<properties
 pageTitle="Los nodos de clúster Autoescala HPC Pack | Microsoft Azure"
 description="Aumentar y reducir el número de nodos de cálculo de clúster de HPC Pack en Azure automáticamente"
 services="virtual-machines-windows"
 documentationCenter=""
 authors="dlepow"
 manager="timlt"
 editor=""
 tags="azure-service-management,hpc-pack"/>
<tags
ms.service="virtual-machines-windows"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-multiple"
 ms.workload="big-compute"
 ms.date="07/22/2016"
 ms.author="danlep"/>

# <a name="automatically-grow-and-shrink-the-hpc-pack-cluster-resources-in-azure-according-to-the-cluster-workload"></a>Automáticamente reducir y ampliar los recursos de clúster de HPC Pack en Azure según la carga de trabajo de clúster




Si implementa nodos de Azure "ráfaga" en el clúster HPC Pack o crear un clúster de HPC Pack en máquinas virtuales de Azure, desea una manera para aumentar o reducir la cantidad de recursos de cálculo Azure como nodos o núcleos según la carga de trabajo actual en el clúster automáticamente. Esto le permite utilizar de forma más eficaz los recursos de Azure y controlar los costos.
Para ello, configure la propiedad de clúster HPC Pack **AutoGrowShrink**. Como alternativa, ejecute la secuencia de comandos de PowerShell HPC **AzureAutoGrowShrink.ps1** que se instala con el paquete de HPC.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Además, actualmente solo automáticamente aumentar y reducir los nodos de cálculo de HPC Pack que ejecutan un sistema operativo Windows Server.

## <a name="set-the-autogrowshrink-cluster-property"></a>Establezca la propiedad de clúster AutoGrowShrink

### <a name="prerequisites"></a>Requisitos previos

* **HPC Pack 2012 R2 actualización 2 o posterior clúster** - puede ser el nodo cabezal implementado en modo local o en una máquina virtual de Azure. Consulte [configurar un clúster híbrido con HPC Pack](../cloud-services/cloud-services-setup-hybrid-hpcpack-cluster.md) para empezar con un nodo de cabeza local y nodos de Azure "ráfaga". Consulte el [script de implementación de HPC Pack IaaS](virtual-machines-windows-classic-hpcpack-cluster-powershell-script.md) implementar rápidamente un clúster HPC Pack en máquinas virtuales de Azure.


* **Para un clúster de un nodo principal en Azure** - si utiliza el script de implementación de HPC Pack IaaS para crear el clúster, habilite la propiedad de clúster **AutoGrowShrink** mediante la opción AutoGrowShrink en el archivo de configuración de clúster. Para obtener información detallada, consulte la documentación que acompaña a la [descarga secuencias de comandos](https://www.microsoft.com/download/details.aspx?id=44949). 

    También puede habilitar la propiedad de clúster **AutoGrowShrink** después de implementar el clúster mediante los comandos de PowerShell HPC descritos en la sección siguiente. Para preparar, siga primero los pasos siguientes:
    1. Configurar un certificado de administración de Azure en el nodo principal y en la suscripción de Azure. Una implementación de prueba puede usar el certificado autofirmado predeterminado Microsoft HPC Azure que HPC Pack se instala en el nodo principal y simplemente cargar certificado a su suscripción de Azure. Para pasos y opciones, consulte las [instrucciones de la biblioteca de TechNet](https://technet.microsoft.com/library/gg481759.aspx).
    2. Ejecute **regedit** en el nodo principal, vaya a HKLM\SOFTWARE\Micorsoft\HPC\IaasInfo y agregar un nuevo valor de cadena. Nombre del valor a "Huella digital" y el valor de datos para la huella digital del certificado en el paso 1.


### <a name="hpc-powershell-commands-to-set-the-autogrowshrink-property"></a>Comandos de PowerShell de HPC para establecer la propiedad AutoGrowShrink

Siguiente se muestra los comandos de PowerShell HPC para establecer **AutoGrowShrink** y ajustar su comportamiento con parámetros adicionales. Vea [los parámetros de AutoGrowShrink](#AutoGrowShrink-parameters) más adelante en este artículo para obtener la lista completa de configuraciones. 

Para ejecutar estos comandos, iniciar HPC PowerShell en el nodo central como administrador.

**Para habilitar la propiedad AutoGrowShrink**

    Set-HpcClusterProperty –EnableGrowShrink 1

**Para deshabilitar la propiedad AutoGrowShrink**

    Set-HpcClusterProperty –EnableGrowShrink 0

**Para cambiar el intervalo de aumentar en minutos**

    Set-HpcClusterProperty –GrowInterval <interval>

**Para cambiar el intervalo de reducir en minutos**

    Set-HpcClusterProperty –ShrinkInterval <interval>

**Para ver la configuración actual de AutoGrowShrink**

    Get-HpcClusterProperty –AutoGrowShrink

### <a name="autogrowshrink-parameters"></a>Parámetros de AutoGrowShrink

Los siguientes son AutoGrowShrink los parámetros que se pueden modificar mediante el comando **Conjunto HpcClusterProperty** .

* **EnableGrowShrink** - conmutador para habilitar o deshabilitar la propiedad **AutoGrowShrink** .
* **ParamSweepTasksPerCore** - número de tareas de barrido paramétrico para ganar un núcleo. El valor predeterminado es aumentar un núcleo por tarea. 
 
    >[AZURE.NOTE] HPC Pack QFE KB3134307 cambia **ParamSweepTasksPerCore** a **TasksPerResourceUnit**. Se basa en el tipo de recurso de trabajo y puede ser nodo, socket o core.
    
* **GrowThreshold** - umbral de tareas en cola para desencadenar crecimiento automático. El valor predeterminado es 1, lo que significa que si hay 1 o más tareas en el estado en cola, crecer automáticamente nodos.
* **GrowInterval** - intervalo en minutos para desencadenar crecimiento automático. El intervalo predeterminado es de 5 minutos.
* **ShrinkInterval** - intervalo en minutos para desencadenar la reducción automática. El intervalo predeterminado es de 5 minutos. |
* **ShrinkIdleTimes** - número de comprobaciones continuas para reducir para indicar los nodos están inactivas. El valor predeterminado es 3 veces. Por ejemplo, si la **ShrinkInterval** es 5 minutos, HPC Pack comprueba si el nodo está inactivo cada 5 minutos. Si los nodos están en el estado inactivo después de 3 continuo comprueba (15 minutos), HPC Pack se reducirá a ese nodo.
* **ExtraNodesGrowRatio** - porcentaje adicional de nodos creciendo para las tareas de la interfaz de paso de mensajes (MPI). El valor predeterminado es 1, lo que significa que el paquete de HPC crece nodos 1% para trabajos MPI. 
* **GrowByMin** - cambiar para indicar si la directiva de crecimiento automático está basada en los recursos mínimos necesarios para el trabajo. El valor predeterminado es false, lo que significa que el paquete de HPC crece nodos para trabajos en función de los recursos máximos necesarios para las tareas.
* **SoaJobGrowThreshold** - umbral de las solicitudes entrantes de inicio de autoridad para desencadenar la automática aumentar el proceso. El valor predeterminado es 50000.  
    
    >[AZURE.NOTE] Este parámetro es compatible a partir de HPC Pack 2012 R2 actualización 3.
    
* **SoaRequestsPerCore** -número de inicio de autoridad entrante solicitudes para ganar un núcleo. El valor predeterminado es 20.000.  

    >[AZURE.NOTE] Este parámetro es compatible a partir de HPC Pack 2012 R2 actualización 3.

### <a name="mpi-example"></a>Ejemplo MPI

De forma predeterminada el paquete de HPC crece 1% nodos adicionales para trabajos MPI (**ExtraNodesGrowRatio** se establece en 1). El motivo es que MPI puede requerir varios nodos y solo se puede ejecutar el trabajo cuando todos los nodos listos. Cuando se inicia nodos en Azure, en ocasiones un nodo necesita más tiempo para iniciar que otros, causando otros nodos debe estar inactivo mientras se espera ese nodo preparar. Crecimiento nodos adicionales, HPC Pack reduce este tiempo de espera de recursos y potencialmente guarda los costos. Para aumentar el porcentaje de nodos adicionales para trabajos MPI (por ejemplo, al 10%), ejecutar un comando similar a

    Set-HpcClusterProperty -ExtraNodesGrowRatio 10

### <a name="soa-example"></a>Ejemplo de inicio de autoridad

De forma predeterminada, **SoaJobGrowThreshold** se establece en 50000 y **SoaRequestsPerCore** se establece en 200000. Si envía un trabajo de inicio de autoridad a 70000 solicitudes, habrá una tarea en cola y las solicitudes entrantes son 70000. En este caso crece HPC Pack 1 principales para la tarea en cola y para las solicitudes entrantes, crecerá (70000 50000) / núcleo 20000 = 1, por lo que en total crecerá 2 núcleos para este trabajo de inicio de autoridad.

## <a name="run-the-azureautogrowshrinkps1-script"></a>Ejecutar la secuencia de comandos de AzureAutoGrowShrink.ps1

### <a name="prerequisites"></a>Requisitos previos

* **HPC Pack 2012 R2 actualización 1 o posterior clúster** - la secuencia de comandos de **AzureAutoGrowShrink.ps1** se instala en la carpeta % CCP_HOME % Papelera. Puede ser el nodo cabezal implementado en modo local o en una máquina virtual de Azure. Consulte [configurar un clúster híbrido con HPC Pack](../cloud-services/cloud-services-setup-hybrid-hpcpack-cluster.md) para empezar con un nodo de cabeza local y nodos de Azure "ráfaga". Consulte el [script de implementación de HPC Pack IaaS](virtual-machines-windows-classic-hpcpack-cluster-powershell-script.md) implementar rápidamente un clúster HPC Pack en máquinas virtuales de Azure o usar una [plantilla de Azure tutorial](https://azure.microsoft.com/documentation/templates/create-hpc-cluster/).

* **Azure PowerShell 0.8.12** - actualmente la secuencia de comandos depende de esta versión específica de PowerShell de Azure. Si está ejecutando una versión posterior en el nodo principal, debe cambiar Azure PowerShell a la [versión 0.8.12](http://az412849.vo.msecnd.net/downloads03/azure-powershell.0.8.12.msi) para ejecutar el script. 

* **Para un clúster de nodos de ráfaga Azure** - ejecutar la secuencia de comandos en un equipo cliente donde está instalado el paquete de HPC o en el nodo principal. Si se ejecuta en un equipo cliente, asegúrese de que ha establecido la variable $env: CCP_SCHEDULER correctamente para que apunten al nodo principal. Los nodos de Azure "ráfaga" deben haberse agregados al clúster, pero se encuentren en el estado implementado en no.


* **Para un clúster implementado en máquinas virtuales de Azure** - ejecutar la secuencia de comandos en el nodo principal VM, porque depende del **HpcIaaSNode.ps1 de inicio** y **Detención HpcIaaSNode.ps1** secuencias de comandos se instalan. Las secuencias de comandos además requieren un certificado de administración de Azure o publicar el archivo de configuración (consulte [Administrar nodos de cálculo en un clúster de HPC Pack en Azure](virtual-machines-windows-classic-hpcpack-cluster-node-manage.md)). Asegúrese de que todo el nodo de cálculo VM necesita ya se han agregado al clúster. Que estén en el estado detenido.

### <a name="syntax"></a>Sintaxis

```
AzureAutoGrowShrink.ps1
[[-NodeTemplates] <String[]>] [[-JobTemplates] <String[]>] [[-NodeType] <String>]
[[-NumOfQueuedJobsPerNodeToGrow] <Int32>]
[[-NumOfQueuedJobsToGrowThreshold] <Int32>]
[[-NumOfActiveQueuedTasksPerNodeToGrow] <Int32>]
[[-NumOfActiveQueuedTasksToGrowThreshold] <Int32>]
[[-NumOfInitialNodesToGrow] <Int32>] [[-GrowCheckIntervalMins] <Int32>]
[[-ShrinkCheckIntervalMins] <Int32>] [[-ShrinkCheckIdleTimes] <Int32>]
[-UseLastConfigurations] [[-ArgFile] <String>] [[-LogFilePrefix] <String>]
[<CommonParameters>]

```
### <a name="parameters"></a>Parámetros

 * **NodeTemplates** - los nombres de las plantillas de nodo para definir el ámbito para los nodos de reducir y ampliar. Si no especifica (el valor predeterminado es @()), todos los nodos en el grupo de nodo **AzureNodes** están en el ámbito cuando **NodeType** tiene un valor de AzureNodes y todos los nodos en el grupo de nodo **ComputeNodes** están en el ámbito cuando **NodeType** tiene un valor de ComputeNodes.

 * **JobTemplates** - nombres de las plantillas de trabajo para definir el ámbito para los nodos de crecimiento.

 * **NodeType** - el tipo de nodo para reducir y ampliar. Los valores admitidos son:

     * **AzureNodes** : para los nodos de Azure PaaS (ráfaga) en un local o clúster de Azure IaaS.

     * **ComputeNodes** - para nodo de cálculo máquinas virtuales solo en un clúster de Azure IaaS.

* **NumOfQueuedJobsPerNodeToGrow** - número de trabajos en cola que se requieren para aumentar un nodo.

* **NumOfQueuedJobsToGrowThreshold** - el número de umbral de trabajos en cola para iniciar el proceso de crecimiento.

* **NumOfActiveQueuedTasksPerNodeToGrow** - el número de activos en cola las tareas necesarias para aumentar un nodo. Si se especifica **NumOfQueuedJobsPerNodeToGrow** con un valor mayor que 0, este parámetro se omite.

* **NumOfActiveQueuedTasksToGrowThreshold** - el número límite de tareas en cola activas para iniciar el proceso de crecimiento.

* **NumOfInitialNodesToGrow** - número mínimo inicial de nodos crecer si todos los nodos de ámbito **Implementado no** o **detenido (Deallocated)**.

* **GrowCheckIntervalMins** - el intervalo en minutos entre las comprobaciones de crecimiento.

* **ShrinkCheckIntervalMins** - el intervalo en minutos entre las comprobaciones de reducir.

* **ShrinkCheckIdleTimes** - el número de continuo reducir comprobaciones (separadas por **ShrinkCheckIntervalMins**) para indicar los nodos están inactivos.

* **UseLastConfigurations** - las configuraciones anteriores guardadas en el archivo de los argumentos.

* **ArgFile**- el nombre del archivo de argumento que se usó para guardar y actualizar las configuraciones para ejecutar la secuencia de comandos.

* **LogFilePrefix** - el prefijo del nombre del archivo de registro. Puede especificar una ruta de acceso. De forma predeterminada el registro se escribe en el directorio de trabajo actual.

### <a name="example-1"></a>Ejemplo 1

En el ejemplo siguiente se configura los nodos de ráfaga Azure implementados con la plantilla de AzureNode predeterminado para reducir y ampliar automáticamente. Si todos los nodos son inicialmente en el estado **Implementado en no** , se inician al menos 3 nodos. Si el número de trabajos en cola supera los 8, la secuencia de comandos inicia nodos hasta que su número supera la proporción de trabajos en cola de **NumOfQueuedJobsPerNodeToGrow**. Si se encuentra un nodo esté inactivo en 3 veces inactivos consecutivos, se detiene.

```
.\AzureAutoGrowShrink.ps1 -NodeTemplates @('Default AzureNode
 Template') -NodeType AzureNodes -NumOfQueuedJobsPerNodeToGrow 5
 -NumOfQueuedJobsToGrowThreshold 8 -NumOfInitialNodesToGrow 3
 -GrowCheckIntervalMins 1 -ShrinkCheckIntervalMins 1 -ShrinkCheckIdleTimes 3
```

### <a name="example-2"></a>Ejemplo 2

En el ejemplo siguiente se configura el nodo de cálculo Azure VM implementados con la plantilla de ComputeNode predeterminado para reducir y ampliar automáticamente.
Las tareas que se configura la plantilla de trabajo predeterminada definen el ámbito de la carga de trabajo en el clúster. Si inicialmente se detienen todos los nodos, se inician al menos 5 nodos. Si el número de tareas en cola activas supera 15, la secuencia de comandos inicia nodos hasta que su número supera la relación de tareas en cola activas a **NumOfActiveQueuedTasksPerNodeToGrow**. Si se encuentra un nodo esté inactivo en 10 veces inactivos consecutivos, se detiene.

```
.\AzureAutoGrowShrink.ps1 -NodeTemplates 'Default ComputeNode Template' -JobTemplates 'Default' -NodeType ComputeNodes -NumOfActiveQueuedTasksPerNodeToGrow 10 -NumOfActiveQueuedTasksToGrowThreshold 15 -NumOfInitialNodesToGrow 5 -GrowCheckIntervalMins 1 -ShrinkCheckIntervalMins 1 -ShrinkCheckIdleTimes 10 -ArgFile 'IaaSVMComputeNodes_Arg.xml' -LogFilePrefix 'IaaSVMComputeNodes_log'
```
