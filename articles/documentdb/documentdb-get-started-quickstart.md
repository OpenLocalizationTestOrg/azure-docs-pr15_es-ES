<properties
    pageTitle="Tutorial de NoSQL: DocumentDB .NET SDK | Microsoft Azure"
    description="Tutorial de NoSQL que crea una base de datos en línea y la aplicación de consola de C# mediante el SDK de .NET DocumentDB. DocumentDB es una base de datos NoSQL JSON."
    keywords="tutorial de NoSQL, base de datos en línea, aplicación de consola de c#"
    services="documentdb"
    documentationCenter=".net"
    authors="AndrewHoh"
    manager="jhubbard"
    editor="monicar"/>

<tags
    ms.service="documentdb"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="hero-article"
    ms.date="09/01/2016"
    ms.author="anhoh"/>

# <a name="nosql-tutorial-build-a-documentdb-c-console-application"></a>NoSQL tutorial: crear una DocumentDB C# aplicación de consola

> [AZURE.SELECTOR]
- [.NET](documentdb-get-started.md)
- [Node.js](documentdb-nodejs-get-started.md)

Bienvenido al tutorial NoSQL de Azure DocumentDB .NET SDK. Después de obtener el proyecto Tutorial o completar el tutorial, tendrá una aplicación de consola que crea y consultas DocumentDB recursos.

- **[Tutorial](#quickstart)**: descargar el proyecto de ejemplo, agregue la información de conexión y disponer de una aplicación de DocumentDB que se ejecuta en menos de 10 minutos.
- **[Tutorial](#tutorial)**: crear la aplicación de tutorial rápido desde cero en 30 minutos.

## <a name="prerequisites"></a>Requisitos previos

- Una cuenta de Azure active. Si no tiene una, puede registrarse para obtener una [cuenta gratuita](https://azure.microsoft.com/free/).
- [2013 de visual Studio o Visual Studio de 2015](http://www.visualstudio.com/).
- .NET framework 4.6

## <a name="quickstart"></a>Tutorial rápido

1. Descargue la .zip de proyecto de ejemplo de [GitHub](https://github.com/Azure-Samples/documentdb-dotnet-getting-started-quickstart/archive/master.zip) o clonar la repo [documentdb-dotnet-Introducción-introducción-tutorial rápido](https://github.com/Azure-Samples/documentdb-dotnet-getting-started-quickstart) .
2. Usar el portal de Azure para [crear una cuenta de DocumentDB](documentdb-create-account.md).
3. En el archivo App.config, reemplace los valores EndpointUri y PrimaryKey con los valores recuperados desde el [portal de Azure](https://portal.azure.com/), navegando a módulo **DocumentDB (NoSQL)** , a continuación, haciendo clic en el **nombre de la cuenta**y haga clic en **las teclas** en el menú de recursos.
    ![Captura de pantalla del valor EndpointUri y PrimaryKey reemplazar en App.config](./media/documentdb-get-started-quickstart/nosql-tutorial-documentdb-keys.png)
4. Genere el proyecto. La ventana de la consola muestra los nuevos recursos que se creado, consultar y, a continuación, limpia.
    
    ![Captura de pantalla de la salida de la consola](./media/documentdb-get-started-quickstart/nosql-tutorial-documentdb-console-output.png)

## <a id="tutorial"></a>Tutorial

Este tutorial le guiará a través de la creación de una base de datos de DocumentDB, una colección de DocumentDB y documentos JSON. A continuación, podrá consultar la colección y limpiar y eliminar la base de datos. Este tutorial crea el mismo proyecto que el proyecto de tutorial rápido, pero deberá generar incrementalmente y recibirán explicación sobre el código que se va a agregar al proyecto.

## <a name="step-1-create-a-documentdb-account"></a>Paso 1: Crear una cuenta de DocumentDB

Vamos a crear una cuenta de DocumentDB. Si ya tiene una cuenta que desea usar, puede vaya directamente al [programa de instalación de su solución de Visual Studio](#SetupVS).

[AZURE.INCLUDE [documentdb-create-dbaccount](../../includes/documentdb-create-dbaccount.md)]

## <a id="SetupVS"></a>Paso 2: Configurar la solución de Visual Studio

1. En el equipo, abra **Visual Studio de 2015** .
2. En el menú **archivo** , seleccione **nuevo**y, a continuación, elija **proyecto**.
3. En el cuadro de diálogo **Nuevo proyecto** , seleccione **plantillas** / **Visual C#** / **Aplicación de consola**, asigne un nombre a su proyecto y, a continuación, haga clic en **Aceptar**.
![Captura de pantalla de la ventana nuevo proyecto](./media/documentdb-get-started/nosql-tutorial-new-project-2.png)
4. En el **Explorador de soluciones**, haga clic derecho en la nueva aplicación de consola, que se encuentra en la solución de Visual Studio.
5. Sin salir del menú, haga clic en **Administrar paquetes de NuGet...** 
 ![Captura de pantalla del menú de Clicked derecha para el proyecto](./media/documentdb-get-started/nosql-tutorial-manage-nuget-pacakges.png)
6. En la pestaña **Nuget** , haga clic en **Examinar**y escriba **documentdb azure** en el cuadro de búsqueda.
7. Dentro de los resultados, busque **Microsoft.Azure.DocumentDB** y haga clic en **instalar**.
El identificador de paquete para la biblioteca de cliente de DocumentDB es [Microsoft.Azure.DocumentDB](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB)
![captura de pantalla del Nuget menú para buscar el SDK de cliente de DocumentDB](./media/documentdb-get-started/nosql-tutorial-manage-nuget-pacakges-2.png)

¡Excelente! Ahora que hemos terminado el programa de instalación, vamos a empezar a escribir código. Puede encontrar un proyecto de código completo de este tutorial en [GitHub](https://github.com/Azure-Samples/documentdb-dotnet-getting-started/blob/master/src/Program.cs).

## <a id="Connect"></a>Paso 3: Conectar con una cuenta de DocumentDB

En primer lugar, agregue estas referencias hasta el comienzo de la aplicación de C#, en el archivo Program.cs:

    using System;
    using System.Linq;
    using System.Threading.Tasks;

    // ADD THIS PART TO YOUR CODE
    using System.Net;
    using Microsoft.Azure.Documents;
    using Microsoft.Azure.Documents.Client;
    using Newtonsoft.Json;

> [AZURE.IMPORTANT] Para poder completar este tutorial NoSQL, asegúrese de que agregar las dependencias anteriores.

Ahora, agregue estos dos constantes y la variable de *cliente* bajo el *programa*de clase pública.

    public class Program
    {
        // ADD THIS PART TO YOUR CODE
        private const string EndpointUri = "<your endpoint URI>";
        private const string PrimaryKey = "<your key>";
        private DocumentClient client;

Encabezado siguiente, en el [Portal de Azure](https://portal.azure.com) para recuperar su URI y la clave principal. La URI de DocumentDB y la clave principal son necesarios para la aplicación a comprender dónde desea conectarse y DocumentDB debe confiar en la conexión de la aplicación.

En el Portal de Azure, vaya a su cuenta de DocumentDB y, a continuación, haga clic en **las teclas**.

Copie el URI desde el portal y péguelo en `<your endpoint URI>` en el archivo program.cs. A continuación, copie la clave principal desde el portal y péguelo en `<your key>`.

![Captura de pantalla del Portal de Azure utiliza el tutorial de NoSQL para crear una aplicación de consola de C#. Muestra una cuenta de DocumentDB, con el concentrador activo resaltado, el botón de teclas resaltada en el módulo de cuenta DocumentDB y los valores URI de clave principal y clave secundaria resaltados en el módulo de teclas][keys]

Comenzaremos la aplicación de introducción al obtener mediante la creación de una nueva instancia de la **DocumentClient**.

Debajo del método **Main** , agregue esta nueva tarea asincrónica denominada **GetStartedDemo**, que se creará una instancia de nuestra nueva **DocumentClient**.

    static void Main(string[] args)
    {
    }

    // ADD THIS PART TO YOUR CODE
    private async Task GetStartedDemo()
    {
        this.client = new DocumentClient(new Uri(EndpointUri), PrimaryKey);
    }

Agregue el código siguiente para ejecutar la tarea asincrónica desde el método **Main** . El método **Main** detectarlos y escribirlos en la consola.

    static void Main(string[] args)
    {
            // ADD THIS PART TO YOUR CODE
            try
            {
                    Program p = new Program();
                    p.GetStartedDemo().Wait();
            }
            catch (DocumentClientException de)
            {
                    Exception baseException = de.GetBaseException();
                    Console.WriteLine("{0} error occurred: {1}, Message: {2}", de.StatusCode, de.Message, baseException.Message);
            }
            catch (Exception e)
            {
                    Exception baseException = e.GetBaseException();
                    Console.WriteLine("Error: {0}, Message: {1}", e.Message, baseException.Message);
            }
            finally
            {
                    Console.WriteLine("End of demo, press any key to exit.");
                    Console.ReadKey();
            }

Presione **F5** para ejecutar la aplicación.

¡Felicidades! Se ha conectado correctamente a una cuenta de DocumentDB, ahora Echemos un vistazo a trabajar con recursos DocumentDB.  

## <a name="step-4-create-a-database"></a>Paso 4: Crear una base de datos
Antes de agregar el código para crear una base de datos, agregar un método auxiliar para escribir en la consola.

Copie y pegue el método **WriteToConsoleAndPromptToContinue** bajo el método **GetStartedDemo** .

    // ADD THIS PART TO YOUR CODE
    private void WriteToConsoleAndPromptToContinue(string format, params object[] args)
    {
            Console.WriteLine(format, args);
            Console.WriteLine("Press any key to continue ...");
            Console.ReadKey();
    }

La [base de datos](documentdb-resources.md#databases) de DocumentDB puede crearse a través del método [CreateDatabaseAsync](https://msdn.microsoft.com/library/microsoft.azure.documents.client.documentclient.createdatabaseasync.aspx) de la clase **DocumentClient** . Una base de datos es el contenedor de almacenamiento de documentos JSON dividida entre colecciones lógico.

Copie y pegue el método **CreateDatabaseIfNotExists** bajo el método **WriteToConsoleAndPromptToContinue** .

    // ADD THIS PART TO YOUR CODE
    private async Task CreateDatabaseIfNotExists(string databaseName)
    {
            // Check to verify a database with the id=FamilyDB does not exist
            try
            {
                    await this.client.ReadDatabaseAsync(UriFactory.CreateDatabaseUri(databaseName));
                    this.WriteToConsoleAndPromptToContinue("Found {0}", databaseName);
            }
            catch (DocumentClientException de)
            {
                    // If the database does not exist, create a new database
                    if (de.StatusCode == HttpStatusCode.NotFound)
                    {
                            await this.client.CreateDatabaseAsync(new Database { Id = databaseName });
                            this.WriteToConsoleAndPromptToContinue("Created {0}", databaseName);
                    }
                    else
                    {
                            throw;
                    }
            }
    }

Copie y pegue el código siguiente en el método **GetStartedDemo** debajo de la creación de cliente. Esta opción creará una base de datos denominada *FamilyDB*.

    private async Task GetStartedDemo()
    {
        this.client = new DocumentClient(new Uri(EndpointUri), PrimaryKey);

        // ADD THIS PART TO YOUR CODE
        await this.CreateDatabaseIfNotExists("FamilyDB_va");

Presione **F5** para ejecutar la aplicación.

¡Felicidades! Ha creado correctamente una base de datos de DocumentDB.  

## <a id="CreateColl"></a>Paso 5: Crear una colección  

> [AZURE.WARNING] **CreateDocumentCollectionAsync** creará una nueva colección con rendimiento reservado, que tiene implicaciones de precios. Para obtener más detalles, visite nuestra [página de precios](https://azure.microsoft.com/pricing/details/documentdb/).

Una [colección](documentdb-resources.md#collections) puede crearse a través del método [CreateDocumentCollectionAsync](https://msdn.microsoft.com/library/microsoft.azure.documents.client.documentclient.createdocumentcollectionasync.aspx) de la clase **DocumentClient** . Una colección es un contenedor de documentos JSON y lógica de la aplicación de JavaScript asociada.

Copie y pegue el método **CreateDocumentCollectionIfNotExists** debajo de su método de **CreateDatabaseIfNotExists** .

    // ADD THIS PART TO YOUR CODE
    private async Task CreateDocumentCollectionIfNotExists(string databaseName, string collectionName)
    {
        try
        {
            await this.client.ReadDocumentCollectionAsync(UriFactory.CreateDocumentCollectionUri(databaseName, collectionName));
            this.WriteToConsoleAndPromptToContinue("Found {0}", collectionName);
        }
        catch (DocumentClientException de)
        {
            // If the document collection does not exist, create a new collection
            if (de.StatusCode == HttpStatusCode.NotFound)
            {
                DocumentCollection collectionInfo = new DocumentCollection();
                collectionInfo.Id = collectionName;

                // Configure collections for maximum query flexibility including string range queries.
                collectionInfo.IndexingPolicy = new IndexingPolicy(new RangeIndex(DataType.String) { Precision = -1 });

                // Here we create a collection with 400 RU/s.
                await this.client.CreateDocumentCollectionAsync(
                    UriFactory.CreateDatabaseUri(databaseName),
                    collectionInfo,
                    new RequestOptions { OfferThroughput = 400 });

                this.WriteToConsoleAndPromptToContinue("Created {0}", collectionName);
            }
            else
            {
                throw;
            }
        }
    }

Copie y pegue el código siguiente en el método **GetStartedDemo** debajo de la creación de la base de datos. Esta opción creará una colección de documento denominada *FamilyCollection_va*.

        this.client = new DocumentClient(new Uri(EndpointUri), PrimaryKey);

        await this.CreateDatabaseIfNotExists("FamilyDB_oa");

        // ADD THIS PART TO YOUR CODE
        await this.CreateDocumentCollectionIfNotExists("FamilyDB_va", "FamilyCollection_va");

Presione **F5** para ejecutar la aplicación.

¡Felicidades! Ha creado correctamente un conjunto de documentos de DocumentDB.  

## <a id="CreateDoc"></a>Paso 6: Crear documentos JSON
Puede crearse un [documento](documentdb-resources.md#documents) mediante el método [CreateDocumentAsync](https://msdn.microsoft.com/library/microsoft.azure.documents.client.documentclient.createdocumentasync.aspx) de la clase **DocumentClient** . Los documentos son contenido JSON (arbitrario) definidas por el usuario. Ahora, podemos Inserte uno o varios documentos. Si ya dispone de datos que desea almacenar en la base de datos, puede usar de DocumentDB [herramienta de migración de datos](documentdb-import-data.md).

En primer lugar, es necesario crear una clase de **familia** que se representa objetos almacenados en DocumentDB en este ejemplo. También crearemos **primario**, **secundario**, **mascota**, subclases de **dirección** que se utilizan dentro de la **familia**. Observe que los documentos deben tener una propiedad **Id** serie como **identificador** en JSON. Crear estas clases mediante la adición de las siguientes clases secundarias internas después del método **GetStartedDemo** .

Copiar y pegar la **familia**, **principal**, **secundario**, **mascota**y **dirección de** clases bajo el método **WriteToConsoleAndPromptToContinue** .

    private void WriteToConsoleAndPromptToContinue(string format, params object[] args)
    {
        Console.WriteLine(format, args);
        Console.WriteLine("Press any key to continue ...");
        Console.ReadKey();
    }

    // ADD THIS PART TO YOUR CODE
    public class Family
    {
        [JsonProperty(PropertyName = "id")]
        public string Id { get; set; }
        public string LastName { get; set; }
        public Parent[] Parents { get; set; }
        public Child[] Children { get; set; }
        public Address Address { get; set; }
        public bool IsRegistered { get; set; }
        public override string ToString()
        {
                return JsonConvert.SerializeObject(this);
        }
    }

    public class Parent
    {
        public string FamilyName { get; set; }
        public string FirstName { get; set; }
    }

    public class Child
    {
        public string FamilyName { get; set; }
        public string FirstName { get; set; }
        public string Gender { get; set; }
        public int Grade { get; set; }
        public Pet[] Pets { get; set; }
    }

    public class Pet
    {
        public string GivenName { get; set; }
    }

    public class Address
    {
        public string State { get; set; }
        public string County { get; set; }
        public string City { get; set; }
    }

Copie y pegue el método **CreateFamilyDocumentIfNotExists** debajo de su método de **CreateDocumentCollectionIfNotExists** .

    // ADD THIS PART TO YOUR CODE
    private async Task CreateFamilyDocumentIfNotExists(string databaseName, string collectionName, Family family)
    {
        try
        {
            await this.client.ReadDocumentAsync(UriFactory.CreateDocumentUri(databaseName, collectionName, family.Id));
            this.WriteToConsoleAndPromptToContinue("Found {0}", family.Id);
        }
        catch (DocumentClientException de)
        {
            if (de.StatusCode == HttpStatusCode.NotFound)
            {
                await this.client.CreateDocumentAsync(UriFactory.CreateDocumentCollectionUri(databaseName, collectionName), family);
                this.WriteToConsoleAndPromptToContinue("Created Family {0}", family.Id);
            }
            else
            {
                throw;
            }
        }
    }

E inserte dos documentos, uno para la familia Andersen y la familia de Wakefield.

Copie y pegue el código siguiente en el método **GetStartedDemo** debajo de la creación de la colección de documentos.

    await this.CreateDatabaseIfNotExists("FamilyDB_va");

    await this.CreateDocumentCollectionIfNotExists("FamilyDB_va", "FamilyCollection_va");

    // ADD THIS PART TO YOUR CODE
    Family andersenFamily = new Family
    {
            Id = "Andersen.1",
            LastName = "Andersen",
            Parents = new Parent[]
            {
                    new Parent { FirstName = "Thomas" },
                    new Parent { FirstName = "Mary Kay" }
            },
            Children = new Child[]
            {
                    new Child
                    {
                            FirstName = "Henriette Thaulow",
                            Gender = "female",
                            Grade = 5,
                            Pets = new Pet[]
                            {
                                    new Pet { GivenName = "Fluffy" }
                            }
                    }
            },
            Address = new Address { State = "WA", County = "King", City = "Seattle" },
            IsRegistered = true
    };

    await this.CreateFamilyDocumentIfNotExists("FamilyDB_va", "FamilyCollection_va", andersenFamily);

    Family wakefieldFamily = new Family
    {
            Id = "Wakefield.7",
            LastName = "Wakefield",
            Parents = new Parent[]
            {
                    new Parent { FamilyName = "Wakefield", FirstName = "Robin" },
                    new Parent { FamilyName = "Miller", FirstName = "Ben" }
            },
            Children = new Child[]
            {
                    new Child
                    {
                            FamilyName = "Merriam",
                            FirstName = "Jesse",
                            Gender = "female",
                            Grade = 8,
                            Pets = new Pet[]
                            {
                                    new Pet { GivenName = "Goofy" },
                                    new Pet { GivenName = "Shadow" }
                            }
                    },
                    new Child
                    {
                            FamilyName = "Miller",
                            FirstName = "Lisa",
                            Gender = "female",
                            Grade = 1
                    }
            },
            Address = new Address { State = "NY", County = "Manhattan", City = "NY" },
            IsRegistered = false
    };

    await this.CreateFamilyDocumentIfNotExists("FamilyDB_va", "FamilyCollection_va", wakefieldFamily);

Presione **F5** para ejecutar la aplicación.

¡Felicidades! Ha creado correctamente dos documentos DocumentDB.  

![Diagrama que ilustra la relación jerárquica entre la cuenta, la base de datos en línea, la colección y los documentos que se utiliza el tutorial de NoSQL para crear una aplicación de consola de C#](./media/documentdb-get-started/nosql-tutorial-account-database.png)

##<a id="Query"></a>Paso 7: Consultar DocumentDB recursos

DocumentDB admite enriquecido [consultas](documentdb-sql-query.md) en documentos JSON almacenados en cada colección.  El código de ejemplo siguiente muestra varias consultas - con ambas sintaxis SQL DocumentDB así como LINQ - que podemos ejecutar frente a los documentos ha insertado en el paso anterior.

Copie y pegue el método **ExecuteSimpleQuery** debajo de su método de **CreateFamilyDocumentIfNotExists** .

    // ADD THIS PART TO YOUR CODE
    private void ExecuteSimpleQuery(string databaseName, string collectionName)
    {
        // Set some common query options
        FeedOptions queryOptions = new FeedOptions { MaxItemCount = -1 };

            // Here we find the Andersen family via its LastName
            IQueryable<Family> familyQuery = this.client.CreateDocumentQuery<Family>(
                    UriFactory.CreateDocumentCollectionUri(databaseName, collectionName), queryOptions)
                    .Where(f => f.LastName == "Andersen");

            // The query is executed synchronously here, but can also be executed asynchronously via the IDocumentQuery<T> interface
            Console.WriteLine("Running LINQ query...");
            foreach (Family family in familyQuery)
            {
                    Console.WriteLine("\tRead {0}", family);
            }

            // Now execute the same query via direct SQL
            IQueryable<Family> familyQueryInSql = this.client.CreateDocumentQuery<Family>(
                    UriFactory.CreateDocumentCollectionUri(databaseName, collectionName),
                    "SELECT * FROM Family WHERE Family.LastName = 'Andersen'",
                    queryOptions);

            Console.WriteLine("Running direct SQL query...");
            foreach (Family family in familyQueryInSql)
            {
                    Console.WriteLine("\tRead {0}", family);
            }

            Console.WriteLine("Press any key to continue ...");
            Console.ReadKey();
    }

Copie y pegue el código siguiente en el método **GetStartedDemo** debajo de la segunda creación del documento.

    await this.CreateFamilyDocumentIfNotExists("FamilyDB_va", "FamilyCollection_va", wakefieldFamily);

    // ADD THIS PART TO YOUR CODE
    this.ExecuteSimpleQuery("FamilyDB_va", "FamilyCollection_va");

Presione **F5** para ejecutar la aplicación.

¡Felicidades! Que haya consultado correctamente con un conjunto de DocumentDB.

El siguiente diagrama muestra cómo se aplica la sintaxis de la consulta se denomina contra la recopilación creada de SQL DocumentDB y la misma lógica a la consulta LINQ también.

![Diagrama que ilustra el ámbito y el significado de la consulta que utiliza el tutorial de NoSQL para crear una aplicación de consola de C#](./media/documentdb-get-started/nosql-tutorial-collection-documents.png)

La palabra clave [FROM](documentdb-sql-query.md#from-clause) es opcional en la consulta porque ya están orientadas al DocumentDB consultas para una sola colección. Por lo tanto, "De familias f" se pueden intercambiar con "R de raíz", o cualquier otra variable nombre elija. DocumentDB se inferir esa familias, raíz o el nombre de variable elegido, hacer referencia a la colección actual de forma predeterminada.

##<a id="ReplaceDocument"></a>Paso 8: Reemplazar documento JSON

DocumentDB es compatible con los documentos JSON reemplazar.  

Copie y pegue el método **ReplaceFamilyDocument** debajo de su método de **ExecuteSimpleQuery** .

    // ADD THIS PART TO YOUR CODE
    private async Task ReplaceFamilyDocument(string databaseName, string collectionName, string familyName, Family updatedFamily)
    {
        try
        {
            await this.client.ReplaceDocumentAsync(UriFactory.CreateDocumentUri(databaseName, collectionName, familyName), updatedFamily);
            this.WriteToConsoleAndPromptToContinue("Replaced Family {0}", familyName);
        }
        catch (DocumentClientException de)
        {
            throw;
        }
    }

Copie y pegue el código siguiente en el método **GetStartedDemo** debajo de la ejecución de consultas. Después de reemplazar el documento, se ejecutará la misma consulta para ver el documento modificado.

    await this.CreateFamilyDocumentIfNotExists("FamilyDB_va", "FamilyCollection_va", wakefieldFamily);

    this.ExecuteSimpleQuery("FamilyDB_va", "FamilyCollection_va");

    // ADD THIS PART TO YOUR CODE
    // Update the Grade of the Andersen Family child
    andersenFamily.Children[0].Grade = 6;

    await this.ReplaceFamilyDocument("FamilyDB_va", "FamilyCollection_va", "Andersen.1", andersenFamily);

    this.ExecuteSimpleQuery("FamilyDB_va", "FamilyCollection_va");

Presione **F5** para ejecutar la aplicación.

¡Felicidades! Un documento de DocumentDB se reemplazaron correctamente.

##<a id="DeleteDocument"></a>Paso 9: Eliminar documento JSON

DocumentDB admite la eliminación de JSON de documentos.  

Copie y pegue el método **DeleteFamilyDocument** debajo de su método de **ReplaceFamilyDocument** .

    // ADD THIS PART TO YOUR CODE
    private async Task DeleteFamilyDocument(string databaseName, string collectionName, string documentName)
    {
        try
        {
            await this.client.DeleteDocumentAsync(UriFactory.CreateDocumentUri(databaseName, collectionName, documentName));
            Console.WriteLine("Deleted Family {0}", documentName);
        }
        catch (DocumentClientException de)
        {
            throw;
        }
    }

Copie y pegue el código siguiente en el método **GetStartedDemo** debajo de la segunda ejecución de consulta.

    await this.ReplaceFamilyDocument("FamilyDB_va", "FamilyCollection_va", "Andersen.1", andersenFamily);

    this.ExecuteSimpleQuery("FamilyDB_va", "FamilyCollection_va");

    // ADD THIS PART TO CODE
    await this.DeleteFamilyDocument("FamilyDB_va", "FamilyCollection_va", "Andersen.1");

Presione **F5** para ejecutar la aplicación.

¡Felicidades! Se ha eliminado correctamente un documento de DocumentDB.

##<a id="DeleteDatabase"></a>Paso 10: Eliminar la base de datos

Eliminar la base de datos creada se quitarán la base de datos y todos los recursos de los niños (colecciones, documentos, etcetera).

Copie y pegue el código siguiente en el método **GetStartedDemo** debajo del documento eliminar para eliminar la base de datos y todos los recursos de los niños.

    this.ExecuteSimpleQuery("FamilyDB_va", "FamilyCollection_va");

    await this.DeleteFamilyDocument("FamilyDB_va", "FamilyCollection_va", "Andersen.1");

    // ADD THIS PART TO CODE
    // Clean up/delete the database
    await this.client.DeleteDatabaseAsync(UriFactory.CreateDatabaseUri("FamilyDB_va"));

Presione **F5** para ejecutar la aplicación.

¡Felicidades! Se ha eliminado correctamente una base de datos de DocumentDB.

##<a id="Run"></a>Paso 11: Ejecutar la aplicación C# consola todo!

Presione F5 en Visual Studio para generar la aplicación en modo de depuración.

Debe ver el resultado de la aplicación de introducción de obtener. El resultado mostrará los resultados de las consultas agregamos y debe coincidir con el texto de ejemplo siguiente.

    Created FamilyDB_va
    Press any key to continue ...
    Created FamilyCollection_va
    Press any key to continue ...
    Created Family Andersen.1
    Press any key to continue ...
    Created Family Wakefield.7
    Press any key to continue ...
    Running LINQ query...
        Read {"id":"Andersen.1","LastName":"Andersen","District":"WA5","Parents":[{"FamilyName":null,"FirstName":"Thomas"},{"FamilyName":null,"FirstName":"Mary Kay"}],"Children":[{"FamilyName":null,"FirstName":"Henriette Thaulow","Gender":"female","Grade":5,"Pets":[{"GivenName":"Fluffy"}]}],"Address":{"State":"WA","County":"King","City":"Seattle"},"IsRegistered":true}
    Running direct SQL query...
        Read {"id":"Andersen.1","LastName":"Andersen","District":"WA5","Parents":[{"FamilyName":null,"FirstName":"Thomas"},{"FamilyName":null,"FirstName":"Mary Kay"}],"Children":[{"FamilyName":null,"FirstName":"Henriette Thaulow","Gender":"female","Grade":5,"Pets":[{"GivenName":"Fluffy"}]}],"Address":{"State":"WA","County":"King","City":"Seattle"},"IsRegistered":true}
    Replaced Family Andersen.1
    Press any key to continue ...
    Running LINQ query...
        Read {"id":"Andersen.1","LastName":"Andersen","District":"WA5","Parents":[{"FamilyName":null,"FirstName":"Thomas"},{"FamilyName":null,"FirstName":"Mary Kay"}],"Children":[{"FamilyName":null,"FirstName":"Henriette Thaulow","Gender":"female","Grade":6,"Pets":[{"GivenName":"Fluffy"}]}],"Address":{"State":"WA","County":"King","City":"Seattle"},"IsRegistered":true}
    Running direct SQL query...
        Read {"id":"Andersen.1","LastName":"Andersen","District":"WA5","Parents":[{"FamilyName":null,"FirstName":"Thomas"},{"FamilyName":null,"FirstName":"Mary Kay"}],"Children":[{"FamilyName":null,"FirstName":"Henriette Thaulow","Gender":"female","Grade":6,"Pets":[{"GivenName":"Fluffy"}]}],"Address":{"State":"WA","County":"King","City":"Seattle"},"IsRegistered":true}
    Deleted Family Andersen.1
    End of demo, press any key to exit.

¡Felicidades! Completado este tutorial NoSQL y tiene una aplicación de consola de C# de trabajo!

## <a name="next-steps"></a>Pasos siguientes

- ¿Desea obtener un tutorial ASP.NET MVC NoSQL más complejo? Consulte [crear una aplicación web con ASP.NET MVC mediante DocumentDB](documentdb-dotnet-application.md).
- ¿Desea realizar escala y pruebas de performance con DocumentDB? Consulte [escala pruebas con Azure DocumentDB y rendimiento](documentdb-performance-testing.md)
-   Obtenga información sobre cómo [supervisar una cuenta de DocumentDB](documentdb-monitor-accounts.md).
-   Ejecutar consultas en nuestro conjunto de datos de ejemplo en la [Animación de la consulta](https://www.documentdb.com/sql/demo).
-   Más información sobre el modelo de programación en la sección de desarrollo de la [página de la documentación de DocumentDB](https://azure.microsoft.com/documentation/services/documentdb/).

[documentdb-create-account]: documentdb-create-account.md
[documentdb-manage]: documentdb-manage.md
[keys]: media/documentdb-get-started-quickstart/nosql-tutorial-keys.png

