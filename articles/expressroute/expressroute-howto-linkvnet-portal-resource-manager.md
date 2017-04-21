<properties
   pageTitle="Vincular una red virtual a un circuito ExpressRoute mediante el modelo de implementación de administrador de recursos y el portal de Azure | Microsoft Azure"
   description="Este documento proporciona información general sobre cómo vincular redes virtuales (VNets) a circuitos ExpressRoute."
   services="expressroute"
   documentationCenter="na"
   authors="cherylmc"
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
   ms.author="cherylmc" />

# <a name="link-a-virtual-network-to-an-expressroute-circuit"></a>Vincular una red virtual a un circuito ExpressRoute

> [AZURE.SELECTOR]
- [Portal de Azure - Administrador de recursos](expressroute-howto-linkvnet-portal-resource-manager.md)
- [PowerShell - Administrador de recursos](expressroute-howto-linkvnet-arm.md)
- [PowerShell - clásica](expressroute-howto-linkvnet-classic.md)



En este artículo le ayudará a vincular redes virtuales (VNets) a Azure ExpressRoute circuitos mediante el modelo de implementación de administrador de recursos y el portal de Azure. Redes virtuales pueden ser en la misma suscripción o pueden ser parte de otra suscripción.


**Acerca de los modelos de implementación de Azure**

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

## <a name="configuration-prerequisites"></a>Requisitos previos de configuración

- Asegúrese de que ha revisado los [requisitos previos](expressroute-prerequisites.md), [requisitos de enrutamiento](expressroute-routing.md)y [flujos de trabajo](expressroute-workflows.md) antes de comenzar la configuración.
- Debe tener un circuito ExpressRoute activo.
    - Siga las instrucciones para [crear un circuito ExpressRoute](expressroute-howto-circuit-arm.md) y tiene el circuito habilitado por su proveedor de servicios de conectividad.

    - Asegúrese de que haya configurado para su circuito la función de interconexión privada de Azure. Vea el artículo [configurar enrutamiento](expressroute-howto-routing-portal-resource-manager.md) para las instrucciones de enrutamiento.

    - Asegúrese de que interconexión privada Azure está configurada y la interconexión BGP entre la red y Microsoft para que puede habilitar la conectividad de llevar a cabo.

    - Asegúrese de que dispone de una red virtual y una puerta de enlace de red virtual creado y configurarán por completo. Siga las instrucciones para crear una [puerta de enlace VPN](../articles/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md) (siga solo pasos 1-5).

Puede vincular hasta 10 redes virtuales a un circuito de ExpressRoute estándar. Todas las redes virtuales deben estar en la misma región geopolíticas cuando se usa un circuito ExpressRoute estándar. Puede vincular un redes virtuales fuera de la región del circuito ExpressRoute geopolítica o conectar un mayor número de redes virtuales con su circuito ExpressRoute Si habilita el complemento de premium ExpressRoute. Consulte las [preguntas más frecuentes](expressroute-faqs.md) para obtener más detalles sobre el complemento premium.

## <a name="connect-a-virtual-network-in-the-same-subscription-to-a-circuit"></a>Conectar una red virtual en la misma suscripción a un circuito


### <a name="to-create-a-connection"></a>Para crear una conexión

1. Asegúrese de que su circuito ExpressRoute e interconexión privada Azure se han configurado correctamente. Siga las instrucciones en [crear un circuito ExpressRoute](expressroute-howto-circuit-arm.md) y [Configurar el enrutamiento](expressroute-howto-routing-arm.md). Su circuito ExpressRoute debe tener un aspecto similar a la siguiente imagen.

    ![Captura de pantalla de circuito ExpressRoute](./media/expressroute-howto-linkvnet-portal-resource-manager/routing1.png)

    >[AZURE.NOTE] Información de configuración de BGP no aparecerá si el proveedor de servicios de nivel 3 configurado su peerings. Si el circuito está en un estado de preparación, debería poder crear conexiones.

2. Ahora puede iniciar una conexión para vincular la puerta de enlace de red virtual a su circuito ExpressRoute de aprovisionamiento. Haga clic en **conexión** > **Agregar** para abrir el módulo de **conexión de agregar** y, a continuación, configure los valores. Vea el siguiente ejemplo de referencia.


    ![Agregar la captura de pantalla de conexión](./media/expressroute-howto-linkvnet-portal-resource-manager/samesub1.png)  


3. Después de la conexión se ha configurado correctamente, el objeto de conexión mostrará la información de la conexión.

    ![Captura de pantalla de objeto de conexión](./media/expressroute-howto-linkvnet-portal-resource-manager/samesub2.png)


### <a name="to-delete-a-connection"></a>Para eliminar una conexión

Puede eliminar una conexión, seleccione el icono **Eliminar** en el módulo para la conexión.

## <a name="connect-a-virtual-network-in-a-different-subscription-to-a-circuit"></a>Conectarse a una red virtual en una suscripción diferente de un circuito

En este momento, no se puede conectar redes virtuales a través de suscripciones mediante el portal de Azure. Sin embargo, puede usar PowerShell para realizar esta acción. Vea el artículo de [PowerShell](expressroute-howto-linkvnet-arm.md) para obtener más información.

## <a name="next-steps"></a>Pasos siguientes

Para obtener más información sobre ExpressRoute, consulte las [Preguntas más frecuentes de ExpressRoute](expressroute-faqs.md).
