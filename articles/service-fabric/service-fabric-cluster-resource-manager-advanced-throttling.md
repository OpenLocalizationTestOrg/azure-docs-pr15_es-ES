<properties
   pageTitle="Limitación en el Administrador de recursos de clúster de servicio tela | Microsoft Azure"
   description="Aprenda a configurar los límites de proporcionados por el Administrador de recursos de clúster tela de servicio."
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


# <a name="throttling-the-behavior-of-the-service-fabric-cluster-resource-manager"></a>Limitar el comportamiento del Administrador de recursos en clúster servicio tela
Incluso si ha configurado el Administrador de recursos de clúster correctamente, puede obtener interrumpen el clúster. Por ejemplo podría ser simultánea nodo o fault dominio errores: ¿qué ocurre si ocurrieron durante la actualización? El Administrador de recursos intentará mejor corregir todo, pero en ocasiones, así que desee considerar parada para que el propio clúster tiene una oportunidad de mantener la estabilidad (los nodos que van a vienen hacer volver, las condiciones de red corregir propios, se desarrollan corregidos bits). Para ayudarle a estos tipos de situaciones, el Administrador de recursos de servicio tela clúster incluyen varios límites. Observe que estos límites son bastante problemáticas y, en general, no deben usarse a menos que se ha producido algún cuidado matemáticas acabado alrededor de la cantidad de trabajo paralelo que puede realizar en el clúster, así como un frecuentes realmente necesita responder a estos se ordena de (es decir) eventos planificado nueva configuración realizados (también conocida como: "Muy incorrectas días").

En general, se recomienda evitar muy malas días a través de otras opciones (por ejemplo, las actualizaciones de código normal y evitar sobreprogramación el clúster comenzar) en lugar de limitar el clúster para evitar que el uso de recursos cuando se está intentando corrija a sí mismo). Los límites tienen valores predeterminados que hemos encontrado a través de la experiencia predeterminada de Aceptar, pero probablemente debería echar un vistazo y ajustarlos a su carga real esperada. Dónde debe disponer de un par de límites, aunque significa el clúster tardará más a regular mientras no está demasiado restringir o cargar que el clúster es un procedimiento recomendado que puede determinar que hay casos que (hasta que se puede resolverlo).

##<a name="configuring-the-throttles"></a>Configurar los límites
Se muestran los límites de que se incluyen de forma predeterminada:

-   GlobalMovementThrottleThreshold: controla el número total de los movimientos del clúster sobre algún tiempo (definido como la GlobalMovementThrottleCountingInterval, el valor en segundos)
-   MovementPerPartitionThrottleThreshold: controla el número total de los movimientos para cualquier partición de servicio sobre algún tiempo (MovementPerPartitionThrottleCountingInterval, valor en segundos)

``` xml
<Section Name="PlacementAndLoadBalancing">
     <Parameter Name="GlobalMovementThrottleThreshold" Value="1000" />
     <Parameter Name="GlobalMovementThrottleCountingInterval" Value="600" />
     <Parameter Name="MovementPerPartitionThrottleThreshold" Value="50" />
     <Parameter Name="MovementPerPartitionThrottleCountingInterval" Value="600" />
</Section>
```

Tener en cuenta que significa que dicha operaciones no tener éxito o sería lenta de todos modos mayoría de las veces que hemos visto a clientes usar estos límites ha sido porque ya estaban en un entorno de recursos restringido (como el ancho de banda de red limitada en nodos individuales o discos que no eran hasta los requisitos de réplica paralelo crea que se encontraban en ellos).  En estas situaciones clientes estaban cómodo saber que ha ampliado potencialmente la cantidad de tiempo que se tardarían el clúster llegar a un estado estable, incluidos saber que podría acabar ejecutando en confiabilidad general inferior mientras estaba limitados.

## <a name="next-steps"></a>Pasos siguientes
- Para obtener información sobre cómo el Administrador de recursos de clúster administra y equilibra carga en el clúster, consulte el artículo sobre el [Equilibrio de carga](service-fabric-cluster-resource-manager-balancing.md)
- El Administrador de recursos de clúster tiene muchas opciones para describir el clúster. Para saber más acerca de ellos desproteger en este artículo en la [Descripción de un clúster de tela de servicio](service-fabric-cluster-resource-manager-cluster-description.md)
