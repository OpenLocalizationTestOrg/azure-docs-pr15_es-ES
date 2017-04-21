<properties
   pageTitle="Configurar una red Virtual y la puerta de enlace para ExpressRoute en el portal de clásico | Microsoft Azure"
   description="En este artículo le guiará a través de configuración de una red virtual para ExpressRoute con el modelo de implementación clásico y el portal clásico."
   documentationCenter="na"
   services="expressroute"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-service-management"/>

<tags 
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="article" 
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services" 
   ms.date="09/20/2016"
   ms.author="cherylmc"/>

# <a name="create-a-virtual-network-for-expressroute-in-the-classic-portal"></a>Crear una red Virtual para ExpressRoute en el portal de clásico

Los pasos de este artículo le guiará a través de la configuración de una red virtual y una puerta de enlace de red virtual para su uso con ExpressRoute mediante el modelo de implementación clásico y el portal de clásico.

Si está buscando las instrucciones para el modelo de implementación de administrador de recursos, puede usar los siguientes artículos: [crear una red virtual con PowerShell](../virtual-network/virtual-networks-create-vnet-arm-ps.md) y [Agregar una puerta de enlace VPN un VNet Administrador de recursos para ExpressRoute](expressroute-howto-add-gateway-resource-manager.md).

**Acerca de los modelos de implementación de Azure**

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)] 

## <a name="create-a-classic-vnet-and-gateway"></a>Crear una puerta de enlace y un VNet clásico

Los pasos siguientes crean un VNet clásica y una puerta de enlace de red virtual. Si ya tiene un VNet clásica, consulte la sección [configurar un VNet clásico existente](#config) en este artículo.

1. Inicie sesión en el [portal de clásico de Azure](http://manage.windowsazure.com).

2. En la esquina inferior izquierda de la pantalla, haga clic en **nuevo**. En el panel de navegación, haga clic en **Servicios de red**y, a continuación, haga clic en **Una red Virtual**. Haga clic en **Crear personalizado** para iniciar al Asistente de configuración.

3. En la página **Detalles de la red Virtual** , escriba lo siguiente:

    - **Nombre** : nombre de la red virtual. Deberá usar este nombre de red virtual cuando se implementación las instancias de máquinas virtuales y PaaS, por lo que no se desea que el nombre demasiado complicado.
    - **Ubicación** : la ubicación directamente relacionada con la ubicación física (región) donde desea que los recursos (VM) resida. Por ejemplo, si desea que las máquinas virtuales que se implementación en esta red virtual que se encuentra en Estados Unidos oriental, seleccione esa ubicación. No puede cambiar la región asociada a su red virtual después de crearlo.

4. En la página **conectividad VPN y los servidores DNS** , escriba la información siguiente y, a continuación, haga clic en la flecha siguiente en la esquina inferior derecha. 

    - **Los servidores DNS** , escriba el nombre del servidor DNS y la dirección IP, o seleccione un servidor DNS previamente registrado en el menú contextual. Esta configuración no crear un servidor DNS. Permite especificar los servidores DNS que desea usar para la resolución de nombre para esta red virtual.
    - **Conectividad de sitio a sitio** : seleccione la casilla de verificación para **Configurar una VPN de sitio a otro**.
    - **ExpressRoute** : Marque la casilla de verificación **Usar ExpressRoute**. Esta opción solo aparece si seleccionó **Configurar una VPN de sitio a otro**.
    - **Red local** , se le pide que tiene un sitio de red local para ExpressRoute. Sin embargo, en el caso de una conexión ExpressRoute, se omitirá los prefijos de dirección especificados para el sitio de la red local. En su lugar, se usará el prefijos de dirección anunciados a Microsoft a través del circuito ExpressRoute para el enrutamiento.<BR>Si ya tiene una red local creada para la conexión de ExpressRoute, puede seleccionar en la lista desplegable. Si no es así, seleccione **especificar una nueva red Local**.

5. La página **conectividad de sitio a sitio** aparece si seleccionó para especificar una nueva red local en el paso anterior. Para configurar su red local, escriba la información siguiente y, a continuación, haga clic en la flecha siguiente. 

    - **Nombre** : el nombre que desea llamar su local (local) el sitio de red.
    - **Espacio de direcciones** - incluidos IP inicial y CIDR (recuento de dirección) Puede especificar cualquier intervalo de direcciones, mientras no se superpone con el intervalo de direcciones para su red virtual. Normalmente, esto debería especificar los intervalos de direcciones para sus redes local, pero en el caso de ExpressRoute, no se usa esta configuración. Sin embargo, esta configuración es necesaria para crear la red local cuando se usa el portal clásico.
    - **Agregar espacio de direcciones** : esta configuración no es relevante para ExpressRoute.


6. En la página **Virtual espacios de direcciones de red** , escriba la información siguiente y, a continuación, haga clic en la marca de verificación en la esquina inferior derecha para configurar su red. 

    - **Espacio de direcciones** - incluido el recuento IP y la dirección de inicio Compruebe que los espacios de direcciones que especifique no superponen a cualquiera de los espacios de direcciones que tiene en su red local.
    - **Agregar subred** - incluidos IP inicial y el recuento de dirección Subredes adicionales no son necesarios.
    - **Agregar subred de puerta de enlace** : haga clic para agregar la subred de puerta de enlace. La subred de puerta de enlace se usa solamente para la puerta de enlace de red virtual y se requiere para esta configuración.<BR>La subred de puerta de enlace CIDR (recuento de dirección) para ExpressRoute debe ser /28 o mayor (/ 27/26 etcetera.). Esto permite suficientes direcciones IP de subred para permitir que la configuración para que funcione. En el portal clásico, si seleccionó la casilla de verificación Usar ExpressRoute, el portal especifica una subred de puerta de enlace con /28.  No se puede ajustar el recuento de dirección CIDR en el portal de clásico. La subred de puerta de enlace se mostrarán como **puerta de enlace** en el portal clásico, aunque el nombre real de la subred de puerta de enlace que se crea es realmente **GatewaySubnet**. Puede ver este nombre con PowerShell o en el portal de Azure.

7. Haga clic en la marca de verificación en la parte inferior de la página y su red virtual comenzará a crear. Cuando finalice, verá **que creado** aparece en el **estado** en la página de **redes** en el portal de clásico.

## <a name="gw"></a>Crear la puerta de enlace

1. En la página de **redes** , haga clic en la red virtual que acaba de crear, haga clic en el **panel** en la parte superior de la página.

2. En la parte inferior de la página de **panel** , haga clic en **Crear la puerta de enlace** y seleccione **Enrutamiento dinámico**. Haga clic en **Sí** para confirmar que desea crear una puerta de enlace.

3. Cuando comienza a crear la puerta de enlace, verá un mensaje informarle de que la puerta de enlace se ha iniciado. Puede tardar hasta 45 minutos para crear la puerta de enlace.

4. Vincular su red a un circuito. Siga las instrucciones en el artículo [cómo vincular VNets a circuitos ExpressRoute](expressroute-howto-linkvnet-classic.md).

## <a name="config"></a>Configurar un VNet clásico existente para ExpressRoute

Si ya tiene un VNet clásico, puede configurarlo para conectarse a ExpressRoute en el portal de clásico. La configuración es la misma que las secciones anteriores, así que lea esas secciones para familiarizarse con la configuración necesaria. Si desea crear una conexión de coexistentes ExpressRoute o a sitios, consulte [este artículo](expressroute-howto-coexist-classic.md) para ver los pasos. Son diferentes de los pasos de este artículo.
 
1. Deberá crear la red local antes de actualizar el resto de la configuración de VNet. Para crear una nueva red local, que es necesaria cuando se configura ExpressRoute a través del portal clásico, haga clic en **nuevo** **>** **Servicios de red** **>** **Red Virtual** **>** **Agregar de red local**. Siga los pasos del Asistente para crear la red local.

2. Use **Configurar** página para actualizar el resto de la configuración de su VNet y asociar la VNet a la red local.

3. Después de configurar la configuración, vaya a la sección [crear la puerta de enlace](#gw) de este artículo para crear la puerta de enlace.


## <a name="next-steps"></a>Pasos siguientes

- Si desea agregar máquinas virtuales a su red virtual, vea [rutas de aprendizaje de máquinas virtuales de Windows](https://azure.microsoft.com/documentation/learning-paths/virtual-machines/).
- Si desea obtener más información sobre ExpressRoute, vea la [Información general de ExpressRoute](expressroute-introduction.md).


 
