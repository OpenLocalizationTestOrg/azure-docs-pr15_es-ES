<properties
   pageTitle="Solucionar problemas con los informes de estado de sistema | Microsoft Azure"
   description="Describe los informes de estado enviados por los componentes de Azure tela de servicio y su uso para clúster de solución de problemas o problemas de la aplicación."
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

# <a name="use-system-health-reports-to-troubleshoot"></a>Usar informes de estado de sistema para solucionar problemas

Azure tela de servicio informe componentes fuera de la caja en todas las entidades en el clúster. El [estado almacenar](service-fabric-health-introduction.md#health-store) crea y elimina entidades basándose en los informes del sistema. También se organiza en una jerarquía que capture interacciones de entidad.

> [AZURE.NOTE] Para conocer los conceptos de estado, más al [modelo de estado del servicio tela](service-fabric-health-introduction.md).

Informes de estado de sistema proporcionan visibilidad clúster y funcionalidad de la aplicación y problemas de marca a través de estado. Para aplicaciones y servicios, informes de estado de sistema comprueban que entidades se implementan y se comportan correctamente desde la perspectiva de tela de servicio. Los informes no proporciona ninguna supervisión de estado de la lógica empresarial del servicio o la detección de procesos se ha bloqueado. Servicios de usuario pueden mejorar los datos de estado con información específica sobre su lógica.

> [AZURE.NOTE] Informes de estado de watchdogs están visibles solo *después de* los componentes del sistema crean una entidad. Cuando se elimina una entidad, el almacén de estado elimina automáticamente todos los informes de estado asociados. El mismo es true cuando se crea una nueva instancia de la entidad (por ejemplo, se crea una nueva instancia de réplica de servicio). Todos los informes asociados a la instancia antigua se elimina y limpia de la tienda.

El componente del sistema informes se identifican por el origen, que comienza con el "**sistema.**" prefijo. Watchdogs no puede utilizar el mismo prefijo para sus orígenes, como informes con parámetros no válidos son rechazados.
Veamos algunos informes del sistema para comprender cómo corregir los posibles problemas que representan y lo que les activa.

> [AZURE.NOTE] Servicio tela sigue agregar los informes de las condiciones de interés que mejoran la visibilidad de lo que sucede en el clúster y la aplicación.

## <a name="cluster-system-health-reports"></a>Informes de estado del sistema de clúster
La entidad de salud de clúster se crea automáticamente en el almacén de estado. Si todo funciona correctamente, no tiene un informe del sistema.

### <a name="neighborhood-loss"></a>Pérdida de entorno
**System.Federation** informa de un error cuando detecta una pérdida del entorno. El informe es de nodos individuales, y el identificador de nodo está incluido en el nombre de propiedad. Si se pierde un entorno del anillo tela de servicio completo, normalmente puede esperar dos eventos (ambos lados del informe de separación). Si se pierden más grupos, hay más eventos.

El informe especifica el tiempo de espera de concesión global como el tiempo de vida. El informe se vuelve a enviar cada mitad de la duración del período de vida de mientras la condición permanezca activada. El evento se elimina automáticamente cuando expire. Quitar al comportamiento expirado garantiza que el informe se limpia de la tienda de salud correctamente, incluso si el nodo de generación de informes.

- **SourceId**: System.Federation
- **Propiedad**: comienza con **entorno** e incluye información de nodo
- **Pasos siguientes**: investigar por qué se pierde el entorno (por ejemplo, comprobar la comunicación entre los nodos de clúster).

## <a name="node-system-health-reports"></a>Informes de estado del sistema de nodo
**System.FM**, que representa el servicio del Administrador de migración tras error, es la autoridad que administra la información acerca de los nodos de clúster. Cada nodo debe tener un informe de System.FM que muestra su estado. Las entidades de nodo se quitan cuando se quita el estado de nodo (consulte [RemoveNodeStateAsync](https://msdn.microsoft.com/library/azure/mt161348.aspx)).

### <a name="node-updown"></a>Nodo arriba o abajo
System.FM informes como Aceptar cuando el nodo une a la llamada (es en marcha). Se detecta un error cuando el nodo volará anillo (es hacia abajo, ya sea para actualizar o simplemente porque no se pudo). La jerarquía de estado generada por el almacén de estado realiza una acción sobre entidades implementadas en correlación con informes de nodo System.FM. Se considera que el nodo de un elemento primario virtual de todas las entidades implementadas. Las entidades implementadas en ese nodo se exponen a través de consultas, si el nodo se notifica como activo por System.FM, con la misma instancia que la instancia asociada a las entidades. Cuando System.FM informa de que el nodo está inactivo o reinicia (una nueva instancia), el almacén de estado limpia automáticamente las entidades implementadas que pueden existir únicamente en el nodo hacia abajo o en la aparición anterior del nodo.

- **SourceId**: System.FM
- **Propiedad**: estado
- **Pasos siguientes**: si el nodo está inactivo para una actualización, debe dar volver una vez que se ha actualizado. En este caso, el estado de mantenimiento debe cambiarlo a Aceptar. Si el nodo no regresar o se produce un error, el problema debe investigación más.

En el ejemplo siguiente se muestra el evento de System.FM con un estado de mantenimiento de Aceptar para nodo hacia arriba:

```powershell

PS C:\> Get-ServiceFabricNodeHealth -NodeName Node.1
NodeName              : Node.1
AggregatedHealthState : Ok
HealthEvents          :
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 2
                        SentAt                : 4/24/2015 5:27:33 PM
                        ReceivedAt            : 4/24/2015 5:28:50 PM
                        TTL                   : Infinite
                        Description           : Fabric node is up.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : ->Ok = 4/24/2015 5:28:50 PM

```


### <a name="certificate-expiration"></a>Caducidad de certificados
**System.FabricNode** muestra una advertencia cuando están certificados usados por el nodo cerca de expiración. Hay tres certificados por nodo: **Certificate_cluster**, **Certificate_server**y **Certificate_default_client**. Cuando la fecha de vencimiento es al menos dos semanas, el estado de mantenimiento de informe es correcto. Cuando la fecha de vencimiento dentro de dos semanas, el tipo de informe es una advertencia. TTL de estos eventos es infinito y se eliminan cuando un nodo abandona el clúster.

- **SourceId**: System.FabricNode
- **Propiedad**: comienza con el **certificado** y contiene más información sobre el tipo de certificado
- **Pasos siguientes**: actualizar los certificados si están cerca de expiración.

### <a name="load-capacity-violation"></a>Infracción de capacidad de carga
El equilibrador de carga de servicio tela muestra una advertencia si detecta una infracción de capacidad de nodo.

 - **SourceId**: System.PLB
 - **Propiedad**: empieza con **capacidad**
 - **Pasos siguientes**: comprobar proporciona métricas y ver la capacidad actual en el nodo.

## <a name="application-system-health-reports"></a>Informes de estado del sistema de aplicación
**System.CM**, que representa el servicio de administrador de clúster, es la autoridad que administra la información sobre una aplicación.

### <a name="state"></a>Estado
System.CM informes como Aceptar cuando la aplicación se ha creado o actualizado. Le notifica la tienda de estado cuando se ha eliminado la aplicación, para que se puede quitar de la tienda.

- **SourceId**: System.CM
- **Propiedad**: estado
- **Pasos siguientes**: si se ha creado la aplicación, debe incluir el informe de estado del Administrador de clúster. En caso contrario, emita una consulta para comprobar el estado de la aplicación (por ejemplo, el cmdlet de PowerShell * *Get-ServiceFabricApplication ApplicationName *applicationName***).

En el ejemplo siguiente se muestra el evento de estado en la **tela: / WordCount** aplicación:

```powershell
PS C:\> Get-ServiceFabricApplicationHealth fabric:/WordCount -ServicesFilter None -DeployedApplicationsFilter None

ApplicationName                 : fabric:/WordCount
AggregatedHealthState           : Ok
ServiceHealthStates             : None
DeployedApplicationHealthStates : None
HealthEvents                    :
                                  SourceId              : System.CM
                                  Property              : State
                                  HealthState           : Ok
                                  SequenceNumber        : 82
                                  SentAt                : 4/24/2015 6:12:51 PM
                                  ReceivedAt            : 4/24/2015 6:12:51 PM
                                  TTL                   : Infinite
                                  Description           : Application has been created.
                                  RemoveWhenExpired     : False
                                  IsExpired             : False
                                  Transitions           : ->Ok = 4/24/2015 6:12:51 PM
```

## <a name="service-system-health-reports"></a>Informes de estado del sistema de servicio
**System.FM**, que representa el servicio del Administrador de migración tras error, es la autoridad que administra la información acerca de los servicios.

### <a name="state"></a>Estado
System.FM informes como Aceptar cuando el servicio se ha creado. Elimina la entidad desde la tienda de estado cuando el servicio se ha eliminado.

- **SourceId**: System.FM
- **Propiedad**: estado

En el ejemplo siguiente se muestra el evento de estado del servicio **tela: / WordCount/WordCountService**:

```powershell
PS C:\> Get-ServiceFabricServiceHealth fabric:/WordCount/WordCountService

ServiceName           : fabric:/WordCount/WordCountService
AggregatedHealthState : Ok
PartitionHealthStates :
                        PartitionId           : 875a1caa-d79f-43bd-ac9d-43ee89a9891c
                        AggregatedHealthState : Ok

HealthEvents          :
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 3
                        SentAt                : 4/24/2015 6:12:51 PM
                        ReceivedAt            : 4/24/2015 6:13:01 PM
                        TTL                   : Infinite
                        Description           : Service has been created.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : ->Ok = 4/24/2015 6:13:01 PM
```

### <a name="unplaced-replicas-violation"></a>Infracción de réplicas sin colocar
**System.PLB** muestra una advertencia si no puede encontrar una ubicación para una o más réplicas de servicio. El informe se elimina cuando expire.

- **SourceId**: System.FM
- **Propiedad**: estado
- **Pasos siguientes**: revisar las restricciones de servicio y el estado actual de la ubicación.

En el ejemplo siguiente se muestra una infracción de un servicio configurado con 7 réplicas de destino en un clúster de 5 nodos:

```xml
PS C:\> Get-ServiceFabricServiceHealth fabric:/WordCount/WordCountService


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
                        SequenceNumber        : 131032232425505477
                        SentAt                : 3/23/2016 4:14:02 PM
                        ReceivedAt            : 3/23/2016 4:14:03 PM
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
                        Transitions           : Error->Warning = 3/22/2016 7:57:48 PM, LastOk = 1/1/0001 12:00:00 AM
```

## <a name="partition-system-health-reports"></a>Informes de estado del sistema de partición
**System.FM**, que representa el servicio del Administrador de migración tras error, es la autoridad que administra la información acerca de las particiones del servicio.

### <a name="state"></a>Estado
System.FM informes como Aceptar cuando la partición se ha creado y es correcto. Elimina la entidad de la tienda de estado cuando se elimina la partición.

Si la partición es inferior el recuento de réplica mínimo, notifica un error. Si la partición no es menor que el recuento de réplica mínimo, pero está por debajo del recuento de réplica de destino, muestra una advertencia. Si la partición es de pérdida de quórum, System.FM informa de un error.

Otros eventos importantes incluyen una advertencia cuando la nueva configuración tarda más de lo esperado, y cuando la compilación tarda más de lo esperado. El esperado horas para la generación y la nueva configuración son configurables basan en escenarios de servicio. Por ejemplo, si un servicio tiene un terabyte de estado, como la base de datos de SQL, la compilación dura más de un servicio con una cantidad pequeña de estado.

- **SourceId**: System.FM
- **Propiedad**: estado
- **Pasos siguientes**: si el estado de mantenimiento no es correcto, es posible que algunas réplicas no creados, abrir o promovidos a primaria o secundaria correctamente. En muchos casos, la causa es un error de servicio en la implementación de abrir o cambiar de la función.

En el ejemplo siguiente se muestra una partición buen estada:

```powershell
PS C:\> Get-ServiceFabricPartition fabric:/StatelessPiApplication/StatelessPiService | Get-ServiceFabricPartitionHealth
PartitionId           : 29da484c-2c08-40c5-b5d9-03774af9a9bf
AggregatedHealthState : Ok
ReplicaHealthStates   : None
HealthEvents          :
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 38
                        SentAt                : 4/24/2015 6:33:10 PM
                        ReceivedAt            : 4/24/2015 6:33:31 PM
                        TTL                   : Infinite
                        Description           : Partition is healthy.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : ->Ok = 4/24/2015 6:33:31 PM
```

En el ejemplo siguiente se muestra el estado de una partición que está debajo del recuento de réplica de destino. El siguiente paso es obtener la descripción de partición, que muestra cómo se configura: **MinReplicaSetSize** es dos y **TargetReplicaSetSize** es siete. A continuación, obtener el número de nodos en el clúster: cinco. Así que en este caso, dos réplicas no se pueden colocar.

```powershell
PS C:\> Get-ServiceFabricPartition fabric:/WordCount/WordCountService | Get-ServiceFabricPartitionHealth -ReplicasFilter None

PartitionId           : 875a1caa-d79f-43bd-ac9d-43ee89a9891c
AggregatedHealthState : Warning
UnhealthyEvaluations  :
                        Unhealthy event: SourceId='System.FM', Property='State', HealthState='Warning', ConsiderWarningAsError=false.

ReplicaHealthStates   : None
HealthEvents          :
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Warning
                        SequenceNumber        : 37
                        SentAt                : 4/24/2015 6:13:12 PM
                        ReceivedAt            : 4/24/2015 6:13:31 PM
                        TTL                   : Infinite
                        Description           : Partition is below target replica or instance count.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Ok->Warning = 4/24/2015 6:13:31 PM

PS C:\> Get-ServiceFabricPartition fabric:/WordCount/WordCountService

PartitionId            : 875a1caa-d79f-43bd-ac9d-43ee89a9891c
PartitionKind          : Int64Range
PartitionLowKey        : 1
PartitionHighKey       : 26
PartitionStatus        : Ready
LastQuorumLossDuration : 00:00:00
MinReplicaSetSize      : 2
TargetReplicaSetSize   : 7
HealthState            : Warning
DataLossNumber         : 130743727710830900
ConfigurationNumber    : 8589934592


PS C:\> @(Get-ServiceFabricNode).Count
5
```

### <a name="replica-constraint-violation"></a>Infracción de restricción de réplica
**System.PLB** muestra una advertencia si detecta una infracción de restricción de réplica y no se pueden colocar réplicas de la partición.

- **SourceId**: System.PLB
- **Propiedad**: empieza con **ReplicaConstraintViolation**

## <a name="replica-system-health-reports"></a>Informes de estado del sistema de réplica
**System.RA**, que representa el componente de agente de la nueva configuración, es la autoridad para el estado de réplica.

### <a name="state"></a>Estado
**System.RA** informes como Aceptar cuando haya acabado de crear la réplica.

- **SourceId**: System.RA
- **Propiedad**: estado

En el ejemplo siguiente se muestra una réplica buen estada:

```powershell
PS C:\> Get-ServiceFabricPartition fabric:/WordCount/WordCountService | Get-ServiceFabricReplica | where {$_.ReplicaRole -eq "Primary"} | Get-ServiceFabricReplicaHealth
PartitionId           : 875a1caa-d79f-43bd-ac9d-43ee89a9891c
ReplicaId             : 130743727717237310
AggregatedHealthState : Ok
HealthEvents          :
                        SourceId              : System.RA
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 130743727718018580
                        SentAt                : 4/24/2015 6:12:51 PM
                        ReceivedAt            : 4/24/2015 6:13:02 PM
                        TTL                   : Infinite
                        Description           : Replica has been created.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : ->Ok = 4/24/2015 6:13:02 PM
```

### <a name="replica-open-status"></a>Estado de réplica abierto
La descripción de este informe de estado contiene la hora de inicio (hora Universal) cuando la API llamada.

**System.RA** muestra una advertencia si la réplica abierta tarda más que el período configurado (predeterminado: 30 minutos). Si la API afecta la disponibilidad de servicio, el informe se emite con mayor rapidez (un intervalo configurable con un valor predeterminado de 30 segundos). El tiempo medido incluye el tiempo necesario para abrir replicator y el servicio de abrir. La propiedad cambia a Aceptar si el abrir completa.

- **SourceId**: System.RA
- **Propiedad**: **ReplicaOpenStatus**
- **Pasos siguientes**: si el estado no es Aceptar, investigar por qué la réplica abrir tarda más de lo esperado.

### <a name="slow-service-api-call"></a>Llamada a la API de servicio lenta
Informe de **System.RAP** y **System.Replicator** una advertencia si una llamada al código de servicio del usuario tarda más que el tiempo configurado. Se desactiva la advertencia cuando finalice la llamada.

- **SourceId**: System.RAP o System.Replicator
- **Propiedad**: el nombre de la API lenta. La descripción proporciona información detallada sobre el tiempo de que la API ha estado pendiente.
- **Pasos siguientes**: investigar por qué la llamada tarda más de lo esperado.

En el ejemplo siguiente se muestra una partición de pérdida de quórum y los pasos de la investigación listo para averiguar por qué. Una de las réplicas tiene un estado de advertencia, para obtener su estado. Muestra que la operación de servicio tarda más de lo esperado, un evento comunicado por System.RAP. Después de recibe esta información, el siguiente paso es mirar el código de servicio e investigar allí. En este caso, la implementación de **RunAsync** del servicio con estado produce una excepción no controlada. Las réplicas son reciclaje, por lo que no puede ver las réplicas en el estado de advertencia. Puede volver a intentar obtener el estado de mantenimiento y busque las diferencias en el identificador de réplica. En algunos casos, los reintentos pueden dar pistas.

```powershell
PS C:\> Get-ServiceFabricPartition fabric:/HelloWorldStatefulApplication/HelloWorldStateful | Get-ServiceFabricPartitionHealth

PartitionId           : 72a0fb3e-53ec-44f2-9983-2f272aca3e38
AggregatedHealthState : Error
UnhealthyEvaluations  :
                        Error event: SourceId='System.FM', Property='State'.

ReplicaHealthStates   :
                        ReplicaId             : 130743748372546446
                        AggregatedHealthState : Ok

                        ReplicaId             : 130743746168084332
                        AggregatedHealthState : Ok

                        ReplicaId             : 130743746195428808
                        AggregatedHealthState : Warning

                        ReplicaId             : 130743746195428807
                        AggregatedHealthState : Ok

HealthEvents          :
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Error
                        SequenceNumber        : 182
                        SentAt                : 4/24/2015 7:00:17 PM
                        ReceivedAt            : 4/24/2015 7:00:31 PM
                        TTL                   : Infinite
                        Description           : Partition is in quorum loss.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Warning->Error = 4/24/2015 6:51:31 PM

PS C:\> Get-ServiceFabricPartition fabric:/HelloWorldStatefulApplication/HelloWorldStateful

PartitionId            : 72a0fb3e-53ec-44f2-9983-2f272aca3e38
PartitionKind          : Int64Range
PartitionLowKey        : -9223372036854775808
PartitionHighKey       : 9223372036854775807
PartitionStatus        : InQuorumLoss
LastQuorumLossDuration : 00:00:13
MinReplicaSetSize      : 2
TargetReplicaSetSize   : 3
HealthState            : Error
DataLossNumber         : 130743746152927699
ConfigurationNumber    : 227633266688

PS C:\> Get-ServiceFabricReplica 72a0fb3e-53ec-44f2-9983-2f272aca3e38 130743746195428808

ReplicaId           : 130743746195428808
ReplicaAddress      : PartitionId: 72a0fb3e-53ec-44f2-9983-2f272aca3e38, ReplicaId: 130743746195428808
ReplicaRole         : Primary
NodeName            : Node.3
ReplicaStatus       : Ready
LastInBuildDuration : 00:00:01
HealthState         : Warning

PS C:\> Get-ServiceFabricReplicaHealth 72a0fb3e-53ec-44f2-9983-2f272aca3e38 130743746195428808

PartitionId           : 72a0fb3e-53ec-44f2-9983-2f272aca3e38
ReplicaId             : 130743746195428808
AggregatedHealthState : Warning
UnhealthyEvaluations  :
                        Unhealthy event: SourceId='System.RAP', Property='ServiceOpenOperationDuration', HealthState='Warning', ConsiderWarningAsError=false.

HealthEvents          :
                        SourceId              : System.RA
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 130743756170185892
                        SentAt                : 4/24/2015 7:00:17 PM
                        ReceivedAt            : 4/24/2015 7:00:33 PM
                        TTL                   : Infinite
                        Description           : Replica has been created.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : ->Ok = 4/24/2015 7:00:33 PM

                        SourceId              : System.RAP
                        Property              : ServiceOpenOperationDuration
                        HealthState           : Warning
                        SequenceNumber        : 130743756399407044
                        SentAt                : 4/24/2015 7:00:39 PM
                        ReceivedAt            : 4/24/2015 7:00:59 PM
                        TTL                   : Infinite
                        Description           : Start Time (UTC): 2015-04-24 19:00:17.019
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : ->Warning = 4/24/2015 7:00:59 PM
```

Cuando se inicia la aplicación defectuosa bajo su control, las ventanas de eventos de diagnóstico mostrar la excepción de RunAsync:

![Eventos de diagnóstico de Visual Studio 2015: error de RunAsync en tela: / HelloWorldStatefulApplication.][1]

Eventos de diagnóstico de Visual Studio 2015: error de RunAsync en **tela: / HelloWorldStatefulApplication**.

[1]: ./media/service-fabric-understand-and-troubleshoot-with-system-health-reports/servicefabric-health-vs-runasync-exception.png


### <a name="replication-queue-full"></a>Cola de replicación completa
**System.Replicator** muestra una advertencia si la cola de replicación está lleno. En el equipo principal, esto suele ocurrir porque una o más réplicas secundarias tardan conscientes de las operaciones. En el secundario, esto suele ocurrir cuando el servicio es lento para aplicar las operaciones. Cuando la cola ya no está completa, se desactiva la advertencia.

- **SourceId**: System.Replicator
- **Propiedad**: **PrimaryReplicationQueueStatus** o **SecondaryReplicationQueueStatus**, dependiendo de la función de réplica

### <a name="slow-naming-operations"></a>Operaciones de nomenclatura lentas

**System.NamingService** informes de estado en su réplica principal cuando una operación de nomenclatura lleva más aceptable. Ejemplos de operaciones de nomenclatura son [CreateServiceAsync](https://msdn.microsoft.com/library/azure/mt124028.aspx) o [DeleteServiceAsync](https://msdn.microsoft.com/library/azure/mt124029.aspx). Más métodos pueden encontrarse en FabricClient, por ejemplo en [los métodos de administración de servicio](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.servicemanagementclient.aspx) o [métodos de administración de la propiedad](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.propertymanagementclient.aspx).

> [AZURE.NOTE] El servicio de nombres resuelve los nombres de servicio en una ubicación en el clúster y permite a los usuarios administrar propiedades y nombres de servicio. Es una estructura de servicio particiones conserva el servicio. Una de las particiones representa el propietario de la entidad, que contiene los metadatos sobre todos los nombres de servicio tela y servicios. Los nombres de servicio tela se asignan a particiones diferentes, denominadas particiones nombre propietario, por lo que el servicio es extensible. Más información sobre el [servicio de nombres](service-fabric-architecture.md).

Cuando una operación de nomenclatura tarda más de lo esperado, la operación se marca con un informe de advertencia sobre la *réplica principal de la partición de servicio de nomenclatura que sirve la operación*. Si la operación se realiza correctamente, se desactiva la advertencia. Si se completa la operación con un error, el informe de estado incluye detalles sobre el error.

- **SourceId**: System.NamingService
- **Propiedad**: empezar con el prefijo **Duration_** e identifica la operación lenta y el nombre de tela de servicio en la que se aplica la operación. Por ejemplo, si crea el servicio en tela de nombre: / MyApp/MyService tarda mucho tiempo, la propiedad es Duration_AOCreateService.fabric:/MyApp/MyService. AO apunta a la función de la partición de nomenclatura para este nombre y la operación.
- **Pasos siguientes**: ¿por qué se produce un error en la operación de nomenclatura de verificación. Cada operación puede tener causas diferentes. Por ejemplo, eliminar servicio puede bloquearse en un nodo porque mantiene bloquea el host de la aplicación en un nodo debido a un error de usuario en el código de servicio.

En el ejemplo siguiente se muestra una operación de servicio de creación. La operación se realizó más de la duración configurada. AO reintentos y envía trabajo a NO. NO completó la última operación con el tiempo de espera. En este caso, la misma réplica es principal para el AO y sin roles.

```powershell
PartitionId           : 00000000-0000-0000-0000-000000001000
ReplicaId             : 131064359253133577
AggregatedHealthState : Warning
UnhealthyEvaluations  :
                        Unhealthy event: SourceId='System.NamingService', Property='Duration_AOCreateService.fabric:/MyApp/MyService', HealthState='Warning', ConsiderWarningAsError=false.

HealthEvents          :
                        SourceId              : System.RA
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 131064359308715535
                        SentAt                : 4/29/2016 8:38:50 PM
                        ReceivedAt            : 4/29/2016 8:39:08 PM
                        TTL                   : Infinite
                        Description           : Replica has been created.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Ok = 4/29/2016 8:39:08 PM, LastWarning = 1/1/0001 12:00:00 AM

                        SourceId              : System.NamingService
                        Property              : Duration_AOCreateService.fabric:/MyApp/MyService
                        HealthState           : Warning
                        SequenceNumber        : 131064359526778775
                        SentAt                : 4/29/2016 8:39:12 PM
                        ReceivedAt            : 4/29/2016 8:39:38 PM
                        TTL                   : 00:05:00
                        Description           : The AOCreateService started at 2016-04-29 20:39:08.677 is taking longer than 30.000.
                        RemoveWhenExpired     : True
                        IsExpired             : False
                        Transitions           : Error->Warning = 4/29/2016 8:39:38 PM, LastOk = 1/1/0001 12:00:00 AM

                        SourceId              : System.NamingService
                        Property              : Duration_NOCreateService.fabric:/MyApp/MyService
                        HealthState           : Warning
                        SequenceNumber        : 131064360657607311
                        SentAt                : 4/29/2016 8:41:05 PM
                        ReceivedAt            : 4/29/2016 8:41:08 PM
                        TTL                   : 00:00:15
                        Description           : The NOCreateService started at 2016-04-29 20:39:08.689 completed with FABRIC_E_TIMEOUT in more than 30.000.
                        RemoveWhenExpired     : True
                        IsExpired             : False
                        Transitions           : Error->Warning = 4/29/2016 8:39:38 PM, LastOk = 1/1/0001 12:00:00 AM
```

## <a name="deployedapplication-system-health-reports"></a>Informes de estado del sistema de DeployedApplication
**System.Hosting** es la autoridad de entidades implementadas.

### <a name="activation"></a>Activación
System.Hosting informes como Aceptar cuando una aplicación se ha activado correctamente en el nodo. En caso contrario, se detecta un error.

- **SourceId**: System.Hosting
- **Propiedad**: activación, incluida la versión de lanzamiento
- **Pasos siguientes**: si la aplicación está en mal estada, investigar por qué ha fallado la activación.

En el ejemplo siguiente se muestra una activación correcta:

```powershell
PS C:\> Get-ServiceFabricDeployedApplicationHealth -NodeName Node.1 -ApplicationName fabric:/WordCount

ApplicationName                    : fabric:/WordCount
NodeName                           : Node.1
AggregatedHealthState              : Ok
DeployedServicePackageHealthStates :
                                     ServiceManifestName   : WordCountServicePkg
                                     NodeName              : Node.1
                                     AggregatedHealthState : Ok

HealthEvents                       :
                                     SourceId              : System.Hosting
                                     Property              : Activation
                                     HealthState           : Ok
                                     SequenceNumber        : 130743727751144415
                                     SentAt                : 4/24/2015 6:12:55 PM
                                     ReceivedAt            : 4/24/2015 6:13:03 PM
                                     TTL                   : Infinite
                                     Description           : The application was activated successfully.
                                     RemoveWhenExpired     : False
                                     IsExpired             : False
                                     Transitions           : ->Ok = 4/24/2015 6:13:03 PM
```

### <a name="download"></a>Descargar
**System.Hosting** informa de un error si se produce un error en la descarga del paquete de aplicación.

- **SourceId**: System.Hosting
- **Propiedad**: * *Descargar:*RolloutVersion***
- **Pasos siguientes**: investigar por qué ha fallado la descarga en el nodo.

## <a name="deployedservicepackage-system-health-reports"></a>Informes de estado del sistema de DeployedServicePackage
**System.Hosting** es la autoridad de entidades implementadas.

### <a name="service-package-activation"></a>Activación del paquete de servicio
System.Hosting informes como Aceptar si la activación del paquete de servicio en el nodo es correcta. En caso contrario, se detecta un error.

- **SourceId**: System.Hosting
- **Propiedad**: activación
- **Pasos siguientes**: investigar por qué ha fallado la activación.

### <a name="code-package-activation"></a>Activación de paquete de código
**System.Hosting** informes como Aceptar para cada paquete de código si la activación es correcta. Si falla la activación, muestra una advertencia como se ha configurado. Si **CodePackage** no se puede activar o termina con un error mayor que el configurado **CodePackageHealthErrorThreshold**, hospeda informes de un error. Si un paquete de servicio contiene varios paquetes de código, se genera un informe de activación para cada uno.

- **SourceId**: System.Hosting
- **Propiedad**: se usa el prefijo **CodePackageActivation** y contiene el nombre del paquete de código y el punto de entrada como * *CodePackageActivation:*CodePackageName*:*SetupEntryPoint/punto de entrada* ** (por ejemplo, **CodePackageActivation:Code:SetupEntryPoint**)

### <a name="service-type-registration"></a>Registro de tipo de servicio
**System.Hosting** informes como Aceptar si el tipo de servicio se ha registrado correctamente. Se detecta un error si el registro no se ha hecho en el tiempo (como se configura con **ServiceTypeRegistrationTimeout**). Si el tipo de servicio se anula el registro desde el nodo, es porque se ha cerrado el tiempo de ejecución. Hospedaje muestra una advertencia.

- **SourceId**: System.Hosting
- **Propiedad**: se usa el prefijo **ServiceTypeRegistration** y contiene el nombre del tipo de servicio (por ejemplo, **ServiceTypeRegistration:FileStoreServiceType**)

En el ejemplo siguiente se muestra un paquete de servicio implementado buen estado:

```powershell
PS C:\> Get-ServiceFabricDeployedServicePackageHealth -NodeName Node.1 -ApplicationName fabric:/WordCount -ServiceManifestName WordCountServicePkg


ApplicationName       : fabric:/WordCount
ServiceManifestName   : WordCountServicePkg
NodeName              : Node.1
AggregatedHealthState : Ok
HealthEvents          :
                        SourceId              : System.Hosting
                        Property              : Activation
                        HealthState           : Ok
                        SequenceNumber        : 130743727751456915
                        SentAt                : 4/24/2015 6:12:55 PM
                        ReceivedAt            : 4/24/2015 6:13:03 PM
                        TTL                   : Infinite
                        Description           : The ServicePackage was activated successfully.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : ->Ok = 4/24/2015 6:13:03 PM

                        SourceId              : System.Hosting
                        Property              : CodePackageActivation:Code:EntryPoint
                        HealthState           : Ok
                        SequenceNumber        : 130743727751613185
                        SentAt                : 4/24/2015 6:12:55 PM
                        ReceivedAt            : 4/24/2015 6:13:03 PM
                        TTL                   : Infinite
                        Description           : The CodePackage was activated successfully.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : ->Ok = 4/24/2015 6:13:03 PM

                        SourceId              : System.Hosting
                        Property              : ServiceTypeRegistration:WordCountServiceType
                        HealthState           : Ok
                        SequenceNumber        : 130743727753644473
                        SentAt                : 4/24/2015 6:12:55 PM
                        ReceivedAt            : 4/24/2015 6:13:03 PM
                        TTL                   : Infinite
                        Description           : The ServiceType was registered successfully.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : ->Ok = 4/24/2015 6:13:03 PM
```

### <a name="download"></a>Descargar
**System.Hosting** informa de un error si se produce un error en la descarga del paquete de servicio.

- **SourceId**: System.Hosting
- **Propiedad**: * *Descargar:*RolloutVersion***
- **Pasos siguientes**: investigar por qué ha fallado la descarga en el nodo.

### <a name="upgrade-validation"></a>Validación de actualización
**System.Hosting** informa de un error si se produce un error de validación durante la actualización o si se produce un error en la actualización en el nodo.

- **SourceId**: System.Hosting
- **Propiedad**: se usa el prefijo **FabricUpgradeValidation** y contiene la versión de actualización
- **Descripción**: el error de destino

## <a name="next-steps"></a>Pasos siguientes
[Ver informes de mantenimiento de tela de servicio](service-fabric-view-entities-aggregated-health.md)

[Cómo informar y comprobar el estado del servicio](service-fabric-diagnostics-how-to-report-and-check-service-health.md)

[Supervisar y diagnosticar servicios localmente](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

[Actualización de la aplicación de servicio tela](service-fabric-application-upgrade.md)
