<properties
   pageTitle="Recuperación de servicio tela Azure | Microsoft Azure"
   description="Azure tela de servicio ofrece las capacidades necesarias para tratar con todos los tipos de desastres. En este artículo se describe los tipos de desastres que pueden ocurrir y cómo tratar con ellos."
   services="service-fabric"
   documentationCenter=".net"
   authors="seanmck"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotNet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="08/10/2016"
   ms.author="seanmck"/>

# <a name="disaster-recovery-in-azure-service-fabric"></a>Recuperación en tela de servicio de Azure

Una parte esencial de ofrecer una aplicación de nube de alta disponibilidad es asegurarse de que pueden superar todos los distintos tipos de errores, incluidos los que están fuera de su control. Este artículo describe la distribución física de un clúster de tela de servicio de Azure en el contexto de posibles desastres y proporciona instrucciones sobre cómo tratar tales desastres para limitar o eliminar el riesgo de pérdida de datos o el tiempo de inactividad.

## <a name="physical-layout-of-service-fabric-clusters-in-azure"></a>Diseño físico de servicio tela clústeres en Azure

Para conocer el riesgo que representan diferentes tipos de errores, resulta útil saber cómo clústeres física aparecen en Azure.

Cuando se crea un clúster de servicio tela en Azure, debe elegir una región donde se va a hospedar. La infraestructura de Azure luego Aprovisiona los recursos para ese clúster dentro de la región, en particular, el número de máquinas virtuales (VM) solicitado. Veamos con más de cerca cómo y dónde se aprovisiona esas máquinas virtuales.

### <a name="fault-domains"></a>Dominios de error

De forma predeterminada, las máquinas virtuales en el clúster se distribuyen uniformemente entre grupos lógicos conocidos como dominios de error (FDs), que los equipos en función de los posibles errores en el hardware del host de segmento. En concreto, si dos máquinas virtuales residen en dos FDs distinct, puede estar seguro de que no comparten el mismo origen o red interruptor. Como resultado, una red local o corte de luz que afectan a una VM no afectará a otros, permitiendo servicio tela a equilibrar la carga de trabajo del equipo que no responde dentro del clúster.

Puede visualizar el diseño del clúster a través de dominios de error con el mapa de clúster proporcionado en el [Explorador de tela de servicio](service-fabric-visualizing-your-cluster.md):

![Nodos extendido por dominios de error en el Explorador de tela de servicio][sfx-cluster-map]

>[AZURE.NOTE] El otro eje en el mapa de clúster muestra los dominios de actualización, que lógicamente agrupan nodos basándose en las actividades de mantenimiento planeado. Servicio tela clústeres en Azure siempre están dispuestos en cinco dominios de actualización.

### <a name="geographic-distribution"></a>Distribución geográfica

Actualmente hay [26 regiones Azure en todo el mundo][azure-regions], con varios anuncia más. Una región individual puede contener uno o varios centros de datos físicos según demanda y la disponibilidad de ubicaciones adecuados, entre otros factores. Sin embargo, tenga en cuenta que incluso en las regiones que contienen varios centros de datos físicos, no hay ninguna garantía que máquinas virtuales de clúster se extienden uniformemente en esas ubicaciones físicas. De hecho, actualmente, todas las máquinas virtuales de un clúster determinado aprovisionadas dentro de un solo sitio físico.

## <a name="dealing-with-failures"></a>Tratar con errores

Existen varios tipos de errores que pueden afectar el clúster, cada una con su propio mitigación. Veamos ellas en orden de probabilidad de que se produzca.

### <a name="individual-machine-failures"></a>Errores de equipo individuales

Como se mencionó, errores de equipo individuales, dentro de la máquina virtual o en el hardware o el software que aloja dentro de un dominio de error no presenten riesgos en sus propias. Servicio tela normalmente se detectar el error en segundos y responder en consecuencia según el estado del clúster. Por ejemplo, si el nodo aloja las réplicas principales para una partición, se elige un nuevo principal réplicas secundario de la partición. Cuando Azure hace que el equipo ha fallado copia de seguridad, se a unir el clúster automáticamente y tomar una vez más en su parte de la carga de trabajo.

### <a name="multiple-concurrent-machine-failures"></a>Varios errores de equipo simultáneas

Mientras dominios de error significativamente reducen el riesgo de errores de equipo simultáneas, siempre es la posibilidad de varios errores aleatorios reducir simultáneamente varios equipos en un clúster.

En general, como la mayoría de los nodos siguen estando disponibles, el clúster seguirá funcionan, aunque a menor capacidad como réplicas con estado obtengan empaquetadas en un conjunto más pequeño de equipos y están disponibles para distribuir carga menos instancias sin estado.

#### <a name="quorum-loss"></a>Pérdida de quórum

Si la mayoría de las réplicas de partición de un servicio con estado ir hacia abajo, dicha partición entra en un estado conocido como "pérdida de quórum". En este momento, servicio tela deja de permitir la escritura en esa partición para garantizar que su estado permanece coherente y confiable. De hecho, elegimos aceptar un período de disponibilidad para asegurarse de que los clientes no se les indica el que se guardaron sus datos cuando en realidad no era. Tenga en cuenta que si han optado por permitir lecturas desde réplicas secundarias de ese servicio con estado, puede seguir realizarlas operaciones en este estado de lectura. Una partición permanece en pérdida de quórum hasta un número suficiente de réplicas que vuelva o el Administrador de clústeres fuerza el sistema mover con la [API de reparación ServiceFabricPartition][repair-partition-ps].

>[AZURE.WARNING] Realizar una acción de reparación mientras la réplica principal está presionado da de pérdida de datos.

Servicios del sistema también pueden sufrir pérdida de quórum, con el impacto ser específico para el servicio en cuestión. Por ejemplo, pérdida de quórum en el servicio de nomenclatura afectará resolución de nombres, mientras que la pérdida de quórum en el servicio de administrador de migración tras error bloqueará migraciones tras error y la creación de nuevos servicios. Tenga en cuenta que a diferencia de sus propios servicios, intentando reparar los servicios del sistema *no* se recomienda. En su lugar, es preferible simplemente espere hasta que devuelven las réplicas hacia abajo.

#### <a name="minimizing-the-risk-of-quorum-loss"></a>Reducir el riesgo de pérdida de quórum

Puede minimizar el riesgo de pérdida de quórum aumentando el tamaño del conjunto de réplica de destino para el servicio. Resulta útil pensar en el número de réplicas necesita en términos del número de nodos no disponibles que puede tolerar en una vez mientras aún disponibles para escritura, teniendo en cuenta esa aplicación o actualizaciones de clúster pueden hacer que los nodos disponible temporalmente, además de errores de hardware.

Tenga en cuenta los siguientes ejemplos suponiendo que haya configurado sus servicios para tener un MinReplicaSetSize de tres, el valor mínimo recomendado para servicios de producción. Con un TargetReplicaSetSize de tres (uno principal y dos secundarios), pérdida de quórum producirá un error de hardware durante una actualización (dos réplicas hacia abajo) y el servicio se convertirá en sólo lectura. Como alternativa, si tiene cinco réplicas, es que pueda resistir dos errores durante la actualización (tres réplicas hacia abajo), como las dos réplicas restantes aún pueden formar un quórum dentro del conjunto de réplica mínimo.

### <a name="data-center-outages-or-destruction"></a>Interrupciones del centro de datos o destrucción

En algunas ocasiones, los centros de datos físicos esté disponibles temporalmente debido a la pérdida de conectividad de red o power. En estos casos, el servicio tela clústeres y las aplicaciones de la misma forma no estará disponibles pero se conservarán los datos. Para clústeres que se ejecutan en Azure, puede ver las actualizaciones en interrupciones en la [página de estado de Azure][azure-status-dashboard].

En el evento es muy probable que se destruye un centro de datos físico completo, los clústeres tela servicio hospedados allí se perderán, junto con su estado.

Para proteger esta posibilidad, es muy importante periódicamente [el estado de la copia de seguridad](service-fabric-reliable-services-backup-restore.md) en una tienda geo redundantes y asegúrese de que se valida la capacidad de restaurar. ¿Con qué frecuencia se realiza una copia dependerá el objetivo de punto de recuperación (RPO). Incluso si no ha implementado totalmente copia de seguridad y restauración todavía, debe implementar un controlador para la `OnDataLoss` evento para que puedan iniciar cuando se produce como sigue:

```c#
protected virtual Task<bool> OnDataLoss(CancellationToken cancellationToken)
{
  ServiceEventSource.Current.ServiceMessage(this, "OnDataLoss event received.");
  return Task.FromResult(true);
}
```


### <a name="software-failures-and-other-sources-of-data-loss"></a>Errores de software y otros orígenes de pérdida de datos

Como una causa de pérdida de datos, son más comunes que los errores del centro de datos extendido defectos de código de servicios, los errores humanos operativos y violaciones de seguridad. Sin embargo, en todos los casos, la estrategia de recuperación es el mismo: realizar copias de seguridad periódicas de todos los servicios con estado y ejercicio su capacidad para restaurar dicho estado.

## <a name="next-steps"></a>Pasos siguientes

- Obtenga información sobre cómo simular diversos errores con el [marco de prueba](service-fabric-testability-overview.md)
- Leer otros recursos de alta disponibilidad y la recuperación. Microsoft ha publicado una gran cantidad de información sobre estos temas. Mientras algunos de estos documentos se refieren a técnicas específicas para su uso en otros productos, que contienen muchos mejores prácticas generales que puede aplicar en el contexto del servicio tela:
 - [Lista de comprobación de disponibilidad](../best-practices-availability-checklist.md)
 - [Realizar un ejercicio de recuperación de desastres](../sql-database/sql-database-disaster-recovery-drills.md)
 - [Recuperación y alta disponibilidad para las aplicaciones de Azure][dr-ha-guide]


<!-- External links -->

[repair-partition-ps]: https://msdn.microsoft.com/library/mt163522.aspx
[azure-status-dashboard]:https://azure.microsoft.com/status/
[azure-regions]: https://azure.microsoft.com/regions/
[dr-ha-guide]: https://msdn.microsoft.com/library/azure/dn251004.aspx


<!-- Images -->

[sfx-cluster-map]: ./media/service-fabric-disaster-recovery/sfx-clustermap.png
