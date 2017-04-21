<properties 
   pageTitle="¿Cuáles son las rutas de definidas por el usuario y el reenvío de IP?"
   description="Obtenga información sobre cómo usar rutas definidas por el usuario (UDR) y reenvío IP reenvíe el tráfico a la red de equipos virtuales en Azure."
   services="virtual-network"
   documentationCenter="na"
   authors="jimdial"
   manager="carmonm"
   editor="tysonn" />
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="03/15/2016"
   ms.author="jdial" />

# <a name="what-are-user-defined-routes-and-ip-forwarding"></a>¿Cuáles son las rutas de definidas por el usuario y el reenvío de IP?
Al agregar máquinas virtuales (VM) a una red virtual (VNet) en Azure, observará que las máquinas virtuales son capaces de comunicarse con otros en la red, automáticamente. No es necesario especificar una puerta de enlace, aunque las máquinas virtuales están en subredes diferentes. El mismo es true para la comunicación de las máquinas virtuales a Internet y, incluso a su local está presente red cuando una conexión híbrido de Azure en su propio centro de datos.

Este flujo de comunicación es posible porque Azure usa una serie de rutas del sistema para definir cómo fluye el tráfico IP. Rutas de sistema controlan el flujo de comunicación en las situaciones siguientes:

- Desde dentro de la misma subred.
- Desde una subred a otro en una VNet.
- Desde máquinas virtuales a Internet.
- Desde un VNet a otro VNet a través de una puerta de enlace VPN.
- Desde un VNet a su red local a través de una puerta de enlace VPN.

La figura siguiente muestra una configuración sencilla con un VNet, dos subredes y unas VM, junto con las rutas de sistema que permiten el flujo del tráfico IP.

![Rutas de sistema de Azure](./media/virtual-networks-udr-overview/Figure1.png)

Aunque el uso de las rutas de sistema facilita tráfico automáticamente para su implementación, hay casos en los que desea controlar el enrutamiento de paquetes a través de un dispositivo virtual. Por lo tanto mediante la creación de rutas definidas por el usuario para especificar el próximo salto para los paquetes que fluye a una subred concreta para ir a su dispositivo virtual en su lugar y habilitar el reenvío de IP de la máquina virtual ejecuta como el dispositivo virtual.

La siguiente ilustración muestra un ejemplo de rutas definidas por el usuario y el reenvío de IP para forzar paquetes se envía a una subred a otra para ir a través de un dispositivo virtual en una tercera subred.

![Rutas de sistema de Azure](./media/virtual-networks-udr-overview/Figure2.png)

>[AZURE.IMPORTANT] Rutas definidas por el usuario solo se aplican a tráfico salir de una subred. no puede crear rutas para especificar cómo tráfico que entra en una subred desde Internet, por ejemplo. Además, el dispositivo que desea reenviar el tráfico a no puede estar en la misma subred donde se origina el tráfico. Crear siempre una subred independiente de los dispositivos. 

## <a name="route-resource"></a>Recurso de ruta
Paquetes se enrutan a través de una red TCP/IP basada en una tabla de ruta definida en cada nodo en la red física. Una tabla de ruta es una colección de rutas individuales que se utiliza para decidir dónde desea reenviar paquetes según la dirección IP de destino. Una ruta consta de las siguientes acciones:

|(Propiedad)|Descripción|Restricciones|Consideraciones|
|---|---|---|---|
| Prefijo de dirección | CIDR de destino a la que se aplica la ruta, como 10.1.0.0/16.|Debe ser un intervalo CIDR válido que representa las direcciones en Internet pública, una red virtual Azure o centro de datos local.|Asegúrese de que el **prefijo de dirección** no contiene la dirección de la **dirección de salto siguiente**, en caso contrario especificará los paquetes en un bucle que va desde el origen al siguiente salto sin alcanzar el destino. |
| Tipo de salto siguiente | El tipo de salto de Azure a la que se debe enviar el paquete. | Debe ser uno de los siguientes valores: <br/> **Una red virtual**. Representa la red virtual local. Por ejemplo, si tiene dos subredes, 10.1.0.0/16 y 10.2.0.0/16 en la misma red virtual, la ruta de cada subred de la tabla de ruta tendrá un valor de salto siguiente de *Una red Virtual*. <br/> **Puerta de enlace de red virtual**. Representa una puerta de enlace VPN S2S Azure. <br/> **Internet**. Representa la puerta de enlace de Internet predeterminada proporcionada por la infraestructura de Azure. <br/> **Dispositivo virtual**. Representa un dispositivo virtual que haya agregado a su red virtual Azure. <br/> **Ninguno**. Representa un agujero negro. No se reenviarán en todos los paquetes reenviados a un agujero negro.| Considere usar un tipo **Ninguno** para dejar de paquetes fluya a un destino dado. | 
| Dirección de salto siguiente | La dirección de salto siguiente contiene la dirección IP se deberían enviar paquetes. Valores de salto siguientes sólo se permiten en las rutas donde el siguiente tipo de salto es *Dispositivo Virtual*.| Debe ser una dirección IP que es accesible dentro de la red Virtual donde se aplica la ruta definida por el usuario. | Si la dirección IP representa una máquina virtual, asegúrese de que habilitar el [reenvío IP](#IP-forwarding) en Azure para la máquina virtual. |

En Azure PowerShell algunos de los valores de "NextHopType" tienen nombres diferentes:
- Red virtual es VnetLocal
- La puerta de enlace de red virtual es VirtualNetworkGateway
- Dispositivo virtual es VirtualAppliance
- Internet es de Internet
- Ninguna es ninguno

### <a name="system-routes"></a>Rutas de sistema
Cada subred creado en una red virtual se asocia automáticamente con una tabla de ruta que contiene las siguientes reglas de la ruta del sistema:

- **Regla de Vnet local**: esta regla se crea automáticamente para cada subred de una red virtual. Especifica que hay un vínculo directo entre las máquinas virtuales de la VNet y no hay ningún salto siguiente intermedio.
- **Regla local**: esta regla se aplica a todo el tráfico destinado al intervalo de direcciones local y usa la puerta de enlace VPN como el siguiente destino de salto.
- **Regla de Internet**: este controladores regla todo el tráfico destinado a Internet pública (dirección prefijo 0.0.0.0/0) y utiliza la puerta de enlace de internet de infraestructura como el próximo salto para todo el tráfico destinado a Internet.

### <a name="user-defined-routes"></a>Rutas definidas por el usuario
Para la mayoría de los entornos solo tendrán las rutas de sistema ya definidas por Azure. Sin embargo, tendrá que crear una tabla de rutas y agregar una o varias rutas en casos específicos, como por ejemplo:

- Forzar túnel a Internet a través de su red local.
- Usar dispositivos virtuales en su entorno de Azure.

En las situaciones anteriores, tendrá que crear una tabla de rutas y agregarle rutas definidas por el usuario. Puede tener varias tablas de rutas y la misma tabla de ruta se puede asociar a una o varias subredes. Y cada subred sólo puede estar asociado a una tabla única ruta. Todas las máquinas virtuales y servicios en la nube en una tabla de rutas de uso de subred asociado a esa subred.

Subredes dependen de las rutas de sistema hasta una tabla de ruta está asociada a la subred. Una vez que existe una asociación, enrutamiento se lleva a cabo en más larga prefijo coincidir (LPM) entre las rutas definidas por el usuario y las rutas de sistema. Si hay más de una ruta con la misma coincidencia LPM se selecciona una ruta en función de su origen en el siguiente orden:

1. Ruta definida de usuario
1. Ruta BGP (cuando se usa ExpressRoute)
1. Ruta del sistema

Para obtener información sobre cómo crear rutas definidas por el usuario, vea [cómo crear rutas y habilitar el reenvío IP en Azure](virtual-network-create-udr-arm-template.md).

>[AZURE.IMPORTANT] Rutas definidas por el usuario solo se aplican a servicios de máquinas virtuales de Azure y la nube. Por ejemplo, si desea agregar un dispositivo virtual de firewall entre su red local y Azure, tendrá que crear una ruta definida por el usuario para las tablas de Azure ruta que reenvía todo el tráfico en el espacio de direcciones local al dispositivo virtual. También puede agregar una ruta definida por el usuario (UDR) a la GatewaySubnet para reenviar todo el tráfico de local a Azure a través del dispositivo virtual. Esta es una incorporación reciente.

### <a name="bgp-routes"></a>Rutas BGP
Si tiene una conexión de ExpressRoute entre su red local y Azure, puede habilitar BGP propagar rutas desde su red local a Azure. Estas rutas BGP se usan en la misma manera que las rutas de sistema y rutas definidas por el usuario en cada subred Azure. Para obtener más información, vea [Introducción ExpressRoute](../expressroute/expressroute-introduction.md).

>[AZURE.IMPORTANT] Puede configurar su entorno de Azure para usar forzar túnel a través de su red local mediante la creación de una ruta definida de usuario para 0.0.0.0/0 de subred que utiliza la puerta de enlace VPN como el próximo salto. Sin embargo, esto solo funciona si está utilizando una puerta de enlace VPN, no ExpressRoute. Para ExpressRoute, túnel forzado se configura mediante BGP.

## <a name="ip-forwarding"></a>Reenvío de IP
Como se describió anteriormente, una de las razones principales para crear una ruta definida de usuario es reenviar el tráfico a un dispositivo virtual. Un dispositivo virtual es nada más que una máquina virtual que se ejecuta una aplicación que se usa para controlar el tráfico de red en algunos casos, como un firewall o un dispositivo NAT.

Este dispositivo virtual VM debe ser capaz de recibir el tráfico entrante que no se dirige a sí mismo. Para permitir que una máquina virtual para recibir tráfico dirigido a otros destinos, debe habilitar el reenvío IP de la máquina virtual. Se trata de una configuración, no un valor en el sistema operativo invitado de Azure.

## <a name="next-steps"></a>Pasos siguientes

- Obtenga información sobre cómo [crear rutas en el modelo de implementación de administrador de recursos](virtual-network-create-udr-arm-template.md) y asociarlos a subredes. 
- Obtenga información sobre cómo [crear rutas en el modelo de implementación clásica](virtual-network-create-udr-classic-ps.md) y asociarlos a subredes.
