
<properties
    pageTitle="Referencia técnica de Azure Active Directory de Access condicional | Microsoft Azure"
    description="Con control de acceso condicional Azure Active Directory comprueba las condiciones específicas que elegir para autenticar al usuario y antes de permitir el acceso a la aplicación. Una vez que se cumplen las condiciones, el usuario autenticado y permite el acceso a la aplicación."
    services="active-directory"
    documentationCenter=""
    authors="MarkusVi"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="identity" 
    ms.date="10/20/2016"
    ms.author="markvi"/>

# <a name="azure-active-directory-conditional-access-technical-reference"></a>Referencia técnica de Azure acceso condicional de Active Directory

## <a name="services-enabled-with-conditional-access"></a>Servicios habilitados con acceso condicional
Reglas de acceso condicional compatibles entre distintos tipos de aplicación de Azure AD. Esta lista incluye:

- Aplicaciones federadas desde la Galería de la aplicación de Azure AD
- Aplicaciones de SSO de contraseña de la Galería de la aplicación de Azure AD
- Aplicaciones registradas con el Proxy de aplicación de Azure
- Desarrollado línea de negocio y aplicaciones de múltiples arrendatarios registradas con Azure AD
- Visual Studio en línea
- Aplicación remota Azure
-   Dynamics CRM
- Yammer de Microsoft Office 365
- Microsoft Office 365 Exchange Online
- Microsoft Office 365 SharePoint Online (incluye OneDrive para la empresa)


## <a name="enable-access-rules"></a>Habilitar reglas de acceso

Cada regla se puede habilitar o deshabilitar en un por bases de datos de aplicación. Cuando las reglas son **ON** se habilitado y exige a los usuarios acceso a la aplicación. Cuando se les **OFF** que no se usan y no afectará a la experiencia de inicio de sesión de los usuarios.

## <a name="applying-rules-to-specific-users"></a>Aplicación de reglas a usuarios específicos
Las reglas se pueden aplicar a conjuntos específicos de usuarios en función de grupo de seguridad estableciendo **Aplicar a**. **Aplicar a** puede establecerse a **Todos los usuarios** o **grupos**. Cuando se establece para **Todos los usuarios** , las reglas se aplicarán a cualquier usuario con acceso a la aplicación. Permite a la opción de **grupos de** seguridad específicos y grupos de distribución que se seleccione, solo se aplicarán las reglas para estos grupos.

Al implementar una regla, es común para aplicarlo primero un conjunto limitado de usuarios, que son los miembros de grupos de piloto. Una vez completada la regla se puede aplicar a **Todos los usuarios**. Esto hará que la regla se aplica para todos los usuarios de la organización.

Seleccionar grupos también podrán quedar exentos de directiva de uso de la opción **excepto** . Incluso si aparecen en un grupo incluido quedarán excluidos todos los miembros de estos grupos.

## <a name="at-work-networks"></a>Redes "en el trabajo"


Las reglas de acceso condicional que usan una red "en el trabajo", que se basan en confianza intervalos de direcciones IP que se han configurado en Azure AD, o el uso de la notificación "dentro de la red corporativa" de AD FS. Incluyen estas reglas:

- Requerir autenticación multifactor cuando no en el trabajo
- Bloquear el acceso cuando no en el trabajo

Opciones para especificando redes "en el trabajo"

1. Configurar los intervalos de direcciones IP confiables en la [página de configuración de autenticación multifactor](../multi-factor-authentication/multi-factor-authentication-whats-next.md). Directiva de acceso condicional usará los intervalos configurados en cada solicitud de autenticación y emisión de token para evaluar reglas. 
2. Configurar el uso de la parte interior reclamar la red corporativa, esta opción puede usarse con directorios federados, con AD FS. [Obtener más información sobre el interior reclamaciones coronet](../multi-factor-authentication/multi-factor-authentication-whats-next.md#trusted-ips).
3. Configurar los intervalos de direcciones IP públicas. En la ficha configurar, para el directorio, puede establecer las direcciones IP públicas. Acceso condicional Utilice estos como 'at work' direcciones IP, lo que permite rangos adicionales configurar, por encima del límite de dirección IP 50 que se aplica mediante la página de configuración de MFA.



## <a name="rules-based-on-application-sensitivity"></a>Reglas basadas en confidencialidad de aplicación

Las reglas se configuran por aplicación, que permite a los servicios de alto valor para protegerse sin afectar el acceso a otros servicios. En la ficha **Configurar** de la aplicación se pueden configurar reglas de acceso condicional. 

Las reglas se ofrecen actualmente:

- **Requerir autenticación multifactor**
 - Todos los usuarios que se aplica esta directiva deben autenticar a través de la autenticación multifactor al menos una vez.
 
- **Requerir autenticación multifactor cuando no en el trabajo**
 - Si se aplica esta directiva, todos los usuarios deben haber realizado con autenticación multifactor al menos una vez si tienen acceso el servicio desde una ubicación remota de trabajo no. Si mueve de un trabajo a una ubicación remota, deberán realizar la autenticación al obtener acceso al servicio.
 
- **Bloquear el acceso cuando no en el trabajo** 
 - Al mover los usuarios desde el trabajo a una ubicación remota, se bloquearán si se les aplica la directiva "Bloquear el acceso cuando no está en trabajo".  Vuelve a tendrán acceso a la ubicación de trabajo.


## <a name="related-topics"></a>Temas relacionados

- [Proteger el acceso a Office 365 y otras aplicaciones conectado a Azure Active Directory](active-directory-conditional-access.md)
- [Índice de artículos de administración de aplicaciones de Azure Active Directory](active-directory-apps-index.md)
