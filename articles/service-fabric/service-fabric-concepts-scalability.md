<properties
   pageTitle="Escalabilidad de servicios de servicio tela | Microsoft Azure"
   description="Describe cómo escalar servicios tela de servicio"
   services="service-fabric"
   documentationCenter=".net"
   authors="appi101"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="08/10/2016"
   ms.author="aprameyr"/>

# <a name="scaling-service-fabric-applications"></a>Aplicaciones de servicio tela escala
Azure tela de servicio facilita la crear aplicaciones scalable equilibrio de carga servicios, particiones y réplicas en todos los nodos en un clúster. Esto permite la utilización de máximo de recursos.

Puede lograr alta escala para las aplicaciones de servicio tela de dos maneras:

1. Ajuste de escala en el nivel de partición

2. Ajuste de escala en el nivel de nombre de servicio

## <a name="scaling-at-the-partition-level"></a>Ajuste de escala en el nivel de partición
Servicio tela admite particiones un servicio individual en varias particiones más pequeñas. Las [particiones Introducción](service-fabric-concepts-partitioning.md) proporciona información sobre los tipos de esquemas de particiones que son compatibles. Las réplicas de cada partición se extienden a través de los nodos en un clúster. Considere la posibilidad de un servicio que utiliza un esquema de particiones intervalos con una clave de baja de 0, una clave de alta de 99 y cuatro particiones. En un clúster de tres nodos, el servicio podría ser diseñado con cuatro réplicas que comparten los recursos de cada nodo como se muestra aquí:

![Diseño de partición con tres nodos](./media/service-fabric-concepts-scalability/layout-three-nodes.png)

Aumentar el número de nodos permite tela de servicio utilizar los recursos en los nodos nuevos al mover algunas de las réplicas a nodos vacíos. Aumentar el número de nodos en cuatro, el servicio ahora tiene tres réplicas que se ejecutan en cada nodo (de particiones diferentes), lo que permite rendimiento y utilización de los recursos.

![Diseño de partición con cuatro nodos](./media/service-fabric-concepts-scalability/layout-four-nodes.png)

## <a name="scaling-at-the-service-name-level"></a>Ajuste de escala en el nivel de nombre de servicio
Una instancia de servicio es una instancia específica de un nombre de aplicación y un nombre de tipo de servicio (consulte [ciclo de vida de aplicación de servicio tela](service-fabric-application-lifecycle.md)). Durante la creación de un servicio, especifique la partición de esquema (vea [Servicios de particiones tela de servicio](service-fabric-concepts-partitioning.md)) para usarse.

Es el primer nivel de escala por nombre de servicio. Puede crear nuevas instancias de un servicio con diferentes niveles de particiones, como las instancias de servicio anteriores se convierten en ocupado. Esto permite nuevo servicio a los consumidores utilizar instancias de servicio no disponible menos, en lugar de demasiado ocupado.

Una opción para aumentar la capacidad, así como recuentos de partición ascendente o descendente, es crear una nueva instancia de servicio con un nuevo esquema de partición. Esto agrega complejidad, sin embargo, como cualquier necesidad de clientes que saber cuándo y cómo usar el servicio con un nombre distinto.

### <a name="example-scenario-embedded-dates"></a>Escenario de ejemplo: incrustados fechas
Un escenario posible sería usar información de fecha como parte del nombre del servicio. Por ejemplo, podría usar una instancia de servicio con un nombre específico para todos los clientes que se unen en 2013 y otro nombre para los clientes que se unen en 2014. Este esquema de nomenclatura permite mediante programación aumentar los nombres según la fecha (como enfoques de 2014, la instancia de servicio de 2014 puede crearse a petición).

Sin embargo, este enfoque se basa en los clientes con información nombres específicos de la aplicación que está fuera del ámbito de conocimiento de la estructura de servicio.

- *Usar una convención de nomenclatura*: en 2013, cuando la aplicación pase directo, cree un servicio denominado tela: / aplicaciones/service2013. Cerca del segundo trimestre de 2013, crear otro servicio, denominado tela: / aplicaciones/service2014. Ambos servicios son del mismo tipo de servicio. En este método, su cliente tendrán que emplear lógica para construir el nombre de servicio apropiado según el año.

- *Usar un servicio de búsqueda*: otro patrón es ofrecer un servicio de búsqueda secundaria, que puede proporcionar el nombre del servicio para una clave que desee. A continuación, pueden crearse nuevas instancias de servicio en el servicio de búsqueda. El servicio de búsqueda no conserva los datos de aplicación, solo los datos sobre los nombres de servicio que crea. Por lo tanto, en el ejemplo basado en el año anterior, el cliente ' d Póngase en contacto con el servicio de búsqueda para buscar el nombre del servicio de administración de datos para un año determinado en primer lugar y, a continuación, usar ese nombre de servicio para realizar la operación real. Puede almacenarse en caché el resultado de la búsqueda de la primera.

## <a name="next-steps"></a>Pasos siguientes

Para obtener más información sobre los conceptos de tela de servicio, consulte lo siguiente:

- [Disponibilidad de los servicios de tela de servicio](service-fabric-availability-services.md)

- [Partición servicios estructurales de servicio](service-fabric-concepts-partitioning.md)

- [Definir y administrar el estado](service-fabric-concepts-state.md)
