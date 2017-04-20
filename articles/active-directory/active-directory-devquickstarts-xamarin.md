<properties
    pageTitle="Introducción a Azure AD Xamarin | Microsoft Azure"
    description="Cómo crear una aplicación de Xamarin que se integra con Azure AD para iniciar sesión y llamadas de Azure AD protegido API con OAuth."
    services="active-directory"
    documentationCenter="xamarin"
    authors="dstrockis"
    manager="mbaldwin"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="mobile-xamarin"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="09/16/2016"
    ms.author="dastrock"/>


# <a name="integrate-azure-ad-into-a-xamarin-app"></a>Integrar Azure AD en una aplicación Xamarin

[AZURE.INCLUDE [active-directory-devquickstarts-switcher](../../includes/active-directory-devquickstarts-switcher.md)]

[AZURE.INCLUDE [active-directory-devguide](../../includes/active-directory-devguide.md)]

Xamarin le permite escribir aplicaciones móviles en C# que se pueden ejecutar iOS, Android y Windows (dispositivos móviles y PC). Si desea crear una aplicación con Xamarin, Azure AD facilita la forma muy sencilla para autenticar los usuarios con sus cuentas de Active Directory. También permite la aplicación de forma segura consumir cualquier web API protegido por Azure AD, como las API de Office 365 o la API de Azure.

Aplicaciones de Xamarin que necesitan tener acceso a los recursos protegidos, Azure AD proporciona la Active Directory Authentication Library o ADAL. Propósitos de ADAL en la vida es más fácil para su aplicación obtener el token de acceso. Para demostrar lo fácil que es, a continuación, crearemos una aplicación "Buscador de directorio":

-   Se ejecuta en iOS, Android, escritorio de Windows, Windows Phone y la tienda Windows.
- Utiliza una biblioteca de clase portátil única (PCL) para autenticar a los usuarios y obtener tokens para la API de Azure AD Graph
-   Busca en un directorio para los usuarios con un determinado UPN.

Para generar la aplicación de trabajo completado, tendrá que:

2. Configurar el entorno de desarrollo Xamarin
2. Registrar la aplicación con Azure AD.
3. Instalar y configurar ADAL.
5. Use ADAL para obtener tokens de Azure AD.

Para comenzar, [descargar un proyecto de esquema](https://github.com/AzureADQuickStarts/NativeClient-MultiTarget-DotNet/archive/skeleton.zip) o [descargar el ejemplo completo](https://github.com/AzureADQuickStarts/NativeClient-MultiTarget-DotNet/archive/complete.zip). Cada uno de ellos es una solución de Visual Studio de 2013. También necesitará a un inquilino de Azure AD en el que puede crear usuarios y registrar una aplicación. Si aún no dispone de un inquilino, [Obtenga información sobre cómo obtener una](active-directory-howto-tenant.md).

## <a name="0-set-up-your-xamarin-development-environment"></a>*0. configurar su entorno de desarrollo Xamarin*
Dado que este tutorial incluye proyectos para iOS, Android y Windows, necesitará Visual Studio y Xamarin juntos. Para crear el entorno es necesario, siga las instrucciones completadas de [instalación e instalar para Visual Studio y Xamarin](https://msdn.microsoft.com/library/mt613162.aspx) en MSDN. Las instrucciones incluyen material que puede revisar para obtener más información sobre Xamarin mientras espera para que completar la instalación. 

Una vez completado el programa de instalación es necesario, abra la solución en Visual Studio para empezar. Encontrará seis proyectos: cinco proyectos específicos de la plataforma y una biblioteca de clases portátil que se compartirán en todas las plataformas`DirectorySearcher.cs`

## <a name="1-register-the-directory-searcher-application"></a>*1. registrar la aplicación de buscador de directorio*
Para habilitar la aplicación obtener tokens, primero deberá registrar en su inquilino de Azure AD y conceder permiso de acceso a la API de Azure AD gráfico:

-   Inicie sesión en el [Portal de administración de Azure](https://manage.windowsazure.com)
-   En la barra de navegación izquierda, haga clic en **Active Directory**
-   Seleccione a un inquilino en el que se va a registrar la aplicación.
-   Haga clic en la pestaña de **aplicaciones** y haga clic en **Agregar** en la Bandeja de la parte inferior.
-   Siga las indicaciones y cree una nueva **Aplicación nativa de cliente**.
    -   El **nombre** de la aplicación describe la aplicación a los usuarios finales
    -   La **Redirección Uri** es una combinación de combinación y la cadena que usará Azure AD para devolver respuestas tokens. Escriba un valor, por ejemplo, `http://DirectorySearcher`.
-   Una vez haya completado el registro, AAD asignar su aplicación un identificador único del cliente. Necesitará este valor en las siguientes secciones, así que cópielo desde la ficha **Configurar** .
- También en la ficha **Configurar** , busque la sección "Permisos a otras aplicaciones". Para la aplicación "Azure Active Directory", agregue el permiso de **acceso a su directorio de la organización** en **Permisos de delegado**. Esto le permitirá la aplicación de la API de gráfico para los usuarios de la consulta.

## <a name="2-install--configure-adal"></a>*2. instalar y configurar ADAL*
Ahora que ya tiene una aplicación en Azure AD, puede instalar ADAL y escriba el código de identidad. Para ADAL puedan comunicarse con Azure AD, debe proporcionar con información sobre el registro de aplicación.
-   Empiece agregando ADAL a cada uno de los proyectos de la solución mediante la consola del Administrador de paquetes.

`
PM> Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -ProjectName DirectorySearcherLib
`

`
PM> Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -ProjectName DirSearchClient-Android
`

`
PM> Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -ProjectName DirSearchClient-Desktop
`

`
PM> Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -ProjectName DirSearchClient-iOS
`

`
PM> Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -ProjectName DirSearchClient-Universal
`

- Debe Observe que dos referencias de biblioteca se agregan a cada proyecto: la parte PCL de ADAL y una parte específica de la plataforma.

-   En el proyecto DirectorySearcherLib, abra `DirectorySearcher.cs`. Cambiar los valores de miembros de la clase para reflejar los valores que introduce en el Portal de Azure. El código hará referencia a estos valores siempre que use ADAL.
    -   La `tenant` es el dominio de su inquilino de Azure AD, por ejemplo, contoso.onmicrosoft.com
    -   La `clientId` está dentro de la aplicación que ha copiado desde el portal.
    - La `returnUri` es la redirectUri que haya escrito en el portal, por ejemplo, `http://DirectorySearcher`.

## <a name="3--use-adal-to-get-tokens-from-aad"></a>*3. use ADAL para obtener Tokens de AAD*
*Casi* todas lógica de autenticación de la aplicación se encuentra en `DirectorySearcher.SearchByAlias(...)`. Todo lo que es necesario en los proyectos específicos de la plataforma es pasar un parámetro contextual para la `DirectorySearcher` PCL.

- En primer lugar, abra `DirectorySearcher.cs` y agregar un parámetro nuevo a la `SearchByAlias(...)` método. `IPlatformParameters`es el parámetro contextual encapsulado los objetos específicos de la plataforma que ADAL debe realizar la autenticación.

```C#
public static async Task<List<User>> SearchByAlias(string alias, IPlatformParameters parent)
{
```

-   A continuación, inicialice la `AuthenticationContext` -ADAL de clase principal. Esto es donde se pasa ADAL las coordenadas que necesita para comunicarse con Azure AD. A continuación, llame a `AcquireTokenAsync(...)`, que acepta la `IPlatformParameters` objeto y llamará el flujo de autenticación necesario para devolver un símbolo de la aplicación.

```C#
...
    AuthenticationResult authResult = null;
    try
    {
        AuthenticationContext authContext = new AuthenticationContext(authority);
        authResult = await authContext.AcquireTokenAsync(graphResourceUri, clientId, returnUri, parent);
    }
    catch (Exception ee)
    {
        results.Add(new User { error = ee.Message });
        return results;
    }
...
```
- `AcquireTokenAsync(...)`en primer lugar intentará devolver un símbolo para el recurso solicitado (la API de gráfico en este caso) sin preguntar al usuario que introduzca sus credenciales (a través de almacenamiento en caché o actualización de tokens antiguos). Si es necesario, se muestran al usuario el inicio de sesión de Azure AD de página antes de adquirir el token solicitado.


- A continuación, puede adjuntar el token de acceso a la solicitud de API de gráfico en el encabezado de autorización:

```C#
...
    request.Headers.Authorization = new AuthenticationHeaderValue("Bearer", authResult.AccessToken);
...
```

Eso es todo para la `DirectorySearcher` PCL y la aplicación de relacionados con la identidad del código.  Todo lo que queda es llamar a la `SearchByAlias(...)` método en vistas de la plataforma, y donde es necesario agregar código para controlar correctamente el ciclo de vida de la interfaz de usuario.

####<a name="android"></a>Android:
- En `MainActivity.cs`, agregue una llamada a `SearchByAlias(...)` en el botón, haga clic en controlador:

```C#
List<User> results = await DirectorySearcher.SearchByAlias(searchTermText.Text, new PlatformParameters(this));
```
- También debe anular la `OnActivityResult` método de ciclo de vida para reenviar cualquier autenticación redirige al método adecuado.  ADAL proporciona un método auxiliar para este en Android:

```C#
...
protected override void OnActivityResult(int requestCode, Result resultCode, Intent data)
{
    base.OnActivityResult(requestCode, resultCode, data);
    AuthenticationAgentContinuationHelper.SetAuthenticationAgentContinuationEventArgs(requestCode, resultCode, data);
}
...
```

####<a name="windows-desktop"></a>Escritorio de Windows:
- En `MainWindow.xaml.cs`, basta con hacer una llamada a `SearchByAlias(...)` pasando un `WindowInteropHelper` en el escritorio `PlatformParameters` objeto:

```C#
List<User> results = await DirectorySearcher.SearchByAlias(
  SearchTermText.Text,
  new PlatformParameters(PromptBehavior.Auto, this.Handle));
```

####<a name="ios"></a>iOS:
- En `DirSearchClient_iOSViewController.cs`, iOS `PlatformParameters` objeto simplemente toma una referencia en el controlador de vista:

```C#
List<User> results = await DirectorySearcher.SearchByAlias(
  SearchTermText.Text,
  new PlatformParameters(PromptBehavior.Auto, this.Handle));
```

####<a name="windows-universal"></a>Universal de Windows:
- En Windows Universal, abra `MainPage.xaml.cs` e implementar la `Search` método, que utiliza un método auxiliar en un proyecto compartido para actualizar la interfaz de usuario según sea necesario.

```C#
...
    List<User> results = await DirectorySearcherLib.DirectorySearcher.SearchByAlias(SearchTermText.Text, new PlatformParameters(PromptBehavior.Auto, false));
...
```

¡Felicidades! Ahora tiene un aplicación de Xamarin que tiene la capacidad para autenticar usuarios y segura llamar a las API de Web usando OAuth 2.0 en cinco diferentes plataformas de trabajo. Si todavía no lo ha hecho, ahora es el momento para rellenar al inquilino de algunos usuarios. Ejecute la aplicación DirectorySearcher e inicie sesión con uno de esos usuarios. Buscar otros usuarios en función de su UPN.

ADAL facilita la incorporar características comunes de identidad de la aplicación. Se encarga de todo el trabajo dirty para usted, gestión de caché, compatibilidad con el protocolo OAuth, presentando el usuario con un inicio de sesión de interfaz de usuario, actualizar tokens caducados y mucho más. Todo lo que necesita saber es una sola llamada API, `authContext.AcquireToken*(…)`.

Para referencia, se proporciona el ejemplo completo (sin los valores de configuración) [aquí](https://github.com/AzureADQuickStarts/NativeClient-MultiTarget-DotNet/archive/complete.zip). Ahora puede mover a escenarios de identidad adicional. Desea probar:

[Proteger un Web .NET API con Azure AD >>](active-directory-devquickstarts-webapi-dotnet.md)

[AZURE.INCLUDE [active-directory-devquickstarts-additional-resources](../../includes/active-directory-devquickstarts-additional-resources.md)]
