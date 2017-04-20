<properties 
    pageTitle="Recursos de documentación WebJobs Azure" 
    description="Recursos recomendados para aprender a utilizar el SDK de WebJobs de Azure y Azure WebJobs." 
    services="app-service" 
    documentationCenter=".net" 
    authors="tdykstra" 
    manager="wpickett" 
    editor="jimbe"/>

<tags 
    ms.service="app-service" 
    ms.workload="na" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/28/2016" 
    ms.author="tdykstra"/>

# <a name="azure-webjobs-documentation-resources"></a>Recursos de documentación WebJobs Azure

## <a name="overview"></a>Información general

Este tema establece vínculos a recursos de documentación sobre cómo usar el SDK de WebJobs de Azure y Azure WebJobs. WebJobs Azure proporcionan una forma sencilla de ejecutar secuencias de comandos o programas como procesos en segundo plano en el contexto de una [aplicación de servicio de aplicación web, API de aplicación o una aplicación móvil](../app-service/app-service-value-prop-what-is.md). Puede cargar y ejecutar un archivo ejecutable como como cmd, bat exe (. NET), ps1, sh, php, copiar, js y jar. Estos programas ejecutan como WebJobs en una programación (cron) o de forma continua.

El propósito del [SDK WebJobs](websites-webjobs-resources.md) es simplificar el código que escriba para las tareas comunes que un WebJob puede llevar a cabo, como el procesamiento de imagen, la cola de procesamiento, RSS agregación, mantenimiento del archivo y enviar correos electrónicos. El SDK WebJobs tiene características integradas para trabajar con el almacenamiento de Azure y Bus de servicio, para programar tareas y tratamiento de errores y muchos otros escenarios comunes. Además, se ha diseñado para ser extensible y hay un [repositorio de origen para las extensiones de abrir](https://github.com/Azure/azure-webjobs-sdk-extensions/wiki/Binding-Extensions-Overview). [Funciones de Azure](../azure-functions/functions-overview.md) (actualmente en vista previa) se basa en una versión de SDK WebJobs que funciona con C# script, Node.js y otros idiomas. 

Crear, implementar y administrar WebJobs están perfecto con las herramientas integradas en Visual Studio. Puede crear WebJobs a partir de plantillas, publicar y administrar (ejecutar, detener, monitor/depurar) ellos. 

El panel WebJobs en el portal de Azure proporciona poderosas capacidades de administración que proporcionan control total sobre la ejecución de WebJobs, incluida la capacidad de invocación de funciones individuales en WebJobs. El panel también muestra el tiempo de ejecución de función y los resultados del registro. 

##<a name="getstarted"></a>Introducción a WebJobs y el SDK WebJobs

* [Introducción a Azure WebJobs](http://www.hanselman.com/blog/IntroducingWindowsAzureWebJobs.aspx)
* [Azure WebJobs son impresionantes y deben comenzar a usarlos ahora mismo!](http://www.troyhunt.com/2015/01/azure-webjobs-are-awesome-and-you.html) (Entrada de Blog de Troya captura).
* [Características de Azure WebJobs](/blog/2014/10/22/webjobs-goes-into-full-production/)
* [¿Qué es el SDK WebJobs](websites-dotnet-webjobs-sdk.md)
* [Guía de trabajos de fondo en modelos de Microsoft y prácticas](/documentation/articles/best-practices-background-jobs/)
* [Anuncio de la 1.1.0 RTM de Microsoft Azure WebJobs SDK](/blog/azure-webjobs-sdk-1-1-0-rtm/)
* [Introducción a Azure SDK WebJobs](websites-dotnet-webjobs-sdk-get-started.md)
* [Cómo usar el almacenamiento de Azure cola con el SDK WebJobs](websites-dotnet-webjobs-sdk-storage-queues-how-to.md)
* [Cómo usar el almacenamiento de blobs de Windows Azure con el SDK WebJobs](websites-dotnet-webjobs-sdk-storage-blobs-how-to.md)
* [Cómo usar el almacenamiento de tablas Azure con el SDK WebJobs](websites-dotnet-webjobs-sdk-storage-tables-how-to.md)
* [Cómo utilizar Bus de servicio de Azure con el SDK WebJobs](websites-dotnet-webjobs-sdk-service-bus.md)
* [Cómo usar WebHooks con el SDK de WebJobs, con ejemplos de GitHub, IFTTT y HTTP](https://github.com/Azure/azure-webjobs-sdk-extensions/wiki/WebHooks-Walkthrough)
* [Azure WebJobs referencia SDK que rápido (descargar PDF)](http://go.microsoft.com/fwlink/?LinkID=524028&clcid=0x409)
* [Documentación de la configuración de WebJobs en GitHub](https://github.com/projectkudu/kudu/wiki/Web-jobs).
* Vídeos
    * [WebJobs y el SDK de WebJobs](http://channel9.msdn.com/Shows/Cloud+Cover/Episode-153-WebJobs-with-Pranav-Rastogi?utm_source=dlvr.it&utm_medium=twitter)
    * [Serie de vídeos de WebJobs Azure en canal 9](http://channel9.msdn.com/Tags/azurefridaywebjobs)
    * [Introducción a WebJobs herramientas para Visual Studio](http://channel9.msdn.com/Shows/Web+Camps+TV/Introducing-WebJobs-Tooling-for-Visual-Studio-with-Brady-Gaster) 
    * [Herramientas de WebJobs y depuración remota](http://channel9.msdn.com/Shows/Web+Camps+TV/WebJobs-GA-Series-Episode-1-WebJobs-Tooling-with-Brady-Gaster)
    * [Actualización de Azure WebJobs con Pranav Rastogi - capacidad de ampliación de la versión 1.1](https://channel9.msdn.com/Shows/Cloud+Cover/Episode-183-Azure-WebJobs-Update-with-Pranav-Rastogi)

Consulte también las siguientes secciones en [WebJobs implementar](#deploy) y [probar y depurar WebJobs](#debug).

##<a name="deploy"></a>Implementar WebJobs

* [Cómo implementar Azure WebJobs mediante Visual Studio](websites-dotnet-deploy-webjobs.md)
* [Cómo implementar WebJobs con el Portal de Azure](web-sites-create-web-jobs.md)
* [Permitir la entrega de línea de comandos o continua de Azure WebJobs](https://azure.microsoft.com/blog/2014/08/18/enabling-command-line-or-continuous-delivery-of-azure-webjobs/)
* [GIT implementar una aplicación de consola de .NET para Azure con WebJobs](http://blog.amitapple.com/post/73574681678/git-deploy-console-app/)
* [Implementar un WebJob F # en Azure](http://blogs.msdn.com/b/dave_crooks_dev_blog/archive/2015/02/18/deploying-f-web-job-to-azure.aspx)
* [Implementar servicios personalizados como Webjobs de Azure](http://withouttheloop.com/articles/2015-06-23-deploying-custom-services-as-azure-webjobs/)
* Vídeos
    * [Introducción a WebJobs herramientas para Visual Studio](http://channel9.msdn.com/Shows/Web+Camps+TV/Introducing-WebJobs-Tooling-for-Visual-Studio-with-Brady-Gaster) 
    * [Herramientas de WebJobs y depuración remota](http://channel9.msdn.com/Shows/Web+Camps+TV/WebJobs-GA-Series-Episode-1-WebJobs-Tooling-with-Brady-Gaster) 

##<a name="schedule"></a>Programación WebJobs

* [El cuadro de diálogo de Azure WebJob agregar](websites-dotnet-deploy-webjobs.md#configure)
* [Crear un WebJob programada en el Portal de Azure](web-sites-create-web-jobs.md#CreateScheduled)
* [Enlazar una tarea del programador para un WebJob](http://blog.davidebbo.com/2015/05/scheduled-webjob.html)
* [Programación de Azure WebJobs con expresiones cron](http://blog.amitapple.com/post/2015/06/scheduling-azure-webjobs/)
* [Funciones de WebJob individuales con el TimerTrigger SDK WebJobs de programación](websites-dotnet-webjobs-sdk.md#schedule)

##<a name="debug"></a>Probar y depurar WebJobs

* [Nuevo programador y las características de depuración de Azure WebJobs en Visual Studio](http://blogs.msdn.com/b/webdev/archive/2014/11/12/new-developer-and-debugging-features-for-azure-webjobs-in-visual-studio.aspx)
* [Ver el panel de WebJobs](websites-dotnet-webjobs-sdk-get-started.md#view-the-webjobs-sdk-dashboard)
* [Cómo escribir registros mediante el SDK WebJobs y verlos en el panel](websites-dotnet-webjobs-sdk-storage-queues-how-to.md#logs)
* [WebJobs depuración remota](web-sites-dotnet-troubleshoot-visual-studio.md#remotedebugwj)
* [¿Quién escribió blob?](http://blogs.msdn.com/b/jmstall/archive/2014/02/19/who-wrote-that-blob.aspx) 
* [Hospedaje código interactiva en la nube](http://blogs.msdn.com/b/jmstall/archive/2014/04/26/hosting-interactive-code-in-the-cloud.aspx)
* [Adición de seguimiento a Azure WebJobs](http://blogs.msdn.com/b/mcsuksoldev/archive/2014/09/04/adding-trace-to-azure-web-sites-and-web-jobs.aspx)
* [Supervisar, diagnosticar y solucionar problemas de Microsoft Azure almacenamiento](../storage/storage-monitoring-diagnosing-troubleshooting.md)
* Vídeos
    * [Herramientas de WebJobs y depuración remota](http://channel9.msdn.com/Shows/Web+Camps+TV/WebJobs-GA-Series-Episode-1-WebJobs-Tooling-with-Brady-Gaster) 

##<a name="scale"></a>Escala WebJobs

* [Ajuste de escala de la aplicación Web con sitios Web de Azure](http://msdn.microsoft.com/magazine/dn786914.aspx)
* [Azure de aplicación de servicio: arquitectura de aplicaciones Web de escala masiva listo para la empresa](https://channel9.msdn.com/Events/Build/2014/3-626). Portadas de escala de aplicaciones web con WebJobs, incluido el SDK WebJobs.
* Vídeos
    * [Escalado WebJobs](http://channel9.msdn.com/Shows/Azure-Friday/Azure-WebJobs-105-Scaling-out-Web-Jobs)

##<a name="additional"></a>Recursos adicionales de WebJobs

* [Entrada de blog de Azure GA WebJobs por Magnus Mårtensson](http://magnusmartensson.com/azure-webjobs-ga)
* [Ejecución de trabajos de Powershell Web en Azure de aplicación de servicio](http://blogs.msdn.com/b/nicktrog/archive/2014/01/22/running-powershell-web-jobs-on-azure-websites.aspx)
* [Recibir notificaciones cuando su Azure Active WebJobs completa](http://blog.amitapple.com/post/2014/03/webjobs-notification/)
* [Directiva de retención de copia de seguridad de aplicación Web simple con WebJobs](https://azure.microsoft.com/blog/2014/04/28/simple-web-site-backup-retention-policy-with-webjobs/)
* [Aplicaciones Web de azure y servicios de nube lenta en la primera solicitud](http://wp.sjkp.dk/windows-azure-websites-and-cloud-services-slow-on-first-request/). Muestra cómo usar WebJobs para simular la característica de AlwaysOn solo está disponible para el nivel de precio estándar.
* [WebJobs se está saliendo](http://blog.amitapple.com/post/2014/05/webjobs-graceful-shutdown/#.U72Il_5OWUl). Para el SDK de WebJobs se está saliendo, vea [está saliendo](websites-dotnet-webjobs-sdk-storage-queues-how-to.md#graceful).)
* [Automatización de las copias de seguridad con Azure WebJobs & AzCopy](http://markjbrown.com/azure-webjobs-azcopy/)
* Vídeos
    * [Vídeos de Azure WebJobs por Magnus Mårtensson](https://www.youtube.com/playlist?list=PLqp1ZOYYUSd81yEzMYLTw8cz91wx_LU9r)
    * [Serie de vídeos de WebJobs Azure en canal 9](http://channel9.msdn.com/Tags/azurefridaywebjobs)

##<a name="additionalsdk"></a>Recursos adicionales de SDK WebJobs

* [Notas de la versión de WebJobs SDK](https://github.com/Azure/azure-webjobs-sdk/wiki/Release-Notes)
* [Código fuente de WebJobs SDK](https://github.com/Azure/azure-webjobs-sdk)
* [Extensiones de SDK WebJobs el código fuente](https://github.com/Azure/azure-webjobs-sdk-extensions), con [una guía detallada al modelo de capacidad de ampliación](https://github.com/Azure/azure-webjobs-sdk-extensions/wiki/Binding-Extensions-Overview).  
* [Código fuente de secuencias de comandos de WebJobs SDK](https://github.com/Azure/azure-webjobs-sdk-script/) (se usa para las [Funciones de Azure](../azure-functions/functions-overview.md))
* [WebJob cargar archivos FREB al almacenamiento de Azure usando el SDK WebJobs](http://thenextdoorgeek.com/post/WAWS-WebJob-to-upload-FREB-files-to-Azure-Storage-using-the-WebJobs-SDK)
* [Hospedaje Azure webjobs fuera de Azure, con las ventajas de registro de una Azure hospedado webjob](http://bypassion.dk/?p=510)
* [Creación de una herramienta de importación de datos con WebJobs de Azure](http://www.freshconsulting.com/building-data-import-tool-azure-webjobs/)
* [Información general de las funciones de Azure](../azure-functions/functions-overview.md)
* Vídeos
    * [Serie de vídeos de WebJobs Azure en canal 9](http://channel9.msdn.com/Tags/azurefridaywebjobs)

##<a name="samples"></a>Aplicaciones de WebJob de ejemplo

* [Aplicaciones de ejemplo proporcionadas por el equipo de WebJobs en GitHub](https://github.com/azure/azure-webjobs-sdk-samples)
* [Simple Azure Web App con WebJobs Backend mediante el SDK WebJobs](http://code.msdn.microsoft.com/Simple-Azure-Website-with-b4391eeb)
* [SiteMonitR](http://code.msdn.microsoft.com/SiteMonitR-dd4fcf77). Muestra el uso de WebJobs condicionados por eventos y programadas. Consulte el blog [reconstruir la SiteMonitR con Azure WebJobs SDK](http://www.bradygaster.com/post/rebuilding-the-sitemonitr-using-windows-azure-webjobs).

##<a name="blogs"></a>Blogs

* [Blog de Azure](/blog)
* [Blog de Amit Apple](http://blog.amitapple.com/). Se centra en WebJobs (no en el SDK).
* [Blog de Magnus Mårtensson](http://magnusmartensson.com/)

##<a name="gethelp"></a>Obtener ayuda con WebJobs

* [StackOverflow para WebJobs](http://stackoverflow.com/questions/tagged/azure-webjobs)
* [StackOverflow para el SDK WebJobs](http://stackoverflow.com/questions/tagged/azure-webjobssdk)
* [StackOverflow para las funciones de Azure](http://stackoverflow.com/questions/tagged/azure-functions)
* [Foro de Azure y ASP.NET](http://forums.asp.net/1247.aspx)
* [Foro de la aplicación de servicio Web Apps de Azure](http://social.msdn.microsoft.com/Forums/azure/home?forum=windowsazurewebsitespreview)
* [Sitio de voz de usuario de aplicaciones Web de Azure](https://feedback.azure.com/forums/169385-websites/)
* [Twitter](http://twitter.com/). Utilice la hashtag #AzureWebJobs.
* [Notificar un error WebJobs o problema](https://github.com/projectkudu/kudu/wiki/Reporting-WebJobs-issues)

