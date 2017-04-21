<properties
    pageTitle="Roles de web y trabajador PHP | Microsoft Azure"
    description="Guía para crear los roles de web y trabajador PHP en un servicio de nube de Azure y configurar el tiempo de ejecución PHP."
    services=""
    documentationCenter="php"
    authors="rmcmurray"
    manager="wpickett"
    editor=""/>

<tags
    ms.service="cloud-services"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="PHP"
    ms.topic="article"
    ms.date="08/11/2016"
    ms.author="robmcm"/>

#<a name="how-to-create-php-web-and-worker-roles"></a>Cómo crear roles de web y trabajador PHP

## <a name="overview"></a>Información general

Esta guía le mostrará cómo crear roles PHP web o de trabajo en un entorno de desarrollo de Windows, elija una versión específica de PHP en las versiones "integradas" disponibles, cambiar la configuración de PHP, habilitar extensiones y por último, implementar en Azure. También se describe cómo configurar una función de trabajo o de web para usar un tiempo de ejecución PHP (con configuración personalizada y extensiones) que proporciona.

## <a name="what-are-php-web-and-worker-roles"></a>¿Qué son los roles de web y trabajador PHP?

Azure ofrece tres modelos para ejecutar aplicaciones de calcular: aplicación de servicio de Azure, máquinas virtuales de Azure y servicios de nube de Azure. Los tres modelos admiten PHP. Servicios de nube, que incluye los roles de web y trabajador, proporciona la *plataforma como servicio (PaaS)*. Dentro de un servicio de nube una función web proporciona un servidor web de Internet Information Services (IIS) dedicado a alojar aplicaciones web front-end. Una función de trabajo puede ejecutar asincrónicas, ejecución larga o perpetuos tareas independientes de entrada o interacción del usuario.

Para obtener más información sobre estas opciones, vea [calcular hospedaje opciones de Azure](./cloud-services/cloud-services-choose-me.md).

## <a name="download-the-azure-sdk-for-php"></a>Descargar el SDK de Azure para PHP

El [SDK de Azure para PHP] consta de varios componentes. En este artículo se usan dos de ellas: Azure PowerShell y los emuladores Azure. Estos dos componentes se pueden instalar mediante el instalador de plataforma Web de Microsoft. Para obtener más información, vea [cómo instalar y configurar Azure PowerShell](powershell-install-configure.md).

## <a name="create-a-cloud-services-project"></a>Crear un proyecto de servicios de nube

El primer paso para crear un rol de trabajo o de web PHP es crear un proyecto de servicio de Azure. un proyecto de servicio de Azure sirve como un contenedor lógico de los roles de web y trabajador y contiene los archivos del proyecto [definición de servicio (.csdef)] y la [Configuración del servicio (.cscfg)] .

Para crear un nuevo proyecto de servicio de Azure, ejecutar Azure PowerShell como administrador y ejecute el siguiente comando:

    PS C:\>New-AzureServiceProject myProject

Este comando creará un nuevo directorio (`myProject`) a la que puede agregar los roles de web y trabajador.

## <a name="add-php-web-or-worker-roles"></a>Agregar funciones PHP web o de trabajo

Para agregar una función de web PHP a un proyecto, ejecute el siguiente comando de directorio raíz del proyecto:

    PS C:\myProject> Add-AzurePHPWebRole roleName

Para una función de trabajo, use este comando:

    PS C:\myProject> Add-AzurePHPWorkerRole roleName

> [AZURE.NOTE] La `roleName` parámetro es opcional. Si se omite, se generará automáticamente el nombre de la función. Será la primera función web creada `WebRole1`, el segundo será `WebRole2`y así sucesivamente. El primer rol de trabajo creado será `WorkerRole1`, será el segundo `WorkerRole2`y así sucesivamente.

## <a name="specify-the-built-in-php-version"></a>Especificar la versión PHP integrada

Cuando agrega una función PHP, web o de trabajo a un proyecto, se modifican los archivos de configuración del proyecto para que se instalará PHP en cada instancia de web o de trabajo de la aplicación cuando se implementa. Para ver la versión de PHP que se instala de forma predeterminada, ejecute el siguiente comando:

    PS C:\myProject> Get-AzureServiceProjectRoleRuntime

El resultado del comando anterior tendrá un aspecto similar a lo que se muestra a continuación. En este ejemplo, el `IsDefault` marcador se establece en `true` para PHP 5.3.17, que indica que está instalada la versión PHP predeterminado.

    Runtime Version     PackageUri                      IsDefault
    ------- -------     ----------                      ---------
    Node 0.6.17         http://nodertncu.blob.core...   False
    Node 0.6.20         http://nodertncu.blob.core...   True
    Node 0.8.4          http://nodertncu.blob.core...   False
    IISNode 0.1.21      http://nodertncu.blob.core...   True
    Cache 1.8.0         http://nodertncu.blob.core...   True
    PHP 5.3.17          http://nodertncu.blob.core...   True
    PHP 5.4.0           http://nodertncu.blob.core...   False

Puede establecer la versión de tiempo de ejecución PHP a cualquiera de las versiones PHP que se muestran. Por ejemplo, para establecer la versión PHP (para una función con el nombre `roleName`) 5.4.0, use el siguiente comando:

    PS C:\myProject> Set-AzureServiceProjectRole roleName php 5.4.0

> [AZURE.NOTE] Versiones PHP disponibles pueden cambiar en el futuro.

## <a name="customize-the-built-in-php-runtime"></a>Personalizar el tiempo de ejecución PHP integrado

Tiene un control completo sobre la configuración de la ejecución PHP que se instala al seguir los pasos anteriores, incluida la modificación de `php.ini` configuración y la activación de extensiones.

Para personalizar el tiempo de ejecución PHP integrado, siga estos pasos:

1. Agregar una nueva carpeta, denominada `php`, para la `bin` directorio de su rol de web. Para una función de trabajo, agregar al directorio de raíz de la función.
2. En la `php` carpeta, cree otra carpeta denominada `ext`. Ponga ningún `.dll` archivos de extensión (por ejemplo, `php_mongo.dll`) que desea habilitar en esta carpeta.
3. Agregar un `php.ini` del archivo a la `php` carpeta. Habilitar las extensiones personalizadas y configurar las directivas PHP en este archivo. Por ejemplo, si desea activar `display_errors` en y habilite la `php_mongo.dll` extensión, el contenido de su `php.ini` archivo sería como sigue:

        display_errors=On
        extension=php_mongo.dll

> [AZURE.NOTE] Cualquier configuración que no establecidos explícitamente en la `php.ini` archivo que proporciona automáticamente will establecerse a sus valores predeterminados. Sin embargo, tenga en cuenta que puede agregar un completo `php.ini` archivo.

## <a name="use-your-own-php-runtime"></a>Usar su propio tiempo de ejecución PHP
En algunos casos, en lugar de seleccionar un tiempo de ejecución PHP integrado y configurar tal como se describió anteriormente, desea proporcionar su propio runtime PHP. Por ejemplo, puede usar el mismo runtime PHP en función de web o de trabajo que usar en su entorno de desarrollo. Esto facilita para asegurarse de que la aplicación no cambiarán comportamiento en su entorno de producción.

### <a name="configure-a-web-role-to-use-your-own-php-runtime"></a>Configurar una función de web para usar su propio tiempo de ejecución PHP

Para configurar una función de web para usar un tiempo de ejecución PHP que proporciona, siga estos pasos:

1. Crear un proyecto de servicio de Azure y agregar una función de web PHP como se describió anteriormente en este tema.
2. Crear un `php` carpeta en el `bin` carpeta que está en el directorio de raíz de su rol web y, a continuación, agregue su tiempo de ejecución PHP (todos los archivos binarios, archivos de configuración, las subcarpetas, etc.) a la `php` carpeta.
3. (OPCIONAL) Si el tiempo de ejecución PHP utiliza los [Controladores de Microsoft para PHP para SQL Server][sqlsrv drivers], debe configurar su rol web para instalar [SQL Server 2012 nativo de cliente] [ sql native client] cuando aprovisionado. Para ello, agregue el [instalador sqlncli.msi x64] a la `bin` carpeta en el directorio de raíz de su rol de web. La secuencia de comandos de inicio se describe en el siguiente paso silenciosamente ejecutará al instalador cuando se aprovisiona el rol. Si el tiempo de ejecución PHP no utiliza la Drivers de Microsoft para PHP para SQL Server, puede quitar la línea siguiente de la secuencia de comandos que se muestra en el siguiente paso:

        msiexec /i sqlncli.msi /qn IACCEPTSQLNCLILICENSETERMS=YES

4. Definir una tarea de inicio que configura [Internet Information Services (IIS)] [ iis.net] utilizar el tiempo de ejecución PHP para controlar las solicitudes de `.php` páginas. Para ello, abra la `setup_web.cmd` archivo (en el `bin` archivo del directorio de raíz de su rol web) en un editor de texto y reemplace su contenido con la siguiente secuencia de comandos:

        @ECHO ON
        cd "%~dp0"

        if "%EMULATED%"=="true" exit /b 0

        msiexec /i sqlncli.msi /qn IACCEPTSQLNCLILICENSETERMS=YES

        SET PHP_FULL_PATH=%~dp0php\php-cgi.exe
        SET NEW_PATH=%PATH%;%RoleRoot%\base\x86

        %WINDIR%\system32\inetsrv\appcmd.exe set config -section:system.webServer/fastCgi /+"[fullPath='%PHP_FULL_PATH%',maxInstances='12',idleTimeout='60000',activityTimeout='3600',requestTimeout='60000',instanceMaxRequests='10000',protocol='NamedPipe',flushNamedPipe='False']" /commit:apphost
        %WINDIR%\system32\inetsrv\appcmd.exe set config -section:system.webServer/fastCgi /+"[fullPath='%PHP_FULL_PATH%'].environmentVariables.[name='PATH',value='%NEW_PATH%']" /commit:apphost
        %WINDIR%\system32\inetsrv\appcmd.exe set config -section:system.webServer/fastCgi /+"[fullPath='%PHP_FULL_PATH%'].environmentVariables.[name='PHP_FCGI_MAX_REQUESTS',value='10000']" /commit:apphost
        %WINDIR%\system32\inetsrv\appcmd.exe set config -section:system.webServer/handlers /+"[name='PHP',path='*.php',verb='GET,HEAD,POST',modules='FastCgiModule',scriptProcessor='%PHP_FULL_PATH%',resourceType='Either',requireAccess='Script']" /commit:apphost
        %WINDIR%\system32\inetsrv\appcmd.exe set config -section:system.webServer/fastCgi /"[fullPath='%PHP_FULL_PATH%'].queueLength:50000"

5. Agregue los archivos de aplicación al directorio de raíz de su rol web. Se trata de directorio raíz del servidor web.

6. Publicar la aplicación, como se describe en la sección [publicar la aplicación](#how-to-publish-your-application) .

> [AZURE.NOTE] La `download.ps1` secuencia de comandos (en el `bin` carpeta del directorio de raíz de la función web) se pueden eliminar después de seguir los pasos descritos anteriormente para usar su propio tiempo de ejecución PHP.

### <a name="configure-a-worker-role-to-use-your-own-php-runtime"></a>Configurar una función de trabajo para usar su propio tiempo de ejecución PHP

Para configurar una función de trabajador para usar un tiempo de ejecución PHP que proporciona, siga estos pasos:

1. Crear un proyecto de servicio de Azure y agregar una función de trabajador PHP como se describió anteriormente en este tema.
2. Crear un `php` carpeta en el directorio de raíz de la función de trabajo y, a continuación, agregue su tiempo de ejecución PHP (todos los archivos binarios, archivos de configuración, las subcarpetas, etc.) a la `php` carpeta.
3. (OPCIONAL) Si el tiempo de ejecución PHP usa [Controladores de Microsoft para PHP para SQL Server][sqlsrv drivers], debe configurar su rol de trabajo para instalar [SQL Server 2012 nativo de cliente] [ sql native client] cuando aprovisionado. Para ello, agregue el [instalador sqlncli.msi x64] al directorio de raíz de la función de trabajo. La secuencia de comandos de inicio se describe en el siguiente paso silenciosamente ejecutará al instalador cuando se aprovisiona el rol. Si el tiempo de ejecución PHP no utiliza la Drivers de Microsoft para PHP para SQL Server, puede quitar la línea siguiente de la secuencia de comandos que se muestra en el siguiente paso:

        msiexec /i sqlncli.msi /qn IACCEPTSQLNCLILICENSETERMS=YES

4. Definir una tarea de inicio que agrega su `php.exe` ejecutable de variable de entorno de ruta de acceso de la función de trabajo cuando se aprovisiona el rol. Para ello, abra el `setup_worker.cmd` (en el directorio de raíz de la función de trabajo) en un editor de texto y reemplace su contenido con la siguiente secuencia de comandos:

        @echo on

        cd "%~dp0"

        echo Granting permissions for Network Service to the web root directory...
        icacls ..\ /grant "Network Service":(OI)(CI)W
        if %ERRORLEVEL% neq 0 goto error
        echo OK

        if "%EMULATED%"=="true" exit /b 0

        msiexec /i sqlncli.msi /qn IACCEPTSQLNCLILICENSETERMS=YES

        setx Path "%PATH%;%~dp0php" /M

        if %ERRORLEVEL% neq 0 goto error

        echo SUCCESS
        exit /b 0

        :error

        echo FAILED
        exit /b -1

5. Agregue los archivos de aplicación al directorio de raíz de la función de su trabajo.

6. Publicar la aplicación, como se describe en la sección [publicar la aplicación](#how-to-publish-your-application) .

## <a name="run-your-application-in-the-compute-and-storage-emulators"></a>Ejecute la aplicación en el proceso y almacenamiento emuladores

Los emuladores Azure proporcionan un entorno local en el que puede probar la aplicación de Azure antes de implementarlo en la nube. Existen algunas diferencias entre los emuladores y el entorno de Azure. Para entender mejor, vea [usar el emulador de almacenamiento de Azure para desarrollo y pruebas](./storage/storage-use-emulator.md).

Tenga en cuenta que debe tener PHP instalado localmente para usarlo de cálculo. El emulador cálculo usará la instalación local de PHP para ejecutar la aplicación.

Para ejecutar el proyecto en los emuladores, ejecute el siguiente comando desde el directorio de raíz de su proyecto:

    PS C:\MyProject> Start-AzureEmulator

Verá el resultado similar al siguiente:

    Creating local package...
    Starting Emulator...
    Role is running at http://127.0.0.1:81
    Started

Puede ver la aplicación que se ejecuta en el emulador abriendo un explorador web y busque la dirección local se muestra en el resultado (`http://127.0.0.1:81` en el resultado del ejemplo anterior).

Para detener la emuladores, ejecute este comando:

    PS C:\MyProject> Stop-AzureEmulator

## <a name="publish-your-application"></a>Publicar su aplicación

Para publicar la aplicación, debe importar la configuración de la publicación mediante el cmdlet [AzurePublishSettingsFile de importación](https://msdn.microsoft.com/library/azure/dn790370.aspx) . A continuación, puede publicar su aplicación mediante el cmdlet [AzureServiceProject publicar](https://msdn.microsoft.com/library/azure/dn495166.aspx) . Para obtener información sobre cómo iniciar sesión, vea [cómo instalar y configurar Azure PowerShell](powershell-install-configure.md).

## <a name="next-steps"></a>Pasos siguientes

Para obtener más información, consulte el [Centro para desarrolladores de PHP](/develop/php/).

[Azure SDK para PHP]: /develop/php/common-tasks/download-php-sdk/
[install ps and emulators]: http://go.microsoft.com/fwlink/p/?linkid=320376&clcid=0x409
[definición de servicio (.csdef)]: http://msdn.microsoft.com/library/windowsazure/ee758711.aspx
[configuración del servicio (.cscfg)]: http://msdn.microsoft.com/library/windowsazure/ee758710.aspx
[iis.net]: http://www.iis.net/
[sql native client]: http://msdn.microsoft.com/sqlserver/aa937733.aspx
[sqlsrv drivers]: http://php.net/sqlsrv
[instalador SQLNCLI.msi x64]: http://go.microsoft.com/fwlink/?LinkID=239648
