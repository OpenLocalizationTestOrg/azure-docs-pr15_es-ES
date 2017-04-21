<properties
   pageTitle="Abra los puertos a una máquina virtual con el portal de Azure | Microsoft Azure"
   description="Obtenga información sobre cómo abrir un puerto o crear un extremo de la máquina virtual de Windows mediante el modelo de implementación de administrador de recursos en el Portal de Azure"
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

# <a name="opening-ports-to-a-vm-in-azure-using-the-azure-portal"></a>Apertura de puertos a una máquina virtual en Azure con el portal de Azure
[AZURE.INCLUDE [virtual-machines-common-nsg-quickstart](../../includes/virtual-machines-common-nsg-quickstart.md)]

## <a name="quick-commands"></a>Comandos rápidos
También puede [realizar estos pasos con PowerShell de Azure](virtual-machines-windows-nsg-quickstart-powershell.md).

Primero, cree el grupo de seguridad de la red. Seleccione un grupo de recursos en el portal, haga clic en **Agregar**, a continuación, busque y seleccione la red del grupo de seguridad:

![Agregar un grupo de seguridad de red](./media/virtual-machines-windows-nsg-quickstart-portal/add-nsg.png)

Escriba un nombre para el grupo de seguridad de red, seleccione o cree un grupo de recursos y seleccione una ubicación. Haga clic en **crear** cuando haya terminado:

![Crear un grupo de seguridad de red](./media/virtual-machines-windows-nsg-quickstart-portal/create-nsg.png)

Seleccione el nuevo grupo de seguridad de red. Seleccione 'reglas de seguridad de entrada' y luego haga clic en el botón **Agregar** para crear una regla:

![Agregar una regla de entrada](./media/virtual-machines-windows-nsg-quickstart-portal/add-inbound-rule.png)

Proporcione un nombre para la nueva regla. Puerto 80 ya se escribe de forma predeterminada. Este módulo es donde desea cambiar el origen, el protocolo y el destino al agregar reglas adicionales a su grupo de seguridad de la red. Haga clic en **Aceptar** para crear la regla:

![Crear una regla de entrada](./media/virtual-machines-windows-nsg-quickstart-portal/create-inbound-rule.png)

El último paso es asociar una subred o una interfaz de red específica en el grupo de seguridad de la red. Vamos a asociar el grupo de seguridad de la red con una subred. Seleccione 'Subredes' y luego haga clic en **asociar**:

![Asociar una subred a un grupo de seguridad de red](./media/virtual-machines-windows-nsg-quickstart-portal/associate-subnet.png)

Seleccione su red virtual y, a continuación, seleccione la subred apropiada:

![Asociar un grupo de seguridad de la red con una red virtual](./media/virtual-machines-windows-nsg-quickstart-portal/select-vnet-subnet.png)

Ahora que ha creado un grupo de seguridad de red, creado una regla entrante que permite el tráfico en el puerto 80 y asociadas a una subred. Cualquier VM que conectarse a esa subred son accesibles en el puerto 80.


## <a name="more-information-on-network-security-groups"></a>Obtener más información sobre los grupos de seguridad de red
Los comandos rápidos aquí le permiten ponerse en marcha con tráfico que fluye a su máquina virtual. Grupos de seguridad de red proporcionan muchas características y detalle para controlar el acceso a los recursos. Puede obtener más información sobre la [creación de un grupo de seguridad de la red y las reglas de ACL aquí](../virtual-network/virtual-networks-create-nsg-arm-ps.md).

Puede definir grupos de seguridad de la red y las reglas de ACL como parte de las plantillas de administrador de recursos de Azure. Obtenga más información sobre la [creación de grupos de seguridad de red con plantillas](../virtual-network/virtual-networks-create-nsg-arm-template.md).

Si necesita usar reenvío de puerto para asignar un único puerto externo a un puerto interno en la máquina virtual, use un equilibrador de carga y las reglas de traducción de direcciones de red (NAT). Por ejemplo, que desea exponer el puerto TCP 8080 externamente y tiene el tráfico dirigido al puerto TCP 80 en una máquina virtual. Puede obtener información sobre cómo [crear un equilibrador de carga a través de Internet](../load-balancer/load-balancer-get-started-internet-arm-ps.md).

## <a name="next-steps"></a>Pasos siguientes
En este ejemplo, ha creado una regla simple para permitir el tráfico HTTP. Puede encontrar información sobre cómo crear entornos más detallados en los siguientes artículos:

- [Introducción al administrador de recursos Azure](../azure-resource-manager/resource-group-overview.md)
- [¿Qué es un grupo de seguridad de la red (GSN)?](../virtual-network/virtual-networks-nsg.md)
- [Información general del Administrador de recursos de Azure para equilibradores de carga](../load-balancer/load-balancer-arm.md)
