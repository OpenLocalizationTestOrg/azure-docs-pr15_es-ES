<properties
    pageTitle="Implemente la aplicación en Azure de aplicación de servicio"
    description="Aprenda a implementar la aplicación de servicio de la aplicación de Azure."
    services="app-service"
    documentationCenter=""
    authors="cephalin"
    manager="wpickett"
    editor="mollybos"/>

<tags
    ms.service="app-service"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/06/2016"
    ms.author="cephalin;dariac"/>
    
# <a name="deploy-your-app-to-azure-app-service"></a>Implemente la aplicación en Azure de aplicación de servicio

Este artículo le ayudará a determinar la mejor opción para implementar los archivos de su aplicación web, una aplicación móvil back-end o aplicación API de [Servicio de aplicación de Azure](http://go.microsoft.com/fwlink/?LinkId=529714)y le guía a los recursos adecuados con instrucciones específicas de la opción que prefiera.

## <a name="overview"></a>Información general de implementación de servicio de aplicación Azure

Servicio de aplicaciones de Azure mantiene el marco de la aplicación para usted (ASP.NET, PHP, Node.js, etcetera). Algunos entornos habilitado de forma predeterminada mientras otras personas, como Java y Python, que tenga una configuración de marca de verificación simple para activarlo. Además, puede personalizar el marco de la aplicación, como la versión PHP o los bits de su tiempo de ejecución. Para obtener más información, vea [Configurar la aplicación de servicio de aplicaciones de Azure](web-sites-configure.md).

Puesto que no tiene que preocuparse por el marco del servidor o la aplicación web, implementar la aplicación de servicio de la aplicación se trata de implementar su código, archivos binarios, archivos de contenido y su estructura de directorios correspondientes, en el [directorio **/site/wwwroot** ](https://github.com/projectkudu/kudu/wiki/File-structure-on-azure) en Azure (o la **/sitios/wwwroot/App_Data o trabajos/** directorio WebJobs). Aplicación de servicio es compatible con las siguientes opciones de implementación: 

- [FTP o FTPS](https://en.wikipedia.org/wiki/File_Transfer_Protocol): Use su favorito FTP o FTPS habilitado herramienta para mover los archivos a Azure, de [FileZilla](https://filezilla-project.org) a IDE completos como [NetBeans](https://netbeans.org). Esto es estrictamente un proceso de carga de archivos. Aplicación de servicio, no proporciona servicios adicionales, como el control de versiones, la administración de la estructura de archivo etcetera. 

- [Kudu (Git/mercuriano o OneDrive o Dropbox)](https://github.com/projectkudu/kudu/wiki/Deployment): Use el [motor de implementación](https://github.com/projectkudu/kudu/wiki) de la aplicación de servicio. El código de inserción a Kudu directamente desde cualquier repositorio. Kudu también proporciona servicios agregados cada vez que se inserta código, como control de versiones, restauración de paquete, MSBuild y [Enlaces web](https://github.com/projectkudu/kudu/wiki/Web-hooks) para su implementación continua y otras tareas de automatización. El motor de implementación de Kudu es compatible con 3 tipos diferentes de orígenes de implementación:   
    * Sincronizar contenido desde OneDrive y Dropbox   
    * Implementación de continua basada en repositorio con la sincronización automática de GitHub, Bitbucket y Visual Studio Team Services  
    * Implementación basada en el repositorio con sincronización manual de Git local  

- [Implementar Web](http://www.iis.net/learn/publish/using-web-deploy/introduction-to-web-deploy): implementar código para la aplicación de servicio directamente desde Microsoft favorita herramientas, como Visual Studio mediante el mismo conjunto de herramientas que automatiza la implementación de los servidores de IIS. Es compatible con esta herramienta de implementación sólo diferencias, creación de base de datos, las transformaciones de cadenas de conexión, etcetera. Implementar Web difiere de Kudu en que se crean archivos binarios de aplicación antes de que se implementen en Azure. Similar a FTP, no se proporcionan servicios adicionales por la aplicación de servicio.

Herramientas de desarrollo de web populares admiten uno o varios de estos procesos de implementación. Mientras la herramienta que elija determina los procesos de implementación que puede aprovechar, la funcionalidad de DevOps real a su disposición depende de la combinación del proceso de implementación y las herramientas específicas que elija. Por ejemplo, si realiza implementar Web de [Visual Studio con Azure SDK](#vspros), incluso si no recibe la automatización de Kudu, obtener restauración de paquete y MSBuild automatización en Visual Studio. 

>[AZURE.NOTE] Estos procesos de implementación no realmente [aprovisionar los recursos Azure](../resource-group-template-deploy-portal.md) que necesite la aplicación. Sin embargo, la mayoría de los artículos de procedimientos vinculadas mostrar cómo aprovisionar la aplicación e implemente el código-to-end. También puede encontrar opciones adicionales para el aprovisionamiento de recursos de Azure en la sección de [implementación de automatizar mediante herramientas de línea de comandos](#automate) .
     
## <a name="ftp"></a>Implementar a través de FTP copiando archivos en Azure manualmente
Si está acostumbrado a copiar manualmente el contenido web en un servidor web, puede usar una utilidad [FTP](http://en.wikipedia.org/wiki/File_Transfer_Protocol) para copiar los archivos, como el Explorador de Windows o [FileZilla](https://filezilla-project.org/).

Los profesionales de copiar archivos manualmente son:

- Familiaridad y complejidad mínima para herramientas de FTP. 
- Saber exactamente dónde se van a sus archivos.
- Mayor seguridad con FTPS.

Los inconvenientes de copiar archivos manualmente son:

- Necesidad de conocer cómo implementar los archivos en los directorios correctos en la aplicación de servicio. 
- Sin control de versiones para deshacer cuando se producen errores.
- Sin historial de implementación integrados para solucionar problemas de implementación.
- Posible implementación mucho tiempo porque muchas de las herramientas FTP no proporcionan solo diferencias copiando y simplemente copiar todos los archivos.  

### <a name="howtoftp"></a>Cómo implementar copiando archivos en Azure manualmente
Copiar archivos a Azure implica unos sencillos pasos:

1. Suponiendo que ya se ha establecido las credenciales de la implementación, obtener la información de conexión FTP, vaya a **configuración** > **Propiedades**y, a continuación, copiar los valores de **Usuario FTP e implementación**, **Nombre de Host FTP**y **FTPS nombre de Host**. Copie el valor de usuario **FTP e implementación usuario** tal como aparece en el Portal de Azure, incluido el nombre de la aplicación para proporcionar contexto apropiado para el servidor FTP.
2. Desde el cliente de FTP, use la información de conexión recopilada para conectarse a su aplicación.
3. Copie los archivos y su estructura de directorios correspondientes en el [directorio **/site/wwwroot** ](https://github.com/projectkudu/kudu/wiki/File-structure-on-azure) en Azure (o la **/sitios/wwwroot/App_Data o trabajos/** directorio WebJobs).
4. Vaya a la dirección URL de la aplicación para comprobar que la aplicación se está ejecutando correctamente. 

Para obtener más información, consulte los siguientes recursos:

* [Crear una aplicación web PHP MySQL e implementar mediante FTP](web-sites-php-mysql-deploy-use-ftp.md).

## <a name="dropbox"></a>Implementar la sincronización con una carpeta de la nube
Una buena alternativa a [Copiar archivos manualmente](#ftp) sincroniza archivos y carpetas a la aplicación de servicio de un servicio de almacenamiento de nube como OneDrive y Dropbox. Sincronización de una carpeta de nube utiliza el proceso de Kudu para la implementación (consulte [información general sobre los procesos de implementación](#overview)).

Los profesionales de sincronización de una carpeta de nube son:

- Simplificar de implementación. Servicios como OneDrive y Dropbox proporcionan a clientes de escritorio de sincronización, de modo que su directorio de trabajo local también es su implementación.
- Implementación de un solo clic.
- Todas las funciones en el motor de implementación de Kudu está disponible (por ejemplo, restauración de paquete, automatización).

Los inconvenientes de sincronización de una carpeta de nube son:

- Sin control de versiones para deshacer cuando se producen errores.
- Sin implementación automatizada, la sincronización manual se requiere.

### <a name="howtodropbox"></a>Cómo implementar la sincronización con una carpeta de la nube
En el [Portal de Azure](https://portal.azure.com), puede designar una carpeta de sincronización de contenido en el almacenamiento de nube de OneDrive o Dropbox, trabajar con el código de la aplicación y el contenido de la carpeta y sincronizar con la aplicación de servicio con el clic de botón.

* [Sincronizar el contenido de una carpeta de nube al servicio de la aplicación de Azure](app-service-deploy-content-sync.md). 

## <a name="continuousdeployment"></a>Implementar continuamente de un servicio de control de código fuente en la nube
Si su equipo de desarrollo usa un servicio de administración (SCM) de código de origen en la nube como [Visual Studio Team Services](http://www.visualstudio.com/), [GitHub](https://www.github.com)o [BitBucket](https://bitbucket.org/), puede configurar el servicio de aplicaciones para integrar con su repositorio e implementar continuamente. 

Los profesionales de implementación de un servicio de control de código fuente en la nube son:

- Control de versiones para habilitar la desinstalación.
- Posibilidad de configurar la implementación continua para Git (y mercuriano donde sea aplicable) repositorios. 
- Implementación de la rama específica, puede implementar diferentes ramas a diferentes [ranuras](web-sites-staged-publishing.md).
- Todas las funciones en el motor de implementación de Kudu está disponible (por ejemplo, el control de versiones de implementación, deshacer, restauración de paquete, automatización).

Con de implementación de un servicio de control de código fuente en la nube es:

- Algunos conocimientos del servicio SCM respectivo necesario.

###<a name="vsts"></a>Cómo implementar continuamente de un servicio de control de código fuente en la nube
En el [Portal de Azure](https://portal.azure.com), puede configurar la implementación continua de GitHub, Bitbucket y Visual Studio Team Services.

* [Implementación de continua Azure de aplicación de servicio](app-service-continuous-deployment.md). 

## <a name="localgitdeployment"></a>Implementar desde Git local
Si su equipo de desarrollo usa un servicio de management (SCM) del código de origen local local según Git, puede configurar como origen de implementación al servicio de aplicación. 

Los profesionales de implementación de Git local son:

- Control de versiones para habilitar la desinstalación.
- Implementación de la rama específica, puede implementar diferentes ramas a diferentes [ranuras](web-sites-staged-publishing.md).
- Todas las funciones en el motor de implementación de Kudu está disponible (por ejemplo, el control de versiones de implementación, deshacer, restauración de paquete, automatización).

Con de implementación de Git local es:

- Algunos conocimientos del sistema SCM respectivo necesario.
- Sin soluciones llave para su implementación continuo. 

###<a name="vsts"></a>Cómo implementar desde Git local
En el [Portal de Azure](https://portal.azure.com), puede configurar la implementación local de Git.

* [Implementación local Git Azure de aplicación de servicio](app-service-deploy-local-git.md). 
* [Publicar en Web Apps desde cualquier repo git/hg](http://blog.davidebbo.com/2013/04/publishing-to-azure-web-sites-from-any.html).  

## <a name="deploy-using-an-ide"></a>Implementar mediante un IDE
Si ya está usando [Visual Studio](https://www.visualstudio.com/products/visual-studio-community-vs.aspx) con un [SDK Azure](https://azure.microsoft.com/downloads/)u otros conjuntos IDE como [Xcode](https://developer.apple.com/xcode/), [Eclipse](https://www.eclipse.org)y [IntelliJ IDEA](https://www.jetbrains.com/idea/), puede implementar en Azure directamente desde el IDE. Esta opción es ideal para un programador individual.

Visual Studio es compatible con todos los procesos de implementación de tres (FTP, Git e implementar Web), dependiendo de sus preferencias, mientras que otros IDE puede implementar aplicación de servicio si tienen integración FTP o Git (consulte [información general sobre los procesos de implementación](#overview)).

Los profesionales de implementación mediante un IDE son:

- Posiblemente minimizar las herramientas para el ciclo de vida de aplicación de llevar a cabo. Desarrollar, depurar, realizar un seguimiento e implemente la aplicación Azure todos sin mover fuera de su IDE. 

Las desventajas de implementación mediante un IDE son:

- Complejidad añadida en herramientas.
- Aún requiere un sistema de control de código fuente para un proyecto de equipo.

<a name="vspros"></a>Los profesionales de TI adicionales de implementación mediante Visual Studio con Azure SDK son:

- Azure SDK hace recursos Azure ciudadanos de primera clase en Visual Studio. Crear, eliminar, editar, iniciar y dejar de aplicaciones, la consulta de la base de datos SQL de back-end, live-depurar la aplicación de Azure y mucho más. 
- Vista de edición de archivos de código en Azure.
- Depuración en directo de aplicaciones en Azure.
- Explorador de Azure integrado.
- Implementación de sólo diferencias. 

###<a name="vs"></a>Cómo implementar directamente desde Visual Studio

* [Introducción a Azure y ASP.NET](web-sites-dotnet-get-started.md). Cómo crear e implementar un proyecto web de ASP.NET MVC sencillo mediante Visual Studio e implementación Web.
* [Cómo implementar Azure WebJobs mediante Visual Studio](websites-dotnet-deploy-webjobs.md). Cómo configurar los proyectos de aplicación de consola para que implemente como WebJobs.  
* [Implementar una aplicación de MVC de ASP.NET seguro 5 con pertenencia, OAuth y base de datos SQL para las aplicaciones Web](web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database.md). Cómo crear e implementar un proyecto web de ASP.NET MVC con una base de datos SQL mediante Visual Studio, Web implementar y entidad Framework código primera migraciones.
* [Implementación de Web de ASP.NET mediante Visual Studio](http://www.asp.net/mvc/tutorials/deployment/visual-studio-web-deployment/introduction). Una serie de tutoriales de 12 parte que cubre una gama más completa de las tareas de implementación que otros usuarios de esta lista. Se han agregado algunas características de implementación de Azure ya se ha escrito el tutorial, pero agregadas más adelante explican lo que falta.
* [Implementar un sitio Web de ASP.NET a Azure en Visual Studio 2012 desde un repositorio de Git directamente](http://www.dotnetcurry.com/ShowArticle.aspx?ID=881). Se explica cómo implementar un proyecto web ASP.NET en Visual Studio, mediante el complemento Git para confirmar el código Git y Azure conexión al repositorio Git. A partir de 2013 de Visual Studio, soporte Git está integrada y no requiere la instalación de un complemento.

###<a name="aztk"></a>Cómo implementar con los Toolkits de Azure para Eclipse y IntelliJ IDEA

Microsoft hace posible implementar aplicaciones Web de Azure directamente desde Eclipse y IntelliJ a través del [Kit de herramientas de Azure para Eclipse](../azure-toolkit-for-eclipse.md) y el [Kit de herramientas de Azure para IntelliJ](../azure-toolkit-for-intellij.md). Los siguientes tutoriales muestran los pasos que intervienen en la implementación simple un "" Hola Web App en Azure con IDE:

*  [Crear una aplicación de Web Hola mundo de Azure en Eclipse](./app-service-web-eclipse-create-hello-world-web-app.md). En este tutorial se muestra cómo usar el Kit de herramientas de Azure para Eclipse para crear e implementar un Hola mundo Web App para Azure.
*  [Crear una aplicación de Web Hola mundo de Azure en IntelliJ](./app-service-web-intellij-create-hello-world-web-app.md). En este tutorial se muestra cómo usar el Kit de herramientas de Azure para IntelliJ para crear e implementar un Hola mundo Web App para Azure.


## <a name="automate"></a>Automatizar la implementación mediante herramientas de línea de comandos

* [Automatizar la implementación con MSBuild](#msbuild)
* [Copiar archivos con secuencias de comandos y herramientas FTP](#ftp)
* [Automatizar la implementación con Windows PowerShell](#powershell)
* [Automatizar la implementación con la API de administración de .NET](#api)
* [Implementación de Azure de línea de comandos interfaz Azure](#cli)
* [Implementar desde Web implementar la línea de comandos](#webdeploy)
* [Usar Scripts por lotes de FTP](http://support.microsoft.com/kb/96269).
 
Otra opción de implementación es un servicio basado en la nube como [Pulpo implementar](http://en.wikipedia.org/wiki/Octopus_Deploy). Para obtener más información, vea [implementar ASP.NET aplicaciones a sitios Web de Azure](https://octopusdeploy.com/blog/deploy-aspnet-applications-to-azure-websites).

###<a name="msbuild"></a>Automatizar la implementación con MSBuild

Si utiliza el [IDE de Visual Studio](#vs) para el desarrollo, puede utilizar [MSBuild](http://msbuildbook.com/) para automatizar cualquier cosa que puede hacer en el IDE. Puede configurar MSBuild para usar [Implementar Web](#webdeploy) o [FTP y FTPS](#ftp) para copiar los archivos. Implementar Web también puede automatizar muchas otras relacionadas con la implementación de tareas, como la implementación de bases de datos.

Para obtener más información acerca de la implementación de línea de comandos con MSBuild, consulte los siguientes recursos:

* [Implementación de Web de ASP.NET mediante Visual Studio: implementación de la línea de comandos](http://www.asp.net/mvc/tutorials/deployment/visual-studio-web-deployment/command-line-deployment). Décima parte de una serie de tutoriales sobre implementación de Azure con Visual Studio. Muestra cómo usar la línea de comandos para implementar después de la configuración de perfiles de publicación en Visual Studio.
* [Dentro del motor de compilación de Microsoft: uso de MSBuild y Team Foundation generar](http://msbuildbook.com/). Libro de copia impresa que incluye capítulos sobre cómo usar MSBuild para su implementación.

###<a name="powershell"></a>Automatizar la implementación con Windows PowerShell

Puede realizar las funciones de implementación de MSBuild o FTP desde [Windows PowerShell](http://msdn.microsoft.com/library/dd835506.aspx). Si lo hace, también puede usar un conjunto de cmdlets de Windows PowerShell que facilitan la llamada de la API de administración del resto de Azure.

Para obtener más información, consulte los siguientes recursos:

* [Implementar una aplicación web vinculada a un repositorio de GitHub](app-service-web-arm-from-github-provision.md)
* [Aprovisionar una aplicación web con una base de datos de SQL](app-service-web-arm-with-sql-database-provision.md)
* [Aprovisionar e implementar microservices predecible en Azure](app-service-deploy-complex-application-predictably.md)
* [Automatizar la creación de aplicaciones de nube reales con Azure - todo](http://asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/automate-everything). Libro electrónico capítulo que explica cómo la aplicación de ejemplo que se muestra en el libro electrónico usa secuencias de comandos de Windows PowerShell para crear un entorno de prueba de Azure e implementar a él. Consulte la sección de [recursos](http://asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/automate-everything#resources) para obtener vínculos a documentación adicional de PowerShell de Azure.
* [Mediante secuencias de comandos de Windows PowerShell para publicar en entornos de prueba y desarrollo](../vs-azure-tools-publishing-using-powershell-scripts.md). Cómo usar Windows PowerShell implementación de secuencias de comandos que genera Visual Studio.

###<a name="api"></a>Automatizar la implementación con la API de administración de .NET

Puede escribir código C# para llevar a cabo funciones MSBuild o FTP para su implementación. Si lo hace, puede acceder a la administración de Azure API de REST para realizar las funciones de administración del sitio.

Para obtener más información, consulte los siguientes recursos:

* [Automatización de todo el contenido con las bibliotecas de administración de Azure y .NET](http://www.hanselman.com/blog/PennyPinchingInTheCloudAutomatingEverythingWithTheWindowsAzureManagementLibrariesAndNET.aspx). Introducción a las API de administración de .NET y vínculos a más documentación.

###<a name="cli"></a>Implementación de Azure de línea de comandos interfaz Azure

Puede usar la línea de comandos en Windows, Mac o Linux máquinas para implementar mediante FTP. Si lo hace, también puede acceder a la administración de Azure REST API mediante la CLI de Azure.

Para obtener más información, consulte los siguientes recursos:

* [Herramientas de línea de comandos de azure](/downloads/#cmd-line-tools). Página del portal en Azure.com para obtener más información de la herramienta de línea de comandos.

###<a name="webdeploy"></a>Implementar desde Web implementar la línea de comandos

[Implementar Web](http://www.iis.net/downloads/microsoft/web-deploy) es un software de Microsoft para su implementación en IIS que no solo proporciona características de sincronización inteligente de archivos, pero también puede realizar o coordinar muchas otras tareas relacionadas con la implementación que no se pueden automatizar cuando utilice FTP. Por ejemplo, implementar Web puede implementar una nueva base de datos o actualizaciones de la base de datos junto con la aplicación web. Implementar Web también puede minimizar el tiempo necesario para actualizar un sitio existente desde inteligente pueden copiar únicamente los archivos modificados. Microsoft Visual Studio y Team Foundation Server tienen compatibilidad para Web implementar integrado, pero también puede usar implementar Web directamente desde la línea de comandos para automatizar la implementación. Comandos de implementar Web son muy eficaces pero el aprendizaje puede ser pronunciado.

Para obtener más información, consulte los siguientes recursos:

* [Aplicaciones Web simple: implementación](https://azure.microsoft.com/blog/2014/07/28/simple-azure-websites-deployment/). Blog de David Ebbo acerca de una herramienta que escribió para que sea más fácil de usar Web implementar.
* [Herramienta de implementación web](http://technet.microsoft.com/library/dd568996). Documentación oficial en el sitio de Microsoft TechNet. Fecha pero aún un buen lugar para empezar.
* [Usar Web implementar](http://www.iis.net/learn/publish/using-web-deploy). Documentación oficial en el sitio de Microsoft IIS.NET. También fechada pero un buen lugar para empezar.
* [Implementación de Web de ASP.NET mediante Visual Studio: implementación de la línea de comandos](http://www.asp.net/mvc/tutorials/deployment/visual-studio-web-deployment/command-line-deployment). MSBuild es el motor de compilación utilizado por Visual Studio, y también se pueden utilizar desde la línea de comandos para implementar aplicaciones web para las aplicaciones Web. Este tutorial es parte de una serie que trata principalmente sobre la implementación de Visual Studio.

##<a name="nextsteps"></a>Pasos siguientes

En algunos escenarios que podría querer puedan cambiar fácilmente y hacia atrás entre un ensayo y una versión de producción de la aplicación. Para obtener más información, vea [Implementación por fases en aplicaciones Web](web-sites-staged-publishing.md).

Disponer de un plan de copia de seguridad y restauración de es una parte importante de los flujos de trabajo de implementación. Para obtener información sobre el servicio de la aplicación de copia de seguridad y restaurar la característica, consulte [Copias de seguridad de aplicaciones Web](web-sites-backup.md).  

Para obtener información acerca de cómo usar el Control de acceso basado en roles de Azure para administrar el acceso a la implementación de servicios de aplicación, vea [RBAC y publicación de Web App](https://azure.microsoft.com/blog/2015/01/05/rbac-and-azure-websites-publishing/).


 
