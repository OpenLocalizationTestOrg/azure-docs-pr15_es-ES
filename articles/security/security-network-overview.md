<properties
   pageTitle="Información general de seguridad de red Azure | Microsoft Azure"
   description=" En este artículo facilita la comprender qué Microsoft Azure tiene que ofrecer en el área de seguridad de la red. Proporcionamos explicaciones básicas conceptos centrales de seguridad de red y requisitos e información sobre qué Azure tiene que ofrecer en cada una de estas áreas. "
   services="security"
   documentationCenter="na"
   authors="TomShinder"
   manager="MBaldwin"
   editor="TomSh"/>

<tags
   ms.service="security"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/09/2016"
   ms.author="terrylan"/>

# <a name="azure-network-security-overview"></a>Información general de seguridad de red de Azure

Microsoft Azure incluye una sólida infraestructura de red para su aplicación y los requisitos de conexión de servicio de soporte técnico. Conectividad de red es posible entre los recursos que se encuentran en Azure, entre local y Azure alojado recursos y a y desde Internet y Azure.

El objetivo de este artículo es más sencillo comprender qué Microsoft Azure tiene que ofrecer en el área de seguridad de la red. Aquí le proporcionamos explicaciones básicas para conceptos centrales de seguridad de red y los requisitos. Nos también proporciona información sobre qué Azure tiene que ofrecer en cada una de estas áreas. Hay numerosos vínculos a otro contenido que le permita obtener un conocimiento más profundo de las áreas en el que está interesado.

En este artículo de información general sobre la seguridad de red de Azure se centrará en lo siguiente:

- Redes de Azure
- Control de acceso de red
- Seguro de conectividad remota de access y entre local
- Disponibilidad
- Registro de información
- Resolución de nombres
- Arquitectura de DMZ
- Centro de seguridad de Azure

## <a name="azure-networking"></a>Redes de Azure

Máquinas virtuales necesita conectividad de red. Para admitir este requisito, Azure requiere máquinas virtuales que estar conectado a una red Virtual de Azure. Una red Virtual de Azure es una construcción lógica creada a partir de la estructura de red física de Azure. Cada red Virtual de Azure lógica está aislado de todas las demás redes Virtual de Azure. Esto le permite asegurarse de que el tráfico de red en las implementaciones no es accesible para otros clientes de Microsoft Azure.

Aprende más:

- [Información general de una red virtual](../virtual-network/virtual-networks-overview.md)

## <a name="network-access-control"></a>Control de acceso de red
Control de acceso de red consiste en limitar la conectividad a y desde dispositivos específicos o subredes dentro de una red Virtual de Azure. Es el objetivo de control de acceso de red para asegurarse de que sus equipos virtuales y servicios sean accesibles únicamente a los usuarios y dispositivos a la que desea accesibles. Controles de acceso se basan en permitir o denegar decisiones para conexiones a y desde su máquina virtual o un servicio.

Azure admite varios tipos de control de acceso de red. Se incluyen:

- Control de la capa de red
- Control de la ruta y se obliga túnel
- Dispositivos de seguridad de una red virtual

### <a name="network-layer-control"></a>Control de la capa de red
Cualquier implementación segura requiere alguna medida de control de acceso de red. Es el objetivo de control de acceso de red para asegurarse de que sus máquinas virtuales y los servicios de red que se ejecutan en esas máquinas virtuales sólo pueden comunicarse con otros dispositivos de red que necesitan comunicarse con y se bloquea el resto de los intentos de conexión.

Si necesita control de acceso de red básica (según la dirección IP y los protocolos TCP o UDP), puede usar grupos de seguridad de red. Un grupo de seguridad de la red (GSN) es un firewall filtrado de paquetes básico y le permite controlar el acceso en función de una [tupla de 5](https://www.techopedia.com/definition/28190/5-tuple). NSGs no proporcionan inspección del nivel de aplicación o se han autenticado acceder a los controles.

Aprende más:

- [Grupos de seguridad de red](../virtual-network/virtual-networks-nsg.md)

### <a name="route-control-and-forced-tunneling"></a>Control de la ruta y túnel forzado
La capacidad de controlar el comportamiento del enrutamiento en las redes de Azure Virtual es una función de control de acceso y seguridad de red crítica. Si el enrutamiento está configurado correctamente, aplicaciones y servicios de la máquina virtual pueden conectar dispositivos que no desea conectarse a, incluidos los dispositivos que pertenecen a través de posibles atacantes.

Redes Azure es compatible con la posibilidad de personalizar el comportamiento de enrutamiento para el tráfico de red en sus redes Virtual de Azure. Esto le permite modificar las entradas de tabla de enrutamiento de forma predeterminada en su red Virtual de Azure. Control de comportamiento de enrutamiento ayuda a asegurarse de que todo el tráfico de un dispositivo o un grupo de dispositivos determinados entra o sale de su red Virtual de Azure a través de una ubicación específica.

Por ejemplo, es posible que tenga un dispositivo de seguridad de una red virtual en su red Virtual de Azure. Desea asegurarse de que todo el tráfico a y desde su red Virtual de Azure pasa a través de ese equipo virtual de seguridad. Puede hacerlo configurando [Rutas de definidas por el usuario](../virtual-network/virtual-networks-udr-overview.md) en Azure.

[Se obliga túnel](https://www.petri.com/azure-forced-tunneling) es un mecanismo que puede utilizar para asegurarse de que los servicios no pueden iniciar una conexión a los dispositivos de Internet. Tenga en cuenta que esto es diferente de aceptar conexiones entrantes y, a continuación, responder a ellos. Servidores web front-end necesitan responder a la solicitud de hosts de Internet y, a continuación, se permite el tráfico originado por tanto en Internet entrante a estos servidores web y los servidores web pueden responder.

Lo que no desea permitir que es un servidor web front-end para iniciar una solicitud de salida. Estas solicitudes pueden representar un riesgo para la seguridad porque estas conexiones se pueden utilizar para descargar el malware. Incluso si desea que estos servidores front-end para iniciar las solicitudes de salida a Internet, desea obligue a ir a través de su proxy de web local para que puede aprovechar las ventajas de la dirección URL de registro y filtrado.

En su lugar, ¿desea usar túnel forzado para evitar esto. Al habilitar túnel forzado, se fuerzan todas las conexiones a Internet a través de la puerta de enlace local. Puede configurar túnel forzado aprovechando las rutas de definidas por el usuario.

Aprende más:

- [¿Qué son las rutas de definidas por el usuario y reenvío IP](../virtual-network/virtual-networks-udr-overview.md)

### <a name="virtual-network-security-appliances"></a>Dispositivos de seguridad de una red virtual
Aunque los grupos de seguridad de red, las rutas de definidas por el usuario y túnel forzado proporcionan un nivel de seguridad en los niveles de red y transporte del [modelo OSI](https://en.wikipedia.org/wiki/OSI_model), puede haber ocasiones que desee habilitar la seguridad en los niveles superiores de la red.

Por ejemplo, se incluyen los requisitos de seguridad:

- Autenticación y la autorización antes de permitir el acceso a la aplicación
- Detección de intrusiones y respuesta intrusiones
- Inspección del nivel de aplicación para los protocolos de alto nivel
- Filtrado URL
- Antimalware y antivirus de nivel de red
- Protección contra el componente
- Control de acceso de la aplicación
- Protección DDoS adicional (encima DDoS protección siempre a la estructura de Azure propio)

Puede acceder a estas características de seguridad de red mejorada mediante una solución de Azure asociado. Puede encontrar al partner de Azure más reciente soluciones de seguridad de red al visitar la [Azure Marketplace](https://azure.microsoft.com/marketplace/) y búsqueda de "seguridad" y "seguridad de la red".

## <a name="secure-remote-access-and-cross-premises-connectivity"></a>Acceso remoto seguro y conectividad entre local
El programa de instalación, configuración y administración de sus necesidades de recursos de Azure hacerse de forma remota. Además, es aconsejable implementar soluciones de [TI híbridos](http://social.technet.microsoft.com/wiki/contents/articles/18120.hybrid-cloud-infrastructure-design-considerations.aspx) que tienen componentes local y en la nube pública de Azure. Estos escenarios requieren acceso remoto seguro.

Redes Azure es compatible con los siguientes escenarios de acceso remoto seguro:

- Conectar estaciones de trabajo individuales a una red Virtual de Azure
- Conecte su red local a una red Virtual de Azure con una VPN
- Conectarse a su red local a una red Virtual de Azure con un vínculo WAN dedicado
- Conectar redes virtuales Azure entre sí

### <a name="connect-individual-workstations-to-an-azure-virtual-network"></a>Conectar estaciones de trabajo individuales a una red Virtual de Azure
Puede haber ocasiones en que desee habilitar individual los desarrolladores o personal de operaciones administrar máquinas virtuales y servicios en Azure. Por ejemplo, necesita acceso a una máquina virtual en una red Virtual de Azure y la directiva de seguridad no permite el acceso remoto RDP o SSH a máquinas virtuales individuales. En este caso, puede usar una conexión VPN punto al sitio.

La conexión VPN de sitio a punto usa el protocolo [VPN SSTP](https://technet.microsoft.com/library/cc731352.aspx) que le permite configurar una conexión segura y privada entre el usuario y la red Virtual de Azure. Una vez establecida la conexión VPN, el usuario podrá RDP o SSH sobre el vínculo VPN en cualquier máquina virtual en la red Virtual de Azure (suponiendo que el usuario puede autenticar y está autorizado).

Aprende más:

- [Configurar una conexión punto a sitio a una red Virtual con PowerShell](../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md)

### <a name="connect-your-on-premises-network-to-an-azure-virtual-network-with-a-vpn"></a>Conectarse a su red local a una red Virtual Azure con una VPN
Desea conectarse a su red corporativa completa o partes del mismo, a una red Virtual de Azure. Esto es común en TI híbridos escenarios donde las empresas [ampliar su centro de datos local en Azure](https://gallery.technet.microsoft.com/Datacenter-extension-687b1d84). En muchos casos, las empresas hospedará partes de un servicio en Azure y elementos local, como cuando una solución incluye servidores web front-end en Azure y bases de datos back-end locales. Este tipo de conexiones de "entre local" también que management de Azure ubicado recursos más seguro y habilitar escenarios como extender los controladores de dominio de Active Directory en Azure.

Una forma de hacerlo es usar una [VPN de sitio a otro](https://www.techopedia.com/definition/30747/site-to-site-vpn). La diferencia entre una VPN de sitio a sitio y una VPN de sitio a punto es que una VPN punto al sitio conecta un solo dispositivo a una red Virtual de Azure, mientras que una VPN de sitio a sitio conecta toda una red (por ejemplo, su red local) a una red Virtual de Azure. VPN de sitio a sitio a una red Virtual de Azure usar el modo de túnel IPsec protocolo VPN muy seguro.

Aprende más:

- [Crear un VNet Administrador de recursos con una conexión VPN de sitio a sitio con el Portal de Azure](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md)
- [Planificación y diseño de puerta de enlace VPN](../vpn-gateway/vpn-gateway-plan-design.md)

### <a name="connect-your-on-premises-network-to-an-azure-virtual-network-with-a-dedicated-wan-link"></a>Conectarse a su red local a una red Virtual Azure con un vínculo WAN dedicado
Conexiones VPN de sitio a punto y a sitios son eficaces para habilitar la conectividad entre local. Sin embargo, algunas organizaciones tenga en cuenta que tiene los siguientes inconvenientes:

- Conexiones VPN mover datos a través de Internet: expone estas conexiones posibles problemas de seguridad que implica mover los datos en una red pública. Además, confiabilidad y disponibilidad para conexiones a Internet no se puede garantizar.
- Podrán considerar las conexiones VPN a redes virtuales de Azure limita el ancho de banda para algunas aplicaciones y efectos, como se max fuera en torno a 200Mbps.

Las organizaciones que necesitan el máximo nivel de seguridad y la disponibilidad de sus conexiones entre local suele usar vínculos WAN dedicados para conectarse a sitios remotos. Azure proporciona la capacidad de usar un vínculo WAN dedicado que puede usar para conectarse a su red local a una red Virtual de Azure. Esto se habilita a través de Azure ExpressRoute.

Aprende más:

- [Información general técnica de ExpressRoute](../expressroute/expressroute-introduction.md)

### <a name="connect-azure-virtual-networks-to-each-other"></a>Conectar redes virtuales Azure entre sí
Es posible usar varias redes virtuales de Azure para las implementaciones. Hay muchas razones por qué puede hacer esto. Uno de los motivos puede simplificar la administración; otro posible por motivos de seguridad. Independientemente de la motivación o razonamiento para colocar los recursos en diferentes redes Virtual de Azure, puede haber ocasiones cuando quiera recursos en cada una de las redes para que se conecten entre sí.

Una opción sería para los servicios en una red Virtual de Azure conectarse a servicios en otra red Virtual de Azure "bucle atrás" a través de Internet. La conexión ¿iniciar en una red Virtual de Azure, vaya a través de Internet y, a continuación, vuelva a la red Virtual de Azure de destino. Esta opción expone la conexión a los problemas de seguridad inherentes a cualquier comunicación basados en Internet.

Una mejor opción podría ser crear una Virtual de Azure red a Azure Virtual red VPN de sitio a otro. Este Virtual de Azure red a Azure Virtual red VPN de sitio a otro, se utiliza el mismo protocolo de [modo de túnel IPsec](https://technet.microsoft.com/library/cc786385.aspx) como la conexión de VPN de sitio a sitio entre local mencionada arriba.

La ventaja de usar una Virtual de Azure red a Azure Virtual red sitio a sitio VPN es que se ha establecido la conexión VPN sobre la estructura de red Azure; no se conecta a través de Internet. Esto proporciona una capa adicional de seguridad en comparación con VPN de sitio a sitio que se conectan a través de Internet.

Aprende más:

- [Configurar una conexión de VNet a VNet mediante PowerShell y el Administrador de recursos de Azure](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md)

## <a name="availability"></a>Disponibilidad
Disponibilidad es un componente clave de cualquier programa de seguridad. Si no tiene acceso a sus usuarios y sistemas necesitan tener acceso a través de la red, el servicio se puede considerar comprometido. Azure tiene tecnologías de red que admiten los siguientes mecanismos de alta disponibilidad:

- Equilibrio de carga basado en HTTP
- Nivel de equilibrio de carga
- Equilibrio de carga global

Equilibrio de carga es un mecanismo diseñado para distribuir uniformemente conexiones entre varios dispositivos. Los objetivos de equilibrio de carga son:

- Aumentar la disponibilidad: al cargar saldo conexiones en varios dispositivos, uno o varios de los dispositivos no esté disponible y pueden continuar los servicios que se ejecutan en los dispositivos restantes en línea servir el contenido del servicio
- Aumentar el rendimiento: cuando se carga saldo conexiones en varios dispositivos, un único dispositivo no hay que realizar el posicionamiento de procesador. En su lugar, las peticiones de procesamiento y la memoria para servir el contenido se distribuye en varios dispositivos.

### <a name="http-based-load-balancing"></a>Equilibrio de carga basado en HTTP
Las organizaciones que ejecutan servicios basados en web a menudo desean tener un equilibrador de carga basado en HTTP delante de los servicios web para ayudar a asegurar niveles de rendimiento y alta disponibilidad. A diferencia de los equilibradores de carga tradicional basada en la red, las decisiones de basado en HTTP de equilibrio de carga equilibradores de carga se basan en las características del protocolo HTTP, no se encuentran en los protocolos de capa de transporte y de red.

Para proporcionar basado en HTTP equilibrio de carga para los servicios basados en web, Azure proporciona la puerta de enlace de aplicación de Azure. Es compatible con la puerta de enlace de aplicación de Azure:

- Basado en HTTP equilibrio de carga – decisiones de equilibrio de carga se realizan en función características especiales para el protocolo HTTP
- Afinidad de la sesión basada en cookies: esta capacidad se asegura de que las conexiones establecidas a uno de los servidores de ese equilibrador de carga permanece intacta entre el cliente y el servidor. Esto asegura estabilidad de transacciones.
- Descarga de SSL: cuando se establece una conexión de cliente con el equilibrador de carga, que entre el cliente y el equilibrador de carga está cifrada mediante el HTTPS (SSL /) protocolo. Sin embargo, con el fin de mejorar el rendimiento, tiene la opción de que la conexión entre el equilibrador de carga y el servidor web, el uso de equilibrador de carga el protocolo HTTP (sin cifrar). Esto se conoce como "Descarga de SSL" porque los servidores web detrás del equilibrador de carga no experimentar la sobrecarga de procesador que implica cifrado y, por tanto, debería poder solicitudes de servicio más rápidamente.
- Enrutamiento contenido basado en la dirección URL: esta característica permite a los equilibradores de carga tomar decisiones sobre dónde reenviar conexiones basadas en la dirección URL de destino. Esto proporciona mucha más flexibilidad que las soluciones que asegúrese de cargar Equilibrio decisiones basándose en direcciones IP.

Aprende más:

- [Información general sobre la puerta de enlace de aplicaciones](../application-gateway/application-gateway-introduction.md)

### <a name="network-level-load-balancing"></a>Nivel de equilibrio de carga
A diferencia de equilibrio de carga basado en HTTP, el equilibrio de carga de nivel de red hace decisiones de equilibrio de carga en función de IP dirección y el puerto (TCP o UDP).
Puede obtener los beneficios de nivel de equilibrio de carga en Azure usando el equilibrador de carga de Azure. Algunas características clave del equilibrador de carga de Azure incluyen:

- Equilibrio de carga de nivel de red en función de los números de puerto y la dirección IP
- Compatibilidad con cualquier protocolo de capa de aplicación
- Cargar los saldos a máquinas virtuales de Windows Azure e instancias de rol de servicios de nube
- Puede usarse para a través de Internet (equilibrio de carga externo) y no es de Internet opuestas (equilibrio de carga interno) aplicaciones y equipos virtuales
- Extremo supervisión, que se utiliza para determinar si cualquiera de los servicios detrás del equilibrador de carga ya no esté disponible

Aprende más:

- [Internet opuestas equilibrador de carga entre varias máquinas virtuales o servicios](../load-balancer/load-balancer-internet-overview.md)
- [Información general del equilibrador de carga interno](../load-balancer/load-balancer-internal-overview.md)

### <a name="global-load-balancing"></a>Equilibrio de carga global
Algunas organizaciones desean el máximo nivel de disponibilidad posible. Una forma para alcanzar este objetivo es para las aplicaciones de host en centros de datos de distribución global. Cuando una aplicación se hospeda en centros de datos que se encuentra en todo el mundo, es posible para toda una región geopolíticas no estarán disponibles y aún tiene la aplicación y en funcionamiento.

Además de las ventajas de disponibilidad que obtener por alojamiento de aplicaciones en los centros de datos de distribución global, también puede obtener ventajas de rendimiento. Pueden obtener estas ventajas de rendimiento mediante un mecanismo que dirige solicitudes de servicio de para el centro de datos más cercano el dispositivo que realiza la solicitud.

Equilibrio de carga global puede proporcionar ambas de estas ventajas. En Azure, puede obtener los beneficios global de equilibrio de carga mediante el Administrador de tráfico de Azure.

Aprende más:

- [¿Qué es el Administrador de tráfico?](../traffic-manager/traffic-manager-overview.md)

## <a name="logging"></a>Registro de información
Registro de un nivel de red es una función clave para cualquier escenario de seguridad de red. En Azure, puede registrar información obtenida para que grupos de seguridad de la red obtener información de registro de nivel de red. Con el registro de GSN, obtener información de:

- Registros de auditoría – estos registros se utilizan para ver todas las operaciones que se envía a las suscripciones de Azure. Estos registros están habilitados de forma predeterminada y se pueden usar en el portal de Azure.
- Registros de eventos – estos registros proporcionan información sobre las reglas de GSN aplicadas.
- Registros de contador: estos registros le permiten saber cuántas veces se aplicó cada regla GSN para denegar o permitir el tráfico.

También puede usar [Microsoft Power BI](https://powerbi.microsoft.com/what-is-power-bi/), una herramienta de visualización de datos para ver y analizar estos registros.

Aprende más:

- [Análisis de registro de grupos de seguridad de red (NSGs)](../virtual-network/virtual-network-nsg-manage-log.md)

## <a name="name-resolution"></a>Resolución de nombres
Resolución de nombres es una función crítica para todos los servicios que se hospeda en Azure. Desde una perspectiva de seguridad, peligro de la función de resolución de nombre puede hacer que un intruso redirigir peticiones de sus sitios de un sitio. Resolución de nombres seguro es necesario para todos los servicios de nube hospedado.

Existen dos tipos de resolución de nombres que debe dirección:

- Resolución de nombre interno: resolución de nombres interno se utiliza por los servicios en sus redes Virtual de Azure, sus redes local o ambos. Nombres de resolución destinados nombres internos no están accesibles en Internet. Para una seguridad óptima, es importante que la combinación de resolución de nombre interno no es accesible para los usuarios externos.
- Resolución de nombres externa: resolución de nombres externos utilizan personas y dispositivos fuera de sus redes virtuales de Azure y local. Estos son los nombres que están visibles en Internet y se utilizan para dirigir la conexión a los servicios en la nube.

Resolución de nombres internos, tiene dos opciones:

- Un servidor DNS de red Virtual de Azure: cuando se crea una nueva red Virtual de Azure, se crea un servidor DNS por usted. Este servidor DNS para resolver los nombres de los equipos que se encuentran en dicha red Virtual de Azure. Este servidor DNS no es configurable y es administrado por el Administrador de Azure tela, lo que una solución de resolución de nombre seguro.
- Traer su propio servidor DNS, tiene la opción de colocación de un servidor DNS de su elección en su red Virtual de Azure. Este servidor DNS podría ser que un Active Directory integrado servidor DNS o una solución de servidor DNS dedicada proporcionadas por un socio de Azure, que puede obtener de Azure Marketplace.

Aprende más:

- [Información general de una red virtual](../virtual-network/virtual-networks-overview.md)
- [Administrar los servidores DNS que utiliza una red Virtual (VNet)](../virtual-network/virtual-networks-manage-dns-in-vnet.md)

Para la resolución DNS externa, tiene dos opciones:

- Hospedar su propio externos DNS server local
- Hospedar su propio servidor DNS externo con un proveedor de servicios

Muchas organizaciones grandes hospedará sus propios servidores DNS en local. Puede hacerlo porque tienen la experiencia en red y presencia global para hacerlo.

En la mayoría de los casos, es mejor hospedar los servicios de resolución de nombres DNS con un proveedor de servicios. Estos proveedores de servicios tienen la experiencia de red y la presencia global para garantizar muy alta disponibilidad de los servicios de resolución de nombre. Disponibilidad es fundamental para los servicios DNS porque si se produce un error en los servicios de resolución de nombres, nadie podrá tener acceso a su opuestas en servicios de Internet.

Azure le proporciona un gran disponibilidad y eficaz solución DNS externo en el formulario de Azure DNS. Esta solución de resolución de nombre externo aprovecha la infraestructura de DNS de Azure en todo el mundo. Permite hospedar su dominio en Azure con las mismas credenciales, API, herramientas y facturación como los otros servicios de Azure. Como parte de Azure, también hereda los controles de seguridad sólidos incorporados en la plataforma.

Aprende más:

- [Introducción a Azure DNS](../dns/dns-overview.md)

## <a name="dmz-architecture"></a>Arquitectura de DMZ
Muchas empresas utilizan DMZ para segmentar sus redes para crear una zona de búfer entre Internet y sus servicios. La parte de DMZ de la red se considera una zona de nivel bajo de seguridad y activos de gran valor no se colocan en ese segmento de red. Normalmente, verá los dispositivos de seguridad de red que tienen una interfaz de red en el segmento DMZ y otra interfaz de red conectado a una red con máquinas virtuales y servicios que aceptan conexiones entrantes de Internet.

Hay una serie de variaciones de diseño de DMZ y la decisión de implementar una DMZ, y, a continuación, ¿qué tipo de DMZ usar si decide usar uno, basado en los requisitos de seguridad de la red.

Aprende más:

- [Servicios de nube de Microsoft y la seguridad de red](../best-practices-network-security.md)

## <a name="azure-security-center"></a>Centro de seguridad de Azure
Centro de seguridad le ayuda a evitar, detectar y responder a amenazas y proporciona que mayor visibilidad y control, la seguridad de los recursos de Azure. Proporciona seguridad integrada supervisión y la directiva de administración de las suscripciones de Azure, ayuda a detecta amenazas que pueden incluirse en caso contrario, problemas y funciona con un amplio ecosistema de soluciones de seguridad.

Centro de seguridad de Azure le ayuda a optimizar y supervisar la seguridad de la red por:

- Proporcionar recomendaciones de seguridad de red
- Supervisar el estado de la configuración de seguridad de red
- Avisarle de red según amenazas tanto en los niveles de red y del punto final

Aprende más:

- [Introducción al centro de seguridad de Azure](../security-center/security-center-intro.md)
