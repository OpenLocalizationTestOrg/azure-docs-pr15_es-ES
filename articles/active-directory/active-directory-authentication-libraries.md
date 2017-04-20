<properties
   pageTitle="Bibliotecas de autenticación de Azure Active Directory | Microsoft Azure"
   description="La biblioteca de autenticación de Azure AD (ADAL) permite a los desarrolladores de la aplicación fácilmente autenticar a los usuarios a la nube de cliente o Active Directory (AD) local y, a continuación, obtenga tokens de acceso para proteger llamadas a la API."
   services="active-directory"
   documentationCenter=""
   authors="bryanla"
   manager="mbaldwin"
   editor="mbaldwin" />
<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="10/11/2016"
   ms.author="mbaldwin" />

# <a name="azure-active-directory-authentication-libraries"></a>Bibliotecas de autenticación de Azure Active Directory

La autenticación de Azure AD biblioteca (ADAL) permite a los desarrolladores de aplicaciones de cliente autenticar fácilmente a los usuarios a la nube o Active Directory (AD) local y, a continuación, obtenga tokens de acceso para proteger llamadas a la API. ADAL tiene muchas características que realizar la autenticación sea más fácil para los desarrolladores, como la compatibilidad asincrónica, una caché de token configurable que almacena los símbolos de acceso y tokens de actualización, actualización automática de token cuando expira un token de acceso y está disponible, un símbolo de actualización y mucho más. Controlando la mayor parte de la complejidad, ADAL puede centrarse en la lógica empresarial en su aplicación programador y proteger fácilmente recursos sin ser un experto en seguridad.

ADAL está disponible en una amplia variedad de plataformas.

|Plataforma|Nombre del paquete|Cliente/servidor|Descargar|Código fuente|Documentación y ejemplos|
|---|---|---|---|---|---|
|Cliente .NET, de la tienda Windows, UWP Xamarin iOS y Android|Active Directory Authentication Library (ADAL) para .NET v3 |Cliente|[Microsoft.IdentityModel.Clients.ActiveDirectory (NuGet)](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory)|[ADAL para .NET (Github)](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet)|[Documentación](https://docs.microsoft.com/active-directory/adal/microsoft.identitymodel.clients.activedirectory)|
|.NET cliente, tienda de Windows, Windows Phone 8.1 |Active Directory Authentication Library (ADAL) para .NET v2 |Cliente|[Microsoft.IdentityModel.Clients.ActiveDirectory (NuGet)](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/2.28.2)|[ADAL para .NET (Github)](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/releases/tag/v2.28.2)|[Documentación](https://docs.microsoft.com/active-directory/adal/v2/microsoft.identitymodel.clients.activedirectory)|
|JavaScript|Active Directory Authentication Library (ADAL) para JavaScript|Cliente|[ADAL para JavaScript (Github)](https://github.com/AzureAD/azure-activedirectory-library-for-js)|[ADAL para JavaScript (Github)](https://github.com/AzureAD/azure-activedirectory-library-for-js)|Ejemplo: [SinglePageApp-DotNet (Github)](https://github.com/AzureADSamples/SinglePageApp-DotNet)|
|OS X, iOS|Active Directory Authentication Library (ADAL) de objetivo-C|Cliente|[ADAL de objetivo-C (CocoaPods)](http://cocoadocs.org/docsets/ADAL/)|[ADAL de objetivo-C (Github)](https://github.com/AzureAD/azure-activedirectory-library-for-objc)|Ejemplo: [NativeClient-iOS (Github)](https://github.com/AzureADSamples/NativeClient-iOS)|
|Android|Active Directory Authentication Library (ADAL) para Android|Cliente|[ADAL para Android (repositorio Central)](http://search.maven.org/remotecontent?filepath=com/microsoft/aad/adal/)|[ADAL para Android (Github)](https://github.com/AzureAD/azure-activedirectory-library-for-android)|Ejemplo: [NativeClient Android (Github)](https://github.com/AzureADSamples/NativeClient-Android)|
|Node.js|Active Directory Authentication Library (ADAL) para Node.js|Cliente|[ADAL para Node.js (npm)](https://www.npmjs.com/package/adal-node)|[ADAL para Node.js (Github)](https://github.com/AzureAD/azure-activedirectory-library-for-nodejs)|Ejemplo: [WebAPI-Nodejs (Github)](https://github.com/AzureADSamples/WebAPI-Nodejs)|
|Node.js|Software intermedio de autenticación de Azure Active Directory MSN para nodo|Cliente|[Azure Passport directorio activo para Node.js (npm)](https://www.npmjs.com/package/passport-azure-ad)|[Azure Active Directory para Node.js (Github)](https://github.com/AzureAD/passport-azure-ad)||
|Java|Active Directory Authentication Library (ADAL) para Java|Cliente|[ADAL para Java (Github)](https://github.com/AzureAD/azure-activedirectory-library-for-java)|[ADAL para Java (Github)](https://github.com/AzureAD/azure-activedirectory-library-for-java)||
|.NET|Extensiones de protocolo de identidad para Microsoft .NET Framework 4.5|Servidor|[Microsoft.IdentityModel.Protocol.Extensions (NuGet)](https://www.nuget.org/packages/Microsoft.IdentityModel.Protocol.Extensions)|[Extensiones de modelo de identidad de Azure AD para .NET (Github)](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet)||
|.NET|Controlador de Token Web JSON para Microsoft .net Framework 4.5|Servidor|[System.IdentityModel.Tokens.Jwt (NuGet)](https://www.nuget.org/packages/System.IdentityModel.Tokens.Jwt)|[Extensiones de modelo de identidad de Azure AD para .NET (Github)](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet)||
|.NET|Software intermedio OWIN que permite a una aplicación para utilizar la tecnología de Microsoft para la autenticación.|Servidor|[Microsoft.Owin.Security.ActiveDirectory (NuGet)](https://www.nuget.org/packages/Microsoft.Owin.Security.ActiveDirectory/)|[OWIN (CodePlex)](http://katanaproject.codeplex.com)||
|.NET|Software intermedio OWIN que permite a una aplicación para utilizar OpenIDConnect para la autenticación.|Servidor|[Microsoft.Owin.Security.OpenIdConnect (NuGet)](https://www.nuget.org/packages/Microsoft.Owin.Security.OpenIdConnect)|[OWIN (CodePlex)](http://katanaproject.codeplex.com)|Ejemplo: [DotNet de OpenIDConnecty de Web App (Github)](https://github.com/AzureADSamples/WebApp-OpenIDConnect-DotNet)|
|.NET|Software intermedio OWIN que permite a una aplicación para utilizar WS-Federation para la autenticación.|Servidor|[Microsoft.Owin.Security.WsFederation (NuGet)](https://www.nuget.org/packages/Microsoft.Owin.Security.WsFederation)|[OWIN (CodePlex)](http://katanaproject.codeplex.com)|Ejemplo: [DotNet de WSFederation de Web App (Github)](https://github.com/AzureADSamples/WebApp-WSFederation-DotNet)|

## <a name="scenarios"></a>Escenarios

Hay tres situaciones habituales en que se puede utilizar ADAL para la autenticación.  

### <a name="authenticating-users-of-a-client-application-to-a-remote-resource"></a>Autenticación de usuarios de una aplicación de cliente a un recurso remoto

En este escenario, un desarrollador tiene un cliente, como una aplicación de WPF que necesita tener acceso a un recurso remoto protegido por Azure AD, como un sitio web API. Ha una suscripción de Azure, sabe cómo invocar la API de web descendentes y sabe al inquilino de Azure AD que usa la API de web. Como resultado, puede utilizar ADAL para facilitar la autenticación con Azure AD, totalmente delegar la experiencia de autenticación para ADAL o controlando explícitamente las credenciales de usuario. ADAL hace fácil autenticar al usuario, obtener un token de acceso y el token de actualización de Azure AD y, a continuación, usar el token de acceso para hacer solicita a la API de web.

Un ejemplo de código que se muestra este escenario mediante la autenticación de Azure AD, vea [Nativa aplicación a cliente WPF API de Web](https://github.com/azureadsamples/nativeclient-dotnet).

### <a name="authenticating-a-server-application-to-a-remote-resource"></a>Autenticación de una aplicación de servidor a un recurso remoto

En este escenario, un desarrollador tiene una aplicación que se ejecuta en un servidor que necesita tener acceso a un recurso remoto protegido por Azure AD, como un sitio web API. Ha una suscripción de Azure, sabe cómo invocar al servicio inferior y sabe que usa el inquilino de Azure AD la API de web. Como resultado, puede utilizar ADAL para facilitar la autenticación con Azure AD controlando explícitamente las credenciales de la aplicación. ADAL hace fácil de recuperar un token de Azure AD mediante credenciales de cliente de la aplicación y, a continuación, usar ese token para hacer solicitudes a la API de web. ADAL también trata la administración de la duración del token de acceso de almacenarla y renovando según sea necesario. Un ejemplo de código que se muestra en este escenario, vea [Aplicación de consola de Web API](https://github.com/AzureADSamples/Daemon-DotNet).

### <a name="authenticating-a-server-application-on-behalf-of-a-user-to-access-a-remote-resource"></a>Autenticación de una aplicación de servidor en nombre de un usuario para tener acceso a un recurso remoto

En este escenario, un desarrollador tiene una aplicación que se ejecuta en un servidor que necesita tener acceso a un recurso remoto protegido por Azure AD, como un sitio web API. La solicitud también debe realizarse en el nombre de un usuario en Azure AD. Ha una suscripción de Azure, sabe cómo invocar la API de web descendente y sabe Azure AD utiliza el servicio de inquilinos. Una vez que el usuario se autentica a la aplicación web, la aplicación puede obtener un código de autorización para el usuario de Azure AD. La aplicación web, a continuación, puede usar ADAL para obtener un token de acceso y actualizar el token en nombre de un usuario con las credenciales de cliente y código de autorización asociadas a la aplicación de Azure AD. Una vez en poder del token de acceso de la aplicación web, puede llamar a la API de web hasta que expire el token. Cuando expira el token, la aplicación web puede usar ADAL para obtener un nuevo token de acceso mediante el uso de la actualización token que recibió anteriormente.


## <a name="see-also"></a>Vea también

[Guía del desarrollador de Azure Active Directory](active-directory-developers-guide.md)

[Escenarios de autenticación de Azure Active directory](active-directory-authentication-scenarios.md)

[Ejemplos de código de Azure Active Directory](active-directory-code-samples.md)
