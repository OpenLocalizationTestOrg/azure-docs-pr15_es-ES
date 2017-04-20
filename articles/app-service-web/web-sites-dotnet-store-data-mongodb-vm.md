<properties 
    pageTitle="Crear una aplicación web en Azure que se conecta a MongoDB que se ejecuta en una máquina virtual" 
    description="Un tutorial que le enseña a usar Git para implementar una aplicación ASP.NET al servicio de la aplicación de Azure, conectada a MongoDB en una máquina Virtual de Azure."
    tags="azure-portal" 
    services="app-service\web, virtual-machines" 
    documentationCenter=".net" 
    authors="cephalin" 
    manager="wpickett" 
    editor=""/>

<tags 
    ms.service="app-service-web" 
    ms.workload="web" 
    ms.tgt_pltfrm="na" 
    ms.devlang="dotnet" 
    ms.topic="article" 
    ms.date="02/29/2016" 
    ms.author="cephalin"/>


# <a name="create-a-web-app-in-azure-that-connects-to-mongodb-running-on-a-virtual-machine"></a>Crear una aplicación web en Azure que se conecta a MongoDB que se ejecuta en una máquina virtual

Usa Git, puede implementar una aplicación de ASP.NET en aplicaciones de Azure aplicación de servicio Web. En este tutorial, creará un simple ASP.NET MVC front-end aplicación de la lista de tareas que se conecta a una base de datos de MongoDB que se ejecuta en una máquina virtual en Azure.  [MongoDB] [ MongoDB] es una base de datos de alto rendimiento NoSQL popular Abrir origen. Después de ejecutar y probar la aplicación ASP.NET en el equipo de desarrollo, cargará la aplicación de la aplicación de servicio Web Apps con Git.

>[AZURE.NOTE] Si desea empezar a trabajar con el servicio de aplicación de Azure antes de suscribirse para una cuenta de Azure, vaya a [Probar el servicio de aplicaciones](http://go.microsoft.com/fwlink/?LinkId=523751), donde puede crear inmediatamente una aplicación web de starter corta duración en la aplicación de servicio. Sin tarjetas de crédito obligatoria; sin compromisos.


## <a name="background-knowledge"></a>Conocimiento ##

Conocimiento de las siguientes acciones es útil para este tutorial, aunque no es necesario:

* El controlador de C# para MongoDB. Para obtener más información sobre cómo desarrollar aplicaciones C# contra MongoDB, vea el MongoDB [CSharp idioma centro][MongoC#LangCenter]. 
* El marco de aplicación web de ASP.NET. Puede obtener toda la información necesaria en el [sitio Web de ASP.net][ASP.NET].
* El marco de aplicación web de ASP .NET MVC. Puede obtener toda la información necesaria en el [sitio Web de ASP.NET MVC][MVCWebSite].
* Azure. Puede empezar a leer en [Azure][WindowsAzure].

## <a name="prerequisites"></a>Requisitos previos ##

- [Visual Studio Express 2013 para Web]  [ VSEWeb] o [2013 de Visual Studio] [VSUlt]
- [Azure SDK para .NET](http://go.microsoft.com/fwlink/p/?linkid=323510&clcid=0x409)
- Una suscripción activa de Microsoft Azure

[AZURE.INCLUDE [create-account-and-websites-note](../../includes/create-account-and-websites-note.md)]

<a id="virtualmachine"></a> 
## <a name="create-a-virtual-machine-and-install-mongodb"></a>Crear una máquina virtual e instale MongoDB ##

En este tutorial se supone que haya creado una máquina virtual en Azure. Después de crear la máquina virtual debe instalar MongoDB en la máquina virtual:

* Para crear una máquina virtual de Windows e instalar MongoDB, vea [Instalar MongoDB en una máquina virtual ejecuta Windows Server en Azure][InstallMongoOnWindowsVM].


Una vez que ha creado la máquina virtual en Azure e instalado MongoDB, asegúrese de recordar el nombre DNS de la máquina virtual (por ejemplo, "testlinuxvm.cloudapp.net") y el puerto externo MongoDB que especificó en el extremo.  Necesitará esta información más adelante en el tutorial.

<a id="createapp"></a>
## <a name="create-the-application"></a>Creación de la aplicación ##

En esta sección creará una aplicación de ASP.NET denominada "Mi lista de tareas" mediante Visual Studio y realizar una implementación inicial para las aplicaciones de Azure aplicación de servicio Web. Se ejecutará la aplicación de forma local, pero se conecte a su equipo virtual en Azure y utilizará la instancia de MongoDB que ha creado allí.

1. En Visual Studio, haga clic en **Nuevo proyecto**.

    ![Nuevo proyecto de la página de inicio][StartPageNewProject]

1. En la ventana **Nuevo proyecto** , en el panel izquierdo, seleccione **Visual C#**y, a continuación, seleccione **Web**. En el panel central, seleccione la **Aplicación Web de ASP.NET**. En la parte inferior, asigne un nombre a su proyecto "MyTaskListApp" y, a continuación, haga clic en **Aceptar**.

    ![Cuadro de diálogo nuevo proyecto][NewProjectMyTaskListApp]

1. En el cuadro de diálogo **Nuevo proyecto** , seleccione **MVC**y, a continuación, haga clic en **Aceptar**.

    ![Seleccionar plantilla MVC][VS2013SelectMVCTemplate]

1. Si ya no haya iniciado sesión en Microsoft Azure, se le pedirá que iniciar sesión. Siga las indicaciones para iniciar sesión en Azure.
2. Una vez que ha iniciado sesión, puede comenzar a configurar la aplicación de servicio de aplicación web. Especifique el **nombre de Web App**, el **plan de servicios de aplicación**, el **grupo de recursos**y la **región**y luego haga clic en **crear**.

    ![](./media/web-sites-dotnet-store-data-mongodb-vm/VSConfigureWebAppSettings.png)

1. Después de que el proyecto finalice la creación, espere a que la aplicación web que se creen en el servicio de aplicación de Azure tal como se indica en la ventana de **Actividad de servicio de aplicación de Azure** . A continuación, haga clic en **Publicar MyTaskListApp para esta aplicación Web ahora**.

1. Haga clic en **Publicar**.

    ![](./media/web-sites-dotnet-store-data-mongodb-vm/VSPublishWeb.png)

    Una vez que la aplicación de ASP.NET predeterminada se publica en Azure aplicación de servicio Web Apps, se iniciará en el explorador.

## <a name="install-the-mongodb-c-driver"></a>Instalar al controlador de MongoDB C#

MongoDB ofrece soporte de cliente para las aplicaciones C# a través de un controlador, que debe instalar en el equipo de desarrollo local. El controlador de C# está disponible a través de NuGet.

Para instalar al controlador de MongoDB C#:

1. En el **Explorador de soluciones**, haga clic en el proyecto **MyTaskListApp** y seleccione **Administrar NuGetPackages**.

    ![Administrar paquetes de NuGet][VS2013ManageNuGetPackages]

2. En la ventana **Administrar paquetes de NuGet** , en el panel izquierdo, haga clic en **línea**. En el cuadro de **Búsqueda en línea** a la derecha, escriba "mongodb.driver".  Haga clic en **instalar** para instalar al controlador.

    ![Buscar MongoDB C# controlador][SearchforMongoDBCSharpDriver]

3. Haga clic en **** para aceptar la 10gen, términos de licencia Inc..

4. Haga clic en **Cerrar** una vez haya instalado el controlador.
    ![MongoDB C# controlador instalado][MongoDBCsharpDriverInstalled]


Ahora está instalado el controlador de MongoDB C#.  Las referencias a las bibliotecas **MongoDB.Bson**, **MongoDB.Driver**y **MongoDB.Driver.Core** se agregaron al proyecto.

![Referencias de MongoDB C# controlador][MongoDBCSharpDriverReferences]

## <a name="add-a-model"></a>Agregar un modelo ##
En el **Explorador de soluciones**, haga clic en la carpeta de *modelos* y **Agregar** una nueva **clase** y asígnele el nombre *TaskModel.cs*.  En *TaskModel.cs*, reemplace el código existente con el siguiente código:

    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Web;
    using MongoDB.Bson.Serialization.Attributes;
    using MongoDB.Bson.Serialization.IdGenerators;
    using MongoDB.Bson;
    
    namespace MyTaskListApp.Models
    {
        public class MyTask
        {
            [BsonId(IdGenerator = typeof(CombGuidGenerator))]
            public Guid Id { get; set; }
    
            [BsonElement("Name")]
            public string Name { get; set; }
    
            [BsonElement("Category")]
            public string Category { get; set; }
    
            [BsonElement("Date")]
            public DateTime Date { get; set; }
    
            [BsonElement("CreatedDate")]
            public DateTime CreatedDate { get; set; }
    
        }
    }

## <a name="add-the-data-access-layer"></a>Agregar la capa de acceso a datos ##
En el **Explorador de soluciones**, haga clic en el proyecto *MyTaskListApp* y **Agregar** una **Nueva carpeta** denominada *DAL*.  Haga clic en la carpeta *DAL* y **Agregar** una nueva **clase**. Nombre del archivo de clase *Dal.cs*.  En *Dal.cs*, reemplace el código existente con el siguiente código:

    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Web;
    using MyTaskListApp.Models;
    using MongoDB.Driver;
    using MongoDB.Bson;
    using System.Configuration;
    
    
    namespace MyTaskListApp
    {
        public class Dal : IDisposable
        {
            private MongoServer mongoServer = null;
            private bool disposed = false;
    
            // To do: update the connection string with the DNS name
            // or IP address of your server. 
            //For example, "mongodb://testlinux.cloudapp.net"
            private string connectionString = "mongodb://mongodbsrv20151211.cloudapp.net";
    
            // This sample uses a database named "Tasks" and a 
            //collection named "TasksList".  The database and collection 
            //will be automatically created if they don't already exist.
            private string dbName = "Tasks";
            private string collectionName = "TasksList";
    
            // Default constructor.        
            public Dal()
            {
            }
    
            // Gets all Task items from the MongoDB server.        
            public List<MyTask> GetAllTasks()
            {
                try
                {
                    var collection = GetTasksCollection();
                    return collection.Find(new BsonDocument()).ToList();
                }
                catch (MongoConnectionException)
                {
                    return new List<MyTask>();
                }
            }
    
            // Creates a Task and inserts it into the collection in MongoDB.
            public void CreateTask(MyTask task)
            {
                var collection = GetTasksCollectionForEdit();
                try
                {
                    collection.InsertOne(task);
                }
                catch (MongoCommandException ex)
                {
                    string msg = ex.Message;
                }
            }
    
            private IMongoCollection<MyTask> GetTasksCollection()
            {
                MongoClient client = new MongoClient(connectionString);
                var database = client.GetDatabase(dbName);
                var todoTaskCollection = database.GetCollection<MyTask>(collectionName);
                return todoTaskCollection;
            }
    
            private IMongoCollection<MyTask> GetTasksCollectionForEdit()
            {
                MongoClient client = new MongoClient(connectionString);
                var database = client.GetDatabase(dbName);
                var todoTaskCollection = database.GetCollection<MyTask>(collectionName);
                return todoTaskCollection;
            }
    
            # region IDisposable
    
            public void Dispose()
            {
                this.Dispose(true);
                GC.SuppressFinalize(this);
            }
    
            protected virtual void Dispose(bool disposing)
            {
                if (!this.disposed)
                {
                    if (disposing)
                    {
                        if (mongoServer != null)
                        {
                            this.mongoServer.Disconnect();
                        }
                    }
                }
    
                this.disposed = true;
            }
    
            # endregion
        }
    }

## <a name="add-a-controller"></a>Agregar un controlador ##
Abra el archivo de *Controllers\HomeController.cs* en el **Explorador de soluciones** y reemplace el código existente con lo siguiente:

    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Web;
    using System.Web.Mvc;
    using MyTaskListApp.Models;
    using System.Configuration;
    
    namespace MyTaskListApp.Controllers
    {
        public class HomeController : Controller, IDisposable
        {
            private Dal dal = new Dal();
            private bool disposed = false;
            //
            // GET: /MyTask/
    
            public ActionResult Index()
            {
                return View(dal.GetAllTasks());
            }
    
            //
            // GET: /MyTask/Create
    
            public ActionResult Create()
            {
                return View();
            }
    
            //
            // POST: /MyTask/Create
    
            [HttpPost]
            public ActionResult Create(MyTask task)
            {
                try
                {
                    dal.CreateTask(task);
                    return RedirectToAction("Index");
                }
                catch
                {
                    return View();
                }
            }
    
            public ActionResult About()
            {
                return View();
            }
    
            # region IDisposable
    
            new protected void Dispose()
            {
                this.Dispose(true);
                GC.SuppressFinalize(this);
            }
    
            new protected virtual void Dispose(bool disposing)
            {
                if (!this.disposed)
                {
                    if (disposing)
                    {
                        this.dal.Dispose();
                    }
                }
    
                this.disposed = true;
            }
    
            # endregion
    
        }
    }

## <a name="set-up-the-styles"></a>Configurar los estilos ##
Para cambiar el título en la parte superior de la página, abra la *Views\Shared\\_Layout.cshtml* de archivo en el **Explorador de soluciones** y reemplace "Nombre de la aplicación" en el encabezado de la barra de exploración con "Mi aplicación de lista de tareas" para que el siguiente aspecto:

    @Html.ActionLink("My Task List Application", "Index", "Home", null, new { @class = "navbar-brand" })

Para configurar el menú de lista de tareas, abra el archivo *\Views\Home\Index.cshtml* y reemplace el código existente con el siguiente código:
    
    @model IEnumerable<MyTaskListApp.Models.MyTask>
    
    @{
        ViewBag.Title = "My Task List";
    }
    
    <h2>My Task List</h2>
    
    <table border="1">
        <tr>
            <th>Task</th>
            <th>Category</th>
            <th>Date</th>
            
        </tr>
    
    @foreach (var item in Model) {
        <tr>
            <td>
                @Html.DisplayFor(modelItem => item.Name)
            </td>
            <td>
                @Html.DisplayFor(modelItem => item.Category)
            </td>
            <td>
                @Html.DisplayFor(modelItem => item.Date)
            </td>
            
        </tr>
    }
    
    </table>
    <div>  @Html.Partial("Create", new MyTaskListApp.Models.MyTask())</div>


Para agregar la capacidad para crear una nueva tarea, haga clic en el *Views\Home\\ * carpeta y **Agregar** una **vista**.  Nombre de la vista de *crear*. Reemplace el código con lo siguiente:

    @model MyTaskListApp.Models.MyTask
    
    <script src="@Url.Content("~/Scripts/jquery-1.10.2.min.js")" type="text/javascript"></script>
    <script src="@Url.Content("~/Scripts/jquery.validate.min.js")" type="text/javascript"></script>
    <script src="@Url.Content("~/Scripts/jquery.validate.unobtrusive.min.js")" type="text/javascript"></script>
    
    @using (Html.BeginForm("Create", "Home")) {
        @Html.ValidationSummary(true)
        <fieldset>
            <legend>New Task</legend>
    
            <div class="editor-label">
                @Html.LabelFor(model => model.Name)
            </div>
            <div class="editor-field">
                @Html.EditorFor(model => model.Name)
                @Html.ValidationMessageFor(model => model.Name)
            </div>
    
            <div class="editor-label">
                @Html.LabelFor(model => model.Category)
            </div>
            <div class="editor-field">
                @Html.EditorFor(model => model.Category)
                @Html.ValidationMessageFor(model => model.Category)
            </div>
    
            <div class="editor-label">
                @Html.LabelFor(model => model.Date)
            </div>
            <div class="editor-field">
                @Html.EditorFor(model => model.Date)
                @Html.ValidationMessageFor(model => model.Date)
            </div>
    
            <p>
                <input type="submit" value="Create" />
            </p>
        </fieldset>
    }

**Explorador de soluciones** debería tener este aspecto:

![Explorador de soluciones][SolutionExplorerMyTaskListApp]

## <a name="set-the-mongodb-connection-string"></a>Establecer la cadena de conexión de MongoDB ##
En el **Explorador de soluciones**, abra el archivo *DAL/Dal.cs* . Buscar la siguiente línea de código:

    private string connectionString = "mongodb://<vm-dns-name>";

Reemplazar `<vm-dns-name>` con el nombre DNS de la máquina virtual ejecuta MongoDB que creó en el paso de [crear una máquina virtual e instale MongoDB][] de este tutorial.  Para buscar el nombre DNS de la máquina virtual, vaya al Portal de Azure, seleccione **máquinas virtuales**y buscar **El nombre de DNS**.

Si el nombre DNS de la máquina virtual es "testlinuxvm.cloudapp.net" y MongoDB está escuchando en el puerto predeterminado 27017, tendrá un aspecto similar a la línea de la cadena de conexión de código:

    private string connectionString = "mongodb://testlinuxvm.cloudapp.net";

Si el extremo de la máquina virtual especifica un puerto externo diferente para MongoDB, puede especificar el puerto en la cadena de conexión:

    private string connectionString = "mongodb://testlinuxvm.cloudapp.net:12345";

Para obtener más información sobre las cadenas de conexión de MongoDB, vea [conexiones][MongoConnectionStrings].

## <a name="test-the-local-deployment"></a>Probar la implementación local ##

Para ejecutar la aplicación en el equipo de desarrollo, seleccione **Iniciar depurar** en el menú **Depurar** o presione **F5**. IIS Express se inicia y un explorador se abre e inicia la página de inicio de la aplicación.  Puede agregar una nueva tarea, que se agregará a la base de datos MongoDB en su máquina virtual en Azure.

![Mi aplicación de la lista de tareas][TaskListAppBlank]

## <a name="publish-to-azure-app-service-web-apps"></a>Publicar en Azure de aplicación de servicio Web Apps

En esta sección se publicar los cambios en aplicaciones de Azure aplicación de servicio Web.

1. En el Explorador de soluciones, haga clic de nuevo en **MyTaskListApp** y haga clic en **Publicar**.
2. Haga clic en **Publicar**.

    Ahora debe ver la aplicación web de ejecución en el servicio de aplicación de Azure y obtener acceso a la base de datos MongoDB en Azure máquinas virtuales de Windows.

## <a name="summary"></a>Resumen ##

Ahora se ha implementado correctamente la aplicación de ASP.NET para las aplicaciones de Azure aplicación de servicio Web. Para ver la aplicación web:

1. Inicie sesión en el Portal de Azure.
2. Haga clic en **aplicaciones Web**. 
3. Seleccione la aplicación web en la lista de **Aplicaciones Web** .

Para obtener más información sobre cómo desarrollar aplicaciones C# contra MongoDB, consulte [Centro de idioma de CSharp][MongoC#LangCenter]. 

[AZURE.INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]
 

<!-- HYPERLINKS -->

[AzurePortal]: http://manage.windowsazure.com
[WindowsAzure]: http://www.windowsazure.com
[MongoC#LangCenter]: http://docs.mongodb.org/ecosystem/drivers/csharp/
[MVCWebSite]: http://www.asp.net/mvc
[ASP.NET]: http://www.asp.net/
[MongoConnectionStrings]: http://www.mongodb.org/display/DOCS/Connections
[MongoDB]: http://www.mongodb.org
[InstallMongoOnWindowsVM]: ../virtual-machines/virtual-machines-windows-classic-install-mongodb.md
[VSEWeb]: http://www.microsoft.com/visualstudio/eng/2013-downloads#d-2013-express
[VSUlt]: http://www.microsoft.com/visualstudio/eng/2013-downloads

<!-- IMAGES -->


[StartPageNewProject]: ./media/web-sites-dotnet-store-data-mongodb-vm/NewProject.png
[NewProjectMyTaskListApp]: ./media/web-sites-dotnet-store-data-mongodb-vm/NewProjectMyTaskListApp.png
[VS2013SelectMVCTemplate]: ./media/web-sites-dotnet-store-data-mongodb-vm/VS2013SelectMVCTemplate.png
[VS2013DefaultMVCApplication]: ./media/web-sites-dotnet-store-data-mongodb-vm/VS2013DefaultMVCApplication.png
[VS2013ManageNuGetPackages]: ./media/web-sites-dotnet-store-data-mongodb-vm/VS2013ManageNuGetPackages.png
[SearchforMongoDBCSharpDriver]: ./media/web-sites-dotnet-store-data-mongodb-vm/SearchforMongoDBCSharpDriver.png
[MongoDBCsharpDriverInstalled]: ./media/web-sites-dotnet-store-data-mongodb-vm/MongoDBCsharpDriverInstalled.png
[MongoDBCSharpDriverReferences]: ./media/web-sites-dotnet-store-data-mongodb-vm/MongoDBCSharpDriverReferences.png
[SolutionExplorerMyTaskListApp]: ./media/web-sites-dotnet-store-data-mongodb-vm/SolutionExplorerMyTaskListApp.png
[TaskListAppBlank]: ./media/web-sites-dotnet-store-data-mongodb-vm/TaskListAppBlank.png
[WAWSCreateWebSite]: ./media/web-sites-dotnet-store-data-mongodb-vm/WAWSCreateWebSite.png
[WAWSDashboardMyTaskListApp]: ./media/web-sites-dotnet-store-data-mongodb-vm/WAWSDashboardMyTaskListApp.png
[Image9]: ./media/web-sites-dotnet-store-data-mongodb-vm/RepoReady.png
[Image10]: ./media/web-sites-dotnet-store-data-mongodb-vm/GitInstructions.png
[Image11]: ./media/web-sites-dotnet-store-data-mongodb-vm/GitDeploymentComplete.png

<!-- TOC BOOKMARKS -->
[Crear una máquina virtual e instale MongoDB]: #virtualmachine
[Create and run the My Task List ASP.NET application on your development computer]: #createapp
[Create an Azure web site]: #createwebsite
[Deploy the ASP.NET application to the web site using Git]: #deployapp
 