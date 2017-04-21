<properties
    pageTitle="Introducción a Hubs de evento en C | Microsoft Azure"
    description="Siga este tutorial para empezar a usar Azure evento Hubs; envío de eventos en C y recibirlos en Java con el Host de procesador de eventos."
    services="event-hubs"
    documentationCenter=""
    authors="jtaubensee"
    manager="timlt"
    editor=""/>

<tags
    ms.service="event-hubs"
    ms.workload="na"
    ms.tgt_pltfrm="c"
    ms.devlang="csharp"
    ms.topic="article"
    ms.date="09/27/2016"
    ms.author="jotaub;sethm"/>

# <a name="get-started-with-event-hubs"></a>Introducción a Hubs de evento

[AZURE.INCLUDE [service-bus-selector-get-started](../../includes/service-bus-selector-get-started.md)]

## <a name="introduction"></a>Introducción

Evento Hubs es un sistema de recopilación de gran escalabilidad que puede admisión millones de eventos por segundo, habilitar una aplicación para procesar y analizar las grandes cantidades de datos creados mediante los dispositivos conectados y las aplicaciones. Una vez recopilada en Hubs de evento, puede transformar y almacenar datos con cualquier proveedor de servicios de análisis en tiempo real o clúster de almacenamiento.

Para obtener más información, vea la [información general de Hubs de evento][].

En este tutorial, aprenderá a recopilación de mensajes en un concentrador de evento en una aplicación de consola C y recuperarlos en paralelo utilizando la biblioteca de C# [Host del procesador de eventos][] .

Para poder completar este tutorial necesita lo siguiente:

+ Un entorno de desarrollo C. Para este tutorial, consideraremos la pila gcc en una [Máquina virtual Linux de Azure](../virtual-machines/virtual-machines-linux-quick-create-cli.md) con Ubuntu 14.04. Se proporcionará instrucciones para otros entornos de vínculos externos.

+ Microsoft Visual Studio Express para Windows

+ Una cuenta de Azure active. <br/>Si no tiene una cuenta, puede crear una cuenta gratuita de dos minutos. Para obtener información detallada, vea <a href="http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F" target="_blank">Prueba gratuita de Azure</a>.

[AZURE.INCLUDE [event-hubs-create-event-hub](../../includes/event-hubs-create-event-hub.md)]

[AZURE.INCLUDE [service-bus-event-hubs-get-started-send-c](../../includes/service-bus-event-hubs-get-started-send-c.md)]

[AZURE.INCLUDE [service-bus-event-hubs-get-started-receive-ephjava](../../includes/service-bus-event-hubs-get-started-receive-ephjava.md)]

## <a name="run-the-applications"></a>Ejecutar las aplicaciones

Ahora ya está listo para ejecutar las aplicaciones.

1.  Ejecute el proyecto **receptor** .

    ![][21]

2.  Ejecutar el programa de **remitente** y observe los eventos que aparecen en la ventana de receptor.

    ![][24]

## <a name="next-steps"></a>Pasos siguientes

Ahora que ha creado una aplicación de trabajo que se crea un concentrador de eventos y envía y recibe datos, puede mover a las situaciones siguientes:

- Una [aplicación de ejemplo que usa el evento Hubs][]completa.
- Ejemplo de [escalado de procesamiento de evento con Hubs de evento][] .
- [Información general de Hubs de evento][]

<!-- Images. -->
[21]: ./media/event-hubs-c-ephjava-getstarted/ephjava.png
[24]: ./media/event-hubs-c-ephjava-getstarted/receive-eph-c.png

<!-- Links -->
[Azure classic portal]: https://manage.windowsazure.com/
[Evento procesador Host]: https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost
[Información general de Hubs de evento]: event-hubs-overview.md
[aplicación de ejemplo que usa Hubs de evento]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-286fd097
[Escalar evento procesamiento con Hubs de evento]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-45f43fc3
