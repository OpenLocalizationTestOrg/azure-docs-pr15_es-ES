<properties
   pageTitle="Solución de problemas de las actualizaciones de la aplicación | Microsoft Azure"
   description="Este artículo describen algunos problemas comunes alrededor de la actualización de una aplicación de servicio tela y cómo resolverlos."
   services="service-fabric"
   documentationCenter=".net"
   authors="mani-ramaswamy"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/14/2016"
   ms.author="subramar"/>

# <a name="troubleshoot-application-upgrades"></a>Solucionar problemas de actualizaciones de las aplicaciones

Este artículo describen algunos de los problemas comunes de actualizar una aplicación de tela de servicio de Azure y cómo resolverlos.

## <a name="troubleshoot-a-failed-application-upgrade"></a>Solucionar problemas de una actualización de la aplicación que ha fallado

Cuando se produce un error en una actualización, el resultado del comando **Get-ServiceFabricApplicationUpgrade** contiene información adicional para depurar el error.  En la lista siguiente especifica cómo se puede utilizar la información adicional:

1. Identificar el tipo de error.
2. Identificar el motivo del error.
3. Aislar uno o varios componentes de errores para una mayor investigación.

Esta información está disponible cuando tela servicio detecta el error independientemente de si es la **FailureAction** deshacer o suspender la actualización.

### <a name="identify-the-failure-type"></a>Identificar el tipo de error

En la salida de **Get-ServiceFabricApplicationUpgrade**, **FailureTimestampUtc** identifica la marca de tiempo (en UTC) en el que se detectó un error de actualización tela de servicio y **FailureAction** activó. **FailureReason** identifica uno de los tres posibles causas de alto nivel del error:

1. UpgradeDomainTimeout - indica que un determinado dominio actualización tardó demasiado tiempo para completar y **UpgradeDomainTimeout** ha expirado.
2. OverallUpgradeTimeout - indica que la actualización global tardó demasiado tiempo en completarse y **UpgradeTimeout** ha expirado.
3. Comprobación del estado - indica que después de actualizar un dominio de actualización, la aplicación permaneció mal estada según las directivas de mantenimiento especificada y **HealthCheckRetryTimeout** ha expirado.

Estas entradas solo aparecerán en los resultados cuando la actualización se produce un error e inicia deshaciendo. Para obtener más información se muestra según el tipo de error.

### <a name="investigate-upgrade-timeouts"></a>Investigar tiempos de espera de actualización

Actualizar errores normalmente son causados por problemas de disponibilidad de servicio de tiempo de espera. El resultado que sigue este párrafo es típico de actualizaciones donde no iniciar en la nueva versión de código réplicas de servicio o instancias. El campo **UpgradeDomainProgressAtFailure** captura una instantánea de cualquier trabajo pendiente de actualización en el momento del error.

~~~
PS D:\temp> Get-ServiceFabricApplicationUpgrade fabric:/DemoApp

ApplicationName                : fabric:/DemoApp
ApplicationTypeName            : DemoAppType
TargetApplicationTypeVersion   : v2
ApplicationParameters          : {}
StartTimestampUtc              : 4/14/2015 9:26:38 PM
FailureTimestampUtc            : 4/14/2015 9:27:05 PM
FailureReason                  : UpgradeDomainTimeout
UpgradeDomainProgressAtFailure : MYUD1

                                 NodeName            : Node4
                                 UpgradePhase        : PostUpgradeSafetyCheck
                                 PendingSafetyChecks :
                                     WaitForPrimaryPlacement - PartitionId: 744c8d9f-1d26-417e-a60e-cd48f5c098f0

                                 NodeName            : Node1
                                 UpgradePhase        : PostUpgradeSafetyCheck
                                 PendingSafetyChecks :
                                     WaitForPrimaryPlacement - PartitionId: 4b43f4d8-b26b-424e-9307-7a7a62e79750
UpgradeState                   : RollingBackCompleted
UpgradeDuration                : 00:00:46
CurrentUpgradeDomainDuration   : 00:00:00
NextUpgradeDomain              :
UpgradeDomainsStatus           : { "MYUD1" = "Completed";
                                 "MYUD2" = "Completed";
                                 "MYUD3" = "Completed" }
UpgradeKind                    : Rolling
RollingUpgradeMode             : UnmonitoredAuto
ForceRestart                   : False
UpgradeReplicaSetCheckTimeout  : 00:00:00
~~~

En este ejemplo, la actualización de error en el dominio de actualización *MYUD1* y se detiene dos particiones (*744c8d9f-1d26-417e-a60e-cd48f5c098f0* y *4b43f4d8-b26b-424e-9307-7a7a62e79750*). Las particiones se detiene porque el tiempo de ejecución no pudo colocar réplicas principales (*WaitForPrimaryPlacement*) en los nodos de destino *nodo 1* y *Nodo4*.

El comando **Get-ServiceFabricNode** puede utilizarse para comprobar que estos dos nodos en el dominio de actualización *MYUD1*. La *UpgradePhase* indica *PostUpgradeSafetyCheck*, lo que significa que estas comprobaciones de seguridad se producen después de que han finalizado la actualización de todos los nodos de actualizar el dominio. Toda esta información indica un posible problema con la nueva versión del código de la aplicación. Los problemas más comunes son los errores de servicio en la abrir o promoción a rutas de código principal.

Un *UpgradePhase* de *PreUpgradeSafetyCheck* significa que ha habido problemas de preparación del dominio de actualización antes de se ha realizado. En este caso, los problemas más comunes son los errores de servicio en el cierre o degradación desde rutas de acceso de código principal.

La actual **UpgradeState** es *RollingBackCompleted*, por lo que debe haber realizado la actualización original con una operación de deshacer **FailureAction**, que deshace automáticamente la actualización en caso de error. Si se realizó la actualización original con un manual **FailureAction**, a continuación, la actualización podría en su lugar se en un estado de suspensión para permitir directo depuración de la aplicación.

### <a name="investigate-health-check-failures"></a>Investigar errores de comprobación de estado

Errores de comprobación de estado pueden activarse mediante varios problemas que pueden ocurrir después de actualizar y pasar todas las comprobaciones de seguridad de todos los nodos en un dominio de actualización finalización. El resultado que sigue este párrafo es típico de un error de actualización debido a comprobaciones de estado error. El campo **UnhealthyEvaluations** captura una instantánea de comprobaciones de estado de error en el momento de la actualización según la [Directiva de mantenimiento](service-fabric-health-introduction.md)de especificada.

~~~
PS D:\temp> Get-ServiceFabricApplicationUpgrade fabric:/DemoApp

ApplicationName                         : fabric:/DemoApp
ApplicationTypeName                     : DemoAppType
TargetApplicationTypeVersion            : v4
ApplicationParameters                   : {}
StartTimestampUtc                       : 4/24/2015 2:42:31 AM
UpgradeState                            : RollingForwardPending
UpgradeDuration                         : 00:00:27
CurrentUpgradeDomainDuration            : 00:00:27
NextUpgradeDomain                       : MYUD2
UpgradeDomainsStatus                    : { "MYUD1" = "Completed";
                                          "MYUD2" = "Pending";
                                          "MYUD3" = "Pending" }
UnhealthyEvaluations                    :
                                          Unhealthy services: 50% (2/4), ServiceType='PersistedServiceType', MaxPercentUnhealthyServices=0%.

                                          Unhealthy service: ServiceName='fabric:/DemoApp/Svc3', AggregatedHealthState='Error'.

                                              Unhealthy partitions: 100% (1/1), MaxPercentUnhealthyPartitionsPerService=0%.

                                              Unhealthy partition: PartitionId='3a9911f6-a2e5-452d-89a8-09271e7e49a8', AggregatedHealthState='Error'.

                                                  Error event: SourceId='Replica', Property='InjectedFault'.

                                          Unhealthy service: ServiceName='fabric:/DemoApp/Svc2', AggregatedHealthState='Error'.

                                              Unhealthy partitions: 100% (1/1), MaxPercentUnhealthyPartitionsPerService=0%.

                                              Unhealthy partition: PartitionId='744c8d9f-1d26-417e-a60e-cd48f5c098f0', AggregatedHealthState='Error'.

                                                  Error event: SourceId='Replica', Property='InjectedFault'.

UpgradeKind                             : Rolling
RollingUpgradeMode                      : Monitored
FailureAction                           : Manual
ForceRestart                            : False
UpgradeReplicaSetCheckTimeout           : 49710.06:28:15
HealthCheckWaitDuration                 : 00:00:00
HealthCheckStableDuration               : 00:00:10
HealthCheckRetryTimeout                 : 00:00:10
UpgradeDomainTimeout                    : 10675199.02:48:05.4775807
UpgradeTimeout                          : 10675199.02:48:05.4775807
ConsiderWarningAsError                  :
MaxPercentUnhealthyPartitionsPerService :
MaxPercentUnhealthyReplicasPerPartition :
MaxPercentUnhealthyServices             :
MaxPercentUnhealthyDeployedApplications :
ServiceTypeHealthPolicyMap              :
~~~

Investigar errores de comprobación de estado, primero requiere entender el modelo de estado del servicio tela. Pero incluso sin una descripción detallada, podemos ver que están mal Estados dos servicios: *tela: / DemoApp/Svc3* y *tela: / DemoApp/Svc2*, junto con los informes de estado de error ("InjectedFault" en este caso). En este ejemplo, dos fuera de cuatro servicios están mal Estados, que está por debajo del objetivo predeterminado de 0% mal estado (*MaxPercentUnhealthyServices*).

La actualización se ha suspendido tras error especificando una **FailureAction** de manual al iniciar la actualización. Este modo nos permite investigar el sistema activo en el estado de error antes de realizar cualquier otra acción.

### <a name="recover-from-a-suspended-upgrade"></a>Recuperarse de una actualización suspendida

Con una deshacer **FailureAction**, no hay ninguna recuperación necesaria, ya que la actualización se deshace automáticamente al error. Con un manual **FailureAction**, hay varias opciones de recuperación:

1. Desencadenar manualmente una operación de deshacer
2. Complete el resto de la actualización de forma manual
3. Reanudar la supervisadas actualización

El comando de **Inicio ServiceFabricApplicationRollback** puede utilizarse en cualquier momento para iniciar la restauración de la aplicación. Una vez que el comando devuelve correctamente, la solicitud de deshacer se ha registrado en el sistema y se inicia poco después.

El comando **Reanudar ServiceFabricApplicationUpgrade** puede usarse para manualmente, complete el resto de la actualización de un dominio de actualización en un momento. En este modo, se realizan comprobaciones de seguridad sólo por el sistema. Se realizan comprobaciones de estado de no más. Este comando solo puede usarse cuando la *UpgradeState* muestra *RollingForwardPending*, lo que significa que el dominio de actualización actual ha finalizado la actualización, pero no se inició (pendiente) siguiente.

El comando de **Actualización ServiceFabricApplicationUpgrade** puede usarse para reanudar la actualización supervisada con ambos seguridad y mantenimiento comprueba llevando a cabo.

~~~
PS D:\temp> Update-ServiceFabricApplicationUpgrade fabric:/DemoApp -UpgradeMode Monitored

UpgradeMode                             : Monitored
ForceRestart                            :
UpgradeReplicaSetCheckTimeout           :
FailureAction                           :
HealthCheckWaitDuration                 :
HealthCheckStableDuration               :
HealthCheckRetryTimeout                 :
UpgradeTimeout                          :
UpgradeDomainTimeout                    :
ConsiderWarningAsError                  :
MaxPercentUnhealthyPartitionsPerService :
MaxPercentUnhealthyReplicasPerPartition :
MaxPercentUnhealthyServices             :
MaxPercentUnhealthyDeployedApplications :
ServiceTypeHealthPolicyMap              :

PS D:\temp>
~~~

La actualización continúa desde la actualización dominio donde se ha suspendido última y el uso de la misma actualizar parámetros y directivas de mantenimiento como antes. Si es necesario, cualquiera de los parámetros de actualización y directivas de estado que se muestra en el resultado anterior pueden cambiarse en el mismo comando cuando se reanuda la actualización. En este ejemplo, la actualización se reanuda en el modo de supervisadas, con los parámetros y las directivas de estado sin cambios.

## <a name="further-troubleshooting"></a>Solucionar problemas

### <a name="service-fabric-is-not-following-the-specified-health-policies"></a>Tela de servicio no está siguiendo las directivas de mantenimiento especificado

Causa posible 1:

Servicio tela traduce todos los porcentajes en números reales de entidades (por ejemplo, réplicas, particiones y servicios) para la evaluación de mantenimiento y siempre se redondea hacia arriba a entidades completas. Por ejemplo, si el máximo _MaxPercentUnhealthyReplicasPerPartition_ es 21% y hay cinco réplicas, tela de servicio permite hasta dos réplicas negativos (ese is,'Math.Ceiling (5\*0.21)). Por lo tanto, las directivas de mantenimiento se deben establecer en consecuencia.

Causa posible 2:

Las directivas de mantenimiento se especifican en porcentajes de servicios total y las instancias de servicio no es específico. Por ejemplo, antes de la actualización, si una aplicación tiene cuatro servicio instancias A, B, C y D, donde servicio D es negativo, pero con poco impacto a la aplicación. Desea omitir el servicio mal estado conocido D durante la actualización y establecer el parámetro *MaxPercentUnhealthyServices* 25%, suponiendo que solo A, B y C necesita estar en buen estado.

Sin embargo, durante la actualización, d. puede convertirse en buen estado mientras C se convierte en mal estado. La actualización todavía tendría éxito porque solo un 25% de los servicios no funcionan correctamente. Sin embargo, puede ocasionar errores imprevistos debido a C está mal inesperadamente en lugar de D. En esta situación, se debe modelar d. como un tipo de servicio diferente en A, B y C. Dado que las directivas de mantenimiento se especifica por tipo de servicio, se pueden aplicar umbrales de porcentaje negativo diferente a distintos servicios. 

### <a name="i-did-not-specify-a-health-policy-for-application-upgrade-but-the-upgrade-still-fails-for-some-time-outs-that-i-never-specified"></a>No ha especificado una directiva de estado de actualización de la aplicación, pero sigue sin funciona la actualización para algunos tiempos de espera nunca especificada

Cuando no se proporcionan directivas de estado a la solicitud de actualización, se toman de la *ApplicationManifest.xml* de la versión actual de la aplicación. Por ejemplo, si está actualizando X de la aplicación de la versión 1.0 a la versión 2.0, directivas de mantenimiento de la aplicación especificada para en la versión 1.0 se usan. Si una directiva de mantenimiento diferentes se debe usar para la actualización, la directiva debe especificarse como parte de la llamada de API de actualización de aplicaciones. Sólo aplicarán las directivas especificadas como parte de la llamada a la API durante la actualización. Una vez completada la actualización, se usan las directivas especificadas en el *ApplicationManifest.xml* .

### <a name="incorrect-time-outs-are-specified"></a>Se especifican los tiempos de espera incorrectas

Puede que se haya preguntado sobre lo que sucede cuando se establecen los tiempos de espera de forma incoherente. Por ejemplo, puede tener un *UpgradeTimeout* que es menor que la *UpgradeDomainTimeout*. La respuesta es que se devuelve un error. Si la *UpgradeDomainTimeout* es menor que la suma de *HealthCheckWaitDuration* y *HealthCheckRetryTimeout*, o si *UpgradeDomainTimeout* es menor que la suma de *HealthCheckWaitDuration* y *HealthCheckStableDuration*, se devuelven errores.

### <a name="my-upgrades-are-taking-too-long"></a>Mi actualizaciones tardan demasiado

El tiempo para completar una actualización depende de las comprobaciones de estado y tiempos de espera especificados. Comprobaciones de estado y tiempos de espera dependen de cuánto tiempo tarda copiar, implementar y estabilizar la aplicación. Ser demasiado agresiva con los tiempos de espera, quizá más errores de actualización, por lo que recomendamos cautela comenzando con los tiempos de espera más larga.

Esto es un repaso rápido sobre cómo interactúan los tiempos de espera con los tiempos de actualización:

Actualizaciones para un dominio de actualización no puede completar más rápido que *HealthCheckWaitDuration* + *HealthCheckStableDuration*.

Error de actualización no puede ser más rápido que *HealthCheckWaitDuration* + *HealthCheckRetryTimeout*.

El tiempo de actualización de un dominio de actualización está limitado por *UpgradeDomainTimeout*.  Si *HealthCheckRetryTimeout* y *HealthCheckStableDuration* estén distinto de cero y el estado de la aplicación le permite conservar el cambio y hacia atrás, a continuación, la actualización finalmente agota el tiempo en *UpgradeDomainTimeout*. *UpgradeDomainTimeout* empieza a contar hacia abajo una vez que comienza la actualización para actualizar el dominio actual.

## <a name="next-steps"></a>Pasos siguientes

[Actualizar la aplicación usando Visual Studio](service-fabric-application-upgrade-tutorial.md) le guiará a través de la actualización de una aplicación mediante Visual Studio.

[Actualizar su Powershell de uso de aplicación](service-fabric-application-upgrade-tutorial-powershell.md) le guiará a través de una actualización de la aplicación con PowerShell.

Controlar cómo se actualiza la aplicación con [Parámetros de actualización](service-fabric-application-upgrade-parameters.md).

Hacer que las actualizaciones de aplicación compatibles por aprender a usar la [Serialización de datos](service-fabric-application-upgrade-data-serialization.md).

Obtenga información sobre cómo usar funciones avanzadas mientras actualiza su aplicación puede hacer referencia a [Temas avanzados](service-fabric-application-upgrade-advanced.md).

Solucionar problemas comunes en las actualizaciones de la aplicación puede hacer referencia a los pasos de [Solución de problemas de actualizaciones de las aplicaciones](service-fabric-application-upgrade-troubleshooting.md).
 
