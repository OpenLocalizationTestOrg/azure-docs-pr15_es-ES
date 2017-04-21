<properties
   pageTitle="Administración de simultaneidad y la carga de trabajo en el almacén de datos de SQL | Microsoft Azure"
   description="Comprender la gestión de simultaneidad y la carga de trabajo en el almacén de datos de SQL Azure para desarrollar soluciones."
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
   ms.date="09/27/2016"
   ms.author="sonyama;barbkess;jrj"/>

# <a name="concurrency-and-workload-management-in-sql-data-warehouse"></a>Administración de simultaneidad y la carga de trabajo en el almacén de datos de SQL

Para ofrecer performance predecible en escala, almacenamiento de datos de SQL de Microsoft Azure le ayuda a controlar los niveles de simultaneidad y asignaciones de recursos como memoria y establecimiento de prioridades de CPU. Este artículo presenta los conceptos de administración de simultaneidad y la carga de trabajo, que explica cómo ambas características se han implementado y cómo puede controlar en el almacén de datos. Administración de carga de trabajo del almacén de datos SQL está pensado para ayudarle a dar soporte a entornos de varios usuarios. No está pensado para inquilinos múltiples cargas de trabajo.

## <a name="concurrency-limits"></a>Límites de simultaneidad

Almacén de datos SQL permite hasta 1.024 conexiones simultáneas. Todas las 1.024 conexiones pueden enviar consultas al mismo tiempo. Sin embargo, para optimizar el rendimiento, almacén de datos SQL puede cola algunas consultas para garantizar que cada consulta recibe una concesión de memoria mínima. Cola se produce en tiempo de ejecución de consulta. Cola de consultas cuando se alcance el límite de simultaneidad, almacén de datos SQL puede aumentar rendimiento total al garantizar que active consultas obtienen acceso a los recursos de memoria muy necesaria.  

Límites de simultaneidad se rigen por dos conceptos: *consultas simultáneas* y *ranuras de simultaneidad*. Para que ejecute una consulta, debe ejecutar dentro del límite de simultaneidad de consulta y la asignación de franja de simultaneidad.

- Consultas simultáneas son la ejecución al mismo tiempo. Almacén de datos SQL admite hasta 32 consultas simultáneas en los tamaños DWU más grandes.
- Se asignan ranuras de simultaneidad basado en DWU. Cada DWU 100 proporciona 4 posiciones de simultaneidad. Por ejemplo, una DW100 asigna 4 posiciones de simultaneidad y DW1000 asigna 40. Cada consulta consume uno o más simultaneidad ranuras, depende de la [clase de recursos](#resource-classes) de la consulta. Consultas que se ejecuta en la clase de recurso smallrc consuman una franja de simultaneidad. Consultas que se ejecuta en una clase de recursos superior consuman más intervalos de simultaneidad.

La siguiente tabla describe los límites de consultas simultáneas y ranuras de simultaneidad en los distintos tamaños DWU.

### <a name="concurrency-limits"></a>Límites de simultaneidad

|  DWU   | Consultas simultáneas de Max  | Ranuras de simultaneidad asignadas |
| :----  | :---------------------: | :-------------------------: |
| DW100  |            4            |                4            |
| DW200  |            8            |                8            |
| DW300  |           12            |               12            |
| DW400  |           16            |               16            |
| DW500  |           20            |               20            |
| DW600  |           24            |               24            |
| DW1000 |           32            |               40            |
| DW1200 |           32            |               48            |
| DW1500 |           32            |               60            |
| DW2000 |           32            |               80            |
| DW3000 |           32            |              120            |
| DW6000 |           32            |              240            |

Cuando se cumple cualquiera de estos umbrales, las consultas nuevas están en la cola y se ejecuta en base, primero en salir.  Como un consultas finalice y el número de consultas y ranuras está por debajo de los límites, se publican en cola de consultas. 

> [AZURE.NOTE]  Consultas *Seleccione* ejecutar exclusivamente en vistas de administración dinámica (DMV) o catálogo no se rigen por cualquiera de los límites de simultaneidad. Puede supervisar el sistema independientemente del número de consultas de ejecutar en él.

## <a name="resource-classes"></a>Clases de recursos

Recursos clases permiten que controlar la asignación de memoria y ciclos de CPU dado a una consulta. Puede asignar cuatro clases de recursos a un usuario en el formulario de *funciones de base de datos*. Las clases de cuatro recursos son **smallrc**, **mediumrc**, **largerc**y **xlargerc**. Los usuarios de smallrc reciben una menor cantidad de memoria y pueden aprovechar las ventajas de simultaneidad superior. En cambio, los usuarios asignados a xlargerc reciben grandes cantidades de memoria y, por tanto, menos de sus consultas pueden ejecutar simultáneamente.

De forma predeterminada, cada usuario es miembro de la clase de recursos pequeños, smallrc. El procedimiento `sp_addrolemember` se usa para aumentar la clase de recursos y `sp_droprolemember` se usa para reducir la clase de recursos. Por ejemplo, este comando aumentaría la clase de recursos de loaduser a largerc:

```sql
EXEC sp_addrolemember 'largerc', 'loaduser'
```

Es una buena práctica permanentemente asignar usuarios a una clase de recursos, en lugar de cambiar sus clases de recursos. Por ejemplo, cargas de tablas agrupadas columnstore crear índices de mayor calidad al asignar más memoria. Para garantizar que las cargas tienen acceso a una memoria superior, cree un usuario específicamente para cargar los datos y asignar permanentemente este usuario a una clase de recursos superior.

Hay algunos tipos de consultas que no beneficiarse de una asignación de memoria más grande. El sistema omitirá su asignación de recursos de la clase y siempre ejecutar estas consultas en la clase de recursos pequeños. Si estas consultas siempre se ejecutan en la clase de recursos pequeños, pueden ejecutar cuando ranuras de simultaneidad son presión y no consumen más intervalos que sea necesario. Para obtener más información, vea [excepciones de clase de recursos](#query-exceptions-to-concurrency-limits) .

Unos obtener más detalles sobre la clase de recurso:

- Permiso *ALTER rol* es necesario cambiar la clase de recursos de un usuario.  
- Aunque puede agregar un usuario a uno o más de las clases de recursos superior, los usuarios tendrá los atributos de la clase recursos superiores a los que están asignados. Es decir, si un usuario se asigna a mediumrc y largerc, la clase de recursos superior (largerc) es la clase de recursos que se aceptará.  
- No se puede cambiar la clase de recurso del usuario de administración del sistema.

Para obtener un ejemplo detallado, vea [ejemplo de clase de recursos de usuario de cambiar](#changing-user-resource-class-example).

## <a name="memory-allocation"></a>Asignación de memoria

Hay ventajas y desventajas para aumentar la clase de recurso de un usuario. Aumenta una clase de recurso para un usuario tendrá acceso de sus consultas a más memoria, lo cual puede implicar consultas se ejecutan más rápido.  Sin embargo, las clases de recursos superior también reducen el número de consultas simultáneas que se pueden ejecutar. Esto es el equilibrio entre asignar grandes cantidades de memoria para una sola consulta o permitir que otras consultas, lo que necesitan también las asignaciones de memoria, ejecutar al mismo tiempo. Si un usuario se expresa altos asignaciones de memoria de una consulta, otros usuarios no tendrán acceso a esa misma memoria para ejecutar una consulta.

La siguiente tabla asigna la memoria asignada a cada distribución DWU y recursos de la clase.

### <a name="memory-allocations-per-distribution-mb"></a>Asignaciones de memoria por distribución (MB)

|  DWU   | smallrc | mediumrc | largerc | xlargerc |
| :----- | :-----: | :------: | :-----: | :------: |
| DW100  |   100   |    100   |    200  |    400   |
| DW200  |   100   |    200   |    400  |    800   |
| DW300  |   100   |    200   |    400  |    800   |
| DW400  |   100   |    400   |    800  |  1.600   |
| DW500  |   100   |    400   |    800  |  1.600   |
| DW600  |   100   |    400   |    800  |  1.600   |
| DW1000 |   100   |    800   |  1.600  |  3.200   |
| DW1200 |   100   |    800   |  1.600  |  3.200   |
| DW1500 |   100   |    800   |  1.600  |  3.200   |
| DW2000 |   100   |  1.600   |  3.200  |  6.400   |
| DW3000 |   100   |  1.600   |  3.200  |  6.400   |
| DW6000 |   100   |  3.200   |  6.400  |  12.800  |

En la tabla anterior, puede ver que una consulta que se ejecuta en un DW2000 en la clase de recurso xlargerc tuviera acceso 6.400 MB de memoria en cada una de las bases de datos distribuidas 60.  En el almacén de datos de SQL, hay 60 distribuciones. Por lo tanto, para calcular la asignación de memoria total de una consulta en una clase de recurso determinado, los valores anteriores deben ser multiplicados por 60.

### <a name="memory-allocations-system-wide-gb"></a>Todo el sistema memoria asignaciones (GB)

|  DWU   | smallrc | mediumrc | largerc | xlargerc |
| :----- | :-----: | :------: | :-----: | :------: |
| DW100  |    6    |    6     |    12   |    23    |
| DW200  |    6    |    12    |    23   |    47    |
| DW300  |    6    |    12    |    23   |    47    |
| DW400  |    6    |    23    |    47   |    94    |
| DW500  |    6    |    23    |    47   |    94    |
| DW600  |    6    |    23    |    47   |    94    |
| DW1000 |    6    |    47    |    94   |   188    |
| DW1200 |    6    |    47    |    94   |   188    |
| DW1500 |    6    |    47    |    94   |   188    |
| DW2000 |    6    |    94    |   188   |   375    |
| DW3000 |    6    |    94    |   188   |   375    |
| DW6000 |    6    |   188    |   375   |   750    |

En esta tabla de asignaciones de memoria de todo el sistema, puede ver que una consulta que se ejecuta en un DW2000 en la clase de recurso xlargerc está asignada a un total de 375 GB de memoria (MB * 60 6.400 distribuciones / 1.024 para convertir en GB) sobre la totalidad de su almacén de datos de SQL.

## <a name="concurrency-slot-consumption"></a>Consumo de franja de simultaneidad

Almacén de datos SQL concede más memoria a las consultas que se ejecuta en clases de recursos superior. Memoria es un recurso fijo.  Por lo tanto, pueden ejecutar la memoria más asignada por consulta, las consultas simultáneas menos. La siguiente tabla Reitere todos los conceptos anterior en una sola vista que muestra el número de espacios de simultaneidad disponibles por DWU y las ranuras consumidas por cada clase de recursos.

### <a name="allocation-and-consumption-of-concurrency-slots"></a>Asignación y el consumo de ranuras de simultaneidad

|  DWU   | Número máximo de consultas simultáneo  | Ranuras de simultaneidad asignadas | Ranuras usados por smallrc |  Ranuras usados por mediumrc |  Ranuras usados por largerc |  Ranuras usados por xlargerc |
| :----  | :---------------------: | :-------------------------: | :-----: | :------: | :-----: | :------: |
| DW100  |            4            |                4            |    1    |     1    |    2    |    4     |
| DW200  |            8            |                8            |    1    |     2    |    4    |    8     |
| DW300  |           12            |               12            |    1    |     2    |    4    |    8     |
| DW400  |           16            |               16            |    1    |     4    |    8    |   16     |
| DW500  |           20            |               20            |    1    |     4    |    8    |   16     |
| DW600  |           24            |               24            |    1    |     4    |    8    |   16     |
| DW1000 |           32            |               40            |    1    |     8    |   16    |   32     |
| DW1200 |           32            |               48            |    1    |     8    |   16    |   32     |
| DW1500 |           32            |               60            |    1    |     8    |   16    |   32     |
| DW2000 |           32            |               80            |    1    |    16    |   32    |   64     |
| DW3000 |           32            |              120            |    1    |    16    |   32    |   64     |
| DW6000 |           32            |              240            |    1    |    32    |   64    |  128     |


En esta tabla, puede ver esa ejecución de almacén de datos de SQL DW1000 asigna un máximo de 32 consultas simultáneas y un total de 40 ranuras de simultaneidad. Si todos los usuarios se están ejecutando en smallrc, se podrán 32 consultas simultáneas porque cada consulta ocupan 1 franja de simultaneidad. Si todos los usuarios de un DW1000 se ejecutan en mediumrc, cada consulta se asignarán 800 MB por distribución para una asignación de memoria total de 47 GB por consulta y simultaneidad que se limita a los usuarios de 5 (40 ranuras de simultaneidad 8 se acopla por usuario mediumrc).

## <a name="query-importance"></a>Importancia de consulta

Almacén de datos SQL implementa las clases de recursos mediante grupos de carga de trabajo. Hay un total de ocho grupos de carga de trabajo que controlan el comportamiento de las clases de recursos entre los distintos tamaños DWU. Para cualquier DWU almacén de datos de SQL utiliza solo cuatro de los grupos de carga de trabajo de ocho. Esto tiene sentido porque cada grupo de carga de trabajo se asigna a uno de los cuatro clases de recursos: smallrc, mediumrc, largerc, o xlargerc. La importancia de comprender los grupos de carga de trabajo es que algunos de estos grupos de carga de trabajo se configuran mayor *importancia*. Importancia se usa para CPU programación. Consultas ejecutar con importancia alta recibirá tres veces más ciclos de CPU que aquellos con importancia Media. Por lo tanto, las asignaciones de franja de simultaneidad también determinan prioridad de CPU. Cuando una consulta consume ranuras 16 o más, se ejecuta como de importancia alta.

La siguiente tabla muestra las asignaciones de importancia de cada grupo de carga de trabajo.

### <a name="workload-group-mappings-to-concurrency-slots-and-importance"></a>Asignaciones de grupo de carga de trabajo a ranuras de simultaneidad e importancia

| Grupos de carga de trabajo | Asignación de franja de simultaneidad | MB o distribución. | Asignación de importancia |
| :-------------- | :----------------------: | :---------------: | :----------------- |
| SloDWGroupC00   |            1             |         100       |       Medio       |
| SloDWGroupC01   |            2             |         200       |       Medio       |
| SloDWGroupC02   |            4             |         400       |       Medio       |
| SloDWGroupC03   |            8             |         800       |       Medio       |
| SloDWGroupC04   |           16             |       1.600       |       Alta         |
| SloDWGroupC05   |           32             |       3.200       |       Alta         |
| SloDWGroupC06   |           64             |       6.400       |       Alta         |
| SloDWGroupC07   |          128             |      12.800       |       Alta         |

En el gráfico de la **asignación y el consumo de ranuras de simultaneidad** , puede ver que un DW500 utiliza 1, 4, 8 o ranuras de simultaneidad 16 para smallrc, mediumrc, largerc y xlargerc, respectivamente. Puede buscar esos valores en el gráfico anterior para buscar la importancia de cada clase de recurso.

### <a name="dw500-mapping-of-resource-classes-to-importance"></a>Asignación de DW500 de clases de recursos para importancia

| Clase de recursos | Grupo de carga de trabajo | Ranuras de simultaneidad usados | MB o distribución. | Importancia |
| :------------- | :------------- | :--------------------: | :---------------: | :--------- |
| smallrc        | SloDWGroupC00  |           1            |         100       |   Medio   |
| mediumrc       | SloDWGroupC02  |           4            |         400       |   Medio   |
| largerc        | SloDWGroupC03  |           8            |         800       |   Medio   |
| xlargerc       | SloDWGroupC04  |          16            |        1.600      |   Alta     |


Puede usar la siguiente consulta DMV para ver las diferencias entre la asignación de recursos de memoria en detalle desde la perspectiva del Administrador de recursos, o para analizar histórico y active uso de los grupos de carga de trabajo para solucionar el problema.

```sql
WITH rg
AS
(   SELECT  
     pn.name                        AS node_name
    ,pn.[type]                      AS node_type
    ,pn.pdw_node_id                 AS node_id
    ,rp.name                        AS pool_name
    ,rp.max_memory_kb*1.0/1024              AS pool_max_mem_MB
    ,wg.name                        AS group_name
    ,wg.importance                  AS group_importance
    ,wg.request_max_memory_grant_percent        AS group_request_max_memory_grant_pcnt
    ,wg.max_dop                     AS group_max_dop
    ,wg.effective_max_dop               AS group_effective_max_dop
    ,wg.total_request_count             AS group_total_request_count
    ,wg.total_queued_request_count          AS group_total_queued_request_count
    ,wg.active_request_count                AS group_active_request_count
    ,wg.queued_request_count                AS group_queued_request_count
    FROM    sys.dm_pdw_nodes_resource_governor_workload_groups wg
    JOIN    sys.dm_pdw_nodes_resource_governor_resource_pools rp    
            ON  wg.pdw_node_id  = rp.pdw_node_id
            AND wg.pool_id      = rp.pool_id
    JOIN    sys.dm_pdw_nodes pn
            ON  wg.pdw_node_id  = pn.pdw_node_id
    WHERE   wg.name like 'SloDWGroup%'
        AND     rp.name = 'SloDWPool'
)
SELECT  pool_name
,       pool_max_mem_MB
,       group_name
,       group_importance
,       (pool_max_mem_MB/100)*group_request_max_memory_grant_pcnt AS max_memory_grant_MB
,       node_name
,       node_type
,       group_total_request_count
,       group_total_queued_request_count
,       group_active_request_count
,       group_queued_request_count
FROM    rg
ORDER BY
    node_name
,   group_request_max_memory_grant_pcnt
,   group_importance
;
```

## <a name="queries-that-honor-concurrency-limits"></a>Consultas que respetan los límites de simultaneidad

La mayoría de las consultas se rigen por las clases de recursos. Estas consultas deben caber en ambos el simultáneas consulta y simultaneidad franja umbrales. Un usuario no puede elegir excluir una consulta desde el modelo de franja de simultaneidad.

En resumen, las siguientes afirmaciones respetan las clases de recursos:

- SELECCIONE INSERTAR
- ACTUALIZACIÓN
- ELIMINAR
- Seleccionar (cuando consultar tablas de usuario)
- ALTER REGENERACIÓN DE ÍNDICE
- MODIFICAR ÍNDICE REORGANIZAR
- VOLVER A GENERAR TABLA ALTER
- CREAR ÍNDICE
- CREAR ÍNDICE AGRUPADO COLUMNSTORE
- CREAR TABLA AS SELECT (CTAS)
- Carga de datos
- Operaciones de movimiento de datos realizadas por el servicio de movimiento de datos (DMS)

## <a name="query-exceptions-to-concurrency-limits"></a>Excepciones de consulta a los límites de simultaneidad

Algunas consultas no sirven para la clase de recurso al que está asignado el usuario. Estas excepciones a los límites de simultaneidad se realizan cuando los recursos de memoria necesarios para un comando determinado suelen bajos, como el comando es una operación de metadatos. El objetivo de estas excepciones es evitar las asignaciones de memoria mayores para las consultas que nunca será necesario. En estos casos, la clase de recursos pequeños predeterminada (smallrc) se usa siempre independientemente de la clase de recurso reales que se asignan al usuario. Por ejemplo, `CREATE LOGIN` siempre se ejecutará en smallrc. Los recursos necesarios para realizar esta operación están muy bajos, por lo que no tiene sentido para incluir la consulta en el modelo de franja de simultaneidad.  Estas consultas no también están limitadas por el límite de simultaneidad del 32 usuario, puede ejecutar una cantidad ilimitada de estas consultas al límite de la sesión de 1.024 sesiones.

Las siguientes afirmaciones no respetan las clases de recursos:

- CREAR o quitar tabla
- MODIFICAR TABLA... CAMBIAR, dividir o combinar partición
- ALTER INDEX DISABLE
- QUITAR EL ÍNDICE
- CREAR, actualizar o quitar estadísticas
- TRUNCAR LA TABLA
- MODIFICAR AUTORIZACIÓN
- CREAR INICIO DE SESIÓN
- CREAR, modificar o quitar usuario
- CREAR, modificar o DROP PROCEDURE
- CREAR o quitar vista
- INSERTAR VALORES
- Seleccionar vistas del sistema y DMV
- EXPLIQUE
- DBCC

<!--
Removed as these two are not confirmed / supported under SQLDW
- CREATE REMOTE TABLE AS SELECT
- CREATE EXTERNAL TABLE AS SELECT
- REDISTRIBUTE
-->

## <a name="change-a-user-resource-class-example"></a>Cambiar un ejemplo de clase de recursos de usuario

1. **Crear inicio de sesión:** Abra una conexión a **la base de datos en el host de la base de datos de almacén de datos SQL de SQL server** y ejecute los comandos siguientes.

    ```sql
    CREATE LOGIN newperson WITH PASSWORD = 'mypassword';
    CREATE USER newperson for LOGIN newperson;
    ```

    > [AZURE.NOTE] Es una buena idea crear un usuario en la base de datos para los usuarios del almacén de datos de SQL Azure. Crear un usuario en el patrón permite a un usuario iniciar sesión utilizando herramientas como SSMS sin especificar un nombre de base de datos.  También les permite utilizar el Explorador de objetos para ver todas las bases de datos en un servidor de SQL server.  Para obtener más detalles sobre la creación y administración de usuarios, vea [proteger una base de datos en el almacén de datos de SQL][].

2. **Usuario de crear un almacén de datos de SQL:** Abra una conexión a la base de datos de **Almacén de datos SQL** y ejecute el siguiente comando.

    ```sql
    CREATE USER newperson FOR LOGIN newperson;
    ```

3. **Conceder permisos:** En el ejemplo siguiente se concede `CONTROL` en la base de datos de **Almacén de datos de SQL** . `CONTROL`en la base de datos nivel es equivalente a db_owner en SQL Server.

    ```sql
    GRANT CONTROL ON DATABASE::MySQLDW to newperson;
    ```

4. **Aumentar la clase de recursos:** Use la consulta siguiente para agregar un usuario a un rol de administración de carga de trabajo superior.

    ```sql
    EXEC sp_addrolemember 'largerc', 'newperson'
    ```

5. **Disminuir la clase de recursos:** Use la siguiente consulta para quitar un usuario de una función de administración de carga de trabajo.

    ```sql
    EXEC sp_droprolemember 'largerc', 'newperson';
    ```

    > [AZURE.NOTE] No es posible quitar un usuario de smallrc.

## <a name="queued-query-detection-and-other-dmvs"></a>Detección de consulta en cola y otros DMV

Puede usar el `sys.dm_pdw_exec_requests` DMV para identificar las consultas que se esperan en una cola de simultaneidad. Consultas esperando una franja de simultaneidad tendrá un estado de **suspensión**.

```sql
SELECT   r.[request_id]              AS Request_ID
        ,r.[status]              AS Request_Status
        ,r.[submit_time]             AS Request_SubmitTime
        ,r.[start_time]              AS Request_StartTime
        ,DATEDIFF(ms,[submit_time],[start_time]) AS Request_InitiateDuration_ms
        ,r.resource_class                         AS Request_resource_class
FROM    sys.dm_pdw_exec_requests r;
```

Funciones de administración de carga de trabajo pueden verse con `sys.database_principals`.

```sql
SELECT  ro.[name]           AS [db_role_name]
FROM    sys.database_principals ro
WHERE   ro.[type_desc]      = 'DATABASE_ROLE'
AND     ro.[is_fixed_role]  = 0;
```

La consulta siguiente muestra qué rol de cada usuario está asignado a.

```sql
SELECT   r.name AS role_principal_name
        ,m.name AS member_principal_name
FROM    sys.database_role_members rm
JOIN    sys.database_principals AS r            ON rm.role_principal_id     = r.principal_id
JOIN    sys.database_principals AS m            ON rm.member_principal_id   = m.principal_id
WHERE   r.name IN ('mediumrc','largerc', 'xlargerc');
```

Almacén de datos de SQL tiene los siguientes tipos de espera:

- **LocalQueriesConcurrencyResourceType**: las consultas que se encuentre fuera de la estructura de la franja de simultaneidad. Consultas DMV y sistema funciona como `SELECT @@VERSION` son ejemplos de consultas locales.
- **UserConcurrencyResourceType**: las consultas que se encuentran dentro del marco de la franja de simultaneidad. Consultas en tablas de usuario final representan ejemplos en los que debería usar este tipo de recurso.
- **DmsConcurrencyResourceType**: espera resultantes de operaciones de movimiento de datos.
- **BackupConcurrencyResourceType**: esta espera indica que una base de datos que se copia. El valor máximo de este tipo de recurso es 1. Si se han solicitado varias copias de seguridad al mismo tiempo, los demás permanecerán en cola.

La `sys.dm_pdw_waits` DMV puede usarse para ver qué está esperando una solicitud de recursos.

```sql
SELECT  w.[wait_id]
,       w.[session_id]
,       w.[type]            AS Wait_type
,       w.[object_type]
,       w.[object_name]
,       w.[request_id]
,       w.[request_time]
,       w.[acquire_time]
,       w.[state]
,       w.[priority]
,   SESSION_ID()            AS Current_session
,   s.[status]          AS Session_status
,   s.[login_name]
,   s.[query_count]
,   s.[client_id]
,   s.[sql_spid]
,   r.[command]         AS Request_command
,   r.[label]
,   r.[status]          AS Request_status
,   r.[submit_time]
,   r.[start_time]
,   r.[end_compile_time]
,   r.[end_time]
,   DATEDIFF(ms,r.[submit_time],r.[start_time])     AS Request_queue_time_ms
,   DATEDIFF(ms,r.[start_time],r.[end_compile_time])    AS Request_compile_time_ms
,   DATEDIFF(ms,r.[end_compile_time],r.[end_time])      AS Request_execution_time_ms
,   r.[total_elapsed_time]
FROM    sys.dm_pdw_waits w
JOIN    sys.dm_pdw_exec_sessions s  ON w.[session_id] = s.[session_id]
JOIN    sys.dm_pdw_exec_requests r  ON w.[request_id] = r.[request_id]
WHERE   w.[session_id] <> SESSION_ID();
```

La `sys.dm_pdw_resource_waits` DMV muestra solo el espera de recursos consumido por una consulta dada. Tiempo de espera de recursos solo mide el tiempo de espera para que los recursos que se debe proporcionar, a diferencia de tiempo de espera de señal, que es el tiempo que tardan los servidores subyacentes SQL Server programar la consulta en la CPU.

```sql
SELECT  [session_id]
,       [type]
,       [object_type]
,       [object_name]
,       [request_id]
,       [request_time]
,       [acquire_time]
,       DATEDIFF(ms,[request_time],[acquire_time])  AS acquire_duration_ms
,       [concurrency_slots_used]                    AS concurrency_slots_reserved
,       [resource_class]
,       [wait_id]                                   AS queue_position
FROM    sys.dm_pdw_resource_waits
WHERE   [session_id] <> SESSION_ID();
```

La `sys.dm_pdw_wait_stats` DMV puede utilizarse para el análisis de tendencias históricas de espera.

```sql
SELECT  w.[pdw_node_id]
,       w.[wait_name]
,       w.[max_wait_time]
,       w.[request_count]
,       w.[signal_time]
,       w.[completed_count]
,       w.[wait_time]
FROM    sys.dm_pdw_wait_stats w;
```

## <a name="next-steps"></a>Pasos siguientes

Para obtener más información sobre la administración de seguridad y los usuarios de la base de datos, vea [proteger una base de datos en el almacén de datos de SQL][]. Para obtener más información acerca de cómo mayor recurso clases puede mejorar la calidad de índice columnstore agrupadas, consulte [creación de nuevos índices para mejorar la calidad de segmento].

<!--Image references-->

<!--Article references-->
[Proteger una base de datos en el almacén de datos de SQL]: ./sql-data-warehouse-overview-manage-security.md
[Reconstruir índices para mejorar la calidad del segmento]: ./sql-data-warehouse-tables-index.md#rebuilding-indexes-to-improve-segment-quality
[Proteger una base de datos en el almacén de datos de SQL]: ./sql-data-warehouse-overview-manage-security.md

<!--MSDN references-->
[Managing Databases and Logins in Azure SQL Database]:https://msdn.microsoft.com/library/azure/ee336235.aspx

<!--Other Web references-->
