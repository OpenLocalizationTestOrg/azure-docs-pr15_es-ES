<properties
   pageTitle="Optimización de transacciones de almacén de datos SQL | Microsoft Azure"
   description="Instrucciones de prácticas recomendadas sobre cómo escribir actualizaciones eficaz de transacciones en el almacén de datos de SQL Azure"
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
   ms.date="07/31/2016"
   ms.author="jrj;barbkess"/>

# <a name="optimizing-transactions-for-sql-data-warehouse"></a>Optimización de transacciones de almacén de datos SQL

En este artículo se explica cómo optimizar el rendimiento del código transacciones minimizando el riesgo para deshacer larga.

## <a name="transactions-and-logging"></a>Las transacciones y registro

Las transacciones son un componente importante de un motor de base de datos relacional. Almacén de datos de SQL utiliza las transacciones durante la modificación de los datos. Estas son las transacciones pueden ser explícitas o implícitas. Solo `INSERT`, `UPDATE` y `DELETE` instrucciones son ejemplos de transacciones implícitas. Usando el desarrollador escribir explícitamente transacciones explícitas `BEGIN TRAN`, `COMMIT TRAN` o `ROLLBACK TRAN` y se usan normalmente cuando necesitan unidas en una sola unidad atómica varios resúmenes de modificación. 

Almacén de datos de SQL Azure confirma cambios a la base de datos con los registros de transacciones. Cada distribución tiene su propio registro de transacciones. Escritura de registro de transacciones es automática. No es necesario realizar ninguna configuración. Sin embargo, mientras que este proceso garantiza la escritura introducir una sobrecarga del sistema. Puede minimizar este impacto escribiendo código transacciones eficaz. Código transacciones eficaz ampliamente se divide en dos categorías.

- Aproveche construcciones de registro mínimo posible
- Procesamiento de datos con ámbito lotes para evitar singulares transacciones de larga ejecución
- Adoptar una partición de cambio de trama para grandes modificaciones en una partición determinada

## <a name="minimal-vs-full-logging"></a>Mínimo frente a registro completo

A diferencia de las operaciones de registradas completo, que utilizan el registro de transacciones para realizar un seguimiento de cada cambio de fila, operaciones registradas al mínimo realizar un seguimiento de las asignaciones de extensiones y solo los cambios de metadatos. Por lo tanto, registro mínimo implica registro solo la información necesaria para deshacer la transacción en caso de un error o una solicitud explícita (`ROLLBACK TRAN`). Como mucho menos un seguimiento de información del registro de transacciones, una operación de registrada mínimo funciona mejor que una operación de registrada completo de tamaño similar. Además, dado que escribe menos ir el registro de transacciones, se genera una cantidad mucho menor de datos de registro y por lo que es más i/OS eficaz.

Los límites de seguridad de la transacción solo se aplican a operaciones de registro completo.

>[AZURE.NOTE] Operaciones registradas al mínimo pueden participar en transacciones explícitas. Mientras se realiza un seguimiento de todos los cambios en las estructuras de asignación, es posible deshacer operaciones registradas al mínimo. Es importante comprender que "mínimo" se registra el cambio no es no ha iniciado.

## <a name="minimally-logged-operations"></a>Operaciones registradas al mínimo

Las operaciones siguientes son capaces de que se registra como mínimo:

- Crear tabla AS SELECT ([CTAS][])
- INSERTAR... SELECCIONE
- CREAR ÍNDICE
- ALTER REGENERACIÓN DE ÍNDICE
- QUITAR EL ÍNDICE
- TRUNCAR LA TABLA
- ELIMINAR TABLA
- MODIFICAR TABLA CAMBIAR PARTICIÓN

<!--
- MERGE
- UPDATE on LOB Types .WRITE
- SELECT..INTO
-->

>[AZURE.NOTE] Operaciones de movimiento de datos internos (como `BROADCAST` y `SHUFFLE`) no se ven afectados por el límite de seguridad de la transacción.

## <a name="minimal-logging-with-bulk-load"></a>Registro mínimo con carga masiva

`CTAS`y `INSERT...SELECT` son masiva ambas operaciones de carga. Sin embargo, ambos influenciadas por la definición de tabla de destino y dependen del escenario de carga. A continuación encontrará una tabla que explica si la operación masiva se registra completamente o como mínimo:  

| Índice principal               | Escenario de carga                                            | Modo de registro |
| --------------------------- | -------------------------------------------------------- | ------------ |
| Montón                        | Cualquier                                                      | **Mínimo**  |
| Índice agrupado             | Tabla de destino vacío                                       | **Mínimo**  |
| Índice agrupado             | No se superpone a filas cargadas con las páginas existentes de destino | **Mínimo**  |
| Índice agrupado             | Superposición de filas cargadas con las páginas existentes de destino        | Pantalla completa         |
| Índice de Columnstore agrupado | Tamaño de los lotes > = 102,400 por distribución particiones alineadas | **Mínimo**  |
| Índice de Columnstore agrupado | Lote tamaño < 102,400 por distribución particiones alineadas  | Pantalla completa         |

Es importante recordar que cualquier escritura actualizar secundarios o no agrupado índices siempre se referirá operaciones de registro completo.

> [AZURE.IMPORTANT] Almacén de datos de SQL tiene 60 distribuciones. Por lo tanto, suponiendo que se distribuyen todas las filas y de inicio en una sola partición, el lote tendrá que contiene 6,144,000 filas o mayor estar registrado como mínimo al escribir en un índice de Columnstore agrupado. Si la tabla tiene particiones y las filas que se inserten abarcan límites de partición, deberá 6,144,000 filas por límite de partición suponiendo que incluso la distribución de datos. Cada partición en cada distribución independientemente debe superar el umbral de 102.400 fila para la inserción se registran como mínimo en la distribución.

Cargar datos en una tabla no vacía con un índice agrupado a menudo puede contener una mezcla de filas de registradas completo y registradas al mínimo. Un índice agrupado es un árbol equilibrado (árbol b) de las páginas. Si la página que se escriben en ya contiene las filas de otra transacción, a continuación, estos escribe totalmente registrarán. Sin embargo, si la página está vacía, a continuación, la escritura en esa página se mínimo registrarán.

## <a name="optimizing-deletes"></a>Optimización de eliminaciones

`DELETE`es una operación de registrada completo.  Si necesita eliminar una gran cantidad de datos de una tabla o una partición, a menudo tiene más sentido `SELECT` los datos que desea conservar, que se pueden ejecutar como una operación de registrada mínimo.  Para ello, cree una nueva tabla con [CTAS][].  Una vez creada, use el valor de [cambiar el nombre][] para intercambiar fuera de la tabla anterior con la tabla recién creada.

```sql
-- Delete all sales transactions for Promotions except PromotionKey 2.

--Step 01. Create a new table select only the records we want to kep (PromotionKey 2)
CREATE TABLE [dbo].[FactInternetSales_d]
WITH
(   CLUSTERED COLUMNSTORE INDEX
,   DISTRIBUTION = HASH([ProductKey])
,   PARTITION   (   [OrderDateKey] RANGE RIGHT 
                                    FOR VALUES  (   20000101, 20010101, 20020101, 20030101, 20040101, 20050101
                                                ,   20060101, 20070101, 20080101, 20090101, 20100101, 20110101
                                                ,   20120101, 20130101, 20140101, 20150101, 20160101, 20170101
                                                ,   20180101, 20190101, 20200101, 20210101, 20220101, 20230101
                                                ,   20240101, 20250101, 20260101, 20270101, 20280101, 20290101
                                                )
)
AS
SELECT  *
FROM    [dbo].[FactInternetSales]
WHERE   [PromotionKey] = 2
OPTION (LABEL = 'CTAS : Delete')
;

--Step 02. Rename the Tables to replace the 
RENAME OBJECT [dbo].[FactInternetSales]   TO [FactInternetSales_old];
RENAME OBJECT [dbo].[FactInternetSales_d] TO [FactInternetSales];
```

## <a name="optimizing-updates"></a>Optimización de actualizaciones

`UPDATE`es una operación de registrada completo.  Si necesita actualizar un gran número de filas de una tabla o una partición puede resultar mucho más eficaz utilizar una operación de registrada mínimo como [CTAS][] para hacerlo.

En el ejemplo siguiente de una tabla completa la actualización se ha convertido en una `CTAS` para que sea posible registro mínimo.

En este caso carácter retrospectivo estamos agregando un importe de descuento para las ventas de la tabla:

```sql
--Step 01. Create a new table containing the "Update". 
CREATE TABLE [dbo].[FactInternetSales_u]
WITH
(   CLUSTERED INDEX
,   DISTRIBUTION = HASH([ProductKey])
,   PARTITION   (   [OrderDateKey] RANGE RIGHT 
                                    FOR VALUES  (   20000101, 20010101, 20020101, 20030101, 20040101, 20050101
                                                ,   20060101, 20070101, 20080101, 20090101, 20100101, 20110101
                                                ,   20120101, 20130101, 20140101, 20150101, 20160101, 20170101
                                                ,   20180101, 20190101, 20200101, 20210101, 20220101, 20230101
                                                ,   20240101, 20250101, 20260101, 20270101, 20280101, 20290101
                                                )
                )
)
AS 
SELECT
    [ProductKey]  
,   [OrderDateKey] 
,   [DueDateKey]  
,   [ShipDateKey] 
,   [CustomerKey] 
,   [PromotionKey] 
,   [CurrencyKey] 
,   [SalesTerritoryKey]
,   [SalesOrderNumber]
,   [SalesOrderLineNumber]
,   [RevisionNumber]
,   [OrderQuantity]
,   [UnitPrice]
,   [ExtendedAmount]
,   [UnitPriceDiscountPct]
,   ISNULL(CAST(5 as float),0) AS [DiscountAmount]
,   [ProductStandardCost]
,   [TotalProductCost]
,   ISNULL(CAST(CASE WHEN [SalesAmount] <=5 THEN 0
         ELSE [SalesAmount] - 5
         END AS MONEY),0) AS [SalesAmount]
,   [TaxAmt]
,   [Freight]
,   [CarrierTrackingNumber] 
,   [CustomerPONumber]
FROM    [dbo].[FactInternetSales]
OPTION (LABEL = 'CTAS : Update')
;

--Step 02. Rename the tables
RENAME OBJECT [dbo].[FactInternetSales]   TO [FactInternetSales_old];
RENAME OBJECT [dbo].[FactInternetSales_u] TO [FactInternetSales];

--Step 03. Drop the old table
DROP TABLE [dbo].[FactInternetSales_old]
```

> [AZURE.NOTE] Volver a crear tablas de gran tamaño puede beneficiarse de características de administración de carga de trabajo de almacén de datos de SQL. Para obtener más detalles, consulte la sección Administración de carga de trabajo en el artículo de [simultaneidad][] .

## <a name="optimizing-with-partition-switching"></a>Optimizar con el cambio de partición

Para enfrentarse a las modificaciones de gran escala dentro de una [partición de tabla][], una partición de cambio de trama hace una gran cantidad de sentido. Si la modificación de los datos es importante y abarca varias particiones, simplemente iteración las particiones consigue el mismo resultado.

Los pasos para realizar un cambio de partición son los siguientes:
1. Crear una vacía fuera partición
2. Realizar la 'actualización' como un CTAS
3. Cambiar los datos existentes a la tabla de salida
4. Cambiar los datos nuevos
5. Limpiar los datos

Sin embargo, para ayudarle a identificar las particiones para cambiar, primero deberá crear un procedimiento auxiliar como la siguiente. 

```sql
CREATE PROCEDURE dbo.partition_data_get
    @schema_name           NVARCHAR(128)
,   @table_name            NVARCHAR(128)
,   @boundary_value        INT
AS
IF OBJECT_ID('tempdb..#ptn_data') IS NOT NULL
BEGIN
    DROP TABLE #ptn_data
END
CREATE TABLE #ptn_data
WITH    (   DISTRIBUTION = ROUND_ROBIN
        ,   HEAP
        )
AS
WITH CTE
AS
(
SELECT  s.name                          AS [schema_name]
,       t.name                          AS [table_name]
,       p.partition_number              AS [ptn_nmbr]
,       p.[rows]                        AS [ptn_rows]
,       CAST(r.[value] AS INT)          AS [boundary_value]
FROM        sys.schemas                 AS s
JOIN        sys.tables                  AS t    ON  s.[schema_id]       = t.[schema_id]
JOIN        sys.indexes                 AS i    ON  t.[object_id]       = i.[object_id]
JOIN        sys.partitions              AS p    ON  i.[object_id]       = p.[object_id] 
                                                AND i.[index_id]        = p.[index_id] 
JOIN        sys.partition_schemes       AS h    ON  i.[data_space_id]   = h.[data_space_id]
JOIN        sys.partition_functions     AS f    ON  h.[function_id]     = f.[function_id]
LEFT JOIN   sys.partition_range_values  AS r    ON  f.[function_id]     = r.[function_id] 
                                                AND r.[boundary_id]     = p.[partition_number]
WHERE i.[index_id] <= 1
)
SELECT  *
FROM    CTE
WHERE   [schema_name]       = @schema_name
AND     [table_name]        = @table_name
AND     [boundary_value]    = @boundary_value
OPTION (LABEL = 'dbo.partition_data_get : CTAS : #ptn_data')
;
GO
```

Este procedimiento aumenta al máximo reutilización de código y conserva la partición cambiar ejemplo más compacta.

El código siguiente muestra los cinco pasos anteriores para lograr una partición completa cambiar rutina.

```sql
--Create a partitioned aligned empty table to switch out the data 
IF OBJECT_ID('[dbo].[FactInternetSales_out]') IS NOT NULL
BEGIN
    DROP TABLE [dbo].[FactInternetSales_out]
END

CREATE TABLE [dbo].[FactInternetSales_out]
WITH
(   DISTRIBUTION = HASH([ProductKey])
,   CLUSTERED COLUMNSTORE INDEX
,   PARTITION   (   [OrderDateKey] RANGE RIGHT 
                                    FOR VALUES  (   20020101, 20030101
                                                )
                )
)
AS
SELECT *
FROM    [dbo].[FactInternetSales]
WHERE 1=2
OPTION (LABEL = 'CTAS : Partition Switch IN : UPDATE')
;

--Create a partitioned aligned table and update the data in the select portion of the CTAS
IF OBJECT_ID('[dbo].[FactInternetSales_in]') IS NOT NULL
BEGIN
    DROP TABLE [dbo].[FactInternetSales_in]
END

CREATE TABLE [dbo].[FactInternetSales_in]
WITH
(   DISTRIBUTION = HASH([ProductKey])
,   CLUSTERED COLUMNSTORE INDEX
,   PARTITION   (   [OrderDateKey] RANGE RIGHT 
                                    FOR VALUES  (   20020101, 20030101
                                                )
                )
)
AS 
SELECT
    [ProductKey]  
,   [OrderDateKey] 
,   [DueDateKey]  
,   [ShipDateKey] 
,   [CustomerKey] 
,   [PromotionKey] 
,   [CurrencyKey] 
,   [SalesTerritoryKey]
,   [SalesOrderNumber]
,   [SalesOrderLineNumber]
,   [RevisionNumber]
,   [OrderQuantity]
,   [UnitPrice]
,   [ExtendedAmount]
,   [UnitPriceDiscountPct]
,   ISNULL(CAST(5 as float),0) AS [DiscountAmount]
,   [ProductStandardCost]
,   [TotalProductCost]
,   ISNULL(CAST(CASE WHEN [SalesAmount] <=5 THEN 0
         ELSE [SalesAmount] - 5
         END AS MONEY),0) AS [SalesAmount]
,   [TaxAmt]
,   [Freight]
,   [CarrierTrackingNumber] 
,   [CustomerPONumber]
FROM    [dbo].[FactInternetSales]
WHERE   OrderDateKey BETWEEN 20020101 AND 20021231
OPTION (LABEL = 'CTAS : Partition Switch IN : UPDATE')
;

--Use the helper procedure to identify the partitions
--The source table
EXEC dbo.partition_data_get 'dbo','FactInternetSales',20030101
DECLARE @ptn_nmbr_src INT = (SELECT ptn_nmbr FROM #ptn_data)
SELECT @ptn_nmbr_src

--The "in" table
EXEC dbo.partition_data_get 'dbo','FactInternetSales_in',20030101
DECLARE @ptn_nmbr_in INT = (SELECT ptn_nmbr FROM #ptn_data)
SELECT @ptn_nmbr_in

--The "out" table
EXEC dbo.partition_data_get 'dbo','FactInternetSales_out',20030101
DECLARE @ptn_nmbr_out INT = (SELECT ptn_nmbr FROM #ptn_data)
SELECT @ptn_nmbr_out

--Switch the partitions over
DECLARE @SQL NVARCHAR(4000) = '
ALTER TABLE [dbo].[FactInternetSales]   SWITCH PARTITION '+CAST(@ptn_nmbr_src AS VARCHAR(20))   +' TO [dbo].[FactInternetSales_out] PARTITION ' +CAST(@ptn_nmbr_out AS VARCHAR(20))+';
ALTER TABLE [dbo].[FactInternetSales_in] SWITCH PARTITION '+CAST(@ptn_nmbr_in AS VARCHAR(20))   +' TO [dbo].[FactInternetSales] PARTITION '     +CAST(@ptn_nmbr_src AS VARCHAR(20))+';'
EXEC sp_executesql @SQL

--Perform the clean-up
TRUNCATE TABLE dbo.FactInternetSales_out;
TRUNCATE TABLE dbo.FactInternetSales_in;

DROP TABLE dbo.FactInternetSales_out
DROP TABLE dbo.FactInternetSales_in
DROP TABLE #ptn_data
```

## <a name="minimize-logging-with-small-batches"></a>Registro con pequeños lotes de minimizar

Para operaciones de modificación de datos de gran tamaño, podría ser conveniente dividir la operación en fragmentos o lotes para definir el ámbito de la unidad de trabajo.

Un ejemplo de trabajo se proporciona a continuación. El tamaño del lote se estableció en un número importante para resaltar la técnica. En realidad, el tamaño del lote sería significativamente mayor. 

```sql
SET NO_COUNT ON;
IF OBJECT_ID('tempdb..#t') IS NOT NULL
BEGIN
    DROP TABLE #t;
    PRINT '#t dropped';
END

CREATE TABLE #t
WITH    (   DISTRIBUTION = ROUND_ROBIN
        ,   HEAP
        )
AS
SELECT  ROW_NUMBER() OVER(ORDER BY (SELECT NULL)) AS seq_nmbr
,       SalesOrderNumber
,       SalesOrderLineNumber
FROM    dbo.FactInternetSales
WHERE   [OrderDateKey] BETWEEN 20010101 and 20011231
;

DECLARE @seq_start      INT = 1
,       @batch_iterator INT = 1
,       @batch_size     INT = 50
,       @max_seq_nmbr   INT = (SELECT MAX(seq_nmbr) FROM dbo.#t)
;

DECLARE @batch_count    INT = (SELECT CEILING((@max_seq_nmbr*1.0)/@batch_size))
,       @seq_end        INT = @batch_size
;

SELECT COUNT(*)
FROM    dbo.FactInternetSales f

PRINT 'MAX_seq_nmbr '+CAST(@max_seq_nmbr AS VARCHAR(20))
PRINT 'MAX_Batch_count '+CAST(@batch_count AS VARCHAR(20))

WHILE   @batch_iterator <= @batch_count
BEGIN
    DELETE
    FROM    dbo.FactInternetSales
    WHERE EXISTS
    (
            SELECT  1
            FROM    #t t
            WHERE   seq_nmbr BETWEEN  @seq_start AND @seq_end
            AND     FactInternetSales.SalesOrderNumber      = t.SalesOrderNumber
            AND     FactInternetSales.SalesOrderLineNumber  = t.SalesOrderLineNumber
    )
    ;

    SET @seq_start = @seq_end
    SET @seq_end = (@seq_start+@batch_size);
    SET @batch_iterator +=1;
END
```

## <a name="pause-and-scaling-guidance"></a>Pausar y escala orientación

Almacén de datos de SQL Azure le permite pausar, reanudar y ajustar el almacén de datos a petición. Cuando haga una pausa o ajustar el almacén de datos de SQL es importante conocer las transacciones en curso terminan inmediatamente; las transacciones abiertas deshacer la causa. Si la carga de trabajo había emitido una modificación de datos incompleta y de larga ejecución antes de la operación de pausar o escala, este trabajo tendrán que se puede deshacer. Esto puede afectar el tiempo que se tarda en Pausar o ajustar el tamaño de la base de datos de almacén de datos de SQL Azure. 

> [AZURE.IMPORTANT] Ambos `UPDATE` y `DELETE` son operaciones de registro completo y para deshacer o rehacer estas operaciones pueden tardar mucho más de equivalente con un mínimo de operaciones de registro. 

El escenario es permitir en transacciones de modificación de datos de vuelo completos antes de pausa o almacén de datos SQL de ajuste de escala. Sin embargo, esto puede no siempre sea práctico. Para reducir el riesgo de una operación de deshacer largo, tenga en cuenta las siguientes opciones:

- Volver a escribir las operaciones de ejecución larga con [CTAS][]
- La operación se dividen en fragmentos; funcionamiento en un subconjunto de las filas

## <a name="next-steps"></a>Pasos siguientes

Vea [las transacciones en el almacén de datos de SQL][] para obtener más información sobre los niveles de aislamiento y los límites de transacciones.  Para obtener información general de otras prácticas recomendadas, consulte [Prácticas recomendadas de almacén de datos de SQL][].

<!--Image references-->

<!--Article references-->
[Transacciones en el almacén de datos SQL]: ./sql-data-warehouse-develop-transactions.md
[partición de tabla]: ./sql-data-warehouse-tables-partition.md
[Simultaneidad]: ./sql-data-warehouse-develop-concurrency.md
[CTAS]: ./sql-data-warehouse-develop-ctas.md
[Prácticas recomendadas de almacén de datos SQL]: ./sql-data-warehouse-best-practices.md

<!--MSDN references-->
[alter index]:https://msdn.microsoft.com/library/ms188388.aspx
[CAMBIAR EL NOMBRE]: https://msdn.microsoft.com/library/mt631611.aspx

<!-- Other web references -->

