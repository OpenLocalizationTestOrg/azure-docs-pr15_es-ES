<properties
   pageTitle="Públicas y privadas el direccionamiento de IP (clásico) en Azure | Microsoft Azure"
   description="Obtenga más información sobre públicas y privadas direcciones IP en Azure"
   services="virtual-network"
   documentationCenter="na"
   authors="jimdial"
   manager="carmonm"
   editor="tysonn"
   tags="azure-service-management" />
<tags
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/11/2016"
   ms.author="jdial" />

# <a name="ip-addresses-classic-in-azure"></a>Direcciones IP (clásico) en Azure
Puede asignar direcciones IP a Azure recursos para comunicarse con otros recursos de Azure, su red local e Internet. Existen dos tipos de direcciones IP que puede usar en Azure: públicos y privados.

Direcciones IP públicas se usan para la comunicación con Internet, incluidos los servicios de Azure público.

Direcciones IP privadas se usan para la comunicación dentro de una red virtual Azure (VNet), un servicio de nube y su red local cuando se utiliza una puerta de enlace VPN o circuito ExpressRoute para extender su red en Azure.

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-classic-include.md)]Obtenga información sobre cómo llevar a [cabo estos pasos con el modelo de implementación de administrador de recursos](virtual-network-ip-addresses-overview-arm.md).

## <a name="public-ip-addresses"></a>Direcciones IP públicas
Direcciones IP públicas permiten Azure recursos para comunicarse con Internet y servicios de Azure público como [Azure Redis caché](https://azure.microsoft.com/services/cache/), [Hubs de evento de Azure](https://azure.microsoft.com/services/event-hubs/), [bases de datos SQL](../sql-database/sql-database-technical-overview.md)y [almacenamiento de Azure](../storage/storage-introduction.md).

Una dirección IP pública está asociada con los siguientes tipos de recursos:

- Servicios de nube
- Máquinas virtuales de IaaS (VM)
- Instancias de la función de PaaS
- Puertas de enlace VPN
- Puertas de enlace de aplicación

### <a name="allocation-method"></a>Método de asignación
Cuando una dirección IP pública debe asignarse a un recurso de Azure, es *dinámicamente* asignada desde un grupo de la dirección IP pública disponible dentro de la ubicación que se crea el recurso. Esta dirección IP se libera cuando se detiene el recurso. En caso de un servicio de nube, esto ocurre cuando se detienen todas las instancias de la función, que se puede evitar mediante una dirección IP *estática* (reservado) (consulte [Servicios de nube](#Cloud-services)).

>[AZURE.NOTE] La lista de intervalos de IP desde la que se asignan direcciones IP públicas a recursos de Azure se publica en [intervalos IP del centro de datos de Azure](https://www.microsoft.com/download/details.aspx?id=41653).

### <a name="dns-hostname-resolution"></a>Resolución de nombre de host DNS
Cuando se crea un servicio de nube o una VM IaaS, debe proporcionar un nombre DNS del servicio de nube que es único en todos los recursos en Azure. Esto crea una asignación en los servidores DNS administrados de Azure para *dnsname*. cloudapp.net a la dirección IP pública del recurso. Por ejemplo, cuando se crea un servicio de nube con un nombre DNS del servicio de nube de **contoso**, se ocupará de (FQDN) de nombre de dominio completo **contoso.cloudapp.net** a una dirección IP pública (VIP) del servicio de nube. Puede usar este FQDN para crear un registro CNAME de dominio personalizado que apunta a la dirección IP pública de Azure.

### <a name="cloud-services"></a>Servicios de nube
Un servicio de nube siempre tiene una dirección IP pública que se conoce como una dirección IP virtual (VIP). Puede crear extremos en un servicio de nube para asociar diferentes puertos en la dirección VIP a puertos internos en máquinas virtuales y las instancias de función dentro del servicio de nube. 

Un servicio de nube puede contener varias máquinas virtuales de IaaS o las instancias de función PaaS, todo expuestas a través de la misma dirección VIP de servicio de nube. También puede asignar [varios VIP a un servicio de nube](../load-balancer/load-balancer-multivip.md), que permite escenarios de múltiples VIP como entorno de múltiples arrendatario con sitios Web basados en SSL.

Puede asegurarse de que la dirección IP pública del servicio de nube permanece igual, incluso cuando se detienen todas las instancias de la función, usando una dirección IP pública *estática* , conocida como [IP reservada](virtual-networks-reserved-public-ip.md). Puede crear un recurso IP estático (reservado) en una ubicación específica y asignar a cualquier servicio de nube en esa ubicación. No se puede especificar la dirección IP real para la IP reservada, se asigna del grupo de direcciones IP disponibles en la ubicación que se crea. Esta dirección IP no se libera hasta que se eliminan de forma explícita.

(Reservado) públicas direcciones IP suelen utilizarse en los casos en los que un servicio de nube:

- requiere reglas de firewall para configurar usuarios finales.
- depende de la resolución de nombres DNS externo, y una dirección IP dinámica requeriría los registros.
- consume servicios web externos que utilizan el modelo de seguridad basada en IP.
- usa certificados SSL vinculados a una dirección IP.

>[AZURE.NOTE] Cuando se crea una máquina virtual clásica, crea un contenedor *servicio de nube de* Azure, que tiene una dirección IP virtual (VIP). Cuando haya terminado la creación a través del portal, una predeterminada RDP o SSH *extremo* está configurado el portal para que pueda conectarse a la máquina virtual a través de la dirección VIP de servicio de nube. Puede reservar este VIP de servicio de nube que proporciona un modo eficaz una dirección IP reservada para conectarse a la máquina virtual. Puede abrir puertos adicionales configurando más extremos.

### <a name="iaas-vms-and-paas-role-instances"></a>Máquinas virtuales de IaaS y PaaS instancias de función
Puede asignar una dirección IP pública directamente a un IaaS [VM](../virtual-machines/virtual-machines-linux-about.md) o una instancia de la función de PaaS dentro de un servicio de nube. Esto se conoce como nivel de instancia público dirección IP ([ILPIP](virtual-networks-instance-level-public-ip.md)). Esta dirección IP pública sólo puede ser dinámica.

>[AZURE.NOTE] Esto es diferente de la dirección VIP del servicio de nube, que es un contenedor para máquinas virtuales de IaaS o PaaS las instancias de función, como un servicio de nube puede contener varias máquinas virtuales de IaaS o instancias de la función de PaaS, todo se expone a través de la misma dirección VIP de servicio de nube.

### <a name="vpn-gateways"></a>Puertas de enlace VPN
Una [puerta de enlace VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md) puede usarse para conectar un VNet de Azure a otras redes de local o VNets de Azure. Una puerta de enlace VPN se asigna a un público IP dirección *dinámicamente*, lo que permite la comunicación con la red de forma remota.

### <a name="application-gateways"></a>Puertas de enlace de aplicación
Una [puerta de enlace de aplicación](../application-gateway/application-gateway-introduction.md) de Azure puede utilizarse Layer7 equilibrio de carga para enrutar el tráfico de red de HTTP. Puerta de enlace de aplicación se asigna a un público IP dirección *dinámicamente*, que sirve como la dirección VIP equilibrio de carga.

### <a name="at-a-glance"></a>Un vistazo
La siguiente tabla muestra cada tipo de recurso con los métodos de asignación posible (estático o dinámico) y la capacidad de asignar varias direcciones IP públicas.

|Recursos|Dinámico|Estático|Varias direcciones IP|
|---|---|---|---|
|Servicio de nube|Sí|Sí|Sí|
|Instancia del rol de VM IaaS o PaaS|Sí|No|No|
|Puerta de enlace VPN|Sí|No|No|
|Puerta de enlace de aplicación|Sí|No|No|

## <a name="private-ip-addresses"></a>Direcciones IP privadas
Direcciones IP privadas permitir que los recursos de Azure para comunicarse con otros recursos en un servicio de nube o una [red virtual](virtual-networks-overview.md)(VNet), o a la red local (a través de una puerta de enlace VPN o circuito ExpressRoute), sin usar una dirección IP de Internet accesible.

En el modelo de implementación clásica Azure, una dirección IP privada puede asignarse a los siguientes recursos de Azure:

- Máquinas virtuales de IaaS y PaaS instancias de función
- Equilibrador de carga interno
- Puerta de enlace de aplicación

### <a name="iaas-vms-and-paas-role-instances"></a>Máquinas virtuales de IaaS y PaaS instancias de función
Máquinas virtuales (VM) creadas con el modelo de implementación clásica siempre se ubican en un servicio de nube similar a las instancias de la función de PaaS. El comportamiento de las direcciones IP privadas, por tanto, son similares para estos recursos.

Es importante que tenga en cuenta que se puede implementar un servicio de nube de dos maneras:

- Como un servicio de nube de *independiente* , donde no está dentro de una red virtual.
- Como parte de una red virtual.

#### <a name="allocation-method"></a>Método de asignación
En el caso de un servicio de nube *independiente* recursos Obtén un privada IP dirección asignada *dinámicamente* desde el intervalo de direcciones IP privadas de centro de datos de Azure. Puede usar solo para la comunicación con otras máquinas virtuales dentro del mismo servicio de nube. Cuando se detiene y se inicia el recurso, puede cambiar esta dirección IP.

En el caso de un servicio de nube implementado dentro de una red virtual recursos Obtén privada direcciones IP que se asignan desde el intervalo de direcciones de la asociada subredes (según lo especificado en su configuración de red). Esta dirección IP privada puede utilizarse para la comunicación entre todas las máquinas virtuales dentro de la VNet.

Además, en caso de servicios de nube dentro de un VNet, una dirección IP privada se asigna *dinámicamente* (con DHCP) de forma predeterminada. Puede cambiar cuando se detiene y se inicia el recurso. Para asegurarse de que la dirección IP es el mismo, debe establecer el método de asignación en *estática*y proporcionar una dirección IP válida dentro del rango de la dirección correspondiente.

Direcciones IP privadas se usan normalmente para:

 - Máquinas virtuales que actúen como controladores de dominio o los servidores DNS.
 - VM que requieren con direcciones IP de las reglas de firewall.
 - Máquinas virtuales ejecutando los servicios de acceso a otras aplicaciones a través de una dirección IP.

#### <a name="internal-dns-hostname-resolution"></a>Resolución de nombre de host DNS interna
Todas las máquinas virtuales de Azure e instancias de la función de PaaS están configuradas con [servidores DNS administrados de Azure](virtual-networks-name-resolution-for-vms-and-role-instances.md#azure-provided-name-resolution) de forma predeterminada, a menos que configure explícitamente personalizados servidores DNS. Estos servidores DNS proporcionan resolución de nombres interna para máquinas virtuales y las instancias de función que residen en el mismo servicio VNet o en la nube.

Cuando se crea una máquina virtual, se agrega una asignación para el nombre de host a su dirección IP privada a los servidores DNS administrados de Azure. En el caso de una máquina virtual entre varios NIC, el nombre de host se asigna a la dirección IP privada de NIC principal. Sin embargo, esta información de asignación está restringida a recursos dentro del mismo servicio de nube o VNet.

En el caso de un servicio de nube *independiente* , podrá resolver nombres de host de todas las instancias de máquinas virtuales y funciones dentro del mismo servicio de nube solo. En el caso de un servicio de nube dentro de un VNet, podrá resolver nombres de host de todas las instancias de máquinas virtuales y funciones dentro de la VNet.

### <a name="internal-load-balancers-ilb--application-gateways"></a>Equilibradores de carga interno (ILB) y puertas de enlace de aplicación
Puede asignar una dirección IP privada a la configuración de **front-end** de un [Equilibrador de carga interno Azure](../load-balancer/load-balancer-internal-overview.md) (ILB) o una [Puerta de enlace de aplicaciones de Azure](../application-gateway/application-gateway-introduction.md). Esta dirección IP privada actúa como extremo interno, accesible únicamente a los recursos de su red virtual (VNet) y las redes remotas conectada a la VNet. Puede asignar una dirección IP privada estática o dinámica para la configuración de front-end. También puede asignar varias direcciones IP para habilitar escenarios de múltiples vip.

### <a name="at-a-glance"></a>Un vistazo
La siguiente tabla muestra cada tipo de recurso con los métodos de asignación posible (estático o dinámico) y la capacidad de asignar varias direcciones IP.

|Recursos|Dinámico|Estático|Varias direcciones IP|
|---|---|---|---|
|VM (en un servicio de nube *independiente* )|Sí|Sí|Sí|
|Instancia de la función de PaaS (en un servicio de nube *independiente* )|Sí|No|Sí|
|Instancia de rol VM o PaaS (en un VNet)|Sí|Sí|Sí|
|Front-end de equilibrador de carga interno|Sí|Sí|Sí|
|Front-end de puerta de enlace de aplicación|Sí|Sí|Sí|

## <a name="limits"></a>Límites

La siguiente tabla muestra los límites impuestos en IP direccionamiento en Azure por suscripción. Puede [ponerse en contacto con soporte técnico](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para aumentar los límites predeterminados hasta los límites máximos según las necesidades de su empresa.

||Límite predeterminado|Límite máximo|
|---|---|---|
|Direcciones IP públicas (dinámico)|5|Póngase en contacto con soporte técnico|
|Direcciones IP públicas reservadas|20|Póngase en contacto con soporte técnico|
|VIP público por implementación (servicio de nube)|5|Póngase en contacto con soporte técnico|
|VIP privado (ILB) por la implementación (servicio de nube)|1|1|

Asegúrese de que leer el conjunto completo de [los límites de las redes los](azure-subscription-service-limits.md#networking-limits) en Azure.

## <a name="pricing"></a>Precios

En la mayoría de los casos, las direcciones IP públicas son gratuitas. Hay un cargo nominal para usar las direcciones IP públicas adicionales o estáticas. Asegúrese de que entender la [estructura de direcciones IP pública de precios](https://azure.microsoft.com/pricing/details/ip-addresses/).

## <a name="differences-between-resource-manager-and-classic-deployments"></a>Diferencias entre el Administrador de recursos e implementaciones clásicas
A continuación se muestra una comparación de características de direcciones IP en el Administrador de recursos y el modelo de implementación clásico.

||Recursos|Clásico|Administrador de recursos|
|---|---|---|---|
|**Dirección IP pública**|VM|Esto se conoce como una ILPIP (solo dinámico)|Esto se conoce como una dirección IP pública (dinámico o estático)|
|||Asignado a una VM IaaS o una instancia de la función de PaaS|Asociado a NIC de la máquina virtual|
||Equilibrador de carga hacia Internet|Se denomina (dinámica) de la dirección VIP o IP reservada (estático)|Esto se conoce como una dirección IP pública (dinámico o estático)|
|||Asignado a un servicio de nube|Asociado a la configuración de front-end del equilibrador de carga|
||||
|**Dirección IP privada**|VM|Esto se conoce como un DIP|Esto se conoce como una dirección IP privada|
|||Asignado a una VM IaaS o una instancia de la función de PaaS|Asignado a NIC de la máquina virtual|
||Equilibrador de carga interno (ILB)|Asignado a la ILB (dinámico o estático)|Asignado a configuración de front-end de ILB (dinámica o estática)|

## <a name="next-steps"></a>Pasos siguientes
- [Implementar una máquina virtual con una dirección IP privada estática](virtual-networks-static-private-ip-classic-pportal.md) con el portal clásico.
