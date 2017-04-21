<properties
   pageTitle="Tutorial de plantilla de administrador de recursos | Microsoft Azure"
   description="Obtener un tutorial paso a paso de una plantilla de administrador de recursos el aprovisionamiento de una arquitectura de Azure IaaS básica."
   services="azure-resource-manager"
   documentationCenter="na"
   authors="navalev"
   manager="timlt"
   editor=""/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/04/2016"
   ms.author="navale;tomfitz"/>
   
# <a name="resource-manager-template-walkthrough"></a>Tutorial de plantilla de administrador de recursos

Una de las primeras preguntas al crear una plantilla es "cómo iniciar". Uno puede empezar desde una plantilla en blanco, siguiendo la estructura básica que se describe en el [artículo de la plantilla de creación](resource-group-authoring-templates.md#template-format)y agregar los recursos y parámetros adecuados y variables. Una buena alternativa sería empiece yendo a través de la [Galería de tutorial rápido](https://github.com/Azure/azure-quickstart-templates) y busque escenarios similares a la que está intentando crear. Puede combinar varias plantillas o modificar uno ya existente para adaptarla a su propio escenario específico. 

Echemos un vistazo a una infraestructura común:

* Dos máquinas virtuales que usan la misma cuenta de almacenamiento están en el mismo conjunto de disponibilidad y, en la misma subred de una red virtual.
* Una única dirección IP de VM y NIC para cada máquina virtual.
* Un equilibrador de carga con una regla en el puerto 80 equilibrio de carga

![arquitectura](./media/resource-group-overview/arm_arch.png)

Este tema le guiará por los pasos de la creación de una plantilla de administrador de recursos para que la infraestructura. La plantilla final que crear está basada en una plantilla de tutorial rápido denominada [máquinas 2 virtuales en un equilibrador de carga y reglas de equilibrio de carga](https://azure.microsoft.com/documentation/templates/201-2-vms-loadbalancer-lbrules/).

Pero, que es una gran cantidad crear a la vez, así que vamos a crear una cuenta de almacenamiento e implementarlo. Una vez que domine la creación de la cuenta de almacenamiento, agregará los otros recursos y volver a implementar la plantilla para completar la infraestructura.

>[AZURE.NOTE] Puede usar cualquier tipo de editor al crear la plantilla. Visual Studio proporciona herramientas que simplifican el desarrollo de la plantilla, pero no es necesario Visual Studio para completar este tutorial. Para obtener un tutorial acerca de cómo utilizar Visual Studio para crear una implementación de la aplicación Web y base de datos de SQL, vea [crear e implementar grupos de recursos de Azure a través de Visual Studio](vs-azure-tools-resource-groups-deployment-projects-create-deploy.md). 

## <a name="create-the-resource-manager-template"></a>Crear la plantilla de administrador de recursos

La plantilla es un archivo JSON que define todos los recursos que va a implementar. También le permite definir los parámetros que se especifican durante la implementación, las variables de construcción de otros valores y expresiones y resultados de la implementación. 

Comencemos con la plantilla más sencilla:

```json
    {
      "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {  },
      "variables": {  },
      "resources": [  ],
      "outputs": {  }
    }
 ```

Guardar este archivo como **azuredeploy.json** (tenga en cuenta que la plantilla puede tener cualquier nombre que desee, solo tiene que TI debe ser un archivo de json).

## <a name="create-a-storage-account"></a>Crear una cuenta de almacenamiento
En la sección **recursos** , agregar un objeto que define la cuenta de almacenamiento, tal como se muestra a continuación. 

```json
"resources": [
  {
    "type": "Microsoft.Storage/storageAccounts",
    "name": "[parameters('storageAccountName')]",
    "apiVersion": "2015-06-15",
    "location": "[resourceGroup().location]",
    "properties": {
      "accountType": "Standard_LRS"
    }
  }
]
```

Es podrán que se pregunte procedencia de estas propiedades y los valores de. El **tipo**de propiedades, el **nombre**, la **apiVersion**y la **ubicación** son elementos estándar que están disponibles para todos los tipos de recursos. Puede obtener información sobre los elementos comunes de los [recursos](resource-group-authoring-templates.md#resources). **nombre** se establece en un valor de parámetro que se pasa durante la implementación y la **ubicación** como la ubicación usada por el grupo de recursos. Veremos cómo determinar el **tipo** y **apiVersion** en las secciones siguientes.

La sección **Propiedades** contiene todas las propiedades que son únicas en un determinado tipo. Los valores que especifique en esta sección exactamente coincide con la operación de COLOCACIÓN de la API de REST para la creación de ese tipo de recurso. Al crear una cuenta de almacenamiento, debe proporcionar un **accountType**. Observe en la [API de REST para crear una cuenta de almacenamiento](https://msdn.microsoft.com/library/azure/mt163564.aspx) que la sección de propiedades de la operación de resto también contiene una propiedad **accountType** y se describen los valores permitidos. En este ejemplo, el tipo de cuenta se establece en **Standard_LRS**, pero puede especificar algún otro valor o permitir que los usuarios para pasar el tipo de cuenta como parámetro.

Ahora vamos a ir a la sección **parámetros** y vea cómo definir el nombre de la cuenta de almacenamiento. Puede obtener más información sobre el uso de parámetros en [parámetros](resource-group-authoring-templates.md#parameters). 

```json
"parameters" : {
    "storageAccountName": {
      "type": "string",
      "metadata": {
        "description": "Storage Account Name"
      }
    }
}
```
Aquí se define un parámetro de tipo cadena que contiene el nombre de la cuenta de almacenamiento. El valor de este parámetro se proporcionará durante la implementación de la plantilla.

## <a name="deploying-the-template"></a>Implementación de la plantilla
Tenemos una plantilla para crear una nueva cuenta de almacenamiento total. Como recordará, la plantilla se guardó en el archivo **azuredeploy.json** :

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters" : {
    "storageAccountName": {
      "type": "string",
      "metadata": {
        "description": "Storage Account Name"
      }
    }
  },  
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "name": "[parameters('storageAccountName')]",
      "apiVersion": "2015-06-15",
      "location": "[resourceGroup().location]",
      "properties": {
        "accountType": "Standard_LRS"
      }
    }
  ]
}
```

Hay algunas formas de implementar una plantilla, como puede ver en el [artículo de la implementación de recursos](resource-group-template-deploy.md). Para implementar la plantilla con PowerShell de Azure, use:

```powershell
# create a new resource group
New-AzureRmResourceGroup -Name ExampleResourceGroup -Location "West Europe"

# deploy the template to the resource group
New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup -TemplateFile azuredeploy.json
```

O bien, para implementar la plantilla con Azure CLI, use:

```
azure group create -n ExampleResourceGroup -l "West Europe"

azure group deployment create -f azuredeploy.json -g ExampleResourceGroup -n ExampleDeployment
```

¡Ahora es el orgulloso propietario de una cuenta de almacenamiento!

Los siguientes pasos será agregar todos los recursos necesarios para implementar la arquitectura que se describe en el inicio de este tutorial. Va a agregar estos recursos en la misma plantilla que ha estado trabajando.

## <a name="availability-set"></a>Conjunto de disponibilidad
Después de la definición de la cuenta de almacenamiento, agregue una disponibilidad establecido para los equipos virtuales. En este caso, no hay propiedades adicionales necesarios, por lo que su definición es muy sencilla. Consulte la [API de REST para crear un conjunto de disponibilidad](https://msdn.microsoft.com/library/azure/mt163607.aspx) de la sección propiedades completa, en caso de que quiera definir la actualización de dominio contar y errores dominio contar valores.

```json
{
   "type": "Microsoft.Compute/availabilitySets",
   "name": "[variables('availabilitySetName')]",
   "apiVersion": "2015-06-15",
   "location": "[resourceGroup().location]",
   "properties": {}
}
```

Observe que el **nombre** se establece en el valor de una variable. Para esta plantilla, el nombre del conjunto de disponibilidad es necesario en algunos lugares diferentes. Puede mantener más fácilmente la plantilla definiendo ese valor una vez y usarlo en varios lugares.

El valor que especifique para **tipo** contiene el proveedor de recursos y el tipo de recurso. Para los conjuntos de disponibilidad, el proveedor de recursos es **Microsoft.Compute** y el tipo de recurso es **availabilitySets**. Puede obtener la lista de proveedores de recursos disponibles, ejecute el siguiente comando PowerShell:

```powershell
    Get-AzureRmResourceProvider -ListAvailable
```

O bien, si está utilizando CLI de Azure, puede ejecutar el siguiente comando:
```
    azure provider list
```
Dado que en este tema va a crear con cuentas de almacenamiento, máquinas virtuales y una red virtual, deberá trabajar con:

- Microsoft.Storage
- Microsoft.Compute
- Microsoft.Network

Para ver los tipos de recursos de un proveedor concreto, ejecute el siguiente comando PowerShell:

```powershell
    (Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Compute).ResourceTypes
```

O bien, para CLI de Azure, el comando siguiente se devuelven los tipos disponibles en formato JSON y guardarlo en un archivo.

```
    azure provider show Microsoft.Compute --json > c:\temp.json
```

Debería ver **availabilitySets** como uno de los tipos de **Microsoft.Compute**. El nombre completo del tipo es **Microsoft.Compute/availabilitySets**. Puede determinar el nombre del tipo de recurso por cualquiera de los recursos en la plantilla.

## <a name="public-ip"></a>IP pública
Definir una dirección IP pública. De nuevo, mire la [API de REST para las direcciones IP públicas](https://msdn.microsoft.com/library/azure/mt163590.aspx) para las propiedades establecer.

```json
{
  "apiVersion": "2015-06-15",
  "type": "Microsoft.Network/publicIPAddresses",
  "name": "[parameters('publicIPAddressName')]",
  "location": "[resourceGroup().location]",
  "properties": {
    "publicIPAllocationMethod": "Dynamic",
    "dnsSettings": {
      "domainNameLabel": "[parameters('dnsNameforLBIP')]"
    }
  }
}
```

El método de asignación se establece en **dinámica** , pero puede establecer el valor necesita o configurarlo para que acepte un valor de parámetro. Habilita a los usuarios de la plantilla para pasar un valor de la etiqueta de nombre de dominio.

Ahora, veamos cómo determinar la **apiVersion**. El valor que especifique simplemente coincide con la versión de la API de REST que desea usar al crear el recurso. Por lo tanto, puede buscar en la documentación de la API de REST de ese tipo de recurso. O bien, puede ejecutar el siguiente comando PowerShell para un tipo concreto.

```powershell
    ((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Network).ResourceTypes | Where-Object ResourceTypeName -eq publicIPAddresses).ApiVersions
```
Que devuelve los valores siguientes:

    2015-06-15
    2015-05-01-preview
    2014-12-01-preview

Para ver las versiones de API con Azure CLI, ejecute el mismo comando **Mostrar azure proveedor** mostrado anteriormente.

Al crear una nueva plantilla, seleccione la versión más reciente de la API.

## <a name="virtual-network-and-subnet"></a>Subred y una red virtual
Crear una red virtual con una subred. Mire la [API de REST de redes virtuales](https://msdn.microsoft.com/library/azure/mt163661.aspx) para todas las propiedades establecer.

```json
{
   "apiVersion": "2015-06-15",
   "type": "Microsoft.Network/virtualNetworks",
   "name": "[parameters('vnetName')]",
   "location": "[resourceGroup().location]",
   "properties": {
     "addressSpace": {
       "addressPrefixes": [
         "10.0.0.0/16"
       ]
     },
     "subnets": [
       {
         "name": "[variables('subnetName')]",
         "properties": {
           "addressPrefix": "10.0.0.0/24"
         }
       }
     ]
   }
}
```

## <a name="load-balancer"></a>Equilibrador de carga
Ahora creará un equilibrador de carga externo opuestas. Dado que este equilibrador de carga, utiliza la dirección IP pública, debe declarar una dependencia en la dirección IP pública en la sección **dependsOn** . Esto significa que el equilibrador de carga no se implementará hasta que haya terminado la dirección IP pública implementar. Sin definir esta dependencia, recibirá un error porque el Administrador de recursos intentará implementar los recursos en paralelo e intentará establecer equilibrador de carga de la dirección IP pública que todavía no existe. 

También creará un conjunto de direcciones de back-end, un par de reglas NAT entrantes RDP en las máquinas virtuales y una regla de equilibrio de carga con un sondeo de tcp en el puerto 80 en esta definición de recursos. Desprotección la [API de REST de equilibrador de carga](https://msdn.microsoft.com/library/azure/mt163574.aspx) para todas las propiedades.

```json
{
   "apiVersion": "2015-06-15",
   "name": "[parameters('lbName')]",
   "type": "Microsoft.Network/loadBalancers",
   "location": "[resourceGroup().location]",
   "dependsOn": [
     "[concat('Microsoft.Network/publicIPAddresses/', parameters('publicIPAddressName'))]"
   ],
   "properties": {
     "frontendIPConfigurations": [
       {
         "name": "LoadBalancerFrontEnd",
         "properties": {
           "publicIPAddress": {
             "id": "[variables('publicIPAddressID')]"
           }
         }
       }
     ],
     "backendAddressPools": [
       {
         "name": "BackendPool1"
       }
     ],
     "inboundNatRules": [
       {
         "name": "RDP-VM0",
         "properties": {
           "frontendIPConfiguration": {
             "id": "[variables('frontEndIPConfigID')]"
           },
           "protocol": "tcp",
           "frontendPort": 50001,
           "backendPort": 3389,
           "enableFloatingIP": false
         }
       },
       {
         "name": "RDP-VM1",
         "properties": {
           "frontendIPConfiguration": {
             "id": "[variables('frontEndIPConfigID')]"
           },
           "protocol": "tcp",
           "frontendPort": 50002,
           "backendPort": 3389,
           "enableFloatingIP": false
         }
       }
     ],
     "loadBalancingRules": [
       {
         "name": "LBRule",
         "properties": {
           "frontendIPConfiguration": {
             "id": "[variables('frontEndIPConfigID')]"
           },
           "backendAddressPool": {
             "id": "[variables('lbPoolID')]"
           },
           "protocol": "tcp",
           "frontendPort": 80,
           "backendPort": 80,
           "enableFloatingIP": false,
           "idleTimeoutInMinutes": 5,
           "probe": {
             "id": "[variables('lbProbeID')]"
           }
         }
       }
     ],
     "probes": [
       {
         "name": "tcpProbe",
         "properties": {
           "protocol": "tcp",
           "port": 80,
           "intervalInSeconds": 5,
           "numberOfProbes": 2
         }
       }
     ]
   }
}
```

## <a name="network-interface"></a>Interfaz de red
Creará 2 interfaces de red, uno para cada máquina virtual. En lugar de tener que incluir las entradas duplicadas para las interfaces de red, puede usar la [función copyIndex()](resource-group-create-multiple.md) iteración en el bucle de copia (denominado nicLoop) y crear las interfaces de red números según se define en la `numberOfInstances` variables. La interfaz de red depende de la creación de la red virtual y el equilibrador de carga. Utiliza la subred definida en la creación de una red virtual y el identificador del equilibrador de carga para configurar el grupo de direcciones del equilibrador de carga y las reglas NAT entrantes.
Mire la [API de REST de interfaces de red](https://msdn.microsoft.com/library/azure/mt163668.aspx) para todas las propiedades.

```json
{
   "apiVersion": "2015-06-15",
   "type": "Microsoft.Network/networkInterfaces",
   "name": "[concat(parameters('nicNamePrefix'), copyindex())]",
   "location": "[resourceGroup().location]",
   "copy": {
     "name": "nicLoop",
     "count": "[variables('numberOfInstances')]"
   },
   "dependsOn": [
     "[concat('Microsoft.Network/virtualNetworks/', parameters('vnetName'))]",
     "[concat('Microsoft.Network/loadBalancers/', parameters('lbName'))]"
   ],
   "properties": {
     "ipConfigurations": [
       {
         "name": "ipconfig1",
         "properties": {
           "privateIPAllocationMethod": "Dynamic",
           "subnet": {
             "id": "[variables('subnetRef')]"
           },
           "loadBalancerBackendAddressPools": [
             {
               "id": "[concat(variables('lbID'), '/backendAddressPools/BackendPool1')]"
             }
           ],
           "loadBalancerInboundNatRules": [
             {
               "id": "[concat(variables('lbID'),'/inboundNatRules/RDP-VM', copyindex())]"
             }
           ]
         }
       }
     ]
   }
}
```

## <a name="virtual-machine"></a>Máquina virtual
Va a crear máquinas virtuales 2, con la función copyIndex(), igual que en la creación de las [interfaces de red](#network-interface).
La creación de la máquina virtual depende de la cuenta de almacenamiento, conjunto de disponibilidad y la interfaz de red. Se creará esta VM desde una imagen de marketplace, según se define en la `storageProfile` (propiedad) - `imageReference` se utiliza para definir el Editor de imágenes, oferta, sku y versión. Por último, se configura un perfil de diagnóstico para habilitar diagnósticos de la máquina virtual. 

Para buscar las propiedades pertinentes para una imagen de marketplace, siga los artículos [Seleccione imágenes de máquina virtual Linux](./virtual-machines/virtual-machines-linux-cli-ps-findimage.md) o [imágenes de máquina virtual de Windows](./virtual-machines/virtual-machines-windows-cli-ps-findimage.md) .

```json
{
   "apiVersion": "2015-06-15",
   "type": "Microsoft.Compute/virtualMachines",
   "name": "[concat(parameters('vmNamePrefix'), copyindex())]",
   "copy": {
     "name": "virtualMachineLoop",
     "count": "[variables('numberOfInstances')]"
   },
   "location": "[resourceGroup().location]",
   "dependsOn": [
     "[concat('Microsoft.Storage/storageAccounts/', parameters('storageAccountName'))]",
     "[concat('Microsoft.Network/networkInterfaces/', parameters('nicNamePrefix'), copyindex())]",
     "[concat('Microsoft.Compute/availabilitySets/', variables('availabilitySetName'))]"
   ],
   "properties": {
     "availabilitySet": {
       "id": "[resourceId('Microsoft.Compute/availabilitySets',variables('availabilitySetName'))]"
     },
     "hardwareProfile": {
       "vmSize": "[parameters('vmSize')]"
     },
     "osProfile": {
       "computerName": "[concat(parameters('vmNamePrefix'), copyIndex())]",
       "adminUsername": "[parameters('adminUsername')]",
       "adminPassword": "[parameters('adminPassword')]"
     },
     "storageProfile": {
       "imageReference": {
         "publisher": "[parameters('imagePublisher')]",
         "offer": "[parameters('imageOffer')]",
         "sku": "[parameters('imageSKU')]",
         "version": "latest"
       },
       "osDisk": {
         "name": "osdisk",
         "vhd": {
           "uri": "[concat('http://',parameters('storageAccountName'),'.blob.core.windows.net/vhds/','osdisk', copyindex(), '.vhd')]"
         },
         "caching": "ReadWrite",
         "createOption": "FromImage"
       }
     },
     "networkProfile": {
       "networkInterfaces": [
         {
           "id": "[resourceId('Microsoft.Network/networkInterfaces',concat(parameters('nicNamePrefix'),copyindex()))]"
         }
       ]
     },
     "diagnosticsProfile": {
       "bootDiagnostics": {
          "enabled": "true",
          "storageUri": "[concat('http://',parameters('storageAccountName'),'.blob.core.windows.net')]"
       }
     }
}
```

>[AZURE.NOTE] Imágenes publicado por **3ª proveedores**, debe especificar otra propiedad denominada `plan`. Un ejemplo puede encontrarse en [esta plantilla](https://github.com/Azure/azure-quickstart-templates/tree/master/checkpoint-single-nic) de la Galería de tutorial rápido. 

Termine de definir los recursos para la plantilla.

## <a name="parameters"></a>Parámetros

En la sección parámetros, defina los valores que se pueden especificar al implementar la plantilla. Solo definir parámetros para los valores que piensa debe variar durante la implementación. Puede proporcionar un valor predeterminado para un parámetro que se utiliza si no se proporciona durante la implementación. También puede definir los valores permitidos tal como se muestra para el parámetro **imageSKU** .

```json
"parameters": {
    "storageAccountName": {
      "type": "string",
      "metadata": {
        "description": "Name of storage account"
      }
    },
    "adminUsername": {
      "type": "string",
      "metadata": {
        "description": "Admin username"
      }
    },
    "adminPassword": {
      "type": "securestring",
      "metadata": {
        "description": "Admin password"
      }
    },
    "dnsNameforLBIP": {
      "type": "string",
      "metadata": {
        "description": "DNS for Load Balancer IP"
      }
    },
    "vmNamePrefix": {
      "type": "string",
      "defaultValue": "myVM",
      "metadata": {
        "description": "Prefix to use for VM names"
      }
    },
    "imagePublisher": {
      "type": "string",
      "defaultValue": "MicrosoftWindowsServer",
      "metadata": {
        "description": "Image Publisher"
      }
    },
    "imageOffer": {
      "type": "string",
      "defaultValue": "WindowsServer",
      "metadata": {
        "description": "Image Offer"
      }
    },
    "imageSKU": {
      "type": "string",
      "defaultValue": "2012-R2-Datacenter",
      "allowedValues": [
        "2008-R2-SP1",
        "2012-Datacenter",
        "2012-R2-Datacenter"
      ],
      "metadata": {
        "description": "Image SKU"
      }
    },
    "lbName": {
      "type": "string",
      "defaultValue": "myLB",
      "metadata": {
        "description": "Load Balancer name"
      }
    },
    "nicNamePrefix": {
      "type": "string",
      "defaultValue": "nic",
      "metadata": {
        "description": "Network Interface name prefix"
      }
    },
    "publicIPAddressName": {
      "type": "string",
      "defaultValue": "myPublicIP",
      "metadata": {
        "description": "Public IP Name"
      }
    },
    "vnetName": {
      "type": "string",
      "defaultValue": "myVNET",
      "metadata": {
        "description": "VNET name"
      }
    },
    "vmSize": {
      "type": "string",
      "defaultValue": "Standard_D1",
      "metadata": {
        "description": "Size of the VM"
      }
    }
  }
```

## <a name="variables"></a>Variables

En la sección de variables, puede definir valores que se usan en más de un lugar en su plantilla, o que se construyen de otras expresiones o variables. Variables con frecuencia se utilizan para simplificar la sintaxis de la plantilla.

```json
"variables": {
    "availabilitySetName": "myAvSet",
    "subnetName": "Subnet-1",
    "vnetID": "[resourceId('Microsoft.Network/virtualNetworks',parameters('vnetName'))]",
    "subnetRef": "[concat(variables('vnetID'),'/subnets/',variables ('subnetName'))]",
    "publicIPAddressID": "[resourceId('Microsoft.Network/publicIPAddresses',parameters('publicIPAddressName'))]",
    "numberOfInstances": 2,
    "lbID": "[resourceId('Microsoft.Network/loadBalancers',parameters('lbName'))]",
    "frontEndIPConfigID": "[concat(variables('lbID'),'/frontendIPConfigurations/LoadBalancerFrontEnd')]",
    "lbPoolID": "[concat(variables('lbID'),'/backendAddressPools/BackendPool1')]",
    "lbProbeID": "[concat(variables('lbID'),'/probes/tcpProbe')]"
  }
```

Ha completado la plantilla. Puede comparar la plantilla con la plantilla en la [Galería de tutorial rápido](https://github.com/Azure/azure-quickstart-templates) en [máquinas virtuales de 2 con equilibrador de carga y la plantilla de reglas de equilibrador de carga](https://github.com/Azure/azure-quickstart-templates/tree/master/201-2-vms-loadbalancer-lbrules)completa. La plantilla puede ser ligeramente distinta en función de los números de versión diferente. 

Puede volver a desplegar la plantilla usando los mismos comandos que utilizó cuando implementar la cuenta de almacenamiento. No debe eliminar la cuenta de almacenamiento antes de volver a implementar porque el Administrador de recursos omitirá volver a crear recursos que ya existen y no han cambiado.

## <a name="next-steps"></a>Pasos siguientes

- [Visualizador de plantilla de administrador de recursos de Azure (ARMViz)](http://armviz.io/#/) es una excelente herramienta para visualizar las plantillas ARM, como estén demasiado grandes para comprender acaba de leer el archivo de json.
- Para obtener más información sobre la estructura de una plantilla, consulte [plantillas de administrador de recursos de Azure de creación](resource-group-authoring-templates.md).
- Para obtener información sobre cómo implementar una plantilla, vea [implementar un grupo de recursos con el Administrador de recursos de Azure plantilla](resource-group-template-deploy.md)
