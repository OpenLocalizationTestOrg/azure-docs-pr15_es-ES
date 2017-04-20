<properties 
    pageTitle="Cómo crear una aplicación Web con la aplicación de servicio en Linux | Microsoft Azure" 
    description="Web app creación flujo de trabajo para la aplicación de servicio en Linux." 
    keywords="servicio de aplicaciones de Azure, aplicación web, linux, sistemas operativos"
    services="app-service" 
    documentationCenter="" 
    authors="naziml" 
    manager="wpickett" 
    editor=""/>

<tags 
    ms.service="app-service" 
    ms.workload="na" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/10/2016" 
    ms.author="naziml"/>

# <a name="create-a-web-app-with-app-service-on-linux"></a>Crear una aplicación Web con la aplicación de servicio en Linux

## <a name="using-the-management-portal-to-create-your-web-app"></a>Con el Portal de administración para crear la aplicación web
Puede comenzar a crear la aplicación Web en Linux desde el [portal de administración](https://portal.azure.com) , como se muestra en la imagen siguiente.

![][1]

Una vez que selecciona la opción siguiente, se mostrarán el módulo de crear como se muestra en la imagen siguiente. 

![][2]

-   Asigne un nombre a la aplicación web.
-   Elija un grupo de recursos existente o cree uno nuevo. (Consulte regiones disponibles en la [sección limitaciones](./app-service-linux-intro.md)).
-   Elija un plan de servicio de aplicación existente o crear un nuevo uno (vea aplicación servicio plan las notas en la [sección limitaciones](./app-service-linux-intro.md)). 
-   Elija la pila de aplicación que desea utilizar. Tendrá que elegir entre varias versiones de Node.js y PHP. 

Una vez que la aplicación que se creó, puede cambiar la pila de aplicación de la configuración de la aplicación como se muestra en la imagen siguiente.

![][3]

## <a name="deploying-your-web-app"></a>Implementación de la aplicación web

Elija "opciones de implementación" en el portal de administración le ofrece la opción Usar local un repositorio Git o un repositorio de GitHub para implementar la aplicación. Las instrucciones son posteriormente de forma similar a una aplicación web de no Linux y puede seguir las instrucciones de nuestra [implementación local de Git](./app-service-deploy-local-git.md) o nuestro artículo de [implementación continua](./app-service-continuous-deployment.md) para GitHub.

También puede usar FTP para cargar la aplicación a su sitio. Puede obtener el extremo FTP para la aplicación web de la sección de registros de diagnóstico, como se muestra en la imagen siguiente.

![][4]


## <a name="next-steps"></a>Pasos siguientes ##

* [¿Qué es la aplicación de servicio en Linux?](./app-service-linux-intro.md)
* [Usar configuración de PM2 para Node.js en las aplicaciones Web en Linux](./app-service-linux-using-nodejs-pm2.md)

<!--Image references-->
[1]: ./media/app-service-linux-how-to-create-a-web-app/top-level-create.png
[2]: ./media/app-service-linux-how-to-create-a-web-app/create-blade.png
[3]: ./media/app-service-linux-how-to-create-a-web-app/application-settings-change-stack.png
[4]: ./media/app-service-linux-how-to-create-a-web-app/diagnostic-logs-ftp.png
