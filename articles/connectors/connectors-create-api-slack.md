<properties
pageTitle=" Usar el conector de margen de demora en las aplicaciones de lógica | Microsoft Azure"
description="Empezar a usar el conector de margen de demora en las aplicaciones de la lógica de servicio de aplicación de Microsoft Azure"
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

# <a name="get-started-with-the-slack-connector"></a>Empezar a trabajar con el conector de margen de demora

Margen de demora es una herramienta de comunicación de grupo, que reúne todas las comunicaciones de grupo en una colocar, al instante que permiten búsquedas y disponible donde vaya.

>[AZURE.NOTE] Esta versión de este artículo se aplica a la versión del esquema de 2015-08-01-vista previa de aplicaciones de lógica.

El conector de margen de demora, puede:

* Usar para crear aplicaciones de lógica

Para agregar una operación en las aplicaciones de lógica, consulte [crear una aplicación de lógica](../app-service-logic/app-service-logic-create-a-logic-app.md).

## <a name="lets-talk-about-triggers-and-actions"></a>Hablemos de desencadenadores y acciones

El conector de margen de demora puede usarse como una acción; No hay ningún desencadenador. Todos los conectores admiten datos en formatos XML y JSON. 

 El conector de margen de demora tiene las siguientes acciones o desencadenadores disponibles:

### <a name="slack-actions"></a>Acciones de margen de demora
Puede realizar estas acciones:

|Acción|Descripción|
|--- | ---|
|PostMessage|Publicar un mensaje en un canal determinado.|
## <a name="create-a-connection-to-slack"></a>Crear una conexión con el margen de demora
Para usar el conector de margen de demora, primero crear una **conexión** y proporcionar los detalles de estas propiedades: 

|(Propiedad)| Obligatorio|Descripción|
| ---|---|---|
|Token|Sí|Proporcionar las credenciales de margen de demora|

Siga estos pasos para iniciar sesión en el margen de demora y completar la configuración de la **conexión** de margen de demora en la aplicación de lógica:

1. Seleccione la **Periodicidad**
2. Seleccione una **frecuencia** y especifique un **intervalo**
3. Seleccione **Agregar una acción**  
![Configurar el margen de demora][1]  
4. Escriba el margen de demora en el cuadro de búsqueda y espere a que la búsqueda devolver todas las entradas con el margen de demora en el nombre
5. Seleccione **margen de demora - mensaje de entrada**
6. Seleccione **iniciar sesión en una demora**:  
![Configurar el margen de demora][2]
7. Proporcionar sus credenciales para iniciar sesión en autorizar la aplicación de margen de demora    
![Configurar el margen de demora][3]  
8. Se le redirigirá a registro de su organización en la página. **Autorizar** Margen de demora para interactuar con la aplicación de lógica:      
![Configurar el margen de demora][5] 
9. Una vez completada la autorización, se le redirigirá a su aplicación lógica para completar mediante la configuración de la sección de **demora: obtener todos los mensajes** . Agregar otros desencadenadores y las acciones que necesita.  
![Configurar el margen de demora][6]
10. Guarde su trabajo, seleccione **Guardar** en la barra de menús superior.


>[AZURE.TIP] Puede usar esta conexión en otras aplicaciones de lógica.

## <a name="slack-rest-api-reference"></a>Referencia de la API de REST de margen de demora
#### <a name="this-documentation-is-for-version-10"></a>Esta documentación es de versión: 1.0


### <a name="post-a-message-to-a-specified-channel"></a>Publicar un mensaje en un canal determinado.
**```POST: /chat.postMessage```** 



| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|canal|cadena|Sí|consulta|Ninguno|Canal, grupo privado o canal de mensajería instantánea para enviar el mensaje. Puede ser un nombre (ex: #general) o un identificador codificado.|
|texto|cadena|Sí|consulta|Ninguno|Texto del mensaje para enviar. Para opciones de formato, vea https://api.slack.com/docs/formatting.|
|nombre de usuario|cadena|No|consulta|Ninguno|Nombre de la componente|
|as_user|valor booleano|No|consulta|Ninguno|Pasar true para publicar el mensaje como el usuario autenticado, en lugar de como un componente|
|analizar|cadena|No|consulta|Ninguno|Cambiar cómo se tratan los mensajes. Para obtener información detallada, consulte https://api.slack.com/docs/formatting.|
|link_names|entero|No|consulta|Ninguno|Buscar y vincular los nombres de los canales y nombres de usuario.|
|unfurl_links|valor booleano|No|consulta|Ninguno|Pasar true habilitar unfurling principalmente contenido de basado en texto.|
|unfurl_media|valor booleano|No|consulta|Ninguno|Pasar el valor false para deshabilitar unfurling de contenido multimedia.|
|icon_url|cadena|No|consulta|Ninguno|Dirección URL de una imagen para usar como icono para este mensaje|
|icon_emoji|cadena|No|consulta|Ninguno|Emojis usar como icono para este mensaje|


### <a name="here-are-the-possible-responses"></a>Estas son las respuestas posibles:

|Nombre|Descripción|
|---|---|
|200|Vale|
|400|Solicitud incorrecta|
|408|Tiempo de espera de solicitud|
|429|Demasiadas solicitudes|
|500|Error de servidor interno. Error desconocido|
|503|Margen de demora servicio no disponible|
|504|Tiempo de espera de puerta de enlace|
|predeterminado|Error en la operación.|
------



## <a name="object-definitions"></a>Definiciones de objeto: 

 **Mensaje**: mensaje de Yammer

Propiedades necesarias para el mensaje:


Ninguna de las propiedades son necesaria. 


**Todas las propiedades**: 


| Nombre | Tipo de datos |
|---|---|
|Id.|entero|
|content_excerpt|cadena|
|sender_id|entero|
|replied_to_id|entero|
|created_at|cadena|
|network_id|entero|
|message_type|cadena|
|sender_type|cadena|
|dirección URL|cadena|
|web_url|cadena|
|group_id|entero|
|cuerpo|no definido|
|thread_id|entero|
|direct_message|valor booleano|
|client_type|cadena|
|client_url|cadena|
|idioma|cadena|
|notified_user_ids|matriz|
|declaración de privacidad|cadena|
|liked_by|no definido|
|system_message|valor booleano|



 **PostOperationRequest**: representa una solicitud de publicación de conector de Yammer publicar yammer

Propiedades necesarias para PostOperationRequest:

cuerpo

**Todas las propiedades**: 


| Nombre | Tipo de datos |
|---|---|
|cuerpo|cadena|
|group_id|entero|
|replied_to_id|entero|
|direct_to_id|entero|
|difusión|valor booleano|
|tema1|cadena|
|tema2|cadena|
|topic3|cadena|
|topic4|cadena|
|topic5|cadena|
|topic6|cadena|
|topic7|cadena|
|topic8|cadena|
|topic9|cadena|
|topic10|cadena|
|topic11|cadena|
|topic12|cadena|
|topic13|cadena|
|topic14|cadena|
|topic15|cadena|
|topic16|cadena|
|topic17|cadena|
|topic18|cadena|
|topic19|cadena|
|topic20|cadena|



 **MessageList**: lista de mensajes

Propiedades necesarias para MessageList:


Ninguna de las propiedades son necesaria. 


**Todas las propiedades**: 


| Nombre | Tipo de datos |
|---|---|
|mensajes|matriz|



 **MessageBody**: cuerpo del mensaje

Propiedades necesarias para MessageBody:


Ninguna de las propiedades son necesaria. 


**Todas las propiedades**: 


| Nombre | Tipo de datos |
|---|---|
|analizar|cadena|
|sin formato|cadena|
|enriquecidos|cadena|



 **LikedBy**: le ha gustado por

Propiedades necesarias para LikedBy:


Ninguna de las propiedades son necesaria. 


**Todas las propiedades**: 


| Nombre | Tipo de datos |
|---|---|
|recuento|entero|
|nombres|matriz|



 **YammmerEntity**: le ha gustado por

Propiedades necesarias para YammmerEntity:


Ninguna de las propiedades son necesaria. 


**Todas las propiedades**: 


| Nombre | Tipo de datos |
|---|---|
|tipo|cadena|
|Id.|entero|
|FULL_NAME|cadena|


## <a name="next-steps"></a>Pasos siguientes
[Crear una aplicación de lógica](../app-service-logic/app-service-logic-create-a-logic-app.md)
## <a name="object-definitions"></a>Definiciones de objeto: 

 **WebResultModel**: resultados de la búsqueda de Bing web

Propiedades necesarias para WebResultModel:


Ninguna de las propiedades son necesaria. 


**Todas las propiedades**: 


| Nombre | Tipo de datos |
|---|---|
|Título|cadena|
|Descripción|cadena|
|DisplayUrl|cadena|
|Id.|cadena|
|FullUrl|cadena|



 **VideoResultModel**: resultados de la búsqueda de vídeo de Bing

Propiedades necesarias para VideoResultModel:


Ninguna de las propiedades son necesaria. 


**Todas las propiedades**: 


| Nombre | Tipo de datos |
|---|---|
|Título|cadena|
|DisplayUrl|cadena|
|Id.|cadena|
|MediaUrl|cadena|
|Tiempo de ejecución|entero|
|Vista en miniatura|no definido|



 **ThumbnailModel**: propiedades de vista en miniatura del elemento multimedia

Propiedades necesarias para ThumbnailModel:


Ninguna de las propiedades son necesaria. 


**Todas las propiedades**: 


| Nombre | Tipo de datos |
|---|---|
|MediaUrl|cadena|
|ContentType|cadena|
|Ancho|entero|
|Alto|entero|
|Tamaño de archivo|entero|



 **ImageResultModel**: resultados de búsqueda de imágenes de Bing

Propiedades necesarias para ImageResultModel:


Ninguna de las propiedades son necesaria. 


**Todas las propiedades**: 


| Nombre | Tipo de datos |
|---|---|
|Título|cadena|
|DisplayUrl|cadena|
|Id.|cadena|
|MediaUrl|cadena|
|SourceUrl|cadena|
|Vista en miniatura|no definido|



 **NewsResultModel**: resultados de búsqueda de noticias de Bing

Propiedades necesarias para NewsResultModel:


Ninguna de las propiedades son necesaria. 


**Todas las propiedades**: 


| Nombre | Tipo de datos |
|---|---|
|Título|cadena|
|Descripción|cadena|
|DisplayUrl|cadena|
|Id.|cadena|
|Origen|cadena|
|Fecha|cadena|



 **SpellResultModel**: resultados de sugerencias de ortografía de Bing

Propiedades necesarias para SpellResultModel:


Ninguna de las propiedades son necesaria. 


**Todas las propiedades**: 


| Nombre | Tipo de datos |
|---|---|
|Id.|cadena|
|Valor|cadena|



 **RelatedSearchResultModel**: Bing relacionadas con los resultados de búsqueda

Propiedades necesarias para RelatedSearchResultModel:


Ninguna de las propiedades son necesaria. 


**Todas las propiedades**: 


| Nombre | Tipo de datos |
|---|---|
|Título|cadena|
|Id.|cadena|
|BingUrl|cadena|



 **CompositeSearchResultModel**: resultados de la búsqueda compuesto de Bing

Propiedades necesarias para CompositeSearchResultModel:


Ninguna de las propiedades son necesaria. 


**Todas las propiedades**: 


| Nombre | Tipo de datos |
|---|---|
|WebResultsTotal|entero|
|ImageResultsTotal|entero|
|VideoResultsTotal|entero|
|NewsResultsTotal|entero|
|SpellSuggestionsTotal|entero|
|WebResults|matriz|
|ImageResults|matriz|
|VideoResults|matriz|
|NewsResults|matriz|
|SpellSuggestionResults|matriz|
|RelatedSearchResults|matriz|


## <a name="object-definitions"></a>Definiciones de objeto: 

 **PostOperationResponse**: representa la respuesta de la operación de publicación de conector de margen de demora para registrarse en el margen de demora

Propiedades necesarias para PostOperationResponse:


Ninguna de las propiedades son necesaria. 


**Todas las propiedades**: 


| Nombre | Tipo de datos |
|---|---|
|Vale|valor booleano|
|canal|cadena|
|TS|cadena|
|Mensaje|no definido|
|Error|cadena|



 **MessageItem**: un mensaje de canal.

Propiedades necesarias para MessageItem:


Ninguna de las propiedades son necesaria. 


**Todas las propiedades**: 


| Nombre | Tipo de datos |
|---|---|
|texto|cadena|
|Id.|cadena|
|usuario|cadena|
|creado|entero|
|eliminar is_user|valor booleano|


## <a name="next-steps"></a>Pasos siguientes
[Crear una aplicación de lógica](../app-service-logic/app-service-logic-create-a-logic-app.md)

[1]: ./media/connectors-create-api-slack/connectionconfig1.png
[2]: ./media/connectors-create-api-slack/connectionconfig2.png 
[3]: ./media/connectors-create-api-slack/connectionconfig3.png
[4]: ./media/connectors-create-api-slack/connectionconfig4.png
[5]: ./media/connectors-create-api-slack/connectionconfig5.png
[6]: ./media/connectors-create-api-slack/connectionconfig6.png
