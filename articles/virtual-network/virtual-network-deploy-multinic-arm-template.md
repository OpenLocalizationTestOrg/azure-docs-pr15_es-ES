<properties
   pageTitle="Implementar máquinas virtuales de NIC multi usando una plantilla en el Administrador de recursos | Microsoft Azure"
   description="Obtenga información sobre cómo implementar máquinas virtuales de NIC multi usando una plantilla en el Administrador de recursos"
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
   ms.date="02/02/2016"
   ms.author="jdial" />

# <a name="deploy-multi-nic-vms-using-a-template"></a>Implementar máquinas virtuales de NIC multi usando una plantilla

[AZURE.INCLUDE [virtual-network-deploy-multinic-arm-selectors-include.md](../../includes/virtual-network-deploy-multinic-arm-selectors-include.md)]

[AZURE.INCLUDE [virtual-network-deploy-multinic-intro-include.md](../../includes/virtual-network-deploy-multinic-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-rm-include.md)][modelo de implementación clásico](virtual-network-deploy-multinic-classic-ps.md).

[AZURE.INCLUDE [virtual-network-deploy-multinic-scenario-include.md](../../includes/virtual-network-deploy-multinic-scenario-include.md)]

Dado que en este momento no puede tener máquinas virtuales con una sola NIC y máquinas virtuales con varias NIC en el mismo grupo de recursos, implementará los servidores back-end de un grupo de recursos y todos los otros componentes en otro grupo de seguridad. Los pasos siguientes use un grupo de recursos denominado *IaaSStory* para el grupo de recursos principal y *Back-end de IaaSStory* para los servidores de back-end.

## <a name="prerequisites"></a>Requisitos previos

Antes de implementar los servidores back-end, debe implementar el grupo de recursos principal con todos los recursos necesarios para este escenario. Para implementar estos recursos, siga los pasos siguientes.

1. Vaya a [la página de plantilla](https://github.com/Azure/azure-quickstart-templates/tree/master/IaaS-Story/11-MultiNIC).
2. En la página de plantilla, a la derecha del **grupo de recursos principal**, haga clic en **implementar en Azure**.
3. Si es necesario, cambie los valores de parámetro, luego, siga los pasos en el portal de vista previa de Azure para implementar el grupo de recursos.

> [AZURE.IMPORTANT] Asegúrese de que los nombres de cuenta de almacenamiento son únicos. No puede tener nombres de cuenta de almacenamiento duplicados en Azure.

## <a name="understand-the-deployment-template"></a>Comprender la plantilla de implementación

Antes de implementar la plantilla proporcionada con esta documentación, asegúrese de que entender lo que hace. Los pasos siguientes proporcionan información general completa de la plantilla en cuestión.

1. Vaya a [la página de plantilla](https://github.com/Azure/azure-quickstart-templates/tree/master/IaaS-Story/11-MultiNIC).
2. Haga clic en **azuredeploy.json** para abrir el archivo de plantilla.
3. Observe el parámetro *osType* que se muestran a continuación. Este parámetro se utiliza para seleccionar la imagen de máquina virtual para el servidor de base de datos, junto con el sistema operativo con varios valores relacionados.

        "osType": {
          "type": "string",
          "defaultValue": "Windows",
          "allowedValues": [
            "Windows",
            "Ubuntu"
          ],
          "metadata": {
            "description": "Type of OS to use for VMs: Windows or Ubuntu."
          }
        },

4. Desplácese hacia abajo hasta la lista de variables y compruebe la definición de las variables **dbVMSetting** , que se muestran a continuación. Recibe uno de los elementos de la matriz contenidos en la variable **dbVMSettings** . Si está familiarizado con terminología de desarrollo de software, puede ver la variable **dbVMSettings** como una tabla hash o un diccionario.

        "dbVMSetting": "[variables('dbVMSettings')[parameters('osType')]]"

5. Suponga que decide implementar máquinas virtuales de Windows que ejecuta SQL en el back-end. A continuación, el valor de **osType** sería *Windows*y la variable **dbVMSetting** contiene el elemento que se muestra abajo, que representa el primer valor de la variable **dbVMSettings** .

          "Windows": {
            "vmSize": "Standard_DS3",
            "publisher": "MicrosoftSQLServer",
            "offer": "SQL2014SP1-WS2012R2",
            "sku": "Standard",
            "version": "latest",
            "vmName": "DB",
            "osdisk": "osdiskdb",
            "datadisk": "datadiskdb",
            "nicName": "NICDB",
            "ipAddress": "192.168.2.",
            "extensionDeployment": "",
            "avsetName": "ASDB",
            "remotePort": 3389,
            "dbPort": 1433
          },

6. Observe que la **vmSize** contiene el valor *Standard_DS3*. Permitir que solo determinados tamaños de máquina virtual para el uso de varias NIC. Puede comprobar los tamaños de VM están entre varios NIC habilitada con una visita a la [información general de múltiples NIC](virtual-networks-multiple-nics.md).
7. Desplácese hacia abajo hasta **recursos** y observe el primer elemento. Describe una cuenta de almacenamiento. Esta cuenta de almacenamiento se utilizará para mantener los discos de datos usados por cada máquina virtual de la base de datos. En este escenario, cada máquina virtual de la base de datos tiene un disco de sistema operativo almacenados en almacenamiento normales y dos discos de datos almacenados en el almacenamiento SSD (premium).

        {
          "apiVersion": "2015-05-01-preview",
          "type": "Microsoft.Storage/storageAccounts",
          "name": "[parameters('prmStorageName')]",
          "location": "[variables('location')]",
          "tags": {
            "displayName": "Storage Account - Premium"
          },
          "properties": {
            "accountType": "[parameters('prmStorageType')]"
          }
        },

8. Desplácese hacia abajo hasta el siguiente recurso, como se indica a continuación. Este recurso representa la NIC que utiliza para el acceso a la base de datos en cada máquina virtual de la base de datos. Observe el uso de la función de **copia** de este recurso. La plantilla permite implementar máquinas virtuales tantos como desee, en función del parámetro **dbCount** . Por lo tanto, deberá crear la misma cantidad de NIC para el acceso de la base de datos, uno para cada máquina virtual.

        {
          "apiVersion": "2015-06-15",
          "type": "Microsoft.Network/networkInterfaces",
          "name": "[concat(variables('dbVMSetting').nicName,'-DA-', copyindex(1))]",
          "location": "[variables('location')]",
          "tags": {
            "displayName": "NetworkInterfaces - DB DA"
          },
          "copy": {
            "name": "dbniccount",
            "count": "[parameters('dbCount')]"
          },
          "properties": {
            "ipConfigurations": [
              {
                "name": "ipconfig1",
                "properties": {
                  "privateIPAllocationMethod": "Static",
                  "privateIPAddress": "[concat(variables('dbVMSetting').ipAddress,copyindex(4))]",
                  "subnet": {
                    "id": "[variables('backEndSubnetRef')]"
                  }
                }
              }
            ]
          }
        },

9. Desplácese hacia abajo hasta el siguiente recurso, como se indica a continuación. Este recurso representa la NIC que utiliza para la administración de cada máquina virtual de la base de datos. Una vez más, necesita una de estas NIC para cada máquina virtual de la base de datos. Observe que el elemento de **networkSecurityGroup** vincular un GSN que permite el acceso a RDP/SSH a esta NIC solo.

        {
          "apiVersion": "2015-06-15",
          "type": "Microsoft.Network/networkInterfaces",
          "name": "[concat(variables('dbVMSetting').nicName, '-RA-',copyindex(1))]",
          "location": "[variables('location')]",
          "tags": {
            "displayName": "NetworkInterfaces - DB RA"
          },
          "copy": {
            "name": "dbniccount",
            "count": "[parameters('dbCount')]"
          },
          "properties": {
            "ipConfigurations": [
              {
                "name": "ipconfig1",
                "properties": {
                  "networkSecurityGroup": {
                    "id": "[resourceId('Microsoft.Network/networkSecurityGroups', parameters('remoteAccessNSGName'))]"
                  },
                  "privateIPAllocationMethod": "Static",
                  "privateIPAddress": "[concat(variables('dbVMSetting').ipAddress,copyindex(54))]",
                  "subnet": {
                    "id": "[variables('backEndSubnetRef')]"
                  }
                }
              }
            ]
          }
        },

10. Desplácese hacia abajo hasta el siguiente recurso, como se indica a continuación. Este recurso representa una disponibilidad establecer compartido por todas las máquinas virtuales de base de datos. De esta forma, garantiza que siempre habrá una VM en el conjunto de ejecución durante el mantenimiento.

        {
          "apiVersion": "2015-06-15",
          "type": "Microsoft.Compute/availabilitySets",
          "name": "[variables('dbVMSetting').avsetName]",
          "location": "[variables('location')]",
          "tags": {
            "displayName": "AvailabilitySet - DB"
          }
        },

11. Desplácese hacia abajo hasta el siguiente recurso. Este recurso representa la base de datos de máquinas virtuales, tal como se muestra en la primera algunas líneas que se muestran a continuación. Observe el uso de la función **Copiar** nuevo, asegurarse de que se crean varios VM en el parámetro **dbCount** . Observe también la colección **dependsOn** . Muestra dos NIC que sea necesaria que debe crearse antes de implementa la máquina virtual, junto con el conjunto de disponibilidad y la cuenta de almacenamiento.

          "apiVersion": "2015-06-15",
          "type": "Microsoft.Compute/virtualMachines",
          "name": "[concat(variables('dbVMSetting').vmName,copyindex(1))]",
          "location": "[variables('location')]",
          "dependsOn": [
            "[concat('Microsoft.Network/networkInterfaces/', variables('dbVMSetting').nicName,'-DA-', copyindex(1))]",
            "[concat('Microsoft.Network/networkInterfaces/', variables('dbVMSetting').nicName,'-RA-', copyindex(1))]",
            "[concat('Microsoft.Compute/availabilitySets/', variables('dbVMSetting').avsetName)]",
            "[concat('Microsoft.Storage/storageAccounts/', parameters('prmStorageName'))]"
          ],
          "tags": {
            "displayName": "VMs - DB"
          },
          "copy": {
            "name": "dbvmcount",
            "count": "[parameters('dbCount')]"
          },

12. Desplácese hacia abajo en el recurso de máquina virtual para el elemento **networkProfile** , como se indica a continuación. Observe que existen dos NIC está referencia para cada máquina virtual. Cuando crea varias NIC para una máquina virtual, debe establecer la propiedad **principal** de una de las NIC en *true*y el resto en *false*.

        "networkProfile": {
          "networkInterfaces": [
            {
              "id": "[resourceId('Microsoft.Network/networkInterfaces', concat(variables('dbVMSetting').nicName,'-DA-',copyindex(1)))]",
              "properties": { "primary": true }
            },
            {
              "id": "[resourceId('Microsoft.Network/networkInterfaces', concat(variables('dbVMSetting').nicName,'-RA-',copyindex(1)))]",
              "properties": { "primary": false }
            }
          ]
        }
      }

## <a name="deploy-the-arm-template-by-using-click-to-deploy"></a>Implementar la plantilla ARM mediante hacer clic para implementar

> [AZURE.IMPORTANT] Asegúrese de que seguir los pasos de [requisitos previos](#Pre-requisites) antes de seguir las instrucciones a continuación.

La plantilla de ejemplo disponible en el repositorio público utiliza un archivo de parámetro que contiene el valor predeterminado valores que se usan para generar el escenario descrito anteriormente. Para implementar esta plantilla con clic para implementar, siga [este vínculo](https://github.com/Azure/azure-quickstart-templates/tree/master/IaaS-Story/11-MultiNIC), a la derecha del **grupo de recursos de back-end (consulte la documentación)** , haga clic en **desplegar en Azure**, reemplace los valores de parámetro de forma predeterminada, si es necesario y siga las instrucciones en el portal.

La figura siguiente muestra el contenido del nuevo grupo de recursos después de la implementación.

![Grupo de recursos de back-end](./media/virtual-network-deploy-multinic-arm-template/Figure2.png)

## <a name="deploy-the-template-by-using-powershell"></a>Implementar la plantilla con PowerShell

Para implementar la plantilla que descargó con PowerShell, siga los pasos siguientes.

[AZURE.INCLUDE [powershell-preview-include.md](../../includes/powershell-preview-include.md)]

3. Ejecutar la **`New-AzureRmResourceGroup`** cmdlet para crear un grupo de recursos con la plantilla.

        New-AzureRmResourceGroup -Name IaaSStory-Backend -Location uswest `
            -TemplateFile 'https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/11-MultiNIC/azuredeploy.json' `
            -TemplateParameterFile 'https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/11-MultiNIC/azuredeploy.parameters.json'

    Resultados esperados:

        ResourceGroupName : IaaSStory-Backend
        Location          : westus
        ProvisioningState : Succeeded
        Tags              :
        Permissions       :
                            Actions  NotActions
                            =======  ==========
                            *                  

        Resources         :
                            Name                 Type                                 Location
                            ===================  ===================================  ========
                            ASDB                 Microsoft.Compute/availabilitySets   westus  
                            DB1                  Microsoft.Compute/virtualMachines    westus  
                            DB2                  Microsoft.Compute/virtualMachines    westus  
                            NICDB-DA-1           Microsoft.Network/networkInterfaces  westus  
                            NICDB-DA-2           Microsoft.Network/networkInterfaces  westus  
                            NICDB-RA-1           Microsoft.Network/networkInterfaces  westus  
                            NICDB-RA-2           Microsoft.Network/networkInterfaces  westus  
                            wtestvnetstorageprm  Microsoft.Storage/storageAccounts    westus  

        ResourceId        : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/IaaSStory-Backend

## <a name="deploy-the-template-by-using-the-azure-cli"></a>Implementar la plantilla mediante la CLI de Azure

Para implementar la plantilla mediante la CLI de Azure, siga los pasos siguientes.

1. Si nunca ha usado CLI de Azure, vea [instalar y configurar la CLI de Azure](../xplat-cli-install.md) y siga las instrucciones hasta el punto donde seleccionar su cuenta de Azure y suscripción.
2. Ejecutar la **`azure config mode`** comando para cambiar al modo de administrador de recursos, como se muestra a continuación.

        azure config mode arm

    Éste es el resultado esperado para el comando anterior:

        info:    New mode is arm

3. Abra el [archivo de parámetro](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/11-MultiNIC/azuredeploy.parameters.json), seleccione su contenido y guardarlo en un archivo en su equipo. En este ejemplo, hemos guardado el archivo de parámetros *parameters.json*.

4. Ejecutar la **`azure group deployment create`** cmdlet para implementar el nuevo VNet mediante la plantilla y el parámetro de archivos que ha descargado y modificado por encima. La lista que aparece después de la salida explica los parámetros que se utiliza.

        azure group create -n IaaSStory-Backend -l westus --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/11-MultiNIC/azuredeploy.json -e parameters.json

    Resultados esperados:

        info:    Executing command group create
        + Getting resource group IaaSStory-Backend
        + Creating resource group IaaSStory-Backend
        info:    Created resource group IaaSStory-Backend
        + Initializing template configurations and parameters
        + Creating a deployment
        info:    Created template deployment "azuredeploy"
        data:    Id:                  /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/IaaSStory-Backend
        data:    Name:                IaaSStory-Backend
        data:    Location:            westus
        data:    Provisioning State:  Succeeded
        data:    Tags: null
        data:
        info:    group create command OK
