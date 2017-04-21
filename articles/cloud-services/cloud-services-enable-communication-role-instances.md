<properties 
pageTitle="Comunicación para las funciones de servicios de nube | Microsoft Azure" 
description="Instancias de rol de servicios de nube pueden tener extremos (http, https, tcp y udp) definidos para ellos que comunican con el exterior o entre otras instancias de la función." 
services="cloud-services" 
documentationCenter="" 
authors="Thraka" 
manager="timlt" 
editor=""/>
<tags 
ms.service="cloud-services" 
ms.workload="tbd" 
ms.tgt_pltfrm="na" 
ms.devlang="na" 
ms.topic="article" 
ms.date="09/06/2016" 
ms.author="adegeo"/>

# <a name="enable-communication-for-role-instances-in-azure"></a>Habilitar la comunicación para instancias de la función de azure

Funciones de servicio de nube comunican a través de conexiones internas y externas. Conexiones externas se denominan **extremos de entrada** mientras conexiones internas se denominan **extremos internos**. En este tema se describe cómo modificar la [definición del servicio](cloud-services-model-and-package.md#csdef) para crear los extremos.


## <a name="input-endpoint"></a>Extremo de entrada
El extremo de entrada se usa cuando desea exponer un puerto fuera. Especifique el tipo de protocolo y el puerto del extremo de los que se aplicará a los puertos internos y externos del extremo. Si lo desea, puede especificar un puerto interno diferente del extremo con el atributo de [puerto](https://msdn.microsoft.com/library/azure/gg557552.aspx#InputEndpoint) .

El extremo de entrada puede utilizar los protocolos siguientes: **http, https, tcp y udp**.

Para crear un extremo de entrada, agregue el elemento secundario de **InputEndpoint** al elemento **extremos** de rol web o de trabajo.

```xml
<Endpoints>
  <InputEndpoint name="StandardWeb" protocol="http" port="80" localPort="80" />
</Endpoints> 
```

## <a name="instance-input-endpoint"></a>Extremo de entrada de instancia
Extremos de entrada de instancia son similares a las de entrada de extremos, pero permite asignar puertos público específicos para cada instancia de rol individuales mediante el reenvío de puerto en el equilibrador de carga. Puede especificar un único puerto público o un intervalo de puertos.

Solo puede usar el extremo de entrada de la instancia **tcp** o **udp** como protocolo.

Para crear un extremo de entrada de instancia, agregue el elemento secundario de **InstanceInputEndpoint** al elemento **extremos** de rol web o de trabajo.

```xml
<Endpoints>
  <InstanceInputEndpoint name="Endpoint2" protocol="tcp" localPort="10100">
    <AllocatePublicPortFrom>
      <FixedPortRange max="10109" min="10105" />
    </AllocatePublicPortFrom>
  </InstanceInputEndpoint>
</Endpoints>
```

## <a name="internal-endpoint"></a>Extremo interno
Extremos internos están disponibles para la comunicación de instancia de instancia. El puerto es opcional y si se omite, se asigna un puerto dinámico al extremo. Puede utilizar un intervalo de puertos. Hay un límite de cinco extremos internos por rol.

El extremo interno puede usar los siguientes protocolos: **http, tcp y udp, cualquier**.

Para crear un extremo de entrada interno, agregar el elemento secundario de **InternalEndpoint** al elemento **extremos** de rol web o de trabajo.

```xml
<Endpoints>
  <InternalEndpoint name="Endpoint3" protocol="any" port="8999" />
</Endpoints> 
```

También puede usar un intervalo de puertos.

```xml
<Endpoints>
  <InternalEndpoint name="Endpoint3" protocol="any">
    <FixedPortRange max="8995" min="8999" />
  </InternalEndpoint>
</Endpoints>
```


## <a name="worker-roles-vs-web-roles"></a>Funciones de trabajo frente a las funciones Web

Hay una diferencia secundaria con extremos cuando se trabaja con los roles de web y trabajador. La función web debe tener al menos un extremo de entrada única mediante el protocolo **HTTP** .


```xml
<Endpoints>
  <InputEndpoint name="StandardWeb" protocol="http" port="80" localPort="80" />
  <!-- more endpoints may be declared after the first InputEndPoint -->
</Endpoints>
```

## <a name="using-the-net-sdk-to-access-an-endpoint"></a>Mediante el SDK de .NET para tener acceso a un punto final
La biblioteca de Azure administrada proporciona métodos para instancias de la función para comunicarse en tiempo de ejecución. Desde el código que se ejecuta dentro de una instancia de rol, puede recuperar información sobre la existencia de otras instancias de la función y sus extremos, así como información sobre la instancia actual de la función.

> [AZURE.NOTE] Sólo puede recuperar información sobre las instancias de la función que se están ejecutando en el servicio de nube y que defina al menos un extremo interno. No puede obtener datos sobre instancias de función que se ejecutan en un servicio diferente.

Puede usar la propiedad [instancias](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.role.instances.aspx) para recuperar las instancias de una función. En primer lugar usar el [CurrentRoleInstance](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleenvironment.currentroleinstance.aspx) para devolver una referencia a la instancia actual de la función y, a continuación, usar la propiedad de la [función](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleinstance.role.aspx) para devolver una referencia a la propia función.

Cuando se conecta a una instancia de funciones mediante programación a través de .NET SDK, es relativamente fácil acceso a la información de extremo. Por ejemplo, una vez que ya ha conectado a un entorno de rol específico, puede obtener el puerto de un extremo específico con este código:

```csharp
int port = RoleEnvironment.CurrentRoleInstance.InstanceEndpoints["StandardWeb"].IPEndpoint.Port;
```

La propiedad de **instancias** devuelve una colección de objetos **RoleInstance** . Esta colección siempre contiene la instancia actual. Si la función no define un extremo interno, la colección incluye la instancia actual, pero ninguna otra instancia. El número de instancias de la función de la colección siempre será 1 en el caso donde no se define ningún extremo interno para la función. Si la función define un extremo interno, sus instancias son reconocibles en tiempo de ejecución y el número de instancias de la colección se corresponde con el número de instancias especificadas para el rol en el archivo de configuración de servicio.

> [AZURE.NOTE] La biblioteca de Azure administrada no proporciona una manera de determinar el estado de otras instancias de la función, pero se pueden implementar dichas evaluaciones de estado si el servicio necesita esta funcionalidad. Puede utilizar [Diagnósticos de Azure](cloud-services-dotnet-diagnostics.md) para obtener información acerca de las instancias de función.

Para determinar el número de puerto para un extremo interno en una instancia de la función, puede usar la propiedad [InstanceEndpoints](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleinstance.instanceendpoints.aspx) para devolver un objeto de diccionario que contiene los nombres de extremo y sus correspondientes direcciones IP y puertos. La propiedad [IPEndpoint](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleinstanceendpoint.ipendpoint.aspx) devuelve la dirección IP y puerto para un extremo especificado. La propiedad **PublicIPEndpoint** devuelve el puerto de un extremo de equilibrio de carga. No se utiliza la parte de la dirección IP de la propiedad **PublicIPEndpoint** .

Aquí tenemos un ejemplo que se itera las instancias de función.

```csharp
foreach (RoleInstance roleInst in RoleEnvironment.CurrentRoleInstance.Role.Instances)
{
    Trace.WriteLine("Instance ID: " + roleInst.Id);
    foreach (RoleInstanceEndpoint roleInstEndpoint in roleInst.InstanceEndpoints.Values)
    {
        Trace.WriteLine("Instance endpoint IP address and port: " + roleInstEndpoint.IPEndpoint);
    }
}
```

Aquí es un ejemplo de una función de trabajo que obtiene el extremo expone a través de la definición del servicio y se inicia la escucha de conexiones.

> [AZURE.WARNING] Este código sólo funcionará para un servicio implementado. Cuando se ejecuta en el emulador calcular de Azure, se pasan por alto los elementos de configuración de servicio que cree extremos de puerto directa (**InstanceInputEndpoint** elementos).

```csharp
using System;
using System.Diagnostics;
using System.Linq;
using System.Net;
using System.Net.Sockets;
using System.Threading;
using Microsoft.WindowsAzure;
using Microsoft.WindowsAzure.Diagnostics;
using Microsoft.WindowsAzure.ServiceRuntime;
using Microsoft.WindowsAzure.StorageClient;

namespace WorkerRole1
{
  public class WorkerRole : RoleEntryPoint
  {
    public override void Run()
    {
      try
      {
        // Initialize method-wide variables
        var epName = "Endpoint1";
        var roleInstance = RoleEnvironment.CurrentRoleInstance;
        
        // Identify direct communication port
        var myPublicEp = roleInstance.InstanceEndpoints[epName].PublicIPEndpoint;
        Trace.TraceInformation("IP:{0}, Port:{1}", myPublicEp.Address, myPublicEp.Port);

        // Identify public endpoint
        var myInternalEp = roleInstance.InstanceEndpoints[epName].IPEndpoint;
                
        // Create socket listener
        var listener = new Socket(
          myInternalEp.AddressFamily, SocketType.Stream, ProtocolType.Tcp);
                
        // Bind socket listener to internal endpoint and listen
        listener.Bind(myInternalEp);
        listener.Listen(10);
        Trace.TraceInformation("Listening on IP:{0},Port: {1}",
          myInternalEp.Address, myInternalEp.Port);

        while (true)
        {
          // Block the thread and wait for a client request
          Socket handler = listener.Accept();
          Trace.TraceInformation("Client request received.");

          // Define body of socket handler
          var handlerThread = new Thread(
            new ParameterizedThreadStart(h =>
            {
              var socket = h as Socket;
              Trace.TraceInformation("Local:{0} Remote{1}",
                socket.LocalEndPoint, socket.RemoteEndPoint);

              // Shut down and close socket
              socket.Shutdown(SocketShutdown.Both);
              socket.Close();
            }
          ));

          // Start socket handler on new thread
          handlerThread.Start(handler);
        }
      }
      catch (Exception e)
      {
        Trace.TraceError("Caught exception in run. Details: {0}", e);
      }
    }

    public override bool OnStart()
    {
      // Set the maximum number of concurrent connections 
      ServicePointManager.DefaultConnectionLimit = 12;

      // For information on handling configuration changes
      // see the MSDN topic at http://go.microsoft.com/fwlink/?LinkId=166357.
      return base.OnStart();
    }
  }
}
```

## <a name="network-traffic-rules-to-control-role-communication"></a>Reglas de tráfico de red para controlar la comunicación de función
Después de definir extremos internos, puede agregar reglas de tráfico de red (basadas en los extremos que haya creado) para controlar cómo las instancias de función pueden comunicarse con ellos. El siguiente diagrama muestra algunos escenarios comunes para controlar las comunicaciones de rol:

![Escenarios de reglas de tráfico de red] (./media/cloud-services-enable-communication-role-instances/scenarios.png "Escenarios de reglas de tráfico de red")

En el ejemplo siguiente se muestra las definiciones de función para las funciones que se muestra en el diagrama anterior. Cada definición de función incluye al menos un extremo interno definido:

```xml
<ServiceDefinition name="MyService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition">
  <WebRole name="WebRole1" vmsize="Medium">
    <Sites>
      <Site name="Web">
        <Bindings>
          <Binding name="HttpIn" endpointName="HttpIn" />
        </Bindings>
      </Site>
    </Sites>
    <Endpoints>
      <InputEndpoint name="HttpIn" protocol="http" port="80" />
      <InternalEndpoint name="InternalTCP1" protocol="tcp" />
    </Endpoints>
  </WebRole>
  <WorkerRole name="WorkerRole1">
    <Endpoints>
      <InternalEndpoint name="InternalTCP2" protocol="tcp" />
    </Endpoints>
  </WorkerRole>
  <WorkerRole name="WorkerRole2">
    <Endpoints>
      <InternalEndpoint name="InternalTCP3" protocol="tcp" />
      <InternalEndpoint name="InternalTCP4" protocol="tcp" />
    </Endpoints>
  </WorkerRole>
</ServiceDefinition>
```

> [AZURE.NOTE] Restricción de la comunicación entre funciones puede ocurrir con internos extremos de ambos fijo y puertos de asignados automáticamente.

De forma predeterminada, después de define un extremo interno, puede flujo de comunicación de función al extremo interno de una función sin restricciones. Para restringir la comunicación, debe agregar un elemento de **NetworkTrafficRules** al elemento **ServiceDefinition** en el archivo de definición de servicio.

### <a name="scenario-1"></a>Escenario 1
Permitir únicamente el tráfico de red de **WebRole1** a **WorkerRole1**.

```xml
<ServiceDefinition name="MyService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition">
  <NetworkTrafficRules>
    <OnlyAllowTrafficTo>
      <Destinations>
        <RoleEndpoint endpointName="InternalTCP2" roleName="WorkerRole1"/>
      </Destinations>
      <AllowAllTraffic/>
      <WhenSource matches="AnyRule">
        <FromRole roleName="WebRole1"/>
      </WhenSource>
    </OnlyAllowTrafficTo>
  </NetworkTrafficRules>
</ServiceDefinition>
```

### <a name="scenario-2"></a>Escenario 2
Solo permite el tráfico de red de **WebRole1** **WorkerRole1** y **WorkerRole2**.

```xml
<ServiceDefinition name="MyService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition">
  <NetworkTrafficRules>
    <OnlyAllowTrafficTo>
      <Destinations>
        <RoleEndpoint endpointName="InternalTCP2" roleName="WorkerRole1"/>
        <RoleEndpoint endpointName="InternalTCP3" roleName="WorkerRole2"/>
      </Destinations>
      <WhenSource matches="AnyRule">
        <FromRole roleName="WebRole1"/>
      </WhenSource>
    </OnlyAllowTrafficTo>
  </NetworkTrafficRules>
</ServiceDefinition>
```

### <a name="scenario-3"></a>Escenario 3
Sólo permite el tráfico de red de **WebRole1** **WorkerRole1**y **WorkerRole1** a **WorkerRole2**.

```xml
<ServiceDefinition name="MyService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition">
  <NetworkTrafficRules>
    <OnlyAllowTrafficTo>
      <Destinations>
        <RoleEndpoint endpointName="InternalTCP2" roleName="WorkerRole1"/>
      </Destinations>
      <AllowAllTraffic/>
      <WhenSource matches="AnyRule">
        <FromRole roleName="WebRole1"/>
      </WhenSource>
    </OnlyAllowTrafficTo>
  </NetworkTrafficRules>
  <NetworkTrafficRules>
    <OnlyAllowTrafficTo>
      <Destinations>
        <RoleEndpoint endpointName="InternalTCP3" roleName="WorkerRole2"/>
      </Destinations>
      <WhenSource matches="AnyRule">
        <FromRole roleName="WorkerRole1"/>
      </WhenSource>
    </OnlyAllowTrafficTo>
  </NetworkTrafficRules>
</ServiceDefinition>
```

### <a name="scenario-4"></a>Escenario 4
Sólo permite el tráfico de red de **WebRole1** **WorkerRole1**, **WebRole1** a **WorkerRole2**y **WorkerRole1** a **WorkerRole2**.

```xml
<ServiceDefinition name="MyService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition">
  <NetworkTrafficRules>
    <OnlyAllowTrafficTo>
      <Destinations>
        <RoleEndpoint endpointName="InternalTCP2" roleName="WorkerRole1"/>
      </Destinations>
      <AllowAllTraffic/>
      <WhenSource matches="AnyRule">
        <FromRole roleName="WebRole1"/>
      </WhenSource>
    </OnlyAllowTrafficTo>
  </NetworkTrafficRules>
  <NetworkTrafficRules>
    <OnlyAllowTrafficTo >
      <Destinations>
        <RoleEndpoint endpointName="InternalTCP3" roleName="WorkerRole2"/>
      </Destinations>
      <AllowAllTraffic/>
      <WhenSource matches="AnyRule">
        <FromRole roleName="WorkerRole1"/>
      </WhenSource>
    </OnlyAllowTrafficTo>
  </NetworkTrafficRules>
  <NetworkTrafficRules>
    <OnlyAllowTrafficTo >
      <Destinations>
        <RoleEndpoint endpointName="InternalTCP4" roleName="WorkerRole2"/>
      </Destinations>
      <AllowAllTraffic/>
      <WhenSource matches="AnyRule">
        <FromRole roleName="WebRole1"/>
      </WhenSource>
    </OnlyAllowTrafficTo>
  </NetworkTrafficRules>
</ServiceDefinition>
```

Puede encontrar una referencia de esquema XML para los elementos utilizados anteriormente [aquí](https://msdn.microsoft.com/library/azure/gg557551.aspx).

## <a name="next-steps"></a>Pasos siguientes
Obtenga más información acerca del [modelo](cloud-services-model-and-package.md)de servicio de nube.