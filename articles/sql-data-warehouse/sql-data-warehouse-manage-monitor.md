<properties
   pageTitle="Supervisar la carga de trabajo con DMV | Microsoft Azure"
   description="Obtenga información sobre cómo supervisar la carga de trabajo con DMV."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="sonyam"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="10/08/2016"
   ms.author="sonyama;barbkess"/>

# <a name="monitor-your-workload-using-dmvs"></a>Supervisar la carga de trabajo con DMV

Este artículo describe cómo usar las vistas de administración dinámicas (DMV) para supervisar la carga de trabajo e investigar la ejecución de la consulta en el almacén de datos de SQL Azure.

## <a name="permissions"></a>Permisos

Para consultar las DMV en este artículo, se necesita permiso de estado de vista de base de datos o CONTROL. Otorgar estado de base de datos de vista suele ser el permiso preferido como es mucho más restrictivo.

```sql
GRANT VIEW DATABASE STATE TO myuser;
```

## <a name="monitor-connections"></a>Supervisar conexiones

Todos los inicios de sesión al almacén de datos de SQL se registran en [sys.dm_pdw_exec_sessions][].  Esta DMV contiene los últimos 10.000 inicios de sesión.  La session_id es la clave principal y se asignan secuencialmente para cada nuevo inicio de sesión.

```sql
-- Other Active Connections
SELECT * FROM sys.dm_pdw_exec_sessions where status <> 'Closed' and session_id <> session_id();
```

## <a name="monitor-query-execution"></a>Ejecución de la consulta de Monitor

Todas las consultas que se ejecutan en el almacén de datos de SQL se registran en [sys.dm_pdw_exec_requests][].  Esta DMV contiene las últimas 10.000 consultas ejecutadas.  La request_id unívocamente identifica cada consulta y es la clave principal para esta DMV.  La request_id se asigna secuencialmente para cada nueva consulta y un prefijo de QID, lo que significa de Id.  Consultar esta DMV para una determinada session_id muestra todas las consultas para un inicio de sesión dado.

>[AZURE.NOTE] Procedimientos almacenados usan varios Solicitar Id.  Identificadores de solicitud se asignan en orden secuencial. 

Estos son los pasos a seguir para investigar planes de ejecución de consulta y las horas de una consulta determinada.

### <a name="step-1-identify-the-query-you-wish-to-investigate"></a>PASO 1: Identificar la consulta que desea investigar

```sql
-- Monitor active queries
SELECT * 
FROM sys.dm_pdw_exec_requests 
WHERE status not in ('Completed','Failed','Cancelled')
  AND session_id <> session_id()
ORDER BY submit_time DESC;

-- Find top 10 queries longest running queries
SELECT TOP 10 * 
FROM sys.dm_pdw_exec_requests 
ORDER BY total_elapsed_time DESC;

-- Find a query with the Label 'My Query'
-- Use brackets when querying the label column, as it it a key word
SELECT  *
FROM    sys.dm_pdw_exec_requests
WHERE   [label] = 'My Query';
```

Desde los resultados de la consulta anterior, **Nota El identificador de solicitud** de la consulta que desea investigar.

Se ponen en cola las consultas en el estado **suspendido** debido a los límites de simultaneidad. Estas consultas también aparecerán en la consulta de espera sys.dm_pdw_waits con un tipo de UserConcurrencyResourceType. Consulte [administración de simultaneidad y la carga de trabajo][] para obtener más detalles sobre los límites de simultaneidad. También pueden esperar consultas por otras razones como bloqueos del objeto.  Si la consulta está en espera para un recurso, vea [consultas Investigating esperando recursos][] más adelante en este artículo.

Para simplificar la búsqueda de una consulta en la tabla sys.dm_pdw_exec_requests, use la [etiqueta][] para asignar un comentario a la consulta que se puede ver en la vista sys.dm_pdw_exec_requests.

```sql
-- Query with Label
SELECT *
FROM sys.tables
OPTION (LABEL = 'My Query')
;
```

### <a name="step-2-investigate-the-query-plan"></a>PASO 2: Investigar el plan de consulta

Utilice el identificador de solicitud para recuperar el plan de la consulta distribuida SQL (DSQL) de [sys.dm_pdw_request_steps][].

```sql
-- Find the distributed query plan steps for a specific query.
-- Replace request_id with value from Step 1.

SELECT * FROM sys.dm_pdw_request_steps
WHERE request_id = 'QID####'
ORDER BY step_index;
```

Cuando un plan DSQL tarda más de lo esperado, puede deberse a un plan de complejo con muchos de los pasos DSQL o un solo paso tarda mucho tiempo.  Si varios pasos con varias operaciones de mover el plan, considere la posibilidad de optimizar su distribuciones de tabla para reducir el movimiento de datos. [Distribución de la tabla][] se explica por qué datos deben moverse a resolver una consulta y explican algunas estrategias de distribución para reducir el movimiento de datos.

Para investigar más detalles acerca de un solo paso, la columna *operation_type* del paso de consulta de ejecución larga y observe el **Índice de paso**:

- Continúe con el paso 3a para **operaciones de SQL**: OnOperation, RemoteOperation, ReturnOperation.
- Continúe con el paso 3b para **operaciones de movimiento de datos**: ShuffleMoveOperation, BroadcastMoveOperation, TrimMoveOperation, PartitionMoveOperation, MoveOperation, CopyOperation.

### <a name="step-3a-investigate-sql-on-the-distributed-databases"></a>PASO 3a: investigar SQL en las bases de datos distribuidos

Use el identificador de la solicitud y el índice de paso para recuperar los detalles de [sys.dm_pdw_sql_requests][], que contiene información de la ejecución del paso de consulta en todas las bases de datos distribuidas.

```sql
-- Find the distribution run times for a SQL step.
-- Replace request_id and step_index with values from Step 1 and 3.

SELECT * FROM sys.dm_pdw_sql_requests
WHERE request_id = 'QID####' AND step_index = 2;
```

Cuando se ejecuta el paso de consulta, [DBCC PDW_SHOWEXECUTIONPLAN][] puede utilizarse para recuperar el plan de SQL Server estimado de la caché del plan de SQL Server para el paso que se ejecuta en una distribución específica.

```sql
-- Find the SQL Server execution plan for a query running on a specific SQL Data Warehouse Compute or Control node.
-- Replace distribution_id and spid with values from previous query.

DBCC PDW_SHOWEXECUTIONPLAN(1, 78);
```

### <a name="step-3b-investigate-data-movement-on-the-distributed-databases"></a>PASO 3b: investigar el movimiento de datos en las bases de datos distribuidos

Usar el identificador de la solicitud y el índice de paso para recuperar información sobre un paso de movimiento de datos que ejecutan en cada distribución desde [sys.dm_pdw_dms_workers][].

```sql
-- Find the information about all the workers completing a Data Movement Step.
-- Replace request_id and step_index with values from Step 1 and 3.

SELECT * FROM sys.dm_pdw_dms_workers
WHERE request_id = 'QID####' AND step_index = 2;
```

- Compruebe la columna *total_elapsed_time* para ver si una distribución específica tarda mucho más que otros para movimiento de datos.
- Para la distribución de larga ejecución, consulte la columna de *rows_processed* para ver si el número de filas que se mueve de la distribución es significativamente mayor que otros usuarios. Si es así, esto puede indicar sesgo de los datos subyacentes.

Si está ejecutando la consulta, [DBCC PDW_SHOWEXECUTIONPLAN][] puede utilizarse para recuperar el plan de SQL Server estimado de la caché del plan de SQL Server para el paso de SQL que se está ejecutando dentro de una distribución específica.

```sql
-- Find the SQL Server estimated plan for a query running on a specific SQL Data Warehouse Compute or Control node.
-- Replace distribution_id and spid with values from previous query.

DBCC PDW_SHOWEXECUTIONPLAN(55, 238);
```

<a name="waiting"></a>
## <a name="monitor-waiting-queries"></a>Supervisar las consultas de espera

Si descubre que la consulta no progresa porque está esperando para un recurso, aquí tiene una consulta que muestra todos los recursos que está esperando una consulta.

```sql
-- Find queries 
-- Replace request_id with value from Step 1.

SELECT waits.session_id,
      waits.request_id,  
      requests.command,
      requests.status,
      requests.start_time,  
      waits.type,
      waits.state,
      waits.object_type,
      waits.object_name
FROM   sys.dm_pdw_waits waits
   JOIN  sys.dm_pdw_exec_requests requests
   ON waits.request_id=requests.request_id
WHERE waits.request_id = 'QID####'
ORDER BY waits.object_name, waits.object_type, waits.state;
```

Si la consulta está en espera activa en los recursos desde otra consulta, el estado será **AcquireResources**.  Si la consulta contiene todos los recursos necesarios, el estado será **concedido**.

## <a name="next-steps"></a>Pasos siguientes
Consulte [vistas del sistema][] para obtener más información sobre DMV.
Consulte [prácticas recomendadas de almacén de datos de SQL][] para obtener más información acerca de prácticas recomendadas

<!--Image references-->

<!--Article references-->
[Manage overview]: ./sql-data-warehouse-overview-manage.md
[Prácticas recomendadas de almacén de datos SQL]: ./sql-data-warehouse-best-practices.md
[Vistas del sistema]: ./sql-data-warehouse-reference-tsql-system-views.md
[Distribución de tablas]: ./sql-data-warehouse-tables-distribute.md
[Administración de simultaneidad y la carga de trabajo]: ./sql-data-warehouse-develop-concurrency.md
[Investigar consultas esperando recursos]: ./sql-data-warehouse-manage-monitor.md#waiting

<!--MSDN references-->
[Sys.dm_pdw_dms_workers]: http://msdn.microsoft.com/library/mt203878.aspx
[Sys.dm_pdw_exec_requests]: http://msdn.microsoft.com/library/mt203887.aspx
[Sys.dm_pdw_exec_sessions]: http://msdn.microsoft.com/library/mt203883.aspx
[Sys.dm_pdw_request_steps]: http://msdn.microsoft.com/library/mt203913.aspx
[Sys.dm_pdw_sql_requests]: http://msdn.microsoft.com/library/mt203889.aspx
[DBCC PDW_SHOWEXECUTIONPLAN]: http://msdn.microsoft.com/library/mt204017.aspx
[DBCC PDW_SHOWSPACEUSED]: http://msdn.microsoft.com/library/mt204028.aspx
[ETIQUETA]: https://msdn.microsoft.com/library/ms190322.aspx
