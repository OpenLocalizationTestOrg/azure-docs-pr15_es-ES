<properties
    pageTitle="Acceso condicional de Azure Active Directory preguntas más frecuentes | Microsoft Azure"
    description="Preguntas más frecuentes sobre el acceso condicional "
    services="active-directory"
    documentationCenter=""
    authors="MarkusVi"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/20/2016"
    ms.author="markvi"/>

# <a name="azure-active-directory-conditional-access-faq"></a>Acceso condicional de Azure Active Directory preguntas más frecuentes

## <a name="which-applications-work-with-conditional-access-policies"></a>¿Las aplicaciones que funcionan con las directivas de acceso condicional?

**A:** Vea el tema de [las aplicaciones de access qué condicional son compatibles](active-directory-conditional-access-supported-apps.md).

## <a name="are-conditional-access-policies-enforced-for-b2b-collaboration-and-guest-users"></a>¿Se aplican las directivas de acceso condicional para los usuarios invitados y colaboración de B2B?

**A:** Se aplican las directivas para los usuarios de colaboración B2B. Sin embargo, en algunos casos, un usuario podría no pueda cumplir con el requisito de directiva si, por ejemplo, una organización no admite la autenticación multifactor. 

Actualmente, la directiva no es obligatoria para los usuarios invitados de SharePoint. Se mantiene la relación de invitado en SharePoint. Directivas de usuarios invitados cuentas no están sujetos a access en el servidor de autenticación. Puede administrar el acceso de invitado en SharePoint.

## <a name="does-a-sharepoint-online-policy-also-apply-to-onedrive-for-business"></a>¿Una directiva de SharePoint Online también se aplica a OneDrive para la empresa?

**A:** Sí.
 
## <a name="why-cant-i-set-a-policy-on-client-apps-like-word-or-outlook"></a>¿Por qué no puedo establecer una directiva en aplicaciones cliente, como Word o Outlook?

**A:** Una directiva de acceso condicional establece requisitos para tener acceso a un servicio y se aplica cuando la autenticación pasa a dicho servicio. La directiva no se establece directamente en una aplicación de cliente; en su lugar, se aplica cuando llama a un servicio. Por ejemplo, una directiva establecida en SharePoint se aplica a los clientes llamar a SharePoint y una directiva establecida en Exchange a Outlook.


## <a name="does-a-conditional-access-policy-apply-to-service-accounts"></a>¿Una directiva de acceso condicional que se aplica a las cuentas de servicio?

**A:** Las directivas de acceso condicional se aplican a todas las cuentas de usuario. Esto incluye las cuentas de usuario que se usa como cuentas de servicio. En muchos casos, no es capaz de cumplir con una directiva de una cuenta de servicio que se ejecute desatendida. Es, por ejemplo el caso, si es necesario AMF. En estos casos, se pueden excluir cuentas de servicios de una directiva, usando la configuración de administración de directivas de acceso condicional. Obtenga más información sobre cómo aplicar una directiva a estos usuarios.
