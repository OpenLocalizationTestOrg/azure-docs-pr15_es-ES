<properties 
    pageTitle="Cómo codificar un activo mediante Media Encoder estándar | Microsoft Azure" 
    description="Obtenga información sobre cómo usar el estándar de Media Encoder para codificar contenido multimedia en Media Services. Ejemplos de código utilizan la API de REST." 
    services="media-services" 
    documentationCenter="" 
    authors="Juliako" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/19/2016"
    ms.author="juliako"/>


#<a name="how-to-encode-an-asset-using-media-encoder-standard"></a>Cómo codificar un activo mediante Media Encoder estándar


> [AZURE.SELECTOR]
- [.NET](media-services-dotnet-encode-with-media-encoder-standard.md)
- [REST](media-services-rest-encode-asset.md)
- [Portal](media-services-portal-encode.md)

##<a name="overview"></a>Información general
Para poder ofrecer vídeo digital a través de internet debe comprimir los archivos multimedia. Archivos de vídeo digital son muy grandes y pueden ser demasiado grandes para ofrecer a través de internet o para dispositivos de los clientes mostrar correctamente. Codificación es el proceso de compresión de vídeo y audio para que sus clientes puedan ver su media.

Codificación de trabajos es una de las operaciones de transformación más comunes de Media Services. Crear tareas de codificación para convertir archivos multimedia de una codificación a otra. Cuando se codifica, puede usar el codificador de Media Services integrado (Media Encoder estándar). También puede utilizar un codificador de un socio Media Services; codificadores de terceros están disponibles a través de Azure Marketplace. Puede especificar los detalles de codificación tareas usando cadenas preestablecidas definidas para el codificador o mediante el uso de los archivos de configuración preestablecida. Para ver los tipos de valores preestablecidos que están disponibles, vea [Valores preestablecidos de tarea para Media Encoder estándar](http://msdn.microsoft.com/library/mt269960).

Cada tarea puede tener una o varias tareas según el tipo de procesamiento que desee realizar. A través de la API de REST, puede crear sus tareas relacionadas en una de estas dos maneras:

- Las tareas pueden ser definido en línea mediante la propiedad de navegación de tareas en entidades de trabajo, o
- a través del proceso por lotes de OData.


Se recomienda codificar siempre los archivos mezzanine en una velocidad adaptación MP4 establecer y, a continuación, convertir el conjunto en el formato deseado mediante el [Embalaje dinámico](media-services-dynamic-packaging-overview.md). Para aprovechar las ventajas de embalaje dinámico, primero debe obtener al menos una unidad de transmisión por secuencias a petición del extremo de transmisión por secuencias desde el que tiene previsto entrega el contenido. Para obtener más información, vea [Cómo escala Media Services](media-services-portal-manage-streaming-endpoints.md).

Si su activo de salida es almacenamiento cifrado, debe configurar Directiva de entrega de activos. Para obtener más información, vea [Directiva de entrega de activos de configuración](media-services-rest-configure-asset-delivery-policy.md).


>[AZURE.NOTE]Antes de empezar a hacer referencia a procesadores de medios, compruebe que tiene el medio correcto Id. de procesador Para obtener más información, vea [Obtener procesadores de medios](media-services-rest-get-media-processor.md).

##<a name="create-a-job-with-a-single-encoding-task"></a>Crear un trabajo con una sola tarea de codificación

>[AZURE.NOTE] Cuando se trabaja con la API de REST de servicios de medios, se aplican las consideraciones siguientes:
>
>Al acceder a las entidades de Media Services, debe establecer los campos de encabezado específico y los valores en las solicitudes HTTP. Para obtener más información, consulte [configuración de desarrollo de API de REST de servicios de multimedia](media-services-rest-how-to-use.md).

>Después de conectarse correctamente a https://media.windows.net, recibirá una redirección 301 especificando otro URI de servicios de medios. Debe realizar las siguientes llamadas para el nuevo URI tal como se describe en [Conectar con Media Services con la API de REST](media-services-rest-connect-programmatically.md).
>
>Cuando mediante JSON y especificando para usar la palabra clave **__metadata** en la solicitud (por ejemplo, que hace referencia a un objeto vinculado) debe establecer el encabezado **Accept** en [formato JSON detallado](http://www.odata.org/documentation/odata-version-3-0/json-verbose-format/): Aceptar: aplicación/json; odata = detallado.

En el ejemplo siguiente se muestra cómo crear y publicar un trabajo con una que tarea se establece para codificar un vídeo en una resolución específica y la calidad. Al codificar con Media Encoder estándar, puede usar valores predeterminados de configuración de tarea especificados [aquí](http://msdn.microsoft.com/library/mt269960).

Solicitar:

PUBLICACIÓN https://media.windows.net/API/Jobs tipo de contenido HTTP/1.1: aplicación/json; odata = Aceptar detallado: aplicación/json; odata = DataServiceVersion detallado: MaxDataServiceVersion 3.0: x-ms-versión 3.0: autorización 2.11: portador <token value> 
 x ms cliente solicitud id: 00000000-0000-0000-0000-000000000000 Host: media.windows.net

    
    {"Name" : "NewTestJob", "InputMediaAssets" : [{"__metadata" : {"uri" : "https://media.windows.net/api/Assets('nb%3Acid%3AUUID%3Aaab7f15b-3136-4ddf-9962-e9ecb28fb9d2')"}}],  "Tasks" : [{"Configuration" : "H264 Multiple Bitrate 720p", "MediaProcessorId" : "nb:mpid:UUID:ff4df607-d419-42f0-bc17-a481b1331e56",  "TaskBody" : "<?xml version=\"1.0\" encoding=\"utf-8\"?><taskBody><inputAsset>JobInputAsset(0)</inputAsset><outputAsset>JobOutputAsset(0)</outputAsset></taskBody>"}]}

Respuesta:
    
    HTTP/1.1 201 Created

    . . . 

###<a name="set-the-output-assets-name"></a>Establecer el nombre del activo de salida

En el ejemplo siguiente se muestra cómo se establece el atributo assetName:

    { "TaskBody" : "<?xml version=\"1.0\" encoding=\"utf-8\"?><taskBody><inputAsset>JobInputAsset(0)</inputAsset><outputAsset assetName=\"CustomOutputAssetName\">JobOutputAsset(0)</outputAsset></taskBody>"}

##<a name="considerations"></a>Consideraciones

- Propiedades de TaskBody deben usar literal XML para definir el número de entrada o salida activos que utilizará la tarea. El tema de la tarea contiene la definición del esquema XML para el XML.
- En la definición de TaskBody, valor cada interna <inputAsset> y <outputAsset> se debe establecer como JobInputAsset(value) o JobOutputAsset(value).
- Una tarea puede tener varios activos de salida. Un JobOutputAsset(x) sólo puede utilizarse una vez como un resultado de una tarea en una tarea.
- Puede especificar JobInputAsset o JobOutputAsset como un entrada activo de una tarea.
- Las tareas no deben formar un ciclo.
- El parámetro de valor que se pasa a JobInputAsset o JobOutputAsset representa el valor de índice de un activo. Los activos reales se definen en las propiedades de navegación InputMediaAssets y OutputMediaAssets en la definición de la entidad de trabajo. 
- Porque Media Services se basa en OData v3, se hace referencia a los activos individuales en InputMediaAssets y OutputMediaAssets colecciones de propiedades de navegación a través de un "__metadata: uri" valor de nombre de par.
- InputMediaAssets asigna a uno o varios de los activos que haya creado en Media Services. OutputMediaAssets creados por el sistema. No hacer referencia a un activo existente.
- Nombre OutputMediaAssets utilizando el atributo assetName. Si este atributo no está presente, el nombre de la OutputMediaAsset será independientemente del valor de texto interno de la <outputAsset> elemento es con un sufijo el valor de nombre del trabajo o el valor de Id. de trabajo (en el caso de que no está definida la propiedad nombre). Por ejemplo, si establece un valor para assetName para "Muestra", podría establecer la propiedad nombre de OutputMediaAsset a "Muestra". Sin embargo, si no ha definido un valor para assetName, pero se ha definido el nombre del trabajo a "NewJob", el nombre de OutputMediaAsset sería "_NewJob JobOutputAsset (valor)". 


##<a name="create-a-job-with-chained-tasks"></a>Crear un trabajo con tareas encadenadas

En muchos escenarios de aplicación, los desarrolladores desea crear una serie de procesamiento de tareas. En servicios de medios, puede crear una serie de tareas encadenadas. Cada tarea lleva a cabo los pasos de procesamiento diferentes y puede usar procesadores de medios diferente. Las tareas encadenadas pueden entregar un activo de una tarea a otra, realizar una secuencia lineal de tareas en el activo. Sin embargo, las tareas que realiza en un trabajo no tienen que estar en una secuencia. Cuando se crea una tarea encadenada, se crean los objetos **ITask** encadenados en un único objeto **IJob** .

>[AZURE.NOTE] Actualmente no hay un límite de 30 tareas por trabajo. Si necesita más de 30 tareas de la cadena, crear más de una tarea para que contenga las tareas.


    POST https://media.windows.net/api/Jobs HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.11
    Authorization: Bearer <token value>
    x-ms-client-request-id: 00000000-0000-0000-0000-000000000000

    {  
       "Name":"NewTestJob",
       "InputMediaAssets":[  
          {  
             "__metadata":{  
                "uri":"https://testrest.cloudapp.net/api/Assets('nb%3Acid%3AUUID%3A910ffdc1-2e25-4b17-8a42-61ffd4b8914c')"
             }
          }
       ],
       "Tasks":[  
          {  
             "Configuration":"H264 Adaptive Bitrate MP4 Set 720p",
             "MediaProcessorId":"nb:mpid:UUID:ff4df607-d419-42f0-bc17-a481b1331e56",
             "TaskBody":"<?xml version=\"1.0\" encoding=\"utf-8\"?><taskBody><inputAsset>JobInputAsset(0)</inputAsset><outputAsset>JobOutputAsset(0)</outputAsset></taskBody>"
          },
          {  
             "Configuration":"H264 Smooth Streaming 720p",
             "MediaProcessorId":"nb:mpid:UUID:ff4df607-d419-42f0-bc17-a481b1331e56",
             "TaskBody":"<?xml version=\"1.0\" encoding=\"utf-16\"?><taskBody><inputAsset>JobOutputAsset(0)</inputAsset><outputAsset>JobOutputAsset(1)</outputAsset></taskBody>"
          }
       ]
    }


###<a name="considerations"></a>Consideraciones

Para habilitar encadenar de tarea:

- Un trabajo debe tener al menos 2 tareas
- Debe haber al menos una tarea cuya entrada es el resultado de otra tarea en el trabajo.

## <a name="use-odata-batch-processing"></a>Usar el proceso por lotes de OData 

En el ejemplo siguiente se muestra cómo usar el proceso por lotes de OData para crear un trabajo y tareas. Para obtener información sobre el proceso por lotes, consulte el [Proceso por lotes de protocolo Open Data (OData)](http://www.odata.org/documentation/odata-version-3-0/batch-processing/).
 
    POST https://media.windows.net/api/$batch HTTP/1.1
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Content-Type: multipart/mixed; boundary=batch_a01a5ec4-ba0f-4536-84b5-66c5a5a6d34e
    Accept: multipart/mixed
    Accept-Charset: UTF-8
    Authorization: Bearer <token>
    x-ms-version: 2.11
    x-ms-client-request-id: 00000000-0000-0000-0000-000000000000
    Host: media.windows.net
    
    
    --batch_a01a5ec4-ba0f-4536-84b5-66c5a5a6d34e
    Content-Type: multipart/mixed; boundary=changeset_122fb0a4-cd80-4958-820f-346309967e4d
    
    --changeset_122fb0a4-cd80-4958-820f-346309967e4d
    Content-Type: application/http
    Content-Transfer-Encoding: binary
    
    POST https://media.windows.net/api/Jobs HTTP/1.1
    Content-ID: 1
    Content-Type: application/json
    Accept: application/json
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    Accept-Charset: UTF-8
    Authorization: Bearer <token>
    x-ms-version: 2.11
    x-ms-client-request-id: 00000000-0000-0000-0000-000000000000
    
    {"Name" : "NewTestJob", "InputMediaAssets@odata.bind":["https://media.windows.net/api/Assets('nb%3Acid%3AUUID%3A2a22445d-1500-80c6-4b34-f1e5190d33c6')"]}
    
    --changeset_122fb0a4-cd80-4958-820f-346309967e4d
    Content-Type: application/http
    Content-Transfer-Encoding: binary
    
    POST https://media.windows.net/api/$1/Tasks HTTP/1.1
    Content-ID: 2
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    Accept-Charset: UTF-8
    Authorization: Bearer <token>
    x-ms-version: 2.11
    x-ms-client-request-id: 00000000-0000-0000-0000-000000000000
    
    {  
       "Configuration":"H264 Adaptive Bitrate MP4 Set 720p",
       "MediaProcessorId":"nb:mpid:UUID:ff4df607-d419-42f0-bc17-a481b1331e56",
       "TaskBody":"<?xml version=\"1.0\" encoding=\"utf-8\"?><taskBody><inputAsset>JobInputAsset(0)</inputAsset><outputAsset assetName=\"Custom output name\">JobOutputAsset(0)</outputAsset></taskBody>"
    }

    --changeset_122fb0a4-cd80-4958-820f-346309967e4d--
    --batch_a01a5ec4-ba0f-4536-84b5-66c5a5a6d34e--
 


## <a name="create-a-job-using-a-jobtemplate"></a>Crear un trabajo mediante una plantilla de trabajo


Al procesar múltiples activos con un conjunto común de tareas, JobTemplates son útiles para especificar tareas preestablecidos, orden de las tareas y así sucesivamente.

En el ejemplo siguiente se muestra cómo crear una plantilla de trabajo con una alineada TaskTemplate definido. La TaskTemplate utiliza el estándar de codificador multimedia como el MediaProcessor para codificar el archivo activo. Sin embargo, también se puede utilizar otros MediaProcessors. 


    POST https://media.windows.net/API/JobTemplates HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.11
    Authorization: Bearer <token value>
    Host: media.windows.net

    
    {"Name" : "NewJobTemplate25", "JobTemplateBody" : "<?xml version=\"1.0\" encoding=\"utf-8\"?><jobTemplate><taskBody taskTemplateId=\"nb:ttid:UUID:071370A3-E63E-4E81-A099-AD66BCAC3789\"><inputAsset>JobInputAsset(0)</inputAsset><outputAsset>JobOutputAsset(0)</outputAsset></taskBody></jobTemplate>", "TaskTemplates" : [{"Id" : "nb:ttid:UUID:071370A3-E63E-4E81-A099-AD66BCAC3789", "Configuration" : "H264 Smooth Streaming 720p", "MediaProcessorId" : "nb:mpid:UUID:ff4df607-d419-42f0-bc17-a481b1331e56", "Name" : "SampleTaskTemplate2", "NumberofInputAssets" : 1, "NumberofOutputAssets" : 1}] }
 

>[AZURE.NOTE]A diferencia de otras entidades de Media Services, debe definir un nuevo identificador GUID para cada TaskTemplate y colóquelo en el taskTemplateId y la propiedad Id en el cuerpo de la solicitud. El esquema de identificación de contenido debe seguir el esquema descrito en identificar Azure Media Services entidades. Además, no se pueden actualizar JobTemplates. En su lugar, debe crear una nueva con los cambios actualizados.
 

Si es correcto, se devuelve la respuesta siguiente:
    
    HTTP/1.1 201 Created
    
    . . .


En el ejemplo siguiente se muestra cómo crear un trabajo que hacen referencia a un identificador de plantilla de trabajo:

    POST https://media.windows.net/API/Jobs HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.11
    Authorization: Bearer <token value>
    Host: media.windows.net

    
    {"Name" : "NewTestJob", "InputMediaAssets" : [{"__metadata" : {"uri" : "https://media.windows.net/api/Assets('nb%3Acid%3AUUID%3A3f1fe4a2-68f5-4190-9557-cd45beccef92')"}}], "TemplateId" : "nb:jtid:UUID:15e6e5e6-ac85-084e-9dc2-db3645fbf0aa"}
     

Si es correcto, se devuelve la respuesta siguiente:
    
    HTTP/1.1 201 Created
    
    . . . 



##<a name="media-services-learning-paths"></a>Rutas de aprendizaje de Media Services

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Proporcionar comentarios

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


##<a name="next-steps"></a>Pasos siguientes
Ahora que sabe cómo crear una tarea para codificar una assset, vaya al tema [Cómo a comprobar trabajo progreso con Media Services](media-services-rest-check-job-progress.md) .


##<a name="see-also"></a>Vea también

[Obtener procesadores multimedia](media-services-rest-get-media-processor.md)
