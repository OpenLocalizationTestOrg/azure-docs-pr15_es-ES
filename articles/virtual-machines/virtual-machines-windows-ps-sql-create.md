<properties
    pageTitle="Crear una máquina Virtual de servidor SQL en Azure PowerShell (jefe de recursos) | Microsoft Azure"
    description="Proporciona los pasos y secuencias de comandos de PowerShell para crear una máquina virtual de Azure con imágenes de galería de máquinas virtuales de SQL Server."
    services="virtual-machines-windows"
    documentationCenter="na"
    authors="rothja"
    manager="jhubbard"
    editor=""
    tags="azure-resource-manager" />
<tags
    ms.service="virtual-machines-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="vm-windows-sql-server"
    ms.workload="infrastructure-services"
    ms.date="10/25/2016"
    ms.author="jroth"/>

# <a name="provision-a-sql-server-virtual-machine-using-azure-powershell-resource-manager"></a>Aprovisionar una máquina virtual de SQL Server con PowerShell Azure (jefe de recursos)

> [AZURE.SELECTOR]
- [Portal](virtual-machines-windows-portal-sql-server-provision.md)
- [PowerShell](virtual-machines-windows-ps-sql-create.md)

## <a name="overview"></a>Información general

En este tutorial se muestra cómo crear una sola máquina virtual Azure utiliza el modelo de implementación de **Administrador de recursos de Azure** con los cmdlets de PowerShell de Azure. En este tutorial, creamos una sola máquina virtual con una sola unidad de disco de una imagen en la Galería de SQL. Vamos a crear nuevos proveedores para el almacenamiento, redes y recursos de cálculo que se utilizará la máquina virtual. Si tiene proveedores existentes para cualquiera de estos recursos, puede usar esos proveedores en su lugar.

Si necesita la versión básica de este tema, vea [aprovisionar una máquina virtual de SQL Server con Azure PowerShell clásico](virtual-machines-windows-classic-ps-sql-create.md).

## <a name="prerequisites"></a>Requisitos previos

Este tutorial necesitará:

- Una cuenta de Azure y la suscripción antes de empezar. Si no tiene una, suscribirse a una [versión de prueba gratuita](https://azure.microsoft.com/pricing/free-trial/).
- [Azure PowerShell)](../powershell-install-configure.md), versión mínima de 1.4.0 o posterior (en este tutorial escrito utilizando la versión 1.5.0).
    - Para recuperar su versión, escriba **Get-módulo Azure ListAvailable**.

## <a name="configure-your-subscription"></a>Configurar la suscripción

Abrir Windows PowerShell y establecer el acceso a su cuenta de Azure, ejecute el cmdlet siguiente. Aparecerá un pantalla iniciar sesión para escribir sus credenciales. Use el mismo correo electrónico y contraseña que utiliza para iniciar sesión en el portal de Azure.

    Add-AzureRmAccount

Tras iniciar sesión correctamente en verán la información en pantalla que incluye la SubscriptionId con la que ha iniciado sesión en. Esta es la suscripción en la que los recursos de este tutorial se creará a menos que cambie a una suscripción diferente. Si tiene varios SubscriptionIds, ejecute el siguiente cmdlet para devolver una lista de todos los SubscriptionIds:

    Get-AzureRmSubscription

Para cambiar a otro SubscriptionID, ejecute el cmdlet siguiente con su SubscriptionId deseada.

    Select-AzureRmSubscription -SubscriptionId xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx

## <a name="define-image-variables"></a>Definir variables de imagen

Para simplificar el uso y la descripción de la secuencia de comandos completa de este tutorial, comenzaremos definiendo un número de variables. Cambie los valores de parámetro como convenga, pero tenga cuidado relacionados con la longitud del nombre y caracteres especiales al modificar los valores de las restricciones de nomenclatura.

### <a name="location-and-resource-group"></a>Ubicación y el grupo de recursos
Use dos variables para definir la región de datos y el grupo de recursos en el que creará los otros recursos de la máquina virtual.

Modificar como desee y, a continuación, ejecute los siguientes cmdlets para inicializar estas variables.

    $Location = "SouthCentralUS"
    $ResourceGroupName = "sqlvm1"

### <a name="storage-properties"></a>Propiedades de almacenamiento

Utilice las variables siguientes para definir la cuenta de almacenamiento y el tipo de almacenamiento de la máquina virtual.

Modificar como desee y, a continuación, ejecute el siguiente cmdlet para inicializar estas variables. Tenga en cuenta que en este ejemplo, estamos utilizando [Almacenamiento Premium](../storage/storage-premium-storage.md), que se recomienda para cargas de trabajo de producción. Para obtener información detallada en esta guía y otras recomendaciones, consulte [prácticas recomendadas de rendimiento para SQL Server en Azure máquinas virtuales de Windows](virtual-machines-windows-sql-performance.md).

    $StorageName = $ResourceGroupName + "storage"
    $StorageSku = "Premium_LRS"

### <a name="network-properties"></a>Propiedades de red

Utilice las variables siguientes para definir la interfaz de red, el método de asignación de TCP/IP, el nombre de red virtual, el nombre de subred virtual, el intervalo de direcciones IP de la red virtual, el intervalo de direcciones IP para la subred y la etiqueta de nombre de dominio público para usarlo con la red de la máquina virtual.

Modificar como desee y, a continuación, ejecute el siguiente cmdlet para inicializar estas variables.

    $InterfaceName = $ResourceGroupName + "ServerInterface"
    $TCPIPAllocationMethod = "Dynamic"
    $VNetName = $ResourceGroupName + "VNet"
    $SubnetName = "Default"
    $VNetAddressPrefix = "10.0.0.0/16"
    $VNetSubnetAddressPrefix = "10.0.0.0/24"
    $DomainName = "sqlvm1"   

### <a name="virtual-machine-properties"></a>Propiedades de la máquina virtual

Utilice las variables siguientes para definir el nombre de la máquina virtual, el nombre del equipo, el tamaño de la máquina virtual y el nombre del disco de sistema operativo para la máquina virtual.

Modificar como desee y, a continuación, ejecute el siguiente cmdlet para inicializar estas variables.

    $VMName = $ResourceGroupName + "VM"
    $ComputerName = $ResourceGroupName + "Server"
    $VMSize = "Standard_DS13"
    $OSDiskName = $VMName + "OSDisk"

### <a name="image-properties"></a>Propiedades de imagen

Utilice las variables siguientes para definir la imagen que se utilizará para la máquina virtual. En este ejemplo, se utiliza la imagen de SQL Server 2016 Enterprise.

Modificar como desee y, a continuación, ejecute el siguiente cmdlet para inicializar estas variables.

    $PublisherName = "MicrosoftSQLServer"
    $OfferName = "SQL2016-WS2016"
    $Sku = "Enterprise"
    $Version = "latest"

Tenga en cuenta que puede obtener una lista completa de ofertas de imagen de SQL Server con el comando Get-AzureRmVMImageOffer:

    Get-AzureRmVMImageOffer -Location 'East US' -Publisher 'MicrosoftSQLServer'

Y puede ver los SKU disponibles para una oferta con el comando Get-AzureRmVMImageSku. El comando siguiente muestra que todos los SKU disponibles para la **SQL2014SP1 WS2012R2** de la oferta.

    Get-AzureRmVMImageSku -Location 'East US' -Publisher 'MicrosoftSQLServer' -Offer 'SQL2014SP1-WS2012R2' | Select Skus

## <a name="create-a-resource-group"></a>Crear un grupo de recursos

Con el modelo de implementación de administrador de recursos, el primer objeto que cree es el grupo de recursos. Se utilizará el cmdlet [AzureRmResourceGroup de nuevo](https://msdn.microsoft.com/library/mt759837.aspx) para crear un grupo de recursos de Azure y sus recursos con el nombre del grupo de recursos y la ubicación definidos por las variables que inicializado anteriormente.

Ejecute el cmdlet siguiente para crear el nuevo grupo de recursos.

    New-AzureRmResourceGroup -Name $ResourceGroupName -Location $Location

## <a name="create-a-storage-account"></a>Crear una cuenta de almacenamiento

La máquina virtual requiere recursos de almacenamiento para el disco de sistema operativo y para los archivos de datos y de registro de SQL Server. Para simplificar, se creará un único disco ambas. Puede adjuntar discos adicionales más adelante mediante el cmdlet de [Azure Agregar disco](https://msdn.microsoft.com/library/azure/dn495252.aspx) para colocar los datos de SQL Server y archivos de registro en discos dedicados. Se utilizará el cmdlet [AzureRmStorageAccount de nuevo](https://msdn.microsoft.com/library/mt607148.aspx) para crear una cuenta de almacenamiento estándar en el nuevo grupo de recursos y con el nombre de la cuenta de almacenamiento, el nombre de Sku de almacenamiento y la ubicación definido mediante las variables que inicializado anteriormente.

Ejecute el cmdlet siguiente para crear la nueva cuenta de almacenamiento.  

    $StorageAccount = New-AzureRmStorageAccount -ResourceGroupName $ResourceGroupName -Name $StorageName -SkuName $StorageSku -Kind "Storage" -Location $Location

## <a name="create-network-resources"></a>Crear recursos de red

La máquina virtual requiere una cantidad de recursos de la red para la conectividad de red.

- Cada máquina virtual requiere una red virtual.
- Una red virtual debe tener al menos una subred definida.
- Una interfaz de red debe definirse con un público o una dirección IP privada.

### <a name="create-a-virtual-network-subnet-configuration"></a>Crear una configuración de subred de una red virtual

Comenzaremos creando una configuración de subred para nuestra red virtual. Nuestro tutorial, creamos una subred predeterminada mediante el cmdlet [AzureRmVirtualNetworkSubnetConfig de nuevo](https://msdn.microsoft.com/library/mt619412.aspx) . Crearemos nuestra configuración de subred de una red virtual con el prefijo de nombre y dirección de subred definido mediante las variables que inicializado anteriormente.

>[AZURE.NOTE] Puede definir propiedades adicionales de la configuración de subred de una red virtual con este cmdlet, pero que está fuera del ámbito de este tutorial.

Ejecute el cmdlet siguiente para crear la configuración de subred virtual.

    $SubnetConfig = New-AzureRmVirtualNetworkSubnetConfig -Name $SubnetName -AddressPrefix $VNetSubnetAddressPrefix

### <a name="create-a-virtual-network"></a>Crear una red virtual

A continuación, crearemos nuestra red virtual mediante el cmdlet [AzureRmVirtualNetwork de nuevo](https://msdn.microsoft.com/library/mt603657.aspx) . Vamos a crear nuestra red virtual en su nuevo grupo de recursos, con el nombre, la ubicación y la dirección prefijo definido mediante las variables que inicializado anteriormente y mediante la configuración de subred que haya definido en el paso anterior.

Ejecute el cmdlet siguiente para crear la red virtual.

    $VNet = New-AzureRmVirtualNetwork -Name $VNetName -ResourceGroupName $ResourceGroupName -Location $Location -AddressPrefix $VNetAddressPrefix -Subnet $SubnetConfig

### <a name="create-the-public-ip-address"></a>Crear la dirección IP pública

Ahora que tenemos nuestro red virtual definida, es necesario configurar una dirección IP para la conectividad de la máquina virtual. En este tutorial, creamos una dirección IP pública mediante IP dinámica direccionamiento para admitir la conexión a Internet. Se utilizará el cmdlet [AzureRmPublicIpAddress de nuevo](https://msdn.microsoft.com/library/mt603620.aspx) para crear la dirección IP pública en el grupo de recursos que se crearon prevously y con el nombre, la ubicación, el método de asignación y la etiqueta de nombre de dominio DNS definido mediante las variables que inicializado anteriormente.

>[AZURE.NOTE] Puede definir propiedades adicionales de la dirección IP pública con este cmdlet, pero que está fuera del ámbito de este tutorial inicial. También puede crear una dirección privada o una dirección con una dirección estática, pero que también está fuera del ámbito de este tutorial.

Ejecute el cmdlet siguiente para crear la dirección IP pública.

    $PublicIp = New-AzureRmPublicIpAddress -Name $InterfaceName -ResourceGroupName $ResourceGroupName -Location $Location -AllocationMethod $TCPIPAllocationMethod -DomainNameLabel $DomainName

### <a name="create-the-network-interface"></a>Crear la interfaz de red

Se está listos crear la interfaz de red que utilizará nuestra máquina virtual. Se utilizará el cmdlet [AzureRmNetworkInterface de nuevo](https://msdn.microsoft.com/library/mt619370.aspx) para crear la interfaz de red en el grupo de recursos creado anteriormente y con el nombre, la ubicación, la subred y la dirección IP pública definido anteriormente.

Ejecute el cmdlet siguiente para crear la interfaz de red.

    $Interface = New-AzureRmNetworkInterface -Name $InterfaceName -ResourceGroupName $ResourceGroupName -Location $Location -SubnetId $VNet.Subnets[0].Id -PublicIpAddressId $PublicIp.Id

## <a name="configure-a-vm-object"></a>Configurar un objeto VM

Ahora que tenemos recursos de almacenamiento y de red definidos, estamos listos para definir los recursos de cálculo para la máquina virtual. Nuestro tutorial, nos se especificar el tamaño de la máquina virtual y varias propiedades de sistema operativo, especifique la interfaz de red que se ha creado anteriormente, defina el almacenamiento de blobs y luego especifique el disco de sistema operativo.

### <a name="create-the-vm-object"></a>Crear el objeto de máquina virtual

Empezaremos especificando el tamaño de la máquina virtual. En este tutorial, nos estamos que especifica un DS13. Se utilizará el cmdlet [AzureRmVMConfig de nuevo](https://msdn.microsoft.com/library/mt603727.aspx) para crear un objeto configurable máquina virtual con el nombre y el tamaño definido mediante las variables que inicializado anteriormente.

Ejecute el cmdlet siguiente para crear el objeto de máquina virtual.

    $VirtualMachine = New-AzureRmVMConfig -VMName $VMName -VMSize $VMSize

### <a name="create-a-credential-object-to-hold-the-name-and-password-for-the-local-administrator-credentials"></a>Crear un objeto de credenciales para almacenar el nombre y la contraseña de las credenciales de administrador local

Antes de que podemos establecer las propiedades de sistema operativo de la máquina virtual, es necesario proporcionar las credenciales para la cuenta de administrador local como una cadena segura. Para ello, usamos el cmdlet [Get-Credential](https://technet.microsoft.com/library/hh849815.aspx) .

Ejecutar el siguiente cmdlet y, en la ventana de solicitud de credenciales de Windows PowerShell, escriba el nombre y la contraseña para la cuenta de administrador local en la máquina virtual de Windows.

    $Credential = Get-Credential -Message "Type the name and password of the local administrator account."

### <a name="set-the-operating-system-properties-for-the-virtual-machine"></a>Establecer las propiedades de sistema operativo de la máquina virtual

Ahora estamos listos establecer las propiedades de sistema operativo de la máquina virtual. Se usará el cmdlet [Set-AzureRmVMOperatingSystem](https://msdn.microsoft.com/library/mt603843.aspx) para establecer el tipo de sistema operativo Windows, requieren el [agente de máquina virtual](virtual-machines-windows-classic-agents-and-extensions.md) para instalarse, especificar que el cmdlet habilita la actualización automática y establecer el nombre de la máquina virtual, el nombre del equipo y la credencial utilizando las variables que inicializado anteriormente.

Ejecute el cmdlet siguiente para configurar las propiedades de sistema operativo de la máquina virtual.

    $VirtualMachine = Set-AzureRmVMOperatingSystem -VM $VirtualMachine -Windows -ComputerName $ComputerName -Credential $Credential -ProvisionVMAgent -EnableAutoUpdate

### <a name="add-the-network-interface-to-the-virtual-machine"></a>Agregar la interfaz de red en la máquina virtual

A continuación, agregaremos la interfaz de red que hemos creado previamente en la máquina virtual. Se usará el cmdlet [AzureRmVMNetworkInterface agregar](https://msdn.microsoft.com/library/mt619351.aspx) para agregar la interfaz de red mediante la variable de interfaz de red que haya definido anteriormente.

Ejecute el cmdlet siguiente para establecer la interfaz de red para el equipo virtual.

    $VirtualMachine = Add-AzureRmVMNetworkInterface -VM $VirtualMachine -Id $Interface.Id

### <a name="set-the-blob-storage-location-for-the-disk-to-be-used-by-the-virtual-machine"></a>Establecer la ubicación de almacenamiento de blobs para el disco que se utilizará la máquina virtual

A continuación, se establecerá la ubicación de almacenamiento de blobs para el disco usarlo con la máquina virtual mediante las variables que haya definido anteriormente.

Ejecute el cmdlet siguiente para establecer la ubicación de almacenamiento de blobs de Windows.

    $OSDiskUri = $StorageAccount.PrimaryEndpoints.Blob.ToString() + "vhds/" + $OSDiskName + ".vhd"

### <a name="set-the-operating-system-disk-properties-for-the-virtual-machine"></a>Establecer propiedades de disco de la máquina virtual del sistema operativo

A continuación, se establecerá el sistema operativo disco propiedades de la máquina virtual. Se utilizará el cmdlet [Set-AzureRmVMOSDisk](https://msdn.microsoft.com/library/mt603746.aspx) para especificar que el sistema operativo de la máquina virtual se proceden de una imagen para establecer el almacenamiento en caché para leer solo (ya se está instalando SQL Server en el mismo disco) y definir el nombre de la máquina virtual y el disco de sistema operativo definidos por el uso de las variables que se definió anteriormente.

Ejecute el cmdlet siguiente para configurar el sistema operativo propiedades de disco para el equipo virtual.

    $VirtualMachine = Set-AzureRmVMOSDisk -VM $VirtualMachine -Name $OSDiskName -VhdUri $OSDiskUri -Caching ReadOnly -CreateOption FromImage

### <a name="specify-the-platform-image-for-the-virtual-machine"></a>Especificar la imagen de la plataforma de la máquina virtual

Es el último paso de configuración especificar la imagen de la plataforma de nuestra máquina virtual. Nuestro tutorial, usamos la imagen de SQL Server 2016 CTP más reciente. Se usará el cmdlet [Set-AzureRmVMSourceImage](https://msdn.microsoft.com/library/mt619344.aspx) para usar esta imagen definida por las variables que haya definido anteriormente.

Ejecute el siguiente cmdlet para especificar la imagen de la plataforma de la máquina virtual.

    $VirtualMachine = Set-AzureRmVMSourceImage -VM $VirtualMachine -PublisherName $PublisherName -Offer $OfferName -Skus $Sku -Version $Version

## <a name="create-the-sql-vm"></a>Crear la máquina virtual de SQL

Ahora que ha terminado los pasos de configuración, está listo para crear la máquina virtual. Se utilizará el cmdlet [AzureRmVM de nuevo](https://msdn.microsoft.com/library/mt603754.aspx) para crear la máquina virtual mediante las variables que hemos definido.

Ejecute el cmdlet siguiente para crear la máquina virtual.

    New-AzureRmVM -ResourceGroupName $ResourceGroupName -Location $Location -VM $VirtualMachine

Se crea la máquina virtual. Observe que se crea una cuenta de almacenamiento estándar para diagnósticos de arranque porque la cuenta de almacenamiento especificado para el disco de la máquina virtual es una cuenta de almacenamiento premium.

Ahora puede ver este equipo en el Portal de Azure para ver [su dirección IP pública y su nombre de dominio completo](virtual-machines-windows-portal-sql-server-provision.md#Connect).  

## <a name="example-script"></a>Secuencia de comandos de ejemplo

La siguiente secuencia de comandos contiene la secuencia de comandos de PowerShell completa para este tutorial. Se supone que ya ha configuración de la suscripción de Azure para usarlo con los comandos **Agregar AzureRmAccount** y **Seleccione AzureRmSubscription** .


    # Variables
    ## Global
    $Location = "SouthCentralUS"
    $ResourceGroupName = "sqlvm1"
    ## Storage
    $StorageName = $ResourceGroupName + "storage"
    $StorageSku = "Premium_LRS"

    ## Network
    $InterfaceName = $ResourceGroupName + "ServerInterface"
    $VNetName = $ResourceGroupName + "VNet"
    $SubnetName = "Default"
    $VNetAddressPrefix = "10.0.0.0/16"
    $VNetSubnetAddressPrefix = "10.0.0.0/24"
    $TCPIPAllocationMethod = "Dynamic"
    $DomainName = "sqlvm1"

    ##Compute
    $VMName = $ResourceGroupName + "VM"
    $ComputerName = $ResourceGroupName + "Server"
    $VMSize = "Standard_DS13"
    $OSDiskName = $VMName + "OSDisk"

    ##Image
    $PublisherName = "MicrosoftSQLServer"
    $OfferName = "SQL2016-WS2016"
    $Sku = "Enterprise"
    $Version = "latest"

    # Resource Group
    New-AzureRmResourceGroup -Name $ResourceGroupName -Location $Location

    # Storage
    $StorageAccount = New-AzureRmStorageAccount -ResourceGroupName $ResourceGroupName -Name $StorageName -SkuName $StorageSku -Kind "Storage" -Location $Location

    # Network
    $SubnetConfig = New-AzureRmVirtualNetworkSubnetConfig -Name $SubnetName -AddressPrefix $VNetSubnetAddressPrefix
    $VNet = New-AzureRmVirtualNetwork -Name $VNetName -ResourceGroupName $ResourceGroupName -Location $Location -AddressPrefix $VNetAddressPrefix -Subnet $SubnetConfig
    $PublicIp = New-AzureRmPublicIpAddress -Name $InterfaceName -ResourceGroupName $ResourceGroupName -Location $Location -AllocationMethod $TCPIPAllocationMethod -DomainNameLabel $DomainName
    $Interface = New-AzureRmNetworkInterface -Name $InterfaceName -ResourceGroupName $ResourceGroupName -Location $Location -SubnetId $VNet.Subnets[0].Id -PublicIpAddressId $PublicIp.Id

    # Compute
    $VirtualMachine = New-AzureRmVMConfig -VMName $VMName -VMSize $VMSize
    $Credential = Get-Credential -Message "Type the name and password of the local administrator account."
    $VirtualMachine = Set-AzureRmVMOperatingSystem -VM $VirtualMachine -Windows -ComputerName $ComputerName -Credential $Credential -ProvisionVMAgent -EnableAutoUpdate #-TimeZone = $TimeZone
    $VirtualMachine = Add-AzureRmVMNetworkInterface -VM $VirtualMachine -Id $Interface.Id
    $OSDiskUri = $StorageAccount.PrimaryEndpoints.Blob.ToString() + "vhds/" + $OSDiskName + ".vhd"
    $VirtualMachine = Set-AzureRmVMOSDisk -VM $VirtualMachine -Name $OSDiskName -VhdUri $OSDiskUri -Caching ReadOnly -CreateOption FromImage

    # Image
    $VirtualMachine = Set-AzureRmVMSourceImage -VM $VirtualMachine -PublisherName $PublisherName -Offer $OfferName -Skus $Sku -Version $Version

    ## Create the VM in Azure
    New-AzureRmVM -ResourceGroupName $ResourceGroupName -Location $Location -VM $VirtualMachine

## <a name="next-steps"></a>Pasos siguientes
Después de crear la máquina virtual, ya está listo para conectarse a la máquina virtual mediante la conectividad RDP y configuración. Para obtener más información, vea [conectarse a una máquina Virtual de SQL Server en Azure (jefe de recursos)](virtual-machines-windows-sql-connect.md).
