<properties
    pageTitle="Notificaciones de inserción bloqueado geo con Hubs de notificación de Azure y datos de Bing espacial | Microsoft Azure"
    description="En este tutorial, aprenderá cómo ofrecer notificaciones de inserción según ubicación con Hubs de notificación de Azure y datos espacial de Bing."
    services="notification-hubs"
    documentationCenter="windows"
    keywords="notificación de inserción, notificación de inserción"
    authors="dend"
    manager="yuaxu"
    editor="dend"/>

<tags
    ms.service="notification-hubs"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-windows-phone"
    ms.devlang="dotnet"
    ms.topic="hero-article"
    ms.date="05/31/2016"
    ms.author="dendeli"/>
    
# <a name="geo-fenced-push-notifications-with-azure-notification-hubs-and-bing-spatial-data"></a>Notificaciones de inserción bloqueado geo con Hubs de notificación de Azure y datos espacial de Bing
 
 > [AZURE.NOTE] Para completar este tutorial, debe tener una cuenta de Azure active. Si no tiene una cuenta, puede crear una cuenta de prueba gratuita de dos minutos. Para obtener información detallada, vea [Prueba gratuita de Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02).

En este tutorial, aprenderá cómo ofrecer notificaciones de inserción según ubicación con Hubs de notificación de Azure y datos de Bing espacial, aprovechar desde dentro de una aplicación de la plataforma de Windows Universal.

##<a name="prerequisites"></a>Requisitos previos
Primero y más importante, debe asegurarse de que tiene todos los servicios y software requisitos previos:

* [1 de actualización de Visual Studio 2015](https://www.visualstudio.com/en-us/downloads/download-visual-studio-vs.aspx) o posterior ([Community Edition](https://go.microsoft.com/fwlink/?LinkId=691978&clcid=0x409) hará también). 
* Versión más reciente del [SDK de Azure](https://azure.microsoft.com/downloads/). 
* [Cuenta de centro de desarrollo de mapas de Bing](https://www.bingmapsportal.com/) (puede crearla gratuitamente y asociar con su cuenta de Microsoft). 

##<a name="getting-started"></a>Introducción

Empecemos creando el proyecto. En Visual Studio, inicie un nuevo proyecto de tipo **Aplicación en blanco (Windows Universal)**.

![](./media/notification-hubs-geofence/notification-hubs-create-blank-app.png)

Una vez completada la creación del proyecto, debería tener el instrumento para la aplicación. Ahora vamos a configurar todo el contenido de la infraestructura de geo barrera. Porque vamos a usar los servicios de Bing para esto, hay un extremo de API de REST público que nos permite marcos ubicación específica de la consulta:

    http://spatial.virtualearth.net/REST/v1/data/
    
Debe especificar los parámetros siguientes para que funcione:

* **Id. de origen de datos** y **El nombre del origen de datos** : en la API de Bing Maps, orígenes de datos contienen metadatos periodificado por diversos, como ubicaciones y horario de operación. Puede obtener más información acerca de los empleados. 
* **Nombre de la entidad** : la entidad que desea usar como punto de referencia para la notificación. 
* **Clave de API de mapas de Bing** : esta es la clave que obtiene con una versión anterior, al crear la cuenta del centro de desarrollo de Bing.
 
Vamos a hacer un análisis más profundo de la configuración para cada uno de los elementos anteriores.

##<a name="setting-up-the-data-source"></a>Configurar el origen de datos

Puede hacerlo en el centro de desarrollo de mapas de Bing. Simplemente haga clic en **orígenes de datos** en la barra de navegación y seleccione **Administrar orígenes de datos**.

![](./media/notification-hubs-geofence/bing-maps-manage-data.png)

Si no ha trabajado con la API de Bing Maps antes, probablemente no habrá los orígenes de datos actual, por lo que solo puede crear una nueva haciendo clic en cargar datos a un origen de datos. Asegúrese de que rellenar todos los campos necesarios:

![](./media/notification-hubs-geofence/bing-maps-create-data.png)

Es posible que se pregunte: ¿qué es el archivo de datos y lo que debe que se pueden cargar? Para los efectos de esta prueba, podemos utilizar el ejemplo basado en la canalización de un área de al mar de San Francisco de marcos:

    Bing Spatial Data Services, 1.0, TestBoundaries
    EntityID(Edm.String,primaryKey)|Name(Edm.String)|Longitude(Edm.Double)|Latitude(Edm.Double)|Boundary(Edm.Geography)
    1|SanFranciscoPier|||POLYGON ((-122.389825 37.776598,-122.389438 37.773087,-122.381885 37.771849,-122.382186 37.777022,-122.389825 37.776598))
    
Las respuestas anteriores representa esta entidad:

![](./media/notification-hubs-geofence/bing-maps-geofence.png)

Simplemente copiar y pegar la cadena anterior en un nuevo archivo y guárdelo como **NotificationHubsGeofence.pipe**y cargar en el centro de desarrollo de Bing.

>[AZURE.NOTE]Puede que deba especificar una nueva clave para la **clave principal** que es diferente de la **Clave de consulta**. Cree una nueva clave a través del panel y actualice la página de carga del origen de datos.

Una vez que cargue el archivo de datos, debe asegurarse de que se publica el origen de datos. 

Vaya a **Administrar orígenes de datos**, al igual que hizo anteriormente, busque el origen de datos en la lista y haga clic en **Publicar** en la columna **acciones** . En un poco, debería ver el origen de datos en la pestaña **Publicar orígenes de datos** :

![](./media/notification-hubs-geofence/bing-maps-published-data.png)

Si hace clic en **Editar**, podrá ver de un vistazo qué ubicaciones presentamos en él:

![](./media/notification-hubs-geofence/bing-maps-data-details.png)

En este momento, el portal no muestran los límites de la geofence que creamos: todo lo que necesitamos es una confirmación de que la ubicación especificada es proximidad derecha.

Ahora tiene todos los requisitos para el origen de datos. Para obtener los detalles en la dirección URL de la solicitud para la llamada API, en el centro de desarrollo de mapas de Bing, haga clic en **orígenes de datos** y seleccione **Información de origen de datos**.

![](./media/notification-hubs-geofence/bing-maps-data-info.png)

La **Dirección URL de la consulta** es lo que estamos después aquí. Éste es el extremo que podemos ejecutar consultas para comprobar si el dispositivo está actualmente dentro de los límites de una ubicación, o no. Para realizar esta comprobación, simplemente debemos realizar una llamada GET con la URL de la consulta, con los siguientes parámetros anexados:

    ?spatialFilter=intersects(%27POINT%20LONGITUDE%20LATITUDE)%27)&$format=json&key=QUERY_KEY

De este modo es que especifica un punto de destino que se obtienen desde el dispositivo y mapas de Bing realizará automáticamente los cálculos para ver si está dentro de la geofence. Una vez que se ejecuta la solicitud a través de un explorador (o doblez), obtendrá estándar una respuesta JSON:

![](./media/notification-hubs-geofence/bing-maps-json.png)

Esta respuesta sólo sucede cuando el punto realmente está dentro de los límites designados. Si no es así, obtendrá un depósito vacíos **resultados** :

![](./media/notification-hubs-geofence/bing-maps-nores.png)

##<a name="setting-up-the-uwp-application"></a>Configurar la aplicación UWP

Ahora que tenemos el origen de datos listo, nos podemos comenzar a trabajar en la aplicación de UWP que nos arranque anteriormente.

En primer lugar y lugar, debemos habilitar servicios de ubicación para la aplicación. Para ello, haga doble clic en `Package.appxmanifest` archivo en el **Explorador de soluciones**.

![](./media/notification-hubs-geofence/vs-package-manifest.png)

En la pestaña Propiedades de paquete que acaba de abrir, haga clic en **funciones** y asegúrese de seleccionar **ubicación**:

![](./media/notification-hubs-geofence/vs-package-location.png)

La capacidad de ubicación que se declara, cree una nueva carpeta en la solución denominada `Core`y agregar un nuevo archivo dentro de ella, denominado `LocationHelper.cs`:

![](./media/notification-hubs-geofence/vs-location-helper.png)

La `LocationHelper` propia clase es bastante básica en este momento: todo lo que hace es que nos permite obtener la ubicación de usuario a través de la API del sistema:

    using System;
    using System.Threading.Tasks;
    using Windows.Devices.Geolocation;

    namespace NotificationHubs.Geofence.Core
    {
        public class LocationHelper
        {
            private static readonly uint AppDesiredAccuracyInMeters = 10;

            public async static Task<Geoposition> GetCurrentLocation()
            {
                var accessStatus = await Geolocator.RequestAccessAsync();
                switch (accessStatus)
                {
                    case GeolocationAccessStatus.Allowed:
                        {
                            Geolocator geolocator = new Geolocator { DesiredAccuracyInMeters = AppDesiredAccuracyInMeters };

                            return await geolocator.GetGeopositionAsync();
                        }
                    default:
                        {
                            return null;
                        }
                }
            }

        }
    }

Puede obtener más información sobre cómo obtener la ubicación del usuario en las aplicaciones UWP en el [documento MSDN](https://msdn.microsoft.com/library/windows/apps/mt219698.aspx)de oficial.

Para comprobar que realmente está funcionando la adquisición de ubicación, abra el lado de código de la página principal (`MainPage.xaml.cs`). Crear un nuevo controlador de eventos para los `Loaded` evento en la `MainPage` constructor:

    public MainPage()
    {
        this.InitializeComponent();
        this.Loaded += MainPage_Loaded;
    }

La implementación del controlador de eventos es la siguiente:

    private async void MainPage_Loaded(object sender, RoutedEventArgs e)
    {
        var location = await LocationHelper.GetCurrentLocation();

        if (location != null)
        {
            Debug.WriteLine(string.Concat(location.Coordinate.Longitude,
                " ", location.Coordinate.Latitude));
        }
    }

Observe que se declare el controlador como asincrónica porque `GetCurrentLocation` es esperable y, por tanto, se requiere que se ejecute en un contexto asincrónico. Además, porque en determinadas circunstancias podríamos termina con una ubicación nula (por ejemplo, la ubicación de los servicios están deshabilitados o la aplicación se ha denegado permisos para la ubicación de acceso), es necesario para asegurarse de que se administran correctamente con una comprobación de null.

Ejecute la aplicación. Asegúrese de que permitir el acceso de ubicación:

![](./media/notification-hubs-geofence/notification-hubs-location-access.png)

Una vez iniciará la aplicación, debería poder ver las coordenadas en la ventana de **resultados** :

![](./media/notification-hubs-geofence/notification-hubs-location-output.png)

Ahora ya sabe que funciona de adquisición de ubicación: no dude en quitar el controlador de eventos de prueba para cargado debido a no esté utilizando ya.

El siguiente paso es capturar los cambios de ubicación. Para ello, volvamos a la `LocationHelper` de clase y agregue el controlador de eventos `PositionChanged`:

    geolocator.PositionChanged += Geolocator_PositionChanged;

La implementación mostrará las coordenadas de ubicación en la ventana de **resultados** :

    private static async void Geolocator_PositionChanged(Geolocator sender, PositionChangedEventArgs args)
    {
        await CoreApplication.MainView.CoreWindow.Dispatcher.RunAsync(CoreDispatcherPriority.Normal, () =>
        {
            Debug.WriteLine(string.Concat(args.Position.Coordinate.Longitude, " ", args.Position.Coordinate.Latitude));
        });
    }

##<a name="setting-up-the-backend"></a>Configurar el back-end

Descargue el [Ejemplo de back-end de .NET desde GitHub](https://github.com/Azure/azure-notificationhubs-samples/tree/master/dotnet/NotifyUsers). Una vez completada la descarga, abra la `NotifyUsers` carpeta y, posteriormente, la `NotifyUsers.sln` archivo.

Establecer el `AppBackend` de proyecto como el **Proyecto de inicio** y ábrala.

![](./media/notification-hubs-geofence/vs-startup-project.png)

El proyecto ya está configurado para enviar notificaciones de inserción a dispositivos de destino, por lo que tendrá que hacer dos cosas: cambiar la conexión adecuada para el concentrador de notificación de cadena y agregar identificación del límite para enviar la notificación solo cuando el usuario está dentro de la geofence.

Para configurar la cadena de conexión en la `Models` carpeta abierta `Notifications.cs`. La `NotificationHubClient.CreateClientFromConnectionString` función debe contener la información sobre su centro de notificación que puede obtener en el [Portal de Azure](https://portal.azure.com) (vistazo el módulo de **Directivas de acceso** en la **configuración**). Guarde el archivo de configuración actualizado.

Ahora es necesario crear un modelo para el resultado de la API de Bing Maps. La manera más sencilla de hacerlo es haga clic en el `Models` carpeta, **Agregar** > **clase**. Asígnele el nombre `GeofenceBoundary.cs`. Una vez hecho, copie el JSON de la respuesta de la API que explicamos en la primera sección y en **Editar**de uso de Visual Studio > **Pegado especial** > **JSON Pegar como clases**. 

De este modo que garantizamos que se van a deserializar el objeto tal y como estaba previsto. El conjunto de clase resultante debe ser similar a esta:

    namespace AppBackend.Models
    {
        public class Rootobject
        {
            public D d { get; set; }
        }

        public class D
        {
            public string __copyright { get; set; }
            public Result[] results { get; set; }
        }

        public class Result
        {
            public __Metadata __metadata { get; set; }
            public string EntityID { get; set; }
            public string Name { get; set; }
            public float Longitude { get; set; }
            public float Latitude { get; set; }
            public string Boundary { get; set; }
            public string Confidence { get; set; }
            public string Locality { get; set; }
            public string AddressLine { get; set; }
            public string AdminDistrict { get; set; }
            public string CountryRegion { get; set; }
            public string PostalCode { get; set; }
        }

        public class __Metadata
        {
            public string uri { get; set; }
        }
    }

A continuación, abra `Controllers`  >  `NotificationsController.cs`. Es necesario ajustar la llamada de entrada a la cuenta para la longitud de destino y latitud. Para ello, agregue simplemente dos cadenas a la firma de la función: `latitude` y `longitude`.

    public async Task<HttpResponseMessage> Post(string pns, [FromBody]string message, string to_tag, string latitude, string longitude)

Crear una nueva clase en el proyecto denominado `ApiHelper.cs` – usará para conectarse a Bing para comprobar seleccione intersecciones de borde. Implementar un `IsPointWithinBounds` función similar a esta:

    public class ApiHelper
    {
        public static readonly string ApiEndpoint = "{YOUR_QUERY_ENDPOINT}?spatialFilter=intersects(%27POINT%20({0}%20{1})%27)&$format=json&key={2}";
        public static readonly string ApiKey = "{YOUR_API_KEY}";

        public static bool IsPointWithinBounds(string longitude,string latitude)
        {
            var json = new WebClient().DownloadString(string.Format(ApiEndpoint, longitude, latitude, ApiKey));
            var result = JsonConvert.DeserializeObject<Rootobject>(json);
            if (result.d.results != null && result.d.results.Count() > 0)
            {
                return true;
            }
            else
            {
                return false;
            }
        }
    }

>[AZURE.NOTE] Asegúrese de sustituir el extremo de la API con la dirección URL de consulta que obtuvo anteriormente desde el centro de desarrollo de Bing (mismo se aplica a la clave de API). 

Si hay resultados de la consulta, esto significa que el punto especificado está dentro de los límites de la geofence, por lo que se devuelven `true`. Si no hay ningún resultado, Bing nos indica que el punto está fuera del marco de la búsqueda, por lo que se devuelven `false`.

En `NotificationsController.cs`, cree una comprobación justo antes de la instrucción switch:

    if (ApiHelper.IsPointWithinBounds(longitude, latitude))
    {
        switch (pns.ToLower())
        {
            case "wns":
                //// Windows 8.1 / Windows Phone 8.1
                var toast = @"<toast><visual><binding template=""ToastText01""><text id=""1"">" +
                            "From " + user + ": " + message + "</text></binding></visual></toast>";
                outcome = await Notifications.Instance.Hub.SendWindowsNativeNotificationAsync(toast, userTag);

                // Windows 10 specific Action Center support
                toast = @"<toast><visual><binding template=""ToastGeneric""><text id=""1"">" +
                            "From " + user + ": " + message + "</text></binding></visual></toast>";
                outcome = await Notifications.Instance.Hub.SendWindowsNativeNotificationAsync(toast, userTag);

                break;
        }
    }

De este modo, la notificación sólo se envía cuando el punto está dentro de los límites.

##<a name="testing-push-notifications-in-the-uwp-app"></a>Probar las notificaciones de inserción en la aplicación UWP

Volver a la aplicación UWP, debemos ahora poder probar las notificaciones. Dentro de la `LocationHelper` de clase, cree una nueva función – `SendLocationToBackend`:

    public static async Task SendLocationToBackend(string pns, string userTag, string message, string latitude, string longitude)
    {
        var POST_URL = "http://localhost:8741/api/notifications?pns=" +
            pns + "&to_tag=" + userTag + "&latitude=" + latitude + "&longitude=" + longitude;

        using (var httpClient = new HttpClient())
        {
            try
            {
                await httpClient.PostAsync(POST_URL, new StringContent("\"" + message + "\"",
                    System.Text.Encoding.UTF8, "application/json"));
            }
            catch (Exception ex)
            {
                Debug.WriteLine(ex.Message);
            }
        }
    }

>[AZURE.NOTE] Intercambiar el `POST_URL` a la ubicación de la aplicación web implementado que hemos creado en la sección anterior. Por ahora, está bien para ejecutarlo localmente, pero mientras trabaja en implementar una versión pública, debe hospedar con un proveedor de servicios externo.

Veamos ahora Asegúrese de que se registre la aplicación UWP notificaciones de inserción. En Visual Studio, haga clic en **proyecto** > **almacenar** > **asociar la aplicación con la tienda**.

![](./media/notification-hubs-geofence/vs-associate-with-store.png)

Una vez que inicie sesión su cuenta de desarrollador, asegúrese de seleccionar una aplicación existente o cree uno nuevo y asociar el paquete. 

Vaya al centro de desarrollo y abra la aplicación que acaba de crear. Haga clic en **Servicios** > **Notificaciones Push** > **sitio Live Services**.

![](./media/notification-hubs-geofence/ms-live-services.png)

En el sitio, tome nota de la **Aplicación secreto** y el **Paquete SID**. Necesitará tanto en el Portal de Azure, abra su centro de notificación, haga clic en **configuración de** > **Notification Services** > **Windows (WNS)** y escriba la información en los campos necesarios.

![](./media/notification-hubs-geofence/notification-hubs-wns.png)

Haga clic en **Guardar**.

Haga clic con el botón secundario en **referencias** en el **Explorador de soluciones** y seleccione **Administrar paquetes de NuGet**. Es necesario agregar una referencia a la **biblioteca de Bus de servicio de Microsoft Azure administrada** : simplemente buscar `WindowsAzure.Messaging.Managed` y lo agrega a su proyecto.

![](./media/notification-hubs-geofence/vs-nuget.png)

Con fines de pruebas, podemos crear la `MainPage_Loaded` una vez más, el controlador de eventos y agregue este fragmento de código:

    var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();

    var hub = new NotificationHub("HUB_NAME", "HUB_LISTEN_CONNECTION_STRING");
    var result = await hub.RegisterNativeAsync(channel.Uri);

    // Displays the registration ID so you know it was successful
    if (result.RegistrationId != null)
    {
        Debug.WriteLine("Reg successful.");
    }

Las respuestas anteriores registra la aplicación con el hub de notificación. Está listo. 

En `LocationHelper`, dentro del `Geolocator_PositionChanged` controlador, puede agregar un fragmento de código de prueba que fuerza colocará la ubicación dentro de la geofence:

    await LocationHelper.SendLocationToBackend("wns", "TEST_USER", "TEST", "37.7746", "-122.3858");

Porque no está pasando las coordenadas reales (que no esté dentro de los límites en el momento) y está utilizando los valores de prueba predefinidas, veremos una notificación que se muestran en la actualización:

![](./media/notification-hubs-geofence/notification-hubs-test-notification.png)

##<a name="whats-next"></a>¿Qué es el siguiente?

Hay un par de pasos que debe seguir además de los anteriores para asegurarse de que la solución está lista para producción.

Primero y más importante, debe asegurarse de que son dinámicos geofences. Se necesita un trabajo adicional con la API de Bing para poder cargar nuevos límites en el origen de datos existente. Consulte la [documentación de la API de servicios de datos espacial de Bing](https://msdn.microsoft.com/library/ff701734.aspx) para obtener más detalles sobre el asunto.

En segundo término, mientras está trabajando para asegurarse de que se ha realizado la entrega a los participantes de la derecha, es recomendable establecerlas como destino a través de [etiquetado](notification-hubs-tags-segment-push-message.md).

La solución que se muestra sobre describe un escenario en el que es posible que tenga una amplia variedad de plataformas de destino, por lo que no se limitar el geofencing a las funciones específicas del sistema. Dicho esto, la plataforma de Windows Universal ofrece capacidades para [detectar geofences derecha - de-predefinidas](https://msdn.microsoft.com/windows/uwp/maps-and-location/set-up-a-geofence).

Para obtener más detalles sobre las capacidades de Hubs de notificación, consulte nuestro [portal de documentación](https://azure.microsoft.com/documentation/services/notification-hubs/).
