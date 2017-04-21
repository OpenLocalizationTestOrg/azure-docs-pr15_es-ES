<properties
   pageTitle="Tablas en el almacén de datos de SQL de partición | Microsoft Azure"
   description="Introducción a las particiones de tablas en el almacén de datos de SQL Azure."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="jrowlandjones"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="07/18/2016"
   ms.author="jrj;barbkess;sonyama"/>

# <a name="partitioning-tables-in-sql-data-warehouse"></a>Partición de tablas en el almacén de datos de SQL

> [AZURE.SELECTOR]
- [Información general][]
- [Tipos de datos][]
- [Distribuir][]
- [Índice][]
- [Partición][]
- [Estadísticas][]
- [Temporal][]

Partición es compatible con todos los tipos de tabla de almacén de datos SQL; incluidos columnstore agrupada, índice agrupado y montón.  Partición también se admite en todos los tipos de distribución, incluyendo hash o turnos distribuido.  Partición permite dividir los datos en grupos más pequeños de datos y, en la mayoría de los casos, la partición se realiza en una columna de fecha.

## <a name="benefits-of-partitioning"></a>Ventajas de las particiones

Partición puede beneficiarse rendimiento de mantenimiento y consulta de datos.  Si aprovecha ambos o solo depende de cómo se cargan los datos y si la misma columna puede utilizarse para ambos propósitos, puesto que la partición solo se puede realizar en una columna.

### <a name="benefits-to-loads"></a>Ventajas de carga

La ventaja principal de las particiones en el almacén de datos de SQL es mejorar la eficiencia y el rendimiento de carga datos por uso de la eliminación de partición, cambiar y combinar.  En la mayoría de los casos los datos se dividen en una columna de fecha que está estrechamente asociada a la secuencia que se cargan los datos de la base de datos.  Una de las mayores ventajas de usar particiones para mantener los datos la anulación de registro de transacciones.  Simplemente insertar, actualizar o eliminar datos puede ser el enfoque más sencillo, con un poco pensamiento y esfuerzo, utilizar las particiones durante el proceso de carga puede mejorar considerablemente el rendimiento.

Cambio de partición puede usarse para quitar o reemplazar una sección de una tabla rápidamente.  Por ejemplo, una tabla de hechos de ventas puede contener sólo los datos de los últimos 36 meses.  Al final de cada mes, mes de datos de ventas más antiguo se elimina de la tabla.  Pueden eliminar estos datos utilizando una instrucción delete para eliminar los datos para el mes más antiguo.  Sin embargo, la eliminación de una gran cantidad de datos por filas con una instrucción delete puede tardar mucho tiempo, así como crear el riesgo de transacciones grandes que podría tardar mucho tiempo para deshacer si algo va mal.  Un enfoque más óptimo es simplemente colocar la partición más antigua de datos.  Donde la eliminación de las filas individuales podría tardar horas, la eliminación de una partición completa podría tardar a segundos.

### <a name="benefits-to-queries"></a>Ventajas a las consultas

Partición también puede utilizarse para mejorar el rendimiento de la consulta.  Si una consulta aplica un filtro en una columna de partición, esto puede limitar la búsqueda a solo las particiones cualificadas que puede ser un subconjunto mucho más pequeño de los datos, evita la exploración de una tabla completa.  Con la introducción de índices columnstore agrupadas, las ventajas de rendimiento de eliminación predicado son menos útiles, pero en algunos casos puede ser una ventaja a las consultas.  Por ejemplo, si la tabla de hechos de ventas se divide en 36 meses utilizando el campo de fecha de ventas, consulta de filtro en la fecha de venta puede omitir búsqueda en particiones que no coincidan con el filtro.

## <a name="partition-sizing-guidance"></a>Guía de tamaño de partición

Mientras particiones pueden usarse para mejorar el rendimiento de algunos escenarios, la creación de una tabla con particiones **demasiados** puede repercutir en el rendimiento en determinadas circunstancias.  Estos problemas son especialmente true para tablas columnstore agrupado.  Para crear particiones para ser útil, es importante conocer cuándo usar particiones y el número de particiones para crear.  No hay ninguna regla rápida disco dura sobre cuántas particiones son demasiados, depende de los datos y cuántas particiones se cargan a simultáneamente.  Pero como regla general, piense en Agregar 10s a 100s de particiones, no 1000s.

Al crear particiones en tablas **columnstore agrupada** , es importante tener en cuenta el número de filas se colocará en cada partición.  Para compresión óptima y rendimiento de las tablas de columnstore agrupadas, es necesario un mínimo de 1 millón de filas por partición y la distribución.  Antes de crean particiones, almacén de datos SQL divide ya cada tabla en bases de datos distribuidas 60.  Cualquier partición agregada a una tabla es además de las distribuciones creadas en segundo plano.  Con este ejemplo, si la tabla de hechos de ventas contenía 36 particiones mensuales y, a continuación, dado que el almacén de datos de SQL tiene 60 distribuciones, a continuación, en la tabla de hechos de ventas debe contener 60 millones de filas por mes o 2.1 millones de filas cuando se rellenan todos los meses.  Si una tabla contiene menos filas que el número mínimo recomendado de filas por partición, considere la posibilidad de usar particiones menos para aumentar el número de filas por partición.  Consulte también el artículo de[índice] de [indización]que incluye las consultas que se pueden ejecutar en el almacén de datos de SQL para evaluar la calidad de índices de clúster columnstore.

## <a name="syntax-difference-from-sql-server"></a>Diferencia de sintaxis de SQL Server

Almacén de datos SQL presenta una definición simplificada de particiones que es ligeramente diferente de SQL Server.  Partición funciones y esquemas no se utilizan en el almacén de datos de SQL que aparecen en SQL Server.  En su lugar, todo lo que necesita hacer es identificar columna dividida y los puntos límite.  Aunque la sintaxis de las particiones puede ser ligeramente diferente de SQL Server, los conceptos básicos son los mismos.  Una columna de partición por tabla, que puede ser una partición intervalos de soporte de SQL Server y almacenamiento de datos de SQL.  Para obtener más información acerca de particiones, consulte [dividir tablas y los índices][].

El ejemplo siguiente de una instrucción de almacén de datos de SQL particiones [Crear tabla][] , particiones de la tabla FactInternetSales en la columna OrderDateKey:

```sql
CREATE TABLE [dbo].[FactInternetSales]
(
    [ProductKey]            int          NOT NULL
,   [OrderDateKey]          int          NOT NULL
,   [CustomerKey]           int          NOT NULL
,   [PromotionKey]          int          NOT NULL
,   [SalesOrderNumber]      nvarchar(20) NOT NULL
,   [OrderQuantity]         smallint     NOT NULL
,   [UnitPrice]             money        NOT NULL
,   [SalesAmount]           money        NOT NULL
)
WITH
(   CLUSTERED COLUMNSTORE INDEX
,   DISTRIBUTION = HASH([ProductKey])
,   PARTITION   (   [OrderDateKey] RANGE RIGHT FOR VALUES
                    (20000101,20010101,20020101
                    ,20030101,20040101,20050101
                    )
                )
)
;
```

## <a name="migrating-partitioning-from-sql-server"></a>Migración de particiones desde SQL Server

Para migrar simplemente definiciones de partición de SQL Server al almacén de datos de SQL:

- Eliminar el [esquema de partición][]de SQL Server.
- Agregar la definición de la [función de partición][] a la tabla de crear.

Si va a migrar una tabla con particiones de una instancia de SQL Server la debajo de SQL le puede ayudar a consultar el número de filas que se encuentran en cada partición.  Tenga en cuenta que si se utiliza el mismo nivel de detalle de particiones en el almacén de datos de SQL, el número de filas por partición disminuirá por un factor de 60.  

```sql
-- Partition information for a SQL Server Database
SELECT      s.[name]                        AS      [schema_name]
,           t.[name]                        AS      [table_name]
,           i.[name]                        AS      [index_name]
,           p.[partition_number]            AS      [partition_number]
,           SUM(a.[used_pages]*8.0)         AS      [partition_size_kb]
,           SUM(a.[used_pages]*8.0)/1024    AS      [partition_size_mb]
,           SUM(a.[used_pages]*8.0)/1048576 AS      [partition_size_gb]
,           p.[rows]                        AS      [partition_row_count]
,           rv.[value]                      AS      [partition_boundary_value]
,           p.[data_compression_desc]       AS      [partition_compression_desc]
FROM        sys.schemas s
JOIN        sys.tables t                    ON      t.[schema_id]         = s.[schema_id]
JOIN        sys.partitions p                ON      p.[object_id]         = t.[object_id]
JOIN        sys.allocation_units a          ON      a.[container_id]      = p.[partition_id]
JOIN        sys.indexes i                   ON      i.[object_id]         = p.[object_id]
                                            AND     i.[index_id]          = p.[index_id]
JOIN        sys.data_spaces ds              ON      ds.[data_space_id]    = i.[data_space_id]
LEFT JOIN   sys.partition_schemes ps        ON      ps.[data_space_id]    = ds.[data_space_id]
LEFT JOIN   sys.partition_functions pf      ON      pf.[function_id]      = ps.[function_id]
LEFT JOIN   sys.partition_range_values rv   ON      rv.[function_id]      = pf.[function_id]
                                            AND     rv.[boundary_id]      = p.[partition_number]
WHERE       p.[index_id] <=1
GROUP BY    s.[name]
,           t.[name]
,           i.[name]
,           p.[partition_number]
,           p.[rows]
,           rv.[value]
,           p.[data_compression_desc]
;
```

## <a name="workload-management"></a>Administración de carga de trabajo

Factor de la decisión de partición de tabla en una cuenta parte final es la [administración de carga de trabajo][].  Administración de carga de trabajo en el almacén de datos de SQL es principalmente la administración de memoria y simultaneidad.  En el almacén de datos de SQL la memoria máxima asignada a cada distribución durante la ejecución de consulta es clases de recursos controlados.  Lo ideal es que las particiones se ajustará el tamaño en consideración otros factores como las necesidades de memoria de creación de índices columnstore agrupado.  Agrupadas columnstore índices beneficio considerablemente cuando se les asignan más memoria.  Por lo tanto, desea asegurarse de que no es permanente de una regeneración de índice partición de memoria. Aumentar la cantidad de memoria disponible para la consulta puede conseguirse cambiar de la función de forma predeterminada, smallrc, a una de las otras funciones como largerc.

Información sobre la asignación de memoria por distribución está disponible consultando las vistas dinámicas de administración de controlador de recursos. En realidad su concesión de memoria será menor que las figuras siguientes. Sin embargo, esto proporciona un nivel de guía que puede utilizar al cambiar el tamaño de las particiones para operaciones de administración de datos.  Procure no cambiar el tamaño de las particiones más allá de la concesión de memoria de la clase de recursos de gran tamaño. Si las particiones crecen más allá de esta ilustración corre el riesgo de presión de memoria, lo que lleva a su vez a menos compresión óptima.

```sql
SELECT  rp.[name]                               AS [pool_name]
,       rp.[max_memory_kb]                      AS [max_memory_kb]
,       rp.[max_memory_kb]/1024                 AS [max_memory_mb]
,       rp.[max_memory_kb]/1048576              AS [mex_memory_gb]
,       rp.[max_memory_percent]                 AS [max_memory_percent]
,       wg.[name]                               AS [group_name]
,       wg.[importance]                         AS [group_importance]
,       wg.[request_max_memory_grant_percent]   AS [request_max_memory_grant_percent]
FROM    sys.dm_pdw_nodes_resource_governor_workload_groups  wg
JOIN    sys.dm_pdw_nodes_resource_governor_resource_pools   rp ON wg.[pool_id] = rp.[pool_id]
WHERE   wg.[name] like 'SloDWGroup%'
AND     rp.[name]    = 'SloDWPool'
;
```

## <a name="partition-switching"></a>Cambiar de partición

Almacén de datos SQL admite partición división, combinar y cambiar. Cada una de estas funciones es ejecutarse mediante la instrucción [ALTER TABLE][] .

Para cambiar las particiones entre dos tablas debe asegurarse de que las particiones alinearán en sus respectivos límites y que coinciden con las definiciones de tabla. Como las restricciones de verificación no están disponibles para aplicar el intervalo de valores en una tabla de la tabla de origen debe contener los mismos límites de partición como la tabla de destino. Si no es el caso, el modificador de partición provocarán como partición metadatos no se sincronizarán.

### <a name="how-to-split-a-partition-that-contains-data"></a>Cómo dividir una partición que contiene los datos

El método más eficaz para dividir una partición que ya contiene datos es utilizar un `CTAS` instrucción. Si la tabla con particiones es una columnstore agrupada la partición de la tabla se debe vacía antes de que puede dividirse.

A continuación es una tabla de columnstore divididas de ejemplo que contienen una fila de cada partición:

```sql
CREATE TABLE [dbo].[FactInternetSales]
(
        [ProductKey]            int          NOT NULL
    ,   [OrderDateKey]          int          NOT NULL
    ,   [CustomerKey]           int          NOT NULL
    ,   [PromotionKey]          int          NOT NULL
    ,   [SalesOrderNumber]      nvarchar(20) NOT NULL
    ,   [OrderQuantity]         smallint     NOT NULL
    ,   [UnitPrice]             money        NOT NULL
    ,   [SalesAmount]           money        NOT NULL
)
WITH
(   CLUSTERED COLUMNSTORE INDEX
,   DISTRIBUTION = HASH([ProductKey])
,   PARTITION   (   [OrderDateKey] RANGE RIGHT FOR VALUES
                    (20000101
                    )
                )
)
;

INSERT INTO dbo.FactInternetSales
VALUES (1,19990101,1,1,1,1,1,1);
INSERT INTO dbo.FactInternetSales
VALUES (1,20000101,1,1,1,1,1,1);


CREATE STATISTICS Stat_dbo_FactInternetSales_OrderDateKey ON dbo.FactInternetSales(OrderDateKey);
```

> [AZURE.NOTE] Al crear el objeto de estadística, garantizamos que metadatos de la tabla están más preciso. Si se omite la creación de estadísticas, almacén de datos SQL usará los valores predeterminados. Para obtener más información sobre la estadística revise [las estadísticas][].

A continuación, podemos consultar el recuento de fila utilizando el `sys.partitions` vista del catálogo:

```sql
SELECT  QUOTENAME(s.[name])+'.'+QUOTENAME(t.[name]) as Table_name
,       i.[name] as Index_name
,       p.partition_number as Partition_nmbr
,       p.[rows] as Row_count
,       p.[data_compression_desc] as Data_Compression_desc
FROM    sys.partitions p
JOIN    sys.tables     t    ON    p.[object_id]   = t.[object_id]
JOIN    sys.schemas    s    ON    t.[schema_id]   = s.[schema_id]
JOIN    sys.indexes    i    ON    p.[object_id]   = i.[object_Id]
                            AND   p.[index_Id]    = i.[index_Id]
WHERE t.[name] = 'FactInternetSales'
;
```

Si tratamos dividir esta tabla, se producirá un error:

```sql
ALTER TABLE FactInternetSales SPLIT RANGE (20010101);
```

Msj 35346, nivel 15, estado 1, línea 44 DIVIDIDO cláusula de instrucción ALTER partición error porque la partición no está vacía.  Particiones vacías sólo se pueden dividir en cuando hay un índice de columnstore en la tabla. Considere la posibilidad de deshabilitar el índice columnstore antes de la instrucción ALTER partición, a continuación, volver a generar el índice columnstore una vez completada la partición ALTER.

Sin embargo, podemos utilizar `CTAS` para crear una nueva tabla que contenga los datos.

```sql
CREATE TABLE dbo.FactInternetSales_20000101
    WITH    (   DISTRIBUTION = HASH(ProductKey)
            ,   CLUSTERED COLUMNSTORE INDEX
            ,   PARTITION   (   [OrderDateKey] RANGE RIGHT FOR VALUES
                                (20000101
                                )
                            )
            )
AS
SELECT *
FROM    FactInternetSales
WHERE   1=2
;
```

Como los límites de partición se alinean se permite un modificador. Esto dejará la tabla de origen con una partición vacía que nos podemos dividido posteriormente.

```sql
ALTER TABLE FactInternetSales SWITCH PARTITION 2 TO  FactInternetSales_20000101 PARTITION 2;

ALTER TABLE FactInternetSales SPLIT RANGE (20010101);
```

Todo lo que queda por hacer es alinear los datos a los nuevos límites de partición con `CTAS` y cambiar los datos de nuevo a la tabla principal

```sql
CREATE TABLE [dbo].[FactInternetSales_20000101_20010101]
    WITH    (   DISTRIBUTION = HASH([ProductKey])
            ,   CLUSTERED COLUMNSTORE INDEX
            ,   PARTITION   (   [OrderDateKey] RANGE RIGHT FOR VALUES
                                (20000101,20010101
                                )
                            )
            )
AS
SELECT  *
FROM    [dbo].[FactInternetSales_20000101]
WHERE   [OrderDateKey] >= 20000101
AND     [OrderDateKey] <  20010101
;

ALTER TABLE dbo.FactInternetSales_20000101_20010101 SWITCH PARTITION 2 TO dbo.FactInternetSales PARTITION 2;
```

Una vez haya completado el movimiento de los datos es una buena idea actualizar las estadísticas de la tabla de destino para asegurarse de que reflejan con precisión la distribución de los datos en sus respectivos particiones nueva:

```sql
UPDATE STATISTICS [dbo].[FactInternetSales];
```

### <a name="table-partitioning-source-control"></a>Tabla de control de código fuente

Para evitar la definición de una tabla de **int** en el sistema de control de origen desea tener en cuenta el enfoque siguiente:

1. Crear la tabla como una tabla con particiones pero sin valores partición

```sql
CREATE TABLE [dbo].[FactInternetSales]
(
    [ProductKey]            int          NOT NULL
,   [OrderDateKey]          int          NOT NULL
,   [CustomerKey]           int          NOT NULL
,   [PromotionKey]          int          NOT NULL
,   [SalesOrderNumber]      nvarchar(20) NOT NULL
,   [OrderQuantity]         smallint     NOT NULL
,   [UnitPrice]             money        NOT NULL
,   [SalesAmount]           money        NOT NULL
)
WITH
(   CLUSTERED COLUMNSTORE INDEX
,   DISTRIBUTION = HASH([ProductKey])
,   PARTITION   (   [OrderDateKey] RANGE RIGHT FOR VALUES
                    ()
                )
)
;
```

2. `SPLIT`la tabla como parte del proceso de implementación:

```sql
-- Create a table containing the partition boundaries

CREATE TABLE #partitions
WITH
(
    LOCATION = USER_DB
,   DISTRIBUTION = HASH(ptn_no)
)
AS
SELECT  ptn_no
,       ROW_NUMBER() OVER (ORDER BY (ptn_no)) as seq_no
FROM    (
        SELECT CAST(20000101 AS INT) ptn_no
        UNION ALL
        SELECT CAST(20010101 AS INT)
        UNION ALL
        SELECT CAST(20020101 AS INT)
        UNION ALL
        SELECT CAST(20030101 AS INT)
        UNION ALL
        SELECT CAST(20040101 AS INT)
        ) a
;

-- Iterate over the partition boundaries and split the table

DECLARE @c INT = (SELECT COUNT(*) FROM #partitions)
,       @i INT = 1                                 --iterator for while loop
,       @q NVARCHAR(4000)                          --query
,       @p NVARCHAR(20)     = N''                  --partition_number
,       @s NVARCHAR(128)    = N'dbo'               --schema
,       @t NVARCHAR(128)    = N'FactInternetSales' --table
;

WHILE @i <= @c
BEGIN
    SET @p = (SELECT ptn_no FROM #partitions WHERE seq_no = @i);
    SET @q = (SELECT N'ALTER TABLE '+@s+N'.'+@t+N' SPLIT RANGE ('+@p+N');');

    -- PRINT @q;
    EXECUTE sp_executesql @q;

    SET @i+=1;
END

-- Code clean-up

DROP TABLE #partitions;
```

Con este método, el código de control de código fuente permanece estático y los valores de límite particiones pueden ser dinámicos; evolucionar con el almacén lo largo del tiempo.

## <a name="next-steps"></a>Pasos siguientes

Para obtener más información, consulte los artículos de [Información general de la tabla][información general], [Tipos de datos de tabla de][Tipos de datos], [distribuir una tabla][distribuir], [indización de una tabla]y de[índice], [Estadísticas de la tabla de mantenimiento de][las estadísticas de] [Tablas temporales][temporal].  Para obtener más información sobre procedimientos recomendados, consulte [Prácticas recomendadas de almacén de datos de SQL][].

<!--Image references-->

<!--Article references-->
[Información general]: ./sql-data-warehouse-tables-overview.md
[Tipos de datos]: ./sql-data-warehouse-tables-data-types.md
[Distribuir]: ./sql-data-warehouse-tables-distribute.md
[Índice]: ./sql-data-warehouse-tables-index.md
[Partición]: ./sql-data-warehouse-tables-partition.md
[Estadísticas]: ./sql-data-warehouse-tables-statistics.md
[Temporal]: ./sql-data-warehouse-tables-temporary.md
[administración de carga de trabajo]: ./sql-data-warehouse-develop-concurrency.md
[Prácticas recomendadas de almacén de datos SQL]: ./sql-data-warehouse-best-practices.md

<!-- MSDN Articles -->
[Índices y tablas divididas]: https://msdn.microsoft.com/library/ms190787.aspx
[MODIFICAR TABLA]: https://msdn.microsoft.com/en-us/library/ms190273.aspx
[CREAR TABLA]: https://msdn.microsoft.com/library/mt203953.aspx
[partición (función)]: https://msdn.microsoft.com/library/ms187802.aspx
[esquema de partición]: https://msdn.microsoft.com/library/ms179854.aspx


<!-- Other web references -->
