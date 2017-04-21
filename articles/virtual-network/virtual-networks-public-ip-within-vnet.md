<properties 
   pageTitle="Cómo utilizar las direcciones IP públicas en una red virtual"
   description="Obtenga información sobre cómo configurar una red virtual para usar las direcciones IP públicas"
   services="virtual-network"
   documentationCenter="na"
   authors="jimdial"
   manager="carmonm"
   editor="tysonn" />
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="04/27/2016"
   ms.author="jdial" />

# <a name="public-ip-address-space-in-a-virtual-network-vnet"></a>Espacio de direcciones IP públicas en una red Virtual (VNet)

Redes virtuales (VNets) pueden contener espacios de direcciones IP (bloques de direcciones RFC 1918) públicos y privados. Cuando agrega un intervalo de direcciones IP público, se tratará como parte del espacio de direcciones IP VNet privado que sólo es accesible dentro de la VNet, VNets interconectados y desde la ubicación local.

La imagen siguiente muestra una VNet que incluye espacios de direcciones IP públicos y privados.

![IP pública Conceptual](./media/virtual-networks-public-ip-within-vnet/IC775683.jpg)

## <a name="how-do-i-add-a-public-ip-address-range"></a>¿Cómo puedo agregar un intervalo de direcciones IP público?

Agregar un intervalo de direcciones IP público de la misma manera que puede agregar un intervalo de direcciones IP privadas; mediante un archivo de *netcfg* o agregando la configuración en el [portal de Azure](http://portal.azure.com). Puede agregar un intervalo de direcciones IP público cuando crea su VNet, o puede volver atrás y agregarla más adelante. El ejemplo siguiente muestra públicos y privados espacios de direcciones IP configurados en el mismo VNet.

![Dirección IP pública de Portal](./media/virtual-networks-public-ip-within-vnet/IC775684.png)

## <a name="are-there-any-limitations"></a>¿Existe alguna limitación?

Hay algunos intervalos de direcciones IP que no se permiten:

- 224.0.0.0/4 (multidifusión)

- 255.255.255.255/32 (difusión)

- 127.0.0.0/8 (bucle)

- 169.254.0.0/16 (vínculo local)

- 168.63.129.16/32 (DNS interno)

## <a name="next-steps"></a>Pasos siguientes

[Cómo administrar servidores DNS utilizados por un VNet](virtual-networks-manage-dns-in-vnet.md)