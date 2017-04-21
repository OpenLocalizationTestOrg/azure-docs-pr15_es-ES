<properties
   pageTitle="Conectar VNets utilizando el modelo de implementación de administrador de recursos y el portal de Azure | Microsoft Azure"
   description="Crear una conexión VPN de puerta de enlace entre VNets mediante el Administrador de recursos y el portal de Azure."
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
   ms.date="10/25/2016"
   ms.author="cherylmc" />

# <a name="configure-a-vnet-to-vnet-connection-using-the-azure-portal"></a>Configurar una conexión de VNet a VNet con el portal de Azure

> [AZURE.SELECTOR]
- [Administrador de recursos - Portal de Azure](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)
- [Administrador de recursos - PowerShell](vpn-gateway-vnet-vnet-rm-ps.md)
- [Clásico: Portal clásica](virtual-networks-configure-vnet-to-vnet-connection.md)


En este artículo le guiará por los pasos para crear una conexión entre VNets en el modelo de implementación de administrador de recursos con el portal de Azure y de puerta de enlace VPN.

Al usar el portal de Azure para conectar redes virtuales, la VNets debe estar en la misma suscripción. Si sus redes virtuales están en varias suscripciones, puede conectarlos con los pasos de [PowerShell](vpn-gateway-vnet-vnet-rm-ps.md) .

![diagrama de V2V](./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/v2vrmps.png)

### <a name="deployment-models-and-methods-for-vnet-to-vnet-connections"></a>Modelos de implementación y métodos para las conexiones de VNet a VNet

[AZURE.INCLUDE [deployment models](../../includes/vpn-gateway-deployment-models-include.md)]

La siguiente tabla muestra los métodos para las configuraciones de VNet a VNet y modelos de implementación disponible actualmente. Cuando esté disponible un artículo con pasos de configuración, nos vincularlo directamente desde esta tabla.

[AZURE.INCLUDE [vpn-gateway-table-vnet-vnet](../../includes/vpn-gateway-table-vnet-to-vnet-include.md)]

#### <a name="vnet-peering"></a>VNet interconexión

[AZURE.INCLUDE [vpn-gateway-vnetpeeringlink](../../includes/vpn-gateway-vnetpeeringlink-include.md)]

## <a name="about-vnet-to-vnet-connections"></a>Acerca de las conexiones de VNet a VNet

Conectarse a una red virtual a otra red virtual (VNet a VNet) es similar a una VNet a conectarse a una ubicación del sitio local. Ambos tipos de conectividad usan una puerta de enlace VPN de Azure para proporcionar un túnel seguro mediante IPsec/IKE. Puede ser la VNets que conectar en diferentes regiones o en diferentes suscripciones.

Incluso puede combinar VNet a VNet comunicación con configuraciones de varios sitios. Permite establecer topologías de red que se combinan entre local conectividad con la conectividad de red entre virtual, tal como se muestra en el diagrama siguiente:

![Acerca de las conexiones] (./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/aboutconnections.png "Acerca de las conexiones")

### <a name="why-connect-virtual-networks"></a>¿Por qué conectar redes virtuales?

Desea conectar redes virtuales por los siguientes motivos:

- **Combinación cruzada redundancia de región geográfica y presencia geo**
    - Puede configurar su propio geo replicación o sincronización con conectividad segura sin saliendo extremos a través de Internet.
    - Con el administrador del tráfico de Azure y equilibrador de carga, puede configurar altamente disponible carga de trabajo con geo redundancia en diferentes regiones de Azure. Un ejemplo de importante es configurar SQL siempre en con grupos de disponibilidad reparte en varias áreas de Azure.

- **Aplicaciones de varios niveles regionales con aislamiento o límite administrativo**
    - Dentro de la misma región, puede configurar las aplicaciones de varios niveles con varias redes virtuales conectadas debido a aislamiento o requisitos administrativos.

Para obtener más información acerca de las conexiones de VNet a VNet, consulte las [preguntas más frecuentes de VNet a VNet](#faq) al final de este artículo.

### <a name="values"></a>Configuración de ejemplo

Cuando usa estos pasos como ejercicio, puede usar los valores de configuración de ejemplo. Para fines, por ejemplo, usamos varios espacios de direcciones para cada VNet. Sin embargo, las configuraciones de VNet a VNet no requieren varios espacios de direcciones.

**Valores TestVNet1:**

- Nombre de VNet: TestVNet1
- Espacio de direcciones: 10.11.0.0/16
    - Nombre de subred: front-end
    - Intervalo de direcciones de subred: 10.11.0.0/24
- Grupo de recursos: TestRG1
- Ubicación: Estados Unidos oriental
- Espacio de direcciones: 10.12.0.0/16
    - Nombre de subred: back-end
    - Intervalo de direcciones de subred: 10.12.0.0/24
- Nombre de puerta de enlace subred: GatewaySubnet (Esto le autorrelleno en el portal)
    - Intervalo de direcciones de puerta de enlace subred: 10.11.255.0/27
- Servidor DNS: Usar la dirección IP del servidor DNS
- Nombre de puerta de enlace de red virtual: TestVNet1GW
- Tipo de puerta de enlace: VPN
- Tipo de VPN: basado en la ruta
- SKU: Seleccione el SKU de puerta de enlace que desea usar
- Nombre de dirección IP público: TestVNet1GWIP
- Valores de la conexión:
    - Nombre: TestVNet1toTestVNet4
    - Clave compartida: puede crear la clave compartida. En este ejemplo, usaremos abc123. Lo importante es que cuando se crea la conexión entre el VNets, el valor debe coincidir con.

**Valores TestVNet4:**

- Nombre de VNet: TestVNet4
- Espacio de direcciones: 10.41.0.0/16
    - Nombre de subred: front-end
    - Intervalo de direcciones de subred: 10.41.0.0/24
- Grupo de recursos: TestRG1
- Ubicación: Estados Unidos oeste
- Espacio de direcciones: 10.42.0.0/16
    - Nombre de subred: back-end
    - Intervalo de direcciones de subred: 10.42.0.0/24
- Nombre de GatewaySubnet: GatewaySubnet (Esto le autorrelleno en el portal)
    - Intervalo de direcciones de GatewaySubnet: 10.41.255.0/27
- Servidor DNS: Usar la dirección IP del servidor DNS
- Nombre de puerta de enlace de red virtual: TestVNet4GW
- Tipo de puerta de enlace: VPN
- Tipo de VPN: basado en la ruta
- SKU: Seleccione el SKU de puerta de enlace que desea usar
- Nombre de dirección IP público: TestVNet4GWIP
- Valores de la conexión:
    - Nombre: TestVNet4toTestVNet1
    - Clave compartida: puede crear la clave compartida. En este ejemplo, usaremos abc123. Lo importante es que cuando se crea la conexión entre el VNets, el valor debe coincidir con.


## <a name="CreatVNet"></a>1. crear y configurar TestVNet1

Si ya tiene un VNet, compruebe que la configuración es compatible con el diseño de la puerta de enlace VPN. Preste atención particular a ninguna subred que pueden superponerse con otras redes. Si tiene superpuestos subredes, la conexión no funcionará correctamente. Si su VNet está configurado con la configuración correcta, puede empezar a los pasos de la sección [especificar un servidor DNS](#dns) .

### <a name="to-create-a-virtual-network"></a>Para crear una red virtual

[AZURE.INCLUDE [vpn-gateway-basic-vnet-rm-portal](../../includes/vpn-gateway-basic-vnet-rm-portal-include.md)]  

## <a name="subnets"></a>2. agregar espacio de direcciones adicionales y crear subredes

Puede agregar espacio de direcciones adicionales y crear subredes una vez que se ha creado su VNet.
[AZURE.INCLUDE [vpn-gateway-additional-address-space](../../includes/vpn-gateway-additional-address-space-include.md)]

## <a name="gatewaysubnet"></a>3. crear una subred de puerta de enlace

Antes de conectar su red virtual a una puerta de enlace, debe crear la subred de puerta de enlace de la red virtual al que desea conectarse. Si es posible, es mejor crear una subred de puerta de enlace con un bloque CIDR de /28 o /27 para proporcionar suficientes direcciones IP para adaptarse a requisitos adicionales configuración futura.

Si va a crear esta configuración como ejercicio, consulte esta [configuración de ejemplo](#values) al crear la subred de puerta de enlace.

[AZURE.INCLUDE [vpn-gateway-no-nsg](../../includes/vpn-gateway-no-nsg-include.md)]

### <a name="to-create-a-gateway-subnet"></a>Para crear una subred de puerta de enlace

[AZURE.INCLUDE [vpn-gateway-add-gwsubnet-rm-portal](../../includes/vpn-gateway-add-gwsubnet-rm-portal-include.md)]

## <a name="DNSServer"></a>4. Especifique un servidor DNS (opcional)

Si desea tener la resolución de nombres para máquinas virtuales que se implementan en su VNets, debe especificar un servidor DNS.

[AZURE.INCLUDE [vpn-gateway-add-dns-rm-portal](../../includes/vpn-gateway-add-dns-rm-portal-include.md)]


## <a name="VNetGateway"></a>5. crear una puerta de enlace de red virtual

En este paso, creará la puerta de enlace de red virtual para su VNet. Este paso puede tardar hasta 45 minutos en completarse. Si va a crear esta configuración como ejercicio, puede hacer referencia a la [configuración de ejemplo](#values).

### <a name="to-create-a-virtual-network-gateway"></a>Para crear una puerta de enlace de red virtual

[AZURE.INCLUDE [vpn-gateway-add-gw-rm-portal](../../includes/vpn-gateway-add-gw-rm-portal-include.md)]


## <a name="CreateTestVNet4"></a>6. crear y configurar TestVNet4

Una vez que haya configurado TestVNet1, crear TestVNet4, repita los pasos anteriores, reemplazando los valores con las de TestVNet4. No es necesario que esperar hasta que la puerta de enlace de red virtual para TestVNet1 ha terminado de crear antes de configurar TestVNet4. Si está utilizando sus propios valores, asegúrese de que no se superponen con los espacios de direcciones con cualquiera de los VNets que desea conectarse a.


## <a name="TestVNet1Connection"></a>7. configurar la conexión de TestVNet1

Cuando han completado las puertas de enlace de red virtual para TestVNet1 y TestVNet4, puede crear su red virtual conexiones de puerta de enlace. En esta sección, creará una conexión de VNet1 a VNet4.

1. En **todos los recursos**, vaya a la puerta de enlace de red virtual para su VNet. Por ejemplo, **TestVNet1GW**. Haga clic en **TestVNet1GW** para abrir la hoja de puerta de enlace de red virtual.

    ![Módulo de conexiones] (./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/settings_connection.png "Módulo de conexiones")

2. Haga clic en **+ Agregar** para abrir el módulo de **Agregar conexión** .

3. En el módulo de **Agregar conexión** , en el campo nombre, escriba un nombre para la conexión. Por ejemplo, **TestVNet1toTestVNet4**.

    ![Nombre de conexión] (./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/v1tov4.png "Nombre de conexión")

4. **Tipo de conexión**. en la lista desplegable, seleccione **VNet a VNet** .

5. El valor del campo **primera puerta de enlace de red virtual** se rellena automáticamente porque va a crear esta conexión de la puerta de enlace de red virtual especificada.

6. El campo de la **segunda puerta de enlace de red virtual** es la puerta de enlace de red virtual de la VNet que desea crear una conexión a. Haga clic en **Elegir otra puerta de enlace de red virtual** para abrir el módulo **Elija puerta de enlace de red virtual** .

    ![Agregar conexión] (./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/add_connection.png "Agregar una conexión")

7. Ver las puertas de enlace de red virtual que se indican en este módulo. Observe que se muestran solo una red virtual puertas de enlace en la suscripción. Si desea conectarse a una puerta de enlace de red virtual que no está en la suscripción, utilice el [artículo de PowerShell](vpn-gateway-vnet-vnet-rm-ps.md). 

8. Haga clic en la puerta de enlace de red virtual desea conectarse.
 
9. En el campo de **clave compartida** , escriba una clave compartida para la conexión. Puede generar o crear esta clave usted mismo. En una conexión de sitio a otro, la clave sería exactamente la misma para el dispositivo local y la conexión de la puerta de enlace de red virtual. El concepto es similar, excepto en lugar de conectarse a un dispositivo VPN, se está conectando a otra puerta de enlace de red virtual.

    ![Clave compartida] (./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/sharedkey.png "Clave compartida")

10. Haga clic en **Aceptar** en la parte inferior del módulo para guardar los cambios.

## <a name="TestVNet4Connection"></a>8. configurar la conexión de TestVNet4

A continuación, crear una conexión desde TestVNet4 a TestVNet1. Utilice el mismo método que se utiliza para crear la conexión de TestVNet1 a TestVNet4. Asegúrese de que usa la misma clave compartida.

## <a name="VerifyConnection"></a>9. Compruebe su conexión

Compruebe la conexión. Para cada puerta de enlace de red virtual, haga lo siguiente:

1. Busque el módulo de la puerta de enlace de red virtual. Por ejemplo, **TestVNet4GW**. 
2. En el módulo de puerta de enlace de red virtual, haga clic en **conexiones** para ver el módulo de conexiones para la puerta de enlace de red virtual.

Ver las conexiones y compruebe el estado. Una vez creada la conexión, verá **correcta** y **conectado** como los valores de estado.

![Se realizó correctamente] (./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/connected.png "Se realizó correctamente")

Hacer doble clic en cada conexión por separado para obtener más información acerca de la conexión.

![Essentials] (./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/essentials.png "Essentials")

## <a name="faq"></a>Preguntas más frecuentes de VNet a VNet

Ver los detalles de preguntas más frecuentes para obtener información adicional acerca de las conexiones de VNet a VNet.

[AZURE.INCLUDE [vpn-gateway-vnet-vnet-faq](../../includes/vpn-gateway-vnet-vnet-faq-include.md)]

## <a name="next-steps"></a>Pasos siguientes

Una vez finalizada la conexión, puede agregar máquinas virtuales con sus redes virtuales. Consulte [crear una máquina Virtual](../virtual-machines/virtual-machines-windows-hero-tutorial.md) para los pasos.
