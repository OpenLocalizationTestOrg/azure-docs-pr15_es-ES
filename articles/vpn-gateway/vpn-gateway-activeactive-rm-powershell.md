<properties
   pageTitle="Cómo configurar conexiones de VPN S2S activo activo con puertas de enlace de Azure VPN con el Administrador de recursos de Azure y PowerShell | Microsoft Azure"
   description="En este artículo se explica cómo configurar conexiones de activo activo con puertas de enlace de Azure VPN con el Administrador de recursos de Azure y PowerShell."
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
   ms.date="09/26/2016"
   ms.author="yushwang"/>

# <a name="configure-active-active-s2s-vpn-connections-with-azure-vpn-gateways-using-azure-resource-manager-and-powershell"></a>Configurar conexiones de VPN S2S activo activo con puertas de enlace de Azure VPN con el Administrador de recursos de Azure y PowerShell

En este artículo le guiará por los pasos para crear conexiones de VNet a VNet mediante el modelo de implementación de administrador de recursos y PowerShell y activo activo entre-local.


**Acerca de los modelos de implementación de Azure**

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)] 

## <a name="about-highly-available-cross-premises-connections"></a>Acerca de las conexiones entre local altamente disponible

Para lograr alta disponibilidad para entre local y conectividad de VNet a VNet, debe implementar varias puertas de enlace VPN y establecer varias conexiones paralelas entre sus redes y Azure. Vea [altamente disponibles entre local y conectividad de VNet a VNet](./vpn-gateway-highlyavailable.md) para obtener información general de opciones de conectividad y la topología.

En este artículo se proporciona las instrucciones para configurar una conexión VPN de activo activo entre local y conexión activo activo entre dos redes virtuales:

- [Parte 1: crear y configurar la puerta de enlace VPN de Azure en modo activo activo](#aagateway)

- [Parte 2: establecer conexiones locales entre activo activo](#aacrossprem)

- [Parte 3: establecer conexiones de VNet a VNet activo activo](#aav2v)

- [Parte 4: actualizar la puerta de enlace existente entre activo activo y reserva activa](#aaupdate)

Puede combinar estas entre sí para crear una topología de red más complejas, es muy disponibles que satisfaga sus necesidades.

>[AZURE.IMPORTANT] Tenga en cuenta que el modo activo / activo sólo funciona en SKU HighPerformance


## <a name ="aagateway"></a>Parte 1: crear y configurar puertas de enlace VPN activo activo

Los pasos siguientes configurará la puerta de enlace VPN de Azure en los modos activo activo. Las diferencias clave entre las puertas de enlace activo activo y reserva activa:

- Debe crear dos configuraciones de puerta de enlace IP con dos direcciones IP públicas
- Necesita configurar la marca de EnableActiveActiveFeature
- La puerta de enlace SKU debe ser de alto rendimiento

Las demás propiedades están la misma que las puertas de enlace no activo. 

### <a name="before-you-begin"></a>Antes de empezar

- Compruebe que tiene una suscripción de Azure. Si todavía no tiene una suscripción de Azure, puede activar los [beneficios de suscriptor MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) o signo hacia arriba para obtener una [cuenta gratuita](https://azure.microsoft.com/pricing/free-trial/).
    
- Debe instalar los cmdlets de PowerShell de administrador de recursos de Azure. Para obtener más información sobre la instalación de los cmdlets de PowerShell, vea [cómo instalar y configurar Azure PowerShell](../powershell-install-configure.md) .

### <a name="step-1---create-and-configure-vnet1"></a>Paso 1: crear y configurar VNet1

#### <a name="1-declare-your-variables"></a>1. declarar las variables

Para este ejercicio, comenzaremos por declarar las variables. El ejemplo siguiente declara las variables con los valores para este ejercicio. Asegúrese de reemplazar los valores con su propio al configurar para producción. Puede utilizar estas variables si se están ejecutando los pasos para familiarizarse con este tipo de configuración. Modifique las variables y, a continuación, copiar y pegar en la consola de PowerShell.

    $Sub1          = "Ross"
    $RG1           = "TestAARG1"
    $Location1     = "West US"
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
    $GW1IPName1    = "VNet1GWIP1"
    $GW1IPName2    = "VNet1GWIP2"
    $GW1IPconf1    = "gw1ipconf1"
    $GW1IPconf2    = "gw1ipconf2"
    $Connection12  = "VNet1toVNet2"
    $Connection151 = "VNet1toSite5_1"
    $Connection152 = "VNet1toSite5_2"

#### <a name="2-connect-to-your-subscription-and-create-a-new-resource-group"></a>2. conectarse a su suscripción y crear un nuevo grupo de recursos

Asegúrese de que cambie al modo de PowerShell para usar los cmdlets del Administrador de recursos. Para obtener más información, vea [Usar Windows PowerShell con el Administrador de recursos](../powershell-azure-resource-manager.md).

Abra la consola de PowerShell y conectarse a su cuenta. Use el siguiente ejemplo para ayudarle a conectarse:

    Login-AzureRmAccount
    Select-AzureRmSubscription -SubscriptionName $Sub1
    New-AzureRmResourceGroup -Name $RG1 -Location $Location1

#### <a name="3-create-testvnet1"></a>3. crear TestVNet1

En el ejemplo siguiente se crea una red virtual denominada TestVNet1 y tres subredes, GatewaySubnet llamado uno, un cliente llamado y back-end llamado una. Al sustituir valores, es importante que siempre nombre de la subred de puerta de enlace concreto GatewaySubnet. Si el nombre algo distinto, se producirá un error en la creación de la puerta de enlace. 

    $fesub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $FESubName1 -AddressPrefix $FESubPrefix1
    $besub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $BESubName1 -AddressPrefix $BESubPrefix1
    $gwsub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $GWSubName1 -AddressPrefix $GWSubPrefix1

    New-AzureRmVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1 -Location $Location1 -AddressPrefix $VNetPrefix11,$VNetPrefix12 -Subnet $fesub1,$besub1,$gwsub1

### <a name="step-2---create-the-vpn-gateway-for-testvnet1-with-active-active-mode"></a>Paso 2: crear la puerta de enlace VPN para TestVNet1 con el modo activo / activo

#### <a name="1-create-the-public-ip-addresses-and-gateway-ip-configurations"></a>1. crear las direcciones IP y la puerta de enlace público configuraciones IP

Solicitar dos direcciones IP públicas que se asignarán a la puerta de enlace que va a crear la VNet. También deberá definir la subred y configuraciones IP necesarias. 

    $gw1pip1    = New-AzureRmPublicIpAddress -Name $GW1IPName1 -ResourceGroupName $RG1 -Location $Location1 -AllocationMethod Dynamic
    $gw1pip2    = New-AzureRmPublicIpAddress -Name $GW1IPName2 -ResourceGroupName $RG1 -Location $Location1 -AllocationMethod Dynamic

    $vnet1      = Get-AzureRmVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1
    $subnet1    = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet1
    $gw1ipconf1 = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GW1IPconf1 -Subnet $subnet1 -PublicIpAddress $gw1pip1
    $gw1ipconf2 = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GW1IPconf2 -Subnet $subnet1 -PublicIpAddress $gw1pip2

#### <a name="2-create-the-vpn-gateway-with-active-active-configuration"></a>2. crear la puerta de enlace VPN con configuración activo activo

Crear la puerta de enlace de red virtual para TestVNet1. Observe que hay dos entradas de GatewayIpConfig y se establece el indicador EnableActiveActiveFeature. Modo activo activo requiere una puerta de enlace VPN basada en la ruta de HighPerformance SKU. Creación de una puerta de enlace puede tardar un rato (30 minutos o más para completar).

    New-AzureRmVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1 -Location $Location1 -IpConfigurations $gw1ipconf1,$gw1ipconf2 -GatewayType Vpn -VpnType RouteBased -GatewaySku HighPerformance -Asn $VNet1ASN -EnableActiveActiveFeature -Debug

#### <a name="3-obtain-the-gateway-public-ip-addresses-and-the-bgp-peer-ip-address"></a>3. obtener las direcciones IP públicas de puerta de enlace y la dirección IP del punto de BGP

Una vez creada la puerta de enlace, debe obtener la dirección IP de punto de BGP en la puerta de enlace de VPN de Azure. Esta dirección es necesaria para configurar la puerta de enlace de Azure VPN como par BGP para los dispositivos VPN local.

    $gw1pip1 = Get-AzureRmPublicIpAddress -Name $GW1IPName1 -ResourceGroupName $RG1
    $gw1pip2 = Get-AzureRmPublicIpAddress -Name $GW1IPName2 -ResourceGroupName $RG1
    $vnet1gw = Get-AzureRmVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1

Use los siguientes cmdlets para mostrar las dos direcciones IP públicas asignadas a la puerta de enlace VPN y sus correspondientes direcciones IP de punto de BGP para cada instancia de puerta de enlace:

    PS D:\> $gw1pip1.IpAddress
    40.112.190.5

    PS D:\> $gw1pip2.IpAddress
    138.91.156.129

    PS D:\> $vnet1gw.BgpSettingsText
    {
      "Asn": 65010,
      "BgpPeeringAddress": "10.12.255.4,10.12.255.5",
      "PeerWeight": 0
    }

El orden de la dirección IP pública direcciones de las instancias de puerta de enlace y las direcciones de interconexión BGP correspondientes son iguales. En este ejemplo, la puerta de enlace VM con IP pública de 40.112.190.5 utiliza 10.12.255.4 como su dirección de interconexión BGP y la puerta de enlace con 138.91.156.129 usará 10.12.255.5. Esta información es necesaria para configurar los dispositivos VPN local en conectarse a la puerta de enlace activo activo. La puerta de enlace se muestra en el siguiente diagrama con todas las direcciones:

![puerta de enlace activo activo](./media/vpn-gateway-activeactive-rm-powershell/active-active-gw.png)

Una vez creada la puerta de enlace, puede usar esta puerta de enlace para establecer conexión de VNet a VNet o activo activo entre local. Las secciones siguientes le guiará por los pasos para completar el ejercicio.


## <a name ="aacrossprem"></a>Parte 2: establecer una conexión local entre activo activo

Para establecer una conexión entre local, debe crear una puerta de enlace de red Local para representar el dispositivo VPN local y una conexión para conectarse a la puerta de enlace de Azure VPN con la puerta de enlace de red local. En este ejemplo, la puerta de enlace VPN de Azure está en modo activo activo. Como resultado, aunque también hay solo uno local dispositivo VPN (puerta de enlace de red local) y recursos de una conexión, ambas instancias de puerta de enlace VPN de Azure establecer túneles VPN S2S con el dispositivo local.

Antes de continuar, asegúrese de que haya completado la [parte 1](#aagateway) de este ejercicio.

### <a name="step-1---create-and-configure-the-local-network-gateway"></a>Paso 1: crear y configurar la puerta de enlace de red local

#### <a name="1-declare-your-variables"></a>1. declarar las variables

Este ejercicio continuará generar la configuración que se muestra en el diagrama. Asegúrese de reemplazar los valores por los que desea usar para su configuración.

    $RG5           = "TestAARG5"
    $Location5     = "West US"
    $LNGName51     = "Site5_1"
    $LNGPrefix51   = "10.52.255.253/32"
    $LNGIP51       = "131.107.72.22"
    $LNGASN5       = 65050
    $BGPPeerIP51   = "10.52.255.253"

Un par de cosas Nota sobre los parámetros de la puerta de enlace de red local:

- La puerta de enlace de red local puede estar en la misma u otra ubicación y el grupo de recursos como la puerta de enlace VPN. Este ejemplo muestra en distintos grupos de recursos, pero en la misma ubicación de Azure.

- Si hay un solo dispositivo VPN local como se indicó anteriormente, la conexión activo activo puede trabajar con o sin el protocolo BGP. Este ejemplo usa BGP para la conexión entre local.

- Si está habilitado BGP, el prefijo que debe declarar de la puerta de enlace de red local es la dirección de host de la dirección IP del punto de BGP en el dispositivo VPN. En este caso, es un /32 prefijo de "10.52.255.253/32".

- Como recordatorio, debe usar diferentes ASNs BGP entre sus redes local y VNet de Azure. Si son iguales, debe cambiar su ASN VNet si el dispositivo VPN local ya usa la ASN del mismo nivel con los otros elementos de próximos BGP.

#### <a name="2-create-the-local-network-gateway-for-site5"></a>2. crear la puerta de enlace de red local para Site5
    
Antes de continuar, asegúrese de que esté conectado a 1 de suscripción. Crear el grupo de recursos si no se ha creado.

    New-AzureRmResourceGroup       -Name $RG5 -Location $Location5
    New-AzureRmLocalNetworkGateway -Name $LNGName51 -ResourceGroupName $RG5 -Location $Location5 -GatewayIpAddress $LNGIP51 -AddressPrefix $LNGPrefix51 -Asn $LNGASN5 -BgpPeeringAddress $BGPPeerIP51

### <a name="step-2---connect-the-vnet-gateway-and-local-network-gateway"></a>Paso 2: conectar la puerta de enlace de VNet y la puerta de enlace de red local

#### <a name="1-get-the-two-gateways"></a>1. obtener las dos puertas de enlace

    $vnet1gw = Get-AzureRmVirtualNetworkGateway -Name $GWName1  -ResourceGroupName $RG1
    $lng5gw1 = Get-AzureRmLocalNetworkGateway   -Name $LNGName51 -ResourceGroupName $RG5

#### <a name="2-create-the-testvnet1-to-site5-connection"></a>2. crear el TestVNet1 a la conexión de Site5

En este paso, creará la conexión de TestVNet1 a Site5_1 con "EnableBGP" valor $True.

    New-AzureRmVirtualNetworkGatewayConnection -Name $Connection151 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -LocalNetworkGateway2 $lng5gw1 -Location $Location1 -ConnectionType IPsec -SharedKey 'AzureA1b2C3' -EnableBGP True

#### <a name="3-vpn-and-bgp-parameters-for-your-on-premises-vpn-device"></a>3. VPN y BGP parámetros de su dispositivo VPN local

El ejemplo siguiente enumera los parámetros que entrará en la sección Configuración de BGP en el dispositivo VPN local para este ejercicio:

    - Site5 ASN: 65050
    - IP de BGP Site5: 10.52.255.253
    - Prefijos anunciar: (por ejemplo), 10.51.0.0/16 y 10.52.0.0/16
    - Azure ASN VNet: 65010
    - Azure VNet BGP IP 1: 10.12.255.4 túnel a 40.112.190.5
    - Azure VNet BGP IP 2: 10.12.255.5 túnel a 138.91.156.129
    - Rutas estáticas: destino 10.12.255.4/32, siguiente salto túnel VPN interfaz a 40.112.190.5 destino 10.12.255.5/32, siguiente salto de la interfaz del túnel VPN a 138.91.156.129
    - eBGP salto múltiple: garantizar la opción "salto múltiple" eBGP se ha habilitado en el dispositivo si es necesario

Se debe establecer la conexión después de unos minutos y, a continuación, se iniciará la sesión de interconexión BGP una vez establecida la conexión IPsec. En este ejemplo hasta ahora ha configurado un solo dispositivo VPN local, es el resultado en el diagrama que se muestra a continuación:

![Active-active-crossprem](./media/vpn-gateway-activeactive-rm-powershell/active-active.png)

### <a name="step-3---connect-two-on-premises-vpn-devices-to-the-active-active-vpn-gateway"></a>Paso 3: conectar dos dispositivos VPN local a la puerta de enlace VPN activo activo

Si tiene dos dispositivos VPN en la misma red local, se puede conseguir redundancia dual mediante una conexión de la puerta de enlace VPN de Azure en el segundo VPN.

#### <a name="1-create-the-second-local-network-gateway-for-site5"></a>1. crear la segunda puerta de enlace de red local para Site5

Tenga en cuenta que la dirección IP de puerta de enlace, el prefijo de dirección y la dirección de interconexión de BGP para la segunda puerta de enlace de red local deben se superpone con la puerta de enlace de red local anterior de la misma red local. 

    $LNGName52     = "Site5_2"
    $LNGPrefix52   = "10.52.255.254/32"
    $LNGIP52       = "131.107.72.23"
    $BGPPeerIP52   = "10.52.255.254"

    New-AzureRmLocalNetworkGateway -Name $LNGName52 -ResourceGroupName $RG5 -Location $Location5 -GatewayIpAddress $LNGIP52 -AddressPrefix $LNGPrefix52 -Asn $LNGASN5 -BgpPeeringAddress $BGPPeerIP52
 
#### <a name="2-connect-the-vnet-gateway-and-the-second-local-network-gateway"></a>2. Conecte la puerta de enlace de VNet y la segunda puerta de enlace de red local

Crear la conexión de TestVNet1 a Site5_2 con "EnableBGP" valor $True

    $lng5gw2 = Get-AzureRmLocalNetworkGateway   -Name $LNGName52 -ResourceGroupName $RG5

    New-AzureRmVirtualNetworkGatewayConnection -Name $Connection152 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -LocalNetworkGateway2 $lng5gw2 -Location $Location1 -ConnectionType IPsec -SharedKey 'AzureA1b2C3' -EnableBGP True

#### <a name="3-vpn-and-bgp-parameters-for-your-second-on-premises-vpn-device"></a>3. VPN y BGP parámetros para el segundo dispositivo VPN local

Asimismo, debajo de listas de los parámetros entrará en el segundo dispositivo VPN:

    - Site5 ASN: 65050
    - IP de BGP Site5: 10.52.255.254
    - Prefijos anunciar: (por ejemplo), 10.51.0.0/16 y 10.52.0.0/16
    - Azure ASN VNet: 65010
    - Azure VNet BGP IP 1: 10.12.255.4 túnel a 40.112.190.5
    - Azure VNet BGP IP 2: 10.12.255.5 túnel a 138.91.156.129
    - Rutas estáticas: destino 10.12.255.4/32, siguiente salto túnel VPN interfaz a 40.112.190.5 destino 10.12.255.5/32, siguiente salto de la interfaz del túnel VPN a 138.91.156.129
    - eBGP salto múltiple: garantizar la opción "salto múltiple" eBGP se ha habilitado en el dispositivo si es necesario

Una vez que se establece la conexión (túneles), tendrá dos dispositivos VPN redundantes y túneles conectarse a su red local y Azure:

![crossprem de redundancia de doble](./media/vpn-gateway-activeactive-rm-powershell/dual-redundancy.png)


## <a name ="aav2v"></a>Parte 3: establecer una conexión de VNet a VNet activo activo

En esta sección se crea una conexión de VNet a VNet activo activo con BGP. 

Seguir las instrucciones a continuación de los pasos anteriores enumerados anteriormente. Debe completar [parte 1](#aagateway) para crear y configurar TestVNet1 y la puerta de enlace VPN con BGP. 

### <a name="step-1---create-testvnet2-and-the-vpn-gateway"></a>Paso 1: crear TestVNet2 y la puerta de enlace VPN

Es importante para asegurarse de que el espacio de direcciones IP de la nueva red virtual TestVNet2, no se superpone con cualquiera de los rangos de VNet.

En este ejemplo, las redes virtuales pertenecen a la misma suscripción. Puede configurar conexiones de VNet a VNet entre varias suscripciones; [Configurar una conexión de VNet a VNet](./vpn-gateway-vnet-vnet-rm-ps.md) para obtener información más detallada, consulte. Asegúrese de que agrega el "-EnableBgp $True" al crear las conexiones para habilitar BGP.

#### <a name="1-declare-your-variables"></a>1. declarar las variables

Asegúrese de reemplazar los valores por los que desea usar para su configuración.

    $RG2           = "TestAARG2"
    $Location2     = "East US"
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
    $GW2IPName1    = "VNet2GWIP1"
    $GW2IPconf1    = "gw2ipconf1"
    $GW2IPName2    = "VNet2GWIP2"
    $GW2IPconf2    = "gw2ipconf2"
    $Connection21  = "VNet2toVNet1"
    $Connection12  = "VNet1toVNet2"

#### <a name="2-create-testvnet2-in-the-new-resource-group"></a>2. crear TestVNet2 en el nuevo grupo de recursos

    New-AzureRmResourceGroup -Name $RG2 -Location $Location2

    $fesub2 = New-AzureRmVirtualNetworkSubnetConfig -Name $FESubName2 -AddressPrefix $FESubPrefix2
    $besub2 = New-AzureRmVirtualNetworkSubnetConfig -Name $BESubName2 -AddressPrefix $BESubPrefix2
    $gwsub2 = New-AzureRmVirtualNetworkSubnetConfig -Name $GWSubName2 -AddressPrefix $GWSubPrefix2

    New-AzureRmVirtualNetwork -Name $VNetName2 -ResourceGroupName $RG2 -Location $Location2 -AddressPrefix $VNetPrefix21,$VNetPrefix22 -Subnet $fesub2,$besub2,$gwsub2

#### <a name="3-create-the-active-active-vpn-gateway-for-testvnet2"></a>3. crear la puerta de enlace VPN activo activo para TestVNet2

Solicitar dos direcciones IP públicas que se asignarán a la puerta de enlace que va a crear la VNet. También deberá definir la subred y configuraciones IP necesarias. 

    $gw2pip1    = New-AzureRmPublicIpAddress -Name $GW2IPName1 -ResourceGroupName $RG2 -Location $Location2 -AllocationMethod Dynamic
    $gw2pip2    = New-AzureRmPublicIpAddress -Name $GW2IPName2 -ResourceGroupName $RG2 -Location $Location2 -AllocationMethod Dynamic

    $vnet2      = Get-AzureRmVirtualNetwork -Name $VNetName2 -ResourceGroupName $RG2
    $subnet2    = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet2
    $gw2ipconf1 = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GW2IPconf1 -Subnet $subnet2 -PublicIpAddress $gw2pip1
    $gw2ipconf2 = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GW2IPconf2 -Subnet $subnet2 -PublicIpAddress $gw2pip2

Crear la puerta de enlace VPN con el número de AS y el indicador de "EnableActiveActiveFeature". Observe que debe reemplazar el valor predeterminado ASN en las puertas de enlace VPN de Azure. La ASNs para el VNets conectados deben ser diferentes para habilitar el enrutamiento de tránsito y BGP.

    New-AzureRmVirtualNetworkGateway -Name $GWName2 -ResourceGroupName $RG2 -Location $Location2 -IpConfigurations $gw2ipconf1,$gw2ipconf2 -GatewayType Vpn -VpnType RouteBased -GatewaySku HighPerformance -Asn $VNet2ASN -EnableActiveActiveFeature

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

Tras completar estos pasos, la conexión se establecerá en unos minutos y el BGP sesión interconexión volverá a estar en una vez que se complete la conexión de VNet a VNet con redundancia dual:

![Active-active-v2v](./media/vpn-gateway-activeactive-rm-powershell/vnet-to-vnet.png)

## <a name ="aaupdate"></a>Parte 4: actualizar la puerta de enlace existente entre activo activo y reserva activa

La última sección describe cómo configurar una puerta de enlace de Azure VPN existente de reserva activa en modo activo activo o viceversa.

>[AZURE.IMPORTANT] Tenga en cuenta que el modo activo / activo sólo funciona en SKU HighPerformance

### <a name="configure-an-active-standby-gateway-to-active-active-gateway"></a>Configurar una puerta de enlace de reserva activa a puerta de enlace activo activo

#### <a name="1-gateway-parameters"></a>1. parámetros de la puerta de enlace

En el ejemplo siguiente se convierte una puerta de enlace de reserva activa en una puerta de enlace activo activo. Tiene que crear otra dirección IP pública, a continuación, agregue una segunda configuración de puerta de enlace IP. A continuación muestra los parámetros que se utiliza:

    $GWName     = "TestVNetAA1GW"
    $VNetName   = "TestVNetAA1"
    $RG         = "TestVPNActiveActive01"
    $GWIPName2  = "gwpip2"
    $GWIPconf2  = "gw1ipconf2"

    $vnet       = Get-AzureRmVirtualNetwork -Name $VNetName -ResourceGroupName $RG
    $subnet     = Get-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -VirtualNetwork $vnet
    $gw         = Get-AzureRmVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG
    $location   = $gw.Location

#### <a name="2-create-the-public-ip-address-then-add-the-second-gateway-ip-configuration"></a>2. crear la dirección IP pública, a continuación, agregue la segunda configuración IP de puerta de enlace

    $gwpip2     = New-AzureRmPublicIpAddress -Name $GWIPName2 -ResourceGroupName $RG -Location $location -AllocationMethod Dynamic
    Add-AzureRmVirtualNetworkGatewayIpConfig -VirtualNetworkGateway $gw -Name $GWIPconf2 -Subnet $subnet -PublicIpAddress $gwpip2 

#### <a name="3-enable-active-active-mode-and-update-the-gateway"></a>3. habilitar el modo activo activo y actualizar la puerta de enlace

Debe establecer el objeto de puerta de enlace de PowerShell para desencadenar la actualización real. El SKU del objeto de puerta de enlace debe cambiarse a alto rendimiento desde que se creó anteriormente como estándar.

    Set-AzureRmVirtualNetworkGateway -VirtualNetworkGateway $gw -EnableActiveActiveFeature -GatewaySku HighPerformance

Esta actualización puede tardar 30 y 45 minutos.

### <a name="configure-an-active-active-gateway-to-active-standby-gateway"></a>Configurar una puerta de enlace activo activo de puerta de enlace de reserva activa

#### <a name="1-gateway-parameters"></a>1. parámetros de la puerta de enlace

Use los mismos parámetros como se indicó anteriormente, obtener el nombre de la configuración de IP que desea quitar.

    $GWName     = "TestVNetAA1GW"
    $RG         = "TestVPNActiveActive01"

    $gw         = Get-AzureRmVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG
    $ipconfname = $gw.IpConfigurations[1].Name

#### <a name="2-remove-the-gateway-ip-configuration-and-disable-the-active-active-mode"></a>2. Quite la configuración de IP de puerta de enlace y deshabilitar el modo activo / activo

Asimismo, debe establecer el objeto de puerta de enlace de PowerShell para desencadenar la actualización real.

    Remove-AzureRmVirtualNetworkGatewayIpConfig -Name $ipconfname -VirtualNetworkGateway $gw
    Set-AzureRmVirtualNetworkGateway -VirtualNetworkGateway $gw -DisableActiveActiveFeature

Esta actualización puede tardar hasta 30 a 45 minutos.


## <a name="next-steps"></a>Pasos siguientes

Una vez finalizada la conexión, puede agregar máquinas virtuales con sus redes virtuales. Consulte [crear una máquina Virtual](../virtual-machines/virtual-machines-windows-hero-tutorial.md) para los pasos.

