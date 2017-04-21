<properties
   pageTitle="Cómo agregar varias conexiones de sitio a sitio de la puerta de enlace VPN a una red virtual para el modelo de implementación de administrador de recursos con el portal de Azure | Microsoft Azure"
   description="Agregar conexiones de múltiples sitios S2S a una puerta de enlace VPN que tiene una conexión existente"
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
   ms.date="10/10/2016"
   ms.author="cherylmc"/>



# <a name="add-a-site-to-site-connection-to-a-vnet-with-an-existing-vpn-gateway-connection"></a>Agregar una conexión de sitio a sitio a una VNet con una conexión de puerta de enlace VPN existente

> [AZURE.SELECTOR]
- [Administrador de recursos - Portal](vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md)
- [Clásico: PowerShell](vpn-gateway-multi-site.md)

En este artículo le guiará por con el portal de Azure para agregar conexiones de sitio a sitio (S2S) a una puerta de enlace VPN que tiene una conexión existente. Este tipo de conexión a menudo se conoce como una configuración de "múltiples sitios". 

Puede usar este artículo para agregar una conexión de S2S a un VNet que ya tiene una conexión S2S, conexión punto a sitio o conexión de VNet a VNet. Existen algunas limitaciones al agregar conexiones. Consulte la sección [antes de comenzar](#before) en este artículo para comprobar antes de iniciar la configuración. 

En este artículo se aplica a VNets creados con el modelo de implementación de administrador de recursos que tienen una puerta de enlace VPN RouteBased. Estos pasos no se aplican a las configuraciones de conexión coexistentes ExpressRoute o a sitios. Para obtener información acerca de las conexiones coexistentes, consulte [ExpressRoute/S2S coexistentes conexiones](../expressroute/expressroute-howto-coexist-resource-manager.md) .

### <a name="deployment-models-and-methods"></a>Métodos y modelos de implementación

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)] 

Actualizamos esta tabla como nuevos artículos y herramientas adicionales no estarán disponibles para esta configuración. Cuando un artículo está disponible, nos vincularlo directamente desde esta tabla.

[AZURE.INCLUDE [vpn-gateway-table-multi-site](../../includes/vpn-gateway-table-multisite-include.md)] 


## <a name="before"></a>Antes de empezar

Compruebe los elementos siguientes:

- No se crea una conexión de coexistentes ExpressRoute/S2S.
- Tiene una red virtual que se creó usando el modelo de implementación de administrador de recursos con una conexión existente.
- La puerta de enlace de red virtual para su VNet es RouteBased. Si tiene una puerta de enlace VPN PolicyBased, debe eliminar la puerta de enlace de red virtual y crear una nueva puerta de enlace VPN como RoutBased.
- Ninguno de los intervalos de direcciones se superponen por cualquiera de los VNets que se conecta este VNet.
- Dispone de dispositivo VPN compatible y alguien configurarlo. Consulte [acerca de los dispositivos VPN](vpn-gateway-about-vpn-devices.md). Si no está familiarizado con la configuración de su dispositivo VPN o no están familiarizados con la dirección IP rangos que se encuentran en su local en la configuración de red, necesita coordinar con alguien que puede proporcionar los detalles por usted.
- Tiene una dirección IP de público orientada externamente para su dispositivo VPN. Esta dirección IP no se encuentra detrás de un NAT.


## <a name="part1"></a>Parte 1: configurar una conexión

1. Desde un explorador, vaya al [portal de Azure](http://portal.azure.com) y, si es necesario, inicie sesión con su cuenta de Azure.
2. Haga clic en **todos los recursos** y busque la **puerta de enlace de red virtual** de la lista de recursos y haga clic en él.
3. En el módulo de **puerta de enlace de red Virtual** , haga clic en **conexiones**.

    ![Módulo de conexiones](./media/vpn-gateway-howto-multi-site-to-site-resource-manager-portal/connectionsblade.png "Connections blade")<br>

4. En el módulo de **conexiones** , haga clic en **+ Add**.

    ![Agregar botón de conexión](./media/vpn-gateway-howto-multi-site-to-site-resource-manager-portal/addbutton.png "Add connection button")<br>

5. En el módulo de **Agregar conexión** , rellene los siguientes campos:
    - **Nombre:** El nombre que desea dar al sitio que se va a crear la conexión.
    - **Tipo de conexión:** Seleccione **sitio a sitio (IPsec)**.

    ![Agregar módulo de conexión](./media/vpn-gateway-howto-multi-site-to-site-resource-manager-portal/addconnectionblade.png "Add connection blade")<br>

## <a name="part2"></a>Parte 2: agregar una puerta de enlace de red local

1. Haga clic en ***Elegir una puerta de enlace de red local***de **puerta de enlace de red Local** . Se abrirá el módulo de **puerta de enlace de elegir una red local** .

    ![Elija la puerta de enlace de red local](./media/vpn-gateway-howto-multi-site-to-site-resource-manager-portal/chooselng.png "Choose local network gateway")<br>
2. Haga clic en **Crear nuevo** para abrir la hoja de **puerta de enlace de crear red local** .

    ![Crear hoja de puerta de enlace de red local](./media/vpn-gateway-howto-multi-site-to-site-resource-manager-portal/createlngblade.png "Create local network gateway")<br>

3. En el módulo de **puerta de enlace de red local a crear** , rellene los siguientes campos:
    - **Nombre:** El nombre que desea dar al recurso de puerta de enlace de red local.
    - **Dirección IP:** La dirección IP pública del dispositivo VPN en el sitio que desea conectarse.
    - **Espacio de direcciones:** El espacio de direcciones que desee que se enrutan en el nuevo sitio de red local.
4. En el módulo de la **puerta de enlace de crear red local** para guardar los cambios, haga clic en **Aceptar** .

## <a name="part3"></a>Parte 3: agregar la clave compartida y crear la conexión

1. En el módulo **Agregar conexión** , agregue la clave compartida que desee usar para crear la conexión. Puede obtener la clave compartida desde su dispositivo VPN, o realizar uno aquí y, a continuación, configurar el dispositivo VPN para usar la misma clave compartida. Lo importante es que las claves son exactamente iguales.

    ![Clave compartida](./media/vpn-gateway-howto-multi-site-to-site-resource-manager-portal/sharedkey.png "Shared key")<br>
2. En la parte inferior de la hoja, haga clic en **Aceptar** para crear la conexión.

## <a name="part4"></a>Parte 4: Compruebe la conexión VPN

Puede comprobar la conexión VPN en el portal de o con PowerShell.

[AZURE.INCLUDE [vpn-gateway-verify-connection-rm](../../includes/vpn-gateway-verify-connection-rm-include.md)]


## <a name="next-steps"></a>Pasos siguientes

- Una vez finalizada la conexión, puede agregar máquinas virtuales con sus redes virtuales. Consulte la máquinas virtuales de [la ruta de aprendizaje](https://azure.microsoft.com/documentation/learning-paths/virtual-machines) para obtener más información.
