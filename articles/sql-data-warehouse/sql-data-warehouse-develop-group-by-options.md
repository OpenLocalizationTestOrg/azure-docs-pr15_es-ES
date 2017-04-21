<properties
   pageTitle="Grupo de opciones en el almacén de datos de SQL | Microsoft Azure"
   description="Sugerencias para implementar el grupo de opciones en el almacén de datos de SQL Azure para desarrollar soluciones."
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

# <a name="group-by-options-in-sql-data-warehouse"></a>Agrupar por opciones en el almacén de datos de SQL

La cláusula [GROUP BY][] se usa para agregar datos a un conjunto de filas de resumen. También tiene varias opciones que amplían su funcionalidad que necesita para solucionar como no son compatibles directamente al almacén de datos de SQL Azure.

Estas opciones están
- GROUP BY con ROLLUP
- CONJUNTOS DE AGRUPACIÓN
- Agrupar por cubo

## <a name="rollup-and-grouping-sets-options"></a>Opciones de conjuntos de resumen y agrupamiento
A continuación, la opción más sencilla es usar `UNION ALL` en su lugar para realizar el informe en lugar de usar la sintaxis explícita. El resultado es exactamente el mismo

A continuación es un ejemplo de un grupo mediante el uso de la instrucción la `ROLLUP` opción:

```sql
SELECT [SalesTerritoryCountry]
,      [SalesTerritoryRegion]
,      SUM(SalesAmount)             AS TotalSalesAmount
FROM  dbo.factInternetSales s
JOIN  dbo.DimSalesTerritory t       ON s.SalesTerritoryKey       = t.SalesTerritoryKey
GROUP BY ROLLUP (
                        [SalesTerritoryCountry]
                ,       [SalesTerritoryRegion]
                )
;
```

Mediante el uso de ROLLUP hemos solicitado las agregaciones siguientes:
- País y región
- País
- Total general

Para reemplazar este debe usar `UNION ALL`; especificar las agregaciones requerido explícitamente para devolver los mismos resultados:

```sql
SELECT [SalesTerritoryCountry]
,      [SalesTerritoryRegion]
,      SUM(SalesAmount) AS TotalSalesAmount
FROM  dbo.factInternetSales s
JOIN  dbo.DimSalesTerritory t     ON s.SalesTerritoryKey       = t.SalesTerritoryKey
GROUP BY
       [SalesTerritoryCountry]
,      [SalesTerritoryRegion]
UNION ALL
SELECT [SalesTerritoryCountry]
,      NULL
,      SUM(SalesAmount) AS TotalSalesAmount
FROM  dbo.factInternetSales s
JOIN  dbo.DimSalesTerritory t     ON s.SalesTerritoryKey       = t.SalesTerritoryKey
GROUP BY
       [SalesTerritoryCountry]
UNION ALL
SELECT NULL
,      NULL
,      SUM(SalesAmount) AS TotalSalesAmount
FROM  dbo.factInternetSales s
JOIN  dbo.DimSalesTerritory t     ON s.SalesTerritoryKey       = t.SalesTerritoryKey;
```

Para que necesitamos todos los conjuntos de agrupación es adoptar al mismo principal, pero sólo crear unión todas las secciones de los niveles de agregación que desea ver

## <a name="cube-options"></a>Opciones de cubo
Es posible crear un grupo por con cubo mediante el enfoque UNION ALL. El problema es que el código rápidamente puede ser pesado y difícil. Para mitigar esto puede usar esta más avanzada enfoque.

Vamos a usar el ejemplo anterior.

El primer paso es definir el cubo' ' que define todos los niveles de agregación que se va a crear. Es importante tome nota de la combinación cruzada de las dos tablas derivadas. Esto genera todos los niveles para nosotros. El resto del código es realmente allí para dar formato.

```sql
CREATE TABLE #Cube
WITH
(   DISTRIBUTION = ROUND_ROBIN
,   LOCATION = USER_DB
)
AS
WITH GrpCube AS
(SELECT    CAST(ISNULL(Country,'NULL')+','+ISNULL(Region,'NULL') AS NVARCHAR(50)) as 'Cols'
,          CAST(ISNULL(Country+',','')+ISNULL(Region,'') AS NVARCHAR(50))  as 'GroupBy'
,          ROW_NUMBER() OVER (ORDER BY Country) as 'Seq'
FROM       ( SELECT 'SalesTerritoryCountry' as Country
             UNION ALL
             SELECT NULL
           ) c
CROSS JOIN ( SELECT 'SalesTerritoryRegion' as Region
             UNION ALL
             SELECT NULL
           ) r
)
SELECT Cols
,      CASE WHEN SUBSTRING(GroupBy,LEN(GroupBy),1) = ','
            THEN SUBSTRING(GroupBy,1,LEN(GroupBy)-1)
            ELSE GroupBy
       END AS GroupBy  --Remove Trailing Comma
,Seq
FROM GrpCube;
```

Los resultados de la CTAS pueden verse a continuación:

![][1]

El segundo paso consiste en especificar una tabla de destino para almacenar los resultados temporales:

```sql
DECLARE
 @SQL NVARCHAR(4000)
,@Columns NVARCHAR(4000)
,@GroupBy NVARCHAR(4000)
,@i INT = 1
,@nbr INT = 0
;
CREATE TABLE #Results
(
 [SalesTerritoryCountry] NVARCHAR(50)
,[SalesTerritoryRegion]  NVARCHAR(50)
,[TotalSalesAmount]      MONEY
)
WITH
(   DISTRIBUTION = ROUND_ROBIN
,   LOCATION = USER_DB
)
;
```

El tercer paso es recorrer nuestra cubo de realizar la agregación de columnas. La consulta se ejecuta una vez por cada fila de la tabla temporal #Cube y almacenar los resultados de la tabla temporal #Results

```sql
SET @nbr =(SELECT MAX(Seq) FROM #Cube);

WHILE @i<=@nbr
BEGIN
    SET @Columns = (SELECT Cols    FROM #Cube where seq = @i);
    SET @GroupBy = (SELECT GroupBy FROM #Cube where seq = @i);

    SET @SQL ='INSERT INTO #Results
              SELECT '+@Columns+'
              ,      SUM(SalesAmount) AS TotalSalesAmount
              FROM  dbo.factInternetSales s
              JOIN  dbo.DimSalesTerritory t  
              ON s.SalesTerritoryKey = t.SalesTerritoryKey
              '+CASE WHEN @GroupBy <>''
                     THEN 'GROUP BY '+@GroupBy ELSE '' END

    EXEC sp_executesql @SQL;
    SET @i +=1;
END
```

Por último nos podemos devolver los resultados simplemente leyendo de la tabla temporal #Results

```sql
SELECT *
FROM #Results
ORDER BY 1,2,3
;
```

Dividir el código en secciones y generar una construcción de bucle el código se convierte en más fáciles de administrar y mantener.


## <a name="next-steps"></a>Pasos siguientes
Para obtener más sugerencias de desarrollo, vea [Introducción al desarrollo][].

<!--Image references-->
[1]: media/sql-data-warehouse-develop-group-by-options/sql-data-warehouse-develop-group-by-cube.png

<!--Article references-->
[Introducción al desarrollo]: sql-data-warehouse-overview-develop.md

<!--MSDN references-->
[AGRUPAR POR]: https://msdn.microsoft.com/library/ms177673.aspx


<!--Other Web references-->
