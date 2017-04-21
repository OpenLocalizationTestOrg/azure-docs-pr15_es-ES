<properties
   pageTitle="Conectarse a su red local a Azure | Microsoft Azure"
   description="Explica y compara los métodos diferentes para conectarse a servicios de nube de Microsoft Azure, Office 365, como Dynamics CRM Online."
   services=""
   documentationCenter="na"
   authors="jimdial"
   manager="carmonm"
   editor=""
   tags=""/>
<tags
   ms.service="guidance"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/25/2016"
   ms.author="jdial"/>
   
# <a name="connecting-your-on-premises-network-to-azure"></a>Conectarse a su red local a Azure

Microsoft proporciona varios tipos de servicios en la nube. Aunque puede conectarse a todos los servicios a través de Internet pública, también puede conectarse a algunos de los servicios mediante un túnel de red privada virtual (VPN) a través de Internet o a través de una conexión privada y directa a Microsoft. Este artículo le ayudará a determinar qué opción de conectividad mejor responda a sus necesidades basadas en los tipos de servicios de nube de Microsoft que consume. La mayoría de organizaciones utilizan varios tipos de conexión que se describe a continuación.

## <a name="connecting-over-the-public-internet"></a>Conectarse a través de Internet pública

Este tipo de conexión proporciona acceso a los servicios de nube de Microsoft directamente a través de Internet, como se muestra a continuación.

![Internet] (./media/guidance-connecting-your-on-premises-network-to-azure/internet.png "Internet")

Esta conexión suele ser el primer tipo que se usa para conectarse a servicios de nube de Microsoft. La siguiente tabla enumera las ventajas y desventajas de este tipo de conexión.



| **Ventajas**| **Consideraciones**|
|---------|---------|
|No requiere ninguna modificación en su red local siempre y cuando todos los dispositivos cliente tienen acceso ilimitado a todas las direcciones IP y puertos de Internet.|Aunque el tráfico a menudo se cifra con HTTPS, puede interceptar en tránsito puesto que atraviesan Internet pública.|
|Puede conectarse a todos los servicios de nube de Microsoft expuestos a Internet.|Una latencia porque la conexión atraviesa Internet.|
|Utiliza la conexión a Internet existente.||
|No requiere la administración de los dispositivos de conectividad.||

Esta conexión no tiene conectividad ni los costos de ancho de banda ya que usa la conexión a Internet existente. 

## <a name="connecting-with-a-point-to-site-connection"></a>Conectar con una conexión punto a sitio

Este tipo de conexión proporciona acceso a algunos servicios de nube de Microsoft a través de un túnel de protocolo de túnel de sockets seguros (SSTP) a través de Internet, como se muestra a continuación.

![P2S] Conexión punto a sitio de (./media/guidance-connecting-your-on-premises-network-to-azure/p2s.png "")

La conexión se realiza a través de su conexión a Internet, pero requiere el uso de una puerta de enlace de VPN de Azure. La siguiente tabla enumera las ventajas y desventajas de este tipo de conexión.

| **Ventajas**| **Consideraciones**|
|---------|---------|
|No requiere ninguna modificación en su red local siempre y cuando todos los dispositivos cliente tienen acceso ilimitado a todas las direcciones IP y puertos de Internet.|Aunque el tráfico se cifra con IPSec, puede interceptar en tránsito puesto que atraviesan Internet pública.|
|Utiliza la conexión a Internet existente.|Una latencia porque la conexión atraviesa Internet.|
|Rendimiento de hasta 200 Mb/s por la puerta de enlace.|Requiere la creación y administración de conexiones independientes entre cada puerta de enlace que debe conectarse a cada dispositivo y cada dispositivo en su red local.|
|Puede utilizarse para conectarse a servicios de Azure que se pueden conectar a un redes virtuales de Azure (VNet) como máquinas virtuales de Azure y servicios de nube de Azure.|Requiere una administración continua mínimo de una puerta de enlace de VPN de Azure.|
||No se pueden usar para conectar con Dynamics CRM Online o de Microsoft Office 365.
||No se pueden usar para conectarse a servicios de Azure que no puede conectarse a un VNet.|

Más información sobre el servicio de [Puerta de enlace VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md) , sus [precios](https://azure.microsoft.com/pricing/details/vpn-gateway)y datos de salida transferencia de [precios](https://azure.microsoft.com/pricing/details/data-transfers).

## <a name="connecting-with-a-site-to-site-connection"></a>Conectar con una conexión de sitio a otro

Este tipo de conexión proporciona acceso a algunos servicios de nube de Microsoft a través de un túnel IPSec a través de Internet, como se muestra a continuación.

![S2S] (./media/guidance-connecting-your-on-premises-network-to-azure/s2s.png "Conexión de sitio a sitio")

La conexión se realiza a través de su conexión a Internet, pero requiere el uso de una puerta de enlace de Azure VPN con su asociado de precios y el precio de transferencia de datos de salida. La siguiente tabla enumera las ventajas y desventajas de este tipo de conexión.

| **Ventajas**| **Consideraciones**|
|---------|---------|
|Todos los dispositivos en su red local pueden comunicarse con los servicios de Azure conectados a una VNet por lo que no es necesario configurar conexiones individuales para cada dispositivo.|Aunque el tráfico se cifra con IPSec, puede interceptar en tránsito puesto que atraviesan Internet pública.|
|Utiliza la conexión a Internet existente.|Una latencia porque la conexión atraviesa Internet.|
|Puede utilizarse para conectarse a servicios de Azure pueden estar conectados a un VNet como máquinas virtuales y servicios en la nube.|Debe configurar y administrar un dispositivo VPN validada * local.|
|Rendimiento de hasta 200 Mb/s por la puerta de enlace.|Requiere una administración continua mínimo de una puerta de enlace de VPN de Azure.|
|Puede forzar el tráfico saliente iniciado desde máquinas virtuales de nube a través de la red local para inspección y el registro mediante rutas definidas por el usuario o el protocolo de puerta de enlace de borde (BGP) **.|No se pueden usar para conectar con Dynamics CRM Online o de Microsoft Office 365.|
||No se pueden usar para conectarse a servicios de Azure que no puede conectarse a un VNet.|
||Si usa los servicios que inician conexiones a los dispositivos locales y lo requieren las directivas de seguridad, puede que tenga un firewall entre la red local y Azure.|

- * Ver una lista de [dispositivos VPN a validar](../vpn-gateway/vpn-gateway-about-vpn-devices.md#validated-vpn-devices).
- ** Más información sobre cómo usar [rutas definidas por el usuario](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md) o [BGP](../vpn-gateway/vpn-gateway-bgp-overview.md) para forzar el enrutamiento de Azure VNets a un dispositivo local.

## <a name="connecting-with-a-dedicated-private-connection"></a>Conectar con una conexión dedicada privada

Este tipo de conexión proporciona acceso a todos los servicios de nube de Microsoft sobre una conexión privada dedicada a Microsoft que atraviesan Internet, como se muestra a continuación.

![ER] (./media/guidance-connecting-your-on-premises-network-to-azure/er.png "Conexión de ExpressRoute")

La conexión requiere el uso del servicio de ExpressRoute y una conexión a un proveedor de servicios de conectividad. La siguiente tabla enumera las ventajas y desventajas de este tipo de conexión.

| **Ventajas**| **Consideraciones**|
|---------|---------|
|Tráfico no se pueden interceptar en tránsito sobre Internet público ya que se utiliza una conexión dedicada a través de un proveedor de servicios.|Requiere una administración enrutador local.|
|Ancho de banda de hasta 10 Gb/s por circuito de ExpressRoute y el rendimiento hasta 2 Gb/s para cada puerta de enlace.|Requiere una conexión a un proveedor de servicios de conectividad dedicada.|
|Latencia predicción porque es una conexión dedicada a Microsoft que atraviesa Internet.|Puede requerir la administración continua mínimo de una o varias puertas de enlace de la VPN Azure (si el circuito a conectarse a VNets).|
|No se requiere una comunicación cifrada, aunque puede cifrar el tráfico, si lo desea.| Si está usando Servicios de nube que inician conexiones a dispositivos locales, puede que tenga un firewall entre la red local y Azure.|
|Puede conectar directamente a todos los servicios de nube de Microsoft, con algunas excepciones *.|Requiere traducción de direcciones de red (NAT) de direcciones IP local introducir los centros de datos de Microsoft para los servicios que no se puede conectar a VNet.* *|
|Puede forzar el tráfico saliente iniciado desde máquinas virtuales de nube a través de la red local para usar BGP de registro y la inspección.|

- * Ver una [lista de servicios](../expressroute/expressroute-faqs.md#supported-services) que no se pueden usar con ExpressRoute. Su suscripción de Azure debe aprobarse para conectarse a Office 365.  Vea el artículo de [Azure ExpressRoute para Office 365](https://support.office.com/article/Azure-ExpressRoute-for-Office-365-6d2534a2-c19c-4a99-be5e-33a0cee5d3bd?ui=en-US&rs=en-US&ad=US&fromAR=1) para obtener información detallada.
- ** Obtener más información sobre ExpressRoute [NAT](../expressroute/expressroute-nat.md) requisitos.

Más información sobre [ExpressRoute](../expressroute/expressroute-introduction.md), su asociado [precios](https://azure.microsoft.com/pricing/details/expressroute)y [proveedores de conectividad](../expressroute/expressroute-locations.md#connectivity-provider-locations).

## <a name="additional-considerations"></a>Consideraciones adicionales

- Varias de las opciones anteriores tienen distintos límites máximos que admiten para conexiones VNet, conexiones de puerta de enlace y otros criterios. Se recomienda que revise el Azure [limita las redes](../azure-subscription-service-limits.md#networking-limits) para conocer si cualquiera de ellos afectan los tipos de conectividad que decide utilizar. 
- Si tiene previsto conectar una puerta de enlace de una conexión VPN de sitio a sitio a la misma VNet como una puerta de enlace de ExpressRoute, debe familiarizarse con las restricciones importantes primero. Vea el artículo [Configurar ExpressRoute y conexiones de sitio a sitio coexistentes](../expressroute/expressroute-howto-coexist-resource-manager.md#limits-and-limitations) para obtener más detalles.

## <a name="next-steps"></a>Pasos siguientes

Los siguientes recursos explican cómo implementar los tipos de conexión de este artículo.

-   [Implementar una conexión punto a sitio](../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md)
-   [Implementar una conexión de sitio a otro](guidance-hybrid-network-vpn.md)
-   [Implementar una conexión dedicada privada](guidance-hybrid-network-expressroute.md)
-   [Implementar una conexión dedicada privada con una conexión de sitio a sitio para alta disponibilidad](guidance-hybrid-network-expressroute-vpn-failover.md)
