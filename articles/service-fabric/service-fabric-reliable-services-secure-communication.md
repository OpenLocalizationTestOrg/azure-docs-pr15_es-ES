<properties
   pageTitle="Ayuda de comunicación segura para servicios en tela de servicio | Microsoft Azure"
   description="Información general sobre cómo ayudar a proteger la comunicación de servicios confiables se ejecutan en un clúster de Azure servicio tela."
   services="service-fabric"
   documentationCenter=".net"
   authors="suchiagicha"
   manager="timlt"
   editor="vturecek"/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="required"
   ms.date="07/06/2016"
   ms.author="suchiagicha"/>

# <a name="help-secure-communication-for-services-in-azure-service-fabric"></a>Comunicación segura de ayuda para los servicios en tela de servicio de Azure

Seguridad es uno de los aspectos más importantes de comunicación. El marco de la aplicación de servicios confiables proporciona unos pilas de comunicación prediseñadas y herramientas que puede utilizar para mejorar la seguridad. En este artículo se habla sobre cómo mejorar la seguridad cuando usa remota de servicio y la pila de comunicación de Windows Communication Foundation (WCF).

## <a name="help-secure-a-service-when-youre-using-service-remoting"></a>Ayudar a proteger un servicio cuando usa remota de servicio

Se va a usar un [ejemplo](service-fabric-reliable-services-communication-remoting.md) existente que explica cómo configurar la interacción remota para servicios fiables. Para ayudar a proteger un servicio cuando usa remota de servicio, siga estos pasos:

1. Crear una interfaz `IHelloWorldStateful`, que define los métodos que estarán disponibles para una llamada a procedimiento remoto en el servicio. El servicio va a utilizar `FabricTransportServiceRemotingListener`, que se declara en el `Microsoft.ServiceFabric.Services.Remoting.FabricTransport.Runtime` espacio de nombres. Esta es una `ICommunicationListener` implementación que ofrece capacidades de acceso remoto.

    ```csharp
    public interface IHelloWorldStateful : IService
    {
        Task<string> GetHelloWorld();
    }

    internal class HelloWorldStateful : StatefulService, IHelloWorldStateful
    {
        protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
        {
            return new[]{
                    new ServiceReplicaListener(
                        (context) => new FabricTransportServiceRemotingListener(context,this))};
        }

        public Task<string> GetHelloWorld()
        {
            return Task.FromResult("Hello World!");
        }
    }
    ```

2. Agregar credenciales de seguridad y configuración de la escucha.

    Asegúrese de que el certificado que desea usar para ayudar a proteger la comunicación de servicio está instalado en todos los nodos en el clúster. Hay dos formas que puede proporcionar la configuración de escucha y las credenciales de seguridad:

    1. Proporcionarles directamente en el código de servicio:

        ```csharp
        protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
        {
            FabricTransportListenerSettings listenerSettings = new FabricTransportListenerSettings
            {
                MaxMessageSize = 10000000,
                SecurityCredentials = GetSecurityCredentials()
            };
            return new[]
            {
                new ServiceReplicaListener(
                    (context) => new FabricTransportServiceRemotingListener(context,this,listenerSettings))
            };
        }

        private static SecurityCredentials GetSecurityCredentials()
        {
            // Provide certificate details.
            var x509Credentials = new X509Credentials
            {
                FindType = X509FindType.FindByThumbprint,
                FindValue = "4FEF3950642138446CC364A396E1E881DB76B48C",
                StoreLocation = StoreLocation.LocalMachine,
                StoreName = "My",
                ProtectionLevel = ProtectionLevel.EncryptAndSign
            };
            x509Credentials.RemoteCommonNames.Add("ServiceFabric-Test-Cert");
            return x509Credentials;
        }
        ```
    2. Proporcionarles mediante un [paquete de configuración](service-fabric-application-model.md):

        Agregar un `TransportSettings` sección del archivo settings.xml.

        ```xml
        <!--Section name should always end with "TransportSettings".-->
        <!--Here we are using a prefix "HelloWorldStateful".-->
        <Section Name="HelloWorldStatefulTransportSettings">
            <Parameter Name="MaxMessageSize" Value="10000000" />
            <Parameter Name="SecurityCredentialsType" Value="X509" />
            <Parameter Name="CertificateFindType" Value="FindByThumbprint" />
            <Parameter Name="CertificateFindValue" Value="4FEF3950642138446CC364A396E1E881DB76B48C" />
            <Parameter Name="CertificateStoreLocation" Value="LocalMachine" />
            <Parameter Name="CertificateStoreName" Value="My" />
            <Parameter Name="CertificateProtectionLevel" Value="EncryptAndSign" />
            <Parameter Name="CertificateRemoteCommonNames" Value="ServiceFabric-Test-Cert" />
        </Section>
        ```

        En este caso, el `CreateServiceReplicaListeners` método tendrá este aspecto:

        ```csharp
        protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
        {
            return new[]
            {
                new ServiceReplicaListener(
                    (context) => new FabricTransportServiceRemotingListener(
                        context,this,FabricTransportListenerSettings.LoadFrom("HelloWorldStateful")))
            };
        }
        ```

         Si agrega un `TransportSettings` sección del archivo settings.xml sin ningún prefijo `FabricTransportListenerSettings` cargará la configuración de esta sección de forma predeterminada.

         ```xml
         <!--"TransportSettings" section without any prefix.-->
         <Section Name="TransportSettings">
             ...
         </Section>
         ```
         En este caso, el `CreateServiceReplicaListeners` método tendrá este aspecto:

         ```csharp
         protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
         {
             return new[]
             {
                 return new[]{
                         new ServiceReplicaListener(
                             (context) => new FabricTransportServiceRemotingListener(context,this))};
             };
         }
         ```

3. Al llamar a métodos en un servicio protegido mediante el uso de la pila remota, en lugar de usar el `Microsoft.ServiceFabric.Services.Remoting.Client.ServiceProxy` clase para crear un proxy de servicio, use `Microsoft.ServiceFabric.Services.Remoting.Client.ServiceProxyFactory`. Pasar `FabricTransportSettings`, que contiene `SecurityCredentials`.

    ```csharp

    var x509Credentials = new X509Credentials
    {
        FindType = X509FindType.FindByThumbprint,
        FindValue = "4FEF3950642138446CC364A396E1E881DB76B48C",
        StoreLocation = StoreLocation.LocalMachine,
        StoreName = "My",
        ProtectionLevel = ProtectionLevel.EncryptAndSign
    };
    x509Credentials.RemoteCommonNames.Add("ServiceFabric-Test-Cert");

    FabricTransportSettings transportSettings = new FabricTransportSettings
    {
        SecurityCredentials = x509Credentials,
    };

    ServiceProxyFactory serviceProxyFactory = new ServiceProxyFactory(
        (c) => new FabricTransportServiceRemotingClientFactory(transportSettings));

    IHelloWorldStateful client = serviceProxyFactory.CreateServiceProxy<IHelloWorldStateful>(
        new Uri("fabric:/MyApplication/MyHelloWorldService"));

    string message = await client.GetHelloWorld();

    ```

    Si está ejecutando el código de cliente como parte de un servicio, puede cargar `FabricTransportSettings` del archivo settings.xml. Crear una sección de TransportSettings es similar al código de servicio, tal como se muestra en una versión anterior. Realice los cambios siguientes en el código de cliente:

    ```csharp

    ServiceProxyFactory serviceProxyFactory = new ServiceProxyFactory(
        (c) => new FabricTransportServiceRemotingClientFactory(FabricTransportSettings.LoadFrom("TransportSettingsPrefix")));

    IHelloWorldStateful client = serviceProxyFactory.CreateServiceProxy<IHelloWorldStateful>(
        new Uri("fabric:/MyApplication/MyHelloWorldService"));

    string message = await client.GetHelloWorld();

    ```

    Si el cliente no se ejecuta como parte de un servicio, puede crear un archivo client_name.settings.xml en la misma ubicación de la client_name.exe. A continuación, cree una sección de TransportSettings en ese archivo.

    Similar al servicio, si agrega un `TransportSettings` sección sin ningún prefijo en cliente settings.xml/client_name.settings.xml, `FabricTransportSettings` cargará la configuración de esta sección de forma predeterminada.

    En ese caso, aún se simplifica el código anterior:  

    ```csharp

    IHelloWorldStateful client = ServiceProxy.Create<IHelloWorldStateful>(
                 new Uri("fabric:/MyApplication/MyHelloWorldService"));

    string message = await client.GetHelloWorld();

    ```

## <a name="help-secure-a-service-when-youre-using-a-wcf-based-communication-stack"></a>Ayudar a proteger un servicio cuando usa una pila de comunicación basada en WCF

Se va a usar un [ejemplo](service-fabric-reliable-services-communication-wcf.md) existente que explica cómo configurar una pila de comunicación basada en WCF para servicios confiables. Para ayudar a proteger un servicio cuando usa una pila de comunicación basada en WCF, siga estos pasos:

1. Para el servicio necesita para ayudar a proteger la escucha de comunicación de WCF (`WcfCommunicationListener`) que cree. Para ello, modifique la `CreateServiceReplicaListeners` método.

    ```csharp
    protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
    {
        return new[]
        {
            new ServiceReplicaListener(
                this.CreateWcfCommunicationListener)
        };
    }

    private WcfCommunicationListener<ICalculator> CreateWcfCommunicationListener(StatefulServiceContext context)
    {
       var wcfCommunicationListener = new WcfCommunicationListener<ICalculator>(
            serviceContext:context,
            wcfServiceObject:this,
            // For this example, we will be using NetTcpBinding.
            listenerBinding: GetNetTcpBinding(),
            endpointResourceName:"WcfServiceEndpoint");

        // Add certificate details in the ServiceHost credentials.
        wcfCommunicationListener.ServiceHost.Credentials.ServiceCertificate.SetCertificate(
            StoreLocation.LocalMachine,
            StoreName.My,
            X509FindType.FindByThumbprint,
            "9DC906B169DC4FAFFD1697AC781E806790749D2F");
        return wcfCommunicationListener;
    }

    private static NetTcpBinding GetNetTcpBinding()
    {
        NetTcpBinding b = new NetTcpBinding(SecurityMode.TransportWithMessageCredential);
        b.Security.Message.ClientCredentialType = MessageCredentialType.Certificate;
        return b;
    }
    ```

2. En el cliente, el `WcfCommunicationClient` permanece sin modificar la clase que creó en el [ejemplo](service-fabric-reliable-services-communication-wcf.md) anterior. Pero debe anular la `CreateClientAsync` método `WcfCommunicationClientFactory`:

    ```csharp
    public class SecureWcfCommunicationClientFactory<TServiceContract> : WcfCommunicationClientFactory<TServiceContract> where TServiceContract : class
    {
        private readonly Binding clientBinding;
        private readonly object callbackObject;
        public SecureWcfCommunicationClientFactory(
            Binding clientBinding,
            IEnumerable<IExceptionHandler> exceptionHandlers = null,
            IServicePartitionResolver servicePartitionResolver = null,
            string traceId = null,
            object callback = null)
            : base(clientBinding, exceptionHandlers, servicePartitionResolver,traceId,callback)
        {
            this.clientBinding = clientBinding;
            this.callbackObject = callback;
        }

        protected override Task<WcfCommunicationClient<TServiceContract>> CreateClientAsync(string endpoint, CancellationToken cancellationToken)
        {
            var endpointAddress = new EndpointAddress(new Uri(endpoint));
            ChannelFactory<TServiceContract> channelFactory;
            if (this.callbackObject != null)
            {
                channelFactory = new DuplexChannelFactory<TServiceContract>(
                this.callbackObject,
                this.clientBinding,
                endpointAddress);
            }
            else
            {
                channelFactory = new ChannelFactory<TServiceContract>(this.clientBinding, endpointAddress);
            }
            // Add certificate details to the ChannelFactory credentials.
            // These credentials will be used by the clients created by
            // SecureWcfCommunicationClientFactory.  
            channelFactory.Credentials.ClientCertificate.SetCertificate(
                StoreLocation.LocalMachine,
                StoreName.My,
                X509FindType.FindByThumbprint,
                "9DC906B169DC4FAFFD1697AC781E806790749D2F");
            var channel = channelFactory.CreateChannel();
            var clientChannel = ((IClientChannel)channel);
            clientChannel.OperationTimeout = this.clientBinding.ReceiveTimeout;
            return Task.FromResult(this.CreateWcfCommunicationClient(channel));
        }
    }
    ```

    Usar `SecureWcfCommunicationClientFactory` para crear un cliente de comunicación de WCF (`WcfCommunicationClient`). Utilice al cliente para invocar métodos de servicio.

    ```csharp
    IServicePartitionResolver partitionResolver = ServicePartitionResolver.GetDefault();

    var wcfClientFactory = new SecureWcfCommunicationClientFactory<ICalculator>(clientBinding: GetNetTcpBinding(), servicePartitionResolver: partitionResolver);

    var calculatorServiceCommunicationClient =  new WcfCommunicationClient(
        wcfClientFactory,
        ServiceUri,
        ServicePartitionKey.Singleton);

    var result = calculatorServiceCommunicationClient.InvokeWithRetryAsync(
        client => client.Channel.Add(2, 3)).Result;
    ```

## <a name="next-steps"></a>Pasos siguientes

* [Web API con OWIN en servicios confiables](service-fabric-reliable-services-communication-webapi.md)
