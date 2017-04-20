## <a name="specifying-formats"></a>Especificar formatos

Generador de datos de Azure es compatible con los siguientes tipos de formato: 

- [Formato de texto](#specifying-textformat)
- [Formato JSON](#specifying-jsonformat)
- [Formato de Avro](#specifying-avroformat)
- [Formato ORC](#specifying-orcformat)
- [Formato de parqués](#specifying-parquetformat)

### <a name="specifying-textformat"></a>Especificar TextFormat

Si el formato se establece en **TextFormat**, puede especificar las siguientes propiedades **opcionales** en la sección **formato** .

| (Propiedad) | Descripción | Valores permitidos | Obligatorio |
| -------- | ----------- | -------- | -------- | 
| columnDelimiter | El carácter utilizado para separar columnas en un archivo. | Se permite un solo carácter. El valor predeterminado es una coma (','). <br/><br/>Para utilizar un carácter Unicode, consulte [Caracteres Unicode](https://en.wikipedia.org/wiki/List_of_Unicode_characters) para obtener el código correspondiente. Por ejemplo, puede para utilizar un carácter no imprimible poco comunes que probablemente no existe en los datos: especifique "\u0001" que representa el inicio de título (informe de mantenimiento). | No |
| rowDelimiter | El carácter utilizado para separar las filas en un archivo. | Se permite un solo carácter. El valor predeterminado es cualquiera de los siguientes valores en la lectura: ["\r\n", "\r", "\n"] y "\r\n" en escritura. | No |
| escapeChar | El carácter especial utilizado para anular un delimitador de columna en el contenido del archivo de entrada. <br/><br/>No puede especificar escapeChar y quoteChar para una tabla. | Se permite un solo carácter. Ningún valor predeterminado. <br/><br/>Ejemplo: si tiene una coma (', ') como el delimitador de columnas, pero desea que el carácter de coma en el texto (ejemplo: "Hola, mundo"), puede definir '$' como carácter de escape y utilizar la cadena "Hola$, world" en el origen. | No | 
| quoteChar | El carácter utilizado para un valor de cadena de presupuesto. Los delimitadores de columna y fila dentro de los caracteres de presupuesto se tratará como parte del valor de cadena. Esta propiedad es aplicable a los conjuntos de datos de entrada y salida.<br/><br/>No puede especificar escapeChar y quoteChar para una tabla. | Se permite un solo carácter. Ningún valor predeterminado. <br/><br/>Por ejemplo, si tiene una coma (', ') como el delimitador de columnas, pero desea tener el carácter de coma en el texto (ejemplo: < Hola, mundo >), puede definir "(comillas dobles) como el carácter de presupuesto y el uso de la cadena"Hola, mundo"en el origen. | No |
| nullValue | Uno o más caracteres que usa para representar un valor nulo. | Uno o varios caracteres. Los valores predeterminados son "\N" y "NULL" en "\N" y la lectura de escritura. | No |
| encodingName | Especifique el nombre de codificación. | Un nombre de codificación válido. vea [Encoding.EncodingName (propiedad).](https://msdn.microsoft.com/library/system.text.encoding.aspx) Ejemplo: windows-1250 o shift_jis. El valor predeterminado es UTF-8. | No | 
| Primerafilacomoencabezado | Especifica si se va a tener en cuenta la primera fila como encabezado. Para un conjunto de datos de entrada, el generador de datos lee primera fila como encabezado. Para un conjunto de datos de salida, el generador de datos escribe primera fila como encabezado. <br/><br/>Consulte [escenarios para utilizar **Primerafilacomoencabezado** y **skipLineCount** ](#scenarios-for-using-firstrowasheader-and-skiplinecount) para escenarios de ejemplo. | True<br/>False (predeterminado) | No |
| skipLineCount | Indica el número de filas que se omiten al leer datos de archivos de entrada. Si se especifican skipLineCount y Primerafilacomoencabezado, las líneas se omiten en primer lugar y, a continuación, se lee la información de encabezado del archivo de entrada. <br/><br/>Consulte [escenarios para utilizar Primerafilacomoencabezado y skipLineCount](#scenarios-for-using-firstrowasheader-and-skiplinecount) para escenarios de ejemplo. | Entero | No | 
| treatEmptyAsNull | Especifica si se va a tratar null o cadena vacía como un valor nulo al leer datos desde un archivo de entrada. | True (predeterminado)<br/>Falso | No |  

#### <a name="textformat-example"></a>Ejemplo de TextFormat
En el ejemplo siguiente se muestra algunas de las propiedades de formato para TextFormat.

    "typeProperties":
    {
        "folderPath": "mycontainer/myfolder",
        "fileName": "myblobname"
        "format":
        {
            "type": "TextFormat",
            "columnDelimiter": ",",
            "rowDelimiter": ";",
            "quoteChar": "\"",
            "NullValue": "NaN"
            "firstRowAsHeader": true,
            "skipLineCount": 0,
            "treatEmptyAsNull": true
        }
    },

Para usar un escapeChar en lugar de quoteChar, reemplace la línea con quoteChar con la escapeChar siguiente:

    "escapeChar": "$",



### <a name="scenarios-for-using-firstrowasheader-and-skiplinecount"></a>Escenarios para utilizar Primerafilacomoencabezado y skipLineCount

- Está copiando desde un origen de archivo no es un archivo de texto y desea agregar una línea de encabezado que contiene los metadatos de esquema (por ejemplo: esquema SQL). Especificar **Primerafilacomoencabezado** como true en el conjunto de datos de salida para este escenario. 
- Desde un archivo de texto que contiene una línea de encabezado a un receptor de archivo no se copia y le gustaría colocar dicha línea. Especificar **Primerafilacomoencabezado** como true en el conjunto de datos de entrada.
- Copia de un archivo de texto y desea omitir unas cuantas líneas al principio que no contienen datos o encabezado de información. Especificar **skipLineCount** para indicar el número de líneas que se pueden omitir. Si el resto del archivo contiene una línea de encabezado, también puede especificar **Primerafilacomoencabezado**. Si se especifican **skipLineCount** y **Primerafilacomoencabezado** , las líneas se omiten en primer lugar y, a continuación, se lee la información de encabezado del archivo de entrada

### <a name="specifying-avroformat"></a>Especificar AvroFormat
Si el formato se establece en AvroFormat, no es necesario especificar las propiedades de la sección formato dentro de la sección typeProperties. Ejemplo:

    "format":
    {
        "type": "AvroFormat",
    }

Para usar el formato de Avro en una tabla subárbol, consulte [tutorial de Apache subárbol](https://cwiki.apache.org/confluence/display/Hive/AvroSerDe).

Tenga en cuenta los siguientes puntos:  

- No se admiten [tipos de datos complejos](http://avro.apache.org/docs/current/spec.html#schema_complex) (registros, enumeraciones, matrices, mapas, uniones y fijo)

### <a name="specifying-jsonformat"></a>Especificar JsonFormat

Si el formato se establece en **JsonFormat**, puede especificar las siguientes propiedades **opcionales** en la sección **formato** .

| (Propiedad) | Descripción | Obligatorio |
| -------- | ----------- | -------- |
| filePattern | Indicar el modelo de datos almacenados en cada archivo JSON. Valores permitidos son: **setOfObjects** y **arrayOfObjects**. El valor **predeterminado** es: **setOfObjects**. Consulte las siguientes secciones para obtener información detallada sobre estos modelos.| No |
| encodingName | Especifique el nombre de codificación. Para la lista de nombres de codificación válidos, consulte: [Encoding.EncodingName](https://msdn.microsoft.com/library/system.text.encoding.aspx) propiedad. Por ejemplo: windows-1250 o shift_jis. El valor **predeterminado** es: **UTF-8**. | No | 
| nestingSeparator | Carácter que se utiliza para separar los niveles de anidamiento. El valor predeterminado es '.' (punto). | No | 


#### <a name="setofobjects-file-pattern"></a>patrón de archivo setOfObjects

Cada archivo contiene un objeto único o línea-delimitado o concatena varios objetos. Cuando se elige esta opción en un conjunto de datos de salida, copiar actividad genera un único archivo JSON con cada objeto por línea (delimitado por línea).

**objeto único** 

    {
        "time": "2015-04-29T07:12:20.9100000Z",
        "callingimsi": "466920403025604",
        "callingnum1": "678948008",
        "callingnum2": "567834760",
        "switch1": "China",
        "switch2": "Germany"
    }

**delimitado por línea JSON** 

    {"time":"2015-04-29T07:12:20.9100000Z","callingimsi":"466920403025604","callingnum1":"678948008","callingnum2":"567834760","switch1":"China","switch2":"Germany"}
    {"time":"2015-04-29T07:13:21.0220000Z","callingimsi":"466922202613463","callingnum1":"123436380","callingnum2":"789037573","switch1":"US","switch2":"UK"}
    {"time":"2015-04-29T07:13:21.4370000Z","callingimsi":"466923101048691","callingnum1":"678901578","callingnum2":"345626404","switch1":"Germany","switch2":"UK"}
    {"time":"2015-04-29T07:13:22.0960000Z","callingimsi":"466922202613463","callingnum1":"789037573","callingnum2":"789044691","switch1":"UK","switch2":"Australia"}
    {"time":"2015-04-29T07:13:22.0960000Z","callingimsi":"466922202613463","callingnum1":"123436380","callingnum2":"789044691","switch1":"US","switch2":"Australia"}

**JSON concatenadas.**

    {
        "time": "2015-04-29T07:12:20.9100000Z",
        "callingimsi": "466920403025604",
        "callingnum1": "678948008",
        "callingnum2": "567834760",
        "switch1": "China",
        "switch2": "Germany"
    }
    {
        "time": "2015-04-29T07:13:21.0220000Z",
        "callingimsi": "466922202613463",
        "callingnum1": "123436380",
        "callingnum2": "789037573",
        "switch1": "US",
        "switch2": "UK"
    }
    {
        "time": "2015-04-29T07:13:21.4370000Z",
        "callingimsi": "466923101048691",
        "callingnum1": "678901578",
        "callingnum2": "345626404",
        "switch1": "Germany",
        "switch2": "UK"
    }


#### <a name="arrayofobjects-file-pattern"></a>modelo de archivo arrayOfObjects. 

Cada archivo contiene una matriz de objetos. 

    [
        {
            "time": "2015-04-29T07:12:20.9100000Z",
            "callingimsi": "466920403025604",
            "callingnum1": "678948008",
            "callingnum2": "567834760",
            "switch1": "China",
            "switch2": "Germany"
        },
        {
            "time": "2015-04-29T07:13:21.0220000Z",
            "callingimsi": "466922202613463",
            "callingnum1": "123436380",
            "callingnum2": "789037573",
            "switch1": "US",
            "switch2": "UK"
        },
        {
            "time": "2015-04-29T07:13:21.4370000Z",
            "callingimsi": "466923101048691",
            "callingnum1": "678901578",
            "callingnum2": "345626404",
            "switch1": "Germany",
            "switch2": "UK"
        },
        {
            "time": "2015-04-29T07:13:22.0960000Z",
            "callingimsi": "466922202613463",
            "callingnum1": "789037573",
            "callingnum2": "789044691",
            "switch1": "UK",
            "switch2": "Australia"
        },
        {
            "time": "2015-04-29T07:13:22.0960000Z",
            "callingimsi": "466922202613463",
            "callingnum1": "123436380",
            "callingnum2": "789044691",
            "switch1": "US",
            "switch2": "Australia"
        },
        {
            "time": "2015-04-29T07:13:24.2120000Z",
            "callingimsi": "466922201102759",
            "callingnum1": "345698602",
            "callingnum2": "789097900",
            "switch1": "UK",
            "switch2": "Australia"
        },
        {
            "time": "2015-04-29T07:13:25.6320000Z",
            "callingimsi": "466923300236137",
            "callingnum1": "567850552",
            "callingnum2": "789086133",
            "switch1": "China",
            "switch2": "Germany"
        }
    ]

### <a name="jsonformat-example"></a>Ejemplo de JsonFormat

Si tiene un archivo JSON con el contenido siguiente:  

    {
        "Id": 1,
        "Name": {
            "First": "John",
            "Last": "Doe"
        },
        "Tags": ["Data Factory”, "Azure"]
    }

y desea copiar en una tabla de SQL Azure en el siguiente formato: 

Id.  | Apellido.nombre | Name.Middle | Name.Last | Etiquetas
--- | ---------- | ----------- | --------- | ----
1 | John | nulo | Bermejo | ["Datos Factory", "Azure"]

El conjunto de datos de entrada con el tipo de JsonFormat se define como sigue: (definición parcial con solo las partes pertinentes)

    "properties": {
        "structure": [
            {"name": "Id", "type": "Int"},
            {"name": "Name.First", "type": "String"},
            {"name": "Name.Middle", "type": "String"},
            {"name": "Name.Last", "type": "String"},
            {"name": "Tags", "type": "string"}
        ],
        "typeProperties":
        {
            "folderPath": "mycontainer/myfolder",
            "format":
            {
                "type": "JsonFormat",
                "filePattern": "setOfObjects",
                "encodingName": "UTF-8",
                "nestingSeparator": "."
            }
        }
    }

Si no se define la estructura, la actividad de copia acopla la estructura de forma predeterminada y copie cada cosa. 

#### <a name="supported-json-structure"></a>Estructura JSON compatible
Tenga en cuenta los siguientes puntos: 

- Cada objeto con una colección de pares nombre/valor se asigna a una fila de datos en un formato tabular. Es posible anidar objetos y puede definir cómo acoplar la estructura de un conjunto de datos con el separador de anidamiento (.) de forma predeterminada. Vea el [ejemplo JsonFormat](#jsonformat-example) sección para obtener un ejemplo anterior.  
- Si no está definida la estructura del conjunto de datos del generador de datos, la actividad de copia detecta el esquema desde el primer objeto y acoplar todo el objeto. 
- Si la entrada JSON tiene una matriz, la actividad de copia convierte el valor de la matriz completa en una cadena. Puede omitir mediante [asignación de columnas o filtros](#column-mapping-with-translator-rules).
- Si hay nombres duplicados en el mismo nivel, la actividad de copia toma el último.
- Nombres de propiedad distinguen mayúsculas de minúsculas. Dos propiedades con el mismo nombre pero canales para diferentes se tratan como dos propiedades independientes. 

### <a name="specifying-orcformat"></a>Especificar OrcFormat
Si el formato se establece en OrcFormat, no es necesario especificar las propiedades de la sección formato dentro de la sección typeProperties. Ejemplo:

    "format":
    {
        "type": "OrcFormat"
    }

> [AZURE.IMPORTANT] Si no va a copiar archivos ORC **como-es** entre local y la nube almacena datos, debe instalar el 8 de JRE (Java Runtime entorno) en el equipo de puerta de enlace. Una puerta de enlace de 64 bits requiere JRE de 64 bits y 32 bits gateway JRE de 32 bits. Puede encontrar las dos versiones de [aquí](http://go.microsoft.com/fwlink/?LinkId=808605). Seleccione el adecuado.

Tenga en cuenta los siguientes puntos:

-   Datos complejos tipos no son compatibles (estructura, mapa, lista, unión)
-   Archivo ORC tiene tres [Opciones de compresión](http://hortonworks.com/blog/orcfile-in-hdp-2-better-compression-better-performance/): ninguno, ZLIB, SNAPPY. Generador de datos admite la lectura de datos desde archivo ORC en cualquiera de estos formatos comprimidos. Utiliza la compresión códec está en los metadatos para leer los datos. Sin embargo, al escribir en un archivo ORC, el generador de datos elige ZLIB, que es el valor predeterminado para ORC. En este momento no hay ninguna opción para invalidar este comportamiento. 

### <a name="specifying-parquetformat"></a>Especificar ParquetFormat
Si el formato se establece en ParquetFormat, no es necesario especificar las propiedades de la sección formato dentro de la sección typeProperties. Ejemplo:

    "format":
    {
        "type": "ParquetFormat"
    }

> [AZURE.IMPORTANT] Si no va a copiar archivos parqués **como-es** entre local y la nube almacena datos, debe instalar el 8 de JRE (Java Runtime entorno) en el equipo de puerta de enlace. Una puerta de enlace de 64 bits requiere JRE de 64 bits y 32 bits gateway JRE de 32 bits. Puede encontrar las dos versiones de [aquí](http://go.microsoft.com/fwlink/?LinkId=808605). Seleccione el adecuado.

Tenga en cuenta los siguientes puntos:

-   Datos complejos tipos no son compatibles (mapa, lista)
-   Archivo de parqués tiene las siguientes opciones relacionadas con la compresión: ninguno, SNAPPY, GZIP y LZO. Generador de datos admite la lectura de datos desde archivo ORC en cualquiera de estos formatos comprimidos. Usa el códec de compresión de los metadatos para leer los datos. Sin embargo, al escribir en un archivo parqués, el generador de datos elige SNAPPY, que es el valor predeterminado para el formato de parqués. En este momento no hay ninguna opción para invalidar este comportamiento. 
