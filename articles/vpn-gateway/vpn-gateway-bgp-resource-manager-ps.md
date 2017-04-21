<properties
   pageTitle="Cómo configurar BGP en Azure de puertas de enlace VPN con el Administrador de recursos de Azure y PowerShell | Microsoft Azure"
   description="En este artículo se explica cómo configurar BGP con puertas de enlace de Azure VPN con el Administrador de recursos de Azure y PowerShell."
   services="vpn-gateway"
   documentationCenter="na"
   authors="yushwang"
   manager="rossort"
   editor=""
   tags="azure-resource-manager"/>

<tags
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="04/15/2016"
   ms.author="yushwang"/>

# <a name="how-to-configure-bgp-on-azure-vpn-gateways-using-azure-resource-manager-and-powershell"></a>Cómo configurar BGP en Azure de puertas de enlace VPN con el Administrador de recursos de Azure y PowerShell

En este artículo le guiará por los pasos para habilitar BGP en una conexión de VPN de sitio a sitio (S2S) entre local y un VNet a VNet utilizando el modelo de implementación de administrador de recursos y PowerShell.


**Acerca de los modelos de implementación de Azure**

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)] 

## <a name="about-bgp"></a>Acerca de BGP

BGP es el protocolo de enrutamiento estándar que se usan normalmente en Internet para intercambiar información de enrutamiento y alcance entre dos o más redes. BGP permite las puertas de enlace de VPN de Azure y los dispositivos VPN local llamados BGP colegas o vecinos intercambiar "enruta" que le informará de dos puertas de enlace en la disponibilidad y la accesibilidad de dichos prefijos ir a través de las puertas de enlace o enrutadores implicados. BGP también puede habilitar el enrutamiento de tránsito entre varias redes propagarse rutas que una puerta de enlace BGP aprende desde un punto BGP a todos los demás BGP.

Vea [Información general de BGP con puertas de enlace de Azure VPN](./vpn-gateway-bgp-overview.md) para obtener más información sobre los beneficios de BGP y entender los requisitos técnicos y consideraciones acerca del uso de BGP.

## <a name="getting-started-with-bgp-on-azure-vpn-gateways"></a>Introducción a BGP en puertas de enlace VPN de Azure

En este artículo le guiará por los pasos para realizar las siguientes tareas:

- [Parte 1: habilitar BGP de la puerta de enlace VPN de Azure](#enablebgp)

- [Parte 2: establecer una conexión entre local con BGP](#crossprembgp)

- [Parte 3: establecer una conexión de VNet a VNet con BGP](#v2vbgp)

Cada parte de las instrucciones de formularios de un bloque de creación básico para habilitar BGP en la conectividad de red. Si completar las tres partes, va a crear la topología tal como se muestra en el diagrama siguiente:

![Topología BGP](./media/vpn-gateway-bgp-resource-manager-ps/bgp-crosspremv2v.png)

Puede combinar estas entre sí para crear una red de tránsito más complejos, múltiples esperanza, que se ajusten a sus necesidades.

## <a name ="enablebgp"></a>Parte 1: configurar BGP en la puerta de enlace VPN de Azure

Los siguientes pasos de configuración configurará los parámetros BGP de la puerta de enlace VPN de Azure tal como se muestra en el diagrama siguiente:

![Puerta de enlace BGP](./media/vpn-gateway-bgp-resource-manager-ps/bgp-gateway.png)

### <a name="before-you-begin"></a>Antes de empezar

- Compruebe que tiene una suscripción de Azure. Si todavía no tiene una suscripción de Azure, puede activar los [beneficios de suscriptor MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) o signo hacia arriba para obtener una [cuenta gratuita](https://azure.microsoft.com/pricing/free-trial/).
    
- Debe instalar los cmdlets de PowerShell de administrador de recursos de Azure. Para obtener más información sobre la instalación de los cmdlets de PowerShell, vea [cómo instalar y configurar Azure PowerShell](../powershell-install-configure.md) .

### <a name="step-1---create-and-configure-vnet1"></a>Paso 1: crear y configurar VNet1 

#### <a name="1-declare-your-variables"></a>1. declarar las variables

Para este ejercicio, comenzaremos por declarar las variables. El ejemplo siguiente declara las variables con los valores para este ejercicio. Asegúrese de reemplazar los valores con su propio al configurar para producción. Puede utilizar estas variables si se están ejecutando los pasos para familiarizarse con este tipo de configuración. Modifique las variables y, a continuación, copiar y pegar en la consola de PowerShell.

    $Sub1          = "Replace_With_Your_Subcription_Name"
    $RG1           = "TestBGPRG1"
    $Location1     = "East US"
    $VNetName1     = "TestVNet1"
    $FESubName1    = "FrontEnd"
    $BESubName1    = "Backend"
    $GWSubName1    = "GatewaySubnet"
    $VNetPrefix11  = "10.11.0.0/16"
    $VNetPrefix12  = "10.12.0.0/16"
    $FESubPrefix1  = "10.11.0.0/24"
    $BESubPrefix1  = "10.12.0.0/24"
    $GWSubPrefix1  = "10.12.255.0/27"
    $VNet1ASN      = 65010
    $DNS1          = "8.8.8.8"
    $GWName1       = "VNet1GW"
    $GWIPName1     = "VNet1GWIP"
    $GWIPconfName1 = "gwipconf1"
    $Connection12  = "VNet1toVNet2"
    $Connection15  = "VNet1toSite5"

#### <a name="2-connect-to-your-subscription-and-create-a-new-resource-group"></a>2. conectarse a su suscripción y crear un nuevo grupo de recursos

Asegúrese de que cambie al modo de PowerShell para usar los cmdlets del Administrador de recursos. Para obtener más información, vea [Usar Windows PowerShell con el Administrador de recursos](../powershell-azure-resource-manager.md).

Abra la consola de PowerShell y conectarse a su cuenta. Use el siguiente ejemplo para ayudarle a conectarse:

    Login-AzureRmAccount
    Select-AzureRmSubscription -SubscriptionName $Sub1
    New-AzureRmResourceGroup -Name $RG1 -Location $Location1

#### <a name="3-create-testvnet1"></a>3. crear TestVNet1

En el ejemplo siguiente se crea una red virtual denominada TestVNet1 y tres subredes, GatewaySubnet llamado uno, un cliente llamado y back-end llamado una. Al sustituir valores, es importante que siempre nombre de la subred de puerta de enlace GatewaySubnet específicamente. Si el nombre algo distinto, se producirá un error en la creación de la puerta de enlace. 

    $fesub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $FESubName1 -AddressPrefix $FESubPrefix1
    $besub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $BESubName1 -AddressPrefix $BESubPrefix1
    $gwsub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $GWSubName1 -AddressPrefix $GWSubPrefix1

    New-AzureRmVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1 -Location $Location1 -AddressPrefix $VNetPrefix11,$VNetPrefix12 -Subnet $fesub1,$besub1,$gwsub1

### <a name="step-2---create-the-vpn-gateway-for-testvnet1-with-bgp-parameters"></a>Paso 2: crear la puerta de enlace VPN para TestVNet1 con parámetros BGP

#### <a name="1-create-the-ip-and-subnet-configurations"></a>1. crear las configuraciones IP y subred

Solicitar una dirección IP pública que se asignarán a la puerta de enlace que va a crear la VNet. También deberá definir la subred y configuraciones IP necesarias. 

    $gwpip1    = New-AzureRmPublicIpAddress -Name $GWIPName1 -ResourceGroupName $RG1 -Location $Location1 -AllocationMethod Dynamic
    
    $vnet1     = Get-AzureRmVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1
    $subnet1   = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet1
    $gwipconf1 = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GWIPconfName1 -Subnet $subnet1 -PublicIpAddress $gwpip1

#### <a name="2-create-the-vpn-gateway-with-the-as-number"></a>2. crear la puerta de enlace VPN con el número de AS

Crear la puerta de enlace de red virtual para TestVNet1. Tenga en cuenta que BGP requiere una puerta de enlace VPN basada en la ruta y también el parámetro de adición, - Asn, para establecer la ASN (como número) para TestVNet1. Creación de una puerta de enlace puede tardar un rato (30 minutos o más para completar).

    New-AzureRmVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1 -Location $Location1 -IpConfigurations $gwipconf1 -GatewayType Vpn -VpnType RouteBased -GatewaySku HighPerformance -Asn $VNet1ASN

#### <a name="3-obtain-the-azure-bgp-peer-ip-address"></a>3. obtener la dirección IP de punto de BGP de Azure

Una vez creada la puerta de enlace, debe obtener la dirección IP de punto de BGP en la puerta de enlace de VPN de Azure. Esta dirección es necesaria para configurar la puerta de enlace de Azure VPN como par BGP para los dispositivos VPN local.

    $vnet1gw = Get-AzureRmVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1
    $vnet1gw.BgpSettingsText

El último comando mostrará las configuraciones de BGP correspondientes de la puerta de enlace de la VPN Azure; Por ejemplo:

    $vnet1gw.BgpSettingsText
    {
        "Asn": 65010,
        "BgpPeeringAddress": "10.12.255.30",
        "PeerWeight": 0
    }

Una vez creada la puerta de enlace, puede usar esta puerta de enlace para establecer conexión entre local o VNet a VNet con BGP. Las secciones siguientes le guiará por los pasos para completar el ejercicio.

## <a name ="crossprembbgp"></a>Parte 2: establecer una conexión entre local con BGP

Para establecer una conexión entre local, debe crear una puerta de enlace de red Local para representar el dispositivo VPN local y una conexión para conectarse a la puerta de enlace de Azure VPN con la puerta de enlace de red local. La diferencia entre las instrucciones de este artículo es las propiedades adicionales necesarios para especificar los parámetros de configuración de BGP.

![BGP para las instalaciones de cruz](./media/vpn-gateway-bgp-resource-manager-ps/bgp-crossprem.png)

Antes de continuar, asegúrese de que haya completado la [parte 1](#enablebgp) de este ejercicio.

### <a name="step-1---create-and-configure-the-local-network-gateway"></a>Paso 1: crear y configurar la puerta de enlace de red local

#### <a name="1-declare-your-variables"></a>1. declarar las variables

Este ejercicio continuará generar la configuración que se muestra en el diagrama. Asegúrese de reemplazar los valores por los que desea usar para su configuración.

    $RG5           = "TestBGPRG5"
    $Location5     = "East US 2"
    $LNGName5      = "Site5"
    $LNGPrefix50   = "10.52.255.254/32"
    $LNGIP5        = "Your_VPN_Device_IP"
    $LNGASN5       = 65050
    $BGPPeerIP5    = "10.52.255.254"

Un par de cosas Nota sobre los parámetros de la puerta de enlace de red local:

- La puerta de enlace de red local puede estar en la misma u otra ubicación y el grupo de recursos como la puerta de enlace VPN. Este ejemplo muestra los diferentes grupos de recursos en diferentes ubicaciones.

- El prefijo mínimo que debe declarar de la puerta de enlace de red local es la dirección de host de la dirección IP del punto de BGP en el dispositivo VPN. En este caso, es un /32 prefijo de "10.52.255.254/32".

- Como recordatorio, debe usar diferentes ASNs BGP entre sus redes local y VNet de Azure. Si son iguales, debe cambiar su ASN VNet si el dispositivo VPN local ya usa la ASN del mismo nivel con los otros elementos de próximos BGP.
    
Antes de continuar, asegúrese de que esté conectado a 1 de suscripción.

#### <a name="2-create-the-local-network-gateway-for-site5"></a>2. crear la puerta de enlace de red local para Site5

Asegúrese de crear el grupo de recursos, si no se crea, antes de crear la puerta de enlace de red local. Observe los dos parámetros adicionales de la puerta de enlace de red local: Asn y BgpPeerAddress.

    New-AzureRmResourceGroup -Name $RG5 -Location $Location5

    New-AzureRmLocalNetworkGateway -Name $LNGName5 -ResourceGroupName $RG5 -Location $Location5 -GatewayIpAddress $LNGIP5 -AddressPrefix $LNGPrefix50 -Asn $LNGASN5 -BgpPeeringAddress $BGPPeerIP5

### <a name="step-2---connect-the-vnet-gateway-and-local-network-gateway"></a>Paso 2: conectar la puerta de enlace de VNet y la puerta de enlace de red local

#### <a name="1-get-the-two-gateways"></a>1. obtener las dos puertas de enlace

        $vnet1gw = Get-AzureRmVirtualNetworkGateway -Name $GWName1  -ResourceGroupName $RG1
        $lng5gw  = Get-AzureRmLocalNetworkGateway -Name $LNGName5 -ResourceGroupName $RG5

#### <a name="2-create-the-testvnet1-to-site5-connection"></a>2. crear el TestVNet1 a la conexión de Site5

En este paso, creará la conexión de TestVNet1 a Site5. Debe especificar "-EnableBGP $True" Habilitar BGP para esta conexión. Como se indicó anteriormente, es posible tener conexiones BGP y no BGP para la misma puerta de enlace de VPN de Azure. A menos que BGP está habilitada en la propiedad de conexión, Azure no habilitar BGP para esta conexión aunque parámetros BGP ya están configurados en dos puertas de enlace.

    New-AzureRmVirtualNetworkGatewayConnection -Name $Connection15 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -LocalNetworkGateway2 $lng5gw -Location $Location1 -ConnectionType IPsec -SharedKey 'AzureA1b2C3' -EnableBGP $True


El ejemplo siguiente enumera los parámetros que entrará en la sección Configuración de BGP en el dispositivo VPN local para este ejercicio:

    - Site5 ASN: 65050
    - IP de BGP Site5: 10.52.255.254
    - Prefijos anunciar: (por ejemplo), 10.51.0.0/16 y 10.52.0.0/16
    - Azure ASN VNet: 65010
    - VNet Azure BGP IP: 10.12.255.30
    - Ruta estática: agregar una ruta para 10.12.255.30/32, con el siguiente salto que se va a la interfaz de túnel VPN en el dispositivo
    - eBGP salto múltiple: garantizar la opción "salto múltiple" eBGP se ha habilitado en el dispositivo si es necesario

Se debe establecer la conexión después de unos minutos y, a continuación, se iniciará la sesión de interconexión BGP una vez establecida la conexión IPsec.
 
## <a name ="v2vbgp"></a>Parte 3: establecer una conexión de VNet a VNet con BGP

En esta sección se agrega una conexión de VNet a VNet con BGP, como se muestra en el diagrama siguiente. 

![BGP para VNet a VNet](./media/vpn-gateway-bgp-resource-manager-ps/bgp-vnet2vnet.png)

Seguir las instrucciones a continuación de los pasos anteriores enumerados anteriormente. Debe completar [parte](#enablebgp) para crear y configurar TestVNet1 y la puerta de enlace VPN con BGP. 

### <a name="step-1---create-testvnet2-and-the-vpn-gateway"></a>Paso 1: crear TestVNet2 y la puerta de enlace VPN

Es importante para asegurarse de que el espacio de direcciones IP de la nueva red virtual TestVNet2, no se superpone con cualquiera de los rangos de VNet.

En este ejemplo, las redes virtuales pertenecen a la misma suscripción. Puede configurar conexiones de VNet a VNet entre varias suscripciones; [Configurar una conexión de VNet a VNet](./vpn-gateway-vnet-vnet-rm-ps.md) para obtener información más detallada, consulte. Asegúrese de que agrega el "-EnableBgp $True" al crear las conexiones para habilitar BGP.

#### <a name="1-declare-your-variables"></a>1. declarar las variables

Asegúrese de reemplazar los valores por los que desea usar para su configuración.

    $RG2           = "TestBGPRG2"
    $Location2     = "West US"
    $VNetName2     = "TestVNet2"
    $FESubName2    = "FrontEnd"
    $BESubName2    = "Backend"
    $GWSubName2    = "GatewaySubnet"
    $VNetPrefix21  = "10.21.0.0/16"
    $VNetPrefix22  = "10.22.0.0/16"
    $FESubPrefix2  = "10.21.0.0/24"
    $BESubPrefix2  = "10.22.0.0/24"
    $GWSubPrefix2  = "10.22.255.0/27"
    $VNet2ASN      = 65020
    $DNS2          = "8.8.8.8"
    $GWName2       = "VNet2GW"
    $GWIPName2     = "VNet2GWIP"
    $GWIPconfName2 = "gwipconf2"
    $Connection21  = "VNet2toVNet1"
    $Connection12  = "VNet1toVNet2"

#### <a name="2-create-testvnet2-in-the-new-resource-group"></a>2. crear TestVNet2 en el nuevo grupo de recursos

    New-AzureRmResourceGroup -Name $RG2 -Location $Location2
    
    $fesub2 = New-AzureRmVirtualNetworkSubnetConfig -Name $FESubName2 -AddressPrefix $FESubPrefix2
    $besub2 = New-AzureRmVirtualNetworkSubnetConfig -Name $BESubName2 -AddressPrefix $BESubPrefix2
    $gwsub2 = New-AzureRmVirtualNetworkSubnetConfig -Name $GWSubName2 -AddressPrefix $GWSubPrefix2

    New-AzureRmVirtualNetwork -Name $VNetName2 -ResourceGroupName $RG2 -Location $Location2 -AddressPrefix $VNetPrefix21,$VNetPrefix22 -Subnet $fesub2,$besub2,$gwsub2

#### <a name="3-create-the-vpn-gateway-for-testvnet2-with-bgp-parameters"></a>3. crear la puerta de enlace VPN para TestVNet2 con parámetros BGP

Solicitar una dirección IP pública que se asignarán a la puerta de enlace que va a crear la VNet. También deberá definir la subred y configuraciones IP necesarias. 

    $gwpip2    = New-AzureRmPublicIpAddress -Name $GWIPName2 -ResourceGroupName $RG2 -Location $Location2 -AllocationMethod Dynamic

    $vnet2     = Get-AzureRmVirtualNetwork -Name $VNetName2 -ResourceGroupName $RG2
    $subnet2   = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet2
    $gwipconf2 = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GWIPconfName2 -Subnet $subnet2 -PublicIpAddress $gwpip2

Crear la puerta de enlace VPN con el número de AS. Observe que debe reemplazar el valor predeterminado ASN en las puertas de enlace VPN de Azure. La ASNs para el VNets conectados deben ser diferentes para habilitar el enrutamiento de tránsito y BGP.

    New-AzureRmVirtualNetworkGateway -Name $GWName2 -ResourceGroupName $RG2 -Location $Location2 -IpConfigurations $gwipconf2 -GatewayType Vpn -VpnType RouteBased -GatewaySku Standard -Asn $VNet2ASN

### <a name="step-2---connect-the-testvnet1-and-testvnet2-gateways"></a>Paso 2: conectar las puertas de enlace TestVNet1 y TestVNet2

En este ejemplo, las puertas de enlace se encuentran en la misma suscripción. Puede completar este paso en la misma sesión de PowerShell.

#### <a name="1-get-both-gateways"></a>1. obtener dos puertas de enlace

Asegúrese de que iniciar sesión y conectarse a 1 de suscripción.

    $vnet1gw = Get-AzureRmVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1
    $vnet2gw = Get-AzureRmVirtualNetworkGateway -Name $GWName2 -ResourceGroupName $RG2
    
#### <a name="2-create-both-connections"></a>2. crear las conexiones

En este paso, creará la conexión de TestVNet1 a TestVNet2 y la conexión de TestVNet2 a TestVNet1.

    New-AzureRmVirtualNetworkGatewayConnection -Name $Connection12 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -VirtualNetworkGateway2 $vnet2gw -Location $Location1 -ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3' -EnableBgp $True

    New-AzureRmVirtualNetworkGatewayConnection -Name $Connection21 -ResourceGroupName $RG2 -VirtualNetworkGateway1 $vnet2gw -VirtualNetworkGateway2 $vnet1gw -Location $Location2 -ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3' -EnableBgp $True

>[AZURE.IMPORTANT] Asegúrese de habilitar BGP para ambas conexiones.

Después de completar estos pasos, la conexión se establecerá en unos minutos y, a continuación, la sesión de interconexión BGP será una vez se complete la conexión de VNet a VNet.

Si ha completado las tres partes de esta práctica, tendrá establecido una topología de red tal como se muestra a continuación:

![BGP para VNet a VNet](./media/vpn-gateway-bgp-resource-manager-ps/bgp-crosspremv2v.png)

## <a name="next-steps"></a>Pasos siguientes

Una vez finalizada la conexión, puede agregar máquinas virtuales con sus redes virtuales. Consulte [crear una máquina Virtual](../virtual-machines/virtual-machines-windows-hero-tutorial.md) para los pasos.

