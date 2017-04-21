<properties
   pageTitle="Abra los puertos a una máquina virtual con PowerShell | Microsoft Azure"
   description="Obtenga información sobre cómo abrir un puerto o crear un extremo de la máquina virtual de Windows con el modo de implementación de administrador de recursos de Azure y Azure PowerShell"
   services="virtual-machines-windows"
   documentationCenter=""
   authors="iainfoulds"
   manager="timlt"
   editor=""/>

<tags
   ms.service="virtual-machines-windows"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-windows"
   ms.workload="infrastructure-services"
   ms.date="10/27/2016"
   ms.author="iainfou"/>

# <a name="opening-ports-and-endpoints-to-a-vm-in-azure-using-powershell"></a>Apertura de puertos y extremos de una máquina virtual en Azure con PowerShell
[AZURE.INCLUDE [virtual-machines-common-nsg-quickstart](../../includes/virtual-machines-common-nsg-quickstart.md)]

## <a name="quick-commands"></a>Comandos rápidos
Para crear un grupo de seguridad de la red y ACL reglas necesita [la última versión de Azure PowerShell instalado](../powershell-install-configure.md). También puede [realizar estos pasos con el portal de Azure](virtual-machines-windows-nsg-quickstart-portal.md).

Inicie sesión su cuenta de Azure:

```powershell
Login-AzureRmAccount
```

En los ejemplos siguientes, reemplace los nombres de parámetro de ejemplo con sus propios valores. Nombres de parámetro de ejemplo incluidos `myResourceGroup`, `myNetworkSecurityGroup`, y `myVnet`.

Crear una regla. En el ejemplo siguiente se crea una regla denominada `myNetworkSecurityGroupRule` para permitir el tráfico TCP en el puerto 80:

```powershell
$httprule = New-AzureRmNetworkSecurityRuleConfig -Name "myNetworkSecurityGroupRule" `
    -Description "Allow HTTP" -Access "Allow" -Protocol "Tcp" -Direction "Inbound" `
    -Priority "100" -SourceAddressPrefix "Internet" -SourcePortRange * `
    -DestinationAddressPrefix * -DestinationPortRange 80
```

A continuación, crear el grupo de seguridad de la red y asignar la regla HTTP que acaba de crear como sigue. En el ejemplo siguiente se crea un grupo de seguridad de red denominado `myNetworkSecurityGroup`:

```powershell
$nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName "myResourceGroup" `
    -Location "WestUS" -Name "myNetworkSecurityGroup" -SecurityRules $httprule
```

Ahora vamos a asignar el grupo de seguridad de la red a una subred. En el ejemplo siguiente se asigna una red virtual existente denominada `myVnet` a la variable `$vnet`:

```powershell
$vnet = Get-AzureRmVirtualNetwork -ResourceGroupName "myResourceGroup" `
    -Name "myVnet"
```

Asociar el grupo de seguridad de la red a la subred. En el ejemplo siguiente se asocia la subred denominada `mySubnet` con su grupo de seguridad de red:

```powershell
$subnetPrefix = $vnet.Subnets|?{$_.Name -eq 'mySubnet'}

Set-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $vnet -Name "mySubnet" `
    -AddressPrefix $subnetPrefix.AddressPrefix `
    -NetworkSecurityGroup $nsg
```

Por último, actualice su red virtual en orden para que los cambios surtan efecto:

```powershell
Set-AzureRmVirtualNetwork -VirtualNetwork $vnet
```


## <a name="more-information-on-network-security-groups"></a>Obtener más información sobre los grupos de seguridad de red
Los comandos rápidos aquí le permiten ponerse en marcha con tráfico que fluye a su máquina virtual. Grupos de seguridad de red proporcionan muchas características y detalle para controlar el acceso a los recursos. Puede obtener más información sobre la [creación de un grupo de seguridad de la red y las reglas de ACL aquí](../virtual-network/virtual-networks-create-nsg-arm-ps.md).

Puede definir grupos de seguridad de la red y las reglas de ACL como parte de las plantillas de administrador de recursos de Azure. Obtenga más información sobre la [creación de grupos de seguridad de red con plantillas](../virtual-network/virtual-networks-create-nsg-arm-template.md).

Si necesita usar reenvío de puerto para asignar un único puerto externo a un puerto interno en la máquina virtual, use un equilibrador de carga y las reglas de traducción de direcciones de red (NAT). Por ejemplo, que desea exponer el puerto TCP 8080 externamente y tiene el tráfico dirigido al puerto TCP 80 en una máquina virtual. Puede obtener información sobre cómo [crear un equilibrador de carga a través de Internet](../load-balancer/load-balancer-get-started-internet-arm-ps.md).

## <a name="next-steps"></a>Pasos siguientes
En este ejemplo, ha creado una regla simple para permitir el tráfico HTTP. Puede encontrar información sobre cómo crear entornos más detallados en los siguientes artículos:

- [Introducción al administrador de recursos Azure](../azure-resource-manager/resource-group-overview.md)
- [¿Qué es un grupo de seguridad de la red (GSN)?](../virtual-network/virtual-networks-nsg.md)
- [Información general del Administrador de recursos de Azure para equilibradores de carga](../load-balancer/load-balancer-arm.md)