<properties
    pageTitle="información general del extremo de versión 2.0 | Microsoft Azure"
    description="Introducción a la creación de aplicaciones Microsoft Account y Azure Active Directory de iniciar sesión."
    services="active-directory"
    documentationCenter=""
    authors="dstrockis"
    manager="mbaldwin"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/27/2016"
    ms.author="dastrock"/>

# <a name="sign-in-microsoft-account--azure-ad-users-in-a-single-app"></a>Inicio de sesión de Microsoft Account & Azure AD usuarios en una sola aplicación

En el pasado, un desarrollador de la aplicación que desea admitir cuentas de Microsoft y Azure Active Directory se debe integrar con dos sistemas independientes.  Ahora hemos introducido una nueva versión de API de autenticación que permite a los usuarios con ambos tipos de cuentas con el sistema de Azure AD de inicio de sesión.  Este sistema de autenticación convergentes se conoce como **el extremo de la versión 2.0**.  Con el extremo de la versión 2.0, una sencilla integración le permite llegar a una audiencia que se extiende por millones de usuarios con cuentas personales y de trabajo o la escuela.

Aplicaciones que utilicen el extremo de la versión 2.0 también pueden usar las API de REST desde el [Gráfico de Microsoft](https://graph.microsoft.io) y [Office 365](https://msdn.microsoft.com/office/office365/howto/authenticate-Office-365-APIs-using-v2) mediante cualquier tipo de cuenta.

<!-- For a quick introduction to the v2.0 endpoint, please view the [Getting Started with Microsoft Identities: Enterprise Grade Sign In For Your Apps](https://azure.microsoft.com/documentation/videos/build-2016-getting-started-with-microsoft-identities-enterprise-grade-sign-in-for-your-apps/) video. -->

## <a name="getting-started"></a>Introducción
[AZURE.VIDEO build-2016-getting-started-with-microsoft-identities-enterprise-grade-sign-in-for-your-apps]

Elija su plataforma favorita de la siguiente lista para crear una aplicación con nuestro bibliotecas de código abierto y marcos.  Como alternativa, puede usar la documentación de protocolo OAuth 2.0 & OpenID conectar a enviar y recibir mensajes de protocolo directamente sin usar una biblioteca de auth.

<!-- TODO: Finalize this table  -->
[AZURE.INCLUDE [active-directory-v2-quickstart-table](../../includes/active-directory-v2-quickstart-table.md)]

## <a name="whats-new"></a>¿Qué novedades
La información conceptual aquí será útil entender qué es y qué no es posible con el extremo de la versión 2.0.

- Obtenga información sobre los [tipos de aplicaciones que se pueden generar con el extremo de la versión 2.0](active-directory-v2-flows.md).
- Comprender las [limitaciones, las restricciones y restricciones](active-directory-v2-limitations.md) con el extremo de la versión 2.0.
- Hemos agregado recientemente compatibilidad para [administración restringido ámbitos](active-directory-v2-scopes.md) y el [cliente de OAuth2 concesión credenciales](active-directory-v2-protocols-oauth-client-creds.md).  ¡Probarlo!

## <a name="reference"></a>Referencia
Estos vínculos le será de utilidad para explorar la plataforma en profundidad:

- Generar 2016: [Introducción a las identidades de Microsoft: Enterprise calificaciones inicio de sesión para las aplicaciones](https://azure.microsoft.com/documentation/videos/build-2016-getting-started-with-microsoft-identities-enterprise-grade-sign-in-for-your-apps/)
- Obtener ayuda sobre el uso del [azure active directory](http://stackoverflow.com/questions/tagged/azure-active-directory) de desbordamiento de pila o [adal](http://stackoverflow.com/questions/tagged/adal) etiquetas.
- [Referencia de protocolo versión 2.0](active-directory-v2-protocols.md)
- [Referencia de Token de versión 2.0](active-directory-v2-tokens.md)
- [Referencia de la biblioteca de versión 2.0](active-directory-v2-libraries.md)
- [Ámbitos y consentimiento del extremo de la versión 2.0](active-directory-v2-scopes.md)
- [El Portal de registro de la aplicación de Microsoft](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList)
- [Referencia de API de REST de Office 365](https://msdn.microsoft.com/office/office365/howto/authenticate-Office-365-APIs-using-v2)
- [Microsoft Graph](https://graph.microsoft.io)