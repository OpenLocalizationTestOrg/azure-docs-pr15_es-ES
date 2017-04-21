<properties
   pageTitle="Introducción a servicios de comunicación fiable | Microsoft Azure"
   description="Información general sobre el modelo de comunicación servicios confiables, incluidos escucha de apertura en servicios, resolver extremos y comunicación entre servicios."
   services="service-fabric"
   documentationCenter=".net"
   authors="vturecek"
   manager="timlt"
   editor="BharatNarasimman"/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="required"
   ms.date="10/19/2016"
   ms.author="vturecek"/>

# <a name="how-to-use-the-reliable-services-communication-apis"></a>Cómo usar la API de comunicación de servicios confiables

Azure tela de servicio como una plataforma es completamente independiente acerca de la comunicación entre los servicios. Todos los protocolos y pilas son aceptables, de UDP a HTTP. Es desarrollador de servicio para elegir cómo deben comunicarse servicios. El marco de la aplicación de servicios confiables proporciona pilas de comunicación integradas, así como las API que puede usar para crear los componentes de comunicación personalizado. 

## <a name="set-up-service-communication"></a>Configurar la comunicación de servicio

La API de servicios fiable utiliza una interfaz simple para la comunicación de servicio. Para abrir un extremo del servicio, simplemente implementar esta interfaz:

```csharp

public interface ICommunicationListener
{
    Task<string> OpenAsync(CancellationToken cancellationToken);

    Task CloseAsync(CancellationToken cancellationToken);

    void Abort();
}

```

A continuación, puede agregar su implementación de escucha de comunicación volviendo en un reemplazo de método de clase basada en el servicio.

Para los servicios de estado:

```csharp
class MyStatelessService : StatelessService
{
    protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
    {
        ...
    }
    ...
}
```

Para los servicios con estado:

```csharp
class MyStatefulService : StatefulService
{
    protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
    {
        ...
    }
    ...
}
```

En ambos casos, se devuelven una colección de agentes de escucha. Esto permite su servicio escuchar en varios extremos, posiblemente mediante diferentes protocolos, varios procesos de escucha. Por ejemplo, puede tener una escucha HTTP y una escucha WebSocket independiente. Cada escucha Obtiene un nombre y la colección resultante de *nombre: dirección* pares se representa como un objeto JSON cuando un cliente solicita direcciones de escucha para una partición o una instancia de servicio.

En un servicio de estado, el reemplazo devuelve una colección de ServiceInstanceListeners. Un ServiceInstanceListener contiene una función para crear un ICommunicationListener y le da un nombre. Para los servicios con estado, el reemplazo devuelve una colección de ServiceReplicaListeners. Esto es ligeramente diferente desde su estado equivalente, como un ServiceReplicaListener tiene una opción para abrir un ICommunicationListener en réplicas secundarias. No solo puede usar varios agentes de escucha de comunicación en un servicio, pero también puede especificar qué escucha acepta solicitudes en réplicas secundarias y cuáles escuchan sólo réplicas principales.

Por ejemplo, puede tener un ServiceRemotingListener que realiza llamadas RPC sólo en réplicas principales y un agente de escucha personalizado y segunda que toma lectura convocatorias en réplicas secundarias sobre HTTP:

```csharp
protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
{
    return new[]
    {
        new ServiceReplicaListener(context =>
            new MyCustomHttpListener(context),
            "HTTPReadonlyEndpoint",
            true),

        new ServiceReplicaListener(context =>
            this.CreateServiceRemotingListener(context),
            "rpcPrimaryEndpoint",
            false)
    };
}
```

> [AZURE.NOTE] Al crear varios agentes de escucha de un servicio, cada escucha **debe** se le dé un nombre único.

Por último, se describen los extremos necesarios para el servicio en el [servicio de manifiestos](service-fabric-application-model.md) en la sección en los extremos.

```xml
<Resources>
    <Endpoints>
      <Endpoint Name="WebServiceEndpoint" Protocol="http" Port="80" />
      <Endpoint Name="OtherServiceEndpoint" Protocol="tcp" Port="8505" />
    <Endpoints>
</Resources>

```

Escucha de comunicación puede tener acceso a los recursos de extremo asignados a él desde el `CodePackageActivationContext` en la `ServiceContext`. La escucha, a continuación, puede iniciar la escucha para las solicitudes de cuando se abre.

```csharp
var codePackageActivationContext = serviceContext.CodePackageActivationContext;
var port = codePackageActivationContext.GetEndpoint("ServiceEndpoint").Port;

```

> [AZURE.NOTE] Recursos de extremo son comunes para el paquete de todo el servicio y se asignan por servicio tela cuando se activa el paquete de servicio. Múltiples réplicas de servicio hospedadas en el mismo ServiceHost pueden compartir el mismo puerto. Esto significa que el oyente comunicación debe admitir el uso compartido de puertos. La manera recomendada de hacerlo es utilizar la partición identificador y réplicas de instancias cuando genera la dirección de escucha el oyente de comunicaciones.

### <a name="service-address-registration"></a>Registro de dirección de servicio

Un servicio de sistema denominado el *Servicio de nombres* se ejecuta en clústeres tela de servicio. El servicio de nombres es un registrador de servicios y sus direcciones de cada instancia o réplica del servicio de escucha. Cuando el `OpenAsync` método de una `ICommunicationListener` completa, su retorno valor obtiene registrado en el servicio de nombres. Este valor devuelto que se publica en el servicio de nombres es una cadena cuyo valor puede ser cualquier cosa en absoluto. Este valor de cadena es lo que verán los clientes cuando solicite una dirección para el servicio del servicio de nombres.

```csharp
public Task<string> OpenAsync(CancellationToken cancellationToken)
{
    EndpointResourceDescription serviceEndpoint = serviceContext.CodePackageActivationContext.GetEndpoint("ServiceEndpoint");
    int port = serviceEndpoint.Port;

    this.listeningAddress = string.Format(
                CultureInfo.InvariantCulture,
                "http://+:{0}/",
                port);
                        
    this.publishAddress = this.listeningAddress.Replace("+", FabricRuntime.GetNodeContext().IPAddressOrFQDN);
            
    this.webApp = WebApp.Start(this.listeningAddress, appBuilder => this.startup.Invoke(appBuilder));
    
    // the string returned here will be published in the Naming Service.
    return Task.FromResult(this.publishAddress);
}
```

Servicio tela proporciona API que permite a los clientes y otros servicios pedir esta dirección por nombre de servicio. Esto es importante porque la dirección del servicio no es estática. Servicios se mueven del clúster para fines de equilibrio y la disponibilidad de recursos. Este es el mecanismo que permite a los clientes resolver la dirección de escucha de un servicio.

> [AZURE.NOTE] Para una descripción completa de cómo escribir una `ICommunicationListener`, vea [Servicios de API del servicio Web de tela con OWIN automática de hospedaje](service-fabric-reliable-services-communication-webapi.md)

## <a name="communicating-with-a-service"></a>Comunicarse con un servicio
La API de servicios fiable proporciona las siguientes bibliotecas para escribir los clientes que se comuniquen con los servicios.

### <a name="service-endpoint-resolution"></a>Resolución del extremo de servicio
Es el primer paso para la comunicación con un servicio resolver una dirección de extremo de la partición o instancia del servicio que desea hablar con. La `ServicePartitionResolver` clase de utilidad es un simple básica que ayude a los clientes a determinar el extremo de un servicio en tiempo de ejecución. En la terminología de tela de servicio, el proceso de determinar el extremo de un servicio se conoce como la *resolución del extremo de servicio*.

Para conectarse a servicios en un clúster `ServicePartitionResolver` pueden crearse con la configuración predeterminada. Esto es el uso recomendado para la mayoría de las situaciones:

```csharp
ServicePartitionResolver resolver = ServicePartitionResolver.GetDefault();
```

Para conectarse a servicios en un clúster diferente, un `ServicePartitionResolver` pueden crearse con un conjunto de puntos finales de puerta de enlace de clúster. Tenga en cuenta que los extremos de puerta de enlace extremos simplemente diferentes para conectar con el mismo clúster. Por ejemplo:

```csharp
ServicePartitionResolver resolver = new  ServicePartitionResolver("mycluster.cloudapp.azure.com:19000", "mycluster.cloudapp.azure.com:19001");
```

Como alternativa, `ServicePartitionResolver` puede especificarse una función para crear un `FabricClient` usar internamente: 
 
```csharp
public delegate FabricClient CreateFabricClientDelegate();
```

`FabricClient`es el objeto que se utiliza para comunicarse con el clúster de tela de servicio para diversas operaciones de administración en el clúster. Esto es útil cuando desea tener más control sobre cómo `ServicePartitionResolver` interactúa con el clúster. `FabricClient`realiza el almacenamiento en caché internamente y resulta caro crear, por lo que es importante volver a `FabricClient` instancias tanto como sea posibles. 

```csharp
ServicePartitionResolver resolver = new  ServicePartitionResolver(() => CreateMyFabricClient());
```

A continuación, se utiliza un método de resolución para recuperar la dirección de un servicio o una partición de servicio para los servicios con particiones.

```csharp
ServicePartitionResolver resolver = ServicePartitionResolver.GetDefault();

ResolvedServicePartition partition =
    await resolver.ResolveAsync(new Uri("fabric:/MyApp/MyService"), new ServicePartitionKey(), cancellationToken);
```

Una dirección de servicio se puede resolver fácilmente con un `ServicePartitionResolver`, pero más trabajo se necesita para asegurarse de que se puede utilizar la dirección resuelta correctamente. El cliente tendrá que detectar si el intento de conexión, error debido a un error transitorio y puede volver a intentar (por ejemplo, servicio movido o no está disponible temporalmente), o un error permanente (por ejemplo, se ha eliminado el servicio o el recurso solicitado ya no existe). Instancias de servicio o réplicas pueden moverse de nodo a nodo en cualquier momento por varios motivos. La dirección del servicio resuelto a través de `ServicePartitionResolver` puede estar obsoleto en el momento en que el código de cliente intenta conectar. En ese caso nuevamente el cliente tendrá que volver a resolver la dirección. Proporcionar la anterior `ResolvedServicePartition` indica que se necesita la resolución para intentarlo de nuevo en lugar de simplemente recuperar una dirección en caché.

Normalmente, el código de cliente no necesita trabajar con el `ServicePartitionResolver` directamente. Se crea y se pasa a fábricas de cliente de comunicaciones de la API de servicios confiable. Las fábricas de utilizan a la resolución internamente para generar un objeto de cliente que se pueden usar para comunicarse con los servicios.

### <a name="communication-clients-and-factories"></a>Fábricas y clientes de comunicación

La biblioteca de fábrica comunicación implementa un patrón de intentos de control de errores típico que facilita reintento conexiones a los extremos de servicio resuelto. La biblioteca de fábrica proporciona el mecanismo de reintento mientras proporciona los controladores de error.

`ICommunicationClientFactory`define la interfaz de base implementada por un generador de cliente de comunicación que genera a los clientes que pueden hablar con un servicio de tela de servicio. La implementación de la CommunicationClientFactory depende de la pila de comunicación utilizada por el servicio de tela de servicio en la que el cliente desea comunicarse. La API de servicios confiable proporciona un `CommunicationClientFactoryBase<TCommunicationClient>`. Proporciona una implementación base de la `ICommunicationClientFactory` interfaz y lleva a cabo tareas comunes a todas las pilas de comunicaciones. (Estas tareas incluyen el uso de un `ServicePartitionResolver` para determinar el extremo de servicio). Clientes suele implementan la clase CommunicationClientFactoryBase para controlar la lógica específica de la pila de comunicaciones.

El cliente de comunicación simplemente recibe una dirección y usa para conectarse a un servicio. El cliente puede utilizar cualquier protocolo que desea.

```csharp
class MyCommunicationClient : ICommunicationClient
{
    public ResolvedServiceEndpoint Endpoint { get; set; }

    public string ListenerName { get; set; }

    public ResolvedServicePartition ResolvedServicePartition { get; set; }
}
```

El generador de cliente es responsable, principalmente, para la creación de clientes de comunicaciones. Para los clientes que no mantienen una conexión persistente, como un cliente HTTP, el generador de solo necesita crear y devolver al cliente. Otros protocolos que mantienen una conexión persistente, como algunos protocolos binarios, también se deben validar la fábrica para determinar si no es necesario volver a crear la conexión.  

```csharp
public class MyCommunicationClientFactory : CommunicationClientFactoryBase<MyCommunicationClient>
{
    protected override void AbortClient(MyCommunicationClient client)
    {
    }

    protected override Task<MyCommunicationClient> CreateClientAsync(string endpoint, CancellationToken cancellationToken)
    {
    }

    protected override bool ValidateClient(MyCommunicationClient clientChannel)
    {
    }

    protected override bool ValidateClient(string endpoint, MyCommunicationClient client)
    {
    }
}
```

Por último, un controlador de excepción es responsable de determinar qué acción debe realizar cuando se produce una excepción. Excepciones se clasifican en **puede reintentar** y **no puede reintentar**. 

 - **No puede reintentar** simplemente obtengan volver a iniciar excepciones a la persona que llama. 
 - Excepciones de **Retriable** se clasifican en **transitorias** y **no transitorios**.
  - Excepciones **transitorias** son aquellos que simplemente puede volver a intentar sin volver a resolver la dirección de extremo de servicio. Estos incluirá transitorias problemas de red o respuestas de error de servicio distintos de los que indican que la dirección de extremo de servicio no existe. 
  - **No transitorios** excepciones son aquellos que requieren la dirección del extremo de servicio necesario volver a resolver. Entre estos incluyen las excepciones que indican que no se pudo conectar el extremo de servicio que indica que el servicio ha movido a un nodo diferente. 

La `TryHandleException` decide sobre una excepción determinada. Si se **no sabe** qué decisiones para hacer sobre una excepción, debe devolver **false**. Si se **sabe** qué decisión para realizar, debe establecer el resultado en consecuencia y devuelve **true**.
 
```csharp
class MyExceptionHandler : IExceptionHandler
{
    public bool TryHandleException(ExceptionInformation exceptionInformation, OperationRetrySettings retrySettings, out ExceptionHandlingResult result)
    {
        // if exceptionInformation.Exception is known and is transient (can be retried without re-resolving)
        result = new ExceptionHandlingRetryResult(exceptionInformation.Exception, true, retrySettings, retrySettings.DefaultMaxRetryCount);
        return true;


        // if exceptionInformation.Exception is known and is not transient (indicates a new service endpoint address must be resolved)
        result = new ExceptionHandlingRetryResult(exceptionInformation.Exception, false, retrySettings, retrySettings.DefaultMaxRetryCount);
        return true;

        // if exceptionInformation.Exception is unknown (let the next IExceptionHandler attempt to handle it)
        result = null;
        return false;
    }
}
```
### <a name="putting-it-all-together"></a>Todo en conjunto
Con un `ICommunicationClient`, `ICommunicationClientFactory`, y `IExceptionHandler` creado en torno a un protocolo de comunicaciones, un `ServicePartitionClient` es todo lo contiene y proporciona el bucle de resolución de dirección de partición de servicio y tratamiento de errores alrededor de estos componentes.

```csharp
private MyCommunicationClientFactory myCommunicationClientFactory;
private Uri myServiceUri;

var myServicePartitionClient = new ServicePartitionClient<MyCommunicationClient>(
    this.myCommunicationClientFactory,
    this.myServiceUri,
    myPartitionKey);

var result = await myServicePartitionClient.InvokeWithRetryAsync(async (client) =>
   {
      // Communicate with the service using the client.
   },
   CancellationToken.None);

```

## <a name="next-steps"></a>Pasos siguientes
 - Vea un ejemplo de comunicación de HTTP entre los servicios de un [proyecto de ejemplo en GitHUb](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started/tree/master/Services/WordCount).

 - [Llamadas a procedimiento remoto con acceso remoto servicios confiables](service-fabric-reliable-services-communication-remoting.md)

 - [Web API que usa OWIN en servicios confiables](service-fabric-reliable-services-communication-webapi.md)

 - [Comunicación de WCF mediante servicios confiable](service-fabric-reliable-services-communication-wcf.md)
