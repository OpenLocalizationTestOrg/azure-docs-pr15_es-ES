### <a name="type-conversion-sample"></a>Ejemplo de conversión de tipo
En el ejemplo siguiente es para copiar datos de un Blob a SQL Azure con las conversiones de tipo.

Supongamos que el conjunto de datos Blob en formato CSV y contiene 3 columnas. Una de ellas es una columna de fecha y hora con un formato de fecha y hora personalizado mediante francés abreviaturas para el día de la semana.

Definirá el conjunto de datos de origen de blobs manera junto con las definiciones de tipo para las columnas.

    {
        "name": "AzureBlobTypeSystemInput",
        "properties":
        {
             "structure": 
              [
                    { "name": "userid", "type": "Int64"},
                    { "name": "name", "type": "String"},
                    { "name": "lastlogindate", "type": "Datetime", "culture": "fr-fr", "format": "ddd-MM-YYYY"}
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
            "external": true,
            "availability":
            {
                "frequency": "Hour",
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

Dada la instrucción SQL tipo de tabla de asignación de tipo de .NET anterior definir la tabla de SQL Azure con el siguiente esquema.

| Nombre de columna | Tipo de SQL |
| ----------- | -------- |
| identificador de usuario | bigint |
| nombre | texto |
| LastLoginDate | fecha y hora |

A continuación se definen el conjunto de datos de SQL Azure como sigue. Nota: No es necesario especificar sección "estructura" con información de tipo, ya que la información de tipo ya está especificada en el almacén de datos subyacente.

    {
        "name": "AzureSQLOutput",
        "properties": {
            "type": "AzureSqlTable",
            "linkedServiceName": "AzureSqlLinkedService",
            "typeProperties": {
                "tableName": "MyTable"
            },
            "availability": {
                "frequency": "Hour",
                "interval": 1
            }
        }
    }

En este caso el generador de datos hará automáticamente el tipo de formato de conversiones, incluido el campo de fecha y hora con la fecha y hora personalizado mediante la referencia cultural NG NG al mover los datos de Blob a SQL Azure.


