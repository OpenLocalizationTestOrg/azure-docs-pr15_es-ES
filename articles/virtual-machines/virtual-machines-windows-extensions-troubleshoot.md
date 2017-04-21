<properties
   pageTitle="Solución de problemas de extensión de máquina virtual de Windows | Microsoft Azure"
   description="Obtenga información acerca de cómo solucionar errores de extensión de VM en Windows Azure"
   services="virtual-machines-windows"
   documentationCenter=""
   authors="kundanap"
   manager="timlt"
   editor=""
   tags="top-support-issue,azure-resource-manager"/>

<tags
   ms.service="virtual-machines-windows"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-windows"
   ms.workload="infrastructure-services"
   ms.date="03/29/2016"
   ms.author="kundanap"/>

# <a name="troubleshooting-azure-windows-vm-extension-failures"></a>Solución de problemas de extensión de VM en Windows Azure

[AZURE.INCLUDE [virtual-machines-common-extensions-troubleshoot](../../includes/virtual-machines-common-extensions-troubleshoot.md)]

## <a name="viewing-extension-status"></a>Ver el estado de extensión
Azure plantillas de administrador de recursos se pueden ejecutar desde PowerShell de Azure. Una vez que se ejecuta la plantilla, el estado de extensión puede verse desde el Explorador de recursos de Azure o las herramientas de línea de comandos.

Aquí tenemos un ejemplo:

Azure PowerShell:

      Get-AzureRmVM -ResourceGroupName $RGName -Name $vmName -Status

Esto es el resultado de ejemplo:

      Extensions:  {
      "ExtensionType": "Microsoft.Compute.CustomScriptExtension",
      "Name": "myCustomScriptExtension",
      "SubStatuses": [
        {
          "Code": "ComponentStatus/StdOut/succeeded",
          "DisplayStatus": "Provisioning succeeded",
          "Level": "Info",
          "Message": "    Directory: C:\\temp\\n\\n\\nMode                LastWriteTime     Length Name
              \\n----                -------------     ------ ----                              \\n-a---          9/1/2015   2:03 AM         11
              test.txt                          \\n\\n",
                      "Time": null
          },
        {
          "Code": "ComponentStatus/StdErr/succeeded",
          "DisplayStatus": "Provisioning succeeded",
          "Level": "Info",
          "Message": "",
          "Time": null
        }
    }
  ]

## <a name="troubleshooting-extension-failures"></a>Solución de problemas de extensión

### <a name="re-running-the-extension-on-the-vm"></a>Volver a ejecutar la extensión en la máquina virtual

Si está ejecutando las secuencias de comandos en la máquina virtual con la extensión de Script personalizado, pueden surgir en ocasiones, un error donde VM se creó correctamente pero no se pudo realizar la secuencia de comandos. En estas condiciones, la manera recomendada para recuperar de este error es quitar la extensión y vuelva a ejecutar la plantilla.
Nota: en el futuro, esta funcionalidad se verán mejorada para quitar la necesidad de desinstalar la extensión.


#### <a name="remove-the-extension-from-azure-powershell"></a>Quitar la extensión de PowerShell de Azure

    Remove-AzureRmVMExtension -ResourceGroupName $RGName -VMName $vmName -Name "myCustomScriptExtension"

Una vez que se ha quitado la extensión, la plantilla puede volver a ejecutar para ejecutar las secuencias de comandos en la máquina virtual.
