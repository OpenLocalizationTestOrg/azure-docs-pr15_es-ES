<properties
   pageTitle="Información general sobre configuraciones altamente disponibles con las puertas de enlace VPN de Azure | Microsoft Azure"
   description="Este artículo proporciona una descripción general de las opciones de configuración altamente disponibles con las puertas de enlace VPN de Azure."
   services="vpn-gateway"
   documentationCenter="na"
   authors="yushwang"
   manager="rossort"
   editor=""
   tags=""/>

<tags
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/24/2016"
   ms.author="yushwang"/>

# <a name="highly-available-cross-premises-and-vnet-to-vnet-connectivity"></a>Altamente disponible entre local y conectividad de VNet a VNet

Este artículo proporciona una descripción general de las opciones de configuración altamente disponibles para las entre local y la conectividad de VNet a VNet con puertas de enlace VPN de Azure.

## <a name = "activestandby"></a>Acerca de redundancia de puerta de enlace VPN de Azure

Cada puerta de enlace de Azure VPN consta de dos instancias en una configuración de reserva activa. Mantenimiento planeado o interrupciones planificadas que pasa a la instancia activa, de la instancia de reserva tomar el control (migración tras error) automáticamente y reanudar la S2S VPN o conexiones de VNet a VNet. Cambiar sobre provocará una breve interrupción. Para el mantenimiento planeado, se debe restaurar la conectividad dentro de 10 o 15 segundos. Para problemas no planeados, la recuperación de conexión será más larga, aproximadamente 1 minuto a 1 y medio minutos en el peor caso. Para conexiones de cliente VPN P2S a la puerta de enlace, se desconectarán las conexiones P2S y los usuarios tendrán que volver a conectarse desde los equipos cliente.

![Reserva activa](./media/vpn-gateway-highlyavailable/active-standby.png)

## <a name="highly-available-cross-premises-connectivity"></a>Conectividad entre local altamente disponible

Para proporcionar una mejor disponibilidad de las conexiones entre local, hay un par de opciones disponibles:

- Varios dispositivos VPN local
- Puerta de enlace de Azure VPN activo activo
- Combinación de ambos

### <a name = "activeactiveonprem"></a>Varios dispositivos VPN local

Puede usar varios dispositivos VPN de su red local para conectarse a la puerta de enlace VPN de Azure, tal como se muestra en el diagrama siguiente:

![Varios local VPN](./media/vpn-gateway-highlyavailable/multiple-onprem-vpns.png)

Esta configuración proporciona varios túneles activos desde la misma puerta de enlace VPN de Azure en sus dispositivos locales en la misma ubicación. Hay algunos requisitos y limitaciones:

1. Tiene que crear varias conexiones VPN S2S desde sus dispositivos VPN en Azure. Puede conectar varios dispositivos VPN desde la misma red local a Azure, debe crear una puerta de enlace de red local para cada dispositivo VPN y una conexión de la puerta de enlace de Azure VPN a la puerta de enlace de red local.

2. Las puertas de enlace de red local que corresponde a los dispositivos VPN deben tener direcciones IP públicas únicas en la propiedad "GatewayIpAddress".

3. BGP es necesario para esta configuración. Cada puerta de enlace de red local que representa un dispositivo VPN debe tener una dirección IP del punto BGP única especificada en la propiedad "BgpPeerIpAddress".

4. No debe superponerse sobre el campo de la propiedad AddressPrefix en cada puerta de enlace de red local. Debe especificar "BgpPeerIpAddress" en /32 formato CIDR en el campo AddressPrefix, por ejemplo, 10.200.200.254/32.

5. Debe utilizar BGP para anunciar los mismos prefijos del mismo prefijos de red para la puerta de enlace de Azure VPN local y el tráfico se reenviarán a través de estos túneles simultáneamente.

6. Cada conexión se cuenta con el número máximo de túneles de la puerta de enlace VPN de Azure, 10 para Basic y SKU estándar y 30 para HighPerformance SKU. 

En esta configuración, la puerta de enlace VPN de Azure aún está en modo de reserva activa, por lo que el mismo comportamiento de migración tras error y una breve interrupción aún se producen como descrito [anteriormente](#activestandby). Aunque esta configuración evita errores o interrupciones en su red local y en dispositivos VPN.
 
### <a name="active-active-azure-vpn-gateway"></a>Puerta de enlace de Azure VPN activo activo

Ahora puede crear una puerta de enlace VPN de Azure en una configuración de activo / activo, donde dos instancias de las máquinas virtuales de puerta de enlace establecer túneles VPN S2S al dispositivo VPN local, tal como se muestra en el diagrama siguiente:

![Activo activo](./media/vpn-gateway-highlyavailable/active-active.png)

En esta configuración, cada instancia de puerta de enlace de Azure tendrá una dirección IP pública única y cada establecerá un túnel IPsec/IKE S2S VPN al dispositivo VPN local especificado en la conexión y la puerta de enlace de red local. Observe que ambos túneles VPN son realmente parte de la misma conexión. Necesitará configurar su dispositivo VPN local para aceptar o establecer dos túneles VPN S2S a las direcciones IP públicas de Azure VPN puerta de enlace dos.

Dado que son las instancias de puerta de enlace de Azure en configuración de activo / activo, el tráfico de su red virtual Azure a su red local se simultáneamente, enrutó a través de ambos túneles incluso si el dispositivo VPN local puede favorecer un túnel encima de la otra. Nota Aunque el mismo flujo TCP o UDP siempre pasarán por el mismo túnel o la ruta de acceso, a menos que se produce un evento de mantenimiento en una de las instancias.

Cuando un evento no planificado o el mantenimiento planeado pasa a la instancia de una puerta de enlace, se desconectarán del túnel IPsec esa instancia al dispositivo VPN local. Las rutas correspondientes en sus dispositivos VPN deben pueden quitarse o retiradas automáticamente para que el tráfico cambiará al túnel IPsec activo. En el lado de Azure, el modificador sobre sucederá automáticamente desde la instancia afectada a la instancia activa.

### <a name="dual-redundancy-active-active-vpn-gateways-for-both-azure-and-on-premises-networks"></a>Redundancia dual: puertas de enlace activo activo VPN para redes de Azure y local

La opción más confiable es combinar las puertas de enlace activo activo en su red y Azure, como se muestra en el diagrama siguiente.

![Redundancia dual](./media/vpn-gateway-highlyavailable/dual-redundancy.png)

Aquí crear y configurar la puerta de enlace VPN de Azure en una configuración de activo activo y crear dos puertas de enlace de red local y dos conexiones para los dos local dispositivos VPN como se describió anteriormente. El resultado es una conectividad completa de 4 túneles IPsec entre su red virtual Azure y su red local.

Todas las puertas de enlace y túneles están activos desde el lado de Azure, para que el tráfico se extienden entre todos los 4 túneles simultáneamente, aunque cada flujo TCP o UDP seguirá nuevamente el mismo túnel o la ruta de acceso desde el lado de Azure. Aunque al distribuir el tráfico, es posible que vea un poco mejor rendimiento sobre los túneles IPsec, es el objetivo principal de esta configuración de alta disponibilidad. Y debido a la naturaleza estadística de dispersión, es difícil proporcionar que la medida en diferentes condiciones de tráfico de aplicación afectará el rendimiento agregado.

Esta topología requieren dos puertas de enlace de red local y dos conexiones con el par de dispositivos VPN local de soporte técnico y BGP es necesario para permitir que las dos conexiones a la misma red local. Estos requisitos son los mismos que [anteriormente](#activeactiveonprem). 

## <a name="highly-available-vnet-to-vnet-connectivity-through-azure-vpn-gateways"></a>Conectividad de alta disponibilidad VNet a VNet a través de puertas de enlace VPN de Azure

También puede aplicar la misma configuración de activo activo para conexiones de Azure VNet a VNet. Puede crear las puertas de enlace VPN activo activo para ambas redes virtuales y conectarlas juntos al formulario de la misma conectividad completa de 4 túneles entre los dos VNets, como se muestra en el diagrama siguiente:

![VNet a VNet](./media/vpn-gateway-highlyavailable/vnet-to-vnet.png)

Así se garantiza que siempre hay un par de túneles entre las dos redes virtuales para los eventos de mantenimiento planeado, proporcionar disponibilidad aún mejor. Aunque la misma topología de cruz local conectividad requiere dos conexiones, la topología de VNet a VNet mostrada arriba necesitarán únicamente una conexión para cada puerta de enlace. Además, BGP es opcional, a menos que se requiere tránsito enrutamiento sobre la conexión de VNet a VNet.


## <a name="next-steps"></a>Pasos siguientes

Vea [Configurar activo activo VPN las puertas de enlace para entre local y conexiones de VNet a VNet](vpn-gateway-activeactive-rm-powershell.md) para conocer los pasos para configurar conexiones de VNet a VNet y activo activo entre local.
