<properties
   pageTitle="Crear una red virtual con una conexión de puerta de enlace VPN de sitio a sitio con el portal clásico Azure | Microsoft Azure"
   description="Crear una VNet con una conexión de puerta de enlace de VPN S2S para entre las instalaciones y configuraciones híbridas utiliza el modelo de implementación clásico."
   services="vpn-gateway"
   documentationCenter=""
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-service-management"/>

<tags
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/14/2016"
   ms.author="cherylmc"/>

# <a name="create-a-vnet-with-a-site-to-site-connection-using-the-azure-classic-portal"></a>Crear una VNet con una conexión de sitio a sitio con el portal de clásico de Azure

> [AZURE.SELECTOR]
- [Administrador de recursos - Portal de Azure](vpn-gateway-howto-site-to-site-resource-manager-portal.md)
- [Administrador de recursos - PowerShell](vpn-gateway-create-site-to-site-rm-powershell.md)
- [Clásico: Portal clásica](vpn-gateway-site-to-site-create.md)

En este artículo le guiará a través de la creación de una red virtual y una conexión de puerta de enlace VPN de sitio a sitio a su red local mediante el modelo de implementación clásico y el portal de clásico. Conexiones de sitio a sitio pueden usarse para entre local y híbrido configuraciones.

![Diagrama de sitio a otro] (./media/vpn-gateway-site-to-site-create/site2site.png "sitio de sitio")


### <a name="deployment-models-and-methods-for-site-to-site-connections"></a>Modelos de implementación y métodos para las conexiones de sitio a otro

[AZURE.INCLUDE [deployment models](../../includes/vpn-gateway-deployment-models-include.md)] 

La siguiente tabla muestra los métodos para las configuraciones de sitio a sitio y modelos de implementación disponible actualmente. Cuando esté disponible un artículo con pasos de configuración, nos vincularlo directamente desde esta tabla.

[AZURE.INCLUDE [vpn-gateway-table-site-to-site-table](../../includes/vpn-gateway-table-site-to-site-include.md)]

#### <a name="additional-configurations"></a>Configuraciones adicionales 

Si desea conectar VNets, vea [Configurar una conexión de VNet a VNet para el modelo de implementación clásico](virtual-networks-configure-vnet-to-vnet-connection.md). Si desea agregar una conexión de sitio a sitio a una VNet que ya tiene una conexión, vea [Agregar una conexión de S2S a una VNet con una conexión de puerta de enlace VPN existente](vpn-gateway-multi-site.md).
 
## <a name="before-you-begin"></a>Antes de empezar

Compruebe que tiene los siguientes elementos antes de comenzar la configuración.

- Un dispositivo VPN compatible y alguien configurarlo. Consulte [acerca de los dispositivos VPN](vpn-gateway-about-vpn-devices.md). Si no está familiarizado con la configuración de su dispositivo VPN o no están familiarizados con la dirección IP rangos que se encuentran en su local en la configuración de red, necesita coordinar con alguien que puede proporcionar los detalles por usted.

- Una dirección IP pública orientada externamente para su dispositivo VPN. Esta dirección IP no se encuentra detrás de un NAT.

- Una suscripción de Azure. Si todavía no tiene una suscripción de Azure, puede activar los [beneficios de suscriptor MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) o signo hacia arriba para obtener una [cuenta gratuita](https://azure.microsoft.com/pricing/free-trial/).


## <a name="CreateVNet"></a>Crear su red virtual

1. Inicie sesión en el [portal de clásico de Azure](https://manage.windowsazure.com/).

2. En la esquina inferior izquierda de la pantalla, haga clic en **nuevo**. En el panel de navegación, haga clic en **Servicios de red**y, a continuación, haga clic en **Una red Virtual**. Haga clic en **Crear personalizado** para iniciar al Asistente de configuración.

3. Para crear su VNet, escriba los valores de configuración en las páginas siguientes:

## <a name="Details"></a>Página de detalles de la red virtual

Escriba la información siguiente:

- **Nombre**: el nombre de su red virtual. Por ejemplo, *EastUSVNet*. Deberá usar este nombre de red virtual cuando se implementación las instancias de máquinas virtuales y PaaS, por lo que no se desea que el nombre demasiado complicado.
- **Ubicación**: la ubicación directamente relacionada con la ubicación física (región) donde desea que los recursos (VM) resida. Por ejemplo, si desea que las máquinas virtuales que se implementación en esta red virtual que se encuentra en *Estados Unidos oriental*, seleccione esa ubicación. No puede cambiar la región asociada a su red virtual después de crearlo.

## <a name="DNS"></a>Página de conectividad VPN y los servidores DNS

Escriba la información siguiente y, a continuación, haga clic en la flecha siguiente en la esquina inferior derecha.

- **Los servidores DNS**: escriba el nombre del servidor DNS y la dirección IP o seleccione un servidor DNS previamente registrado en el menú contextual. Esta configuración no crear un servidor DNS. Permite especificar los servidores DNS que desea usar para la resolución de nombre para esta red virtual.
- **Configurar VPN de sitio a sitio**: seleccione la casilla de verificación para **Configurar una VPN de sitio a otro**.
- **Red local**: una red local representa su ubicación física local. Puede seleccionar una red local que ya ha creado o puede crear una nueva red local. Sin embargo, si selecciona una red local que creó anteriormente, vaya a la página de configuración de **Redes locales** y compruebe que la dirección de IP de dispositivo VPN (dirección IPv4 opuesta pública) para el dispositivo VPN es precisa.

## <a name="Connectivity"></a>Página de conectividad de sitio a otro

Si está creando una nueva red local, verá la página **Conectividad de sitio a otro** . Si desea usar una red local que creó anteriormente, esta página no aparecerá en el asistente y puede pasar a la siguiente sección.

Escriba la información siguiente y, a continuación, haga clic en la flecha siguiente.

-   **Nombre**: el nombre que desea llamar su local (local) el sitio de red.
-   **Dirección IP del dispositivo VPN**: la dirección IPv4 opuesta pública del dispositivo VPN local que usa para conectarse a Azure. No se encuentra el dispositivo VPN detrás de un NAT.
-   **Espacio de direcciones**: incluir iniciar IP y CIDR (recuento de dirección). Especifique la dirección range(s) que desea que se envíen a través de la puerta de enlace de red virtual a su ubicación local en local. Si la dirección IP de destino se encuentra dentro de los rangos que especifique aquí, se dirige a través de la puerta de enlace de red virtual.
-   **Agregar espacio de direcciones**: si tiene varios intervalos de direcciones que desee que se envíen a través de la puerta de enlace de red virtual, especifique cada intervalo de direcciones adicionales. Puede agregar o quitar intervalos más adelante en la página de la **Red Local** .

## <a name="Address"></a>Página de espacios de dirección de una red virtual

Especifique el intervalo de direcciones que desee usar para su red virtual. Son las direcciones IP dinámicas (DIP) que se asignará a las máquinas virtuales y otras instancias de la función se implementación en esta red virtual.

Es especialmente importante seleccionar un rango que no se superpone con cualquiera de los rangos que se usan para su red local. Debe coordinar con el Administrador de red. El Administrador de red puede necesitar dividir fuera de un intervalo de direcciones IP de su espacio de direcciones de red local para su uso para su red virtual.

Escriba la información siguiente y, a continuación, haga clic en la marca de verificación en la esquina inferior derecha para configurar su red.

- **Espacio de direcciones**: incluir el inicio de IP y el recuento de dirección. Compruebe que los espacios de direcciones que especifique no superponen a cualquiera de los espacios de direcciones que tiene en su red local.
- **Agregar subred**: incluir IP inicial y el recuento de dirección. Subredes adicionales no son obligatorios, pero desea crear una subred independiente para máquinas virtuales que tendrán DIP estático. O bien, es posible que desee tener sus máquinas virtuales en una subred independiente de las instancias del rol.
- **Agregar subred de puerta de enlace**: haga clic para agregar la subred de puerta de enlace. La subred de puerta de enlace se usa solamente para la puerta de enlace de red virtual y se requiere para esta configuración.

Haga clic en la marca de verificación en la parte inferior de la página y su red virtual comenzará a crear. Cuando finalice, verá **que creado** aparece en el **estado** en la página de **redes** en el Portal de clásico de Azure. Una vez creado el VNet, a continuación, puede configurar la puerta de enlace de red virtual.

[AZURE.INCLUDE [vpn-gateway-no-nsg](../../includes/vpn-gateway-no-nsg-include.md)] 

## <a name="VNetGateway"></a>Configurar la puerta de enlace de red virtual

Configurar la puerta de enlace de red virtual para crear una conexión segura de sitio a otro. Vea [Configurar una puerta de enlace de red virtual en el portal de clásico de Azure](vpn-gateway-configure-vpn-gateway-mp.md).

## <a name="next-steps"></a>Pasos siguientes

Una vez finalizada la conexión, puede agregar máquinas virtuales con sus redes virtuales. Consulte la documentación de [máquinas virtuales de Windows](https://azure.microsoft.com/documentation/services/virtual-machines/) para obtener más información.
