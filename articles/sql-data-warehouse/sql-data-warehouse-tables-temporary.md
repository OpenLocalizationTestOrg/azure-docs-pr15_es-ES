<properties
   pageTitle="Tablas temporales en el almacén de datos de SQL | Microsoft Azure"
   description="Introducción a las tablas temporales en el almacén de datos de SQL Azure."
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
   ms.date="06/29/2016"
   ms.author="jrj;barbkess;sonyama"/>

# <a name="temporary-tables-in-sql-data-warehouse"></a>Tablas temporales en el almacén de datos de SQL

> [AZURE.SELECTOR]
- [Información general][]
- [Tipos de datos][]
- [Distribuir][]
- [Índice][]
- [Partición][]
- [Estadísticas][]
- [Temporal][]

Tablas temporales son muy útiles para procesar datos - especialmente durante la transformación donde están transitorios los resultados intermedios. En el almacén de datos de SQL tablas temporales existen en el nivel de sesión.  Sólo son visibles a la sesión en el que se crearon y se eliminan automáticamente cuando cierra la sesión.  Tablas temporales ofrecen mejora el rendimiento porque se escriben sus resultados en local en lugar de almacenamiento remoto.  Tablas temporales son ligeramente diferentes en el almacén de datos de SQL Azure base de datos de SQL Azure, tal como puede tener acceso desde cualquier lugar dentro de la sesión, incluyendo dentro y fuera de un procedimiento almacenado.

Este artículo contiene instrucciones esenciales para usar las tablas temporales y resalta los principios de tablas temporales de nivel de sesión. Con la información en este artículo puede ayudarle a dividir su código, mejorar la reutilización y facilitar el mantenimiento del código.

## <a name="create-a-temporary-table"></a>Crear una tabla temporal

Se crean tablas temporales anteponiendo simplemente su nombre de la tabla con un `#`.  Por ejemplo:

```sql
CREATE TABLE #stats_ddl
(
    [schema_name]       NVARCHAR(128) NOT NULL
,   [table_name]            NVARCHAR(128) NOT NULL
,   [stats_name]            NVARCHAR(128) NOT NULL
,   [stats_is_filtered]     BIT           NOT NULL
,   [seq_nmbr]              BIGINT        NOT NULL
,   [two_part_name]         NVARCHAR(260) NOT NULL
,   [three_part_name]       NVARCHAR(400) NOT NULL
)
WITH
(
    DISTRIBUTION = HASH([seq_nmbr])
,   HEAP
)
```

También se pueden crear tablas temporales con un `CTAS` con exactamente el mismo método:

```sql
CREATE TABLE #stats_ddl
WITH
(
    DISTRIBUTION = HASH([seq_nmbr])
,   HEAP
)
AS
(
SELECT
        sm.[name]                                                               AS [schema_name]
,       tb.[name]                                                               AS [table_name]
,       st.[name]                                                               AS [stats_name]
,       st.[has_filter]                                                         AS [stats_is_filtered]
,       ROW_NUMBER()
        OVER(ORDER BY (SELECT NULL))                                            AS [seq_nmbr]
,                                QUOTENAME(sm.[name])+'.'+QUOTENAME(tb.[name])  AS [two_part_name]
,       QUOTENAME(DB_NAME())+'.'+QUOTENAME(sm.[name])+'.'+QUOTENAME(tb.[name])  AS [three_part_name]
FROM    sys.objects         AS ob
JOIN    sys.stats           AS st   ON  ob.[object_id]      = st.[object_id]
JOIN    sys.stats_columns   AS sc   ON  st.[stats_id]       = sc.[stats_id]
                                    AND st.[object_id]      = sc.[object_id]
JOIN    sys.columns         AS co   ON  sc.[column_id]      = co.[column_id]
                                    AND sc.[object_id]      = co.[object_id]
JOIN    sys.tables          AS tb   ON  co.[object_id]      = tb.[object_id]
JOIN    sys.schemas         AS sm   ON  tb.[schema_id]      = sm.[schema_id]
WHERE   1=1
AND     st.[user_created]   = 1
GROUP BY
        sm.[name]
,       tb.[name]
,       st.[name]
,       st.[filter_definition]
,       st.[has_filter]
)
SELECT
    CASE @update_type
    WHEN 1
    THEN 'UPDATE STATISTICS '+[two_part_name]+'('+[stats_name]+');'
    WHEN 2
    THEN 'UPDATE STATISTICS '+[two_part_name]+'('+[stats_name]+') WITH FULLSCAN;'
    WHEN 3
    THEN 'UPDATE STATISTICS '+[two_part_name]+'('+[stats_name]+') WITH SAMPLE '+CAST(@sample_pct AS VARCHAR(20))+' PERCENT;'
    WHEN 4
    THEN 'UPDATE STATISTICS '+[two_part_name]+'('+[stats_name]+') WITH RESAMPLE;'
    END AS [update_stats_ddl]
,   [seq_nmbr]
FROM    t1
;
``` 

>[AZURE.NOTE] `CTAS`es un comando muy eficaces y tiene la ventaja de ser muy eficaz en el uso de espacio de registro de transacciones. 


## <a name="dropping-temporary-tables"></a>Eliminar las tablas temporales

Cuando se crea una nueva sesión, no deben existir tablas temporales.  Sin embargo, si está llamando el mismo procedimiento almacenado, que se crea un archivo temporal con el mismo nombre, para asegurarse de que su `CREATE TABLE` instrucciones son correctas una simple comprobación de existencia previa con un `DROP` puede usarse como en el ejemplo siguiente:

```sql
IF OBJECT_ID('tempdb..#stats_ddl') IS NOT NULL
BEGIN
    DROP TABLE #stats_ddl
END
```

Para codificar coherencia, es aconsejable usar este patrón para las tablas y tablas temporales.  También es una buena idea usar `DROP TABLE` quitar tablas temporales cuando haya terminado con ellos en el código.  En el procedimiento almacenado es bastante común para ver los comandos de menú agrupan al final de un procedimiento para asegurarse de estos objetos de desarrollo desaparecen.

```sql
DROP TABLE #stats_ddl
```

## <a name="modularizing-code"></a>Código modularizing

Dado que las tablas temporales pueden verse en cualquier lugar en una sesión de usuario, puede aprovecharse para ayudar a dividir el código de la aplicación.  Por ejemplo, el siguiente procedimiento almacenado reúne las prácticas recomendadas de arriba para generar DDL que se actualizará todas las estadísticas de la base de datos por nombre de estadística.

```sql
CREATE PROCEDURE    [dbo].[prc_sqldw_update_stats]
(   @update_type    tinyint -- 1 default 2 fullscan 3 sample 4 resample
    ,@sample_pct     tinyint
)
AS

IF @update_type NOT IN (1,2,3,4)
BEGIN;
    THROW 151000,'Invalid value for @update_type parameter. Valid range 1 (default), 2 (fullscan), 3 (sample) or 4 (resample).',1;
END;

IF @sample_pct IS NULL
BEGIN;
    SET @sample_pct = 20;
END;

IF OBJECT_ID('tempdb..#stats_ddl') IS NOT NULL
BEGIN
    DROP TABLE #stats_ddl
END

CREATE TABLE #stats_ddl
WITH
(
    DISTRIBUTION = HASH([seq_nmbr])
)
AS
(
SELECT
        sm.[name]                                                               AS [schema_name]
,       tb.[name]                                                               AS [table_name]
,       st.[name]                                                               AS [stats_name]
,       st.[has_filter]                                                         AS [stats_is_filtered]
,       ROW_NUMBER()
        OVER(ORDER BY (SELECT NULL))                                            AS [seq_nmbr]
,                                QUOTENAME(sm.[name])+'.'+QUOTENAME(tb.[name])  AS [two_part_name]
,       QUOTENAME(DB_NAME())+'.'+QUOTENAME(sm.[name])+'.'+QUOTENAME(tb.[name])  AS [three_part_name]
FROM    sys.objects         AS ob
JOIN    sys.stats           AS st   ON  ob.[object_id]      = st.[object_id]
JOIN    sys.stats_columns   AS sc   ON  st.[stats_id]       = sc.[stats_id]
                                    AND st.[object_id]      = sc.[object_id]
JOIN    sys.columns         AS co   ON  sc.[column_id]      = co.[column_id]
                                    AND sc.[object_id]      = co.[object_id]
JOIN    sys.tables          AS tb   ON  co.[object_id]      = tb.[object_id]
JOIN    sys.schemas         AS sm   ON  tb.[schema_id]      = sm.[schema_id]
WHERE   1=1
AND     st.[user_created]   = 1
GROUP BY
        sm.[name]
,       tb.[name]
,       st.[name]
,       st.[filter_definition]
,       st.[has_filter]
)
SELECT
    CASE @update_type
    WHEN 1
    THEN 'UPDATE STATISTICS '+[two_part_name]+'('+[stats_name]+');'
    WHEN 2
    THEN 'UPDATE STATISTICS '+[two_part_name]+'('+[stats_name]+') WITH FULLSCAN;'
    WHEN 3
    THEN 'UPDATE STATISTICS '+[two_part_name]+'('+[stats_name]+') WITH SAMPLE '+CAST(@sample_pct AS VARCHAR(20))+' PERCENT;'
    WHEN 4
    THEN 'UPDATE STATISTICS '+[two_part_name]+'('+[stats_name]+') WITH RESAMPLE;'
    END AS [update_stats_ddl]
,   [seq_nmbr]
FROM    t1
;
GO
```

En esta fase la única acción que se ha producido es la creación de un procedimiento almacenado que simplemente genera una tabla temporal, stats_ddl #, con instrucciones DDL.  Este procedimiento almacenado quitará #stats_ddl si ya existe para asegurarse de que no falla si ejecuta más de una vez dentro de una sesión.  Sin embargo, dado que no hay ningún `DROP TABLE` al final del procedimiento almacenado cuando se complete el procedimiento almacenado, dejará la tabla creada para que pueda leerlo fuera del procedimiento almacenado.  En el almacén de datos de SQL, a diferencia de otras bases de datos de SQL Server, es posible usar la tabla temporal fuera del procedimiento que creó.  Tablas temporales de almacén de datos SQL pueden ser utilizados **en cualquier lugar** dentro de la sesión. Esto puede provocar más modular y fácil de administrar el código como en el ejemplo siguiente:

```sql
EXEC [dbo].[prc_sqldw_update_stats] @update_type = 1, @sample_pct = NULL;

DECLARE @i INT              = 1
,       @t INT              = (SELECT COUNT(*) FROM #stats_ddl)
,       @s NVARCHAR(4000)   = N''

WHILE @i <= @t
BEGIN
    SET @s=(SELECT update_stats_ddl FROM #stats_ddl WHERE seq_nmbr = @i);

    PRINT @s
    EXEC sp_executesql @s
    SET @i+=1;
END

DROP TABLE #stats_ddl;
```

## <a name="temporary-table-limitations"></a>Limitaciones de la tabla temporal

Almacén de datos SQL imponer algunas limitaciones al implementar tablas temporales.  Actualmente, sólo sesión son compatibles con ámbito tablas temporales.  Las tablas temporales globales no son compatibles.  Además, no se puede crear vistas en las tablas temporales.

## <a name="next-steps"></a>Pasos siguientes

Para obtener más información, vea los artículos de[información general]de [Información general de la tabla],[Tipos de datos]de [Los tipos de datos de tabla], [distribuir una tabla][distribuir], [indización de una tabla]y de[índice], [una tabla de partición][partición] [Estadísticas de la tabla de mantenimiento de][estadísticas].  Para obtener más información sobre procedimientos recomendados, consulte [Prácticas recomendadas de almacén de datos de SQL][].

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

<!--MSDN references-->

<!--Other Web references-->
