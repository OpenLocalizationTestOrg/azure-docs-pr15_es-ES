<properties
    pageTitle="Uso compartido de cuentas con Azure AD |  Microsoft Azure"
    description="Describe cómo Azure Active Directory permite a las organizaciones compartir de forma segura cuentas para local aplicaciones y servicios de nube de consumidores."
    services="active-directory"
    documentationCenter=""
    authors="msStevenPo"
    manager="femila"
    editor=""/>

 <tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="02/09/2016"  
    ms.author="stevenpo"/>

# <a name="sharing-accounts-with-azure-ad"></a>Uso compartido de cuentas con Azure AD

## <a name="overview"></a>Información general
A veces las empresas deben utilizar un único usuario y una contraseña para varias personas. Normalmente, esto sucede en dos casos:

- Al tener acceso a las aplicaciones que requieren un nombre y una contraseña de inicio de sesión único para cada usuario, si aplicaciones local o consumidor de servicios de nube (por ejemplo, cuentas de medios sociales corporativa).
- Al crear entornos de varios usuarios. Puede tener una sola cuenta local que tiene privilegios elevados y puede utilizarse para principales actividades de instalación, administración y recuperación (por ejemplo, la cuenta "administrador global" local para Office 365) o la cuenta de raíz de Salesforce.

Tradicionalmente, estas cuentas ¿pueden compartir la distribución de las credenciales (nombre de usuario y contraseña) a las personas adecuadas o almacenarlos en una ubicación compartida donde varios agentes de confianza pueden tener acceso a ellos.

El modelo de uso compartido tradicional tiene varios inconvenientes:

- Habilitar el acceso a las nuevas aplicaciones, debe distribuir las credenciales para todos los usuarios que necesita obtener acceso.
- Cada aplicación compartida puede requerir su propio conjunto único de credenciales compartidas, requerir que los usuarios a recordar varios conjuntos de credenciales. Cuando los usuarios tienen que recordar muchas credenciales, aumenta el riesgo de que se utilizar prácticas arriesgadas. (por ejemplo, escribirlas).
- No se puede saber quién tiene acceso a la aplicación.
- No se puede saber quién tiene *acceso a* una aplicación.
- Cuando necesite quitar el acceso a la aplicación, debe actualizar las credenciales y volver a los distribuir a todos los usuarios que necesita obtener acceso a esa aplicación.

## <a name="azure-active-directory-account-sharing"></a>Compartir cuentas de Azure Active Directory

Azure AD proporciona un nuevo método al uso compartidas cuentas que elimina estos inconvenientes.

El Administrador de Azure AD configura las aplicaciones que puede tener acceso un usuario mediante el Panel de acceso y elegir el tipo de mejor de inicio de sesión único adecuado para la aplicación. Uno de esos tipos, *basado en la contraseña de inicio de sesión único en*, le permite Azure AD actuar como un tipo de "intermediario" durante el proceso de inicio de sesión de dicha aplicación.

Los usuarios inician sesión en una vez con su cuenta de la organización. Esta es la misma cuenta que utilizan habitualmente para tener acceso a su escritorio o un correo electrónico. Pueden detectar y tener acceso a solo las aplicaciones que están asignados. Con cuentas compartidas, esta lista de aplicaciones puede incluir cualquier número de credenciales compartidas. El usuario final no necesita recordar o anote las varias cuentas que puede utilizar.

Cuentas compartidas no solo aumentan su supervisión y mejoran el uso, también mejoran la seguridad. Los usuarios con permisos para usar las credenciales no vean la contraseña compartida, pero prefiere obtienen los permisos para usar la contraseña como parte de un flujo de autenticación organizado. Además, con algunas aplicaciones de SSO contraseña, tiene la opción tener Azure AD periódicamente conversión (actualización) la contraseña mediante grandes y complejas contraseñas, aumentar la seguridad de la cuenta. El administrador puede fácilmente conceder o revocar el acceso a la aplicación y también saber quién tiene acceso a la cuenta y quién tiene acceso a él en el pasado.

Azure AD admite cuentas compartidas para cualquier conjunto de movilidad de empresa (EMS), Premium o los usuarios con licencia básicos en todos los tipos de contraseña única, inicie sesión en aplicaciones. Puede compartir cuentas por cualquiera de las miles de aplicaciones integradas previamente en la Galería de la aplicación y puede agregar su propia aplicación de autenticación de contraseña con [aplicaciones SSO personalizadas](active-directory-sso-integrate-saas-apps.md).

Azure AD que habilitar el uso compartido de la cuenta incluye:

- [Contraseña de inicio de sesión único](active-directory-appssoaccess-whatis.md#password-based-single-sign-on)
- Agente de inicio de sesión único de contraseña
- [Asignación a un grupo](active-directory-accessmanagement-self-service-group-management.md)
- Aplicaciones de contraseña personalizada
- [Informes y paneles de uso de aplicación](active-directory-passwords-get-insights.md)
- Portales de acceso de usuario final
- [Proxy de aplicación](active-directory-application-proxy-get-started.md)
- [Catálogo de soluciones de Active Directory](https://azure.microsoft.com/marketplace/active-directory/all/)

## <a name="sharing-an-account"></a>Uso compartido de una cuenta
Usar Azure AD para compartir una cuenta que debe:

- Agregar una aplicación [Galería de aplicación](https://azure.microsoft.com/marketplace/active-directory/) o una [aplicación personalizada](http://blogs.technet.com/b/ad/archive/2015/06/17/bring-your-own-app-with-azure-ad-self-service-saml-configuration-gt-now-in-preview.aspx)
- Configurar la aplicación de la contraseña de inicio de sesión único (SSO)
- Usar [asignación basada en el grupo](active-directory-accessmanagement-group-saasapps.md) y seleccione la opción de introducir una credencial compartida
- Opcional: en algunas aplicaciones, como Facebook, Twitter o LinkedIn, puede habilitar la opción de [contraseña automatizado de Azure AD con](http://blogs.technet.com/b/ad/archive/2015/02/20/azure-ad-automated-password-roll-over-for-facebook-twitter-and-linkedin-now-in-preview.aspx)

También puede hacer que su cuenta compartida sea más segura con la autenticación multifactor (AMF) (más información sobre [cómo proteger las aplicaciones con Azure AD](../multi-factor-authentication/multi-factor-authentication-get-started.md)) y puede delegar la capacidad para administrar quién tiene acceso a la aplicación con [Azure AD sin intervención del administrador](active-directory-accessmanagement-self-service-group-management.md) de grupo de administración.

## <a name="related-articles"></a>Artículos relacionados

- [Índice de artículos de administración de aplicaciones de Azure Active Directory](active-directory-apps-index.md)
- [Proteger aplicaciones con acceso condicional](active-directory-conditional-access.md)
- [Grupo de autoservicio administración/SSAA](active-directory-accessmanagement-self-service-group-management.md)
