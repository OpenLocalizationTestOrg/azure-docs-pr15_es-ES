<properties
    pageTitle="Cómo utilizar diagnóstico de Azure (. NET) con servicios de nube | Microsoft Azure"
    description="Uso de diagnósticos de Azure para recopilar datos de Azure cloud Services para depuración, medir el rendimiento, supervisión, análisis del tráfico y mucho más."
    services="cloud-services"
    documentationCenter=".net"
    authors="rboucher"
    manager="jwhit"
    editor=""/>

<tags
    ms.service="cloud-services"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="01/25/2016"
    ms.author="robb"/>



# <a name="enabling-azure-diagnostics-in-azure-cloud-services"></a>Habilitar diagnósticos de Azure en servicios de nube de Azure

Consulte [Introducción a Azure diagnósticos](../azure-diagnostics.md) de un fondo de diagnósticos de Azure.


## <a name="how-to-enable-diagnostics-in-a-worker-role"></a>Cómo habilitar diagnósticos de una función de trabajo

Esta información básica, describe cómo implementar una función de trabajo de Azure que emite datos de telemetría mediante la clase de origen de eventos de .NET. Diagnóstico de Azure se usa para recopilar los datos de telemetría y guardarlo en una cuenta de almacenamiento de Azure. Al crear una función de trabajador Visual Studio permite automáticamente diagnósticos 1.0 como parte de la solución en Azure SDK para .NET 2,4 y versiones anteriores. Las instrucciones siguientes describen el proceso de creación de la función de trabajo, deshabilitar 1.0 de diagnósticos de la solución e implementar diagnósticos 1.2 o 1.3 a su rol de trabajo.

### <a name="pre-requisites"></a>Requisitos previos
En este artículo se supone que tiene una suscripción de Azure y usa 2013 de Visual Studio con el SDK de Azure. Si no tiene una suscripción de Azure, puede suscribirse a la [Versión de prueba gratuita][]. Asegúrese de que esté [instalar y configurar Azure PowerShell versión 0.8.7 o posterior][].

### <a name="step-1-create-a-worker-role"></a>Paso 1: Crear una función de trabajo
1.  Inicie **Visual Studio de 2013**.
2.  Crear un nuevo proyecto de **Servicio de nube de Azure** desde la plantilla de **nube** destinada a .NET Framework 4.5.  Nombre del proyecto "WadExample" y haga clic en Aceptar.
3.  Seleccione **El rol de trabajo** y haga clic en Aceptar. Se creará el proyecto.
4.  En el **Explorador de soluciones**, haga doble clic en el archivo de propiedades de **WorkerRole1** .
5.  En la **configuración** de la ficha desactive **Habilitar diagnósticos** para deshabilitar diagnósticos 1.0 (Azure SDK 2,4 y eariler).
6.  Genere su solución para comprobar que no tiene errores.

### <a name="step-2-instrument-your-code"></a>Paso 2: Implementarlo
Reemplazar el contenido de WorkerRole.cs con el código siguiente. La clase SampleEventSourceWriter, que se heredan de la [Clase de origen de eventos][], implementa cuatro métodos de registro: **SendEnums**, **MessageMethod**, **SetOther** y **HighFreq**. El primer parámetro al método **WriteEvent** define el identificador del evento respectivo. El método de ejecución implementa un bucle que llama a cada uno de los métodos de registro que se ha implementado en la clase **SampleEventSourceWriter** cada 10 segundos.

    using Microsoft.WindowsAzure.ServiceRuntime;
    using System;
    using System.Diagnostics;
    using System.Diagnostics.Tracing;
    using System.Net;
    using System.Threading;

    namespace WorkerRole1
    {
    sealed class SampleEventSourceWriter : EventSource
    {
        public static SampleEventSourceWriter Log = new SampleEventSourceWriter();
        public void SendEnums(MyColor color, MyFlags flags) { if (IsEnabled())  WriteEvent(1, (int)color, (int)flags); }// Cast enums to int for efficient logging.
        public void MessageMethod(string Message) { if (IsEnabled())  WriteEvent(2, Message); }
        public void SetOther(bool flag, int myInt) { if (IsEnabled())  WriteEvent(3, flag, myInt); }
        public void HighFreq(int value) { if (IsEnabled()) WriteEvent(4, value); }

    }

    enum MyColor
    {
        Red,
        Blue,
        Green
    }

    [Flags]
    enum MyFlags
    {
        Flag1 = 1,
        Flag2 = 2,
        Flag3 = 4
    }

    public class WorkerRole : RoleEntryPoint
    {
        public override void Run()
        {
            // This is a sample worker implementation. Replace with your logic.
            Trace.TraceInformation("WorkerRole1 entry point called");

            int value = 0;

            while (true)
            {
                Thread.Sleep(10000);
                Trace.TraceInformation("Working");

                // Emit several events every time we go through the loop
                for (int i = 0; i < 6; i++)
                {
                    SampleEventSourceWriter.Log.SendEnums(MyColor.Blue, MyFlags.Flag2 | MyFlags.Flag3);
                }

                for (int i = 0; i < 3; i++)
                {
                    SampleEventSourceWriter.Log.MessageMethod("This is a message.");
                    SampleEventSourceWriter.Log.SetOther(true, 123456789);
                }

                if (value == int.MaxValue) value = 0;
                SampleEventSourceWriter.Log.HighFreq(value++);
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


### <a name="step-3-deploy-your-worker-role"></a>Paso 3: Implementar su rol de trabajo
1.  Implementar su rol de trabajo en Azure desde dentro de Visual Studio, seleccione el proyecto **WadExample** en el Explorador de soluciones, a continuación, **Publicar** en el menú **Generar** .
2.  Elija la suscripción.
3.  En el cuadro de diálogo **Configuración de publicación de Microsoft Azure** , seleccione **Crear nuevo...**.
4.  En el cuadro de diálogo **Crear servicio de nube y cuenta de almacenamiento** , escriba un **nombre** (por ejemplo, "WadExample") y seleccione un grupo de afinidad o la región.
5.  Establecer el **entorno de** **prueba**.
6.  Modificar otra **configuración** según corresponda y haga clic en **Publicar**.
7.  Una vez completada la implementación Compruebe en el portal de clásico Azure que su servicio de nube se encuentra en un estado de **ejecución** .

### <a name="step-4-create-your-diagnostics-configuration-file-and-install-the-extension"></a>Paso 4: Crear el archivo de configuración de diagnósticos e instale la extensión
1.  Descargar la definición de esquema del archivo de configuración público, ejecute el siguiente comando PowerShell:
2.
        (AzureServiceAvailableExtension get - NombreExtensión 'PaaSDiagnostics' - ProviderNamespace 'Microsoft.Azure.Diagnostics'). PublicConfigurationSchema | Out-File-codificación utf8 - ruta del archivo 'WadConfig.xsd'

2.  Agregar un archivo XML a un proyecto **WorkerRole1** con el botón secundario en el proyecto **WorkerRole1** y seleccione **Agregar** -> **Nuevo elemento...**  ->  **Elementos visual C#** -> **datos** -> **Archivo XML**. Nombre del archivo "WadExample.xml".

    ![CloudServices_diag_add_xml](./media/cloud-services-dotnet-diagnostics/AddXmlFile.png)

3.  Asociar la WadConfig.xsd con el archivo de configuración. Asegúrese de que la ventana del editor de WadExample.xml es la ventana activa. Presione **F4** para abrir la ventana de **Propiedades** . Haga clic en la propiedad de **esquemas** en la ventana **Propiedades** . Haga clic en **...** en la propiedad de **esquemas** . Haga clic en el **Agregar...** botón y navegue hasta la ubicación donde guardó el archivo XSD y seleccione el archivo WadConfig.xsd. Haga clic en **Aceptar**.
4.  Reemplace el contenido del archivo de configuración de WadExample.xml con el siguiente código XML y guarde el archivo. Este archivo de configuración define un par contadores de rendimiento para recopilar: uno para la CPU y otro para el uso de la memoria. A continuación, la configuración define los cuatro eventos correspondientes a los métodos de la clase SampleEventSourceWriter.

```
        <?xml version="1.0" encoding="utf-8"?>
        <PublicConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
            <WadCfg>
                <DiagnosticMonitorConfiguration overallQuotaInMB="25000">
                <PerformanceCounters scheduledTransferPeriod="PT1M">
                    <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT1M" unit="percent" />
                    <PerformanceCounterConfiguration counterSpecifier="\Memory\Committed Bytes" sampleRate="PT1M" unit="bytes"/>
                    </PerformanceCounters>
                    <EtwProviders>
                        <EtwEventSourceProviderConfiguration provider="SampleEventSourceWriter" scheduledTransferPeriod="PT5M">
                            <Event id="1" eventDestination="EnumsTable"/>
                            <Event id="2" eventDestination="MessageTable"/>
                            <Event id="3" eventDestination="SetOtherTable"/>
                            <Event id="4" eventDestination="HighFreqTable"/>
                            <DefaultEvents eventDestination="DefaultTable" />
                        </EtwEventSourceProviderConfiguration>
                    </EtwProviders>
                </DiagnosticMonitorConfiguration>
            </WadCfg>
        </PublicConfig>
```

### <a name="step-5-install-diagnostics-on-your-worker-role"></a>Paso 5: Instalar diagnósticos en su rol de trabajo
Los cmdlets de PowerShell para administrar diagnósticos en una función web o trabajo son: conjunto AzureServiceDiagnosticsExtension, Get-AzureServiceDiagnosticsExtension y quitar AzureServiceDiagnosticsExtension.

1.  Abra el PowerShell Azure.
2.  Ejecute la secuencia de comandos para instalar diagnósticos en su rol de trabajo (reemplazar *StorageAccountKey* con la clave de cuenta de almacenamiento para su cuenta de almacenamiento wadexample):

```
    $storage_name = "wadexample"
    $key = "<StorageAccountKey>"
    $config_path="c:\users\<user>\documents\visual studio 2013\Projects\WadExample\WorkerRole1\WadExample.xml"
    $service_name="wadexample"
    $storageContext = New-AzureStorageContext -StorageAccountName $storage_name -StorageAccountKey $key
    Set-AzureServiceDiagnosticsExtension -StorageContext $storageContext -DiagnosticsConfigurationPath $config_path -ServiceName $service_name -Slot Staging -Role WorkerRole1
```

### <a name="step-6-look-at-your-telemetry-data"></a>Paso 6: Ver los datos de telemetría
En el **Explorador de servidores** de Visual Studio vaya a la cuenta de almacenamiento de wadexample. Después de la nube servicio ejecutando aproximadamente 5 minutos, debería ver las tablas **WADEnumsTable**, **WADHighFreqTable**, **WADMessageTable**, **WADPerformanceCountersTable** y **WADSetOtherTable**. Haga doble clic en una de las tablas para ver la telemetría que ha recopilado.
    ![CloudServices_diag_tables](./media/cloud-services-dotnet-diagnostics/WadExampleTables.png)


## <a name="configuration-file-schema"></a>Esquema de archivo de configuración

El archivo de configuración de diagnósticos define los valores que se usan iniciar configuración de diagnóstico cuando se inicia el agente de diagnóstico. Vea la [referencia de esquema más reciente](https://msdn.microsoft.com/library/azure/mt634524.aspx) para valores válidos y ejemplos.

## <a name="troubleshooting"></a>Solución de problemas

Si tiene problemas, vea [Solución de problemas de diagnósticos de Azure](../azure-diagnostics-troubleshooting.md) para obtener ayuda con los problemas más comunes.

## <a name="next-steps"></a>Pasos siguientes
[Vea una lista de máquina virtual relacionados con los artículos de diagnóstico de Azure](azure-diagnostics.md#cloud-services) para cambiar los datos que se están recopilando, solucionar problemas o más información sobre diagnósticos en general.


[Clase de origen de eventos]: http://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource(v=vs.110).aspx

[Debugging an Azure Application]: http://msdn.microsoft.com/library/windowsazure/ee405479.aspx   
[Collect Logging Data by Using Azure Diagnostics]: http://msdn.microsoft.com/library/windowsazure/gg433048.aspx
[Versión de prueba gratuita]: http://azure.microsoft.com/pricing/free-trial/
[Instalar y configurar Azure PowerShell versión 0.8.7 o posterior]: http://azure.microsoft.com/documentation/articles/install-configure-powershell/
