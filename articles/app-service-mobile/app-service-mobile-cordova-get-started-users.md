<properties
    pageTitle="Agregar autenticación en Apache Cordova con aplicaciones móviles | Servicio de aplicaciones de Azure"
    description="Aprenda a usar aplicaciones móviles en servicio de la aplicación de Azure para autenticar a los usuarios de la aplicación Cordova Apache a través de una gran variedad de proveedores de identidades, incluyendo Google, Facebook, Twitter y Microsoft."
    services="app-service\mobile"
    documentationCenter="javascript"
    authors="adrianhall"
    manager="erikre"
    editor=""/>

<tags
    ms.service="app-service-mobile"
    ms.workload="na"
    ms.tgt_pltfrm="mobile-html"
    ms.devlang="javascript"
    ms.topic="article"
    ms.date="10/01/2016"
    ms.author="adrianha"/>

# <a name="add-authentication-to-your-apache-cordova-app"></a>Agregar autenticación para su aplicación Apache Cordova

[AZURE.INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]
    
## <a name="summary"></a>Resumen

En este tutorial, agregue autenticación al proyecto todolist tutorial en Cordova Apache con un proveedor de identidades compatibles. En este tutorial se basa en el tutorial de [Introducción a las aplicaciones móviles] , debe completar primero.

##<a name="register"></a>Registrar la aplicación para la autenticación y configurar el servicio de aplicación

[AZURE.INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

[Vea un vídeo que muestra los pasos similares](https://channel9.msdn.com/series/Azure-connected-services-with-Cordova/Azure-connected-services-task-8-Azure-authentication)

##<a name="permissions"></a>Restringir permisos a los usuarios autenticados

[AZURE.INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

Ahora, puede comprobar que se ha deshabilitado el acceso anónimo a su back-end. En Visual Studio, abra el proyecto que ha creado una vez que el tutorial de [Introducción a las aplicaciones móviles], a continuación, ejecute la aplicación en el **Emulador de Google Android** y compruebe que se muestra un error inesperado de conexión que se inicia la aplicación.

A continuación, se actualizará la aplicación para autenticar usuarios antes de solicitar recursos de la aplicación móvil de back-end.

##<a name="add-authentication"></a>Agregar autenticación a la aplicación

1. Abra el proyecto de **Visual Studio**, a continuación, abra la `www/index.html` archivo para editarlo.

2. Busque la `Content-Security-Policy` etiqueta meta en la sección de encabezado.  Debe agregar el host de OAuth a la lista de orígenes de permitidos.

  	| Proveedor               | Nombre del proveedor SDK | OAuth Host                  |
  	| :--------------------- | :---------------- | :-------------------------- |
  	| Azure Active Directory | aad               | https://Login.Windows.NET   |
  	| Facebook               | Facebook          | https://www.Facebook.com    |
  	| Google                 | Google            | https://Accounts.Google.com |
  	| Microsoft              | microsoftaccount  | https://Login.Live.com      |
  	| Twitter                | Twitter           | https://API.twitter.com     |

    Un ejemplo de contenido-directiva de seguridad (implementado para Azure Active Directory) es la siguiente:

        <meta http-equiv="Content-Security-Policy" content="default-src 'self'
            data: gap: https://login.windows.net https://yourapp.azurewebsites.net; style-src 'self'">

    Debe reemplazar `https://login.windows.net` con el host de OAuth de la tabla anterior.  Consulte la [documentación de la directiva de seguridad de contenido] para obtener más información acerca de esta etiqueta meta.

    Tenga en cuenta que algunos proveedores de autenticación no requieren que Directiva de seguridad de contenido cambia cuando se utiliza en un dispositivo móvil adecuado.  Por ejemplo, se requiere ningún cambio de la directiva de seguridad de contenido al usar autenticación de Google en un dispositivo Android.

3. Abrir la `www/js/index.js` para la edición de archivos, busque la `onDeviceReady()` método, y en la creación de cliente código agregue lo siguiente:

        // Login to the service
        client.login('SDK_Provider_Name')
            .then(function () {

                // BEGINNING OF ORIGINAL CODE

                // Create a table reference
                todoItemTable = client.getTable('todoitem');

                // Refresh the todoItems
                refreshDisplay();

                // Wire up the UI Event Handler for the Add Item
                $('#add-item').submit(addItemHandler);
                $('#refresh').on('click', refreshDisplay);

                // END OF ORIGINAL CODE

            }, handleError);

    Tenga en cuenta que este código reemplazar el código existente que crea la referencia de tabla y se actualiza la interfaz de usuario.

    El método login() inicia la autenticación con el proveedor. El método de login() es una función asincrónica que devuelve una promesa de JavaScript.  El resto de la inicialización se coloca dentro de la respuesta promesa para que no se ejecuta hasta que se complete el método login().

4. En el código que acaba de agregar, reemplace `SDK_Provider_Name` con el nombre de su proveedor de inicio de sesión. Por ejemplo, para Azure Active Directory, use `client.login('aad')`.

4. Ejecute el proyecto.  Si el proyecto ha finalizado la inicialización, la aplicación mostrará la página de inicio de sesión de OAuth para el proveedor de servicios de autenticación elegido.

##<a name="next-steps"></a>Pasos siguientes

* Obtenga más [Información sobre la autenticación] con el servicio de aplicación de Azure.
* Continuar con el tutorial agregando [Notificaciones Push] para su aplicación Cordova Apache.

Obtenga información sobre cómo usar el SDK.

* [Apache Cordova SDK]
* [SDK de servidor de ASP.NET]
* [Node.js Server SDK]

<!-- URLs. -->
[Empezar a trabajar con aplicaciones móviles]: app-service-mobile-cordova-get-started.md
[Documentación de la directiva de seguridad de contenido]: https://cordova.apache.org/docs/en/latest/guide/appdev/whitelist/index.html
[Notificaciones de inserción]: app-service-mobile-cordova-get-started-push.md
[Acerca de la autenticación]: app-service-mobile-auth.md
[Apache Cordova SDK]: app-service-mobile-cordova-how-to-use-client-library.md 
[SDK de servidor de ASP.NET]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[Node.js Server SDK]: app-service-mobile-node-backend-how-to-use-server-sdk.md
