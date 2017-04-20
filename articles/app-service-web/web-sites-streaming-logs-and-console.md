<properties 
    pageTitle="Consola y los registros de secuencias" 
    description="Introducción a consola y transmisiones de registros" 
    authors="btardif" 
    manager="wpickett" 
    editor="" 
    services="app-service\web" 
    documentationCenter=""/>

<tags 
    ms.service="app-service-web" 
    ms.workload="web" 
    ms.tgt_pltfrm="na" 
    ms.devlang="multiple" 
    ms.topic="article" 
    ms.date="10/12/2016" 
    ms.author="byvinyal"/>

# <a name="streaming-logs-and-the-console"></a>Registros streaming y la consola

## <a name="streaming-logs"></a>Registros de secuencias

El **portal de Azure** proporciona un visor de registro de streaming integrado que le permite ver los eventos de seguimiento de las aplicaciones de **Servicio de aplicaciones** en tiempo real.  

La configuración de esta característica requiere unos sencillos pasos:

- Escribir seguimientos en el código
- Habilitar los **registros de diagnóstico** de la aplicación de la aplicación
- Ver la secuencia de la interfaz de usuario de **Registros de transmisión** integrada en el **portal de Azure**.

### <a name="how-to-write-traces-in-your-code"></a>Cómo escribir seguimientos en el código ###

Es fácil escribir seguimientos en el código.  En C# es tan fácil como escribir el siguiente código:

`````````````````````````
Trace.TraceInformation("My trace statement");
`````````````````````````

`````````````````````````
Trace.TraceWarning("My warning statement");
`````````````````````````

`````````````````````````
Trace.TraceError("My error statement");
`````````````````````````

La clase Trace reside en el espacio de nombres System.Diagnostics.

En una aplicación de node.js puede escribir este código para conseguir el mismo resultado:

`````````````````````````
console.log("My trace statement").
`````````````````````````

### <a name="how-to-enable-and-view-the-streaming-logs"></a>Cómo habilitar y ver la transmisión de registros
![][BrowseSitesScreenshot]Diagnósticos están habilitadas en una base por la aplicación. Comience por la exploración al sitio que desea activar esta característica.  
  
![][DiagnosticsLogs]Desde el menú Configuración, desplácese hacia abajo hasta la sección de **supervisión** y haga clic en **Registros de diagnóstico (1)**. A continuación, **Habilitar (2)** **Aplicación registro (sistema de archivos)** o **(Blob) el registro de aplicación de** la opción de **nivel** le permite cambiar el nivel de gravedad de trazas para capturar. Si solo tiene que intenta familiarizarse con la característica, establezca el nivel en **detallado** para asegurarse de que se recopilan todas las instrucciones de seguimiento.

Haga clic en **Guardar** en la parte superior del módulo y esté listo para ver los registros.

>[AZURE.NOTE] Cuanto mayor sea el **nivel de gravedad** se consumen más recursos del registro y los seguimientos más se fabrican. Asegúrese de que el **nivel de gravedad** está configurado para el nivel de detalle correcta para un sitio de tráfico alta o de producción. 

![][StreamingLogsScreenshot]Para ver la **transmisión registros** desde dentro del portal de Azure, haga clic en la **secuencia de registro (1)** también en la sección **supervisión** del menú Configuración. Si la aplicación activa está escribiendo instrucciones de seguimiento, debe verlas en la **transmisión (2) registra la interfaz de usuario** en casi en tiempo real.

## <a name="console"></a>Consola
El **portal de Azure** proporciona acceso a la aplicación de consola. Puede explorar el sistema de archivos de la aplicación y ejecutar secuencias de comandos de powershell/cmd. Se enlazan por los mismos permisos establecidos como la ejecución de código de aplicación al ejecutar los comandos de la consola. Acceso a directorios protegidos o ejecutar secuencias de comandos que requieren permisos elevados está bloqueada.  

![][ConsoleScreenshot]Desde el menú Configuración, desplácese hacia abajo hasta la sección **Herramientas de desarrollo** y haga clic en **Consola (1)** y la interfaz de usuario **(2) consola** se abre a la derecha.

Para familiarizarse con la **consola**, intente comandos básicos, como:

`````````````````````````
dir
`````````````````````````

`````````````````````````
cd
`````````````````````````

<!-- Images. -->
[DiagnosticsLogs]: ./media/web-sites-streaming-logs-and-console/diagnostic-logs.png
[BrowseSitesScreenshot]: ./media/web-sites-streaming-logs-and-console/browse-sites.png
[StreamingLogsScreenshot]: ./media/web-sites-streaming-logs-and-console/streaming-logs.png
[ConsoleScreenshot]: ./media/web-sites-streaming-logs-and-console/console.png
