<properties
   pageTitle="Agregar una puerta de enlace de VNet a una red virtual para ExpressRoute con el Administrador de recursos y PowerShell | Microsoft Azure"
   description="En este artículo le guiará a través de la adición de una puerta de enlace Vnet un VNet Administrador de recursos ya se ha creado para ExpressRoute"
   documentationCenter="na"
   services="expressroute"
   authors="charwen"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"/>

<tags 
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="article" 
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services" 
   ms.date="10/10/2016"
   ms.author="charwen"/>

# <a name="configure-a-virtual-network-gateway-for-expressroute-using-resource-manager-and-powershell"></a>Configurar una puerta de enlace de red virtual para ExpressRoute con el Administrador de recursos y PowerShell


> [AZURE.SELECTOR]
- [PowerShell - Administrador de recursos](expressroute-howto-add-gateway-resource-manager.md)
- [PowerShell - clásica](expressroute-howto-add-gateway-classic.md)


En este artículo le guiará por los pasos para agregar, cambiar el tamaño y quitar una puerta de enlace de red virtual (VNet) para un VNet existente. Los pasos para esta configuración se específicamente para VNets que se crearon usando el **modelo de implementación de administrador de recursos** y que se pueden usar en una configuración de ExpressRoute. 

**Acerca de los modelos de implementación de Azure**

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)] 

## <a name="before-beginning"></a>Antes de comenzar

Compruebe que ha instalado los cmdlets de PowerShell de Azure necesarios para esta configuración (1.0.2 o posterior). Si no ha instalado los cmdlets, debe hacerlo antes de comenzar con los pasos de configuración. Para obtener más información sobre la instalación de Azure PowerShell, vea [cómo instalar y configurar Azure PowerShell](../powershell-install-configure.md).


[AZURE.INCLUDE [expressroute-gateway-rm-ps](../../includes/expressroute-gateway-rm-ps-include.md)]

    
## <a name="next-steps"></a>Pasos siguientes

Después de haber creado la puerta de enlace VNet, puede vincular la VNet a un circuito de ExpressRoute. Vea [una red Virtual a un circuito ExpressRoute vínculo](expressroute-howto-linkvnet-arm.md).
