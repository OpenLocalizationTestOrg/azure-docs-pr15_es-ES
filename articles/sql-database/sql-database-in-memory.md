<properties
    pageTitle="SQL en memoria, introducción | Microsoft Azure"
    description="Tecnologías de SQL en memoria mejoran el rendimiento de transacciones y las cargas de trabajo de análisis. Aprenda a aprovechar las ventajas de estas tecnologías."
    services="sql-database"
    documentationCenter=""
    authors="jodebrui"
    manager="jhubbard"
    editor=""/>


<tags
    ms.service="sql-database"
    ms.workload="data-management"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/19/2016"
    ms.author="jodebrui"/>


# <a name="get-started-with-in-memory-preview-in-sql-database"></a>Introducción a en memoria (Preview) en la base de datos SQL

Características de memoria mejoran el rendimiento de transacciones y las cargas de trabajo de análisis en las situaciones derecha.

Este tema resalta dos demostraciones, uno para OLTP en memoria y otro para el análisis en memoria. Cada demostración incluye los pasos y el código que necesarias para ejecutar la demostración. Puede:

- Usar el código para probar variaciones para ver las diferencias en los resultados de rendimiento; o
- Lea el código para comprender el escenario y para ver cómo crear y utilizar los objetos en memoria.

> [AZURE.VIDEO azure-sql-database-in-memory-technologies]

- [1 Inicio rápido: en memoria OLTP tecnologías más rápido T SQL rendimiento](http://msdn.microsoft.com/library/mt694156.aspx) -es otro artículo que le ayudarán a empezar.

#### <a name="in-memory-oltp"></a>OLTP en memoria

Las características de en memoria [OLTP](#install_oltp_manuallink) (procesamiento de transacciones en línea) son:

- Tablas de memoria optimizada.
- Compilados de forma nativa procedimientos almacenados.


Una tabla de memoria optimizada tiene una representación de sí misma en la memoria activa, además de la representación estándar en una unidad de disco dura. Transacciones de la empresa en la tabla se ejecutan más rápidamente debido a que interactúan directamente con únicamente la representación que está en la memoria activa.

Con OLTP en memoria, puede lograr hacia arriba para tener 30 veces en el rendimiento de las transacciones, dependiendo de las características de la carga de trabajo.


Procedimientos almacenados compilados de forma nativa requieren menos instrucciones máquina durante el tiempo de ejecución de interpretarse tradicional procedimientos almacenan. Resultado de compilación nativa hemos visto en las duraciones de 1/100 de la duración de interpretar.


#### <a name="in-memory-analytics"></a>Análisis en memoria 

La característica de [análisis](#install_analytics_manuallink) de en memoria es:

Índices ColumnStore mejoran el rendimiento de análisis y consultas de informes. 


#### <a name="real-time-analytics"></a>Análisis en tiempo real

Para [El análisis en tiempo real](http://msdn.microsoft.com/library/dn817827.aspx) combinar OLTP en memoria y análisis para obtener:

- Información de negocios en tiempo real basado en datos de operaciones.


#### <a name="availability"></a>Disponibilidad


GA, disponibilidad General:

- [Índices Columnstore](http://msdn.microsoft.com/library/dn817827.aspx) que están *en el disco*.


Vista previa:

- OLTP en memoria
- Análisis de las operaciones en tiempo real


Consideraciones mientras las características en memoria están en vista preliminar son se describe [más adelante en este tema](#preview_considerations_for_in_memory).


> [AZURE.NOTE] Estas características de vista previa están disponibles solo para bases de datos de [*Premium*](sql-database-service-tiers.md) no en grupos elásticos y no está disponible para las bases de datos básico o estándar.  Soporte para bases de datos de Premium en grupos elásticos estará disponible próximamente. 


<a id="install_oltp_manuallink" name="install_oltp_manuallink"></a>

&nbsp;

## <a name="a-install-the-in-memory-oltp-sample"></a>A. Instalar el ejemplo OLTP en memoria

Puede crear la [V12] de la base de datos de ejemplo con unos pocos clics en el [portal de Azure](https://portal.azure.com/). A continuación, en esta sección se explica cómo puede mejorar la base de datos AdventureWorksLT con:

- Tablas en memoria.
- Un procedimiento almacenado de forma nativa.


#### <a name="installation-steps"></a>Pasos de instalación

1. En el [portal de Azure](https://portal.azure.com/), crear una base de datos Premium en un servidor V12. Establecer el **origen de** la base de datos de ejemplo AdventureWorksLT [V12].
 - Para obtener instrucciones detalladas, puede ver [crear la primera base de datos de SQL Azure](sql-database-get-started.md).

2. Conectarse a la base de datos con SQL Server Management Studio [(SSMS.exe)](http://msdn.microsoft.com/library/mt238290.aspx).

3. Copie el [script en memoria OLTP Transact-SQL](https://raw.githubusercontent.com/Microsoft/sql-server-samples/master/samples/features/in-memory/t-sql-scripts/sql_in-memory_oltp_sample.sql) en el Portapapeles.
 - La secuencia de comandos SQL T crea los objetos necesarios en la memoria en la base de datos de ejemplo que creó en el paso 1.

4. Pegue el script SQL T SSMS y, a continuación, ejecute la secuencia de comandos.
 - Es fundamental la `MEMORY_OPTIMIZED = ON` instrucciones CREATE TABLE, cláusula, como en:


```
CREATE TABLE [SalesLT].[SalesOrderHeader_inmem](
    [SalesOrderID] int IDENTITY NOT NULL PRIMARY KEY NONCLUSTERED ...,
    ...
) WITH (MEMORY_OPTIMIZED = ON);
```


#### <a name="error-40536"></a>Error 40536


Si obtiene el error 40536 cuando se ejecuta la secuencia de comandos SQL T, ejecute el siguiente script de SQL T para comprobar si la base de datos admite en memoria:


```
SELECT DatabasePropertyEx(DB_Name(), 'IsXTPSupported');
```


Un resultado de **0** indica que no se admite en memoria y 1 significa que es compatible. Para diagnosticar el problema:

- Asegúrese de que se creó la base de datos después de que las características de OLTP en memoria se ha activado para la vista previa.
- Asegúrese de que la base de datos en el nivel de servicio Premium.


#### <a name="about-the-created-memory-optimized-items"></a>Acerca de los elementos creados optimizado de memoria

**Tablas**: los datos de ejemplo contiene optimizado de memoria en las tablas siguientes:

- SalesLT.Product_inmem
- SalesLT.SalesOrderHeader_inmem
- SalesLT.SalesOrderDetail_inmem
- Demo.DemoSalesOrderHeaderSeed
- Demo.DemoSalesOrderDetailSeed


Puede inspeccionar optimizar memoria tablas a través del **Explorador de objetos** en SSMS por:

- Haga clic en **tablas** > **filtro** > **Configuración de filtro** > **Está optimizado de memoria** es igual a 1.


O bien, puede consultar las vistas de catálogo como:


```
SELECT is_memory_optimized, name, type_desc, durability_desc
    FROM sys.tables
    WHERE is_memory_optimized = 1;
```


**Procedimiento almacenado compilado de forma nativa**: puede inspeccionar SalesLT.usp_InsertSalesOrder_inmem a través de una consulta de vista del catálogo:


```
SELECT uses_native_compilation, OBJECT_NAME(object_id), definition
    FROM sys.sql_modules
    WHERE uses_native_compilation = 1;
```


&nbsp;

## <a name="run-the-sample-oltp-workload"></a>Ejecutar la carga de trabajo OLTP de ejemplo

La única diferencia entre los siguientes *procedimientos almacenados* del dos es que el primer procedimiento versiones de las tablas optimizadas de memoria, mientras que el segundo procedimiento utiliza las tablas en disco regulares:

- SalesLT**.** usp_InsertSalesOrder**_inmem**
- SalesLT**.** usp_InsertSalesOrder**_ondisk**


En esta sección, vea cómo usar la utilidad de práctica **ostress.exe** para ejecutar los dos procedimientos almacenados en niveles estresantes. Puede comparar cuánto tiempo tarda se ejecuta el dos esfuerzo para completar.


Cuando se ejecuta ostress.exe, le recomendamos que pasar valores de parámetro diseñados para ambos:

- Ejecutar un gran número de conexiones simultáneas, mediante - n100.
- Tiene un bucle de cada conexión cientos de veces, por uso - r500.


Sin embargo, es recomendable empezar con cuánta valores más pequeños como - n10 y - r50 para garantizar la todo lo que está trabajando.


### <a name="script-for-ostressexe"></a>Secuencia de comandos de ostress.exe


Esta sección muestra la secuencia de comandos T SQL incrustada en la línea de comandos ostress.exe. La secuencia de comandos usa elementos creados por la secuencia de comandos de SQL T instalados anteriormente.


La siguiente secuencia de comandos inserta un pedido de venta de ejemplo con cinco elementos de línea en memoria optimizados en las siguientes *tablas*:

- SalesLT.SalesOrderHeader_inmem
- SalesLT.SalesOrderDetail_inmem


```
DECLARE
    @i int = 0,
    @od SalesLT.SalesOrderDetailType_inmem,
    @SalesOrderID int,
    @DueDate datetime2 = sysdatetime(),
    @CustomerID int = rand() * 8000,
    @BillToAddressID int = rand() * 10000,
    @ShipToAddressID int = rand() * 10000;
    
INSERT INTO @od
    SELECT OrderQty, ProductID
    FROM Demo.DemoSalesOrderDetailSeed
    WHERE OrderID= cast((rand()*60) as int);
    
WHILE (@i < 20)
begin;
    EXECUTE SalesLT.usp_InsertSalesOrder_inmem @SalesOrderID OUTPUT,
        @DueDate, @CustomerID, @BillToAddressID, @ShipToAddressID, @od;
    SET @i = @i + 1;
end
```


Para hacer que la versión de _ondisk de la anterior T-SQL para ostress.exe, simplemente ¿reemplazar las apariciones de la subcadena *_inmem* con *_ondisk*. Estos reemplaza afectan a los nombres de tablas y procedimientos almacenados.


### <a name="install-rml-utilities-and-ostress"></a>Instalar ostress y utilidades RML


Lo ideal es que se desea ejecutar ostress.exe en una máquina virtual de Azure. Se crea una [Máquina Virtual de Azure](https://azure.microsoft.com/documentation/services/virtual-machines/) en la misma región geográfica Azure donde reside la base de datos AdventureWorksLT. Pero puede ejecutar ostress.exe en su ordenador portátil en su lugar.


En la máquina virtual o en cualquier cosa hospedar elija, instale las herramientas de lenguaje de marcado de reproducción (RML), que incluyen ostress.exe.

- Vea la discusión ostress.exe en la [Base de datos de muestra para OLTP en memoria](http://msdn.microsoft.com/library/mt465764.aspx).
 - O consulte la [Base de datos de muestra para OLTP en memoria](http://msdn.microsoft.com/library/mt465764.aspx).
 - O consulte [Blog para instalar ostress.exe](http://blogs.msdn.com/b/psssql/archive/2013/10/29/cumulative-update-2-to-the-rml-utilities-for-microsoft-sql-server-released.aspx)



<!--
dn511655.aspx is for SQL 2014,
[Extensions to AdventureWorks to Demonstrate In-Memory OLTP]
(http://msdn.microsoft.com/library/dn511655&#x28;v=sql.120&#x29;.aspx)

whereas for SQL 2016+
[Sample Database for In-Memory OLTP]
(http://msdn.microsoft.com/library/mt465764.aspx)
-->



### <a name="run-the-inmem-stress-workload-first"></a>Ejecutar la carga de trabajo de carga de _inmem primero


Puede usar una ventana de *Símbolo del sistema de RML Cmd* para ejecutar la línea de comandos ostress.exe. Los parámetros de la línea de comandos directos ostress para:

- Ejecutar 100 conexiones simultáneamente (-n100).
- Tiene cada conexión al ejecutar la secuencia de comandos SQL T 50 veces (-r50).


```
ostress.exe -n100 -r50 -S<servername>.database.windows.net -U<login> -P<password> -d<database> -q -Q"DECLARE @i int = 0, @od SalesLT.SalesOrderDetailType_inmem, @SalesOrderID int, @DueDate datetime2 = sysdatetime(), @CustomerID int = rand() * 8000, @BillToAddressID int = rand() * 10000, @ShipToAddressID int = rand()* 10000; INSERT INTO @od SELECT OrderQty, ProductID FROM Demo.DemoSalesOrderDetailSeed WHERE OrderID= cast((rand()*60) as int); WHILE (@i < 20) begin; EXECUTE SalesLT.usp_InsertSalesOrder_inmem @SalesOrderID OUTPUT, @DueDate, @CustomerID, @BillToAddressID, @ShipToAddressID, @od; set @i += 1; end"
```


Para ejecutar la línea de comandos de ostress.exe anterior:


1. Restablecer el contenido de datos de la base de datos ejecutando el siguiente comando de SSMS, para eliminar todos los datos que se insertaron cualquier ejecuciones anterior:
```
EXECUTE Demo.usp_DemoReset;
```

2. Copiar el texto de la línea de comandos de ostress.exe anterior en el Portapapeles.

3. Reemplazar el `<placeholders>` para los parámetros -S - U -P -d con los valores reales correctos.

4. Ejecutar la línea de comandos modificada en una ventana RML Cmd.


#### <a name="result-is-a-duration"></a>Resultado es una duración


Cuando se complete la ostress.exe, escribe la duración de ejecución como su última línea de salida en la ventana RML Cmd. Por ejemplo, la ejecución de una prueba más corto pasado aproximadamente 1,5 minutos:

`11/12/15 00:35:00.873 [0x000030A8] OSTRESS exiting normally, elapsed time: 00:01:31.867`


#### <a name="reset-edit-for-ondisk-then-rerun"></a>Restablecer, editar para _ondisk, a continuación, vuelva a ejecutar


Una vez que el resultado de la ejecución de _inmem, siga estos pasos para la _ondisk ejecutar:


1. Restablecer la base de datos ejecutando el siguiente comando de SSMS, para eliminar todos los datos que se insertaron la ejecución anterior:
```
EXECUTE Demo.usp_DemoReset;
```

2. Editar la línea de comandos ostress.exe para reemplazar todas las *_inmem* con *_ondisk*.

3. Vuelva a ejecutar ostress.exe por segunda vez y capturar el resultado de duración.

4. Restablecer nuevamente la base de datos para su eliminación responsable de lo que puede ser una gran cantidad de datos de prueba.


#### <a name="expected-comparison-results"></a>Resultados de la comparación esperada

Nuestras pruebas en memoria han mostrado una mejora del rendimiento de **9 veces** para esta carga de trabajo simple con ostress que se ejecuta en una máquina virtual de Azure en la misma región Azure como la base de datos.



<a id="install_analytics_manuallink" name="install_analytics_manuallink"></a>

&nbsp;


## <a name="b-install-the-in-memory-analytics-sample"></a>B. Instalar la muestra de análisis en memoria


En esta sección, comparar los resultados de IO y estadísticas cuando se usa un índice columnstore frente a un índice de árbol b tradicional.


Para el análisis en tiempo real en una carga de trabajo OLTP, a menudo es mejor usar un índice no agrupado columnstore. Para obtener información detallada, vea [Columnstore índices descritos](http://msdn.microsoft.com/library/gg492088.aspx).



### <a name="prepare-the-columnstore-analytics-test"></a>Preparar la prueba de análisis columnstore


1. Usar el portal de Azure para crear una nueva base de datos de AdventureWorksLT de la muestra.
 - Usar ese nombre exacto.
 - Elija cualquier nivel de servicio Premium.

2. Copie la [sql_in memory_analytics_sample](https://raw.githubusercontent.com/Microsoft/sql-server-samples/master/samples/features/in-memory/t-sql-scripts/sql_in-memory_analytics_sample.sql) al Portapapeles.
 - La secuencia de comandos SQL T crea los objetos necesarios en la memoria en la base de datos de ejemplo que creó en el paso 1.
 - El script crea la tabla de dimensiones y dos tablas de hechos. Las tablas de hechos se rellenan con 3,5 millones filas.
 - La secuencia de comandos puede tardar 15 minutos en completarse.

3. Pegue el script SQL T SSMS y, a continuación, ejecute la secuencia de comandos.
 - Crucial es la palabra clave **COLUMNSTORE** en una instrucción **CREATE INDEX** , como en:<br/>`CREATE NONCLUSTERED COLUMNSTORE INDEX ...;`

4. Establecer AdventureWorksLT en el nivel de compatibilidad 130:<br/>`ALTER DATABASE AdventureworksLT SET compatibility_level = 130;`
 - Nivel de 130 no está relacionado directamente a las características en memoria. Pero nivel 130 generalmente proporciona más rápido rendimiento de la consulta que 120.


#### <a name="crucial-tables-and-columnstore-indexes"></a>Tablas cruciales y columnstore índices


- dbo. FactResellerSalesXL_CCI es una tabla que tiene un índice agrupado **columnstore** , que ha avanzado compresión en el nivel de *datos* .

- dbo. FactResellerSalesXL_PageCompressed es una tabla que tiene un equivalente índice normal agrupado se comprime sólo en el nivel de *página* .


#### <a name="crucial-queries-to-compare-the-columnstore-index"></a>Consultas fundamentales para comparar el índice columnstore


[Aquí](https://raw.githubusercontent.com/Microsoft/sql-server-samples/master/samples/features/in-memory/t-sql-scripts/clustered_columnstore_sample_queries.sql) varios tipos de consulta SQL T puede ejecutar para ver las mejoras de rendimiento. En el paso 2, en la secuencia de comandos SQL T, hay un par de consultas que sean de interés directo. Las dos consultas diferencian sólo en una sola línea:


- `FROM FactResellerSalesXL_PageCompressed a`
- `FROM FactResellerSalesXL_CCI a`


Un índice agrupado columnstore se encuentra en la tabla**_CCI** FactResellerSalesXL.

El siguiente fragmento de secuencia de comandos de SQL T imprime estadísticas de IO y la hora de la consulta de cada tabla.


```
/*********************************************************************
Step 2 -- Overview
-- Page Compressed BTree table v/s Columnstore table performance differences
-- Enable actual Query Plan in order to see Plan differences when Executing
*/
-- Ensure Database is in 130 compatibility mode
ALTER DATABASE AdventureworksLT SET compatibility_level = 130
GO

-- Execute a typical query that joins the Fact Table with dimension tables
-- Note this query will run on the Page Compressed table, Note down the time
SET STATISTICS IO ON
SET STATISTICS TIME ON
GO

SELECT c.Year
    ,e.ProductCategoryKey
    ,FirstName + ' ' + LastName AS FullName
    ,count(SalesOrderNumber) AS NumSales
    ,sum(SalesAmount) AS TotalSalesAmt
    ,Avg(SalesAmount) AS AvgSalesAmt
    ,count(DISTINCT SalesOrderNumber) AS NumOrders
    ,count(DISTINCT a.CustomerKey) AS CountCustomers
FROM FactResellerSalesXL_PageCompressed a
INNER JOIN DimProduct b ON b.ProductKey = a.ProductKey
INNER JOIN DimCustomer d ON d.CustomerKey = a.CustomerKey
Inner JOIN DimProductSubCategory e on e.ProductSubcategoryKey = b.ProductSubcategoryKey
INNER JOIN DimDate c ON c.DateKey = a.OrderDateKey
WHERE e.ProductCategoryKey =2
    AND c.FullDateAlternateKey BETWEEN '1/1/2014' AND '1/1/2015'
GROUP BY e.ProductCategoryKey,c.Year,d.CustomerKey,d.FirstName,d.LastName
GO
SET STATISTICS IO OFF
SET STATISTICS TIME OFF
GO


-- This is the same Prior query on a table with a Clustered Columnstore index CCI 
-- The comparison numbers are even more dramatic the larger the table is, this is a 11 million row table only.
SET STATISTICS IO ON
SET STATISTICS TIME ON
GO
SELECT c.Year
    ,e.ProductCategoryKey
    ,FirstName + ' ' + LastName AS FullName
    ,count(SalesOrderNumber) AS NumSales
    ,sum(SalesAmount) AS TotalSalesAmt
    ,Avg(SalesAmount) AS AvgSalesAmt
    ,count(DISTINCT SalesOrderNumber) AS NumOrders
    ,count(DISTINCT a.CustomerKey) AS CountCustomers
FROM FactResellerSalesXL_CCI a
INNER JOIN DimProduct b ON b.ProductKey = a.ProductKey
INNER JOIN DimCustomer d ON d.CustomerKey = a.CustomerKey
Inner JOIN DimProductSubCategory e on e.ProductSubcategoryKey = b.ProductSubcategoryKey
INNER JOIN DimDate c ON c.DateKey = a.OrderDateKey
WHERE e.ProductCategoryKey =2
    AND c.FullDateAlternateKey BETWEEN '1/1/2014' AND '1/1/2015'
GROUP BY e.ProductCategoryKey,c.Year,d.CustomerKey,d.FirstName,d.LastName
GO

SET STATISTICS IO OFF
SET STATISTICS TIME OFF
GO
```



<a id="preview_considerations_for_in_memory" name="preview_considerations_for_in_memory"></a>


## <a name="preview-considerations-for-in-memory-oltp"></a>Consideraciones de vista previa para OLTP en memoria


Las características de OLTP en memoria de base de datos de SQL Azure se ha [activado para la vista previa en el 28 de octubre de 2015](https://azure.microsoft.com/updates/public-preview-in-memory-oltp-and-real-time-operational-analytics-for-azure-sql-database/).


En la vista previa actual, OLTP en memoria sólo es compatible:

- Bases de datos en un nivel de servicio *Premium* .

- Bases de datos que se crearon después de las características en memoria OLTP se ha activado.
 - Una nueva base de datos no admite en memoria OLTP si se restaura una base de datos que se creó antes de que se ha activado las características de OLTP en memoria.


En caso de duda, siempre puede ejecutar la siguiente T-SQL SELECT para determinar si la base de datos admite OLTP en memoria. Un resultado de **1** significa que la base de datos admite OLTP en memoria:

```
SELECT DatabasePropertyEx(DB_NAME(), 'IsXTPSupported');
```


Si la consulta devuelve **1**, OLTP en memoria es compatible con esta base de datos y cualquier copia de la base de datos y restaurar base de datos creado en función de esta base de datos.


#### <a name="objects-allowed-only-at-premium"></a>Objetos que sólo se permite en Premium


Si una base de datos contiene cualquiera de los siguientes tipos de objetos en memoria OLTP o tipos, no se admite el cambio del nivel de servicio de la base de datos de Premium básica o estándar. Para reducir la base de datos, anular estos objetos:

- Optimizar memoria tablas
- Tipos de tabla optimizar memoria
- Módulos compilados de forma nativa


#### <a name="other-relationships"></a>Otras relaciones


- No se admiten las características de usando en memoria OLTP con bases de datos en grupos elásticas durante la vista previa.
 - Para mover una base de datos que tenga o haya tenido objetos en memoria OLTP a un grupo de elástico, siga estos pasos:
  - 1. Eliminar cualquier tablas optimizado de memoria, tipos de tabla y módulos de forma nativa compilados T SQL en la base de datos
  - 2. Cambiar el nivel de servicio de la base de datos a estándar
  - 3. Mover la base de datos en el grupo elástico

- No se admite el uso de memoria OLTP con almacén de datos de SQL.
 - Se admite la característica de índice de columnstore de análisis en memoria en el almacén de datos de SQL.

- El almacén de la consulta no captura consultas interior módulos compilados de forma nativa.

- Algunas características de Transact-SQL no son compatibles con OLTP en memoria. Esto se aplica a Microsoft SQL Server y base de datos de SQL Azure. Para obtener información detallada, consulte:
 - [Soporte de Transact-SQL para OLTP en memoria](http://msdn.microsoft.com/library/dn133180.aspx)
 - [Construcciones de Transact-SQL no admitidos OLTP en memoria](http://msdn.microsoft.com/library/dn246937.aspx)


## <a name="next-steps"></a>Pasos siguientes


- Intente [OLTP de uso de memoria en una aplicación existente de SQL Azure.](sql-database-in-memory-oltp-migration.md)


## <a name="additional-resources"></a>Recursos adicionales

#### <a name="deeper-information"></a>Más información

- [Obtenga más información sobre OLTP en memoria, que se aplica a Microsoft SQL Server y base de datos de SQL Azure](http://msdn.microsoft.com/library/dn133186.aspx)

- [Obtenga más información sobre el análisis de operaciones en tiempo real en MSDN](http://msdn.microsoft.com/library/dn817827.aspx)

- Notas del producto en [modelos de carga de trabajo comunes y consideraciones sobre la migración](http://msdn.microsoft.com/library/dn673538.aspx), que describe los patrones de carga de trabajo donde OLTP en memoria normalmente proporciona mejoras de rendimiento significativas.

#### <a name="application-design"></a>Diseño de la aplicación

- [OLTP (optimización en memoria) en la memoria](http://msdn.microsoft.com/library/dn133186.aspx)

- [OLTP en memoria de uso en una aplicación existente de SQL Azure.](sql-database-in-memory-oltp-migration.md)

#### <a name="tools"></a>Herramientas

- [Vista previa de herramientas de datos de SQL Server (SSDT)](http://msdn.microsoft.com/library/mt204009.aspx), de la última versión mensual.

- [Descripción de las herramientas de idioma (RML) de marcado de reproducción para SQL Server](http://support.microsoft.com/en-us/kb/944837)

- [Almacenamiento en memoria de monitor](sql-database-in-memory-oltp-monitoring.md) para OLTP en memoria.

