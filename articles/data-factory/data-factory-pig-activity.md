<properties 
    pageTitle="Actividad de cerdo" 
    description="Obtenga información sobre cómo puede usar la actividad de cerdo en un generador de datos de Azure ejecuten secuencias de comandos de cerdo en un clúster de HDInsight en-petición o su propio." 
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
    ms.date="08/31/2016" 
    ms.author="shlo"/>

# <a name="pig-activity"></a>Actividad de cerdo
> [AZURE.SELECTOR]
[Sección](data-factory-hive-activity.md)  
[Cerdo](data-factory-pig-activity.md)  
[MapReduce](data-factory-map-reduce.md)  
[Transmisión de Hadoop](data-factory-hadoop-streaming-activity.md)
[El aprendizaje](data-factory-azure-ml-batch-execution-activity.md) 
[Procedimiento almacenado](data-factory-stored-proc-activity.md)
[Datos lago análisis U-SQL](data-factory-usql-activity.md)
[.NET personalizada](data-factory-use-custom-activities.md)

La actividad de cerdo HDInsight en un generador de datos [canalización](data-factory-create-pipelines.md) ejecuta consultas de cerdo en [su propia](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) o [a petición](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) clúster HDInsight basado en Windows o Linux. En este artículo se basa en el artículo de [actividades de transformación de datos](data-factory-data-transformation-activities.md) , que ofrece una descripción general de transformación de datos y las actividades de transformación compatibles.

## <a name="syntax"></a>Sintaxis

    {
        "name": "HiveActivitySamplePipeline",
        "properties": {
        "activities": [
            {
                "name": "Pig Activity",
                "description": "description",
                "type": "HDInsightPig",
                "inputs": [
                    {
                        "name": "input tables"
                    }
                ],
                "outputs": [
                    {
                        "name": "output tables"
                    }
                ],
                "linkedServiceName": "MyHDInsightLinkedService",
                "typeProperties": {
                    "script": "Pig script",
                    "scriptPath": "<pathtothePigscriptfileinAzureblobstorage>",
                    "defines": {
                        "param1": "param1Value"
                    }
                },
                "scheduler": {
                    "frequency": "Day",
                    "interval": 1
                }
            }
        ]
      }
    }

## <a name="syntax-details"></a>Detalles de la sintaxis

(Propiedad) | Descripción | Obligatorio
-------- | ----------- | --------
nombre | Nombre de la actividad | Sí
Descripción | Texto que describe qué se usa la actividad | No
tipo | HDinsightPig | Sí
entradas | Una o más entradas consumidas la actividad de cerdo | No
resultados | Uno o varios de los resultados producidos por la actividad de cerdo | Sí
linkedServiceName | Referencia al clúster HDInsight registrado como un servicio en el generador de datos vinculado | Sí
secuencia de comandos | Especificar la secuencia de comandos de cerdo en línea | No
ruta de acceso de secuencia de comandos | Almacenar la secuencia de comandos de cerdo en un almacenamiento de blobs de Windows Azure y proporcione la ruta de acceso al archivo. Utilice la propiedad 'script' o 'scriptPath'. No se pueden usar juntos. El nombre de archivo distingue mayúsculas de minúsculas. | No
define | Especificar los parámetros como pares de clave y valor para hacer referencia a la secuencia de comandos de cerdo | No

## <a name="example"></a>Ejemplo

Veamos un ejemplo de juegos registros análisis donde desea identificar el tiempo invertido reproductores jugar iniciado por la empresa.
 
El registro de juego de ejemplo siguiente es un archivo separado por comas (,). Contiene los siguientes campos: ID, SessionStart, duración, SrcIPAddress y GameType.

    1809,2014-05-04 12:04:25.3470000,14,221.117.223.75,CaptureFlag
    1703,2014-05-04 06:05:06.0090000,16,12.49.178.247,KingHill
    1703,2014-05-04 10:21:57.3290000,10,199.118.18.179,CaptureFlag
    1809,2014-05-04 05:24:22.2100000,23,192.84.66.141,KingHill
    .....

La **secuencia de comandos de cerdo** procesar estos datos:

    PigSampleIn = LOAD 'wasb://adfwalkthrough@anandsub14.blob.core.windows.net/samplein/' USING PigStorage(',') AS (ProfileID:chararray, SessionStart:chararray, Duration:int, SrcIPAddress:chararray, GameType:chararray);
    
    GroupProfile = Group PigSampleIn all;
    
    PigSampleOut = Foreach GroupProfile Generate PigSampleIn.ProfileID, SUM(PigSampleIn.Duration);
    
    Store PigSampleOut into 'wasb://adfwalkthrough@anandsub14.blob.core.windows.net/sampleoutpig/' USING PigStorage (',');

Para ejecutar esta secuencia de comandos de cerdo en una canalización del generador de datos, haga lo siguiente:

1. Crear un servicio vinculado para registrar [su propio HDInsight calcular clúster](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) o configurar [a petición HDInsight clúster de cálculo](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service). Vamos a llamar a este servicio vinculado **HDInsightLinkedService**.
2.  Crear un [servicio vinculada](data-factory-azure-blob-connector.md) para configurar la conexión con el almacenamiento de blobs de Windows Azure aloja los datos. Vamos a llamar a este servicio vinculado **StorageLinkedService**.
3.  Crear [conjuntos de datos](data-factory-create-datasets.md) que apunta a la entrada y los datos de salida. Vamos a llamar el conjunto de datos de entrada **PigSampleIn** y el conjunto de datos de salida **PigSampleOut**.
4.  Copie la consulta cerdo en un archivo de almacenamiento de blobs de Azure configurado en el paso 2 #. Si el almacenamiento de Azure que hospeda los datos es diferente del que aloja el archivo de consulta, cree un servicio de almacenamiento de Azure vinculado independiente. Hacer referencia al servicio vinculado en la configuración de la actividad. Use **scriptPath **para especificar la ruta de acceso al archivo de script de cerdo y **scriptLinkedService**. 
    
    > [AZURE.NOTE] También puede proporcionar la secuencia de comandos de cerdo en línea en la definición de actividad mediante la propiedad de **secuencia de comandos** . Sin embargo, que no se recomienda este enfoque como todos los caracteres especiales en el script tiene que ser de escape y puede provocar problemas de depuración. El procedimiento recomendado es siga el paso 4 #.
5. Crear la canalización con la actividad de HDInsightPig. Esta actividad procesa los datos de entrada ejecutando el script de cerdo en clúster HDInsight.

        {
          "name": "PigActivitySamplePipeline",
          "properties": {
            "activities": [
              {
                "name": "PigActivitySample",
                "type": "HDInsightPig",
                "inputs": [
                  {
                    "name": "PigSampleIn"
                  }
                ],
                "outputs": [
                  {
                    "name": "PigSampleOut"
                  }
                ],
                "linkedServiceName": "HDInsightLinkedService",
                "typeproperties": {
                  "scriptPath": "adfwalkthrough\\scripts\\enrichlogs.pig",
                  "scriptLinkedService": "StorageLinkedService"
                },
                "scheduler": {
                    "frequency": "Day",
                    "interval": 1
                }
              }
            ]
          }
        } 
6. Implemente la canalización. Vea el artículo [crear canalizaciones](data-factory-create-pipelines.md) para obtener información detallada. 
7. Supervisar la canalización de las vistas de administración y supervisión de generador de datos. Consulte [supervisión y administrar datos fábrica canalizaciones](data-factory-monitor-manage-pipelines.md) artículo para obtener información detallada.

## <a name="specifying-parameters-for-a-pig-script"></a>Especificar los parámetros de una secuencia de comandos de cerdo 

Considere el siguiente ejemplo: juego de registros se ingestión diaria en el almacenamiento de blobs de Windows Azure y se almacenan en una carpeta de fecha en función de particiones y la hora. Desea agregar parámetros de la secuencia de comandos de cerdo y pasar la ubicación de carpeta de entrada dinámicamente en tiempo de ejecución y también producir particiones con fecha y hora.
 
Para usar la secuencia de comandos de cerdo con parámetros, haga lo siguiente:

- Defina los parámetros de **define**.

        {
            "name": "PigActivitySamplePipeline",
            "properties": {
            "activities": [
                {
                    "name": "PigActivitySample",
                    "type": "HDInsightPig",
                    "inputs": [
                        {
                            "name": "PigSampleIn"
                        }
                    ],
                    "outputs": [
                        {
                            "name": "PigSampleOut"
                        }
                    ],
                    "linkedServiceName": "HDInsightLinkedService",
                    "typeproperties": {
                        "scriptPath": "adfwalkthrough\\scripts\\samplepig.hql",
                        "scriptLinkedService": "StorageLinkedService",
                        "defines": {
                            "Input": "$$Text.Format('wasb: //adfwalkthrough@<storageaccountname>.blob.core.windows.net/samplein/yearno={0: yyyy}/monthno={0: %M}/dayno={0: %d}/',SliceStart)",
                            "Output": "$$Text.Format('wasb://adfwalkthrough@<storageaccountname>.blob.core.windows.net/sampleout/yearno={0:yyyy}/monthno={0:%M}/dayno={0:%d}/', SliceStart)"
                        }
                    },
                    "scheduler": {
                        "frequency": "Day",
                        "interval": 1
                    }
                }
            ]
          }
        }  
      
- En la secuencia de comandos de cerdo, consulte los parámetros con '**$parameterName**' tal como se muestra en el ejemplo siguiente:

        PigSampleIn = LOAD '$Input' USING PigStorage(',') AS (ProfileID:chararray, SessionStart:chararray, Duration:int, SrcIPAddress:chararray, GameType:chararray);   
        GroupProfile = Group PigSampleIn all;       
        PigSampleOut = Foreach GroupProfile Generate PigSampleIn.ProfileID, SUM(PigSampleIn.Duration);      
        Store PigSampleOut into '$Output' USING PigStorage (','); 


## <a name="see-also"></a>Vea también
- [Actividad de sección](data-factory-hive-activity.md)
- [MapReduce actividad](data-factory-map-reduce.md)
- [Actividad de transmisión de Hadoop](data-factory-hadoop-streaming-activity.md)
- [Invocar programas motor](data-factory-spark.md)
- [Invocar secuencias de comandos de R](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/RunRScriptUsingADFSample)


