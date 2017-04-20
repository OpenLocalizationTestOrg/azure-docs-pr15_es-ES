<properties
    pageTitle="Crear un WebJob .NET en Azure de aplicación de servicio | Microsoft Azure"
    description="Crear una aplicación de varios niveles con ASP.NET MVC y Azure. Se ejecuta el front-end ejecuta en una aplicación web de servicio de la aplicación de Azure y el back-end como un WebJob. La aplicación usa entidad Framework, base de datos de SQL y colas de almacenamiento de Azure y BLOB."
    services="app-service"
    documentationCenter=".net"
    authors="tdykstra"
    manager="wpickett"
    editor="mollybos"/>

<tags
    ms.service="app-service"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/28/2016"
    ms.author="tdykstra"/>

# <a name="create-a-net-webjob-in-azure-app-service"></a>Crear un WebJob .NET en Azure de aplicación de servicio

Este tutorial muestra cómo escribir código para una aplicación de ASP.NET MVC 5 simple de varios niveles que usa el [SDK de WebJobs](websites-dotnet-webjobs-sdk.md).

El propósito del [SDK WebJobs](websites-webjobs-resources.md) es simplificar el código que escriba para las tareas comunes que un WebJob puede llevar a cabo, como el procesamiento de imagen, la cola de procesamiento, RSS agregación, mantenimiento del archivo y enviar correos electrónicos. El SDK WebJobs tiene características integradas para trabajar con el almacenamiento de Azure y Bus de servicio, para programar tareas y tratamiento de errores y muchos otros escenarios comunes. Además, se ha diseñado para ser extensible y hay un [repositorio de origen para las extensiones de abrir](https://github.com/Azure/azure-webjobs-sdk-extensions/wiki/Binding-Extensions-Overview).

La aplicación de ejemplo es un boletín de publicidad. Los usuarios pueden cargar imágenes de anuncios y un proceso de back-end convierte las imágenes en miniatura. La página de lista de ad muestra las miniaturas y la página de detalles de ad muestra la imagen de tamaño completo. Esta es una captura de pantalla:

![Lista de anuncios](./media/websites-dotnet-webjobs-sdk-get-started/list.png)

Esta aplicación de ejemplo funciona con [colas de Azure](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/queue-centric-work-pattern) y [Azure BLOB](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/unstructured-blob-storage). El tutorial muestra cómo implementar la aplicación de [Servicio de la aplicación de Azure](http://go.microsoft.com/fwlink/?LinkId=529714) y [Base de datos de SQL Azure](http://msdn.microsoft.com/library/azure/ee336279).

## <a id="prerequisites"></a>Requisitos previos

El tutorial, se supone que sabe cómo trabajar con proyectos de [ASP.NET MVC 5](http://www.asp.net/mvc/tutorials/mvc-5/introduction/getting-started) en Visual Studio.

El tutorial se ha escrito para Visual Studio de 2013. Si ya no tiene Visual Studio, se instalará automáticamente automáticamente cuando se instala el SDK de Azure para .NET.

Puede utilizar el tutorial con 2015 de Visual Studio, pero antes de ejecutar la aplicación localmente tiene que cambiar la `Data Source` forma parte de la cadena de conexión de SQL Server LocalDB en los archivos Web.config y App.config de `Data Source=(localdb)\v11.0` a `Data Source=(LocalDb)\MSSQLLocalDB`. 

> [AZURE.NOTE] <a name="note"></a>Necesita una cuenta de Azure para completar este tutorial:
  >
  > + Puede [Abrir una cuenta de Azure gratis](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F): obtenga créditos puede usar para probar los servicios de Azure pagados e incluso después de que se utilizan hasta puede mantener la cuenta y usar libre Azure servicios, como sitios Web. Nunca se cargará su tarjeta de crédito a menos que explícitamente cambia la configuración y pídale que se cargará.
  >
  > + Puede [activar las ventajas de suscriptor MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F): MSDN su suscripción le proporciona créditos cada mes que puede usar para los servicios de Azure pagados.
  >
  >Si desea empezar a trabajar con el servicio de aplicación de Azure antes de suscribirse para una cuenta de Azure, vaya a [Probar el servicio de aplicaciones](http://go.microsoft.com/fwlink/?LinkId=523751), donde puede crear inmediatamente una aplicación web de starter corta duración en la aplicación de servicio. Sin tarjetas de crédito obligatoria; sin compromisos.

## <a id="learn"></a>Lo que aprenderá

El tutorial muestra cómo realizar las siguientes tareas:

* Habilitar el equipo para el desarrollo de Azure instalando el SDK de Azure.
* Crear un proyecto de aplicación de consola se instala automáticamente como un WebJob de Azure cuando se implementa el proyecto web asociado.
* Probar back-end WebJobs SDK localmente en el equipo de desarrollo.
* Publicar una aplicación con un back-end de WebJobs en una aplicación web en la aplicación de servicio.
* Cargar archivos y guardarlos en el servicio de blobs de Windows Azure.
* Usar el SDK de Azure WebJobs para trabajar con BLOB y colas de almacenamiento de Azure.

## <a id="contosoads"></a>Arquitectura de la aplicación

La aplicación de ejemplo usa el [modelo de trabajo centrada en la cola](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/queue-centric-work-pattern) para descargar el trabajo intensivo de CPU de la creación de miniaturas de un proceso de back-end.

La aplicación almacena los anuncios en una base de datos SQL con entidad Framework Code First para crear las tablas y acceso a los datos. Para cada anuncio, la base de datos almacena dos direcciones URL: uno para la imagen de tamaño completo y otro para la miniatura.

![Tabla de AD](./media/websites-dotnet-webjobs-sdk-get-started/adtable.png)

Cuando un usuario carga una imagen, la aplicación web almacena la imagen en un [blob de Azure](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/unstructured-blob-storage)y almacena la información de ad en la base de datos con una dirección URL que señala al objeto binario. Al mismo tiempo, escribe un mensaje a una cola de Azure. En un proceso de back-end que se ejecuta como un WebJob de Azure, el SDK WebJobs explora la cola para mensajes nuevos. Cuando aparezca un mensaje nuevo, la WebJob crea una miniatura de la imagen y actualiza el campo de base de datos en miniatura de dirección URL para dicho anuncio. Este es un diagrama que muestra cómo interactúan los elementos de la aplicación:

![Arquitectura de anuncios de Contoso](./media/websites-dotnet-webjobs-sdk-get-started/apparchitecture.png)

[AZURE.INCLUDE [install-sdk](../../includes/install-sdk-2015-2013.md)]

Las instrucciones del tutorial se aplican a Azure SDK para .NET 2.7.1 o posterior.

## <a id="storage"></a>Crear una cuenta de almacenamiento de Azure

Una cuenta de almacenamiento de Azure proporciona recursos para almacenar los datos de cola y blob en la nube. También se usa el SDK de WebJobs para almacenar los datos de registro para el escritorio.

En una aplicación del mundo real, normalmente se crean independientes cuentas de aplicación de datos en comparación con el registro de datos e independiente para los datos de prueba frente a los datos de producción. En este tutorial usará una cuenta.

1. Abra la ventana del **Explorador de servidores** en Visual Studio.

2. Haga clic en el nodo de **Azure** y, a continuación, haga clic en **Conectar con Microsoft Azure**.
![Conectarse a Azure](./media/websites-dotnet-webjobs-sdk-get-started/connaz.png)

3. Inicie sesión con sus credenciales de Azure.

5. Haga clic en **almacenamiento** bajo el nodo de Azure y, a continuación, haga clic en **Crear cuenta de almacenamiento**.
![Crear cuenta de almacenamiento](./media/websites-dotnet-webjobs-sdk-get-started/createstor.png)

3. En el cuadro de diálogo **Crear cuenta de almacenamiento** , escriba un nombre para la cuenta de almacenamiento.

    El nombre debe ser debe ser único (ninguna otra cuenta de almacenamiento de Azure puede tener el mismo nombre). Si el nombre que escribe está ya en uso tendrá la oportunidad para cambiarlo.

    La dirección URL para tener acceso a su cuenta de almacenamiento será *{nombre}*. Core.Windows.NET..

5. Establezca la lista desplegable **grupo afinidad o la región** a la región más cercana a usted.

    Esta opción especifica qué centro de datos de Azure hospedará su cuenta de almacenamiento. En este tutorial, la opción no hacer una gran diferencia. Sin embargo, para una aplicación web de producción, desea el servidor web y su cuenta de almacenamiento en la misma región para minimizar latencia y datos de los gastos de salida. La aplicación web (que creará más adelante) Centro de datos debe ser lo más cerca posible los exploradores tener acceso a la aplicación web para minimizar la latencia.

6. Establecer la lista de la lista desplegable de **replicación** **redundantes localmente**.

    Cuando geo replicación está habilitada para una cuenta de almacenamiento, es replicar el contenido almacenado en un centro de datos secundario para habilitar la migración tras error a esa ubicación en el caso de un desastre importante en la ubicación principal. Replicación geo puede tengan costes adicionales. Para las cuentas de prueba y desarrollo, generalmente no desea pagar geo replicación. Para obtener más información, vea [crear, administrar, o eliminar una cuenta de almacenamiento](../storage-create-storage-account/#replication-options).

5. Haga clic en **crear**.

    ![Nueva cuenta de almacenamiento](./media/websites-dotnet-webjobs-sdk-get-started/newstorage.png)

## <a id="download"></a>Descargar la aplicación

1. Descargue y descomprima el [completado solución](http://code.msdn.microsoft.com/Simple-Azure-Website-with-b4391eeb).

2. Inicie Visual Studio.

3. En el menú **archivo** , elija **Abrir > proyecto o solución**, desplácese hasta que se ha descargado la solución y, a continuación, abra el archivo de solución.

4. Presione CTRL + MAYÚS + B para generar la solución.

    De forma predeterminada, Visual Studio restaura automáticamente el contenido del paquete NuGet, que no se ha incluido en el archivo *.zip* . Si no restauran los paquetes, instalarlos manualmente mediante el cuadro de diálogo **Administrar paquetes de NuGet para la solución** y haga clic en el botón **Restaurar** en la parte superior derecha.

5. En el **Explorador de soluciones**, asegúrese de que **ContosoAdsWeb** está seleccionado como proyecto de inicio.

## <a id="configurestorage"></a>Configurar la aplicación para usar su cuenta de almacenamiento

1. Abra el archivo *Web.config* de la aplicación en el proyecto ContosoAdsWeb.

    El archivo contiene una cadena de conexión de SQL y una cadena de conexión de almacenamiento de Azure para trabajar con BLOB y colas.

    La cadena de conexión de SQL apunta a una base de datos de [SQL Server Express LocalDB](http://msdn.microsoft.com/library/hh510202.aspx) .

    La cadena de conexión de almacenamiento es un ejemplo que tiene marcadores de posición de la clave de nombre y el acceso de la cuenta de almacenamiento. Va a reemplazar con una cadena de conexión que tiene el nombre y la clave de su cuenta de almacenamiento.  

    <pre class="prettyprint">&lt;connectionStrings&gt;
   &lt;Agregar nombre = cadena de conexión "ContosoAdsContext" = "origen de datos = \v11.0 (localdb); Inicial del catálogo = ContosoAds; Seguridad integrada = verdadero; MultipleActiveResultSets = verdadero; "providerName="System.Data.SqlClient "/&gt;
   &lt;Agregar nombre = cadena de conexión"AzureWebJobsStorage"=" DefaultEndpointsProtocol = https; Nombre de la cuenta =<mark>[nombre de la cuenta]</mark>; AccountKey =<mark>[accesskey]</mark>"/&gt; 
    &lt;/connectionStrings      &gt;</pre>

    La cadena de conexión de almacenamiento se denomina AzureWebJobsStorage porque es el nombre que usa el SDK WebJobs predeterminada. El mismo nombre se utiliza aquí para que tenga que establecer un único valor de cadena de conexión en el entorno de Azure.

2. En el **Explorador de servidores**, haga clic en su cuenta de almacenamiento en el nodo de **almacenamiento** y, a continuación, haga clic en **Propiedades**.

    ![Haga clic en Propiedades de la cuenta de almacenamiento](./media/websites-dotnet-webjobs-sdk-get-started/storppty.png)

3. En la ventana de **Propiedades** , haga clic en **Las teclas de cuenta de almacenamiento**y, a continuación, haga clic en los puntos suspensivos.

    ![Nueva cuenta de almacenamiento](./media/websites-dotnet-webjobs-sdk-get-started/newstorage.png)

4. Copie la **cadena de conexión**.

    ![Cuadro de diálogo de teclas de la cuenta de almacenamiento](./media/websites-dotnet-webjobs-sdk-get-started/cpak.png)

5. Reemplace la cadena de conexión de almacenamiento en el archivo *Web.config* con la cadena de conexión que acaba de copiar. Asegúrese de que selecciona todo el contenido está entre comillas, pero sin incluir las comillas antes de pegarlo.

6. Abra el archivo *App.config* en el proyecto ContosoAdsWebJob.

    Este archivo tiene dos cadenas de conexión de almacenamiento, uno de los datos de la aplicación y otro para el registro. Puede usar cuentas de almacenamiento independiente para el registro y datos de la aplicación y puede utilizar [varias cuentas de almacenamiento de datos](https://github.com/Azure/azure-webjobs-sdk/blob/master/test/Microsoft.Azure.WebJobs.Host.EndToEndTests/MultipleStorageAccountsEndToEndTests.cs). En este tutorial usará una cuenta de almacenamiento única. Las cadenas de conexión tienen marcadores de posición de las teclas de la cuenta de almacenamiento. 
    <pre class="prettyprint">&lt;configuración&gt; 
    &lt;connectionStrings&gt;
   &lt;Agregar nombre = cadena de conexión "AzureWebJobsDashboard" = "DefaultEndpointsProtocol = https; Nombre de la cuenta =<mark>[nombre de la cuenta]</mark>; AccountKey =<mark>[accesskey]</mark>"/&gt;
   &lt;Agregar nombre = cadena de conexión"AzureWebJobsStorage"=" DefaultEndpointsProtocol = https; Nombre de la cuenta =<mark>[nombre de la cuenta]</mark>; AccountKey =<mark>[accesskey]</mark>"/&gt;
   &lt;Agregar nombre = cadena de conexión"ContosoAdsContext"=" origen de datos = \v11.0 (localdb); Inicial del catálogo = ContosoAds; Seguridad integrada = verdadero; MultipleActiveResultSets = verdadero; " /&gt; 
    &lt;/connectionStrings&gt;
   &lt;inicio&gt;
   &lt;versión supportedRuntime = sku "v4.0" = ". NETFramework, versión = v4.5 "/&gt; 
    &lt;/startup&gt;
&lt;/configuration                             &gt;</pre>

    De forma predeterminada, el SDK WebJobs busca cadenas de conexión denominadas AzureWebJobsStorage y AzureWebJobsDashboard. Como alternativa, puede [almacén de la cadena de conexión no obstante que desee y pasarlo explícitamente a la `JobHost` objeto](websites-dotnet-webjobs-sdk-storage-queues-how-to.md#config).

7. Reemplace las dos cadenas de conexión de almacenamiento por la cadena de conexión que copió anteriormente.

8. Guarde los cambios.

## <a id="run"></a>Ejecute la aplicación localmente

1. Para iniciar el cliente de web de la aplicación, presione CTRL + F5.

    Se abre el explorador predeterminado a la página principal. (El proyecto web se ejecuta porque se ha realizado el proyecto de inicio).

    ![Página principal de Contoso anuncios](./media/websites-dotnet-webjobs-sdk-get-started/home.png)

2. Para iniciar el WebJob de back-end de la aplicación, haga clic en el proyecto ContosoAdsWebJob en el **Explorador de soluciones**y, a continuación, haga clic en **Depurar** > **Iniciar nueva instancia**.

    Una ventana de aplicación de consola se abre y muestra los mensajes de registro que indica que el objeto WebJobs SDK JobHost ha empezado a ejecutar.

    ![Ventana de la aplicación de consola que muestra que se está ejecutando el back-end](./media/websites-dotnet-webjobs-sdk-get-started/backendrunning.png)

3. En el explorador, haga clic en **crear un anuncio**.

4. Escriba algunos datos de prueba, seleccione una imagen para cargar y, a continuación, haga clic en **crear**.

    ![Crear página](./media/websites-dotnet-webjobs-sdk-get-started/create.png)

    La aplicación que se va a la página de índice, pero que no muestre una miniatura para el nuevo anuncio debido a que el procesamiento no ha hecho todavía.

    Mientras tanto, tras una breve espera un mensaje de registro en la ventana de aplicación de consola muestra que un mensaje de cola se recibió y se ha procesado.

    ![Ventana de la aplicación de consola que muestra que se ha procesado un mensaje de cola](./media/websites-dotnet-webjobs-sdk-get-started/backendlogs.png)

5. Después de ver los mensajes de registro en la ventana de aplicación de consola, actualice la página de índice para ver la miniatura.

    ![Página de índice](./media/websites-dotnet-webjobs-sdk-get-started/list.png)

6. Haga clic en **Detalles** para el anuncio para ver la imagen de tamaño completo.

    ![Página de detalles](./media/websites-dotnet-webjobs-sdk-get-started/details.png)

Se ejecuta la aplicación en el equipo local y que usa un servidor de SQL Server base de datos que se encuentra en el equipo, pero se trabaja con colas y blobs en la nube. En la siguiente sección deberá ejecutar la aplicación en la nube, con una base de datos de la nube, así como BLOB de la nube y colas.  

## <a id="runincloud"></a>Ejecute la aplicación en la nube

Tendrá que hacer lo siguiente para ejecutar la aplicación en la nube:

* Implementar aplicaciones Web. Visual Studio crea automáticamente una nueva aplicación web en la aplicación de servicio y una instancia de base de datos de SQL.
* Configurar la aplicación web para usar su cuenta de almacenamiento y base de datos de SQL Azure.

Una vez que haya creado algunos anuncios mientras se ejecuta en la nube, podrá ver el panel de SDK WebJobs para ver al enriquecido supervisión características tiene que ofrecer.

### <a name="deploy-to-web-apps"></a>Implementar aplicaciones Web

1. Cierre el explorador y la ventana de la aplicación de consola.

2. En el **Explorador de soluciones**, haga clic en el proyecto ContosoAdsWeb y, a continuación, haga clic en **Publicar**.

3. En el paso de **perfil** del Asistente para la **Publicación Web** , haga clic en **aplicaciones web de Microsoft Azure**.

    ![Aplicación web de Azure seleccione Publicar destino](./media/websites-dotnet-webjobs-sdk-get-started/pubweb.png)

4. Inicie sesión en Azure si aún no haya iniciado sesión.

5. Haga clic en **nuevo**.

    El cuadro de diálogo puede tener un aspecto ligeramente diferente en función de la versión de Azure SDK para .NET tiene instalada.

    ![Haga clic en nuevo](./media/websites-dotnet-webjobs-sdk-get-started/clicknew.png)

6. En el cuadro de diálogo **crear Online en Microsoft Azure** , escriba un nombre único en el cuadro **nombre de la aplicación Web** .

    La dirección URL completa consta de lo que especifique aquí plus. azurewebsites.net (como se muestra junto al cuadro de texto **nombre de la aplicación Web** ). Por ejemplo, si el nombre de la aplicación web es ContosoAds, la dirección URL será ContosoAds.azurewebsites.net.

7. En la lista desplegable de [plan de servicios de aplicación](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md) , seleccione **Crear nuevo plan de servicio de aplicación**. Escriba un nombre para el plan de servicio de aplicaciones, como ContosoAdsPlan.

8. En la lista desplegable de [grupo de recursos](../azure-resource-manager/resource-group-overview.md) , elija **Crear nuevo grupo de recursos**.

9. Escriba un nombre para el grupo de recursos, como ContosoAdsGroup.

10. En la lista desplegable **región** , elija la misma región que ha elegido para su cuenta de almacenamiento.

    Esta opción especifica qué centro de datos de Azure se ejecutará la aplicación web. Mantener la cuenta de almacenamiento y la aplicación web en el mismo centro de datos reduce la latencia y datos de los gastos de salida.

11. En la lista desplegable de **servidor de base de datos** , elija **Crear nuevo servidor**.

12. Escriba un nombre para el servidor de base de datos, como contosoadsserver + un número o el nombre para que el nombre de servidor único. 

    El nombre del servidor debe ser único. Puede contener letras minúsculas, dígitos numéricos y guiones. No puede contener un guión al final. 

    Como alternativa, si su suscripción ya tiene un servidor, puede seleccionar dicho servidor en la lista desplegable.

12. Escriba un **nombre de usuario de base de datos** de administrador y **contraseña de base de datos**.

    Si ha seleccionado la **nueva base de datos de SQL server** no escribir un nombre existente y una contraseña a continuación, se introduce un nuevo nombre y una contraseña que está definiendo ahora para usarla más tarde, cuando tenga acceso a la base de datos. Si ha seleccionado un servidor que ha creado anteriormente, se le pedirá la contraseña para la cuenta de usuario administrativo ya ha creado.

13. Haga clic en **crear**.

    ![Crear la aplicación web en el cuadro de diálogo de Microsoft Azure](./media/websites-dotnet-webjobs-sdk-get-started/newdb.png)

    Visual Studio crea la solución, el proyecto web, la aplicación web en Azure y la instancia de base de datos de SQL Azure.

14. En el paso de la **conexión** del Asistente para la **Publicación Web** , haga clic en **siguiente**.

    ![Paso de conexión](./media/websites-dotnet-webjobs-sdk-get-started/connstep.png)

15. En el paso de la **configuración** , desactive la casilla **usar esta cadena de conexión en tiempo de ejecución** y, a continuación, haga clic en **siguiente**.

    ![Paso de configuración](./media/websites-dotnet-webjobs-sdk-get-started/settingsstep.png)

    No es necesario utilizar el cuadro de diálogo Publicar para establecer la cadena de conexión de SQL porque configure ese valor en el entorno de Azure más adelante.

    Puede pasar por alto las advertencias en esta página.

    * Normalmente la cuenta de almacenamiento usa cuando se ejecuta en Azure sería diferente de la que se utiliza cuando se ejecuta localmente, pero este tutorial estar utilizando la misma en ambos entornos. Por lo tanto la cadena de conexión de AzureWebJobsStorage no es necesario transformar. Incluso si desea usar una cuenta de almacenamiento diferente en la nube, no es necesario transformar la cadena de conexión porque la aplicación usa una configuración de entorno Azure cuando se ejecuta en Azure. Verá esto más adelante en el tutorial.

    * En este tutorial que no se van a se pueden realizar cambios en el modelo de datos utilizado para la base de datos de ContosoAdsContext, así que no es necesario utilizar entidad Framework código primera migraciones para su implementación. En primer lugar, código crea automáticamente una nueva hora para la base de datos la primera la aplicación intenta obtener acceso a datos SQL.

    Para este tutorial, los valores predeterminados de las opciones del **Archivo de opciones de publicación** están correctamente.

16. En el paso de **vista previa** , haga clic en **Vista previa de inicio**.

    ![Haga clic en vista previa de inicio](./media/websites-dotnet-webjobs-sdk-get-started/previewstep.png)

    Puede pasar por alto la advertencia sobre no bases de datos que se está publicando. Entidad Framework Code First crea la base de datos; no es necesario que se publiquen.

    La ventana Vista previa muestra que los archivos binarios y configuración del proyecto WebJob se copiarán a la carpeta *app_data\jobs\continuous* de la aplicación web.

    ![Archivos de WebJobs en la ventana de vista previa](./media/websites-dotnet-webjobs-sdk-get-started/previewwjfiles.png)

17. Haga clic en **Publicar**.

    Visual Studio implementa la aplicación y abrirá la dirección URL de la página de inicio en el explorador.

    No podrá usar la aplicación web hasta que se establezca las cadenas de conexión en el entorno de Azure en la siguiente sección. Verá una página de error o la página de inicio dependiendo de web app y base de datos de opciones de creación elegido anterior.

### <a name="configure-the-web-app-to-use-your-azure-sql-database-and-storage-account"></a>Configurar la aplicación web para usar su cuenta de almacenamiento y base de datos de SQL Azure.

Es recomendable [evitar colocar información confidencial, como cadenas de conexión en los archivos que se almacenan en repositorios de código de origen](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/source-control#secrets). Azure proporciona una manera de hacerlo: puede establecer la cadena de conexión y otros valores de configuración en el entorno de Azure y API de configuración de ASP.NET recoger estos valores de automáticamente cuando la aplicación se ejecuta en Azure. Puede establecer estos valores en Azure usando el **Explorador de servidores**, el Portal de Azure, Windows PowerShell o la interfaz de línea de comandos de entre plataformas. Para obtener más información, vea [cómo las cadenas de la aplicación y el trabajo de cadenas de conexión](/blog/2013/07/17/windows-azure-web-sites-how-application-strings-and-connection-strings-work/).

En esta sección se utiliza el **Explorador de servidores** para establecer los valores de cadena de conexión en Azure.

7. En el **Explorador de servidores**, haga clic en la aplicación web en **Azure > aplicación de servicio > {su grupo de recursos}**y, a continuación, haga clic en **Configuración de la vista**.

    Se abre la ventana de la **Aplicación Web de Azure** en la pestaña **configuración** .

9. Cambiar el nombre de la cadena de conexión DefaultConnection a ContosoAdsContext.

    Cuando creada automáticamente esta cadena de conexión que creó la aplicación web con una base de datos asociada, por lo que ya tiene el valor de cadena de conexión adecuada de Azure. Está cambiando el nombre a lo que está buscando el código.

9. Agregue dos nuevas cadenas de conexión, denominadas AzureWebJobsStorage y AzureWebJobsDashboard. Establecer tipo personalizado y el valor de cadena de conexión en el mismo valor que ha usado anteriormente para los archivos *Web.config* y *App.config* . (Asegúrese de incluir la cadena de conexión completa, no solo la tecla de acceso y no incluye las comillas).

    Estas cadenas de conexión que utilizan el SDK WebJobs, uno para los datos de aplicación y otro para el registro. Como se ha visto anteriormente, el elemento de datos de aplicación también se utiliza el código de front-end web.

9. Haga clic en **Guardar**.

    ![Cadenas de conexión en el Portal de Azure](./media/websites-dotnet-webjobs-sdk-get-started/azconnstr.png)

10. En el **Explorador de servidores**, haga clic en la aplicación web y, a continuación, haga clic en **Detener**.

12. Después de que se detiene la aplicación web, haga clic en la aplicación web de nuevo y, a continuación, haga clic en **Inicio**.

    La WebJob se inicia automáticamente cuando se publican, pero se detiene cuando se realiza un cambio configuración. Para reiniciarlo puede reiniciar la aplicación web o reiniciar el WebJob en el [Portal de Azure](http://go.microsoft.com/fwlink/?LinkId=529715). Se recomienda para reiniciar la aplicación web después de un cambio de configuración.

9. Actualizar la ventana del explorador que tiene la dirección URL de web app en su barra de direcciones.

    Aparece la página de inicio.

10. Crear un anuncio, como hizo al ejecutar la aplicación localmente.

    Muestra la página de índice sin una miniatura en primer lugar.

11. Actualice la página después de unos segundos y aparece la miniatura.

    Si no aparece la miniatura, tendrá que espere un minuto o lo para el WebJob reiniciar. If después de un cierto tiempo aún no ve la miniatura cuando actualice la página, la WebJob no se inicia automáticamente. En ese caso, vaya a la pestaña WebJobs en la página de [portal clásico](https://manage.windowsazure.com) para la aplicación web y, a continuación, haga clic en **Inicio**.

### <a name="view-the-webjobs-sdk-dashboard"></a>Ver el panel de SDK WebJobs

1. En el [portal de clásico](https://manage.windowsazure.com), seleccione la aplicación web.

2. Haga clic en la ficha **WebJobs** .

3. Haga clic en la dirección URL en la columna de registros para su WebJob.

    ![Ficha WebJobs](./media/websites-dotnet-webjobs-sdk-get-started/wjtab.png)

    Una nueva pestaña del explorador se abre en el panel de WebJobs SDK. El panel de muestra que el WebJob se está ejecutando y muestra una lista de funciones en el código que ha desencadenado el SDK WebJobs.

4. Haga clic en una de las funciones para ver detalles sobre su ejecución.

    ![Panel de WebJobs SDK](./media/websites-dotnet-webjobs-sdk-get-started/wjdashboardhome.png)

    ![Panel de WebJobs SDK](./media/websites-dotnet-webjobs-sdk-get-started/wjfunctiondetails.png)

    El botón de **Función de reproducción** en esta página hace que el marco SDK WebJobs volver a llamar a la función y, a continuación, le dará la opción para cambiar los datos que se pasa a la función primero.

>[AZURE.NOTE] Cuando haya terminado de realizar pruebas, eliminar la aplicación web y la instancia de base de datos de SQL. La aplicación web es gratuita, pero la instancia de base de datos SQL y la cuenta de almacenamiento acumulan cargos (mínimos debido a tamaño reducido). Además, si deja la aplicación web que se ejecuta, cualquier persona que se encuentra la dirección URL puede crear y ver anuncios. En el portal clásico, vaya a la pestaña **panel** para la aplicación web y, a continuación, haga clic en el botón **Eliminar** en la parte inferior de la página. A continuación, puede seleccionar una casilla de verificación para eliminar la instancia de base de datos SQL al mismo tiempo. Desea evitar temporalmente que otros usuarios accedan a la aplicación web, haga clic en **Detener** en su lugar. En ese caso, cargos seguirá acumular para la cuenta de almacenamiento y base de datos de SQL. Puede seguir un procedimiento similar para eliminar la cuenta de almacenamiento y base de datos SQL cuando ya no necesite.

## <a id="create"></a>Creación de la aplicación desde cero

En esta sección tendrá que hacer las tareas siguientes:

* Crear una solución de Visual Studio con un proyecto web.
* Agregar un proyecto de la biblioteca de clases para el nivel de acceso a datos se comparte entre back-end y front-end.
* Agregue un proyecto de aplicación de consola para el back-end, con la implementación de WebJobs habilitado.
* Agregar paquetes de NuGet.
* Establecer las referencias de proyecto.
* Copiar archivos de código y la configuración de la aplicación de la aplicación descargada que ha estado trabajando con en la sección anterior del tutorial.
* Revise las partes del código que funcionan con Azure BLOB, colas y el SDK WebJobs.

### <a name="create-a-visual-studio-solution-with-a-web-project-and-class-library-project"></a>Crear una solución de Visual Studio con un proyecto web y el proyecto de la biblioteca de clases

1. En Visual Studio, elija **nuevo** > **proyecto** desde el menú **archivo** .

2. En el cuadro de diálogo **Nuevo proyecto** , elija **Visual C#** > **Web** > **Aplicación Web de ASP.NET**.

3. Nombre el nombre del proyecto ContosoAdsWeb, la solución ContosoAdsWebJobsSDK (cambiar el nombre de la solución si se va a colocar en la misma carpeta que la solución descargada) y, a continuación, haga clic en **Aceptar**.

    ![Nuevo proyecto](./media/websites-dotnet-webjobs-sdk-get-started/newproject.png)

5. En el cuadro de diálogo **Nuevo proyecto** , elija la plantilla MVC y desactive la casilla de verificación de **Host en la nube** en **Microsoft Azure**.

    Selección de **Host en la nube** permite Visual Studio para crear automáticamente una nueva aplicación web de Azure y base de datos de SQL. Puesto que ya ha creado estas versiones anteriores, no es necesario ahora mientras la creación del proyecto. Si desea crear una nueva, seleccione la casilla de verificación. Se pueden configurar la nueva aplicación web y base de datos SQL del mismo modo que realizó antes cuando se implementa la aplicación.

5. Haga clic en **Cambiar autenticación**.

    ![Cambio de la autenticación](./media/websites-dotnet-webjobs-sdk-get-started/chgauth.png)

7. En el cuadro de diálogo **Cambiar autenticación** , elija **Sin autenticación**y, a continuación, haga clic en **Aceptar**.

    ![Sin autenticación](./media/websites-dotnet-webjobs-sdk-get-started/noauth.png)

8. En el cuadro de diálogo **Nuevo proyecto** , haga clic en **Aceptar**.

    Visual Studio crea la solución y el proyecto web.

9. En el **Explorador de soluciones**, haga clic en la solución (no en el proyecto) y elija **Agregar** > **Nuevo proyecto**.

11. En el cuadro de diálogo **Agregar nuevo proyecto** , elija **Visual C#** > **Windows Desktop** > plantilla**Biblioteca de clases** .  

10. Nombre del proyecto *ContosoAdsCommon*y, a continuación, haga clic en **Aceptar**.

    Este proyecto contendrá el contexto de marco de entidad y el modelo de datos que usarán los front-end y back-end. Como alternativa podría definir las clases relacionadas con el EF en el proyecto web y hacen referencia a ese proyecto del proyecto WebJob. Pero, a continuación, el proyecto WebJob tendría una referencia a ensamblados de web que no es necesario.

### <a name="add-a-console-application-project-that-has-webjobs-deployment-enabled"></a>Agregar un proyecto de aplicación de consola que tiene la implementación de WebJobs habilitado

1. Haga clic en el proyecto de web (no la solución o el proyecto de la biblioteca de clases) y, a continuación, haga clic en **Agregar** > **Nuevo proyecto WebJob de Azure**.

    ![Selección de menú de Azure WebJob proyecto nuevo](./media/websites-dotnet-webjobs-sdk-get-started/newawjp.png)

2. En el cuadro de diálogo **Agregar WebJob de Azure** , escriba ContosoAdsWebJob como el **nombre del proyecto** y el **nombre de WebJob**. Deje **WebJob ejecutar modo** establecido para **Ejecutarse continuamente**.

3.  Haga clic en **Aceptar**.

    Visual Studio crea una aplicación de consola está configurada para implementar como un WebJob siempre que implemente el proyecto web. Para ello, realizan las siguientes tareas después de crear el proyecto:

    * Agrega un archivo de *settings.json publicar webjob* en la carpeta de propiedades del proyecto WebJob.
    * Agrega un archivo de *webjobs list.json* en la carpeta de propiedades del proyecto de web.
    * Instalado el paquete de Microsoft.Web.WebJobs.Publish NuGet en el proyecto WebJob.

    Para obtener más información acerca de estos cambios, vea [cómo implementar WebJobs mediante Visual Studio](websites-dotnet-deploy-webjobs.md).

### <a name="add-nuget-packages"></a>Agregar paquetes de NuGet

La plantilla de proyecto nuevo para un proyecto de WebJob instalará automáticamente el paquete de WebJobs SDK NuGet [Microsoft.Azure.WebJobs](http://www.nuget.org/packages/Microsoft.Azure.WebJobs) y sus dependencias.

Una de las dependencias de SDK WebJobs que se instala automáticamente en el proyecto WebJob es la biblioteca de cliente de almacenamiento de Azure (SCL). Sin embargo, debe agregar al proyecto web para trabajar con BLOB y colas.

1. Abra el cuadro de diálogo **Administrar paquetes de NuGet** para la solución.

2. En el panel izquierdo, seleccione **instalar paquetes**.

3. Busque el paquete de *Almacenamiento de Azure* y, a continuación, haga clic en **Administrar**.

4. En el cuadro **Seleccionar proyectos** , active la casilla de verificación **ContosoAdsWeb** y, a continuación, haga clic en **Aceptar**.

    Los tres proyectos usar el marco de trabajo de entidad para trabajar con datos en la base de datos de SQL.

5. En el panel izquierdo, seleccione **en línea**.

6. Busque el paquete de NuGet *EntityFramework* e instálelo en todos los proyectos de tres.


### <a name="set-project-references"></a>Establecer las referencias de proyecto

Web y proyectos WebJob trabajan con la base de datos SQL, por lo tanto necesitan una referencia al proyecto ContosoAdsCommon.

1. En el proyecto ContosoAdsWeb, establezca una referencia al proyecto ContosoAdsCommon. (Haga clic en el proyecto ContosoAdsWeb y, a continuación, haga clic en **Agregar** > **referencia**. En el cuadro de diálogo **Administrador de referencia** , seleccione **solución** > **proyectos** > **ContosoAdsCommon**y, a continuación, haga clic en **Aceptar**.)

1. En el proyecto ContosoAdsWebJob, establezca una referencia al proyecto ContosAdsCommon.

    El proyecto WebJob necesita referencias para trabajar con imágenes y para obtener acceso a las cadenas de conexión.

3. En el proyecto ContosoAdsWebJob, establezca una referencia a `System.Drawing` y `System.Configuration`.

### <a name="add-code-and-configuration-files"></a>Agregar código y archivos de configuración

Este tutorial no muestra cómo [crear controladores MVC y vistas mediante scaffolding](http://www.asp.net/mvc/tutorials/mvc-5/introduction/getting-started), cómo [escribir código de marco de entidad que funciona con bases de datos de SQL Server](http://www.asp.net/mvc/tutorials/getting-started-with-ef-using-mvc)o [los conceptos básicos de asincrónica de programación en ASP.NET 4.5](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/web-development-best-practices#async). Así que todo lo que queda por hacer es copiar los archivos de código y la configuración de la solución descargado en la solución nueva. Después de eso, las secciones siguientes se muestran y explican partes principales del código.

Para agregar archivos a un proyecto o una carpeta, haga clic en el proyecto o la carpeta y haga clic en **Agregar** > **Elemento existente**. Seleccione los archivos que desee y haga clic en **Agregar**. Si se le solicita si desea reemplazar los archivos existentes, haga clic en **Sí**.

1. En el proyecto ContosoAdsCommon, elimine el archivo *Class1.cs* y agregue en su lugar los archivos siguientes desde el proyecto descargado.

    - *AD.cs*
    - *ContosoAdscontext.cs*
    - *BlobInformation.cs*<br/><br/>

2. En el proyecto ContosoAdsWeb, agregue los siguientes archivos desde el proyecto descargado.

    - *Web.config*
    - *Global.asax.cs*  
    - En la carpeta *controladores* : *AdController.cs*
    - En la carpeta *Views\Shared* : *_Layout.cshtml* archivo
    - En la carpeta *Views\Home* : *Index.cshtml*
    - En la carpeta *Views\Ad* (cree la carpeta primero): cinco *.cshtml* archivos<br/><br/>

3. En el proyecto ContosoAdsWebJob, agregue los siguientes archivos desde el proyecto descargado.

    - *App.config* (cambiar el filtro de tipo de archivo a **Todos los archivos**)
    - *Program.cs*
    - *Functions.cs*

Ahora puede crear, ejecutar e implementar la aplicación, tal como se indica más adelante en el tutorial. Antes de hacerlo, sin embargo, detenga la WebJob que aún se está ejecutando en la aplicación web primera en que implementa. En caso contrario, ese WebJob procesará los mensajes de cola creados localmente o por la aplicación que se ejecuta en una nueva aplicación web, ya que todos utilizan la misma cuenta de almacenamiento.

## <a id="code"></a>Revisar el código de la aplicación

Las siguientes secciones explican el código relacionadas con el trabajo con el SDK de WebJobs y Azure almacenamiento de blobs y colas.

> [AZURE.NOTE] Para el código específico en el SDK de WebJobs, vaya a las secciones [Program.cs y Functions.cs](#programcs) .

### <a name="contosoadscommon---adcs"></a>ContosoAdsCommon - Ad.cs

El archivo Ad.cs define una enumeración de categorías de ad y una clase de entidad POCO para obtener información de ad.

        public enum Category
        {
            Cars,
            [Display(Name="Real Estate")]
            RealEstate,
            [Display(Name = "Free Stuff")]
            FreeStuff
        }

        public class Ad
        {
            public int AdId { get; set; }

            [StringLength(100)]
            public string Title { get; set; }

            public int Price { get; set; }

            [StringLength(1000)]
            [DataType(DataType.MultilineText)]
            public string Description { get; set; }

            [StringLength(1000)]
            [DisplayName("Full-size Image")]
            public string ImageURL { get; set; }

            [StringLength(1000)]
            [DisplayName("Thumbnail")]
            public string ThumbnailURL { get; set; }

            [DataType(DataType.Date)]
            [DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
            public DateTime PostedDate { get; set; }

            public Category? Category { get; set; }
            [StringLength(12)]
            public string Phone { get; set; }
        }

### <a name="contosoadscommon---contosoadscontextcs"></a>ContosoAdsCommon - ContosoAdsContext.cs

La clase ContosoAdsContext especifica que se utiliza la clase de Ad en una colección de DbSet, entidad Framework se almacena en una base de datos SQL.

        public class ContosoAdsContext : DbContext
        {
            public ContosoAdsContext() : base("name=ContosoAdsContext")
            {
            }
            public ContosoAdsContext(string connString)
                : base(connString)
            {
            }
            public System.Data.Entity.DbSet<Ad> Ads { get; set; }
        }

La clase tiene dos constructores. La primera se utiliza en el proyecto web y especifica el nombre de una cadena de conexión que se almacena en el archivo Web.config o el entorno de tiempo de ejecución de Azure. El segundo constructor permite pasar de la cadena de conexión real. Es necesario para el proyecto WebJob porque no tiene un archivo Web.config. Se ha visto anteriormente donde se almacena esta cadena de conexión y verá más adelante cómo el código recupera la cadena de conexión cuando se crea una instancia de la clase DbContext.

### <a name="contosoadscommon---blobinformationcs"></a>ContosoAdsCommon - BlobInformation.cs

La `BlobInformation` clase se utiliza para almacenar información sobre un objeto binario de imagen en un mensaje de cola.

        public class BlobInformation
        {
            public Uri BlobUri { get; set; }

            public string BlobName
            {
                get
                {
                    return BlobUri.Segments[BlobUri.Segments.Length - 1];
                }
            }
            public string BlobNameWithoutExtension
            {
                get
                {
                    return Path.GetFileNameWithoutExtension(BlobName);
                }
            }
            public int AdId { get; set; }
        }


### <a name="contosoadsweb---globalasaxcs"></a>ContosoAdsWeb - Global.asax.cs

Código que se llama desde el `Application_Start` método crea un contenedor de blobs de *imágenes* y de una cola de *imágenes* si aún no existen. Esto garantiza que cada vez que inicie con una nueva cuenta de almacenamiento, el contenedor de blob necesarios y la cola se crean automáticamente.

El código obtiene acceso a la cuenta de almacenamiento mediante la cadena de conexión de almacenamiento del archivo de *Web.config* o el entorno de tiempo de ejecución de Azure.

        var storageAccount = CloudStorageAccount.Parse
            (ConfigurationManager.ConnectionStrings["AzureWebJobsStorage"].ToString());

A continuación, obtiene una referencia al contenedor de blobs de *imágenes* , se crea el contenedor si ya no existe y establece permisos de acceso en el nuevo contenedor. De forma predeterminada nuevos contenedores permiten únicamente a los clientes con credenciales de cuenta de almacenamiento acceso BLOB. La aplicación web necesita el BLOB sea público para que pueda mostrar imágenes con direcciones URL que apuntan a los BLOB de imagen.

        var blobClient = storageAccount.CreateCloudBlobClient();
        var imagesBlobContainer = blobClient.GetContainerReference("images");
        if (imagesBlobContainer.CreateIfNotExists())
        {
            imagesBlobContainer.SetPermissions(
                new BlobContainerPermissions
                {
                    PublicAccess = BlobContainerPublicAccessType.Blob
                });
        }

Código similar Obtiene una referencia a la cola de *thumbnailrequest* y crea una nueva cola. En este caso no se necesita ningún cambio de permisos. 

        CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
        var imagesQueue = queueClient.GetQueueReference("thumbnailrequest");
        imagesQueue.CreateIfNotExists();

### <a name="contosoadsweb---layoutcshtml"></a>ContosoAdsWeb - _Layout.cshtml

El archivo *_Layout.cshtml* establece el nombre de la aplicación en el encabezado y pie de página y crea una entrada de menú "Anuncios".

### <a name="contosoadsweb---viewshomeindexcshtml"></a>ContosoAdsWeb - Views\Home\Index.cshtml

El archivo *Views\Home\Index.cshtml* muestra los vínculos de categoría en la página principal. Los vínculos de pasan el valor entero de la `Category` enumeración en una variable de cadena de consulta a la página de índice de anuncios.

        <li>@Html.ActionLink("Cars", "Index", "Ad", new { category = (int)Category.Cars }, null)</li>
        <li>@Html.ActionLink("Real estate", "Index", "Ad", new { category = (int)Category.RealEstate }, null)</li>
        <li>@Html.ActionLink("Free stuff", "Index", "Ad", new { category = (int)Category.FreeStuff }, null)</li>
        <li>@Html.ActionLink("All", "Index", "Ad", null, null)</li>

### <a name="contosoadsweb---adcontrollercs"></a>ContosoAdsWeb - AdController.cs

En el *AdController.cs* archivo las llamadas de constructor la `InitializeStorage` método para crear objetos de la biblioteca de cliente de almacenamiento de Azure que proporcionan una API para trabajar con BLOB y colas.

A continuación, el código obtiene una referencia al contenedor de blobs de *imágenes* como hemos visto anteriormente en *Global.asax.cs*. Al hacerlo, Establece una predeterminada [Reintentar la directiva](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/transient-fault-handling) correspondiente a una aplicación web. La directiva predeterminada de intentos de interrupción exponencial podría bloquea la aplicación web durante más de un minuto en repetidos reintentos de un error transitorio. La directiva de reintento especificada aquí espera 3 segundos después de cada uno de ellos intente para intentos hasta 3.

        var blobClient = storageAccount.CreateCloudBlobClient();
        blobClient.DefaultRequestOptions.RetryPolicy = new LinearRetry(TimeSpan.FromSeconds(3), 3);
        imagesBlobContainer = blobClient.GetContainerReference("images");

Código similar Obtiene una referencia a la cola de *imágenes* .

        CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
        queueClient.DefaultRequestOptions.RetryPolicy = new LinearRetry(TimeSpan.FromSeconds(3), 3);
        imagesQueue = queueClient.GetQueueReference("blobnamerequest");

La mayoría del código controlador es típico para trabajar con un modelo de datos de entidad Framework con una clase DbContext. Una excepción es el HttpPost `Create` método, que se carga un archivo y se guarda en el almacenamiento de blobs. El cuaderno modelo proporciona un objeto de [HttpPostedFileBase](http://msdn.microsoft.com/library/system.web.httppostedfilebase.aspx) al método.

        [HttpPost]
        [ValidateAntiForgeryToken]
        public async Task<ActionResult> Create(
            [Bind(Include = "Title,Price,Description,Category,Phone")] Ad ad,
            HttpPostedFileBase imageFile)

Si el usuario selecciona un archivo para cargar, el código carga el archivo, guarda en un blob y actualiza el registro de la base de datos de Ad con una dirección URL que señala al objeto binario.

        if (imageFile != null && imageFile.ContentLength != 0)
        {
            blob = await UploadAndSaveBlobAsync(imageFile);
            ad.ImageURL = blob.Uri.ToString();
        }

El código que realiza la carga se encuentra en la `UploadAndSaveBlobAsync` método. Crea un nombre GUID para el blob, las cargas y guarda el archivo y devuelve una referencia al objeto binario guardada.

        private async Task<CloudBlockBlob> UploadAndSaveBlobAsync(HttpPostedFileBase imageFile)
        {
            string blobName = Guid.NewGuid().ToString() + Path.GetExtension(imageFile.FileName);
            CloudBlockBlob imageBlob = imagesBlobContainer.GetBlockBlobReference(blobName);
            using (var fileStream = imageFile.InputStream)
            {
                await imageBlob.UploadFromStreamAsync(fileStream);
            }
            return imageBlob;
        }

Después de la HttpPost `Create` método blob las cargas y actualiza la base de datos, crea un mensaje de cola para informar el proceso de back-end que una imagen está lista para la conversión a una miniatura.

        BlobInformation blobInfo = new BlobInformation() { AdId = ad.AdId, BlobUri = new Uri(ad.ImageURL) };
        var queueMessage = new CloudQueueMessage(JsonConvert.SerializeObject(blobInfo));
        await thumbnailRequestQueue.AddMessageAsync(queueMessage);

El código para el HttpPost `Edit` método es similar, excepto que si el usuario selecciona un nuevo archivo de imagen se debe eliminar cualquier BLOB que ya existe para este anuncio.

        if (imageFile != null && imageFile.ContentLength != 0)
        {
            await DeleteAdBlobsAsync(ad);
            imageBlob = await UploadAndSaveBlobAsync(imageFile);
            ad.ImageURL = imageBlob.Uri.ToString();
        }

Este es el código que elimina BLOB cuando se elimina un anuncio:

        private async Task DeleteAdBlobsAsync(Ad ad)
        {
            if (!string.IsNullOrWhiteSpace(ad.ImageURL))
            {
                Uri blobUri = new Uri(ad.ImageURL);
                await DeleteAdBlobAsync(blobUri);
            }
            if (!string.IsNullOrWhiteSpace(ad.ThumbnailURL))
            {
                Uri blobUri = new Uri(ad.ThumbnailURL);
                await DeleteAdBlobAsync(blobUri);
            }
        }
        private static async Task DeleteAdBlobAsync(Uri blobUri)
        {
            string blobName = blobUri.Segments[blobUri.Segments.Length - 1];
            CloudBlockBlob blobToDelete = imagesBlobContainer.GetBlockBlobReference(blobName);
            await blobToDelete.DeleteAsync();
        }

### <a name="contosoadsweb---viewsadindexcshtml-and-detailscshtml"></a>ContosoAdsWeb - Views\Ad\Index.cshtml y Details.cshtml

El archivo *Index.cshtml* muestra miniaturas con el resto de los datos de ad:

        <img  src="@Html.Raw(item.ThumbnailURL)" />

El archivo *Details.cshtml* muestra la imagen de tamaño completo:

        <img src="@Html.Raw(Model.ImageURL)" />

### <a name="contosoadsweb---viewsadcreatecshtml-and-editcshtml"></a>ContosoAdsWeb - Views\Ad\Create.cshtml y Edit.cshtml

Los archivos *Create.cshtml* y *Edit.cshtml* especifican la codificación de formulario que habilita el controlador de obtener la `HttpPostedFileBase` objeto.

        @using (Html.BeginForm("Create", "Ad", FormMethod.Post, new { enctype = "multipart/form-data" }))

Un `<input>` elemento indica el explorador para proporcionar un cuadro de diálogo de selección de archivo.

        <input type="file" name="imageFile" accept="image/*" class="form-control fileupload" />

### <a id="programcs"></a>ContosoAdsWebJob - Program.cs

Cuando se inicia el WebJob, el `Main` método llama el SDK WebJobs `JobHost.RunAndBlock` método para comenzar la ejecución de activa funciones en el subproceso actual.

        static void Main(string[] args)
        {
            JobHost host = new JobHost();
            host.RunAndBlock();
        }

### <a id="generatethumbnail"></a>Método de ContosoAdsWebJob - Functions.cs - GenerateThumbnail

El SDK WebJobs llama a este método cuando recibe un mensaje de cola. El método crea una miniatura y coloca la miniatura de la dirección URL en la base de datos.

        public static void GenerateThumbnail(
        [QueueTrigger("thumbnailrequest")] BlobInformation blobInfo,
        [Blob("images/{BlobName}", FileAccess.Read)] Stream input,
        [Blob("images/{BlobNameWithoutExtension}_thumbnail.jpg")] CloudBlockBlob outputBlob)
        {
            using (Stream output = outputBlob.OpenWrite())
            {
                ConvertImageToThumbnailJPG(input, output);
                outputBlob.Properties.ContentType = "image/jpeg";
            }

            // Entity Framework context class is not thread-safe, so it must
            // be instantiated and disposed within the function.
            using (ContosoAdsContext db = new ContosoAdsContext())
            {
                var id = blobInfo.AdId;
                Ad ad = db.Ads.Find(id);
                if (ad == null)
                {
                    throw new Exception(String.Format("AdId {0} not found, can't create thumbnail", id.ToString()));
                }
                ad.ThumbnailURL = outputBlob.Uri.ToString();
                db.SaveChanges();
            }
        }

* La `QueueTrigger` atributo dirige el SDK WebJobs llamar a este método cuando se recibe un mensaje nuevo en la cola de thumbnailrequest.

        [QueueTrigger("thumbnailrequest")] BlobInformation blobInfo,

    La `BlobInformation` objeto en el mensaje de cola es deserializado automáticamente en la `blobInfo` parámetro. Cuando finaliza el método, se elimina el mensaje de cola. Si se produce un error en el método antes de finalizar, no se elimina el mensaje de cola; una vez que caduca un permiso de 10 minutos, el mensaje es publicado para seleccionar otra vez y procesar. Esta secuencia no se repetirá indefinidamente si un mensaje siempre produce una excepción. Después de 5 incorrecta intenta procesar un mensaje, el mensaje se mueve a una cola denominada {nombre}-dudoso. El número máximo de intentos es configurable.

* Los dos `Blob` atributos proporcionan objetos que se enlazan a BLOB: uno para el blob de imagen existente y uno a un nuevo blob de miniaturas que crea el método.

        [Blob("images/{BlobName}", FileAccess.Read)] Stream input,
        [Blob("images/{BlobNameWithoutExtension}_thumbnail.jpg")] CloudBlockBlob outputBlob)

    Nombres de blobs proceden de propiedades de la `BlobInformation` objeto recibido en el mensaje de cola (`BlobName` y `BlobNameWithoutExtension`). Para obtener la funcionalidad completa de la biblioteca de cliente de almacenamiento que puede usar el `CloudBlockBlob` clase para trabajar con BLOB. Si desea reutilizar el código que ha escrito para trabajar con `Stream` objetos, puede usar el `Stream` clase.

Para obtener más información sobre cómo escribir funciones que utilice atributos WebJobs SDK, consulte los siguientes recursos:

* [Cómo usar el almacenamiento de Azure cola con el SDK WebJobs](websites-dotnet-webjobs-sdk-storage-queues-how-to.md)
* [Cómo usar el almacenamiento de blobs de Windows Azure con el SDK WebJobs](websites-dotnet-webjobs-sdk-storage-blobs-how-to.md)
* [Cómo usar el almacenamiento de tablas Azure con el SDK WebJobs](websites-dotnet-webjobs-sdk-storage-tables-how-to.md)
* [Cómo utilizar Bus de servicio de Azure con el SDK WebJobs](websites-dotnet-webjobs-sdk-service-bus.md)

> [AZURE.NOTE]
>
> * Si la aplicación web se ejecuta en máquinas virtuales de varios, se ejecutarán simultáneamente varios WebJobs y, en algunos casos esto puede dar lugar a los mismos datos que se procesan varias veces. No es un problema si utiliza la cola integrado, blob y desencadenadores de Bus de servicio. El SDK garantiza que las funciones se procesará solo una vez para cada mensaje o blob.
>
> * Para obtener información sobre cómo implementar apagado correcto, vea [Apagado correcto](websites-dotnet-webjobs-sdk-storage-queues-how-to.md#graceful).
>
> * El código de la `ConvertImageToThumbnailJPG` método (no mostrado) utiliza las clases de la `System.Drawing` espacio de nombres para simplificar. Sin embargo, las clases de este espacio de nombres se han diseñado para su uso con formularios de Windows. No se admiten para su uso en un servicio Windows o ASP.NET. Para obtener más información acerca de las opciones de procesamiento de imagen, vea [Dinámicos de generación de imágenes](http://www.hanselman.com/blog/BackToBasicsDynamicImageGenerationASPNETControllersRoutingIHttpHandlersAndRunAllManagedModulesForAllRequests.aspx) y [Profundidad dentro de cambio de tamaño](http://www.hanselminutes.com/313/deep-inside-image-resizing-and-scaling-with-aspnet-and-iis-with-imageresizingnet-author-na).

## <a name="next-steps"></a>Pasos siguientes

En este tutorial ha visto una sencilla aplicación de varios niveles que usa el SDK de WebJobs para el procesamiento de back-end. En esta sección se proporciona algunas sugerencias para obtener más información acerca de las aplicaciones de varios niveles ASP.NET y WebJobs.

### <a name="missing-features"></a>Características que faltan

La aplicación se ha guardado simple para obtener un tutorial Introducción. En una aplicación del mundo real ' d implementar la [inserción de dependencia](http://www.asp.net/mvc/tutorials/hands-on-labs/aspnet-mvc-4-dependency-injection) y los [patrones de trabajo de repositorio y la unidad de](http://www.asp.net/mvc/tutorials/getting-started-with-ef-using-mvc/advanced-entity-framework-scenarios-for-an-mvc-web-application#repo), utilice [una interfaz para iniciar sesión](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/monitoring-and-telemetry#log), use [EF código primera migraciones](http://www.asp.net/mvc/tutorials/getting-started-with-ef-using-mvc/migrations-and-deployment-with-the-entity-framework-in-an-asp-net-mvc-application) para administrar los cambios del modelo de datos y usar [EF conexión resistencia](http://www.asp.net/mvc/tutorials/getting-started-with-ef-using-mvc/connection-resiliency-and-command-interception-with-the-entity-framework-in-an-asp-net-mvc-application) para administrar los errores de red transitorias.

### <a name="scaling-webjobs"></a>Escala WebJobs

WebJobs ejecutar en el contexto de una aplicación web y no son scalable por separado. Por ejemplo, si tiene una instancia de aplicación web estándar, tiene solo una instancia de un proceso de fondo que se ejecuta y usa algunos de los recursos de servidor (CPU, memoria, etc.) que de lo contrario, estaría disponibles para servir contenido web.

Si el tráfico varía según la hora del día o día de la semana, y el procesamiento de back-end necesita que puede esperar, puede programar la WebJobs para que se ejecute en momentos de poco tráfico. Si la carga sigue siendo demasiado alta para esa solución, puede ejecutar el back-end como un WebJob en una aplicación web independiente dedicada a tal fin. A continuación, puede escalar la aplicación web de back-end independientemente de la aplicación web de front-end.

Para obtener más información, vea [WebJobs ajuste de escala](websites-webjobs-resources.md#scale).

### <a name="avoiding-web-app-timeout-shut-downs"></a>Evitar el tiempo de espera de aplicación web apagar-listas desplegables

Para asegurarse de que su WebJobs siempre se están ejecutando y se ejecuta en todas las instancias de la aplicación web, debe habilitar la característica [AlwaysOn](http://weblogs.asp.net/scottgu/archive/2014/01/16/windows-azure-staging-publishing-support-for-web-sites-monitoring-improvements-hyper-v-recovery-manager-ga-and-pci-compliance.aspx) .

### <a name="using-the-webjobs-sdk-outside-of-webjobs"></a>Mediante el SDK WebJobs fuera WebJobs

No tiene un programa que usa el SDK de WebJobs para que se ejecute en Azure en un WebJob. Se puede ejecutar localmente y también puede ejecutar en otros entornos como una función de trabajo de servicio de nube o un servicio de Windows. Sin embargo, solo puede tener acceso al panel de SDK WebJobs a través de una aplicación web de Azure. Para usar el panel debe conectar la aplicación web con la cuenta de almacenamiento que está usando estableciendo la cadena de conexión de AzureWebJobsDashboard en la ficha **Configurar** del portal clásico. A continuación, puede ir a los paneles utilizando la siguiente URL:

https://{webappname}.SCM.azurewebsites.NET/azurejobs/#/Functions

Para obtener más información, vea [Introducción a un panel para el desarrollo local con el SDK de WebJobs](http://blogs.msdn.com/b/jmstall/archive/2014/01/27/getting-a-dashboard-for-local-development-with-the-webjobs-sdk.aspx), pero tenga en cuenta que muestra un nombre de cadena de conexión anterior.

### <a name="more-webjobs-documentation"></a>Más documentación WebJobs

Para obtener más información, consulte [los recursos de documentación WebJobs de Azure](http://go.microsoft.com/fwlink/?LinkId=390226).
