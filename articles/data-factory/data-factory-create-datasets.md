<properties 
    pageTitle="Crear conjuntos de datos en el generador de datos de Azure | Microsoft Azure" 
    description="Obtenga información sobre cómo crear conjuntos de datos en el generador de datos de Azure con los ejemplos que utilizan propiedades de desplazamiento y anchorDateTime."
    keywords="Crear conjunto de datos de ejemplo de conjunto de datos, desplazamiento de ejemplo"
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
    ms.date="09/13/2016" 
    ms.author="shlo"/>

# <a name="datasets-in-azure-data-factory"></a>Conjuntos de datos en el generador de datos de Azure
En este artículo se describe los conjuntos de datos en el generador de datos de Azure e incluye ejemplos como desplazamiento, anchorDateTime y bases de datos de desplazamiento o estilo.

Cuando se crea un conjunto de datos, que está creando un puntero a los datos que se desean procesar. Los datos se procesan (entrada/salida) en una actividad y una actividad se encuentra en una canalización. Un conjunto de datos de entrada representa la entrada de una actividad de la canalización y un conjunto de datos de salida representa el resultado de la actividad.

Conjuntos de datos identifican datos dentro de almacena datos diferentes, como tablas, archivos, carpetas y documentos. Después de crear un conjunto de datos, puede usarlo con actividades en una canalización. Por ejemplo, un conjunto de datos puede ser un conjunto de datos de entrada y salida de una actividad de copia o un HDInsightHive. El portal de Azure proporciona un diseño visual de todos los datos y canalizaciones entradas y salidas. Un vistazo, verá todas las relaciones y las dependencias de su canalizaciones en todos los orígenes para siempre sepa dónde proceden datos y dónde va.

En el generador de datos de Azure, puede obtener datos de un conjunto de datos mediante el uso de actividad de copia de una canalización.

> [AZURE.NOTE] Si es nuevo en el generador de datos de Azure, vea [Introducción al generador de datos de Azure](data-factory-introduction.md) para obtener información general del servicio de generador de datos de Azure. Vea [crear su primer generador de datos](data-factory-build-your-first-pipeline.md) para obtener un tutorial para crear su primer generador de datos. Estos dos artículos proporcionan que fondo información necesaria para comprender mejor de este artículo. 

## <a name="define-datasets"></a>Definir los conjuntos de datos
Un conjunto de datos en el generador de datos de Azure se define como sigue: 


    {
        "name": "<name of dataset>",
        "properties": {
            "type": "<type of dataset: AzureBlob, AzureSql etc...>",
            "external": <boolean flag to indicate external data. only for input datasets>,
            "linkedServiceName": "<Name of the linked service that refers to a data store.>",
            "structure": [
                {
                    "name": "<Name of the column>",
                    "type": "<Name of the type>"
                }
            ],
            "typeProperties": {
                "<type specific property>": "<value>",
                "<type specific property 2>": "<value 2>",
            },
            "availability": {
                "frequency": "<Specifies the time unit for data slice production. Supported frequency: Minute, Hour, Day, Week, Month>",
                "interval": "<Specifies the interval within the defined frequency. For example, frequency set to 'Hour' and interval set to 1 indicates that new data slices should be produced hourly>"
            },
           "policy": 
            {      
            }
        }
    }

La siguiente tabla describe las propiedades en el anterior JSON:   

| (Propiedad) | Descripción | Obligatorio | Predeterminado |
| -------- | ----------- | -------- | ------- |
| nombre | Nombre del conjunto de datos. Vea [Generador de datos de Azure - reglas de nomenclatura](data-factory-naming-rules.md) para las reglas de nomenclatura. | Sí | NOD |
| tipo | Tipo del conjunto de datos. Especifique uno de los tipos admitidos por el generador de datos de Azure (por ejemplo: AzureBlob, AzureSqlTable). <br/><br/>Para obtener información detallada, vea [El tipo de conjunto de datos](#Type) . | Sí | NOD |
| estructura | Esquema del conjunto de datos<br/><br/>Para obtener más información, vea [La estructura Dataset](#Structure) sección. | No. | NOD |
| typeProperties | Propiedades correspondiente al tipo seleccionado. Vea la sección [Tipo de conjunto de datos](#Type) para obtener detalles sobre los tipos compatibles y sus propiedades. | Sí | NOD |
| externos | Indicador booleano para especificar si un conjunto de datos se produce explícitamente una canalización del generador de datos o no.  | No | falso | 
| disponibilidad | Define el modelo de división para el conjunto de datos de producción o de la ventana de procesamiento. <br/><br/>Para obtener información detallada, vea [Disponibilidad de conjunto de datos de](#Availability) sección. <br/><br/>Para obtener detalles sobre el modelo, consulte el artículo de [la programación y ejecución](data-factory-scheduling-and-execution.md) de división del conjunto de datos. | Sí | NOD
| Directiva | Define los criterios o la condición que deben cumplir los sectores del conjunto de datos. <br/><br/>Para obtener más información, vea [Directiva de conjunto de datos de](#Policy) sección. | No | NOD |

## <a name="dataset-example"></a>Ejemplo de conjunto de datos
En el ejemplo siguiente, el conjunto de datos representa una tabla denominada **MyTable** en una **base de datos de SQL Azure**. 

    {
        "name": "DatasetSample",
        "properties": {
            "type": "AzureSqlTable",
            "linkedServiceName": "AzureSqlLinkedService",
            "typeProperties": 
            {
                "tableName": "MyTable"
            },
            "availability": 
            {
                "frequency": "Day",
                "interval": 1
            }
        }
    }

Tenga en cuenta los siguientes puntos: 

- tipo se establece en AzureSqlTable.
- propiedad de tipo de nombre de tabla (específico AzureSqlTable tipo) se establece en MyTable.
- linkedServiceName hace referencia a un servicio de tipo AzureSqlDatabase vinculado. Consulte la definición del servicio vinculada siguiente. 
- frecuencia de disponibilidad se establece en día e intervalo está establecido en 1, lo que significa que la segmentación de datos se produce diariamente.  

AzureSqlLinkedService se define como sigue:

    {
        "name": "AzureSqlLinkedService",
        "properties": {
            "type": "AzureSqlDatabase",
            "description": "",
            "typeProperties": {
                "connectionString": "Data Source=tcp:<servername>.database.windows.net,1433;Initial Catalog=<databasename>;User ID=<username>@<servername>;Password=<password>;Integrated Security=False;Encrypt=True;Connect Timeout=30"
            }
        }
    }

En el anterior JSON: 

- tipo se establece en AzureSqlDatabase
- propiedad de tipo cadena de conexión especifica información para conectarse a una base de datos de SQL Azure.  


Como puede ver, el servicio vinculado define cómo conectarse a una base de datos de SQL Azure. El conjunto de datos define qué tabla se utiliza como una entrada y salida de la actividad de una canalización. La sección de la actividad de la [canalización de](data-factory-create-pipelines.md) JSON especifica si el conjunto de datos se utiliza como un conjunto de datos de entrada o salida.


> [AZURE.IMPORTANT] A menos que se produce un conjunto de datos mediante el generador de datos de Azure, debe marcarse como **externo**. Normalmente, esta configuración se aplica a las entradas de la primera actividad de una canalización.   

## <a name="Type"></a>Tipo de conjunto de datos
Se alinean los orígenes de datos compatibles y los tipos de conjunto de datos. Consulte los temas que se hace referencia en el artículo de [Actividades de movimiento de datos](data-factory-data-movement-activities.md#supported-data-stores) para obtener información sobre los tipos y la configuración de conjuntos de datos. Por ejemplo, si está utilizando los datos de una base de datos de SQL Azure, haga clic en base de datos de SQL Azure en la lista de comercios de datos compatibles para ver información detallada.  

## <a name="Structure"></a>Estructura de conjunto de datos
La sección **estructura** define el esquema del conjunto de datos. Contiene una colección de nombres y tipos de datos de las columnas.  En el ejemplo siguiente, el conjunto de datos tiene tres columnas slicetimestamp, nombre de proyecto y vistas de página para y son de tipo: cadena, cadena y Decimal respectivamente.

    structure:  
    [ 
        { "name": "slicetimestamp", "type": "String"},
        { "name": "projectname", "type": "String"},
        { "name": "pageviews", "type": "Decimal"}
    ]

## <a name="Availability"></a>Disponibilidad de conjunto de datos
La sección **disponibilidad** de un conjunto de datos define la ventana de procesamiento (cada hora, diaria, semanal, etc.) o el modelo de división para el conjunto de datos. Consulte el artículo de [la programación y ejecución](data-factory-scheduling-and-execution.md) para obtener más detalles sobre el modelo de división y dependencia de conjunto de datos. 

En la sección disponibilidad siguiente especifica que el conjunto de datos de salida es producidos por hora (o) entrada conjunto de datos está disponible por hora:

    "availability": 
    {   
        "frequency": "Hour",        
        "interval": 1   
    }

La siguiente tabla describe las propiedades que puede utilizar en la sección disponibilidad: 

| (Propiedad) | Descripción | Obligatorio | Predeterminado |
| -------- | ----------- | -------- | ------- |
| frecuencia | Especifica la unidad de producción del sector de conjunto de datos.<br/><br/>**Compatibles con frecuencia**: minuto, hora, día, semana, mes | Sí | NOD |
| intervalo | Especifica un factor de frecuencia<br/><br/>"Intervalo de frecuencia x" determina la frecuencia con la que se produce la segmentación de datos.<br/><br/>Si necesita el conjunto de datos se corta cada hora, establezca **frecuencia** de **horas**y el **intervalo** a **1**.<br/><br/>**Nota:** Si especifica la frecuencia de minuto, recomendamos que establezca el intervalo a no menos de 15 | Sí | NOD |
| estilo | Especifica si se debe crear la segmentación de datos en el inicio o finalización del intervalo.<ul><li>StartOfInterval</li><li>EndOfInterval</li></ul><br/><br/>Si frecuencia se establece en mes y estilo se establece en EndOfInterval, se produce la segmentación de datos en el último día del mes. Si el estilo se establece en StartOfInterval, se produce la segmentación de datos en el primer día del mes.<br/><br/>Si frecuencia se establece en día y estilo se establece en EndOfInterval, la segmentación de datos se produce en la última hora del día.<br/><br/>Si frecuencia se establece en hora y estilo se establece en EndOfInterval, la segmentación de datos se produce al final de la hora. Por ejemplo, para un sector para el período de 1 P.M. – 2 P.M., la segmentación de datos se genera a 2 P.M.. | No | EndOfInterval |
| anchorDateTime | Define la posición absoluta en el tiempo que se utiliza el programador para calcular los límites de sectores de conjunto de datos. <br/><br/>**Nota:** Si el AnchorDateTime tiene elementos de fecha son más granulares que la frecuencia se pasan por alto las partes más detalladas. <br/><br/>Por ejemplo, si el **intervalo de** **cada hora** (frecuencia: hora e intervalo: 1) y la **AnchorDateTime** contiene **minutos y segundos**, a continuación, se pasan por alto las partes de **minutos y segundos** de la AnchorDateTime. | No | 01/01/0001 |
| desplazamiento | Intervalo de tiempo que se desplazan el inicio y finalización de todos los sectores del conjunto de datos. <br/><br/>**Nota:** Si se especifican anchorDateTime y desplazamiento, el resultado es el turno combinado. | No | NOD |

### <a name="offset-example"></a>ejemplo de desplazamiento

Diaria sectores que comiencen a 6 A.M. en lugar de la medianoche de forma predeterminada. 

    "availability":
    {
        "frequency": "Day",
        "interval": 1,
        "offset": "06:00:00"
    }

La **frecuencia** se establece al **día** y el **intervalo** está establecido en **1** (una vez al día): si desea que el sector presentarse a 6 A.M. en lugar de en el momento de forma predeterminada: 12 A.M.. Recuerde que esta vez es una hora UTC. 

## <a name="anchordatetime-example"></a>ejemplo de anchorDateTime

**Ejemplo:** los sectores de conjunto de datos 23 horas que comienzan en 2007-04-19T08:00:00

    "availability": 
    {   
        "frequency": "Hour",        
        "interval": 23, 
        "anchorDateTime":"2007-04-19T08:00:00"  
    }

## <a name="offsetstyle-example"></a>desplazamiento o estilo de ejemplo

Si necesita un conjunto de datos de cada mes en fecha y hora determinadas (suponga en 3 º de cada mes a 8:00 A.M.), use la etiqueta de **desplazamiento** para establecer la fecha y la hora se debe ejecutar. 

    {
      "name": "MyDataset",
      "properties": {
        "type": "AzureSqlTable",
        "linkedServiceName": "AzureSqlLinkedService",
        "typeProperties": {
          "tableName": "MyTable"
        },
        "availability": {
          "frequency": "Month",
          "interval": 1,
          "offset": "3.08:10:00",
          "style": "StartOfInterval"
        }
      }
    }


## <a name="Policy"></a>Directiva de conjunto de datos

La sección de **Directiva** en la definición de conjunto de datos define los criterios o la condición que deben cumplir los sectores del conjunto de datos.

### <a name="validation-policies"></a>Directivas de validación

| Nombre de la directiva | Descripción | Aplicar a | Obligatorio | Predeterminado |
| ----------- | ----------- | ---------- | -------- | ------- |
| minimumSizeMB | Valida que los datos de un **blob de Azure** cumplen los requisitos de tamaño mínimo (en megabytes). | Blobs de Windows Azure | No | NOD |
|minimumRows | Valida que los datos de una **base de datos de SQL Azure** o una **tabla de Azure** contienen el número mínimo de filas. | <ul><li>Base de datos SQL Azure</li><li>Tablas de Azure</li></ul> | No | NOD

#### <a name="examples"></a>Ejemplos

**minimumSizeMB:**

    "policy":
    
    {
        "validation":
        {
            "minimumSizeMB": 10.0
        }
    }

**minimumRows**

    "policy":
    {
        "validation":
        {
            "minimumRows": 100
        }
    }

### <a name="external-datasets"></a>Conjuntos de datos externos

Conjuntos de datos externos son los que no se fabrican una canalización de ejecución en el generador de datos. Si el conjunto de datos está marcado como **externo**, puede definirse la directiva **ExternalData** para influir en el comportamiento de la disponibilidad de sector del conjunto de datos. 

A menos que se produce un conjunto de datos mediante el generador de datos de Azure, debe marcarse como **externo**. Normalmente, esta configuración se aplica a las entradas de la primera actividad de una canalización a menos que se utiliza actividad o canalización encadenar. 

| Nombre | Descripción | Obligatorio | Valor predeterminado  |
| ---- | ----------- | -------- | -------------- |
| dataDelay | Tiempo de retraso de la comprobación de la disponibilidad de los datos externos de la segmentación de datos determinado. Por ejemplo, si los datos se supone que esté disponible cada hora, que se puede retrasar la comprobación de los datos externos están disponibles y el sector correspondiente está listo para usando dataDelay.<br/><br/>Solo se aplica a la hora actual.  Por ejemplo, si es 1:00 P.M. ahora y este valor es de 10 minutos, la validación comenzará a 1:10 PM.<br/><br/>Esta configuración no afectará a los sectores en el pasado (sectores con la hora de finalización del sector + dataDelay < ahora) se procesan sin demora.<br/><br/>Hora mayor que 23:59 horas necesitan especificado con el formato day.hours:minutes:seconds. Por ejemplo, para especificar las 24 horas, no use 24:00:00; en su lugar, use 1.00:00:00. Si utiliza 24:00:00, este se considera 24 días (24.00:00:00). Para 1 día y 4 horas, especificar 1:04:00:00. | No | 0 |
| retryInterval | Reintentar el tiempo de espera entre un error y la siguiente. Se aplica a la hora actual; Si intenta error anterior, se espera este tiempo después del último intento. <br/><br/>Si es 1:00 p.m. ahora mismo, comenzaremos al primer intento. Si la duración para completar la primera comprobación de validación es 1 minuto y error en la operación, el siguiente reintento está en 1:00 + 1 min (duración) + 1min (intervalo entre intentos) = 1:02 pm. <br/><br/>Para los segmentos en el pasado, no hay ningún retraso. El reintento ocurre inmediatamente. | No | 00:01:00 (1 minuto) | 
| retryTimeout | El tiempo de espera para cada reintento.<br/><br/>Si esta propiedad está establecida en 10 minutos, la validación debe completarse dentro de 10 minutos. Si tiene más de 10 minutos para realizar la validación, el reintento agota el tiempo.<br/><br/>Si todos los intentos de validación agota el tiempo de espera, la segmentación de datos se marca como tiempo de espera agotado. | No | 00:10:00 (10 minutos) |
| maximumRetry | Número de veces para comprobar la disponibilidad de los datos externos. El valor máximo permitido es de 10. | No | 3 | 

## <a name="scoped-datasets"></a>Ámbito de conjuntos de datos
Puede crear conjuntos de datos que están orientadas a una canalización mediante la propiedad de **conjuntos de datos** . Estos conjuntos de datos solo pueden usarse actividades dentro de este proceso, pero no las actividades de otras canalizaciones. En el ejemplo siguiente se define una canalización con dos conjuntos de datos - rdc InputDataset y OutputDataset-rdc - para usarse dentro de la canalización:  

> [AZURE.IMPORTANT] Ámbito de conjuntos de datos son compatibles sólo con canalizaciones puntuales (**pipelineMode** establecidos **OneTime**). Para obtener información detallada, vea [Onetime canalización](data-factory-scheduling-and-execution.md#onetime-pipeline) .

    {
        "name": "CopyPipeline-rdc",
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
                            "name": "InputDataset-rdc"
                        }
                    ],
                    "outputs": [
                        {
                            "name": "OutputDataset-rdc"
                        }
                    ],
                    "scheduler": {
                        "frequency": "Day",
                        "interval": 1,
                        "style": "StartOfInterval"
                    },
                    "name": "CopyActivity-0"
                }
            ],
            "start": "2016-02-28T00:00:00Z",
            "end": "2016-02-28T00:00:00Z",
            "isPaused": false,
            "pipelineMode": "OneTime",
            "expirationTime": "15.00:00:00",
            "datasets": [
                {
                    "name": "InputDataset-rdc",
                    "properties": {
                        "type": "AzureBlob",
                        "linkedServiceName": "InputLinkedService-rdc",
                        "typeProperties": {
                            "fileName": "emp.txt",
                            "folderPath": "adftutorial/input",
                            "format": {
                                "type": "TextFormat",
                                "rowDelimiter": "\n",
                                "columnDelimiter": ","
                            }
                        },
                        "availability": {
                            "frequency": "Day",
                            "interval": 1
                        },
                        "external": true,
                        "policy": {}
                    }
                },
                {
                    "name": "OutputDataset-rdc",
                    "properties": {
                        "type": "AzureBlob",
                        "linkedServiceName": "OutputLinkedService-rdc",
                        "typeProperties": {
                            "fileName": "emp.txt",
                            "folderPath": "adftutorial/output",
                            "format": {
                                "type": "TextFormat",
                                "rowDelimiter": "\n",
                                "columnDelimiter": ","
                            }
                        },
                        "availability": {
                            "frequency": "Day",
                            "interval": 1
                        },
                        "external": false,
                        "policy": {}
                    }
                }
            ]
        }
    }