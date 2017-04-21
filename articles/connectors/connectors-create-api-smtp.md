<properties
pageTitle="SMTP | Microsoft Azure"
description="Crear aplicaciones de lógica con el servicio de aplicación de Azure. Conectarse a SMTP para enviar correo electrónico."
services="logic-apps"   
documentationCenter=".net,nodejs,java"  
authors="msftman"   
manager="erikre"    
editor=""
tags="connectors" />

<tags
ms.service="app-service-logic"
ms.devlang="multiple"
ms.topic="article"
ms.tgt_pltfrm="na"
ms.workload="integration"
ms.date="07/15/2016"
ms.author="deonhe"/>

# <a name="get-started-with-the-smtp-connector"></a>Empezar a trabajar con el conector SMTP

Conectarse a SMTP para enviar correo electrónico.

Para usar [cualquier conector](./apis-list.md), debe crear una aplicación de lógica. Puede empezar creando [una aplicación de lógica ahora](../app-service-logic/app-service-logic-create-a-logic-app.md).

## <a name="connect-to-smtp"></a>Conectarse a SMTP

Antes de la aplicación de lógica puede tener acceso a cualquier servicio, debe crear una *conexión* al servicio. Una [conexión](./connectors-overview.md) proporciona conectividad entre una aplicación de lógica y otro servicio. Por ejemplo, para conectarse a SMTP, primero necesita una *conexión*de SMTP. Para crear una conexión, necesarias para proporcionar las credenciales que normalmente se utilizan para tener acceso al servicio que desea conectarse. Por lo tanto, en el ejemplo de SMTP, necesarias las credenciales de su nombre de conexión, la dirección del servidor SMTP y la información de inicio de sesión de usuario para crear una conexión SMTP. [Más información acerca de las conexiones]()  

### <a name="create-a-connection-to-smtp"></a>Crear una conexión a SMTP

>[AZURE.INCLUDE [Steps to create a connection to SMTP](../../includes/connectors-create-api-smtp.md)]

## <a name="use-an-smtp-trigger"></a>Use un desencadenador de SMTP

Un desencadenador es un evento que se pueden usar para iniciar el flujo de trabajo que se definen en una aplicación de lógica. [Obtenga más información acerca de desencadenadores](../app-service-logic/app-service-logic-what-are-logic-apps.md#logic-app-concepts).

En este ejemplo, porque SMTP no tiene un desencadenador de su propia, usaremos el desencadenador de **Salesforce - cuando se crea un objeto** . Este desencadenador se activará cuando se crea un nuevo objeto de Salesforce. En nuestro ejemplo, hemos deberá configurarlo tal que cada vez que se crea un nuevo cliente potencial de Salesforce, se produce una acción *Enviar correo electrónico* a través del conector SMTP con una notificación de que el nuevo cliente potencial se ha creado.

1. Escriba *salesforce* en el cuadro de búsqueda en el Diseñador de aplicaciones de lógica, a continuación, seleccione el desencadenador de **Salesforce - cuando se crea un objeto** .  
 ![](../../includes/media/connectors-create-api-salesforce/trigger-1.png)  

2. Se muestra el control **cuando se crea un objeto** .
 ![](../../includes/media/connectors-create-api-salesforce/trigger-2.png)  

3. Seleccione el **Tipo de objeto** , a continuación, seleccione el *cliente potencial* desde la lista de objetos. En este paso se indica que está creando un desencadenador que le notificará la aplicación lógica cuando se cree un nuevo cliente potencial en Salesforce.  
 ![](../../includes/media/connectors-create-api-salesforce/trigger3.png)  

4. El desencadenador se ha creado.  
 ![](../../includes/media/connectors-create-api-salesforce/trigger-4.png)  

## <a name="use-an-smtp-action"></a>Usar una acción de SMTP

Una acción es una operación que lleva a cabo por el flujo de trabajo que se definen en una aplicación de lógica. [Más información acerca de las acciones](../app-service-logic/app-service-logic-what-are-logic-apps.md#logic-app-concepts).

Ahora que ha agregado el desencadenador, siga estos pasos para agregar una acción de SMTP que se producirá cuando se crea un nuevo cliente potencial en Salesforce.

1. Seleccione **+ nuevo paso** para agregar la acción que desea realizar cuando se crea un nuevo cliente potencial.  
 ![](../../includes/media/connectors-create-api-salesforce/trigger4.png)  

2. Seleccione **Agregar una acción**. Se abre el cuadro de búsqueda donde puede buscar cualquier acción le gustaría tener.  
 ![](../../includes/media/connectors-create-api-smtp/using-smtp-action-2.png)  

3. Escriba *smtp* para buscar acciones relacionadas con SMTP.  

4. Seleccione **SMTP - enviar correo electrónico** como la acción que se realizará cuando se crea el nuevo cliente potencial. Se abre el bloque de control de acción. Tendrá que establecer la conexión de smtp en el bloque de diseñador si no ha hecho previamente.  
 ![](../../includes/media/connectors-create-api-smtp/smtp-2.png)    

5. Introduzca la información de correo electrónico que desee en el bloque de **SMTP - enviar correo electrónico** .  
 ![](../../includes/media/connectors-create-api-smtp/using-smtp-action-4.PNG)  

6. Guarde su trabajo para activar el flujo de trabajo.  

## <a name="technical-details"></a>Detalles técnicos

Estos son los detalles acerca de los desencadenadores, acciones y las respuestas que admite esta conexión:

## <a name="smtp-triggers"></a>Desencadenadores de SMTP

SMTP no tiene ningún desencadenador. 

## <a name="smtp-actions"></a>Acciones SMTP

SMTP consta de las siguientes acciones:


|Acción|Descripción|
|--- | ---|
|[Enviar correo electrónico](connectors-create-api-smtp.md#send-email)|Esta operación envía un correo electrónico a uno o más destinatarios.|

### <a name="action-details"></a>Detalles de acción

Estos son los detalles de la acción de este tipo de conector, junto con sus respuestas:


### <a name="send-email"></a>Enviar correo electrónico
Esta operación envía un correo electrónico a uno o más destinatarios. 


|Nombre de propiedad| Nombre para mostrar|Descripción|
| ---|---|---|
|Para|Para|Especificar las direcciones de correo electrónico separadas por punto y coma comorecipient1@domain.com;recipient2@domain.com|
|CC|CC|Especificar las direcciones de correo electrónico separadas por punto y coma comorecipient1@domain.com;recipient2@domain.com|
|Asunto|Asunto|Asunto del correo electrónico|
|Cuerpo|Cuerpo|Cuerpo del correo electrónico|
|De|De|Dirección de correo electrónico del remitente comosender@domain.com|
|IsHtml|Es Html|Envíe el correo electrónico como HTML (verdadero o falso)|
|CCO|CCO|Especificar las direcciones de correo electrónico separadas por punto y coma comorecipient1@domain.com;recipient2@domain.com|
|Importancia|Importancia|Importancia del correo electrónico (alta, Normal o baja)|
|ContentData|Datos de contenido de los datos adjuntos|Datos de contenido (codificado base 64 para secuencias y como-es para cadena)|
|ContentType|Tipo de contenido de los datos adjuntos|Tipo de contenido|
|ContentTransferEncoding|Codificación de transferencia de contenido de los datos adjuntos|Transferir codificación de contenido (base 64 o ninguno)|
|Nombre de archivo|Nombre de archivo de datos adjuntos|Nombre de archivo|
|ContentId|Id. de contenido de los datos adjuntos|Id. de contenido|

Un * indica que se requiere una propiedad


## <a name="http-responses"></a>Respuestas HTTP

Las acciones y desencadenadores anteriores pueden devolver uno o varios de los siguientes códigos de estado HTTP: 

|Nombre|Descripción|
|---|---|
|200|Vale|
|202|Aceptado|
|400|Solicitud incorrecta|
|401|No autorizado|
|403|Prohibido|
|404|No se encontró|
|500|Error de servidor interno. Se produjo un error desconocido.|
|predeterminado|Error en la operación.|

## <a name="next-steps"></a>Pasos siguientes
[Crear una aplicación de lógica](../app-service-logic/app-service-logic-create-a-logic-app.md)