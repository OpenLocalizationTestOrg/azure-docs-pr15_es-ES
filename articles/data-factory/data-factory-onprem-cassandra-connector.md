<properties 
    pageTitle="Mover los datos de Casandra con datos fábrica | Microsoft Azure" 
    description="Obtenga información sobre cómo mover los datos de una base de datos local Casandra con el generador de datos de Azure." 
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
    ms.date="09/07/2016" 
    ms.author="jingwang"/>

# <a name="move-data-from-an-on-premises-cassandra-database-using-azure-data-factory"></a>Mover los datos de una base de datos local Casandra con el generador de datos de Azure
En este artículo se describe cómo puede usar la actividad de copia de un generador de datos de Azure para copiar los datos desde una base de datos de Casandra local a cualquier almacén de datos que aparece en la columna receptor en la sección [orígenes compatibles y receptores](data-factory-data-movement-activities.md#supported-data-stores) . En este artículo se basa en el artículo de [actividades de movimiento de datos](data-factory-data-movement-activities.md) , que ofrece una descripción general de movimiento de datos con la actividad de copia y combinaciones de almacén de datos compatibles.

Generador de datos admite actualmente solo mover datos desde una base de datos de Casandra que [compatible con receptor datos almacena](data-factory-data-movement-activities.md#supported-data-stores), pero no mover datos desde otro almacena datos a una base de datos de Casandra.

## <a name="prerequisites"></a>Requisitos previos
Al servicio de generador de datos de Azure para que pueda conectarse a la base de datos de Casandra local, debe instalar lo siguiente: 

- Data Management Gateway 2.0 o superior en el mismo equipo que hospeda la base de datos o en un equipo distinto para evitar una competición de recursos con la base de datos. Data Management Gateway es un software que conecta los orígenes de datos locales a servicios en la nube de forma segura y administrada. Consulte el artículo de [mover datos entre local y la nube](data-factory-move-data-between-onprem-and-cloud.md) para obtener más información acerca de Data Management Gateway.
  
    Cuando se instala la puerta de enlace, instala automáticamente un controlador ODBC de Microsoft Casandra usado para conectarse a la base de datos de Casandra. 

> [AZURE.NOTE] Consulte [problemas de puerta de enlace de solución de problemas](data-factory-data-management-gateway.md#troubleshoot-gateway-issues) para obtener sugerencias sobre solución de problemas de conexión o puerta de enlace problemas relacionados con. 

## <a name="copy-data-wizard"></a>Asistente de datos de copia
La manera más sencilla de crear una canalización que copie datos de una base de datos de Casandra a cualquier el receptor compatibles de almacenamiento de datos es usar al Asistente de datos de copia. Consulte [Tutorial: crear una tubería mediante el Asistente para copiar](data-factory-copy-data-wizard-tutorial.md) para obtener un tutorial rápido sobre la creación de una canalización mediante el Asistente de datos de copia. 

En el ejemplo siguiente se proporciona definiciones de JSON de ejemplo que puede usar para crear una canalización mediante el [portal de Azure](data-factory-copy-activity-tutorial-using-azure-portal.md) o [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) o [PowerShell de Azure](data-factory-copy-activity-tutorial-using-powershell.md). Se muestra cómo copiar datos de la base de datos de Casandra para el almacenamiento de blobs de Windows Azure. Sin embargo, los datos se pueden copiar a cualquiera de los receptores indicada [aquí](data-factory-data-movement-activities.md#supported-data-stores) mediante la actividad de copia en el generador de datos de Azure.   


## <a name="sample-copy-data-from-cassandra-to-blob"></a>Ejemplo: Copiar datos de Casandra a Blob
El ejemplo copia datos de una base de datos de Casandra a un blobs de Windows Azure cada hora. Las propiedades JSON usadas en estos ejemplos se describen en secciones siguiendo los ejemplos. Datos se pueden copiar directamente a cualquiera de los receptores indicados en el artículo de [Actividades de movimiento de datos](data-factory-data-movement-activities.md#supported-data-stores) mediante la actividad de copia en el generador de datos de Azure. 

- Servicio de tipo [OnPremisesCassandra](#onpremisescassandra-linked-service-properties)vinculado.
- Servicio de tipo [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties)vinculado.
- Un [conjunto de datos](data-factory-create-datasets.md) a la entrada de tipo [CassandraTable](#cassandratable-properties).
- Un resultado [conjunto de datos](data-factory-create-datasets.md) de tipo [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties).
- [Canalización](data-factory-create-pipelines.md) con actividad copia que utiliza [CassandraSource](#cassandrasource-type-properties) y [BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties).

**Servicio de Casandra vinculado**

Este ejemplo usa el servicio de **Casandra** vinculado. Consulte la sección [Casandra vinculado servicio](#onpremisescassandra-linked-service-properties) para las propiedades admitidas por este servicio vinculado.  

    {
        "name": "CassandraLinkedService",
        "properties":
        {
            "type": "OnPremisesCassandra",
            "typeProperties":
            {
                "authenticationType": "Basic",
                "host": "mycassandraserver",
                "port": 9042,
                "username": "user",
                "password": "password",
                "gatewayName": "mygateway"
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

**Conjunto de datos de entrada de Casandra**

    {
        "name": "CassandraInput",
        "properties": {
            "linkedServiceName": "CassandraLinkedService",
            "type": "CassandraTable",
            "typeProperties": {
                "tableName": "mytable",
                "keySpace": "mykeyspace" 
            },
            "availability": {
                "frequency": "Hour",
                "interval": 1
            },
            "external": true,
            "policy": {
                "externalData": {
                    "retryInterval": "00:01:00",
                    "retryTimeout": "00:10:00",
                    "maximumRetry": 3
                }
            }
        }
    }

Establecer **externos** en **true** , servicio del generador de datos le informa de que el conjunto de datos, el generador de datos externo y no se produce en una actividad en el generador de datos.

**Conjunto de datos de salida de blobs de Windows Azure**

Se escriben datos en un nuevo blob cada hora (frecuencia: horas, intervalo: 1). 

    {
        "name": "AzureBlobOutput",
        "properties":
        {
            "type": "AzureBlob",
            "linkedServiceName": "AzureStorageLinkedService",
            "typeProperties":
            {
                "folderPath": "adfgetstarted/fromcassandra"
            },
            "availability":
            {
                "frequency": "Hour",
                "interval": 1
            }
        }
    }


**Canalización con actividad de copia**

La canalización contiene una actividad de la copia que está configurado para usar los conjuntos de datos de entrada y salida y se ha programado para que se ejecute cada hora. En la canalización de definición de JSON, el tipo de **origen** se establece en **CassandraSource** y tipo de **receptor** se establece en **BlobSink**. 

Ver [Propiedades de tipo de RelationalSource](#cassandrasource-type-properties) para la lista de propiedades compatibles con el RelationalSource. 
    
    {  
        "name":"SamplePipeline",
        "properties":{  
            "start":"2016-06-01T18:00:00",
            "end":"2016-06-01T19:00:00",
            "description":"pipeline with copy activity",
            "activities":[  
            {
                "name": "CassandraToAzureBlob",
                "description": "Copy from Cassandra to an Azure blob",
                "type": "Copy",
                "inputs": [
                {
                    "name": "CassandraInput"
                }
                ],
                "outputs": [
                {
                    "name": "AzureBlobOutput"
                }
                ],
                "typeProperties": {
                    "source": {
                        "type": "CassandraSource",
                        "query": "select id, firstname, lastname from mykeyspace.mytable"
        
                    },
                    "sink": {
                        "type": "BlobSink"
                    }
                },
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                },
                "policy": {
                    "concurrency": 1,
                    "executionPriorityOrder": "OldestFirst",
                    "retry": 0,
                    "timeout": "01:00:00"
                }
            }
            ]   
        }
    }
## <a name="onpremisescassandra-linked-service-properties"></a>Propiedades del servicio OnPremisesCassandra vinculado

La tabla siguiente proporciona la descripción para elementos JSON específicos al servicio Casandra vinculado.

| (Propiedad) | Descripción | Obligatorio |
| -------- | ----------- | -------- | 
| tipo | La propiedad tipo debe estar establecida en: **OnPremisesCassandra** | Sí |
| host | Una o más direcciones IP o nombres de host de servidores Casandra.<br/><br/>Especificar una lista de valores separados por comas de direcciones IP o nombres de host para conectarse a todos los servidores simultáneamente. | Sí | 
| puerto | El puerto TCP que utiliza el servidor de Casandra para escuchar las conexiones de cliente. | No, el valor de predeterminado: 9042 |
| authenticationType | Básica o anónima | Sí |
| nombre de usuario |Especifique el nombre de usuario para la cuenta de usuario. | Sí, si authenticationType se establece en básica. |
| contraseña | Especifique la contraseña para la cuenta de usuario.  | Sí, si authenticationType se establece en básica. |
| gatewayName | El nombre de la puerta de enlace que se usa para conectarse a la base de datos de Casandra local. | Sí |
| encryptedCredential | Credencial cifrado por la puerta de enlace. | No | 

## <a name="cassandratable-properties"></a>Propiedades de CassandraTable

Para obtener una lista completa de secciones y propiedades disponibles para definir conjuntos de datos, vea el artículo [crear conjuntos de datos](data-factory-create-datasets.md) . Secciones como estructura, la disponibilidad y la directiva de un conjunto de datos JSON son similares para todos los tipos de conjunto de datos (SQL Azure, blobs de Windows Azure, tablas de Azure, etcetera.).

La sección **typeProperties** es diferente para cada tipo de conjunto de datos y proporciona información sobre la ubicación de los datos en el almacén de datos. La sección de typeProperties para el conjunto de datos de tipo **CassandraTable** tiene las siguientes propiedades

| (Propiedad) | Descripción | Obligatorio |
| -------- | ----------- | -------- |
| espacio de claves | Nombre del espacio de claves o esquema de base de datos de Casandra. | Sí (si no se ha definido la **consulta** para **CassandraSource** ). |
| nombre de tabla | Nombre de la tabla de base de datos de Casandra. | Sí (si no se ha definido la **consulta** para **CassandraSource** ). |


## <a name="cassandrasource-type-properties"></a>Propiedades de tipo de CassandraSource
Para obtener una lista completa de secciones y propiedades disponibles para definir actividades, vea el artículo de la [Creación de canalizaciones](data-factory-create-pipelines.md) . Propiedades como nombre, descripción, entrada y salida tablas y directiva están disponibles para todos los tipos de actividades. 

Propiedades disponibles en la sección typeProperties de la actividad por otro lado varían en función de cada tipo de actividad. Actividad de copia, que varían en función de los tipos de orígenes y receptores.

Cuando el origen es de tipo **CassandraSource**, las siguientes propiedades están disponibles en la sección typeProperties:

| (Propiedad) | Descripción | Valores permitidos | Obligatorio |
| -------- | ----------- | -------------- | -------- |
| consulta | Uso de la consulta personalizada para leer datos. | Consulta SQL-92 o consulta CQL. Vea [referencia de CQL](https://docs.datastax.com/en/cql/3.1/cql/cql_reference/cqlReferenceTOC.html). <br/><br/>Cuando se usa la consulta SQL, especifique el **nombre de espacio de claves name.table** para representar la tabla que desea consultar. | No (si se definen el nombre de tabla y el espacio de claves en el conjunto de datos).  |
| consistencyLevel | El nivel de coherencia especifica cuántas réplicas deben responder a una solicitud de lectura antes de devolver datos a la aplicación cliente. Casandra comprueba el número especificado de réplicas de datos cumplir con la solicitud de lectura. | UNO, DOS, TRES, QUÓRUM, TODOS, LOCAL_QUORUM, EACH_QUORUM, LOCAL_ONE. Para obtener información detallada, vea [Configurar coherencia de los datos](http://docs.datastax.com/en//cassandra/2.0/cassandra/dml/dml_config_consistency_c.html) . | No. Valor predeterminado es uno. |  


### <a name="type-mapping-for-cassandra"></a>Asignación de tipo para Casandra
Tipo de Casandra | Tipo basado en .net
--------------- | ---------------
ASCII | Cadena 
BIGINT | Int64
BLOB | Byte]
VALOR BOOLEANO | Valor booleano
DECIMAL | Decimal
DOBLE | Doble
HACER FLOTANTE | Solo
INET | Cadena
INT | Int32
TEXTO | Cadena
MARCA DE TIEMPO | Fecha y hora
TIMEUUID | GUID
UUID | GUID
VARCHAR | Cadena
VARINT | Decimal

> [AZURE.NOTE]  
> Colección tipos (mapa, conjunto, lista, etc.), consulte [trabajar con tipos de colección de Casandra usando tabla virtual](#work-with-collections-using-virtual-table) sección. 
> 
> No se admiten tipos definidos por el usuario.
> 
> La longitud de la columna binaria y columna de cadena de longitud no puede ser superior a 4000. 

## <a name="work-with-collections-using-virtual-table"></a>Trabajar con colecciones con tabla virtual
Generador de datos de Azure usa un controlador ODBC integrado para conectarse y copiar los datos de la base de datos de Casandra. Para los tipos de colección incluidos mapa, establecer y lista, el controlador de normalización de volver a los datos en tablas virtuales correspondientes. Más concretamente, si una tabla contiene todas las columnas de la colección, el controlador genera las siguientes tablas virtuales:

-   Una **tabla base**, que contiene los mismos datos como una tabla real excepto para las columnas de la colección. La tabla base, utiliza el mismo nombre que la tabla real que representa.
-   Una **tabla virtual** para cada columna de la colección, que se expande los datos anidados. Las tablas virtuales que representan colecciones se denominan con el nombre de la tabla real, un separador "_vt_" y el nombre de la columna.

Tablas virtuales hacen referencia a los datos en la tabla real, habilitar el controlador tener acceso a los datos sin normalizar. Para obtener más información, vea la sección ejemplo. Tener acceso al contenido de las colecciones de Casandra consultando y unirse a las tablas virtuales.

Puede aprovechar el [Asistente para copiar](data-factory-data-movement-activities.md#data-factory-copy-wizard) para intuitivamente ver la lista de tablas de base de datos de Casandra incluye las tablas virtuales y obtener una vista previa de los datos. También puede crear una consulta en el Asistente para copiar y validar para ver el resultado.

### <a name="example"></a>Ejemplo
Por ejemplo, el siguiente "ExampleTable" es una tabla de base de datos de Casandra que contiene una columna de clave de entero principal denominada "pk_int", una columna de texto con el nombre de valor, una columna de la lista, una columna de mapa y una columna de conjunto (denominada "StringSet").

pk_int | Valor | Lista | Mapa |   StringSet
------ | ----- | ---- | --- | --------
1 | "valor de ejemplo 1" | ["1", "2", "3"]  | {"S1": "a", "S2": "b"} | {"A", "B", "C"}
3 | "valor de ejemplo 3" | ["100", "101", "102", "105"] | {"S1": "t"} | {"A", "E"}

El controlador generará varias tablas virtuales para representar esta tabla única. Las columnas de clave externa en las tablas virtuales hacen referencia a las columnas de clave principal en la tabla real e indican que la fila de tabla virtual corresponde a una fila de tabla real. 

La primera tabla virtual es la tabla base denominada "ExampleTable" se muestra en la tabla siguiente. La tabla base contiene los mismos datos que la tabla de base de datos original excepto las colecciones, que se omiten de esta tabla y expandido en otras tablas virtuales.

pk_int | Valor
------ | -----
1 | "valor de ejemplo 1"
3 | "valor de ejemplo 3"

Las siguientes tablas muestran las tablas virtuales que renormalize los datos de las columnas de la lista, mapa y StringSet. Las columnas con nombres que terminan con "_index" o "_key" indican la posición de los datos de la lista o la asignación original. Las columnas con nombres que terminan con "_value" contienen los datos expandidos de la colección.

#### <a name="table-exampletablevtlist"></a>Tabla "ExampleTable_vt_List":
pk_int | List_index | List_value
------ | ---------- | ----------
1 | 0 | 1
1 | 1 | 2
1 | 2 | 3
3 | 0 | 100
3 | 1 | 101
3 | 2 | 102
3 | 3 | 103

#### <a name="table-exampletablevtmap"></a>Tabla "ExampleTable_vt_Map":
pk_int | Map_key | Map_value
------ | ------- | ---------
1 | S1 | RESPUESTAS
1 | S2 | b
3 | S1 | t

#### <a name="table-exampletablevtstringset"></a>Tabla "ExampleTable_vt_StringSet":
pk_int | StringSet_value
------ | ---------------
1 | RESPUESTAS
1 | B
1 | C
3 | RESPUESTAS
3 | E





[AZURE.INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]
[AZURE.INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

## <a name="performance-and-tuning"></a>Rendimiento y optimización  
Consulte [Guía de optimización y rendimiento de la actividad de copia](data-factory-copy-activity-performance.md) para obtener información acerca de los factores clave que afectan al rendimiento de movimiento de datos (copiar actividad) en el generador de datos de Azure y distintas formas de optimizar.
