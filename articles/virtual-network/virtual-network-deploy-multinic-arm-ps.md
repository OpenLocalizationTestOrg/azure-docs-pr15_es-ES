<properties
   pageTitle="Implementar máquinas virtuales de NIC multi con PowerShell en el Administrador de recursos | Microsoft Azure"
   description="Obtenga información sobre cómo implementar múltiples máquinas virtuales de NIC con PowerShell en el Administrador de recursos"
   services="virtual-network"
   documentationCenter="na"
   authors="jimdial"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"
/>
<tags  
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/02/2016"
   ms.author="jdial" />

#<a name="deploy-multi-nic-vms-using-powershell"></a>Implementar máquinas virtuales de NIC multi con PowerShell

[AZURE.INCLUDE [virtual-network-deploy-multinic-arm-selectors-include.md](../../includes/virtual-network-deploy-multinic-arm-selectors-include.md)]

[AZURE.INCLUDE [virtual-network-deploy-multinic-intro-include.md](../../includes/virtual-network-deploy-multinic-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-rm-include.md)][modelo de implementación clásico](virtual-network-deploy-multinic-classic-ps.md).

[AZURE.INCLUDE [virtual-network-deploy-multinic-scenario-include.md](../../includes/virtual-network-deploy-multinic-scenario-include.md)]

Actualmente, no puede tener máquinas virtuales con una sola NIC y máquinas virtuales con varias NIC en el mismo conjunto de disponibilidad. Por lo tanto, debe implementar los servidores back-end de un grupo de recursos diferentes que todos los otros componentes. Los pasos siguientes use un grupo de recursos denominado *IaaSStory* para el grupo de recursos principal y *Back-end de IaaSStory* para los servidores de back-end.

## <a name="prerequisites"></a>Requisitos previos

Antes de implementar los servidores back-end, debe implementar el grupo de recursos principal con todos los recursos necesarios para este escenario. Para implementar estos recursos, siga los pasos siguientes.

1. Vaya a [la página de plantilla](https://github.com/Azure/azure-quickstart-templates/tree/master/IaaS-Story/11-MultiNIC).
2. En la página de plantilla, a la derecha del **grupo de recursos principal**, haga clic en **implementar en Azure**.
3. Si es necesario, cambie los valores de parámetro, luego, siga los pasos en el portal de vista previa de Azure para implementar el grupo de recursos.

> [AZURE.IMPORTANT] Asegúrese de que los nombres de cuenta de almacenamiento son únicos. No puede tener nombres de cuenta de almacenamiento duplicados en Azure.

[AZURE.INCLUDE [azure-ps-prerequisites-include.md](../../includes/azure-ps-prerequisites-include.md)]

## <a name="deploy-the-back-end-vms"></a>Implementar la VM back-end

Las máquinas virtuales de back-end dependen de la creación de los recursos que se muestran a continuación.

- **Cuenta de almacenamiento para los discos de datos**. Para mejorar el rendimiento, los discos de datos en los servidores de base de datos utiliza tecnología de unidades (SSD) de estado sólido, que requiere una cuenta de almacenamiento premium. Asegúrese de que la ubicación de Azure implementa para admitir el almacenamiento de premium.
- **NIC**. Cada VM tendrá dos NIC, uno para el acceso de la base de datos y otro para la administración.
- **Establece la disponibilidad**. Todos los servidores de base de datos se agregará a una sola disponibilidad establecida, para asegurarse de que al menos una de las máquinas virtuales es hacia arriba y ejecuta durante el mantenimiento.  

### <a name="step-1---start-your-script"></a>Paso 1: comenzar la secuencia de comandos

Puede descargar la secuencia de comandos de PowerShell completa utiliza [aquí](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/11-MultiNIC/arm/virtual-network-deploy-multinic-arm-ps.ps1). Siga los pasos siguientes para cambiar la secuencia de comandos para trabajar en su entorno.

[AZURE.INCLUDE [powershell-preview-include.md](../../includes/powershell-preview-include.md)]

1. Cambiar los valores de las variables siguientes en función de su grupo de recursos existente implementado por encima de [los requisitos previos](#Prerequisites).

        $existingRGName        = "IaaSStory"
        $location              = "West US"
        $vnetName              = "WTestVNet"
        $backendSubnetName     = "BackEnd"
        $remoteAccessNSGName   = "NSG-RemoteAccess"
        $stdStorageAccountName = "wtestvnetstoragestd"

2. Cambiar los valores de las variables siguientes en función de los valores que desea usar para la implementación de back-end.

        $backendRGName         = "IaaSStory-Backend"
        $prmStorageAccountName = "wtestvnetstorageprm"
        $avSetName             = "ASDB"
        $vmSize                = "Standard_DS3"
        $publisher             = "MicrosoftSQLServer"
        $offer                 = "SQL2014SP1-WS2012R2"
        $sku                   = "Standard"
        $version               = "latest"
        $vmNamePrefix          = "DB"
        $osDiskPrefix          = "osdiskdb"
        $dataDiskPrefix        = "datadisk"
        $diskSize              = "120"  
        $nicNamePrefix         = "NICDB"
        $ipAddressPrefix       = "192.168.2."
        $numberOfVMs           = 2

3. Recuperar los recursos existentes necesarios para su implementación.

        $vnet                  = Get-AzureRmVirtualNetwork -Name $vnetName -ResourceGroupName $existingRGName
        $backendSubnet         = $vnet.Subnets|?{$_.Name -eq $backendSubnetName}
        $remoteAccessNSG       = Get-AzureRmNetworkSecurityGroup -Name $remoteAccessNSGName -ResourceGroupName $existingRGName
        $stdStorageAccount     = Get-AzureRmStorageAccount -Name $stdStorageAccountName -ResourceGroupName $existingRGName

### <a name="step-2---create-necessary-resources-for-your-vms"></a>Paso 2: crear recursos necesarios para sus máquinas virtuales

Tiene que crear un nuevo grupo de recursos, una cuenta de almacenamiento para los discos de datos y una disponibilidad para todas las máquinas virtuales. Alos necesita las credenciales de cuenta de administrador local para cada máquina virtual. Para crear estos recursos, ejecute los siguientes pasos.

1. Crear un nuevo grupo de recursos.

        New-AzureRmResourceGroup -Name $backendRGName -Location $location

2. Crear una nueva cuenta de almacenamiento premium en el grupo de recursos creado anteriormente.

        $prmStorageAccount = New-AzureRmStorageAccount -Name $prmStorageAccountName `
            -ResourceGroupName $backendRGName -Type Premium_LRS -Location $location

3. Crear un nuevo conjunto de disponibilidad.

        $avSet = New-AzureRmAvailabilitySet -Name $avSetName -ResourceGroupName $backendRGName -Location $location

4. Obtener las credenciales de cuenta que se usará para cada VM de administrador local.

        $cred = Get-Credential -Message "Type the name and password for the local administrator account."

### <a name="step-3---create-the-nics-and-backend-vms"></a>Paso 3: crear la NIC y máquinas virtuales de back-end

Debe utilizar un bucle para crear tantas máquinas virtuales como desee y crear las NIC y máquinas virtuales en el bucle. Para crear la NIC y máquinas virtuales, ejecute los siguientes pasos.

1. Iniciar un `for` en bucle para repetir rápidamente los comandos para crear una máquina virtual y dos NIC tantas veces como sea necesario, según el valor de la `$numberOfVMs` variable.

        for ($suffixNumber = 1; $suffixNumber -le $numberOfVMs; $suffixNumber++){

2. Crear la NIC que utiliza para el acceso de la base de datos.

            $nic1Name = $nicNamePrefix + $suffixNumber + "-DA"
            $ipAddress1 = $ipAddressPrefix + ($suffixNumber + 3)
            $nic1 = New-AzureRmNetworkInterface -Name $nic1Name -ResourceGroupName $backendRGName `
                -Location $location -SubnetId $backendSubnet.Id -PrivateIpAddress $ipAddress1

3. Crear la NIC que utiliza para el acceso remoto. Observe cómo esta NIC tiene un GSN asociada a ella.

            $nic2Name = $nicNamePrefix + $suffixNumber + "-RA"
            $ipAddress2 = $ipAddressPrefix + (53 + $suffixNumber)
            $nic2 = New-AzureRmNetworkInterface -Name $nic2Name -ResourceGroupName $backendRGName `
                -Location $location -SubnetId $backendSubnet.Id -PrivateIpAddress $ipAddress2 `
                -NetworkSecurityGroupId $remoteAccessNSG.Id

4. Crear `vmConfig` objeto.

            $vmName = $vmNamePrefix + $suffixNumber
            $vmConfig = New-AzureRmVMConfig -VMName $vmName -VMSize $vmSize -AvailabilitySetId $avSet.Id

5. Cree dos discos de datos por máquina virtual. Observe que los discos de datos están en la cuenta de almacenamiento premium creada anteriormente.

            $dataDisk1Name = $vmName + "-" + $osDiskPrefix + "-1"    
            $data1VhdUri = $prmStorageAccount.PrimaryEndpoints.Blob.ToString() + "vhds/" + $dataDisk1Name + ".vhd"
            Add-AzureRmVMDataDisk -VM $vmConfig -Name $dataDisk1Name -DiskSizeInGB $diskSize `
                -VhdUri $data1VhdUri -CreateOption empty -Lun 0

            $dataDisk2Name = $vmName + "-" + $dataDiskPrefix + "-2"    
            $data2VhdUri = $prmStorageAccount.PrimaryEndpoints.Blob.ToString() + "vhds/" + $dataDisk2Name + ".vhd"
            Add-AzureRmVMDataDisk -VM $vmConfig -Name $dataDisk2Name -DiskSizeInGB $diskSize `
                -VhdUri $data2VhdUri -CreateOption empty -Lun 1

6. Configurar el sistema operativo y de imagen que se utilizará para la máquina virtual.

            $vmConfig = Set-AzureRmVMOperatingSystem -VM $vmConfig -Windows -ComputerName $vmName -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
            $vmConfig = Set-AzureRmVMSourceImage -VM $vmConfig -PublisherName $publisher -Offer $offer -Skus $sku -Version $version

7. Agregar las dos NIC creadas anteriormente a la `vmConfig` objeto.

            $vmConfig = Add-AzureRmVMNetworkInterface -VM $vmConfig -Id $nic1.Id -Primary
            $vmConfig = Add-AzureRmVMNetworkInterface -VM $vmConfig -Id $nic2.Id

8. Cree el disco de sistema operativo y la máquina virtual. Aviso de la `}` que termina el `for` bucle.

            $osDiskName = $vmName + "-" + $osDiskSuffix
            $osVhdUri = $stdStorageAccount.PrimaryEndpoints.Blob.ToString() + "vhds/" + $osDiskName + ".vhd"
            $vmConfig = Set-AzureRmVMOSDisk -VM $vmConfig -Name $osDiskName -VhdUri $osVhdUri -CreateOption fromImage
            New-AzureRmVM -VM $vmConfig -ResourceGroupName $backendRGName -Location $location
        }

### <a name="step-4---run-the-script"></a>Paso 4: ejecute la secuencia de comandos

Ahora que descargó y cambiar la secuencia de comandos en función de sus necesidades, ejecución secuencia de comandos para crear máquinas virtuales de base de datos de back-end con varias NIC.

1. Guarde la secuencia de comandos y ejecutar desde el símbolo del sistema de **PowerShell** o **PowerShell ISE**. Verá el resultado inicial, como se muestra a continuación.

        ResourceGroupName : IaaSStory-Backend
        Location          : westus
        ProvisioningState : Succeeded
        Tags              :
        Permissions       :
                            Actions  NotActions
                            =======  ==========
                            *                  

        ResourceId        : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/IaaSStory-Backend

2. Después de unos minutos, rellene la pregunta de credenciales y haga clic en **Aceptar**. El resultado siguiente representa una única VM. Aviso de todo el proceso realizó 8 minutos en completarse.

        ResourceGroupName            :
        Id                           :
        Name                         : DB2
        Type                         :
        Location                     :
        Tags                         :
        TagsText                     : null
        AvailabilitySetReference     : Microsoft.Azure.Management.Compute.Models.AvailabilitySetReference
        AvailabilitySetReferenceText : {
                                         "ReferenceUri": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/IaaSStory-Backend/providers/
                                       Microsoft.Compute/availabilitySets/ASDB"
                                       }
        Extensions                   :
        ExtensionsText               : null
        HardwareProfile              : Microsoft.Azure.Management.Compute.Models.HardwareProfile
        HardwareProfileText          : {
                                         "VirtualMachineSize": "Standard_DS3"
                                       }
        InstanceView                 :
        InstanceViewText             : null
        NetworkProfile               :
        NetworkProfileText           : null
        OSProfile                    :
        OSProfileText                : null
        Plan                         :
        PlanText                     : null
        ProvisioningState            :
        StorageProfile               : Microsoft.Azure.Management.Compute.Models.StorageProfile
        StorageProfileText           : {
                                         "DataDisks": [
                                           {
                                             "Lun": 0,
                                             "Caching": null,
                                             "CreateOption": "empty",
                                             "DiskSizeGB": 127,
                                             "Name": "DB2-datadisk-1",
                                             "SourceImage": null,
                                             "VirtualHardDisk": {
                                               "Uri": "https://wtestvnetstorageprm.blob.core.windows.net/vhds/DB2-datadisk-1.vhd"
                                             }
                                           }
                                         ],
                                         "ImageReference": null,
                                         "OSDisk": null
                                       }
        DataDiskNames                : {DB2-datadisk-1}
        NetworkInterfaceIDs          :
        RequestId                    :
        StatusCode                   : 0


        ResourceGroupName            :
        Id                           :
        Name                         : DB2
        Type                         :
        Location                     :
        Tags                         :
        TagsText                     : null
        AvailabilitySetReference     : Microsoft.Azure.Management.Compute.Models.AvailabilitySetReference
        AvailabilitySetReferenceText : {
                                         "ReferenceUri": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/IaaSStory-Backend/providers/
                                       Microsoft.Compute/availabilitySets/ASDB"
                                       }
        Extensions                   :
        ExtensionsText               : null
        HardwareProfile              : Microsoft.Azure.Management.Compute.Models.HardwareProfile
        HardwareProfileText          : {
                                         "VirtualMachineSize": "Standard_DS3"
                                       }
        InstanceView                 :
        InstanceViewText             : null
        NetworkProfile               :
        NetworkProfileText           : null
        OSProfile                    :
        OSProfileText                : null
        Plan                         :
        PlanText                     : null
        ProvisioningState            :
        StorageProfile               : Microsoft.Azure.Management.Compute.Models.StorageProfile
        StorageProfileText           : {
                                         "DataDisks": [
                                           {
                                             "Lun": 0,
                                             "Caching": null,
                                             "CreateOption": "empty",
                                             "DiskSizeGB": 127,
                                             "Name": "DB2-datadisk-1",
                                             "SourceImage": null,
                                             "VirtualHardDisk": {
                                               "Uri": "https://wtestvnetstorageprm.blob.core.windows.net/vhds/DB2-datadisk-1.vhd"
                                             }
                                           },
                                           {
                                             "Lun": 1,
                                             "Caching": null,
                                             "CreateOption": "empty",
                                             "DiskSizeGB": 127,
                                             "Name": "DB2-datadisk-2",
                                             "SourceImage": null,
                                             "VirtualHardDisk": {
                                               "Uri": "https://wtestvnetstorageprm.blob.core.windows.net/vhds/DB2-datadisk-2.vhd"
                                             }
                                           }
                                         ],
                                         "ImageReference": null,
                                         "OSDisk": null
                                       }
        DataDiskNames                : {DB2-datadisk-1, DB2-datadisk-2}
        NetworkInterfaceIDs          :
        RequestId                    :
        StatusCode                   : 0


        EndTime             : 10/30/2015 9:30:03 AM -08:00
        Error               :
        Output              :
        StartTime           : 10/30/2015 9:22:54 AM -08:00
        Status              : Succeeded
        TrackingOperationId : xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
        RequestId           : xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
        StatusCode          : OK
