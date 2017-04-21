<properties
    pageTitle="Aplicación de varios niveles de .NET | Microsoft Azure"
    description="Un tutorial de .NET que le ayuda a desarrollar una aplicación de varios niveles en Azure que utiliza colas de Bus de servicio para comunicarse entre niveles."
    services="service-bus"
    documentationCenter=".net"
    authors="sethmanheim"
    manager="timlt"
    editor=""/>

<tags
    ms.service="service-bus"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="get-started-article"
    ms.date="09/01/2016"
    ms.author="sethm"/>

# <a name="net-multi-tier-application-using-azure-service-bus-queues"></a>Aplicación de varios niveles de .NET mediante colas de Bus de servicio de Azure

## <a name="introduction"></a>Introducción

Desarrollo de Microsoft Azure es fácil mediante Visual Studio y el SDK de Azure gratuito para .NET. Este tutorial le guiará por los pasos para crear una aplicación que usa varios recursos Azure que se ejecutan en su entorno local. Los pasos se supone que no tiene experiencia previa con Azure.

Aprenderá lo siguiente:

-   Cómo habilitar el equipo de desarrollo de Azure con una sola descarga e instale.
-   Cómo usar Visual Studio para desarrollar para Azure.
-   Cómo crear una aplicación de varios niveles en Azure con los roles de web y trabajador.
-   Procedimientos para la comunicación entre niveles mediante colas de Bus de servicio.

[AZURE.INCLUDE [create-account-note](../../includes/create-account-note.md)]

En este tutorial deberá crear y ejecutar la aplicación de varios niveles en un servicio de nube de Azure. Un rol web de ASP.NET MVC se mostrará el front-end y back-end se un rol de trabajo que utiliza una cola de Bus de servicio. Puede crear la misma aplicación de varios niveles con el front-end como un proyecto web que se implementa en un sitio Web de Azure en lugar de un servicio de nube. Para obtener instrucciones sobre qué hacer diferente en un front-end de sitio Web de Azure, consulte la sección [pasos siguientes](#nextsteps) . También puede probar el tutorial de [aplicación de .NET híbrido en local y la nube](../service-bus-relay/service-bus-dotnet-hybrid-app-using-service-bus-relay.md) .

La siguiente captura de pantalla muestra la aplicación completada.

![][0]

## <a name="scenario-overview-inter-role-communication"></a>Información general del escenario: comunicación entre rol

Para enviar un pedido para el procesamiento, el componente de interfaz de usuario front-end, que se ejecuta en el rol web, debe interactuar con la lógica de nivel intermedio que se ejecuta en el rol de trabajo. Este ejemplo usa Bus de servicio con la mensajería para la comunicación entre los niveles.

Uso de la mensajería con entre los niveles de medio y web separa los dos componentes. A diferencia de directa de mensajería (es decir, TCP o HTTP), el nivel de web no se conecta al nivel intermedio directamente; en su lugar inserta unidades de trabajo, como mensajes, en Bus de servicio, que conserva confiable hasta el nivel intermedio está preparado para consumir y procesar.

Bus de servicio proporciona dos entidades para admitir mensajería con: colas y temas. Con colas, cada mensaje enviado a la cola consume un receptor único. Temas de soporte técnico el patrón de publicación o suscripción en el que estará disponible para una suscripción registrada con el tema de cada mensaje publicado. Cada suscripción lógicamente mantiene su propia cola de mensajes. Suscripciones también pueden configurarse con reglas de filtro que restringen el conjunto de mensajes que se pasan a la cola de suscripción a aquellos que coincidan con el filtro. En el ejemplo siguiente se utiliza colas de Bus de servicio.

![][1]

Este mecanismo de comunicación tiene varias ventajas sobre mensajería directa:

-   **Desacoplar temporal.** Con el modelo de mensajería asincrónico, productor y los consumidores no necesitan ser en línea al mismo tiempo. Bus de servicio confiable almacena los mensajes hasta que la parte que está preparada para recibirlos. Esto permite que los componentes de la aplicación distribuida desconectarán, ya sea voluntario, por ejemplo, para realizar el mantenimiento o debido a un bloqueo de componente sin afectar el sistema como un todo. Además, la aplicación que solo necesite conectarse durante determinadas horas del día.

-   **Cargar la redistribución.** En muchas aplicaciones, carga del sistema varía con el tiempo, mientras que el tiempo de procesamiento necesario para cada unidad de trabajo es normalmente constante. Intermediating productor de mensaje y los consumidores de una cola significa que la aplicación consume (trabajador) solo necesita proporcionar para acomodar el promedio de carga en lugar de carga de recursos asignadas. La profundidad de la cola crece y contratos como varía la carga entrante. Esto directamente ahorra dinero en términos de la cantidad de infraestructura necesaria para la carga de la aplicación de servicio.

-   **Equilibrio de carga.** Como aumenta la carga, más procesos de trabajo pueden agregarse a leer en la cola. Cada mensaje se procesa solamente mediante uno de los procesos de trabajo. Además, este equilibrio de carga en función de extracción permite un uso óptimo de los equipos de trabajo incluso si los equipos de trabajo difieren en cuanto a la potencia de procesamiento, como extraerá los mensajes a su propia velocidad máxima. Este patrón a menudo se denomina el patrón de *una competición de consumidores* .

    ![][2]

Las secciones siguientes describen el código que implementa esta arquitectura.

## <a name="set-up-the-development-environment"></a>Configurar el entorno de desarrollo

Antes de empezar a desarrollar aplicaciones de Azure, obtenga las herramientas y configurar el entorno de desarrollo.

1.  Instale el SDK de Azure para .NET en [obtener herramientas y SDK][].

2.  Haga clic en **instalar el SDK** de la versión de Visual Studio que está utilizando. Los pasos de este tutorial usan Visual Studio de 2015.

4.  Cuando se le solicite que ejecute o guarde el programa de instalación, haga clic en **Ejecutar**.

5.  En el **Instalador de plataforma Web**, haga clic en **instalar** y continuar con la instalación.

6.  Una vez completada la instalación, tendrá todo lo necesario para comenzar a desarrollar la aplicación. El SDK incluye herramientas que le permiten desarrollar fácilmente aplicaciones de Azure en Visual Studio. Si no tiene instalado Visual Studio, el SDK también instala la versión Visual Studio Express gratuita.

## <a name="create-a-namespace"></a>Crear un espacio de nombres

El siguiente paso es crear un espacio de nombres de servicio y obtener una clave de firma de acceso compartido (SA). Un espacio de nombres proporciona un límite de aplicación para cada aplicación que se expone a través de Bus de servicio. Cuando se crea un espacio de nombres, se genera una clave de SA por el sistema. La combinación de espacio de nombres y la clave de SA proporciona las credenciales de Bus de servicio autenticar el acceso a la aplicación.

[AZURE.INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

## <a name="create-a-web-role"></a>Crear una función de web

En esta sección, cree el front-end de la aplicación. Primero, cree las páginas que muestra la aplicación.
Después de eso, agregar código que envía los elementos de una cola de Bus de servicio y se muestra información de estado sobre la cola.

### <a name="create-the-project"></a>Crear el proyecto

1.  Con privilegios de administrador, inicie Microsoft Visual Studio. Para iniciar Visual Studio con privilegios de administrador, haga clic en el icono de programa de **Visual Studio** y, a continuación, haga clic en **Ejecutar como administrador**. El emulador cálculo Azure, descrito más adelante en este artículo, se requiere que se inicie Visual Studio con privilegios de administrador.

    En Visual Studio, en el menú **archivo** , haga clic en **nuevo**y, a continuación, haga clic en **proyecto**.

2.  Desde **Plantillas instaladas**, en **Visual C#**, haga clic en la **nube** y, a continuación, haga clic en **Servicio de nube de Azure**. Nombre del proyecto **MultiTierApp**. A continuación, haga clic en **Aceptar**.

    ![][9]

3.  Roles de **.NET Framework 4.5** , haga doble clic en el **Rol Web de ASP.NET**.

    ![][10]

4.  Desplace el puntero sobre **WebRole1** en **solución de servicio de nube de Azure**, haga clic en el icono de lápiz y cambie el nombre de la función web **FrontendWebRole**. A continuación, haga clic en **Aceptar**. (Asegúrese de que escribir "Cliente" con una letra minúscula 'e,' no "cliente").

    ![][11]

5.  En el cuadro de diálogo **Nuevo proyecto** , en la lista **Seleccionar una plantilla** , haga clic en **MVC**.

    ![][12]

6. Aún en el cuadro de diálogo **Nuevo proyecto** , haga clic en el botón de **Cambio de la autenticación** . En el cuadro de diálogo **Cambiar autenticación** , haga clic en **Sin autenticación**y, a continuación, haga clic en **Aceptar**. En este tutorial, vaya a implementar una aplicación que no es necesario un inicio de sesión de usuario.

    ![][16]

7. En el cuadro de diálogo **Nuevo proyecto** , haga clic en **Aceptar** para crear el proyecto.

6.  En el **Explorador de soluciones**, en el proyecto **FrontendWebRole** , haga clic en **referencias**, haga clic en **Administrar paquetes de NuGet**.

7.  Haga clic en la ficha **Examinar** , busque `Microsoft Azure Service Bus`. Haga clic en **instalar**y acepte los términos de uso.

    ![][13]

    Observe que los ensamblados necesarios cliente ahora se hace referencia y se han agregado algunos archivos nuevos de código.

9.  En el **Explorador de soluciones**, haga clic en **modelos** y haga clic en **Agregar**, haga clic en **clase**. En el cuadro **nombre** , escriba el nombre **OnlineOrder.cs**. A continuación, haga clic en **Agregar**.

### <a name="write-the-code-for-your-web-role"></a>Escribir el código para su rol de web

En esta sección, creará las distintas páginas que muestra la aplicación.

1.  En el archivo OnlineOrder.cs en Visual Studio, reemplace la definición de espacio de nombres existente con el siguiente código:

    ```
    namespace FrontendWebRole.Models
    {
        public class OnlineOrder
        {
            public string Customer { get; set; }
            public string Product { get; set; }
        }
    }
    ```

2.  En el **Explorador de soluciones**, haga doble clic en **Controllers\HomeController.cs**. Agregue las siguientes instrucciones **using** en la parte superior del archivo para incluir los espacios de nombres para el modelo que acaba de crear, así como de Bus de servicio.

    ```
    using FrontendWebRole.Models;
    using Microsoft.ServiceBus.Messaging;
    using Microsoft.ServiceBus;
    ```

3.  También en el archivo HomeController.cs en Visual Studio, sustituya la definición de espacio de nombres existente con el código siguiente. Este código contiene métodos para controlar el envío de elementos en la cola.

    ```
    namespace FrontendWebRole.Controllers
    {
        public class HomeController : Controller
        {
            public ActionResult Index()
            {
                // Simply redirect to Submit, since Submit will serve as the
                // front page of this application.
                return RedirectToAction("Submit");
            }
    
            public ActionResult About()
            {
                return View();
            }
    
            // GET: /Home/Submit.
            // Controller method for a view you will create for the submission
            // form.
            public ActionResult Submit()
            {
                // Will put code for displaying queue message count here.
    
                return View();
            }
    
            // POST: /Home/Submit.
            // Controller method for handling submissions from the submission
            // form.
            [HttpPost]
            // Attribute to help prevent cross-site scripting attacks and
            // cross-site request forgery.  
            [ValidateAntiForgeryToken]
            public ActionResult Submit(OnlineOrder order)
            {
                if (ModelState.IsValid)
                {
                    // Will put code for submitting to queue here.
    
                    return RedirectToAction("Submit");
                }
                else
                {
                    return View(order);
                }
            }
        }
    }
    ```

4.  En el menú **Generar** , haga clic en **Generar solución** para comprobar la exactitud de su trabajo hasta ese momento.

5.  Ahora, cree la vista de la `Submit()` método que creó anteriormente. Secundario dentro de la `Submit()` método (la sobrecarga de `Submit()` que no requiera parámetros) y, a continuación, elija **Agregar vista**.

    ![][14]

6.  Aparece un cuadro de diálogo para crear la vista. En la lista de **plantilla** , elija **crear**. En la lista de **clase de modelo** , haga clic en la clase **OnlineOrder** .

    ![][15]

7.  Haga clic en **Agregar**.

8.  Ahora, cambie el nombre de la aplicación que se muestra. En el **Explorador de soluciones**, haga doble clic en el **Views\Shared\\_Layout.cshtml** archivo para abrirlo en el editor de Visual Studio.

9.  Reemplazar todas las apariciones de **Mi aplicación ASP.NET** con **productos de LITWARE**.

10. Quitar los vínculos de ** **Inicio**y **contacto** **. Eliminar el código resaltado:

    ![][28]

11. Por último, modifique la página de envío para incluir información sobre la cola. En el **Explorador de soluciones**, haga doble clic en el archivo **Views\Home\Submit.cshtml** para abrirlo en el editor de Visual Studio. Agregue la línea siguiente después de `<h2>Submit</h2>`. Por ahora, la `ViewBag.MessageCount` está vacío. Se llenará más adelante.

    ```
    <p>Current number of orders in queue waiting to be processed: @ViewBag.MessageCount</p>
    ```

12. Se ha implementado la interfaz de usuario. Presione **F5** para ejecutar la aplicación y confirme que se muestra como se esperaba.

    ![][17]

### <a name="write-the-code-for-submitting-items-to-a-service-bus-queue"></a>Escribir el código para enviar elementos a una cola de Bus de servicio

Ahora, agregue el código para enviar elementos de una cola. Primero, cree una clase que contiene la información de conexión de cola de Bus de servicio. A continuación, inicializar su conexión de Global.aspx.cs. Por último, actualice el código de presentación que creó anteriormente en HomeController.cs envíe elementos de una cola de Bus de servicio.

1.  En el **Explorador de soluciones**, haga clic con el botón **FrontendWebRole** (ratón del proyecto, no el rol). Haga clic en **Agregar**y, a continuación, haga clic en **clase**.

2.  Nombre de la clase **QueueConnector.cs**. Haga clic en **Agregar** para crear la clase.

3.  Ahora, agregue el código que encapsula la información de conexión e inicializa la conexión a una cola de Bus de servicio. Reemplazar todo el contenido de QueueConnector.cs con el siguiente código y escriba los valores de `your Service Bus namespace` (su nombre de espacio de nombres) y `yourKey`, que es la **clave principal** obtenido previamente desde el portal de Azure.

    ```
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Web;
    using Microsoft.ServiceBus.Messaging;
    using Microsoft.ServiceBus;
    
    namespace FrontendWebRole
    {
        public static class QueueConnector
        {
            // Thread-safe. Recommended that you cache rather than recreating it
            // on every request.
            public static QueueClient OrdersQueueClient;
    
            // Obtain these values from the portal.
            public const string Namespace = "your Service Bus namespace";
    
            // The name of your queue.
            public const string QueueName = "OrdersQueue";
    
            public static NamespaceManager CreateNamespaceManager()
            {
                // Create the namespace manager which gives you access to
                // management operations.
                var uri = ServiceBusEnvironment.CreateServiceUri(
                    "sb", Namespace, String.Empty);
                var tP = TokenProvider.CreateSharedAccessSignatureTokenProvider(
                    "RootManageSharedAccessKey", "yourKey");
                return new NamespaceManager(uri, tP);
            }
    
            public static void Initialize()
            {
                // Using Http to be friendly with outbound firewalls.
                ServiceBusEnvironment.SystemConnectivity.Mode =
                    ConnectivityMode.Http;
    
                // Create the namespace manager which gives you access to
                // management operations.
                var namespaceManager = CreateNamespaceManager();
    
                // Create the queue if it does not exist already.
                if (!namespaceManager.QueueExists(QueueName))
                {
                    namespaceManager.CreateQueue(QueueName);
                }
    
                // Get a client to the queue.
                var messagingFactory = MessagingFactory.Create(
                    namespaceManager.Address,
                    namespaceManager.Settings.TokenProvider);
                OrdersQueueClient = messagingFactory.CreateQueueClient(
                    "OrdersQueue");
            }
        }
    }
    ```

4.  Ahora, asegúrese de que se llama a su método **Initialize** . En el **Explorador de soluciones**, haga doble clic en **Global.asax\Global.asax.cs**.

5.  Agregue la siguiente línea de código al final del método **Application_Start** .

    ```
    FrontendWebRole.QueueConnector.Initialize();
    ```

6.  Por último, actualice el código de web que creó anteriormente, para enviar elementos a la cola. En el **Explorador de soluciones**, haga doble clic en **Controllers\HomeController.cs**.

7.  Actualizar la `Submit()` método (la sobrecarga que no toma parámetros) como se indica a continuación para obtener el recuento de mensajes para la cola.

    ```
    public ActionResult Submit()
    {
        // Get a NamespaceManager which allows you to perform management and
        // diagnostic operations on your Service Bus queues.
        var namespaceManager = QueueConnector.CreateNamespaceManager();
    
        // Get the queue, and obtain the message count.
        var queue = namespaceManager.GetQueue(QueueConnector.QueueName);
        ViewBag.MessageCount = queue.MessageCount;
    
        return View();
    }
    ```

8.  Actualizar la `Submit(OnlineOrder order)` método (la sobrecarga que toma un parámetro) como sigue para enviar información de orden a la cola.

    ```
    public ActionResult Submit(OnlineOrder order)
    {
        if (ModelState.IsValid)
        {
            // Create a message from the order.
            var message = new BrokeredMessage(order);
    
            // Submit the order.
            QueueConnector.OrdersQueueClient.Send(message);
            return RedirectToAction("Submit");
        }
        else
        {
            return View(order);
        }
    }
    ```

9.  Ahora puede ejecutar la aplicación de nuevo. Cada vez que envíe un pedido, que aumenta el número de mensajes.

    ![][18]

## <a name="create-the-worker-role"></a>Crear la función de trabajo

Va a crear la función de trabajo que procesa los envíos de orden. Este ejemplo usa la plantilla de proyecto de Visual Studio de **Rol de trabajo con cola de Bus de servicio** . Ya que se obtienen las credenciales necesarias desde el portal.

1. Asegúrese de que Visual Studio se ha conectado a su cuenta de Azure.

2.  En Visual Studio, en el **Explorador de soluciones** secundario en la carpeta de **Roles** en el proyecto **MultiTierApp** .

3.  Haga clic en **Agregar**y, a continuación, haga clic en **Nuevo proyecto de la función de trabajo**. Aparece el cuadro de diálogo **Agregar nuevo proyecto de rol** .

    ![][26]

4.  En el cuadro de diálogo **Agregar nuevo proyecto de rol** , haga clic en **Función del trabajo con cola de Bus de servicio**.

    ![][23]

5.  En el cuadro **nombre** , asigne un nombre al proyecto **OrderProcessingRole**. A continuación, haga clic en **Agregar**.

6.  Copie la cadena de conexión que obtuvo en el paso 9 de la sección "Crear un espacio de nombres de Bus de servicio" en el Portapapeles.

7.  En el **Explorador de soluciones**, haga clic en el **OrderProcessingRole** que creó en el paso 5 (asegúrese de que haga **OrderProcessingRole** en **Roles**y no la clase). A continuación, haga clic en **Propiedades**.

8.  En la pestaña **configuración** del cuadro de diálogo **Propiedades** , haga clic en el cuadro **valor** para **Microsoft.ServiceBus.ConnectionString**y, a continuación, pegue el valor de extremo que copió en el paso 6.

    ![][25]

9.  Crear una clase **OnlineOrder** para representar los pedidos mientras se procesa de la cola. Puede reutilizar una clase que ya ha creado. En el **Explorador de soluciones**, haga clic en la clase **OrderProcessingRole** (el icono de clase, no el rol de ratón). Haga clic en **Agregar**y luego haga clic en **Elemento existente**.

10. Busque la subcarpeta **FrontendWebRole\Models**y, a continuación, haga doble clic en **OnlineOrder.cs** para agregar a este proyecto.

11. En **WorkerRole.cs**, cambie el valor de la variable de **nombre** de `"ProcessingQueue"` a `"OrdersQueue"` tal como se muestra en el código siguiente.

    ```
    // The name of your queue.
    const string QueueName = "OrdersQueue";
    ```

12. Agregue la siguiente instrucción using en la parte superior del archivo WorkerRole.cs.

    ```
    using FrontendWebRole.Models;
    ```

13. En la `Run()` funcione, dentro del `OnMessage()` llamar, reemplace el contenido de la `try` cláusula con el siguiente código.

    ```
    Trace.WriteLine("Processing", receivedMessage.SequenceNumber.ToString());
    // View the message as an OnlineOrder.
    OnlineOrder order = receivedMessage.GetBody<OnlineOrder>();
    Trace.WriteLine(order.Customer + ": " + order.Product, "ProcessingMessage");
    receivedMessage.Complete();
    ```

14. Ha finalizado la aplicación. Puede probar la aplicación completa haciendo clic en el proyecto MultiTierApp en el Explorador de soluciones, seleccione **establecer como proyecto de inicio**y, a continuación, presione F5. Tenga en cuenta que no aumenta el número de mensajes, porque la función de trabajo procesa elementos de la cola y los marca como completado. Puede ver los resultados del seguimiento de su rol de trabajo consultando la IU de emulador calcular de Azure. Puede hacerlo haciendo clic en el icono de emulador en el área de notificación de la barra de tareas y seleccionando **Mostrar IU de emulador calcular**.

    ![][19]

    ![][20]

## <a name="next-steps"></a>Pasos siguientes  

Para obtener más información acerca de Bus de servicio, consulte los siguientes recursos:  

* [Bus de servicio de Azure][sbmsdn]  
* [Página de servicio de Bus de servicio][sbwacom]  
* [Cómo usar colas de Bus de servicio][sbwacomqhowto]  

Para obtener más información acerca de los escenarios de varios niveles, consulte:  

* [Aplicación de varios niveles de .NET con BLOB, colas y tablas de almacenamiento][mutitierstorage]  

  [0]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-01.png
  [1]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-100.png
  [2]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-101.png
  [Obtenga herramientas y SDK]: http://go.microsoft.com/fwlink/?LinkId=271920


  [GetSetting]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.cloudconfigurationmanager.getsetting.aspx
  [Microsoft.WindowsAzure.Configuration.CloudConfigurationManager]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.cloudconfigurationmanager.aspx
  [NamespaceMananger]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx

  [QueueClient]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.aspx

  [TopicClient]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.topicclient.aspx

  [EventHubClient]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventhubclient.aspx

  [9]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-10.png
  [10]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-11.png
  [11]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-02.png
  [12]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-12.png
  [13]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-13.png
  [14]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-33.png
  [15]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-34.png
  [16]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-14.png
  [17]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-36.png
  [18]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-37.png

  [19]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-38.png
  [20]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-39.png
  [23]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/SBWorkerRole1.png
  [25]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/SBWorkerRoleProperties.png
  [26]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/SBNewWorkerRole.png
  [28]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-40.png

  [sbmsdn]: http://msdn.microsoft.com/library/azure/ee732537.aspx  
  [sbwacom]: /documentation/services/service-bus/  
  [sbwacomqhowto]: service-bus-dotnet-get-started-with-queues.md  
  [mutitierstorage]: https://code.msdn.microsoft.com/Windows-Azure-Multi-Tier-eadceb36
  