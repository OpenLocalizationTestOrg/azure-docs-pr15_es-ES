<properties 
    pageTitle="Actividad de sección" 
    description="Obtenga información sobre cómo puede usar la actividad de subárbol en un generador de datos de Azure para ejecutar consultas de la sección en un clúster de HDInsight en-petición o su propio." 
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
    ms.date="10/11/2016" 
    ms.author="shlo"/>

# <a name="hive-activity"></a>Actividad de sección
> [AZURE.SELECTOR]
[Sección](data-factory-hive-activity.md)  
[Cerdo](data-factory-pig-activity.md)  
[MapReduce](data-factory-map-reduce.md)  
[Transmisión de Hadoop](data-factory-hadoop-streaming-activity.md)
[El aprendizaje](data-factory-azure-ml-batch-execution-activity.md) 
[Procedimiento almacenado](data-factory-stored-proc-activity.md)
[Datos lago análisis U-SQL](data-factory-usql-activity.md)
[.NET personalizada](data-factory-use-custom-activities.md)

La actividad HDInsight subárbol en un generador de datos [canalización](data-factory-create-pipelines.md) ejecuta las consultas de la sección en [su propia](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) o [a petición](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) clúster HDInsight basado en Windows o Linux. En este artículo se basa en el artículo de [actividades de transformación de datos](data-factory-data-transformation-activities.md) , que ofrece una descripción general de transformación de datos y las actividades de transformación compatibles.

## <a name="syntax"></a>Sintaxis

    {
        "name": "Hive Activity",
        "description": "description",
        "type": "HDInsightHive",
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
          "script": "Hive script",
          "scriptPath": "<pathtotheHivescriptfileinAzureblobstorage>",
          "defines": {
            "param1": "param1Value"
          }
        },
       "scheduler": {
          "frequency": "Day",
          "interval": 1
        }
    }
    
## <a name="syntax-details"></a>Detalles de la sintaxis

(Propiedad) | Descripción | Obligatorio
-------- | ----------- | --------
nombre | Nombre de la actividad | Sí
Descripción | Texto que describe qué se usa la actividad | No
tipo | HDinsightHive | Sí
entradas | Entradas consumidos por la actividad de sección | No
resultados | Salidas creados mediante la actividad de sección | Sí 
linkedServiceName | Referencia al clúster HDInsight registrado como un servicio en el generador de datos vinculado | Sí 
secuencia de comandos | Especificar la secuencia de comandos de sección en línea | No
ruta de acceso de secuencia de comandos | Almacenar la secuencia de comandos de la sección en un almacenamiento de blobs de Windows Azure y proporcione la ruta de acceso al archivo. Utilice la propiedad 'script' o 'scriptPath'. No se pueden usar juntos. El nombre de archivo distingue mayúsculas de minúsculas. | No 
define | Especificar los parámetros como pares de clave y valor para hacer referencia a la secuencia de comandos de la sección con 'hiveconf'  | No

## <a name="example"></a>Ejemplo

Veamos un ejemplo de juegos registros análisis donde desea identificar el tiempo invertido por usuarios jugar iniciado por la empresa. 

El siguiente es un registro de juego de muestra, que es el punto y coma (`,`) separados y contiene los siguientes campos: ID, SessionStart, duración, SrcIPAddress y GameType.

    1809,2014-05-04 12:04:25.3470000,14,221.117.223.75,CaptureFlag
    1703,2014-05-04 06:05:06.0090000,16,12.49.178.247,KingHill
    1703,2014-05-04 10:21:57.3290000,10,199.118.18.179,CaptureFlag
    1809,2014-05-04 05:24:22.2100000,23,192.84.66.141,KingHill
    .....

La **sección de script** para procesar estos datos:

    DROP TABLE IF EXISTS HiveSampleIn; 
    CREATE EXTERNAL TABLE HiveSampleIn 
    (
        ProfileID       string, 
        SessionStart    string, 
        Duration        int, 
        SrcIPAddress    string, 
        GameType        string
    ) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION 'wasb://adfwalkthrough@<storageaccount>.blob.core.windows.net/samplein/'; 
    
    DROP TABLE IF EXISTS HiveSampleOut; 
    CREATE EXTERNAL TABLE HiveSampleOut 
    (   
        ProfileID   string, 
        Duration    int
    ) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION 'wasb://adfwalkthrough@<storageaccount>.blob.core.windows.net/sampleout/';
    
    INSERT OVERWRITE TABLE HiveSampleOut
    Select 
        ProfileID,
        SUM(Duration)
    FROM HiveSampleIn Group by ProfileID

Para ejecutar esta secuencia de comandos de la sección de una canalización de fábrica de datos, debe hacer lo siguiente

1. Crear un servicio vinculado para registrar [su propio HDInsight calcular clúster](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) o configurar [a petición HDInsight clúster de cálculo](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service). Vamos a llamar a este servicio vinculada "HDInsightLinkedService".
2. Crear un [servicio vinculada](data-factory-azure-blob-connector.md) para configurar la conexión con el almacenamiento de blobs de Windows Azure aloja los datos. Vamos a llamar este servicio vinculada "StorageLinkedService"
3. Crear [conjuntos de datos](data-factory-create-datasets.md) que apunta a la entrada y los datos de salida. Vamos a llamar el conjunto de datos de entrada "HiveSampleIn" y el conjunto de datos de salida "HiveSampleOut"
4. Copiar la consulta de la sección como un archivo de almacenamiento de blobs de Windows Azure configurado en el paso 2 #. Si el almacenamiento de los datos de host es diferente del hospedaje este archivo de consulta, cree un servicio de almacenamiento de Azure vinculado independiente y referencia a él en la actividad. Use **scriptPath **para especificar la ruta al archivo de consulta de sección y **scriptLinkedService** para especificar el almacenamiento de Azure que contiene el archivo de script. 

    > [AZURE.NOTE] También puede proporcionar la secuencia de comandos de sección en línea en la definición de actividad mediante la propiedad de **secuencia de comandos** . Que no se recomienda este enfoque como todos los caracteres especiales en la secuencia de comandos dentro de las necesidades de documento JSON escape y puede provocar problemas de depuración. El procedimiento recomendado es siga el paso 4 #.
5.  Crear una canalización con la actividad de HDInsightHive. La actividad de procesos/transformaciones de los datos.

        {
          "name": "HiveActivitySamplePipeline",
          "properties": {
            "activities": [
              {
                "name": "HiveActivitySample",
                "type": "HDInsightHive",
                "inputs": [
                  {
                    "name": "HiveSampleIn"
                  }
                ],
                "outputs": [
                  {
                    "name": "HiveSampleOut"
                  }
                ],
                "linkedServiceName": "HDInsightLinkedService",
                "typeproperties": {
                  "scriptPath": "adfwalkthrough\\scripts\\samplehive.hql",
                  "scriptLinkedService": "StorageLinkedService"
                },
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                }
              }
            ]
          }
        }

6.  Implemente la canalización. Vea el artículo [crear canalizaciones](data-factory-create-pipelines.md) para obtener información detallada. 
7.  Supervisar la canalización de las vistas de administración y supervisión de generador de datos. Consulte [supervisión y administrar datos fábrica canalizaciones](data-factory-monitor-manage-pipelines.md) artículo para obtener información detallada. 


## <a name="specifying-parameters-for-a-hive-script"></a>Especificar los parámetros de una secuencia de comandos de la sección  
En este ejemplo, los registros de juegos son ingestión diaria en el almacenamiento de blobs de Windows Azure y se almacenan en una carpeta particionada con fecha y hora. Desea agregar parámetros de la secuencia de comandos de la sección y pasar la ubicación de carpeta de entrada dinámicamente en tiempo de ejecución y también producir particiones con fecha y hora.

Para usar con parámetros de sección de script, haga lo siguiente

- Defina los parámetros de **define**.

        {
            "name": "HiveActivitySamplePipeline",
            "properties": {
            "activities": [
                {
                    "name": "HiveActivitySample",
                    "type": "HDInsightHive",
                    "inputs": [
                        {
                            "name": "HiveSampleIn"
                          }
                    ],
                    "outputs": [
                        {
                            "name": "HiveSampleOut"
                        }
                    ],
                    "linkedServiceName": "HDInsightLinkedService",
                    "typeproperties": {
                        "scriptPath": "adfwalkthrough\\scripts\\samplehive.hql",
                        "scriptLinkedService": "StorageLinkedService",
                        "defines": {
                            "Input": "$$Text.Format('wasb://adfwalkthrough@<storageaccountname>.blob.core.windows.net/samplein/yearno={0:yyyy}/monthno={0:%M}/dayno={0:%d}/', SliceStart)",
                            "Output": "$$Text.Format('wasb://adfwalkthrough@<storageaccountname>.blob.core.windows.net/sampleout/yearno={0:yyyy}/monthno={0:%M}/dayno={0:%d}/', SliceStart)"
                        },
                        "scheduler": {
                            "frequency": "Hour",
                            "interval": 1
                        }
                    }
                }
            ]
          }
        }

- En la secuencia de comandos de la sección, consulte el parámetro utilizando **${hiveconf:parameterName}**. 

        DROP TABLE IF EXISTS HiveSampleIn; 
        CREATE EXTERNAL TABLE HiveSampleIn 
        (
            ProfileID   string, 
            SessionStart    string, 
            Duration    int, 
            SrcIPAddress    string, 
            GameType    string
        ) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION '${hiveconf:Input}'; 
        
        DROP TABLE IF EXISTS HiveSampleOut; 
        CREATE EXTERNAL TABLE HiveSampleOut 
        (
            ProfileID   string, 
            Duration    int
        ) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION '${hiveconf:Output}';
        
        INSERT OVERWRITE TABLE HiveSampleOut
        Select 
            ProfileID,
            SUM(Duration)
        FROM HiveSampleIn Group by ProfileID


## <a name="see-also"></a>Vea también
- [Actividad de cerdo](data-factory-pig-activity.md)
- [MapReduce actividad](data-factory-map-reduce.md)
- [Actividad de transmisión de Hadoop](data-factory-hadoop-streaming-activity.md)
- [Invocar programas motor](data-factory-spark.md)
- [Invocar secuencias de comandos de R](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/RunRScriptUsingADFSample)









