<properties
    pageTitle="Azure aplicación servicio Online config y extensiones avanzadas"
    description="Utilizar declaraciones de Transformation(XDT) del documento XML para transformar el archivo ApplicationHost.config en la aplicación de servicio de la aplicación de Azure y agregar extensiones privadas para habilitar acciones de administración personalizada."
    authors="cephalin"
    writer="cephalin"
    editor="mollybos"
    manager="wpickett"
    services="app-service"
    documentationCenter=""/>

<tags
    ms.service="app-service"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="02/25/2016"
    ms.author="cephalin"/>

# <a name="azure-app-service-web-app-advanced-config-and-extensions"></a>Azure aplicación servicio Online config y extensiones avanzadas

Mediante el uso de declaraciones de [Transformación de documento XML](http://msdn.microsoft.com/library/dd465326.aspx) (XDT), puede transformar el archivo [ApplicationHost.config](http://www.iis.net/learn/get-started/planning-your-iis-architecture/introduction-to-applicationhostconfig) en la aplicación web de la aplicación de servicio de Azure. También puede usar las declaraciones XDT agregar extensiones privadas para habilitar acciones de administración de la aplicación web personalizada. Este artículo incluye una extensión de aplicación de ejemplo PHP administrador web que permite la administración de configuración de PHP mediante una interfaz web.

[AZURE.INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]

##<a id="transform"></a>Configuración avanzada a través de ApplicationHost.config
La plataforma de servicio de aplicación proporciona flexibilidad y control de configuración de aplicación web. Aunque el archivo de configuración de IIS ApplicationHost.config estándar no está disponible para su edición directa en aplicación de servicio, la plataforma es compatible con un modelo de transformación de ApplicationHost.config descriptiva basado en XML documento transformación (XDT).

Para aprovechar la funcionalidad de transformación, se crea un archivo ApplicationHost.xdt con contenido XDT y poner en la raíz del sitio (d:\home\site) en la [Consola de Kudu](https://github.com/projectkudu/kudu/wiki/Kudu-console). Debe reiniciar la aplicación Web de cambios surtan efecto.

En el siguiente ejemplo applicationHost.xdt muestra cómo agregar una nueva variable de entorno personalizado a una aplicación web que usa PHP 5.4.

    <?xml version="1.0"?>
    <configuration xmlns:xdt="http://schemas.microsoft.com/XML-Document-Transform">
        <system.webServer>
                <fastCgi>
                    <application>
                        <environmentVariables>
                                <environmentVariable name="CONFIGTEST" value="TEST" xdt:Transform="Insert" xdt:Locator="XPath(/configuration/system.webServer/fastCgi/application[contains(@fullPath,'5.4')]/environmentVariables)" />
                        </environmentVariables>
                    </application>
                </fastCgi>
        </system.webServer>
    </configuration>


Un archivo de registro con el estado de transformación y detalles está disponible desde la raíz FTP en LogFiles\Transform.

Para obtener más ejemplos, vea [https://github.com/projectkudu/kudu/wiki/Xdt-transform-samples](https://github.com/projectkudu/kudu/wiki/Xdt-transform-samples).

**Nota**<br />
Elementos de la lista de módulos en `system.webServer` no se pueden quitar ni reordenados, pero son posibles las adiciones a la lista.


##<a id="extend"></a>Ampliar la aplicación web

###<a id="overview"></a>Información general sobre las extensiones de aplicación web privado

Aplicación de servicio es compatible con las extensiones de aplicación web como un punto de la capacidad de ampliación para acciones administrativas. De hecho, algunas características de la plataforma de servicio de la aplicación se implementan como extensiones preinstaladas. Aunque no se puede modificar las extensiones de plataforma preinstalado, puede crear y configurar extensiones privadas su propia aplicación web. Esta funcionalidad también se basa en declaraciones XDT. Los pasos básicos para crear una extensión de aplicación web privada son las siguientes:

1. Extensión de aplicación **contenido**Web: crear cualquier aplicación web compatible con la aplicación de servicio
2. Web app extensión **declaración**: crear un archivo de ApplicationHost.xdt
3. **Implementación**de la extensión de aplicación de Web: colocar contenido en la carpeta SiteExtensions en`root`

Vínculos internos para la aplicación web deben apuntar a una ruta de acceso relativa a la ruta de la aplicación especificada en el archivo ApplicationHost.xdt. Cualquier cambio en el archivo ApplicationHost.xdt requiere un reciclaje de la aplicación web.

**Nota**: la información adicional para estos elementos clave está disponible en [https://github.com/projectkudu/kudu/wiki/Azure-Site-Extensions](https://github.com/projectkudu/kudu/wiki/Azure-Site-Extensions).

Se incluye un ejemplo detallado para ilustrar los pasos necesarios para crear y habilitar una extensión de aplicación web privado. El código fuente para el siguiente ejemplo de administrador de PHP puede descargarse desde [https://github.com/projectkudu/PHPManager](https://github.com/projectkudu/PHPManager).

###<a id="SiteSample"></a>Ejemplo de extensión de aplicación Web: Administrador de PHP

Administrador de PHP es una extensión de aplicación web que permite a los administradores de la aplicación ver fácilmente y configurar las opciones de PHP mediante una interfaz web en lugar de tener que modificar los archivos .ini PHP directamente web. Archivos de configuración comunes para PHP incluyan archivo php.ini ubicado en archivos de programa y la. archivo user.ini ubicado en la carpeta raíz de la aplicación web. Como archivo php.ini no se puede modificar directamente en la plataforma de servicio de aplicación, la extensión del Administrador de PHP usa la. archivo user.ini para aplicar los cambios de configuración.

####<a id="PHPwebapp"></a>La aplicación web de PHP Manager

La siguiente es la página de inicio de la implementación de administrador de PHP.

![TransformSitePHPUI][TransformSitePHPUI]

Como puede ver, una extensión de aplicación web es igual que una aplicación web normal, pero con un archivo de ApplicationHost.xdt adicional que se encuentra en la carpeta raíz de la aplicación web (más detalles sobre el archivo ApplicationHost.xdt están disponibles en la siguiente sección de este artículo).

La extensión de PHP Manager se creó con la plantilla de aplicación Web ASP.NET MVC 4 de Visual Studio. La siguiente vista del explorador de soluciones muestra la estructura de la extensión del Administrador de PHP.

![TransformSiteSolEx][TransformSiteSolEx]

La lógica solo especial necesaria para archivo i/OS es indicar dónde se encuentra el directorio wwwroot de la aplicación web. Como se muestra en el siguiente ejemplo, la variable de entorno "HOME" indica la ruta de acceso de la aplicación web y la ruta de acceso de wwwroot puede crearse agregando "site\wwwroot":

    /// <summary>
    /// Gives the location of the .user.ini file, even if one doesn't exist yet
    /// </summary>
    private static string GetUserSettingsFilePath()
    {
            var rootPath = Environment.GetEnvironmentVariable("HOME"); // For use on Azure Websites
            if (rootPath == null)
            {
                rootPath = System.IO.Path.GetTempPath(); // For testing purposes
            };
            var userSettingsFile = Path.Combine(rootPath, @"site\wwwroot\.user.ini");
            return userSettingsFile;
    }


Una vez que la ruta del directorio, puede utilizar operaciones de i/OS de archivo normal para leer y escribir en archivos.

Un punto de precaución con las extensiones de aplicación web que se refiere a la administración de los vínculos internos.  Si tiene todos los vínculos en los archivos HTML que proporcionan rutas absolutas para vínculos internos en la aplicación web, debe asegurarse de que esos vínculos llevan el nombre de la extensión como raíz. Esto es necesario porque la raíz de la extensión es ahora "/`[your-extension-name]`/" en lugar de ser simplemente "/", así que cualquier interno vínculos se deben actualizar según corresponda. Por ejemplo, supongamos que su código incluye un vínculo a la siguiente:

`"<a href="/Home/Settings">PHP Settings</a>"`

Cuando el vínculo forma parte de una extensión de aplicación web, el vínculo debe estar en la forma siguiente:

`"<a href="/[your-site-name]/Home/Settings">Settings</a>"`

Puede evitar este requisito, ya sea por usar solo rutas de acceso relativas dentro de la aplicación web o en el caso de las aplicaciones de ASP.NET mediante la `@Html.ActionLink` método que crea los vínculos correspondientes.

####<a id="XDT"></a>El archivo applicationHost.xdt

El código para la extensión de la aplicación web está en %HOME%\SiteExtensions\[su nombre de extensión]. Se le llame la raíz de la extensión.  

Para registrar la extensión de la aplicación web con el archivo applicationHost.config, debe colocar un archivo llamado ApplicationHost.xdt en la raíz de la extensión. El contenido del archivo ApplicationHost.xdt debe ser como sigue:

    <?xml version="1.0"?>
    <configuration xmlns:xdt="http://schemas.microsoft.com/XML-Document-Transform">
        <system.applicationHost>
                <sites>
                    <site name="%XDT_SCMSITENAME%" xdt:Locator="Match(name)">
                        <!-- NOTE: Add your extension name in the application paths below -->
                        <application path="/[your-extension-name]" xdt:Locator="Match(path)" xdt:Transform="Remove" />
                        <application path="/[your-extension-name]" applicationPool="%XDT_APPPOOLNAME%" xdt:Transform="Insert">
                            <virtualDirectory path="/" physicalPath="%XDT_EXTENSIONPATH%" />
                        </application>
                    </site>
                </sites>
        </system.applicationHost>
    </configuration>

El nombre que se selecciona como el nombre de la extensión debe tener el mismo nombre como la carpeta raíz de extensión.

Esto tiene el efecto de agregar una nueva ruta de acceso de aplicación a la `system.applicationHost` lista de sitios en el sitio SCM. El sitio SCM es un extremo de la administración de sitio con las credenciales de acceso específico. Tiene la dirección URL `https://[your-site-name].scm.azurewebsites.net`.  

    <system.applicationHost>
        ...
        <site name="~1[your-website]" id="1716402716">
                <bindings>
                    <binding protocol="http" bindingInformation="*:80: [your-website].scm.azurewebsites.net" />
                    <binding protocol="https" bindingInformation="*:443: [your-website].scm.azurewebsites.net" />
                </bindings>
                <traceFailedRequestsLogging enabled="false" directory="C:\DWASFiles\Sites\[your-website]\VirtualDirectory0\LogFiles" />
                <detailedErrorLogging enabled="false" directory="C:\DWASFiles\Sites\[your-website]\VirtualDirectory0\LogFiles\DetailedErrors" />
                <logFile logSiteId="false" />
                <application path="/" applicationPool="[your-website]">
                    <virtualDirectory path="/" physicalPath="D:\Program Files (x86)\SiteExtensions\Kudu\1.24.20926.5" />
                </application>
                <!-- Note the custom changes that go here -->
                <application path="/[your-extension-name]" applicationPool="[your-website]">
                    <virtualDirectory path="/" physicalPath="C:\DWASFiles\Sites\[your-website]\VirtualDirectory0\SiteExtensions\[your-extension-name]" />
                </application>
            </site>
    </sites>
      ...
    </system.applicationHost>

###<a id="deploy"></a>Implementación de extensión de aplicación Web

Para instalar la extensión de la aplicación web, puede usar FTP para copiar todos los archivos de la aplicación web a la `\SiteExtensions\[your-extension-name]` carpeta de la aplicación web en el que desea instalar la extensión.  Asegúrese de copiar el archivo ApplicationHost.xdt en esta ubicación también. Reinicie la aplicación web para habilitar la extensión.

Debería poder ver la extensión de la aplicación web en:

`https://[your-site-name].scm.azurewebsites.net/[your-extension-name]`

Tenga en cuenta que la dirección URL es similar a la dirección URL para la aplicación web, salvo que usa HTTPS y que contiene ".scm".

Es posible deshabilitar todas las extensiones de (no preinstaladas) privadas para la aplicación web de investigación y desarrollo agregando una configuración de la aplicación con la clave `WEBSITE_PRIVATE_EXTENSIONS` y un valor de `0`.

>[AZURE.NOTE] Si desea empezar a trabajar con el servicio de aplicación de Azure antes de suscribirse para una cuenta de Azure, vaya a [Probar el servicio de aplicaciones](http://go.microsoft.com/fwlink/?LinkId=523751), donde puede crear inmediatamente una aplicación web de starter corta duración en la aplicación de servicio. Sin tarjetas de crédito obligatoria; sin compromisos.

## <a name="whats-changed"></a>¿Qué ha cambiado
* Encontrará una guía para el cambio de sitios Web a la aplicación de servicio: [servicio de aplicación de Azure y su influencia sobre los servicios de Azure existentes](http://go.microsoft.com/fwlink/?LinkId=529714)

<!-- IMAGES -->
[TransformSitePHPUI]: ./media/web-sites-transform-extend/TransformSitePHPUI.png
[TransformSiteSolEx]: ./media/web-sites-transform-extend/TransformSiteSolEx.png
 
