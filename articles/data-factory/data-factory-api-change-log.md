<properties 
    pageTitle="Generador de datos - registro de cambios de la API de .NET | Microsoft Azure" 
    description="Describe los cambios importantes, las adiciones de características, correcciones de errores etcetera... en una versión específica de .NET API para el generador de datos de Azure." 
    services="data-factory" 
    documentationCenter="" 
    authors="spelluru" 
    manager="jhubbard" 
    editor="monicar"/>

<tags 
    ms.service="data-factory" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/21/2016" 
    ms.author="spelluru"/>

# <a name="azure-data-factory---net-api-change-log"></a>Generador de datos de Azure - registro de cambios de la API de .NET 
En este artículo se proporciona información sobre cambios en SDK de fábrica de datos de Azure en una versión específica. Puede encontrar el paquete NuGet más reciente para el generador de datos de Azure [aquí](https://www.nuget.org/packages/Microsoft.Azure.Management.DataFactories) 

## <a name="version-4110"></a>Versión 4.11.0
Adiciones de características:

- Se han agregado los siguientes tipos de servicio vinculadas:
    - [OnPremisesMongoDbLinkedService](https://msdn.microsoft.com/library/mt765129.aspx)
    - [AmazonRedshiftLinkedService](https://msdn.microsoft.com/library/mt765121.aspx)
    - [AwsAccessKeyLinkedService](https://msdn.microsoft.com/library/mt765144.aspx)
- Se han agregado los siguientes tipos de conjunto de datos: 
    - [MongoDbCollectionDataset](https://msdn.microsoft.com/library/mt765145.aspx)
    - [AmazonS3Dataset](https://msdn.microsoft.com/library/mt765112.aspx)
- Se han agregado los siguientes tipos de origen de copia:
    - [MongoDbSource](https://msdn.microsoft.com/en-US/library/mt765123.aspx)

## <a name="version-4100"></a>Versión 4.10.0
- Las siguientes propiedades opcionales se agregaron a TextFormat:
    - [SkipLineCount](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.textformat.skiplinecount.aspx)
    - [Primerafilacomoencabezado](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.textformat.firstrowasheader.aspx)
    - [TreatEmptyAsNull](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.textformat.treatemptyasnull.aspx)
- Se han agregado los siguientes tipos de servicio vinculadas:
    - [OnPremisesCassandraLinkedService](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.onpremisescassandralinkedservice.aspx)
    - [SalesforceLinkedService](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.salesforcelinkedservice.aspx)
- Se han agregado los siguientes tipos de conjunto de datos:
    - [OnPremisesCassandraTableDataset](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.onpremisescassandratabledataset.aspx)
- Se han agregado los siguientes tipos de origen de copia:
    - [CassandraSource](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.cassandrasource.aspx)
- Agregar propiedad de [WebServiceInputs](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.azuremlbatchexecutionactivity.webserviceinputs.aspx) a AzureMLBatchExecutionActivity 
    - Habilitar pasando web múltiples entradas de servicio a un ensayo de aprendizaje del equipo de Azure


## <a name="version-491"></a>Versión 4.9.1

### <a name="bug-fix"></a>Corregir el error

- Descartar la autenticación basada en WebApi para [WebLinkedService](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.weblinkedservice.authenticationtype.aspx).

## <a name="version-490"></a>Versión 4.9.0

### <a name="feature-additions"></a>Adiciones de características

- Agregar propiedades [EnableStaging](https://msdn.microsoft.com/library/mt767916.aspx) y [StagingSettings](https://msdn.microsoft.com/library/mt767918.aspx) a CopyActivity. Para obtener detalles sobre la característica, vea [Copiar preconfigurada](data-factory-copy-activity-performance.md#staged-copy) .


### <a name="bug-fix"></a>Corregir el error

- Introducir una sobrecarga del método [ActivityWindowOperationExtensions.List](https://msdn.microsoft.com/library/mt767915.aspx) , que toma una instancia de [ActivityWindowsByActivityListParameters](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.activitywindowsbyactivitylistparameters.aspx) .
- Marcar [WriteBatchSize](https://msdn.microsoft.com/library/dn884293.aspx) y [WriteBatchTimeout](https://msdn.microsoft.com/library/dn884245.aspx) como opcionales en CopySink.

## <a name="version-480"></a>Versión 4.8.0

### <a name="feature-additions"></a>Adiciones de características
- Se agregaron las siguientes propiedades opcionales para el tipo de actividad de copia para habilitar la optimización del rendimiento de la copia:
    - [ParallelCopies](https://msdn.microsoft.com/library/mt767910.aspx)
    - [CloudDataMovementUnits](https://msdn.microsoft.com/library/mt767912.aspx)

## <a name="version-470"></a>Versión 4.7.0

### <a name="feature-additions"></a>Adiciones de características
* Agregar nuevo StorageFormat tipo [OrcFormat](https://msdn.microsoft.com/library/mt723391.aspx) para copiar los archivos en formato de fila optimizado columnas (ORC).
* Agregar propiedades [AllowPolyBase](https://msdn.microsoft.com/library/mt723396.aspx) y PolyBaseSettings a SqlDWSink.
    * Habilita el uso de PolyBase para copiar datos en el almacén de datos de SQL.

## <a name="version-461"></a>Versión 4.6.1

### <a name="bug-fixes"></a>Correcciones de errores
* Corrige la solicitud HTTP para incluir windows actividad.
    * Quita el nombre del grupo de recursos y el nombre del generador de datos de la carga de la solicitud.

## <a name="version-460"></a>Versión 4.6.0

### <a name="feature-additions"></a>Adiciones de características

- Las siguientes propiedades se agregaron a [PipelineProperties](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.pipelineproperties_properties.aspx):
    - [PipelineMode](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.pipelineproperties.pipelinemode.aspx)
    - [ExpirationTime](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.pipelineproperties.expirationtime.aspx)
    - [Conjuntos de datos](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.pipelineproperties.datasets.aspx)
- Las siguientes propiedades se agregaron a [PipelineRuntimeInfo](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.common.models.pipelineruntimeinfo.aspx):
    - [PipelineState](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.common.models.pipelineruntimeinfo.pipelinestate.aspx)
- Nueva agregada [StorageFormat](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.storageformat.aspx) escriba tipo [JsonFormat](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.jsonformat.aspx) definir conjuntos de datos cuyos datos están en formato JSON. 

## <a name="version-450"></a>Versión 4.5.0

### <a name="feature-additions"></a>Adiciones de características
* [Operaciones de lista de la ventana de actividad](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.activitywindowoperationsextensions.aspx)de agregado.
    * Nuevos métodos para recuperar windows actividad con los filtros en función de los tipos de entidad (es decir, generadores de datos, conjuntos de datos, canalizaciones y actividades).
* Se han agregado los siguientes tipos de servicio vinculadas: 
    * [ODataLinkedService](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.odatalinkedservice.aspx), [WebLinkedService](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.weblinkedservice.aspx)
* Se han agregado los siguientes tipos de conjunto de datos: 
    * [ODataResourceDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.odataresourcedataset.aspx), [WebTableDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.webtabledataset.aspx)
* Se han agregado los siguientes tipos de origen de copia:  
    * [WebSource](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.websource.aspx)

## <a name="version-440"></a>Versión 4.4.0

### <a name="feature-additions"></a>Adiciones de características

- El siguiente tipo de servicio vinculada se ha agregado como orígenes de datos y receptores de actividades de copia:
    - [AzureStorageSasLinkedService](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.azurestoragesaslinkedservice.aspx). Para obtener ejemplos e información conceptual, consulte [Sa vinculado servicio de almacenamiento de Azure](data-factory-azure-blob-connector.md#azure-storage-sas-linked-service) . 

## <a name="version-430"></a>Versión 4.3.0

### <a name="feature-additions"></a>Adiciones de características

- La siguiente refugio tipos de servicios vinculados sido agregados como orígenes de datos para las actividades de copia:
    - [HdfsLinkedService](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.hdfslinkedservice.aspx). Para obtener ejemplos e información conceptual, vea [mover datos de HDFS con el generador de datos](data-factory-hdfs-connector.md) . 
    - [OnPremisesOdbcLinkedService](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.onpremisesodbclinkedservice.aspx). Para obtener ejemplos e información conceptual, vea [mover datos de ODBC almacena usando el generador de datos de Azure](data-factory-odbc-connector.md) . 

## <a name="version-420"></a>Versión 4.2.0

### <a name="feature-additions"></a>Adiciones de características

- Se ha agregado el nuevo tipo de actividad de los siguientes: [AzureMLUpdateResourceActivity](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuremlupdateresourceactivity.aspx). Para obtener más información acerca de la actividad, consulte [modelos de actualización de Azure m mediante la actividad de recursos de actualización](data-factory-azure-ml-batch-execution-activity.md#updating-azure-ml-models-using-the-update-resource-activity).
- Un nuevo de la propiedad opcional [updateResourceEndpoint](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuremllinkedservice.updateresourceendpoint.aspx) se ha agregado a la [clase AzureMLLinkedService](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuremllinkedservice.aspx). 
- Propiedades [LongRunningOperationInitialTimeout](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.datafactorymanagementclient.longrunningoperationinitialtimeout.aspx) y [LongRunningOperationRetryTimeout](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.datafactorymanagementclient.longrunningoperationretrytimeout.aspx) se agregaron a la clase [DataFactoryManagementClient](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.datafactorymanagementclient.aspx) . 
- Permitir la configuración de los tiempos de espera para las llamadas de cliente con el servicio de generador de datos. 


## <a name="version-410"></a>Versión 4.1.0

### <a name="feature-additions"></a>Adiciones de características
* Se han agregado los siguientes tipos de servicio vinculadas: 
    * [AzureDataLakeStoreLinkedService](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakestorelinkedservice.aspx)
    * [AzureDataLakeAnalyticsLinkedService](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakeanalyticslinkedservice.aspx)
* Se han agregado los siguientes tipos de actividad: 
    * [DataLakeAnalyticsUSQLActivity](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datalakeanalyticsusqlactivity.aspx)
* Se han agregado los siguientes tipos de conjunto de datos: 
    * [AzureDataLakeStoreDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakestoredataset.aspx)
* Se han agregado los siguientes tipos de fuente y el receptor para copiar actividad:
    * [AzureDataLakeStoreSource](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakestoresource.aspx)
    * [AzureDataLakeStoreSink](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakestoresink.aspx)


## <a name="version-401"></a>Versión 4.0.1

### <a name="breaking-changes"></a>Cambios importantes
Las clases siguientes han cambiado de nombre. Los nuevos nombres estaban los nombres de clases originales antes de soltar 4.0.0. 
 
Nombre de 4.0.0 | Nombre en 4.0.1
:------------ | :------------ 
AzureSqlDataWarehouseDataset | [AzureSqlDataWarehouseTableDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuresqldatawarehousetabledataset.aspx)
AzureSqlDataset | [AzureSqlTableDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuresqltabledataset.aspx)
AzureDataset | [AzureTableDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuretabledataset.aspx)
OracleDataset | [OracleTableDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.oracletabledataset.aspx)
RelationalDataset | [RelationalTableDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.relationaltabledataset.aspx)
SqlServerDataset | [SqlServerTableDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.sqlservertabledataset.aspx)


## <a name="version-400"></a>Versión 4.0.0

### <a name="breaking-changes"></a>Cambios importantes



- Las siguientes clases/interfaces han cambiado de nombre.

| Nombre anterior | Nuevo nombre |
| :-------- | :-------- |
| ITableOperations | [IDatasetOperations](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.idatasetoperations.aspx) |  
| Tabla | [Conjunto de datos](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.dataset.aspx) | 
| TableProperties | [DatasetProperties](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasetproperties.aspx) | 
| TableTypeProprerties | [DatasetTypeProperties](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasettypeproperties.aspx) |
| TableCreateOrUpdateParameters | [DatasetCreateOrUpdateParameters](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasetcreateorupdateparameters.aspx) | 
| TableCreateOrUpdateResponse | [DatasetCreateOrUpdateResponse](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasetcreateorupdateresponse.aspx) | 
| TableGetResponse | [DatasetGetResponse](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasetgetresponse.aspx) | 
| TableListResponse | [DatasetListResponse](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasetlistresponse.aspx) |
| CreateOrUpdateWithRawJsonContentParameters | [DatasetCreateOrUpdateWithRawJsonContentParameters](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasetcreateorupdatewithrawjsoncontentparameters.aspx) | 
    

- Los métodos de **lista** devuelven resultados de la páginas ahora. Si la respuesta contiene una propiedad de **NextLink** no está vacío, la aplicación cliente debe continuar buscando la siguiente página hasta que se devuelven todas las páginas.  Aquí tenemos un ejemplo: 

        PipelineListResponse response = client.Pipelines.List("ResourceGroupName", "DataFactoryName");
        var pipelines = new List<Pipeline>(response.Pipelines);
    
        string nextLink = response.NextLink;
        while (string.IsNullOrEmpty(response.NextLink))
        {
            PipelineListResponse nextResponse = client.Pipelines.ListNext(nextLink);
            pipelines.AddRange(nextResponse.Pipelines);
    
            nextLink = nextResponse.NextLink;
        }
    
- **Lista** canalización API devuelve solo el resumen de una canalización en lugar de detalles completos. Por ejemplo, actividades en un resumen de canalización solo contienen el nombre y tipo.

### <a name="feature-additions"></a>Adiciones de características
- La clase [SqlDWSink](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.sqldwsink.aspx) es compatible con dos nuevas propiedades, **SliceIdentifierColumnName** y **SqlWriterCleanupScript**, para admitir idempotente copia al almacén de datos de SQL Azure. Vea el artículo de [Almacén de datos de SQL Azure](data-factory-azure-sql-data-warehouse-connector.md) , en concreto, las secciones de [mecanismo de 2](data-factory-azure-sql-data-warehouse-connector.md#mechanism-2) , para obtener más información acerca de estas propiedades y [mecanismo de 1](data-factory-azure-sql-data-warehouse-connector.md#mechanism-1) .

- Ahora admitimos ejecuta el procedimiento almacenado en orígenes de base de datos de SQL Azure y almacenamiento de datos de SQL Azure como parte de la actividad de copia. Las clases [SqlSource](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.sqlsource.aspx) y [SqlDWSource](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.sqldwsource.aspx) tienen las siguientes propiedades: **SqlReaderStoredProcedureName** y **StoredProcedureParameters**. Vea los artículos de la [Base de datos de SQL Azure](data-factory-azure-sql-connector.md#sqlsource) y [Almacenamiento de datos de SQL Azure](data-factory-azure-sql-data-warehouse-connector.md#sqldwsource) en Azure.com para obtener más información acerca de estas propiedades.  