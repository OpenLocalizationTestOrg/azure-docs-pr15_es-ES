<properties
   pageTitle="Implementar una máquina virtual con una dirección IP pública estática usando una plantilla en el Administrador de recursos | Microsoft Azure"
   description="Obtenga información sobre cómo implementar máquinas virtuales con una dirección IP pública estática usando una plantilla en el Administrador de recursos"
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
   ms.date="04/27/2016"
   ms.author="jdial" />

# <a name="deploy-a-vm-with-a-static-public-ip-using-a-template"></a>Implementar una máquina virtual con una dirección IP pública estática usando una plantilla

[AZURE.INCLUDE [virtual-network-deploy-static-pip-arm-selectors-include.md](../../includes/virtual-network-deploy-static-pip-arm-selectors-include.md)]

[AZURE.INCLUDE [virtual-network-deploy-static-pip-intro-include.md](../../includes/virtual-network-deploy-static-pip-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-rm-include.md)]modelo de implementación clásico.

[AZURE.INCLUDE [virtual-network-deploy-static-pip-scenario-include.md](../../includes/virtual-network-deploy-static-pip-scenario-include.md)]

## <a name="public-ip-resources-in-a-template-file"></a>Recursos de IP pública en un archivo de plantilla

Puede ver y descargar la [plantilla de ejemplo](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/03-Static-public-IP/azuredeploy.json).

La sección siguiente muestra la definición del recurso IP pública, según el escenario anterior.

      {
        "apiVersion": "2015-06-15",
        "type": "Microsoft.Network/publicIPAddresses",
        "name": "[variables('webVMSetting').pipName]",
        "location": "[variables('location')]",
        "properties": {
          "publicIPAllocationMethod": "Static"
        },
        "tags": {
          "displayName": "PublicIPAddress - Web"
        }
      },

Observe que la propiedad **publicIPAllocationMethod** , que se establece en *estático*. Esta propiedad puede ser *dinámica* (valor predeterminado) o *estático*. Si se establece en estática garantiza que nunca se cambiará la dirección IP pública asignada.

La sección siguiente muestra la asociación de la dirección IP pública con una interfaz de red.

      {
        "apiVersion": "2015-06-15",
        "type": "Microsoft.Network/networkInterfaces",
        "name": "[variables('webVMSetting').nicName]",
        "location": "[variables('location')]",
        "tags": {
          "displayName": "NetworkInterface - Web"
        },
        "dependsOn": [
          "[concat('Microsoft.Network/publicIPAddresses/', variables('webVMSetting').pipName)]",
          "[concat('Microsoft.Network/virtualNetworks/', parameters('vnetName'))]"
        ],
        "properties": {
          "ipConfigurations": [
            {
              "name": "ipconfig1",
              "properties": {
                "privateIPAllocationMethod": "Static",
                "privateIPAddress": "[variables('webVMSetting').ipAddress]",
                "publicIPAddress": {
                  "id": "[resourceId('Microsoft.Network/publicIPAddresses',variables('webVMSetting').pipName)]"
                },
                "subnet": {
                  "id": "[variables('frontEndSubnetRef')]"
                }
              }
            }
          ]
        }
      },

Observe que la propiedad **publicIPAddress** señalando el **identificador** de un recurso denominado **variables('webVMSetting').pipName**. Que es el nombre del recurso IP público mostrado arriba.

Por último, se muestra la interfaz de red anterior en la propiedad **networkProfile** de la máquina virtual se ha creado.

      "networkProfile": {
        "networkInterfaces": [
          {
            "id": "[resourceId('Microsoft.Network/networkInterfaces', variables('webVMSetting').nicName)]"
          }
        ]
      }

## <a name="deploy-the-template-by-using-click-to-deploy"></a>Implementar la plantilla mediante hacer clic para implementar

La plantilla de ejemplo disponible en el repositorio público utiliza un archivo de parámetro que contiene el valor predeterminado valores que se usan para generar el escenario descrito anteriormente. Para implementar esta plantilla con clic para implementar, haga clic en **implementar a Azure** en el archivo Readme.md para la plantilla de [máquina virtual con puntos estático](https://github.com/Azure/azure-quickstart-templates/tree/master/IaaS-Story/03-Static-public-IP) . Reemplace los valores de parámetro de forma predeterminada si lo desea y escriba los valores de los parámetros en blanco.  Siga las instrucciones en el portal para crear una máquina virtual con una dirección IP pública estática.

## <a name="deploy-the-template-by-using-powershell"></a>Implementar la plantilla con PowerShell

Para implementar la plantilla que descargó con PowerShell, siga los pasos siguientes.

1. Si nunca ha usado Azure PowerShell, vea [cómo instalar y configurar Azure PowerShell](../powershell-install-configure.md) y siga las instrucciones en los pasos 1 a 3.

2. En la consola de PowerShell, ejecute el cmdlet **AzureRmResourceGroup de nuevo** para crear un nuevo grupo de recursos, si es necesario. Si ya tiene un grupo de recursos que se creó, vaya al paso 3.

        New-AzureRmResourceGroup -Name PIPTEST -Location westus

    Resultados esperados:

        ResourceGroupName : PIPTEST
        Location          : westus
        ProvisioningState : Succeeded
        Tags              :
        ResourceId        : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/StaticPublicIP

3. En la consola de PowerShell, ejecute el cmdlet de **Nuevo AzureRmResourceGroupDeployment** para implementar la plantilla.

        New-AzureRmResourceGroupDeployment -Name DeployVM -ResourceGroupName PIPTEST `
            -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/03-Static-public-IP/azuredeploy.json `
            -TemplateParameterUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/03-Static-public-IP/azuredeploy.parameters.json

    Resultados esperados:

        DeploymentName    : DeployVM
        ResourceGroupName : PIPTEST
        ProvisioningState : Succeeded
        Timestamp         : <Deployment date> <Deployment time>
        Mode              : Incremental
        TemplateLink      :
                            Uri            : https://raw.githubusercontent.com/Azure/azure-quickstart-templates/mas
                            ter/IaaS-Story/03-Static-public-IP/azuredeploy.json
                            ContentVersion : 1.0.0.0

        Parameters        :
                            Name                      Type                       Value     
                            ========================  =========================  ==========
                            vnetName                  String                     WTestVNet
                            vnetPrefix                String                     192.168.0.0/16
                            frontEndSubnetName        String                     FrontEnd  
                            frontEndSubnetPrefix      String                     192.168.1.0/24
                            storageAccountNamePrefix  String                     iaasestd  
                            stdStorageType            String                     Standard_LRS
                            osType                    String                     Windows   
                            adminUsername             String                     adminUser
                            adminPassword             SecureString                         

        Outputs           :

## <a name="deploy-the-template-by-using-the-azure-cli"></a>Implementar la plantilla mediante la CLI de Azure

Para implementar la plantilla mediante la CLI de Azure, siga los pasos siguientes.

1. Si nunca ha usado Azure CLI, siga los pasos del artículo [instalar y configurar la CLI de Azure](../xplat-cli-install.md) y, a continuación, los pasos para conectar CLI a su suscripción en la sección "Usar inicio de sesión de azure para autenticar de forma interactiva" del artículo [conectarse a una suscripción de Azure desde la interfaz de línea de comandos de Azure (Azure CLI)](../xplat-cli-connect.md) .
2. Ejecutar el comando de **azure config modo** para cambiar al modo de administrador de recursos, como se muestra a continuación.

        azure config mode arm

    Éste es el resultado esperado para el comando anterior:

        info:    New mode is arm

3. Abra el [archivo de parámetro](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/03-Static-public-IP/azuredeploy.parameters.json), seleccione su contenido y guardarlo en un archivo en su equipo. En este ejemplo, los parámetros se guardan en un archivo denominado *parameters.json*. Cambiar los valores de parámetro en el archivo si lo desea, pero como mínimo, se recomienda que cambie el valor del parámetro adminPassword a una contraseña única y compleja.

4. Ejecute el cmdlet de **implementación de azure grupo crear** para implementar el nuevo VNet mediante los archivos de plantilla y parámetro descargado y modificado por encima. En el siguiente comando, reemplace <path> con la ruta de acceso que ha guardado el archivo. 

        azure group create -n PIPTEST2 -l westus --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/03-Static-public-IP/azuredeploy.json -e <path>\parameters.json

    Resultado esperado (listas de valores de parámetro utilizados):

        info:    Executing command group create
        + Getting resource group PIPTEST2
        + Creating resource group PIPTEST2
        info:    Created resource group PIPTEST2
        + Initializing template configurations and parameters
        + Creating a deployment
        info:    Created template deployment "azuredeploy"
        data:    Id:                  /subscriptions/<Subscription ID>/resourceGroups/PIPTEST2
        data:    Name:                PIPTEST2
        data:    Location:            westus
        data:    Provisioning State:  Succeeded
        data:    Tags: null
        data:
        info:    group create command OK
