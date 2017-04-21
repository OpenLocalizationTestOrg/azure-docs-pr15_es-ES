<properties
    pageTitle="Seguimiento del flujo de una aplicación de servicios de nube con diagnósticos de Azure | Microsoft Azure"
    description="Agregar mensajes de seguimiento a una aplicación de Azure para ayudar a depurar, medir el rendimiento, supervisión, análisis del tráfico y mucho más."
    services="cloud-services"
    documentationCenter=".net"
    authors="rboucher"
    manager="jwhit"
    editor=""/>

<tags
    ms.service="cloud-services"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="02/20/2016"
    ms.author="robb"/>



# <a name="trace-the-flow-of-a-cloud-services-application-with-azure-diagnostics"></a>Seguimiento del flujo de una aplicación de servicios de nube con diagnósticos de Azure

Seguimiento de forma para supervisar la ejecución de la aplicación mientras se está ejecutando. Puede usar las clases [System.Diagnostics.Trace](https://msdn.microsoft.com/library/system.diagnostics.trace.aspx) [System.Diagnostics.Debug](https://msdn.microsoft.com/library/system.diagnostics.debug.aspx)y [System.Diagnostics.TraceSource](https://msdn.microsoft.com/library/system.diagnostics.tracesource.aspx) para registrar información sobre errores y ejecución de la aplicación en registros, archivos de texto u otros dispositivos para su análisis posterior. Para obtener más información sobre el seguimiento, vea [seguimiento e instrumentación de aplicaciones](https://msdn.microsoft.com/library/zs6s4h68.aspx).


## <a name="use-trace-statements-and-trace-switches"></a>Usar instrucciones de seguimiento y los modificadores de seguimiento

Implementar seguimiento en su aplicación de servicios de nube agregando el [DiagnosticMonitorTraceListener](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.diagnostics.diagnosticmonitortracelistener.aspx) a la configuración de la aplicación y realizar llamadas a System.Diagnostics.Trace o System.Diagnostics.Debug en el código de la aplicación. Use el archivo de configuración *app.config* para las funciones de trabajo y *web.config* para funciones web. Cuando se crea un nuevo servicio hospedado usando una plantilla de Visual Studio, diagnósticos de Azure se agregan automáticamente al proyecto y la DiagnosticMonitorTraceListener se agrega al archivo de configuración adecuado para las funciones que agregar.

Para obtener información sobre la colocación de instrucciones de seguimiento, vea [Cómo: agregar instrucciones de seguimiento al código de aplicación](https://msdn.microsoft.com/library/zd83saa2.aspx).

Colocando [Modificadores de seguimiento](https://msdn.microsoft.com/library/3at424ac.aspx) en el código, puede controlar si se realiza el seguimiento y cómo es su alcance. Esto le permite controlar el estado de la aplicación en un entorno de producción. Esto es especialmente importante en una aplicación empresarial que usa varios componentes que se ejecutan en varios equipos. Para obtener más información, vea [Cómo: configurar modificadores de seguimiento](https://msdn.microsoft.com/library/t06xyy08.aspx).

## <a name="configure-the-trace-listener-in-an-azure-application"></a>Configurar la escucha de seguimiento en una aplicación de Azure

Seguimiento, depuración y TraceSource, deberá configurar "escucha" para recopilar y registrar los mensajes que se envían. Escucha recopila, almacena y enrutar mensajes de seguimiento. Dirigen a los resultados del seguimiento a un destino apropiado, como un registro, una ventana o un archivo de texto. Diagnóstico de Azure usa la clase [DiagnosticMonitorTraceListener](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.diagnostics.diagnosticmonitortracelistener.aspx) .

Antes de completar el siguiente procedimiento, debe inicializarse al monitor de diagnóstico de Azure. Para ello, vea [Habilitar diagnósticos de Microsoft Azure](cloud-services-dotnet-diagnostics.md).

Tenga en cuenta que si utiliza las plantillas que se proporcionan en Visual Studio, la configuración de la escucha se agrega automáticamente para usted.


### <a name="add-a-trace-listener"></a>Agregar un agente de escucha

1. Abra el archivo web.config o app.config para su rol.
2. Agregue el código siguiente al archivo. Cambie el atributo de versión para usar el número de versión del ensamblado que se hace referencia. La versión de ensamblado no se cambia necesariamente con cada versión del SDK de Azure a menos que hay actualizaciones.

    ```
    <system.diagnostics>
        <trace>
            <listeners>
                <add type="Microsoft.WindowsAzure.Diagnostics.DiagnosticMonitorTraceListener,
                  Microsoft.WindowsAzure.Diagnostics,
                  Version=2.8.0.0,
                  Culture=neutral,
                  PublicKeyToken=31bf3856ad364e35"
                  name="AzureDiagnostics">
                  <filter type="" />
                </add>
            </listeners>
        </trace>
    </system.diagnostics>
    ```
    >[AZURE.IMPORTANT] Asegúrese de que tiene una referencia de proyecto al ensamblado Microsoft.WindowsAzure.Diagnostics. Actualizar el número de versión en el xml anterior para que coincida con la versión del ensamblado Microsoft.WindowsAzure.Diagnostics que se hace referencia.

3. Guarde el archivo de configuración.

Para obtener más información acerca de escucha, vea [Agentes de escucha](https://msdn.microsoft.com/library/4y5y10s7.aspx).

Después de completar los pasos para agregar el agente de escucha, puede agregar instrucciones de seguimiento al código.


### <a name="to-add-trace-statement-to-your-code"></a>Para agregar instrucciones de seguimiento al código

1. Abrir un archivo de origen de la aplicación. Por ejemplo, el <RoleName>. cs archivo para la función de trabajo o web.
2. Agregue la siguiente instrucción using si ya no se ha agregado:
    ```
        using System.Diagnostics;
    ```
3. Agregar instrucciones de seguimiento en la que desea capturar información sobre el estado de la aplicación. Puede utilizar una variedad de métodos para dar formato al resultado de la instrucción de seguimiento. Para obtener más información, vea [Cómo: agregar instrucciones de seguimiento al código de aplicación](https://msdn.microsoft.com/library/zd83saa2.aspx).
4. Guarde el archivo de origen.
