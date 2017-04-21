
<properties
   pageTitle="Actualización de la aplicación: actualizar parámetros | Microsoft Azure"
   description="Describe los parámetros relacionados con la actualización de una aplicación de servicio tela, incluidos comprobaciones de estado para realizar y directivas para deshacer automáticamente la actualización."
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



# <a name="application-upgrade-parameters"></a>Parámetros de actualización de la aplicación

En este artículo se describe los distintos parámetros que se aplican durante la actualización de una aplicación de Azure servicio tela. Los parámetros incluyen el nombre y la versión de la aplicación. Son los botones que controlan la interacción con los datos y las comprobaciones de estado que se aplican durante la actualización y especifican las directivas que deben aplicarse cuando se produce un error en una actualización.


<br>

| Parámetro | Descripción |
| --- | --- |
| ApplicationName | Nombre de la aplicación que está actualizando. Ejemplos: tela: / VisualObjects, tela: / ClusterMonitor  |
| TargetApplicationTypeVersion | Tipo de la versión de la aplicación que los objetivos de la actualización. |
| FailureAction | Acción realizada por servicio tela cuando se produce un error en la actualización. La aplicación puede volver a la versión preliminar de actualización (revertir), o puede detener la actualización en el dominio de actualización actual. En este caso, también se cambia el modo de actualización manual. Los valores permitidos son Deshacer y Manual. |
| HealthCheckWaitDurationSec | El tiempo de espera (en segundos) cuando haya finalizado la actualización en actualizar el dominio antes de servicio tela evalúa el estado de la aplicación. Esta duración también se consideran como la hora en que una aplicación debe estar ejecutándose antes de que se puedan considerar correcto. Si se pasa la comprobación del estado, el proceso de actualización continúa a la siguiente actualización del dominio.  Si se produce un error en la comprobación del estado, servicio tela espera un intervalo (la UpgradeHealthCheckInterval) antes de reintentar la comprobación del estado de nuevo hasta que se alcance el HealthCheckRetryTimeout. El valor predeterminado y recomendado es 0 segundos. |
| HealthCheckRetryTimeoutSec | La duración (en segundos) que tela servicio continúa realizar la evaluación de mantenimiento antes de declarar la actualización como error. El valor predeterminado es 600 segundos. Esta duración se inicia una vez alcanzado HealthCheckWaitDuration. En este HealthCheckRetryTimeout servicio tela puede realizar varias comprobaciones de estado de mantenimiento de la aplicación. El valor predeterminado es de 10 minutos y debe personalizarse adecuadamente para su aplicación. |
| HealthCheckStableDurationSec | Duración (en segundos) para comprobar que la aplicación está estable antes de pasar a la siguiente actualización del dominio o finalizar la actualización. Esta duración esperar a que se utiliza para evitar sin detectar cambios de estado derecha después de realiza la comprobación del estado. El valor predeterminado es 120 segundos y debe personalizarse adecuadamente para su aplicación. |
| UpgradeDomainTimeoutSec | Tiempo máximo (en segundos) para actualizar un único dominio de actualización. Si se alcanza este tiempo de espera, la actualización se detiene y continúa en función de la configuración de UpgradeFailureAction. El valor predeterminado no es nunca (infinito) y debe personalizarse adecuadamente para su aplicación. |
| UpgradeTimeout | Tiempo de espera (en segundos) que se aplica para toda la actualización. Si se alcanza este tiempo de espera, se activa la actualización se detiene y UpgradeFailureAction. El valor predeterminado no es nunca (infinito) y debe personalizarse adecuadamente para su aplicación. |
| UpgradeHealthCheckInterval | La frecuencia que se comprueba el estado de salud. Este parámetro se especifica en la sección ClusterManager de _clúster_ del _manifiestos_y no se especifica como parte del cmdlet actualización. El valor predeterminado es 60 segundos.  |






<br>
## <a name="service-health-evaluation-during-application-upgrade"></a>Evaluación de mantenimiento del servicio durante la actualización de la aplicación

<br>
Los criterios de evaluación de mantenimiento son opcionales. Si no se especifican los criterios de evaluación de estado cuando se inicia una actualización, servicio tela usa las directivas de mantenimiento de aplicación especificadas en el ApplicationManifest.xml de la instancia de aplicación.


<br>

| Parámetro | Descripción |
| --- | --- |
| ConsiderWarningAsError | Valor predeterminado es False. Tratar los eventos de estado de advertencia de la aplicación como errores al evaluar el estado de la aplicación durante la actualización. De forma predeterminada, tela de servicio no se evalúa los eventos de estado de advertencia para poder errores (errores), la actualización puede continuar incluso si hay eventos de advertencia.   |
| MaxPercentUnhealthyDeployedApplications | Valor predeterminado y recomendado es 0. Especificar el número máximo de aplicaciones distribuidas (consulte la [sección estado actual](service-fabric-health-introduction.md)) que puede estar mal estado antes de que la aplicación se considera mal estada y errores en la actualización. Este parámetro define el estado de la aplicación en el nodo y ayuda a detecta problemas durante la actualización. Normalmente, las réplicas de la aplicación Obtén el equilibrio de carga al otro nodo, lo que permite la aplicación aparezca en buen estado, lo que permite la actualización continuar. Especificando un mantenimiento MaxPercentUnhealthyDeployedApplications estricta tela de servicio puede detectar un problema con el paquete de aplicación rápidamente y ayudar a producir un error de actualización rápida. |
| MaxPercentUnhealthyServices | Valor predeterminado y recomendado es 0. Especificar el número máximo de servicios de la instancia de aplicación que puede estar mal estada antes de que la aplicación se considera mal estada y errores en la actualización. |
| MaxPercentUnhealthyPartitionsPerService | Valor predeterminado y recomendado es 0. Especifique el número máximo de particiones en un servicio que puede estar mal estado antes de que el servicio se considera en mal estado. |
| MaxPercentUnhealthyReplicasPerPartition | Valor predeterminado y recomendado es 0. Especificar el número máximo de réplicas partición que puede estar mal estado antes de que la partición se considera en mal estada. |
| UpgradeReplicaSetCheckTimeout | **Servicio de estado**--dentro de un único dominio de actualización, tela de servicio intenta asegurarse de que están disponibles más instancias del servicio. Si el recuento de la instancia de destino es más de uno, servicio tela espera más de una instancia esté disponible, hasta un valor máximo de tiempo de espera. Este tiempo de espera se especifica mediante la propiedad UpgradeReplicaSetCheckTimeout. Si se agota el tiempo de espera, servicio tela continúa con la actualización, independientemente del número de instancias de servicio. Si el recuento de la instancia de destino, tela de servicio no espera y continúa inmediatamente con la actualización. **Servicio con estado**--dentro de un único dominio de actualización, servicio tela intenta asegurarse de que el conjunto de réplica tiene quórum. Servicio tela espera un quórum esté disponible, hasta un valor máximo de tiempo de espera (especificado por la propiedad UpgradeReplicaSetCheckTimeout). Si se agota el tiempo de espera, servicio tela continúa con la actualización, independientemente de quórum. Esta opción está conjunto como nunca (infinito) cuando confirmar y 900 segundos al deshacer. |
| ForceRestart | Si actualiza una configuración o un paquete de datos sin actualizar el código de servicio, el servicio se reinicia solo si la propiedad ForceRestart está establecida en true. Una vez completada la actualización, servicio tela notifica al servicio que un nuevo paquete de configuración o un paquete de datos está disponible. El servicio es responsable de aplicar los cambios. Si es necesario, puede reiniciar el servicio propiamente dicho. |



<br>
<br>
Pueden especificar los criterios MaxPercentUnhealthyServices, MaxPercentUnhealthyPartitionsPerService y MaxPercentUnhealthyReplicasPerPartition por tipo de servicio de una instancia de la aplicación. Establecer estos parámetros por servicio permite una aplicación contienen tipos diferentes servicios con las directivas de evaluación diferente. Por ejemplo, un tipo de servicio de estado de la puerta de enlace puede tener un MaxPercentUnhealthyPartitionsPerService que es diferente de un tipo de servicio motor con estado de una instancia de aplicación en particular.

## <a name="next-steps"></a>Pasos siguientes

[Actualizar la aplicación usando Visual Studio](service-fabric-application-upgrade-tutorial.md) le guiará a través de la actualización de una aplicación mediante Visual Studio.

[Actualizar su Powershell de uso de aplicación](service-fabric-application-upgrade-tutorial-powershell.md) le guiará a través de una actualización de la aplicación con PowerShell.

Hacer que las actualizaciones de aplicación compatibles por aprender a usar la [Serialización de datos](service-fabric-application-upgrade-data-serialization.md).

Obtenga información sobre cómo usar funciones avanzadas mientras actualiza su aplicación puede hacer referencia a [Temas avanzados](service-fabric-application-upgrade-advanced.md).

Solucionar problemas comunes en las actualizaciones de la aplicación puede hacer referencia a los pasos de [Solución de problemas de actualizaciones de las aplicaciones](service-fabric-application-upgrade-troubleshooting.md).
 
