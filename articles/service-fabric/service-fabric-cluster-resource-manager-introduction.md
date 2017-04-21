<properties
   pageTitle="Introducción al administrador de recursos de clúster de servicio tela | Microsoft Azure"
   description="Introducción al administrador de recursos en clúster servicio tela."
   services="service-fabric"
   documentationCenter=".net"
   authors="masnider"
   manager="timlt"
   editor=""/>

<tags
   ms.service="Service-Fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="08/19/2016"
   ms.author="masnider"/>

# <a name="introducing-the-service-fabric-cluster-resource-manager"></a>Introducción al administrador de recursos de clúster de tela de servicio
Administrar tradicionalmente los sistemas de TI o un conjunto de servicios pensado obtener unos físicos o máquinas virtuales dedicados a esos sistemas o servicios específicos. Muchos servicios principales se dividen en un nivel de "web" y un nivel de "datos" o "almacenamiento", quizás con algunos otros componentes especializadas como una caché. Otros tipos de aplicaciones tendría un nivel de mensajería donde las solicitudes de flujo y conectado a un nivel de trabajo para los análisis y transformación necesario como parte de la mensajería. Cada tipo de carga de trabajo tiene un máquinas específicas dedicado a ella: la base de datos tiene algunos equipos dedicados a ella, los servidores web a pocos. Si un determinado tipo de carga de trabajo había causado los equipos estaba en ejecución demasiado caliente, agrega más máquinas con ese tipo de carga de trabajo configurado para ejecutar en él, o reemplaza algunos de los equipos con equipos más grandes. Fácil. Si se produce un error en un equipo, esa parte de la aplicación global ejecutó en capacidad inferior hasta que se puede restaurar el equipo. Bastante fácil (si no es necesariamente divertido).

Ahora sin embargo, vamos a decir haya encontrado una necesidad de escalar y se han los contenedores o microservice salto. De repente se encuentra con decenas, cientos o incluso miles de equipos, docenas de distintos tipos de servicios, quizás cientos de distintas instancias de estos servicios, cada una con una o varias instancias o réplicas para alta disponibilidad (HA).

Administrar su entorno de repente no es tan sencillo como administrar algunos equipos dedicados a solo tipos de cargas de trabajo. Los servidores son virtuales y ya no tienen nombres ( *han* cambiado máximas de [animales a ganado](http://www.slideshare.net/randybias/architectures-for-open-and-scalable-clouds/20) después de todo). Configuración es menor acerca de los equipos y más información acerca de los servicios a sí mismos. Hardware dedicado es cosa del pasado y servicios se han convertido en pequeños sistemas distribuidos, que ocupan varios pequeños hardware genérico.

Como consecuencia de dividir la aplicación en niveles, anteriormente monolítica en servicios independientes que se ejecutan en hardware básico, ahora tiene más combinaciones de atender. ¿Quién decide qué tipos de cargas de trabajo que se pueden ejecutar en qué hardware, o cuántos? ¿Qué cargas de trabajo funcionan bien en el mismo hardware y que entran en conflicto? Cuando una máquina deja de funcionar... ¿Qué incluso se ejecutan allí? ¿Quién es responsable de asegurarse de que carga de trabajo inicia ejecutando de nuevo? ¿Se espera para que el equipo (virtual?) a que vuelva o las cargas de trabajo conmutar automáticamente a otros equipos y mantenerse ejecutando? ¿Es necesaria intervención? ¿Qué hay actualizaciones en este tipo de entorno?

Como los programadores y los operadores que viven en este tipo de llamadas internacionales, vamos a necesita alguna ayuda para administrar esta complejidad y obtener el sentido que una por comilona contratación e intentar sobre la complejidad de papel con personas no es la respuesta correcta.

¿Qué hacer?

## <a name="introducing-orchestrators"></a>Introducción a orchestrators
Un "Orchestrator" es el término general para un programa de software que ayuda a los administradores a administrar estos tipos de entornos. Orchestrators son los componentes que tomar en solicitudes como "Me gustaría 5 copias de este servicio se ejecuta en mi entorno", que sea verdadera y, a continuación (intentar) manténgalo así.

Orchestrators (no humanos) son lo que ponerse en acción cuando se produce un error en un equipo o una carga de trabajo termina por algún motivo inesperado. Orchestrators mayoría algo más que tratar con error, como ayudar con nuevas implementaciones, gestionar las actualizaciones y cómo tratar el consumo de recursos, pero están todos básicamente sobre el mantenimiento de que algunos deseado estado de configuración en el entorno. Desea poder decirle un Orchestrator lo que desea y tiene hacer el trabajo pesado. Chronos o maratón encima Mesos, flota, conjunto, Kubernetes y servicio tela son ejemplos de Orchestrators (o pídales integrada). Más se crean todo el tiempo como las complejidades de la administración de implementaciones del mundo real en diferentes tipos de entornos y condiciones crecen y cambian.

## <a name="orchestration-as-a-service"></a>Orquestación como servicio
El trabajo del organizador dentro de un clúster de tela de servicio se administra principalmente por el Administrador de recursos de clúster. El Administrador de recursos de clúster de servicio tela es uno de los servicios de sistema dentro de la estructura de servicio y se inicia automáticamente en cada clúster.  En general, trabajo del Administrador de recursos de clúster se divide en tres partes:

1. Aplicar reglas
2. Optimizar su entorno
3. Ayuda en otros procesos

### <a name="what-it-isnt"></a>Qué no
En tradicional N nivel-aplicaciones web siempre hubo algún concepto "equilibradores de carga", normalmente se denomina equilibrador de carga de red (NLB) o un equilibrador de carga de la aplicación (ALB) según donde sal en la pila de red. Algunos equilibradores de carga son basados en como oferta de BigIP F5 Hardware y otras son software según como de Microsoft NLB. En otros entornos es posible que vea algo parecido a HAProxy en este rol. En estas arquitecturas el trabajo de equilibrio de carga está para asegurarse de que todos los equipos diferentes sin estado front-end o los equipos diferentes en el clúster (aproximadamente) reciben la misma cantidad de trabajo. Estrategias para esto varía, de envío de cada llamada a otro servidor diferente a sesión anclada o se pega, asignación de estimación y llamada real según su costo previsto y la carga de máquina actual.

Tenga en cuenta que esto era en el mejor el mecanismo para garantizar que el nivel de web permaneció aproximadamente equilibrada. Estrategias para el equilibrio de la capa de datos estaban completamente diferente y depende del mecanismo de almacenamiento de datos, normalmente centrar alrededor de sharding de datos, almacenamiento en caché, vistas de la base de datos administrada y procedimientos almacenados, etcetera.

Aunque algunas de estas estrategias son interesantes, el Administrador de recursos de clúster de tela de servicio no es nada como un equilibrador de carga de red o una caché. Mientras un equilibrador de carga de red garantiza que se equilibra el front-end moviendo el tráfico a donde se están ejecutando los servicios, el Administrador de recursos de clúster de servicio tela toma una estrategia completamente diferente: básicamente, servicio tela mueve *Servicios* a donde tenga más sentido (y espera tráfico o carga para seguir). Puede ser, por ejemplo, los nodos que son actualmente frías porque los servicios que existen no hacen una gran cantidad de trabajo ahora, o que se han eliminado o movido en otra aplicación. Otro ejemplo el Administrador de recursos de clúster también puede mover un servicio fuera de un equipo que se va a ser actualizado o que se sobrecarga debido a un aumento en el consumo de los servicios que se ejecutan en él. Dado que el Administrador de recursos de clúster es responsable de mover servicios alrededor (no realiza el tráfico de red al lugar donde ya están servicios), contiene un conjunto de características presenten diferencias significativas en comparación con lo que se encuentra en un equilibrador de carga de red y emplea muy diferentes estrategias para garantizar que también se utilizan los recursos de hardware en el clúster.

## <a name="next-steps"></a>Pasos siguientes
- Para obtener información sobre el flujo de información y arquitectura dentro del Administrador de recursos de clúster, consulte [este artículo](service-fabric-cluster-resource-manager-architecture.md)
- El Administrador de recursos de clúster tiene muchas opciones para describir el clúster. Para saber más acerca de ellos desproteger en este artículo en la [Descripción de un clúster de tela de servicio](service-fabric-cluster-resource-manager-cluster-description.md)
- Para obtener más información acerca de las opciones disponibles para configurar los servicios, vea el tema sobre las otras configuraciones del Administrador de recursos de clúster disponibles [Obtenga más información sobre la configuración de servicios](service-fabric-cluster-resource-manager-configure-services.md)
- Métricas son cómo se administra el Administrador de recursos de clúster de servicio tela consumo y la capacidad del clúster. Para obtener más información acerca de ellos y cómo configurarlos consulte [este artículo](service-fabric-cluster-resource-manager-metrics.md)
- El Administrador de recursos de clúster funciona con las capacidades de administración del servicio tela. Para obtener más información sobre esa integración, lea [este artículo](service-fabric-cluster-resource-manager-management-integration.md)
- Para obtener información sobre cómo el Administrador de recursos de clúster administra y equilibra carga en el clúster, consulte el artículo sobre el [Equilibrio de carga](service-fabric-cluster-resource-manager-balancing.md)
