<properties
   pageTitle="Diferencias entre servicios en la nube y servicio tela | Microsoft Azure"
   description="Información general y conceptual para migrar las aplicaciones de servicio tela de servicios en la nube."
   services="service-fabric"
   documentationCenter=".net"
   authors="vturecek"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotNet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="10/19/2016"
   ms.author="vturecek"/>

# <a name="learn-about-the-differences-between-cloud-services-and-service-fabric-before-migrating-applications"></a>Obtenga más información sobre las diferencias entre servicios en la nube y tela de servicio antes de migrar aplicaciones.
Tela de servicio de Microsoft Azure es la plataforma de aplicación de nube de última generación para las aplicaciones distribuidas de gran escalabilidad, altamente confiables. Presenta muchas características nuevas de embalaje, implementar, actualizar y administrar aplicaciones de nube distribuido. 

Esta es una guía de introducción para migrar las aplicaciones de servicio tela de servicios en la nube. Se centra principalmente en arquitectura y diferencias de diseño entre servicios de nube y tela de servicio.
 
## <a name="applications-and-infrastructure"></a>Aplicaciones y la infraestructura

Una diferencia fundamental entre los servicios de nube y tela del servicio es la relación entre máquinas virtuales, las cargas de trabajo y las aplicaciones. Aquí una carga de trabajo se define como el código que escriba para realizar una tarea específica o proporcionar un servicio.
 
 - **Servicios de nube es sobre cómo implementar aplicaciones como máquinas virtuales.** El código que escriba se acopla estrechamente a una instancia de máquina virtual, como una función de trabajo o la Web. Para implementar una carga de trabajo en servicios de nube es implementar una o varias instancias VM que se ejecutan la carga de trabajo. Hay una separación de aplicaciones y máquinas virtuales y, así que no hay ninguna definición formal de una aplicación. Puede considerar una aplicación como un conjunto de instancias de rol de trabajo o Web dentro de una implementación de servicios de nube o una implementación completa de servicios en la nube. En este ejemplo, se muestra una aplicación como un conjunto de instancias de la función.
 
![Topología y aplicaciones de servicios de nube][1]

 - **Servicio tela consiste en implementación de aplicaciones en máquinas virtuales existentes o equipos que ejecutan servicio tela en Windows o Linux.** Los servicios que escriba están completamente desacoplados de la infraestructura subyacente, que se resume la plataforma de aplicación de servicio tela, por lo que puede implementar una aplicación a varios entornos. Una carga de trabajo en tela de servicio se llama a un servicio de"" y uno o más servicios se agrupan en una aplicación formal definidos por el que se ejecuta en la plataforma de aplicaciones de servicio tela. Pueden implementar varias aplicaciones a un único clúster de tela de servicio.
 
![Topología y aplicaciones de servicio tela][2]
 
Tela servicio propio es una capa de plataforma de aplicación que se ejecuta en Windows o Linux, mientras que los servicios de nube es un sistema para implementar máquinas virtuales administradas Azure con cargas de trabajo adjuntados.
El modelo de aplicación de servicio tela tiene varias ventajas:

 - Horas de implementación rápida. Crear instancias VM puede tardar mucho tiempo. En tela de servicio, máquinas virtuales se implementan solo una vez para formar un clúster que hospeda la plataforma de aplicaciones de servicio tela. A partir de ese momento, se pueden implementar en muy rápidamente paquetes de aplicaciones en el clúster.
 - Alta densidad de hospedaje. En servicios de nube, una función de trabajo máquina virtual aloja una carga de trabajo. En tela de servicio, las aplicaciones son independientes de las máquinas virtuales que se ejecutan en ellos, lo que significa que puede implementar una gran cantidad de aplicaciones a un pequeño número de máquinas virtuales, lo que puede reducir el costo total para implementaciones más grandes.
 - La estructura del servicio de plataforma puede ejecutar cualquier parte que tiene equipos con Windows Server o Linux, ya sea Azure o local. La plataforma proporciona una capa de abstracción sobre la infraestructura subyacente para que pueda ejecutar la aplicación en diferentes entornos. 
 - Administración de la aplicación distribuida. Servicio tela es una plataforma que no sólo hosts aplicaciones distribuyen, sino que también ayuda a administrar el ciclo de vida del equipo o de su ciclo de vida independientemente de la máquina virtual de hospedaje.

## <a name="application-architecture"></a>Arquitectura de la aplicación

Normalmente, la arquitectura de una aplicación de servicios de nube incluye numerosas dependencias de servicio externo, como Bus de servicio, tabla de Azure y almacenamiento de blobs, SQL, Redis y otros usuarios para administrar el estado y los datos de una aplicación y la comunicación entre Web y las funciones de trabajo en una implementación de servicios en la nube. Un ejemplo de una aplicación de servicios de nube completa podría tener este aspecto:  

![Arquitectura de servicios de nube][9]

Aplicaciones de servicio tela también puede usar los mismos servicios externos en una aplicación completa. En este ejemplo arquitectura de servicios de nube, la ruta de migración más sencilla de servicios de nube al servicio tela es reemplazar la implementación de servicios de nube con una aplicación de servicio tela, manteniendo la arquitectura general de la misma. Las funciones de trabajo y la Web se pueden migrar a servicios sin estado del servicio tela con los cambios de código mínimo.

![Arquitectura de servicio tela después de la migración simple][10]

En este momento, el sistema debería seguir funcionando igual que antes. Aprovechar las características de estado del servicio tela, almacena estado externo se puede hemos como los servicios con estado donde sea aplicable. Esto es más compleja que una migración simple de Web y las funciones de trabajo a los servicios de estado de servicio tela, ya que requiere servicios personalizados que proporcionan funcionalidad equivalente a la aplicación como los servicios externos hacían antes de escribir. Las ventajas de hacerlo son: 

 - Quitar dependencias externas 
 - Unificar la implementación, administración y actualización de modelos. 
 
Una arquitectura de resultante de ejemplo de internalizing estos servicios podría tener este aspecto:

![Arquitectura de servicio tela después de la migración total][11]

## <a name="communication-and-workflow"></a>Comunicaciones y flujo de trabajo

Aplicaciones de servicio de nube mayoría constan de más de un nivel. Del mismo modo, una aplicación de servicio tela formado por más de un servicio (normalmente muchos servicios). Dos modelos de comunicación comunes son comunicación directa y a través de un resistentes de almacenamiento externo.

### <a name="direct-communication"></a>Comunicación directa

Con la comunicación directa niveles pueden comunicarse directamente a través de extremo expuesta por cada nivel. En entornos sin estado como servicios en la nube, este significa que seleccionar una instancia de un rol de VM aleatoriamente o turnos para equilibrar la carga y conectarse directamente a su punto final.

![Comunicación directa de servicios de nube][5]

 Comunicación directa es un modelo de comunicación comunes en tela de servicio. La diferencia clave entre tela de servicio y los servicios de nube es que en servicios de nube se conecta a una máquina virtual, mientras que en tela de servicio que se conecte a un servicio. Esta es una distinción importante por dos razones:

 - Servicios en tela de servicio no se enlazan a las máquinas virtuales que hospeda Puede desplazarse en el clúster de servicios y de hecho, se espera para desplazarse por varias razones: equilibrio de recursos, migración tras error, las actualizaciones de aplicaciones y la infraestructura y las restricciones de colocación o carga. Esto significa que puede cambiar la dirección de la instancia de servicio en cualquier momento. 
 - Una máquina virtual en tela de servicio puede hospedar varios servicios, cada una con extremos únicos.

Servicio tela proporciona un mecanismo de descubrimiento de servicio, el servicio de nombres, que se puede utilizar para resolver las direcciones de extremo de servicios. 

![Comunicación directa de tela de servicio][6]

### <a name="queues"></a>Colas

Un mecanismo de comunicación común entre niveles en entornos sin estado como servicios de nube es utilizar una cola de almacenamiento externo para almacenar permanentemente tareas de trabajo desde un nivel a otro. Un escenario común es un nivel de web que envía trabajos a una cola de Azure o Bus de servicio, donde pueden quitar de la cola y procese los trabajos de instancias de la función de trabajo.

![Comunicación de cola de servicios de nube][7]

El mismo modelo de comunicación puede usarse en tela de servicio. Esto puede ser útil al migrar una aplicación de servicios de nube existente en tela de servicio. 

![Comunicación directa de tela de servicio][8]
 
## <a name="next-steps"></a>Pasos siguientes

La ruta de migración más sencilla de servicios de nube al servicio tela es reemplazar la implementación de servicios de nube con una aplicación de servicio tela, manteniendo la arquitectura general de la aplicación aproximadamente el mismo. El siguiente artículo proporciona una guía para ayudar a convertir un Web o una función de trabajo en un servicio de estado de servicio tela.

 - [Migración simple: convertir un Web o una función de trabajo en un servicio de estado de tela de servicio](./service-fabric-cloud-services-migration-worker-role-stateless-service.md)

<!--Image references-->
[1]: ./media/service-fabric-cloud-services-migration-differences/topology-cloud-services.png
[2]: ./media/service-fabric-cloud-services-migration-differences/topology-service-fabric.png
[5]: ./media/service-fabric-cloud-services-migration-differences/cloud-service-communication-direct.png
[6]: ./media/service-fabric-cloud-services-migration-differences/service-fabric-communication-direct.png
[7]: ./media/service-fabric-cloud-services-migration-differences/cloud-service-communication-queues.png
[8]: ./media/service-fabric-cloud-services-migration-differences/service-fabric-communication-queues.png
[9]: ./media/service-fabric-cloud-services-migration-differences/cloud-services-architecture.png
[10]: ./media/service-fabric-cloud-services-migration-differences/service-fabric-architecture-simple.png
[11]: ./media/service-fabric-cloud-services-migration-differences/service-fabric-architecture-full.png
