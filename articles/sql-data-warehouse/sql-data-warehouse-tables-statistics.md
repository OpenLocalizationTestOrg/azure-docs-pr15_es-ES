<properties
   pageTitle="Administración de estadísticas de tablas en el almacén de datos de SQL | Microsoft Azure"
   description="Introducción a las estadísticas de tablas en el almacén de datos de SQL Azure."
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
   ms.date="06/30/2016"
   ms.author="jrj;barbkess;sonyama"/>

# <a name="managing-statistics-on-tables-in-sql-data-warehouse"></a>Administración de estadísticas de tablas en el almacén de datos de SQL

> [AZURE.SELECTOR]
- [Información general][]
- [Tipos de datos][]
- [Distribuir][]
- [Índice][]
- [Partición][]
- [Estadísticas][]
- [Temporal][]

Más almacenamiento de datos SQL sabe acerca de los datos, más rápido pueden ejecutar consultas en los datos.  La manera en que puede decirle a almacén de datos SQL acerca de los datos es recopilar estadísticas acerca de los datos.  Disponer las estadísticas en los datos es uno de los aspectos más importantes que puede realizar para optimizar las consultas.  Ayuda de las estadísticas almacén de datos SQL crear el plan óptimo de las consultas.  Esto es porque el optimizador de consultas de almacén de datos de SQL es un optimizador en función del costo.  Es decir, compara el costo de los distintos planes de consulta y, a continuación, selecciona el plan con el menor costo, que también debe ser el plan que se ejecutará más rápido.

Pueden crear estadísticas en una sola columna, varias columnas o en un índice de una tabla.  Estadísticas se almacenan en un histograma que captura el rango y la capacidad de selección de valores.  Se trata de interés especial cuando el optimizador debe evaluar combinaciones, GROUP BY, HAVING y cláusulas WHERE en una consulta.  Por ejemplo, si el optimizador considera que la fecha que se va a filtrar en la consulta devolverá la 1 fila, puede elegir muy otro plan que if se calcula que fecha ha seleccionado se devuelven 1 millón de filas.  Aunque es muy importante crear estadísticas, es importante que estadísticas *con precisión* reflejar el estado actual de la tabla.  Tener estadísticas actualizadas garantiza que un buen plan está seleccionado por el optimizador.  Los planes creados por el optimizador son buenos como las estadísticas de los datos.

El proceso de creación y actualización de estadísticas actualmente es un proceso manual, pero es muy fácil de hacer.  Esto es diferente de SQL Server que crea automáticamente y actualiza las estadísticas de índices y columnas único.  Mediante el uso de la siguiente información puede automatizar en gran medida la administración de las estadísticas en los datos. 

## <a name="getting-started-with-statistics"></a>Introducción a las estadísticas

 Crear estadísticas muestras en cada columna es una forma sencilla de empezar a trabajar con estadísticas.  Puesto que es importante mantener actualizada estadísticas, un enfoque conservador puede actualizar las estadísticas diaria o después de cada carga. Siempre hay compensaciones entre el rendimiento y el costo para crear y actualizar las estadísticas.  Si encuentra tarda demasiado tiempo para conservar todas las estadísticas, desea probar ser más selectivo acerca de las columnas que tienen estadísticas o las columnas que necesitan una actualización frecuente.  Por ejemplo, que desea actualizar columnas de fecha diariamente, tal como se pueden agregar nuevos valores en lugar de después de cada carga. De nuevo, obtendrá el mayor beneficio haciendo que las estadísticas de columnas implicadas en las combinaciones, GROUP BY, HAVING y cláusulas WHERE.  Si tiene una tabla con una gran cantidad de columnas que únicamente se utilizan en la cláusula SELECT, las estadísticas de estas columnas no pueden ayudar y gastos algo más esfuerzo para identificar únicamente las columnas donde le ayudará estadísticas, puede reducir el tiempo necesario para mantener las estadísticas.

## <a name="multi-column-statistics"></a>Estadísticas de varias columnas

Además de crear estadísticas en columnas individuales, puede encontrarse con que las consultas se beneficiarán de estadísticas de varias columnas.  Estadísticas de varias columnas son las estadísticas creadas en una lista de columnas.  Incluyen las estadísticas de columna en la primera columna en la lista, además de alguna información de correlación entre columnas denominado densidad.  Por ejemplo, si tiene una tabla que se une a otro en dos columnas, es posible que el almacenamiento de datos de SQL mejor puede optimizar el plan si entiende la relación entre dos columnas.   Estadísticas de varias columnas pueden mejorar el rendimiento de la consulta para algunas operaciones como las combinaciones compuestas y agrupar por.

## <a name="updating-statistics"></a>Actualización de estadísticas

Actualización de estadísticas es una parte importante de su rutina de administración de la base de datos.  Cuando cambie la distribución de datos en la base de datos, las estadísticas deben actualizarse.  Estadísticas obsoletas llevará a rendimiento óptimo de consulta.

Un procedimiento recomendado es actualizar estadísticas en columnas de fecha cada día cuando se agregan nuevas fechas.  Cada nuevas filas de tiempo se cargan en el almacén de datos, se agregan nuevas fechas de carga o transacción. Estos cambiar la distribución de datos y realizar las estadísticas actualizadas. Por el contrario, las estadísticas de una columna de país en una tabla de clientes nunca necesite actualizarse, como, en general, no cambia la distribución de valores. Suponiendo que la distribución es constante entre los clientes, agregar nuevas filas a la variación de tabla no se va a cambiar la distribución de datos. Sin embargo, si el almacén de datos contiene solo un país y reunir datos desde un país nuevo, los datos de varios países que se va a almacenar, a continuación, definitivamente debe actualizar las estadísticas en la columna de país.

Una de las primeras preguntas Preguntar cuando una consulta de solución de problemas, "Son las estadísticas actualizadas?"

Esta pregunta no es uno que pueden responder la antigüedad de los datos. Un objeto de estadísticas actualizada podría ser muy antiguo si no se ha producido ningún cambio material a los datos subyacentes. Cuando el número de filas ha cambiado significativamente o hay algún cambio material en la distribución de valores de una columna determinada *, a continuación,* es el momento de actualizar las estadísticas.  

Para referencia, **SQL Server** (no el almacén de datos SQL) actualiza automáticamente las estadísticas de estas situaciones:

- Si tiene cero filas en la tabla, cuando se agregan filas, obtendrá una actualización automática de estadísticas
- Cuando se agrega más de 500 filas a una tabla comenzando con menos de 500 filas (por ejemplo, en el inicio tiene 499 y, a continuación, agregar 500 filas a un total de 999 filas), recibirá una actualización automática 
- Una vez que haya más de 500 filas que tendrá que agregar 500 filas adicionales + 20% del tamaño de la tabla antes de que verá una actualización automática en el estadísticas

Dado que no hay ningún DMV para determinar si ha cambiado los datos dentro de la tabla desde que se actualizaron las estadísticas de tiempo últimas, conocer la edad de las estadísticas puede proporcionar con parte de la imagen.  Puede usar la siguiente consulta para determinar las estadísticas de la última vez cuando se actualiza en cada tabla.  

> [AZURE.NOTE] Recuerde que si hay un cambio de material de la distribución de valores de una columna determinada, debe actualizar estadísticas independientemente de la última vez que se han actualizado.  

```sql
SELECT
    sm.[name] AS [schema_name],
    tb.[name] AS [table_name],
    co.[name] AS [stats_column_name],
    st.[name] AS [stats_name],
    STATS_DATE(st.[object_id],st.[stats_id]) AS [stats_last_updated_date]
FROM
    sys.objects ob
    JOIN sys.stats st
        ON  ob.[object_id] = st.[object_id]
    JOIN sys.stats_columns sc    
        ON  st.[stats_id] = sc.[stats_id]
        AND st.[object_id] = sc.[object_id]
    JOIN sys.columns co    
        ON  sc.[column_id] = co.[column_id]
        AND sc.[object_id] = co.[object_id]
    JOIN sys.types  ty    
        ON  co.[user_type_id] = ty.[user_type_id]
    JOIN sys.tables tb    
        ON  co.[object_id] = tb.[object_id]
    JOIN sys.schemas sm    
        ON  tb.[schema_id] = sm.[schema_id]
WHERE
    st.[user_created] = 1;
```

Columnas de fecha en un almacén de datos, por ejemplo, normalmente necesitan estadísticas actualizaciones frecuentes. Cada nuevas filas de tiempo se cargan en el almacén de datos, se agregan nuevas fechas de carga o transacción. Estos cambiar la distribución de datos y realizar las estadísticas actualizadas.  Por el contrario, las estadísticas de una columna de género en una tabla de clientes nunca que tenga que se pueden actualizar. Suponiendo que la distribución es constante entre los clientes, agregar nuevas filas a la variación de tabla no se va a cambiar la distribución de datos. Sin embargo, si el almacén de datos contiene solo un género y un nuevo requisito da como resultado varios géneros después definitivamente debe actualizar las estadísticas en la columna género.

Para obtener más información, vea [las estadísticas][] de MSDN.

## <a name="implementing-statistics-management"></a>Implementar la administración de estadísticas

A menudo es una buena idea para ampliar los proceso para asegurarse de que las estadísticas se actualizan al final de la carga de carga de datos. La carga de datos es cuando tablas con más frecuencia cambian su tamaño o su distribución de valores. Por lo tanto, se trata de un lugar lógico implementar algunos procesos de administración.

Algunas orientaciones que se proporcionan a continuación para actualizar las estadísticas durante el proceso de carga:

- Asegúrese de que cada tabla cargado tiene al menos un objeto de estadísticas actualizado. Esto actualiza la información de tamaño (número de fila y número de páginas) de tablas como parte de la actualización de estadísticas.
- Centrarse en las columnas que participan en la combinación, cláusulas GROUP BY, ORDER BY y DISTINCT
- Considere actualizar "ascendente clave" columnas como transacción fechas con más frecuencia como estos valores no se incluirán en el histograma estadísticas.
- Considere la posibilidad de actualizar las columnas de distribución estática menos frecuencia.
- Recuerde que cada objeto estadístico se actualiza en serie. Simplemente la implementación `UPDATE STATISTICS <TABLE_NAME>` puede no ser ideal - especialmente para tablas amplia con muchos de los objetos de estadísticas.

> [AZURE.NOTE] Para obtener más detalles sobre [ascendente clave], consulte las notas del producto SQL Server 2014 cardinalidad estimación modelo.

Para obtener más información, consulte [Estimación de cardinalidad][] en MSDN.

## <a name="examples-create-statistics"></a>Ejemplos: Crear estadísticas

Estos ejemplos muestran cómo usar diversas opciones para la creación de estadísticas. Las opciones que se use para cada columna dependen de las características de los datos y cómo se utilizará la columna en las consultas.

### <a name="a-create-single-column-statistics-with-default-options"></a>A. Crear estadísticas de una columna con las opciones predeterminadas

Para crear estadísticas en una columna, simplemente proporcione un nombre para el objeto de estadísticas y el nombre de la columna.

Esta sintaxis utiliza todas las opciones predeterminadas. De forma predeterminada, almacenamiento de datos SQL muestras 20 por ciento de la tabla al crear estadísticas.

```sql
CREATE STATISTICS [statistics_name] ON [schema_name].[table_name]([column_name]);
```

Por ejemplo:

```sql
CREATE STATISTICS col1_stats ON dbo.table1 (col1);
```

### <a name="b-create-single-column-statistics-by-examining-every-row"></a>B. Crear una sola columna estadísticas examinando todas las filas

La velocidad de muestreo predeterminado de 20 por ciento es suficiente para la mayoría de las situaciones. Sin embargo, puede ajustar la velocidad de muestreo.

Para tomar una muestra de la tabla completa, utilice la siguiente sintaxis:

```sql
CREATE STATISTICS [statistics_name] ON [schema_name].[table_name]([column_name]) WITH FULLSCAN;
```

Por ejemplo:

```sql
CREATE STATISTICS col1_stats ON dbo.table1 (col1) WITH FULLSCAN;
```

### <a name="c-create-single-column-statistics-by-specifying-the-sample-size"></a>C. Crear estadísticas de una columna especificando el tamaño de la muestra

Como alternativa, puede especificar el tamaño de la muestra como un porcentaje:

```sql
CREATE STATISTICS col1_stats ON dbo.table1 (col1) WITH SAMPLE = 50 PERCENT;
```

### <a name="d-create-single-column-statistics-on-only-some-of-the-rows"></a>D. Crear estadísticas de una columna en solo algunas de las filas

Otra opción, puede crear estadísticas en una parte de las filas de la tabla. Esto se denomina una estadística filtrada.

Por ejemplo, podría usar estadísticas filtradas cuando planea una partición específica de una tabla con particiones grande de la consulta. Mediante la creación de estadísticas en los valores de la partición, la precisión de las estadísticas de mejorar y, por tanto, mejorar el rendimiento de la consulta.

Este ejemplo crea estadísticas de un rango de valores. Los valores se podrían definir fácilmente para que coincida con el rango de valores en una partición.

```sql
CREATE STATISTICS stats_col1 ON table1(col1) WHERE col1 > '2000101' AND col1 < '20001231';
```

> [AZURE.NOTE] Para que el optimizador considere usar estadísticas filtradas cuando selecciona el plan de consulta distribuida, la consulta debe ajustarse dentro de la definición del objeto estadísticas. Usando el ejemplo anterior, la consulta donde debe especificar valores de col1 entre 2000101 y 20001231 cláusula.

### <a name="e-create-single-column-statistics-with-all-the-options"></a>E. Crear estadísticas de una columna con todas las opciones

Por supuesto, puede combinar las opciones juntas. El ejemplo siguiente crea un objeto de estadísticas filtrados con un tamaño de la muestra personalizado:

```sql
CREATE STATISTICS stats_col1 ON table1 (col1) WHERE col1 > '2000101' AND col1 < '20001231' WITH SAMPLE = 50 PERCENT;
```

Para obtener la referencia completa, consulte [Crear estadísticas][] en MSDN.

### <a name="f-create-multi-column-statistics"></a>F. Crear estadísticas de varias columnas

Para crear una estadística de varias columnas, simplemente use los ejemplos anteriores, pero especificar más columnas.

> [AZURE.NOTE] El histograma, que se utiliza para calcular el número de filas en el resultado de la consulta, sólo está disponible para la primera columna en la definición del objeto estadísticas.

En este ejemplo, el histograma se encuentra en *producto\_categoría*. Se calculan las estadísticas de columna entre *producto\_categoría* y *producto\_sub_c\ategory*:

```sql
CREATE STATISTICS stats_2cols ON table1 (product_category, product_sub_category) WHERE product_category > '2000101' AND product_category < '20001231' WITH SAMPLE = 50 PERCENT;
```

Puesto que no hay una correlación entre *producto\_categoría* y *producto\_sub\_categoría*, un estado de varias columnas puede ser útil si se tiene acceso a estas columnas al mismo tiempo.

### <a name="g-create-statistics-on-all-the-columns-in-a-table"></a>G. Crear estadísticas en todas las columnas de una tabla

Una forma de crear estadísticas es comandos crear estadísticas de problemas después de crear la tabla.

```sql
CREATE TABLE dbo.table1
(
   col1 int
,  col2 int
,  col3 int
)
WITH
  (
    CLUSTERED COLUMNSTORE INDEX
  )
;

CREATE STATISTICS stats_col1 on dbo.table1 (col1);
CREATE STATISTICS stats_col2 on dbo.table2 (col2);
CREATE STATISTICS stats_col3 on dbo.table3 (col3);
```

### <a name="h-use-a-stored-procedure-to-create-statistics-on-all-columns-in-a-database"></a>H. Utilizar un procedimiento almacenado para crear estadísticas de todas las columnas en una base de datos

Almacén de datos de SQL no tiene un procedimiento almacenado del sistema equivalente a [] [sp_create_stats] en SQL Server. Este procedimiento almacenado crea un objeto de estadísticas de columna en cada columna de la base de datos que ya no tiene estadísticas.

Esto le ayudará a empezar a trabajar con el diseño de la base de datos. No dude en adaptarse a sus necesidades.

```sql
CREATE PROCEDURE    [dbo].[prc_sqldw_create_stats]
(   @create_type    tinyint -- 1 default 2 Fullscan 3 Sample
,   @sample_pct     tinyint
)
AS

IF @create_type NOT IN (1,2,3)
BEGIN
    THROW 151000,'Invalid value for @stats_type parameter. Valid range 1 (default), 2 (fullscan) or 3 (sample).',1;
END;

IF @sample_pct IS NULL
BEGIN;
    SET @sample_pct = 20;
END;

IF OBJECT_ID('tempdb..#stats_ddl') IS NOT NULL
BEGIN;
    DROP TABLE #stats_ddl;
END;

CREATE TABLE #stats_ddl
WITH    (   DISTRIBUTION    = HASH([seq_nmbr])
        ,   LOCATION        = USER_DB
        )
AS
WITH T
AS
(
SELECT      t.[name]                        AS [table_name]
,           s.[name]                        AS [table_schema_name]
,           c.[name]                        AS [column_name]
,           c.[column_id]                   AS [column_id]
,           t.[object_id]                   AS [object_id]
,           ROW_NUMBER()
            OVER(ORDER BY (SELECT NULL))    AS [seq_nmbr]
FROM        sys.[tables] t
JOIN        sys.[schemas] s         ON  t.[schema_id]       = s.[schema_id]
JOIN        sys.[columns] c         ON  t.[object_id]       = c.[object_id]
LEFT JOIN   sys.[stats_columns] l   ON  l.[object_id]       = c.[object_id]
                                    AND l.[column_id]       = c.[column_id]
                                    AND l.[stats_column_id] = 1
LEFT JOIN   sys.[external_tables] e ON  e.[object_id]       = t.[object_id]
WHERE       l.[object_id] IS NULL
AND         e.[object_id] IS NULL -- not an external table
)
SELECT  [table_schema_name]
,       [table_name]
,       [column_name]
,       [column_id]
,       [object_id]
,       [seq_nmbr]
,       CASE @create_type
        WHEN 1
        THEN    CAST('CREATE STATISTICS '+QUOTENAME('stat_'+table_schema_name+ '_' + table_name + '_'+column_name)+' ON '+QUOTENAME(table_schema_name)+'.'+QUOTENAME(table_name)+'('+QUOTENAME(column_name)+')' AS VARCHAR(8000))
        WHEN 2
        THEN    CAST('CREATE STATISTICS '+QUOTENAME('stat_'+table_schema_name+ '_' + table_name + '_'+column_name)+' ON '+QUOTENAME(table_schema_name)+'.'+QUOTENAME(table_name)+'('+QUOTENAME(column_name)+') WITH FULLSCAN' AS VARCHAR(8000))
        WHEN 3
        THEN    CAST('CREATE STATISTICS '+QUOTENAME('stat_'+table_schema_name+ '_' + table_name + '_'+column_name)+' ON '+QUOTENAME(table_schema_name)+'.'+QUOTENAME(table_name)+'('+QUOTENAME(column_name)+') WITH SAMPLE '+@sample_pct+'PERCENT' AS VARCHAR(8000))
        END AS create_stat_ddl
FROM T
;

DECLARE @i INT              = 1
,       @t INT              = (SELECT COUNT(*) FROM #stats_ddl)
,       @s NVARCHAR(4000)   = N''
;

WHILE @i <= @t
BEGIN
    SET @s=(SELECT create_stat_ddl FROM #stats_ddl WHERE seq_nmbr = @i);

    PRINT @s
    EXEC sp_executesql @s
    SET @i+=1;
END

DROP TABLE #stats_ddl;
```

Para crear estadísticas de todas las columnas de la tabla con este procedimiento, simplemente llame al procedimiento.

```sql
prc_sqldw_create_stats;
```

## <a name="examples-update-statistics"></a>Ejemplos: estadísticas de actualización

Para actualizar estadísticas, puede:

1. Actualizar un objeto de estadísticas. Especifique el nombre del objeto de estadísticas que desea actualizar.
2. Actualizar todos los objetos de las estadísticas de una tabla. Especifique el nombre de la tabla en lugar de un objeto de las estadísticas específicas.


### <a name="a-update-one-specific-statistics-object"></a>A. Actualizar un objeto de las estadísticas específicas ###
Use la siguiente sintaxis para actualizar un objeto de estadísticas específicos:

```sql
UPDATE STATISTICS [schema_name].[table_name]([stat_name]);
```

Por ejemplo:

```sql
UPDATE STATISTICS [dbo].[table1] ([stats_col1]);
```

Al actualizar los objetos de las estadísticas específicas, puede minimizar el tiempo y recursos necesarios para administrar estadísticas. Esto requiere algunas consideraciones, sin embargo, para elegir el mejores objetos estadísticas actualizar.


### <a name="b-update-all-statistics-on-a-table"></a>B. Actualizar todas las estadísticas de una tabla ###
Muestra un método sencillo para actualizar todos los objetos de las estadísticas de una tabla.

```sql
UPDATE STATISTICS [schema_name].[table_name];
```

Por ejemplo:

```sql
UPDATE STATISTICS dbo.table1;
```

Esta declaración es fácil de usar. Recuerde esto actualiza todas las estadísticas de la tabla y, por tanto, puede realizar más trabajo que es necesario. Si el rendimiento no es un problema, definitivamente es la manera más fácil y más completa para garantizar que las estadísticas estén actualizadas.

> [AZURE.NOTE] Cuando se actualiza todas las estadísticas de una tabla, almacén de datos SQL realiza un recorrido para muestra la tabla para cada estadísticas. Si la tabla es grande, tiene varias columnas y las estadísticas, puede resultar más eficaz para actualizar estadísticas individuales según la necesidad.

Para una implementación de un `UPDATE STATISTICS` procedimiento, vea el artículo[temporal] de [Tablas temporales]. El método de implementación es ligeramente diferente a la `CREATE STATISTICS` procedimiento anterior, pero el resultado final es el mismo.

Para obtener la sintaxis completa, vea [Actualizar estadísticas][] en MSDN.

## <a name="statistics-metadata"></a>Metadatos de estadísticas
Hay varias funciones que puede usar para buscar información acerca de las estadísticas y vista del sistema. Por ejemplo, puede ver si un objeto de las estadísticas podría ser obsoleto utilizando la función Fecha estadísticas ver cuándo estadísticas última se han creado o actualizado.

### <a name="catalog-views-for-statistics"></a>Vistas de las estadísticas de catálogo
Estas vistas del sistema proporcionan información acerca de las estadísticas:

| Vista de catálogo | Descripción |
| :----------- | :---------- |
| [Sys.Columns][]  | Una fila de cada columna. |
| [Sys.Objects][]  | Una fila por cada objeto de la base de datos. |  |
| [Sys.Schemas][]  | Una fila por cada esquema de la base de datos. |  |
| [Sys.Stats][] | Una fila por cada objeto estadísticas. |
| [Sys.stats_columns][] | Una fila de cada columna en el objeto de estadísticas. Vínculos a sys.columns. |
| [Sys.Tables][] | Una fila por cada tabla (incluye tablas externas). |
| [Sys.table_types][] | Una fila por cada tipo de datos. |


### <a name="system-functions-for-statistics"></a>Funciones estadísticas del sistema
Estas funciones de sistema son útiles para trabajar con las estadísticas:

| Función del sistema | Descripción |
| :-------------- | :---------- |
| [STATS_DATE][]    | Fecha que se actualizó por última vez el objeto de estadísticas. |
| [DBCC SHOW_STATISTICS][] | Proporciona información resumen de nivel y detallada sobre la distribución de valores como entender el objeto estadísticas. |

### <a name="combine-statistics-columns-and-functions-into-one-view"></a>Combinar columnas de estadísticas y funciones en una vista

Esta vista incluye las columnas que se relacionan con las estadísticas y resultados de la función de [] [STATS_DATE()] juntas.

```sql
CREATE VIEW dbo.vstats_columns
AS
SELECT
        sm.[name]                           AS [schema_name]
,       tb.[name]                           AS [table_name]
,       st.[name]                           AS [stats_name]
,       st.[filter_definition]              AS [stats_filter_defiinition]
,       st.[has_filter]                     AS [stats_is_filtered]
,       STATS_DATE(st.[object_id],st.[stats_id])
                                            AS [stats_last_updated_date]
,       co.[name]                           AS [stats_column_name]
,       ty.[name]                           AS [column_type]
,       co.[max_length]                     AS [column_max_length]
,       co.[precision]                      AS [column_precision]
,       co.[scale]                          AS [column_scale]
,       co.[is_nullable]                    AS [column_is_nullable]
,       co.[collation_name]                 AS [column_collation_name]
,       QUOTENAME(sm.[name])+'.'+QUOTENAME(tb.[name])
                                            AS two_part_name
,       QUOTENAME(DB_NAME())+'.'+QUOTENAME(sm.[name])+'.'+QUOTENAME(tb.[name])
                                            AS three_part_name
FROM    sys.objects                         AS ob
JOIN    sys.stats           AS st ON    ob.[object_id]      = st.[object_id]
JOIN    sys.stats_columns   AS sc ON    st.[stats_id]       = sc.[stats_id]
                            AND         st.[object_id]      = sc.[object_id]
JOIN    sys.columns         AS co ON    sc.[column_id]      = co.[column_id]
                            AND         sc.[object_id]      = co.[object_id]
JOIN    sys.types           AS ty ON    co.[user_type_id]   = ty.[user_type_id]
JOIN    sys.tables          AS tb ON  co.[object_id]        = tb.[object_id]
JOIN    sys.schemas         AS sm ON  tb.[schema_id]        = sm.[schema_id]
WHERE   1=1
AND     st.[user_created] = 1
;
```

## <a name="dbcc-showstatistics-examples"></a>Ejemplos de DBCC SHOW_STATISTICS()

DBCC SHOW_STATISTICS() muestra los datos contenidos en un objeto de estadísticas. Estos datos proceden de tres partes.

1. Encabezado
2. Densidad vectorial
3. Histograma

Los metadatos de encabezado acerca de las estadísticas. El histograma muestra la distribución de valores en la primera columna de clave del objeto estadísticas. El vector densidad medidas de correlación entre columnas. SQLDW calcula las estimaciones de cardinalidad con cualquiera de los datos en el objeto de estadísticas.

### <a name="show-header-density-and-histogram"></a>Mostrar encabezado, densidad e histograma

Este sencillo ejemplo muestra las tres partes de un objeto de estadísticas.

```sql
DBCC SHOW_STATISTICS([<schema_name>.<table_name>],<stats_name>)
```

Por ejemplo:

```sql
DBCC SHOW_STATISTICS (dbo.table1, stats_col1);
```

### <a name="show-one-or-more-parts-of-dbcc-showstatistics"></a>Mostrar una o varias partes de DBCC SHOW_STATISTICS();

Si sólo está interesado en ver partes específicas, use la `WITH` cláusula y especificar qué elementos desea ver:

```sql
DBCC SHOW_STATISTICS([<schema_name>.<table_name>],<stats_name>) WITH stat_header, histogram, density_vector
```

Por ejemplo:

```sql
DBCC SHOW_STATISTICS (dbo.table1, stats_col1) WITH histogram, density_vector
```

## <a name="dbcc-showstatistics-differences"></a>Diferencias de DBCC SHOW_STATISTICS()
DBCC SHOW_STATISTICS() más estrictamente se ha implementado en el almacén de datos de SQL en comparación con SQL Server.

1. Sin documentar características no son compatibles
- No se puede usar Stats_stream
- No puede unirse a resultados para subconjuntos específicos de datos estadísticos, por ejemplo, (STAT_HEADER combinación DENSITY_VECTOR)
2. No se puede establecer NO_INFOMSGS para supresión de mensaje
3. No se pueden usar corchetes en los nombres de estadísticas
4. No puede usar nombres de columna para identificar los objetos de estadísticas
5. Error personalizados 2767 no es compatible

## <a name="next-steps"></a>Pasos siguientes

Para obtener más detalles, consulte [DBCC SHOW_STATISTICS][] en MSDN.  Para obtener más información, consulte los artículos[información general]de [Información general de la tabla], [Tipos de datos de tabla de][Tipos de datos], [distribuir una tabla][distribuir], [indización de una tabla]y de[índice], [partición de una tabla]de[partición] [Tablas temporales][temporal].  Para obtener más información sobre procedimientos recomendados, consulte [Prácticas recomendadas de almacén de datos de SQL][].  

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
[Estimación de cardinalidad]: https://msdn.microsoft.com/library/dn600374.aspx
[CREAR ESTADÍSTICAS]: https://msdn.microsoft.com/library/ms188038.aspx
[DBCC SHOW_STATISTICS]:https://msdn.microsoft.com/library/ms174384.aspx
[Estadísticas]: https://msdn.microsoft.com/library/ms190397.aspx
[STATS_DATE]: https://msdn.microsoft.com/library/ms190330.aspx
[Sys.Columns]: https://msdn.microsoft.com/library/ms176106.aspx
[Sys.Objects]: https://msdn.microsoft.com/library/ms190324.aspx
[Sys.Schemas]: https://msdn.microsoft.com/library/ms190324.aspx
[Sys.Stats]: https://msdn.microsoft.com/library/ms177623.aspx
[Sys.stats_columns]: https://msdn.microsoft.com/library/ms187340.aspx
[Sys.Tables]: https://msdn.microsoft.com/library/ms187406.aspx
[Sys.table_types]: https://msdn.microsoft.com/library/bb510623.aspx
[ESTADÍSTICAS DE ACTUALIZACIÓN]: https://msdn.microsoft.com/library/ms187348.aspx

<!--Other Web references-->  
