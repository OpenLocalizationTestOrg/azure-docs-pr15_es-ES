<properties
   pageTitle="Crear una red virtual con una conexión VPN de sitio a sitio mediante el Administrador de recursos de Azure y el Portal de Azure | Microsoft Azure"
   description="Cómo crear VNet utiliza el modelo de implementación de administrador de recursos y conectarse a su local local red mediante una conexión VPN de S2S de la puerta de enlace."
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"/>

<tags
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/14/2016"
   ms.author="cherylmc"/>

# <a name="create-a-vnet-with-a-site-to-site-connection-using-the-azure-portal"></a>Crear una VNet con una conexión de sitio a sitio con el portal de Azure

> [AZURE.SELECTOR]
- [Administrador de recursos - Portal de Azure](vpn-gateway-howto-site-to-site-resource-manager-portal.md)
- [Administrador de recursos - PowerShell](vpn-gateway-create-site-to-site-rm-powershell.md)
- [Clásico: Portal clásica](vpn-gateway-site-to-site-create.md)


En este artículo le guiará a través de creación de una red virtual y una conexión de puerta de enlace VPN de sitio a sitio a su red local mediante el modelo de implementación de administrador de recursos de Azure y el portal de Azure. Conexiones de sitio a sitio pueden usarse para entre local y híbrido configuraciones.

![Diagrama](./media/vpn-gateway-howto-site-to-site-resource-manager-portal/s2srmportal.png)


### <a name="deployment-models-and-methods-for-site-to-site-connections"></a>Modelos de implementación y métodos para las conexiones de sitio a otro

[AZURE.INCLUDE [deployment models](../../includes/vpn-gateway-deployment-models-include.md)] 

La siguiente tabla muestra los métodos para las configuraciones de sitio a sitio y modelos de implementación disponible actualmente. Cuando esté disponible un artículo con pasos de configuración, nos vincularlo directamente desde esta tabla.

[AZURE.INCLUDE [site-to-site table](../../includes/vpn-gateway-table-site-to-site-include.md)]

#### <a name="additional-configurations"></a>Configuraciones adicionales 

Si desea conectar VNets, pero no va a crear una conexión a una ubicación local, vea [Configurar una conexión de VNet a VNet](vpn-gateway-vnet-vnet-rm-ps.md). Si desea agregar una conexión de sitio a sitio a una VNet que ya tiene una conexión, vea [Agregar una conexión de S2S a una VNet con una conexión de puerta de enlace VPN existente](vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md).

## <a name="before-you-begin"></a>Antes de empezar

Compruebe que tiene los siguientes elementos antes de comenzar la configuración:

- Un dispositivo VPN compatible y alguien configurarlo. Consulte [acerca de los dispositivos VPN](vpn-gateway-about-vpn-devices.md). Si no está familiarizado con la configuración de su dispositivo VPN o no están familiarizados con la dirección IP rangos que se encuentran en su local en la configuración de red, necesita coordinar con alguien que puede proporcionar los detalles por usted.

- Una dirección IP pública orientada externamente para su dispositivo VPN. Esta dirección IP no se encuentra detrás de un NAT.
    
- Una suscripción de Azure. Si todavía no tiene una suscripción de Azure, puede activar los [beneficios de suscriptor MSDN](http://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) o signo hacia arriba para obtener una [cuenta gratuita](http://azure.microsoft.com/pricing/free-trial/).

### <a name="values"></a>Valores de configuración de ejemplo para este ejercicio


Cuando usa estos pasos como ejercicio, puede usar los valores de configuración de ejemplo:

- **VNet nombre:** TestVNet1
- **Espacio de direcciones:** 10.11.0.0/16 y 10.12.0.0/16
- **Subredes:**
    - Front-end: 10.11.0.0/24
    - Back-end: 10.12.0.0/24
    - GatewaySubnet: 10.12.255.0/27
- **Grupo de recursos:** TestRG1
- **Ubicación:** Estados Unidos oriental
- **Servidor DNS:** 8.8.8.8
- **Nombre de puerta de enlace:** VNet1GW
- **IP pública:** VNet1GWIP
- **Tipo de VPN:** Basado en la ruta
- **Tipo de conexión:** Sitio a sitio (IPsec)
- **Tipo de puerta de enlace:** VPN
- **Nombre de puerta de enlace de red local:** Sitio2
- **Nombre de conexión:** VNet1toSite2


## <a name="CreatVNet"></a>1. crear una red virtual 

Si ya tiene un VNet, compruebe que la configuración es compatible con el diseño de la puerta de enlace VPN. Preste atención particular a ninguna subred que pueden superponerse con otras redes. Si tiene superpuestos subredes, la conexión no funcionará correctamente. Si su VNet está configurado con la configuración correcta, puede empezar a los pasos de la sección [especificar un servidor DNS](#dns) .

### <a name="to-create-a-virtual-network"></a>Para crear una red virtual

[AZURE.INCLUDE [vpn-gateway-basic-vnet-rm-portal](../../includes/vpn-gateway-basic-vnet-rm-portal-include.md)]  

## <a name="subnets"></a>2. agregar subredes y el espacio de direcciones adicionales

Puede agregar espacio adicional de direcciones y subredes a su VNet una vez que se ha creado.

[AZURE.INCLUDE [vpn-gateway-additional-address-space](../../includes/vpn-gateway-additional-address-space-include.md)] 

## <a name="dns"></a>3. Especifique un servidor DNS

### <a name="to-specify-a-dns-server"></a>Para especificar un servidor DNS

[AZURE.INCLUDE [vpn-gateway-add-dns-rm-portal](../../includes/vpn-gateway-add-dns-rm-portal-include.md)]

## <a name="gatewaysubnet"></a>4. crear una subred de puerta de enlace

Antes de conectar su red virtual a una puerta de enlace, debe crear la subred de puerta de enlace de la red virtual al que desea conectarse. Si es posible, es mejor crear una subred de puerta de enlace con un bloque CIDR de /28 o /27 para proporcionar suficientes direcciones IP para adaptarse a requisitos adicionales configuración futura.

Si va a crear esta configuración como ejercicio, consulte estos [valores](#values) al crear la subred de puerta de enlace.

### <a name="to-create-a-gateway-subnet"></a>Para crear una subred de puerta de enlace


[AZURE.INCLUDE [vpn-gateway-add-gwsubnet-rm-portal](../../includes/vpn-gateway-add-gwsubnet-rm-portal-include.md)]

## <a name="VNetGateway"></a>5. crear una puerta de enlace de red virtual

Si va a crear esta configuración como ejercicio, puede hacer referencia a los [valores de configuración de ejemplo](#values).

### <a name="to-create-a-virtual-network-gateway"></a>Para crear una puerta de enlace de red virtual

[AZURE.INCLUDE [vpn-gateway-add-gw-rm-portal](../../includes/vpn-gateway-add-gw-rm-portal-include.md)]

## <a name="LocalNetworkGateway"></a>6. crear una puerta de enlace de red local

La 'puerta de enlace de red local' hace referencia a su ubicación local. Asigne un nombre por el que puede hacer referencia a él Azure a la puerta de enlace de red local. 

Si va a crear esta configuración como ejercicio, puede hacer referencia a los [valores de configuración de ejemplo](#values).

### <a name="to-create-a-local-network-gateway"></a>Para crear una puerta de enlace de red local

[AZURE.INCLUDE [vpn-gateway-add-lng-rm-portal](../../includes/vpn-gateway-add-lng-rm-portal-include.md)]

## <a name="VPNDevice"></a>7. configurar el dispositivo VPN

[AZURE.INCLUDE [vpn-gateway-configure-vpn-device-rm](../../includes/vpn-gateway-configure-vpn-device-rm-include.md)]

## <a name="CreateConnection"></a>8. crear una conexión VPN de sitio a otro

Crear la conexión VPN de sitio a sitio entre la puerta de enlace de red virtual y el dispositivo VPN. Asegúrese de reemplazar los valores con su propio. La clave compartida debe coincidir con el valor que se utiliza para la configuración del dispositivo VPN. 

Antes de comenzar esta sección, compruebe que la puerta de enlace de red virtual y puertas de enlace de red local han terminado de crear. Si va a crear esta configuración como ejercicio, consulte estos [valores](#values) al crear la conexión.

### <a name="to-create-the-vpn-connection"></a>Para crear la conexión VPN


[AZURE.INCLUDE [vpn-gateway-add-site-to-site-connection-rm-portal](../../includes/vpn-gateway-add-site-to-site-connection-rm-portal-include.md)]

## <a name="VerifyConnection"></a>9. Compruebe la conexión VPN

Puede comprobar la conexión VPN en el portal de o con PowerShell.

[AZURE.INCLUDE [vpn-gateway-verify-connection-rm](../../includes/vpn-gateway-verify-connection-rm-include.md)]

## <a name="next-steps"></a>Pasos siguientes

- Una vez finalizada la conexión, puede agregar máquinas virtuales con sus redes virtuales. Consulte la máquinas virtuales de [la ruta de aprendizaje](https://azure.microsoft.com/documentation/learning-paths/virtual-machines) para obtener más información.

- Para obtener información acerca de BGP, vea la [Información general de BGP](vpn-gateway-bgp-overview.md) y [cómo configurar BGP](vpn-gateway-bgp-resource-manager-ps.md).
