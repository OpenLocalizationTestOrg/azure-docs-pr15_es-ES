<properties
    pageTitle="Habilitar el registro de diagnóstico para las aplicaciones web en la aplicación de servicio de Azure"
    description="Obtenga información sobre cómo habilitar el registro de diagnóstico y agregar instrumentación a la aplicación, así como cómo tener acceso a la información que registra Azure."
    services="app-service"
    documentationCenter=".net"
    authors="cephalin"
    manager="wpickett"
    editor="jimbe"/>

<tags
    ms.service="app-service"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="06/06/2016"
    ms.author="cephalin"/>

# <a name="enable-diagnostics-logging-for-web-apps-in-azure-app-service"></a>Habilitar el registro de diagnóstico para las aplicaciones web en la aplicación de servicio de Azure

## <a name="overview"></a>Información general

Azure proporciona diagnósticos integrados para ayudar a la depuración de una [aplicación de servicio de aplicación web](http://go.microsoft.com/fwlink/?LinkId=529714). En este artículo aprenderá cómo habilitar el registro de diagnóstico y agregar instrumentación a la aplicación, así como cómo tener acceso a la información que registra Azure.

En este artículo se usa el [Portal de Azure](https://portal.azure.com), Azure PowerShell y la interfaz de línea de comandos de Azure (Azure CLI) para trabajar con registros de diagnóstico. Para obtener información sobre cómo trabajar con registros de diagnóstico mediante Visual Studio, vea [Solución de problemas de Azure en Visual Studio](web-sites-dotnet-troubleshoot-visual-studio.md).

[AZURE.INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]

## <a name="whatisdiag"></a>Diagnósticos de servidor Web y diagnósticos de aplicación

Aplicaciones de servicio de aplicación web proporcionan funcionalidad de diagnóstico para registrar información desde el servidor web y la aplicación web. Se divide lógicamente en **Diagnósticos de servidor web** y **Diagnósticos de aplicación**.

### <a name="web-server-diagnostics"></a>Diagnósticos de servidor Web

Puede habilitar o deshabilitar a los siguientes tipos de registros:

- **Registro de errores detallado** - información de error detallada de los códigos de estado HTTP que indique un error (código de estado 400 o mayor). Esto puede contener información que puede ayudar a determinar por qué el servidor devolvió el código de error.
- **Seguimiento de solicitud de error** - información detallada sobre solicitudes de error, como un seguimiento de los componentes IIS utilizado para procesar la solicitud y el tiempo necesario en cada componente. Esto puede ser útil si está intentando aumentar el rendimiento del sitio o aislar la causa de un error específico de HTTP que se devolverá.
- **Registro de servidor web** : información sobre las transacciones de HTTP con el [formato de archivo de registro extendido W3C](http://msdn.microsoft.com/library/windows/desktop/aa814385.aspx). Esto es útil cuando determinar general métricas del sitio, como el número de solicitudes de administran o cuántas peticiones son de una dirección IP específica.

### <a name="application-diagnostics"></a>Diagnósticos de aplicación

Diagnósticos de la aplicación le permite capturar información creado mediante una aplicación web. Aplicaciones de ASP.NET pueden utilizar la clase [System.Diagnostics.Trace](http://msdn.microsoft.com/library/36hhw2t6.aspx) para registrar información en el registro de diagnósticos de aplicación. Por ejemplo:

    System.Diagnostics.Trace.TraceError("If you're seeing this, something bad happened");

En tiempo de ejecución puede recuperar estos registros para ayudar a solucionar problemas. Para obtener más información, vea [solución de problemas de Azure web apps en Visual Studio](web-sites-dotnet-troubleshoot-visual-studio.md).

Aplicaciones de servicio de la aplicación web también registrar información de implementación al publicar contenido en una aplicación web. Esto ocurre automáticamente y no hay ninguna configuración de registro de implementación. Registro de implementación le permite determinar por qué ha fallado una implementación. Por ejemplo, si está utilizando un script de implementación personalizado, puede usar el registro de implementación para determinar por qué no funciona la secuencia de comandos.

## <a name="enablediag"></a>Cómo habilitar diagnósticos

Para habilitar diagnósticos en el [Portal de Azure](https://portal.azure.com), vaya a la hoja para la aplicación web y haga clic en **Configuración > registros de diagnósticos de**.

<!-- todo:cleanup dogfood addresses in screenshot -->
![Elemento de registros](./media/web-sites-enable-diagnostic-log/logspart.png)

Al habilitar **Diagnósticos de aplicación** también elegir el **nivel**. Esta configuración permite filtrar la información que se genera a información **informativo**, **Advertencia** o **error** . Establecer esta opción para **Mostrar información detallada** registrará toda la información producida por la aplicación.

> [AZURE.NOTE] A diferencia de cambiar el archivo web.config, habilitar diagnósticos de aplicación o cambiando los niveles de registro de diagnóstico reciclaje no el dominio de aplicación que la aplicación se ejecuta en.

En la ficha de **Configurar** [portal clásica](https://manage.windowsazure.com) Web app, puede seleccionar **almacenamiento** o **sistema de archivos** de **registro de servidor web**. Selección de **almacenamiento** le permite seleccionar una cuenta de almacenamiento y, a continuación, en un contenedor de blob que se escriben en los registros. Todos los otros registros de **Diagnósticos de sitio** se escriben en el sistema de archivos solo.

La ficha de **Configurar** [portal clásica](https://manage.windowsazure.com) Web app también tiene parámetros adicionales de diagnósticos de aplicación:

* **Sistema de archivos** - almacena la información de diagnósticos de aplicación en el sistema de archivos de aplicación web. Estos archivos se pueden acceder a FTP o descargados como un archivo Zip con Azure PowerShell o Azure de línea de comandos interfaz Azure.
* **Almacenamiento de tablas** - almacena la información de diagnóstico de aplicación en el nombre de cuenta de almacenamiento de Azure y la tabla especificado.
* **Almacenamiento de blobs** - almacena la información de diagnóstico de aplicación en el contenedor de la cuenta de almacenamiento de Azure y blob especificado.
* **Período de retención** - de forma predeterminada, los registros no se eliminan automáticamente de **almacenamiento de blobs de Windows**. Seleccione **establecer la retención** y escriba el número de días para mantener los registros si desea eliminar automáticamente los registros.

>[AZURE.NOTE] Si [regenerar las teclas de acceso de su cuenta de almacenamiento](storage-create-storage-account.md#view-copy-and-regenerate-storage-access-keys), debe restablecer la configuración de registro correspondientes a utilizar las teclas actualizadas. Para hacer esto:
>
> 1. En la ficha **Configurar** , establezca la característica de registro correspondiente para **desactivar**. Guardar la configuración.
> 2. Habilitar el registro nuevo a la tabla o blobs de la cuenta de almacenamiento. Guardar la configuración.

Cualquier combinación de sistema de archivos, almacenamiento de tablas o almacenamiento de blobs puede habilitarse al mismo tiempo y tiene las configuraciones de nivel de registro individuales. Por ejemplo, puede registrar errores y advertencias de almacenamiento de blobs como una solución a largo plazo de registro, al habilitar el registro del sistema de archivos con un nivel de detallado.

Mientras todas las tres ubicaciones de almacenamiento proporcionan la misma información básica de eventos registrados, **almacenamiento de tablas** y el **almacenamiento de blobs de** registrar información adicional, como el identificador de instancia, el identificador de subproceso y una marca de tiempo más granular (formato de graduación) de registro al **sistema de archivos**.

> [AZURE.NOTE] Solo se puede acceder a la información almacenada en el **almacenamiento de tablas** o **almacenamiento de blobs** usando un cliente de almacenamiento o una aplicación que puede trabajar directamente con estos sistemas de almacenamiento. Por ejemplo, 2013 de Visual Studio contiene un explorador de almacenamiento que se pueden usar para explorar el almacenamiento de blobs o tabla y HDInsight puede tener acceso a los datos almacenados en el almacenamiento de blobs. También puede escribir una aplicación que tiene acceso a almacenamiento de Azure mediante uno de los [SDK de Azure](/downloads/#).

> [AZURE.NOTE] También se pueden habilitar diagnósticos de PowerShell de Azure mediante el cmdlet **Set-AzureWebsite** . Si no ha instalado Azure PowerShell, o si no ha configurado para usar su suscripción de Azure, consulte [cómo usar PowerShell de Azure](/develop/nodejs/how-to-guides/powershell-cmdlets/).

##<a name="download"></a>Cómo: descargar registros

Información de diagnóstico almacenada en el sistema de archivos de aplicación web se puede acceder directamente mediante FTP. También puede descargarse como un archivo Zip con Azure PowerShell o la interfaz de línea de comandos de Azure.

La estructura de directorios que los registros se almacenan en es la siguiente:

* **Registros de aplicación** - /LogFiles/aplicaciones /. Esta carpeta contiene uno o más archivos de texto que contiene información producido por el registro de aplicación.

* **No se pudo seguimientos de solicitud** - / archivos de registro/W3SVC ### /. Esta carpeta contiene un archivo XSL y uno o más archivos XML. Asegúrese de descargar el archivo XSL en el mismo directorio como XML archivos porque el archivo XSL proporciona funcionalidad para dar formato y filtrar el contenido de los archivos XML cuando se ve en Internet Explorer.

* **Registros de errores detallados** - /LogFiles/DetailedErrors /. Esta carpeta contiene uno o más archivos .htm que proporcionan información detallada de los errores HTTP que se han producido.

* **Registros del servidor web** - /LogFiles/http/RawLogs. Esta carpeta contiene uno o más archivos de texto con formato utilizando el [formato de archivo de registro extendido W3C](http://msdn.microsoft.com/library/windows/desktop/aa814385.aspx).

* **Registros de implementación** - / archivos de registro/Git. Esta carpeta contiene registros generados por los procesos de implementación interna usados por aplicaciones web de Azure, así como los registros para implementaciones Git.

### <a name="ftp"></a>FTP

Para obtener acceso a información de diagnóstico mediante FTP, visite el **panel** de la aplicación web en el [portal de clásico](https://manage.windowsazure.com). En la sección **vistazo rápido** , utilice el vínculo de **Registros de diagnóstico FTP** para tener acceso a los archivos de registro mediante FTP. La entrada de **Usuario de la implementación/FTP** indica el nombre de usuario que debe usarse para acceder al sitio FTP.

> [AZURE.NOTE] Si no se establece la entrada del **Usuario de la implementación/FTP** o ha olvidado la contraseña para este usuario, puede crear un nuevo usuario y una contraseña mediante el vínculo **Restablecer credenciales de implementación** en la sección **vistazo rápido** del **panel**.

### <a name="download-with-azure-powershell"></a>Descargar con PowerShell Azure

Para descargar los archivos de registro, inicie una nueva instancia de PowerShell de Azure y use el comando siguiente:

    Save-AzureWebSiteLog -Name webappname

Esto guarde los registros de la aplicación web especificado por el **-nombre** parámetros a un archivo llamado **logs.zip** en el directorio actual.

> [AZURE.NOTE] Si no ha instalado Azure PowerShell, o si no ha configurado para usar su suscripción de Azure, consulte [cómo usar PowerShell de Azure](/develop/nodejs/how-to-guides/powershell-cmdlets/).

### <a name="download-with-azure-command-line-interface"></a>Descargar con la interfaz de línea de comandos de Azure

Para descargar los archivos de registro mediante la interfaz de línea de comandos de Azure, abra un nuevo símbolo, PowerShell, fiesta o sesión de Terminal y escriba el siguiente comando:

    azure site log download webappname

Esta acción guardará los registros de la aplicación web con el nombre 'webappname' en un archivo llamado **diagnostics.zip** en el directorio actual.

> [AZURE.NOTE] Si no ha instalado la interfaz de línea de comandos de Azure (Azure CLI), o si no ha configurado para usar su suscripción de Azure, consulte [cómo usar CLI de Azure](../xplat-cli-install.md).

## <a name="how-to-view-logs-in-application-insights"></a>Cómo: ver los registros de información de la aplicación

Perspectivas de aplicación de Visual Studio proporciona herramientas para filtrar y buscar registros y correlación de los registros con las convocatorias y otros eventos.

1. Agregue el SDK de perspectivas de aplicación a un proyecto de Visual Studio.
 * En el Explorador de soluciones, haga clic con el botón secundario del mouse en el proyecto y elija agregar perspectivas de aplicación. Se le guiará a través de los pasos que se incluyen la creación de un recurso de información de la aplicación. [Aprende más](../application-insights/app-insights-asp-net.md)
2. Agregue el paquete de escucha de seguimiento a su proyecto.
 * Haga clic con el botón secundario del mouse en el proyecto y elija Administrar paquetes de NuGet. Seleccione `Microsoft.ApplicationInsights.TraceListener` [más información](../application-insights/app-insights-asp-net-trace-logs.md)
3. Cargar el proyecto y ejecútelo para generar los datos del registro.
4. En el [Portal de Azure](https://portal.azure.com/), vaya a su nuevo recurso de información de la aplicación y abra la **búsqueda**. Verá los datos de registro, junto con la solicitud, uso y otra telemetría. Algunos telemetría puede tardar unos minutos para llegar: haga clic en actualizar. [Aprende más](../application-insights/app-insights-diagnostic-search.md)

[Más información sobre el rendimiento de seguimiento con recomendaciones de aplicación](../application-insights/app-insights-azure-web-apps.md)

##<a name="streamlogs"></a>Cómo: transmitir registros

Al desarrollar una aplicación, suele ser útil ver la información de registro en prácticamente en tiempo real. Para ello puede transmisión información de registro para su entorno de desarrollo con Azure PowerShell o la interfaz de línea de comandos de Azure.

> [AZURE.NOTE] Algunos tipos de registro de búfer de escritura en el archivo de registro, puede dar lugar a eventos en el orden correcto en la secuencia. Por ejemplo, se mostrará una entrada de registro de la aplicación que se produce cuando un usuario visita una página en la secuencia antes de la entrada de registro HTTP correspondiente de la solicitud de página.

> [AZURE.NOTE] Transmisión de registro también se transmita información escrita en cualquier archivo de texto almacenado en el **D:\\principal\\archivos de registro\\ ** carpeta.

### <a name="streaming-with-azure-powershell"></a>Transmisión con PowerShell Azure

Transmitir información de registro, inicie una nueva instancia de PowerShell de Azure y use el comando siguiente:

    Get-AzureWebSiteLog -Name webappname -Tail

Esto se conectará a la aplicación web especificada por el **-nombre** parámetro y empezar a transmitir información a la ventana de PowerShell que se producen eventos de registro en la aplicación web. Cualquier información que se escriben en los archivos que termina en .txt, .log o .htm que se almacenan en el directorio /LogFiles (d:/inicio o archivos de registro) se transferirá a la consola local.

Para filtrar eventos específicos, como errores, utilice la **-mensaje** parámetro. Por ejemplo:

    Get-AzureWebSiteLog -Name webappname -Tail -Message Error

Para filtrar los tipos de registro específico, como HTTP, utilice la **-ruta** parámetro. Por ejemplo:

    Get-AzureWebSiteLog -Name webappname -Tail -Path http

Para ver una lista de rutas disponibles, use el parámetro - ListPath.

> [AZURE.NOTE] Si no ha instalado Azure PowerShell, o si no ha configurado para usar su suscripción de Azure, consulte [cómo usar PowerShell de Azure](/develop/nodejs/how-to-guides/powershell-cmdlets/).

### <a name="streaming-with-azure-command-line-interface"></a>Transmisión con interfaz de línea de comandos de Azure

Para transmitir información de registro, abra un nuevo símbolo, PowerShell, fiesta o sesión de Terminal y escriba el siguiente comando:

    azure site log tail webappname

Se conecta a la aplicación web con el nombre 'webappname' y empezar a transmitir información a la ventana que se producen eventos de registro en la aplicación web. Cualquier información que se escriben en los archivos que termina en .txt, .log o .htm que se almacenan en el directorio /LogFiles (d:/inicio o archivos de registro) se transferirá a la consola local.

Para filtrar eventos específicos, como errores, utilice la **--filtro** parámetro. Por ejemplo:

    azure site log tail webappname --filter Error

Para filtrar los tipos de registro específico, como HTTP, utilice la **--ruta** parámetro. Por ejemplo:

    azure site log tail webappname --path http

> [AZURE.NOTE] Si no ha instalado la interfaz de línea de comandos de Azure, o si no ha configurado para usar su suscripción de Azure, consulte [cómo a Azure línea de comandos de interfaz de usar](../xplat-cli-install.md).

##<a name="understandlogs"></a>Cómo: comprender los registros de diagnóstico

### <a name="application-diagnostics-logs"></a>Registros de diagnósticos de aplicación

Diagnósticos de aplicación almacenan información en un formato específico para aplicaciones. NET, dependiendo de si almacenar registros al sistema de archivos, el almacenamiento de tablas o el almacenamiento de blobs. El conjunto de base de datos almacenados es el mismo en todas las tres tipos de almacenamiento: la fecha y hora que se produjo el evento, el identificador del proceso que generó el evento, el tipo de evento (información, advertencia, error) y el mensaje del evento.

__Sistema de archivos__

Cada línea conectado al sistema de archivos o recibido utilizando la transmisión tendrá el siguiente formato:

    {Date}  PID[{process id}] {event type/level} {message}

Por ejemplo, un evento de error tendría un aspecto similar al siguiente:

    2014-01-30T16:36:59  PID[3096] Error       Fatal error on the page!

Registro en el sistema de archivos proporciona la información más básica de los tres métodos disponibles, proporcionar solo la hora, id de proceso, el nivel de evento y mensaje.

__Almacenamiento de tablas__

Cuando se inicia sesión almacenamiento de tablas, propiedades adicionales se utilizan para facilitar la búsqueda de los datos almacenados en la tabla, así como información más detallada sobre el evento. Las siguientes propiedades (columnas) se utilizan para cada entidad (fila) que se almacenan en la tabla.

Nombre de propiedad|Valor o formato
---|---
PartitionKey|Fecha y hora del evento en formato de yyyyMMddHH
RowKey|Un valor GUID que identifica esta entidad
Marca de tiempo|La fecha y hora en que ocurrió el evento
EventTickCount|La fecha y hora en que se produjo el evento, en formato de graduación (mayor precisión)
ApplicationName|El nombre de la aplicación web
Nivel|Nivel de evento (por ejemplo, error, advertencia, información)
IdEvento|El identificador de evento de este evento<p><p>El valor predeterminado es 0, si no especifica ninguno
InstanceId|Instancia de la aplicación web que se produjo el incluso en
PID|ID de proceso
TID|El identificador de subproceso del subproceso que generó el evento
Mensaje|Mensaje de evento de detalle

__Almacenamiento de blobs__

Al iniciar sesión para el almacenamiento de blobs, los datos se almacenan en formato de valores separados por comas (CSV). Similar al almacenamiento de tablas, campos adicionales se registran para proporcionar información más detallada sobre el evento. Para cada fila de la CSV, se usan las siguientes propiedades:

Nombre de propiedad|Valor o formato
---|---
Fecha|La fecha y hora en que ocurrió el evento
Nivel|Nivel de evento (por ejemplo, error, advertencia, información)
ApplicationName|El nombre de la aplicación web
InstanceId|Instancia de la aplicación web que se produjo el evento
EventTickCount|La fecha y hora en que se produjo el evento, en formato de graduación (mayor precisión)
IdEvento|El identificador de evento de este evento<p><p>El valor predeterminado es 0, si no especifica ninguno
PID|ID de proceso
TID|El identificador de subproceso del subproceso que generó el evento
Mensaje|Mensaje de evento de detalle

Los datos almacenados en un blob tendría un aspecto similares al siguiente:

    date,level,applicationName,instanceId,eventTickCount,eventId,pid,tid,message
    2014-01-30T16:36:52,Error,mywebapp,6ee38a,635266966128818593,0,3096,9,An error occurred

> [AZURE.NOTE] La primera línea del registro contendrá los encabezados de columna como representados en este ejemplo.

### <a name="failed-request-traces"></a>No se pudo seguimientos de solicitud

Error de solicitud trazas se almacenan en archivos XML denominados __NG ### .xml__. Para que sea más fácil ver la información registrada, hojas de estilo XSL denominado __freb.xsl__ está disponible en el mismo directorio que los archivos XML. Abrir uno de los archivos XML en Internet Explorer usará la hoja de estilo XSL para proporcionar una presentación con formato de la información de seguimiento. Esto tendrá un aspecto similar al siguiente:

![Error en las peticiones vista en el explorador](./media/web-sites-enable-diagnostic-log/tws-failedrequestinbrowser.png)

### <a name="detailed-error-logs"></a>Registros de errores detallados

Registros de errores detallados son documentos HTML que proporcionan información más detallada sobre los errores HTTP que se han producido. Ya que son simplemente los documentos HTML, que pueden verse usando un explorador web.

### <a name="web-server-logs"></a>Registros del servidor Web

Formatean de los registros del servidor web con el [formato de archivo de registro extendido W3C](http://msdn.microsoft.com/library/windows/desktop/aa814385.aspx). Esta información puede leerse con un editor de texto o analizar mediante herramientas como el [Analizador de registro](http://go.microsoft.com/fwlink/?LinkId=246619).

> [AZURE.NOTE] Los registros generados por aplicaciones web Azure no admiten los campos __nombreDeEquipo s__, __s ip__o __versión de cs__ .

##<a name="nextsteps"></a>Pasos siguientes

- [Cómo supervisar las aplicaciones Web](/manage/services/web-sites/how-to-monitor-websites/)
- [Solución de problemas de aplicaciones web de Azure en Visual Studio](web-sites-dotnet-troubleshoot-visual-studio.md)
- [Analizar los registros de la aplicación web en HDInsight](http://gallery.technet.microsoft.com/scriptcenter/Analyses-Windows-Azure-web-0b27d413)

> [AZURE.NOTE] Si desea empezar a trabajar con el servicio de aplicación de Azure antes de suscribirse para una cuenta de Azure, vaya a [Probar el servicio de aplicaciones](http://go.microsoft.com/fwlink/?LinkId=523751), donde puede crear inmediatamente una aplicación web de starter corta duración en la aplicación de servicio. Sin tarjetas de crédito obligatoria; sin compromisos.

## <a name="whats-changed"></a>¿Qué ha cambiado
* Encontrará una guía para el cambio de sitios Web a la aplicación de servicio: [servicio de aplicación de Azure y su influencia sobre los servicios de Azure existentes](http://go.microsoft.com/fwlink/?LinkId=529714)
* Encontrará una guía para el cambio del portal antiguo en el nuevo portal: [referencia para navegar por el portal de Azure](http://go.microsoft.com/fwlink/?LinkId=529715)
 
