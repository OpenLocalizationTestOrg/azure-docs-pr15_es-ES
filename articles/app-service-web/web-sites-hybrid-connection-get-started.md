<properties 
    pageTitle="Acceso a los recursos locales con conexiones híbrido en el servicio de aplicación de Azure" 
    description="Crear una conexión entre una aplicación web de servicio de la aplicación de Azure y un recurso local que usa un puerto TCP estático" 
    services="app-service" 
    documentationCenter="" 
    authors="cephalin" 
    manager="wpickett" 
    editor="mollybos"/>

<tags 
    ms.service="app-service" 
    ms.workload="na" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="02/03/2016" 
    ms.author="cephalin"/>

#<a name="access-on-premises-resources-using-hybrid-connections-in-azure-app-service"></a>Acceso a los recursos locales con conexiones híbrido en el servicio de aplicación de Azure

Puede conectar una aplicación de servicio de la aplicación de Azure a cualquier recurso local que usa un puerto TCP estático, como SQL Server, MySQL, HTTP Web API y mayoría de los servicios Web personalizados. Este artículo le muestra cómo crear una conexión híbrida entre el servicio de la aplicación y una base de datos de SQL Server local.

> [AZURE.NOTE] La parte de aplicaciones Web de la característica híbrida conexiones solo está disponible en el [Portal de Azure](https://portal.azure.com). Para crear una conexión en servicios de BizTalk, vea [Conexiones híbrido](http://go.microsoft.com/fwlink/p/?LinkID=397274). 
> 
> Este contenido también se aplica a aplicaciones móviles en servicio de la aplicación de Azure. 

## <a name="prerequisites"></a>Requisitos previos
- Una suscripción de Azure. Para una suscripción gratuita, consulte la [Versión de prueba gratuita de Azure](https://azure.microsoft.com/pricing/free-trial/). 
 
    Si desea empezar a trabajar con el servicio de aplicación de Azure antes de suscribirse para una cuenta de Azure, vaya a [Probar el servicio de aplicaciones](http://go.microsoft.com/fwlink/?LinkId=523751), donde puede crear inmediatamente una aplicación web de starter corta duración en la aplicación de servicio. Sin tarjetas de crédito obligatoria; sin compromisos.

- Para usar una base de datos de SQL Server o SQL Server Express local con una conexión híbrido, TCP/IP debe habilitarse en un puerto estático. Utilizar una instancia predeterminada en SQL Server se recomienda porque utiliza el puerto 1433 estático. Para obtener información sobre cómo instalar y configurar SQL Server Express para usarlo con conexiones híbrida, vea [conectarse a un servidor de SQL local desde un sitio web de Azure usando conexiones híbrido](http://go.microsoft.com/fwlink/?LinkID=397979).

- El equipo en el que instale al agente de administrador de conexiones de híbrida local que se describe más adelante en este artículo:

    - Debe poder conectarse a Azure a través del puerto 5671
    - Debe poder llegar al *nombre de host*:*número de puerto* del recurso local. 

> [AZURE.NOTE] Los pasos de este artículo se supone que está usando el explorador desde el equipo que aloja al agente de conexión local híbrido.


## <a name="create-a-web-app-in-the-azure-portal"></a>Crear una aplicación web en el Portal de Azure ##

> [AZURE.NOTE] Si ya ha creado una aplicación web o la aplicación Mobile back-end en el Portal de Azure que desea usar para este tutorial, puede ir directamente al [crear una conexión de híbrido y un servicio de BizTalk](#CreateHC) e iniciar desde allí.

1. En la esquina superior izquierda del [Portal de Azure](https://portal.azure.com), haga clic en **nuevo** > **Web + Mobile** > **Web App**.
    
    ![Nueva aplicación web][NewWebsite]
    
2. En el módulo de **Web app** , proporcionar una dirección URL y haga clic en **crear**. 
    
    ![Nombre del sitio Web][WebsiteCreationBlade]
    
3. Pasados unos momentos, se crea la aplicación web y se muestra el módulo de aplicación web. El módulo es un panel desplazable verticalmente que le permite administrar su sitio.
    
    ![Ejecución de sitio Web][WebSiteRunningBlade]
    
4. Para comprobar que el sitio está activo, puede hacer clic en el icono **Examinar** para mostrar la página predeterminada.
    
    ![Haga clic en Examinar para ver la aplicación web][Browse]
    
    ![Página predeterminada de aplicación web][DefaultWebSitePage]
    
A continuación, creará una conexión híbrido y un servicio de BizTalk para la aplicación web.

<a name="CreateHC"></a>
## <a name="create-a-hybrid-connection-and-a-biztalk-service"></a>Crear una conexión de híbrido y un servicio de BizTalk ##

1. En el módulo de aplicación web haga clic en **todas las configuraciones** > **redes** > **configurar los extremos de conexión híbrido**.
    
    ![Conexiones de híbrido][CreateHCHCIcon]
    
2. En el módulo de conexiones híbrido, haga clic en **Agregar**.
    
    <!-- ![Add a hybrid connnection][CreateHCAddHC]
-->
    
3. Se abre el módulo de **Agregar una conexión híbrido** .  Dado que este es su primera conexión híbrido, la opción **nueva conexión híbrido** es el valor y abre el módulo de **crear una conexión híbrido** para usted.
    
    ![Crear una conexión híbrido][TwinCreateHCBlades]
    
    En el **módulo de conexión de crear híbrido**:
    - **Nombre**, proporcione un nombre para la conexión.
    - Para **nombre de host**, escriba el nombre del equipo local que hospeda el recurso.
    - En **puerto**, escriba el número de puerto que el recurso local utiliza (1433 para instancias predeterminadas de SQL Server).
    - Haga clic en **servicio de hablar de negocios**


4. Se abre el módulo de **Crear el servicio de BizTalk** . Escriba un nombre para el servicio BizTalk y, a continuación, haga clic en **Aceptar**.
    
    ![Crear servicio BizTalk][CreateHCCreateBTS]
    
    El módulo de **Crear el servicio de BizTalk** se cierra y vuelve a la hoja de **crear una conexión híbrido** .
    
5. En el módulo de conexión híbrido de crear, haga clic en **Aceptar**. 
    
    ![Haga clic en Aceptar][CreateBTScomplete]
    
6. Cuando se complete el proceso, el área de notificación en el Portal de le informa de que la conexión se ha creado correctamente.
    <!---TODO

    Todo lo que se produce un error en este paso. No se puede crear un servicio de BizTalk en el portal de dogfood. Cambiar el clásico Portal (portal completa) y creó el servicio BizTalk pero parece que no le permiten conectar, cuando haya terminado el paso de conexión crear híbrido, recibe el siguiente error no se pudo crear conexión híbrido RelecIoudHC. No se encontró el tipo de recurso en el espacio de nombres 'Microsoft.BizTaIkServices para api versión 2014-06-01'.
    
    El error indica que no pudo encontrar el tipo, no la instancia.
    ![Notificación de éxito][CreateHCSuccessNotification]
    -->
7. En el módulo de la aplicación web, el icono de **conexiones híbrido** ahora muestra que se ha creado 1 conexión híbrido.
    
    ![Conexión de un híbrido creada][CreateHCOneConnectionCreated]
    
En este momento, ha completado una parte importante de la infraestructura de nube híbrida conexión. A continuación, creará un fragmento local correspondiente.

<a name="InstallHCM"></a>
## <a name="install-the-on-premises-hybrid-connection-manager-to-complete-the-connection"></a>Instalar al administrador de conexión de local híbrido para completar la conexión ##

1. En el módulo de la aplicación web, haga clic en **configuración de todos los** > **redes** > **configurar los extremos de conexión híbrido**. 
    
    ![Icono de conexiones híbrido][HCIcon]
    
2. En el módulo de **conexiones híbrido** , la columna **estado** del extremo de agregado recientemente muestra **no conectado**. Haga clic en la conexión para configurarlo.
    
    ![No conectado][NotConnected]
    
    Se abre el módulo de conexión híbrido.
    
    ![NotConnectedBlade][NotConnectedBlade]
    
3. En el módulo, haga clic en **Configuración de escucha**.
    
    ![Haga clic en configuración de escucha][ClickListenerSetup]
    
4. Se abre la hoja de **Propiedades de conexión híbrido** . En **El Administrador de conexión de local híbrido**, elija **haga clic aquí para instalar**.
    
    ![Haga clic aquí para instalar][ClickToInstallHCM]
    
5. En el diálogo de advertencia de seguridad de ejecutar la aplicación, seleccione **Ejecutar** para continuar.
    
    ![Elija ejecutar para continuar][ApplicationRunWarning]
    
6.  En el cuadro de diálogo **Control de cuentas de usuario** , elija **Sí**.
    
    ![Elija Sí][UAC]
    
7. El Administrador de conexión híbrido se descargó e instaló para usted. 
    
    ![Instalar][HCMInstalling]
    
8. Cuando finalice la instalación, haga clic en **Cerrar**.
    
    ![Haga clic en Cerrar][HCMInstallComplete]
    
    En el módulo de **conexiones híbrido** , la columna **estado** muestra ahora **conectado**. 
    
    ![Estado conectado][HCStatusConnected]

Ahora que ha finalizado la infraestructura de conexión híbrido, puede crear una aplicación híbrida que se usa. 

>[AZURE.NOTE]En las siguientes secciones se muestran cómo usar una conexión de híbrido con un proyecto de back-end .NET de aplicaciones móviles.

## <a name="configure-the-mobile-app-net-backend-project-to-connect-to-the-sql-server-database"></a>Configurar el proyecto de back-end de Mobile aplicación .NET para conectarse a la base de datos de SQL Server

En la aplicación de servicio, un proyecto de back-end de Mobile aplicaciones .NET es solo una aplicación de web de ASP.NET con un SDK de aplicaciones adicionales de Mobile instalado e inicializado. Para usar la aplicación web como un servidor de aplicaciones móviles, debe [descargar e inicializar el back-end de Mobile aplicaciones .NET SDK](../app-service-mobile/app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#install-sdk).  

Para las aplicaciones móviles, también debe definir una cadena de conexión para la base de datos local y modificar el back-end para usar esta conexión. 

1. En el Explorador de soluciones en Visual Studio, abra el archivo Web.config para su .NET de aplicación móvil de back-end, busque la sección **connectionStrings** , agregue una nueva entrada de SqlClient parecido al siguiente, que apunta a la base de datos de SQL Server local:

        <add name="OnPremisesDBConnection"
         connectionString="Data Source=OnPremisesServer,1433;
         Initial Catalog=OnPremisesDB;
         User ID=HybridConnectionLogin;
         Password=<**secure_password**>;
         MultipleActiveResultSets=True"
         providerName="System.Data.SqlClient" />

    No olvide reemplazar `<**secure_password**>` en esta cadena con la contraseña que ha creado para *HybridConnectionLogin*.

3. Haga clic en **Guardar** en Visual Studio para guardar el archivo Web.config.

    > [AZURE.NOTE]Esta configuración de conexión se usa cuando se ejecuta en el equipo local. Cuando se ejecuta en Azure, esta opción está reemplaza la configuración de conexión que se definen en el portal.

4. Expanda la carpeta **modelos** y abra el archivo de modelo de datos, que termina en *Context.cs*.

6. Modifique el constructor de instancia de **DbContext** para pasar el valor `OnPremisesDBConnection` al constructor **DbContext** base, similar al siguiente fragmento de código:

        public class hybridService1Context : DbContext
        {
            public hybridService1Context()
                : base("OnPremisesDBConnection")
            {
            }
        }

    Ahora, el servicio utilizará la nueva conexión a la base de datos de SQL Server.

## <a name="update-the-mobile-app-backend-to-use-the-on-premises-connection-string"></a>Actualizar la aplicación móvil de back-end para usar la cadena de conexión local

A continuación, debe agregar una configuración de aplicación para esta nueva cadena de conexión para que se puede usar de Azure.  

1. En el [portal de Azure](https://portal.azure.com) en el código de back-end de aplicación web de la aplicación móvil, haga clic en **toda la configuración**y, a continuación, **configuración de la aplicación**.

3. En el módulo de **configuración de la aplicación Web** , desplácese hacia abajo hasta **las cadenas de conexión** y agregar una nueva cadena de conexión de **SQL Server** denominada `OnPremisesDBConnection` con un valor como `Server=OnPremisesServer,1433;Database=OnPremisesDB;User ID=HybridConnectionsLogin;Password=<**secure_password**>`.

    Reemplazar `<**secure_password**>` con una contraseña segura para la base de datos local.

    ![Cadena de conexión de base de datos local](./media/web-sites-hybrid-connection-get-started/set-sql-server-database-connection.png)

2. Pulse **Guardar** para guardar la conexión híbrido y la cadena de conexión que acaba de crear.

En este momento puede volver a publicar el proyecto de servidor y probar la nueva conexión con los clientes existentes de aplicaciones móviles. Datos se pueden leer y escritos la base de datos local con la conexión híbrido.

<a name="NextSteps"></a>
## <a name="next-steps"></a>Pasos siguientes ##

- Para obtener información sobre cómo crear una aplicación web ASP.NET que usa una conexión de implementación híbrida, vea [conectarse a un servidor de SQL local desde un sitio web de Azure usando conexiones híbrido](http://go.microsoft.com/fwlink/?LinkID=397979). 

### <a name="additional-resources"></a>Recursos adicionales

[Introducción a las conexiones híbrido](http://go.microsoft.com/fwlink/p/?LinkID=397274)

[Josh Twist presenta conexiones híbrido (9 de canal de vídeo)](http://channel9.msdn.com/Shows/Azure-Friday/Josh-Twist-introduces-hybrid-connections)

[Sitio web de conexiones de híbrido](https://azure.microsoft.com/services/biztalk-services/)

[Servicios de BizTalk: Fichas de paneles, Monitor, escala, configurar y conexión híbrido](../biztalk-services/biztalk-dashboard-monitor-scale-tabs.md)

[Crear un reales en la nube híbrida con portabilidad de aplicación transparente (9 de canal de vídeo)](http://channel9.msdn.com/events/TechEd/NorthAmerica/2014/DCIM-B323#fbid=)

[Conectarse a un servidor de SQL local de servicios de móvil de Azure mediante conexiones híbrido (9 de canal de vídeo)](http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Connect-to-an-on-premises-SQL-Server-from-Azure-Mobile-Services-using-Hybrid-Connections)

## <a name="whats-changed"></a>¿Qué ha cambiado
* Encontrará una guía para el cambio de sitios Web a la aplicación de servicio: [servicio de aplicación de Azure y su influencia sobre los servicios de Azure existentes](http://go.microsoft.com/fwlink/?LinkId=529714)

<!-- IMAGES -->
[New]:./media/web-sites-hybrid-connection-get-started/B01New.png
[NewWebsite]:./media/web-sites-hybrid-connection-get-started/B02NewWebsite.png
[WebsiteCreationBlade]:./media/web-sites-hybrid-connection-get-started/B03WebsiteCreationBlade.png
[WebSiteRunningBlade]:./media/web-sites-hybrid-connection-get-started/B04WebSiteRunningBlade.png
[Browse]:./media/web-sites-hybrid-connection-get-started/B05Browse.png
[DefaultWebSitePage]:./media/web-sites-hybrid-connection-get-started/B06DefaultWebSitePage.png
[CreateHCHCIcon]:./media/web-sites-hybrid-connection-get-started/C01CreateHCHCIcon.png
[CreateHCAddHC]:./media/web-sites-hybrid-connection-get-started/C02CreateHCAddHC.png
[TwinCreateHCBlades]:./media/web-sites-hybrid-connection-get-started/C03TwinCreateHCBlades.png
[CreateHCCreateBTS]:./media/web-sites-hybrid-connection-get-started/C04CreateHCCreateBTS.png
[CreateBTScomplete]:./media/web-sites-hybrid-connection-get-started/C05CreateBTScomplete.png
[CreateHCSuccessNotification]:./media/web-sites-hybrid-connection-get-started/C06CreateHCSuccessNotification.png
[CreateHCOneConnectionCreated]:./media/web-sites-hybrid-connection-get-started/C07CreateHCOneConnectionCreated.png
[HCIcon]:./media/web-sites-hybrid-connection-get-started/D01HCIcon.png
[NotConnected]:./media/web-sites-hybrid-connection-get-started/D02NotConnected.png
[NotConnectedBlade]:./media/web-sites-hybrid-connection-get-started/D03NotConnectedBlade.png
[ClickListenerSetup]:./media/web-sites-hybrid-connection-get-started/D04ClickListenerSetup.png
[ClickToInstallHCM]:./media/web-sites-hybrid-connection-get-started/D05ClickToInstallHCM.png
[ApplicationRunWarning]:./media/web-sites-hybrid-connection-get-started/D06ApplicationRunWarning.png
[UAC]:./media/web-sites-hybrid-connection-get-started/D07UAC.png
[HCMInstalling]:./media/web-sites-hybrid-connection-get-started/D08HCMInstalling.png
[HCMInstallComplete]:./media/web-sites-hybrid-connection-get-started/D09HCMInstallComplete.png
[HCStatusConnected]:./media/web-sites-hybrid-connection-get-started/D10HCStatusConnected.png
 