<properties
   pageTitle="Guía de diseño y Plan de red Virtual Azure (VNet) | Microsoft Azure"
   description="Obtenga información sobre cómo planear y diseñar redes virtuales en Azure según sus requisitos de aislamiento, la conectividad y la ubicación."
   services="virtual-network"
   documentationCenter="na"
   authors="jimdial"
   manager="carmonm"
   editor="tysonn" />
<tags
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/08/2016"
   ms.author="jdial" />

# <a name="plan-and-design-azure-virtual-networks"></a>Planear y diseñar redes virtuales de Azure

Crear un VNet para experimentar con es bastante fácil, pero lo más probable es que va a implementar varias VNets lo largo del tiempo para satisfacer las necesidades de producción de su organización. Con algunos planificación y diseño, podrá implementar VNets y conectar los recursos que necesita de manera más eficaz. Si no está familiarizado con VNets, es recomendable que [Obtenga más información sobre VNets](virtual-networks-overview.md) y [cómo implementar](virtual-networks-create-vnet-arm-pportal.md) uno antes de continuar. 

## <a name="plan"></a>Plan

Comprender las suscripciones de Azure, regiones y recursos de la red es fundamental para tener éxito. Puede usar la lista de consideraciones debajo como punto de partida. Una vez que comprenda las consideraciones, puede definir los requisitos para el diseño de la red.

### <a name="considerations"></a>Consideraciones

Antes de responder a la planificación preguntas a continuación, considere lo siguiente:

- Todo lo que cree en Azure se compone de uno o más recursos. Una máquina virtual (VM) es un recurso, la interfaz de adaptador de red (NIC) usada por una máquina virtual es un recurso, la dirección IP pública utiliza una NIC es un recurso, la VNet la NIC está conectada a es un recurso.
- Crear recursos dentro de una [región de Azure](https://azure.microsoft.com/regions/#services) y de suscripción. Y recursos solo se pueden conectar a un VNet que existe en la configuración regional y de suscripción están en mismo. 
- Puede conectar VNets entre sí mediante una [Puerta de enlace VPN](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md)de Azure. También puede conectar VNets entre regiones y las suscripciones de esta manera.
- VNets puede conectarse a su red local mediante una de las [Opciones de conectividad](../vpn-gateway/vpn-gateway-about-vpngateways.md#site-to-site-and-multi-site) disponibles en Azure. 
- Diferentes recursos se pueden agrupar en [grupos de recursos](../azure-resource-manager/resource-group-overview.md#resource-groups), lo que sea más fácil administrar el recurso como una unidad. Un grupo de recursos puede contener recursos de varias regiones, como los recursos pertenecen a la misma suscripción.

### <a name="define-requirements"></a>Definir los requisitos

Use las siguientes preguntas como punto de partida para el diseño de la red de Azure.  

1. ¿Qué ubicaciones de Azure usará al host VNets?
2. ¿Necesita proporcionan comunicación entre estas ubicaciones Azure?
3. ¿Necesita proporcionan comunicación entre su VNet(s) de Azure y su datacenter(s) local?
4. ¿Servicios de nube de infraestructura de cuántos como una VM de servicio (IaaS), funciones y hacer de aplicaciones web que necesita para la solución?
5. ¿Necesita aislar el tráfico basado en grupos de máquinas virtuales (es decir, front-end servidores de web y base de datos back-end)?
6. ¿Necesita controlar el flujo del tráfico con dispositivos virtuales?
7. ¿Necesitan los usuarios de diferentes conjuntos de permisos a los recursos de Azure diferentes?

### <a name="understand-vnet-and-subnet-properties"></a>Comprender las propiedades VNet y subred

Recursos VNet y subredes ayudan a definir un límite de seguridad para cargas de trabajo que se ejecuta en Azure. Una VNet se caracteriza por una colección de espacios de direcciones, definido como bloques CIDR. 

>[AZURE.NOTE] Los administradores de red están familiarizados con la notación CIDR. Si no está familiarizado con CIDR, [obtener más información acerca de él](http://whatismyipaddress.com/cidr).

VNets contener las siguientes propiedades.

|(Propiedad)|Descripción|Restricciones|
|---|---|---|
|**nombre**|Nombre de VNet|Cadena de caracteres hasta 80. Puede contener letras, números, subrayado, puntos o guiones. Debe comenzar con una letra o un número. Debe terminar con una letra, número o subrayado. Puede contener letras en mayúsculas o minúsculas.|  
|**ubicación**|Ubicación de Azure (también conocida como región).|Debe ser una de las ubicaciones de Azure válidas.|
|**addressSpace**|Colección de prefijos de direcciones que conforman la VNet en la notación CIDR.|Debe ser una matriz de bloques de dirección CIDR válidos, incluidos los intervalos de direcciones IP públicas.|
|**subredes**|Colección de subredes que conforman la VNet|Consulte la tabla de propiedades de subred siguiente.||
|**dhcpOptions**|Objeto que contiene una sola propiedad requerido denominado **dnsServers**.||
|**dnsServers**|Matriz de servidores DNS utilizados por la VNet. Si no se especifica ningún servidor, se utiliza la resolución de nombres internos Azure.|Debe ser una matriz de hasta 10 servidores DNS, por dirección IP.| 

Una subred es un recurso secundario de una VNet y ayuda a define segmentos de espacios de direcciones dentro de un bloque CIDR con prefijos de direcciones IP. NIC se pueden agregar a subredes y conectadas a máquinas virtuales, que permite la conectividad de varias cargas de trabajo.

Subredes contengan las siguientes propiedades. 

|(Propiedad)|Descripción|Restricciones|
|---|---|---|
|**nombre**|Nombre de subred|Cadena de caracteres hasta 80. Puede contener letras, números, subrayado, puntos o guiones. Debe comenzar con una letra o un número. Debe terminar con una letra, número o subrayado. Puede contener letras en mayúsculas o minúsculas.|
|**ubicación**|Ubicación de Azure (también conocida como región).|Debe ser una de las ubicaciones de Azure válidas.|
|**addressPrefix**|Prefijo de dirección único que conforman la subred en notación CIDR|Debe ser un único bloque CIDR que forma parte de uno de los espacios de direcciones de VNet.|
|**networkSecurityGroup**|GSN aplicado a la subred|consulte [NSGs](resource-groups-networking.md#Network-Security-Group)|
|**routeTable**|Tabla de ruta que se aplican a la subred|consulte [UDR](resource-groups-networking.md#Route-table)|
|**ipConfigurations**|Colección de objetos de configuración de IP utilizado NIC conectadas a la subred|ver [la configuración de IP](../resource-groups-networking.md#IP-configurations)|

### <a name="name-resolution"></a>Resolución de nombres

De forma predeterminada, se utiliza el VNet [resolución de nombre proporcionado Azure.](virtual-networks-name-resolution-for-vms-and-role-instances.md#Azure-provided-name-resolution) Para resolver los nombres dentro de la VNet y en Internet. Sin embargo, si su VNets se conecta a los centros de datos local, debe proporcionar [su propio servidor DNS](virtual-networks-name-resolution-for-vms-and-role-instances.md#Name-resolution-using-your-own-DNS-server) para resolver los nombres de sus redes.  

### <a name="limits"></a>Límites

Revise el artículo [Azure limita](../azure-subscription-service-limits.md#networking-limits) para asegurarse de que el diseño no entran en conflicto con cualquiera de los límites de los límites de redes. Algunos límites se pueden aumentar abriendo una incidencia de soporte técnico.

### <a name="role-based-access-control-rbac"></a>Control de acceso basado en roles (RBAC)

Puede usar [Azure RBAC](../active-directory/role-based-access-built-in-roles.md) para controlar el nivel de acceso a diferentes usuarios puedan tener a diferentes recursos en Azure. De esta forma puede dividir el trabajo realizado por el equipo según sus necesidades. 

En cuanto a redes virtuales, los usuarios del rol de **Colaborador de red** tienen control total sobre los recursos de una red virtual del Administrador de recursos de Azure. Del mismo modo, los usuarios del rol de **Colaborador clásico de red** tienen control total sobre los recursos de una red virtual clásico.

>[AZURE.NOTE] También puede [crear sus propias funciones](../active-directory/role-based-access-control-configure.md) para separar sus necesidades administrativas.

## <a name="design"></a>Diseño

Una vez que conozca las respuestas a las preguntas en la sección [Plan](#Plan) , revise lo siguiente antes de definir su VNets.

### <a name="number-of-subscriptions-and-vnets"></a>Número de suscripciones y VNets

Considere la posibilidad de crear varias VNets en las situaciones siguientes:

- **Máquinas virtuales que deben colocarse en diferentes ubicaciones de Azure**. VNets en Azure son regionales. No pueden abarcar ubicaciones. Por lo tanto, deberá VNet al menos una para cada Azure ubicación que desee en máquinas virtuales de host.
- **Cargas de trabajo que necesitan estar completamente aisladas entre sí**. Puede crear VNets independiente, que incluso utilizan los mismos espacios de direcciones IP, para aislar diferentes cargas de trabajo entre sí. 

Tenga en cuenta que los límites que vea encima están por región por suscripción. Esto significa que puede utilizar varias suscripciones para aumentar el límite de recursos que puede mantener en Azure. Puede usar una VPN de sitio a sitio o un circuito de ExpressRoute para conectar VNets en diferentes suscripciones.

### <a name="subscription-and-vnet-design-patterns"></a>Patrones de diseño de VNet y suscripción

La siguiente tabla muestra algunos patrones de diseño comunes para el uso de las suscripciones y VNets.

|Escenario|Diagrama|Los profesionales de TI|Desventajas|
|---|---|---|---|
|Sola suscripción, dos VNets por aplicación|![Sola suscripción](./media/virtual-network-vnet-plan-design-arm/figure1.png)|Solo una suscripción para administrar.|Número máximo de VNets por región Azure. Necesita más suscripciones después de eso. Revise el artículo de [Azure limita](../azure-subscription-service-limits.md#networking-limits) para obtener información detallada.|
|Una suscripción por aplicación, dos VNets por aplicación|![Sola suscripción](./media/virtual-network-vnet-plan-design-arm/figure2.png)|Usa sólo dos VNets por suscripción.|Más difícil de administrar cuando hay demasiadas aplicaciones.|
|Una suscripción por unidad de negocio, dos VNets por la aplicación.|![Sola suscripción](./media/virtual-network-vnet-plan-design-arm/figure3.png)|Equilibrio entre el número de suscripciones y VNets.|Número máximo de VNets por unidad de negocio (suscripción). Revise el artículo de [Azure limita](../azure-subscription-service-limits.md#networking-limits) para obtener información detallada.|
|Una suscripción por unidad de negocio, dos VNets por grupo de aplicaciones.|![Sola suscripción](./media/virtual-network-vnet-plan-design-arm/figure4.png)|Equilibrio entre el número de suscripciones y VNets.|Aplicaciones deben ser aisladas mediante subredes y NSGs.|


### <a name="number-of-subnets"></a>Número de subredes

Debe tener en cuenta varias subredes en un VNet en las situaciones siguientes:

- **No hay suficiente direcciones IP privadas para todas las NIC en una subred**. Si el espacio de direcciones de subred no contiene suficientes direcciones IP para el número de NIC en la subred, debe crear varias subredes. Tenga en cuenta que Azure reserva 5 direcciones IP de cada subred que no se pueden usar: las direcciones y el apellido del espacio de direcciones (para la dirección de subred y multidifusión) y 3 direcciones para usarse internamente (con fines DHCP y DNS). 
- **Seguridad**. Puede utilizar subredes para separar los grupos de máquinas virtuales entre sí para cargas de trabajo que tienen una estructura de varias capa y aplicar diferentes [grupos de seguridad de red (NSGs)](virtual-networks-nsg.md#subnets) para esas subredes.
- **Conectividad de híbrido**. Puede utilizar puertas de enlace VPN y circuitos ExpressRoute para [Conectar](../vpn-gateway/vpn-gateway-about-vpngateways.md#site-to-site-and-multi-site) su VNets entre sí y a su centers de datos local. Puertas de enlace VPN y circuitos ExpressRoute requieren una subred propia que debe crearse.
- **Equipos virtuales**. Puede usar un dispositivo virtual, como un firewall, Acelerador WAN o puerta de enlace VPN en un VNet de Azure. Cuando lo haga, necesita [enrutar el tráfico](virtual-networks-udr-overview.md) a dichos dispositivos y aislar a ellos en su propia subred.

### <a name="subnet-and-nsg-design-patterns"></a>Subred y patrones de diseño GSN

La siguiente tabla muestra algunos patrones de diseño comunes para el uso de subredes.

|Escenario|Diagrama|Los profesionales de TI|Desventajas|
|---|---|---|---|
|Subred única, NSGs por nivel de aplicación, por aplicación|![Subred única](./media/virtual-network-vnet-plan-design-arm/figure5.png)|Solo una subred para administrar.|Varios NSGs necesarios para aislar cada aplicación.|
|Una subred por aplicación, NSGs por nivel de aplicación|![Subred por aplicación](./media/virtual-network-vnet-plan-design-arm/figure6.png)|NSGs menos para administrar.|Varias subredes para administrar.|
|Una subred por nivel de aplicación, NSGs por la aplicación.|![Subred por capa](./media/virtual-network-vnet-plan-design-arm/figure7.png)|Equilibrio entre el número de subredes y NSGs.|Número máximo de NSGs por suscripción. Revise el artículo de [Azure limita](../azure-subscription-service-limits.md#networking-limits) para obtener información detallada.|
|Una subred por nivel de aplicación, por aplicación, NSGs por subred|![Subred por capa por aplicación](./media/virtual-network-vnet-plan-design-arm/figure8.png)|Posiblemente menor número de NSGs.|Varias subredes para administrar.|

## <a name="sample-design"></a>Ejemplo de diseño

Para ilustrar la aplicación de la información en este artículo, considere el siguiente escenario.

Trabajar de una compañía que tiene 2 centros de datos de Norteamérica y dos centros de datos Europe. Identifica 6 diferentes hacia las aplicaciones cliente mantenidas 2 diferentes unidades de negocio que desea migrar a Azure como un programa piloto. La arquitectura básica para las aplicaciones son las siguientes:

- App1, App2, App3 y App4 son aplicaciones web hospedadas en servidores Linux que ejecutan Ubuntu. Cada aplicación conecta a un servidor de una aplicación independiente que hospeda REST servicios en servidores Linux. Los servicios de REST conectan a una base de datos de back-end MySQL.
- App5 y App6 son aplicaciones web alojadas en servidores de Windows que ejecutan Windows Server 2012 R2. Cada aplicación se conecta a una base de datos de SQL Server back-end.
- Todas las aplicaciones se hospedan actualmente en uno de los centros de datos de la empresa en América del Norte.
- Los centros de datos local utilizan el espacio de direcciones 10.0.0.0/8.

Debe diseñar una solución de red virtual que cumple los siguientes requisitos:

- Cada unidad de negocio no se debería afectada por consumo de recursos de otras empresas.
- Debe reducir la cantidad de VNets y subredes para facilitar la administración.
- Cada unidad de negocio debe tener un sola prueba y desarrollo VNet utilizado para todas las aplicaciones.
- Cada aplicación se hospeda en los centros de datos de Azure distintos 2 por continente (Europa y América del Norte).
- Cada aplicación es completamente aislada entre sí.
- Cada aplicación puede tener acceso a los clientes a través de Internet utilizando HTTP.
- Cada aplicación puede tener acceso a los usuarios que se conecta a los centros de datos locales a través de un túnel cifrado.
- Conexión a los centros de datos local debe utilizar dispositivos VPN existentes.
- Grupo de redes de la empresa debe tener un control completo sobre la configuración de VNet.
- Los desarrolladores de cada unidad de negocio solo debería poder implementar máquinas virtuales en subredes existentes.
- Se migrará todas las aplicaciones que aparecen en Azure (elevación y MAYÚS).
- Las bases de datos en cada ubicación deben replicar en otras ubicaciones Azure una vez al día.
- 5 servidores de web front-end, 2 servidores de aplicaciones (si es necesario) y 2 servidores de base de datos, debe usar cada aplicación.

### <a name="plan"></a>Plan

Debe iniciar el diseño de la planificación al responder a la pregunta en la sección [definir requisitos](#Define-requirements) tal como se muestra a continuación.

1. ¿Qué ubicaciones de Azure usará al host VNets?

    2 dirección en América del Norte y 2 lugares en Europa. Debe elegirlos basados en la ubicación física de los centros de datos local existente. De este modo la conexión de las ubicaciones de físicas a Azure tendrá una latencia mejor.

2. ¿Necesita proporcionan comunicación entre estas ubicaciones Azure?

    Sí. Dado que las bases de datos deben replicarse en todas las ubicaciones.

3. ¿Necesita proporcionan comunicación entre su VNet(s) de Azure y su local data centers?

    Sí. Puesto que los usuarios conectados a los centros de datos local debe tener acceso a las aplicaciones a través de un túnel cifrado.
 
4. ¿Cuántas máquinas virtuales de IaaS necesita para la solución?

    Máquinas virtuales de IaaS 200. App1 y App2, App3 requieren 5 servidores web, 2 servidores aplicaciones y los servidores de base de datos 2. Ese es el total de 9 máquinas virtuales de IaaS por aplicación o 36 máquinas virtuales de IaaS. App5 y App6 requieren los servidores web 5 y 2 servidores de base de datos. Ese es el total de 7 máquinas virtuales de IaaS por aplicación o máquinas virtuales de IaaS 14. Por lo tanto, necesita 50 máquinas virtuales de IaaS para todas las aplicaciones de cada región de Azure. Debido a que necesitamos usar 4 regiones, habrá máquinas virtuales de IaaS 200.

    También debe proporcionar los servidores DNS en cada VNet o en los centros de datos local para resolver el nombre entre sus máquinas virtuales IaaS de Azure y su red local. 

5. ¿Necesita aislar el tráfico basado en grupos de máquinas virtuales (es decir, front-end servidores de web y base de datos back-end)?

    Sí. Cada aplicación debe ser completamente aislada entre ellas, y cada nivel de aplicación también debe aislado. 

6. ¿Necesita controlar el flujo del tráfico con dispositivos virtuales?

    No. Dispositivos virtuales pueden usarse para proporcionar más control sobre el flujo del tráfico, incluido el registro de plano de datos más detallado. 

7. ¿Necesitan los usuarios de diferentes conjuntos de permisos a los recursos de Azure diferentes?

    Sí. El equipo de red necesita control total sobre la configuración de red virtual, mientras los desarrolladores solo podrán implementar sus VM subredes ya existentes. 

### <a name="design"></a>Diseño

Debe seguir el diseño que especifica suscripciones, VNets, subredes y NSGs. Trataremos NSGs aquí, pero debería obtener más información acerca de [NSGs](virtual-networks-nsg.md) antes de finalizar el diseño.

**Número de suscripciones y VNets**

Los siguientes requisitos relacionados con las suscripciones y VNets:

- Cada unidad de negocio no se debería afectada por consumo de recursos de otras empresas.
- Debe reducir la cantidad de VNets y subredes.
- Cada unidad de negocio debe tener un sola prueba y desarrollo VNet utilizado para todas las aplicaciones.
- Cada aplicación se hospeda en los centros de datos de Azure distintos 2 por continente (Europa y América del Norte).

En función de dichos requisitos, necesita una suscripción para cada unidad de negocio. De este modo, consumo de recursos de una empresa no se contabilizan en límites de otras empresas. Y como desea reducir el número de VNets, considere la posibilidad de utilizando el modelo de **una suscripción por unidad de negocio, dos VNets por grupo de aplicaciones** , tal como se muestra a continuación.

![Sola suscripción](./media/virtual-network-vnet-plan-design-arm/figure9.png)

También debe especificar el espacio de direcciones para cada VNet. Puesto que necesita centros de conectividad entre los datos locales y las regiones Azure, no puede entrar en conflicto el espacio de direcciones utilizado para Azure VNets con la red local y el espacio de direcciones utilizado por cada VNet no debe entrar en conflicto con otros VNets existente. Puede utilizar los espacios de direcciones en la tabla siguiente para cumplir estos requisitos.  

|**Suscripción**|**VNet**|**Región de Azure**|**Espacio de direcciones**|
|---|---|---|---|
|BU1|ProdBU1US1|Estados Unidos occidental|172.16.0.0/16|
|BU1|ProdBU1US2|Estados Unidos oriental|172.17.0.0/16|
|BU1|ProdBU1EU1|Europa del Norte|172.18.0.0/16|
|BU1|ProdBU1EU2|Europa occidental|172.19.0.0/16|
|BU1|TestDevBU1|Estados Unidos occidental|172.20.0.0/16|
|BU2|TestDevBU2|Estados Unidos occidental|172.21.0.0/16|
|BU2|ProdBU2US1|Estados Unidos occidental|172.22.0.0/16|
|BU2|ProdBU2US2|Estados Unidos oriental|172.23.0.0/16|
|BU2|ProdBU2EU1|Europa del Norte|172.24.0.0/16|
|BU2|ProdBU2EU2|Europa occidental|172.25.0.0/16|

**Número de subredes y NSGs**

Los siguientes requisitos relacionados con subredes y NSGs:

- Debe reducir la cantidad de VNets y subredes.
- Cada aplicación es completamente aislada entre sí.
- Cada aplicación puede tener acceso a los clientes a través de Internet utilizando HTTP.
- Cada aplicación puede tener acceso a los usuarios que se conecta a los centros de datos locales a través de un túnel cifrado.
- Conexión a los centros de datos local debe utilizar dispositivos VPN existentes.
- Las bases de datos en cada ubicación deben replicar en otras ubicaciones Azure una vez al día.

En función de dichos requisitos, podría usar una subred por nivel de aplicación y usar NSGs para filtrar el tráfico por aplicación. De este modo, solo tiene una GSN por aplicación y por subred y 3 subredes en cada VNet (front-end de nivel de aplicación y capa de datos). En este caso, debe considerar usar el modelo de diseño de **una subred por nivel de aplicación, NSGs por la aplicación** . La figura siguiente muestra el uso del patrón de diseño que representa el VNet **ProdBU1US1** .

![Una subred por capa, uno GSN por aplicación y por capas](./media/virtual-network-vnet-plan-design-arm/figure11.png)

Sin embargo, también debe crear una subred adicional para la conectividad VPN entre el VNets y los centros de datos local. Y debe especificar el espacio de direcciones para cada subred. La siguiente ilustración muestra un ejemplo de solución de **ProdBU1US1** VNet. ¿Replicar este escenario para cada VNet. Cada color representa una aplicación distinta.

![Ejemplo VNet](./media/virtual-network-vnet-plan-design-arm/figure10.png)

**Control de acceso**

Los siguientes requisitos relacionados con control de acceso:

- Grupo de redes de la empresa debe tener un control completo sobre la configuración de VNet.
- Los desarrolladores de cada unidad de negocio solo debería poder implementar máquinas virtuales en subredes existentes.

Basándose en dichos requisitos, puede agregar los usuarios desde el equipo de redes para el rol de **Colaborador de red** integrado en cada suscripción; y crear una función personalizada para los desarrolladores de aplicaciones en cada suscripción concederles derechos para agregar máquinas virtuales a subredes existentes.

## <a name="next-steps"></a>Pasos siguientes

- [Implementar una red virtual](virtual-networks-create-vnet-arm-template-click.md) basada en un escenario.
- Entender cómo máquinas virtuales de IaaS de [Equilibrio de carga](../load-balancer/load-balancer-overview.md) y [administrar el enrutamiento en múltiples regiones de Azure](../traffic-manager/traffic-manager-overview.md).
- Más información sobre [NSGs y cómo planear y diseñar](virtual-networks-nsg.md) una solución GSN.
- Más información sobre la [cruz local y opciones de conectividad de VNet](../vpn-gateway/vpn-gateway-about-vpngateways.md#site-to-site-and-multi-site).
