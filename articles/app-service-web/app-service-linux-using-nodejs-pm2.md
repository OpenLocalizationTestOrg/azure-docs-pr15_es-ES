<properties 
    pageTitle="Con la configuración de PM2 para NodeJS en las aplicaciones Web en Linux | Microsoft Azure" 
    description="Usar configuración de PM2 para NodeJS en las aplicaciones Web en Linux" 
    keywords="servicio de aplicaciones de Azure, aplicación web, nodejs, pm2, linux, sistemas operativos"
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

# <a name="using-pm2-configuration-for-nodejs-in-web-apps-on-linux"></a>Usar configuración de PM2 para Node.js en las aplicaciones Web en Linux

Si establece la pila de aplicación a Node.js para las aplicaciones Web en Linux, se proporciona la opción de establecer un archivo de inicio de Node.js tal como se muestra en la imagen siguiente.

![][1]

Puede usar esta opción:

-   Especificar la secuencia de comandos de inicio de la aplicación Node.js (por ejemplo: /bin/server.js)
-   Especificar el archivo de configuración de PM2 para su aplicación Node.js (por ejemplo: /foo/process.json)

 >[AZURE.NOTE] Si desea que los procesos de nodo reiniciar automáticamente cuando se modifican determinados archivos, debe usar la configuración de PM2. En caso contrario, la aplicación no se reinicie cuando recibe las notificaciones de cambio de cosas como implementación continua cuando cambie el código de la aplicación.

Puede comprobar la [documentación de archivo de proceso](http://pm2.keymetrics.io/docs/usage/application-declaration/) de Node.js para todas las opciones, pero a continuación se muestra una muestra de usaría como el archivo process.json

        {
          "name"        : "worker",
          "script"      : "/bin/server.js",
          "instances"   : 1,
          "merge_logs"  : true,
          "log_date_format" : "YYYY-MM-DD HH:mm Z",
          "watch": ["/bin/server.js", "foo.txt"],
          "watch_options": {
            "followSymlinks": true,
            "usePolling"   : true,
            "interval"    : 5
          }
        }

Son las cosas importantes a tener en cuenta en esta configuración 

-   La propiedad "script" especifica la secuencia de comandos de inicio de la aplicación.
-   La propiedad "instancias" especifica cuántas instancias del proceso de nodo Iniciar. Si está ejecutando la aplicación en tamaños VM mayores que tengan varios núcleos, desea maximizar los recursos, establezca un valor más alto aquí.
-   La matriz "inspección" Especifica todos los archivos cuyos cambios desea reiniciar los procesos de nodo.
-   Para "watch_options", actualmente debe especificar "usePolling" como verdadero debido a la forma que se montaje el contenido de la aplicación.


## <a name="next-steps"></a>Pasos siguientes ##

* [¿Qué es la aplicación de servicio en Linux?](./app-service-linux-intro.md)

<!--Image references-->
[1]: ./media/app-service-linux-using-nodejs-pm2/nodejs-startup-file.png