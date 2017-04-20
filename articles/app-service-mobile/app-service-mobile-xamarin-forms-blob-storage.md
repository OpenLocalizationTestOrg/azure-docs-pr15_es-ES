<properties
    pageTitle="Conectarse al almacenamiento de Azure en la aplicación de Xamarin.Forms"
    description="Agregar imágenes a la aplicación móvil de todo lista Xamarin.Forms mediante una conexión con el almacenamiento de blobs de Windows Azure"
    documentationCenter="xamarin"
    authors="adrianhall"
    manager="erikre"
    editor=""
    services="app-service\mobile"/>

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-xamarin-ios"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="10/01/2016"
    ms.author="adrianha"/>

#<a name="connect-to-azure-storage-in-your-xamarinforms-app"></a>Conectarse al almacenamiento de Azure en la aplicación de Xamarin.Forms

## <a name="overview"></a>Información general

El cliente de aplicaciones móviles de Azure y servidor SDK admiten sincronización sin conexión de datos estructurados con operaciones CRUD el extremo /tables. Generalmente estos datos se almacenan en una base de datos o el almacén similar y, en general, estas almacena datos no puede almacenar datos binarios grandes eficazmente. Además, algunas aplicaciones tener y relacionados con los datos almacenan en otro sitio (por ejemplo, almacenamiento de blobs, recursos compartidos de archivos), resulta útil poder crear asociaciones entre registros en el extremo de /tables y otros datos.

Este tema muestra cómo agregar imágenes al tutorial de lista de aplicaciones de Mobile todo. En primer lugar, debe completar el tutorial [crear una aplicación de Xamarin.Forms].

En este tutorial, se crea una cuenta de almacenamiento y agregar una cadena de conexión a la aplicación móvil de back-end. A continuación, agregará una herencia de nuevo desde el nuevo tipo de aplicaciones de Mobile `StorageController<T>` a su proyecto de servidor.

>[AZURE.TIP] Este tutorial tiene un [ejemplo companion](https://azure.microsoft.com/documentation/samples/app-service-mobile-dotnet-todo-list-files/) disponible, que se puede implementar a su cuenta de Azure. 

## <a name="prerequisites"></a>Requisitos previos

* Complete el tutorial de [crear una aplicación de Xamarin.Forms] , que se enumeran otros requisitos previos. En este artículo se utiliza la aplicación de ese tutorial completada.

>[AZURE.NOTE] Si desea empezar a trabajar con el servicio de aplicación de Azure antes de suscribirse a una cuenta de Azure, vaya a [Probar el servicio de aplicación](https://tryappservice.azure.com/?appServiceName=mobile). Allí, puede crear inmediatamente una aplicación móvil de corta duración starter en la aplicación de servicio: sin tarjeta de crédito obligatorio y sin compromisos.

## <a name="create-a-storage-account"></a>Crear una cuenta de almacenamiento

1. Cree una cuenta de almacenamiento siguiendo el tutorial [crear una cuenta de almacenamiento de Azure]. 

2. En el portal de Azure, vaya a su cuenta de almacenamiento recién creado y haga clic en el icono de **teclas** . Copie la **cadena de conexión principal**.

3. Vaya a la aplicación móvil de back-end. En **Todos los valores** -> **Configuración de la aplicación** -> **Cadenas de conexión**, crear una nueva clave denominada `MS_AzureStorageAccountConnectionString` y use el valor copiado de la cuenta de almacenamiento. Usar **personalizada** como el tipo de clave.

## <a name="add-a-storage-controller-to-the-server"></a>Agregar un controlador de almacenamiento en el servidor

Debe agregar un nuevo controlador a un proyecto de servidor que se responder a solicitudes de un símbolo de SA para el almacenamiento de Azure, así como obtener una lista de archivos que corresponde a un registro:

- [Agregar un controlador de almacenamiento a su proyecto de servidor](#add-controller-code)
- [Rutas registradas por el controlador de almacenamiento](#routes-registered)
- [Comunicación de cliente y de servidor](#client-communication)

###<a name="add-controller-code"></a>Agregar un controlador de almacenamiento a su proyecto de servidor

1. En Visual Studio, abra el proyecto de servidor de .NET. Agregue el paquete de Nuget [Microsoft.Azure.Mobile.Server.Files]. Asegúrese de seleccionar **incluir versión preliminar**.

2. En Visual Studio, abra el proyecto de servidor de .NET. Haga clic en la carpeta de **controladores** y seleccione **Agregar** -> **controlador** -> **Controlador de Web API 2 - vacío**. Nombre del controlador `TodoItemStorageController`.

3. Agregue las siguientes instrucciones:

        using Microsoft.Azure.Mobile.Server.Files;
        using Microsoft.Azure.Mobile.Server.Files.Controllers;

4. Cambiar la clase base para `StorageController`:
    
        public class TodoItemStorageController : StorageController<TodoItem>

5. Agregue los métodos siguientes a la clase:

        [HttpPost]
        [Route("tables/TodoItem/{id}/StorageToken")]
        public async Task<HttpResponseMessage> PostStorageTokenRequest(string id, StorageTokenRequest value)
        {
            StorageToken token = await GetStorageTokenAsync(id, value);

            return Request.CreateResponse(token);
        }

        // Get the files associated with this record
        [HttpGet]
        [Route("tables/TodoItem/{id}/MobileServiceFiles")]
        public async Task<HttpResponseMessage> GetFiles(string id)
        {
            IEnumerable<MobileServiceFile> files = await GetRecordFilesAsync(id);

            return Request.CreateResponse(files);
        }

        [HttpDelete]
        [Route("tables/TodoItem/{id}/MobileServiceFiles/{name}")]
        public Task Delete(string id, string name)
        {
            return base.DeleteFileAsync(id, name);
        }

6. Actualizar la configuración de Web API para configurar el enrutamiento de atributo. En **Startup.MobileApp.cs**, agregue la línea siguiente a la `ConfigureMobileApp()` método después de la definición de la `config` variable:

        config.MapHttpAttributeRoutes();

7. Publicar el proyecto de servidor en la aplicación móvil de back-end.

###<a name="routes-registered"></a>Rutas registradas por el controlador de almacenamiento

El nuevo `TodoItemStorageController` expone dos recursos subcarpetas en el registro administra:

- StorageToken

    + HTTP POST: Crea un símbolo de almacenamiento
    
        `/tables/TodoItem/{id}/MobileServiceFiles`
    
- MobileServiceFiles

    + HTTP GET: Recupera una lista de archivos asociados con el registro
    
        `/tables/TodoItem/{id}/MobileServiceFiles`

    + ELIMINAR de HTTP: Elimina el archivo especificado en el identificador de recursos de archivo
    
        `/tables/TodoItem/{id}/MobileServiceFiles/{fileid}`

###<a name="client-communication"></a>Comunicación de cliente y de servidor

Tenga en cuenta que `TodoItemStorageController` hace *no* tiene una ruta para cargar o descargar un blob. Eso es porque un cliente móvil interactúa con blob almacenamiento *directamente* para realizar estas operaciones, después de obtener primero un token SA (compartido firma de Access) para acceso seguro a un determinado blob o contenedor. Se trata de un diseño de arquitectura importante, tal como lo contrario, acceso al almacenamiento estaría limitado a la escalabilidad y la disponibilidad de la back-end móvil. En su lugar, conectando directamente al almacenamiento de Azure, los clientes móviles pueden sacar partido de sus características como partición automática y distribución geográfica.

Una firma de acceso compartido proporciona acceso delegado a los recursos en su cuenta de almacenamiento. Esto significa que puede conceder a que un cliente de permisos limitados a objetos de su cuenta de almacenamiento durante un período específico de tiempo y con un conjunto determinado de permisos, sin tener que compartir su teclas de acceso de la cuenta. Para obtener más información, vea [Descripción compartidas de las firmas de Access].

El diagrama siguiente muestra las interacciones de cliente y el servidor. Antes de cargar un archivo, el cliente solicita un token SA del servicio. El servicio utiliza la cadena de conexión de almacenamiento para generar una nueva SA, que, a continuación, vuelve al cliente. Las asociaciones de seguridad es de tiempo limitado y restringe permisos a un archivo en particular o contenedor. El cliente móvil usa esta SA y el cliente de almacenamiento de Azure SDK para cargar el archivo en el almacenamiento de blobs.

![Solicita un token SA](./media/app-service-mobile-xamarin-forms-blob-storage/storage-token-diagram.png)

## <a name="update-your-client-app-to-add-image-support"></a>Actualizar la aplicación cliente para agregar compatibilidad con imágenes

Abra el proyecto de tutorial rápido Xamarin.Forms en Visual Studio o Xamarin Studio. Se instalar paquetes de Nuget y actualizar el proyecto de biblioteca portátil y el iOS, Android y Windows proyectos de cliente:

- [Agregar paquetes de Nuget](#add-nuget)
- [Agregar interfaz IPlatform](#add-iplatform)
- [Agregar clase FileHelper](#add-filehelper)
- [Agregar un controlador de sincronización de archivos](#file-sync-handler)
- [Actualizar TodoItemManager](#update-todoitemmanager)
- [Agregar una vista de detalles](#add-details-view)
- [Actualizar la vista principal](#update-main-view)
- [Actualizar el proyecto Android](#update-android), [iOS proyecto](#update-ios), [project de Windows](#update-windows)

>[AZURE.NOTE] Este tutorial solo contiene instrucciones para la plataformas de la tienda Windows, no Windows Phone, iOS y Android.

###<a name="add-nuget"></a>Agregar paquetes de Nuget

Haga clic en la solución y seleccione **Administrar Nuget paquetes de solución**. Agregue los siguientes paquetes de Nuget para **todos los** proyectos en la solución. Asegúrese de comprobar la **versión preliminar de incluir**.

  - [Microsoft.Azure.Mobile.Client.Files]

  - [Microsoft.Azure.Mobile.Client.SQLiteStore]

  - [PCLStorage]

Para mayor comodidad, en este ejemplo usa la biblioteca [PCLStorage] , pero no es necesaria para el cliente de aplicaciones móviles de Azure SDK.

[PCLStorage]: https://www.nuget.org/packages/PCLStorage/

###<a name="add-iplatform"></a>Agregar interfaz IPlatform

Crear una nueva interfaz `IPlatform` en el proyecto principal biblioteca portátil. Esto sigue el modelo de [Xamarin.Forms DependencyService] para cargar la clase derecha específica de la plataforma en tiempo de ejecución. Más adelante agregará implementaciones específicas de la plataforma en cada uno de los proyectos de cliente.

1. Agregue las siguientes instrucciones:

        using Microsoft.WindowsAzure.MobileServices.Files;
        using Microsoft.WindowsAzure.MobileServices.Files.Metadata;
        using Microsoft.WindowsAzure.MobileServices.Sync;

2. Reemplace la implementación con los siguientes elementos:

        public interface IPlatform
        {
            Task <string> GetTodoFilesPathAsync();

            Task<IMobileServiceFileDataSource> GetFileDataSource(MobileServiceFileMetadata metadata);

            Task<string> TakePhotoAsync(object context);

            Task DownloadFileAsync<T>(IMobileServiceSyncTable<T> table, MobileServiceFile file, string filename);
        }

###<a name="add-filehelper"></a>Agregar clase FileHelper

1. Crear una nueva clase `FileHelper` en el proyecto principal biblioteca portátil. Agregue las siguientes instrucciones:

        using System.IO;
        using PCLStorage;
        using System.Threading.Tasks;
        using Xamarin.Forms;

2. Agregue la definición de clase:

        public class FileHelper
        {
            public static async Task<string> CopyTodoItemFileAsync(string itemId, string filePath)
            {
                IFolder localStorage = FileSystem.Current.LocalStorage;

                string fileName = Path.GetFileName(filePath);
                string targetPath = await GetLocalFilePathAsync(itemId, fileName);

                var sourceFile = await localStorage.GetFileAsync(filePath);
                var sourceStream = await sourceFile.OpenAsync(FileAccess.Read);

                var targetFile = await localStorage.CreateFileAsync(targetPath, CreationCollisionOption.ReplaceExisting);

                using (var targetStream = await targetFile.OpenAsync(FileAccess.ReadAndWrite)) {
                    await sourceStream.CopyToAsync(targetStream);
                }

                return targetPath;
            }

            public static async Task<string> GetLocalFilePathAsync(string itemId, string fileName)
            {
                IPlatform platform = DependencyService.Get<IPlatform>();

                string recordFilesPath = Path.Combine(await platform.GetTodoFilesPathAsync(), itemId);

                    var checkExists = await FileSystem.Current.LocalStorage.CheckExistsAsync(recordFilesPath);
                    if (checkExists == ExistenceCheckResult.NotFound) {
                        await FileSystem.Current.LocalStorage.CreateFolderAsync(recordFilesPath, CreationCollisionOption.ReplaceExisting);
                    }

                return Path.Combine(recordFilesPath, fileName);
            }

            public static async Task DeleteLocalFileAsync(Microsoft.WindowsAzure.MobileServices.Files.MobileServiceFile fileName)
            {
                string localPath = await GetLocalFilePathAsync(fileName.ParentId, fileName.Name);
                var checkExists = await FileSystem.Current.LocalStorage.CheckExistsAsync(localPath);

                if (checkExists == ExistenceCheckResult.FileExists) {
                    var file = await FileSystem.Current.LocalStorage.GetFileAsync(localPath);
                    await file.DeleteAsync();
                }
            }
        }

###<a name="file-sync-handler"></a>Agregar un controlador de sincronización de archivos

Crear una nueva clase `TodoItemFileSyncHandler` en el proyecto principal biblioteca portátil. Esta clase contiene devoluciones de llamada en el SDK de Azure para notificar a su código cuando se agrega o quita un archivo.

El SDK de cliente de Azure Mobile almacena los datos de archivo: el cliente SDK invoca su implementación de `IFileSyncHandler` por separado, que determina cuándo y cómo se almacenan los archivos en el dispositivo local.

1. Agregue las siguientes instrucciones:

        using System.Threading.Tasks;
        using Microsoft.WindowsAzure.MobileServices.Files.Sync;
        using Microsoft.WindowsAzure.MobileServices.Files;
        using Microsoft.WindowsAzure.MobileServices.Files.Metadata;
        using Xamarin.Forms;

2. Reemplazar la definición de clase con los siguientes elementos: 

        public class TodoItemFileSyncHandler : IFileSyncHandler
        {
            private readonly TodoItemManager todoItemManager;

            public TodoItemFileSyncHandler(TodoItemManager itemManager)
            {
                this.todoItemManager = itemManager;
            }

            public Task<IMobileServiceFileDataSource> GetDataSource(MobileServiceFileMetadata metadata)
            {
                IPlatform platform = DependencyService.Get<IPlatform>();
                return platform.GetFileDataSource(metadata);
            }

            public async Task ProcessFileSynchronizationAction(MobileServiceFile file, FileSynchronizationAction action)
            {
                if (action == FileSynchronizationAction.Delete) {
                    await FileHelper.DeleteLocalFileAsync(file);
                }
                else { // Create or update. We're aggressively downloading all files.
                    await this.todoItemManager.DownloadFileAsync(file);
                }
            }
        }

###<a name="update-todoitemmanager"></a>Actualizar TodoItemManager

1. En **TodoItemManager.cs**, elimine la línea `#define OFFLINE_SYNC_ENABLED`.

2. En **TodoItemManager.cs**, agregue las siguientes instrucciones:

        using System.IO;
        using Xamarin.Forms;
        using Microsoft.WindowsAzure.MobileServices.Files;
        using Microsoft.WindowsAzure.MobileServices.Files.Sync;
        using Microsoft.WindowsAzure.MobileServices.Eventing;

3. En el constructor de `TodoItemManager`, agregue lo siguiente después de la llamada a `DefineTable()`:

        // Initialize file sync
        this.client.InitializeFileSyncContext(new TodoItemFileSyncHandler(this), store);

4. En el constructor, reemplace la llamada a `InitializeAsync` con la siguiente. Esto se asegurará de que no hay devoluciones de llamada cuando se modifican los registros en el almacén local. La característica de sincronización de archivos usa estas devoluciones de llamada para activar el controlador de sincronización de archivos.

        this.client.SyncContext.InitializeAsync(store, StoreTrackingOptions.NotifyLocalAndServerOperations);

5. En `SyncAsync()`, agregue lo siguiente después de la llamada a `PushAsync()`:

        await this.todoTable.PushFileChangesAsync();

6. Agregar los siguientes métodos para `TodoItemManager`:

        internal async Task DownloadFileAsync(MobileServiceFile file)
        {
            var todoItem = await todoTable.LookupAsync(file.ParentId);
            IPlatform platform = DependencyService.Get<IPlatform>();

            string filePath = await FileHelper.GetLocalFilePathAsync(file.ParentId, file.Name); 
            await platform.DownloadFileAsync(this.todoTable, file, filePath);
        }

        internal async Task<MobileServiceFile> AddImage(TodoItem todoItem, string imagePath)
        {
            string targetPath = await FileHelper.CopyTodoItemFileAsync(todoItem.Id, imagePath);
            return await this.todoTable.AddFileAsync(todoItem, Path.GetFileName(targetPath));
        }

        internal async Task DeleteImage(TodoItem todoItem, MobileServiceFile file)
        {
            await this.todoTable.DeleteFileAsync(file);
        }

        internal async Task<IEnumerable<MobileServiceFile>> GetImageFilesAsync(TodoItem todoItem)
        {
            return await this.todoTable.GetFilesAsync(todoItem);
        }

###<a name="add-details-view"></a>Agregar una vista de detalles

En esta sección, agregará una nueva vista de detalles de un elemento todo. Cuando el usuario selecciona un elemento todo y permite nuevas imágenes para agregarse a un elemento, se crea la vista.

1. Agregar una nueva clase **TodoItemImage** al proyecto Biblioteca portátil con la implementación siguiente:

        public class TodoItemImage : INotifyPropertyChanged
        {
            private string name;
            private string uri;

            public MobileServiceFile File { get; private set; }

            public string Name
            {
                get { return name; }
                set
                {
                    name = value;
                    OnPropertyChanged(nameof(Name));
                }
            }

            public string Uri
            {
                get { return uri; }      
                set
                {
                    uri = value;
                    OnPropertyChanged(nameof(Uri));
                }
            }

            public TodoItemImage(MobileServiceFile file, TodoItem todoItem)
            {
                Name = file.Name;
                File = file;

                FileHelper.GetLocalFilePathAsync(todoItem.Id, file.Name).ContinueWith(x => this.Uri = x.Result);
            }

            public event PropertyChangedEventHandler PropertyChanged;

            private void OnPropertyChanged(string propertyName)
            {
                PropertyChanged?.Invoke(this, new PropertyChangedEventArgs(propertyName));
            }
        }

2. Editar **App.cs**. Reemplazar la inicialización de `MainPage` con lo siguiente:
    
        MainPage = new NavigationPage(new TodoList());

3. En **App.cs**, agregue las siguientes propiedades:

        public static object UIContext { get; set; }

4. Haga clic en el proyecto de biblioteca portátil y seleccione **Agregar** -> **Nuevo elemento** -> **entre plataformas** -> **Página Xaml de formularios**. Asigne un nombre a la vista `TodoItemDetailsView`.

5. Abra **TodoItemDetailsView.xaml** y reemplace el cuerpo de la contenidoPage con lo siguiente:

          <Grid>
            <Grid.RowDefinitions>
              <RowDefinition Height="Auto"/>
              <RowDefinition Height="Auto"/>
              <RowDefinition Height="*"/>
            </Grid.RowDefinitions>
            <Button Clicked="OnAdd" Text="Add image"></Button>
            <ListView x:Name="imagesList"
                      ItemsSource="{Binding Images}"
                      IsPullToRefreshEnabled="false"
                      Grid.Row="2">
              <ListView.ItemTemplate>
                <DataTemplate>
                  <ImageCell ImageSource="{Binding Uri}"
                             Text="{Binding Name}">
                  </ImageCell>
                </DataTemplate>
              </ListView.ItemTemplate>
            </ListView>
          </Grid>

6. Editar **TodoItemDetailsView.xaml.cs** y agregue las siguientes instrucciones:

        using System.Collections.ObjectModel;
        using Microsoft.WindowsAzure.MobileServices.Files;

7. Reemplazar la implementación de `TodoItemDetailsView` con lo siguiente:

        public partial class TodoItemDetailsView : ContentPage
        {
            private TodoItemManager manager;

            public TodoItem TodoItem { get; set; }        
            public ObservableCollection<TodoItemImage> Images { get; set; }

            public TodoItemDetailsView(TodoItem todoItem, TodoItemManager manager)
            {
                InitializeComponent();
                this.Title = todoItem.Name;

                this.TodoItem = todoItem;
                this.manager = manager;

                this.Images = new ObservableCollection<TodoItemImage>();
                this.BindingContext = this;
            }

            public async Task LoadImagesAsync()
            {
                IEnumerable<MobileServiceFile> files = await this.manager.GetImageFilesAsync(TodoItem);
                this.Images.Clear();

                foreach (var f in files) {
                    var todoImage = new TodoItemImage(f, this.TodoItem);
                    this.Images.Add(todoImage);
                }
            }

            public async void OnAdd(object sender, EventArgs e)
            {
                IPlatform mediaProvider = DependencyService.Get<IPlatform>();
                string sourceImagePath = await mediaProvider.TakePhotoAsync(App.UIContext);

                if (sourceImagePath != null) {
                    MobileServiceFile file = await this.manager.AddImage(this.TodoItem, sourceImagePath);

                    var image = new TodoItemImage(file, this.TodoItem);
                    this.Images.Add(image);
                }
            }
        }

###<a name="update-main-view"></a>Actualizar la vista principal 

Actualizar la vista principal para abrir la vista de detalles cuando se selecciona un elemento todo.

En **TodoList.xaml.cs**, reemplace la implementación de `OnSelected` con lo siguiente:

    public async void OnSelected(object sender, SelectedItemChangedEventArgs e)
    {
        var todo = e.SelectedItem as TodoItem;

        if (todo != null) {
            var detailsView = new TodoItemDetailsView(todo, manager);
            await detailsView.LoadImagesAsync();
            await Navigation.PushAsync(detailsView);
        }

        todoList.SelectedItem = null;
    }

###<a name="update-android"></a>Actualizar el proyecto Android

Agregar código específico de la plataforma al proyecto Android, incluido el código para descargar un archivo y el uso de la cámara para capturar una imagen nueva. 

Este código utiliza la Xamarin.Forms [DependencyService](https://developer.xamarin.com/guides/xamarin-forms/dependency-service/) para cargar la clase derecha específica de la plataforma en tiempo de ejecución.

1. Agregue el componente **Xamarin.Mobile** al proyecto Android.

2. Agregar una nueva clase `DroidPlatform` con la implementación siguiente. Reemplace "YourNamespace" por el espacio de nombres principal de su proyecto.

        using System;
        using System.IO;
        using System.Threading.Tasks;
        using Android.Content;
        using Microsoft.WindowsAzure.MobileServices.Files;
        using Microsoft.WindowsAzure.MobileServices.Files.Metadata;
        using Microsoft.WindowsAzure.MobileServices.Files.Sync;
        using Microsoft.WindowsAzure.MobileServices.Sync;
        using Xamarin.Media;

        [assembly: Xamarin.Forms.Dependency(typeof(YourNamespace.Droid.DroidPlatform))]
        namespace YourNamespace.Droid
        {
            public class DroidPlatform : IPlatform
            {
                public async Task DownloadFileAsync<T>(IMobileServiceSyncTable<T> table, MobileServiceFile file, string filename)
                {
                    var path = await FileHelper.GetLocalFilePathAsync(file.ParentId, file.Name);
                    await table.DownloadFileAsync(file, path);
                }

                public async Task<IMobileServiceFileDataSource> GetFileDataSource(MobileServiceFileMetadata metadata)
                {
                    var filePath = await FileHelper.GetLocalFilePathAsync(metadata.ParentDataItemId, metadata.FileName);
                    return new PathMobileServiceFileDataSource(filePath);
                }

                public async Task<string> TakePhotoAsync(object context)
                {
                    try {
                        var uiContext = context as Context;
                        if (uiContext != null) {
                            var mediaPicker = new MediaPicker(uiContext);
                            var photo = await mediaPicker.TakePhotoAsync(new StoreCameraMediaOptions());

                            return photo.Path;
                        }
                    }
                    catch (TaskCanceledException) {
                    }

                    return null;
                }

                public Task<string> GetTodoFilesPathAsync()
                {
                    string appData = Environment.GetFolderPath(Environment.SpecialFolder.MyDocuments);
                    string filesPath = Path.Combine(appData, "TodoItemFiles");

                    if (!Directory.Exists(filesPath)) {
                        Directory.CreateDirectory(filesPath);
                    }

                    return Task.FromResult(filesPath);
                }
            }
        }

3. Editar **MainActivity.cs**. En `OnCreate`, agregue lo siguiente antes de la llamada a `LoadApplication()`:

        App.UIContext = this;

###<a name="update-ios"></a>Actualizar el proyecto de iOS

Agregar código específico de la plataforma al proyecto iOS.

1. Agregue el componente **Xamarin.Mobile** al proyecto iOS.

2. Agregar una nueva clase `TouchPlatform` con la implementación siguiente. Reemplace "YourNamespace" por el espacio de nombres principal de su proyecto.

        using System;
        using System.Collections.Generic;
        using System.IO;
        using System.Text;
        using System.Threading.Tasks;
        using Microsoft.WindowsAzure.MobileServices.Files;
        using Microsoft.WindowsAzure.MobileServices.Files.Metadata;
        using Microsoft.WindowsAzure.MobileServices.Files.Sync;
        using Microsoft.WindowsAzure.MobileServices.Sync;
        using Xamarin.Media;

        [assembly: Xamarin.Forms.Dependency(typeof(YourNamespace.iOS.TouchPlatform))]
        namespace YourNamespace.iOS
        {
            class TouchPlatform : IPlatform
            {
                public async Task DownloadFileAsync<T>(IMobileServiceSyncTable<T> table, MobileServiceFile file, string filename)
                {
                    var path = await FileHelper.GetLocalFilePathAsync(file.ParentId, file.Name);
                    await table.DownloadFileAsync(file, path);
                }

                public async Task<IMobileServiceFileDataSource> GetFileDataSource(MobileServiceFileMetadata metadata)
                {
                    var filePath = await FileHelper.GetLocalFilePathAsync(metadata.ParentDataItemId, metadata.FileName);
                    return new PathMobileServiceFileDataSource(filePath);
                }

                public async Task<string> TakePhotoAsync(object context)
                {
                    try {
                        var mediaPicker = new MediaPicker();
                        var mediaFile = await mediaPicker.PickPhotoAsync();
                        return mediaFile.Path;
                    }
                    catch (TaskCanceledException) {
                        return null;
                    }
                }

                public Task<string> GetTodoFilesPathAsync()
                {
                    string filesPath = Path.Combine(Environment.GetFolderPath(Environment.SpecialFolder.MyDocuments), "TodoItemFiles");

                    if (!Directory.Exists(filesPath)) {
                        Directory.CreateDirectory(filesPath);
                    }

                    return Task.FromResult(filesPath);
                }
            }
        }

3. Editar **AppDelegate.cs** y quite los comentarios de la llamada a `SQLitePCL.CurrentPlatform.Init()`.

###<a name="update-windows"></a>Actualizar el proyecto de Windows

1. Instale la extensión de Visual Studio [SQLite para Windows 8.1](http://go.microsoft.com/fwlink/?LinkID=716919). Para obtener más información, consulte el tutorial [Habilitar la sincronización sin conexión para la aplicación de Windows](app-service-mobile-windows-store-dotnet-get-started-offline-data.md). 

2. Editar **Package.appxmanifest** y comprobar las funcionalidades de **cámara Web** .

3. Agregar una nueva clase `WindowsStorePlatform` con la implementación siguiente. Reemplace "YourNamespace" por el espacio de nombres principal de su proyecto.

        using System;
        using System.Threading.Tasks;
        using Microsoft.WindowsAzure.MobileServices.Files;
        using Microsoft.WindowsAzure.MobileServices.Files.Metadata;
        using Microsoft.WindowsAzure.MobileServices.Files.Sync;
        using Microsoft.WindowsAzure.MobileServices.Sync;
        using Windows.Foundation;
        using Windows.Media.Capture;
        using Windows.Storage;
        using YourNamespace;

        [assembly: Xamarin.Forms.Dependency(typeof(WinApp.WindowsStorePlatform))]
        namespace WinApp
        {
            public class WindowsStorePlatform : IPlatform
            {
                public async Task DownloadFileAsync<T>(IMobileServiceSyncTable<T> table, MobileServiceFile file, string filename)
                {
                    var path = await FileHelper.GetLocalFilePathAsync(file.ParentId, file.Name);
                    await table.DownloadFileAsync(file, path);
                }

                public async Task<IMobileServiceFileDataSource> GetFileDataSource(MobileServiceFileMetadata metadata)
                {
                    var filePath = await FileHelper.GetLocalFilePathAsync(metadata.ParentDataItemId, metadata.FileName);
                    return new PathMobileServiceFileDataSource(filePath);
                }

                public async Task<string> GetTodoFilesPathAsync()
                {
                    var storageFolder = ApplicationData.Current.LocalFolder;
                    var filePath = "TodoItemFiles";

                    var result = await storageFolder.TryGetItemAsync(filePath);

                    if (result == null) {
                        result = await storageFolder.CreateFolderAsync(filePath);
                    }

                    return result.Name; // later operations will use relative paths
                }

                public async Task<string> TakePhotoAsync(object context)
                {
                    try {
                        CameraCaptureUI dialog = new CameraCaptureUI();
                        Size aspectRatio = new Size(16, 9);
                        dialog.PhotoSettings.CroppedAspectRatio = aspectRatio;

                        StorageFile file = await dialog.CaptureFileAsync(CameraCaptureUIMode.Photo);
                        return file.Path;
                    }
                    catch (TaskCanceledException) {
                        return null;
                    }
                }
            }
        }

##<a name="summary"></a>Resumen

En este artículo se describe cómo usar la nueva compatibilidad de archivo en el cliente móvil de Azure y el SDK de servidor para trabajar con el almacenamiento de Azure. 

- Crear una cuenta de almacenamiento y agregar la cadena de conexión a la aplicación móvil de back-end. Solo el back-end tiene la clave para el almacenamiento de Azure: el cliente móvil solicita un token SA (firma de acceso compartido) cada vez que necesita tener acceso al almacenamiento de Azure. Para obtener más información sobre los tokens SA en el almacenamiento de Azure, consulte [Descripción compartidas de las firmas de Access].

- Crear un controlador que subclasifique `StorageController` con el fin de controlar las solicitudes de token SA y obtener los archivos que están asociados a un registro. De forma predeterminada, los archivos están asociados a un registro mediante el identificador de registro como parte del nombre del contenedor; Puede personalizar el comportamiento especificando una implementación de `IContainerNameResolver`. También se puede personalizar la directiva de token SA.

- No almacenar el SDK de cliente de Azure Mobile almacena los datos del archivo. En su lugar, el cliente SDK invoca su `IFileSyncHandler`, lo que decida cómo (y si) se almacenan los archivos en el dispositivo local. El controlador de sincronización está registrado como sigue:

        client.InitializeFileSync(new MyFileSyncHandler(), store);

      + `IFileSyncHandler.GetDataSource`se llama cuando el SDK de cliente móvil de Azure tiene los datos de archivo (por ejemplo, como parte del proceso de carga). Esto le da la capacidad de administrar cómo (y si) archivos se almacenan en el dispositivo local y devolver esa información cuando sea necesario.

      + `IFileSyncHandler.ProcessFileSynchronizationAction`se invoca como parte del flujo de sincronización de archivos. Una referencia de archivo y un valor de enumeración FileSynchronizationAction se proporcionan para que pueda decidir cómo la aplicación debe controlar ese evento (por ejemplo, descargar automáticamente un archivo cuando se crea o actualiza, eliminación de un archivo desde el dispositivo local cuando se elimina el archivo en el servidor).

- A `MobileServiceFile` se pueden utilizar en modo en línea o sin conexión, mediante un `IMobileServiceTable` o `IMobileServiceSyncTable`, respectivamente. En la situación sin conexión, la carga se producirá cuando la aplicación llama `PushFileChangesAsync`. Esto hace que la cola de la operación sin conexión que deben procesarse; para cada operación de archivo, el cliente móvil de Azure SDK llamará la `GetDataSource` método en la `IFileSyncHandler` instancia para recuperar el contenido del archivo para la carga.

- Para recuperar archivos de un elemento, llame a la "GetFilesAsync` method on the  `IMobileServiceTable<T> ` or IMobileServiceSyncTable<T>` instancia. Este método devuelve una lista de archivos asociados con el elemento de datos. (Nota: se trata de una operación *local* y devolverá los archivos basados en el estado del objeto cuando se sincronizó por última vez. Para obtener una lista actualizada de archivos del servidor, debe iniciar una operación de sincronización en primer lugar.)

        IEnumerable<MobileServiceFile> files = await myTable.GetFilesAsync(myItem);

- La característica de sincronización de archivos usa las notificaciones de cambio de registro en el almacén local para recuperar los registros que recibió el cliente como parte de una operación de inserción o de extracción. Esto se logra al activar las notificaciones locales y del servidor para el contexto de sincronización mediante el `StoreTrackingOptions` parámetro. 

        this.client.SyncContext.InitializeAsync(store, StoreTrackingOptions.NotifyLocalAndServerOperations);

      + Otras opciones de seguimiento de almacenamiento están disponibles, como las notificaciones sólo local o solo servidor. Puede agregar o propietario de devolución de llamada personalizado utilizando la `EventManager` propiedad de `IMobileServiceClient`:

            jobService.MobileService.EventManager.Subscribe<StoreOperationCompletedEvent>(StoreOperationEventHandler);

- Es posible agregar o quitar archivos de un registro modificando almacenamiento de blobs directamente, ya que la asociación se lleva a cabo una convención de nomenclatura. Sin embargo, en este caso debe siempre **actualizar la marca de tiempo de registro cuando se modifican los blobs asociados**. El cliente móvil de Azure SDK siempre actualiza un registro al agregar o quitar un archivo. 

    El motivo de este requisito es que los clientes móviles ya tiene el registro en el almacenamiento local. Cuando estos clientes realizan un extracción incremental, este registro no se devolverá y el cliente no consultará para los nuevos archivos asociados. Para evitar este problema, se recomienda actualizar la marca de tiempo de registro cuando se realiza cualquier cambio de almacenamiento de blobs de Windows que no utilice al cliente móvil de Azure SDK.

- El proyecto de cliente utiliza el modelo de [Xamarin.Forms DependencyService] para cargar la clase derecha específica de la plataforma en tiempo de ejecución. En este ejemplo, hemos definido una interfaz `IPlatform` con implementaciones en cada uno de los proyectos específicos de la plataforma.

<!-- URLs. -->

[Visual Studio Community 2013]: https://go.microsoft.com/fwLink/p/?LinkID=534203
[Crear una aplicación de Xamarin.Forms]: app-service-mobile-xamarin-forms-get-started.md
[Xamarin.Forms DependencyService]: https://developer.xamarin.com/guides/xamarin-forms/dependency-service/
[Microsoft.Azure.Mobile.Client.Files]: https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client.Files/
[Microsoft.Azure.Mobile.Client.SQLiteStore]: https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client.SQLiteStore/
[Microsoft.Azure.Mobile.Server.Files]: https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Files/
[Descripción de las firmas de Access compartidas]: ../storage/storage-dotnet-shared-access-signature-part-1.md
[Crear una cuenta de almacenamiento de Azure]:  ../storage/storage-create-storage-account.md#create-a-storage-account
