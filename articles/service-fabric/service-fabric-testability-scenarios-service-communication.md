<properties
   pageTitle="Prueba: Comunicación de servicio | Microsoft Azure"
   description="Servicio de servicio de comunicación es un punto de integración crítica de una aplicación de servicio tela. Este artículo describe las consideraciones de diseño y técnicas de pruebas."
   services="service-fabric"
   documentationCenter=".net"
   authors="vturecek"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="07/06/2016"
   ms.author="vturecek"/>

# <a name="service-fabric-testability-scenarios-service-communication"></a>Escenarios de prueba de tela de servicio: servicio de comunicación

Superficie de estilos de arquitectura orientada a servicios naturalmente en tela de servicio de Azure y Microservices. En estos tipos de arquitecturas distribuidas, aplicaciones divididas en componentes microservice suelen constar de varios servicios que necesitan comunicarse entre sí. En incluso los casos más simples, generalmente tiene al menos un servicio web sin estado y un servicio de almacenamiento de datos con estado que necesitan comunicarse.

Servicio de servicio de comunicación es un punto de integración crítica de una aplicación, porque cada servicio expone una API remota a otros servicios. Trabajar con un conjunto de límites de la API que implica i/OS generalmente requiere cuidado, con una buena cantidad de prueba y validación.

Existen varias consideraciones para realizar cuando estos límites de servicio se conectan juntos en un sistema distribuido:

 - *Protocolo de transferencia*. ¿Va a utilizar HTTP para aumentar la interoperabilidad o el protocolo binario personalizado para el máximo rendimiento?
 - *Control de errores*. ¿Cómo se tratará errores permanentes o temporales? ¿Qué ocurre cuando mueve un servicio a un nodo diferente?
 - *Tiempos de espera y latencia*. ¿En las aplicaciones de varios niveles, cómo cada nivel de servicio controlará latencia por la pila y para el usuario?

Si usa uno de los componentes de comunicaciones de servicio integrado proporcionados por servicio tela o crear sus propias, pruebas de las interacciones entre los servicios es fundamental para garantizar la resistencia de la aplicación.

## <a name="prepare-for-services-to-move"></a>Prepararse para que Servicios de mover

Instancias de servicio pueden desplazarse por el tiempo. Esto es cierto cuando están configurados con métricas de carga de equilibrio personalizada óptimo de los recursos. Servicio tela mueve las instancias de servicio para maximizar su disponibilidad incluso durante las actualizaciones, migraciones tras error, escalado y otras situaciones que se producen a lo largo de un sistema distribuido.

Como servicios de desplazarse por el clúster, sus clientes y otros servicios deben estar preparados para controlar los dos escenarios cuando hablan a un servicio:

- La réplica de instancia o una partición de servicio se ha movido desde la última vez descritas en él. Esta es una parte de un ciclo de vida de servicio normal y se debe esperar que ocurrirá durante la duración de la aplicación.
- La réplica de instancia o una partición de servicio está desplazándose. Aunque de un servicio de un nodo a otro se produce error muy rápidamente en tela de servicio, puede haber un retraso en la disponibilidad si es lento iniciar el componente de comunicaciones de su servicio.

Controlar estos escenarios correctamente es importante para un sistema de funcione sin problemas. Para ello, tenga en cuenta que:

- Todos los servicios que se pueden conectar a tienen una *dirección* que escucha (por ejemplo, HTTP o WebSockets). Cuando se mueve una instancia de servicio o partición, cambia su punto final de la dirección. (Se mueve a un nodo diferente con una dirección IP diferente). Si está usando los componentes de comunicación integradas, controlará volver a resolver las direcciones del servicio para usted.
- Puede haber un aumento temporal de latencia de servicio como la instancia de servicio se inicia el detector nuevamente. Depende de cómo el servicio abre rápidamente el oyente después de mover la instancia de servicio.
- Todas las conexiones existentes tenga que cerrar y volver a abrir después de que el servicio se abre en un nuevo nodo. Reinicio o apagado correcto de nodo permite que las conexiones existentes se cierra correctamente.

### <a name="test-it-move-service-instances"></a>Pruébelo: mover instancias de servicio

Mediante herramientas de capacidad de tela de servicio, puede crear un escenario de prueba para probar diferentes maneras de estas situaciones:

1. Mover réplica principal del estado de un servicio.

    La réplica principal de una partición de servicio de estado se puede mover cualquier número de razones. Use esta opción para la réplica principal de una partición específica para ver cómo los servicios ante el movimiento de una forma muy controlada de destino.

    ```powershell

    PS > Move-ServiceFabricPrimaryReplica -PartitionId 6faa4ffa-521a-44e9-8351-dfca0f7e0466 -ServiceName fabric:/MyApplication/MyService

    ```

2. Dejar de un nodo.

    Cuando se detiene un nodo, servicio tela mueve todas las instancias de servicio o particiones que hubiera en ese nodo a uno de los otros nodos disponibles en el clúster. Use esta opción para probar una situación donde se pierde desde el clúster y todas las instancias del servicio de un nodo y réplicas en ese nodo tienen que mover.

    Puede dejar un nodo mediante el cmdlet de PowerShell **Detener ServiceFabricNode** :

    ```powershell

    PS > Restart-ServiceFabricNode -NodeName Node_1

    ```

## <a name="maintain-service-availability"></a>Mantener la disponibilidad de servicio

Como una plataforma tela de servicio está diseñada para proporcionar alta disponibilidad de los servicios. Pero, en casos extremos, problemas de infraestructura subyacentes pueden causar falta de disponibilidad. Es importante probar estas situaciones, demasiado.

Servicios con estado use un sistema basado en quórum para replicar estado para alta disponibilidad. Esto significa que un quórum de réplicas debe estar disponible para realizar operaciones de escritura. En algunas ocasiones, como un error de hardware extendido, un quórum de réplicas no esté disponible. En estos casos, no podrá realizar operaciones de escritura, pero aún podrá realizar operaciones de lectura.

### <a name="test-it-write-operation-unavailability"></a>Pruébelo: escribir la disponibilidad de operación

Con las herramientas de capacidad de prueba en tela de servicio, puede insertar un error que provoca la pérdida de quórum como una prueba. Aunque esta situación es poco comunes, es importante que los clientes y los servicios que dependen de un servicio con estado están preparados para controlar situaciones donde no pueden realizar las solicitudes de escritura a él. También es importante que el servicio de estado de esta posibilidad y correctamente puede comunicarse con las personas que llaman.

Puede provocar la pérdida de quórum mediante el cmdlet de PowerShell **Invocar ServiceFabricPartitionQuorumLoss** :

```powershell

PS > Invoke-ServiceFabricPartitionQuorumLoss -ServiceName fabric:/Myapplication/MyService -QuorumLossMode QuorumReplicas -QuorumLossDurationInSeconds 20

```

En este ejemplo, se establece `QuorumLossMode` a `QuorumReplicas` para indicar que deseamos provocar la pérdida de quórum sin interrumpir todas las réplicas. De este modo, las operaciones de lectura aún son posibles. Para probar un escenario donde no está disponible una partición completa, puede establecer este modificador en `AllReplicas`.

## <a name="next-steps"></a>Pasos siguientes

[Más información acerca de las acciones de prueba](service-fabric-testability-actions.md)

[Más información acerca de los escenarios de prueba](service-fabric-testability-scenarios.md)
