<properties
    pageTitle="Introducción a aplicaciones web de Node.js en la aplicación de servicio de Azure"
    description="Obtenga información sobre cómo implementar una aplicación de Node.js en una aplicación web de servicio de aplicaciones de Azure."
    services="app-service\web"
    documentationCenter="nodejs"
    authors="cephalin"
    manager="wpickett"
    editor=""/>

<tags
    ms.service="app-service-web"
    ms.workload="web"
    ms.tgt_pltfrm="na"
    ms.devlang="nodejs"
    ms.topic="get-started-article"
    ms.date="07/01/2016"
    ms.author="cephalin"/>

# <a name="get-started-with-nodejs-web-apps-in-azure-app-service"></a>Introducción a aplicaciones web de Node.js en la aplicación de servicio de Azure

[AZURE.INCLUDE [tabs](../../includes/app-service-web-get-started-nav-tabs.md)]

Este tutorial muestra cómo crear una aplicación de [Node.js] simple e implementar al [Servicio de la aplicación de Azure] desde un entorno de línea de comandos, como cmd.exe o bash. Pueden seguir las instrucciones de este tutorial en ningún sistema operativo que se pueden ejecutar Node.js.

>[AZURE.INCLUDE [app-service-linux](../../includes/app-service-linux.md)] 

<a name="prereq"></a>
## <a name="prerequisites"></a>Requisitos previos

- [Node.js]
- [Bower]
- [Yeoman]
- [GIT]
- [CLI de Azure]
- Una cuenta de Microsoft Azure. Si no tiene una cuenta, puede [registrarse para una prueba gratuita] o [activar las ventajas de suscriptor de Visual Studio].

## <a name="create-and-deploy-a-simple-nodejs-web-app"></a>Crear e implementar una aplicación web de Node.js simple

1. Abra el terminal de línea de comandos de su elección e instale el [Generador de Yeoman de Express].

        npm install -g generator-express

2. `CD`en un directorio de trabajo y generar una aplicación express con la siguiente sintaxis:

        yo express
        
    Elegir las opciones siguientes cuando se le solicite:  

    `? Would you like to create a new directory for your project?`**Sí**  
    `? Enter directory name`**{nombre de aplicación}**  
    `? Select a version to install:`**MVC**  
    `? Select a view engine to use:`**Jade**  
    `? Select a css preprocessor to use (Sass Requires Ruby):`**Ninguno**  
    `? Select a database to use:`**Ninguno**  
    `? Select a build tool to use:`**Simplifique**

3. `CD`en el directorio de raíz de la nueva aplicación y se inicie para asegurarse de que se ejecute en su entorno de desarrollo:

        npm start

    En el explorador, vaya a <http://localhost:3000> para asegurarse de que puede ver la página de inicio de Express. Una vez que haya comprobado se ejecuta la aplicación correctamente, utilice `Ctrl-C` para detenerlo.
    
1. Cambiar a modo ASM e inicie sesión en Azure (debe [Azure CLI](#prereq)):

        azure config mode asm
        azure login

    Siga el símbolo del sistema para continuar el inicio de sesión en un explorador con una cuenta de Microsoft que tiene la suscripción de Azure.

2. Asegúrese de que sigue en el directorio de raíz de la aplicación y luego crear el recurso de aplicación de servicio de aplicación en Azure con un nombre de aplicación única con el comando siguiente. Por ejemplo: http://{appname}.azurewebsites.net

        azure site create --git {appname}

    Siga el indicador para seleccionar un área de Azure para implementar. Si nunca ha configurado las credenciales de implementación de Git/FTP para su suscripción de Azure, también se pedirá de crearlas.

3. Abra el archivo./config/config.js desde la raíz de la aplicación y cambie el puerto de producción a `process.env.port`; su `production` propiedad en el `config` objeto debe tener un aspecto similar al ejemplo siguiente:

        production: {
            root: rootPath,
            app: {
                name: 'express1'
            },
            port: process.env.port,
        }

    Esto permite a su aplicación Node.js responder a las solicitudes en el puerto predeterminado que iisnode escucha.
    
4. Abra./package.json y agregue la `engines` propiedad para [especificar la versión deseada de Node.js](#version).

        "engines": {
            "node": "6.6.0"
        }, 

4. Guardar los cambios y luego use git para implementar la aplicación en Azure:

        git add .
        git commit -m "{your commit message}"
        git push azure master

    El generador de Express ya proporciona un archivo de .gitignore, de modo que su `git push` no consumir ancho de banda de intentar cargar la node_modules / directory.

5. Por último, inicie la aplicación de Azure directo en el explorador:

        azure site browse

    Ahora debe ver la aplicación web de Node.js ejecutándose en vivo en servicio de la aplicación de Azure.
    
    ![Ejemplo de exploración a la aplicación implementada.][deployed-express-app]

## <a name="update-your-nodejs-web-app"></a>Actualizar la aplicación web de Node.js

Para realizar actualizaciones en la aplicación web de Node.js ejecuta en la aplicación de servicio, simplemente ejecutar `git add`, `git commit`, y `git push` como hecho cuando se implementan por primera vez la aplicación web.
     
## <a name="how-app-service-deploys-your-nodejs-app"></a>La aplicación de servicio implementa la aplicación Node.js

Servicio de aplicaciones de Azure utiliza [iisnode] para ejecutar aplicaciones Node.js. CLI Azure y motor de Kudu (Git implementación) trabajan en conjunto para dar a una experiencia simplificada al desarrollar e implementar aplicaciones de Node.js desde la línea de comandos. 

- `azure site create --git`reconoce el modelo de Node.js comunes de server.js o app.js y crea un iisnode.yml en el directorio raíz. Puede usar este archivo para personalizar iisnode.
- En `git push azure master`, Kudu automatiza las tareas de implementación siguientes:

    - Si package.json está en la raíz del repositorio, ejecute `npm install --production`.
    - Generar un archivo Web.config para iisnode que apunta a la secuencia de comandos de inicio de package.json (por ejemplo, server.js o app.js).
    - Personalizar Web.config para preparar su aplicación para depuración con el Inspector de nodo.
    
## <a name="use-a-nodejs-framework"></a>Usar un marco Node.js

Si usa un marco Node.js popular, como [Sails.js] [ SAILSJS] o [MEAN.js] [ MEANJS] para desarrollar aplicaciones, puede implementarlos servicios de aplicación. Marcos de Node.js populares tienen su modo de interpretación específico y mantienen actualizan sus dependencias de paquete. Sin embargo, el servicio de aplicación hace los registros stdout y stderr disponibles, para que pueda saber exactamente lo que sucede con la aplicación y realizar cambios en consecuencia. Para obtener más información, vea [obtener registros stdout y stderr desde iisnode](#iisnodelog).

Los siguientes tutoriales le mostrará cómo trabajar con un marco concreto en la aplicación de servicio:

- [Implementar una aplicación web de Sails.js al servicio de la aplicación de Azure]
- [Crear una aplicación de chat de Node.js con Socket.IO en el servicio de aplicación de Azure]
- [Cómo usar io.js con aplicaciones de Azure aplicación de servicio Web]

<a name="version"></a>
## <a name="use-a-specific-nodejs-engine"></a>Usar un motor Node.js específico

En el flujo de trabajo típico saber que use un motor Node.js específico como lo haría normalmente en package.json el servicio de aplicación.
Por ejemplo:

    "engines": {
        "node": "6.6.0"
    }, 

El motor de implementación de Kudu determina qué motor Node.js para su uso en el siguiente orden:

- En primer lugar, mire iisnode.yml para ver si `nodeProcessCommandLine` especificado. En caso afirmativo, utilice.
- A continuación, mire package.json para ver si `"node": "..."` especificado en el `engines` objeto. En caso afirmativo, utilice.
- Elija una versión de Node.js predeterminado de forma predeterminada.

>[AZURE.NOTE] Se recomienda que defina explícitamente el motor de Node.js que desee. Puede cambiar la versión de Node.js predeterminada y puede recibir errores en la aplicación web de Azure porque la versión de Node.js predeterminada no es adecuada para la aplicación.

<a name="iisnodelog"></a>
## <a name="get-stdout-and-stderr-logs-from-iisnode"></a>Obtener registros de stdout y stderr desde iisnode

Para leer los registros de iisnode, siga estos pasos.

> [AZURE.NOTE] Después de completar estos pasos, los archivos de registro no exista hasta que se produce un error.

1. Abra el archivo iisnode.yml que proporciona la CLI de Azure.

2. Establecer los dos parámetros siguientes: 

        loggingEnabled: true
        logDirectory: iisnode
    
    Juntas, dicen iisnode en el servicio de aplicación para poner su resultado stdout y stderror en la D:\home\site\wwwroot\**iisnode** directorio.

3. Guardar los cambios y actualizar los cambios en Azure con los siguientes comandos Git:

        git add .
        git commit -m "{your commit message}"
        git push azure master
   
    Ahora está configurado iisnode. Los pasos siguientes muestran cómo tener acceso a estos registros.
     
4. En el explorador, obtener acceso a la consola de depuración Kudu para la aplicación, que se encuentra en:

        https://{appname}.scm.azurewebsites.net/DebugConsole 

    Esta URL es distinto de la URL de la aplicación web mediante la adición de "*.scm.*" el nombre de DNS. Si omite esta adición a la dirección URL, obtendrá un error 404.

5. Vaya a D:\home\site\wwwroot\iisnode

    ![Navegar hasta la ubicación de los archivos de registro de iisnode.][iislog-kudu-console-find]

6. Haga clic en el icono de **Edición** para el registro que desea leer. También puede hacer clic en **Descargar** o **Eliminar** si lo desea.

    ![Abrir un archivo de registro iisnode.][iislog-kudu-console-open]

    Ahora puede ver el registro para ayudarle a depurar la implementación del servicio de aplicación.
    
    ![Examinar un archivo de registro iisnode.][iislog-kudu-console-read]

## <a name="debug-your-app-with-node-inspector"></a>Depurar la aplicación con el Inspector de nodo

Si utiliza el Inspector de nodo depurar sus aplicaciones Node.js, puede usar para la aplicación de servicio de la aplicación activa. Inspector de nodo está preinstalado en la instalación de iisnode para la aplicación de servicio. Y si implementa mediante Git, Web.config generado automáticamente desde Kudu ya contiene toda la configuración que se debe habilitar el Inspector de nodo.

Para habilitar el Inspector de nodo, siga estos pasos:

1. Abra iisnode.yml en la raíz de repositorio y especificar los parámetros siguientes: 

        debuggingEnabled: true
        debuggerExtensionDll: iisnode-inspector.dll

3. Guardar los cambios y actualizar los cambios en Azure con los siguientes comandos Git:

        git add .
        git commit -m "{your commit message}"
        git push azure master
   
4. Ahora, simplemente vaya al archivo de inicio de la aplicación especificado por la secuencia de comandos de inicio en su package.json con /debug agregado a la dirección URL. Por ejemplo,

        http://{appname}.azurewebsites.net/server.js/debug
    
    O bien,
    
        http://{appname}.azurewebsites.net/app.js/debug

## <a name="more-resources"></a>Más recursos

- [Especificar una versión de Node.js en una aplicación de Azure](../nodejs-specify-node-version-azure-apps.md)
- [Mejores prácticas y la Guía de solución de problemas para aplicaciones de Node.js en Azure](app-service-web-nodejs-best-practices-and-troubleshoot-guide.md)
- [Cómo depurar una aplicación web de Node.js en la aplicación de servicio de Azure](web-sites-nodejs-debug.md)
- [Utilizar módulos de Node.js con aplicaciones de Azure](../nodejs-use-node-modules-azure-apps.md)
- [Aplicaciones de Azure de aplicación de servicio Web: Node.js](http://blogs.msdn.com/b/silverlining/archive/2012/06/14/windows-azure-websites-node-js.aspx)
- [Centro para desarrolladores de Node.js](/develop/nodejs/)
- [Introducción a aplicaciones web en la aplicación de servicio de Azure](app-service-web-get-started.md)
- [Explorar la consola de depuración Kudu muy secreta]

<!-- URL List -->

[CLI de Azure]: ../xplat-cli-install.md
[Servicio de aplicaciones de Azure]: ../app-service/app-service-value-prop-what-is.md
[activar las ventajas de suscriptor de Visual Studio]: http://go.microsoft.com/fwlink/?LinkId=623901
[Bower]: http://bower.io/
[Crear una aplicación de chat de Node.js con Socket.IO en el servicio de aplicación de Azure]: ./web-sites-nodejs-chat-app-socketio.md
[Implementar una aplicación web de Sails.js al servicio de la aplicación de Azure]: ./app-service-web-nodejs-sails.md
[Explorar la consola de depuración Kudu muy secreta]: /documentation/videos/super-secret-kudu-debug-console-for-azure-web-sites/
[Generador de Express para Yeoman]: https://github.com/petecoop/generator-express
[GIT]: http://www.git-scm.com/downloads
[Cómo usar io.js con aplicaciones de Azure aplicación de servicio Web]: ./web-sites-nodejs-iojs.md
[iisnode]: https://github.com/tjanczuk/iisnode/wiki
[MEANJS]: http://meanjs.org/
[Node.js]: http://nodejs.org
[SAILSJS]: http://sailsjs.org/
[suscribirse a una prueba gratuita]: http://go.microsoft.com/fwlink/?LinkId=623901
[web app]: ./app-service-web-overview.md
[Yeoman]: http://yeoman.io/

<!-- IMG List -->

[deployed-express-app]: ./media/app-service-web-nodejs-get-started/deployed-express-app.png
[iislog-kudu-console-find]: ./media/app-service-web-nodejs-get-started/iislog-kudu-console-navigate.png
[iislog-kudu-console-open]: ./media/app-service-web-nodejs-get-started/iislog-kudu-console-open.png
[iislog-kudu-console-read]: ./media/app-service-web-nodejs-get-started/iislog-kudu-console-read.png
