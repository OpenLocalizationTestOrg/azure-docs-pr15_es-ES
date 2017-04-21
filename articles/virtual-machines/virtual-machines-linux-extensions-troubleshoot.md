<properties
   pageTitle="Solución de problemas de extensión Linux VM | Microsoft Azure"
   description="Obtenga información acerca de cómo solucionar errores de extensión de Azure Linux VM"
   services="virtual-machines-linux"
   documentationCenter=""
   authors="kundanap"
   manager="timlt"
   editor=""
   tags="top-support-issue,azure-resource-manager"/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="na"
   ms.topic="support-article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure-services"
   ms.date="03/29/2016"
   ms.author="kundanap"/>

# <a name="troubleshooting-azure-linux-vm-extension-failures"></a>Solución de problemas de extensión de Azure Linux VM

[AZURE.INCLUDE [virtual-machines-common-extensions-troubleshoot](../../includes/virtual-machines-common-extensions-troubleshoot.md)]

## <a name="viewing-extension-status"></a>Ver el estado de extensión
Plantillas de Azure Administrador de recursos se pueden ejecutar desde la CLI de Azure. Una vez que se ejecuta la plantilla, el estado de extensión puede verse desde el Explorador de recursos de Azure o las herramientas de línea de comandos.

Aquí tenemos un ejemplo:

Azure CLI:

      azure vm get-instance-view


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

## <a name="troubleshooting-extenson-failures"></a>Solución de problemas de Extenson:

### <a name="re-running-the-extension-on-the-vm"></a>Volver a ejecutar la extensión en la máquina virtual

Si está ejecutando las secuencias de comandos en la máquina virtual con la extensión de Script personalizado, pueden surgir en ocasiones, un error donde VM se creó correctamente pero no se pudo realizar la secuencia de comandos. En estas condiciones, la manera recomendada para recuperar de este error es quitar la extensión y vuelva a ejecutar la plantilla.
Nota: en el futuro, esta funcionalidad se verán mejorada para quitar la necesidad de desinstalar la extensión.

#### <a name="remove-the-extension-from-azure-cli"></a>Quitar la extensión de CLI de Azure

      azure vm extension set --resource-group "KPRG1" --vm-name "kundanapdemo" --publisher-name "Microsoft.Compute.CustomScriptExtension" --name "myCustomScriptExtension" --version 1.4 --uninstall

Donde "nombre de publsher" correspondiente al tipo de extensión de los resultados de "azure vm get-instancia-view" y nombre es el nombre del recurso de extensión de la plantilla

Una vez que se ha quitado la extensión, la plantilla puede volver a ejecutar para ejecutar las secuencias de comandos en la máquina virtual.
