<properties
   pageTitle="Implementar calcular recursos con las plantillas de administrador de recursos de Azure | Microsoft Azure"
   description="Tutorial de DotNet principales de Azure Máquina Virtual"
   services="virtual-machines-windows"
   documentationCenter="virtual-machines"
   authors="neilpeterson"
   manager="timlt"
   editor="tysonn"
   tags="azure-resource-manager"/>

<tags
   ms.service="virtual-machines-windows"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-windows"
   ms.workload="infrastructure-services"
   ms.date="10/21/2016"
   ms.author="nepeters"/>

# <a name="application-architecture-with-azure-resource-manager-templates"></a>Arquitectura de la aplicación con las plantillas de administrador de recursos de Azure

Al desarrollar una implementación de administrador de recursos de Azure, calcular requisitos deben asignarse a los servicios y recursos de Azure. Si una aplicación consta de varios extremos de http, una base de datos y un servicio de caché de datos, los recursos de Azure que alojan cada de estos componentes debe ser racionalizado. Por ejemplo, la aplicación de la tienda de música de ejemplo incluye una aplicación web que se hospeda en una máquina virtual y una base de datos SQL, que se encuentra en la base de datos de SQL Azure. 

Este documento describe cómo se configuran los recursos de proceso de la tienda de música en la plantilla de administrador de recursos de Azure de ejemplo. Se resaltan todas las dependencias y configuraciones únicas. Para obtener la mejor experiencia, implementar previamente una instancia de la solución a su suscripción de Azure y funcionan junto con la plantilla de administrador de recursos de Azure. La plantilla completa puede encontrarse aquí: [Implementación de almacenamiento de música en Windows](https://github.com/Microsoft/dotnet-core-sample-templates/tree/master/dotnet-core-music-windows).

## <a name="virtual-machine"></a>Máquina virtual

La aplicación de almacenamiento de música incluye una aplicación web donde pueden buscar y adquirir música clientes. Si bien hay varios servicios de Azure que pueden hospedar aplicaciones web, en este ejemplo, se utiliza una máquina Virtual. Usar la plantilla de la tienda de música de ejemplo, se implementa una máquina virtual, instalar un servidor web y el sitio Web de la tienda de música instalado y configurado. Por motivos de este artículo, se detalla la implementación de la máquina virtual. La configuración del servidor web y la aplicación se detalla en un artículo posterior.

Una máquina virtual pueden agregarse a una plantilla con el Asistente para agregar nuevo recurso de Visual Studio o insertando JSON válido en la plantilla de implementación. Al implementar una máquina virtual, también se necesitan varios recursos relacionados. Si utiliza Visual Studio para crear la plantilla, estos recursos se crean para usted. Si crear manualmente la plantilla, estos recursos deben insertarse y configurarse.

Siga este vínculo para ver el ejemplo JSON dentro de la plantilla de administrador de recursos: [JSON de máquina Virtual](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-windows/azuredeploy.json#L285).

```none
{
  {
  "apiVersion": "2015-06-15",
  "type": "Microsoft.Compute/virtualMachines",
  "name": "[concat(variables('vmName'),copyindex())]",
  "location": "[resourceGroup().location]",
  "copy": {
    "name": "virtualMachineLoop",
    "count": "[parameters('numberOfInstances')]"
  },
  "tags": {
    "displayName": "virtual-machine"
  },
  "dependsOn": [
    "[concat('Microsoft.Storage/storageAccounts/', variables('vhdStorageName'))]",
    "[concat('Microsoft.Compute/availabilitySets/', variables('availabilitySetName'))]",
    "nicLoop"
  ],
  "properties": {
    "availabilitySet": {
      "id": "[resourceId('Microsoft.Compute/availabilitySets', variables('availabilitySetName'))]"
    },
  ........<truncated>  
}
```

Una vez implementado, las propiedades de la máquina virtual pueden verse en el portal de Azure.

![Máquina virtual](./media/virtual-machines-windows-dotnet-core/vm-win.png)

## <a name="storage-account"></a>Cuenta de almacenamiento

Cuentas de almacenamiento tienen muchas opciones de almacenamiento y las capacidades. Para el contexto de máquinas virtuales de Azure, una cuenta de almacenamiento contiene las unidades de disco duras virtuales de la máquina virtual y los discos de datos adicionales. El ejemplo de la tienda de música incluye una cuenta de almacenamiento para que contenga la unidad de disco dura virtual de cada máquina virtual de la implementación. 

Siga este vínculo para ver el ejemplo JSON dentro de la plantilla de administrador de recursos: [Cuenta de almacenamiento](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-windows/azuredeploy.json#L98).


```none
{
  "apiVersion": "2015-06-15",
  "type": "Microsoft.Storage/storageAccounts",
  "name": "[variables('vhdStorageName')]",
  "location": "[resourceGroup().location]",
  "tags": {
    "displayName": "storage-account"
  },
  "properties": {
    "accountType": "[variables('vhdStorageType')]"
  }
}
```

Una cuenta de almacenamiento está asociada con un equipo virtual dentro de la declaración de plantilla de administrador de recursos de la máquina virtual. 

Siga este vínculo para ver el ejemplo JSON dentro de la plantilla de administrador de recursos: [asociación de máquina Virtual y cuenta de almacenamiento](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-windows/azuredeploy.json#L321).

```none
"osDisk": {
  "name": "osdisk",
  "vhd": {
    "uri": "[concat(reference(concat('Microsoft.Storage/storageAccounts/',variables('vhdStorageName')), '2015-06-15').primaryEndpoints.blob,'vhds/osdisk', copyindex(), '.vhd')]"
  },
  "caching": "ReadWrite",
  "createOption": "FromImage"
}
```

Después de la implementación, la cuenta de almacenamiento se pueden visualizar en el portal de Azure.

![Cuenta de almacenamiento](./media/virtual-machines-windows-dotnet-core/storacct-win.png)

Si hace clic en el contenedor de blobs de cuenta de almacenamiento, se puede ver el archivo de la unidad de disco duro virtual para cada máquina virtual implementado con la plantilla.

![Unidades de disco duro virtuales](./media/virtual-machines-windows-dotnet-core/vhd-win.png)

Para obtener más información sobre el almacenamiento de Azure, consulte la [documentación de almacenamiento de Azure](https://azure.microsoft.com/documentation/services/storage/).

## <a name="virtual-network"></a>Red virtual

Si una máquina virtual requiere redes internas, como la capacidad de comunicarse con otros recursos de Azure y máquinas virtuales de Windows, se requiere una red Virtual de Azure.  Una red virtual no tiene la máquina virtual accesibles en internet. Conectividad pública requiere una dirección IP pública, que se detalla más adelante en esta serie.

Siga este vínculo para ver el ejemplo JSON dentro de la plantilla de administrador de recursos: [red Virtual y subredes](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-windows/azuredeploy.json#L126).

```none
{
  "apiVersion": "2015-06-15",
  "type": "Microsoft.Network/virtualNetworks",
  "name": "[variables('virtualNetworkName')]",
  "location": "[resourceGroup().location]",
  "dependsOn": [
    "[concat('Microsoft.Network/networkSecurityGroups/', variables('networkSecurityGroup'))]"
  ],
  "tags": {
    "displayName": "virtual-network"
  },
  "properties": {
    "addressSpace": {
      "addressPrefixes": [
        "10.0.0.0/24"
      ]
    },
    "subnets": [
      {
        "name": "[variables('subnetName')]",
        "properties": {
          "addressPrefix": "10.0.0.0/24",
          "networkSecurityGroup": {
            "id": "[resourceId('Microsoft.Network/networkSecurityGroups', variables('networkSecurityGroup'))]"
          }
        }
      }
    ]
  }
}
```

Desde el portal de Azure, la red virtual es similar a la siguiente imagen. Observe que todos los equipos virtuales que se implementan con la plantilla están conectados a la red virtual.

![Red virtual](./media/virtual-machines-windows-dotnet-core/vnet-win.png)

## <a name="network-interface"></a>Interfaz de red

 Una interfaz de red conecta a una máquina virtual a una red virtual, específicamente a una subred que se ha definido en la red virtual. 
 
 Siga este vínculo para ver el ejemplo JSON dentro de la plantilla de administrador de recursos: [Interfaz de red](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-windows/azuredeploy.json#L156).
 
```none
{
  "apiVersion": "2015-06-15",
  "type": "Microsoft.Network/networkInterfaces",
  "name": "[concat(variables('networkInterfaceName'), copyindex())]",
  "location": "[resourceGroup().location]",
  "tags": {
    "displayName": "network-interface"
  },
  "copy": {
    "name": "nicLoop",
    "count": "[parameters('numberOfInstances')]"
  },
  "dependsOn": [
    "[concat('Microsoft.Network/virtualNetworks/', variables('virtualNetworkName'))]",
    "[concat('Microsoft.Network/loadBalancers/', variables('loadBalancerName'))]",
    "[concat('Microsoft.Network/publicIPAddresses/', variables('publicIpAddressName'))]",
    "[concat('Microsoft.Network/loadBalancers/', variables('loadBalancerName'), '/inboundNatRules/', 'RDP-VM', copyIndex())]"
  ],
  "properties": {
    "ipConfigurations": [
      {
        "name": "ipconfig",
        "properties": {
          "privateIPAllocationMethod": "Dynamic",
          "subnet": {
            "id": "[variables('subnetRef')]"
          },
          "loadBalancerBackendAddressPools": [
            {
              "id": "[variables('lbPoolID')]"
            }
          ],
          "loadBalancerInboundNatRules": [
            {
              "id": "[concat(variables('lbID'),'/inboundNatRules/RDP-VM', copyIndex())]"
            }
          ]
        }
      }
    ]
  }
}
```

Cada recurso de máquina virtual incluye un perfil de red. La interfaz de red está asociada con la máquina virtual en este perfil.  

Siga este vínculo para ver el ejemplo JSON dentro de la plantilla de administrador de recursos: [Perfil de red de la máquina Virtual](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-windows/azuredeploy.json#L330).


```none
"networkProfile": {
  "networkInterfaces": [
    {
      "id": "[resourceId('Microsoft.Network/networkInterfaces', concat(variables('networkInterfaceName'), copyindex()))]"
    }
  ]
}
```

Desde el portal de Azure, la interfaz de red es similar a la siguiente imagen. La dirección IP interna y la asociación de máquina virtual puede verse en el recurso de interfaz de red.

![Interfaz de red](./media/virtual-machines-windows-dotnet-core/nic-win.png)

Para obtener más información sobre redes virtuales de Azure, consulte la [documentación de red Virtual de Azure](https://azure.microsoft.com/documentation/services/virtual-network/).

## <a name="azure-sql-database"></a>Base de datos SQL Azure

Además de una máquina virtual hospedar el sitio Web de la tienda de música, una base de datos de SQL Azure se implementa para alojar la base de datos de la tienda de música. La ventaja de usar la base de datos de SQL Azure aquí es que no es necesario un segundo conjunto de máquinas virtuales y escala y disponibilidad integrado en el servicio.

Una base de datos de SQL Azure puede agregarse mediante Visual Studio Agregar nuevo recurso asistente, o insertando JSON válido en una plantilla. El recurso de SQL Server incluye un nombre de usuario y contraseña que se le conceden derechos administrativos en la instancia SQL. Además, se agrega un recurso de firewall SQL. De forma predeterminada, aplicaciones hospedadas en Azure son puedas conectarse a la instancia SQL. Para permitir que la aplicación externa tal un SQL Server Management studio para conectarse a la instancia de SQL, el firewall debe estar configurado. Por motivos de la demostración de la tienda de música, la configuración predeterminada es adecuada. 

Siga este vínculo para ver el ejemplo JSON dentro de la plantilla de administrador de recursos: [DB de SQL Azure](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-windows/azuredeploy.json#L379).


```none
{
  "apiVersion": "2014-04-01-preview",
  "type": "Microsoft.Sql/servers",
  "name": "[variables('musicstoresqlName')]",
  "location": "[resourceGroup().location]",
  "dependsOn": [],
  "tags": {
    "displayName": "sql-music-store"
  },
  "properties": {
    "administratorLogin": "[parameters('adminUsername')]",
    "administratorLoginPassword": "[parameters('adminPassword')]"
  },
  "resources": [
    {
      "apiVersion": "2014-04-01-preview",
      "type": "firewallrules",
      "name": "firewall-allow-azure",
      "location": "[resourceGroup().location]",
      "dependsOn": [
        "[concat('Microsoft.Sql/servers/', variables('musicstoresqlName'))]"
      ],
      "properties": {
        "startIpAddress": "0.0.0.0",
        "endIpAddress": "0.0.0.0"
      }
    }
  ]
}
```

Vista de SQL server y base de datos de MusicStore tal como se muestra en el portal de Azure.

![SQL Server](./media/virtual-machines-windows-dotnet-core/sql-win.png)

Para obtener más información sobre cómo implementar la base de datos de SQL Azure, consulte la [documentación de la base de datos de SQL Azure](https://azure.microsoft.com/documentation/services/sql-database/).

## <a name="next-step"></a>Siguiente paso

<hr>

[Paso 2: acceso y seguridad en Azure plantillas de administrador de recursos](./virtual-machines-windows-dotnet-core-3-access-security.md)
