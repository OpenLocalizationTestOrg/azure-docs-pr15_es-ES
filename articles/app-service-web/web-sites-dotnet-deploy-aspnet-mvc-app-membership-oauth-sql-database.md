<properties 
    pageTitle="Crear una aplicación de ASP.NET MVC con auth y DB de SQL e implementar en servicio de la aplicación de Azure" 
    description="Obtenga información sobre cómo desarrollar una aplicación de ASP.NET MVC 5 con una base de datos SQL back-end, agregar autenticación y la autorización e implementar en Azure." 
    services="app-service\web" 
    documentationCenter=".net" 
    authors="Rick-Anderson" 
    writer="Rick-Anderson" 
    manager="wpickett" 
    editor=""/>

<tags 
    ms.service="app-service-web" 
    ms.workload="web" 
    ms.tgt_pltfrm="na" 
    ms.devlang="dotnet" 
    ms.topic="article" 
    ms.date="03/21/2016" 
    ms.author="riande"/> 

# <a name="create-an-aspnet-mvc-app-with-auth-and-sql-db-and-deploy-to-azure-app-service"></a>Crear una aplicación de ASP.NET MVC con auth y DB de SQL e implementar en servicio de la aplicación de Azure

Este tutorial muestra cómo crear una aplicación de web de ASP.NET MVC 5 segura que permite a los usuarios inicien sesión con las credenciales de Facebook o Google. La aplicación es una simple lista de contactos que utiliza el marco de trabajo de entidad de ADO.NET para el acceso de la base de datos. Deberá implementar la aplicación de servicio de la [aplicación](http://go.microsoft.com/fwlink/?LinkId=529714)de Azure. 

En completar el tutorial, tendrá una aplicación web controlados por datos seguro hacia arriba y en la nube y utilizar una base de datos de la nube. La siguiente ilustración muestra la página de inicio de sesión para la aplicación finalizada.

![página de inicio de sesión][rxb]

Aprenderá:

* Cómo crear un proyecto web de ASP.NET MVC 5 seguro en Visual Studio.
* Cómo autenticar y autorizar a los usuarios que inicien sesión con las credenciales de sus cuentas de Google o Facebook (autenticación de proveedor social mediante [OAuth 2.0](http://oauth.net/2 "http://oauth.net/2")).
* Cómo autenticar y autorizar a los usuarios que registran en una base de datos administrado por la aplicación (autenticación local con la [Identidad de ASP.NET](http://asp.net/identity/)).
* Cómo usar el ADO.NET entidad Framework 6 Code First para leer y escribir datos en una base de datos SQL.
* Cómo usar primera migraciones de entidad Framework código para implementar una base de datos.
* Cómo almacenar datos relacionales en la nube mediante el uso de la base de datos de SQL Azure.
* Cómo implementar un proyecto web que usa una base de datos para una [aplicación web](http://go.microsoft.com/fwlink/?LinkId=529714) de servicio de aplicaciones de Azure.

>[AZURE.NOTE] Se trata de un tutorial largo. Si desea una introducción rápida a la aplicación de servicio de Azure y Visual Studio proyectos web, vea [crear una aplicación web de ASP.NET en la aplicación de servicio de Azure](web-sites-dotnet-get-started.md). Para información de solución de problemas, vea la sección [solucionar problemas](#troubleshooting) .
>
>O bien, si desea empezar a trabajar con el servicio de aplicación de Azure antes de suscribirse para una cuenta de Azure, vaya a [Probar el servicio de aplicaciones](http://go.microsoft.com/fwlink/?LinkId=523751), donde puede crear inmediatamente una aplicación web de starter corta duración en la aplicación de servicio. Sin tarjetas de crédito obligatoria; sin compromisos.

## <a name="prerequisites"></a>Requisitos previos

Para completar este tutorial, necesita una cuenta de Microsoft Azure. Si no tiene una cuenta, puede [activar las ventajas de suscriptor de Visual Studio](/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F) o [suscribirse a una prueba gratuita](/pricing/free-trial/?WT.mc_id=A261C142F).

Para configurar el entorno de desarrollo, debe instalar [Visual Studio 2013 actualización 5](http://go.microsoft.com/fwlink/?LinkId=390521) o versiones posteriores y la versión más reciente de [Azure SDK para .NET](http://go.microsoft.com/fwlink/?linkid=324322&clcid=0x409). En este artículo se escribió para 4 de actualización de Visual Studio y SDK 2.8.1. Las mismas instrucciones funcionan de Visual Studio de 2015 con el último [SDK de Azure para .NET](http://go.microsoft.com/fwlink/?linkid=518003&clcid=0x409) instalado, pero algunas pantallas tendrá un aspecto diferentes de las ilustraciones.

## <a name="create-an-aspnet-mvc-5-application"></a>Crear una aplicación de ASP.NET MVC 5

### <a name="create-the-project"></a>Crear el proyecto

1. En el menú **archivo** , haga clic en **Nuevo proyecto**.

    ![Nuevo proyecto en el menú archivo](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/gs13newproj.png)

1. En el cuadro de diálogo **Nuevo proyecto** , expanda **C#** y, a continuación, seleccione la **Aplicación Web de ASP.NET** **Web** en **Plantillas instaladas**. Asigne un nombre a la aplicación **ContactManager**y, a continuación, haga clic en **Aceptar**.

    ![Cuadro de diálogo nuevo proyecto](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/GS13newprojdb.png)
 
    **Nota:** Asegúrese de que escriba "ContactManager". Bloques de código que va a copiar más adelante, se suponen que el nombre del proyecto es ContactManager. 

1. En el cuadro de diálogo **Nuevo proyecto** , seleccione la plantilla **MVC** . Compruebe la **autenticación** se establece en **Cuentas de usuario individuales**, **Host en la nube** está activada y, a continuación, se selecciona la **Aplicación de servicio** .

    ![Cuadro de diálogo nuevo proyecto de ASP.NET](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/newproject.png)

1. Haga clic en **Aceptar**.

1. Aparece el cuadro de diálogo **Configurar Microsoft Azure configuración Web App** . Debe iniciar sesión si aún no lo ha hecho, o si ha caducado su inicio de sesión, volver a escribir sus credenciales.

1. Opcional: cambiar el valor en el **nombre de la aplicación Web** de cuadro (consulte la imagen siguiente).

    La dirección URL de la aplicación web será .azurewebsites {nombre}. NET, por lo que el nombre tiene que ser único en el dominio azurewebsites.net. El Asistente para configuración sugiere un nombre único agregando un número al nombre del proyecto "ContactManager" y que está bien para este tutorial.

5. En el **grupo de recursos** de la lista desplegable seleccione un grupo existente o **Crear nuevo grupo de recursos**(consulte la imagen siguiente). 

    Si lo prefiere, puede seleccionar un grupo de recursos que ya tiene. Pero si crea un nuevo grupo de recursos y solo se utiliza para este tutorial, será fácil eliminar todos los recursos de Azure que ha creado para el tutorial cuando haya terminado con ellos. Para obtener información acerca de los grupos de recursos, vea [información general del Administrador de recursos de Azure](../azure-resource-manager/resource-group-overview.md). 

5. En el **plan de aplicación de servicio** de lista desplegable seleccione un plan existente o **Crear nueva aplicación de servicio planear**(consulte la imagen siguiente).

    Si lo prefiere, puede seleccionar un plan de servicio de aplicación que ya tiene. Para obtener información acerca de los planes de servicio de aplicaciones, vea [introducción exhaustiva de planes de servicio de aplicaciones de Azure](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md). 

1. Puntee en **Servicios de Azure adicionales explorar** para agregar una base de datos SQL.

    ![Agregar nuevos servicios](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/n2.png)

1. Pulse la **+** icono para agregar una base de datos SQL.

    ![Nueva base de datos de SQL](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/nsql.png)

1. Pulse **nuevo** en el cuadro de diálogo **Configurar la base de datos SQL** :

    ![Pw y nombre del Administrador SQL](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/nc.png)

1. Escriba un nombre para el administrador y una contraseña segura.

    ![Nueva base de datos de SQL](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/np.png)

    El nombre del servidor debe ser único. Puede contener letras minúsculas, dígitos numéricos y guiones. No puede contener un guión al final. El nombre de usuario y la contraseña son nuevas credenciales que está creando para el servidor de nuevo. 

    Si ya tiene un servidor de base de datos, puede seleccionar en lugar de crear uno. Servidores de base de datos son un recurso valioso y generalmente desea crear varias bases de datos en el mismo servidor de pruebas y desarrollo en lugar de crear un servidor de base de datos por base de datos. Sin embargo, este tutorial solo necesita el servidor temporalmente y creando el servidor en el mismo grupo de recursos que el sitio web es más fácil eliminar ambos aplicación web de los recursos de la base de datos eliminando el grupo de recursos cuando haya terminado con el tutorial. 

    Si selecciona un servidor de base de datos existente, asegúrese de que su aplicación web y base de datos están en la misma región.

    ![Usar la nueva base de datos](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/newdb.png)

4. Pulse en **crear**.

    Visual Studio crea el proyecto de web ContactManager, crea el grupo de recursos y un plan de servicios de aplicación que ha especificado, y se crea una aplicación web en la aplicación de servicio de Azure con el nombre especificado.

### <a name="set-the-page-header-and-footer"></a>Establecer el encabezado de página y pie de página

1. En el **Explorador de soluciones** , abra el archivo *Layout.cshtml* en la carpeta *Views\Shared* .

    ![_Layout.cshtml en Explorador de soluciones][newapp004]

1. Reemplazar el ActionLink en el archivo *Layout.cshtml* con el código siguiente.


    @Html.ActionLink("CMDemostración","Índice","Contactos"nuevo {área =" "}, el nuevo { @class ="barra de exploración-marca"})
                   

    Asegúrese de que cambiar el tercer parámetro de "Inicio" a "Contactos". La revisión anterior creará un vínculo "Contactos" en cada página para el método de índice del controlador de contactos. Cambiar el nombre de aplicación en el encabezado y el pie de página de "Mi aplicación de ASP.NET" y "Nombre de la aplicación" a "Contact Manager" y "Demostración CM". 
 
### <a name="run-the-application-locally"></a>Ejecute la aplicación localmente

1. Presione CTRL + F5 para ejecutar la aplicación.

    La página de inicio de la aplicación aparece en el explorador predeterminado.

    ![Aplicación Web ejecuta localmente](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/rr2.png)

Esto es todo lo que debe hacer para ahora crear la aplicación que se implementará en Azure. 

## <a name="deploy-the-application-to-azure"></a>Implementar la aplicación en Azure

1. En Visual Studio, haga clic en el proyecto en el **Explorador de soluciones** y seleccione **Publicar** en el menú contextual.

    ![Publicar en el menú contextual del proyecto](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/GS13publish.png)
    
    Se abre el Asistente de **Publicación Web** .

1. En el cuadro de diálogo **Publicar en Web** , haga clic en **Publicar**.

    ![Publicar](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/rr3.png)

    Ahora se está ejecutando la aplicación que creó en la nube. La próxima vez que se implementa la aplicación, se implementan los archivos modificados (o nuevos).

    ![Ejecutar en la nube](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/ss4.PNG)

## <a name="enable-ssl-for-the-project"></a>Habilitar SSL para el proyecto ##

1. En el **Explorador de soluciones**, haga clic en el proyecto **ContactManager** , haga clic en F4 para abrir la ventana de **Propiedades** .

3. Cambiar **SSL habilitado** en **True**. 

4. Copie la **dirección URL de SSL**.

    La dirección URL de SSL será https://localhost:44300 / a menos que haya creado previamente en aplicaciones web SSL.

    ![habilitar SSL][rxSSL]
 
1. En el **Explorador de soluciones**, haga clic con el botón secundario del mouse en el proyecto de **Contact Manager** y haga clic en **Propiedades**.

1. Haga clic en la ficha **Web** .

1. Cambiar la **Dirección Url de Project** para usar la **Dirección URL de SSL** y guarde la página (Control de S).

    ![habilitar SSL](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/rrr1.png)
 
1. Compruebe que Internet Explorer es el explorador que se inicia Visual Studio, como se muestra en la imagen siguiente:

    ![explorador predeterminado](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/ss12.PNG)

    El selector de explorador le permite especificar el explorador que inicia Visual Studio. Puede seleccionar varios exploradores y tiene Visual Studio actualizar cada explorador cuando realice cambios. Para obtener más información, vea [Usar el vínculo de explorador en Visual Studio de 2013](http://www.asp.net/visual-studio/overview/2013/using-browser-link).

    ![selector de explorador](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/ss13.png)

1. Presione CTRL + F5 para ejecutar la aplicación. Haga clic en **Sí** para iniciar el proceso de confiar en el certificado autofirmado que IIS Express ha generado.

     ![instrucciones para confiar en el certificado autofirmado que ha generado IIS Express](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/ss26.PNG)

1. Lea el cuadro de diálogo de **Advertencia de seguridad** y, a continuación, haga clic en **Sí** si desea instalar el certificado que representa el **host local**.

    ![Advertencia de certificado de IIS Express host local ](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/ss27.PNG)

1. Internet Explorer muestra la página de *Inicio* y no hay ninguna advertencia SSL.

     ![IE con SSL de host local y ninguna advertencia](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/ss28.PNG)

     Internet Explorer es una buena opción cuando usa SSL porque acepta el certificado y muestra el contenido HTTPS sin una advertencia. Microsoft Edge y Google Chrome también aceptan el certificado. Firefox utiliza su propio almacén de certificados, se muestra una advertencia.

     ![Advertencia de certificado de FireFox](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/ss30.PNG)

## <a name="add-a-database-to-the-application"></a>Agregar una base de datos a la aplicación

A continuación, deberá actualizar la aplicación para agregar la capacidad para mostrar y actualizar los contactos y almacenar los datos en una base de datos. La aplicación utilizará el marco de entidad (EF) para crear la base de datos y para leer y actualizar datos.

### <a name="add-data-model-classes-for-the-contacts"></a>Agregar clases de modelo de datos para los contactos

Empiece por crear un modelo de datos simple de código.

1. En el **Explorador de soluciones**, haga clic en la carpeta de modelos, haga clic en **Agregar**y, a continuación, en **clase**.

    ![Agregar clase en el menú contextual de carpeta de modelos](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/rr5.png)

2. En el cuadro de diálogo **Agregar nuevo elemento** , nombre al nuevo archivo de clase *Contact.cs*y, a continuación, haga clic en **Agregar**.

    ![Agregar el cuadro de diálogo nuevo elemento][adddb002]

3. Reemplace el contenido del archivo Contact.cs con el código siguiente.

        using System.ComponentModel.DataAnnotations;
        using System.Globalization;
        namespace ContactManager.Models
        {
            public class Contact
            {
                public int ContactId { get; set; }
                public string Name { get; set; }
                public string Address { get; set; }
                public string City { get; set; }
                public string State { get; set; }
                public string Zip { get; set; }
                [DataType(DataType.EmailAddress)]
                public string Email { get; set; }
            }
        }
La clase **póngase en contacto con** define los datos que se guardará para cada contacto, junto con una clave principal, *ID de contacto*, que es necesario para la base de datos.

### <a name="create-web-pages-that-enable-app-users-to-work-with-the-contacts"></a>Crear páginas web que permiten a los usuarios de la aplicación trabajar con los contactos

La característica de scaffolding ASP.NET MVC puede generar automáticamente el código que realiza crear, leer, actualizar y eliminar (CRUD) acciones. 

1. Genere el proyecto **(Ctrl + Mayús + B)**. (Generar el proyecto antes de utilizar el mecanismo de scaffolding).
 
1. En el **Explorador de soluciones**, haga clic en la carpeta de controladores y haga clic en **Agregar**y, a continuación, haga clic en el **controlador**.

    ![Agregar controlador en el menú contextual de carpeta de controladores][addcode001]

5. En el cuadro de diálogo **Agregar Scaffold** , seleccione **MVC 5 controlador con vistas, mediante EF** y, a continuación, haga clic en **Agregar**.
    
    ![Agregar Scaffold dlg](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/rr6.png)

1. En el cuadro de lista desplegable de **clase de modelo** , seleccione el **contacto (ContactManager.Models)**. (Consulte la imagen siguiente).

1. En la **clase de contexto de datos**, seleccione **ApplicationDbContext (ContactManager.Models)**. La **ApplicationDbContext** se usará para la base de datos de pertenencia y los datos de contacto.


    ![Dlg de ctx de datos nueva](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/ss5.PNG)

1. Haga clic en **Agregar**.

   Visual Studio crea un controlador con métodos y vistas para las operaciones de base de datos CRUD para objetos de **contacto** .

## <a name="enable-migrations-create-the-database-add-sample-data-and-a-data-initializer"></a>Habilitar las migraciones, crear la base de datos, agregar datos de ejemplo y un inicializador de datos ##

La siguiente tarea es habilitar la característica de [Código primera migraciones](http://msdn.microsoft.com/library/hh770484.aspx) para crear tablas de base de datos basadas en el modelo de datos que ha creado.

1. En el menú **Herramientas** , seleccione **Administrador de paquetes de NuGet** y **Consola del Administrador de paquetes**.

    ![Consola del Administrador de paquete en el menú Herramientas](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/SS6.png)

2. En la ventana de la **Consola del administrador del paquete** , escriba el siguiente comando:

        enable-migrations

    El comando de **migraciones de habilitar** crea una carpeta de *migraciones* y coloca en la carpeta de un archivo *Configuration.cs* que se puede editar para iniciar la base de datos y configurar las migraciones. 

2. En la ventana de la **Consola del administrador del paquete** , escriba el siguiente comando:

        add-migration Initial


    El comando **inicial de migración agregar** genera un archivo denominado ** &lt;date_stamp&gt;inicial** en la carpeta de *migraciones* . El código de este archivo crea las tablas de base de datos. El primer parámetro ( **inicial** ) se usa para crear el nombre del archivo. Puede ver los nuevos archivos de clase en el **Explorador de soluciones**.

    En la clase **inicial** , el método **de** crea la tabla Contactos y coloca el método **hacia abajo** (se usa cuando desea devolver al estado anterior).

3. Abra el archivo *Migrations\Configuration.cs* . 

4. Agregue las siguientes `using` instrucción. 

         using ContactManager.Models;

5. Reemplace el método de *inicialización* con el siguiente código:

        protected override void Seed(ContactManager.Models.ApplicationDbContext context)
        {
            context.Contacts.AddOrUpdate(p => p.Name,
               new Contact
               {
                   Name = "Debra Garcia",
                   Address = "1234 Main St",
                   City = "Redmond",
                   State = "WA",
                   Zip = "10999",
                   Email = "debra@example.com",
               },
                new Contact
                {
                    Name = "Thorsten Weinrich",
                    Address = "5678 1st Ave W",
                    City = "Redmond",
                    State = "WA",
                    Zip = "10999",
                    Email = "thorsten@example.com",
                },
                new Contact
                {
                    Name = "Yuhong Li",
                    Address = "9012 State st",
                    City = "Redmond",
                    State = "WA",
                    Zip = "10999",
                    Email = "yuhong@example.com",
                },
                new Contact
                {
                    Name = "Jon Orton",
                    Address = "3456 Maple St",
                    City = "Redmond",
                    State = "WA",
                    Zip = "10999",
                    Email = "jon@example.com",
                },
                new Contact
                {
                    Name = "Diliana Alexieva-Bosseva",
                    Address = "7890 2nd Ave E",
                    City = "Redmond",
                    State = "WA",
                    Zip = "10999",
                    Email = "diliana@example.com",
                }
                );
        }

    Este código inicializa (semillas) la base de datos con información de contacto. Para obtener más información sobre el inicio de la base de datos, consulte [Inicio y DBs de depuración entidad Framework (EF)](http://blogs.msdn.com/b/rickandy/archive/2013/02/12/seeding-and-debugging-entity-framework-ef-dbs.aspx). Genere el proyecto para comprobar que no hay ningún error de compilación.

6. En la **Consola del administrador del paquete** , escriba el comando:

        update-database

    ![Comandos de la consola del Administrador de paquetes][addcode009]

    La **actualización de base de datos** se ejecuta la primera migración que crea la base de datos. De forma predeterminada, se crea la base de datos como una base de datos de SQL Server Express LocalDB. 

7. Presione CTRL + F5 para ejecutar la aplicación y, a continuación, haga clic en el vínculo de **Demostración de CM** . o bien, vaya a https://localhost:(port#)/Cm. 

    La aplicación muestra los datos de valor de inicialización y proporciona vínculos de edición, detalles y eliminar. Puede crear, editar, eliminar y ver los datos.

    ![Vista MVC de datos][rx2]

## <a name="add-an-oauth2-provider"></a>Agregar un proveedor de servicios de OAuth2

>[AZURE.NOTE] Para obtener instrucciones detalladas sobre cómo usar la Google y Facebook programador sitios de portal, este tutorial vínculos a tutoriales en el sitio ASP.NET. Sin embargo, Google y Facebook cambian sus sitios con más frecuencia se actualizan los tutoriales y ahora estén actualizados. Si tiene problemas para seguir las instrucciones, vea el comentario Disqus destacado al final de este tutorial para obtener una lista de lo que ha cambiado. 

[OAuth] (http://oauth.net/ "http://OAuth.NET/") es un protocolo abierto que permite autorización segura en un método simple y estándar desde aplicaciones de web, móviles y de escritorio. La plantilla de internet de ASP.NET MVC usa OAuth para exponer Facebook, Twitter, Google y Microsoft como proveedores de autenticación. Aunque este tutorial utiliza solo Google como el proveedor de autenticación, puede modificar fácilmente el código para usar cualquiera de estos proveedores. Los pasos para implementar otros proveedores son muy similares a los pasos que se ve en este tutorial. Para usar Facebook como un proveedor de servicios de autenticación, vea [MVC 5 aplicación con Facebook, Twitter, LinkedIn y Google OAuth2 inicio de sesión ](http://www.asp.net/mvc/tutorials/mvc-5/create-an-aspnet-mvc-5-app-with-facebook-and-google-oauth2-and-openid-sign-on).

Además de la autenticación, este tutorial usa roles para implementar la autorización. Sólo los usuarios que agregue a la función *canEdit* se pueden cambiar los datos (es decir, crear, editar o eliminar contactos).

1. Siga las instrucciones de la [Aplicación de 5 MVC con Facebook, Twitter, LinkedIn y Google OAuth2 inicio de sesión](http://www.asp.net/mvc/tutorials/mvc-5/create-an-aspnet-mvc-5-app-with-facebook-and-google-oauth2-and-openid-sign-on#goog) en la sección **crear una aplicación de Google para 2 OAuth configurar una aplicación de Google para OAuth2**.

3. Ejecutar y probar la aplicación para comprobar que pueden iniciar sesión usando autenticación de Google.

2. Si desea crear botones de inicio de sesión sociales con iconos específicas del proveedor, vea [botones de inicio de sesión bastante sociales para ASP.NET MVC 5](http://www.jerriepelser.com/blog/pretty-social-login-buttons-for-asp-net-mvc-5)

## <a name="using-the-membership-api"></a>Uso de la API de pertenencia

En esta sección agregará un usuario local y la función *canEdit* a la base de datos de pertenencia. Sólo los usuarios de la función *canEdit* podrán modificar los datos. Un procedimiento recomendado es roles nombre por las acciones que pueden realizar, por lo que es preferible *canEdit* frente a una función de *Administrador*. Cuando la aplicación evoluciona, puede agregar nuevas funciones como *canDeleteMembers* en lugar de la menos descriptivo *superAdmin*.

1. Abra el archivo *migrations\configuration.cs* y agregue las siguientes `using` instrucciones:

        using Microsoft.AspNet.Identity;
        using Microsoft.AspNet.Identity.EntityFramework;

1. Agregue el siguiente método **AddUserAndRole** a la clase:

        bool AddUserAndRole(ContactManager.Models.ApplicationDbContext context)
        {
            IdentityResult ir;
            var rm = new RoleManager<IdentityRole>
                (new RoleStore<IdentityRole>(context));
            ir = rm.Create(new IdentityRole("canEdit"));
            var um = new UserManager<ApplicationUser>(
                new UserStore<ApplicationUser>(context));
            var user = new ApplicationUser()
            {
                UserName = "user1@contoso.com",
            };
            ir = um.Create(user, "P_assw0rd1");
            if (ir.Succeeded == false)
                return ir.Succeeded;
            ir = um.AddToRole(user.Id, "canEdit");
            return ir.Succeeded;
        }

1. Llame al nuevo método desde el método de **valor de inicialización** :

        protected override void Seed(ContactManager.Models.ApplicationDbContext context)
        {
            AddUserAndRole(context);
            context.Contacts.AddOrUpdate(p => p.Name,
                // Code removed for brevity
        }

    Las siguientes imágenes muestran los cambios al método de *valor de inicialización* :

    ![imagen de código](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/ss24.PNG)

    Este código crea una nueva función denominada *canEdit*, crea un nuevo usuario local *user1@contoso.com*y se agrega *user1@contoso.com* a la función *canEdit* . Para obtener más información, vea los [tutoriales de identidad de ASP.NET](http://www.asp.net/identity/overview/features-api) en el sitio ASP.NET.

## <a name="use-temporary-code-to-add-new-social-login-users-to-the-canedit-role"></a>Usar código temporal para agregar nuevos usuarios de inicio de sesión sociales para el rol de canEdit  ##

En esta sección se modifique temporalmente el método de **ExternalLoginConfirmation** en el controlador de cuenta para agregar nuevos usuarios registrar con un proveedor de OAuth a la función *canEdit* . Esperamos proporcionar una herramienta similar a [WSAT](http://msdn.microsoft.com/library/ms228053.aspx) en el futuro que le permitirá crear y editar cuentas de usuario y funciones. Hasta ese momento, puede realizar la misma función usando código temporal.

1. Abra el archivo **Controllers\AccountController.cs** y navegue hasta el método **ExternalLoginConfirmation** .

1. Agregue la siguiente llamada a **AddToRoleAsync** antes de la llamada **SignInAsync** .

        await UserManager.AddToRoleAsync(user.Id, "canEdit");

   El código anterior, agrega el usuario recién registrado para la función "canEdit", que proporciona acceso a los métodos de acción que cambian los datos (Editar). El fragmento de código siguiente muestra la nueva línea de código en contexto.

          // POST: /Account/ExternalLoginConfirmation
          [HttpPost]
          [AllowAnonymous]
          [ValidateAntiForgeryToken]
          public async Task ExternalLoginConfirmation(ExternalLoginConfirmationViewModel model, string returnUrl)
          {
             if (User.Identity.IsAuthenticated)
             {
                return RedirectToAction("Index", "Manage");
             }
             if (ModelState.IsValid)
             {
                // Get the information about the user from the external login provider
                var info = await AuthenticationManager.GetExternalLoginInfoAsync();
                if (info == null)
                {
                   return View("ExternalLoginFailure");
                }
                var user = new ApplicationUser { UserName = model.Email, Email = model.Email };
                var result = await UserManager.CreateAsync(user);
                if (result.Succeeded)
                {
                   result = await UserManager.AddLoginAsync(user.Id, info.Login);
                   if (result.Succeeded)
                   {
                      await UserManager.AddToRoleAsync(user.Id, "canEdit");
                      await SignInManager.SignInAsync(user, isPersistent: false, rememberBrowser: false);
                      return RedirectToLocal(returnUrl);
                   }
                }
                AddErrors(result);
             }
             ViewBag.ReturnUrl = returnUrl;
             return View(model);
          }

Más adelante en el tutorial se implementará la aplicación en Azure, donde que se inicie sesión con Google u otro proveedor de autenticación de terceros. Esto agregará la cuenta recién registrada a la función *canEdit* . A continuación, cualquier persona que se encuentra la dirección URL de la aplicación web y tiene un identificador de Google puede registrar y actualizar la base de datos. Para evitar que otras personas hacerlo, puede dejar el sitio. Podrá comprobar quién está en la función *canEdit* examinando la base de datos.

En la **Consola del Administrador de paquetes** de presionar la tecla de flecha arriba para que aparezca el siguiente comando:

        Update-Database

El comando de **Actualización de base de datos** ejecuta el método de **valor de inicialización** y que ejecuta el método **AddUserAndRole** que agregó anteriormente. El método **AddUserAndRole** crea el usuario *user1@contoso.com* y lo agrega a la función *canEdit* .

## <a name="protect-the-application-with-ssl-and-the-authorize-attribute"></a>Proteger la aplicación con SSL y el atributo de autorizar ##

En esta sección se aplica el atributo de [autorizar](http://msdn.microsoft.com/library/system.web.mvc.authorizeattribute.aspx) para restringir el acceso a los métodos de acción. Los usuarios anónimos podrán ver sólo el método de acción de **índice** del controlador de inicio. Los usuarios registrados podrán ver datos de contacto (las páginas de **índice** y **Detalles** del controlador Cm), la página acerca y la página de contacto. Solo los usuarios de la función *canEdit* podrán acceso a los métodos de acción que cambian los datos.

1. Abra el archivo *App_Start\FilterConfig.cs* y reemplace el método *RegisterGlobalFilters* con la siguiente (que agrega los dos filtros):

        public static void RegisterGlobalFilters(GlobalFilterCollection filters)
        {
            filters.Add(new HandleErrorAttribute());
            filters.Add(new System.Web.Mvc.AuthorizeAttribute());
            filters.Add(new RequireHttpsAttribute());
        }
        
    Este código agrega el filtro de [autorizar](http://msdn.microsoft.com/library/system.web.mvc.authorizeattribute.aspx) y el filtro [RequireHttps](http://msdn.microsoft.com/library/system.web.mvc.requirehttpsattribute.aspx) a la aplicación. El filtro de [autorizar](http://msdn.microsoft.com/library/system.web.mvc.authorizeattribute.aspx) impide que los usuarios anónimos tengan acceso a los métodos en la aplicación. El atributo [AllowAnonymous](http://blogs.msdn.com/b/rickandy/archive/2012/03/23/securing-your-asp-net-mvc-4-app-and-the-new-allowanonymous-attribute.aspx) usará para optar por el requisito de autorización de un par de métodos para que los usuarios anónimos pueden iniciar sesión y pueden ver la página principal. La [RequireHttps](http://msdn.microsoft.com/library/system.web.mvc.requirehttpsattribute.aspx) requiere que todo el acceso a la aplicación web a través de HTTPS.

    Un enfoque alternativo es agregar los atributos de [autorizar](http://msdn.microsoft.com/library/system.web.mvc.authorizeattribute.aspx) y [RequireHttps](http://msdn.microsoft.com/library/system.web.mvc.requirehttpsattribute.aspx) a cada controlador, pero es recomendable aplicarlos a toda la aplicación. Para insertarlas globalmente, cada nuevo método de controlador y acción que agrega automáticamente está protegida, no necesita recordar aplicarlos. Para obtener más información, vea [proteger su aplicación de MVC de ASP.NET y los nuevos atributos de AllowAnonymous](http://blogs.msdn.com/b/rickandy/archive/2012/03/23/securing-your-asp-net-mvc-4-app-and-the-new-allowanonymous-attribute.aspx). 

1. Agregue el atributo [AllowAnonymous](http://blogs.msdn.com/b/rickandy/archive/2012/03/23/securing-your-asp-net-mvc-4-app-and-the-new-allowanonymous-attribute.aspx) para el método de **índice** del controlador de inicio. El atributo [AllowAnonymous](http://blogs.msdn.com/b/rickandy/archive/2012/03/23/securing-your-asp-net-mvc-4-app-and-the-new-allowanonymous-attribute.aspx) permite lista blanca los métodos que desea dejar de autorización de usar. 

        public class HomeController : Controller
        {
          [AllowAnonymous]
          public ActionResult Index()
          {
             return View();
          }

    Si realiza una búsqueda global para *AllowAnonymous*, verá que se utiliza en los métodos de inicio de sesión y registro del controlador de cuenta.

1. En *CmController.cs*, agregue `[Authorize(Roles = "canEdit")]` a los métodos HttpGet y HttpPost que cambian los datos (crear, editar, eliminar, cada método de acción excepto índice y detalles) en el controlador de *Cm* . A continuación se muestra una parte del código completado: 

        // GET: Cm/Create
        [Authorize(Roles = "canEdit")]
        public ActionResult Create()
        {
           return View(new Contact { Address = "123 N 456 W",
            City="Great Falls", Email = "ab@cd.com", Name="Joe Smith", State="MT",
           Zip = "59405"});
        }
        // POST: Cm/Create
        // To protect from overposting attacks, please enable the specific properties you want to bind to, for 
        // more details see http://go.microsoft.com/fwlink/?LinkId=317598.
        [HttpPost]
        [ValidateAntiForgeryToken]
         [Authorize(Roles = "canEdit")]
        public ActionResult Create([Bind(Include = "ContactId,Name,Address,City,State,Zip,Email")] Contact contact)
        {
            if (ModelState.IsValid)
            {
                db.Contacts.Add(contact);
                db.SaveChanges();
                return RedirectToAction("Index");
            }
            return View(contact);
        }
        // GET: Cm/Edit/5
        [Authorize(Roles = "canEdit")]
        public ActionResult Edit(int? id)
        {
            if (id == null)
            {
                return new HttpStatusCodeResult(HttpStatusCode.BadRequest);
            }
            Contact contact = db.Contacts.Find(id);
            if (contact == null)
            {
                return HttpNotFound();
            }
            return View(contact);
        }
        
1. Presione CTRL + F5 para ejecutar la aplicación.

1. Si sigue ha iniciado sesión de una sesión anterior, presione el vínculo **Cerrar sesión** .

1. Haga clic en el **sobre** o **contacto** vínculos. Se le redirige a la página de inicio de sesión porque los usuarios anónimos no pueden ver las páginas.

1. Haga clic en el vínculo **registrarse como un nuevo usuario** y agregar un usuario local con el correo electrónico *joe@contoso.com*. Comprobar *Joe* puede ver el inicio de aproximadamente y póngase en contacto con páginas. 

    ![inicio de sesión](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/ss14.PNG)

1. Haga clic en el vínculo de *Demostración de CM* y compruebe que puede ver los datos.

1. Haga clic en un vínculo para editar en la página, se le redirigirá a la página de inicio de sesión (porque no se agrega un nuevo usuario local a la función *canEdit* ).

1. Inicie sesión como *user1@contoso.com* con la contraseña "P_assw0rd1" (el "0" en "word" es un cero). Se le redirigirá a la página Editar seleccionado anteriormente. 
2. 

    Si no puede iniciar sesión con esa cuenta y la contraseña, pruebe a copiar la contraseña del código fuente y lo pega. Si aún no puede iniciar sesión, consulte la columna **nombre de usuario** de la tabla **AspNetUsers** para comprobar *user1@contoso.com* se ha agregado. 

1. Compruebe que puede realizar cambios en los datos.

## <a name="deploy-the-app-to-azure"></a>Implementar la aplicación en Azure

1. En Visual Studio, haga clic en el proyecto en el **Explorador de soluciones** y seleccione **Publicar** en el menú contextual.

    ![Publicar en el menú contextual del proyecto][firsdeploy003]

    Se abre el Asistente de **Publicación Web** .

1. Haga clic en la pestaña **configuración** en el lado izquierdo del cuadro de diálogo **Publicar en Web** . 

2. En **ApplicationDbContext** seleccione la base de datos creada cuando creó el proyecto.
   

1. En **ContactManagerContext**, seleccione **Ejecutar código primera migraciones**.

    ![Configuración](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/rrc2.png)

1. Haga clic en **Publicar**.

1. Inicie sesión como *user1@contoso.com* (con contraseña de "P_assw0rd1") y compruebe que se pueden editar los datos.

1. Cerrar la sesión.

1. Vaya a la [Consola de programadores de Google](https://console.developers.google.com/) y en la actualización de ficha **credenciales** la redirección URI y JavaScript Orgins para usar la dirección URL de Azure.

1. Inicie sesión con Google o Facebook. La función **canEdit** que agregará la cuenta de Google o Facebook. Si recibe un error de HTTP 400 con el mensaje *la redirección URI de la solicitud: https://contactmanager {mi version}.azurewebsites.net/signin-google no coincide con una redirección registrada URI.*, tendrá que esperar hasta que se propagarán los cambios realizados. Si recibe este error después de unos minutos, compruebe los URI son correctos.

### <a name="stop-the-web-app-to-prevent-other-people-from-registering"></a>Detener la aplicación web para impedir que otras personas se registren  

1. En el **Explorador de servidores**, vaya a **Azure > aplicación de servicio > {su grupo de recursos} > {su aplicación web}**.

4. Haga clic en la aplicación web y seleccione **Detener**. 

    O bien, desde el [Portal de Azure](https://portal.azure.com/), puede ir a módulo de la aplicación web y luego haga clic en el icono **Detener** en la parte superior de la hoja.

    ![detener el portal de aplicación web](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/stopweb.png)

### <a name="remove-addtoroleasync-publish-and-test"></a>Quitar AddToRoleAsync, publicar y probar

1. Comentar o quite el siguiente código del método **ExternalLoginConfirmation** en el controlador de cuenta:

        await UserManager.AddToRoleAsync(user.Id, "canEdit");

1. Genere el proyecto (que guarda los cambios de archivo y comprueba que no tiene los errores de compilación).

5. Haga clic en el proyecto en el **Explorador de soluciones** y seleccione **Publicar**.

       ![Publish in project context menu](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/GS13publish.png)
    
4. Haga clic en el botón de **Vista previa de inicio** . Se implementan únicamente los archivos que necesitan actualizarse.

5. Inicie la aplicación web de Visual Studio o desde el Portal. **No podrá publicar mientras se detiene la aplicación web**.

    ![Iniciar aplicación web](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/ss15.png)

5. Vuelva a Visual Studio y haga clic en **Publicar**.

3. Abre la aplicación de Azure en el explorador predeterminado. Si ha iniciado sesión, cierre la sesión para que pueda ver la página de inicio como un usuario anónimo.  

4. Haga clic en el vínculo **acerca de** . Se le redirigirá en el registro de la página.

5. Haga clic en el vínculo **registrarse** en el registro de la página y crear cuenta local. Se usará esta cuenta local para comprobar que puede tener acceso a las páginas sola lecturas, pero no puede acceder a las páginas que cambian los datos (que están protegidos por el rol *canEdit* ). Más adelante en el tutorial se quitará el acceso a la cuenta local. 

    ![Registrar](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/ss16.PNG)

1. Compruebe que puede navegar a las páginas *acerca de* y de *contacto* .

    ![Cerrar sesión](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/ss17.PNG)

1. Haga clic en el vínculo de **Demostración de CM** para navegar hasta el controlador de **Cm** . Como alternativa, puede anexar *Cm* a la dirección URL. 

    ![Página de CM](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/rrr4.png)
 
1. Haga clic en un vínculo de edición. 

    Se le redirigirá a la página de inicio de sesión. 

2. En **usar otro servicio para iniciar sesión**, haga clic en Google o Facebook y registro con la cuenta que previamente registrado. (Si está trabajando rápidamente y la cookie de la sesión no se ha agotado, se automáticamente conectará con la cuenta de Google o Facebook que usaba anteriormente.)

2. Compruebe que se pueden modificar los datos mientras está registrado en esa cuenta.

    **Nota:** No puede iniciar sesión de fuera de Google de esta aplicación e inicie sesión en una cuenta de google diferentes con el mismo explorador. Si está utilizando un explorador, tendrá que vaya a Google y cierre la sesión. Puede iniciar sesión con otra cuenta de la misma autenticador de terceros (como Google) mediante un explorador diferente.

    Si no han rellenado el nombre y el apellido de la información de su cuenta de Google, se producirá una excepción NullReferenceException.

## <a name="examine-the-sql-azure-db"></a>Examine la instrucción SQL Azure DB ##

1. En el **Explorador de servidores**, vaya a **Azure > bases de datos SQL > {la base de datos}**

2. Haga clic con el botón secundario del mouse en la base de datos y, a continuación, seleccione **Abrir en el Explorador de objetos de SQL Server**.
 
    ![Abrir en SSOX](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/rrr12.png)
 
3. Si todavía no lo ha conectado a esta base de datos previamente, puede que se le pregunte si para agregar una regla de firewall para habilitar el acceso de la dirección IP actual. La dirección IP se rellenará automáticamente. Simplemente haga clic en **Agregar regla de Firewall** para habilitar el acceso.

    ![Agregar regla de firewall](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/addfirewallrule.png)

3. Inicie sesión en la base de datos con el nombre de usuario y la contraseña que especificó al crear el servidor de base de datos. 
 
1. Haga clic con el botón secundario del mouse en la tabla **AspNetUsers** y seleccione la **Vista de datos**.

    ![Página de CM](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/rrr8.png)
 
1. Nota El identificador de la cuenta de Google registrado con a estar en el rol **canEdit** y el identificador de *user1@contoso.com*. Deben ser los únicos usuarios de la función **canEdit** . (Tendrá que comprobar en el siguiente paso.)

    ![Página de CM](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/s2.png)
 
2. En el **Explorador de objetos de SQL Server**, haga clic con el botón secundario en **AspNetUserRoles** y seleccione la **Vista de datos**.

    ![Página de CM](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/rs1.png)
 
3. Compruebe que es el **identificador de usuario** de *user1@contoso.com* y la cuenta de Google que haya registrado. 

## <a name="troubleshooting"></a>Solución de problemas

Si tiene problemas, estas son algunas sugerencias para qué puede probar.

* Errores de aprovisionamiento de SQL de base de datos: asegúrese de que tiene instalado el SDK actual. Las versiones anteriores 2.8.1 tienen un error en algunos escenarios que hace que errores cuando intenta VS crear la base de datos o el servidor de base de datos.
* El mensaje de error "operación no se admite su tipo de oferta de suscripción" al crear recursos Azure - igual al anterior.
* Errores al implementar - plantéese mediante el artículo de la [implementación de ASP.NET básica](web-sites-dotnet-get-started.md) . Que es más sencillo escenario de implementación y si tiene el mismo problema puede resultar más fácil aislar. Por ejemplo, en algunos entornos de empresa un firewall corporativo puede impedir Web implementar realizar los tipos de conexiones a Azure que requiere.
* Ninguna opción para seleccionar la cadena de conexión en el Asistente para la publicación Web cuando se implementa - si utiliza otro método para crear los recursos de Azure (por ejemplo, está intentando implementar una aplicación web y crea una base de datos SQL en el Portal), la base de datos SQL no se pueden asociar con la aplicación web. La solución más sencilla es crear una nueva aplicación web y base de datos mediante VS tal como se muestra en el tutorial. No tiene que volver a empezar el tutorial: en el Asistente para la publicación Web puede optar por crear una nueva aplicación web y recibe el cuadro de diálogo de creación de recursos de Azure mismo que se obtiene al crear el proyecto.
* Instrucciones para el portal de programadores de Google o Facebook están actualizados: consulte el comentario Disqus destacado al final de este tutorial.

## <a name="next-steps"></a>Pasos siguientes

Ha creado una aplicación web de ASP.NET MVC básica que autentica a los usuarios. Para obtener más información sobre las tareas comunes de autenticación y cómo proteger datos confidenciales, vea los siguientes tutoriales.

- [Crear una aplicación web de ASP.NET MVC 5 segura con el registro de restablecimiento de contraseña y de confirmación de correo electrónico](http://www.asp.net/mvc/overview/getting-started/create-an-aspnet-mvc-5-web-app-with-email-confirmation-and-password-reset)
- [Aplicación de ASP.NET MVC 5 con SMS y correo electrónico autenticación en dos fases](http://www.asp.net/mvc/overview/getting-started/aspnet-mvc-5-app-with-sms-and-email-two-factor-authentication)
- [Prácticas recomendadas para implementar contraseñas y otros datos confidenciales en Azure y ASP.NET](http://www.asp.net/identity/overview/features-api/best-practices-for-deploying-passwords-and-other-sensitive-data-to-aspnet-and-azure) 
- [Crear una aplicación de ASP.NET MVC 5 con Facebook y Google OAuth2](http://www.asp.net/mvc/tutorials/mvc-5/create-an-aspnet-mvc-5-app-with-facebook-and-google-oauth2-and-openid-sign-on ) Esto incluye instrucciones sobre cómo agregar datos de perfil a la base de datos de registro de usuario y para obtener instrucciones detalladas sobre el uso de Facebook como un proveedor de servicios de autenticación.
- [Introducción a ASP.NET MVC 5](http://www.asp.net/mvc/tutorials/mvc-5/introduction/getting-started)

Para obtener un tutorial acerca de cómo usar el marco de trabajo de entidad, vea [Introducción a EF y MVC](http://www.asp.net/mvc/tutorials/getting-started-with-ef-using-mvc/creating-an-entity-framework-data-model-for-an-asp-net-mvc-application)más avanzado.

En este tutorial se escribió por [Anderson enriquecido](http://blogs.msdn.com/b/rickandy/) (Twitter [@RickAndMSFT](https://twitter.com/RickAndMSFT)) con la asistencia de Tom Dykstra y Barry Dorrans (Twitter [@blowdart](https://twitter.com/blowdart)). 

***Por favor votar*** en qué le gustó o lo que le gustaría ver mejorado, no solo sobre el tutorial propio, sino también acerca de los productos que se muestran. Sus comentarios nos ayudarán a prioridades mejoras. También puede solicitar y votar nuevos temas [Mostrar cómo con](http://aspnet.uservoice.com/forums/228522-show-me-how-with-code)código.

## <a name="whats-changed"></a>¿Qué ha cambiado

* Encontrará una guía para el cambio de sitios Web a la aplicación de servicio: [servicio de aplicación de Azure y su influencia sobre los servicios de Azure existentes](http://go.microsoft.com/fwlink/?LinkId=529714)

<!-- bookmarks -->
[Add an OAuth Provider]: #addOauth
[Using the Membership API]:#mbrDB
[Create a Data Deployment Script]:#ppd
[Update the Membership Database]:#ppd2

[setupwindowsazureenv]: #bkmk_setupwindowsazure
[createapplication]: #bkmk_createmvc4app
[deployapp1]: #bkmk_deploytowindowsazure1
[deployapp11]: #bkmk_deploytowindowsazure11
[adddb]: #bkmk_addadatabase


<!-- images-->
[rx2]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/rx2.png

[rx5]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rx5.png
[rx6]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rx6.png
[rx7]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rx7.png
[rx8]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rx8.png
[rx9]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rx9.png

[rxb]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/rxb.png


[rxSSL]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/rxSSL.png

[rxNOT]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rxNOT.png
[rxNOT2]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rxNOT2.png

[rxNOT]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rxNOT.png
[rxNOT]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rxNOT.png
[rxNOT]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rxNOT.png
[rr1]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rr1.png

[rxPrevDB]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rxPrevDB.png

[rxWSnew]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rxWSnew2.png
[rxCreateWSwithDB]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rxCreateWSwithDB.png

[setup007]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/dntutmobile-setup-azure-site-004.png

[newapp004]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/dntutmobile-createapp-004.png

[firsdeploy003]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/dntutmobile-deploy1-publish-001.png

[adddb002]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/dntutmobile-adddatabase-002.png
[addcode001]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/dntutmobile-controller-add-context-menu.png

[addcode008]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/dntutmobile-migrations-package-manager-menu.png
[addcode009]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/dntutmobile-migrations-package-manager-console.png


[Important information about ASP.NET in Azure web apps]: #aspnetwindowsazureinfo
[Next steps]: #nextsteps

[ImportPublishSettings]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ImportPublishSettings.png
 
