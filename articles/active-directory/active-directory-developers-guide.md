<properties
   pageTitle="Guía del desarrollador de Azure Active Directory | Microsoft Azure"
   description="Este artículo proporciona a una guía completa a recursos de desarrollador de Azure Active Directory."
   services="active-directory"
   documentationCenter="dev-center-name"
   authors="bryanla"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="10/24/2016"
   ms.author="mbaldwin"/>


# <a name="azure-active-directory-developers-guide"></a>Guía del desarrollador de Azure Active Directory

## <a name="overview"></a>Información general
Como una administración de identidades como una plataforma de servicio (IDMaaS), Azure Active Directory (AD) proporciona a los desarrolladores integrar la administración de identidades en las aplicaciones de un modo eficaz. Los artículos siguientes proporcionan información general de implementación y las características claves de Azure AD. Le recomendamos que lea en orden o saltar a [Introducción](#getting-started) si está listo para profundizar.


1. [Las ventajas de integración de Azure AD](./develop/active-directory-how-to-integrate.md): descubra por qué la integración con Azure AD ofrece la mejor solución para el inicio de sesión seguro y la autorización.

1. [Escenarios de autenticación de Azure AD](active-directory-authentication-scenarios.md): aprovechar las ventajas de autenticación simplificada en Azure AD para proporcionar inicio de sesión en la aplicación.

1. [Integración de aplicaciones de Azure AD](active-directory-integrating-applications.md): Obtenga información sobre cómo agregar, actualizar y eliminar aplicaciones de Azure AD y acerca de las directrices de personalización de marca para las aplicaciones integradas.

1. [API de Azure AD Graph](active-directory-graph-api.md): utilizar la API de Azure AD Graph acceso mediante programación a Azure AD a través de extremos de la API de REST. La API de Azure AD gráfico también está accesible a través de [Microsoft Graph](https://graph.microsoft.io/). Microsoft Graph proporciona una API unificada que permite el acceso a varias servicio de nube de Microsoft a través de un extremo de la API de REST único y con un token de acceso único API.

1. [Bibliotecas de autenticación de Azure AD](active-directory-authentication-libraries.md): fácilmente autenticar a los usuarios para obtener acceso tokens mediante las bibliotecas de autenticación de Azure AD para .NET, JavaScript, C objetivo, Android y mucho más.


## <a name="getting-started"></a>Introducción

Estos tutoriales están diseñados para varias plataformas y pueden ayudarle a comenzar rápidamente a desarrollar con Azure Active Directory. Como requisito previo, debe [obtener a un inquilino de Azure Active Directory](active-directory-howto-tenant.md).

### <a name="mobile-and-pc-application-quick-start-guides"></a>Guías de inicio rápido de aplicación móvil y el equipo

|[![iOS](./media/active-directory-developers-guide/ios.png)](active-directory-devquickstarts-ios.md)|[![Android](./media/active-directory-developers-guide/android.png)](active-directory-devquickstarts-android.md)|[![.NET](./media/active-directory-developers-guide/net.png)](active-directory-devquickstarts-dotnet.md)|[![Universal de Windows](./media/active-directory-developers-guide/windows.png)](active-directory-devquickstarts-windowsstore.md)|[![Xamarin](./media/active-directory-developers-guide/xamarin.png)](active-directory-devquickstarts-xamarin.md)|[![Cordova](./media/active-directory-developers-guide/cordova.png)](active-directory-devquickstarts-cordova.md)|[![OAuth 2.0](./media/active-directory-developers-guide/oauth-2.png)](active-directory-protocols-oauth-code.md)
|:--:|:--:|:--:|:--:|:--:|:--:|:--:|:--:|
|[iOS](active-directory-devquickstarts-ios.md)|[Android](active-directory-devquickstarts-android.md)|[.NET](active-directory-devquickstarts-dotnet.md)|[Universal de Windows](active-directory-devquickstarts-windowsstore.md)|[Xamarin](active-directory-devquickstarts-xamarin.md)|[Cordova](active-directory-devquickstarts-cordova.md)|[Integrar directamente con OAuth 2.0](active-directory-protocols-oauth-code.md)|

### <a name="web-application-quick-start-guides"></a>Guías de inicio rápido de aplicación Web

|[![.NET](./media/active-directory-developers-guide/net.png)](active-directory-devquickstarts-webapp-dotnet.md)|[![Java](./media/active-directory-developers-guide/java.png)](active-directory-devquickstarts-webapp-java.md)|[![AngularJS](./media/active-directory-developers-guide/angularjs.png)](active-directory-devquickstarts-angular.md)|[![JavaScript](./media/active-directory-developers-guide/javascript.png)](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi)|[![Node.js](./media/active-directory-developers-guide/nodejs.png)](active-directory-devquickstarts-openidconnect-nodejs.md) | [![Conectar OpenID](./media/active-directory-developers-guide/openid-connect.png)](active-directory-protocols-openid-connect-code.md)
|:--:|:--:|:--:|:--:|:--:|:--:|
|[.NET](active-directory-devquickstarts-webapp-dotnet.md)|[Java](active-directory-devquickstarts-webapp-java.md)|[AngularJS](active-directory-devquickstarts-angular.md)|[JavaScript](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi)|[Node.js](active-directory-devquickstarts-openidconnect-nodejs.md)|[Integrar directamente con OpenID conectar](active-directory-protocols-openid-connect-code.md)|

### <a name="web-api-quick-start-guides"></a>Guías de inicio rápido de Web API

|[![.NET](./media/active-directory-developers-guide/net.png)](active-directory-devquickstarts-webapi-dotnet.md)|[![Node.js](./media/active-directory-developers-guide/nodejs.png)](active-directory-devquickstarts-webapi-nodejs.md)
|:--:|:--:|
|[.NET](active-directory-devquickstarts-webapi-dotnet.md)|[Node.js](active-directory-devquickstarts-webapi-nodejs.md)

### <a name="querying-the-directory-quickstart-guide"></a>Consultar a la Guía de inicio rápido de directorio

| [![.NET](./media/active-directory-developers-guide/graph.png)](active-directory-graph-api-quickstart.md)|
|:--:|
|[API de gráfico](active-directory-graph-api-quickstart.md)|

## <a name="how-tos"></a>Procedimientos

Estos artículos describe cómo llevar a cabo tareas concretas mediante Azure Active Directory:

- [Obtener a un inquilino de Azure AD](active-directory-howto-tenant.md)
- [Inicie sesión en cualquier usuario de Azure AD utilizando el modelo de aplicación de múltiples arrendatarios](active-directory-devhowto-multi-tenant-overview.md)
- Habilitar SSO entre aplicación con ADAL, [Android](active-directory-sso-android.md) y en dispositivos [iOS](active-directory-sso-ios.md)
- [Convertir la aplicación AppSource certificados para Azure AD](active-directory-devhowto-appsource-certified.md)
- [Lista de la aplicación en la Galería de la aplicación de Azure AD](active-directory-app-gallery-listing.md)
- [Enviar Online para Office 365 para el panel de vendedor](https://msdn.microsoft.com/office/office365/howto/submit-web-apps-seller-dashboard)
- [Comprender el manifiesto de aplicación de Azure Active Directory](active-directory-application-manifest.md)
- [Comprender las directrices de personalización de marca para los botones de adquisición de inicio de sesión y aplicación en la aplicación cliente](active-directory-branding-guidelines.md)
- [Vista previa: Cómo crear aplicaciones que inicie sesión los usuarios con cuentas de tanto personales y profesional o educativa](active-directory-appmodel-v2-overview.md)
- [Vista previa: Cómo crear aplicaciones de inicio de sesión y los consumidores de inicio de sesión](../active-directory-b2c/active-directory-b2c-overview.md)
- [Vista previa: configurar la duración de token de Azure AD](active-directory-configurable-token-lifetimes.md) con PowerShell. Vea [las operaciones de directiva](https://msdn.microsoft.com/library/azure/ad/graph/api/policy-operations) y la [entidad de directiva](https://msdn.microsoft.com/library/azure/ad/graph/api/entity-and-complex-type-reference#policy-entity) para obtener más información sobre cómo configurar mediante la API de Azure AD Graph.

## <a name="reference"></a>Referencia

Estos artículos proporcionan una referencia foundation para resto y las API de biblioteca de autenticación, protocolos, errores, ejemplos de código y extremos.  

###  <a name="support"></a>Soporte técnico
- [Preguntas etiquetada](http://stackoverflow.com/questions/tagged/azure-active-directory): soluciones de buscar Azure Active Directory en la pila de desbordamiento mediante la búsqueda de etiquetas [azure active directory](http://stackoverflow.com/questions/tagged/azure-active-directory) y [adal](http://stackoverflow.com/questions/tagged/adal).
- Consulte el [Glosario de Azure AD programador](active-directory-dev-glossary.md) para definiciones de algunos de los términos comunes relacionados con la integración y desarrollo de aplicaciones.

### <a name="code"></a>Código

- [Bibliotecas de código abierto de Azure Active Directory](http://github.com/AzureAD): es la manera más sencilla de Buscar origen de una biblioteca mediante nuestra [lista de biblioteca](active-directory-authentication-libraries.md).

- [Ejemplos de Azure Active Directory](https://github.com/azure-samples?query=active-directory): desplazarse por la lista de ejemplos de la manera más sencilla es utilizando el [índice de ejemplos de código](active-directory-code-samples.md).

- [Active Directory autenticación biblioteca (ADAL) para .NET](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet) - documentación de referencia está disponible para [la última versión principal](https://docs.microsoft.com/active-directory/adal/microsoft.identitymodel.clients.activedirectory) y la [versión principal anterior](https://docs.microsoft.com/active-directory/adal/v2/microsoft.identitymodel.clients.activedirectory).

### <a name="graph-api"></a>API de gráfico

- [Referencia de la API de gráfico](https://msdn.microsoft.com/library/azure/hh974476.aspx): referencia del resto de la API de gráfico de Azure Active Directory. [Ver la experiencia interactiva de referencia de API de gráfico](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog).

- [Ámbitos de permiso de la API de gráfico](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-permission-scopes): OAuth 2.0 ámbitos de permiso que se utilizan para controlar el acceso de una aplicación a los datos de directorio de un inquilino.

### <a name="authentication-and-authorization-protocols"></a>Protocolos de autenticación y la autorización

- [Conversión de clave de firma en Azure AD](active-directory-signing-key-rollover.md): Obtenga más información sobre cómo actualizar la clave para los escenarios más comunes de aplicaciones y la sustitución de la clave de firma de Azure AD.

- [Protocolo OAuth 2.0: mediante la concesión de autorización de código](active-directory-protocols-oauth-code.md): concesión de código de autorización de protocolo OAuth 2.0, puede usar para autorizar el acceso a las aplicaciones Web y las API de Web en Azure Active Directory de inquilinos.

- [Protocolo OAuth 2.0: descripción de la concesión implícita](active-directory-dev-understanding-oauth2-implicit-grant.md): más información sobre la concesión de autorización implícita, y si es adecuado para la aplicación.

- [Protocolo OAuth 2.0: servicio de credenciales de cliente de uso de llamadas de servicio](active-directory-protocols-oauth-service-to-service.md): concesión de las credenciales de cliente de OAuth 2.0 permite a un servicio web (un confidencial cliente) para usar sus propias credenciales para autenticar al llamar a otro servicio web, en lugar de suplantación de un usuario. En este escenario, el cliente normalmente es un servicio web de nivel medio, un servicio demonio o sitio Web.

- [Protocolo OpenID conectar 1.0: inicio de sesión y la autenticación](active-directory-protocols-openid-connect-code.md): el OpenID Connect 1.0 protocolo extiende OAuth 2.0 para usar como protocolo de autenticación. Una aplicación de cliente puede recibir un id_token para administrar el proceso de inicio de sesión o aumentar el flujo de código de autorización para recibir un id_token y la autorización de código.

- [Referencia de protocolo de SAML 2.0](active-directory-saml-protocol-reference.md): protocolo el SAML 2.0 permite que las aplicaciones proporcionar una experiencia de inicio de sesión único a sus usuarios.

- [Protocolo WS-Federation 1.2](http://docs.oasis-open.org/wsfed/federation/v1.2/os/ws-federation-1.2-spec-os.html): Azure Active Directory admite WS-Federation 1.2 según la especificación Web Servicios de federación versión 1.2. Para obtener más información sobre el documento de metadatos de federación, vea [Los metadatos de federación](active-directory-federation-metadata.md).

- [Tipos de token y notificación compatibles](active-directory-token-and-claims.md): puede usar esta guía para comprender y evaluar las notificaciones de los tokens SAML 2.0 y JSON Web Tokens (JWT).

## <a name="videos"></a>Vídeos

### <a name="build"></a>Crear

Estas presentaciones de información general sobre el desarrollo de aplicaciones mediante el uso de los altavoces de la característica de Azure Active Directory que trabajan directamente en el equipo de ingeniería. Las presentaciones tratan temas fundamentales, incluidos IDMaaS, autenticación, la federación de identidades y el inicio de sesión único.

- [Identidad de Microsoft: Estado de la unión y la dirección en el futuro](https://azure.microsoft.com/documentation/videos/build-2016-microsoft-identity-state-of-the-union-and-future-direction/)
- [Azure Active Directory: Administración de identidades como un servicio para las aplicaciones modernas](https://azure.microsoft.com/documentation/videos/build-2015-azure-active-directory-identity-management-as-a-service-for-modern-applications/)
- [Crear aplicaciones web moderna con Azure Active Directory](https://azure.microsoft.com/documentation/videos/build-2015-develop-modern-web-applications-with-azure-active-directory/)
- [Desarrolle aplicaciones nativas moderna con Azure Active Directory](https://azure.microsoft.com/documentation/videos/build-2015-develop-modern-native-applications-with-azure-active-directory/)

### <a name="azure-friday"></a>Azure el viernes
[Azure el viernes](https://azure.microsoft.com/documentation/videos/azure-friday/) es una serie de vídeos de 1:1 está dedicado a lo que breve (10 y 15 minutos) entrevista al viernes con expertos en una gran variedad de temas de Azure periódica.  Use la característica de filtro de servicios en la página para ver todos los vídeos de Azure Active Directory.

- [Identidad Azure 101](https://azure.microsoft.com/documentation/videos/azure-identity-basics/)
- [Identidad Azure 102](https://azure.microsoft.com/documentation/videos/azure-identity-creating-active-directory/)
- [Identidad Azure 103](https://azure.microsoft.com/documentation/videos/azure-identity-application-to-authenticate/)

## <a name="social"></a>Social

- [Blog del equipo de Active Directory](http://blogs.technet.com/b/ad/): Novedades en el mundo de Azure Active Directory.

- [Blog del equipo de Azure Active Directory Graph](http://blogs.msdn.com/b/aadgraphteam): información de Azure Active Directory específica a la API de gráfico.

- [Identidad de la nube](http://www.cloudidentity.net): ideas sobre la administración de identidades como un servicio, desde un principal Azure Active Directory PM.  

- [Azure Active Directory en Twitter](https://twitter.com/azuread): anuncios de Azure Active Directory en 140 caracteres o menos.

## <a name="windows-server-on-premises-development"></a>Desarrollo de Windows Server local
Para obtener instrucciones sobre el uso de desarrollo de Windows Server y servicios de federación de Active Directory (ADFS), consulte:

- [Escenarios de AD FS para desarrolladores](https://technet.microsoft.com/windows-server-docs/identity/ad-fs/overview/ad-fs-scenarios-for-developers): ofrece una descripción general de los componentes de AD FS y cómo funciona, con detalles acerca de los escenarios de autenticación o autorización compatibles.
- [Tutoriales de AD FS](https://technet.microsoft.com/windows-server-docs/identity/ad-fs/ad-fs-development): una lista de artículos de tutorial, que proporcionan instrucciones paso a paso sobre cómo implementar los flujos de autenticación/autorización relacionados.
