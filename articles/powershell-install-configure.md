<properties
    pageTitle="Cómo instalar y configurar PowerShell de Azure"
    description="Obtenga información sobre cómo instalar y configurar Azure PowerShell."
    editor="tysonn"
    manager="dongill"
    documentationCenter=""
    services=""
    authors="coreyp-at-msft"/>

<tags
    ms.service="multiple"
    ms.workload="multiple"
    ms.tgt_pltfrm="powershell"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/30/2016"
    ms.author="coreyp"/>

# <a name="how-to-install-and-configure-azure-powershell"></a>Cómo instalar y configurar PowerShell de Azure

<div class="dev-center-tutorial-selector sublanding"><a href="/manage/install-and-configure-windows-powershell/" title="PowerShell" class="current">PowerShell</a><a href="/manage/install-and-configure-cli/" title="Azure CLI">CLI Azure</a></div>

##<a name="what-is-azure-powershell"></a>¿Qué es PowerShell de Azure?
PowerShell Azure es un conjunto de módulos que proporcionan cmdlets para administrar Azure con Windows PowerShell. Puede usar los cmdlets para crear, probar, implementar y administrar soluciones y servicios ofrecidos a través de la plataforma Windows Azure. En la mayoría de los casos, los cmdlets se puede utilizar para las mismas tareas que el Portal de Azure, como crear y configurar los servicios en la nube, máquinas virtuales, redes virtuales y aplicaciones web.

## <a name="how-versioning-works"></a>Cómo funciona el control de versiones

PowerShell Azure usa semántico control de versiones, significa que si versión A > versión B y, a continuación, versión A tiene las API más actualizadas. Además, significa que en la media de la versión principal un salto de los cambios realizados en uno o más cmdlets.  Por lo tanto, por ejemplo, versión 1.7.0 es una revisión para tratar un cambio importante en las versiones 1.x de PowerShell de Azure.

Para obtener más información acerca de las prácticas de control de versiones semántico en Azure PowerShell, vea la especificación de control de versiones semántico en: http://semver.org
 
Para obtener las últimas API, debe usar la versión 2.x. Pero si tiene secuencias de comandos escritas con la versión 1.x y no desea absorber los cambios importantes en la versión 2.x descritos en las [Notas de la versión](https://github.com/Azure/azure-powershell/blob/dev/documentation/release-notes/migration-guide.2.0.0.md), 2.x, a continuación, debe instalar 1.7.0.

Un error de coincidencia de versión puede producirse si se instala la versión más reciente del módulo de perfil y posteriormente se carga una versión anterior de un módulo que depende de él. La manera más sencilla de resolver este problema es instalar desde el archivo .msi más reciente. El archivo .msi limpia automáticamente las versiones anteriores de los módulos.
 
###<a name="installing-module-versions-side-by-side"></a>Instalar versiones de módulo en paralelo

Versión 2.1.0 (versión 1.2.6 para AzureStack) son las primeras versiones de módulo diseñadas para instalarse y usa en paralelo. Como Azure PowerShell utiliza módulos binarios, debe abrir una nueva ventana de PowerShell y utilizar el **Módulo de importación** para importar una versión específica de los cmdlets de AzureRM:

    Import-Module AzureRM -RequiredVersion 2.1.0**

Versiones antes 2.1.0 (excepto 1.2.6) no funcionan bien en paralelo con otras versiones de módulo de PowerShell de Azure. Al cargar una versión anterior de los módulos de Azure PowerShell utilizando un comando como el anterior, se cargarán versiones incompatibles del módulo **AzureRM.Profile** , por lo que los cmdlets que le pide que inicie sesión en cada vez que ejecute un cmdlet, incluso después de que haya iniciado sesión.

Fuente de la manera más sencilla de resolver que este es para instalar la última Azure PowerShell desde la WebPI o .msi-Esto elimina las versiones anteriores de los módulos instalados desde la galería. 

Tenga en cuenta que módulos de Azure y AzureRM tienen dependencias en común, por lo que si usa ambos módulos, cuando se actualiza uno, debe actualizarlos. Las versiones anteriores del módulo Azure tienen el mismo problema con módulo en paralelo que las versiones anteriores del módulo AzureRM de carga.

<a id="Install"></a>
## <a name="step-1-install"></a>Paso 1: instalar

Siguientes son los dos métodos que puede instalar Azure PowerShell. Puede instalarlo desde la Galería de PowerShell o la WebPI.

###<a name="installing-azure-powershell-from-the-powershell-gallery"></a>Instalar PowerShell Azure desde la Galería de PowerShell

Es la mejor forma de usar la Galería de PowerShell. Necesita el módulo PowerShellGet usar la Galería de PowerShell. Esta opción está disponible aquí: [PowerShellGallery.com](https://www.powershellgallery.com/)

Instalar Azure PowerShell 1.3.0 o superior de la Galería de PowerShell con un mensaje de Windows PowerShell o entorno de Scripting integrado (ISE) de PowerShell elevado mediante los siguientes comandos:

    # Install the Azure Resource Manager modules from the PowerShell Gallery
    Install-Module AzureRM

    # Install the Azure Service Management module from the PowerShell Gallery
    Install-Module Azure

####<a name="more-about-these-commands"></a>Más información sobre estos comandos

- **Módulo de instalación AzureRM** instala un módulo de resumen de los cmdlets del Administrador de recursos de Azure. Depende del módulo AzureRM 
- un rango de la versión específica para cada módulo de administrador de recursos de Azure. El rango de la versión incluyen garantiza que se pueden incluidos sin cambios de módulo importantes al instalar AzureRM módulos con la misma versión principal. Cuando instale el módulo AzureRM, cualquier módulo de administrador de recursos de Azure no se ha instalado anteriormente se descargará e instalada desde la Galería de PowerShell. Para obtener más información sobre el control de versiones semántico utilizan módulos de Azure PowerShell, vea [semver.org](http://semver.org). 
- **Instalar módulo Azure** instala el módulo Azure. Este módulo es el módulo de administración de servicios de Azure PowerShell 0.9.x. Debe tener no principales cambios y ser intercambiables para la versión anterior del módulo Azure.

###<a name="installing-azure-powershell-from-webpi"></a>Instalar PowerShell Azure desde WebPI

Instalación de Azure PowerShell 1.0 y mayor de WebPI es el mismo tal y como era para 0.9.x. Descargar [Azure PowerShell](http://aka.ms/webpi-azps) e inicie la instalación. Si tiene Azure PowerShell 0.9.x instalado, versión 0.9.x que se van a desinstalar como parte de la actualización. Si instaló módulos de PowerShell de Azure desde la Galería de PowerShell, el programa de instalación quita automáticamente los módulos antes de la instalación para garantizar un entorno de PowerShell de Azure coherente.

> [AZURE.NOTE] Si previamente ha instalado módulos Azure desde la Galería de PowerShell, quitará automáticamente el instalador de ellos. Esto evita confusión acerca de qué versiones de módulo que haya instalado y dónde se encuentran. Módulos de galería de PowerShell normalmente se instalarán en **%ProgramFiles%\WindowsPowerShell\Modules**. En cambio, WebPI se instalará los módulos Azure en * *% ProgramFiles (x86) %\Microsoft SDKs\Azure\PowerShell\**. Si se produce un error durante la instalación, puede quitar manualmente el Azure* las carpetas en la carpeta **%ProgramFiles%\WindowsPowerShell\Modules** y pruebe la instalación de nuevo.

Una vez finalizada la instalación, su ```$env:PSModulePath``` configuración debe incluir los directorios que contienen los cmdlets de PowerShell de Azure.

> [AZURE.NOTE] Hay un problema conocido con PowerShell **$env: PSModulePath** que pueden ocurrir al instalar desde WebPI. Si el equipo requiere un reinicio debido a otras instalaciones o actualizaciones del sistema, puede hacer que las actualizaciones **$env: PSModulePath** para no incluir la ruta de acceso donde está instalado Azure PowerShell. Si esto sucede, puede verá un mensaje 'cmdlet no reconoce' al intentar usar cmdlets de PowerShell de Azure tras la instalación o actualización. Si esto sucede, reiniciar el equipo debe solucionar el problema.

Si recibe un mensaje similar a los siguientes al intentar cargar o ejecutar cmdlets:

```
    PS C:\> Get-AzureRmResource
    Get-AzureRmResource : The term 'Get-AzureRmResource' is not recognized as the name of a cmdlet, function,
    script file, or operable program. Check the spelling of the name, or if a path was included, verify that the path is
    correct and try again.
    At line:1 char:1
    + Get-AzureRmResource
    + ~~~~~~~~~~~~~~~~~~~~~~~
        + CategoryInfo          : ObjectNotFound: (get-azurermresourcefork:String) [], CommandNotFoundException
        + FullyQualifiedErrorId : CommandNotFoundException
```

Esto puede corregirse reiniciar el equipo o importando los cmdlets de C:\Program Files\WindowsPowerShell\Modules\Azure\XXXX\ como siguiente (donde XXXX es la versión de PowerShell instalado:

```
Import-Module "C:\Program Files\WindowsPowerShell\Modules\Azure\XXXX\azure.psd1"
Import-Module "C:\Program Files\WindowsPowerShell\Modules\Azure\XXXX\expressroute\expressroute.psd1"
```

## <a name="step-2-start"></a>Paso 2: inicio
Puede ejecutar los cmdlets desde la consola de Windows PowerShell estándar o del entorno de Scripting integrado en PowerShell (ISE).
El método que se usa para abrir cualquier consola depende de la versión de Windows que se está ejecutando:

- En un equipo con al menos Windows 8 o Windows Server 2012, puede usar la búsqueda integrada. Desde la pantalla **Inicio** , comience a escribir power. Devuelve una lista de ámbito de aplicaciones que incluye Windows PowerShell. Para abrir la consola, haga clic en cualquier aplicación. (Para anclar la aplicación a la pantalla de **Inicio** , haga clic en el icono).

- En un equipo que ejecute una versión anterior a Windows 8 o Windows Server 2012, use el **menú Inicio**. En el menú **Inicio** , haga clic en **Todos los programas**, haga clic en **Accesorios**y, a continuación, haga clic en la carpeta de **Windows PowerShell** y, a continuación, haga clic en **Windows PowerShell**.

También puede ejecutar el **Windows PowerShell ISE** para usar elementos de menú y métodos abreviados de teclado para realizar muchas de las tareas que debe realizar en la consola de Windows PowerShell. Para usar el ISE, en la consola de Windows PowerShell, Cmd.exe, o en el cuadro **Ejecutar** , escriba **powershell_ise.exe**.

###<a name="commands-to-help-you-get-started"></a>Comandos para ayudarle a empezar a

    # To make sure the Azure PowerShell module is available after you install
    Get-Module –ListAvailable 
    
    # To log in to Azure Resource Manager
    Login-AzureRmAccount

    # You can also use a specific Tenant if you would like a faster log in experience
    # Login-AzureRmAccount -TenantId xxxx

    # To view all subscriptions for your account
    Get-AzureRmSubscription

    # To select a default subscription for your current session
    Get-AzureRmSubscription –SubscriptionName “your sub” | Select-AzureRmSubscription

    # View your current Azure PowerShell session context
    # This session state is only applicable to the current session and will not affect other sessions
    Get-AzureRmContext

    # To select the default storage context for your current session
    Set-AzureRmCurrentStorageAccount –ResourceGroupName “your resource group” –StorageAccountName “your storage account name”

    # View your current Azure PowerShell session context
    # Note: the CurrentStorageAccount is now set in your session context
    Get-AzureRmContext

    # To list all of the blobs in all of your containers in all of your accounts
    Get-AzureRmStorageAccount | Get-AzureStorageContainer | Get-AzureStorageBlob


## <a name="step-3-connect"></a>Paso 3: conectar
Los cmdlets necesita su suscripción para que puedan administrar sus servicios. Puede comprar una suscripción a Azure si aún no tiene una. Para obtener instrucciones, vea [cómo comprar Azure](http://go.microsoft.com/fwlink/p/?LinkId=320795).

1. Tipo de **Inicio de sesión AzureRmAccount**

2. Escriba la dirección de correo electrónico y contraseña asociada a su cuenta. Azure autentica y guarda la información de credenciales y, a continuación, cierra la ventana.

--O BIEN--

Inicie sesión en su trabajo o escuela cuenta:

    $cred = Get-Credential
    Login-AzureRmAccount -Credential $cred
> [AZURE.NOTE] Si tiene más de un inquilino asociado con su cuenta profesional, especifique el parámetro TenantId:

    $loadersubscription = Get-AzureRmSubscription -SubscriptionName $YourSubscriptionName -TenantId $YourAssociatedSubscriptionTenantId


> [AZURE.NOTE] Este registro no interactivo en método solo funciona con una cuenta profesional o educativa. Una cuenta profesional o educativa es un usuario que es administrado por su trabajo o escuela y definido en la instancia de Azure Active Directory para su trabajo o escuela. Si actualmente no tiene una cuenta profesional o educativa y está utilizando una cuenta de Microsoft para iniciar sesión en su suscripción de Azure, puede crear fácilmente una mediante los siguientes pasos.

> 1. Inicie sesión en el [portal de clásica Azure](https://manage.windowsazure.com)y haga clic en **Active Directory**.

> 2. Si no existe ningún directorio, seleccione **crear su directorio** y proporcione la información solicitada.

> 3. Seleccione el directorio y agregar un nuevo usuario. Este nuevo usuario puede iniciar sesión con una cuenta profesional o educativa. Durante la creación del usuario, se proporcionará con una dirección de correo para el usuario y una contraseña temporal. Guardar esta información, como se utiliza en el paso 5 a continuación.

> 4. Desde el portal de clásico Azure, seleccione **configuración** y, a continuación, seleccione **los administradores**. Seleccione **Agregar**y agregar el nuevo usuario como administrador de compañeros. Esto permite la cuenta profesional o educativa administrar su suscripción de Azure.

> 5. Por último, inicie sesión en el portal de clásico Azure y, a continuación, vuelva a conectarse utilizando el trabajo o cuenta escolar. Si esta es la primera vez que inicia sesión con esta cuenta, se le pedirá que cambie la contraseña.

> Para obtener más información sobre suscribirse en Microsoft Azure con una cuenta profesional o educativa, vea [iniciar sesión en Microsoft Azure como una organización](./active-directory/sign-up-organization.md).

> Para obtener más información sobre la administración de autenticación y la suscripción en Azure, vea [Administrar cuentas, suscripciones y funciones administrativas](http://go.microsoft.com/fwlink/?LinkId=324796).

### <a name="view-account-and-subscription-details"></a>Ver detalles de cuenta y su suscripción

Puede tener varias suscripciones disponibles para Azure PowerShell y cuentas. Puede agregar varias cuentas ejecutando **AzureRmAccount agregar** más de una vez.

Para mostrar las cuentas de Azure disponibles, escriba **Get-AzureAccount**.

Para mostrar las suscripciones de Azure, escriba **Get-AzureRmSubscription**.

##<a id="Help"></a>Obtener ayuda##

Estos recursos proporcionan ayuda para cmdlets específicos:


-   Desde la consola, puede usar el sistema de Ayuda integrado. El cmdlet **Get-Help** proporciona acceso a este sistema. 

- Para obtener ayuda de la Comunidad, pruebe estos foros populares:

 - [Foro de Azure en MSDN]( http://go.microsoft.com/fwlink/p/?LinkId=320212)
 - [Stackoverflow](http://go.microsoft.com/fwlink/?LinkId=320213)

##<a name="learn-more"></a>Aprende más


Consulte los siguientes recursos para obtener más información sobre cómo usar los cmdlets:

Para obtener instrucciones básicas sobre el uso de Windows PowerShell, vea [Usar Windows PowerShell](http://go.microsoft.com/fwlink/p/?LinkId=321939).

Para obtener información de referencia sobre los cmdlets, vea [Referencia de Cmdlet de Azure](https://msdn.microsoft.com/library/windowsazure/jj554330.aspx).

Para que las secuencias de comandos de ejemplo e instrucciones para aprender a utilizar secuencias de comandos para administrar Azure, consulte el [Centro de secuencia de comandos](http://go.microsoft.com/fwlink/p/?LinkId=321940).

