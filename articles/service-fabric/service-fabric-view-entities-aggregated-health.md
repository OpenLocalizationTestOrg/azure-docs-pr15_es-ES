<properties
   pageTitle="Cómo ver las entidades de Azure servicio tela agregado estado | Microsoft Azure"
   description="Describe cómo consultar, ver y evaluar agregado, mantenimiento las entidades de Azure servicio tela a través de consultas de salud y general."
   services="service-fabric"
   documentationCenter=".net"
   authors="oanapl"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/28/2016"
   ms.author="oanapl"/>

# <a name="view-service-fabric-health-reports"></a>Ver informes de mantenimiento de tela de servicio
Azure tela servicio presenta un [modelo de estado](service-fabric-health-introduction.md) que incluye entidades de salud sistema componentes y watchdogs puede informar de las condiciones locales que va a supervisar. El [estado almacenar](service-fabric-health-introduction.md#health-store) agrega todos los datos de estado para determinar la entidades correcta.

En el cuadro, el clúster se rellena con informes de estado enviados por los componentes del sistema. Más en [informes de estado de sistema de uso para solucionar problemas](service-fabric-understand-and-troubleshoot-with-system-health-reports.md).

Servicio tela proporciona varias maneras de obtener el estado de las entidades agregado:

- [Servicio tela Explorer](service-fabric-visualizing-your-cluster.md) u otras herramientas de visualización

- Consultas de estado (a través de PowerShell, API o resto)

- General consulta devuelto es una lista de entidades que tiene el estado como una de las propiedades (mediante PowerShell, API o resto)

Para mostrar estas opciones, vamos a usar un clúster local con cinco nodos. Junto a la **tela: / sistema** aplicación (que se encuentra fuera de la caja), se implementan algunas otras aplicaciones. Una de estas aplicaciones es **tela: / WordCount**. Esta aplicación contiene un servicio con estado configurado con siete réplicas. Porque hay solo cinco nodos, los componentes del sistema muestran una advertencia de que la partición es inferior al recuento de destino.

```xml
<Service Name="WordCountService">
    <StatefulService ServiceTypeName="WordCountServiceType" TargetReplicaSetSize="7" MinReplicaSetSize="2">
      <UniformInt64Partition PartitionCount="1" LowKey="1" HighKey="26" />
    </StatefulService>
</Service>
```

## <a name="health-in-service-fabric-explorer"></a>Estado en el Explorador de tela de servicio
Explorador de tela de servicio proporciona una vista visual del clúster. En la imagen siguiente, puede ver:

- La aplicación **tela: / WordCount** es rojo (de error) porque tiene un evento de error informado **MyWatchdog** para la **disponibilidad**de la propiedad.

- Uno de sus servicios, **tela: / WordCount/WordCountService** amarillo (de advertencia). Dado que el servicio está configurado con siete réplicas, que no pueden todos colocarse, como han solo cinco nodos. Aunque no se muestra aquí, la partición de servicio es amarilla por el informe del sistema. La partición amarilla activa el servicio amarillo.

- El clúster es rojo debido a la aplicación de color rojo.

La evaluación usa directivas predeterminadas desde el clúster manifiestos y aplicación. Son directivas estrictas y no tolerará ningún error.

Vista del clúster con el Explorador de tela de servicio:

![Vista del clúster con el Explorador de tela de servicio.][1]

[1]: ./media/service-fabric-view-entities-aggregated-health/servicefabric-explorer-cluster-health.png


> [AZURE.NOTE] Más información sobre [El Explorador de tela de servicio](service-fabric-visualizing-your-cluster.md).

## <a name="health-queries"></a>Consultas de estado
Servicio tela expone las consultas de estado para cada uno de los [tipos de entidad](service-fabric-health-introduction.md#health-entities-and-hierarchy)de admitidos. Se puede obtener acceso a través de la API (los métodos pueden encontrarse en **FabricClient.HealthManager**), el cmdlets de PowerShell y el resto. Estas consultas devuelven la información de estado completa acerca de la entidad: el estado de mantenimiento agregado, eventos de estado de la entidad, Estados de salud secundarios (si procede) y evaluaciones mal estadas cuando la entidad no es correcto.

> [AZURE.NOTE] Cuando esté completo en el almacén de estado, se devuelve una entidad de mantenimiento. La entidad debe estar activo (no se eliminan) y un informe de sistema. Sus entidades primario en la cadena de jerarquía también deben tener los informes del sistema. Si no se cumple cualquiera de estas condiciones, las consultas de estado devuelven una excepción que muestra ¿por qué no se devuelve la entidad.

Las consultas de estado deben pasar el identificador de la entidad, que depende del tipo de entidad. Las consultas aceptan parámetros de la directiva de mantenimiento opcional. Si no se especifican ningún directivas de mantenimiento, se usan las [directivas de mantenimiento](service-fabric-health-introduction.md#health-policies) del manifiesto de aplicación o clúster de evaluación. Las consultas también aceptan filtros para devolver solo los niños parciales o eventos, los que respetan los filtros especificados.

> [AZURE.NOTE] Se aplican los filtros de salida en el servidor, por lo que se reduce el tamaño de respuesta del mensaje. Se recomienda que se use los filtros de salida para limitar los datos devueltos, en lugar de aplicar filtros en el cliente.

Estado de la entidad contiene:

- El estado de mantenimiento agregado de la entidad. Calcula el almacén de estado basado en informes de estado de la entidad, Estados de salud secundarios (cuando sea aplicable) y las directivas de mantenimiento. Obtenga más información acerca de la [evaluación de mantenimiento de la entidad](service-fabric-health-introduction.md#entity-health-evaluation).  

- Los eventos de estado de la entidad.

- La colección de Estados de estado de todos los elementos secundarios para las entidades que pueden tener elementos secundarios. Los Estados de salud contienen identificadores de entidad y el estado de mantenimiento agregado. Para obtener el estado completado para un elemento secundario, llamar el estado de consulta para el tipo de entidad secundarios y pase el identificador secundario.

- Las evaluaciones mal estadas que apunten al informe que activa el estado de la entidad, si la entidad no es correcto.

## <a name="get-cluster-health"></a>Obtener estado del clúster
Devuelve el estado de la entidad de clúster y contiene los Estados de mantenimiento de aplicaciones y nodos (elementos secundarios del clúster). Entrada:

- [Opcional] La directiva de mantenimiento de clúster que se utiliza para evaluar los nodos y los eventos de clúster.

- [Opcional] Mapa de directiva de estado de aplicación, con las directivas de estado que se utiliza para reemplazar las directivas de manifiestos de aplicación.

- [Opcional] Filtros de eventos, nodos y aplicaciones que especifican las entradas que son de interés y se deben devolver en el resultado (por ejemplo, sólo, errores o advertencias tanto errores). Todas las aplicaciones, nodos y eventos se usan para evaluar el estado de la entidad agregado, independientemente del filtro.

### <a name="api"></a>API
Para obtener el estado del clúster, cree una `FabricClient` y llame al método [GetClusterHealthAsync](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.healthclient.getclusterhealthasync.aspx) en su **HealthManager**.

La siguiente llamada Obtiene el estado de clúster:

```csharp
ClusterHealth clusterHealth = await fabricClient.HealthManager.GetClusterHealthAsync();
```

El código siguiente obtiene el estado de clúster mediante una directiva de mantenimiento de clúster personalizado y filtros de nodos y aplicaciones. Crea [ClusterHealthQueryDescription](https://msdn.microsoft.com/library/azure/system.fabric.description.clusterhealthquerydescription.aspx), que contiene la información de entrada.

```csharp
var policy = new ClusterHealthPolicy()
{
    MaxPercentUnhealthyNodes = 20
};
var nodesFilter = new NodeHealthStatesFilter()
{
    HealthStateFilterValue = HealthStateFilter.Error | HealthStateFilter.Warning
};
var applicationsFilter = new ApplicationHealthStatesFilter()
{
    HealthStateFilterValue = HealthStateFilter.Error
};
var queryDescription = new ClusterHealthQueryDescription()
{
    HealthPolicy = policy,
    ApplicationsFilter = applicationsFilter,
    NodesFilter = nodesFilter,
};

ClusterHealth clusterHealth = await fabricClient.HealthManager.GetClusterHealthAsync(queryDescription);
```

### <a name="powershell"></a>PowerShell
El cmdlet para obtener el estado de clúster es [Get-ServiceFabricClusterHealth](https://msdn.microsoft.com/library/mt125850.aspx). En primer lugar, conectar con el clúster mediante el cmdlet [ServiceFabricCluster conectar](https://msdn.microsoft.com/library/mt125938.aspx) .

El estado del clúster es cinco nodos, la aplicación del sistema y tela: / WordCount configurado como se describe.

El siguiente cmdlet Obtiene el estado de clúster mediante el uso de directivas de mantenimiento de forma predeterminada. El estado de salud agregado es de advertencia, porque la estructura: aplicación WordCount es advertencia. Observe cómo las evaluaciones mal estadas proporcionan detalles en las condiciones que se activa el estado agregado.

```xml
PS C:\> Get-ServiceFabricClusterHealth

AggregatedHealthState   : Warning
UnhealthyEvaluations    :
                          Unhealthy applications: 100% (1/1), MaxPercentUnhealthyApplications=0%.

                          Unhealthy application: ApplicationName='fabric:/WordCount', AggregatedHealthState='Warning'.

                              Unhealthy services: 100% (1/1), ServiceType='WordCountServiceType', MaxPercentUnhealthyServices=0%.

                              Unhealthy service: ServiceName='fabric:/WordCount/WordCountService', AggregatedHealthState='Warning'.

                                  Unhealthy event: SourceId='System.PLB',
                          Property='ServiceReplicaUnplacedHealth_Secondary_a1f83a35-d6bf-4d39-b90d-28d15f39599b', HealthState='Warning',
                          ConsiderWarningAsError=false.


NodeHealthStates        :
                          NodeName              : _Node_2
                          AggregatedHealthState : Ok

                          NodeName              : _Node_0
                          AggregatedHealthState : Ok

                          NodeName              : _Node_1
                          AggregatedHealthState : Ok

                          NodeName              : _Node_3
                          AggregatedHealthState : Ok

                          NodeName              : _Node_4
                          AggregatedHealthState : Ok

ApplicationHealthStates :
                          ApplicationName       : fabric:/System
                          AggregatedHealthState : Ok

                          ApplicationName       : fabric:/WordCount
                          AggregatedHealthState : Warning

HealthEvents            : None
```

El siguiente cmdlet de PowerShell Obtiene el estado del clúster mediante una directiva de aplicación personalizada. Filtra los resultados para obtener solo error o aplicaciones de advertencia y nodos. Como resultado, no se devuelven nodos, como todo buen Estados. Solo la estructura: / aplicación WordCount respeta el filtro de aplicaciones. Dado que la directiva personalizada especifica considerar advertencias como errores de la estructura: / aplicación WordCount, la aplicación se evalúa como error, por lo que es el clúster.

```powershell
PS c:\> $appHealthPolicy = New-Object -TypeName System.Fabric.Health.ApplicationHealthPolicy
$appHealthPolicy.ConsiderWarningAsError = $true
$appHealthPolicyMap = New-Object -TypeName System.Fabric.Health.ApplicationHealthPolicyMap
$appUri1 = New-Object -TypeName System.Uri -ArgumentList "fabric:/WordCount"
$appHealthPolicyMap.Add($appUri1, $appHealthPolicy)
Get-ServiceFabricClusterHealth -ApplicationHealthPolicyMap $appHealthPolicyMap -ApplicationsFilter "Warning,Error" -NodesFilter "Warning,Error"


AggregatedHealthState   : Error
UnhealthyEvaluations    :
                          Unhealthy applications: 100% (1/1), MaxPercentUnhealthyApplications=0%.

                          Unhealthy application: ApplicationName='fabric:/WordCount', AggregatedHealthState='Error'.

                              Unhealthy services: 100% (1/1), ServiceType='WordCountServiceType', MaxPercentUnhealthyServices=0%.

                              Unhealthy service: ServiceName='fabric:/WordCount/WordCountService', AggregatedHealthState='Error'.

                                  Unhealthy event: SourceId='System.PLB',
                          Property='ServiceReplicaUnplacedHealth_Secondary_a1f83a35-d6bf-4d39-b90d-28d15f39599b', HealthState='Warning',
                          ConsiderWarningAsError=true.


NodeHealthStates        : None
ApplicationHealthStates :
                          ApplicationName       : fabric:/WordCount
                          AggregatedHealthState : Error

HealthEvents            : None

```

### <a name="rest"></a>REST
Puede obtener estado de clúster con una [solicitud](https://msdn.microsoft.com/library/azure/dn707669.aspx) o una [solicitud de publicación](https://msdn.microsoft.com/library/azure/dn707696.aspx) que incluya las directivas de mantenimiento que se describe en el cuerpo.

## <a name="get-node-health"></a>Obtener estado de nodo
Devuelve el estado de una entidad de nodo y contiene los eventos de salud informados sobre el nodo. Entrada:

- [Obligatorio] El nombre de nodo que identifica el nodo.

- [Opcional] La configuración de directiva de estado de clúster utilizada para evaluar el estado.

- [Opcional] Filtros para los eventos que especifican las entradas que son de interés y se deben devolver en el resultado (por ejemplo, sólo, errores o advertencias tanto errores). Todos los eventos se usan para evaluar el estado de la entidad agregado, independientemente del filtro.

### <a name="api"></a>API
Para obtener el estado de nodo a través de la API, cree una `FabricClient` y llame al método [GetNodeHealthAsync](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.healthclient.getnodehealthasync.aspx) en su HealthManager.

El código siguiente obtiene el estado del nodo del nombre de nodo especificado:

```csharp
NodeHealth nodeHealth = await fabricClient.HealthManager.GetNodeHealthAsync(nodeName);
```

El código siguiente obtiene el mantenimiento de nodo para el nombre de nodo especificado y atraviesa [NodeHealthQueryDescription](https://msdn.microsoft.com/library/azure/system.fabric.description.nodehealthquerydescription.aspx)en filtro de eventos y directiva personalizada:

```csharp
var queryDescription = new NodeHealthQueryDescription(nodeName)
{
    HealthPolicy = new ClusterHealthPolicy() {  ConsiderWarningAsError = true },
    EventsFilter = new HealthEventsFilter() { HealthStateFilterValue = HealthStateFilter.Warning },
};

NodeHealth nodeHealth = await fabricClient.HealthManager.GetNodeHealthAsync(queryDescription);
```

### <a name="powershell"></a>PowerShell
El cmdlet para obtener el estado de nodo es [Get-ServiceFabricNodeHealth](https://msdn.microsoft.com/library/mt125937.aspx). En primer lugar, conectar con el clúster mediante el cmdlet [ServiceFabricCluster conectar](https://msdn.microsoft.com/library/mt125938.aspx) .
El siguiente cmdlet Obtiene el mantenimiento de nodo mediante el uso de directivas de mantenimiento de forma predeterminada:

```powershell
PS C:\> Get-ServiceFabricNodeHealth _Node_1


NodeName              : _Node_1
AggregatedHealthState : Ok
HealthEvents          :
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 6
                        SentAt                : 3/22/2016 7:47:56 PM
                        ReceivedAt            : 3/22/2016 7:48:19 PM
                        TTL                   : Infinite
                        Description           : Fabric node is up.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Ok = 3/22/2016 7:48:19 PM, LastWarning = 1/1/0001 12:00:00 AM
```

El siguiente cmdlet Obtiene el estado de todos los nodos en el clúster:

```powershell
PS C:\> Get-ServiceFabricNode | Get-ServiceFabricNodeHealth | select NodeName, AggregatedHealthState | ft -AutoSize

NodeName AggregatedHealthState
-------- ---------------------
_Node_2                     Ok
_Node_0                     Ok
_Node_1                     Ok
_Node_3                     Ok
_Node_4                     Ok
```

### <a name="rest"></a>REST
Puede obtener el estado de nodo con una [solicitud](https://msdn.microsoft.com/library/azure/dn707650.aspx) o una [solicitud de publicación](https://msdn.microsoft.com/library/azure/dn707665.aspx) que incluya las directivas de mantenimiento que se describe en el cuerpo.

## <a name="get-application-health"></a>Obtener el estado de la aplicación
Devuelve el estado de una entidad de aplicación. Contiene los Estados de mantenimiento de la aplicación implementada y elementos secundarios del servicio. Entrada:

- [Obligatorio] El nombre de la aplicación (URI) que identifica la aplicación.

- [Opcional] La directiva de mantenimiento de aplicación que se utiliza para reemplazar las directivas de manifiestos de aplicación.

- [Opcional] Filtros de eventos, servicios y aplicaciones de implementada que especifican las entradas que son de interés y se deben devolver en el resultado (por ejemplo, sólo, errores o advertencias tanto errores). Todos los eventos, servicios y aplicaciones distribuidas se usan para evaluar el estado de la entidad agregado, independientemente del filtro.

### <a name="api"></a>API
Para obtener el estado de la aplicación, cree una `FabricClient` y llame al método [GetApplicationHealthAsync](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.healthclient.getapplicationhealthasync.aspx) en su HealthManager.

El código siguiente obtiene el estado de la aplicación para el nombre de aplicación especificado (URI):

```csharp
ApplicationHealth applicationHealth = await fabricClient.HealthManager.GetApplicationHealthAsync(applicationName);
```

El código siguiente obtiene el estado de la aplicación para el nombre de aplicación especificado (URI), con los filtros y directivas personalizadas especificadas a través de [ApplicationHealthQueryDescription](https://msdn.microsoft.com/library/azure/system.fabric.description.applicationhealthquerydescription.aspx).

```csharp
HealthStateFilter warningAndErrors = HealthStateFilter.Error | HealthStateFilter.Warning;
var serviceTypePolicy = new ServiceTypeHealthPolicy()
{
    MaxPercentUnhealthyPartitionsPerService = 0,
    MaxPercentUnhealthyReplicasPerPartition = 5,
    MaxPercentUnhealthyServices = 0,
};
var policy = new ApplicationHealthPolicy()
{
    ConsiderWarningAsError = false,
    DefaultServiceTypeHealthPolicy = serviceTypePolicy,
    MaxPercentUnhealthyDeployedApplications = 0,
};

var queryDescription = new ApplicationHealthQueryDescription(applicationName)
{
    HealthPolicy = policy,
    EventsFilter = new HealthEventsFilter() { HealthStateFilterValue = warningAndErrors },
    ServicesFilter = new ServiceHealthStatesFilter() { HealthStateFilterValue = warningAndErrors },
    DeployedApplicationsFilter = new DeployedApplicationHealthStatesFilter() { HealthStateFilterValue = warningAndErrors },
};

ApplicationHealth applicationHealth = await fabricClient.HealthManager.GetApplicationHealthAsync(queryDescription);
```

### <a name="powershell"></a>PowerShell
El cmdlet para obtener el estado de la aplicación es [Get-ServiceFabricApplicationHealth](https://msdn.microsoft.com/library/mt125976.aspx). En primer lugar, conectar con el clúster mediante el cmdlet [ServiceFabricCluster conectar](https://msdn.microsoft.com/library/mt125938.aspx) .

El siguiente cmdlet devuelve el estado de la **tela: / WordCount** aplicación:

```powershell
PS c:\>
PS C:\WINDOWS\system32>  Get-ServiceFabricApplicationHealth fabric:/WordCount


ApplicationName                 : fabric:/WordCount
AggregatedHealthState           : Warning
UnhealthyEvaluations            :
                                  Unhealthy services: 100% (1/1), ServiceType='WordCountServiceType', MaxPercentUnhealthyServices=0%.

                                  Unhealthy service: ServiceName='fabric:/WordCount/WordCountService', AggregatedHealthState='Warning'.

                                      Unhealthy event: SourceId='System.PLB',
                                  Property='ServiceReplicaUnplacedHealth_Secondary_a1f83a35-d6bf-4d39-b90d-28d15f39599b', HealthState='Warning',
                                  ConsiderWarningAsError=false.

ServiceHealthStates             :
                                  ServiceName           : fabric:/WordCount/WordCountService
                                  AggregatedHealthState : Warning

                                  ServiceName           : fabric:/WordCount/WordCountWebService
                                  AggregatedHealthState : Ok

DeployedApplicationHealthStates :
                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : _Node_0
                                  AggregatedHealthState : Ok

                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : _Node_2
                                  AggregatedHealthState : Ok

                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : _Node_3
                                  AggregatedHealthState : Ok

                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : _Node_4
                                  AggregatedHealthState : Ok

                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : _Node_1
                                  AggregatedHealthState : Ok

HealthEvents                    :
                                  SourceId              : System.CM
                                  Property              : State
                                  HealthState           : Ok
                                  SequenceNumber        : 360
                                  SentAt                : 3/22/2016 7:56:53 PM
                                  ReceivedAt            : 3/22/2016 7:56:53 PM
                                  TTL                   : Infinite
                                  Description           : Application has been created.
                                  RemoveWhenExpired     : False
                                  IsExpired             : False
                                  Transitions           : Error->Ok = 3/22/2016 7:56:53 PM, LastWarning = 1/1/0001 12:00:00 AM

                                  SourceId              : MyWatchdog
                                  Property              : Availability
                                  HealthState           : Ok
                                  SequenceNumber        : 131031545225930951
                                  SentAt                : 3/22/2016 9:08:42 PM
                                  ReceivedAt            : 3/22/2016 9:08:42 PM
                                  TTL                   : Infinite
                                  Description           : Availability checked successfully, latency ok
                                  RemoveWhenExpired     : False
                                  IsExpired             : False
                                  Transitions           : Error->Ok = 3/22/2016 8:55:39 PM, LastWarning = 1/1/0001 12:00:00 AM
```

El siguiente cmdlet de PowerShell pasa directivas personalizadas. También filtra eventos y elementos secundarios.

```powershell
PS C:\> Get-ServiceFabricApplicationHealth -ApplicationName fabric:/WordCount -ConsiderWarningAsError $true -ServicesFilter Error -EventsFilter Error -DeployedApplicationsFilter Error

ApplicationName                 : fabric:/WordCount
AggregatedHealthState           : Error
UnhealthyEvaluations            :
                                  Unhealthy services: 100% (1/1), ServiceType='WordCountServiceType', MaxPercentUnhealthyServices=0%.

                                  Unhealthy service: ServiceName='fabric:/WordCount/WordCountService', AggregatedHealthState='Error'.

                                      Unhealthy event: SourceId='System.PLB',
                                  Property='ServiceReplicaUnplacedHealth_Secondary_a1f83a35-d6bf-4d39-b90d-28d15f39599b', HealthState='Warning',
                                  ConsiderWarningAsError=true.

ServiceHealthStates             :
                                  ServiceName           : fabric:/WordCount/WordCountService
                                  AggregatedHealthState : Error

DeployedApplicationHealthStates : None
HealthEvents                    : None
```

### <a name="rest"></a>REST
Puede obtener el estado de la aplicación con una [solicitud](https://msdn.microsoft.com/library/azure/dn707681.aspx) o una [solicitud de publicación](https://msdn.microsoft.com/library/azure/dn707643.aspx) que incluya las directivas de mantenimiento que se describe en el cuerpo.

## <a name="get-service-health"></a>Obtener estado del servicio
Devuelve el estado de una entidad de servicio. Contiene los Estados de mantenimiento de partición. Entrada:

- [Obligatorio] El nombre de servicio (URI) que identifica el servicio.

- [Opcional] La directiva de mantenimiento de aplicación que se usa para invalidar la directiva de manifiestos de aplicación.

- [Opcional] Filtros de eventos y las particiones que especifican las entradas que son de interés y se deben devolver en el resultado (por ejemplo, sólo, errores o advertencias tanto errores). Todas las particiones y eventos se usan para evaluar el estado de la entidad agregado, independientemente del filtro.

### <a name="api"></a>API
Para obtener el estado del servicio a través de la API, cree una `FabricClient` y llame al método [GetServiceHealthAsync](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.healthclient.getservicehealthasync.aspx) en su HealthManager.

En el ejemplo siguiente se obtiene el estado de un servicio con el nombre de servicio especificado (URI):

```charp
ServiceHealth serviceHealth = await fabricClient.HealthManager.GetServiceHealthAsync(serviceName);
```

El código siguiente obtiene el estado del servicio para el nombre de servicio especificado (URI) que especifica la directiva personalizada a través de [ServiceHealthQueryDescription](https://msdn.microsoft.com/library/azure/system.fabric.description.servicehealthquerydescription.aspx)y filtros:

```csharp
var queryDescription = new ServiceHealthQueryDescription(serviceName)
{
    EventsFilter = new HealthEventsFilter() { HealthStateFilterValue = HealthStateFilter.All },
    PartitionsFilter = new PartitionHealthStatesFilter() { HealthStateFilterValue = HealthStateFilter.Error },
};

ServiceHealth serviceHealth = await fabricClient.HealthManager.GetServiceHealthAsync(queryDescription);
```

### <a name="powershell"></a>PowerShell
El cmdlet para obtener el estado del servicio es [Get-ServiceFabricServiceHealth](https://msdn.microsoft.com/library/mt125984.aspx). En primer lugar, conectar con el clúster mediante el cmdlet [ServiceFabricCluster conectar](https://msdn.microsoft.com/library/mt125938.aspx) .

El siguiente cmdlet Obtiene el estado del servicio mediante el uso de directivas de mantenimiento de forma predeterminada:

```powershell
PS C:\> Get-ServiceFabricServiceHealth -ServiceName fabric:/WordCount/WordCountService


ServiceName           : fabric:/WordCount/WordCountService
AggregatedHealthState : Warning
UnhealthyEvaluations  :
                        Unhealthy event: SourceId='System.PLB',
                        Property='ServiceReplicaUnplacedHealth_Secondary_a1f83a35-d6bf-4d39-b90d-28d15f39599b', HealthState='Warning',
                        ConsiderWarningAsError=false.

PartitionHealthStates :
                        PartitionId           : a1f83a35-d6bf-4d39-b90d-28d15f39599b
                        AggregatedHealthState : Warning

HealthEvents          :
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 10
                        SentAt                : 3/22/2016 7:56:53 PM
                        ReceivedAt            : 3/22/2016 7:57:18 PM
                        TTL                   : Infinite
                        Description           : Service has been created.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Ok = 3/22/2016 7:57:18 PM, LastWarning = 1/1/0001 12:00:00 AM

                        SourceId              : System.PLB
                        Property              : ServiceReplicaUnplacedHealth_Secondary_a1f83a35-d6bf-4d39-b90d-28d15f39599b
                        HealthState           : Warning
                        SequenceNumber        : 131031547693687021
                        SentAt                : 3/22/2016 9:12:49 PM
                        ReceivedAt            : 3/22/2016 9:12:49 PM
                        TTL                   : 00:01:05
                        Description           : The Load Balancer was unable to find a placement for one or more of the Service's Replicas:
                        fabric:/WordCount/WordCountService Secondary Partition a1f83a35-d6bf-4d39-b90d-28d15f39599b could not be placed, possibly,
                        due to the following constraints and properties:  
                        Placement Constraint: N/A
                        Depended Service: N/A

                        Constraint Elimination Sequence:
                        ReplicaExclusionStatic eliminated 4 possible node(s) for placement -- 1/5 node(s) remain.
                        ReplicaExclusionDynamic eliminated 1 possible node(s) for placement -- 0/5 node(s) remain.

                        Nodes Eliminated By Constraints:

                        ReplicaExclusionStatic:
                        FaultDomain:fd:/0 NodeName:_Node_0 NodeType:NodeType0 UpgradeDomain:0 UpgradeDomain: ud:/0 Deactivation Intent/Status:
                        None/None
                        FaultDomain:fd:/1 NodeName:_Node_1 NodeType:NodeType1 UpgradeDomain:1 UpgradeDomain: ud:/1 Deactivation Intent/Status:
                        None/None
                        FaultDomain:fd:/3 NodeName:_Node_3 NodeType:NodeType3 UpgradeDomain:3 UpgradeDomain: ud:/3 Deactivation Intent/Status:
                        None/None
                        FaultDomain:fd:/4 NodeName:_Node_4 NodeType:NodeType4 UpgradeDomain:4 UpgradeDomain: ud:/4 Deactivation Intent/Status:
                        None/None

                        ReplicaExclusionDynamic:
                        FaultDomain:fd:/2 NodeName:_Node_2 NodeType:NodeType2 UpgradeDomain:2 UpgradeDomain: ud:/2 Deactivation Intent/Status:
                        None/None


                        RemoveWhenExpired     : True
                        IsExpired             : False
```

### <a name="rest"></a>REST
Puede obtener con una [solicitud de publicación](https://msdn.microsoft.com/library/azure/dn707646.aspx) que incluya las directivas de mantenimiento que se describe en el cuerpo o una [solicitud de](https://msdn.microsoft.com/library/azure/dn707609.aspx) estado del servicio.

## <a name="get-partition-health"></a>Obtener estado de partición
Devuelve el estado de una entidad de partición. Contiene los Estados de salud de réplica. Entrada:

- [Obligatorio] Identificador (GUID) que identifica la partición partición.

- [Opcional] La directiva de mantenimiento de aplicación que se usa para invalidar la directiva de manifiestos de aplicación.

- [Opcional] Filtros de eventos y réplicas que especifican las entradas que son de interés y se deben devolver en el resultado (por ejemplo, sólo, errores o advertencias tanto errores). Todos los eventos y réplicas se usan para evaluar el estado de la entidad agregado, independientemente del filtro.

### <a name="api"></a>API
Para obtener el estado de partición a través de la API, cree una `FabricClient` y llame al método [GetPartitionHealthAsync](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.healthclient.getpartitionhealthasync.aspx) en su HealthManager. Para especificar los parámetros opcionales, crear [PartitionHealthQueryDescription](https://msdn.microsoft.com/library/azure/system.fabric.description.partitionhealthquerydescription.aspx).

```csharp
PartitionHealth partitionHealth = await fabricClient.HealthManager.GetPartitionHealthAsync(partitionId);
```

### <a name="powershell"></a>PowerShell
El cmdlet para obtener el estado de partición es [Get-ServiceFabricPartitionHealth](https://msdn.microsoft.com/library/mt125869.aspx). En primer lugar, conectar con el clúster mediante el cmdlet [ServiceFabricCluster conectar](https://msdn.microsoft.com/library/mt125938.aspx) .

El siguiente cmdlet Obtiene el estado de todas las particiones de la **tela: / WordCount/WordCountService** servicio:

```powershell
PS C:\> Get-ServiceFabricPartition fabric:/WordCount/WordCountService | Get-ServiceFabricPartitionHealth


PartitionId           : a1f83a35-d6bf-4d39-b90d-28d15f39599b
AggregatedHealthState : Warning
UnhealthyEvaluations  :
                        Unhealthy event: SourceId='System.FM', Property='State', HealthState='Warning', ConsiderWarningAsError=false.

ReplicaHealthStates   :
                        ReplicaId             : 131031502143040223
                        AggregatedHealthState : Ok

                        ReplicaId             : 131031502346844060
                        AggregatedHealthState : Ok

                        ReplicaId             : 131031502346844059
                        AggregatedHealthState : Ok

                        ReplicaId             : 131031502346844061
                        AggregatedHealthState : Ok

                        ReplicaId             : 131031502346844058
                        AggregatedHealthState : Ok

HealthEvents          :
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Warning
                        SequenceNumber        : 76
                        SentAt                : 3/22/2016 7:57:26 PM
                        ReceivedAt            : 3/22/2016 7:57:48 PM
                        TTL                   : Infinite
                        Description           : Partition is below target replica or instance count.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Warning = 3/22/2016 7:57:48 PM, LastOk = 1/1/0001 12:00:00 AM
```

### <a name="rest"></a>REST
Puede obtener estado de partición con una [solicitud](https://msdn.microsoft.com/library/azure/dn707683.aspx) o una [solicitud de publicación](https://msdn.microsoft.com/library/azure/dn707680.aspx) que incluya las directivas de mantenimiento que se describe en el cuerpo.

## <a name="get-replica-health"></a>Obtener estado de réplica
Devuelve el estado de una réplica de estado del servicio o una instancia de estado del servicio. Entrada:

- [Obligatorio] El identificador (GUID) y réplica ID que identifica la réplica.

- [Opcional] Los parámetros de directiva de estado de aplicación utilizados para reemplazar las directivas de manifiestos de aplicación.

- [Opcional] Filtros para los eventos que especifican las entradas que son de interés y se deben devolver en el resultado (por ejemplo, sólo, errores o advertencias tanto errores). Todos los eventos se usan para evaluar el estado de la entidad agregado, independientemente del filtro.

### <a name="api"></a>API
Para obtener el estado de réplica a través de la API, cree una `FabricClient` y llame al método [GetReplicaHealthAsync](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.healthclient.getreplicahealthasync.aspx) en su HealthManager. Para especificar los parámetros avanzados, use [ReplicaHealthQueryDescription](https://msdn.microsoft.com/library/azure/system.fabric.description.replicahealthquerydescription.aspx).

```csharp
ReplicaHealth replicaHealth = await fabricClient.HealthManager.GetReplicaHealthAsync(partitionId, replicaId);
```

### <a name="powershell"></a>PowerShell
El cmdlet para obtener el estado de réplica es [Get-ServiceFabricReplicaHealth](https://msdn.microsoft.com/library/mt125808.aspx). En primer lugar, conectar con el clúster mediante el cmdlet [ServiceFabricCluster conectar](https://msdn.microsoft.com/library/mt125938.aspx) .

El siguiente cmdlet Obtiene el estado de la réplica principal para todas las particiones del servicio:

```powershell
PS C:\> Get-ServiceFabricPartition fabric:/WordCount/WordCountService | Get-ServiceFabricReplica | where {$_.ReplicaRole -eq "Primary"} | Get-ServiceFabricReplicaHealth


PartitionId           : a1f83a35-d6bf-4d39-b90d-28d15f39599b
ReplicaId             : 131031502143040223
AggregatedHealthState : Ok
HealthEvents          :
                        SourceId              : System.RA
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 131031502145556748
                        SentAt                : 3/22/2016 7:56:54 PM
                        ReceivedAt            : 3/22/2016 7:57:12 PM
                        TTL                   : Infinite
                        Description           : Replica has been created.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Ok = 3/22/2016 7:57:12 PM, LastWarning = 1/1/0001 12:00:00 AM
```

### <a name="rest"></a>REST
Puede obtener el estado de réplica con una [solicitud](https://msdn.microsoft.com/library/azure/dn707673.aspx) o una [solicitud de publicación](https://msdn.microsoft.com/library/azure/dn707641.aspx) que incluya las directivas de mantenimiento que se describe en el cuerpo.

## <a name="get-deployed-application-health"></a>Obtener el estado de la aplicación implementada
Devuelve el estado de una aplicación implementada en una entidad de nodo. Contiene los Estados de estado del paquete de servicio implementado. Entrada:

- [Obligatorio] El nombre de la aplicación (URI) y el nombre de nodo (cadena) que identifica la aplicación implementada.

- [Opcional] La directiva de mantenimiento de aplicación que se utiliza para reemplazar las directivas de manifiestos de aplicación.

- [Opcional] Filtros de eventos y paquetes de servicio implementado que especifican las entradas que son de interés y se deben devolver en el resultado (por ejemplo, sólo, errores o advertencias tanto errores). Todos los eventos y paquetes de servicio implementado se usan para evaluar el estado de la entidad agregado, independientemente del filtro.

### <a name="api"></a>API
Para obtener el estado de una aplicación implementada en un nodo a través de la API, cree una `FabricClient` y llame al método [GetDeployedApplicationHealthAsync](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.healthclient.getdeployedapplicationhealthasync.aspx) en su HealthManager. Para especificar los parámetros opcionales, utilice [DeployedApplicationHealthQueryDescription](https://msdn.microsoft.com/library/azure/system.fabric.description.deployedapplicationhealthquerydescription.aspx).

```csharp
DeployedApplicationHealth health = await fabricClient.HealthManager.GetDeployedApplicationHealthAsync(
    new DeployedApplicationHealthQueryDescription(applicationName, nodeName));
```

### <a name="powershell"></a>PowerShell
El cmdlet para obtener el estado de la aplicación implementada es [Get-ServiceFabricDeployedApplicationHealth](https://msdn.microsoft.com/library/mt163523.aspx). En primer lugar, conectar con el clúster mediante el cmdlet [ServiceFabricCluster conectar](https://msdn.microsoft.com/library/mt125938.aspx) . Para averiguar dónde se implementa una aplicación, ejecute [Get-ServiceFabricApplicationHealth](https://msdn.microsoft.com/library/mt125976.aspx) y ver los elementos secundarios de la aplicación implementada.

El siguiente cmdlet Obtiene el estado de la **tela: / WordCount** aplicación implementada en **_Node_2**.

```powershell
PS C:\> Get-ServiceFabricDeployedApplicationHealth -ApplicationName fabric:/WordCount -NodeName _Node_2


ApplicationName                    : fabric:/WordCount
NodeName                           : _Node_2
AggregatedHealthState              : Ok
DeployedServicePackageHealthStates :
                                     ServiceManifestName   : WordCountServicePkg
                                     NodeName              : _Node_2
                                     AggregatedHealthState : Ok

                                     ServiceManifestName   : WordCountWebServicePkg
                                     NodeName              : _Node_2
                                     AggregatedHealthState : Ok

HealthEvents                       :
                                     SourceId              : System.Hosting
                                     Property              : Activation
                                     HealthState           : Ok
                                     SequenceNumber        : 131031502143710698
                                     SentAt                : 3/22/2016 7:56:54 PM
                                     ReceivedAt            : 3/22/2016 7:57:12 PM
                                     TTL                   : Infinite
                                     Description           : The application was activated successfully.
                                     RemoveWhenExpired     : False
                                     IsExpired             : False
                                     Transitions           : Error->Ok = 3/22/2016 7:57:12 PM, LastWarning = 1/1/0001 12:00:00 AM
```

### <a name="rest"></a>REST
Puede obtener el estado de la aplicación implementada con una [solicitud](https://msdn.microsoft.com/library/azure/dn707644.aspx) o una [solicitud de publicación](https://msdn.microsoft.com/library/azure/dn707688.aspx) que incluya las directivas de mantenimiento que se describe en el cuerpo.

## <a name="get-deployed-service-package-health"></a>Obtener estado del servicio implementado paquete
Devuelve el estado de una entidad de paquete de servicio implementado. Entrada:

- [Obligatorio] El nombre de la aplicación (URI), el nombre de nodo (cadena) y el servicio de manifiestos nombre (string) que identificar el paquete de servicio implementado.

- [Opcional] La directiva de mantenimiento de aplicación que se usa para invalidar la directiva de manifiestos de aplicación.

- [Opcional] Filtros para los eventos que especifican las entradas que son de interés y se deben devolver en el resultado (por ejemplo, sólo, errores o advertencias tanto errores). Todos los eventos se usan para evaluar el estado de la entidad agregado, independientemente del filtro.

### <a name="api"></a>API
Para obtener el estado de un paquete de servicio implementado a través de la API, cree una `FabricClient` y llame al método [GetDeployedServicePackageHealthAsync](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.healthclient.getdeployedservicepackagehealthasync.aspx) en su HealthManager. Para especificar los parámetros opcionales, utilice [DeployedServicePackageHealthQueryDescription](https://msdn.microsoft.com/library/azure/system.fabric.description.deployedservicepackagehealthquerydescription.aspx).

```csharp
DeployedServicePackageHealth health = await fabricClient.HealthManager.GetDeployedServicePackageHealthAsync(
    new DeployedServicePackageHealthQueryDescription(applicationName, nodeName, serviceManifestName));
```

### <a name="powershell"></a>PowerShell
El cmdlet para obtener el estado del paquete de servicio implementado es [Get-ServiceFabricDeployedServicePackageHealth](https://msdn.microsoft.com/library/mt163525.aspx). En primer lugar, conectar con el clúster mediante el cmdlet [ServiceFabricCluster conectar](https://msdn.microsoft.com/library/mt125938.aspx) . Para ver dónde se implementa una aplicación, ejecutar [Get-ServiceFabricApplicationHealth](https://msdn.microsoft.com/library/mt125976.aspx) y observe las aplicaciones implementadas. Para ver qué paquetes están en una aplicación de servicio, busque los elementos secundarios de paquete de servicio implementado en el resultado de [Get-ServiceFabricDeployedApplicationHealth](https://msdn.microsoft.com/library/mt163523.aspx) .

El siguiente cmdlet Obtiene el estado del paquete de servicio de **WordCountServicePkg** de la **tela: / WordCount** aplicación implementada en **_Node_2**. La entidad tiene **System.Hosting** informes para correcta paquete de servicio y activación de punto de entrada y registro de tipo de servicio correcta.

```powershell
PS C:\> Get-ServiceFabricDeployedApplication -ApplicationName fabric:/WordCount -NodeName _Node_2 | Get-ServiceFabricDeployedServicePackageHealth -ServiceManifestName WordCountServicePkg


ApplicationName       : fabric:/WordCount
ServiceManifestName   : WordCountServicePkg
NodeName              : _Node_2
AggregatedHealthState : Ok
HealthEvents          :
                        SourceId              : System.Hosting
                        Property              : Activation
                        HealthState           : Ok
                        SequenceNumber        : 131031502301306211
                        SentAt                : 3/22/2016 7:57:10 PM
                        ReceivedAt            : 3/22/2016 7:57:12 PM
                        TTL                   : Infinite
                        Description           : The ServicePackage was activated successfully.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Ok = 3/22/2016 7:57:12 PM, LastWarning = 1/1/0001 12:00:00 AM

                        SourceId              : System.Hosting
                        Property              : CodePackageActivation:Code:EntryPoint
                        HealthState           : Ok
                        SequenceNumber        : 131031502301568982
                        SentAt                : 3/22/2016 7:57:10 PM
                        ReceivedAt            : 3/22/2016 7:57:12 PM
                        TTL                   : Infinite
                        Description           : The CodePackage was activated successfully.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Ok = 3/22/2016 7:57:12 PM, LastWarning = 1/1/0001 12:00:00 AM

                        SourceId              : System.Hosting
                        Property              : ServiceTypeRegistration:WordCountServiceType
                        HealthState           : Ok
                        SequenceNumber        : 131031502314788519
                        SentAt                : 3/22/2016 7:57:11 PM
                        ReceivedAt            : 3/22/2016 7:57:12 PM
                        TTL                   : Infinite
                        Description           : The ServiceType was registered successfully.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Ok = 3/22/2016 7:57:12 PM, LastWarning = 1/1/0001 12:00:00 AM
```

### <a name="rest"></a>REST
Puede obtener el estado del paquete de servicio implementado con una [solicitud](https://msdn.microsoft.com/library/azure/dn707677.aspx) o una [solicitud de publicación](https://msdn.microsoft.com/library/azure/dn707689.aspx) que incluya las directivas de mantenimiento que se describe en el cuerpo.

## <a name="health-chunk-queries"></a>Consultas de fragmento de estado
Las consultas de fragmento de salud pueden devolver a elementos secundarios de clúster multinivel (recursiva) por los filtros de entrada. Es compatible con los filtros avanzados que permiten una flexibilidad expresar qué elementos secundarios específicos que se devolverá, identificado por su identificador único u otros identificador del grupo y el estado de mantenimiento. De forma predeterminada, ningún elemento secundario se incluyen, en lugar de comandos de mantenimiento que incluyen siempre los elementos secundarios de primer nivel.

Las [consultas de estado](service-fabric-view-entities-aggregated-health.md#health-queries) devolver solo elementos secundarios de primer nivel de la entidad especificada por los filtros necesarios. Para obtener a los elementos secundarios de los elementos secundarios, debe llamar a las API de estado adicional para cada entidad de interés. Del mismo modo, para obtener el estado de entidades específicas, debe llamar a un estado API para cada entidad que desee. La consulta de fragmento avanzada de filtrado permite solicitar varios elementos de interés en una consulta, minimizar el tamaño del mensaje y el número de mensajes.

El valor de la consulta de fragmento es que puede obtener estado de mantenimiento de más entidades de clúster (potencialmente todos los clúster entidades empezando por la raíz necesario) en una llamada. Consulta de salud complejas puede expresar como:

- Devolver solo aplicaciones de error y para esas aplicaciones incluyen todos los servicios de advertencia | error. Servicios devueltos, incluya todas las particiones.

- Devolver solo el estado de las 4 aplicaciones, especificada por sus nombres.

- Devolver solo el estado de las aplicaciones de un tipo de aplicación que desee.

- Devolver entidades todo implementadas en un nodo. Devuelve todas las aplicaciones, todos los instalar aplicaciones en el nodo especificado y todos los paquetes de servicio implementado en ese nodo.

- Devolver todas las réplicas en error. Devuelve todas las aplicaciones, servicios, particiones y solo réplicas en error.

- Devolver todas las aplicaciones. Para un servicio específico, incluya todas las particiones.

Actualmente, la consulta de fragmento de estado se expone solo para la entidad de clúster. Devuelve un fragmento de estado de clúster que contiene:

- El estado de mantenimiento de clúster agregado.

- Lista de fragmentos de estado de salud de nodos que respetar los filtros de entrada.

- Lista de fragmentos de estado de mantenimiento de aplicaciones que respetar los filtros de entrada. Cada fragmento de estado de salud de aplicación contiene una lista de fragmentos con todos los servicios que respetan los filtros de entrada y una lista de fragmentos con todas las aplicaciones implementadas que respetar los filtros. El mismo procedimiento para los elementos secundarios de servicios y aplicaciones implementadas. De este modo, todas las entidades de clúster pueden devolverse potencialmente si se solicita, de forma jerárquica.

### <a name="cluster-health-chunk-query"></a>Consulta de fragmento de estado de clúster
Devuelve el estado de la entidad de clúster y contiene los fragmentos de estado de salud jerárquica de elementos secundarios necesarios. Entrada:

- [Opcional] La directiva de mantenimiento de clúster que se utiliza para evaluar los nodos y los eventos de clúster.

- [Opcional] Mapa de directiva de estado de aplicación, con las directivas de estado que se utiliza para reemplazar las directivas de manifiestos de aplicación.

- [Opcional] Filtros de nodos y aplicaciones que especifican las entradas que son de interés y se deben devolver en el resultado. Los filtros son específicos de un entidad o grupo de entidades o son aplicables a todas las entidades en ese nivel. La lista de filtros puede contener un filtro general o filtros para identificadores específicos a entidades detallado devueltos por la consulta. Si está vacío, no se devuelven los elementos secundarios de forma predeterminada.
Obtenga más información acerca de los filtros en [NodeHealthStateFilter](https://msdn.microsoft.com/library/azure/system.fabric.health.nodehealthstatefilter.aspx) y [ApplicationHealthStateFilter](https://msdn.microsoft.com/library/azure/system.fabric.health.applicationhealthstatefilter.aspx). La forma recursiva de aplicación filtros puede especificar filtros avanzados para niños.

El resultado del campo incluye a los elementos secundarios que respetan los filtros.

Actualmente, la consulta de fragmento no devuelve evaluaciones negativos o eventos de entidad. Puede obtener información adicional con la consulta de estado de clúster existente.

### <a name="api"></a>API
Para obtener el fragmento de estado de clúster, cree una `FabricClient` y llame al método [GetClusterHealthChunkAsync](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.healthclient.getclusterhealthchunkasync.aspx) en su **HealthManager**. Puede pasar en [ClusterHealthQueryDescription](https://msdn.microsoft.com/library/azure/system.fabric.description.clusterhealthchunkquerydescription.aspx) para describir las directivas de mantenimiento y filtros avanzados.

El código siguiente obtiene el fragmento de estado de clúster con los filtros avanzados.

```csharp
var queryDescription = new ClusterHealthChunkQueryDescription();
queryDescription.ApplicationFilters.Add(new ApplicationHealthStateFilter()
    {
        // Return applications only if they are in error
        HealthStateFilter = HealthStateFilter.Error
    });

// Return all replicas
var wordCountServiceReplicaFilter = new ReplicaHealthStateFilter()
    {
        HealthStateFilter = HealthStateFilter.All
    };

// Return all replicas and all partitions
var wordCountServicePartitionFilter = new PartitionHealthStateFilter()
    {
        HealthStateFilter = HealthStateFilter.All
    };
wordCountServicePartitionFilter.ReplicaFilters.Add(wordCountServiceReplicaFilter);

// For specific service, return all partitions and all replicas
var wordCountServiceFilter = new ServiceHealthStateFilter()
{
    ServiceNameFilter = new Uri("fabric:/WordCount/WordCountService"),
};
wordCountServiceFilter.PartitionFilters.Add(wordCountServicePartitionFilter);

// Application filter: for specific application, return no services except the ones of interest
var wordCountApplicationFilter = new ApplicationHealthStateFilter()
    {
        // Always return fabric:/WordCount application
        ApplicationNameFilter = new Uri("fabric:/WordCount"),
    };
wordCountApplicationFilter.ServiceFilters.Add(wordCountServiceFilter);

queryDescription.ApplicationFilters.Add(wordCountApplicationFilter);

var result = await fabricClient.HealthManager.GetClusterHealthChunkAsync(queryDescription);
```

### <a name="powershell"></a>PowerShell
El cmdlet para obtener el estado de clúster es [Get-ServiceFabricClusterChunkHealth](https://msdn.microsoft.com/library/mt644772.aspx). En primer lugar, conectar con el clúster mediante el cmdlet [ServiceFabricCluster conectar](https://msdn.microsoft.com/library/mt125938.aspx) .

El código siguiente obtiene nodos solo si están en Error excepto para un nodo específico, que siempre se debe devolver.

```xml
PS C:\> $errorFilter = [System.Fabric.Health.HealthStateFilter]::Error;
$allFilter = [System.Fabric.Health.HealthStateFilter]::All;

$nodeFilter1 = New-Object System.Fabric.Health.NodeHealthStateFilter -Property @{HealthStateFilter=$errorFilter}
$nodeFilter2 = New-Object System.Fabric.Health.NodeHealthStateFilter -Property @{NodeNameFilter="_Node_1";HealthStateFilter=$allFilter}
# Create node filter list that will be passed in the cmdlet
$nodeFilters = New-Object System.Collections.Generic.List[System.Fabric.Health.NodeHealthStateFilter]
$nodeFilters.Add($nodeFilter1)
$nodeFilters.Add($nodeFilter2)

Get-ServiceFabricClusterHealthChunk -NodeFilters $nodeFilters

HealthState                  : Error
NodeHealthStateChunks        :
                               TotalCount            : 1

                               NodeName              : _Node_1
                               HealthState           : Ok

ApplicationHealthStateChunks : None
```

El siguiente cmdlet obtiene fragmento de clúster con los filtros de aplicación.

```xml
$errorFilter = [System.Fabric.Health.HealthStateFilter]::Error;
$allFilter = [System.Fabric.Health.HealthStateFilter]::All;

# All replicas
$replicaFilter = New-Object System.Fabric.Health.ReplicaHealthStateFilter -Property @{HealthStateFilter=$allFilter}

# All partitions
$partitionFilter = New-Object System.Fabric.Health.PartitionHealthStateFilter -Property @{HealthStateFilter=$allFilter}
$partitionFilter.ReplicaFilters.Add($replicaFilter)

# For WordCountService, return all partitions and all replicas
$svcFilter1 = New-Object System.Fabric.Health.ServiceHealthStateFilter -Property @{ServiceNameFilter="fabric:/WordCount/WordCountService"}
$svcFilter1.PartitionFilters.Add($partitionFilter)

$svcFilter2 = New-Object System.Fabric.Health.ServiceHealthStateFilter -Property @{HealthStateFilter=$errorFilter}

$appFilter = New-Object System.Fabric.Health.ApplicationHealthStateFilter -Property @{ApplicationNameFilter="fabric:/WordCount"}
$appFilter.ServiceFilters.Add($svcFilter1)
$appFilter.ServiceFilters.Add($svcFilter2)

$appFilters = New-Object System.Collections.Generic.List[System.Fabric.Health.ApplicationHealthStateFilter]
$appFilters.Add($appFilter)

Get-ServiceFabricClusterHealthChunk -ApplicationFilters $appFilters

HealthState                  : Error
NodeHealthStateChunks        : None
ApplicationHealthStateChunks :
                               TotalCount            : 1

                               ApplicationName       : fabric:/WordCount
                               ApplicationTypeName   : WordCount
                               HealthState           : Error
                               ServiceHealthStateChunks :
                                   TotalCount            : 1

                                   ServiceName           : fabric:/WordCount/WordCountService
                                   HealthState           : Error
                                   PartitionHealthStateChunks :
                                       TotalCount            : 1

                                       PartitionId           : a1f83a35-d6bf-4d39-b90d-28d15f39599b
                                       HealthState           : Error
                                       ReplicaHealthStateChunks :
                                           TotalCount            : 5

                                           ReplicaOrInstanceId   : 131031502143040223
                                           HealthState           : Ok

                                           ReplicaOrInstanceId   : 131031502346844060
                                           HealthState           : Ok

                                           ReplicaOrInstanceId   : 131031502346844059
                                           HealthState           : Ok

                                           ReplicaOrInstanceId   : 131031502346844061
                                           HealthState           : Ok

                                           ReplicaOrInstanceId   : 131031502346844058
                                           HealthState           : Error
```

El siguiente cmdlet devuelve todas las entidades implementadas en un nodo.

```xml
$errorFilter = [System.Fabric.Health.HealthStateFilter]::Error;
$allFilter = [System.Fabric.Health.HealthStateFilter]::All;

$dspFilter = New-Object System.Fabric.Health.DeployedServicePackageHealthStateFilter -Property @{HealthStateFilter=$allFilter}
$daFilter =  New-Object System.Fabric.Health.DeployedApplicationHealthStateFilter -Property @{HealthStateFilter=$allFilter;NodeNameFilter="_Node_2"}
$daFilter.DeployedServicePackageFilters.Add($dspFilter)

$appFilter = New-Object System.Fabric.Health.ApplicationHealthStateFilter -Property @{HealthStateFilter=$allFilter}
$appFilter.DeployedApplicationFilters.Add($daFilter)

$appFilters = New-Object System.Collections.Generic.List[System.Fabric.Health.ApplicationHealthStateFilter]
$appFilters.Add($appFilter)
Get-ServiceFabricClusterHealthChunk -ApplicationFilters $appFilters


HealthState                  : Error
NodeHealthStateChunks        : None
ApplicationHealthStateChunks :
                               TotalCount            : 2

                               ApplicationName       : fabric:/System
                               HealthState           : Ok
                               DeployedApplicationHealthStateChunks :
                                   TotalCount            : 1

                                   NodeName              : _Node_2
                                   HealthState           : Ok
                                   DeployedServicePackageHealthStateChunks :
                                       TotalCount            : 1

                                       ServiceManifestName   : FAS
                                       HealthState           : Ok



                               ApplicationName       : fabric:/WordCount
                               ApplicationTypeName   : WordCount
                               HealthState           : Error
                               DeployedApplicationHealthStateChunks :
                                   TotalCount            : 1

                                   NodeName              : _Node_2
                                   HealthState           : Ok
                                   DeployedServicePackageHealthStateChunks :
                                       TotalCount            : 2

                                       ServiceManifestName   : WordCountServicePkg
                                       HealthState           : Ok

                                       ServiceManifestName   : WordCountWebServicePkg
                                       HealthState           : Ok
```

### <a name="rest"></a>REST
Puede obtener el fragmento de estado de clúster con una [solicitud](https://msdn.microsoft.com/library/azure/mt656722.aspx) o una [solicitud de publicación](https://msdn.microsoft.com/library/azure/mt656721.aspx) que incluya las directivas de mantenimiento y los filtros avanzados se describe en el cuerpo.

## <a name="general-queries"></a>Consultas generales
Consultas generales devuelven una lista de entidades de tela de servicio de un tipo especificado. Se exponen a través de la API (a través de los métodos de **FabricClient.QueryManager**), el cmdlets de PowerShell y el resto. Estas consultas agregan subconsultas de varios componentes. Uno de ellos es el [almacén de estado](service-fabric-health-introduction.md#health-store), que rellena el estado de mantenimiento agregado para cada resultado de la consulta.  

> [AZURE.NOTE] Consultas generales devuelven el estado de salud agregado de la entidad y no contienen datos de estado enriquecido. Si una entidad no es correcta, puede seguir con consultas de estado para obtener toda su información de estado, incluyendo evaluaciones negativos, Estados de salud secundarios y eventos.

Si las consultas generales devuelven un estado de salud desconocido para una entidad, es posible que el almacén de estado no tiene datos completos acerca de la entidad. También es posible que una subconsulta en el almacén de estado no era correcta (por ejemplo, se ha producido un error de comunicación o se ha limitado la tienda de estado). Seguimiento con una consulta de estado de la entidad. Si la subconsulta encuentra errores transitorios, como los problemas de red, esta consulta seguimiento puede tener éxito. Pueden también proporcionan más detalles de la tienda de salud sobre por qué no se expone la entidad.

Las consultas que contienen **HealthState** para entidades son:

- Lista de nodos: devuelve los nodos de lista en el clúster (páginas).
  - API: [FabricClient.QueryClient.GetNodeListAsync](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.queryclient.getnodelistasync.aspx)
  - PowerShell: Get-ServiceFabricNode
- Lista de aplicaciones: devuelve la lista de aplicaciones en el clúster (páginas).
  - API: [FabricClient.QueryClient.GetApplicationListAsync](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.queryclient.getapplicationlistasync.aspx)
  - PowerShell: Get-ServiceFabricApplication
- Lista de servicios: devuelve la lista de servicios en una aplicación (páginas).
  - API: [FabricClient.QueryClient.GetServiceListAsync](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.queryclient.getservicelistasync.aspx)
  - PowerShell: Get-ServiceFabricService
- Lista de particiones: devuelve la lista de particiones en un servicio (páginas).
  - API: [FabricClient.QueryClient.GetPartitionListAsync](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.queryclient.getpartitionlistasync.aspx)
  - PowerShell: Get-ServiceFabricPartition
- Lista de réplica: devuelve la lista de réplicas de una partición (páginas).
  - API: [FabricClient.QueryClient.GetReplicaListAsync](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.queryclient.getreplicalistasync.aspx)
  - PowerShell: Get-ServiceFabricReplica
- Implementar la lista de aplicaciones: devuelve la lista de aplicaciones distribuidas en un nodo.
  - API: [FabricClient.QueryClient.GetDeployedApplicationListAsync](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.queryclient.getdeployedapplicationlistasync.aspx)
  - PowerShell: Get-ServiceFabricDeployedApplication
- Implementar lista de paquete de servicio: devuelve la lista de paquetes de servicio en una aplicación implementada.
  - API: [FabricClient.QueryClient.GetDeployedServicePackageListAsync](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.queryclient.getdeployedservicepackagelistasync.aspx)
  - PowerShell: Get-ServiceFabricDeployedApplication

> [AZURE.NOTE] Algunas de las consultas devuelven resultados de páginas. El rendimiento de estas consultas es una lista que se deriva de [PagedList<T>](https://msdn.microsoft.com/library/azure/mt280056.aspx). Si los resultados no cabe en un mensaje, se devuelve solo una página y un ContinuationToken que realiza un seguimiento de dónde se detiene enumeración. Debe continuar llamar a la misma consulta y pase el token de continuación de la consulta anterior para obtener los resultados de la siguientes.

### <a name="examples"></a>Ejemplos

El código siguiente obtiene las aplicaciones mal estadas en el clúster:

```csharp
var applications = fabricClient.QueryManager.GetApplicationListAsync().Result.Where(
  app => app.HealthState == HealthState.Error);
```

El siguiente cmdlet obtiene los detalles de la aplicación de la estructura: / aplicación WordCount. Observe que el estado es en advertencia.

```powershell
PS C:\> Get-ServiceFabricApplication -ApplicationName fabric:/WordCount

ApplicationName        : fabric:/WordCount
ApplicationTypeName    : WordCount
ApplicationTypeVersion : 1.0.0
ApplicationStatus      : Ready
HealthState            : Warning
ApplicationParameters  : { "WordCountWebService_InstanceCount" = "1";
                         "_WFDebugParams_" = "[{"ServiceManifestName":"WordCountWebServicePkg","CodePackageName":"Code","EntryPointType":"Main","Debug
                         ExePath":"C:\\Program Files (x86)\\Microsoft Visual Studio
                         14.0\\Common7\\Packages\\Debugger\\VsDebugLaunchNotify.exe","DebugArguments":" {74f7e5d5-71a9-47e2-a8cd-1878ec4734f1} -p
                         [ProcessId] -tid [ThreadId]","EnvironmentBlock":"_NO_DEBUG_HEAP=1\u0000"},{"ServiceManifestName":"WordCountServicePkg","CodeP
                         ackageName":"Code","EntryPointType":"Main","DebugExePath":"C:\\Program Files (x86)\\Microsoft Visual Studio
                         14.0\\Common7\\Packages\\Debugger\\VsDebugLaunchNotify.exe","DebugArguments":" {2ab462e6-e0d1-4fda-a844-972f561fe751} -p
                         [ProcessId] -tid [ThreadId]","EnvironmentBlock":"_NO_DEBUG_HEAP=1\u0000"}]" }
```

El siguiente cmdlet obtiene los servicios con un estado de mantenimiento de advertencia:

```powershell
PS C:\> Get-ServiceFabricApplication | Get-ServiceFabricService | where {$_.HealthState -eq "Warning"}


ServiceName            : fabric:/WordCount/WordCountService
ServiceKind            : Stateful
ServiceTypeName        : WordCountServiceType
IsServiceGroup         : False
ServiceManifestVersion : 1.0.0
HasPersistedState      : True
ServiceStatus          : Active
HealthState            : Warning
```

## <a name="cluster-and-application-upgrades"></a>Actualizaciones de clúster y aplicación
Durante la actualización supervisada del clúster y la aplicación, servicio tela comprobaciones de estado para asegurarse de que todo está correcto. Si una entidad está en mal estada como examinando usando las directivas de mantenimiento configurado, la actualización aplica a las directivas de actualización específicas para determinar la acción siguiente. La actualización puede detenerse para permitir la interacción con el usuario (por ejemplo, corregir las condiciones de error o cambiar las directivas) o pueden automáticamente revertir a la versión anterior válida.

Durante una actualización de *clúster* , puede obtener el estado de actualización de clúster. El estado de actualización incluye evaluaciones de mal estadas, seleccione lo que está en mal estado en el clúster. Si la actualización se deshace debido a problemas de estado, el estado de actualización recuerda la últimos motivos mal Estados. Esta información puede ayudar a los administradores a investigar qué ha fallado después de la actualización de deshecho o detenido.

Asimismo, durante la actualización de una *aplicación* , las evaluaciones mal estadas se encuentran en el estado de actualización de la aplicación.

Siguiente muestra el estado de actualización de la aplicación para una estructura modificada: / aplicación WordCount. A continuación se proporciona un error en una de sus réplicas. Deshace la actualización porque no se respetan las comprobaciones de estado.

```powershell
PS C:\> Get-ServiceFabricApplicationUpgrade fabric:/WordCount

ApplicationName               : fabric:/WordCount
ApplicationTypeName           : WordCount
TargetApplicationTypeVersion  : 1.0.0.0
ApplicationParameters         : {}
StartTimestampUtc             : 4/21/2015 5:23:26 PM
FailureTimestampUtc           : 4/21/2015 5:23:37 PM
FailureReason                 : HealthCheck
UpgradeState                  : RollingBackInProgress
UpgradeDuration               : 00:00:23
CurrentUpgradeDomainDuration  : 00:00:00
CurrentUpgradeDomainProgress  : UD1

                                NodeName            : _Node_1
                                UpgradePhase        : Upgrading

                                NodeName            : _Node_2
                                UpgradePhase        : Upgrading

                                NodeName            : _Node_3
                                UpgradePhase        : PreUpgradeSafetyCheck
                                PendingSafetyChecks :
                                EnsurePartitionQuorum - PartitionId: 30db5be6-4e20-4698-8185-4bd7ca744020
NextUpgradeDomain             : UD2
UpgradeDomainsStatus          : { "UD1" = "Completed";
                                "UD2" = "Pending";
                                "UD3" = "Pending";
                                "UD4" = "Pending" }
UnhealthyEvaluations          :
                                Unhealthy services: 100% (1/1), ServiceType='WordCountServiceType', MaxPercentUnhealthyServices=0%.

                                  Unhealthy service: ServiceName='fabric:/WordCount/WordCountService', AggregatedHealthState='Error'.

                                      Unhealthy partitions: 100% (1/1), MaxPercentUnhealthyPartitionsPerService=0%.

                                      Unhealthy partition: PartitionId='a1f83a35-d6bf-4d39-b90d-28d15f39599b', AggregatedHealthState='Error'.

                                          Unhealthy replicas: 20% (1/5), MaxPercentUnhealthyReplicasPerPartition=0%.

                                          Unhealthy replica: PartitionId='a1f83a35-d6bf-4d39-b90d-28d15f39599b',
                                  ReplicaOrInstanceId='131031502346844058', AggregatedHealthState='Error'.

                                              Error event: SourceId='DiskWatcher', Property='Disk'.

UpgradeKind                   : Rolling
RollingUpgradeMode            : UnmonitoredAuto
ForceRestart                  : False
UpgradeReplicaSetCheckTimeout : 00:15:00
```

Más información sobre la [aplicación de servicio tela actualizar](service-fabric-application-upgrade.md).

## <a name="use-health-evaluations-to-troubleshoot"></a>Utilizar evaluaciones de estado para solucionar problemas
Cada vez que hay un problema con el clúster o una aplicación, busque el estado de clúster o la aplicación para indicar cuál es el problema. Las evaluaciones mal estadas proporcionan detalles sobre qué activó la mal estado actual. Si necesita, puede profundizar en entidades secundarios mal estado para identificar la causa principal.

> [AZURE.NOTE] Las evaluaciones mal estadas mostrar que la primera razón por la entidad se evalúa para el estado actual. Puede haber varias otros eventos que desencadenan este estado, pero no se reflejan en las evaluaciones. Para obtener más información, explorar en profundidad las entidades de estado para calcular todos los informes mal Estados en el clúster.

## <a name="next-steps"></a>Pasos siguientes
[Usar informes de estado de sistema para solucionar problemas](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)

[Agregar los informes de estado de servicio tela personalizados](service-fabric-report-health.md)

[Cómo informar y comprobar el estado del servicio](service-fabric-diagnostics-how-to-report-and-check-service-health.md)

[Supervisar y diagnosticar servicios localmente](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

[Actualización de la aplicación de servicio tela](service-fabric-application-upgrade.md)
