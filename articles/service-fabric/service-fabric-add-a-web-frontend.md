<properties
   pageTitle="Crear un web front-end de su aplicación utilizando ASP.NET Core | Microsoft Azure"
   description="Exponer la aplicación de servicio tela para la web mediante un proyecto de ASP.NET Core Web API y la comunicación entre servicio a través de ServiceProxy."
   services="service-fabric"
   documentationCenter=".net"
   authors="seanmck"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotNet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="08/11/2016"
   ms.author="seanmck"/>


# <a name="build-a-web-service-front-end-for-your-application-using-aspnet-core"></a>Crear un web servicio front-end para la aplicación con núcleo de ASP.NET

De forma predeterminada, servicios de Azure servicio tela no proporciona una interfaz pública para la web. Para exponer la funcionalidad de la aplicación a los clientes HTTP, debe crear un proyecto web para que actúe como punto de entrada y, a continuación, comunicar desde allí a los servicios individuales.

En este tutorial, se le recoger donde se dejó en el tutorial [crear su primera aplicación en Visual Studio](service-fabric-create-your-first-application-in-visual-studio.md) y agregar un servicio web delante el servicio de contador con estado. Si no lo ha hecho ya, debe volver atrás y recorra primero ese tutorial.

## <a name="add-an-aspnet-core-service-to-your-application"></a>Agregar un servicio de ASP.NET principales a la aplicación

Núcleo de ASP.NET es un entorno de desarrollo ligero, entre plataformas web que puede usar para crear la interfaz de usuario de web moderna y web API. Vamos a agregar un proyecto de ASP.NET Web API a nuestra aplicación existente.

>[AZURE.NOTE] Para completar este tutorial, debe [instalar .NET Core 1.0][dotnetcore-install].

1. En el Explorador de soluciones, haga clic con el botón **Servicios** dentro del proyecto de la aplicación y elija **Agregar > nuevo servicio de tela**.

    ![Agregar un nuevo servicio a una aplicación existente][vs-add-new-service]

2. En la página **crear un servicio** , elija **ASP.NET Core** y asígnele un nombre.

    ![Elegir servicio web ASP.NET en el cuadro de diálogo nuevo servicio][vs-new-service-dialog]

3. La siguiente página proporciona un conjunto de ASP.NET Core plantillas de proyectos. Tenga en cuenta que se trata de las mismas plantillas que verá si ha creado un proyecto principal de ASP.NET fuera de una aplicación de servicio tela. En este tutorial, elegimos **API de Web**. Sin embargo, puede aplicar los mismos conceptos para crear una aplicación web completa.

    ![Elegir tipo de proyecto ASP.NET][vs-new-aspnet-project-dialog]

    Una vez creado el proyecto de Web API, tendrá dos servicios en la aplicación. Continuar generar la aplicación, agregará más servicios de exactamente la misma manera. Cada uno puede ser actualizado y una versión de forma independiente.

>[AZURE.TIP] Para obtener más información sobre cómo crear ASP.NET Core services, consulte la [Documentación de ASP.NET básica](https://docs.asp.net).

## <a name="run-the-application"></a>Ejecute la aplicación

Para hacerse una idea de lo que hemos hecho, vamos a implementar la nueva aplicación y eche un vistazo a las predeterminadas que proporciona la plantilla de Web API de ASP.NET principal.

1. Presione F5 en Visual Studio para depurar la aplicación.

2. Cuando se complete la implementación, Visual Studio se iniciará el explorador en la raíz del servicio ASP.NET Web API--algo como http://localhost:33003. El número de puerto se asigna aleatoriamente y puede ser diferente en su equipo. La plantilla de ASP.NET Core Web API no proporciona comportamiento predeterminado para la raíz, por lo que se producirá un error en el explorador.

3. Agregar `/api/values` a la ubicación en el explorador. Esto se llamará la `Get` método en la ValuesController en la plantilla de Web API. Devuelve la respuesta predeterminada proporcionada por la plantilla--una matriz JSON que contiene dos cadenas:

    ![Valores predeterminados devueltos desde plantilla Web API de núcleo de ASP.NET][browser-aspnet-template-values]

    Al final del tutorial, se habrá reemplazan estos valores predeterminados por el valor de contador más reciente de nuestro servicio de estado.


## <a name="connect-the-services"></a>Conecte los servicios

Servicio tela ofrece mayor flexibilidad completa en modo de comunicación con servicios confiables. En una sola aplicación, es posible que tenga los servicios que están disponibles a través de TCP, otros servicios que están disponibles a través de una API de REST de HTTP y otros servicios que están disponibles a través de sockets de web. Para obtener información general sobre las opciones disponibles y las compensaciones implicadas, vea [comunicar con los servicios](service-fabric-connect-and-communicate-with-services.md). En este tutorial, se siga uno de los métodos más simples y usar la `ServiceProxy` / `ServiceRemotingListener` clases que se proporcionan en el SDK.

En la `ServiceProxy` enfoque (modelado en llamadas a procedimiento remoto o RPC), definir una interfaz para que actúe como el contrato de servicio público. A continuación, use dicha interfaz para generar una clase proxy para interactuar con el servicio.


### <a name="create-the-interface"></a>Crear la interfaz

Empezaremos mediante la creación de la interfaz para que actúe como el contrato entre el servicio de estado y sus clientes, incluido el proyecto principal de ASP.NET.

1. En el Explorador de soluciones, haga clic en la solución y elija **Agregar** > **Nuevo proyecto**.

2. Seleccione la entrada de **Visual C#** en el panel de navegación izquierdo y, a continuación, seleccione la plantilla de **Biblioteca de clases** . Asegúrese de que la versión de .NET Framework se establece en **4.5.2**.

    ![Crear un proyecto de la interfaz de su servicio de estado][vs-add-class-library-project]

3. En orden de una interfaz para que puedan usarlo `ServiceProxy`, debe derivarse de la interfaz de IService. Esta interfaz se incluye en uno de los paquetes de servicio tela NuGet. Para agregar el paquete, haga clic en el nuevo proyecto de biblioteca de clases y elija **Administrar paquetes de NuGet**.

4. Buscar el paquete de **Microsoft.ServiceFabric.Services** e instálelo.

    ![Agregar el paquete de NuGet de servicios][vs-services-nuget-package]

5. En la biblioteca de clases, crear una interfaz con un solo método, `GetCountAsync`, y ampliar la interfaz de IService.

    ```c#
    namespace MyStatefulService.Interfaces
    {
        using Microsoft.ServiceFabric.Services.Remoting;

        public interface ICounter: IService
        {
            Task<long> GetCountAsync();
        }
    }
    ```


### <a name="implement-the-interface-in-your-stateful-service"></a>Implementar la interfaz en el servicio de estado

Ahora que hemos definido la interfaz, es necesario implementar en el servicio de estado.

1. En el servicio con estado, agregue una referencia al proyecto de biblioteca de clase que contiene la interfaz.

    ![Agregar una referencia al proyecto de biblioteca de clase en el servicio de estado][vs-add-class-library-reference]

2. Busque la clase que herede de `StatefulService`, como por ejemplo `MyStatefulService`, y extenderla a implementar la `ICounter` interfaz.

    ```c#
    using MyStatefulService.Interfaces;

    ...

    public class MyStatefulService : StatefulService, ICounter
    {        
          // ...
    }
    ```

3. Ahora implemente el único método que se define en la `ICounter` interfaz, `GetCountAsync`.

    ```c#
    public async Task<long> GetCountAsync()
    {
      var myDictionary =
        await this.StateManager.GetOrAddAsync<IReliableDictionary<string, long>>("myDictionary");

        using (var tx = this.StateManager.CreateTransaction())
        {          
            var result = await myDictionary.TryGetValueAsync(tx, "Counter");
            return result.HasValue ? result.Value : 0;
        }
    }
    ```


### <a name="expose-the-stateful-service-using-a-service-remoting-listener"></a>Exponer el servicio con estado con un agente de escucha de servicio remoto

Con la `ICounter` interfaz implementada, el paso final para habilitar el servicio poder llamarlo desde otros servicios con estado es abrir un canal de comunicación. Para los servicios con estado, servicio tela proporciona un método de puede reemplazar denominado `CreateServiceReplicaListeners`. Con este método, puede especificar escucha de comunicación de uno o más, según el tipo de comunicación que desea habilitar en el servicio.

>[AZURE.NOTE] El método equivalente para abrir un canal de comunicación a servicios sin estado se llama `CreateServiceInstanceListeners`.

En este caso, se reemplazará la existente `CreateServiceReplicaListeners` método y proporcionar una instancia de `ServiceRemotingListener`, lo que crea un extremo RPC es llamar desde clientes mediante `ServiceProxy`.  

```c#
using Microsoft.ServiceFabric.Services.Remoting.Runtime;

...

protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
{
    return new List<ServiceReplicaListener>()
    {
        new ServiceReplicaListener(
            (context) =>
                this.CreateServiceRemotingListener(context))
    };
}
```


### <a name="use-the-serviceproxy-class-to-interact-with-the-service"></a>Use la clase ServiceProxy para interactuar con el servicio

Nuestro servicio con estado ahora está listo para recibir el tráfico de otros servicios. Así que todo lo que sigue está agregando el código para comunicarse con ella desde el servicio web ASP.NET.

1. En su proyecto ASP.NET, agregue una referencia a la biblioteca de clases que contiene la `ICounter` interfaz.

2. En el menú **Generar** , abra el **Administrador de configuración**. Verá algo parecido a esto:

    ![Biblioteca de clase de configuración de administrador que muestra como cualquier CPU][vs-configuration-manager]

    Tenga en cuenta que el proyecto de biblioteca de clase, **MyStatefulService.Interface**, está configurado para generar para cualquier CPU. Para que funcione correctamente con tela de servicio, se debe estar explícitamente dirigido al x64. Haga clic en la lista desplegable de la plataforma y elija **nuevo**y crear una x64 configuración de la plataforma.

    ![Crear nueva plataforma de biblioteca de clases][vs-create-platform]

3. Agregar el paquete de Microsoft.ServiceFabric.Services al proyecto ASP.NET, igual que realizó antes para el proyecto de la biblioteca de clases. Esto le proporcionará la `ServiceProxy` clase.

4. En la carpeta **controladores** , abra la `ValuesController` clase. Tenga en cuenta que el `Get` método actualmente solo devuelve una matriz de cadena codificada de "valor1" y "valor2"--que coincide con lo que observamos anteriormente en el explorador. Reemplazar esta implementación con el siguiente código:

    ```c#
    using MyStatefulService.Interfaces;
    using Microsoft.ServiceFabric.Services.Remoting.Client;

    ...

    public async Task<IEnumerable<string>> Get()
    {
        ICounter counter =
            ServiceProxy.Create<ICounter>(new Uri("fabric:/MyApplication/MyStatefulService"), new ServicePartitionKey(0));

        long count = await counter.GetCountAsync();

        return new string[] { count.ToString() };
    }
    ```

    La primera línea de código es la clave. Para crear el proxy ICounter al servicio con estado, debe proporcionar dos partes de la información: un ID y el nombre del servicio.

    Puede usar particiones a escala con estado servicios al dividir su estado en depósitos diferentes, en función de una clave que defina, como un ID de cliente o código postal. En nuestra aplicación trivial, el servicio con estado sólo tiene una partición, por lo que no importa la clave. Cualquier tecla que proporciona le llevará a la misma partición. Para más información sobre servicios de partición, vea [cómo partición servicios a tela confiable](service-fabric-concepts-partitioning.md).

    El nombre del servicio es un URI de la estructura de formulario: /&lt;application_name&gt;/&lt;service_name&gt;.

    Con estas dos partes de la información, servicio tela puede identificar el equipo que se envían a las convocatorias. La `ServiceProxy` clase perfecta también controla el caso se produce un error en el equipo que hospeda la partición de servicio con estado y otro equipo debe ser ascendido para tomar su lugar. Esta abstracción permite escribir el código de cliente para tratar con otros servicios significativamente más simples.

    Una vez que tenemos el servidor proxy, nos simplemente invocar la `GetCountAsync` método y volver a su resultado.

5. Presione F5 de nuevo para ejecutar la aplicación modificada. Como antes, Visual Studio iniciará automáticamente el explorador en la raíz del proyecto web. Agregar la ruta de acceso "api o valores" y verá el valor actual de contador devuelto.

    ![El valor de contador con estado que se muestra en el explorador][browser-aspnet-counter-value]

    Actualice el explorador periódicamente para ver el valor de contador actualizar.


>[AZURE.WARNING] El servidor web de ASP.NET Core proporcionado en la plantilla, conocida como Kestrel, es que [no es compatible actualmente para controlar el tráfico de internet directa](https://docs.asp.net/en/latest/fundamentals/servers.html#kestrel). Escenarios de producción, considere la posibilidad de los extremos de ASP.NET Core detrás de la [Administración de la API] de hospedaje[ api-management-landing-page] u otra puerta de enlace a través de internet. Tenga en cuenta que no se admite tela de servicio para su implementación en IIS.


## <a name="what-about-actors"></a>¿Qué hay actores?

En este tutorial se centra en Agregar un web front-end que se comunica con un servicio de estado. Sin embargo, puede seguir un modelo muy similar a hablar con actores. De hecho, es algo más sencilla.

Cuando se crea un proyecto de actor, Visual Studio genera automáticamente un proyecto de la interfaz de. Puede usar la interfaz para generar a un proxy de actor en el proyecto web para comunicarse con el actor. El canal de comunicación se proporciona automáticamente. Por lo que no es necesario hacer nada que es equivalente a establecer un `ServiceRemotingListener` como hecho para el servicio de estado en este tutorial.

## <a name="how-web-services-work-on-your-local-cluster"></a>Funcionan de los servicios web en el clúster local

En general, puede implementar la misma aplicación al servicio tela a un clúster de varios equipo implementado en el clúster local y estar altamente seguro que funcionará como se esperaba. Esto es porque el clúster local es simplemente una configuración de nodo de cinco contraída a un único equipo.

Cuando se trata de servicios web, sin embargo, hay un matiz clave. Cuando el clúster se encuentra detrás de un equilibrador de carga, como lo hace en Azure, debe asegurarse de que se implementan los servicios web en todos los equipos, ya que el equilibrador de carga le simplemente turnos tráfico a través de los equipos. Puede hacerlo estableciendo la `InstanceCount` para el servicio en el valor especial "-1".

Por el contrario, cuando se ejecuta un servicio web localmente, debe asegurarse de que solo una instancia de se está ejecutando el servicio. En caso contrario, se ejecutará en conflictos de varios procesos que escuchan en la misma ruta de acceso y puerto. Como resultado, el recuento de instancias del servicio web se debe establecer en "1" para las implementaciones locales.

Para obtener información sobre cómo configurar valores diferentes para entorno diferente, vea [parámetros de la aplicación de administración para múltiples entornos](service-fabric-manage-multiple-environment-app-configuration.md).

## <a name="next-steps"></a>Pasos siguientes

- [Crear un clúster en Azure para implementar la aplicación en la nube](service-fabric-cluster-creation-via-portal.md)
- [Más información sobre la comunicación con los servicios](service-fabric-connect-and-communicate-with-services.md)
- [Más información sobre la partición services con estado](service-fabric-concepts-partitioning.md)

<!-- Image References -->

[vs-add-new-service]: ./media/service-fabric-add-a-web-frontend/vs-add-new-service.png
[vs-new-service-dialog]: ./media/service-fabric-add-a-web-frontend/vs-new-service-dialog.png
[vs-new-aspnet-project-dialog]: ./media/service-fabric-add-a-web-frontend/vs-new-aspnet-project-dialog.png
[browser-aspnet-template-values]: ./media/service-fabric-add-a-web-frontend/browser-aspnet-template-values.png
[vs-add-class-library-project]: ./media/service-fabric-add-a-web-frontend/vs-add-class-library-project.png
[vs-add-class-library-reference]: ./media/service-fabric-add-a-web-frontend/vs-add-class-library-reference.png
[vs-services-nuget-package]: ./media/service-fabric-add-a-web-frontend/vs-services-nuget-package.png
[browser-aspnet-counter-value]: ./media/service-fabric-add-a-web-frontend/browser-aspnet-counter-value.png
[vs-configuration-manager]: ./media/service-fabric-add-a-web-frontend/vs-configuration-manager.png
[vs-create-platform]: ./media/service-fabric-add-a-web-frontend/vs-create-platform.png


<!-- external links -->
[dotnetcore-install]: https://www.microsoft.com/net/core#windows
[api-management-landing-page]: https://azure.microsoft.com/en-us/services/api-management/
