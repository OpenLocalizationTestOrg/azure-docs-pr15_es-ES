<properties
    pageTitle="Implementar plantillas de PowerShell en pila de Azure | Microsoft Azure"
    description="Obtenga información sobre cómo implementar una máquina virtual mediante una plantilla de administrador de recursos y PowerShell."
    services="azure-stack"
    documentationCenter=""
    authors="heathl17"
    manager="byronr"
    editor=""/>

<tags
    ms.service="azure-stack"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/10/2016"
    ms.author="helaw"/>

# <a name="deploy-templates-in-azure-stack-using-powershell"></a>Implementar plantillas de pila de Azure con PowerShell

Usar PowerShell para implementar plantillas de administrador de recursos de Azure en la prueba de concepto de pila de Azure.  Plantillas de administrador de recursos implementación y aprovisionar todos los recursos para la aplicación en una única operación coordinada.

## <a name="run-azurerm-powershell-cmdlets"></a>Ejecute los cmdlets de AzureRM PowerShell

En este ejemplo, ejecute una secuencia de comandos para implementar una máquina virtual a prueba de concepto de pila de Azure usando una plantilla de administrador de recursos.  Antes de continuar, asegúrese de que tiene [instalado y configurado PowerShell](azure-stack-connect-powershell.md)  

El disco duro virtual utilizado en esta plantilla de ejemplo es una imagen de marketplace predeterminada (Windows Server 2012-R2-centro de datos).

1.  Vaya a <http://aka.ms/AzureStackGitHub>, busque la plantilla de **101 simple windows vm** y guardarla en la siguiente ubicación: c:\\plantillas\\azuredeploy 101 simple windows vm.json.

2.  En PowerShell, ejecute el siguiente script de implementación. Reemplace el *nombre de usuario* y *contraseña* con su nombre de usuario y contraseña. En los usos posteriores, incrementar el valor del parámetro *$myNum* evitar que se sobrescriba la implementación.

    ```PowerShell
        # Set Deployment Variables
        $myNum = "001" #Modify this per deployment
        $RGName = "myRG$myNum"
        $myLocation = "local"

        # Create Resource Group for Template Deployment
        New-AzureRmResourceGroup -Name $RGName -Location $myLocation

        # Deploy Simple IaaS Template
        New-AzureRmResourceGroupDeployment `
            -Name myDeployment$myNum `
            -ResourceGroupName $RGName `
            -TemplateFile c:\templates\azuredeploy-101-simple-windows-vm.json `
            -NewStorageAccountName mystorage$myNum `
            -DnsNameForPublicIP mydns$myNum `
            -AdminUsername <username> `
            -AdminPassword ("<password>" | ConvertTo-SecureString -AsPlainText -Force) `
            -VmName myVM$myNum `
            -WindowsOSVersion 2012-R2-Datacenter
    ```

3.  Abra el portal de pila de Azure, haga clic en **Examinar**, haga clic en **máquinas virtuales**y busque su nueva máquina virtual (*myDeployment001*).

## <a name="video-example-hybrid-virtual-machine-deployment"></a>Ejemplo de vídeo: implementación híbrida de máquina virtual

[AZURE.VIDEO microsoft-azure-stack-tp1-poc-hybrid-vm-deployment]

## <a name="next-steps"></a>Pasos siguientes

[Implementar plantillas de Visual Studio](azure-stack-deploy-template-visual-studio.md)
