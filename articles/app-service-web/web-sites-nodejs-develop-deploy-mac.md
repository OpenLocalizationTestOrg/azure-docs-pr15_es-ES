<properties
    pageTitle="Crear una aplicación web de Node.js en la aplicación de servicio de Azure | Microsoft Azure"
    description="Obtenga información sobre cómo implementar una aplicación de Node.js en una aplicación web de servicio de aplicaciones de Azure."
    services="app-service\web"
    documentationCenter="nodejs"
    authors="rmcmurray"
    manager="wpickett"
    editor=""/>

<tags
    ms.service="app-service-web"
    ms.workload="web"
    ms.tgt_pltfrm="na"
    ms.devlang="nodejs"
    ms.topic="hero-article"
    ms.date="08/11/2016"
    ms.author="robmcm"/>

# <a name="create-a-nodejs-web-app-in-azure-app-service"></a>Crear una aplicación web de Node.js en la aplicación de servicio de Azure

> [AZURE.SELECTOR]
- [.NET](web-sites-dotnet-get-started.md)
- [Node.js](web-sites-nodejs-develop-deploy-mac.md)
- [Java](web-sites-java-get-started.md)
- [PHP - Git](web-sites-php-mysql-deploy-use-git.md)
- [PHP - FTP](web-sites-php-mysql-deploy-use-ftp.md)
- [Python](web-sites-python-ptvs-django-mysql.md)

Este tutorial muestra cómo crear una aplicación de [Node.js](http://nodejs.org) simple e implementar una [aplicación web](app-service-web-overview.md) en la [Aplicación de servicio de Azure](../app-service/app-service-value-prop-what-is.md) con [Git](http://git-scm.com). Pueden seguir las instrucciones de este tutorial en ningún sistema operativo que sea capaz de ejecutar Node.js.

Aprenderá:

* Cómo crear una aplicación web de servicio de aplicación de Azure a través del Portal de Azure.
* Cómo implementar una aplicación de Node.js en la aplicación web presionando al repositorio de Git de la aplicación web.

La aplicación completada escribe una cadena corta "Hola a todos" en el explorador.

![Un explorador que muestra el mensaje 'Hola a todos'.][helloworld-completed]

Tutoriales y código de ejemplo con aplicaciones de Node.js más complejas, o para otros temas sobre cómo usar Node.js en Azure, consulte el [Centro para desarrolladores de Node.js](/develop/nodejs/).

> [AZURE.NOTE]
> Para completar este tutorial, necesita una cuenta de Microsoft Azure. Si no tiene una cuenta, puede [activar las ventajas de suscriptor de Visual Studio](/en-us/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F) o [suscribirse a una prueba gratuita](/en-us/pricing/free-trial/?WT.mc_id=A261C142F).
>
> Si desea empezar a trabajar con el servicio de aplicación de Azure antes de suscribirse a una cuenta de Azure, vaya a [Probar el servicio de aplicación](http://go.microsoft.com/fwlink/?LinkId=523751). Allí, puede crear inmediatamente una aplicación web de starter corta duración en la aplicación de servicio: sin tarjeta de crédito obligatorio y sin compromisos.

## <a name="create-a-web-app-and-enable-git-publishing"></a>Crear una aplicación web y habilitar la publicación de Git

Siga estos pasos para crear una aplicación web en la aplicación de servicio de Azure y habilitar la publicación de Git. 

[GIT](http://git-scm.com/) es un sistema de control de versiones distribuidos que puede usar para implementar el sitio Web de Azure. Va a almacenar el código que escriba para la aplicación web en un repositorio Git local y, a continuación, deberá implementar su código en Azure presionando a un repositorio remoto. Este método de implementación es una característica de aplicaciones de servicio de aplicación web.  

1. Inicie sesión en el [Portal de Azure](https://portal.azure.com).

2. Haga clic en el icono **+ nuevo** en la parte superior izquierdo del Portal de Azure.

3. Haga clic en **Web + Mobile**y, a continuación, haga clic en **Web app**.

    ![][portal-quick-create]

4. Escriba un nombre para la aplicación web en el cuadro de la **aplicación Web** .

    Este nombre debe ser único en el dominio azurewebsites.net porque la dirección URL de la aplicación web será {nombre}. azurewebsites.net. Si el nombre que escribe no único, aparece un signo de exclamación rojo en el cuadro de texto.

5. Seleccione una **suscripción**.

6. Seleccione un **Grupo de recursos** o cree uno nuevo.

    Para obtener más información acerca de los grupos de recursos, vea [información general del Administrador de recursos de Azure](../azure-resource-manager/resource-group-overview.md).

7. Seleccione una **Ubicación o plan de aplicación de servicio** o cree uno nuevo.

    Para obtener más información acerca de los planes de servicio de aplicaciones, vea [información general de los planes de servicio de la aplicación de Azure](../azure-web-sites-web-hosting-plans-in-depth-overview.md)

8. Haga clic en **crear**.
   
    ![][portal-quick-create2]

    En poco tiempo, suelen ser menores que un minuto Azure termine de crear la nueva aplicación web.

9. Haga clic en **aplicaciones Web > {la nueva aplicación web}**.

    ![](./media/web-sites-nodejs-develop-deploy-mac/gotowebapp.png)

10. En el módulo de la **aplicación Web** , haga clic en el elemento de **implementación** .

    ![][deployment-part]

11. En el módulo de **Implementación continua** , haga clic en **Elegir origen**

12. Haga clic en **Repositorio Git Local**y, a continuación, haga clic en **Aceptar**.

    ![][setup-git-publishing]

13. Configurar las credenciales de implementación si aún no lo ha hecho.

    una. En el módulo de aplicación Web, haga clic en **Configuración > credenciales de implementación**.

    ![][deployment-credentials]
 
    b. Crear un nombre de usuario y contraseña. 
    
    ![](./media/web-sites-nodejs-develop-deploy-mac/setdeploycreds.png)

14. En el módulo de aplicación Web, haga clic en **configuración**y, a continuación, haga clic en **Propiedades**.
 
    Para publicar, deberá insertar a un repositorio Git remoto. La dirección URL para el repositorio aparece en la **Dirección URL de GIT**. Más adelante en el tutorial, usará esta dirección URL.

    ![][git-url]

## <a name="build-and-test-your-application-locally"></a>Crear y probar la aplicación localmente

En esta sección, creará un archivo **server.js** que contiene una versión ligeramente modificada del ejemplo 'Hola a todos' de [nodejs.org]. El código agrega process.env.PORT como el puerto para escuchar cuando se ejecuta en una aplicación web de Azure.

1. Cree un directorio denominado *Hola a todos*.

2. Use un editor de texto para crear un nuevo archivo denominado **server.js** en el directorio de *Hola a todos* .

2. Copie el código siguiente en el archivo **server.js** y, a continuación, guarde el archivo:

        var http = require('http')
        var port = process.env.PORT || 1337;
        http.createServer(function(req, res) {
          res.writeHead(200, { 'Content-Type': 'text/plain' });
          res.end('Hello World\n');
        }).listen(port);

3. Abra la línea de comandos y use el comando siguiente para iniciar la aplicación web localmente.

        node server.js

4. Abra el explorador web y vaya a http://localhost:1337. 

    Aparece una página Web que muestra "Hola a todos", como se muestra en la siguiente captura de pantalla.

    ![Un explorador que muestra el mensaje 'Hola a todos'.][helloworld-localhost]

## <a name="publish-your-application"></a>Publicar su aplicación

1. Si aún no lo ha hecho, instale Git.

    Para obtener instrucciones de instalación para su plataforma, consulte la [página de descarga de Git](http://git-scm.com/download).

1. Desde la línea de comandos, cambie los directorios al directorio de **Hola a todos** y escriba el siguiente comando iniciar un repositorio Git local.

        git init


2. Utilice los siguientes comandos para agregar archivos al repositorio:

        git add .
        git commit -m "initial commit"

3. Agregar un Git remoto para insertar las actualizaciones de la aplicación web que creó anteriormente, mediante el comando siguiente:

        git remote add azure [URL for remote repository]

4. Aplicar los cambios a Azure mediante el comando siguiente:

        git push azure master

    Se le pedirá la contraseña que creó anteriormente. El resultado es similar al siguiente ejemplo.

        Counting objects: 3, done.
        Delta compression using up to 8 threads.
        Compressing objects: 100% (2/2), done.
        Writing objects: 100% (3/3), 374 bytes, done.
        Total 3 (delta 0), reused 0 (delta 0)
        remote: New deployment received.
        remote: Updating branch 'master'.
        remote: Preparing deployment for commit id '5ebbe250c9'.
        remote: Preparing files for deployment.
        remote: Deploying Web.config to enable Node.js activation.
        remote: Deployment successful.
        To https://user@testsite.scm.azurewebsites.net/testsite.git
         * [new branch]      master -> master

5. Para ver la aplicación, haga clic en el botón **Examinar** en el elemento **Web App** en el portal de Azure.

    ![Botón Examinar](./media/web-sites-nodejs-develop-deploy-mac/browsebutton.png)

    ![Hola a todos en Azure](./media/web-sites-nodejs-develop-deploy-mac/helloworldazure.png)

## <a name="publish-changes-to-your-application"></a>Publicar los cambios en la aplicación

1. Abra el archivo **server.js** en un editor de texto y cambiar 'Hola a todos\n' a 'Hola Azure\n'. 

2. Guarde el archivo.

2. Desde la línea de comandos, cambie al directorio de **Hola a todos** los directorios y ejecute los comandos siguientes:

        git add .
        git commit -m "changing to hello azure"
        git push azure master

    Se le pedirá la contraseña de nuevo.

3. Actualizar la ventana del explorador que se haya desplazado a la dirección URL de la aplicación web.

    ![Una página web que muestra 'Hola Azure'][helloworld-completed]

## <a name="roll-back-a-deployment"></a>Deshacer una implementación

Desde el módulo de **Web app** puede hacer clic en **Configuración > implementación continua** para ver el historial de implementación en el módulo de **implementaciones** . Si necesita volver a una implementación anterior, puede seleccionarlo y, a continuación, haga clic en **volver a implementar** en el módulo de **Detalles de la implementación** .

## <a name="next-steps"></a>Pasos siguientes

Ha implementado una aplicación de Node.js para una aplicación web de servicio de aplicaciones de Azure. Para obtener más información acerca de cómo ejecutan aplicaciones de servicio de aplicación web Node.js aplicaciones, consulte [Azure aplicación de servicio Web Apps: Node.js](http://blogs.msdn.com/b/silverlining/archive/2012/06/14/windows-azure-websites-node-js.aspx) y [especificar una versión de Node.js en una aplicación de Azure](../nodejs-specify-node-version-azure-apps.md).

Node.js proporciona un rico ecosistema de módulos que se pueden utilizar las aplicaciones. Para obtener información sobre el funcionamiento de aplicaciones Web con módulos, vea [utilizando Node.js módulos con aplicaciones de Azure](../nodejs-use-node-modules-azure-apps.md).

Si tiene problemas con la aplicación después de que se ha implementado en Azure, consulte [cómo depurar una aplicación de Node.js en Azure aplicación de servicio](web-sites-nodejs-debug.md) para obtener información sobre diagnosticar el problema.

En este artículo, se utiliza el Portal de Azure para crear una aplicación web. También puede usar la [interfaz de línea de comandos de Azure](../xplat-cli-install.md) o [Azure PowerShell](../powershell-install-configure.md) para realizar las mismas operaciones.

Para obtener más información sobre cómo desarrollar aplicaciones Node.js en Azure, consulte el [Centro para desarrolladores de Node.js](/develop/nodejs/).

[helloworld-completed]: ./media/web-sites-nodejs-develop-deploy-mac/helloazure.png
[helloworld-localhost]: ./media/web-sites-nodejs-develop-deploy-mac/helloworldlocal.png
[portal-quick-create]: ./media/web-sites-nodejs-develop-deploy-mac/create-quick-website.png
[portal-quick-create2]: ./media/web-sites-nodejs-develop-deploy-mac/create-quick-website2.png
[setup-git-publishing]: ./media/web-sites-nodejs-develop-deploy-mac/setup_git_publishing.png
[go-to-dashboard]: ./media/web-sites-nodejs-develop-deploy-mac/go_to_dashboard.png
[deployment-part]: ./media/web-sites-nodejs-develop-deploy-mac/deployment-part.png
[deployment-credentials]: ./media/web-sites-nodejs-develop-deploy-mac/deployment-credentials.png
[git-url]: ./media/web-sites-nodejs-develop-deploy-mac/git-url.png
