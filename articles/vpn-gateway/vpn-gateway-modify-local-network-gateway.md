<properties
   pageTitle="Modificar prefijos de direcciones IP de red local puerta de enlace y la puerta de enlace IP | Microsoft Azure"
   description="En este artículo le guiará por cambiar prefijos de direcciones IP de la puerta de enlace de red local"
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"/>

<tags
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="08/08/2016"
   ms.author="cherylmc"/>

# <a name="modify-local-network-gateway-settings-using-powershell"></a>Modificar la configuración de puerta de enlace de red local con PowerShell

En ocasiones, cambia la configuración de la puerta de enlace de su red local AddressPrefix o GatewayIPAddress. Las siguientes instrucciones le ayudará a modificar la configuración de puerta de enlace de red local. También puede modificar esta configuración en el portal de Azure.

## <a name="before-you-begin"></a>Antes de empezar
    
Debe instalar la última versión de los cmdlets de PowerShell de administrador de recursos de Azure. Para obtener más información sobre la instalación de los cmdlets de PowerShell, vea [cómo instalar y configurar Azure PowerShell](../powershell-install-configure.md) .

## <a name="to-modify-ip-address-prefixes"></a>Para modificar prefijos de direcciones IP

[AZURE.INCLUDE [vpn-gateway-modify-ip-prefix-rm](../../includes/vpn-gateway-modify-ip-prefix-rm-include.md)]

## <a name="to-modify-the-gateway-ip-address"></a>Para modificar la dirección IP de puerta de enlace

[AZURE.INCLUDE [vpn-gateway-modify-lng-gateway-ip-rm](../../includes/vpn-gateway-modify-lng-gateway-ip-rm-include.md)]

## <a name="next-steps"></a>Pasos siguientes

Puede comprobar la conexión de la puerta de enlace. Consulte [comprobar una conexión de puerta de enlace](vpn-gateway-verify-connection-resource-manager.md).

