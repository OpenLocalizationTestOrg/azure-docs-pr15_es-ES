<properties
pageTitle="Usar el conector de Office 365 Video en las aplicaciones de lógica | Microsoft Azure"
description="Empezar a usar el conector de Office 365 Video en las aplicaciones de lógica de servicio de aplicación de Microsoft Azure"
services=""    
documentationCenter=""     
authors="msftman"    
manager="erikre"    
editor=""
tags="connectors"/>

<tags
ms.service="multiple"
ms.devlang="na"
ms.topic="article"
ms.tgt_pltfrm="na"
ms.workload="na"
ms.date="05/18/2016"
ms.author="deonhe"/>

# <a name="get-started-with-the-office365-video-connector"></a>Empezar a trabajar con el conector de vídeo de Office 365
Conectarse a Office 365 vídeo para obtener información acerca de Office 365 vídeo, obtener una lista de vídeos y mucho más. Puede utilizar el conector de vídeo de Office 365 desde:

- Aplicaciones de lógica 

>[AZURE.NOTE] Esta versión de este artículo se aplica a la versión del esquema de 2015-08-01-vista previa de aplicaciones de lógica. Este conector no es compatible con las versiones anteriores de esquema.

Con Office 365 Video, puede:

- Crear el flujo de negocio basándose en los datos que recibe de Office 365 Video. 
- Usar acciones que comprobar el estado de portal de vídeo, obtener una lista de todos los vídeo en un canal y mucho más. Estas acciones obtienen una respuesta y, a continuación, hacer que el resultado esté disponible para otras acciones. Por ejemplo, puede utilizar el conector de búsqueda de Bing para buscar los vídeos de Office 365 y, a continuación, utilice el conector de vídeo de Office 365 para obtener información sobre esa vídeo. Si el vídeo cumple los requisitos, puede publicar este vídeo en Facebook. 

Para agregar una operación en las aplicaciones de lógica, consulte [crear una aplicación de lógica](../app-service-logic/app-service-logic-create-a-logic-app.md).

## <a name="triggers-and-actions"></a>Desencadenadores y acciones

El conector de vídeo de Office 365 tiene las siguientes acciones disponibles. No hay ningún desencadenador.

| Desencadenadores | Acciones|
| --- | --- |
| Ninguno | <ul><li>Comprobaciones de estado de portal de vídeo</li><li>Obtener todos los canales visibles</li><li>Obtener la dirección url de la reproducción del manifiesto Azure Media Services para un vídeo</li><li>Obtener el token portador para obtener acceso a descifrar el vídeo</li><li>Obtiene información sobre un determinado office365 vídeo</li><li>Enumera todos los vídeos de Office 365 presentes en un canal</li></ul>

Todos los conectores admiten datos en formatos XML y JSON. 

## <a name="create-a-connection-to-office365-video-connector"></a>Crear una conexión al conector de vídeo de Office 365
Cuando agrega este conector a sus aplicaciones de lógica, debe iniciar sesión en su cuenta de Office 365 Video y permitir que las aplicaciones de lógica para conectarse a su cuenta.

>[AZURE.INCLUDE [Steps to create a connection to Office 365 Video](../../includes/connectors-create-api-office365video.md)]

Después de crear la conexión, escriba las propiedades de vídeo de Office 365, como el nombre del inquilino o identificador del canal La **referencia de la API de REST** en este tema se describen las propiedades.

>[AZURE.TIP] Puede utilizar esta misma conexión de Office 365 Video en otras aplicaciones de lógica.

## <a name="swagger-rest-api-reference"></a>Referencia de la API de REST de swagger
Se aplica a la versión: 1.0.

### <a name="checks-video-portal-status"></a>Comprobaciones de estado de portal de vídeo 
Comprueba el estado de portal de vídeo para ver si se habilitan servicios de vídeo.  
```GET: /{tenant}/IsEnabled``` 

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|inquilino|cadena|Sí|ruta de acceso|Ninguno|El nombre del inquilino para el directorio del usuario es parte de|


#### <a name="response"></a>Respuesta

|Nombre|Descripción|
|---|---|
|200|La operación es correcto|
|400|BadRequest|
|401|No autorizado|
|404|No se encontró|
|500|Error interno del servidor|
|predeterminado|Error en la operación.|



### <a name="get-all-viewable-channels"></a>Obtener todos los canales visibles 
Obtiene todos los canales que el usuario tiene la visualización de acceso a.  
```GET: /{tenant}/Channels``` 

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|inquilino|cadena|Sí|ruta de acceso|Ninguno|El nombre del inquilino para el directorio del usuario es parte de|


#### <a name="response"></a>Respuesta

|Nombre|Descripción|
|---|---|
|200|La operación es correcto|
|400|BadRequest|
|401|No autorizado|
|404|No se encontró|
|500|Error interno del servidor|
|predeterminado|Error en la operación.|




### <a name="lists-all-the-office365-videos-present-in-a-channel"></a>Enumera todos los vídeos de Office 365 presentes en un canal 
Enumera todos los vídeos de Office 365 presentes en un canal.  
```GET: /{tenant}/Channels/{channelId}/Videos``` 

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|inquilino|cadena|Sí|ruta de acceso|Ninguno|El nombre del inquilino para el directorio del usuario es parte de|
|///ChannelID|cadena|Sí|ruta de acceso|Ninguno|El identificador de canal desde el que deben recuperarse vídeos|


#### <a name="response"></a>Respuesta

|Nombre|Descripción|
|---|---|
|200|La operación es correcto|
|400|BadRequest|
|401|No autorizado|
|404|No se encontró|
|500|Error interno del servidor|
|predeterminado|Error en la operación.|




### <a name="gets-information-about-a-particular-office365-video"></a>Obtiene información sobre un determinado office365 vídeo 
Obtiene información sobre un determinado office365 vídeo.  
```GET: /{tenant}/Channels/{channelId}/Videos/{videoId}``` 

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|inquilino|cadena|Sí|ruta de acceso|Ninguno|El nombre del inquilino para el directorio del usuario es parte de|
|///ChannelID|cadena|Sí|ruta de acceso|Ninguno|El identificador de canal|
|videoId|cadena|Sí|ruta de acceso|Ninguno|El identificador de vídeo|


#### <a name="response"></a>Respuesta

|Nombre|Descripción|
|---|---|
|200|La operación es correcto|
|400|BadRequest|
|401|No autorizado|
|404|No se encontró|
|500|Error interno del servidor|
|predeterminado|Error en la operación.|




### <a name="get-playback-url-of-the-azure-media-services-manifest-for-a-video"></a>Obtener la dirección url de la reproducción del manifiesto Azure Media Services para un vídeo 
Obtener la dirección url de la reproducción del manifiesto Azure Media Services para ver un vídeo.  
```GET: /{tenant}/Channels/{channelId}/Videos/{videoId}/playbackurl``` 

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|inquilino|cadena|Sí|ruta de acceso|Ninguno|El nombre del inquilino para el directorio del usuario es parte de|
|///ChannelID|cadena|Sí|ruta de acceso|Ninguno|El identificador de canal|
|videoId|cadena|Sí|ruta de acceso|Ninguno|El identificador de vídeo|
|streamingFormatType|cadena|Sí|consulta|Ninguno|Tipo de formato streaming. 1 - transmisión por secuencias suave o MPEG-guión. 0 - HLS transmisión|


#### <a name="response"></a>Respuesta

|Nombre|Descripción|
|---|---|
|200|La operación es correcto|
|400|BadRequest|
|401|No autorizado|
|404|No se encontró|
|500|Error interno del servidor|
|predeterminado|Error en la operación.|




### <a name="get-the-bearer-token-to-get-access-to-decrypt-the-video"></a>Obtener el token portador para obtener acceso a descifrar el vídeo 
Obtener el token portador para obtener acceso a descifrar el vídeo.  
```GET: /{tenant}/Channels/{channelId}/Videos/{videoId}/token```

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|inquilino|cadena|Sí|ruta de acceso|Ninguno|El nombre del inquilino para el directorio del usuario es parte de|
|///ChannelID|cadena|Sí|ruta de acceso|Ninguno|El identificador de canal|
|videoId|cadena|Sí|ruta de acceso|Ninguno|El identificador de vídeo|


#### <a name="response"></a>Respuesta

|Nombre|Descripción|
|---|---|
|200|La operación es correcto|
|400|BadRequest|
|401|No autorizado|
|404|No se encontró|
|500|Error interno del servidor|
|predeterminado|Error en la operación.|


## <a name="object-definitions"></a>Definiciones de objeto

#### <a name="channel-channel-class"></a>Canal: Clase de canal

| Nombre | Tipo de datos | Obligatorio|
|---|---|---|
|Id.|cadena|No|
|Título|cadena|No|
|Descripción|cadena|No|


#### <a name="video"></a>Vídeo 

| Nombre | Tipo de datos |Obligatorio|
|---|---|---|
|Id.|cadena|No|
|Título|cadena|No|
|Descripción|cadena|No|
|CreationDate|cadena|No|
|Propietario|cadena|No|
|ThumbnailUrl|cadena|No|
|VideoUrl|cadena|No|
|VideoDuration|entero|No|
|VideoProcessingStatus|entero|No|
|ViewCount|entero|No|


## <a name="next-steps"></a>Pasos siguientes
[Crear una aplicación de lógica](../app-service-logic/app-service-logic-create-a-logic-app.md).
