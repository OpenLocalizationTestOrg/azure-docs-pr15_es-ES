<properties
   pageTitle="Administrador de recursos de clúster de tela servicio - afinidad | Microsoft Azure"
   description="Información general de la configuración de afinidad para los servicios de tela de servicio"
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

# <a name="configuring-and-using-service-affinity-in-service-fabric"></a>Configurar y utilizar la afinidad de servicio en tela de servicio

La afinidad es un control que se proporciona principalmente para facilitar la transición de aplicaciones monolíticas más grandes en el mundo de la nube y microservices. Que dice que también puede utilizarse en algunos casos la optimización legítimos para mejorar el rendimiento de servicios, aunque esto puede tener efectos secundarios.

Supongamos que utiliza una aplicación más grande o que simplemente no se ha diseñado con microservices en cuenta al servicio tela. Esta transición es realmente comunes y, a continuación, tenemos varios clientes (internos y externos) en esta situación. Para empezar, levantar la aplicación en el entorno, obtenerlo empaquetados y se ejecuta todo. A continuación, inicie dividirla en distintos servicios más pequeños que todos hablar entre sí.

Hay un "queja...". "Queja" normalmente pertenece a una de estas categorías:

1. Algunos componentes X en la aplicación monolítica tenían una dependencia documentada en componente Y y solo se activa en los distintos servicios. Puesto que estas ahora se ejecutan en distintos nodos en el clúster, están rotos.
2.  Estas cosas comunican a través de (canalizaciones con nombre locales | memoria compartida | archivos en disco), pero necesita realmente puedan actualizar de forma independiente para agilizar las cosas un poco. Más adelante deberá quitar la dependencia de disco dura.
3.  Todo lo que está bien, pero resulta que estos dos componentes son realmente muy conversadoras/rendimiento confidencial. Cuando mueven en servicios independientes de rendimiento de la aplicación tanked o latencia incrementa general. Como resultado, toda la solicitud no cumple las expectativas.

En estos casos que no desea perder nuestro trabajo refactorización y no desea volver a monolito, pero se necesita algunos sentido de ubicación. Esto se conservan hasta que nos podemos diseñar los componentes de naturalmente funcionan como servicios o hasta que podemos resolver las expectativas de rendimiento algún otro modo, si es posible.

¿Qué hacer? También puede intentar activar afinidad.

## <a name="how-to-configure-affinity"></a>Cómo configurar la afinidad
Para configurar la afinidad, defina una relación de afinidad entre dos servicios distintos. Puede considerar que afinidad como "que señala" en un servicio en otro y que indica "este servicio puede ejecutar sólo donde que se está ejecutando el servicio." A veces nos referimos a afinidad como una relación de primario-secundario (donde elemento secundario en el elemento primario). Afinidad garantiza que la réplicas o instancias de un servicio están colocadas en los nodos del mismos como réplicas o instancias de otro.

``` csharp
ServiceCorrelationDescription affinityDescription = new ServiceCorrelationDescription();
affinityDescription.Scheme = ServiceCorrelationScheme.Affinity;
affinityDescription.ServiceName = new Uri("fabric:/otherApplication/parentService");
serviceDescription.Correlations.Add(affinityDescription);
await fabricClient.ServiceManager.CreateServiceAsync(serviceDescription);
```

## <a name="different-affinity-options"></a>Opciones de afinidad diferente
Afinidad se representa mediante uno de varios esquemas de correlación y tiene dos modos distintos. El modo más común de afinidad es lo que denominamos NonAlignedAffinity. En NonAlignedAffinity la réplicas o instancias de los distintos servicios se colocan en los nodos del mismos. Otro modo es AlignedAffinity. Afinidad alineada es útil solo con los servicios de estado. Configuración de dos servicios con estado para han alineado afinidad garantiza que se colocan los primarios de estos servicios en los nodos del mismos como entre sí. También hace que cada pareja de secundarios para estos servicios colocar en los nodos del mismos. También es posible (aunque menos común) configurar NonAlignedAffinity para los servicios con estado. Para NonAlignedAffinity las réplicas diferentes de los dos servicios con estado se podrían ubicadas en los nodos del mismos, pero no se realizará ningún intento para alinear sus primarios y secundarios.

![Modos de afinidad y sus efectos][Image1]

### <a name="best-effort-desired-state"></a>Estado de mejor esfuerzo deseado
Existen algunas diferencias entre arquitecturas monolíticas y afinidad. Muchos de ellos son porque una relación afinidad es la mejor opción. Los servicios de una relación de afinidad son entidades muy diferentes que pueden fallar y mover de forma independiente. También son las causas de por qué podría romper una relación de afinidad. Por ejemplo, las limitaciones de capacidad donde sólo algunos de los objetos de servicio en la relación de afinidad pueden caber en un nodo determinado. En estos casos incluso si hay una relación de afinidad en su lugar, se no se puede aplicar debido a las otras restricciones. Si es posible aplicar todas las otras delimitaciones y afinidad tarde la infracción de la restricción afinidad se corregirán automáticamente.  

### <a name="chains-vs-stars"></a>Cadenas y estrellas
Hoy bien no podemos a cadenas modelo de relaciones de afinidad. Esto significa que es un servicio que es un elemento secundario en una relación de afinidad no puede ser un elemento primario en otra relación de afinidad. Si desea este tipo de relación del modelo, debe eficaz modelo como una estrella en lugar de una cadena. Para ello, el secundario inferior ' d se primarios principal del niño "central" en su lugar. Dependiendo de la disposición de los servicios, esto puede requerir la creación de un servicio de "marcador" para servir el contenido primario de varios elementos secundarios.

![Cadenas frente estrellas en el contexto de las relaciones de afinidad][Image2]

Otra cosa Nota acerca de las relaciones de afinidad hoy es que son direcciones. Esto significa que la regla "afinidad" solo exige que el elemento secundario es donde el elemento primario. Si por ejemplo primario repente falla sobre a otro nodo, a continuación, el Administrador de recursos de clúster no realmente considerar que no hay ningún error hasta que se observa que el elemento secundario no se encuentra con un elemento primario; la relación no se aplica inmediatamente.

### <a name="partitioning-support"></a>Compatibilidad con particiones
La última debe tener en cuenta sobre afinidad es que no se admiten relaciones donde está dividido primario la afinidad. Esto es algo que podemos admitimos finalmente, pero actualmente no está permitido.

## <a name="next-steps"></a>Pasos siguientes
- Para obtener más información acerca de las opciones disponibles para configurar los servicios, vea el tema sobre las otras configuraciones del Administrador de recursos de clúster disponibles [Obtenga más información sobre la configuración de servicios](service-fabric-cluster-resource-manager-configure-services.md)
- Muchas razones donde los usuarios usar afinidad, como la limitación de servicios a un pequeño conjunto de equipos e intenta agregar la carga de una colección de servicios, se admiten mejor a través de grupos de aplicación. Consulte [Grupos de aplicaciones](service-fabric-cluster-resource-manager-application-groups.md)

[Image1]:./media/service-fabric-cluster-resource-manager-advanced-placement-rules-affinity/cluster-resrouce-manager-affinity-modes.png
[Image2]:./media/service-fabric-cluster-resource-manager-advanced-placement-rules-affinity/cluster-resource-manager-chains-vs-stars.png
