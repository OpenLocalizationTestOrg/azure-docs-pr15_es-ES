<properties
    pageTitle="Vulnerabilidad detectados por la protección de identidad de Azure Active Directory | Microsoft Azure"
    description="Información general sobre la vulnerabilidad detectados por la protección de identidad de Azure Active Directory."
    services="active-directory"
    keywords="protección de la identidad de Azure directorio activo, detección de aplicación de nube, administración de aplicaciones, seguridad, riesgo, nivel de riesgo, vulnerabilidad, directiva de seguridad"
    documentationCenter=""
    authors="markusvi"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/22/2016"
    ms.author="markvi"/>

# <a name="vulnerabilities-detected-by-azure-active-directory-identity-protection"></a>Vulnerabilidad detectados por la protección de identidad de Azure Active Directory 

Vulnerabilidad es puntos débiles en su entorno que puede aprovecharse un intruso. Le recomendamos que corrige estos vulnerabilidad para mejorar la posición de seguridad de su organización y evitar que los atacantes aprovechamiento de ellos.
<br><br>
![vulnerabilidad](./media/active-directory-identityprotection-vulnerabilities/101.png "vulnerabilities")
<br>

Las secciones siguientes le proporciona una descripción general de la vulnerabilidad notificado por la protección de identidad.

## <a name="multi-factor-authentication-registration-not-configured"></a>No se ha configurado el registro de autenticación multifactor 

Esta vulnerabilidad le ayuda a controlar la implementación de Azure con autenticación multifactor de su organización. 

La autenticación multifactor Azure proporciona una segunda capa de seguridad para la autenticación de usuario. Ayuda a proteger el acceso a datos y aplicaciones cumpliendo demanda de usuario de un proceso de inicio de sesión simple. Ofrece autenticación segura a través de un rango de opciones de comprobación fácil, llamada de teléfono, mensajes de texto o aplicación móvil notificación o una comprobación de código y 3 º fiesta JURAMENTO tokens.

Le recomendamos que requieren autenticación multifactor de Azure para inicios de sesión de usuario. Autenticación multifactor desempeña un papel clave en las directivas de acceso condicional basada en riesgos disponibles a través de protección de la identidad.

Para obtener más detalles, consulte [¿Qué es la autenticación multifactor de Azure?](../multi-factor-authentication/multi-factor-authentication.md)


## <a name="unmanaged-cloud-apps"></a>Aplicaciones de nube no administrado

Esta vulnerabilidad le ayuda a identificar aplicaciones de nube no administrado de su organización.
 
En las empresas modernas, los departamentos de TI a menudo son conscientes de todas las aplicaciones de nube que los usuarios de su organización están usando para hacer su trabajo. Es más fácil ver por qué los administradores tendrían dudas sobre el acceso no autorizado a los datos corporativos, pérdidas de datos y otros riesgos de seguridad. 

Se recomienda que su organización implementar detección de aplicación de nube para descubrir aplicaciones de nube no administrado y administrar estas aplicaciones con Azure Active Directory.

Para obtener más detalles, vea [Buscar aplicaciones de nube no administrado con detección de aplicación de nube](active-directory-cloudappdiscovery-whatis.md).



##<a name="security-alerts-from-privileged-identity-management"></a>Alertas de seguridad de la administración de identidades con privilegios

Esta vulnerabilidad le ayuda a detectar y resolver alertas acerca de las identidades con privilegios de su organización.  

Para habilitar los usuarios para llevar a cabo operaciones con privilegios, las empresas deben conceder acceso con privilegios de los usuarios permanentes o temporales en Azure AD recursos Azure u Office 365 u otras aplicaciones de SaaS. Cada uno de estos usuarios con privilegios aumenta la superficie de ataque de su organización. Esta vulnerabilidad le ayuda a identificar a los usuarios con acceso con privilegios innecesario y tomar las medidas adecuadas para reducir o eliminar el riesgo que suponen. 

Se recomienda que su organización usa la administración de identidades con privilegios de Azure AD para administrar, controlar y supervisar con privilegios identidades y el acceso a los recursos en Azure AD, así como otros servicios en línea de Microsoft como Office 365 o Microsoft Intune.

Para obtener más detalles, consulte [la administración de identidades con privilegios de Azure AD](active-directory-privileged-identity-management-configure.md). 



## <a name="see-also"></a>Vea también

 - [Protección de la identidad de Azure Active Directory](active-directory-identityprotection.md)
