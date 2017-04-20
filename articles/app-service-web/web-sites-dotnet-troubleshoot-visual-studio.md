<properties 
    pageTitle="Solucionar problemas de una aplicación web de servicio de aplicación de Azure con Visual Studio" 
    description="Obtenga información sobre cómo solucionar problemas de una aplicación web de Azure con depuración remota, seguimiento y herramientas de registro que están integrados en Visual Studio de 2013." 
    services="app-service" 
    documentationCenter=".net" 
    authors="tdykstra" 
    manager="wpickett" 
    editor=""/>

<tags 
    ms.service="app-service" 
    ms.workload="na" 
    ms.tgt_pltfrm="na" 
    ms.devlang="dotnet" 
    ms.topic="article" 
    ms.date="08/29/2016" 
    ms.author="rachelap"/>

# <a name="troubleshoot-a-web-app-in-azure-app-service-using-visual-studio"></a>Solucionar problemas de una aplicación web de servicio de aplicación de Azure con Visual Studio

## <a name="overview"></a>Información general

Este tutorial muestra cómo usar las herramientas de Visual Studio que ayudan a depurar una aplicación web en la [Aplicación de servicio](http://go.microsoft.com/fwlink/?LinkId=529714), ejecutar en [modo de depuración](http://www.visualstudio.com/get-started/debug-your-app-vs.aspx) de forma remota o viendo registros de aplicaciones y los registros del servidor web.

[AZURE.INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]

Aprenderá:

* Las funciones de administración de aplicación web de Azure están disponibles en Visual Studio.
* Cómo usar la vista remota de Visual Studio para realizar cambios rápidos en una aplicación web remoto.
* Cómo ejecutar en modo de depuración de forma remota mientras un proyecto se ejecuta en Azure tanto para una aplicación web para un WebJob.
* Cómo crear registros de seguimiento de la aplicación y verlos mientras la aplicación está creando ellos.
* Cómo ver los registros del servidor web, incluidos mensajes de error detallados y seguimiento de solicitud de error.
* Cómo enviar registros de diagnóstico para el almacenamiento de Azure cuenta y ven allí.

Si tiene Visual Studio Ultimate, también puede usar [IntelliTrace](http://msdn.microsoft.com/library/vstudio/dd264915.aspx) para depurar. IntelliTrace no se trata en este tutorial.

## <a name="prerequisites"></a>Requisitos previos

Este tutorial se trabaja con el entorno de desarrollo, project web y aplicación de Azure web que haya configurado en [Introducción a Azure y ASP.NET][GetStarted]. Las secciones WebJobs, necesitará la aplicación que cree en [Empezar a trabajar con el SDK de Azure WebJobs][GetStartedWJ].

Los ejemplos de código que se muestra en este tutorial son para una aplicación web de MVC C#, pero los procedimientos de solución de problemas son los mismos para las aplicaciones de Visual Basic y formularios Web.

El tutorial se supone que usa Visual Studio de 2015 o 2013. Si está usando Visual Studio de 2013, las características de WebJobs requieren [actualización 4](http://go.microsoft.com/fwlink/?LinkID=510314) o posterior. 

Los registros de transmisiones de características solo funciona para aplicaciones destinadas a .NET Framework 4 o posterior.

## <a name="sitemanagement"></a>Administración y la configuración de aplicación web

Visual Studio proporciona acceso a un subconjunto de las funciones de administración de aplicaciones web y opciones de configuración disponibles en el [Portal de Azure](http://go.microsoft.com/fwlink/?LinkId=529715). En esta sección verá lo que está disponible mediante el **Explorador de servidor**. Para ver las últimas características de integración de Azure, pruebe **Explorer nube** también. Puede abrir dos ventanas en el menú **Ver** . 

1. Si no está ya iniciado sesión Azure en Visual Studio, haga clic en el botón **Conectar a Azure** en el **Explorador de servidores**.

    Es una alternativa instalar un certificado de administración que permite el acceso a su cuenta. Si elige instalar un certificado, haga clic en el nodo de **Azure** en el **Explorador de servidor**y, a continuación, haga clic en **administrar y suscripciones de filtro** en el menú contextual. En el cuadro de diálogo **Administrar suscripciones de Azure** , haga clic en la ficha **certificados** y, a continuación, haga clic en **Importar**. Siga las instrucciones para descargar y, a continuación, importar un archivo de suscripción (también conocido como archivo *.publishsettings* ) para su cuenta de Azure.

    > [AZURE.NOTE]
    > Si se descarga un archivo de suscripción, guardar en una carpeta fuera de los directorios de código de origen (por ejemplo, en la carpeta de descargas) y eliminarlo una vez completada la importación. Un usuario malintencionado que obtiene acceso al archivo de suscripción, puede editar, crear y eliminar los servicios de Azure.

    Para obtener más información sobre cómo conectarse a recursos de Azure desde Visual Studio, vea [Administrar cuentas, suscripciones y funciones administrativas](http://go.microsoft.com/fwlink/?LinkId=324796#BKMK_AccountVCert).

2. En el **Explorador de servidores**, expanda **Azure** y **Aplicación de servicio**.

3. Expanda el grupo de recursos que incluye la aplicación web que creó en [Introducción a Azure y ASP.NET][GetStarted]y, a continuación, haga clic en el nodo de aplicación web y haga clic en **Configuración de la vista**.

    ![Configuración de la vista del explorador de servidores](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-viewsettings.png)

    Aparece la pestaña de la **Aplicación Web de Azure** y puede ver no existe la web app configuración y administración de las tareas que están disponibles en Visual Studio.

    ![Ventana de aplicación Web de Azure](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-configtab.png)

    En este tutorial va a usar el registro y seguimiento de listas desplegables. También deberá usar depuración remota, pero deberá usar otro método para habilitarlo.
   
    Para obtener información sobre los cuadros de configuración de la aplicación y cadenas de conexión en esta ventana, consulte [aplicaciones Web de Azure: cómo las cadenas de la aplicación y el trabajo de cadenas de conexión](http://blogs.msdn.com/b/windowsazure/archive/2013/07/17/windows-azure-web-sites-how-application-strings-and-connection-strings-work.aspx).

    Si desea realizar una tarea de administración de aplicaciones web que no se puede hacer en esta ventana, haga clic en **Abrir en el Portal de administración** para abrir una ventana del explorador para el portal de Azure.

## <a name="remoteview"></a>Archivos de aplicación web de Access en el Explorador de servidores

Normalmente se implementa un proyecto web con el `customErrors` indicador en el archivo Web.config establecido en `On` o `RemoteOnly`, lo que significa que no recibe un mensaje de error útil cuando algo va mal. Para muchos errores todos recibe es una página como una de las siguientes.

**Error de servidor en la aplicación '/':**

![Página de error inútil](./media/web-sites-dotnet-troubleshoot-visual-studio/genericerror.png)

**Se produjo un error:**

![Página de error inútil](./media/web-sites-dotnet-troubleshoot-visual-studio/genericerror1.png)

**El sitio Web no puede mostrar la página**

![Página de error inútil](./media/web-sites-dotnet-troubleshoot-visual-studio/genericerror2.png)

Con frecuencia la manera más sencilla de encontrar la causa del error es habilitar los mensajes de error detallados, el primero de las capturas de pantalla anterior explica cómo hacerlo. Requiere un cambio en el archivo Web.config implementado. Puede editar el archivo *Web.config* del proyecto y redistribuir el proyecto, o crear una [transformación de Web.config](http://www.asp.net/mvc/tutorials/deployment/visual-studio-web-deployment/web-config-transformations) e implementar una versión de depuración, pero hay una forma más rápida: en el **Explorador de soluciones** directamente puede ver y editar archivos en la aplicación web remoto mediante la característica *vista remota* .

1. En el **Explorador de servidores**, expanda **Azure**, expandir la **Aplicación de servicio**, expandir el grupo de recursos que se encuentra la aplicación web en y, a continuación, expanda el nodo para la aplicación web.

    Ver los nodos que proporcionan acceso a archivos de contenido y los archivos de registro de la aplicación web.

2. Expanda el nodo de **archivos** y haga doble clic en el archivo *Web.config* .

    ![Abra el archivo Web.config](./media/web-sites-dotnet-troubleshoot-visual-studio/webconfig.png)

    Visual Studio abre el archivo Web.config de la aplicación web remoto y muestra [remoto] al lado del nombre de archivo en la barra de título.

3. Agregue la línea siguiente a la `system.web` elemento:

    `<customErrors mode="Off"></customErrors>`

    ![Modificar Web.config](./media/web-sites-dotnet-troubleshoot-visual-studio/webconfigedit.png)

4. Actualice el explorador que se muestra el mensaje de error inútil y ahora recibe un mensaje de error detallado, como el ejemplo siguiente:

    ![Mensaje de error detallado](./media/web-sites-dotnet-troubleshoot-visual-studio/detailederror.png)

    (El error que se muestra se creó mediante la adición de la línea que se muestra en rojo para *Views\Home\Index.cshtml*).

Editar el archivo Web.config es sólo un ejemplo de casos en los que la capacidad de leer y editar archivos en la aplicación web de Azure Asegúrese de solución de problemas.

## <a name="remotedebug"></a>Aplicaciones web de depuración remota

Si el mensaje de error detallado no proporciona suficiente información, y no puede volver a crear el error localmente, es otra forma de solución de problemas ejecutar en modo de depuración de forma remota. Puede establecer puntos de interrupción, manipular memoria directamente, paso a través de código e incluso cambiar la ruta de acceso de código. 

Depuración remota no funciona en las ediciones Express de Visual Studio.

Esta sección muestra cómo depurar de forma remota mediante el proyecto que cree en [Introducción a Azure y ASP.NET][GetStarted].

1. Abra el proyecto web que creó en [Introducción a Azure y ASP.NET][GetStarted].

1. Abra *Controllers\HomeController.cs*.

2. Eliminar la `About()` método e insertar el siguiente código en su lugar.

        public ActionResult About()
        {
            string currentTime = DateTime.Now.ToLongTimeString();
            ViewBag.Message = "The current time is " + currentTime;
            return View();
        }

2. [Establecer un punto de interrupción](http://www.visualstudio.com/get-started/debug-your-app-vs.aspx) en el `ViewBag.Message` línea.

1. En el **Explorador de soluciones**, haga clic en el proyecto y haga clic en **Publicar**.

2. En la lista desplegable de **perfil** , seleccione el mismo perfil que utilizó en [Introducción a Azure y ASP.NET][GetStarted].

3. Haga clic en la pestaña **configuración** y cambiar la **configuración** para **Depurar**y, a continuación, haga clic en **Publicar**.

    ![Publicar en modo de depuración](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-publishdebug.png)

4. Después de la implementación finaliza y el explorador se abre con la URL de Azure de la aplicación web, cierre el explorador.

5. En el **Explorador de servidores**, haga clic en la aplicación web y, a continuación, haga clic en **Adjuntar depurador**. 

    ![Adjuntar depurador](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-attachdebugger.png)

    El explorador se abrirá automáticamente a la página principal que se ejecuta en Azure. Es posible que tenga que esperar 20 segundos mientras Azure configura el servidor para depuración. Este retraso sólo ocurre la primera vez que se ejecuta en modo de depuración en una aplicación web. Veces subsiguientes en las próximas 48 horas al iniciar depuración nuevamente allí no será un retraso.

    **Nota:** Si tiene algún problema para iniciar a la depuración, intente hacer mediante el **Explorador de nube** en lugar del **Explorador de servidores**.

6. En el menú, haga clic en **acerca de** .

    Visual Studio se detiene en el punto de interrupción y el código se ejecuta en Azure, no en el equipo local.

7. Desplace el puntero sobre la `currentTime` variable para ver el valor de hora.

    ![Variable de vista en modo de depuración que se ejecuta en Azure](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-debugviewinwa.png)

    El tiempo que verá es la hora del servidor de Azure, que puede estar en una zona de tiempo diferente a su equipo local.

8. Escriba un nuevo valor para el `currentTime` variable, como "Ahora se ejecuta en Azure".

5. Presione F5 para continuar la ejecución.

    La página sobre que se ejecuta en Azure muestra el nuevo valor que ha introducido en la variable currentTime.

    ![Acerca de la página con el nuevo valor](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-debugchangeinwa.png)

## <a name="remotedebugwj"></a>WebJobs depuración remota

Esta sección muestra cómo depurar remotamente con la aplicación web y project que crea en [Empezar a trabajar con el SDK de WebJobs de Azure](websites-dotnet-webjobs-sdk.md). 

Las características que se muestran en esta sección sólo están disponibles en de 2013 Visual Studio con Update 4 o posterior. 

Depuración remota solo funciona con WebJobs continuo. No admite depuración WebJobs programadas y a petición.

1. Abra el proyecto web que creó en [Empezar a trabajar con el SDK de Azure WebJobs][GetStartedWJ].

1. En el proyecto ContosoAdsWebJob, abra *Functions.cs*.

2. [Establecer un punto de interrupción](http://www.visualstudio.com/get-started/debug-your-app-vs.aspx) en la primera instrucción de la `GnerateThumbnail` método.

    ![Punto de interrupción establecido](./media/web-sites-dotnet-troubleshoot-visual-studio/wjbreakpoint.png)

1. En el **Explorador de soluciones**, haga clic en el proyecto de web (no en el proyecto WebJob) y haga clic en **Publicar**.

2. En la lista desplegable de **perfil** , seleccione el mismo perfil que utilizó en [Empezar a trabajar con el SDK de WebJobs de Azure](websites-dotnet-webjobs-sdk.md).

3. Haga clic en la pestaña **configuración** y cambiar la **configuración** para **Depurar**y, a continuación, haga clic en **Publicar**.

    Visual Studio implementa la web y los proyectos de WebJob y se abre el explorador a la dirección URL de Azure de la aplicación web.

5. En el **Explorador de servidores** , expanda **Azure > aplicación de servicio > su grupo de recursos > la aplicación web > WebJobs > continuo**y, a continuación, haga clic con el botón **ContosoAdsWebJob**.

7. Haga clic en **adjuntar a depurador**. 

    ![Adjuntar depurador](./media/web-sites-dotnet-troubleshoot-visual-studio/wjattach.png)

    El explorador se abrirá automáticamente a la página principal que se ejecuta en Azure. Es posible que tenga que esperar 20 segundos mientras Azure configura el servidor para depuración. Este retraso sólo ocurre la primera vez que se ejecuta en modo de depuración en una aplicación web. La próxima vez que se asocia al depurador allí no será un retraso, si lo hace en 48 horas.

6. En el explorador web que se abre la página principal de Contoso anuncios, cree un nuevo anuncio. 

    Creación de un anuncio provoca un mensaje de cola que se creará, que se toma el WebJob y procesan. Cuando el SDK WebJobs llama a la función para procesar el mensaje de cola, el código alcance el punto de interrupción.

7. Cuando el depurador interrumpe en el punto de interrupción, puede examinar y cambiar los valores de variables mientras ejecuta el programa la nube. En la siguiente ilustración el depurador muestra el contenido del objeto blobInfo que se pasó al método GenerateThumbnail.

    ![objeto blobInfo en depurador](./media/web-sites-dotnet-troubleshoot-visual-studio/blobinfo.png)
 
5. Presione F5 para continuar la ejecución.

    El método GenerateThumbnail finaliza la creación de la miniatura.

6. En el explorador, actualice la página de índice y verá la miniatura.

6. En Visual Studio, presione MAYÚS + F5 para detener la depuración.

7. En el **Explorador de servidores**, haga clic en el nodo ContosoAdsWebJob y haga clic en **Panel de vista**.

8. Inicie sesión con sus credenciales de Azure y, a continuación, haga clic en el nombre de WebJob para ir a la página para su WebJob. 

    ![Haga clic en ContosoAdsWebJob](./media/web-sites-dotnet-troubleshoot-visual-studio/clickcaw.png)

    El panel muestra la función GenerateThumbnail ejecutada recientemente.

    (La próxima vez que haga clic en **Panel de vista**, no tiene que iniciar sesión y el explorador irá directamente a la página para su WebJob).

9. Haga clic en el nombre de la función para ver detalles sobre la ejecución de la función.

    ![Detalles de la función](./media/web-sites-dotnet-troubleshoot-visual-studio/funcdetails.png)

Si la función [escribir registros](websites-dotnet-webjobs-sdk-storage-queues-how-to.md#logs), puede hacer clic en **ToggleOutput** para verlos.

## <a name="notes-about-remote-debugging"></a>Notas acerca de depuración remota

* No se recomienda ejecutar en modo de depuración en producción. Si la aplicación web de producción no está distribuida en varias instancias de servidor, depuración impedirá el servidor web responder a otras solicitudes. Si tiene varias instancias de servidor web, al adjuntar la depuración obtendrá una instancia aleatoria y no hay forma para asegurarse de que las solicitudes subsiguientes explorador irá a esa instancia. Además, normalmente no se implementa una versión de depuración en producción y las optimizaciones del compilador versiones de lanzamiento pueden resultar imposible mostrar lo que sucede línea por línea en el código fuente. Para solucionar problemas de producción, el mejor recurso es la aplicación web y seguimiento de registros del servidor.

* Evitar larga se detiene en los puntos de interrupción cuando remoto de depuración. Azure trata un proceso que se detiene durante más de unos minutos como un proceso no responde y se cierra.

* Mientras está depurar, el servidor está enviando datos a Visual Studio, que podría afectar cargos de ancho de banda. Para obtener información sobre las tasas de ancho de banda, consulte [Precios de Azure](/pricing/calculator/).

* Asegúrese de que el `debug` atributo de la `compilation` en el archivo *Web.config* está establecida en true. Se establece en true de forma predeterminada cuando se publica una configuración de compilación de depuración.

        <system.web>
          <compilation debug="true" targetFramework="4.5" />
          <httpRuntime targetFramework="4.5" />
        </system.web>

* Si cree que el depurador no recorrer todo el código que desea depurar, es posible que tenga que cambiar la configuración de sólo mi código.  Para obtener más información, vea [restringir paso a paso para sólo mi código](http://msdn.microsoft.com/library/vstudio/y740d9d3.aspx#BKMK_Restrict_stepping_to_Just_My_Code).

* Un temporizador se inicia en el servidor al habilitar la característica de depuración remota y 48 horas después de la característica se desactiva automáticamente. Este límite de 48 horas se hace por razones de seguridad y el rendimiento. Puede activar la característica fácilmente en tantas veces como sea necesario. Se recomienda dejarlo deshabilitado no depuración activa.

* Puede asociar manualmente el depurador a cualquier proceso, no solo el proceso de aplicación de web (w3wp.exe). Para obtener más información sobre cómo usar el modo de depuración en Visual Studio, vea [depuración en Visual Studio](http://msdn.microsoft.com/library/vstudio/sc65sadd.aspx).

## <a name="logsoverview"></a>Información general de registros de diagnóstico

Una aplicación de ASP.NET que se ejecuta en una aplicación web de Azure puede crear los siguientes tipos de registros:

* **Registros de seguimiento de la aplicación**<br/>
  La aplicación crea estos registros mediante una llamada a métodos de la clase [System.Diagnostics.Trace](http://msdn.microsoft.com/library/system.diagnostics.trace.aspx) .
* **Registros del servidor Web**<br/>
  El servidor web crea una entrada de registro para cada solicitud HTTP a la aplicación web.
* **Registros de mensajes de error detallados**<br/>
  El servidor web, crea una página HTML con información adicional de errores en las peticiones HTTP (aquellos que provocan el código de estado 400 o mayor). 
* **No se pudo registros de seguimiento de solicitud**<br/>
  El servidor web crea un archivo XML con información de seguimiento detallada de las solicitudes HTTP erróneas. El servidor web también proporciona un archivo XSL para dar formato XML en un explorador.
  
Registro afecta al rendimiento de la aplicación de web, Azure le ofrece la capacidad para habilitar o deshabilitar a cada tipo de registro según sea necesario. Para los registros de aplicación, puede especificar que deben escribirse solo registros encima de un determinado nivel de gravedad. Al crear una nueva aplicación web, de forma predeterminada todo el registro está deshabilitado.

Los registros se escriben en archivos de una carpeta de *archivos de registro* en el sistema de archivos de la aplicación web y están disponibles a través de FTP. Registros de servidor Web y aplicaciones también pueden escribirse a una cuenta de almacenamiento de Azure. Puede conservar un mayor volumen de registros en una cuenta que es posible en el sistema de archivos de almacenamiento. Está limitado a un máximo de 100 megabytes de registros cuando se usa el sistema de archivos. (Registros del sistema de archivos son solo de retención a corto plazo. Azure elimina los archivos de registro antiguos para crear espacio para nuevos después de que se ha alcanzado el límite.)  

## <a name="apptracelogs"></a>Crear y ver los registros de seguimiento de aplicación

En esta sección tendrá que hacer las tareas siguientes:

* Agregar instrucciones de seguimiento para el proyecto web que creó en [Introducción a Azure y ASP.NET][GetStarted].
* Ver los registros cuando se ejecuta el proyecto localmente.
* Ver los registros como generadas por la aplicación que se ejecuta en Azure. 

Para obtener información acerca de cómo crear la aplicación se inicia en WebJobs, vea [cómo trabajar con el almacenamiento de Azure cola mediante el SDK de WebJobs - cómo escribir registros](websites-dotnet-webjobs-sdk-storage-queues-how-to.md#logs). Las siguientes instrucciones para ver los registros y controlar cómo se almacenaron en Azure aplicarán también en los registros de aplicación creados por WebJobs. 

### <a name="add-tracing-statements-to-the-application"></a>Agregar instrucciones de seguimiento a la aplicación

1. Abra *Controllers\HomeController.cs*y reemplace la `Index`, `About`, y `Contact` métodos con el siguiente código para agregar `Trace` instrucciones y un `using` declaración de `System.Diagnostics`:

        public ActionResult Index()
        {
            Trace.WriteLine("Entering Index method");
            ViewBag.Message = "Modify this template to jump-start your ASP.NET MVC application.";
            Trace.TraceInformation("Displaying the Index page at " + DateTime.Now.ToLongTimeString());
            Trace.WriteLine("Leaving Index method");
            return View();
        }
        
        public ActionResult About()
        {
            Trace.WriteLine("Entering About method");
            ViewBag.Message = "Your app description page.";
            Trace.TraceWarning("Transient error on the About page at " + DateTime.Now.ToShortTimeString());
            Trace.WriteLine("Leaving About method");
            return View();
        }
        
        public ActionResult Contact()
        {
            Trace.WriteLine("Entering Contact method");
            ViewBag.Message = "Your contact page.";
            Trace.TraceError("Fatal error on the Contact page at " + DateTime.Now.ToLongTimeString());
            Trace.WriteLine("Leaving Contact method");
            return View();
        }       

2. Agregar un `using System.Diagnostics;` instrucción a la parte superior del archivo.
                
### <a name="view-the-tracing-output-locally"></a>Ver los resultados del seguimiento localmente

3. Presione F5 para ejecutar la aplicación en modo de depuración.

    La escucha de seguimiento predeterminado escribe todos los resultados de seguimiento en la ventana de **resultados** , junto con otros resultados de la depuración. La ilustración siguiente muestra el resultado de las instrucciones de seguimiento que haya agregado a la `Index` método.

    ![En la ventana de depuración de seguimiento](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-debugtracing.png)

    Los pasos siguientes muestran cómo ver los resultados del seguimiento en una página web, sin compilar en modo de depuración.

2. Abra el archivo Web.config de la aplicación (lo encuentra en la carpeta de proyecto) y agregue una `<system.diagnostics>` elemento al final del archivo inmediatamente antes de cerrar `</configuration>` elemento:

        <system.diagnostics>
            <trace>
              <listeners>
                <add name="WebPageTraceListener"
                    type="System.Web.WebPageTraceListener, 
                    System.Web, 
                    Version=4.0.0.0, 
                    Culture=neutral,
                    PublicKeyToken=b03f5f7f11d50a3a" />
              </listeners>
            </trace>
          </system.diagnostics>

    La `WebPageTraceListener` le permite ver los resultados de seguimiento a través de `/trace.axd`.

3. Agregar un <a href="http://msdn.microsoft.com/library/vstudio/6915t83k(v=vs.100).aspx">elemento de seguimiento</a> bajo `<system.web>` en el archivo Web.config, como el ejemplo siguiente:

        <trace enabled="true" writeToDiagnosticsTrace="true" mostRecent="true" pageOutput="false" />

3. Presione CTRL + F5 para ejecutar la aplicación.

4. En la barra de direcciones de la ventana del explorador, agregue *trace.axd* a la dirección URL y, a continuación, presione ENTRAR (la dirección URL será similar a http://localhost:53370/trace.axd).

5. En la página de **Seguimiento de la aplicación** , haga clic en **Ver detalles** en la primera línea (no en la línea de BrowserLink).

    ![Trace.axd](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-traceaxd1.png)

    Aparece la página de **Detalles de la solicitud** y, en la sección **Información de seguimiento** se ve el resultado de las instrucciones de seguimiento que haya agregado a la `Index` método.

    ![Trace.axd](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-traceaxd2.png)

    De forma predeterminada, `trace.axd` solo está disponible localmente. Si desea que esté disponible desde una aplicación web remoto, puede agregar `localOnly="false"` a la `trace` elemento en el archivo *Web.config* , como se muestra en el ejemplo siguiente:

        <trace enabled="true" writeToDiagnosticsTrace="true" localOnly="false" mostRecent="true" pageOutput="false" />

    Sin embargo, habilitar `trace.axd` en una producción Online generalmente no se recomienda por motivos de seguridad y, a continuación, en las secciones siguientes, verá una manera más fácil de leer los registros de seguimiento en una aplicación web de Azure.

### <a name="view-the-tracing-output-in-azure"></a>Ver los resultados del seguimiento de Azure

1. En el **Explorador de soluciones**, haga clic en el proyecto web y haga clic en **Publicar**.

2. En el cuadro de diálogo **Publicar en Web** , haga clic en **Publicar**.

    Después de que Visual Studio publica la actualización, se abre una ventana del explorador a la página de inicio (suponiendo que no desactive **Dirección URL de destino** en la pestaña **conexión** ).

3. En el **Explorador de servidores**, haga clic en la aplicación web y seleccione **Ver registros de transmisión**. 

    ![Ver registros de transmisión en el menú contextual](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-viewlogsmenu.png)

    La ventana de **salida** muestra que está conectado al servicio de transmisión de registro y agrega una línea de notificación cada minuto que pasa sin un registro para mostrar.

    ![Ver registros de transmisión en el menú contextual](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-nologsyet.png)

4. En la ventana del explorador que muestra la página de inicio de la aplicación, haga clic en **contacto**.

    En unos segundos la salida desde el nivel de error de seguimiento que ha agregado a la `Contact` método aparece en la ventana de **resultados** .

    ![Seguimiento de error en la ventana de resultados](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-errortrace.png)

    Visual Studio solo muestra seguimientos de nivel de error porque es el valor predeterminado cuando se habilita el registro de servicio de supervisión. Cuando se crea una nueva aplicación web de Azure, todo está deshabilitado de forma predeterminada, tal y como hemos visto cuando se abre la página de configuración de una versión anterior:

    ![Cerrar la sesión de aplicación](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-apploggingoff.png)


    Sin embargo, si se selecciona **Ver registros de transmisión**, Visual Studio cambia automáticamente **Aplicación Logging(File System)** a **Error**, lo que significa notificará los registros de error. Para ver todos los registros de seguimiento, puede cambiar esta configuración a **detallado**. Al seleccionar un nivel de gravedad inferior a error, también se muestran todos los registros para los niveles de gravedad superior. Así que al seleccionar detallado, también puede ver información, advertencia y registros de errores.  

4. En el **Explorador de servidores**, haga clic en la aplicación web y, a continuación, haga clic en **Configuración de la vista** como lo hizo anteriormente.

5. Cambiar el **Registro de aplicación (sistema de archivos)** a **detallado**y, a continuación, haga clic en **Guardar**.
 
    ![Establecer el nivel de seguimiento en detallado](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-applogverbose.png)

6. En la ventana del explorador que ahora se muestra la página de **contacto** , haga clic en **Inicio**, a continuación, haga clic en **acerca de**y, a continuación, haga clic en **contacto**.

    En unos segundos, la ventana de **salida** muestra todos los resultados del seguimiento.

    ![Resultados del seguimiento detallado](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-verbosetraces.png)

    En esta sección había habilitado y había deshabilitado registro mediante la configuración de la aplicación web de Azure. También puede habilitar y deshabilitar los agentes de escucha modificando el archivo Web.config. Sin embargo, modificar el archivo Web.config hace que el dominio de aplicación para reciclaje, aunque no siga habilitar el registro a través de la configuración de la aplicación web. Si el problema tarda mucho tiempo para reproducir, o es intermitente, reciclaje del dominio de aplicación podrían "solucionar" y forzar que esperar hasta que vuelva a ocurrir. Habilitar diagnósticos en Azure no hacerlo, para que pueda empezar capturar información de error inmediatamente.

### <a name="output-window-features"></a>Características de la ventana de salida

La pestaña de **Azure registros** de la ventana de **resultados** tiene varios botones y un cuadro de texto:

![Botones de registros](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-icons.png)

Estos realizan las siguientes funciones:

* Borrar la ventana de **resultados** .
* Activar o desactivar el ajuste de línea.
* Iniciar o detener la supervisión de registros.
* Especifique los archivos de registro para supervisar.
* Descargue los registros.
* Filtrar registros en función de una cadena de búsqueda o una expresión regular.
* Cierre la ventana de **resultados** .

Si especifica una cadena de búsqueda o expresiones regulares, Visual Studio filtra la información de registro en el cliente. Esto significa que puede especificar los criterios después de que los registros se muestran en la ventana de **resultados** y puede cambiar los criterios de filtrado sin tener que volver a crear los registros.

## <a name="webserverlogs"></a>Ver registros de servidor web

Registros del servidor Web graban todas las actividades HTTP para la aplicación web. Debe habilitarlos para la aplicación web e indicar a Visual Studio que desea supervisar para poder verlas en la ventana de **resultados** . 

1. En la pestaña **Configuración de aplicación Web de Azure** que abrió desde el **Explorador de servidores**, cambie el registro de servidor Web a **en**y, a continuación, haga clic en **Guardar**.

    ![Habilitar el registro de servidor web](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-webserverloggingon.png)

2. En la ventana de **resultados** , haga clic en el botón **especificar qué registros de Azure para supervisar** .
    
    ![Especificar qué registros de Azure para supervisar](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-specifylogs.png)

3. En el cuadro de diálogo **Opciones de registro de Azure** , seleccione **los registros de servidor Web**y, a continuación, haga clic en **Aceptar**.

    ![Supervisar los registros del servidor web](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-monitorwslogson.png)

4. En la ventana del explorador que muestra la aplicación web, haga clic en **Inicio**, a continuación, haga clic en **acerca de**y, a continuación, haga clic en **contacto**.

    Los registros de aplicaciones, en general, aparezcan en primer lugar, seguido de los registros del servidor web. Es posible que tenga que esperar unos instantes para los registros que aparezca. 

    ![Registros de servidor Web en la ventana de resultados](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-wslogs.png)


De forma predeterminada, cuando se habilita primero los registros de servidor web mediante Visual Studio, Azure escribe los registros en el sistema de archivos. Como alternativa, puede usar el portal de Azure para especificar que el servidor web registros se deben escribir en un contenedor de blob en una cuenta de almacenamiento.

Si usar el portal para habilitar el registro a una cuenta de Azure almacenamiento de servidor web y, a continuación, deshabilitar el registro en Visual Studio, al volver a habilitar registro en Visual Studio se restaura la configuración de la cuenta de almacenamiento. 

## <a name="detailederrorlogs"></a>Ver registros de mensajes de error detallados

Registros de errores detallados proporcionan información adicional acerca de las solicitudes HTTP que producen códigos de error de respuesta (400 o superior). Para poder verlas en la ventana de **resultados** , tendrá que habilitarlos para la aplicación web e indicar a Visual Studio que desea supervisar.

1. En la pestaña **Configuración de aplicación Web de Azure** que abrió desde el **Explorador de servidores**, cambiar **Mensajes de Error detallados** a **en**y, a continuación, haga clic en **Guardar**.

    ![Habilitar mensajes de error detallados](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-detailedlogson.png)

2. En la ventana de **resultados** , haga clic en el botón **especificar qué registros de Azure para supervisar** .

3. En el cuadro de diálogo **Opciones de registro de Azure** , haga clic en **todos los registros**y, a continuación, haga clic en **Aceptar**.

    ![Supervisar todos los registros](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-monitorall.png)

4. En la barra de direcciones de la ventana del explorador, agregue un carácter adicional a la dirección URL que pueden provocar un error 404 (por ejemplo, `http://localhost:53370/Home/Contactx`), y presione ENTRAR.

    Después de varios segundos el registro de errores detallado aparece en la ventana de **resultados** de Visual Studio.

    ![Registro de errores detallado en la ventana de resultados](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-detailederrorlog.png)

    Control + clic en el vínculo para ver el resultado del registro tiene el formato de un explorador:

    ![Registro de errores detallado en la ventana del explorador](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-detailederrorloginbrowser.png)

## <a name="downloadlogs"></a>Descargar los registros del sistema de archivos

También se pueden descargar todos los registros que se pueden supervisar en la ventana de **resultados** como un archivo *.zip* . 

1. En la ventana de **resultados** , haga clic en **Descargar registros de transmisión**.

    ![Botones de registros](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-downloadicon.png)

    Abre el Explorador de archivos a su carpeta de *descargas* con el archivo descargado seleccionado.

    ![Archivo descargado](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-downloadedfile.png)

2. Extraer el archivo *.zip* y verá la siguiente estructura de carpetas:

    ![Archivo descargado](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-logfilefolders.png)

    * Registros de seguimiento de la aplicación están en archivos *.txt* en la carpeta *LogFiles\Application* .
    * Registros del servidor Web se encuentran en archivos *.log* en la carpeta *LogFiles\http\RawLogs* . Puede usar una herramienta como [El analizador de registro](http://www.microsoft.com/download/details.aspx?displaylang=en&id=24659) para ver y manipular estos archivos.
    * Registros de mensajes de error detallados están en archivos *HTML* en la carpeta *LogFiles\DetailedErrors* .

    (Es la carpeta de *implementaciones* para los archivos creados por el control de código fuente publicación; no hay nada relacionadas con la publicación de Visual Studio. La carpeta *Git* es para seguimientos de control de origen de publicación y el registro de servicio de transmisión de archivos.)  

## <a name="storagelogs"></a>Ver registros de almacenamiento

También puede enviar los registros de seguimiento de aplicación a una cuenta de almacenamiento de Azure y poder verlos en Visual Studio. Para hacer que debe crear una cuenta de almacenamiento, habilitar registros de almacenamiento en el portal de clásico y verlos en la ficha **registros** de la ventana de la **Aplicación Web de Azure** .

Puede enviar registros a uno o todos los tres destinos de:

* El sistema de archivos.
* Tablas de la cuenta de almacenamiento.
* BLOB de la cuenta de almacenamiento.

Puede especificar un nivel de gravedad diferentes para cada destino. 

Tablas que sea fácil de ver los detalles de registros en línea y que admiten la transmisión; puede consultar registros en tablas y ver registros nuevos mientras se crean. BLOB que sea más fácil para descargar registros en archivos y analizarlos con HDInsight, ya que HDInsight sabe cómo trabajar con el almacenamiento de blobs. Para obtener más información, consulte **Hadoop y MapReduce** en las [Opciones de almacenamiento de datos (creación nube aplicaciones del mundo Real con Azure)](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/data-storage-options).

Actualmente tiene registros del sistema de archivo establecidos en el nivel de detalle; los siguientes pasos le guiará por configurar los registros de nivel de información para ir a la tabla de la cuenta de almacenamiento. Nivel de información significa todos los registros que se creó mediante una llamada a `Trace.TraceInformation`, `Trace.TraceWarning`, y `Trace.TraceError` se mostrará, pero no los registros creados mediante una llamada a `Trace.WriteLine`.

Cuentas de almacenamiento ofrecen más almacenamiento y más larga duración retención para los registros en comparación con el sistema de archivos. Otra ventaja de enviar registros de seguimiento de aplicación al almacenamiento es obtiene información adicional con cada registro que no recibe del sistema de archivos de registro.

5. Haga clic en **almacenamiento** bajo el nodo de Azure y, a continuación, haga clic en **Crear cuenta de almacenamiento**.

![Crear cuenta de almacenamiento](./media/web-sites-dotnet-troubleshoot-visual-studio/createstor.png)

3. En el cuadro de diálogo **Crear cuenta de almacenamiento** , escriba un nombre para la cuenta de almacenamiento. 

    El nombre debe ser debe ser único (ninguna otra cuenta de almacenamiento de Azure puede tener el mismo nombre). Si el nombre que escribe está ya en uso tendrá la oportunidad para cambiarlo.

    La dirección URL para tener acceso a su cuenta de almacenamiento será *{nombre}*. Core.Windows.NET.. 

5. Establezca la lista desplegable **grupo afinidad o la región** a la región más cercana a usted.

    Esta opción especifica qué centro de datos de Azure hospedará su cuenta de almacenamiento. Este tutorial su elección no hacer una gran diferencia, pero para una aplicación web de producción que desea que el servidor web y su cuenta de almacenamiento en la misma región para minimizar latencia y datos de los gastos de salida. En una región más cerca posible de los exploradores tener acceso a la aplicación web para minimizar la latencia, debe ejecutar la aplicación web (que creará más adelante).

6. Establecer la lista de la lista desplegable de **replicación** **redundantes localmente**. 

    Cuando geo replicación está habilitada para una cuenta de almacenamiento, es replicar el contenido almacenado en un centro de datos secundario para habilitar la migración tras error a esa ubicación en el caso de un desastre importante en la ubicación principal. Replicación geo puede tengan costes adicionales. Para las cuentas de prueba y desarrollo, generalmente no desea pagar geo replicación. Para obtener más información, vea [crear, administrar, o eliminar una cuenta de almacenamiento](../storage-create-storage-account/#replication-options).

5. Haga clic en **crear**. 

    ![Nueva cuenta de almacenamiento](./media/web-sites-dotnet-troubleshoot-visual-studio/newstorage.png)  

1. En la ventana de Visual Studio **Azure Web App** , haga clic en la pestaña de **registros** y, a continuación, haga clic en **Configurar el registro en el Portal de administración**.

    <!-- todo:screenshot of new portal if the VS page link goes to new portal -->
    ![Configurar el registro](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-configlogging.png)

    Se abrirá la ficha **Configurar** en el portal de clásico para la aplicación web.

2. En la ficha de **Configurar** del portal clásica, desplácese hacia abajo hasta la sección de diagnósticos de la aplicación y, a continuación, cambie **El registro de aplicación (almacenamiento de tablas)** **en**.

3. Cambiar el **nivel de registro** para **obtener información**.

4. Haga clic en **Administrar almacenamiento de tablas**.

    ![Haga clic en administrar TableStorage](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-stgsettingsmgmtportal.png)

    En el cuadro **Administrar almacenamiento de tablas de los diagnósticos de aplicación** , puede elegir la cuenta de almacenamiento si tiene más de uno. Puede crear una tabla nueva o usar una existente.

    ![Administrar el almacenamiento de tablas](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-choosestorageacct.png)

6. En el cuadro **Administrar almacenamiento de tablas de los diagnósticos de aplicación** , haga clic en la marca de verificación para cerrar el cuadro.

6. En la ficha de **Configurar** del portal clásica, haga clic en **Guardar**.

7. En la ventana del explorador que muestra la aplicación web app, haga clic en **Inicio**, a continuación, haga clic en **acerca de**y, a continuación, haga clic en **contacto**.

    La información de registro creada mediante una exploración estas páginas web se escribirá en la cuenta de almacenamiento.

8. En la pestaña de **registros** de la ventana de la **Aplicación Web de Azure** en Visual Studio, haga clic en **Actualizar** en **Resumen de diagnóstico**.

    ![Haga clic en actualizar](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-refreshstorage.png)

    La sección **Diagnóstico resumen** muestra registros de los últimos 15 minutos de forma predeterminada. Puede cambiar el período para ver más registros. 

    (Si recibe un error "no se encontró la tabla", compruebe examinando las páginas que realizar el seguimiento después de habilitar **El registro de aplicación (almacenamiento)** y después haga clic en **Guardar**).

    ![Registros de almacenamiento](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-storagelogs.png)

    Observe que en esta vista se ven **El identificador de proceso** y **Subproceso** para cada registro, que no recibe en los registros del sistema de archivos. Puede ver los campos adicionales, consulte la tabla de almacenamiento de Azure directamente.

8. Haga clic en **Ver todos los registros de la aplicación**.

    La tabla de registro de seguimiento aparece en el Visor de la tabla de almacenamiento de Azure.
   
    (Si recibe un error de "secuencia no contiene elementos", abra el **Explorador de servidores**, expanda el nodo para su cuenta de almacenamiento en el nodo de **Azure** y, a continuación, haga clic en **tablas** y haga clic en **Actualizar**.)

    ![Almacenamiento de registros en la vista de tabla](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-tracelogtableview.png)

    Esta vista muestra campos adicionales que no ve en cualquier otra vista. Esta vista permite filtrar registros mediante especial IU de generador de consultas para crear una consulta. Para obtener más información, vea Trabajar con recursos de la tabla - entidades de filtrado de [Recursos de almacenamiento de exploración con el Explorador de servidores](http://msdn.microsoft.com/library/ff683677.aspx).

7. Para ver los detalles de una sola fila, haga doble clic en una de las filas.

    ![Tabla de traza en el Explorador de servidores](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-tracetablerow.png)

## <a name="failedrequestlogs"></a>Ver registros de seguimiento de solicitudes erróneas

Registros de errores de solicitudes de seguimiento son útiles cuando necesita conocer los detalles de cómo IIS controla una solicitud HTTP, en escenarios como problemas de autenticación o reescritura de URL. 

Aplicaciones web de Azure use la misma funcionalidad de seguimiento de solicitud errónea que ha sido disponible con IIS 7.0 o posterior. No tiene acceso a la configuración de IIS que configurar obtengan registran qué errores, sin embargo. Cuando se habilita el seguimiento de solicitudes con error, se capturan todos los errores. 

Puede habilitar el seguimiento de error en las peticiones mediante Visual Studio, pero no puede ver en Visual Studio. Estos registros son archivos XML. El servicio de registro de transmisiones sólo supervisa los archivos que se consideran legibles en modo de texto sin formato: archivos *.txt*, *.html*y *. log* .

Puede ver registros de errores de solicitudes de seguimiento en un explorador directamente a través de FTP o localmente después de usar una herramienta FTP descargarlos en el equipo local. En esta sección podrá verlos en un explorador directamente.

1. En la pestaña **configuración** de la ventana de la **Aplicación Web de Azure** que abrió desde el **Explorador de servidores**, cambiar la **Solicitud de seguimiento de error** a **en**y, a continuación, haga clic en **Guardar**.

    ![Habilitar el seguimiento de solicitudes erróneas](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-failedrequeston.png)

4. En la barra de direcciones de la ventana del explorador que muestra la aplicación web, agregue un carácter adicional a la dirección URL y haga clic en ENTRAR para que se produzca un error 404.

    Esto hace que un registro de seguimiento de solicitud errónea que debe crearse y los pasos siguientes muestran cómo ver o descargar el registro.

2. En Visual Studio, en la pestaña **configuración** de la ventana de la **Aplicación Web de Azure** , haga clic en **Abrir en el Portal de administración**.

3. En el módulo de **configuración** del [Portal de Azure](https://portal.azure.com) para la aplicación web, haga clic en **credenciales de implementación**y, a continuación, escriba un nuevo nombre de usuario y contraseña.

    ![Nuevo nombre de usuario FTP y contraseña](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-enterftpcredentials.png)

    ** Al iniciar sesión, debe usar el nombre de usuario completo con el nombre de aplicación web con el prefijo a él. Por ejemplo, si escribe "myid" como nombre de usuario y el sitio es "myexample", que inicie sesión como "myexample\myid".

5. En una nueva ventana del explorador, vaya a la dirección URL que se muestra en **el nombre de host FTP** o **FTPS hostname** en el módulo de **Aplicación Web** para la aplicación web. 

6. Inicie sesión con las credenciales FTP que creó anteriormente (incluido el prefijo de nombre de aplicación web para el nombre de usuario).

    El explorador muestra la carpeta raíz de la aplicación web.

6. Abra la carpeta de *archivos de registro* .

    ![Abrir carpeta de archivos de registro](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-logfilesfolder.png)

7. Abra la carpeta que se denomina W3SVC además de un valor numérico.

    ![Abrir carpeta de W3SVC](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-w3svcfolder.png)

    La carpeta contiene archivos XML para los errores que se han registrado después de habilitar el seguimiento de solicitudes con error y un archivo XSL que puede usar un explorador para dar formato a XML.

    ![Carpeta de W3SVC](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-w3svcfoldercontents.png)

8. Haga clic en el archivo XML de la solicitud con errores que desea ver la información de seguimiento de.

    La siguiente ilustración muestra parte de la información de seguimiento para un error de ejemplo.

    ![Error de seguimiento de solicitud en el explorador](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-failedrequestinbrowser.png)


## <a name="nextsteps"></a>Pasos siguientes

Ha visto cómo Visual Studio facilita ver los registros creados por una aplicación web de Azure. Las siguientes secciones proporcionan vínculos a más recursos sobre temas relacionados:

* Solución de problemas de aplicación web de Azure
* Depuración en Visual Studio 
* Depuración en Azure remota
* Seguimiento de aplicaciones de ASP.NET
* Analizar registros del servidor web
* Error al analizar los registros de seguimiento de solicitud
* Servicios de nube de depuración

### <a name="azure-web-app-troubleshooting"></a>Solución de problemas de aplicación web de Azure

Para obtener más información sobre solución de problemas de aplicaciones web en la aplicación de servicio de Azure, consulte los siguientes recursos:

* [Cómo supervisar las aplicaciones web](/manage/services/web-sites/how-to-monitor-websites/)
* [Investigar pérdidas de memoria en las aplicaciones Web de Azure con Visual Studio de 2013](http://blogs.msdn.com/b/visualstudioalm/archive/2013/12/20/investigating-memory-leaks-in-azure-web-sites-with-visual-studio-2013.aspx). Entrada de blog de Microsoft ALM sobre las características de Visual Studio para analizar los problemas de memoria administrada.
* [Herramientas en línea de azure web aplicaciones que debe conocer](/blog/2014/03/28/windows-azure-websites-online-tools-you-should-know-about-2/). Entrada de blog de Amit Apple.

Para obtener ayuda con una pregunta de solución de problemas específica, iniciar un subproceso en uno de los foros de la siguientes:

* [Foro de la Azure en el sitio ASP.NET](http://forums.asp.net/1247.aspx/1?Azure+and+ASP+NET).
* [Foro de la Azure en MSDN](http://social.msdn.microsoft.com/Forums/windowsazure/).
* [StackOverflow.com](http://www.stackoverflow.com).

### <a name="debugging-in-visual-studio"></a>Depuración en Visual Studio 

Para obtener más información sobre cómo usar el modo de depuración en Visual Studio, vea el tema MSDN de [depuración en Visual Studio](http://msdn.microsoft.com/library/vstudio/sc65sadd.aspx) y [Sugerencias de depuración con Visual Studio 2010](http://weblogs.asp.net/scottgu/archive/2010/08/18/debugging-tips-with-visual-studio-2010.aspx).

### <a name="remote-debugging-in-azure"></a>Depuración en Azure remota

Para obtener más información acerca de depuración remota de aplicaciones web de Azure y WebJobs, consulte los siguientes recursos:

* [Introducción a Azure de aplicación de servicio Web Apps depuración remota](/blog/2014/05/06/introduction-to-remote-debugging-on-azure-web-sites/).
* [Introducción al elemento remoto depuración Azure aplicación servicio Web Apps 2 - dentro de depuración remota](/blog/2014/05/07/introduction-to-remote-debugging-azure-web-sites-part-2-inside-remote-debugging/)
* [Introducción a la depuración remota en parte 3: entorno de instancias múltiples y GIT de Azure aplicación servicio Web Apps](/blog/2014/05/08/introduction-to-remote-debugging-on-azure-web-sites-part-3-multi-instance-environment-and-git/)
* [WebJobs depuración (vídeo)](https://www.youtube.com/watch?v=ncQm9q5ZFZs&list=UU_SjTh-ZltPmTYzAybypB-g&index=1)

Si la aplicación web usa una back-end de Azure Web API o servicios móviles y necesita depurar, consulte [Depuración .NET back-end en Visual Studio](http://blogs.msdn.com/b/azuremobile/archive/2014/03/14/debugging-net-backend-in-visual-studio.aspx).

### <a name="tracing-in-aspnet-applications"></a>Seguimiento de aplicaciones de ASP.NET

No hay ningún introducciones actualizadas y completa en el seguimiento de ASP.NET disponibles en Internet. Lo mejor que puede hacer es Introducción a los materiales introductorios antiguos escritos para formularios Web porque MVC no existe aún y el suplemento con más nuevo blog publicaciones que se centren en problemas específicos. Algunos buenos lugares para empezar son los siguientes recursos:

* [Supervisión y telemetría (creación de aplicaciones de nube reales con Azure)](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/monitoring-and-telemetry).<br> Capítulos del libro electrónico con recomendaciones para realizar un seguimiento en aplicaciones de nube de Azure.
* [Seguimiento de ASP.NET](http://msdn.microsoft.com/library/ms972204.aspx)<br/>
  Antiguo pero aún un buen recurso para obtener una introducción básica en el asunto.
* [Escucha de seguimiento](http://msdn.microsoft.com/library/4y5y10s7.aspx)<br/>
  Información sobre los agentes de escucha, pero no se menciona el [WebPageTraceListener](http://msdn.microsoft.com/library/system.web.webpagetracelistener.aspx).
* [Tutorial: Integrar el seguimiento en ASP.NET con el seguimiento de System.Diagnostics](http://msdn.microsoft.com/library/b0ectfxd.aspx)<br/>
  Esto también es antiguo, pero incluye información adicional que no cubre el artículo Introducción.
* [Seguimiento en vistas Razor de ASP.NET MVC](http://blogs.msdn.com/b/webdev/archive/2013/07/16/tracing-in-asp-net-mvc-razor-views.aspx)<br/>
  Además de seguimiento en vistas Razor, la entrada también explica cómo crear un filtro de error para poder iniciar las excepciones no controladas en una aplicación MVC. Para obtener información sobre cómo iniciar todas las excepciones no controladas en una aplicación Web Forms, vea el ejemplo de Global.asax de [Ejemplo completo de los controladores de Error](http://msdn.microsoft.com/library/bb397417.aspx) en MSDN. En MVC o en formularios Web, si desea iniciar ciertas excepciones pero dejar que el marco de trabajo predeterminada tratamiento surtan efecto, puede detectar y reiniciar como en el ejemplo siguiente:

        try
        {
           // Your code that might cause an exception to be thrown.
        }
        catch (Exception ex)
        {
            Trace.TraceError("Exception: " + ex.ToString());
            throw;
        } 

* [Registro de la línea de comandos de Azure (más vistazo) de seguimiento de diagnósticos Streaming](http://www.hanselman.com/blog/StreamingDiagnosticsTraceLoggingFromTheAzureCommandLinePlusGlimpse.aspx)<br/>
  Cómo usar la línea de comandos para realizar este tutorial en qué muestra cómo hacer en Visual Studio. [Visión](http://www.hanselman.com/blog/IfYoureNotUsingGlimpseWithASPNETForDebuggingAndProfilingYoureMissingOut.aspx) es una herramienta de depuración de aplicaciones ASP.NET. 
* [Uso de aplicaciones Web de registro y diagnóstico - con David Ebbo](/documentation/videos/azure-web-site-logging-and-diagnostics/) y los [registros de transmisiones de aplicaciones Web - con David Ebbo](/documentation/videos/log-streaming-with-azure-web-sites/)<br>
  Vídeos por Scott Hanselman y David Ebbo.

Registro de errores, una alternativa a escribir su propio código de seguimiento es usar un marco de registro de código abierto como [ELMAH](http://nuget.org/packages/elmah/). Para obtener más información, vea [publicaciones en el blog de Scott Hanselman ELMAH](http://www.hanselman.com/blog/NuGetPackageOfTheWeek7ELMAHErrorLoggingModulesAndHandlersWithSQLServerCompact.aspx).

Además, tenga en cuenta que no debe usar ASP.NET o System.Diagnostics maqueta si desea obtener transmisión registros de Azure. El servicio de registro de transmisiones de Azure web app transmita cualquier archivo *.txt*, *.html*o *.log* que se encuentra en la carpeta de *archivos de registro* . Por lo tanto, puede crear su propio sistema de registro que escribe en el sistema de archivos de la aplicación web y el archivo se transmite y descargar automáticamente. Todo lo que tiene que hacer es escribir código de aplicación que crea archivos en la carpeta *d:\home\logfiles* . 

### <a name="analyzing-web-server-logs"></a>Analizar registros del servidor web

Para obtener más información acerca del análisis de registros del servidor web, consulte los siguientes recursos:

* [LogParser](http://www.microsoft.com/download/details.aspx?id=24659)<br/>
  Herramienta de visualización de datos en los registros de servidor web (archivos*. log* ).
* [Para solucionar problemas de rendimiento de IIS o errores de aplicación con LogParser](http://www.iis.net/learn/troubleshoot/performance-issues/troubleshooting-iis-performance-issues-or-application-errors-using-logparser)<br/>
  Introducción a la herramienta de analizador de registro que puede usar para analizar los registros del servidor web.
* [Entradas de blog por Robert McMurray acerca de cómo utilizar LogParser](http://blogs.msdn.com/b/robert_mcmurray/archive/tags/logparser/)<br/>
* [El código de estado HTTP en IIS 7.0, IIS 7.5 y IIS 8.0](http://support.microsoft.com/kb/943891)

### <a name="analyzing-failed-request-tracing-logs"></a>Error al analizar los registros de seguimiento de solicitud

El sitio Web de Microsoft TechNet incluye una sección [Usando solicitar seguimiento de error](http://www.iis.net/learn/troubleshoot/using-failed-request-tracing) que puede ser útil para comprender cómo usar estos registros. Sin embargo, esta documentación se centra principalmente en configuración del seguimiento de error en las peticiones de IIS, que no puede realizar en aplicaciones Web de Azure.

[GetStarted]: web-sites-dotnet-get-started.md
[GetStartedWJ]: websites-dotnet-webjobs-sdk.md
 
