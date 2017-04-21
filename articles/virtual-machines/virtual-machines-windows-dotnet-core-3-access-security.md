<properties
   pageTitle="Acceso y seguridad en las plantillas de administrador de recursos de Azure | Microsoft Azure" 
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

# <a name="access-and-security-in-azure-resource-manager-templates"></a>Acceso y seguridad en las plantillas de administrador de recursos de Azure

Aplicaciones hospedadas en Azure es muy probable que necesitan pueden acceso a través de internet o de una VPN o conexión directa con Azure. Con el ejemplo de aplicación de la tienda de música, estará disponible el sitio web en internet con una dirección IP pública. Con el acceso establecido, se deben proteger conexiones con la aplicación y el acceso a los recursos de máquina virtual a sí mismos. Se proporciona esta seguridad de access con un grupo de seguridad de la red. 

¿Cómo se protege la aplicación de almacenamiento de música en la plantilla de administrador de recursos de Azure de ejemplo de detalles de este documento. Se resaltan todas las dependencias y configuraciones únicas. Para obtener la mejor experiencia, implementar previamente una instancia de la solución a su suscripción de Azure y funcionan junto con la plantilla de administrador de recursos de Azure. La plantilla completa puede encontrarse aquí: [Implementación de almacenamiento de música en Windows](https://github.com/Microsoft/dotnet-core-sample-templates/tree/master/dotnet-core-music-windows).


## <a name="public-ip-address"></a>Dirección IP pública

Para proporcionar acceso del público a un recurso de Azure, puede usar un recurso de dirección IP público. Dirección IP pública puede estar configurado con una dirección IP estática o dinámica. Si se usa una dirección dinámica y se detiene y se cancela la asignación de la máquina virtual, se quita las direcciones. Cuando se vuelva a iniciar el equipo, se puede asignar una dirección IP pública diferente. Para impedir que se modifiquen una dirección IP, puede utilizar una dirección IP reservada. 

Una dirección IP pública pueden agregarse a una plantilla de administrador de recursos de Azure mediante el Asistente Visual Studio agregar nuevos recursos o insertando JSON válido en una plantilla. 

Siga este vínculo para ver el ejemplo JSON dentro de la plantilla de administrador de recursos: la [Dirección IP pública](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-windows/azuredeploy.json#L110).


```none
{
  "apiVersion": "2015-06-15",
  "type": "Microsoft.Network/publicIPAddresses",
  "name": "[variables('publicIpAddressName')]",
  "location": "[resourceGroup().location]",
  "dependsOn": [],
  "tags": {
    "displayName": "public-ip"
  },
  "properties": {
    "publicIPAllocationMethod": "Dynamic",
    "dnsSettings": {
      "domainNameLabel": "[parameters('publicipaddressDnsName')]"
    }
  }
}
```

Una dirección IP pública se pueden asociar con un adaptador de red Virtual o un equilibrador de carga. En este ejemplo, porque el sitio Web de la tienda de música es cargan equilibradas en varias máquinas virtuales de Windows, la dirección IP pública está conectada al equilibrador de carga.

Siga este vínculo para ver el ejemplo JSON dentro de la plantilla de administrador de recursos: la [dirección IP pública de asociación con equilibrador de carga](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-windows/azuredeploy.json#L211).

```none
"frontendIPConfigurations": [
  {
    "properties": {
      "publicIPAddress": {
        "id": "[resourceId('Microsoft.Network/publicIPAddresses', variables('publicIpAddressName'))]"
      }
    },
    "name": "LoadBalancerFrontend"
  }
]
```

La dirección IP pública como se muestra en el portal de Azure. Observe que la dirección IP pública está asociada a un equilibrador de carga y no una máquina virtual. Equilibradores de carga de red se detallan en el documento siguiente de esta serie.

![Dirección IP pública](./media/virtual-machines-windows-dotnet-core/pubip-win.png)

Para obtener más información sobre las direcciones IP públicas Azure, consulte [direcciones IP en Azure](../virtual-network/virtual-network-ip-addresses-overview-arm.md).

## <a name="network-security-group"></a>Grupo de seguridad de red

Una vez que se ha establecido el acceso a los recursos de Azure, este acceso debe estar limitado. Para equipos virtuales Azure, acceso seguro se logra mediante un grupo de seguridad de la red. Con el ejemplo de aplicación de la tienda de música, todo el acceso a la máquina virtual está restringido excepto sobre puertos 80 para el acceso http y 3389 acceso RDP. Un grupo de seguridad de red pueden agregarse a una plantilla de administrador de recursos de Azure mediante el Asistente Visual Studio agregar nuevos recursos o insertando JSON válido en una plantilla.

Siga este vínculo para ver el ejemplo JSON dentro de la plantilla de administrador de recursos: [Grupo de seguridad de red](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-windows/azuredeploy.json#L57).

```none
{
  "apiVersion": "2016-03-30",
  "type": "Microsoft.Network/networkSecurityGroups",
  "name": "[variables('networkSecurityGroup')]",
  "location": "[resourceGroup().location]",
  "tags": {
    "displayName": "network-security-group"
  },
  "properties": {
    "securityRules": [
      {
        "name": "http",
        "properties": {
          "description": "http endpoint",
          "protocol": "Tcp",
          "sourcePortRange": "*",
          "destinationPortRange": "80",
          "sourceAddressPrefix": "*",
          "destinationAddressPrefix": "*",
          "access": "Allow",
          "priority": 100,
          "direction": "Inbound"
        }
      },
      ........<truncated> 
    ]
  }
},
```

En este ejemplo, el grupo de seguridad de red está asociada con el objeto de subred declarado en el recurso de red Virtual. 

Siga este vínculo para ver el ejemplo JSON dentro de la plantilla de administrador de recursos: [asociación de grupo de seguridad de red con una red Virtual](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-windows/azuredeploy.json#L143).


```none
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
```

Aquí es el aspecto que tiene el grupo de seguridad de la red desde el portal de Azure. Observe que puede ser un GSN asociar con una interfaz subred o de red. En este caso, la GSN está asociado a una subred. En esta configuración, las reglas de entrada se aplican a todas las máquinas virtuales de Windows conectado a la subred.

![Grupo de seguridad de red](./media/virtual-machines-windows-dotnet-core/nsg-win.png)

Para obtener información detallada sobre los grupos de seguridad de red, consulte [¿Qué es un grupo de seguridad de la red]( https://azure.microsoft.com/documentation/articles/virtual-networks-nsg/).

## <a name="next-step"></a>Siguiente paso

<hr>

[Paso 3: disponibilidad y escala en Azure plantillas de administrador de recursos](./virtual-machines-windows-dotnet-core-4-availability-scale.md)
