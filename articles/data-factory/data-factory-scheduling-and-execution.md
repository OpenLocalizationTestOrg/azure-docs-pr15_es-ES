<properties
    pageTitle="Programación y ejecución con datos Factory | Microsoft Azure"
    description="Obtenga información sobre aspectos de programación y ejecución del generador de datos de Azure del modelo de aplicación."
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
    ms.date="08/22/2016"
    ms.author="spelluru"/>

# <a name="data-factory-scheduling-and-execution"></a>Programación de fábrica de datos y la ejecución
Este artículo explica los aspectos de la programación y ejecución del generador de datos de Azure del modelo de aplicación. 

## <a name="prerequisites"></a>Requisitos previos
En este artículo se supone que conocer conceptos básicos de los conceptos de modelo de la aplicación de fábrica de datos, incluidos actividad, canalizaciones, servicios vinculados y conjuntos de datos. Para los conceptos básicos del generador de datos de Azure, consulte los siguientes artículos:

- [Introducción al generador de datos](data-factory-introduction.md)
- [Canalizaciones](data-factory-create-pipelines.md)
- [Conjuntos de datos](data-factory-create-datasets.md) 

## <a name="schedule-an-activity"></a>Programar una actividad

Con la sección programador de la actividad de JSON, puede especificar una programación periódica de una actividad. Por ejemplo, puede programar una actividad de cada hora como sigue:

    "scheduler": {
        "frequency": "Hour",
        "interval": 1
    },  

![Ejemplo de programador](./media/data-factory-scheduling-and-execution/scheduler-example.png)

Como se muestra en el diagrama, especificar una programación de la actividad crea una serie de ventanas de rotación. Rotación de windows son una serie de intervalos de tiempo de tamaño fijo, no se superponen, contiguas. Estas ventanas rotación lógica de la actividad se denominan *windows actividad*.

La ventana de actividad actualmente en ejecución, puede tener acceso el intervalo de tiempo asociado a la ventana de actividad con [WindowStart](data-factory-functions-variables.md#data-factory-system-variables) y [WindowEnd](data-factory-functions-variables.md#data-factory-system-variables) variables del sistema en la actividad JSON. Puede usar estas variables para distintos fines en su actividad JSON. Por ejemplo, puede usar para seleccionar datos de entrada y salida conjuntos de datos que representa los datos de la serie de tiempo.

La propiedad **Programador** es compatible con el mismas subpropiedades como la propiedad de **disponibilidad** de un conjunto de datos. Para obtener información detallada, vea [disponibilidad de conjunto de datos](data-factory-create-datasets.md#Availability) . Ejemplos: programación en un desplazamiento de tiempo específico o establecer el modo de alinear procesamiento al principio o al final del intervalo de la ventana de la actividad.

Puede especificar las propiedades de **Programador** de una actividad, pero esta propiedad es **opcional**. Si especifica una propiedad, debe coincidir con la cadencia que especificar en la definición de conjunto de datos de salida. Actualmente, conjunto de datos de salida es qué unidades de la programación, así que debe crear un conjunto de datos de salida, incluso si la actividad produce ningún resultado. Si la actividad no realiza cualquier entrada, puede omitir la creación del conjunto de datos de entrada.

## <a name="time-series-datasets-and-data-slices"></a>Sectores de conjuntos de datos y los datos de la serie de tiempo

Datos de la serie de tiempo están una secuencia continua de puntos de datos que normalmente consta de medidas sucesivas realizadas a lo largo de un intervalo de tiempo. Ejemplos de datos de la serie de tiempo incluyen datos de sensor y los datos de telemetría de aplicación.

Con el generador de datos, puede procesar tiempo de ejecución de datos de la serie de forma por lotes con actividad. Normalmente, hay una cadencia periódica en el que datos de entrada llegan y necesidades de datos va a producir de salida. Se basa este cadencia especificando la **disponibilidad** en el conjunto de datos como sigue:

    "availability": {
      "frequency": "Hour",
      "interval": 1
    },

Cada unidad de datos consumida y creados mediante una ejecución de actividad se denomina un segmento de datos. El siguiente diagrama muestra un ejemplo de una actividad con un conjunto de datos de entrada y salida del conjunto de datos. Estos conjuntos de datos con **disponibilidad** establecida en una frecuencia cada hora.

![Programador de disponibilidad](./media/data-factory-scheduling-and-execution/availability-scheduler.png)

El diagrama anterior muestra los sectores de datos por hora para el conjunto de datos de entrada y salida. El diagrama muestra tres sectores de entrada que están listos para el procesamiento. La actividad de 10-11 A.M. está en curso, producir la segmentación de datos de salida de 10-11 A.M..

Puede tener acceso el intervalo de tiempo asociado a la segmentación de datos actual que se produce en el conjunto de datos JSON con variables [SliceStart](data-factory-functions-variables.md#data-factory-system-variables) y [SliceEnd](data-factory-functions-variables.md#data-factory-system-variables).

Actualmente, el generador de datos requiere que la programación especificada en la actividad exactamente coincide con la programación especificada en la **disponibilidad** del conjunto de datos de salida. Por lo tanto, **WindowStart**, **WindowEnd**, **SliceStart**y **SliceEnd** siempre se asignan para el mismo período de tiempo y un sector único resultado.

Para obtener más información sobre las distintas propiedades disponibles para la sección disponibilidad, vea [crear conjuntos de datos](data-factory-create-datasets.md).

## <a name="move-data-from-sql-database-to-blob-storage"></a>Mover los datos de la base de datos de SQL con el almacenamiento de blobs

Pongamos algunos elementos juntos y en acción mediante la creación de una canalización que copie los datos de una tabla de base de datos de SQL Azure con el almacenamiento de blobs de Windows Azure cada hora.

**Entrada: Conjunto de datos de la base de datos de SQL Azure**

    {
        "name": "AzureSqlInput",
        "properties": {
            "published": false,
            "type": "AzureSqlTable",
            "linkedServiceName": "AzureSqlLinkedService",
            "typeProperties": {
                "tableName": "MyTable"
            },
            "availability": {
                "frequency": "Hour",
                "interval": 1
            },
            "external": true,
            "policy": {}
        }
    }


**Frecuencia** se establece en **horas** y el **intervalo** está establecido en **1** en la sección disponibilidad.

**Salida: Conjunto de datos de almacenamiento de blobs de Windows Azure**

    {
        "name": "AzureBlobOutput",
        "properties": {
            "published": false,
            "type": "AzureBlob",
            "linkedServiceName": "StorageLinkedService",
            "typeProperties": {
                "folderPath": "mypath/{Year}/{Month}/{Day}/{Hour}",
                "format": {
                    "type": "TextFormat"
                },
                "partitionedBy": [
                    {
                        "name": "Year",
                        "value": {
                            "type": "DateTime",
                            "date": "SliceStart",
                            "format": "yyyy"
                        }
                    },
                    {
                        "name": "Month",
                        "value": {
                            "type": "DateTime",
                            "date": "SliceStart",
                            "format": "%M"
                        }
                    },
                    {
                        "name": "Day",
                        "value": {
                            "type": "DateTime",
                            "date": "SliceStart",
                            "format": "%d"
                        }
                    },
                    {
                        "name": "Hour",
                        "value": {
                            "type": "DateTime",
                            "date": "SliceStart",
                            "format": "%H"
                        }
                    }
                ]
            },
            "availability": {
                "frequency": "Hour",
                "interval": 1
            }
        }
    }


**Frecuencia** se establece en **horas** y el **intervalo** está establecido en **1** en la sección disponibilidad.



**Actividad: Actividad de copia**

    {
        "name": "SamplePipeline",
        "properties": {
            "description": "copy activity",
            "activities": [
                {
                    "type": "Copy",
                    "name": "AzureSQLtoBlob",
                    "description": "copy activity",
                    "typeProperties": {
                        "source": {
                            "type": "SqlSource",
                            "sqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
                        },
                        "sink": {
                            "type": "BlobSink",
                            "writeBatchSize": 100000,
                            "writeBatchTimeout": "00:05:00"
                        }
                    },
                    "inputs": [
                        {
                            "name": "AzureSQLInput"
                        }
                    ],
                    "outputs": [
                        {
                            "name": "AzureBlobOutput"
                        }
                    ],
                    "scheduler": {
                        "frequency": "Hour",
                        "interval": 1
                    }
                }
            ],
            "start": "2015-01-01T08:00:00Z",
            "end": "2015-01-01T11:00:00Z"
        }
    }


Muestra la programación de la actividad y secciones de disponibilidad del conjunto de datos en una frecuencia cada hora. En el ejemplo se muestra cómo puede usar **WindowStart** y **WindowEnd** para seleccionar datos relevantes para una actividad ejecutar y copiarlo en un blob con la adecuada **folderPath**. La **folderPath** tiene parámetros para que tenga una carpeta independiente para cada hora.

Cuando ejecutan tres de los sectores entre 8 – 11 A.M., los datos de la base de datos de SQL Azure están la siguiente:

![Entrada de ejemplo](./media/data-factory-scheduling-and-execution/sample-input-data.png)

Cuando se implementa la canalización, el blobs de Windows Azure se rellenan como sigue:

-   Archivo mypath/2015/1/1/8/datos. &lt;Guid&gt;.txt con datos

            10002345,334,2,2015-01-01 08:24:00.3130000
            10002345,347,15,2015-01-01 08:24:00.6570000
            10991568,2,7,2015-01-01 08:56:34.5300000

    > [AZURE.NOTE] &lt;GUID&gt; se reemplaza con un guid real. Ejemplo de nombre de archivo: Data.bcde1348 7620 4f93 bb89 0eed3455890b.txt
-   Archivo mypath/2015/1/1/9/datos. &lt;Guid&gt;.txt con datos:

            10002345,334,1,2015-01-01 09:13:00.3900000
            24379245,569,23,2015-01-01 09:25:00.3130000
            16777799,21,115,2015-01-01 09:47:34.3130000
-   Archivo mypath/2015/1/1/10/datos. &lt;Guid&gt;.txt sin datos.


## <a name="active-period-for-pipeline"></a>Período activo para canalización

[Creación de canalizaciones](data-factory-create-pipelines.md) presentó el concepto de un período activo para una canalización especificado mediante la configuración de las propiedades de **Inicio** y **finalización** .

Puede establecer la fecha de inicio para el período de canalización activo en el pasado. Generador de datos automáticamente calcula (rellenos back) todas las segmentaciones de datos en el pasado y comienza el procesamiento de ellos.

## <a name="parallel-processing-of-data-slices"></a>Procesamiento en paralelo de sectores de datos
Puede configurar sectores de datos de relleno de fondo para ejecutarse en paralelo estableciendo la propiedad de **simultaneidad** en la sección de directiva de la actividad de JSON. Para obtener más información sobre esta propiedad, vea [crear canalizaciones](data-factory-create-pipelines.md).

## <a name="rerun-a-failed-data-slice"></a>Vuelva a ejecutar un segmento de datos errores 
Puede supervisar la ejecución de sectores de un modo visual enriquecido. Para obtener más información, vea [supervisión y administración de tuberías mediante Azure aspas portal](data-factory-monitor-manage-pipelines.md) o la [aplicación de administración y supervisión](data-factory-monitor-manage-app.md) .

Considere el ejemplo siguiente, que muestra dos actividades. Activity1 produce un conjunto de datos de serie de tiempo con sectores como resultado que consume como entrada Activity2 para generar el conjunto de datos de serie de tiempo de salida final.

![Error división](./media/data-factory-scheduling-and-execution/failed-slice.png)

El diagrama muestra fuera de tres sectores recientes, se ha producido un error al crear la segmentación de datos de 9 o 10 A.M. para Dataset2. Generador de datos realiza automáticamente un seguimiento de dependencia para el conjunto de datos de serie de tiempo. No se iniciarán la actividad ejecutar para el sector descendentes 9-10 A.M..

Herramientas de administración y supervisión de fábrica datos permiten profundizar en los registros de diagnóstico para el sector error encontrar la causa principal para el problema y corregirlo fácilmente. Después de corregir el problema, puede iniciar la actividad de ejecutarse para generar la segmentación de datos error fácilmente. Para obtener más detalles acerca de cómo volver a ejecutar y comprender transiciones de estado para segmentaciones de datos, vea [supervisión y administración de tuberías mediante Azure aspas portal](data-factory-monitor-manage-pipelines.md) o [aplicación de administración y supervisión](data-factory-monitor-manage-app.md).

Después de volver a ejecutar el sector 9-10 A.M. para **Dataset2**, el generador de datos se inicia la ejecución del sector dependientes de 9 o 10 A.M. en el conjunto de datos final.

![Volver a ejecutar error sector](./media/data-factory-scheduling-and-execution/rerun-failed-slice.png)

## <a name="run-activities-in-a-sequence"></a>Ejecutar actividades en una secuencia
Puede encadenar dos actividades (ejecutar una actividad después de otro) estableciendo el conjunto de datos de salida de la actividad de un conjunto de datos de entrada de la actividad de otra. Las actividades pueden estar en la misma tubería o en canalizaciones diferentes. La segunda actividad ejecuta solo cuando la primera que finaliza correctamente.

Por ejemplo, tenga en cuenta lo siguiente:

1.  Canalización P1 tiene A1 actividad que requiere el conjunto de datos de entrada externo D1 y genera el conjunto de datos de salida D2.
2.  Canalización P2 tiene A2 de actividad que requiere una entrada de conjunto de datos D2 y genera el conjunto de datos de salida D3.

En este escenario, actividades A1 y A2 son canalizaciones diferente. La actividad A1 se ejecuta cuando los datos externos están disponibles y se alcanza la frecuencia de disponibilidad programada. La actividad A2 se ejecuta cuando los sectores programados de D2 no estarán disponibles y se alcanza la frecuencia de disponibilidad programada. Si hay un error en uno de los sectores del conjunto de datos D2, A2 no se ejecuta para ese sector hasta que esté disponible.

El siguiente diagrama se verá la vista de diagrama:

![Encadenar las actividades de dos canalizaciones](./media/data-factory-scheduling-and-execution/chaining-two-pipelines.png)

Como se mencionó anteriormente, las actividades pueden estar en la misma canalización. La vista de diagrama con dos actividades en la misma tubería tendría un aspecto como el siguiente diagrama:

![Encadenar actividades en la misma tubería](./media/data-factory-scheduling-and-execution/chaining-one-pipeline.png)

### <a name="copy-sequentially"></a>Copiar secuencialmente
Es posible ejecutar varias operaciones de copia consecutiva de forma secuencial o encargado. Por ejemplo, es posible que tenga dos actividades de copia de una canalización (CopyActivity1 y CopyActivity2) con los siguientes conjuntos de datos de salida de datos de entrada:   

CopyActivity1

Entrada: conjunto de datos. Resultado: Dataset2.

CopyActivity2

Entrada: Dataset2.  Resultado: Dataset3.

Ejecutar CopyActivity2 solo si se ha ejecutado correctamente el CopyActivity1 y Dataset2 está disponible.

Esta es la canalización de ejemplo JSON:

    {
        "name": "ChainActivities",
        "properties": {
            "description": "Run activities in sequence",
            "activities": [
                {
                    "type": "Copy",
                    "typeProperties": {
                        "source": {
                            "type": "BlobSource"
                        },
                        "sink": {
                            "type": "BlobSink",
                            "copyBehavior": "PreserveHierarchy",
                            "writeBatchSize": 0,
                            "writeBatchTimeout": "00:00:00"
                        }
                    },
                    "inputs": [
                        {
                            "name": "Dataset1"
                        }
                    ],
                    "outputs": [
                        {
                            "name": "Dataset2"
                        }
                    ],
                    "policy": {
                        "timeout": "01:00:00"
                    },
                    "scheduler": {
                        "frequency": "Hour",
                        "interval": 1
                    },
                    "name": "CopyFromBlob1ToBlob2",
                    "description": "Copy data from a blob to another"
                },
                {
                    "type": "Copy",
                    "typeProperties": {
                        "source": {
                            "type": "BlobSource"
                        },
                        "sink": {
                            "type": "BlobSink",
                            "writeBatchSize": 0,
                            "writeBatchTimeout": "00:00:00"
                        }
                    },
                    "inputs": [
                        {
                            "name": "Dataset2"
                        }
                    ],
                    "outputs": [
                        {
                            "name": "Dataset3"
                        }
                    ],
                    "policy": {
                        "timeout": "01:00:00"
                    },
                    "scheduler": {
                        "frequency": "Hour",
                        "interval": 1
                    },
                    "name": "CopyFromBlob2ToBlob3",
                    "description": "Copy data from a blob to another"
                }
            ],
            "start": "2016-08-25T01:00:00Z",
            "end": "2016-08-25T01:00:00Z",
            "isPaused": false
        }
    }

Observe que en el ejemplo, se especifica el conjunto de datos de salida de la primera actividad copia (Dataset2) como entrada para la segunda actividad. Por lo tanto, la segunda actividad sólo se ejecuta cuando el conjunto de datos de salida de la primera actividad está listo.  

En el ejemplo, CopyActivity2 puede tener una entrada diferente, por ejemplo, Dataset3, pero especificar Dataset2 como una entrada de CopyActivity2, por lo que no se ejecutará la actividad hasta que finalice CopyActivity1. Por ejemplo:

CopyActivity1

Entrada: Dataset1. Resultado: Dataset2.

CopyActivity2

Entradas: Dataset3, Dataset2. Resultado: Dataset4.

    {
        "name": "ChainActivities",
        "properties": {
            "description": "Run activities in sequence",
            "activities": [
                {
                    "type": "Copy",
                    "typeProperties": {
                        "source": {
                            "type": "BlobSource"
                        },
                        "sink": {
                            "type": "BlobSink",
                            "copyBehavior": "PreserveHierarchy",
                            "writeBatchSize": 0,
                            "writeBatchTimeout": "00:00:00"
                        }
                    },
                    "inputs": [
                        {
                            "name": "Dataset1"
                        }
                    ],
                    "outputs": [
                        {
                            "name": "Dataset2"
                        }
                    ],
                    "policy": {
                        "timeout": "01:00:00"
                    },
                    "scheduler": {
                        "frequency": "Hour",
                        "interval": 1
                    },
                    "name": "CopyFromBlobToBlob",
                    "description": "Copy data from a blob to another"
                },
                {
                    "type": "Copy",
                    "typeProperties": {
                        "source": {
                            "type": "BlobSource"
                        },
                        "sink": {
                            "type": "BlobSink",
                            "writeBatchSize": 0,
                            "writeBatchTimeout": "00:00:00"
                        }
                    },
                    "inputs": [
                        {
                            "name": "Dataset3"
                        },
                        {
                            "name": "Dataset2"
                        }
                    ],
                    "outputs": [
                        {
                            "name": "Dataset4"
                        }
                    ],
                    "policy": {
                        "timeout": "01:00:00"
                    },
                    "scheduler": {
                        "frequency": "Hour",
                        "interval": 1
                    },
                    "name": "CopyFromBlob3ToBlob4",
                    "description": "Copy data from a blob to another"
                }
            ],
            "start": "2017-04-25T01:00:00Z",
            "end": "2017-04-25T01:00:00Z",
            "isPaused": false
        }
    }


Observe que en el ejemplo, se especifican dos conjuntos de datos de entrada para la segunda actividad de copia. Cuando se especifican varias entradas, solo el primer conjunto de entrada datos se utiliza para copiar los datos, pero otros conjuntos de datos se usan como dependencias. ¿Iniciar CopyActivity2 solo después de que se cumplen las condiciones siguientes:

- CopyActivity1 se ha completado correctamente y Dataset2 está disponible. No se utiliza este conjunto de datos al copiar datos a Dataset4. Sólo actúa como una dependencia de programación para CopyActivity2.   
- Dataset3 está disponible. Este conjunto de datos representa los datos que se copian en el destino.  



## <a name="model-datasets-with-different-frequencies"></a>Conjuntos de datos de modelo con distintas frecuencias

En los ejemplos, la frecuencia de conjuntos de datos de entrada y salida y la ventana del plan de actividad eran iguales. Algunos escenarios requieren la capacidad de producir resultados con una frecuencia diferente a la frecuencia de una o más entradas. Generador de datos es compatible con estos escenarios de modelado.

### <a name="sample-1-produce-a-daily-output-report-for-input-data-that-is-available-every-hour"></a>Ejemplo 1: Crear un informe de salida diaria de datos de entrada que está disponibles cada hora

Considere la posibilidad de un escenario en el que ha introducido datos de medida de sensores disponibles cada hora en el almacenamiento de blobs de Windows Azure. Desea generar un informe agregado diario con estadísticas, como promedio, máximo y mínimo para el día con el [Generador de datos subárbol actividad](data-factory-hive-activity.md).

Esto es cómo puede modelar este escenario con el generador de datos:

**Conjunto de datos de entrada**

Los archivos de entrada por hora se eliminan de la carpeta del día dado. Disponibilidad de entrada se establece en **hora** (frecuencia: horas, intervalo: 1).

    {
      "name": "AzureBlobInput",
      "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
          "folderPath": "mycontainer/myfolder/{Year}/{Month}/{Day}/",
          "partitionedBy": [
            { "name": "Year", "value": {"type": "DateTime","date": "SliceStart","format": "yyyy"}},
            { "name": "Month","value": {"type": "DateTime","date": "SliceStart","format": "%M"}},
            { "name": "Day","value": {"type": "DateTime","date": "SliceStart","format": "%d"}}
          ],
          "format": {
            "type": "TextFormat"
          }
        },
        "external": true,
        "availability": {
          "frequency": "Hour",
          "interval": 1
        }
      }
    }

**Conjunto de datos de salida**

Un archivo de salida se crea cada día en la carpeta del día. Disponibilidad de salida se establece en **día** (frecuencia: día e intervalo: 1).


    {
      "name": "AzureBlobOutput",
      "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
          "folderPath": "mycontainer/myfolder/{Year}/{Month}/{Day}/",
          "partitionedBy": [
            { "name": "Year", "value": {"type": "DateTime","date": "SliceStart","format": "yyyy"}},
            { "name": "Month","value": {"type": "DateTime","date": "SliceStart","format": "%M"}},
            { "name": "Day","value": {"type": "DateTime","date": "SliceStart","format": "%d"}}
          ],
          "format": {
            "type": "TextFormat"
          }
        },
        "availability": {
          "frequency": "Day",
          "interval": 1
        }
      }
    }

**Actividad: actividad de una canalización de subárbol**

La secuencia de comandos de la sección recibe la información de *fecha y hora* adecuada como parámetros que use la variable **WindowStart** como se muestra en el fragmento de código siguiente. La secuencia de comandos de la sección usa esta variable para cargar los datos de la carpeta correcta para el día y ejecutar la agregación para generar el resultado.

        {  
            "name":"SamplePipeline",
            "properties":{  
            "start":"2015-01-01T08:00:00",
            "end":"2015-01-01T11:00:00",
            "description":"hive activity",
            "activities": [
                {
                    "name": "SampleHiveActivity",
                    "inputs": [
                        {
                            "name": "AzureBlobInput"
                        }
                    ],
                    "outputs": [
                        {
                            "name": "AzureBlobOutput"
                        }
                    ],
                    "linkedServiceName": "HDInsightLinkedService",
                    "type": "HDInsightHive",
                    "typeProperties": {
                        "scriptPath": "adftutorial\\hivequery.hql",
                        "scriptLinkedService": "StorageLinkedService",
                        "defines": {
                            "Year": "$$Text.Format('{0:yyyy}',WindowStart)",
                            "Month": "$$Text.Format('{0:%M}',WindowStart)",
                            "Day": "$$Text.Format('{0:%d}',WindowStart)"
                        }
                    },
                    "scheduler": {
                        "frequency": "Day",
                        "interval": 1
                    },          
                    "policy": {
                        "concurrency": 1,
                        "executionPriorityOrder": "OldestFirst",
                        "retry": 2,
                        "timeout": "01:00:00"
                    }
                 }
             ]
           }
        }

En el diagrama siguiente muestra el escenario desde un punto de vista de dependencia de datos.

![Dependencia de datos](./media/data-factory-scheduling-and-execution/data-dependency.png)

La segmentación de datos de salida para cada día depende 24 sectores por hora de un conjunto de datos de entrada. Generador de datos calcula automáticamente estas dependencias averiguar los datos de entrada de sectores que se encuentran en el mismo período de tiempo que la segmentación de datos de salida va a producir. Si cualquiera de los sectores de entrada 24 no está disponible, el generador de datos espera la segmentación de datos de entrada estar preparado antes de iniciar la actividad diaria ejecutar.


### <a name="sample-2-specify-dependency-with-expressions-and-data-factory-functions"></a>Ejemplo 2: Especificar dependencia con expresiones y funciones del generador de datos

Veamos otro escenario. Suponga que tiene una actividad de sección que procesa dos conjuntos de datos de entrada. Uno de ellos tenga nuevos datos diariamente, pero uno de ellos obtiene nuevos datos cada semana. Suponga que desea hacer una combinación entre las dos entradas y producir un resultado cada día.

El enfoque simple en qué fábrica datos automáticamente cifras de la derecha de entrada de sectores para procesar mediante la alineación de los resultados de la hora del sector datos período no funciona.

Debe especificar que para ejecutar todas las actividades, el generador de datos deben usar sectores de datos de la semana pasada para el conjunto de datos de entrada semanal. Usar funciones del generador de datos de Azure tal como se muestra en el siguiente fragmento de código para implementar este comportamiento.

**Entrada1: Blobs de Windows Azure**

La primera entrada es la blobs de Windows Azure que se actualiza diariamente.

    {
      "name": "AzureBlobInputDaily",
      "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
          "folderPath": "mycontainer/myfolder/{Year}/{Month}/{Day}/",
          "partitionedBy": [
            { "name": "Year", "value": {"type": "DateTime","date": "SliceStart","format": "yyyy"}},
            { "name": "Month","value": {"type": "DateTime","date": "SliceStart","format": "%M"}},
            { "name": "Day","value": {"type": "DateTime","date": "SliceStart","format": "%d"}}
          ],
          "format": {
            "type": "TextFormat"
          }
        },
        "external": true,
        "availability": {
          "frequency": "Day",
          "interval": 1
        }
      }
    }

**Entrada2: Blobs de Windows Azure**

Entrada2 es la blobs de Windows Azure actualizando semanales.

    {
      "name": "AzureBlobInputWeekly",
      "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
          "folderPath": "mycontainer/myfolder/{Year}/{Month}/{Day}/",
          "partitionedBy": [
            { "name": "Year", "value": {"type": "DateTime","date": "SliceStart","format": "yyyy"}},
            { "name": "Month","value": {"type": "DateTime","date": "SliceStart","format": "%M"}},
            { "name": "Day","value": {"type": "DateTime","date": "SliceStart","format": "%d"}}
          ],
          "format": {
            "type": "TextFormat"
          }
        },
        "external": true,
        "availability": {
          "frequency": "Day",
          "interval": 7
        }
      }
    }

**Resultado: Blobs de Windows Azure**

Un archivo de salida se crea cada día en la carpeta para el día. Disponibilidad de salida se establece en **día** (frecuencia: día, intervalo: 1).

    {
      "name": "AzureBlobOutputDaily",
      "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
          "folderPath": "mycontainer/myfolder/{Year}/{Month}/{Day}/",
          "partitionedBy": [
            { "name": "Year", "value": {"type": "DateTime","date": "SliceStart","format": "yyyy"}},
            { "name": "Month","value": {"type": "DateTime","date": "SliceStart","format": "%M"}},
            { "name": "Day","value": {"type": "DateTime","date": "SliceStart","format": "%d"}}
          ],
          "format": {
            "type": "TextFormat"
          }
        },
        "availability": {
          "frequency": "Day",
          "interval": 1
        }
      }
    }

**Actividad: actividad de una canalización de subárbol**

La actividad de subárbol toma las dos entradas y genera un sector de salida cada día. Puede especificar el sector de salida de cada día para dependen de sectores de entrada de la semana anterior entrada semanal como sigue.

    {  
        "name":"SamplePipeline",
        "properties":{  
        "start":"2015-01-01T08:00:00",
        "end":"2015-01-01T11:00:00",
        "description":"hive activity",
        "activities": [
          {
            "name": "SampleHiveActivity",
            "inputs": [
              {
                "name": "AzureBlobInputDaily"
              },
              {
                "name": "AzureBlobInputWeekly",
                "startTime": "Date.AddDays(SliceStart, - Date.DayOfWeek(SliceStart))",
                "endTime": "Date.AddDays(SliceEnd,  -Date.DayOfWeek(SliceEnd))"  
              }
            ],
            "outputs": [
              {
                "name": "AzureBlobOutputDaily"
              }
            ],
            "linkedServiceName": "HDInsightLinkedService",
            "type": "HDInsightHive",
            "typeProperties": {
              "scriptPath": "adftutorial\\hivequery.hql",
              "scriptLinkedService": "StorageLinkedService",
              "defines": {
                "Year": "$$Text.Format('{0:yyyy}',WindowStart)",
                "Month": "$$Text.Format('{0:%M}',WindowStart)",
                "Day": "$$Text.Format('{0:%d}',WindowStart)"
              }
            },
            "scheduler": {
              "frequency": "Day",
              "interval": 1
            },          
            "policy": {
              "concurrency": 1,
              "executionPriorityOrder": "OldestFirst",
              "retry": 2,  
              "timeout": "01:00:00"
            }
           }
         ]
       }
    }


## <a name="data-factory-functions-and-system-variables"></a>Funciones de fábrica de datos y las variables del sistema   

Para obtener una lista de funciones y variables del sistema compatibles con el generador de datos, vea [funciones de fábrica de datos y las variables del sistema](data-factory-functions-variables.md) .

## <a name="data-dependency-deep-dive"></a>Análisis detallado de dependencia de datos

Para generar un sector del conjunto de datos por una ejecución de la actividad, generador de datos utiliza el *modelo de dependencia* para determinar las relaciones entre conjuntos de datos consumida y creados mediante una actividad.

El intervalo de tiempo de los conjuntos de datos de entrada necesarios para generar la segmentación de datos del conjunto de datos de salida se denomina el *período de dependencia*.

Ejecución de una actividad genera un sector del conjunto de datos solo después de que los sectores de datos en conjuntos de datos de entrada en el período de dependencia están disponibles. En otras palabras, todos los sectores de entrada que incluya el período de dependencia deben estar en estado **Listo** para la actividad de ejecutan para generar un sector del conjunto de datos de salida.

Para generar la segmentación de datos del conjunto de datos [**Inicio**, **fin**], una función debe asignar la segmentación de datos del conjunto de datos a su período de dependencia. Esta función es, esencialmente, una fórmula que convierte la inicial y final de la segmentación de datos del conjunto de datos en el inicio y finalización del período de dependencia. Modo más formal:

    DatasetSlice = [start, end]
    DependecyPeriod = [f(start, end), g(start, end)]

**F** y **g** asignar funciones que calculan el inicio y finalización del período de dependencia para cada actividad de entrada.

Tal como se muestra en los ejemplos, el período de dependencia es el mismo que el período para el sector de datos que se ha producido. En estos casos, el generador de datos calcula automáticamente los sectores de entrada que se encuentran en el período de dependencia.  

Por ejemplo, en el ejemplo de agregación donde se produce diariamente resultado y entrada de datos están disponibles cada hora, el período de sectores de datos es 24 horas. Generador de datos se encuentra la entrada de cada hora pertinente sectores durante este período de tiempo y realiza el sector de salida depende de la segmentación de datos de entrada.

También puede proporcionar su propia asignación para el período de dependencia tal como se muestra en el ejemplo, donde una de las entradas es semanal y el segmento de salida se produce diariamente.

## <a name="data-dependency-and-validation"></a>Dependencia de datos y validación

Un conjunto de datos puede tener una directiva de validación definida que especifica cómo se pueden validar los datos generados por la ejecución de un sector antes de que esté listo para su uso. Para obtener información detallada, vea [crear conjuntos de datos](data-factory-create-datasets.md) .

En estos casos, después de la segmentación de datos ha finalizado la ejecución, el estado del sector de salida cambia a **en espera** con un subestado de **validación**. Después de que los sectores se validarán, el estado del sector cambia a **Listo**.

Si se ha producido un segmento de datos, pero no ha superado la validación, no se procesan ejecuciones de actividad de sectores descendentes que dependen de este sector.

[Monitor y administrar canalizaciones](data-factory-monitor-manage-pipelines.md) trata sobre los distintos Estados de sectores de datos en el generador de datos.

## <a name="external-data"></a>Datos externos

Un conjunto de datos puede marcarse como externos (como se muestra en el siguiente fragmento JSON), lo que implica que no se ha generado con el generador de datos. En este caso, la directiva de conjunto de datos puede tener un conjunto de parámetros que describe validación adicional y vuelva a intentar directiva para el conjunto de datos. Consulte [crear canalizaciones](data-factory-create-pipelines.md) para obtener una descripción de todas las propiedades.

Similar a los conjuntos de datos producidos por el generador de datos, los sectores de datos de datos externos deben estar preparado antes de que se pueden procesar sectores dependientes.

    {
        "name": "AzureSqlInput",
        "properties":
        {
            "type": "AzureSqlTable",
            "linkedServiceName": "AzureSqlLinkedService",
            "typeProperties":
            {
                "tableName": "MyTable"
            },
            "availability":
            {
                "frequency": "Hour",
                "interval": 1     
            },
            "external": true,
            "policy":
            {
                "externalData":
                {
                    "retryInterval": "00:01:00",
                    "retryTimeout": "00:10:00",
                    "maximumRetry": 3
                }
            }  
        }
    }


## <a name="onetime-pipeline"></a>Canalización de una vez.
Puede crear y programar una canalización para ejecutar periódicamente (por ejemplo: cada hora o diariamente) dentro de las horas de inicio y finalización que especifica en la definición de canalización. Para obtener más información, vea [las actividades de programación](#scheduling-and-execution) . También puede crear una canalización que se ejecuta una sola vez. Para ello, Establece la propiedad **pipelineMode** en la definición de canalización **tendrá que repetir** tal como se muestra en el siguiente ejemplo JSON. El valor predeterminado de esta propiedad está **programada**.

    {
        "name": "CopyPipeline",
        "properties": {
            "activities": [
                {
                    "type": "Copy",
                    "typeProperties": {
                        "source": {
                            "type": "BlobSource",
                            "recursive": false
                        },
                        "sink": {
                            "type": "BlobSink",
                            "writeBatchSize": 0,
                            "writeBatchTimeout": "00:00:00"
                        }
                    },
                    "inputs": [
                        {
                            "name": "InputDataset"
                        }
                    ],
                    "outputs": [
                        {
                            "name": "OutputDataset"
                        }
                    ]
                    "name": "CopyActivity-0"
                }
            ]
            "pipelineMode": "OneTime"
        }
    }

Tenga en cuenta lo siguiente:

- Horas de **Inicio** y **finalización** para la canalización no especificados.
- **Disponibilidad** de los conjuntos de datos de entrada y salida especificada (**frecuencia** e **intervalo**), aunque el generador de datos no utiliza los valores.  
- Vista de diagrama no muestra canalizaciones puntuales. Este comportamiento es por diseño.
- No se pueden actualizar canalizaciones puntuales. Puede clonar canalización de una sola vez, cambiarle el nombre, actualizar propiedades e implementarlo para crear otra.
