<properties
    pageTitle="Implementación local Git Azure de aplicación de servicio"
    description="Obtenga información sobre cómo habilitar la implementación local de Git al servicio de la aplicación de Azure."
    services="app-service"
    documentationCenter=""
    authors="dariagrigoriu"
    manager="wpickett"
    editor="mollybos"/>

<tags
    ms.service="app-service"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="06/13/2016"
    ms.author="dariagrigoriu"/>
    
# <a name="local-git-deployment-to-azure-app-service"></a>Implementación local Git Azure de aplicación de servicio

En este tutorial se muestra cómo implementar la aplicación de servicio de la [aplicación] de Azure desde un repositorio Git en el equipo local. Aplicación de servicio es compatible con este método con la opción de implementación **Git Local** en el [Portal de Azure].  
Muchos de los comandos de Git descritos en este artículo se realizan automáticamente al crear una aplicación de servicio de la aplicación utilizando la [interfaz de línea de comandos de Azure] como se describe [a continuación](app-service-web-get-started.md).

## <a name="prerequisites"></a>Requisitos previos

Para completar este tutorial, necesita:

- GIT. Puede descargar la instalación binario [aquí](http://www.git-scm.com/downloads).  
- Conocimiento básico de Git.
- Una cuenta de Microsoft Azure. Si no tiene una cuenta, puede [registrarse para una prueba gratuita](https://azure.microsoft.com/pricing/free-trial) o [activar las ventajas de suscriptor de Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details).

>[AZURE.NOTE] Si desea empezar a trabajar con el servicio de aplicación de Azure antes de suscribirse para una cuenta de Azure, vaya a [Probar el servicio de aplicaciones](http://go.microsoft.com/fwlink/?LinkId=523751), donde puede crear inmediatamente una aplicación de corta duración starter en la aplicación de servicio. Sin tarjetas de crédito obligatoria; sin compromisos.  

## <a name="Step1"></a>Paso 1: Crear un repositorio local

Realizar las siguientes tareas para crear un nuevo repositorio Git.

1. Iniciar una herramienta de línea de comandos, como **GitBash** (Windows) o **Bash** (Shell Unix). En los sistemas de OS X puede acceder a la línea de comandos mediante la aplicación de **Terminal** .

2. Vaya al directorio donde se encuentre el contenido a distribuir.

3. Iniciar un nuevo repositorio Git, utilice el siguiente comando:

        git init

## <a name="Step2"></a>Paso 2: Confirmar su contenido

Aplicación de servicio es compatible con las aplicaciones creadas en una gran variedad de lenguajes de programación. 

1. Si su repositorio ya incluye contenido saltar este punto y mover al punto 2 a continuación. Si el repositorio no ya incluye contenido simplemente rellenar con un archivo .html estáticos como sigue: 

    - Con un editor de texto, cree un nuevo archivo denominado **index.html** en la raíz del repositorio Git
    - Agregue el siguiente texto como el contenido de la index.html archivo y guárdelo: *Hola Git!*
        
2. Desde la línea de comandos, compruebe que se encuentra en la raíz del repositorio Git. A continuación, use el comando siguiente para agregar archivos al repositorio:

        git add -A 

4. A continuación, confirme los cambios en el repositorio mediante el comando siguiente:

        git commit -m "Hello Azure App Service"

## <a name="Step3"></a>Paso 3: Habilitar el repositorio de aplicación de servicio de aplicaciones

Realice los pasos siguientes para habilitar un repositorio Git para la aplicación de servicio de aplicación.

1. Inicie sesión en el [Portal de Azure].

2. En el módulo de la aplicación servicio de aplicaciones, haga clic en **Configuración > origen de implementación**. Haga clic en **Elegir origen**, a continuación, haga clic en **Repositorio Git Local**y, a continuación, haga clic en **Aceptar**.  

    ![Repositorio de Git local](./media/app-service-deploy-local-git/local_git_selection.png)

3. Si esta es la primera vez configurar un repositorio de Azure, debe crear las credenciales de inicio de sesión para el mismo. Los usará para iniciar sesión en los cambios de repositorio e inserción Azure desde su repositorio Git local. En el módulo de la aplicación, haga clic en **Configuración > credenciales de implementación**, a continuación, configure el nombre de usuario de implementación y la contraseña. Cuando haya terminado, haga clic en **Guardar**.

    ![](./media/app-service-deploy-local-git/deployment_credentials.png)

## <a name="Step4"></a>Paso 4: Implementar el proyecto

Use los siguientes pasos para publicar la aplicación al servicio de aplicación con Git Local.

1. En el módulo de la aplicación en el Portal de Azure, haga clic en **Configuración > Propiedades** para la **Dirección URL de Git**.

    ![](./media/app-service-deploy-local-git/git_url.png)

    **Dirección URL de GIT** es la referencia remota para implementar desde su repositorio local. Deberá usar esta dirección URL en los siguientes pasos.

2. Uso de la línea de comandos, compruebe que se encuentra en la raíz de su repositorio Git local.

3. Usar `git remote` agregar la referencia remota enumerados en la **Dirección URL de Git** desde el paso 1. El comando tendrá un aspecto similar al siguiente:

        git remote add azure https://<username>@localgitdeployment.scm.azurewebsites.net:443/localgitdeployment.git         
    > [AZURE.NOTE] El comando **remoto** agrega una referencia con nombre a un repositorio remoto. En este ejemplo, se crea una referencia con nombre 'azure' repositorio de la aplicación web.

4. Insertar el contenido al servicio de aplicación con la nueva **azure** remoto que acaba de crear.

        git push azure master

    Se le pedirá la contraseña que creó anteriormente cuando restablecer sus credenciales de implementación en el Portal de Azure. Escriba la contraseña (tenga en cuenta que Gitbash no eco asteriscos en la consola mientras escribe la contraseña). 
       
5. Vuelva a la aplicación en el Portal de Azure. Una entrada de registro de la inserción de más reciente debe mostrarse en el módulo de **implementaciones** . 

    ![](./media/app-service-deploy-local-git/deployment_history.png)

6. Haga clic en el botón **Examinar** en la parte superior del módulo de la aplicación para comprobar que el contenido se ha implementado. 
    
## <a name="Step5"></a>Solución de problemas

Los siguientes son errores o problemas más comunes al utilizar Git para publicar en una aplicación de servicio de aplicación en Azure:

****

**Síntoma**: no se ha podido acceso '[siteURL]': no se pudo conectar a [scmAddress]

**Causa**: este error puede ocurrir si la aplicación no está en marcha.

**Resolución**: iniciar la aplicación en el Portal de Azure. Implementación de GIT no funcionarán a menos que la aplicación se está ejecutando. 


****

**Síntoma**: no se ha podido resolver el host 'hostname'

**Causa**: este error puede ocurrir si la información de dirección que escribió al crear remoto 'azure' es incorrecta.

**Resolución**: usar la `git remote -v` comando a la lista de todos los controles remotos, junto con la URL asociada. Compruebe que la dirección URL de remoto 'azure' es correcta. Si es necesario, quitar y volver a crear este remota usando la dirección URL correcta.

****

**Síntoma**: sin referencias cruzadas en común y ninguno especificado; no hacer nada. Tal vez debe especificar una rama como 'patrón'.

**Causa**: este error puede ocurrir si no especifica una rama cuando realiza una git operación de inserción y no se ha establecido el valor de push.default utilizado por Git.

**Resolución**: realizar la operación de inserción de nuevo, que especifica la rama principal. Por ejemplo:

    git push azure master

****

**Síntoma**: src refspec [branchname] no coincide con ninguno.

**Causa**: este error puede ocurrir si intenta insertar una rama que no sea el patrón de 'azure' remoto.

**Resolución**: realizar la operación de inserción de nuevo, que especifica la rama principal. Por ejemplo:

    git push azure master

****

**Síntoma**: Error - cambios comprometidos a repositorio remoto, pero la aplicación web no se actualizan.

**Causa**: este error puede ocurrir si va a implementar una aplicación de Node.js que contiene un archivo package.json que especifica los módulos necesarios adicionales.

**Resolución**: mensajes adicionales que contiene 'npm error'! debe ser registrados antes de este error y puede proporcionar contexto adicional sobre el error. Los siguientes son las causas de este error y el correspondiente 'npm error!' conocidos Mensaje:

* **Archivo package.json incorrecto**: npm error! No puede leer las dependencias.

* **Módulo nativo que no tienen una distribución binaria para Windows**:

    * ¡NPM error! \`cmd "/ c" "regeneración gyp nodo"\` error con 1

        OR

    * ¡NPM error! [modulename@version]preinstalar: \`realizar || gmake\`


## <a name="additional-resources"></a>Recursos adicionales

* [Documentación GIT](http://git-scm.com/documentation)
* [Documentación Kudu del proyecto](https://github.com/projectkudu/kudu/wiki)
* [Implementación de continua Azure de aplicación de servicio](app-service-continuous-deployment.md)
* [Cómo usar PowerShell para Azure](../powershell-install-configure.md)
* [Cómo usar la interfaz de línea de comandos de Azure](../xplat-cli-install.md)

[Servicio de aplicaciones de Azure]: https://azure.microsoft.com/documentation/articles/app-service-changes-existing-services/
[Azure Developer Center]: http://www.windowsazure.com/en-us/develop/overview/
[Portal de Azure]: https://portal.azure.com
[Git website]: http://git-scm.com
[Installing Git]: http://git-scm.com/book/en/Getting-Started-Installing-Git
[Interfaz de línea de comandos de Azure]: https://azure.microsoft.com/en-us/documentation/articles/xplat-cli-azure-resource-manager/

[Using Git with CodePlex]: http://codeplex.codeplex.com/wikipage?title=Using%20Git%20with%20CodePlex&referringTitle=Source%20control%20clients&ProjectName=codeplex
[Quick Start - Mercurial]: http://mercurial.selenic.com/wiki/QuickStart
