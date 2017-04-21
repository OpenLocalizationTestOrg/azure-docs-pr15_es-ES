<properties
   pageTitle="Implementar máquinas virtuales de NIC multi con PowerShell en el modelo de implementación clásica | Microsoft Azure"
   description="Obtenga información sobre cómo implementar múltiples máquinas virtuales de NIC con PowerShell en el modelo de implementación clásica"
   services="virtual-network"
   documentationCenter="na"
   authors="jimdial"
   manager="carmonm"
   editor=""
   tags="azure-service-management"
/>
<tags  
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/02/2016"
   ms.author="jdial" />

#<a name="deploy-multi-nic-vms-classic-using-powershell"></a>Implementar múltiples máquinas virtuales de NIC con PowerShell (clásico)

[AZURE.INCLUDE [virtual-network-deploy-multinic-classic-selectors-include.md](../../includes/virtual-network-deploy-multinic-classic-selectors-include.md)]

Puede crear máquinas virtuales (VM) en Azure y adjuntar varias interfaces de red (NIC) para cada uno de sus máquinas virtuales. Varias NIC Habilitar separación de tipos de tráfico a través de NIC. Por ejemplo, una NIC puede comunicarse con Internet, mientras que otro sólo se comunica con recursos internos no está conectados a Internet. La capacidad para separar el tráfico de red en varias NIC es necesaria para muchos dispositivos virtual red, como la entrega de aplicaciones y soluciones de optimización de WAN.

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-classic-include.md)]Obtenga información sobre cómo llevar a [cabo estos pasos con el modelo de administrador de recursos](virtual-network-deploy-multinic-arm-ps.md).

[AZURE.INCLUDE [virtual-network-deploy-multinic-scenario-include.md](../../includes/virtual-network-deploy-multinic-scenario-include.md)]

Actualmente, no puede tener máquinas virtuales con una sola NIC y máquinas virtuales con varias NIC en el mismo servicio de nube. Por lo tanto, debe implementar los servidores back-end en un servicio de nube diferente que y todos los demás componentes en el escenario. Los pasos siguientes utilizan un servicio de nube denominado *IaaSStory* para los recursos principales y *Back-end de IaaSStory* para los servidores de back-end.

## <a name="prerequisites"></a>Requisitos previos

Antes de implementar los servidores back-end, debe implementar el servicio de nube principal con todos los recursos necesarios para este escenario. Como mínimo, debe crear una red virtual con una subred para el back-end. Visite [crear una red virtual con PowerShell](virtual-networks-create-vnet-classic-netcfg-ps.md) para obtener información sobre cómo implementar una red virtual.

[AZURE.INCLUDE [azure-ps-prerequisites-include.md](../../includes/azure-ps-prerequisites-include.md)]

## <a name="deploy-the-back-end-vms"></a>Implementar la VM back-end

Las máquinas virtuales de back-end dependen de la creación de los recursos que se muestran a continuación.

- **Subred de back-end**. Los servidores de base de datos será parte de una subred independiente, dividir el tráfico. La siguiente secuencia de comandos espera esta subred existen en un vnet denominado *WTestVnet*.
- **Cuenta de almacenamiento para los discos de datos**. Para mejorar el rendimiento, los discos de datos en los servidores de base de datos utiliza tecnología de unidades (SSD) de estado sólido, que requiere una cuenta de almacenamiento premium. Asegúrese de que la ubicación de Azure implementa para admitir el almacenamiento de premium.
- **Establece la disponibilidad**. Todos los servidores de base de datos se agregará a una sola disponibilidad establecida, para asegurarse de que al menos una de las máquinas virtuales es hacia arriba y ejecuta durante el mantenimiento.

### <a name="step-1---start-your-script"></a>Paso 1: comenzar la secuencia de comandos

Puede descargar la secuencia de comandos de PowerShell completa utiliza [aquí](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/11-MultiNIC/classic/virtual-network-deploy-multinic-classic-ps.ps1). Siga los pasos siguientes para cambiar la secuencia de comandos para trabajar en su entorno.

1. Cambiar los valores de las variables siguientes en función de su grupo de recursos existente implementado por encima de [los requisitos previos](#Prerequisites).

        $location              = "West US"
        $vnetName              = "WTestVNet"
        $backendSubnetName     = "BackEnd"

2. Cambiar los valores de las variables siguientes en función de los valores que desea usar para la implementación de back-end.

        $backendCSName         = "IaaSStory-Backend"
        $prmStorageAccountName = "iaasstoryprmstorage"
        $avSetName             = "ASDB"
        $vmSize                = "Standard_DS3"
        $diskSize              = 127
        $vmNamePrefix          = "DB"
        $dataDiskSuffix        = "datadisk"
        $ipAddressPrefix       = "192.168.2."
        $numberOfVMs           = 2

### <a name="step-2---create-necessary-resources-for-your-vms"></a>Paso 2: crear recursos necesarios para sus máquinas virtuales

Debe crear un nuevo servicio de nube y una cuenta de almacenamiento para los discos de datos para todas las máquinas virtuales. También debe especificar una imagen y una cuenta de administrador local para las máquinas virtuales. Para crear estos recursos, ejecute los siguientes pasos.

1. Crear un nuevo servicio de nube.

        New-AzureService -ServiceName $backendCSName -Location $location

2. Crear una nueva cuenta de almacenamiento premium.

        New-AzureStorageAccount -StorageAccountName $prmStorageAccountName `
            -Location $location `
            -Type Premium_LRS

3. Configurar la cuenta de almacenamiento creada anteriormente, como la cuenta de almacenamiento actual para la suscripción.

        $subscription = Get-AzureSubscription `
            | where {$_.IsCurrent -eq $true}  
        Set-AzureSubscription -SubscriptionName $subscription.SubscriptionName `
            -CurrentStorageAccountName $prmStorageAccountName

4. Seleccione una imagen de la máquina virtual.

        $image = Get-AzureVMImage `
            | where{$_.ImageFamily -eq "SQL Server 2014 RTM Web on Windows Server 2012 R2"} `
            | sort PublishedDate -Descending `
            | select -ExpandProperty ImageName -First 1

5. Establecer las credenciales de cuenta de administrador local.

        $cred = Get-Credential -Message "Enter username and password for local admin account"

### <a name="step-3---create-vms"></a>Paso 3: crear máquinas virtuales

Debe utilizar un bucle para crear tantas máquinas virtuales como desee y crear las NIC y máquinas virtuales en el bucle. Para crear la NIC y máquinas virtuales, ejecute los siguientes pasos.

1. Iniciar un `for` en bucle para repetir rápidamente los comandos para crear una máquina virtual y dos NIC tantas veces como sea necesario, según el valor de la `$numberOfVMs` variable.

        for ($suffixNumber = 1; $suffixNumber -le $numberOfVMs; $suffixNumber++){

2. Crear un `VMConfig` objeto especificando la imagen, el tamaño y la disponibilidad para la máquina virtual.

            $vmName = $vmNamePrefix + $suffixNumber
            $vmConfig = New-AzureVMConfig -Name $vmName `
                            -ImageName $image `
                            -InstanceSize $vmSize `
                            -AvailabilitySetName $avSetName  

3. Aprovisionar la máquina virtual como una máquina virtual de Windows.

            Add-AzureProvisioningConfig -VM $vmConfig -Windows `
                -AdminUsername $cred.UserName `
                -Password $cred.Password

4. Establecer el valor predeterminado NIC y asignar una dirección IP estática.

            Set-AzureSubnet -SubnetNames $backendSubnetName -VM $vmConfig
            Set-AzureStaticVNetIP -IPAddress ($ipAddressPrefix+$suffixNumber+3) -VM $vmConfig

5. Agregar una segunda NIC para cada máquina virtual.

            Add-AzureNetworkInterfaceConfig -Name ("RemoteAccessNIC"+$suffixNumber) `
                -SubnetName $backendSubnetName `
                -StaticVNetIPAddress ($ipAddressPrefix+(53+$suffixNumber)) `
                -VM $vmConfig

6. Para cada VM, cree discos de datos.

            $dataDisk1Name = $vmName + "-" + $dataDiskSuffix + "-1"    
            Add-AzureDataDisk -CreateNew -VM $vmConfig `
                -DiskSizeInGB $diskSize `
                -DiskLabel $dataDisk1Name `
                -LUN 0       

            $dataDisk2Name = $vmName + "-" + $dataDiskSuffix + "-2"   
            Add-AzureDataDisk -CreateNew -VM $vmConfig `
                -DiskSizeInGB $diskSize `
                -DiskLabel $dataDisk2Name `
                -LUN 1

7. Crear cada VM y terminar el bucle.

            New-AzureVM -VM $vmConfig `
                -ServiceName $backendCSName `
                -Location $location `
                -VNetName $vnetName
        }

### <a name="step-4---run-the-script"></a>Paso 4: ejecute la secuencia de comandos

Ahora que descargó y cambiar la secuencia de comandos en función de sus necesidades, ejecución secuencia de comandos para crear máquinas virtuales de base de datos de back-end con varias NIC.

1. Guarde la secuencia de comandos y ejecutar desde el símbolo del sistema de **PowerShell** o **PowerShell ISE**. Verá el resultado inicial, como se muestra a continuación.

        OperationDescription    OperationId                          OperationStatus
        --------------------    -----------                          ---------------
        New-AzureService        xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx Succeeded      
        New-AzureStorageAccount xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx Succeeded      

        WARNING: No deployment found in service: 'IaaSStory-Backend'.

2. Rellene la información necesaria en el símbolo del sistema de credenciales y haga clic en **Aceptar**. Se mostrará el siguiente resultado.

        New-AzureVM             xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx Succeeded
        New-AzureVM             xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx Succeeded
