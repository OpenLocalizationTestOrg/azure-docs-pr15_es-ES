<properties
    pageTitle="Mejores prácticas y la Guía de solución de problemas para aplicaciones de nodo en aplicaciones Web de Azure"
    description="Obtenga información sobre las mejores prácticas y los pasos de solución de problemas para las aplicaciones de nodo en aplicaciones Web de Azure."
    services="app-service\web"
    documentationCenter="nodejs"
    authors="ranjithr"
    manager="wadeh"
    editor=""/>

<tags
    ms.service="app-service-web"
    ms.workload="web"
    ms.tgt_pltfrm="na"
    ms.devlang="nodejs"
    ms.topic="article"
    ms.date="06/06/2016"
    ms.author="ranjithr;wadeh"/>
    
# <a name="best-practices-and-troubleshooting-guide-for-node-applications-on-azure-web-apps"></a>Mejores prácticas y la Guía de solución de problemas para aplicaciones de nodo en aplicaciones Web de Azure

[AZURE.INCLUDE [tabs](../../includes/app-service-web-get-started-nav-tabs.md)]

En este artículo, aprenderá las mejores prácticas y los pasos para solucionar problemas de [aplicaciones de nodo](app-service-web-nodejs-get-started.md) se ejecutan en aplicaciones Web de Azure (con [iisnode](https://github.com/azure/iisnode)).

>[AZURE.WARNING] Tenga cuidado al usar los pasos de solución de problemas en el sitio de producción. Se recomienda solucionar su aplicación en un programa de instalación no es de producción, por ejemplo la franja de ensayo y cuando se corrige el problema, intercambiar la franja de ensayo con la franja de producción.

## <a name="iisnode-configuration"></a>Configuración de IISNODE

Este [archivo de esquema](https://github.com/Azure/iisnode/blob/master/src/config/iisnode_schema_x64.xml) se muestran todos los valores que se pueden configurar para iisnode. Algunas de las configuraciones que le será de utilidad para su aplicación son:

* nodeProcessCountPerApplication

    Esta opción controla el número de procesos de nodo que se inician por aplicación IIS. Valor predeterminado es 1. Puede iniciar de tantas node.exe como el recuento de core VM al establecer esta opción en 0. Valor recomendado es 0 para la mayoría de aplicaciones para que pueda utilizar todas las muestras en su equipo. Node.exe tiene un único subproceso para que una node.exe consume un máximo de 1 core y obtener el máximo rendimiento de la aplicación de nodo que desee utilizar las muestras.

* nodeProcessCommandLine

    Esta opción controla la ruta de acceso a la node.exe. Puede establecer este valor para que apunten a la versión node.exe.

* maxConcurrentRequestsPerProcess

    Esta opción controla el número máximo de solicitudes simultáneas enviado por iisnode para cada node.exe. En aplicaciones Web de azure, el valor predeterminado es infinito. No tendrá que preocuparse por esta configuración. Fuera de aplicaciones Web de azure, el valor predeterminado es 1024. Desea configurar esta función cuántas solicitudes que obtiene la aplicación y la velocidad la aplicación procesa cada solicitud.

* maxNamedPipeConnectionRetry

    Esta opción controla el número máximo de veces iisnode intentará hacer conexión en la canalización con nombre para enviar la solicitud al node.exe. Esta opción en combinación con namedPipeConnectionRetryDelay determina el tiempo de espera total de cada solicitud de iisnode. Valor predeterminado es 200 en aplicaciones Web de Azure. Total de tiempo de espera en segundos = (maxNamedPipeConnectionRetry \* namedPipeConnectionRetryDelay) / 1000

* namedPipeConnectionRetryDelay

    Esta configuración controla la cantidad de tiempo (en milisegundos) iisnode esperará entre cada reintento para enviar la solicitud a node.exe sobre la canalización con nombre. Valor predeterminado es 250ms.
    Total de tiempo de espera en segundos = (maxNamedPipeConnectionRetry \* namedPipeConnectionRetryDelay) / 1000

    De forma predeterminada, el tiempo de espera total en iisnode en aplicaciones Web de azure es 200 \* 250ms = 50 segundos.

* logDirectory

    Esta opción controla el directorio donde iisnode registrará stdout y stderr. Valor predeterminado es iisnode que es relativa al directorio de secuencia de comandos principal (directorio donde está presente server.js principal)

* debuggerExtensionDll

    Esta opción controla qué versión de nodo inspector iisnode usará al depurar su aplicación de nodo. Actualmente inspector de iisnode de 0.7.3.dll y iisnode inspector.dll son los valores válidos 2 solo para esta configuración. Valor predeterminado es 0.7.3.dll de inspector de iisnode. inspector de iisnode de 0.7.3.dll versión utiliza el inspector de nodo de 0.7.3 y websockets, por lo que debe habilitar websockets en su aplicación Web azure para utilizar esta versión. Consulte <http://www.ranjithr.com/?p=98> para obtener más detalles sobre cómo configurar iisnode para usar el inspector de nodo nuevo.

* flushResponse

    El comportamiento predeterminado de IIS es que coloca los datos de respuesta hacia arriba a 4MB antes de vaciar o hasta el final de la respuesta que suceda primero. iisnode ofrece una opción de configuración para reemplazar este comportamiento: para vaciar un fragmento de cuerpo de la entidad de respuesta tan pronto como lo reciba iisnode desde node.exe, es necesario configurar la iisnode/@flushResponse atributo de web.config en 'true':
    
    ```
    <configuration>    
        <system.webServer>    
            <!-- ... -->    
            <iisnode flushResponse="true" />    
        </system.webServer>    
    </configuration>
    ```

    Si se habilita la baja de cada fragmento del cuerpo de la entidad de respuesta agrega sobrecarga de rendimiento que reduce el rendimiento del sistema % ~ 5 (a partir de v0.1.13), por lo que es mejor definir el ámbito de esta configuración solo a los extremos que requieren la transmisión de respuesta (por ejemplo, con la <location> elemento de web.config)

    Además, para transmitir aplicaciones, debe establecer también responseBufferLimit del controlador iisnode de 0.
    
    ```
    <handlers>    
        <add name="iisnode" path="app.js" verb="\*" modules="iisnode" responseBufferLimit="0"/>    
    </handlers>
    ```

* watchedFiles

    Esto es una lista de punto y coma separado de archivos que se observan cambios. Cambios en un archivo, la aplicación de reciclaje. Cada entrada consta de un nombre de directorio opcional más el nombre de archivo requerido que son relativas al directorio donde se encuentra el punto de entrada de la aplicación principal. En la parte del nombre de archivo solo se admiten comodines. Valor predeterminado es "\*. js;web.config"

* recycleSignalEnabled

    Valor predeterminado es false. Si está habilitado, la aplicación de nodo puede conectarse a una canalización con nombre (variable de entorno IISNODE\_CONTROL\_canalización) y enviar un mensaje de "reciclaje". Esto hará que la w3wp para reciclaje correctamente.

* idlePageOutTimePeriod

    Valor predeterminado es 0, lo que significa que esta característica está deshabilitada. Cuando se establece en un valor mayor que 0, iisnode se página fuera todos sus procesos secundarios cada milisegundos 'idlePageOutTimePeriod'. Para entender qué página fuera de medios, consulte esta [documentación](https://msdn.microsoft.com/library/windows/desktop/ms682606.aspx). Esta configuración le será de utilidad para las aplicaciones que consumen una gran cantidad de memoria y desea ocasionalmente pageout memoria en disco para liberar algo de RAM.

>[AZURE.WARNING] Tenga cuidado cuando se habilitan a las siguientes opciones de configuración en aplicaciones de producción. Se recomienda no habilitarlos en aplicaciones de producción.

* debugHeaderEnabled

    El valor predeterminado es false. Si se establece en true, iisnode agregará un encabezado de respuesta HTTP iisnode de depuración a todas las respuestas HTTP envía que el valor de encabezado de depuración iisnode es una dirección URL. Piezas individuales de información de diagnóstico pueden ser recopilados consultando el fragmento de dirección URL, pero se logra una mucho mejor visualización abriendo la dirección URL en el explorador.

* loggingEnabled

    Esta opción controla el registro de stdout y stderr por iisnode. Iisnode capturará stdout y stderr de procesos de nodo inicia y escribir en el directorio especificado en la configuración de 'logDirectory'. Una vez habilitar, la aplicación va a escribir registros al sistema de archivos y, dependiendo de la cantidad de registros que hace la aplicación, puede haber implicaciones de rendimiento.

* devErrorsEnabled

    Valor predeterminado es false. Cuando establecida en true, iisnode mostrará el código de estado HTTP y el código de error de Win32 en el explorador. El código de win32 será útil para determinados tipos de problemas de depuración.
    
* debuggingEnabled (no habilita en el sitio de producción)

    Esta opción controla la característica de depuración. Iisnode se integra con el inspector de nodo. Al habilitar a esta configuración, habilitar la depuración de la aplicación de nodo. Una vez esté habilitada, iisnode le diseño de los archivos de inspector de nodo necesarios en el directorio de 'debuggerVirtualDir' en la primera solicitud de depuración a la aplicación de nodo. Puede cargar el inspector de nodo al enviar una solicitud a http://yoursite/server.js/debug. Puede controlar el segmento de la dirección URL de depuración con la configuración de 'debuggerPathSegment'. Por debuggerPathSegment predeterminado = 'depurar'. Puede establecer un GUID de ejemplo para que sea más difícil de descubrir otros usuarios.

    Consulte este [vínculo](https://tomasz.janczuk.org/2011/11/debug-nodejs-applications-on-windows.html) para obtener más detalles sobre la depuración.

## <a name="scenarios-and-recommendationstroubleshooting"></a>Recomendaciones y solución de problemas y escenarios

### <a name="my-node-application-is-making-too-many-outbound-calls"></a>Mi aplicación de nodo realiza demasiadas llamadas salientes.

Muchas aplicaciones desee realizar conexiones salientes como parte de su funcionamiento normal. Por ejemplo, cuando se recibe una solicitud, la aplicación de nodo desee ponerse en contacto con una API de REST en otra aplicación y obtener información para procesar la solicitud. ¿Desea usar a un agente activo de mantener al realizar llamadas de http o https. Por ejemplo, podría usar el módulo agentkeepalive como el agente de mantener activo al realizar estas llamadas salientes. Esto se asegura de que los sockets se vuelven a utilizar en la aplicación Web de azure VM y reducir la sobrecarga de la creación de nuevos sockets para cada solicitud de salida. Además, esto se asegura de que está utilizando menor número de sockets para realizar muchas solicitudes de salida y, por tanto, no supere el valor de maxSockets se asigna por máquina virtual. Recomendación en aplicaciones Web de Azure sería establecer el valor de maxSockets agentKeepAlive a un total de 160 sockets por máquina virtual. Esto significa que si tiene 4 node.exe ejecutando en la máquina virtual, quiera establecer el valor de maxSockets agentKeepAlive 40 por node.exe que es 160 total por máquina virtual.

Ejemplo de configuración de agentKeepALive:

```
var keepaliveAgent = new Agent({    
    maxSockets: 40,    
    maxFreeSockets: 10,    
    timeout: 60000,    
    keepAliveTimeout: 300000    
});
```

En este ejemplo, se supone que tiene 4 node.exe ejecutando en la máquina virtual. Si tiene un número diferente de node.exe ejecutando en la máquina virtual, debe modificar el valor de maxSockets configurar en consecuencia.

### <a name="my-node-application-is-consuming-too-much-cpu"></a>Mi aplicación de nodo consume demasiada CPU.

Probablemente obtendrán una recomendación de aplicaciones Web de Azure en el portal sobre alto consumo de cpu. También puede configurar monitores para inspeccionar ciertas [métricas](web-sites-monitor.md). Al comprobar el uso de CPU en el [Panel de Portal de Azure](../application-insights/app-insights-web-monitor-performance.md), compruebe los valores MAX de CPU para no perder los valores de recursos asignadas.
En casos donde cree su aplicación consume demasiada CPU y no se explique por qué, necesitará la aplicación de nodo de perfil.

### 

#### <a name="profiling-your-node-application-on-azure-webapps-with-v8-profiler"></a>Perfiles de la aplicación de nodo en aplicaciones Web de azure con el analizador de la versión 8

Por ejemplo, supongamos, por ejemplo le permite disponer de una aplicación del mundo Hola que desee al perfil como se muestra a continuación:

```
var http = require('http');    
function WriteConsoleLog() {    
    for(var i=0;i<99999;++i) {    
        console.log('hello world');    
    }    
}

function HandleRequest() {    
    WriteConsoleLog();    
}

http.createServer(function (req, res) {    
    res.writeHead(200, {'Content-Type': 'text/html'});    
    HandleRequest();    
    res.end('Hello world!');    
}).listen(process.env.PORT);
```

Vaya a su scm sitio https://yoursite.scm.azurewebsites.net/DebugConsole

Verá un símbolo del sistema, tal como se muestra a continuación. Vaya al directorio de sitios o wwwroot

![](./media/app-service-web-nodejs-best-practices-and-troubleshoot-guide/scm_install_v8.png)

Ejecute el comando "npm instalar a analizador de versión 8"

Esto debe instalar la versión 8-analizador en nodo\_directorio de módulos y todas sus dependencias.
Ahora, modifique la server.js para el perfil de su aplicación.

```
var http = require('http');    
var profiler = require('v8-profiler');    
var fs = require('fs');

function WriteConsoleLog() {    
    for(var i=0;i<99999;++i) {    
        console.log('hello world');    
    }    
}

function HandleRequest() {    
    profiler.startProfiling('HandleRequest');    
    WriteConsoleLog();    
    fs.writeFileSync('profile.cpuprofile', JSON.stringify(profiler.stopProfiling('HandleRequest')));    
}

http.createServer(function (req, res) {    
    res.writeHead(200, {'Content-Type': 'text/html'});    
    HandleRequest();    
    res.end('Hello world!');    
}).listen(process.env.PORT);
```

Los cambios anteriores se la función WriteConsoleLog de perfil y, a continuación, escribir el resultado de perfil al archivo 'profile.cpuprofile' en su sitio de wwwroot. Enviar una solicitud a la aplicación. Verá un archivo de 'profile.cpuprofile' creado en el sitio wwwroot.

![](./media/app-service-web-nodejs-best-practices-and-troubleshoot-guide/scm_profile.cpuprofile.png)

Descargue este archivo y debe abrir este archivo con herramientas de Chrome F12. Presionar F12 en chrome y luego haga clic en la pestaña de perfiles de"". Haga clic en el botón "Cargar". Seleccione el archivo profile.cpuprofile que acaba de descargar. Haga clic en el perfil que ha cargado.

![](./media/app-service-web-nodejs-best-practices-and-troubleshoot-guide/chrome_tools_view.png)

Verá que 95% del tiempo se ha consumido por función WriteConsoleLog como se muestra a continuación. También se muestran los números de línea exacto y los archivos de origen que provocan el problema.

### <a name="my-node-application-is-consuming-too-much-memory"></a>Mi aplicación de nodo consume demasiada memoria.

Probablemente obtendrán una recomendación de aplicaciones Web de Azure en el portal acerca del consumo de memoria alta. También puede configurar monitores para inspeccionar ciertas [métricas](web-sites-monitor.md). Al comprobar el uso de memoria en el [Panel de Portal de Azure](../application-insights/app-insights-web-monitor-performance.md), compruebe los valores máximos en memoria para no perder los valores de recursos asignadas.

#### <a name="leak-detection-and-heap-diffing-for-nodejs"></a>Detección de pérdidas y comparando montón para node.js 

Puede utilizar [memwatch de nodo](https://github.com/lloyd/node-memwatch) para ayudarle a identificar pérdidas de memoria.
Puede instalar memwatch como analizador de versión 8 y editar el código para capturar y diferencias pérdidas de montones para identificar la memoria en la aplicación.

### <a name="my-nodeexes-are-getting-killed-randomly"></a>Mi node.exe están recibiendo eliminado aleatoriamente 

Existen algunas razones por qué podría estar ocurriendo esto:

1.  La aplicación excepciones no detectada: Please verificación d:\\principal\\archivos de registro\\aplicación\\archivo de registro errors.txt para obtener los detalles de la excepción. Este archivo tiene el seguimiento de pila para que pueda corregir la aplicación en función de esta.

2.  Consume demasiada memoria que afecta otros procesos de introducción a la aplicación. Si la memoria VM total se aproxima a 100%, puede suprimir su node.exe mediante el Administrador de procesos para permitir que otros procesos tendrá la oportunidad de hacer el trabajo. Para corregir este error, asegúrese de que su aplicación no está perdiendo memoria o si la aplicación realmente necesita usar una gran cantidad de memoria, por favor, escalar a una máquina virtual más grande con mucha más memoria RAM.

### <a name="my-node-application-does-not-start"></a>No iniciar mi aplicación de nodo

Si su aplicación devuelve 500 errores durante el inicio, podría ser algunos motivos posibles:

1.  Node.exe no está presente en la ubicación correcta. Compruebe la configuración de nodeProcessCommandLine.

2.  Archivo de script principal no está presente en la ubicación correcta. Active web.config y asegúrese de que el nombre del archivo de script principal en la sección controladores coincide con el archivo de comandos principal.

3.  Configuración de Web.config no es correcta: Compruebe los nombres o valores de configuración.

4.  Inicio fría – la aplicación dura demasiada inicio. Si su aplicación tarda más tiempo (maxNamedPipeConnectionRetry \* namedPipeConnectionRetryDelay) / 1000 segundos, iisnode devolverá 500 error. Aumentar los valores de esta configuración para que coincida con la hora de inicio de la aplicación para evitar que iisnode de tiempo de espera y devuelve el error 500.

### <a name="my-node-application-crashed"></a>Mi aplicación de nodo se bloqueó

La aplicación excepciones no detectada: Please verificación d:\\principal\\archivos de registro\\aplicación\\archivo de registro errors.txt para obtener los detalles de la excepción. Este archivo tiene el seguimiento de pila para que pueda corregir la aplicación en función de esta.

### <a name="my-node-application-takes-too-much-time-to-startup-cold-start"></a>Mi aplicación de nodo tarda demasiado tiempo en Inicio (Start fría)

Razón más común es que la aplicación tiene una gran cantidad de archivos en el nodo\_módulos y la aplicación intenta cargar la mayoría de estos archivos durante el inicio. De forma predeterminada, ya que los archivos residen en el recurso compartido de red en aplicaciones Web de Azure, cargar archivos tantos puede tardar algún tiempo.
Algunas soluciones para que sea más rápido son:

1.  Asegúrese de que tiene una estructura de dependencia plano y sin dependencias duplicados mediante npm3 para instalar los módulos.

2.  Intentar diferida cargar el nodo\_módulos y no cargar todos los módulos en el inicio. Esto significa que se debe realizar la llamada a require('module') cuando se necesita realmente dentro de la función que intenta utilizar el módulo.

3.  Aplicaciones Web de Azure ofrece una característica de memoria caché local. Esta característica copia el contenido de la red compartida en el disco local en la máquina virtual. Puesto que los archivos son locales, el tiempo de carga de nodo\_módulos es mucho más rápido. -Esta [documentación](../app-service/app-service-local-cache.md) explica cómo usar la memoria caché Local con más detalle.

## <a name="iisnode-http-status-and-substatus"></a>Subestado y el estado de http IISNODE

Este [archivo de origen](https://github.com/Azure/iisnode/blob/master/src/iisnode/cnodeconstants.h) se enumeran todos lo iisnode de combinación de estado y subestado posibles puede devolver en caso de error.

Habilitar FREB para su aplicación ver el código de error win32 (asegúrese de que la opción Habilitar FREB solo en los sitios de producción no por razones de rendimiento.).

| Estado de http | Subestado http | ¿Motivo posible?                                                                                                                                                                                            
|-------------|----------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------
| 500         | 1000           | Se ha producido algún problema de enviar la solicitud al IISNODE: comprobar si se inició node.exe hacia arriba. Node.exe podría haber bloqueado en el inicio. Compruebe la configuración de web.config errores.                                                                                                                                                                                                                                                                                     |
| 500         | 1001           | -Win32Error 0 x 2 - aplicación no responde a la dirección URL. Compruebe las reglas de reescritura de direcciones URL o si la aplicación express tiene las rutas correctas definidas. -Está ocupado Win32Error 0x6d: canalizaciones con nombre: Node.exe no acepta solicitudes porque la canalización está ocupada. Compruebe el uso de cpu alta. -Otros errores: Compruebe si node.exe se bloqueó.
| 500         | 1002           | Se bloqueó Node.exe; consulte d:\\principal\\archivos de registro\\errors.txt de registro de seguimiento de la pila.                                                                                                                                                                                                                                                                                                                                                                                        |
| 500         | 1003           | Problema de configuración de canalización: nunca debe ver esto, pero si lo hace, la configuración de canalización es incorrecta.                                                                                                                                                                                                                                                                                                                                                          |
| 500         | 1004-1018      | Se produjo un error al enviar la solicitud o procesamiento de node.exe la respuesta. Compruebe si node.exe se bloqueó. comprobar d:\\principal\\archivos de registro\\errors.txt de registro de seguimiento de la pila.                                                                                                                                                                                                                                                                                    |
| 503         | 1000           | Memoria insuficiente para asignar más conexiones de canalizaciones con nombre. ¿Por qué la aplicación consume memoria mucha de verificación. Compruebe el valor de maxConcurrentRequestsPerProcess. Si no infinito y tiene una gran cantidad de solicitudes, aumentar este valor para evitar este error.                                                                                                                                                                                                                                                                                                                  |
| 503         | 1001           | No se pudo distribuir la solicitud a node.exe porque es reciclaje de la aplicación. Después de la aplicación se reciclados, las solicitudes deben representarse normalmente.                                                                                                                                                                                                                                                                                                               |
| 503         | 1002           | No se pudo distribuir el código de error de verificación win32 por motivo real: solicitud para una node.exe.                                                                                                                                                                                                                                                                                                                                                                               |
| 503         | 1003           | Canalización está demasiado ocupado; consulte si nodo consume una gran cantidad de CPU                                                                                                                                                                                                                                                                                                                                                                                                        
                                                                                                                                                                                                                                                                                                            
                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                         
Hay una opción en NODE.exe denominado nodo\_pendiente\_canalización\_instancias. Fuera de aplicaciones Web de azure de forma predeterminada, este valor es 4. Esto significa que node.exe sólo puede aceptar solicitudes de 4 a la vez en la canalización con nombre. En aplicaciones Web de Azure, este valor se establece en 5000 y este valor debe ser suficiente para la mayoría nodo aplicaciones que se ejecutan en aplicaciones Web de azure. No debería ver 503.1003 en aplicaciones Web de azure porque tenemos un valor alto para el nodo\_pendiente\_canalización\_instancias.  |

## <a name="more-resources"></a>Más recursos

Siga estos vínculos para obtener más información acerca de las aplicaciones de node.js en la aplicación de servicio de Azure.

* [Introducción a aplicaciones web de Node.js en la aplicación de servicio de Azure](app-service-web-nodejs-get-started.md)
* [Cómo depurar una aplicación web de Node.js en la aplicación de servicio de Azure](web-sites-nodejs-debug.md)
* [Utilizar módulos de Node.js con aplicaciones de Azure](../nodejs-use-node-modules-azure-apps.md)
* [Aplicaciones de Azure de aplicación de servicio Web: Node.js](https://blogs.msdn.microsoft.com/silverlining/2012/06/14/windows-azure-websites-node-js/)
* [Centro para desarrolladores de Node.js](../nodejs-use-node-modules-azure-apps.md)
* [Explorar la consola de depuración Kudu muy secreta](https://azure.microsoft.com/documentation/videos/super-secret-kudu-debug-console-for-azure-web-sites/)