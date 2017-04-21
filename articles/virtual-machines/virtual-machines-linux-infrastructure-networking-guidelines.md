<properties
    pageTitle="Directrices de infraestructura de red | Microsoft Azure"
    description="Obtenga información sobre las directrices de diseño e implementación claves para implementar una red virtual en servicios de infraestructura de Azure."
    documentationCenter=""
    services="virtual-machines-linux"
    authors="iainfoulds"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/08/2016"
    ms.author="iainfou"/>

# <a name="networking-infrastructure-guidelines"></a>Directrices de infraestructura de red

[AZURE.INCLUDE [virtual-machines-linux-infrastructure-guidelines-intro](../../includes/virtual-machines-linux-infrastructure-guidelines-intro.md)] 

En este artículo se centra en la descripción de los pasos de diseño necesarios para una red virtual dentro de Azure y conectividad entre entornos local.


## <a name="implementation-guidelines-for-virtual-networks"></a>Directrices de implementación de redes virtuales

Decisiones:

- ¿Qué tipo de red virtual necesita hospedar su carga de trabajo de TI o infraestructura (solo nube o entre local)?
- ¿Para las redes virtuales entre local, cuánto espacio de direcciones debe hospedar las subredes y máquinas virtuales ahora y razonable expansión en el futuro?
- ¿Va a crear redes virtuales centralizadas o crear redes virtuales individuales para cada grupo de recursos?

Tareas:

- Definir el espacio de direcciones para las redes virtuales que debe crearse.
- Definir el conjunto de subredes y el espacio de direcciones para cada uno.
- Para las instalaciones entre redes virtuales, definir el conjunto de espacios de direcciones de la red local para las ubicaciones de local las máquinas virtuales de la red virtual necesitan comunicarse.
- Trabajo con local está configurado el equipo de red para asegurarse de que el enrutamiento adecuado al crear redes virtuales de entre-local.
- Crear la red virtual utilizando la convención de nomenclatura.


## <a name="virtual-networks"></a>Redes virtuales

Redes virtuales son necesarias para admitir comunicaciones entre máquinas virtuales (VM). Puede definir subredes, direcciones IP personalizadas, configuración de DNS, filtrado de seguridad y equilibrio de carga, al igual que con redes físicas. Mediante una [Puerta de enlace VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md) o la [ruta de Express circuito](../expressroute/expressroute-introduction.md), puede conectar Azure redes virtuales con sus redes local. Puede obtener más información acerca de [las redes virtuales y sus componentes](../virtual-network/virtual-networks-overview.md).

Mediante grupos de recursos, dispone de flexibilidad en el diseño de los componentes de red virtual. Máquinas virtuales pueden conectarse a redes virtuales fuera de su propio grupo de recursos. Un enfoque de diseño común sería crear grupos de recursos centralizada que contienen la infraestructura de red principal que se puede administrar un grupo comunes. Máquinas virtuales y las aplicaciones que se implementa en grupos de recursos independientes. Este enfoque permite el acceso de los propietarios de aplicación al grupo de recursos que contenga sus VM sin abrir el acceso a la configuración de los recursos red virtuales más anchos.

## <a name="site-connectivity"></a>Conectividad de sitio

### <a name="cloud-only-virtual-networks"></a>Redes virtuales solo nube
Si los usuarios locales y equipos no requieren conectividad en curso en máquinas virtuales de una red virtual Azure, el diseño de la red virtual es sencillo:

![Diagrama de red básica de virtual solo nube](./media/virtual-machines-common-infrastructure-service-guidelines/vnet01.png)

Este enfoque suele para cargas de trabajo a través de Internet, como un servidor web de Internet. Puede administrar estos máquinas virtuales con SSH o conexiones de punto a sitio VPN.

Ya no se conecta a su red local, redes virtuales solo Azure pueden usar cualquier parte del espacio de direcciones IP privado. El espacio de direcciones puede ser el mismo espacio privado que está en uso en local.


### <a name="cross-premises-virtual-networks"></a>Redes virtuales entre local
Si los equipos y usuarios locales requieren conectividad en curso en máquinas virtuales de una red virtual Azure, crear una red virtual de cruz local. Conectarse a su red local con una conexión de VPN de sitio a otro o ExpressRoute la red virtual.

![Diagrama de red virtual entre local](./media/virtual-machines-common-infrastructure-service-guidelines/vnet02.png)

En esta configuración, la red virtual Azure es, esencialmente, una extensión basada en nube de su red local.

Dado que se conectan a su red local, entre locales redes virtuales deben utilizar una parte del espacio de direcciones usada por su organización que sea único. En la misma manera que diferentes ubicaciones corporativas se asignan subred IP específica, Azure se convierte en otra ubicación mientras se extiende fuera de su red.

Para permitir que los paquetes de viaje de su red virtual de cruz local a su red local, debe configurar el conjunto de prefijos de direcciones locales pertinentes como parte de la definición de la red local de la red virtual. Según el espacio de direcciones de la red virtual y el conjunto de ubicaciones locales pertinentes, puede haber varios prefijos de direcciones en la red local.

Puede convertir una red virtual solo nube a una red virtual de cruz local, pero es muy probable que requiere IP redistribuir el espacio de direcciones de red virtual y recursos de Azure. Por lo tanto, considere detenidamente si una red virtual tiene que estar conectado a su red local cuando se asigna una subred IP.

## <a name="subnets"></a>Subredes
Subredes permiten organizar recursos relacionados, ya sea lógicamente (por ejemplo, una subred para máquinas virtuales asociados a la misma aplicación), o física (por ejemplo, una subred por grupo de recursos). También puede emplear técnicas de aislamiento de subred para una mayor seguridad.

Para las redes virtuales entre local, debe diseñar subredes con las mismas convenciones que use para recursos locales. **Azure siempre usa las tres primeras direcciones IP del espacio de direcciones para cada subred**. Para determinar el número de direcciones necesarias para la subred, comience por contar el número de máquinas virtuales que necesita ahora. Estimar para el crecimiento futuro y, a continuación, utilice la siguiente tabla para determinar el tamaño de la subred.

Número de máquinas virtuales es necesario | Número de bits de host es necesario | Tamaño de la subred
--- | --- | ---
1-3 | 3 | 29
4-11     | 4 | / 28
12-27 | 5 | / 27
28 – 59 | 6 | / 26
60: 123 | 7 | 25

> [AZURE.NOTE] Para subredes normal en local, el número máximo de direcciones de host para una subred con bits host n es 2<sup>n</sup> -2. Para una subred Azure, el número máximo de direcciones de host para una subred con bits host n es 2<sup>n</sup> -5 (2 más 3 para las direcciones de Azure en cada subred).

Si elige un tamaño de subred es muy pequeño, que deberá re IP y volver a implementar las máquinas virtuales de la subred.


## <a name="network-security-groups"></a>Grupos de seguridad de red
Puede aplicar las reglas de filtrado para el tráfico que fluye a través de sus redes virtuales mediante grupos de seguridad de red. Puede crear reglas filtradas granulares para proteger su entorno de red virtual, controlar entrantes y salientes tráfico de origen y de destino intervalos IP, permitido puertos, etcetera. Grupos de seguridad de red puede aplicarse a subredes de una red virtual o directamente a una interfaz de red virtual determinado. Se recomienda tener un nivel de grupo de seguridad de red de filtrado de tráfico en sus redes virtuales. Puede obtener más información acerca de los [Grupos de seguridad de red](../virtual-network/virtual-networks-nsg.md).


## <a name="additional-network-components"></a>Componentes adicionales de red
Al igual que con una infraestructura de red física de local, una red virtual Azure puede contener más que simplemente subredes y direcciones IP. Al diseñar la infraestructura de aplicaciones, desea incorporar algunos de estos componentes adicionales:

- [Puertas de enlace VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md) - conectarse Azure redes virtuales a otras redes virtuales Azure, o redes de local a través de una conexión VPN de sitio a otro. Implementar Express ruta conexiones dedicadas y seguras. También puede proporcionar acceso directo de usuarios con conexiones VPN de sitio a punto.
- [Equilibrador de carga](../load-balancer/load-balancer-overview.md) - proporciona equilibrio de carga de tráfico de tráfico interno y externo como desee.
- [Puerta de enlace de aplicación](../application-gateway/application-gateway-introduction.md) - HTTP equilibrio de carga en el nivel de aplicación, que proporciona algunas ventajas adicionales para las aplicaciones web en lugar de implementar la Azure equilibrador de carga.
- [Administrador de tráfico](../traffic-manager/traffic-manager-overview.md) : distribución de tráfico basado en DNS para dirigir a los usuarios finales al extremo de aplicación disponible más próximo, lo que le permite hospedar la aplicación de centros de datos de Azure en diferentes regiones.


## <a name="next-steps"></a>Pasos siguientes

[AZURE.INCLUDE [virtual-machines-linux-infrastructure-guidelines-next-steps](../../includes/virtual-machines-linux-infrastructure-guidelines-next-steps.md)] 