<properties
    pageTitle="Administrar máquinas virtuales de un conjunto de escala de la máquina Virtual | Microsoft Azure"
    description="Administrar máquinas virtuales de una escala de máquina virtual establezca con PowerShell de Azure."
    services="virtual-machine-scale-sets"
    documentationCenter=""
    authors="davidmu1"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machine-scale-sets"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/27/2016"
    ms.author="davidmu"/>

# <a name="manage-virtual-machines-in-a-virtual-machine-scale-set"></a>Administrar máquinas virtuales de un conjunto de escala de máquina virtual

Use las tareas de este artículo para administrar máquinas virtuales en el conjunto de escala de la máquina virtual.

La mayoría de las tareas que implican administrar una máquina virtual de un conjunto de escala requiere que conozca el identificador de instancia del equipo que desea administrar. Puede utilizar el [Explorador de recursos de Azure](https://resources.azure.com) para encontrar el identificador de instancia de una máquina virtual en un conjunto de escala. También se pueden utilizar el Explorador de recursos para comprobar el estado de las tareas que termine.

Vea [cómo instalar y configurar Azure PowerShell](../powershell-install-configure.md) para obtener información sobre cómo instalar la última versión de PowerShell de Azure, seleccionar la suscripción e iniciar sesión con su cuenta.

## <a name="display-information-about-a-scale-set"></a>Mostrar información sobre un conjunto de escala

Puede obtener información general acerca de un conjunto de escala, que también se conoce como la vista de instancia. O bien, puede obtener información más específica, como la información de los recursos en el conjunto de escala.

Reemplace los valores entre comillas por el nombre o el grupo de recursos y escala establecer y, a continuación, ejecute el comando:

    Get-AzureRmVmss -ResourceGroupName "resource group name" -VMScaleSetName "scale set name"

Devuelve algo parecido a esto:

    Id                                          : /subscriptions/{sub-id}/resourceGroups/myrg1/providers/Microsoft.Compute/virtualMachineScaleSets/myvmss1
    Name                                        : myvmss1
    Type                                        : Microsoft.Compute/virtualMachineScaleSets
    Location                                    : centralus
    Sku                                         :
      Name                                      : Standard_A0
      Tier                                      : Standard
      Capacity                                  : 3
    UpgradePolicy                               :
      Mode                                      : Manual
    VirtualMachineProfile                       :
      OsProfile                                 :
        ComputerNamePrefix                      : vmss1
        AdminUsername                           : admin1
        WindowsConfiguration                    :
          ProvisionVMAgent                      : True
          EnableAutomaticUpdates                : True
    StorageProfile                              :
      ImageReference                            :
        Publisher                               : MicrosoftWindowsServer
        Offer                                   : WindowsServer
        Sku                                     : 2012-R2-Datacenter
        Version                                 : latest
      OsDisk                                    :
        Name                                    : vmssosdisk
        Caching                                 : ReadOnly
        CreateOption                            : FromImage
        VhdContainers[0]                        : https://astore.blob.core.windows.net/vmss
        VhdContainers[1]                        : https://gstore.blob.core.windows.net/vmss
        VhdContainers[2]                        : https://mstore.blob.core.windows.net/vmss
        VhdContainers[3]                        : https://sstore.blob.core.windows.net/vmss
        VhdContainers[4]                        : https://ystore.blob.core.windows.net/vmss
    NetworkProfile                              :
      NetworkInterfaceConfigurations[0]         :
        Name                                    : mync1
        Primary                                 : True
        IpConfigurations[0]                     :
          Name                                  : ip1
          Subnet                                :
            Id                                  : /subscriptions/{sub-id}/resourceGroups/myrg1/providers/Microsoft.Network/virtualNetworks/myvn1/subnets/mysn1
          LoadBalancerBackendAddressPools[0]    :
            Id                                  : /subscriptions/{sub-id}/resourceGroups/myrg1/providers/Microsoft.Network/loadBalancers/mylb1/backendAddressPools/bepool1
        LoadBalancerInboundNatPools[0]          :
            Id                                  : /subscriptions/{sub-id}/resourceGroups/myrg1/providers/Microsoft.Network/loadBalancers/mylb1/inboundNatPools/natpool1
    ExtensionProfile                            :
      Extensions[0]                             :
        Name                                    : Microsoft.Insights.VMDiagnosticsSettings
        Publisher                               : Microsoft.Azure.Diagnostics
        Type                                    : IaaSDiagnostics
        TypeHandlerVersion                      : 1.5
        AutoUpgradeMinorVersion                 : True
        Settings                                : {"xmlCfg":"...","storageAccount":"astore"}
    ProvisioningState                           : Succeeded
    
Reemplace los valores entre comillas por el nombre de su conjunto de escala y grupo de recursos. Reemplazar *#* con el identificador de instancia de la máquina virtual que desea obtener información acerca de y ejecútelo:

    Get-AzureRmVmssVM -ResourceGroupName "resource group name" -VMScaleSetName "scale set name" -InstanceId #
        
Devuelve algo parecido a este ejemplo:

    Id                            : /subscriptions/{sub-id}/resourceGroups/myrg1/providers/Microsoft.Compute/
                                    virtualMachineScaleSets/myvmss1/virtualMachines/0
    Name                          : myvmss1_0
    Type                          : Microsoft.Compute/virtualMachineScaleSets/virtualMachines
    Location                      : centralus
    InstanceId                    : 0
    Sku                           :
      Name                        : Standard_A0
      Tier                        : Standard
    LatestModelApplied            : True
    StorageProfile                :
      ImageReference              :
        Publisher                 : MicrosoftWindowsServer
        Offer                     : WindowsServer
        Sku                       : 2012-R2-Datacenter
        Version                   : 4.0.20160617
      OsDisk                      :
        OsType                    : Windows
        Name                      : vmssosdisk-os-0-e11cad52959b4b76a8d9f26c5190c4f8
        Vhd                       :
          Uri                     : https://astore.blob.core.windows.net/vmss/vmssosdisk-os-0-e11cad52959b4b76a8d9f26c5190c4f8.vhd
        Caching                   : ReadOnly
        CreateOption              : FromImage
    OsProfile                     :
      ComputerName                : myvmss1-0
      AdminUsername               : admin1
      WindowsConfiguration        :
        ProvisionVMAgent          : True
        EnableAutomaticUpdates    : True
    NetworkProfile                :
      NetworkInterfaces[0]        :
        Id                        : /subscriptions/{sub-id}/resourceGroups/myrg1/providers/Microsoft.Compute/virtualMachineScaleSets/
                                    myvmss1/virtualMachines/0/networkInterfaces/mync1
    ProvisioningState             : Succeeded
    Resources[0]                  :
      Id                          : /subscriptions/{sub-id}/resourceGroups/myrg1/providers/Microsoft.Compute/virtualMachines/
                                    myvmss1_0/extensions/Microsoft.Insights.VMDiagnosticsSettings
      Name                        : Microsoft.Insights.VMDiagnosticsSettings
      Type                        : Microsoft.Compute/virtualMachines/extensions
      Location                    : centralus
      Publisher                   : Microsoft.Azure.Diagnostics
      VirtualMachineExtensionType : IaaSDiagnostics
      TypeHandlerVersion          : 1.5
      AutoUpgradeMinorVersion     : True
      Settings                    : {"xmlCfg":"...","storageAccount":"astore"}
      ProvisioningState           : Succeeded
        
## <a name="start-a-virtual-machine-in-a-scale-set"></a>Iniciar una máquina virtual de un conjunto de escala.

Reemplace los valores entre comillas por el nombre de su conjunto de escala y grupo de recursos. Reemplazar *#* con el identificador de la máquina virtual que desee iniciar y ejecútelo:

    Start-AzureRmVmss -ResourceGroupName "resource group name" -VMScaleSetName "scale set name" -InstanceId #

En el Explorador de recursos, podemos ver que el estado de la instancia está **ejecutando**:

    "statuses": [
      {
        "code": "ProvisioningState/succeeded",
        "level": "Info",
        "displayStatus": "Provisioning succeeded",
        "time": "2016-03-15T02:10:08.0730839+00:00"
      },
      {
        "code": "PowerState/running",
        "level": "Info",
        "displayStatus": "VM running"
      }
    ]

Puede iniciar todas las máquinas virtuales en la escala de establecer no utilizando el parámetro - InstanceId.
    
## <a name="stop-a-virtual-machine-in-a-scale-set"></a>Detener una máquina virtual de un conjunto de escala.

Reemplace los valores entre comillas por el nombre de su conjunto de escala y grupo de recursos. Reemplazar *#* con el identificador de la máquina virtual que desea detener y ejecútelo:

    Stop-AzureRmVmss -ResourceGroupName "resource group name" -VMScaleSetName "scale set name" -InstanceId #

En el Explorador de recursos, podemos ver que el estado de la instancia es **asignación**:

    "statuses": [
      {
        "code": "ProvisioningState/succeeded",
        "level": "Info",
        "displayStatus": "Provisioning succeeded",
        "time": "2016-03-15T01:25:17.8792929+00:00"
      },
      {
        "code": "PowerState/deallocated",
        "level": "Info",
        "displayStatus": "VM deallocated"
      }
    ]
    
Para detener una máquina virtual y no lo desasignar, use el parámetro - StayProvisioned. Puede detener todas las máquinas virtuales en el conjunto de si no usa el parámetro - InstanceId.
    
## <a name="restart-a-virtual-machine-in-a-scale-set"></a>Reiniciar una máquina virtual de un conjunto de escala.

Reemplace los valores entre comillas por el nombre de su grupo de recursos y el conjunto de escala. Reemplazar *#* con el identificador de la máquina virtual que desea reiniciar y ejecútelo:

    Restart-AzureRmVmss -ResourceGroupName "resource group name" -VMScaleSetName "scale set name" -InstanceId #
    
Puede reiniciar todas las máquinas virtuales en el conjunto no utilizando el parámetro - InstanceId.

## <a name="remove-a-virtual-machine-from-a-scale-set"></a>Quitar una máquina virtual de un conjunto de escala

Reemplace los valores entre comillas por el nombre de su grupo de recursos y el conjunto de escala. Reemplazar *#* con el identificador de la máquina virtual que desea quitar y, a continuación, ejecútelo:  

    Remove-AzureRmVmss -ResourceGroupName "resource group name" –VMScaleSetName "scale set name" -InstanceId #

Puede quitar el conjunto de escala de máquina virtual a la vez si no usa el parámetro - InstanceId.

## <a name="change-the-capacity-of-a-scale-set"></a>Cambiar la capacidad de un conjunto de escala

Puede agregar o quitar máquinas virtuales cambiando la capacidad del conjunto. Obtener el conjunto de escala que desee cambiar, establecer la capacidad para lo que desea y, a continuación, actualizar la configuración con la nueva capacidad de escala. En estos comandos, reemplace los valores entre comillas por el nombre de su grupo de recursos y el conjunto de escala.

  $vmss = get-AzureRmVmss - ResourceGroupName "nombre grupo de recursos" - VMScaleSetName "nombre de conjunto de escala" $vmss.sku.capacity = 5 actualización AzureRmVmss - ResourceGroupName "nombre grupo de recursos"-nombre "ajustar el nombre del conjunto de" - VirtualMachineScaleSet $vmss 

Si va a quitar máquinas virtuales desde el conjunto de escala, las máquinas virtuales con los identificadores de más altos se quitan en primer lugar.
