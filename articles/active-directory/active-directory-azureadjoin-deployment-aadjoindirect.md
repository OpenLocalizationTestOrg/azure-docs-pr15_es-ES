<properties
    pageTitle="Escenarios de uso y consideraciones de implementación de Azure AD unirse | Microsoft Azure"
    description="Explica cómo los administradores pueden configurar Azure AD unirse para sus usuarios finales (empleados, estudiantes, otros usuarios). También trata los diferentes escenarios reales para usar la combinación de Azure AD."
    services="active-directory"
    documentationCenter=""
    authors="femila"
    manager="swadhwa"
    editor=""
    tags="azure-classic-portal"/>

< etiquetas ms.service= "active directory" ms.workload="identity" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/27/2016"

    ms.author="femila"/>

# <a name="usage-scenarios-and-deployment-considerations-for-azure-ad-join"></a>Consideraciones de implementación de Azure AD unirse y escenarios de uso

## <a name="usage-scenarios-for-azure-ad-join"></a>Escenarios de uso para unirse a AD de Azure
### <a name="scenario-1-businesses-largely-in-the-cloud"></a>Escenario 1: Negocios en gran medida en la nube

Azure Active Directory unirse (Azure AD unirse a) pueden beneficiarse si actualmente operar y administrar identidades para su empresa en la nube o se mueve a la nube pronto. Puede usar una cuenta que ha creado en Azure AD para iniciar sesión en Windows 10. Por [el primer proceso de experiencia de ejecución (FRX)](active-directory-azureadjoin-user-frx.md)o unirse a Azure AD desde [el menú Configuración](active-directory-azureadjoin-user-upgrade.md), los usuarios pueden unirse a los equipos a Azure AD.  Los usuarios también pueden disfrutar de inicio de sesión único (SSO) acceso único a recursos de la nube como Office 365, en los exploradores o aplicaciones de Office.

### <a name="scenario-2-educational-institutions"></a>Escenario 2: Docentes

Instituciones educativas normalmente cuentan con dos tipos de usuario: los profesores y alumnos. Los miembros del personal docente se consideran a miembros a largo plazo de la organización. Es aconsejable crear cuentas locales para ellos. Pero los estudiantes se shorter-term miembros de la organización y pueden administrarse sus cuentas de Azure AD. Esto significa que se puede insertar escala de directorio en la nube en lugar de ser almacenado en local. También significa que los estudiantes podrán iniciar sesión en Windows con sus cuentas de Azure AD y obtener acceso a los recursos de Office 365 en aplicaciones de Office.

### <a name="scenario-3-retail-businesses"></a>Escenario 3: Empresas de minorista

Empresas de comercio por menor tienen trabajadores de temporada y empleados a largo plazo. Normalmente crear cuentas locales y usar equipos unidos a un dominio para empleados a tiempo completo a largo plazo. Trabajadores de temporada son shorter-term miembros de la organización pero es aconsejable administrar sus cuentas donde licencias de usuario se pueden mover con más facilidad en. Cuando cree sus cuentas de usuario en la nube con licencias de Office 365, estos usuarios Obtén las ventajas de iniciar sesión en Windows y Office aplicaciones con una cuenta de Azure AD, mientras mantiene más flexibilidad con las licencias después de abandonar.

### <a name="scenario-4-additional-scenarios"></a>Escenario 4: Escenarios adicionales

Junto con las ventajas que se indicó anteriormente, las ventajas de tener los usuarios unirse a sus dispositivos a Azure AD debido a una experiencia simplificada de unión, administración de dispositivos eficaz, inscripción de administración automática de dispositivos móviles y de inicio de sesión único de Azure AD y recursos locales.  


## <a name="deployment-considerations-for-azure-ad-join"></a>Consideraciones de implementación de Azure AD unirse

### <a name="enable-your-users-to-join-a-company-owned-device-directly-to-azure-ad"></a>Permitir a los usuarios para unirse a un dispositivo propiedad de la empresa directamente a Azure AD


Las empresas pueden proporcionar solo nube cuentas a las organizaciones y empresas asociadas. Estos socios, a continuación, pueden acceder fácilmente aplicaciones de empresa y los recursos con inicio de sesión único. Este escenario es aplicable a los usuarios que tengan acceso a recursos principalmente en la nube, como Office 365 o SaaS aplicaciones que se basan en Azure AD para la autenticación.

### <a name="prerequisites"></a>Requisitos previos
**En el nivel de empresa (Administrador)**

*   Suscripción de Azure con Azure Active Directory  

**En el nivel de usuario**

*   Windows 10 (ediciones Professional y Enterprise)

### <a name="administrator-tasks"></a>Tareas de administrador
* [Configurar el registro de dispositivo](active-directory-azureadjoin-setup.md)

### <a name="user-tasks"></a>Tareas de usuario
* [Configurar un nuevo dispositivo de Windows 10 con Azure AD durante la instalación](active-directory-azureadjoin-user-frx.md)
* [Configurar un dispositivo de Windows 10 con Azure AD desde el menú Configuración](active-directory-azureadjoin-user-upgrade.md)
* [Unirse a un dispositivo de Windows 10 personal a la organización](active-directory-azureadjoin-personal-device.md)



## <a name="enable-byod-in-your-organization-for-windows-10"></a>Habilitar BYOD de su organización para Windows 10
Puede configurar los usuarios y empleados para usar sus recursos y dispositivos de Windows personales (BYOD) para obtener acceso a las aplicaciones de empresa. Los usuarios pueden agregar sus cuentas de Azure AD (cuentas de trabajo o escuela) a un dispositivo de Windows personal acceso a los recursos de forma segura y compatible.

### <a name="prerequisites"></a>Requisitos previos
**En el nivel de empresa (Administrador)**

*   Suscripción de Azure AD

**En el nivel de usuario**

*   Windows 10 (ediciones Professional y Enterprise)


### <a name="administrator-tasks"></a>Tareas de administrador

* [Configurar el registro de dispositivo](active-directory-azureadjoin-setup.md)

### <a name="user-tasks"></a>Tareas de usuario
* [Unirse a un dispositivo de Windows 10 personal a la organización](active-directory-azureadjoin-personal-device.md)


## <a name="additional-information"></a>Información adicional
* [Windows 10 para la empresa: formas de usar dispositivos para el trabajo](active-directory-azureadjoin-windows10-devices-overview.md)
* [Amplía las capacidades de nube para Windows 10 dispositivos a través de Azure Active Directory unirse](active-directory-azureadjoin-user-upgrade.md)
* [Autenticación de identidades sin contraseñas a través de Microsoft Passport](active-directory-azureadjoin-passport.md)
* [Obtenga información sobre escenarios de uso para unirse a AD de Azure](active-directory-azureadjoin-deployment-aadjoindirect.md)
* [Conectar dispositivos unido al dominio con Azure AD para experiencias de Windows 10](active-directory-azureadjoin-devices-group-policy.md)
* [Configurar combinación de Azure AD](active-directory-azureadjoin-setup.md)
