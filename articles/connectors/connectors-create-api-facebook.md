<properties
    pageTitle="Agregar el conector de Facebook en las aplicaciones de lógica | Microsoft Azure"
    description="Información general sobre el conector de Facebook con parámetros de la API de REST"
    services=""
    documentationCenter="" 
    authors="MandiOhlinger"
    manager="erikre"
    editor=""
    tags="connectors"/>

<tags
   ms.service="multiple"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na" 
   ms.date="08/18/2016"
   ms.author="mandia"/>

# <a name="get-started-with-the-facebook-connector"></a>Empezar a trabajar con el conector de Facebook
Conectar con Facebook y publicar en una escala de tiempo, obtener una fuente de página y mucho más. 

>[AZURE.NOTE] Esta versión de este artículo se aplica a la versión del esquema de 2015-08-01-vista previa de aplicaciones de lógica.


Facebook, puede:

- Crear el flujo de negocio basándose en los datos que recibe de Facebook. 
- Use un desencadenador cuando se recibe una nueva publicación.
- Acciones de uso que publican en la escala de tiempo, obtener una fuente de página y mucho más. Estas acciones obtienen una respuesta y, a continuación, hacer que el resultado esté disponible para otras acciones. Por ejemplo, cuando hay una nueva publicación en la escala de tiempo, puede tomar esa publicación y enviarlo a la fuente de Twitter. 



Para agregar una operación en las aplicaciones de lógica, consulte [crear una aplicación de lógica](../app-service-logic/app-service-logic-create-a-logic-app.md).

## <a name="triggers-and-actions"></a>Desencadenadores y acciones
El conector de Facebook incluye los siguientes desencadenador y acciones. 

| Desencadenadores | Acciones|
| --- | --- |
| <ul><li>Cuando hay una nueva publicación en la escala de tiempo</li></ul> |<ul><li>Obtener la fuente de la escala de tiempo</li><li>Publicar en mi escala de tiempo</li><li>Cuando hay una nueva publicación en la escala de tiempo</li><li>Fuente de página</li><li>Obtener la escala de tiempo de usuario</li><li>Publicar en la página</li></ul>

Todos los conectores admiten datos en formatos XML y JSON.

## <a name="create-a-connection-to-facebook"></a>Crear una conexión a Facebook
Cuando agrega este conector a sus aplicaciones de lógica, debe autorizar aplicaciones lógica para conectarse a su Facebook.

1. Inicie sesión en su cuenta de Facebook
2. Seleccione **autorizar**y permitir que las aplicaciones de lógica conectar y utilizar su Facebook. 

>[AZURE.INCLUDE [Steps to create a connection to Facebook](../../includes/connectors-create-api-facebook.md)]

>[AZURE.TIP] Puede utilizar esta misma conexión de Facebook en otras aplicaciones de lógica.

## <a name="swagger-rest-api-reference"></a>Referencia de la API de REST de swagger
Se aplica a la versión: 1.0.

### <a name="get-feed-from-my-timeline"></a>Obtener la fuente de la escala de tiempo
Obtiene las fuentes de escala de tiempo del usuario registrado.  
```GET: /me/feed```

| Nombre|Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|campos|cadena|No|consulta|Ninguno |Especificar los campos que desee incluir. Ejemplo (id, nombre, imagen).|
|límite|entero|No|consulta| Ninguno|Número máximo de entradas que se recuperarán|
|con|cadena|No|consulta| Ninguno|Restringir la lista de publicaciones a sólo aquellos con ubicación adjuntado.|
|filtro|cadena|No|consulta| Ninguno|Recuperar solo las entradas que coincidan con un filtro de secuencia concreta.|

#### <a name="response"></a>Respuesta
|Nombre|Descripción|
|---|---|
|200|Vale|
|400|Solicitud incorrecta|
|500|Error interno del servidor|
|predeterminado|Error en la operación.|


### <a name="post-to-my-timeline"></a>Publicar en mi escala de tiempo
Publicar un mensaje de estado a la escala de tiempo del usuario registrado.  
```POST: /me/feed```

| Nombre|Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|Exponer|cadena |Sí|cuerpo|Ninguno |Nuevo mensaje para registrarse|

#### <a name="response"></a>Respuesta
|Nombre|Descripción|
|---|---|
|200|Vale|
|400|Solicitud incorrecta|
|500|Error interno del servidor|
|predeterminado|Error en la operación.|


### <a name="when-there-is-a-new-post-on-my-timeline"></a>Cuando hay una nueva publicación en la escala de tiempo
Activa un flujo de nuevo cuando hay una nueva publicación en escala de tiempo del usuario ha iniciado sesión.  
```GET: /trigger/me/feed```

No hay parámetros. 

#### <a name="response"></a>Respuesta
|Nombre|Descripción|
|---|---|
|200|Vale|
|400|Solicitud incorrecta|
|500|Error interno del servidor|
|predeterminado|Error en la operación.|


### <a name="get-page-feed"></a>Fuente de página
Obtener entradas de la fuente de la página especificada.  
```GET: /{pageId}/feed```

| Nombre|Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|Vez|cadena|Sí|ruta de acceso| Ninguno|Identificador de la página desde la que publicaciones tienen que recuperarse.|
|límite|entero|No|consulta| Ninguno|Número máximo de entradas que se recuperarán|
|include_hidden|valor booleano|No|consulta|Ninguno |Si no desea incluir las entradas que se han ocultado mediante la página|
|campos|cadena|No|consulta|Ninguno |Especificar los campos que desee incluir. Ejemplo (id, nombre, imagen).|

#### <a name="response"></a>Respuesta
|Nombre|Descripción|
|---|---|
|200|Vale|
|400|Solicitud incorrecta|
|500|Error interno del servidor|
|predeterminado|Error en la operación.|


### <a name="get-user-timeline"></a>Obtener la escala de tiempo de usuario
Obtener entradas de escala de tiempo de un usuario.  
```GET: /{userId}/feed```

| Nombre|Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|identificador de usuario|cadena|Sí|ruta de acceso|Ninguno |Identificador del usuario cuya escala de tiempo se van a recuperar.|
|límite|entero|No|consulta|Ninguno |Número máximo de entradas que se recuperarán|
|con|cadena|No|consulta|Ninguno |Restringir la lista de publicaciones a sólo aquellos con ubicación adjuntado.|
|filtro|cadena|No|consulta| Ninguno|Recuperar solo las entradas que coincidan con un filtro de secuencia concreta.|
|campos|cadena|No|consulta| Ninguno|Especificar los campos que desee incluir. Ejemplo (id, nombre, imagen).|

#### <a name="response"></a>Respuesta
|Nombre|Descripción|
|---|---|
|200|Vale|
|400|Solicitud incorrecta|
|500|Error interno del servidor|
|predeterminado|Error en la operación.|


### <a name="post-to-page"></a>Publicar en la página
Publicar un mensaje a una Facebook Page como el usuario que inició sesión.  
```POST: /{pageId}/feed```

| Nombre|Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|Vez|cadena|Sí|ruta de acceso|Ninguno |Identificador de la página para publicar.|
|Exponer|muchos |Sí|cuerpo|Ninguno |Nuevo mensaje que se publicará.|

#### <a name="response"></a>Respuesta
|Nombre|Descripción|
|---|---|
|200|Vale|
|400|Solicitud incorrecta|
|500|Error interno del servidor|
|predeterminado|Error en la operación.|


## <a name="object-definitions"></a>Definiciones de objeto

#### <a name="getfeedresponse"></a>GetFeedResponse

|Nombre de propiedad | Tipo de datos | Obligatorio|
|---|---|---|
|datos|matriz|No|

#### <a name="triggerfeedresponse"></a>TriggerFeedResponse

|Nombre de propiedad | Tipo de datos |Obligatorio|
|---|---|---|
|datos|matriz|No|

#### <a name="postitem-a-single-entry-in-a-profiles-feed"></a>PostItem: Una entrada en un perfil de fuente
El perfil podría ser un usuario, la página, la aplicación o el grupo. 

|Nombre de propiedad | Tipo de datos |Obligatorio|
|---|---|---|
|Id.|cadena|No|
|admin_creator|matriz|No|
|título|cadena|No|
|created_time|cadena|No|
|Descripción|cadena|No|
|feed_targeting|no definido|No|
|De|no definido|No|
|icono|cadena|No|
|is_hidden|valor booleano|No|
|is_published|valor booleano|No|
|vínculo|cadena|No|
|Mensaje|cadena|No|
|nombre|cadena|No|
|object_id|cadena|No|
|imagen|cadena|No|
|colocar|no definido|No|
|declaración de privacidad|no definido|No|
|propiedades|matriz|No|
|origen|cadena|No|
|status_type|cadena|No|
|Historia|cadena|No|
|identificación|no definido|No|
|Para|matriz|No|
|tipo|cadena|No|
|updated_time|cadena|No|
|with_tags|no definido|No|

#### <a name="triggeritem-a-single-entry-in-a-profiles-feed"></a>TriggerItem: Una entrada en un perfil de fuente
El perfil podría ser un usuario, la página, la aplicación o el grupo.

|Nombre de propiedad | Tipo de datos |Obligatorio|
|---|---|---|
|Id.|cadena|No|
|created_time|cadena|No|
|De|no definido|No|
|Mensaje|cadena|No|
|tipo|cadena|No|

#### <a name="adminitem"></a>AdminItem

|Nombre de propiedad | Tipo de datos |Obligatorio|
|---|---|---|
|Id.|cadena|No|
|vínculo|cadena|No|

#### <a name="propertyitem"></a>PropertyItem

|Nombre de propiedad | Tipo de datos |Obligatorio|
|---|---|---|
|nombre|cadena|No|
|texto|cadena|No|
|referencia de|cadena|No|

#### <a name="userpostfeedrequest"></a>UserPostFeedRequest

|Nombre de propiedad | Tipo de datos |Obligatorio|
|---|---|---|
|Mensaje|cadena|Sí|
|vínculo|cadena|No|
|imagen|cadena|No|
|nombre|cadena|No|
|título|cadena|No|
|Descripción|cadena|No|
|colocar|cadena|No|
|etiquetas|cadena|No|
|declaración de privacidad|no definido|No|
|object_attachment|cadena|No|

#### <a name="pagepostfeedrequest"></a>PagePostFeedRequest

|Nombre de propiedad | Tipo de datos |Obligatorio|
|---|---|---|
|Mensaje|cadena|Sí|
|vínculo|cadena|No|
|imagen|cadena|No|
|nombre|cadena|No|
|título|cadena|No|
|Descripción|cadena|No|
|acciones|matriz|No|
|colocar|cadena|No|
|etiquetas|cadena|No|
|object_attachment|cadena|No|
|identificación|no definido|No|
|feed_targeting|no definido|No|
|publicado|valor booleano|No|
|scheduled_publish_time|cadena|No|
|backdated_time|cadena|No|
|backdated_time_granularity|cadena|No|
|child_attachments|matriz|No|
|multi_share_end_card|valor booleano|No|

#### <a name="postfeedresponse"></a>PostFeedResponse

|Nombre de propiedad | Tipo de datos |Obligatorio|
|---|---|---|
|Id.|cadena|No|

#### <a name="profilecollection"></a>ProfileCollection

|Nombre de propiedad | Tipo de datos |Obligatorio|
|---|---|---|
|datos|matriz|No|

#### <a name="useritem"></a>UserItem

|Nombre de propiedad | Tipo de datos |Obligatorio|
|---|---|---|
|Id.|cadena|No|
|nombre|cadena|No|
|apellido|cadena|No|
|nombre|cadena|No|
|género|cadena|No|
|acerca de|cadena|No|

#### <a name="actionitem"></a>ActionItem

|Nombre de propiedad | Tipo de datos |Obligatorio|
|---|---|---|
|nombre|cadena|No|
|vínculo|cadena|No|

#### <a name="targetitem"></a>TargetItem

|Nombre de propiedad | Tipo de datos |Obligatorio|
|---|---|---|
|países|matriz|No|
|configuraciones regionales|matriz|No|
|regiones|matriz|No|
|ciudades|matriz|No|

#### <a name="feedtargetitem-object-that-controls-news-feed-targeting-for-this-post"></a>FeedTargetItem: Identificación para esta entrada de la fuente de objeto que controla noticias
Cualquiera de estos grupos es más probable que vea esta entrada y otras son menos probables. Solo se aplica a las páginas.

|Nombre de propiedad | Tipo de datos |Obligatorio|
|---|---|---|
|países|matriz|No|
|regiones|matriz|No|
|ciudades|matriz|No|
|age_min|entero|No|
|age_max|entero|No|
|géneros|matriz|No|
|relationship_statuses|matriz|No|
|interested_in|matriz|No|
|college_years|matriz|No|
|intereses|matriz|No|
|relevant_until|entero|No|
|education_statuses|matriz|No|
|configuraciones regionales|matriz|No|

#### <a name="placeitem"></a>PlaceItem

|Nombre de propiedad | Tipo de datos |Obligatorio|
|---|---|---|
|Id.|cadena|No|
|nombre|cadena|No|
|overall_rating|número|No|
|ubicación|no definido|No|

#### <a name="locationitem"></a>LocationItem

|Nombre de propiedad | Tipo de datos |Obligatorio|
|---|---|---|
|Ciudad|cadena|No|
|país|cadena|No|
|latitud|número|No|
|located_in|cadena|No|
|latitud|número|No|
|nombre|cadena|No|
|región|cadena|No|
|estado|cadena|No|
|Calle|cadena|No|
|ZIP|cadena|No|

#### <a name="privacyitem"></a>PrivacyItem

|Nombre de propiedad | Tipo de datos |Obligatorio|
|---|---|---|
|Descripción|cadena|No|
|valor|cadena|Sí|
|Permitir|cadena|No|
|denegar|cadena|No|
|amigos|cadena|No|

#### <a name="childattachmentsitem"></a>ChildAttachmentsItem

|Nombre de propiedad | Tipo de datos |Obligatorio|
|---|---|---|
|vínculo|cadena|No|
|imagen|cadena|No|
|image_hash|cadena|No|
|nombre|cadena|No|
|Descripción|cadena|No|

#### <a name="postphotorequest"></a>PostPhotoRequest

|Nombre de propiedad | Tipo de datos |Obligatorio|
|---|---|---|
|dirección URL|cadena|Sí|
|título|cadena|No|

#### <a name="postphotoresponse"></a>PostPhotoResponse

|Nombre de propiedad | Tipo de datos |Obligatorio|
|---|---|---|
|Id.|cadena|Sí|
|post_id|cadena|Sí|

#### <a name="postvideorequest"></a>PostVideoRequest

|Nombre de propiedad | Tipo de datos |Obligatorio|
|---|---|---|
|videoData|cadena|Sí|
|Descripción|cadena|Sí|
|título|cadena|Sí|
|uploadedVideoName|cadena|No|

#### <a name="getphotoresponse"></a>GetPhotoResponse

|Nombre de propiedad | Tipo de datos |Obligatorio|
|---|---|---|
|datos|no definido|Sí|

#### <a name="getphotoresponseitem"></a>GetPhotoResponseItem

|Nombre de propiedad | Tipo de datos |Obligatorio|
|---|---|---|
|dirección URL|cadena|Sí|
|is_silhouette|valor booleano|Sí|
|alto|cadena|No|
|Ancho|cadena|No|

#### <a name="geteventresponse"></a>GetEventResponse

|Nombre de propiedad | Tipo de datos |Obligatorio|
|---|---|---|
|datos|matriz|Sí|

#### <a name="geteventresponseitem"></a>GetEventResponseItem

|Nombre de propiedad | Tipo de datos |Obligatorio|
|---|---|---|
|Id.|cadena|Sí|
|nombre|cadena|Sí|
|start_time|cadena|No|
|end_time|cadena|No|
|zona horaria|cadena|No|
|ubicación|cadena|No|
|Descripción|cadena|No|
|ticket_uri|cadena|No|
|rsvp_status|cadena|Sí|


## <a name="next-steps"></a>Pasos siguientes

[Crear una aplicación de lógica](../app-service-logic/app-service-logic-create-a-logic-app.md).
