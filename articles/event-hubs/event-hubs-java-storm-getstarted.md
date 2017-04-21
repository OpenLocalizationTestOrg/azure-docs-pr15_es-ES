<properties
    pageTitle="Introducción a eventos Hubs en Java con Apache tormenta | Microsoft Azure"
    description="Siga este tutorial para empezar a usar Azure evento Hubs; envío de eventos con Java y recibirlos en un clúster de tormenta Apache."
    services="event-hubs"
    documentationCenter=""
    authors="fsautomata"
    manager="timlt"
    editor=""/>

<tags
    ms.service="event-hubs"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/06/2016"
    ms.author="sethm"/>

# <a name="get-started-with-event-hubs"></a>Introducción a Hubs de evento

[AZURE.INCLUDE [service-bus-selector-get-started](../../includes/service-bus-selector-get-started.md)]

## <a name="introduction"></a>Introducción

Evento Hubs es un sistema de recopilación de gran escalabilidad que puede admisión millones de eventos por segundo, habilitar una aplicación para procesar y analizar las grandes cantidades de datos creados mediante los dispositivos conectados y las aplicaciones. Una vez recopilada en Hubs de evento, puede transformar y almacenar datos con cualquier proveedor de servicios de análisis en tiempo real o clúster de almacenamiento.

Para obtener más información, consulte la [Introducción de Hubs de eventos][].

Este tutorial describe cómo recopilar los mensajes a un concentrador de eventos con una aplicación de consola en Java y recuperarlas en paralelo con Apache tormenta.

Para completar este tutorial, necesita lo siguiente:

+ Entorno de desarrollo de Java configurado para ejecutar [experto](http://maven.apache.org/). En este tutorial, se supone [Eclipse](https://www.eclipse.org/).

+ Una cuenta de Azure active. Si no tiene una cuenta, puede crear una cuenta de prueba gratuita de dos minutos. Para obtener información detallada, vea [Prueba gratuita de Azure](https://azure.microsoft.com/pricing/free-trial/).

[AZURE.INCLUDE [event-hubs-create-event-hub](../../includes/event-hubs-create-event-hub.md)]

[AZURE.INCLUDE [service-bus-event-hubs-get-started-send-java](../../includes/service-bus-event-hubs-get-started-send-java.md)]


[AZURE.INCLUDE [service-bus-event-hubs-get-started-receive-storm](../../includes/service-bus-event-hubs-get-started-receive-storm.md)]

## <a name="run-the-applications"></a>Ejecutar las aplicaciones

Ahora ya está listo para ejecutar las aplicaciones.

1.  Ejecutar la clase **LogTopology** desde Eclipse y espere a que se inicie los receptores para todas las particiones.

2.  Ejecute el proyecto de **remitente** , presione **ENTRAR** en la ventana de consola y ver los eventos que aparecen en la ventana de receptor.

    ![][22]

> [AZURE.NOTE] En este tutorial sólo use tormenta en modo local para fines de desarrollo. Vea la [Información general de tormenta HDInsight][] y la documentación de [Tormenta Apache][] oficial para obtener más información de implementaciones de tormenta y patrones.

## <a name="next-steps"></a>Pasos siguientes

Los siguientes recursos están disponibles para el desarrollo de aplicaciones de integración de evento Hubs y tormenta.

- [Analizar los datos de sensor con tormenta y HDInsight] es un tutorial escenario completo mediante Hubs de evento, tormenta y HBase para recopilar datos de sensor en un clúster de Hadoop.
- [Desarrolle aplicaciones de procesamiento de datos con SCP.NET y C# en tormenta y HDInsight de transmisión][] es un tutorial acerca de cómo escribir canalizaciones tormenta mediante C#.

<!-- Images. -->
[22]: ./media/event-hubs-java-storm-getstarted/receive-storm2.png

<!-- Links -->
[Azure classic portal]: https://manage.windowsazure.com/
[Event Processor Host]: https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost
[Información general de Hubs de evento]: event-hubs-overview.md

[Apache tormenta]: https://storm.incubator.apache.org
[Información general de tormenta HDInsight]: ../hdinsight/hdinsight-storm-overview.md
[Analizar los datos de sensor con tormenta y HDInsight]: ../hdinsight/hdinsight-storm-sensor-data-analysis.md
[Desarrollar aplicaciones de procesamiento de datos con SCP.NET y C# en tormenta y HDInsight de transmisión]: ../hdinsight/hdinsight-storm-develop-csharp-visual-studio-topology.md
 