<properties
    pageTitle="Introducción de Azure AD versión 2.0 AngularJS | Microsoft Azure"
    description="Cómo crear una aplicación de Angular JS única página que inicia sesión en cuentas de trabajo o escuela y los usuarios con dos cuentas personales de Microsoft."
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


# <a name="add-sign-in-to-an-angularjs-single-page-app---nodejs"></a>Agregar inicio de sesión a una aplicación de la página de AngularJS - NodeJS

En este artículo agregaremos iniciar sesión con cuentas de Microsoft ofrecido a una aplicación de AngularJS utilizando el extremo de la versión 2.0 de Azure Active Directory. el punto final de la versión 2.0 permiten realizar una única integración en su aplicación y autenticar a los usuarios con cuentas personales y de trabajo o la escuela.

En este ejemplo es una aplicación de página de lista de tareas pendientes sencilla que almacena las tareas de back-end API de REST, escrito en NodeJS y proteger mediante tokens portador de OAuth de Azure AD.  La aplicación AngularJS usará nuestro Abrir origen JavaScript autenticación biblioteca [adal.js](https://github.com/AzureAD/azure-activedirectory-library-for-js) para controlar el proceso de inicio de sesión completa y adquirir tokens para llamar a la API de REST.  Para autenticar a otras API de REST, como el de [Microsoft Graph](https://graph.microsoft.com) o las API de administrador de recursos de Azure, se puede aplicar el mismo patrón.

> [AZURE.NOTE]
    No todas las características y escenarios de Azure Active Directory son compatibles con el extremo de la versión 2.0.  Para determinar si debe usar el extremo de la versión 2.0, lea acerca de [las limitaciones de la versión 2.0](active-directory-v2-limitations.md).

## <a name="download"></a>Descargar

Para empezar, debe descargar e instalar [node.js](https://nodejs.org).  A continuación, se puede clonar o [Descargar](https://github.com/AzureADQuickStarts/AppModelv2-SinglePageApp-AngularJS-NodeJS/archive/skeleton.zip) una aplicación de esquema:

```
git clone --branch skeleton https://github.com/AzureADQuickStarts/AppModelv2-SinglePageApp-AngularJS-NodeJS.git
```

La aplicación de esquema incluye todo el código de texto reutilizable para una aplicación de AngularJS simple, pero le falta todas las partes relacionadas con la identidad.  Si no desea seguir a lo largo, en su lugar puede clonar o [Descargar](https://github.com/AzureADQuickStarts/AppModelv2-SinglePageApp-AngularJS-NodeJS/archive/complete.zip) el ejemplo completo.

```
git clone https://github.com/AzureADSamples/SinglePageApp-AngularJS-NodeJS.git
```

## <a name="register-an-app"></a>Registrar una aplicación

En primer lugar, crear una aplicación en el [Portal de registro de la aplicación](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList), o siga estos [pasos detallados](active-directory-v2-app-registration.md).  Asegúrese de que:

- Agregue la plataforma de **Web** para la aplicación.
- Escriba el correcto **URI redirigir**. El valor predeterminado para este ejemplo es `http://localhost:8080`.
- Deje la casilla de verificación **Permitir flujo implícitos** habilitado. 

Copia hacia abajo el **Identificador de la aplicación** que se ha asignado a la aplicación, necesitará breve. 

## <a name="install-adaljs"></a>Instalar adal.js
Para empezar, vaya Project que descargó e instale adal.js.  Si tiene [bower](http://bower.io/) instalado, solo puede ejecutar este comando.  Para los errores de coincidencia de la versión de dependencia, elija solo una versión posterior.
```
bower install adal-angular#experimental
```

Como alternativa, puede descargar manualmente [adal.js](https://raw.githubusercontent.com/AzureAD/azure-activedirectory-library-for-js/experimental/dist/adal.min.js) y [adal angular.js](https://raw.githubusercontent.com/AzureAD/azure-activedirectory-library-for-js/experimental/dist/adal-angular.min.js).  Agregar los archivos a la `app/lib/adal-angular-experimental/dist` directorio.

Ahora abra el proyecto en el editor de texto y cargar adal.js al final del cuerpo de la página:

```html
<!--index.html-->

...

<script src="App/bower_components/dist/adal.min.js"></script>
<script src="App/bower_components/dist/adal-angular.min.js"></script>

...
```

## <a name="set-up-the-rest-api"></a>Configurar la API de REST

Mientras estamos estableciendo cosas, permite lograr la API de REST de back-end.  En el símbolo del sistema, instale todos los paquetes necesarios ejecutando (asegúrese de que está en el directorio de nivel superior del proyecto):

```
npm install
```

Ahora abra `config.js` y reemplace la `audience` valor:

```js
exports.creds = {
     
     // TODO: Replace this value with the Application ID from the registration portal
     audience: '<Your-application-id>',
     
     ...
}
```

La API de REST usará este valor para validar tokens que recibe desde la aplicación Angular en solicitudes de AJAX.  Nota que este simple API de REST almacena datos en memoria de manera que cada vez para detener el servidor, perderá todas las tareas previamente creadas.

Ese es todo el tiempo que vamos a gastar va a hablar sobre cómo funciona la API de REST.  No dude en echar un vistazo a en el código, pero si desea obtener más información sobre protección de web API con Azure AD, consulte [este artículo](active-directory-v2-devquickstarts-node-api.md). 

## <a name="sign-users-in"></a>Usuarios de inicio de sesión en
Tiempo para escribir código identidad.  Es posible que haya ya observado que adal.js contiene un proveedor de AngularJS, que se reproduce satisfactoriamente con mecanismos de enrutamiento Angular.  Empiece agregando el módulo adal a la aplicación:

```js
// app/scripts/app.js

angular.module('todoApp', ['ngRoute','AdalAngular'])
.config(['$routeProvider','$httpProvider', 'adalAuthenticationServiceProvider',
 function ($routeProvider, $httpProvider, adalProvider) {

...
```

Ahora puede inicializar el `adalProvider` con su ID de aplicación:

```js
// app/scripts/app.js

...

adalProvider.init({
        
        // Use this value for the public instance of Azure AD
        instance: 'https://login.microsoftonline.com/', 
        
        // The 'common' endpoint is used for multi-tenant applications like this one
        tenant: 'common',
        
        // Your application id from the registration portal
        clientId: '<Your-application-id>',
        
        // If you're using IE, uncommment this line - the default HTML5 sessionStorage does not work for localhost.
        //cacheLocation: 'localStorage',
         
    }, $httpProvider);
```

Excelente, adal.js tiene ahora toda la información que necesita para proteger su aplicación y los usuarios de inicio de sesión.  Para forzar el inicio de sesión en una determinada ruta en la aplicación, basta con una línea de código:

```js
// app/scripts/app.js

...

}).when("/TodoList", {
    controller: "todoListCtrl",
    templateUrl: "/static/views/TodoList.html",
    requireADLogin: true, // Ensures that the user must be logged in to access the route
})

...
```

Ahora cuando un usuario hace clic en el `TodoList` vínculo, adal.js automáticamente redirigirá a Azure AD para iniciar sesión si es necesario.  Puede enviar explícitamente las solicitudes de inicio de sesión y cierre de sesión al invocar adal.js en los controladores de:

```js
// app/scripts/homeCtrl.js

angular.module('todoApp')
// Load adal.js the same way for use in controllers and views   
.controller('homeCtrl', ['$scope', 'adalAuthenticationService','$location', function ($scope, adalService, $location) {
    $scope.login = function () {
        
        // Redirect the user to sign in
        adalService.login();
        
    };
    $scope.logout = function () {
        
        // Redirect the user to log out    
        adalService.logOut();
    
    };
...
```

## <a name="display-user-info"></a>Mostrar la información de usuario
Ahora que el usuario ha iniciado sesión en, probablemente deberá tener acceso a datos de autenticación de usuario que ha iniciado sesión en la aplicación.  Adal.js expone esta información para el usuario en el `userInfo` objeto.  Para obtener acceso a este objeto en una vista, en primer lugar, agregue adal.js al ámbito raíz del controlador correspondiente:

```js
// app/scripts/userDataCtrl.js

angular.module('todoApp')
// Load ADAL for use in view
.controller('userDataCtrl', ['$scope', 'adalAuthenticationService', function ($scope, adalService) {}]);
```

A continuación, se pueden resolver directamente la `userInfo` objeto en la vista: 

```html
<!--app/views/UserData.html-->

...

    <!--Get the user's profile information from the ADAL userInfo object-->
    <tr ng-repeat="(key, value) in userInfo.profile">
        <td>{{key}}</td>
        <td>{{value}}</td>
    </tr>
...
```

También puede usar el `userInfo` objeto para determinar si el usuario que ha iniciado sesión en o no.

```html
<!--index.html-->

...

    <!--Use the ADAL userInfo object to show the right login/logout button-->
    <ul class="nav navbar-nav navbar-right">
        <li><a class="btn btn-link" ng-show="userInfo.isAuthenticated" ng-click="logout()">Logout</a></li>
        <li><a class="btn btn-link" ng-hide="userInfo.isAuthenticated" ng-click="login()">Login</a></li>
    </ul>
...
```

## <a name="call-the-rest-api"></a>Llamar a la API de REST
Por último, es hora de obtener algunos tokens y llamar a la API de REST para crear, leer, actualizar y eliminar tareas.  Bien ¿sabe qué?  No tiene que hacer *algo*.  Adal.js se ocupará automáticamente de introducción, almacenamiento en caché y actualización de tokens.  También se ocupará de adjuntar esos símbolos a las solicitudes de AJAX salientes que se envían a la API de REST.  

¿Exactamente cómo funciona esto? Es todo ello gracias a la magia de [interceptores AngularJS](https://docs.angularjs.org/api/ng/service/$http), que permite adal.js transformar los mensajes entrantes y salientes de http.  Además, adal.js se supone que las solicitudes de envían al mismo dominio, como la ventana debe usar tokens está pensados para el mismo ID de aplicación que la aplicación AngularJS.  Se trata de por qué hemos usado el mismo ID de aplicación en ambos la aplicación Angular y en la API de REST de NodeJS.  Por supuesto, puede reemplazar este comportamiento y saber adal.js obtener tokens para otras API de REST si es necesario - pero para este sencillo escenario hará los valores predeterminados.

Esto es un fragmento de código que muestra lo fácil que es enviar solicitudes de tokens portador de Azure AD:

```js
// app/scripts/todoListSvc.js

...
return $http.get('/api/tasks');
...
```

¡Felicidades!  La aplicación integrada única página de Azure AD está ahora completa.  Continuar, dedique un lazo.  Puede autenticar a los usuarios, deben llamar a su API de REST usando OpenID conectar de back-end y obtenga información básica sobre el usuario.  En el cuadro, es compatible con cualquier usuario con un Account personal de Microsoft o una cuenta de trabajo o la escuela de Azure AD.  Probar la aplicación ejecutando:

```
node server.js
```

En un explorador, vaya a `http://localhost:8080`.  Inicie sesión con una cuenta personal de Microsoft o una cuenta de trabajo o la escuela.  Agregar tareas a la lista de tareas pendientes del usuario y cerrar sesión.  Intente iniciar sesión con el otro tipo de cuenta. Si necesita un inquilino de Azure AD para crear usuarios de trabajo o la escuela, [obtener información sobre cómo obtener una aquí](active-directory-howto-tenant.md) (es gratuita).

Para continuar aprendiendo sobre el extremo de la versión 2.0, vuelva a nuestra [Guía del desarrollador de la versión 2.0](active-directory-appmodel-v2-overview.md).  Para obtener recursos adicionales, consulte:

- [Ejemplos de Azure en GitHub >>](https://github.com/Azure-Samples)
- [Azure AD en desbordamiento de pila >>](http://stackoverflow.com/questions/tagged/azure-active-directory)
- Documentación de Azure AD en [Azure.com >>](https://azure.microsoft.com/documentation/services/active-directory/)

## <a name="get-security-updates-for-our-products"></a>Obtener actualizaciones de seguridad de nuestros productos

Le recomendamos que obtener notificaciones de cuándo se producen incidencias de seguridad, visite [esta página](https://technet.microsoft.com/security/dd252948) y suscribirse a las alertas de aviso de seguridad.
