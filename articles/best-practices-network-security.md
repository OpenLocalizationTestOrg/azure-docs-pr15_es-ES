<properties
   pageTitle="Seguridad de red y servicios de nube de Microsoft | Microsoft Azure"
   description="Obtenga información sobre algunas de las características clave disponibles en Azure para ayudar a crear entornos de red seguros"
   services="virtual-network"
   documentationCenter="na"
   authors="tracsman"
   manager="rossort"
   editor=""/>

<tags
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="03/14/2016"
   ms.author="jonor;sivae"/>

# <a name="microsoft-cloud-services-and-network-security"></a>Seguridad de red y servicios de nube de Microsoft

Servicios de nube de Microsoft ofrecen servicios ampliados y la infraestructura, funcionalidades de clase empresarial y muchas opciones para la conectividad de híbrido. Pueden elegir los clientes tener acceso a estos servicios a través de Internet o con Azure ExpressRoute, que proporciona la conectividad de red privada. La plataforma de Microsoft Azure permite a los clientes ampliar su infraestructura en la nube sin problemas y crear arquitecturas de varios niveles. Además, proveedores de terceros puede habilitar capacidades mejoradas que ofrece servicios de seguridad y accesorios virtuales. Este documento proporciona una descripción general de seguridad y problemas de arquitectura clientes deben tener en cuenta cuando se usa servicios de nube de Microsoft, tiene acceso a través de ExpressRoute. También trata sobre crear servicios sea más seguros en redes virtuales Azure.

## <a name="fast-start"></a>Inicio rápido
El siguiente gráfico lógica puede dirigir a un ejemplo específico de las numerosas técnicas de seguridad disponibles con la plataforma Windows Azure. Para referencia rápida, busque el ejemplo que mejor se adapte a su caso. Para obtener una explicación más completa, continúe leyendo a través del papel.
![Diagrama de flujo de opciones de seguridad][0]

[Ejemplo 1: Crear una red de perímetro (también conocida como DMZ, DMZ y subred filtrada) para ayudar a proteger aplicaciones con grupos de seguridad de red (NSGs).](#example-1-build-a-simple-dmz-with-nsgs)</br>
[Ejemplo 2: Crear una red perimetral para ayudar a proteger aplicaciones con un firewall y NSGs.](#example-2-build-a-dmz-to-protect-applications-with-a-firewall-and-nsgs)</br>
[Ejemplo 3: Crear una red perimetral para ayudar a proteger una red con un firewall, ruta definidas por el usuario (UDR) y GSN.](#example-3-build-a-dmz-to-protect-networks-with-a-firewall-udr-and-nsg)</br>
[Ejemplo 4: Agregar una conexión de híbrido con una red privada virtual (VPN) de dispositivo virtual de sitio a otro.](#example-4-adding-a-hybrid-connection-with-a-site-to-site-virtual-appliance-vpn)</br>
[Ejemplo 5: Agregar una conexión de híbrido con una puerta de enlace a sitios, Azure VPN.](#example-5-adding-a-hybrid-connection-with-a-site-to-site-azure-gateway-vpn)</br>
[Ejemplo 6: Agregar una conexión de híbrido con ExpressRoute.](#example-6-adding-a-hybrid-connection-with-expressroute)</br>
Ejemplos para agregar conexiones entre redes virtuales, alta disponibilidad y servicio encadenar se agregará a este documento en los próximos meses.

## <a name="microsoft-compliance-and-infrastructure-protection"></a>Protección de cumplimiento y la infraestructura de Microsoft
Microsoft ha adoptado una posición de liderazgo auxiliares iniciativas de cumplimiento necesarios para que los clientes de empresa. Los siguientes son algunos de los certificados de cumplimiento para Azure: ![distintivos de cumplimiento de Azure][1]

Para obtener más detalles, consulte la información de cumplimiento en el [Centro de confianza de Microsoft](https://azure.microsoft.com/support/trust-center/compliance/).

Microsoft tiene un enfoque integral para proteger la infraestructura de nube necesaria para ejecutar ampliados servicios globales. Infraestructura de nube de Microsoft incluye hardware, software, redes y administrativos y personal de operaciones, además de los centros de datos físicos.

![Características de seguridad de Azure][2]

Este enfoque proporciona una base más segura para los clientes a implementar sus servicios en la nube de Microsoft. El siguiente paso es para que los clientes a diseñar y crear una arquitectura de seguridad para proteger estos servicios.

## <a name="traditional-security-architectures-and-perimeter-networks"></a>Arquitectura de seguridad tradicional y redes perimetrales
Aunque Microsoft invierte considerablemente en proteger la infraestructura de nube, los clientes también deben proteger sus servicios en la nube y grupos de recursos. Utilizar un enfoque de seguridad proporciona la mejor defensa. Una zona de seguridad de la red de perímetro protege los recursos de la red interna de una red de confianza. Una red perimetral hace referencia a los bordes o partes de la red que se encuentran entre Internet y la infraestructura de TI de empresa protegida.

En redes de empresa normal, la infraestructura central se enriquece intensamente en los perímetros con varias capas de dispositivos de seguridad. El límite de cada capa consiste en dispositivos y puntos de cumplimiento de la directiva. Dispositivos incluyen las siguientes: los firewalls, prevención distribuido denegación de servicio, detección de intrusiones o sistemas de protección (identificadores y direcciones IP) y dispositivos VPN. Aplicación de directivas puede tomar la forma de las directivas de firewall, listas de control de acceso (ACL) o ruta en concreto. La primera línea de defensa de la red, directamente acepte tráfico entrante desde Internet, es una combinación de estos mecanismos para bloquear ataques y tráfico dañino permitiendo solicitudes legítimas aún más en la red. Este tráfico se dirige directamente a los recursos en la red perimetral. Ese recurso puede, a continuación, "hablar" con recursos más profundos en la red, tránsito el siguiente límite de validación primera. La capa externa se denomina la red perimetral porque este parte de la red se expone a Internet, normalmente con algún tipo de protección en ambos lados. La siguiente ilustración muestra un ejemplo de una única subred perimetral en una red corporativa, con dos límites de seguridad.

![Una red perimetral en una red corporativa][3]

Hay muchos arquitecturas utilizadas para implementar una red perimetral de un equilibrador de carga simple delante de una granja de servidores web, a una red de perímetro de subred varias con distintos mecanismos en cada límite para bloquear el tráfico y proteger las capas más profundas de la red corporativa. Cómo se crea la red perimetral depende de las necesidades específicas de la organización y su tolerancia de riesgo global.

Como los clientes mover sus cargas de trabajo a nubes públicas, es fundamental para admitir características similares de arquitectura de red de perímetro en Azure para satisfacer requisitos de seguridad y cumplimiento. Este documento proporciona instrucciones detalladas sobre cómo los clientes pueden crear un entorno de red seguro en Azure. Se centra en la red perimetral, pero también incluye una discusión completa de muchos aspectos de seguridad de la red. Esta explicación de comunicar a las siguientes preguntas:

- ¿Cómo se crea una red de perímetro en Azure?
- ¿Cuáles son algunas de las características de Azure disponibles para generar la red perimetral?
- ¿Cómo pueden protegerse las cargas de trabajo de back-end?
- ¿Cómo se controlan las comunicaciones de Internet a las cargas de trabajo en Azure?
- ¿Cómo se pueden proteger las redes locales de implementaciones en Azure?
- ¿Cuándo se deben utilizar características de seguridad de Azure nativa frente a los servicios o dispositivos de terceros?

El diagrama siguiente muestra distintas capas de seguridad que Azure proporciona a los clientes. Estas capas son nativo de la plataforma Windows Azure propio y funciones definidas por el cliente:

![Arquitectura de seguridad de Azure][4]

Entrada de Internet, DDoS Azure ayuda a proteger contra ataques a gran escala de Azure. El siguiente nivel es extremos públicos definidas por el usuario, que se usan para determinar qué tráfico puede pasar a través del servicio de nube a la red virtual. Azure nativa virtual aislamiento completo de todas las demás redes asegura de aislamiento de la red y que el tráfico fluye sólo a través de los métodos y rutas de acceso de usuario configurado. Estas rutas de acceso y métodos son la capa siguiente, donde NSGs, UDR y dispositivos de red virtual pueden utilizarse para crear límites de seguridad para proteger las implementaciones de aplicación en la red protegida.

La siguiente sección proporciona una descripción general de redes virtuales Azure. Estas redes virtuales creadas por los clientes y son lo que sus cargas de trabajo implementadas están conectados a. Redes virtuales son la base de todas las características de seguridad de red necesaria para establecer una red perimetral para proteger implementaciones de cliente en Azure.

## <a name="overview-of-azure-virtual-networks"></a>Información general de redes virtuales de Azure
Antes de que el tráfico de Internet puede obtener acceso a las redes virtuales Azure, hay dos niveles de seguridad inherente de la plataforma Windows Azure:

1.  **Protección de DDoS**: protección DDoS es un nivel de la red física Azure que protege la plataforma de Windows Azure propio de ataques basados en Internet a gran escala. Estos ataques use varios nodos de "robots" en un intento para saturar un servicio de Internet. Azure tiene una sólida protección DDoS mesh en toda la conectividad de Internet entrante. Este nivel de protección DDoS no tiene usuario configurable atributos y no es accesible al cliente. Protege Azure como una plataforma de ataques a gran escala, pero no se protegen directamente aplicaciones cliente individual. Capas adicionales de resistencia se pueden configurar el cliente ataques localizados. Por ejemplo, si un cliente A se ataque con un ataque DDoS a gran escala en un extremo del público, Azure bloquea las conexiones a dicho servicio. Cliente A podría conmutar a otra red virtual o extremo no relacionados con el ataque para restaurar el servicio del servicio. Se debe tener en cuenta que aunque puede verse afectado cliente A en ese extremo, ningún otro servicio fuera de ese extremo ¿se verá afectado. Además, otros servicios y clientes no verá ningún impacto de dicho ataque.
2.  **Extremos de servicio**: extremos permiten nube tiene público Internet direcciones y puertos IP expuestos de los grupos de servicios o recursos. El extremo usará traducción de direcciones de red (NAT) para enrutar el tráfico a la dirección interna y el puerto de la red virtual Azure. Esta es la ruta de acceso principal para el tráfico externo pasar a la red virtual. Los extremos de servicio son configurable por el usuario para determinar qué tráfico se pasa en y, a continuación, cómo y dónde se traduce a de la red virtual.

Una vez que el tráfico alcanza la red virtual, hay muchas características que se incluyen en reproducir. Azure redes virtuales son la base para los clientes adjuntar sus cargas de trabajo y donde se aplica la seguridad a nivel de red básica. Es una red privada (una superposición de red virtual) en Azure para los clientes con las características y las características siguientes:
 
- **Aislamiento de tráfico**: una red virtual es el límite de aislamiento de tráfico en la plataforma Windows Azure. Máquinas virtuales (VM) en una red virtual no se puede comunicar directamente a máquinas virtuales en una red virtual diferente, aunque ambas redes virtuales creados por el mismo cliente. Esto es una propiedad fundamental que garantiza máquinas virtuales de cliente y comunicación permanece privada dentro de una red virtual.
- **Topología de varios niveles**: redes virtuales permiten a los clientes definir la topología de varios niveles y asignar subredes y designar espacios de direcciones independientes para distintos elementos o "niveles" de sus cargas de trabajo. Estas agrupaciones lógicas topologías permiten a los clientes definir la directiva de acceso distintas en función de los tipos de carga de trabajo y también controlan flujos de tráfico entre los niveles.
- **Conectividad entre local**: los clientes pueden establecer conectividad entre local entre una red virtual y varios sitios local u otras redes virtuales en Azure. Para ello, los clientes puedan usar puertas de enlace de Azure VPN o dispositivos virtuales de red de terceros. Azure es compatible con el sitio a sitio (S2S) VPN con protocolos IPsec/IKE estándar y conectividad de ExpressRoute privado.
- **GSN** permite a los clientes crear reglas (ACL) en el nivel de detalle que desee: interfaces, máquinas virtuales individuales o subredes virtuales de red. Los clientes pueden controlar el acceso al permitir o denegar la comunicación entre las cargas de trabajo dentro de una red virtual de los sistemas de redes del cliente a través de conectividad entre local, o dirigir comunicaciones de Internet.
- **UDR** y **Reenvío IP** permite a los clientes definir las rutas de comunicación entre diferentes niveles dentro de una red virtual. Los clientes pueden implementar un servidor de seguridad, identificadores o direcciones IP y otros dispositivos virtuales y enrutar el tráfico de red en estos dispositivos de seguridad para la aplicación de directivas de límite de seguridad, auditoría e inspección.
- **Dispositivos de red virtuales** de Azure Marketplace: dispositivos de seguridad como identificadores/IP, equilibradores de carga y los firewalls están disponibles en el catálogo de soluciones de Azure y la Galería de imágenes de máquina virtual. Los clientes pueden implementar estos dispositivos en sus redes virtuales y, en concreto, en los límites de seguridad (incluidas las subredes de la red de perímetro) para completar un entorno de red seguro de varios niveles.

Con estas características y funcionalidades, un ejemplo de cómo se puede construir una arquitectura de red de perímetro en Azure es la siguiente:

![Una red perimetral en una red virtual de Azure][5]

## <a name="perimeter-network-characteristics-and-requirements"></a>Requisitos y características de la red de perímetro
La red perimetral está diseñada para ser el front-end de la red, interactúa directamente la comunicación de Internet. Los paquetes de entrada deben fluyen a través de los dispositivos de seguridad, como el firewall, identificadores y direcciones IP, antes de llegar a los servidores back-end. Paquetes de enlazado a Internet de las cargas de trabajo pueden pasar los dispositivos de seguridad en la red perimetral para la aplicación de directivas, inspección y fines de auditoría, antes de salir de la red. Además, la red perimetral puede hospedar las puertas de enlace VPN local entre entre redes virtual de cliente y local.

### <a name="perimeter-network-characteristics"></a>Características de la red de perímetro
Hacer referencia a la figura anterior, son algunas de las características de una red perimetral buena como sigue:

- A través de Internet:
    - La subred perimetral propio es a través de Internet, comunicarse directamente con Internet.
    - Direcciones IP pública, VIP o extremos de servicio pasan el tráfico de Internet a la red front-end y los dispositivos.
    - El tráfico de Internet pasa a través de los dispositivos de seguridad antes de otros recursos de la red front-end.
    - Si está habilitada la seguridad saliente, el tráfico pasa a través de dispositivos de seguridad, como el último paso, antes de pasar a Internet.
- Protegido de red:
    - No hay ninguna ruta de acceso directo de Internet a la infraestructura central.
    - Canales a la infraestructura central deben recorrer dispositivos de seguridad como NSGs, firewalls o dispositivos VPN.
    - No deben puente de otros dispositivos de Internet y la infraestructura central.
    - Dispositivos de seguridad en el a través de Internet y la red protegida opuestas límites de la red de perímetro (por ejemplo, los dos iconos que aparecen en la figura anterior del firewall) realmente pueden ser un solo dispositivo virtual con reglas diferenciadas o interfaces para cada límite. (Es decir, un dispositivo, lógicamente separado, administrar la carga para los límites de la red perimetral).
- Otras prácticas y las restricciones comunes:
    - Cargas de trabajo no deben almacenar información crítica del negocio.
    - Acceso y actualizaciones de configuraciones de red perimetral e implementaciones están limitadas a solo los administradores autorizados.

### <a name="perimeter-network-requirements"></a>Requisitos de la red de perímetro
Para habilitar estas características, siga estas instrucciones sobre los requisitos de una red virtual para implementar una red de perímetro correctamente:

- **Arquitectura de subred:** Especifique la red virtual que está dedicada subred completa como la red de perímetro separada de otras subredes en la misma red virtual. Así se garantiza que el tráfico entre la red perimetral y otros flujos de niveles de subred interna o privada a través de un firewall o dispositivo virtual de identificadores o direcciones IP en los límites de subred con rutas definidas por el usuario.
- **GSN:** La subred perimetral propio debe estar abierta para permitir la comunicación con Internet, pero no significa que los clientes deben estar omitiendo NSGs. Siga las prácticas comunes de seguridad para reducir la expone de red que se exponen en Internet. Bloquear los intervalos de direcciones remoto permitidos el acceso a las implementaciones o la aplicación específica puertos y protocolos de que están abiertos. Puede haber ocasiones, sin embargo, en el que esto no siempre es posible. Por ejemplo, si los clientes tienen un sitio Web externo en Azure, la red perimetral deben permitir las solicitudes entrantes de web desde cualquier dirección IP pública, pero solo debe abrir los puertos de la aplicación web: TCP: 80 y TCP:443.
- **Tabla de enrutamiento:** La subred perimetral propio debería poder comunicarse directamente a Internet, pero no debe permitir la comunicación directa a y de las redes de fin o local atrás sin pasar por un dispositivo o servidor de seguridad.
- **Configuración de dispositivo de seguridad:** Para redirigir e inspeccionar paquetes entre la red perimetral y el resto de las redes protegidas, los dispositivos de seguridad como firewall, identificadores y direcciones IP de los dispositivos pueden ser hosts múltiples. Pueden tener NIC independientes para la red perimetral y las subredes back-end. Las NIC en la red perimetral comunican directamente a y desde Internet, con el correspondientes NSGs y la tabla de enrutamiento de red de perímetro. La conexión a las subredes back-end de NIC más restringidos NSGs y tablas de enrutamiento correspondiente subred back-end.
- **Funcionalidad de dispositivo de seguridad:** Los dispositivos de seguridad implementados en la red perimetral suele realizan las siguientes funciones:
    - Firewall: Exigir reglas de firewall o directivas de control de acceso para las solicitudes entrantes.
    - Detección y prevención de amenazas: detectar y mitigar ataques de Internet.
    - Auditoría y registro: mantener registros detallados para análisis y auditoría.
    - Proxy inverso: redirigir las solicitudes entrantes a los servidores back-end correspondientes. Esto implica la asignación y traducir las direcciones de destino en los dispositivos front-end, normalmente los firewalls, a las direcciones de servidor back-end.
    - Reenviar proxy: proporcionar NAT y la realización de auditoría para la comunicación iniciada desde dentro de la red virtual a Internet.
    - Enrutador: Desvío de llamadas entrante y de subred entre el tráfico dentro de la red virtual.
    - Dispositivo VPN: actúa como las puertas de enlace VPN local cruzado para la conectividad VPN local cruzados entre redes de cliente local y Azure virtual.
    - Servidor VPN: aceptación de clientes VPN conectarse a redes virtuales Azure.

>[AZURE.TIP] Separar los siguientes dos grupos: las personas autorización para acceder a la engranaje de seguridad de perímetro de la red y las personas autorizadas como los administradores de operaciones, implementación o desarrollo de aplicaciones. Mantener estos grupos separados permite la separación de funciones e impide que una persona sola Omitir seguridad de aplicaciones y controles de seguridad de red.

### <a name="questions-to-be-asked-when-building-network-boundaries"></a>Preguntas que se le pida al crear los límites de red
En esta sección, a menos que se menciona específicamente, el término "redes" hace referencia a redes virtuales Azure privadas creadas por un administrador de suscripción. El término no hace referencia a las redes físicas subyacentes dentro de Azure.

Además, redes virtuales Azure a menudo se usan para extender redes local tradicional. Es posible incorporar a sitios o soluciones de red ExpressRoute híbrido con arquitecturas de red perimetral. Este es un factor importante en la creación de límites de seguridad de red.

Las tres preguntas siguientes son fundamentales para responder cuando se crea una red con una red perimetral y varios límites de seguridad.

#### <a name="1-how-many-boundaries-are-needed"></a>¿1) límites de cuántos son necesarias?
El primer punto de decisión es decida cuántas límites de seguridad son necesarias en un escenario determinado:

- Borde de una sola: uno en la red de perímetro front-end, entre la red virtual e Internet.
- Dos límites: uno en el lado de Internet de la red perimetral y otro entre la subred perimetral y las subredes de back-end de las redes virtuales Azure.
- Tres límites: una en el lado de Internet de la red perimetral, entre la red perimetral y subredes de back-end y uno entre las subredes back-end y la red local.
- Límites N: un número de variables. Dependiendo de los requisitos de seguridad, hay realmente cualquier número de límites de seguridad que se pueden aplicar en una red dada.

El número y tipo de límites necesitan varían según tolerancia de riesgos de la empresa y la situación específica implementada. Suele ser una decisión conjunta realizada por varios grupos dentro de una organización, incluyendo a menudo un riesgo y grupo de cumplimiento, una red y grupo de plataforma y un equipo de desarrollo de aplicaciones. Personas con conocimientos de seguridad, los datos implicados y las tecnologías que se usa deben tener un ejemplo de esta decisión para garantizar la opción de seguridad apropiada para cada aplicación.

>[AZURE.TIP] Use el valor mínimo de límites que cumplan los requisitos de seguridad para una situación determinada. Con más límites más difíciles pueden ser operaciones y solución de problemas, así como la sobrecarga de administración que implica la administración de las directivas de límite varios con el tiempo. Sin embargo, los límites de insuficiente para aumentan el riesgo. Encontrar el equilibrio es fundamental.

![Red híbrido con tres límites de seguridad][6]

La figura anterior muestra una vista de alto nivel de una red de límite de seguridad de tres. Los límites están entre la red perimetral y Internet, las Azure front-end y back-end subredes privadas y la subred Azure de back-end y la red corporativa local.

#### <a name="2-where-are-the-boundaries-located"></a>¿2), donde se encuentran los límites?
Una vez que se ha decidido el número de límites, dónde se implementan es el punto de decisión siguiente. Por lo general, hay tres opciones:
- Uso de un servicio intermediario basados en Internet (por ejemplo, un basada en nube Web aplicación firewall, que no se describe en este documento)
- Usar las características nativas y dispositivos de red virtual en Azure
- Uso de dispositivos físicos en la red local

En redes puramente Azure, las opciones son características de Azure nativas (por ejemplo, equilibradores de carga de Azure) o red virtual dispositivos desde el ecosistema de socios enriquecidos de Azure (por ejemplo, los firewalls del punto de verificación).

Si hay un límite entre Azure y una red local, los dispositivos de seguridad pueden residen en ambos lados de la conexión (o ambas caras). Por lo tanto una decisión en la ubicación para colocar el engranaje de seguridad.

En la ilustración anterior, la red de perímetro de Internet y los límites de frente a back-end totalmente dentro de Azure y deben ser características de Azure nativas o red virtuales dispositivos. En el lado de Azure o el lado local o incluso una combinación de dispositivos en ambos lados, podrían ser dispositivos de seguridad en el borde entre Azure (back-end subred) y la red corporativa. Puede haber significativas ventajas y desventajas en cualquiera de las opciones que se deben tener en cuenta gravemente.

Por ejemplo, usando engranaje de seguridad física existente en el lado de la red local tiene la ventaja de que no se necesita ningún engranaje nuevo. Solo necesita nueva configuración. La desventaja, sin embargo, es que todo el tráfico debe volver de Azure a la red local deben ser vistos por el engranaje de seguridad. Por tanto, el tráfico de Azure a Azure puede suponer latencia significativa y afectan al rendimiento de la aplicación y usuario experiencia, si se ha tenido volver a la aplicación de directivas de seguridad de la red local.

#### <a name="3-how-are-the-boundaries-implemented"></a>¿3) cómo se implementan los límites?
Cada límite de seguridad probablemente tendrá requisitos de capacidad diferente (por ejemplo, identificadores y las reglas de firewall en el lado de Internet de la red perimetral, pero solo ACL entre la red perimetral y back-end subred). Decidir qué dispositivos desea usar depende de los requisitos de seguridad y escenario. En la siguiente sección, ejemplos 1, 2 y 3 describen algunas opciones que se pueden utilizar. Revisar las características de red nativo Azure y los dispositivos disponibles en Azure desde el ecosistema de socios, muestra las opciones de miles para resolver prácticamente cualquier escenario.

Otro punto de decisión de implementación de claves es cómo conectarse a la red local con Azure. ¿Debe usar un dispositivo virtual de red o de la puerta de enlace virtual Azure? Estas opciones se tratan con más detalle en la sección siguiente (ejemplos 4, 5 y 6).

Además, puede que se necesiten tráfico entre redes virtuales dentro de Azure. Estos escenarios se agregará en una fecha posterior.

Una vez que conozca las respuestas a las preguntas anteriores, la sección de [Inicio rápido](#fast-start) puede ayudar a identificar qué ejemplos son las más apropiados para un escenario determinado.

## <a name="examples-building-security-boundaries-with-azure-virtual-networks"></a>Ejemplos: Creación de límites de seguridad en redes virtuales Azure
### <a name="example-1-build-a-perimeter-network-to-help-protect-applications-with-nsgs"></a>Ejemplo 1: Crear una red perimetral para ayudar a proteger aplicaciones con NSGs
[Volver a Inicio rápido](#fast-start) | [detallado generar instrucciones para este ejemplo][Example1]

![Red perimetral con GSN de entrada][7]

#### <a name="environment-description"></a>Descripción del entorno
En este ejemplo, hay una suscripción que contiene lo siguiente:

- Dos servicios de nube: "FrontEnd001" y "BackEnd001"
- Una red virtual, "CorpNetwork", con dos subredes: "Cliente" y "Back-end"
- Un grupo de seguridad de red que se aplica a ambas subredes
- Un servidor de Windows que representa un servidor de aplicaciones web ("IIS01")
- Dos servidores de Windows que representan servidores de back-end de aplicación ("AppVM01", "AppVM02")
- Un servidor de Windows que representa un servidor DNS ("DNS01")

Para las secuencias de comandos y una plantilla de administrador de recursos de Azure, consulte las [instrucciones de generación detallado][Example1].

#### <a name="nsg-description"></a>Descripción del GSN
En este ejemplo, un grupo GSN está integrado y, a continuación, cargar con seis reglas.

>[AZURE.TIP] Por lo general, debe crear las reglas de "Permitir" específicas en primer lugar, seguidas de las reglas de "Denegar" más genéricas. La prioridad dada determina las reglas que se evalúan en primer lugar. Una vez que se encuentra el tráfico a aplicar a una regla específica, no se evalúan otras reglas. Pueden aplicar reglas de GSN en la dirección de entrada o salida (desde la perspectiva de la subred).

Mediante declaración, que se crean las siguientes reglas para el tráfico entrante:

1.  Se permite el tráfico DNS interno (puerto 53).
2.  Se permite el tráfico RDP (puerto 3389) desde Internet a cualquier máquina Virtual.
3.  Se permite el tráfico HTTP (puerto 80) de Internet en el servidor web (IIS01).
4.  Se permite el tráfico (todos los puertos) desde IIS01 a AppVM1.
5.  Denegado cualquier (todos los puertos) el tráfico de Internet a la red virtual completo (ambas subredes).
6.  Denegado el tráfico (todos los puertos) de la subred front-end a la subred back-end.

Con estas reglas enlazado cada subred, si una solicitud HTTP era entrante desde Internet en el servidor web, ambas reglas 3 (permitir) y 5 (denegar) se aplican. Pero como regla 3 tiene una prioridad más alta, solo desea aplicar y regla 5 no debería tener en cuenta. Por lo tanto la solicitud HTTP podrán el servidor web. Si ese mismo tráfico estaba intentando alcanzar el servidor DNS01, regla 5 (denegar) sería el primero en aplicar y, a continuación, el tráfico no se permitirá pasar al servidor. Regla 6 (denegar) bloquea la subred front-end de hablar a la subred back-end (excepto el tráfico permitido en las reglas 1 y 4). Esto protege la red de back-end en caso de que un intruso comprometa la aplicación web en el front-end. El intruso habría acceso limitado a la red de "protegida" back-end (solo a los recursos que se exponen en el servidor de AppVM01).

Hay una regla de salida predeterminado que permite tráfico saliente a Internet. En este ejemplo, estamos permitir el tráfico saliente y no modificar las reglas de salida. Para bloquear el tráfico en ambas direcciones, se requiere el enrutamiento definidas por el usuario (consulte el ejemplo 3).

#### <a name="conclusion"></a>Conclusión
Esto es una forma relativamente simple y sencilla de aislar la subred back-end de tráfico entrante. Para obtener más información, consulte las [instrucciones de generación detallado][Example1]. Estas instrucciones incluyen:

- Cómo crear esta red perimetral con secuencias de comandos de PowerShell.
- Cómo crear esta red perimetral con una plantilla de administrador de recursos de Azure.
- Descripciones detalladas de cada comando GSN.
- Escenarios de flujo de tráfico detallada, que muestra cómo el tráfico está permitido o denegado en cada capa.


 ### <a name="example-2-build-a-perimeter-network-to-help-protect-applications-with-a-firewall-and-nsgs"></a>Ejemplo 2: Crear una red perimetral para ayudar a proteger aplicaciones con un firewall y NSGs
[Volver a Inicio rápido](#fast-start) | [detallado generar instrucciones para este ejemplo][Example2]

![Red perimetral con NVA y GSN de entrada][8]

#### <a name="environment-description"></a>Descripción del entorno
En este ejemplo, hay una suscripción que contiene lo siguiente:

- Dos servicios de nube: "FrontEnd001" y "BackEnd001"
- Una red virtual, "CorpNetwork", con dos subredes: "Cliente" y "Back-end"
- Un grupo de seguridad de red que se aplica a ambas subredes
- Un dispositivo virtual de red, en este caso un firewall, conectado a la subred front-end
- Un servidor de Windows que representa un servidor de aplicaciones web ("IIS01")
- Dos servidores de Windows que representan servidores de back-end de aplicación ("AppVM01", "AppVM02")
- Un servidor de Windows que representa un servidor DNS ("DNS01")

Para las secuencias de comandos y una plantilla de administrador de recursos de Azure, consulte las [instrucciones de generación detallado][Example2].

#### <a name="nsg-description"></a>Descripción del GSN
En este ejemplo, un grupo GSN está integrado y, a continuación, cargar con seis reglas.

>[AZURE.TIP] Por lo general, debe crear las reglas de "Permitir" específicas en primer lugar, seguidas de las reglas de "Denegar" más genéricas. La prioridad dada determina las reglas que se evalúan en primer lugar. Una vez que se encuentra el tráfico a aplicar a una regla específica, no se evalúan otras reglas. Pueden aplicar reglas de GSN en la dirección de entrada o salida (desde la perspectiva de la subred).

Mediante declaración, que se crean las siguientes reglas para el tráfico entrante:

1.  Se permite el tráfico DNS interno (puerto 53).
2.  Se permite el tráfico RDP (puerto 3389) desde Internet a cualquier máquina Virtual.
3.  Se permite todo el tráfico de Internet (todos los puertos) en el dispositivo de red virtual (firewall).
4.  Se permite el tráfico (todos los puertos) desde IIS01 a AppVM1.
5.  Denegado cualquier (todos los puertos) el tráfico de Internet a la red virtual completo (ambas subredes).
6.  Denegado el tráfico (todos los puertos) de la subred front-end a la subred back-end.

Con estas reglas enlazado cada subred, si una solicitud HTTP era entrante desde Internet, el firewall, ambas reglas 3 (permitir) y 5 (denegar) se aplican. Pero como regla 3 tiene una prioridad más alta, solo desea aplicar y regla 5 no debería tener en cuenta. Por lo tanto la solicitud HTTP podrán el firewall. Si ese mismo tráfico estaba intentando alcanzar el servidor IIS01, aunque se encuentra en la subred front-end, regla 5 (denegar) se aplican, y no se permitirá el tráfico para pasar al servidor. Regla 6 (denegar) bloquea la subred front-end de hablar a la subred back-end (excepto el tráfico permitido en las reglas 1 y 4). Esto protege la red de back-end en caso de que un intruso comprometa la aplicación web en el front-end. El intruso habría acceso limitado a la red de "protegida" back-end (solo a los recursos que se exponen en el servidor de AppVM01).

Hay una regla de salida predeterminado que permite tráfico saliente a Internet. En este ejemplo, estamos permitir el tráfico saliente y no modificar las reglas de salida. Para bloquear el tráfico en ambas direcciones, se requiere el enrutamiento definidas por el usuario (consulte el ejemplo 3).

#### <a name="firewall-rule-description"></a>Descripción de la regla de Firewall
En el firewall, deben crear reglas de reenvío. Dado que este ejemplo solo enruta el tráfico de Internet en enlazado al servidor de seguridad y, a continuación, en el servidor web, de reenvío solo una red traducción de direcciones (NAT) es necesaria la regla.

La regla de reenvío acepta cualquier dirección de origen de entrada que visita el firewall intentando alcanzar HTTP (puerto 80 o 443 para HTTPS). Ha enviado fuera de la interfaz local del firewall y redirige al servidor web con la dirección IP de 10.0.1.5.

#### <a name="conclusion"></a>Conclusión
Se trata de una forma relativamente sencilla de protección de la aplicación con un firewall y aislar la subred back-end de tráfico entrante. Para obtener más información, consulte las [instrucciones de generación detallado][Example2]. Estas instrucciones incluyen:

- Cómo crear esta red perimetral con secuencias de comandos de PowerShell.
- Cómo crear este ejemplo con una plantilla de administrador de recursos de Azure.
- Descripción detallada de cada regla GSN de comandos y el firewall.
- Escenarios de flujo de tráfico detallada, que muestra cómo el tráfico está permitido o denegado en cada capa.

### <a name="example-3-build-a-perimeter-network-to-help-protect-networks-with-a-firewall-udr-and-nsg"></a>Ejemplo 3: Crear una red perimetral para ayudar a proteger una red con un firewall, UDR y NSG
[Volver a Inicio rápido](#fast-start) | [detallado generar instrucciones para este ejemplo][Example3]

![Red de perímetro bidireccional con NVA, NSG y UDR][9]

#### <a name="environment-description"></a>Descripción del entorno
En este ejemplo, hay una suscripción que contiene lo siguiente:

- Tres servicios de nube: "SecSvc001", "FrontEnd001" y "BackEnd001"
- Una red virtual, "CorpNetwork", con tres subredes: "SecNet", "Cliente" y "Back-end"
- Un dispositivo virtual de red, en este caso un firewall, conectado a la subred SecNet
- Un servidor de Windows que representa un servidor de aplicaciones web ("IIS01")
- Dos servidores de Windows que representan servidores de back-end de aplicación ("AppVM01", "AppVM02")
- Un servidor de Windows que representa un servidor DNS ("DNS01")

Para las secuencias de comandos y una plantilla de administrador de recursos de Azure, consulte las [instrucciones de generación detallado][Example3].

#### <a name="udr-description"></a>Descripción de UDR
De forma predeterminada, las rutas de sistema siguientes se definen como:

        Effective routes : 
         Address Prefix    Next hop type    Next hop IP address Status   Source     
         --------------    -------------    ------------------- ------   ------     
         {10.0.0.0/16}     VNETLocal                            Active   Default    
         {0.0.0.0/0}       Internet                             Active   Default    
         {10.0.0.0/8}      Null                                 Active   Default    
         {100.64.0.0/10}   Null                                 Active   Default    
         {172.16.0.0/12}   Null                                 Active   Default    
         {192.168.0.0/16}  Null                                 Active   Default

El VNETLocal es siempre la prefix(es) definido dirección de la red virtual para esa red específica (es decir, cambia de una red virtual a una red virtual, dependiendo de cómo se define cada red virtual específico). Las rutas de sistema restantes son estáticas y predeterminados según se indica en la tabla.

En este ejemplo, se crean dos tablas de enrutamiento, una de las subredes front-end y back-end. Cada tabla se carga con rutas estáticas correspondiente a la subred determinada. En este ejemplo, cada tabla tiene tres rutas que dirigir todo el tráfico (0.0.0.0/0) a través del firewall (salto siguiente = dirección IP de dispositivo Virtual):

1. Para permitir el tráfico de subred local omitir el firewall definidas por el tráfico de subred local con ningún siguiente salto.
2. Tráfico de red virtual con un siguiente salto definido como firewall; Esto invalida la regla predeterminada que permite el tráfico de red virtual local enrutar directamente.
3. Todos los restante tráfico (0/0) con un siguiente salto definido como el firewall.

>[AZURE.TIP] No tiene la entrada de subred local en la UDR romperá comunicaciones de subred local. 
> - En nuestro ejemplo, 10.0.1.0/24 apuntando a VNETLocal es fundamental como salir de paquete en caso contrario, se producirá un error en el servidor Web (10.0.1.4) destinados a otro servidor local 10.0.1.25 (por ejemplo) mientras se enviarán sobre a la NVA, que se envía a la subred, y la subred se vuelva a enviarlo a la NVA y así sucesivamente.
> - Posibilidades de un bucle de enrutamiento son normalmente superiores en dispositivos de múltiples nic que están conectados directamente a cada subred se comunican, que suele ser de tradicional, local, electrodomésticos. 

Una vez que se crean las tablas de enrutamiento, se enlazan a sus subredes. La tabla de enrutamiento de subred front-end, una vez creado y enlazado a la subred tendría un aspecto similar a este:

        Effective routes : 
         Address Prefix    Next hop type    Next hop IP address Status   Source     
         --------------    -------------    ------------------- ------   ------     
         {10.0.1.0/24}     VNETLocal                            Active 
         {10.0.0.0/16}     VirtualAppliance 10.0.0.4            Active    
         {0.0.0.0/0}       VirtualAppliance 10.0.0.4            Active

>[AZURE.NOTE] UDR ahora se puede aplicar a la subred de puerta de enlace en el que está conectado el circuito ExpressRoute.
>
> Ejemplos de cómo habilitar la red de perímetro con ExpressRoute o redes de sitio a sitio se muestran en los ejemplos 3 y 4.


#### <a name="ip-forwarding-description"></a>Descripción de reenvío IP
Reenvío IP es una característica companion UDR. Se trata de una configuración en un dispositivo virtual que le permite recibir tráfico no específicamente dirigido al dispositivo y, a continuación, reenviar que el tráfico a su destino final.

Por ejemplo, si el tráfico de AppVM01 realiza una solicitud al servidor DNS01, UDR ¿redirigir esta el firewall. Con reenvío IP habilitado, el tráfico al destino DNS01 (10.0.2.4) es aceptado por el dispositivo (10.0.0.4) y, a continuación, reenvía a su destino final (10.0.2.4). Sin reenvío IP habilitado en el firewall, el tráfico no debería aceptado por el dispositivo incluso si la tabla de ruta tiene el firewall como el próximo salto. Para usar un dispositivo virtual, es fundamental recordar habilitar el reenvío IP junto con UDR.

#### <a name="nsg-description"></a>Descripción del GSN
En este ejemplo, un grupo GSN está integrado y, a continuación, se cargan con una sola regla. Este grupo se enlaza únicamente a las subredes front-end y back-end (no el SecNet). Mediante declaración se genera la siguiente regla:

- Denegado cualquier (todos los puertos) el tráfico de Internet a la red virtual completa (todas las subredes).

Aunque NSGs se utilizan en este ejemplo, su finalidad principal es como un nivel secundario de defensa frente a errores de configuración manual. El objetivo es bloquear todo el tráfico entrante de Internet a las subredes front-end o back-end. Solo debe flujo de tráfico a través de la subred SecNet al servidor de seguridad (y, a continuación, si corresponde, en las subredes front-end o back-end). Además, con las reglas UDR en su lugar, tráfico que realizó en las subredes front-end o back-end ' d dirigirá fuera del firewall (gracias a UDR). El firewall verá como un flujo asimétrico y desea colocar el tráfico saliente. Por lo tanto, hay tres capas de seguridad proteger las subredes:
- Servicios de nube sin extremos abiertos en el FrontEnd001 y BackEnd001.
- NSGs Denegar tráfico de Internet.
- El tráfico asimétricos colocar de firewall.

Un punto interesante sobre la GSN en este ejemplo es que contiene solo una regla, que es Denegar el tráfico de Internet a toda la red virtual, incluida la subred de seguridad. Sin embargo, dado que la GSN sólo se enlaza a las subredes front-end y back-end, no se procesa la regla en el tráfico de entrada a la subred de seguridad. Como resultado, el tráfico fluirá a la subred de seguridad.

#### <a name="firewall-rules"></a>Reglas de Firewall
En el firewall, deben crear reglas de reenvío. Dado que el firewall está bloqueando o reenvío todo el tráfico entrante, saliente y tráfico de red virtual dentro, son necesarias muchas reglas de firewall. Además, todo el tráfico entrante visitas la dirección IP pública del servicio de seguridad (en distintos puertos), para procesar el firewall. Un procedimiento recomendado es crear diagramas de los flujos de lógicos antes de configurar las subredes y las reglas de firewall para evitar rediseñar más adelante. La figura siguiente es una vista lógica de las reglas de firewall para este ejemplo:
 
![Vista lógica de las reglas de firewall][10]

>[AZURE.NOTE] Según el dispositivo de red Virtual usado, varían los puertos de administración. En este ejemplo, un NextGen Firewall Barracuda se hace referencia, que usa los puertos 22, 801 y 807. Consulte la documentación de proveedor del dispositivo para buscar los puertos exactos que se usa para la administración del dispositivo que se usa.

#### <a name="firewall-rules-description"></a>Descripción de las reglas de Firewall
En el diagrama lógico anterior, no se muestra la subred de seguridad. Esto es porque el firewall es el único recurso de la subred y este diagrama muestra las reglas de firewall, y cómo lógicamente permitir o denegar flujos de tráfico, no la ruta real redirigida. Además, los puertos externos seleccionados para el tráfico RDP son superiores iban puertos (8014 – 8026) y se han seleccionado para algo alinear con los dos últimos octetos de la dirección IP local para mejorar la legibilidad sea más fácil (por ejemplo, dirección del servidor local 10.0.1.4 está asociada con el puerto externo 8014). Cualquier superior no entran en conflicto, sin embargo, pueden utilizarse puertos.

En este ejemplo, debemos siete tipos de reglas:

- Reglas externos (para el tráfico entrante):
  1.    Regla de administración de Firewall: redirigir de aplicación esta regla permite el tráfico pasar a los puertos de administración del dispositivo virtual de red.
  2.    Reglas RDP (por cada servidor de Windows): estos cuatro reglas (uno por cada servidor) permiten la administración de los servidores individuales a través de RDP. También se incluirá en una regla, dependiendo de las capacidades del dispositivo virtual de red que se usa.
  3.    Las reglas de tráfico de aplicación: hay dos de estas reglas, la primera para el tráfico web front-end y la segunda para el tráfico de back-end (por ejemplo, el servidor web a nivel de datos). La configuración de estas reglas depende de la arquitectura de red (donde se colocan los servidores) y flujos de tráfico (qué dirección se usan los flujos de tráfico y los puertos).
      - La primera regla permite el tráfico de la aplicación real llegar al servidor de aplicaciones. Mientras que las demás reglas permiten de seguridad y administración, las reglas de tráfico de aplicación son los que permiten usuarios externos o servicios tener acceso a las aplicaciones. En este ejemplo, hay un único servidor web en el puerto 80. Por lo tanto una regla de aplicación única firewall redirige el tráfico de entrada a la dirección IP externa a la dirección IP de web servidores interna. La sesión de tráfico redirigido ¿traducir a través de NAT al servidor interno.
      - La segunda regla es la regla de back-end para permitir que el servidor web póngase en contacto con el servidor de AppVM01 (pero no AppVM02) a través de cualquier puerto.
- Reglas internas (para el tráfico de red virtual internos)
  4.    Salida de regla de Internet: esta regla permite el tráfico de cualquier red para pasar a las redes seleccionadas. Esta regla suele ser una regla predeterminada ya en el firewall, pero en un estado deshabilitado. Esta regla debe estar habilitada para este ejemplo.
  5.    Regla DNS: esta regla permite sólo el tráfico DNS (puerto 53) pasar al servidor DNS. Para este entorno bloquea el tráfico de la mayoría de front-end al back-end. Esta regla permite específicamente DNS desde cualquier subred local.
  6.    Subred regla subred: esta regla es permitir que cualquier servidor en la subred back-end para conectarse a cualquier servidor en la subred front-end (pero no a la inversa).
- Regla a prueba de errores (para el tráfico que no se ajusten a ninguno de los anteriores):
  7.    Denegar todas las reglas de tráfico: siempre debe ser la regla final (en términos de prioridad) y, a continuación, como tales si un flujo de tráfico no coincide con alguno de los últimos reglas se quitarán por esta regla. Se trata de una regla predeterminada y normalmente activado. Por lo general no se necesitan modificaciones.

>[AZURE.TIP] En la segunda regla de tráfico de aplicación, para simplificar el ejemplo, se permite cualquier puerto. En un escenario real, el puerto más específico y los intervalos de direcciones deben usarse para reducir la superficie de ataque de esta regla.

Una vez que se crean todas las reglas anteriores, es importante revisar la prioridad de cada regla para asegurarse de tráfico se permitido o denegado como desee. En este ejemplo, las reglas están en orden de prioridad.

#### <a name="conclusion"></a>Conclusión
Se trata de una forma más compleja pero más completa de proteger y aislar la red que los ejemplos anteriores. (Ejemplo 2 protege simplemente la aplicación y ejemplo 1 solo aísla subredes). Este diseño permite para supervisar el tráfico en ambas direcciones y protege no solo en el servidor de aplicaciones entrantes pero aplica la directiva de seguridad de red para todos los servidores de esta red. Además, según el dispositivo que utiliza, auditoría de tráfico completo y concienciación pueden conseguirse. Para obtener más información, consulte las [instrucciones de generación detallado][Example3]. Estas instrucciones incluyen:

- Cómo crear esta red de perímetro de ejemplo con las secuencias de comandos de PowerShell.
- Cómo crear este ejemplo con una plantilla de administrador de recursos de Azure.
- Descripciones detalladas de cada UDR, GSN comando y regla de firewall.
- Escenarios de flujo de tráfico detallada, que muestra cómo el tráfico está permitido o denegado en cada capa.

### <a name="example-4-add-a-hybrid-connection-with-a-site-to-site-virtual-appliance-virtual-private-network-vpn"></a>Ejemplo 4: Agregar una conexión de híbrido con una red privada virtual (VPN) de dispositivo virtual de sitio a otro
[Volver a Inicio rápido](#fast-start) | Instrucciones de compilación disponible de detalladas pronto

![Híbrido de red conectados a la red perimetral con NVA][11]

#### <a name="environment-description"></a>Descripción del entorno
Redes híbrido con un dispositivo de red virtual (NVA) se pueden agregar a cualquiera de los tipos de red perimetral descritos en los ejemplos 1, 2 o 3.

Como se muestra en la figura anterior, una conexión VPN a través de Internet (sitio a sitio) se usa para conectarse a una red local a una red virtual Azure a través de un NVA.

>[AZURE.NOTE] Si usa ExpressRoute con la opción interconexión pública de Azure habilitada, debe crear una ruta estática. Esto se debe redirigir a la dirección IP de VPN NVA su Internet corporativo y no a través de la WAN ExpressRoute. El NAT requerido la opción interconexión pública de Azure ExpressRoute puede interrumpir la sesión de VPN.

Una vez la VPN, el NVA se convierte en el hub central para todas las redes y subredes. Las reglas de reenvío de firewall determinan qué flujos de tráfico permitidos, se convierten a través de NAT, se redirigen o se eliminan (incluso para los flujos de tráfico entre la red local y Azure).

Flujos de tráfico se deben considerar con cuidado, como se puede optimizar o degradado por este patrón de diseño, función del caso de uso.

Usar el entorno creado en el ejemplo 3 y, a continuación, agregar una conexión de red de sitio a sitio VPN híbrido, genera el siguiente diseño:

![Red perimetral con NVA conectado con una VPN de sitio a otro][12]

El enrutador local o cualquier otro dispositivo de red que sea compatible con su NVA para VPN, sería el cliente VPN. Este dispositivo físico sería responsable de iniciar y mantener la conexión VPN con su NVA.

Lógicamente a la NVA, la red es similar a independiente cuatro "zonas de seguridad de" con las reglas en la NVA ser el director principal del tráfico entre estas zonas:

![Red lógica desde la perspectiva NVA][13]

#### <a name="conclusion"></a>Conclusión
La adición de una conexión de red de sitio a sitio VPN híbrido a una red virtual Azure puede ampliar la red local en Azure de forma segura. Usar una conexión VPN, el tráfico se cifra y redirige a través de Internet. El NVA en este ejemplo proporciona una ubicación central para exigir y administrar la directiva de seguridad. Para obtener más información, consulte las instrucciones de generación detallado (próximamente). Estas instrucciones incluyen:

- Cómo crear esta red de perímetro de ejemplo con las secuencias de comandos de PowerShell.
- Cómo crear este ejemplo con una plantilla de administrador de recursos de Azure.
- Escenarios de flujo de tráfico detallada, que muestra cómo fluye el tráfico a través de este diseño.

### <a name="example-5-add-a-hybrid-connection-with-a-site-to-site-azure-gateway-vpn"></a>Ejemplo 5: Agregar una conexión de híbrido con una puerta de enlace a sitios, Azure VPN
[Volver a Inicio rápido](#fast-start) | Instrucciones de compilación disponible de detalladas pronto

![Red perimetral con red conectada híbrido de puerta de enlace][14]

#### <a name="environment-description"></a>Descripción del entorno
Redes híbrido con una puerta de enlace VPN de Azure pueden agregarse a cualquier tipo de red de perímetro descrito en los ejemplos 1 o 2.

Como se muestra en la figura anterior, una conexión VPN a través de Internet (sitio a sitio) se usa para conectarse a una red local a una red virtual Azure a través de una puerta de enlace VPN de Azure.

>[AZURE.NOTE] Si usa ExpressRoute con la opción interconexión pública de Azure habilitada, debe crear una ruta estática. Esto se debe redirigir a la dirección IP de VPN NVA su Internet corporativo y no a través de la WAN ExpressRoute. El NAT requerido la opción interconexión pública de Azure ExpressRoute puede interrumpir la sesión de VPN.

La figura siguiente muestra los dos bordes de red en esta opción. En el borde de la primera, la NVA y NSGs controlar flujos de tráfico de redes Azure dentro y entre Azure e Internet. La segunda arista es la puerta de enlace VPN de Azure, que es un borde de la red completamente independiente y aislado entre Azure y local.

Flujos de tráfico se deben considerar con cuidado, como se puede optimizar o degradado por este patrón de diseño, función del caso de uso.

Usando el entorno creado en el ejemplo 1 y, a continuación, agregar una conexión de red de sitio a sitio VPN híbrido, genera el siguiente diseño:

![Red perimetral con la puerta de enlace mediante una conexión de ExpressRoute][15]

#### <a name="conclusion"></a>Conclusión
La adición de una conexión de red de sitio a sitio VPN híbrido a una red virtual Azure puede ampliar la red local en Azure de forma segura. Mediante la puerta de enlace VPN de Azure nativa, el tráfico es IPSec cifrado y redirige a través de Internet. Además, mediante la puerta de enlace de Azure VPN puede proporcionar una opción de coste inferior (ninguna licencia adicional de costo como con NVAs de terceros). Esto es más económico en el ejemplo 1, donde no se usa NVA. Para obtener más información, consulte las instrucciones de generación detallado (próximamente). Estas instrucciones incluyen:

- Cómo crear esta red de perímetro de ejemplo con las secuencias de comandos de PowerShell.
- Cómo crear este ejemplo con una plantilla de administrador de recursos de Azure.
- Escenarios de flujo de tráfico detallada, que muestra cómo fluye el tráfico a través de este diseño.

### <a name="example-6-add-a-hybrid-connection-with-expressroute"></a>Ejemplo 6: Agregar una conexión de híbrido con ExpressRoute
[Volver a Inicio rápido](#fast-start) | Instrucciones de compilación disponible de detalladas pronto

![Red perimetral con red conectada híbrido de puerta de enlace][16]

#### <a name="environment-description"></a>Descripción del entorno
Redes híbrida mediante una conexión de interconexión privada ExpressRoute se pueden agregar a cualquier tipo de red de perímetro descrito en los ejemplos 1 o 2.

Como se muestra en la figura anterior, interconexión privada de ExpressRoute proporciona una conexión directa entre su red local y la red virtual Azure. Tráfico transmitidos por solo la red del proveedor de servicio y la red de Microsoft Azure, nunca toque Internet.

>[AZURE.NOTE] Hay algunas restricciones con UDR ExpressRoute, debido a la complejidad del enrutamiento dinámico utilizados en la puerta de enlace virtual Azure. Estos son los siguientes:
>
>- UDR no se pueden aplicar a la subred de puerta de enlace en el que está conectada la ExpressRoute vinculado Azure virtual puerta de enlace.
>- La ExpressRoute vinculado Azure virtual puerta de enlace no puede ser que el dispositivo siguiente salto para otro UDR enlazado subredes.
>
>
<br />

>[AZURE.TIP] Usar ExpressRoute mantiene el tráfico de red corporativa quitarlas Internet para una mejor seguridad y significativamente mejor rendimiento. También permite de contratos de nivel de servicio de su proveedor de ExpressRoute. La puerta de enlace de Azure puede pasar hasta 2 Gb/s con ExpressRoute, mientras que con VPN de sitio a otro, el rendimiento de puerta de enlace de Azure máximo es 200 Mb/s.

Tal como se muestra en el siguiente diagrama, con esta opción el entorno ahora tiene dos bordes de red. La NVA y GSN controlan flujos de tráfico de redes Azure dentro y entre Azure e Internet, mientras que la puerta de enlace es un borde de la red completamente independiente y aislado entre Azure y local.

Flujos de tráfico se deben considerar con cuidado, como se puede optimizar o degradado por este patrón de diseño, función del caso de uso.

Usar el entorno creado en el ejemplo 1 y, a continuación, agregar una conexión de red ExpressRoute híbrido, genera el siguiente diseño:

![Red perimetral con la puerta de enlace mediante una conexión de ExpressRoute][17]

#### <a name="conclusion"></a>Conclusión
La adición de una conexión de red privada ExpressRoute Peering puede ampliar la red local en Azure en un nivel de latencia seguro, inferior, superior realización de manera. Además, uso de la puerta de enlace nativo de Azure, como en este ejemplo, proporciona una opción de costo inferior (no adicional licencias como con NVAs de terceros). Para obtener más información, consulte las instrucciones de generación detallado (próximamente). Estas instrucciones incluyen:

- Cómo crear esta red de perímetro de ejemplo con las secuencias de comandos de PowerShell.
- Cómo crear este ejemplo con una plantilla de administrador de recursos de Azure.
- Escenarios de flujo de tráfico detallada, que muestra cómo fluye el tráfico a través de este diseño.

## <a name="references"></a>Referencias
### <a name="helpful-websites-and-documentation"></a>Documentación y sitios Web útiles
- Access Azure con el Administrador de recursos Azure:
- Obtener acceso a Azure con PowerShell: [https://azure.microsoft.com/documentation/articles/powershell-install-configure/](./powershell-install-configure.md)
- Documentación de red virtual: [https://azure.microsoft.com/documentation/services/virtual-network/](https://azure.microsoft.com/documentation/services/virtual-network/)
- Documentación de grupo de seguridad de red: [https://azure.microsoft.com/documentation/articles/virtual-networks-nsg/](./virtual-network/virtual-networks-nsg.md)
- Documentación de enrutamiento definidas por el usuario: [https://azure.microsoft.com/documentation/articles/virtual-networks-udr-overview/](./virtual-network/virtual-networks-udr-overview.md)
- Las puertas de enlace virtuales Azure: [https://azure.microsoft.com/documentation/services/vpn-gateway/](https://azure.microsoft.com/documentation/services/vpn-gateway/)
- [Https://azure.microsoft.com/documentation/articles/vpn-gateway-create-site-to-site-rm-powershell](./vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md) VPN de sitio a sitio:
- Documentación de ExpressRoute (asegúrese de consultar las secciones "Introducción" y "cómo"): [https://azure.microsoft.com/documentation/services/expressroute/](https://azure.microsoft.com/documentation/services/expressroute/)

<!--Image References-->
[0]: ./media/best-practices-network-security/flowchart.png "Diagrama de flujo de opciones de seguridad"
[1]: ./media/best-practices-network-security/compliancebadges.png "Teclas de cumplimiento de Azure"
[2]: ./media/best-practices-network-security/azuresecurityfeatures.png "Características de seguridad de Azure"
[3]: ./media/best-practices-network-security/dmzcorporate.png "Una DMZ en una red corporativa"
[4]: ./media/best-practices-network-security/azuresecurityarchitecture.png "Arquitectura de seguridad de Azure"
[5]: ./media/best-practices-network-security/dmzazure.png "Una DMZ en una red Virtual de Azure"
[6]: ./media/best-practices-network-security/dmzhybrid.png "Red híbrido con tres límites de seguridad"
[7]: ./media/best-practices-network-security/example1design.png "Entrada DMZ con GSN"
[8]: ./media/best-practices-network-security/example2design.png "Entrada DMZ con NVA y GSN"
[9]: ./media/best-practices-network-security/example3design.png "DMZ bidireccional con NVA, NSG y UDR"
[10]: ./media/best-practices-network-security/example3firewalllogical.png "Vista lógica de las reglas de Firewall"
[11]: ./media/best-practices-network-security/example4designoptions.png "Híbrido de red conectados a DMZ con NVA"
[12]: ./media/best-practices-network-security/example4designs2s.png "DMZ con NVA conectado con una VPN de sitio a otro"
[13]: ./media/best-practices-network-security/example4networklogical.png "Red lógica desde la perspectiva NVA"
[14]: ./media/best-practices-network-security/example5designoptions.png "Híbrido de sitio para el sitio de red conectados a DMZ con la puerta de enlace de Azure"
[15]: ./media/best-practices-network-security/example5designs2s.png "DMZ con Azure puerta de enlace con VPN de sitio a sitio"
[16]: ./media/best-practices-network-security/example6designoptions.png "ExpressRoute híbrido de red conectados a DMZ con la puerta de enlace de Azure"
[17]: ./media/best-practices-network-security/example6designexpressroute.png "DMZ con Azure puerta de enlace con una conexión de ExpressRoute"

<!--Link References-->
[Example1]: ./virtual-network/virtual-networks-dmz-nsg-asm.md
[Example2]: ./virtual-network/virtual-networks-dmz-nsg-fw-asm.md
[Example3]: ./virtual-network/virtual-networks-dmz-nsg-fw-udr-asm.md
[Example4]: ./virtual-network/virtual-networks-hybrid-s2s-nva-asm.md
[Example5]: ./virtual-network/virtual-networks-hybrid-s2s-agw-asm.md
[Example6]: ./virtual-network/virtual-networks-hybrid-expressroute-asm.md
[Example7]: ./virtual-network/virtual-networks-vnet2vnet-direct-asm.md
[Example8]: ./virtual-network/virtual-networks-vnet2vnet-transit-asm.md
