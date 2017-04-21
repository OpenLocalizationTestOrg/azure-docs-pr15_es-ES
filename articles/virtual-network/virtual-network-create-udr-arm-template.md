<properties 
   pageTitle="Controlar el enrutamiento y usar en el Administrador de recursos de equipos virtuales mediante una plantilla | Microsoft Azure"
   description="Obtenga información sobre cómo controlar el enrutamiento y utilizar equipos virtuales en el Administrador de recursos de Azure mediante una plantilla"
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
   ms.date="02/23/2016"
   ms.author="jdial" />

#<a name="create-user-defined-routes-udr-in-resource-manager-by-using-a-template"></a>Crear rutas definidas por el usuario (UDR) en el Administrador de recursos mediante una plantilla

[AZURE.INCLUDE [virtual-network-create-udr-arm-selectors-include.md](../../includes/virtual-network-create-udr-arm-selectors-include.md)]

[AZURE.INCLUDE [virtual-network-create-udr-intro-include.md](../../includes/virtual-network-create-udr-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]En este artículo trata sobre el modelo de implementación de administrador de recursos. 

[AZURE.INCLUDE [virtual-network-create-udr-scenario-include.md](../../includes/virtual-network-create-udr-scenario-include.md)]

## <a name="udr-resources-in-a-template-file"></a>Recursos UDR en un archivo de plantilla

Puede ver y descargar la [plantilla de ejemplo](https://github.com/telmosampaio/azure-templates/tree/master/IaaS-NSG-UDR).

La sección siguiente muestra la definición de la parte delantera UDR en el archivo **azuredeploy-vnet-nsg-udr.json** , según el escenario anterior.

    "apiVersion": "2015-06-15",
    "type": "Microsoft.Network/routeTables",
    "name": "[parameters('frontEndRouteTableName')]",
    "location": "[resourceGroup().location]",
    "tags": {
      "displayName": "UDR - FrontEnd"
    },
    "properties": {
      "routes": [
        {
          "name": "RouteToBackEnd",
          "properties": {
            "addressPrefix": "[parameters('backEndSubnetPrefix')]",
            "nextHopType": "VirtualAppliance",
            "nextHopIpAddress": "[parameters('vmaIpAddress')]"
          }
        }
      ]


Para asociar la UDR a la subred front-end, tendrá que cambiar la definición de subred en la plantilla y usar el identificador de referencia para la UDR.

    "subnets": [
        "name": "[parameters('frontEndSubnetName')]",
        "properties": {
          "addressPrefix": "[parameters('frontEndSubnetPrefix')]",
          "networkSecurityGroup": {
            "id": "[resourceId('Microsoft.Network/networkSecurityGroups', parameters('frontEndNSGName'))]"
          },
          "routeTable": {
              "id": "[resourceId('Microsoft.Network/routeTables', parameters('frontEndRouteTableName'))]"
          }
        },
        ...]

Observe el mismo que se realiza para el back-end GSN y la subred back-end en la plantilla.

También debe asegurarse de que la máquina virtual de **FW1** tiene el reenvío propiedad habilitada en la NIC que se usará para recibir y reenviar paquetes de IP. La sección siguiente muestra la definición de la NIC para FW1 en el archivo azuredeploy-nsg-udr.json, según el escenario anterior.

    "apiVersion": "2015-06-15",
    "type": "Microsoft.Network/networkInterfaces",
    "location": "[variables('location')]",
    "tags": {
      "displayName": "NetworkInterfaces - DMZ"
    },
    "name": "[concat(variables('fwVMSettings').nicName, copyindex(1))]",
    "dependsOn": [
      "[concat('Microsoft.Network/publicIPAddresses/', variables('fwVMSettings').pipName, copyindex(1))]",
      "[concat('Microsoft.Resources/deployments/', 'vnetTemplate')]"
    ],
    "properties": {
      "ipConfigurations": [
        {
          "name": "ipconfig1",
          "properties": {
            "enableIPForwarding": true,
            "privateIPAllocationMethod": "Static",
            "privateIPAddress": "[concat('192.168.0.',copyindex(4))]",
            "publicIPAddress": {
              "id": "[resourceId('Microsoft.Network/publicIPAddresses',concat(variables('fwVMSettings').pipName, copyindex(1)))]"
            },
            "subnet": {
              "id": "[variables('dmzSubnetRef')]"
            }
          }
        }
      ]
    },
    "copy": {
      "name": "fwniccount",
      "count": "[parameters('fwCount')]"
    }

## <a name="deploy-the-arm-template-by-using-click-to-deploy"></a>Implementar la plantilla ARM mediante hacer clic para implementar

La plantilla de ejemplo disponible en el repositorio público utiliza un archivo de parámetro que contiene el valor predeterminado valores que se usan para generar el escenario descrito anteriormente. Para implementar esta plantilla con clic para implementar, siga [este vínculo](https://github.com/telmosampaio/azure-templates/tree/master/IaaS-NSG-UDR), haga clic en **implementar a Azure**, reemplace los valores de parámetro de forma predeterminada, si es necesario y siga las instrucciones en el portal.

## <a name="deploy-the-arm-template-by-using-powershell"></a>Implementar la plantilla ARM con PowerShell

Para implementar la plantilla ARM que ha descargado con PowerShell, siga los pasos siguientes.

[AZURE.INCLUDE [powershell-preview-include.md](../../includes/powershell-preview-include.md)]

1. Si nunca ha usado Azure PowerShell, vea [cómo instalar y configurar Azure PowerShell](../powershell-install-configure.md) y siga las instrucciones hasta el final para iniciar sesión en Azure y seleccione la suscripción.

2. Ejecutar la `New-AzureRmResourceGroup` cmdlet para crear un grupo de recursos.

        New-AzureRmResourceGroup -Name TestRG -Location westus

3. Ejecutar la `New-AzureRmResourceGroupDeployment` cmdlet para implementar la plantilla.

        New-AzureRmResourceGroupDeployment -Name DeployUDR -ResourceGroupName TestRG `
            -TemplateUri https://raw.githubusercontent.com/telmosampaio/azure-templates/master/IaaS-NSG-UDR/azuredeploy.json `
            -TemplateParameterUri https://raw.githubusercontent.com/telmosampaio/azure-templates/master/IaaS-NSG-UDR/azuredeploy.parameters.json            

    Resultados esperados:

        ResourceGroupName : TestRG
        Location          : westus
        ProvisioningState : Succeeded
        Tags              : 
        Permissions       : 
                            Actions  NotActions
                            =======  ==========
                            *                  
                            
        Resources         : 
                            Name                Type                                     Location
                            ==================  =======================================  ========
                            ASFW                Microsoft.Compute/availabilitySets       westus  
                            ASSQL               Microsoft.Compute/availabilitySets       westus  
                            ASWEB               Microsoft.Compute/availabilitySets       westus  
                            FW1                 Microsoft.Compute/virtualMachines        westus  
                            SQL1                Microsoft.Compute/virtualMachines        westus  
                            SQL2                Microsoft.Compute/virtualMachines        westus  
                            WEB1                Microsoft.Compute/virtualMachines        westus  
                            WEB2                Microsoft.Compute/virtualMachines        westus  
                            NICFW1              Microsoft.Network/networkInterfaces      westus  
                            NICSQL1             Microsoft.Network/networkInterfaces      westus  
                            NICSQL2             Microsoft.Network/networkInterfaces      westus  
                            NICWEB1             Microsoft.Network/networkInterfaces      westus  
                            NICWEB2             Microsoft.Network/networkInterfaces      westus  
                            NSG-BackEnd         Microsoft.Network/networkSecurityGroups  westus  
                            NSG-FrontEnd        Microsoft.Network/networkSecurityGroups  westus  
                            PIPFW1              Microsoft.Network/publicIPAddresses      westus  
                            PIPSQL1             Microsoft.Network/publicIPAddresses      westus  
                            PIPSQL2             Microsoft.Network/publicIPAddresses      westus  
                            PIPWEB1             Microsoft.Network/publicIPAddresses      westus  
                            PIPWEB2             Microsoft.Network/publicIPAddresses      westus  
                            UDR-BackEnd         Microsoft.Network/routeTables            westus  
                            UDR-FrontEnd        Microsoft.Network/routeTables            westus  
                            TestVNet            Microsoft.Network/virtualNetworks        westus  
                            testvnetstorageprm  Microsoft.Storage/storageAccounts        westus  
                            testvnetstoragestd  Microsoft.Storage/storageAccounts        westus  
                            
        ResourceId        : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG

## <a name="deploy-the-arm-template-by-using-the-azure-cli"></a>Implementar la plantilla ARM mediante la CLI de Azure

Para implementar la plantilla ARM mediante CLI Azure, siga los pasos siguientes.

1. Si nunca ha usado CLI de Azure, vea [instalar y configurar la CLI de Azure](../xplat-cli-install.md) y siga las instrucciones hasta el punto donde seleccionar su cuenta de Azure y suscripción.
2. Ejecutar la `azure config mode` comando para cambiar al modo de administrador de recursos, como se muestra a continuación.

        azure config mode arm

    Éste es el resultado esperado para el comando anterior:

        info:    New mode is arm

3. Desde el explorador, vaya a **https://raw.githubusercontent.com/telmosampaio/azure-templates/master/IaaS-NSG-UDR/azuredeploy.parameters.json**, copie el contenido del archivo json y pegar en un nuevo archivo en el equipo. En este escenario, ¿copiando los valores que aparecen a continuación en un archivo denominado **c:\udr\azuredeploy.parameters.json**.

        {
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
          "contentVersion": "1.0.0.0",
          "parameters": {
            "fwCount": {
              "value": 1
            },
            "webCount": {
              "value": 2
            },
            "sqlCount": {
              "value": 2
            }
          }
        }

4. Ejecute el cmdlet **Crear grupo azure** para implementar el nuevo VNet mediante los archivos de plantilla y parámetro descargado y modificado por encima. La lista que aparece después de la salida explica los parámetros que se utiliza.

        azure group create -n TestRG -l westus --template-uri 'https://raw.githubusercontent.com/telmosampaio/azure-templates/master/IaaS-NSG-UDR/azuredeploy.json' -e 'c:\udr\azuredeploy.parameters.json'

    Resultados esperados:

        info:    Executing command group create
        info:    Getting resource group TestRG
        info:    Updating resource group TestRG
        info:    Updated resource group TestRG
        info:    Initializing template configurations and parameters
        info:    Creating a deployment
        info:    Created template deployment "azuredeploy"
        data:    Id:                  /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG
        data:    Name:                TestRG
        data:    Location:            westus
        data:    Provisioning State:  Succeeded
        data:    Tags: null
        data:    
        info:    group create command OK

5. Ejecute el comando **Mostrar grupo azure** para ver los recursos creados en el nuevo grupo de recursos. 

        azure group show TestRG

    Resultado esperado
        
        info:    Executing command group show
        info:    Listing resource groups
        info:    Listing resources for the group
        data:    Id:                  /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG
        data:    Name:                TestRG
        data:    Location:            westus
        data:    Provisioning State:  Succeeded
        data:    Tags: null
        data:    Resources:
        data:    
        data:      Id      : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Compute/availabilitySets/ASFW
        data:      Name    : ASFW
        data:      Type    : availabilitySets
        data:      Location: westus
        data:      Tags    : displayName=AvailabilitySet - DMZ
        data:    
        data:      Id      : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Compute/availabilitySets/ASSQL
        data:      Name    : ASSQL
        data:      Type    : availabilitySets
        data:      Location: westus
        data:      Tags    : displayName=AvailabilitySet - SQL
        data:    
        data:      Id      : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Compute/availabilitySets/ASWEB
        data:      Name    : ASWEB
        data:      Type    : availabilitySets
        data:      Location: westus
        data:      Tags    : displayName=AvailabilitySet - Web
        data:    
        data:      Id      : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Compute/virtualMachines/FW1
        data:      Name    : FW1
        data:      Type    : virtualMachines
        data:      Location: westus
        data:      Tags    : displayName=VMs - DMZ
        data:    
        data:      Id      : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Compute/virtualMachines/SQL1
        data:      Name    : SQL1
        data:      Type    : virtualMachines
        data:      Location: westus
        data:      Tags    : displayName=VMs - SQL
        data:    
        data:      Id      : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Compute/virtualMachines/SQL2
        data:      Name    : SQL2
        data:      Type    : virtualMachines
        data:      Location: westus
        data:      Tags    : displayName=VMs - SQL
        data:    
        data:      Id      : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Compute/virtualMachines/WEB1
        data:      Name    : WEB1
        data:      Type    : virtualMachines
        data:      Location: westus
        data:      Tags    : displayName=VMs - Web
        data:    
        data:      Id      : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Compute/virtualMachines/WEB2
        data:      Name    : WEB2
        data:      Type    : virtualMachines
        data:      Location: westus
        data:      Tags    : displayName=VMs - Web
        data:    
        data:      Id      : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/NICFW1
        data:      Name    : NICFW1
        data:      Type    : networkInterfaces
        data:      Location: westus
        data:      Tags    : displayName=NetworkInterfaces - DMZ
        data:    
        data:      Id      : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/NICSQL1
        data:      Name    : NICSQL1
        data:      Type    : networkInterfaces
        data:      Location: westus
        data:      Tags    : displayName=NetworkInterfaces - SQL
        data:    
        data:      Id      : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/NICSQL2
        data:      Name    : NICSQL2
        data:      Type    : networkInterfaces
        data:      Location: westus
        data:      Tags    : displayName=NetworkInterfaces - SQL
        data:    
        data:      Id      : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/NICWEB1
        data:      Name    : NICWEB1
        data:      Type    : networkInterfaces
        data:      Location: westus
        data:      Tags    : displayName=NetworkInterfaces - Web
        data:    
        data:      Id      : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/NICWEB2
        data:      Name    : NICWEB2
        data:      Type    : networkInterfaces
        data:      Location: westus
        data:      Tags    : displayName=NetworkInterfaces - Web
        data:    
        data:      Id      : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/networkSecurityGroups/NSG-BackEnd
        data:      Name    : NSG-BackEnd
        data:      Type    : networkSecurityGroups
        data:      Location: westus
        data:      Tags    : displayName=NSG - Front End
        data:    
        data:      Id      : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/networkSecurityGroups/NSG-FrontEnd
        data:      Name    : NSG-FrontEnd
        data:      Type    : networkSecurityGroups
        data:      Location: westus
        data:      Tags    : displayName=NSG - Remote Access
        data:    
        data:      Id      : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/publicIPAddresses/PIPFW1
        data:      Name    : PIPFW1
        data:      Type    : publicIPAddresses
        data:      Location: westus
        data:      Tags    : displayName=PublicIPAddresses - DMZ
        data:    
        data:      Id      : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/publicIPAddresses/PIPSQL1
        data:      Name    : PIPSQL1
        data:      Type    : publicIPAddresses
        data:      Location: westus
        data:      Tags    : displayName=PublicIPAddresses - SQL
        data:    
        data:      Id      : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/publicIPAddresses/PIPSQL2
        data:      Name    : PIPSQL2
        data:      Type    : publicIPAddresses
        data:      Location: westus
        data:      Tags    : displayName=PublicIPAddresses - SQL
        data:    
        data:      Id      : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/publicIPAddresses/PIPWEB1
        data:      Name    : PIPWEB1
        data:      Type    : publicIPAddresses
        data:      Location: westus
        data:      Tags    : displayName=PublicIPAddresses - Web
        data:    
        data:      Id      : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/publicIPAddresses/PIPWEB2
        data:      Name    : PIPWEB2
        data:      Type    : publicIPAddresses
        data:      Location: westus
        data:      Tags    : displayName=PublicIPAddresses - Web
        data:    
        data:      Id      : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/routeTables/UDR-BackEnd
        data:      Name    : UDR-BackEnd
        data:      Type    : routeTables
        data:      Location: westus
        data:      Tags    : displayName=Route Table - Back End
        data:    
        data:      Id      : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/routeTables/UDR-FrontEnd
        data:      Name    : UDR-FrontEnd
        data:      Type    : routeTables
        data:      Location: westus
        data:      Tags    : displayName=UDR - FrontEnd
        data:    
        data:      Id      : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet
        data:      Name    : TestVNet
        data:      Type    : virtualNetworks
        data:      Location: westus
        data:      Tags    : displayName=VNet
        data:    
        data:      Id      : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Storage/storageAccounts/testvnetstorageprm
        data:      Name    : testvnetstorageprm
        data:      Type    : storageAccounts
        data:      Location: westus
        data:      Tags    : displayName=Storage Account - Premium
        data:    
        data:      Id      : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Storage/storageAccounts/testvnetstoragestd
        data:      Name    : testvnetstoragestd
        data:      Type    : storageAccounts
        data:      Location: westus
        data:      Tags    : displayName=Storage Account - Simple
        data:    
        data:    Permissions:
        data:      Actions: *
        data:      NotActions: 
        data:    
        info:    group show command OK

>[AZURE.TIP] Si no ve todos los recursos, ejecute el `azure group deployment show` comando para garantizar el estado de aprovisionamiento de la implementación es *completado*.