<properties
    pageTitle="Integrar un servicio de nube con Azure CDN | Microsoft Azure"
    description="Un tutorial que le enseña cómo implementar un servicio de nube que sirve el contenido de un extremo de Azure CDN integrado"
    services="cdn, cloud-services"
    documentationCenter=".net"
    authors="camsoper"
    manager="erikre"
    editor="tysonn"/>

<tags
    ms.service="cdn"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="07/28/2016"
    ms.author="casoper"/>


# <a name="intro"></a>Integrar un servicio de nube con CDN de Azure

Puede integrar un servicio de nube con Azure CDN, servir cualquier contenido de la ubicación del servicio de nube. Este enfoque proporciona las siguientes ventajas:

- Implementar fácilmente y actualizar imágenes, scripts y hojas de estilos en directorios de proyecto de su servicio de nube
- Actualizar fácilmente los paquetes de NuGet en el servicio de nube, como jQuery o versiones de inicio
- Administrar la aplicación Web y su todo contenido servida CDN desde la misma interfaz Visual Studio
- Flujo de trabajo de implementación unificado para la aplicación Web y su contenido servida CDN
- Intégrelo agrupación de ASP.NET y reducción CDN de Azure

## <a name="what-you-will-learn"></a>Lo que aprenderá ##

En este tutorial, aprenderá cómo:

-   [Integrar un extremo de Azure CDN con el servicio de nube y servir contenido estático en las páginas Web de CDN de Azure](#deploy)
-   [Establecer la configuración de caché de contenido estático en el servicio de nube](#caching)
-   [Servir contenido desde acciones de controlador a través de CDN de Azure](#controller)
-   [Serve incluye y reducir el contenido a través de Azure CDN conservando el script de depuración experiencia en Visual Studio](#bundling)
-   [Configurar reserva sus scripts y CSS cuando está sin conexión la CDN de Azure](#fallback)

## <a name="what-you-will-build"></a>¿Qué va a crear ##

Implementar una función de Web de servicio de nube con la plantilla de ASP.NET MVC predeterminada, agregará código para servir contenido desde un CDN de Azure integradas, como una imagen, los resultados de acción de controlador y los archivos CSS y JavaScript predeterminada y también escribir código para configurar el mecanismo de reserva para paquetes servida en caso de que la CDN está desconectada.

## <a name="what-you-will-need"></a>Lo que necesita ##

Este tutorial tiene los siguientes requisitos previos:

-   Una [cuenta de Microsoft Azure](/account/) de activos
-   2015 de Visual Studio con [Azure SDK](http://go.microsoft.com/fwlink/?linkid=518003&clcid=0x409)

> [AZURE.NOTE] Necesita una cuenta de Azure para completar este tutorial:
> + Puede [Abrir una cuenta de Azure gratis](/pricing/free-trial/) : obtenga créditos puede usar para probar los servicios de Azure pagados e incluso después de que se utilizan hasta puede mantener la cuenta y usar libre Azure servicios, como sitios Web.
> + Puede [activar las ventajas de suscriptor MSDN](/pricing/member-offers/msdn-benefits-details/) : MSDN su suscripción le proporciona créditos cada mes que puede usar para los servicios de Azure pagados.

<a name="deploy"></a>
## <a name="deploy-a-cloud-service"></a>Implementar un servicio de nube ##

En esta sección, se implementar la plantilla de aplicación de ASP.NET MVC en Visual Studio de 2015 predeterminada para una función de Web del servicio de nube y, a continuación, integrarlo con un nuevo extremo CDN. Siga las instrucciones siguientes:

1. En Visual Studio de 2015, crear un nuevo servicio de nube de Azure en la barra de menús, vaya a **archivo > Nuevo > proyecto > nube > servicio de nube de Azure**. Asigne un nombre y haga clic en **Aceptar**.

    ![](media/cdn-cloud-service-with-cdn/cdn-cs-1-new-project.PNG)

2. Seleccione el **Rol Web de ASP.NET** y haga clic en el **>** botón. Haga clic en Aceptar.

    ![](media/cdn-cloud-service-with-cdn/cdn-cs-2-select-role.PNG)

3. Seleccione **MVC** y haga clic en **Aceptar**.

    ![](media/cdn-cloud-service-with-cdn/cdn-cs-3-mvc-template.PNG)

4. Ahora, puede publicar este rol Web a un servicio de nube de Azure. Haga clic en el proyecto de servicio de nube y seleccione **Publicar**.

    ![](media/cdn-cloud-service-with-cdn/cdn-cs-4-publish-a.png)

5. Si aún no ha firmado en Microsoft Azure, haga clic en la lista desplegable de **Agregar una cuenta...** y haga clic en el elemento de menú **Agregar una cuenta** .

    ![](media/cdn-cloud-service-with-cdn/cdn-cs-5-publish-signin.png)

6. En la página de inicio de sesión, inicie sesión con la cuenta de Microsoft que usó para activar su cuenta de Azure.
7. Una vez que haya iniciado sesión, haga clic en **siguiente**.

    ![](media/cdn-cloud-service-with-cdn/cdn-cs-6-publish-signedin.png)

8. Suponiendo que todavía no lo ha creado una cuenta de almacenamiento o servicio de nube, Visual Studio le ayudará a crear. En el cuadro de diálogo **Crear servicio de nube y cuenta** , escriba el nombre de servicio que desee y seleccione la región que desee. A continuación, haga clic en **crear**.

    ![](media/cdn-cloud-service-with-cdn/cdn-cs-7-publish-createserviceandstorage.png)

9. En la página de configuración de publicación, compruebe la configuración y haga clic en **Publicar**.

    ![](media/cdn-cloud-service-with-cdn/cdn-cs-8-publish-finalize.png)

    >[AZURE.NOTE] El proceso de publicación para los servicios de nube tarda mucho tiempo. El habilitar Web implementar opción de todas las funciones para hacer su servicio de nube mucho más rápida de depuración proporcionando actualizaciones rápidas (aunque temporal) de los roles de Web. Para obtener más información sobre esta opción, consulte [publicar un servicio de nube con las herramientas de Azure](http://msdn.microsoft.com/library/ff683672.aspx).

    Cuando el **Registro de actividad de Microsoft Azure** muestra que el estado de publicación está **completada**, va a crear un extremo CDN se integra con este servicio de nube.

    >[AZURE.WARNING] Si, tras la publicación, el servicio de nube implementada muestra una pantalla de error, es probable porque el servicio de nube que ha implementado está usando un [sistema operativo que no incluye .NET 4.5.2 invitado](../cloud-services/cloud-services-guestos-update-matrix.md#news-updates).  Puede evitar este problema implementando [.NET 4.5.2 como una tarea de inicio](../cloud-services/cloud-services-dotnet-install-dotnet.md).

## <a name="create-a-new-cdn-profile"></a>Crear un nuevo perfil CDN

Un perfil CDN es una colección de extremos CDN.  Cada perfil contiene uno o más extremos CDN.  Puede usar varios perfiles para organizar sus extremos CDN por dominio de internet, aplicación web o cualquier otro criterio.

> [AZURE.TIP] Si ya tiene un perfil CDN que desea usar para este tutorial, vaya a [crear un nuevo extremo CDN](#create-a-new-cdn-endpoint).

[AZURE.INCLUDE [cdn-create-profile](../../includes/cdn-create-profile.md)]

## <a name="create-a-new-cdn-endpoint"></a>Crear un nuevo extremo CDN

**Para crear un nuevo punto final CDN para su cuenta de almacenamiento**

1. En el [Portal de administración de Azure](https://portal.azure.com), vaya a su perfil CDN.  Se puede ha anclado se al panel en el paso anterior.  Si no es así, puede encontrar, haciendo clic en **Examinar**y luego **perfiles CDN**y en el perfil que se va a agregar el extremo.

    Aparece el módulo de perfil CDN.

    ![Perfil CDN][cdn-profile-settings]

2. Haga clic en el botón **Agregar extremo** .

    ![Agregar botón de punto final][cdn-new-endpoint-button]

    Aparece el módulo de **Agregar un extremo** .

    ![Agregar módulo de punto final][cdn-add-endpoint]

3. Escriba un **nombre** para este extremo CDN.  Este nombre se usará para tener acceso a los recursos almacenados en caché en el dominio `<EndpointName>.azureedge.net`.

4. En la lista desplegable **tipo de origen** , seleccione el *servicio de nube*.  

5. En el menú desplegable **nombre de host de origen** , seleccione el servicio de nube.

6. Deje los valores predeterminados para la **ruta de acceso de origen**, **encabezado de host de origen**y **puerto de origen o de protocolo**.  Debe especificar al menos un protocolo (HTTP o HTTPS).

7. Haga clic en el botón **Agregar** para crear el extremo de nuevo.

8. Una vez creado el extremo, aparece en una lista de los extremos del perfil. La vista de lista muestra la dirección URL para tener acceso a contenido de la caché, así como el dominio de origen.

    ![Extremo CDN][cdn-endpoint-success]

    > [AZURE.NOTE] El extremo no inmediatamente estará disponible para su uso.  Puede tardar hasta 90 minutos para el registro en propagarse a través de la red CDN. Los usuarios que intenta utilizar el nombre de dominio CDN inmediatamente pueden recibir el código de estado 404 hasta que el contenido esté disponible a través de la CDN.

## <a name="test-the-cdn-endpoint"></a>Probar el extremo CDN

Cuando el estado de publicación está **completada**, abra una ventana del explorador y vaya a * *http://<cdnName>*.azureedge.net/Content/bootstrap.css**. En configuración de mi, esta URL es:

    http://camservice.azureedge.net/Content/bootstrap.css

Que corresponde a la siguiente dirección URL de origen en el extremo CDN:

    http://camcdnservice.cloudapp.net/Content/bootstrap.css

Cuando se desplaza a * *http://*&lt;cdnName >*.azureedge.net/Content/bootstrap.css**, según el explorador, se le pedirá a descargar o abra el bootstrap.css suministrado desde la aplicación Web publicada.

![](media/cdn-cloud-service-with-cdn/cdn-1-browser-access.PNG)

Del mismo modo puede tener acceso a cualquier dirección URL accesible públicamente en * *http://*&lt;serviceName >*.cloudapp.net/** directamente desde el extremo CDN. Por ejemplo:

-   Un archivo de js desde la ruta de acceso de /Script
-   Cualquier archivo de contenido de la /contenido ruta de acceso
-   Cualquier acción de controlador
-   Si la cadena de consulta está habilitada en el extremo CDN, una dirección URL con las cadenas de consulta

De hecho, con la configuración anterior, puede hospedar el servicio de nube completo de * *http://*&lt;cdnName >*.azureedge.net/**. Si desplaza a **http://camservice.azureedge.net/ ** obtengo el resultado de la acción de inicio o de índice.

![](media/cdn-cloud-service-with-cdn/cdn-2-home-page.PNG)

Esto no significa, sin embargo, que siempre es una buena idea (o, en general, una buena idea) para proporcionar el servicio de un servicio de nube completo a través de CDN de Azure. Algunas de las advertencias son:

-   Este enfoque requiere todo su sitio sea pública, porque CDN Azure no puede servir cualquier contenido privado en este momento.
-   Si el extremo CDN se desconecta por cualquier motivo, si mantenimiento programado o error de usuario, el servicio de nube toda pierde la conexión a menos que se pueden redirigir los clientes a la dirección URL de origen * *http://*&lt;serviceName >*.cloudapp.net/**.
-   Incluso con la configuración de Control de caché personalizada (consulte [Configurar opciones de caché para los archivos estáticos en el servicio de nube](#caching)), un extremo CDN mejora el rendimiento de contenido altamente dinámicos. Si ha intentado cargar la página de inicio desde el extremo CDN como se indicó anteriormente, observe que tardó al menos 5 segundos para cargar la página principal predeterminada la primera vez, que es una página bastante simple. Imagine lo que ocurre a la experiencia del cliente si esta página contiene contenido dinámico que debe actualizar cada minuto. Servir contenido dinámico de un extremo CDN requiere la caducidad de la caché breve, que se traduce en caché frecuentes en el extremo CDN. Esto duele el rendimiento de su servicio de nube y anula la finalidad de una CDN.

La alternativa es determinar qué contenido servir de Azure CDN de forma caso por caso en el servicio de nube. Para ello, ya ha visto cómo tener acceso a archivos de contenido individuales desde el extremo CDN. Le mostrará cómo servir una acción de controlador específico a través del extremo CDN en [servir contenido desde acciones de controlador a través de CDN de Azure](#controller).

<a name="caching"></a>
## <a name="configure-caching-options-for-static-files-in-your-cloud-service"></a>Configurar las opciones de almacenamiento en caché para los archivos estáticos en el servicio de nube ##

Con la integración de CDN de Azure en el servicio de nube, puede especificar cómo desea que el contenido estático en caché en el extremo CDN. Para ello, abra el *archivo Web.config* de su proyecto de rol Web (por ejemplo, WebRole1) y agregue una `<staticContent>` elemento a `<system.webServer>`. El XML siguiente configura la caché para que caduque 3 días.  

    <system.webServer>
      <staticContent>
        <clientCache cacheControlMode="UseMaxAge" cacheControlMaxAge="3.00:00:00"/>
      </staticContent>
      ...
    </system.webServer>

Una vez que se hace esto, todos los archivos estáticos en el servicio de nube observará la misma regla en la memoria caché CDN. Para un control más detallado de configuración de caché, agregue un archivo *Web.config* a una carpeta y la configuración no existe. Por ejemplo, agregue un archivo *Web.config* a la carpeta *\Content* y reemplace el contenido con el siguiente código XML:

    <?xml version="1.0"?>
    <configuration>
      <system.webServer>
        <staticContent>
          <clientCache cacheControlMode="UseMaxAge" cacheControlMaxAge="15.00:00:00"/>
        </staticContent>
      </system.webServer>
    </configuration>

Esta opción hace que todos los archivos estáticos de la carpeta *\Content* en caché de 15 días.

Para obtener más información sobre cómo configurar la `<clientCache>` elemento, vea [caché cliente &lt;clientCache >](http://www.iis.net/configreference/system.webserver/staticcontent/clientcache).

En [servir contenido desde acciones de controlador a través de Azure CDN](#controller), también mostrará cómo configurar opciones de caché de resultados de la acción de controlador en la caché CDN.

<a name="controller"></a>
## <a name="serve-content-from-controller-actions-through-azure-cdn"></a>Servir contenido desde acciones de controlador a través de CDN de Azure ##

Cuando se integra un rol Web de servicio de nube con Azure CDN, es relativamente fácil servir el contenido de las acciones de controlador mediante la CDN de Azure. Distinto de servir su servicio de nube directamente a través de CDN de Azure (mostrado anteriormente), [Maarten Balliauw](https://twitter.com/maartenballiauw) muestra cómo hacer con una divertida MemeGenerator controlador de [reducción de latencia en la web con la CDN de Azure](http://channel9.msdn.com/events/TechDays/Techdays-2014-the-Netherlands/Reducing-latency-on-the-web-with-the-Windows-Azure-CDN). Le simplemente reproducirlo aquí.

Supongamos que en el servicio de nube que desea generar memes basado en una imagen de Chuck Norris los (fotografía por [Alan luz](http://www.flickr.com/photos/alan-light/218493788/)) similar a esta:

![](media/cdn-cloud-service-with-cdn/cdn-5-memegenerator.PNG)

Tiene una sencilla `Index` acción que permite a los clientes especificar la superlativos en la imagen, a continuación, genera la meme una vez que se envíe a la acción. Puesto que es Chuck Norris, esperaría esta página para convertirse en totalmente populares globalmente. Este es un buen ejemplo de servir contenido dinámico semiestructurados con Azure CDN.

Siga los pasos anteriores para esta acción de controlador de configuración:

1. En la carpeta *\Controllers* , cree un nuevo archivo. cs denominado *MemeGeneratorController.cs* y reemplace el contenido con el siguiente código. Asegúrese de reemplazar la parte resaltada con su nombre CDN.  

        using System;
        using System.Collections.Generic;
        using System.Diagnostics;
        using System.Drawing;
        using System.IO;
        using System.Net;
        using System.Web.Hosting;
        using System.Web.Mvc;
        using System.Web.UI;

        namespace WebRole1.Controllers
        {
            public class MemeGeneratorController : Controller
            {
                static readonly Dictionary<string, Tuple<string ,string>> Memes = new Dictionary<string, Tuple<string, string>>();

                public ActionResult Index()
                {
                    return View();
                }

                [HttpPost, ActionName("Index")]
                public ActionResult Index_Post(string top, string bottom)
                {
                    var identifier = Guid.NewGuid().ToString();
                    if (!Memes.ContainsKey(identifier))
                    {
                        Memes.Add(identifier, new Tuple<string, string>(top, bottom));
                    }

                    return Content("<a href=\"" + Url.Action("Show", new {id = identifier}) + "\">here's your meme</a>");
                }

                [OutputCache(VaryByParam = "*", Duration = 1, Location = OutputCacheLocation.Downstream)]
                public ActionResult Show(string id)
                {
                    Tuple<string, string> data = null;
                    if (!Memes.TryGetValue(id, out data))
                    {
                        return new HttpStatusCodeResult(HttpStatusCode.NotFound);
                    }

                    if (Debugger.IsAttached) // Preserve the debug experience
                    {
                        return Redirect(string.Format("/MemeGenerator/Generate?top={0}&bottom={1}", data.Item1, data.Item2));
                    }
                    else // Get content from Azure CDN
                    {
                        return Redirect(string.Format("http://<yourCdnName>.azureedge.net/MemeGenerator/Generate?top={0}&bottom={1}", data.Item1, data.Item2));
                    }
                }

                [OutputCache(VaryByParam = "*", Duration = 3600, Location = OutputCacheLocation.Downstream)]
                public ActionResult Generate(string top, string bottom)
                {
                    string imageFilePath = HostingEnvironment.MapPath("~/Content/chuck.bmp");
                    Bitmap bitmap = (Bitmap)Image.FromFile(imageFilePath);

                    using (Graphics graphics = Graphics.FromImage(bitmap))
                    {
                        SizeF size = new SizeF();
                        using (Font arialFont = FindBestFitFont(bitmap, graphics, top.ToUpperInvariant(), new Font("Arial Narrow", 100), out size))
                        {
                            graphics.DrawString(top.ToUpperInvariant(), arialFont, Brushes.White, new PointF(((bitmap.Width - size.Width) / 2), 10f));
                        }
                        using (Font arialFont = FindBestFitFont(bitmap, graphics, bottom.ToUpperInvariant(), new Font("Arial Narrow", 100), out size))
                        {
                            graphics.DrawString(bottom.ToUpperInvariant(), arialFont, Brushes.White, new PointF(((bitmap.Width - size.Width) / 2), bitmap.Height - 10f - arialFont.Height));
                        }
                    }

                    MemoryStream ms = new MemoryStream();
                    bitmap.Save(ms, System.Drawing.Imaging.ImageFormat.Png);
                    return File(ms.ToArray(), "image/png");
                }

                private Font FindBestFitFont(Image i, Graphics g, String text, Font font, out SizeF size)
                {
                    // Compute actual size, shrink if needed
                    while (true)
                    {
                        size = g.MeasureString(text, font);

                        // It fits, back out
                        if (size.Height < i.Height &&
                             size.Width < i.Width) { return font; }

                        // Try a smaller font (90% of old size)
                        Font oldFont = font;
                        font = new Font(font.Name, (float)(font.Size * .9), font.Style);
                        oldFont.Dispose();
                    }
                }
            }
        }

2. Haga clic en el valor predeterminado `Index()` acción y seleccione **Agregar vista**.

    ![](media/cdn-cloud-service-with-cdn/cdn-6-addview.PNG)

3.  Acepte los valores siguientes y haga clic en **Agregar**.

    ![](media/cdn-cloud-service-with-cdn/cdn-7-configureview.PNG)

4. Abra el nuevo *Views\MemeGenerator\Index.cshtml* y reemplace el contenido con el siguiente HTML simple para enviar la superlativos:

        <h2>Meme Generator</h2>

        <form action="" method="post">
            <input type="text" name="top" placeholder="Enter top text here" />
            <br />
            <input type="text" name="bottom" placeholder="Enter bottom text here" />
            <br />
            <input class="btn" type="submit" value="Generate meme" />
        </form>

5. Vuelva a publicar el servicio de nube y navegue hasta * *http://*&lt;serviceName >*.cloudapp.net/MemeGenerator/Index** en el explorador.

Al enviar los valores del formulario a `/MemeGenerator/Index`, el `Index_Post` método de acción devuelve un vínculo a la `Show` método de acción con el identificador de la entrada correspondiente. Al hacer clic en el vínculo, ponerse en contacto con el siguiente código:  

    [OutputCache(VaryByParam = "*", Duration = 1, Location = OutputCacheLocation.Downstream)]
    public ActionResult Show(string id)
    {
        Tuple<string, string> data = null;
        if (!Memes.TryGetValue(id, out data))
        {
            return new HttpStatusCodeResult(HttpStatusCode.NotFound);
        }

        if (Debugger.IsAttached) // Preserve the debug experience
        {
            return Redirect(string.Format("/MemeGenerator/Generate?top={0}&bottom={1}", data.Item1, data.Item2));
        }
        else // Get content from Azure CDN
        {
            return Redirect(string.Format("http://<yourCDNName>.azureedge.net/MemeGenerator/Generate?top={0}&bottom={1}", data.Item1, data.Item2));
        }
    }

Si se asocia el depurador local, obtendrá la experiencia de depuración normal con una redirección local. Si está ejecutando en el servicio de nube, le redirigirá a:

    http://<yourCDNName>.azureedge.net/MemeGenerator/Generate?top=<formInput>&bottom=<formInput>

Que corresponde a la siguiente dirección URL de origen en el extremo CDN:

    http://<youCloudServiceName>.cloudapp.net/MemeGenerator/Generate?top=<formInput>&bottom=<formInput>


A continuación, puede usar el `OutputCacheAttribute` atributo en la `Generate` método para especificar cómo el resultado de la acción debe almacenarse en caché, que respetan CDN de Azure. El código siguiente especifique una expiración de la memoria caché de 1 hora (3.600 segundos).

    [OutputCache(VaryByParam = "*", Duration = 3600, Location = OutputCacheLocation.Downstream)]

Asimismo, puede servir contenido de cualquier acción de controlador en el servicio de nube a través de su CDN de Azure, con la opción de almacenamiento en caché que desee.

En la siguiente sección, le mostrará cómo servir las secuencias de comandos agrupados y reducir y CSS a través de CDN de Azure.

<a name="bundling"></a>
## <a name="integrate-aspnet-bundling-and-minification-with-azure-cdn"></a>Intégrelo agrupación de ASP.NET y reducción CDN de Azure ##

Hojas de estilo CSS y scripts cambian con poca frecuencia y son candidatos principales para la caché de CDN de Azure. Servir el rol Web completo a través de su CDN Azure es la manera más sencilla de integrar agrupación y reducción con Azure CDN. Sin embargo, como no desea hacerlo, mostrará cómo hacerlo mientras mantiene la experiencia de Developer deseado de agrupación de ASP.NET y reducción, como por ejemplo:

-   Experiencia de depuración excelente modo
-   Implementación simplificada
-   Actualizaciones inmediatas a los clientes para actualizaciones de versión de secuencia de comandos/CSS
-   Mecanismo de reserva cuando se produce un error en el extremo CDN
-   Modificación del código de minimizar

En el proyecto **WebRole1** que creó en [integrar un extremo de Azure CDN con su Azure sitio Web y servir contenido estático en las páginas Web de Azure CDN](#deploy), abra *App_Start\BundleConfig.cs* y eche un vistazo a la `bundles.Add()` método llamadas.

    public static void RegisterBundles(BundleCollection bundles)
    {
        bundles.Add(new ScriptBundle("~/bundles/jquery").Include(
                    "~/Scripts/jquery-{version}.js"));
        ...
    }

La primera `bundles.Add()` instrucción agrega un paquete de secuencia de comandos en el directorio virtual `~/bundles/jquery`. A continuación, abra *Views\Shared\_Layout.cshtml* para ver cómo se procesa la etiqueta de paquete de script. Debería poder encontrar la siguiente línea de código Razor:

    @Scripts.Render("~/bundles/jquery")

Cuando se ejecuta este código Razor en el rol Web de Windows Azure, representará una `<script>` etiqueta para el paquete de secuencia de comandos similar al siguiente:

    <script src="/bundles/jquery?v=FVs3ACwOLIVInrAl5sdzR2jrCDmVOWFbZMY6g6Q0ulE1"></script>

Sin embargo, cuando se ejecuta en Visual Studio escribiendo `F5`, se representan cada archivo de script con el paquete de forma individual (en el caso anterior, archivo de solo script está en el paquete):

    <script src="/Scripts/jquery-1.10.2.js"></script>

Esto le permite depurar el código de JavaScript en su entorno de desarrollo mientras reducir las conexiones de cliente simultáneas (agrupación) y mejorar el archivo descargar rendimiento (reducción) de producción. Es una excelente característica conservar con integración de CDN de Azure. Además, dado que el paquete representado ya contiene una cadena de versión generada automáticamente, desea replicar esa funcionalidad para que siempre que actualice su versión de jQuery mediante NuGet, se puede actualizar en el cliente tan pronto como sea posible.

Siga los pasos a continuación para integración ASP.NET agrupación y reducción con el extremo CDN.

1. En el *App_Start\BundleConfig.cs*, modifique la `bundles.Add()` métodos para utilizar un diferentes [constructor del paquete](http://msdn.microsoft.com/library/jj646464.aspx), que se especifica una dirección CDN. Para ello, reemplace el `RegisterBundles` definición de método con el siguiente código:  

        public static void RegisterBundles(BundleCollection bundles)
        {
            bundles.UseCdn = true;
            var version = System.Reflection.Assembly.GetAssembly(typeof(Controllers.HomeController))
                .GetName().Version.ToString();
            var cdnUrl = "http://<yourCDNName>.azureedge.net/{0}?v=" + version;

            bundles.Add(new ScriptBundle("~/bundles/jquery", string.Format(cdnUrl, "bundles/jquery")).Include(
                        "~/Scripts/jquery-{version}.js"));

            bundles.Add(new ScriptBundle("~/bundles/jqueryval", string.Format(cdnUrl, "bundles/jqueryval")).Include(
                        "~/Scripts/jquery.validate*"));

            // Use the development version of Modernizr to develop with and learn from. Then, when you're
            // ready for production, use the build tool at http://modernizr.com to pick only the tests you need.
            bundles.Add(new ScriptBundle("~/bundles/modernizr", string.Format(cdnUrl, "bundles/modernizer")).Include(
                        "~/Scripts/modernizr-*"));

            bundles.Add(new ScriptBundle("~/bundles/bootstrap", string.Format(cdnUrl, "bundles/bootstrap")).Include(
                        "~/Scripts/bootstrap.js",
                        "~/Scripts/respond.js"));

            bundles.Add(new StyleBundle("~/Content/css", string.Format(cdnUrl, "Content/css")).Include(
                        "~/Content/bootstrap.css",
                        "~/Content/site.css"));
        }

    Asegúrese de reemplazar `<yourCDNName>` con el nombre de su CDN de Azure.

    En palabras sencillas, se establece `bundles.UseCdn = true` y agrega una dirección URL de CDN cuidadosamente a cada paquete. Por ejemplo, el primer constructor en el código:

        new ScriptBundle("~/bundles/jquery", string.Format(cdnUrl, "bundles/jquery"))

    es el mismo que:

        new ScriptBundle("~/bundles/jquery", string.Format(cdnUrl, "http://<yourCDNName>.azureedge.net/bundles/jquery?v=<W.X.Y.Z>"))

    Este constructor indica la agrupación de ASP.NET y reducción para representar archivos de script individuales al depurar localmente, pero use la dirección CDN especificada para tener acceso a la secuencia de comandos en cuestión. Sin embargo, tenga en cuenta dos características importantes con esta dirección URL de CDN cuidadosamente:

    -   El origen de esta URL CDN es `http://<yourCloudService>.cloudapp.net/bundles/jquery?v=<W.X.Y.Z>`, que es el directorio virtual de la agrupación de secuencia de comandos en el servicio de nube.
    -   Puesto que está utilizando constructor CDN, la etiqueta de script CDN para el paquete ya no contiene la cadena de versión generada automáticamente en la dirección URL representada. Debe generar manualmente una cadena de versión único cada vez que se modifica el paquete de secuencia de comandos para forzar un error de caché en su CDN de Azure. Al mismo tiempo, esta cadena de versión único debe permanecer constante la vida de la implementación para maximizar la caché en su CDN Azure después de que se implementa el paquete.
    -   La cadena de consulta v = extrae < W.X.Y.Z > desde *Properties\AssemblyInfo* en su proyecto de rol Web. Puede tener un flujo de trabajo de implementación que incluya incremento de la versión de ensamblado cada vez que publique en Azure. O bien, solo puede modificar *Properties\AssemblyInfo* en el proyecto para incrementar automáticamente la cadena de versión cada vez que genere, con el carácter comodín ' *'. Por ejemplo:

            [assembly: AssemblyVersion("1.0.0.*")]

        Cualquier otra estrategia de simplificar la generación de una cadena única para la vida de una implementación funcionará aquí.

3. Volver a publicar el servicio de nube y tener acceso a la página principal.

4. Ver el código HTML de la página. Debería poder ver la dirección URL de CDN representado por una cadena de versión único cada vez que vuelva a publicar los cambios en el servicio de nube. Por ejemplo:  

        ...

        <link href="http://camservice.azureedge.net/Content/css?v=1.0.0.25449" rel="stylesheet"/>

        <script src="http://camservice.azureedge.net/bundles/modernizer?v=1.0.0.25449"></script>

        ...

        <script src="http://camservice.azureedge.net/bundles/jquery?v=1.0.0.25449"></script>

        <script src="http://camservice.azureedge.net/bundles/bootstrap?v=1.0.0.25449"></script>

        ...

5. En Visual Studio, depurar el servicio de nube en Visual Studio escribiendo `F5`.,

6. Ver el código HTML de la página. Aún se muestra cada archivo de script representado por separado para que pueda tener una depuración coherente experiencia en Visual Studio.  

        ...

            <link href="/Content/bootstrap.css" rel="stylesheet"/>
        <link href="/Content/site.css" rel="stylesheet"/>

            <script src="/Scripts/modernizr-2.6.2.js"></script>

        ...

            <script src="/Scripts/jquery-1.10.2.js"></script>

            <script src="/Scripts/bootstrap.js"></script>
        <script src="/Scripts/respond.js"></script>

        ...   

<a name="fallback"></a>
## <a name="fallback-mechanism-for-cdn-urls"></a>Mecanismo de reserva para las direcciones URL de CDN ##

Cuando se produce un error en el extremo de Azure CDN por cualquier motivo, desea su página Web suficientemente inteligente obtener acceso a su servidor Web de origen como la opción de reserva para cargar JavaScript o inicio. Es suficientemente grave perder imágenes en su sitio Web debido a la falta de disponibilidad CDN, pero mucho más graves perder la funcionalidad de página fundamentales de los scripts y hojas de estilo.

La clase de [paquete](http://msdn.microsoft.com/library/system.web.optimization.bundle.aspx) contiene una propiedad denominada [CdnFallbackExpression](http://msdn.microsoft.com/library/system.web.optimization.bundle.cdnfallbackexpression.aspx) que le permite configurar el mecanismo de reserva error CDN. Para usar esta propiedad, siga los pasos siguientes:

1. En el proyecto de rol Web, abra *App_Start\BundleConfig.cs*, donde ha agregado una dirección URL de CDN en cada [constructor de paquete](http://msdn.microsoft.com/library/jj646464.aspx)y realice los siguientes cambios resaltados para agregar mecanismo de reserva a los paquetes de forma predeterminada:  

        public static void RegisterBundles(BundleCollection bundles)
        {
            var version = System.Reflection.Assembly.GetAssembly(typeof(BundleConfig))
                .GetName().Version.ToString();
            var cdnUrl = "http://cdnurl.azureedge.net/.../{0}?" + version;
            bundles.UseCdn = true;

            bundles.Add(new ScriptBundle("~/bundles/jquery", string.Format(cdnUrl, "bundles/jquery"))
                        { CdnFallbackExpression = "window.jquery" }
                        .Include("~/Scripts/jquery-{version}.js"));

            bundles.Add(new ScriptBundle("~/bundles/jqueryval", string.Format(cdnUrl, "bundles/jqueryval"))
                        { CdnFallbackExpression = "$.validator" }
                        .Include("~/Scripts/jquery.validate*"));

            // Use the development version of Modernizr to develop with and learn from. Then, when you&#39;re
            // ready for production, use the build tool at http://modernizr.com to pick only the tests you need.
            bundles.Add(new ScriptBundle("~/bundles/modernizr", string.Format(cdnUrl, "bundles/modernizer"))
                        { CdnFallbackExpression = "window.Modernizr" }
                        .Include("~/Scripts/modernizr-*"));

            bundles.Add(new ScriptBundle("~/bundles/bootstrap", string.Format(cdnUrl, "bundles/bootstrap"))     
                        { CdnFallbackExpression = "$.fn.modal" }
                        .Include(
                                "~/Scripts/bootstrap.js",
                                "~/Scripts/respond.js"));

            bundles.Add(new StyleBundle("~/Content/css", string.Format(cdnUrl, "Content/css")).Include(
                        "~/Content/bootstrap.css",
                        "~/Content/site.css"));
        }

    Cuando `CdnFallbackExpression` es no nulo script se inserta en el código HTML para comprobar si el paquete se ha cargado correctamente y, si no, el paquete de acceso directamente desde el servidor Web de origen. Esta propiedad debe estar establecida en una expresión de JavaScript que comprueba si el paquete CDN respectivo está cargado correctamente. La expresión es necesaria probar cada paquete varía según el contenido. Para los paquetes de forma predeterminada anteriores:

    -   `window.jquery`se define en jquery-{versión} js
    -   `$.validator`se define en jquery.validate.js
    -   `window.Modernizr`se define en modernizer-{versión} js
    -   `$.fn.modal`se define en bootstrap.js

    Es posible que haya observado que no ha definido CdnFallbackExpression para la `~/Cointent/css` paquete. Esto es porque hay un [error en System.Web.Optimization](https://aspnetoptimization.codeplex.com/workitem/104) que inserta un `<script>` etiqueta de la reserva CSS en lugar de la esperada `<link>` etiqueta.

    Sin embargo, hay una buena [Estilo paquete reserva](https://github.com/EmberConsultingGroup/StyleBundleFallback) ofrecido por [Ember Consulting Group](https://github.com/EmberConsultingGroup).

2. Para usar la solución alternativa para CSS, crear un nuevo archivo. cs en carpeta de *App_Start* de su proyecto de rol Web llamada *StyleBundleExtensions.cs*y reemplace su contenido con el [código de GitHub](https://github.com/EmberConsultingGroup/StyleBundleFallback/blob/master/Website/App_Start/StyleBundleExtensions.cs).

4. En *App_Start\StyleFundleExtensions.cs*, cambie el nombre del espacio de nombres al nombre de su rol Web (por ejemplo, **WebRole1**).

3. Volver a `App_Start\BundleConfig.cs` y modificar la última `bundles.Add` instrucción con el siguiente código resaltado:  

        bundles.Add(new StyleBundle("~/Content/css", string.Format(cdnUrl, "Content/css"))
            <mark>.IncludeFallback("~/Content/css", "sr-only", "width", "1px")</mark>
            .Include(
                  "~/Content/bootstrap.css",
                  "~/Content/site.css"));

    Este nuevo método de extensión usa la misma idea para insertar script en el código HTML para comprobar el DOM para la un nombre de la clase coincidentes, el nombre de la regla y el valor de regla definidos en el paquete CSS y cae el servidor Web de origen si no puede encontrar la coincidencia.

4. Vuelva a publicar el servicio de nube y tener acceso a la página principal.

5. Ver el código HTML de la página. Encontrará secuencias de comandos insertados similar al siguiente:    

        ...

        <link href="http://az632148.azureedge.net/Content/css?v=1.0.0.25474" rel="stylesheet"/>
        <script>(function() {
                        var loadFallback,
                            len = document.styleSheets.length;
                        for (var i = 0; i < len; i++) {
                            var sheet = document.styleSheets[i];
                            if (sheet.href.indexOf('http://camservice.azureedge.net/Content/css?v=1.0.0.25474') !== -1) {
                                var meta = document.createElement('meta');
                                meta.className = 'sr-only';
                                document.head.appendChild(meta);
                                var value = window.getComputedStyle(meta).getPropertyValue('width');
                                document.head.removeChild(meta);
                                if (value !== '1px') {
                                    document.write('<link href="/Content/css" rel="stylesheet" type="text/css" />');
                                }
                            }
                        }
                        return true;
                    }())||document.write('<script src="/Content/css"><\/script>');</script>

            <script src="http://camservice.azureedge.net/bundles/modernizer?v=1.0.0.25474"></script>
        <script>(window.Modernizr)||document.write('<script src="/bundles/modernizr"><\/script>');</script>

        ...

            <script src="http://camservice.azureedge.net/bundles/jquery?v=1.0.0.25474"></script>
        <script>(window.jquery)||document.write('<script src="/bundles/jquery"><\/script>');</script>

            <script src="http://camservice.azureedge.net/bundles/bootstrap?v=1.0.0.25474"></script>
        <script>($.fn.modal)||document.write('<script src="/bundles/bootstrap"><\/script>');</script>

        ...


    Tenga en cuenta que script insertado para el paquete CSS todavía contiene el residuo erróneas desde el `CdnFallbackExpression` propiedad en la línea:

        }())||document.write('<script src="/Content/css"><\/script>');</script>

    Pero desde la primera parte de la || expresión siempre devolverá true (en la línea directamente encima), nunca se ejecutará la función de la sección.

## <a name="more-information"></a>Más información ##
- [Información general de la red de Azure entrega de contenido (CDN)](http://msdn.microsoft.com/library/azure/ff919703.aspx)
- [Uso de Azure CDN](cdn-create-new-endpoint.md)
- [Reducción y agrupación de ASP.NET](http://www.asp.net/mvc/tutorials/mvc-4/bundling-and-minification)



[new-cdn-profile]: ./media/cdn-cloud-service-with-cdn/cdn-new-profile.png
[cdn-profile-settings]: ./media/cdn-cloud-service-with-cdn/cdn-profile-settings.png
[cdn-new-endpoint-button]: ./media/cdn-cloud-service-with-cdn/cdn-new-endpoint-button.png
[cdn-add-endpoint]: ./media/cdn-cloud-service-with-cdn/cdn-add-endpoint.png
[cdn-endpoint-success]: ./media/cdn-cloud-service-with-cdn/cdn-endpoint-success.png
