<properties
    pageTitle="Crear una máquina virtual con una plantilla de administrador de recursos | Microsoft Azure"
    description="Usar una plantilla de administrador de recursos y PowerShell para crear fácilmente una nueva máquina virtual de Windows."
    services="virtual-machines-windows"
    documentationCenter=""
    authors="davidmu1"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="na"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/06/2016"
    ms.author="davidmu"/>

# <a name="create-a-windows-virtual-machine-with-a-resource-manager-template"></a>Crear una máquina virtual de Windows con una plantilla de administrador de recursos

En este artículo se presenta una plantilla de administrador de recursos de Azure y le muestra cómo usar PowerShell para implementarlo. La plantilla implementa una sola máquina virtual con Windows Server en una red virtual nueva con una sola subred.

Tardará aproximadamente 20 minutos para realizar los pasos de este artículo.

> [AZURE.IMPORTANT] Si desea que su máquina virtual forme parte de un conjunto de disponibilidad, agregarlo al conjunto cuando se crea la máquina virtual. Actualmente no hay una manera de agregar una máquina virtual a una disponibilidad establecer después de que se ha creado.

## <a name="step-1-create-the-template-file"></a>Paso 1: Crear el archivo de plantilla

Puede crear su propia plantilla con la información que se encuentra en el [Administrador de recursos de Azure creación plantillas](../resource-group-authoring-templates.md). También puede implementar plantillas que se han creado para usted de [Azure tutoriales plantillas](https://azure.microsoft.com/documentation/templates/).

1. Abra el editor de texto y agregar el elemento de esquema requerido y el elemento contentVersion necesarios:

        {
          "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
        }
2. [Parámetros](../resource-group-authoring-templates.md#parameters) no son siempre obligatorios, pero proporciona una manera de valores de entrada cuando se implementa la plantilla. Agregar el elemento de parámetros y sus elementos secundarios después del elemento contentVersion:

        {
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
          "contentVersion": "1.0.0.0",
          "parameters": {
            "adminUserName": { "type": "string" },
            "adminPassword": { "type": "securestring" }
          },
        }

3. [Variables de](../resource-group-authoring-templates.md#variables) puede usarse en una plantilla para especificar valores que se pueden cambiar con frecuencia o que tenga que crear a partir de una combinación de valores de parámetro. Agregue el elemento de variables después de la sección de parámetros:

        {
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
          "contentVersion": "1.0.0.0",
          "parameters": {
            "adminUsername": { "type": "string" },
            "adminPassword": { "type": "securestring" }
          },
          "variables": {
            "vnetID":"[resourceId('Microsoft.Network/virtualNetworks','myvn1')]",
            "subnetRef": "[concat(variables('vnetID'),'/subnets/mysn1')]"  
          },
        }
        
4. [Recursos](../resource-group-authoring-templates.md#resources) como la máquina virtual, la red virtual y la cuenta de almacenamiento se definen siguiente en la plantilla. Agregue la sección de recursos después de la sección de variables:

        {
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
          "contentVersion": "1.0.0.0",
          "parameters": {
            "adminUsername": { "type": "string" },
            "adminPassword": { "type": "securestring" }
          },
          "variables": {
            "vnetID":"[resourceId('Microsoft.Network/virtualNetworks','myvn1')]",
            "subnetRef": "[concat(variables('vnetID'),'/subnets/mysn1')]"
          },
          "resources": [
            {
              "type": "Microsoft.Storage/storageAccounts",
              "name": "mystorage1",
              "apiVersion": "2015-06-15",
              "location": "[resourceGroup().location]",
              "properties": { "accountType": "Standard_LRS" }
            },
            {
              "apiVersion": "2016-03-30",
              "type": "Microsoft.Network/publicIPAddresses",
              "name": "myip1",
              "location": "[resourceGroup().location]",
              "properties": {
                "publicIPAllocationMethod": "Dynamic",
                "dnsSettings": { "domainNameLabel": "mydns1" }
              }
            },
            {
              "apiVersion": "2016-03-30",
              "type": "Microsoft.Network/virtualNetworks",
              "name": "myvn1",
              "location": "[resourceGroup().location]",
              "properties": {
                "addressSpace": { "addressPrefixes": [ "10.0.0.0/16" ] },
                "subnets": [ {
                  "name": "mysn1",
                  "properties": { "addressPrefix": "10.0.0.0/24" }
                } ]
              }
            },
            {
              "apiVersion": "2016-03-30",
              "type": "Microsoft.Network/networkInterfaces",
              "name": "mync1",
              "location": "[resourceGroup().location]",
              "dependsOn": [
                "Microsoft.Network/publicIPAddresses/myip1",
                "Microsoft.Network/virtualNetworks/myvn1"
              ],
              "properties": {
                "ipConfigurations": [ {
                  "name": "ipconfig1",
                  "properties": {
                    "privateIPAllocationMethod": "Dynamic",
                    "publicIPAddress": {
                      "id": "[resourceId('Microsoft.Network/publicIPAddresses', 'myip1')]"
                    },
                    "subnet": { "id": "[variables('subnetRef')]" }
                  }
                } ]
              }
            },
            {
              "apiVersion": "2016-03-30",
              "type": "Microsoft.Compute/virtualMachines",
              "name": "myvm1",
              "location": "[resourceGroup().location]",
              "dependsOn": [
                "Microsoft.Network/networkInterfaces/mync1",
                "Microsoft.Storage/storageAccounts/mystorage1"
              ],
              "properties": {
                "hardwareProfile": { "vmSize": "Standard_A1" },
                "osProfile": {
                  "computerName": "myvm1",
                  "adminUsername": "[parameters('adminUsername')]",
                  "adminPassword": "[parameters('adminPassword')]"
                },
                "storageProfile": {
                  "imageReference": {
                    "publisher": "MicrosoftWindowsServer",
                    "offer": "WindowsServer",
                    "sku": "2012-R2-Datacenter",
                    "version" : "latest"
                  },
                  "osDisk": {
                    "name": "myosdisk1",
                    "vhd": {
                      "uri": "https://mystorage1.blob.core.windows.net/vhds/myosdisk1.vhd"
                    },
                    "caching": "ReadWrite",
                    "createOption": "FromImage"
                  }
                },
                "networkProfile": {
                  "networkInterfaces" : [ {
                    "id": "[resourceId('Microsoft.Network/networkInterfaces','mync1')]"
                  } ]
                }
              }
            } ]
          }
          
    >[AZURE.NOTE] En este artículo, se crea una máquina virtual ejecuta una versión del sistema operativo Windows Server. Para obtener más información acerca de cómo seleccionar otras imágenes, vea [navegar y seleccionar máquina virtual Azure imágenes con Windows PowerShell y CLI Azure](virtual-machines-linux-cli-ps-findimage.md).  
            
2. Guarde el archivo de plantilla como *VirtualMachineTemplate.json*.

## <a name="step-2-create-the-parameters-file"></a>Paso 2: Crear el archivo de parámetros

Para especificar valores para los parámetros de recursos que se han definido en la plantilla, cree un archivo de parámetros que contiene los valores que se usan cuando se implementa la plantilla.

1. En el editor de texto, copie este contenido JSON a un nuevo archivo llamado *Parameters.json*:

        {
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
          "contentVersion": "1.0.0.0",
          "parameters": {
            "adminUserName": { "value": "mytestacct1" },
            "adminPassword": { "value": "mytestpass1" }
          }
        }

    >[AZURE.NOTE] Obtenga más información sobre [los requisitos de usuario y contraseña](virtual-machines-windows-faq.md#what-are-the-username-requirements-when-creating-a-vm).

2. Guarde el archivo de parámetros.

## <a name="step-3-install-azure-powershell"></a>Paso 3: Instalar PowerShell Azure

Vea [cómo instalar y configurar Azure PowerShell](../powershell-install-configure.md) para obtener información sobre cómo instalar la última versión de PowerShell de Azure, seleccionar la suscripción e iniciar sesión con su cuenta.

## <a name="step-4-create-a-resource-group"></a>Paso 4: Crear un grupo de recursos

Todos los recursos se deben implementar en un [grupo de recursos](../azure-resource-manager/resource-group-overview.md).

1. Obtener una lista de ubicaciones disponibles donde se pueden crear recursos.

        Get-AzureRmLocation | sort DisplayName | Select DisplayName

2. Reemplace el valor de **$locName** con una ubicación de la lista, por ejemplo **Central de Estados Unidos**. Cree la variable.

        $locName = "location name"
        
3. Reemplace el valor de **$rgName** con el nombre del nuevo grupo de recursos. Crear la variable y el grupo de recursos.

        $rgName = "resource group name"
        New-AzureRmResourceGroup -Name $rgName -Location $locName
        
    Verá algo parecido a este ejemplo:
    
        ResourceGroupName : myrg1
        Location          : centralus
        ProvisioningState : Succeeded
        Tags              :
        ResourceId        : /subscriptions/{subscription-id}/resourceGroups/myrg1

## <a name="step-5-create-the-resources-with-the-template-and-parameters"></a>Paso 5: Crear los recursos con la plantilla y parámetros

Reemplace el valor de **$templateFile** por la ruta de acceso y el nombre del archivo de plantilla. Reemplace el valor de **$parameterFile** por la ruta de acceso y el nombre del archivo de parámetros. Crear las variables y, a continuación, implementar la plantilla. 

        $templateFile = "template file"
        $parameterFile = "parameter file"
        New-AzureRmResourceGroupDeployment -ResourceGroupName $rgName -TemplateFile $templateFile -TemplateParameterFile $parameterFile

    You will see something like this:

        DeploymentName    : VirtualMachineTemplate
        ResourceGroupName : myrg1
        ProvisioningState : Succeeded
        Timestamp         : 4/14/2016 8:11:37 PM
        Mode              : Incremental
        TemplateLink      :
        Parameters        :
                            Name             Type                       Value
                            ===============  =========================  ==========
                            adminUsername    String                     mytestacct1
                            adminPassword    SecureString

        Outputs           :

>[AZURE.NOTE] También puede implementar plantillas y los parámetros de una cuenta de almacenamiento de Azure. Para obtener más información, vea [Usar PowerShell de Azure con el almacenamiento de Azure](../storage/storage-powershell-guide-full.md).

## <a name="next-steps"></a>Pasos siguientes

- Si hay problemas con la implementación, sería un paso siguiente buscar en [implementaciones de grupo de recursos de solución de problemas con el portal de Azure](../resource-manager-troubleshoot-deployments-portal.md)
- Obtenga información sobre cómo administrar la máquina virtual que ha creado revisando [máquinas virtuales de administrar con el Administrador de recursos de Azure y PowerShell](virtual-machines-windows-ps-manage.md).
