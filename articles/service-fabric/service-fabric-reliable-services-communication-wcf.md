<properties
   pageTitle="Pila de comunicaciones de servicios WCF fiable | Microsoft Azure"
   description="La pila de comunicación de WCF integrada en tela de servicio proporciona comunicación de WCF del servicio de cliente para servicios confiables."
   services="service-fabric"
   documentationCenter=".net"
   authors="BharatNarasimman"
   manager="timlt"
   editor="vturecek"/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="required"
   ms.date="07/26/2016"
   ms.author="bharatn"/>

# <a name="wcf-based-communication-stack-for-reliable-services"></a>Pila de comunicación basada en WCF para servicios confiables
El marco de servicios confiables permite a los autores de servicio elegir la pila de comunicación que desea usar para su servicio. Puede conectar la pila de comunicaciones de su elección mediante la **ICommunicationListener** obtenidos mediante los métodos [CreateServiceReplicaListeners o CreateServiceInstanceListeners](service-fabric-reliable-services-communication.md) . El marco de trabajo proporciona una implementación de la pila de comunicación basada en Windows Communication Foundation (WCF) para los autores de servicio que desea usar comunicación basada en WCF.

## <a name="wcf-communication-listener"></a>Escucha de comunicación de WCF
La clase **Microsoft.ServiceFabric.Services.Communication.Wcf.Runtime.WcfCommunicationListener** proporciona la implementación de WCF específica de **ICommunicationListener** .

Menos que tenemos un contrato de servicio de tipo`ICalculator`

```csharp
[ServiceContract]
public interface ICalculator
{
    [OperationContract]
    Task<int> Add(int value1, int value2);
}
```

Podemos crear una escucha de comunicación de WCF en el servicio de la siguiente manera.

```csharp

protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
{
    return new[] { new ServiceReplicaListener((context) =>
        new WcfCommunicationListener<ICalculator>(
            wcfServiceObject:this,
            serviceContext:context,
            //
            // The name of the endpoint configured in the ServiceManifest under the Endpoints section
            // that identifies the endpoint that the WCF ServiceHost should listen on.
            //
            endpointResourceName: "WcfServiceEndpoint",

            //
            // Populate the binding information that you want the service to use.
            //
            listenerBinding: WcfUtility.CreateTcpListenerBinding()
        )
    )};
}

```

## <a name="writing-clients-for-the-wcf-communication-stack"></a>Escribir clientes de la pila de comunicación de WCF
Para escribir los clientes para comunicarse con los servicios con WCF, el marco de trabajo proporciona **WcfClientCommunicationFactory**, que es la implementación de WCF específica de [ClientCommunicationFactoryBase](service-fabric-reliable-services-communication.md).

```csharp

public WcfCommunicationClientFactory(
    Binding clientBinding = null,
    IEnumerable<IExceptionHandler> exceptionHandlers = null,
    IServicePartitionResolver servicePartitionResolver = null,
    string traceId = null,
    object callback = null);
```

El canal de comunicación de WCF puede tener acceso desde el **WcfCommunicationClient** creado por la **WcfCommunicationClientFactory**.

```csharp

public class WcfCommunicationClient : ServicePartitionClient<WcfCommunicationClient<ICalculator>>
   {
       public WcfCommunicationClient(ICommunicationClientFactory<WcfCommunicationClient<ICalculator>> communicationClientFactory, Uri serviceUri, ServicePartitionKey partitionKey = null, TargetReplicaSelector targetReplicaSelector = TargetReplicaSelector.Default, string listenerName = null, OperationRetrySettings retrySettings = null)
           : base(communicationClientFactory, serviceUri, partitionKey, targetReplicaSelector, listenerName, retrySettings)
       {
       }
   }

```

Código de cliente puede utilizar el **WcfCommunicationClientFactory** junto con el **WcfCommunicationClient** que implementa **ServicePartitionClient** para determinar el extremo de servicio y comunicarse con el servicio.

```csharp
// Create binding
Binding binding = WcfUtility.CreateTcpClientBinding();
// Create a partition resolver
IServicePartitionResolver partitionResolver = ServicePartitionResolver.GetDefault();
// create a  WcfCommunicationClientFactory object.
var wcfClientFactory = new WcfCommunicationClientFactory<ICalculator>
    (clientBinding: binding, servicePartitionResolver: partitionResolver);

//
// Create a client for communicating with the ICalculator service that has been created with the
// Singleton partition scheme.
//
var calculatorServiceCommunicationClient =  new WcfCommunicationClient(
                wcfClientFactory,
                ServiceUri,
                ServicePartitionKey.Singleton);

//
// Call the service to perform the operation.
//
var result = calculatorServiceCommunicationClient.InvokeWithRetryAsync(
                client => client.Channel.Add(2, 3)).Result;

```
>[AZURE.NOTE] ServicePartitionResolver, se supone que el cliente se ejecuta en el mismo clúster como el servicio. Si es que no es el caso, cree un objeto de ServicePartitionResolver y pase en los extremos de la conexión de clúster.

## <a name="next-steps"></a>Pasos siguientes
* [Llamada a procedimiento remoto con acceso remoto servicios confiables](service-fabric-reliable-services-communication-remoting.md)

* [Web API con OWIN en servicios confiables](service-fabric-reliable-services-communication-webapi.md)

* [Proteger la comunicación de servicios confiables](service-fabric-reliable-services-secure-communication.md)
