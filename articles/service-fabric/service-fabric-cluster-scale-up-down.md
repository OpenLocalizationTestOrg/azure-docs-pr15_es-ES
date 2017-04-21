<properties
   pageTitle="Ajustar el tamaño de un clúster de servicio tela o alejar | Microsoft Azure"
   description="Ajustar el tamaño de un clúster de servicio tela o alejar para que coincida con demanda configurando reglas de escala automático para cada conjunto de escala de tipo/VM de nodo. Agregar o quitar nodos a un clúster de tela de servicio"
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


# <a name="scale-a-service-fabric-cluster-in-or-out-using-auto-scale-rules"></a>Ajustar el tamaño de un clúster de servicio tela o desprotege mediante reglas de escala automático

Conjuntos de escala de máquina virtual son un recurso de Azure cálculo que puede usar para implementar y administrar una colección de máquinas virtuales como un conjunto. Todos los tipos de nodo se definen en un clúster de servicio tela está configurado como un conjunto de escala VM independiente. Cada tipo de nodo puede ampliarse en o fuera de forma independiente, tener diferentes conjuntos de puertos abiertos y puede tener métricas de capacidad diferente. Más información sobre él en el documento de [servicio tela nodetypes](service-fabric-cluster-nodetypes.md) . Puesto que la estructura de servicio realizados tipos de nodos en el clúster de escala VM se establece en el servidor, debe configurar reglas de escala automático para cada conjunto de escala de tipo/VM de nodo.

>[AZURE.NOTE] La suscripción debe tener suficiente núcleos para agregar la nuevas VM que componen este clúster. Actualmente no hay ninguna validación del modelo, por lo que se obtiene un error de tiempo de implementación, si se encuentra cualquiera de los límites de cuota.

## <a name="choose-the-node-typevm-scale-set-to-scale"></a>Elegir la escala de tipo/VM nodo establecer escalar

Actualmente, no son capaces de especificar las reglas de escala automático de conjuntos de escala VM con el portal, nos usar Azure PowerShell (1.0 +) para los tipos de nodo de la lista y, a continuación, agregar reglas de ajustar a ellos.

Para obtener la lista de conjunto de escala VM que componen el clúster, ejecute los cmdlets siguientes:

```powershell
Get-AzureRmResource -ResourceGroupName <RGname> -ResourceType Microsoft.Compute/VirtualMachineScaleSets

Get-AzureRmVmss -ResourceGroupName <RGname> -VMScaleSetName <VM Scale Set name>
```

## <a name="set-auto-scale-rules-for-the-node-typevm-scale-set"></a>Establecer reglas de escala automática para el conjunto de escala de tipo/VM de nodo

Si el clúster tiene varios tipos de nodo, repita que esto para cada escala de nodo tipos/VM establece que desee escalar (o). Tenga en cuenta el número de nodos que debe tener antes de configurar la escala automática. El número mínimo de nodos que debe tener para el tipo de nodo principal se basa en el nivel de confiabilidad que haya elegido. Más información sobre [niveles de confiabilidad](service-fabric-cluster-capacity.md).

>[AZURE.NOTE]  Ajuste de escala hacia abajo el nodo principal escriba a menor que el número mínimo, asegúrese que el clúster estable o Traer hacia abajo. Esto puede provocar la pérdida de datos para las aplicaciones y los servicios del sistema.

Actualmente la característica de escala automática no depende de la carga de las aplicaciones se pueden informar al servicio tela. Así que en este momento Autoescala que recibe puramente es controlado por el rendimiento contadores que se emiten por cada una de la máquina virtual escalan instancias de conjunto.  

Siga estas instrucciones [para configurar la escala automática para cada conjunto de escala de la máquina virtual](../virtual-machine-scale-sets/virtual-machine-scale-sets-autoscale-overview.md).

>[AZURE.NOTE] En una escala hacia abajo el escenario, a menos que el tipo de nodo tiene un nivel de duración de oro o plata debe llamar el [cmdlet quitar ServiceFabricNodeState](https://msdn.microsoft.com/library/azure/mt125993.aspx) con el nombre de nodo correspondiente.

## <a name="manually-add-vms-to-a-node-typevm-scale-set"></a>Agregar manualmente VM a un conjunto de escala de tipo/VM de nodo

Siga las instrucciones de ejemplo o de la [Galería de plantillas de inicio rápido](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-scale-existing) para cambiar el número de máquinas virtuales en cada Nodetype. 

>[AZURE.NOTE] Adición de VM tarda, por lo que no espera las adiciones sea instantáneo. Así que se va a agregar capacidad bien en hora, para permitir más de 10 minutos antes de que la capacidad VM está disponible para las réplicas o que se colocan instancias de servicio.

## <a name="manually-remove-vms-from-the-primary-node-typevm-scale-set"></a>Quitar manualmente máquinas virtuales del conjunto de escala de tipo/VM de nodo principal

>[AZURE.NOTE] El servicio de sistema de tela se ejecuta en el tipo de nodo principal en el clúster. Por lo que nunca deben apagar o reducir el número de instancias en que los tipos de nodo menor que el nivel de confiabilidad de qué garantiza. Consulte [los detalles en los niveles de fiabilidad aquí](service-fabric-cluster-capacity.md). 

Debe ejecutar la siguientes pasos una máquina virtual instancia a la vez. Esto permite que los servicios del sistema (y los servicios con estado) se cierra correctamente en la instancia de máquina virtual que está quitando y nuevas réplicas creadas en otros nodos.

1. Ejecutar [Deshabilitar ServiceFabricNode](https://msdn.microsoft.com/library/mt125852.aspx) con intención 'RemoveNode' para deshabilitar el nodo que se va a quitar (la instancia más alta en ese tipo de nodo).

2. Ejecutar [Get-ServiceFabricNode](https://msdn.microsoft.com/library/mt125856.aspx) para asegurarse de que el nodo ya ha hecho pasado deshabilitado. Si no es así, espere hasta que el nodo está deshabilitado. No puede apresurar este paso.

2. Siga las instrucciones de ejemplo o de la [Galería de plantillas de inicio rápido](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-scale-existing) para cambiar el número de máquinas virtuales de uno en ese Nodetype. La instancia quitada es la máquina virtual más alto. 

3. Repita los pasos 1 a 3, según sea necesario, pero nunca escalar hacia abajo el número de instancias de los tipos de nodo principal menor que lo que garantiza que el nivel de confiabilidad. Consulte [los detalles en los niveles de fiabilidad aquí](service-fabric-cluster-capacity.md). 

## <a name="manually-remove-vms-from-the-non-primary-node-typevm-scale-set"></a>Quitar manualmente máquinas virtuales del conjunto de escala de tipo/VM de nodo no principal

>[AZURE.NOTE] Para un servicio de estado, es necesario un determinado número de nodos se siempre al mantener la disponibilidad y conservar el estado del servicio. Como mínimo, necesitará el número de nodos igual que el recuento de conjunto de réplica de destino del servicio de partición. 

Necesita la ejecución de la siguiente instancia de pasos una máquina virtual a la vez. Esto permite que los servicios del sistema (y los servicios con estado) se cierra correctamente en la instancia de máquina virtual que se va a quitar y réplicas nuevas creadas dónde más.

1. Ejecutar [Deshabilitar ServiceFabricNode](https://msdn.microsoft.com/library/mt125852.aspx) con intención 'RemoveNode' para deshabilitar el nodo que se va a quitar (la instancia más alta en ese tipo de nodo).

2. Ejecutar [Get-ServiceFabricNode](https://msdn.microsoft.com/library/mt125856.aspx) para asegurarse de que el nodo ya ha hecho pasado deshabilitado. Si no espera hasta que el nodo está deshabilitado. No puede apresurar este paso.

2. Siga las instrucciones de ejemplo o de la [Galería de plantillas de inicio rápido](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-scale-existing) para cambiar el número de máquinas virtuales de uno en ese Nodetype. Esto quitará la instancia VM más alta. 

3. Repita los pasos 1 a 3, según sea necesario, pero nunca escalar hacia abajo el número de instancias de los tipos de nodo principal menor que lo que garantiza que el nivel de confiabilidad. Consulte [los detalles en los niveles de fiabilidad aquí](service-fabric-cluster-capacity.md).

## <a name="behaviors-you-may-observe-in-service-fabric-explorer"></a>Comportamientos que se pueden observar en el Explorador de tela de servicio

Al escalar un clúster de servicio en el Explorador de tela reflejará el número de nodos (escala VM establece instancias) que forman parte del clúster.  Sin embargo, al escalar un clúster hacia abajo muestra la instancia de nodo quitado/VM mostrada en mal estado a menos que llame a [Quitar ServiceFabricNodeState cmd](https://msdn.microsoft.com/library/mt125993.aspx) con el nombre de nodo correspondiente.   

Aquí es la explicación de este comportamiento.

Los nodos en el Explorador de tela de servicio de son un reflejo de los servicios de sistema del servicio tela (FM específicamente) conozca el número de nodos en el clúster tenía/tiene. Al ajustar la escala VM establecida, se ha eliminado la máquina virtual pero el servicio del sistema de FM todavía cree que el nodo (que se ha asignado a la máquina virtual que se ha eliminado) se que vuelva. Así que servicio tela Explorer continúa mostrando ese nodo (aunque el estado de mantenimiento puede ser error o desconocido).

Para asegurarse de que un nodo se quita cuando se quita una máquina virtual, tiene dos opciones:

1) Elija un nivel de duración de oro o plata (disponible próximamente) para los tipos de nodo en el clúster, que proporciona la integración de infraestructura. Que, a continuación, quitará automáticamente los nodos de nuestro estado del sistema servicios (FM) al escalar hacia abajo.
Consulte [los detalles sobre los niveles de duración aquí](service-fabric-cluster-capacity.md)

2) Una vez que se ha reducido en la instancia de VM, debe llamar el [cmdlet quitar ServiceFabricNodeState](https://msdn.microsoft.com/library/mt125993.aspx).

>[AZURE.NOTE] Servicio tela clústeres requieren un número determinado de nodos pueden empezar en todo momento para mantener la disponibilidad y mantener el estado - se denomina "mantenimiento quórum". Por lo tanto, no es seguro normalmente para cerrar todos los equipos del clúster a menos que primero ha realizado una [copia de seguridad completa de su estado](service-fabric-reliable-services-backup-restore.md).

## <a name="next-steps"></a>Pasos siguientes
Lea las siguientes acciones para también obtenga más información sobre la planeación de la capacidad de clúster, actualizar un clúster y partición de servicios:

- [Planear la capacidad de clúster](service-fabric-cluster-capacity.md)
- [Actualizaciones de clúster](service-fabric-cluster-upgrade.md)
- [Servicios de estado de partición para escala máxima](service-fabric-concepts-partitioning.md)

<!--Image references-->
[BrowseServiceFabricClusterResource]: ./media/service-fabric-cluster-scale-up-down/BrowseServiceFabricClusterResource.png
[ClusterResources]: ./media/service-fabric-cluster-scale-up-down/ClusterResources.png
