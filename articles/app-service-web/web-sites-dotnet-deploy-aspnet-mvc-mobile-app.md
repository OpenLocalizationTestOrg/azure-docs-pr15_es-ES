<properties 
    pageTitle="Implementar una aplicación web móvil de ASP.NET MVC 5 en la aplicación de servicio de Azure" 
    description="Tutorial que le enseña cómo implementar una aplicación web en la aplicación de servicio de Azure con características de móviles en la aplicación web de ASP.NET MVC 5." 
    services="app-service" 
    documentationCenter=".net" 
    authors="cephalin" 
    manager="wpickett" 
    editor="jimbe"/>

<tags 
    ms.service="app-service" 
    ms.workload="na" 
    ms.tgt_pltfrm="na" 
    ms.devlang="dotnet" 
    ms.topic="article" 
    ms.date="01/12/2016" 
    ms.author="cephalin;riande"/>


# <a name="deploy-an-aspnet-mvc-5-mobile-web-app-in-azure-app-service"></a>Implementar una aplicación web móvil de ASP.NET MVC 5 en la aplicación de servicio de Azure

Este curso enseña los conceptos básicos de cómo crear una aplicación web de ASP.NET MVC 5 que sea descriptivo mobile e implementar al servicio de la aplicación de Azure. En este tutorial, necesita [Visual Studio Express 2013 para Web] [ Visual Studio Express 2013] o la edición professional de Visual Studio si ya tiene que. Puede usar [2015 de Visual Studio] , pero las capturas de pantalla será diferentes y debe usar las plantillas de 4.x ASP.NET.

[AZURE.INCLUDE [create-account-and-websites-note](../../includes/create-account-and-websites-note.md)]

## <a name="what-youll-build"></a>¿Qué va a crear

En este tutorial, deberá agregar características móviles a la aplicación simple de anuncio de conferencia que se proporciona en el [proyecto de inicio][StarterProject]. La siguiente captura de pantalla muestra las sesiones ASP.NET en la aplicación completada, tal como se muestra en el emulador explorador en herramientas de desarrollo de Internet Explorer 11 F12.

![][FixedSessionsByTag]

Puede usar las herramientas de desarrollo F12 de Internet Explorer 11 y la [herramienta de Fiddler] [ Fiddler] para ayudar a depurar la aplicación. 

## <a name="skills-youll-learn"></a>Habilidades que aprenderá

Esto es lo que aprenderá:

-   Cómo usar 2013 de Visual Studio para publicar la aplicación web directamente a una aplicación web de servicio de aplicaciones de Azure.
-   ¿Cómo las plantillas de ASP.NET MVC 5 usan el marco de trabajo de inicio de CSS para mejorar la visualización en dispositivos móviles
-   Cómo crear vistas de mobile específicos para exploradores móviles específicos, como el iPhone y Android
-   Cómo crear responde vistas (vistas que respondan a distintos navegadores entre dispositivos)

## <a name="set-up-the-development-environment"></a>Configurar el entorno de desarrollo

Configurar el entorno de desarrollo instalando el SDK de Azure para .NET 2.5.1 o posterior. 

1. Para instalar el SDK de Azure para .NET, haga clic en el siguiente vínculo. Si no tiene Visual Studio de 2013 ha instalado, se instalará por el vínculo. Este tutorial requiere Visual Studio de 2013. [Azure SDK para Visual Studio de 2013][AzureSDKVs2013]
1. En la ventana del instalador de plataforma Web, haga clic en **instalar** y continuar con la instalación.

También necesitará un emulador de explorador móvil. Funcionarán cualquiera de las siguientes acciones:

-   Explorador emulador en [Herramientas de desarrollo de Internet Explorer 11 F12] [ EmulatorIE11] (que se usa en todas las capturas de pantalla de explorador móvil). Tiene valores preestablecidos de cadena de agente de usuario para Windows Phone 8, Windows Phone 7 y iPad de Apple.
-   Explorador emulador en [Google Chrome DevTools][EmulatorChrome]. Contiene valores preestablecidos para numerosos dispositivos Android, así como Apple iPhone, iPad de Apple y Kindle Fire de Amazon. También emula eventos touch.
-   [Opera móvil emulador][EmulatorOpera]

Proyectos de Visual Studio con C\# código fuente están disponibles para este tema:

-   [Descarga de proyecto Starter][StarterProject]
-   [Completar la descarga de proyecto][CompletedProject]

##<a name="bkmk_DeployStarterProject"></a>Implementar el proyecto de inicio en una aplicación web de Azure

1.  Descargar la aplicación de conferencia listado [proyecto de inicio][StarterProject].

2.  A continuación, en el Explorador de Windows, haga clic en el archivo ZIP descargado y elija *Propiedades*.

3.  En el cuadro de diálogo **Propiedades** , elija el botón **desbloquear** . (Desbloquear impide una advertencia de seguridad que se produce cuando intenta usar un archivo *.zip* que haya descargado desde la web).

4.  Haga clic en el archivo ZIP y seleccione **Extraer todo** para descomprima el archivo. 

5.  En Visual Studio, abra el archivo *C#\Mvc5Mobile.sln* .

6.  En el Explorador de soluciones, haga clic en el proyecto y haga clic en **Publicar**.

    ![][DeployClickPublish]

7.  Publicar en Web, haga clic en **Servicio de aplicación de Microsoft Azure**.

    ![][DeployClickWebSites]

8.  Si ya no ha iniciado sesión en Azure, haga clic en **Agregar una cuenta**.

    ![][DeploySignIn]

9.  Siga las indicaciones para iniciar sesión en su cuenta de Azure.

11. El cuadro de diálogo de servicio de la aplicación debe mostrar ahora como sesión iniciada. Haga clic en **nuevo**.

    ![][DeployNewWebsite]  

12. En el campo **Nombre de la aplicación Web** , especifique un prefijo de nombre de aplicación única. El nombre de la aplicación web completo será * &lt;prefijo >*. azurewebsites.net. Además, seleccione o especifique un nuevo nombre de grupo de recursos en el **grupo de recursos**. A continuación, haga clic en **nuevo** para crear un nuevo plan de servicio de aplicación.

    ![][DeploySiteSettings]

13. Configurar el nuevo plan de servicio de aplicaciones y haga clic en **Aceptar**. 

    ![](./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/deploy-to-azure-website-7a.png)

13. En el cuadro de diálogo Crear servicio de aplicación, haga clic en **crear**.

    ![](./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/deploy-to-azure-website-7b.png) 

13. Después de la Azure recursos se crean, publicación Web se rellenará el cuadro de diálogo con la configuración para la nueva aplicación. Haga clic en **Publicar**.

    ![][DeployPublishSite]

    Una vez que Visual Studio termine de publicar el proyecto de inicio a la aplicación web de Azure, se abre el Explorador de escritorio para mostrar la aplicación web de live.

14. Iniciar el emulador de explorador móvil, copie la dirección URL para la aplicación de conferencia (*<prefix>*. azurewebsites.net) en el emulador y, a continuación, haga clic en el botón de la parte superior derecha y seleccione **Examinar por etiqueta**. Si está utilizando Internet Explorer 11 como el explorador predeterminado, solo debe escribir `F12`, a continuación, `Ctrl+8`y, a continuación, cambie el perfil de explorador para **Windows Phone**. La imagen siguiente muestra la vista de *AllTags* en modo vertical (desde elegir **Examinar por etiqueta**).

    ![][AllTags]

>[AZURE.TIP] Aunque puede depurar su aplicación MVC 5 de Visual Studio, puede publicar la aplicación web en Azure para comprobar la aplicación web en vivo directamente desde el explorador móvil o un emulador de explorador.

La visualización es muy legible en un dispositivo móvil. También puede ver algunos de los efectos visuales aplicados por el marco de trabajo de inicio CSS.
Haga clic en el vínculo **ASP.NET** .

![][SessionsByTagASP.NET]

La vista de la etiqueta ASP.NET es zoom para ajustarse a la pantalla, que realiza una secuencia de inicio de automáticamente. Sin embargo, puede mejorar esta vista para que se ajuste mejor al explorador móvil. Por ejemplo, la columna de **fecha** es difícil de leer. Más adelante en el tutorial va a cambiar la vista de *AllTags* para que sea compatible con mobile.

##<a name="bkmk_bootstrap"></a>Marco de trabajo de inicio de CSS

Nuevo en el 5 de MVC plantilla está compatibilidad integrada de inicio. Ya ha visto cómo inmediatamente mejora las diferentes vistas de la aplicación. Por ejemplo, la barra de navegación en la parte superior es contraíble automáticamente cuando el ancho del explorador es más pequeño. En el Explorador de escritorio, pruebe a cambiar el tamaño de la ventana del explorador y vea cómo la barra de navegación cambia su apariencia. Este es el diseño web responde integrado en Inicio.

Para ver qué aspecto que tendrá la aplicación Web sin inicio, abra *aplicación\_iniciar\\BundleConfig.cs* y comentario las líneas que contienen *bootstrap.js* y *bootstrap.css*. El código siguiente muestra los dos últimos Estados de la `RegisterBundles` método después del cambio:

     bundles.Add(new ScriptBundle("~/bundles/bootstrap").Include(
              //"~/Scripts/bootstrap.js",
              "~/Scripts/respond.js"));

    bundles.Add(new StyleBundle("~/Content/css").Include(
              //"~/Content/bootstrap.css",
              "~/Content/site.css"));

Presione `Ctrl+F5` para ejecutar la aplicación.

Observe que la barra de navegación contraíble ahora es solo una lista sin ordenar ordinaria. Haga clic en **Examinar por etiqueta** de nuevo y luego haga clic en **ASP.NET**.
En la vista móvil emulador, puede ver ahora que ya no está instalado de zoom en la pantalla y, a continuación, debe desplazarse lateralmente para ver el lado derecho de la tabla.

![][SessionsByTagASP.NETNoBootstrap]

Deshacer los cambios y actualice el explorador móvil para comprobar que se ha restaurado la visualización móvil fácil de usar.

Inicio no es específico a ASP.NET MVC 5 y puede aprovechar las ventajas de estas características en cualquier aplicación web. Pero ahora está integrada en la plantilla de proyecto de ASP.NET MVC 5, para que la aplicación Web de MVC 5 puede aprovechar las ventajas de inicio de forma predeterminada.

Para obtener más información acerca de inicio, vaya a la [secuencia de inicio] [ BootstrapSite] sitio.

En la siguiente sección, verá cómo proporcionar vistas específicas del explorador móvil.

##<a name="bkmk_overrideviews"></a>Reemplazar las vistas, diseños y parcial

Puede reemplazar cualquier vista (incluidos diseños y vistas parciales) para exploradores de dispositivos móviles en general, para un explorador móvil individual o para cualquier explorador específico. Para proporcionar una vista específica de móvil, puede copiar un archivo de vista y agregar *. Mobile* al nombre del archivo. Por ejemplo, para crear una vista móvil de *índice* , puede copiar *vistas\\inicio\\Index.cshtml* a *vistas\\inicio\\Index.Mobile.cshtml*.

En esta sección, creará un archivo de diseño móvil específicas.

Para empezar, copie *vistas\\Shared\\\_Layout.cshtml* a *vistas\\Shared\\\_Layout.Mobile.cshtml*. Abrir * \_Layout.Mobile.cshtml* y cambiar el título de la **Aplicación de MVC5** a **MVC5 aplicación (móvil)**.

En cada `Html.ActionLink` de llamadas para la barra de navegación, quitar "Examinar por" en cada vínculo *ActionLink*. El código siguiente muestra la completada `<ul class="nav navbar-nav">` etiqueta del archivo de diseño móvil.

    <ul class="nav navbar-nav">
        <li>@Html.ActionLink("Home", "Index", "Home")</li>
        <li>@Html.ActionLink("Date", "AllDates", "Home")</li>
        <li>@Html.ActionLink("Speaker", "AllSpeakers", "Home")</li>
        <li>@Html.ActionLink("Tag", "AllTags", "Home")</li>
    </ul>

Copiar la *vistas\\inicio\\AllTags.cshtml* del archivo a *vistas\\inicio\\AllTags.Mobile.cshtml*. Abra el archivo nuevo y cambie la `<h2>` elemento de "Etiquetas" a "etiquetas (M)":

    <h2>Tags (M)</h2>

Vaya a la página de etiquetas con un explorador de escritorio y uso de explorador móvil. El emulador de explorador móvil muestra los dos cambios realizados (el título de * \_Layout.Mobile.cshtml* y el título de *AllTags.Mobile.cshtml*).

![][AllTagsMobile_LayoutMobile]

Por el contrario, no ha cambiado la presentación del escritorio (con títulos de * \_Layout.cshtml* y *AllTags.cshtml*).

![][AllTagsMobile_LayoutMobileDesktop]

##<a name="bkmk_browserviews"></a>Crear vistas específicas del explorador

Además de las vistas específicas de escritorio y móvil, puede crear vistas para un explorador individual. Por ejemplo, puede crear vistas que están específicamente para iPhone o en el explorador Android. En esta sección, creará un diseño para el Explorador de iPhone y una versión de iPhone de la vista de *AllTags* .

Abra el archivo *Global.asax* y agregue el código siguiente a la parte inferior de la `Application_Start` método.

    DisplayModeProvider.Instance.Modes.Insert(0, new DefaultDisplayMode("iPhone")
    {
        ContextCondition = (context => context.GetOverriddenUserAgent().IndexOf
            ("iPhone", StringComparison.OrdinalIgnoreCase) >= 0)
    });

Este código define un nuevo modo de presentación denominado "iPhone" que se asociarán con cada solicitud entrante. Si la solicitud entrante coincide con la condición que se haya definido (es decir, si el agente de usuario contiene la cadena "iPhone"), ASP.NET MVC buscará vistas cuyo nombre contenga el sufijo "iPhone".

>[AZURE.NOTE] Al agregar modos de pantalla específicos del explorador móvil, como iPhone y Android, asegúrese de establecer el primer argumento `0` (insertar en la parte superior de la lista) para asegurarse de que el modo específico del explorador tiene prioridad sobre la plantilla mobile (*. Mobile.cshtml). Si la plantilla mobile está en la parte superior de la lista en su lugar, se seleccionará sobre el modo de pantalla previsto (el primer wins coincidir y la plantilla móvil coincide con todos los exploradores de dispositivos móviles). 

En el código, haga clic con el botón `DefaultDisplayMode`, elija **resolver**y, a continuación, elija `using System.Web.WebPages;`. Esto agrega una referencia a la `System.Web.WebPages` espacio de nombres, que es donde el `DisplayModeProvider` y `DefaultDisplayMode` tipos definidos.

![][ResolveDefaultDisplayMode]

Como alternativa, puede agregar simplemente manualmente la siguiente línea a la `using` sección del archivo.

    using System.Web.WebPages;

Guardar los cambios. Copiar la *vistas\\Shared\\\_Layout.Mobile.cshtml* del archivo a *vistas\\Shared\\\_Layout.iPhone.cshtml*. Abra el archivo nuevo y, a continuación, cambiar el título de `MVC5 Application (Mobile)` a `MVC5 Application (iPhone)`.

Copiar la *vistas\\inicio\\AllTags.Mobile.cshtml* del archivo a *vistas\\inicio\\AllTags.iPhone.cshtml*. En el nuevo archivo, cambie la `<h2>` elemento de "etiquetas (M)" a "Etiquetas (iPhone)".

Ejecute la aplicación. Ejecutar un emulador de explorador móvil, asegúrese de que su agente de usuario se establece en "iPhone" y vaya a la vista de *AllTags* . Si está utilizando el emulador en herramientas de desarrollo F12 de Internet Explorer 11, configure emulación a la siguiente:

-   Perfil de explorador = **Windows Phone**
-   Cadena de agente de usuario = **personalizada**
-   Cadena personalizada = **Apple-iPhone5C1/1001.525**

La siguiente captura de pantalla muestra la vista de *AllTags* representada en el emulador en herramientas de desarrollo F12 de Internet Explorer 11 con la cadena de agente de usuario personalizada (es decir, una cadena de agente de usuario de iPhone 5 C).

![][AllTagsIPhone_LayoutIPhone]

En el explorador móvil, seleccione el vínculo de **los altavoces** . Porque no hay una vista móvil (*AllSpeakers.Mobile.cshtml*), la vista de los altavoces predeterminada (*AllSpeakers.cshtml*) se representa con la vista de diseño móvil (*\_Layout.Mobile.cshtml*). Como se muestra a continuación, el título de la **Aplicación MVC5 (móvil)** se define en * \_Layout.Mobile.cshtml*.

![][AllSpeakers_LayoutMobile]

Puede deshabilitar globalmente una vista de (no móvil) predeterminada de la representación dentro de un diseño móvil estableciendo `RequireConsistentDisplayMode` a `true` en la *vistas\\\_ViewStart.cshtml* archivo similar a esta:

    @{
        Layout = "~/Views/Shared/_Layout.cshtml";
        DisplayModeProvider.Instance.RequireConsistentDisplayMode = true;
    }

Cuando `RequireConsistentDisplayMode` se establece en `true`, el diseño móvil (*\_Layout.Mobile.cshtml*) se usa solamente para vistas móviles (es decir, cuando el archivo de vista tiene el formato * **ViewName**. Mobile.cshtml*). Es posible que desee establecer `RequireConsistentDisplayMode` a `true` si su diseño móvil no funciona bien con las vistas no móvil. Captura de pantalla siguiente muestra cómo el que representa la página de *los altavoces* cuando `RequireConsistentDisplayMode` se establece en `true` (sin la cadena "(móvil)" en la barra de navegación en la parte superior).

![][AllSpeakers_LayoutMobileOverridden]

Puede deshabilitar el modo de presentación coherente en una vista específica estableciendo `RequireConsistentDisplayMode` a `false` en el archivo de vista. El siguiente marcado en la *vistas\\inicio\\AllSpeakers.cshtml* conjuntos de archivos `RequireConsistentDisplayMode` a `false`:

    @model IEnumerable<string>

    @{
        ViewBag.Title = "All speakers";
        DisplayModeProvider.Instance.RequireConsistentDisplayMode = false;
    }

En esta sección se ha visto cómo crear vistas y diseños móviles y cómo crear diseños y vistas para dispositivos específicos, como el iPhone.
Sin embargo, la principal ventaja de framework inicio CSS es el diseño de capacidad de respuesta, lo que significa que se puede aplicar una única hoja de estilos en el escritorio, teléfono y tableta exploradores para crear un aspecto coherente. En la siguiente sección, verá cómo aprovechar inicio para crear vistas fáciles de móvil.

##<a name="bkmk_Improvespeakerslist"></a>Mejorar la lista de los altavoces

Como ha visto, la vista de *los altavoces* es legible, pero los vínculos son pequeños y son difíciles de pulsar en un dispositivo móvil. En esta sección, realizará la vista *AllSpeakers* fáciles de móvil, que muestra los vínculos de gran volumen y de fácil punteo y contiene un cuadro de búsqueda para encontrar rápidamente los altavoces.

Puede usar el estilo de [grupo de la lista vinculada][] de inicio para mejorar la vista de *los altavoces* . En *vistas\\inicio\\AllSpeakers.cshtml*, reemplace el contenido del archivo Razor con el código siguiente.

     @model IEnumerable<string>

    @{
        ViewBag.Title = "All Speakers";
    }

    <h2>Speakers</h2>

    <div class="list-group">
        @foreach (var speaker in Model)
        {
            @Html.ActionLink(speaker, "SessionsBySpeaker", new { speaker }, new { @class = "list-group-item" })
        }
    </div>

La `class="list-group"` el atributo en el `<div>` etiqueta aplica el estilo de la lista de inicio y la `class="input-group-item"` atributo aplica el estilo del elemento de lista inicio para cada vínculo.

Actualice el explorador móvil. La vista actualizada es similar a esta:

![][AllSpeakersFixed]

El estilo de [grupo de la lista vinculada][] de inicio hace todo el cuadro para cada vínculo interactivo, que es una experiencia de usuario mucho mejor. Cambiar a la vista de escritorio y observe la apariencia y funcionamiento coherentes.

![][AllSpeakersFixedDesktop]

Aunque la vista de explorador móvil ha mejorado, es difícil navegar por la lista de los altavoces. Inicio no proporciona una búsqueda filtro funcionalidad-de-predefinidas, pero puede agregar con algunas líneas de código. En primer lugar se agregue un cuadro de búsqueda en la vista y luego conectar con el código de JavaScript para la función de filtro. En *vistas\\inicio\\AllSpeakers.cshtml*, agregar un \<formulario\> etiqueta justo después del \<h2\> etiqueta, como se muestra a continuación:

    @model IEnumerable<string>

    @{
        ViewBag.Title = "All Speakers";
    }

    <h2>Speakers</h2>

    <form class="input-group">
        <span class="input-group-addon"><span class="glyphicon glyphicon-search"></span></span>
        <input type="text" class="form-control" placeholder="Search speaker">
    </form>
    <br />
    <div class="list-group">
        @foreach (var speaker in Model)
        {
            @Html.ActionLink(speaker, 
                             "SessionsBySpeaker", 
                             new { speaker }, 
                             new { @class = "list-group-item" })
        }
    </div>

Observe que la `<form>` y `<input>` etiquetas ambos tienen los estilos inicio aplicados a ellas. La `<span>` elemento agrega un inicio [glyphicon][] al cuadro de búsqueda.

En la carpeta *Scripts* , agregue un archivo de JavaScript denominado *filter.js*. Abra el archivo y pegue el código siguiente:

    $(function () {

        // reset the search form when the page loads
        $("form").each(function () {
            this.reset();
        });

        // wire up the events to the <input> element for search/filter
        $("input").bind("keyup change", function () {
            var searchtxt = this.value.toLowerCase();
            var items = $(".list-group-item");

            // show all speakers that begin with the typed text and hide others
            for (var i = 0; i < items.length; i++) {
                var val = items[i].text.toLowerCase();
                val = val.substring(0, searchtxt.length);
                if (val == searchtxt) {
                    $(items[i]).show();
                }
                else {
                    $(items[i]).hide();
                }
            }
        });
    });

También debe incluir filter.js en los paquetes registrados. Abrir *aplicación\_iniciar\\BundleConfig.cs* y cambiar los paquetes de la primera. Cambiar la primera `bundles.Add` instrucción (para el paquete **jquery** ) para incluir *secuencias de comandos\\filter.js*, como sigue:

     bundles.Add(new ScriptBundle("~/bundles/jquery").Include(
                "~/Scripts/jquery-{version}.js",
                "~/Scripts/filter.js"));

El paquete de **jquery** ya está representado por el valor predeterminado * \_diseño* vista. Más adelante, puede utilizar el mismo código de JavaScript para aplicar la funcionalidad de filtro a otras vistas de lista.

Actualice el explorador móvil y vaya a la vista de *AllSpeakers* . En el cuadro Buscar, escriba "sc". Ahora se debe filtrar la lista de los altavoces según la cadena de búsqueda.

![][AllSpeakersFixedSearchBySC]

##<a name="bkmk_improvetags"></a>Mejorar la lista de etiquetas

Al igual que la vista de *los altavoces* , la vista de *etiquetas* es legible, pero los vínculos son pequeño y difícil de pulsar en un dispositivo móvil. Corregir la vista de *etiquetas de* la misma manera que corregir la vista de *los altavoces* , si utiliza los cambios de código descritos anteriormente, pero con la siguiente `Html.ActionLink` sintaxis del método en *vistas\\inicio\\AllTags.cshtml*:

    @Html.ActionLink(tag, 
                     "SessionsByTag", 
                     new { tag }, 
                     new { @class = "list-group-item" })

El Explorador de escritorio actualizado tiene el siguiente aspecto:

![][AllTagsFixedDesktop]

Y el explorador móvil actualizado tiene el siguiente aspecto: 

![][AllTagsFixed]

>[AZURE.NOTE] Si observa que el formato de lista original sigue estando ahí en el explorador móvil y pregunta qué ha ocurrido con el estilo de arranque bueno, esto es muestra la acción anterior para crear vistas específicas móviles. Sin embargo, ahora que está utilizando el marco de trabajo de CSS de inicio para crear un diseño web responde, vaya cabeza y quite estas vistas móviles específicos y las vistas diseño móvil específicas. Una vez hecho, el explorador móvil actualizado mostrará el estilo de inicio.

##<a name="bkmk_improvedates"></a>Mejorar la lista de fechas

Puede mejorar la vista de *fechas* como mejorado las vistas de *etiquetas* y *altavoces* si utiliza los cambios de código descritos anteriormente, pero con la siguiente `Html.ActionLink` sintaxis del método en *vistas\\inicio\\AllDates.cshtml*:

    @Html.ActionLink(date.ToString("ddd, MMM dd, h:mm tt"), 
                     "SessionsByDate", 
                     new { date }, 
                     new { @class = "list-group-item" })

Obtendrá una vista de explorador móvil actualizados similar a esta:

![][AllDatesFixed]

Puede mejorar aún más la vista de *fechas* mediante la organización de los valores de fecha y hora por fecha. Esto puede hacerse con el estilo de arranque [paneles][] . Reemplazar el contenido de la *vistas\\inicio\\AllDates.cshtml* archivo con el siguiente código:

    @model IEnumerable<DateTime>

    @{
        ViewBag.Title = "All Dates";
    }

    <h2>Dates</h2>

    @foreach (var dategroup in Model.GroupBy(x=>x.Date))
    {
        <div class="panel panel-primary">
            <div class="panel-heading">
                @dategroup.Key.ToString("ddd, MMM dd")
            </div>
            <div class="panel-body list-group">
                @foreach (var date in dategroup)
                {
                    @Html.ActionLink(date.ToString("h:mm tt"), 
                                     "SessionsByDate", 
                                     new { date }, 
                                     new { @class = "list-group-item" })
                }
            </div>
        </div>
    }

Este código crea un independiente `<div class="panel panel-primary">` etiqueta para cada fecha distinta en la lista y se utiliza el [grupo de la lista vinculada][] para los vínculos correspondientes como antes. Aquí es el explorador móvil como cuando se ejecuta este código:

![][AllDatesFixed2]

Cambiar al explorador de escritorio. Nuevamente, observe la apariencia uniforme.

![][AllDatesFixed2Desktop]

##<a name="bkmk_improvesessionstable"></a>Mejorar la vista SessionsTable

En esta sección, deberá crear la vista de *SessionsTable* más fáciles de móvil. Este cambio es más amplio los cambios anteriores.

En el explorador móvil, puntee en el botón de **etiqueta** y luego escriba `asp` en el cuadro de búsqueda.

![][AllTagsFixedSearchByASP]

Puntee en el vínculo **ASP.NET** .

![][SessionsTableTagASP.NET]

Como puede ver, la visualización tiene el formato como una tabla, que actualmente está diseñada para visualizarse en el Explorador de escritorio. Sin embargo, es un poco más difícil de leer en un explorador móvil. Para corregir este error, abra *vistas\\inicio\\SessionsTable.cshtml* y, a continuación, reemplace el contenido del archivo con el siguiente código:

    @model IEnumerable<Mvc5Mobile.Models.Session>

    <h2>@ViewBag.Title</h2>

    <div class="container">
        <div class="row">
            @foreach (var session in Model)
            {
                <div class="col-md-4">
                    <div class="list-group">
                        @Html.ActionLink(session.Title, 
                                         "SessionByCode", 
                                         new { session.Code }, 
                                         new { @class="list-group-item active" })
                        <div class="list-group-item">
                            <div class="list-group-item-text">
                                @Html.Partial("_SpeakersLinks", session)
                            </div>
                            <div class="list-group-item-info">
                                @session.DateText
                            </div>
                            <div class="list-group-item-info small hidden-xs">
                                @Html.Partial("_TagsLinks", session)
                            </div>
                        </div>
                    </div>
                </div>
            }
        </div>
    </div>

El código hace 3 cosas:

-   utiliza el [grupo personalizado lista vinculada][] de inicio para dar formato a la información de sesión verticalmente, para que toda esta información es legible en un explorador móvil (mediante clases como lista grupo elemento de texto)
-   aplica el [sistema de cuadrícula][] en el diseño, para que los elementos de la sesión de flujo horizontalmente en el Explorador de escritorio y verticalmente en el explorador móvil (con la clase col-md-4)
-   usa las [utilidades responde][] para ocultar las etiquetas de sesión cuando se ve en el explorador móvil (con la clase x ocultos)

También puede pulsar en un vínculo de título para ir a la sesión respectiva. La imagen siguiente refleja los cambios de código.

![][FixedSessionsByTag]

El sistema de inicio de cuadrícula que aplica automáticamente Organiza las sesiones verticalmente en el explorador móvil. Observe también que no se muestran las etiquetas. Cambiar al explorador de escritorio.

![][SessionsTableFixedTagASP.NETDesktop]

En el Explorador de escritorio, observe que ahora se muestran las etiquetas. Además, puede ver que el sistema de cuadrícula inicio aplicó organiza los elementos de la sesión de dos columnas. Si amplía el explorador, verá que la disposición de los cambios en tres columnas.

##<a name="bkmk_improvesessionbycode"></a>Mejorar la vista SessionByCode

Por último, para corregir la vista *SessionByCode* para que sea compatible con mobile.

En el explorador móvil, puntee en el botón de **etiqueta** y luego escriba `asp` en el cuadro de búsqueda.

![][AllTagsFixedSearchByASP]

Puntee en el vínculo **ASP.NET** . Se muestran las sesiones de la etiqueta ASP.NET.

![][FixedSessionsByTag]

Elija el vínculo **crear una aplicación de página única con ASP.NET y AngularJS** .

![][SessionByCode3-644]

La vista de escritorio predeterminada está bien, pero puede mejorar el aspecto fácilmente con algunos componentes de la interfaz gráfica de usuario de inicio.

Abrir *vistas\\inicio\\SessionByCode.cshtml* y reemplace el contenido con el siguiente formato:

    @model Mvc5Mobile.Models.Session

    @{
        ViewBag.Title = "Session details";
    }
    <h3>@Model.Title (@Model.Code)</h3>
    <p>
        <strong>@Model.DateText</strong> in <strong>@Model.Room</strong>
    </p>

    <div class="panel panel-primary">
        <div class="panel-heading">
            Speakers
        </div>
        @foreach (var speaker in Model.Speakers)
        {
            @Html.ActionLink(speaker, 
                             "SessionsBySpeaker", 
                             new { speaker }, 
                             new { @class="panel-body" })
        }
    </div>

    <p>@Model.Abstract</p>

    <div class="panel panel-primary">
        <div class="panel-heading">
            Tags
        </div>
        @foreach (var tag in Model.Tags)
        {
            @Html.ActionLink(tag, 
                             "SessionsByTag", 
                             new { tag }, 
                             new { @class = "panel-body" })
        }
    </div>

El nuevo marcado usa inicio paneles estilos para mejorar la vista móvil. 

Actualice el explorador móvil. La siguiente imagen refleja los cambios de código que acaba de crear:

![][SessionByCodeFixed3-644]

## <a name="wrap-up-and-review"></a>Conclusión y revisar

Este tutorial ha mostrado cómo usar ASP.NET MVC 5 para desarrollar aplicaciones Web de mobile descriptivo. Se incluyen:

-   Implementar una aplicación de ASP.NET MVC 5 en una aplicación de servicio de aplicación web
-   Usar inicio para crear el diseño web responde en su aplicación MVC 5
-   Reemplazar diseño, vistas y vistas parciales, globalmente y para obtener una vista individual
-   Diseño de control y parcial invalidan la aplicación mediante el `RequireConsistentDisplayMode` (propiedad)
-   Crear vistas para exploradores específicos, como el Explorador de iPhone
-   Aplicar un estilo de inicio en código Razor

## <a name="see-also"></a>Vea también

-   [9 principios básicos de diseño web responde](http://blog.froont.com/9-basic-principles-of-responsive-web-design/)
-   [Inicio][BootstrapSite]
-   [Blog oficial de inicio][]
-   [Tutorial de inicio de Twitter de Tutorial República][]
-   [La animación del inicio][]
-   [Prácticas recomendadas de W3C recomendación Mobile Web Application][]
-   [W3C Candidate recomendación para las consultas de medios][]

## <a name="whats-changed"></a>¿Qué ha cambiado
* Encontrará una guía para el cambio de sitios Web a la aplicación de servicio: [servicio de aplicación de Azure y su influencia sobre los servicios de Azure existentes](http://go.microsoft.com/fwlink/?LinkId=529714)

<!-- Internal Links -->
[Deploy the starter project to an Azure web app]: #bkmk_DeployStarterProject
[Bootstrap CSS Framework]: #bkmk_bootstrap
[Override the Views, Layouts, and Partial Views]: #bkmk_overrideviews
[Create Browser-Specific Views]:#bkmk_browserviews
[Improve the Speakers List]: #bkmk_Improvespeakerslist
[Improve the Tags List]: #bkmk_improvetags
[Improve the Dates List]: #bkmk_improvedates
[Improve the SessionsTable View]: #bkmk_improvesessionstable
[Improve the SessionByCode View]: #bkmk_improvesessionbycode

<!-- External Links -->
[Visual Studio Express 2013]: http://www.visualstudio.com/downloads/download-visual-studio-vs#d-express-web
[Visual Studio de 2015]: https://www.visualstudio.com/downloads/download-visual-studio-vs
[AzureSDKVs2013]: http://go.microsoft.com/fwlink/p/?linkid=323510&clcid=0x409
[Fiddler]: http://www.fiddler2.com/fiddler2/
[EmulatorIE11]: http://msdn.microsoft.com/library/ie/dn255001.aspx
[EmulatorChrome]: https://developers.google.com/chrome-developer-tools/docs/mobile-emulation
[EmulatorOpera]: http://www.opera.com/developer/tools/mobile/
[StarterProject]: http://go.microsoft.com/fwlink/?LinkID=398780&clcid=0x409
[CompletedProject]: http://go.microsoft.com/fwlink/?LinkID=398781&clcid=0x409
[BootstrapSite]: http://getbootstrap.com/
[WebPIAzureSdk23NetVS13]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/WebPIAzureSdk23NetVS13.png
[grupo de la lista vinculada]: http://getbootstrap.com/components/#list-group-linked
[glyphicon]: http://getbootstrap.com/components/#glyphicons
[paneles]: http://getbootstrap.com/components/#panels
[grupo de la lista vinculada personalizada]: http://getbootstrap.com/components/#list-group-custom-content
[sistema de cuadrícula]: http://getbootstrap.com/css/#grid
[Utilidades de capacidad de respuesta]: http://getbootstrap.com/css/#responsive-utilities
[Blog oficial de inicio]: http://blog.getbootstrap.com/
[Tutorial de inicio de Twitter de Tutorial República]: http://www.tutorialrepublic.com/twitter-bootstrap-tutorial/
[La animación del inicio]: http://www.bootply.com/
[Prácticas recomendadas de W3C recomendación Mobile Web Application]: http://www.w3.org/TR/mwabp/
[W3C Candidate recomendación para las consultas de medios]: http://www.w3.org/TR/css3-mediaqueries/

<!-- Images -->
[DeployClickPublish]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/deploy-to-azure-website-1.png
[DeployClickWebSites]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/deploy-to-azure-website-2.png
[DeploySignIn]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/deploy-to-azure-website-3.png
[DeployUsername]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/deploy-to-azure-website-4.png
[DeployPassword]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/deploy-to-azure-website-5.png
[DeployNewWebsite]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/deploy-to-azure-website-6.png
[DeploySiteSettings]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/deploy-to-azure-website-7.png
[DeployPublishSite]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/deploy-to-azure-website-8.png
[MobileHomePage]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/mobile-home-page.png
[FixedSessionsByTag]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/SessionsByTag-ASP.NET-Fixed.png
[AllTags]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/AllTags.png
[SessionsByTagASP.NET]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/SessionsByTag-ASP.NET.png
[SessionsByTagASP.NETNoBootstrap]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/SessionsByTag-ASP.NET-NoBootstrap.png
[AllTagsMobile_LayoutMobile]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/AllTagsMobile-_LayoutMobile.png
[AllTagsMobile_LayoutMobileDesktop]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/AllTagsMobile-_LayoutMobile-Desktop.png
[ResolveDefaultDisplayMode]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/Resolve-DefaultDisplayMode.png
[AllTagsIPhone_LayoutIPhone]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/AllTagsIPhone-_LayoutIPhone.png
[AllSpeakers_LayoutMobile]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/AllSpeakers-_LayoutMobile.png
[AllSpeakers_LayoutMobileOverridden]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/AllSpeakers-_LayoutMobile-Overridden.png
[AllSpeakersFixed]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/AllSpeakers-Fixed.png
[AllSpeakersFixedDesktop]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/AllSpeakers-Fixed-Desktop.png
[AllSpeakersFixedSearchBySC]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/AllSpeakers-Fixed-SearchBySC.png
[AllTagsFixedDesktop]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/AllTags-Fixed-Desktop.png 
[AllTagsFixed]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/AllTags-Fixed.png
[AllDatesFixed]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/AllDates-Fixed.png
[AllDatesFixed2]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/AllDates-Fixed2.png
[AllDatesFixed2Desktop]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/AllDates-Fixed2-Desktop.png
[AllTagsFixedSearchByASP]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/AllTags-Fixed-SearchByASP.png
[SessionsTableTagASP.NET]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/SessionsTable-Tag-ASP.NET.png
[SessionsTableFixedTagASP.NETDesktop]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/SessionsTable-Fixed-Tag-ASP.NET-Desktop.png
[SessionByCode3-644]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/SessionByCode-3-644.png
[SessionByCodeFixed3-644]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/SessionByCode-Fixed-3-644.png
 
