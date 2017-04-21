<properties
   pageTitle="Información general sobre el modelo de programación de servicio confiable tela | Microsoft Azure"
   description="Obtenga más información sobre el modelo de programación de tela de servicio servicio confiable y empezar a escribir sus propios servicios."
   services="Service-Fabric"
   documentationCenter=".net"
   authors="masnider"
   manager="timlt"
   editor="vturecek; mani-ramaswamy"/>

<tags
   ms.service="Service-Fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="03/25/2016"
   ms.author="masnider;vturecek"/>

# <a name="reliable-services-overview"></a>Introducción a servicios confiable
Azure tela de servicio simplifica la escritura y administrar servicios confiables con y sin estado. Habla acerca de este documento:

- El modelo de programación de servicios confiables para los servicios con y sin estado.
- Las opciones, tiene que hacer al escribir un servicio confiable.
- Algunos escenarios y ejemplos de cuándo usar servicios confiable y cómo se escriben.

Servicios confiables es uno de los modelos de programación en tela de servicio. Para obtener más información sobre el modelo de programación de actores confiable, vea [Introducción a los actores fiable de servicio tela](service-fabric-reliable-actors-introduction.md).

En tela de servicio, un servicio se compone de configuración de código de la aplicación y (opcionalmente) de estado.

Tela de servicio administra la duración de servicios de aprovisionamiento y la implementación a través de actualización y eliminación a través de la [administración de aplicaciones de servicio tela](service-fabric-deploy-remove-applications.md).

## <a name="what-are-reliable-services"></a>¿Qué servicios confiables?
Servicios confiables le ofrece un modelo de programación de nivel superior, eficaces y sencillo para ayudarle a express lo que es importante para la aplicación. Con los servicios confiable modelo de programación, obtendrá:

- Para los servicios con estado, el modelo de programación de servicios confiables permite coherente y confiable almacenar su derecho de estado dentro de su servicio mediante colecciones confiable. Se trata de un conjunto de clases de colección altamente disponibles que le será familiar a cualquier persona que ha usado C# colecciones simples. Tradicionalmente, los servicios necesarios sistemas externos para la administración de estado confiable. Colecciones fiable, puede almacenar su estado junto a su cálculo con la misma alta disponibilidad y la confiabilidad que ha llegado al pasar de stores externos altamente disponibles y con las mejoras de latencia adicional que proporcionan conjuntamente localizar el cálculo y el estado.

- Un modelo simple para ejecutar su propio código que es similar a que se usa en los modelos de programación. El código tiene un punto de entrada bien definido y fácil de administrar del ciclo de vida.

- Un modelo de comunicación acoplable. Utilice el transporte de su elección, como HTTP con la [API de Web](service-fabric-reliable-services-communication-webapi.md), WebSockets, protocolos TCP personalizados, etcetera. Servicios confiables proporcionan algunos excelente del cuadro Opciones puede usar, o puede proporcionar su propio.

## <a name="what-makes-reliable-services-different"></a>¿Qué hace diferentes servicios confiables?
Servicios confiables en tela del servicio es diferente de servicios que se ha escrito antes. Servicio tela proporciona confiabilidad, disponibilidad, coherencia y escalabilidad.  

- **Confiabilidad**: su servicio permanecen incluso en entornos poco confiables, donde los equipos pueden fallar o visitas problemas de red.

- **Disponibilidad**, su servicio estará accesible y capacidad de respuesta. (Esto no significa que no puede tener servicios que no se encuentra o llegar desde fuera de.)

- **Escalabilidad**se separan los servicios de hardware específicos y pueden aumentar o reducir según sea necesario mediante la adición o eliminación de hardware o recursos virtuales. Servicios fácilmente se dividen (especialmente en el caso de estado) para asegurarse de que las partes independientes del servicio pueden escalar y responder a errores de forma independiente. Por último, servicio tela promueva servicios que sea ligero al permitir miles de servicios de aprovisionamiento en un único proceso, en lugar de que requieren o dedica toda instancias de sistema operativo a una instancia de una carga de trabajo determinado.

- Se puede garantizar la **coherencia**--toda la información almacenada en este servicio para que sea coherente (Esto se aplica solo a los servicios con estado - más información sobre esto más adelante)

## <a name="service-lifecycle"></a>Ciclo de vida de servicio
Si su servicio se con estado o sin estado, servicios fiable proporcionan un ciclo de vida simple que permite conectar rápidamente en el código y empezar a trabajar.  Hay uno o dos métodos que necesita para implementar para poner en marcha su servicio.

- **CreateServiceReplicaListeners/CreateServiceInstanceListeners** - esto es donde el servicio define la pila de comunicación que desea usar. La pila de comunicaciones, como las [API de Web](service-fabric-reliable-services-communication-webapi.md), es lo que define el extremo de escucha o extremos para el servicio (cómo clientes se llegar a él). También define cómo los mensajes que aparecen terminan en interactúe con el resto del código de servicio.

- **RunAsync** - esto es donde el servicio ejecuta su lógica empresarial. El token de cancelación proporcionado es una señal de cuándo debe detenerse que funcionan. Por ejemplo, si tiene un servicio que necesita para extraer los mensajes fuera de una cola confiable y procesar constantemente, es donde sucede que funcionan.

### <a name="service-startup"></a>Inicio del servicio

Los eventos importantes del ciclo de vida de un servicio confiable son:

1. Se crea el objeto de servicio (lo que se deriva el servicio independiente o el servicio de estado).

2. La `CreateServiceReplicaListeners` / `CreateServiceInstanceListeners` se denomina método, lo que permite el servicio para devolver escucha de comunicación de uno o más de su elección.
  - Tenga en cuenta que es opcional, aunque la mayoría de los servicios se expone algunas extremo directamente.

3. Una vez que se crean los agentes de escucha de comunicación, se abre.
  - Escucha de comunicación tiene un método denominado `OpenAsync()`, que se llama en este momento y que devuelve la dirección de escucha para el servicio. Si su servicio confiable usa uno de los ICommunicationListeners integrados, esto se controla por usted.

4. Una vez que el oyente de comunicaciones está abierto, el `RunAsync()` se llama método en el servicio principal.
  - Tenga en cuenta que `RunAsync()` es opcional. Si el servicio hace todo su trabajo directamente en respuesta a usuario solo llamadas, no es necesario para que pueda implementar `RunAsync()`.

### <a name="service-shutdown"></a>Cierre del servicio

Cuando se cierra el servicio (va a eliminar, actualizado o movido) se refleja el orden de llamada: en primer lugar, el token de cancelación mantenidos por `RunAsync()` se cancela; a continuación, `CloseAsync()` se denomina en los agentes de escucha de comunicaciones.

Hay algunas cosas importantes Nota sobre apagado para los servicios con estado:

- Tela de servicio no se promover otra réplica de su servicio de estado principal hasta `CloseAsync` y `RunAsync` han devuelto. Si está usando una escucha de comunicación integradas, el `CloseAsync` método se administra automáticamente.

- Aunque no hay ningún límite de tiempo en devolver de estos métodos, inmediatamente pierde la capacidad para escribir en colecciones confiable y, por tanto, no puede completar ningún trabajo real. Se recomienda que vuelva tan rápidamente como sea posible al recibir la solicitud de cancelación.

## <a name="example-services"></a>Servicios de ejemplo
Conocer este modelo de programación, echemos un vistazo a dos servicios distintos para ver cómo encajan estas piezas.

### <a name="stateless-reliable-services"></a>Servicios confiables sin estado
Un servicio independiente es uno donde literalmente no hay ningún estado mantenida en el servicio o el estado que se encuentra está completamente disponible y no requiere sincronización, replicación, persistencia o alta disponibilidad.

Por ejemplo, considere una calculadora que no tiene memoria y recibe todos los términos y las operaciones a realizar al mismo tiempo.

En este caso, el RunAsync() del servicio puede estar vacío, ya que no hay ningún fondo-procesamiento de tareas que debe realizar el servicio. Cuando se crea el servicio de calculadora, devolverá un ICommunicationListener (por ejemplo, la [API de Web](service-fabric-reliable-services-communication-webapi.md)) que abre un extremo de escucha en algún puerto. Este extremo de escucha conectar a los diferentes métodos (ejemplo: "Agregar (n1, n2)") que definen la API pública de calculadora.

Cuando se realiza una llamada de un cliente, se invoca el método adecuado, y el servicio de calculadora realiza las operaciones en los datos proporcionados y devuelve el resultado. No almacena cualquier estado.

No almacenar cualquier estado interno hace esta calculadora de ejemplo muy simple. Pero la mayoría de servicios no está realmente sin estado. En su lugar, extraer su estado en el otro almacén. (Por ejemplo, una aplicación web que se basa en mantener el estado de sesión en un almacén de respaldo o en caché no está completamente independiente.)

Un ejemplo de cómo se usan servicios sin estado en tela de servicio es como un cliente que expone la API público para una aplicación web. El servicio front-end, a continuación, se comunica con los servicios con estado para completar una solicitud de usuario. En este caso, llamadas de clientes se dirigen a un puerto, como 80, donde está escuchando el servicio de estado. Este servicio sin estado recibe la llamada y determina si la llamada es de una entidad de confianza, como así como el servicio que está destinado para.  A continuación, el servicio de estado reenvía la llamada a la partición correcta del servicio con estado y espera una respuesta. Cuando el servicio de estado recibe una respuesta, las respuestas al cliente original.

### <a name="stateful-reliable-services"></a>Servicios confiables con estado
Un servicio con estado es uno que debe tener una parte de estado que se mantiene coherente y presentar el servicio de función. Considere la posibilidad de un servicio que constantemente calcula una media acumulada de algún valor basándose en las actualizaciones que recibe. Para ello, debe tener el conjunto actual de las solicitudes entrantes que necesita para el proceso, así como el promedio actual. Cualquier servicio que recupera, procesa y almacena información en un almacén externo (por ejemplo, una Azure blob o tabla tienda hoy) es con estado. Solo se mantiene su estado en el almacén de estado externo.

La mayoría de servicios hoy almacena su estado externamente, ya que el almacén externo es lo que proporciona confiabilidad, disponibilidad, escalabilidad y coherencia para dicho estado. En tela de servicio, no están necesario almacenar su estado externamente; services con estado Tela de servicio se encarga de estos requisitos para el código de servicio y el estado del servicio.

Supongamos que desea escribir un servicio que toma las solicitudes de una serie de conversiones que deben llevarse a cabo en una imagen y la imagen que debe convertirse.  Este servicio, debería devolver una escucha de comunicación (supongamos Web API) que se abre el puerto de comunicación y permite envíos a través de una API como `ConvertImage(Image i, IList<Conversion> conversions)`. En esta API, el servicio podría tener la información y almacenar la solicitud de una cola confiable y vuelva algunos token al cliente por lo que puede realizar un seguimiento de la solicitud (como las solicitudes podrían tardar algún tiempo).

En este servicio, RunAsync podría ser más complejo. El servicio podría tener un bucle dentro de su RunAsync que extrae solicitudes de IReliableQueue, realiza las conversiones enumeradas y almacena los resultados en IReliableDictionary, de modo que cuando vuelva al cliente, puede obtener sus imágenes convertidas. Para asegurarse de que incluso si se produce un error la imagen no se pierde, este servicio confiable ¿extraer fuera de la cola, realizar las conversiones y almacenar el resultado en una transacción. En este caso, el mensaje se quita realmente solo de la cola y los resultados se almacenan en el diccionario de resultado cuando se han completado las conversiones. Si se produce un error en el medio (por ejemplo, el equipo en el que está ejecutando esta instancia del código), la solicitud permanece en la cola de espera para volver a procesar.

Una cosa que tenga en cuenta sobre este servicio es que parece un servicio .NET normal. La única diferencia es que las estructuras de datos que se usa (IReliableQueue y IReliableDictionary) se proporcionan en tela de servicio y, por tanto, se realizan altamente confiables, disponibles y coherentes.

## <a name="when-to-use-reliable-services-apis"></a>Cuándo usar confiable API de los servicios
Si uno de estos procedimientos caracterizan sus necesidades de servicio de la aplicación, debe tener en cuenta confiable API de servicios:

- Debe proporcionar el comportamiento de la aplicación a través de varias unidades de estado (por ejemplo, pedidos y ordenar los elementos de línea).

- Estado de la aplicación se puede modelar naturalmente como diccionarios confiable y colas.

- El estado debe estar altamente disponibles con acceso de latencia baja.

- La aplicación se necesita controlar la simultaneidad o detalle de operaciones de transacción a través de una o varias colecciones fiable.

- Desea administrar las comunicaciones o controlar el esquema de particiones para el servicio.

- El código necesita un entorno de tiempo de ejecución de subproceso libre.

- La aplicación se necesita crear dinámicamente o destroy diccionarios fiable o colas en tiempo de ejecución.

- Debe controlar siempre tela de servicio de copia de seguridad y restaurar características para estado * su servicio mediante programación.

- La aplicación se necesita mantener el historial de cambios para sus unidades de estado *.

- Desea desarrollar o consumir estado personalizado desarrollado de fiesta de otros proveedores *.

> [AZURE.NOTE] * Características disponibles en la disponibilidad general SDK.


## <a name="next-steps"></a>Pasos siguientes
+ [Inicio rápido de servicios confiable](service-fabric-reliable-services-quick-start.md)
+ [Servicios confiables uso avanzado](service-fabric-reliable-services-advanced-usage.md)
+ [El modelo de programación de actores confiable](service-fabric-reliable-actors-introduction.md)
