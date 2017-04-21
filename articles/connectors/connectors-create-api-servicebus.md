<properties
pageTitle="Aprenda a usar el conector de Bus de servicio de Azure en las aplicaciones de lógica | Microsoft Azure"
description="Crear aplicaciones de lógica con el servicio de aplicación de Azure. Conectarse a Azure Bus de servicio para enviar y recibir mensajes. Puede realizar acciones como enviar a cola, enviar al tema, recibir de cola y recibir de suscripción."
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
ms.date="08/02/2016"
ms.author="deonhe"/>

# <a name="get-started-with-the-azure-service-bus-connector"></a>Empezar a trabajar con el conector de Bus de servicio de Azure

Conectarse a Azure Bus de servicio para enviar y recibir mensajes. Puede realizar acciones como enviar a cola, enviar al tema, recibir de cola y recibir de suscripción.

Para usar [cualquier conector](./apis-list.md), debe crear una aplicación de lógica. Puede empezar creando [una aplicación de lógica ahora](../app-service-logic/app-service-logic-create-a-logic-app.md).

## <a name="connect-to-service-bus"></a>Conectarse a Bus de servicio

Antes de la aplicación de lógica puede tener acceso a cualquier servicio, debe crear una conexión al servicio. Una [conexión](./connectors-overview.md) proporciona conectividad entre una aplicación de lógica y otro servicio.  

>[AZURE.INCLUDE [Steps to create a connection to Azure Service Bus](../../includes/connectors-create-api-servicebus.md)]

## <a name="use-a-service-bus-trigger"></a>Utilizar un desencadenador de Bus de servicio

Un desencadenador es un evento que se pueden usar para iniciar el flujo de trabajo que se definen en una aplicación de lógica. [Obtenga más información acerca de desencadenadores](../app-service-logic/app-service-logic-what-are-logic-apps.md#logic-app-concepts).  

>[AZURE.INCLUDE [Steps to create a Service Bus trigger](../../includes/connectors-create-api-servicebus-trigger.md)]  

## <a name="use-a-service-bus-action"></a>Usar una acción de Bus de servicio

Una acción es una operación que lleva a cabo por el flujo de trabajo que se definen en una aplicación de lógica. [Más información acerca de las acciones](../app-service-logic/app-service-logic-what-are-logic-apps.md#logic-app-concepts).

[AZURE.INCLUDE [Steps to create a Service Bus action](../../includes/connectors-create-api-servicebus-action.md)]  

## <a name="technical-details"></a>Detalles técnicos

Estos son los detalles acerca de los desencadenadores, acciones y las respuestas que admite esta conexión.

### <a name="service-bus-triggers"></a>Desencadenadores de Bus de servicio

Bus de servicio tiene los siguientes desencadenadores:  

|Desencadenador | Descripción|
|--- | ---|
|[Cuando se recibe un mensaje de una cola](connectors-create-api-servicebus.md#when-a-message-is-received-in-a-queue)|Esta operación activa un flujo cuando se recibe un mensaje de una cola.|
|[Cuando se recibe un mensaje en una suscripción de tema](connectors-create-api-servicebus.md#when-a-message-is-received-in-a-topic-subscription)|Esta operación activa un flujo cuando se recibe un mensaje en una suscripción de tema.|


### <a name="service-bus-actions"></a>Acciones de Bus de servicio

Bus de servicio tiene las siguientes acciones:


|Acción|Descripción|
|--- | ---|
|[Enviar mensaje](connectors-create-api-servicebus.md#send-message)|Esta operación envía un mensaje a una cola o un tema.|
### <a name="action-and-trigger-details"></a>Detalles de acción y desencadenador

Estos son los detalles de las acciones y desencadenadores para este conector, junto con sus respuestas.



#### <a name="send-message"></a>Enviar mensaje

|Nombre de propiedad| Nombre para mostrar|Descripción|
| ---|---|---|
|ContentData *|Contenido|Contenido del mensaje.|
|ContentType|Tipo de contenido|Tipo de contenido del contenido del mensaje.|
|Propiedades|Propiedades|Pares de clave y valor para cada negociada propiedad.|
|entityName *|Nombre de cola o tema|Nombre de la cola o tema.|

Parámetros avanzados también están disponibles:

|Nombre de propiedad| Nombre para mostrar|Descripción|
| ---|---|---|
|Id. de mensaje|Id. de mensaje|Un valor definido por el usuario que puede usar Bus de servicio para identificar los mensajes duplicados, si está habilitado.|
|Para|Para|Dirección para enviar a.|
|Origen|Responder a|Dirección de la cola de responder.|
|ReplyToSessionId|Responder a ID de sesión.|Identificador de la sesión de responder.|
|Etiqueta|Etiqueta|Etiqueta específica de la aplicación.|
|ScheduledEnqueueTimeUtc|ScheduledEnqueueTimeUtc|Fecha y hora en UTC, cuando el mensaje se agregará a la cola.|
|Id. de sesión|Id. de sesión|Identificador de la sesión.|
|CorrelationId|Id. de correlación|Identificador de la correlación.|
|Período de vida|Tiempo de vida|La duración, en marcas, que el mensaje es válido. La duración se inicia desde cuando el mensaje se envía al Bus de servicio.|



Un * indica que se requiere una propiedad.


#### <a name="when-a-message-is-received-in-a-queue"></a>Cuando se recibe un mensaje de una cola

|Nombre de propiedad| Nombre para mostrar|Descripción|
| ---|---|---|
|Nombre *|Nombre de la cola|Nombre de la cola.|


Un * indica que se requiere una propiedad.


##### <a name="output-details"></a>Detalles de salida

ServiceBusMessage: Este objeto tiene el contenido y las propiedades de un mensaje de Bus de servicio.


| Nombre de propiedad | Tipo de datos | Descripción |
|---|---|---|
|ContentData|cadena|Contenido del mensaje.|
|ContentType|cadena|Tipo de contenido del contenido del mensaje.|
|Propiedades|objeto|Pares de clave y valor para cada negociada propiedad.|
|Id. de mensaje|cadena|Un valor definido por el usuario que puede usar Bus de servicio para identificar los mensajes duplicados, si está habilitado.|
|Para|cadena|Enviar a dirección.|
|Origen|cadena|Dirección de la cola de responder.|
|ReplyToSessionId|cadena|Identificador de la sesión de responder.|
|Etiqueta|cadena|Etiqueta específica de la aplicación.|
|ScheduledEnqueueTimeUtc|cadena|Fecha y hora en UTC, cuando el mensaje se agregará a la cola.|
|Id. de sesión|cadena|Identificador de la sesión.|
|CorrelationId|cadena|Identificador de la correlación.|
|Período de vida|cadena|La duración, en marcas, que el mensaje es válido. La duración se inicia desde cuando el mensaje se envía al Bus de servicio.|




#### <a name="when-a-message-is-received-in-a-topic-subscription"></a>Cuando se recibe un mensaje en una suscripción de tema

|Nombre de propiedad| Nombre para mostrar|Descripción|
| ---|---|---|
|topicName *|Nombre del tema|Nombre del tema.|
|subscriptionName *|Nombre de la suscripción de tema|Nombre de la suscripción de tema.|


Un * indica que se requiere una propiedad.


##### <a name="output-details"></a>Detalles de salida

ServiceBusMessage: Este objeto tiene el contenido y las propiedades de un mensaje de Bus de servicio.


| Nombre de propiedad | Tipo de datos | Descripción |
|---|---|---|
|ContentData|cadena|Contenido del mensaje.|
|ContentType|cadena|Tipo de contenido del contenido del mensaje.|
|Propiedades|objeto|Pares de clave y valor para cada negociada propiedad.|
|Id. de mensaje|cadena|Un valor definido por el usuario que puede usar Bus de servicio para identificar los mensajes duplicados, si está habilitado.|
|Para|cadena|Enviar a dirección.|
|Origen|cadena|Dirección de la cola de responder.|
|ReplyToSessionId|cadena|Identificador de la sesión de responder.|
|Etiqueta|cadena|Etiqueta específica de la aplicación.|
|ScheduledEnqueueTimeUtc|cadena|Fecha y hora en UTC, cuando el mensaje se agregará a la cola.|
|Id. de sesión|cadena|Identificador de la sesión.|
|CorrelationId|cadena|Identificador de la correlación.|
|Período de vida|cadena|La duración, en marcas, que el mensaje es válido. La duración se inicia desde cuando el mensaje se envía al Bus de servicio.|



### <a name="http-responses"></a>Respuestas HTTP

Las acciones y desencadenadores anterior pueden devolver uno o varios de los siguientes códigos de estado HTTP:

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
[Crear una aplicación de lógica](../app-service-logic/app-service-logic-create-a-logic-app.md).
