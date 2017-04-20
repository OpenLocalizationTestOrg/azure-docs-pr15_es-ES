<properties
   pageTitle="Cómo obtener AppSource certificado para Azure Active Directory | Microsoft Azure"
   description="Detalles sobre cómo obtener la aplicación AppSource certificado para Azure Active Directory."
   services="active-directory"
   documentationCenter=""
   authors="skwan"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="10/28/2016"
   ms.author="skwan;bryanla"/>

#<a name="how-to-get-appsource-certified-for-azure-active-directory-ad"></a>Cómo obtener AppSource certificados para Azure Active Directory (AD) 

Para recibir AppSource certificación de Azure AD, la aplicación debe implementar el inicio de sesión de varios inquilino de trama con Azure AD mediante los protocolos OpenID conectarse, OAuth 2.0 o SAML 2.0. 

Si no está familiarizado con inicio de sesión de Azure AD o desarrollo de aplicaciones de múltiples usuarios:

1. Empiece por lectura acerca del [explorador para escenarios de Web App en escenarios de autenticación para Azure AD][AAD-Auth-Scenarios-Browser-To-WebApp].  
2. A continuación, consulte la Azure AD [guías de inicio rápido aplicación web][AAD-QuickStart-Web-Apps], que muestran cómo implementar el inicio de sesión e incluyen companion ejemplos de código. 

    > [AZURE.TIP] Pruebe la vista previa de nuestro nuevo [portal para desarrolladores](https://identity.microsoft.com/Docs/Web) que le ayudarán a ponerse en marcha con Azure Active Directory en tan solo unos minutos.  El portal de programadores lo guiará por el proceso de registrar una aplicación y la integración de Azure AD en el código.  Cuando haya terminado, tendrá una aplicación simple que puede autenticar a los usuarios de su inquilino y un back-end que pueden aceptar tokens y realizar una validación.

3. Para obtener información sobre cómo implementar el modelo de inicio de sesión de múltiples arrendatario con Azure AD, consulte [cómo iniciar sesión en cualquier usuario de Azure Active Directory (AD) mediante el modelo de aplicación de múltiples arrendatarios][AAD-Howto-Multitenant-Overview]

## <a name="related-content"></a>Contenido relacionado
Para obtener más información sobre la creación de aplicaciones que admiten Azure AD sesión o para obtener ayuda y soporte técnico, consulte la [Guía del desarrollador de Azure AD][AAD-Dev-Guide].

Utilice la sección de comentarios de Disqus instrucciones de este artículo para proporcionar comentarios y ayudar a perfeccionar y dar forma a nuestro contenido.

<!--Reference style links -->
[AAD-Auth-Scenarios]: ./active-directory-authentication-scenarios.md
[AAD-Auth-Scenarios-Browser-To-WebApp]: ./active-directory-authentication-scenarios.md#web-browser-to-web-application
[AAD-Dev-Guide]: ./active-directory-developers-guide.md
[AAD-Howto-Multitenant-Overview]: ./active-directory-devhowto-multi-tenant-overview.md
[AAD-QuickStart-Web-Apps]: ./active-directory-developers-guide.md#web-application-quick-start-guides


<!--Image references-->










