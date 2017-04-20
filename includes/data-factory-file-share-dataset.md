## <a name="fileshare-dataset-type-properties"></a>Propiedades de tipo de recurso compartido de archivos del conjunto de datos

Para obtener una lista completa de secciones y propiedades disponibles para definir conjuntos de datos, vea el artículo [crear conjuntos de datos](../articles/data-factory/data-factory-create-datasets.md) . Secciones como estructura, la disponibilidad y la directiva de un conjunto de datos JSON son similares para todos los tipos de conjunto de datos. 

La sección **typeProperties** es diferente para cada tipo de conjunto de datos. Proporciona información específica en el tipo de conjunto de datos. La sección typeProperties para un conjunto de datos del tipo de **recurso compartido de archivos** del conjunto de datos tiene las siguientes propiedades:

(Propiedad) | Descripción | Obligatorio
-------- | ----------- | --------
folderPath | Ruta de acceso de Sub a la carpeta. Usar el carácter de escape ' \ ' caracteres especiales en la cadena. Para obtener ejemplos, vea [ejemplo vinculado definiciones de servicio y conjunto de datos](#sample-linked-service-and-dataset-definitions) .<br/><br/>Puede combinar esta propiedad con **partitionBy** tener trayectorias de la carpeta en el sector fechas y horas de inicio o finalización. | Sí
nombre de archivo | Especifique el nombre del archivo en la **folderPath** si desea que la tabla para hacer referencia a un archivo en la carpeta específico. Si no especifica ningún valor para esta propiedad, todos los archivos en la carpeta de destino en la tabla.<br/><br/>Cuando no se especifica el nombre de archivo para un conjunto de datos de salida, el nombre del archivo generado será en el siguiente este formato: <br/><br/>Datos. <Guid>.txt (ejemplo: Data.0a405f8a 93ff 4c6f b3be f69616f1df7a.txt | No
partitionedBy | partitionedBy puede utilizarse para especificar un folderPath dinámico, nombre de archivo de datos de la serie de tiempo. Por ejemplo, folderPath parametrizada, para cada hora de datos. | No
Formato | Se admiten los siguientes tipos de formato: **TextFormat**, **AvroFormat**, **JsonFormat**y **OrcFormat**. Establezca la propiedad **tipo** en formato en uno de estos valores. Vea las secciones [TextFormat que especifica](#specifying-textformat), [Que especifica AvroFormat](#specifying-avroformat), [Especificar JsonFormat](#specifying-jsonformat)y [OrcFormat que especifica](#specifying-orcformat) para obtener información detallada. Si desea copiar archivos como-está entre stores basados en archivos (copia binario), puede omitir la sección formato de ambas definiciones de conjunto de datos de entrada y salida. | No
fileFilter | Especificar un filtro que se utilizará para seleccionar un subconjunto de archivos en la folderPath en lugar de todos los archivos.<br/><br/>Valores permitidos son: `*` (varios caracteres) y `?` (carácter).<br/><br/>Ejemplos 1:`"fileFilter": "*.log"`<br/>Ejemplo 2:`"fileFilter": 2014-1-?.txt"`<br/><br/> fileFilter es aplicable para un conjunto de datos de recurso compartido de archivos de entrada. Esta propiedad no es compatible con HDFS.  | No
| compresión | Especifique el tipo y el nivel de compresión de los datos. Tipos compatibles son: **GZip**, **Deflate**y **BZip2** y niveles compatibles son: **óptima** y **más rápida**. Actualmente, no se admiten los valores de compresión de datos en **AvroFormat** o **OrcFormat**. Para obtener más información, vea la sección de [compatibilidad con la compresión](#compression-support) .  | No |
| useBinaryTransfer | Especifique si usar el modo de transferencia binaria. True para modo binario y ASCII false. Valor predeterminado: True. Esta propiedad sólo puede utilizarse al tipo de servicio vinculadas asociado es de tipo: FtpServer. | No | 
 

> [AZURE.NOTE] nombre de archivo y fileFilter no pueden usarse simultáneamente.

### <a name="using-partionedby-property"></a>Uso de partionedBy (propiedad)

Como se mencionó en la sección anterior, puede especificar un folderPath dinámico, nombre de archivo de datos de la serie de tiempo con partitionedBy. Puede hacerlo con las macros de datos fábrica y la variable del sistema SliceStart, SliceEnd que indican el período de tiempo lógico para un sector de datos determinado. 

Para obtener información sobre los conjuntos de datos de serie de tiempo, la programación y sectores, vea [Crear conjuntos de datos](../articles/data-factory/data-factory-create-datasets.md), [programación y ejecución](../articles/data-factory/data-factory-scheduling-and-execution.md)y [Creación de canalizaciones](../articles/data-factory/data-factory-create-pipelines.md) artículos. 

#### <a name="sample-1"></a>Ejemplo 1:

    "folderPath": "wikidatagateway/wikisampledataout/{Slice}",
    "partitionedBy": 
    [
        { "name": "Slice", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyyMMddHH" } },
    ],

En este ejemplo {sector} se reemplaza con el valor de la variable de sistema de datos fábrica SliceStart en el formato (YYYYMMDDHH) especificado. Hace referencia a la SliceStart hora de inicio de la segmentación de datos. La folderPath es diferente para cada sector. Ejemplo: wikisampledataout/wikidatagateway/2014100103 o wikisampledataout/wikidatagateway/2014100104.

#### <a name="sample-2"></a>Ejemplo 2:

    "folderPath": "wikidatagateway/wikisampledataout/{Year}/{Month}/{Day}",
    "fileName": "{Hour}.csv",
    "partitionedBy": 
     [
        { "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
        { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } }, 
        { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } }, 
        { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "hh" } } 
    ],

En este ejemplo, el año, mes, día y hora de SliceStart se extraen en variables diferentes que se usan las propiedades folderPath y nombre de archivo.
