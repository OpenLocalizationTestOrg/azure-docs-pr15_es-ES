<properties
   pageTitle="Extensión de Script personalizado en una máquina virtual de Windows | Microsoft Azure"
   description="Automatizar tareas de configuración de Azure VM mediante la extensión de Script personalizado para ejecutar las secuencias de comandos de PowerShell en una máquina virtual de Windows remoto"
   services="virtual-machines-windows"
   documentationCenter=""
   authors="kundanap"
   manager="timlt"
   editor=""
   tags="azure-service-management"/>

<tags
   ms.service="virtual-machines-windows"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-windows"
   ms.workload="infrastructure-services"
   ms.date="08/06/2015"
   ms.author="kundanap"/>

# <a name="custom-script-extension-for-windows-virtual-machines"></a>Extensión de Script personalizado máquinas virtuales de Windows

Este artículo ofrece información general sobre cómo usar la extensión de Script personalizado en máquinas virtuales de Windows mediante el uso de cmdlets de PowerShell de Azure con las API de administración de servicio de Azure.

Extensiones de máquina virtual (VM) son generadas por Microsoft y confianza editores de terceros para ampliar la funcionalidad de la máquina virtual. Para obtener información general de las extensiones VM, vea [características y extensiones de Azure VM](virtual-machines-windows-extensions-features.md).

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Obtenga información sobre cómo llevar a [cabo estos pasos mediante el modelo de administrador de recursos](virtual-machines-windows-extensions-customscript.md).

## <a name="custom-script-extension-overview"></a>Introducción a la extensión Script personalizado

Con la extensión de Script personalizado para Windows, puede ejecutar secuencias de comandos de PowerShell en una máquina virtual remota sin iniciar sesión en él. Puede ejecutar las secuencias de comandos si ya dispone de la máquina virtual o en cualquier momento durante el ciclo de vida de la máquina virtual sin tener que abrir los puertos adicionales. Los casos de uso más comunes para ejecutar la extensión de Script personalizado incluyen ejecutando, instalar y configurar software adicional en la máquina virtual después de se aprovisiona.

### <a name="prerequisites-for-running-the-custom-script-extension"></a>Requisitos previos para ejecutar la extensión de Script personalizado

1. Instalar los <a href="http://azure.microsoft.com/downloads" target="_blank">cmdlets de PowerShell de Azure</a> versión 0.8.0 o posterior.
2. Si desea que las secuencias de comandos para que se ejecute en una máquina virtual existente, asegúrese de que el agente de VM se habilita en la máquina virtual. Si no está instalada, siga estos [pasos](virtual-machines-windows-classic-agents-and-extensions.md). Si se crea la máquina virtual desde el portal de Azure, agente de VM se instala de forma predeterminada.
3. Cargue las secuencias de comandos que desea ejecutar en la máquina virtual para el almacenamiento de Azure. Las secuencias de comandos pueden provenir de un solo contenedor o varios de los contenedores de almacenamiento.
4. La secuencia de comandos debe crearse para que la secuencia de entrada, que se inicia la extensión, comienza otras secuencias de comandos.

## <a name="custom-script-extension-scenarios"></a>Escenarios de extensión de Script personalizados

### <a name="upload-files-to-the-default-container"></a>Cargar archivos en el contenedor predeterminado

En el ejemplo siguiente se muestra cómo puede ejecutar las secuencias de comandos en la máquina virtual si se encuentran en el contenedor de almacenamiento de la cuenta predeterminada de la suscripción. Cargue las secuencias de comandos en nombre del contenedor. Puede comprobar la cuenta de almacenamiento predeterminada mediante la **AzureSubscription Get-predeterminado** comando.

En el ejemplo siguiente se crea una máquina virtual, pero también puede ejecutar el mismo escenario en una máquina virtual existente.

    # Create a new VM in Azure.
    $vm = New-AzureVMConfig -Name $name -InstanceSize Small -ImageName $imagename
    $vm = Add-AzureProvisioningConfig -VM $vm -Windows -AdminUsername $username -Password $password
    // Add Custom Script extension to the VM. The container name refers to the storage container that contains the file.
    $vm = Set-AzureVMCustomScriptExtension -VM $vm -ContainerName $container -FileName 'start.ps1'
    New-AzureVM -ServiceName $servicename -Location $location -VMs $vm
    #  After the VM is created, the extension downloads the script from the storage location and executes it on the VM.

    # Viewing the  script execution output.
    $vm = Get-AzureVM -ServiceName $servicename -Name $name
    # Use the position of the extension in the output as index.
    $vm.ResourceExtensionStatusList[i].ExtensionSettingStatus.SubStatusList

### <a name="upload-files-to-a-non-default-storage-container"></a>Cargar archivos en un contenedor de almacenamiento no predeterminada

Este escenario muestra cómo usar un contenedor de almacenamiento no predeterminada dentro de la misma suscripción o en una suscripción diferente para cargar archivos y las secuencias de comandos. Este ejemplo muestra una máquina virtual existente, pero se pueden realizar las mismas operaciones mientras está creando una máquina virtual.

        Get-AzureVM -Name $name -ServiceName $servicename | Set-AzureVMCustomScriptExtension -StorageAccountName $storageaccount -StorageAccountKey $storagekey -ContainerName $container -FileName 'file1.ps1','file2.ps1' -Run 'file.ps1' | Update-AzureVM

### <a name="upload-scripts-to-multiple-containers-across-different-storage-accounts"></a>Cargar secuencias de comandos en varios contenedores entre cuentas de almacenamiento diferente

  Si se almacenan los archivos de script a través de varios contenedores, debe proporcionar la URL de firmas (SA) acceso compartido completo para los archivos para ejecutar las secuencias de comandos.

      Get-AzureVM -Name $name -ServiceName $servicename | Set-AzureVMCustomScriptExtension -StorageAccountName $storageaccount -StorageAccountKey $storagekey -ContainerName $container -FileUri $fileUrl1, $fileUrl2 -Run 'file.ps1' | Update-AzureVM


### <a name="add-the-custom-script-extension-from-the-azure-portal"></a>Agregar la extensión de Script personalizado desde el portal de Azure

Vaya a la máquina virtual en el <a href="https://portal.azure.com/ " target="_blank">portal de Azure</a> y agregue la extensión especificando el archivo de comandos para ejecutar.

  ![Especifique el archivo de script][5]


### <a name="uninstall-the-custom-script-extension"></a>Desinstale la extensión de Script personalizado

Puede desinstalar la extensión de Script personalizado de la máquina virtual mediante el comando siguiente.

      get-azureVM -ServiceName KPTRDemo -Name KPTRDemo | Set-AzureVMCustomScriptExtension -Uninstall | Update-AzureVM

### <a name="use-the-custom-script-extension-with-templates"></a>Usar la extensión de Script personalizado con plantillas

Para obtener información sobre cómo usar la extensión de Script personalizado con las plantillas de administrador de recursos de Azure, consulte [uso de la extensión de Script personalizado para máquinas virtuales de Windows con las plantillas de administrador de recursos de Azure](virtual-machines-windows-extensions-customscript.md).

<!--Image references-->
[5]: ./media/virtual-machines-windows-classic-extensions-customscript/addcse.png
