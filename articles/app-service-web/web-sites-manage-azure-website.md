<properties 
    pageTitle="Administrar una aplicación web en la aplicación de servicio de Azure" 
    description="Vínculos a recursos de administración de una aplicación web de servicio de aplicaciones de Azure." 
    services="app-service\web" 
    documentationCenter="" 
    authors="erikre" 
    manager="wpickett" 
    editor=""/>

<tags 
    ms.service="app-service-web" 
    ms.workload="web" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/24/2016" 
    ms.author="rachelap"/>

# <a name="manage-a-web-app-in-azure-app-service"></a>Administrar una aplicación web en la aplicación de servicio de Azure

Este tema contiene vínculos a recursos de administración de una aplicación web de [Servicio de aplicaciones de Azure](http://go.microsoft.com/fwlink/?LinkId=529714). Administración incluye todas las tareas que tenga la aplicación web de funcionamiento. 

La duración de una aplicación web, debe realizar distintas tareas de administración, como mover de implementación inicial actualizaciones, mantenimiento y funcionamiento normal.

Pueden realizar muchas tareas de administración de la aplicación web en el Portal de Azure.

## <a name="before-you-deploy-your-web-app-to-production"></a>Antes de implementar la aplicación web de producción

### <a name="choose-a-tier"></a>Elija un nivel

Servicio de aplicación de Azure se ofrece en cinco niveles: libre, compartido, Basic, estándar y Premium. Para obtener información sobre las características y precios para cada nivel, consulte [Detalles de precios](/pricing/details/app-service/). 

- [Planes de servicio de la aplicación](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md) le permiten agrupar varias aplicaciones web en el mismo nivel.
- Siempre puede [cambiar niveles](web-sites-scale.md) después de crear la aplicación web.

### <a name="configuration"></a>Configuración

Utilice el [Portal de Azure](https://portal.azure.com/) para establecer diversas opciones de configuración. Para obtener información detallada, vea [Configurar web apps en el servicio de aplicación de Azure](web-sites-configure.md). Esta es una lista de comprobación rápida:

- Seleccione **las versiones del runtime** para .NET, PHP, Java o Python, si es necesario.
- Habilitar **WebSockets** si la aplicación web usa el protocolo WebSocket. (Esto incluye aplicaciones que utilicen [ASP.NET SignalR](http://www.asp.net/signalr) o [socket.io](web-sites-nodejs-chat-app-socketio.md)).
- ¿Está ejecutando trabajos continuo web? Si es así, habilitar **Siempre en**.
- Configurar el **documento predeterminado**, como index.html.

Además de estas opciones de configuración básica, desea configurar lo siguiente:

- Cifrado **Secure Socket Layer (SSL)** . Para usar SSL con un nombre de dominio personalizado, debe obtener un certificado SSL y configurar la aplicación web para usarlo. Vea [Habilitar HTTPS para una aplicación web de servicio de aplicaciones de Azure](web-sites-configure-ssl-certificate.md).
- **Nombre de dominio personalizado.** La aplicación web tiene automáticamente un subdominio bajo azurewebsites.net. Puede asociar un nombre de dominio personalizado, como por ejemplo contoso.com. Consulte [configurar un nombre de dominio personalizado en la aplicación de servicio de Azure](web-sites-custom-domain-name.md).

Configuración de idioma específico:

- **PHP**: [configurar PHP en las aplicaciones de Azure de aplicación de servicio Web](web-sites-php-configure.md).
- **Python**: [Configurar Python con aplicaciones de Azure de aplicación de servicio Web](web-sites-python-configure.md)


## <a name="while-your-web-app-is-running"></a>Mientras se ejecuta la aplicación web

Mientras se ejecuta la aplicación web, desea asegurarse de que está disponible y que escala para cumplir el tráfico de usuario. También debe solucionar errores.

### <a name="monitoring"></a>Supervisión

- A través del Portal de Azure, puede [Agregar indicadores de rendimiento](web-sites-monitor.md) como el uso de CPU y el número de solicitudes de cliente.
- [Escala de la aplicación web](web-sites-scale.md) como respuesta al tráfico. Dependiendo de su nivel, puede escalar el número de máquinas virtuales o el tamaño de las instancias de máquina virtual. En las capas estándar y Premium, también puede configurar el ajuste automático, para que la aplicación web de escala automáticamente, según una programación fija o en respuesta a cargar.  
 
### <a name="backups"></a>Realizar copias de seguridad

- Configurar [copias de seguridad automáticas](web-sites-backup.md) de la aplicación web. Más información sobre las copias de seguridad en [este vídeo](https://azure.microsoft.com/documentation/videos/azure-websites-automatic-and-easy-backup/).
- Obtenga información sobre las opciones de [recuperación de base de datos](../sql-database/sql-database-business-continuity.md) en la base de datos de SQL Azure.

### <a name="troubleshooting"></a>Solución de problemas

- Si algo va mal, puede [solucionar problemas en Visual Studio](web-sites-dotnet-troubleshoot-visual-studio.md#remotedebug), con los registros de diagnósticos y depuración en directo en la nube. 
- Fuera de Visual Studio, hay varias formas para recopilar registros de diagnóstico. Vea [Habilitar registro de diagnósticos para aplicaciones web de servicio de aplicaciones de Azure](web-sites-enable-diagnostic-log.md).
- Para las aplicaciones de Node.js, consulte [cómo depurar una aplicación web de Node.js en la aplicación de servicio de Azure](web-sites-nodejs-debug.md).

### <a name="restoring-data"></a>Restauración de datos

- [Restaurar](web-sites-restore.md) una aplicación web que se copiaron previamente.


## <a name="when-you-update-your-web-app"></a>Al actualizar la aplicación web

Si no ha habilitado copias de seguridad automáticas, puede crear una [copia de seguridad manual](web-sites-backup.md).

Considere la posibilidad de [distribución por fases](web-sites-staged-publishing.md). Esta opción permite publicar actualizaciones en una implementación de ensayo que se ejecuta en paralelo con la implementación de producción. 

Si usa Visual Studio Team Services, puede configurar la implementación continua de control de código fuente:

- [Usar el Control de versiones de Team Foundation (TFVC)](../cloud-services/cloud-services-continuous-delivery-use-vso.md) 
- [Usar Git](../cloud-services/cloud-services-continuous-delivery-use-vso-git.md)
 
<!-- Anchors. -->

[Before you deploy your site to production]: #before-you-deploy-your-site-to-production
[While your website is running]: #while-your-website-is-running
[When you update your website]: #when-you-update-your-website

  
