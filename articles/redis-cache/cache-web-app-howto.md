<properties 
    pageTitle="Cómo crear una aplicación Web con caché Redis | Microsoft Azure" 
    description="Obtenga información sobre cómo crear una aplicación Web con Redis caché" 
    services="redis-cache" 
    documentationCenter="" 
    authors="steved0x" 
    manager="douge" 
    editor=""/>

<tags 
    ms.service="cache" 
    ms.workload="tbd" 
    ms.tgt_pltfrm="cache-redis" 
    ms.devlang="na" 
    ms.topic="hero-article" 
    ms.date="10/11/2016" 
    ms.author="sdanie"/>

# <a name="how-to-create-a-web-app-with-redis-cache"></a>Cómo crear una aplicación Web con Redis caché

> [AZURE.SELECTOR]
- [.NET](cache-dotnet-how-to-use-azure-redis-cache.md)
- [ASP.NET](cache-web-app-howto.md)
- [Node.js](cache-nodejs-get-started.md)
- [Java](cache-java-get-started.md)
- [Python](cache-python-get-started.md)

Este tutorial muestra cómo crear e implementar una aplicación web ASP.NET a una aplicación web en la aplicación de servicio de Azure mediante Visual Studio de 2015. La aplicación de ejemplo muestra una lista de estadísticas de grupo desde una base de datos y muestra diferentes maneras de utilizar Azure Redis caché para almacenar y recuperar datos de la memoria caché. Cuando haya completado el tutorial tendrá una aplicación web de ejecución que lee y escribe en una base de datos, optimizada con Azure Redis caché y alojado en Azure.

Aprenderá:

-   Cómo crear una aplicación web de ASP.NET MVC 5 en Visual Studio.
-   Cómo obtener acceso a datos desde una base de datos con el marco de trabajo de entidad.
-   Cómo mejorar el rendimiento y reducir la carga de la base de datos, almacenar y recuperar datos con Azure Redis caché.
-   Cómo usar un Redis ordenados conjunto para recuperar los equipos de 5 superiores.
-   Cómo aprovisionar los recursos Azure para la aplicación con una plantilla de administrador de recursos.
-   Cómo publicar la aplicación en Azure con Visual Studio.

## <a name="prerequisites"></a>Requisitos previos

Para completar el tutorial, debe tener los siguientes requisitos previos.

-   [Cuenta de Azure](#azure-account)
-   [2015 de Visual Studio con el SDK de Azure para .NET](#visual-studio-2015-with-the-azure-sdk-for-net)

### <a name="azure-account"></a>Cuenta de Azure

Necesita una cuenta de Azure para completar el tutorial. Puedes:

* [Abrir una cuenta de Azure de forma gratuita](/pricing/free-trial/?WT.mc_id=redis_cache_hero). Obtenga créditos que se pueden usar para probar los servicios de Azure pagados. Incluso después de que se usan los créditos hacia arriba, puede mantener la cuenta y usar las características y servicios de Azure gratuitos.
* [Activar Visual Studio ventajas de suscriptor](/pricing/member-offers/msdn-benefits-details/?WT.mc_id=redis_cache_hero). Su suscripción de MSDN le proporciona créditos cada mes que puede usar para los servicios de Azure pagados.

### <a name="visual-studio-2015-with-the-azure-sdk-for-net"></a>2015 de Visual Studio con el SDK de Azure para .NET

El tutorial está escrito para Visual Studio de 2015 con el [SDK de Azure para .NET](../dotnet-sdk.md) 2.8.2 o posterior. [Descargar el SDK de Azure más reciente para Visual Studio año 2015 aquí](http://go.microsoft.com/fwlink/?linkid=518003). Visual Studio se instala automáticamente con el SDK, si todavía no tiene.

Si tiene 2013 de Visual Studio, puede [descargar el SDK de Azure más reciente para Visual Studio de 2013](http://go.microsoft.com/fwlink/?LinkID=324322). Algunas pantallas pueden tener un aspecto diferentes de las ilustraciones que se muestra en este tutorial.

>[AZURE.NOTE] Dependiendo de cuántos las dependencias SDK ya tiene en su equipo, instalar el SDK podría tardar mucho tiempo, desde varios minutos a media hora o más.

## <a name="create-the-visual-studio-project"></a>Crear el proyecto de Visual Studio

1. Abra Visual Studio y haga clic en **archivo**, **nuevo** **proyecto**.

2. Expanda el nodo **Visual C#** en la lista de **plantillas** , seleccione la **nube**y haga clic en la **Aplicación Web de ASP.NET**. Asegúrese de que **.NET Framework 4.5.2** está seleccionada.  Escriba **ContosoTeamStats** en el cuadro de texto **nombre** y haga clic en **Aceptar**.
 
    ![Crear proyecto][cache-create-project]

3. Seleccione **MVC** como el tipo de proyecto. Desactive la casilla de verificación de **Host en la nube** . Deberá [proporcionar los recursos de Azure](#provision-the-azure-resources) y [publicar la aplicación en Azure](#publish-the-application-to-azure) en los pasos siguientes en el tutorial. Para obtener un ejemplo de una aplicación de servicio de aplicación web de Visual Studio de aprovisionamiento dejando **Host en la nube** activada, vea [Introducción a aplicaciones Web en el servicio de aplicación de Azure, mediante ASP.NET y Visual Studio](../app-service-web/web-sites-dotnet-get-started.md).

    ![Seleccione la plantilla de proyecto][cache-select-template]

4. Haga clic en **Aceptar** para crear el proyecto.

## <a name="create-the-aspnet-mvc-application"></a>Crear la aplicación MVC de ASP.NET

En esta sección del tutorial, deberá crear la aplicación básica que lee y muestra las estadísticas de grupo desde una base de datos.

-   [Agregar el modelo](#add-the-model)
-   [Agregar el controlador](#add-the-controller)
-   [Configurar las vistas](#configure-the-views)

### <a name="add-the-model"></a>Agregar el modelo

1. Haga clic en **modelos** en el **Explorador de soluciones**y elija **Agregar**, **clase**. 

    ![Agregar modelo][cache-model-add-class]

2. Escriba `Team` para la clase asigne un nombre y haga clic en **Agregar**.

    ![Agregar clase modelo][cache-model-add-class-dialog]

3. Reemplazar el `using` instrucciones en la parte superior de la `Team.cs` archivo con la siguiente mediante instrucciones.


        using System;
        using System.Collections.Generic;
        using System.Data.Entity;
        using System.Data.Entity.SqlServer;


4. Reemplace la definición de la `Team` clase con fragmento de código siguiente actualizados `Team` definición, así como otras clases de aplicación auxiliar de entidad Framework de clase. Para obtener más información sobre el primer enfoque de código al marco de entidad que se utiliza en este tutorial, vea el [código en primer lugar a una nueva base de datos](https://msdn.microsoft.com/data/jj193542).


        public class Team
        {
            public int ID { get; set; }
            public string Name { get; set; }
            public int Wins { get; set; }
            public int Losses { get; set; }
            public int Ties { get; set; }
        
            static public void PlayGames(IEnumerable<Team> teams)
            {
                // Simple random generation of statistics.
                Random r = new Random();
        
                foreach (var t in teams)
                {
                    t.Wins = r.Next(33);
                    t.Losses = r.Next(33);
                    t.Ties = r.Next(0, 5);
                }
            }
        }
        
        public class TeamContext : DbContext
        {
            public TeamContext()
                : base("TeamContext")
            {
            }
        
            public DbSet<Team> Teams { get; set; }
        }
        
        public class TeamInitializer : CreateDatabaseIfNotExists<TeamContext>
        {
            protected override void Seed(TeamContext context)
            {
                var teams = new List<Team>
                {
                    new Team{Name="Adventure Works Cycles"},
                    new Team{Name="Alpine Ski House"},
                    new Team{Name="Blue Yonder Airlines"},
                    new Team{Name="Coho Vineyard"},
                    new Team{Name="Contoso, Ltd."},
                    new Team{Name="Fabrikam, Inc."},
                    new Team{Name="Lucerne Publishing"},
                    new Team{Name="Northwind Traders"},
                    new Team{Name="Consolidated Messenger"},
                    new Team{Name="Fourth Coffee"},
                    new Team{Name="Graphic Design Institute"},
                    new Team{Name="Nod Publishers"}
                };
        
                Team.PlayGames(teams);
        
                teams.ForEach(t => context.Teams.Add(t));
                context.SaveChanges();
            }
        }
        
        public class TeamConfiguration : DbConfiguration
        {
            public TeamConfiguration()
            {
                SetExecutionStrategy("System.Data.SqlClient", () => new SqlAzureExecutionStrategy());
            }
        }


2. En el **Explorador de soluciones**, haga doble clic en **web.config** para abrirlo.

    ![Web.config][cache-web-config]

3.  Agregue la siguiente cadena de conexión para la `connectionStrings` sección. El nombre de la cadena de conexión debe coincidir con el nombre de la clase de contexto de base de datos de entidad Framework que es `TeamContext`.

        <add name="TeamContext" connectionString="Data Source=(LocalDB)\v11.0;AttachDbFilename=|DataDirectory|\Teams.mdf;Integrated Security=True" providerName="System.Data.SqlClient" />


    Después de agregar, el `connectionStrings` sección debe tener un aspecto similar al ejemplo siguiente.


        <connectionStrings>
            <add name="DefaultConnection" connectionString="Data Source=(LocalDb)\MSSQLLocalDB;AttachDbFilename=|DataDirectory|\aspnet-ContosoTeamStats-20160216120918.mdf;Initial Catalog=aspnet-ContosoTeamStats-20160216120918;Integrated Security=True"
                providerName="System.Data.SqlClient" />
            <add name="TeamContext" connectionString="Data Source=(LocalDB)\v11.0;AttachDbFilename=|DataDirectory|\Teams.mdf;Integrated Security=True"  providerName="System.Data.SqlClient" />
        </connectionStrings>

### <a name="add-the-controller"></a>Agregar el controlador

1. Presione **F6** para generar el proyecto. 
2. En el **Explorador de soluciones**, haga clic en la carpeta de **controladores** y elija **Agregar** **controlador**.

    ![Agregar controlador][cache-add-controller]

3. Elija **MVC 5 controlador con las vistas, con el marco de trabajo de entidad**y haga clic en **Agregar**. Si recibe un error después de hacer clic en **Agregar**, asegúrese de que ha creado el proyecto en primer lugar.

    ![Agregar clase de controlador][cache-add-controller-class]

5. Seleccione el **grupo (ContosoTeamStats.Models)** de la lista desplegable de **clase de modelo** . Seleccione **TeamContext (ContosoTeamStats.Models)** en la lista desplegable de **clase de contexto de datos** . Tipo de `TeamsController` en el cuadro de texto Nombre de **controlador** (si no se rellena automáticamente). Haga clic en **Agregar** para crear la clase de controlador y agregar la vista predeterminada.

    ![Configurar controlador][cache-configure-controller]

4. En el **Explorador de soluciones**, expanda **Global.asax** y haga doble clic en **Global.asax.cs** para abrirlo.

    ![Global.asax.cs][cache-global-asax]

5. Agregar las dos siguientes instrucciones using en la parte superior del archivo en otra mediante las instrucciones.


        using System.Data.Entity;
        using ContosoTeamStats.Models;


6. Agregue la siguiente línea de código al final de la `Application_Start` método.


        Database.SetInitializer<TeamContext>(new TeamInitializer());


7. En el **Explorador de soluciones**, expanda `App_Start` y haga doble clic en `RouteConfig.cs`.

    ![RouteConfig.cs][cache-RouteConfig-cs]

8. Reemplazar `controller = "Home"` en el código siguiente en la `RegisterRoutes` método con `controller = "Teams"` tal como se muestra en el ejemplo siguiente.


        routes.MapRoute(
            name: "Default",
            url: "{controller}/{action}/{id}",
            defaults: new { controller = "Teams", action = "Index", id = UrlParameter.Optional }
        );


### <a name="configure-the-views"></a>Configurar las vistas

1. En el **Explorador de soluciones**, expanda la carpeta de **vistas** y, a continuación, en la carpeta **compartida** y haga doble clic en **_Layout.cshtml**. 

    ![_Layout.cshtml][cache-layout-cshtml]

2. Cambiar el contenido de la `title` elemento y reemplazar `My ASP.NET Application` con `Contoso Team Stats` tal como se muestra en el ejemplo siguiente.


        <title>@ViewBag.Title - Contoso Team Stats</title>


3. En la `body` sección, actualizar la primera `Html.ActionLink` instrucción y reemplazar `Application name` con `Contoso Team Stats` y reemplazar `Home` con `Teams`.
    -   Antes:`@Html.ActionLink("Application name", "Index", "Home", new { area = "" }, new { @class = "navbar-brand" })`
    -   Después de:`@Html.ActionLink("Contoso Team Stats", "Index", "Teams", new { area = "" }, new { @class = "navbar-brand" })`

    ![Cambios de código][cache-layout-cshtml-code]

4. Presione **Ctrl + F5** para generar y ejecutar la aplicación. Esta versión de la aplicación lee los resultados directamente desde la base de datos. Tenga en cuenta las acciones de **Nuevo para crear**, **Editar**, **Detalles**y **Eliminar** que se han agregado automáticamente a la aplicación por la scaffold **MVC 5 controlador con las vistas, con el marco de trabajo de entidad** . En la sección siguiente del tutorial que agregará Redis caché para optimizar el acceso a datos y proporcionar características adicionales a la aplicación.

![Aplicación de inicio][cache-starter-application]

## <a name="configure-the-application-to-use-redis-cache"></a>Configurar la aplicación para utilizar Redis caché

En esta sección del tutorial, deberá configurar la aplicación de ejemplo para almacenar y recuperar las estadísticas de grupo de Contoso desde una instancia de Azure Redis caché mediante el cliente de caché [StackExchange.Redis](https://github.com/StackExchange/StackExchange.Redis) .

-   [Configurar la aplicación para utilizar StackExchange.Redis](#configure-the-application-to-use-stackexchangeredis)
-   [Actualizar la clase TeamsController para devolver los resultados de la memoria caché o la base de datos](#update-the-teamscontroller-class-to-return-results-from-the-cache-or-the-database)
-   [Actualizar los métodos de crear, editar y eliminar para trabajar con la memoria caché](#update-the-create-edit-and-delete-methods-to-work-with-the-cache)
-   [Actualizar la vista de índice a los equipos a trabajar con la memoria caché](#update-the-teams-index-view-to-work-with-the-cache)


### <a name="configure-the-application-to-use-stackexchangeredis"></a>Configurar la aplicación para utilizar StackExchange.Redis

1. Para configurar una aplicación de cliente en Visual Studio mediante el paquete StackExchange.Redis NuGet, haga clic en el proyecto en el **Explorador de soluciones** y elija **Administrar paquetes de NuGet**. 

    ![Administrar paquetes de NuGet][redis-cache-manage-nuget-menu]

2. Escriba **StackExchange.Redis** en el cuadro de texto de búsqueda, seleccione la versión deseada de los resultados y haga clic en **instalar**.

    ![Paquete StackExchange.Redis NuGet][redis-cache-stack-exchange-nuget]

    El paquete de NuGet descarga y agrega las referencias de ensamblado necesario para su aplicación de cliente tener acceso a la caché de Azure Redis con el cliente de caché StackExchange.Redis. Si prefiere usar una versión con nombre seguro de la biblioteca de cliente de **StackExchange.Redis** , elija **StackExchange.Redis.StrongName**; en caso contrario, elija **StackExchange.Redis**.

3. En el **Explorador de soluciones**, expanda la carpeta de **controladores** y haga doble clic en **TeamsController.cs** para abrirla.

    ![Controlador de equipos][cache-teamscontroller]

4. Agregar las dos siguientes instrucciones using al **TeamsController.cs**.

        using System.Configuration;
        using StackExchange.Redis;

5. Agregue las siguientes propiedades de dos a la `TeamsController` clase.

        // Redis Connection string info
        private static Lazy<ConnectionMultiplexer> lazyConnection = new Lazy<ConnectionMultiplexer>(() =>
        {
            string cacheConnection = ConfigurationManager.AppSettings["CacheConnection"].ToString();
            return ConnectionMultiplexer.Connect(cacheConnection);
        });
    
        public static ConnectionMultiplexer Connection
        {
            get
            {
                return lazyConnection.Value;
            }
        }
  
1. Crear un archivo en el equipo denominado `WebAppPlusCacheAppSecrets.config` y colocarlo en una ubicación que no se comprueba con el código fuente de la aplicación de ejemplo, si decide comprobar en algún lugar. En este ejemplo la `AppSettingsSecrets.config` archivo se encuentra en `C:\AppSecrets\WebAppPlusCacheAppSecrets.config`.

    Editar la `WebAppPlusCacheAppSecrets.config` archivo y agregue el contenido siguiente. Si ejecuta la aplicación localmente esta información se usa para conectarse a su instancia de Azure Redis caché. Más adelante en el tutorial deberá proporcionar una instancia de Azure Redis caché y actualizar el nombre de la memoria caché y la contraseña. Si no va a ejecutar la aplicación de ejemplo localmente puede omitir la creación de este archivo y los pasos posteriores que hacen referencia al archivo, porque cuando se implementa en Azure la aplicación recupera la información de conexión de la caché de la configuración de la aplicación para la aplicación Web y no desde este archivo. Puesto que la `WebAppPlusCacheAppSecrets.config` no se ha implementado en Azure con la aplicación, no es necesario a menos que se va a ejecutar la aplicación localmente.


        <appSettings>
          <add key="CacheConnection" value="MyCache.redis.cache.windows.net,abortConnect=false,ssl=true,password=..."/>
        </appSettings>


2. En el **Explorador de soluciones**, haga doble clic en **web.config** para abrirlo.

    ![Web.config][cache-web-config]

3. Agregue las siguientes `file` atributo a la `appSettings` elemento. Si utiliza un nombre de archivo diferente o una ubicación, sustituir los valores para los que se muestra en el ejemplo.
    -   Antes:`<appSettings>`
    -   Después de:` <appSettings file="C:\AppSecrets\WebAppPlusCacheAppSecrets.config">`

    El tiempo de ejecución ASP.NET combina el contenido del archivo externo con el marcado en la `<appSettings>` elemento. El tiempo de ejecución pasa por alto el atributo de archivo si no se encuentra el archivo especificado. La información confidencial (la cadena de conexión a la memoria caché) no se incluye como parte del código fuente de la aplicación. Cuando se implementa la aplicación web en Azure, la `WebAppPlusCacheAppSecrests.config` archivo no implementado (que es lo que desea). Hay varias formas de especificar estos información confidencial en Azure y en este tutorial se configuran automáticamente para cuando [aprovisionar los recursos de Azure](#provision-the-azure-resources) en un tutorial posterior paso. Para obtener más información sobre cómo trabajar con información confidencial en Azure, consulte [prácticas recomendadas para implementar contraseñas y otros datos confidenciales a ASP.NET y servicio de la aplicación de Azure](http://www.asp.net/identity/overview/features-api/best-practices-for-deploying-passwords-and-other-sensitive-data-to-aspnet-and-azure).


### <a name="update-the-teamscontroller-class-to-return-results-from-the-cache-or-the-database"></a>Actualizar la clase TeamsController para devolver los resultados de la memoria caché o la base de datos

En este ejemplo, se pueden recuperar las estadísticas de grupo de la base de datos o de la memoria caché. Estadísticas de grupo se almacenan en la memoria caché como un número de serie `List<Team>`y también como un conjunto ordenado utilizando Redis tipos de datos. Al recuperar elementos de un conjunto ordenado, puede recuperar algunos, todos o consultar para determinados elementos. En este ejemplo podrá consultar el conjunto ordenado de los equipos de 5 superiores de la clasificación por número de wins.

>[AZURE.NOTE] No es necesario almacenar las estadísticas de grupo en varios formatos en la memoria caché para utilizar Azure Redis caché. Este tutorial utiliza varios formatos para demostrar algunas de las diferentes maneras y diferentes tipos de datos que puede usar para almacenar datos en caché.



1. Agregue las siguientes instrucciones using a la `TeamsController.cs` archivo en la parte superior con el otro con instrucciones.

        using System.Diagnostics;
        using Newtonsoft.Json;

2. Reemplazar la actual `public ActionResult Index()` método con la siguiente implementación.


        // GET: Teams
        public ActionResult Index(string actionType, string resultType)
        {
            List<Team> teams = null;
        
            switch(actionType)
            {
                case "playGames": // Play a new season of games.
                    PlayGames();
                    break;
        
                case "clearCache": // Clear the results from the cache.
                    ClearCachedTeams();
                    break;
        
                case "rebuildDB": // Rebuild the database with sample data.
                    RebuildDB();
                    break;
            }
        
            // Measure the time it takes to retrieve the results.
            Stopwatch sw = Stopwatch.StartNew();
        
            switch(resultType)
            {
                case "teamsSortedSet": // Retrieve teams from sorted set.
                    teams = GetFromSortedSet();
                    break;
        
                case "teamsSortedSetTop5": // Retrieve the top 5 teams from the sorted set.
                    teams = GetFromSortedSetTop5();
                    break;
        
                case "teamsList": // Retrieve teams from the cached List<Team>.
                    teams = GetFromList();
                    break;
        
                case "fromDB": // Retrieve results from the database.
                default:
                    teams = GetFromDB();
                    break;
            }
        
            sw.Stop();
            double ms = sw.ElapsedTicks / (Stopwatch.Frequency / (1000.0));

            // Add the elapsed time of the operation to the ViewBag.msg.
            ViewBag.msg += " MS: " + ms.ToString();
        
            return View(teams);
        }


3. Agregar los tres métodos siguientes a la `TeamsController` clase para implementar el `playGames`, `clearCache`, y `rebuildDB` tipos de acción de la instrucción de conmutador agregado en el fragmento de código anterior.

    La `PlayGames` método actualiza las estadísticas de grupo al simular una temporada de juegos, guarda los resultados de la base de datos y borra los datos de la memoria caché ahora obsoletos.


        void PlayGames()
        {
            ViewBag.msg += "Updating team statistics. ";
            // Play a "season" of games.
            var teams = from t in db.Teams
                        select t;
    
            Team.PlayGames(teams);
    
            db.SaveChanges();
    
            // Clear any cached results
            ClearCachedTeams();
        }


    La `RebuildDB` método reinicializa la base de datos con el conjunto de los grupos predeterminados, genera estadísticas para ellos y borra los datos de la memoria caché ahora obsoletos.
    
        void RebuildDB()
        {
            ViewBag.msg += "Rebuilding DB. ";
            // Delete and re-initialize the database with sample data.
            db.Database.Delete();
            db.Database.Initialize(true);

            // Clear any cached results
            ClearCachedTeams();
        }


    La `ClearCachedTeams` método quita las estadísticas de grupo en caché de la memoria caché.

    
        void ClearCachedTeams()
        {
            IDatabase cache = Connection.GetDatabase();
            cache.KeyDelete("teamsList");
            cache.KeyDelete("teamsSortedSet");
            ViewBag.msg += "Team data removed from cache. ";
        } 


4. Agregar los cuatro métodos siguientes a la `TeamsController` clase para implementar las diversas formas de recuperar las estadísticas de grupo de la caché y la base de datos. Cada uno de estos métodos devuelve una `List<Team>` que se muestra a continuación, en la vista.

    La `GetFromDB` método lee las estadísticas de grupo de la base de datos.

        List<Team> GetFromDB()
        {
            ViewBag.msg += "Results read from DB. ";
            var results = from t in db.Teams
                orderby t.Wins descending
                select t; 
    
            return results.ToList<Team>();
        }


    La `GetFromList` método lee las estadísticas de grupo de caché como un número de serie `List<Team>`. Si hay un error de caché, las estadísticas de grupo son leer de la base de datos y, a continuación, se almacena en la caché para la próxima vez. En este ejemplo, usaremos JSON.NET serialización para serializar los objetos .NET a y desde la memoria caché. Para obtener más información, vea [cómo trabajar con objetos de .NET en Azure Redis caché](cache-dotnet-how-to-use-azure-redis-cache.md#work-with-net-objects-in-the-cache).

        List<Team> GetFromList()
        {
            List<Team> teams = null;

            IDatabase cache = Connection.GetDatabase();
            string serializedTeams = cache.StringGet("teamsList");
            if (!String.IsNullOrEmpty(serializedTeams))
            {
                teams = JsonConvert.DeserializeObject<List<Team>>(serializedTeams);

                ViewBag.msg += "List read from cache. ";
            }
            else
            {
                ViewBag.msg += "Teams list cache miss. ";
                // Get from database and store in cache
                teams = GetFromDB();

                ViewBag.msg += "Storing results to cache. ";
                cache.StringSet("teamsList", JsonConvert.SerializeObject(teams));
            }
            return teams;
        }


    La `GetFromSortedSet` método lee las estadísticas de grupo de un conjunto ordenado en caché. Si hay un error de caché, las estadísticas de grupo son leer de la base de datos y se almacenan en la memoria caché como un conjunto ordenado.


        List<Team> GetFromSortedSet()
        {
            List<Team> teams = null;
            IDatabase cache = Connection.GetDatabase();
            // If the key teamsSortedSet is not present, this method returns a 0 length collection.
            var teamsSortedSet = cache.SortedSetRangeByRankWithScores("teamsSortedSet", order: Order.Descending);
            if (teamsSortedSet.Count() > 0)
            {
                ViewBag.msg += "Reading sorted set from cache. ";
                teams = new List<Team>();
                foreach (var t in teamsSortedSet)
                {
                    Team tt = JsonConvert.DeserializeObject<Team>(t.Element);
                    teams.Add(tt);
                }
            }
            else
            {
                ViewBag.msg += "Teams sorted set cache miss. ";
    
                // Read from DB
                teams = GetFromDB();
    
                ViewBag.msg += "Storing results to cache. ";
                foreach (var t in teams)
                {
                    Console.WriteLine("Adding to sorted set: {0} - {1}", t.Name, t.Wins);
                    cache.SortedSetAdd("teamsSortedSet", JsonConvert.SerializeObject(t), t.Wins);
                }
            }
            return teams;
        }


    La `GetFromSortedSetTop5` método lee los equipos de 5 principales de la caché conjunto ordenado. Se inicia activando la memoria caché la existencia de la `teamsSortedSet` clave. Si esta clave no está presente, el `GetFromSortedSet` se llama método para leer las estadísticas de grupo y guardarlos en la memoria caché. A continuación se consulta el conjunto ordenado en caché de los equipos de 5 superiores que se devuelven.


        List<Team> GetFromSortedSetTop5()
        {
            List<Team> teams = null;
            IDatabase cache = Connection.GetDatabase();

            // If the key teamsSortedSet is not present, this method returns a 0 length collection.
            var teamsSortedSet = cache.SortedSetRangeByRankWithScores("teamsSortedSet", stop: 4, order: Order.Descending);
            if(teamsSortedSet.Count() == 0)
            {
                // Load the entire sorted set into the cache.
                GetFromSortedSet();

                // Retrieve the top 5 teams.
                teamsSortedSet = cache.SortedSetRangeByRankWithScores("teamsSortedSet", stop: 4, order: Order.Descending);
            }

            ViewBag.msg += "Retrieving top 5 teams from cache. ";
            // Get the top 5 teams from the sorted set
            teams = new List<Team>();
            foreach (var team in teamsSortedSet)
            {
                teams.Add(JsonConvert.DeserializeObject<Team>(team.Element));
            }
            return teams;
        }


### <a name="update-the-create-edit-and-delete-methods-to-work-with-the-cache"></a>Actualizar los métodos de crear, editar y eliminar para trabajar con la memoria caché

El código de scaffolding generado como parte de este ejemplo incluye métodos para agregar, editar y eliminar equipos. Cada vez que un grupo es agregar, editar o quitar, los datos de la memoria caché pasa a ser obsoletos. En esta sección podrá modificar estos tres métodos para borrar la caché de equipos y la caché no estará sincronizada con la base de datos.

1. Vaya a la `Create(Team team)` método en la `TeamsController` clase. Agregar una llamada a la `ClearCachedTeams` método, tal como se muestra en el ejemplo siguiente.


        // POST: Teams/Create
        // To protect from overposting attacks, please enable the specific properties you want to bind to, for 
        // more details see http://go.microsoft.com/fwlink/?LinkId=317598.
        [HttpPost]
        [ValidateAntiForgeryToken]
        public ActionResult Create([Bind(Include = "ID,Name,Wins,Losses,Ties")] Team team)
        {
            if (ModelState.IsValid)
            {
                db.Teams.Add(team);
                db.SaveChanges();
                // When a team is added, the cache is out of date.
                // Clear the cached teams.
                ClearCachedTeams();
                return RedirectToAction("Index");
            }
    
            return View(team);
        }


2. Vaya a la `Edit(Team team)` método en la `TeamsController` clase. Agregar una llamada a la `ClearCachedTeams` método, tal como se muestra en el ejemplo siguiente.


        // POST: Teams/Edit/5
        // To protect from overposting attacks, please enable the specific properties you want to bind to, for 
        // more details see http://go.microsoft.com/fwlink/?LinkId=317598.
        [HttpPost]
        [ValidateAntiForgeryToken]
        public ActionResult Edit([Bind(Include = "ID,Name,Wins,Losses,Ties")] Team team)
        {
            if (ModelState.IsValid)
            {
                db.Entry(team).State = EntityState.Modified;
                db.SaveChanges();
                // When a team is edited, the cache is out of date.
                // Clear the cached teams.
                ClearCachedTeams();
                return RedirectToAction("Index");
            }
            return View(team);
        }


3. Vaya a la `DeleteConfirmed(int id)` método en la `TeamsController` clase. Agregar una llamada a la `ClearCachedTeams` método, tal como se muestra en el ejemplo siguiente.


        // POST: Teams/Delete/5
        [HttpPost, ActionName("Delete")]
        [ValidateAntiForgeryToken]
        public ActionResult DeleteConfirmed(int id)
        {
            Team team = db.Teams.Find(id);
            db.Teams.Remove(team);
            db.SaveChanges();
            // When a team is deleted, the cache is out of date.
            // Clear the cached teams.
            ClearCachedTeams();
            return RedirectToAction("Index");
        }


### <a name="update-the-teams-index-view-to-work-with-the-cache"></a>Actualizar la vista de índice a los equipos a trabajar con la memoria caché

1. En el **Explorador de soluciones**, expanda la carpeta de **vistas** , a continuación, en la carpeta **equipos** y haga doble clic en **Index.cshtml**.

    ![Index.cshtml][cache-views-teams-index-cshtml]

2. Cerca de la parte superior del archivo, busque el siguiente elemento de párrafo.

    ![Tabla de acción][cache-teams-index-table]

    Este es el vínculo para crear un grupo nuevo. Reemplace el elemento de párrafo con la tabla siguiente. Esta tabla contiene vínculos a acciones para crear un nuevo equipo, reproducir una nueva temporada de juegos, borrar la caché, recuperar los equipos de la caché en varios formatos, recuperar los equipos de la base de datos y reconstruir la base de datos con datos de ejemplo de un nuevo.


        <table class="table">
            <tr>
                <td>
                    @Html.ActionLink("Create New", "Create")
                </td>
                <td>
                    @Html.ActionLink("Play Season", "Index", new { actionType = "playGames" })
                </td>
                <td>
                    @Html.ActionLink("Clear Cache", "Index", new { actionType = "clearCache" })
                </td>
                <td>
                    @Html.ActionLink("List from Cache", "Index", new { resultType = "teamsList" })
                </td>
                <td>
                    @Html.ActionLink("Sorted Set from Cache", "Index", new { resultType = "teamsSortedSet" })
                </td>
                <td>
                    @Html.ActionLink("Top 5 Teams from Cache", "Index", new { resultType = "teamsSortedSetTop5" })
                </td>
                <td>
                    @Html.ActionLink("Load from DB", "Index", new { resultType = "fromDB" })
                </td>
                <td>
                    @Html.ActionLink("Rebuild DB", "Index", new { actionType = "rebuildDB" })
                </td>
            </tr>    
        </table>


3. Desplácese hasta el final del archivo **Index.cshtml** y agregue las siguientes `tr` elemento para que sea la última fila de la última tabla en el archivo.

        <tr><td colspan="5">@ViewBag.Msg</td></tr>

    Esta fila muestra el valor de `ViewBag.Msg` que contiene un informe de estado sobre la operación actual que se establece al hacer clic en uno de los vínculos de acción desde el paso anterior.   

    ![Mensaje de estado][cache-status-message]

4. Presione **F6** para generar el proyecto.

## <a name="provision-the-azure-resources"></a>Aprovisionar los recursos de Azure

Para hospedar la aplicación en Azure, primero debe proporcionar los servicios de Azure que requiere la aplicación. La aplicación de ejemplo en este tutorial usa los siguientes servicios de Azure.

-   Caché de Azure Redis
-   Aplicación de servicio de aplicación Web
-   Base de datos SQL

Para implementar estos servicios en un grupo de recursos nuevos o existentes de su elección, haga clic en el botón siguiente de **implementar en Azure** .

[! [Implementar en Azure] [deploybutton]](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-web-app-redis-cache-sql-database%2Fazuredeploy.json)

Este botón **implementar a Azure** utiliza la plantilla de [Azure tutorial](https://github.com/Azure/azure-quickstart-templates) [crear una aplicación Web más caché Redis más bases de datos SQL](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-redis-cache-sql-database) para aprovisionar estos servicios y establecer la cadena de conexión para la base de datos de SQL y la configuración de la aplicación de la cadena de conexión de Azure Redis caché.

>[AZURE.NOTE] Si no tiene una cuenta de Azure, puede [crear una cuenta gratuita de Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=redis_cache_hero) en solo un par de minutos.

Haciendo clic en el botón de **implementar a Azure** se tarda en el portal de Azure e inicia el proceso de creación de los recursos descritos por la plantilla.

![Implementar en Azure][cache-deploy-to-azure-step-1]

1. En el módulo de **implementación personalizado** , seleccione la suscripción Azure a usar y seleccione un grupo de recursos existente o cree uno nuevo y especifique la ubicación del grupo de recursos.
2. En el módulo de **parámetros** , especifique un nombre de cuenta de administrador (**ADMINISTRATORLOGIN** - no usar el **Administrador**), contraseña de inicio de sesión de administrador (**ADMINISTRATORLOGINPASSWORD**) y el nombre de la base de datos (**DATABASENAME**). Los demás parámetros se configuran para un servicio gratuito de aplicación aloja plan y menor costo opciones para base de datos de SQL Azure Redis caché, que no vienen con un nivel gratuito.
3. Cambiar la configuración si lo desea, o mantener los valores predeterminados y haga clic en **Aceptar**.


![Implementar en Azure][cache-deploy-to-azure-step-2]

1. Haga clic en **condiciones legales de revisión**.
2. Lea los términos en el módulo de **compra** y haga clic en **comprar**.
3. Para comenzar el aprovisionamiento de los recursos, haga clic en **crear** en el módulo de **implementación personalizada** .

Para ver el progreso de la implementación, haga clic en el icono de notificación y haga clic en **implementación iniciado**.

![Implementación iniciado][cache-deployment-started]

Puede ver el estado de la implementación en el módulo **Microsoft.Template** .

![Implementar en Azure][cache-deploy-to-azure-step-3]

Cuando termine de aprovisionamiento, puede publicar la aplicación a Azure de Visual Studio.

>[AZURE.NOTE] Se muestran los errores que pueden producirse durante el proceso de aprovisionamiento en el módulo **Microsoft.Template** . Errores comunes son demasiados servidores de SQL Server o demasiados servicio gratuito de aplicación host planes de suscripción. Resolver los errores y reinicie el proceso haciendo clic en **volver a implementar** en el módulo **Microsoft.Template** o el botón de **implementar a Azure** en este tutorial.

## <a name="publish-the-application-to-azure"></a>Publicar la aplicación en Azure

En este paso del tutorial, deberá publicar la aplicación en Azure y ejecutar en la nube.

1. Haga clic en el proyecto **ContosoTeamStats** en Visual Studio y elija **Publicar**.

    ![Publicar][cache-publish-app]

2. Haga clic en **Servicio de aplicaciones de Microsoft Azure**.

    ![Publicar][cache-publish-to-app-service]

3. Seleccione la suscripción que se utiliza al crear los recursos de Azure, expanda el grupo de recursos que contenga los recursos, seleccione la aplicación Web que desee y haga clic en **Aceptar**. Si utiliza el botón de **implementar a Azure** inicia el nombre de la aplicación Web con **sitio Web** seguido de algunos caracteres adicionales.

    ![Seleccione la aplicación Web][cache-select-web-app]

4. Haga clic en **Validar conexión** para comprobar la configuración y, a continuación, haga clic en **Publicar**.

    ![Publicar][cache-publish]

    Pasados unos momentos se complete el proceso de publicación y un explorador se iniciará con la ejecución de ejemplo de aplicación. Si recibe un error DNS al validar o publicación y recientemente ha completado el proceso de aprovisionamiento de los recursos para la aplicación de Azure, espere un momento y vuelva a intentarlo.

    ![Caché de agregado][cache-added-to-application]

La tabla siguiente describe cada vínculo de acción de la aplicación de ejemplo.

| Acción                  | Descripción                                                                                                                                                      |
|-------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Crear nuevo              | Crear un grupo nuevo.                                                                                                                                               |
| Reproducir temporada             | Reproducir una temporada de juegos, actualizar las estadísticas de grupo y desactive cualquiera obsoleta datos de la caché de grupo.                                                                          |
| Borrar caché             | Borrar las estadísticas de grupo de la caché.                                                                                                                             |
| Lista de caché         | Recuperar las estadísticas de grupo de la caché. Si hay un error de caché, las estadísticas de carga de la base de datos y guardar en la memoria caché para la próxima vez.                                        |
| Conjunto ordenado de caché   | Recuperar las estadísticas de grupo de la caché mediante un conjunto ordenado. Si hay un error de caché, las estadísticas de carga de la base de datos y guardar en la memoria caché mediante un conjunto ordenado.  |
| Grupos de 5 principales de caché  | Recuperar los equipos de 5 superiores de la memoria caché mediante un conjunto ordenado. Si hay un error de caché, las estadísticas de carga de la base de datos y guardar en la memoria caché mediante un conjunto ordenado. |
| Cargar la base de datos            | Recuperar las estadísticas de grupo de la base de datos.                                                                                                                       |
| Reconstruir la base de datos              | Reconstruir la base de datos y volver a cargarlo con datos de grupo de ejemplo.                                                                                                        |
| Editar detalles y eliminar | Editar un grupo, ver los detalles de un grupo, eliminar un grupo.                                                                                                             |


Haga clic en algunas de las acciones y experimentar con recuperar los datos de distintos orígenes. No las diferencias en el tiempo que se tarda en completar las diversas maneras de recuperar los datos de la base de datos y la memoria caché.

## <a name="delete-the-resources-when-you-are-finished-with-the-application"></a>Eliminar los recursos cuando haya terminado con la aplicación

Cuando haya terminado con la aplicación de tutorial de ejemplo, puede eliminar los recursos de Azure utilizados para conservar el costo y recursos. Si usa el botón de **implementar a Azure** en la sección [aprovisionar los recursos de Azure](#provision-the-azure-resources) y todos los recursos están contenidos en el mismo grupo de recursos, puede eliminarlas de manera conjunta en una operación eliminando el grupo de recursos.

1. Inicie sesión en el [portal de Azure](https://portal.azure.com) y haga clic en **grupos de recursos**.
2. En el cuadro de texto **Filtrar elementos...** , escriba el nombre de su grupo de recursos.
3. Haga clic en **...** a la derecha de su grupo de recursos.
4. Haga clic en **Eliminar**.

    ![Eliminar][cache-delete-resource-group]

5. Escriba el nombre de su grupo de recursos y haga clic en **Eliminar**.

    ![Confirmar la eliminación][cache-delete-confirm]

Pasados unos momentos se eliminan el grupo de recursos y todos sus recursos contenidas.

>[AZURE.IMPORTANT] Tenga en cuenta que la eliminación de un grupo de recursos es irreversible y que el grupo de recursos y todos los recursos se eliminan permanentemente. Asegúrese de que no elimina accidentalmente el grupo de recursos incorrecto o recursos. Si ha creado los recursos para hospedar este ejemplo dentro de un grupo de recursos existente, puede eliminar cada recurso individualmente de sus módulos respectivos.

## <a name="run-the-sample-application-on-your-local-machine"></a>Ejecute la aplicación de ejemplo en el equipo local

Para ejecutar la aplicación localmente en el equipo, necesita una instancia de Azure Redis caché en el que los datos en caché. 

-   Si ha publicado su aplicación en Azure como se describe en la sección anterior, puede usar la instancia de caché de Azure Redis aprovisionada durante este paso.
-   Si tiene otra instancia de Azure Redis caché existente, puede usar para ejecutar este ejemplo localmente.
-   Si necesita crear una instancia de Azure Redis caché, puede seguir los pasos de [crear una caché](cache-dotnet-how-to-use-azure-redis-cache.md#create-a-cache).

Una vez que ha seleccionado o creado que utilice la caché, vaya a la caché en el portal de Azure y recuperar el [nombre de host](cache-configure.md#properties) y [las teclas de acceso](cache-configure.md#access-keys) para la memoria caché. Para obtener instrucciones, consulte [Configurar Redis configuración de la caché](cache-configure.md#configure-redis-cache-settings).

1. Abrir la `WebAppPlusCacheAppSecrets.config` archivo que creó en el paso de [Configurar la aplicación para utilizar Redis caché](#configure-the-application-to-use-redis-cache) de este tutorial utilizando el editor de su elección.

2. Editar la `value` atributo y reemplace `MyCache.redis.cache.windows.net` con el [nombre de host](cache-configure.md#properties) de la memoria caché y especifique la [clave principal o secundario](cache-configure.md#access-keys) de la memoria caché como la contraseña.


        <appSettings>
          <add key="CacheConnection" value="MyCache.redis.cache.windows.net,abortConnect=false,ssl=true,password=..."/>
        </appSettings>


3. Presione **Ctrl + F5** para ejecutar la aplicación.

>[AZURE.NOTE] Observe que, dado que la aplicación, incluida la base de datos se está ejecutando localmente y la caché Redis está alojada en Azure, la caché puede aparecer en realizar la base de datos. Para mejorar el rendimiento, la aplicación cliente y la instancia de Azure Redis caché debería en la misma ubicación. 

## <a name="next-steps"></a>Pasos siguientes

-   Más información sobre la [Introducción a ASP.NET MVC 5](http://www.asp.net/mvc/overview/getting-started/introduction/getting-started) en el sitio [ASP.NET](http://asp.net/) .
-   Para obtener más ejemplos de creación de una aplicación Web de ASP.NET en la aplicación de servicio, consulte [crear e implementar una aplicación web de ASP.NET en la aplicación de servicio de Azure](https://github.com/Microsoft/HealthClinic.biz/wiki/Create-and-deploy-an-ASP.NET-web-app-in-Azure-App-Service) desde la conexión de [HealthClinic.biz](https://github.com/Microsoft/HealthClinic.biz) 2015 [demostración](https://blogs.msdn.microsoft.com/visualstudio/2015/12/08/connectdemos-2015-healthclinic-biz/).
    -   Para más tutoriales de la demostración HealthClinic.biz, vea [Tutoriales de herramientas de desarrollador de Azure](https://github.com/Microsoft/HealthClinic.biz/wiki/Azure-Developer-Tools-Quickstarts).
-   Obtenga más información sobre el enfoque de [código primero a una nueva base de datos](https://msdn.microsoft.com/data/jj193542) para la estructura de entidad que se utiliza en este tutorial.
-   Más información sobre [web apps en el servicio de aplicación de Azure](../app-service-web/app-service-web-overview.md).
-   Obtenga información sobre cómo [supervisar](cache-how-to-monitor.md) la memoria caché en el portal de Azure.

-   Explorar las características premium de Azure Redis caché
    -   [Cómo configurar persistencia para una caché de Redis Premium Azure](cache-how-to-premium-persistence.md)
    -   [Cómo configurar clústeres para una caché de Redis Premium Azure](cache-how-to-premium-clustering.md)
    -   [Cómo configurar la compatibilidad con una red Virtual Premium Azure Redis caché](cache-how-to-premium-vnet.md)
    -   Consulte las [Preguntas más frecuentes de caché de Redis Azure](cache-faq.md#what-redis-cache-offering-and-size-should-i-use) para obtener más detalles sobre el tamaño, el rendimiento y el ancho de banda con caché premium.



<!-- IMAGES -->
[cache-starter-application]: ./media/cache-web-app-howto/cache-starter-application.png
[cache-added-to-application]: ./media/cache-web-app-howto/cache-added-to-application.png
[cache-create-project]: ./media/cache-web-app-howto/cache-create-project.png
[cache-select-template]: ./media/cache-web-app-howto/cache-select-template.png
[cache-model-add-class]: ./media/cache-web-app-howto/cache-model-add-class.png
[cache-model-add-class-dialog]: ./media/cache-web-app-howto/cache-model-add-class-dialog.png
[cache-add-controller]: ./media/cache-web-app-howto/cache-add-controller.png
[cache-add-controller-class]: ./media/cache-web-app-howto/cache-add-controller-class.png
[cache-configure-controller]: ./media/cache-web-app-howto/cache-configure-controller.png
[cache-global-asax]: ./media/cache-web-app-howto/cache-global-asax.png
[cache-RouteConfig-cs]: ./media/cache-web-app-howto/cache-RouteConfig-cs.png
[cache-layout-cshtml]: ./media/cache-web-app-howto/cache-layout-cshtml.png
[cache-layout-cshtml-code]: ./media/cache-web-app-howto/cache-layout-cshtml-code.png
[redis-cache-manage-nuget-menu]: ./media/cache-web-app-howto/redis-cache-manage-nuget-menu.png
[redis-cache-stack-exchange-nuget]: ./media/cache-web-app-howto/redis-cache-stack-exchange-nuget.png
[cache-teamscontroller]: ./media/cache-web-app-howto/cache-teamscontroller.png
[cache-web-config]: ./media/cache-web-app-howto/cache-web-config.png
[cache-views-teams-index-cshtml]: ./media/cache-web-app-howto/cache-views-teams-index-cshtml.png
[cache-teams-index-table]: ./media/cache-web-app-howto/cache-teams-index-table.png
[cache-status-message]: ./media/cache-web-app-howto/cache-status-message.png
[deploybutton]: ./media/cache-web-app-howto/deploybutton.png
[cache-deploy-to-azure-step-1]: ./media/cache-web-app-howto/cache-deploy-to-azure-step-1.png
[cache-deploy-to-azure-step-2]: ./media/cache-web-app-howto/cache-deploy-to-azure-step-2.png
[cache-deploy-to-azure-step-3]: ./media/cache-web-app-howto/cache-deploy-to-azure-step-3.png
[cache-deployment-started]: ./media/cache-web-app-howto/cache-deployment-started.png
[cache-publish-app]: ./media/cache-web-app-howto/cache-publish-app.png
[cache-publish-to-app-service]: ./media/cache-web-app-howto/cache-publish-to-app-service.png
[cache-select-web-app]: ./media/cache-web-app-howto/cache-select-web-app.png
[cache-publish]: ./media/cache-web-app-howto/cache-publish.png
[cache-delete-resource-group]: ./media/cache-web-app-howto/cache-delete-resource-group.png
[cache-delete-confirm]: ./media/cache-web-app-howto/cache-delete-confirm.png

