<properties
    pageTitle="Introducción a Azure AD AngularJS | Microsoft Azure"
    description="Cómo crear una aplicación de Angular JS única página que se integra con Azure AD para iniciar sesión y llamadas de Azure AD protegido API con OAuth."
    services="active-directory"
    documentationCenter=""
    authors="dstrockis"
    manager="mbaldwin"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="javascript"
    ms.topic="article"
    ms.date="09/16/2016"
    ms.author="dastrock"/>


# <a name="securing-angularjs-single-page-apps-with-azure-ad"></a>Seguridad de aplicaciones de la página de AngularJS solo con Azure AD

[AZURE.INCLUDE [active-directory-devquickstarts-switcher](../../includes/active-directory-devquickstarts-switcher.md)]

[AZURE.INCLUDE [active-directory-devguide](../../includes/active-directory-devguide.md)]

Azure AD facilita la forma muy sencilla que agregar el inicio de sesión en Cerrar sesión y proteger llamadas a la API de OAuth a sus aplicaciones de la misma página.  Permite a su aplicación autenticar usuarios con sus cuentas de Active Directory y consumir cualquier web API protegido por Azure AD, como las API de Office 365 o la API de Azure.

Para las aplicaciones de javascript que se ejecuta en un explorador, Azure AD proporciona la Active Directory Authentication Library o adal.js.  Propósitos de Adal.js en la vida es más fácil para su aplicación obtener el token de acceso.  Para mostrar solo lo fácil que es, a continuación, crearemos una aplicación de AngularJS a la lista de tareas pendientes:

- Inicio de sesión del usuario en la aplicación con Azure AD como el proveedor de identidades.
- Muestra información sobre el usuario.
- Forma segura llama para hacer lista API de la aplicación con tokens portador de AAD.
- Inicia el usuario de la aplicación.

Para generar la aplicación de trabajo completado, tendrá que:

2. Registrar la aplicación con Azure AD.
3. ADAL de instalar y configurar la SPA.
5. Use ADAL para páginas en el SPA seguras.

Para comenzar, [Descargue la estructura de la aplicación](https://github.com/AzureADQuickStarts/SinglePageApp-AngularJS-DotNet/archive/skeleton.zip) o [descargar el ejemplo completo](https://github.com/AzureADQuickStarts/SinglePageApp-AngularJS-DotNet/archive/complete.zip).  También necesitará a un inquilino de Azure AD en el que puede crear usuarios y registrar una aplicación.  Si aún no dispone de un inquilino, [Obtenga información sobre cómo obtener una](active-directory-howto-tenant.md).

## <a name="1-register-the-directorysearcher-application"></a>*1. registrar la aplicación DirectorySearcher*
Para habilitar la aplicación autenticar usuarios y obtener tokens, primero deberá registrar en su inquilino de Azure AD:

-   Inicie sesión en el [Portal de administración de Azure](https://manage.windowsazure.com)
-   En la barra de navegación izquierda, haga clic en **Active Directory**
-   Seleccione a un inquilino en el que se va a registrar la aplicación.
-   Haga clic en la pestaña de **aplicaciones** y haga clic en **Agregar** en la Bandeja de la parte inferior.
-   Siga las indicaciones y cree una nueva **aplicación Web o WebAPI**.
    -   El **nombre** de la aplicación describe la aplicación a los usuarios finales.
    -   La **Redirección Uri** es la ubicación a la que AAD devolverá tokens.  La ubicación predeterminada para este ejemplo es`https://localhost:44326/`
-   Una vez haya completado el registro, AAD asignar la aplicación de un único **Identificador de cliente**.  Necesitará este valor en las siguientes secciones, así que cópielo desde la ficha **Configurar** .
- Adal.js utiliza el flujo de OAuth implícito para comunicarse con Azure AD.  Debe habilitar el flujo implícito de la aplicación:
    - Descargar el manifiesto de aplicación, haga clic en **Administrar manifiestos**.
    - Abra el manifiesto y busque el `oauth2AllowImplicitFlow` propiedad. Establezca su valor en `true`.
    - Guardar y cargar el manifiesto de aplicación haciendo clic en **Administrar manifiestos** de nuevo.

## <a name="2-install-adal--configure-the-spa"></a>*2. ADAL de instalar y configurar el SPA*
Ahora que ya tiene una aplicación en Azure AD, puede instalar adal.js y escriba el código de identidad.

-   Empiece agregando adal.js al proyecto TodoSPA con la consola del Administrador de paquete:
  - Descargar [adal.js](https://raw.githubusercontent.com/AzureAD/azure-activedirectory-library-for-js/master/lib/adal.js) y agregarlo a la `App/Scripts/` directorio del proyecto.
  - Descargar [adal angular.js](https://raw.githubusercontent.com/AzureAD/azure-activedirectory-library-for-js/master/lib/adal-angular.js) y agregarlo a la `App/Scripts/` directorio del proyecto.
  - Cargar cada script antes del final de la `</body>` en `index.html`:

```js
...
<script src="App/Scripts/adal.js"></script>
<script src="App/Scripts/adal-angular.js"></script>
...
```

-   Para back-end del SPA a hacer lista API para aceptar tokens desde el explorador, el servidor necesita información de configuración acerca del registro de aplicación. En el proyecto TodoSPA, abra `web.config`.  Reemplace los valores de los elementos de la `<appSettings>` sección para reflejar los valores que introduce en el Portal de Azure.  El código hará referencia a estos valores siempre que use ADAL.
    -   La `ida:Tenant` es el dominio de su inquilino de Azure AD, por ejemplo, contoso.onmicrosoft.com
    -   La `ida:Audience` debe ser el **Identificador de cliente** de la aplicación que ha copiado desde el portal.

## <a name="3--use-adal-to-secure-pages-in-the-spa"></a>*3. use ADAL para páginas en el SPA seguras*
Adal.js se ha creado para integrar con proveedores de ruta y http AngularJS, que le permite las vistas individuales en su SPA seguras.

- En `App/Scripts/app.js`, poner en el módulo adal.js:

```js
angular.module('todoApp', ['ngRoute','AdalAngular'])
.config(['$routeProvider','$httpProvider', 'adalAuthenticationServiceProvider',
 function ($routeProvider, $httpProvider, adalProvider) {
...
```
- Ahora puede inicializar el `adalProvider` con los valores de configuración de registro de la aplicación, también en `App/Scripts/app.js`:

```js
adalProvider.init(
  {
      instance: 'https://login.microsoftonline.com/',
      tenant: 'Enter your tenant name here e.g. contoso.onmicrosoft.com',
      clientId: 'Enter your client ID here e.g. e9a5a8b6-8af7-4719-9821-0deef255f68e',
      extraQueryParameter: 'nux=1',
      //cacheLocation: 'localStorage', // enable this for IE, as sessionStorage does not work for localhost.
  },
  $httpProvider
);
```
- Ahora para proteger la `TodoList` ver en la aplicación, solo en una línea de código es necesaria - `requireADLogin`.

```js
...
}).when("/TodoList", {
        controller: "todoListCtrl",
        templateUrl: "/App/Views/TodoList.html",
        requireADLogin: true,
...
```

Ahora tiene una aplicación de página único segura con la capacidad de los usuarios de inicio de sesión y emitir solicitudes protegidas tokens portador a su API de back-end.  Cuando un usuario hace clic en el `TodoList` vínculo, adal.js automáticamente redirigirá a Azure AD para iniciar sesión si es necesario.  Además, adal.js adjuntará automáticamente un access_token a las solicitudes de ajax que se envían a back-end de la aplicación.  El ejemplo anterior es el mínimo necesario para generar un SPA con adal.js - pero hay una serie de otras características que son útiles en SPAs:

- Explícitamente problema inicio de sesión y las solicitudes de inicio de sesión puede definir funciones en los controladores que invocación adal.js.  In `App/Scripts/homeCtrl.js`:

```js
...
$scope.login = function () {
    adalService.login();
};
$scope.logout = function () {
    adalService.logOut();
};
...
```
- También puede presentar la información de usuario en la interfaz de usuario de la aplicación.  El servicio de adal ya se ha agregado a la `userDataCtrl` controlador, por lo que puede tener acceso a la `userInfo` objeto en la vista asociada, `App/Views/UserData.html`:

```js
<p>{{userInfo.userName}}</p>
<p>aud:{{userInfo.profile.aud}}</p>
<p>iss:{{userInfo.profile.iss}}</p>
...
```

- También hay muchos escenarios en los que desee saber si el usuario se ha iniciado sesión.  También puede usar el `userInfo` objeto para recopilar esta información.  Por ejemplo, en `index.html` puede mostrar el "Inicio de sesión" o "Desconectarse" botón basándose en el estado de autenticación:

```js
<li><a class="btn btn-link" ng-show="userInfo.isAuthenticated" ng-click="logout()">Logout</a></li>
<li><a class="btn btn-link" ng-hide=" userInfo.isAuthenticated" ng-click="login()">Login</a></li>
```

¡Felicidades! Su Azure AD integrada única aplicación de página está ahora completa.  Puede autenticar a los usuarios, segura llame a su back-end mediante OAuth 2.0 y obtenga información básica sobre el usuario.  Si todavía no lo ha hecho, ahora es el momento para rellenar al inquilino de algunos usuarios.  Ejecutar el para hacer lista SPA e inicie sesión con uno de esos usuarios.  Agregar tareas a los usuarios de la lista, cerrar la sesión y vuelva a iniciarla.

Adal.js facilita la incorporar todas estas características de identidad comunes a la aplicación.  Se encarga de todo el trabajo dirty para usted, gestión de caché, compatibilidad con el protocolo OAuth, presentando el usuario con un inicio de sesión de interfaz de usuario, actualizar tokens caducados y mucho más.

Para referencia, se proporciona el ejemplo completo (sin los valores de configuración) [aquí](https://github.com/AzureADQuickStarts/SinglePageApp-AngularJS-DotNet/archive/complete.zip).  Ahora puede mover a escenarios adicionales.  Desea probar:

[Llamar a un sitio Web CORS API desde un SPA >>](https://github.com/AzureAdSamples/SinglePageApp-WebAPI-AngularJS-DotNet)

[AZURE.INCLUDE [active-directory-devquickstarts-additional-resources](../../includes/active-directory-devquickstarts-additional-resources.md)]
