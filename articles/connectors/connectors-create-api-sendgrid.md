<properties
pageTitle="SendGrid | Microsoft Azure"
description="Crear aplicaciones de lógica con el servicio de aplicación de Azure. Proveedor de conexiones SendGrid le permite enviar correo electrónico y administrar listas de destinatarios."
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

# <a name="get-started-with-the-sendgrid-connector"></a>Empezar a trabajar con el conector de SendGrid

Proveedor de conexiones SendGrid le permite enviar correo electrónico y administrar listas de destinatarios.

>[AZURE.NOTE] Esta versión de este artículo se aplica a la versión del esquema de 2015-08-01-vista previa de aplicaciones de lógica. 

Puede empezar creando una aplicación de lógica ahora, vea [crear una aplicación de lógica](../app-service-logic/app-service-logic-create-a-logic-app.md).

## <a name="triggers-and-actions"></a>Desencadenadores y acciones

El conector de SendGrid puede utilizarse como una acción; tiene desencadenadores. Todos los conectores admiten datos en formatos XML y JSON. 

 El conector de SendGrid tiene las siguientes acciones disponibles. No hay ningún desencadenador.

### <a name="sendgrid-actions"></a>Acciones de SendGrid
Puede realizar estas acciones:

|Acción|Descripción|
|--- | ---|
|[SendEmail](connectors-create-api-sendgrid.md#sendemail)|Envía un correo electrónico con la API de SendGrid (limitado a 10.000 destinatarios)|
|[AddRecipientToList](connectors-create-api-sendgrid.md#addrecipienttolist)|Agregar a un destinatario individual a una lista de destinatarios|


## <a name="create-a-connection-to-sendgrid"></a>Crear una conexión a SendGrid
Para crear aplicaciones de lógica con SendGrid, primero debe crear una **conexión** y proporcionar los detalles de las siguientes propiedades: 

|(Propiedad)| Obligatorio|Descripción|
| ---|---|---|
|ApiKey|Sí|Proporcionar la clave de la Api SendGrid|
 

>[AZURE.INCLUDE [Steps to create a connection to SendGrid](../../includes/connectors-create-api-sendgrid.md)]

>[AZURE.TIP] Puede usar esta conexión en otras aplicaciones de lógica.

Después de crear la conexión, puede usarlo para ejecutar las acciones y escuchar los desencadenadores descritos en este artículo.

## <a name="reference-for-sendgrid"></a>Referencia para SendGrid
Se aplica a la versión: 1.0

## <a name="sendemail"></a>SendEmail
Enviar correo electrónico: envía un correo electrónico usando la API de SendGrid (limitado a 10.000 destinatarios) 

```POST: /api/mail.send.json``` 

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|solicitud| |Sí|cuerpo|Ninguno|Mensaje de correo electrónico para enviar|

#### <a name="response"></a>Respuesta

|Nombre|Descripción|
|---|---|
|200|Vale|
|400|Solicitud incorrecta|
|401|No autorizado|
|403|Prohibido|
|404|No se encontró|
|429|Demasiadas solicitud|
|500|Error de servidor interno. Error desconocido|
|predeterminado|Error en la operación.|


## <a name="addrecipienttolist"></a>AddRecipientToList
Agregar a la lista: agregar un destinatario individual a una lista de destinatarios 

```POST: /v3/contactdb/lists/{listId}/recipients/{recipientId}``` 

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|listId|cadena|Sí|ruta de acceso|Ninguno|Identificador exclusivo de la lista de destinatarios|
|recipientId|cadena|Sí|ruta de acceso|Ninguno|Identificador exclusivo del destinatario|

#### <a name="response"></a>Respuesta

|Nombre|Descripción|
|---|---|
|200|Vale|
|400|Solicitud incorrecta|
|401|No autorizado|
|403|Prohibido|
|404|No se encontró|
|500|Error de servidor interno. Error desconocido|
|predeterminado|Error en la operación.|


## <a name="object-definitions"></a>Definiciones de objeto 

### <a name="emailrequest"></a>EmailRequest


| Nombre de propiedad | Tipo de datos | Obligatorio |
|---|---|---|
|De|cadena|Sí |
|FromName|cadena|No |
|Para|cadena|Sí |
|toname|cadena|No |
|Asunto|cadena|Sí |
|cuerpo|cadena|Sí |
|ishtml|valor booleano|No |
|CC|cadena|No |
|nombrecc|cadena|No |
|CCO|cadena|No |
|nombrecco|cadena|No |
|origen|cadena|No |
|fecha|cadena|No |
|encabezados|cadena|No |
|archivos|matriz|No |
|nombres de archivo|matriz|No |



### <a name="emailresponse"></a>EmailResponse


| Nombre de propiedad | Tipo de datos | Obligatorio |
|---|---|---|
|Mensaje|cadena|No |



### <a name="recipientlists"></a>RecipientLists


| Nombre de propiedad | Tipo de datos | Obligatorio |
|---|---|---|
|listas|matriz|No |



### <a name="recipientlist"></a>RecipientList


| Nombre de propiedad | Tipo de datos | Obligatorio |
|---|---|---|
|Id.|entero|No |
|nombre|cadena|No |
|recipient_count|entero|No |



### <a name="recipients"></a>Destinatarios


| Nombre de propiedad | Tipo de datos | Obligatorio |
|---|---|---|
|destinatarios|matriz|No |



### <a name="recipient"></a>Destinatario


| Nombre de propiedad | Tipo de datos | Obligatorio |
|---|---|---|
|Correo electrónico|cadena|No |
|apellido|cadena|No |
|nombre|cadena|No |
|Id.|cadena|No |


## <a name="next-steps"></a>Pasos siguientes
[Crear una aplicación de lógica](../app-service-logic/app-service-logic-create-a-logic-app.md)