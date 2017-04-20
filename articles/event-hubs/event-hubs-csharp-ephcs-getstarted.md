<properties
    pageTitle="Introducción a Hubs de evento en C# | Microsoft Azure"
    description="Siga este tutorial para empezar mediante Azure evento Hubs con C# y la EventProcessorHost."
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
    ms.date="09/02/2016"
    ms.author="jotaub;sethm"/>

# <a name="get-started-with-event-hubs"></a>Introducción a Hubs de evento

[AZURE.INCLUDE [service-bus-selector-get-started](../../includes/service-bus-selector-get-started.md)]

## <a name="introduction"></a>Introducción

Evento Hubs es un servicio que procesa grandes cantidades de datos de evento (telemetría) de aplicaciones y los dispositivos conectados. Después de recopilar datos en Hubs de evento, puede almacenar los datos mediante un clúster de almacenamiento o transformar los datos con un proveedor de servicios de análisis en tiempo real. Esta capacidad de procesamiento y la recopilación de eventos a gran escala es un componente clave de arquitecturas de aplicación moderna incluidos Internet de elementos (IoT).

Este tutorial muestra cómo usar el portal de clásico Azure para crear un concentrador de evento. También muestra cómo recopilar los mensajes a un concentrador de eventos con una aplicación de consola escrita en C# y cómo recuperarlos en paralelo utilizando la biblioteca de C# [Host del procesador de eventos][] .

Para completar este tutorial, necesitará la siguiente:

+ [Microsoft Visual Studio](http://visualstudio.com)

+ Una cuenta de Azure active. Si no tiene una, puede crear una cuenta gratuita de dos minutos. Para obtener información detallada, vea [Prueba gratuita de Azure](https://azure.microsoft.com/free/).

[AZURE.INCLUDE [event-hubs-create-event-hub](../../includes/event-hubs-create-event-hub.md)]

[AZURE.INCLUDE [service-bus-event-hubs-get-started-send-csharp](../../includes/service-bus-event-hubs-get-started-send-csharp.md)]

[AZURE.INCLUDE [service-bus-event-hubs-get-started-receive-ephcs](../../includes/service-bus-event-hubs-get-started-receive-ephcs.md)]

## <a name="run-the-applications"></a>Ejecutar las aplicaciones

Ahora ya está listo para ejecutar las aplicaciones.

1. Desde dentro de Visual Studio, abra el proyecto de **receptor** que creó anteriormente.

2. Haga clic en la solución de **receptor** , a continuación, haga clic en **Agregar**y, a continuación, haga clic en **Proyecto existente**.
 
3. Busque el archivo Sender.csproj existente y luego haga doble clic en ella para agregarla a la solución.
 
4. De nuevo, haga clic en la solución **receptor** y, a continuación, haga clic en **Propiedades**. Se muestra la página de propiedades de **receptor** .

5. Haga clic en **Proyecto de inicio**y luego haga clic en el botón de **varios proyectos de inicio** . Establezca el cuadro de **acción** para el **receptor** y el **remitente** proyectos en **Inicio**.

    ![][19]

6. Haga clic en **dependencias del proyecto**. En el cuadro de **proyectos** , haga clic en el **remitente**. En el cuadro **depende** , asegúrese de que está activado el **receptor** .

    ![][20]

7. Haga clic en **Aceptar** para cerrar el cuadro de diálogo de **Propiedades** .

1.  Presione F5 para ejecutar el **receptor de** un proyecto de Visual Studio, a continuación, espere a que se inicie los receptores para todas las particiones.

    ![][21]

2.  El proyecto de **remitente** se ejecutará automáticamente. Presione **ENTRAR** en la ventana de consola y ver los eventos que aparecen en la ventana de receptor.

    ![][22]

Presione **CTRL+c** en la ventana de **remitente** para finalizar la aplicación remitente, a continuación, presione **ENTRAR** en la ventana de receptor cerrar dicha aplicación.

## <a name="next-steps"></a>Pasos siguientes

Ahora que ha creado una aplicación de trabajo que se crea un concentrador de eventos y envía y recibe datos, puede mover a las situaciones siguientes:

- Una [aplicación de ejemplo que usa el evento Hubs][]completa.
- Ejemplo de [escalado de procesamiento de evento con Hubs de evento][] .
- [Información general de Hubs de evento][]

<!-- Images. -->
[19]: ./media/event-hubs-csharp-ephcs-getstarted/create-eh-proj1.png
[20]: ./media/event-hubs-csharp-ephcs-getstarted/create-eh-proj2.png
[21]: ./media/event-hubs-csharp-ephcs-getstarted/run-csharp-ephcs1.png
[22]: ./media/event-hubs-csharp-ephcs-getstarted/run-csharp-ephcs2.png

<!-- Links -->
[Azure classic portal]: https://manage.windowsazure.com/
[Evento procesador Host]: https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost
[Información general de Hubs de evento]: event-hubs-overview.md
[aplicación de ejemplo que usa Hubs de evento]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-286fd097
[Escalar evento procesamiento con Hubs de evento]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-45f43fc3
[queued messaging solution]: ../service-bus-messaging/service-bus-dotnet-multi-tier-app-using-service-bus-queues.md
 
