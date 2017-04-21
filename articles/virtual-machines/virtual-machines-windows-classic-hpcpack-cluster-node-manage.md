<properties
 pageTitle="Administrar los nodos de cálculo de clúster de HPC Pack | Microsoft Azure"
 description="Obtenga más información sobre herramientas de secuencia de comandos de PowerShell para agregar, quitar, iniciar y detener los nodos de cálculo de clúster de HPC Pack en Azure"
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

# <a name="manage-the-number-and-availability-of-compute-nodes-in-an-hpc-pack-cluster-in-azure"></a>Administrar el número y la disponibilidad de los nodos de cálculo en un clúster de HPC Pack en Azure

Si ha creado un clúster de HPC Pack en máquinas virtuales de Azure, es recomendable formas de agregar, quitar, iniciar (aprovisionar) o dejar el nodo (deprovision) en un número de cálculo máquinas virtuales en el clúster fácilmente. Para realizar estas tareas, ejecute los scripts de PowerShell de Azure que están instalados en el nodo principal VM. Estas secuencias de comandos ayudan a controlar el número y la disponibilidad de los recursos de clúster de HPC Pack para controlar los costos.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]


## <a name="prerequisites"></a>Requisitos previos

* **Clúster HPC Pack en máquinas virtuales de Azure** - crear un clúster de HPC Pack en el modelo de implementación clásica con al menos HPC Pack 2012 R2 actualización 1. Por ejemplo, puede automatizar la implementación mediante la imagen de HPC Pack VM actual en una secuencia de comandos de PowerShell de Azure y de Azure Marketplace. Para obtener información y los requisitos previos, consulte [crear un clúster de HPC con el script de implementación de HPC Pack IaaS](virtual-machines-windows-classic-hpcpack-cluster-powershell-script.md).

    Después de la implementación, busque el nodo secuencias de comandos de administración en la carpeta % CCP\_carpeta de bin % de inicio en el nodo principal. Cada una de las secuencias de comandos debe ejecutar como administrador.

* **Certificado de archivo o administración de la configuración de publicación de azure** - debe realizar uno de estos procedimientos en el nodo principal:

    * **Importar la Azure publicar el archivo de configuración**. Para ello, ejecute los siguientes cmdlets de PowerShell de Azure en el nodo principal:

    ```
    Get-AzurePublishSettingsFile

    Import-AzurePublishSettingsFile –PublishSettingsFile <publish settings file>
    ```

    * **Configurar el certificado de administración de Azure en el nodo principal**. Si tiene el archivo .cer, importar en el almacén de certificados CurrentUser\My y luego ejecute el siguiente cmdlet de PowerShell de Azure en su entorno de Azure (AzureCloud o AzureChinaCloud):

    ```
    Set-AzureSubscription -SubscriptionName <Sub Name> -SubscriptionId <Sub ID> -Certificate (Get-Item Cert:\CurrentUser\My\<Cert Thrumbprint>) -Environment <AzureCloud | AzureChinaCloud>
    ```

## <a name="add-compute-node-vms"></a>Agregar máquinas virtuales de nodo de cálculo

Agregar nodos de cálculo con la secuencia de comandos de **HpcIaaSNode.ps1 agregar** .

### <a name="syntax"></a>Sintaxis
```
Add-HPCIaaSNode.ps1 [-ServiceName] <String> [-ImageName] <String>
 [-Quantity] <Int32> [-InstanceSize] <String> [-DomainUserName] <String> [[-DomainUserPassword] <String>]
 [[-NodeNameSeries] <String>] [<CommonParameters>]

```
### <a name="parameters"></a>Parámetros

* **ServiceName** - nombre de la nube de servicio ese nuevo nodo de cálculo máquinas virtuales se agregará a.

* **ImageName** - nombre de la imagen de Azure VM, que puede obtenerse a través del portal clásica Azure o Azure PowerShell cmdlet **Get-AzureVMImage**. La imagen debe cumplir los requisitos siguientes:

    1. Debe estar instalado un sistema operativo Windows.

    2. HPC Pack debe estar instalado en el rol de nodo de cálculo.

    3. La imagen debe tener una imagen en la categoría de usuario, no es una imagen de Azure VM pública privada.

* **Cantidad** - número de nodo de cálculo VM agregarse.

* **InstanceSize** - tamaño de la máquinas virtuales de nodo de cálculo.

* **Dominionombre_de_usuario** - nombre de usuario de dominio, que se utilizará para unir las nuevas máquinas virtuales al dominio.

* **DomainUserPassword** - contraseña de usuario del dominio.

* **NodeNameSeries** (opcional) - modelo de nomenclatura para los nodos de cálculo. El formato debe ser &lt; *Root\_nombre*&gt;&lt;*iniciar\_número*&gt;%. Por ejemplo, MyCN % 10% significa una serie de los nombres de nodo de cálculo a partir de MyCN11. Si no se especifica, la secuencia de comandos usa el nodo configurado series en el clúster HPC de nomenclatura.

### <a name="example"></a>Ejemplo

En el ejemplo siguiente se agrega el nodo de cálculo de gran tamaño 20 máquinas virtuales en la nube servicio *hpcservice1*, en función de la imagen de máquina virtual *hpccnimage1*.

```
Add-HPCIaaSNode.ps1 –ServiceName hpcservice1 –ImageName hpccniamge1
–Quantity 20 –InstanceSize Large –DomainUserName <username>
-DomainUserPassword <password>
```


## <a name="remove-compute-node-vms"></a>Quitar máquinas virtuales de nodo de cálculo

Quitar nodos de cálculo con la secuencia de comandos de **Quitar HpcIaaSNode.ps1** .

### <a name="syntax"></a>Sintaxis

```
Remove-HPCIaaSNode.ps1 -Name <String[]> [-DeleteVHD] [-Force] [-WhatIf] [-Confirm] [<CommonParameters>]

Remove-HPCIaaSNode.ps1 -Node <Object> [-DeleteVHD] [-Force] [-Confirm] [<CommonParameters>]
```

### <a name="parameters"></a>Parámetros

* **Nombre** : nombres de los nodos de clúster que se eliminen. Se admite caracteres comodín. El nombre del conjunto de parámetro es el nombre. No puede especificar el **nombre** y el **nodo** parámetros.

* **Nodo** - HpcNode el objeto para los nodos que se eliminen, que puede obtenerse mediante el cmdlet de PowerShell HPC [Get-HpcNode](https://technet.microsoft.com/library/dn887927.aspx). El nombre del conjunto de parámetro es nodo. No puede especificar el **nombre** y el **nodo** parámetros.

* **DeleteVHD** (opcional) - configuración para eliminar los discos asociados para las máquinas virtuales que se quitan.

* **Forzar** (opcional) - configuración Forzar nodos HPC sin conexión antes de quitarlos.

* **Confirmar** (opcional): solicitar confirmación antes de ejecutar el comando.

* **WhatIf** - la configuración para describir lo que ocurre si se ejecuta el comando sin ejecutar el comando.

### <a name="example"></a>Ejemplo

En el ejemplo siguiente se fuerza sin conexión nodos cuyos nombres empiecen *HPCNode-CN -* y les quita los nodos y sus discos asociados.

```
Remove-HPCIaaSNode.ps1 –Name HPCNodeCN-* –DeleteVHD -Force
```

## <a name="start-compute-node-vms"></a>Iniciar máquinas virtuales de nodo de cálculo

Iniciar nodos de cálculo con la secuencia de comandos de **HpcIaaSNode.ps1 de inicio** .

### <a name="syntax"></a>Sintaxis

```
Start-HPCIaaSNode.ps1 -Name <String[]> [<CommonParameters>]

Start-HPCIaaSNode.ps1 -Node <Object> [<CommonParameters>]
```
### <a name="parameters"></a>Parámetros

* **Nombre** : los nombres de los nodos de clúster para que se inicie. Se admite caracteres comodín. El nombre del conjunto de parámetro es el nombre. No puede especificar el **nombre** y el **nodo** parámetros.

* **Nodo**- HpcNode el objeto para los nodos para que se inicie, que puede obtenerse mediante el cmdlet de PowerShell HPC [Get-HpcNode](https://technet.microsoft.com/library/dn887927.aspx). El nombre del conjunto de parámetro es nodo. No puede especificar el **nombre** y el **nodo** parámetros.

### <a name="example"></a>Ejemplo

En el ejemplo siguiente se inicia nodos cuyos nombres empiecen *HPCNode-CN -*.

```
Start-HPCIaaSNode.ps1 –Name HPCNodeCN-*
```

## <a name="stop-compute-node-vms"></a>Detener nodo de cálculo VM

Dejar de nodos de cálculo con la secuencia de comandos de **HpcIaaSNode.ps1 detener** .

### <a name="syntax"></a>Sintaxis

```
Stop-HPCIaaSNode.ps1 -Name <String[]> [-Force] [<CommonParameters>]

Stop-HPCIaaSNode.ps1 -Node <Object> [-Force] [<CommonParameters>]
```

### <a name="parameters"></a>Parámetros


* **Nombre**: los nombres de los nodos de clúster para detenerla. Se admite caracteres comodín. El nombre del conjunto de parámetro es el nombre. No puede especificar el **nombre** y el **nodo** parámetros.

* **Nodo** - HpcNode el objeto para los nodos de detenerla, que puede obtenerse mediante el cmdlet de PowerShell HPC [Get-HpcNode](https://technet.microsoft.com/library/dn887927.aspx). El nombre del conjunto de parámetro es nodo. No puede especificar el **nombre** y el **nodo** parámetros.

* **Forzar** (opcional) - configuración Forzar nodos HPC sin conexión antes de detener ellos.

### <a name="example"></a>Ejemplo

En el ejemplo siguiente se fuerza sin conexión nodos cuyos nombres empiecen *HPCNode-CN -* y, a continuación, deja de los nodos.

Detener-HPCIaaSNode.ps1 – nombre HPCNodeCN-*-forzar

## <a name="next-steps"></a>Pasos siguientes

* Si desea una forma para aumentar o reducir los nodos de clúster según la carga de trabajo actual de los trabajos y tareas en el clúster automáticamente, consulte [reducir y ampliar los recursos de clúster de HPC Pack en Azure según la carga de trabajo de clúster de automáticamente](virtual-machines-windows-classic-hpcpack-cluster-node-autogrowshrink.md).
