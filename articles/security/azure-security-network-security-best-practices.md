<properties
   pageTitle="Prácticas recomendadas de seguridad de red Azure | Microsoft Azure"
   description="En este artículo se proporciona un conjunto de prácticas recomendadas para el uso de seguridad de red creada en capacidades de Azure."
   services="security"
   documentationCenter="na"
   authors="TomShinder"
   manager="swadhwa"
   editor="TomShinder"/>

<tags
   ms.service="security"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="05/25/2016"
   ms.author="TomSh"/>

# <a name="azure-network-security-best-practices"></a>Prácticas recomendadas de seguridad de red de Azure

Microsoft Azure le permiten conectar máquinas virtuales y dispositivos para otros dispositivos de red colocando redes virtuales de Azure. Una red Virtual de Azure es una construcción de una red virtual que le permite conectarse a tarjetas de interfaz de red virtual a una red virtual para permitir las comunicaciones basadas en TCP/IP entre los dispositivos de red habilitado. Azure máquinas virtuales de Windows conectado a una red Virtual de Azure son pueda conectarse a los dispositivos de la misma red Virtual Azure, diferentes redes Virtual de Azure, en Internet o incluso en sus propia redes local.

En este artículo se describen una colección de prácticas recomendadas de seguridad de red de Azure. Estas recomendaciones derivan de nuestra experiencia con las redes de Azure y las experiencias de los clientes como usted mismo.

Para cada práctica recomendada, explicaremos:

- ¿Qué es el método recomendado
- ¿Por qué desea habilitar esa práctica recomendada
- ¿Qué podría ser el resultado si no puede habilitar la práctica recomendada
- Posibles alternativas a la práctica recomendada
- ¿Cómo puede aprender a habilitar la práctica recomendada

En este artículo de prácticas recomendadas de seguridad de red de Azure se basa en una opinión consenso y las capacidades de la plataforma Windows Azure y conjuntos de características que ya existen en el momento en que se escribió este artículo. Opiniones y tecnologías cambian con el tiempo y en este artículo se actualizarán de forma periódica para reflejar los cambios.

Azure red mejores prácticas de seguridad analizadas en este artículo se incluyen:

- Subredes de segmento lógicamente
- Controlar el comportamiento de enrutamiento
- Habilitar túnel forzado
- Uso de dispositivos de red Virtual
- Implemente DMZ para zonas de seguridad
- Evitar la exposición a Internet con vínculos WAN dedicados
- Optimizar la actividad y el rendimiento
- Usar Equilibrio de carga global
- Deshabilitar el acceso RDP a máquinas virtuales de Windows Azure
- Habilitar el centro de seguridad de Azure
- Ampliar su centro de datos en Azure


## <a name="logically-segment-subnets"></a>Subredes de segmento lógicamente

[Redes virtuales de Azure](https://azure.microsoft.com/documentation/services/virtual-network/) son similares a una LAN en su red local. La idea de una red Virtual de Azure es crear una único IP dirección espacio basado en una red privada en la que puede colocar sus [máquinas virtuales de Azure](https://azure.microsoft.com/services/virtual-machines/). Los espacios de direcciones IP privados se encuentran en la clase A (10.0.0.0/8), la clase B (172.16.0.0/12) y la clase C rangos (192.168.0.0/16).

Similar a lo que lo hace en local, desea segmentar más espacio de direcciones en subredes. Puede usar [CIDR](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) según subred principios para crear su subredes.

Enrutamiento entre subredes sucederá automáticamente y no es necesario configurar manualmente las tablas de enrutamiento. Sin embargo, el valor predeterminado es que no hay ningún control de acceso de red entre subredes que crear en la red Virtual de Azure. Para crear controles de acceso de red entre subredes, debe colocar algo entre subredes.

Una de las cosas que puede usar para llevar a cabo esta tarea es un [Grupo de seguridad de la red](../virtual-network/virtual-networks-nsg.md) (GSN). NSGs son dispositivos de inspección de paquetes simple que utilizan la tupla 5 (dirección IP de origen, puerto de origen, dirección IP de destino, puerto de destino y protocolo de capa 4) enfoque para permitir o denegar de crear reglas para el tráfico de red. Puede permitir o denegar el tráfico a y desde única dirección IP y de varias direcciones IP o incluso y de subredes enteras.

Usar NSGs para el control de acceso de red entre subredes permite poner los recursos que pertenecen a la misma zona de seguridad o una función de sus propios subredes. Por ejemplo, piense en una aplicación de nivel 3 simple que tiene un nivel de web, un nivel de la lógica de aplicación y una base de datos. Poner máquinas virtuales que pertenecen a cada uno de estos elementos en sus propia subredes. A continuación, use NSGs para controlar el tráfico entre subredes:

- Niveles de Web máquinas virtuales solo se pueden iniciar conexiones a los equipos de lógica de la aplicación y sólo puede aceptar conexiones de Internet
- Máquinas virtuales de lógica de aplicación sólo se puede iniciar conexiones con el nivel de base de datos y sólo puede aceptar conexiones desde el nivel de web
- Máquinas virtuales de nivel de base de datos no se puede iniciar la conexión con fuera de su propia subred y sólo puede aceptar conexiones desde el nivel de la lógica de aplicación

Para obtener más información sobre grupos de seguridad de la red y cómo puede usarlos para segmentar lógicamente sus redes Virtual de Azure, lea el artículo [¿Qué es un grupo de seguridad de la red](../virtual-network/virtual-networks-nsg.md) (GSN).

## <a name="control-routing-behavior"></a>Controlar el comportamiento de enrutamiento

Cuando se coloca una máquina virtual en una red Virtual de Azure, habrá observado que la máquina virtual puede conectarse a cualquier otra máquina virtual en la misma red Virtual de Azure, incluso si las demás máquinas virtuales están en subredes diferentes. La razón por qué es posible es que hay una colección de rutas de sistema predeterminada están habilitados para permitir este tipo de comunicación. Estas rutas predeterminadas permiten máquinas virtuales de Windows en la misma red Virtual de Azure para iniciar las conexiones entre sí y con Internet (para las comunicaciones salientes a sólo Internet).

Mientras que las rutas de sistema predeterminadas son útiles para muchos escenarios de implementación, hay ocasiones en que desea personalizar la configuración de enrutamiento para las implementaciones. Estas personalizaciones, podrá configurar la dirección de salto siguiente para llegar a destinos específicos.

Se recomienda configurar rutas de definidas por el usuario cuando se implementa en un dispositivo de seguridad de una red virtual, hablaremos en práctica recomendada posterior.

> [AZURE.NOTE] Rutas de definidas por el usuario no son necesarias y las rutas de sistema predeterminadas funcionarán en la mayoría de los casos.

Puede obtener más información sobre las rutas de definidas por el usuario y cómo configurarlos, lea el artículo [¿Qué son las rutas de definidas por el usuario y el reenvío de IP](../virtual-network/virtual-networks-udr-overview.md).

## <a name="enable-forced-tunneling"></a>Habilitar túnel forzado

Para entender mejor túnel forzado, resulta útil entender qué "dividir túnel" es.
El ejemplo más común de túnel dividido se ve con conexiones VPN. Imagine que establecer una conexión VPN de la sala de hotel a su red corporativa. Esta conexión le permite conectarse a recursos de la red corporativa y todas las comunicaciones con los recursos de su red corporativa vaya a través del túnel VPN.

¿Qué sucede cuando desee conectarse a recursos de Internet? Cuando se habilita el túnel dividido, las conexiones vaya directamente a Internet y no a través del túnel VPN. Algunos expertos en seguridad considere la posibilidad de que sea un riesgo y, por tanto, se recomienda que túnel dividido deshabilitará y todas las conexiones, los destinados a Internet y los destinados a recursos corporativos, vaya a través del túnel VPN. La ventaja de hacerlo es que, a continuación, se fuerzan las conexiones a Internet a través de los dispositivos de seguridad de la red corporativa, que sería el caso si el cliente VPN conectado a Internet fuera del túnel VPN.

Ahora vamos a ponerlas este a máquinas virtuales en una red Virtual de Azure. Las rutas predeterminadas para una red Virtual de Azure permiten máquinas virtuales de Windows iniciar el tráfico a Internet. Esto también puede representar riesgos de seguridad, como estas conexiones salientes, pueden aumentar la superficie de ataque de una máquina virtual y aprovechadas por atacantes.
Por este motivo, recomendamos que habilite túnel forzado en sus máquinas virtuales cuando tiene conectividad local cruzados entre su red Virtual de Azure y su red local. Hablaremos sobre cruzada conectividad local más adelante en este documento de prácticas recomendada redes Azure.

Si no tiene una conexión local cruzada, asegúrese de que aprovecha las ventajas de los grupos de seguridad de red (descrito anteriormente) o Azure virtual dispositivos de seguridad (explicado siguiente) para evitar conexiones salientes a Internet desde sus máquinas virtuales de Azure.

Para obtener más información sobre forzado túnel y cómo habilitarlo, lea el artículo [Configurar forzado túnel usar PowerShell y el Administrador de recursos de Azure](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md).

## <a name="use-virtual-network-appliances"></a>Uso de dispositivos de red virtual

Mientras definidas por el enrutamiento usuarios y grupos de seguridad de red pueden proporcionar un cierto grado de seguridad de la red en las capas de red y transporte del [modelo OSI](https://en.wikipedia.org/wiki/OSI_model), habrá situaciones donde deberá desea o necesita habilitar la seguridad de alto nivel de la pila. En estos casos, se recomienda implementar los dispositivos de seguridad de una red virtual proporcionados por los socios de Azure.

Dispositivos de seguridad de red Azure pueden ofrecer niveles muy mejoradas de seguridad sobre lo que se proporcionan controles de nivel de red. Algunas de las capacidades de seguridad de red proporcionadas por los dispositivos de seguridad de una red virtual incluyen:

- Servidores de seguridad
- Detección de intrusiones/Intrusion prevención
- Administración de vulnerabilidad
- Control de la aplicación
- Detección de anomalías basada en la red
- Filtrado de Web
- Antivirus
- Protección de botnet

Si necesita un mayor nivel de seguridad de red que puede obtener con controles de nivel de acceso de red, se recomienda que investigar e implementar, los dispositivos de seguridad de una red virtual Azure.

Para obtener información acerca de qué dispositivos de seguridad de una red virtual Azure están disponibles y sus capacidades, visite la [Azure Marketplace](https://azure.microsoft.com/marketplace/) y busque "seguridad" y "seguridad de la red".

##<a name="deploy-dmzs-for-security-zoning"></a>Implemente DMZ para zonas de seguridad
Una DMZ o "red perimetral" es un segmento de red física o lógica que está diseñado para proporcionar una capa adicional de seguridad entre los activos y de Internet. El propósito de la DMZ es colocar dispositivos de control de acceso de red especializado en el borde de la red DMZ para que se permite únicamente el tráfico deseado más allá de dispositivo de seguridad de la red y en la red Virtual de Azure.

DMZ es útiles porque se puede Centrar la administración de control de acceso de red, supervisión, registro y los informes en los dispositivos en el borde de la red Virtual de Azure. Aquí normalmente haría habilitar DDoS prevención, sistemas de prevención de intrusiones/detección de intrusiones (identificadores/IP), las reglas de firewall y directivas, filtrado web, antimalware de red y mucho más. Los dispositivos de seguridad de red sentarse entre Internet y su red Virtual de Azure y tienen una interfaz en ambas redes.

Aunque esto es el diseño básico de una DMZ, hay muchos diseños DMZ diferentes, como opuesta, alojados tri, hosts múltiples y otros usuarios.

Le recomendamos que considere la posibilidad de implementar una DMZ para aumentar el nivel de seguridad de la red para los recursos de Azure para todas las implementaciones de alta seguridad.

Para obtener más información acerca de DMZ y cómo se implementan en Azure, lea el artículo de la [seguridad de red y servicios de nube de Microsoft](../best-practices-network-security.md).

## <a name="avoid-exposure-to-the-internet-with-dedicated-wan-links"></a>Evitar la exposición a Internet con vínculos WAN dedicados
Muchas organizaciones han elegido la ruta de TI híbrido. En TI híbridos, algunos de los activos de información de la compañía están en Azure, mientras que otros usuarios permanecen en local. En muchos casos, algunos componentes de un servicio se ejecuta en Azure mientras otros componentes permanecen en local.

En el escenario de TI híbrido, suele haber algún tipo de conectividad entre local. Esto entre locales conectividad permite a la compañía conectar sus redes local a redes Virtual de Azure. Hay dos soluciones de conectividad entre local:

- VPN de sitio a sitio
- ExpressRoute

[VPN de sitio a sitio](../vpn-gateway/vpn-gateway-site-to-site-create.md) representa una conexión privada virtual entre su red local y una red Virtual de Azure. Esta conexión tiene lugar a través de Internet y le permite "túnel" información dentro de un vínculo entre la red y Azure cifrado. VPN de sitio a sitio es una tecnología de adultos, segura que se ha implementado por las empresas de todos los tamaños durante décadas. Cifrado de túnel se realiza mediante [el modo de túnel IPsec](https://technet.microsoft.com/library/cc786385.aspx).

Mientras VPN de sitio a sitio es una tecnología de confianza, confiable y establecida, el tráfico del túnel pasar por Internet. Además, el ancho de banda relativamente se limita a un máximo de sobre 200Mbps.

Si establece un nivel de seguridad o de rendimiento excepcional para las conexiones entre local, le recomendamos que use Azure ExpressRoute para la conectividad entre local. ExpressRoute es una WAN dedicada vínculo entre su ubicación local o un proveedor de hospedaje de Exchange. Dado que se trata de una conexión de telecomunicaciones, los datos no viajan a través de Internet y, por tanto, no se expongan a los posibles riesgos inherentes de comunicaciones de Internet.

Para obtener más información sobre cómo implementar y cómo funciona Azure ExpressRoute, lea el artículo [Información general técnica de ExpressRoute](../expressroute/expressroute-introduction.md).

## <a name="optimize-uptime-and-performance"></a>Optimizar la actividad y el rendimiento
Confidencialidad, integridad y disponibilidad (CIA) comprenden los tres del modelo de seguridad más importantes de hoy. Confidencialidad es sobre el cifrado y privacidad, integridad consiste en garantizar que personas no autorizadas no cambian los datos y disponibilidad consiste en asegurarse de que los usuarios autorizados tienen acceso a la información que están autorizados a acceder. Error en cualquiera de estas áreas representa una posible infracción de seguridad.

Disponibilidad puede considerarse como información sobre la actividad y el rendimiento. Si un servicio está desactivado, no puede tener acceso a información. Si el rendimiento es tan defectuoso como para hacer que los datos no se puede usar, nos podemos tener en cuenta los datos sean accesibles. Por lo tanto, desde una perspectiva de seguridad, es necesario hacer lo podemos para asegurarse de que dispone de nuestros servicios de rendimiento y el tiempo de actividad óptimo.
Un método popular y eficaz que se usan para mejorar la disponibilidad y el rendimiento es usar el equilibrio de carga. Equilibrio de carga es un método de distribución de tráfico de red entre servidores que forman parte de un servicio. Por ejemplo, si tiene servidores web front-end como parte de su servicio, puede usar el equilibrio de carga para distribuir el tráfico a través de los servidores web front-end varios.

Esta distribución de tráfico aumenta la disponibilidad porque, si uno de los servidores web no está disponible, el equilibrador de carga, dejar de enviar tráfico a dicho servidor y redirigir el tráfico a los servidores que aún están en línea. Equilibrio de carga también mejora el rendimiento, porque el procesador, la red y la memoria sobrecarga para atender las solicitudes se distribuye entre la carga de todos los servidores de equilibrio

Se recomienda que emplee equilibrio de carga siempre que pueda y, según corresponda para los servicios. Trataremos idoneidad en las secciones siguientes.
En el nivel de red Virtual de Azure, Azure proporciona con tres primarias opciones equilibrio de carga:

- Equilibrio de carga basado en HTTP
- Equilibrio de carga externa
- Equilibrio de carga interna

## <a name="http-based-load-balancing"></a>Equilibrio de carga basado en HTTP
Equilibrio de carga de HTTP basa decisiones sobre qué servidor para enviar las conexiones con las características del protocolo HTTP. Azure tiene un equilibrador de carga HTTP que pasa por el nombre de puerta de enlace de aplicación.

Se recomienda que se nos puerta de enlace de aplicaciones de Azure cuando:

- Aplicaciones que requieren solicitudes desde la misma sesión de usuario o cliente para llegar a la misma máquina virtual de back-end. Ejemplos de esto harían compras carro aplicaciones y los servidores de correo web.
- Aplicaciones que desea liberar granjas de servidores web aérea de terminación SSL para aprovechar las características de [descarga de SSL](https://f5.com/glossary/ssl-offloading) de puerta de enlace de aplicaciones.
- Aplicaciones, como una red de entrega de contenido, que requieren varias solicitudes HTTP en la misma conexión TCP de ejecución larga enrutar o cargar equilibrada a diferentes servidores de back-end.

Para obtener más información sobre cómo funciona la puerta de enlace de aplicaciones de Azure y cómo puede usar en las implementaciones, lea el artículo [Información general de puerta de enlace de aplicación](../application-gateway/application-gateway-introduction.md).

## <a name="external-load-balancing"></a>Equilibrio de carga externa
Equilibrio de carga externo tiene lugar cuando se carga equilibrada entre los servidores que se encuentra en una red Virtual de Azure de conexiones entrantes de Internet. Equilibrador de carga externo de Azure puede proporcionar esta función y se recomienda usar cuando no requieren las sesiones rápidas o SSL de descarga.

A diferencia de equilibrio de carga basado en HTTP, el equilibrador de carga externo utiliza información en las capas de red y transporte del modelo de redes OSI para tomar decisiones en qué servidor cargar saldo conexión a.

Le recomendamos que use el equilibrio de carga externo siempre que tenga [las aplicaciones sin estado](http://whatis.techtarget.com/definition/stateless-app) Aceptar las solicitudes entrantes de Internet.

Para obtener más información sobre cómo funciona el equilibrador de carga externos de Azure y cómo se puede implementar, por favor, lea el artículo [Introducción de creación de un equilibrador de carga opuestas de Internet en el Administrador de recursos con PowerShell](../load-balancer/load-balancer-get-started-internet-arm-ps.md).

## <a name="internal-load-balancing"></a>Equilibrio de carga interna
Equilibrio de carga interno es similar a Equilibrio de carga externo y utiliza el mismo mecanismo para cargar saldo conexiones a los servidores detrás de ellos. La única diferencia es que el equilibrador de carga en este caso acepta conexiones desde máquinas virtuales que no están en Internet. En la mayoría de los casos, se inician las conexiones que se aceptarán equilibrio de carga de dispositivos de una red Virtual de Azure.

Le recomendamos que use interna equilibrio de carga para escenarios que se beneficiarán de esta función, por ejemplo, cuando tenga que cargar saldo conexiones a servidores de SQL Server o servidores web internos.

Para obtener más información sobre cómo funciona el equilibrio de carga interna de Azure y cómo puede implementar, lea el artículo [Introducción de creación de un equilibrador de carga interno con PowerShell](../load-balancer/load-balancer-get-started-internet-arm-ps.md#update-an-existing-load-balancer).

## <a name="use-global-load-balancing"></a>Usar Equilibrio de carga global
Hace informático de nube pública posible implementar globalmente distribuye aplicaciones que tienen componentes ubicados en centros de datos de todo el mundo. Esto es posible en Microsoft Azure debido a la presencia de un centro de datos global de Azure. A diferencia de las tecnologías mencionadas anteriormente de equilibrio de carga, equilibrio de carga global permite que los servicios disponibles incluso cuando todo centros de datos podrían no estarán disponibles.

Puede obtener este tipo global de equilibrio de carga en Azure aprovechando [Administrador de tráfico de Azure](https://azure.microsoft.com/documentation/services/traffic-manager/). Hace que el Administrador de tráfico es posible cargar saldo conexiones a los servicios según la ubicación del usuario.

Por ejemplo, si el usuario está realizando una solicitud al servicio de la Unión Europea, la conexión se dirige a los servicios que se encuentra en un centro de datos de la UE. Esta parte del tráfico administrador global cargar Equilibrio ayuda a mejorar el rendimiento porque conectarse al centro de datos más cercano es más rápido que conectarse a los centros de datos que están lejos.

En el lado de disponibilidad, equilibrio de carga global se asegura de que el servicio está disponible incluso si un centro de datos completo debe estar disponible.

Por ejemplo, si un centro de datos de Azure debe no están disponible debido a razones medioambientales o debido a interrupciones (como errores de red regional), conexiones a un servicio se transfiere a la más cercana de centro de datos en línea. Este equilibrio de carga global se logra aprovechar las directivas DNS que se pueden crear en el Administrador de tráfico.

Se recomienda usar el Administrador de tráfico de cualquier solución de nube desarrollar que tiene un ámbito distribuido en diferentes regiones y requiere el más alto nivel de actividad posible.

Para obtener más información sobre el Administrador de tráfico de Azure y cómo implementarlo, lea el artículo [¿Qué es el Administrador de tráfico](../traffic-manager/traffic-manager-overview.md).

## <a name="disable-rdpssh-access-to-azure-virtual-machines"></a>Deshabilitar el acceso RDP/SSH a máquinas virtuales de Windows Azure
Es posible llegar a máquinas virtuales de Azure mediante el [Protocolo de escritorio remoto](https://en.wikipedia.org/wiki/Remote_Desktop_Protocol) (RDP) y los protocolos de [Secure Shell](https://en.wikipedia.org/wiki/Secure_Shell) (SSH). Estos protocolos que sea posible administrar máquinas virtuales de ubicaciones remotas y son estándar de la informática en el centro de datos.

El problema de seguridad con el uso de estos protocolos por Internet es que los atacantes pueden utilizar varias técnicas de [fuerza bruta](https://en.wikipedia.org/wiki/Brute-force_attack) para tener acceso a Azure máquinas virtuales de Windows. Una vez que los atacantes tener acceso, pueden utilizar la máquina virtual como un punto de inicio para poner en peligro otros equipos en su red Virtual de Azure o incluso ataque dispositivos de red fuera de Azure.

Por este motivo, recomendamos que desactive acceso directo de RDP y SSH a sus máquinas virtuales de Azure de Internet. Cuando está deshabilitado el acceso directo de RDP y SSH desde Internet, tiene otras opciones que puede usar para tener acceso a estas máquinas virtuales para la administración remota:

- VPN de sitio a punto
- VPN de sitio a sitio
- ExpressRoute

[Punto al sitio VPN](../vpn-gateway/vpn-gateway-point-to-site-create.md) es otro término para una conexión de cliente/servidor VPN de acceso remoto. Una VPN de sitio a punto permite a un solo usuario para conectarse a una red Virtual de Azure a través de Internet. Después de establece la conexión punto a sitio, el usuario podrá usar RDP o SSH para conectarse a cualquier máquinas virtuales de Windows situado en la red Virtual de Azure que el usuario conectado mediante VPN de sitio a punto. Esto supone que el usuario está autorizado para llegar a esas máquinas virtuales.

Sitio de punto VPN es más seguro que conexiones RDP o SSH directas porque el usuario tiene que autenticar dos veces antes de conectarse a una máquina virtual. En primer lugar, el usuario debe autenticar (y autorizar) para establecer la conexión VPN de sitio a punto; en segundo término, el usuario debe autenticar (y autorizar) para establecer la sesión RDP o SSH.

Una [VPN de sitio a sitio](../vpn-gateway/vpn-gateway-site-to-site-create.md) conecta toda una red a otra red a través de Internet. Puede usar una VPN de sitio a otro para conectarse a su red local a una red Virtual de Azure. Si implementa una VPN de sitio a sitio, los usuarios de su red local podrá conectarse a máquinas virtuales de Windows en su red Virtual de Azure mediante el protocolo RDP o SSH sobre la conexión VPN de sitio a sitio y no requiere para permitir el acceso directo de RDP o SSH a través de Internet.

También puede utilizar un vínculo WAN dedicado para proporcionar funcionalidad similar a la VPN de sitio a otro. Las diferencias principales son 1. el vínculo WAN dedicado no recorre Internet y 2. vínculos WAN dedicados suelen ser más estabilidad y rendimiento. Azure proporciona una solución de vínculo WAN dedicado en forma de [ExpressRoute](https://azure.microsoft.com/documentation/services/expressroute/).

## <a name="enable-azure-security-center"></a>Habilitar el centro de seguridad de Azure
Centro de seguridad de Azure le ayuda a evitar, detectar y responder a amenazas y proporciona que mayor visibilidad y control, la seguridad de los recursos de Azure. Proporciona seguridad integrada supervisión y la directiva de administración de las suscripciones de Azure, ayuda a detecta amenazas que pueden incluirse en caso contrario, problemas y funciona con un amplio ecosistema de soluciones de seguridad.

Centro de seguridad de Azure le ayuda a optimizar y supervisar la seguridad de la red por:

- Proporcionar recomendaciones de seguridad de red
- Supervisar el estado de la configuración de seguridad de red
- Avisarle de red según amenazas tanto en los niveles de red y del punto final

Le recomendamos encarecidamente que habilite el centro de seguridad de Azure para todas las instalaciones de Azure.

Para obtener más información sobre el centro de seguridad de Azure y cómo habilitar para las implementaciones, lea el artículo [Introducción al centro de seguridad de Azure](../security-center/security-center-intro.md).

## <a name="securely-extend-your-datacenter-into-azure"></a>Ampliar de forma segura su centro de datos en Azure
TI de las empresas muchas organizaciones desean para expandir en la nube en lugar de crecimiento sus centros de datos local. Esta expansión representa una extensión de infraestructura de TI existente en la nube pública. Aprovechando local entre opciones de conectividad es posible tratar sus redes Virtual de Azure como otra subred en la infraestructura de red local.

Sin embargo, hay una gran cantidad de problemas de diseño y que se abordan en primer lugar. Esto es especialmente importante en el área de seguridad de la red. Una de las mejores formas de comprender cómo se abordar este tipo de diseño es ver un ejemplo.

Microsoft ha creado el [Diagrama de arquitectura de referencia de extensión de centro de datos](https://gallery.technet.microsoft.com/Datacenter-extension-687b1d84#content) y materiales de soporte para ayudarle a comprender cuál sería como una extensión de centro de datos. Proporciona un ejemplo de implementación de referencia que puede usar para planear y diseñar una extensión de centro de datos empresariales seguros en la nube. Se recomienda que lea este documento para hacerse una idea de los componentes clave de una solución segura.

Para obtener más información acerca de cómo ampliar de forma segura su centro de datos en Azure, consulte ver el vídeo [A Microsoft Azure ampliar su centro de datos](https://www.youtube.com/watch?v=Th1oQQCb2KA).
