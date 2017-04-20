<properties 
    pageTitle="Agregar una aplicación de Java para las aplicaciones de Azure aplicación de servicio Web" 
    description="En este tutorial se muestra cómo agregar una página o la aplicación a su instancia de Azure aplicación de servicio Web Apps ya está configurado para utilizar Java." 
    services="app-service\web" 
    documentationCenter="java" 
    authors="rmcmurray" 
    manager="wpickett" 
    editor=""/>

<tags 
    ms.service="app-service-web" 
    ms.workload="web" 
    ms.tgt_pltfrm="na" 
    ms.devlang="Java" 
    ms.topic="article" 
    ms.date="08/11/2016" 
    ms.author="robmcm"/>

# <a name="add-a-java-application-to-azure-app-service-web-apps"></a>Agregar una aplicación de Java para las aplicaciones de Azure aplicación de servicio Web

Una vez haya inicializado la aplicación web de Java en la [Aplicación de servicio de Azure][] tal como se describe en [crear una aplicación web de Java en la aplicación de servicio de Azure](web-sites-java-get-started.md), puede cargar la aplicación colocando el guerra en la carpeta **aplicaciones Web** .

La ruta de navegación a la carpeta **aplicaciones Web** varía en función de cómo configurar la instancia de aplicaciones Web.

- Si configurar la aplicación web mediante Azure Marketplace, es la ruta de acceso a la carpeta **aplicaciones Web** en el formulario **d:\home\site\wwwroot\bin\application\_server\webapps**, donde **aplicación\_server** es el nombre del servidor de aplicaciones en vigor para la instancia de aplicaciones Web. 
- Si configurar la aplicación web mediante la interfaz de usuario de la configuración de Azure, la ruta de acceso a la carpeta **aplicaciones Web** se encuentra en el formulario **d:\home\site\wwwroot\webapps**. 

Tenga en cuenta que puede usar el control de código fuente para cargar la aplicación o páginas web, incluidos [los escenarios de integración continua](app-service-continuous-deployment.md). FTP también es una opción para cargar la aplicación o páginas web.

Nota para las aplicaciones web de Tomcat: una vez que haya cargado el archivo guerra en la carpeta **aplicaciones Web** , el servidor de aplicaciones de Tomcat detecta que ha agregado y lo cargará automáticamente. Tenga en cuenta que si copia archivos (que no sean guerra) en el directorio raíz, el servidor de aplicaciones tendrán que reiniciar antes de que se usan los archivos. La funcionalidad de cargar automáticamente para las aplicaciones web de Java Tomcat ejecuta en Azure se basa en un nuevo archivo de guerra se agrega, o nuevos archivos o directorios agregados a la carpeta de **aplicaciones Web** . 

## <a name="next-steps"></a>Pasos siguientes

Para obtener más información, consulte el [Centro para desarrolladores de Java](/develop/java/).

[AZURE.INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]

[AZURE.INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]

<!-- External Links -->
[Servicio de aplicaciones de Azure]: http://go.microsoft.com/fwlink/?LinkId=529714
