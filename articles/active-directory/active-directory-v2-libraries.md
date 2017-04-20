<properties
   pageTitle="Bibliotecas de autenticación de Azure Active Directory versión 2.0 | Microsoft Azure"
   description="Bibliotecas de cliente compatibles y bibliotecas de software intermedio de servidor y biblioteca relacionado, origen y vínculos de muestras, del extremo de la versión 2.0 de Azure Active Directory."
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
   ms.date="09/30/2016"
   ms.author="skwan;bryanla"/>


# <a name="azure-active-directory-v20-authentication-libraries"></a>Bibliotecas de autenticación de Azure Active Directory versión 2.0
El extremo de la versión 2.0 de Azure Active Directory (AD Azure) es compatible con los protocolos de OAuth 2.0 y OpenID Connect 1.0 estándar. Puede usar diversas bibliotecas de Microsoft y otras organizaciones con el extremo de la versión 2.0.

Cuando crea una aplicación que utiliza el extremo de la versión 2.0, le recomendamos que use bibliotecas que se escriben por expertos de dominio de protocolo que sigan una metodología de ciclo de vida de desarrollo de seguridad (SDL), como [el seguido de Microsoft][Microsoft-SDL]. Si decide mano el código de soporte técnico para los protocolos, le recomendamos que siga la metodología SDL y preste atención a las consideraciones de seguridad en las especificaciones de estándares de cada protocolo.

## <a name="types-of-libraries"></a>Tipos de bibliotecas

Versión 2.0 de AD Azure funciona con dos tipos de bibliotecas:

- **Bibliotecas de cliente**. Servidores y clientes nativos usan bibliotecas de cliente para obtener acceso tokens para llamar a un recurso, como Microsoft Graph.
- **Bibliotecas de software intermedio de servidor**. Aplicaciones Web de usar bibliotecas de software intermedio de servidor de inicio de sesión de usuario. API de Web usar bibliotecas de software intermedio de servidor para validar los tokens que se envían mediante clientes nativos o por otros servidores.

## <a name="library-support"></a>Compatibilidad con bibliotecas
Porque puede elegir cualquier biblioteca estándar cuando se usa el extremo de la versión 2.0, es importante saber dónde obtener soporte técnico. Problemas y solicitudes de características en el código de biblioteca, póngase en contacto con el propietario de la biblioteca. Problemas y solicitudes de características en la implementación de protocolo del servicio, póngase en contacto con Microsoft.

Las bibliotecas se dividen en dos categorías de soporte técnico:

- **Soporte de Microsoft**. Microsoft proporciona correcciones de estas bibliotecas y ha hecho SDL diligencia en estas bibliotecas.
- Es **compatible**. Microsoft ha probado estas bibliotecas en escenarios básicos y confirmado que funcionan con el extremo de la versión 2.0. Microsoft no ofrece soluciones para estas bibliotecas y no se ha realizado una revisión de estas bibliotecas. Problemas y solicitudes de características deben dirigirse al proyecto de código abierto de la biblioteca.

Para obtener una lista de bibliotecas que funcionan con el extremo de la versión 2.0, vea las siguientes secciones de este artículo.

## <a name="microsoft-supported-client-libraries"></a>Bibliotecas de cliente compatibles de Microsoft
| Plataforma| Nombre de la biblioteca| Descargar | Código fuente | Ejemplo |
| :-: | :-: | :-: | :-: | :-: |
| Xamarin. NET, la tienda de Windows | Biblioteca de autenticación de Microsoft (MSAL) para .NET | [Microsoft.Identity.Client (NuGet)][ClientLib-NET-Lib] | [MSAL para .NET (GitHub)][ClientLib-NET-Repo] | [Ejemplo de cliente nativo de escritorio de Windows][ClientLib-NET-Sample] |
| Node.js | Complemento de Microsoft Azure Active Directory Passport.js | [ADFS de Azure Passport (npm)][ClientLib-Node-Lib] | [ADFS de Azure Passport (GitHub)][ClientLib-Node-Repo] | Próximamente |

<!--- COMMENTING OUT UNTIL THEY ARE READY
| iOS, Mac | Microsoft Authentication Library (MSAL) for ObjC | In development | In development | In development |
| Android | Microsoft Authentication Library (MSAL) for Android | In development | In development | In development |
| JavaScript | Microsoft Authentication Library (MSAL) for JavaScript | In development | In development | In development |
 -->

## <a name="microsoft-supported-server-middleware-libraries"></a>Bibliotecas de servidor compatible con Microsoft software intermedio
| Plataforma| Nombre de la biblioteca| Descargar | Código fuente | Ejemplo |
| :-: | :-: | :-: | :-: | :-: |
| .NET 4.x | OWIN OpenID conectar software intermedio para ASP.NET | [Microsoft.Owin.Security.OpenIdConnect (NuGet)][ServerLib-Net4-Owin-Oidc-Lib] | [Proyecto Katana (CodePlex)][ServerLib-Net4-Owin-Oidc-Repo] | [Ejemplo de aplicación Web][ServerLib-Net4-Owin-Oidc-Sample] |
| .NET 4.x | Software intermedio de portador de OAuth OWIN de ASP.NET | [Microsoft.Owin.Security.OAuth (NuGet)][ServerLib-Net4-Owin-Oauth-Lib] | [Proyecto Katana (CodePlex)][ServerLib-Net4-Owin-Oauth-Repo] | [Ejemplo de Web API][ServerLib-Net4-Owin-Oauth-Sample] |
| Núcleo de .NET | OWIN OpenID conectar software intermedio para .NET principales | [Microsoft.AspNetCore.Authentication.OpenIdConnect (NuGet)][ServerLib-NetCore-Owin-Oidc-Lib] | [Seguridad de ASP.NET (GitHub)][ServerLib-NetCore-Owin-Oidc-Repo] | [Ejemplo de aplicación Web][ServerLib-NetCore-Owin-Oidc-Sample] |
| Núcleo de .NET | Software intermedio de portador de OAuth OWIN para .NET principales | [Microsoft.AspNetCore.Authentication.OAuth (NuGet)][ServerLib-NetCore-Owin-Oauth-Lib] | [Seguridad de ASP.NET (GitHub)][ServerLib-NetCore-Owin-Oauth-Repo] | Próximamente |
| Node.js | Microsoft Azure Active Directory Passport.js complemento | [ADFS de Azure Passport (npm)][ServerLib-Node-Lib] | [ADFS de Azure Passport (GitHub)][ServerLib-Node-Repo] | [Ejemplo de aplicación Web][ServerLib-Node-Sample] |
<!--- COMMENTING UNTIL SAMPLE IS AVAILABLE
| .NET 4.x, .NET Core | JSON Web Token Handler for .NET | [System.IdentityModel.Tokens.Jwt (NuGet)][ServerLib-Net-Jwt-Lib] | [Azure AD identity model extensions for .NET (GitHub)][ServerLib-Net-Jwt-Repo] | Coming soon |
--->
## <a name="compatible-client-libraries"></a>Bibliotecas de cliente compatibles
| Plataforma| Nombre de la biblioteca | Versión probado | Código fuente | Ejemplo |
| :-: | :-: | :-: | :-: | :-: |
| Android | [OIDCAndroidLib](https://github.com/kalemontes/OIDCAndroidLib/wiki) | 0.2.1 | [OIDCAndroidLib](https://github.com/kalemontes/OIDCAndroidLib) | [Ejemplo de aplicación nativa](active-directory-v2-devquickstarts-android.md) |
| iOS | [NXOAuth2Client](https://github.com/nxtbgthng/OAuth2Client) | 1.2.8 | [NXOAuth2Client](https://github.com/nxtbgthng/OAuth2Client) | [Ejemplo de aplicación nativa](active-directory-v2-devquickstarts-ios.md)|
| JavaScript | [Hello.js](https://adodson.com/hello.js/) | 1.13.5 | [Hello.js](https://github.com/MrSwitch/hello.js) | Próximamente |
| Matraz Python | [Matraz OAuthlib](https://github.com/lepture/flask-oauthlib) | 0.9.3 | [Matraz OAuthlib](https://github.com/lepture/flask-oauthlib) | Próximamente |
| Ruby | [OmniAuth](https://github.com/omniauth/omniauth/wiki) | omniauth:1.3.1</br>omniauth-oauth2:1.4.0 | [OmniAuth](https://github.com/omniauth/omniauth)</br>[OmniAuth OAuth2](https://github.com/intridea/omniauth-oauth2) | Próximamente |
<!--- REMOVING BRANDON'S FOR NOW
|  |  |  |  |  |
| Android | [OAuth2 Client](https://github.com/wuman/android-oauth-client) |   | [OAuth2 Client](https://github.com/wuman/android-oauth-client)  | Coming soon  |
| Java | [WSO2 Identity Server](https://docs.wso2.com/display/IS500/Introducing+the+Identity+Server) | [Version 5.2.0](http://wso2.com/products/identity-server/) | [Source](https://docs.wso2.com/display/IS500/Building+from+Source) | [Samples index](https://docs.wso2.com/display/IS500/Samples)  |
| Java | [Java Gluu Server](https://gluu.org/docs/) |   | [oxAuth](https://github.com/GluuFederation/oxAuth)  | Coming soon |
| Node.js | [NPM passport-openidconnect](https://www.npmjs.com/package/passport-openidconnect) | 0.0.1  | [Passport-OpenID Connect](https://github.com/jaredhanson/passport-openidconnect) | Coming soon  |
| PHP | [OpenID Connect Basic Client](https://github.com/jumbojett/OpenID-Connect-PHP) |   | [OpenID Connect Basic Client](https://github.com/jumbojett/OpenID-Connect-PHP)  | Coming soon  |
-->

## <a name="compatible-server-middleware-libraries"></a>Bibliotecas de software intermedio de servidor compatible
Próximamente

## <a name="related-content"></a>Contenido relacionado
Para obtener más información acerca del extremo de la versión 2.0 de Azure AD, vea la [información general de versión 2.0 del modelo de aplicación de Azure AD][AAD-App-Model-V2-Overview].

Para ayudar a perfeccionar y dar forma a nuestro contenido, use la característica de comentarios de Disqus al final de este artículo para proporcionar comentarios.

<!--Image references-->

<!--Reference style links -->
[AAD-App-Model-V2-Overview]: active-directory-appmodel-v2-overview.md
[ClientLib-NET-Lib]: http://www.nuget.org/packages/Microsoft.Identity.Client
[ClientLib-NET-Repo]: https://github.com/AzureAD/microsoft-authentication-library-for-dotnet
[ClientLib-NET-Sample]: active-directory-v2-devquickstarts-wpf.md
[ClientLib-Node-Lib]: https://www.npmjs.com/package/passport-azure-ad
[ClientLib-Node-Repo]: https://github.com/AzureAD/passport-azure-ad
[ClientLib-Node-Sample]:
[ClientLib-Iosmac-Lib]:
[ClientLib-Iosmac-Repo]:
[ClientLib-Iosmac-Sample]:
[ClientLib-Android-Lib]:
[ClientLib-Android-Repo]:
[ClientLib-Android-Sample]:
[ClientLib-Js-Lib]:
[ClientLib-Js-Repo]:
[ClientLib-Js-Sample]:
[Microsoft-SDL]: http://www.microsoft.com/sdl/default.aspx
[ServerLib-Net4-Owin-Oidc-Lib]: https://www.nuget.org/packages/Microsoft.Owin.Security.OpenIdConnect/
[ServerLib-Net4-Owin-Oidc-Repo]: http://katanaproject.codeplex.com/
[ServerLib-Net4-Owin-Oidc-Sample]: active-directory-v2-devquickstarts-dotnet-web.md
[ServerLib-Net4-Owin-Oauth-Lib]: https://www.nuget.org/packages/Microsoft.Owin.Security.OAuth/
[ServerLib-Net4-Owin-Oauth-Repo]: http://katanaproject.codeplex.com/
[ServerLib-Net4-Owin-Oauth-Sample]: https://azure.microsoft.com/en-us/documentation/articles/active-directory-v2-devquickstarts-dotnet-api/
[ServerLib-Net-Jwt-Lib]: https://www.nuget.org/packages/System.IdentityModel.Tokens.Jwt
[ServerLib-Net-Jwt-Repo]: https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet
[ServerLib-Net-Jwt-Sample]:/
[ServerLib-NetCore-Owin-Oidc-Lib]: https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.OpenIdConnect/
[ServerLib-NetCore-Owin-Oidc-Repo]: https://github.com/aspnet/Security
[ServerLib-NetCore-Owin-Oidc-Sample]: https://github.com/Azure-Samples/active-directory-dotnet-webapp-openidconnect-aspnetcore-v2
[ServerLib-NetCore-Owin-Oauth-Lib]: https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.OAuth/
[ServerLib-NetCore-Owin-Oauth-Repo]: https://github.com/aspnet/Security
[ServerLib-NetCore-Owin-Oauth-Sample]:/
[ServerLib-Node-Lib]: https://www.npmjs.com/package/passport-azure-ad
[ServerLib-Node-Repo]: https://github.com/AzureAD/passport-azure-ad/
[ServerLib-Node-Sample]: https://azure.microsoft.com/en-us/documentation/articles/active-directory-v2-devquickstarts-node-web/
