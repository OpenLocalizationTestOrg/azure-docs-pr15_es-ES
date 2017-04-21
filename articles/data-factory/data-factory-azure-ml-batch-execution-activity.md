<properties 
    pageTitle="Usar las actividades de aprendizaje de equipo | Microsoft Azure" 
    description="Describe cómo crear crear predictivas canalizaciones con el generador de datos de Azure y aprendizaje del equipo de Azure" 
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
    ms.date="09/06/2016" 
    ms.author="shlo"/>

# <a name="create-predictive-pipelines-using-azure-machine-learning-activities"></a>Crear predictivas canalizaciones con actividades de aprendizaje del equipo de Azure   
> [AZURE.SELECTOR]
[Sección](data-factory-hive-activity.md)  
[Cerdo](data-factory-pig-activity.md)  
[MapReduce](data-factory-map-reduce.md)  
[Transmisión de Hadoop](data-factory-hadoop-streaming-activity.md)
[El aprendizaje](data-factory-azure-ml-batch-execution-activity.md) 
[Procedimiento almacenado](data-factory-stored-proc-activity.md)
[Datos lago análisis U-SQL](data-factory-usql-activity.md)
[.NET personalizada](data-factory-use-custom-activities.md)

## <a name="introduction"></a>Introducción

[Aprendizaje del equipo de Azure](https://azure.microsoft.com/documentation/services/machine-learning/) le permite crear, probar e implementar soluciones de análisis de predicción. Desde el punto de vista alto nivel, se realiza en tres pasos: 

1. **Crear un ensayo de aprendizaje**. Realizar este paso mediante la Studio m de Azure. Studio de m es un entorno de desarrollo en colaboración visual que usa para entrenar y probar un modelo de análisis predictiva con datos de entrenamiento.
2. **Convertir un ensayo predictivo**. Una vez que ha sido formado del modelo con los datos existentes y esté listo para usarlo para puntuación nuevos datos, preparar y simplificar el ensayo de puntuación.
3. **Implementarlo como un servicio web**. Puede publicar su ensayo puntuación como un servicio web de Azure. Puede enviar datos a su modelo a través de este extremo de servicio web y recibir predicciones de resultado para el modelo.  

Generador de datos de Azure le permite crear fácilmente canalizaciones que usan un publicados [Aprendizaje del equipo de Azure] [ azure-machine-learning] servicio para análisis predictivo web. Vea [Introducción al generador de datos de Azure](data-factory-introduction.md) y [crear su primer canalización](data-factory-build-your-first-pipeline.md) artículos para comenzar rápidamente con el servicio de generador de datos de Azure. 

Uso de la **Actividad de ejecución de lotes** en una canalización del generador de datos de Azure, puede invocar un servicio web de Azure m para realizar predicciones en los datos por lotes. Vea la sección [invocar un m Azure web mediante la actividad de ejecución por lotes del servicio](#invoking-an-azure-ml-web-service-using-the-batch-execution-activity) para obtener información detallada.

Con el tiempo, los modelos de predictivos en la v de Azure puntuación experimentación necesitan aprender a utilizarla con nuevos conjuntos de datos de entrada. Puede reciclaje un modelo de Azure m de una canalización de fábrica datos realizando los siguientes pasos: 

1. Publicar el ensayo de formación (ensayo no predictiva) como un servicio web. Siga este paso en Azure m Studio como hizo para exponer ensayo predictiva como un servicio web en el escenario anterior.
2. Use la actividad de ejecución de Azure m lote para llamar al servicio web para el ensayo de aprendizaje. Básicamente, puede usar la actividad de ejecución de lotes de Azure m para invocar el servicio web de formación y puntuación servicio web. 
  
Cuando haya terminado con la formación, que desea actualizar el servicio web puntuación (se exponen como un servicio web predictiva ensayo) con el modelo recién capacitado. Estos son los pasos: 

1. Agregar un punto final de no predeterminada para el servicio web puntuación. No se puede actualizar el extremo predeterminado del servicio web, por lo que necesita para crear un extremo no predeterminada con el portal de Azure. Vea el artículo [Crear extremos](../machine-learning/machine-learning-create-endpoint.md) para obtener información conceptual y procedimientos.
2. Actualizar servicios existentes de m Azure vinculado de puntuación para usar el extremo no predeterminada. Empezar a usar el nuevo extremo del uso del servicio web que se actualiza.
3. Use la **Actividad de Azure m actualizar recursos** para actualizar el servicio web con el modelo recién capacitado.  

Para obtener información detallada, consulte la sección [modelos m de Azure actualizar mediante la actividad de recursos de actualización](#updating-azure-ml-models-using-the-update-resource-activity) . 

## <a name="invoking-a-web-service-using-batch-execution-activity"></a>Invocar un servicio web mediante la actividad de ejecución de lotes

Usar el generador de datos de Azure organizar procesamiento y movimiento de datos y realice ejecución por lotes utilizando el aprendizaje Azure. Estos son los pasos de nivel superior:

1. Crear un servicio de aprendizaje del equipo de Azure vinculado. Se necesita lo siguiente:
    1. **Solicitar URI** de la ejecución del lote API. Puede encontrar el URI solicitar haciendo clic en el vínculo de **Ejecución por lotes** en la página de servicios web.
    1. **Clave API** para el servicio web de aprendizaje del equipo de Azure publicado. Puede encontrar la clave de API haciendo clic en el servicio web que se ha publicado. 
 2. Use la actividad **AzureMLBatchExecution** .

    ![Panel de aprendizaje de equipo](./media/data-factory-azure-ml-batch-execution-activity/AzureMLDashboard.png)

    ![URI del lote](./media/data-factory-azure-ml-batch-execution-activity/batch-uri.png)


### <a name="scenario-experiments-using-web-service-inputsoutputs-that-refer-to-data-in-azure-blob-storage"></a>Escenario: Experimentación con Web servicio entradas/salidas que hacen referencia a datos en el almacenamiento de blobs de Windows Azure
En este escenario, el servicio Web de aprendizaje del equipo de Azure lleva a cabo predicciones con datos desde un archivo en un almacenamiento de blobs de Windows Azure y almacena los resultados de predicción en el almacenamiento de blobs de Windows. El siguiente JSON define una canalización del generador de datos con una actividad AzureMLBatchExecution. La actividad tiene el conjunto de datos **DecisionTreeInputBlob** como entrada y **DecisionTreeResultBlob** como salida. La **DecisionTreeInputBlob** se pasa como entrada al servicio web mediante la propiedad JSON **webServiceInput** . La **DecisionTreeResultBlob** se pasa como salida al servicio Web mediante la propiedad JSON **webServiceOutputs** .  

> [AZURE.IMPORTANT] 
> Si el servicio web tarda varias entradas, use la propiedad **webServiceInputs** en lugar de usar **webServiceInput**. Consulte la sección [servicio Web requiere varias entradas](#web-service-requires-multiple-inputs) para obtener un ejemplo de uso de la propiedad webServiceInputs.
>  
> Conjuntos de datos que hace referencia la **webServiceInput**/propiedades**webServiceInputs** y **webServiceOutputs** (en **typeProperties**) también deben incluirse en la actividad de **entradas** y **salidas**.
> 
> En la prueba de Azure m parámetros globales y puertos de salida y entrada de servicio web tienen nombres predeterminados ("Entrada1", "Entrada2") que puede personalizar. Los nombres que usa para webServiceInputs, webServiceOutputs y configuración de globalParameters deben coincidir exactamente con los nombres de la experimentación. Puede ver la carga de solicitud de ejemplo en la página de Ayuda de ejecución de proceso por lotes el extremo de Azure v verificar la asignación esperada. 


    {
      "name": "PredictivePipeline",
      "properties": {
        "description": "use AzureML model",
        "activities": [
          {
            "name": "MLActivity",
            "type": "AzureMLBatchExecution",
            "description": "prediction analysis on batch input",
            "inputs": [
              {
                "name": "DecisionTreeInputBlob"
              }
            ],
            "outputs": [
              {
                "name": "DecisionTreeResultBlob"
              }
            ],
            "linkedServiceName": "MyAzureMLLinkedService",
            "typeProperties":
            {
                "webServiceInput": "DecisionTreeInputBlob",
                "webServiceOutputs": {
                    "output1": "DecisionTreeResultBlob"
                }                
            },
            "policy": {
              "concurrency": 3,
              "executionPriorityOrder": "NewestFirst",
              "retry": 1,
              "timeout": "02:00:00"
            }
          }
        ],
        "start": "2016-02-13T00:00:00Z",
        "end": "2016-02-14T00:00:00Z"
      }
    }

> [AZURE.NOTE] Solo las entradas y los resultados de la actividad de AzureMLBatchExecution se pueden pasar como parámetros al servicio Web. Por ejemplo, en el fragmento de código anterior JSON, DecisionTreeInputBlob es una entrada de la actividad AzureMLBatchExecution, que se pasa como entrada al servicio Web mediante un parámetro webServiceInput.   

### <a name="example"></a>Ejemplo

Este ejemplo usa el almacenamiento de Azure para almacenar los datos de entrada y salidos. 

Le recomendamos que va a [crear su primer canalización con el generador de datos] [ adf-build-1st-pipeline] tutorial antes de pasar por este ejemplo. Use el Editor del generador de datos para crear artefactos del generador de datos (servicios vinculados, los conjuntos de datos, canalización) en este ejemplo.   
 

1. Crear un **servicio vinculada** para el **Almacenamiento de Azure**. Si los archivos de entrada y salidos se encuentran en cuentas de almacenamiento diferente, tiene dos servicios vinculados. Este es un ejemplo JSON:

        {
          "name": "StorageLinkedService",
          "properties": {
            "type": "AzureStorage",
            "typeProperties": {
              "connectionString": "DefaultEndpointsProtocol=https;AccountName=[acctName];AccountKey=[acctKey]"
            }
          }
        }

2. Crear la **entrada** del generador de datos de Azure **conjunto de datos**. A diferencia de algunos otros datos fábrica conjuntos de datos, estos conjuntos de datos deben contener valores **folderPath** y **nombre de archivo** . Puede usar particiones causado cada ejecución por lotes (cada sector datos) procesar o producir único entrada y salida de archivos. Debe incluir algunas actividades en sentido ascendente para transformar la entrada en el formato de archivo CSV y colóquelo en la cuenta de almacenamiento para cada sector. En ese caso, no incluiría la configuración **externa** y **externalData** que se muestra en el ejemplo siguiente, y su DecisionTreeInputBlob sería el conjunto de datos de resultado de una actividad diferente.

        {
          "name": "DecisionTreeInputBlob",
          "properties": {
            "type": "AzureBlob",
            "linkedServiceName": "StorageLinkedService",
            "typeProperties": {
              "folderPath": "azuremltesting/input",
              "fileName": "in.csv",
              "format": {
                "type": "TextFormat",
                "columnDelimiter": ","
              }
            },
            "external": true,
            "availability": {
              "frequency": "Day",
              "interval": 1
            },
            "policy": {
              "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
              }
            }
          }
        }
    
    El archivo csv de entrada debe tener la fila de encabezado de columna. Si está utilizando la **Actividad de copia** para crear o mover la csv en el almacenamiento de blobs, debe establecer la propiedad de receptor **blobWriterAddHeader** en **true**. Por ejemplo:
    
         sink: 
         {
             "type": "BlobSink",     
             "blobWriterAddHeader": true 
         }
     
    Si el archivo csv no tiene la fila de encabezado, verá el siguiente error: Error en la actividad de **: Error al leer la cadena. Símbolo inesperado: StartObject. Ruta de acceso ", línea 1, posición 1**.
3. Crear la **salida** del **conjunto de datos**del generador de datos de Azure. Este ejemplo usa particiones para crear una ruta de salida único para cada ejecución del sector. Sin la división, la actividad debería sobrescribir el archivo.

        {
          "name": "DecisionTreeResultBlob",
          "properties": {
            "type": "AzureBlob",
            "linkedServiceName": "StorageLinkedService",
            "typeProperties": {
              "folderPath": "azuremltesting/scored/{folderpart}/",
              "fileName": "{filepart}result.csv",
              "partitionedBy": [
                {
                  "name": "folderpart",
                  "value": {
                    "type": "DateTime",
                    "date": "SliceStart",
                    "format": "yyyyMMdd"
                  }
                },
                {
                  "name": "filepart",
                  "value": {
                    "type": "DateTime",
                    "date": "SliceStart",
                    "format": "HHmmss"
                  }
                }
              ],
              "format": {
                "type": "TextFormat",
                "columnDelimiter": ","
              }
            },
            "availability": {
              "frequency": "Day",
              "interval": 15
            }
          }
        }

4. Crear un **servicio vinculado** de tipo: **AzureMLLinkedService**, lo que proporciona la API de clave y la dirección URL de ejecución por lotes del modelo.
        
        {
          "name": "MyAzureMLLinkedService",
          "properties": {
            "type": "AzureML",
            "typeProperties": {
              "mlEndpoint": "https://[batch execution endpoint]/jobs",
              "apiKey": "[apikey]"
            }
          }
        }
5. Por último, crear una canalización que contiene una actividad **AzureMLBatchExecution** . En tiempo de ejecución canalización lleva a cabo los siguientes pasos:
    1. Obtiene la ubicación del archivo de entrada de los conjuntos de datos de entrada.
    2. Invoca la ejecución del lote de aprendizaje del equipo de Azure API
    3. Copia el resultado de la ejecución de lote en el blob proporcionado en el conjunto de datos de salida. 

    > [AZURE.NOTE] Actividad de AzureMLBatchExecution puede tener cero o más entradas y uno o varios resultados.

        {
          "name": "PredictivePipeline",
          "properties": {
            "description": "use AzureML model",
            "activities": [
              {
                "name": "MLActivity",
                "type": "AzureMLBatchExecution",
                "description": "prediction analysis on batch input",
                "inputs": [
                  {
                    "name": "DecisionTreeInputBlob"
                  }
                ],
                "outputs": [
                  {
                    "name": "DecisionTreeResultBlob"
                  }
                ],
                "linkedServiceName": "MyAzureMLLinkedService",
                "typeProperties":
                {
                    "webServiceInput": "DecisionTreeInputBlob",
                    "webServiceOutputs": {
                        "output1": "DecisionTreeResultBlob"
                    }                
                },
                "policy": {
                  "concurrency": 3,
                  "executionPriorityOrder": "NewestFirst",
                  "retry": 1,
                  "timeout": "02:00:00"
                }
              }
            ],
            "start": "2016-02-13T00:00:00Z",
            "end": "2016-02-14T00:00:00Z"
          }
        }

    Fechas y horas de **Inicio** y **finalización** deben estar en [formato ISO](http://en.wikipedia.org/wiki/ISO_8601). Por ejemplo: 2014-10-14T16:32:41Z. La hora de **finalización** es opcional. Si no especifica el valor de la propiedad **final** , se calcula como "**Inicio + 48 horas.**" Para ejecutar la canalización de forma indefinida, especifique **9999-09-09** como el valor de la propiedad **end** . Para obtener más información sobre las propiedades JSON, vea [Referencia de secuencias de comandos de JSON](https://msdn.microsoft.com/library/dn835050.aspx) .

    > [AZURE.NOTE] Especificar la entrada para la AzureMLBatchExecution actividad es opcional. 

### <a name="scenario-experiments-using-readerwriter-modules-to-refer-to-data-in-various-storages"></a>Escenario: Experimentación con módulos de lectura o escritura para hacer referencia a los datos de almacenamiento distintos

Otro escenario común al crear experimentación Azure m es usar módulos de lector y escritor. El módulo de lector se usa para cargar los datos en un ensayo y el módulo de escritor es guardar datos de su experimentación. Para obtener más información sobre los módulos de lector y escritor, vea los temas de [lector](https://msdn.microsoft.com/library/azure/dn905997.aspx) y [escritor](https://msdn.microsoft.com/library/azure/dn905984.aspx) en MSDN Library.     

Al utilizar los módulos de lector y escritor, es aconsejable usar un parámetro del servicio Web para cada propiedad de estos módulos de lectura o escritura. Estos parámetros web permiten configurar los valores de tiempo de ejecución. Por ejemplo, puede crear un ensayo con un módulo de lector que utiliza una base de datos de SQL Azure: XXX.database.windows.net. Después de haber implementado el servicio web, en el que desee habilitar los consumidores del servicio web especificar otro servidor de SQL Azure denominado YYY.database.windows.net. Puede usar un parámetro del servicio Web para permitir que este valor se configure.

> [AZURE.NOTE] Entrada de servicio Web y salida son diferentes de los parámetros del servicio Web. En el primer escenario, ha visto cómo se pueden especificar una entrada y salida para un servicio Web de m de Azure. En este escenario, debe pasar parámetros de un servicio Web que se corresponden a las propiedades de módulos de lectura o escritura. 

Veamos un escenario para el uso de parámetros de servicio Web. Tiene un servicio web de aprendizaje del equipo de Azure implementado que usa un módulo de lector para leer los datos de uno de los orígenes de datos admitidos por el aprendizaje Azure (por ejemplo: base de datos de SQL Azure). Después de realiza la ejecución por lotes, los resultados se escriben mediante un módulo de escritor (base de datos de SQL Azure).  Entradas de servicio web y los resultados no se definen en la experimentación. En este caso, se recomienda que configure los parámetros del servicio web relevantes para los módulos lector y escritor. Esta configuración permite a la lectura o escritura módulos configurarse cuando se usa la actividad AzureMLBatchExecution. Especificar los parámetros de servicio Web en la sección **globalParameters** en la actividad JSON como sigue. 


    "typeProperties": {
        "globalParameters": {
            "Param 1": "Value 1",
            "Param 2": "Value 2"
        }
    }

También puede usar [Funciones del generador de datos](https://msdn.microsoft.com/library/dn835056.aspx) de envío de los valores de los parámetros del servicio Web tal como se muestra en el ejemplo siguiente:

    "typeProperties": {
        "globalParameters": {
           "Database query": "$$Text.Format('SELECT * FROM myTable WHERE timeColumn = \\'{0:yyyy-MM-dd HH:mm:ss}\\'', Time.AddHours(WindowStart, 0))"
        }
    }
 
> [AZURE.NOTE] Los parámetros del servicio Web distinguen mayúsculas de minúsculas, así que asegúrese de que los nombres que especifique en la actividad JSON coincidan con los expuestas por el servicio Web. 

### <a name="using-a-reader-module-to-read-data-from-multiple-files-in-azure-blob"></a>Usar un módulo de lector para leer los datos de varios archivos de blobs de Windows Azure
Datos grandes de tuberías con actividades como cerdo y subárbol puede generar uno o más archivos de salida sin extensiones. Por ejemplo, cuando se especifica una tabla subárbol externa, los datos de la tabla subárbol externo pueden almacenarse en el almacenamiento de blobs de Windows Azure con los siguientes 000000_0 nombre. Puede utilizar el módulo de lector en apropiados para leer varios archivos y usarlos para predicciones. 

Cuando se usa el módulo de lector apropiados aprendizaje del equipo de Azure, puede especificar blobs de Windows Azure como entrada. Los archivos en el almacenamiento de blobs de Windows Azure pueden ser los archivos de salida (ejemplo: 000000_0) que se producen por un script de cerdo y subárbol ejecutando en HDInsight. El módulo de lectura permite leer archivos (con sin extensiones) mediante la configuración de la **ruta de acceso al contenedor, directorio/blob**. Puntos de **ruta de acceso al contenedor** al contenedor y el **directorio/blob** a la carpeta que contiene los archivos como se muestra en la siguiente imagen. El asterisco es decir, \*) **Especifica que todos los archivos en la carpeta de contenedor (es decir, datos/aggregateddata/año = mes/2014-6 /\*)** se leen como parte de la ensayo.

![Propiedades de blobs de Windows Azure](./media/data-factory-create-predictive-pipelines/azure-blob-properties.png)


### <a name="example"></a>Ejemplo 
#### <a name="pipeline-with-azuremlbatchexecution-activity-with-web-service-parameters"></a>Canalización con AzureMLBatchExecution actividad con parámetros de servicio Web

    {
      "name": "MLWithSqlReaderSqlWriter",
      "properties": {
        "description": "Azure ML model with sql azure reader/writer",
        "activities": [
          {
            "name": "MLSqlReaderSqlWriterActivity",
            "type": "AzureMLBatchExecution",
            "description": "test",
            "inputs": [
              {
                "name": "MLSqlInput"
              }
            ],
            "outputs": [
              {
                "name": "MLSqlOutput"
              }
            ],
            "linkedServiceName": "MLSqlReaderSqlWriterDecisionTreeModel",
            "typeProperties":
            {
                "webServiceInput": "MLSqlInput",
                "webServiceOutputs": {
                    "output1": "MLSqlOutput"
                }
                "globalParameters": {
                    "Database server name": "<myserver>.database.windows.net",
                    "Database name": "<database>",
                    "Server user account name": "<user name>",
                    "Server user account password": "<password>"
                }              
            },
            "policy": {
              "concurrency": 1,
              "executionPriorityOrder": "NewestFirst",
              "retry": 1,
              "timeout": "02:00:00"
            },
          }
        ],
        "start": "2016-02-13T00:00:00Z",
        "end": "2016-02-14T00:00:00Z"
      }
    }
 
En el ejemplo anterior de JSON:

- Servicio Web de aprendizaje del equipo de Azure implementado usa un lector y un módulo de escritor para leer o escribir datos desde o a una base de datos de SQL Azure. Este servicio Web expone los cuatro parámetros siguientes: nombre del servidor, nombre de la base de datos, nombre de cuenta de usuario de servidor y contraseña de cuenta de usuario de servidor de base de datos.  
- Fechas y horas de **Inicio** y **finalización** deben estar en [formato ISO](http://en.wikipedia.org/wiki/ISO_8601). Por ejemplo: 2014-10-14T16:32:41Z. La hora de **finalización** es opcional. Si no especifica el valor de la propiedad **final** , se calcula como "**Inicio + 48 horas.**" Para ejecutar la canalización de forma indefinida, especifique **9999-09-09** como el valor de la propiedad **end** . Para obtener más información sobre las propiedades JSON, vea [Referencia de secuencias de comandos de JSON](https://msdn.microsoft.com/library/dn835050.aspx) .

### <a name="other-scenarios"></a>Otras situaciones

#### <a name="web-service-requires-multiple-inputs"></a>Servicio Web requiere varias entradas
Si el servicio web tarda varias entradas, use la propiedad **webServiceInputs** en lugar de usar **webServiceInput**. Conjuntos de datos que hace referencia la **webServiceInputs** también deben incluirse en las **entradas**de actividad.
 
En la prueba de Azure m parámetros globales y puertos de salida y entrada de servicio web tienen nombres predeterminados ("Entrada1", "Entrada2") que puede personalizar. Los nombres que usa para webServiceInputs, webServiceOutputs y configuración de globalParameters deben coincidir exactamente con los nombres de la experimentación. Puede ver la carga de solicitud de ejemplo en la página de Ayuda de ejecución de proceso por lotes el extremo de Azure v verificar la asignación esperada.


    {
        "name": "PredictivePipeline",
        "properties": {
            "description": "use AzureML model",
            "activities": [{
                "name": "MLActivity",
                "type": "AzureMLBatchExecution",
                "description": "prediction analysis on batch input",
                "inputs": [{
                    "name": "inputDataset1"
                }, {
                    "name": "inputDataset2"
                }],
                "outputs": [{
                    "name": "outputDataset"
                }],
                "linkedServiceName": "MyAzureMLLinkedService",
                "typeProperties": {
                    "webServiceInputs": {
                        "input1": "inputDataset1",
                        "input2": "inputDataset2"
                    },
                    "webServiceOutputs": {
                        "output1": "outputDataset"
                    }
                },
                "policy": {
                    "concurrency": 3,
                    "executionPriorityOrder": "NewestFirst",
                    "retry": 1,
                    "timeout": "02:00:00"
                }
            }],
            "start": "2016-02-13T00:00:00Z",
            "end": "2016-02-14T00:00:00Z"
        }
    }

#### <a name="web-service-does-not-require-an-input"></a>Servicio Web no requiere una entrada

Servicios de Azure m lote ejecución web pueden usarse para ejecutar ningún flujo de trabajo para los scripts de ejemplo R o Python, que no requieran las entradas. O bien, puede que esté configurado el ensayo con un módulo de lector que no se exponen cualquier GlobalParameters. En ese caso, la actividad de AzureMLBatchExecution debería configurarse como sigue:

    {
        "name": "scoring service",
        "type": "AzureMLBatchExecution",
        "outputs": [
            {
                "name": "myBlob"
            }
        ],
        "typeProperties": {
            "webServiceOutputs": {
                "output1": "myBlob"
            }              
         },
        "linkedServiceName": "mlEndpoint",
        "policy": {
            "concurrency": 1,
            "executionPriorityOrder": "NewestFirst",
            "retry": 1,
            "timeout": "02:00:00"
        }
    },
   

#### <a name="web-service-does-not-require-an-inputoutput"></a>Servicio Web no requiere una entrada y salida
El servicio web de Azure m lote ejecución que no tenga ningún resultado de servicio Web configurado. En este ejemplo, no hay ningún servicio Web entrada o salida, ni se configuran cualquier GlobalParameters. Sigue siendo un resultado configurado en la actividad propia, pero no se da como un webServiceOutput.

    {
        "name": "retraining",
        "type": "AzureMLBatchExecution",
        "outputs": [
            {
                "name": "placeholderOutputDataset"
            }
        ],
        "typeProperties": {
         },
        "linkedServiceName": "mlEndpoint",
        "policy": {
            "concurrency": 1,
            "executionPriorityOrder": "NewestFirst",
            "retry": 1,
            "timeout": "02:00:00"
        }
    },

#### <a name="web-service-uses-readers-and-writers-and-the-activity-runs-only-when-other-activities-have-succeeded"></a>Los lectores de usos de servicio Web y sistemas de escritura y la actividad se ejecuta solo cuando se han realizado correctamente otras actividades

Los módulos de lector y escritor del servicio de web de Azure m podrían estar configurados para ejecutar con o sin ninguna GlobalParameters. Sin embargo, desea incrustar llamadas de servicio en una canalización que usa dependencias de conjunto de datos para llamar al servicio de finalizada algún procesamiento en sentido ascendente. También puede desencadenar otra acción después de la ejecución por lotes ha terminado de usar este método. En ese caso, puede expresar las dependencias con entradas de la actividad y los resultados, sin nombre cualquiera de ellos como servicio Web entradas ni salidas.

    {
        "name": "retraining",
        "type": "AzureMLBatchExecution",
        "inputs": [
            {
                "name": "upstreamData1"
            },
            {
                "name": "upstreamData2"
            }
        ],
        "outputs": [
            {
                "name": "downstreamData"
            }
        ],
        "typeProperties": {
         },
        "linkedServiceName": "mlEndpoint",
        "policy": {
            "concurrency": 1,
            "executionPriorityOrder": "NewestFirst",
            "retry": 1,
            "timeout": "02:00:00"
        }
    },

Se muestran los **puntos** :

-   Si el extremo de ensayo usa un webServiceInput: se está representado por un conjunto de datos blob y se incluye en las entradas de la actividad y la propiedad webServiceInput. En caso contrario, se omite la propiedad webServiceInput. 
-   Si el extremo de ensayo usa webServiceOutput(s): que están representados por los conjuntos de datos blob y se incluyen en los resultados de la actividad y en la propiedad webServiceOutputs. Resultados de la actividad y se asignan webServiceOutputs por el nombre de cada resultado en la prueba. En caso contrario, se omite la propiedad webServiceOutputs.
-   Si el extremo de ensayo expone globalParameter(s), se expresa en la propiedad de globalParameters de actividad como clave, pares de valor. En caso contrario, se omite la propiedad globalParameters. Las teclas distinguen mayúsculas de minúsculas. [Funciones del generador de datos de Azure](data-factory-scheduling-and-execution.md#data-factory-functions-reference) pueden utilizarse en los valores. 
- Conjuntos de datos adicionales pueden incluirse en las propiedades de entradas y los resultados de actividad, sin que se hace referencia en la typeProperties actividad. Estos conjuntos de datos rigen ejecución mediante dependencias del sector, pero en caso contrario, se pasan por alto la actividad AzureMLBatchExecution. 


## <a name="updating-models-using-update-resource-activity"></a>Actualizar modelos de uso de recursos de actividad de actualización
Con el tiempo, los modelos de predictivos en la v de Azure puntuación experimentación necesitan aprender a utilizarla con nuevos conjuntos de datos de entrada. Cuando haya terminado con la formación, que desea actualizar el servicio web puntuación con el modelo de m retrained. Los pasos típicos para habilitar entrenamiento y actualizados modelos de Azure m a través de los servicios web son: 

1. Crear un ensayo en [Azure m Studio](https://studio.azureml.net). 
2. Cuando esté satisfecho con el modelo, use Azure m Studio para publicar servicios web para ambos la **formación experimentar** y puntuación /**ensayo predictiva**.

La siguiente tabla describe los servicios web usados en este ejemplo.  Para obtener más información, consulte [modelos de aprendizaje de reciclaje mediante programación](../machine-learning/machine-learning-retrain-models-programmatically.md) .

| Tipo de servicio web | Descripción 
| :------------------ | :---------- 
| **Servicio web de formación** | Recibe datos de formación y genera modelos capacitados. El resultado de la formación es un archivo de .ilearner en un almacenamiento de blobs de Windows Azure.  El **extremo predeterminado** se creará automáticamente cuando se publica el ensayo formación como un servicio web. Puede crear más extremos pero el ejemplo usa solo en el extremo predeterminado |
| **Servicio web de resultados** | Ejemplos de datos sin etiqueta de recibe y realiza predicciones. El resultado de predicción puede tener varios formularios, como un archivo .csv o filas en una base de datos de SQL Azure, según la configuración de la ensayo. El extremo predeterminado se creará automáticamente cuando se publica el ensayo predictiva como un servicio web. Crear el segundo **extremo no predeterminada y actualizable** mediante el [portal de Azure](https://manage.windowsazure.com). Puede crear más extremos, pero este ejemplo usa solo uno de los extremos no predeterminada actualizable. Vea el artículo [Crear extremos](../machine-learning/machine-learning-create-endpoint.md) de pasos.       
 
La imagen siguiente muestra la relación entre la formación y puntuación extremos en Azure m. 

![Servicios Web](./media/data-factory-azure-ml-batch-execution-activity/web-services.png)


Puede invocar el **servicio web de aprendizaje** mediante la **Actividad de ejecución de lotes de Azure m**. Invocar un servicio web de aprendizaje es el mismo que invocar un servicio web de Azure m (puntuación servicio web) para los datos de puntuación. En las secciones anteriores cubren cómo invocar un servicio web de Azure m de una canalización de generador de datos de Azure en detalle. 
  
Puede invocar el **servicio web de resultados** mediante el uso de la **Actividad de Azure m actualizar recursos** para actualizar el servicio web con el modelo recién capacitado. Como se mencionó en la tabla anterior, debe crear y utilizar el extremo actualizable no predeterminada. Además, actualice los servicios vinculados existentes en el generador de datos para usar el extremo no predeterminada para que utilicen siempre el modelo retrained más reciente. 

El siguiente escenario proporciona más detalles. Tiene un ejemplo de reciclaje y actualizar modelos de Azure m de una canalización de generador de datos de Azure. 
 
### <a name="scenario-retraining-and-updating-an-azure-ml-model"></a>Escenario: formación y actualizar un modelo de Azure m
Esta sección proporciona una canalización de ejemplo que usa la **actividad de ejecución de Azure m por lotes** a un modelo de reciclaje. La canalización también usa la **actividad de Azure m actualizar recursos** para actualizar el modelo en el servicio web puntuación. La sección también proporciona fragmentos de JSON para todos los servicios vinculados, los conjuntos de datos y canalización en el ejemplo. 

Aquí es la vista de diagrama de la canalización de ejemplo. Como puede ver, la actividad de ejecución de Azure m lote toma la entrada de formación y produce un resultado de formación (archivo iLearner). La actividad de recursos de actualización de Azure m toma este resultado de formación y actualiza el modelo en el extremo de servicio web puntuación. La actividad de recursos de actualización no produce ningún resultado. El placeholderBlob es simplemente un conjunto de datos de salida ficticias requerido por el servicio de generador de datos de Azure para ejecutar la canalización. 

![diagrama de canalización](./media/data-factory-azure-ml-batch-execution-activity/update-activity-pipeline-diagram.png)


#### <a name="azure-blob-storage-linked-service"></a>Almacenamiento de blobs de Windows Azure vinculado servicio:
El almacenamiento de Azure contiene los datos siguientes:

- datos de entrenamiento. Los datos de entrada para el servicio web de aprendizaje de Azure m.  
- archivo de iLearner. El resultado del servicio web de aprendizaje de Azure m. Este archivo es también la entrada de la actividad de recurso de actualización.  
   
Esto es la definición de JSON de ejemplo del servicio vinculada: 

    {
        "name": "StorageLinkedService",
        "properties": {
            "type": "AzureStorage",
            "typeProperties": {
                "connectionString": "DefaultEndpointsProtocol=https;AccountName=name;AccountKey=key"
            }
        }
    }


#### <a name="training-input-dataset"></a>El conjunto de datos de entrada formación:
El siguiente conjunto de datos representa los datos de entrada de formación para el servicio web de aprendizaje de Azure m. La actividad de ejecución de Azure m por lotes toma este conjunto de datos como una entrada. 

    {
        "name": "trainingData",
        "properties": {
            "type": "AzureBlob",
            "linkedServiceName": "StorageLinkedService",
            "typeProperties": {
                "folderPath": "labeledexamples",
                "fileName": "labeledexamples.arff",
                "format": {
                    "type": "TextFormat"
                }
            },
            "availability": {
                "frequency": "Week",
                "interval": 1
            },
            "policy": {          
                "externalData": {
                    "retryInterval": "00:01:00",
                    "retryTimeout": "00:10:00",
                    "maximumRetry": 3
                }
            }
        }
    }

#### <a name="training-output-dataset"></a>Conjunto de datos de salida de aprendizaje:
El siguiente conjunto de datos representa el archivo de iLearner de salida del servicio web de aprendizaje de Azure m. La actividad de ejecución de Azure m lote genera este conjunto de datos. Este conjunto de datos es también la entrada de la actividad de Azure m actualizar recursos.

    {
        "name": "trainedModelBlob",
        "properties": {
            "type": "AzureBlob",
            "linkedServiceName": "StorageLinkedService",
            "typeProperties": {
                "folderPath": "trainingoutput",
                "fileName": "model.ilearner",
                "format": {
                    "type": "TextFormat"
                }
            },
            "availability": {
                "frequency": "Week",
                "interval": 1
            }
        }
    }

#### <a name="linked-service-for-azure-ml-training-endpoint"></a>Servicios vinculados extremo de formación de Azure m 
El siguiente fragmento JSON define un servicio de aprendizaje del equipo de Azure vinculado que apunta al extremo predeterminado del servicio web de aprendizaje. 

    {   
        "name": "trainingEndpoint",
        "properties": {
            "type": "AzureML",
            "typeProperties": {
                "mlEndpoint": "https://ussouthcentral.services.azureml.net/workspaces/xxx/services/--training experiment--/jobs",
                "apiKey": "myKey"
            }
        }
    }

En **Azure m Studio**, siga este procedimiento para obtener los valores para **mlEndpoint** y **apiKey**:

1. En el menú de la izquierda, haga clic en **Servicios WEB** .
2. Haga clic en el **servicio web de aprendizaje** en la lista de servicios web. 
3. Haga clic en Copiar junto al cuadro de texto de la **clave API** . Pegue la clave en el Portapapeles en el editor de datos JSON de fábrica.
4. En **Azure m studio**, haga clic en el vínculo de **Ejecución por lotes** .
5. Copie la **Solicitud URI** de la sección **Solicitar** y pegarlo en el editor de datos JSON de fábrica.   


#### <a name="linked-service-for-azure-ml-updatable-scoring-endpoint"></a>Servicios vinculados extremo de puntuación actualizable m de Azure:
El siguiente fragmento JSON define un servicio de aprendizaje del equipo de Azure vinculado que apunta al extremo actualizable no predeterminado del servicio web puntuación.  

    {
        "name": "updatableScoringEndpoint2",
        "properties": {
            "type": "AzureML",
            "typeProperties": {
                "mlEndpoint": "https://ussouthcentral.services.azureml.net/workspaces/xxx/services/--scoring experiment--/jobs",
                "apiKey": "endpoint2Key",
                "updateResourceEndpoint": "https://management.azureml.net/workspaces/xxx/webservices/--scoring experiment--/endpoints/endpoint2"
            }
        }
    }


Antes de crear e implementar un m Azure vinculan servicio, siga los pasos en el artículo [Crear extremos](../machine-learning/machine-learning-create-endpoint.md) para crear un segundo extremo (no predeterminada y actualizable) para el servicio web puntuación.

Después de crear el extremo de actualizable no predeterminada, haga lo siguiente:

- Haga clic en **Ejecución por lotes** para obtener el valor URI de la propiedad JSON **mlEndpoint** .
- Haga clic en el vínculo de **Actualización de recursos** para obtener el valor URI de la propiedad JSON **updateResourceEndpoint** . La clave de API se encuentra en la página de punto final (en la esquina inferior derecha). 

![extremo actualizable](./media/data-factory-azure-ml-batch-execution-activity/updatable-endpoint.png)

 
#### <a name="placeholder-output-dataset"></a>Conjunto de datos de salida de marcador de posición:
La actividad de recurso de actualización de Azure m no genera ningún resultado. Sin embargo, el generador de datos de Azure requiere un conjunto de datos de salida a la programación de una canalización. Por lo tanto, usamos un conjunto de datos ficticio o marcador de posición en este ejemplo.  

    {
        "name": "placeholderBlob",
        "properties": {
            "availability": {
                "frequency": "Week",
                "interval": 1
            },
            "type": "AzureBlob",
            "linkedServiceName": "StorageLinkedService",
            "typeProperties": {
                "folderPath": "any",
                "format": {
                    "type": "TextFormat"
                }
            }
        }
    }


#### <a name="pipeline"></a>Canalización
La canalización tiene dos actividades: **AzureMLBatchExecution** y **AzureMLUpdateResource**. La actividad de ejecución de Azure m por lotes toma los datos de entrenamiento como entrada y genera un archivo iLearner como un resultado. La actividad invoca el servicio web de formación (expuesta como un servicio web de ensayo formación) con los datos de entrada de formación y recibe el archivo ilearner desde el servicio Web. El placeholderBlob es simplemente un conjunto de datos de salida ficticias requerido por el servicio de generador de datos de Azure para ejecutar la canalización. 

![diagrama de canalización](./media/data-factory-azure-ml-batch-execution-activity/update-activity-pipeline-diagram.png)


    {
        "name": "pipeline",
        "properties": {
            "activities": [
                {
                    "name": "retraining",
                    "type": "AzureMLBatchExecution",
                    "inputs": [
                        {
                            "name": "trainingData"
                        }
                    ],
                    "outputs": [
                        {
                            "name": "trainedModelBlob"
                        }
                    ],
                    "typeProperties": {
                        "webServiceInput": "trainingData",
                        "webServiceOutputs": {
                            "output1": "trainedModelBlob"
                        }              
                     },
                    "linkedServiceName": "trainingEndpoint",
                    "policy": {
                        "concurrency": 1,
                        "executionPriorityOrder": "NewestFirst",
                        "retry": 1,
                        "timeout": "02:00:00"
                    }
                },
                {
                    "type": "AzureMLUpdateResource",
                    "typeProperties": {
                        "trainedModelName": "Training Exp for ADF ML [trained model]",
                        "trainedModelDatasetName" :  "trainedModelBlob"
                    },
                    "inputs": [
                        {
                            "name": "trainedModelBlob"
                        }
                    ],
                    "outputs": [
                        {
                            "name": "placeholderBlob"
                        }
                    ],
                    "policy": {
                        "timeout": "01:00:00",
                        "concurrency": 1,
                        "retry": 3
                    },
                    "name": "AzureML Update Resource",
                    "linkedServiceName": "updatableScoringEndpoint2"
                }
            ],
            "start": "2016-02-13T00:00:00Z",
            "end": "2016-02-14T00:00:00Z"
        }
    }


### <a name="reader-and-writer-modules"></a>Lector y escritor módulos

Un escenario común para el uso de parámetros de servicio Web es el uso de lectores de SQL Azure y sistemas de escritura. El módulo de lector se usa para cargar los datos en un ensayo de servicios de administración de datos fuera de Azure máquina aprendizaje Studio. El módulo de escritor es guardar los datos de su experimentación en servicios de administración de datos fuera de Azure máquina aprendizaje Studio.  

Para obtener más información acerca de la lectura o escritura de SQL Azure o blobs de Azure, vea los temas de [lector](https://msdn.microsoft.com/library/azure/dn905997.aspx) y [escritor](https://msdn.microsoft.com/library/azure/dn905984.aspx) en MSDN Library. El ejemplo en la sección anterior usa el lector de blobs de Windows Azure y el escritor de blobs de Windows Azure. Esta sección describe el uso de lector de SQL Azure y escritor de SQL Azure.


## <a name="frequently-asked-questions"></a>Preguntas más frecuentes

**Q:** Tengo varios archivos generados por mi canalizaciones de datos grande. ¿Puedo usar la actividad de AzureMLBatchExecution para que funcione en todos los archivos?

**A:** Sí. Vea la sección **mediante un módulo de lector para leer los datos de varios archivos de blobs de Windows Azure** para obtener información detallada. 

## <a name="azure-ml-batch-scoring-activity"></a>Actividad de puntuación de Azure lotes de m
Si está utilizando la actividad **AzureMLBatchScoring** integrar con el aprendizaje Azure, le recomendamos que use la última actividad **AzureMLBatchExecution** . 

Se introduce la actividad AzureMLBatchExecution en la versión de agosto del de 2015 de SDK de Azure y Azure PowerShell.

Si desea seguir utilizando la actividad AzureMLBatchScoring, continúe leyendo esta sección.  

### <a name="azure-ml-batch-scoring-activity-using-azure-storage-for-inputoutput"></a>Actividad de puntuación del lote de m Azure con el almacenamiento de Azure para entrada y salida 

    {
      "name": "PredictivePipeline",
      "properties": {
        "description": "use AzureML model",
        "activities": [
          {
            "name": "MLActivity",
            "type": "AzureMLBatchScoring",
            "description": "prediction analysis on batch input",
            "inputs": [
              {
                "name": "ScoringInputBlob"
              }
            ],
            "outputs": [
              {
                "name": "ScoringResultBlob"
              }
            ],
            "linkedServiceName": "MyAzureMLLinkedService",
            "policy": {
              "concurrency": 3,
              "executionPriorityOrder": "NewestFirst",
              "retry": 1,
              "timeout": "02:00:00"
            }
          }
        ],
        "start": "2016-02-13T00:00:00Z",
        "end": "2016-02-14T00:00:00Z"
      }
    }

### <a name="web-service-parameters"></a>Parámetros del servicio Web
Para especificar valores para los parámetros del servicio Web, agregue una sección de **typeProperties** a la sección **AzureMLBatchScoringActivty** en la canalización de JSON tal como se muestra en el ejemplo siguiente: 

    "typeProperties": {
        "webServiceParameters": {
            "Param 1": "Value 1",
            "Param 2": "Value 2"
        }
    }


También puede usar [Funciones del generador de datos](https://msdn.microsoft.com/library/dn835056.aspx) de envío de los valores de los parámetros del servicio Web tal como se muestra en el ejemplo siguiente:

    "typeProperties": {
        "webServiceParameters": {
           "Database query": "$$Text.Format('SELECT * FROM myTable WHERE timeColumn = \\'{0:yyyy-MM-dd HH:mm:ss}\\'', Time.AddHours(WindowStart, 0))"
        }
    }
 
> [AZURE.NOTE] Los parámetros del servicio Web distinguen mayúsculas de minúsculas, así que asegúrese de que los nombres que especifique en la actividad JSON coincidan con los expuestas por el servicio Web. 

## <a name="see-also"></a>Vea también

- [Entrada de blog de Azure: Introducción a Azure datos fábrica y aprendizaje del equipo de Azure](https://azure.microsoft.com/blog/getting-started-with-azure-data-factory-and-azure-machine-learning-4/)





[adf-build-1st-pipeline]: data-factory-build-your-first-pipeline.md

[azure-machine-learning]: http://azure.microsoft.com/services/machine-learning/


 
