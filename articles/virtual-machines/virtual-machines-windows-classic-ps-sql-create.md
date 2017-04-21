<properties
    pageTitle="Crear una máquina Virtual de servidor SQL en Azure PowerShell (clásico) | Microsoft Azure"
    description="Proporciona los pasos y secuencias de comandos de PowerShell para crear una máquina virtual de Azure con imágenes de galería de máquinas virtuales de SQL Server. En este tema se utiliza el modo de implementación clásico."
    services="virtual-machines-windows"
    documentationCenter="na"
    authors="rothja"
    manager="jhubbard"
    editor=""
    tags="azure-service-management" />
<tags
    ms.service="virtual-machines-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="vm-windows-sql-server"
    ms.workload="infrastructure-services"
    ms.date="10/07/2016"
    ms.author="jroth" />

# <a name="provision-a-sql-server-virtual-machine-using-azure-powershell-classic"></a>Aprovisionar una máquina virtual de SQL Server con PowerShell Azure (clásico)

## <a name="overview"></a>Información general

Este artículo proporciona los pasos para crear una máquina virtual de SQL Server en Azure con los cmdlets de PowerShell.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]En la versión de administrador de recursos de este tema, vea [aprovisionar una máquina virtual de SQL Server con el Administrador de recursos de PowerShell de Azure](virtual-machines-windows-ps-sql-create.md).

### <a name="install-and-configure-powershell"></a>Instalar y configurar PowerShell:

1. Si no tiene una cuenta de Azure, visite [Azure prueba gratuita](https://azure.microsoft.com/pricing/free-trial/).

2. [Descargar e instalar la última comandos de PowerShell de Azure](../powershell-install-configure.md).

3. Inicie Windows PowerShell y conectarse a su suscripción de Azure con el comando **Agregar AzureAccount** .

        Add-AzureAccount

## <a name="determine-your-target-azure-region"></a>Determinar el destino región de Azure

El equipo de SQL Server Virtual se alojarán en un servicio de nube que se encuentra en un área específica de Azure. Los siguientes pasos le ayudan a determinar su región, la cuenta de almacenamiento y servicio que se utilizará para el resto del tutorial en la nube.

1. Determinar el centro de datos que desea usar para hospedar el VM de SQL Server. Los siguientes comandos de PowerShell mostrará las regiones disponibles en detalle con una lista de resumen al final.

        Get-AzureLocation
        (Get-AzureLocation).Name

2.  Una vez que haya identificado su ubicación preferida, establezca una variable denominada **$dcLocation** a esa región.

        $dcLocation = "<region name>"

## <a name="set-your-subscription-and-storage-account"></a>Configurar su cuenta de suscripción y almacenamiento

1. Determinar la suscripción de Azure que se usa para la nueva máquina virtual.

        (Get-AzureSubscription).SubscriptionName

1. Asigne el destino Azure suscripción a la variable **$subscr** . A continuación, establezca como su suscripción actual de Azure.

        $subscr="<subscription name>"
        Select-AzureSubscription -SubscriptionName $subscr –Current

1. A continuación, busque cuentas de almacenamiento existentes. La siguiente secuencia de comandos muestra todas las cuentas de almacenamiento que existen en su región elegido:

        (Get-AzureStorageAccount | where { $_.GeoPrimaryLocation -eq $dcLocation }).StorageAccountName

    >[AZURE.NOTE] Si necesita una nueva cuenta de almacenamiento, primero cree un nombre de cuenta de almacenamiento de todas las minúsculas con el comando nuevo AzureStorageAccount como en el ejemplo siguiente: **nuevo AzureStorageAccount - StorageAccountName "<storage account name>"-ubicación $dcLocation**

1. Asigne el nombre de cuenta de almacenamiento de destino a la **$staccount**. A continuación, use **Conjunto AzureSubscription** para establecer la suscripción y la cuenta de almacenamiento actual.

        $staccount="<storage account name>"
        Set-AzureSubscription -SubscriptionName $subscr -CurrentStorageAccountName $staccount

## <a name="select-a-sql-server-virtual-machine-image"></a>Seleccione una imagen de la máquina virtual de SQL Server

1. Descubra la lista de imágenes de máquinas virtuales de SQL Server disponibles de la galería. Estas imágenes que todos tienen una propiedad **ImageFamily** que comienza con "SQL". La siguiente consulta muestra la familia de imagen disponible que tienen preinstalado de SQL Server.

        Get-AzureVMImage | where { $_.ImageFamily -like "SQL*" } | select ImageFamily -Unique | Sort-Object -Property ImageFamily

1. Cuando encuentre la familia de imagen de la máquina virtual, puede haber varias imágenes publicadas en esta familia. Use la siguiente secuencia de comandos para buscar el nombre de imagen publicados máquina virtual más reciente para la familia de la imagen seleccionada (por ejemplo, **SQL Server 2016 RTM Enterprise en Windows Server 2012 R2**):

        $family="<ImageFamily value>"
        $image=Get-AzureVMImage | where { $_.ImageFamily -eq $family } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1

        echo "Selected SQL Server image name:"
        echo "   $image"

## <a name="create-the-virtual-machine"></a>Crear la máquina virtual

Por último, cree la máquina virtual con PowerShell:

1. Crear un servicio de nube para la nueva máquina virtual de host. Tenga en cuenta que también es posible usar un servicio de nube existente en su lugar. Crear una nueva variable **$svcname** con el nombre corto del servicio de nube.

        $svcname = "<cloud service name>"
        New-AzureService -ServiceName $svcname -Label $svcname -Location $dcLocation

2. Especifique el nombre de la máquina virtual y un tamaño. Para obtener más información sobre los tamaños de máquina virtual, vea [Tamaños de máquina Virtual para Azure](virtual-machines-windows-sizes.md).

        $vmname="<machine name>"
        $vmsize="<Specify one: Large, ExtraLarge, A5, A6, A7, A8, A9, or see the link to the other VM sizes>"
        $vm1=New-AzureVMConfig -Name $vmname -InstanceSize $vmsize -ImageName $image

3. Especifique la cuenta de administrador local y la contraseña.

        $cred=Get-Credential -Message "Type the name and password of the local administrator account."
        $vm1 | Add-AzureProvisioningConfig -Windows -AdminUsername $cred.GetNetworkCredential().Username -Password $cred.GetNetworkCredential().Password

4. Ejecutar la siguiente secuencia de comandos para crear la máquina virtual.

        New-AzureVM –ServiceName $svcname -VMs $vm1

>[AZURE.NOTE] Para una explicación adicional y opciones de configuración, vea la sección **generar el conjunto de comandos** de [PowerShell de Azure usar para crear y configurar previamente máquinas virtuales basadas en Windows](virtual-machines-windows-classic-create-powershell.md).

## <a name="example-powershell-script"></a>Secuencia de comandos de PowerShell de ejemplo

Proporciona la siguiente secuencia de comandos y ejemplo de un script completo que crea una máquina virtual de **SQL Server 2016 RTM Enterprise en Windows Server 2012 R2** . Si utiliza esta secuencia de comandos, debe personalizar las variables iniciales basadas en los pasos anteriores en este tema.

    # Customize these variables based on your settings and requirements:
    $dcLocation = "East US"
    $subscr="mysubscription"
    $staccount="mystorageaccount"
    $family="SQL Server 2016 RTM Enterprise on Windows Server 2012 R2"
    $svcname = "mycloudservice"
    $vmname="myvirtualmachine"
    $vmsize="A5"

    # Set the current subscription and storage account
    # Comment out the New-AzureStorageAccount line if the account already exists
    Select-AzureSubscription -SubscriptionName $subscr –Current
    New-AzureStorageAccount -StorageAccountName $staccount -Location $dcLocation
    Set-AzureSubscription -SubscriptionName $subscr -CurrentStorageAccountName $staccount

    # Select the most recent VM image in this image family:
    $image=Get-AzureVMImage | where { $_.ImageFamily -eq $family } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1

    # Create the new cloud service; comment out this line if cloud service exists already:
    New-AzureService -ServiceName $svcname -Label $svcname -Location $dcLocation

    # Create the VM config:
    $vm1=New-AzureVMConfig -Name $vmname -InstanceSize $vmsize -ImageName $image

    # Set administrator credentials:
    $cred=Get-Credential -Message "Type the name and password of the local administrator account."
    $vm1 | Add-AzureProvisioningConfig -Windows -AdminUsername $cred.GetNetworkCredential().Username -Password $cred.GetNetworkCredential().Password

    # Create the SQL Server VM:
    New-AzureVM –ServiceName $svcname -VMs $vm1


## <a name="connect-with-remote-desktop"></a>Conectar con Escritorio remoto

1. Crear la. Archivos RDP en la carpeta de documentos del usuario actual para iniciar estas máquinas virtuales para completar la instalación:

        $documentspath = [environment]::getfolderpath("mydocuments")
        Get-AzureRemoteDesktopFile -ServiceName $svcname -Name $vmname -LocalPath "$documentspath\vm1.rdp"

1. En el directorio de documentos, inicie el archivo RDP. Conectar con el nombre de usuario administrador y la contraseña proporcionado anteriormente (por ejemplo, si su nombre de usuario es VMAdmin, especifique "\VMAdmin" como el usuario y proporcione la contraseña).

        cd $documentspath
        .\vm1.rdp

## <a name="complete-the-configuration-of-the-sql-server-machine-for-remote-access"></a>Completar la configuración de la máquina de SQL Server para el acceso remoto

Después de iniciar sesión en el equipo de escritorio remoto, configure SQL Server según las instrucciones de [los pasos para configurar la conectividad de SQL Server en una máquina virtual de Azure](virtual-machines-windows-classic-sql-connect.md#steps-for-configuring-sql-server-connectivity-in-an-azure-vm).

## <a name="next-steps"></a>Pasos siguientes

Puede encontrar instrucciones adicionales para el aprovisionamiento de máquinas virtuales con PowerShell en la [documentación de máquinas virtuales de Windows](virtual-machines-windows-classic-create-powershell.md). Secuencias de comandos relacionados con SQL Server y Premium almacenamiento adicional, vea [Usar el almacenamiento de Premium con SQL Server en máquinas virtuales de Windows Azure](virtual-machines-windows-classic-sql-server-premium-storage.md).

En muchos casos, el siguiente paso es migrar las bases de datos a este nuevo VM del servidor SQL. Para obtener instrucciones de migración de base de datos, vea [migrar una base de datos de SQL Server en una máquina virtual de Azure](virtual-machines-windows-migrate-sql.md).

Si también está interesado en con el portal de Azure para crear máquinas virtuales de SQL, vea [el aprovisionamiento de un equipo Virtual con SQL Server en Azure](virtual-machines-windows-portal-sql-server-provision.md). Observe que el tutorial que le guiará a través del portal crea máquinas virtuales usando el modelo recomendado del Administrador de recursos, en lugar del modelo clásico utilizado en este tema de PowerShell.

Además de estos recursos, le recomendamos que revise [otros temas relacionados con la ejecución de SQL Server en Azure máquinas virtuales de Windows](virtual-machines-windows-sql-server-iaas-overview.md).
