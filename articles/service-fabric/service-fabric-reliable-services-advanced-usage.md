<properties
   pageTitle="Uso de servicios confiables avanzado | Microsoft Azure"
   description="Obtenga información sobre uso avanzado del servicio tela servicios fiables para una mayor flexibilidad en sus servicios."
   services="Service-Fabric"
   documentationCenter=".net"
   authors="vturecek"
   manager="timlt"
   editor="masnider"/>

<tags
   ms.service="Service-Fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="10/19/2016"
   ms.author="vturecek"/>

# <a name="advanced-usage-of-the-reliable-services-programming-model"></a>Uso de los servicios fiable modelo de programación avanzado
Azure tela servicio simplifica escribir y administración de servicios y sin estado confiables. Esta guía hablar de usos avanzados de servicios confiables para tener más flexibilidad y control sobre los servicios. Antes de leer a esta guía, familiarícese con [El modelo de programación de servicios confiable](service-fabric-reliable-services-introduction.md).

Servicios de estado y sin estado tienen dos puntos de entrada principal para el código de usuario:

 - `RunAsync`es un punto de entrada de propósito general para el código de servicio.
 - `CreateServiceReplicaListeners`y `CreateServiceInstanceListeners` es para abrir escucha de comunicación para las solicitudes de cliente.
 
La mayoría de los servicios, estos puntos de dos entrada son suficientes. En algunos casos cuando es necesario, tener más control sobre el ciclo de vida de un servicio eventos de ciclo de vida adicionales están disponibles.

## <a name="stateless-service-instance-lifecycle"></a>Ciclo de vida de instancia de servicio de estado

Ciclo de vida de un servicio independiente es muy sencillo. Un servicio independiente sólo se abre, cerrado o anulado. `RunAsync`en un servicio de estado se ejecuta cuando una instancia de servicio se abre y se ha cancelado cuando está cerrada o se ha cancelado una instancia de servicio. 

Aunque `RunAsync` debe ser suficiente en casi todos los casos, abrir, cerrar y eventos de anulación en un servicio de estado también están disponibles:

- `Task OnOpenAsync(IStatelessServicePartition, CancellationToken)`
  OnOpenAsync se llama cuando la instancia de servicio de estado se va a utilizar. Tareas de inicialización de servicio ampliado se pueden iniciar en este momento.

- `Task OnCloseAsync(CancellationToken)`
  Cuando la instancia de estado del servicio va a ser cerrar correctamente, se denomina OnCloseAsync. Esto puede ocurrir cuando se está actualizando del código del servicio, se mueve la instancia de servicio debido a Equilibrio de carga o se detecta un error transitorio. OnCloseAsync puede usarse para cerrar todos los recursos, detener el procesamiento de fondo, terminar de guardar el estado externo o cerrar conexiones existentes de forma segura.

- `void OnAbort()`
  Cuando la instancia de servicio sin estado se fuerza cierra, se denomina OnAbort. Generalmente se denomina cuando se detecta un error permanente en el nodo, o cuando tela de servicio sujeto no puede administrar el ciclo de vida de la instancia de servicio debido a errores internos.

## <a name="stateful-service-replica-lifecycle"></a>Ciclo de vida de réplica de estado del servicio

Ciclo de vida de una réplica de estado del servicio es mucho más compleja que una instancia de estado del servicio. Además para abrir, cerrar y anular eventos, una réplica de servicio con estado realiza cambios de función durante su duración. Cuando una réplica de estado del servicio cambia la función, el `OnChangeRoleAsync` se activa el evento:

- `Task OnChangeRoleAsync(ReplicaRole, CancellationToken)`
  Cuando la réplica de estado del servicio está cambiando la función, por ejemplo a primaria o secundaria, se denomina OnChangeRoleAsync. Réplicas principales se les asigna estado de escritura (se permiten crear y escribir a colecciones confiable). Réplicas secundarias reciben el estado de lectura (solo puede leer de colecciones de confiable existente). La mayoría de trabajo en un servicio de estado se realiza en la réplica principal. Validación de sólo lectura, generación de informes, minería de datos u otros trabajos de sólo lectura, pueden realizar réplicas secundarias.

En un servicio de estado, solo la réplica principal tenga acceso de escritura en estado y, por tanto, suele si el servicio está realizando trabajo real. La `RunAsync` método en un servicio de estado se ejecuta solo cuando la réplica de estado del servicio es principal. La `RunAsync` método se cancela cuando se cambia el rol de la réplica principal lejos principal, así como durante los eventos de cerrar y anulación. 

Uso de la `OnChangeRoleAsync` evento le permite realizar el trabajo según la función de réplica, así como en respuesta a cambiar de la función.

Un servicio con estado también proporciona los mismos eventos de ciclo de vida de cuatro como servicio independiente, con la misma semántica y casos de uso:

- `Task OnOpenAsync(IStatefulServicePartition, CancellationToken)`
- `Task OnCloseAsync(CancellationToken)`
- `void OnAbort()`



## <a name="next-steps"></a>Pasos siguientes
Para temas más avanzados relacionados con tela de servicio, consulte los siguientes artículos:

- [Configurar servicios confiables con estado](service-fabric-reliable-services-configuration.md)

- [Introducción de estado del servicio tela](service-fabric-health-introduction.md)

- [Usar informes de estado del sistema para solucionar este problema](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)

- [Configuración de los servicios con el Administrador de recursos de clúster tela de servicio](service-fabric-cluster-resource-manager-configure-services.md)
