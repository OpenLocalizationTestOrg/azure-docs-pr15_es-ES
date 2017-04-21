<properties
   pageTitle="Planear la capacidad de clúster de estructura de servicio | Microsoft Azure"
   description="Capacidad de clúster de estructura de servicio consideraciones de diseño. Niveles de NodeTypes, duración y confiabilidad"
   services="service-fabric"
   documentationCenter=".net"
   authors="ChackDan"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/09/2016"
   ms.author="chackdan"/>


# <a name="service-fabric-cluster-capacity-planning-considerations"></a>Consideraciones de planeación de la capacidad de servicio tela clúster

Para cualquier implementación de producción, la planificación de capacidad es un paso importante. Estos algunos de los elementos que debe tener en cuenta como parte de ese proceso.

- El número de tipos de nodo que debe empezar con el clúster
- Las propiedades de cada tipo de nodo (tamaño, principal, internet opuestas, número de máquinas virtuales, etcetera).
- Las características de confiabilidad y la duración del clúster

Deje que nosotros Revise brevemente cada uno de estos elementos.

## <a name="the-number-of-node-types-your-cluster-needs-to-start-out-with"></a>El número de tipos de nodo que debe empezar con el clúster

Primero, tiene que averiguar qué va que se usará para el clúster que va a crear y qué tipos de aplicaciones que va a implementar en este clúster. Si no está claro el propósito del clúster, más probable es que todavía no está listo para escribir el proceso de planificación de capacidad.

Establecer el número de tipos de nodo que debe empezar con el clúster.  Cada tipo de nodo se asigna a un conjunto de escala de máquina Virtual. Cada tipo de nodo puede ampliarse hacia arriba o hacia abajo por separado, tener diferentes conjuntos de puertos abiertos y puede tener métricas de capacidad diferente. Por lo tanto la Decisión del número de tipos de nodo esencialmente trata los siguientes aspectos:

- ¿Su aplicación tiene varios servicios y cualquiera de ellos, necesite ser público o a través de internet? Las aplicaciones típicas contienen un servicio de puerta de enlace cliente que recibe la entrada de un cliente y uno o más servicios back-end que comunican con los servicios front-end. En este caso, así que terminen tener al menos dos tipos de nodo.

- ¿Los servicios (que componen la aplicación) tienen necesidades de infraestructura diferente como más de RAM o más ciclos de CPU? Por ejemplo, supongamos que la aplicación que desea implementar contiene un servicio front-end y un servicio de back-end. El servicio front-end puede ejecutarse en máquinas virtuales más pequeñas (tamaños VM como D2) que tienen puertos abierto a internet.  El servicio de back-end, sin embargo, se debe ejecutar en máquinas virtuales más grandes (con tamaños VM como D4, D6, D15) que no son internet y cálculo opuestas.

 En este ejemplo, aunque puede decidir poner todos los servicios en el tipo de un nodo, se recomienda colocarlos en un clúster con dos tipos de nodo.  Esto permite para que cada tipo de nodo puede tener distintas propiedades, como la conexión a internet o el tamaño de la máquina virtual. El número de máquinas virtuales puede ampliarse de forma independiente, así como.  

- Puesto que no puede predecir el futuro, vaya con hechos que conoce y decida el número de tipos de nodo que necesitan para empezar con las aplicaciones. Siempre puede agregar o quitar tipos de nodo más adelante. Un clúster de tela de servicio debe tener el tipo de al menos un nodo.

## <a name="the-properties-of-each-node-type"></a>Las propiedades de cada tipo de nodo

El **tipo de nodo** puede verse como equivalente a roles en servicios en la nube. Tipos de nodo definen los tamaños VM, el número de máquinas virtuales y sus propiedades. Todos los tipos de nodo se definen en un clúster de servicio tela está configurado como un conjunto de escala de máquina Virtual independiente. Conjuntos de escala de VM son un Azure calcular recursos que puede usar para implementar y administrar una colección de máquinas virtuales como un conjunto. Está definida como distinct VM escala establece, cada tipo de nodo puede ampliarse hacia arriba o hacia abajo por separado, tiene diferentes conjuntos de puertos abiertos y puede tener métricas de capacidad diferente.

El clúster puede tener más de un tipo de nodo, pero el tipo de nodo principal (el primer uno que defina en el portal) debe tener al menos cinco máquinas virtuales para clústeres utilizados para cargas de trabajo de producción (o al menos tres máquinas virtuales para clústeres de prueba). Si va a crear el clúster mediante una plantilla de administrador de recursos, encontrará un atributo **principal** en la definición de tipo de nodo. El tipo de nodo principal es el tipo de nodo donde se colocan los servicios del sistema de servicio tela.  

### <a name="primary-node-type"></a>Tipo de nodo principal
Para un clúster con varios tipos de nodo, debe elegir uno de ellos como principal. Estas son las características de un tipo de nodo principal:

- El tamaño mínimo de VM para el tipo de nodo principal se determina por el nivel de duración que elija. El valor predeterminado para el nivel de duración es Bronce. Desplácese hacia abajo para obtener más información en el nivel de duración y los valores que puede tomar.  

- El número mínimo de máquinas virtuales para el tipo de nodo principal es determinado por el nivel de confiabilidad que elija. El valor predeterminado para el nivel de confiabilidad es plata. Desplácese hacia abajo para obtener más información en el nivel de confiabilidad y los valores que puede tomar.

- Los servicios del sistema tela de servicio (por ejemplo, el servicio de administrador de clúster o servicio de almacenamiento de la imagen) se ubican en el tipo de nodo principal y por tanto la confiabilidad y la duración del clúster se determina por el valor de nivel de confiabilidad y el valor de nivel de duración que seleccione para el tipo de nodo principal.

![Captura de pantalla de un clúster que tiene dos tipos de nodo ][SystemServices]


### <a name="non-primary-node-type"></a>Tipo de nodo no primarios
Para un clúster con varios tipos de nodo, hay un tipo de nodo principal y el resto de ellas no principal. Estas son las características de un tipo de nodo no principal:

- Tamaño mínimo de máquinas virtuales de este tipo de nodo es determinado por el nivel de duración que elija. El valor predeterminado para el nivel de duración es Bronce. Desplácese hacia abajo para obtener más información en el nivel de duración y los valores que puede tomar.  

- El número mínimo de máquinas virtuales de este tipo de nodo puede ser una. Sin embargo, debe elegir este número en función del número de réplicas de aplicaciones y servicios que desea ejecutar en este tipo de nodo. Después de implementar el clúster puede aumentar el número de máquinas virtuales de un tipo de nodo.


## <a name="the-durability-characteristics-of-the-cluster"></a>Las características de la duración del clúster

El nivel de duración se utiliza para indicar al sistema los privilegios de sus máquinas virtuales con la infraestructura de Azure subyacente. En el tipo de nodo principal, este privilegio permite tela de servicio pausar cualquier solicitud de infraestructura VM (por ejemplo, un reinicio de la máquina virtual, crear una nueva imagen de VM o migración de VM) que afectan a los requisitos de quórum para los servicios de sistema y su estado. En los tipos de nodo no principal, este privilegio permite tela de servicio pausar cualquier solicitud de infraestructura VM como reinicio de la máquina virtual, crear una nueva imagen de VM, migración de VM etc., que afectan a los requisitos de quórum para los servicios con estado ejecutando en él.

Este privilegio se expresa en los siguientes valores:

- Se puede pausar oro - los trabajos de infraestructura durante un período de 2 horas por ud.

- Se puede pausar plata - los trabajos de infraestructura durante un período de 30 minutos por ud. (Esto actualmente no está habilitado para su uso. Una vez activada esta opción estará disponible en todas las VM estándar de núcleo único y superior).

- Bronce - sin privilegios. Esto es el valor predeterminado.

## <a name="the-reliability-characteristics-of-the-cluster"></a>Las características de confiabilidad de clúster

El nivel de confiabilidad se usa para establecer el número de réplicas de los servicios del sistema que desea ejecutar en el clúster en el tipo de nodo principal. Más el número de réplicas, más fiable son los servicios del sistema en el clúster.  

El nivel de confiabilidad puede tomar los siguientes valores.

- Platinum - ejecutar los servicios del sistema con un destino de réplica establece recuento de 9

- Oro - ejecutar los servicios del sistema con un destino de réplica establece recuento de 7

- Plata - ejecutar los servicios del sistema con un destino de réplica establece recuento de 5

- Bronce - ejecutar los servicios del sistema con un destino de réplica establece recuento de 3

>[AZURE.NOTE] El nivel de confiabilidad que elija determina el número mínimo de nodos que debe tener el tipo de nodo principal. El nivel de confiabilidad no tiene relación con el tamaño máximo del clúster. Para que puedan tener un clúster de 20 nodo, que se ejecuta en confiabilidad Bronze.

 Puede actualizar la confiabilidad de un clúster de un nivel. Esto se activarán las actualizaciones de clúster es necesario cambiar la réplica de servicios de sistema establecer recuento. Espere a que la actualización en curso para completar antes de realizar otros cambios en el clúster, como agregar nodos etcetera.  Puede supervisar el progreso de la actualización de servicio tela Explorer o ejecutando [Get-ServiceFabricClusterUpgrade](https://msdn.microsoft.com/library/mt126012.aspx)

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Pasos siguientes

Una vez que termine la planificación de capacidad y configurar un clúster, lea lo siguiente:
- [Seguridad de clúster de tela de servicio](service-fabric-cluster-security.md)
- [Introducción de modelo de mantenimiento de tela de servicio](service-fabric-health-introduction.md)

<!--Image references-->
[SystemServices]: ./media/service-fabric-cluster-capacity/SystemServices.png
