<properties
   pageTitle="Mediante el estado de configuración con conjuntos de escala de máquina Virtual de deseado | Microsoft Azure"
   description="Uso de la escala de la máquina Virtual establece con la extensión DSC Azure"
   services="virtual-machine-scale-sets"
   documentationCenter=""
   authors="zjalexander"
   manager="timlt"
   editor=""
   tags="azure-service-management,azure-resource-manager"
   keywords=""/>

<tags
   ms.service="virtual-machine-scale-sets"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-windows"
   ms.workload="na"
   ms.date="09/15/2016"
   ms.author="zachal"/>

# <a name="using-virtual-machine-scale-sets-with-the-azure-dsc-extension"></a>Uso de la escala de la máquina Virtual establece con la extensión DSC Azure

[Conjuntos de escala de máquina virtual (VMSS)](virtual-machine-scale-sets-overview.md) pueden utilizarse con el controlador de extensión de [Configuración de estado deseado de Azure (DSC)](../virtual-machines/virtual-machines-windows-extensions-dsc-overview.md) . VMSS proporciona una manera de implementar y administrar un gran número de máquinas virtuales y elástica puede escalar y alejar en respuesta a cargar. DSC se utiliza para configurar las máquinas virtuales a medida que se incluyen en línea, de manera que están ejecutando el software de producción.

## <a name="differences-between-deploying-to-vm-and-vmss"></a>Diferencias entre la implementación de máquina virtual y VMSS

La estructura de la plantilla subyacente de VMSS es ligeramente diferente desde una única VM. Más concretamente, una única VM implementa extensiones bajo el nodo "virtualMachines". Hay una entrada del tipo "extensiones" donde DSC se agrega a la plantilla

```
"resources": [
          {
              "name": "Microsoft.Powershell.DSC",
              "type": "extensions",
              "location": "[resourceGroup().location]",
              "apiVersion": "2015-06-15",
              "dependsOn": [
                  "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
              ],
              "tags": {
                  "displayName": "dscExtension"
              },
              "properties": {
                  "publisher": "Microsoft.Powershell",
                  "type": "DSC",
                  "typeHandlerVersion": "2.20",
                  "autoUpgradeMinorVersion": false,
                  "forceUpdateTag": "[parameters('dscExtensionUpdateTagVersion')]",
                  "settings": {
                      "configuration": {
                          "url": "[concat(parameters('_artifactsLocation'), '/', variables('dscExtensionArchiveFolder'), '/', variables('dscExtensionArchiveFileName'))]",
                          "script": "DscExtension.ps1",
                          "function": "Main"
                      },
                      "configurationArguments": {
                          "nodeName": "[variables('vmName')]"
                      }
                  },
                  "protectedSettings": {
                      "configurationUrlSasToken": "[parameters('_artifactsLocationSasToken')]"
                  }
              }
          }
      ]
```

Un nodo VMSS tiene una sección de "propiedades de" con el "VirtualMachineProfile", "extensionProfile" atributo. DSC se agrega con las extensiones de""

```
"extensionProfile": {
            "extensions": [
                {
                    "name": "Microsoft.Powershell.DSC",
                    "properties": {
                        "publisher": "Microsoft.Powershell",
                        "type": "DSC",
                        "typeHandlerVersion": "2.20",
                        "autoUpgradeMinorVersion": false,
                        "forceUpdateTag": "[parameters('DscExtensionUpdateTagVersion')]",
                        "settings": {
                            "configuration": {
                                "url": "[concat(parameters('_artifactsLocation'), '/', variables('DscExtensionArchiveFolder'), '/', variables('DscExtensionArchiveFileName'))]",
                                "script": "DscExtension.ps1",
                                "function": "Main"
                            },
                            "configurationArguments": {
                                "nodeName": "localhost"
                            }
                        },
                        "protectedSettings": {
                            "configurationUrlSasToken": "[parameters('_artifactsLocationSasToken')]"
                        }
                    }
                }
            ]
```

## <a name="behavior-for-vmss"></a>Comportamiento de VMSS

El comportamiento de VMSS es idéntico al comportamiento para una única VM. Cuando se crea una nueva máquina virtual, se le proporciona automáticamente con la extensión DSC. Si se requiere una versión más reciente del WMF por la extensión, reinicia la máquina virtual antes de conectarse. Una vez que está en línea, descargas la .zip configuración DSC y aprovisionar en la máquina virtual. Encontrará más detalles en [La visión general de Azure DSC extensión](../virtual-machines/virtual-machines-windows-extensions-dsc-overview.md).

## <a name="next-steps"></a>Pasos siguientes ##
Examine la [plantilla de administrador de recursos de Azure para la extensión DSC](../virtual-machines/virtual-machines-windows-extensions-dsc-template.md).

Obtenga información sobre cómo la [extensión DSC controladores de forma segura las credenciales](../virtual-machines/virtual-machines-windows-extensions-dsc-credentials.md). 

Para obtener más información sobre el controlador de extensión DSC de Azure, vea [Introducción al controlador de extensión de configuración de estado de Azure deseado](../virtual-machines/virtual-machines-windows-extensions-dsc-overview.md). 

Para obtener más información acerca de PowerShell DSC, [visite el centro de documentación de PowerShell](https://msdn.microsoft.com/powershell/dsc/overview). 


