<properties
   pageTitle="Información general de la red Virtual Azure (VNet)"
   description="Obtenga información sobre redes virtuales (VNets) en Azure."
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

# <a name="virtual-network-overview"></a>Información general de una red virtual

Una red virtual Azure (VNet) es una representación de su propia red en la nube.  Es un aislamiento lógico de la nube de Azure dedicado a su suscripción. Puede controlar completamente el bloques de direcciones IP, configuración de DNS, directivas de seguridad y tablas de rutas dentro de esta red. También puede segmentar su VNet en subredes e iniciar máquinas virtuales de Azure IaaS (VM) o [Servicios de nube (instancias de la función de PaaS)](../cloud-services/cloud-services-choose-me.md). Además, puede conectar una red virtual a su red local mediante una de las [Opciones de conectividad](../vpn-gateway/vpn-gateway-about-vpngateways.md#site-to-site-and-multi-site) disponibles en Azure. Básicamente, puede ampliar su red a Azure, con un control completo en bloques de direcciones IP con el beneficio de escala empresarial que Azure proporciona.

Para entender mejor VNets, eche un vistazo a la siguiente ilustración, que muestra una red local simplificada.

![Red local](./media/virtual-networks-overview/figure01.png)

La figura anterior muestra una red local conectada a Internet a través de un enrutador. También puede ver un firewall entre el enrutador y una DMZ aloja un servidor DNS y una granja de servidores web. La granja de servidores web está utilizando un equilibrador de carga de hardware que se expone a Internet y a continuación, consume recursos de la subred interna de equilibrio de carga. La subred interna está separada de la DMZ por otro servidor de seguridad y los servidores hosts de controlador de dominio de Active Directory, servidores de base de datos y servidores de aplicaciones.

La misma red puede estar hospedada en Azure tal como se muestra en la figura siguiente.

![Red virtual Azure](./media/virtual-networks-overview/figure02.png)

Observe cómo la infraestructura de Azure asume el rol del enrutador, permite el acceso desde su VNet a Internet sin necesidad de ninguna configuración. Los firewalls pueden sustituirse por grupos de seguridad de red (NSGs) aplicado a cada subred individual. Y equilibradores de carga físicos se sustituyen por equilibradores de carga de internet hacia e internos en Azure.

>[AZURE.NOTE] Existen dos modos de implementación de Azure: clásica (también conocido como servicio de administración) y Azure Resource Manager (ARM). VNets clásica podría agregado a un grupo de afinidad o se crean como un VNet regional. Si tiene un VNet en un grupo de afinidad, se recomienda migrar [a un VNet regional](virtual-networks-migrate-to-regional-vnet.md).

## <a name="virtual-network-benefits"></a>Ventajas de una red virtual

- **Aislamiento**. VNets están completamente aisladas entre sí. Que le permite crear redes discontinuas de producción, desarrollo y pruebas que usan los mismos bloques de dirección CIDR.

- **Acceso a Internet**. Todas las máquinas virtuales de IaaS y PaaS instancias de función en una VNet pueden tener acceso a Internet pública de forma predeterminada. Puede controlar el acceso mediante el uso de grupos de seguridad de red (NSGs).

- **Acceso a máquinas virtuales dentro de la VNet**. Instancias de la función de PaaS y máquinas virtuales de IaaS se pueden iniciar en la misma red virtual y se pueden conectar entre sí mediante direcciones IP, incluso si se encuentran en distintas subredes sin necesidad de configurar una puerta de enlace o use las direcciones IP públicas.

- **Resolución de nombres**. Azure proporciona resolución de nombres interno para instancias de la función de PaaS implementadas en su VNet y máquinas virtuales de IaaS. También puede implementar sus propios servidores DNS y configurar la VNet para usarlos.

- **Seguridad**. Puede controlar el tráfico que entra y sale las máquinas virtuales y las instancias de la función de PaaS en un VNet mediante grupos de seguridad de la red.

- **Conectividad**. Se pueden conectar entre sí mediante puertas de enlace de red o interconexión VNet VNets. Se pueden conectar a centros de datos locales a través de redes VPN de sitio a sitio o Azure ExpressRoute VNets. Para obtener más información sobre la conectividad VPN de sitio a otro, visite [puertas de enlace VPN sobre](../vpn-gateway/vpn-gateway-about-vpngateways.md#site-to-site-and-multi-site). Para obtener más información sobre ExpressRoute, visite [Introducción técnica de ExpressRoute](../expressroute/expressroute-introduction.md). Para obtener más información sobre VNet interconexión, visite [VNet interconexión](virtual-network-peering-overview.md).

    >[AZURE.NOTE] Asegúrese de que crear un VNet antes de implementar las instancias de función de PaaS o máquinas virtuales de IaaS en su entorno de Azure. Basado en ARM máquinas virtuales requieren un VNet y, si no especifica un VNet existente, Azure crea predeterminado VNet que podría tener un conflicto de bloque de dirección CIDR con su red local. Por lo que le impide para conectar su VNet a su red local.

## <a name="subnets"></a>Subredes

Subred es un intervalo de direcciones IP en el VNet, puede dividir un VNet en varias subredes para la organización y la seguridad. Máquinas virtuales y las instancias de función PaaS implementadas a subredes (mismas u otra) dentro de un VNet puedan comunicarse con ellas sin ninguna configuración adicional. También puede configurar tablas de rutas y NSGs a una subred.

## <a name="ip-addresses"></a>Direcciones IP


Existen dos tipos de direcciones IP asignadas a los recursos en Azure: *públicos* y *privados*. Direcciones IP públicas permiten Azure recursos para comunicarse con otros servicios de público Azure como [Azure Redis caché](https://azure.microsoft.com/services/cache/), [Azure evento Hubs](https://azure.microsoft.com/documentation/services/event-hubs/)e Internet. Direcciones IP privadas permite la comunicación entre los recursos en una red virtual, así como las conectado a través de una VPN, sin usar direcciones IP enrutables de Internet.

Para obtener más información sobre las direcciones IP de Azure, visite [direcciones IP en una red virtual](virtual-network-ip-addresses-overview-arm.md)

## <a name="azure-load-balancers"></a>Equilibradores de carga de Azure

Máquinas virtuales y servicios en la nube en una red Virtual se pueden exponer a Internet mediante equilibradores de carga de Azure. Línea de aplicaciones empresariales que están interna solo puede ser con equilibrador de carga interno de equilibrio de carga.

- **Equilibrador de carga externo**. Puede utilizar un equilibrador de carga externo para proporcionar alta disponibilidad para máquinas virtuales de IaaS y PaaS instancias de rol tiene acceso desde Internet pública.

- **Equilibrador de carga interno**. Puede utilizar un equilibrador de carga interno para proporcionar alta disponibilidad para máquinas virtuales de IaaS e instancias de la función de PaaS acceso desde otros servicios en su VNet.

Para obtener más información acerca de equilibrio de carga en Azure, visite [Introducción del equilibrador de carga](../load-balancer/load-balancer-overview.md).

## <a name="network-security-group-nsg"></a>Grupo de seguridad de red (NSG)

Puede crear NSGs para controlar el acceso de entrada y salido para interfaces de red (NIC), máquinas virtuales y subredes. Cada GSN contiene una o más reglas que especifica si se aprobado o denegado tráfico en función de la dirección IP de origen, puerto de origen, dirección IP de destino y puerto de destino. Para obtener más información sobre NSGs, visite [¿Qué es un grupo de seguridad de la red](virtual-networks-nsg.md).

## <a name="virtual-appliances"></a>Dispositivos virtuales

Un dispositivo virtual es simplemente otra VM en su VNet que ejecute una función de dispositivo en función de software, como firewall, optimización de WAN o la detección de intrusiones. Puede crear una ruta en Azure para enrutar el tráfico de VNet un dispositivo virtual para usar sus funciones.

Por ejemplo, NSGs puede usarse para proporcionar seguridad en el VNet. Sin embargo, NSGs proporcionan capa 4 lista de Control de acceso (ACL) a los paquetes entrantes y salientes. Si desea usar un modelo de seguridad 7 de capa, debe usar un dispositivo de firewall.

Dispositivos virtuales dependen de [las rutas y reenvío IP definidas por el usuario](virtual-networks-udr-overview.md).

## <a name="limits"></a>Límites
Hay límites en el número de redes virtuales permiten en una suscripción, consulte [límites de redes de Azure](../azure-subscription-service-limits.md#networking-limits) para obtener más información.

## <a name="pricing"></a>Precios
Es sin costo adicional para usar redes virtuales de Azure. Las instancias de cálculo que se inicia dentro de la Vnet se cargarán los coeficientes como se describe en [Azure VM precios](https://azure.microsoft.com/pricing/details/virtual-machines/). Las [Puertas de enlace VPN](https://azure.microsoft.com/pricing/details/vpn-gateway/) y [las direcciones IP públicas] (https://azure.microsoft.com/pricing/details/ip-addresses/) utilizados en la VNet también será cargadas tasas estándar.

## <a name="next-steps"></a>Pasos siguientes

- [Crear un VNet](virtual-networks-create-vnet-arm-pportal.md) y subredes.
- [Crear una máquina virtual en un VNet](../virtual-machines/virtual-machines-windows-hero-tutorial.md).
- Obtenga más información sobre [NSGs](virtual-networks-nsg.md).
- Obtenga información sobre [las rutas y reenvío IP definidas por el usuario](virtual-networks-udr-overview.md).
