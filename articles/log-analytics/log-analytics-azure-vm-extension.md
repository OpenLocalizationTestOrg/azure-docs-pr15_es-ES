<properties
    pageTitle="Conectar máquinas virtuales de Windows Azure para el análisis de registro | Microsoft Azure"
    description="Para Windows y Linux máquinas virtuales que ejecutan en Azure, la manera recomendada de registros recopilados y métricas es instalar la extensión de VM de Azure de análisis de registro. Puede usar el portal de Azure o PowerShell para instalar la extensión de máquina virtual de análisis de registro en máquinas virtuales de Azure."
    services="log-analytics"
    documentationCenter=""
    authors="richrundmsft"
    manager="jochan"
    editor=""/>

<tags
    ms.service="log-analytics"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/10/2016"
    ms.author="richrund"/>

# <a name="connect-azure-virtual-machines-to-log-analytics"></a>Conectar máquinas virtuales de Windows Azure para el análisis de registro

Para equipos de Windows y Linux, el método recomendado para recopilar registros y mediciones está instalando el agente de análisis de registro.

La manera más sencilla de instalar al agente de análisis de registro en máquinas virtuales de Windows Azure es a través de la extensión de máquina virtual de análisis de registro.  Con la extensión simplifica el proceso de instalación y configurará automáticamente el agente para enviar datos al área de trabajo de análisis de registro que se especifique. El agente también se actualiza automáticamente, asegurarse de que tiene las últimas características y soluciones.

Máquinas virtuales de Windows, habilite la extensión de máquina virtual de *Microsoft Agent supervisión* .
Máquinas virtuales de Linux, habilite la extensión de máquina virtual *OMS agente para Linux* .

Más información sobre [las extensiones de máquina virtual de Azure](../virtual-machines/virtual-machines-windows-extensions-features.md) y [agente de Linux] (.. / virtual-machines/virtual-machines-linux-agent-user-guide.md).

Cuando utiliza la colección de agente de datos de registro, debe configurar los [orígenes de datos en el análisis de registro](log-analytics-data-sources.md) para especificar los registros y métricas que se desean recopilar.

>[AZURE.IMPORTANT] Si configura el análisis de registro para indizar los datos de registro mediante el uso de [Diagnósticos de Azure](log-analytics-azure-storage.md)y configurar el agente para recopilar los registros de la misma, se recopilan los registros de dos veces. Se cargan de ambos orígenes de datos. Si tiene instalado el agente, a continuación, debe recopilar datos de registro mediante el agente solo - no configurar el análisis de registro para recopilar datos de registro de diagnóstico de Azure.

Existen tres métodos sencillos para habilitar la extensión de máquina virtual de análisis de registro:

+ Mediante el portal de Azure
+ Con PowerShell de Azure
+ Mediante una plantilla de administrador de recursos de Azure

## <a name="enable-the-vm-extension-in-the-azure-portal"></a>Habilitar la extensión VM en el portal de Azure

Puede instalar al agente de análisis de registro y conectar la máquina virtual Azure que se ejecuta en mediante el [portal de Azure](https://portal.azure.com).

### <a name="to-install-the-log-analytics-agent-and-connect-the-virtual-machine-to-a-log-analytics-workspace"></a>Para instalar al agente de análisis de registro y conectar la máquina virtual a un área de trabajo de análisis de registro

1.  Iniciar sesión en el [portal de Azure](http://portal.azure.com).
2.  Seleccione **Examinar** en el lado izquierdo del portal y, a continuación, vaya a **Análisis de registro (OMS)** y selecciónelo.
3.  En la lista de áreas de trabajo de análisis de registro, seleccione el elemento que desea usar con la máquina virtual de Azure.  
    ![Áreas de trabajo OMS](./media/log-analytics-azure-vm-extension/oms-connect-azure-01.png)
4.  En **administración de análisis de registro**, seleccione **máquinas virtuales de Windows**.  
    ![Máquinas virtuales de Windows](./media/log-analytics-azure-vm-extension/oms-connect-azure-02.png)
5.  En la lista de **máquinas virtuales de Windows**, seleccione la máquina virtual en el que desea instalar al agente. El **estado de conexión de OMS** de la máquina virtual indica que está **sin conexión**.  
    ![VM no conectado](./media/log-analytics-azure-vm-extension/oms-connect-azure-03.png)
6.  En los detalles de la máquina virtual, seleccione **Conectar**. El agente automáticamente está instalado y configurado para el área de trabajo de análisis de registro. Este proceso tarda unos minutos, durante el cual el estado de conexión de OMS está *conectando...*  
    ![Conectar VM](./media/log-analytics-azure-vm-extension/oms-connect-azure-04.png)
7.  Después de instalar y conectar al agente, el estado de **conexión de OMS** se actualizará para mostrar **esta área de trabajo**.  
    ![Conectado](./media/log-analytics-azure-vm-extension/oms-connect-azure-05.png)


## <a name="enable-the-vm-extension-using-powershell"></a>Habilitar la extensión VM con PowerShell

Hay comandos para máquinas virtuales clásica de Windows Azure y máquinas virtuales de administrador de recursos. Siguientes son ejemplos de clásica y máquinas virtuales de administrador de recursos.

Para equipos virtuales clásicos, utilice el siguiente ejemplo de PowerShell:

```
Add-AzureAccount

$workspaceId = "enter workspace ID here"
$workspaceKey = "enter workspace key here"
$hostedService = "enter hosted service here"

$vm = Get-AzureVM –ServiceName $hostedService

# For Windows VM uncomment the following line
# Set-AzureVMExtension -VM $vm -Publisher 'Microsoft.EnterpriseCloud.Monitoring' -ExtensionName 'MicrosoftMonitoringAgent' -Version '1.*' -PublicConfiguration "{'workspaceId': '$workspaceId'}" -PrivateConfiguration "{'workspaceKey': '$workspaceKey' }" | Update-AzureVM -Verbose

# For Linux VM uncomment the following line
# Set-AzureVMExtension -VM $vm -Publisher 'Microsoft.EnterpriseCloud.Monitoring' -ExtensionName 'OmsAgentForLinux' -Version '1.*' -PublicConfiguration "{'workspaceId': '$workspaceId'}" -PrivateConfiguration "{'workspaceKey': '$workspaceKey' }" | Update-AzureVM -Verbose
```

Máquinas virtuales de administrador de recursos, utilice el siguiente ejemplo de PowerShell:

```
Login-AzureRMAccount
Select-AzureSubscription -SubscriptionId "**"

$workspaceName = "your workspace name"
$VMresourcegroup = "**"
$VMresourcename = "**"

$workspace = (Get-AzureRmOperationalInsightsWorkspace).Where({$_.Name -eq $workspaceName})

if ($workspace.Name -ne $workspaceName)
{
    Write-Error "Unable to find OMS Workspace $workspaceName. Do you need to run Select-AzureRMSubscription?"
}

$workspaceId = $workspace.CustomerId
$workspaceKey = (Get-AzureRmOperationalInsightsWorkspaceSharedKeys -ResourceGroupName $workspace.ResourceGroupName -Name $workspace.Name).PrimarySharedKey

$vm = Get-AzureRmVM -ResourceGroupName $VMresourcegroup -Name $VMresourcename
$location = $vm.Location

# For Windows VM uncomment the following line
# Set-AzureRmVMExtension -ResourceGroupName $VMresourcegroup -VMName $VMresourcename -Name 'MicrosoftMonitoringAgent' -Publisher 'Microsoft.EnterpriseCloud.Monitoring' -ExtensionType 'MicrosoftMonitoringAgent' -TypeHandlerVersion '1.0' -Location $location -SettingString "{'workspaceId': '$workspaceId'}" -ProtectedSettingString "{'workspaceKey': '$workspaceKey'}"

# For Linux VM uncomment the following line
# Set-AzureRmVMExtension -ResourceGroupName $VMresourcegroup -VMName $VMresourcename -Name 'OmsAgentForLinux' -Publisher 'Microsoft.EnterpriseCloud.Monitoring' -ExtensionType 'OmsAgentForLinux' -TypeHandlerVersion '1.0' -Location $location -SettingString "{'workspaceId': '$workspaceId'}" -ProtectedSettingString "{'workspaceKey': '$workspaceKey'}"


```
Cuando configura su máquina virtual con PowerShell, debe proporcionar el **Identificador de área de trabajo** y la **Clave principal**. Puede encontrar el identificador y la clave en la página de **configuración** del portal OMS o con PowerShell como se muestra en el ejemplo anterior.

![Id. de área de trabajo y la clave principal](./media/log-analytics-azure-vm-extension/oms-analyze-azure-sources.png)

## <a name="deploy-the-vm-extension-using-a-template"></a>Implementar la extensión VM usando una plantilla

Mediante el Administrador de recursos de Azure, puede crear una plantilla simple (en formato JSON) que define la implementación y configuración de la aplicación. Esta plantilla se conoce como una plantilla de administrador de recursos y proporciona una manera descriptiva para definir implementación. Varias veces mediante una plantilla, puede implementar la aplicación en todo el ciclo de vida de la aplicación y tiene confianza que se implementan los recursos en un estado coherente.

Como parte de la plantilla de administrador de recursos, incluyendo el agente de análisis de registro, puede asegurarse de que cada máquina virtual está preconfigurado para informar de su área de trabajo de análisis de registro.

Para obtener más información acerca de las plantillas de administrador de recursos, vea [crear un administrador de recursos de Azure plantillas](../resource-group-authoring-templates.md).

A continuación se muestra un ejemplo de una plantilla de administrador de recursos que se usa para implementar una máquina virtual que se está ejecutando Windows con la extensión de supervisión de Microsoft Agent instalada. Esta es una plantilla típica máquina virtual, con las siguientes excepciones:

+ parámetros workspaceId y workspaceName
+ Sección de extensión de recursos Microsoft.EnterpriseCloud.Monitoring
+ Resultados para buscar la workspaceId y workspaceSharedKey


```
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "adminUsername": {
      "type": "string",
      "metadata": {
        "description": "Username for the Virtual Machine."
      }
    },
    "adminPassword": {
      "type": "securestring",
      "metadata": {
        "description": "Password for the Virtual Machine."
      }
    },
    "dnsLabelPrefix": {
       "type": "string",
       "metadata": {
          "description": "DNS Label for the Public IP. Must be lowercase. It should match with the following regular expression: ^[a-z][a-z0-9-]{1,61}[a-z0-9]$ or it will raise an error."
       }
    },
    "workspaceId": {
      "type": "string",
      "metadata": {
        "description": "OMS workspace ID"
      }
    },
    "workspaceName": {
      "type": "string",
      "metadata": {
         "description": "OMD workspace name"
      }
    },
    "windowsOSVersion": {
      "type": "string",
      "defaultValue": "2012-R2-Datacenter",
      "allowedValues": [
        "2008-R2-SP1",
        "2012-Datacenter",
        "2012-R2-Datacenter",
        "Windows-Server-Technical-Preview"
      ],
      "metadata": {
        "description": "The Windows version for the VM. This will pick a fully patched image of this given Windows version. Allowed values: 2008-R2-SP1, 2012-Datacenter, 2012-R2-Datacenter, Windows-Server-Technical-Preview."
      }
    }
  },
  "variables": {
    "storageAccountName": "[concat(uniquestring(resourceGroup().id), 'standardsa')]",
    "apiVersion": "2015-06-15",
    "imagePublisher": "MicrosoftWindowsServer",
    "imageOffer": "WindowsServer",
    "OSDiskName": "osdiskforwindowssimple",
    "nicName": "myVMNic",
    "addressPrefix": "10.0.0.0/16",
    "subnetName": "Subnet",
    "subnetPrefix": "10.0.0.0/24",
    "storageAccountType": "Standard_LRS",
    "publicIPAddressName": "myPublicIP",
    "publicIPAddressType": "Dynamic",
    "vmStorageAccountContainerName": "vhds",
    "vmName": "MyWindowsVM",
    "vmSize": "Standard_DS1",
    "virtualNetworkName": "MyVNET",
    "resourceId": "[resourceGroup().id]",
    "vnetID": "[resourceId('Microsoft.Network/virtualNetworks',variables('virtualNetworkName'))]",
    "subnetRef": "[concat(variables('vnetID'),'/subnets/',variables('subnetName'))]"
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "name": "[variables('storageAccountName')]",
      "apiVersion": "[variables('apiVersion')]",
      "location": "[resourceGroup().location]",
      "properties": {
        "accountType": "[variables('storageAccountType')]"
      }
    },
    {
      "apiVersion": "[variables('apiVersion')]",
      "type": "Microsoft.Network/publicIPAddresses",
      "name": "[variables('publicIPAddressName')]",
      "location": "[resourceGroup().location]",
      "properties": {
        "publicIPAllocationMethod": "[variables('publicIPAddressType')]",
        "dnsSettings": {
          "domainNameLabel": "[parameters('dnsLabelPrefix')]"
        }
      }
    },
    {
      "apiVersion": "[variables('apiVersion')]",
      "type": "Microsoft.Network/virtualNetworks",
      "name": "[variables('virtualNetworkName')]",
      "location": "[resourceGroup().location]",
      "properties": {
        "addressSpace": {
          "addressPrefixes": [
            "[variables('addressPrefix')]"
          ]
        },
        "subnets": [
          {
            "name": "[variables('subnetName')]",
            "properties": {
              "addressPrefix": "[variables('subnetPrefix')]"
            }
          }
        ]
      }
    },
    {
      "apiVersion": "[variables('apiVersion')]",
      "type": "Microsoft.Network/networkInterfaces",
      "name": "[variables('nicName')]",
      "location": "[resourceGroup().location]",
      "dependsOn": [
        "[concat('Microsoft.Network/publicIPAddresses/', variables('publicIPAddressName'))]",
        "[concat('Microsoft.Network/virtualNetworks/', variables('virtualNetworkName'))]"
      ],
      "properties": {
        "ipConfigurations": [
          {
            "name": "ipconfig1",
            "properties": {
              "privateIPAllocationMethod": "Dynamic",
              "publicIPAddress": {
                "id": "[resourceId('Microsoft.Network/publicIPAddresses',variables('publicIPAddressName'))]"
              },
              "subnet": {
                "id": "[variables('subnetRef')]"
              }
            }
          }
        ]
      }
    },
    {
      "apiVersion": "2015-06-15",
      "type": "Microsoft.Compute/virtualMachines",
      "name": "[variables('vmName')]",
      "location": "[resourceGroup().location]",
      "dependsOn": [
        "[concat('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))]",
        "[concat('Microsoft.Network/networkInterfaces/', variables('nicName'))]"
      ],
      "properties": {
        "hardwareProfile": {
          "vmSize": "[variables('vmSize')]"
        },
        "osProfile": {
          "computername": "[variables('vmName')]",
          "adminUsername": "[parameters('adminUsername')]",
          "adminPassword": "[parameters('adminPassword')]"
        },
        "storageProfile": {
          "imageReference": {
            "publisher": "[variables('imagePublisher')]",
            "offer": "[variables('imageOffer')]",
            "sku": "[parameters('windowsOSVersion')]",
            "version": "latest"
          },
          "osDisk": {
            "name": "osdisk",
            "vhd": {
              "uri": "[concat('http://',variables('storageAccountName'),'.blob.core.windows.net/',variables('vmStorageAccountContainerName'),'/',variables('OSDiskName'),'.vhd')]"
            },
            "caching": "ReadWrite",
            "createOption": "FromImage"
          }
        },
        "networkProfile": {
          "networkInterfaces": [
            {
              "id": "[resourceId('Microsoft.Network/networkInterfaces',variables('nicName'))]"
            }
          ]
        },
        "diagnosticsProfile": {
          "bootDiagnostics": {
             "enabled": "true",
             "storageUri": "[concat('http://',variables('storageAccountName'),'.blob.core.windows.net')]"
          }
        }
      },
      "resources": [
        {
          "type": "extensions",
          "name": "Microsoft.EnterpriseCloud.Monitoring",
          "apiVersion": "[variables('apiVersion')]",
          "location": "[resourceGroup().location]",
          "dependsOn": [
            "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
          ],
          "properties": {
            "publisher": "Microsoft.EnterpriseCloud.Monitoring",
            "type": "MicrosoftMonitoringAgent",
            "typeHandlerVersion": "1.0",
            "autoUpgradeMinorVersion": true,
            "settings": {
              "workspaceId": "[parameters('workspaceId')]"
            },
            "protectedSettings": {
              "workspaceKey": "[listKeys(resourceId('Microsoft.OperationalInsights/workspaces', parameters('workspaceName')), '2015-03-20').primarySharedKey]"
            }
          }
        }
      ]
    }
  ],
  "outputs": {
      "sharedKeyOutput": {
         "value": "[listKeys(resourceId('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName')), '2015-03-20').primarySharedKey]",
         "type": "string"
      },
      "workspaceIdOutput": {
         "value": "[reference(concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName')), '2015-03-20').customerId]",
        "type" : "string"
      }
  }
}
```

Puede implementar una plantilla con el siguiente comando PowerShell:

```
New-AzureRmResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateFile $templateFilePath
```

## <a name="troubleshooting-windows-virtual-machines"></a>Solución de problemas de máquinas virtuales de Windows

Si la extensión del agente *Agente de supervisión de Microsoft* VM no se instala o informes puede realizar los siguientes pasos para solucionar el problema.

1. Comprobar si está instalado el agente de Azure VM y funcionar correctamente mediante los pasos descritos en el [artículo de Knowledge 2965986](https://support.microsoft.com/kb/2965986#mt1).
  + También puede revisar el archivo de registro de agente VM`C:\WindowsAzure\logs\WaAppAgent.log`
  + Si el registro no existe, no está instalado el agente de máquina virtual.
    - [Instalar al agente de máquina virtual de Azure en máquinas virtuales clásicas](../virtual-machines/virtual-machines-windows-classic-agents-and-extensions.md)
2. Confirme que está ejecutando la tarea de latido de extensión de agente de supervisión de Microsoft con los pasos siguientes:
  + Inicie sesión en la máquina virtual
  + Abra el programador de tareas y buscar el `update_azureoperationalinsight_agent_heartbeat` tareas
  + Confirme la tarea está habilitada y está ejecutando cada minuto
  + Proteger el archivo de registro de latido`C:\WindowsAzure\Logs\Plugins\Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent\heartbeat.log`
3. Revise los archivos de registro de la extensión de máquina virtual de agente de supervisión de Microsoft en`C:\Packages\Plugins\Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent`
3. Asegúrese de que la máquina virtual puede ejecutar secuencias de comandos de PowerShell
4. Asegúrese de que no se han modificado los permisos en C:\Windows\temp
5. Ver el estado de agente de supervisión de Microsoft, escriba lo siguiente en una ventana de PowerShell elevada en la máquina virtual`  (New-Object -ComObject 'AgentConfigManager.MgmtSvcCfg').GetCloudWorkspaces() | Format-List`
6. Revise los archivos de registro de configuración de supervisión de Microsoft Agent en`C:\Windows\System32\config\systemprofile\AppData\Local\SCOM\Logs`

Para obtener más información, consulte [solución de problemas de las extensiones de Windows](../virtual-machines/virtual-machines-windows-extensions-troubleshoot.md).

## <a name="troubleshooting-linux-virtual-machines"></a>Solución de problemas de máquinas virtuales de Linux

Si la extensión del agente *Agente de OMS para Linux* VM no se instala o informes puede realizar los siguientes pasos para solucionar el problema.

1. Si el estado de extensión es *desconocido* verificación si está instalado el agente de Azure VM y funcionar correctamente a revisar el archivo de registro de agente VM`/var/log/waagent.log`
  + Si el registro no existe, no está instalado el agente de máquina virtual.
  - [Instalar al agente de Azure VM en máquinas virtuales de Linux](../virtual-machines/virtual-machines-linux-agent-user-guide.md)
2. Para otros Estados negativos, revise el agente de OMS para archivos de registro de extensión Linux VM `/var/log/azure/Microsoft.EnterpriseCloud.Monitoring.OmsAgentForLinux/*/extension.log` y`/var/log/azure/Microsoft.EnterpriseCloud.Monitoring.OmsAgentForLinux/*/CommandExecution.log`
3. Si el estado de extensión es correcto, pero no se está cargando datos revise al agente de OMS para archivos de registro de Linux en`/var/opt/microsoft/omsagent/log/omsagent.log`

Para obtener más información, consulte [solución de problemas de extensiones de Linux](../virtual-machines/virtual-machines-linux-extensions-troubleshoot.md).


## <a name="next-steps"></a>Pasos siguientes

+ Configurar los [orígenes de datos en el análisis de registro](log-analytics-data-sources.md) para especificar las mediciones para recopilar y los registros.
+ Para recopilar datos de virtual máquinas [soluciones de agregar el registro de análisis de la Galería de soluciones](log-analytics-add-solutions.md).
+ [Recopilar datos mediante el uso de diagnósticos de Azure](log-analytics-azure-storage.md) para otros recursos que se están ejecutando en Azure.

Para equipos que no están en Azure, puede instalar al agente de análisis de registro mediante los métodos que se describen en los siguientes artículos:

+ [Conectar equipos de Windows para el análisis de registro](log-analytics-windows-agents.md)
+ [Conectar equipos Linux para el análisis de registro](log-analytics-linux-agents.md)
