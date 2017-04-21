<properties
   pageTitle="Arquitectura de servicio confiable | Microsoft Azure"
   description="Información general sobre la arquitectura de servicio confiable para los servicios con estado y sin estado"
   services="service-fabric"
   documentationCenter=".net"
   authors="AlanWarwick"
   manager="timlt"
   editor="vturecek"/>

<tags
   ms.service="Service-Fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="03/30/2016"
   ms.author="alanwar"/>

# <a name="architecture-for-stateful-and-stateless-reliable-services"></a>Arquitectura de servicios confiables con estado y sin estado

Puede ser un servicio confiable de Azure servicio tela con estado o sin estado. Cada tipo de servicio se ejecuta en una arquitectura concreta. Estas arquitecturas se describen en este artículo.
Vea la [información general del servicio confiable](service-fabric-reliable-services-introduction.md) para obtener más información acerca de las diferencias entre los servicios con estado y sin estado.

## <a name="stateful-reliable-services"></a>Servicios confiables con estado

### <a name="architecture-of-a-stateful-service"></a>Arquitectura de un servicio de estado
![Diagrama de la arquitectura de un servicio de estado](./media/service-fabric-reliable-services-platform-architecture/reliable-stateful-service-architecture.png)

### <a name="stateful-reliable-service"></a>Servicio confiable con estado

Un servicio confiable con estado puede derivan de clase de la StatefulService o StatefulServiceBase. Ambas clases bases proporciona servicio tela. Ofrecen distintos niveles de soporte técnico y abstracción para el servicio con estado para interactuar con el servicio tela--y participar como un servicio en el clúster de servicio tela.

StatefulService se deriva de StatefulServiceBase. StatefulServiceBase ofrece servicios más flexibilidad, pero requiere más conocimiento por el interior del servicio tela.
Para obtener más información sobre las características de escritura de servicios con las clases StatefulService y StatefulServiceBase, consulte la [Introducción de un servicio confiable](service-fabric-reliable-services-introduction.md) y el [uso avanzado de servicio confiable](service-fabric-reliable-services-advanced-usage.md) .

Ambas clases base administran la duración y la función de la implementación del servicio. La implementación del servicio puede reemplazar métodos virtuales de cualquier clase base si la aplicación de servicio tiene trabajo que hacer en los puntos del ciclo de vida de implementación de servicio, o si desea crear un objeto de escucha de comunicaciones. Tenga en cuenta que aunque una implementación de servicio puede implementar su propio objeto de escucha de comunicación exponer ICommunicationListener, en el diagrama anterior, la escucha de comunicación se implementa tela de servicio, como la implementación de servicio utiliza una escucha de comunicaciones que se implementa mediante el servicio tela.

Un servicio confiable con estado utiliza el Administrador de estado confiable para aprovechar las ventajas de colecciones fiables. Colecciones fiables son estructuras de datos locales altamente disponibles para el servicio, que es, siempre están disponibles, independientemente de migraciones tras error del servicio. Cada tipo de colección confiable se ha implementado por un proveedor de estado confiable.
Para obtener más información sobre colecciones confiables, vea la [información general sobre colecciones confiable](service-fabric-reliable-services-reliable-collections.md).

### <a name="reliable-state-manager-and-state-providers"></a>Administrador de estado confiable y proveedores de estado

El Administrador de estado confiable es el objeto que administra proveedores de estado confiable. Tiene la funcionalidad para crear, eliminar, enumerar y asegúrese de que los proveedores de estado confiable son persistentes y altamente disponibles. Una instancia de proveedor de estado confiable representa una instancia de una estructura de datos persistentes y altamente disponibles, como un diccionario o una cola.

Cada proveedor de estado confiable expone una interfaz que se utiliza un servicio de estado para interactuar con el proveedor de estado fiable. Por ejemplo, se utiliza IReliableDictionary para interactuar con el diccionario confiable, mientras que IReliableQueue se utiliza para interactuar con la cola confiable. Todos los proveedores de estado confiable implementar la interfaz IReliableState.

El Administrador de estado confiable tiene una interfaz denominada IReliableStateManager, que permite el acceso a él desde un servicio de estado. Interfaces de los proveedores de estado confiable se devuelven a través de IReliableStateManager.

El Administrador de estado confiable utiliza una arquitectura de complemento para que los nuevos tipos de colecciones confiables se pueden conectar dinámicamente.

El diccionario confiable y cola confiable se basan en la implementación de una tienda diferencial de alto rendimiento, con control de versiones.

### <a name="transactional-replicator"></a>Transacciones replicator

El componente de transacciones replicator es responsable de garantizar que el estado de un servicio (es decir, el estado en el Administrador de estado confiable y las colecciones de confiables) es coherente en todas las réplicas que se ejecuta el servicio. También se asegura de que el estado se conserva en el registro. Las interfaces de administrador de estado confiable con el duplicador transacciones a través de un mecanismo privado.

El replicador transacciones usa un protocolo de red para comunicar el estado con otras réplicas de la instancia de servicio para que todas las réplicas tienen información de estado actualizada.

El replicador transacciones usa un registro para almacenar la información de estado para que la información de estado supera el proceso o se bloquea el nodo. La interfaz en el registro es a través de un mecanismo privado.

### <a name="log"></a>Registro

El componente de registro proporciona un almacén persistente de alto rendimiento que se puede optimizar para grabar discos giratoria o sólidos.  El diseño del registro es para el almacenamiento persistente (es decir, el disco duro) local a los nodos que se estén ejecutando el servicio de estado. Esto permite baja latencia y alto rendimiento, en comparación con almacenamiento persistente remoto, que no es el nodo local.

El componente de registro usa varios archivos de registro. Hay un archivo de registro compartido de todo el nodo que todas las réplicas usar como puede proporcionar la latencia menor y mayor rendimiento para almacenar los datos de estado. De forma predeterminada el registro compartido se coloca en el directorio de trabajo de nodo de tela de servicio, pero también se puede configurar para colocar en otra ubicación, lo ideal es que en un disco reservado para solo el registro compartido. Cada réplica al servicio también tiene un archivo de registro dedicado y se coloca el registro dedicado dentro del directorio de trabajo del servicio. No hay ningún mecanismo para configurar el registro dedicado colocar en una ubicación diferente.

El registro compartido es un área de transición para la información de estado de la réplica, mientras que el archivo de registro dedicado es el destino final donde se almacena. En este diseño, la información de estado se escribe por primera vez en el archivo de registro compartido y pasarlos diferida al archivo de registro dedicada en segundo plano. En este modo, la escritura en el registro compartido tendría la latencia menor y mayor rendimiento que permite que el servicio avanzar más rápido.

Lee y escribe en el registro compartido se realizan a través de IO directa a asignados previamente espacio en disco para el archivo de registro compartido. Para permitir que un uso óptimo de espacio en disco en la unidad de registros dedicadas, se crea el archivo de registro dedicado como archivo disperso NTFS. Tenga en cuenta que esto le permitirá para otros fines de espacio en disco y el sistema operativo mostrará los archivos de registro dedicado con mucha más espacio en disco que se utiliza.

Aparte de una interfaz mínima de modo de usuario en el registro, el registro se escribe como un controlador de modo de núcleo. Funciona como un controlador de modo de núcleo, el registro puede proporcionar el rendimiento más alto para todos los servicios que usa.

Para obtener más información acerca de cómo configurar el registro, vea [Configurar los servicios con estado confiable](service-fabric-reliable-services-configuration.md).

## <a name="stateless-reliable-service"></a>Servicio confiable sin estado

### <a name="architecture-of-a-stateless-service"></a>Arquitectura de un servicio de estado
![Diagrama de la arquitectura de un servicio de estado](./media/service-fabric-reliable-services-platform-architecture/reliable-stateless-service-architecture.png)

### <a name="stateless-reliable-service"></a>Servicio confiable sin estado

Implementaciones de estado del servicio derivan de la clase StatelessService o StatelessServiceBase. La clase StatelessServiceBase permite más flexibilidad que la clase StatelessService.
Ambas clases base administran la duración y la función de un servicio.

La implementación del servicio puede reemplazar métodos virtuales de cualquier clase base si el servicio tiene trabajo que hacer en los puntos del ciclo de vida de servicio, o si desea crear un objeto de escucha de comunicaciones. Tenga en cuenta que aunque el servicio puede implementar su propio objeto de escucha de comunicación exponer ICommunicationListener, en el diagrama anterior, la escucha de comunicación se implementa tela de servicio, como la implementación de ese servicio utiliza una escucha de comunicaciones que se implementa mediante el servicio tela.

Para obtener más información sobre las características de escritura de servicios con las clases StatelessService y StatelessServiceBase, consulte la [Introducción de un servicio confiable](service-fabric-reliable-services-introduction.md) y el [uso avanzado de servicio confiable](service-fabric-reliable-services-advanced-usage.md) .

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Pasos siguientes

Para obtener más información sobre la estructura de servicio, consulte:

[Introducción al servicio confiable](service-fabric-reliable-services-introduction.md)

[Inicio rápido](service-fabric-reliable-services-quick-start.md)

[Información general sobre colecciones confiable](service-fabric-reliable-services-reliable-collections.md)

[Servicio confiable uso avanzado](service-fabric-reliable-services-advanced-usage.md)

[Configuración del servicio confiable](service-fabric-reliable-services-configuration.md)  
