<properties 
    pageTitle="Tutorial de retransmisión de Bus de servicio | Microsoft Azure"
    description="Generar a un cliente de Bus de servicios de aplicación y servicio mediante retransmisión de Bus de servicio."
    services="service-bus"
    documentationCenter="na"
    authors="sethmanheim"
    manager="timlt"
    editor="tysonn" />
<tags 
    ms.service="service-bus"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="na"
    ms.date="09/27/2016"
    ms.author="sethm" />

# <a name="service-bus-relay-tutorial"></a>Tutorial de retransmisión de Bus de servicio

Este tutorial describe cómo crear una aplicación de cliente de Bus de servicio simple y el servicio con las capacidades de "retransmisión" Bus de servicio. Para obtener un tutorial correspondiente que usa [con mensajería](../service-bus-messaging/service-bus-messaging-overview.md#Brokered-messaging)Bus de servicio, consulte el [Servicio de Bus negociada mensajería .NET Tutorial](../service-bus-messaging/service-bus-brokered-tutorial-dotnet.md).

En este curso le ofrece una descripción de los pasos necesarios para crear una aplicación de cliente y el servicio de Bus de servicio. Como sus homólogos WCF, un servicio es una construcción que expone uno o más extremos, cada una de las cuales expone una o varias de las operaciones de servicio. El extremo de un servicio especifica una dirección donde se encuentra el servicio, un enlace que contiene la información que un cliente debe comunicarse con el servicio y un contrato que define la funcionalidad proporcionada por el servicio a sus clientes. La diferencia entre un WCF y un servicio de Bus de servicio principal es que el extremo se expone en la nube en lugar de localmente en el equipo.

Después de trabajar a través de la secuencia de temas en este tutorial, tendrá un servicio en ejecución y un cliente que puede invocar las operaciones del servicio. El primer tema describe cómo configurar una cuenta. Los pasos siguientes describen cómo definir un servicio que usa un contrato, cómo implementar el servicio y cómo configurar el servicio de código. También se describen cómo hospedar y ejecutar el servicio. El servicio que se crea se hospeda a sí mismo y el cliente y el servicio se ejecuten en el mismo equipo. Puede configurar el servicio mediante código o un archivo de configuración.

Los últimos tres pasos describen cómo crear una aplicación de cliente, configurar la aplicación cliente y crear y usar a un cliente que puede tener acceso a la funcionalidad del host.

Todos los temas de esta sección se supone que está usando Visual Studio como entorno de desarrollo.

## <a name="sign-up-for-an-account"></a>Registrarse para una cuenta

El primer paso es crear un espacio de nombres y obtener una clave de firma de acceso compartido (SA). Un espacio de nombres proporciona un límite de aplicación para cada aplicación que se expone a través de Bus de servicio. El sistema genera automáticamente una clave de SA cuando se crea un espacio de nombres de servicio. La combinación de espacio de nombres de servicio y clave SA proporciona las credenciales de Bus de servicio autenticar el acceso a la aplicación.

[AZURE.INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

## <a name="define-a-wcf-service-contract-to-use-with-service-bus"></a>Definir un contrato de servicio WCF para usar con Bus de servicio

El contrato de servicio especifica las operaciones que admite el servicio de (la terminología del servicio Web de funciones o métodos). Contratos se crean al definir una interfaz de C++, C# o Visual Basic. Cada método de la interfaz corresponde a una operación de servicio específico. Cada interfaz debe tener el atributo [ServiceContractAttribute](https://msdn.microsoft.com/library/system.servicemodel.servicecontractattribute.aspx) aplicado, y cada operación debe tener el atributo [OperationContractAttribute](https://msdn.microsoft.com/library/system.servicemodel.operationcontractattribute.aspx) aplicado. Si un método en una interfaz que tiene el atributo [ServiceContractAttribute](https://msdn.microsoft.com/library/system.servicemodel.servicecontractattribute.aspx) no tiene el atributo [OperationContractAttribute](https://msdn.microsoft.com/library/system.servicemodel.operationcontractattribute.aspx) , no se expone a ese método. El código para estas tareas se proporciona en el ejemplo siguiente el procedimiento. Para obtener una descripción más grande de contratos y servicios, consulte [diseñar los servicios de implementación y](https://msdn.microsoft.com/library/ms729746.aspx) en la documentación de WCF.

### <a name="to-create-a-service-bus-contract-with-an-interface"></a>Para crear un contrato de servicio Bus con una interfaz

1. Abra Visual Studio como administrador haciendo clic en el programa en el menú **Inicio** y seleccione **Ejecutar como administrador**.

2. Crear un nuevo proyecto de aplicación de consola. Haga clic en el menú **archivo** , seleccione **nuevo**y haga clic en **proyecto**. En el cuadro de diálogo **Nuevo proyecto** , haga clic en **Visual C#** ( **Visual C#** no aparece, busque en **Otros idiomas**). Haga clic en la plantilla de **Aplicación de consola** y asígnele el nombre **EchoService**. Haga clic en **Aceptar** para crear el proyecto.

    ![][2]

3. Instale el paquete NuGet Bus de servicio. Este paquete agrega automáticamente las referencias a las bibliotecas de Bus de servicio, así como la WCF **System.ServiceModel**. [System.ServiceModel](https://msdn.microsoft.com/library/system.servicemodel.aspx) es el espacio de nombres que permite acceso mediante programación a las características básicas de WCF. Bus de servicio usa muchos de los objetos y atributos de WCF para definir contratos de servicio.

    En el Explorador de soluciones, haga clic en la solución y, a continuación, haga clic en **Administrar paquetes de NuGet para la solución**. Haga clic en la ficha **Examinar** , busque `Microsoft Azure Service Bus`. Asegúrese de que el nombre del proyecto está seleccionado en el cuadro de **versiones** . Haga clic en **instalar**y acepte los términos de uso.

    ![][3]

3. En el Explorador de soluciones, haga doble clic en el archivo Program.cs para abrirlo en el editor, si no está ya abierto.

4. Agregue las siguientes instrucciones using en la parte superior del archivo:

    ```
    using System.ServiceModel;
    using Microsoft.ServiceBus;
    ```

1. Cambiar el espacio de nombres de su nombre predeterminado de **EchoService** a **Microsoft.ServiceBus.Samples**.

    >[AZURE.IMPORTANT] Este tutorial utiliza el C# espacio de nombres **Microsoft.ServiceBus.Samples**, que es el espacio de nombres del tipo de contrato administrada que se usa en el archivo de configuración en el paso de [Configurar el cliente WCF](#configure-the-wcf-client) . Puede especificar cualquier espacio de nombres que desee al generar este ejemplo; Sin embargo, el tutorial no funcionarán a menos que, a continuación, modifique los espacios de nombres del contrato y servicio según corresponda, en el archivo de configuración de aplicación. El espacio de nombres especificado en el archivo App.config debe ser el mismo que el espacio de nombres especificado en los archivos de C#.

1. Directamente después de la `Microsoft.ServiceBus.Samples` declaración de espacio de nombres, pero en el espacio de nombres, definir una nueva interfaz denominada `IEchoContract` y aplicar el `ServiceContractAttribute` atributo a la interfaz con un valor de espacio de nombres de **http://samples.microsoft.com/ServiceModel/Relay/**. El valor de espacio de nombres difiere del espacio de nombres que usar en todo el ámbito de su código. En su lugar, el valor de espacio de nombres se usa como un identificador único para este contrato. Especificar el espacio de nombres explícitamente impide que el valor de espacio de nombres predeterminado que se agrega el nombre de contrato.

    ```
    [ServiceContract(Name = "IEchoContract", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IEchoContract
    {
    }
    ```

    >[AZURE.NOTE] Normalmente, el espacio de nombres de contrato de servicio contiene un esquema de nombres que incluye información de versión. Incluida la información de versión del espacio de nombres de contrato de servicio permite servicios a aislar los principales cambios definiendo un nuevo contrato de servicio con un nuevo espacio de nombres y exponer en un nuevo punto final. De esta manera, los clientes pueden seguir usando el contrato de servicio antiguo sin tener que se pueden actualizar. Información de versión puede constar de una fecha o un número de compilación. Para obtener más información, consulte [Control de versiones de servicio](http://go.microsoft.com/fwlink/?LinkID=180498). Para el propósito de este tutorial, el esquema de nombres del espacio de nombres del contrato de servicio no contiene información de versión.

1. Dentro de la `IEchoContract` interfaz, declarar un método para la operación solo la `IEchoContract` contrato expone en la interfaz y aplicar el `OperationContractAttribute` atributo al método que desea exponer como parte del contrato de Bus de servicio público.

    ```
    [OperationContract]
    string Echo(string text);
    ```

1. Justo después del `IEchoContract` definición de interfaz, declarar un canal que herede de ambas `IEchoContract` y también la `IClientChannel` interfaz, como se muestra aquí:

    ```
    public interface IEchoChannel : IEchoContract, IClientChannel { }
    ```

    Un canal es el objeto WCF que el host y el cliente pasan información entre sí. Más adelante, escriba código contra el canal para devolver información entre las dos aplicaciones.

1. En el menú **Generar** , haga clic en **Generar solución** o presione **Ctrl + Mayús + B** para confirmar la exactitud de su trabajo hasta ese momento.

### <a name="example"></a>Ejemplo

El código siguiente muestra una interfaz básica que define un contrato de Bus de servicio.

```
using System;
using System.ServiceModel;

namespace Microsoft.ServiceBus.Samples
{
    [ServiceContract(Name = "IEchoContract", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IEchoContract
    {
        [OperationContract]
        String Echo(string text);
    }

    public interface IEchoChannel : IEchoContract, IClientChannel { }

    class Program
    {
        static void Main(string[] args)
        {
        }
    }
}
```

Ahora que se ha creado la interfaz, puede implementar la interfaz.

## <a name="implement-the-wcf-contract-to-use-service-bus"></a>Implementar el contrato WCF usar Bus de servicio

Crear una retransmisión de Bus de servicio requiere que cree primero el contrato, que se define mediante una interfaz. Para obtener más información sobre la creación de la interfaz, vea el paso anterior. El siguiente paso es implementar la interfaz. Esto implica la creación de una clase denominada `EchoService` que implementa definido por el usuario `IEchoContract` interfaz. Después de implementar la interfaz, a continuación, configure la interfaz mediante un archivo de configuración App.config. El archivo de configuración contiene la información necesaria para la aplicación, como el nombre del servicio, el nombre del contrato y el tipo de protocolo que se utiliza para comunicarse con Bus de servicio. El código utilizado para estas tareas se proporciona en el ejemplo siguiente el procedimiento. Para obtener información más general sobre cómo implementar un contrato de servicio, vea [Implementación de contratos de servicio](https://msdn.microsoft.com/library/ms733764.aspx) en la documentación de WCF.

1. Crear una nueva clase denominada `EchoService` directamente después de la definición de la `IEchoContract` interfaz. La `EchoService` clase implementa el `IEchoContract` interfaz. 

    ```
    class EchoService : IEchoContract
    {
    }
    ```
    
    Similar a otras implementaciones de interfaz, puede implementar la definición en un archivo diferente. Sin embargo, para este tutorial, la aplicación se encuentra en el mismo archivo como la definición de la interfaz y el `Main` método.

1. Aplicar el atributo [ServiceBehaviorAttribute](https://msdn.microsoft.com/library/system.servicemodel.servicebehaviorattribute.aspx) a la `IEchoContract` interfaz. El atributo especifica el nombre de servicio y el espacio de nombres. Después de hacerlo, el `EchoService` clase aparece como sigue:

    ```
    [ServiceBehavior(Name = "EchoService", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
    class EchoService : IEchoContract
    {
    }
    ```

1. Implementar la `Echo` método definido en la `IEchoContract` interfaz en el `EchoService` clase. 

    ```
    public string Echo(string text)
    {
        Console.WriteLine("Echoing: {0}", text);
        return text;
    }
    ```

1. Haga clic en **Generar**y luego haga clic en **Generar solución** para confirmar la exactitud de su trabajo.

### <a name="to-define-the-configuration-for-the-service-host"></a>Para definir la configuración para el host de servicio

1. El archivo de configuración es muy similar a un archivo de configuración de WCF. Incluye el nombre del servicio, extremo (es decir, la ubicación expone Bus de servicio para que los clientes y hosts para comunicarse con ellos) y el enlace (el tipo de protocolo que se utiliza para comunicarse). La diferencia principal es que este extremo de servicio configuradas hace referencia a un enlace de [NetTcpRelayBinding](https://msdn.microsoft.com/library/azure/microsoft.servicebus.nettcprelaybinding.aspx) que no forma parte de .NET Framework. [NetTcpRelayBinding](https://msdn.microsoft.com/library/microsoft.servicebus.nettcprelaybinding.aspx) es uno de los enlaces definidos por el Bus de servicio.

1. En el **Explorador de soluciones**, haga doble clic en el archivo App.config para abrirlo en el editor de Visual Studio.

2. En la `<appSettings>` elemento, reemplazar los marcadores de posición con el nombre de su espacio de nombres de servicio y las asociaciones de seguridad de clave que ha copiado en un paso anterior. 

1. Dentro de la `<system.serviceModel>` etiquetas, agregue un `<services>` elemento. Puede definir varios de Bus de servicio de aplicaciones en un solo archivo de configuración. Sin embargo, este tutorial define solo uno.
 
    ```
    <?xmlversion="1.0"encoding="utf-8"?>
    <configuration>
      <system.serviceModel>
        <services>

        </services>
      </system.serviceModel>
    </configuration>
    ```

1. Dentro de la `<services>` elemento, agregue un `<service>` elemento para definir el nombre del servicio.

    ```
    <service name="Microsoft.ServiceBus.Samples.EchoService">
    </service>
    ```

1. Dentro de la `<service>` elemento, definir la ubicación del contrato de punto final y también el tipo de enlace del extremo.

    ```
    <endpoint contract="Microsoft.ServiceBus.Samples.IEchoContract" binding="netTcpRelayBinding"/>
    ```

    El punto final define dónde debe buscar el cliente de la aplicación host. Más adelante, el tutorial utiliza este paso para crear un URI que expone el host a través de Bus de servicio. El enlace declara que estamos utilizando TCP como protocolo para comunicarse con Bus de servicio.

1. En el menú **Generar** , haga clic en **Generar solución** para confirmar la exactitud de su trabajo.

### <a name="example"></a>Ejemplo

El código siguiente muestra la implementación del contrato de servicio.

```
[ServiceBehavior(Name = "EchoService", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]

    class EchoService : IEchoContract
    {
        public string Echo(string text)
        {
            Console.WriteLine("Echoing: {0}", text);
            return text;
        }
    }
```

El código siguiente muestra el formato básico del archivo App.config asociado con el host del servicio.

```
<?xml version="1.0" encoding="utf-8" ?>
<configuration>
  <system.serviceModel>
    <services>
      <service name="Microsoft.ServiceBus.Samples.EchoService">
        <endpoint contract="Microsoft.ServiceBus.Samples.IEchoContract" binding="netTcpRelayBinding" />
      </service>
    </services>
    <extensions>
      <bindingExtensions>
        <add name="netTcpRelayBinding"
                    type="Microsoft.ServiceBus.Configuration.NetTcpRelayBindingCollectionElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
      </bindingExtensions>
    </extensions>
  </system.serviceModel>
</configuration>
```

## <a name="host-and-run-a-basic-web-service-to-register-with-service-bus"></a>Hospedar y ejecutar un servicio Web básico para registrar con Bus de servicio

Este paso describe cómo ejecutar un servicio de Bus de servicio básico.

### <a name="to-create-the-service-bus-credentials"></a>Para crear las credenciales de Bus de servicio

1. En `Main()`, cree dos variables en la que desea almacenar el espacio de nombres y las asociaciones de seguridad de clave que se leen desde la ventana de consola.

    ```
    Console.Write("Your Service Namespace: ");
    string serviceNamespace = Console.ReadLine();
    Console.Write("Your SAS key: ");
    string sasKey = Console.ReadLine();
    ```

    La clave de SA se utilizará más adelante para tener acceso a su proyecto de Bus de servicio. El espacio de nombres se pasa como un parámetro para `CreateServiceUri` para crear un URI de servicio.

4. Con un objeto [TransportClientEndpointBehavior](https://msdn.microsoft.com/library/microsoft.servicebus.transportclientendpointbehavior.aspx) , declare que va a usar una clave de SA como el tipo de credencial. Agregue el código siguiente directamente después del código agregado en el último paso.

    ```
    TransportClientEndpointBehavior sasCredential = new TransportClientEndpointBehavior();
    sasCredential.TokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider("RootManageSharedAccessKey", sasKey);
    ```

### <a name="to-create-a-base-address-for-the-service"></a>Para crear una dirección base para el servicio

1. Siguiendo el código que agregó en el último paso, crear una `Uri` instancia para la dirección base del servicio. Este URI especifica la combinación de Bus de servicio, el espacio de nombres y la ruta de acceso de la interfaz de servicio.

    ```
    Uri address = ServiceBusEnvironment.CreateServiceUri("sb", serviceNamespace, "EchoService");
    ```

    "sb" es una abreviatura para la combinación de Bus de servicio e indica que estamos utilizando TCP como protocolo. También previamente se ha indicado en el archivo de configuración, cuando se ha especificado [NetTcpRelayBinding](https://msdn.microsoft.com/library/microsoft.servicebus.nettcprelaybinding.aspx) como el enlace.
    
    En este tutorial, el URI es `sb://putServiceNamespaceHere.windows.net/EchoService`.

### <a name="to-create-and-configure-the-service-host"></a>Crear y configurar el host de servicio

1. Establecer el modo de conectividad en `AutoDetect`.

    ```
    ServiceBusEnvironment.SystemConnectivity.Mode = ConnectivityMode.AutoDetect;
    ```

    El modo de conectividad describe el protocolo que utiliza el servicio para comunicarse con Bus de servicio; HTTP o TCP. Utilizar la configuración predeterminada `AutoDetect`, el servicio intenta conectarse al servicio Bus sobre TCP si está disponible y a continuación, HTTP si TCP no está disponible. Especifica que esto difiere el protocolo del servicio de nota para la comunicación de cliente. Protocolo viene determinado por el enlace utilizado. Por ejemplo, un servicio puede usar el enlace de [BasicHttpRelayBinding](https://msdn.microsoft.com/library/microsoft.servicebus.basichttprelaybinding.aspx) , que se especifica que su extremo (expuesta en Bus de servicio) se comunica con los clientes a través de HTTP. Ese mismo servicio podría especificar **ConnectivityMode.AutoDetect** para que el servicio se comunica con Bus de servicio a través de TCP.

1. Crear el host de servicio, utilizando el URI que creó anteriormente en esta sección.

    ```
    ServiceHost host = new ServiceHost(typeof(EchoService), address);
    ```

    El host del servicio es el objeto WCF que crea una instancia del servicio. A continuación, le pasa el tipo de servicio que desea crear (un `EchoService` tipo) y también a la dirección a la que desea exponer el servicio.

1. En la parte superior del archivo Program.cs, agregue referencias a [System.ServiceModel.Description](https://msdn.microsoft.com/library/system.servicemodel.description.aspx) y [Microsoft.ServiceBus.Description](https://msdn.microsoft.com/library/microsoft.servicebus.description.aspx).

    ```
    using System.ServiceModel.Description;
    using Microsoft.ServiceBus.Description;
    ```

1. En `Main()`, configurar el extremo para habilitar el acceso del público.

    ```
    IEndpointBehavior serviceRegistrySettings = new ServiceRegistrySettings(DiscoveryType.Public);
    ```

    Este paso informa Bus de servicio que se puede encontrar la aplicación públicamente examinando Service Bus ATOM fuente para el proyecto. Si establece **DiscoveryType** en **privado**, un cliente aún podrá tener acceso al servicio. Sin embargo, el servicio no aparecerán cuando busca el espacio de nombres de Bus de servicio. En su lugar, el cliente tendría que saber antes de la ruta de acceso de extremo.

1. Las credenciales de servicio se aplican a los extremos de servicio definidos en el archivo App.config:

    ```
    foreach (ServiceEndpoint endpoint in host.Description.Endpoints)
    {
        endpoint.Behaviors.Add(serviceRegistrySettings);
        endpoint.Behaviors.Add(sasCredential);
    }
    ```

    Como se indica en el paso anterior, podría haber declarado varios servicios y extremos en el archivo de configuración. Si tenía, este código podría recorrer el archivo de configuración y buscar cada extremo al que se deben aplicar sus credenciales. Sin embargo, para este tutorial, el archivo de configuración tiene solo un extremo.

### <a name="to-open-the-service-host"></a>Para abrir el host del servicio

1. Abra el servicio.

    ```
    host.Open();
    ```

1. Informar al usuario que el servicio se está ejecutando y se explica cómo cerrar el servicio.

    ```
    Console.WriteLine("Service address: " + address);
    Console.WriteLine("Press [Enter] to exit");
    Console.ReadLine();
    ```

1. Cuando haya terminado, cierre el host del servicio.

    ```
    host.Close();
    ```

1. Presione **Ctrl + Mayús + B** para generar el proyecto.

### <a name="example"></a>Ejemplo

En el ejemplo siguiente se incluye el contrato de servicio e implementación de los pasos anteriores en el tutorial y aloja el servicio en una aplicación de consola. Compilar en un archivo ejecutable denominado EchoService.exe las siguientes.

```
using System;
using System.ServiceModel;
using System.ServiceModel.Description;
using Microsoft.ServiceBus;
using Microsoft.ServiceBus.Description;

namespace Microsoft.ServiceBus.Samples
{
    [ServiceContract(Name = "IEchoContract", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IEchoContract
    {
        [OperationContract]
        String Echo(string text);
    }

    public interface IEchoChannel : IEchoContract, IClientChannel { };

    [ServiceBehavior(Name = "EchoService", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
    class EchoService : IEchoContract
    {
        public string Echo(string text)
        {
            Console.WriteLine("Echoing: {0}", text);
            return text;
        }
    }

    class Program
    {
        static void Main(string[] args)
        {

            ServiceBusEnvironment.SystemConnectivity.Mode = ConnectivityMode.AutoDetect;         
          
            Console.Write("Your Service Namespace: ");
            string serviceNamespace = Console.ReadLine();
            Console.Write("Your SAS key: ");
            string sasKey = Console.ReadLine();

           // Create the credentials object for the endpoint.
            TransportClientEndpointBehavior sasCredential = new TransportClientEndpointBehavior();
            sasCredential.TokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider("RootManageSharedAccessKey", sasKey);

            // Create the service URI based on the service namespace.
            Uri address = ServiceBusEnvironment.CreateServiceUri("sb", serviceNamespace, "EchoService");

            // Create the service host reading the configuration.
            ServiceHost host = new ServiceHost(typeof(EchoService), address);

            // Create the ServiceRegistrySettings behavior for the endpoint.
            IEndpointBehavior serviceRegistrySettings = new ServiceRegistrySettings(DiscoveryType.Public);

            // Add the Service Bus credentials to all endpoints specified in configuration.
            foreach (ServiceEndpoint endpoint in host.Description.Endpoints)
            {
                endpoint.Behaviors.Add(serviceRegistrySettings);
                endpoint.Behaviors.Add(sasCredential);
            }
            
            // Open the service.
            host.Open();

            Console.WriteLine("Service address: " + address);
            Console.WriteLine("Press [Enter] to exit");
            Console.ReadLine();

            // Close the service.
            host.Close();
        }
    }
}
```

## <a name="create-a-wcf-client-for-the-service-contract"></a>Crear a un cliente WCF del contrato de servicio

El siguiente paso es crear una aplicación de cliente de Bus de servicio básica y definir el contrato de servicio que se implementará en pasos posteriores. Nota muchos de estos pasos son similares a los pasos que se utiliza para crear un servicio: definición de un contrato, un archivo App.config de edición de archivos, con las credenciales para conectarse a Bus de servicio y así sucesivamente. El código utilizado para estas tareas se proporciona en el ejemplo siguiente el procedimiento.

1. Crear un nuevo proyecto en la solución actual de Visual Studio para el cliente mediante el procedimiento siguiente:
    1. En el Explorador de soluciones, en la misma solución que contiene el servicio, haga clic en la solución actual (no en el proyecto) y haga clic en **Agregar**. A continuación, haga clic en **Nuevo proyecto**.
    2. En el cuadro de diálogo **Agregar nuevo proyecto** , haga clic en **Visual C#** (si **Visual C#** no aparece, busque en **Otros idiomas**), seleccione la plantilla de **Aplicación de consola** y asígnele el nombre **EchoClient**.
    3. Haga clic en **Aceptar**.
<br />

1. En el Explorador de soluciones, haga doble clic en el archivo Program.cs en el proyecto **EchoClient** para abrirlo en el editor, si no está ya abierto.

1. Cambiar el espacio de nombres de su nombre predeterminado de `EchoClient` a `Microsoft.ServiceBus.Samples`.

1. Instale el [paquete NuGet Bus de servicio](https://www.nuget.org/packages/WindowsAzure.ServiceBus). En el Explorador de soluciones, haga clic en el proyecto **EchoClient** y, a continuación, haga clic en **Administrar paquetes de NuGet**. Haga clic en la ficha **Examinar** , busque `Microsoft Azure Service Bus`. Haga clic en **instalar**y acepte los términos de uso.

    ![][3]

1. Agregar un `using` declaración de espacio de nombres en el archivo Program.cs [System.ServiceModel](https://msdn.microsoft.com/library/system.servicemodel.aspx) . 

    ```
    using System.ServiceModel;
    ```

1. Agregue la definición del contrato de servicio para el espacio de nombres, como se muestra en el ejemplo siguiente. Tenga en cuenta que esta definición es idéntica a la definición utilizada en el proyecto de **servicio** . Debe agregar este código en la parte superior de la `Microsoft.ServiceBus.Samples` espacio de nombres.

    ```
    [ServiceContract(Name = "IEchoContract", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IEchoContract
    {
        [OperationContract]
        string Echo(string text);
    }

    public interface IEchoChannel : IEchoContract, IClientChannel { }
    ```

1. Presione **Ctrl + Mayús + B** para crear al cliente.

### <a name="example"></a>Ejemplo

El código siguiente muestra el estado actual del archivo Program.cs en el proyecto EchoClient.

```
using System;
using Microsoft.ServiceBus;
using System.ServiceModel;

namespace Microsoft.ServiceBus.Samples
{

    [ServiceContract(Name = "IEchoContract", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IEchoContract
    {
        [OperationContract]
        string Echo(string text);
    }

    public interface IEchoChannel : IEchoContract, IClientChannel { }


    class Program
    {
        static void Main(string[] args)
        {
        }
    }
}
```

## <a name="configure-the-wcf-client"></a>Configurar al cliente WCF

En este paso, creará un archivo App.config para una aplicación de cliente básico que tiene acceso al servicio que creó anteriormente en este tutorial. Este archivo App.config define el contrato, el enlace y el nombre del extremo. El código utilizado para estas tareas se proporciona en el ejemplo siguiente el procedimiento.

1. En el Explorador de soluciones, en el proyecto **EchoClient** , haga doble clic en **App.config** para abrir el archivo en el editor de Visual Studio.

2. En la `<appSettings>` elemento, reemplazar los marcadores de posición con el nombre de su espacio de nombres de servicio y las asociaciones de seguridad de clave que ha copiado en un paso anterior.

1. En el elemento system.serviceModel, agregue un `<client>` elemento.

    ```
    <?xmlversion="1.0"encoding="utf-8"?>
    <configuration>
      <system.serviceModel>
        <client>
        </client>
      </system.serviceModel>
    </configuration>
    ```

    Este paso declara la definición de una aplicación de cliente de WCF estilo.

1. Dentro de la `client` elemento, defina el nombre, el contrato y el tipo de enlace del extremo.

    ```
    <endpoint name="RelayEndpoint"
                    contract="Microsoft.ServiceBus.Samples.IEchoContract"
                    binding="netTcpRelayBinding"/>
    ```

    Este paso define el nombre del extremo, el contrato definido en el servicio y el hecho de que la aplicación cliente utiliza TCP para comunicarse con Bus de servicio. El nombre del extremo se usa en el siguiente paso para vincular esta configuración del extremo con el URI del servicio.

1. Haga clic en **archivo**, **Guardar todo**.

## <a name="example"></a>Ejemplo

El código siguiente muestra el archivo App.config para el cliente de eco.

```
<?xml version="1.0" encoding="utf-8" ?>
<configuration>
  <system.serviceModel>
    <client>
      <endpoint name="RelayEndpoint"
                      contract="Microsoft.ServiceBus.Samples.IEchoContract"
                      binding="netTcpRelayBinding"/>
    </client>
    <extensions>
      <bindingExtensions>
        <add name="netTcpRelayBinding"
                    type="Microsoft.ServiceBus.Configuration.NetTcpRelayBindingCollectionElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
      </bindingExtensions>
    </extensions>
  </system.serviceModel>
</configuration>
```

## <a name="implement-the-wcf-client-to-call-service-bus"></a>Implementar el cliente WCF para llamar Bus de servicio

En este paso, implementar una aplicación de cliente básico que tiene acceso al servicio que creó anteriormente en este tutorial. El cliente similar al servicio, realiza muchas de las mismas operaciones para tener acceso a Bus de servicio:

1. Establece el modo de conectividad.
1. Crea el URI que se encuentra el servicio de host.
1. Define las credenciales de seguridad.
1. Se aplica a las credenciales para la conexión.
1. Se abre la conexión.
1. Realiza las tareas específicas de la aplicación.
1. Cierra la conexión.

Sin embargo, una de las principales diferencias es que la aplicación cliente usa un canal para conectarse a Bus de servicio, mientras que el servicio utiliza una llamada a **ServiceHost**. El código utilizado para estas tareas se proporciona en el ejemplo siguiente el procedimiento.

### <a name="to-implement-a-client-application"></a>Implementar una aplicación de cliente

1. Establecer el modo de conectividad **detectar automáticamente**. Agregue el código siguiente dentro de la `Main()` método de la aplicación **EchoClient** .

    ```
    ServiceBusEnvironment.SystemConnectivity.Mode = ConnectivityMode.AutoDetect;
    ```

1. Definir variables para almacenar los valores para el espacio de nombres de servicio y la clave de SA que se leen desde la consola.

    ```
    Console.Write("Your Service Namespace: ");
    string serviceNamespace = Console.ReadLine();
    Console.Write("Your SAS Key: ");
    string sasKey = Console.ReadLine();
    ```

1. Crear el URI que define la ubicación del host de su proyecto de Bus de servicio.

    ```
    Uri serviceUri = ServiceBusEnvironment.CreateServiceUri("sb", serviceNamespace, "EchoService");
    ```

1. Crear el objeto de credenciales para el extremo del espacio de nombres de servicio.

    ```
    TransportClientEndpointBehavior sasCredential = new TransportClientEndpointBehavior();
    sasCredential.TokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider("RootManageSharedAccessKey", sasKey);
    ```

1. Crear el generador de canales que carga la configuración que se describe en el archivo App.config.

    ```
    ChannelFactory<IEchoChannel> channelFactory = new ChannelFactory<IEchoChannel>("RelayEndpoint", new EndpointAddress(serviceUri));
    ```

    Un generador de canales es un objeto WCF que crea un canal a través del cual comunican las aplicaciones cliente y el servicio.

1. Aplicar las credenciales de Bus de servicio.

    ```
    channelFactory.Endpoint.Behaviors.Add(sasCredential);
    ```

1. Crear y abrir el canal al servicio.

    ```
    IEchoChannel channel = channelFactory.CreateChannel();
    channel.Open();
    ```

1. Escribir la interfaz de usuario básica y funcionalidad para el eco.

    ```
    Console.WriteLine("Enter text to echo (or [Enter] to exit):");
    string input = Console.ReadLine();
    while (input != String.Empty)
    {
        try
        {
            Console.WriteLine("Server echoed: {0}", channel.Echo(input));
        }
        catch (Exception e)
        {
            Console.WriteLine("Error: " + e.Message);
        }
        input = Console.ReadLine();
    }
    ```

    Tenga en cuenta que el código utiliza la instancia del objeto de canal como proxy para el servicio.

1. Cierre el canal y la fábrica.

    ```
    channel.Close();
    channelFactory.Close();
    ```

## <a name="to-run-the-applications"></a>Para ejecutar las aplicaciones

1. Presione **Ctrl + Mayús + B** para generar la solución. Esto crea el proyecto de cliente y el proyecto de servicio que creó en los pasos anteriores.

2. Antes de ejecutar la aplicación cliente, debe asegurarse de que la aplicación de servicio se está ejecutando. En el Explorador de soluciones en Visual Studio, haga clic en la solución **EchoService** y luego haga clic en **Propiedades**.

3. En el cuadro de diálogo de propiedades de solución, haga clic en **Proyecto de inicio**, haga clic en el botón de **varios proyectos de inicio** . Asegúrese de que **EchoService** aparece en primer lugar en la lista. 

4. Establezca el cuadro de **acción** para los **EchoService** **EchoClient** proyectos y en **Inicio**.

    ![][5]

5. Haga clic en **dependencias del proyecto**. En el cuadro de **proyectos** , seleccione **EchoClient**. En el cuadro **depende** , asegúrese de que **EchoService** está activada.

    ![][6]

6. Haga clic en **Aceptar** para cerrar el cuadro de diálogo de **Propiedades** .

7. Presione **F5** para ejecutar ambos proyectos.

8. Ambas ventanas de consola abra y solicitan el nombre de espacio de nombres. El servicio debe ejecutar primero, por tanto, en la ventana de consola **EchoService** , escriba el espacio de nombres y, a continuación, presione **ENTRAR**.

9. A continuación, se le pedirá la clave de SA. Escriba la clave de SA y presione ENTRAR.

    Aquí es ejemplo de salida de la ventana de la consola. Tenga en cuenta que los valores proporcionan por ejemplo hay solo con fines.

    `Your Service Namespace: myNamespace`
    `Your SAS Key: <SAS key value>`

    La aplicación de servicio imprime en la ventana de la consola la dirección en la que está escuchando, tal como se muestra en el ejemplo siguiente.

    `Service address: sb://mynamespace.servicebus.windows.net/EchoService/`
    `Press [Enter] to exit`
    
10. En la ventana de consola **EchoClient** , escriba la misma información que ha escrito previamente de la aplicación de servicio. Siga los pasos anteriores para escribir el mismo espacio de nombres de servicio y los valores de clave SA para la aplicación cliente.

11. Después de introducir estos valores, el cliente abre un canal al servicio y le pide que escriba texto tal como se muestra en el siguiente ejemplo de salida de la consola.

    `Enter text to echo (or [Enter] to exit):` 

    Escriba un texto para enviar a la aplicación de servicio y presione ENTRAR. Este texto se envía al servicio a través de la operación de servicio de eco y aparece en la ventana de la consola de servicio como en el siguiente resultado de ejemplo.

    `Echoing: My sample text`

    La aplicación cliente recibe el valor devuelto por la `Echo` operación, que es el texto original y lo imprime en la ventana de la consola. A continuación se muestra ejemplo de salida de la ventana de la consola de cliente.

    `Server echoed: My sample text`

12. Puede seguir enviando mensajes de texto desde el cliente al servicio de esta manera. Cuando haya terminado, presione ENTRAR en la consola de cliente y el servicio de windows para finalizar ambas aplicaciones.

## <a name="example"></a>Ejemplo

En el ejemplo siguiente se muestra cómo crear una aplicación de cliente, cómo llamar a las operaciones del servicio y cómo cerrar al cliente cuando finaliza la llamada de operación.

```
using System;
using Microsoft.ServiceBus;
using System.ServiceModel;

namespace Microsoft.ServiceBus.Samples
{
    [ServiceContract(Name = "IEchoContract", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IEchoContract
    {
        [OperationContract]
        String Echo(string text);
    }

    public interface IEchoChannel : IEchoContract, IClientChannel { }

    class Program
    {
        static void Main(string[] args)
        {
            ServiceBusEnvironment.SystemConnectivity.Mode = ConnectivityMode.AutoDetect;

            
            Console.Write("Your Service Namespace: ");
            string serviceNamespace = Console.ReadLine();
            Console.Write("Your SAS Key: ");
            string sasKey = Console.ReadLine();



            Uri serviceUri = ServiceBusEnvironment.CreateServiceUri("sb", serviceNamespace, "EchoService");

            TransportClientEndpointBehavior sasCredential = new TransportClientEndpointBehavior();
            sasCredential.TokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider("RootManageSharedAccessKey", sasKey);

            ChannelFactory<IEchoChannel> channelFactory = new ChannelFactory<IEchoChannel>("RelayEndpoint", new EndpointAddress(serviceUri));

            channelFactory.Endpoint.Behaviors.Add(sasCredential);

            IEchoChannel channel = channelFactory.CreateChannel();
            channel.Open();

            Console.WriteLine("Enter text to echo (or [Enter] to exit):");
            string input = Console.ReadLine();
            while (input != String.Empty)
            {
                try
                {
                    Console.WriteLine("Server echoed: {0}", channel.Echo(input));
                }
                catch (Exception e)
                {
                    Console.WriteLine("Error: " + e.Message);
                }
                input = Console.ReadLine();
            }

            channel.Close();
            channelFactory.Close();

        }
    }
}
```

## <a name="next-steps"></a>Pasos siguientes

Este tutorial ha mostrado cómo crear un cliente de Bus de servicio de aplicaciones y servicios con las capacidades de "retransmisión" Bus de servicio. Para obtener un tutorial similar que usa Bus de servicios de [mensajería](../service-bus-messaging/service-bus-messaging-overview.md#Brokered-messaging), consulte el [Servicio de Bus negociada mensajería .NET Tutorial](../service-bus-messaging/service-bus-brokered-tutorial-dotnet.md).

Para obtener más información acerca de Bus de servicio, consulte los temas siguientes.

- [Introducción a mensajería Bus de servicio](../service-bus-messaging/service-bus-messaging-overview.md)
- [Conceptos básicos de Bus de servicio](../service-bus-messaging/service-bus-fundamentals-hybrid-solutions.md)
- [Arquitectura de Bus de servicio](../service-bus-messaging/service-bus-architecture.md)

[Azure classic portal]: http://manage.windowsazure.com

[1]: ./media/service-bus-relay-tutorial/service-bus-policies.png
[2]: ./media/service-bus-relay-tutorial/create-console-app.png
[3]: ./media/service-bus-relay-tutorial/install-nuget.png
[4]: ./media/service-bus-relay-tutorial/create-ns.png
[5]: ./media/service-bus-relay-tutorial/set-projects.png
[6]: ./media/service-bus-relay-tutorial/set-depend.png
