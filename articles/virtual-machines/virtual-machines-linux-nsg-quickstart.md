<properties
   pageTitle="Abra los puertos y protocolos de extremos de una VM Linux | Microsoft Azure"
   description="Obtenga información sobre cómo abrir un puerto o crear un extremo de la VM Linux mediante el modelo de implementación de administrador de recursos de Azure y Azure CLI"
   services="virtual-machines-linux"
   documentationCenter=""
   authors="iainfoulds"
   manager="timlt"
   editor=""/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure-services"
   ms.date="10/27/2016"
   ms.author="iainfou"/>

# <a name="opening-ports-and-endpoints-to-a-linux-vm-in-azure"></a>Apertura de puertos y extremos a una VM Linux en Azure
Abrir un puerto o crear un extremo, para una máquina virtual (VM) en Azure mediante la creación de un filtro de red en una subred o la interfaz de red de la máquina virtual. Coloque estos filtros, que controlan el tráfico entrante y saliente, en un grupo de seguridad de red adjuntado al recurso que recibe el tráfico. Vamos a usar un ejemplo común de tráfico web en el puerto 80.

## <a name="quick-commands"></a>Comandos rápidos
Para crear un grupo de seguridad de la red y las reglas que necesita [CLI Azure](../xplat-cli-install.md) instalado y usar el modo de administrador de recursos:

```bash
azure config mode arm
```

En los ejemplos siguientes, reemplace los nombres de parámetro de ejemplo con sus propios valores. Nombres de parámetro de ejemplo incluidos `myResourceGroup`, `myNetworkSecurityGroup`, y `myVnet`.

Crear el grupo de seguridad de red, escribir sus propios nombres y ubicación correctamente. En el ejemplo siguiente se crea un grupo de seguridad de red denominado `myNetworkSecurityGroup` en la `WestUS` ubicación:

```
azure network nsg create --resource-group myResourceGroup --location westus \
    --name myNetworkSecurityGroup
```

Agregar una regla para permitir el tráfico a su servidor Web HTTP (o ajustar para su propio escenario, como la conectividad de base de datos o acceso SSH). En el ejemplo siguiente se crea una regla denominada `myNetworkSecurityGroupRule` para permitir el tráfico TCP en el puerto 80:

```
azure network nsg rule create --resource-group myResourceGroup \
    --nsg-name myNetworkSecurityGroup --name myNetworkSecurityGroupRule \
    --protocol tcp --direction inbound --priority 1000 \
    --destination-port-range 80 --access allow
```

Asociar el grupo de seguridad de la red con la interfaz de red de la VM (NIC). En el ejemplo siguiente se asocia una NIC existente denominada `myNic` con el grupo de seguridad de red denominado `myNetworkSecurityGroup`:

```
azure network nic set --resource-group myResourceGroup \
    --network-security-group-name myNetworkSecurityGroup --name myNic
```

Como alternativa, puede asociar el grupo de seguridad de la red con una subred de una red virtual en lugar de en la interfaz de red en una única VM. En el ejemplo siguiente se asocia una subred existente denominada `mySubnet` en la `myVnet` red virtual con el grupo de seguridad de red denominado `myNetworkSecurityGroup`:

```
azure network vnet subnet set --resource-group myResourceGroup \
    --network-security-group-name myNetworkSecurityGroup \
    --vnet-name myVnet --name mySubnet
```

## <a name="more-information-on-network-security-groups"></a>Obtener más información sobre los grupos de seguridad de red
Los comandos rápidos aquí le permiten ponerse en marcha con tráfico que fluye a su máquina virtual. Grupos de seguridad de red proporcionan muchas características y detalle para controlar el acceso a los recursos. Puede obtener más información sobre la [creación de un grupo de seguridad de la red y las reglas de ACL aquí](../virtual-network/virtual-networks-create-nsg-arm-cli.md).

Puede definir grupos de seguridad de la red y las reglas de ACL como parte de las plantillas de administrador de recursos de Azure. Obtenga más información sobre la [creación de grupos de seguridad de red con plantillas](../virtual-network/virtual-networks-create-nsg-arm-template.md).

Si necesita usar reenvío de puerto para asignar un único puerto externo a un puerto interno en la máquina virtual, use un equilibrador de carga y las reglas de traducción de direcciones de red (NAT). Por ejemplo, que desea exponer el puerto TCP 8080 externamente y tiene el tráfico dirigido al puerto TCP 80 en una máquina virtual. Puede obtener información sobre cómo [crear un equilibrador de carga a través de Internet](../load-balancer/load-balancer-get-started-internet-arm-cli.md).

## <a name="next-steps"></a>Pasos siguientes
En este ejemplo, ha creado una regla simple para permitir el tráfico HTTP. Puede encontrar información sobre cómo crear entornos más detallados en los siguientes artículos:

- [Introducción al administrador de recursos Azure](../azure-resource-manager/resource-group-overview.md)
- [¿Qué es un grupo de seguridad de la red (GSN)?](../virtual-network/virtual-networks-nsg.md)
- [Información general del Administrador de recursos de Azure para equilibradores de carga](../load-balancer2    /load-balancer-arm.md)