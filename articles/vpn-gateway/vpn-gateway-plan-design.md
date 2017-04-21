<properties 
   pageTitle="Diseño y planificación de la puerta de enlace VPN | Microsoft Azure"
   description="Obtenga más información sobre la planificación de puerta de enlace VPN y diseño para entre local, híbrido y conexiones de VNet a VNet"
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-service-management,azure-resource-manager"/>
<tags 
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/18/2016"
   ms.author="cherylmc"/>

# <a name="planning-and-design-for-vpn-gateway"></a>Planificación y diseño de puerta de enlace VPN

Planear y diseñar su entre local y configuraciones de VNet a VNet pueden ser simple o compleja, según sus necesidades de red. En este artículo le guiará por consideraciones de diseño y básicas.

## <a name="planning"></a>Planificación


### <a name="compare"></a>Opciones de conectividad entre local

Si desea conectar los sitios local de forma segura a una red virtual, tiene tres maneras diferentes de hacerlo: a sitios, sitio de punto y ExpressRoute. Comparar las conexiones locales entre diferentes que están disponibles. La opción que elija depende de varias consideraciones, como por ejemplo:


- ¿Qué tipo de rendimiento requiere la solución?
- ¿Desea comunicarse por Internet pública mediante VPN segura, o a través de una conexión privada?
- ¿Tiene una dirección IP pública disponible para su uso?
- ¿Planea usar un dispositivo VPN? Si es así, ¿es compatible?
- ¿Se conecta unos pocos equipos o desea una conexión persistente para su sitio?
- ¿Qué tipo de puerta de enlace VPN es necesario para la solución que desea crear?
- ¿Qué SKU de puerta de enlace debe utilizar?


La siguiente tabla puede ayudarle a decidir la mejor opción de conectividad para la solución.


[AZURE.INCLUDE [vpn-gateway-cross-premises](../../includes/vpn-gateway-cross-premises-include.md)]



### <a name="gwrequire"></a>Requisitos de la puerta de enlace por tipo VPN y SKU

[AZURE.INCLUDE [vpn-gateway-gwsku](../../includes/vpn-gateway-gwsku-include.md)]

Para obtener más información acerca de la puerta de enlace SKU, consulte [configuración de puerta de enlace VPN](vpn-gateway-about-vpn-gateway-settings.md#gwsku).

#### <a name="aggregate-throughput-by-sku-and-vpn-type"></a>Rendimiento agregado por tipo SKU y VPN

La siguiente tabla muestra los tipos de puerta de enlace y el rendimiento agregado estimado. El rendimiento agregado estimado puede ser un factor decisivo para el diseño.
Precios diferentes SKU de puerta de enlace. Para obtener información sobre precios, consulte [Precios de puerta de enlace VPN](https://azure.microsoft.com/pricing/details/vpn-gateway/). Esta tabla se aplica al administrador de recursos y modelos de implementación clásico.

[AZURE.INCLUDE [vpn-gateway-table-gwtype-aggtput](../../includes/vpn-gateway-table-gwtype-aggtput-include.md)] 

#### <a name="supported-configurations-by-sku-and-vpn-type"></a>Configuraciones admitidas por tipo SKU y VPN

[AZURE.INCLUDE [vpn-gateway-table-requirements](../../includes/vpn-gateway-table-requirements-include.md)] 

### <a name="wf"></a>Flujo de trabajo

En la lista siguiente describe el flujo de trabajo comunes para la conectividad de nube:

1.  Diseñar y planear la topología de conectividad y la lista de los espacios de direcciones para todas las redes que desea conectarse.
2.  Crear una red virtual Azure. 
3.  Crear una puerta de enlace VPN de la red virtual.
4.  Crear y configurar conexiones a redes local u otras redes virtuales (según sea necesario).
5.  Crear y configurar una conexión punto a sitios de la puerta de enlace VPN de Azure (según sea necesario).
 

## <a name="design"></a>Diseño

### <a name="topologies"></a>Topologías de conexión

Iniciar consultando los diagramas en el artículo [Acerca de la puerta de enlace VPN](vpn-gateway-about-vpngateways.md) . El artículo contiene diagramas básicos, los modelos de implementación para cada topología (Administrador de recursos o clásico) y la implementación de las herramientas pueden usar para implementar la configuración.   

### <a name="designbasics"></a>Conceptos básicos del diseño

Las secciones siguientes describen los conceptos básicos de puerta de enlace VPN. Considere también las [limitaciones de los servicios de red](../articles/azure-subscription-service-limits.md#networking-limits).


#### <a name="subnets"></a>Acerca de las subredes

Al crear conexiones, debe tener en cuenta los rangos de subred. No puede tener la superposición de intervalos de direcciones de subred. Una subred superpuesta es cuando una red virtual o ubicación local contiene el mismo espacio de direcciones que contiene la otra ubicación. Esto significa que necesita su ingenieros de red para sus redes local local dividir fuera de un rango que puede utilizar para su dirección IP de Azure direccionamiento espacio o subredes. Necesita espacio de direcciones que no se utiliza en la red local en local. 

Evitar superpuestos subredes también es importante cuando se trabaja con conexiones de VNet a VNet. Si se superponen las subredes y una dirección IP existe en el envío y el destino VNets, conexiones de VNet a VNet un error. Azure no puede enviar los datos a lo otro VNet porque la dirección de destino es parte de la VNet envío. 

Puertas de enlace VPN requieren subred específica denominada una subred de puerta de enlace. Todas las subredes de puerta de enlace deben denominarse GatewaySubnet funcione correctamente. Asegúrese de no asignar un nombre a la subred de puerta de enlace un nombre diferente y no implementar máquinas virtuales o nada a la subred de puerta de enlace. Consulte [subredes de puerta de enlace](vpn-gateway-about-vpn-gateway-settings.md#gwsub).

#### <a name="local"></a>Acerca de las puertas de enlace de red local

Normalmente, la puerta de enlace de red local se refiere a su ubicación local. En el modelo de implementación clásica, la puerta de enlace de red local se conoce como un sitio de red Local. Al configurar una puerta de enlace de red local, asígnele un nombre, especifique la dirección IP pública del dispositivo VPN local y especificar los prefijos de dirección que se encuentran en la ubicación local. Azure busca en los prefijos de dirección de destino para el tráfico de red, consulta la configuración que haya especificado para la puerta de enlace de red local y enruta paquetes según corresponda. Puede modificar estos prefijos de dirección según sea necesario. Para obtener más información, vea [puertas de enlace de red Local](vpn-gateway-about-vpn-gateway-settings.md#lng).


#### <a name="gwtype"></a>Acerca de los tipos de puerta de enlace

Seleccionar el tipo correcto de la puerta de enlace para su topología es fundamental. Si selecciona un tipo incorrecto, la puerta de enlace no funcionará correctamente. El tipo de puerta de enlace especifica cómo se conecta la puerta de enlace y una opción de configuración necesarios para el modelo de implementación de administrador de recursos.

Los tipos de puerta de enlace son:

- VPN
- ExpressRoute

#### <a name="connectiontype"></a>Acerca de los tipos de conexión

Cada configuración requiere un tipo de conexión específico. Los tipos de conexión son:

- IPsec
- Vnet2Vnet
- ExpressRoute
- VPNClient


#### <a name="vpntype"></a>Acerca de los tipos VPN

Cada configuración requiere un tipo específico de VPN. Si está combinando dos configuraciones, como la creación de una conexión de sitio a sitio y un punto al sitio para el mismo VNet, debe utilizar un tipo VPN que cumpla los requisitos de conexión.

[AZURE.INCLUDE [vpn-gateway-vpntype](../../includes/vpn-gateway-vpntype-include.md)] 

Las tablas siguientes muestran el tipo de VPN como se asigna a cada configuración de conexión. Asegúrese de que el tipo de conexión VPN para la puerta de enlace coincide con la configuración que desea crear. 


[AZURE.INCLUDE [vpn-gateway-table-vpntype](../../includes/vpn-gateway-table-vpntype-include.md)] 

### <a name="devices"></a>Dispositivos VPN para conexiones de sitio a otro

Para configurar una conexión de sitio a otro, independientemente del modelo de implementación, necesita lo siguiente:

- Un dispositivo VPN que sea compatible con puertas de enlace VPN de Azure
- Una dirección IP IPv4 público que no es una NAT

Debe tener experiencia configurar el dispositivo VPN o que alguien que puede configurar el dispositivo para usted. Para obtener más información acerca de los dispositivos VPN, vea [dispositivos sobre VPN](vpn-gateway-about-vpn-devices.md). El artículo de dispositivos VPN contiene información sobre dispositivos validados, requisitos de los dispositivos que no han sido validados y vínculos a documentos de configuración de dispositivo si está disponible.

### <a name="forcedtunnel"></a>Considere la posibilidad de enrutamiento de túnel forzado

Para la mayoría de las configuraciones, puede configurar túnel forzado. Túnel forzado le permite redirigir o "forzar" todo el tráfico de Internet enlazado volver a su ubicación local a través de un túnel VPN de sitio a sitio de inspección y auditoría. Se trata de un requisito de seguridad crítica de TI la mayoría de las empresas directivas. 

Sin túnel forzado, tráfico de Internet enlazado de sus máquinas virtuales en Azure pasarán por siempre de infraestructura de red Azure directamente fuera a Internet, sin la opción para que pueda inspeccionar o el tráfico de auditoría. Acceso no autorizado de Internet puede dar como resultado revelación de información u otros tipos de problemas de seguridad.

**Se obliga túnel diagrama**

![Conexión forzada túnel] (./media/vpn-gateway-plan-design/forced-tunnel.png "se obliga túnel")

Se pueden configurar una conexión túnel forzada en ambos modelos de implementación y mediante herramientas diferentes. Consulte la tabla siguiente para obtener más información. Actualizamos esta tabla como nuevos artículos, nuevos modelos de implementación y otras herramientas no estarán disponibles para esta configuración. Cuando un artículo está disponible, nos vincularlo directamente desde la tabla.

[AZURE.INCLUDE [vpn-gateway-table-forcedtunnel](../../includes/vpn-gateway-table-forcedtunnel-include.md)] 



## <a name="next-steps"></a>Pasos siguientes

Consulte los artículos de [Preguntas más frecuentes de puerta de enlace de VPN](vpn-gateway-vpn-faq.md) y [Acerca de la puerta de enlace VPN](vpn-gateway-about-vpngateways.md) para obtener más información para ayudarle con el diseño.

Para obtener más información sobre la configuración de puerta de enlace específica, consulte [Acerca de la configuración de puerta de enlace VPN](vpn-gateway-about-vpn-gateway-settings.md).




