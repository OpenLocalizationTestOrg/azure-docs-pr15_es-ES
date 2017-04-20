<properties
   pageTitle="Ejemplos de código de Azure Active Directory | Microsoft Azure"
   description="Un índice de ejemplos de código de Azure Active Directory, organizados por escenario."
   services="active-directory"
   documentationCenter="dev-center-name"
   authors="priyamohanram"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="09/16/2016"
   ms.author="mbaldwin"/>

# <a name="azure-active-directory-code-samples"></a>Ejemplos de código de Azure Active Directory

[AZURE.INCLUDE [active-directory-devguide](../../includes/active-directory-devguide.md)]

Puede usar Microsoft Azure Active Directory (AD Azure) para agregar la autenticación y la autorización para las aplicaciones web y las API de web. Esta sección proporciona vínculos a ejemplos de código que muestran cómo se hace y fragmentos de código que puede usar en las aplicaciones. En la página de ejemplo de código, encontrará lectura detallada-me temas de ayudarán con los requisitos de instalación y configuración. Y el código es comentado para ayudarle a entender las secciones críticas.

Para conocer el escenario básico para cada tipo de muestra, vea escenarios de autenticación para Azure AD.

Contribuir a nuestros ejemplos sobre GitHub: [documentación y ejemplos de Microsoft Azure Active Directory](https://github.com/Azure-Samples?page=3&query=active-directory).

## <a name="web-browser-to-web-application"></a>Explorador Web para la aplicación Web

Estos ejemplos muestran cómo escribir una aplicación web que dirige el explorador del usuario iniciar sesión en a Azure AD.



| Lenguaje o plataforma | Ejemplo | Descripción
| ----------------- | ------ | -----------
| C# y .NET | [DotNet de OpenIDConnect de Web App](https://github.com/Azure-Samples/active-directory-dotnet-webapp-openidconnect) | Use OpenID conectar (ASP.Net OpenID conectar OWIN software intermedio) para autenticar a los usuarios de un inquilino de Azure AD.
| C# y .NET | [Aplicación Web-MultiTenant-OpenIdConnect-DotNet](https://github.com/Azure-Samples/active-directory-dotnet-webapp-multitenant-openidconnect) | Una aplicación web de MVC .NET que usa OpenID conectar (ASP.Net OpenID conectar OWIN software intermedio) para autenticar a los usuarios de varios inquilinos de Azure AD de inquilinos múltiples.
| C# y .NET | [DotNet de WSFederation de Web App](https://github.com/Azure-Samples/active-directory-dotnet-webapp-wsfederation) | Use WS-Federation (ASP.Net WS-Federation OWIN software intermedio) para autenticar a los usuarios de un inquilino de Azure AD.






## <a name="single-page-application-spa"></a>Aplicación de la página (SPA)

Este ejemplo muestra cómo escribir una aplicación única página protegida con Azure AD.  

| Lenguaje o plataforma | Ejemplo | Descripción
| ----------------- | ------ | -----------
| JavaScript, C# y .NET | [DotNet SinglePageApp](https://github.com/Azure-Samples/active-directory-angularjs-singlepageapp) | Utilizar ADAL para JavaScript y Azure AD para proteger una aplicación basada en AngularJS sola página implementada con un ASP.NET web API back-end.


## <a name="native-application-to-web-api"></a>Aplicación nativa para Web API

Estos ejemplos de código muestran cómo crear aplicaciones cliente nativo que llame a web API que están protegidas por Azure AD. Utilizan [Biblioteca de autenticación de Azure AD (ADAL)](active-directory-authentication-libraries.md) y [OAuth 2.0 en Azure AD](https://msdn.microsoft.com/library/azure/dn645545.aspx).

| Lenguaje o plataforma | Ejemplo | Descripción
| ----------------- | ------ | -----------
| JavaScript | [NativeClient-MultiTarget-Cordova](https://github.com/Azure-Samples/active-directory-cordova-multitarget) | Utilizar el complemento de ADAL para Cordova Apache para crear una aplicación de Apache Cordova que llama a un sitio web API y utiliza Azure AD para la autenticación.
| C# y .NET | [NativeClient DotNet](https://github.com/Azure-Samples/active-directory-dotnet-native-desktop) | Una aplicación de WPF de .NET que llama a un sitio web API que está protegida mediante el uso de Azure AD.
| C# y .NET | [NativeClient WindowsStore](https://github.com/Azure-Samples/active-directory-dotnet-windows-store) | Una aplicación de almacenamiento de Windows que llama a un sitio web API que está protegida con Azure AD.
| C# y .NET | [NativeClient-WebAPI-MultiTenant-WindowsStore](https://github.com/Azure-Samples/active-directory-dotnet-webapi-multitenant-windows-store) | Una aplicación de la tienda Windows llamar a un sitio web de múltiples arrendatario API que está protegido con Azure AD.
| C# y .NET | [WebAPI-OnBehalfOf-DotNet](https://github.com/Azure-Samples/active-directory-dotnet-webapi-onbehalfof) | Una aplicación de cliente nativo que llama a un sitio web API, que obtiene un token actuar en nombre de usuario original y, a continuación, se utiliza el token para llamar a otro web API.
| C# y .NET | [NativeClient WindowsPhone8.1](https://github.com/Azure-Samples/active-directory-dotnet-windowsphone-8.1) | Una aplicación de la tienda Windows 8.1 de Windows Phone que llama a un sitio web API que está protegida por Azure AD.
| ObjC | [NativeClient iOS](https://github.com/Azure-Samples/active-directory-ios) | Una aplicación iOS que llama a un sitio web API que requiere Azure AD para la autenticación.
| C# y .NET | [WebAPI-ManuallyValidateJwt-DotNet](https://github.com/Azure-Samples/active-directory-dotnet-webapi-manual-jwt-validation) | Una aplicación de cliente nativo que incluye lógica para procesar un token JWT en un sitio web API, en lugar de usar el software intermedio OWIN.
| C# / Xamarin | [NativeClient-Xamarin-Android](https://github.com/Azure-Samples/active-directory-xamarin-android) | Un enlace de Xamarin a la nativa Azure AD autenticación biblioteca (ADAL) para la biblioteca de Android.
| C# / Xamarin | [NativeClient-Xamarin-iOS](https://github.com/Azure-Samples/active-directory-xamarin-ios) | Un enlace de Xamarin a la nativa Azure AD autenticación biblioteca (ADAL) para iOS.
| C# / Xamarin | [NativeClient-MultiTarget-DotNet](https://github.com/Azure-Samples/active-directory-dotnet-native-multitarget) | Un proyecto Xamarin cinco plataformas y llamadas de web API que está protegido por Azure AD.
| C# y .NET | [NativeClient desatendida DotNet](https://github.com/Azure-Samples/active-directory-dotnet-native-headless) | Una aplicación nativa que realiza la autenticación no interactivo y llamadas de web API está protegida por Azure AD.



## <a name="web-application-to-web-api"></a>Aplicación Web en Web API

Mostrar cómo usar [OAuth 2.0 en Azure AD](https://msdn.microsoft.com/library/azure/dn645545.aspx) para crear aplicaciones web que llame a estos ejemplos de código web API que están protegidas por Azure AD.

| Lenguaje o plataforma | Ejemplo | Descripción
| ----------------- | ------ | -----------
| C# y .NET | [Aplicación Web-WebAPI-OpenIDConnect-DotNet](https://github.com/Azure-Samples/active-directory-dotnet-webapp-webapi-openidconnect) | Llamar a un sitio web API con permisos del usuario que ha iniciado sesión en.
|  C# y .NET | [Aplicación Web WebAPI OAuth2 AppIdentity DotNet](https://github.com/Azure-Samples/active-directory-dotnet-webapp-webapi-oauth2-appidentity) | Llamar a un sitio web API con permisos de la aplicación.
| C# y .NET | [Aplicación Web WebAPI OAuth2 IdentidadDeUsuario Dotnet](https://github.com/Azure-Samples/active-directory-dotnet-webapp-webapi-oauth2-useridentity) | Agregar autorización con [OAuth 2.0 en Azure AD](https://msdn.microsoft.com/library/azure/dn645545.aspx) a una aplicación web existente para que pueda llamar a un sitio web API.
| JavaScript | [WebAPI Nodejs](https://github.com/Azure-Samples/active-directory-node-webapi) | Configurar un servicio de la API de REST que se integra con Azure AD para la protección de la API. Incluye un servidor Node.js con la API de Web.
| C# y .NET | [Aplicación Web WebAPI MultiTenant OpenIdConnect DotNet](https://github.com/Azure-Samples/active-directory-dotnet-webapp-webapi-multitenant-openidconnect) |  Un inquilino múltiples MVC web aplicación que se utiliza OpenID conectar (ASP.Net OpenID conectar OWIN software intermedio) para autenticar a los usuarios de un inquilino de Azure AD. Usa un código de autorización para invocar la API de gráfico.

## <a name="server-or-daemon-application-to-web-api"></a>Servidor o aplicación Daemon Web API

Estos ejemplos de código muestran cómo crear una aplicación de servidor o daemon que obtiene recursos desde un sitio web API mediante el uso de la [Biblioteca de autenticación de Azure AD (ADAL)](active-directory-authentication-libraries.md) y [OAuth 2.0 en Azure AD](https://msdn.microsoft.com/library/azure/dn645545.aspx).

| Lenguaje o plataforma | Ejemplo | Descripción
| ----------------- | ------ | -----------
| C# y .NET | [Daemon DotNet](https://github.com/Azure-Samples/active-directory-dotnet-daemon) | Una aplicación de consola llama a un sitio web API. Las credenciales de cliente es una contraseña.
| C# y .NET | [CertificateCredential de daemon de DotNet](https://github.com/Azure-Samples/active-directory-dotnet-daemon-certificate-credential) | Una aplicación de consola que llama a un sitio web API. Las credenciales de cliente es un certificado.


## <a name="calling-azure-ad-graph-api"></a>Llamar a la API de gráfico de Azure AD

Estos ejemplos de código muestran cómo crear aplicaciones que llaman a la API de Azure AD Graph para leer y escribir datos de directorio.

| Lenguaje o plataforma | Ejemplo | Descripción
| ----------------- | ------ | -----------
| Java | [GraphAPI de aplicación Web de Java](https://github.com/Azure-Samples/active-directory-java-graphapi-web) | Una aplicación web que usa la API de gráfico para tener acceso a datos de directorio de Azure AD.
| PHP | [Aplicación Web-GraphAPI-PHP](https://github.com/Azure-Samples/active-directory-php-graphapi-web) | Una aplicación web que usa la API de gráfico para tener acceso a datos de directorio de Azure AD.
| C# y .NET | [DotNet de GraphAPI de Web App](https://github.com/Azure-Samples/active-directory-dotnet-graphapi-web) | Una aplicación web que usa la API de gráfico para tener acceso a datos de directorio de Azure AD.
| C# y .NET | [ConsoleApp-GraphAPI-DotNet](https://github.com/Azure-Samples/active-directory-dotnet-graphapi-console) | Esta aplicación de consola muestra llamadas comunes de lectura y escritura a la API de gráfico y muestra cómo ejecutar la asignación de licencias de usuario y actualizar vínculos y fotos en miniatura de un usuario.
| C# y .NET | [ConsoleApp-GraphAPI-DiffQuery-DotNet](https://github.com/Azure-Samples/active-directory-dotnet-graphapi-diffquery) | Una aplicación de consola que usa la consulta diferencial de la API de gráfico para obtener periódicos cambios en objetos de usuario en un inquilino de Azure AD.
| C# y .NET | [Aplicación Web-GraphAPI-DirectoryExtensions-DotNet](https://github.com/Azure-Samples/active-directory-dotnet-graphapi-directoryextensions-web) | Una aplicación MVC usa las consultas de la API de gráfico para generar un organigrama de empresa simple.
| PHP | [Aplicación Web GraphAPI DirectoryExtensions PHP](https://github.com/Azure-Samples/active-directory-php-graphapi-directoryextensions-web) | Una aplicación de PHP que llama a la API de gráfico para registrar una extensión y, a continuación, leer, actualizar y eliminar valores en el atributo de extensión.


## <a name="authorization"></a>Autorización

Estos ejemplos de código muestran cómo utilizar Azure AD para autorización.

| Lenguaje o plataforma | Ejemplo | Descripción
| ----------------- | ------ | -----------
| C# y .NET | [DotNet de GroupClaims de Web App](https://github.com/Azure-Samples/active-directory-dotnet-webapp-groupclaims) | Realizar el control de acceso basado en roles (RBAC) mediante notificaciones de grupo de Azure Active Directory en una aplicación que se integra con Azure AD.
| C# y .NET | [DotNet de RoleClaims de Web App](https://github.com/Azure-Samples/active-directory-dotnet-webapp-roleclaims) | Realizar el control de acceso basado en roles (RBAC) mediante funciones de aplicación de Azure Active Directory en una aplicación que se integra con Azure AD.


## <a name="legacy-walkthroughs"></a>Tutoriales heredados

Estos tutoriales usar tecnología ligeramente más antigua, pero aún podrían ser de interés.

| Lenguaje o plataforma | Ejemplo | Descripción
| ----------------- | ------ | -----------
| C# y .NET | [Autorización basada en roles y ACL en una aplicación de Microsoft Azure AD](http://go.microsoft.com/fwlink/?LinkId=331694) | Realizar la autorización de basado en roles (RBAC) y basado en ACL en una aplicación que se integra con Azure AD.
| C# y .NET |  [Autenticación de AAL - aplicación de la tienda de Windows al servicio REST:](http://go.microsoft.com/fwlink/?LinkId=330605) |  Usar [Biblioteca de autenticación de Azure AD (ADAL)](active-directory-authentication-libraries.md) (anteriormente AAL) para la versión Beta de la tienda de Windows para agregar funciones de autenticación de usuario a una aplicación de la tienda de Windows.
| C# y .NET | [Autenticación de ADAL - aplicación nativa al servicio REST: con AAD mediante el cuadro de diálogo de explorador](http://go.microsoft.com/fwlink/?LinkId=259814) |  Usar la [Biblioteca de autenticación de Azure AD (ADAL)](active-directory-authentication-libraries.md) para agregar capacidades de autenticación de usuario a un cliente WPF.
| C# y .NET | [Autenticación de ADAL - aplicación nativa al servicio REST: con ACS mediante el cuadro de diálogo de explorador](http://code.msdn.microsoft.com/AAL-Native-App-to-REST-de57f2cc) |  Use la [Biblioteca de autenticación de Azure AD (ADAL)](active-directory-authentication-libraries.md) y [2.0 de servicio de Control de acceso (ACS)](http://msdn.microsoft.com/library/azure/hh147631.aspx) para agregar funciones de autenticación de usuario a un cliente WPF.
| C# y .NET | [ADAL - autenticación de servidor a servidor](http://go.microsoft.com/fwlink/?LinkId=259816) | Usar la [Biblioteca de autenticación de Azure AD (ADAL)](active-directory-authentication-libraries.md) para proteger llamadas de servicio de un proceso del lado servidor a un servicio de MVC4 Web API REST.
| C# y .NET | [Agregar inicio de sesión a la aplicación Web mediante Azure AD](https://github.com/Azure-Samples/active-directory-dotnet-webapp-openidconnect) | Configurar una aplicación de .NET para realizar web inicio de sesión único en el directorio de Azure AD enterprise.
| C# y .NET | [Desarrollar aplicaciones Web de múltiples arrendatarios con Azure AD](https://github.com/Azure-Samples/active-directory-dotnet-webapp-multitenant-openidconnect) | Use Azure AD para agregar al inicio de sesión único y capacidades de acceso del directorio de una aplicación de .NET trabajar entre varias organizaciones.
JAVA | [Aplicación de ejemplo de Java para Azure AD Graph API](http://go.microsoft.com/fwlink/?LinkId=263969) | Utilizar la API de gráfico de acceso a los datos de directorio de Azure AD.
PHP | [Aplicación de ejemplo PHP para Azure AD Graph API](http://code.msdn.microsoft.com/PHP-Sample-App-For-Windows-228c6ddb) | Utilizar la API de gráfico de acceso a los datos de directorio de Azure AD.
| C# y .NET | [Aplicación de ejemplo para Azure AD Graph API](http://go.microsoft.com/fwlink/?LinkID=262648) | Utilizar la API de gráfico de acceso a los datos de directorio de Azure AD.
| C# y .NET | [Aplicación de ejemplo de gráfico de Azure AD diferencial consulta](http://go.microsoft.com/fwlink/?LinkId=275398) | Utilizar la consulta diferencial de la API de gráfico para obtener los cambios periódicos en objetos de usuario en un inquilino de Azure AD.
| C# y .NET | [Aplicación de ejemplo para la integración de aplicación de nube de varios inquilinos de Azure AD](http://go.microsoft.com/fwlink/?LinkId=275397) | Integrar una aplicación de múltiples arrendatario en Azure AD.
| C# y .NET | [Proteger una aplicación de la tienda de Windows y el servicio Web REST con Azure AD](https://github.com/Azure-Samples/active-directory-dotnet-windows-store) | Crear un recurso web simple API y una aplicación de cliente de la tienda Windows utilizando Azure AD y la [Biblioteca de autenticación de Azure AD (ADAL)](active-directory-authentication-libraries.md).
| C# y .NET| [Uso de la API de gráfico para consultar Azure AD](https://github.com/Azure-Samples/active-directory-dotnet-graphapi-web) | Configurar una aplicación de Microsoft .NET para utilizar la API de Azure AD Graph para tener acceso a datos desde un directorio de Azure AD inquilino.

## <a name="see-also"></a>Vea también

##### <a name="other-resources"></a>Otros recursos

[Guía del desarrollador de Azure Active Directory](active-directory-developers-guide.md)

[Gráfico de Azure AD API Conceptual y referencia](https://msdn.microsoft.com/library/azure/hh974476.aspx)

[Gráfico de Azure AD API auxiliar biblioteca](https://www.nuget.org/packages/Microsoft.Azure.ActiveDirectory.GraphClient)

