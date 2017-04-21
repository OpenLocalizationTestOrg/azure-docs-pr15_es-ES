<properties
pageTitle="Outlook.com | Microsoft Azure"
description="Crear aplicaciones de lógica con el servicio de aplicación de Azure. Conector de Outlook.com le permite administrar su correo, calendarios y contactos. Puede realizar varias acciones, como enviar correo, programar reuniones, agregar contactos, etcetera."
services="logic-apps"   
documentationCenter=".net,nodejs,java"  
authors="msftman"   
manager="erikre"    
editor=""
tags="connectors" />

<tags
ms.service="logic-apps"
ms.devlang="multiple"
ms.topic="article"
ms.tgt_pltfrm="na"
ms.workload="integration"
ms.date="08/18/2016"
ms.author="deonhe"/>

# <a name="get-started-with-the-outlookcom-connector"></a>Empezar a trabajar con el conector de Outlook.com

Conector de Outlook.com le permite administrar su correo, calendarios y contactos. Puede realizar varias acciones, como enviar correo, programar reuniones, agregar contactos, etcetera.

>[AZURE.NOTE] Esta versión de este artículo se aplica a la versión del esquema de 2015-08-01-vista previa de aplicaciones de lógica. 

Puede empezar creando una aplicación de lógica ahora, vea [crear una aplicación de lógica](../app-service-logic/app-service-logic-create-a-logic-app.md).

## <a name="triggers-and-actions"></a>Desencadenadores y acciones

El conector de Outlook.com puede utilizarse como una acción; tiene desencadenadores. Todos los conectores admiten datos en formatos XML y JSON. 

 El conector de Outlook.com tiene las siguientes acciones o desencadenadores disponibles:

### <a name="outlookcom-actions"></a>Acciones de Outlook.com
Puede realizar estas acciones:

|Acción|Descripción|
|--- | ---|
|[GetEmails](connectors-create-api-outlook.md#GetEmails)|Recupera correos electrónicos de una carpeta|
|[SendEmail](connectors-create-api-outlook.md#SendEmail)|Envía un correo electrónico|
|[DeleteEmail](connectors-create-api-outlook.md#DeleteEmail)|Elimina un mensaje de correo por Id.|
|[MarkAsRead](connectors-create-api-outlook.md#MarkAsRead)|Marca un correo electrónico como leídos|
|[Origen](connectors-create-api-outlook.md#ReplyTo)|Respuestas a un correo electrónico|
|[GetAttachment](connectors-create-api-outlook.md#GetAttachment)|Datos adjuntos de correo electrónico recupera por Id.|
|[SendMailWithOptions](connectors-create-api-outlook.md#SendMailWithOptions)|Enviar un correo electrónico con varias opciones y espere a que el destinatario responder con una de las opciones|
|[SendApprovalMail](connectors-create-api-outlook.md#SendApprovalMail)|Enviar un correo electrónico de aprobación y espere una respuesta del destinatario|
|[CalendarGetTables](connectors-create-api-outlook.md#CalendarGetTables)|Recupera los calendarios|
|[CalendarGetItems](connectors-create-api-outlook.md#CalendarGetItems)|Recupera los elementos de calendario|
|[CalendarPostItem](connectors-create-api-outlook.md#CalendarPostItem)|Crea un nuevo evento|
|[CalendarGetItem](connectors-create-api-outlook.md#CalendarGetItem)|Recupera un elemento específico de un calendario|
|[CalendarDeleteItem](connectors-create-api-outlook.md#CalendarDeleteItem)|Elimina un elemento de calendario|
|[CalendarPatchItem](connectors-create-api-outlook.md#CalendarPatchItem)|Parcialmente actualiza un elemento de calendario|
|[ContactGetTables](connectors-create-api-outlook.md#ContactGetTables)|Recupera las carpetas de contactos|
|[ContactGetItems](connectors-create-api-outlook.md#ContactGetItems)|Recupera los contactos de una carpeta de contactos|
|[ContactPostItem](connectors-create-api-outlook.md#ContactPostItem)|Crea un nuevo contacto|
|[ContactGetItem](connectors-create-api-outlook.md#ContactGetItem)|Recupera un contacto específico de una carpeta de contactos|
|[ContactDeleteItem](connectors-create-api-outlook.md#ContactDeleteItem)|Elimina un contacto|
|[ContactPatchItem](connectors-create-api-outlook.md#ContactPatchItem)|Parcialmente actualiza un contacto|
### <a name="outlookcom-triggers"></a>Desencadenadores de Outlook.com
Puede escuchar para estos eventos:

|Desencadenador | Descripción|
|--- | ---|
|En el evento comienzan pronto|Activa un flujo cuando se inicia un evento de calendario de próximos eventos|
|En correo electrónico nuevo|Activa un flujo cuando reciba un nuevo correo electrónico|
|En nuevos elementos|Activa cuando se crea un nuevo elemento de calendario|
|En elementos actualizados|Activa cuando se modifica un elemento de calendario|


## <a name="create-a-connection-to-outlookcom"></a>Crear una conexión a Outlook.com
Para crear aplicaciones de lógica con Outlook.com, primero debe crear una **conexión** y proporcionar los detalles de las siguientes propiedades: 

|(Propiedad)| Obligatorio|Descripción|
| ---|---|---|
|Token|Sí|Proporcionar las credenciales de Outlook.com|
Después de crear la conexión, puede usarlo para ejecutar las acciones y escuchar los desencadenadores descritos en este artículo.

>[AZURE.INCLUDE [Steps to create a connection to Outlook.com](../../includes/connectors-create-api-outlook.md)] 

>[AZURE.TIP] Puede usar esta conexión en otras aplicaciones de lógica.  

## <a name="reference-for-outlookcom"></a>Referencia de Outlook.com
Se aplica a la versión: 1.0

## <a name="onupcomingevents"></a>OnUpcomingEvents
En el evento comienzan pronto: activa un flujo cuando se inicia un evento de calendario de próximos eventos 

```GET: /Events/OnUpcomingEvents``` 

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|tabla|cadena|Sí|consulta|Ninguno|Identificador único del calendario|
|lookAheadTimeInMinutes|entero|No|consulta|15|Tiempo (en minutos) para buscar anticipada próximos eventos|

#### <a name="response"></a>Respuesta

|Nombre|Descripción|
|---|---|
|200|La operación es correcto|
|202|La operación es correcto|
|400|BadRequest|
|401|No autorizado|
|403|Prohibido|
|500|Error interno del servidor|
|predeterminado|Error en la operación.|


## <a name="getemails"></a>GetEmails
Obtener mensajes de correo electrónico: recupera correos electrónicos de una carpeta 

```GET: /Mail``` 

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|folderPath|cadena|No|consulta|Bandeja de entrada|Ruta de acceso de la carpeta para recuperar mensajes de correo electrónico (predeterminado: 'Buzón')|
|Arriba|entero|No|consulta|10|Número de mensajes de correo electrónico para recuperar (predeterminado: 10)|
|fetchOnlyUnread|valor booleano|No|consulta|Verdadero|¿Recuperar solo los correos electrónicos no leídos?|
|includeAttachments|valor booleano|No|consulta|falso|Si el conjunto de datos adjuntos es true, también se va a recuperar junto con el correo electrónico|
|searchQuery|cadena|No|consulta|Ninguno|Consulta de búsqueda para filtrar los mensajes de correo electrónico|
|saltar|entero|No|consulta|0|Número de mensajes de correo electrónico para omitir (valor predeterminado: 0)|
|skipToken|cadena|No|consulta|Ninguno|Omitir token a capturar nueva página|

#### <a name="response"></a>Respuesta

|Nombre|Descripción|
|---|---|
|200|La operación es correcto|
|400|BadRequest|
|401|No autorizado|
|403|Prohibido|
|500|Error interno del servidor|
|predeterminado|Error en la operación.|


## <a name="sendemail"></a>SendEmail
Enviar correo electrónico: envía un correo electrónico 

```POST: /Mail``` 

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|emailMessage| |Sí|cuerpo|Ninguno|Correo electrónico|

#### <a name="response"></a>Respuesta

|Nombre|Descripción|
|---|---|
|200|La operación es correcto|
|400|BadRequest|
|401|No autorizado|
|403|Prohibido|
|500|Error interno del servidor|
|predeterminado|Error en la operación.|


## <a name="deleteemail"></a>DeleteEmail
Eliminar el correo electrónico: elimina un mensaje de correo por Id. 

```DELETE: /Mail/{messageId}``` 

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|Id. de mensaje|cadena|Sí|ruta de acceso|Ninguno|Id. del correo electrónico para eliminar|

#### <a name="response"></a>Respuesta

|Nombre|Descripción|
|---|---|
|200|La operación es correcto|
|400|BadRequest|
|401|No autorizado|
|403|Prohibido|
|500|Error interno del servidor|
|predeterminado|Error en la operación.|


## <a name="markasread"></a>MarkAsRead
Marcar como leído: marca un correo electrónico como leídos 

```POST: /Mail/MarkAsRead/{messageId}``` 

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|Id. de mensaje|cadena|Sí|ruta de acceso|Ninguno|Identificador del correo electrónico para marcar como leído|

#### <a name="response"></a>Respuesta

|Nombre|Descripción|
|---|---|
|200|La operación es correcto|
|400|BadRequest|
|401|No autorizado|
|403|Prohibido|
|500|Error interno del servidor|
|predeterminado|Error en la operación.|


## <a name="replyto"></a>Origen
Responder al correo electrónico: respuestas a un correo electrónico 

```POST: /Mail/ReplyTo/{messageId}``` 

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|Id. de mensaje|cadena|Sí|ruta de acceso|Ninguno|Id. del correo electrónico para responder a|
|comentario|cadena|Sí|consulta|Ninguno|Comentario de respuesta|
|responder a todos|valor booleano|No|consulta|falso|Responder a todos los destinatarios|

#### <a name="response"></a>Respuesta

|Nombre|Descripción|
|---|---|
|200|La operación es correcto|
|400|BadRequest|
|401|No autorizado|
|403|Prohibido|
|500|Error interno del servidor|
|predeterminado|Error en la operación.|


## <a name="getattachment"></a>GetAttachment
Obtener datos adjuntos: recupera datos adjuntos de correo por Id. 

```GET: /Mail/{messageId}/Attachments/{attachmentId}``` 

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|Id. de mensaje|cadena|Sí|ruta de acceso|Ninguno|Id. del correo electrónico|
|attachmentId|cadena|Sí|ruta de acceso|Ninguno|Id. del archivo adjunto para descargar|

#### <a name="response"></a>Respuesta

|Nombre|Descripción|
|---|---|
|200|La operación es correcto|
|400|BadRequest|
|401|No autorizado|
|403|Prohibido|
|500|Error interno del servidor|
|predeterminado|Error en la operación.|


## <a name="onnewemail"></a>OnNewEmail
En nuevo correo electrónico: activa un flujo cuando reciba un nuevo correo electrónico 

```GET: /Mail/OnNewEmail``` 

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|folderPath|cadena|No|consulta|Bandeja de entrada|Carpeta de correo electrónico para recuperar (predeterminado: Bandeja de entrada)|
|Para|cadena|No|consulta|Ninguno|Direcciones de correo electrónico del destinatario|
|De|cadena|No|consulta|Ninguno|De dirección|
|importancia|cadena|No|consulta|Normal|Importancia del correo electrónico (alta, Normal, baja) (valor predeterminado: Normal)|
|fetchOnlyWithAttachment|valor booleano|No|consulta|falso|Recuperar solo los correos electrónicos con datos adjuntos|
|includeAttachments|valor booleano|No|consulta|falso|Incluir datos adjuntos|
|subjectFilter|cadena|No|consulta|Ninguno|Cadena para buscar en el asunto|

#### <a name="response"></a>Respuesta

|Nombre|Descripción|
|---|---|
|200|La operación es correcto|
|202|Aceptado|
|400|BadRequest|
|401|No autorizado|
|403|Prohibido|
|500|Error interno del servidor|
|predeterminado|Error en la operación.|


## <a name="sendmailwithoptions"></a>SendMailWithOptions
Enviar correo electrónico con opciones: enviar un correo electrónico con varias opciones y espere a que el destinatario responder con una de las opciones 

```POST: /mailwithoptions/$subscriptions``` 

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|optionsEmailSubscription| |Sí|cuerpo|Ninguno|Solicitud de suscripción para el correo electrónico de opciones|

#### <a name="response"></a>Respuesta

|Nombre|Descripción|
|---|---|
|200|Vale|
|201|Suscripción creada|
|400|BadRequest|
|401|No autorizado|
|403|Prohibido|
|500|Error interno del servidor|
|predeterminado|Error en la operación.|


## <a name="sendapprovalmail"></a>SendApprovalMail
Enviar correo electrónico de aprobación: enviar un correo electrónico de aprobación y espere una respuesta del destinatario 

```POST: /approvalmail/$subscriptions``` 

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|approvalEmailSubscription| |Sí|cuerpo|Ninguno|Solicitud de suscripción para el correo electrónico de aprobación|

#### <a name="response"></a>Respuesta

|Nombre|Descripción|
|---|---|
|200|Vale|
|201|Suscripción creada|
|400|BadRequest|
|401|No autorizado|
|403|Prohibido|
|500|Error interno del servidor|
|predeterminado|Error en la operación.|


## <a name="calendargettables"></a>CalendarGetTables
Obtener calendarios: recupera los calendarios 

```GET: /datasets/calendars/tables``` 

No hay parámetros para esta llamada
#### <a name="response"></a>Respuesta

|Nombre|Descripción|
|---|---|
|200|Vale|
|predeterminado|Error en la operación.|


## <a name="calendargetitems"></a>CalendarGetItems
Obtenga los eventos: recupera los elementos de calendario 

```GET: /datasets/calendars/tables/{table}/items``` 

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|tabla|cadena|Sí|ruta de acceso|Ninguno|Identificador único del calendario para recuperar|
|$filter|cadena|No|consulta|Ninguno|Una consulta de filtro ODATA para restringir el número de entradas|
|$orderby|cadena|No|consulta|Ninguno|Una consulta ODATA orderBy para especificar el orden de las entradas.|
|$skip|entero|No|consulta|Ninguno|Número de entradas para omitir (predeterminado = 0)|
|$top|entero|No|consulta|Ninguno|Número máximo de entradas para recuperar (predeterminado = 256)|

#### <a name="response"></a>Respuesta

|Nombre|Descripción|
|---|---|
|200|Vale|
|predeterminado|Error en la operación.|


## <a name="calendarpostitem"></a>CalendarPostItem
Crear evento: crea un nuevo evento 

```POST: /datasets/calendars/tables/{table}/items``` 

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|tabla|cadena|Sí|ruta de acceso|Ninguno|Identificador único de un calendario|
|elemento| |Sí|cuerpo|Ninguno|Elemento de calendario para crear|

#### <a name="response"></a>Respuesta

|Nombre|Descripción|
|---|---|
|200|Vale|
|predeterminado|Error en la operación.|


## <a name="calendargetitem"></a>CalendarGetItem
Obtener evento: recupera un elemento específico de un calendario 

```GET: /datasets/calendars/tables/{table}/items/{id}``` 

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|tabla|cadena|Sí|ruta de acceso|Ninguno|Identificador único de un calendario|
|Id.|cadena|Sí|ruta de acceso|Ninguno|Identificador único de un elemento de calendario para recuperar|

#### <a name="response"></a>Respuesta

|Nombre|Descripción|
|---|---|
|200|Vale|
|predeterminado|Error en la operación.|


## <a name="calendardeleteitem"></a>CalendarDeleteItem
Eliminar evento: elimina un elemento de calendario 

```DELETE: /datasets/calendars/tables/{table}/items/{id}``` 

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|tabla|cadena|Sí|ruta de acceso|Ninguno|Identificador único de un calendario|
|Id.|cadena|Sí|ruta de acceso|Ninguno|Identificador único del elemento de calendario a eliminar|

#### <a name="response"></a>Respuesta

|Nombre|Descripción|
|---|---|
|200|Vale|
|predeterminado|Error en la operación.|


## <a name="calendarpatchitem"></a>CalendarPatchItem
Evento de actualización: parcialmente actualiza un elemento de calendario 

```PATCH: /datasets/calendars/tables/{table}/items/{id}``` 

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|tabla|cadena|Sí|ruta de acceso|Ninguno|Identificador único de un calendario|
|Id.|cadena|Sí|ruta de acceso|Ninguno|Identificador único del elemento de calendario para actualizar|
|elemento| |Sí|cuerpo|Ninguno|Elemento de calendario para actualizar|

#### <a name="response"></a>Respuesta

|Nombre|Descripción|
|---|---|
|200|Vale|
|predeterminado|Error en la operación.|


## <a name="calendargetonnewitems"></a>CalendarGetOnNewItems
En nuevos elementos: activa cuando se crea un nuevo elemento de calendario 

```GET: /datasets/calendars/tables/{table}/onnewitems``` 

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|tabla|cadena|Sí|ruta de acceso|Ninguno|Identificador único de un calendario|
|$filter|cadena|No|consulta|Ninguno|Una consulta de filtro ODATA para restringir el número de entradas|
|$orderby|cadena|No|consulta|Ninguno|Una consulta ODATA orderBy para especificar el orden de las entradas.|
|$skip|entero|No|consulta|Ninguno|Número de entradas para omitir (predeterminado = 0)|
|$top|entero|No|consulta|Ninguno|Número máximo de entradas para recuperar (predeterminado = 256)|

#### <a name="response"></a>Respuesta

|Nombre|Descripción|
|---|---|
|200|Vale|
|predeterminado|Error en la operación.|


## <a name="calendargetonupdateditems"></a>CalendarGetOnUpdatedItems
En los artículos actualizados: activa cuando se modifica un elemento de calendario 

```GET: /datasets/calendars/tables/{table}/onupdateditems``` 

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|tabla|cadena|Sí|ruta de acceso|Ninguno|Identificador único de un calendario|
|$filter|cadena|No|consulta|Ninguno|Una consulta de filtro ODATA para restringir el número de entradas|
|$orderby|cadena|No|consulta|Ninguno|Una consulta ODATA orderBy para especificar el orden de las entradas.|
|$skip|entero|No|consulta|Ninguno|Número de entradas para omitir (predeterminado = 0)|
|$top|entero|No|consulta|Ninguno|Número máximo de entradas para recuperar (predeterminado = 256)|

#### <a name="response"></a>Respuesta

|Nombre|Descripción|
|---|---|
|200|Vale|
|predeterminado|Error en la operación.|


## <a name="contactgettables"></a>ContactGetTables
Obtener las carpetas de contactos: recupera carpetas de contactos 

```GET: /datasets/contacts/tables``` 

No hay parámetros para esta llamada
#### <a name="response"></a>Respuesta

|Nombre|Descripción|
|---|---|
|200|Vale|
|predeterminado|Error en la operación.|


## <a name="contactgetitems"></a>ContactGetItems
Obtener contactos: recupera los contactos de una carpeta de contactos 

```GET: /datasets/contacts/tables/{table}/items``` 

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|tabla|cadena|Sí|ruta de acceso|Ninguno|Identificador único de la carpeta de contactos para recuperar|
|$filter|cadena|No|consulta|Ninguno|Una consulta de filtro ODATA para restringir el número de entradas|
|$orderby|cadena|No|consulta|Ninguno|Una consulta ODATA orderBy para especificar el orden de las entradas.|
|$skip|entero|No|consulta|Ninguno|Número de entradas para omitir (predeterminado = 0)|
|$top|entero|No|consulta|Ninguno|Número máximo de entradas para recuperar (predeterminado = 256)|

#### <a name="response"></a>Respuesta

|Nombre|Descripción|
|---|---|
|200|Vale|
|predeterminado|Error en la operación.|


## <a name="contactpostitem"></a>ContactPostItem
Crear contacto: crea un nuevo contacto 

```POST: /datasets/contacts/tables/{table}/items``` 

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|tabla|cadena|Sí|ruta de acceso|Ninguno|Identificador único de una carpeta de contactos|
|elemento| |Sí|cuerpo|Ninguno|Contacto para crear|

#### <a name="response"></a>Respuesta

|Nombre|Descripción|
|---|---|
|200|Vale|
|predeterminado|Error en la operación.|


## <a name="contactgetitem"></a>ContactGetItem
Obtener contacto: recupera un contacto específico de una carpeta de contactos 

```GET: /datasets/contacts/tables/{table}/items/{id}``` 

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|tabla|cadena|Sí|ruta de acceso|Ninguno|Identificador único de una carpeta de contactos|
|Id.|cadena|Sí|ruta de acceso|Ninguno|Identificador único de un contacto para recuperar|

#### <a name="response"></a>Respuesta

|Nombre|Descripción|
|---|---|
|200|Vale|
|predeterminado|Error en la operación.|


## <a name="contactdeleteitem"></a>ContactDeleteItem
Eliminar contacto: elimina un contacto 

```DELETE: /datasets/contacts/tables/{table}/items/{id}``` 

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|tabla|cadena|Sí|ruta de acceso|Ninguno|Identificador único de una carpeta de contactos|
|Id.|cadena|Sí|ruta de acceso|Ninguno|Identificador único del contacto para eliminar|

#### <a name="response"></a>Respuesta

|Nombre|Descripción|
|---|---|
|200|Vale|
|predeterminado|Error en la operación.|


## <a name="contactpatchitem"></a>ContactPatchItem
Actualizar contacto: parcialmente actualiza un contacto 

```PATCH: /datasets/contacts/tables/{table}/items/{id}``` 

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|tabla|cadena|Sí|ruta de acceso|Ninguno|Identificador único de una carpeta de contactos|
|Id.|cadena|Sí|ruta de acceso|Ninguno|Identificador único del contacto para la actualización|
|elemento| |Sí|cuerpo|Ninguno|Actualizar elemento de contacto|

#### <a name="response"></a>Respuesta

|Nombre|Descripción|
|---|---|
|200|Vale|
|predeterminado|Error en la operación.|


## <a name="object-definitions"></a>Definiciones de objeto 

### <a name="triggerbatchresponseidictionarystringobject"></a>TriggerBatchResponse [IDictionary [cadena, objeto]]


| Nombre de propiedad | Tipo de datos | Obligatorio |
|---|---|---|
|valor|matriz|No |



### <a name="object"></a>Objeto


| Nombre de propiedad | Tipo de datos | Obligatorio |
|---|---|---|



### <a name="sendmessage"></a>SendMessage


| Nombre de propiedad | Tipo de datos | Obligatorio |
|---|---|---|
|Datos adjuntos|matriz|No |
|De|cadena|No |
|CC|cadena|No |
|CCO|cadena|No |
|Asunto|cadena|Sí |
|Cuerpo|cadena|Sí |
|Importancia|cadena|No |
|IsHtml|valor booleano|No |
|Para|cadena|Sí |



### <a name="sendattachment"></a>SendAttachment


| Nombre de propiedad | Tipo de datos | Obligatorio |
|---|---|---|
|@odata.type|cadena|No |
|Nombre|cadena|Sí |
|ContentBytes|cadena|Sí |



### <a name="receivemessage"></a>ReceiveMessage


| Nombre de propiedad | Tipo de datos | Obligatorio |
|---|---|---|
|Id.|cadena|No |
|Estáleído|valor booleano|No |
|HasAttachment|valor booleano|No |
|DateTimeReceived|cadena|No |
|Datos adjuntos|matriz|No |
|De|cadena|No |
|CC|cadena|No |
|CCO|cadena|No |
|Asunto|cadena|Sí |
|Cuerpo|cadena|Sí |
|Importancia|cadena|No |
|IsHtml|valor booleano|No |
|Para|cadena|Sí |



### <a name="receiveattachment"></a>ReceiveAttachment


| Nombre de propiedad | Tipo de datos | Obligatorio |
|---|---|---|
|Id.|cadena|Sí |
|ContentType|cadena|Sí |
|@odata.type|cadena|No |
|Nombre|cadena|Sí |
|ContentBytes|cadena|Sí |



### <a name="digestmessage"></a>DigestMessage


| Nombre de propiedad | Tipo de datos | Obligatorio |
|---|---|---|
|Asunto|cadena|Sí |
|Cuerpo|cadena|No |
|Importancia|cadena|No |
|Resumen|matriz|Sí |
|Datos adjuntos|matriz|No |
|Para|cadena|Sí |



### <a name="triggerbatchresponsereceivemessage"></a>TriggerBatchResponse [ReceiveMessage]


| Nombre de propiedad | Tipo de datos | Obligatorio |
|---|---|---|
|valor|matriz|No |



### <a name="datasetsmetadata"></a>DataSetsMetadata


| Nombre de propiedad | Tipo de datos | Obligatorio |
|---|---|---|
|tabular|no definido|No |
|BLOB|no definido|No |



### <a name="tabulardatasetsmetadata"></a>TabularDataSetsMetadata


| Nombre de propiedad | Tipo de datos | Obligatorio |
|---|---|---|
|origen|cadena|No |
|displayName|cadena|No |
|urlEncoding|cadena|No |
|tableDisplayName|cadena|No |
|tablePluralName|cadena|No |



### <a name="blobdatasetsmetadata"></a>BlobDataSetsMetadata


| Nombre de propiedad | Tipo de datos | Obligatorio |
|---|---|---|
|origen|cadena|No |
|displayName|cadena|No |
|urlEncoding|cadena|No |



### <a name="tablemetadata"></a>TableMetadata


| Nombre de propiedad | Tipo de datos | Obligatorio |
|---|---|---|
|nombre|cadena|No |
|título|cadena|No |
|permiso de ms x|cadena|No |
|capacidades de ms x|no definido|No |
|esquema|no definido|No |



### <a name="tablecapabilitiesmetadata"></a>TableCapabilitiesMetadata


| Nombre de propiedad | Tipo de datos | Obligatorio |
|---|---|---|
|sortRestrictions|no definido|No |
|filterRestrictions|no definido|No |
|filterFunctions|matriz|No |



### <a name="tablesortrestrictionsmetadata"></a>TableSortRestrictionsMetadata


| Nombre de propiedad | Tipo de datos | Obligatorio |
|---|---|---|
|Puede ordenar|valor booleano|No |
|unsortableProperties|matriz|No |
|ascendingOnlyProperties|matriz|No |



### <a name="tablefilterrestrictionsmetadata"></a>TableFilterRestrictionsMetadata


| Nombre de propiedad | Tipo de datos | Obligatorio |
|---|---|---|
|Puede filtrar|valor booleano|No |
|nonFilterableProperties|matriz|No |
|requiredProperties|matriz|No |



### <a name="optionsemailsubscription"></a>OptionsEmailSubscription


| Nombre de propiedad | Tipo de datos | Obligatorio |
|---|---|---|
|NotificationUrl|cadena|No |
|Mensaje|no definido|No |



### <a name="messagewithoptions"></a>MessageWithOptions


| Nombre de propiedad | Tipo de datos | Obligatorio |
|---|---|---|
|Asunto|cadena|Sí |
|Opciones|cadena|Sí |
|Cuerpo|cadena|No |
|Importancia|cadena|No |
|Datos adjuntos|matriz|No |
|Para|cadena|Sí |



### <a name="subscriptionresponse"></a>SubscriptionResponse


| Nombre de propiedad | Tipo de datos | Obligatorio |
|---|---|---|
|Id.|cadena|No |
|recursos|cadena|No |
|notificationType|cadena|No |
|notificationUrl|cadena|No |



### <a name="approvalemailsubscription"></a>ApprovalEmailSubscription


| Nombre de propiedad | Tipo de datos | Obligatorio |
|---|---|---|
|NotificationUrl|cadena|No |
|Mensaje|no definido|No |



### <a name="approvalmessage"></a>ApprovalMessage


| Nombre de propiedad | Tipo de datos | Obligatorio |
|---|---|---|
|Asunto|cadena|Sí |
|Opciones|cadena|Sí |
|Cuerpo|cadena|No |
|Importancia|cadena|No |
|Datos adjuntos|matriz|No |
|Para|cadena|Sí |



### <a name="approvalemailresponse"></a>ApprovalEmailResponse


| Nombre de propiedad | Tipo de datos | Obligatorio |
|---|---|---|
|SelectedOption|cadena|No |



### <a name="tableslist"></a>TablesList


| Nombre de propiedad | Tipo de datos | Obligatorio |
|---|---|---|
|valor|matriz|No |



### <a name="table"></a>Tabla


| Nombre de propiedad | Tipo de datos | Obligatorio |
|---|---|---|
|Nombre|cadena|No |
|DisplayName|cadena|No |



### <a name="item"></a>Elemento


| Nombre de propiedad | Tipo de datos | Obligatorio |
|---|---|---|
|ItemInternalId|cadena|No |



### <a name="calendaritemslist"></a>CalendarItemsList


| Nombre de propiedad | Tipo de datos | Obligatorio |
|---|---|---|
|valor|matriz|No |



### <a name="calendaritem"></a>CalendarItem


| Nombre de propiedad | Tipo de datos | Obligatorio |
|---|---|---|
|ItemInternalId|cadena|No |



### <a name="contactitemslist"></a>ContactItemsList


| Nombre de propiedad | Tipo de datos | Obligatorio |
|---|---|---|
|valor|matriz|No |



### <a name="contactitem"></a>ContactItem


| Nombre de propiedad | Tipo de datos | Obligatorio |
|---|---|---|
|ItemInternalId|cadena|No |



### <a name="datasetslist"></a>DataSetsList


| Nombre de propiedad | Tipo de datos | Obligatorio |
|---|---|---|
|valor|matriz|No |



### <a name="dataset"></a>Conjunto de datos


| Nombre de propiedad | Tipo de datos | Obligatorio |
|---|---|---|
|Nombre|cadena|No |
|DisplayName|cadena|No |


## <a name="next-steps"></a>Pasos siguientes
[Crear una aplicación de lógica](../app-service-logic/app-service-logic-create-a-logic-app.md)