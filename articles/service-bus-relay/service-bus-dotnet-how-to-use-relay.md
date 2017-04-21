<properties
    pageTitle="Cómo utilizar la retransmisión de Bus de servicio con .NET | Microsoft Azure"
    description="Obtenga información sobre cómo usar el servicio de retransmisión de Bus de servicio de Azure para conectar dos aplicaciones que se encuentran en diferentes ubicaciones."
    services="service-bus"
    documentationCenter=".net"
    authors="sethmanheim"
    manager="timlt"
    editor=""/>

<tags
    ms.service="service-bus"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="09/16/2016"
    ms.author="sethm"/>


# <a name="how-to-use-the-azure-service-bus-relay-service"></a>Cómo usar el servicio de retransmisión de Bus de servicio de Azure

En este artículo se describe cómo usar el servicio de retransmisión de Bus de servicio. Los ejemplos se escriben en C# y usan la API de Windows Communication Foundation (WCF) con extensiones contenidas en el conjunto de Bus de servicio. Para obtener más información acerca de la retransmisión de Bus de servicio, vea la información general sobre [mensajería Bus de servicio de retransmisión](service-bus-relay-overview.md) .

[AZURE.INCLUDE [create-account-note](../../includes/create-account-note.md)]

## <a name="what-is-the-service-bus-relay"></a>¿Qué es la retransmisión de Bus de servicio?

El servicio de [Bus de servicio de *retransmisión* ](service-bus-relay-overview.md) le permite crear aplicaciones híbridas que se ejecutan en un centro de datos de Azure y su propio entorno local de la empresa. La retransmisión de Bus de servicio facilita este habilitando exponer de forma segura servicios Windows Communication Foundation (WCF) que se encuentran dentro de una red corporativa enterprise en la nube pública, sin tener que abrir una conexión del firewall ni requerir cambios intrusivos en una infraestructura de red corporativa.

![Conceptos de retransmisión](./media/service-bus-dotnet-how-to-use-relay/sb-relay-01.png)

La retransmisión de Bus de servicio le permite alojar los servicios WCF dentro de su entorno existente de la empresa. A continuación, puede delegar la escucha entrante sesiones y solicitudes de estos servicios WCF al servicio de Bus de servicio que se ejecuta en Azure. Esto le permite exponer estos servicios para código de aplicación que se ejecuta en Azure, o para los trabajadores móviles o entornos de extranet asociado. Bus de servicio permite segura controlar quién puede tener acceso a estos servicios en un nivel específico. Proporciona una manera eficaz y segura para exponer la funcionalidad de la aplicación y los datos de las soluciones existentes de la empresa y aprovechar las ventajas de la nube.

En este artículo se muestra cómo usar la retransmisión de Bus de servicio para crear un servicio web WCF, expuesto mediante un enlace de canal TCP que implementa una conversación segura entre dos partes.

## <a name="create-a-service-namespace"></a>Crear un espacio de nombres de servicio

Para empezar a utilizar la retransmisión de Bus de servicio en Azure, primero debe crear un espacio de nombres. Un espacio de nombres proporciona un contenedor de ámbito para el direccionamiento de recursos de Bus de servicio de la aplicación.

Para crear un espacio de nombres de servicio:

[AZURE.INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

## <a name="get-the-service-bus-nuget-package"></a>Obtener el paquete NuGet Bus de servicio

El [paquete de servicio Bus NuGet](https://www.nuget.org/packages/WindowsAzure.ServiceBus) es la manera más sencilla de obtener la API de Bus de servicio y configurar la aplicación con todas las dependencias de Bus de servicio. Para instalar el paquete de NuGet en su proyecto, haga lo siguiente:

1.  En el Explorador de soluciones, haga clic en **referencias**, haga clic en **Administrar paquetes de NuGet**.
2.  Buscar "Bus de servicio" y seleccione el elemento de **Bus de servicio de Microsoft Azure** . Haga clic en **instalar** para completar la instalación, cierre el cuadro de diálogo siguiente:

    ![](./media/service-bus-dotnet-how-to-use-relay/getting-started-multi-tier-13.png)

## <a name="use-service-bus-to-expose-and-consume-a-soap-web-service-with-tcp"></a>Usar Bus de servicio para exponer y consumir un servicio web SOAP con TCP

Para exponer un servicio web de WCF SOAP existente para su consumo externo, debe realizar cambios en los enlaces de servicio y direcciones. Esto puede requerir cambios en el archivo de configuración o podría requerir cambios en el código, dependiendo de cómo haya configurado y configurar los servicios WCF. Tenga en cuenta que WCF le permite tener varios extremos de red sobre el mismo servicio, por lo que puede conservar los extremos internos existentes al agregar extremos de Bus de servicio para el acceso externo al mismo tiempo.

En esta tarea, se crear un servicio WCF simple y agregarle una escucha de Bus de servicio. Este ejercicio supone familiarizado con Visual Studio y por tanto no recorra todos los detalles de la creación de un proyecto. En su lugar, se centra en el código.

Antes de iniciar estos pasos, complete el procedimiento siguiente para configurar su entorno:

1.  En Visual Studio, cree una aplicación de consola que contiene dos proyectos, "Cliente" y "Servicio" dentro de la solución.
2.  Agregue el paquete de Microsoft Azure servicio Bus NuGet para ambos proyectos. Este paquete agrega todas las referencias de ensamblado necesarias para sus proyectos.

### <a name="how-to-create-the-service"></a>Cómo crear el servicio

Primero, cree el propio servicio. Cualquier servicio WCF consta de al menos tres partes distintas:

-   Definición de un contrato que describe qué mensajes están intercambiar y qué son las operaciones invocar.
-   Implementación de dicho contrato.
-   Host que hospeda el servicio WCF y expone varios extremos.

Los ejemplos de código de esta sección dirección de cada uno de estos componentes.

El contrato define una única operación, `AddNumbers`, que agrega dos números y devuelve el resultado. La `IProblemSolverChannel` interfaz permite que el cliente administrar más fácilmente la duración de proxy. Crear una interfaz se considera práctica recomendada. Es una buena idea colocar esta definición del contrato en un archivo independiente, por lo que puede hacer referencia a ese archivo de proyectos de su "Cliente" y el "servicio de", pero también puede copiar el código en ambos proyectos.

```
using System.ServiceModel;

[ServiceContract(Namespace = "urn:ps")]
interface IProblemSolver
{
    [OperationContract]
    int AddNumbers(int a, int b);
}

interface IProblemSolverChannel : IProblemSolver, IClientChannel {}
```

Con el contrato en su lugar, la implementación es trivial.

```
class ProblemSolver : IProblemSolver
{
    public int AddNumbers(int a, int b)
    {
        return a + b;
    }
}
```

### <a name="configure-a-service-host-programmatically"></a>Configurar un servicio de host mediante programación

Con el contrato y la implementación en su lugar, ahora puede hospedar el servicio. Hospedaje dentro de un objeto [System.ServiceModel.ServiceHost](https://msdn.microsoft.com/library/azure/system.servicemodel.servicehost.aspx) , que se encarga de administrar instancias del servicio y hospeda los extremos que escuchan los mensajes. El código siguiente configura el servicio con un extremo local normal y un extremo de servicio Bus para ilustrar la apariencia, en paralelo, de extremos internos y externos. Reemplazar el *espacio de nombres* de cadena con el nombre de espacio de nombres y *yourKey* con la clave de SA que obtuvo en el paso anterior de la instalación.

```
ServiceHost sh = new ServiceHost(typeof(ProblemSolver));

sh.AddServiceEndpoint(
   typeof (IProblemSolver), new NetTcpBinding(),
   "net.tcp://localhost:9358/solver");

sh.AddServiceEndpoint(
   typeof(IProblemSolver), new NetTcpRelayBinding(),
   ServiceBusEnvironment.CreateServiceUri("sb", "namespace", "solver"))
    .Behaviors.Add(new TransportClientEndpointBehavior {
          TokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider("RootManageSharedAccessKey", "<yourKey>")});

sh.Open();

Console.WriteLine("Press ENTER to close");
Console.ReadLine();

sh.Close();
```

En el ejemplo, cree dos extremos que están en la misma implementación de contratos. Uno es local y uno está proyectado a través de Bus de servicio. Las diferencias claves entre ellos son los enlaces; [NetTcpBinding](https://msdn.microsoft.com/library/azure/system.servicemodel.nettcpbinding.aspx) para local y [NetTcpRelayBinding](https://msdn.microsoft.com/library/azure/microsoft.servicebus.nettcprelaybinding.aspx) del extremo de Bus de servicio y las direcciones. El extremo local tiene una dirección de la red local con un puerto distinto. El extremo de servicio Bus tiene una dirección de extremo compuesta de la cadena `sb`, el nombre de espacio de nombres y la ruta de acceso "solver." El resultado es el URI `sb://[serviceNamespace].servicebus.windows.net/solver`, identificar el extremo de servicio como un extremo de servicio Bus TCP con un nombre completo de DNS externo. Si coloca el código reemplazando los marcadores de posición en la `Main` función de la aplicación de **servicio** , tendrá un servicio funcional. Si desea que el servicio escuche exclusivamente en Bus de servicio, quite la declaración de extremo local.

### <a name="configure-a-service-host-in-the-appconfig-file"></a>Configurar un host de servicio en el archivo App.config

También puede configurar el host mediante el archivo App.config. El servicio de código de hospedaje en este caso aparece en el ejemplo siguiente.

```
ServiceHost sh = new ServiceHost(typeof(ProblemSolver));
sh.Open();
Console.WriteLine("Press ENTER to close");
Console.ReadLine();
sh.Close();
```

Mover las definiciones de extremo en el archivo App.config. El paquete de NuGet ya ha agregado un intervalo de definición en el archivo App.config, que son las extensiones de configuración necesarias para Bus de servicio. El ejemplo siguiente, que es el equivalente exacto del código anterior, debe aparecer inmediatamente debajo del elemento **system.serviceModel** . Este ejemplo de código se presupone que el espacio de nombres de proyecto C# se denomina **servicio**.
Reemplace los marcadores de posición por el espacio de nombres de servicio de Bus de servicio y la clave de SA.

```
<services>
    <service name="Service.ProblemSolver">
        <endpoint contract="Service.IProblemSolver"
                  binding="netTcpBinding"
                  address="net.tcp://localhost:9358/solver"/>
        <endpoint contract="Service.IProblemSolver"
                  binding="netTcpRelayBinding"
                  address="sb://namespace.servicebus.windows.net/solver"
                  behaviorConfiguration="sbTokenProvider"/>
    </service>
</services>
<behaviors>
    <endpointBehaviors>
        <behavior name="sbTokenProvider">
            <transportClientEndpointBehavior>
                <tokenProvider>
                    <sharedAccessSignature keyName="RootManageSharedAccessKey" key="<yourKey>" />
                </tokenProvider>
            </transportClientEndpointBehavior>
        </behavior>
    </endpointBehaviors>
</behaviors>
```

Después de realizar estos cambios, inicie el servicio igual que antes, pero con dos extremos directo: uno local y escucha uno en la nube.

### <a name="create-the-client"></a>Crear el cliente

#### <a name="configure-a-client-programmatically"></a>Configurar a un cliente mediante programación

Para utilizar el servicio, puede crear a un cliente WCF mediante un objeto [ChannelFactory](https://msdn.microsoft.com/library/system.servicemodel.channelfactory.aspx) . Bus de servicio utiliza un modelo de seguridad basada en token implementado mediante SA. La clase [TokenProvider](https://msdn.microsoft.com/library/azure/microsoft.servicebus.tokenprovider.aspx) representa un proveedor de token de seguridad con métodos de fábrica integrados que devuelven algunos proveedores de tokens conocidos. En el ejemplo siguiente se utiliza el método [CreateSharedAccessSignatureTokenProvider](https://msdn.microsoft.com/library/azure/microsoft.servicebus.tokenprovider.createsharedaccesssignaturetokenprovider.aspx) para controlar la adquisición del token de SA adecuado. El nombre y la clave son aquellos que se obtienen desde el portal como se describe en la sección anterior.

En primer lugar, referencia o copie la `IProblemSolver` código del servicio de contrato en el proyecto de cliente.

A continuación, reemplace el código de la `Main` método del cliente, vuelva a reemplazar el texto de marcador de posición con el espacio de nombres de Bus de servicio y la clave de SA.

```
var cf = new ChannelFactory<IProblemSolverChannel>(
    new NetTcpRelayBinding(),
    new EndpointAddress(ServiceBusEnvironment.CreateServiceUri("sb", "namespace", "solver")));

cf.Endpoint.Behaviors.Add(new TransportClientEndpointBehavior
            { TokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider("RootManageSharedAccessKey","<yourKey>") });

using (var ch = cf.CreateChannel())
{
    Console.WriteLine(ch.AddNumbers(4, 5));
}
```

Ahora puede crear el cliente y el servicio, ejecutarlos (el servicio se ejecutan primero) y el cliente llama al servicio e imprime **9**. Puede ejecutar el cliente y el servidor en equipos diferentes, incluso a través de redes y comunicación ¿seguirán funcionando. También puede ejecutar el código de cliente en la nube o localmente.

#### <a name="configure-a-client-in-the-appconfig-file"></a>Configurar a un cliente en el archivo App.config

El código siguiente muestra cómo configurar al cliente utilizando el archivo App.config.

```
var cf = new ChannelFactory<IProblemSolverChannel>("solver");
using (var ch = cf.CreateChannel())
{
    Console.WriteLine(ch.AddNumbers(4, 5));
}
```

Mover las definiciones de extremo en el archivo App.config. El ejemplo siguiente, que es el mismo que el código enumerado anteriormente, debería aparecer directamente debajo del elemento de **system.serviceModel** . Aquí, como antes, debe reemplazar los marcadores de posición con la clave de SA y el espacio de nombres de Bus de servicio.

```
<client>
    <endpoint name="solver" contract="Service.IProblemSolver"
              binding="netTcpRelayBinding"
              address="sb://namespace.servicebus.windows.net/solver"
              behaviorConfiguration="sbTokenProvider"/>
</client>
<behaviors>
    <endpointBehaviors>
        <behavior name="sbTokenProvider">
            <transportClientEndpointBehavior>
                <tokenProvider>
                    <sharedAccessSignature keyName="RootManageSharedAccessKey" key="<yourKey>" />
                </tokenProvider>
            </transportClientEndpointBehavior>
        </behavior>
    </endpointBehaviors>
</behaviors>
```

## <a name="next-steps"></a>Pasos siguientes

Ahora que ha aprendido los conceptos básicos del servicio de retransmisión de Bus de servicio, siga estos vínculos para obtener más información.

- [Introducción a mensajería de retransmisión de Bus de servicio](service-bus-relay-overview.md)
- [Información general de arquitectura de Bus de servicio Azure](../service-bus-messaging/service-bus-fundamentals-hybrid-solutions.md)
- Descargar ejemplos de Bus de servicio de [Azure ejemplos][] o ver una [Descripción general de las muestras de Bus de servicio][].

  [Shared Access Signature Authentication with Service Bus]: ../service-bus-messaging/service-bus-shared-access-signature-authentication.md
  [Ejemplos de Azure]: https://code.msdn.microsoft.com/site/search?query=service%20bus&f%5B0%5D.Value=service%20bus&f%5B0%5D.Type=SearchText&ac=2
  [información general de las muestras de Bus de servicio]: ../service-bus-messaging/service-bus-samples.md