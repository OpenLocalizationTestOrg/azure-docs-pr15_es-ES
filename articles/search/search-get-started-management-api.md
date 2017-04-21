<properties 
    pageTitle="Introducción a la API de REST de administración de búsqueda de Azure | Microsoft Azure | Servicio de nube hospedado de búsqueda" 
    description="Administrar su nube hospedado mediante una API de REST de administración de servicio de búsqueda de Azure" 
    services="search" 
    documentationCenter="" 
    authors="HeidiSteen" 
    manager="jhubbard" 
    editor=""/>

<tags 
    ms.service="search" 
    ms.devlang="rest-api" 
    ms.workload="search" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.date="10/17/2016" 
    ms.author="heidist"/>

# <a name="get-started-with-azure-search-management-rest-api"></a>Introducción a la API de REST de administración de búsqueda de Azure
> [AZURE.SELECTOR]
- [Portal](search-manage.md)
- [PowerShell](search-manage-powershell.md)
- [API DE REST](search-get-started-management-api.md)

La API de administración de REST de búsqueda de Azure es una alternativa de programación para realizar tareas administrativas en el portal. Operaciones de administración de servicio incluyen crear o eliminar el servicio, el servicio de ajuste de escala y administración de claves. En este tutorial se suministra con una aplicación de cliente de ejemplo que muestra la API de administración de servicio. También incluye los pasos de configuración necesarios para ejecutar el ejemplo en su entorno de desarrollo local.

Para completar este tutorial, necesitará:

- Visual Studio 2012 o 2013
- Descargar la aplicación de cliente de ejemplo

En el transcurso de completar el tutorial, se aprovisionará dos servicios: búsqueda de Azure y Azure Active Directory (AD). Además, se creará una aplicación de AD que establece la confianza entre la aplicación de cliente y el extremo del Administrador de recursos en Azure.

Se necesita una cuenta de Azure para completar este tutorial.


##<a name="download-the-sample-application"></a>Descargar la aplicación de ejemplo

En este tutorial se basa en una aplicación de consola de Windows escrita en C#, que puede editar y ejecutar en Visual Studio 2012 o 2013

Puede encontrar la aplicación cliente en Github en [Azure búsqueda .NET administración API demostración](https://github.com/Azure-Samples/search-dotnet-management-api/).


##<a name="configure-the-application"></a>Configurar la aplicación

Antes de ejecutar la aplicación de ejemplo, debe habilitar la autenticación de modo que se aceptan las solicitudes enviadas desde la aplicación de cliente en el extremo del Administrador de recursos. El requisito de autenticación que se origina con el [Administrador de recursos de Azure](https://msdn.microsoft.com/library/azure/dn790568.aspx), que es la base para todas las operaciones relacionadas con el portal solicitado a través de una API, incluidas aquellas relacionadas con la administración de servicio de búsqueda. La API de administración de servicio de búsqueda de Azure es una extensión del Administrador de recursos de Azure y, por tanto, hereda sus dependencias.  

Administrador de recursos de Azure requiere servicio de Azure Active Directory como su proveedor de identidades. 

Para obtener un token de acceso que le permitirá solicitudes alcance el Administrador de recursos, la aplicación cliente incluye un segmento de código que llama a Active Directory. El segmento de código, además de los pasos sobre cómo usar el segmento de código, como requisito previos provienen de este artículo: [peticiones de autenticación de administrador de recursos de Azure](http://msdn.microsoft.com/library/azure/dn790557.aspx).

Puede seguir las instrucciones en el vínculo anterior o siga los pasos de este documento si prefiere pasar por el tutorial paso a paso.

En esta sección, realizará las siguientes tareas:

1. Crear un servicio de AD
1. Crear una aplicación de AD
1. Configurar la aplicación de AD mediante el registro de detalles sobre la aplicación de cliente de ejemplo que ha descargado
1. Cargar la aplicación cliente de ejemplo con los valores que se usará para obtener autorización para sus solicitudes

> [AZURE.NOTE] Estos vínculos proporcionan fondo sobre el uso de Azure Active Directory para autenticar solicitudes de cliente para el Administrador de recursos: [El Administrador de recursos de Azure](http://msdn.microsoft.com/library/azure/dn790568.aspx), [las solicitudes de autenticación de administrador de recursos de Azure](http://msdn.microsoft.com/library/azure/dn790557.aspx)y [Azure Active Directory](http://msdn.microsoft.com/library/azure/jj673460.aspx).

###<a name="create-an-active-directory-service"></a>Crear un servicio de Active Directory

1. Inicie sesión en el [Portal de Azure](https://manage.windowsazure.com).

2. Desplácese por el panel de navegación izquierdo y haga clic en **Active Directory**.

4. Haga clic en **nuevo** para abrir la **Aplicación de servicios** | **Active Directory**. En este paso, se crea un nuevo servicio de Active Directory. Este servicio hospedará la aplicación de AD que deberá definir unos pasos desde ahora. Crear un nuevo servicio le ayuda a aislar el tutorial de otras aplicaciones que ya pueden hospedar en Azure.

5. Haga clic en el **directorio** | **Crear personalizado**.

6. Escriba un nombre de servicio, el dominio y la ubicación geográfica. El dominio debe ser único. Haga clic en la marca de verificación para crear el servicio.

     ![][5]

###<a name="create-a-new-ad-application-for-this-service"></a>Cree una nueva aplicación de AD para este servicio

1. Seleccione el servicio de Active Directory de "SearchTutorial" que acaba de crear.

2. En el menú superior, haga clic en **aplicaciones**. 
 
3. Haga clic en **Agregar una aplicación**. Una aplicación de AD almacena información sobre las aplicaciones cliente que va a utilizar, como un proveedor de identidades.  
 
4. Elija **Agregar una aplicación se desarrolla mi organización**. Esta opción proporciona la configuración de registro para las aplicaciones que no se publican en la Galería de aplicación. Desde la aplicación cliente no forma parte de la Galería de aplicación, esta es la mejor opción para este tutorial.

     ![][6]
 
5. Escriba un nombre, como "Administrador de búsqueda de Azure".

6. Elija **aplicación Native client** para tipo de aplicación. Esto es correcto para la aplicación de ejemplo pasa a ser una aplicación de cliente (consola) de Windows, no una aplicación web.

     ![][7]
 
7. En la redirección URI, escriba "http://localhost/Azure-Search-Manager-App". Este URI a qué Azure Active Directory redirigirá el agente de usuario en respuesta a una solicitud de autorización de OAuth 2.0. El valor no es necesario ser un extremo físico, pero debe ser un URI válido. 

    Para el propósito de este tutorial, el valor puede ser cualquier cosa, pero todo lo que escriba se convierte en una información necesaria para la conexión administrativa en la aplicación de ejemplo. 
 
7. Haga clic en la marca de verificación para crear la aplicación de Active Directory. Debería ver "Azure búsqueda-administrador de aplicación" en el panel de navegación izquierdo.

###<a name="configure-the-ad-application"></a>Configurar la aplicación de AD
 
9. Haga clic en la aplicación de AD "Azure-búsqueda-administrador-App", que acaba de crear. Verá que aparece en el panel de navegación izquierdo.

10. En el menú superior, haga clic en **Configurar** .
 
11. Desplácese hacia abajo hasta permisos y seleccione **Las API de administración de Azure**. En este paso, especifique la API (en este caso, la API del Administrador de recursos de Azure) que la aplicación cliente necesita obtener acceso a, junto con el nivel de acceso que necesita.

12. En permisos de delegado, haga clic en la lista desplegable de la lista y seleccione **administración del servicio de Azure Access (vista previa**).
 
     ![][8]
 
13. Guardar los cambios. 

Mantener abierta la página de configuración de aplicación. En el siguiente paso, se copie los valores de esta página e introducirlos en la aplicación de ejemplo.

###<a name="load-the-sample-application-program-with-registration-and-subscription-values"></a>Cargar el programa de aplicación de ejemplo con valores de registro y suscripción

En esta sección, podrá editar la solución en Visual Studio, sustituir valores válidos que se obtienen desde el portal.
Los valores que se va a agregar aparecen cerca de la parte superior de Program.cs:

        private const string TenantId = "<your tenant id>";
        private const string ClientId = "<your client id>";
        private const string SubscriptionId = "<your subscription id>";
        private static readonly Uri RedirectUrl = new Uri("<your redirect url>");

Si todavía no ha [descargado la aplicación de ejemplo de Github](https://github.com/Azure-Samples/search-dotnet-management-api/), necesitará para este paso.

1. Abra la **ManagementAPI.sln** en Visual Studio.

2. Abra Program.cs.

3. Proporcionar `ClientId`. Desde la aplicación de AD izquierda de la página de configuración abrir desde el paso anterior, copie el identificador de cliente de la página de configuración de aplicación de AD en el portal y pegarlo en Program.cs.

4. Proporcionar `RedirectUrl`. Copie redirección URI de la misma página del portal y pegarlo en Program.cs.

    ![][9]

5. Proporcionar`TenantID.` 
    - Volver a Active Directory | SearchTutorial (servicio). 
    - Haga clic en **aplicaciones** de la barra superior. 
    - Haga clic en **Los extremos de la vista** en la parte inferior de la página. 
    - Copie el extremo de autorización de OAUTH 2.0 en la parte inferior de la lista. 
    - Pegue el extremo TenantID, recortar el valor de todos los parámetros URI excepto el identificador del inquilino.

    Dado "https://login.windows.net/55e324c7-1656-4afe-8dc3-43efcd4ffa50/oauth2/authorize?api-version=1.0", elimine todo excepto "55e324c7-1656-4afe-8dc3-43efcd4ffa50".

    ![][10]

6. Proporcionar `SubscriptionID`.
    - Vaya a la página principal del portal.
    - Haga clic en **configuración** en la parte inferior del panel de navegación izquierdo.
    - Desde la ficha suscripciones, copie el identificador de suscripción y pegarlo en Program.cs.

7. Guarde y, a continuación, cree la solución.


##<a name="explore-the-application"></a>Explorar la aplicación

Agregar un punto de interrupción en la primera llamada del método de modo que puede comenzar a través del programa. Presione **F5** para ejecutar la aplicación y, a continuación, presione **F11** para desplazarse por el código.

La aplicación de ejemplo crea un servicio gratuito de búsqueda de Azure para una suscripción existente de Azure. Si ya existe un servicio gratuito de la suscripción, se producirá un error en la aplicación de ejemplo. Se permite sólo un servicio gratuito de búsqueda por suscripción.

1. Abrir Program.cs desde el Explorador de soluciones y vaya a la función Main (string [] anular). 
 
3. Observe que **ExecuteArmRequest** se usa para ejecutar peticiones en el extremo del Administrador de recursos de Azure, `https://management.azure.com/subscriptions` para especificado `subscriptionID`. Este método se utiliza en todo el programa para realizar las operaciones con la API del Administrador de recursos de Azure o la API de administración de búsqueda.

3. Solicitudes para el Administrador de recursos de Azure deben autenticar y autorizar. Esto se logra utilizando el método **GetAuthorizationHeader** , llamado por el método **ExecuteArmRequest** , que se ha copiado de [solicitudes de autenticación de administrador de recursos de Azure](http://msdn.microsoft.com/library/azure/dn790557.aspx). Observe que se llama **GetAuthorizationHeader** `https://management.core.windows.net` para obtener un token de acceso.

4. Se le pide que inicie sesión con un nombre de usuario y contraseña que sea válido para la suscripción.

5. A continuación, un nuevo servicio de búsqueda de Azure está registrado con el proveedor del Administrador de recursos de Azure. De nuevo, es el método de **ExecuteArmRequest** utilizado esta vez para crear el servicio de búsqueda en Azure para su suscripción a través de `providers/Microsoft.Search/register`. 

6. El resto del programa utiliza la [API de REST de administración de búsqueda de Azure](http://msdn.microsoft.com/library/dn832684.aspx). Observe que la `api-version` de esta API es diferente de la versión de api de administrador de recursos de Azure. Por ejemplo, `/listAdminKeys?api-version=2014-07-31-Preview` muestra la `api-version` de la API de REST de administración de búsqueda de Azure.

    La siguiente serie de operaciones recuperar la definición del servicio que acaba de crear, las teclas de api de administración, regenera recupera claves cambia la réplica y partición y, finalmente, elimina el servicio.

    Cuando se cambia el recuento de réplica o una partición de servicio, esperamos que esta acción se producirá un error si está utilizando la edición gratuita. Sólo standard edition puede hacer uso de particiones adicionales y réplicas.

    Eliminar el servicio es la última operación.

##<a name="next-steps"></a>Pasos siguientes

Una vez terminado este tutorial, es recomendable obtener más información sobre la administración del servicio o la autenticación con el servicio de Active Directory:

- Más información sobre la integración de una aplicación cliente con Active Directory. Vea la [integración de aplicaciones de Azure Active Directory](http://msdn.microsoft.com/library/azure/dn151122.aspx).
- Obtenga más información sobre otras operaciones de administración del servicio en Azure. Vea [administración de los servicios](http://msdn.microsoft.com/library/azure/dn578292.aspx).

<!--Anchors-->
[Download the sample application]: #Download
[Configure the application]: #config
[Explore the application]: #explore
[Next Steps]: #next-steps

<!--Image references-->
[5]: ./media/search-get-started-management-api/Azure-Search-MGMT-AD-Service.PNG
[6]: ./media/search-get-started-management-api/Azure-Search-MGMT-AD-App.PNG
[7]: ./media/search-get-started-management-api/Azure-Search-MGMT-AD-App-prop.PNG
[8]: ./media/search-get-started-management-api/Azure-Search-MGMT-AD-ConfigPermissions.PNG
[9]: ./media/search-get-started-management-api/Azure-Search-MGMT-AD-ConfigPage.PNG
[10]: ./media/search-get-started-management-api/Azure-Search-MGMT-AD-OAuthEndpoint.PNG

<!--Link references-->
[Manage your search solution in Microsoft Azure]: search-manage.md
[Azure Search development workflow]: search-workflow.md
[Create your first azure search solution]: search-create-first-solution.md
[Create a geospatial search app using Azure Search]: search-create-geospatial.md


 
