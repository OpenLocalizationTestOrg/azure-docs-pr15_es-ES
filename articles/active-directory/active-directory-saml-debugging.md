<properties 
    pageTitle="Cómo depurar basada en SAML inicio de sesión único para aplicaciones de Azure Active Directory | Microsoft Azure" 
    description="Obtenga información sobre cómo depurar basada en SAML inicio de sesión único para aplicaciones de Azure Active Directory " 
    services="active-directory" 
    authors="asmalser-msft"  
    documentationCenter="na" manager="femila"/>
<tags 
    ms.service="active-directory" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="identity" 
    ms.date="02/09/2016" 
    ms.author="asmalser" />

#<a name="how-to-debug-saml-based-single-sign-on-to-applications-in-azure-active-directory"></a>Cómo depurar basada en SAML inicio de sesión único para aplicaciones de Azure Active Directory

Al depurar la integración de una aplicación basada en SAML, suele ser útil usar una herramienta como [Fiddler](http://www.telerik.com/fiddler) para ver la solicitud SAML, la respuesta SAML y el token SAML real que se envía a la aplicación. Examinar el token de SAML, puede asegurarse de que todos los atributos necesarios, el nombre de usuario en el asunto SAML y el emisor URI proceden según lo esperado.

![][1]

La respuesta de Azure AD que contiene el token SAML normalmente es el que aparece después de un HTTP 302 desviar desde https://login.windows.net y se envía a la **Dirección URL de respuesta** de configurado de la aplicación. 
 
Puede ver el token de SAML seleccionando esta línea y, a continuación, seleccione la **inspectores > formularios Web** ficha en el panel derecho. Desde allí, haga clic en el valor de **SAMLResponse** y seleccione **Enviar a TextWizard**. A continuación, seleccione **Desde la base 64** en el menú **transformar** descodificar el token y ver su contenido.
 
**Nota**: para ver el contenido de esta solicitud HTTP, Fiddler puede pedirle que configure descifrado de tráfico HTTPS, lo que debe hacer.

## <a name="related-articles"></a>Artículos relacionados

- [Índice de artículos de administración de aplicaciones de Azure Active Directory](active-directory-apps-index.md)
- [Configuración de inicio de sesión único en aplicaciones que no están en la Galería de la aplicación de Azure Active Directory](active-directory-saas-custom-apps.md)
- [Cómo personalizar notificaciones emitidas el token SAML aplicaciones integradas previamente](active-directory-saml-claims-customization.md)

<!--Image references-->
[1]: ./media/active-directory-saml-debugging/fiddler.png
