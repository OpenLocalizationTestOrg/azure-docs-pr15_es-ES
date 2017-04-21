<properties 
    pageTitle="Tutorial de ASP.NET MVC para DocumentDB: desarrollo de aplicaciones Web | Microsoft Azure" 
    description="Tutorial de ASP.NET MVC para crear una aplicación web MVC con DocumentDB. Deberá almacenar JSON y tener acceso a datos desde una aplicación de todo hospedada en sitios Web de Azure - tutorial paso a paso de ASP neto MVC." 
    keywords="tutorial de ASP.NET mvc, desarrollo de aplicaciones web, aplicación web de mvc, tutorial de asp mvc neto paso a paso"
    services="documentdb" 
    documentationCenter=".net" 
    authors="syamkmsft" 
    manager="jhubbard" 
    editor="cgronlun"/>


<tags 
    ms.service="documentdb" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="dotnet" 
    ms.topic="hero-article" 
    ms.date="08/25/2016" 
    ms.author="syamk"/>

# <a name="_Toc395809351"></a>ASP.NET MVC Tutorial: Desarrollo de aplicaciones Web con DocumentDB

> [AZURE.SELECTOR]
- [.NET](documentdb-dotnet-application.md)
- [Node.js](documentdb-nodejs-application.md)
- [Java](documentdb-java-application.md)
- [Python](documentdb-python-application.md) 

Para resaltar cómo aprovechar eficazmente DocumentDB de Azure para almacenar y consultar documentos JSON, este artículo proporciona un tutorial to-end que muestra cómo crear una aplicación de todo con DocumentDB de Azure. Las tareas se guardarán como documentos JSON en DocumentDB de Azure.

![Captura de pantalla de la aplicación web de MVC creado por este tutorial - tutorial ASP neto MVC paso a paso de la lista de tareas pendientes](./media/documentdb-dotnet-application/asp-net-mvc-tutorial-image1.png)

En este tutorial se muestra cómo usar el servicio de DocumentDB proporcionado por Azure para almacenar y tener acceso a datos desde una aplicación web de ASP.NET MVC hospedada en Azure. Si busca un tutorial que sólo se centra en DocumentDB y no los componentes de ASP.NET MVC, vea [crear una aplicación de consola DocumentDB C#](documentdb-get-started.md).

> [AZURE.TIP] En este tutorial, se supone que tiene experiencia previa con ASP.NET MVC y sitios Web de Azure. Si es nuevo en ASP.NET o las [Herramientas como requisito previos](#_Toc395637760), se recomienda descargar el proyecto de ejemplo completo de [GitHub][] y siguiendo las instrucciones de este ejemplo. Una vez que ha creado, puede revisar este artículo para obtener información sobre el código en el contexto del proyecto.

## <a name="_Toc395637760"></a>Requisitos previos para este tutorial de base de datos

Antes de seguir las instrucciones de este artículo, debe asegurarse de que tiene los siguientes:

- Una cuenta de Azure active. Si no tiene una cuenta, puede crear una cuenta de prueba gratuita de dos minutos. Para obtener información detallada, vea [Prueba gratuita de Azure](https://azure.microsoft.com/pricing/free-trial/).
- [Visual Studio de 2015](http://www.visualstudio.com/) o actualización de Visual Studio 2013 4 o posterior. Si usa 2013 de Visual Studio, debe instalar el [paquete de nuget Microsoft.Net.Compilers](https://www.nuget.org/packages/Microsoft.Net.Compilers/) para agregar la compatibilidad con C# 6.0. 
- Azure SDK para .NET versión 2.5.1 o posterior, disponible mediante el [Instalador de plataforma Web de Microsoft][].

Todas las capturas de pantalla en este artículo se han adoptado con 2013 de Visual Studio Update 4 aplicado y el SDK de Azure para .NET versión 2.5.1. Si el sistema está configurado con diferentes versiones, es posible que las opciones y pantallas no coincidirán con por completo, pero si cumple los requisitos previos anteriores esta solución debería funcionar.

## <a name="_Toc395637761"></a>Paso 1: Crear una cuenta de base de datos DocumentDB

Empecemos creando una cuenta de DocumentDB. Si ya tiene una cuenta, vaya al [crear una nueva aplicación ASP.NET MVC](#_Toc395637762).

[AZURE.INCLUDE [documentdb-create-dbaccount](../../includes/documentdb-create-dbaccount.md)]

[AZURE.INCLUDE [documentdb-keys](../../includes/documentdb-keys.md)]

<br/>
Ahora trataremos cómo crear una nueva aplicación MVC de ASP.NET desde cero. 

## <a name="_Toc395637762"></a>Paso 2: Crear una nueva aplicación MVC de ASP.NET

Ahora que ya tiene una cuenta, vamos a crear el nuevo proyecto ASP.NET.

1. En Visual Studio, en el menú **archivo** , apunte a **nuevo**y, a continuación, haga clic en **proyecto**.

    Aparece el cuadro de diálogo **Nuevo proyecto** .
2. En el panel **tipos de proyecto** , expanda **plantillas**, **Visual C#**, **Web**y, a continuación, seleccione la **Aplicación Web de ASP.NET**.

    ![Captura de pantalla del cuadro de diálogo nuevo proyecto con el tipo de proyecto de aplicación Web de ASP.NET resaltado](./media/documentdb-dotnet-application/asp-net-mvc-tutorial-image10.png)

3. En el cuadro **nombre** , escriba el nombre del proyecto. En este tutorial se utiliza el nombre "todo". Si elige usar un número distinto de esto, a continuación, siempre que este tutorial trata sobre el espacio de nombres de todo, debe ajustar los ejemplos de código proporcionado para usar cualquier cosa había denominado la aplicación. 

4. Haga clic en **Examinar** para desplazarse a la carpeta donde desea crear el proyecto y, a continuación, haga clic en **Aceptar**.

    Aparece el cuadro de diálogo **Nuevo proyecto de ASP.NET** .

    ![Captura de pantalla del cuadro de diálogo nuevo proyecto de ASP.NET con la plantilla de aplicación de MVC resaltado y el Host en el cuadro de nube activado](./media/documentdb-dotnet-application/asp-net-mvc-tutorial-image11.png)

5. En el panel Plantillas, seleccione **MVC**.

6. Si va a hospedar su aplicación en Azure, a continuación, seleccione **Host en la nube** en la esquina inferior derecha tener Azure hospedar la aplicación. Hemos seleccionado para hospedar en la nube y ejecutar la aplicación alojada en un sitio Web de Azure. Si selecciona esta opción se preprovision un sitio Web de Azure para usted y facilitar la vida mucho cuando sea el momento de implementar la aplicación de trabajo final. Si desea hospedar en otro sitio o no desea configurar Azure por adelantado, simplemente desactive **Host en la nube**.

7. Haga clic en **Aceptar** y deje que Visual Studio lo alrededor de scaffolding la plantilla de ASP.NET MVC vacía. 

8. Si decide alojar esto en la nube que se muestra al menos una pantalla adicionales que le pide que inicie sesión en su cuenta de Azure y proporcionar valores para el nuevo sitio Web. Proporcionar todos los valores adicionales y continuar. 

    Que no he elegido "servidor de base de datos" aquí porque no estamos utilizando un servidor de base de datos de SQL Azure aquí, vamos a crear una nueva cuenta de Azure DocumentDB más adelante en el Portal de Azure.

    Para obtener más información sobre cómo elegir un **plan de servicio de aplicaciones** y un **grupo de recursos**, vea [introducción exhaustiva de planes de servicio de aplicaciones de Azure](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md).

    ![Captura de pantalla del cuadro de diálogo Configurar el sitio de Web de Microsoft Azure](./media/documentdb-dotnet-application/image11_1.png)

9. Una vez que Visual Studio ha terminado de crear la aplicación MVC repetitivo tiene una aplicación de ASP.NET vacía que se puede ejecutar localmente.

    Se omitirá ejecutando el proyecto localmente porque estoy seguro hemos visto ASP.NET "Hola a todos" de aplicación. Volvamos directamente a agregar DocumentDB a este proyecto y la creación de la aplicación.

## <a name="_Toc395637767"></a>Paso 3: Agregar DocumentDB a un proyecto de aplicación web MVC

Ahora que tenemos la mayor parte de la infraestructura de ASP.NET MVC que se necesita para esta solución, pongamos en marcha para el propósito de este tutorial, agregar Azure DocumentDB a nuestra aplicación web MVC real.

1. DocumentDB .NET SDK se empaqueta y se distribuye como un paquete de NuGet. Para obtener el paquete de NuGet en Visual Studio, use el Administrador de paquetes de NuGet en Visual Studio haciendo doble clic en el proyecto en el **Explorador de soluciones** y, a continuación, haciendo clic en **Administrar paquetes de NuGet**.

    ![Captura de pantalla de las opciones del botón derecho para el proyecto de aplicación web en el Explorador de soluciones, con administrar paquetes de NuGet resaltado.](./media/documentdb-dotnet-application/image21.png)

    Aparece el cuadro de diálogo **Administrar paquetes de NuGet** .

2. En el cuadro NuGet **Examinar** , escriba ***DocumentDB de Azure***.
    
    Desde los resultados, instale el paquete de la **Biblioteca de cliente de Microsoft Azure DocumentDB** . Esto se descargue e instale el paquete de DocumentDB, así como todas las dependencias, como Newtonsoft.Json. Haga clic en **Aceptar** en la ventana de **vista previa** y **** en la ventana de **Aceptación de la licencia** para completar la instalación.

    ![Captura de pantalla de la ventana Administrar paquetes de NuGet, con la biblioteca de cliente de Microsoft Azure DocumentDB resaltado](./media/documentdb-dotnet-application/nuget.png)

    También puede usar la consola del Administrador de paquete para instalar el paquete. Para ello, en el menú **Herramientas** , haga clic en **Administrador de paquetes de NuGet**y, a continuación, haga clic en la **Consola del Administrador de paquetes**. En el símbolo del sistema, escriba lo siguiente.

        Install-Package Microsoft.Azure.DocumentDB

3. Una vez instalado el paquete, la solución de Visual Studio debe ser similar a la siguiente con dos nuevas referencias agregadas, Microsoft.Azure.Documents.Client y Newtonsoft.Json.

    ![Captura de pantalla de las dos referencias que agregó al proyecto datos JSON en Explorador de soluciones](./media/documentdb-dotnet-application/image22.png)


##<a name="_Toc395637763"></a>Paso 4: Configurar la aplicación MVC de ASP.NET
 
Ahora vamos a agregar los modelos, vistas y controladores para esta aplicación MVC:

- [Agregar un modelo](#_Toc395637764).
- [Agregar un controlador](#_Toc395637765).
- [Agregar vistas](#_Toc395637766).


### <a name="_Toc395637764"></a>Agregar un modelo de datos JSON

Empecemos por crear la **M** en MVC, el modelo. 

1. En el **Explorador de soluciones**, haga clic en la carpeta de **modelos** , haga clic en **Agregar**y, a continuación, haga clic en **clase**.

    Aparece el cuadro de diálogo **Agregar nuevo elemento** .

2. Nombre de su nueva clase **Item.cs** y haga clic en **Agregar**. 

3. En este nuevo archivo **Item.cs** , agregue lo siguiente después de la última *instrucción using*.
        
        using Newtonsoft.Json;
    
4. Ahora reemplazar este código 
        
        public class Item
        {
        }

    con el siguiente código.

        public class Item
        {
            [JsonProperty(PropertyName = "id")]
            public string Id { get; set; }
             
            [JsonProperty(PropertyName = "name")]
            public string Name { get; set; }

            [JsonProperty(PropertyName = "description")]
            public string Description { get; set; }

            [JsonProperty(PropertyName = "isComplete")]
            public bool Completed { get; set; }
        }

    Todos los datos de DocumentDB se transfieren por la red y se almacenan como JSON. Para controlar el modo en que los objetos son serie/deserializar JSON.NET puede utilizar el atributo **JsonProperty** como se muestra en la clase de **elemento** que se acaba de crear. No **tiene** hacer este pero quiero garantizar que las propiedades siguen las convenciones de nomenclatura camelCase JSON. 
    
    No solo puede controlar el formato del nombre de propiedad cuando pasa a JSON, pero por completo puede cambiar las propiedades de .NET como hacía con la propiedad **Descripción** . 
    

### <a name="_Toc395637765"></a>Agregar un controlador

Que se encarga de la **M**, ahora vamos a crear la **C** en MVC, una clase de controlador.

1. En el **Explorador de soluciones**, haga clic en la carpeta de **controladores** , haga clic en **Agregar**y, a continuación, haga clic en el **controlador**.

    Aparece el cuadro de diálogo **Agregar Scaffold** .

2. Seleccione **Controlador de MVC 5 - vacía** y, a continuación, haga clic en **Agregar**.

    ![Captura de pantalla del cuadro de diálogo Agregar Scaffold con el controlador de MVC 5 - opción vacío resaltada](./media/documentdb-dotnet-application/image14.png)

3. Asigne un nombre a su nuevo controlador, **ItemController.**

    ![Captura de pantalla del cuadro de diálogo Agregar controlador](./media/documentdb-dotnet-application/image15.png)

    Una vez creado el archivo, la solución de Visual Studio debe ser similar a la siguiente con el nuevo archivo ItemController.cs en el **Explorador de soluciones**. También se muestra el nuevo archivo de Item.cs que creó anteriormente.

    ![Captura de pantalla de la solución de Visual Studio - Explorador de soluciones con el archivo nuevo ItemController.cs y Item.cs resaltado](./media/documentdb-dotnet-application/image16.png)

    Puede cerrar ItemController.cs, se deberá volver a él más adelante. 

### <a name="_Toc395637766"></a>Agregar vistas

Ahora, vamos a crear la **V** en MVC, las vistas:

- [Agregar una vista de índice del elemento](#AddItemIndexView).
- [Agregar una vista de nuevo elemento](#AddNewIndexView).
- [Agregar una vista de Editar elemento](#_Toc395888515).


#### <a name="AddItemIndexView"></a>Agregar una vista de índice del elemento

1. En el **Explorador de soluciones**, expanda la carpeta de **vistas** , secundario en la carpeta de **elemento** vacía que creará Visual Studio cuando agregó el **ItemController** anteriormente, haga clic en **Agregar**y, a continuación, haga clic en **Ver**.

    ![Captura de pantalla del explorador de soluciones con la carpeta de elementos que Visual Studio se creó con los comandos de agregar vista resaltados](./media/documentdb-dotnet-application/image17.png)

2. En el cuadro de diálogo **Agregar vista** , haga lo siguiente:
    - En el cuadro **nombre de vista** , escriba el ***índice***.
    - En el cuadro **plantilla** , seleccione ***lista***.
    - En el cuadro **clase del modelo** , seleccione ***elemento (todo. Modelos)***.
    - Deje vacío el cuadro de **clase de contexto de datos** . 
    - En el cuadro página de diseño, escriba ***~/Views/Shared/_Layout.cshtml***.
    
    ![Captura de pantalla que muestra el cuadro de diálogo Agregar vista](./media/documentdb-dotnet-application/image18.png)

3. Una vez que se establecen todos estos valores, haga clic en **Agregar** y deje que Visual Studio a crear una nueva vista de plantilla. Una vez hecho, se abrirá el archivo cshtml que se creó. Podemos Cerrar archivo en Visual Studio como se volverá a él más adelante.

#### <a name="AddNewIndexView"></a>Agregar una vista de nuevo elemento

De forma similar al modo en que se creó una vista de **Índice del elemento** , ahora crearemos una nueva vista para crear nuevos **elementos**.

1. En el **Explorador de soluciones**, haga clic en la carpeta de **elementos** de nuevo, haga clic en **Agregar**y, a continuación, haga clic en **Ver**.

2. En el cuadro de diálogo **Agregar vista** , haga lo siguiente:
    - En el cuadro **nombre de vista** , escriba ***crear***.
    - En el cuadro **plantilla** , seleccione ***crear***.
    - En el cuadro **clase del modelo** , seleccione ***elemento (todo. Modelos)***.
    - Deje vacío el cuadro de **clase de contexto de datos** .
    - En el cuadro página de diseño, escriba ***~/Views/Shared/_Layout.cshtml***.
    - Haga clic en **Agregar**.

#### <a name="_Toc395888515"></a>Agregar una vista de Editar elemento

Y por último, agregue una última vista para editar un **elemento** de la misma manera que antes.

1. En el **Explorador de soluciones**, haga clic en la carpeta de **elementos** de nuevo, haga clic en **Agregar**y, a continuación, haga clic en **Ver**.

2. En el cuadro de diálogo **Agregar vista** , haga lo siguiente:
    - En el cuadro **nombre de vista** , escriba ***Editar***.
    - En el cuadro **plantilla** , seleccione ***Editar***.
    - En el cuadro **clase del modelo** , seleccione ***elemento (todo. Modelos)***.
    - Deje vacío el cuadro de **clase de contexto de datos** . 
    - En el cuadro página de diseño, escriba ***~/Views/Shared/_Layout.cshtml***.
    - Haga clic en **Agregar**.

Una vez hecho esto, cerrar todos los documentos de cshtml en Visual Studio, ya que se devolverá a estas vistas más adelante.

## <a name="_Toc395637769"></a>Paso 5: Conectar DocumentDB

Ahora que está teniendo en cuenta las cosas MVC estándar de, pasaremos a agregar el código para DocumentDB. 

En esta sección, agregaremos código para controlar lo siguiente:

- [Lista de elementos incompletos](#_Toc395637770).
- [Agregar elementos](#_Toc395637771).
- [Editar elementos](#_Toc395637772).

### <a name="_Toc395637770"></a>Lista de elementos incompletos en la aplicación web MVC

Lo primero que hay que hacer aquí es agregar una clase que contiene el valor lógico para conectarse y usar DocumentDB. En este tutorial se deberá encapsulados toda esta lógica en a una clase de repositorio denominada DocumentDBRepository. 

1. En el **Explorador de soluciones**, haga clic en el proyecto, haga clic en **Agregar**y, a continuación, haga clic en **clase**. Asigne un nombre a la nueva clase **DocumentDBRepository** y haga clic en **Agregar**.
 
2. En la recién creado **DocumentDBRepository** la clase y agregue el siguiente *mediante instrucciones* encima de la declaración de *espacio de nombres*
        
        using Microsoft.Azure.Documents; 
        using Microsoft.Azure.Documents.Client; 
        using Microsoft.Azure.Documents.Linq; 
        using System.Configuration;
        using System.Linq.Expressions;
        using System.Threading.Tasks;

    Ahora reemplazar este código 

        public class DocumentDBRepository
        {
        }

    con el siguiente código.

        public static class DocumentDBRepository<T> where T : class
        {
            private static readonly string DatabaseId = ConfigurationManager.AppSettings["database"];
            private static readonly string CollectionId = ConfigurationManager.AppSettings["collection"];
            private static DocumentClient client;
    
            public static void Initialize()
            {
                client = new DocumentClient(new Uri(ConfigurationManager.AppSettings["endpoint"]), ConfigurationManager.AppSettings["authKey"]);
                CreateDatabaseIfNotExistsAsync().Wait();
                CreateCollectionIfNotExistsAsync().Wait();
            }
    
            private static async Task CreateDatabaseIfNotExistsAsync()
            {
                try
                {
                    await client.ReadDatabaseAsync(UriFactory.CreateDatabaseUri(DatabaseId));
                }
                catch (DocumentClientException e)
                {
                    if (e.StatusCode == System.Net.HttpStatusCode.NotFound)
                    {
                        await client.CreateDatabaseAsync(new Database { Id = DatabaseId });
                    }
                    else
                    {
                        throw;
                    }
                }
            }
    
            private static async Task CreateCollectionIfNotExistsAsync()
            {
                try
                {
                    await client.ReadDocumentCollectionAsync(UriFactory.CreateDocumentCollectionUri(DatabaseId, CollectionId));
                }
                catch (DocumentClientException e)
                {
                    if (e.StatusCode == System.Net.HttpStatusCode.NotFound)
                    {
                        await client.CreateDocumentCollectionAsync(
                            UriFactory.CreateDatabaseUri(DatabaseId),
                            new DocumentCollection { Id = CollectionId },
                            new RequestOptions { OfferThroughput = 1000 });
                    }
                    else
                    {
                        throw;
                    }
                }
            }
        }

    > [AZURE.TIP] Al crear un nuevo DocumentCollection puede proporcionar un parámetro de RequestOptions opcional de OfferType, que le permite especificar el nivel de rendimiento de la colección de nuevo. Si no se pasa este parámetro se utilizará el tipo de oferta predeterminado. Para obtener más información sobre tipos de oferta de DocumentDB, consulte [Niveles de rendimiento DocumentDB](documentdb-performance-levels.md)

3. Nos estamos leer algunos valores de configuración, así que abra el archivo **Web.config** de la aplicación y agregue las siguientes líneas en el `<AppSettings>` sección.
    
        <add key="endpoint" value="enter the URI from the Keys blade of the Azure Portal"/>
        <add key="authKey" value="enter the PRIMARY KEY, or the SECONDARY KEY, from the Keys blade of the Azure  Portal"/>
        <add key="database" value="ToDoList"/>
        <add key="collection" value="Items"/>
    
4. Ahora, actualice los valores de *punto final* y *authKey* mediante el módulo de teclas del Portal de Azure. Utilice el **URI** de la hoja de claves como el valor de la configuración de punto final y la **Clave principal**o **Clave secundaria** desde el módulo de claves como el valor de la configuración de authKey.


    Que se encarga de cableado seguridad del repositorio de DocumentDB ahora vamos a agrega la lógica de aplicación.

5. Lo primero que queremos que pueda hacer con una aplicación de la lista todo es mostrar los elementos incompletos.  Copie y pegue el siguiente fragmento de código en cualquier lugar dentro de la clase **DocumentDBRepository** .

        public static async Task<IEnumerable<T>> GetItemsAsync(Expression<Func<T, bool>> predicate)
        {
            IDocumentQuery<T> query = client.CreateDocumentQuery<T>(
                UriFactory.CreateDocumentCollectionUri(DatabaseId, CollectionId))
                .Where(predicate)
                .AsDocumentQuery();

            List<T> results = new List<T>();
            while (query.HasMoreResults)
            {
                results.AddRange(await query.ExecuteNextAsync<T>());
            }

            return results;
        }


6. Abra **ItemController** que agregó anteriormente y agregue el siguiente *con instrucciones* encima de la declaración de espacio de nombres.

        using System.Net;
        using System.Threading.Tasks;
        using todo.Models;

    Si el proyecto no se denomina "todo", a continuación, deberá actualizar el uso de "todo. Modelos"; para reflejar el nombre del proyecto.

    Ahora reemplazar este código

        //GET: Item
        public ActionResult Index()
        {
            return View();
        }

    con el siguiente código.

        [ActionName("Index")]
        public async Task<ActionResult> IndexAsync()
        {
            var items = await DocumentDBRepository<Item>.GetItemsAsync(d => !d.Completed);
            return View(items);
        }
    
7. Abrir **Global.asax.cs** y agregue la línea siguiente en el método **Application_Start** 
 
        DocumentDBRepository<todo.Models.Item>.Initialize();
    
En este momento la solución debería poder crear sin errores.

Si ejecutó la aplicación ahora, ¿vaya **HomeController** y la vista de **índice** de ese controlador. Este es el comportamiento predeterminado para el proyecto de plantilla MVC que elegimos al principio, pero no queremos! Vamos a cambiar el enrutamiento de esta aplicación MVC cambiar este comportamiento.

Abrir ***aplicación\_Start\RouteConfig.cs*** y busque la línea que comienza con "valores predeterminados:" y cámbielo para similares a los siguientes.

        defaults: new { controller = "Item", action = "Index", id = UrlParameter.Optional }

Esto ahora le indica a ASP.NET MVC si no ha especificado un valor en la dirección URL para controlar el comportamiento de enrutamiento que en lugar de **Inicio**, use **elemento** como el **índice** de usuario y el controlador de vista.

Ahora si ejecuta la aplicación, llama a su **ItemController** que llamar a la clase de repositorio y use el método GetItems para devolver todos los elementos incompletos a las **vistas**\\**elemento**\\vista**índice** . 

Si crear y ejecutar este proyecto ahora, ahora debe ver algo que tiene este aspecto.    

![Captura de pantalla de la aplicación de web de lista todo creada por este tutorial de base de datos](./media/documentdb-dotnet-application/image23.png)

### <a name="_Toc395637771"></a>Agregar elementos

Pongamos algunos elementos en la base de datos, así que hay algo más que una cuadrícula vacía para mirar.

Vamos a agregar código a DocumentDBRepository y ItemController para conservar el registro en DocumentDB.

1.  Agregue el método siguiente a la clase **DocumentDBRepository** .

        public static async Task<Document> CreateItemAsync(T item)
        {
            return await client.CreateDocumentAsync(UriFactory.CreateDocumentCollectionUri(DatabaseId, CollectionId), item);
        }

    Este método simplemente toma un objeto pasado y continúa en DocumentDB.

2. Abra el archivo ItemController.cs y agregue el fragmento de código siguiente dentro de la clase. Esto es cómo ASP.NET MVC sabe qué hacer para la acción **crear** . En este caso simplemente representar la vista Create.cshtml asociada que creó anteriormente.

        [ActionName("Create")]
        public async Task<ActionResult> CreateAsync()
        {
            return View();
        }

    Ahora debemos más código de este controlador que acepte la presentación desde la vista de **creación** .

2. Agregue el bloque de código siguiente a la clase ItemController.cs que indica a ASP.NET MVC qué hacer con un formulario de entrada para este controlador.
    
        [HttpPost]
        [ActionName("Create")]
        [ValidateAntiForgeryToken]
        public async Task<ActionResult> CreateAsync([Bind(Include = "Id,Name,Description,Completed")] Item item)
        {
            if (ModelState.IsValid)
            {
                await DocumentDBRepository<Item>.CreateItemAsync(item);
                return RedirectToAction("Index");
            }

            return View(item);
        }

    Este código llama a la DocumentDBRepository y utiliza el método CreateItemAsync para conservar el nuevo elemento todo a la base de datos. 
 
    **Nota de seguridad**: el atributo **ValidateAntiForgeryToken** se utiliza aquí para ayudar a proteger esta aplicación ataques de falsificación de solicitud entre sitios. Hay más que simplemente agregar este atributo, las vistas necesitan para trabajar con este token contra también. Para obtener más información sobre el tema y ejemplos de cómo implementar este correctamente, vea [Impedir falsificación de solicitud entre sitios][]. El código fuente proporcionado en [GitHub][] tiene la implementación completa en su lugar.

    **Nota de seguridad**: También usamos el atributo **enlazar** el parámetro de método para ayudar a proteger contra exceso registro ataques. Para obtener más detalles, vea [Operaciones CRUD básicas en ASP.NET MVC][].

Esto concluye el código necesario para agregar nuevos elementos a la base de datos.


### <a name="_Toc395637772"></a>Editar elementos

Hay una cosa más que para poder hacer, y que es agregar la capacidad de editar **elementos** en la base de datos y marcarlos como completado. La vista de edición ya se ha agregado al proyecto, por lo que necesitamos agregar código a nuestro controlador y a la clase **DocumentDBRepository** nuevamente.

1. Agregue lo siguiente a la clase **DocumentDBRepository** .

        public static async Task<Document> UpdateItemAsync(string id, T item)
        {
            return await client.ReplaceDocumentAsync(UriFactory.CreateDocumentUri(DatabaseId, CollectionId, id), item);
        }

        public static async Task<T> GetItemAsync(string id)
        {
            try
            {
                Document document = await client.ReadDocumentAsync(UriFactory.CreateDocumentUri(DatabaseId, CollectionId, id));
                return (T)(dynamic)document;
            }
            catch (DocumentClientException e)
            {
                if (e.StatusCode == HttpStatusCode.NotFound)
                {
                    return null;
                }
                else
                {
                    throw;
                }
            }
        }
    
    El primero de estos métodos, **GetItem** búsquedas de un elemento de DocumentDB que se pasa a la **ItemController** y, a continuación, en la vista de **Edición** .
    
    El segundo de los métodos hemos agregado reemplaza que el **documento** en DocumentDB con la versión del **documento** pasado desde el **ItemController**.

2. Agregue lo siguiente a la clase **ItemController** .

        [HttpPost]
        [ActionName("Edit")]
        [ValidateAntiForgeryToken]
        public async Task<ActionResult> EditAsync([Bind(Include = "Id,Name,Description,Completed")] Item item)
        {
            if (ModelState.IsValid)
            {
                await DocumentDBRepository<Item>.UpdateItemAsync(item.Id, item);
                return RedirectToAction("Index");
            }

            return View(item);
        }

        [ActionName("Edit")]
        public async Task<ActionResult> EditAsync(string id)
        {
            if (id == null)
            {
                return new HttpStatusCodeResult(HttpStatusCode.BadRequest);
            }

            Item item = await DocumentDBRepository<Item>.GetItemAsync(id);
            if (item == null)
            {
                return HttpNotFound();
            }

            return View(item);
        }
    
    El primer método controladores Http GET que ocurre cuando el usuario hace clic en el vínculo **Editar** de la vista de **índice** . Este método recupera un [**documento**](http://msdn.microsoft.com/library/azure/microsoft.azure.documents.document.aspx) de DocumentDB y pasa a la vista de **Edición** .

    La vista de **Edición** , a continuación, va a hacer un Http POST en la **IndexController**. 
    
    El segundo método agregamos controladores pasando el objeto actualizado a DocumentDB conservarse en la base de datos.

Eso es todo, todo lo que necesita para ejecutar la aplicación, incompleta **elementos**de lista, agregar nuevos **elementos**y editar **elementos**.

## <a name="_Toc395637773"></a>Paso 6: Ejecute la aplicación localmente

Para probar la aplicación en el equipo local, haga lo siguiente:

1. Presione F5 en Visual Studio para generar la aplicación en modo de depuración. Debe generar la aplicación e iniciar un explorador con la página de cuadrícula vacía que se ha visto antes:

    ![Captura de pantalla de la aplicación de web de lista todo creada por este tutorial de base de datos](./media/documentdb-dotnet-application/image24.png)

    Si está utilizando 2013 de Visual Studio y recibe el error "No se espera en el cuerpo de una cláusula capturas". debe instalar el [paquete de nuget Microsoft.Net.Compilers](https://www.nuget.org/packages/Microsoft.Net.Compilers/). También puede comparar el código con el proyecto de ejemplo en [GitHub][]. 

2. Haga clic en el vínculo **Crear nuevo** y agregar valores a los campos **nombre** y **Descripción** . Deje el **completado** casilla desactivada en caso contrario, el nuevo **elemento** se agregará en un estado completado y no aparecerá en la lista inicial.

    ![Captura de pantalla de la vista de creación](./media/documentdb-dotnet-application/image25.png)

3. Haga clic en **crear** y se le redirigirá a la vista de **índice** y su **elemento** aparece en la lista.

    ![Captura de pantalla de la vista de índice](./media/documentdb-dotnet-application/image26.png)

    No dude en agregar más algunos **elementos** a su lista de tareas pendientes.

3. Haga clic en **Editar** junto a un **elemento** en la lista y se toman a la vista de **Edición** que se puede actualizar cualquier propiedad de objeto, incluida la marca de **completada** . Si marcar como **completado** y haga clic en **Guardar**, el **elemento** se quita de la lista de tareas incompletas.

    ![Captura de pantalla de la vista de índice con la casilla completadas activada](./media/documentdb-dotnet-application/image27.png)

4. Una vez que ha probado la aplicación, presione Ctrl + F5 para detener la depuración de la aplicación. Está preparado para implementar.

## <a name="_Toc395637774"></a>Paso 7: Implementar la aplicación a los sitios Web de Azure

Ahora que tiene la aplicación completa funcionan correctamente con DocumentDB vamos a implementar esta aplicación web a sitios Web de Azure. Si ha seleccionado **un Host de la nube** cuando creó el proyecto de ASP.NET MVC vacío Visual Studio facilita y hace la mayor parte del trabajo para usted. 

1. Para publicar esta aplicación todo lo que debe hacer es secundario en el proyecto en el **Explorador de soluciones** y haga clic en **Publicar**.

    ![Captura de pantalla de la opción de publicación en el Explorador de soluciones](./media/documentdb-dotnet-application/image28.png)

2. Todo lo que debe estar configurado según sus credenciales; de hecho el sitio Web ya se ha creado en Azure para usted en la **Dirección URL de destino** que se muestra, todo lo que debe hacer es haga clic en **Publicar**.

    ![Cuadro de captura de pantalla del cuadro de diálogo Publicar Web de Visual Studio - tutorial ASP neto MVC paso a paso](./media/documentdb-dotnet-application/image29.png)

En unos segundos, Visual Studio se terminar de publicar su aplicación web e inicie un explorador donde puede ver su trabajo práctico que se ejecuta en Azure!

## <a name="_Toc395637775"></a>Pasos siguientes

¡Felicidades! Simplemente había creado su primera ASP.NET MVC de aplicación web mediante DocumentDB de Azure y había publicado a sitios Web de Azure. El código fuente de la aplicación completa, incluida la funcionalidad de detalle y eliminar que no se incluyen en este tutorial se puede descargar o clonar desde [GitHub][]. Si está interesado en agregándolo a la aplicación, conseguir el código y agregarlo a esta aplicación.

Para agregar funcionalidad adicional a la aplicación, revise la API disponibles en la [Biblioteca de .NET DocumentDB](https://msdn.microsoft.com/library/azure/dn948556.aspx) y no dude en contribuir a la biblioteca de .NET DocumentDB en [GitHub][]. 


[\*]: https://microsoft.sharepoint.com/teams/DocDB/Shared%20Documents/Documentation/Docs.LatestVersions/PicExportError
[Visual Studio Express]: http://www.visualstudio.com/products/visual-studio-express-vs.aspx
[Instalador de plataforma Web de Microsoft]: http://www.microsoft.com/web/downloads/platform.aspx
[Evitar solicitudes entre sitios]: http://go.microsoft.com/fwlink/?LinkID=517254
[Operaciones de CRUD básica en MVC de ASP.NET]: http://go.microsoft.com/fwlink/?LinkId=317598
[GitHub]: https://github.com/Azure-Samples/documentdb-net-todo-app
