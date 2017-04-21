<properties
   pageTitle="Mover ExpressRoute circuitos de clásico para el Administrador de recursos | Microsoft Azure"
   description="Esta página proporciona información general sobre lo que necesita saber sobre puente clásico y los modelos de implementación de administrador de recursos."
   documentationCenter="na"
   services="expressroute"
   authors="ganesr"
   manager="carmonm"
   editor=""/>
<tags
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/10/2016"
   ms.author="ganesr"/>

# <a name="moving-expressroute-circuits-from-the-classic-to-the-resource-manager-deployment-model"></a>Mover ExpressRoute circuitos de clásico al modelo de implementación de administrador de recursos

Este artículo proporciona una descripción general de lo que quiere mover un circuito de Azure ExpressRoute de clásico al modelo de implementación de administrador de recursos de Azure.

[AZURE.INCLUDE [vpn-gateway-sm-rm](../../includes/vpn-gateway-classic-rm-include.md)]

Puede usar un único circuito ExpressRoute para conectarse a redes virtuales que se implementan en clásico y los modelos de implementación de administrador de recursos. Un circuito ExpressRoute, independientemente de cómo se crea, ahora puede vincular a redes virtuales en ambos modelos de implementación.

![Un circuito ExpressRoute que lleve a redes virtuales en ambos modelos de implementación](./media/expressroute-move/expressroute-move-1.png)

## <a name="expressroute-circuits-that-are-created-in-the-classic-deployment-model"></a>Circuitos ExpressRoute que se crean en el modelo de implementación clásica

Circuitos ExpressRoute que se crean en el modelo de implementación clásica necesitan se moverá el modelo de implementación de administrador de recursos en primer lugar para habilitar la conectividad a clásico y los modelos de implementación de administrador de recursos. No hay pérdida de conectividad o interrupciones cuando se mueve una conexión. Se conservan todos los vínculos de red circuito a virtual en el modelo de implementación clásica (dentro de la misma suscripción y suscripción cruzado).

Después de que el cambio se completó correctamente, el circuito ExpressRoute busca, realiza y se siente exactamente como un circuito ExpressRoute que se creó en el modelo de implementación de administrador de recursos. Ahora puede crear conexiones de redes virtuales en el modelo de implementación de administrador de recursos.

Después de un ExpressRoute circuito se ha movido al modelo de implementación de administrador de recursos, puede administrar el ciclo de vida del circuito ExpressRoute sólo mediante el modelo de implementación de administrador de recursos. Esto significa que puede realizar operaciones como agregar, actualizar o eliminar peerings, actualizar propiedades de circuito (como el ancho de banda, SKU y facturación tipo) y eliminar circuitos solo en el modelo de implementación de administrador de recursos. Consulte la sección debajo de circuitos que se crean en el modelo de implementación de administrador de recursos para obtener más información sobre cómo administrar el acceso a los modelos de implementación.

No tiene implicar a su proveedor de servicios de conectividad para llevar a cabo el movimiento.

## <a name="expressroute-circuits-that-are-created-in-the-resource-manager-deployment-model"></a>Circuitos ExpressRoute que se crean en el modelo de implementación de administrador de recursos

Puede habilitar circuitos ExpressRoute creados en el modelo de implementación de administrador de recursos para ser accesible desde ambos modelos de implementación. Para tener acceso a los modelos de implementación de se puede habilitar cualquier circuito ExpressRoute en su suscripción.

- Circuitos ExpressRoute que se crearon en el modelo de implementación de administrador de recursos no tienen acceso al modelo de implementación clásico de forma predeterminada.
- Circuitos ExpressRoute que se han movido desde el modelo de implementación clásico al modelo de implementación de administrador de recursos están accesibles desde ambos modelos de implementación de forma predeterminada.
- Un circuito ExpressRoute siempre tiene acceso al modelo de implementación de administrador de recursos, independientemente de si se ha creado en el Administrador de recursos o el modelo de implementación clásico. Esto significa que puede crear conexiones a redes virtuales creadas en el modelo de implementación de administrador de recursos siguiendo las instrucciones sobre [cómo vincular redes virtuales](expressroute-howto-linkvnet-arm.md).
- Acceso al modelo de implementación clásica está controlado por el parámetro **allowClassicOperations** en el circuito de ExpressRoute.

>[AZURE.IMPORTANT] Aplicarán todas las cuotas que se explican en la página [límites de servicio](../azure-subscription-service-limits.md) . Por ejemplo, un circuito estándar puede tener como máximo 10 vínculos o conexiones de red virtual a través de la estándar y los modelos de implementación de administrador de recursos.


## <a name="controlling-access-to-the-classic-deployment-model"></a>Controlar el acceso al modelo de implementación clásica

Puede habilitar un único circuito ExpressRoute vincular a redes virtuales en ambos modelos de implementación estableciendo el parámetro **allowClassicOperations** del circuito ExpressRoute.

Establecer **allowClassicOperations** en TRUE permite vincular redes virtuales de ambos modelos de implementación al circuito ExpressRoute. Puede vincular a redes virtuales en el modelo de implementación clásica siguiendo instrucciones sobre [cómo vincular redes virtuales en el modelo de implementación clásico](expressroute-howto-linkvnet-classic.md). Puede vincular a redes virtuales en el modelo de implementación de administrador de recursos siguiendo instrucciones sobre [cómo vincular redes virtuales en el modelo de implementación de administrador de recursos](expressroute-howto-linkvnet-arm.md).

Establecer **allowClassicOperations** en FALSE bloquea el acceso al circuito del modelo de implementación clásico. Sin embargo, se conservan todos los vínculos de red virtual en el modelo de implementación clásico. En este caso, el circuito de ExpressRoute no está visible en el modelo de implementación clásico.

## <a name="supported-operations-in-the-classic-deployment-model"></a>Operaciones admitidas en el modelo de implementación clásica

Las siguientes operaciones clásicas son compatibles en un circuito ExpressRoute cuando **allowClassicOperations** es verdadero:

 - Obtener información de circuito ExpressRoute
 - Vínculos de crear, actualizar o get o eliminar una red virtual a redes virtuales clásicas
 - Autorizaciones de vínculo de crear, actualizar o get o eliminar una red virtual de conectividad entre suscripción

No puede realizar las siguientes operaciones clásicas cuando **allowClassicOperations** es verdadero:

 - Crear, actualizar o get/Eliminar protocolo de puerta de enlace de borde (BGP) peerings de Azure público privado, Azure y peerings de Microsoft
 - Eliminar circuitos ExpressRoute

## <a name="communication-between-the-classic-and-the-resource-manager-deployment-models"></a>Comunicación entre el estándar y los modelos de implementación de administrador de recursos

Circuito de ExpressRoute actúa como un puente entre el estándar y los modelos de implementación de administrador de recursos. Tráfico entre máquinas virtuales en redes virtuales en el modelo de implementación clásica y los de las redes virtuales en los flujos de modelo de implementación de administrador de recursos a través de ExpressRoute si ambas redes virtuales están vinculadas a la misma circuito ExpressRoute.

Rendimiento agregado está limitado por la capacidad de rendimiento de la puerta de enlace de red virtual. No escriba tráfico redes de su proveedor de conectividad o su en estos casos. Flujo del tráfico entre las redes virtuales totalmente está dentro de la red de Microsoft.

## <a name="access-to-azure-public-and-microsoft-peering-resources"></a>Acceso a Azure público y recursos de interconexión de Microsoft

Aún puede tener acceso a los recursos que suelen ser accesibles a través de interconexión pública de Azure y Microsoft interconexión sin ninguna interrupción.  

## <a name="whats-supported"></a>¿Qué es compatible

Esta sección describe qué es compatible para circuitos ExpressRoute:

 - Puede usar un único circuito ExpressRoute para tener acceso a las redes virtuales que se implementan en clásico y los modelos de implementación de administrador de recursos.
 - Puede mover un circuito ExpressRoute de clásico al modelo de implementación de administrador de recursos. Tras moverlo, el circuito ExpressRoute busca, se siente y realiza como cualquier circuito de ExpressRoute que se crea en el modelo de implementación de administrador de recursos.
 - Puede mover el circuito de ExpressRoute. Vínculos de circuito, redes virtuales y puertas de enlace VPN no se puede mover a través de esta operación.
 - Después de un ExpressRoute circuito se ha movido al modelo de implementación de administrador de recursos, puede administrar el ciclo de vida del circuito ExpressRoute sólo mediante el modelo de implementación de administrador de recursos. Esto significa que puede realizar operaciones como agregar, actualizar o eliminar peerings, actualizar propiedades de circuito (como el ancho de banda, SKU y facturación tipo) y eliminar circuitos solo en el modelo de implementación de administrador de recursos.
 - Circuito de ExpressRoute actúa como un puente entre el estándar y los modelos de implementación de administrador de recursos. Tráfico entre máquinas virtuales en redes virtuales en el modelo de implementación clásica y los de las redes virtuales en los flujos de modelo de implementación de administrador de recursos a través de ExpressRoute si ambas redes virtuales están vinculadas a la misma circuito ExpressRoute.
 - Conectividad entre suscripción es compatible con el estándar y los modelos de implementación de administrador de recursos.

## <a name="whats-not-supported"></a>Lo que no es compatible

Esta sección describe lo que no es compatible para circuitos ExpressRoute:

 - Mover los vínculos circuito, puertas de enlace y redes virtuales desde el clásico al modelo de implementación de administrador de recursos.
 - Administrar el ciclo de vida de un circuito ExpressRoute desde el modelo de implementación clásico.
 - Basado en roles de soporte de Control de acceso (RBAC) para el modelo de implementación clásico. No puede realizar controles RBAC a un circuito en el modelo de implementación clásico. Cualquier administrador/coadministrator de la suscripción puede vincular o desvincular redes virtuales al circuito.

## <a name="configuration"></a>Configuración

Siga las instrucciones que se describen en [mover un circuito ExpressRoute desde el estándar para el modelo de implementación de administrador de recursos](expressroute-howto-move-arm.md).

## <a name="next-steps"></a>Pasos siguientes

- Para obtener información de flujo de trabajo, consulte [ExpressRoute circuito aprovisionamiento circuito Estados y flujos de trabajo](expressroute-workflows.md).
- Para configurar su conexión de ExpressRoute:

    - [Crear un circuito ExpressRoute](expressroute-howto-circuit-arm.md)
    - [Configurar el enrutamiento](expressroute-howto-routing-arm.md)
    - [Vincular una red virtual a un circuito ExpressRoute](expressroute-howto-linkvnet-arm.md)
