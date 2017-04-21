<properties
pageTitle="Agregar el conector de Yammer en las aplicaciones de lógica | Microsoft Azure"
description="Información general sobre el conector de Yammer con parámetros de la API de REST"
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

# <a name="get-started-with-the-yammer-connector"></a>Empezar a trabajar con el conector de Yammer

Conectarse a Yammer a las conversaciones de acceso en la red de la empresa.

>[AZURE.NOTE] Esta versión de este artículo se aplica a la versión del esquema de 2015-08-01-vista previa de aplicaciones de lógica.

Con Yammer, puede:

- Crear el flujo de negocio basándose en los datos que recibe de Yammer. 
- Usar desencadenadores para cuando hay un mensaje nuevo en un grupo o una fuente de la siguiente.
- Usar acciones para publicar un mensaje, obtener todos los mensajes y mucho más. Estas acciones obtienen una respuesta y, a continuación, hacer que el resultado esté disponible para otras acciones. Por ejemplo, cuando aparezca un mensaje nuevo, puede enviar un correo electrónico usando Office 365.

Para agregar una operación en las aplicaciones de lógica, consulte [crear una aplicación de lógica](../app-service-logic/app-service-logic-create-a-logic-app.md).

## <a name="triggers-and-actions"></a>Desencadenadores y acciones
Yammer incluye los siguientes desencadenadores y acciones. 

Desencadenador | Acciones
--- | ---
<ul><li>Cuando hay un mensaje nuevo en un grupo</li><li>Cuando hay un mensaje nuevo en la siguiente fuente</li></ul>| <ul><li>Obtener todos los mensajes</li><li>Obtiene los mensajes de un grupo</li><li>Obtiene que la fuente de los mensajes de mi siguiente</li><li>Mensaje de entrada</li><li>Cuando hay un mensaje nuevo en un grupo</li><li>Cuando hay un mensaje nuevo en la siguiente fuente</li></ul>

Todos los conectores admiten datos en formatos XML y JSON. 

## <a name="create-a-connection-to-yammer"></a>Crear una conexión a Yammer
Para usar el conector de Yammer, primero crear una **conexión** y proporcionar los detalles de estas propiedades: 

|(Propiedad)| Obligatorio|Descripción|
| ---|---|---|
|Token|Sí|Proporcionar las credenciales de Yammer|

>[AZURE.INCLUDE [Steps to create a connection to Yammer](../../includes/connectors-create-api-yammer.md)]


>[AZURE.TIP] Puede usar esta conexión en otras aplicaciones de lógica.

## <a name="yammer-rest-api-reference"></a>Referencia de la API de REST de yammer
Esta documentación es de versión: 1.0


### <a name="get-all-public-messages-in-the-logged-in-users-yammer-network"></a>Obtener todos los mensajes públicos en la red de Yammer del usuario conectado
Se corresponde con "Todos" conversaciones en la interfaz de web de Yammer.  
```GET: /messages.json```

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|older_then|entero|No|consulta|Ninguno|Devuelve los mensajes anteriores al identificador de mensaje especificado como una cadena numérica. Esto es útil para paginar mensajes. Por ejemplo, si está viendo actualmente 20 mensajes y el más antiguo es el número 2912, podría anexar "? older_than = 2912″ a su solicitud para obtener los 20 mensajes anteriores a los que está viendo.|
|newer_then|entero|No|consulta|Ninguno|Devuelve mensajes más reciente que el identificador de mensaje especificado como una cadena numérica. Se recomienda cuando sondeo para mensajes nuevos. Si está buscando mensajes y el mensaje más reciente devuelto es 3516, puede realizar una solicitud con el parámetro "? newer_than = 3516″ para asegurarse de que no obtenga duplicados de mensajes ya en la página.|
|límite|entero|No|consulta|Ninguno|Devuelve solo el número especificado de mensajes.|
|página|entero|No|consulta|Ninguno|Obtener la página especificada. Si devuelve datos están mayores que el límite, puede usar este campo para tener acceso a las páginas siguientes|


### <a name="response"></a>Respuesta

|Nombre|Descripción|
|---|---|
|200|Vale|
|400|Solicitud incorrecta|
|408|Tiempo de espera de solicitud|
|429|Demasiadas solicitudes|
|500|Error de servidor interno. Error desconocido|
|503|Yammer servicio no disponible|
|504|Tiempo de espera de puerta de enlace|
|predeterminado|Error en la operación.|


### <a name="post-a-message-to-a-group-or-all-company-feed"></a>Publicar un mensaje a un grupo o la fuente de toda la compañía
Si se proporciona el identificador del grupo, el mensaje se registrará al grupo especificado más que se registrará en todas las fuentes de empresa.    
```POST: /messages.json``` 

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|entrada| |Sí|cuerpo|Ninguno|Mensaje de solicitud de publicación|


### <a name="response"></a>Respuesta

|Nombre|Descripción|
|---|---|
|201|Creado|



## <a name="object-definitions"></a>Definiciones de objeto

#### <a name="message-yammer-message"></a>Mensaje: Mensaje de Yammer

| Nombre | Tipo de datos | Obligatorio |
|---|---| --- | 
|Id.|entero|No|
|content_excerpt|cadena|No|
|sender_id|entero|No|
|replied_to_id|entero|No|
|created_at|cadena|No|
|network_id|entero|No|
|message_type|cadena|No|
|sender_type|cadena|No|
|dirección URL|cadena|No|
|web_url|cadena|No|
|group_id|entero|No|
|cuerpo|no definido|No|
|thread_id|entero|No|
|direct_message|valor booleano|No|
|client_type|cadena|No|
|client_url|cadena|No|
|idioma|cadena|No|
|notified_user_ids|matriz|No|
|declaración de privacidad|cadena|No|
|liked_by|no definido|No|
|system_message|valor booleano|No|

#### <a name="postoperationrequest-represents-a-post-request-for-yammer-connector-to-post-to-yammer"></a>PostOperationRequest: Representa una solicitud de publicación de conector de Yammer publicar yammer

| Nombre | Tipo de datos | Obligatorio |
|---|---| --- | 
|cuerpo|cadena|Sí|
|group_id|entero|No|
|replied_to_id|entero|No|
|direct_to_id|entero|No|
|difusión|valor booleano|No|
|tema1|cadena|No|
|tema2|cadena|No|
|topic3|cadena|No|
|topic4|cadena|No|
|topic5|cadena|No|
|topic6|cadena|No|
|topic7|cadena|No|
|topic8|cadena|No|
|topic9|cadena|No|
|topic10|cadena|No|
|topic11|cadena|No|
|topic12|cadena|No|
|topic13|cadena|No|
|topic14|cadena|No|
|topic15|cadena|No|
|topic16|cadena|No|
|topic17|cadena|No|
|topic18|cadena|No|
|topic19|cadena|No|
|topic20|cadena|No|

#### <a name="messagelist-list-of-messages"></a>MessageList: Lista de mensajes

| Nombre | Tipo de datos | Obligatorio |
|---|---| --- | 
|mensajes|matriz|No|


#### <a name="messagebody-message-body"></a>MessageBody: Cuerpo del mensaje

| Nombre | Tipo de datos | Obligatorio |
|---|---| --- | 
|analizar|cadena|No|
|sin formato|cadena|No|
|enriquecidos|cadena|No|

#### <a name="likedby-liked-by"></a>LikedBy: Le ha gustado por

| Nombre | Tipo de datos | Obligatorio |
|---|---| --- | 
|recuento|entero|No|
|nombres|matriz|No|

#### <a name="yammmerentity-liked-by"></a>YammmerEntity: Le ha gustado por

| Nombre | Tipo de datos | Obligatorio |
|---|---| --- | 
|tipo|cadena|No|
|Id.|entero|No|
|FULL_NAME|cadena|No|


## <a name="next-steps"></a>Pasos siguientes
[Crear una aplicación de lógica](../app-service-logic/app-service-logic-create-a-logic-app.md).

[1]: ./media/connectors-create-api-yammer/connectionconfig1.png
[2]: ./media/connectors-create-api-yammer/connectionconfig2.png 
[3]: ./media/connectors-create-api-yammer/connectionconfig3.png
[4]: ./media/connectors-create-api-yammer/connectionconfig4.png
[5]: ./media/connectors-create-api-yammer/connectionconfig5.png
