<properties 
    pageTitle="Mover los datos de MongoDB con datos fábrica | Microsoft Azure" 
    description="Obtenga información sobre cómo mover los datos de la base de datos de MongoDB mediante el generador de datos de Azure." 
    services="data-factory" 
    documentationCenter="" 
    authors="linda33wj" 
    manager="jhubbard" 
    editor="monicar"/>

<tags 
    ms.service="data-factory" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/04/2016" 
    ms.author="jingwang"/>

# <a name="move-data-from-mongodb-using-azure-data-factory"></a>Mover los datos de MongoDB con el generador de datos de Azure

En este artículo se describe cómo puede usar la actividad de copia de un generador de datos de Azure para mover datos desde una base de datos de MongoDB local a otro almacén de datos. En este artículo se basa en el artículo de [actividades de movimiento de datos](data-factory-data-movement-activities.md) , que ofrece una descripción general de movimiento de datos con la actividad de copia y las combinaciones de almacén de datos de origen/receptor que admite la actividad de copia.

Servicio de generador de datos admite la conexión a orígenes de MongoDB locales con Data Management Gateway. Consulte el artículo de [Data Management Gateway](data-factory-data-management-gateway.md) para obtener información sobre Data Management Gateway y [mover los datos locales a la nube](data-factory-move-data-between-onprem-and-cloud.md) artículo para obtener instrucciones detalladas sobre cómo configurar la puerta de enlace de una canalización de datos para mover datos. 

> [AZURE.NOTE] Debe usar la puerta de enlace para conectarse a MongoDB incluso si está hospedado en máquinas virtuales de IaaS de Azure. Si está intentando conectarse a una instancia de MongoDB alojado en la nube, también puede instalar la instancia de puerta de enlace en la VM IaaS.

Generador de datos admite actualmente solo mover los datos de MongoDB a otro almacena datos, pero no para mover datos desde otro almacena datos a MongoDB.

## <a name="prerequisites"></a>Requisitos previos
Al servicio de generador de datos de Azure para que pueda conectarse a la base de datos de MongoDB local, debe instalar los siguientes componentes: 

- Data Management Gateway 2.0 o superior en el mismo equipo que hospeda la base de datos o en un equipo distinto para evitar una competición de recursos con la base de datos. Data Management Gateway es un software que conecta los orígenes de datos locales a servicios en la nube de forma segura y administrada. Consulte el artículo de [Data Management Gateway](data-factory-data-management-gateway.md) para obtener más información acerca de Data Management Gateway.
  
    Cuando se instala la puerta de enlace, se instalará automáticamente un MongoDB controlador ODBC Microsoft usado para conectarse a MongoDB. 

## <a name="copy-data-wizard"></a>Asistente de datos de copia
La manera más sencilla de crear una canalización que copie datos de una base de datos MongoDB a ninguna el receptor compatibles de almacenamiento de datos es usar al Asistente de datos de copia. Consulte [Tutorial: crear una tubería mediante el Asistente para copiar](data-factory-copy-data-wizard-tutorial.md) para obtener un tutorial rápido sobre la creación de una canalización mediante el Asistente de datos de copia. 

En el ejemplo siguiente se proporciona definiciones de JSON de ejemplo que puede usar para crear una canalización mediante el [portal de Azure](data-factory-copy-activity-tutorial-using-azure-portal.md) o [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) o [PowerShell de Azure](data-factory-copy-activity-tutorial-using-powershell.md). Se muestra cómo copiar datos de la base de datos MongoDB para el almacenamiento de blobs de Windows Azure. Sin embargo, los datos se pueden copiar a cualquiera de los receptores indicada [aquí](data-factory-data-movement-activities.md#supported-data-stores) mediante la actividad de copia en el generador de datos de Azure.

## <a name="sample-copy-data-from-mongodb-to-azure-blob"></a>Ejemplo: Copiar datos de MongoDB a blobs de Windows Azure
En este ejemplo se muestra cómo copiar datos de una base de datos de MongoDB local a un almacenamiento de blobs de Windows Azure. Sin embargo, los datos pueden ser copiado **directamente** a cualquiera de los receptores indicada [aquí](data-factory-data-movement-activities.md#supported-data-stores) mediante la actividad de copia en el generador de datos de Azure.  
 
El ejemplo tiene las siguientes entidades de fábrica de datos:

1.  Servicio de tipo [OnPremisesMongoDb](#linked-service-properties)vinculado.
2.  Servicio de tipo [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties)vinculado.
3.  Un [conjunto de datos](data-factory-create-datasets.md) a la entrada de tipo [MongoDbCollection](#dataset-type-properties).
4.  Un resultado [conjunto de datos](data-factory-create-datasets.md) de tipo [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties).
4.  [Canalización](data-factory-create-pipelines.md) con actividad copia que utiliza [MongoDbSource](#copy-activity-type-properties) y [BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties).

El ejemplo copia datos de un resultado de consulta de base de datos MongoDB a un blob cada hora. Las propiedades JSON usadas en estos ejemplos se describen en secciones siguiendo los ejemplos. 

Como primer paso, configure la puerta de enlace de administración de datos según las instrucciones en el artículo de [Data Management Gateway](data-factory-data-management-gateway.md) . 

**Servicio de MongoDB vinculado**

    { 
        "name": "OnPremisesMongoDbLinkedService", 
        "properties": 
        { 
            "type": "OnPremisesMongoDb", 
            "typeProperties": 
            { 
                "authenticationType": "<Basic or Anonymous>", 
                "server": "< The IP address or host name of the MongoDB server >",  
                "port": "<The number of the TCP port that the MongoDB server uses to listen for client connections.>", 
                "username": "<username>", 
                "password": "<password>",
               "authSource": "< The database that you want to use to check your credentials for authentication. >",
                "databaseName": "<database name>",
                "gatewayName": "<mygateway>"
            } 
        }
    } 


**Servicio de almacenamiento vinculado de Azure**

    {
      "name": "AzureStorageLinkedService",
      "properties": {
        "type": "AzureStorage",
        "typeProperties": {
          "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
        }
      }
    }

**Conjunto de datos de entrada de MongoDB** Establecer "externo": "true" informa de servicio del generador de datos que la tabla, el generador de datos externo y no se produce en una actividad en el generador de datos.
    
    {
         "name":  "MongoDbInputDataset",
        "properties": { 
            "type": "MongoDbCollection", 
            "linkedServiceName": "OnPremisesMongoDbLinkedService", 
            "typeProperties": { 
                "collectionName": "<Collection name>"   
            }, 
            "availability": {
                "frequency": "Hour",
                "interval": 1
            },
            "external": true
        } 
    }



**Conjunto de datos de salida de blobs de Windows Azure**

Se escriben datos en un nuevo blob cada hora (frecuencia: horas, intervalo: 1). La ruta de acceso para el blob se evalúa dinámicamente en función de la hora de inicio de la segmentación de datos que se está procesando. La ruta de acceso de la carpeta usa el año, mes, día y partes de horas de la hora de inicio.

    {
        "name": "AzureBlobOutputDataSet",
        "properties": {
            "type": "AzureBlob",
            "linkedServiceName": "AzureStorageLinkedService",
            "typeProperties": {
                "folderPath": "mycontainer/frommongodb/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
                "format": {
                    "type": "TextFormat",
                    "rowDelimiter": "\n",
                    "columnDelimiter": "\t"
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



**Canalización con actividad de copia**

La canalización contiene una actividad de copia está configurado para usar la entrada anterior y conjuntos de datos de salida y se ha programado para que se ejecute cada hora. En la canalización de definición de JSON, el tipo de **origen** se establece en **MongoDbSource** y tipo de **receptor** se establece en **BlobSink**. La consulta SQL especificada para la propiedad de **consulta** selecciona los datos en la última hora para copiar.
    
    {
        "name": "CopyMongoDBToBlob",
        "properties": {
            "description": "pipeline for copy activity",
            "activities": [
                {
                    "type": "Copy",
                    "typeProperties": {
                        "source": {
                            "type": "MongoDbSource",
                            "query": "$$Text.Format('select * from  MyTable where LastModifiedDate >= {{ts\'{0:yyyy-MM-dd HH:mm:ss}\'}} AND LastModifiedDate < {{ts\'{1:yyyy-MM-dd HH:mm:ss}\'}}', WindowStart, WindowEnd)"
                        },
                        "sink": {
                            "type": "BlobSink",
                            "writeBatchSize": 0,
                            "writeBatchTimeout": "00:00:00"
                        }
                    },
                    "inputs": [
                        {
                            "name": "MongoDbInputDataset"
                        }
                    ],
                    "outputs": [
                        {
                            "name": "AzureBlobOutputDataSet"
                        }
                    ],
                    "policy": {
                        "timeout": "01:00:00",
                        "concurrency": 1
                    },
                    "scheduler": {
                        "frequency": "Hour",
                        "interval": 1
                    },
                    "name": "MongoDBToAzureBlob"
                }
            ],
            "start": "2016-06-01T18:00:00Z",
            "end": "2016-06-01T19:00:00Z"
        }
    }


## <a name="linked-service-properties"></a>Propiedades del servicio vinculado

La tabla siguiente proporciona la descripción para elementos JSON específicos al servicio **OnPremisesMongoDB** vinculado.

| (Propiedad) | Descripción | Obligatorio |
| -------- | ----------- | -------- | 
| tipo | La propiedad tipo debe estar establecida en: **OnPremisesMongoDb** | Sí |
| servidor | Dirección IP o host nombre del servidor MongoDB. | Sí | 
| puerto | Puerto TCP que utiliza el servidor de MongoDB para escuchar las conexiones de cliente. | Valor predeterminado opcional: 27017 |
| authenticationType | Básica o anónima. | Sí | 
| nombre de usuario | Cuenta de usuario para tener acceso a MongoDB. | Sí (si se utiliza la autenticación básica).|
| contraseña | Contraseña para el usuario. |   Sí (si se utiliza la autenticación básica). | 
| authSource | Nombre de la base de datos MongoDB que desee usar para comprobar sus credenciales para la autenticación. | Opcional (si se utiliza la autenticación básica). predeterminado: utiliza la cuenta de administrador y la base de datos especificados mediante la propiedad databaseName. |  
| databaseName | Nombre de la base de datos MongoDB que desea tener acceso. | Sí |
| gatewayName | Nombre de la puerta de enlace que tiene acceso el almacén de datos. | Sí | 
| encryptedCredential | Credencial cifrado por la puerta de enlace. | Opcional |


Para obtener más información sobre cómo establecer credenciales para un origen de datos local MongoDB, vea [establecer credenciales y seguridad](data-factory-move-data-between-onprem-and-cloud.md#set-credentials-and-security) .

## <a name="dataset-type-properties"></a>Propiedades de tipo de conjunto de datos

Para obtener una lista completa de secciones y propiedades disponibles para definir conjuntos de datos, vea el artículo [crear conjuntos de datos](data-factory-create-datasets.md) . Secciones como estructura, la disponibilidad y la directiva de un conjunto de datos JSON son similares para todos los tipos de conjunto de datos (SQL Azure, blobs de Windows Azure, tablas de Azure, etcetera.).

La sección **typeProperties** es diferente para cada tipo de conjunto de datos y proporciona información sobre la ubicación de los datos en el almacén de datos. La sección de typeProperties para el conjunto de datos de tipo **MongoDbCollection** tiene las siguientes propiedades:

| (Propiedad) | Descripción | Obligatorio |
| -------- | ----------- | -------- |
| collectionName | Nombre de la colección de base de datos MongoDB. | Sí | 

## <a name="copy-activity-type-properties"></a>Copiar las propiedades del tipo de actividad

Para obtener una lista completa de secciones y propiedades disponibles para definir actividades, vea el artículo de la [Creación de canalizaciones](data-factory-create-pipelines.md) . Propiedades como nombre, descripción, entrada y salida tablas y directiva están disponibles para todos los tipos de actividades. 

Propiedades disponibles en la sección **typeProperties** de la actividad por otro lado varían en función de cada tipo de actividad. Actividad de copia, que varían en función de los tipos de orígenes y receptores.

Cuando el origen es de tipo **MongoDbSource** las propiedades siguientes están disponibles en la sección typeProperties:

| (Propiedad) | Descripción | Valores permitidos | Obligatorio |
| -------- | ----------- | -------------- | -------- |
| consulta | Uso de la consulta personalizada para leer datos. | Cadena de consulta de SQL-92. Por ejemplo: seleccione * from MyTable. | No (si se especifica **collectionName** del **conjunto de datos** ) | 

## <a name="schema-by-data-factory"></a>Esquema de generador de datos
Servicio de generador de datos de Azure deduce el esquema de una colección de MongoDB usando los últimos 100 documentos en la colección. Si estos 100 documentos no contienen esquema completo, se pueden omitir algunas columnas durante la operación de copia. 

## <a name="type-mapping-for-mongodb"></a>Asignación de tipo para MongoDB

Como se mencionó en el artículo de [actividades de movimiento de datos](data-factory-data-movement-activities.md) , actividad copia realiza las conversiones de tipo automática de tipos de origen para recibir tipos con el siguiente enfoque paso 2:

1. Convertir de tipos de origen nativo al tipo de .NET
2. Conversión de tipo de .NET a tipo de lavabo nativo

Al mover los datos a MongoDB se usan las siguientes asignaciones de tipos de MongoDB a tipos de .NET.

| Tipo de MongoDB | Tipo de .NET framework |
| ------------------- | ------------------- | 
| Binario | Byte] |
| Valor booleano | Valor booleano |
| Fecha | Fecha y hora |
| NumberDouble | Doble |
| NumberInt | Int32 |
| NumberLong | Int64 |
| Id. de objeto | Cadena |
| Cadena | Cadena |
| UUID | GUID | 
| Objeto | Normalizarse en acoplar columnas con "_" como separador anidada |

> [AZURE.NOTE]  
> Para obtener información sobre cómo admitir matrices mediante tablas virtuales, consulte [soporte técnico para tipos complejos mediante tablas virtuales](#support-for-complex-types-using-virtual-tables) la siguiente sección. 

Actualmente, no se admiten los siguientes tipos de datos MongoDB: DBPointer, JavaScript, Max o Min clave, expresiones regulares, símbolo, marca de tiempo, no definido

## <a name="support-for-complex-types-using-virtual-tables"></a>Soporte técnico para los tipos complejos mediante tablas virtuales
Generador de datos de Azure usa un controlador ODBC integrado para conectarse y copiar los datos de la base de datos MongoDB. Para los tipos complejos como matrices u objetos con diferentes tipos en los documentos, el controlador normaliza volver a datos en tablas virtuales correspondientes. Más concretamente, si una tabla contiene estas columnas, el controlador genera las siguientes tablas virtuales:

-   Una **tabla base**, que contiene los mismos datos como una tabla real excepto para las columnas de tipo complejo. La tabla base, utiliza el mismo nombre que la tabla real que representa.
-   Una **tabla virtual** para cada columna de tipo complejo, que se expande los datos anidados. Las tablas virtuales se denominan con el nombre de la tabla real, un separador "_" y el nombre de la matriz o el objeto.

Tablas virtuales hacen referencia a los datos en la tabla real, habilitar el controlador tener acceso a los datos sin normalizar. Vea la sección ejemplo debajo de detalles. Tener acceso al contenido de las matrices MongoDB consultando y unirse a las tablas virtuales.

Puede usar el [Asistente para copiar](data-factory-data-movement-activities.md#data-factory-copy-wizard) intuitivamente ver la lista de tablas de base de datos MongoDB incluyendo las tablas virtuales y obtener una vista previa de los datos. También puede crear una consulta en el Asistente para copiar y validar para ver el resultado.

### <a name="example"></a>Ejemplo

Por ejemplo, "ExampleTable" debajo es una tabla de MongoDB que tenga una columna con una matriz de objetos en cada celda – facturas y una columna con una matriz de tipos de escalar: clasificaciones. 

_ID | Nombre del cliente | Facturas | Nivel de servicio | Clasificaciones
--- | ------------- | -------- | ------------- | -------
1111 | ABC | [{invoice_id: elemento "123": "Tostadora", precio: descuento "456": "0,2"}, {invoice_id: elemento "124": "térmico", precio: descuento "1235": "0,2"}] | Plata | [5,6]
2222 | XYZ | [{invoice_id: "135" producto: "frigorífico", precio: descuento "12543": "0,0"}] | Oro | [1,2]

El controlador generará varias tablas virtuales para representar esta tabla única. La primera tabla virtual es la tabla base denominada "ExampleTable", se muestra a continuación. La tabla base contiene todos los datos de la tabla original, pero los datos de las matrices se omite y se expanden en las tablas virtuales.

_ID | Nombre del cliente | Nivel de servicio
--- | ------------- | -------------
1111 | ABC | Plata
2222 | XYZ | Oro

Las siguientes tablas muestran las tablas virtuales que representan las matrices originales en el ejemplo. Estas tablas contienen lo siguiente: 

- Una referencia a la columna de clave principal original correspondiente a la fila de la matriz original (a través de la columna _id)
- Una indicación de la posición de los datos de la matriz original 
- Los datos de cada elemento de la matriz expandidos

Tabla "ExampleTable_Invoices":

_ID | ExampleTable_Invoices_dim1_idx | invoice_id | elemento | precio | Descuento
--- | -------------- | ---------- | ---- | ----- | --------
1111 | 0 | 123 | Tostadora | 456 | 0,2
1111 | 1 | 124 | térmico | 1235 | 0,2
2222 | 0 | 135 | frigorífico | 12543 | 0.0

Tabla "ExampleTable_Ratings":

_ID | ExampleTable_Ratings_dim1_idx | ExampleTable_Ratings
--- | ------------- | -------------
1111 | 0 | 5
1111 | 1 | 6
2222 | 0 | 1
2222 | 1 | 2

[AZURE.INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]

[AZURE.INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

## <a name="performance-and-tuning"></a>Rendimiento y optimización  
Consulte [Guía de optimización y rendimiento de la actividad de copia](data-factory-copy-activity-performance.md) para obtener información acerca de los factores clave que afectan al rendimiento de movimiento de datos (copiar actividad) en el generador de datos de Azure y distintas formas de optimizar.

## <a name="next-steps"></a>Pasos siguientes
Vea el artículo de [mover datos entre local y la nube](data-factory-move-data-between-onprem-and-cloud.md) para obtener instrucciones paso a paso para crear una canalización de datos que mueve los datos de un almacén de datos local a un almacén de datos de Azure. 

