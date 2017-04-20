<properties 
    pageTitle="Crear un servicio REST ASP.NET Web API y con base de datos de SQL Azure aplicación de servicio" 
    description="Tutorial que le enseña cómo implementar una aplicación que usa la API de Web de ASP.NET para una aplicación web de Azure mediante Visual Studio." 
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
    ms.date="02/29/2016" 
    ms.author="riande"/>

# <a name="create-a-rest-service-using-aspnet-web-api-and-sql-database-in-azure-app-service"></a>Crear un servicio REST ASP.NET Web API y con base de datos de SQL Azure aplicación de servicio

Este tutorial muestra cómo implementar una aplicación web de ASP.NET a un [Servicio de aplicación de Azure](http://go.microsoft.com/fwlink/?LinkId=529714) mediante el Asistente de publicación Web en 2013 de Visual Studio o Visual Studio 2013 Community Edition. 

Puede abrir una cuenta de Azure de forma gratuita y, si todavía no tiene 2013 de Visual Studio, el SDK instala automáticamente 2013 de Visual Studio para Web Express. Así que puede comenzar a desarrollar para Azure por completo de forma gratuita.

En este tutorial, se supone que no tiene experiencia previa con Azure. En completar este tutorial, tendrá una aplicación web simple hacia arriba y en la nube.
 
Aprenderá:

* Cómo habilitar el equipo para el desarrollo de Azure instalando el SDK de Azure.
* Cómo crear un proyecto de Visual Studio ASP.NET MVC 5 y publicarlo en una aplicación de Azure.
* Cómo utilizar la API de Web de ASP.NET para habilitar las llamadas de la API de REST.
* Cómo utilizar una base de datos SQL para almacenar los datos en Azure.
* Cómo publicar actualizaciones de la aplicación en Azure.

Deberá crear una aplicación web de simple lista de contactos que se basa en ASP.NET MVC 5 y utiliza el marco de trabajo de entidad de ADO.NET para el acceso de la base de datos. La siguiente ilustración muestra la aplicación completa:

![captura de pantalla del sitio web][intro001]

<!-- the next line produces the "Set up the development environment" section as see at http://azure.microsoft.com/documentation/articles/web-sites-dotnet-get-started/ -->
[AZURE.INCLUDE [create-account-and-websites-note](../../includes/create-account-and-websites-note.md)]

### <a name="create-the-project"></a>Crear el proyecto

1. Inicie 2013 de Visual Studio.
1. En el menú **archivo** , haga clic en **Nuevo proyecto**.
3. En el cuadro de diálogo **Nuevo proyecto** , expanda **Visual C#** , seleccione **Web** y, a continuación, seleccione la **Aplicación Web de ASP.NET**. Nombre de la aplicación **ContactManager** y haga clic en **Aceptar**.

    ![Cuadro de diálogo nuevo proyecto](./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rr4.png)

1. En el cuadro de diálogo **Nuevo proyecto** , seleccione la plantilla **MVC** , compruebe la **API de Web** y, a continuación, haga clic en **Cambiar autenticación**.

1. En el cuadro de diálogo **Cambiar autenticación** , haga clic en **Sin autenticación**y, a continuación, haga clic en **Aceptar**.

    ![Sin autenticación](./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/GS13noauth.png)

    La aplicación de ejemplo que está creando no tiene características que requieren el registro de usuarios. Para obtener información sobre cómo implementar las características de autenticación y la autorización, vea la sección [Pasos siguientes](#nextsteps) al final de este tutorial. 

1. En el cuadro de diálogo **Nuevo proyecto de ASP.NET** , asegúrese de que el **Host en la nube** está activada y haga clic en **Aceptar**.


Si ha no ha iniciado sesión Azure, se le pedirá que iniciar sesión.

1. El Asistente para configuración sugerirá un nombre único basado en *ContactManager* (consulte la imagen siguiente). Seleccione un área cerca de usted. Puede usar [azurespeed.com](http://www.azurespeed.com/ "AzureSpeed.com") para encontrar el centro de datos de latencia más bajo. 
2. Si todavía no lo ha creado un servidor de base de datos antes de, seleccione **Crear nuevo servidor**, escriba un nombre de usuario de la base de datos y una contraseña.

    ![Configurar el sitio Web de Azure](./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/configAz.PNG)

Si tiene un servidor de base de datos, se use para crear una nueva base de datos. Servidores de base de datos son un recurso valioso y generalmente desea crear varias bases de datos en el mismo servidor de pruebas y desarrollo en lugar de crear un servidor de base de datos por base de datos. Asegúrese de que su sitio web y la base de datos se encuentran en la misma región.

![Configurar el sitio Web de Azure](./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/configWithDB.PNG)

### <a name="set-the-page-header-and-footer"></a>Establecer el encabezado de página y pie de página


1. En el **Explorador de soluciones**, expanda la carpeta *Views\Shared* y abra el archivo *_Layout.cshtml* .

    ![_Layout.cshtml en Explorador de soluciones][newapp004]

1. Reemplace el contenido del archivo *Views\Shared_Layout.cshtml* con el siguiente código:


        <!DOCTYPE html>
        <html lang="en">
        <head>
            <meta charset="utf-8" />
            <title>@ViewBag.Title - Contact Manager</title>
            <link href="~/favicon.ico" rel="shortcut icon" type="image/x-icon" />
            <meta name="viewport" content="width=device-width" />
            @Styles.Render("~/Content/css")
            @Scripts.Render("~/bundles/modernizr")
        </head>
        <body>
            <header>
                <div class="content-wrapper">
                    <div class="float-left">
                        <p class="site-title">@Html.ActionLink("Contact Manager", "Index", "Home")</p>
                    </div>
                </div>
            </header>
            <div id="body">
                @RenderSection("featured", required: false)
                <section class="content-wrapper main-content clear-fix">
                    @RenderBody()
                </section>
            </div>
            <footer>
                <div class="content-wrapper">
                    <div class="float-left">
                        <p>&copy; @DateTime.Now.Year - Contact Manager</p>
                    </div>
                </div>
            </footer>
            @Scripts.Render("~/bundles/jquery")
            @RenderSection("scripts", required: false)
        </body>
        </html>
            
El marcado encima cambia el nombre de la aplicación de "Mi aplicación de ASP.NET" a "Contact Manager" y quitan los vínculos al **Inicio**, **sobre** y de **contacto**.

### <a name="run-the-application-locally"></a>Ejecute la aplicación localmente

1. Presione CTRL + F5 para ejecutar la aplicación.
La página de inicio de la aplicación aparece en el explorador predeterminado.
    ![En la página de inicio de la lista de tareas pendientes](./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rr5.png)

Esto es todo lo que debe hacer para ahora crear la aplicación que se implementará en Azure. Más adelante, deberá agregar funcionalidad de base de datos.

## <a name="deploy-the-application-to-azure"></a>Implementar la aplicación en Azure

1. En Visual Studio, haga clic en el proyecto en el **Explorador de soluciones** y seleccione **Publicar** en el menú contextual.

    ![Publicar en el menú contextual del proyecto][PublishVSSolution]

    Se abre el Asistente de **Publicación Web** .

12. Haga clic en **Publicar**.

![Pestaña Configuración](./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/pw.png)

Visual Studio inicia el proceso de copiar los archivos en el servidor de Azure. La ventana de **salida** muestra las acciones de implementación tomadas y finalización correcta de la implementación de informes.

14. El explorador predeterminado se abre automáticamente a la dirección URL del sitio de implementada.

    Ahora se está ejecutando la aplicación que creó en la nube.
    
    ![A la página de inicio de lista de tareas pendientes ejecuta en Azure][rxz2]

## <a name="add-a-database-to-the-application"></a>Agregar una base de datos a la aplicación

A continuación, deberá actualizar la aplicación MVC para agregar la capacidad para mostrar y actualizar los contactos y almacenar los datos en una base de datos. La aplicación utilizará el marco de trabajo de entidad para crear la base de datos y para leer y actualizar datos en la base de datos.

### <a name="add-data-model-classes-for-the-contacts"></a>Agregar clases de modelo de datos para los contactos

Empiece por crear un modelo de datos simple de código.

1. En el **Explorador de soluciones**, haga clic en la carpeta de modelos, haga clic en **Agregar**y, a continuación, en **clase**.

    ![Agregar clase en el menú contextual de carpeta de modelos][adddb001]

2. En el cuadro de diálogo **Agregar nuevo elemento** , nombre al nuevo archivo de clase *Contact.cs*y, a continuación, haga clic en **Agregar**.

    ![Agregar el cuadro de diálogo nuevo elemento][adddb002]

3. Reemplace el contenido del archivo Contacts.cs con el código siguiente.

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
                public string Email { get; set; }
                public string Twitter { get; set; }
                public string Self
                {
                    get { return string.Format(CultureInfo.CurrentCulture,
                         "api/contacts/{0}", this.ContactId); }
                    set { }
                }
            }
        }

La clase **póngase en contacto con** define los datos que se guardará para cada contacto, junto con una clave principal, ID de contacto, que es necesario para la base de datos. Puede obtener más información acerca de los modelos de datos en la sección [Pasos siguientes](#nextsteps) al final de este tutorial.

### <a name="create-web-pages-that-enable-app-users-to-work-with-the-contacts"></a>Crear páginas web que permiten a los usuarios de la aplicación trabajar con los contactos

ASP.NET MVC la característica de scaffolding puede generar automáticamente el código que realiza crear, leer, actualizar y eliminar (CRUD) acciones.

## <a name="add-a-controller-and-a-view-for-the-data"></a>Agregar un controlador y una vista de los datos

1. En el **Explorador de soluciones**, expanda la carpeta de controladores.

3. Genere el proyecto **(Ctrl + Mayús + B)**. (Generar el proyecto antes de utilizar el mecanismo de scaffolding). 

4. Haga clic en la carpeta de controladores y haga clic en **Agregar**y, a continuación, haga clic en el **controlador**.

    ![Agregar controlador en el menú contextual de carpeta de controladores][addcode001]

1. En el cuadro de diálogo **Agregar Scaffold** , seleccione **Controlador MVC con las vistas, con el marco de trabajo de entidad** y haga clic en **Agregar**.

 ![Agregar controlador](./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rrAC.png)

6. Establezca el nombre del controlador en **HomeController**. Seleccione el **contacto** como su clase modelo. Haga clic en el botón **nuevo contexto de datos** y acepte el valor predeterminado de "ContactManager.Models.ContactManagerContext" para el **nuevo tipo de contexto de datos**. Haga clic en **Agregar**.


    Un cuadro de diálogo le pedirá que: "HomeController de un archivo con el nombre ya existe. ¿Desea reemplazarla? ". Haga clic en **Sí**. Nos estamos sobrescribir el controlador de inicio que se creó con el nuevo proyecto. Usaremos el nuevo controlador Home para nuestra lista de contactos.

    Visual Studio crea métodos de controlador y las vistas para operaciones de base de datos CRUD para objetos de **contacto** .

## <a name="enable-migrations-create-the-database-add-sample-data-and-a-data-initializer"></a>Habilitar las migraciones, crear la base de datos, agregar datos de ejemplo y un inicializador de datos ##

La siguiente tarea es habilitar la característica de [Migraciones de primera de código](http://curah.microsoft.com/55220) para crear la base de datos basándose en el modelo de datos que creó.

1. En el menú **Herramientas** , seleccione **Administrador de paquetes de biblioteca** y, a continuación, **Consola del Administrador de paquetes**.

    ![Consola del Administrador de paquete en el menú Herramientas][addcode008]

2. En la ventana de la **Consola del administrador del paquete** , escriba el siguiente comando:

        enable-migrations 
  
    El comando de **migraciones de habilitar** crea una carpeta de *migraciones* y coloca en la carpeta de un archivo *Configuration.cs* que puede modificar para configurar las migraciones. 

2. En la ventana de la **Consola del administrador del paquete** , escriba el siguiente comando:

        add-migration Initial

    El comando **inicial de migración agregar** genera una clase denominada ** &lt;date_stamp&gt;inicial** que crea la base de datos. El primer parámetro ( *inicial* ) es arbitrario y usados para crear el nombre del archivo. Puede ver los nuevos archivos de clase en el **Explorador de soluciones**.

    En la clase **inicial** , el método **de** crea la tabla Contactos y coloca el método **hacia abajo** (se usa cuando desea devolver al estado anterior).

3. Abra el archivo *Migrations\Configuration.cs* . 

4. Agregue los siguientes espacios de nombres. 

         using ContactManager.Models;

5. Reemplace el método de *inicialización* con el siguiente código:
        
        protected override void Seed(ContactManager.Models.ContactManagerContext context)
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
                   Twitter = "debra_example"
               },
                new Contact
                {
                    Name = "Thorsten Weinrich",
                    Address = "5678 1st Ave W",
                    City = "Redmond",
                    State = "WA",
                    Zip = "10999",
                    Email = "thorsten@example.com",
                    Twitter = "thorsten_example"
                },
                new Contact
                {
                    Name = "Yuhong Li",
                    Address = "9012 State st",
                    City = "Redmond",
                    State = "WA",
                    Zip = "10999",
                    Email = "yuhong@example.com",
                    Twitter = "yuhong_example"
                },
                new Contact
                {
                    Name = "Jon Orton",
                    Address = "3456 Maple St",
                    City = "Redmond",
                    State = "WA",
                    Zip = "10999",
                    Email = "jon@example.com",
                    Twitter = "jon_example"
                },
                new Contact
                {
                    Name = "Diliana Alexieva-Bosseva",
                    Address = "7890 2nd Ave E",
                    City = "Redmond",
                    State = "WA",
                    Zip = "10999",
                    Email = "diliana@example.com",
                    Twitter = "diliana_example"
                }
                );
        }

    Este código anterior iniciar la base de datos con la información de contacto. Para obtener más información sobre el inicio de la base de datos, vea [DBs de depuración entidad Framework (EF)](http://blogs.msdn.com/b/rickandy/archive/2013/02/12/seeding-and-debugging-entity-framework-ef-dbs.aspx).


1. En la **Consola del administrador del paquete** , escriba el comando:

        update-database

    ![Comandos de la consola del Administrador de paquetes][addcode009]

    La **actualización de base de datos** se ejecuta la primera migración que crea la base de datos. De forma predeterminada, se crea la base de datos como una base de datos de SQL Server Express LocalDB.

1. Presione CTRL + F5 para ejecutar la aplicación. 

La aplicación muestra los datos de valor de inicialización y proporciona vínculos de edición, detalles y eliminar.

![Vista MVC de datos][rxz3]

## <a name="edit-the-view"></a>Editar la vista

1. Abra el archivo *Views\Home\Index.cshtml* . En el siguiente paso, se reemplazará el marcado generado con el código que usa [jQuery](http://jquery.com/) y [Knockout.js](http://knockoutjs.com/). Este nuevo código recupera la lista de contactos de uso de web API y JSON y, a continuación, enlaza los datos de contacto a la interfaz de usuario mediante knockout.js. Para obtener más información, vea la sección [Pasos siguientes](#nextsteps) al final de este tutorial. 


2. Reemplace el contenido del archivo con el código siguiente.

        @model IEnumerable<ContactManager.Models.Contact>
        @{
            ViewBag.Title = "Home";
        }
        @section Scripts {
            @Scripts.Render("~/bundles/knockout")
            <script type="text/javascript">
                function ContactsViewModel() {
                    var self = this;
                    self.contacts = ko.observableArray([]);
                    self.addContact = function () {
                        $.post("api/contacts",
                            $("#addContact").serialize(),
                            function (value) {
                                self.contacts.push(value);
                            },
                            "json");
                    }
                    self.removeContact = function (contact) {
                        $.ajax({
                            type: "DELETE",
                            url: contact.Self,
                            success: function () {
                                self.contacts.remove(contact);
                            }
                        });
                    }

                    $.getJSON("api/contacts", function (data) {
                        self.contacts(data);
                    });
                }
                ko.applyBindings(new ContactsViewModel());  
        </script>
        }
        <ul id="contacts" data-bind="foreach: contacts">
            <li class="ui-widget-content ui-corner-all">
                <h1 data-bind="text: Name" class="ui-widget-header"></h1>
                <div><span data-bind="text: $data.Address || 'Address?'"></span></div>
                <div>
                    <span data-bind="text: $data.City || 'City?'"></span>,
                    <span data-bind="text: $data.State || 'State?'"></span>
                    <span data-bind="text: $data.Zip || 'Zip?'"></span>
                </div>
                <div data-bind="if: $data.Email"><a data-bind="attr: { href: 'mailto:' + Email }, text: Email"></a></div>
                <div data-bind="ifnot: $data.Email"><span>Email?</span></div>
                <div data-bind="if: $data.Twitter"><a data-bind="attr: { href: 'http://twitter.com/' + Twitter }, text: '@@' + Twitter"></a></div>
                <div data-bind="ifnot: $data.Twitter"><span>Twitter?</span></div>
                <p><a data-bind="attr: { href: Self }, click: $root.removeContact" class="removeContact ui-state-default ui-corner-all">Remove</a></p>
            </li>
        </ul>
        <form id="addContact" data-bind="submit: addContact">
            <fieldset>
                <legend>Add New Contact</legend>
                <ol>
                    <li>
                        <label for="Name">Name</label>
                        <input type="text" name="Name" />
                    </li>
                    <li>
                        <label for="Address">Address</label>
                        <input type="text" name="Address" >
                    </li>
                    <li>
                        <label for="City">City</label>
                        <input type="text" name="City" />
                    </li>
                    <li>
                        <label for="State">State</label>
                        <input type="text" name="State" />
                    </li>
                    <li>
                        <label for="Zip">Zip</label>
                        <input type="text" name="Zip" />
                    </li>
                    <li>
                        <label for="Email">E-mail</label>
                        <input type="text" name="Email" />
                    </li>
                    <li>
                        <label for="Twitter">Twitter</label>
                        <input type="text" name="Twitter" />
                    </li>
                </ol>
                <input type="submit" value="Add" />
            </fieldset>
        </form>

3. Haga clic en la carpeta de contenido, haga clic en **Agregar**y, a continuación, haga clic en **Nuevo elemento...**.

    ![Agregar hoja de estilos en el menú contextual de carpeta de contenido][addcode005]

4. En el cuadro de diálogo **Agregar nuevo elemento** , escriba **estilo** en el cuadro de búsqueda derecha superior y, a continuación, seleccione la **Hoja de estilos**.
    ![Agregar el cuadro de diálogo nuevo elemento][rxStyle]

5. Nombre del archivo *Contacts.css* y haga clic en **Agregar**. Reemplace el contenido del archivo con el código siguiente.
    
        .column {
            float: left;
            width: 50%;
            padding: 0;
            margin: 5px 0;
        }
        form ol {
            list-style-type: none;
            padding: 0;
            margin: 0;
        }
        form li {
            padding: 1px;
            margin: 3px;
        }
        form input[type="text"] {
            width: 100%;
        }
        #addContact {
            width: 300px;
            float: left;
            width:30%;
        }
        #contacts {
            list-style-type: none;
            margin: 0;
            padding: 0;
            float:left;
            width: 70%;
        }
        #contacts li {
            margin: 3px 3px 3px 0;
            padding: 1px;
            float: left;
            width: 300px;
            text-align: center;
            background-image: none;
            background-color: #F5F5F5;
        }
        #contacts li h1
        {
            padding: 0;
            margin: 0;
            background-image: none;
            background-color: Orange;
            color: White;
            font-family: Trebuchet MS, Tahoma, Verdana, Arial, sans-serif;
        }
        .removeContact, .viewImage
        {
            padding: 3px;
            text-decoration: none;
        }

    Usaremos esta hoja de estilos para el diseño, colores y estilos utilizados en la aplicación de administrador de contactos.

6. Abra el archivo *App_Start\BundleConfig.cs* .


7. Agregue el código siguiente para registrar el complemento de [cobertura](http://knockoutjs.com/index.html "KO") .

        bundles.Add(new ScriptBundle("~/bundles/knockout").Include(
                    "~/Scripts/knockout-{version}.js"));
    En este ejemplo con cobertura para simplificar el código de JavaScript dinámico que controla las plantillas de pantalla.

8. Modificar la entrada de contenido o css para registrar la hoja de estilos de *contacts.css* . Cambie la línea siguiente:

                 bundles.Add(new StyleBundle("~/Content/css").Include(
                   "~/Content/bootstrap.css",
                   "~/Content/site.css"));
Para:

        bundles.Add(new StyleBundle("~/Content/css").Include(
                   "~/Content/bootstrap.css",
                   "~/Content/contacts.css",
                   "~/Content/site.css"));

1. En la consola del Administrador de paquetes, ejecute el comando siguiente para instalar cobertura.

        Install-Package knockoutjs

## <a name="add-a-controller-for-the-web-api-restful-interface"></a>Agregar un controlador de la interfaz de Web API REST

1. En el **Explorador de soluciones**, haga clic con el botón controladores y haga clic en **Agregar** y, a continuación, **controlador...** 

1. En el cuadro de diálogo **Agregar Scaffold** , escriba **Web API 2 controlador con acciones, con el marco de trabajo de entidad** y, a continuación, haga clic en **Agregar**.

    ![Agregar controlador de API](./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rt1.png)

4. En el cuadro de diálogo **Agregar controlador** , escriba "ContactsController" como nombre del controlador. Seleccione "Contacto (ContactManager.Models)" de la **clase del modelo**.  Mantenga el valor predeterminado para la **clase de contexto de datos**. 

6. Haga clic en **Agregar**.

### <a name="run-the-application-locally"></a>Ejecute la aplicación localmente

1. Presione CTRL + F5 para ejecutar la aplicación.

    ![Página de índice][intro001]

2. Escriba un contacto y haga clic en **Agregar**. La aplicación se devuelve a la página de inicio y muestra el contacto que haya escrito.

    ![Página de índice con elementos de lista de tareas pendientes][addwebapi004]

3. En el explorador, anexar **/api/contacts** a la dirección URL.

    La dirección URL resultante será similar a http://localhost:1234, api y contactos. La web REST API agregó devuelve los contactos almacenados. Firefox y Chrome mostrará los datos en formato XML.

    ![Página de índice con elementos de lista de tareas pendientes][rxFFchrome]
    

    Internet Explorer le preguntará si desea abrir o guardar los contactos.

    ![Cuadro de diálogo Guardar de Web API][addwebapi006]
    
    
    Puede abrir los contactos devueltos en el Bloc de notas o en un explorador.
    
    Este resultado puede ser utilizado por otra aplicación como página web móvil o una aplicación.

    ![Cuadro de diálogo Guardar de Web API][addwebapi007]

    **Advertencia de seguridad**: en este momento, la aplicación es segura y vulnerable a ataque CSRF. Más adelante en el tutorial se eliminará esta vulnerabilidad. Para obtener más información, consulte [ataques de falsificación de solicitud entre sitios impide (CSRF)][prevent-csrf-attacks].
## <a name="add-xsrf-protection"></a>Agregar protección XSRF

Falsificación de solicitud entre sitios (también conocido como XSRF o CSRF) es un ataque contra aplicaciones hospedadas en web según la cual puede influir en un sitio Web malintencionado la interacción entre un explorador cliente y un sitio Web de confianza para ese explorador. Estos ataques se ha hecho posibles porque los exploradores web enviará tokens de autenticación automáticamente con cada solicitud a un sitio Web. El ejemplo canónico es una cookie de autenticación, como ASP. Vale de autenticación de formularios de red. Sin embargo, estos ataques pueden destino sitios Web que usan cualquier mecanismo de autenticación persistente (por ejemplo, la autenticación de Windows, Basic etc.).

Ataque XSRF es distinto de un ataque de suplantación de identidad. Phishing o requiere interacción de la víctima. En un ataque de suplantación de identidad, un sitio Web malintencionado imita el sitio Web de destino y, a continuación, la víctima es engañada para proporcionar información confidencial como el intruso. En un ataque XSRF, no hay a menudo interacción es necesario de la víctima. En su lugar, el intruso confía en el explorador enviar automáticamente todas las cookies relevantes para el sitio Web de destino.

Para obtener más información, consulte el [Proyecto de seguridad de aplicación Web abierta](https://www.owasp.org/index.php/Main_Page) (OWASP) [XSRF](https://www.owasp.org/index.php/Cross-Site_Request_Forgery_(CSRF)).

1. En el **Explorador de soluciones**, derecha **ContactManager** project y haga clic en **Agregar** y, a continuación, haga clic en **clase**.

2. Nombre del archivo *ValidateHttpAntiForgeryTokenAttribute.cs* y agregue el siguiente código:

        using System;
        using System.Collections.Generic;
        using System.Linq;
        using System.Net;
        using System.Net.Http;
        using System.Web.Helpers;
        using System.Web.Http.Controllers;
        using System.Web.Http.Filters;
        using System.Web.Mvc;
        namespace ContactManager.Filters
        {
            public class ValidateHttpAntiForgeryTokenAttribute : AuthorizationFilterAttribute
            {
                public override void OnAuthorization(HttpActionContext actionContext)
                {
                    HttpRequestMessage request = actionContext.ControllerContext.Request;
                    try
                    {
                        if (IsAjaxRequest(request))
                        {
                            ValidateRequestHeader(request);
                        }
                        else
                        {
                            AntiForgery.Validate();
                        }
                    }
                    catch (HttpAntiForgeryException e)
                    {
                        actionContext.Response = request.CreateErrorResponse(HttpStatusCode.Forbidden, e);
                    }
                }
                private bool IsAjaxRequest(HttpRequestMessage request)
                {
                    IEnumerable<string> xRequestedWithHeaders;
                    if (request.Headers.TryGetValues("X-Requested-With", out xRequestedWithHeaders))
                    {
                        string headerValue = xRequestedWithHeaders.FirstOrDefault();
                        if (!String.IsNullOrEmpty(headerValue))
                        {
                            return String.Equals(headerValue, "XMLHttpRequest", StringComparison.OrdinalIgnoreCase);
                        }
                    }
                    return false;
                }
                private void ValidateRequestHeader(HttpRequestMessage request)
                {
                    string cookieToken = String.Empty;
                    string formToken = String.Empty;
                    IEnumerable<string> tokenHeaders;
                    if (request.Headers.TryGetValues("RequestVerificationToken", out tokenHeaders))
                    {
                        string tokenValue = tokenHeaders.FirstOrDefault();
                        if (!String.IsNullOrEmpty(tokenValue))
                        {
                            string[] tokens = tokenValue.Split(':');
                            if (tokens.Length == 2)
                            {
                                cookieToken = tokens[0].Trim();
                                formToken = tokens[1].Trim();
                            }
                        }
                    }
                    AntiForgery.Validate(cookieToken, formToken);
                }
            }
        }

1. Agregue la siguiente instrucción de *uso* en el controlador de contratos para tener acceso al atributo **[ValidateHttpAntiForgeryToken]** .

        using ContactManager.Filters;

1. Agregue el atributo **[ValidateHttpAntiForgeryToken]** a los métodos de entrada de la **ContactsController** proteger de amenazas XSRF. Se agregará a los métodos de acción "PutContact", "PostContact" y **DeleteContact** .

        [ValidateHttpAntiForgeryToken]
            public IHttpActionResult PutContact(int id, Contact contact)
            {

1. Actualice la sección de *secuencias de comandos* del archivo *Views\Home\Index.cshtml* para incluir el código para obtener los tokens XSRF.

         @section Scripts {
            @Scripts.Render("~/bundles/knockout")
            <script type="text/javascript">
                @functions{
                   public string TokenHeaderValue()
                   {
                      string cookieToken, formToken;
                      AntiForgery.GetTokens(null, out cookieToken, out formToken);
                      return cookieToken + ":" + formToken;                
                   }
                }

               function ContactsViewModel() {
                  var self = this;
                  self.contacts = ko.observableArray([]);
                  self.addContact = function () {

                     $.ajax({
                        type: "post",
                        url: "api/contacts",
                        data: $("#addContact").serialize(),
                        dataType: "json",
                        success: function (value) {
                           self.contacts.push(value);
                        },
                        headers: {
                           'RequestVerificationToken': '@TokenHeaderValue()'
                        }
                     });

                  }
                  self.removeContact = function (contact) {
                     $.ajax({
                        type: "DELETE",
                        url: contact.Self,
                        success: function () {
                           self.contacts.remove(contact);
                        },
                        headers: {
                           'RequestVerificationToken': '@TokenHeaderValue()'
                        }

                     });
                  }

                  $.getJSON("api/contacts", function (data) {
                     self.contacts(data);
                  });
               }
               ko.applyBindings(new ContactsViewModel());
            </script>
         }


## <a name="publish-the-application-update-to-azure-and-sql-database"></a>Publicar la actualización de la aplicación en Azure y base de datos SQL

Para publicar la aplicación, repita el procedimiento que anterior ha seguido.

1. En el **Explorador de soluciones**, haga clic con el botón secundario del mouse en el proyecto y seleccione **Publicar**.

    ![Publicar][rxP]

5. Haga clic en la pestaña **configuración** .
    

1. En **ContactsManagerContext(ContactsManagerContext)**, haga clic en el icono de **v** para cambiar la *cadena de conexión remota* a la cadena de conexión para la base de datos de contacto. Haga clic en **ContactDB**.

    ![Configuración](./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rt5.png)

7. Active la casilla **ejecutar código primero**las migraciones (se ejecuta en el inicio de la aplicación).

1. Haga clic en **siguiente** y, a continuación, haga clic en **vista previa**. Visual Studio muestra una lista de los archivos que se agregado o actualizado.

8. Haga clic en **Publicar**.
Una vez finalizada la implementación, se abre el explorador a la página principal de la aplicación.

    ![Página de índice con ningún contacto][intro001]

    Visual Studio publicar proceso automáticamente configura la cadena de conexión en el archivo *Web.config* implementado para que apunten a la base de datos SQL. También configurar primera migraciones de código para actualizar automáticamente la base de datos a la versión más reciente la primera vez que la aplicación tiene acceso a la base de datos después de la implementación.

    Como resultado de esta configuración, Code First crea la base de datos ejecutando el código de la clase **inicial** que creó anteriormente. Hizo esto la primera vez que la aplicación intentó obtener acceso a la base de datos después de la implementación.

9. Escriba un contacto como hizo al ejecutar la aplicación localmente, para comprobar que la implementación de la base de datos se realizó correctamente.

Cuando vea que el elemento que escriba se guarda y aparece en la página Administrador de contactos, sabrá que se han almacenado en la base de datos.

![Página de índice de contactos][addwebapi004]

La aplicación se está ejecutando ahora en la nube, con la base de datos SQL para almacenar sus datos. Cuando termine de probar la aplicación en Azure, elimínelo. La aplicación es pública y no tiene un mecanismo para limitar el acceso.

>[AZURE.NOTE] Si desea empezar a trabajar con el servicio de aplicación de Azure antes de suscribirse para una cuenta de Azure, vaya a [Probar el servicio de aplicaciones](http://go.microsoft.com/fwlink/?LinkId=523751), donde puede crear inmediatamente una aplicación web de starter corta duración en la aplicación de servicio. Sin tarjetas de crédito obligatoria; sin compromisos.

## <a name="next-steps"></a>Pasos siguientes

Una aplicación real se requeriría autenticación y la autorización y utilizará la base de datos de la pertenencia a tal fin. El tutorial de [implementar una aplicación de MVC de ASP.NET seguro con OAuth, la suscripción y la base de datos de SQL](web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database.md) se basa en este tutorial y muestra cómo implementar una aplicación web con la base de datos de pertenencia.

Otra forma de almacenar los datos en una aplicación de Azure es usar el almacenamiento de Azure, que proporciona almacenamiento de datos relacionales de en el formulario de BLOB y tablas. Los siguientes vínculos proporcionan más información en Web API, ASP.NET MVC y ventana de Azure.
 

* [Introducción a Framework entidad con MVC][EFCodeFirstMVCTutorial]
* [Introducción a ASP.NET MVC 5](http://www.asp.net/mvc/tutorials/mvc-5/introduction/getting-started)
* [El primer Web de ASP.NET API](http://www.asp.net/web-api/overview/getting-started-with-aspnet-web-api/tutorial-your-first-web-api)
* [Depuración WAWS](web-sites-dotnet-troubleshoot-visual-studio.md)

En este tutorial y la aplicación de ejemplo escrita por [Anderson enriquecido](http://blogs.msdn.com/b/rickandy/) (Twitter [@RickAndMSFT](https://twitter.com/RickAndMSFT)) con la asistencia de Tom Dykstra y Barry Dorrans (Twitter [@blowdart](https://twitter.com/blowdart)). 

Por favor, votar en qué le gustó o lo que le gustaría ver mejorado, no solo sobre el tutorial propio, sino también acerca de los productos que se muestran. Sus comentarios nos ayudarán a prioridades mejoras. Estamos especialmente interesados en averiguar cuánto interés que hay en la automatización más para el proceso de configuración e implementación de la base de datos de pertenencia. 

## <a name="whats-changed"></a>¿Qué ha cambiado
* Encontrará una guía para el cambio de sitios Web a la aplicación de servicio: [servicio de aplicación de Azure y su influencia sobre los servicios de Azure existentes](http://go.microsoft.com/fwlink/?LinkId=529714)

<!-- bookmarks -->
[Add an OAuth Provider]: #addOauth
[Add Roles to the Membership Database]:#mbrDB
[Create a Data Deployment Script]:#ppd
[Update the Membership Database]:#ppd2
[setupdbenv]: #bkmk_setupdevenv
[setupwindowsazureenv]: #bkmk_setupwindowsazure
[createapplication]: #bkmk_createmvc4app
[deployapp1]: #bkmk_deploytowindowsazure1
[adddb]: #bkmk_addadatabase
[addcontroller]: #bkmk_addcontroller
[addwebapi]: #bkmk_addwebapi
[deploy2]: #bkmk_deploydatabaseupdate

<!-- links -->
[EFCodeFirstMVCTutorial]: http://www.asp.net/mvc/tutorials/getting-started-with-ef-using-mvc/creating-an-entity-framework-data-model-for-an-asp-net-mvc-application
[dbcontext-link]: http://msdn.microsoft.com/library/system.data.entity.dbcontext(v=VS.103).aspx


<!-- images-->
[rxE]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rxE.png
[rxP]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rxP.png
[rx22]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/
[rxb2]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rxb2.png
[rxz]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rxz.png
[rxzz]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rxzz.png
[rxz2]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rxz2.png
[rxz3]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rxz3.png
[rxStyle]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rxStyle.png
[rxz4]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rxz4.png
[rxz44]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rxz44.png
[rxNewCtx]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rxNewCtx.png
[rxPrevDB]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rxPrevDB.png
[rxOverwrite]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rxOverwrite.png
[rxPWS]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rxPWS.png
[rxNewCtx]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rxNewCtx.png
[rxAddApiController]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rxAddApiController.png
[rxFFchrome]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rxFFchrome.png
[intro001]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/dntutmobil-intro-finished-web-app.png
[rxCreateWSwithDB]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rxCreateWSwithDB.png
[setup007]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/dntutmobile-setup-azure-site-004.png
[setup009]: ../Media/dntutmobile-setup-azure-site-006.png
[newapp002]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/dntutmobile-createapp-002.png
[newapp004]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/dntutmobile-createapp-004.png
[firsdeploy007]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/dntutmobile-deploy1-publish-005.png
[firsdeploy009]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/dntutmobile-deploy1-publish-007.png
[adddb001]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/dntutmobile-adddatabase-001.png
[adddb002]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/dntutmobile-adddatabase-002.png
[addcode001]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/dntutmobile-controller-add-context-menu.png
[addcode002]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/dntutmobile-controller-add-controller-dialog.png
[addcode004]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/dntutmobile-controller-modify-index-context.png
[addcode005]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/dntutmobile-controller-add-contents-context-menu.png
[addcode007]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/dntutmobile-controller-modify-bundleconfig-context.png
[addcode008]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/dntutmobile-migrations-package-manager-menu.png
[addcode009]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/dntutmobile-migrations-package-manager-console.png
[addwebapi004]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/dntutmobile-webapi-added-contact.png
[addwebapi006]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/dntutmobile-webapi-save-returned-contacts.png
[addwebapi007]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/dntutmobile-webapi-contacts-in-notepad.png
[Add XSRF Protection]: #xsrf
[WebPIAzureSdk20NetVS12]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/WebPIAzureSdk20NetVS12.png
[Add XSRF Protection]: #xsrf
[ImportPublishSettings]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/ImportPublishSettings.png
[ImportPublishProfile]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/ImportPublishProfile.png
[PublishVSSolution]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/PublishVSSolution.png
[ValidateConnection]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/ValidateConnection.png
[WebPIAzureSdk20NetVS12]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/WebPIAzureSdk20NetVS12.png
[prevent-csrf-attacks]: http://www.asp.net/web-api/overview/security/preventing-cross-site-request-forgery-(csrf)-attacks
 
