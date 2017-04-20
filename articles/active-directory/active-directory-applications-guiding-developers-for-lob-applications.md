<properties
    pageTitle="Azure AD y aplicaciones: guiar a los desarrolladores | Microsoft Azure"
    description="Escrito para profesionales de TI, este artículo proporciona directrices para integrar aplicaciones de Azure con Active Directory."
    services="active-directory"
    documentationCenter=""
    authors="kgremban"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/03/2016"
    ms.author="kgremban"/>

# <a name="azure-ad-and-applications-develop-line-of-business-apps"></a>Azure AD y aplicaciones: desarrolle aplicaciones de línea de negocio

Esta guía proporciona información general sobre el desarrollo de aplicaciones de la línea de negocio (LoB) de Azure Active Directory (AD). El público objetivo es que los administradores globales de Active Directory y Office 365.

## <a name="overview"></a>Información general

Creación de aplicaciones integradas con Azure AD proporciona a los usuarios de su organización inicio de sesión único con Office 365. Tiene la aplicación en proporciona de Azure AD que control sobre la directiva de autenticación de la aplicación. Para obtener más información sobre condicional acceso y cómo proteger aplicaciones con autenticación multifactor (AMF), consulte [configuración de las reglas de acceso](active-directory-conditional-access-azuread-connected-apps.md).

Registrar la aplicación para usar Azure Active Directory. Registro de la aplicación significa que los desarrolladores pueden utilizar Azure AD para autenticar usuarios y solicitar acceso a los recursos de usuario como correo electrónico, calendario y documentos.

Cualquier miembro de su directorio (no invitados) puede registrar una aplicación, que también se conoce como la *creación de un objeto de la aplicación*.

Registrar una aplicación permite que cualquier usuario hacer lo siguiente:

- Obtener una identidad para su aplicación que reconoce Azure AD
- Obtener uno o más información confidencial y las teclas que puede usar la aplicación para autenticarse con AD
- Personalización de la aplicación en el portal de Azure con un nombre personalizado, logotipo, etcetera.
- Características de autorización de Azure AD se aplican a su aplicación, incluidos:
  - Control de acceso basado en roles (RBAC)
  - Azure Active Directory como servidor de autorización de oAuth (segura una API expuesta por la aplicación)

- Declarar permisos requeridos necesarios para la aplicación de la función según lo esperado, incluidos:-permisos de aplicación (solo administradores globales). Por ejemplo: pertenencia a una función en otra suscripción Azure AD de aplicación o función con respecto a un recurso de Azure, grupo recurso o suscripción - permisos delegados (cualquier usuario). Por ejemplo: Azure AD, inicio de sesión y leer el perfil


> [AZURE.NOTE]De forma predeterminada, cualquier miembro puede registrar una aplicación. Para obtener información sobre cómo restringir los permisos para registrar aplicaciones a miembros específicos, vea [cómo se agregan las aplicaciones de Azure AD](active-directory-how-applications-are-added.md#who-has-permission-to-add-applications-to-my-azure-ad-instance).

Aquí es lo que el administrador global, tiene que hacer para ayudar a los desarrolladores a que su aplicación esté listo para producción:

- Configurar reglas de acceso (directiva de acceso o AMF)
- Configurar la aplicación para requerir la asignación de usuario y asignarle usuarios
- Suprimir la experiencia de consentimiento del usuario de forma predeterminada

## <a name="configure-access-rules"></a>Configurar reglas de acceso

Configurar reglas de acceso por la aplicación a sus aplicaciones de SaaS. Por ejemplo, puede requerir AMF o solo permitir acceso a los usuarios de redes de confianza. Los detalles de este están disponibles en la [configuración de las reglas de acceso](active-directory-conditional-access-azuread-connected-apps.md)del documento.

## <a name="configure-the-app-to-require-user-assignment-and-assign-users"></a>Configurar la aplicación para requerir la asignación de usuario y asignarle usuarios

De forma predeterminada, los usuarios pueden acceder aplicaciones sin tener asignado. Sin embargo, si la aplicación expone roles, o si desea que la aplicación aparezca en el panel de acceso de un usuario, debe requerir asignación de usuario.

[Requerir la asignación de usuario](active-directory-applications-guiding-developers-requiring-user-assignment.md)

Si es un suscriptor de Azure AD Premium o conjunto de aplicaciones de movilidad de empresa (EMS), se recomienda utilizar grupos. Asignación de grupos a la aplicación le permite delegar la administración de un acceso continuo al propietario del grupo. Puede crear el grupo o solicite al responsable de su organización para crear el grupo mediante la funcionalidad de administración de grupo.

[Asignar usuarios a una aplicación](active-directory-applications-guiding-developers-assigning-users.md)  
[Asignación de grupos a una aplicación](active-directory-applications-guiding-developers-assigning-groups.md)

## <a name="suppress-user-consent"></a>Suprimir consentimiento del usuario

De forma predeterminada, cada usuario pasa por una experiencia de consentimiento para iniciar sesión. La experiencia de consentimiento pedir a los usuarios conceder permisos para una aplicación, puede resultar desconcertante para los usuarios que no están familiarizados con dichas decisiones.

Para las aplicaciones de confianza, puede simplificar la experiencia del usuario al dar su consentimiento para la aplicación en el nombre de su organización.

Para obtener más información sobre la experiencia de consentimiento en Azure y consentimiento del usuario, consulte [Aplicaciones de integración con Azure Active Directory](active-directory-integrating-applications.md).

##<a name="related-articles"></a>Artículos relacionados

- [Habilitar acceso remoto seguro a aplicaciones locales con Azure Proxy de aplicación de AD](active-directory-application-proxy-get-started.md)
- [Vista previa de Azure acceso condicional para las aplicaciones de SaaS](active-directory-conditional-access-azuread-connected-apps.md)
- [Administrar el acceso a aplicaciones de Azure AD](active-directory-managing-access-to-apps.md)
- [Índice de artículos de administración de aplicaciones de Azure Active Directory](active-directory-apps-index.md)
