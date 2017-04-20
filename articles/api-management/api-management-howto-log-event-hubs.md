<properties 
    pageTitle="Cómo iniciar eventos en Azure evento Hubs en administración de la API de Azure | Microsoft Azure" 
    description="Obtenga información sobre cómo iniciar eventos en Azure evento Hubs en administración de la API de Azure." 
    services="api-management" 
    documentationCenter="" 
    authors="steved0x" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="api-management" 
    ms.workload="mobile" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/25/2016" 
    ms.author="sdanie"/>

# <a name="how-to-log-events-to-azure-event-hubs-in-azure-api-management"></a>Cómo iniciar eventos en Azure evento Hubs en administración de la API de Azure

Azure Hubs de evento es un servicio de entrada de datos de gran escalabilidad que puede recopilar millones de eventos por segundo para que pueda procesar y analizar el grandes cantidades de datos creados mediante los dispositivos conectados y las aplicaciones. Evento Hubs actúa como la "puerta principal" para la canalización de un evento, y una vez que se recopilan los datos a un concentrador de evento, se pueden transformar y almacenar con cualquier proveedor de análisis en tiempo real o adaptadores de almacenamiento por lotes. Evento Hubs separa la producción de una secuencia de eventos desde el consumo de esos eventos, para que los consumidores de eventos pueden tener acceso a los eventos en su propia programación.

En este artículo es un complemento para el vídeo de [Integrar Azure API administración con Hubs de evento](https://azure.microsoft.com/documentation/videos/integrate-azure-api-management-with-event-hubs/) y se describe cómo registrar eventos de administración de la API mediante Hubs de evento de Azure.

## <a name="create-an-azure-event-hub"></a>Crear un concentrador de evento de Azure

Para crear un nuevo evento de concentrador, inicie sesión en el [portal de clásica Azure](https://manage.windowsazure.com) y haga clic en **nuevo**->**Servicios de aplicación**->**Bus de servicio**->**Concentrador de evento**->**Crear rápido**. Escriba un nombre de evento concentrador, región, seleccione una suscripción y un espacio de nombres. Si todavía no lo ha creado previamente un espacio de nombres puede crearla escribiendo un nombre en el cuadro de texto de **Namespace** . Una vez que se haya configurado todas las propiedades, haga clic en **crear un nuevo evento concentrador** para crear el Hub de evento.

![Crear concentrador de evento][create-event-hub]

A continuación, vaya a la ficha **Configurar** para su centro de evento nuevo y crear dos **directivas de acceso compartido**. Asigne un nombre a la primera **Enviar** y asígnele permisos de **envío** .

![Directiva de envío][sending-policy]

Nombre el segundo **recibir**, asígnele permisos **escuchar** y haga clic en **Guardar**.

![Directiva de recepción][receiving-policy]

Cada directiva de acceso compartido permite a las aplicaciones enviar y recibir eventos a y desde el Hub de evento. Para obtener acceso a las cadenas de conexión para estas directivas, vaya a la pestaña **panel** del concentrador de evento y haga clic en **información de conexión**.

![Cadena de conexión][event-hub-dashboard]

Se usa la cadena de conexión de **Enviar** al registro de eventos y se usa la cadena de conexión **recibir** al descargar eventos desde el Hub de evento.

![Cadena de conexión][event-hub-connection-string]

## <a name="create-an-api-management-logger"></a>Crear un registrador de administración de la API

Ahora que tiene un concentrador de evento, el siguiente paso es configurar un [registrador](https://msdn.microsoft.com/library/azure/mt592020.aspx) en el servicio de administración de la API de modo que pueden iniciar eventos en el Hub de evento.

Registradores de administración de la API están configurados con la [API de REST API de administración](http://aka.ms/smapi). Antes de utilizar la API de REST por primera vez, revise los [requisitos previos](https://msdn.microsoft.com/library/azure/dn776326.aspx#Prerequisites) y asegurarse de que tiene [habilitado el acceso a la API de REST](https://msdn.microsoft.com/library/azure/dn776326.aspx#EnableRESTAPI).

Para crear un registrador, realizar una solicitud de HTTP poner con la plantilla de dirección URL siguiente.

    https://{your service}.management.azure-api.net/loggers/{new logger name}?api-version=2014-02-14-preview

-   Reemplazar `{your service}` con el nombre de la instancia de servicio de administración de la API.
-   Reemplazar `{new logger name}` con el nombre deseado para el registrador de nuevo. Se hace referencia a este nombre al configurar la directiva de [registro para eventhub](https://msdn.microsoft.com/library/azure/dn894085.aspx#log-to-eventhub)

Agregue los encabezados siguientes a la solicitud.

-   Tipo de contenido: aplicación/json
-   Autorización: SharedAccessSignature uid =...
    -   Para obtener instrucciones sobre cómo generar el `SharedAccessSignature` vea [Autenticación de API de REST de Azure API administración](https://msdn.microsoft.com/library/azure/dn798668.aspx).

Especifique el cuerpo de la solicitud con la plantilla siguiente.

    {
      "type" : "AzureEventHub",
      "description" : "Sample logger description",
      "credentials" : {
        "name" : "Name of the Event Hub from the Azure Classic Portal",
        "connectionString" : "Endpoint=Event Hub Sender connection string"
        }
    }

-   `type`debe estar establecida en `AzureEventHub`.
-   `description`Proporciona una descripción opcional del registrador y puede ser una cadena de longitud cero, si lo desea.
-   `credentials`contiene el `name` y `connectionString` de su centro de evento de Azure.

Cuando realice la solicitud, si el registrador se crea un código de estado de `201 Created` se devuelve. 

>[AZURE.NOTE] Para otras posibles códigos devueltos y sus razones, vea [crear un registrador](https://msdn.microsoft.com/library/azure/mt592020.aspx#PUT). Para ver cómo realizar otras operaciones como lista, actualizar y eliminar, consulte la documentación de la entidad de [registrador](https://msdn.microsoft.com/library/azure/mt592020.aspx) .

## <a name="configure-log-to-eventhubs-policies"></a>Configurar las directivas de registro para eventhubs

Una vez configurada su registrador en administración de la API, puede configurar las directivas de registro a eventhubs para registrar los eventos que desee. La directiva de registro para eventhubs puede usarse en la sección de directiva de entrada o en la sección de directiva saliente.

Para configurar directivas, inicie sesión en el [portal de clásica Azure](https://manage.windowsazure.com), vaya a su servicio de administración de la API y haga clic en **el portal de publisher** o **Administrar** para acceder al portal de publisher.

![Portal de Publisher][publisher-portal]

Haga clic en **directivas** en el menú de administración de la API de la izquierda, seleccione el producto deseado y API y haga clic en **Agregar directiva**. En este ejemplo agregamos una directiva a la **API de eco** en el producto **ilimitado** .

![Agregar directiva][add-policy]

Coloque el cursor en el `inbound` directiva de sección y haga clic en la directiva de **registro de EventHub** para insertar la `log-to-eventhub` plantilla de la declaración de directiva.

![Editor de directivas][event-hub-policy]

    <log-to-eventhub logger-id ='logger-id'>
      @( string.Join(",", DateTime.UtcNow, context.Deployment.ServiceName, context.RequestId, context.Request.IpAddress, context.Operation.Name))
    </log-to-eventhub>

Reemplazar `logger-id` con el nombre del registrador de administración de la API que configuró en el paso anterior.

Puede usar cualquier expresión que devuelve una cadena como el valor de la `log-to-eventhub` elemento. En este ejemplo se registra una cadena que contiene la fecha y hora, nombre de servicio, el identificador de solicitud, dirección ip de solicitud y nombre de la operación.

Haga clic en **Guardar** para guardar la configuración de directiva actualizada. En cuanto se guarda la directiva está activa y eventos se registran en el Hub de evento designado.

## <a name="next-steps"></a>Pasos siguientes

-   Obtenga más información sobre Hubs de evento de Azure
    -   [Introducción a Azure evento Hubs](../event-hubs/event-hubs-csharp-ephcs-getstarted.md)
    -   [Recibir mensajes con EventProcessorHost](../event-hubs/event-hubs-csharp-ephcs-getstarted.md#receive-messages-with-eventprocessorhost)
    -   [Guía de programación de Hubs de evento](../event-hubs/event-hubs-programming-guide.md)
-   Obtenga más información sobre la integración de administración de la API y Hubs de evento
    -   [Referencia de la entidad de registrador](https://msdn.microsoft.com/library/azure/mt592020.aspx)
    -   [referencia de la directiva de registro para eventhub](https://msdn.microsoft.com/library/azure/dn894085.aspx#log-to-eventhub)
    -   [Supervisar la API con la administración de la API de Azure, Hubs de evento y Runscope](api-management-log-to-eventhub-sample.md)    

## <a name="watch-a-video-walkthrough"></a>Ver un tutorial de vídeo

> [AZURE.VIDEO integrate-azure-api-management-with-event-hubs]


[publisher-portal]: ./media/api-management-howto-log-event-hubs/publisher-portal.png
[create-event-hub]: ./media/api-management-howto-log-event-hubs/create-event-hub.png
[event-hub-connection-string]: ./media/api-management-howto-log-event-hubs/event-hub-connection-string.png
[event-hub-dashboard]: ./media/api-management-howto-log-event-hubs/event-hub-dashboard.png
[receiving-policy]: ./media/api-management-howto-log-event-hubs/receiving-policy.png
[sending-policy]: ./media/api-management-howto-log-event-hubs/sending-policy.png
[event-hub-policy]: ./media/api-management-howto-log-event-hubs/event-hub-policy.png
[add-policy]: ./media/api-management-howto-log-event-hubs/add-policy.png






