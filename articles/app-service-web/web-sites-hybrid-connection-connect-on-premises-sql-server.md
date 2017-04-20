<properties
    pageTitle="Conectarse a la implementación local de SQL Server desde una aplicación web de servicio de aplicación de Azure con conexiones híbrido"
    description="Crear una aplicación web de Microsoft Azure y conectarse a una base de datos de SQL Server local"
    services="app-service\web"
    documentationCenter=""
    authors="cephalin"
    manager="wpickett"
    editor="mollybos"/>

<tags
    ms.service="app-service-web"
    ms.workload="web"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="02/09/2016"
    ms.author="cephalin"/>

# <a name="connect-to-on-premises-sql-server-from-a-web-app-in-azure-app-service-using-hybrid-connections"></a>Conectarse a la implementación local de SQL Server desde una aplicación web de servicio de aplicación de Azure con conexiones híbrido

Híbrido conexiones [Azure aplicación de servicio](http://go.microsoft.com/fwlink/?LinkId=529714) Web Apps para recursos locales que utilizan el puerto TCP estático. Recursos compatibles incluyen Microsoft SQL Server, MySQL, API de Web HTTP, servicio de aplicaciones y mayoría de los servicios Web personalizados.

En este tutorial, aprenderá a crear una aplicación de servicio de aplicación web en el [Portal de Azure](http://go.microsoft.com/fwlink/?LinkId=529715), conectarse a la aplicación web de la base de datos de SQL Server local local mediante la nueva característica de conexión híbrido, cree una aplicación de ASP.NET simple que utiliza la conexión híbrido y a continuación, implementar la aplicación en la aplicación de servicio de aplicación web. La aplicación web completada en Azure almacena las credenciales de usuario en una base de datos de suscripción local. El tutorial no supone experiencia previa con Azure o ASP.NET.

>[AZURE.NOTE] Si desea empezar a trabajar con el servicio de aplicación de Azure antes de suscribirse para una cuenta de Azure, vaya a [Probar el servicio de aplicaciones](http://go.microsoft.com/fwlink/?LinkId=523751), donde puede crear inmediatamente una aplicación web de starter corta duración en la aplicación de servicio. Sin tarjetas de crédito obligatoria; sin compromisos.
>
>La parte de aplicaciones Web de la característica híbrida conexiones solo está disponible en el [Portal de Azure](https://portal.azure.com). Para crear una conexión en servicios de BizTalk, vea [Conexiones híbrido](http://go.microsoft.com/fwlink/p/?LinkID=397274).  

## <a name="prerequisites"></a>Requisitos previos ##

Para completar este tutorial, tendrá los siguientes productos. Todos están disponibles en versiones gratuitas, para poder empezar a desarrollar para Azure por completo de forma gratuita.

- **Suscripción de azure** - para una suscripción gratuita, consulte la [Versión de prueba gratuita de Azure](/pricing/free-trial/).

- **2013 de visual Studio** : descargar una versión de prueba gratuita de 2013 de Visual Studio, vea [Descargas de Visual Studio](http://www.visualstudio.com/downloads/download-visual-studio-vs). Instale esto antes de continuar.

- **Microsoft .NET Framework 3.5 Service Pack 1** - si el sistema operativo es Windows 8.1, Windows Server 2012 R2, Windows 8, Windows Server 2012, Windows 7 o Windows Server 2008 R2, puede activar esta opción en el Panel de Control > programas y características > Activar o desactivar las características de activar Windows. En caso contrario, puede descargar desde el [Centro de descarga de Microsoft](http://www.microsoft.com/download/en/details.aspx?displaylang=en&id=22).

- **2014 de SQL Server Express con herramientas** : Descargar Microsoft SQL Server Express de forma gratuita en la [página de la base de datos de plataforma de Web de Microsoft](http://www.microsoft.com/web/platform/database.aspx). Elegir la versión **Express** (no LocalDB). La versión **Express con herramientas** incluye SQL Server Management Studio, que se usa en este tutorial.

- **SQL Server Management Studio Express** - se incluye con la 2014 de SQL Server Express con la descarga de herramientas mencionado arriba, pero si necesita instalar de forma independiente, puede descargar e instalar desde la [página de descarga de SQL Server Express](http://www.microsoft.com/web/platform/database.aspx).

El tutorial se supone que tiene una suscripción a Azure que ha instalado 2013 de Visual Studio y que ha instalado o habilitado para .NET Framework 3.5. El tutorial muestra cómo instalar SQL Server Express 2014 en una configuración que funciona bien con la característica de conexiones de Azure híbrido (una instancia de forma predeterminada con un puerto TCP estático). Antes de iniciar el tutorial, descargue 2014 de SQL Server Express con herramientas de la ubicación mencionada arriba, si no tiene instalado SQL Server.

### <a name="notes"></a>Notas ###
Para usar una base de datos de SQL Server o SQL Server Express local con una conexión híbrido, TCP/IP debe habilitarse en un puerto estático. Instancias predeterminadas de SQL Server utilizan el puerto estático 1433, mientras que las instancias con nombre no.

El equipo en el que instale al agente de administrador de conexiones de híbrida local:

- Debe tener conectividad saliente de Azure sobre:

Puerto|¿Por qué
---|---
80|**Requerido** para el puerto HTTP validación del certificado y, opcionalmente, para la conectividad de datos.
443|**Opcional** para la conectividad de datos. Si no está disponible la conectividad saliente 443, se utiliza el puerto TCP 80.
5671 y 9352|**Recomendado** , pero opcional de conectividad de datos. Nota Este modo normalmente da como resultado un rendimiento más alto. Si no está disponible la conectividad saliente a estos puertos, se utiliza el puerto TCP 443.
- Debe poder llegar al *nombre de host*:*número de puerto* del recurso local.

Los pasos de este artículo se supone que está usando el explorador desde el equipo que aloja al agente de conexión local híbrido.

Si ya tiene instalado en una configuración y en un entorno que cumpla las condiciones indicadas de SQL Server, puede avanzar e iniciar con [crear una base de datos de SQL Server local](#CreateSQLDB).

<a name="InstallSQL"></a>
## <a name="a-install-sql-server-express-enable-tcpip-and-create-a-sql-server-database-on-premises"></a>A. Instalar SQL Server Express, habilitar TCP/IP y crear una base de datos de SQL Server local ##

Esta sección muestra cómo instalar SQL Server Express, habilitar TCP/IP y crear una base de datos para que funcione la aplicación web con el Portal de Azure.

### <a name="install-sql-server-express"></a>Instalar SQL Server Express ###

1. Para instalar SQL Server Express, ejecute el archivo **SQLEXPRWT_x64_ENU.exe** o **SQLEXPR_x86_ENU.exe** que ha descargado. Aparecerá el Asistente de centro de instalación de SQL Server.

    ![Instalación SQL Server][SQLServerInstall]

2. Elija **instalación independiente de nuevo SQL Server o agregar características a una instalación existente**. Siga las instrucciones, acepte la configuración y las opciones predeterminadas hasta llegar a la página de **Configuración de la instancia** .

3. En la página **Configuración de la instancia** , seleccione **instancia predeterminada**.

    ![Elija instancia predeterminada][ChooseDefaultInstance]

    De forma predeterminada, la instancia predeterminada de SQL Server escucha para las solicitudes de clientes de SQL Server en el puerto estático 1433, que es la característica de conexiones híbrido que requiere. Instancias con nombre utilizan puertos dinámicos y UDP, que no son compatibles con conexiones híbrido.

4. Acepte los valores predeterminados en la página **Configuración del servidor** .

5. En la página **Configuración del motor de base de datos** , en **Modo de autenticación**, elija **El modo mixto (autenticación de SQL Server y Windows)**y proporcione una contraseña.

    ![Elija el modo mixto][ChooseMixedMode]

    En este tutorial, va a usar autenticación de SQL Server. Asegúrese de recordar la contraseña que proporciona, porque lo necesitará más adelante.

6. Desplazarse por el resto del Asistente para completar la instalación.

### <a name="enable-tcpip"></a>Habilitar TCP/IP ###
Para habilitar TCP/IP, usará el Administrador de configuración de SQL Server, que se instaló al instalar SQL Server Express. Siga los pasos de [Habilitar el protocolo de red TCP/IP para SQL Server](http://technet.microsoft.com/library/hh231672%28v=sql.110%29.aspx) antes de continuar.

<a name="CreateSQLDB"></a>
### <a name="create-a-sql-server-database-on-premises"></a>Crear una base de datos de SQL Server local ###

La aplicación web de Visual Studio requiere una base de datos de pertenencia Azure puede tener acceso. Esto requiere una base de datos de SQL Server o SQL Server Express (no LocalDB base de datos que la plantilla MVC usa de forma predeterminada), por lo que deberá crear la base de datos de pertenencia siguiente.

1. En SQL Server Management Studio, conéctese a SQL Server que acaba de instalar. (Si la **conexión al servidor** no aparece el cuadro de diálogo automáticamente, desplácese hasta **El Explorador de objetos** en el panel izquierdo, haga clic en **Conectar**y, a continuación, haga clic en **El motor de base de datos**).  ![Conectarse al servidor][SSMSConnectToServer]

    **Tipo de servidor**, elija **El motor de base de datos**. **Nombre del servidor**, puede usar el **host local** o el nombre del equipo que está utilizando. Elija **autenticación de SQL Server**y, a continuación, inicie sesión con el nombre de usuario sa y la contraseña que creó anteriormente.

2. Para crear una nueva base de datos con SQL Server Management Studio, haga clic en **bases de datos** en el Explorador de objetos y, a continuación, haga clic en **Nueva base de datos**.

    ![Crear nueva base de datos][SSMScreateNewDB]

3. En el cuadro de diálogo **Nueva base de datos** , escriba MembershipDB para el nombre de la base de datos y, a continuación, haga clic en **Aceptar**.

    ![Proporcione el nombre de base de datos][SSMSprovideDBname]

    Tenga en cuenta que no realiza cambios a la base de datos en este momento. La información de pertenencia se agregará automáticamente más tarde mediante la aplicación web cuando se ejecuta.

4. En el Explorador de objetos, si expandir **las bases de datos**, verá que se ha creado la base de datos de pertenencia.

    ![MembershipDB creado][SSMSMembershipDBCreated]

<a name="CreateSite"></a>
## <a name="b-create-a-web-app-in-the-azure-portal"></a>B. Crear una aplicación web en el Portal de Azure ##

> [AZURE.NOTE] Si ya ha creado una aplicación web en el Portal de Azure que desea usar para este tutorial, puede ir directamente al [crear una conexión de híbrido y un servicio de BizTalk](#CreateHC) y continuar desde allí.

1. En el [Portal de Azure](https://portal.azure.com), haga clic en **nuevo** > **Web + Mobile** > **Web app**.

    ![Botón nuevo][New]

2. Configurar la aplicación web y, a continuación, haga clic en **crear**.

    ![Nombre del sitio Web][WebsiteCreationBlade]

3. Pasados unos momentos, se crea la aplicación web y se muestra el módulo de aplicación web. El módulo es un panel de desplazamiento vertical que permite administrar la aplicación web.

    ![Ejecución de sitio Web][WebSiteRunningBlade]

    Para comprobar que la aplicación web está activa, puede hacer clic en el icono **Examinar** para mostrar la página predeterminada.

A continuación, creará una conexión híbrido y un servicio de BizTalk para la aplicación web.

<a name="CreateHC"></a>
## <a name="c-create-a-hybrid-connection-and-a-biztalk-service"></a>C. Crear una conexión de híbrido y un servicio de BizTalk ##

1. Atrás en el Portal, vaya a configuración y haga clic en **redes** > **configurar los extremos de conexión híbrido**.

    ![Conexiones de híbrido][CreateHCHCIcon]

2. En el módulo de conexiones híbrido, haga clic en **Agregar** > **nueva conexión híbrido**.

3. En el módulo de **crear una conexión híbrido** :
    - **Nombre**, proporcione un nombre para la conexión.
    - Para **nombre de host**, escriba el nombre del equipo del equipo del host de SQL Server.
    - En **puerto**, escriba 1433 (el puerto predeterminado para SQL Server).
    - Haga clic en **Servicio de BizTalk** > **Servicio BizTalk nuevo** y escriba un nombre para el servicio BizTalk.

    ![Crear una conexión híbrido][TwinCreateHCBlades]

5. Haga clic en **Aceptar** dos veces.

    Cuando se complete el proceso, usará de área de **notificaciones de** flash **éxito** verde y la vista de módulo de **conexión híbrido** muestra la nueva conexión híbrido con el estado como **no conectado**.

    ![Conexión de un híbrido creada][CreateHCOneConnectionCreated]

En este momento, ha completado una parte importante de la infraestructura de nube híbrida conexión. A continuación, creará un fragmento local correspondiente.

<a name="InstallHCM"></a>
## <a name="d-install-the-on-premises-hybrid-connection-manager-to-complete-the-connection"></a>D. Instalar al administrador de conexión de local híbrido para completar la conexión ##

[AZURE.INCLUDE [app-service-hybrid-connections-manager-install](../../includes/app-service-hybrid-connections-manager-install.md)]

Ahora que ha finalizado la infraestructura de conexión híbrido, creará una aplicación web que se usa.

<a name="CreateASPNET"></a>
## <a name="e-create-a-basic-aspnet-web-project-edit-the-database-connection-string-and-run-the-project-locally"></a>E. Crear un proyecto web ASP.NET básico, edite la cadena de conexión de base de datos y ejecutar el proyecto localmente ##

### <a name="create-a-basic-aspnet-project"></a>Crear un proyecto básico de ASP.NET ###
1. En Visual Studio, en el menú **archivo** , cree un nuevo proyecto:

    ![Nuevo proyecto de Visual Studio][HCVSNewProject]

2. En la sección **plantillas** del cuadro de diálogo **Nuevo proyecto** , seleccione **Web** y elija la **Aplicación Web de ASP.NET**y, a continuación, haga clic en **Aceptar**.

    ![Elija la aplicación Web de ASP.NET][HCVSChooseASPNET]

3. En el cuadro de diálogo **Nuevo proyecto** , elija **MVC**y, a continuación, haga clic en **Aceptar**.

    ![Elija MVC][HCVSChooseMVC]

4. Cuando se ha creado el proyecto, aparecerá la página de archivo Léame de la aplicación. No ejecute todavía el proyecto web.

    ![Página de Léame][HCVSReadmePage]

### <a name="edit-the-database-connection-string-for-the-application"></a>Editar la cadena de conexión de base de datos de la aplicación ###

En este paso, edite la cadena de conexión que indica dónde encontrar la base de datos de SQL Server Express local de la aplicación. Es la cadena de conexión en el archivo de Web.config de la aplicación, que contiene la información de configuración de la aplicación.

> [AZURE.NOTE] Para asegurarse de que la aplicación utiliza la base de datos que creó en SQL Server Express y no el de predeterminado de Visual Studio LocalDB, es importante que haya completado este paso antes de ejecutar el proyecto.

1. En el Explorador de soluciones, haga doble clic en el archivo Web.config.

    ![Web.config][HCVSChooseWebConfig]

2. Editar la sección **connectionStrings** para que apunten a la base de datos de SQL Server en el equipo local, respetando la sintaxis en el ejemplo siguiente:

    ![Cadena de conexión][HCVSConnectionString]

    Al redactar la cadena de conexión, tenga en cuenta lo siguiente:

    - Si se está conectando a una instancia con nombre en lugar de una instancia predeterminada (por ejemplo, YourServer\SQLEXPRESS), debe configurar SQL Server para usar puertos estáticos. Para obtener información sobre la configuración de puertos estáticos, consulte [cómo configurar SQL Server para escuchar en un puerto específico](http://support.microsoft.com/kb/823938). De forma predeterminada, las instancias con nombre utilizan UDP y puertos dinámicos, que no son compatibles con conexiones híbrido.

    - Se recomienda que especifique el puerto (1433 de forma predeterminada, tal como se muestra en el ejemplo) en la cadena de conexión para que puede estar seguro de que su servidor de SQL Server local tiene TCP habilitado y está usando el puerto correcto.

    - Recuerde usar autenticación de SQL Server para conectar, que especifica el identificador de usuario y la contraseña en la cadena de conexión.

3. Haga clic en **Guardar** en Visual Studio para guardar el archivo Web.config.

### <a name="run-the-project-locally-and-register-a-new-user"></a>Ejecute el proyecto localmente y registrar un nuevo usuario ###

1. Ahora, ejecute el nuevo proyecto web localmente haciendo clic en el botón Examinar en depuración. Este ejemplo usa Internet Explorer.

    ![Ejecutar el proyecto][HCVSRunProject]

2. En la esquina superior derecha de la página web predeterminada, elija **registrar** registrar una cuenta nueva:

    ![Registrar una cuenta nueva][HCVSRegisterLocally]

3. Escriba un nombre de usuario y contraseña:

    ![Escriba el nombre de usuario y contraseña][HCVSCreateNewAccount]

    Esto crea automáticamente una base de datos en el servidor SQL Server local que contiene la información de pertenencia de la aplicación. Una de las tablas (**dbo. AspNetUsers**) suspensiones web credenciales de usuario de la aplicación como los que acaba de escribir. Esta tabla se muestra más adelante en el tutorial.

4. Cierre la ventana del explorador de la página web predeterminada. Esto detiene la aplicación en Visual Studio.

Ya está listo para el siguiente paso es publicar la aplicación en Azure y probarlo.

<a name="PubNTest"></a>
## <a name="f-publish-the-web-application-to-azure-and-test-it"></a>F. Publicar la aplicación web en Azure y probarlo ##

Ahora, deberá publicar la aplicación a su aplicación de servicio de aplicaciones web y probarlo para ver cómo se utiliza la conexión de híbrido configurado previamente para conectar la aplicación web de la base de datos en el equipo local.

### <a name="publish-the-web-application"></a>Publicar la aplicación web ###

1. Puede descargar el perfil de publicación para la aplicación de servicio de aplicación web en el Portal de Azure. En el módulo para la aplicación web, haga clic en **obtener publicar perfil**y, a continuación, guarde el archivo en su equipo.

    ![Descargar publicar perfil][PortalDownloadPublishProfile]

    A continuación, se importará este archivo en la aplicación web de Visual Studio.

2. En Visual Studio, haga clic en el nombre del proyecto en el Explorador de soluciones y seleccione **Publicar**.

    ![Seleccione Publicar][HCVSRightClickProjectSelectPublish]

3. En el cuadro de diálogo **Publicar en Web** , en la pestaña **perfil** , elija **Importar**.

    ![Importar][HCVSPublishWebDialogImport]

4. Vaya a su perfil de publicación descargado, selecciónelo y, a continuación, haga clic en **Aceptar**.

    ![Vaya a perfil][HCVSBrowseToImportPubProfile]

5. La información de la publicación se importa y se muestra en la ficha **conexión** del cuadro de diálogo.

    ![Haga clic en publicar][HCVSClickPublish]

    Haga clic en **Publicar**.

    Cuando finalice la publicación, el explorador iniciará y mostrar su aplicación ASP.NET ahora familiar, excepto que ahora es directo en la nube de Azure!

A continuación, usará la aplicación web en vivo para ver su conexión híbrido en acción.

### <a name="test-the-completed-web-application-on-azure"></a>Probar la aplicación web completada en Azure ###

1. En la parte superior derecha de la página web en Azure, elija **iniciar sesión**.

    ![Registro de prueba.][HCTestLogIn]

2. La aplicación de servicio de la aplicación web ahora está conectada a la base de datos de la aplicación web de miembros en el equipo local. Para comprobarlo, inicie sesión con las mismas credenciales que escribió antes de la base de datos local.

    ![Saludo de Hola a todos][HCTestHelloContoso]

3. Para volver a probar la nueva conexión híbrido, cierre la sesión en la aplicación web de Azure y registrar como otro usuario. Proporcione un nuevo nombre de usuario y contraseña y, a continuación, haga clic en **registrar**.

    ![Prueba registrar otro usuario][HCTestRegisterRelecloud]

4. Para comprobar que las credenciales de usuario nuevo se han almacenado en la base de datos local a través de su conexión de híbridos, abra SQL Management Studio en el equipo local. En el Explorador de objetos, expanda la base de datos de **MembershipDB** y, a continuación, expanda **tablas**. Haga clic en la tabla **dbo. AspNetUsers** pertenencia a la tabla y elija **Seleccionar principio 1000 filas** para ver los resultados.

    ![Ver los resultados][HCTestSSMSTree]

5. La tabla de pertenencia local ahora muestra dos cuentas, lo que ha creado localmente y lo que ha creado en la nube de Azure. El elemento que ha creado en la nube se ha guardado en la base de datos local a través de la característica de conexión híbrido de Azure.

    ![Usuarios registrados en la base de datos local][HCTestShowMemberDb]

Ahora ha creado e implementado una aplicación web ASP.NET que usa una conexión de híbrida entre una aplicación web en la nube de Azure y una base de datos de SQL Server local. ¡Felicidades!

## <a name="see-also"></a>Vea también ##
[Introducción a las conexiones híbrido](http://go.microsoft.com/fwlink/p/?LinkID=397274)

[Josh Twist presenta conexiones híbrido (9 de canal de vídeo)](http://channel9.msdn.com/Shows/Azure-Friday/Josh-Twist-introduces-hybrid-connections)

[Introducción a las conexiones híbrido](/services/biztalk-services/)

[Servicios de BizTalk: Fichas de paneles, Monitor, escala, configurar y conexión híbrido](../biztalk-services/biztalk-dashboard-monitor-scale-tabs.md)

[Crear un reales en la nube híbrida con portabilidad de aplicación transparente (9 de canal de vídeo)](http://channel9.msdn.com/events/TechEd/NorthAmerica/2014/DCIM-B323#fbid=)

[Acceso a los recursos locales con conexiones híbrido en el servicio de aplicación de Azure](../app-service-web/web-sites-hybrid-connection-get-started.md)

[Información general de la identidad de ASP.NET](http://www.asp.net/identity)

[AZURE.INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]


<!-- IMAGES -->
[SQLServerInstall]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/A01SQLServerInstall.png
[ChooseDefaultInstance]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/A02ChooseDefaultInstance.png
[ChooseMixedMode]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/A03ChooseMixedMode.png
[SSMSConnectToServer]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/A04SSMSConnectToServer.png
[SSMScreateNewDB]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/A05SSMScreateNewDBlh.png
[SSMSprovideDBname]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/A06SSMSprovideDBname.png
[SSMSMembershipDBCreated]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/A07SSMSMembershipDBCreated.png
[New]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/B01New.png
[NewWebsite]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/B02NewWebsite.png
[WebsiteCreationBlade]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/B03WebsiteCreationBlade.png
[WebSiteRunningBlade]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/B04WebSiteRunningBlade.png
[Browse]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/B05Browse.png
[DefaultWebSitePage]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/B06DefaultWebSitePage.png
[CreateHCHCIcon]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/C01CreateHCHCIcon.png
[CreateHCAddHC]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/C02CreateHCAddHC.png
[TwinCreateHCBlades]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/C03TwinCreateHCBlades.png
[CreateHCCreateBTS]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/C04CreateHCCreateBTS.png
[CreateBTScomplete]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/C05CreateBTScomplete.png
[CreateHCSuccessNotification]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/C06CreateHCSuccessNotification.png
[CreateHCOneConnectionCreated]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/C07CreateHCOneConnectionCreated.png
[HCIcon]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/D01HCIcon.png
[NotConnected]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/D02NotConnected.png
[NotConnectedBlade]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/D03NotConnectedBlade.png
[ClickListenerSetup]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/D04ClickListenerSetup.png
[ClickToInstallHCM]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/D05ClickToInstallHCM.png
[ApplicationRunWarning]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/D06ApplicationRunWarning.png
[UAC]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/D07UAC.png
[HCMInstalling]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/D08HCMInstalling.png
[HCMInstallComplete]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/D09HCMInstallComplete.png
[HCStatusConnected]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/D10HCStatusConnected.png
[HCVSNewProject]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/E01HCVSNewProject.png
[HCVSChooseASPNET]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/E02HCVSChooseASPNET.png
[HCVSChooseMVC]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/E03HCVSChooseMVC.png
[HCVSReadmePage]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/E04HCVSReadmePage.png
[HCVSChooseWebConfig]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/E05HCVSChooseWebConfig.png
[HCVSConnectionString]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/E06HCVSConnectionString.png
[HCVSRunProject]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/E06HCVSRunProject.png
[HCVSRegisterLocally]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/E07HCVSRegisterLocally.png
[HCVSCreateNewAccount]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/E08HCVSCreateNewAccount.png
[PortalDownloadPublishProfile]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/F01PortalDownloadPublishProfile.png
[HCVSPublishProfileInDownloadsFolder]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/F02HCVSPublishProfileInDownloadsFolder.png
[HCVSRightClickProjectSelectPublish]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/F03HCVSRightClickProjectSelectPublish.png
[HCVSPublishWebDialogImport]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/F04HCVSPublishWebDialogImport.png
[HCVSBrowseToImportPubProfile]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/F05HCVSBrowseToImportPubProfile.png
[HCVSClickPublish]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/F06HCVSClickPublish.png
[HCTestLogIn]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/F07HCTestLogIn.png
[HCTestHelloContoso]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/F08HCTestHelloContoso.png
[HCTestRegisterRelecloud]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/F09HCTestRegisterRelecloud.png
[HCTestSSMSTree]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/F10HCTestSSMSTree.png
[HCTestShowMemberDb]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/F11HCTestShowMemberDb.png
