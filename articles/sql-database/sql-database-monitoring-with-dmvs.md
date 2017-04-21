<properties
   pageTitle="Supervisión con vistas dinámicas de administración de base de datos SQL Azure | Microsoft Azure"
   description="Aprenda a detectar y diagnosticar problemas comunes de rendimiento mediante el uso de vistas dinámicas de administración para supervisar la base de datos de SQL de Microsoft Azure."
   services="sql-database"
   documentationCenter=""
   authors="CarlRabeler"
   manager="jhubbard"
   editor=""
   tags=""/>

<tags
   ms.service="sql-database"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="data-management"
   ms.date="09/20/2016"
   ms.author="carlrab"/>

# <a name="monitoring-azure-sql-database-using-dynamic-management-views"></a>Supervisión con vistas dinámicas de administración de base de datos SQL Azure

Base de datos de SQL de Microsoft Azure permite un subconjunto de vistas dinámicas de administración para diagnosticar problemas de rendimiento, que podrían estar causados por bloqueados o de larga ejecución de consultas, botella de recursos, planes de consulta deficiente y así sucesivamente. En este tema se proporciona información sobre cómo detectar problemas comunes de rendimiento mediante el uso de vistas dinámicas de administración.

Base de datos SQL parcialmente admite tres categorías de vistas dinámicas de administración:

- Vistas de administración dinámica relacionados con la base de datos.
- Vistas de administración dinámica relacionada con la ejecución.
- Vistas de administración dinámica relacionados con la transacción.

Para obtener información detallada sobre las vistas dinámicas de administración, vea [vistas de administración dinámicas y funciones (Transact-SQL)](https://msdn.microsoft.com/library/ms188754.aspx) en los libros en pantalla de SQL Server.

## <a name="permissions"></a>Permisos

En la base de datos de SQL, consultar una vista de administración dinámica requiere permisos de **Estado de la base de datos de vista** . El permiso de **Estado de la base de datos de vista** devuelve información acerca de todos los objetos de la base de datos actual.
Para conceder permiso de **Estado de vista de base de datos** a un usuario específico de la base de datos, ejecute la consulta siguiente:

```GRANT VIEW DATABASE STATE TO database_user; ```

En una instancia de la implementación local de SQL Server, vistas dinámicas de administración devuelven información de estado del servidor. En la base de datos SQL devuelven información relacionada con la actual base de datos lógica solo.

## <a name="calculating-database-size"></a>Calcular el tamaño de la base de datos

La siguiente consulta devuelve el tamaño de la base de datos (en megabytes):

```
-- Calculates the size of the database.
SELECT SUM(reserved_page_count)*8.0/1024
FROM sys.dm_db_partition_stats;
GO
```

La siguiente consulta devuelve el tamaño de objetos (en megabytes) de la base de datos:

```
-- Calculates the size of individual database objects.
SELECT sys.objects.name, SUM(reserved_page_count) * 8.0 / 1024
FROM sys.dm_db_partition_stats, sys.objects
WHERE sys.dm_db_partition_stats.object_id = sys.objects.object_id
GROUP BY sys.objects.name;
GO
```

## <a name="monitoring-connections"></a>Conexiones de supervisión

Puede usar la vista de [sys.dm_exec_connections](https://msdn.microsoft.com/library/ms181509.aspx) para recuperar información acerca de las conexiones establecidas para un servidor de base de datos de SQL Azure específico y los detalles de cada conexión. Además, la vista de [sys.dm_exec_sessions](https://msdn.microsoft.com/library/ms176013.aspx) es útil al recuperar información acerca de todas las conexiones de usuario activo y tareas internas.
La siguiente consulta recupera información sobre la conexión actual:

```
SELECT
    c.session_id, c.net_transport, c.encrypt_option,
    c.auth_scheme, s.host_name, s.program_name,
    s.client_interface_name, s.login_name, s.nt_domain,
    s.nt_user_name, s.original_login_name, c.connect_time,
    s.login_time
FROM sys.dm_exec_connections AS c
JOIN sys.dm_exec_sessions AS s
    ON c.session_id = s.session_id
WHERE c.session_id = @@SPID;
```

> [AZURE.NOTE] Al ejecutar la **sys.dm_exec_requests** y **vistas sys.dm_exec_sessions**, si tiene permiso de **Estado de vista de base de datos** en la base de datos, vea ejecutar todas las sesiones en la base de datos; en caso contrario, vea solo la sesión actual.

## <a name="monitoring-query-performance"></a>Supervisar el rendimiento de la consulta

Lento o cuánto tiempo ejecución de consultas puede consumir muchos recursos del sistema. Esta sección muestra cómo usar las vistas dinámicas de administración para detectar algunos problemas comunes de rendimiento de consulta. Una referencia anterior pero aún útil para solucionar este problema, es el artículo de [Solucionar problemas de rendimiento en SQL Server 2008](http://download.microsoft.com/download/D/B/D/DBDE7972-1EB9-470A-BA18-58849DB3EB3B/TShootPerfProbs2008.docx) en Microsoft TechNet.

### <a name="finding-top-n-queries"></a>Buscar consultas top N

En el ejemplo siguiente se devuelve información acerca de las consultas de cinco principales según el tiempo de CPU promedio. Este ejemplo agrega las consultas según su hash de consulta, para que las consultas lógicamente equivalentes se agrupan por su consumo de recursos acumulativa.

```
SELECT TOP 5 query_stats.query_hash AS "Query Hash",
    SUM(query_stats.total_worker_time) / SUM(query_stats.execution_count) AS "Avg CPU Time",
    MIN(query_stats.statement_text) AS "Statement Text"
FROM
    (SELECT QS.*,
    SUBSTRING(ST.text, (QS.statement_start_offset/2) + 1,
    ((CASE statement_end_offset
        WHEN -1 THEN DATALENGTH(ST.text)
        ELSE QS.statement_end_offset END
            - QS.statement_start_offset)/2) + 1) AS statement_text
     FROM sys.dm_exec_query_stats AS QS
     CROSS APPLY sys.dm_exec_sql_text(QS.sql_handle) as ST) as query_stats
GROUP BY query_stats.query_hash
ORDER BY 2 DESC;
```

### <a name="monitoring-blocked-queries"></a>Supervisar las consultas bloqueadas

Las consultas lentas o larga ejecución pueden contribuir a consumo excesivo de los recursos y ser la consecuencia de consultas bloqueadas. Puede ser la causa del bloqueo diseño deficiente de las aplicaciones, planes de consulta incorrecta, la falta de índices útiles y así sucesivamente. Puede usar la vista de sys.dm_tran_locks para obtener información sobre la actividad de bloqueo actual en la base de datos de SQL Azure. Por ejemplo código, consulte [sys.dm_tran_locks (Transact-SQL)](https://msdn.microsoft.com/library/ms190345.aspx) en los libros en pantalla de SQL Server.

### <a name="monitoring-query-plans"></a>Supervisar los planes de consulta

Un plan de consulta ineficaz también puede aumentar el consumo de CPU. En el ejemplo siguiente se utiliza la vista [sys.dm_exec_query_stats](https://msdn.microsoft.com/library/ms189741.aspx) para determinar la consulta que utiliza la CPU más acumulativa.

```
SELECT
    highest_cpu_queries.plan_handle,
    highest_cpu_queries.total_worker_time,
    q.dbid,
    q.objectid,
    q.number,
    q.encrypted,
    q.[text]
FROM
    (SELECT TOP 50
        qs.plan_handle,
        qs.total_worker_time
    FROM
        sys.dm_exec_query_stats qs
    ORDER BY qs.total_worker_time desc) AS highest_cpu_queries
    CROSS APPLY sys.dm_exec_sql_text(plan_handle) AS q
ORDER BY highest_cpu_queries.total_worker_time DESC;
```

## <a name="see-also"></a>Vea también

[Introducción a la base de datos SQL](sql-database-technical-overview.md)
