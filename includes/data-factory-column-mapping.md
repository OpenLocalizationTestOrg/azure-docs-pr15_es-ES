## <a name="column-mapping-with-translator-rules"></a>Asignación de columnas con reglas de Minitraductor
Asignación de columna puede utilizarse para especificar cómo columnas especifican en la "estructura" del mapa de la tabla de origen a las columnas especificada en la estructura de"" de la tabla de receptor. La propiedad **columnMapping** está disponible en la sección **typeProperties** de la actividad de copia.

Asignación de columnas es compatible con las siguientes situaciones:

- Todas las columnas de la tabla de origen "estructura" se asignan a todas las columnas de la tabla de receptor "estructura".
- Un subconjunto de las columnas de la tabla de origen "estructura" se asignan a todas las columnas de la tabla de receptor "estructura".

Los siguientes son las condiciones de error y se producirán una excepción:

- Menos columnas o en más columnas en la "estructura" de la tabla de receptor que especifica en la asignación.
- Asignación duplicada.
- Resultado de la consulta SQL no tiene un nombre de columna especificado en la asignación.

## <a name="column-mapping-samples"></a>Ejemplos de asignación de columna
> [AZURE.NOTE] Los ejemplos siguientes son para SQL Azure y blobs de Windows Azure, pero son aplicables a cualquier almacén de datos que admita rectangular conjuntos de datos. Tendrá que ajustar el conjunto de datos y las definiciones de servicio vinculadas en los ejemplos siguientes para que apunten a los datos de origen de datos pertinentes. 

### <a name="sample-1--column-mapping-from-azure-sql-to-azure-blob"></a>Ejemplo 1: columna equivalencia de SQL Azure de blobs de Windows Azure
En este ejemplo, la tabla de entrada tiene una estructura y apunte a una tabla SQL en una base de datos de SQL Azure.

    {
        "name": "AzureSQLInput",
        "properties": {
            "structure": 
             [
               { "name": "userid"},
               { "name": "name"},
               { "name": "group"}
             ],
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
            "policy": {
                "externalData": {
                    "retryInterval": "00:01:00",
                    "retryTimeout": "00:10:00",
                    "maximumRetry": 3
                }
            }
        }
    }

En este ejemplo, la tabla de resultados tiene una estructura y apunte a un blob en un almacenamiento de blobs de Windows Azure.

    {
        "name": "AzureBlobOutput",
        "properties":
        {
             "structure": 
              [
                    { "name": "myuserid"},
                    { "name": "myname" },
                    { "name": "mygroup"}
              ],
            "type": "AzureBlob",
            "linkedServiceName": "StorageLinkedService",
            "typeProperties": {
                "folderPath": "mycontainer/myfolder",
                "fileName":"myfile.csv",
                "format":
                {
                    "type": "TextFormat",
                    "columnDelimiter": ","
                }
            },
            "availability":
            {
                "frequency": "Hour",
                "interval": 1
            }
        }
    }

A continuación se muestra el JSON para la actividad. Las columnas de origen que se asignan a columnas de Lavabo (**columnMappings**) mediante **traductor** propiedad.

    {
        "name": "CopyActivity",
        "description": "description", 
        "type": "Copy",
        "inputs":  [ { "name": "AzureSQLInput"  } ],
        "outputs":  [ { "name": "AzureBlobOutput" } ],
        "typeProperties":    {
            "source":
            {
                "type": "SqlSource"
            },
            "sink":
            {
                "type": "BlobSink"
            },
            "translator": 
            {
                "type": "TabularTranslator",
                "ColumnMappings": "UserId: MyUserId, Group: MyGroup, Name: MyName"
            }
        },
       "scheduler": {
              "frequency": "Hour",
              "interval": 1
            }
    }

**Flujo de la asignación de columna:**

![Flujo de la asignación de columna](./media/data-factory-data-stores-with-rectangular-tables/column-mapping-flow.png)

### <a name="sample-2--column-mapping-with-sql-query-from-azure-sql-to-azure-blob"></a>Ejemplo 2: consulta SQL desde SQL Azure blobs de Windows Azure en la asignación de columnas
En este ejemplo, se utiliza una consulta SQL para extraer datos de SQL Azure en lugar de simplemente especificar el nombre de tabla y los nombres de columna en la sección "estructura". 

    {
        "name": "CopyActivity",
        "description": "description", 
        "type": "CopyActivity",
        "inputs":  [ { "name": " AzureSQLInput"  } ],
        "outputs":  [ { "name": " AzureBlobOutput" } ],
        "typeProperties":
        {
            "source":
            {
                "type": "SqlSource",
                "SqlReaderQuery": "$$Text.Format('SELECT * FROM MyTable WHERE StartDateTime = \\'{0:yyyyMMdd-HH}\\'', WindowStart)"
            },
            "sink":
            {
                "type": "BlobSink"
            },
            "Translator": 
            {
                "type": "TabularTranslator",
                "ColumnMappings": "UserId: MyUserId, Group: MyGroup,Name: MyName"
            }
        },
        "scheduler": {
              "frequency": "Hour",
              "interval": 1
            }
    }

En este caso, los resultados de la consulta en primer lugar se asignan a las columnas especificadas en "estructura" de origen. A continuación, las columnas de origen "estructura" se asignan a las columnas de receptor "estructura" con las reglas especificadas en columnMappings.  Supongamos que la consulta devuelve 5 columnas, dos columnas adicionales, a continuación, los especificados en la estructura de"" de origen.

**Flujo de la asignación de columna**

![Columna de asignación flujo 2](./media/data-factory-data-stores-with-rectangular-tables/column-mapping-flow-2.png)







