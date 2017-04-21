<properties 
    pageTitle="Mover los datos a o de DocumentDB | Microsoft Azure" 
    description="Obtenga información sobre cómo mover los datos a o de la colección de Azure DocumentDB con el generador de datos de Azure" 
    services="data-factory, documentdb" 
    documentationCenter="" 
    authors="linda33wj" 
    manager="jhubbard" 
    editor="monicar"/>

<tags 
    ms.service="multiple" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/26/2016" 
    ms.author="jingwang"/>

# <a name="move-data-to-and-from-documentdb-using-azure-data-factory"></a>Mover los datos hacia y desde DocumentDB con el generador de datos de Azure

En este artículo se describe cómo puede usar la actividad de copia de un generador de datos de Azure para mover datos a Azure DocumentDB de datos de otras almacenan y mover datos de DocumentDB a otro almacén de datos. En este artículo se basa en el artículo de [actividades de movimiento de datos](data-factory-data-movement-activities.md) , que ofrece una descripción general de movimiento de datos con la actividad de copia y combinaciones de almacén de datos compatibles.

Los siguientes ejemplos muestran cómo copiar datos a y desde DocumentDB de Azure y el almacenamiento de blobs de Windows Azure. Sin embargo, los datos pueden ser copiado **directamente** desde cualquiera de los orígenes a cualquiera de los receptores indicada [aquí](data-factory-data-movement-activities.md#supported-data-stores) mediante la actividad de copia en el generador de datos de Azure.  

> [AZURE.NOTE] Copiar datos de on-local/Azure IaaS datos almacena a DocumentDB de Azure y viceversa son compatibles con Data Management Gateway versión 2.1 y superiores.

## <a name="sample-copy-data-from-documentdb-to-azure-blob"></a>Ejemplo: Copiar datos de DocumentDB a blobs de Windows Azure

El siguiente ejemplo muestra:

1. Servicio de tipo [DocumentDb](#azure-documentdb-linked-service-properties)vinculado.
2. Servicio de tipo [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties)vinculado. 
3. Un [conjunto de datos](data-factory-create-datasets.md) a la entrada de tipo [DocumentDbCollection](#azure-documentdb-dataset-type-properties). 
4. Un resultado [conjunto de datos](data-factory-create-datasets.md) de tipo [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties).
4. [Canalización](data-factory-create-pipelines.md) con actividad copia que utiliza [DocumentDbCollectionSource](#azure-documentdb-copy-activity-type-properties) y [BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties).

El ejemplo copia datos de Azure DocumentDB a blobs de Windows Azure. Las propiedades JSON usadas en estos ejemplos se describen en secciones siguiendo los ejemplos.

**Servicio de Azure DocumentDB vinculada:**

    {
      "name": "DocumentDbLinkedService",
      "properties": {
        "type": "DocumentDb",
        "typeProperties": {
          "connectionString": "AccountEndpoint=<EndpointUrl>;AccountKey=<AccessKey>;Database=<Database>"
        }
      }
    }

**Almacenamiento de blobs de Windows Azure vinculado servicio:**

    {
      "name": "StorageLinkedService",
      "properties": {
        "type": "AzureStorage",
        "typeProperties": {
          "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
        }
      }
    }

**Conjunto de datos de entrada de Azure DB de documento:**

En el ejemplo se supone que tiene una colección con el nombre de **la persona** en una base de datos de Azure DocumentDB.
 
Establecer "externo": "true" y especificar la información de directiva de externalData servicio del generador de datos de Azure que la tabla, el generador de datos externo y no se produce por una actividad en el generador de datos.

    {
      "name": "PersonDocumentDbTable",
      "properties": {
        "type": "DocumentDbCollection",
        "linkedServiceName": "DocumentDbLinkedService",
        "typeProperties": {
          "collectionName": "Person"
        },
        "external": true,
        "availability": {
          "frequency": "Day",
          "interval": 1
        }
      }
    }


**Conjunto de datos de salida de blobs de Windows Azure:**

Datos se copian a un nuevo blob cada hora con la ruta de acceso para el blob que refleja el valor de datetime específico con detalle de hora.

    {
      "name": "PersonBlobTableOut",
      "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
          "folderPath": "docdb",
          "format": {
            "type": "TextFormat",
            "columnDelimiter": ",",
            "nullValue": "NULL"
          }
        },
        "availability": {
          "frequency": "Day",
          "interval": 1
        }
      }
    }

Ejemplo JSON de documento en la colección de persona en una base de datos de DocumentDB: 

    {
      "PersonId": 2,
      "Name": {
        "First": "Jane",
        "Middle": "",
        "Last": "Doe"
      }
    }

DocumentDB admite consultar documentos utilizando un SQL como sintaxis sobre documentos JSON jerárquicas. 

Ejemplo: Seleccione Person.Name.Middle Person.PersonId, Person.Name.First como nombre, como MiddleName, Person.Name.Last AS apellido de persona

La canalización de la siguiente copia los datos de la colección de la persona en la base de datos de DocumentDB a un blobs de Windows Azure. Como parte de la actividad de copia de la entrada y salida se han especificado los conjuntos de datos.  
    
    {
      "name": "DocDbToBlobPipeline",
      "properties": {
        "activities": [
          {
            "type": "Copy",
            "typeProperties": {
              "source": {
                "type": "DocumentDbCollectionSource",
                "query": "SELECT Person.Id, Person.Name.First AS FirstName, Person.Name.Middle as MiddleName, Person.Name.Last AS LastName FROM Person",
                "nestingSeparator": "."
              },
              "sink": {
                "type": "BlobSink",
                "blobWriterAddHeader": true,
                "writeBatchSize": 1000,
                "writeBatchTimeout": "00:00:59"
              }
            },
            "inputs": [
              {
                "name": "PersonDocumentDbTable"
              }
            ],
            "outputs": [
              {
                "name": "PersonBlobTableOut"
              }
            ],
            "policy": {
              "concurrency": 1
            },
            "name": "CopyFromDocDbToBlob"
          }
        ],
        "start": "2015-04-01T00:00:00Z",
        "end": "2015-04-02T00:00:00Z"
      }
    }

## <a name="sample-copy-data-from-azure-blob-to-azure-documentdb"></a>Ejemplo: Copiar datos de blobs de Windows Azure a DocumentDB de Azure

El siguiente ejemplo muestra:

1. Servicio de tipo [DocumentDb](#azure-documentdb-linked-service-properties)vinculado.
2. Servicio de tipo [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties)vinculado.
3. Un [conjunto de datos](data-factory-create-datasets.md) a la entrada de tipo [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties).
4. Un resultado [conjunto de datos](data-factory-create-datasets.md) de tipo [DocumentDbCollection](#azure-documentdb-dataset-type-properties). 
4. [Canalización](data-factory-create-pipelines.md) con actividad copia que utiliza [BlobSource](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties) y [DocumentDbCollectionSink](#azure-documentdb-copy-activity-type-properties).


El ejemplo copia los datos de Azure blob a DocumentDB de Azure. Las propiedades JSON usadas en estos ejemplos se describen en secciones siguiendo los ejemplos.

**Almacenamiento de blobs de Windows Azure vinculado servicio:**
    
    {
      "name": "StorageLinkedService",
      "properties": {
        "type": "AzureStorage",
        "typeProperties": {
          "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
        }
      }
    }

**Servicio de Azure DocumentDB vinculada:**
    
    {
      "name": "DocumentDbLinkedService",
      "properties": {
        "type": "DocumentDb",
        "typeProperties": {
          "connectionString": "AccountEndpoint=<EndpointUrl>;AccountKey=<AccessKey>;Database=<Database>"
        }
      }
    }

**Dataset de entrada de blobs de Windows Azure:**

    {
      "name": "PersonBlobTableIn",
      "properties": {
        "structure": [
          {
            "name": "Id",
            "type": "Int"
          },
          {
            "name": "FirstName",
            "type": "String"
          },
          {
            "name": "MiddleName",
            "type": "String"
          },
          {
            "name": "LastName",
            "type": "String"
          }
        ],
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
          "fileName": "input.csv",
          "folderPath": "docdb",
          "format": {
            "type": "TextFormat",
            "columnDelimiter": ",",
            "nullValue": "NULL"
          }
        },
        "external": true,
        "availability": {
          "frequency": "Day",
          "interval": 1
        }
      }
    }

**Azure DocumentDB de salida del conjunto de datos:**

El ejemplo copia datos en una colección denominada a "Persona".

    {
      "name": "PersonDocumentDbTableOut",
      "properties": {
        "structure": [
          {
            "name": "Id",
            "type": "Int"
          },
          {
            "name": "Name.First",
            "type": "String"
          },
          {
            "name": "Name.Middle",
            "type": "String"
          },
          {
            "name": "Name.Last",
            "type": "String"
          }
        ],
        "type": "DocumentDbCollection",
        "linkedServiceName": "DocumentDbLinkedService",
        "typeProperties": {
          "collectionName": "Person"
        },
        "availability": {
          "frequency": "Day",
          "interval": 1
        }
      }
    }

La canalización de la siguiente copia datos de blobs de Windows Azure a la colección de la persona en la DocumentDB. Como parte de la actividad de copia de la entrada y salida se han especificado los conjuntos de datos. 
    
    {
      "name": "BlobToDocDbPipeline",
      "properties": {
        "activities": [
          {
            "type": "Copy",
            "typeProperties": {
              "source": {
                "type": "BlobSource"
              },
              "sink": {
                "type": "DocumentDbCollectionSink",
                "nestingSeparator": ".",
                "writeBatchSize": 2,
                "writeBatchTimeout": "00:00:00"
              }
              "translator": {
                  "type": "TabularTranslator",
                  "ColumnMappings": "FirstName: Name.First, MiddleName: Name.Middle, LastName: Name.Last, BusinessEntityID: BusinessEntityID, PersonType: PersonType, NameStyle: NameStyle, Title: Title, Suffix: Suffix, EmailPromotion: EmailPromotion, rowguid: rowguid, ModifiedDate: ModifiedDate"
              }
            },
            "inputs": [
              {
                "name": "PersonBlobTableIn"
              }
            ],
            "outputs": [
              {
                "name": "PersonDocumentDbTableOut"
              }
            ],
            "policy": {
              "concurrency": 1
            },
            "name": "CopyFromBlobToDocDb"
          }
        ],
        "start": "2015-04-14T00:00:00Z",
        "end": "2015-04-15T00:00:00Z"
      }
    }
 
Si es la entrada de blobs de ejemplo 

    1,John,,Doe

A continuación, el resultado JSON en DocumentDB será como:

    {
      "Id": 1,
      "Name": {
        "First": "John",
        "Middle": null,
        "Last": "Doe"
      },
      "id": "a5e8595c-62ec-4554-a118-3940f4ff70b6"
    }
    
DocumentDB es un almacén de documentos JSON, donde se permiten estructuras anidadas. Generador de datos de Azure permite usuario denotan jerarquía a través de **nestingSeparator**, que es "." en este ejemplo. Con el separador, la actividad de copia generará el objeto "Nombre" con elementos tres secundarios en primer lugar, segundo nombre y apellido, según "Apellido.nombre", "Name.Middle" y "Name.Last" en la definición de tabla.

## <a name="azure-documentdb-linked-service-properties"></a>Propiedades del servicio de DocumentDB vinculada de Azure

La tabla siguiente proporciona la descripción de los elementos JSON específicos al servicio DocumentDB de Azure vinculado. 

| **(Propiedad)** | **Descripción** | **Obligatorio** |
| -------- | ----------- | --------- |
| tipo | La propiedad tipo debe estar establecida en: **DocumentDb** | Sí |
| cadena de conexión | Especifique la información necesaria para conectarse a la base de datos de Azure DocumentDB. | Sí |

## <a name="azure-documentdb-dataset-type-properties"></a>Propiedades de tipo de Azure DocumentDB Dataset

Para obtener una lista completa de secciones y propiedades disponibles para definir conjuntos de datos, consulte el artículo [crear conjuntos de datos](data-factory-create-datasets.md) . Secciones como estructura, la disponibilidad y la directiva de un conjunto de datos JSON son similares para todos los tipos de conjunto de datos (SQL Azure, blobs de Windows Azure, tablas de Azure, etcetera.).
 
La sección typeProperties es diferente para cada tipo de conjunto de datos y proporciona información sobre la ubicación de los datos en el almacén de datos. La sección de typeProperties para el conjunto de datos de tipo **DocumentDbCollection** tiene las siguientes propiedades.

| **(Propiedad)** | **Descripción** | **Obligatorio** |
| -------- | ----------- | -------- |
| collectionName | Nombre de la colección de documentos de DocumentDB. | Sí |


Ejemplo:

    {
      "name": "PersonDocumentDbTable",
      "properties": {
        "type": "DocumentDbCollection",
        "linkedServiceName": "DocumentDbLinkedService",
        "typeProperties": {
          "collectionName": "Person"
        },
        "external": true,
        "availability": {
          "frequency": "Day",
          "interval": 1
        }
      }
    }

### <a name="schema-by-data-factory"></a>Esquema de generador de datos
Para almacena datos sin esquema como DocumentDB, el servicio de datos fábrica deduce el esquema de una de las siguientes maneras:  

1.  Si especifica la estructura de datos utilizando la propiedad de la **estructura** de la definición de conjunto de datos, el servicio de generador de datos respeta la estructura como el esquema. En este caso, si una fila contiene un valor de una columna, se proporcionará un valor nulo para el mismo.
2.  Si no especifica la estructura de datos utilizando la propiedad de la **estructura** de la definición de conjunto de datos, el servicio de datos fábrica deduce el esquema utilizando la primera fila de los datos. En este caso, si la primera fila contiene el esquema completo, algunas columnas será presentes en el resultado de la operación de copia.

Por lo tanto, para los orígenes de datos sin esquema, lo mejor es especificar la estructura de datos utilizando la propiedad de la **estructura** .

## <a name="azure-documentdb-copy-activity-type-properties"></a>Propiedades de tipo de actividad de copia de DocumentDB Azure

Para obtener una lista completa de secciones y propiedades disponibles para definir actividades, consulte el artículo de la [Creación de canalizaciones](data-factory-create-pipelines.md) . Propiedades como nombre, descripción, entrada y salida tablas y directiva están disponibles para todos los tipos de actividades.
 
**Nota:** La actividad de copia toma solo una entrada y genera un único resultado.

Propiedades disponibles en la sección typeProperties de la actividad por otro lado varían en función de cada tipo de actividad y en caso de actividad de copia que varían en función de los tipos de orígenes y receptores.

En caso de actividad de copia al origen es de tipo **DocumentDbCollectionSource** las siguientes propiedades están disponibles en la sección **typeProperties** :

| **(Propiedad)** | **Descripción** | **Valores permitidos** | **Obligatorio** |
| ------------ | --------------- | ------------------ | ------------ |
| consulta | Especifique la consulta para leer los datos. | Compatible con DocumentDB de cadena de consulta. <br/><br/>Ejemplo:`SELECT c.BusinessEntityID, c.PersonType, c.NameStyle, c.Title, c.Name.First AS FirstName, c.Name.Last AS LastName, c.Suffix, c.EmailPromotion FROM c WHERE c.ModifiedDate > \"2009-01-01T00:00:00\"` | No <br/><br/>Si no se especifica, la instrucción SQL que se ejecuta:`select <columns defined in structure> from mycollection` 
| nestingSeparator | Carácter especial para indicar que el documento está anidado | Cualquier carácter. <br/><br/>DocumentDB es un almacén de documentos JSON, donde se permiten estructuras anidadas. Generador de datos de Azure permite usuario denotan jerarquía a través de nestingSeparator, que es "." en los ejemplos anteriores. Con el separador, la actividad de copia generará el objeto "Nombre" con elementos tres secundarios en primer lugar, segundo nombre y apellido, según "Apellido.nombre", "Name.Middle" y "Name.Last" en la definición de tabla. | No

**DocumentDbCollectionSink** admite las siguientes propiedades:

| **(Propiedad)** | **Descripción** | **Valores permitidos** | **Obligatorio** |
| -------- | ----------- | -------------- | -------- |
| nestingSeparator | Es necesario un carácter especial en el nombre de columna de origen para indicar ese documento anidado. <br/><br/>Por ejemplo encima: `Name.First` en los resultados de la tabla genera la estructura JSON siguiente en el documento DocumentDB:<br/><br/>"Nombre": {<br/>  "Primero": "Juan"<br/>}, | Carácter que se utiliza para separar los niveles de anidamiento.<br/><br/>Valor predeterminado es `.` (punto). | Carácter que se utiliza para separar los niveles de anidamiento. <br/><br/>Valor predeterminado es `.` (punto). | No | 
| writeBatchSize | Número de solicitudes paralelas al servicio de DocumentDB para crear documentos.<br/><br/>Puede ajustar el rendimiento al copiar datos de DocumentDB mediante esta propiedad. Puede esperar un mejor rendimiento cuando aumentar writeBatchSize porque se envían más solicitudes paralelas a DocumentDB. Sin embargo deberá evitar limitación que puede producir el mensaje de error: "Solicitar tasa es grande".<br/><br/>Un número de factores, incluido el tamaño de los documentos, el número de términos de documentos, los índices de directiva de colección de destino, etcetera decide limitación. Para operaciones de copia, puede usar una colección mejor (por ejemplo, S3) tener el máximo rendimiento disponible (2500 solicitar unidades por segundo). | Entero | No (predeterminado: 10000) |
| writeBatchTimeout | Tiempo para la operación que debe completar antes de que se agota el tiempo de espera. | intervalo de tiempo<br/><br/> Ejemplo: "00: 30:00" (30 minutos). | No |
 
## <a name="appendix"></a>Apéndice
1. **Pregunta:**  
    ¿por qué la actualización de soporte técnico de actividad de copia de los registros existentes?

    **Respuesta:**  
    no.

2. **Pregunta:**  
    ¿cómo ya un reintento de una copia para tratar DocumentDB copia registros?

    **Respuesta:**  
    si registros tienen un campo "ID" y la operación de copia intenta insertar un registro con el mismo ID, la operación de copia produce un error.  
 
3. **Pregunta:** 
    generador de datos admite [rango o partición de datos basado en hash](https://azure.microsoft.com/documentation/articles/documentdb-partition-data/)? 

    **Respuesta:** 
    no. 
4. **Pregunta:** 
    ¿se puede especificar más de una colección de DocumentDB para una tabla?
    
    **Respuesta:** 
    no. Solo colección se puede especificar en este momento.
     
## <a name="performance-and-tuning"></a>Rendimiento y optimización  
Consulte [Guía de optimización y rendimiento de la actividad de copia](data-factory-copy-activity-performance.md) para obtener información acerca de los factores clave que afectan al rendimiento de movimiento de datos (copiar actividad) en el generador de datos de Azure y distintas formas de optimizar.
