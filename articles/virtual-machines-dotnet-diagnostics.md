<properties
    pageTitle="Cómo usar diagnóstico de Azure en máquinas virtuales | Microsoft Azure"
    description="Uso de diagnósticos de Azure para recopilar datos de Azure máquinas virtuales de Windows para depuración, medir el rendimiento, supervisión, análisis del tráfico y mucho más."
    services="virtual-machines"
    documentationCenter=".net"
    authors="rboucher"
    manager="jwhit"
    editor=""/>

<tags
    ms.service="virtual-machines"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="02/20/2016"
    ms.author="robb"/>



# <a name="enabling-diagnostics-in-azure-virtual-machines"></a>Habilitar diagnósticos en máquinas virtuales de Windows Azure

Consulte [Introducción a Azure diagnósticos](azure-diagnostics.md) de un fondo de diagnósticos de Azure.

## <a name="how-to-enable-diagnostics-in-a-virtual-machine"></a>Cómo habilitar diagnósticos en una máquina Virtual

Esta información básica describe cómo instalar diagnósticos de forma remota a una máquina virtual Azure desde un equipo de desarrollo. También aprenderá a implementar una aplicación que se ejecuta en Azure máquina virtual y emite datos de telemetría mediante la [Clase de origen de eventos][]. Diagnóstico de Azure se usa para recopilar la telemetría y guardarlo en una cuenta de almacenamiento de Azure.

### <a name="pre-requisites"></a>Requisitos previos
Esta información básica se supone que tiene una suscripción de Azure y usa 2013 de Visual Studio con el SDK de Azure. Si no tiene una suscripción de Azure, puede suscribirse a la [Versión de prueba gratuita][]. Asegúrese de que esté [instalar y configurar Azure PowerShell versión 0.8.7 o posterior][].

### <a name="step-1-create-a-virtual-machine"></a>Paso 1: Crear una máquina Virtual
1.  En el equipo de desarrollo, inicie Visual Studio de 2013.
2.  En el **Explorador de servidores** de Visual Studio expandir **Azure**, haga **máquinas virtuales** y **Máquina Virtual de crear**.
3.  Seleccione la suscripción de Azure en el cuadro de diálogo **Elegir una suscripción** y haga clic en **siguiente**.
4.  Seleccione el **Centro de datos de Windows Server 2012 R2, noviembre de 2014** en el cuadro de diálogo **Seleccionar una imagen de máquina Virtual** y haga clic en **siguiente**.
5.  En la **Configuración básica de una máquina Virtual**, establezca el nombre de la máquina virtual a "wadexample". Establezca su nombre de usuario administrador y la contraseña y haga clic en **siguiente**.
6.  En el cuadro de diálogo **Configuración del servicio de nube** crear un nuevo servicio de nube denominado "wadexampleVM". Crear una nueva cuenta de almacenamiento denominada "wadexample" y haga clic en **siguiente**.
7.  Haga clic en **crear**.

### <a name="step-2-create-your-application"></a>Paso 2: Crear la aplicación
1.  En el equipo de desarrollo, inicie Visual Studio de 2013.
2.  Crear una nueva aplicación Visual C# consola destinada a .NET Framework 4.5. Nombre del proyecto "WadExampleVM".
    ![CloudServices_diag_new_project](./media/virtual-machines-dotnet-diagnostics/NewProject.png)
3.  Reemplazar el contenido de Program.cs con el código siguiente. La clase **SampleEventSourceWriter** implementa cuatro métodos de registro: **SendEnums**, **MessageMethod**, **SetOther** y **HighFreq**. El primer parámetro al método WriteEvent define el identificador del evento respectivo. El método de ejecución implementa un bucle que llama a cada uno de los métodos de registro que se ha implementado en la clase **SampleEventSourceWriter** cada 10 segundos.

        using System;
        using System.Diagnostics;
        using System.Diagnostics.Tracing;
        using System.Threading;

        namespace WadExampleVM
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

        class Program
        {
        static void Main(string[] args)
        {
            Trace.TraceInformation("My application entry point called");

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
        }
        }


4.  Guarde el archivo y seleccione **Generar solución** en el menú **Generar** para generar el código.


### <a name="step-3-deploy-your-application"></a>Paso 3: Implementar la aplicación
1.  Haga clic en el proyecto **WadExampleVM** en el **Explorador de soluciones** y elija **Abrir carpeta en el Explorador de archivos**.
2.  Vaya a la carpeta *bin\Debug* y copie todos los archivos (WadExampleVM.*)
3.  En el **Explorador de servidor** haga clic en la máquina virtual y elija **Conectar utilizando Escritorio remoto**.
4.  Una vez conectado a la máquina virtual cree una carpeta denominada WadExampleVM y pegar archivos de la aplicación a la carpeta.
5.  Inicie la aplicación WadExampleVM.exe. Debería ver una ventana de consola en blanco.

### <a name="step-4-create-your-diagnostics-configuration-and-install-the-extension"></a>Paso 4: Crear su configuración de diagnósticos e instale la extensión
1.  Descargar la definición de esquema del archivo de configuración público con su equipo de desarrollo, ejecute el siguiente comando PowerShell:

        (Get-AzureServiceAvailableExtension -ExtensionName 'PaaSDiagnostics' -ProviderNamespace 'Microsoft.Azure.Diagnostics').PublicConfigurationSchema | Out-File -Encoding utf8 -FilePath 'WadConfig.xsd'

2.  Abra un nuevo archivo XML en Visual Studio, ya sea en un proyecto ya ha abierto o en un Visual Studio instancia sin proyectos abiertos. En Visual Studio, seleccione **Agregar** -> **Nuevo elemento...**  ->  **Elementos visual C#** -> **datos** -> **Archivo XML**. Nombre del archivo "WadExample.xml"
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

### <a name="step-5-remotely-install-diagnostics-on-your-azure-virtual-machine"></a>Paso 5: Instalar de forma remota diagnósticos en la máquina Virtual de Azure
Los cmdlets de PowerShell para administrar diagnósticos en una máquina virtual son: conjunto AzureVMDiagnosticsExtension, Get-AzureVMDiagnosticsExtension y quitar AzureVMDiagnosticsExtension.

1.  En el equipo de desarrollo, abra PowerShell de Azure.
2.  Ejecute la secuencia de comandos para instalar remotamente diagnósticos en la máquina virtual (reemplazar *StorageAccountKey* con la clave de cuenta de almacenamiento para su cuenta de almacenamiento wadexamplevm):

        $storage_name = "wadexamplevm"
        $key = "<StorageAccountKey>"
        $config_path="c:\users\<user>\documents\visual studio 2013\Projects\WadExampleVM\WadExampleVM\WadExample.xml"
        $service_name="wadexamplevm"
        $vm_name="WadExample"
        $storageContext = New-AzureStorageContext -StorageAccountName $storage_name -StorageAccountKey $key
        $VM1 = Get-AzureVM -ServiceName $service_name -Name $vm_name
        $VM2 = Set-AzureVMDiagnosticsExtension -DiagnosticsConfigurationPath $config_path -Version "1.*" -VM $VM1 -StorageContext $storageContext
        $VM3 = Update-AzureVM -ServiceName $service_name -Name $vm_name -VM $VM2.VM


### <a name="step-6-look-at-your-telemetry-data"></a>Paso 6: Ver los datos de telemetría
En el **Explorador de servidores** de Visual Studio vaya a la cuenta de almacenamiento de wadexample. Después de la máquina virtual que se ha ejecutado aproximadamente 5 minutos debería ver las tablas **WADEnumsTable**, **WADHighFreqTable**, **WADMessageTable**, **WADPerformanceCountersTable** y **WADSetOtherTable**. Haga doble clic en una de las tablas para ver la telemetría que ha recopilado.

![CloudServices_diag_wadexamplevm_tables](./media/virtual-machines-dotnet-diagnostics/WadExampleVMTables.png)

## <a name="configuration-file-schema"></a>Esquema de archivo de configuración

El archivo de configuración de diagnósticos define los valores que se usan iniciar configuración de diagnóstico cuando se inicia el agente de diagnóstico. Vea la [referencia de esquema más reciente](https://msdn.microsoft.com/library/azure/mt634524.aspx) para valores válidos y ejemplos.

## <a name="troubleshooting"></a>Solución de problemas

Para obtener más información, vea [Solución de problemas de diagnósticos de Azure](azure-diagnostics-troubleshooting.md) .


## <a name="next-steps"></a>Pasos siguientes
[Vea una lista de máquina virtual relacionados con los artículos de diagnóstico de Azure](azure-diagnostics.md#virtual-machines-using-azure-diagnostics) para cambiar los datos que se están recopilando, solucionar problemas o más información sobre diagnósticos en general.


[Clase de origen de eventos]: http://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource(v=vs.110).aspx

[Debugging an Azure Application]: http://msdn.microsoft.com/library/windowsazure/ee405479.aspx   
[Collect Logging Data by Using Azure Diagnostics]: http://msdn.microsoft.com/library/windowsazure/gg433048.aspx
[Versión de prueba gratuita]: http://azure.microsoft.com/pricing/free-trial/
[Instalar y configurar Azure PowerShell versión 0.8.7 o posterior]: http://azure.microsoft.com/documentation/articles/install-configure-powershell/
