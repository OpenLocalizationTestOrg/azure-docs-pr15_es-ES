<properties
    pageTitle="Autenticación de usuario para las aplicaciones de la API de servicio de la aplicación de Azure | Microsoft Azure"
    description="Obtenga información sobre cómo proteger una aplicación de API de servicio de la aplicación de Azure al permitir el acceso únicamente a los usuarios autenticados."
    services="app-service\api"
    documentationCenter=".net"
    authors="tdykstra"
    manager="wpickett"
    editor=""/>

<tags
    ms.service="app-service-api"
    ms.workload="na"
    ms.tgt_pltfrm="dotnet"
    ms.devlang="na"
    ms.topic="article"
    ms.date="06/30/2016"
    ms.author="rachelap"/>

# <a name="user-authentication-for-api-apps-in-azure-app-service"></a>Autenticación de usuario para las aplicaciones de la API de servicio de la aplicación de Azure

## <a name="overview"></a>Información general

Este artículo le muestra cómo proteger una aplicación de Azure API para que solo los usuarios autenticados pueden llamar. El artículo se presupone que ha leído la [información general de autenticación de servicio de aplicaciones de Azure](../app-service/app-service-authentication-overview.md).

Aprenderá:

* Cómo configurar un proveedor de servicios de autenticación, con detalles de Azure Active Directory (AD Azure).
* Cómo utilizar una aplicación de API protegida mediante la [Biblioteca de autenticación de directorio activo (ADAL) para JavaScript](https://github.com/AzureAD/azure-activedirectory-library-for-js).

El artículo contiene dos secciones:

* La sección [cómo configurar la autenticación de usuario en el servicio de aplicación de Azure](#authconfig) en general explica cómo configurar la autenticación de usuario para cualquier aplicación de la API y se aplica igualmente a todos los marcos compatibles con la aplicación de servicio, incluidos .NET, Node.js y Java.

* Comenzando por la sección [continuar los tutoriales de aplicaciones de la API de .NET](#tutorialstart) , las guías de artículo le guiará para configurar una aplicación de ejemplo con un .NET back-end y un frente AngularJS final para que utilice Azure Active Directory para la autenticación de usuario. 

## <a id="authconfig"></a>Cómo configurar la autenticación de usuario en el servicio de aplicación de Azure

Esta sección proporciona instrucciones generales que se aplican a cualquier aplicación de la API. Para pasos específicos de la aplicación de ejemplo para hacer el .NET de lista, vaya a [continuar los tutoriales de .NET Introducción](#tutorialstart).

1. En el [portal de Azure](https://portal.azure.com/), vaya a la hoja de **configuración** de la aplicación de la API que desea proteger, busque la sección de **características** y, a continuación, haga clic en **autenticación / autorización**.

    ![Portal de Azure autenticación/autorización](./media/app-service-api-dotnet-user-principal-auth/features.png)

3. En la **autenticación / autorización** módulo, haga clic **en**.

4. Seleccione una de las opciones de la lista desplegable **acción que se ejecuta cuando no se autentica la solicitud** .

    * Si desea que solo las llamadas autenticadas para llegar a la aplicación de la API, elija una de las opciones **iniciar sesión...** . Esta opción permite proteger la aplicación de la API sin escribir código que se ejecute en él.

    * Si desea que todas las llamadas a llegar a la aplicación de la API, elija **Permitir solicitud (ninguna acción)**. Puede usar esta opción para dirigir a llamadas sin autenticar con una gama de proveedores de autenticación. Con esta opción, debe escribir código para controlar la autorización.

    Para obtener más información, vea [autenticación y la autorización para las aplicaciones de la API de servicio de aplicaciones de Azure](app-service-api-authentication.md#multiple-protection-options).

5. Seleccione uno o varios de los **Proveedores de autenticación**.

    La imagen muestra las opciones que requieren todos los autores de llamadas para autenticarse en Azure AD.
 
    ![Módulo de autenticación/autorización portal Azure](./media/app-service-api-dotnet-user-principal-auth/authblade.png)

    Al elegir un proveedor de servicios de autenticación, el portal muestra un módulo de configuración para ese proveedor. 

    Para obtener instrucciones detalladas que explican cómo configurar los módulos de configuración del proveedor de servicios de autenticación, consulte [cómo configurar la aplicación de servicio de aplicaciones para usar el inicio de sesión de Azure Active Directory](../app-service-mobile/app-service-mobile-how-to-configure-active-directory-authentication.md). (El vínculo que se va a un artículo acerca de Azure AD, pero el artículo contiene pestañas con vínculos a artículos a los demás proveedores de autenticación).

7. Cuando haya terminado con la hoja de configuración del proveedor de servicios de autenticación, haga clic en **Aceptar**.

7. En la **autenticación / autorización** módulo, haga clic en **Guardar**.

Cuando haya hecho esto, el servicio de aplicación autentica todas las llamadas a la API antes de que lleguen a la aplicación de la API. Los servicios de autenticación funcionan de la misma para todos los idiomas que admite el servicio de aplicaciones, incluidos .NET, Node.js y Java. 

Para hacer llamadas autenticadas de API, el llamador incluye token portador de OAuth 2.0 de su proveedor de autenticación en el encabezado de autorización de solicitudes HTTP. El token se puede adquirir mediante SDK del proveedor de autenticación.

## <a id="tutorialstart"></a>Continuar los tutoriales de aplicaciones de la API de .NET

Si sigue los tutoriales Node.js o Java para las aplicaciones de la API, omita el siguiente artículo de [autenticación principal del servicio para las aplicaciones de la API](app-service-api-dotnet-service-principal-auth.md). 

Si están siguiendo la serie de tutoriales de .NET para las aplicaciones de la API y ya se ha implementado la aplicación de ejemplo que se indican en los tutoriales [primera](app-service-api-dotnet-get-started.md) y [segunda](app-service-api-cors-consume-javascript.md) , vaya a la sección [Configurar la autenticación en la aplicación de servicio y Azure AD](#azureauth) .

Si desea seguir este tutorial sin pasar por la primeros y segundo tutoriales, siga los pasos siguientes que se explican cómo empezar a trabajar usando un proceso automatizado para implementar la aplicación de ejemplo.

>[AZURE.NOTE] Los pasos siguientes ayudarán con el mismo punto inicial como si hizo los dos primeros tutoriales, con una excepción: Visual Studio ya no sabe qué aplicación web o una aplicación de API que cada proyecto se implementa a. Esto significa que no dispone de instrucciones exactas en este tutorial se explican cómo implementar los objetivos de la derecha. Si no se siente cómodo con averiguar cómo llevar a cabo los pasos de implementación sus propias, es mejor seguir la serie tutorial desde el [primer tutorial](app-service-api-dotnet-get-started.md) que empezar con este proceso de implementación automatizada.

1. Asegúrese de que tiene todos los requisitos previos descritos en el [primer tutorial](app-service-api-dotnet-get-started.md). Además de los requisitos previos enumerados, estos tutoriales de autenticación se suponen que ha trabajado con aplicaciones de servicio de aplicaciones web y aplicaciones de la API en Visual Studio y el portal de Azure.

2. Haga clic en el botón de **implementar a Azure** en el [archivo Léame de lista de tareas pendientes muestra repositorio](https://github.com/azure-samples/app-service-api-dotnet-todo-list/blob/master/readme.md) para implementar las aplicaciones de la API y la aplicación web. Tome nota del grupo de recursos de Azure que obtiene creado, puede utilizar esta posterior para buscar web app y nombres de aplicación de la API.
 
3. Descargar o clonar el [repositorio de ejemplo de lista de tareas pendientes](https://github.com/Azure-Samples/app-service-api-dotnet-todo-list) para obtener el código que va a trabajar localmente en Visual Studio.

## <a id="azureauth"></a>Configurar la autenticación en la aplicación de servicio y Azure AD

Ahora tiene la aplicación se ejecuta en Azure aplicación de servicio sin necesidad de que los usuarios pueden autenticados. En esta sección se agregue autenticación mediante las siguientes tareas:

* Configurar el servicio de aplicación para exigir la autenticación de Azure Active Directory (AD Azure) para llamar a la aplicación de la API de nivel intermedio.
* Crear una aplicación de Azure AD.
* Configurar la aplicación de Azure AD para enviar el token portador después de iniciar sesión a AngularJS front-end. 

Si tiene problemas mientras siguiendo las instrucciones de tutorial, consulte la sección [solucionar problemas](#troubleshooting) al final del curso. 
 
### <a name="configure-authentication-for-the-middle-tier-api-app"></a>Configurar la autenticación para la aplicación de la API de nivel intermedio

1. En el [portal de Azure](https://portal.azure.com/), vaya a la hoja de **configuración** de la aplicación de la API que ha creado para el proyecto ToDoListAPI, busque la sección de **características** y, a continuación, haga clic en **autenticación / autorización**.

    ![Portal de Azure autenticación/autorización](./media/app-service-api-dotnet-user-principal-auth/features.png)

3. En la **autenticación / autorización** módulo, haga clic **en**.

4. En la lista desplegable **acción que se ejecuta cuando no se autentica la solicitud** , seleccione **iniciar sesión con Azure Active Directory**.

    Esta opción garantiza que no hay solicitudes de unauathenticated llegarán a la aplicación de la API. 

5. **Proveedores de autenticación**, haga clic en **Azure Active Directory**.

    ![Módulo de autenticación/autorización portal Azure](./media/app-service-api-dotnet-user-principal-auth/authblade.png)

6. En el módulo de **Configuración de Azure Active Directory** , haga clic en **Express**

    ![Opción de autenticación/autorización Express portal de Azure](./media/app-service-api-dotnet-user-principal-auth/aadsettings.png)

    Con la opción **Express** , servicio de aplicación puede crear automáticamente una aplicación de Azure AD en su [inquilino](https://msdn.microsoft.com/en-us/library/azure/jj573650.aspx#BKMK_WhatIsAnAzureADTenant)de Azure AD. 

    No tienes que cree a un inquilino, porque cada cuenta de Azure automáticamente tiene uno.

7. En **modo de administración**, haga clic en **Crear una nueva aplicación de AD** si aún no está seleccionada y a continuación, observe el valor que se encuentra en el cuadro de texto de la **Aplicación de crear** ; deberá buscar esta aplicación AAD más adelante en el portal de clásico de Azure.

    ![Configuración del portal de Azure Azure AD](./media/app-service-api-dotnet-user-principal-auth/aadsettings2.png)

    Azure creará automáticamente una aplicación de Azure AD con el nombre indicado en el inquilino de Azure AD. De forma predeterminada, la aplicación de Azure AD es el mismo nombre que la aplicación de la API. Si lo prefiere, puede escribir un nombre diferente.
 
7. Haga clic en **Aceptar**.

7. En la **autenticación / autorización** módulo, haga clic en **Guardar**.

    ![Haga clic en Guardar](./media/app-service-api-dotnet-user-principal-auth/authsave.png)

Ahora solo los usuarios de su inquilino de Azure AD pueden llamar a la aplicación de la API.

### <a name="optional-test-the-api-app"></a>Opcional: Probar la aplicación de la API

1. En un explorador, vaya a la dirección URL de la aplicación de la API: en el módulo de **aplicación de la API** en el portal de Azure, haga clic en el vínculo en la **dirección URL**.  

    Se le redirige a una pantalla de inicio de sesión porque no se permiten solicitudes no autenticadas para llegar a la aplicación de la API.

    Si el explorador va a la página "creada correctamente", podría estar registrado el explorador--en ese caso, abra una ventana de InPrivate o incógnito y vaya a la dirección URL de la API la aplicación.

2. Inicie sesión con las credenciales de un usuario en su inquilino de Azure AD.

    Cuando haya iniciado sesión, aparece la página "creada correctamente" en el explorador.

9. Cierre el explorador.

### <a name="configure-the-azure-ad-application"></a>Configurar la aplicación de Azure AD

Cuando configura la autenticación de Azure AD, servicio de aplicación creará una aplicación de Azure AD. De forma predeterminada, la nueva Azure AD aplicación se configuró para proporcionar el token portador a la dirección URL de la aplicación API. En esta sección se configura la aplicación de Azure AD para proporcionar el token portador AngularJS front-end en lugar de directamente a la aplicación de la API de nivel intermedio. AngularJS front-end enviará el token a la aplicación de la API cuando llama a la aplicación de la API.

>[AZURE.NOTE] Para mantener el proceso como simple como sea posible, en este tutorial se utiliza un único Azure AD aplicación para el front-end y medio nivel de aplicación de la API. Otra opción es usar dos aplicaciones de Azure AD. En ese caso tendrá que conceder permisos de aplicación de Azure AD de front-end para llamar a la aplicación de Azure AD de nivel intermedio. Este enfoque múltiples aplicaciones daría mayor control sobre los permisos para cada nivel.

11. En el [portal de clásica Azure](https://manage.windowsazure.com/), vaya a **Azure Active Directory**.

    Debe usar el portal clásico porque algunas configuraciones de Azure Active Directory que necesiten acceder a no ya están disponibles en el portal de Azure actual.

12. En la ficha **directorio** , haga clic en el inquilino de AAD.

    ![Azure AD en portal clásica](./media/app-service-api-dotnet-user-principal-auth/selecttenant.png)

14. Haga clic en **aplicaciones > aplicaciones de mi empresa posee**y, a continuación, haga clic en la marca de verificación.

    También es posible que tenga que actualizar la página para ver la nueva aplicación.

15. En la lista de aplicaciones, haga clic en el nombre de la que creará Azure cuando habilitó la autenticación de la aplicación de la API.

    ![Ficha de Azure AD aplicaciones](./media/app-service-api-dotnet-user-principal-auth/appstab.png)

16. Haga clic en **Configurar**.

    ![Ficha de Azure AD configurar](./media/app-service-api-dotnet-user-principal-auth/configure.png)

17. Establecer la **dirección URL de inicio de sesión** a la dirección URL para la aplicación web de AngularJS, sin barra diagonal.

    Por ejemplo: https://todolistangular.azurewebsites.net

    ![Dirección URL de inicio de sesión](./media/app-service-api-dotnet-user-principal-auth/signonurlazure.png)

17. Establecer la **Dirección URL de respuesta** a la dirección URL para la aplicación web, sin barra diagonal.

    Por ejemplo: https://todolistsangular.azurewebsites.net

16. Haga clic en **Guardar**.

    ![Dirección URL de respuesta](./media/app-service-api-dotnet-user-principal-auth/replyurlazure.png)

15. En la parte inferior de la página, haga clic en **manifiesto administrar > descargar manifiesto**.

    ![Descargar manifiesto](./media/app-service-api-dotnet-user-principal-auth/downloadmanifest.png)

17. Descargue el archivo en una ubicación donde se modifican.

16. En el archivo descargado manifiesto, busque la `oauth2AllowImplicitFlow` propiedad. Cambie el valor de esta propiedad de `false` a `true`y, a continuación, guarde el archivo.

    Esta configuración se requiere acceso desde una aplicación de una sola página de JavaScript. Permite que el token portador de Oauth 2.0 que se devolverán en el fragmento de dirección URL.

16. Haga clic en **manifiesto administrar > cargar manifiesto**y cargue el archivo actualizado en el paso anterior.

    ![Cargar manifiesto](./media/app-service-api-dotnet-user-principal-auth/uploadmanifest.png)

17. Copie el valor del **Identificador de cliente** y guárdelo en otro que puede ir desde más adelante.

## <a name="configure-the-todolistangular-project-to-use-authentication"></a>Configurar el proyecto ToDoListAngular para usar la autenticación

En esta sección, cambiar AngularJS front-end para que utilice Active Directory autenticación biblioteca (ADAL) para JS para adquirir un token portador para el usuario de inicio de sesión de Azure AD. El código incluirá el token en las solicitudes HTTP enviadas al nivel intermedio, tal como se muestra en el diagrama siguiente. 

![Diagrama de autenticación de usuario](./media/app-service-api-dotnet-user-principal-auth/appdiagram.png)

Realice los cambios siguientes a los archivos en el proyecto ToDoListAngular.

1. Abra el archivo *index.html* .

2. Quite los comentarios de las líneas que hacen referencia a la biblioteca de autenticación de Active Directory (ADAL) de secuencias de comandos JS.

        <script src="app/scripts/adal.js"></script>
        <script src="app/scripts/adal-angular.js"></script>

1. Abra el archivo *app/scripts/app.js* .

2. Comentario del bloque de código marcado como "sin autenticación" y quite el bloque de código marcado como "con autenticación".

    Este cambio hace referencia al proveedor de autenticación de ADAL JS y proporciona los valores de configuración. En los siguientes pasos establecer los valores de configuración de la aplicación de la API y la aplicación de Azure AD.

8. En el código que establece el `endpoints` variable, establecer la dirección URL de la API a la dirección URL de la aplicación de la API que crea para el proyecto ToDoListAPI y establezca el identificador de aplicación de Azure AD en el identificador de cliente que ha copiado desde el portal de clásico de Azure.

    El código ahora es similar al siguiente ejemplo.

        var endpoints = {
            "https://todolistapi0121.azurewebsites.net/": "1cf55bc9-9ed8-4df31cf55bc9-9ed8-4df3"
        };

9. En la llamada a `adalProvider.init`, establezca `tenant` al nombre del inquilino y `clientId` para el mismo valor que utilizó en el paso anterior.

    El código ahora es similar al siguiente ejemplo.

        adalProvider.init(
            {
                instance: 'https://login.microsoftonline.com/', 
                tenant: 'contoso.onmicrosoft.com',
                clientId: '1cf55bc9-9ed8-4df31cf55bc9-9ed8-4df3',
                extraQueryParameter: 'nux=1',
                endpoints: endpoints
            },
            $httpProvider
            );

    Estos cambios a `app.js` especificar que el código de llamada y la llamada API están en la misma aplicación de Azure AD.

1. Abra el archivo *app/scripts/homeCtrl.js* .

2. Comentario del bloque de código marcado como "sin autenticación" y quite el bloque de código marcado como "con autenticación".

1. Abra el archivo *app/scripts/indexCtrl.js* .

2. Comentario del bloque de código marcado como "sin autenticación" y quite el bloque de código marcado como "con autenticación".

### <a name="deploy-the-todolistangular-project-to-azure"></a>Implemente el proyecto de ToDoListAngular de Azure

8. En el **Explorador de soluciones**, haga clic en el proyecto ToDoListAngular y, a continuación, haga clic en **Publicar**.

9. Haga clic en **Publicar**.

    Implementa el proyecto de Visual Studio y abre un explorador en la dirección URL de base de la aplicación web. Esto le mostrará una página de 403 error, que es normal que un intento ir a una dirección URL de Web API base desde un explorador.

    Debe hacer un cambio en la aplicación de la API de nivel intermedio antes de probar la aplicación.

10. Cierre el explorador.

## <a name="configure-the-todolistapi-project-to-use-authentication"></a>Configurar el proyecto ToDoListAPI para usar la autenticación

Actualmente se envía el proyecto ToDoListAPI "*" como la `owner` valor a ToDoListDataAPI. En esta sección, cambiar el código para enviar el identificador del usuario que ha iniciado sesión.

Realice los cambios siguientes en el proyecto ToDoListAPI.

1. Abra el archivo *Controllers/ToDoListController.cs* y, a continuación, elimine la línea en cada método de acción que establece `owner` a Azure AD `NameIdentifier` el valor de notificación. Por ejemplo:

        owner = ((ClaimsIdentity)User.Identity).FindFirst(ClaimTypes.NameIdentifier).Value;

    **Importante**: no quite los comentarios de código en el `ToDoListDataAPI` método; puede hacer más adelante para el tutorial de autenticación principal del servicio.

### <a name="deploy-the-todolistapi-project-to-azure"></a>Implemente el proyecto de ToDoListAPI de Azure

8. En el **Explorador de soluciones**, haga clic en el proyecto ToDoListAPI y, a continuación, haga clic en **Publicar**.

9. Haga clic en **Publicar**.

    Implementa el proyecto de Visual Studio y abre un explorador en la dirección URL base de la API la aplicación.

10. Cierre el explorador.

### <a name="test-the-application"></a>Probar la aplicación

9. Vaya a la dirección URL de la aplicación web, **utilizando HTTPS y no HTTP**.

8. Haga clic en la pestaña **Lista de tareas pendientes** .

    Se le pide que inicie sesión.

9. Inicie sesión con las credenciales de un usuario de su inquilino AAD.

10. Aparece la página de **Lista de tareas pendientes** .

    ![Página de lista de tareas pendientes](./media/app-service-api-dotnet-user-principal-auth/webappindex.png)

    No hay elementos pendientes aparecen porque hasta ahora han sido todos para propietario "*". Ahora el nivel intermedio está solicitando elementos para el usuario que ha iniciado sesión, y no se ha creado aún.

11. Agregar nuevos elementos de tareas pendientes para comprobar que la aplicación está funcionando.

12. En otra ventana del explorador, vaya a la dirección URL Swagger de interfaz de usuario para la aplicación de la API ToDoListDataAPI y haga clic en **ToDoList > obtener**. Escriba un asterisco para la `owner` parámetro y, a continuación, haga clic en **Pruébelo**.

    La respuesta muestra que los nuevos elementos de tareas pendientes tienen el identificador de usuario de Azure AD en la propiedad de propietario.

    ![Id. de propietario en respuesta JSON](./media/app-service-api-dotnet-user-principal-auth/todolistapiauth.png)


## <a name="building-the-projects-from-scratch"></a>Generar los proyectos desde cero

Los dos proyectos de Web API creados con la plantilla de proyecto de **Aplicación de la API de Azure** y reemplazar el controlador de valores predeterminados con un controlador ToDoList. 

Obtenga información sobre cómo crear una aplicación de una sola página AngularJS con un back-end de Web API 2 [manos en práctica: crear una aplicación de página único (SPA) con la API de Web de ASP.NET y Angular.js](http://www.asp.net/web-api/overview/getting-started-with-aspnet-web-api/build-a-single-page-application-spa-with-aspnet-web-api-and-angularjs). Para obtener información sobre cómo agregar código de autenticación de Azure AD, consulte los siguientes recursos:

* [Seguridad de aplicaciones de la página de AngularJS solo con Azure AD](../active-directory/active-directory-devquickstarts-angular.md).
* [Introducción a v1 ADAL JS](http://www.cloudidentity.com/blog/2015/02/19/introducing-adal-js-v1/)

## <a name="troubleshooting"></a>Solución de problemas

[AZURE.INCLUDE [troubleshooting](../../includes/app-service-api-auth-troubleshooting.md)]

* Asegúrese de que no confunda ToDoListAPI (nivel medio) y ToDoListDataAPI (nivel de datos). Por ejemplo, compruebe que ha agregado autenticación a la aplicación de la API de nivel intermedio, no el nivel de datos. 
* Asegúrese de que el código fuente de AngularJS hace referencia a la dirección URL de aplicación de nivel intermedio API (ToDoListAPI, no ToDoListDataAPI) y la correcta Azure AD ID de cliente. 

## <a name="next-steps"></a>Pasos siguientes

En este tutorial, ha aprendido cómo usar autenticación de servicio de aplicación para una aplicación de API y cómo llamar a la aplicación de la API mediante el uso de la biblioteca de ADAL JS. En el siguiente tutorial aprenderá cómo [proteger el acceso a la aplicación de la API para escenarios de servicio para el servicio](app-service-api-dotnet-service-principal-auth.md).

