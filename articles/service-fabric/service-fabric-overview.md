<properties
   pageTitle="Información general sobre el servicio tela | Microsoft Azure"
   description="Información general sobre la estructura de servicio, donde aplicaciones están compuestos por varios microservices para proporcionar escala y resistencia. Estructura de servicio es una plataforma de sistemas distribuidos utiliza para generar scalable, confiable y fácil de administrar aplicaciones para la nube"
   services="service-fabric"
   documentationCenter=".net"
   authors="msfussell"
   manager="timlt"
   editor="masnider"/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="10/22/2016"
   ms.author="mfussell"/>

# <a name="overview-of-service-fabric"></a>Información general sobre la estructura de servicio
Estructura de servicio es una plataforma de sistemas distribuidos que hace que sea fácil empaquetar, implementar y administrar microservices scalable y confiable. Servicio tela también soluciona los desafíos importantes en desarrollar y administrar aplicaciones de la nube. Los programadores y administradores pueden evitar problemas de infraestructura compleja y enfoque de resolución en su lugar en la implementación de cargas de trabajo críticas y exigentes saber que están scalable, confiable y fácil de administrar. Servicio tela representa la plataforma de software intermedio de última generación para crear y administrar estos empresariales, aplicaciones de nube escala de nivel 1.

Este [breve vídeo](https://aka.ms/servicefabricvideo) proporciona una introducción a la estructura de servicio y microservices.


## <a name="applications-composed-of-microservices"></a>Aplicaciones que se componen de microservices
Tela de servicio le permite crear y administrar aplicaciones scalable y confiables se compone de microservices que se ejecuta en muy alta densidad con un grupo compartido de equipos (denominados un clúster). Proporciona un tiempo de ejecución sofisticado para generar distribuida, scalable microservices con y sin estado. También proporciona las capacidades de administración de la aplicación completa de aprovisionamiento, implementación, supervisión, actualizar y revisiones y eliminar desarrollan aplicaciones.

¿Por qué es importante un enfoque microservices? Las dos razones principales son:

1. Permiten escalar distintas partes de la aplicación según sus necesidades.

2. Equipos de desarrollo pueden ser más ágil en distribuir los cambios y, por tanto, proporcionan características a sus clientes más rápidamente y con más frecuencia.

Servicio tela potencia muchos servicios de Microsoft en la actualidad, incluida la base de datos de SQL Azure, Azure DocumentDB, Cortana, Power BI, Microsoft Intune, Hubs de evento de Azure, IoT Azure, Skype empresarial y muchos básicas de servicios de Azure.

Servicio tela está diseñada para la creación de nube servicios nativos que pueden comenzar pequeña, según sea necesario y crecer a escala masiva con cientos o miles de equipos.

Servicios de Internet escala de hoy se han diseñado de microservices. Ejemplos de microservices, puertas de enlace de protocolo, perfiles de usuario, compras carros de inventario de procesamiento, colas y se almacena en caché. Servicio tela es una plataforma de microservices que proporciona cada microservice un nombre único que puede ser independiente o con estado.

Servicio tela proporciona completas capacidades de administración de ciclo de vida y tiempo de ejecución para las aplicaciones que se compone de estos microservices. Anfitriones microservices dentro de contenedores implementado y activado en el clúster de servicio tela. Mover de máquinas virtuales a posible de contenedores hace un aumento de magnitud en densidad. Del mismo modo, es posible otra orden de magnitud en densidad moviendo de contenedores a microservices. Por ejemplo, un único clúster de base de datos de SQL Azure incluye cientos de equipos que ejecutan miles de contenedores un total de cientos de miles de bases de datos de host. Cada base de datos es una microservice con estado del servicio tela. El mismo es cierta acerca de los otros servicios que se mencionó anteriormente, motivo por el que se utiliza el término "ampliados" para describir las capacidades de servicio tela. Si contenedores le ofrecen alta densidad, a continuación, microservices proporcionarle ampliados.

Para obtener más información sobre el enfoque microservices, lea [por qué un enfoque microservices a la creación de aplicaciones?](service-fabric-overview-microservices.md)

## <a name="container-deployment-and-orchestration"></a>Organización e implementación de contenedor
Estructura de servicio es un [orchestrator](service-fabric-cluster-resource-manager-introduction.md) de microservices en un clúster de equipos. Microservices se pueden desarrollar de muchas formas de mediante los [modelos de programación de servicio tela](service-fabric-choose-framework.md) implementar [ejecutables de invitado](service-fabric-deploy-existing-app.md). Servicio tela puede implementar los servicios de imágenes de contenedor y lo que es importante puede combinar servicios en los procesos y servicios en contenedores juntos en la misma aplicación. Si solo desea [implementar y administrar imágenes del contenedor](service-fabric-containers-overview.md) en un clúster de equipos, tela de servicio es una opción ideal para esto.


## <a name="create-service-fabric-clusters-anywhere"></a>Crear clústeres de servicio tela en cualquier lugar
Puede crear clústeres de servicio tela en muchos entornos, incluyendo Azure o local, en Windows Server o en Linux. Además, el entorno de desarrollo en el SDK es idéntico al entorno de producción con ningún emuladores implicados. En otras palabras, si se ejecuta en el clúster de desarrollo local se implementa en el mismo clúster en otros entornos.

Para obtener más información sobre la creación de clústeres local, lea [crear un clúster en Windows Server o Linux](service-fabric-deploy-anywhere.md) o para Azure creando un clúster [a través del portal de Azure](service-fabric-cluster-creation-via-portal.md).

![Plataforma de tela de servicio][Image1]

## <a name="stateless-and-stateful-service-fabric-microservices"></a>Estado y sin microservices tela de servicio

Tela de servicio le permite crear aplicaciones formada por microservices. Sin estado microservices (puertas de enlace de protocolo, servidores proxy de web, etc.) no mantiene un estado modificable fuera cualquier solicitud dada y su respuesta del servicio. Funciones de trabajo de servicios de nube de Azure son un ejemplo de un servicio de estado. Microservices con estado (cuentas de usuario, las bases de datos, dispositivos y compras carros, colas, etc.) mantener un estado modificable, relevantes más allá de la solicitud y su respuesta. Escala de Internet aplicaciones actuales consta de una combinación de estado y sin microservices.

¿Por qué tener microservices con estado junto con otros sin estado? Las dos razones principales son:

1. La capacidad de generación de alto rendimiento, baja latencia, con tolerancia a errores de proceso de transacción servicios (OLTP) al mantener el código y cerrar los datos en el mismo equipo. Algunos ejemplos son apropiados interactivos, búsqueda, sistemas de Internet de cosas (IoT), sistemas de comercio, sistemas de detección de procesamiento y fraude de tarjeta de crédito y administración de registros personal.

2. Simplificar de diseño de aplicación. Microservices con estado quitar la necesidad de colas adicionales y almacena en caché, habitualmente necesarias para satisfacer los requisitos de disponibilidad y la latencia de una aplicación puramente sin estado. Servicios con estado naturalmente son alta y baja latencia, reduciendo el número de partes móviles para administrar en la aplicación como un todo.

Para obtener más información sobre patrones de aplicación con tela de servicio, lea [escenarios de aplicaciones](service-fabric-application-scenarios.md) y [Elija un marco de modelo de programación](service-fabric-choose-framework.md) para el servicio

## <a name="application-lifecycle-management"></a>Administración del ciclo de vida de aplicaciones
Servicio tela proporciona soporte de primera clase para la administración de ciclo de vida de aplicación completa (ALM) de las aplicaciones de nube: desde el desarrollo hasta la implementación, administración diaria y mantenimiento para retirar final.

Las capacidades de servicio tela ALM permiten a los administradores de la aplicación / operadores de TI usar flujos de trabajo simples, bajo táctil a disposición, implementar, revisión y supervisar las aplicaciones. Estos flujos de trabajo integrados reducen en gran medida la carga de operadores de TI para mantener las aplicaciones disponibles continuamente.

Muchas aplicaciones constan de una combinación de estado y sin microservices y otro ejecutables/Runtime implementado juntas. Al tener tipos de seguros en las aplicaciones y empaquetado microservices, tela de servicio permite la implementación de varias instancias de aplicación. Cada instancia es administrada y se actualizan de forma independiente. Lo que es importante, tela del servicio es capaz de implementar *los* ejecutables o runtime y hacer que sean confiables. Por ejemplo, tela servicio implementa ASP.NET Core 1, Node.js, máquinas virtuales de Java, secuencias de comandos o nada que componen la aplicación.

Para obtener más información sobre la administración de ciclo de vida de aplicación, lea [el ciclo de vida de aplicación](service-fabric-application-lifecycle.md) y de la implementación de cualquier código, vea [implementar un invitado ejecutable](service-fabric-deploy-existing-app.md)

## <a name="key-capabilities"></a>Capacidades clave
Mediante la estructura de servicio, puede:

- Desarrollar aplicaciones masivamente scalable recuperación automática.

- Desarrolle aplicaciones se compone de microservices utiliza el modelo de programación de servicio tela. O bien, simplemente host invitado ejecutables y otros marcos de aplicaciones de su elección, como ASP.NET Core 1 o Node.js.

- Desarrollar confiables microservices con y sin estado.

- Implementar y coordinar contenedores incluyen contenedores de Windows y Docker en un clúster. Estos contenedores pueden contenedor invitado ejecutables o microservices con y sin estado confiable.  En cualquier caso, obtendrá puerto contenedor asignación de puerto de host, de detectabilidad de contenedor y de errores automatizado.

- Simplificar el diseño de la aplicación mediante microservices con estado en lugar de caché y colas.

- Implementar Azure o nubes locales que ejecutan Windows Server o Linux con cero cambios en el código. Escribir una vez y, a continuación, implementar en cualquier lugar a cualquier clúster de tela de servicio.

- Desarrollar con un enfoque "Centro de datos en su equipo". El entorno de desarrollo local es el mismo código que se ejecuta en los centros de datos de Azure.

- Distribuir aplicaciones en segundos.

- Distribuir aplicaciones en mayor densidad de máquinas virtuales, implementar cientos o miles de aplicaciones de cada equipo.

- Implementar versiones diferentes de la misma aplicación en paralelo, cada forma independiente actualizable.

- Administrar el ciclo de vida de las aplicaciones con estado sin ningún tiempo de inactividad, incluidas las actualizaciones de última hora y de no separación.

- Administrar aplicaciones con la API de .NET, Java (Linux), PowerShell, Azure CLI (Linux) o resto interfaces.

- Actualizaciones y revisiones microservices en las aplicaciones de forma independiente.

- Supervisar y diagnosticar el estado de las aplicaciones y establecer directivas para efectuar reparaciones automáticas.

- Capacidad de escalado o el número de nodos en un clúster, así como escalado de escala o reducción del tamaño de cada nodo, saber que las aplicaciones escalarán automáticamente y se distribuyen según los recursos disponibles.

- Ver la recuperación automática equilibrador de recursos coordinar la redistribución de aplicaciones en el clúster. Servicio tela recupera de errores y optimiza la distribución de carga en función de los recursos disponibles.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Pasos siguientes

* Para obtener más información:
    * [¿Por qué un microservices acercarse a la creación de aplicaciones?](service-fabric-overview-microservices.md)
    * [Información general de terminología](service-fabric-technical-overview.md)
* Configurar el [entorno de desarrollo](service-fabric-get-started.md) de tela de servicio  
* [Elegir un marco de modelo de programación](service-fabric-choose-framework.md) para el servicio

[Image1]: media/service-fabric-overview/Service-Fabric-Overview.png
