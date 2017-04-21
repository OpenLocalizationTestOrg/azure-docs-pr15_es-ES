<properties
   pageTitle="Ejemplo de configuración para las extensiones de Linux VM | Microsoft Azure"
   description="Ejemplo de configuración para la creación de plantillas con extensiones para máquinas virtuales de Linux"
   services="virtual-machines-linux"
   documentationCenter=""
   authors="kundanap"
   manager="timlt"
   editor=""
   tags="azure-resource-manager"/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure-services"
   ms.date="09/13/2016"
   ms.author="kundanap"/>

# <a name="linux-vm-extension-configuration-samples"></a>Ejemplos de configuración de extensión Linux VM

> [AZURE.SELECTOR]
- [PowerShell - plantilla](virtual-machines-windows-extensions-configuration-samples.md)
- [CLI - plantilla](virtual-machines-linux-extensions-configuration-samples.md)

<br>

Este artículo proporciona la configuración de ejemplo para configurar las extensiones de Azure VM para máquinas virtuales de Linux.

Para obtener más información sobre estas extensiones haga clic aquí: [Introducción a las extensiones Azure VM.](virtual-machines-windows-extensions-features.md)

Para más información sobre la extensión de creación de plantillas, haga clic aquí: [de creación de plantillas de extensión.](virtual-machines-windows-extensions-authoring-templates.md)

Este artículo enumeran los valores de configuración esperado para algunas de las extensiones de Linux.

## <a name="sample-template-snippet-for-vm-extensions"></a>Fragmento de código de plantilla de ejemplo para VM extensiones.
Fragmento de código de plantilla para implementar extensiones aspectos como la siguiente:

      {
      "type": "Microsoft.Compute/virtualMachines/extensions",
      "name": "MyExtension",
      "apiVersion": "2015-05-01-preview",
      "location": "[parameters('location')]",
      "dependsOn": ["[concat('Microsoft.Compute/virtualMachines/',parameters('vmName'))]"],
      "properties":
      {
      "publisher": "Publisher Namespace",
      "type": "extension Name",
      "typeHandlerVersion": "extension version",
      "autoUpgradeMinorVersion":true,
      "settings": {
      // Extension specific configuration goes in here.
      }
      }
      }

## <a name="sample-template-snippet-for-vm-extensions-with-vm-scale-sets"></a>Fragmento de código de plantilla de muestra para extensiones de VM con conjuntos de escala de máquina virtual.

          {
           "type":"Microsoft.Compute/virtualMachineScaleSets",
          ....
                 "extensionProfile":{
                 "extensions":[
                   {
                     "name":"extension Name",
                     "properties":{
                       "publisher":"Publisher Namespace",
                       "type":"extension Name",
                       "typeHandlerVersion":"extension version",
                       "autoUpgradeMinorVersion":true,
                       "settings":{
                       // Extension specific configuration goes in here.
                       }
                     }
                    }
                  }
                }

Antes de implementar la extensión, compruebe la última versión de extensión y reemplace "typeHandlerVersion" con la última versión actual.

El resto de este artículo proporciona ejemplos de configuraciones para las extensiones de máquina virtual Linux.

### <a name="cloudlink-securevm-agent"></a>Agente de SecureVM CloudLink
          {
            "publisher": "CloudLinkEMC.SecureVM",
            "type": "CloudLinkSecureVMLinuxAgent",
            "typeHandlerVersion": "4.0",
            "settings": {
              "CloudLinkCenter" : "specify valid IP/FQDN to CloudLinkCenter"
            }
          }

### <a name="customscript-extension-for-linux"></a>Extensión de CustomScript para Linux.
    {
        "publisher": " Microsoft.Azure.Extensions",
        "type": "CustomScript",
        "typeHandlerVersion": "2.0",
        "autoUpgradeMinorVersion": true,
        "settings": {
            "fileUris": [
                "http: //Yourstorageaccount.blob.core.windows.net/customscriptfiles/start.ps1"
            ],
            "commandToExecute": "powershell.exe-ExecutionPolicyUnrestricted-Filestart.ps1"
        }
    }


### <a name="datadog-agent"></a>Agente de Datadog
        {
          "publisher": "Datadog.Agent",
          "type": "DatadogLinuxAgent",
          "typeHandlerVersion": "0.4",
          "settings": {
            "api_key" : "API Key from https://app.datadoghq.com/account/settings#api"
          }
        }

### <a name="chef-agent"></a>Agente de Chef
        {
          "publisher": "Chef.Bootstrap.WindowsAzure",
          "type": "CentosChefClient|LinuxChefClient",
          "typeHandlerVersion": "1210.12",
          "settings": {
            "validation_key" : " Validation key",
            "client_rb" : "client_rb file",
            "runlist" : "Optional runlist"
          }
        }

### <a name="vm-access-extension-password-reset"></a>Extensión de acceso VM (restablecimiento de contraseña)
Para esquema actualizado, consulte la [Documentación de VMAccessForLinux](https://github.com/Azure/azure-linux-extensions/tree/master/VMAccess)

        {
          "publisher": "Microsoft.OSTCExtensions",
          "type": "VMAccessForLinux",
          "typeHandlerVersion": "1.2",
          "protectedSettings": {
            "username": "(required, string) the name of the user",
            "password": "(optional, string) the password of the user",
            "reset_ssh": "(optional, boolean) whether or not reset the ssh",
            "ssh_key": "(optional, string) the public key of the user, base64 encoded pem",
            "remove_user": "(optional, string) the user name to remove"
          }
        }

### <a name="os-patching"></a>Revisiones de sistema operativo
Para esquema actualizado, consulte la [Documentación de OSPatching](https://github.com/Azure/azure-linux-extensions/tree/master/OSPatching)

        {
        "publisher": "Microsoft.OSTCExtensions",
        "type": "OSPatchingForLinux",
        "typeHandlerVersion": "2.9",
        "Settings": {
          "disabled": false,
          "stop": false,
          "rebootAfterPatch": "RebootIfNeed|Required|NotRequired|Auto",
          "category": "Important|ImportantAndRecommended",
          "installDuration": "<hr:min>",
          "oneoff": false,
          "intervalOfWeeks": "<number>",
          "dayOfWeek": "Sunday|Monday|Tuesday|Wednesday|Thursday|Friday|Saturday|Everyday",
          "startTime": "<hr:min>",
          "vmStatusTest": {
              "local": false,
              "idleTestScript": "<path_to_idletestscript>",
              "healthyTestScript": "<path_to_healthytestscript>"
          }
        }
        }

### <a name="docker-extension"></a>Extensión de docker
Para esquema actualizado, consulte la [Documentación de extensión Docker](https://github.com/Azure/azure-docker-extension/blob/master/README.md#1-configuration-schema)

        {
          "publisher": "Microsoft.Azure.Extensions ",
          "type": "DockerExtension ",
          "typeHandlerVersion": "1.0",
          "Settings": {
            "docker":{
                "port": "2376",
                "options": ["-D", "--dns=8.8.8.8"]
            },
            "compose": {
                "cache" : {
                    "image" : "memcached",
                    "ports" : ["11211:11211"]
                },
                "blog": {
                    "image": "ghost",
                    "ports": ["80:2368"]
                }
            }
            }
        }

        ### Linux Diagnostics Extension
        {
        "storageAccountName": "storage account to receive data",
        "storageAccountKey": "key of the account",
        "perfCfg": [
        {
            "query": "SELECT PercentAvailableMemory, AvailableMemory, UsedMemory ,PercentUsedSwap FROM SCX_MemoryStatisticalInformation",
            "table": "LinuxMemory"
        }
        ],
        "fileCfg": [
        {
            "file": "/var/log/mysql.err",
            "table": "mysqlerr"
        }
        ]
        }

En los ejemplos anteriores, reemplace el número de versión con el número de versión más reciente.

Aquí es una plantilla de máquina virtual completa para crear una VM Linux con una extensión:

[Extensión de scripts personalizados en una máquina virtual de Linux](https://github.com/Azure/azure-quickstart-templates/blob/b1908e74259da56a92800cace97350af1f1fc32b/mongodb-on-ubuntu/azuredeploy.json/)
