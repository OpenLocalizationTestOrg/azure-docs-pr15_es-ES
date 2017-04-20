## <a name="invoking-stored-procedure-for-sql-sink"></a>Invocar el procedimiento almacenado de receptor de SQL

Al copiar datos de SQL Server o base de datos de Azure SQL y SQL Server, un usuario especificados procedimiento almacenado podría ser configurado e invoca con parámetros adicionales. 

Un procedimiento almacenado puede aprovecharse cuando mecanismos de copia integrada no tienen el propósito. Normalmente, esto es aprovechar al procesamiento adicional (combinar columnas, buscar valores adicionales, inserción en varias tablas...), debe hacerse antes de la inserción final de datos de origen en la tabla de destino. 

Puede invocar un procedimiento almacenado de elección. En el ejemplo siguiente se muestra cómo usar un procedimiento almacenado para realizar una inserción simple en una tabla de la base de datos. 

**Conjunto de datos de salida**

En este ejemplo, el tipo se establece en: SqlServerTable. Establecer a AzureSqlTable utilizar con una base de datos de SQL Azure. 

    {
      "name": "SqlOutput",
      "properties": {
        "type": "SqlServerTable",
        "linkedServiceName": "SqlLinkedService",
        "typeProperties": {
          "tableName": "Marketing"
        },
        "availability": {
          "frequency": "Hour",
          "interval": 1
        }
      }
    }
    
Definir la sección SqlSink en la actividad de copia JSON como sigue. Para llamar a un procedimiento almacenado al insertar datos, propiedades SqlWriterStoredProcedureName y SqlWriterTableType son necesarios.

    "sink":
    {
        "type": "SqlSink",
        "SqlWriterTableType": "MarketingType",
        "SqlWriterStoredProcedureName": "spOverwriteMarketing", 
        "storedProcedureParameters":
                {
                    "stringData": 
                    {
                        "value": "str1"     
                    }
                }
    }

En la base de datos, defina el procedimiento almacenado con el mismo nombre como SqlWriterStoredProcedureName. Controladores de entrada de datos desde el origen especificado y el insertar en la tabla de resultados. Observe que el nombre de parámetro del procedimiento almacenado debe ser el mismo que el nombre de tabla definido en el archivo de tabla JSON.

    CREATE PROCEDURE spOverwriteMarketing @Marketing [dbo].[MarketingType] READONLY, @stringData varchar(256)
    AS
    BEGIN
        DELETE FROM [dbo].[Marketing] where ProfileID = @stringData
        INSERT [dbo].[Marketing](ProfileID, State)
        SELECT * FROM @Marketing
    END

En la base de datos, defina el tipo de tabla con el mismo nombre como SqlWriterTableType. Observe que el esquema del tipo de tabla debe ser igual que el esquema devuelto por los datos de entrada.

    CREATE TYPE [dbo].[MarketingType] AS TABLE(
        [ProfileID] [varchar](256) NOT NULL,
        [State] [varchar](256) NOT NULL
    )

La característica de procedimiento almacenado aprovecha [Parámetros con valores de tabla](https://msdn.microsoft.com/library/bb675163.aspx).