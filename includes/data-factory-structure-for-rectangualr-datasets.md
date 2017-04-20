## <a name="specifying-structure-definition-for-rectangular-datasets"></a>Especificar la definición de la estructura de conjuntos de datos rectangular
La sección de la estructura de los conjuntos de datos JSON es una sección **opcional** para tablas rectangulares (con filas y columnas) y contiene una colección de columnas de la tabla. La sección de la estructura que usa para cualquiera que proporciona información de tipo de conversiones de tipo o hacer las asignaciones de columna. Las secciones siguientes describen estas características en detalle. 

Cada columna contiene las siguientes propiedades:

| (Propiedad) | Descripción | Obligatorio |
| -------- | ----------- | -------- |
| nombre | Nombre de la columna. | Sí |
| tipo | Tipo de datos de la columna. Consulte la sección de conversiones de tipo siguiente para obtener más información sobre cuándo se debe especificar información de tipo | No |
| referencia cultural | Referencia cultural que se utilizará cuando se especifica tipo y tipo de .NET Datetime o Datetimeoffset basadas en .NET. El valor predeterminado es "en-us".  | No |
| formato | Cadena de formato que se utilizará cuando se especifica tipo y .NET escriba Datetime o Datetimeoffset. | No |

En el ejemplo siguiente se muestra la sección estructura JSON para una tabla que tiene tres columnas Id, nombre y lastlogindate.

    "structure": 
    [
        { "name": "userid"},
        { "name": "name"},
        { "name": "lastlogindate"}
    ],

Utilice las siguientes directrices para cuándo incluir información de "estructura" y qué incluir en la sección **estructura** .

- **Para orígenes de datos estructurados** que almacenan información de esquema y el tipo de datos junto con los datos en Sí (orígenes como tablas de Azure de SQL Server, Oracle, etc.), debe especificar la sección "estructura" solo si desea hacer la asignación de columnas específicas de columnas de origen a columnas específicas de receptor y sus nombres no son iguales (consulte los detalles en la sección de asignación de columna a continuación). 

    Como se mencionó anteriormente, la información de tipo es opcional en la sección "estructura". Información de tipo para orígenes estructurados, ya está disponible como parte de la definición de conjunto de datos en el almacén de datos, así que no debe incluir información de tipo cuando se incluye la sección "estructura".
- **Esquema de orígenes de datos de lectura (específicamente blobs de Azure)** puede elegir para almacenar datos sin almacenar la información de tipo o esquema con los datos. Para estos tipos de orígenes de datos debe incluir "estructura" en los siguientes 2 casos:
    - Desea hacer la asignación de columnas.
    - Cuando un origen en una actividad de copiar el conjunto de datos, puede proporcionar información de tipo en "estructura" y generador de datos usará esta información de tipo de conversión de tipos nativos para el receptor. Consulte el artículo de [mover los datos hacia y desde blobs de Windows Azure](../articles/data-factory/data-factory-azure-blob-connector.md) para obtener más información.

### <a name="supported-net-based-types"></a>Compatible. Tipos de red 
Generador de datos admite los siguientes CLS compatible con .NET según tipo valores para proporcionar información de tipo en "estructura" para esquema de orígenes de datos de lectura como blobs de Windows Azure.

- Int16
- Int32 
- Int64
- Solo
- Doble
- Decimal
- Byte]
- BOOL
- Cadena 
- GUID
- Fecha y hora
- DateTimeOffset
- Intervalo de tiempo 

Para Datetime y Datetimeoffset opcionalmente, también puede especificar la cadena "cultura" y "formato" para facilitar el análisis de la cadena de fecha y hora personalizada. Vea ejemplo de conversión de tipo a continuación.

