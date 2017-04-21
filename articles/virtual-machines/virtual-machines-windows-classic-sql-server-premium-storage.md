<properties
    pageTitle="Usar el almacenamiento de Azure Premium con SQL Server | Microsoft Azure"
    description="En este artículo usa recursos creados con el modelo de implementación clásica y se ofrece instrucciones sobre cómo usar el almacenamiento de Azure Premium con SQL Server se ejecuta en Azure máquinas virtuales de Windows."
    services="virtual-machines-windows"
    documentationCenter=""
    authors="danielsollondon"
    manager="jhubbard"
    editor="monicar"    
    tags="azure-service-management"/>

<tags
    ms.service="virtual-machines-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="vm-windows-sql-server"
    ms.workload="infrastructure-services"
    ms.date="08/19/2016"
    ms.author="jroth"/>

# <a name="use-azure-premium-storage-with-sql-server-on-virtual-machines"></a>Usar el almacenamiento de Azure Premium con SQL Server en máquinas virtuales


## <a name="overview"></a>Información general

[Almacenamiento de Azure Premium](../storage/storage-premium-storage.md) es la siguiente generación de almacenamiento que proporciona baja latencia y alto rendimiento IO. Funciona mejor para claves IO intensivas cargas de trabajo como SQL Server en [máquinas virtuales](https://azure.microsoft.com/services/virtual-machines/)de IaaS.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]


Este artículo proporciona consejos de diseño y para migrar una máquina Virtual que ejecuta SQL Server para usar almacenamiento Premium. Esto incluye Azure infraestructura (red, almacenamiento) y los pasos de la máquina virtual de Windows de invitado. El ejemplo en el [Apéndice](#appendix-migrating-a-multisite-alwayson-cluster-to-premium-storage) muestra una migración de llevar a cabo integral de cómo mover máquinas virtuales de mayores aprovechar mejor almacenamiento SSD local con PowerShell.

Es importante comprender el proceso de llevar a cabo de utilizando el almacenamiento de Azure Premium con SQL Server en máquinas virtuales de IAAS. Esto incluye:

- Identificación de los requisitos previos para usar almacenamiento Premium.
- Ejemplos de implementación de SQL Server en IaaS al almacenamiento Premium para implementaciones nuevas.
- Ejemplos de migración implementaciones existentes, servidores independientes e implementaciones con SQL siempre en disponibilidad de grupos.
- Enfoques de migración posibles.
- Ejemplo de end-to-end que muestra los pasos de Azure, Windows y SQL Server para la migración de una implementación siempre en existente.

Para obtener más información en SQL Server en Azure máquinas virtuales de Windows, vea [SQL Server en Azure máquinas virtuales de Windows](virtual-machines-windows-sql-server-iaas-overview.md).

**Autor:** Gerardo Sol **Revisores técnicos:** Luis Carlos Vargas arenque, Sanjay Mishra, Pravin Mital, Juergen Thomas, Gonzalo Ruiz.

## <a name="prerequisites-for-premium-storage"></a>Requisitos previos para el almacenamiento de Premium

Existen varios requisitos previos para usar el almacenamiento de Premium.

### <a name="machine-size"></a>Tamaño del equipo

Para usar el almacenamiento de Premium debe usar serie DS máquinas virtuales (VM). Si no ha utilizado máquinas serie DS en el servicio de nube antes, debe eliminar la máquina virtual existente, mantener los discos vinculados y, a continuación, cree un nuevo servicio de nube antes de volver a crear la máquina virtual como el tamaño de la función DS *. Para obtener más información sobre los tamaños de máquina Virtual, vea [Máquina Virtual y los tamaños de servicio de nube para Azure](virtual-machines-linux-sizes.md).

### <a name="cloud-services"></a>Servicios de nube

Solo puede usar DS * máquinas virtuales con almacenamiento Premium cuando se crean en un nuevo servicio de nube. Si está utilizando SQL Server siempre en Azure, siempre en escucha hará referencia a la dirección de Azure interna o externa IP de equilibrador de carga está asociada a un servicio de nube. En este artículo se centra en cómo migrar conservando disponibilidad en este escenario.

> [AZURE.NOTE] Una serie de DS * debe ser la primera VM que se implementa el nuevo servicio de nube.

### <a name="regional-vnets"></a>VNETS regionales

Para máquinas virtuales de DS * debe configurar la red Virtual (VNET) aloja sus máquinas virtuales para ser regional. Esto "amplía" la VNET es permitir que las VM más grandes que se aprovisiona en otros clústeres y permitir la comunicación entre ellas. En la siguiente captura de pantalla, la ubicación resaltada muestra regionales VNETs, mientras que el primer resultado muestra un VNET "estrecho".

![RegionalVNET][1]

Puede elevar una incidencia de soporte técnico de Microsoft para migrar a un VNET regional, Microsoft se realiza un cambio, a continuación, para completar la migración a regionales VNETs, cambie la propiedad AffinityGroup en la configuración de red. Exportar la configuración de red en PowerShell y, a continuación, reemplace la propiedad **AffinityGroup** en el elemento **VirtualNetworkSite** con una propiedad de **ubicación** . Especificar `Location = XXXX` donde `XXXX` es un área de Azure. A continuación, importar la nueva configuración.

Por ejemplo, teniendo en cuenta la siguiente configuración de VNET:

    <VirtualNetworkSite name="danAzureSQLnet" AffinityGroup="AzureSQLNetwork">
    <AddressSpace>
      <AddressPrefix>10.0.0.0/8</AddressPrefix>
      <AddressPrefix>172.16.0.0/12</AddressPrefix>
    </AddressSpace>
    <Subnets>
    ...
    </VirtualNetworkSite>

Para colocar un VNET regional en Europa occidental, cambie la configuración para lo siguiente:

    <VirtualNetworkSite name="danAzureSQLnet" Location="West Europe">
    <AddressSpace>
      <AddressPrefix>10.0.0.0/8</AddressPrefix>
      <AddressPrefix>172.16.0.0/12</AddressPrefix>
    </AddressSpace>
    <Subnets>
    ...
    </VirtualNetworkSite>

### <a name="storage-accounts"></a>Cuentas de almacenamiento

Debe crear una nueva cuenta de almacenamiento que está configurada para el almacenamiento de Premium. Tenga en cuenta que el uso de almacenamiento de Premium se establece en la cuenta de almacenamiento, no se encuentran en VHD individuales, sin embargo, cuando se usa una serie de DS * VM puede adjuntar el disco duro de virtual de cuentas de almacenamiento estándar y Premium. Puede considerar esta opción si no desea colocar el sistema operativo de disco duro virtual en la cuenta de almacenamiento Premium.

El siguiente comando **AzureStorageAccountPowerShell de nuevo** con el **tipo** de "Premium_LRS" crea una cuenta de almacenamiento Premium:

    $newstorageaccountname = "danpremstor"
    New-AzureStorageAccount -StorageAccountName $newstorageaccountname -Location "West Europe" -Type "Premium_LRS"   

### <a name="vhds-cache-settings"></a>Configuración de la caché de VHD

La diferencia principal entre crear discos que forman parte de una cuenta de almacenamiento Premium es la configuración de caché de disco. Se recomienda que use '**Almacenamiento en caché de lectura**' discos de volumen de datos de SQL Server. Para volúmenes de registro de transacciones, la configuración de caché de disco se debe establecer en '**Ninguno**'. Esto es diferente de las recomendaciones para las cuentas de almacenamiento estándar.

Una vez que se han adjuntado los VHD, no se puede modificar la configuración de caché. Necesarias para separar y volver a conectar el disco duro virtual con una configuración de la memoria caché actualizada.

### <a name="windows-storage-spaces"></a>Espacios de almacenamiento de Windows

Puede usar [Los espacios de almacenamiento de Windows](https://technet.microsoft.com/library/hh831739.aspx) como hizo con almacenamiento estándar anterior, podrá migrar una máquina virtual que ya está utilizando espacios de almacenamiento. En el ejemplo de [Apéndice](#appendix-migrating-a-multisite-alwayson-cluster-to-premium-storage) (paso 9 y posteriores) se muestra el código de Powershell para extraer e importar una máquina virtual con varios VHD adjunto.

Grupos de almacenamiento se ha usado con la cuenta de almacenamiento de Azure estándar para mejorar el rendimiento y reducir la latencia. Puede encontrar el valor en las pruebas de grupos de almacenamiento de almacenamiento Premium para implementaciones nuevas, pero agregan complejidad adicional con la instalación de almacenamiento.

#### <a name="how-to-find-which-azure-virtual-disks-map-to-storage-pools"></a>Cómo encontrar los discos virtuales de Azure se asignan a grupos de almacenamiento

Como han recomendaciones de configuración de caché diferente para VHD adjunto, decide copiar los VHD a una cuenta de almacenamiento Premium. Sin embargo, al volver a conectarlos a la nueva serie de DS VM, debe modificar la configuración de la memoria caché. Es más sencillo aplicar la configuración de la caché se recomienda cuando haya VHD independiente para los archivos de datos de SQL y archivos de registro (en lugar de un solo disco duro virtual que contenga) de almacenamiento de Premium.

> [AZURE.NOTE] Si tiene archivos de datos y de registro de SQL Server en el mismo volumen, la opción de caché que elija depende de los patrones de acceso IO para las cargas de trabajo de la base de datos. Pruebas solo pueden mostrar qué opción de caché es la mejor para este escenario.

Sin embargo, si está utilizando espacios de almacenamiento de Windows que están compuestos de varios VHD debe mirar las secuencias de comandos originales para identificar que asociada VHD están en el grupo específico, así que a continuación puede establecer la configuración de la caché según corresponda para cada disco.

Si no tiene la secuencia de comandos original mostrar que VHD se asignan al grupo de almacenamiento, puede usar los siguientes pasos para determinar la asignación de grupo de almacenamiento en disco.

Para cada disco, realice los siguientes pasos:

1. Obtener la lista de discos conectados a VM con el comando **Get-AzureVM** :

    AzureVM Get - ServiceName <servicename> -nombre <vmname> | Get-AzureDataDisk

1. Tenga en cuenta la Diskname y LUN.

    ![DisknameAndLUN][2]

1. Escritorio remoto en la máquina virtual. A continuación, vaya a **Administración de equipos** | **El Administrador de dispositivos** | **unidades de disco**. Ver las propiedades de cada uno de los discos virtuales Microsoft

    ![VirtualDiskProperties][3]

1. El número de LUN aquí es una referencia al número LUN que especifique al adjuntar el disco duro virtual a la máquina virtual.
1. Para el disco Virtual Microsoft vaya a la pestaña **Detalles** , a continuación, en la lista de **Propiedades** , vaya a **Clave del controlador**. En el **valor**, tenga en cuenta el **desplazamiento**, que es 0002 en la siguiente captura de pantalla. La 0002 indica la PhysicalDisk2 que hace referencia a la agrupación de almacenamiento.

    ![VirtualDiskPropertyDetails][4]

2. Para cada grupo de almacenamiento, descarga fuera los discos asociados:

    Get-StoragePool - FriendlyName AMS1pooldata | Get-físico

    ![GetStoragePool][5]

Ahora puede usar esta información para asociar adjuntos VHD a discos físicos en grupos de almacenamiento.

Una vez que ha asignado a VHD discos físicos en grupos de almacenamiento, a continuación, puede separar y copiar sobre a una cuenta de almacenamiento Premium, adjúntelos con la configuración de caché correctas. Consulte el ejemplo en el [Apéndice](#appendix-migrating-a-multisite-alwayson-cluster-to-premium-storage), los pasos 8 a 12. Estos pasos le muestran cómo extraer una configuración de disco adjunto VM de disco duro virtual a un archivo CSV, copie los VHD, alterar la configuración de caché de disco y por último vuelva a implementar la máquina virtual como una serie de DS VM con todos los discos conectados.

### <a name="vm-storage-bandwidth-and-vhd-storage-throughput"></a>Ancho de banda de almacenamiento VM y rendimiento de almacenamiento de disco duro virtual

La cantidad de rendimiento de almacenamiento depende de la tamaño DS * VM especificado y los tamaños de disco duro virtual. Las máquinas virtuales tienen subsidios diferentes para el número de VHD que se puede vincular y el ancho de banda máximo que admiten (MB/s). Para los números de ancho de banda específico, vea [Máquina Virtual y los tamaños de servicio de nube para Azure](virtual-machines-linux-sizes.md).

Mayor IOPS se obtienen con tamaños de disco más grandes. Debe considerar al pensar la ruta de migración. Para obtener información detallada, [consulte la tabla para IOPS y tipos de disco](../storage-premium-storage.md#scalability-and-performance-targets-when-using-premium-storage).

Por último, tenga en cuenta que VM tienen anchos de banda de disco máximo diferente que se admiten para todos los discos conectados. Carga es alta, podría saturar el ancho de banda máximo en disco disponible para ese tamaño del rol de máquina virtual. Por ejemplo un Standard_DS14 admitirá hasta 512 MB/s; por lo tanto, con tres discos P30 podría saturar el ancho de banda de disco de la máquina virtual. Pero, en este ejemplo, podría ser supera el límite de rendimiento según la combinación de lectura y escritura IOs.

## <a name="new-deployments"></a>Nuevas implementaciones

Las dos secciones siguientes muestran cómo puede implementar máquinas virtuales de SQL Server con el almacenamiento de Premium. Como se mencionó anteriormente, no necesariamente debe colocar el disco de sistema operativo en almacenamiento Premium. Puede hacerlo si va a colocar las cargas de trabajo intensivo de IO en el disco duro virtual de sistema operativo.

El primer ejemplo muestra el uso de imágenes de la Galería de Azure existente. El segundo ejemplo muestra cómo usar una imagen de máquina virtual personalizada que tiene en una cuenta existente de almacenamiento estándar.

> [AZURE.NOTE] Estos ejemplos, se supone que ya ha creado un VNET Regional.

### <a name="create-a-new-vm-with-premium-storage-with-gallery-image"></a>Crear una nueva máquina virtual con almacenamiento Premium con imagen de la Galería

El ejemplo siguiente muestra cómo colocar el sistema operativo de disco duro virtual en almacenamiento premium y adjuntar VHD de almacenamiento Premium. Sin embargo, puede colocar también el disco de sistema operativo en una cuenta de almacenamiento estándar y luego adjunte VHD que residen en una cuenta de almacenamiento Premium. Se muestran ambos escenarios.

    $mysubscription = "DansSubscription"
    $location = "West Europe"

    #Set up subscription
    Set-AzureSubscription -SubscriptionName $mysubscription
    Select-AzureSubscription -SubscriptionName $mysubscription -Current  

#### <a name="step-1-create-a-premium-storage-account"></a>Paso 1: Crear una cuenta de almacenamiento Premium


    #Create Premium Storage account, note Type
    $newxiostorageaccountname = "danspremsams"
    New-AzureStorageAccount -StorageAccountName $newxiostorageaccountname -Location $location -Type "Premium_LRS"  


#### <a name="step-2-create-a-new-cloud-service"></a>Paso 2: Crear un nuevo servicio de nube

    $destcloudsvc = "danNewSvcAms"
    New-AzureService $destcloudsvc -Location $location


#### <a name="step-3-reserve-a-cloud-service-vip-optional"></a>Paso 3: Reservar a VIP de servicio de nube (opcional)
    #check exisitng reserved VIP
    Get-AzureReservedIP

    $reservedVIPName = “sqlcloudVIP”
    New-AzureReservedIP –ReservedIPName $reservedVIPName –Label $reservedVIPName –Location $location

#### <a name="step-4-create-a-vm-container"></a>Paso 4: Crear un contenedor VM
    #Generate storage keys for later
    $xiostorage = Get-AzureStorageKey -StorageAccountName $newxiostorageaccountname

    ##Generate storage acc contexts
    $xioContext = New-AzureStorageContext –StorageAccountName $newxiostorageaccountname -StorageAccountKey $xiostorage.Primary   

    #Create container
    $containerName = 'vhds'
    New-AzureStorageContainer -Name $containerName -Context $xioContext

#### <a name="step-5-placing-os-vhd-on-standard-or-premium-storage"></a>Paso 5: Colocando el disco duro virtual de OS en estándar o Premium almacenamiento
    #NOTE: Set up subscription and default storage account which will be used to place the OS VHD in

    #If you want to place the OS VHD Premium Storage Account
    Set-AzureSubscription -SubscriptionName $mysubscription -CurrentStorageAccount  $newxiostorageaccountname  

    #If you wanted to place the OS VHD Standard Storage Account but attach Premium Storage VHDs then you would run this instead:
    $standardstorageaccountname = "danstdams"

    Set-AzureSubscription -SubscriptionName $mysubscription -CurrentStorageAccount  $standardstorageaccountname

#### <a name="step-6-create-vm"></a>Paso 6: Crear VM
    #Get list of available SQL Server Images from the Azure Image Gallery.
    $galleryImage = Get-AzureVMImage | where-object {$_.ImageName -like "*SQL*2014*Enterprise*"}
    $image = $galleryImage.ImageName

    #Set up Machine Specific Information
    $vmName = "dsDan1"
    $vnet = "dansvnetwesteur"
    $subnet = "SQL"
    $ipaddr = "192.168.0.8"

    #Remember to change to DS series VM
    $newInstanceSize = "Standard_DS1"

    #create new Avaiability Set
    $availabilitySet = "cloudmigAVAMS"

    #Machine User Credentials
    $userName = "myadmin"
    $pass = "mycomplexpwd4*"

    #Create VM Config
    $vmConfigsl = New-AzureVMConfig -Name $vmName -InstanceSize $newInstanceSize -ImageName $image  -AvailabilitySetName $availabilitySet  ` | Add-AzureProvisioningConfig -Windows ` -AdminUserName $userName -Password $pass | Set-AzureSubnet -SubnetNames $subnet | Set-AzureStaticVNetIP -IPAddress $ipaddr

    #Add Data and Log Disks to VM Config
    #Note the size specified ‘-DiskSizeInGB 1023’, this will attach 2 x P30 Premium Storage Disk Type
    #Utilising the Premium Storage enabled Storage account

    $vmConfigsl | Add-AzureDataDisk -CreateNew -DiskSizeInGB 1023 -LUN 0 -HostCaching "ReadOnly"  -DiskLabel "DataDisk1" -MediaLocation "https://$newxiostorageaccountname.blob.core.windows.net/vhds/$vmName-data1.vhd"
    $vmConfigsl | Add-AzureDataDisk -CreateNew -DiskSizeInGB 1023 -LUN 1 -HostCaching "None"  -DiskLabel "logDisk1" -MediaLocation "https://$newxiostorageaccountname.blob.core.windows.net/vhds/$vmName-log1.vhd"

    #Create VM
    $vmConfigsl  | New-AzureVM –ServiceName $destcloudsvc -VNetName $vnet ## Optional (-ReservedIPName $reservedVIPName)  

    #Add RDP Endpoint
    $EndpointNameRDPInt = "3389"
    Get-AzureVM -ServiceName $destcloudsvc -Name $vmName | Add-AzureEndpoint -Name "EndpointNameRDP" -Protocol "TCP" -PublicPort "53385" -LocalPort $EndpointNameRDPInt  | Update-AzureVM

    #Check VHD storage account, these should be in $newxiostorageaccountname
    Get-AzureVM -ServiceName $destcloudsvc -Name $vmName | Get-AzureDataDisk
    Get-AzureVM -ServiceName $destcloudsvc -Name $vmName |Get-AzureOSDisk


### <a name="create-a-new-vm-to-use-premium-storage-with-a-custom-image"></a>Crear una nueva máquina virtual para usar almacenamiento Premium con una imagen personalizada

Esta situación muestra que tenga imágenes personalizadas existentes que residen en una cuenta de almacenamiento estándar. Como se mencionó si desea colocar el sistema operativo de disco duro virtual de almacenamiento Premium debe copiar la imagen que existe en la cuenta de almacenamiento estándar y transferirlos a un almacenamiento Premium antes de poder usarlo. Si tiene una imagen local, también puede usar este método para copiar directamente a la cuenta de almacenamiento Premium.

#### <a name="step-1-create-storage-account"></a>Paso 1: Crear la cuenta de almacenamiento
    $mysubscription = "DansSubscription"
    $location = "West Europe"

    #Create Premium Storage account
    $newxiostorageaccountname = "danspremsams"
    New-AzureStorageAccount -StorageAccountName $newxiostorageaccountname -Location $location -Type "Premium_LRS"  

    #Standard Storage account
    $origstorageaccountname = "danstdams"

#### <a name="step-2-create-cloud-service"></a>Paso 2 Crear servicio de nube
    $destcloudsvc = "danNewSvcAms"
    New-AzureService $destcloudsvc -Location $location


#### <a name="step-3-use-existing-image"></a>Paso 3: Usar la imagen existente
Puede usar una imagen existente. O bien, puede [tomar una imagen de un equipo existente](virtual-machines-windows-classic-capture-image.md). Tenga en cuenta el equipo en la imagen no tiene que ser DS* equipo. Una vez que la imagen, los pasos siguientes muestran cómo copiar a la cuenta de almacenamiento Premium con la * *Inicio AzureStorageBlobCopy** commandlet de PowerShell.

    #Get storage account keys:
    #Standard Storage account
    $originalstorage =  Get-AzureStorageKey -StorageAccountName $origstorageaccountname
    #Premium Storage account
    $xiostorage = Get-AzureStorageKey -StorageAccountName $newxiostorageaccountname

    #Set up contexts for the storage accounts:
    $origContext = New-AzureStorageContext  –StorageAccountName $origstorageaccountname -StorageAccountKey $originalstorage.Primary
    $destContext = New-AzureStorageContext  –StorageAccountName $newxiostorageaccountname -StorageAccountKey $xiostorage.Primary  

#### <a name="step-4-copy-blob-between-storage-accounts"></a>Paso 4: Copiar Blob entre cuentas de almacenamiento
    #Get Image VHD
    $myImageVHD = "dansoldonorsql2k14-os-2015-04-15.vhd"
    $containerName = 'vhds'

    #Copy Blob between accounts
    $blob = Start-AzureStorageBlobCopy -SrcBlob $myImageVHD -SrcContainer $containerName `
    -DestContainer vhds -Destblob "prem-$myImageVHD" `
    -Context $origContext -DestContext $destContext  

#### <a name="step-5-regularly-check-copy-status"></a>Paso 5: Compruebe periódicamente Copiar estado:
    $blob | Get-AzureStorageBlobCopyState

#### <a name="step-6-add-image-disk-to-azure-disk-repository-in-subscription"></a>Paso 6: Agregar disco de imagen en el disco de Azure repositorio de suscripción
    $imageMediaLocation = $destContext.BlobEndPoint+"/"+$myImageVHD
    $newimageName = "prem"+"dansoldonorsql2k14"

    Add-AzureVMImage -ImageName $newimageName -MediaLocation $imageMediaLocation

> [AZURE.NOTE] Es posible que aunque los informes de estado correcto, todavía puede recibir un error de concesión de disco. En este caso, espere unos 10 minutos.

#### <a name="step-7--build-the-vm"></a>Paso 7: Crear la máquina virtual
Aquí se crea la máquina virtual de la imagen y asociar dos VHD de almacenamiento Premium:

    $newimageName = "prem"+"dansoldonorsql2k14"
    #Set up Machine Specific Information
    $vmName = "dansolchild"
    $vnet = "westeur"
    $subnet = "Clients"
    $ipaddr = "192.168.0.41"

    #This will need to be a new cloud service
    $destcloudsvc = "danregsvcamsxio2"

    #Use to DS Series VM
    $newInstanceSize = "Standard_DS1"

    #create new Avaiability Set
    $availabilitySet = "cloudmigAVAMS3"

    #Machine User Credentials
    $userName = "myadmin"
    $pass = "theM)stC0mplexP@ssw0rd!”


    #Create VM Config
    $vmConfigsl2 = New-AzureVMConfig -Name $vmName -InstanceSize $newInstanceSize -ImageName $newimageName  -AvailabilitySetName $availabilitySet  ` | Add-AzureProvisioningConfig -Windows ` -AdminUserName $userName -Password $pass | Set-AzureSubnet -SubnetNames $subnet | Set-AzureStaticVNetIP -IPAddress $ipaddr

    $vmConfigsl2 | Add-AzureDataDisk -CreateNew -DiskSizeInGB 1023 -LUN 0 -HostCaching "ReadOnly"  -DiskLabel "DataDisk1" -MediaLocation "https://$newxiostorageaccountname.blob.core.windows.net/vhds/$vmName-Datadisk-1.vhd"
    $vmConfigsl2 | Add-AzureDataDisk -CreateNew -DiskSizeInGB 1023 -LUN 1 -HostCaching "None"  -DiskLabel "LogDisk1" -MediaLocation "https://$newxiostorageaccountname.blob.core.windows.net/vhds/$vmName-logdisk-1.vhd"



    $vmConfigsl2 | New-AzureVM –ServiceName $destcloudsvc -VNetName $vnet

## <a name="existing-deployments-that-do-not-use-always-on-availability-groups"></a>Implementaciones existentes que no utilizan siempre en grupos de disponibilidad

> [AZURE.NOTE] Para las implementaciones existentes, primero consulte la sección [requisitos previos](#prerequisites-for-premium-storage) de este tema.

Existen diferentes consideraciones para las implementaciones de SQL Server que no utilizan siempre en grupos de disponibilidad y las que. Si no está utilizando siempre en y tiene una independiente de SQL Server, puede actualizar a Premium almacenamiento utilizando una cuenta nueva de almacenamiento y servicio de nube. Tenga en cuenta las siguientes opciones:

- **Crear una nueva máquina virtual de servidor de SQL**. Puede crear una nueva VM de servidor de SQL que usa una cuenta de almacenamiento Premium, tal como se describe en implementaciones de nuevo. A continuación, hacer una copia de seguridad y restaurar las bases de datos de usuario y la configuración de SQL Server. La aplicación necesitará actualizarse para hacer referencia a la nueva SQL Server si se tiene acceso interna o externamente. Debe copiar todos los objetos 'fuera de la base de datos' como si está realizando una migración de (verticales) de SQL Server en paralelo. Esto incluye objetos como inicios de sesión, certificados y servidores vinculados.
- **Migrar una máquina virtual existente SQL Server**. Se necesita desconectar la VM de SQL Server, a continuación, transferirla a un nuevo servicio de nube, lo que incluye copiar todos sus VHD adjunto a la cuenta de almacenamiento Premium. Cuando se conecta la máquina virtual, hará referencia a la aplicación el nombre de host del servidor como antes. Tenga en cuenta que el tamaño del disco existente afectará a las características de rendimiento. Por ejemplo, un disco de 400 GB obtiene redondea hacia arriba un P20. Si sabe que no requieren que rendimiento de disco, puede volver a crear la máquina virtual como una serie de DS máquina virtual y adjunte Premium VHD de almacenamiento de la especificación de tamaño y rendimiento que requiere. A continuación, podría desasociar y volver a adjuntar los archivos de base de datos de SQL.

> [AZURE.NOTE] Cuando copia de los discos de disco duro virtual que se deben tener en cuenta el tamaño, dependiendo del tamaño significará qué tipo de disco de almacenamiento de Premium se dividen en, determina la especificación de rendimiento de disco. El tamaño de Azure will redondear hasta el disco más próximo, por lo que si tiene un disco de 400 GB, esto se redondea hacia arriba a una P20. Según las necesidades de IO existentes del disco duro virtual de sistema operativo, no necesitará migrar esto a una cuenta de almacenamiento Premium.

Si se tiene acceso a SQL Server externamente, cambiará la dirección VIP de servicio de nube. También necesitará puntos finales de actualización, ACL y DNS configuración.

## <a name="existing-deployments-that-use-always-on-availability-groups"></a>Implementaciones existentes que usan siempre en grupos de disponibilidad

> [AZURE.NOTE] Para las implementaciones existentes, primero consulte la sección [requisitos previos](#prerequisites-for-premium-storage) de este tema.

Principio de esta sección veremos cómo siempre en interactúe con redes de Azure. A continuación, se le desglosar migraciones en dos escenarios: donde puede tolerarse el tiempo de inactividad de migraciones y migraciones donde debe lograr tiempo de inactividad mínimo.

Local SQL Server siempre en disponibilidad de grupos de usar una escucha local que registra un nombre DNS virtual junto con una dirección IP que se comparte entre uno o más servidores de SQL Server. Cuando se conectan los clientes que se enrutan a través de la dirección IP de escucha con el servidor SQL principal. Este es el servidor que posee el recurso siempre IP en ese momento.

![DeploymentsUseAlways en][6]

En Microsoft Azure que puede tener asignada a una NIC en la máquina virtual solo una dirección IP, así que para lograr la misma capa de abstracción como local, Azure utiliza la dirección IP que se asigna a los equilibradores de carga internas y externas (ILB/ELB). El recurso IP que se comparte entre los servidores se establece en la misma IP como la ILB/ELB. Esto se publica en el DNS y tráfico del cliente se pasa a través de la ILB/ELB la réplica principal SQL Server. El ILB/ELB sabe que SQL Server es principal, ya que utiliza sondeos para sondeo el recurso siempre IP. En el ejemplo anterior, sondeos de cada nodo que tiene un extremo al que hace referencia la ELB/ILB, lo que responde es el principal de SQL Server.

> [AZURE.NOTE] La ILB y ELB se asignan a un servicio de nube de Azure determinado, por lo tanto, cualquier migración a la nube en Azure probablemente significa que va a cambiar la dirección IP del equilibrador de carga.

### <a name="migrating-always-on-deployments-that-can-allow-some-downtime"></a>Migrar implementaciones siempre que se pueden permitir el tiempo de inactividad

Hay dos estrategias para migrar siempre en implementaciones que permiten el tiempo de inactividad:

1. **Agregar más réplicas secundarias a un siempre en clúster existente**
1. **Migrar a un nuevo siempre en clúster**

#### <a name="1-add-more-secondary-replicas-to-an-existing-always-on-cluster"></a>1. agregar más réplicas secundario a un siempre en clúster existente

Una estrategia consiste en agregar más secundarios al siempre en disponibilidad de grupo. Debe agregar estos en un nuevo servicio de nube y actualizar la escucha con el nuevo IP del equilibrador de carga.

##### <a name="points-of-downtime"></a>Puntos de inactividad:

- Validación de clúster.
- Probar siempre en migraciones tras error secundarios de nuevo.

Si está utilizando agrupaciones de almacenamiento de Windows dentro de la máquina virtual para un mayor rendimiento IO, a continuación, estos se desconectarán durante la validación de clúster completa. La prueba de validación es necesaria cuando agregue nodos al clúster. El tiempo necesario para ejecutar la prueba puede variar, por lo que debe probar en su entorno de prueba representante para obtener un tiempo aproximado de cuánto tiempo tardará.

Debe aprovisionar tiempo donde puede realizar migración manual y caos pruebas en los nodos recién agregados para asegurar siempre en alta disponibilidad funciona según lo esperado.

![DeploymentUseAlways On2][7]

> [AZURE.NOTE] Debe detener todas las instancias de SQL Server donde se utilizan los grupos de almacenamiento antes de la ejecución de validación.
##### <a name="high-level-steps"></a>Pasos de alto nivel

1. Crear dos nuevos servidores de SQL en el nuevo servicio de nube con almacenamiento Premium conectado.
1. Copiar a través de copias de seguridad completas y restaurar con **NORECOVERY**.
1. Copiar a través 'fuera del usuario DB' objetos dependientes, como inicios de sesión etcetera.
1. Crear un nuevo equilibrador de carga interno (ILB) o use un equilibrador de carga externo (ELB) y luego configurar extremos de equilibrio de carga en ambos nodos nuevos.
> [AZURE.NOTE] Comprobar que todos los nodos tienen la configuración correcta del extremo antes de continuar

1. Impedir el acceso de usuario/aplicación a SQL Server (si usa agrupaciones de almacenamiento).
1. Detener servicios del motor de SQL Server en todos los nodos (si utiliza los grupos de almacenamiento).
1. Agregar nuevos nodos para clúster y ejecutar validación completa.
1. Una vez validación es correcta, inicie todos los servicios de SQL Server.
1. Registros de transacciones de la copia de seguridad y restaurar bases de datos de usuario.
1. Agregar nuevos nodos al siempre en disponibilidad de grupo y coloque replicación en **sincrónico**.
1. Agregar el recurso de dirección IP de la nueva nube servicio ILB/ELB a través de PowerShell para siempre en basándose en el ejemplo de varios sitios en el [Apéndice](#appendix-migrating-a-multisite-alwayson-cluster-to-premium-storage). En el clúster de Windows, establezca los **posibles propietarios** del recurso **Dirección IP** en los nuevos nodos antiguos. Consulte la sección 'Agregar recurso de dirección IP en la misma subred' del [Apéndice](#appendix-migrating-a-multisite-alwayson-cluster-to-premium-storage).
1. Migración tras error a uno de los nodos nuevos.
1. Asegúrese de los nuevos nodos asociados de migración tras error automática y a prueba de migraciones tras error.
1. Quitar nodos originales del grupo de disponibilidad.

##### <a name="advantages"></a>Ventajas

- Nuevos servidores SQL puede ser probado (SQL Server y aplicación) antes de que se hayan agregado a siempre.
- Puede cambiar el tamaño de la máquina virtual y personalizar el almacenamiento a sus necesidades. Sin embargo, sería conveniente mantener las rutas de acceso SQL de la misma.
- Puede controlar cuando se inicia la transferencia de las copias de seguridad de base de datos a las réplicas secundario. Esto difiere del uso de Azure **Inicio AzureStorageBlobCopy** commandlet para copiar VHD, ya que es una copia asincrónica.

##### <a name="disadvantages"></a>Desventajas
- Al usar grupos de almacenamiento de Windows, hay tiempo de inactividad de clúster durante la validación de clúster completa para los nuevos nodos adicionales.
- Dependiendo de la versión de SQL Server y el número de réplicas secundarios existente, no es posible que pueda agregar más réplicas secundarias sin eliminar secundarios existentes.
- Puede haber mucho tiempo de transferencia de datos SQL al configurar los secundarios.
- Hay costo adicional durante la migración mientras tiene nuevos equipos que se ejecutan en paralelo.

#### <a name="2-migrate-to-a-new-always-on-cluster"></a>2. migrar a un nuevo siempre en clúster

Otra estrategia es crear un nuevo siempre en clúster con nodos nuevos en servicio de nube de nuevo y, a continuación, redirigir los clientes para usarlo.

##### <a name="points-of-downtime"></a>Puntos de tiempo de inactividad

Hay tiempo de inactividad al transferir a la nueva escucha siempre en aplicaciones y los usuarios. El tiempo de inactividad depende de:

- El tiempo necesario para restaurar las copias de seguridad del registro de transacciones final a bases de datos nueva.
- El tiempo invertido en actualizar aplicaciones cliente usar nueva siempre en escucha.

##### <a name="advantages"></a>Ventajas

- Puede probar el entorno de producción real, SQL Server, y OS compilar los cambios.
- Tiene la opción para personalizar el almacenamiento y para reducir el tamaño de la máquina virtual. Esto podría provocar reducción de costos.
- Puede actualizar su versión de SQL Server o versión durante este proceso. También puede actualizar el sistema operativo.
- El anterior siempre en clúster pueden actuar como destino deshacer sólido.

##### <a name="disadvantages"></a>Desventajas

- Debe cambiar el nombre DNS de la escucha si desea que ambos clústeres siempre en ejecución simultáneamente. Esto agrega administración sobrecarga durante la migración, como las cadenas de la aplicación de cliente deben reflejar el nuevo nombre de escucha.
- Debe implementar un mecanismo de sincronización entre los dos entornos para mantenerlas más cerca posible minimizar los requisitos de sincronización final antes de la migración.
- Se agrega costo durante la migración mientras se ejecuta el entorno de nuevo.

### <a name="migrating-always-on-deployments-for-minimal-downtime"></a>Migrar siempre en implementaciones de tiempo de inactividad mínimo

Hay dos estrategias para migrar siempre en implementaciones de tiempo de inactividad mínimo:

1. **Utilizar un secundario existente: solo sitio**
1. **Utilizar réplicas secundario existente: múltiples sitios**

#### <a name="1-utilize-an-existing-secondary-single-site"></a>1. utilizar un secundario existente: solo sitio

Una estrategia de tiempo de inactividad mínimo es tomar una nube existente secundario y quitar del servicio de nube actual. A continuación, copie los VHD a la nueva cuenta de almacenamiento Premium y crear la máquina virtual en el nuevo servicio de nube. A continuación, actualice el oyente en clústeres y migración tras error.

##### <a name="points-of-downtime"></a>Puntos de tiempo de inactividad

- Hay tiempo de inactividad cuando se actualiza el nodo final con el extremo de equilibrio de carga.
- Su volver cliente podría retrasarse dependiendo de la configuración de cliente y DNS.
- No hay tiempo de inactividad adicional si decide poner sin conexión el grupo de siempre clúster cambiar las direcciones IP. Se puede evitar mediante una dependencia OR y posibles propietarios para el recurso de dirección IP agregado. Consulte la sección 'Agregar recurso de dirección IP en la misma subred' del [Apéndice](#appendix-migrating-a-multisite-alwayson-cluster-to-premium-storage).

> [AZURE.NOTE] Si desea que el nodo agregado a partake en como siempre en migración tras error Partner, debe agregar un extremo de Azure con una referencia para el conjunto de equilibrio de carga. Cuando se ejecuta el comando **Agregar AzureEndpoint** para hacer esto, conexiones actuales permanezca abiertas pero nuevas conexiones a la escucha no podrá establecerse hasta que haya actualizado el equilibrador de carga. En esta prueba se ha visto a última 90-120 segundos, se debe probar.

##### <a name="advantages"></a>Ventajas

- Sin adicional gastos durante la migración.
- Una migración de uno a uno.
- Reducir la complejidad.
- Admite IOPS mayor de Premium almacenamiento SKU. Cuando los discos se separa de la máquina virtual y ha copiado en el nuevo servicio de nube, un 3 º herramienta puede utilizarse para aumentar el tamaño del disco duro virtual, que proporciona un mayor rendimiento. Para aumentar el tamaño del disco duro virtual, consulte este [foro de debate](https://social.msdn.microsoft.com/Forums/azure/4a9bcc9e-e5bf-4125-9994-7c154c9b0d52/resizing-azure-data-disk?forum=WAVirtualMachinesforWindows).

##### <a name="disadvantages"></a>Desventajas

- Hay una pérdida temporal de alta disponibilidad y DR durante la migración.
- Como se trata de una migración de 1:1, tendrá que utilizar un tamaño mínimo de VM que admite su número de VHD, por lo que no podrá downsize sus máquinas virtuales.
- Este escenario usaría commandlet de Azure **AzureStorageBlobCopy de inicio** , que es asincrónica. No hay ningún SLA de finalización de la copia. El tiempo de las copias varía, aunque depende de espera en cola también dependerá de la cantidad de datos para transferir. El tiempo de copiar aumenta si la transferencia va a otro centro de datos de Azure que admite el almacenamiento de Premium en otra región. Si tiene 2 nodos, tenga en cuenta una posible mitigación en caso de que la copia lleva más en las pruebas. Esto puede incluir las siguientes ideas.
    - Agregar un nodo de SQL Server 3ª temporal para alta disponibilidad antes de la migración con el tiempo de inactividad acordado.
    - Ejecutar la migración fuera de Azure mantenimiento programado.
    - Asegúrese de que ha configurado el quórum de clúster correctamente.  

##### <a name="high-level-steps"></a>Pasos de alto nivel

Este documento no muestra un ejemplo completo de llevar a cabo, el [Apéndice](#appendix-migrating-a-multisite-alwayson-cluster-to-premium-storage) proporciona detalles que pueden aprovecharse para realizar esta operación.

![MinimalDowntime][8]

- Recopilar la configuración del disco y quitar el nodo (no elimine VHD adjuntos).
- Crear una cuenta de almacenamiento Premium y copie VHD de la cuenta de almacenamiento estándar
- Crear nuevo servicio de nube y volver a la VM SQL2 en ese servicio de nube. Crear la máquina virtual usando la copia original OS disco duro virtual y adjuntar el VHD copiados.
- Configurar ILB / ELB y agregar los extremos.
- Escucha de actualización, ya sea por:
    - Desconectar el siempre en grupo y actualizar la siempre en escucha con ILB nuevo / dirección IP ELB.
    - O agregando el recurso de dirección IP de nuevo nube servicio ILB/ELB a través de PowerShell en clústeres de Windows. A continuación, establezca los posibles propietarios del recurso Dirección IP de nodo migrados, SQL2 y esto como dependencia o el nombre de red. Consulte la sección 'Agregar recurso de dirección IP en la misma subred' del [Apéndice](#appendix-migrating-a-multisite-alwayson-cluster-to-premium-storage).
- Compruebe la configuración de DNS/propagación a los clientes.
- Migrar SQL1 VM y vaya a través de los pasos del 2 al 4.
- Si usa pasos 5ii, a continuación, agregue SQL1 como posible propietario para el recurso de dirección IP agregada
- Probar migraciones tras error.

#### <a name="2-utilize-existing-secondary-replicas-multi-site"></a>2. utilizar réplicas secundario existente: múltiples sitios

Si tiene nodos en más de un centro de datos Azure (CC) o si tiene un entorno híbrido, puede usar una configuración siempre en este entorno para minimizar el tiempo de inactividad.

El enfoque consiste en cambiar la sincronización siempre a sincrónico en el local o DC de Azure secundario y, a continuación, por error sobre a ese servidor de SQL. A continuación, copiar los VHD a una cuenta de almacenamiento Premium y volver a implementar el equipo en un nuevo servicio de nube. Actualizar la escucha y, a continuación, se produce de nuevo.

##### <a name="points-of-downtime"></a>Puntos de tiempo de inactividad

El tiempo de inactividad consiste en el tiempo de migración tras error para el DC alternativo y al revés. También depende de la configuración de cliente/DNS y puede retrasarse su volver de cliente.
Tenga en cuenta el siguiente ejemplo de una configuración híbrida siempre en:

![MultiSite1][9]

##### <a name="advantages"></a>Ventajas

- Puede utilizar la infraestructura existente.
- Tiene la opción de actualizar previamente el almacenamiento de Azure en el controlador de dominio de Azure DR en primer lugar.
- Se puede modificar el almacenamiento de Azure DR DC.
- Hay un mínimo de dos migraciones tras error durante la migración, excluyendo las migraciones tras error de prueba.
- No es necesario mover los datos de SQL Server con copia de seguridad y restauración.

##### <a name="disadvantages"></a>Desventajas

- Función de acceso de cliente a SQL Server, puede haber mayor latencia cuando se ejecuta SQL Server en un controlador de dominio alternativo a la aplicación.
- El tiempo de copia de VHD al almacenamiento Premium podría ser largo. Esto puede afectar a su decisión si desea conservar el nodo en el grupo de disponibilidad. Considere esto para cuando se están ejecutando cargas durante la migración de trabajo intensivo registro es necesario, ya que tendrá el nodo principal mantener las transacciones sin duplicar en su registro de transacciones. Por lo tanto, esto puede aumentar significativamente.
- Este escenario usaría commandlet de Azure **AzureStorageBlobCopy de inicio** , que es asincrónica. No hay ningún SLA de finalización. El tiempo de las copias varía, aunque depende de espera en cola, también depende de la cantidad de datos para transferir. Por tanto, tiene un nodo en el centro de datos 2 º, debe seguir los pasos de mitigación en caso de que la copia lleva más en las pruebas. Esto puede incluir las siguientes ideas.
    - Agregar un nodo SQL 2 º temporal para alta disponibilidad antes de la migración con el tiempo de inactividad acordado.
    - Ejecutar la migración fuera de Azure mantenimiento programado.
    - Asegúrese de que ha configurado el quórum de clúster correctamente.

Este escenario se asume que ha documentado su instalación y saber cómo se asigna el almacenamiento para realizar cambios de configuración de la caché de disco óptimas.

##### <a name="high-level-steps"></a>Pasos de alto nivel
![Multisite2][10]

- Realice la local / alternar la principal de servidor de SQL Azure DC y realizar los otros automática migración tras error asociado (AFP).
- Recopile información de configuración de disco de SQL2 y quitar el nodo (no elimine VHD adjuntos).
- Crear una cuenta de almacenamiento Premium y copie VHD de la cuenta de almacenamiento estándar.
- Cree un nuevo servicio de nube y la VM SQL2 con sus discos de almacenamiento de bonificaciones adjuntados.
- Configurar ILB / ELB y agregar los extremos.
- Actualizar la siempre en escucha con ILB nuevo / failover de prueba y la dirección IP ELB.
- Compruebe la configuración de DNS.
- Cambiar la AFP a SQL2 y migrar SQL1 y vaya a través de los pasos 2 a 5.
- Probar migraciones tras error.
- Cambie la AFP a SQL1 y SQL2

## <a name="appendix-migrating-a-multisite-always-on-cluster-to-premium-storage"></a>Apéndice: Migrar un multisitio siempre en clúster al almacenamiento Premium

El resto de este tema ofrece un ejemplo detallado de convertir un clúster de siempre en varios sitios con el almacenamiento de Premium. Convierte al oyente desde mediante un equilibrador de carga externo (ELB) a un equilibrador de carga interno (ILB).

### <a name="environment"></a>Entorno

- Windows 2k 12 / SQL 2k 12
- Archivos de base de datos 1 en SP
- 2 x agrupaciones de almacenamiento por nodo

![Appendix1][11]

### <a name="vm"></a>VM:

En este ejemplo, vamos a demostrar moviéndose desde un ELB a ILB. ELB estaba disponible antes ILB, por lo que muestra cómo cambiar este durante la migración.

![Appendix2][12]

### <a name="pre-steps-connect-to-subscription"></a>Los pasos anteriores: Conectar con suscripción

    Add-AzureAccount

    #Set up subscription
    Get-AzureSubscription

#### <a name="step-1-create-new-storage-account-and-cloud-service"></a>Paso 1: Crear la nueva cuenta de almacenamiento y servicio de nube
    $mysubscription = "DansSubscription"
    $location = "West Europe"

    #Storage accounts
    #current storage account where the vm to migrate resides
    $origstorageaccountname = "danstdams"

    #Create Premium Storage account
    $newxiostorageaccountname = "danspremsams"
    New-AzureStorageAccount -StorageAccountName $newxiostorageaccountname -Location $location -Type "Premium_LRS"  

    #Generate storage keys for later
    $originalstorage =  Get-AzureStorageKey -StorageAccountName $origstorageaccountname
    $xiostorage = Get-AzureStorageKey -StorageAccountName $newxiostorageaccountname

    #Generate storage acc contexts
    $origContext = New-AzureStorageContext  –StorageAccountName $origstorageaccountname -StorageAccountKey $originalstorage.Primary
    $xioContext = New-AzureStorageContext  –StorageAccountName $newxiostorageaccountname -StorageAccountKey $xiostorage.Primary  

    #Set up subscription and default storage account
    Set-AzureSubscription -SubscriptionName $mysubscription -CurrentStorageAccount $origstorageaccountname
    Select-AzureSubscription -SubscriptionName $mysubscription -Current

    #CREATE NEW CLOUD SVC
    $vnet = "dansvnetwesteur"

    ##Existing cloud service
    $sourceSvc="dansolSrcAms"

    ##Create new cloud service
    $destcloudsvc = "danNewSvcAms"
    New-AzureService $destcloudsvc -Location $location

#### <a name="step-2-increase-the-permitted-failures-on-resources-optional"></a>Paso 2: Aumentar los errores permitidos en los recursos<Optional>
En algunos recursos que pertenecen a su siempre en grupo disponibilidad existen límites para cuántos errores que pueden ocurrir en un período, donde el servicio de clúster intentará reiniciar el grupo de recursos. Se recomienda que aumente mientras se recorrer este procedimiento desde si manualmente no migraciones tras error de migración tras error y desencadenador, cerrando máquinas que puede obtener próximo a alcanzar este límite.

Es aconsejable doble la amortización por error, para hacer esto en el Administrador de clúster de migración tras error, vaya a las propiedades del grupo de recursos siempre en:

![Appendix3][13]

Cambiar los errores máximos a 6.

#### <a name="step-3-addition-ip-address-resource-for-cluster-group-optional"></a>Paso 3: Recurso de dirección IP de adición para el grupo de clúster<Optional>

Si tiene una única dirección IP para el grupo de clúster y esto se alinea a la subred de la nube, tenga en cuenta, si accidentalmente desconectar todos los nodos en la nube de la red, a continuación, el recurso de IP de clúster y nombre de red clúster no podrá conectarse. En caso de que esto impedirá que las actualizaciones a otros recursos de clúster.

#### <a name="step-4-dns-configuration"></a>Paso 4: Configuración de DNS

Implementar un suave transición depende de cómo se está DNS utilizan y actualizado.
Cuando se instala siempre, crea un grupo de recursos de clúster de Windows, si se abre el Administrador de clúster de migración tras error, verá que tiene tres recursos como mínimo, los dos que hace referencia el documento:

- Nombre de red virtual (VNN): este es el nombre DNS que el cliente conectarse a cuando desee conectarse a los servidores de SQL Server a través de siempre.
- Recurso de dirección IP: esta es la dirección IP asociada a la VNN, puede tener más de uno y, en una configuración multisitio tendrá una dirección IP por subred del sitio.

Cuando se conecta a SQL Server, el cliente de SQL Server recuperará los registros DNS asociados con la escucha e intenta conectar con cada siempre en el controlador de asocia dirección IP, debajo de trataremos algunos factores que pueden influir en esto.

El número de registros DNS simultáneos que están asociados con el nombre de la escucha no solo depende del número de direcciones IP asociadas, pero la ' RegisterAllIpProviders'setting en el clúster de migración tras error para el recurso siempre activado VNN.

Cuando se implementa siempre en Azure existen varios pasos para crear la escucha y las direcciones IP, debe configurar manualmente 'RegisterAllIpProviders' 1, esto es distinto a una instalación local en implementación siempre en donde ya está establecido en 1.

Si 'RegisterAllIpProviders' es 0, solo verá uno registro DNS en DNS asociado con la escucha:

![Appendix4][14]

Si 'RegisterAllIpProviders' es 1:

![Appendix5][15]

El siguiente código de descarga de la configuración de VNN y establecido por usted, tenga en cuenta, para que el cambio surta efecto, deberá desconectar el VNN y activar de nuevo en línea, este teniendo escucha sin conexión causando interrupciones de conectividad de cliente.

    ##Always On Listener Name
    $ListenerName = "Mylistener"
    ##Get AlwaysOn Network Name Settings
    Get-ClusterResource $ListenerName| Get-ClusterParameter
    ##Set RegisterAllProvidersIP
    Get-ClusterResource $ListenerName| Set-ClusterParameter RegisterAllProvidersIP  1

En un paso posterior de migración debe actualizar siempre en escucha con una dirección IP actualizada que hacen referencia a un equilibrador de carga, esto implica una adición y eliminación de recursos de dirección IP. Tras la actualización IP, debe asegurarse de la nueva dirección IP se ha actualizado en la zona DNS y que los clientes están actualizando su caché local de DNS.

Si sus clientes residen en un segmento de red diferente y hacer referencia a un servidor DNS diferente, necesitará tener en cuenta lo que sucede con transferencia de zona DNS durante la migración, como volver la aplicación tiempo estará restringido por al menos el tiempo de transferencia de zona de las nuevas direcciones IP para la escucha. Si está en la restricción de tiempo aquí, debe discutir y probar forzar una transferencia de zona incremental con sus equipos de Windows y poner también el registro de host DNS a una inferior período de vida (TTL), así que los clientes actualizan. Para obtener más información, vea [Transferencias de zona incrementales](https://technet.microsoft.com/library/cc958973.aspx) y [DnsServerZoneTransfer inicio](https://technet.microsoft.com/library/jj649917.aspx).

De forma predeterminada, el valor de TTL de registro DNS que está asociado a la escucha en siempre en Azure es 1200 segundos. Desea reducir esto si se encuentra en tiempo de restricción durante la migración para asegurarse de los clientes actualizar sus DNS con la dirección IP actualizada para la escucha. Puede ver y modificar la configuración mediante el volcado de la configuración de la VNN:

    $AGName = "myProductionAG"
    $ListenerName = "Mylistener"
    #Look at HostRecordTTL
    Get-ClusterResource $ListenerName| Get-ClusterParameter

    #Set HostRecordTTL Examples
    Get-ClusterResource $ListenerName| Set-ClusterParameter -Name "HostRecordTTL" 120

Tenga en cuenta que cuanto menor sea el 'HostRecordTTL,' se producirá una mayor cantidad de tráfico DNS.

##### <a name="client-application-settings"></a>Configuración de la aplicación de cliente

Si la aplicación de cliente SQL admite .net 4.5 SQLClient, puede usar ' MULTISUBNETFAILOVER = TRUE' palabra clave, se recomienda que se aplique ya que permite la conexión más rápida a SQL siempre en grupo de disponibilidad durante la migración tras error. Enumera todas las direcciones IP asociadas a la escucha siempre en paralelo y realiza una velocidad de intentos de conexión TCP más rigurosa durante un error.

Para obtener más información acerca de la configuración anterior, vea [palabras clave de MultiSubnetFailover y las características asociadas](https://msdn.microsoft.com/library/hh213080.aspx#MultiSubnetFailover). Vea también [Compatibilidad de SqlClient con alta disponibilidad, recuperación](https://msdn.microsoft.com/library/hh205662(v=vs.110).aspx).

#### <a name="step-5-cluster-quorum-settings"></a>Paso 5: Configuración de quórum de clúster

Como va a tener al menos un SQL Server hacia abajo a la vez, debe modificar la configuración de quórum de clúster, si usa auxiliar de recurso compartido de archivos (FSW) con 2 nodos, debe establecer el quórum permitir la mayoría de nodo y utilizar voto dinámico y este es para permitir que un único nodo permanezca permanente.


    Set-ClusterQuorum -NodeMajority  

Para obtener más información sobre la administración y configurar el quórum del clúster, vea [configurar y administrar el quórum en un clúster de migración tras error de Windows Server 2012](https://technet.microsoft.com/library/jj612870.aspx).

#### <a name="step-6-extract-existing-endpoints-and-acls"></a>Paso 6: Extraer ACL y extremos existentes
    #GET Endpoint info
    Get-AzureVM -ServiceName $destcloudsvc -Name $vmNameToMigrate | Get-AzureEndpoint
    #GET ACL Rules for Each EP, this example is for the Always On Endpoint
    Get-AzureVM -ServiceName $destcloudsvc -Name $vmNameToMigrate | Get-AzureAclConfig -EndpointName "myAOEndPoint-LB"  

Guardar a un archivo de texto.

#### <a name="step-7-change-failover-partners-and-replication-modes"></a>Paso 7: Cambiar los modos de replicación y socios de migración tras error

Si tiene más de 2 servidores de SQL Server, debe cambiar la migración tras error del otro secundario en otro DC o local a 'Sincrónico' y darle un Partner de migración tras error automática (AFP), por lo que mantener HA mientras se realizan cambios. Puede hacerlo a través de TSQL de modificar aunque SSMS:

![Appendix6][16]

#### <a name="step-8-remove-secondary-vm-from-cloud-service"></a>Paso 8: Quitar VM secundario de servicio de nube

Debe ser planea migrar un nodo secundario de la nube en primer lugar, si se trata de actualmente principal, debe iniciar un error manual.

    $vmNameToMigrate="dansqlams2"

    #Check machine status
    Get-AzureVM -ServiceName $sourceSvc -Name $vmNameToMigrate

    #Shutdown secondary VM
    Get-AzureVM -ServiceName $sourceSvc -Name $vmNameToMigrate | stop-AzureVM


    #Extract disk configuration

    ##Building Existing Data Disk Configuration
    $file = "C:\Azure Storage Testing\mydiskconfig_$vmNameToMigrate.csv"
    $datadisks = @(Get-AzureVM -ServiceName $sourceSvc -Name $vmNameToMigrate | Get-AzureDataDisk )
    Add-Content $file “lun, vhdname, hostcaching, disklabel, diskName”
    foreach ($disk in $datadisks)
    {
      $vhdname = $disk.MediaLink.AbsolutePath -creplace  "/vhds/"
      $disk.Lun, , $disk.HostCaching, $vhdname, $disk.DiskLabel,$disks.DiskName
    # Write-Host "copying disk $disk"
    $adddisk = "{0},{1},{2},{3},{4}" -f $disk.Lun,$vhdname, $disk.HostCaching, $disk.DiskLabel, $disk.DiskName
    $adddisk | add-content -path $file
    }

    #Get OS Disk
    $osdisks = Get-AzureVM -ServiceName $sourceSvc -Name $vmNameToMigrate | Get-AzureOSDisk ## | select -ExpandProperty MediaLink
    $osvhdname = $osdisks.MediaLink.AbsolutePath -creplace  "/vhds/"
    $osdisks.OS, $osdisks.HostCaching, $osvhdname, $osdisks.DiskLabel, $osdisks.DiskName
    $addosdisk = "{0},{1},{2},{3},{4}" -f $osdisks.OS,$osvhdname, $osdisks.HostCaching, $osdisks.Disklabel , $osdisks.DiskName
    $addosdisk | add-content -path $file

    #Import disk config
    $diskobjects  = Import-CSV $file

    #Check disk config, make sure below returns the disks associated with the VM
    $diskobjects

    #Identify OS Disk
    $osdiskimport = $diskobjects | where {$_.lun -eq "Windows"}
    $osdiskforbuild = $osdiskimport.diskName

    #Check machibe is off
    Get-AzureVM -ServiceName $sourceSvc -Name  $vmNameToMigrate

    #Drop machine and rebuild to new cls
    Remove-AzureVM -ServiceName $sourceSvc -Name $vmNameToMigrate

#### <a name="step-9-change-disk-caching-settings-in-csv-file-and-save"></a>Paso 9: Cambiar la configuración en el archivo CSV de caché de disco y guardar

Volúmenes de datos de estos se deben establecer en sólo lectura.

Para volúmenes de registro de transacciones estos deben establecer en ninguna.

![Appendix7][17]

#### <a name="step-10-copy-vhds"></a>Paso 10: Copiar VHD
    #Ensure you have created the container for these:
    $containerName = 'vhds'

    #Create container
    New-AzureStorageContainer -Name $containerName -Context $xioContext

    ####DISK COPYING####
    #Get disks from csv, get settings for each VHDs and copy to Premium Storage accoun
    ForEach ($disk in $diskobjects)
       {
       $lun = $disk.Lun
       $vhdname = $disk.vhdname
       $cacheoption = $disk.HostCaching
       $disklabel = $disk.DiskLabel
       $diskName = $disk.DiskName
       Write-Host "Copying Disk Lun $lun, Label : $disklabel, VHD : $vhdname has cache setting : $cacheoption"

       #Start async copy
       Start-AzureStorageBlobCopy -srcUri "https://$origstorageaccountname.blob.core.windows.net/vhds/$vhdname" `
    -SrcContext $origContext `
    -DestContainer $containerName `
    -DestBlob $vhdname `
    -DestContext $xioContext
       }



Puede comprobar el estado de la copia de los VHD a la cuenta de almacenamiento Premium:

    ForEach ($disk in $diskobjects)
       {
       $lun = $disk.Lun
       $vhdname = $disk.vhdname
       $cacheoption = $disk.HostCaching
       $disklabel = $disk.DiskLabel
       $diskName = $disk.DiskName

       $copystate = Get-AzureStorageBlobCopyState -Blob $vhdname -Container $containerName -Context $xioContext
    Write-Host "Copying Disk Lun $lun, Label : $disklabel, VHD : $vhdname, STATUS = " $copystate.Status
       }

![Appendix8][18]

Espere a que todos ellos se registran como correcto.

Para obtener información para blobs individuales:

    Get-AzureStorageBlobCopyState -Blob "blobname.vhd" -Container $containerName -Context $xioContext

#### <a name="step-11-register-os-disk"></a>Paso 11: Disco de registrar OS

    #Change storage account
    Set-AzureSubscription -SubscriptionName $mysubscription -CurrentStorageAccount $newxiostorageaccountname
    Select-AzureSubscription -SubscriptionName $mysubscription -Current

    #Register OS disk
    $osdiskimport = $diskobjects | where {$_.lun -eq "Windows"}
    $osvhd = $osdiskimport.vhdname
    $osdiskforbuild = $osdiskimport.diskName

    #Registering OS disk, but as XIO disk
    $xioDiskName = $osdiskforbuild + "xio"
    Add-AzureDisk -DiskName $xioDiskName -MediaLocation  "https://$newxiostorageaccountname.blob.core.windows.net/vhds/$osvhd"  -Label "BootDisk" -OS "Windows"

#### <a name="step-12-import-secondary-into-new-cloud-service"></a>Paso 12: Importar secundario en el nuevo servicio de nube

El código siguiente también usa la opción adicional aquí puede importar el equipo y usar la dirección VIP conservable.

    #Build VM Config
    $ipaddr = "192.168.0.5"
    #Remember to change to XIO
    $newInstanceSize = "Standard_DS13"
    $subnet = "SQL"

    #Create new Avaiability Set
    $availabilitySet = "cloudmigAVAMS"

    #build machine config into object
    $vmConfig = New-AzureVMConfig -Name $vmNameToMigrate -InstanceSize $newInstanceSize -DiskName $xioDiskName -AvailabilitySetName $availabilitySet  ` | Add-AzureProvisioningConfig -Windows ` | Set-AzureSubnet -SubnetNames $subnet | Set-AzureStaticVNetIP -IPAddress $ipaddr

    #Reload disk config
    $diskobjects  = Import-CSV $file
    $datadiskimport = $diskobjects | where {$_.lun -ne "Windows"}

    ForEach ( $attachdatadisk in $datadiskimport)
       {
    $label = $attachdatadisk.disklabel
    $lunNo = $attachdatadisk.lun
    $hostcach = $attachdatadisk.hostcaching
    $datadiskforbuild = $attachdatadisk.diskName
    $vhdname = $attachdatadisk.vhdname

    ###Attaching disks to a VM during a deploy to a new cloud service and new storage account is different from just attaching VHDs to just a redeploy in a new cloud service
    $vmConfig | Add-AzureDataDisk -ImportFrom -MediaLocation "https://$newxiostorageaccountname.blob.core.windows.net/vhds/$vhdname" -LUN $lunNo -HostCaching $hostcach -DiskLabel $label

    }

    #Create VM
    $vmConfig  | New-AzureVM –ServiceName $destcloudsvc –Location $location -VNetName $vnet ## Optional (-ReservedIPName $reservedVIPName)

#### <a name="step-13-create-ilb-on-new-cloud-svc-add-load-balanced-endpoints-and-acls"></a>Paso 13: Crear ILB en Svc nube nuevo, agregar carga equilibrada extremos y ACL
    #Check for existing ILB
    GET-AzureInternalLoadBalancer -ServiceName $destcloudsvc

    $ilb="sqlIntIlbDest"
    $subnet = "SQL"
    $IP="192.168.0.25"
    Add-AzureInternalLoadBalancer -ServiceName $destcloudsvc -InternalLoadBalancerName $ilb –SubnetName $subnet –StaticVNetIPAddress $IP

    #Endpoints
    $epname="sqlIntEP"
    $prot="tcp"
    $locport=1433
    $pubport=1433
    Get-AzureVM –ServiceName $destcloudsvc –Name $vmNameToMigrate  | Add-AzureEndpoint -Name $epname -Protocol $prot -LocalPort $locport -PublicPort $pubport -ProbePort 59999 -ProbeIntervalInSeconds 5 -ProbeTimeoutInSeconds 11  -ProbeProtocol "TCP" -InternalLoadBalancerName $ilb -LBSetName $ilb -DirectServerReturn $true | Update-AzureVM

    #SET Azure ACLs or Network Security Groups & Windows FWs

    #http://msdn.microsoft.com/library/azure/dn495192.aspx

    ####WAIT FOR FULL AlwaysOn RESYNCRONISATION!!!!!!!!!#####

####<a name="step-14-update-always-on"></a>Paso 14: Actualizar siempre en
    #Code to be executed on a Cluster Node
    $ClusterNetworkNameAmsterdam = "Cluster Network 2" # the azure cluster subnet network name
    $newCloudServiceIPAmsterdam = "192.168.0.25" # IP address of your cloud service

    $AGName = "myProductionAG"
    $ListenerName = "Mylistener"


    Add-ClusterResource "IP Address $newCloudServiceIPAmsterdam" -ResourceType "IP Address" -Group $AGName -ErrorAction Stop |  Set-ClusterParameter -Multiple @{"Address"="$newCloudServiceIPAmsterdam";"ProbePort"="59999";SubnetMask="255.255.255.255";"Network"=$ClusterNetworkNameAmsterdam;"OverrideAddressMatch"=1;"EnableDhcp"=0} -ErrorAction Stop

    #set dependancy and NETBIOS, then remove old IP address

    #set NETBIOS, then remove old IP address
    Get-ClusterGroup $AGName | Get-ClusterResource -Name "IP Address $newCloudServiceIPAmsterdam" | Set-ClusterParameter -Name EnableNetBIOS -Value 0

    #set dependency to Listener (OR Dependency) and delete previous IP Address resource that references:

    #Make sure no static records in DNS

![Appendix9][19]

Ahora, quite el servicio de nube antigua dirección IP.

![Appendix10][20]

#### <a name="step-15-dns-update-check"></a>Paso 15: Comprobación de actualizaciones de DNS

Ahora debe comprobar los servidores DNS en sus redes de cliente de SQL Server y asegúrese de que el clúster ha agregado el registro de host adicional para la dirección IP agregada. Si los servidores DNS no actualizado, considere la posibilidad de forzar una transferencia de zona DNS y asegúrese de que los clientes de allí subred pueden resolver en ambos siempre en direcciones IP, así no es necesario esperar la replicación de DNS automática.

#### <a name="step-16-reconfigure-always-on"></a>Paso 16: Volver a configurar siempre en

En este momento espere secundaria ese nodo que migró para totalmente volver a sincronizar con el nodo local y cambie al nodo de replicación sincrónico y que sea la AFP.  

#### <a name="step-17-migrate-second-node"></a>17 de paso: Migrar segundo nodo
    $vmNameToMigrate="dansqlams1"

    Get-AzureVM -ServiceName $sourceSvc -Name $vmNameToMigrate

    #Get endpoint information
    $endpoint = Get-AzureVM -ServiceName $sourceSvc  -Name $vmNameToMigrate | Get-AzureEndpoint

    #Shutdown VM
    Get-AzureVM -ServiceName $sourceSvc -Name $vmNameToMigrate | stop-AzureVM

    #Get disk config

    #Building Existing Data Disk Configuration
    $file = "C:\Azure Storage Testing\mydiskconfig_$vmNameToMigrate.csv"
    $datadisks = @(Get-AzureVM -ServiceName $sourceSvc -Name $vmNameToMigrate | Get-AzureDataDisk )
    Add-Content $file “lun, vhdname, hostcaching, disklabel, diskName”
    foreach ($disk in $datadisks)
    {
      $vhdname = $disk.MediaLink.AbsolutePath -creplace  "/vhds/"
      $disk.Lun, , $disk.HostCaching, $vhdname, $disk.DiskLabel,$disks.DiskName
    # Write-Host "copying disk $disk"
    $adddisk = "{0},{1},{2},{3},{4}" -f $disk.Lun,$vhdname, $disk.HostCaching, $disk.DiskLabel, $disk.DiskName
    $adddisk | add-content -path $file
    }

    #Get OS Disk
    $osdisks = Get-AzureVM -ServiceName $sourceSvc -Name $vmNameToMigrate | Get-AzureOSDisk ## | select -ExpandProperty MediaLink
    $osvhdname = $osdisks.MediaLink.AbsolutePath -creplace  "/vhds/"
    $osdisks.OS, $osdisks.HostCaching, $osvhdname, $osdisks.DiskLabel, $osdisks.DiskName
    $addosdisk = "{0},{1},{2},{3},{4}" -f $osdisks.OS,$osvhdname, $osdisks.HostCaching, $osdisks.Disklabel , $osdisks.DiskName
    $addosdisk | add-content -path $file

    #Import disk config
    $diskobjects  = Import-CSV $file

    #Check disk configuration
    $diskobjects

    #Identify OS Disk
    $osdiskimport = $diskobjects | where {$_.lun -eq "Windows"}
    $osdiskforbuild = $osdiskimport.diskName

    #Check machine is off
    Get-AzureVM -ServiceName $sourceSvc -Name  $vmNameToMigrate

    #Drop machine and rebuild to new cls
    Remove-AzureVM -ServiceName $sourceSvc -Name $vmNameToMigrate

#### <a name="step-18-change-disk-caching-settings-in-csv-file-and-save"></a>Paso 18: Cambiar la configuración en el archivo CSV de caché de disco y guardar

Volúmenes de datos de estos se deben establecer en sólo lectura.

Para volúmenes de registro de transacciones estos deben establecer en ninguna.

![Appendix11][21]

#### <a name="step-19-create-new-independent-storage-account-for-secondary-node"></a>Paso 19: Crear nueva cuenta de almacenamiento independientes para nodo secundario
    $newxiostorageaccountnamenode2 = "danspremsams2"
    New-AzureStorageAccount -StorageAccountName $newxiostorageaccountnamenode2 -Location $location -Type "Premium_LRS"  

    #Reset the storage account src if node 1 in a different storage account
    $origstorageaccountname2nd = "danstdams2"

    #Generate storage keys for later
    $xiostoragenode2 = Get-AzureStorageKey -StorageAccountName $newxiostorageaccountnamenode2

    #Generate storage acc contexts
    $xioContextnode2 = New-AzureStorageContext  –StorageAccountName $newxiostorageaccountnamenode2 -StorageAccountKey $xiostoragenode2.Primary  

    #Set up subscription and default storage account
    Set-AzureSubscription -SubscriptionName $mysubscription -CurrentStorageAccount $newxiostorageaccountnamenode2
    Select-AzureSubscription -SubscriptionName $mysubscription -Current

#### <a name="step-20-copy-vhds"></a>Paso 20: Copiar VHD
    #Ensure you have created the container for these:
    $containerName = 'vhds'

    #Create container
    New-AzureStorageContainer -Name $containerName -Context $xioContextnode2  

    ####DISK COPYING####
    ##get disks from csv, get settings for each VHDs and copy to Premium Storage accoun
    ForEach ($disk in $diskobjects)
       {
       $lun = $disk.Lun
       $vhdname = $disk.vhdname
       $cacheoption = $disk.HostCaching
       $disklabel = $disk.DiskLabel
       $diskName = $disk.DiskName
       Write-Host "Copying Disk Lun $lun, Label : $disklabel, VHD : $vhdname has cache setting : $cacheoption"

       #Start async copy
       Start-AzureStorageBlobCopy -srcUri "https://$origstorageaccountname2nd.blob.core.windows.net/vhds/$vhdname" `
        -SrcContext $origContext `
        -DestContainer $containerName `
        -DestBlob $vhdname `
        -DestContext $xioContextnode2
       }

    #Check for copy progress

    #check induvidual blob status
    Get-AzureStorageBlobCopyState -Blob "danRegSvcAms-dansqlams1-2014-07-03.vhd" -Container $containerName -Context $xioContext


Puede comprobar el estado de copia de disco duro virtual para todos los VHD: ForEach ($disk en $diskobjects) {$lun = $disk. LUN $vhdname = $disk.vhdname $cacheoption = $disk. HostCaching $disklabel = $disk. EtiquetaDeDisco $diskName = $disk. DiskName

       $copystate = Get-AzureStorageBlobCopyState -Blob $vhdname -Container $containerName -Context $xioContextnode2
    Write-Host "Copying Disk Lun $lun, Label : $disklabel, VHD : $vhdname, STATUS = " $copystate.Status
       }

![Appendix12][22]

Espere a que todos ellos se registran como correcto.

Para obtener información para blobs individuales: estado de blobs de #Check induvidual AzureStorageBlobCopyState Get-Blob "danRegSvcAms-dansqlams1-2014-07-03.vhd"-contenedor $containerName-contexto $xioContextnode2

#### <a name="step-21-register-os-disk"></a>Paso 21: Disco de registrar OS
    #change storage account to the new XIO storage account
    Set-AzureSubscription -SubscriptionName $mysubscription -CurrentStorageAccount $newxiostorageaccountnamenode2
    Select-AzureSubscription -SubscriptionName $mysubscription -Current

    #Register OS disk
    $osdiskimport = $diskobjects | where {$_.lun -eq "Windows"}
    $osvhd = $osdiskimport.vhdname
    $osdiskforbuild = $osdiskimport.diskName

    #Registering OS disk, but as XIO disk
    $xioDiskName = $osdiskforbuild + "xio"
    Add-AzureDisk -DiskName $xioDiskName -MediaLocation  "https://$newxiostorageaccountnamenode2.blob.core.windows.net/vhds/$osvhd"  -Label "BootDisk" -OS "Windows"

    #Build VM Config
    $ipaddr = "192.168.0.4"
    $newInstanceSize = "Standard_DS13"

    #Join to existing Avaiability Set

    #Build machine config into object
    $vmConfig = New-AzureVMConfig -Name $vmNameToMigrate -InstanceSize $newInstanceSize -DiskName $xioDiskName -AvailabilitySetName $availabilitySet  ` | Add-AzureProvisioningConfig -Windows ` | Set-AzureSubnet -SubnetNames $subnet | Set-AzureStaticVNetIP -IPAddress $ipaddr

    #Reload disk config
    $diskobjects  = Import-CSV $file
    $datadiskimport = $diskobjects | where {$_.lun -ne "Windows"}

    ForEach ( $attachdatadisk in $datadiskimport)
       {
    $label = $attachdatadisk.disklabel
    $lunNo = $attachdatadisk.lun
    $hostcach = $attachdatadisk.hostcaching
    $datadiskforbuild = $attachdatadisk.diskName
    $vhdname = $attachdatadisk.vhdname

    ###This is different to just a straight cloud service change
    #note if you do not have a disk label the command below will fail, populate as required.
    $vmConfig | Add-AzureDataDisk -ImportFrom -MediaLocation "https://$newxiostorageaccountnamenode2.blob.core.windows.net/vhds/$vhdname" -LUN $lunNo -HostCaching $hostcach -DiskLabel $label

    }

    #Create VM
    $vmConfig  | New-AzureVM –ServiceName $destcloudsvc –Location $location -VNetName $vnet -Verbose

#### <a name="step-22-add-load-balanced-endpoints-and-acls"></a>Paso 22: Agregar carga equilibrada extremos y ACL
    #Endpoints
    $epname="sqlIntEP"
    $prot="tcp"
    $locport=1433
    $pubport=1433
    Get-AzureVM –ServiceName $destcloudsvc –Name $vmNameToMigrate  | Add-AzureEndpoint -Name $epname -Protocol $prot -LocalPort $locport -PublicPort $pubport -ProbePort 59999 -ProbeIntervalInSeconds 5 -ProbeTimeoutInSeconds 11  -ProbeProtocol "TCP" -InternalLoadBalancerName $ilb -LBSetName $ilb -DirectServerReturn $true | Update-AzureVM


    #STOP!!! CHECK in the Azure classic portal or Machine Endpoints through powershell that these Endpoints are created!

    #SET ACLs or Azure Network Security Groups & Windows FWs

    #http://msdn.microsoft.com/library/azure/dn495192.aspx

#### <a name="step-23-test-failover"></a>Paso 23: Migración tras error de prueba

Ahora debe permitir que el nodo migrado sincronizar con el nodo local siempre, colocarla en modo sincrónico replicación y espere a que se sincroniza. A continuación, migra migración tras error de local al primer nodo cuál es la AFP. Una vez que haya trabajado, cambie el último nodo migrado a la AFP.

Debe probar migraciones tras error entre todos los nodos y ejecute aunque caos pruebas para asegurarse de trabajo de migraciones tras error como se esperaba y en un manor oportuna.

#### <a name="step-24-put-back-cluster-quorum-settings--dns-ttl--failover-pntrs--sync-settings"></a>Paso 24: Vuelva colocar las opciones de quórum de clúster / DNS TTL / Failover Pntrs o configuración de sincronización
##### <a name="adding-ip-address-resource-on-same-subnet"></a>Agregar recurso de dirección IP en la misma subred

Si tiene sólo 2 servidores de SQL Server y desea migrarlas a un nuevo servicio de nube, pero desea conservar en la misma subred, no puede tomar el oyente sin conexión para eliminar la siempre en dirección IP original y agregar la nueva dirección IP. Si va a migrar las máquinas virtuales a otra subred no debe hacerlo como habrá una red de clúster adicional que hacen referencia a esa subred.

Una vez haya abre el secundario migrado y agregado en el nuevo recurso de dirección IP para el nuevo servicio de nube antes de la migración tras error principal existente, debe seguir estos pasos en el Administrador de clúster de migración tras error:

Para agregar la dirección IP, consulte el [Apéndice](#appendix-migrating-a-multisite-alwayson-cluster-to-premium-storage), paso 14.

1. Para el recurso de dirección IP actual, cambiar al propietario posible 'Existente principal SQL Server', en el ejemplo siguiente, 'dansqlams4':

    ![Appendix13][23]

1. Para el nuevo recurso de dirección IP, cambie al propietario posible 'Migrado secundario SQL Server', en el ejemplo siguiente, 'dansqlams5':

    ![Appendix14][24]

1. Una vez que se establece puede migración tras error y cuando se migra el último nodo debe editarse los posibles propietarios para que ese nodo se agrega como posible propietario:

    ![Appendix15][25]

## <a name="additional-resources"></a>Recursos adicionales
- [Almacenamiento de Azure Premium](../storage/storage-premium-storage.md)
- [Máquinas virtuales de Windows](https://azure.microsoft.com/services/virtual-machines/)
- [SQL Server en máquinas virtuales de Windows Azure](virtual-machines-windows-sql-server-iaas-overview.md)

<!-- IMAGES -->
[1]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/1_VNET_Portal.png
[2]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/2_Diskname_Lun.png
[3]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/3_Virtual_Disk_Properties.png
[4]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/4_Virtual_Disk_Properties_Details.png
[5]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/5_Get_Storage_Pool.png
[6]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/6_Deployments_Always_On.png
[7]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/7_Add_More_Secondaries.png
[8]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/8_Use_Existing_Secondary_Single_Site.png
[9]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/9_Use_Existing_Secondary_Multi_Site.png
[10]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/9_Use_Existing_Secondary_Multi_Site_b.png
[11]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_01.png
[12]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_02.png
[13]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_03.png
[14]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_04.png
[15]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_05.png
[16]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_06.png
[17]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_07.png
[18]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_08.png
[19]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_09.png
[20]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_10.png
[21]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_11.png
[22]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_12.png
[23]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_13.png
[24]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_14.png
[25]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_15.png
