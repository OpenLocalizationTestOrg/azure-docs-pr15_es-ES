<properties
   pageTitle="Introducción a la seguridad de Microsoft Azure | Microsoft Azure"
   description="En este artículo se proporciona información general sobre las capacidades de seguridad de Microsoft Azure y consideraciones generales para las organizaciones que va a migrar sus activos a un proveedor de servicios de nube."
   services="security"
   documentationCenter="na"
   authors="YuriDio"
   manager="swadhwa"
   editor="TomSh"/>

<tags
   ms.service="security"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="05/19/2016"
   ms.author="yuridio"/>

#<a name="getting-started-with-microsoft-azure-security"></a>Introducción a la seguridad de Microsoft Azure

Cuando se crea o migración los activos de TI a un proveedor de servicios de nube, se basa en capacidades de la organización a proteger las aplicaciones y los datos confiar a sus servicios y a los controles de seguridad que se proporcionan para controlar la seguridad de los activos en la nube.

Infraestructura de Azure está diseñada de la función para aplicaciones de hospedaje millones de clientes simultáneamente y a continuación, proporcionar una base de confianza en el que las empresas pueden satisfacer sus necesidades de seguridad. Además, Azure le ofrece una amplia variedad de opciones de seguridad configurable y la capacidad de controlar ellos para que puede personalizar la seguridad para satisfacer los requisitos únicos para las implementaciones.

En este artículo de información general sobre la seguridad de Azure, veremos:

-   Servicios de Azure y características que puede utilizar para ayudar a proteger sus servicios y datos dentro de Azure

-   ¿Cómo Microsoft protege la infraestructura de Azure para ayudar a proteger sus datos y aplicaciones

##<a name="identity-and-access-management"></a>Administración de identidades y acceso


Controlar el acceso a la infraestructura de TI, datos y aplicaciones es fundamental. En Microsoft Azure, estas funciones se entregan mediante servicios como Azure Active Directory, el almacenamiento de Azure y soporte técnico para numerosos estándares y las API.

[Azure Active Directory](../active-directory/active-directory-whatis.md) (Azure AD) es un repositorio de identidad y el motor que proporciona autenticación, autorización y control de acceso de usuarios, grupos y objetos de una organización. Azure AD también ofrece a los desarrolladores integrar la administración de identidades en las aplicaciones de forma eficaz. Protocolos estándar como [SAML 2.0](https://en.wikipedia.org/wiki/SAML_2.0), [WS-Federation](https://msdn.microsoft.com/library/bb498017.aspx)y [Conectar OpenID](http://openid.net/connect/) hace posible el inicio de sesión en una amplia variedad de plataformas como. NET, Java, Node.js y PHP.

La API de gráfico basada en el resto permite a los desarrolladores de lectura y escritura en el directorio desde cualquier plataforma. A través de soporte para [OAuth 2.0](http://oauth.net/2/), los desarrolladores pueden crear móviles y aplicaciones web que se integran con Microsoft y otros fabricantes web API y crear los suyos propios secure web API. Bibliotecas de cliente de Abrir origen están disponibles para .net, de la tienda Windows, iOS y Android con bibliotecas adicionales en desarrollo.

### <a name="how-azure-enables-identity-and-access-management"></a>¿Cómo Azure permite la administración de identidades y acceso

Azure AD puede usarse como un directorio de nube independiente para su organización o como una solución integrada con su local de Active Directory existente. Algunas características de integración incluyen sincronización de directorios e inicio de sesión único (SSO). Estos ampliación el alcance de su identidad local existente en la nube y mejoran la experiencia de administración y usuario final.

Se incluyen algunas otras funciones para la administración de identidades y acceso:

-   Azure AD permite [SSO](https://azure.microsoft.com/documentation/videos/overview-of-single-sign-on/) para aplicaciones de SaaS, independientemente de dónde se hospedan. Algunas aplicaciones se federación con Azure AD y otras personas usar contraseña SSO. También pueden admitir aplicaciones federadas aprovisionamiento de usuario y la reasignación de contraseña.

-   Acceso a datos en [El almacenamiento de Azure](https://azure.microsoft.com/services/storage/) está controlado mediante la autenticación. Cada cuenta de almacenamiento tiene una clave principal ([Clave de cuenta de almacenamiento](https://msdn.microsoft.com/library/azure/ee460785.aspx)o SAK) y clave secreta secundaria (la firma de acceso compartido o SA).

-   Azure AD proporciona identidad como un servicio a través de federación (con [Los servicios de federación de Active Directory](../active-directory/fundamentals-identity.md), la sincronización y la replicación de directorios local.

-   [Autenticación multifactor de Azure (AMF)](../multi-factor-authentication/multi-factor-authentication.md) es el servicio de autenticación de varios factores que requiere que los usuarios también comprobar inicios de sesión con una aplicación móvil, llamada de teléfono o mensaje de texto. Está disponible para su uso con Azure AD, para proteger los recursos locales con el servidor de Azure AMF y con aplicaciones personalizadas y mediante el SDK de directorios.

-   [Servicios de dominio de Active Directory de Azure](https://azure.microsoft.com/services/active-directory-ds/) le permite unir máquinas virtuales de Windows Azure a un dominio sin necesidad de implementar controladores de dominio. Usuarios pueden iniciar sesión en estas máquinas virtuales con sus credenciales de Active Directory corporativos y administrar máquinas virtuales de dominio conjunto de directivas de grupo para exigir la seguridad básica en todos los equipos virtuales Azure.

-   [Azure Active Directory B2C](https://azure.microsoft.com/services/active-directory-b2c/) proporciona un servicio de administración de identidad global, altamente disponible para las aplicaciones para consumidores que escala a cientos de millones de identidades. Se puede integrar a través de mobile y web plataformas. Los consumidores pueden iniciar sesión en todas las aplicaciones a través de experiencias personalizables usando sus cuentas sociales existentes o crear nuevas credenciales.

##<a name="data-access-control-and-encryption"></a>Control de acceso de datos y cifrado

Microsoft utiliza los principios de separación de derechos y [Privilegios mínimos](https://en.wikipedia.org/wiki/Principle_of_least_privilege) a lo largo de las operaciones de Azure. Acceso a datos por personal de soporte técnico de Azure requiere su permiso explícito y se concede de forma "just in time" que se registra y auditoría, a continuación, revoca después de completar el compromiso.

Además, Azure proporciona varias funciones para proteger los datos en tránsito y almacenados, incluido el cifrado de datos, archivos, aplicaciones, servicios, comunicaciones y unidades. Tiene la opción para cifrar la información antes de colocar en Azure, así como el almacenamiento de claves en los centros de datos local.

![Microsoft Antimalware en Azure](./media/azure-security-getting-started/sec-azgsfig1.PNG)

### <a name="azure-encryption-technologies"></a>Tecnologías de cifrado de Azure

Puede reunir datos en acceso administrativo a su entorno de suscripción mediante el uso de [informes de Azure AD](../active-directory/active-directory-reporting-audit-events.md). Tiene la opción para configurar el [Cifrado de unidad BitLocker](https://technet.microsoft.com/library/cc732774.aspx) en VHD que contiene información confidencial de Azure.

Otras funciones de Azure que le ayudará a proteger los datos incluyen:

-   Los desarrolladores de aplicaciones pueden generar cifrado en las aplicaciones que se despliegan en Azure con [CryptoAPI](https://msdn.microsoft.com/library/ms867086.aspx) de Windows y .NET Framework.

- Cifrado de cliente para el almacenamiento de blobs de Microsoft le permiten controlar completamente las teclas.  El servicio de almacenamiento nunca ve las teclas y no puede descifrar los datos.

-   [Azure RMS](https://technet.microsoft.com/library/jj585026.aspx) (con el [SDK de RMS](https://msdn.microsoft.com/library/dn758244.aspx) proporciona cifrado de nivel de datos y archivos y prevención de pérdida de datos a través de administración de acceso basado en la directiva.

-   Azure admite el [cifrado de tabla y columna nivel (TDE/borrar)](http://blogs.msdn.com/b/sqlsecurity/archive/2015/05/12/recommendations-for-using-cell-level-encryption-in-azure-sql-database.aspx) en máquinas virtuales de SQL Server y terceros es compatible con los servidores de administración de claves en centros de datos de los clientes locales.

-   Teclas de la cuenta de almacenamiento, firmas de acceso compartido, certificados de administración y otras teclas son únicas para cada inquilino Azure.

-   Almacenamiento de Azure [StorSimple](http://www.microsoft.com/server-cloud/products/storsimple/overview.aspx) híbrido cifra los datos a través de un 128 bits público / privado par de claves antes de cargarlo con el almacenamiento de Azure.

-   Azure admite y utiliza numerosos mecanismos de cifrado, como SSL/TLS, IPsec y AES, dependiendo de los tipos de datos y contenedores y transportes.

##<a name="virtualization"></a>Virtualización

La plataforma de Windows Azure usa un entorno virtualizado. Instancias de usuario funcionan como máquinas virtuales de independiente que no tienen acceso a un servidor de host físico y este aislamiento se aplica utilizando físico [niveles de procesador privilegio (anillo 0/anillo 3)](https://en.wikipedia.org/wiki/Protection_ring).

Anillo 0 es más con privilegios y 3 el menos. El sistema operativo invitado se ejecuta en un 1 anillo de menor privilegio y aplicaciones en el último con privilegios anillo 3. Esta virtualización de recursos físicos lleva a una separación clara entre invitado OS e hipervisor de separación de seguridad adicional entre los dos.

Hipervisor de Azure actúa como un núcleo de micro y pasa todas las solicitudes de acceso de hardware de invitadas al host para el procesamiento mediante una interfaz de memoria compartida denominada VMBus. Esto impide que los usuarios obtengan acceso de lectura/escritura/ejecución sin formato al sistema y reduce el riesgo de uso compartido de recursos del sistema.

![Microsoft Antimalware en Azure](./media/azure-security-getting-started/sec-azgsfig2.PNG)

### <a name="how-azure-implements-virtualization"></a>¿Cómo Azure implementa virtualización

Azure usa un firewall de hipervisor (filtro de paquetes), que se ha implementado en el hipervisor y configura un agente de controlador de tela. Esto ayuda a proteger los inquilinos de acceso no autorizado. De forma predeterminada, cuando se crea una máquina virtual, se bloquea todo el tráfico y, a continuación, el agente de controlador de tela configura el filtro de paquetes para agregar *reglas y excepciones* para permitir el tráfico autorizado.

Existen dos categorías de reglas que se programan a continuación:

-   **Configuración de equipo o las reglas de infraestructura**: de forma predeterminada, todas las comunicaciones se bloquean. Existen excepciones para permitir una máquina virtual para enviar y recibir tráfico DHCP y DNS. Máquinas virtuales también pueden enviar tráfico a "" internet y enviar tráfico a otras en el clúster y el servidor de activación de sistema operativo. Las VM permite la lista de destinos salientes no incluye subredes enrutador Azure, back-end de administración de Azure y otras propiedades de Microsoft.

-   **Archivo de configuración de rol**: define las ACL entrantes basadas en modelo de servicio de los inquilinos. Por ejemplo, si un inquilino tiene un servidor Web en el puerto 80 en una máquina virtual determinada, a continuación, Azure abre el puerto TCP 80 a todas las direcciones IP si está configurando un extremo en el modelo de [Administración de servicios de Azure](../resource-manager-deployment-model.md) . Si la máquina virtual tiene un rol de back-end o de trabajo en ejecución, se abre el rol de trabajo solo a la máquina virtual con el mismo inquilino.

##<a name="isolation"></a>Aislamiento

Mantenimiento de separación para evitar la transferencia no autorizada y no intencionada de información entre implementaciones en una arquitectura de múltiples arrendatario compartida es otro requisito de seguridad de la nube importantes.

Azure implementa [el control de acceso de red](https://azure.microsoft.com/blog/network-isolation-options-for-machines-in-windows-azure-virtual-networks/) y separación a través de aislamiento de VLAN, ACL, cargan equilibradores y filtros IP. Externo transmisiones entrantes para su máquinas virtuales está restringido a puertos y protocolos que defina. El filtrado de red se ha implementado para evitar que el tráfico simulado y restringe el tráfico entrante y saliente a componentes de plataforma de confianza. Directivas de flujo del tráfico se implementan en los dispositivos de protección de borde que denegación el tráfico de forma predeterminada.

![Microsoft Antimalware en Azure](./media/azure-security-getting-started/sec-azgsfig3.PNG)

Traducción de direcciones de red (NAT) se usa para separar el tráfico de red interna de tráfico externo. Tráfico interno no es externamente enrutable. Las [direcciones IP virtuales](http://blogs.msdn.com/b/cloud_solution_architect/archive/2014/11/08/vips-dips-and-pips-in-microsoft-azure.aspx) externamente enrutables se convierten en las direcciones [IP de dinámica interno](http://blogs.msdn.com/b/cloud_solution_architect/archive/2014/11/08/vips-dips-and-pips-in-microsoft-azure.aspx) que solo están enrutables dentro de Azure.

El tráfico externo a máquinas virtuales de Windows Azure pasa a través de listas de Control de acceso (ACL) en enrutadores, equilibradores de carga y conmutadores de nivel 3. Se permiten determinados protocolos conocidos. ACL son para limitar el tráfico origina invitadas a otras VLAN que se utiliza para la administración. Además, se filtra el tráfico a través de filtros IP en el sistema operativo host, limitar aún más el tráfico en ambas capas de red y vínculo de datos.

### <a name="how-azure-implements-isolation"></a>¿Cómo Azure implementa aislamiento

El controlador de tela Azure es responsable de la asignación de recursos de infraestructura para cargas de trabajo de inquilinos y administra las comunicaciones unidireccionales desde el host de máquinas virtuales. El hipervisor Azure aplica la memoria y el proceso de separación entre máquinas virtuales y segura rutas de tráfico de red a inquilinos de sistema operativo de invitado. Azure también implementa aislamiento de inquilinos, almacenamiento y redes virtuales:

-   Cada inquilino de Azure AD es lógicamente aislado con límites de seguridad.

-   Cuentas de Azure almacenamiento son exclusivas de cada suscripción y acceso se debe autenticar con una clave de cuenta de almacenamiento.

-   Redes virtuales son lógicamente aisladas mediante una combinación de direcciones IP privadas únicas, firewalls y ACL de IP. Carga equilibradores enrutar el tráfico a los inquilinos adecuados basados en definiciones de extremo.

##<a name="virtual-network-and-firewall"></a>Red virtual y el firewall

Las [redes distribuidas y virtuales](http://download.microsoft.com/download/4/3/9/43902EC9-410E-4875-8800-0788BE146A3D/Windows%20Azure%20Network%20Security%20Whitepaper%20-%20FINAL.docx) en Azure ayudan a garantizar que el tráfico de red privada es lógicamente aislado de tráfico de otras redes Virtual de Azure.

![Microsoft Antimalware en Azure](./media/azure-security-getting-started/sec-azgsfig4.PNG)

La suscripción puede contener varias redes privadas aisladas (e incluir firewall, el equilibrio de carga y la traducción de direcciones de red).

Azure ofrece tres niveles principales de separación de la red de cada clúster Azure dividir lógicamente el tráfico. [Redes de área Local virtual](https://azure.microsoft.com/services/virtual-network/) (VLAN) se utilizan para separar el tráfico de cliente desde el resto de la red de Azure. Acceso a la red de Azure desde fuera del clúster está restringido a través de equilibradores de carga.

Tráfico de red de máquinas virtuales debe pasar por el modificador virtual de hipervisor. El componente de filtro IP en el sistema operativo de raíz aísla la VM raíz de la invitadas y la invitadas entre sí. Realiza el filtrado de tráfico para restringir la comunicación entre los nodos del inquilino y pública Internet (basado en la configuración del servicio del cliente), separar a ellos desde otros inquilinos.

El filtro IP impide invitadas desde:

- Generar tráfico simulado

- Recibir tráfico no dirigido a ellas

- Dirigir el tráfico a extremos de infraestructura protegidos

- Enviar o recibir tráfico de difusión inadecuado

Puede colocar sus máquinas virtuales en [Redes virtuales de Azure](https://azure.microsoft.com/documentation/services/virtual-network/). Estas redes virtuales son similares a las redes se configura en entornos locales, donde están normalmente asociados con un modificador virtual. Equipos virtuales conectados a la misma red Virtual de Azure puede comunicarse con otros sin ninguna configuración adicional. También tiene la opción para configurar subredes diferentes dentro de la red Virtual de Azure.

Puede usar las siguientes tecnologías de red Virtual de Azure para ayudar a comunicaciones seguras en su red Virtual de Azure:

-   [**Grupos de seguridad de la red (GSN)**](../virtual-network/virtual-networks-nsg.md). Puede usar un GSN para controlar el tráfico a una o varias instancias de máquina virtual (VM) en su red virtual. Un GSN contiene reglas de control de acceso permitan o denegación el tráfico en función de la dirección del tráfico, protocolo, dirección de origen y puerto y dirección de destino y puerto.

-   [**Enrutamiento definidas por el usuario**](../virtual-network/virtual-networks-udr-overview.md). Puede controlar el enrutamiento de paquetes a través de un dispositivo virtual mediante la creación de rutas definidas por el usuario que especifican el próximo salto para los paquetes que fluye a una subred concreta para ir un dispositivo de seguridad de una red virtual.

-   [**Reenvío de IP**](../virtual-network/virtual-networks-udr-overview.md). Un dispositivo de seguridad de una red virtual debe poder recibir el tráfico entrante que no se dirige a sí mismo. Para permitir que una máquina virtual para recibir tráfico dirigido a otros destinos, habilitar reenvío IP para la máquina virtual.

-   [**Se obliga túnel**](../vpn-gateway/vpn-gateway-about-forced-tunneling.md). Forzado túnel permite redirigir o todo el tráfico de Internet enlazado generado por sus máquinas virtuales en un Virtual de Azure "forzar" volver a su ubicación local a través de un túnel VPN de sitio a sitio de inspección y auditoría

-   [ACL de **extremo** ](../virtual-machines/virtual-machines-windows-classic-setup-endpoints.md). Puede controlar qué equipos permitidos las conexiones entrantes de Internet a una máquina virtual en su red Virtual de Azure definiendo ACL de extremo.

-   [**Las soluciones de seguridad de red asociados**](https://azure.microsoft.com/marketplace/). Hay un número de solución de seguridad de red de partner que puede tener acceso de Azure Marketplace.

### <a name="how-azure-implements-virtual-networks-and-firewall"></a>¿Cómo implementa Azure firewall y redes virtuales

Azure implementa firewalls de filtrado de paquetes en todos los host e invitado máquinas virtuales de forma predeterminada. Imágenes de sistema operativo Windows desde la Galería de Azure también tienen Firewall de Windows habilitado de forma predeterminada. Equilibradores de carga en el perímetro de público hacia redes controlar las comunicaciones del Azure que se basa en las ACL de IP administradas por los administradores del cliente.

Si Azure mueve los datos del cliente como parte de las operaciones normales o durante un desastre, lo hace mediante canales de comunicaciones cifrado privadas. Otras funciones de Azure para usar en firewall y redes virtuales son:

-   **Nativo Firewall de Host**: tela Azure y almacenamiento ejecutarán en un sistema operativo nativo que no tiene hipervisor y, por tanto, el firewall de windows está configurado con la anteriores dos conjuntos de reglas. Almacenamiento ejecuta nativo para optimizar el rendimiento.

-   **Firewall de host**: el firewall de host es proteger el sistema operativo que se ejecuta el hipervisor. Las reglas se programan para permitir que el controlador de tela y saltar cuadros para comunicarse con el host OS en un puerto específico. Las otras excepciones son para permitir la respuesta DHCP y respuestas de DNS. Reglas del firewall de Azure usa una configuración de equipo que tiene la plantilla de archivo para el sistema operativo host. El propio host está protegido de ataques externos por un firewall de Windows configurado para permitir únicamente la comunicación de orígenes conocidos, autenticados.

-   **Firewall de invitado**: aplica las reglas en el filtro de paquetes de conmutador VM pero programada en otro software (es decir, el fragmento de Firewall de Windows del sistema operativo invitado). El firewall VM invitado puede estar configurado para restringir las comunicaciones de los Invitados VM, incluso si la comunicación está permitida por configuraciones en el host de filtro de IP. Por ejemplo, puede usar el firewall VM invitado para restringir la comunicación entre dos de los VNets que se han configurado para conectar entre sí.

-   **Almacenamiento de Firewall (FW)**: el firewall en el almacenamiento de front-end filtra el tráfico sólo en los puertos 80 y 443 y otros utilidad es necesario. El firewall en el almacenamiento back-end restringe comunicaciones para solo proceden de servidores front-end de almacenamiento.

-   **La puerta de enlace de red virtual**: [Puertas de enlace de red Virtual Azure](../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md) servir como la cruz locales las cargas de trabajo en una red Virtual Azure a conectarse a los sitios local en puertas de enlace. Es necesario para conectarse a en los sitios de local a través de [túneles VPN de sitio a sitio IPsec](../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md)o circuitos [ExpressRoute](../expressroute/expressroute-introduction.md) . Túneles VPN IPsec/IKE, las puertas de enlace realizan protocolos de enlace de IKE y establecer los túneles IPsec S2S VPN entre las redes virtuales y en los sitios local. Las puertas de enlace de red virtual también terminar [VPN de sitio a punto](../vpn-gateway/vpn-gateway-point-to-site-create.md).

##<a name="secure-remote-access"></a>Acceso remoto seguro

Los datos almacenados en la nube deben tener suficiente seguridad habilitado para evitar ataques y mantener la confidencialidad y la integridad mientras en tránsito. Esto incluye controles de red concuerda con basada en directivas, auditables acceso e identidad mecanismos de administración de una organización.

Tecnología cifrada integrada le permite cifrar las comunicaciones dentro y entre implementaciones, entre regiones Azure y de Azure a centros de datos local. Acceso de administrador a máquinas virtuales a través de [sesiones de escritorio remoto](../virtual-machines/virtual-machines-windows-classic-connect-logon.md), [Remoto Windows PowerShell](http://blogs.technet.com/b/heyscriptingguy/archive/2013/09/07/weekend-scripter-remoting-the-cloud-with-windows-azure-and-powershell.aspx)y el Portal de administración de Azure siempre está cifrado.

Para ampliar de forma segura su centro de datos local en la nube, Azure proporciona [VPN de sitio a sitio](../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md) y [VPN punto al sitio](../vpn-gateway/vpn-gateway-point-to-site-create.md), así como vínculos dedicados con [ExpressRoute](../expressroute/expressroute-introduction.md) (conexiones a redes Virtual de Azure sobre VPN están cifradas).

### <a name="how-azure-implements-secure-remote-access"></a>¿Cómo Azure implementa acceso remoto seguro

Siempre se deben autenticar conexiones portal de Azure y requieren SSL/TLS. Puede configurar certificados de administración para habilitar la administración segura. Seguros protocolos estándar como [SSTP](https://technet.microsoft.com/magazine/2007.06.cableguy.aspx) y [IPsec](https://en.wikipedia.org/wiki/IPsec) son totalmente compatibles.

[Azure ExpressRoute](../expressroute/expressroute-introduction.md) le permite crear conexiones privadas entre los centros de datos de Azure y la infraestructura en sus instalaciones o en un entorno de la ubicación. Conexiones de ExpressRoute no vaya a través de Internet pública. Ofrecen más confiabilidad, velocidades, latencia menor y mayor seguridad que típico vínculos basados en Internet. En algunos casos, el uso de conexiones de ExpressRoute para transferir datos entre local y Azure también puede generar beneficios de costo significativo.

##<a name="logging-and-monitoring"></a>Registro y la supervisión

Azure proporciona autenticado un registro de eventos de seguridad relevantes que generan un registro de auditoría y está diseñado para ser resistentes a alteraciones. Esto incluye información del sistema, como registros de eventos de seguridad en máquinas virtuales de infraestructura de Azure y Azure AD. Supervisión de eventos de seguridad incluye la recopilación de eventos como los cambios en DHCP o DNS server direcciones IP, intentos de acceso a los puertos y protocolos o las direcciones IP que están bloqueadas por cambios de diseño, en configuración de directiva o firewall de seguridad, grupo o cuenta de creación, procesos inesperados o instalación del controlador.

![Microsoft Antimalware en Azure](./media/azure-security-getting-started/sec-azgsfig5.PNG)

Grabación acceso de usuarios con privilegios y actividades, intentos de acceso no autorizado y autorizados, excepciones de sistema y la información se conservan los eventos de seguridad para un período de tiempo de registros de auditoría. La retención de los registros es su discreción porque configurar retención y recopilación de registro con sus propios requisitos.

### <a name="how-azure-implements-logging-and-monitoring"></a>¿Cómo implementa Azure registro y la supervisión

Azure implementa a agentes de agentes de administración (MA) y Monitor de seguridad de Azure (ASM) en cada nodo de tela en administración, almacenamiento o cálculo sean nativo o virtual. Cada agente de administración está configurado para autenticar con una cuenta de almacenamiento de grupo de servicio con un certificado que se obtienen del almacén de certificados de Azure y reenviar preconfigurada diagnóstico y datos de evento para la cuenta de almacenamiento. Estos agentes no se implementan en máquinas virtuales de los clientes.

Los administradores de Azure acceder a registros mediante un portal web para el acceso autenticado y controlado a los registros. Un administrador puede analizar, filtrar, relacionar y analizar los registros. Las cuentas de almacenamiento de grupo de servicio de Azure para registros están protegidas del acceso directo del administrador para ayudar a evitar la manipulación de registro.

Microsoft recopila registros de dispositivos de red mediante el protocolo de registro del sistema y de los servidores de host usando Servicios de recopilación de auditorías (ACS) de Microsoft. Estos registros se colocan en una base de datos de registro del que se generan alertas para los eventos sospechosos directamente a un administrador de Microsoft. El administrador puede tener acceso y analizar estos registros.

[Diagnósticos de Azure](https://msdn.microsoft.com/library/azure/gg433048.aspx) es una característica de Azure que permite recopilar datos de diagnóstico desde una aplicación que se ejecuta en Azure. Se trata de datos de diagnóstico para depurar y solución de problemas, medir el rendimiento, uso de recursos, análisis del tráfico y planear la capacidad de supervisión y auditoría. Después de que se recopilan los datos de diagnóstico, se puede transferir a una cuenta de almacenamiento de Azure persistencia. O bien pueden programarse transferencias o a petición.

##<a name="threat-mitigation"></a>Eliminación de amenazas

Además de aislamiento, el cifrado y filtrado, Azure emplea a un número de mecanismos de mitigación de amenazas y procesos para proteger la infraestructura y los servicios. Se incluyen los controles internos y tecnologías utilizadas para detectar y corregir amenazas avanzadas como DDoS, elevación de privilegio y las [10 mejores OWASP](https://www.owasp.org/index.php/Category:OWASP_Top_Ten_Project).

Los controles de seguridad y procesos de administración de riesgos de que Microsoft ha para proteger su infraestructura de nube reducen el riesgo de incidencias de seguridad. Pero, en el caso de que se produzca un incidente, el equipo de administración de incidencias de seguridad (SIM) en el equipo de Microsoft Online Services de seguridad y cumplimiento (OSSC) es 24 x 7 listo para responder.

### <a name="how-azure-implements-threat-mitigation"></a>¿Cómo Azure implementa mitigación de amenazas

Azure tiene seguridad controles implementar mitigación de amenazas y ayudar a clientes mitigación posibles amenazas en su entorno. La lista siguiente resume las capacidades de mitigación de amenazas ofrecidas por Azure:

-   [Azure antimalware](../security/azure-security-antimalware.md) está habilitado de forma predeterminada en todos los servidores de infraestructura. Opcionalmente, puede habilitar dentro de sus propio máquinas virtuales.

-   Microsoft mantiene continua supervisión en servidores, redes y aplicaciones para detectar amenazas y evitar ataques. Alertas automatizadas notificación a los administradores de comportamientos irregulares, lo que les permite tomar medidas correctivas en amenazas internas y externas.

-   Tiene la opción de implementar soluciones de seguridad de la parte 3 º dentro de sus suscripciones, como el Firewall de aplicación web de [Barracuda](https://techlib.barracuda.com/ng54/deployonazure).

-   Enfoque de Microsoft para pruebas de penetración incluye "[Agrupación rojo](http://download.microsoft.com/download/C/1/9/C1990DBA-502F-4C2A-848D-392B93D9B9C3/Microsoft_Enterprise_Cloud_Red_Teaming.pdf)", que implica a profesionales de seguridad de Microsoft a sistemas de producción (que no sea de clientes) en Azure para probar la defensa frente a amenazas persistentes reales, avanzadas.

-   Sistemas de implementación integrada administración la instalación de revisiones de seguridad y distribución a través de la plataforma Windows Azure.

##<a name="next-steps"></a>Pasos siguientes

[Centro de confianza de Azure](https://azure.microsoft.com/support/trust-center/)

[Blog del equipo de seguridad de Azure](http://blogs.msdn.com/b/azuresecurity/)

[Centro de respuesta de seguridad de Microsoft](https://technet.microsoft.com/library/dn440717.aspx)

[Blog de Active Directory](http://blogs.technet.com/b/ad/)
