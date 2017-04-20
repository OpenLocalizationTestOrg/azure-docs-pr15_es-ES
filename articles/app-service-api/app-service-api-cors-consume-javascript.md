<properties
    pageTitle="En la aplicación de servicio de soporte técnico CORS | Microsoft Azure"
    description="Aprenda a usar CORS del soporte técnico de la aplicación de servicio de Azure Azure."
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
    ms.topic="get-started-article"
    ms.date="08/27/2016"
    ms.author="rachelap"/>

# <a name="consume-an-api-app-from-javascript-using-cors"></a>Utilizar una aplicación de la API de JavaScript mediante CORS

Aplicación de servicio ofrece compatibilidad integrada para [Uso compartido de recursos de origen de cruz (CORS)](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing), que permite a los clientes realizar llamadas de dominios a las API que se hospedan en las aplicaciones de la API de JavaScript. Aplicación de servicio le permite configurar el acceso CORS a la API sin escribir código en la API.

Este artículo contiene dos secciones:

* En general, la sección de [cómo configurar CORS](#corsconfig) explica cómo configurar CORS para cualquier aplicación de la API, aplicación web o una aplicación móvil. Se aplica igualmente a todos los marcos que son compatibles con la aplicación de servicio, incluidos .NET, Node.js y Java. 

* A partir de la sección [continuar los tutoriales de .NET Introducción](#tutorialstart) , el artículo es un tutorial que muestra que CORS admita basándose en lo que hizo en [las aplicaciones de API primera introducción tutorial de inicio](app-service-api-dotnet-get-started.md). 

## <a id="corsconfig"></a>Cómo configurar CORS en el servicio de aplicación de Azure

Puede configurar CORS en el portal de Azure o mediante herramientas de [Administrador de recursos de Azure](../azure-resource-manager/resource-group-overview.md) .

#### <a name="configure-cors-in-the-azure-portal"></a>Configurar CORS en el portal de Azure

8. En un explorador, vaya al [portal de Azure](https://portal.azure.com/).

2. Haga clic en **Servicios de aplicación**y, a continuación, haga clic en el nombre de la aplicación de la API.

    ![Seleccione la aplicación de API de portal](./media/app-service-api-cors-consume-javascript/browseapiapps.png)

10. En el módulo de **configuración** que se abre a la derecha del módulo de **aplicación de la API** , busque la sección **API** y, a continuación, haga clic en **CORS**.

    ![Seleccione CORS en el módulo de configuración](./media/app-service-api-cors-consume-javascript/clicksettings.png)

11. En el texto del cuadro Escriba la dirección URL o las direcciones URL que desea permitir llamadas de JavaScript proceden.


    Por ejemplo, si implementa la aplicación de JavaScript para una aplicación web denominada todolistangular, escriba "https://todolistangular.azurewebsites.net". Como alternativa, puede escribir un asterisco (*) para especificar que se aceptarán todos los dominios de origen.


13. Haga clic en **Guardar**.

    ![Haga clic en Guardar](./media/app-service-api-cors-consume-javascript/corsinportal.png)

    Después de hacer clic en **Guardar**, la aplicación de la API aceptará llamadas de JavaScript desde la dirección URL.

#### <a name="configure-cors-by-using-azure-resource-manager-tools"></a>Configurar CORS mediante herramientas de administrador de recursos de Azure

También puede configurar CORS para una aplicación de API con [plantillas de administrador de recursos de Azure](../resource-group-authoring-templates.md) en herramientas de línea de comandos como [PowerShell de Azure](../powershell-install-configure.md) y [Azure CLI](../xplat-cli-install.md). 

Para obtener un ejemplo de un administrador de recursos de Azure de plantilla que establezca la propiedad CORS, abra el [archivo azuredeploy.json en el repositorio de aplicación de ejemplo de este tutorial](https://github.com/azure-samples/app-service-api-dotnet-todo-list/blob/master/azuredeploy.json). Busque la sección de la plantilla que tiene un aspecto similar al ejemplo siguiente:

        "cors": {
            "allowedOrigins": [
                "todolistangular.azurewebsites.net"
            ]
        }

## <a id="tutorialstart"></a>Continuar con el tutorial de introducción a .NET

Si está siguiendo la serie Node.js o Java de iniciación para aplicaciones de la API, ha finalizado la serie de introducción al obtener. Vaya a la sección de [pasos siguientes](#next-steps) para buscar sugerencias para aprender más sobre las aplicaciones de la API.

El resto de este artículo es una continuación de la serie de .NET-Introducción y se supone que se ha completado correctamente [el primer tutorial](app-service-api-dotnet-get-started.md).

## <a name="deploy-the-todolistangular-project-to-a-new-web-app"></a>Implemente el proyecto ToDoListAngular una nueva aplicación web

En [el primer tutorial](app-service-api-dotnet-get-started.md), se crean una aplicación de la API de nivel intermedio y una aplicación de API de nivel de datos. En este tutorial se crea una aplicación web de aplicación de una sola página (SPA) esa aplicación de la API de nivel intermedio de llamadas. Para que el SPA funcione, debe habilitar CORS en la aplicación de la API de nivel intermedio. 

En la [aplicación de ejemplo ToDoList](https://github.com/Azure-Samples/app-service-api-dotnet-todo-list), el proyecto de ToDoListAngular es un cliente de AngularJS simple que llama el proyecto ToDoListAPI Web API de nivel intermedio. El código de JavaScript en el archivo *app/scripts/todoListSvc.js* llama a la API mediante el proveedor de HTTP AngularJS. 

        angular.module('todoApp')
        .factory('todoListSvc', ['$http', function ($http) {

            $http.defaults.useXDomain = true;
            delete $http.defaults.headers.common['X-Requested-With']; 
        
            return {
                getItems : function(){
                    return $http.get(apiEndpoint + '/api/TodoList');
                },

                /* Get by ID, Put, and Delete methods not shown */

                postItem : function(item){
                    return $http.post(apiEndpoint + '/api/TodoList', item);
                }
            };
        }]);

### <a name="create-a-new-web-app-for-the-todolistangular-project"></a>Crear una nueva aplicación web para el proyecto ToDoListAngular

El procedimiento para crear una nueva aplicación web de servicio de aplicaciones e implemente un proyecto es similar a lo que veía para [crear e implementar una aplicación de la API en el primer tutorial de esta serie](app-service-api-dotnet-get-started.md#createapiapp). La única diferencia es que el tipo de aplicación **Web App** en lugar de la **API de aplicación**.  Para capturas de pantalla de los cuadros de diálogo, vea 

1. En el **Explorador de soluciones**, haga clic en el proyecto ToDoListAngular y, a continuación, haga clic en **Publicar**.

3.  En la pestaña **perfil** del Asistente para la **Publicación Web** , haga clic en **Servicio de aplicación de Microsoft Azure**.

5. En el cuadro de diálogo **Servicios de aplicación** , haga clic en **nuevo**.

3. En la pestaña de **hospedaje** del cuadro de diálogo **Crear aplicación de servicio** , escriba un **Nombre de la aplicación Web** que sea único en el dominio *azurewebsites.net* . 

5. Elija la Azure **suscripción** que desea trabajar.

6. En la lista desplegable de **Grupo de recursos** , elija el mismo grupo de recursos que creó anteriormente.

4. En la lista desplegable del **Plan de servicios de aplicación** , elija el mismo plan que creó anteriormente. 

7. Haga clic en **crear**.

    Visual Studio crea la aplicación web, crea un perfil de publicación y muestra el paso de **conexión** del Asistente para la **Publicación Web** .

    No haga clic en **Publicar** todavía. En la siguiente sección, puede configurar la nueva aplicación web para llamar a la aplicación de la API de nivel intermedio que se ejecuta en la aplicación de servicio. 

### <a name="set-the-middle-tier-url-in-web-app-settings"></a>Establecer la dirección URL de nivel intermedio en la configuración de la aplicación web

1. Vaya al [portal de Azure](https://portal.azure.com/)y, a continuación, desplácese hasta el módulo de **Aplicación Web** para la aplicación web que creó para hospedar el proyecto TodoListAngular (front-end).

2. Haga clic en **Configuración > configuración de la aplicación**.

3. En la sección **configuración de la aplicación** , agregue la clave y el valor siguiente:

  	|Clave|Valor|Ejemplo
  	|---|---|---|
  	|toDoListAPIURL|el nombre de la aplicación de nivel intermedio API https://{Your} .azurewebsites .net|https://todolistapi0121.azurewebsites.NET|

4. Haga clic en **Guardar**.

    Cuando el código se ejecuta en Azure, este valor reemplaza la dirección URL del host local que se encuentra en el archivo *Web.config* . 

    El código que obtiene el valor de configuración se encuentra en *index.cshtml*:

        <script type="text/javascript">
            var apiEndpoint = "@System.Configuration.ConfigurationManager.AppSettings["toDoListAPIURL"]";
        </script>
        <script src="app/scripts/todoListSvc.js"></script>

    El código en *todoListSvc.js* utiliza la configuración:

        return {
            getItems : function(){
                return $http.get(apiEndpoint + '/api/TodoList');
            },
            getItem : function(id){
                return $http.get(apiEndpoint + '/api/TodoList/' + id);
            },
            postItem : function(item){
                return $http.post(apiEndpoint + '/api/TodoList', item);
            },
            putItem : function(item){
                return $http.put(apiEndpoint + '/api/TodoList/', item);
            },
            deleteItem : function(id){
                return $http({
                    method: 'DELETE',
                    url: apiEndpoint + '/api/TodoList/' + id
                });
            }
        };

### <a name="deploy-the-todolistangular-web-project-to-the-new-web-app"></a>Implemente el proyecto de web ToDoListAngular la nueva aplicación web

*  En Visual Studio, en el paso de **conexión** del Asistente de **Publicación Web** , haga clic en **Publicar**.

    Visual Studio implementa el proyecto ToDoListAngular a la nueva aplicación web y abre un explorador a la dirección URL de la aplicación web. 

### <a name="test-the-application-without-cors-enabled"></a>Probar la aplicación sin CORS habilitado 

2. En el Explorador de herramientas de desarrollador, abra la ventana de la consola.

3. En la ventana del explorador que muestra la UI AngularJS, haga clic en el vínculo de la **Lista de tareas pendientes** .

    El código JavaScript intenta llamar a la aplicación de la API de nivel intermedio, pero se produce un error en la llamada porque el front-end se ejecuta en un dominio diferente que el back-end. La ventana del explorador consola de herramientas de desarrollador muestra un mensaje de error de origen de la cruz.

    ![Mensaje de error de origen de cruz](./media/app-service-api-cors-consume-javascript/consoleaccessdenied.png)

## <a name="configure-cors-for-the-middle-tier-api-app"></a>Configurar CORS para la aplicación de la API de nivel intermedio

En esta sección, configure el valor de CORS en Azure para la aplicación de la API ToDoListAPI de nivel intermedio. Esta configuración permite la aplicación de la API para recibir llamadas de JavaScript desde la aplicación web que ha creado para el proyecto ToDoListAngular de nivel intermedio.

8. En un explorador, vaya al [portal de Azure](https://portal.azure.com/).

2. Haga clic en **Servicios de aplicación**y, a continuación, haga clic en la aplicación de API de ToDoListAPI (nivel medio).

    ![Seleccione la aplicación de API de portal](./media/app-service-api-cors-consume-javascript/browseapiapps.png)

10. En el módulo de **configuración** que se abre a la derecha del módulo de **aplicación de la API** , busque la sección **API** y, a continuación, haga clic en **CORS**.

    ![Seleccione CORS de portal](./media/app-service-api-cors-consume-javascript/clicksettings.png)

12. En el cuadro de texto, escriba la dirección URL para la aplicación web de ToDoListAngular (front-end). Por ejemplo, si implementa el proyecto ToDoListAngular en una aplicación web denominada todolistangular0121, permitir llamadas desde la dirección URL `https://todolistangular0121.azurewebsites.net`.

    Como alternativa, puede escribir un asterisco (*) para especificar que se aceptarán todos los dominios de origen.

13. Haga clic en **Guardar**.

    ![Haga clic en Guardar](./media/app-service-api-cors-consume-javascript/corsinportal.png)

    Después de hacer clic en **Guardar**, la aplicación de la API aceptará llamadas de JavaScript desde la dirección URL especificada. En esta captura de pantalla, la aplicación de la API ToDoListAPI0223 aceptará llamadas de cliente de JavaScript desde la aplicación web de ToDoListAngular.

### <a name="test-the-application-with-cors-enabled"></a>Probar la aplicación con CORS habilitado

* Abra un explorador a la dirección URL de HTTPS de la aplicación web. 

    En este momento la aplicación le permite ver, agregar, editar y eliminar elementos de tareas pendientes. 

    ![Página de aplicación de ejemplo de lista de tareas pendientes](./media/app-service-api-cors-consume-javascript/corssuccess.png)

## <a name="app-service-cors-versus-web-api-cors"></a>Aplicación de servicio CORS frente a Web API CORS

En un proyecto de Web API, puede instalar el paquete de NuGet [Microsoft.AspNet.WebApi.Cors](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.Cors/) para especificar en código JavaScript llama a los dominios que se acepte la API de.
 
Compatibilidad con la API CORS Web es más flexible que soporte CORS de servicio de aplicación. Por ejemplo, en el código puede especificar diferentes aceptadas orígenes diferentes métodos de acción, mientras que para la aplicación de servicio CORS especifique un conjunto de orígenes aceptadas para todos los métodos de una aplicación de API de.

> [AZURE.NOTE] No intente usar Web API CORS y CORS de servicio de aplicación en una aplicación de API. Aplicación de servicio CORS tendrá prioridad y Web API CORS no tiene ningún efecto. Por ejemplo, si habilitar un dominio de origen en la aplicación de servicio y habilitar todos los dominios de origen en el código de la API de Web, la aplicación de la API de Azure sólo aceptará llamadas desde el dominio especificado en Azure.

### <a name="how-to-enable-cors-in-web-api-code"></a>Cómo habilitar CORS en el código de la API de Web

Los pasos siguientes resumen el proceso para habilitar la compatibilidad de Web API CORS. Para obtener más información, vea [Habilitar Origin entre solicitudes en ASP.NET Web API 2](http://www.asp.net/web-api/overview/security/enabling-cross-origin-requests-in-web-api).

1. En un proyecto de Web API, instale el paquete de NuGet [Microsoft.AspNet.WebApi.Cors](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.Cors/) .

1. Incluir un `config.EnableCors()` línea de código en el método de **registro** de la clase **WebApiConfig** , como en el ejemplo siguiente. 

        public static class WebApiConfig
        {
            public static void Register(HttpConfiguration config)
            {
                // Web API configuration and services
                
                // The following line enables you to control CORS by using Web API code
                config.EnableCors();
    
                // Web API routes
                config.MapHttpAttributeRoutes();
    
                config.Routes.MapHttpRoute(
                    name: "DefaultApi",
                    routeTemplate: "api/{controller}/{id}",
                    defaults: new { id = RouteParameter.Optional }
                );
            }
        }

1. En el controlador de Web API, agregue un `using` declaración de la `System.Web.Http.Cors` espacio de nombres y agregue la `EnableCors` atributo a métodos de acción individuales o la clase de controlador. En el ejemplo siguiente, la compatibilidad CORS se aplica en el controlador de todo.

        namespace ToDoListAPI.Controllers 
        {
            [HttpOperationExceptionFilterAttribute]
            [EnableCors(origins:"https://todolistangular0121.azurewebsites.net", headers:"accept,content-type,origin,x-my-header", methods: "get,post")]
            public class ToDoListController : ApiController
 
## <a name="using-azure-api-management-with-api-apps"></a>Utilización de administración de la API de Azure con aplicaciones de la API

Si utiliza la administración de la API de Azure con una aplicación de API, configurar CORS en administración de la API en lugar de en la aplicación de la API. Para obtener más información, consulte los siguientes recursos:

* [Información general de administración de API Azure (vídeo: CORS comienza en 12:10)](https://azure.microsoft.com/documentation/videos/azure-api-management-overview/)
* [Administración de la API entre las directivas de dominio](https://msdn.microsoft.com/library/azure/dn894084.aspx#CORS)
 
## <a name="troubleshooting"></a>Solución de problemas

En caso de que tiene un problema al seguir este tutorial, estas son algunas ideas para solucionar problemas.

* Asegúrese de que está usando la versión más reciente de [Azure SDK para .NET de Visual Studio de 2015](http://go.microsoft.com/fwlink/?linkid=518003).

* Asegúrese de que haya escrito `https` en la configuración de CORS y asegúrese de que está usando `https` para ejecutar la aplicación web front-end.

* Asegúrese de que ha escrito la configuración de CORS en la aplicación de la API de nivel intermedio, no en la aplicación web front-end.

* Si está configurando CORS en código de la aplicación y el servicio de aplicación de Azure, tenga en cuenta que la configuración de aplicación servicio CORS invalidará cualquier cosa que hace en el código de la aplicación. 

Para obtener más información acerca de Visual Studio características que simplifican la solución de problemas, consulte [aplicaciones de servicio de solución de problemas de aplicaciones de Azure en Visual Studio](../app-service-web/web-sites-dotnet-troubleshoot-visual-studio.md).

## <a name="next-steps"></a>Pasos siguientes 

En este artículo, hemos visto cómo habilitar el soporte técnico de CORS de servicio de aplicación para que el código de JavaScript de cliente puede llamar a una API en un dominio diferente. Para obtener más información sobre las aplicaciones de la API, leer la [Introducción a la autenticación en la aplicación de servicio](../app-service/app-service-authentication-overview.md)y, a continuación, vaya al tutorial de [autenticación de usuario para las aplicaciones de la API](app-service-api-dotnet-user-principal-auth.md) .
