<properties
   pageTitle="Cargar datos de almacenamiento de blobs de Windows Azure en el almacén de datos de SQL (PolyBase) | Microsoft Azure"
   description="Obtenga información sobre cómo usar PolyBase para cargar los datos desde el almacenamiento de blobs de Windows Azure en el almacén de datos de SQL. Cargar algunas tablas de datos públicos en el esquema de almacén de datos de Contoso minorista."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="ckarst"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="08/25/2016"
   ms.author="cakarst;barbkess;sonyama"/>


# <a name="load-data-from-azure-blob-storage-into-sql-data-warehouse-polybase"></a>Cargar datos de almacenamiento de blobs de Windows Azure en el almacén de datos de SQL (PolyBase)

> [AZURE.SELECTOR]
- [Generador de datos](sql-data-warehouse-load-from-azure-blob-storage-with-data-factory.md)
- [PolyBase](sql-data-warehouse-load-from-azure-blob-storage-with-polybase.md)

Utilizar comandos de PolyBase y T-SQL para cargar los datos desde el almacenamiento de blobs de Windows Azure en el almacén de datos de SQL Azure. 

Para ser sencillo, este tutorial carga dos tablas de un público Blob de almacenamiento de Azure en el esquema de almacén de datos de Contoso minorista. Para cargar el conjunto completo de datos, ejecute el ejemplo [cargar el almacén de datos de comercio por menor de Contoso completa][] del repositorio de ejemplos de Microsoft SQL Server.

En este tutorial, podrá:

1. Configurar PolyBase a cargar desde el almacenamiento de blobs de Windows Azure
2. Cargar datos públicos en la base de datos
3. Realizar las optimizaciones cuando finaliza la carga.


## <a name="before-you-begin"></a>Antes de empezar
Para ejecutar este tutorial, necesita una cuenta de Azure que ya tiene una base de datos de almacén de datos de SQL. Si todavía no tiene, consulte [crear un almacén de datos de SQL][].

## <a name="1-configure-the-data-source"></a>1. configurar el origen de datos

PolyBase utiliza objetos externos de T SQL para definir la ubicación y los atributos de los datos externos. Las definiciones de objeto externos se almacenan en el almacén de datos de SQL. Los datos se almacenan externamente.

### <a name="11-create-a-credential"></a>1.1. Crear una credencial

**Omitir este paso** si va a cargar los datos públicos de Contoso. No es necesario un acceso seguro a los datos públicos porque ya se tiene acceso a todo el mundo.

**No omitir este paso** si está utilizando este tutorial como una plantilla para cargar sus propios datos. Para obtener acceso a datos a través de una credencial, utilice la siguiente secuencia de comandos para crear una credencial de ámbito de la base de datos y usarla al definir la ubicación del origen de datos.


```sql
-- A: Create a master key.
-- Only necessary if one does not already exist.
-- Required to encrypt the credential secret in the next step.

CREATE MASTER KEY;


-- B: Create a database scoped credential
-- IDENTITY: Provide any string, it is not used for authentication to Azure storage.
-- SECRET: Provide your Azure storage account key.


CREATE DATABASE SCOPED CREDENTIAL AzureStorageCredential
WITH
    IDENTITY = 'user',
    SECRET = '<azure_storage_account_key>'
;


-- C: Create an external data source
-- TYPE: HADOOP - PolyBase uses Hadoop APIs to access data in Azure blob storage.
-- LOCATION: Provide Azure storage account name and blob container name.
-- CREDENTIAL: Provide the credential created in the previous step.

CREATE EXTERNAL DATA SOURCE AzureStorage
WITH (
    TYPE = HADOOP,
    LOCATION = 'wasbs://<blob_container_name>@<azure_storage_account_name>.blob.core.windows.net',
    CREDENTIAL = AzureStorageCredential
);
```

Vaya al paso 2.

### <a name="12-create-the-external-data-source"></a>1.2. Crear el origen de datos externos

Use este comando [Crear origen de datos externo][] para almacenar la ubicación de los datos y el tipo de datos. 

```sql
CREATE EXTERNAL DATA SOURCE AzureStorage_west_public
WITH 
(  
    TYPE = Hadoop 
,   LOCATION = 'wasbs://contosoretaildw-tables@contosoretaildw.blob.core.windows.net/'
); 
```

> [AZURE.IMPORTANT] Si decide hacer los contenedores de almacenamiento su blobs de Windows azure público, recuerde que como el propietario de los datos se cargará datos cargos de salida cuando datos dejan el centro de datos. 

## <a name="2-configure-data-format"></a>2. configurar el formato de datos

Los datos se almacenan en archivos de texto en el almacenamiento de blobs de Windows Azure, y cada campo separado por un delimitador. Ejecutar este comando [Crear formato de archivo externo][] para especificar el formato de los datos en los archivos de texto. Sin comprimir los datos de Contoso y delimitado por canalización.

```sql
CREATE EXTERNAL FILE FORMAT TextFileFormat 
WITH 
(   FORMAT_TYPE = DELIMITEDTEXT
,   FORMAT_OPTIONS  (   FIELD_TERMINATOR = '|'
                    ,   STRING_DELIMITER = ''
                    ,   DATE_FORMAT      = 'yyyy-MM-dd HH:mm:ss.fff'
                    ,   USE_TYPE_DEFAULT = FALSE 
                    )
);
``` 

## <a name="3-create-the-external-tables"></a>3. crear las tablas externas

Ahora que ha especificado el formato de archivo y origen de datos, ya está listo para crear las tablas externas. 

### <a name="31-create-a-schema-for-the-data"></a>3.1. Crear un esquema para los datos. 

Para crear un sitio para almacenar los datos de Contoso en la base de datos, crear un esquema.

```sql
CREATE SCHEMA [asb]
GO
```

### <a name="32-create-the-external-tables"></a>3.2. Crear las tablas externas. 

Ejecute esta secuencia de comandos para crear las tablas externas DimProduct y FactOnlineSales. Lo que estamos haciendo aquí es definir los nombres de columna y tipos de datos y enlazarlos a la ubicación y el formato de los archivos de almacenamiento de blobs de Windows Azure. La definición se almacena en el almacén de datos de SQL y los datos aún están en el almacenamiento de Azure Blob.

El parámetro de **ubicación** es la carpeta en la carpeta raíz en el almacenamiento de Azure Blob. Cada tabla está en una carpeta diferente.


```sql

--DimProduct
CREATE EXTERNAL TABLE [asb].DimProduct (
    [ProductKey] [int] NOT NULL,
    [ProductLabel] [nvarchar](255) NULL,
    [ProductName] [nvarchar](500) NULL,
    [ProductDescription] [nvarchar](400) NULL,
    [ProductSubcategoryKey] [int] NULL,
    [Manufacturer] [nvarchar](50) NULL,
    [BrandName] [nvarchar](50) NULL,
    [ClassID] [nvarchar](10) NULL,
    [ClassName] [nvarchar](20) NULL,
    [StyleID] [nvarchar](10) NULL,
    [StyleName] [nvarchar](20) NULL,
    [ColorID] [nvarchar](10) NULL,
    [ColorName] [nvarchar](20) NOT NULL,
    [Size] [nvarchar](50) NULL,
    [SizeRange] [nvarchar](50) NULL,
    [SizeUnitMeasureID] [nvarchar](20) NULL,
    [Weight] [float] NULL,
    [WeightUnitMeasureID] [nvarchar](20) NULL,
    [UnitOfMeasureID] [nvarchar](10) NULL,
    [UnitOfMeasureName] [nvarchar](40) NULL,
    [StockTypeID] [nvarchar](10) NULL,
    [StockTypeName] [nvarchar](40) NULL,
    [UnitCost] [money] NULL,
    [UnitPrice] [money] NULL,
    [AvailableForSaleDate] [datetime] NULL,
    [StopSaleDate] [datetime] NULL,
    [Status] [nvarchar](7) NULL,
    [ImageURL] [nvarchar](150) NULL,
    [ProductURL] [nvarchar](150) NULL,
    [ETLLoadID] [int] NULL,
    [LoadDate] [datetime] NULL,
    [UpdateDate] [datetime] NULL
)
WITH
(
    LOCATION='/DimProduct/' 
,   DATA_SOURCE = AzureStorage_west_public
,   FILE_FORMAT = TextFileFormat
,   REJECT_TYPE = VALUE
,   REJECT_VALUE = 0
)
;
 
--FactOnlineSales
CREATE EXTERNAL TABLE [asb].FactOnlineSales 
(
    [OnlineSalesKey] [int]  NOT NULL,
    [DateKey] [datetime] NOT NULL,
    [StoreKey] [int] NOT NULL,
    [ProductKey] [int] NOT NULL,
    [PromotionKey] [int] NOT NULL,
    [CurrencyKey] [int] NOT NULL,
    [CustomerKey] [int] NOT NULL,
    [SalesOrderNumber] [nvarchar](20) NOT NULL,
    [SalesOrderLineNumber] [int] NULL,
    [SalesQuantity] [int] NOT NULL,
    [SalesAmount] [money] NOT NULL,
    [ReturnQuantity] [int] NOT NULL,
    [ReturnAmount] [money] NULL,
    [DiscountQuantity] [int] NULL,
    [DiscountAmount] [money] NULL,
    [TotalCost] [money] NOT NULL,
    [UnitCost] [money] NULL,
    [UnitPrice] [money] NULL,
    [ETLLoadID] [int] NULL,
    [LoadDate] [datetime] NULL,
    [UpdateDate] [datetime] NULL
)
WITH
(
    LOCATION='/FactOnlineSales/' 
,   DATA_SOURCE = AzureStorage_west_public
,   FILE_FORMAT = TextFileFormat
,   REJECT_TYPE = VALUE
,   REJECT_VALUE = 0
)
;
```

## <a name="4-load-the-data"></a>4. cargar los datos
Hay diferentes maneras de obtener acceso a datos externos.  Puede consultar datos directamente de la tabla externa, cargar los datos en tablas de base de datos nueva o agregar datos externos a las tablas de base de datos existentes.  


### <a name="41-create-a-new-schema"></a>4.1. Crear un nuevo esquema

CTAS crea una nueva tabla que contiene datos.  Primero, cree un esquema para los datos de contoso.

```sql
CREATE SCHEMA [cso]
GO
```

### <a name="42-load-the-data-into-new-tables"></a>4.2. Cargar los datos en tablas nuevas

Para cargar datos desde el almacenamiento de blobs de Windows Azure y guárdelo en una tabla dentro de la base de datos, utilice la instrucción [CREATE TABLE AS SELECT (Transact-SQL)][] . Carga con CTAS aprovecha las tablas externas inflexible que acaba de crear. Para cargar los datos en tablas nuevas, utilice una instrucción de [CTAS][] por tabla. 

CTAS crea una tabla nueva y rellena con los resultados de una instrucción select. CTAS define la nueva tabla para tener las mismas columnas y tipos de datos, como los resultados de la instrucción select. Si selecciona todas las columnas de una tabla externa, la nueva tabla será una réplica de las columnas y tipos de datos en la tabla externa.

En este ejemplo, creamos la dimensión y la tabla de hechos como hash tablas distribuidos. 


```sql
SELECT GETDATE();
GO

CREATE TABLE [cso].[DimProduct]            WITH (DISTRIBUTION = HASH([ProductKey]  ) ) AS SELECT * FROM [asb].[DimProduct]             OPTION (LABEL = 'CTAS : Load [cso].[DimProduct]             ');
CREATE TABLE [cso].[FactOnlineSales]       WITH (DISTRIBUTION = HASH([ProductKey]  ) ) AS SELECT * FROM [asb].[FactOnlineSales]        OPTION (LABEL = 'CTAS : Load [cso].[FactOnlineSales]        ');
```

### <a name="43-track-the-load-progress"></a>4.3 realizar un seguimiento del progreso de carga

Puede realizar un seguimiento del progreso de la carga de vistas de administración dinámica (DMV). 

```sql
-- To see all requests
SELECT * FROM sys.dm_pdw_exec_requests;

-- To see a particular request identified by its label
SELECT * FROM sys.dm_pdw_exec_requests as r;
WHERE r.[label] = 'CTAS : Load [cso].[DimProduct]             '
      OR r.[label] = 'CTAS : Load [cso].[FactOnlineSales]        '
;

-- To track bytes and files
SELECT
    r.command,
    s.request_id,
    r.status,
    count(distinct input_name) as nbr_files, 
    sum(s.bytes_processed)/1024/1024 as gb_processed
FROM
    sys.dm_pdw_exec_requests r
    inner join sys.dm_pdw_dms_external_work s
        on r.request_id = s.request_id
WHERE 
    r.[label] = 'CTAS : Load [cso].[DimProduct]             '
    OR r.[label] = 'CTAS : Load [cso].[FactOnlineSales]        '
GROUP BY
    r.command,
    s.request_id,
    r.status
ORDER BY
    nbr_files desc,
    gb_processed desc;
```

## <a name="5-optimize-columnstore-compression"></a>5. optimizar columnstore compresión

De forma predeterminada, el almacén de datos de SQL almacena la tabla como un índice agrupado columnstore. Una vez completada la carga, algunas de las filas de datos no se comprima en el columnstore.  Hay varios motivos por qué esto puede suceder. Para obtener más información, vea [Administrar índices columnstore][].

Para optimizar el rendimiento de la consulta y compresión columnstore después de una carga, volver a crear la tabla para forzar el índice columnstore para comprimir todas las filas. 

```sql
SELECT GETDATE();
GO

ALTER INDEX ALL ON [cso].[DimProduct]               REBUILD;
ALTER INDEX ALL ON [cso].[FactOnlineSales]          REBUILD;
```

Para obtener más información sobre el mantenimiento de índices columnstore, vea el artículo [Administrar índices columnstore][] .

## <a name="6-optimize-statistics"></a>6. optimizar estadísticas

Es aconsejable crear estadísticas de una columna inmediatamente después de una carga. Existen varias opciones para las estadísticas. Por ejemplo, si crea estadísticas de una columna en cada columna tardará mucho tiempo en volver a crear todas las estadísticas. Si sabe que algunas columnas no va a estar en predicados de consulta, puede omitir las estadísticas de creación en esas columnas.

Si decide crear estadísticas de una columna en cada columna de cada tabla, puede usar el código de ejemplo de procedimiento almacenado `prc_sqldw_create_stats` en el artículo de [estadísticas][] .

El ejemplo siguiente es un buen punto de partida para crear estadísticas. Crea estadísticas de una columna de cada columna de la tabla de dimensiones y, en cada columna de combinación en las tablas de hechos. Siempre puede agregar más adelante estadísticas único o de varias columnas a otras columnas de la tabla de hechos.


```sql
CREATE STATISTICS [stat_cso_DimProduct_AvailableForSaleDate] ON [cso].[DimProduct]([AvailableForSaleDate]);
CREATE STATISTICS [stat_cso_DimProduct_BrandName] ON [cso].[DimProduct]([BrandName]);
CREATE STATISTICS [stat_cso_DimProduct_ClassID] ON [cso].[DimProduct]([ClassID]);
CREATE STATISTICS [stat_cso_DimProduct_ClassName] ON [cso].[DimProduct]([ClassName]);
CREATE STATISTICS [stat_cso_DimProduct_ColorID] ON [cso].[DimProduct]([ColorID]);
CREATE STATISTICS [stat_cso_DimProduct_ColorName] ON [cso].[DimProduct]([ColorName]);
CREATE STATISTICS [stat_cso_DimProduct_ETLLoadID] ON [cso].[DimProduct]([ETLLoadID]);
CREATE STATISTICS [stat_cso_DimProduct_ImageURL] ON [cso].[DimProduct]([ImageURL]);
CREATE STATISTICS [stat_cso_DimProduct_LoadDate] ON [cso].[DimProduct]([LoadDate]);
CREATE STATISTICS [stat_cso_DimProduct_Manufacturer] ON [cso].[DimProduct]([Manufacturer]);
CREATE STATISTICS [stat_cso_DimProduct_ProductDescription] ON [cso].[DimProduct]([ProductDescription]);
CREATE STATISTICS [stat_cso_DimProduct_ProductKey] ON [cso].[DimProduct]([ProductKey]);
CREATE STATISTICS [stat_cso_DimProduct_ProductLabel] ON [cso].[DimProduct]([ProductLabel]);
CREATE STATISTICS [stat_cso_DimProduct_ProductName] ON [cso].[DimProduct]([ProductName]);
CREATE STATISTICS [stat_cso_DimProduct_ProductSubcategoryKey] ON [cso].[DimProduct]([ProductSubcategoryKey]);
CREATE STATISTICS [stat_cso_DimProduct_ProductURL] ON [cso].[DimProduct]([ProductURL]);
CREATE STATISTICS [stat_cso_DimProduct_Size] ON [cso].[DimProduct]([Size]);
CREATE STATISTICS [stat_cso_DimProduct_SizeRange] ON [cso].[DimProduct]([SizeRange]);
CREATE STATISTICS [stat_cso_DimProduct_SizeUnitMeasureID] ON [cso].[DimProduct]([SizeUnitMeasureID]);
CREATE STATISTICS [stat_cso_DimProduct_Status] ON [cso].[DimProduct]([Status]);
CREATE STATISTICS [stat_cso_DimProduct_StockTypeID] ON [cso].[DimProduct]([StockTypeID]);
CREATE STATISTICS [stat_cso_DimProduct_StockTypeName] ON [cso].[DimProduct]([StockTypeName]);
CREATE STATISTICS [stat_cso_DimProduct_StopSaleDate] ON [cso].[DimProduct]([StopSaleDate]);
CREATE STATISTICS [stat_cso_DimProduct_StyleID] ON [cso].[DimProduct]([StyleID]);
CREATE STATISTICS [stat_cso_DimProduct_StyleName] ON [cso].[DimProduct]([StyleName]);
CREATE STATISTICS [stat_cso_DimProduct_UnitCost] ON [cso].[DimProduct]([UnitCost]);
CREATE STATISTICS [stat_cso_DimProduct_UnitOfMeasureID] ON [cso].[DimProduct]([UnitOfMeasureID]);
CREATE STATISTICS [stat_cso_DimProduct_UnitOfMeasureName] ON [cso].[DimProduct]([UnitOfMeasureName]);
CREATE STATISTICS [stat_cso_DimProduct_UnitPrice] ON [cso].[DimProduct]([UnitPrice]);
CREATE STATISTICS [stat_cso_DimProduct_UpdateDate] ON [cso].[DimProduct]([UpdateDate]);
CREATE STATISTICS [stat_cso_DimProduct_Weight] ON [cso].[DimProduct]([Weight]);
CREATE STATISTICS [stat_cso_DimProduct_WeightUnitMeasureID] ON [cso].[DimProduct]([WeightUnitMeasureID]);
CREATE STATISTICS [stat_cso_FactOnlineSales_CurrencyKey] ON [cso].[FactOnlineSales]([CurrencyKey]);
CREATE STATISTICS [stat_cso_FactOnlineSales_CustomerKey] ON [cso].[FactOnlineSales]([CustomerKey]);
CREATE STATISTICS [stat_cso_FactOnlineSales_DateKey] ON [cso].[FactOnlineSales]([DateKey]);
CREATE STATISTICS [stat_cso_FactOnlineSales_OnlineSalesKey] ON [cso].[FactOnlineSales]([OnlineSalesKey]);
CREATE STATISTICS [stat_cso_FactOnlineSales_ProductKey] ON [cso].[FactOnlineSales]([ProductKey]);
CREATE STATISTICS [stat_cso_FactOnlineSales_PromotionKey] ON [cso].[FactOnlineSales]([PromotionKey]);
CREATE STATISTICS [stat_cso_FactOnlineSales_StoreKey] ON [cso].[FactOnlineSales]([StoreKey]);
```

## <a name="achievement-unlocked"></a>¡Logro desbloqueado!

Correctamente que haya cargado datos públicos en almacén de datos de SQL Azure. ¡Excelente trabajo!

Ahora puede comenzar a consultar las tablas que el uso de consultas como la siguiente:

```sql
SELECT  SUM(f.[SalesAmount]) AS [sales_by_brand_amount]
,       p.[BrandName]
FROM    [cso].[FactOnlineSales] AS f
JOIN    [cso].[DimProduct]      AS p ON f.[ProductKey] = p.[ProductKey]
GROUP BY p.[BrandName]
```

## <a name="next-steps"></a>Pasos siguientes
Para cargar los datos del almacén de datos de Contoso comercial completos, utilice la secuencia de comandos para obtener más sugerencias de desarrollo, vea [Introducción al desarrollo de almacén de datos de SQL][].

<!--Image references-->

<!--Article references-->
[Crear un almacén de datos SQL]: sql-data-warehouse-get-started-provision.md
[Load data into SQL Data Warehouse]: sql-data-warehouse-overview-load.md
[Información general sobre el desarrollo de almacén de datos SQL]: sql-data-warehouse-overview-develop.md
[Administrar índices columnstore]: sql-data-warehouse-tables-index.md
[Estadísticas]: sql-data-warehouse-tables-statistics.md
[CTAS]: sql-data-warehouse-develop-ctas.md
[label]: sql-data-warehouse-develop-label.md

<!--MSDN references-->
[CREAR ORIGEN DE DATOS EXTERNO]: https://msdn.microsoft.com/en-us/library/dn935022.aspx
[CREAR EL FORMATO DE ARCHIVO EXTERNO]: https://msdn.microsoft.com/en-us/library/dn935026.aspx
[Crear tabla AS SELECT (Transact-SQL)]: https://msdn.microsoft.com/library/mt204041.aspx
[sys.dm_pdw_exec_requests]: https://msdn.microsoft.com/library/mt203887.aspx
[REBUILD]: https://msdn.microsoft.com/library/ms188388.aspx

<!--Other Web references-->
[Microsoft Download Center]: http://www.microsoft.com/download/details.aspx?id=36433
[Cargar el almacén de datos de comercio por menor de Contoso completa]: https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/contoso-data-warehouse/readme.md
