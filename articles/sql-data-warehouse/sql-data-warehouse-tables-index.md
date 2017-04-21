<properties
   pageTitle="Tablas en el almacén de datos de SQL de indización | Microsoft Azure"
   description="Introducción a la tabla de indización en el almacén de datos de SQL Azure."
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
   ms.date="07/12/2016"
   ms.author="jrj;barbkess;sonyama"/>

# <a name="indexing-tables-in-sql-data-warehouse"></a>Indización de tablas en el almacén de datos de SQL

> [AZURE.SELECTOR]
- [Información general][]
- [Tipos de datos][]
- [Distribuir][]
- [Índice][]
- [Partición][]
- [Estadísticas][]
- [Temporal][]

Almacén de datos SQL ofrece varias opciones de indización, incluidos los [índices columnstore agrupadas][], [índices agrupados e índices no agrupados][].  También no ofrece una opción de índice también conocido como [montón][].  En este artículo trata sobre las ventajas de cada tipo de índice, así como sugerencias para obtener el máximo rendimiento de los índices. Consulte [crear la sintaxis de la tabla][] para obtener más información sobre cómo crear una tabla en el almacén de datos de SQL.

## <a name="clustered-columnstore-indexes"></a>Índices columnstore agrupadas

De forma predeterminada, almacenamiento de datos de SQL crea un índice de agrupado columnstore cuando no hay opciones de índice se especifican en una tabla. Tablas agrupadas columnstore ofrecen el máximo nivel de compresión de datos así como el mejor rendimiento de consulta.  Tablas de columnstore agrupadas generalmente superan tablas agrupadas de índice o montón y suelen ser la mejor opción para tablas de gran tamaño.  Por estas razones, columnstore agrupado es el mejor lugar para comenzar cuando no está seguro de cómo indizar la tabla.  

Para crear una tabla columnstore agrupadas, basta con especificar índice COLUMNSTORE agrupado en la cláusula WITH o abandonar la cláusula WITH:

```SQL
CREATE TABLE myTable   
  (  
    id int NOT NULL,  
    lastName varchar(20),  
    zipCode varchar(6)  
  )  
WITH ( CLUSTERED COLUMNSTORE INDEX );
```

Hay algunos escenarios donde columnstore agrupada no puede ser una buena opción:

- Tablas de ColumnStore no admiten índices secundarios no agrupados.  Considere la posibilidad de tablas de índice agrupado o montón en su lugar.
- Tablas de ColumnStore no admiten varchar (max), nvarchar (max) y varbinary (max).  Considere la posibilidad de índice agrupado o montón en su lugar.
- Tablas de ColumnStore pueden ser menos eficientes para datos transitorias.  Considere la posibilidad de montón y tablas incluso temporales.
- Tablas pequeñas con menos de 100 millones de filas.  Considere la posibilidad de tablas de montones.

## <a name="heap-tables"></a>Tablas de montones

Cuando temporalmente se aterrizaje datos en el almacén de datos de SQL, puede encontrarse con que el uso de una tabla de montón hará todo el proceso más rápido.  Esto es porque cargas a montones son más rápidas que a las tablas de índice y en algunos casos que pueden hacerse las siguientes lecturas de caché.  Si está cargando datos solo para fase antes de ejecutar transformaciones más, cargar la tabla de la tabla de montón será mucho más rápido que cargar los datos a una tabla columnstore agrupado. Además, cargar datos en una [tabla temporal][temporal] también cargará mucho más rápido que cargar una tabla en un almacenamiento permanente.  

Para tablas de búsqueda pequeña, menos de 100 millones de filas, tablas de montones a menudo tienen sentido.  Tablas de clúster columnstore comenzarán lograr la compresión óptima cuando hay más de 100 millones de filas.

Para crear una tabla de montón, solo tiene que especificar MONTÓN en la cláusula WITH:

```SQL
CREATE TABLE myTable   
  (  
    id int NOT NULL,  
    lastName varchar(20),  
    zipCode varchar(6)  
  )  
WITH ( HEAP );
```

## <a name="clustered-and-nonclustered-indexes"></a>Índices agrupados y

Los índices agrupados pueden superan columnstore agrupadas tablas cuando una sola fila es necesario recuperar rápidamente.  Para las consultas que se requiera rendimiento con velocidad extrema un único o muy pocos búsqueda de fila, considere la posibilidad de un índice de clúster índice agrupado o secundario.  La desventaja de utilizar un índice agrupado es que le vendrá solo las consultas que usar un filtro muy selectivo en la columna de índice agrupado.  Para mejorar el filtro en otras columnas un índice no agrupado puede agregarse a otras columnas.  Sin embargo, cada índice que se agrega a una tabla agregará espacio y tiempo de procesamiento de carga.

Para crear una tabla de índice agrupado, solo tiene que especificar índice agrupado en la cláusula WITH:

```SQL
CREATE TABLE myTable   
  (  
    id int NOT NULL,  
    lastName varchar(20),  
    zipCode varchar(6)  
  )  
WITH ( CLUSTERED INDEX (id) );
```

Para agregar un índice no agrupado en una tabla, simplemente use la siguiente sintaxis:

```SQL
CREATE INDEX zipCodeIndex ON t1 (zipCode);
```

> [AZURE.NOTE] Cuando se utiliza CREATE INDEX, se crea un índice no agrupado de forma predeterminada. Además, sólo se permite un índice no agrupado en una tabla de almacenamiento de fila (índice agrupado o MONTÓN). Los índices no agrupados en la parte superior un índice COLUMNSTORE agrupado no está permitido en este momento.


## <a name="optimizing-clustered-columnstore-indexes"></a>Optimización de índices columnstore agrupadas

Agrupado columnstore tablas están organizadas en datos por segmentos.  Disponer de calidad de alta segmento es fundamental para lograr un rendimiento óptimo de consulta en una tabla columnstore.  Calidad de segmento se puede medir por el número de filas de un grupo de filas comprimido.  Calidad de segmento es óptima donde al menos hay filas de 100 KB por fila comprimido agrupar y obtienen el rendimiento como el número de filas por método de grupo de fila 1.048.576 filas, que es la mayoría filas que puede contener un grupo de filas.

La debajo de vista puede crea y utiliza en el sistema para calcular el promedio filas por fila Agrupar e identifican los índices de columnstore clúster óptimo.  La última columna en esta vista se generará como instrucción SQL que se puede utilizar para volver a generar los índices.

```sql
CREATE VIEW dbo.vColumnstoreDensity
AS
SELECT
        GETDATE()                                                               AS [execution_date]
,       DB_Name()                                                               AS [database_name]
,       s.name                                                                  AS [schema_name]
,       t.name                                                                  AS [table_name]
,   COUNT(DISTINCT rg.[partition_number])                   AS [table_partition_count]
,       SUM(rg.[total_rows])                                                    AS [row_count_total]
,       SUM(rg.[total_rows])/COUNT(DISTINCT rg.[distribution_id])               AS [row_count_per_distribution_MAX]
,   CEILING ((SUM(rg.[total_rows])*1.0/COUNT(DISTINCT rg.[distribution_id]))/1048576) AS [rowgroup_per_distribution_MAX]
,       SUM(CASE WHEN rg.[State] = 0 THEN 1                   ELSE 0    END)    AS [INVISIBLE_rowgroup_count]
,       SUM(CASE WHEN rg.[State] = 0 THEN rg.[total_rows]     ELSE 0    END)    AS [INVISIBLE_rowgroup_rows]
,       MIN(CASE WHEN rg.[State] = 0 THEN rg.[total_rows]     ELSE NULL END)    AS [INVISIBLE_rowgroup_rows_MIN]
,       MAX(CASE WHEN rg.[State] = 0 THEN rg.[total_rows]     ELSE NULL END)    AS [INVISIBLE_rowgroup_rows_MAX]
,       AVG(CASE WHEN rg.[State] = 0 THEN rg.[total_rows]     ELSE NULL END)    AS [INVISIBLE_rowgroup_rows_AVG]
,       SUM(CASE WHEN rg.[State] = 1 THEN 1                   ELSE 0    END)    AS [OPEN_rowgroup_count]
,       SUM(CASE WHEN rg.[State] = 1 THEN rg.[total_rows]     ELSE 0    END)    AS [OPEN_rowgroup_rows]
,       MIN(CASE WHEN rg.[State] = 1 THEN rg.[total_rows]     ELSE NULL END)    AS [OPEN_rowgroup_rows_MIN]
,       MAX(CASE WHEN rg.[State] = 1 THEN rg.[total_rows]     ELSE NULL END)    AS [OPEN_rowgroup_rows_MAX]
,       AVG(CASE WHEN rg.[State] = 1 THEN rg.[total_rows]     ELSE NULL END)    AS [OPEN_rowgroup_rows_AVG]
,       SUM(CASE WHEN rg.[State] = 2 THEN 1                   ELSE 0    END)    AS [CLOSED_rowgroup_count]
,       SUM(CASE WHEN rg.[State] = 2 THEN rg.[total_rows]     ELSE 0    END)    AS [CLOSED_rowgroup_rows]
,       MIN(CASE WHEN rg.[State] = 2 THEN rg.[total_rows]     ELSE NULL END)    AS [CLOSED_rowgroup_rows_MIN]
,       MAX(CASE WHEN rg.[State] = 2 THEN rg.[total_rows]     ELSE NULL END)    AS [CLOSED_rowgroup_rows_MAX]
,       AVG(CASE WHEN rg.[State] = 2 THEN rg.[total_rows]     ELSE NULL END)    AS [CLOSED_rowgroup_rows_AVG]
,       SUM(CASE WHEN rg.[State] = 3 THEN 1                   ELSE 0    END)    AS [COMPRESSED_rowgroup_count]
,       SUM(CASE WHEN rg.[State] = 3 THEN rg.[total_rows]     ELSE 0    END)    AS [COMPRESSED_rowgroup_rows]
,       SUM(CASE WHEN rg.[State] = 3 THEN rg.[deleted_rows]   ELSE 0    END)    AS [COMPRESSED_rowgroup_rows_DELETED]
,       MIN(CASE WHEN rg.[State] = 3 THEN rg.[total_rows]     ELSE NULL END)    AS [COMPRESSED_rowgroup_rows_MIN]
,       MAX(CASE WHEN rg.[State] = 3 THEN rg.[total_rows]     ELSE NULL END)    AS [COMPRESSED_rowgroup_rows_MAX]
,       AVG(CASE WHEN rg.[State] = 3 THEN rg.[total_rows]     ELSE NULL END)    AS [COMPRESSED_rowgroup_rows_AVG]
,       'ALTER INDEX ALL ON ' + s.name + '.' + t.NAME + ' REBUILD;'             AS [Rebuild_Index_SQL]
FROM    sys.[pdw_nodes_column_store_row_groups] rg
JOIN    sys.[pdw_nodes_tables] nt                   ON  rg.[object_id]          = nt.[object_id]
                                                    AND rg.[pdw_node_id]        = nt.[pdw_node_id]
                                                    AND rg.[distribution_id]    = nt.[distribution_id]
JOIN    sys.[pdw_table_mappings] mp                 ON  nt.[name]               = mp.[physical_name]
JOIN    sys.[tables] t                              ON  mp.[object_id]          = t.[object_id]
JOIN    sys.[schemas] s                             ON t.[schema_id]            = s.[schema_id]
GROUP BY
        s.[name]
,       t.[name]
;
```

Ahora que ha creado la vista, ejecute esta consulta para identificar las tablas con grupos de filas con menos de 100 KB filas.  Por supuesto, es aconsejable aumentar el umbral de 100 KB si está buscando más calidad óptima segmento. 

```sql
SELECT  *
FROM    [dbo].[vColumnstoreDensity]
WHERE   COMPRESSED_rowgroup_rows_AVG < 100000
        OR INVISIBLE_rowgroup_rows_AVG < 100000
```

Una vez que ha ejecutado la consulta que se puede empezar a ver los datos y analizar los resultados. Esta tabla explica qué debe buscar en el análisis de grupo de la fila.


| Columna                             | Cómo usar estos datos                                                                                                                                                                      |
| ---------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [table_partition_count]            | Si la tabla tiene particiones, puede esperar ver la cuenta del grupo abrir fila superior. Cada partición de la distribución podría tener en teoría un grupo de filas abierta asociado. Incluir esto en el análisis. Una tabla pequeña que ha sido dividida podría optimizar eliminando las particiones totalmente como mejorar la compresión.                                                                        |
| [row_count_total]                  | Número total de filas de la tabla. Por ejemplo, puede usar este valor para calcular el porcentaje de filas de estado comprimido.                                                                      |
| [row_count_per_distribution_MAX]   | Si todas las filas se distribuyen este valor es el número de filas por distribución de destino. Compare este valor con el compressed_rowgroup_count.                                 |
| [COMPRESSED_rowgroup_rows]         | Número total de filas de columnstore formato para la tabla.                                                                                                                                 |
| [COMPRESSED_rowgroup_rows_AVG]     | Si el promedio de filas es considerablemente menor que el número máximo de filas para un grupo de filas, a continuación, piense en usar CTAS o modificar REGENERACIÓN de índice para volver a comprimir los datos                     |
| [COMPRESSED_rowgroup_count]        | Número de grupos de filas en formato columnstore. Si este número es muy alta en relación con la tabla es un indicador de que la densidad columnstore es baja.                                  |
| [COMPRESSED_rowgroup_rows_DELETED] | Filas lógicamente se eliminan en formato columnstore. Si el número es alto en relación con el tamaño de la tabla, considere la posibilidad de recrear la partición o volver a generar el índice como esto elimina física. |
| [COMPRESSED_rowgroup_rows_MIN]     | Use esta opción junto con las columnas promedio y MAX para comprender el rango de valores para los grupos de fila en su columnstore. Un número bajo por encima del umbral de carga (102,400 por distribución particiones alineadas) sugiere que las optimizaciones están disponibles en la carga de datos                                                                                                                                                 |
| [COMPRESSED_rowgroup_rows_MAX]     | Como encima                                                                                                                                                                                  |
| [OPEN_rowgroup_count]              | Los grupos de fila abrir son normales. Uno esperaría razonablemente un grupo de filas abierta por distribución de tabla (60). Números excesivo sugieren carga en todas las particiones de datos. Vuelva a comprobar la estrategia de partición para asegurarse de que es el sonido                                                                                                                                                                                                |
| [OPEN_rowgroup_rows]               | Cada grupo de la fila puede tener 1.048.576 filas en él como máximo. Usar este valor para ver cómo completa los grupos de filas abierto actualmente                                                                 |
| [OPEN_rowgroup_rows_MIN]           | Abrir grupos indican los datos que se va a filtrar cargado en la tabla o que la carga anterior esparcido por restante filas en este grupo de fila. Utilizar MIN, MAX, grupos de filas de columnas AVG para ver la cantidad de datos es sal en Abrir. Para tablas pequeñas podría ser 100% de todos los datos. En cuyo caso ALTER REGENERACIÓN de índice para obligar a los datos a columnstore.                                                                       |
| [OPEN_rowgroup_rows_MAX]           | Como encima                                                                                                                                                                                  |
| [OPEN_rowgroup_rows_AVG]           | Como encima                                                                                                                                                                                  |
| [CLOSED_rowgroup_rows]             | Busque las filas de grupo cerrado fila como una comprobación de validez.                                                                                                                                       |
| [CLOSED_rowgroup_count]            | El número de fila cerrado grupos debería bajo si alguna se ven en absoluto. Grupos de fila cerrado se pueden convertir a rupos rowg comprimido mediante ALTER INDEX... SANEAMIENTO comando. Sin embargo, no es necesario normalmente. Grupos cerrados se convierten automáticamente a los grupos de fila columnstore por el proceso de "mover tupla" del fondo.                                                                                               |
| [CLOSED_rowgroup_rows_MIN]         | Grupos de filas cerrado deberían tener una tasa de relleno muy alta. Si la tasa de relleno para un grupo de filas cerrados es baja, un análisis más profundo de la columnstore es necesario.                                   |
| [CLOSED_rowgroup_rows_MAX]         | Como encima                                                                                                                                                                                  |
| [CLOSED_rowgroup_rows_AVG]         | Como encima                                                                                                                                                                                  |
| [Rebuild_Index_SQL]         | SQL para volver a generar el índice columnstore de una tabla                                                                                                                                                     |

## <a name="causes-of-poor-columnstore-index-quality"></a>Causas de columnstore una mala calidad de índice

Si ha identificado a tablas con una calidad deficiente segmento, desea identificar la causa.  A continuación se muestran algunas causas comunes de una mala segmento quaility:

1. Presión de memoria cuando se creó el índice
2. Gran volumen de operaciones de DML
3. Pequeña o filtrar las operaciones de carga
4. Demasiadas particiones

Estos factores pueden provocar un índice columnstore tener significativamente menor que 1 millón de filas óptimo por grupo de filas.  También pueden provocar filas ir al grupo de fila delta en lugar de un grupo de filas comprimido. 

### <a name="memory-pressure-when-index-was-built"></a>Presión de memoria cuando se creó el índice

El número de filas por grupo de filas comprimida está directamente relacionadas con el ancho de la fila y la cantidad de memoria disponible para procesar el grupo de la fila.  Cuando se escriben filas a las tablas de columnstore en memoria, puede sufrir columnstore calidad de segmento.  Por lo tanto, lo mejor es dar a la sesión que está escribiendo en el acceso de tablas de índice columnstore a toda la memoria como sea posible.  Puesto que hay un equilibrio entre memoria y simultaneidad, la orientación sobre la asignación de memoria derecha depende de los datos de cada fila de la tabla, la cantidad de DWU ha asignado a su sistema y la cantidad de ranuras de simultaneidad que puede dar a la sesión que está escribiendo datos en la tabla.  Como práctica recomendada, se recomienda comenzando con xlargerc si está utilizando DW300 o menos, largerc si está utilizando DW400 DW600 y mediumrc si está utilizando DW1000 y superiores.

### <a name="high-volume-of-dml-operations"></a>Gran volumen de operaciones de DML

Un gran volumen de operaciones de DML que actualizar y eliminar filas puede introducir ineficiencia en la columnstore. Esto es cierto cuando se modifica la mayoría de las filas de un grupo de filas.

- Eliminar una fila de un grupo de filas comprimido solo lógicamente marca la fila como eliminado. La fila permanece en el grupo de fila comprimido hasta que se vuelve a crear la partición o la tabla.
- Insertar una fila, agrega la fila a una tabla de rowstore interno llamada a un grupo de filas de delta. La fila insertada no se convierte en columnstore hasta que el grupo de la fila de delta es completo y está marcado como cerrado. Grupos de filas se cierran una vez que lleguen a la capacidad máxima de 1.048.576 filas. 
- Actualizar una fila en formato columnstore se procesa como delete lógica y, a continuación, una instrucción insert. La fila insertada puede almacenarse en el almacén de delta.

Actualización por lotes e insertar operaciones que superan el umbral de masa de 102.400 filas por partición distribución alineado se escriben directamente en el formato columnstore. Sin embargo, suponiendo que una distribución uniforme, necesarias para modificar más de 6.144 millones de filas en una única operación para ello. Si el número de filas para una partición determinada alineado distribución es menor que 102,400, a continuación, las filas se enviarán a la tienda delta y permanecerán allí hasta que se han insertado o modificado para cerrar el grupo de filas suficientes filas o se ha vuelto al índice.

### <a name="small-or-trickle-load-operations"></a>Pequeña o filtrar las operaciones de carga

Pequeño carga que flujo de almacén de datos SQL en ocasiones también se conocen como filtrar cargas. Normalmente representan una secuencia de constante cercana de datos que se ingestión por el sistema. Sin embargo, como esta secuencia está cerca continua el volumen de filas no es especialmente grande. Más a menudo los datos están considerablemente en el umbral necesario para una carga directa a columnstore formato.

En estos casos, suele ser mejor land los datos primero en el almacenamiento de blobs de Windows Azure y deje que se acumulará antes de la carga. Esta técnica a menudo se conoce como *lotes micro*.

### <a name="too-many-partitions"></a>Demasiadas particiones

Debe tener en cuenta es el impacto de las particiones en las tablas columnstore agrupado.  Antes de dividir, almacén de datos SQL ya divide los datos en bases de datos de 60.  Además, partición divide los datos.  Si divide los datos, que desea tener en cuenta que **cada** partición debe tener al menos 1 millón de filas para beneficiarse de un índice agrupado columnstore.  Si divide la tabla en 100 particiones, la tabla tendrán que tener al menos un 6 millones de filas para beneficiarse de un índice agrupado columnstore (filas de *100 particiones* 1 millón de 60 distribuciones). Si la tabla de 100 partición no tiene 6 millones de filas, reduzca el número de particiones o considere la posibilidad de usar una tabla de montón en su lugar.

Una vez que se han cargado las tablas con algunos datos, siga los pasos siguientes para identificar y volver a crear tablas con clúster óptimo columnstore índices.

## <a name="rebuilding-indexes-to-improve-segment-quality"></a>Reconstruir índices para mejorar la calidad del segmento

### <a name="step-1-identify-or-create-user-which-uses-the-right-resource-class"></a>Paso 1: Identificar o crear usuario que usa la clase recursos derecha

Es una forma rápida de mejorar la calidad de segmento inmediatamente a generar el índice.  La instrucción SQL devuelta por la vista anterior devolverá una instrucción ALTER REGENERACIÓN de índice que se puede utilizar para volver a generar los índices.  Al volver a generar los índices, asegúrese de que asigne memoria insuficiente para la sesión que volverá a crear el índice.  Para ello, aumentar la clase de recurso de un usuario que tiene permisos para generar el índice en esta tabla a los requisitos mínimos.  No se puede cambiar la clase de recurso del usuario al propietario de la base de datos, por lo que si no ha creado un usuario en el sistema, debe hacerlo en primer lugar.  El mínimo que se recomienda es xlargerc si está utilizando DW300 o menos, largerc si está utilizando DW400 DW600 y mediumrc si está utilizando DW1000 y superiores.

A continuación se muestra un ejemplo de cómo asignar más memoria a un usuario aumentando su clase de recursos.  Para obtener más información sobre recursos clases y cómo crear un nuevo usuario pueden encontrarse en la [administración de simultaneidad y la carga de trabajo] [ Concurrency] artículo.

```sql
EXEC sp_addrolemember 'xlargerc', 'LoadUser'
```

### <a name="step-2-rebuild-clustered-columnstore-indexes-with-higher-resource-class-user"></a>Paso 2: Generar índices de columnstore agrupadas con mayor usuario de clase de recursos
Inicie sesión como el usuario del paso 1 (por ejemplo, LoadUser), que ahora está utilizando una clase superior de recursos y ejecutar las instrucciones ALTER INDEX.  Asegúrese de que este usuario tiene permisos de ALTER a las tablas donde el índice que se regenera.  Estos ejemplos muestran cómo volver a generar el índice columnstore todo o cómo volver a generar una sola partición. En tablas de gran tamaño, es más práctico para volver a generar índices de una sola partición a la vez.

Como alternativa, en lugar de volver a generar el índice, puede copiar la tabla a una tabla nueva con [CTAS][].  ¿Qué forma es mejor? Para grandes volúmenes de datos, [CTAS][] suele ser más rápido que [ALTER INDEX][]. Para más pequeños volúmenes de datos, [ALTER INDEX][] es más fácil de usar y no requieren que intercambiar fuera de la tabla.  Consulte **creación de nuevos índices con CTAS y cambio de partición** a continuación para obtener más detalles acerca de cómo volver a crear índices con CTAS.

```sql
-- Rebuild the entire clustered index
ALTER INDEX ALL ON [dbo].[DimProduct] REBUILD
```

```sql
-- Rebuild a single partition
ALTER INDEX ALL ON [dbo].[FactInternetSales] REBUILD Partition = 5
```

```sql
-- Rebuild a single partition with archival compression
ALTER INDEX ALL ON [dbo].[FactInternetSales] REBUILD Partition = 5 WITH (DATA_COMPRESSION = COLUMNSTORE_ARCHIVE)
```

```sql
-- Rebuild a single partition with columnstore compression
ALTER INDEX ALL ON [dbo].[FactInternetSales] REBUILD Partition = 5 WITH (DATA_COMPRESSION = COLUMNSTORE)
```

Volver a generar un índice en el almacén de datos de SQL es una operación sin conexión.  Para obtener más información acerca de cómo reconstruir índices, consulte la sección ALTER REGENERACIÓN de índice en [Columnstore desfragmentación de índices][] y el tema de sintaxis [ALTER INDEX][].
 
### <a name="step-3-verify-clustered-columnstore-segment-quality-has-improved"></a>Paso 3: Comprobar la calidad de segmento columnstore agrupado ha mejorado
Volver a ejecutar la consulta qué tabla identificado con mala calidad de segmento y comprobar la calidad de segmento ha mejorado.  Si no mejora la calidad de segmento, es posible que las filas de la tabla son más amplia.  Considere el uso de una clase de recursos superior o DWU al volver a generar los índices.

 
## <a name="rebuilding-indexes-with-ctas-and-partition-switching"></a>Reconstruir índices con CTAS y cambio de partición

Este ejemplo usa [CTAS][] y partición cambiar para volver a crear una partición de tabla. 

```sql
-- Step 1: Select the partition of data and write it out to a new table using CTAS
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
FROM    [dbo].[FactInternetSales]
WHERE   [OrderDateKey] >= 20000101
AND     [OrderDateKey] <  20010101
;

-- Step 2: Create a SWITCH out table
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
FROM    [dbo].[FactInternetSales]
WHERE   1=2 -- Note this table will be empty

-- Step 3: Switch OUT the data 
ALTER TABLE [dbo].[FactInternetSales] SWITCH PARTITION 2 TO  [dbo].[FactInternetSales_20000101] PARTITION 2;

-- Step 4: Switch IN the rebuilt data
ALTER TABLE [dbo].[FactInternetSales_20000101_20010101] SWITCH PARTITION 2 TO  [dbo].[FactInternetSales] PARTITION 2;
```

Para obtener más detalles acerca de cómo volver a crear particiones con `CTAS`, vea el artículo de [partición][] .

## <a name="next-steps"></a>Pasos siguientes

Para obtener más información, vea los artículos en[información general]de [Información general de la tabla], [Tipos de datos de tabla de][Tipos de datos], [distribuir una tabla][distribuir], [partición de una tabla]de[partición], [Estadísticas de la tabla de mantenimiento de][estadísticas] y [Tablas temporales][temporal].  Para obtener más información sobre procedimientos recomendados, consulte [Prácticas recomendadas de almacén de datos de SQL][].

<!--Image references-->

<!--Article references-->
[Información general]: ./sql-data-warehouse-tables-overview.md
[Tipos de datos]: ./sql-data-warehouse-tables-data-types.md
[Distribuir]: ./sql-data-warehouse-tables-distribute.md
[Índice]: ./sql-data-warehouse-tables-index.md
[Partición]: ./sql-data-warehouse-tables-partition.md
[Estadísticas]: ./sql-data-warehouse-tables-statistics.md
[Temporal]: ./sql-data-warehouse-tables-temporary.md
[Concurrency]: ./sql-data-warehouse-develop-concurrency.md
[CTAS]: ./sql-data-warehouse-develop-ctas.md
[Prácticas recomendadas de almacén de datos SQL]: ./sql-data-warehouse-best-practices.md

<!--MSDN references-->
[MODIFICAR ÍNDICE]: https://msdn.microsoft.com/library/ms188388.aspx
[montón]: https://msdn.microsoft.com/library/hh213609.aspx
[índices agrupados e índices no agrupados]: https://msdn.microsoft.com/library/ms190457.aspx
[crear la sintaxis de la tabla]: https://msdn.microsoft.com/library/mt203953.aspx
[Desfragmentación de índices ColumnStore]: https://msdn.microsoft.com/library/dn935013.aspx#Anchor_1
[índices columnstore agrupadas]: https://msdn.microsoft.com/library/gg492088.aspx

<!--Other Web references-->
