<properties
    pageTitle="Autenticación principal del servicio para las aplicaciones de la API de servicio de la aplicación de Azure | Microsoft Azure"
    description="Obtenga información sobre cómo proteger una aplicación de API de servicio de la aplicación de Azure para escenarios de servicio para el servicio."
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

# <a name="service-principal-authentication-for-api-apps-in-azure-app-service"></a>Autenticación principal del servicio para las aplicaciones de la API de servicio de la aplicación de Azure

## <a name="overview"></a>Información general

En este artículo se explica cómo usar autenticación de servicio de aplicaciones para el acceso *interno* para las aplicaciones de la API. Un escenario interno es que tenga una aplicación de API que desea que sean sólo por su propio código de la aplicación. La forma recomendada para implementar este escenario en la aplicación de servicio es usar Azure AD para proteger la aplicación de la API llamada. Llamar a la aplicación de API protegida con un token portador obtenida de Azure AD al proporcionar la identidad de la aplicación credenciales (principal del servicio). Para alternativas al uso de Azure AD, vea la sección **autenticación de servicio de servicio** de la [información general de autenticación de servicio de aplicaciones de Azure](../app-service/app-service-authentication-overview.md#service-to-service-authentication).

En este artículo, aprenderá:

* Cómo usar Azure Active Directory (AD Azure) para proteger una aplicación de API de acceso no autenticado.
* Cómo utilizar una aplicación de API protegida desde una aplicación de API, aplicación web o una aplicación móvil mediante credenciales de Azure AD servicio principal (identidad de la aplicación). Para obtener información acerca de cómo consumir desde una aplicación de lógica, consulte [usar la API personalizada hospedada en servicio de la aplicación con aplicaciones de lógica](../app-service-logic/app-service-logic-custom-hosted-api.md).
* Cómo asegurarse de que la aplicación de la API protegida no se puede llamar desde un explorador por usuarios conectados.
* Cómo para asegurarse de que la aplicación API protegida solo se puede llamar por un determinado Azure AD service principal.

El artículo contiene dos secciones:

* La sección [cómo configurar la autenticación principal del servicio en la aplicación de servicio de Azure](#authconfig) en general explica cómo configurar la autenticación de cualquier aplicación de la API y cómo utilizar la aplicación de la API protegida. En esta sección se aplica igualmente a todos los marcos compatibles con la aplicación de servicio, incluidos .NET, Node.js y Java.

* A partir de la sección [continuar los tutoriales de .NET Introducción](#tutorialstart) , el tutorial le guiará para configurar un escenario de "acceso interno" para una aplicación de ejemplo de .NET ejecuta en la aplicación de servicio. 

## <a id="authconfig"></a>Cómo configurar autenticación principal del servicio en el servicio de aplicación de Azure

Esta sección proporciona instrucciones generales que se aplican a cualquier aplicación de la API. Para pasos específicos de la aplicación de ejemplo para hacer el .NET de lista, vaya a [continuar la serie de tutoriales de aplicaciones de la API de .NET](#tutorialstart).

1. En el [portal de Azure](https://portal.azure.com/), vaya a la hoja de **configuración** de la aplicación de la API que desea proteger y, a continuación, busque la sección **características** y haga clic en **autenticación / autorización**.

    ![Autenticación/autorización en el portal de Azure](./media/app-service-api-dotnet-user-principal-auth/features.png)

3. En la **autenticación / autorización** módulo, haga clic **en**.

4. En la lista desplegable **acción que se ejecuta cuando no se autentica la solicitud** , seleccione **iniciar sesión con Azure Active Directory** .

5. En **Los proveedores de autenticación**, seleccione **Azure Active Directory**.

    ![Módulo de autenticación o autorización en el portal de Azure](./media/app-service-api-dotnet-user-principal-auth/authblade.png)

6. Configurar el módulo de **Configuración de Azure Active Directory** para crear una nueva aplicación de Azure AD o usar una aplicación de Azure AD existente si ya tiene una que desee usar.

    Escenarios internos normalmente implican una aplicación de API llamar a una aplicación de API. Puede usar Azure independiente aplicaciones de AD para cada aplicación de la API o una aplicación de Azure AD.

    Para obtener instrucciones detalladas sobre este módulo, consulte [cómo configurar la aplicación de servicio de aplicaciones para usar el inicio de sesión de Azure Active Directory](../app-service-mobile/app-service-mobile-how-to-configure-active-directory-authentication.md).

7. Cuando haya terminado con la hoja de configuración del proveedor de servicios de autenticación, haga clic en **Aceptar**.

7. En la **autenticación / autorización** módulo, haga clic en **Guardar**.

    ![Haga clic en Guardar](./media/app-service-api-dotnet-service-principal-auth/authsave.png)

Cuando haya hecho esto, el servicio de aplicación solo permite las solicitudes de las personas que llaman en el configurado inquilino de Azure AD. No es necesario código de autenticación o autorización en la aplicación de la API protegida. El token portador se pasa a la aplicación de la API junto con frecuencia reclamaciones utilizados en los encabezados HTTP y, a continuación, puede leer esta información en el código para validar que las solicitudes proceden de un llamador determinado, como una entidad de seguridad del servicio.

Esta funcionalidad de autenticación funciona del mismo modo para todos los idiomas que admite el servicio de aplicaciones, incluidos .NET, Node.js y Java. 

#### <a name="how-to-consume-the-protected-api-app"></a>Cómo utilizar la aplicación de la API protegida

El llamador debe proporcionar un token portador de Azure AD con llamadas a API. Para obtener un token portador con las credenciales de servicio principal, el llamador usa Active Directory Authentication Library (ADAL para [.NET](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory), [Node.js](https://github.com/AzureAD/azure-activedirectory-library-for-nodejs)o [Java](https://github.com/AzureAD/azure-activedirectory-library-for-java)). Para obtener un símbolo, el código que llama ADAL proporciona a ADAL la siguiente información:

* El nombre de su inquilino de Azure AD.
* El identificador de cliente y el secreto de cliente (tecla de aplicación) de la aplicación de Azure AD asociada a la persona que llama.
* Identificador de cliente de la aplicación de Azure AD asociada con la aplicación de la API protegida. (Si se usa una aplicación de Azure AD, es el mismo ID de cliente que el autor de la llamada).

Estos valores están disponibles en las páginas de Azure AD del [portal clásica Azure](https://manage.windowsazure.com/).

Una vez que se ha adquirido el token, el llamador incluye con las solicitudes HTTP en el encabezado de autorización.  Aplicación de servicio valida el token y permite a las solicitudes llegar a la aplicación de la API protegida.

#### <a name="how-to-protect-the-api-app-from-access-by-users-in-the-same-tenant"></a>Cómo proteger la aplicación de la API de acceso a los usuarios en el mismo inquilino

Tokens portador para los usuarios en el mismo inquilino se consideran válidos para la aplicación de la API protegida.  Si desea asegurarse de que solo una entidad de seguridad de servicio puede llamar a la aplicación de la API protegida, agregar código en la aplicación de API protegida para validar las notificaciones del token siguientes:

* `appid`debe ser el identificador de cliente de la aplicación de Azure AD que está asociado con el llamador. 
* `oid`(`objectidentifier`) debe ser el identificador principal del servicio de la persona que llama. 

Aplicación de servicio también proporciona la `objectidentifier` reclamar en el encabezado de X MS cliente capital identificador.

### <a name="how-to-protect-the-api-app-from-browser-access"></a>Cómo proteger la aplicación de la API de acceso a explorador

Si no validar reclamaciones en el código de la aplicación de la API protegida y usar otra aplicación de Azure AD para la aplicación de la API protegida, asegúrese de que la dirección URL de respuesta de la aplicación de Azure AD no es la misma que la dirección URL base de la API la aplicación. Si la dirección URL de respuesta apunta directamente a la aplicación de la API protegida, un usuario en el mismo inquilino de Azure AD podría vaya a la aplicación de la API, inicie sesión y llamar correctamente a la API.

## <a id="tutorialstart"></a>Continuar la serie de tutoriales de aplicaciones de la API de .NET

Si está siguiendo la serie de tutoriales Node.js o Java para las aplicaciones de la API, vaya a la sección de [pasos siguientes](#next-steps) . 

El resto de este artículo continúa la serie de tutoriales de aplicaciones de la API de .NET y se supone que se ha completado el [tutorial de autenticación de usuario](app-service-api-dotnet-user-principal-auth.md) y tiene la aplicación de ejemplo que se ejecuta en Azure con la autenticación de usuario habilitada.

## <a name="set-up-authentication-in-azure"></a>Configurar la autenticación en Azure

En esta sección Configurar servicios de aplicación para que las solicitudes HTTP sola permite llegar a la aplicación de API de nivel de datos son los que tienen Azure válida tokens portador de AD. 

En la siguiente sección, puede configurar la aplicación de la API de nivel intermedio para enviar las credenciales de la aplicación a Azure AD, volver un token portador y enviar el token portador a la aplicación de API de nivel de datos. Este proceso se muestra en el diagrama.

![Diagrama de servicio de autenticación](./media/app-service-api-dotnet-service-principal-auth/appdiagram.png)

Si tiene problemas mientras siguiendo las instrucciones de tutorial, consulte la sección [solucionar problemas](#troubleshooting) al final del curso. 

1. En el [portal de Azure](https://portal.azure.com/), vaya a la hoja de **configuración** de la aplicación de la API que creó para la aplicación de API de ToDoListDataAPI (nivel de datos) y, a continuación, haga clic en **configuración**.

2. En el módulo de **configuración** , busque la sección de **características** y, a continuación, haga clic en **autenticación / autorización**.

    ![Autenticación/autorización en el portal de Azure](./media/app-service-api-dotnet-user-principal-auth/features.png)

3. En la **autenticación / autorización** módulo, haga clic **en**.

4. En la lista desplegable **acción que se ejecuta cuando no se autentica la solicitud** , seleccione **iniciar sesión con Azure Active Directory**.

    Esta es la configuración que hace que el servicio de aplicación para asegurarse de que solo se han autenticado las solicitudes de encontrar la aplicación de la API. Para las solicitudes que tengan tokens portador válido, servicio de aplicación pasa los tokens a lo largo de la aplicación de la API y rellena encabezados HTTP con notificaciones utilizados con frecuencia para asegurarse de que la información más fácilmente disponible para el código.

5. **Proveedores de autenticación**, haga clic en **Azure Active Directory**.

    ![Módulo de autenticación o autorización en el portal de Azure](./media/app-service-api-dotnet-user-principal-auth/authblade.png)

6. En el módulo de **Configuración de Azure Active Directory** , haga clic en **Express**.

    Con la **Express** opción Azure puede crear automáticamente una aplicación de AAD en su [inquilino](https://msdn.microsoft.com/en-us/library/azure/jj573650.aspx#BKMK_WhatIsAnAzureADTenant)de Azure AD. 

    No tienes que cree a un inquilino, porque cada cuenta de Azure automáticamente tiene uno.

7. En **modo de administración**, haga clic en **Crear una nueva aplicación de AD** si aún no está seleccionada.

    El portal conecta el cuadro de entrada de **Aplicación crear** con un valor predeterminado. De forma predeterminada, la aplicación de Azure AD es el mismo nombre que la aplicación de la API. Si lo prefiere, puede escribir un nombre diferente.
    
    ![Configuración de Azure AD](./media/app-service-api-dotnet-service-principal-auth/aadsettings.png)

    **Nota**: como alternativa, puede usar una sola Azure AD aplicación para la aplicación de la API llamada y la aplicación de la API protegida. Si ha elegido alternativa, no necesarias la opción de **Crear una nueva aplicación de AD** aquí porque ya ha creado una aplicación de Azure AD anteriormente en el tutorial de autenticación de usuario. En este tutorial, usará separar aplicaciones de Azure AD para la aplicación de la API llamada y la aplicación de la API protegida.

8. Anote el valor que se encuentra en el cuadro de entrada de **Aplicación crear** ; deberá buscar esta aplicación AAD más adelante en el portal de clásico de Azure.

7. Haga clic en **Aceptar**.

10. En la **autenticación / autorización** módulo, haga clic en **Guardar**.

    ![Haga clic en Guardar](./media/app-service-api-dotnet-service-principal-auth/saveauth.png)

    Servicio de aplicación, se crea una aplicación de Azure Active Directory con la **dirección URL de inicio de sesión** y **Dirección URL de respuesta** se establece automáticamente a la dirección URL de la aplicación de la API. El último valor permite a los usuarios de su inquilino de AAD para iniciar sesión y obtener acceso a la aplicación de la API.

### <a name="verify-that-the-api-app-is-protected"></a>Compruebe que la aplicación de la API está protegida

1. En un explorador, vaya a la dirección URL de la aplicación de la API: en el módulo de **aplicación de la API** en el portal de Azure, haga clic en el vínculo en la **dirección URL**. 

    Se le redirige a una pantalla de inicio de sesión porque no se permiten solicitudes no autenticadas para llegar a la aplicación de la API. 

    Si su explorador ir a la interfaz de usuario Swagger, podría estar registrado el explorador--en ese caso, abra una ventana de InPrivate o incógnito y vaya a la dirección URL Swagger de interfaz de usuario.

18. Inicie sesión con las credenciales de un usuario de su inquilino AAD.

    Cuando haya iniciado sesión, aparece la página "creada correctamente" en el explorador.

## <a name="configure-the-todolistapi-project-to-acquire-and-send-the-azure-ad-token"></a>Configurar el proyecto ToDoListAPI para adquirir y enviar el token de Azure AD

En esta sección realizar las siguientes tareas:

* Agregar código en la aplicación de la API de nivel intermedio que usa las credenciales de la aplicación de Azure AD para adquirir un símbolo y enviar con las solicitudes HTTP a la aplicación de API de nivel de datos.
* Obtener las credenciales que necesita de Azure AD.
* Escriba las credenciales en la configuración de entorno de tiempo de ejecución de servicio de la aplicación de Azure en la aplicación de la API de nivel intermedio. 

### <a name="configure-the-todolistapi-project-to-acquire-and-send-the-azure-ad-token"></a>Configurar el proyecto ToDoListAPI para adquirir y enviar el token de Azure AD

Realice los cambios siguientes en el proyecto ToDoListAPI en Visual Studio.

1. Quite los comentarios de todo el código en el archivo *ServicePrincipal.cs* .

    Este es el código que usa ADAL para .NET para adquirir el token portador de Azure AD.  Usa varios valores de configuración que configure más adelante en el entorno de tiempo de ejecución de Azure. Este es el código: 

        public static class ServicePrincipal
        {
            static string authority = ConfigurationManager.AppSettings["ida:Authority"];
            static string clientId = ConfigurationManager.AppSettings["ida:ClientId"];
            static string clientSecret = ConfigurationManager.AppSettings["ida:ClientSecret"];
            static string resource = ConfigurationManager.AppSettings["ida:Resource"];
        
            public static AuthenticationResult GetS2SAccessTokenForProdMSA()
            {
                return GetS2SAccessToken(authority, resource, clientId, clientSecret);
            }
        
            static AuthenticationResult GetS2SAccessToken(string authority, string resource, string clientId, string clientSecret)
            {
                var clientCredential = new ClientCredential(clientId, clientSecret);
                AuthenticationContext context = new AuthenticationContext(authority, false);
                AuthenticationResult authenticationResult = context.AcquireToken(
                    resource,
                    clientCredential);
                return authenticationResult;
            }
        }

    **Nota:** Este código requiere la ADAL para el paquete NuGet .NET (Microsoft.IdentityModel.Clients.ActiveDirectory), que ya está instalado en el proyecto. Si está creando este proyecto desde cero, tendrá que instalar este paquete. Este paquete no se instala automáticamente de la plantilla de proyecto nuevo de aplicación de API.

2. En los *Controladores de/ToDoListController*, quite el código de la `NewDataAPIClient` método que agrega el token a HTTP solicita en el encabezado de autorización.

        client.HttpClient.DefaultRequestHeaders.Authorization =
            new AuthenticationHeaderValue("Bearer", ServicePrincipal.GetS2SAccessTokenForProdMSA().AccessToken);

3. Implemente el proyecto ToDoListAPI. (Haga clic en el proyecto y luego haga clic en **Publicar > Publicar**.)

    Implementa el proyecto de Visual Studio y abre un explorador en la dirección URL de base de la aplicación web. Esto le mostrará una página de 403 error, que es normal que un intento ir a una dirección URL de Web API base desde un explorador.

4. Cierre el explorador.

### <a name="get-azure-ad-configuration-values"></a>Obtener valores de configuración de Azure AD

11. En el [portal de clásica Azure](https://manage.windowsazure.com/), vaya a **Azure Active Directory**.

12. En la ficha **directorio** , haga clic en el inquilino de AAD.

14. Haga clic en **aplicaciones > aplicaciones de mi empresa posee**y, a continuación, haga clic en la marca de verificación.

15. En la lista de aplicaciones, haga clic en el nombre de la que creará Azure cuando habilitó la autenticación para la aplicación de API de ToDoListDataAPI (nivel de datos).

16. Haga clic en la ficha **Configurar** .

5. Copie el valor del **Identificador de cliente** y guardarlo en un lugar que puede ir desde más adelante. 

8. En el portal de clásico Azure volver a la lista de **aplicaciones que es propietario de mi compañía**y haga clic en la aplicación de AAD que ha creado para la aplicación de la API ToDoListAPI de nivel intermedio (lo que creó en el tutorial anterior, no lo que ha creado en este tutorial).

16. Haga clic en la ficha **Configurar** .

5. Copie el valor del **Identificador de cliente** y guardarlo en un lugar que puede ir desde más adelante.

6. En **las teclas**, seleccione **1 año** de la lista desplegable **Seleccione duración** .

6. Haga clic en **Guardar**.

    ![Generar clave de aplicación](./media/app-service-api-dotnet-service-principal-auth/genkey.png)

7. Copie el valor de clave y guardarlo en un lugar que puede ir desde más adelante.

    ![Copiar nueva tecla de aplicación](./media/app-service-api-dotnet-service-principal-auth/genkeycopy.png)

### <a name="configure-azure-ad-settings-in-the-middle-tier-api-apps-runtime-environment"></a>Configurar las opciones de Azure AD en el entorno de tiempo de ejecución de la aplicación de la API de nivel intermedio

1. Vaya al [portal de Azure](https://portal.azure.com/)y, a continuación, desplácese hasta el módulo de **Aplicación de la API** para la aplicación de la API que hospeda el proyecto TodoListAPI (nivel medio).

2. Haga clic en **Configuración > configuración de la aplicación**.

3. En la sección **configuración de la aplicación** , agregue las claves y los valores siguientes:

  	| **Clave** | Entidad de ida: |
  	|---|---|
  	| **Valor** | https://Login.microsoftonline.com/ {su nombre del inquilino de Azure AD} |
  	| **Ejemplo** | https://Login.microsoftonline.com/contoso.onmicrosoft.com |

  	| **Clave** | ida: ClientId |
  	|---|---|
  	| **Valor** | Identificador de cliente de la aplicación de llamada (nivel medio - ToDoListAPI) |
  	| **Ejemplo** | 960adec2-b74a-484a-960adec2-b74a-484a |

  	| **Clave** | ida: ClientSecret |
  	|---|---|
  	| **Valor** | Tecla de aplicación de la aplicación de llamada (nivel medio - ToDoListAPI) |
  	| **Ejemplo** | e65e8fc9-5f6b-48e8-e65e8fc9-5f6b-48e8 |

  	| **Clave** | ida: recursos |
  	|---|---|
  	| **Valor** | Identificador de cliente de la aplicación llamada (nivel de datos - ToDoListDataAPI) |
  	| **Ejemplo** | e65e8fc9-5f6b-48e8-e65e8fc9-5f6b-48e8 |

    **Nota**: para `ida:Resource`, asegúrese de que usa la llamada de la aplicación **ID de cliente** y no su **URI de ID de aplicación**.

    `ida:ClientId`y `ida:Resource` son diferentes los valores de este tutorial ya está usando separan applicaations de Azure AD para el nivel intermedio y nivel de datos. Si estaba utilizando una sola aplicación de Azure AD para la aplicación de la API llamada y la aplicación de la API protegida, debe usar el mismo valor en ambas `ida:ClientId` y `ida:Resource`.

    El código utiliza ConfigurationManager para obtener estos valores, por lo que se podrían almacenar en el archivo Web.config del proyecto o en el entorno de tiempo de ejecución de Azure. Mientras se ejecuta una aplicación de ASP.NET en la aplicación de servicio de Azure, configuración de entorno automáticamente invalidar la configuración de Web.config. Configuración de entorno generalmente es una [forma más segura para almacenar información confidencial en comparación con un archivo Web.config](http://www.asp.net/identity/overview/features-api/best-practices-for-deploying-passwords-and-other-sensitive-data-to-aspnet-and-azure).

6. Haga clic en **Guardar**.

    ![Haga clic en Guardar](./media/app-service-api-dotnet-service-principal-auth/appsettings.png)

### <a name="test-the-application"></a>Probar la aplicación

1. En un explorador, vaya a la dirección URL de HTTPS de AngularJS front-end web app.

2. Haga clic en la pestaña de la **Lista de tareas pendientes** e inicie sesión con credenciales de un usuario de su inquilino de Azure AD. 

4. Agregar elementos de tareas pendientes para comprobar que la aplicación está funcionando.

    ![Página de lista de tareas pendientes](./media/app-service-api-dotnet-service-principal-auth/mvchome.png)

    Si la aplicación no funciona según lo esperado, vuelva a comprobar todos los valores que haya escrito en el portal de Azure. Si todas las opciones de parecen ser correcta, vea la sección [solucionar problemas](#troubleshooting) más adelante en este tutorial.

## <a name="protect-the-api-app-from-browser-access"></a>Proteger la aplicación de la API de acceso a explorador

Este tutorial crea otra aplicación de Azure AD para la aplicación de la API de ToDoListDataAPI (nivel de datos). Como ha visto, cuando se crea una aplicación de AAD en la aplicación de servicio, configura la aplicación de AAD de manera que permite a un usuario ir a la dirección URL de la aplicación de la API en un explorador e iniciar sesión. Esto significa que es posible que un usuario final de su inquilino de Azure AD, no solo un servicio principal, para tener acceso a la API. 

Si desea evitar el acceso de explorador sin escribir código en la aplicación de la API protegida, puede cambiar la **Dirección URL de respuesta** en la aplicación de AAD para que sea distinta de la dirección URL base de la API la aplicación. 

### <a name="disable-browser-access"></a>Deshabilitar el acceso de explorador

1. En la ficha de **Configurar** clásica del portal de la aplicación de AAD que se creó para la TodoListService, cambie el valor en el campo **Dirección URL de respuesta** para que sea una dirección URL válida, pero no dirección URL de la API la aplicación.
 
2. Haga clic en **Guardar**.

### <a name="verify-browser-access-no-longer-works"></a>Comprobar el acceso a explorador ya no funciona

Anteriormente había verificado que vaya a la dirección URL de aplicación de API desde un explorador iniciando sesión con las credenciales de un usuario individual. En esta sección, compruebe que ya no es posible. 

1. En una nueva ventana del explorador, vaya a la dirección URL de la aplicación de la API nuevamente.

2. Inicie sesión cuando se le pida que lo haga.

3. Inicio de sesión es correcta, pero lleva a una página de error.

    La aplicación AAD ha configurado para que los usuarios en el inquilino AAD no pueden iniciar y obtener acceso a la API desde un explorador. Es posible tener acceso a la aplicación de la API utilizando un símbolo principal del servicio, puede comprobar yendo a la dirección URL de la aplicación web y agregar más elementos de tareas pendientes.

## <a name="restrict-access-to-a-particular-service-principal"></a>Restringir el acceso a una entidad de seguridad de servicio en particular  

En este momento, cualquier llamador que puede obtener un token para un usuario o servicio principal de su inquilino de Azure AD puede llamar a la aplicación de la API de TodoListDataAPI (nivel de datos). Desea asegurarse de que la aplicación de API de nivel de datos sólo acepta llamadas desde la aplicación de API de TodoListAPI (nivel medio) y únicamente de una entidad de seguridad de servicio en particular. 

Puede agregar estas restricciones mediante la adición de código para validar el `appid` y `objectidentifier` reclamaciones en llamadas entrantes.

En este tutorial colocar el código que valida aplicación identificador y servicio principal directamente en las acciones de controlador.  Alternativas son usar un personalizado `Authorize` atributo o hacer esta validación en el inicio de secuencias (por ejemplo, software intermedio OWIN). Para obtener un ejemplo de este último, consulte [esta aplicación de ejemplo](https://github.com/mohitsriv/EasyAuthMultiTierSample/blob/master/MyDashDataAPI/Startup.cs). 

Realice los cambios siguientes en el proyecto TodoListDataAPI.

2. Abra el archivo *Controllers/TodoListController.cs* .

3. Quite los comentarios de las líneas que establezcan `trustedCallerClientId` y `trustedCallerServicePrincipalId`.

        private static string trustedCallerClientId = ConfigurationManager.AppSettings["todo:TrustedCallerClientId"];
        private static string trustedCallerServicePrincipalId = ConfigurationManager.AppSettings["todo:TrustedCallerServicePrincipalId"];

4. Quite el código en el método CheckCallerId. Este método se denomina al principio de cada método de acción en el controlador. 

        private static void CheckCallerId()
        {
            string currentCallerClientId = ClaimsPrincipal.Current.FindFirst("appid").Value;
            string currentCallerServicePrincipalId = ClaimsPrincipal.Current.FindFirst("http://schemas.microsoft.com/identity/claims/objectidentifier").Value;
            if (currentCallerClientId != trustedCallerClientId || currentCallerServicePrincipalId != trustedCallerServicePrincipalId)
            {
                throw new HttpResponseException(new HttpResponseMessage { StatusCode = HttpStatusCode.Unauthorized, ReasonPhrase = "The appID or service principal ID is not the expected value." });
            }
        }

5. Redistribuir el proyecto ToDoListDataAPI al servicio de la aplicación de Azure.

6. En el explorador, vaya a la dirección URL de HTTPS AngularJS front-end web de la aplicación y, en la página de inicio, haga clic en la pestaña **Lista de tareas pendientes** .

    La aplicación no funciona porque se producen errores en las llamadas al back-end. El nuevo código está revisando ID real y objectidentifier pero aún no dispone de los valores correctos para protegerlos contra. El Explorador de consola de herramientas de desarrollador informa de que el servidor devuelve un error de HTTP 401.

    ![Error en el programador herramientas consola](./media/app-service-api-dotnet-service-principal-auth/webapperror.png)

    En los siguientes pasos para configurar los valores esperados.

8. Con Azure AD PowerShell, obtener el valor de la entidad de seguridad de servicio de la aplicación de Azure AD que ha creado para el proyecto TodoListWebApp.

    una. Para obtener instrucciones sobre cómo instalar Azure PowerShell y conectarse a su suscripción, vea [con Azure con el Administrador de recursos de Azure](../powershell-azure-resource-manager.md).

    b. Para obtener una lista de principales de servicio, ejecute el `Login-AzureRmAccount` comando y, a continuación, el `Get-AzureRmADServicePrincipal` comando.

    c. Buscar el Id. de objeto de la entidad de seguridad de servicio de la aplicación TodoListAPI y guárdelo en una ubicación que puede copiar de más adelante.

7. En el portal de Azure, desplácese hasta el módulo de aplicación de la API para la aplicación de la API que implementa el proyecto ToDoListDataAPI.

9. Haga clic en **Configuración > configuración de la aplicación**.

3. En la sección **configuración de la aplicación** , agregue las claves y los valores siguientes:

  	| **Clave** | todo:TrustedCallerServicePrincipalId |
  	|---|---|
  	| **Valor** | Identificador principal del servicio de llamar a la aplicación |
  	| **Ejemplo** | 4f4a94a4-6f0d-4072-4f4a94a4-6f0d-4072 |

  	| **Clave** | todo:TrustedCallerClientId |
  	|---|---|
  	| **Valor** | Identificador de cliente de llamar a aplicación - copiado de la aplicación TodoListAPI Azure AD |
  	| **Ejemplo** | 960adec2-b74a-484a-960adec2-b74a-484a |

6. Haga clic en **Guardar**.

    ![Haga clic en Guardar](./media/app-service-api-dotnet-service-principal-auth/trustedcaller.png)

6. En el explorador, vuelva a la dirección URL de la aplicación web y, en la página de inicio, haga clic en la pestaña **Lista de tareas pendientes** .

    En este momento la aplicación funciona como se espera porque la aplicación de confianza llamador identificador y servicio principal son los valores esperados.

    ![Página de lista de tareas pendientes](./media/app-service-api-dotnet-service-principal-auth/mvchome.png)

## <a name="building-the-projects-from-scratch"></a>Generar los proyectos desde cero

Los dos proyectos de Web API creados con la plantilla de proyecto de **Aplicación de la API de Azure** y reemplazar el controlador de valores predeterminados con un controlador ToDoList. Para adquirir tokens principales de servicio de Azure AD en el proyecto ToDoListAPI, se ha instalado el paquete de [Active Directory autenticación biblioteca (ADAL) para .NET](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/) NuGet.
 
Obtenga información sobre cómo crear una aplicación de una sola página AngularJS con un fondo de Web API como ToDoListAngular [manos en práctica: crear una aplicación de página único (SPA) con la API de Web de ASP.NET y Angular.js](http://www.asp.net/web-api/overview/getting-started-with-aspnet-web-api/build-a-single-page-application-spa-with-aspnet-web-api-and-angularjs). Para obtener información sobre cómo agregar código de autenticación de Azure AD, vea [Proteger AngularJS única página aplicaciones con Azure AD](../active-directory/active-directory-devquickstarts-angular.md).

## <a name="troubleshooting"></a>Solución de problemas

[AZURE.INCLUDE [troubleshooting](../../includes/app-service-api-auth-troubleshooting.md)]

* Asegúrese de que no confunda ToDoListAPI (nivel medio) y ToDoListDataAPI (nivel de datos). Por ejemplo, en este tutorial agregar autenticación de la aplicación de API de nivel de datos, **pero debe proceder la tecla de aplicación de la aplicación de Azure AD que ha creado para la aplicación de la API de nivel intermedio**.

## <a name="next-steps"></a>Pasos siguientes

Este es el último tutorial de la serie de aplicaciones de la API. 

Para obtener más información acerca de Azure Active Directory, consulte los siguientes recursos.

* [Guía de Azure programadores de AD](http://aka.ms/aaddev)
* [Escenarios de Azure AD](http://aka.ms/aadscenarios)
* [Ejemplos de Azure AD](http://aka.ms/aadsamples)

    El ejemplo de [Aplicación Web WebAPI OAuth2 AppIdentity DotNet](http://github.com/AzureADSamples/WebApp-WebAPI-OAuth2-AppIdentity-DotNet) es similar a lo que se muestra en este tutorial, pero sin utilizar autenticación de servicio de aplicación.

Para obtener información sobre otros modos de implementar proyectos de Visual Studio para aplicaciones de la API, mediante Visual Studio o mediante la [automatización de implementación](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/continuous-integration-and-continuous-delivery) de un [sistema de control de código fuente](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/source-control), vea [cómo implementar una aplicación de servicio de aplicaciones de Azure](../app-service-web/web-sites-deploy.md).
