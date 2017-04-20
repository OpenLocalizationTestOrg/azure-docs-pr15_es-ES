<properties
    pageTitle="Crear, configurar e implementar una aplicación web PHP en Azure"
    description="Tutorial que muestra cómo ejecutar una aplicación web PHP (Laravel) en la aplicación de servicio de Azure. Obtenga información sobre cómo configurar el servicio de aplicación de Azure para satisfacer los requisitos de framework PHP que elija."
    services="app-service\web"
    documentationCenter="php"
    authors="cephalin"
    manager="wpickett"
    editor=""
    tags="mysql"/>

<tags
    ms.service="app-service-web"
    ms.workload="web"
    ms.tgt_pltfrm="na"
    ms.devlang="PHP"
    ms.topic="article"
    ms.date="06/03/2016" 
    ms.author="cephalin"/>

# <a name="create-configure-and-deploy-a-php-web-app-to-azure"></a>Crear, configurar e implementar una aplicación web PHP en Azure

[AZURE.INCLUDE [tabs](../../includes/app-service-web-get-started-nav-tabs.md)]

En este tutorial se muestra cómo crear, configurar e implementar una aplicación web PHP para Azure y cómo configurar el servicio de aplicación de Azure para satisfacer los requisitos de la aplicación web de PHP. Al final del tutorial, tendrá un trabajo [Laravel](https://www.laravel.com/) Online ejecutándose en vivo en [Servicio de la aplicación de Azure](../app-service/app-service-value-prop-what-is.md).

Como desarrollador PHP, puede poner el marco PHP favorito en Azure. Este tutorial usa Laravel simplemente como un ejemplo de una aplicación concreta. Aprenderá: 

- Implementar mediante Git
- Establecer PHP versión
- Usar un archivo de inicio no está en el directorio raíz de la aplicación
- Variables de entorno específicas de Access
- Actualizar la aplicación en Azure

Puede aplicar lo aprendido aquí a otras aplicaciones web PHP que se implementación en Azure.

>[AZURE.INCLUDE [app-service-linux](../../includes/app-service-linux.md)] 

## <a name="prerequisites"></a>Requisitos previos

- Instalar [PHP 5.6.x](http://php.net/downloads.php) (soporte técnico de PHP 7 es beta)
- Instalar [Compositor](https://getcomposer.org/download/)
- Instalar [CLI Azure](../xplat-cli-install.md)
- Instalar [Git](http://www.git-scm.com/downloads)
- Obtener una cuenta de Microsoft Azure. Si no tiene una cuenta, puede [registrarse para una prueba gratuita](/pricing/free-trial/?WT.mc_id=A261C142F) o [activar las ventajas de suscriptor de Visual Studio](/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F).

>[AZURE.NOTE] Vea una aplicación web en acción. [Probar el servicio de aplicación](http://go.microsoft.com/fwlink/?LinkId=523751) inmediatamente y crear una aplicación de corta duración starter: necesaria ninguna tarjeta de crédito, sin compromisos.

## <a name="create-a-php-laravel-app-on-your-dev-machine"></a>Crear una aplicación PHP (Laravel) en el equipo de desarrollo

1. Abra un nuevo símbolo de Windows, la ventana de PowerShell, el shell de Linux o el terminal de OS X. Ejecute los comandos siguientes para comprobar que las herramientas necesarias están instaladas correctamente en su equipo. 

        php --version
        composer --version
        azure --version
        git --version

    ![Instalación de herramientas de prueba antes de crear la aplicación PHP (Laravel) de Azure](./media/app-service-web-php-get-started/test-tools.png)

    Si no ha instalado las herramientas, vea [requisitos previos](#Prerequisites) para los vínculos de descarga.
    
2. Instalar Laravel de este modo:

        composer global require "laravel/installer

3. `CD`en un directorio de trabajo y cree una nueva aplicación Laravel así:

        cd <working_directory>
        laravel new <app_name>

4. `CD`en recién creado `<app_name>` directorio y probar la aplicación de la siguiente manera:

        cd <app_name>
        php artisan serve
        
    Debería poder navegar a http://localhost:8000 en un explorador ahora y ver la pantalla de inicio de Laravel.
    
    ![Probar la aplicación PHP (Laravel) localmente antes de implementarlo en Azure](./media/app-service-web-php-get-started/laravel-splash-screen.png)
    
Así que ahora, simplemente el flujo de trabajo normal de Laravel y no está aquí para <a href="https://laravel.com/docs/5.2" rel="nofollow">obtener información sobre Laravel</a>. Así que continuemos.

## <a name="create-an-azure-web-app-and-set-up-git-deployment"></a>Crear una aplicación web de Azure y configurar la implementación Git

>[AZURE.NOTE] "Espere! ¿Qué hago para implementar con FTP?" Hay un [tutorial FTP](web-sites-php-mysql-deploy-use-ftp.md) para sus necesidades. 

Con la CLI Azure, puede crear una aplicación web en la aplicación de servicio de Azure y configurarla Git implementación con una sola línea de comandos. Vamos a hacer esto.

1. Cambiar a modo ASM e inicie sesión en Azure:

        azure config mode asm
        azure login
    
    Seguir el mensaje de ayuda para continuar el proceso de inicio de sesión.
    
    ![Inicie sesión en Azure para implementar la aplicación PHP (Laravel) Azure](./media/app-service-web-php-get-started/log-in-to-azure-cli.png)

4. Ejecute el comando para crear la aplicación web de Azure con Git implementación. Cuando se le solicite, especifique el número de la región que desee.

        azure site create --git <app_name>
    
    ![Crear el recurso de Azure para su aplicación PHP (Laravel) en Azure](./media/app-service-web-php-get-started/create-site-cli.png)
    
    >[AZURE.NOTE] Si nunca ha configurado las credenciales de implementación para su suscripción de Azure, le pedirá que crean. Estas credenciales, no las credenciales de cuenta de Azure, se usan por la aplicación de servicio solo para las implementaciones Git e inicios de sesión FTP. 
    
    Este comando crea un nuevo repositorio Git en el directorio actual (con `git init`) y se conecta al repositorio en Azure como remoto Git (con `git remote add`).

<a name="configure"/>
## <a name="configure-the-azure-web-app"></a>Configurar la aplicación web de Azure

Para que la aplicación Laravel trabajar en Azure, debe atender a varias cosas. Puede hacer este ejercicio similar para el marco PHP elegido.

- Configurar PHP 5.5.9 o superior. Consulte [Requisitos de servidor de última Laravel 5.2](https://laravel.com/docs/5.2#server-requirements) para la lista completa de requisitos del servidor. El resto de la lista son extensiones que ya están habilitadas por las instalaciones de PHP de Azure. 
- Establecer las variables de entorno su aplicación necesita. Laravel utiliza la `.env` archivo para facilitar la definición de variables de entorno. Sin embargo, dado que no debe ser confirmada en control de código fuente (consulte [Configuración del entorno Laravel](https://laravel.com/docs/5.2/configuration#environment-configuration), se establece la configuración de la aplicación de la aplicación web de Azure en su lugar.
- Asegúrese de que punto de entrada de la aplicación Laravel, `public/index.php`, primero se carga. Consulte [Descripción general del ciclo de vida de Laravel](https://laravel.com/docs/5.2/lifecycle#lifecycle-overview). En otras palabras, es necesario configurar la dirección URL de raíz de la aplicación web para que apunten a la `public` directorio.
- Habilitar la extensión de compositor en Azure, ya que tienen un composer.json. De este modo, puede dejar que compositor preocuparse por obtener los paquetes necesarios cuando se implementa con `git push`. Trata de comodidad. Si no habilita la automatización compositor, ¿necesita quitar `/vendor` desde el `.gitignore` de archivo para que incluya Git ("anule-pasa por alto") todo el contenido de la `vendor` directorio cuando confirmación e implementación de código.

Vamos a configurar estas tareas de forma secuencial.

4. Establezca la versión PHP que requiere la aplicación Laravel.

        azure site set --php-version 5.6

    Finalice la configuración de la versión PHP! 
    
4. Generar un nuevo `APP_KEY` para su Azure web app y establézcalo como una configuración de aplicación para la aplicación web de Azure.

        php artisan key:generate --show
        azure site appsetting add APP_KEY="<output_of_php_artisan_key:generate_--show>"

4. Además, activar Laravel de depuración para preferencia cualquier críptica `Whoops, looks like something went wrong.` página.

        azure site appsetting add APP_DEBUG=true

    Ya habrá acabado establecer variables de entorno.
    
    >[AZURE.NOTE] Espere, vamos a ralentizar un poco y explicar qué hace Laravel y qué hace Azure aquí. Laravel utiliza la `.env` archivo en el directorio raíz para proporcionar variables de entorno para la aplicación, donde encontrará la línea `APP_DEBUG=true` (y también `APP_KEY=...`). Se tiene acceso a esta variable en `config/app.php` por el código de     `'debug' => env('APP_DEBUG', false),`. [env()](https://laravel.com/docs/5.2/helpers#method-env) es un método auxiliar Laravel que usa la PHP [getenv()](http://php.net/manual/en/function.getenv.php) en segundo plano.
    >
    >Sin embargo, `.env` pasa por alto Git porque está señalada el `.gitignore` archivo en el directorio raíz. En pocas palabras, `.env`  
 en su Git local repositorio no se inserta en Azure con el resto de los archivos. Por supuesto, se puede quitar dicha línea de `.gitignore`, pero ya hemos establecido que no se recomienda confirmar este archivo en el control de origen. No obstante, necesita una manera de especificar estas variables de entorno en Azure. 
    >
    >La buena noticia es que la configuración de la aplicación de servicio de la aplicación de Azure admite [getenv()](http://php.net/manual/en/function.getenv.php)  
 en PHP. Es así, aunque puede utilizar FTP u otro medio para cargar manualmente una `.env` archivo en Azure, basta con especificar las variables que desee como la configuración de la aplicación de Azure sin un `.env` en Azure, como hecho. Además, si es una variable en ambos un `.env` de archivos y configuración de la aplicación de Azure, la configuración de la aplicación de Azure gana.     

4. Las dos últimos tareas (configurando el directorio virtual y habilitar compositor) requiere el [portal de Azure](https://portal.azure.com), así que inicie sesión en el [portal](https://portal.azure.com) con su cuenta de Azure.

4. Desde el menú de la izquierda, haga clic en **Servicios de aplicación** > **&lt;app_name >** > **Herramientas**.

    ![Habilitar a Compositor de la aplicación PHP (Laravel) en Azure](./media/app-service-web-php-get-started/configure-composer-tools.png)
    
    >[AZURE.TIP] Si hace clic en **configuración** , en lugar de **Herramientas**, podrá tener acceso a la **Configuración de la aplicación**  
 módulo, que le permite establece versiones PHP, configuración de la aplicación y directorios virtuales como hecho. 
    
4. Haga clic en **extensiones** > **Agregar** para agregar una extensión.

4. Seleccione **Compositor** en el [módulo](../azure-portal-overview.md) de **extensión de elegir** (*módulo*: una página de portal abre horizontalmente).

4. En el módulo de **aceptación de condiciones legales** , haga clic en **Aceptar** . 

5. En el módulo de **extensión de agregar** , haga clic en **Aceptar** .

    Cuando haya terminado Azure agregando la extensión, verá un mensaje emergente descriptivo en la esquina así como  **Compositor** enumerados en el módulo de **extensiones** .

    ![Módulo de extensiones después de habilitar la aplicación PHP (Laravel) en Azure compositor](./media/app-service-web-php-get-started/configure-composer-end.png)

    Ya habrá acabado compositor habilitar.
    
4. En el módulo de la aplicación web, haga clic en **configuración de** > **Configuración de la aplicación**.

    ![Módulo de configuración de acceso para configurar el directorio virtual de la aplicación PHP (Laravel) en Azure](./media/app-service-web-php-get-started/configure-virtual-dir-settings.png)

    En el módulo de **Configuración de la aplicación** , tenga en cuenta la versión PHP establecidos anteriormente:

    ![Versión PHP en el módulo de configuración de la aplicación PHP (Laravel) en Azure](./media/app-service-web-php-get-started/configure-virtual-dir-settings-a.png)

    y la configuración de la aplicación que agregó:
    
    ![Configuración de la aplicación en el módulo de configuración de la aplicación PHP (Laravel) en Azure](./media/app-service-web-php-get-started/configure-virtual-dir-settings-b.png)

4. Desplácese hasta la parte inferior de la hoja y cambie el directorio de raíz virtual para que apunten a **site\wwwroot\public** en lugar de **site\wwwroot**.

    ![Configurar el directorio virtual de la aplicación PHP (Laravel) en Azure](./media/app-service-web-php-get-started/configure-virtual-dir-public.png)

4. Haga clic en **Guardar** en la parte superior de la hoja.

    Termine configurando el directorio virtual! 

## <a name="deploy-your-web-app-with-git-and-setting-environment-variables"></a>Implemente la aplicación web con Git (y las variables de entorno)

Está listo para implementar el código ahora. Puede hacer este en el símbolo o terminal.

4. Confirmar todos los cambios e implemente el código de la aplicación web de Azure como lo haría en cualquier repositorio Git:

        git add .
        git commit -m "Hurray! My first commit for my Azure app!"
        git push azure master 

    Cuando se ejecuta `git push`, se le pedirá que proporcione su contraseña de implementación Git. Si se solicita crear credenciales de implementación en `azure site create` anterior, escriba la contraseña que usa.
    
5. Vamos a verlo ejecutar en el explorador con este comando:

        azure site browse

    El explorador debe mostrar la pantalla de inicio de Laravel.
    
    ![Pantalla de inicio de Laravel después de implementar la aplicación web de Azure](./media/app-service-web-php-get-started/laravel-azure-splash-screen.png)
    
    Enhorabuena, ahora se ejecuta una aplicación web de Laravel en Azure.
             
## <a name="troubleshoot-common-errors"></a>Solucionar errores comunes

Estas son algunas los errores que puede surgir al seguir este tutorial:

- [Azure CLI muestra "'sitio' no es un comando de azure"](#clierror)
- [Aplicación Web muestra el error HTTP 403](#http403)
- [Aplicación Web muestra "Whoops, parece hubo un problema".](#whoops)
- [La aplicación Web no aparece "compatible cifrador encontrado."](#encryptor)

<a name="clierror"></a>
### <a name="azure-cli-shows-site-is-not-an-azure-command"></a>Azure CLI muestra "'sitio' no es un comando de azure"

Cuando se ejecuta `azure site *` en la línea de comandos terminal, consulte el error`error:   'site' is not an azure command. See 'azure help'.` 

Suele ser un resultado de cambiar en modo de "ARM" (Administrador de recursos de Azure). Para resolver este problema, cambie en modo de "ASM" (administración de servicios de Azure) ejecutando `azure config mode asm`.

<a name="http403"></a>
### <a name="web-app-shows-http-403-error"></a>Aplicación Web muestra el error HTTP 403

Ha implementado la aplicación web de Azure correctamente, pero cuando vaya a su aplicación web de Azure, se obtiene un `HTTP 403` o`You do not have permission to view this directory or page.`

Esto es más probable porque la aplicación web no puede encontrar el punto de entrada a la aplicación Laravel. Asegúrese de que ha cambiado el directorio virtual raíz para que apunten a `site\wwwroot\public`, donde del Laravel `index.php` es (vea [Configurar la aplicación web de Azure](#configure)).

<a name="whoops"></a>
### <a name="web-app-shows-whoops-looks-like-something-went-wrong"></a>Aplicación Web muestra "Whoops, parece hubo un problema".

Ha implementado la aplicación web de Azure correctamente, pero cuando examine la aplicación web de Azure, recibe el mensaje`Whoops, looks like something went wrong.`

Para obtener un error más descriptivo, habilitar Laravel depuración estableciendo `APP_DEBUG` variable de entorno `true` (vea [Configurar la aplicación web de Azure](#configure)).

<a name="encryptor"></a>
### <a name="web-app-shows-no-supported-encryptor-found"></a>La aplicación Web no aparece "compatible cifrador encontrado."

Ha implementado la aplicación web de Azure correctamente, pero cuando examine la aplicación web de Azure, recibe el mensaje de error siguiente:

![APP_KEY que faltan en la aplicación PHP (Laravel) en Azure](./media/app-service-web-php-get-started/laravel-error-APP_KEY.png)
    
Es un error mala, pero al menos no es críptica desde que se activó depuración Laravel. Una búsqueda rápida de la cadena de error en los foros de Laravel mostrar que es debido a que no se establece la APP_KEY `.env`, o en su caso, no tiene `.env` en Azure en absoluto. Puede corregir este error agregando configuración `APP_KEY` como una aplicación de Azure configuración (vea [Configurar la aplicación web de Azure](#configure)).
    
## <a name="next-steps"></a>Pasos siguientes

Obtenga información sobre cómo agregar datos a la aplicación mediante la [creación de una base de datos MySQL en Azure](../store-php-create-mysql-database.md). Además, consulte los vínculos más útiles para PHP en Azure siguiente:

- [Centro para desarrolladores de PHP](/develop/php/).
- [Crear una aplicación web de Azure Marketplace](app-service-web-create-web-app-from-marketplace.md)
- [Configurar PHP en Azure de aplicación de servicio Web Apps](web-sites-php-configure.md)
- [Convertir WordPress en multisitio en Azure de aplicación de servicio](web-sites-php-convert-wordpress-multisite.md)
- [WordPress empresariales en Azure de aplicación de servicio](web-sites-php-enterprise-wordpress.md)
