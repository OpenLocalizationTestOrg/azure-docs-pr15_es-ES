<properties
    pageTitle="Administración de registro"
    description="En este tema se explica cómo registrar dispositivos con hubs de notificación para recibir notificaciones de inserción."
    services="notification-hubs"
    documentationCenter=".net"
    authors="ysxu"
    manager="erikre"
    editor=""/>

<tags
    ms.service="notification-hubs"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-multiple"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="06/29/2016"
    ms.author="yuaxu"/>

# <a name="registration-management"></a>Administración de registro

##<a name="overview"></a>Información general

En este tema se explica cómo registrar dispositivos con hubs de notificación para recibir notificaciones de inserción. El tema describe los registros en un nivel superior y luego presenta los dos patrones principales para registrar dispositivos: registrar desde el dispositivo directamente en el hub de notificación y el registro a través de una aplicación de back-end. 


##<a name="what-is-device-registration"></a>¿Qué es el registro de dispositivo

Registro de dispositivo con un concentrador de notificación se realiza mediante un **registro** o **instalación**.

#### <a name="registrations"></a>Registros
Un registro asocia el controlador de servicio de notificación de plataforma (PNS) de un dispositivo con etiquetas y, posiblemente, una plantilla. El controlador PNS podría ser un ChannelURI, el token de dispositivo o el identificador de registro GCM. Las etiquetas se usan para enrutar las notificaciones para el conjunto de controladores de dispositivo correcto. Para obtener más información, vea [expresiones de etiqueta y enrutamiento](notification-hubs-tags-segment-push-message.md). Las plantillas se utilizan para implementar la transformación de cada registro. Para obtener más información, consulte [plantillas](notification-hubs-templates-cross-platform-push-messages.md).

#### <a name="installations"></a>Instalaciones
Es un mejor una instalación propiedades relacionadas con el registro que incluya una bolsa de inserción. Es el enfoque mejor y más reciente para registrar sus dispositivos. Sin embargo, no es compatible con cliente .NET SDK ([SDK de concentrador de notificación para operaciones de back-end](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/)) disponible todavía.  Esto significa que si va a registrar el propio dispositivo cliente, tendrá que utilizar el enfoque de la [API de REST de Hubs de notificación de](https://msdn.microsoft.com/library/mt621153.aspx) soporte a las instalaciones. Si usa un servicio de back-end, debe usar el [SDK de concentrador de notificación para operaciones de back-end](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/).

Las siguientes son algunas ventajas claves instalaciones:

* Crear o actualizar una instalación totalmente es idempotente. Así que puede reintentar sin tener que preocuparse de los registros duplicados.
* El modelo de instalación hace que sea fácil hacer individuales Push - identificación de dispositivo específico. Una etiqueta de sistema **"$InstallationId: [installationId]"** se agregan automáticamente con cada registro en función de instalación. Así que puede llamar a un envío a esta etiqueta de destino de un dispositivo específico sin tener que realizar programación adicional.
* Uso de las instalaciones también le permite realizar las actualizaciones de registro parcial. Se solicita la actualización parcial de una instalación con un método de revisión mediante el [estándar JSON revisión](https://tools.ietf.org/html/rfc6902). Esto es especialmente útil cuando desea actualizar etiquetas en el registro. No tienes que hacia abajo el registro completo y vuelva a enviar de nuevo en todas las etiquetas anteriores.

Puede contener una instalación de las siguientes propiedades. Para obtener una lista completa de la instalación de propiedades de ver, [crear o sobrescribir una instalación con la API de REST](https://msdn.microsoft.com/library/azure/mt621153.aspx) o [Propiedades de instalación](https://msdn.microsoft.com/library/azure/microsoft.azure.notificationhubs.installation_properties.aspx) para el.

    // Example installation format to show some supported properties
    {
        installationId: "",
        expirationTime: "",
        tags: [],
        platform: "",
        pushChannel: "",
        ………
        templates: {
            "templateName1" : {
                body: "",
                tags: [] },
            "templateName2" : {
                body: "",
                // Headers are for Windows Store only
                headers: {
                    "X-WNS-Type": "wns/tile" }
                tags: [] }
        },
        secondaryTiles: {
            "tileId1": {
                pushChannel: "",
                tags: [],
                templates: {
                    "otherTemplate": {
                        bodyTemplate: "",
                        headers: {
                            ... }
                        tags: [] }
                }
            }
        }
    }

 

Es importante que tenga en cuenta que ya no expiren registros y las instalaciones de forma predeterminada.

Registros y las instalaciones deben contener un identificador PNS válido para cada canal de dispositivo. Porque los controladores de PNS sólo se puede obtener en una aplicación de cliente en el dispositivo, una trama es registrar directamente en ese dispositivo con la aplicación de cliente. Por otro lado, consideraciones de seguridad y lógica de negocios relacionadas con etiquetas necesite administrar registro de dispositivo en la aplicación back-end. 

#### <a name="templates"></a>Plantillas

Si desea usar [plantillas](notification-hubs-templates-cross-platform-push-messages.md), la instalación del dispositivo mantenga también todas las plantillas asociadas con el dispositivo en un JSON formato (consulte el ejemplo anterior). Los nombres de plantilla ayudan diferentes plantillas para el mismo dispositivo de destino.

Tenga en cuenta que cada nombre de la plantilla de mapas en el cuerpo de una plantilla y un conjunto opcional de etiquetas. Además, cada plataforma puede tener propiedades adicionales de la plantilla. Para la tienda Windows (mediante WNS) y Windows Phone 8 (mediante MPNS), un conjunto adicional de encabezados puede ser parte de la plantilla. En el caso de APN, puede establecer una propiedad de expiración para una constante o una expresión de plantilla. Para una lista completa de la instalación de propiedades, vea [crear o sobrescribir una instalación con el resto](https://msdn.microsoft.com/library/azure/mt621153.aspx) de tema.

#### <a name="secondary-tiles-for-windows-store-apps"></a>Mosaicos secundarios para las aplicaciones de la tienda Windows

Para las aplicaciones de cliente de la tienda Windows, enviar notificaciones a mosaicos secundarios es igual a enviarlos a la principal. Esto también es compatible con las instalaciones. Mosaicos secundarios debe tener un ChannelUri diferentes, que el SDK en la aplicación cliente controla transparente.

El diccionario de SecondaryTiles utiliza el mismo TileId que se utiliza para crear el objeto SecondaryTiles en su aplicación de la tienda de Windows.
Como ocurre con la principal ChannelUri, ChannelUris de mosaicos secundarios puede cambiar en cualquier momento. Para mantener las instalaciones en el hub de notificación que se actualiza, el dispositivo debe actualizarlos con la ChannelUris actual de los mosaicos secundarios.


##<a name="registration-management-from-the-device"></a>Administración de registro del dispositivo

Al administrar el registro de dispositivo de aplicaciones cliente, el servidor sólo es responsable de enviar notificaciones. Aplicaciones cliente mantener actualizada PNS controladores y registrar etiquetas. La imagen siguiente muestra este modelo.

![](./media/notification-hubs-registration-management/notification-hubs-registering-on-device.png)

En primer lugar, el dispositivo recupera el controlador PNS desde el PNS y luego registra directamente con el concentrador de notificación. Una vez completado el registro, el servidor de la aplicación puede enviar una notificación de que el registro de destino. Para obtener más información sobre cómo enviar notificaciones, consulte [enrutamiento y expresiones de etiqueta](notification-hubs-tags-segment-push-message.md).
Tenga en cuenta que en este caso, usará escuchar sólo derechos de acceso a su hubs notificación desde el dispositivo. Para obtener más información, vea [seguridad](notification-hubs-push-notification-security.md).

Registrar desde el dispositivo es el método más sencillo, pero tiene algunos inconvenientes.
La primera desventaja es que una aplicación de cliente solo puede actualizar sus etiquetas cuando la aplicación esté activa. Por ejemplo, si un usuario tiene dos dispositivos que registrar etiquetas relacionadas con equipos de deportes, cuando se registra el primer dispositivo para una etiqueta adicional (por ejemplo, Seahawks), el segundo dispositivo no recibirá las notificaciones sobre la Seahawks hasta que la aplicación en el segundo dispositivo se ejecuta una segunda vez. Por lo general, cuando las etiquetas se ven afectadas por varios dispositivos, administrar etiquetas del servidor es una opción conveniente.
La segunda desventaja de administración de registro de la aplicación cliente es que, dado que pueden ser pirateadas aplicaciones, proteger el registro a etiquetas específicas requiere atención adicional, como se explica en la sección "seguridad a nivel de la etiqueta".



#### <a name="example-code-to-register-with-a-notification-hub-from-a-device-using-an-installation"></a>Código de ejemplo para registrar con un concentrador de notificación de un dispositivo con una instalación 

En este momento, solo es compatible con la [API de REST de Hubs de notificación](https://msdn.microsoft.com/library/mt621153.aspx).

También puede usar el método de revisión mediante la [revisión de JSON estándar](https://tools.ietf.org/html/rfc6902) para actualizar la instalación.

    class DeviceInstallation
    {
        public string installationId { get; set; }
        public string platform { get; set; }
        public string pushChannel { get; set; }
        public string[] tags { get; set; }
    }

    private async Task<HttpStatusCode> CreateOrUpdateInstallationAsync(DeviceInstallation deviceInstallation,
         string hubName, string listenConnectionString)
    {
        if (deviceInstallation.installationId == null)
            return HttpStatusCode.BadRequest;

        // Parse connection string (https://msdn.microsoft.com/library/azure/dn495627.aspx)
        ConnectionStringUtility connectionSaSUtil = new ConnectionStringUtility(listenConnectionString);
        string hubResource = "installations/" + deviceInstallation.installationId + "?";
        string apiVersion = "api-version=2015-04";

        // Determine the targetUri that we will sign
        string uri = connectionSaSUtil.Endpoint + hubName + "/" + hubResource + apiVersion;

        //=== Generate SaS Security Token for Authorization header ===
        // See, https://msdn.microsoft.com/library/azure/dn495627.aspx
        string SasToken = connectionSaSUtil.getSaSToken(uri, 60);

        using (var httpClient = new HttpClient())
        {
            string json = JsonConvert.SerializeObject(deviceInstallation);

            httpClient.DefaultRequestHeaders.Add("Authorization", SasToken);

            var response = await httpClient.PutAsync(uri, new StringContent(json, System.Text.Encoding.UTF8, "application/json"));
            return response.StatusCode;
        }
    }

    var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();

    string installationId = null;
    var settings = ApplicationData.Current.LocalSettings.Values;

    // If we have not stored a installation id in application data, create and store as application data.
    if (!settings.ContainsKey("__NHInstallationId"))
    {
        installationId = Guid.NewGuid().ToString();
        settings.Add("__NHInstallationId", installationId);
    }

    installationId = (string)settings["__NHInstallationId"];

    var deviceInstallation = new DeviceInstallation
    {
        installationId = installationId,
        platform = "wns",
        pushChannel = channel.Uri,
        //tags = tags.ToArray<string>()
    };

    var statusCode = await CreateOrUpdateInstallationAsync(deviceInstallation, 
                        "<HUBNAME>", "<SHARED LISTEN CONNECTION STRING>");

    if (statusCode != HttpStatusCode.Accepted)
    {
        var dialog = new MessageDialog(statusCode.ToString(), "Registration failed. Installation Id : " + installationId);
        dialog.Commands.Add(new UICommand("OK"));
        await dialog.ShowAsync();
    }
    else
    {
        var dialog = new MessageDialog("Registration successful using installation Id : " + installationId);
        dialog.Commands.Add(new UICommand("OK"));
        await dialog.ShowAsync();
    }

   

#### <a name="example-code-to-register-with-a-notification-hub-from-a-device-using-a-registration"></a>Código de ejemplo para registrar con un concentrador de notificación de un dispositivo con un registro


Estos métodos crean o actualización un registro para el dispositivo en el que se llama. Esto significa que para actualizar el controlador o las etiquetas, debe sobrescribir el registro completo. Recuerde que los registros son transitorios, por lo que debería tener siempre un almacén confiable con las etiquetas actuales que necesita un dispositivo específico.


    // Initialize the Notification Hub
    NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString(listenConnString, hubName);

    // The Device id from the PNS
    var pushChannel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();

    // If you are registering from the client itself, then store this registration id in device
    // storage. Then when the app starts, you can check if a registration id already exists or not before
    // creating.
    var settings = ApplicationData.Current.LocalSettings.Values;

    // If we have not stored a registration id in application data, store in application data.
    if (!settings.ContainsKey("__NHRegistrationId"))
    {
        // make sure there are no existing registrations for this push handle (used for iOS and Android)    
        string newRegistrationId = null;
        var registrations = await hub.GetRegistrationsByChannelAsync(pushChannel.Uri, 100);
        foreach (RegistrationDescription registration in registrations)
        {
            if (newRegistrationId == null)
            {
                newRegistrationId = registration.RegistrationId;
            }
            else
            {
                await hub.DeleteRegistrationAsync(registration);
            }
        }

        newRegistrationId = await hub.CreateRegistrationIdAsync();

        settings.Add("__NHRegistrationId", newRegistrationId);
    }
     
    string regId = (string)settings["__NHRegistrationId"];

    RegistrationDescription registration = new WindowsRegistrationDescription(pushChannel.Uri);
    registration.RegistrationId = regId;
    registration.Tags = new HashSet<string>(YourTags);

    try
    {
        await hub.CreateOrUpdateRegistrationAsync(registration);
    }
    catch (Microsoft.WindowsAzure.Messaging.RegistrationGoneException e)
    {
        settings.Remove("__NHRegistrationId");
    }


## <a name="registration-management-from-a-backend"></a>Administración de registro de back-end

Administración de registros del servidor requiere escribir código adicional. La aplicación desde el dispositivo debe proporcionar la PNS actualizados controlador del back-end cada vez que inicie la aplicación (junto con plantillas y etiquetas) y el back-end debe actualizar este controlador en el hub de notificación. La imagen siguiente muestra este diseño.

![](./media/notification-hubs-registration-management/notification-hubs-registering-on-backend.png)

Las ventajas de la administración de registros del servidor incluyen la capacidad para modificar etiquetas a los registros, incluso cuando la aplicación correspondiente en el dispositivo está inactiva y para autenticar la aplicación cliente antes de agregar una etiqueta a su registro.


#### <a name="example-code-to-register-with-a-notification-hub-from-a-backend-using-an-installation"></a>Código de ejemplo para registrar con un concentrador de notificación de back-end mediante la instalación

El dispositivo cliente aún Obtiene el controlador PNS y propiedades de instalación relevantes como antes y llama a una API personalizada en el servidor que puede realizar el registro y autorizar etiquetas etcetera. El back-end puede aprovechar el [SDK de concentrador de notificación para operaciones de back-end](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/).

También puede usar el método de revisión mediante la [revisión de JSON estándar](https://tools.ietf.org/html/rfc6902) para actualizar la instalación.
 

    // Initialize the Notification Hub
    NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString(listenConnString, hubName);

    // Custom API on the backend
    public async Task<HttpResponseMessage> Put(DeviceInstallation deviceUpdate)
    {

        Installation installation = new Installation();
        installation.InstallationId = deviceUpdate.InstallationId;
        installation.PushChannel = deviceUpdate.Handle;
        installation.Tags = deviceUpdate.Tags;

        switch (deviceUpdate.Platform)
        {
            case "mpns":
                installation.Platform = NotificationPlatform.Mpns;
                break;
            case "wns":
                installation.Platform = NotificationPlatform.Wns;
                break;
            case "apns":
                installation.Platform = NotificationPlatform.Apns;
                break;
            case "gcm":
                installation.Platform = NotificationPlatform.Gcm;
                break;
            default:
                throw new HttpResponseException(HttpStatusCode.BadRequest);
        }


        // In the backend we can control if a user is allowed to add tags
        //installation.Tags = new List<string>(deviceUpdate.Tags);
        //installation.Tags.Add("username:" + username);

        await hub.CreateOrUpdateInstallationAsync(installation);

        return Request.CreateResponse(HttpStatusCode.OK);
    }


#### <a name="example-code-to-register-with-a-notification-hub-from-a-device-using-a-registration-id"></a>Código de ejemplo para registrar con un concentrador de notificación de un dispositivo con un identificador de registro

Desde su aplicación de back-end, puede realizar operaciones de CRUDS básicas en los registros. Por ejemplo:

    var hub = NotificationHubClient.CreateClientFromConnectionString("{connectionString}", "hubName");
            
    // create a registration description object of the correct type, e.g.
    var reg = new WindowsRegistrationDescription(channelUri, tags);

    // Create
    await hub.CreateRegistrationAsync(reg);

    // Get by id
    var r = await hub.GetRegistrationAsync<RegistrationDescription>("id");

    // update
    r.Tags.Add("myTag");

    // update on hub
    await hub.UpdateRegistrationAsync(r);

    // delete
    await hub.DeleteRegistrationAsync(r);


El back-end debe controlar simultaneidad entre las actualizaciones de registro. Bus de servicio ofrece control de simultaneidad optimista para la administración de registro. En el nivel HTTP, se ha implementado con el uso de ETag en las operaciones de administración de registro. Esta característica se usa transparente en Microsoft SDKs, que inicia una excepción si se rechaza una actualización por motivos de simultaneidad. El servidor de la aplicación es responsable de control de estas excepciones y volver a intentar la actualización, si es necesario.