<properties
   pageTitle="Información general de las tablas en el almacén de datos de SQL | Microsoft Azure"
   description="Introducción a las tablas de almacén de datos de SQL Azure."
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
   ms.date="08/04/2016"
   ms.author="sonyama;barbkess;jrj"/>

# <a name="overview-of-tables-in-sql-data-warehouse"></a>Información general de las tablas en el almacén de datos de SQL

> [AZURE.SELECTOR]
- [Información general][]
- [Tipos de datos][]
- [Distribuir][]
- [Índice][]
- [Partición][]
- [Estadísticas][]
- [Temporal][]

Introducción a la creación de tablas en el almacén de datos de SQL es sencillo.  La sintaxis de [CREATE TABLE][] básica sigue la sintaxis comunes que es más probable que ya conoce de trabajar con otras bases de datos.  Para crear una tabla, basta con el nombre de la tabla, el nombre de las columnas y definir tipos de datos para cada columna.  Si ha crear tablas en otras bases de datos, será muy familiar.

```sql  
CREATE TABLE Customers (FirstName VARCHAR(25), LastName VARCHAR(25))
 ``` 

El ejemplo siguiente crea una tabla denominada a clientes con dos columnas, nombre y apellidos.  Cada columna se define con un tipo de datos de VARCHAR(25), lo que limita los datos de 25 caracteres.  Los siguientes atributos fundamentales de una tabla, así como otras personas, principalmente son los mismos que otras bases de datos.  Tipos de datos definidos para cada columna y garantizan la integridad de los datos.  Pueden agregarse índices para mejorar el rendimiento reduciendo i/OS.  Pueden agregarse particiones para mejorar el rendimiento cuando necesite modificar los datos.

[Cambiar el nombre de] [ RENAME] una tabla de almacén de datos SQL es similar a esta:

```sql  
RENAME OBJECT Customer TO CustomerOrig; 
 ```

## <a name="distributed-tables"></a>Tablas distribuidos

Un nuevo atributo fundamental introducido por sistemas distribuidos como almacén de datos SQL es la **columna de distribución**.  La columna de la distribución es muy eso.  Es la columna que determina cómo distribuir o dividir los datos en segundo plano.  Cuando se crea una tabla sin especificar la columna de la distribución, la tabla se distribuye automáticamente mediante **turnos**.  Turnos tablas pueden ser suficientes en algunos escenarios, definir las columnas de distribución puede reducir en gran medida el movimiento de datos durante las consultas, así optimización del rendimiento.  Consulte [distribuir una tabla][distribuir] para obtener más información sobre cómo seleccionar una columna de distribución.

## <a name="indexing-and-partitioning-tables"></a>Indización y partición de tablas

Mientras se hacen más avanzadas en el almacén de datos SQL y desea optimizar el rendimiento, desea obtener más información sobre el diseño de tabla.  Para obtener más información, consulte los artículos de [Tipos de datos de tabla de][Tipos de datos], [distribuir una tabla][distribuir], [indización de una tabla]y de[índice] de [una tabla de partición][partición].

## <a name="table-statistics"></a>Estadísticas de tabla

Estadísticas son una muy importante para obtener el mejor rendimiento fuera de su almacenamiento de datos de SQL.  Debido a que el almacenamiento de datos de SQL no se crea automáticamente y actualizar estadísticas para usted, como llegue a esperar en la base de datos de SQL Azure, nuestro artículo en [estadísticas][] de lectura podrían ser uno de los artículos más importantes leer para asegurarse de obtener el mejor rendimiento de las consultas.

## <a name="temporary-tables"></a>Tablas temporales

Las tablas temporales son tablas que solo existen durante el inicio de sesión y no se puede ver por otros usuarios.  Tablas temporales pueden ser una buena manera de evitar que otros vean los resultados temporales y reducir la necesidad de limpieza.  Dado que las tablas temporales también usan almacenamiento local, pueden ofrecer un rendimiento más rápido para algunas operaciones.  Consulte los artículos[temporal] de la [Tabla temporal]para obtener más detalles acerca de las tablas temporales.

## <a name="external-tables"></a>Tablas externas

Tablas externas, también conocido como tablas de Polybase son tablas que se pueden consultar desde el almacén de datos de SQL, pero punto a datos externos desde el almacén de datos de SQL.  Por ejemplo, puede crear una tabla externa que señala a los archivos de almacenamiento de blobs de Windows Azure.  Para obtener más detalles acerca de cómo crear y consultar una tabla externa, vea [cargar datos con Polybase][].  

## <a name="unsupported-table-features"></a>Características de tabla no compatibles

Mientras que el almacén de datos SQL contiene muchas de las mismas características de tabla ofrecidas por otras bases de datos, hay algunas características que no son compatibles.  A continuación se muestra una lista de algunas de las características de tabla que ya no son compatibles.

| Características no compatibles |
| --- |
|[Propiedad de identidad][] (consulte [Solución de claves suplentes asignar][])|
|Clave principal, las claves externas único y comprobar [Las restricciones de tabla][]|
|[Índices únicos][]|
|[Columnas calculadas][]|
|[Columnas dispersas][]|
|[Tipos definidos por el usuario][]|
|[Secuencia][]|
|[Desencadenadores][]|
|[Vistas indizadas][]|
|[Sinónimos][]|

## <a name="table-size-queries"></a>Consultas de tamaño de tabla

Una forma sencilla de identificar espacio y filas consumidos por una tabla de cada una de las distribuciones de 60, es usar [DBCC PDW_SHOWSPACEUSED][].

```sql
DBCC PDW_SHOWSPACEUSED('dbo.FactInternetSales');
```

Sin embargo, con los comandos DBCC puede suponer bastante limitación.  Vistas de administración dinámica (DMV), podrá ver más detalle así como dar control mucho mayor sobre los resultados de la consulta.  Iniciar mediante la creación de esta vista, que se hará referencia a muchos de los ejemplos de este y otros artículos.

```sql
CREATE VIEW dbo.vTableSizes
AS
WITH base
AS
(
SELECT 
 GETDATE()                                                             AS  [execution_time]
, DB_NAME()                                                            AS  [database_name]
, s.name                                                               AS  [schema_name]
, t.name                                                               AS  [table_name]
, QUOTENAME(s.name)+'.'+QUOTENAME(t.name)                              AS  [two_part_name]
, nt.[name]                                                            AS  [node_table_name]
, ROW_NUMBER() OVER(PARTITION BY nt.[name] ORDER BY (SELECT NULL))     AS  [node_table_name_seq]
, tp.[distribution_policy_desc]                                        AS  [distribution_policy_name]
, c.[name]                                                             AS  [distribution_column]
, nt.[distribution_id]                                                 AS  [distribution_id]
, i.[type]                                                             AS  [index_type]
, i.[type_desc]                                                        AS  [index_type_desc]
, nt.[pdw_node_id]                                                     AS  [pdw_node_id]
, pn.[type]                                                            AS  [pdw_node_type]
, pn.[name]                                                            AS  [pdw_node_name]
, di.name                                                              AS  [dist_name]
, di.position                                                          AS  [dist_position]
, nps.[partition_number]                                               AS  [partition_nmbr]
, nps.[reserved_page_count]                                            AS  [reserved_space_page_count]
, nps.[reserved_page_count] - nps.[used_page_count]                    AS  [unused_space_page_count]
, nps.[in_row_data_page_count] 
    + nps.[row_overflow_used_page_count] 
    + nps.[lob_used_page_count]                                        AS  [data_space_page_count]
, nps.[reserved_page_count] 
 - (nps.[reserved_page_count] - nps.[used_page_count]) 
 - ([in_row_data_page_count] 
         + [row_overflow_used_page_count]+[lob_used_page_count])       AS  [index_space_page_count]
, nps.[row_count]                                                      AS  [row_count]
from 
    sys.schemas s
INNER JOIN sys.tables t
    ON s.[schema_id] = t.[schema_id]
INNER JOIN sys.indexes i
    ON  t.[object_id] = i.[object_id]
    AND i.[index_id] <= 1
INNER JOIN sys.pdw_table_distribution_properties tp
    ON t.[object_id] = tp.[object_id]
INNER JOIN sys.pdw_table_mappings tm
    ON t.[object_id] = tm.[object_id]
INNER JOIN sys.pdw_nodes_tables nt
    ON tm.[physical_name] = nt.[name]
INNER JOIN sys.dm_pdw_nodes pn
    ON  nt.[pdw_node_id] = pn.[pdw_node_id]
INNER JOIN sys.pdw_distributions di
    ON  nt.[distribution_id] = di.[distribution_id]
INNER JOIN sys.dm_pdw_nodes_db_partition_stats nps
    ON nt.[object_id] = nps.[object_id]
    AND nt.[pdw_node_id] = nps.[pdw_node_id]
    AND nt.[distribution_id] = nps.[distribution_id]
LEFT OUTER JOIN (select * from sys.pdw_column_distribution_properties where distribution_ordinal = 1) cdp
    ON t.[object_id] = cdp.[object_id]
LEFT OUTER JOIN sys.columns c
    ON cdp.[object_id] = c.[object_id]
    AND cdp.[column_id] = c.[column_id]
)
, size
AS
(
SELECT
   [execution_time]
,  [database_name]
,  [schema_name]
,  [table_name]
,  [two_part_name]
,  [node_table_name]
,  [node_table_name_seq]
,  [distribution_policy_name]
,  [distribution_column]
,  [distribution_id]
,  [index_type]
,  [index_type_desc]
,  [pdw_node_id]
,  [pdw_node_type]
,  [pdw_node_name]
,  [dist_name]
,  [dist_position]
,  [partition_nmbr]
,  [reserved_space_page_count]
,  [unused_space_page_count]
,  [data_space_page_count]
,  [index_space_page_count]
,  [row_count]
,  ([reserved_space_page_count] * 8.0)                                 AS [reserved_space_KB]
,  ([reserved_space_page_count] * 8.0)/1000                            AS [reserved_space_MB]
,  ([reserved_space_page_count] * 8.0)/1000000                         AS [reserved_space_GB]
,  ([reserved_space_page_count] * 8.0)/1000000000                      AS [reserved_space_TB]
,  ([unused_space_page_count]   * 8.0)                                 AS [unused_space_KB]
,  ([unused_space_page_count]   * 8.0)/1000                            AS [unused_space_MB]
,  ([unused_space_page_count]   * 8.0)/1000000                         AS [unused_space_GB]
,  ([unused_space_page_count]   * 8.0)/1000000000                      AS [unused_space_TB]
,  ([data_space_page_count]     * 8.0)                                 AS [data_space_KB]
,  ([data_space_page_count]     * 8.0)/1000                            AS [data_space_MB]
,  ([data_space_page_count]     * 8.0)/1000000                         AS [data_space_GB]
,  ([data_space_page_count]     * 8.0)/1000000000                      AS [data_space_TB]
,  ([index_space_page_count]  * 8.0)                                   AS [index_space_KB]
,  ([index_space_page_count]  * 8.0)/1000                              AS [index_space_MB]
,  ([index_space_page_count]  * 8.0)/1000000                           AS [index_space_GB]
,  ([index_space_page_count]  * 8.0)/1000000000                        AS [index_space_TB]
FROM base
)
SELECT * 
FROM size
;
```

### <a name="table-space-summary"></a>Espacio de tabla de resumen

Esta consulta devuelve las filas y el espacio de tabla.  Es una excelente consulta para ver las tablas que son las tablas más grandes y que estén turnos o hash distribuido.  Para tablas hash distribuido también se muestra la columna de distribución.  En la mayoría de los casos, las tablas más grandes debería hash que se distribuye con un índice agrupado columnstore.

```sql
SELECT 
     database_name
,    schema_name
,    table_name
,    distribution_policy_name
,     distribution_column
,    index_type_desc
,    COUNT(distinct partition_nmbr) as nbr_partitions
,    SUM(row_count)                 as table_row_count
,    SUM(reserved_space_GB)         as table_reserved_space_GB
,    SUM(data_space_GB)             as table_data_space_GB
,    SUM(index_space_GB)            as table_index_space_GB
,    SUM(unused_space_GB)           as table_unused_space_GB
FROM 
    dbo.vTableSizes
GROUP BY 
     database_name
,    schema_name
,    table_name
,    distribution_policy_name
,     distribution_column
,    index_type_desc
ORDER BY
    table_reserved_space_GB desc
;
```

### <a name="table-space-by-distribution-type"></a>Espacio de tabla por tipo de distribución

```sql
SELECT 
     distribution_policy_name
,    SUM(row_count)                as table_type_row_count
,    SUM(reserved_space_GB)        as table_type_reserved_space_GB
,    SUM(data_space_GB)            as table_type_data_space_GB
,    SUM(index_space_GB)           as table_type_index_space_GB
,    SUM(unused_space_GB)          as table_type_unused_space_GB
FROM dbo.vTableSizes
GROUP BY distribution_policy_name
;
```

### <a name="table-space-by-index-type"></a>Espacio de tabla por tipo de índice

```sql
SELECT 
     index_type_desc
,    SUM(row_count)                as table_type_row_count
,    SUM(reserved_space_GB)        as table_type_reserved_space_GB
,    SUM(data_space_GB)            as table_type_data_space_GB
,    SUM(index_space_GB)           as table_type_index_space_GB
,    SUM(unused_space_GB)          as table_type_unused_space_GB
FROM dbo.vTableSizes
GROUP BY index_type_desc
;
```

### <a name="distribution-space-summary"></a>Resumen de espacio de distribución.

```sql
SELECT 
    distribution_id
,    SUM(row_count)                as total_node_distribution_row_count
,    SUM(reserved_space_MB)        as total_node_distribution_reserved_space_MB
,    SUM(data_space_MB)            as total_node_distribution_data_space_MB
,    SUM(index_space_MB)           as total_node_distribution_index_space_MB
,    SUM(unused_space_MB)          as total_node_distribution_unused_space_MB
FROM dbo.vTableSizes
GROUP BY     distribution_id
ORDER BY    distribution_id
;
```

## <a name="next-steps"></a>Pasos siguientes

Para obtener más información, vea los artículos en[Tipos de datos]de [Los tipos de datos de tabla], [distribuir una tabla][distribuir], [indización de una tabla]de[índice], [partición de una tabla]de[partición], [Estadísticas de la tabla de mantenimiento de][estadísticas] y [Tablas temporales][temporal].  Para obtener más información sobre procedimientos recomendados, consulte [Prácticas recomendadas de almacén de datos de SQL][].

<!--Image references-->

<!--Article references-->
[Información general]: ./sql-data-warehouse-tables-overview.md
[Tipos de datos]: ./sql-data-warehouse-tables-data-types.md
[Distribuir]: ./sql-data-warehouse-tables-distribute.md
[Índice]: ./sql-data-warehouse-tables-index.md
[Partición]: ./sql-data-warehouse-tables-partition.md
[Estadísticas]: ./sql-data-warehouse-tables-statistics.md
[Temporal]: ./sql-data-warehouse-tables-temporary.md
[Prácticas recomendadas de almacén de datos SQL]: ./sql-data-warehouse-best-practices.md
[Cargar los datos con Polybase]: ./sql-data-warehouse-load-from-azure-blob-storage-with-polybase.md

<!--MSDN references-->
[CREAR TABLA]: https://msdn.microsoft.com/library/mt203953.aspx
[RENAME]: https://msdn.microsoft.com/library/mt631611.aspx
[DBCC PDW_SHOWSPACEUSED]: https://msdn.microsoft.com/library/mt204028.aspx
[Propiedad de identidad]: https://msdn.microsoft.com/library/ms186775.aspx
[Asignación de solución de claves suplentes]: https://blogs.msdn.microsoft.com/sqlcat/2016/02/18/assigning-surrogate-key-to-dimension-tables-in-sql-dw-and-aps/
[Restricciones de tabla]: https://msdn.microsoft.com/library/ms188066.aspx
[Columnas calculadas]: https://msdn.microsoft.com/library/ms186241.aspx
[Columnas dispersas]: https://msdn.microsoft.com/library/cc280604.aspx
[Tipos definidos por el usuario]: https://msdn.microsoft.com/library/ms131694.aspx
[Secuencia]: https://msdn.microsoft.com/library/ff878091.aspx
[Desencadenadores]: https://msdn.microsoft.com/library/ms189799.aspx
[Vistas indizadas]: https://msdn.microsoft.com/library/ms191432.aspx
[Sinónimos]: https://msdn.microsoft.com/library/ms177544.aspx
[Índices únicos]: https://msdn.microsoft.com/library/ms188783.aspx

<!--Other Web references-->
