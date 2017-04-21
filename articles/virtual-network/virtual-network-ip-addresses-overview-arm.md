<properties
   pageTitle="Públicas y privadas asignación de direcciones IP en el Administrador de recursos de Azure | Microsoft Azure"
   description="Obtenga más información sobre públicas y privadas asignación de direcciones IP en el Administrador de recursos de Azure"
   services="virtual-network"
   documentationCenter="na"
   authors="jimdial"
   manager="carmonm"
   editor="tysonn"
   tags="azure-resource-manager" />
<tags
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="04/27/2016"
   ms.author="jdial" />

# <a name="ip-addresses-in-azure"></a>Direcciones IP de Azure
Puede asignar direcciones IP a Azure recursos para comunicarse con otros recursos de Azure, su red local e Internet. Existen dos tipos de direcciones IP que puede usar en Azure:

- **Direcciones IP públicas**: utilizado para la comunicación con Internet, incluidos los servicios de Azure público
- **Direcciones IP privadas**: utilizado para la comunicación dentro de una red virtual Azure (VNet) y, a continuación, en sus instalaciones de red cuando se utiliza una puerta de enlace VPN o circuito ExpressRoute para extender su red en Azure.

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-rm-include.md)][modelo de implementación clásico](virtual-network-ip-addresses-overview-classic.md).

Si está familiarizado con el modelo de implementación clásica, compruebe la [diferencias en entre clásico de direcciones IP y el Administrador de recursos](virtual-network-ip-addresses-overview-classic.md#Differences-between-Resource-Manager-and-classic-deployments).

## <a name="public-ip-addresses"></a>Direcciones IP públicas
Direcciones IP públicas permiten Azure recursos para comunicarse con Internet y servicios de Azure público como [Azure Redis caché](https://azure.microsoft.com/services/cache/), [Hubs de evento de Azure](https://azure.microsoft.com/services/event-hubs/), [bases de datos SQL](../sql-database/sql-database-technical-overview.md)y [almacenamiento de Azure](../storage/storage-introduction.md).

En el Administrador de recursos de Azure, una dirección [IP pública](resource-groups-networking.md#public-ip-address) es un recurso que tiene sus propias propiedades. Puede asociar un recurso de dirección IP público con cualquiera de los siguientes recursos:

- Máquinas virtuales (VM)
- Equilibradores de carga a través de Internet
- Puertas de enlace VPN
- Puertas de enlace de aplicación

### <a name="allocation-method"></a>Método de asignación
Existen dos métodos en el que se asigna una dirección IP a un recurso IP *pública* - *dinámico* o *estático*. El método de asignación predeterminado es *dinámico*, donde una dirección IP **no** está asignada en el momento de su creación. En su lugar, se asigna la dirección IP pública al iniciar (o crear) el recurso asociado (por ejemplo, un equilibrador de carga o VM). La dirección IP se libera cuando detener (o eliminar) del recurso. Hace que la dirección IP cambiar al detener e iniciar un recurso.

Para asegurarse de que la dirección IP para el recurso asociado es el mismo, puede establecer el método de asignación de explícitamente en *estático*. En este caso una dirección IP se asigna inmediatamente. Se publica cuando se elimine el recurso o cambiar su método de asignación en *dinámico*.

>[AZURE.NOTE] Incluso cuando se establece el método de asignación de *estático*, no puede especificar la dirección IP asignada al *recurso IP pública*. En su lugar, se obtiene asignada desde un grupo de direcciones IP disponibles en el recurso se crea en la ubicación de Azure.

Direcciones IP públicas normalmente se usan en las situaciones siguientes:

- Los usuarios finales que necesite actualizar las reglas de firewall para comunicarse con los recursos de Azure.
- Resolución de nombres DNS, donde un cambio en la dirección IP requeriría los registros.
- Los recursos de Azure comunican con otras aplicaciones o servicios que utilizan un modelo de seguridad basada en la dirección IP.
- Usar certificados SSL vinculados a una dirección IP.

>[AZURE.NOTE] La lista de intervalos de IP desde la que se asignan direcciones IP públicas (dinámico o estático) a recursos de Azure se publica en [intervalos IP del centro de datos de Azure](https://www.microsoft.com/download/details.aspx?id=41653).

### <a name="dns-hostname-resolution"></a>Resolución de nombre de host DNS
Puede especificar una etiqueta de nombre de dominio DNS para un recurso IP pública, que se crea una asignación para *domainnamelabel*. *ubicación*. cloudapp.azure.com a la dirección IP pública en los servidores DNS administrados de Azure. Por ejemplo, si crea un recurso IP público con **contoso** como un *domainnamelabel* de **Estados Unidos oeste** Azure *ubicación*, resolverá el de dominio completo (FQDN) de nombre **contoso.westus.cloudapp.azure.com** a la dirección IP pública del recurso. Puede usar este FQDN para crear un registro CNAME de dominio personalizado que apunta a la dirección IP pública de Azure.

>[AZURE.IMPORTANT] Cada etiqueta de nombre de dominio creado debe ser único dentro de su ubicación de Azure.  

### <a name="virtual-machines"></a>Máquinas virtuales de Windows
Puede asociar una dirección IP pública con un [Windows](../virtual-machines/virtual-machines-windows-about.md) o [Linux](../virtual-machines/virtual-machines-linux-about.md) VM asignando a su **interfaz de red**. En el caso de una interfaz de red múltiples VM, puede asignar a la interfaz *principal* de red. Puede asignar un dinámico o una dirección IP pública estática a una máquina virtual.

### <a name="internet-facing-load-balancers"></a>Equilibradores de carga a través de Internet
Puede asociar una dirección IP pública con un [Equilibrador de carga de Azure](../load-balancer/load-balancer-overview.md), asignando a la configuración de **front-end** del equilibrador de carga. Esta dirección IP pública sirve como una equilibrio de carga dirección IP virtual (VIP). Puede asignar un dinámico o una dirección IP pública estática a un equilibrador de carga front-end. También puede asignar varias direcciones IP públicas a un equilibrador de carga front-end, que permite escenarios de [Múltiples VIP](../load-balancer/load-balancer-multivip.md) como un entorno de múltiples arrendatario con sitios Web basados en SSL.

### <a name="vpn-gateways"></a>Puertas de enlace VPN
[Puerta de enlace de Azure VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md) se usa para conectarse a una red virtual Azure (VNet) a otros VNets de Azure o a una red local. Debe asignar una dirección IP pública a su **configuración de IP** para permitirle comunicarse con la red de forma remota. Actualmente, sólo puede asignar una dirección IP pública *dinámica* a una puerta de enlace VPN.

### <a name="application-gateways"></a>Puertas de enlace de aplicación
Puede asociar una dirección IP pública con una [Puerta de enlace de aplicación](../application-gateway/application-gateway-introduction.md)de Azure, asignando a la configuración de **cliente** de la puerta de enlace. Esta dirección IP pública sirve como VIP equilibrio de carga. Actualmente, sólo puede asignar una dirección IP pública *dinámica* en una configuración de front-end de puerta de enlace de la aplicación.

### <a name="at-a-glance"></a>Un vistazo
La siguiente tabla muestra la propiedad específica a través del cual se puede asociar a un recurso de nivel superior y los métodos de asignación posible (dinámicos o estáticos) que se pueden usar una dirección IP pública.

|Recursos de nivel superior|Asociación de dirección IP|Dinámico|Estático|
|---|---|---|---|
|Máquina virtual|Interfaz de red|Sí|Sí|
|Equilibrador de carga|Configuración de front-end|Sí|Sí|
|Puerta de enlace VPN|Configuración de puerta de enlace IP|Sí|No|
|Puerta de enlace de aplicación|Configuración de front-end|Sí|No|

## <a name="private-ip-addresses"></a>Direcciones IP privadas
Direcciones IP privadas permiten Azure recursos para comunicarse con otros recursos en una [red virtual](virtual-networks-overview.md) o una red local a través de una puerta de enlace VPN o circuito ExpressRoute, sin usar una dirección IP de Internet accesible.

En el modelo de implementación de administrador de recursos de Azure, una dirección IP privada está asociada a los siguientes tipos de recursos de Azure:

- Máquinas virtuales
- Equilibradores de carga interno (ILBs)
- Puertas de enlace de aplicación

### <a name="allocation-method"></a>Método de asignación
Se asigna una dirección IP privada del intervalo de direcciones de la subred a la que está conectado al recurso. El intervalo de direcciones de la propia subred es una parte del intervalo de direcciones de VNet.

Existen dos métodos en el que se asigna una dirección IP privada: *estática*o *dinámica* . El método de asignación predeterminado es *dinámico*, donde se asigna automáticamente a la dirección IP de subred del recurso (con DHCP). Puede cambiar esta dirección IP al detener e iniciar el recurso.

Puede establecer el método de asignación en *estático* para asegurarse de que la dirección IP se mantiene igual. En este caso, también debe proporcionar una dirección IP que forma parte de subred del recurso.

Direcciones IP privadas se usan normalmente para:

- Máquinas virtuales que actúen como controladores de dominio o los servidores DNS.
- Recursos que requieren reglas de firewall mediante direcciones IP.
- Recursos que se tiene acceso a otros recursos de aplicaciones o a través de una dirección IP.

### <a name="virtual-machines"></a>Máquinas virtuales de Windows
Una dirección IP privada se asigna a la **interfaz de red** de un [Windows](../virtual-machines/virtual-machines-windows-about.md) o [Linux](../virtual-machines/virtual-machines-linux-about.md) VM. En el caso de una interfaz de red múltiples VM, cada interfaz obtiene una dirección IP privada asignada. Puede especificar el método de asignación como dinámico o estático para una interfaz de red.

#### <a name="internal-dns-hostname-resolution-for-vms"></a>Resolución de nombre de host DNS interna (para máquinas virtuales)
Todas las máquinas virtuales de Azure estén configuradas con [servidores DNS administrados de Azure](virtual-networks-name-resolution-for-vms-and-role-instances.md#azure-provided-name-resolution) de forma predeterminada, a menos que configure explícitamente personalizados servidores DNS. Estos servidores DNS proporcionan resolución de nombres interno para máquinas virtuales que residen en el mismo VNet.

Cuando se crea una máquina virtual, se agrega una asignación para el nombre de host a su dirección IP privada a los servidores DNS administrados de Azure. En el caso de una interfaz de red múltiples VM, el nombre de host se asigna a la dirección IP privada de la interfaz de red principal.

Máquinas virtuales configuradas con servidores DNS administrados Azure podrán resolver los nombres de host de todas las máquinas virtuales dentro de su VNet a sus direcciones IP.

### <a name="internal-load-balancers-ilb--application-gateways"></a>Equilibradores de carga interno (ILB) y puertas de enlace de aplicación
Puede asignar una dirección IP privada a la configuración de **front-end** de un [Equilibrador de carga interno Azure](../load-balancer/load-balancer-internal-overview.md) (ILB) o una [Puerta de enlace de aplicaciones de Azure](../application-gateway/application-gateway-introduction.md). Esta dirección IP privada actúa como extremo interno, accesible únicamente a los recursos de su red virtual (VNet) y las redes remotas conectada a la VNet. Puede asignar una dirección IP privada estática o dinámica para la configuración de front-end.

### <a name="at-a-glance"></a>Un vistazo
La siguiente tabla muestra la propiedad específica a través del cual se puede asociar a un recurso de nivel superior y los métodos de asignación posible (dinámicos o estáticos) que se pueden usar una dirección IP privada.

|Recursos de nivel superior|Asociación de dirección IP|Dinámico|Estático|
|---|---|---|---|
|Máquina virtual|Interfaz de red|Sí|Sí|
|Equilibrador de carga|Configuración de front-end|Sí|Sí|
|Puerta de enlace de aplicación|Configuración de front-end|Sí|Sí|

## <a name="limits"></a>Límites

En el conjunto completo de [los límites de las redes los](azure-subscription-service-limits.md#networking-limits) en Azure se indican los límites impuestos en direcciones IP. Estos límites son por región y por suscripción. Puede [ponerse en contacto con soporte técnico](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para aumentar los límites predeterminados hasta los límites máximos según las necesidades de su empresa.

## <a name="pricing"></a>Precios

Direcciones IP públicas pueden tener un cargo nominal. Para obtener más información sobre precios de dirección IP en Azure, revise la página de [precios de dirección IP](https://azure.microsoft.com/pricing/details/ip-addresses) .

## <a name="next-steps"></a>Pasos siguientes
- [Implementar una máquina virtual con una dirección IP pública estática con el portal de Azure](virtual-network-deploy-static-pip-arm-portal.md)
- [Implementar una máquina virtual con una dirección IP pública estática usando una plantilla](virtual-network-deploy-static-pip-arm-template.md)
- [Implementar una máquina virtual con una dirección IP privada estática con el portal de Azure](virtual-networks-static-private-ip-arm-pportal.md)
