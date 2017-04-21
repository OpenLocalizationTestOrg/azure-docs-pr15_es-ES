<properties
   pageTitle="Servicio de comunicación con la API de Web ASP.NET | Microsoft Azure"
   description="Aprenda a implementar la comunicación de servicio paso a paso mediante la API de Web de ASP.NET con OWIN automática de hospedaje en la API de servicios confiable."
   services="service-fabric"
   documentationCenter=".net"
   authors="vturecek"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="required"
   ms.date="10/19/2016"
   ms.author="vturecek"/>

# <a name="get-started-service-fabric-web-api-services-with-owin-self-hosting"></a>Introducción: servicios de servicio tela Web API con OWIN automática de hospedaje

Azure tela de servicio pone la potencia en las manos cuando decida cómo desea que los servicios para comunicarse con los usuarios y entre sí. En este tutorial se centra en la implementación de la comunicación de servicio con ASP.NET Web API con interfaz Web abierta para .NET (OWIN) propia alojamiento en confiable API de servicios del servicio tela. Trataremos profundidad en la API de comunicación acoplable servicios confiables. También usaremos Web API en un ejemplo paso a paso para mostrar cómo configurar una escucha de comunicación personalizado.


## <a name="introduction-to-web-api-in-service-fabric"></a>Introducción a Web API en tela de servicio

API de Web de ASP.NET es un marco popular y eficaz para la creación de HTTP APIs sobre .NET Framework. Si no está familiarizado con el marco de trabajo, vea [Introducción a ASP.NET Web API 2](http://www.asp.net/web-api/overview/getting-started-with-aspnet-web-api/tutorial-your-first-web-api) para obtener más información.

Web API en tela de servicio es la misma API Web de ASP.NET que conozco y Domino. La diferencia es cómo *host* una aplicación Web API. No se pueden utilizar Microsoft Internet Information Services (IIS). Para entender mejor la diferencia, vamos a dividirlo en dos partes:

 1. La aplicación Web API (incluidos los controladores y los modelos)
 2. El host (el servidor web, normalmente IIS)

Una aplicación Web API propiamente dicho no cambia. No difiere de aplicaciones Web API puede que haya realizado en el pasado y debería poder simplemente mueva sobre la mayor parte de su código de la aplicación. Pero, si ha sido hospedar en IIS, donde host la aplicación puede ser un poco diferente de lo que está acostumbrado. Antes de entrar en el elemento de hospedaje, comencemos con algo más familiar: la aplicación Web API.


## <a name="create-the-application"></a>Creación de la aplicación

Empiece por crear una nueva aplicación de servicio tela con un solo servicio independiente en Visual Studio de 2015:

![Cree una nueva aplicación de servicio tela](media/service-fabric-reliable-services-communication-webapi/webapi-newproject.png)

Una plantilla de Visual Studio para un servicio independiente con la API de Web está disponible para usted. En este tutorial, vamos a crear un proyecto de Web API desde cero, que da como resultado lo que obtendría si selecciona esta plantilla.

Seleccione un proyecto en blanco de servicio sin estado para obtener información sobre cómo crear un proyecto de Web API desde cero, o puede empezar con la plantilla de Web API de estado del servicio y siga a lo largo.  

![Crear un único servicio de estado](media/service-fabric-reliable-services-communication-webapi/webapi-newproject2.png)

El primer paso es extraer algunos paquetes de NuGet para Web API. El paquete que queremos utilizar es Microsoft.AspNet.WebApi.OwinSelfHost. Este paquete incluye todos los paquetes de la API de Web y los paquetes de *host* . Esto será importante más adelante.

![Crear API Web mediante el Administrador de paquetes de NuGet](media/service-fabric-reliable-services-communication-webapi/webapi-nuget.png)

Después de han instalado los paquetes, puede empezar a crear la estructura básica de project Web API. Si ha usado API de Web, la estructura de proyecto será muy familiar. Empiece agregando un `Controllers` directorio y un controlador de valores simples:

**ValuesController.cs**

```csharp
using System.Collections.Generic;
using System.Web.Http;
    
namespace WebService.Controllers
{
    public class ValuesController : ApiController
    {
        // GET api/values 
        public IEnumerable<string> Get()
        {
            return new string[] { "value1", "value2" };
        }

        // GET api/values/5 
        public string Get(int id)
        {
            return "value";
        }

        // POST api/values 
        public void Post([FromBody]string value)
        {
        }

        // PUT api/values/5 
        public void Put(int id, [FromBody]string value)
        {
        }

        // DELETE api/values/5 
        public void Delete(int id)
        {
        }
    }
}

```

A continuación, agregue una clase de inicio en la raíz del proyecto para registrar el enrutamiento, formateadores y cualquier otra configuración. Esta es también donde se conecta API Web al *host*, que se revisan más tarde. 

**Startup.cs**

```csharp
using System.Web.Http;
using Owin;

namespace WebService
{
    public static class Startup
    {
        public static void ConfigureApp(IAppBuilder appBuilder)
        {
            // Configure Web API for self-host. 
            HttpConfiguration config = new HttpConfiguration();

            config.Routes.MapHttpRoute(
                name: "DefaultApi",
                routeTemplate: "api/{controller}/{id}",
                defaults: new { id = RouteParameter.Optional }
            );

            appBuilder.UseWebApi(config);
        }
    }
}
```

Eso es todo para el elemento de aplicación. En este momento, hemos configurar simplemente el diseño básico de la project Web API. Hasta ese momento, no aparece distinta de proyectos de Web API puede que haya realizado en el pasado o de la plantilla de Web API básica. La lógica empresarial se incluye en los controladores y los modelos de forma habitual.

¿Ahora lo podemos hacer hospedaje para que realmente que podemos ejecutar?

## <a name="service-hosting"></a>Servicio de hospedaje

En tela de servicio, el servicio se ejecuta en un *proceso de host de servicio*, un archivo ejecutable que se ejecuta el código de servicio. Cuando escribe un servicio mediante la API de servicios confiables, el proyecto de servicio simplemente compila a un archivo ejecutable que registra el tipo de servicio y se ejecuta el código. Esto es verdadero en la mayoría de los casos, cuando escribe un servicio en tela de servicio en .NET. Cuando abre Program.cs en el proyecto de servicio independiente, debe ver:

```csharp
using System;
using System.Diagnostics;
using System.Threading;
using Microsoft.ServiceFabric.Services.Runtime;

internal static class Program
{
    private static void Main()
    {
        try
        {
            ServiceRuntime.RegisterServiceAsync("WebServiceType",
                context => new WebService(context)).GetAwaiter().GetResult();

            ServiceEventSource.Current.ServiceTypeRegistered(Process.GetCurrentProcess().Id, typeof(WebService).Name);

            // Prevents this host process from terminating so services keeps running. 
            Thread.Sleep(Timeout.Infinite);
        }
        catch (Exception e)
        {
            ServiceEventSource.Current.ServiceHostInitializationFailed(e.ToString());
            throw;
        }
    }
}

```

Si parece es sospechosamente el punto de entrada a una aplicación de consola, que es porque es.

Más detalles sobre el proceso de host de servicio y el registro de servicio están fuera del ámbito de este artículo. Pero es importante conocer por ahora que *que el código de servicio se está ejecutando en su propio proceso*.

## <a name="self-host-web-api-with-an-owin-host"></a>API de Web Self-host con un host OWIN

Dado que el código de la aplicación Web API está alojado en su propio proceso, ¿cómo para enlazar, hasta un servidor web? Escriba [OWIN](http://owin.org/). OWIN es simplemente un contrato entre las aplicaciones web .NET y servidores web. Tradicionalmente cuando se utiliza ASP.NET (hasta MVC 5), la aplicación web se acopla estrechamente con IIS a través de System.Web. Sin embargo, API Web implementa OWIN, por lo que puede escribir una aplicación web que está desconectada desde el servidor web que hospeda. Por este motivo, puede usar un servidor de web OWIN *alojado en sí mismo* que se puede iniciar en su propio proceso. Esto se adapta perfectamente con el modelo de hospedaje de servicio tela que hemos descrito.

En este artículo, usaremos Katana como el host OWIN para la aplicación Web API. Katana es una implementación de host de código abierto OWIN integrada en [System.Net.HttpListener](https://msdn.microsoft.com/library/system.net.httplistener.aspx) y el [Servidor HTTP API](https://msdn.microsoft.com/library/windows/desktop/aa364510.aspx)de Windows.

> [AZURE.NOTE] Para obtener más información sobre Katana, vaya al [sitio Katana](http://www.asp.net/aspnet/overview/owin-and-katana/an-overview-of-project-katana). Para obtener una introducción rápida de cómo usar Katana self-host Web API, vea [Usar OWIN a Self-Host ASP.NET Web API 2](http://www.asp.net/web-api/overview/hosting-aspnet-web-api/use-owin-to-self-host-web-api).


## <a name="set-up-the-web-server"></a>Configurar el servidor web

La API de servicios confiable proporciona un punto de entrada de comunicación donde puede conectar en pilas de comunicación que permiten a los usuarios y los clientes para conectarse al servicio:

```csharp

protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
{
    ...
}

```

El servidor web (y cualquier otra pila de comunicación que usar en el futuro, como WebSockets) deben usar la interfaz de ICommunicationListener integrar correctamente con el sistema. Las razones para ello serán más evidentes en los siguientes pasos.

Primero, cree una clase denominada OwinCommunicationListener que implementa ICommunicationListener:

**OwinCommunicationListener.cs**

```csharp
using Microsoft.Owin.Hosting;
using Microsoft.ServiceFabric.Services.Communication.Runtime;
using Owin;
using System;
using System.Fabric;
using System.Globalization;
using System.Threading;
using System.Threading.Tasks;

namespace WebService
{
    internal class OwinCommunicationListener : ICommunicationListener
    {
        public void Abort()
        {
        }

        public Task CloseAsync(CancellationToken cancellationToken)
        {
        }

        public Task<string> OpenAsync(CancellationToken cancellationToken)
        {
        }
    }
}
```

La interfaz de ICommunicationListener proporciona tres métodos para administrar una escucha de comunicaciones de su servicio:

 - *OpenAsync*. Iniciar la escucha para las solicitudes de.
 - *CloseAsync*. Detener escucha de solicitudes, finalizar las solicitudes en curso y cerrar correctamente.
 - *Anular*. Cancelar todo y se detendrá inmediatamente.

Para empezar, agregar a miembros de la clase privada para cosas que el oyente tendrán que funcione. Estas inicializadas a través del constructor y usar posteriormente al configurar la dirección URL escucha.

```csharp
internal class OwinCommunicationListener : ICommunicationListener
{
    private readonly ServiceEventSource eventSource;
    private readonly Action<IAppBuilder> startup;
    private readonly ServiceContext serviceContext;
    private readonly string endpointName;
    private readonly string appRoot;

    private IDisposable webApp;
    private string publishAddress;
    private string listeningAddress;

    public OwinCommunicationListener(Action<IAppBuilder> startup, ServiceContext serviceContext, ServiceEventSource eventSource, string endpointName)
        : this(startup, serviceContext, eventSource, endpointName, null)
    {
    }

    public OwinCommunicationListener(Action<IAppBuilder> startup, ServiceContext serviceContext, ServiceEventSource eventSource, string endpointName, string appRoot)
    {
        if (startup == null)
        {
            throw new ArgumentNullException(nameof(startup));
        }

        if (serviceContext == null)
        {
            throw new ArgumentNullException(nameof(serviceContext));
        }

        if (endpointName == null)
        {
            throw new ArgumentNullException(nameof(endpointName));
        }

        if (eventSource == null)
        {
            throw new ArgumentNullException(nameof(eventSource));
        }

        this.startup = startup;
        this.serviceContext = serviceContext;
        this.endpointName = endpointName;
        this.eventSource = eventSource;
        this.appRoot = appRoot;
    }
   

    ...

```

## <a name="implement-openasync"></a>Implementar OpenAsync

Para configurar el servidor web, necesita dos partes de la información:

 - *Prefijo de ruta de acceso de una URL*. Aunque es opcional, es conveniente para establecer esto ahora para que puede alojar de forma segura varios servicios web en la aplicación.
 - *Un puerto*.

Antes de obtener un puerto para el servidor web, es importante que comprenda que servicio tela proporciona un nivel de aplicación que actúa como un búfer entre la aplicación y el sistema operativo subyacente que se ejecuta en. Como tal, tela de servicio proporciona una manera de configurar los *extremos* de los servicios. Tela de servicio garantiza que los extremos estén disponibles para su servicio. De este modo, no tiene configurarlos usted mismo en el entorno de sistema operativo subyacente. Puede hospedar fácilmente la aplicación de servicio tela en diferentes entornos sin tener que realizar los cambios en la aplicación. (Por ejemplo, puede hospedar la misma aplicación en Azure o en su propio centro de datos).

Configurar un extremo HTTP en PackageRoot\ServiceManifest.xml:

```xml

<Resources>
    <Endpoints>
        <Endpoint Name="ServiceEndpoint" Type="Input" Protocol="http" Port="8281" />
    </Endpoints>
</Resources>

```

Este paso es importante porque el proceso de host de servicio se ejecuta en credenciales restringidas (servicio de red en Windows). Esto significa que el servicio no tiene acceso al configurar un extremo HTTP en su propia. Mediante la configuración de extremo, servicio tela sabe para configurar la lista de control de acceso (ACL) para la dirección URL que escuchará el servicio. Servicio tela también le ofrece un lugar estándar para configurar los extremos.


En el OwinCommunicationListener.cs, puede empezar a implementar OpenAsync. Esto es donde empieza el servidor web. En primer lugar, obtener la información de punto final y crear la dirección URL que escuchará el servicio. La dirección URL serán diferente dependiendo de si se utiliza la escucha en un servicio independiente o un servicio de estado. Para un servicio de estado, debe crear una dirección única para cada réplica de estado del servicio escucha en la escucha. Servicios de estado, la dirección puede ser mucho más sencilla. 

```csharp
public Task<string> OpenAsync(CancellationToken cancellationToken)
{
    var serviceEndpoint = this.serviceContext.CodePackageActivationContext.GetEndpoint(this.endpointName);
    var protocol = serviceEndpoint.Protocol;
    int port = serviceEndpoint.Port;

    if (this.serviceContext is StatefulServiceContext)
    {
        StatefulServiceContext statefulServiceContext = this.serviceContext as StatefulServiceContext;

        this.listeningAddress = string.Format(
            CultureInfo.InvariantCulture,
            "{0}://+:{1}/{2}{3}/{4}/{5}",
            protocol,
            port,
            string.IsNullOrWhiteSpace(this.appRoot)
                ? string.Empty
                : this.appRoot.TrimEnd('/') + '/',
            statefulServiceContext.PartitionId,
            statefulServiceContext.ReplicaId,
            Guid.NewGuid());
    }
    else if (this.serviceContext is StatelessServiceContext)
    {
        this.listeningAddress = string.Format(
            CultureInfo.InvariantCulture,
            "{0}://+:{1}/{2}",
            protocol,
            port,
            string.IsNullOrWhiteSpace(this.appRoot)
                ? string.Empty
                : this.appRoot.TrimEnd('/') + '/');
    }
    else
    {
        throw new InvalidOperationException();
    }
    
    ...

```

Tenga en cuenta que aquí se utiliza "http://+". Esto es para asegurarse de que el servidor web está escuchando en todas las direcciones disponibles, incluidos host local, FQDN y la dirección IP del equipo.

La implementación de OpenAsync es uno de los motivos más importantes, ¿por qué se ha implementado el servidor web (o cualquier pila de comunicación) como un ICommunicationListener, en lugar de tenerlo simplemente abre directamente desde `RunAsync()` en el servicio. El valor devuelto desde OpenAsync es la dirección que está escuchando el servidor web. Cuando esta dirección se devuelve al sistema, registra la dirección con el servicio. Servicio tela proporciona una API que permite a los clientes y otros servicios pedir esta dirección por nombre de servicio. Esto es importante porque la dirección del servicio no es estática. Servicios se mueven del clúster para fines de equilibrio y la disponibilidad de recursos. Este es el mecanismo que permite a los clientes resolver la dirección de escucha de un servicio.

Con esto en mente, OpenAsync inicia el servidor web y devuelve la dirección que está escuchando. Nota que escuche "http://+", pero antes de OpenAsync devuelve la dirección, el "+" se sustituye por la dirección IP o FQDN del nodo que actualmente está activado. La dirección devuelta por este método es lo que está registrado con el sistema. También es lo que ven los clientes y otros servicios al que le pidan dirección del servicio. Para que los clientes de conectarse correctamente, necesita un FQDN o IP real en la dirección.

```csharp
    ...

    this.publishAddress = this.listeningAddress.Replace("+", FabricRuntime.GetNodeContext().IPAddressOrFQDN);

    try
    {
        this.eventSource.Message("Starting web server on " + this.listeningAddress);

        this.webApp = WebApp.Start(this.listeningAddress, appBuilder => this.startup.Invoke(appBuilder));

        this.eventSource.Message("Listening on " + this.publishAddress);

        return Task.FromResult(this.publishAddress);
    }
    catch (Exception ex)
    {
        this.eventSource.Message("Web server failed to open endpoint {0}. {1}", this.endpointName, ex.ToString());

        this.StopWebServer();

        throw;
    }
}

```

Tenga en cuenta que esto hace referencia a la clase de inicio que se pasa a la OwinCommunicationListener en el constructor. El servidor web utiliza esta instancia de inicio para iniciar la aplicación Web API.

La `ServiceEventSource.Current.Message()` línea aparecerá en la ventana de eventos de diagnóstico más adelante, cuando ejecute la aplicación para confirmar que el servidor web se ha iniciado correctamente.

## <a name="implement-closeasync-and-abort"></a>Implementar CloseAsync y anulación

Por último, implemente CloseAsync y Cancelar para detener el servidor web. El servidor web se puede detenerla eliminando el controlador de servidor que se creó durante la OpenAsync.

```csharp
public Task CloseAsync(CancellationToken cancellationToken)
{
    this.eventSource.Message("Closing web server on endpoint {0}", this.endpointName);
            
    this.StopWebServer();

    return Task.FromResult(true);
}

public void Abort()
{
    this.eventSource.Message("Aborting web server on endpoint {0}", this.endpointName);
    
    this.StopWebServer();
}

private void StopWebServer()
{
    if (this.webApp != null)
    {
        try
        {
            this.webApp.Dispose();
        }
        catch (ObjectDisposedException)
        {
            // no-op
        }
    }
}
```

En este ejemplo de implementación, CloseAsync y anular simplemente dejar el servidor web. Puede elegir si desea para realizar un apagado más correctamente coordinado del servidor web en CloseAsync. Por ejemplo, el cierre podría esperar solicitudes en vuelo completarse antes de la devolución.

## <a name="start-the-web-server"></a>Inicie el servidor web

Ahora ya está listo para crear y devolver una instancia de OwinCommunicationListener para iniciar el servidor web. En la clase de servicio (WebService.cs), anular la `CreateServiceInstanceListeners()` método:

```csharp
protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
{
    var endpoints = Context.CodePackageActivationContext.GetEndpoints()
                           .Where(endpoint => endpoint.Protocol == EndpointProtocol.Http || endpoint.Protocol == EndpointProtocol.Https)
                           .Select(endpoint => endpoint.Name);

    return endpoints.Select(endpoint => new ServiceInstanceListener(
        serviceContext => new OwinCommunicationListener(Startup.ConfigureApp, serviceContext, ServiceEventSource.Current, endpoint), endpoint));
}
```

Esto es donde la *aplicación* de Web API y el *host* OWIN finalmente cumplen. El host (OwinCommunicationListener) se da una instancia de la *aplicación* (Web API) a través de la clase de inicio. Servicio tela administra su ciclo de vida. En este mismo patrón normalmente puede seguirse con cualquier pila de comunicaciones.

## <a name="put-it-all-together"></a>Integrarlo todo

En este ejemplo, no es necesario hacer nada la `RunAsync()` método, por lo que se pueden quitar simplemente que reemplazan.

La implementación del servicio final debe ser muy sencilla. Solo necesita crear la escucha de comunicación:

```csharp
using System;
using System.Collections.Generic;
using System.Fabric;
using System.Fabric.Description;
using System.Linq;
using Microsoft.ServiceFabric.Services.Communication.Runtime;
using Microsoft.ServiceFabric.Services.Runtime;

namespace WebService
{
    internal sealed class WebService : StatelessService
    {
        public WebService(StatelessServiceContext context)
            : base(context)
        { }

        protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
        {
            var endpoints = Context.CodePackageActivationContext.GetEndpoints()
                                   .Where(endpoint => endpoint.Protocol == EndpointProtocol.Http || endpoint.Protocol == EndpointProtocol.Https)
                                   .Select(endpoint => endpoint.Name);

            return endpoints.Select(endpoint => new ServiceInstanceListener(
                serviceContext => new OwinCommunicationListener(Startup.ConfigureApp, serviceContext, ServiceEventSource.Current, endpoint), endpoint));
        }
    }
}
```

El completado `OwinCommunicationListener` clase:

```csharp
using System;
using System.Diagnostics;
using System.Fabric;
using System.Globalization;
using System.Threading;
using System.Threading.Tasks;
using Microsoft.Owin.Hosting;
using Microsoft.ServiceFabric.Services.Communication.Runtime;
using Owin;

namespace WebService
{
    internal class OwinCommunicationListener : ICommunicationListener
    {
        private readonly ServiceEventSource eventSource;
        private readonly Action<IAppBuilder> startup;
        private readonly ServiceContext serviceContext;
        private readonly string endpointName;
        private readonly string appRoot;

        private IDisposable webApp;
        private string publishAddress;
        private string listeningAddress;

        public OwinCommunicationListener(Action<IAppBuilder> startup, ServiceContext serviceContext, ServiceEventSource eventSource, string endpointName)
            : this(startup, serviceContext, eventSource, endpointName, null)
        {
        }

        public OwinCommunicationListener(Action<IAppBuilder> startup, ServiceContext serviceContext, ServiceEventSource eventSource, string endpointName, string appRoot)
        {
            if (startup == null)
            {
                throw new ArgumentNullException(nameof(startup));
            }

            if (serviceContext == null)
            {
                throw new ArgumentNullException(nameof(serviceContext));
            }

            if (endpointName == null)
            {
                throw new ArgumentNullException(nameof(endpointName));
            }

            if (eventSource == null)
            {
                throw new ArgumentNullException(nameof(eventSource));
            }

            this.startup = startup;
            this.serviceContext = serviceContext;
            this.endpointName = endpointName;
            this.eventSource = eventSource;
            this.appRoot = appRoot;
        }

        public Task<string> OpenAsync(CancellationToken cancellationToken)
        {
            var serviceEndpoint = this.serviceContext.CodePackageActivationContext.GetEndpoint(this.endpointName);
            var protocol = serviceEndpoint.Protocol;
            int port = serviceEndpoint.Port;

            if (this.serviceContext is StatefulServiceContext)
            {
                StatefulServiceContext statefulServiceContext = this.serviceContext as StatefulServiceContext;

                this.listeningAddress = string.Format(
                    CultureInfo.InvariantCulture,
                    "{0}://+:{1}/{2}{3}/{4}/{5}",
                    protocol,
                    port,
                    string.IsNullOrWhiteSpace(this.appRoot)
                        ? string.Empty
                        : this.appRoot.TrimEnd('/') + '/',
                    statefulServiceContext.PartitionId,
                    statefulServiceContext.ReplicaId,
                    Guid.NewGuid());
            }
            else if (this.serviceContext is StatelessServiceContext)
            {
                this.listeningAddress = string.Format(
                    CultureInfo.InvariantCulture,
                    "{0}://+:{1}/{2}",
                    protocol,
                    port,
                    string.IsNullOrWhiteSpace(this.appRoot)
                        ? string.Empty
                        : this.appRoot.TrimEnd('/') + '/');
            }
            else
            {
                throw new InvalidOperationException();
            }

            this.publishAddress = this.listeningAddress.Replace("+", FabricRuntime.GetNodeContext().IPAddressOrFQDN);

            try
            {
                this.eventSource.Message("Starting web server on " + this.listeningAddress);

                this.webApp = WebApp.Start(this.listeningAddress, appBuilder => this.startup.Invoke(appBuilder));

                this.eventSource.Message("Listening on " + this.publishAddress);

                return Task.FromResult(this.publishAddress);
            }
            catch (Exception ex)
            {
                this.eventSource.Message("Web server failed to open endpoint {0}. {1}", this.endpointName, ex.ToString());

                this.StopWebServer();

                throw;
            }
        }

        public Task CloseAsync(CancellationToken cancellationToken)
        {
            this.eventSource.Message("Closing web server on endpoint {0}", this.endpointName);

            this.StopWebServer();

            return Task.FromResult(true);
        }

        public void Abort()
        {
            this.eventSource.Message("Aborting web server on endpoint {0}", this.endpointName);

            this.StopWebServer();
        }

        private void StopWebServer()
        {
            if (this.webApp != null)
            {
                try
                {
                    this.webApp.Dispose();
                }
                catch (ObjectDisposedException)
                {
                    // no-op
                }
            }
        }
    }
}
```

Ahora que ha poner todos los elementos en su lugar, el proyecto debe tener un aspecto similar a una aplicación Web API típica con puntos de entrada de la API de servicios confiable y un host OWIN:


![Web API con puntos de entrada de la API de servicios confiable y host OWIN](media/service-fabric-reliable-services-communication-webapi/webapi-projectstructure.png)

## <a name="run-and-connect-through-a-web-browser"></a>Ejecutar y conectarse a través de un explorador web

Si todavía no lo ha hecho, [Configurar el entorno de desarrollo](service-fabric-get-started.md).


Ahora puede compilar e implementar su servicio. Presione **F5** en Visual Studio para crear e implementar la aplicación. En la ventana de eventos de diagnóstico, verá un mensaje que indica que el servidor web abierto en http://localhost:8281 /.


![Ventana Visual Studio eventos de diagnóstico](media/service-fabric-reliable-services-communication-webapi/webapi-diagnostics.png)

> [AZURE.NOTE] Si ya ha abierto el puerto ningún otro proceso en su equipo, verá un error aquí. Esto indica que no se puede abrir la escucha. Si es así, pruebe con un puerto diferente para la configuración del extremo en ServiceManifest.xml.


Una vez que se ejecuta el servicio, abra un explorador y vaya a la [api de http://localhost:8281/valores](http://localhost:8281/api/values) para probarlo.

## <a name="scale-it-out"></a>Ajustar tamaño de salida

Escalado de aplicaciones web sin estado normalmente significa agregar más equipos y girando las aplicaciones web en ellas. Puede hacer por usted de motor de organización del servicio tela siempre que se agregan nuevos nodos a un clúster. Al crear instancias de un servicio independiente, puede especificar el número de instancias que desea crear. Tela de servicio coloca ese número de instancias de nodos en el clúster. Y asegura de que no debe crear más de una instancia de un nodo. También puede indicar al servicio tela crear siempre una instancia en todos los nodos especificando **-1** para el recuento de la instancia. Así se garantiza que, siempre que agregue nodos escalar el clúster, se creará una instancia de su servicio de estado en los nodos nuevos. Este valor es una propiedad de la instancia de servicio, por lo que se establece cuando se crea una instancia de servicio. Puede hacerlo a través de PowerShell:

```powershell

New-ServiceFabricService -ApplicationName "fabric:/WebServiceApplication" -ServiceName "fabric:/WebServiceApplication/WebService" -ServiceTypeName "WebServiceType" -Stateless -PartitionSchemeSingleton -InstanceCount -1

```

También puede hacer al definir un servicio de forma predeterminada en un proyecto de Visual Studio sin estado del servicio:

```xml

<DefaultServices>
  <Service Name="WebService">
    <StatelessService ServiceTypeName="WebServiceType" InstanceCount="-1">
      <SingletonPartition />
    </StatelessService>
  </Service>
</DefaultServices>

```

Para obtener más información sobre cómo crear instancias del servicio y aplicación, vea [implementar una aplicación](service-fabric-deploy-remove-applications.md).

## <a name="next-steps"></a>Pasos siguientes

[Depurar la aplicación de servicio tela mediante Visual Studio](service-fabric-debugging-your-application.md)
