<properties 
    pageTitle="Bus de servicio negociada mensajería tutorial .NET | Microsoft Azure"
    description="Tutorial de .NET mensajería con."
    services="service-bus"
    documentationCenter="na"
    authors="sethmanheim"
    manager="timlt"
    editor="" />
<tags 
    ms.service="service-bus"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="na"
    ms.workload="na"
    ms.date="09/27/2016"
    ms.author="sethm" />

# <a name="service-bus-brokered-messaging-net-tutorial"></a>Tutorial de Bus de servicio con mensajería .NET

Bus de servicios de Azure proporciona dos soluciones de mensajería completas: uno a través de un servicio de retransmisión centralizada"" en la nube que admite una gran variedad de diferentes protocolos de transporte y Web services estándares, incluidos SOAP, WS-* y el resto. El cliente no tenga una conexión directa al servicio local ni necesita saber donde reside el servicio y el servicio local no necesitan puertos entrantes abierto en el firewall.

La segunda solución mensajería permite capacidades de mensajería "con". Estos pueden considerarse como asincrónica o desacoplado características de mensajería soporte publicación suscripción, disociación temporal y escenarios de uso de la infraestructura de mensajería de Bus de servicios de equilibrio de carga. Comunicación desacoplada tiene muchas ventajas; Por ejemplo, clientes y servidores pueden conectar según sea necesario y realizar sus operaciones de forma asincrónica.

Este tutorial está pensado para proporcionar una descripción general y experimentar con colas, uno de los componentes principales de Bus de servicio negociada mensajería. Después de trabajar a través de la secuencia de temas en este tutorial, tendrá una aplicación que rellena una lista de mensajes, crea una cola y envía mensajes a esa cola. Por último, la aplicación recibe muestra los mensajes de la cola y limpia sus recursos y salidas. Para obtener un tutorial correspondiente que describe cómo crear una aplicación que utiliza la retransmisión de Bus de servicio, consulte el [tutorial mensajería de retransmisión de Bus de servicio](../service-bus-relay/service-bus-relay-tutorial.md).

## <a name="introduction-and-prerequisites"></a>Introducción y los requisitos previos

Colas ofrecen primera en, entrega de mensajes de la primera salida (FIFO) a uno o varios de los consumidores competencia. FIFO significa que los mensajes se espera normalmente se recibió y procesados por los receptores en el orden temporal en el que estaban en cola y cada mensaje se ha recibido y procesado por los consumidores de un solo mensaje. Una ventaja clave de uso de colas es lograr la *disociación temporal* de componentes de la aplicación: en otras palabras, el productor y los consumidores no es necesario enviar y recibir mensajes al mismo tiempo, ya que los mensajes se almacenan duradera en la cola. Una ventaja relacionada es *nivelación de carga*, lo que permite productor y los consumidores para enviar y recibir mensajes a tasas diferentes.

Los siguientes son algunos pasos administrativos y los requisitos previos que debe seguir antes de comenzar el tutorial. El primero es crear un espacio de nombres de servicio y obtener una clave de firma (SA) de acceso compartido. Un espacio de nombres proporciona un límite de aplicación para cada aplicación que se expone a través de Bus de servicio. El sistema genera automáticamente una clave de SA cuando se crea un espacio de nombres de servicio. La combinación de espacio de nombres de servicio y clave SA proporciona una credencial que Bus de servicio autentica el acceso a la aplicación.

### <a name="create-a-service-namespace-and-obtain-a-sas-key"></a>Crear un espacio de nombres de servicio y obtener una clave de SA

El primer paso es crear un espacio de nombres de servicio y obtener una clave de [Firma de acceso compartido](service-bus-sas-overview.md) (SA). Un espacio de nombres proporciona un límite de aplicación para cada aplicación que se expone a través de Bus de servicio. El sistema genera automáticamente una clave de SA cuando se crea un espacio de nombres de servicio. La combinación de espacio de nombres de servicio y clave SA proporciona una credencial de Bus de servicio autenticar el acceso a la aplicación.

[AZURE.INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

El siguiente paso es crear un proyecto de Visual Studio y escribir dos funciones auxiliares que cargar una lista delimitada por comas de mensajes en un objeto de .NET [lista](https://msdn.microsoft.com/library/6sh2ey19.aspx) [BrokeredMessage](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.aspx) inflexible.

### <a name="create-a-visual-studio-project"></a>Crear un proyecto de Visual Studio

1. Abra Visual Studio como administrador haciendo clic en el programa en el menú Inicio y haga clic en **Ejecutar como administrador**.

1. Crear un nuevo proyecto de aplicación de consola. Haga clic en el menú **archivo** , seleccione **nuevo**y haga clic en **proyecto**. En el cuadro de diálogo **Nuevo proyecto** , haga clic en **Visual C#** (si **Visual C#** no aparece, busque en **Otros idiomas**), haga clic en la plantilla de **Aplicación de consola** y asígnele el nombre **QueueSample**. Utilice la **ubicación**predeterminada. Haga clic en **Aceptar** para crear el proyecto.

1. Usar el Administrador de paquetes de NuGet para agregar las bibliotecas de Bus de servicio a su proyecto:
    1. En el Explorador de soluciones, haga clic en el proyecto **QueueSample** y, a continuación, haga clic en **Administrar paquetes de NuGet**.
    2. En el cuadro de diálogo **Administrar paquetes de Nuget** , haga clic en la ficha **Examinar** , busque **Bus de servicio de Azure**y haga clic en **instalar**.
<br />
1. En el Explorador de soluciones, haga doble clic en el archivo Program.cs para abrirlo en el editor de Visual Studio. Cambiar el espacio de nombres de su nombre predeterminado de `QueueSample` a `Microsoft.ServiceBus.Samples`.

    ```
    Microsoft.ServiceBus.Samples
    {
        ...
    ```

1. Modificar el `using` instrucciones tal como se muestra en el código siguiente.

    ```
    using System;
    using System.Collections.Generic;
    using System.Data;
    using System.IO;
    using System.Threading;
    using System.Threading.Tasks;
    using Microsoft.ServiceBus.Messaging;
    ```

1. Crear un archivo de texto denominado Data.csv y copie el siguiente texto delimitado por comas.

    ```
    IssueID,IssueTitle,CustomerID,CategoryID,SupportPackage,Priority,Severity,Resolved
    1,Package lost,1,1,Basic,5,1,FALSE
    2,Package damaged,1,1,Basic,5,1,FALSE
    3,Product defective,1,2,Premium,5,2,FALSE
    4,Product damaged,2,2,Premium,5,2,FALSE
    5,Package lost,2,2,Basic,5,2,TRUE
    6,Package lost,3,2,Basic,5,2,FALSE
    7,Package damaged,3,7,Premium,5,3,FALSE
    8,Product defective,3,2,Premium,5,3,FALSE
    9,Product damaged,4,6,Premium,5,3,TRUE
    10,Package lost,4,8,Basic,5,3,FALSE
    11,Package damaged,5,4,Basic,5,4,FALSE
    12,Product defective,5,4,Basic,5,4,FALSE
    13,Package lost,6,8,Basic,5,4,FALSE
    14,Package damaged,6,7,Premium,5,5,FALSE
    15,Product defective,6,2,Premium,5,5,FALSE
    ```

    Guarde y cierre el archivo Data.csv y recordar la ubicación en la que ha guardado.

1. En el Explorador de soluciones, haga clic en el nombre del proyecto (en este ejemplo, **QueueSample**), haga clic en **Agregar**y luego haga clic en **Elemento existente**.

1. Busque el archivo Data.csv que creó en el paso 6. Haga clic en el archivo y luego haga clic en **Agregar**. Asegúrese de que * *todos los archivos (*.*) ** está seleccionada en la lista de tipos de archivo.

### <a name="create-a-method-that-parses-a-list-of-messages"></a>Crear un método que analiza una lista de mensajes

1. En la `Program` de clase antes de la `Main()` método, declarar dos variables: uno de tipo de **tabla de datos**que contendrá la lista de mensajes en Data.csv. La otra debe ser del tipo de objeto de la lista, inflexible [BrokeredMessage](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.aspx). El último es la lista de los mensajes con los pasos siguientes en el tutorial usará.

    ```
    namespace Microsoft.ServiceBus.Samples
    {
        class Program
        {
    
            private static DataTable issues;
            private static List<BrokeredMessage> MessageList;
    ```

1. Externas `Main()`, defina un `ParseCSV()` método que analiza la lista de mensajes en Data.csv y carga los mensajes en una tabla de la [tabla de datos](https://msdn.microsoft.com/library/azure/system.data.datatable.aspx) , como se muestra aquí. El método devuelve un objeto de la **tabla de datos** .

    ```
    static DataTable ParseCSVFile()
    {
        DataTable tableIssues = new DataTable("Issues");
        string path = @"..\..\data.csv";
        try
        {
            using (StreamReader readFile = new StreamReader(path))
            {
                string line;
                string[] row;
    
                // create the columns
                line = readFile.ReadLine();
                foreach (string columnTitle in line.Split(','))
                {
                    tableIssues.Columns.Add(columnTitle);
                }
    
                while ((line = readFile.ReadLine()) != null)
                {
                    row = line.Split(',');
                    tableIssues.Rows.Add(row);
                }
            }
        }
        catch (Exception e)
        {
            Console.WriteLine("Error:" + e.ToString());
        }
    
        return tableIssues;
    }
    ```

1. En la `Main()` método, agregue una instrucción que llama el `ParseCSVFile()` método:

    ```
    public static void Main(string[] args)
    {
    
        // Populate test data
        issues = ParseCSVFile();
    
    }
    ```

### <a name="create-a-method-that-loads-the-list-of-messages"></a>Crear un método que carga la lista de mensajes

1. Externas `Main()`, defina un `GenerateMessages()` método que tome el objeto de la **tabla de datos** devuelto por `ParseCSVFile()` y carga de la tabla en una lista inflexible de mensajes con. El método devuelve el objeto de la **lista** , como en el ejemplo siguiente. 

    ```
    static List<BrokeredMessage> GenerateMessages(DataTable issues)
    {
        // Instantiate the brokered list object
        List<BrokeredMessage> result = new List<BrokeredMessage>();
    
        // Iterate through the table and create a brokered message for each row
        foreach (DataRow item in issues.Rows)
        {
            BrokeredMessage message = new BrokeredMessage();
            foreach (DataColumn property in issues.Columns)
            {
                message.Properties.Add(property.ColumnName, item[property]);
            }
            result.Add(message);
        }
        return result;
    }
    ```

1. En `Main()`, directamente después de la llamada a `ParseCSVFile()`, agregue una instrucción que llama el `GenerateMessages()` método con el valor devuelto por `ParseCSVFile()` como argumento:

    ```
    public static void Main(string[] args)
    {
    
        // Populate test data
        issues = ParseCSVFile();
        MessageList = GenerateMessages(issues);
    }
    ```

### <a name="obtain-user-credentials"></a>Obtener las credenciales de usuario

1. Primero, cree tres variables de cadena global para mantener estos valores. Declare estas variables directamente después de las declaraciones de variable anterior; Por ejemplo:

    ```
    namespace Microsoft.ServiceBus.Samples
    {
        public class Program
        {
    
            private static DataTable issues;
            private static List<BrokeredMessage> MessageList; 

            // Add these variables
            private static string ServiceNamespace;
            private static string sasKeyName = "RootManageSharedAccessKey";
            private static string sasKeyValue;
            …
    ```

1. A continuación, cree una función que acepta y almacena el espacio de nombres de servicio y la clave de SA. Agregar este método fuera de `Main()`. Por ejemplo: 

    ```
    static void CollectUserInput()
    {
        // User service namespace
        Console.Write("Please enter the namespace to use: ");
        ServiceNamespace = Console.ReadLine();
    
        // Issuer key
        Console.Write("Enter the SAS key to use: ");
        sasKeyValue = Console.ReadLine();
    }
    ```

1. En `Main()`, directamente después de la llamada a `GenerateMessages()`, agregue una instrucción que llama el `CollectUserInput()` método:

    ```
    public static void Main(string[] args)
    {
    
        // Populate test data
        issues = ParseCSVFile();
        MessageList = GenerateMessages(issues);
        
        // Collect user input
        CollectUserInput();
    }
    ```

### <a name="build-the-solution"></a>Generar la solución

En el menú **Generar** en Visual Studio, puede haga clic en **Generar solución** o presione **Ctrl + Mayús + B** para confirmar la exactitud de su trabajo hasta ese momento.

## <a name="create-management-credentials"></a>Crear credenciales de administración

En este paso, se definen las operaciones de administración que se usa para crear las credenciales de firma (SA) con la que se va a autorizar la aplicación de acceso compartido.

1. Por claridad, este tutorial coloca todas las operaciones de cola en un método independiente. Crear un asincrónico `Queue()` método en la `Program` de clase, cuando la `Main()` método. Por ejemplo:
 
    ```
    public static void Main(string[] args)
    {
    …
    }
    static async Task Queue()
    {
    }
    ```

1. El siguiente paso es crear una credencial de SA utilizando un objeto [TokenProvider](https://msdn.microsoft.com/library/azure/microsoft.servicebus.tokenprovider.aspx) . El método de creación toma el nombre de la clave de SA y obtener valor en el `CollectUserInput()` método. Agregue el código siguiente a la `Queue()` método:

    ```
    static async Task Queue()
    {
        // Create management credentials
        TokenProvider credentials = TokenProvider.CreateSharedAccessSignatureTokenProvider(sasKeyName,sasKeyValue);
    }
    ```

2. Crear un nuevo objeto de administración de espacio de nombres con un URI que contiene el espacio de nombres y las credenciales de administración obtenidas en el paso anterior, como argumentos. Agregue este código directamente después del código que agregó en el paso anterior. Asegúrese de reemplazar `<yourNamespace>` con el nombre de su espacio de nombres de servicio:
    
    ```
    NamespaceManager namespaceClient = new NamespaceManager(ServiceBusEnvironment.CreateServiceUri("sb", "<yourNamespace>", string.Empty), credentials);
    ```

### <a name="example"></a>Ejemplo

En este momento, el código debe ser similar al siguiente:

```
using System;
using System.Collections.Generic;
using System.Data;
using System.IO;
using System.Threading;
using System.Threading.Tasks;
using Microsoft.ServiceBus.Messaging;

namespace Microsoft.ServiceBus.Samples
{
  public class Program
  {
    private static DataTable issues;
    private static List<BrokeredMessage> MessageList;
    private static string ServiceNamespace;
    private static string sasKeyName = "RootManageSharedAccessKey";
    private static string sasKeyValue;

    static void Main(string[] args)
    {
      // Populate test data
      issues = ParseCSVFile();
      MessageList = GenerateMessages(issues);

      // Collect user input
      CollectUserInput();

      // Add this call
      Task.WaitAll(Queue());
    }

    static async Task Queue()
    {
      // Create management credentials
      TokenProvider credentials = TokenProvider.CreateSharedAccessSignatureTokenProvider(sasKeyName, sasKeyValue);
      NamespaceManager namespaceClient = new NamespaceManager(ServiceBusEnvironment.CreateServiceUri("sb", "<yourNamespace>", string.Empty), credentials);
    }

    static DataTable ParseCSVFile()
    {
      DataTable tableIssues = new DataTable("Issues");
      string path = @"..\..\data.csv";
      try
      {
        using (StreamReader readFile = new StreamReader(path))
        {
          string line;
          string[] row;

          // create the columns
          line = readFile.ReadLine();
          foreach (string columnTitle in line.Split(','))
          {
            tableIssues.Columns.Add(columnTitle);
          }

          while ((line = readFile.ReadLine()) != null)
          {
            row = line.Split(',');
            tableIssues.Rows.Add(row);
          }
        }
      }
      catch (Exception e)
      {
        Console.WriteLine("Error:" + e.ToString());
      }

      return tableIssues;
    }

    static List<BrokeredMessage> GenerateMessages(DataTable issues)
    {
      // Instantiate the brokered list object
      List<BrokeredMessage> result = new List<BrokeredMessage>();

      // Iterate through the table and create a brokered message for each row
      foreach (DataRow item in issues.Rows)
      {
        BrokeredMessage message = new BrokeredMessage();
        foreach (DataColumn property in issues.Columns)
        {
          message.Properties.Add(property.ColumnName, item[property]);
        }
        result.Add(message);
      }
      return result;
    }

    static void CollectUserInput()
    {
      // User service namespace
      Console.Write("Please enter the service namespace to use: ");
      ServiceNamespace = Console.ReadLine();

      // Issuer key
      Console.Write("Please enter the issuer key to use: ");
      sasKeyValue = Console.ReadLine();
    }
  }
}
```

## <a name="send-messages-to-the-queue"></a>Enviar mensajes a la cola

En este paso, cree una cola y luego enviar los mensajes contenidos en la lista de mensajes con a dicha cola.

### <a name="create-queue-and-send-messages-to-the-queue"></a>Crear la cola y enviar mensajes a la cola

1. Primero, cree la cola. Por ejemplo, la llamada `myQueue`y declare directamente después de las operaciones de administración que agregó en el `Queue()` método en el último paso:

    ```
    QueueDescription myQueue;

    if (namespaceClient.QueueExists("IssueTrackingQueue"))
    {
        namespaceClient.DeleteQueue("IssueTrackingQueue");
    }

    myQueue = namespaceClient.CreateQueue("IssueTrackingQueue");
    ```

1. En la `Queue()` método, cree un objeto de fábrica mensajería con un URI de Bus de servicio recién creado como un argumento. Agregue el código siguiente directamente después de las operaciones de administración que agregó en el último paso. Asegúrese de reemplazar `<yourNamespace>` con el nombre de su espacio de nombres de servicio:

    ```
    MessagingFactory factory = MessagingFactory.Create(ServiceBusEnvironment.CreateServiceUri("sb", "<yourNamespace>", string.Empty), credentials);
    ```

1. A continuación, cree el objeto de cola con la clase [QueueClient](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.aspx) . Agregue el código siguiente directamente después del código que agregó en el último paso:

    ```
    QueueClient myQueueClient = factory.CreateQueueClient("IssueTrackingQueue");
    ```

1. A continuación, agregue código que recorra la lista de mensajes con que creó anteriormente, cada uno a la cola de envío. Agregue el código siguiente directamente después de la `CreateQueueClient()` instrucción en el paso anterior:
    
    ```
    // Send messages
    Console.WriteLine("Now sending messages to the queue.");
    for (int count = 0; count < 6; count++)
    {
        var issue = MessageList[count];
        issue.Label = issue.Properties["IssueTitle"].ToString();
        await myQueueClient.SendAsync(issue);
        Console.WriteLine(string.Format("Message sent: {0}, {1}", issue.Label, issue.MessageId));
    }
    ```

## <a name="receive-messages-from-the-queue"></a>Recibir mensajes de la cola

En este paso, se obtiene la lista de mensajes de la cola que ha creado en el paso anterior.

### <a name="create-a-receiver-and-receive-messages-from-the-queue"></a>Crear un receptor y recibir mensajes de la cola

En la `Queue()` método, recorrer la cola y recibir los mensajes con el método [QueueClient.ReceiveAsync](https://msdn.microsoft.com/library/azure/dn130423.aspx) , cada mensaje en la consola de impresión. Agregue el código siguiente directamente después del código que agregó en el paso anterior:

```
Console.WriteLine("Now receiving messages from Queue.");
BrokeredMessage message;
while ((message = await myQueueClient.ReceiveAsync(new TimeSpan(hours: 0, minutes: 1, seconds: 5))) != null)
    {
        Console.WriteLine(string.Format("Message received: {0}, {1}, {2}", message.SequenceNumber, message.Label, message.MessageId));
        message.Complete();
    
        Console.WriteLine("Processing message (sleeping...)");
        Thread.Sleep(1000);
    }
```

Tenga en cuenta que el `Thread.Sleep` solo se usa para simular el mensaje de procesamiento y probablemente no se necesita en una aplicación de mensajería real.

### <a name="end-the-queue-method-and-clean-up-resources"></a>Finalizar el método de cola y limpiar los recursos

Directamente después del código anterior, agregue el código siguiente para limpiar los recursos de fábrica y cola de mensaje:

```
factory.Close();
myQueueClient.Close();
namespaceClient.DeleteQueue("IssueTrackingQueue");
```

### <a name="call-the-queue-method"></a>Llamar al método de cola

El último paso consiste en Agregar una instrucción que llama el `Queue()` método de `Main()`. Agregue la siguiente línea de código resaltada al final de Main():
    
```
public static void Main(string[] args)
{
    // Collect user input
    CollectUserInput();
    
    // Populate test data
    issues = ParseCSVFile();
    MessageList = GenerateMessages(issues);
    
    // Add this call
    Queue();
}
```

### <a name="example"></a>Ejemplo

El código siguiente contiene la aplicación de **QueueSample** completa.

```
using System;
using System.Collections.Generic;
using System.Data;
using System.IO;
using System.Threading;
using System.Threading.Tasks;
using Microsoft.ServiceBus.Messaging;

namespace Microsoft.ServiceBus.Samples
{
    public class Program
    {
        private static DataTable issues;
        private static List<BrokeredMessage> MessageList;

        // Add these variables
        private static string ServiceNamespace;
        private static string sasKeyName = "RootManageSharedAccessKey";
        private static string sasKeyValue;

        static void Main(string[] args)
        {

            // Populate test data
            issues = ParseCSVFile();
            MessageList = GenerateMessages(issues);

            // Collect user input
            CollectUserInput();

            Queue();

        }

        static async Task Queue()
        {
            // Create management credentials
            TokenProvider credentials = TokenProvider.CreateSharedAccessSignatureTokenProvider(sasKeyName, sasKeyValue);
            NamespaceManager namespaceClient = new NamespaceManager(ServiceBusEnvironment.CreateServiceUri("sb", "<yourNamespace>", string.Empty), credentials);

            QueueDescription myQueue;

            if (namespaceClient.QueueExists("IssueTrackingQueue"))
            {
                namespaceClient.DeleteQueue("IssueTrackingQueue");
            }
            
            myQueue = namespaceClient.CreateQueue("IssueTrackingQueue");
            
            MessagingFactory factory = MessagingFactory.Create(ServiceBusEnvironment.CreateServiceUri("sb", "<yourNamespace>", string.Empty), credentials);

            QueueClient myQueueClient = factory.CreateQueueClient("IssueTrackingQueue");

            // Send messages
            Console.WriteLine("Now sending messages to the queue.");
            for (int count = 0; count < 6; count++)
            {
                var issue = MessageList[count];
                issue.Label = issue.Properties["IssueTitle"].ToString();
                await myQueueClient.SendAsync(issue);
                Console.WriteLine(string.Format("Message sent: {0}, {1}", issue.Label, issue.MessageId));
            }

            Console.WriteLine("Now receiving messages from Queue.");
            BrokeredMessage message;
            while ((message = await myQueueClient.ReceiveAsync(new TimeSpan(hours: 0, minutes: 1, seconds: 5))) != null)
            {
                Console.WriteLine(string.Format("Message received: {0}, {1}, {2}", message.SequenceNumber, message.Label, message.MessageId));
                message.Complete();

                Console.WriteLine("Processing message (sleeping...)");
                Thread.Sleep(1000);
            }

            factory.Close();
            myQueueClient.Close();
            namespaceClient.DeleteQueue("IssueTrackingQueue");


        }

        static void CollectUserInput()
        {
            // User service namespace
            Console.Write("Please enter the namespace to use: ");
            ServiceNamespace = Console.ReadLine();

            // Issuer key
            Console.Write("Enter the SAS key to use: ");
            sasKeyValue = Console.ReadLine();
        }

        static List<BrokeredMessage> GenerateMessages(DataTable issues)
        {
            // Instantiate the brokered list object
            List<BrokeredMessage> result = new List<BrokeredMessage>();

            // Iterate through the table and create a brokered message for each row
            foreach (DataRow item in issues.Rows)
            {
                BrokeredMessage message = new BrokeredMessage();
                foreach (DataColumn property in issues.Columns)
                {
                    message.Properties.Add(property.ColumnName, item[property]);
                }
                result.Add(message);
            }
            return result;
        }

        static DataTable ParseCSVFile()
        {
            DataTable tableIssues = new DataTable("Issues");
            string path = @"..\..\data.csv";
            try
            {
                using (StreamReader readFile = new StreamReader(path))
                {
                    string line;
                    string[] row;

                    // create the columns
                    line = readFile.ReadLine();
                    foreach (string columnTitle in line.Split(','))
                    {
                        tableIssues.Columns.Add(columnTitle);
                    }

                    while ((line = readFile.ReadLine()) != null)
                    {
                        row = line.Split(',');
                        tableIssues.Rows.Add(row);
                    }
                }
            }
            catch (Exception e)
            {
                Console.WriteLine("Error:" + e.ToString());
            }

            return tableIssues;
        }
    }
}
```

## <a name="build-and-run-the-queuesample-application"></a>Crear y ejecutar la aplicación QueueSample

Ahora que ha completado los pasos anteriores, puede crear y ejecutar la aplicación **QueueSample** .

### <a name="build-the-queuesample-application"></a>Generar la aplicación QueueSample

En Visual Studio, en el menú **Generar** , haga clic en **Generar solución**o presione **Ctrl + Mayús + B**. Si se producen errores, compruebe que el código es correcto basado en el ejemplo completo presentado al final del paso anterior.

## <a name="next-steps"></a>Pasos siguientes

Este tutorial ha mostrado cómo crear un cliente de Bus de servicio de aplicación y usar las capacidades de mensajería de Bus de servicio con el servicio. Para obtener un tutorial similar que utiliza [retransmisión](service-bus-messaging-overview.md#Relayed-messaging)de Bus de servicio, consulte el [tutorial mensajería de retransmisión de Bus de servicio](../service-bus-relay/service-bus-relay-tutorial.md).

Para obtener más información acerca de [Bus de servicio](https://azure.microsoft.com/services/service-bus/), consulte los temas siguientes.

- [Introducción a mensajería Bus de servicio](service-bus-messaging-overview.md)
- [Conceptos básicos de Bus de servicio](service-bus-fundamentals-hybrid-solutions.md)
- [Arquitectura de Bus de servicio](service-bus-architecture.md)

