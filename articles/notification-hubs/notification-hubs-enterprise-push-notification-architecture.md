<properties
    pageTitle="Notificación Hubs - arquitectura de inserción de empresa"
    description="Instrucciones sobre el uso de Hubs de notificación de Azure en un entorno empresarial"
    services="notification-hubs"
    documentationCenter=""
    authors="ysxu"
    manager="erikre"
    editor=""/>

<tags
    ms.service="notification-hubs"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-windows"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="06/29/2016" 
    ms.author="yuaxu"/>

# <a name="enterprise-push-architectural-guidance"></a>Guía de arquitectura de inserción de empresa

Las empresas actuales son gradualmente mover hacia crear aplicaciones móviles para cualquiera de sus usuarios finales (externo) o para los empleados (internos). Tienen sistemas de back-end existentes en su lugar sea mainframe o algunas aplicaciones de línea de negocio que deben estar integrados en la arquitectura de la aplicación móvil. Esta guía le hablar sobre la mejor forma de hacer esta integración recomendar solución posible para escenarios comunes.

Un requisito frecuente es enviar notificación de inserción a los usuarios a través de sus aplicaciones móviles cuando se produce un evento de interés en los sistemas de back-end. Por ejemplo un cliente de banco que tiene la aplicación de bancario del banco en su iPhone desea recibir una notificación cuando se realiza un débito encima de una determinada cantidad de su cuenta en un escenario de intranet en la que un empleado del departamento de finanzas que tiene una aplicación de aprobación del presupuesto en su Windows Phone desea recibir una notificación cuando se recibe una solicitud de aprobación.

El banco o el proceso de aprobación es probable que deben realizarse en algunos sistema back-end que debe iniciar inserción para el usuario. Puede haber varias dichos sistemas de back-end que deben crear el mismo tipo de lógica para implementar inserción cuando un evento activa una notificación de todos. La complejidad aquí se encuentra en la integración de varios sistemas de back-end junto con un sistema de inserción única donde los usuarios finales puede suscribirse a diferentes notificaciones e incluso puede haber varias aplicaciones móviles, por ejemplo, en el caso de aplicaciones móviles de intranet en una aplicación móvil puede que desee recibir notificaciones de varios tales sistemas de back-end. Los sistemas de back-end no conoce o debe saber de semántica/tecnología de inserción para que una solución común aquí tradicionalmente ha sido introducir un componente que explora los sistemas de back-end para los eventos de interés y es responsable de enviar los mensajes de inserción al cliente.
A continuación hablaremos sobre una solución aún mejor con Bus de servicio de Azure - modelo de tema o suscripción que disminuirá la complejidad mientras realiza la solución scalable.

Aquí es la arquitectura general de la solución (generalizado con varias aplicaciones móviles pero igualmente aplicable cuando hay solo una aplicación móvil)

## <a name="architecture"></a>Arquitectura

![][1]

La pieza clave en este diagrama de la arquitectura es Bus de servicio de Azure que proporciona un modelo (más a la [programación de Bus de servicio Pub/Sub]) de programación de temas/suscripciones. El receptor, que en este caso, es el móvil back-end (normalmente [Servicio móvil de Azure], que se inicie una instalación remota a las aplicaciones móviles) no recibe mensajes directamente desde los sistemas de back-end, pero en su lugar tenemos una capa de abstracción intermedio proporcionada por [Bus de servicio de Azure] que permite móvil back-end recibir mensajes de uno o varios de los sistemas de back-end. Un tema de Bus de servicio debe crearse para cada uno de los sistemas de back-end, por ejemplo, cuenta, recursos humanos, finanzas que son básicamente "temas" de interés que se inician los mensajes se envíen como notificación de inserción. Los sistemas de back-end enviará mensajes a estos temas. Mobile back-end puede suscribirse a uno o más temas mediante la creación de una suscripción de Bus de servicio. Esto le da derecho a la back-end móvil para recibir una notificación del sistema back-end correspondiente. Móvil back-end continúa escuchar los mensajes en sus suscripciones y tan pronto como llega un mensaje, vuelve y envía como notificación para su concentrador de notificación. A continuación, hubs notificación entrega finalmente el mensaje a la aplicación móvil. Resumen de los componentes clave, tenemos:

1. Back-end (sistemas LoB/heredado)
    - Crea el tema de Bus de servicio
    - Envía el mensaje
2. Móvil back-end
    - Crea la suscripción de servicio
    - Recibe el mensaje (desde el sistema de back-end)
    - Envía una notificación a clientes (a través de concentrador de notificación de Azure)
3. Aplicación móvil
    - Recibe y Mostrar notificación

###<a name="benefits"></a>Ventajas:

1. La separación entre el receptor (aplicación o servicio móvil a través de concentrador de notificación) y el remitente (sistemas de back-end) permite a los sistemas adicionales de back-end integrados con un cambio mínimo.
2. Esto hará que el escenario de varias aplicaciones móviles para poder recibir los eventos de uno o varios de los sistemas de back-end.  

## <a name="sample"></a>Ejemplo:

###<a name="prerequisites"></a>Requisitos previos
Debe realizar los siguientes tutoriales para familiarizarse con los conceptos así como creación comunes y los pasos de configuración:

1. [Programación de Bus de servicio Pub/Sub] - esto explica los detalles del trabajo con temas de Bus de servicio o suscripciones, cómo crear un espacio de nombres que contengan temas/suscripciones, cómo enviar y recibir mensajes de ellos.
2. [Notificación Hubs - Windows Universal tutorial] - explica cómo configurar una aplicación de la tienda de Windows y usar Hubs de notificación para registrar y, a continuación, recibir notificaciones.

###<a name="sample-code"></a>Código de ejemplo

El código de ejemplo completo está disponible en los [Ejemplos de concentrador de notificación]. Se divide en tres componentes:

1. **EnterprisePushBackendSystem**

    una. Este proyecto utiliza el paquete de Nuget *WindowsAzure.ServiceBus* y se basa en la [programación de Bus de servicio Pub/Sub].

    b. Se trata de una aplicación de consola C# simple para simular un sistema de LoB que inicia el mensaje se ha entregado en la aplicación móvil.

        static void Main(string[] args)
        {
            string connectionString =
                CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

            // Create the topic where we will send notifications
            CreateTopic(connectionString);

            // Send message
            SendMessage(connectionString);
        }

    c. `CreateTopic`se usa para crear el tema de Bus de servicio que se enviará mensajes.

        public static void CreateTopic(string connectionString)
        {
            // Create the topic if it does not exist already

            var namespaceManager =
                NamespaceManager.CreateFromConnectionString(connectionString);

            if (!namespaceManager.TopicExists(sampleTopic))
            {
                namespaceManager.CreateTopic(sampleTopic);
            }
        }

    d. `SendMessage`se usa para enviar los mensajes a este tema de Bus de servicio. Aquí le estamos enviando simplemente un conjunto de mensajes aleatorios al tema periódicamente con el fin de la muestra. Normalmente será un sistema de back-end que le enviará mensajes cuando se produce un evento.

        public static void SendMessage(string connectionString)
        {
            TopicClient client =
                TopicClient.CreateFromConnectionString(connectionString, sampleTopic);

            // Sends random messages every 10 seconds to the topic
            string[] messages =
            {
                "Employee Id '{0}' has joined.",
                "Employee Id '{0}' has left.",
                "Employee Id '{0}' has switched to a different team."
            };

            while (true)
            {
                Random rnd = new Random();
                string employeeId = rnd.Next(10000, 99999).ToString();
                string notification = String.Format(messages[rnd.Next(0,messages.Length)], employeeId);

                // Send Notification
                BrokeredMessage message = new BrokeredMessage(notification);
                client.Send(message);

                Console.WriteLine("{0} Message sent - '{1}'", DateTime.Now, notification);

                System.Threading.Thread.Sleep(new TimeSpan(0, 0, 10));
            }
        }

2. **ReceiveAndSendNotification**

    una. Este proyecto usa los paquetes *WindowsAzure.ServiceBus* y *Microsoft.Web.WebJobs.Publish* Nuget y se basa en la [programación de Bus de servicio Pub/Sub].

    b. Esta es otra C# consola aplicación que se ejecutará como un [WebJob de Azure] puesto que debe ejecutarse de forma continua para escuchar los mensajes de los sistemas de LoB/back-end. Se trata de parte de su back-end móvil.

        static void Main(string[] args)
        {
            string connectionString =
                     CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

            // Create the subscription which will receive messages
            CreateSubscription(connectionString);

            // Receive message
            ReceiveMessageAndSendNotification(connectionString);
        }

    c. `CreateSubscription`se usa para crear una suscripción de Bus de servicio para el tema que el sistema de back-end enviará mensajes. Dependiendo del escenario de empresa, este componente creará una o más suscripciones a temas correspondientes (por ejemplo, algunos pueden recibir mensajes del sistema de recursos humanos, algunos de sistema de finanzas etc.)

        static void CreateSubscription(string connectionString)
        {
            // Create the subscription if it does not exist already
            var namespaceManager =
                NamespaceManager.CreateFromConnectionString(connectionString);

            if (!namespaceManager.SubscriptionExists(sampleTopic, sampleSubscription))
            {
                namespaceManager.CreateSubscription(sampleTopic, sampleSubscription);
            }
        }

    d. ReceiveMessageAndSendNotification se utiliza para leer el mensaje desde el tema con su suscripción y si es correcta la lectura elabore una notificación (en el escenario de ejemplo de notificación nativo de Windows) para que se envíen a la aplicación móvil usando Hubs de notificación de Azure.

        static void ReceiveMessageAndSendNotification(string connectionString)
        {
            // Initialize the Notification Hub
            string hubConnectionString = CloudConfigurationManager.GetSetting
                    ("Microsoft.NotificationHub.ConnectionString");
            hub = NotificationHubClient.CreateClientFromConnectionString
                    (hubConnectionString, "enterprisepushservicehub");

            SubscriptionClient Client =
                SubscriptionClient.CreateFromConnectionString
                        (connectionString, sampleTopic, sampleSubscription);

            Client.Receive();

            // Continuously process messages received from the subscription
            while (true)
            {
                BrokeredMessage message = Client.Receive();
                var toastMessage = @"<toast><visual><binding template=""ToastText01""><text id=""1"">{messagepayload}</text></binding></visual></toast>";

                if (message != null)
                {
                    try
                    {
                        Console.WriteLine(message.MessageId);
                        Console.WriteLine(message.SequenceNumber);
                        string messageBody = message.GetBody<string>();
                        Console.WriteLine("Body: " + messageBody + "\n");

                        toastMessage = toastMessage.Replace("{messagepayload}", messageBody);
                        SendNotificationAsync(toastMessage);

                        // Remove message from subscription
                        message.Complete();
                    }
                    catch (Exception)
                    {
                        // Indicate a problem, unlock message in subscription
                        message.Abandon();
                    }
                }
            }
        }
        static async void SendNotificationAsync(string message)
        {
            await hub.SendWindowsNativeNotificationAsync(message);
        }

    e. Para publicar esto como una **WebJob**, haga clic con el botón secundario en la solución en Visual Studio y seleccione **Publicar como WebJob**

    ![][2]

    f. Seleccione el perfil de publicación y cree un nuevo sitio Web de Azure si no existe ya que va a hospedar este WebJob y una vez que tenga el sitio Web después de la **publicación**.

    ![][3]

    g. Configurar el trabajo para que sea "Ejecutar continuamente" para que al iniciar sesión el [Portal de Azure clásica] verá algo parecido a lo siguiente:

    ![][4]


3. **EnterprisePushMobileApp**

    una. Se trata de una aplicación de almacenamiento de Windows que recibirá notificaciones de brindar desde el WebJob que se ejecuta como parte de su back-end móvil y mostrarla. Esto se basa en [Notificación Hubs - tutorial Universal de Windows].  

    b. Asegúrese de que la aplicación está habilitada para recibir notificaciones de aviso.

    c. Asegúrese de que se inician los siguientes centros de notificación se llama código de registro en la aplicación (después de reemplazar las *HubName* y *DefaultListenSharedAccessSignature*:

        private async void InitNotificationsAsync()
        {
            var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();

            var hub = new NotificationHub("[HubName]", "[DefaultListenSharedAccessSignature]");
            var result = await hub.RegisterNativeAsync(channel.Uri);

            // Displays the registration ID so you know it was successful
            if (result.RegistrationId != null)
            {
                var dialog = new MessageDialog("Registration successful: " + result.RegistrationId);
                dialog.Commands.Add(new UICommand("OK"));
                await dialog.ShowAsync();
            }
        }

### <a name="running-sample"></a>Ejemplo de ejecución:

1. Asegúrese de que su WebJob se está ejecutando correctamente y programada para "Ejecutar continuamente".
2. Ejecutar **EnterprisePushMobileApp** que se inicie la aplicación de la tienda de Windows.
3. Ejecute la aplicación de consola de **EnterprisePushBackendSystem** que el back-end de LoB para simular y empezará a enviar mensajes y verá las notificaciones de aviso que aparecen como la siguiente:

    ![][5]

4. Los mensajes enviados originalmente a temas de Bus de servicio que fue que supervisa suscripciones de Bus de servicio en el trabajo de Web. Una vez que se recibió un mensaje, se ha creado una notificación y se envían a la aplicación móvil. Puede consultar los registros de WebJob confirme el procesamiento cuando vaya al vínculo de registros en [Azure clásica Portal] para su trabajo Web:

    ![][6]

<!-- Images -->
[1]: ./media/notification-hubs-enterprise-push-architecture/architecture.png
[2]: ./media/notification-hubs-enterprise-push-architecture/WebJobsContextMenu.png
[3]: ./media/notification-hubs-enterprise-push-architecture/PublishAsWebJob.png
[4]: ./media/notification-hubs-enterprise-push-architecture/WebJob.png
[5]: ./media/notification-hubs-enterprise-push-architecture/Notifications.png
[6]: ./media/notification-hubs-enterprise-push-architecture/WebJobsLog.png

<!-- Links -->
[Ejemplos de concentrador de notificación]: https://github.com/Azure/azure-notificationhubs-samples
[Servicio móvil de Azure]: http://azure.microsoft.com/documentation/services/mobile-services/
[Bus de servicio de Azure]: http://azure.microsoft.com/documentation/articles/fundamentals-service-bus-hybrid-solutions/
[Programación de Bus de servicio Pub/Sub]: http://azure.microsoft.com/documentation/articles/service-bus-dotnet-how-to-use-topics-subscriptions/
[WebJob de Azure]: http://azure.microsoft.com/documentation/articles/web-sites-create-web-jobs/
[Notificación Hubs - tutorial Universal de Windows]: http://azure.microsoft.com/documentation/articles/notification-hubs-windows-store-dotnet-get-started/
[Portal de clásico de Azure]: https://manage.windowsazure.com/
