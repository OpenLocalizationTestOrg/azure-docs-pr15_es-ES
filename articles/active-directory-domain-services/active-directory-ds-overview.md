<properties
    pageTitle="Información general sobre servicios de dominio de Azure Active Directory | Microsoft Azure"
    description="Información general sobre servicios de dominio de Azure Active Directory"
    services="active-directory-ds"
    documentationCenter=""
    authors="mahesh-unnikrishnan"
    manager="stevenpo"
    editor="curtand"/>

<tags
    ms.service="active-directory-ds"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/07/2016"
    ms.author="maheshu"/>

# <a name="azure-ad-domain-services"></a>Servicios de dominio de Active Directory de Azure

## <a name="overview"></a>Información general
Servicios de infraestructura de Azure le permiten implementar una amplia gama de soluciones de sistemas de forma ágil. Con Azure máquinas virtuales de Windows, puede implementar casi instantáneamente y paga sólo con el minuto. Con el soporte técnico para Windows, Linux, SQL Server, Oracle, IBM, SAP y BizTalk, puede implementar cualquier carga de trabajo, cualquier idioma en casi cualquier sistema operativo. Estos beneficios le permiten migrar aplicaciones heredadas implementadas local a Azure, para guardar en los gastos operativos.

Un aspecto clave de migración de aplicaciones locales con Azure está controlando las necesidades de la identidad de estas aplicaciones. Directorio de aplicaciones pueden basar en LDAP de acceso de lectura o escritura en el directorio corporativo o se basan en autenticación integrada de Windows (autenticación Kerberos o NTLM) para autenticar a los usuarios finales. Aplicaciones de línea de negocio (LOB) en Windows Server normalmente se implementan en equipos de la pertenencia a un dominio, por lo que se pueden administrar de forma segura con directivas de grupo. Aplicaciones 'elevación y Mayús' local en la nube, es necesario resolver estas dependencias en la infraestructura de identidad corporativa.

Los administradores suelen optar por una de las siguientes soluciones para satisfacer las necesidades de la identidad de sus aplicaciones de implementada en Azure:

- Implementar una conexión VPN de sitio a sitio entre las cargas de trabajo que se ejecuta en servicios de infraestructura de Azure y el directorio corporativo local.
- Ampliar la infraestructura de dominio o bosque de AD corporativa mediante la configuración de los controladores de dominio de réplica mediante máquinas virtuales de Windows Azure.
- Implementar un dominio independiente en Azure con controladores de dominio implementados como máquinas virtuales de Windows Azure.

Todos estos enfoques sufran alto costo y sobrecarga administrativa. Los administradores deben implementar los controladores de dominio en máquinas virtuales de Windows Azure. Además, necesitan administrar, proteger, revisiones, supervisar, hacer copia de seguridad y solucionar estas máquinas virtuales. La dependencia de conexiones VPN en el directorio local hace implementados en Azure a ser problemas de red transitorias o interrupciones de cargas de trabajo. Estas interrupciones de red por separado resultado inferior actividad y menor confiabilidad para estas aplicaciones.

Hemos diseñado servicios de dominio de Active Directory de Azure para proporcionar una alternativa más sencilla.


## <a name="introducing-azure-ad-domain-services"></a>Introducción a servicios de dominio de Active Directory de Azure
Servicios de dominio de Active Directory Azure proporciona servicios de dominio administrado como unirse a un dominio, autenticación Kerberos/NTLM de grupo Directiva, LDAP, que son totalmente compatibles con Active Directory de Windows Server. Puede utilizar estos servicios de dominio sin necesidad de implementar, administrar y revisar los controladores de dominio en la nube. Servicios de dominio de Active Directory Azure se integra con el inquilino de Azure AD existente, lo que los usuarios puedan iniciar sesión con sus credenciales de corporativas. Además, puede usar los grupos existentes y cuentas de usuario para proteger el acceso a los recursos, lo que garantiza una más suave 'elevación-y-Mayús' de recursos locales a servicios de infraestructura de Azure.

Funcionalidad de servicios de dominio de Active Directory Azure funciona sin problemas independientemente de si su inquilino de Azure AD es solo nube o sincronizado con Active Directory local.

### <a name="azure-ad-domain-services-for-cloud-only-organizations"></a>Servicios de dominio de Azure AD para las organizaciones solo nube
Solo nube inquilino de Azure AD (a menudo se denomina 'inquilinos administrados') no tiene un espacio de identidad local. En otras palabras, las cuentas de usuario, sus contraseñas y pertenencia a grupos es todo ello nativo de la nube, es decir, crea y administra en Azure AD. Considere por un momento que Contoso es solo nube inquilino de Azure AD. Como se muestra en la siguiente ilustración, el Administrador de Contoso ha configurado una red virtual en servicios de infraestructura de Azure. Aplicaciones y cargas de trabajo se implementan en esta red virtual en máquinas virtuales de Windows Azure. Dado que Contoso es un inquilino solo nube, todas las identidades de usuario, sus credenciales, y crea y administra en Azure AD pertenencias a grupos.

![Información general de servicios de dominio de Active Directory de Azure](./media/active-directory-domain-services-overview/aadds-overview.png)

Del Contoso Administrador de TI puede habilitar servicios de dominio de Active Directory de Azure para su inquilino de Azure AD y elegir a disposición de los servicios de dominio de la red virtual. Por lo tanto, los servicios de dominio de Azure AD aprovisiona un dominio administrado y hace que esté disponible en la red virtual. Todas las cuentas de usuario, pertenencias a grupos y las credenciales de usuario disponibles en el inquilino de Azure AD de Contoso también están disponibles en este dominio recién creado. Esta característica permite a los usuarios de la organización iniciar sesión en el dominio con sus credenciales corporativas: por ejemplo, cuando conecte remotamente a equipos unidos a un dominio a través de escritorio remoto. Los administradores pueden aprovisionar acceso a los recursos del dominio con los miembros del grupo existente. Aplicaciones implementadas en máquinas virtuales en la red virtual pueden usar características como unirse a un dominio, lectura LDAP, enlace LDAP, autenticación Kerberos y NTLM y directiva de grupo.

Algunos aspectos destacados del dominio administrado que se le proporciona servicios de dominio de Active Directory de Azure son las siguientes:

- Del Contoso Administrador de TI no es necesario administrar, revisión o supervisar este dominio o los controladores de dominio para este dominio administrado.
- No es necesario para administrar la replicación de AD para este dominio. Cuentas de usuario, pertenencias a grupos y las credenciales de inquilinos de Azure AD de Contoso están automáticamente disponibles en este dominio administrado.
- Dado que el dominio se administra Azure AD servicios de dominio, Contoso del Administrador de TI no tiene privilegios de administrador de empresa o de administrador de dominio en este dominio.


### <a name="azure-ad-domain-services-for-hybrid-organizations"></a>Servicios de dominio de Azure AD para las organizaciones híbrido
Organizaciones con una infraestructura de TI híbrida consuman una mezcla de recursos de la nube y locales. Dichas organizaciones sincronizan la información de identidad desde su directorio local a su inquilino de Azure AD. Aspecto de las organizaciones híbrido para migrar más de sus aplicaciones local en la nube, especialmente heredadas directorio de aplicaciones, servicios de dominio de Active Directory de Azure pueden ser útiles en ellos.

Litware Corporation ha implementado [Azure AD Connect](../active-directory/active-directory-aadconnect.md)para sincronizar la información de identidad desde su directorio local a su inquilino de Azure AD. La información de identidad que se sincroniza incluye las cuentas de usuario, sus hash credenciales para la autenticación (sincronización de la contraseña) y la pertenencia a grupos.

> [AZURE.NOTE] **Sincronización de contraseñas es obligatoria para las organizaciones híbrido usar los servicios de dominio de Active Directory de Azure**. Este requisito es porque se necesitan credenciales de los usuarios en el dominio administrado proporcionado servicios de dominio de Active Directory de Azure, para autenticar a estos usuarios a través de métodos de autenticación de NTLM o Kerberos.

![Servicios de dominio de Azure AD para Litware Corporation](./media/active-directory-domain-services-overview/aadds-overview-synced-tenant.png)

La ilustración anterior muestra cómo las organizaciones con un híbrido infraestructura de TI, como Litware Corporation, pueden usar servicios de dominio de Active Directory de Azure. Cargas de trabajo que requieren servicios de dominio y aplicaciones de Litware se implementan en una red virtual en servicios de infraestructura de Azure. Del Litware Administrador de TI puede habilitar servicios de dominio de Active Directory de Azure para su inquilino de Azure AD y elegir disponer de un dominio administrado en esta red virtual. Puesto que Litware es una organización con una infraestructura de TI híbridos, credenciales, grupos y cuentas de usuario se sincronizan con su inquilino de Azure AD desde su directorio local. Esta característica permite a los usuarios iniciar sesión en el dominio con sus credenciales corporativas: por ejemplo, al conectarse a los equipos de forma remota unido al dominio a través de escritorio remoto. Los administradores pueden aprovisionar acceso a los recursos del dominio con los miembros del grupo existente. Aplicaciones implementadas en máquinas virtuales en la red virtual pueden usar características como unirse a un dominio, lectura LDAP, enlace LDAP, autenticación Kerberos y NTLM y directiva de grupo.

Algunos aspectos destacados del dominio administrado que se le proporciona servicios de dominio de Active Directory de Azure son las siguientes:

- El dominio administrado es un dominio independiente. No es una extensión del dominio de Litware local.
- Del Litware Administrador de TI no necesita administrar, revisión, o supervisar los controladores de dominio para este dominio administrado.
- No es necesario para administrar la replicación de AD para este dominio. Cuentas de usuario, pertenencias a grupos y las credenciales de directorio de Litware local se sincronizan con Azure AD a través de Azure AD Connect. Estas cuentas de usuario, pertenencias a grupos y las credenciales están automáticamente disponibles en el dominio administrado.
- Dado que el dominio se administra Azure AD servicios de dominio, Litware del Administrador de TI no tiene privilegios de administrador de empresa o de administrador de dominio en este dominio.


## <a name="benefits"></a>Ventajas
Con los servicios de dominio de Active Directory de Azure, puedes disfrutar de las siguientes ventajas:

-   **Simple** : puede satisfacer las necesidades de identidad de máquinas virtuales implementados a servicios de infraestructura de Azure con unos pocos clics simples. No es necesario implementar y administrar la infraestructura de identidades en Azure o configuración conectividad con su infraestructura de identidad local.

-   **Integrado** : servicios de dominio de Active Directory de Azure está estrechamente integrada con el inquilino de Azure AD. Ahora puede usar Azure AD como un directorio de integración empresarial basada en nube que se suministra a las necesidades de sus aplicaciones modernas y la tradicionales directorio de aplicaciones.

-   **Compatible** , servicios de dominio de Active Directory de Azure se basa en la infraestructura de calificaciones empresarial comprobada de Active Directory de Windows Server. Por lo tanto, las aplicaciones pueden confiar en un mayor grado de compatibilidad con las características de Active Directory de Windows Server. No todas las características disponibles en Windows Server AD están disponibles actualmente en servicios de dominio de Active Directory de Azure. Sin embargo, las características disponibles son compatibles con las características de Windows Server AD correspondientes que se basan en la infraestructura local. Las funciones de combinación LDAP, Kerberos, NTLM, directiva de grupo y dominio constituyen una oferta para adultos que ha probado y refina varias versiones de Windows Server.

-   **Rentable** : con los servicios de dominio de Azure AD, puede evitar la carga de infraestructura y administración asociados con la administración de la infraestructura de identidades para admitir tradicionales directorio de aplicaciones. Puede mover estas aplicaciones a servicios de infraestructura de Azure y beneficiarse de ahorros mayores en los gastos operativos.
