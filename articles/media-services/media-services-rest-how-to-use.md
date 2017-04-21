<properties 
    pageTitle="Información general de la API de REST de servicios multimedia | Microsoft Azure" 
    description="Información general de la API de REST de servicios de medios" 
    services="media-services" 
    documentationCenter="" 
    authors="Juliako" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="na" 
    ms.devlang="dotnet" 
    ms.topic="article" 
    ms.date="10/12/2016"
    ms.author="juliako"/>


# <a name="media-services-rest-api-overview"></a>Información general de la API de REST de servicios de medios 

[AZURE.INCLUDE [media-services-selector-setup](../../includes/media-services-selector-setup.md)]

Microsoft Azure Media Services es un servicio que acepta las solicitudes HTTP basado en OData y puede responder en detallado JSON o atom + pub. Como Media Services se ajusta a las directrices de diseño de Azure, hay un conjunto de encabezados HTTP requiere que cada cliente debe utilizar al conectarse a servicios de medios, así como un conjunto de encabezados opcionales que se pueden usar. Las secciones siguientes describen los encabezados y verbos HTTP puede usar cuándo crear solicitudes y recibir respuestas de Media Services.

##<a name="considerations"></a>Consideraciones 

Las consideraciones siguientes se aplican cuando se usa el resto.

- Al consultar entidades, hay un límite de 1000 entidades devuelto a la vez porque público v2 resto los límites de los resultados de la consulta a los resultados de 1000. Debe usar **Saltar** y **tomar** (. NET) o **superior** (REST) como se describe en [este ejemplo .NET](media-services-dotnet-manage-entities.md#enumerating-through-large-collections-of-entities) y [este ejemplo API de REST](media-services-rest-manage-entities.md#enumerating-through-large-collections-of-entities). 

- Cuando mediante JSON y especificando para usar la palabra clave **__metadata** en la solicitud (por ejemplo, que hace referencia a un objeto vinculado) debe establecer el encabezado **Accept** en [formato JSON detallado](http://www.odata.org/documentation/odata-version-3-0/json-verbose-format/) (consulte el ejemplo siguiente). OData no comprende la propiedad **__metadata** en la convocatoria, a menos que establezca para mostrar información detallada.  

        POST https://media.windows.net/API/Jobs HTTP/1.1
        Content-Type: application/json;odata=verbose
        Accept: application/json;odata=verbose
        DataServiceVersion: 3.0
        MaxDataServiceVersion: 3.0
        x-ms-version: 2.11
        Authorization: Bearer <token> 
        Host: media.windows.net
        
        {
            "Name" : "NewTestJob", 
            "InputMediaAssets" : 
                [{"__metadata" : {"uri" : "https://media.windows.net/api/Assets('nb%3Acid%3AUUID%3Aba5356eb-30ff-4dc6-9e5a-41e4223540e7')"}}]
        . . . 
        

## <a name="standard-http-request-headers-supported-by-media-services"></a>Encabezados de solicitud HTTP estándar compatibles con servicios de medios

Para cada llamada que realice en Media Services, hay un conjunto de encabezados necesarios que debe incluir en la convocatoria y también un conjunto de encabezados opcionales que desea incluir. La siguiente tabla enumeran los encabezados necesarios:


Encabezado|Tipo|Valor
---|---|---
Autorización|Portador|Portador es el mecanismo de autorización aceptada solo. El valor debe incluir también el token de acceso de ACS.
versión de ms x|Decimal|2.11
DataServiceVersion|Decimal|3.0
MaxDataServiceVersion|Decimal|3.0



>[AZURE.NOTE] Como Media Services utiliza OData exponer su repositorio de metadatos activo subyacente a través de las API de REST, los encabezados de DataServiceVersion y MaxDataServiceVersion deben incluirse en cualquier solicitud; Sin embargo, si no lo están, a continuación, actualmente Media Services supone que el valor de DataServiceVersion de uso es 3.0.

A continuación se muestra un conjunto de encabezados opcionales:

Encabezado|Tipo|Valor
---|---|---
Fecha|Fecha de RFC 1123|Marca de tiempo de la solicitud
Aceptar|Tipo de contenido|El tipo de contenido solicitado para la respuesta como la siguiente:<p> -aplicación/json; odata = detallado<p> -aplicación/atom + xml<p> Las respuestas pueden tener un tipo de contenido diferentes, como una recopilación de blobs de Windows, donde una respuesta correcta contendrá la secuencia blob como la carga.
Codificación de Aceptar|Gzip, disminuir|GZIP y DEFLATE codificación cuando corresponda. Nota: Para recursos de gran tamaño, Media Services puede omitir este encabezado y devolver datos no comprimidas.
Idioma Aceptar|"en", "es" y así sucesivamente.|Especifica el idioma preferido para la respuesta.
Juego de caracteres Aceptar|Tipo de conjunto de caracteres como "UTF-8"|El valor predeterminado es UTF-8.
Método de HTTP X|Método HTTP|Permite a los clientes o los firewalls que no admiten métodos HTTP como SUPERPONER o eliminar para usar estos métodos, túnel a través de una llamada de obtener.
Tipo de contenido|Tipo de contenido|Se solicita el tipo de contenido del cuerpo de la solicitud de SUPERPONER o publicación.
Id. de solicitud de cliente|Cadena|Un valor definido por el llamador que identifica la solicitud especificada. Si se especifica, este valor se incluirán en el mensaje de respuesta, como una manera de asignar la solicitud. <p><p>**Importante**<p>Los valores deben estar cerrados en 2096b (2 KB).

## <a name="standard-http-response-headers-supported-by-media-services"></a>Encabezados de respuesta HTTP estándar compatibles con servicios de medios

A continuación se muestra un conjunto de encabezados que puede devolverse según el recurso que se solicita y la acción que desea realizar.


Encabezado|Tipo|Valor
---|---|---
Id. de solicitud|Cadena|Un identificador único para la operación actual, generados por el servicio.
Id. de solicitud de cliente|Cadena|Un identificador especificado por el llamador en la convocatoria original, si está presente.
Fecha|Fecha de RFC 1123|La fecha en la que se ha procesado la solicitud.
Tipo de contenido|Varía|El tipo de contenido del cuerpo de la respuesta.
Codificación de contenido|Varía|Gzip o disminuir, según corresponda.


## <a name="standard-http-verbs-supported-by-media-services"></a>Verbos HTTP estándar compatibles con servicios de medios

A continuación se muestra una lista completa de los verbos HTTP que pueden utilizarse para realizar HTTP solicita:


Verbo|Descripción
---|---
Obtener|Devuelve el valor actual de un objeto.
Exponer|Crea un objeto basado en los datos proporcionados o envía un comando.
COLOCAR|Reemplaza un objeto o crea un objeto con nombre (si procede).
ELIMINAR|Elimina un objeto.
COMBINACIÓN DE CORRESPONDENCIA|Actualiza un objeto existente con los cambios de la propiedad con nombre.
CABEZA|Devuelve los metadatos de un objeto para una respuesta de obtener.

##<a name="limitation"></a>Limitación

Al consultar entidades, hay un límite de 1000 entidades devuelto a la vez porque público v2 resto los límites de los resultados de la consulta a los resultados de 1000. Debe usar **Saltar** y **tomar** (. NET) o **superior** (REST) como se describe en [este ejemplo .NET](media-services-dotnet-manage-entities.md#enumerating-through-large-collections-of-entities) y [este ejemplo API de REST](media-services-rest-manage-entities.md#enumerating-through-large-collections-of-entities). 


## <a name="discovering-media-services-model"></a>Descubrir modelo Media Services

Para hacer que las entidades de Media Services más reconocible, puede usarse la operación $metadata. Permite recuperar todos los tipos de entidad válido, propiedades de la entidad, las asociaciones, funciones, acciones y así sucesivamente. En el ejemplo siguiente se muestra cómo construir el URI: https://media.windows.net/API/$ metadatos.

Se debe agregar "? version=2.x api" al final de la URI si desea ver los metadatos en un explorador o no incluye el encabezado de versión de ms x en la convocatoria.



##<a name="media-services-learning-paths"></a>Rutas de aprendizaje de Media Services

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Proporcionar comentarios

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]





 
