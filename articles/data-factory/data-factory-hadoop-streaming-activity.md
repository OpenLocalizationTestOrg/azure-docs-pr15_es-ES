<properties 
    pageTitle="Actividad de transmisión de Hadoop" 
    description="Obtenga información sobre cómo puede usar la actividad de transmisión de Hadoop un generador de datos de Azure para ejecutar programas de transmisión de Hadoop en un clúster de HDInsight en-petición o su propio." 
    services="data-factory" 
    documentationCenter="" 
    authors="sharonlo101" 
    manager="jhubbard" 
    editor="monicar"/>

<tags 
    ms.service="data-factory" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/20/2016" 
    ms.author="shlo"/>

# <a name="hadoop-streaming-activity"></a>Actividad de transmisión de Hadoop
> [AZURE.SELECTOR]
[Sección](data-factory-hive-activity.md)  
[Cerdo](data-factory-pig-activity.md)  
[MapReduce](data-factory-map-reduce.md)  
[Transmisión de Hadoop](data-factory-hadoop-streaming-activity.md)
[El aprendizaje](data-factory-azure-ml-batch-execution-activity.md) 
[Procedimiento almacenado](data-factory-stored-proc-activity.md)
[Datos lago análisis U-SQL](data-factory-usql-activity.md)
[.NET personalizada](data-factory-use-custom-activities.md)

Puede usar la actividad de HDInsightStreamingActivity invocar un trabajo Streaming Hadoop de una canalización de generador de datos de Azure. El siguiente fragmento JSON muestra la sintaxis de la HDInsightStreamingActivity en un archivo de canalización JSON. 

La actividad de transmisión de HDInsight en un generador de datos [canalización](data-factory-create-pipelines.md) ejecuta programas de transmisión de Hadoop en [su propia](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) o [a petición](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) clúster HDInsight basado en Windows o Linux. En este artículo se basa en el artículo de [actividades de transformación de datos](data-factory-data-transformation-activities.md) , que ofrece una descripción general de transformación de datos y las actividades de transformación compatibles.

## <a name="json-sample"></a>Ejemplo JSON
El clúster HDInsight se rellena automáticamente con los programas de ejemplo (wc.exe y cat.exe) y datos (davinci.txt). De forma predeterminada, el nombre del contenedor que se usa el clúster HDInsight es el nombre del propio clúster. Por ejemplo, si el nombre de clúster es myhdicluster, el nombre del contenedor de blobs asociado sería myhdicluster. 

    {
        "name": "HadoopStreamingPipeline",
        "properties": {
            "description": "Hadoop Streaming Demo",
            "activities": [
                {
                    "type": "HDInsightStreaming",
                    "typeProperties": {
                        "mapper": "cat.exe",
                        "reducer": "wc.exe",
                        "input": "wasb://<nameofthecluster>@spestore.blob.core.windows.net/example/data/gutenberg/davinci.txt",
                        "output": "wasb://<nameofthecluster>@spestore.blob.core.windows.net/example/data/StreamingOutput/wc.txt",
                        "filePaths": [
                            "<nameofthecluster>/example/apps/wc.exe",
                            "<nameofthecluster>/example/apps/cat.exe"
                        ],
                        "fileLinkedService": "StorageLinkedService",
                        "getDebugInfo": "Failure"
                    },
                    "outputs": [
                        {
                            "name": "StreamingOutputDataset"
                        }
                    ],
                    "policy": {
                        "timeout": "01:00:00",
                        "concurrency": 1,
                        "executionPriorityOrder": "NewestFirst",
                        "retry": 1
                    },
                    "scheduler": {
                        "frequency": "Day",
                        "interval": 1
                    },
                    "name": "RunHadoopStreamingJob",
                    "description": "Run a Hadoop streaming job",
                    "linkedServiceName": "HDInsightLinkedService"
                }
            ],
            "start": "2014-01-04T00:00:00Z",
            "end": "2014-01-05T00:00:00Z"
        }
    }

Tenga en cuenta los siguientes puntos:

1. Establecer el **linkedServiceName** en el nombre del servicio vinculado que apunta al clúster HDInsight en que se ejecuta el trabajo mapreduce streaming.
2. Establecer el tipo de la actividad a **HDInsightStreaming**.
3. Para la propiedad **asignador** , especifique el nombre de asignador ejecutable. En el ejemplo, cat.exe es el asignador ejecutable.
4. Para la propiedad **reductor** , especifique el nombre de ejecutable reductor. En el ejemplo, wc.exe es el ejecutable reductor.
5. Para la propiedad de tipo de **entrada** , especifique el archivo de entrada (incluida la ubicación) para el asignador. En el ejemplo: "wasb://adfsample@ <account name>.blob.core.windows.net/example/data/gutenberg/davinci.txt ": adfsample es el contenedor de blob, datos de ejemplo/Gutenberg es la carpeta y davinci.txt es el blob.
6. Para la propiedad de tipo de **salida** , especifique el archivo de salida (incluida la ubicación) el reductor. El resultado de la tarea de transmisión de Hadoop se escribe en la ubicación especificada para esta propiedad.
7. En la sección **filePaths** , especifique las rutas de acceso para los ejecutables asignador y reductor. En el ejemplo: "adfsample/example/apps/wc.exe", adfsample es el contenedor de blob, ejemplo/aplicaciones es la carpeta y wc.exe es el ejecutable.
8. Para la propiedad **fileLinkedService** , especifique el servicio de almacenamiento de Azure vinculado que representa el almacenamiento de Azure que contiene los archivos que se especifica en la sección filePaths.
9. Para la propiedad de **argumentos** , especifique los argumentos para el trabajo streaming.
10. La propiedad **getDebugInfo** es un elemento opcional. Cuando se establece en error, se descargarán los registros de error de. Cuando se establece a todos, registros siempre se descargan independientemente del estado de ejecución.

> [AZURE.NOTE] Como se muestra en el ejemplo, especifique un conjunto de datos de salida de la actividad de transmisión de Hadoop para la propiedad **resultados** . Este conjunto de datos es simplemente un ficticia conjunto de datos que es necesario para la programación de canalización. No debe especificar cualquier entrada del conjunto de datos de la actividad de la propiedad de **entradas** .  

    
## <a name="example"></a>Ejemplo
La canalización de este tutorial, ejecuta el programa de mapa o reducir transmisiones de recuento de palabras en el clúster HDInsight de Azure. 

### <a name="linked-services"></a>Servicios vinculados

#### <a name="azure-storage-linked-service"></a>Servicio de almacenamiento vinculado de Azure
Primero, cree un servicio vinculado para vincular el almacenamiento de Azure que se usa el clúster HDInsight de Azure en el generador de datos de Azure. Si copia y pegar el código siguiente, no olvide reemplazar el nombre de la cuenta y clave de cuenta con el nombre y la clave de su almacenamiento de Azure. 

    {
        "name": "StorageLinkedService",
        "properties": {
            "type": "AzureStorage",
            "typeProperties": {
                "connectionString": "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>"
            }
        }
    }

#### <a name="azure-hdinsight-linked-service"></a>Servicio de Azure HDInsight vinculado
A continuación, cree un servicio vinculado para vincular el clúster de Azure HDInsight con el generador de datos de Azure. Si copia y pegar el código siguiente, reemplace el nombre de clúster de HDInsight con el nombre de su clúster HDInsight y cambiar los valores de nombre y contraseña de usuario. 
    
    {
        "name": "HDInsightLinkedService",
        "properties": {
            "type": "HDInsight",
            "typeProperties": {
                "clusterUri": "https://<HDInsight cluster name>.azurehdinsight.net",
                "userName": "admin",
                "password": "**********",
                "linkedServiceName": "StorageLinkedService"
            }
        }
    }

### <a name="datasets"></a>Conjuntos de datos

#### <a name="output-dataset"></a>Conjunto de datos de salida
En este ejemplo la canalización de no aprovechar las entradas. Especifique un conjunto de datos de salida de la actividad de transmisión de HDInsight. Este conjunto de datos es simplemente un ficticia conjunto de datos que es necesario para la programación de canalización. 

    {
        "name": "StreamingOutputDataset",
        "properties": {
            "published": false,
            "type": "AzureBlob",
            "linkedServiceName": "StorageLinkedService",
            "typeProperties": {
                "folderPath": "adftutorial/streamingdata/",
                "format": {
                    "type": "TextFormat",
                    "columnDelimiter": ","
                },
            },
            "availability": {
                "frequency": "Day",
                "interval": 1
            }
        }
    }

### <a name="pipeline"></a>Canalización

La canalización en este ejemplo tiene sólo una actividad es de tipo: **HDInsightStreaming**. 

El clúster HDInsight se rellena automáticamente con los programas de ejemplo (wc.exe y cat.exe) y datos (davinci.txt). De forma predeterminada, el nombre del contenedor que se usa el clúster HDInsight es el nombre del propio clúster. Por ejemplo, si el nombre de clúster es myhdicluster, el nombre del contenedor de blobs asociado sería myhdicluster.  

    {
        "name": "HadoopStreamingPipeline",
        "properties": {
            "description": "Hadoop Streaming Demo",
            "activities": [
                {
                    "type": "HDInsightStreaming",
                    "typeProperties": {
                        "mapper": "cat.exe",
                        "reducer": "wc.exe",
                        "input": "wasb://<blobcontainer>@spestore.blob.core.windows.net/example/data/gutenberg/davinci.txt",
                        "output": "wasb://<blobcontainer>@spestore.blob.core.windows.net/example/data/StreamingOutput/wc.txt",
                        "filePaths": [
                            "<blobcontainer>/example/apps/wc.exe",
                            "<blobcontainer>/example/apps/cat.exe"
                        ],
                        "fileLinkedService": "StorageLinkedService"
                    },
                    "outputs": [
                        {
                            "name": "StreamingOutputDataset"
                        }
                    ],
                    "policy": {
                        "timeout": "01:00:00",
                        "concurrency": 1,
                        "executionPriorityOrder": "NewestFirst",
                        "retry": 1
                    },
                    "scheduler": {
                        "frequency": "Day",
                        "interval": 1
                    },
                    "name": "RunHadoopStreamingJob",
                    "description": "Run a Hadoop streaming job",
                    "linkedServiceName": "HDInsightLinkedService"
                }
            ],
            "start": "2014-01-04T00:00:00Z",
            "end": "2014-01-05T00:00:00Z"
        }
    }

## <a name="see-also"></a>Vea también
- [Actividad de sección](data-factory-hive-activity.md)
- [Actividad de cerdo](data-factory-pig-activity.md)
- [MapReduce actividad](data-factory-map-reduce.md)
- [Invocar programas motor](data-factory-spark.md)
- [Invocar secuencias de comandos de R](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/RunRScriptUsingADFSample)

