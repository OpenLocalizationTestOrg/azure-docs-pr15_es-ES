<properties
    pageTitle="Transmisión de datos de diagnóstico de Azure en la ruta de acceso rápida con el evento Hubs | Microsoft Azure"
    description="Muestra cómo configurar diagnósticos de Azure con evento Hubs de principio a fin, incluidos pautas para escenarios comunes."
    services="event-hubs"
    documentationCenter="na"
    authors="sethmanheim"
    manager="timlt"
    editor="" />
<tags
    ms.service="event-hubs"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="na"
    ms.date="07/14/2016"
    ms.author="sethm" />

# <a name="streaming-azure-diagnostics-data-in-the-hot-path-by-using-event-hubs"></a>Transmisión de datos de diagnóstico de Azure en la ruta de acceso rápida mediante Hubs de evento

Diagnóstico de Azure proporciona maneras flexibles recopilar métricas y los registros de máquinas virtuales de servicios nube (VM) y transferir los resultados al almacenamiento de Azure. Comenzando en el período de tiempo de marzo de 2016 (SDK 2,9), puede receptor de diagnósticos a orígenes de datos totalmente personalizado y transferir datos de la ruta de acceso rápido en segundos mediante [Hubs de evento de Azure](https://azure.microsoft.com/services/event-hubs/).

Tipos de datos compatibles son:

- Eventos de seguimiento para Windows (ETW)
- Contadores de rendimiento
- Registros de eventos de Windows
- Registros de aplicación
- Registros de infraestructura de diagnósticos de Azure

En este artículo se muestra cómo configurar diagnósticos de Azure con evento Hubs de principio a fin. También se ofrecen instrucciones para los siguientes escenarios comunes:

- Cómo personalizar los registros y métricas que obtener sinked a Hubs de evento
- Cómo cambiar las configuraciones de cada entorno
- Cómo ver los datos de la secuencia de evento Hubs
- Cómo solucionar problemas de la conexión  

## <a name="prerequisites"></a>Requisitos previos

Hubs evento recepción en diagnóstico de Azure es compatible con servicios de nube, máquinas virtuales, conjuntos de escala de máquina Virtual y tela de servicio para Visual Studio a partir del 2,9 SDK de Azure y las herramientas de Azure correspondiente.

- Extensión de diagnósticos Azure 1,6 ([Azure SDK para .NET 2,9 o posterior](https://azure.microsoft.com/downloads/) destino esto de forma predeterminada)
- [Visual Studio 2013 o posterior](https://www.visualstudio.com/downloads/download-visual-studio-vs.aspx)
- Configuraciones existentes de diagnósticos de Azure en una aplicación mediante un archivo de *.wadcfgx* y uno de los métodos siguientes:
    - Visual Studio: [configuración de diagnósticos para servicios de nube de Azure y máquinas virtuales](../vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines.md)
    - Windows PowerShell: [Habilitar diagnósticos en servicios de nube de Azure con PowerShell](../cloud-services/cloud-services-diagnostics-powershell.md)
- Espacio de nombres de evento Hubs aprovisionado por el artículo [Introducción a Hubs de evento](./event-hubs-csharp-ephcs-getstarted.md)

## <a name="connect-azure-diagnostics-to-event-hubs-sink"></a>Conectar diagnósticos de Azure al receptor de evento Hubs

Diagnóstico de Azure siempre los receptores de registros y métricas, de forma predeterminada, una cuenta de almacenamiento de Azure. Una aplicación puede receptor además para Hubs evento agregando una nueva sección **receptores** al elemento **WadCfg** en la sección **PublicConfig** del archivo *.wadcfgx* . En Visual Studio, el archivo *.wadcfgx* se almacena en la siguiente ruta: **Proyecto de servicio de nube** > **Roles** >  **(nombre de rol)** > **diagnostics.wadcfgx** archivo.

```
<SinksConfig>
  <Sink name="HotPath">
    <EventHub Url="https://diags-mycompany-ns.servicebus.windows.net/diageventhub" SharedAccessKeyName="SendRule" />
  </Sink>
</SinksConfig>
```

En este ejemplo, se establece la dirección URL de concentrador de evento en el espacio de nombres completo del concentrador de evento: espacio de nombres de evento Hubs + "/" + nombre del concentrador de evento.  

La dirección URL de concentrador de eventos se muestra en el [portal de Azure](http://go.microsoft.com/fwlink/?LinkID=213885) en el panel de Hubs de evento.  

El nombre de **receptor** se puede establecer en cualquier cadena válida como el mismo valor se usa siempre en el archivo de configuración.

> [AZURE.NOTE]  Puede haber receptores adicionales, como *applicationInsights* configurado en esta sección. Diagnóstico de Azure permite uno o más receptores definirse si cada receptor también se declara en la sección **PrivateConfig** .  

El receptor de evento Hubs también debe ser declarado y definido en la sección **PrivateConfig** *.wadcfgx* archivo de configuración.

```
<PrivateConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
  <StorageAccount name="" key="" endpoint="" />
  <EventHub Url="https://diags-mycompany-ns.servicebus.windows.net/diageventhub" SharedAccessKeyName="SendRule" SharedAccessKey="9B3SwghJOGEUvXigc6zHPInl2iYxrgsKHZoy4nm9CUI=" />
</PrivateConfig>
```

La `SharedAccessKeyName` valor debe coincidir con una clave de firma de acceso compartido (SA) y la directiva que se ha definido en el espacio de nombres de **Evento Hubs** . Vaya a panel Hubs de evento en el [portal de Azure](https://manage.windowsazure.com), haga clic en la ficha **Configurar** y configurar una directiva con nombre (por ejemplo, "SendRule") que tiene permisos de *envío* . El **StorageAccount** también se declara en **PrivateConfig**. No es necesario cambiar estos valores si están trabajando. En este ejemplo, hemos deje los valores vacío, que es un inicio de sesión que un activo descendente establecerá los valores. Por ejemplo, el archivo de configuración de entorno *ServiceConfiguration.Cloud.cscfg* establece las teclas y nombres de entorno apropiados.  

> [AZURE.WARNING] La clave de evento Hubs SA se almacena en texto sin formato en el archivo *.wadcfgx* . A menudo, esta clave está desprotegida control de código fuente o está disponible como un recurso en el servidor de compilación, por lo que debe proteger según corresponda. Se recomienda usar una clave de SA aquí con permisos *Enviar solo* con el que un usuario malintencionado puede escribir en el Hub de evento, pero no escuchar a él o administrarlo.

## <a name="configure-azure-diagnostics-logs-and-metrics-to-sink-with-event-hubs"></a>Configurar los registros de diagnósticos de Azure y métricas para recibir con Hubs de evento

Como se indicó anteriormente, todos los datos de diagnóstico personalizado y predeterminado, es decir, métricas y registros, sinked automáticamente al almacenamiento de Azure en los intervalos configurados. Con Hubs de evento y cualquier receptor adicional, puede especificar cualquier nodo de hoja o raíz de la jerarquía de ser sinked con el concentrador de eventos. Esto incluye los eventos ETW, contadores de rendimiento, registros de eventos de Windows y registros de la aplicación.   

Es importante tener en cuenta el número de puntos de datos se transferirán realmente a Hubs de evento. Normalmente, los desarrolladores transfieren datos de la ruta de acceso rápido de baja latencia que deben usar e interpreta rápidamente. Sistemas que supervisión alertas o Autoescala reglas son ejemplos. Un desarrollador también configurar un almacén de análisis alternativo o buscar en tienda: por ejemplo, el análisis de secuencia de Azure, Elasticsearch, un sistema de supervisión personalizado o un sistema de supervisión favorito de otras personas.

Las siguientes son algunas configuraciones de ejemplo.

```
<PerformanceCounters scheduledTransferPeriod="PT1M" sinks="HotPath">
  <PerformanceCounterConfiguration counterSpecifier="\Memory\Available MBytes" sampleRate="PT3M" />
  <PerformanceCounterConfiguration counterSpecifier="\Web Service(_Total)\ISAPI Extension Requests/sec" sampleRate="PT3M" />
  <PerformanceCounterConfiguration counterSpecifier="\Web Service(_Total)\Bytes Total/Sec" sampleRate="PT3M" />
  <PerformanceCounterConfiguration counterSpecifier="\ASP.NET Applications(__Total__)\Requests/Sec" sampleRate="PT3M" />
  <PerformanceCounterConfiguration counterSpecifier="\ASP.NET Applications(__Total__)\Errors Total/Sec" sampleRate="PT3M" />
  <PerformanceCounterConfiguration counterSpecifier="\ASP.NET\Requests Queued" sampleRate="PT3M" />
  <PerformanceCounterConfiguration counterSpecifier="\ASP.NET\Requests Rejected" sampleRate="PT3M" />
  <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT3M" />
</PerformanceCounters>
```

En el ejemplo siguiente, el receptor se aplica al nodo **PerformanceCounters** primario en la jerarquía, lo que significa que todos los secundarios **PerformanceCounters** se enviarán a Hubs de evento.  

```
<PerformanceCounters scheduledTransferPeriod="PT1M">
  <PerformanceCounterConfiguration counterSpecifier="\Memory\Available MBytes" sampleRate="PT3M" />
  <PerformanceCounterConfiguration counterSpecifier="\Web Service(_Total)\ISAPI Extension Requests/sec" sampleRate="PT3M" />
  <PerformanceCounterConfiguration counterSpecifier="\Web Service(_Total)\Bytes Total/Sec" sampleRate="PT3M" />
  <PerformanceCounterConfiguration counterSpecifier="\ASP.NET Applications(__Total__)\Requests/Sec" sampleRate="PT3M" />
  <PerformanceCounterConfiguration counterSpecifier="\ASP.NET Applications(__Total__)\Errors Total/Sec" sampleRate="PT3M" />
  <PerformanceCounterConfiguration counterSpecifier="\ASP.NET\Requests Queued" sampleRate="PT3M" sinks="HotPath" />
  <PerformanceCounterConfiguration counterSpecifier="\ASP.NET\Requests Rejected" sampleRate="PT3M" sinks="HotPath"/>
  <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT3M" sinks="HotPath"/>
</PerformanceCounters>
```

En el ejemplo anterior, se aplica el receptor a solo tres contadores: **Solicitudes en cola**, **Las solicitudes rechazadas**y **% tiempo de procesador**.  

En el ejemplo siguiente se muestra cómo un desarrollador puede limitar la cantidad de datos enviados las métricas críticas que se usan para el estado de este servicio.  

```
<Logs scheduledTransferPeriod="PT1M" sinks="HotPath" scheduledTransferLogLevelFilter="Error" />
```

En este ejemplo, el receptor se aplica a los registros y se filtra solo para el seguimiento de nivel de error.

## <a name="deploy-and-update-a-cloud-services-application-and-diagnostics-config"></a>Implementar y actualizar una configuración de aplicación y diagnósticos de servicios en la nube

Visual Studio proporciona la ruta de acceso más sencillo para implementar la aplicación y la configuración de receptor de evento Hubs. Para ver y editar el archivo, abra el archivo *.wadcfgx* en Visual Studio, editarlo y guardarlo. La ruta es el **Proyecto de servicio de nube** > **Roles** > **(nombre de rol)** > **diagnostics.wadcfgx**.  

En este momento, todos e implementación actualizan acciones en Visual Studio, Visual Studio Team System y todos los comandos o las secuencias de comandos que se basan en MSBuild y usar la **/t: publicar** destino incluir la *.wadcfgx* en el proceso de empaquetado. Además, implementaciones y actualizaciones implementación el archivo en Azure mediante la extensión de agente de diagnósticos de Azure adecuada en sus máquinas virtuales.

Después de implementar la aplicación y la configuración de diagnósticos de Azure, verán inmediatamente actividad en el panel del concentrador de evento. Esto indica que está listo para pasar a ver los datos de la ruta de acceso rápido en la herramienta de análisis o el cliente de escucha de su elección.  

En la siguiente ilustración, el panel de evento Hubs muestra correcto el envío de datos de diagnóstico para el concentrador de eventos comenzando en algún momento después 11 PM. Esto sucede cuando la aplicación se ha implementado con un archivo actualizado *.wadcfgx* y el receptor se ha configurado correctamente.

![][0]  

> [AZURE.NOTE] Al realizar actualizaciones en el archivo de configuración de diagnósticos de Azure (.wadcfgx), se recomienda insertar las actualizaciones a toda la aplicación, así como la configuración mediante la publicación de Visual Studio o un script de Windows PowerShell.  

## <a name="view-hot-path-data"></a>Ver datos de la ruta de acceso rápido

Como se indicó anteriormente, hay muchos casos de uso para escuchar y procesar datos Hubs de evento.

Un enfoque simple es crear una aplicación de consola de prueba pequeña para escuchar el Hub de evento e imprimir la secuencia de salida. Puede colocar el código siguiente, que se explica detalladamente en [Introducción a evento Hubs](./event-hubs-csharp-ephcs-getstarted.md)), en una aplicación de consola.  

Observe que la aplicación de consola debe incluir el [paquete evento procesador Host Nuget](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost/).  

No olvide reemplazar los valores de corchetes angulares en la función **Main** con valores para los recursos.   

```
//Console application code for EventHub test client
using System;
using System.Collections.Generic;
using System.Diagnostics;
using System.Linq;
using System.Text;
using System.Threading.Tasks;
using Microsoft.ServiceBus.Messaging;

namespace EventHubListener
{
    class SimpleEventProcessor : IEventProcessor
    {
        Stopwatch checkpointStopWatch;

        async Task IEventProcessor.CloseAsync(PartitionContext context, CloseReason reason)
        {
            Console.WriteLine("Processor Shutting Down. Partition '{0}', Reason: '{1}'.", context.Lease.PartitionId, reason);
            if (reason == CloseReason.Shutdown)
            {
                await context.CheckpointAsync();
            }
        }

        Task IEventProcessor.OpenAsync(PartitionContext context)
        {
            Console.WriteLine("SimpleEventProcessor initialized.  Partition: '{0}', Offset: '{1}'", context.Lease.PartitionId, context.Lease.Offset);
            this.checkpointStopWatch = new Stopwatch();
            this.checkpointStopWatch.Start();
            return Task.FromResult<object>(null);
        }

        async Task IEventProcessor.ProcessEventsAsync(PartitionContext context, IEnumerable<EventData> messages)
        {
            foreach (EventData eventData in messages)
            {
                string data = Encoding.UTF8.GetString(eventData.GetBytes());
                    Console.WriteLine(string.Format("Message received.  Partition: '{0}', Data: '{1}'",
                        context.Lease.PartitionId, data));

                foreach (var x in eventData.Properties)
                {
                    Console.WriteLine(string.Format("    {0} = {1}", x.Key, x.Value));
                }
            }

            //Call checkpoint every 5 minutes, so that worker can resume processing from 5 minutes back if it restarts.
            if (this.checkpointStopWatch.Elapsed > TimeSpan.FromMinutes(5))
            {
                await context.CheckpointAsync();
                this.checkpointStopWatch.Restart();
            }
        }
    }

    class Program
    {
        static void Main(string[] args)
        {
            string eventHubConnectionString = "Endpoint= <your connection string>”
            string eventHubName = "<Event Hub name>";
            string storageAccountName = "<Storage account name>";
            string storageAccountKey = "<Storage account key>”;
            string storageConnectionString = string.Format("DefaultEndpointsProtocol=https;AccountName={0};AccountKey={1}", storageAccountName, storageAccountKey);

            string eventProcessorHostName = Guid.NewGuid().ToString();
            EventProcessorHost eventProcessorHost = new EventProcessorHost(eventProcessorHostName, eventHubName, EventHubConsumerGroup.DefaultGroupName, eventHubConnectionString, storageConnectionString);
            Console.WriteLine("Registering EventProcessor...");
            var options = new EventProcessorOptions();
            options.ExceptionReceived += (sender, e) => { Console.WriteLine(e.Exception); };
            eventProcessorHost.RegisterEventProcessorAsync<SimpleEventProcessor>(options).Wait();

            Console.WriteLine("Receiving. Press enter key to stop worker.");
            Console.ReadLine();
            eventProcessorHost.UnregisterEventProcessorAsync().Wait();
        }
    }
}
```

## <a name="troubleshoot-event-hubs-sink"></a>Solucionar problemas de receptores de eventos Hubs

- El concentrador de eventos no muestra la actividad de evento entrantes y salientes como se esperaba.

    Compruebe que su centro de evento aprovisionado correctamente. Toda la información de conexión en la sección de **PrivateConfig** de *.wadcfgx* debe coincidir con los valores de los recursos, tal como se muestra en el portal. Asegúrese de que dispone de una directiva de SA ("SendRule" en el ejemplo) en el portal y que se ha concedido permisos *Enviar* .  

- Después de una actualización, el concentrador de eventos ya no se muestra la actividad de evento entrantes y salientes.

    En primer lugar, asegúrese de que la información de concentrador de evento y la configuración es correcta, como se explica anteriormente. A veces se restablece el **PrivateConfig** en una actualización de la implementación. La solución recomendada es realizar todos los cambios a *.wadcfgx* en el proyecto y, a continuación, insertar una actualización de la aplicación completa. Si esto no es posible, asegúrese de que la actualización de diagnósticos inserta un completo **PrivateConfig** que incluye la clave de SA.  

- Intenté las sugerencias y el Hub de evento sigue sin funcionar.

    Intente buscar en la tabla de almacenamiento de Azure que contiene errores y registros de diagnósticos de Azure propio: **WADDiagnosticInfrastructureLogsTable**. Una opción es usar una herramienta como [El Explorador de almacenamiento de Azure](http://www.storageexplorer.com) para conectarse a esta cuenta de almacenamiento, ver esta tabla y agregar una consulta de marca de tiempo en las últimas 24 horas. Puede usar la herramienta para exportar un archivo .csv y ábralo en una aplicación como Microsoft Excel. Excel facilita la busca cadenas de tarjeta de llamada, como **EventHubs**, para ver qué error se notifica.  

## <a name="next-steps"></a>Pasos siguientes

• [Más información sobre el evento Hubs](https://azure.microsoft.com/services/event-hubs/)

## <a name="appendix-complete-azure-diagnostics-configuration-file-wadcfgx-example"></a>Apéndice: Ejemplo de archivo (.wadcfgx) de configuración de diagnósticos de Azure completo

```
<?xml version="1.0" encoding="utf-8"?>
<DiagnosticsConfiguration xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
  <PublicConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
    <WadCfg>
      <DiagnosticMonitorConfiguration overallQuotaInMB="4096" sinks="applicationInsights.errors">
        <DiagnosticInfrastructureLogs scheduledTransferLogLevelFilter="Error" />
        <Directories scheduledTransferPeriod="PT1M">
          <IISLogs containerName="wad-iis-logfiles" />
          <FailedRequestLogs containerName="wad-failedrequestlogs" />
        </Directories>
        <PerformanceCounters scheduledTransferPeriod="PT1M" sinks="HotPath">
          <PerformanceCounterConfiguration counterSpecifier="\Memory\Available MBytes" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\Web Service(_Total)\ISAPI Extension Requests/sec" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\Web Service(_Total)\Bytes Total/Sec" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\ASP.NET Applications(__Total__)\Requests/Sec" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\ASP.NET Applications(__Total__)\Errors Total/Sec" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\ASP.NET\Requests Queued" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\ASP.NET\Requests Rejected" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT3M" />
        </PerformanceCounters>
        <WindowsEventLog scheduledTransferPeriod="PT1M">
          <DataSource name="Application!*" />
        </WindowsEventLog>
        <CrashDumps>
          <CrashDumpConfiguration processName="WaIISHost.exe" />
          <CrashDumpConfiguration processName="WaWorkerHost.exe" />
          <CrashDumpConfiguration processName="w3wp.exe" />
        </CrashDumps>
        <Logs scheduledTransferPeriod="PT1M" sinks="HotPath" scheduledTransferLogLevelFilter="Error" />
      </DiagnosticMonitorConfiguration>
      <SinksConfig>
        <Sink name="HotPath">
          <EventHub Url="https://diageventhub-py-ns.servicebus.windows.net/diageventhub-py" SharedAccessKeyName="SendRule" />
        </Sink>
        <Sink name="applicationInsights">
          <ApplicationInsights />
          <Channels>
            <Channel logLevel="Error" name="errors" />
          </Channels>
        </Sink>
      </SinksConfig>
    </WadCfg>
    <StorageAccount />
  </PublicConfig>
  <PrivateConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
    <StorageAccount name="" key="" endpoint="" />
    <EventHub Url="https://diageventhub-py-ns.servicebus.windows.net/diageventhub-py" SharedAccessKeyName="SendRule" SharedAccessKey="YOUR_KEY_HERE" />
  </PrivateConfig>
  <IsEnabled>true</IsEnabled>
</DiagnosticsConfiguration>
```

La complementaria *ServiceConfiguration.Cloud.cscfg* en este ejemplo es similar a la siguiente.

```
<?xml version="1.0" encoding="utf-8"?>
<ServiceConfiguration serviceName="MyFixItCloudService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceConfiguration" osFamily="3" osVersion="*" schemaVersion="2015-04.2.6">
  <Role name="MyFixIt.WorkerRole">
    <Instances count="1" />
    <ConfigurationSettings>
      <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="YOUR_CONNECTION_STRING" />
    </ConfigurationSettings>
  </Role>
</ServiceConfiguration>
```

<!-- Images. -->
[0]: ./media/event-hubs-streaming-azure-diags-data/dashboard.png
