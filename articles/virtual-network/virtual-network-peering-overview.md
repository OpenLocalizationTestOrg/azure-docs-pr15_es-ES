
<properties
   pageTitle="Interconexión de red virtual Azure | Microsoft Azure"
   description="Obtenga más información sobre VNet interconexión en Azure."
   services="virtual-network"
   documentationCenter="na"
   authors="NarayanAnnamalai"
   manager="jefco"
   editor="tysonn" />
<tags
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/17/2016"
   ms.author="narayan" />

# <a name="vnet-peering"></a>VNet interconexión

Interconexión VNet es un mecanismo que conecta dos redes virtuales (VNets) en la misma región a través de la red troncal Azure. Una vez peered, las dos redes virtuales aparecen como uno para todos los fines de conectividad. Se administran como recursos independientes, pero máquinas virtuales de estas redes virtuales puede comunicarse entre sí directamente mediante direcciones IP.

El tráfico entre máquinas virtuales de las redes virtuales peered se dirige a través de la infraestructura de Azure, al igual que se enruta el tráfico entre máquinas virtuales en la misma red virtual. Algunas de las ventajas de usar VNet interconexión incluyen:

- Una conexión de baja latencia, ancho de banda entre los recursos en redes virtuales diferentes.
- La capacidad de usar recursos como equipos de red y puertas de enlace VPN como puntos de tránsito en un VNet peered.
- La capacidad de conectar una red virtual que utiliza el modelo de administrador de recursos de Azure a una red virtual que utiliza el modelo de implementación clásica y habilita la conectividad completa entre los recursos en estas redes virtuales.

Requisitos y aspectos clave de interconexión VNet:

- Las dos redes virtuales que están peered deben estar en la misma región de Azure.
- Las redes virtuales que están peered deberían tener espacios de direcciones IP no superpuestos.
- VNet interconexión entre dos redes virtuales y hay ninguna relación transitiva derivada. Por ejemplo, si se peered A una red virtual con una red virtual B y, a continuación, si una red virtual B es peered con una red virtual C, no traducir a virtual red una peered con una red virtual C.
- Interconexión se puede establecer entre redes virtuales en dos suscripciones diferentes, como cuánto tiempo un usuario con privilegios de ambas suscripciones autoriza la interconexión y las suscripciones se asocian al mismo inquilino de Active Directory. 
- Interconexión entre una red virtual modelo de administrador de recursos y el modelo de implementación clásica requiere que el VNets debe estar en la misma suscripción.
- Una red virtual que utiliza el modelo de implementación de administrador de recursos se puede peered con otra red virtual que usa este modelo o con una red virtual utiliza el modelo de implementación clásico. Sin embargo, redes virtuales que utilizan el modelo de implementación clásica no pueden ser peered entre sí.
- Aunque la comunicación entre máquinas virtuales de redes virtuales peered no tiene restricciones de ancho de banda adicional, aún se aplica capital de ancho de banda basada en el tamaño de la máquina virtual.


![Interconexión de VNet básicas](./media/virtual-networks-peering-overview/figure01.png)

## <a name="connectivity"></a>Conectividad
Después de que se peered dos redes virtuales, una máquina virtual (función web o trabajador) en la red virtual puede conectar directamente con otros equipos virtuales en la red virtual peered. Estos dos redes tienen el nivel de IP completa conectividad.

La latencia de red de ida y vuelta entre dos máquinas virtuales de peered redes virtuales es el mismo que un recorrido dentro de una red virtual local. El rendimiento de la red se basa en el ancho de banda permitido para la máquina virtual de acuerdo con su tamaño. No hay ninguna restricción adicional en el ancho de banda.

Se enruta el tráfico entre las máquinas virtuales de redes virtuales peered directamente a través de la infraestructura de back-end de Azure y no a través de una puerta de enlace.

Máquinas virtuales de una red virtual puede acceder a los extremos (ILB) equilibrio de carga internos en una red virtual peered. Grupos de seguridad de red (NSGs) se pueden aplicar en cualquier red virtual para bloquear el acceso a otras redes virtuales o subredes si lo desea.

Cuando los usuarios configuran interconexión, puede abrir o cerrar las reglas GSN entre las redes virtuales. Si el usuario elige abrir plena conectividad entre redes virtuales peered (que es la opción predeterminada), a continuación, pueden usar NSGs en máquinas virtuales o subredes específicas para admitir o denegar el acceso específico.

Siempre Azure interna resolución de nombres DNS para máquinas virtuales no funciona a través de redes virtuales peered. Máquinas virtuales tienen nombres DNS internos que se pueden resolver sólo dentro de la red virtual local. Sin embargo, los usuarios pueden configurar máquinas virtuales que se ejecutan en redes virtuales peered como servidores DNS para una red virtual.

## <a name="service-chaining"></a>Cadena de servicio
Los usuarios pueden configurar tablas de ruta definidas por el usuario que apuntan a máquinas virtuales de redes virtuales peered como "próximo salto" dirección IP, como se muestra en el diagrama más adelante en este artículo. Esto permite a los usuarios lograr encadenar servicio, a través del cual puede dirigir el tráfico de una red virtual a un dispositivo virtual que se ejecuta en una red virtual peered a través de tablas de rutas definidas por el usuario.

Los usuarios también eficaz pueden crear entornos de tipo radial y donde el concentrador puede hospedar componentes de infraestructura como un dispositivo virtual de red. A continuación, pueden consultarla todas las redes virtual radial con él, así como un subconjunto de tráfico a los dispositivos que se ejecutan en la red virtual de concentrador. En resumen, interconexión VNet permite la dirección IP de salto siguiente en la 'definidas por el usuario tabla ruta' sea la dirección IP de una máquina virtual en la red virtual peered.

## <a name="gateways-and-on-premises-connectivity"></a>Conectividad de puertas de enlace y local
Cada red virtual, independientemente de si se está peered con otra red virtual, aún puede tener su propia puerta de enlace y usar para conectarse a local. Los usuarios también pueden configurar [conexiones de VNet a VNet](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md) mediante el uso de puertas de enlace, aunque se peered las redes virtuales.

Cuando se configuran ambas opciones para la interconectividad de red virtual, el tráfico entre las redes virtuales fluye a través de la configuración de interconexión (es decir, a través de la espina Azure).

Cuando se peered redes virtuales, los usuarios pueden configurar también la puerta de enlace en la red virtual peered como punto de tránsito a local. En este caso, la red virtual que está usando una puerta de enlace remota no puede tener su propia puerta de enlace. Una red virtual puede tener solo una puerta de enlace. Puede ser una puerta de enlace local o una puerta de enlace remoto (en la peered red virtual), como se muestra en la siguiente imagen.

Tránsito de puerta de enlace no es compatible con la relación de interconexión entre redes virtuales con el modelo de administrador de recursos y las que utilizan el modelo de implementación clásica. Ambas redes virtuales en la relación de interconexión que necesite utilizar el modelo de implementación de administrador de recursos para el tránsito de puerta de enlace para trabajar.

Cuando se peered las redes virtuales que están compartiendo una única conexión de Azure ExpressRoute, el tráfico entre ellas pasa a través de la relación de interconexión (es decir, a través de la red troncal Azure). Los usuarios aún pueden usar las puertas de enlace locales en cada red virtual para conectar con el circuito local. Como alternativa, puede usar las puertas de enlace y configurar tránsito conectividad local.

![VNet interconexión tránsito](./media/virtual-networks-peering-overview/figure02.png)

## <a name="provisioning"></a>Aprovisionamiento
Interconexión VNet es una operación de privilegio. Es una función independiente en el espacio de nombres VirtualNetworks. Un usuario puede especificarse derechos específicos para autorizar interconexión. Un usuario que tenga acceso de lectura y escritura a la red virtual hereda automáticamente estos derechos.

Un usuario que ya sea un administrador o un usuario con privilegios de la capacidad de interconexión puede iniciar una operación de interconexión en otro VNet. Si hay una solicitud coincidente para interconexión en el otro lado y si se cumplen los demás requisitos, se establecerá la interconexión.

Consulte los artículos de la sección "Pasos siguientes" para obtener más información sobre cómo establecer VNet interconexión entre dos redes virtuales.

## <a name="limits"></a>Límites
Hay límites en el número de peerings que se permiten en una única red virtual. Para obtener más información, consulte [límites de redes de Azure](../azure-subscription-service-limits.md#networking-limits) .

## <a name="pricing"></a>Precios
Interconexión VNet será de forma gratuita durante el período de revisión. Después de haber publicado, habrá un cargo nominal en el tráfico de entrada y salida que utiliza la interconexión. Para obtener más información, consulte la [página de precios](https://azure.microsoft.com/pricing/details/virtual-network).


## <a name="next-steps"></a>Pasos siguientes
- [Configurar interconexión entre redes virtuales](virtual-networks-create-vnetpeering-arm-portal.md).
- Obtenga más información sobre [NSGs](virtual-networks-nsg.md).
- Obtenga información sobre [las rutas definidas por el usuario y el reenvío de IP](virtual-networks-udr-overview.md).
