<properties
   pageTitle="Administrador de recursos de clúster de tela servicio - directivas de colocación | Microsoft Azure"
   description="Información general sobre las políticas de ubicación adicionales y reglas para los servicios de tela de servicio"
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

# <a name="placement-policies-for-service-fabric-services"></a>Políticas de ubicación para los servicios de tela de servicio
Hay muchos diferentes reglas adicionales que puede terminar preocuparse por si se distribuye el clúster de servicio tela a una distancia geográfica, diga varios centros de datos o regiones Azure, o si su entorno abarca varias áreas de control geopolíticos (o en algunos casos donde tiene límites legal o directiva que le interesa o la distancia que implica tener impacto en el rendimiento y latencia real). La mayoría de ellos podría configurarse a través de propiedades de nodo y las restricciones de posición, pero algunos son más complicadas. Para hacer las cosas más simples proporcionamos a estas commmands adicionales. Igual que con otras restricciones de colocación, las políticas de ubicación se pueden configurar en una base de instancia denominado por servicio.

## <a name="specifying-invalid-domains"></a>Especificar dominios no válidos
La directiva de colocación InvalidDomain le permite especificar que un dominio de error determinada es válido para esta carga de trabajo. Esta directiva garantiza que un servicio en particular nunca se ejecuta en un área determinada, por ejemplo por motivos de directiva geopolíticas o corporativa. Se pueden especificar varios dominios no válidos a través de directivas independientes.

![Ejemplo de dominio no válido][Image1]

Código:

```csharp
ServicePlacementInvalidDomainPolicyDescription invalidDomain = new ServicePlacementInvalidDomainPolicyDescription();
invalidDomain.DomainName = "fd:/DCEast"; //regulations prohibit this workload here
serviceDescription.PlacementPolicies.Add(invalidDomain);
```

PowerShell:

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 2 -TargetReplicaSetSize 3 -PartitionSchemeSingleton -PlacementPolicy @("InvalidDomain,fd:/DCEast”)
```
## <a name="specifying-required-domains"></a>Especificar dominios necesarios
La directiva de colocación de dominio requerido requiere que todas las réplicas con estado o instancias del servicio de estado del servicio en el dominio especificado. Pueden especificar varios dominios necesarios a través de directivas independientes.

![Ejemplo de dominio requerido][Image2]

Código:

```csharp
ServicePlacementRequiredDomainPolicyDescription requiredDomain = new ServicePlacementRequiredDomainPolicyDescription();
requiredDomain.DomainName = "fd:/DC01/RK03/BL2";
serviceDescription.PlacementPolicies.Add(requiredDomain);
```

PowerShell:

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 2 -TargetReplicaSetSize 3 -PartitionSchemeSingleton -PlacementPolicy @("RequiredDomain,fd:/DC01/RK03/BL2")
```

## <a name="specifying-a-preferred-domain-for-the-primary-replicas"></a>Especificar el dominio preferido para las réplicas principales
El dominio principal preferido es un control interesante, ya que permite la selección del dominio de errores en el que se colocarán principal si es posible hacerlo. Cuando todo está correcto principal finalizará en este dominio. Debe el dominio o el error de réplica principal o apagar por algún motivo que principal se migrarán a otra ubicación. Si esta ubicación no está en el dominio preferido, a continuación, cuando sea posible el Administrador de recursos de clúster se devolverlo al dominio preferido. Naturalmente esta configuración sólo tiene sentido para los servicios con estado. Esta directiva es muy útil en clústeres que están distribuidos en Azure regiones o varios centros de datos. En estas situaciones usa todas las ubicaciones para redundancia, pero preferiría colocar las réplicas principales en una ubicación determinada para proporcionar menor latencia para operaciones que vaya a principal (escribe y también de forma predeterminada todas las lecturas sirve principal).

![Migración tras error y dominios principales preferidos][Image3]

```csharp
ServicePlacementPreferPrimaryDomainPolicyDescription primaryDomain = new ServicePlacementPreferPrimaryDomainPolicyDescription();
primaryDomain.DomainName = "fd:/EastUS/";
serviceDescription.PlacementPolicies.Add(invalidDomain);
```

PowerShell:

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 2 -TargetReplicaSetSize 3 -PartitionSchemeSingleton -PlacementPolicy @("PreferredPrimaryDomain,fd:/EastUS")
```

## <a name="requiring-replicas-to-be-distributed-among-all-domains-and-disallowing-packing"></a>Requerir réplicas se distribuya entre todos los dominios y no permitiendo embalaje
Otra directiva que puede especificar es requieren réplicas siempre se distribuya entre los dominios de errores disponibles. Esto ocurre de forma predeterminada, en la mayoría de los casos donde el clúster es correcto, pero hay dañados casos donde réplicas para una partición determinada puede acabar temporalmente empaquetadas en un único errores o dominio de actualización. Por ejemplo, supongamos aunque el clúster tiene 9 nodos en dominios de error 3 (0, 1 y 2) y su servicio tiene 3 réplicas, los nodos que se utilizaron para esas réplicas en errores dominios 1 y 2 se desactivó y debido a problemas de capacidad ninguno de los otros nodos en esos dominios son válido. Si servicio tela eran crear sustituciones de dichas réplicas, el Administrador de recursos de clúster tendría que colocarlos en el dominio de error 0, pero que crea una situación donde se han infringido la restricción del dominio de error. También aumenta la posibilidad de que el conjunto completo de réplica podría perderse (si se han FD 0 se permananently perdido). (Para obtener más información sobre las restricciones y restricción prioridades en general, consulte [este tema](service-fabric-cluster-resource-manager-management-integration.md#constraint-priorities) )

Si alguna vez ha visto una advertencia de estado como "el equilibrador de carga ha detectado una infracción de restricción de esta réplica: tela: /<some service name> partición secundaria <some partition ID> es infringir la restricción: FaultDomain" se ha encontrado esta condición o algo parecido. Normalmente, estas situaciones son transitorias (los nodos no mantenerse presionada mucho tiempo o si lo hacen y es necesario crear sustituciones hay son otros nodos en los dominios de error correcto que son válidos), pero hay algunas cargas de trabajo que prefiere ¿comercio disponibilidad para el riesgo de perder todos sus réplicas. Para ello especificando la directiva de "RequireDomainDistribution", lo que garantizará que dos réplicas no desde la misma partición nunca se permiten en el mismo dominio errores o actualizar.

Algunas cargas de trabajo prefieren tener el número de destino de réplicas (copias de estado) en todo momento (apuestas contra errores de dominio total y saber que normalmente puede recuperar estado local), mientras que otras personas en su lugar tardarían el tiempo de inactividad anteriores a evitar el riesgo de las preocupaciones corrección y dataloss. Puesto que la mayoría de las cargas de producción ejecuta con más de 3 réplicas, el valor predeterminado es no requieren distribución de dominio y permitir que Equilibrio y migración tras error controlar casos normalmente, incluso si eso significa que temporalmente un dominio tiene varias réplicas empaquetadas en él.

Código:

```csharp
ServicePlacementRequireDomainDistributionPolicyDescription distributeDomain = new ServicePlacementRequireDomainDistributionPolicyDescription();
serviceDescription.PlacementPolicies.Add(distributeDomain);
```

PowerShell:

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 2 -TargetReplicaSetSize 3 -PartitionSchemeSingleton -PlacementPolicy @("RequiredDomainDistribution")
```

¿Ahora, sería posible utilizar estas configuraciones para servicios en un clúster que no se ha distribuido geográfica? ¡Sabe que podría! Pero no hay una buena razón demasiado: deben evitar especialmente las configuraciones de dominio requerido, no válido y preferido a menos que realmente está ejecutando un clúster geográfico distribuido: no tiene ningún sentido para intentar forzar una carga de trabajo determinado para ejecutar en un único bastidor o prefiere algún segmento de su clúster local sobre otro a menos que hay diferentes tipos de cargas de trabajo o de hardware segmentación ocurriendo , y esos casos pueden corregirse mediante restricciones de posición normal.

## <a name="next-steps"></a>Pasos siguientes
- Para obtener más información acerca de las opciones disponibles para configurar los servicios, vea el tema sobre las otras configuraciones del Administrador de recursos de clúster disponibles [Obtenga más información sobre la configuración de servicios](service-fabric-cluster-resource-manager-configure-services.md)

[Image1]:./media/service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies/cluster-invalid-placement-domain.png
[Image2]:./media/service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies/cluster-required-placement-domain.png
[Image3]:./media/service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies/cluster-preferred-primary-domain.png
