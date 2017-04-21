<properties
    pageTitle="Introducción a Hubs de evento en C# | Microsoft Azure"
    description="Siga este tutorial para empezar a usar Azure evento Hubs; envío de eventos en C# y recibirlos en Java con la EventProcessorHost."
    services="event-hubs"
    documentationCenter=""
    authors="jtaubensee"
    manager="timlt"
    editor=""/>

<tags
    ms.service="event-hubs"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="hero-article"
    ms.date="09/27/2016"
    ms.author="jotaub;sethm"/>

# <a name="get-started-with-event-hubs"></a>Introducción a Hubs de evento

[AZURE.INCLUDE [service-bus-selector-get-started](../../includes/service-bus-selector-get-started.md)]

## <a name="introduction"></a>Introducción

Evento Hubs es un servicio que procesa grandes cantidades de datos de evento (telemetría) de aplicaciones y los dispositivos conectados. Después de recopilar datos en Hubs de evento, puede almacenar los datos mediante un clúster de almacenamiento o transformar los datos con un proveedor de servicios de análisis en tiempo real. Esta capacidad de procesamiento y la recopilación de eventos a gran escala es un componente clave de arquitecturas de aplicación moderna incluidos Internet de elementos (IoT).

Este tutorial muestra cómo usar el portal de clásico Azure para crear un concentrador de evento. También muestra cómo recopilar los mensajes a un concentrador de eventos con una aplicación de consola escrita en C# y cómo recuperarlos en paralelo utilizando la biblioteca Host de procesador de eventos de Java.

Para completar este tutorial, necesitará la siguiente:

+ [Microsoft Visual Studio](http://visualstudio.com)

+ Una cuenta de Azure active. <br/>Si no tiene una, puede crear una cuenta gratuita de dos minutos. Para obtener información detallada, vea [Prueba gratuita de Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F target="_blank").

[AZURE.INCLUDE [event-hubs-create-event-hub](../../includes/event-hubs-create-event-hub.md)]

[AZURE.INCLUDE [service-bus-event-hubs-get-started-send-csharp](../../includes/service-bus-event-hubs-get-started-send-csharp.md)]

[AZURE.INCLUDE [service-bus-event-hubs-get-started-receive-ephjava](../../includes/service-bus-event-hubs-get-started-receive-ephjava.md)]

## <a name="run-the-applications"></a>Ejecutar las aplicaciones

Ahora ya está listo para ejecutar las aplicaciones.

1.  Ejecute el proyecto **receptor** .

    ![][21]

2.  Ejecute el proyecto del **remitente** .

    ![][22]

## <a name="next-steps"></a>Pasos siguientes

Ahora que ha creado una aplicación de trabajo que se crea un concentrador de eventos y envía y recibe datos, puede mover a las situaciones siguientes:

- Una [aplicación de ejemplo que usa el evento Hubs][]completa.
- Ejemplo de [escalado de procesamiento de evento con Hubs de evento][] .
- [Información general de Hubs de evento][]

<!-- Images. -->
[21]: ./media/event-hubs-csharp-ephjava-getstarted/ephjava.png
[22]: ./media/event-hubs-csharp-ephjava-getstarted/cs-send.png

<!-- Links -->
[Azure classic portal]: https://manage.windowsazure.com/
[Información general de Hubs de evento]: event-hubs-overview.md
[aplicación de ejemplo que usa Hubs de evento]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-286fd097
[Escalar evento procesamiento con Hubs de evento]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-45f43fc3
