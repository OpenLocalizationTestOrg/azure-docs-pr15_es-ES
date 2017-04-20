<properties
    pageTitle="Integrar el inicio de sesión único Azure Active Directory con aplicaciones de SaaS |  Microsoft Azure"
    description="Habilitar la autenticación de inicio de sesión único y administración centralizada acceso de aplicaciones de SaaS en Azure Active Directory de aprovisionamiento de usuarios. Información general sobre cómo integrar Azure Active Directory para las aplicaciones SaaS."
    services="active-directory"
      keywords="integrar Azure AD con aplicaciones de SaaS"
    documentationCenter=""
    authors="curtand"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="identity"
    ms.date="09/30/2016"
    ms.author="curtand"/>

# <a name="integrate-azure-active-directory-single-sign-on-with-saas-apps"></a>Integrar el inicio de sesión único Azure Active Directory con aplicaciones de SaaS  

> [AZURE.SELECTOR]
- [Portal de Azure](active-directory-enterprise-apps-manage-sso.md)
- [Portal de clásico de Azure](active-directory-sso-integrate-saas-apps.md)

[AZURE.INCLUDE [active-directory-sso-use-case-intro](../../includes/active-directory-sso-use-case-intro.md)]

Para empezar a configurar el inicio de sesión único para una aplicación que está ponerlos en su organización, va a usar un directorio existente en Azure Active Directory (AD Azure). Puede utilizar un directorio de Azure AD que se obtiene a través de Microsoft Azure, Office 365 o Windows Intune. Si tiene dos o más de estos, consulte [administrar el directorio de Azure AD](active-directory-administer.md) para determinar cuál usar.

## <a name="authentication"></a>Autenticación

Para las aplicaciones que admiten la SAML 2.0, WS-Federation, o conectar OpenID protocolos, usos de Azure Active Directory firma de certificados para establecer relaciones de confianza. Para obtener más información, vea [administrar los certificados para un inicio de sesión único federado](active-directory-sso-certs.md).

Para las aplicaciones que admiten sólo HTML basada en formularios de sesión, Azure Active Directory usa 'reasignación de contraseña' para establecer relaciones de confianza. Esto permite a los usuarios de su organización sesión automáticamente una aplicación de SaaS por Azure AD con la información de cuenta de usuario de la aplicación de SaaS. Azure AD recopila y almacena la información de cuenta de usuario y la contraseña relacionada de forma segura. Para obtener más información, vea [basada en la contraseña de inicio de sesión único](active-directory-appssoaccess-whatis.md#password-based-single-sign-on).

## <a name="authorization"></a>Autorización

Una cuenta de preparación permite a un usuario a estar autorizado para usar una aplicación después de autenticar a través de inicio de sesión único. Aprovisionamiento de usuario puede realizarse manualmente o, en algunos casos, puede agregar y quitar información de usuario de la aplicación de SaaS según los cambios realizados en Azure Active Directory. Para obtener más información sobre el uso de conectores de Azure AD existentes de aprovisionamiento automatizado, vea [automatizado usuario crear y eliminar aplicaciones de SaaS](active-directory-saas-app-provisioning.md).

En caso contrario, manualmente puede agregar información de usuario a una aplicación o usar otras soluciones de aprovisionamiento que están disponibles en el catálogo de soluciones.

## <a name="access"></a>Access

Azure AD proporciona varias maneras personalizables para distribuir aplicaciones a los usuarios finales de su organización. No se bloquean en cualquier implementación particular o solución de acceso. Puede usar [la solución que mejor se adapte a sus necesidades](active-directory-appssoaccess-whatis.md#deploying-azure-ad-integrated-applications-to-users).

## <a name="additional-considerations-for-applications-already-in-use"></a>Consideraciones adicionales para aplicaciones ya en uso

Configurar el inicio de sesión único en para una aplicación que utiliza su organización ya es un proceso diferente del proceso de creación de nuevas cuentas para una nueva aplicación. Hay un par de pasos preliminares incluidos: la asignación de identidades de usuario en la aplicación de las identidades de Azure AD y comprender cómo los usuarios tendrán inicio de sesión en una aplicación después de que está integrado.

> [AZURE.NOTE] Para configurar el SSO para una aplicación existente, debe tener derechos de administrador global en ambos Azure AD y la aplicación de SaaS.

### <a name="mapping-user-accounts"></a>Asignación de cuentas de usuario

Normalmente, identidad de un usuario tiene un identificador único que podría ser una dirección de correo electrónico o nombre principal de usuario (UPN). Vínculo (mapa) debe identidad de aplicación de cada usuario a sus respectivos identidad de Azure AD. Hay un par de maneras para llevar a cabo esta función de cómo el requisito de autenticación de la aplicación.

Para obtener más información sobre la asignación de identidades de la aplicación con las identidades de Azure AD, vea [notificaciones de personalización emitidos en el token de SAML](http://social.technet.microsoft.com/wiki/contents/articles/31257.azure-active-directory-customizing-claims-issued-in-the-saml-token-for-pre-integrated-apps.aspx) y [asignaciones de atributos de personalización de aprovisionamiento](active-directory-saas-customizing-attribute-mappings.md).

### <a name="understanding-the-users-log-in-experience"></a>Descripción de registro del usuario en experiencia

Al realizar la integración de SSO para una aplicación que ya está en uso, es importante tener en cuenta que la experiencia del usuario se verán afectada. Para todas las aplicaciones, los usuarios iniciará con sus credenciales de Azure AD para iniciar sesión. También puede deberse a que se debe utilizar otro portal para obtener acceso a las aplicaciones.

Se puede realizar SSO para algunas aplicaciones de inicio de sesión de la aplicación en la interfaz, pero para otras aplicaciones, el usuario tendrá que ir a través de un portal central como ([Mis aplicaciones](http://myapps.microsoft.com) u [Office 365](http://portal.office.com/myapps)) para iniciar sesión. Más información sobre los distintos tipos de SSO y sus experiencias de usuario en [¿Qué es el acceso a la aplicación e inicio de sesión único con Azure Active Directory](active-directory-appssoaccess-whatis.md).

Otro recurso valiosa es *usuario Suppressing consentimiento* en el artículo de [desarrolladores directrices](active-directory-applications-guiding-developers-for-lob-applications.md) .

## <a name="next-steps"></a>Pasos siguientes


Para las aplicaciones SaaS que encontrará en la Galería de aplicación, Azure AD proporciona una serie de [Tutoriales sobre cómo integrar aplicaciones de SaaS](active-directory-saas-tutorial-list.md).

Si la aplicación no está en la Galería de aplicación, puede [agregarlo a la Galería de aplicaciones de Azure AD como una aplicación personalizada](http://blogs.technet.com/b/ad/archive/2015/06/17/bring-your-own-app-with-azure-ad-self-service-saml-configuration-gt-now-in-preview.aspx).

Hay mucho más detalle en todos estos problemas en la biblioteca Azure.com, comenzando con [¿Qué es el acceso a la aplicación e inicio de sesión único con Azure Active Directory.](active-directory-appssoaccess-whatis.md).

## <a name="see-also"></a>Vea también

- [Índice de artículos de administración de aplicaciones de Azure Active Directory](active-directory-apps-index.md)
