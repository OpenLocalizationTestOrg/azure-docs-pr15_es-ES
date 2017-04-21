<properties 
    pageTitle="Invocar programa MapReduce del generador de datos de Azure" 
    description="Obtenga información sobre cómo procesar datos ejecutando programas MapReduce en un clúster de Azure HDInsight desde un generador de datos de Azure." 
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
    ms.date="09/12/2016" 
    ms.author="shlo"/>

# <a name="invoke-mapreduce-programs-from-data-factory"></a>Invocar MapReduce programas de la fábrica de datos
> [AZURE.SELECTOR]
[Sección](data-factory-hive-activity.md)  
[Cerdo](data-factory-pig-activity.md)  
[MapReduce](data-factory-map-reduce.md)  
[Transmisión de Hadoop](data-factory-hadoop-streaming-activity.md)
[El aprendizaje](data-factory-azure-ml-batch-execution-activity.md) 
[Procedimiento almacenado](data-factory-stored-proc-activity.md)
[Datos lago análisis U-SQL](data-factory-usql-activity.md)
[.NET personalizada](data-factory-use-custom-activities.md)

La actividad HDInsight MapReduce en un generador de datos [canalización](data-factory-create-pipelines.md) ejecuta programas MapReduce en [su propia](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) o [a petición](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) clúster HDInsight basado en Windows o Linux. En este artículo se basa en el artículo de [actividades de transformación de datos](data-factory-data-transformation-activities.md) , que ofrece una descripción general de transformación de datos y las actividades de transformación compatibles.

## <a name="introduction"></a>Introducción 
Una canalización en un generador de datos de Azure procesa datos en servicios de almacenamiento vinculado mediante servicios de cálculo vinculada. Contiene una secuencia de actividades, donde cada actividad realiza una operación de procesamiento específico. Este artículo describe el uso de la actividad de MapReduce HDInsight.
 
Vea [cerdo](data-factory-pig-activity.md) y la [sección](data-factory-hive-activity.md) para obtener más información acerca de cómo ejecutar secuencias de comandos de cerdo o subárbol en un clúster basado en Windows o Linux HDInsight de una canalización utilizando las actividades HDInsight cerdo y subárbol. 

## <a name="json-for-hdinsight-mapreduce-activity"></a>JSON HDInsight MapReduce actividad 

En la definición de JSON para la actividad de HDInsight: 
 
1. Establecer el **tipo** de la **actividad** a **HDInsight**.
3. Especifique el nombre de la clase para la propiedad del **nombre de clase** .
4. Especificar la ruta de acceso al archivo JAR incluido el nombre de archivo para la propiedad **jarFilePath** .
5. Especificar el servicio vinculado que hace referencia al almacenamiento de blobs de Azure que contiene el archivo JAR de propiedad **jarLinkedService** .   
6. Especificar argumentos para el programa MapReduce en la sección de **argumentos** . En tiempo de ejecución, vea algunos argumentos adicionales (por ejemplo: mapreduce.job.tags) desde el marco MapReduce. Para diferenciar los argumentos con los argumentos MapReduce, considere usar opción y valor como argumentos tal como se muestra en el ejemplo siguiente (- s,--entrada,--salida, etc., son opciones seguidos inmediatamente por sus valores).

        {
            "name": "MahoutMapReduceSamplePipeline",
            "properties": {
                "description": "Sample Pipeline to Run a Mahout Custom Map Reduce Jar. This job calcuates an Item Similarity Matrix to determine the similarity between 2 items",
                "activities": [
                    {
                        "type": "HDInsightMapReduce",
                        "typeProperties": {
                            "className": "org.apache.mahout.cf.taste.hadoop.similarity.item.ItemSimilarityJob",
                            "jarFilePath": "adfsamples/Mahout/jars/mahout-examples-0.9.0.2.2.7.1-34.jar",
                            "jarLinkedService": "StorageLinkedService",
                            "arguments": [
                                "-s",
                                "SIMILARITY_LOGLIKELIHOOD",
                                "--input",
                                "wasb://adfsamples@spestore.blob.core.windows.net/Mahout/input",
                                "--output",
                                "wasb://adfsamples@spestore.blob.core.windows.net/Mahout/output/",
                                "--maxSimilaritiesPerItem",
                                "500",
                                "--tempDir",
                                "wasb://adfsamples@spestore.blob.core.windows.net/Mahout/temp/mahout"
                            ]
                        },
                        "inputs": [
                            {
                                "name": "MahoutInput"
                            }
                        ],
                        "outputs": [
                            {
                                "name": "MahoutOutput"
                            }
                        ],
                        "policy": {
                            "timeout": "01:00:00",
                            "concurrency": 1,
                            "retry": 3
                        },
                        "scheduler": {
                            "frequency": "Hour",
                            "interval": 1
                        },
                        "name": "MahoutActivity",
                        "description": "Custom Map Reduce to generate Mahout result",
                        "linkedServiceName": "HDInsightLinkedService"
                    }
                ],
                "start": "2014-01-03T00:00:00Z",
                "end": "2014-01-04T00:00:00Z",
                "isPaused": false,
                "hubName": "mrfactory_hub",
                "pipelineMode": "Scheduled"
            }
        }
    
    

Puede usar la actividad de MapReduce HDInsight para ejecutar cualquier archivo de jar MapReduce en un clúster de HDInsight. En la siguiente definición de JSON de ejemplo de una canalización, la actividad de HDInsight está configurada para ejecutar un archivo JAR Mahout.

## <a name="sample-on-github"></a>Ejemplo de GitHub
Puede descargar un ejemplo para el uso de la actividad de MapReduce HDInsight desde: [Ejemplos del generador de datos en GitHub](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/JSON/MapReduce_Activity_Sample).  

## <a name="running-the-word-count-program"></a>Ejecutar el programa de recuento de palabras
La canalización en este ejemplo ejecuta el programa de mapa/Reduce el recuento de palabras en el clúster HDInsight de Azure.   

### <a name="linked-services"></a>Servicios vinculados
Primero, cree un servicio vinculado para vincular el almacenamiento de Azure que se usa el clúster HDInsight de Azure en el generador de datos de Azure. Si copia y pegar el código siguiente, no olvide reemplazar el **nombre de la cuenta** y **clave de cuenta** con el nombre y la clave de su almacenamiento de Azure. 

#### <a name="azure-storage-linked-service"></a>Servicio de almacenamiento vinculado de Azure

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
A continuación, cree un servicio vinculado para vincular el clúster de Azure HDInsight con el generador de datos de Azure. Si copia y pegar el código siguiente, reemplace el **nombre de clúster de HDInsight** con el nombre de su clúster HDInsight y cambiar los valores de nombre y contraseña de usuario.   

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
En este ejemplo la canalización de no aprovechar las entradas. Especifique un conjunto de datos de salida de la actividad de MapReduce HDInsight. Este conjunto de datos es simplemente un ficticia conjunto de datos que es necesario para la programación de canalización.  

    {
        "name": "MROutput",
        "properties": {
            "type": "AzureBlob",
            "linkedServiceName": "StorageLinkedService",
            "typeProperties": {
                "fileName": "WordCountOutput1.txt",
                "folderPath": "example/data/",
                "format": {
                    "type": "TextFormat",
                    "columnDelimiter": ","
                }
            },
            "availability": {
                "frequency": "Day",
                "interval": 1
            }
        }
    }

### <a name="pipeline"></a>Canalización
La canalización en este ejemplo tiene sólo una actividad es de tipo: HDInsightMapReduce. Algunas de las propiedades importantes en la JSON son: 

(Propiedad) | Notas
:-------- | :-----
tipo | El tipo debe estar establecido en **HDInsightMapReduce**. 
nombre de clase | Es el nombre de la clase: **wordcount**
jarFilePath | Ruta de acceso al archivo jar que contiene la clase. Si copia y pegar el código siguiente, no olvide cambiar el nombre del clúster. 
jarLinkedService | Almacenamiento de Azure vinculado servicio que contiene el archivo jar. Este servicio vinculada se refiere al almacenamiento de información que está asociado con el clúster de HDInsight. 
argumentos | El programa de wordcount toma dos argumentos, una entrada y un resultado. El archivo de entrada es el archivo davinci.txt.
frecuencia/intervalo | Los valores de estas propiedades que coincidan con el conjunto de datos de salida. 
linkedServiceName | hace referencia al servicio HDInsight vinculada que creó anteriormente.   

    {
        "name": "MRSamplePipeline",
        "properties": {
            "description": "Sample Pipeline to Run the Word Count Program",
            "activities": [
                {
                    "type": "HDInsightMapReduce",
                    "typeProperties": {
                        "className": "wordcount",
                        "jarFilePath": "<HDInsight cluster name>/example/jars/hadoop-examples.jar",
                        "jarLinkedService": "StorageLinkedService",
                        "arguments": [
                            "/example/data/gutenberg/davinci.txt",
                            "/example/data/WordCountOutput1"
                        ]
                    },
                    "outputs": [
                        {
                            "name": "MROutput"
                        }
                    ],
                    "policy": {
                        "timeout": "01:00:00",
                        "concurrency": 1,
                        "retry": 3
                    },
                    "scheduler": {
                        "frequency": "Day",
                        "interval": 1
                    },
                    "name": "MRActivity",
                    "linkedServiceName": "HDInsightLinkedService"
                }
            ],
            "start": "2014-01-03T00:00:00Z",
            "end": "2014-01-04T00:00:00Z"
        }
    }

## <a name="run-spark-programs"></a>Ejecutar programas de motor
Puede usar MapReduce actividad para ejecutar programas de motor en el clúster HDInsight Spark. Consulte [motor invocar programas de la fábrica de datos de Azure](data-factory-spark.md) para obtener información detallada.  

[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456


[adfgetstarted]: data-factory-copy-data-from-azure-blob-storage-to-sql-database.md
[adfgetstartedmonitoring]:data-factory-copy-data-from-azure-blob-storage-to-sql-database.md#monitor-pipelines 

[Developer Reference]: http://go.microsoft.com/fwlink/?LinkId=516908
[Azure Portal]: http://portal.azure.com
 
## <a name="see-also"></a>Vea también
- [Actividad de sección](data-factory-hive-activity.md)
- [Actividad de cerdo](data-factory-pig-activity.md)
- [Actividad de transmisión de Hadoop](data-factory-hadoop-streaming-activity.md)
- [Invocar programas motor](data-factory-spark.md)
- [Invocar secuencias de comandos de R](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/RunRScriptUsingADFSample)
