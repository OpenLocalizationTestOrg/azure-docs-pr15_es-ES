<properties
   pageTitle="Diagnósticos de servicios confiables con estado | Microsoft Azure"
   description="Funcionalidad de diagnóstico para los servicios de confiable con estado"
   services="service-fabric"
   documentationCenter=".net"
   authors="AlanWarwick"
   manager="timlt"
   editor=""/>

<tags
   ms.service="Service-Fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="05/17/2016"
   ms.author="alanwar"/>

# <a name="diagnostic-functionality-for-stateful-reliable-services"></a>Funcionalidad de diagnóstico para los servicios de fiable con estado
La clase con estado confiable servicios StatefulServiceBase emite los eventos de [origen de eventos](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource.aspx) que se pueden usar para el servicio de depuración, proporcionan información sobre cómo funciona el tiempo de ejecución y ayudar a solucionar problemas.

## <a name="eventsource-events"></a>Eventos de origen de eventos
El nombre de origen de eventos de la clase con estado confiable servicios StatefulServiceBase es "Servicios de ServiceFabric de Microsoft". Eventos de este origen de evento aparecen en la ventana de [Eventos de diagnósticos](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md#view-service-fabric-system-events-in-visual-studio) cuando el servicio se está [Depurar en Visual Studio](service-fabric-debugging-your-application.md).

Ejemplos de herramientas y tecnologías de ayuda en recopilar y ver los eventos de origen de eventos son [PerfView](http://www.microsoft.com/download/details.aspx?id=28567), [Diagnósticos de Microsoft Azure](../cloud-services/cloud-services-dotnet-diagnostics.md)y la [Biblioteca de Microsoft TraceEvent](http://www.nuget.org/packages/Microsoft.Diagnostics.Tracing.TraceEvent).

## <a name="events"></a>Eventos

|Nombre del evento|Id. de evento|Nivel|Descripción del evento|
|----------|--------|-----|-----------------|
|StatefulRunAsyncInvocation|1|Informativo|Cuando se inicia la tarea de servicio RunAsync a emitir|
|StatefulRunAsyncCancellation|2|Informativo|Emitir cuando se ha cancelado la tarea de RunAsync de servicio|
|StatefulRunAsyncCompletion|3|Informativo|Cuando se complete la tarea de servicio RunAsync a emitir|
|StatefulRunAsyncSlowCancellation|4|Advertencia|Emite cuando la tarea de servicio RunAsync requiere demasiado tiempo para completar la cancelación|
|StatefulRunAsyncFailure|5|Error|Emite al servicio RunAsync tarea produce una excepción|

## <a name="interpret-events"></a>Interpretar los eventos

Eventos StatefulRunAsyncInvocation, StatefulRunAsyncCompletion y StatefulRunAsyncCancellation son útiles para el escritor de servicio para comprender el ciclo de vida de un servicio, así como los intervalos para cuando un servicio se inicia, cancelado o completado. Esto puede ser útil al depurar problemas de servicio o entender el ciclo de vida de servicio.

Editores del servicio deben preste atención a los eventos StatefulRunAsyncSlowCancellation y StatefulRunAsyncFailure porque indican problemas con el servicio.

StatefulRunAsyncFailure se emite cada vez que la tarea de RunAsync() servicio inicia una excepción. Normalmente, una excepción indica un error o un error en el servicio. Además, la excepción hace que el servicio no se realice correctamente, por lo que se mueve a un nodo diferente. Esto puede ser una operación cara y puede retrasar las solicitudes entrantes mientras se mueve el servicio. Editores del servicio deben determinar la causa de la excepción y, si es posible, la mitigar.

StatefulRunAsyncSlowCancellation se emite siempre una solicitud de cancelación de la tarea de RunAsync tarda más de cuatro segundos. Cuando un servicio tarda mucho tiempo para completar la cancelación, afecta a la capacidad de reiniciar rápidamente en otro nodo el servicio. Esto puede afectar a la disponibilidad general del servicio.
