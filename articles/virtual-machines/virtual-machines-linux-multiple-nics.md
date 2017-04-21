<properties
   pageTitle="Crear una VM Linux con varias NIC | Microsoft Azure"
   description="Obtenga información sobre cómo crear una VM Linux con varias NIC conectadas a través de las plantillas de Azure CLI o administrador de recursos."
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
   ms.workload="infrastructure"
   ms.date="10/27/2016"
   ms.author="iainfou"/>

# <a name="creating-a-linux-vm-with-multiple-nics"></a>Crear una VM Linux con varias NIC
Puede crear una máquina virtual (VM) en Azure que tiene varias interfaces de red virtual (NIC) adjuntas. Un escenario común sería tener diferentes subredes para conectividad front-end y back-end o una red dedicado a una solución de copia de seguridad o supervisión. Este artículo proporciona comandos rápidos para crear una máquina virtual con varias NIC adjuntadas. Para obtener información detallada, incluido cómo crear varias NIC dentro de sus propios scripts fiesta, obtener más información sobre cómo [implementar máquinas virtuales de múltiples NIC](../virtual-network/virtual-network-deploy-multinic-arm-cli.md). Diferentes [tamaños VM](virtual-machines-linux-sizes.md) admite un número distinto de NIC, así que cambiar el tamaño de la máquina virtual según corresponda.

>[AZURE.WARNING] Debe adjuntar varias NIC cuando cree una máquina virtual: no se puede agregar NIC a una máquina virtual existente. Puede [crear una máquina virtual en función de los discos virtuales originales](virtual-machines-linux-copy-vm.md) y crear varias NIC como implementar la máquina virtual.

## <a name="quick-commands"></a>Comandos rápidos
Asegúrese de que dispone de [Azure CLI](../xplat-cli-install.md) inició sesión y usar el modo de administrador de recursos:

```bash
azure config mode arm
```

En los ejemplos siguientes, reemplace los nombres de parámetro de ejemplo con sus propios valores. Nombres de parámetro de ejemplo incluidos `myResourceGroup`, `mystorageaccount`, y `myVM`.

Primero, cree un grupo de recursos. En el ejemplo siguiente se crea un grupo de recursos denominado `myResourceGroup` en la `WestUS` ubicación:

```bash
azure group create myResourceGroup -l WestUS
```

Crear una cuenta de almacenamiento para almacenar sus máquinas virtuales. En el ejemplo siguiente se crea una cuenta de almacenamiento denominada `mystorageaccount`:

```bash
azure storage account create mystorageaccount -g myResourceGroup \
    -l WestUS --kind Storage --sku-name PLRS
```

Crear una red virtual para conectar sus máquinas virtuales a. En el ejemplo siguiente se crea una red virtual denominada `myVnet` con un prefijo de dirección de `192.168.0.0/16`:

```bash
azure network vnet create -g myResourceGroup -l WestUS \
    -n myVnet -a 192.168.0.0/16
```

Crear dos subredes de red virtual: uno para el tráfico front-end y otro para el tráfico de back-end. En el ejemplo siguiente se crea dos subredes, denominados `mySubnetFrontEnd` y `mySubnetBackEnd`:

```bash
azure network vnet subnet create -g myResourceGroup -e myVnet \
    -n mySubnetFrontEnd -a 192.168.1.0/24
azure network vnet subnet create -g myResourceGroup -e myVnet \
    -n mySubnetBackEnd -a 192.168.2.0/24
```

Cree dos NIC, adjuntando una NIC a la subred front-end y una NIC a la subred back-end. En el ejemplo siguiente se crea dos NIC, denominadas `myNic1` y `myNic2`y se unen a su subredes:

```bash
azure network nic create -g myResourceGroup -l WestUS \
    -n myNic1 -m myVnet -k mySubnetFrontEnd
azure network nic create -g myResourceGroup -l WestUS \
    -n myNic2 -m myVnet -k mySubnetBackEnd
```

Por último, cree la máquina virtual, asociar dos NIC que creó anteriormente. En el ejemplo siguiente se crea una máquina virtual denominada `myVM`:

```bash
azure vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --location WestUS \
    --os-type linux \
    --nic-names myNic1,myNic2 \
    --vm-size Standard_DS2_v2 \
    --storage-account-name mystorageaccount \
    --image-urn UbuntuLTS \
    --admin-username ops \
    --ssh-publickey-file ~/.ssh/id_rsa.pub
```

## <a name="creating-multiple-nics-using-azure-cli"></a>Crear varias NIC mediante CLI de Azure
Si previamente ha creado una máquina virtual con la CLI de Azure, los comandos rápidos deben estar familiarizados. El proceso es el mismo para crear una NIC o varias NIC. Obtenga más información sobre cómo [implementar varias NIC con la CLI de Azure](../virtual-network/virtual-network-deploy-multinic-arm-cli.md), incluido el proceso de recorrer para crear todas las NIC de secuencias de comandos.

En el ejemplo siguiente se crea dos NIC, denominadas `myNic1` y `myNic2`, con una NIC conectar a cada subred:

```bash
azure network nic create --resource-group myResourceGroup --location WestUS \
    -n myNic1 --subnet-vnet-name myVnet --subnet-name mySubnetFrontEnd
azure network nic create --resource-group myResourceGroup --location WestUS \
    -n myNic2 --subnet-vnet-name myVnet --subnet-name mySubnetBackEnd
```

Normalmente se crea un [Grupo de seguridad de la red](../virtual-network/virtual-networks-nsg.md) o [equilibrador de carga](../load-balancer/load-balancer-overview.md) para ayudar a administrar y distribuir el tráfico entre sus máquinas virtuales. De nuevo, los comandos son los mismos cuando se trabaja con varias NIC. En el ejemplo siguiente se crea un grupo de seguridad de red denominado `myNetworkSecurityGroup`:

```bash
azure network nsg create --resource-group myResourceGroup --location WestUS \
    --name myNetworkSecurityGroup
```

Enlazar las NIC al grupo de seguridad de red mediante `azure network nic set`. En el ejemplo siguiente se enlaza `myNic1` y `myNic2` con `myNetworkSecurityGroup`:

```bashazure 
azure network nic set --resource-group myResourceGroup --name myNic1 \
    --network-security-group-name myNetworkSecurityGroup
azure network nic set --resource-group myResourceGroup --name myNic2 \
    --network-security-group-name myNetworkSecurityGroup
```

Al crear la máquina virtual, ahora especificar varias NIC. Prefiere usar `--nic-name` para proporcionar una sola NIC, en su lugar de usar `--nic-names` y proporcione una lista de valores separados por comas de NIC. También debe tener cuidado al seleccionar el tamaño de la máquina virtual. Hay límites para el número total de NIC que puede agregar a una máquina virtual. Obtenga más información acerca de los [tamaños de máquina virtual Linux](virtual-machines-linux-sizes.md). En el ejemplo siguiente se muestra cómo especificar varias NIC y, a continuación, un tamaño de memoria virtual que admite el uso de varias NIC (`Standard_DS2_v2`):

```bash
azure vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --location WestUS \
    --os-type linux \
    --nic-names myNic1,myNic2 \
    --vm-size Standard_DS2_v2 \
    --storage-account-name mystorageaccount \
    --image-urn UbuntuLTS \
    --admin-username ops \
    --ssh-publickey-file ~/.ssh/id_rsa.pub
```

## <a name="creating-multiple-nics-using-resource-manager-templates"></a>Crear varias NIC con plantillas de administrador de recursos
Plantillas de administrador de recursos de Azure use descriptiva archivos JSON para definir su entorno. Obtenga una [Visión general del Administrador de recursos de Azure](../azure-resource-manager/resource-group-overview.md). Plantillas de administrador de recursos proporcionan una manera de crear varias instancias de un recurso durante la implementación, como la creación de varias NIC. Utilice *Copiar* para especificar el número de instancias para crear:

```bash
"copy": {
    "name": "multiplenics"
    "count": "[parameters('count')]"
}
```

Obtenga más información sobre cómo [crear varias instancias con la función *Copiar*](../resource-group-create-multiple.md). 

También puede usar un `copyIndex()` , a continuación, anexar un número a un nombre de recurso, que le permite crear `myNic1`, `myNic2`, etcetera. A continuación muestra un ejemplo de anexar el valor de índice:

```bash
"name": "[concat('myNic', copyIndex())]", 
```

Puede obtener un ejemplo completo de [creación de varias NIC con plantillas de administrador de recursos](../virtual-network/virtual-network-deploy-multinic-arm-template.md).

## <a name="next-steps"></a>Pasos siguientes
Asegúrese de revisar los [tamaños de Linux VM](virtual-machines-linux-sizes.md) cuando intenta crear una máquina virtual con varias NIC. Preste atención a la cantidad máxima de NIC es compatible con el tamaño de cada máquina virtual. 

Recuerde que no puede agregar NIC adicionales a una máquina virtual existente, debe crear todas las NIC cuando se implementa la máquina virtual. Tenga cuidado al planear las implementaciones para asegurarse de que tiene todos los la conectividad de red necesarios desde el principio.