<properties
    pageTitle="Cómo usar el almacenamiento de blobs de Xamarin | Microsoft Azure"
    description="La biblioteca de cliente de almacenamiento de Azure para Xamarin permite a los desarrolladores crear iOS, Android y aplicaciones de la tienda de Windows con sus interfaces de usuario nativa. Este tutorial muestra cómo usar Xamarin para crear una aplicación que usa el almacenamiento de blobs de Windows Azure."
    services="storage"
    documentationCenter="xamarin"
    authors="micurd"
    manager="jahogg"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/08/2016"
    ms.author="micurd"/>

# <a name="how-to-use-blob-storage-from-xamarin"></a>Cómo usar el almacenamiento de blobs de Xamarin

[AZURE.INCLUDE [storage-selector-blob-include](../../includes/storage-selector-blob-include.md)]

## <a name="overview"></a>Información general

Xamarin permite a los desarrolladores utilizar compartido C# código base para crear iOS, Android y aplicaciones de la tienda de Windows con sus interfaces de usuario nativa. En este tutorial se muestra cómo usar el almacenamiento de blobs de Windows Azure con una aplicación de Xamarin. Si desea más información sobre el almacenamiento de Azure, antes de comenzar con el código, vea [Introducción a Microsoft Azure almacenamiento](storage-introduction.md).

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

[AZURE.INCLUDE [storage-mobile-authentication-guidance](../../includes/storage-mobile-authentication-guidance.md)]

## <a name="create-a-new-xamarin-application"></a>Cree una nueva aplicación de Xamarin

Para esta introducción, se creará una aplicación destinada a ventanas, iOS y Android. Esta aplicación se cree un contenedor y cargar un blob en este contenedor. Se va a usar Visual Studio en Windows para esta introducción a, pero se pueden aplicar la misma conocimientos al crear una aplicación con Xamarin Studio en Mac OS.

Siga estos pasos para crear la aplicación:

1. Si todavía no lo ha hecho, descargue e instale [Xamarin para Visual Studio](https://www.xamarin.com/download).
2. Abra Visual Studio y crear una aplicación en blanco (compartida nativa): **archivo > Nuevo > proyecto > entre plataformas > App(Native Shared) en blanco**.
3. Haga clic en la solución en el panel Explorador de soluciones y seleccione **Administrar paquetes de NuGet para la solución**. Buscar **WindowsAzure.Storage** e instalar la última versión estable a todos los proyectos de la solución.
4. Crear y ejecutar el proyecto.

Ahora debería tener una aplicación que le permite hacer clic en un botón que se incrementa un contador.

> [AZURE.NOTE] La biblioteca de cliente de almacenamiento de Azure para Xamarin es compatible actualmente con los siguientes tipos de proyecto: compartido nativa, Xamarin.Forms compartido, Xamarin.Android y Xamarin.iOS.

## <a name="create-container-and-upload-blob"></a>Crear contenedor y cargar blobs

A continuación, deberá agregar código a la clase compartida `MyClass.cs` que crea un contenedor y carga un blob en este contenedor. `MyClass.cs`debe tener un aspecto similar al siguiente:

    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Blob;
    using System.Threading.Tasks;

    namespace XamarinApp
    {
        public class MyClass
        {
            public MyClass ()
            {
            }

            public static async Task createContainerAndUpload()
            {
                // Retrieve storage account from connection string.
                CloudStorageAccount storageAccount = CloudStorageAccount.Parse("DefaultEndpointsProtocol=https;AccountName=your_account_name_here;AccountKey=your_account_key_here");

                // Create the blob client.
                CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

                // Retrieve reference to a previously created container.
                CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

                // Create the container if it doesn't already exist.
                await container.CreateIfNotExistsAsync();

                // Retrieve reference to a blob named "myblob".
                CloudBlockBlob blockBlob = container.GetBlockBlobReference("myblob");

                // Create the "myblob" blob with the text "Hello, world!"
                await blockBlob.UploadTextAsync("Hello, world!");
            }
        }
    }

Asegúrese de reemplazar "your_account_name_here" y "your_account_key_here" con el nombre de la cuenta real y la clave de cuenta. A continuación, puede usar esta clase compartida en su iOS, Android y Windows Phone aplicación. Puede agregar simplemente `MyClass.createContainerAndUpload()` a cada proyecto. Por ejemplo:

### <a name="xamarinappdroid--mainactivitycs"></a>XamarinApp.Droid > MainActivity.cs

    using Android.App;
    using Android.Widget;
    using Android.OS;

    namespace XamarinApp.Droid
    {
        [Activity (Label = "XamarinApp.Droid", MainLauncher = true, Icon = "@drawable/icon")]
        public class MainActivity : Activity
        {
            int count = 1;

            protected override async void OnCreate (Bundle bundle)
            {
                base.OnCreate (bundle);

                // Set our view from the "main" layout resource
                SetContentView (Resource.Layout.Main);

                // Get our button from the layout resource,
                // and attach an event to it
                Button button = FindViewById<Button> (Resource.Id.myButton);

                button.Click += delegate {
                    button.Text = string.Format ("{0} clicks!", count++);
                };

              await MyClass.createContainerAndUpload();
            }
        }
    }

### <a name="xamarinappios--viewcontrollercs"></a>XamarinApp.iOS > ViewController.cs

    using System;
    using UIKit;

    namespace XamarinApp.iOS
    {
        public partial class ViewController : UIViewController
        {
            int count = 1;

            public ViewController (IntPtr handle) : base (handle)
            {
            }

            public override async void ViewDidLoad ()
            {
                base.ViewDidLoad ();
                // Perform any additional setup after loading the view, typically from a nib.
                Button.AccessibilityIdentifier = "myButton";
                Button.TouchUpInside += delegate {
                    var title = string.Format ("{0} clicks!", count++);
                    Button.SetTitle (title, UIControlState.Normal);
                };

                await MyClass.createContainerAndUpload();
            }

            public override void DidReceiveMemoryWarning ()
            {
                base.DidReceiveMemoryWarning ();
                // Release any cached data, images, etc that aren't in use.
            }
        }
    }

### <a name="xamarinappwinphone--mainpagexaml--mainpagexamlcs"></a>XamarinApp.WinPhone > MainPage.xaml > MainPage.xaml.cs

    using Windows.UI.Xaml.Controls;
    using Windows.UI.Xaml.Navigation;

    // The Blank Page item template is documented at http://go.microsoft.com/fwlink/?LinkId=391641

    namespace XamarinApp.WinPhone
    {
        /// <summary>
        /// An empty page that can be used on its own or navigated to within a Frame.
        /// </summary>
        public sealed partial class MainPage : Page
        {
            int count = 1;

            public MainPage()
            {
                this.InitializeComponent();

                this.NavigationCacheMode = NavigationCacheMode.Required;
            }

            /// <summary>
            /// Invoked when this page is about to be displayed in a Frame.
            /// </summary>
            /// <param name="e">Event data that describes how this page was reached.
            /// This parameter is typically used to configure the page.</param>
            protected override async void OnNavigatedTo(NavigationEventArgs e)
            {
                // TODO: Prepare page for display here.

                // TODO: If your application contains multiple pages, ensure that you are
                // handling the hardware Back button by registering for the
                // Windows.Phone.UI.Input.HardwareButtons.BackPressed event.
                // If you are using the NavigationHelper provided by some templates,
                // this event is handled for you.
                Button.Click += delegate {
                    var title = string.Format("{0} clicks!", count++);
                    Button.Content = title;
                };

                await MyClass.createContainerAndUpload();
            }
        }
    }


## <a name="run-the-application"></a>Ejecute la aplicación

Ahora puede ejecutar esta aplicación en un emulador Android o Windows Phone. También puede ejecutar esta aplicación en un emulador de iOS, pero esto requiere un MAC. Para obtener instrucciones específicas acerca de cómo hacerlo, consulte la documentación para la [conexión de Visual Studio para Mac](https://developer.xamarin.com/guides/ios/getting_started/installation/windows/connecting-to-mac/)

Una vez que se ejecuta la aplicación, creará el contenedor `mycontainer` en su cuenta de almacenamiento. Debe contener el blob `myblob`, que contiene el texto, `Hello, world!`. Puede comprobarlo mediante el [Explorador de almacenamiento de Microsoft Azure](http://storageexplorer.com/).

## <a name="next-steps"></a>Pasos siguientes

En esta introducción, ha aprendido cómo crear una aplicación de varias plataformas en Xamarin que usa el almacenamiento de Azure. Esta introducción específicamente centrado en un escenario de almacenamiento de blobs. Sin embargo, puede hacer mucho más con, no solo el almacenamiento de blobs, pero también con tabla, archivo y almacenamiento de cola. Por favor, consulte los siguientes artículos para obtener más información:
- [Introducción a almacenamiento de blobs de Azure con .NET](storage-dotnet-how-to-use-blobs.md)
- [Introducción a Azure almacenamiento de tablas con .NET](storage-dotnet-how-to-use-tables.md)
- [Introducción a Azure almacenamiento de cola con .NET](storage-dotnet-how-to-use-queues.md)
- [Introducción a Azure almacenamiento de archivos en Windows](storage-dotnet-how-to-use-files.md)

[AZURE.INCLUDE [storage-try-azure-tools-blobs](../../includes/storage-try-azure-tools-blobs.md)]
