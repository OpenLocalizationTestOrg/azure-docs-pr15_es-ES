<properties
   pageTitle="PolyBase en el Tutorial de almacén de datos SQL | Microsoft Azure"
   description="Obtenga información sobre qué es PolyBase y cómo usarla para escenarios de almacenamiento de datos."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="ckarst"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="10/10/2016"
   ms.author="cakarst;barbkess;sonyama"/>


# <a name="load-data-with-polybase-in-sql-data-warehouse"></a>Cargar los datos con PolyBase en el almacén de datos de SQL

> [AZURE.SELECTOR]
- [Redgate](sql-data-warehouse-load-with-redgate.md)  
- [Generador de datos](sql-data-warehouse-get-started-load-with-azure-data-factory.md)  
- [PolyBase](sql-data-warehouse-get-started-load-with-polybase.md)  
- [BCP](sql-data-warehouse-load-with-bcp.md)

Este tutorial muestra cómo cargar datos en el almacén de datos SQL con AzCopy y PolyBase. Cuando haya terminado, sabrá cómo:

- Utilizar AzCopy para copiar los datos al almacenamiento de blobs de Windows Azure
- Crear objetos de base de datos para definir los datos
- Ejecutar una consulta SQL T para cargar los datos

>[AZURE.VIDEO loading-data-with-polybase-in-azure-sql-data-warehouse]

## <a name="prerequisites"></a>Requisitos previos

Paso a través de este tutorial, necesita

- Una base de datos de almacén de datos de SQL.
- Una cuenta de Azure almacenamiento de tipo localmente redundantes almacenamiento estándar (estándar-LRS), almacenamiento Geo redundantes estándar (estándar GRS) o almacenamiento de Geo redundantes de acceso de lectura estándar (estándar RAGRS).
- Utilidad de línea de comandos de AzCopy. Descargar e instalar la [última versión de AzCopy][] que se instala con las herramientas de Microsoft Azure almacenamiento.

    ![Herramientas de almacenamiento de Azure](./media/sql-data-warehouse-get-started-load-with-polybase/install-azcopy.png)


## <a name="step-1-add-sample-data-to-azure-blob-storage"></a>Paso 1: Agregar datos de ejemplo con el almacenamiento de blobs de Windows Azure

Para cargar los datos, es necesario colocar algunos datos de ejemplo en un almacenamiento de blobs de Windows Azure. En este paso se rellenan una blobs de Windows Azure almacenamiento con datos de ejemplo. Más adelante, vamos a utilizar PolyBase para cargar estos datos de ejemplo en la base de datos de almacén de datos de SQL.

### <a name="a-prepare-a-sample-text-file"></a>A. Preparar un archivo de texto de ejemplo

Para preparar un archivo de texto de muestra:

1. Abra el Bloc de notas y copie las siguientes líneas de datos en un archivo nuevo. Guardar en el directorio temporal local como % temp%\DimDate2.txt.

```
20150301,1,3
20150501,2,4
20151001,4,2
20150201,1,3
20151201,4,2
20150801,3,1
20150601,2,4
20151101,4,2
20150401,2,4
20150701,3,1
20150901,3,1
20150101,1,3
```

### <a name="b-find-your-blob-service-endpoint"></a>B. Busque el extremo de servicio blob

Para buscar su extremo de servicio de blobs de Windows:

1. Desde el Portal de Azure, seleccione **Examinar** > **Cuentas de almacenamiento**.
2. Haga clic en la cuenta de almacenamiento que desee usar.
3. En el módulo de cuenta de almacenamiento, haga clic en BLOB

    ![Haga clic en BLOB](./media/sql-data-warehouse-get-started-load-with-polybase/click-blobs.png)

1. Guardar la URL de extremo de servicio blob para más tarde.

    ![Extremo de servicio de blobs](./media/sql-data-warehouse-get-started-load-with-polybase/blob-service.png)

### <a name="c-find-your-azure-storage-key"></a>C. Buscar la clave de almacenamiento de Azure

Para buscar la clave de almacenamiento de Azure:

1. Desde el Portal de Azure, seleccione **Examinar** > **Cuentas de almacenamiento**.
2. Haga clic en la cuenta de almacenamiento que desee usar.
3. Seleccione **todas las configuraciones** > **teclas de acceso**.
4. Haga clic en el cuadro Copiar para copiar una de las claves de acceso al Portapapeles.

    ![Copiar clave de almacenamiento de Azure](./media/sql-data-warehouse-get-started-load-with-polybase/access-key.png)

### <a name="d-copy-the-sample-file-to-azure-blob-storage"></a>D. Copie el archivo con el almacenamiento de blobs de Windows Azure

Para copiar los datos al almacenamiento de blobs de Windows Azure:

1. Abra un símbolo del sistema y cambie los directorios al directorio de instalación AzCopy. Este comando cambia al directorio de instalación predeterminada en un cliente de Windows de 64 bits.

    ```
    cd /d "%ProgramFiles(x86)%\Microsoft SDKs\Azure\AzCopy"
    ```

1. Ejecute el comando siguiente para cargar el archivo. Especificar la dirección URL de extremo de servicio blob para <blob service endpoint URL> y la clave de cuenta de almacenamiento de Azure para < azure_storage_account_key >.

    ```
    .\AzCopy.exe /Source:C:\Temp\ /Dest:<blob service endpoint URL> /datacontainer/datedimension/ /DestKey:<azure_storage_account_key> /Pattern:DimDate2.txt
    ```

También, vea [Introducción a la utilidad de línea de comandos de AzCopy][].

### <a name="e-explore-your-blob-storage-container"></a>E. Explorar el contenedor de almacenamiento de blobs

Para ver el archivo cargado al almacenamiento de blobs:

1. Vuelva a su módulo de servicio de blobs de Windows.
2. En contenedores, haga doble clic en **datacontainer**.
3. Para explorar la ruta de acceso a los datos, haga clic en la carpeta **datedimension** y se muestra el archivo cargado **DimDate2.txt**.
4. Para ver las propiedades, haga clic en **DimDate2.txt**.
5. Tenga en cuenta que en la hoja de propiedades de blobs, puede descargar o eliminar el archivo.

    ![Blobs de Azure almacenamiento de vista](./media/sql-data-warehouse-get-started-load-with-polybase/view-blob.png)


## <a name="step-2-create-an-external-table-for-the-sample-data"></a>Paso 2: Crear una tabla externa para los datos de ejemplo

En esta sección se crea una tabla externa que define los datos de ejemplo.

PolyBase usa tablas externas para tener acceso a datos en el almacenamiento de blobs de Windows Azure. Puesto que los datos no se almacenan en el almacén de datos de SQL, PolyBase controla la autenticación a los datos externos usando una credencial de ámbito de la base de datos.

El ejemplo en este paso utiliza estas instrucciones Transact-SQL para crear una tabla externa.

- [Crear clave principal (Transact-SQL)][] para cifrar el secreto de la base de datos en el ámbito credenciales.
- [Crear base de datos de ámbito de la credencial (Transact-SQL)][] para especificar la información de autenticación para su cuenta de almacenamiento de Azure.
- [Crear origen de datos externo (Transact-SQL)][] para especificar la ubicación de su almacenamiento de blobs de Windows Azure.
- [Crear formato de archivo externo (Transact-SQL)][] para especificar el formato de los datos.
- [Crear tabla externa (Transact-SQL)][] para especificar la definición de tabla y la ubicación de los datos.

Ejecute esta consulta en la base de datos de almacén de datos de SQL. Crea una tabla externa denominada DimDate2External en el esquema dbo que apunta a los datos de ejemplo de DimDate2.txt en el almacenamiento de blobs de Windows Azure.


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


-- D: Create an external file format
-- FORMAT_TYPE: Type of file format in Azure storage (supported: DELIMITEDTEXT, RCFILE, ORC, PARQUET).
-- FORMAT_OPTIONS: Specify field terminator, string delimiter, date format etc. for delimited text files.
-- Specify DATA_COMPRESSION method if data is compressed.

CREATE EXTERNAL FILE FORMAT TextFile
WITH (
    FORMAT_TYPE = DelimitedText,
    FORMAT_OPTIONS (FIELD_TERMINATOR = ',')
);


-- E: Create the external table
-- Specify column names and data types. This needs to match the data in the sample file.
-- LOCATION: Specify path to file or directory that contains the data (relative to the blob container).
-- To point to all files under the blob container, use LOCATION='.'

CREATE EXTERNAL TABLE dbo.DimDate2External (
    DateId INT NOT NULL,
    CalendarQuarter TINYINT NOT NULL,
    FiscalQuarter TINYINT NOT NULL
)
WITH (
    LOCATION='/datedimension/',
    DATA_SOURCE=AzureStorage,
    FILE_FORMAT=TextFile
);


-- Run a query on the external table

SELECT count(*) FROM dbo.DimDate2External;

```


En el Explorador de objetos de SQL Server en Visual Studio, puede ver el formato de archivo externo, origen de datos externo y la tabla DimDate2External.

![Tabla externa de vista](./media/sql-data-warehouse-get-started-load-with-polybase/external-table.png)

## <a name="step-3-load-data-into-sql-data-warehouse"></a>Paso 3: Cargar datos en el almacén de datos SQL

Una vez creada la tabla externa, puede cargar los datos en una tabla nueva o insertarlo en una tabla existente.

- Para cargar los datos en una nueva tabla, ejecute la instrucción [CREATE TABLE AS SELECT (Transact-SQL)][] . La nueva tabla tendrá las columnas con el nombre de la consulta. Los tipos de datos de las columnas se coinciden con los tipos de datos en la definición de tabla externa.
- Para cargar los datos en una tabla existente, use la [Insertar... Seleccione (Transact-SQL)][] instrucción.

```sql
-- Load the data from Azure blob storage to SQL Data Warehouse

CREATE TABLE dbo.DimDate2
WITH
(   
    CLUSTERED COLUMNSTORE INDEX,
    DISTRIBUTION = ROUND_ROBIN
)
AS
SELECT * FROM [dbo].[DimDate2External];
```

## <a name="step-4-create-statistics-on-your-newly-loaded-data"></a>Paso 4: Crear estadísticas en los datos recién cargados

Almacén de datos SQL no crear automáticamente o actualización automática de estadísticas. Por lo tanto, para mejorar el rendimiento de consulta alto, es importante crear estadísticas de cada columna de cada tabla después de la primera carga. También es importante actualizar estadísticas después de realizar cambios importantes en los datos.

Este ejemplo crea estadísticas de una columna en la nueva tabla de DimDate2.

```sql
CREATE STATISTICS [DateId] on [DimDate2] ([DateId]);
CREATE STATISTICS [CalendarQuarter] on [DimDate2] ([CalendarQuarter]);
CREATE STATISTICS [FiscalQuarter] on [DimDate2] ([FiscalQuarter]);
```

Para obtener más información, consulte [estadísticas][].  


## <a name="next-steps"></a>Pasos siguientes
Consulte la [Guía de PolyBase][] para obtener más información que debe conocer al desarrollar una solución que use PolyBase.

<!--Image references-->


<!--Article references-->
[PolyBase in SQL Data Warehouse Tutorial]: ./sql-data-warehouse-get-started-load-with-polybase.md
[Load data with bcp]: ./sql-data-warehouse-load-with-bcp.md
[Estadísticas]: ./sql-data-warehouse-tables-statistics.md
[Guía de PolyBase]: ./sql-data-warehouse-load-polybase-guide.md
[Introducción a la utilidad de línea de comandos de AzCopy]: ../storage/storage-use-azcopy.md
[versión más reciente de AzCopy]: ../storage/storage-use-azcopy.md

<!--External references-->
[supported source/sink]: https://msdn.microsoft.com/library/dn894007.aspx
[copy activity]: https://msdn.microsoft.com/library/dn835035.aspx
[SQL Server destination adapter]: https://msdn.microsoft.com/library/ms141095.aspx
[SSIS]: https://msdn.microsoft.com/library/ms141026.aspx


[Crear origen de datos externo (Transact-SQL)]:https://msdn.microsoft.com/library/dn935022.aspx
[CREAR el formato de archivo externo (Transact-SQL)]:https://msdn.microsoft.com/library/dn935026.aspx
[Crear tabla externa (Transact-SQL)]:https://msdn.microsoft.com/library/dn935021.aspx

[DROP EXTERNAL DATA SOURCE (Transact-SQL)]:https://msdn.microsoft.com/library/mt146367.aspx
[DROP EXTERNAL FILE FORMAT (Transact-SQL)]:https://msdn.microsoft.com/library/mt146379.aspx
[DROP EXTERNAL TABLE (Transact-SQL)]:https://msdn.microsoft.com/library/mt130698.aspx

[Crear tabla AS SELECT (Transact-SQL)]:https://msdn.microsoft.com/library/mt204041.aspx
[INSERTAR... Seleccione (Transact-SQL)]:https://msdn.microsoft.com/library/ms174335.aspx
[Crear clave principal (Transact-SQL)]:https://msdn.microsoft.com/library/ms174382.aspx
[CREATE CREDENTIAL (Transact-SQL)]:https://msdn.microsoft.com/library/ms189522.aspx
[Crear base de datos en el ámbito CREDENCIAL (Transact-SQL)]:https://msdn.microsoft.com/library/mt270260.aspx
[DROP CREDENTIAL (Transact-SQL)]:https://msdn.microsoft.com/library/ms189450.aspx
