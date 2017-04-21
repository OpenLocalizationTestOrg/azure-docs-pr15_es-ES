<properties
   pageTitle="Crear tabla como seleccione (CTAS) en el almacén de datos de SQL | Microsoft Azure"
   description="Sugerencias para codificar con create table como instrucción select de (CTAS) en el almacén de datos de SQL Azure para desarrollar soluciones."
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
   ms.date="06/14/2016"
   ms.author="jrj;barbkess;sonyama"/>

# <a name="create-table-as-select-ctas-in-sql-data-warehouse"></a>Crear tabla como Select (CTAS) en el almacén de datos SQL
Crear tabla como select o `CTAS` es una de las características más importantes de SQL T disponibles. Es una operación totalmente paralelizada que crea una nueva tabla basada en el resultado de una instrucción SELECT. `CTAS`es la manera más simple y rápida de crear una copia de una tabla. Puede considerar que sea una versión sobrealimentada de `SELECT..INTO` si lo desea. Este documento proporciona ejemplos y prácticas recomendadas para `CTAS`.

## <a name="using-ctas-to-copy-a-table"></a>Uso CTAS para copiar una tabla

Quizás uno de los más comunes utiliza de `CTAS` es crear una copia de una tabla para que pueda cambiar el DDL. Si por ejemplo creó originalmente la tabla como `ROUND_ROBIN` y ahora desea cambiarlo a una tabla que se distribuye en una columna, `CTAS` es ¿cómo se cambia la columna de la distribución. `CTAS`También puede utilizarse para cambiar los tipos de particiones, la indización o la columna.

Supongamos que crea esta tabla mediante el tipo de distribución predeterminado de `ROUND_ROBIN` distribuido ya que se especificó ninguna columna de distribución en el `CREATE TABLE`.

```sql
CREATE TABLE FactInternetSales
(
    ProductKey int NOT NULL,
    OrderDateKey int NOT NULL,
    DueDateKey int NOT NULL,
    ShipDateKey int NOT NULL,
    CustomerKey int NOT NULL,
    PromotionKey int NOT NULL,
    CurrencyKey int NOT NULL,
    SalesTerritoryKey int NOT NULL,
    SalesOrderNumber nvarchar(20) NOT NULL,
    SalesOrderLineNumber tinyint NOT NULL,
    RevisionNumber tinyint NOT NULL,
    OrderQuantity smallint NOT NULL,
    UnitPrice money NOT NULL,
    ExtendedAmount money NOT NULL,
    UnitPriceDiscountPct float NOT NULL,
    DiscountAmount float NOT NULL,
    ProductStandardCost money NOT NULL,
    TotalProductCost money NOT NULL,
    SalesAmount money NOT NULL,
    TaxAmt money NOT NULL,
    Freight money NOT NULL,
    CarrierTrackingNumber nvarchar(25),
    CustomerPONumber nvarchar(25)
);
```

Ahora desea crear una nueva copia de esta tabla con un índice de Columnstore agrupado poder aprovechar el rendimiento de las tablas de Columnstore agrupada. También desea distribuir esta tabla en ProductKey ya que son anticipar combinaciones en esta columna y desea evitar movimientos de datos durante las combinaciones en ProductKey. Por último también desee agregar partición en OrderDateKey de modo que puede eliminar rápidamente datos antiguos colocando particiones antiguas. Aquí es la instrucción de CTAS que desea copiar la tabla anterior en una nueva tabla.

```sql
CREATE TABLE FactInternetSales_new
WITH
(
    CLUSTERED COLUMNSTORE INDEX,
    DISTRIBUTION = HASH(ProductKey),
    PARTITION
    (
        OrderDateKey RANGE RIGHT FOR VALUES
        (
        20000101,20010101,20020101,20030101,20040101,20050101,20060101,20070101,20080101,20090101,
        20100101,20110101,20120101,20130101,20140101,20150101,20160101,20170101,20180101,20190101,
        20200101,20210101,20220101,20230101,20240101,20250101,20260101,20270101,20280101,20290101
        )
    )
)
AS SELECT * FROM FactInternetSales;
```

Por último puede cambiar el nombre de las tablas para intercambiar en la nueva tabla y, a continuación, eliminar la tabla anterior.

```sql
RENAME OBJECT FactInternetSales TO FactInternetSales_old;
RENAME OBJECT FactInternetSales_new TO FactInternetSales;

DROP TABLE FactInternetSales_old;
```

> [AZURE.NOTE] Almacén de datos de SQL Azure hace aún no automática de soporte técnico, crear o actualizará las estadísticas automáticamente.  Para obtener el mejor rendimiento de las consultas, es importante que estadísticas crearse en todas las columnas de todas las tablas después de la primera carga o se producen cambios importantes en los datos.  Para obtener una explicación detallada de las estadísticas, vea el tema de [estadísticas][] en el grupo de desarrollo de temas.

## <a name="using-ctas-to-work-around-unsupported-features"></a>Usar CTAS para evitar las características no compatibles

`CTAS`También puede usarse para solucionar un número de las características no compatibles que se muestran a continuación. Esto puede suelen para ser una situación win/win como no sólo su código serán compatible, pero a menudo se ejecutará más rápidamente en el almacén de datos de SQL. Se trata como resultado de su diseño completamente paralelizada. Escenarios que pueden trabajar con CTAS alrededor incluyen:

- SELECCIONE... EN
- COMBINACIONES de ANSI en actualizaciones
- Combinaciones de ANSI en eliminaciones
- Instrucción MERGE

> [AZURE.NOTE] Intente pensar "CTAS primera". Si cree que puede resolver un problema usando `CTAS` , a continuación, que suele ser la mejor manera de enfocar, incluso si escribe más datos como resultado.
>

## <a name="selectinto"></a>SELECCIONE... EN
Puede encontrarse con `SELECT..INTO` aparece en un número de sitios en la solución.

A continuación es un ejemplo de un `SELECT..INTO` instrucción:

```sql
SELECT *
INTO    #tmp_fct
FROM    [dbo].[FactInternetSales]
```

Para convertir las respuestas anteriores a `CTAS` es bastante directa:

```sql
CREATE TABLE #tmp_fct
WITH
(
    DISTRIBUTION = ROUND_ROBIN
)
AS
SELECT  *
FROM    [dbo].[FactInternetSales]
;
```

> [AZURE.NOTE] CTAS actualmente requiere que especificar una columna de distribución.  Si no está intentando deliberadamente cambiar la columna de la distribución, su `CTAS` realizará mayor rapidez si selecciona una columna de distribución que es idéntica a la tabla subyacente como esta estrategia evita el movimiento de datos.  Si va a crear una tabla pequeña donde rendimiento no es un factor, a continuación, puede especificar `ROUND_ROBIN` para evitar tener que decida en una columna de distribución.

## <a name="ansi-join-replacement-for-update-statements"></a>Reemplazo de la combinación de ANSI para las instrucciones de actualización

Es posible que tenga una actualización compleja que se unan a más de dos tablas de manera conjunta con unirse a la sintaxis de ANSI para realizar la actualización o eliminación.

Imagine que contenía para actualizar la tabla:

```sql
CREATE TABLE [dbo].[AnnualCategorySales]
(   [EnglishProductCategoryName]    NVARCHAR(50)    NOT NULL
,   [CalendarYear]                  SMALLINT        NOT NULL
,   [TotalSalesAmount]              MONEY           NOT NULL
)
WITH
(
    DISTRIBUTION = ROUND_ROBIN
)
;
```

La consulta original podría haber optimizado algo parecido a esto:

```sql
UPDATE  acs
SET     [TotalSalesAmount] = [fis].[TotalSalesAmount]
FROM    [dbo].[AnnualCategorySales]     AS acs
JOIN    (
        SELECT  [EnglishProductCategoryName]
        ,       [CalendarYear]
        ,       SUM([SalesAmount])              AS [TotalSalesAmount]
        FROM    [dbo].[FactInternetSales]       AS s
        JOIN    [dbo].[DimDate]                 AS d    ON s.[OrderDateKey]             = d.[DateKey]
        JOIN    [dbo].[DimProduct]              AS p    ON s.[ProductKey]               = p.[ProductKey]
        JOIN    [dbo].[DimProductSubCategory]   AS u    ON p.[ProductSubcategoryKey]    = u.[ProductSubcategoryKey]
        JOIN    [dbo].[DimProductCategory]      AS c    ON u.[ProductCategoryKey]       = c.[ProductCategoryKey]
        WHERE   [CalendarYear] = 2004
        GROUP BY
                [EnglishProductCategoryName]
        ,       [CalendarYear]
        ) AS fis
ON  [acs].[EnglishProductCategoryName]  = [fis].[EnglishProductCategoryName]
AND [acs].[CalendarYear]                = [fis].[CalendarYear]
;
```

Ya no admite el almacenamiento de datos SQL ANSI uniones en el `FROM` cláusula de un `UPDATE` instrucción, no puede copiar este código sobre sin cambiarlo ligeramente.

Puede usar una combinación de un `CTAS` y una combinación implícita para reemplazar este código:

```sql
-- Create an interim table
CREATE TABLE CTAS_acs
WITH (DISTRIBUTION = ROUND_ROBIN)
AS
SELECT  ISNULL(CAST([EnglishProductCategoryName] AS NVARCHAR(50)),0)    AS [EnglishProductCategoryName]
,       ISNULL(CAST([CalendarYear] AS SMALLINT),0)                      AS [CalendarYear]
,       ISNULL(CAST(SUM([SalesAmount]) AS MONEY),0)                     AS [TotalSalesAmount]
FROM    [dbo].[FactInternetSales]       AS s
JOIN    [dbo].[DimDate]                 AS d    ON s.[OrderDateKey]             = d.[DateKey]
JOIN    [dbo].[DimProduct]              AS p    ON s.[ProductKey]               = p.[ProductKey]
JOIN    [dbo].[DimProductSubCategory]   AS u    ON p.[ProductSubcategoryKey]    = u.[ProductSubcategoryKey]
JOIN    [dbo].[DimProductCategory]      AS c    ON u.[ProductCategoryKey]       = c.[ProductCategoryKey]
WHERE   [CalendarYear] = 2004
GROUP BY
        [EnglishProductCategoryName]
,       [CalendarYear]
;

-- Use an implicit join to perform the update
UPDATE  AnnualCategorySales
SET     AnnualCategorySales.TotalSalesAmount = CTAS_ACS.TotalSalesAmount
FROM    CTAS_acs
WHERE   CTAS_acs.[EnglishProductCategoryName] = AnnualCategorySales.[EnglishProductCategoryName]
AND     CTAS_acs.[CalendarYear]               = AnnualCategorySales.[CalendarYear]
;

--Drop the interim table
DROP TABLE CTAS_acs
;
```

## <a name="ansi-join-replacement-for-delete-statements"></a>Reemplazo de la combinación de ANSI para eliminar instrucciones
A veces es usar el mejor método para eliminar los datos `CTAS`. En lugar de eliminar los datos simplemente seleccione los datos que desea conservar. Este especialmente cierto para `DELETE` instrucciones que utilizan ansi sintaxis unión desde el almacén de datos SQL no admite ANSI uniones en el `FROM` cláusula de un `DELETE` instrucción.

Un ejemplo de una instrucción DELETE convertida está disponible a continuación:

```sql
CREATE TABLE dbo.DimProduct_upsert
WITH
(   Distribution=HASH(ProductKey)
,   CLUSTERED INDEX (ProductKey)
)
AS -- Select Data you wish to keep
SELECT     p.ProductKey
,          p.EnglishProductName
,          p.Color
FROM       dbo.DimProduct p
RIGHT JOIN dbo.stg_DimProduct s
ON         p.ProductKey = s.ProductKey
;

RENAME OBJECT dbo.DimProduct        TO DimProduct_old;
RENAME OBJECT dbo.DimProduct_upsert TO DimProduct;
```

## <a name="replace-merge-statements"></a>Reemplazar instrucciones de combinación
Instrucciones de combinación se pueden reemplazar, al menos en parte, usando `CTAS`. Puede consolidar la `INSERT` y la `UPDATE` en una sola instrucción. Cualquier registro eliminado tendría que ser cerrado en una segunda instrucción.

Un ejemplo de un `UPSERT` está disponible a continuación:

```sql
CREATE TABLE dbo.[DimProduct_upsert]
WITH
(   DISTRIBUTION = HASH([ProductKey])
,   CLUSTERED INDEX ([ProductKey])
)
AS
-- New rows and new versions of rows
SELECT      s.[ProductKey]
,           s.[EnglishProductName]
,           s.[Color]
FROM      dbo.[stg_DimProduct] AS s
UNION ALL  
-- Keep rows that are not being touched
SELECT      p.[ProductKey]
,           p.[EnglishProductName]
,           p.[Color]
FROM      dbo.[DimProduct] AS p
WHERE NOT EXISTS
(   SELECT  *
    FROM    [dbo].[stg_DimProduct] s
    WHERE   s.[ProductKey] = p.[ProductKey]
)
;

RENAME OBJECT dbo.[DimProduct]          TO [DimProduct_old];
RENAME OBJECT dbo.[DimpProduct_upsert]  TO [DimProduct];

```

## <a name="ctas-recommendation-explicitly-state-data-type-and-nullability-of-output"></a>Recomendación CTAS: indicar explícitamente el tipo de datos y la aceptación de salida

Durante la migración de código, es posible que ejecutar a través de este tipo de modelo de codificación:

```sql
DECLARE @d decimal(7,2) = 85.455
,       @f float(24)    = 85.455

CREATE TABLE result
(result DECIMAL(7,2) NOT NULL
)
WITH (DISTRIBUTION = ROUND_ROBIN)

INSERT INTO result
SELECT @d*@f
;
```

Instintivamente imagina debe migrar este código a un CTAS y sería correcta. Sin embargo, hay un problema oculto aquí.

El siguiente código no produce el mismo resultado:

```sql
DECLARE @d decimal(7,2) = 85.455
,       @f float(24)    = 85.455
;

CREATE TABLE ctas_r
WITH (DISTRIBUTION = ROUND_ROBIN)
AS
SELECT @d*@f as result
;
```

Observe que la columna "resultado" lleva hacia delante los valores de aceptación y tipo de datos de la expresión. Si no tiene cuidado Esto puede provocar sutiles variaciones en valores.

Intente lo siguiente como ejemplo:

```sql
SELECT result,result*@d
from result
;

SELECT result,result*@d
from ctas_r
;
```

El valor almacenado para el resultado es diferente. Como el valor almacenado en la columna resultado se utiliza en otras expresiones el error es aún más significativo.

![][1]

Esto es especialmente importante para las migraciones de datos. Aunque la segunda consulta es posiblemente más precisa, hay un problema. Los datos son diferentes en comparación con el sistema de origen y que lleva a las preguntas de la integridad de la migración. Es uno de esos casos donde la respuesta "incorrecta" es realmente la adecuada.

El motivo que vemos que esta diferencia entre los dos resultados es hacia abajo hasta la conversión de tipos implícita. En el primer ejemplo la tabla define la definición de columna. Cuando se inserta la fila se produce una conversión de tipo implícita. En el segundo ejemplo no hay ninguna conversión de tipo implícita como la expresión define el tipo de datos de la columna. Observe también que la columna en el segundo ejemplo se ha definido como una columna NULLable mientras que en el primer ejemplo no tiene. Cuando se creó la tabla en la primera aceptación de columna de ejemplo se definió explícitamente. En el segundo ejemplo que simplemente estaba a la expresión y predeterminada esto daría como resultado una definición de NULL.  

Para resolver estos problemas debe establecer explícitamente la conversión de tipo y la aceptación de la `SELECT` parte de la `CTAS` instrucción. No puede establecer estas propiedades en el elemento de tabla de crear.

El ejemplo siguiente muestra cómo solucionar el código:

```sql
DECLARE @d decimal(7,2) = 85.455
,       @f float(24)    = 85.455

CREATE TABLE ctas_r
WITH (DISTRIBUTION = ROUND_ROBIN)
AS
SELECT ISNULL(CAST(@d*@f AS DECIMAL(7,2)),0) as result
```

Tenga en cuenta lo siguiente:
- CONVERSIÓN o convertir podría haber utilizado
- ISNULL se utiliza para exigir la aceptación no combinación
- ISNULL es la función más visibles
- La segunda parte de la ISNULL es una constante, es decir, 0

> [AZURE.NOTE] Los valores NULL se establezca correctamente, es fundamental usar `ISNULL` y no `COALESCE`. `COALESCE`no es una función determinista y así el resultado de la expresión siempre será NULLable. `ISNULL`es diferente. Es determinante. Por lo tanto, cuando la segunda parte de la `ISNULL` función es una constante o un literal, el valor resultante será no nulo.

Esta sugerencia no es solo es útil para garantizar la integridad de los cálculos. También es importante para cambiar de partición de tabla. Imagine que tiene esta tabla definida como el hecho de:

```sql
CREATE TABLE [dbo].[Sales]
(
    [date]      INT     NOT NULL
,   [product]   INT     NOT NULL
,   [store]     INT     NOT NULL
,   [quantity]  INT     NOT NULL
,   [price]     MONEY   NOT NULL
,   [amount]    MONEY   NOT NULL
)
WITH
(   DISTRIBUTION = HASH([product])
,   PARTITION   (   [date] RANGE RIGHT FOR VALUES
                    (20000101,20010101,20020101
                    ,20030101,20040101,20050101
                    )
                )
)
;
```

Sin embargo, el campo de valor es una expresión calculada no forma parte del origen de datos.

Para crear el conjunto de datos dividida que desea haga lo siguiente:

```sql
CREATE TABLE [dbo].[Sales_in]
WITH    
(   DISTRIBUTION = HASH([product])
,   PARTITION   (   [date] RANGE RIGHT FOR VALUES
                    (20000101,20010101
                    )
                )
)
AS
SELECT
    [date]    
,   [product]
,   [store]
,   [quantity]
,   [price]   
,   [quantity]*[price]  AS [amount]
FROM [stg].[source]
OPTION (LABEL = 'CTAS : Partition IN table : Create')
;
```

La consulta se ejecuta perfectamente. El problema se produce al intentar realizar el cambio de partición. No coincidan con las definiciones de tabla. Para hacer que las definiciones de tabla que coincida con el CTAS debe modificarse.

```sql
CREATE TABLE [dbo].[Sales_in]
WITH    
(   DISTRIBUTION = HASH([product])
,   PARTITION   (   [date] RANGE RIGHT FOR VALUES
                    (20000101,20010101
                    )
                )
)
AS
SELECT
    [date]    
,   [product]
,   [store]
,   [quantity]
,   [price]   
,   ISNULL(CAST([quantity]*[price] AS MONEY),0) AS [amount]
FROM [stg].[source]
OPTION (LABEL = 'CTAS : Partition IN table : Create');
```

Por lo tanto, puede ver que coherencia de tipo y el mantenimiento de propiedades de aceptación en un CTAS es una buena práctica recomendada ingeniería. Le ayuda a mantener la integridad de los cálculos y también se asegura de que el cambio de partición es posible.

Consulte MSDN para obtener más información sobre el uso de [CTAS][]. Es una de las instrucciones más importantes en el almacén de datos de SQL Azure. Asegúrese de que comprende perfectamente.

## <a name="next-steps"></a>Pasos siguientes
Para obtener más sugerencias de desarrollo, vea [Introducción al desarrollo][].

<!--Image references-->
[1]: media/sql-data-warehouse-develop-ctas/ctas-results.png

<!--Article references-->
[Introducción al desarrollo]: sql-data-warehouse-overview-develop.md
[Estadísticas]: ./sql-data-warehouse-tables-statistics.md

<!--MSDN references-->
[CTAS]: https://msdn.microsoft.com/library/mt204041.aspx

<!--Other Web references-->
