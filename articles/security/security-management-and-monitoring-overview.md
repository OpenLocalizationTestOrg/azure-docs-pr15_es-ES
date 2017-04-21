<properties
   pageTitle="Administración de seguridad de Azure e información general sobre supervisión | Microsoft Azure"
   description=" Azure proporciona mecanismos de seguridad como ayuda para la administración y supervisión de servicios de nube de Azure y máquinas virtuales de Windows.  Este artículo proporciona una descripción general de estas características básicas de seguridad y servicios. "
   services="security"
   documentationCenter="na"
   authors="TerryLanfear"
   manager="StevenPo"
   editor="TomSh"/>

<tags
   ms.service="security"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/16/2016"
   ms.author="terrylan"/>

# <a name="azure-security-management-and-monitoring-overview"></a>Administración de seguridad de Azure e información general sobre supervisión

Azure proporciona mecanismos de seguridad como ayuda para la administración y supervisión de servicios de nube de Azure y máquinas virtuales de Windows. Este artículo proporciona una descripción general de estas características básicas de seguridad y servicios. Se proporcionan vínculos a artículos que dará detalles de cada uno, por lo que puede obtener más información.

La seguridad de los servicios de nube de Microsoft es una asociación y una responsabilidad compartida entre usted y Microsoft. Responsabilidad compartida significa Microsoft es responsable de Microsoft Azure y centros de seguridad física de sus datos (mediante el uso de protección de seguridad como bloqueada distintivo puertas de entrada, límites y protege). Además, Azure proporciona fuerte niveles de seguridad de la nube en el nivel de software que satisfaga las necesidades de seguridad, privacidad y cumplimiento de sus clientes exigentes.

Propietario de los datos y las identidades, la responsabilidad de proteger, la seguridad de los recursos locales y la seguridad de los componentes de nube en los que tenga el control. Microsoft le ofrece capacidades para ayudar a proteger sus datos y aplicaciones y controles de seguridad. La responsabilidad de la seguridad se basa en el tipo de servicio de nube.

En la tabla siguiente resume el saldo de responsabilidad de Microsoft y el cliente.

![Responsabilidad compartida][1]

Para un análisis más profundo sobre administración de seguridad, vea [administración de seguridad en Azure](azure-security-management.md).

Estas son las características principales que se tratan en este artículo:

- Control de acceso basado en roles
- Antimalware
- Autenticación multifactor
- ExpressRoute
- Puertas de enlace de red virtual
- Administración de identidades con privilegios
- Protección de identidad
- Centro de seguridad

## <a name="role-based-access-control"></a>Control de acceso basado en roles

Control de acceso basado en roles (RBAC) proporciona acceso específico de administración de Azure recursos. Using RBAC, puede conceder a los usuarios solo la cantidad de acceso que necesitan para realizar su trabajo.  RBAC también puede ayudarle a asegurarse de que cuando personas dejan la organización pierden el acceso a los recursos en la nube.

Aprende más:

- [Blog del equipo de Active Directory en RBAC](http://i1.blogs.technet.com/b/ad/archive/2015/10/12/azure-rbac-is-ga.aspx)
- [Control de acceso basado en roles de Azure](../active-directory/role-based-access-control-configure.md)

## <a name="antimalware"></a>Antimalware

Puede usar software antimalware de proveedores de principales de seguridad como Microsoft, Symantec, tendencia Micro, McAfee y Kaspersky para proteger sus máquinas virtuales de archivos malintencionados, publicidad y otras amenazas con Azure.

Microsoft Antimalware le ofrece la capacidad para instalar a un agente antimalware para PaaS roles y máquinas virtuales. En función de System Center Endpoint Protection, esta función aporta local comprobada tecnología de seguridad en la nube.

También le ofrecemos integración de de tendencias [Seguridad profunda](http://www.trendmicro.com/us/enterprise/cloud-solutions/deep-security/)™ y [SecureCloud](http://www.trendmicro.com/us/enterprise/cloud-solutions/secure-cloud/)™ productos en la plataforma Windows Azure. DeepSecurity es una solución Antivirus y SecureCloud es una solución de cifrado. DeepSecurity se implementará dentro de máquinas virtuales con un modelo de extensión. Con el portal de interfaz de usuario y PowerShell, puede usar DeepSecurity dentro de nuevas máquinas virtuales que giran hacia arriba o máquinas virtuales existentes que ya se han implementado.

Protección de punto final de Symantec (SEP) también se admite en Azure. Mediante la integración de portal, los clientes tienen la capacidad para especificar que se va a utilizar SEP dentro de una máquina virtual. SEP se puede instalar en una máquina virtual nueva a través del Portal de Azure o se puede instalar en una máquina virtual existente con PowerShell.

Aprende más:

- [Implementar soluciones de Antimalware en máquinas virtuales de Windows Azure](https://azure.microsoft.com/blog/deploying-antimalware-solutions-on-azure-virtual-machines/)
- [Antimalware de Microsoft para servicios de nube de Azure y máquinas virtuales](../security/azure-security-antimalware.md)
- [Cómo instalar y configurar la seguridad de profundidad de tendencia Micro como un servicio en una máquina virtual de Windows](../virtual-machines/virtual-machines-windows-classic-install-trend.md)
- [Cómo instalar y configurar Symantec Endpoint Protection en una máquina virtual de Windows](../virtual-machines/virtual-machines-windows-classic-install-symantec.md)
- [Nuevas opciones de Antimalware para proteger máquinas virtuales de Azure – McAfee Endpoint Protection](https://azure.microsoft.com/blog/new-antimalware-options-for-protecting-azure-virtual-machines/)

## <a name="multi-factor-authentication"></a>Autenticación multifactor

Azure con autenticación multifactor (AMF) es un método de autenticación que requiere el uso de más de un método de verificación y se agrega una segunda capa crítica de seguridad para inicios de sesión de usuario y las transacciones. AMF ayuda a proteger el acceso a datos y aplicaciones cumpliendo demanda de usuario de un proceso de inicio de sesión simple. Ofrece autenticación segura a través de un rango de opciones de comprobación, llamada de teléfono, mensajes de texto o aplicación móvil notificación o una comprobación de código y 3 º fiesta JURAMENTO tokens.

Aprende más:

- [Autenticación multifactor](https://azure.microsoft.com/documentation/services/multi-factor-authentication/)
- [¿Qué es la autenticación multifactor de Azure?](../multi-factor-authentication/multi-factor-authentication.md)
- [Cómo funciona la autenticación multifactor de Azure](../multi-factor-authentication/multi-factor-authentication-how-it-works.md)

## <a name="expressroute"></a>ExpressRoute

Microsoft Azure ExpressRoute le permite ampliar sus redes local en la nube de Microsoft sobre una conexión privada dedicada facilitada mediante un proveedor de servicios de conectividad. Con ExpressRoute, puede establecer conexiones a servicios de nube de Microsoft, como Microsoft Azure, Office 365 y CRM Online. Conectividad puede ser de una red (IP VPN) y a cualquier, una red de punto a punto Ethernet o una conexión entre virtual a través de un proveedor de conectividad en una instalación de la ubicación. Conexiones de ExpressRoute no vaya a través de Internet pública. Esto permite conexiones ExpressRoute ofrecer más confiabilidad, velocidades, latencia menor y mayor seguridad que típico conexiones a través de Internet.

Aprende más:

- [Información general técnica de ExpressRoute](../expressroute/expressroute-introduction.md)

## <a name="virtual-network-gateways"></a>Puertas de enlace de red virtual

Las puertas de enlace VPN, también denominado Azure Virtual red puertas de enlace se utilizan para enviar tráfico de red entre redes virtuales y ubicaciones local. También se utilizan para enviar tráfico entre varias redes virtuales dentro de Azure (VNet a VNet).  Puertas de enlace VPN proporcionan conectividad entre local segura entre Azure y su infraestructura.

Aprende más:

- [Acerca de las puertas de enlace VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md)
- [Información general de seguridad de red de Azure](security-network-overview.md)

## <a name="privileged-identity-management"></a>Administración de identidades con privilegios

A veces, los usuarios necesitan para llevar a cabo operaciones con privilegios en Azure recursos u otras aplicaciones de SaaS. Esto suele significar que las organizaciones tiene para darle acceso con privilegios permanente de Azure Active Directory (AD Azure). Esto es aumentando el riesgo de seguridad para recursos hospedada en la nube porque las organizaciones suficientemente no pueden supervisar qué hacen los usuarios con su acceso con privilegios.
Además, si se compromete a una cuenta de usuario con acceso con privilegios, que una infracción puede afectar la seguridad de la nube global. Administración de identidades de AD privilegios Azure ayuda a resolver este riesgo, disminuir el tiempo de exposición de privilegios y aumentar la visibilidad en uso.  

La administración de identidades con privilegios presenta el concepto de un administrador temporal para una función o "en el momento" el acceso de administrador, que es un usuario que necesita para completar un proceso de activación de esa función. El proceso de activación cambia la asignación del usuario a un rol en Azure AD de período inactivo a activo durante un tiempo determinado, como 8 horas.

Aprende más:

- [Administración de identidades de Azure con privilegios de AD](../active-directory/active-directory-privileged-identity-management-configure.md)
- [Introducción a la administración de identidades con privilegios de Azure AD](../active-directory/active-directory-privileged-identity-management-getting-started.md)

## <a name="identity-protection"></a>Protección de identidad

Protección de identidad de Azure Active Directory (AD) proporciona una vista consolidada de actividades de inicio de sesión sospechosas y vulnerabilidad posibles para ayudar a proteger su negocio. Protección de identidad detecta actividades sospechosas para usuarios e identidades con privilegios (Administrador), en función de las señales como ataques, pérdidas de credenciales y complementos de inicio de sesión de ubicaciones desconocidas e infectado dispositivos.

Proporcionando notificaciones y corrección recomendada, protección de identidad ayuda a mitigar riesgos en tiempo real. Calcula la gravedad del riesgo de usuario y puede configurar directivas basadas en el riesgo para ayudar automáticamente a proteger aplicación acceder desde amenazas futuras.

Aprende más:

- [Protección de la identidad de Azure Active Directory](../active-directory/active-directory-identityprotection.md)
- [Canal 9: Azure AD y mostrar identidad: vista previa de protección de identidad](https://channel9.msdn.com/Series/Azure-AD-Identity/Azure-AD-and-Identity-Show-Identity-Protection-Preview)

## <a name="security-center"></a>Centro de seguridad

Centro de seguridad de Azure le ayuda a evitar, detectar y responder a amenazas y proporciona que mayor visibilidad y control, la seguridad de los recursos de Azure. Proporciona seguridad integrada supervisión y la directiva de administración de las suscripciones de Azure, ayuda a detecta amenazas que pueden incluirse en caso contrario, problemas y funciona con un amplio ecosistema de soluciones de seguridad.

Centro de seguridad le ayuda a optimizar y supervisar la seguridad de los recursos de Azure por:

- Lo que le permite definir directivas para los recursos de suscripción Azure según las necesidades de seguridad de su empresa y el tipo de aplicaciones o confidencialidad de los datos de cada suscripción.
- Supervisar el estado de sus máquinas virtuales de Windows Azure, redes y aplicaciones.
- Proporcionar una lista con prioridades de alertas de seguridad, incluidas las alertas de soluciones integradas de socios, junto con la información que necesita para investigar rápidamente y recomendaciones sobre cómo corregir un ataque.

Aprende más:

- [Introducción al centro de seguridad de Azure](../security-center/security-center-intro.md)

<!--Image references-->
[1]: ./media/security-management-and-monitoring-overview/shared-responsibility.png
