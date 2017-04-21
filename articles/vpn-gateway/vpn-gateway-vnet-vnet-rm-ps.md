<properties
   pageTitle="Conectar VNets Azure con puerta de enlace VPN y PowerShell | Microsoft Azure"
   description="En este artículo le guiará por conectar redes virtuales con el Administrador de recursos de Azure y PowerShell."
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"/>

<tags
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="08/31/2016"
   ms.author="cherylmc"/>

# <a name="configure-a-vnet-to-vnet-connection-for-resource-manager-using-powershell"></a>Configurar una conexión de VNet a VNet para el Administrador de recursos con PowerShell

> [AZURE.SELECTOR]
- [Administrador de recursos - Portal de Azure](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)
- [Administrador de recursos - PowerShell](vpn-gateway-vnet-vnet-rm-ps.md)
- [Clásico: Portal clásica](virtual-networks-configure-vnet-to-vnet-connection.md)

En este artículo le guiará por los pasos para crear una conexión entre VNets en el modelo de implementación de administrador de recursos mediante el uso de la puerta de enlace VPN. Las redes virtuales pueden ser en el mismos o diferentes regiones y de la mismas u otra suscripciones.


![diagrama de V2V](./media/vpn-gateway-vnet-vnet-rm-ps/v2vrmps.png)

### <a name="deployment-models-and-methods-for-vnet-to-vnet-connections"></a>Modelos de implementación y métodos para las conexiones de VNet a VNet

[AZURE.INCLUDE [deployment models](../../includes/vpn-gateway-deployment-models-include.md)]

La siguiente tabla muestra los métodos para las configuraciones de VNet a VNet y modelos de implementación disponible actualmente. Cuando esté disponible un artículo con pasos de configuración, nos vincularlo directamente desde esta tabla.

[AZURE.INCLUDE [vpn-gateway-table-vnet-vnet](../../includes/vpn-gateway-table-vnet-to-vnet-include.md)]

#### <a name="vnet-peering"></a>VNet interconexión

[AZURE.INCLUDE [vpn-gateway-vnetpeeringlink](../../includes/vpn-gateway-vnetpeeringlink-include.md)]


## <a name="about-vnet-to-vnet-connections"></a>Acerca de las conexiones de VNet a VNet

Conectarse a una red virtual a otra red virtual (VNet a VNet) es similar a una VNet a conectarse a una ubicación del sitio local. Ambos tipos de conectividad usan una puerta de enlace VPN de Azure para proporcionar un túnel seguro mediante IPsec/IKE. Puede ser la VNets que conectar en diferentes regiones. O en diferentes suscripciones. Incluso puede combinar VNet a VNet comunicación con configuraciones de varios sitios. Permite establecer topologías de red que se combinan entre local conectividad con la conectividad de red entre virtual, tal como se muestra en el diagrama siguiente:


![Acerca de las conexiones](./media/vpn-gateway-vnet-vnet-rm-ps/aboutconnections.png)
 
### <a name="why-connect-virtual-networks"></a>¿Por qué conectar redes virtuales?

Desea conectar redes virtuales por los siguientes motivos:

- **Combinación cruzada redundancia de región geográfica y presencia geo**
    - Puede configurar su propio geo replicación o sincronización con conectividad segura sin saliendo extremos a través de Internet.
    - Con el administrador del tráfico de Azure y equilibrador de carga, puede configurar altamente disponible carga de trabajo con geo redundancia en diferentes regiones de Azure. Un ejemplo de importante es configurar SQL siempre en con grupos de disponibilidad reparte en varias áreas de Azure.

- **Aplicaciones de varios niveles regionales con aislamiento o límite administrativo**
    - Dentro de la misma región, puede configurar las aplicaciones de varios niveles con varias redes virtuales conectadas debido a aislamiento o requisitos administrativos.


### <a name="vnet-to-vnet-faq"></a>Preguntas más frecuentes de VNet a VNet

[AZURE.INCLUDE [vpn-gateway-vnet-vnet-faq](../../includes/vpn-gateway-vnet-vnet-faq-include.md)] 


## <a name="which-set-of-steps-should-i-use"></a>¿Qué conjunto de pasos debo usar?

En este artículo, verá dos diferentes conjuntos de pasos. Un conjunto de pasos los para [VNets que se encuentran en la misma suscripción](#samesub)y otro para [VNets que residen en diferentes suscripciones](#difsub). La diferencia entre los conjuntos de clave es que puede crear y configurar todos los red virtual y los recursos de la puerta de enlace en la misma sesión de PowerShell.

Los pasos de este artículo utilizan variables declarados al principio de cada sección. Si ya está trabajando con VNets existente, modifique las variables para reflejar la configuración en su entorno. 

![Ambas conexiones](./media/vpn-gateway-vnet-vnet-rm-ps/differentsubscription.png)


## <a name="samesub"></a>Cómo conectar VNets que están en la misma suscripción

![diagrama de V2V](./media/vpn-gateway-vnet-vnet-rm-ps/v2vrmps.png)

### <a name="before-you-begin"></a>Antes de empezar
    
Antes de empezar, debe instalar los cmdlets de PowerShell de administrador de recursos de Azure. Para obtener más información sobre la instalación de los cmdlets de PowerShell, vea [cómo instalar y configurar Azure PowerShell](../powershell-install-configure.md) .

### <a name="Step1"></a>Paso 1: planear los intervalos de direcciones IP


En los pasos siguientes, creamos dos redes virtuales junto con sus subredes respectivos de la puerta de enlace y configuraciones. A continuación, creamos una conexión VPN entre los dos VNets. Es importante planear los intervalos de direcciones IP para la configuración de red. Tenga en cuenta que debe asegurarse de que ninguna de sus VNet rangos o intervalos de red local se superpone en modo alguno.

En los ejemplos, usamos los siguientes valores:

**Valores TestVNet1:**

- Nombre de VNet: TestVNet1
- Grupo de recursos: TestRG1
- Ubicación: Estados Unidos oriental
- TestVNet1: 10.11.0.0/16 & 10.12.0.0/16
- Front-end: 10.11.0.0/24
- Back-end: 10.12.0.0/24
- GatewaySubnet: 10.12.255.0/27
- Servidor DNS: 8.8.8.8
- GatewayName: VNet1GW
- IP pública: VNet1GWIP
- VPNType: RouteBased
- Connection(1to4): VNet1toVNet4
- Connection(1to5): VNet1toVNet5
- ConnectionType: VNet2VNet


**Valores TestVNet4:**

- Nombre de VNet: TestVNet4
- TestVNet2: 10.41.0.0/16 & 10.42.0.0/16
- Front-end: 10.41.0.0/24
- Back-end: 10.42.0.0/24
- GatewaySubnet: 10.42.255.0/27
- Grupo de recursos: TestRG4
- Ubicación: Estados Unidos oeste
- Servidor DNS: 8.8.8.8
- GatewayName: VNet4GW
- IP pública: VNet4GWIP
- VPNType: RouteBased
- Conexión: VNet4toVNet1
- ConnectionType: VNet2VNet



### <a name="Step2"></a>Paso 2: crear y configurar TestVNet1

1. Declarar las variables

    Para iniciar la declaración de variables. Este ejemplo declara las variables con los valores para este ejercicio. En la mayoría de los casos, debe reemplazar los valores con su propio. Sin embargo, puede usar estas variables si se están ejecutando los pasos para familiarizarse con este tipo de configuración. Modifique las variables si es necesario, a continuación, copiarlos y pegarlos en la consola de PowerShell.

        $Sub1 = "Replace_With_Your_Subcription_Name"
        $RG1 = "TestRG1"
        $Location1 = "East US"
        $VNetName1 = "TestVNet1"
        $FESubName1 = "FrontEnd"
        $BESubName1 = "Backend"
        $GWSubName1 = "GatewaySubnet"
        $VNetPrefix11 = "10.11.0.0/16"
        $VNetPrefix12 = "10.12.0.0/16"
        $FESubPrefix1 = "10.11.0.0/24"
        $BESubPrefix1 = "10.12.0.0/24"
        $GWSubPrefix1 = "10.12.255.0/27"
        $DNS1 = "8.8.8.8"
        $GWName1 = "VNet1GW"
        $GWIPName1 = "VNet1GWIP"
        $GWIPconfName1 = "gwipconf1"
        $Connection14 = "VNet1toVNet4"
        $Connection15 = "VNet1toVNet5"

2. Conectarse a su suscripción

    Cambiar a modo de PowerShell para usar los cmdlets del Administrador de recursos. Abra la consola de PowerShell y conectarse a su cuenta. Utilice el siguiente ejemplo para ayudarle a conectarse:

        Login-AzureRmAccount

    Compruebe las suscripciones para la cuenta.

        Get-AzureRmSubscription 

    Especifique la suscripción que desea usar.

        Select-AzureRmSubscription -SubscriptionName $Sub1

3. Crear un nuevo grupo de recursos

        New-AzureRmResourceGroup -Name $RG1 -Location $Location1

4. Crear las configuraciones de subred para TestVNet1

    En este ejemplo se crea una red virtual denominada TestVNet1 y tres subredes, GatewaySubnet llamado uno, un cliente llamado y back-end llamado una. Al sustituir valores, es importante que siempre nombre de la subred de puerta de enlace GatewaySubnet específicamente. Si el nombre algo distinto, se producirá un error en la creación de la puerta de enlace. 

    El ejemplo siguiente utiliza las variables que ha configurado una versión anterior. En este ejemplo, la subred de puerta de enlace está usando un /27. Si es posible crear una subred de puerta de enlace tan pequeños como /29, le recomendamos que cree una subred mayor que incluye más direcciones seleccionando al menos /28 o /27. Esto le permitirá para que las direcciones de suficiente acomodar posibles configuraciones adicionales que desee en el futuro. 

        $fesub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $FESubName1 -AddressPrefix $FESubPrefix1
        $besub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $BESubName1 -AddressPrefix $BESubPrefix1
        $gwsub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $GWSubName1 -AddressPrefix $GWSubPrefix1


5. Crear TestVNet1

        New-AzureRmVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1 `
        -Location $Location1 -AddressPrefix $VNetPrefix11,$VNetPrefix12 -Subnet $fesub1,$besub1,$gwsub1

6. Solicitar una dirección IP pública

    Solicitar una dirección IP pública que se asignarán a la puerta de enlace que va a crear la VNet. Observe que la AllocationMethod es dinámico. No puede especificar la dirección IP que desee usar. Se asigna dinámicamente a la puerta de enlace. 

        $gwpip1 = New-AzureRmPublicIpAddress -Name $GWIPName1 -ResourceGroupName $RG1 `
        -Location $Location1 -AllocationMethod Dynamic

7. Crear la configuración de puerta de enlace

    La configuración de puerta de enlace define la subred y la dirección IP pública. Use el ejemplo para crear su configuración de puerta de enlace. 

        $vnet1 = Get-AzureRmVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1
        $subnet1 = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet1
        $gwipconf1 = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GWIPconfName1 `
        -Subnet $subnet1 -PublicIpAddress $gwpip1

8. Crear la puerta de enlace para TestVNet1

    En este paso, creará la puerta de enlace de red virtual para su TestVNet1. Configuraciones de VNet a VNet requieren un RouteBased VpnType. Creación de una puerta de enlace puede tardar un rato (45 minutos o más para completar).

        New-AzureRmVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1 `
        -Location $Location1 -IpConfigurations $gwipconf1 -GatewayType Vpn `
        -VpnType RouteBased -GatewaySku Standard

### <a name="step-3---create-and-configure-testvnet4"></a>Paso 3: crear y configurar TestVNet4

Una vez que haya configurado TestVNet1, crear TestVNet4. Siga los pasos siguientes, reemplazando los valores con su propio cuando sea necesario. Este paso puede realizarse en la misma sesión de PowerShell porque está en la misma suscripción.

1. Declarar las variables

    Asegúrese de reemplazar los valores por los que desea usar para su configuración.

        $RG4 = "TestRG4"
        $Location4 = "West US"
        $VnetName4 = "TestVNet4"
        $FESubName4 = "FrontEnd"
        $BESubName4 = "Backend"
        $GWSubName4 = "GatewaySubnet"
        $VnetPrefix41 = "10.41.0.0/16"
        $VnetPrefix42 = "10.42.0.0/16"
        $FESubPrefix4 = "10.41.0.0/24"
        $BESubPrefix4 = "10.42.0.0/24"
        $GWSubPrefix4 = "10.42.255.0/27"
        $DNS4 = "8.8.8.8"
        $GWName4 = "VNet4GW"
        $GWIPName4 = "VNet4GWIP"
        $GWIPconfName4 = "gwipconf4"
        $Connection41 = "VNet4toVNet1"

    Antes de continuar, asegúrese de que esté conectado a 1 de suscripción.

2. Crear un nuevo grupo de recursos

        New-AzureRmResourceGroup -Name $RG4 -Location $Location4

3. Crear las configuraciones de subred para TestVNet4

        $fesub4 = New-AzureRmVirtualNetworkSubnetConfig -Name $FESubName4 -AddressPrefix $FESubPrefix4
        $besub4 = New-AzureRmVirtualNetworkSubnetConfig -Name $BESubName4 -AddressPrefix $BESubPrefix4
        $gwsub4 = New-AzureRmVirtualNetworkSubnetConfig -Name $GWSubName4 -AddressPrefix $GWSubPrefix4

4. Crear TestVNet4

        New-AzureRmVirtualNetwork -Name $VnetName4 -ResourceGroupName $RG4 `
        -Location $Location4 -AddressPrefix $VnetPrefix41,$VnetPrefix42 -Subnet $fesub4,$besub4,$gwsub4

5. Solicitar una dirección IP pública

        $gwpip4 = New-AzureRmPublicIpAddress -Name $GWIPName4 -ResourceGroupName $RG4 `
        -Location $Location4 -AllocationMethod Dynamic

6. Crear la configuración de puerta de enlace

        $vnet4 = Get-AzureRmVirtualNetwork -Name $VnetName4 -ResourceGroupName $RG4
        $subnet4 = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet4
        $gwipconf4 = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GWIPconfName4 -Subnet $subnet4 -PublicIpAddress $gwpip4

7. Crear la puerta de enlace TestVNet4

        New-AzureRmVirtualNetworkGateway -Name $GWName4 -ResourceGroupName $RG4 `
        -Location $Location4 -IpConfigurations $gwipconf4 -GatewayType Vpn `
        -VpnType RouteBased -GatewaySku Standard

### <a name="step-4---connect-the-gateways"></a>Paso 4: conectar las puertas de enlace

1. Obtener dos puertas de enlace de red virtual

    En este ejemplo, porque las puertas de enlace están en la misma suscripción, puede completar este paso en la misma sesión de PowerShell.

        $vnet1gw = Get-AzureRmVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1
        $vnet4gw = Get-AzureRmVirtualNetworkGateway -Name $GWName4 -ResourceGroupName $RG4


2. Crear la TestVNet1 a la conexión de TestVNet4

    En este paso, cree la conexión de TestVNet1 a TestVNet4. Verá una clave compartida que se hace referencia en los ejemplos. Puede usar sus propios valores de la clave compartida. Lo importante es que debe coincidir con la clave compartida para ambas conexiones. Crear una conexión puede tardar unos minutos en completarse.

        New-AzureRmVirtualNetworkGatewayConnection -Name $Connection14 -ResourceGroupName $RG1 `
        -VirtualNetworkGateway1 $vnet1gw -VirtualNetworkGateway2 $vnet4gw -Location $Location1 `
        -ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3'

3. Crear la TestVNet4 a la conexión de TestVNet1

    Este paso es similar a la anterior, excepto que va a crear la conexión de TestVNet4 a TestVNet1. Asegúrese de que coinciden con las claves compartidas.

        New-AzureRmVirtualNetworkGatewayConnection -Name $Connection41 -ResourceGroupName $RG4 `
        -VirtualNetworkGateway1 $vnet4gw -VirtualNetworkGateway2 $vnet1gw -Location $Location4 `
        -ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3'

    Debe establecer la conexión después de unos minutos.

4. Compruebe su conexión. Consulte la sección [cómo comprobar la conexión](#verify).


## <a name="difsub"></a>Cómo conectar VNets que están en varias suscripciones


![diagrama de V2V](./media/vpn-gateway-vnet-vnet-rm-ps/v2vdiffsub.png)

En este escenario, se conecte TestVNet1 y TestVNet5. TestVNet1 y TestVNet5 residen en una suscripción diferente. Los pasos para esta configuración agregan una conexión de VNet a VNet adicional para conectar TestVNet1 a TestVNet5. 

La diferencia aquí es que algunos de los pasos de configuración deben llevarse a cabo en una sesión PowerShell independiente en el contexto de la suscripción a la segunda. Especialmente cuando dos suscripciones pertenezcan a organizaciones diferentes. 

Siga las instrucciones de los pasos anteriores enumerados anteriormente. Debe completar el [paso 1](#Step1) y [2 de paso](#Step2) para crear y configurar TestVNet1 y la puerta de enlace VPN para TestVNet1. Cuando haya completado el paso 1 y 2 de paso, continúe con el paso 5 para crear TestVNet5.

### <a name="step-5---verify-the-additional-ip-address-ranges"></a>Paso 5: comprobar los intervalos de direcciones IP adicionales

Es importante para asegurarse de que el espacio de direcciones IP de la nueva red virtual TestVNet5, no se superpone con un VNet rangos o intervalos de puerta de enlace de red local. 

En este ejemplo, las redes virtuales pueden pertenecer a diferentes organizaciones. Para este ejercicio, puede usar los siguientes valores para el TestVNet5:

**Valores TestVNet5:**

- Nombre de VNet: TestVNet5
- Grupo de recursos: TestRG5
- Ubicación: Japón oriental
- TestVNet5: 10.51.0.0/16 & 10.52.0.0/16
- Front-end: 10.51.0.0/24
- Back-end: 10.52.0.0/24
- GatewaySubnet: 10.52.255.0.0/27
- Servidor DNS: 8.8.8.8
- GatewayName: VNet5GW
- IP pública: VNet5GWIP
- VPNType: RouteBased
- Conexión: VNet5toVNet1
- ConnectionType: VNet2VNet

**Valores adicionales para TestVNet1:**

- Conexión: VNet1toVNet5


### <a name="step-6---create-and-configure-testvnet5"></a>Paso 6: crear y configurar TestVNet5

Este paso debe realizarse en el contexto de la nueva suscripción. Este elemento puede realizarse por el administrador en una organización distinta que es el propietario de la suscripción.

1. Declarar las variables

    Asegúrese de reemplazar los valores por los que desea usar para su configuración.

        $Sub5 = "Replace_With_the_New_Subcription_Name"
        $RG5 = "TestRG5"
        $Location5 = "Japan East"
        $VnetName5 = "TestVNet5"
        $FESubName5 = "FrontEnd"
        $BESubName5 = "Backend"
        $GWSubName5 = "GatewaySubnet"
        $VnetPrefix51 = "10.51.0.0/16"
        $VnetPrefix52 = "10.52.0.0/16"
        $FESubPrefix5 = "10.51.0.0/24"
        $BESubPrefix5 = "10.52.0.0/24"
        $GWSubPrefix5 = "10.52.255.0/27"
        $DNS5 = "8.8.8.8"
        $GWName5 = "VNet5GW"
        $GWIPName5 = "VNet5GWIP"
        $GWIPconfName5 = "gwipconf5"
        $Connection51 = "VNet5toVNet1"

2. Conectarse a 5 de la suscripción

    Abra la consola de PowerShell y conectarse a su cuenta. Use el siguiente ejemplo para ayudarle a conectarse:

        Login-AzureRmAccount

    Compruebe las suscripciones para la cuenta.

        Get-AzureRmSubscription 

    Especifique la suscripción que desea usar.

        Select-AzureRmSubscription -SubscriptionName $Sub5

3. Crear un nuevo grupo de recursos

        New-AzureRmResourceGroup -Name $RG5 -Location $Location5

4. Crear las configuraciones de subred para TestVNet4
    
        $fesub5 = New-AzureRmVirtualNetworkSubnetConfig -Name $FESubName5 -AddressPrefix $FESubPrefix5
        $besub5 = New-AzureRmVirtualNetworkSubnetConfig -Name $BESubName5 -AddressPrefix $BESubPrefix5
        $gwsub5 = New-AzureRmVirtualNetworkSubnetConfig -Name $GWSubName5 -AddressPrefix $GWSubPrefix5

5. Crear TestVNet5

        New-AzureRmVirtualNetwork -Name $VnetName5 -ResourceGroupName $RG5 -Location $Location5 `
        -AddressPrefix $VnetPrefix51,$VnetPrefix52 -Subnet $fesub5,$besub5,$gwsub5

6. Solicitar una dirección IP pública

        $gwpip5 = New-AzureRmPublicIpAddress -Name $GWIPName5 -ResourceGroupName $RG5 `
        -Location $Location5 -AllocationMethod Dynamic


7. Crear la configuración de puerta de enlace

        $vnet5 = Get-AzureRmVirtualNetwork -Name $VnetName5 -ResourceGroupName $RG5
        $subnet5  = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet5
        $gwipconf5 = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GWIPconfName5 -Subnet $subnet5 -PublicIpAddress $gwpip5

8. Crear la puerta de enlace TestVNet5

        New-AzureRmVirtualNetworkGateway -Name $GWName5 -ResourceGroupName $RG5 -Location $Location5 `
        -IpConfigurations $gwipconf5 -GatewayType Vpn -VpnType RouteBased -GatewaySku Standard

### <a name="step-7---connecting-the-gateways"></a>Paso 7: conectar las puertas de enlace

En este ejemplo, porque las puertas de enlace están en varias suscripciones, nos hemos dividir este paso en dos sesiones de PowerShell que esté marcadas como [suscripción 1] y [suscripción 5].

1. **[Suscripción 1]** Obtener la puerta de enlace de red virtual de suscripción 1

    Asegúrese de que iniciar sesión y conectarse a 1 de suscripción.

        $vnet1gw = Get-AzureRmVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1

    Copiar el resultado de los siguientes elementos y enviar estos al administrador del 5 de suscripción a través de correo electrónico u otro método.

        $vnet1gw.Name
        $vnet1gw.Id

    Estos dos elementos tendrá valores similares a los resultados de ejemplo siguientes:

        PS D:\> $vnet1gw.Name
        VNet1GW
        PS D:\> $vnet1gw.Id
        /subscriptions/b636ca99-6f88-4df4-a7c3-2f8dc4545509/resourceGroupsTestRG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW

2. **[Suscripción 5]** Obtener la puerta de enlace de red virtual de suscripción 5

    Asegúrese de que iniciar sesión y conectarse a 5 de la suscripción.

        $vnet5gw = Get-AzureRmVirtualNetworkGateway -Name $GWName5 -ResourceGroupName $RG5

    Copiar el resultado de los siguientes elementos y enviar estos al administrador del 1 de suscripción a través de correo electrónico u otro método.

        $vnet5gw.Name
        $vnet5gw.Id

    Estos dos elementos tendrá valores similares a los resultados de ejemplo siguientes:

        PS C:\> $vnet5gw.Name
        VNet5GW
        PS C:\> $vnet5gw.Id
        /subscriptions/66c8e4f1-ecd6-47ed-9de7-7e530de23994/resourceGroups/TestRG5/providers/Microsoft.Network/virtualNetworkGateways/VNet5GW

3. **[Suscripción 1]** Crear la TestVNet1 a la conexión de TestVNet5

    En este paso, cree la conexión de TestVNet1 a TestVNet5. La diferencia es que vnet5gw $ no se puede obtener directamente porque está en una suscripción diferente. Debe crear un nuevo objeto de PowerShell con los valores que se comunican de suscripción 1 en los pasos anteriores. Use el ejemplo siguiente. Reemplace el nombre, el identificador y la clave compartida con sus propios valores. Lo importante es que debe coincidir con la clave compartida para ambas conexiones. Crear una conexión puede tardar unos minutos en completarse.

    Asegúrese de que se conecte a 1 de suscripción. 
    
        $vnet5gw = New-Object Microsoft.Azure.Commands.Network.Models.PSVirtualNetworkGateway
        $vnet5gw.Name = "VNet5GW"
        $vnet5gw.Id   = "/subscriptions/66c8e4f1-ecd6-47ed-9de7-7e530de23994/resourceGroups/TestRG5/providers/Microsoft.Network/virtualNetworkGateways/VNet5GW"
        $Connection15 = "VNet1toVNet5"
        New-AzureRmVirtualNetworkGatewayConnection -Name $Connection15 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -VirtualNetworkGateway2 $vnet5gw -Location $Location1 -ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3'

4. **[Suscripción 5]** Crear la TestVNet5 a la conexión de TestVNet1

    Este paso es similar a la anterior, excepto que va a crear la conexión de TestVNet5 a TestVNet1. El mismo proceso de creación de un objeto de PowerShell basándose en los valores que se obtienen de suscripción 1 se aplica también aquí. En este paso, asegúrese de que coinciden con las claves compartidas.

    Asegúrese de que se conecte a 5 de la suscripción.

        $vnet1gw = New-Object Microsoft.Azure.Commands.Network.Models.PSVirtualNetworkGateway
        $vnet1gw.Name = "VNet1GW"
        $vnet1gw.Id = "/subscriptions/b636ca99-6f88-4df4-a7c3-2f8dc4545509/resourceGroups/TestRG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW "
        New-AzureRmVirtualNetworkGatewayConnection -Name $Connection51 -ResourceGroupName $RG5 -VirtualNetworkGateway1 $vnet5gw -VirtualNetworkGateway2 $vnet1gw -Location $Location5 -ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3'

## <a name="verify"></a>Cómo comprobar una conexión


[AZURE.INCLUDE [vpn-gateway-no-nsg-include](../../includes/vpn-gateway-no-nsg-include.md)]


[AZURE.INCLUDE [verify connection powershell](../../includes/vpn-gateway-verify-connection-ps-rm-include.md)] 


## <a name="next-steps"></a>Pasos siguientes

- Una vez finalizada la conexión, puede agregar máquinas virtuales con sus redes virtuales. Consulte [crear una máquina Virtual](../virtual-machines/virtual-machines-windows-hero-tutorial.md) para los pasos.
- Para obtener información acerca de BGP, vea la [Información general de BGP](vpn-gateway-bgp-overview.md) y [cómo configurar BGP](vpn-gateway-bgp-resource-manager-ps.md). 

