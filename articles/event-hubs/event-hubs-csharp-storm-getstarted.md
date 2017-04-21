<properties
    pageTitle="Introducción a Hubs de evento en C# con Apache tormenta | Microsoft Azure"
    description="Siga este tutorial para empezar a usar Azure evento Hubs; envío de eventos en C# y recibirlos en un clúster de tormenta Apache."
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
    ms.topic="article" 
    ms.date="09/06/2016"
    ms.author="jotaub;sethm"/>

# <a name="get-started-with-event-hubs"></a>Introducción a Hubs de evento

[AZURE.INCLUDE [service-bus-selector-get-started](../../includes/service-bus-selector-get-started.md)]

## <a name="introduction"></a>Introducción

Evento Hubs es un sistema de recopilación de gran escalabilidad que puede admisión millones de eventos por segundo, habilitar una aplicación para procesar y analizar las grandes cantidades de datos creados mediante los dispositivos conectados y las aplicaciones. Una vez recopilada en Hubs de evento, puede transformar y almacenar datos con cualquier proveedor de servicios de análisis en tiempo real o clúster de almacenamiento.

Para obtener más información, vea la [información general de Hubs de evento].

En este tutorial, aprenderá a recopilación de mensajes en un concentrador de eventos con una aplicación de consola en C# y recuperarlos en paralelo con Apache tormenta.

Para poder completar este tutorial necesita lo siguiente:

+ [Microsoft Visual Studio](http://visualstudio.com)

+ Entorno de desarrollo de Java configurado para ejecutar [experto](http://maven.apache.org/). Para este tutorial, consideraremos [Eclipse](https://www.eclipse.org/)

+ Una cuenta de Azure active. <br/>Si no tiene una cuenta, puede crear una cuenta gratuita de dos minutos. Para obtener información detallada, vea <a href="http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F" target="_blank">Prueba gratuita de Azure</a>.

[AZURE.INCLUDE [event-hubs-create-event-hub](../../includes/event-hubs-create-event-hub.md)]

[AZURE.INCLUDE [service-bus-event-hubs-get-started-send-csharp](../../includes/service-bus-event-hubs-get-started-send-csharp.md)]


[AZURE.INCLUDE [service-bus-event-hubs-get-started-receive-storm](../../includes/service-bus-event-hubs-get-started-receive-storm.md)]

## <a name="run-the-applications"></a>Ejecutar las aplicaciones

Ahora ya está listo para ejecutar las aplicaciones.

1.  Ejecutar la clase **LogTopology** desde Eclipse y espere a que se inicie los receptores para todas las particiones.

2.  Ejecute el proyecto de **remitente** , presione **ENTRAR** en la ventana de consola y ver los eventos que aparecen en la ventana de receptor.

    ![][22]

## <a name="next-steps"></a>Pasos siguientes

Ahora que ha creado una aplicación de trabajo que se crea un concentrador de eventos y envía y recibe datos, puede mover a las situaciones siguientes:

- Una [aplicación de ejemplo que usa el evento Hubs][]completa.
- Ejemplo de [escalado de procesamiento de evento con Hubs de evento][] .

<!-- Images. -->
[22]: ./media/event-hubs-csharp-storm-getstarted/receive-storm1.png

<!-- Links -->
[Azure classic portal]: https://manage.windowsazure.com/
[Información general de Hubs de evento]: event-hubs-overview.md
[aplicación de ejemplo que usa Hubs de evento]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-286fd097
[Escalar evento procesamiento con Hubs de evento]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-45f43fc3
 