<properties
    pageTitle="Trabajar con la biblioteca de aplicación de servicio móvil aplicaciones cliente administrado (Windows | Xamarin) | Microsoft Azure"
    description="Obtenga información sobre cómo usar a un cliente .NET para aplicaciones de Azure aplicación servicio Mobile con aplicaciones de Windows y Xamarin."
    services="app-service\mobile"
    documentationCenter=""
    authors="adrianhall"
    manager="erikre"
    editor=""/>

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-multiple"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="10/01/2016"
    ms.author="adrianha"/>

# <a name="how-to-use-the-managed-client-for-azure-mobile-apps"></a>Cómo usar al cliente administrado para aplicaciones móviles de Azure

[AZURE.INCLUDE [app-service-mobile-selector-client-library](../../includes/app-service-mobile-selector-client-library.md)]

##<a name="overview"></a>Información general

Esta guía le muestra cómo llevar a cabo escenarios comunes con la biblioteca de cliente administrado para Azure aplicación del servicio móvil aplicaciones para Windows y aplicaciones de Xamarin. Si no conoce aplicaciones móviles, debe tener en cuenta en primer lugar completar el [Tutorial de aplicaciones de Azure Mobile] [ 1] tutorial. En esta guía nos centrarse en el SDK de cliente administrado. Para obtener más información sobre el SDK de servidor para las aplicaciones móviles, consulte la documentación de [.NET Server SDK] [ 2] o el [SDK de servidor Node.js][3].

## <a name="reference-documentation"></a>Documentación de referencia

La documentación de referencia para el SDK de cliente se encuentra aquí: [referencia de cliente de Azure Mobile aplicaciones .NET][4].
También puede encontrar varios ejemplos de cliente en el [repositorio de Azure muestras GitHub][5].

## <a name="supported-platforms"></a>Plataformas admitidas

La plataforma .NET es compatible con las plataformas siguientes:

* Versiones de Xamarin Android para API 19 y 24 (KitKat a través de nueces)
* IOS Xamarin libera para iOS 8.0 y versiones posteriores
* Plataforma Windows universal
* Windows Phone 8.1
* Windows Phone 8.0 salvo para las aplicaciones de Silverlight

La autenticación de "flujo de servidor" utiliza una vista de Web para la interfaz de usuario presentado.  Si el dispositivo no puede presentar una UI WebView, son necesarios otros métodos de autenticación.  Este SDK, por tanto, no es adecuado para el tipo de inspección o del mismo modo restringidos dispositivos.

##<a name="setup"></a>El programa de instalación y los requisitos previos

Se supone que ya ha creado y publicado el proyecto de back-end de aplicación Mobile, que incluye al menos una tabla.  En el código que se utilizan en este tema, la tabla se denomina `TodoItem` y tiene las siguientes columnas: `Id`, `Text`, y `Complete`. Esta tabla es la misma tabla que creó al completar el [tutorial de aplicaciones de Azure Mobile].

El tipo de cliente escrito correspondiente en C# es la clase siguiente:

    public class TodoItem
    {
        public string Id { get; set; }

        [JsonProperty(PropertyName = "text")]
        public string Text { get; set; }

        [JsonProperty(PropertyName = "complete")]
        public bool Complete { get; set; }
    }

La [JsonPropertyAttribute] [ 6] se utiliza para definir la asignación de *PropertyName* entre el tipo de cliente y la tabla.

Para obtener información sobre cómo crear tablas en el servidor de aplicaciones móviles, vea la información en el [tema de .NET Server SDK] [ 7] o el [tema de Node.js Server SDK][8]. Si ha creado la aplicación móvil de back-end en el portal de Azure con el tutorial, también puede usar la configuración de **fáciles tablas** en el [portal de Azure].

###<a name="how-to-install-the-managed-client-sdk-package"></a>Cómo: instalar el paquete del SDK cliente administrado

Siga uno de los métodos siguientes para instalar el paquete del SDK cliente administrado para aplicaciones móviles de [NuGet][9]:

+ **Visual Studio** Haga clic en su proyecto, haga clic en **Administrar paquetes de NuGet**, busque la `Microsoft.Azure.Mobile.Client` empaquetar, a continuación, haga clic en **instalar**.

+ **Xamarin Studio** Haga clic en su proyecto, haga clic en **Agregar** > **Agregar paquetes de NuGet**, busque la `Microsoft.Azure.Mobile.Client `empaquetar y, a continuación, haga clic en **Agregar paquete**.

En el archivo de actividad principal, no olvide agregar la siguiente instrucción **using** :

    using Microsoft.WindowsAzure.MobileServices;

###<a name="symbolsource"></a>Cómo: trabajar con símbolos de depuración en Visual Studio

Los símbolos para el espacio de nombres Microsoft.Azure.Mobile están disponibles en [SymbolSource][10].  Consulte las [instrucciones de SymbolSource] [ 11] integrar SymbolSource con Visual Studio.

##<a name="create-client"></a>Crear al cliente de aplicaciones móviles

El código siguiente crea la [MobileServiceClient] [ 12] objeto que se utiliza para tener acceso a la aplicación móvil de back-end.

    var client = new MobileServiceClient("MOBILE_APP_URL");

En el código anterior, reemplace `MOBILE_APP_URL` con la dirección URL de la aplicación móvil de back-end, que se encuentra en el módulo de la aplicación móvil de back-end en el [portal de Azure]. El objeto MobileServiceClient debe ser un singleton.

## <a name="work-with-tables"></a>Trabajar con tablas

En la sección siguiente detalla cómo buscar y recuperar registros y modificar los datos de la tabla.  Se tratan los siguientes temas:

* [Crear una referencia de tabla](#instantiating)
* [Consulta de datos](#querying)
* [Filtrar los datos devueltos](#filtering)
* [Ordenar los datos devueltos](#sorting)
* [Devolver datos en las páginas](#paging)
* [Seleccionar columnas específicas](#selecting)
* [Buscar un registro por Id.](#lookingup)
* [Cómo tratar las consultas sin tipo](#untypedqueries)
* [Insertar datos](#inserting)
* [Actualización de datos](#updating)
* [Eliminar datos](#deleting)
* [Resolución de conflictos y simultaneidad optimista](#optimisticconcurrency)
* [Enlace a una interfaz de usuario de Windows](#binding)
* [Cambiar el tamaño de página](#pagesize)

###<a name="instantiating"></a>Cómo: crear una referencia de tabla

Todo el código que tenga acceso o modifique los datos en una tabla de back-end a llamadas de funciones en el `MobileServiceTable` objeto. Obtener una referencia a la tabla llamando el método [GetTable] , como sigue:

    IMobileServiceTable<TodoItem> todoTable = client.GetTable<TodoItem>();

El objeto devuelto utiliza el modelo de serialización escrito. Un modelo de serialización sin tipo también es compatible. El siguiente ejemplo se [crea una referencia a una tabla sin tipo]:

    // Get an untyped table reference
    IMobileServiceTable untypedTodoTable = client.GetTable("TodoItem");

En consultas sin tipo, especifique la cadena de consulta subyacente de OData.

###<a name="querying"></a>Cómo: consultar los datos de la aplicación móvil

Esta sección describe cómo realizar consultas en el servidor de la aplicación móvil, que incluye las siguientes funciones:

- [Filtrar los datos devueltos](#filtering)
- [Ordenar los datos devueltos](#sorting)
- [Devolver datos en las páginas](#paging)
- [Seleccionar columnas específicas](#selecting)
- [Buscar datos por Id.](#lookingup)

>[AZURE.NOTE]Se aplica un tamaño de página condicionada por el servidor para evitar que todas las filas que se devuelven.  Paginación mantiene solicitudes de forma predeterminada para grandes conjuntos de datos desde un impacto negativo en el servicio.  Para obtener más de 50 filas, use la `Skip` y `Take` método, como se describe en [devolver datos en páginas].

###<a name="filtering"></a>Cómo: filtrar los datos devueltos

El código siguiente muestra cómo filtrar los datos, incluyendo una `Where` cláusula en una consulta. Devuelve todos los elementos de `todoTable` cuyo `Complete` propiedad es igual a `false`. La función [donde] aplica una fila filtrado predicado de la consulta en la tabla.

    // This query filters out completed TodoItems and items without a timestamp.
    List<TodoItem> items = await todoTable
       .Where(todoItem => todoItem.Complete == false)
       .ToListAsync();

Puede ver el URI de la solicitud enviada del back-end mediante software de inspección de mensaje, como las herramientas de desarrollador de explorador o [Fiddler]. Si mira el URI de la solicitud, observe que se ha modificado la cadena de consulta:

    GET /tables/todoitem?$filter=(complete+eq+false) HTTP/1.1

Esta solicitud de OData se convierte en una consulta SQL en el SDK de servidor:

    SELECT *
    FROM TodoItem
    WHERE ISNULL(complete, 0) = 0

La función que se pasa a la `Where` método puede tener un número arbitrario de condiciones.

    // This query filters out completed TodoItems where Text isn't null
    List<TodoItem> items = await todoTable
       .Where(todoItem => todoItem.Complete == false && todoItem.Text != null)
       .ToListAsync();

En este ejemplo se traducir una consulta SQL el SDK de servidor:

    SELECT *
    FROM TodoItem
    WHERE ISNULL(complete, 0) = 0
          AND ISNULL(text, 0) = 0

Esta consulta también se puede dividir en varias cláusulas:

    List<TodoItem> items = await todoTable
       .Where(todoItem => todoItem.Complete == false)
       .Where(todoItem => todoItem.Text != null)
       .ToListAsync();

Los dos métodos son equivalentes y pueden utilizarse indistintamente.  La primera opción&mdash;de concatenar varios predicados en una consulta&mdash;es más compacta y recomendados.

La `Where` cláusula es compatible con las operaciones que se pueden traducir en el subconjunto de OData. Las operaciones incluyen:

* Operadores relacionales (==,! = <, < =, >, > =),
* Operadores aritméticos (+, -, /, *, %),
* Número de precisión (Math.Floor, Math.Ceiling)
* Funciones de cadena (longitud, subcadena, reemplazar, IndexOf, StartsWith, EndsWith),
* Propiedades de fecha (año, mes, día, hora, minuto, segundo)
* Acceso a las propiedades de un objeto, y
* Expresiones de combinación de cualquiera de estas operaciones.

Cuando se plantee qué es compatible con el SDK de servidor, puede considerar la [documentación de OData v3].

###<a name="sorting"></a>Cómo: ordenar datos devueltos

El código siguiente muestra cómo ordenar los datos, incluyendo una función [OrderBy] o [OrderByDescending] en la consulta. Devuelve elementos de `todoTable` orden ascendente por el `Text` campo.

    // Sort items in ascending order by Text field
    MobileServiceTableQuery<TodoItem> query = todoTable
                    .OrderBy(todoItem => todoItem.Text)
    List<TodoItem> items = await query.ToListAsync();

    // Sort items in descending order by Text field
    MobileServiceTableQuery<TodoItem> query = todoTable
                    .OrderByDescending(todoItem => todoItem.Text)
    List<TodoItem> items = await query.ToListAsync();

###<a name="paging"></a>Cómo: devolver datos en las páginas

De forma predeterminada, el servidor devuelve solo las primeras 50 filas. Puede aumentar el número de filas devueltos llamando al método [tomar] . Usar `Take` junto con el método [Saltar] para solicitar un determinado "página" del conjunto de datos total devuelto por la consulta. Cuando se ejecuta, la siguiente consulta devuelve los elementos de tres principales de la tabla.

    // Define a filtered query that returns the top 3 items.
    MobileServiceTableQuery<TodoItem> query = todoTable
                    .Take(3);
    List<TodoItem> items = await query.ToListAsync();

La siguiente consulta revisada omite los primeros tres resultados y devuelve los resultados de tres. Esta consulta genera la segunda "página" de datos, donde el tamaño de página es tres elementos.

    // Define a filtered query that skips the top 3 items and returns the next 3 items.
    MobileServiceTableQuery<TodoItem> query = todoTable
                    .Skip(3)
                    .Take(3);
    List<TodoItem> items = await query.ToListAsync();

El método de [IncludeTotalCount] solicita el recuento total de _todos_ los registros que se habría devuelto, omitiendo cualquier cláusula o límite de paginación especificado:

    query = query.IncludeTotalCount();

En una aplicación del mundo real, puede usar consultas similares al ejemplo anterior con un control de paginación o UI comparable para desplazarse entre las páginas.

>[AZURE.NOTE]Para reemplazar el límite de 50 filas en un aplicación móvil de back-end, también debe aplicar la [EnableQueryAttribute] al método GET público y especificar el comportamiento de paginación. Cuando se aplica al método, Establece el máximo devuelven filas a 1000 lo siguiente:
>
>    [EnableQuery(MaxTop=1000)]

### <a name="selecting"></a>Cómo: seleccionar columnas específicas

Puede especificar el conjunto de propiedades que desea incluir en los resultados agregando una cláusula [Select] a la consulta. Por ejemplo, el código siguiente muestra cómo seleccionar un solo campo y también cómo seleccionar y aplicar formato a varios campos:

    // Select one field -- just the Text
    MobileServiceTableQuery<TodoItem> query = todoTable
                    .Select(todoItem => todoItem.Text);
    List<string> items = await query.ToListAsync();

    // Select multiple fields -- both Complete and Text info
    MobileServiceTableQuery<TodoItem> query = todoTable
                    .Select(todoItem => string.Format("{0} -- {1}",
                        todoItem.Text.PadRight(30), todoItem.Complete ?
                        "Now complete!" : "Incomplete!"));
    List<string> items = await query.ToListAsync();

Todas las funciones descritas hasta ahora son aditivos, por lo que nos podemos mantener encadenar de ellos. Cada llamada encadenado afecta a más de la consulta. Un ejemplo más:

    MobileServiceTableQuery<TodoItem> query = todoTable
                    .Where(todoItem => todoItem.Complete == false)
                    .Select(todoItem => todoItem.Text)
                    .Skip(3).
                    .Take(3);
    List<string> items = await query.ToListAsync();

### <a name="lookingup"></a>Cómo: buscar datos por Id.

La función [LookupAsync] puede usarse para buscar objetos de la base de datos con un identificador determinado.

    // This query filters out the item with the ID of 37BBF396-11F0-4B39-85C8-B319C729AF6D
    TodoItem item = await todoTable.LookupAsync("37BBF396-11F0-4B39-85C8-B319C729AF6D");

### <a name="untypedqueries"></a>Cómo: ejecutar consultas sin tipo

Cuando se ejecuta una consulta con un objeto de tabla sin tipo, especifique explícitamente la cadena de consulta de OData mediante una llamada a [ReadAsync], como en el ejemplo siguiente:

    // Lookup untyped data using OData
    JToken untypedItems = await untypedTodoTable.ReadAsync("$filter=complete eq 0&$orderby=text");

Volver valores JSON que puede utilizar como un contenedor de propiedades. Para obtener más información sobre JToken y Newtonsoft Json.NET, consulte el sitio [Json.NET] .

### <a name="inserting"></a>Cómo: insertar datos en un aplicación móvil de back-end

Todos los tipos de cliente deben contener a un miembro denominado **identificador**, que es una cadena de forma predeterminada. Este **Id** es necesaria para realizar operaciones CRUD y de la sincronización sin conexión. El código siguiente muestra cómo usar el método [InsertAsync] para insertar nuevas filas en una tabla. El parámetro contiene los datos que se inserta como un objeto de .NET.

    await todoTable.InsertAsync(todoItem);

Si un valor de identificador único personalizado no se incluye en el `todoItem` durante la inserción, se genera un GUID por el servidor.
Puede recuperar el identificador generado mediante la inspección el objeto después de la llamada.

Para insertar datos sin tipo, puede aprovechar las ventajas de Json.NET:

    JObject jo = new JObject();
    jo.Add("Text", "Hello World");
    jo.Add("Complete", false);
    var inserted = await table.InsertAsync(jo);

Aquí tiene un ejemplo con una dirección de correo electrónico como una única cadena id:

    JObject jo = new JObject();
    jo.Add("id", "myemail@emaildomain.com");
    jo.Add("Text", "Hello World");
    jo.Add("Complete", false);
    var inserted = await table.InsertAsync(jo);

### <a name="working-with-id-values"></a>Trabajar con valores de Id.

Aplicaciones de Mobile es compatible con valores de cadena personalizados únicos para **identificador de** columna de la tabla. Un valor de cadena permite a las aplicaciones usar valores personalizados como direcciones de correo electrónico o nombres de usuario para el Id.  Identificadores de cadena proporcionan las siguientes ventajas:

* Se generan identificadores sin realizar un recorrido en la base de datos.
* Registros son más fáciles de combinación a partir de diferentes tablas o bases de datos.
* Valores de identificadores pueden integrar mejor con lógica de la aplicación.

Cuando no se establece un valor de Id. de cadena en un registro insertado, la aplicación móvil de back-end genera un valor único para el Id. Puede usar el método [Guid.NewGuid] para generar sus propios valores de Id., en el cliente o en el servidor.

    JObject jo = new JObject();
    jo.Add("id", Guid.NewGuid().ToString("N"));

###<a name="modifying"></a>Cómo: modificar datos en un aplicación móvil de back-end

El código siguiente muestra cómo usar el método [UpdateAsync] para actualizar un registro existente con el mismo ID con información nueva. El parámetro contiene los datos que se actualicen como un objeto de .NET.

    await todoTable.UpdateAsync(todoItem);

Para actualizar los datos sin tipo, puede aprovechar [Json.NET] como sigue:

    JObject jo = new JObject();
    jo.Add("id", "37BBF396-11F0-4B39-85C8-B319C729AF6D");
    jo.Add("Text", "Hello World");
    jo.Add("Complete", false);
    var inserted = await table.UpdateAsync(jo);

Un `id` campo debe especificarse al realizar una actualización. El back-end utiliza la `id` campo para identificar qué fila Actualizar. La `id` campo puede obtenerse el resultado de la `InsertAsync` de llamadas. Un `ArgumentException` se produce si intenta actualizar un elemento sin proporcionar la `id` valor.

###<a name="deleting"></a>Cómo: eliminar datos en un aplicación móvil de back-end

El código siguiente muestra cómo usar el método [DeleteAsync] para eliminar una instancia existente. La instancia se identifica por la `id` conjunto de campos en la `todoItem`.

    await todoTable.DeleteAsync(todoItem);

Para eliminar datos sin tipo, puede aprovechar Json.NET como sigue:

    JObject jo = new JObject();
    jo.Add("id", "37BBF396-11F0-4B39-85C8-B319C729AF6D");
    await table.DeleteAsync(jo);

Al realizar una solicitud de eliminar, debe especificar un ID. Otras propiedades no se pasan al servicio o se pasan por alto en el servicio. El resultado de un `DeleteAsync` suele ser llamada `null`. El identificador de pasar puede obtenerse el resultado de la `InsertAsync` de llamadas. A `MobileServiceInvalidOperationException` se produce cuando intenta eliminar un elemento sin especificar la `id` campo.

###<a name="optimisticconcurrency"></a>Cómo: usar simultaneidad optimista de resolución de conflictos

Dos o más clientes pueden escribir cambios en el mismo elemento al mismo tiempo. Sin detección de conflictos, la última escritura sobrescribirá cualquier actualización anterior. **Control de simultaneidad optimista** supone que cada transacción puede confirmar y, por tanto, no utiliza el bloqueo de los recursos.  Antes de confirmar una transacción, control de simultaneidad optimista comprueba que ninguna otra transacción ha modificado los datos. Si los datos se ha modificado, se deshace la transacción de confirmación.

Aplicaciones de Mobile es compatible con el control de simultaneidad optimista seguimiento de cambios de cada elemento con el `version` columna de propiedad de sistema que se define para cada tabla en la aplicación móvil de back-end. Conjuntos de aplicaciones móviles de cada vez que se actualiza un registro, el `version` propiedad para dicho registro para un nuevo valor. Durante cada solicitud de actualización, la `version` propiedad del registro que se incluye con la solicitud se compara con la misma propiedad para el registro en el servidor. Si la versión pasa con la solicitud no coincide con el back-end, a continuación, la biblioteca de cliente provoca un `MobileServicePreconditionFailedException<T>` excepción. El tipo incluido con la excepción es el registro del servidor que contiene la versión de servidores del registro. La aplicación, a continuación, puede usar esta información para decidir si desea ejecutar la solicitud de actualización de nuevo con la correcta `version` valor del servidor para confirmar los cambios.

Definir una columna de la clase de tabla para la `version` propiedad del sistema para habilitar la simultaneidad optimista. Por ejemplo:

    public class TodoItem
    {
        public string Id { get; set; }

        [JsonProperty(PropertyName = "text")]
        public string Text { get; set; }

        [JsonProperty(PropertyName = "complete")]
        public bool Complete { get; set; }

        // *** Enable Optimistic Concurrency *** //
        [JsonProperty(PropertyName = "version")]
        public string Version { set; get; }
    }


Habilitar la aplicaciones mediante tablas sin tipo simultaneidad optimista estableciendo la `Version` marcar en la `SystemProperties` de la tabla siguiente.

    //Enable optimistic concurrency by retrieving version
    todoTable.SystemProperties |= MobileServiceSystemProperties.Version;

Además de habilitar simultaneidad optimista, se deben detectar el `MobileServicePreconditionFailedException<T>` excepción en el código cuando llama [UpdateAsync].  Resolver el conflicto aplicando el correcto `version` al registro actualizado y llamada [UpdateAsync] con el registro que se ha resuelto. El código siguiente muestra cómo resolver un conflicto de escritura una vez detectado:

    private async void UpdateToDoItem(TodoItem item)
    {
        MobileServicePreconditionFailedException<TodoItem> exception = null;

        try
        {
            //update at the remote table
            await todoTable.UpdateAsync(item);
        }
        catch (MobileServicePreconditionFailedException<TodoItem> writeException)
        {
            exception = writeException;
        }

        if (exception != null)
        {
            // Conflict detected, the item has changed since the last query
            // Resolve the conflict between the local and server item
            await ResolveConflict(item, exception.Item);
        }
    }


    private async Task ResolveConflict(TodoItem localItem, TodoItem serverItem)
    {
        //Ask user to choose the resoltion between versions
        MessageDialog msgDialog = new MessageDialog(
            String.Format("Server Text: \"{0}\" \nLocal Text: \"{1}\"\n",
            serverItem.Text, localItem.Text),
            "CONFLICT DETECTED - Select a resolution:");

        UICommand localBtn = new UICommand("Commit Local Text");
        UICommand ServerBtn = new UICommand("Leave Server Text");
        msgDialog.Commands.Add(localBtn);
        msgDialog.Commands.Add(ServerBtn);

        localBtn.Invoked = async (IUICommand command) =>
        {
            // To resolve the conflict, update the version of the item being committed. Otherwise, you will keep
            // catching a MobileServicePreConditionFailedException.
            localItem.Version = serverItem.Version;

            // Updating recursively here just in case another change happened while the user was making a decision
            UpdateToDoItem(localItem);
        };

        ServerBtn.Invoked = async (IUICommand command) =>
        {
            RefreshTodoItems();
        };

        await msgDialog.ShowAsync();
    }

Para obtener más información, vea el tema de la [Sincronización de datos sin conexión en las aplicaciones móviles de Azure] .

###<a name="binding"></a>Cómo: datos de aplicaciones móviles de enlace a una interfaz de usuario de Windows

Esta sección muestra cómo se muestran los objetos de datos devueltos mediante elementos de interfaz de usuario en una aplicación de Windows.  Ejemplo de código siguiente enlaza al origen de la lista con una consulta para los elementos incompletos. El [MobileServiceCollection] crea una colección de enlace aplicaciones cuenta móvil.

    // This query filters out completed TodoItems.
    MobileServiceCollection<TodoItem, TodoItem> items = await todoTable
        .Where(todoItem => todoItem.Complete == false)
        .ToCollectionAsync();

    // itemsControl is an IEnumerable that could be bound to a UI list control
    IEnumerable itemsControl  = items;

    // Bind this to a ListBox
    ListBox lb = new ListBox();
    lb.ItemsSource = items;

Algunos controles en tiempo de ejecución administrado admiten una interfaz denominada [ISupportIncrementalLoading]. Esta interfaz permite que los controles solicitar datos adicionales cuando el usuario se desplaza. No hay compatibilidad integrada para esta interfaz para las aplicaciones de Windows universales a través de [MobileServiceIncrementalLoadingCollection], que controla automáticamente las llamadas de los controles. Usar `MobileServiceIncrementalLoadingCollection` en las aplicaciones de Windows como sigue:

    MobileServiceIncrementalLoadingCollection<TodoItem,TodoItem> items;
    items = todoTable.Where(todoItem => todoItem.Complete == false).ToIncrementalLoadingCollection();

    ListBox lb = new ListBox();
    lb.ItemsSource = items;

Para usar la nueva colección en aplicaciones de Windows Phone 8 y "Silverlight", use la `ToCollection` métodos de extensión en `IMobileServiceTableQuery<T>` y `IMobileServiceTable<T>`. Para cargar los datos, llame a `LoadMoreItemsAsync()`.

    MobileServiceCollection<TodoItem, TodoItem> items = todoTable.Where(todoItem => todoItem.Complete==false).ToCollection();
    await items.LoadMoreItemsAsync();

Cuando se utiliza la colección creada mediante una llamada a `ToCollectionAsync` o `ToCollection`, obtendrá una colección que puede estar enlazada con controles de la interfaz de usuario.  Esta colección es compatible con paginación.  Puesto que la colección de carga los datos de la red, cargar a veces se produce un error. Para controlar esos errores, reemplace la `OnException` método en `MobileServiceIncrementalLoadingCollection` para controlar las excepciones resultantes de las llamadas a `LoadMoreItemsAsync`.

Tenga en cuenta si la tabla tiene muchos campos, pero desea mostrar algunos de ellos en el control. Puede utilizar las instrucciones en la sección anterior "[Seleccionar columnas específicas](#selecting)" para seleccionar columnas específicas para mostrar en la interfaz de usuario.

###<a name="pagesize"></a>Cambiar el tamaño de página

Aplicaciones de Azure Mobile devuelve un máximo de 50 elementos por cada solicitud de forma predeterminada.  Puede cambiar el tamaño de paginación aumentando el tamaño máximo de página en el servidor y de cliente.  Para aumentar el tamaño de página solicitado, especifique `PullOptions` al usar `PullAsync()`:

    PullOptions pullOptions = new PullOptions
        {
            MaxPageSize = 100
        };

Suponiendo que ha realizado la `PageSize` igual o mayor que 100 dentro del servidor, una solicitud devuelve los elementos hasta 100.

##<a name="#offlinesync"></a>Trabajar con tablas sin conexión

Tablas sin conexión utilizan un local de SQLite al almacén de datos para su uso sin conexión.  Tabla de todas las operaciones que se realizan en el equipo local SQLite almacenar en lugar del almacén de servidor remoto.  Para crear una tabla sin conexión, preparar primero el proyecto:

1. En Visual Studio, haga clic en la solución > **Administrar paquetes de NuGet para solución...**, a continuación, buscar e instalar el paquete de NuGet **Microsoft.Azure.Mobile.Client.SQLiteStore** para todos los proyectos de la solución.

2. (Opcional) Para admitir dispositivos de Windows, instalar uno de los paquetes de tiempo de ejecución de SQLite siguientes:

    * **En tiempo de ejecución de Windows 8.1:** Instalar [SQLite para Windows 8.1][3].
    * **Windows Phone 8.1:** Instalar [SQLite para Windows Phone 8.1][4].
    * **Plataforma Windows universal** Instalar [SQLite para Universal de Windows Universal][5].

3. (Opcional). Para los dispositivos de Windows, haga clic en **referencias** > **Agregar referencia**, expanda la carpeta de **Windows** > **extensiones**y, a continuación, habilitar las adecuadas **SQLite para Windows** SDK junto con el SDK de **2013 Runtime para Windows de Visual C++** .
    Los nombres de SQLite SDK varían ligeramente con cada plataforma de Windows.

Para poder crear una referencia de tabla, debe estar preparado el almacén local:

    var store = new MobileServiceSQLiteStore(Constants.OfflineDbPath);
    store.DefineTable<TodoItem>();

    //Initializes the SyncContext using the default IMobileServiceSyncHandler.
    await this.client.SyncContext.InitializeAsync(store);

Normalmente, se realiza la inicialización del almacén inmediatamente después de crea el cliente.  El **OfflineDbPath** debe ser un nombre de archivo adecuado para su uso en todas las plataformas compatibles.  Si la ruta de acceso es una ruta de acceso completa (es decir, empieza con una barra diagonal), se utiliza la ruta de acceso.  Si la ruta de acceso no está completo, el archivo se coloca en una ubicación específica de la plataforma.

* Para dispositivos iOS y Android, la ruta de acceso predeterminada es la carpeta "Archivos personales".
* Para dispositivos de Windows, la ruta de acceso predeterminada es la carpeta de "AppData" específicos de la aplicación.

Puede obtener una referencia de tabla mediante la `GetSyncTable<>` método:

    var table = client.GetSyncTable<TodoItem>();

No es necesario autenticar para usar una tabla sin conexión.  Solo debe autenticar cuando se comunica con el servicio de back-end.

###<a name="syncoffline"></a>Sincronización de una tabla sin conexión

Tablas sin conexión no se sincronizan con el servidor de forma predeterminada.  La sincronización se divide en dos partes.  Puede aplicar cambios por separado descarguen nuevos elementos.  Aquí es un método de sincronización típica:

    public async Task SyncAsync()
    {
        ReadOnlyCollection<MobileServiceTableOperationError> syncErrors = null;

        try
        {
            await this.client.SyncContext.PushAsync();

            await this.todoTable.PullAsync(
                //The first parameter is a query name that is used internally by the client SDK to implement incremental sync.
                //Use a different query name for each unique query in your program
                "allTodoItems",
                this.todoTable.CreateQuery());
        }
        catch (MobileServicePushFailedException exc)
        {
            if (exc.PushResult != null)
            {
                syncErrors = exc.PushResult.Errors;
            }
        }

        // Simple error/conflict handling. A real application would handle the various errors like network conditions,
        // server conflicts and others via the IMobileServiceSyncHandler.
        if (syncErrors != null)
        {
            foreach (var error in syncErrors)
            {
                if (error.OperationKind == MobileServiceTableOperationKind.Update && error.Result != null)
                {
                    //Update failed, reverting to server's copy.
                    await error.CancelAndUpdateItemAsync(error.Result);
                }
                else
                {
                    // Discard local change.
                    await error.CancelAndDiscardItemAsync();
                }

                Debug.WriteLine(@"Error executing sync operation. Item: {0} ({1}). Operation discarded.", error.TableName, error.Item["id"]);
            }
        }
    }

Si el primer argumento `PullAsync` es null, no se utiliza la sincronización incremental.  Cada operación de sincronización recupera todos los registros.

El SDK realiza una implícitos `PushAsync()` antes de la extracción de registros.

Control de conflictos sucede en un `PullAsync()` método.  Puede ocuparse de conflictos en la misma manera que las tablas en línea.  El conflicto se produce cuando `PullAsync()` se llama en lugar de durante la Insertar, actualizar o eliminar. Si se producen varios conflictos, se incorporará una sola MobileServicePushFailedException.  Administrar cada error por separado.

##<a name="#customapi"></a>Trabajar con una API personalizada

Una API personalizada le permite definir extremos personalizados que exponen la funcionalidad del servidor que no se asignan a una instrucción insert, actualizar, eliminar o la operación de lectura. Mediante una API personalizada, puede tener más control sobre la mensajería incluidos leer y establecer encabezados del mensaje HTTP y definir un formato de cuerpo de mensaje que no sea JSON.

Para llamar a una API personalizada al llamar a uno de los métodos de [InvokeApiAsync] en el cliente. Por ejemplo, la siguiente línea de código envía una solicitud de entrada a la **completeAll** API en el servidor:

    var result = await client.InvokeApiAsync<MarkAllResult>("completeAll", System.Net.Http.HttpMethod.Post, null);

Este formulario es una llamada de método escrito y requiere que se define el tipo de valor devuelto de **MarkAllResult** . Se admiten métodos con y sin tipo.

##<a name="authentication"></a>Autenticación de usuarios

Aplicaciones de Mobile admite autenticación y autorización de los usuarios de la aplicación con varios proveedores de identidades externo: Azure Active Directory, Google, Microsoft Account, Twitter y Facebook. Puede establecer permisos en las tablas para restringir el acceso para operaciones específicas a solo los usuarios autenticados. También puede usar la identidad de los usuarios autenticados para implementar reglas de autorización de secuencias de comandos del servidor. Para obtener más información, consulte el tutorial [autenticación de agregar a la aplicación].

Se admiten dos flujos de autenticación: flujo _administrado por el cliente_ y _administradas por el servidor_ . El flujo administrados por el servidor proporciona la experiencia de autenticación más sencilla, como se basa en la interfaz del proveedor web autenticación. El flujo administrado por el cliente permite una mayor integración con capacidades específicas del dispositivo como se basa en el SDK de específicas del proveedor específica del dispositivo.

>[AZURE.NOTE] Se recomienda utilizar un flujo de cliente administrados en las aplicaciones de producción.

Para configurar la autenticación, debe registrar la aplicación con uno o varios proveedores de identidades.  El proveedor de identidades genera un identificador de cliente y un secreto de cliente para la aplicación.  Estos valores se establecen a continuación, en el servidor para habilitar la autenticación/autorización de servicio de aplicaciones de Azure.  Para obtener más información, siga las instrucciones detalladas en el tutorial [autenticación de agregar a la aplicación].

En esta sección se tratan los siguientes temas:

+ [Autenticación administradas por el cliente](#clientflow)
+ [Autenticación de servidor administrados](#serverflow)
+ [Almacenamiento en caché el símbolo de autenticación](#caching)

###<a name="clientflow"></a>Autenticación administradas por el cliente

La aplicación puede independientemente póngase en contacto con el proveedor de identidades y proporcione el token devuelto durante el inicio de sesión con el servidor. Este flujo de cliente le permite proporcionar una experiencia de inicio de sesión único para los usuarios o para recuperar datos de usuario adicionales desde el proveedor de identidades. Autenticación de flujo de cliente es preferida mediante un flujo de servidor como el proveedor de identidades SDK proporciona una apariencia más nativo de UX y permite personalizar adicional.

Se proporcionan ejemplos de los modelos de autenticación de flujo de cliente siguientes:

+ [Active Directory Authentication Library](#adal)
+ [Facebook o Google](#client-facebook)
+ [Live SDK](#client-livesdk)

#### <a name="adal"></a>Autenticar a los usuarios con el Active Directory Authentication Library

Puede usar la biblioteca de autenticación de Active Directory (ADAL) para la autenticación de usuario inicio desde el cliente mediante la autenticación de Azure Active Directory.

1. Configurar la aplicación móvil de back-end para el inicio de sesión de AAD siguiendo el tutorial de [cómo configurar la aplicación de servicio de inicio de sesión de Active Directory] . Asegúrese de completar el paso opcional de registrar una aplicación nativa de cliente.
2. En Visual Studio o Xamarin Studio, abra el proyecto y agregue una referencia a la `Microsoft.IdentityModel.CLients.ActiveDirectory` paquete NuGet. Al buscar, incluir versiones preliminares.
3. Agregue el código siguiente a la aplicación, según la plataforma que está utilizando. En cada uno, realice las siguientes sustituciones:

    * Reemplazar **Aquí de autoridad de INSERCIÓN** con el nombre del inquilino en el que se aprovisione la aplicación. El formato debe ser https://login.windows.net/contoso.onmicrosoft.com. Este valor se puede copiar en la ficha de dominio de Azure Active Directory en el [portal de clásico de Azure].
    * Reemplazar **Insertar recurso ID aquí** con el ID de cliente para la aplicación móvil de back-end. Puede obtener el identificador de cliente de la ficha **Opciones avanzadas** , en **Configuración de Azure Active Directory** en el portal.
    * Reemplazar **Insertar cliente ID aquí** con el ID de cliente que ha copiado de la aplicación cliente nativa.
    * Reemplazar **Insertar REDIRECCIÓN URI aquí** con extremo de _/.auth/login/done_ de su sitio, mediante la combinación de HTTPS. Este valor debe ser similar a _https://contoso.azurewebsites.net/.auth/login/done_.

    El código necesario para cada plataforma sigue:

    **Windows:**

        private MobileServiceUser user;
        private async Task AuthenticateAsync()
        {
            string authority = "INSERT-AUTHORITY-HERE";
            string resourceId = "INSERT-RESOURCE-ID-HERE";
            string clientId = "INSERT-CLIENT-ID-HERE";
            string redirectUri = "INSERT-REDIRECT-URI-HERE";
            while (user == null)
            {
                string message;
                try
                {
                    AuthenticationContext ac = new AuthenticationContext(authority);
                    AuthenticationResult ar = await ac.AcquireTokenAsync(resourceId, clientId,
                        new Uri(redirectUri), new PlatformParameters(PromptBehavior.Auto, false) );
                    JObject payload = new JObject();
                    payload["access_token"] = ar.AccessToken;
                    user = await App.MobileService.LoginAsync(
                        MobileServiceAuthenticationProvider.WindowsAzureActiveDirectory, payload);
                    message = string.Format("You are now logged in - {0}", user.UserId);
                }
                catch (InvalidOperationException)
                {
                    message = "You must log in. Login Required";
                }
                var dialog = new MessageDialog(message);
                dialog.Commands.Add(new UICommand("OK"));
                await dialog.ShowAsync();
            }
        }

    **Xamarin.iOS**

        private MobileServiceUser user;
        private async Task AuthenticateAsync(UIViewController view)
        {
            string authority = "INSERT-AUTHORITY-HERE";
            string resourceId = "INSERT-RESOURCE-ID-HERE";
            string clientId = "INSERT-CLIENT-ID-HERE";
            string redirectUri = "INSERT-REDIRECT-URI-HERE";
            try
            {
                AuthenticationContext ac = new AuthenticationContext(authority);
                AuthenticationResult ar = await ac.AcquireTokenAsync(resourceId, clientId,
                    new Uri(redirectUri), new PlatformParameters(view));
                JObject payload = new JObject();
                payload["access_token"] = ar.AccessToken;
                user = await client.LoginAsync(
                    MobileServiceAuthenticationProvider.WindowsAzureActiveDirectory, payload);
            }
            catch (Exception ex)
            {
                Console.Error.WriteLine(@"ERROR - AUTHENTICATION FAILED {0}", ex.Message);
            }
        }

    **Xamarin.Android**

        private MobileServiceUser user;
        private async Task AuthenticateAsync()
        {
            string authority = "INSERT-AUTHORITY-HERE";
            string resourceId = "INSERT-RESOURCE-ID-HERE";
            string clientId = "INSERT-CLIENT-ID-HERE";
            string redirectUri = "INSERT-REDIRECT-URI-HERE";
            try
            {
                AuthenticationContext ac = new AuthenticationContext(authority);
                AuthenticationResult ar = await ac.AcquireTokenAsync(resourceId, clientId,
                    new Uri(redirectUri), new PlatformParameters(this));
                JObject payload = new JObject();
                payload["access_token"] = ar.AccessToken;
                user = await client.LoginAsync(
                    MobileServiceAuthenticationProvider.WindowsAzureActiveDirectory, payload);
            }
            catch (Exception ex)
            {
                AlertDialog.Builder builder = new AlertDialog.Builder(this);
                builder.SetMessage(ex.Message);
                builder.SetTitle("You must log in. Login Required");
                builder.Create().Show();
            }
        }
        protected override void OnActivityResult(int requestCode, Result resultCode, Intent data)
        {
            base.OnActivityResult(requestCode, resultCode, data);
            AuthenticationAgentContinuationHelper.SetAuthenticationAgentContinuationEventArgs(requestCode, resultCode, data);
        }

####<a name="client-facebook"></a>Inicio de sesión único con un símbolo de Facebook o Google

Puede usar el flujo de cliente, como se muestra en este fragmento de código de Facebook o Google.

    var token = new JObject();
    // Replace access_token_value with actual value of your access token obtained
    // using the Facebook or Google SDK.
    token.Add("access_token", "access_token_value");

    private MobileServiceUser user;
    private async Task AuthenticateAsync()
    {
        while (user == null)
        {
            string message;
            try
            {
                // Change MobileServiceAuthenticationProvider.Facebook
                // to MobileServiceAuthenticationProvider.Google if using Google auth.
                user = await client.LoginAsync(MobileServiceAuthenticationProvider.Facebook, token);
                message = string.Format("You are now logged in - {0}", user.UserId);
            }
            catch (InvalidOperationException)
            {
                message = "You must log in. Login Required";
            }

            var dialog = new MessageDialog(message);
            dialog.Commands.Add(new UICommand("OK"));
            await dialog.ShowAsync();
        }
    }

####<a name="client-livesdk"></a>Solo inicio de sesión con Account de Microsoft con el SDK de Live

Para autenticar usuarios, debe registrar la aplicación en el Centro para desarrolladores de la cuenta de Microsoft. Configurar los detalles de registro en la aplicación móvil de back-end. Para crear un registro de cuenta de Microsoft y conéctela a la aplicación móvil de back-end, complete los pasos de [registrar su aplicación para usar un inicio de sesión de cuenta de Microsoft]. Si tiene las versiones de la tienda Windows y Windows Phone 8/Silverlight de la aplicación, registre primero la versión de la tienda Windows.

El siguiente código autentica utilizando Live SDK y utiliza el token devuelto para iniciar sesión en la aplicación móvil de back-end.

    private LiveConnectSession session;
    //private static string clientId = "<microsoft-account-client-id>";
    private async System.Threading.Tasks.Task AuthenticateAsync()
    {

        // Get the URL the Mobile App backend.
        var serviceUrl = App.MobileService.ApplicationUri.AbsoluteUri;

        // Create the authentication client for Windows Store using the service URL.
        LiveAuthClient liveIdClient = new LiveAuthClient(serviceUrl);
        //// Create the authentication client for Windows Phone using the client ID of the registration.
        //LiveAuthClient liveIdClient = new LiveAuthClient(clientId);

        while (session == null)
        {
            // Request the authentication token from the Live authentication service.
            // The wl.basic scope should always be requested.  Other scopes can be added
            LiveLoginResult result = await liveIdClient.LoginAsync(new string[] { "wl.basic" });
            if (result.Status == LiveConnectSessionStatus.Connected)
            {
                session = result.Session;

                // Get information about the logged-in user.
                LiveConnectClient client = new LiveConnectClient(session);
                LiveOperationResult meResult = await client.GetAsync("me");

                // Use the Microsoft account auth token to sign in to App Service.
                MobileServiceUser loginResult = await App.MobileService
                    .LoginWithMicrosoftAccountAsync(result.Session.AuthenticationToken);

                // Display a personalized sign-in greeting.
                string title = string.Format("Welcome {0}!", meResult.Result["first_name"]);
                var message = string.Format("You are now logged in - {0}", loginResult.UserId);
                var dialog = new MessageDialog(message, title);
                dialog.Commands.Add(new UICommand("OK"));
                await dialog.ShowAsync();
            }
            else
            {
                session = null;
                var dialog = new MessageDialog("You must log in.", "Login Required");
                dialog.Commands.Add(new UICommand("OK"));
                await dialog.ShowAsync();
            }
        }
    }

Para obtener más información, consulte la documentación del [SDK de Windows Live] .

###<a name="serverflow"></a>Autenticación de servidor administrados

Una vez que haya registrado el proveedor de identidades, llame al método [LoginAsync] en [MobileServiceClient] con el valor de [MobileServiceAuthenticationProvider] de su proveedor. Por ejemplo, el siguiente código inicia una sesión del flujo de servidor mediante Facebook.

    private MobileServiceUser user;
    private async System.Threading.Tasks.Task Authenticate()
    {
        while (user == null)
        {
            string message;
            try
            {
                user = await client
                    .LoginAsync(MobileServiceAuthenticationProvider.Facebook);
                message =
                    string.Format("You are now logged in - {0}", user.UserId);
            }
            catch (InvalidOperationException)
            {
                message = "You must log in. Login Required";
            }

            var dialog = new MessageDialog(message);
            dialog.Commands.Add(new UICommand("OK"));
            await dialog.ShowAsync();
        }
    }

Si está utilizando un proveedor de identidades que no sea de Facebook, cambie el valor de [MobileServiceAuthenticationProvider] al valor para el proveedor.

En un flujo de servidor, el servicio de aplicación de Azure administra el flujo de autenticación de OAuth mostrando la página de inicio de sesión del proveedor seleccionado.  Una vez que la identidad proveedor devuelve, servicio de aplicación de Azure genera un símbolo de autenticación del servicio de aplicación. El método [LoginAsync] devuelve un [MobileServiceUser], que proporciona tanto el [identificador de usuario] del usuario autenticado y la [MobileServiceAuthenticationToken], como un símbolo de web JSON (JWT). Este token se puede en caché y volver a utilizar hasta que caduca. Para obtener más información, vea [almacenamiento en caché el símbolo de autenticación](#caching).

###<a name="caching"></a>Almacenamiento en caché el símbolo de autenticación

En algunos casos, se puede evitar la llamada al método de inicio de sesión después de la primera autenticación correcta almacenando el símbolo de autenticación del proveedor.  Aplicaciones de la tienda Windows y UWP pueden usar [PasswordVault] en caché el símbolo de autenticación actual después de una correcta de inicio de sesión, como sigue:

    await client.LoginAsync(MobileServiceAuthenticationProvider.Facebook);

    PasswordVault vault = new PasswordVault();
    vault.Add(new PasswordCredential("Facebook", client.currentUser.UserId,
        client.currentUser.MobileServiceAuthenticationToken));

El valor del identificador de usuario se almacena como el nombre de usuario de la credencial y el token está almacenado como la contraseña. En las siguientes empresas, puede comprobar la **PasswordVault** las credenciales en caché. En el ejemplo siguiente se usa las credenciales en caché cuando se encuentran y, en caso contrario, intenta autenticarse de nuevo con el servidor:

    // Try to retrieve stored credentials.
    var creds = vault.FindAllByResource("Facebook").FirstOrDefault();
    if (creds != null)
    {
        // Create the current user from the stored credentials.
        client.currentUser = new MobileServiceUser(creds.UserName);
        client.currentUser.MobileServiceAuthenticationToken =
            vault.Retrieve("Facebook", creds.UserName).Password;
    }
    else
    {
        // Regular login flow and cache the token as shown above.
    }

Al cerrar la sesión un usuario, también debe quitar las credenciales almacenadas, como sigue:

    client.Logout();
    vault.Remove(vault.Retrieve("Facebook", client.currentUser.UserId));

Aplicaciones Xamarin use [Xamarin.Auth] API para almacenar credenciales de forma segura en un objeto de la **cuenta** . Para obtener un ejemplo de uso de estas API, consulte el archivo de código de [AuthStore.cs] en la [foto de ContosoMoments ejemplo de uso compartido].

Al usar autenticación administradas por el cliente, también puede almacenar en caché el token de acceso que obtuvo de su proveedor de servicios como Facebook o Twitter. Puede proporcionar este token para solicitar un nuevo token de autenticación del servidor, como sigue:

    var token = new JObject();
    // Replace <your_access_token_value> with actual value of your access token
    token.Add("access_token", "<your_access_token_value>");

    // Authenticate using the access token.
    await client.LoginAsync(MobileServiceAuthenticationProvider.Facebook, token);

##<a name="pushnotifications"></a>Notificaciones de inserción

Los temas siguientes tratan las notificaciones de inserción:

* [Registrar las notificaciones de inserción](#register-for-push)
* [Obtener un paquete de la tienda Windows SID](#package-sid)
* [Registrar con plantillas de varias plataformas](#register-xplat)

###<a name="register-for-push"></a>Cómo: registrar las notificaciones de inserción

El cliente de aplicaciones móviles le permite registrar las notificaciones de inserción con Hubs de notificación de Azure. Cuando se registra, se obtiene un identificador que se obtiene de específico de la plataforma de inserción notificación de servicio (PNS). A continuación, proporcionar este valor junto con las etiquetas cuando cree el registro. El código siguiente registra la aplicación de Windows para las notificaciones de inserción con el servicio de notificación de Windows (WNS):

    private async void InitNotificationsAsync()
    {
        // Request a push notification channel.
        var channel =await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();

        // Register for notifications using the new channel.
        await MobileService.GetPush().RegisterNativeAsync(channel.Uri, null);
    }

Si va a insertar WNS, primero debe [obtener un paquete de la tienda Windows SID](#package-sid).  Para obtener más información sobre las aplicaciones de Windows, incluido cómo registrar para los registros de plantilla, vea [Agregar las notificaciones de inserción a la aplicación].

No se admite la solicitud de etiquetas desde el cliente.  Las solicitudes de etiqueta silenciosamente se eliminan del registro.
Si desea registrar el dispositivo con etiquetas, cree una API personalizada que usa la API de Hubs de notificación para realizar el registro en su nombre.  [Llamar a la API personalizado](#customapi) en lugar de la `RegisterNativeAsync()` método.

###<a name="package-sid"></a>Cómo: obtener un paquete de la tienda Windows SID

Se necesita un paquete SID para habilitar las notificaciones de inserción en las aplicaciones de la tienda Windows.  Para recibir un paquete SID, registrar la aplicación en la tienda de Windows.

Para obtener este valor:

1. En el Explorador de soluciones de Visual Studio, haga clic en el proyecto de aplicación de la tienda de Windows, haga clic en **almacén** > **Asociar la aplicación en la tienda …**.
2. En el asistente, haga clic en **siguiente**, inicie sesión con su cuenta de Microsoft, escriba un nombre para la aplicación de **reserva un nuevo nombre de la aplicación**y haga clic en **reservar**.
3. Una vez creado correctamente el registro de aplicación, seleccione el nombre de la aplicación, haga clic en **siguiente**y, a continuación, haga clic en **asociar**.
4. Inicie sesión en el [Centro de desarrollo de Windows] mediante su Account de Microsoft. En **Mis aplicaciones**, haga clic en el registro de aplicación que ha creado.
5. Haga clic en **administración de la aplicación** > **identidad de la aplicación**y, a continuación, desplácese hacia abajo hasta encontrar el **Paquete SID**.

Muchos usos del paquete SID tratan como un URI, en cuyo caso debe usar _ms aplicación: / /_ como la combinación. Tome nota de la versión de su paquete SID concatenando este valor como un prefijo.

Aplicaciones de Xamarin requieran código adicional para poder registrar una aplicación que se ejecuta en el iOS o Android plataformas. Para obtener más información, vea el tema correspondiente a su plataforma:

* [Xamarin.Android](app-service-mobile-xamarin-android-get-started-push.md#add-push)
* [Xamarin.iOS](app-service-mobile-xamarin-ios-get-started-push.md#add-push)

###<a name="register-xplat"></a>Cómo: registrar plantillas de inserción para enviar notificaciones de varias plataformas

Para registrar plantillas, utilice la `RegisterAsync()` método con las plantillas, como sigue:

        JObject templates = myTemplates();
        MobileService.GetPush().RegisterAsync(channel.Uri, templates);

Las plantillas deben ser `JObject` tipos y puede contener varias plantillas en el siguiente formato JSON:

        public JObject myTemplates()
        {
            // single template for Windows Notification Service toast
            var template = "<toast><visual><binding template=\"ToastText01\"><text id=\"1\">$(message)</text></binding></visual></toast>";

            var templates = new JObject
            {
                ["generic-message"] = new JObject
                {
                    ["body"] = template,
                    ["headers"] = new JObject
                    {
                        ["X-WNS-Type"] = "wns/toast"
                    },
                    ["tags"] = new JArray()
                },
                ["more-templates"] = new JObject {...}
            };
            return templates;
        }

El método **RegisterAsync()** también acepta mosaicos secundarios:

        MobileService.GetPush().RegisterAsync(string channelUri, JObject templates, JObject secondaryTiles);

Todas las etiquetas se eliminan ausente durante el registro de seguridad. Para agregar etiquetas a las instalaciones o plantillas dentro de las instalaciones, consulte [trabajar con el servidor back-end de .NET SDK para las aplicaciones móviles de Azure].

Para enviar notificaciones utilizando estas plantillas registradas, consulte las [API de Hubs de notificación].

##<a name="misc"></a>Varios temas

###<a name="errors"></a>Cómo: controlar los errores

Cuando se produce un error en el servidor, el cliente de SDK provoca un `MobileServiceInvalidOperationException`.  En el ejemplo siguiente se muestra cómo controlar una excepción devuelta por el servidor:

    private async void InsertTodoItem(TodoItem todoItem)
    {
        // This code inserts a new TodoItem into the database. When the operation completes
        // and App Service has assigned an Id, the item is added to the CollectionView
        try
        {
            await todoTable.InsertAsync(todoItem);
            items.Add(todoItem);
        }
        catch (MobileServiceInvalidOperationException e)
        {
            // Handle error
        }
    }

Otro ejemplo de cómo tratar con condiciones de error puede encontrarse en la [Muestra de archivos de aplicaciones móviles]. El ejemplo [LoggingHandler] proporciona un controlador de delegado de registro (siguiente) para iniciar las solicitudes que se realizan en el servidor.

###<a name="headers"></a>Cómo: personalizar encabezados de solicitud

Para admitir el escenario de aplicación concreta, necesitará personalizar la comunicación con el servidor de la aplicación móvil. Por ejemplo, que desea agregar un encabezado personalizado a cada solicitud saliente o incluso cambiar los códigos de estado de las respuestas. Puede usar una personalizada [DelegatingHandler], como en el ejemplo siguiente:

    public async Task CallClientWithHandler()
    {
        HttpResponseMessage[]
        MobileServiceClient client = new MobileServiceClient("AppUrl",
            new MyHandler()
            );
        IMobileServiceTable<TodoItem> todoTable = client.GetTable<TodoItem>();
        var newItem = new TodoItem { Text = "Hello world", Complete = false };
        await todoTable.InsertAsync(newItem);
    }

    public class MyHandler : DelegatingHandler
    {
        protected override async Task<HttpResponseMessage>
            SendAsync(HttpRequestMessage request, CancellationToken cancellationToken)
        {
            // Change the request-side here based on the HttpRequestMessage
            request.Headers.Add("x-my-header", "my value");

            // Do the request
            var response = await base.SendAsync(request, cancellationToken);

            // Change the response-side here based on the HttpResponseMessage

            // Return the modified response
            return response;
        }
    }


<!-- Anchors. -->


<!-- Images. -->

<!-- URLs. -->
[1]: app-service-mobile-windows-store-dotnet-get-started.md
[2]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[3]: app-service-mobile-node-backend-how-to-use-server-sdk.md
[4]: https://msdn.microsoft.com/en-us/library/azure/mt419521(v=azure.10).aspx
[5]: https://github.com/Azure-Samples
[6]: http://www.newtonsoft.com/json/help/html/Properties_T_Newtonsoft_Json_JsonPropertyAttribute.htm
[7]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#how-to-define-a-table-controller
[8]: app-service-mobile-node-backend-how-to-use-server-sdk.md#TableOperations
[9]: https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/
[10]: http://www.symbolsource.org/
[11]: http://www.symbolsource.org/Public/Wiki/Using
[12]: https://msdn.microsoft.com/en-us/library/azure/microsoft.windowsazure.mobileservices.mobileserviceclient(v=azure.10).aspx

[Agregar autenticación para su aplicación]: app-service-mobile-windows-store-dotnet-get-started-users.md
[Sincronización de datos sin conexión en aplicaciones móviles de Azure]: app-service-mobile-offline-data-sync.md
[Agregar las notificaciones de inserción a la aplicación]: app-service-mobile-windows-store-dotnet-get-started-push.md
[Registrar su aplicación para usar un inicio de sesión de cuenta de Microsoft]: app-service-mobile-how-to-configure-microsoft-authentication.md
[Cómo configurar la aplicación de servicio de inicio de sesión de Active Directory]: app-service-mobile-how-to-configure-active-directory-authentication.md

<!-- Microsoft URLs. -->
[MobileServiceCollection]: https://msdn.microsoft.com/en-us/library/azure/dn250636(v=azure.10).aspx
[MobileServiceIncrementalLoadingCollection]: https://msdn.microsoft.com/en-us/library/azure/dn268408(v=azure.10).aspx
[MobileServiceAuthenticationProvider]: http://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceauthenticationprovider(v=azure.10).aspx
[MobileServiceUser]: http://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceuser(v=azure.10).aspx
[MobileServiceAuthenticationToken]: http://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceuser.mobileserviceauthenticationtoken(v=azure.10).aspx
[GetTable]: https://msdn.microsoft.com/en-us/library/azure/jj554275(v=azure.10).aspx
[crea una referencia a una tabla sin tipo]: https://msdn.microsoft.com/en-us/library/azure/jj554278(v=azure.10).aspx
[DeleteAsync]: https://msdn.microsoft.com/en-us/library/azure/dn296407(v=azure.10).aspx
[IncludeTotalCount]: https://msdn.microsoft.com/en-us/library/azure/dn250560(v=azure.10).aspx
[InsertAsync]: https://msdn.microsoft.com/en-us/library/azure/dn296400(v=azure.10).aspx
[InvokeApiAsync]: https://msdn.microsoft.com/en-us/library/azure/dn268343(v=azure.10).aspx
[LoginAsync]: https://msdn.microsoft.com/en-us/library/azure/dn296411(v=azure.10).aspx
[LookupAsync]: https://msdn.microsoft.com/en-us/library/azure/jj871654(v=azure.10).aspx
[OrderBy]: https://msdn.microsoft.com/en-us/library/azure/dn250572(v=azure.10).aspx
[OrderByDescending]: https://msdn.microsoft.com/en-us/library/azure/dn250568(v=azure.10).aspx
[ReadAsync]: https://msdn.microsoft.com/en-us/library/azure/mt691741(v=azure.10).aspx
[Tomar]: https://msdn.microsoft.com/en-us/library/azure/dn250574(v=azure.10).aspx
[Seleccione]: https://msdn.microsoft.com/en-us/library/azure/dn250569(v=azure.10).aspx
[Saltar]: https://msdn.microsoft.com/en-us/library/azure/dn250573(v=azure.10).aspx
[UpdateAsync]: https://msdn.microsoft.com/en-us/library/azure/dn250536.(v=azure.10)aspx
[Identificador de usuario]: http://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceuser.userid(v=azure.10).aspx
[Donde]: https://msdn.microsoft.com/en-us/library/azure/dn250579(v=azure.10).aspx
[Portal de Azure]: https://portal.azure.com/
[Portal de clásico de Azure]: https://manage.windowsazure.com/
[EnableQueryAttribute]: https://msdn.microsoft.com/library/system.web.http.odata.enablequeryattribute.aspx
[Guid.NewGuid]: https://msdn.microsoft.com/en-us/library/system.guid.newguid(v=vs.110).aspx
[ISupportIncrementalLoading]: http://msdn.microsoft.com/library/windows/apps/Hh701916.aspx
[Centro de desarrollo de Windows]: https://dev.windows.com/en-us/overview
[DelegatingHandler]: https://msdn.microsoft.com/library/system.net.http.delegatinghandler(v=vs.110).aspx
[SDK de Windows Live]: https://msdn.microsoft.com/en-us/library/bb404787.aspx
[PasswordVault]: http://msdn.microsoft.com/library/windows/apps/windows.security.credentials.passwordvault.aspx
[ProtectedData]: http://msdn.microsoft.com/library/system.security.cryptography.protecteddata%28VS.95%29.aspx
[API de Hubs de notificación]: https://msdn.microsoft.com/library/azure/dn495101.aspx
[Ejemplo de archivos de aplicaciones móviles]: https://github.com/Azure-Samples/app-service-mobile-dotnet-todo-list-files
[LoggingHandler]: https://github.com/Azure-Samples/app-service-mobile-dotnet-todo-list-files/blob/master/src/client/MobileAppsFilesSample/Helpers/LoggingHandler.cs#L63

<!-- External URLs -->
[Documentación de OData v3]: http://www.odata.org/documentation/odata-version-3-0/
[Fiddler]: http://www.telerik.com/fiddler
[Json.NET]: http://www.newtonsoft.com/json
[Xamarin.Auth]: https://components.xamarin.com/view/xamarin.auth/
[AuthStore.cs]: (https://github.com/azure-appservice-samples/ContosoMoments/blob/dev/src/Mobile/ContosoMoments/Helpers/AuthStore.cs)
[Ejemplo de uso compartido de fotografías ContosoMoments]: https://github.com/azure-appservice-samples/ContosoMoments