<properties
    pageTitle="Implementar una aplicación en conjuntos de escala de máquina virtual | Microsoft Azure"
    description="Implementar una aplicación en conjuntos de escala de máquina virtual"
    services="virtual-machine-scale-sets"
    documentationCenter=""
    authors="gbowerman"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machine-scale-sets"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/13/2016"
    ms.author="guybo"/>


# <a name="upgrade-a-virtual-machine-scale-set"></a>Actualizar un conjunto de escala de máquina virtual

Este artículo describe cómo restaurar una actualización de sistema operativo a una escala de Azure máquina virtual establecer sin ningún tiempo de inactividad. En este contexto, una actualización de OS implica cambiar la versión o la SKU del sistema operativo o cambiar el URI de una imagen personalizada. Actualizar sin significa que el tiempo de inactividad actualizando máquinas virtuales uno en uno o en grupos (por ejemplo, un dominio de error a la vez) en lugar de a la vez. De esta forma, puede mantener ejecutando cualquier máquinas virtuales que no se actualizan.

Para evitar la ambigüedad, vamos a distinguir los tres tipos de actualización de sistema operativo que desea realizar:

- Cambiar la versión o SKU de una imagen de la plataforma. Por ejemplo, cambiar Ubuntu versión 14.04.2-LTS 14.04.201506100 14.04.201507060, o cambiando el SKU de última o 15.10 de Ubuntu a 16.04.0-LTS/latest. En este artículo se trata este escenario.

- Cambiar el URI que señala a una nueva versión de una imagen personalizada integrado (**Propiedades** > **virtualMachineProfile** > **storageProfile** > **osDisk** > **imagen** > **uri**). En este artículo se trata este escenario.

- Revisión del sistema operativo desde dentro de una máquina virtual (por ejemplo de instalar una revisión de seguridad y ejecutar Windows Update). Este escenario es compatible, pero no se tratan en este artículo.

Las dos primeras opciones son compatibles requisitos mencionados en este artículo. Deberá crear una nueva escala establecida para ejecutar la tercera opción.

Aquí no se tratan los conjuntos de escala de máquina virtual que se implementan como parte de un clúster de [Azure servicio tela](https://azure.microsoft.com/services/service-fabric/) .

La secuencia básica para cambiar el sistema operativo versión/SKU de una imagen de la plataforma o el URI de una imagen personalizada tiene el siguiente aspecto:

1. Obtener el modelo de conjunto de escala de máquina virtual.

2. Cambiar la versión, SKU o URI de valor en el modelo.

3. Actualizar el modelo.

4. Hacer una llamada de *manualUpgrade* en los equipos virtuales en el conjunto de escala. Este paso solo es relevante si *upgradePolicy* se establece en **Manual** en el conjunto de escala. Si se establece en **automático**, todas las máquinas virtuales se actualizan a la vez, por lo que el tiempo de inactividad.


Con esta información de fondo en cuenta, vamos a ver cómo puede actualizar la versión de una escala establecida en PowerShell y con la API de REST. Estos ejemplos cubren el caso de una imagen de la plataforma, pero en este artículo se proporciona información suficiente para adaptar este proceso a una imagen personalizada.

## <a name="powershell"></a>PowerShell##

Este ejemplo actualiza una escala de máquina virtual de Windows establecida para la nueva versión 4.0.20160229. Después de actualizar el modelo, lo hace una actualización de una instancia de máquina virtual a la vez.

```powershell
$rgname = "myrg"
$vmssname = "myvmss"
$newversion = "4.0.20160229"
$instanceid = "1"

# get the VMSS model
$vmss = Get-AzureRmVmss -ResourceGroupName $rgname -VMScaleSetName $vmssname

# set the new version in the model data
$vmss.virtualMachineProfile.storageProfile.imageReference.version = $newversion

# update the virtual machine scale set model
Update-AzureRmVmss -ResourceGroupName $rgname -Name $vmssname -VirtualMachineScaleSet $vmss

# now start updating instances
Update-AzureRmVmssInstance -ResourceGroupName $rgname -VMScaleSetName $vmssname -InstanceId $instanceId
```

Si está actualizando el URI de una imagen personalizada en lugar de cambiar de una versión de plataforma de imagen, reemplace la línea "establecer la nueva versión" con algo similar al siguiente:

```powershell
# set the new version in the model data
$vmss.virtualMachineProfile.storageProfile.osDisk.image.uri= $newURI
```


## <a name="the-rest-api"></a>La API de REST

Estos son algunos ejemplos de Python que usan la API de REST de Azure para dar a conocer una actualización de versión del sistema operativo. Ambas usan la biblioteca de ligero [azurerm](https://pypi.python.org/pypi/azurerm) de funciones de API de REST de Azure contenedor hacer GET en el modelo de conjunto de escala, seguido de una ubicación con un modelo actualizado. También buscar en vistas de instancias de máquina virtual para identificar los equipos virtuales por actualizar dominio.

### <a name="vmssupgrade"></a>Vmssupgrade

 [Vmssupgrade](https://github.com/gbowerman/vmsstools) es una secuencia de comandos de Python que se utiliza para dar a conocer una actualización del SO a una escala de máquina virtual ejecución establece un dominio de actualización en un momento.

![Secuencia de comandos de Vmssupgrade para elegir máquinas virtuales o un dominio de actualización](./media/virtual-machine-scale-sets-upgrade-scale-set/vmssupgrade-screenshot.png)

Esta secuencia de comandos le permite elegir equipos virtuales específicos para actualizar o especifique un dominio de actualización. Es compatible con cambiar una versión de plataforma de imagen o cambiar el URI de una imagen personalizada.

### <a name="vmsseditor"></a>Vmsseditor

[Vmsseditor](https://github.com/gbowerman/vmssdashboard) es un editor general para los conjuntos de escala de máquina virtual que muestra el estado de la máquina virtual como un heatmap donde una fila representa un dominio de actualización. Entre otras cosas, puede actualizar el modelo de un conjunto de escala con una nueva versión, SKU o URI personalizado de la imagen y, a continuación, elija dominios de error de actualización. Cuando lo haga, todas las máquinas virtuales de ese dominio de actualización se actualizan en el nuevo modelo. Como alternativa, puede realizar una actualización sucesiva basada en el tamaño del lote de su elección.  

La siguiente captura de pantalla muestra un modelo de una escala establecida para Ubuntu 14.04-2LTS versión 14.04.201507060. Se agregaron muchas más opciones para esta herramienta desde que se realizó esta captura de pantalla.

![Modelo de Vmsseditor de una escala establecida para Ubuntu 14.04-2LTS](./media/virtual-machine-scale-sets-upgrade-scale-set/vmssEditor1.png)

Después de hacer clic en **Actualizar** y, a continuación, **Obtener detalles**, máquinas virtuales de Ud. 0 empezar a actualizar.

![Vmsseditor que muestra una actualización en curso](./media/virtual-machine-scale-sets-upgrade-scale-set/vmssEditor2.png)
