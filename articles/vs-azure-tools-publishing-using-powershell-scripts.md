<properties
   pageTitle="Utilizar secuencias de comandos de Windows PowerShell para publicar en entornos de prueba y desarrollo | Microsoft Azure"
   description="Obtenga información sobre cómo usar las secuencias de comandos de Windows PowerShell desde Visual Studio para publicar en desarrollo y entornos de prueba."
   services="visual-studio-online"
   documentationCenter="na"
   authors="TomArcher"
   manager="douge"
   editor="" />
<tags
   ms.service="multiple"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="multiple"
   ms.date="08/15/2016"
   ms.author="tarcher" />

# <a name="using-windows-powershell-scripts-to-publish-to-dev-and-test-environments"></a>Uso de secuencias de comandos de Windows PowerShell para publicar en desarrollo y entornos de prueba

Cuando se crea una aplicación web en Visual Studio, puede generar un script de Windows PowerShell que puede usar para automatizar la publicación de su sitio Web a Azure como una aplicación Web en la aplicación de servicio de Azure o una máquina virtual más adelante. Puede editar y ampliar la secuencia de comandos de Windows PowerShell en el editor de Visual Studio para satisfacer sus requisitos o integrar el script con compilación existente, prueba y las secuencias de comandos de la publicación.

Con estas secuencias de comandos, puede aprovisionar versiones personalizadas (también conocido como los entornos de prueba y desarrollo) de su sitio para uso temporal. Por ejemplo, puede configurar una versión específica del sitio Web en una máquina virtual Azure o en la franja de ensayo en un sitio Web para ejecutar un conjunto de pruebas, reproducir un error, prueba corregir un error, prueba un cambio propuesto o establecer un entorno personalizado para una demostración o presentación. Después de crear un script que publica un proyecto, puede volver a crear entornos idénticos volviendo a ejecutar la secuencia de comandos según sea necesario o ejecutar la secuencia de comandos con su propia versión de la aplicación web para crear un entorno personalizado para realizar pruebas.

## <a name="what-you-need"></a>Lo que necesita

- Azure SDK 2.3 o posterior. Para obtener más información, vea [Descargas de Visual Studio](http://go.microsoft.com/fwlink/?LinkID=624384) .

No es necesario el SDK de Azure para generar las secuencias de comandos para proyectos web. Esta característica es para proyectos de web, no los roles de web en servicios en la nube.

- Azure PowerShell 0.7.4 o posterior. Para obtener más información, consulte [cómo instalar y configurar Azure PowerShell](powershell-install-configure.md) .

- [Windows PowerShell 3.0](http://go.microsoft.com/?linkid=9811175) o posterior.

## <a name="additional-tools"></a>Herramientas adicionales

Herramientas y recursos para trabajar con PowerShell en Visual Studio para el desarrollo de Azure adicionales están disponibles. Vea [PowerShell Tools para Visual Studio](http://go.microsoft.com/fwlink/?LinkId=404012).

## <a name="generating-the-publish-scripts"></a>Generar scripts de publicación

Puede generar las secuencias de comandos de publicación para una máquina virtual que hospeda su sitio Web cuando se crea un nuevo proyecto siguientes [estas instrucciones](./virtual-machines/virtual-machines-windows-classic-web-app-visual-studio.md). También puede [Generar publicar secuencias de comandos para aplicaciones web de servicio de la aplicación de Azure](./app-service-web/web-sites-dotnet-get-started.md).

## <a name="scripts-that-visual-studio-generates"></a>Secuencias de comandos que genera Visual Studio

Visual Studio genera una carpeta de nivel de la solución denominada **PublishScripts** que contiene dos archivos de Windows PowerShell, un script de publicación para el sitio Web o máquina virtual y un módulo que contiene funciones que puede usar en las secuencias de comandos. Visual Studio también genera un archivo con el formato JSON que especifica los detalles del proyecto que está implementando.

### <a name="windows-powershell-publish-script"></a>Windows PowerShell publicar script

La secuencia de comandos de publicar contiene pasos de publicar específicos para implementar un sitio Web o una máquina virtual. Visual Studio proporciona una sintaxis colorear para el desarrollo de Windows PowerShell. Ayuda para las funciones está disponible y puede editar libremente las funciones de la secuencia de comandos para satisfacer sus requisitos cambiantes.

### <a name="windows-powershell-module"></a>Módulo de Windows PowerShell

El módulo de Windows PowerShell que Visual Studio genera contiene funciones que usa la secuencia de comandos de la publicación. Estas son las funciones de PowerShell de Azure y no están pensadas para modificarse. Para obtener más información, consulte [cómo instalar y configurar Azure PowerShell](powershell-install-configure.md) .

### <a name="json-configuration-file"></a>Archivo de configuración de JSON

El archivo JSON se crea en la carpeta de **configuraciones** y contiene datos de configuración que especifica exactamente qué recursos para implementar Azure. El nombre del archivo que Visual Studio genera es proyecto-nombre-WAWS-dev.json si ha creado un sitio Web o el nombre de proyecto-VM-dev.json si ha creado una máquina virtual. Aquí es un ejemplo de un archivo de configuración de JSON que se genera cuando se crea un sitio Web. La mayoría de los valores no necesitan explicación. El nombre del sitio Web se genera por Azure, por lo que podría no coincidir con el nombre del proyecto.

```
{
"environmentSettings": {
"webSite": {
"name": "WebApplication26632",
"location": "West US"
},
"databases": [
{
"connectionStringName": "DefaultConnection",
"databaseName": "WebApplication26632_db",
"serverName": "YourDatabaseServerName",
"user": "sqluser2",
"password": "",
"edition": "",
"size": "",
"collation": "",
"location": "West US"
}
]
}
}
```
Cuando se crea una máquina virtual, el archivo de configuración de JSON tiene un aspecto similar al siguiente. Tenga en cuenta que se crea un servicio de nube como un contenedor de la máquina virtual. La máquina virtual contiene los extremos habituales para web access a través de HTTP y HTTPS, así como los extremos para implementar la Web, que le permite publicar en el sitio Web de su equipo local, escritorio remoto y Windows PowerShell.

```
{
"environmentSettings": {
"cloudService": {
"name": "myusernamevm1",
"affinityGroup": "",
"location": "West US",
"virtualNetwork": "",
"subnet": "",
"availabilitySet": "",
"virtualMachine": {
"name": "myusernamevm1",
"vhdImage": "a699494373c04fc0bc8f2bb1389d6106__Win2K8R2SP1-Datacenter-201403.01-en.us-127GB.vhd",
"size": "Small",
"user": "vmuser1",
"password": "",
"enableWebDeployExtension": true,
"endpoints": [
{
"name": "Http",
"protocol": "TCP",
"publicPort": "80",
"privatePort": "80"
},
{
"name": "Https",
"protocol": "TCP",
"publicPort": "443",
"privatePort": "443"
},
{
"name": "WebDeploy",
"protocol": "TCP",
"publicPort": "8172",
"privatePort": "8172"
},
{
"name": "Remote Desktop",
"protocol": "TCP",
"publicPort": "3389",
"privatePort": "3389"
},
{
"name": "Powershell",
"protocol": "TCP",
"publicPort": "5986",
"privatePort": "5986"
}
]
}
},
"databases": [
{
"connectionStringName": "",
"databaseName": "",
"serverName": "",
"user": "",
"password": ""
}
],
"webDeployParameters": {
"iisWebApplicationName": "Default Web Site"
}
}
}
```

Puede editar la configuración de JSON para cambiar lo que sucede cuando se ejecutan las secuencias de comandos de la publicación. La `cloudService` y `virtualMachine` secciones son necesarias, pero puede eliminar el `databases` sección si ya no lo necesita. Las propiedades que están vacías en el archivo de configuración predeterminado que Visual Studio genera son opcionales; aquellos que tienen valores en el archivo de configuración predeterminada son necesarios.

Si tiene un sitio Web que tiene varios entornos de implementación (denominados ranuras) en lugar de un único sitio de producción en Azure, puede incluir el nombre del buzón en el nombre del sitio Web en el archivo de configuración de JSON. Por ejemplo, si tiene un sitio Web que tiene con el nombre de **Mi sitio** y un espacio para él denominado **probar** a continuación, el URI es test.cloudapp.net de Mi sitio, pero el nombre correcto para usar en el archivo de configuración es mysite(test). Sólo puede hacerlo si el sitio Web y ranuras ya existen en la suscripción. Si no existen, crear el sitio Web ejecutando el script sin especificar la franja y crear la franja en el [portal de clásico de Azure](http://go.microsoft.com/fwlink/?LinkID=213885)y después ejecutar la secuencia de comandos con el nombre del sitio Web modificada. Para obtener más información acerca de posiciones de implementación para aplicaciones web, vea [Configurar el ensayo entornos de web apps en el servicio de aplicación de Azure](./app-service-web/web-sites-staged-publishing.md).

## <a name="how-to-run-the-publish-scripts"></a>Cómo ejecutar las secuencias de comandos de publicación

Si nunca ha ejecutado una secuencia de comandos de Windows PowerShell antes, primero debe establecer la directiva de ejecución para habilitar la ejecución de scripts. Esta es una característica de seguridad para evitar que los usuarios ejecuten secuencias de comandos de Windows PowerShell si está expuestos a malware o virus que implican ejecutando secuencias de comandos.

### <a name="run-the-script"></a>Ejecutar la secuencia de comandos

1. Crear el paquete Web implementar para el proyecto. Un paquete de implementación Web es un archivo comprimido (archivo .zip) que contienen los archivos que desea copiar a su sitio Web o una máquina virtual. Puede crear paquetes Web implementar en Visual Studio para cualquier aplicación web.

![Crear Web implementar paquete](./media/vs-azure-tools-publishing-using-powershell-scripts/IC767885.png)

Para obtener más información, vea [Cómo: crear un paquete de implementación Web en Visual Studio](https://msdn.microsoft.com/library/dd465323.aspx). También puede automatizar la creación de su paquete Web implementar, como se describe en la sección **Personalizar y ampliar las secuencias de comandos de publicar** más adelante en este tema.

1. En el **Explorador de soluciones**, abra el menú contextual de la secuencia de comandos y, a continuación, elija **Abrir con PowerShell ISE**.

1. Si esta es la primera vez que se ha ejecutado secuencias de comandos de Windows PowerShell en este equipo, abra una ventana de símbolo del sistema con privilegios de administrador y escriba el siguiente comando:

`Set-ExecutionPolicy RemoteSigned`

1. Inicie sesión con el comando siguiente en Azure.

`Add-AzureAccount`

Cuando se le solicite, proporcione el nombre de usuario y la contraseña.

Tenga en cuenta que, al automatizar la secuencia de comandos, este método de proporcionar credenciales Azure no funcionará. En su lugar, debe utilizar el archivo .publishsettings para proporcionar credenciales. Una vez, que use el comando **Get-AzurePublishSettingsFile** para descargar el archivo de Azure y usa posteriormente **AzurePublishSettingsFile de importación** para importar el archivo. Para obtener instrucciones detalladas, consulte [cómo instalar y configurar Azure PowerShell](powershell-install-configure.md).

1. (Opcional) Si desea crear recursos Azure como la máquina virtual, la base de datos y el sitio Web sin tener que publicar su aplicación web, utilice el comando **Publicar WebApplication.ps1** con el **-configuración** argumento establecido en el archivo de configuración de JSON. Esta línea de comandos usa el archivo de configuración de JSON para determinar qué recursos para crear. Porque utiliza la configuración predeterminada para el resto de los argumentos de línea de comandos, crea los recursos, pero no publicar su aplicación web. – Detallado opción le proporciona más información sobre lo que sucede.

`Publish-WebApplication.ps1 -Verbose –Configuration C:\Path\WebProject-WAWS-dev.json`

1. Use el comando **Publicar WebApplication.ps1** tal como se muestra en uno de los ejemplos siguientes para llamar a la secuencia de comandos y publicar su aplicación web. Si necesita invalidar la configuración predeterminada para cualquiera de los argumentos, como el nombre de la suscripción, publicar el nombre del paquete, credenciales de máquina virtual o credenciales de servidor de base de datos, puede especificar los parámetros. Usar la **– detallado** opción para obtener más información sobre el progreso del proceso de publicación.

```
Publish-WebApplication.ps1 –Configuration C:\Path\WebProject-WAWS-dev-json `
–SubscriptionName Contoso `
-WebDeployPackage C:\Documents\Azure\ADWebApp.zip `
-DatabaseServerPassword @{Name="dbServerName";Password="adminPassword"} `
-Verbose
```

Si está creando una máquina virtual, el comando es similar a la siguiente. Este ejemplo también muestra cómo especificar las credenciales de varias bases de datos. Para los equipos virtuales que crean estas secuencias de comandos, el certificado SSL no es de una entidad emisora de raíz de confianza. Por lo tanto, debe usar la opción **-AllowUntrusted** .

```
Publish-WebApplication.ps1 `
-Configuration C:\Path\ADVM-VM-test.json `
-SubscriptionName Contoso `
-WebDeployPackage C:\Path\ADVM.zip `
-AllowUntrusted `
-VMPassword @{name = "vmUserName"; password = "YourPasswordHere"} `
-DatabaseServerPassword @{Name="server1";Password="adminPassword1"}, @{Name="server2";Password="adminPassword2"} `
-Verbose
```

La secuencia de comandos puede crear bases de datos, pero no servidores de base de datos. Si desea crear un servidor de base de datos, puede usar la función **AzureSqlDatabaseServer de nuevo** en el módulo Azure.

## <a name="customizing-and-extending-the-publish-scripts"></a>Personalizar y ampliar las secuencias de comandos de publicación

Puede personalizar la secuencia de comandos de publicación y el archivo de configuración de JSON. Las funciones en el módulo Windows PowerShell **AzureWebAppPublishModule.psm1** no están diseñadas para modificarse. Si desea especificar una base de datos diferente o cambiar algunas de las propiedades de la máquina virtual, editar el archivo de configuración de JSON. Si desea ampliar la funcionalidad de la secuencia de comandos para automatizar la creación y probar el proyecto, puede implementar código auxiliar de la función de **Publicar WebApplication.ps1**.

Para automatizar la creación de su proyecto, agregue el código que llame MSBuild para `New-WebDeployPackage` como se muestra en este ejemplo de código. La ruta de acceso al comando MSBuild es diferente según la versión de Visual Studio que ha instalado. Para obtener la ruta de acceso correcta, puede usar la función **Get-MSBuildCmd**, tal como se muestra en este ejemplo.

### <a name="to-automate-building-your-project"></a>Para automatizar la creación de un proyecto

1. Agregar la `$ProjectFile` parámetro en la sección de parámetro global.

```
[Parameter(Mandatory = $false)]
  [ValidateScript({Test-Path $_ -PathType Leaf})]
  [String]
  $ProjectFile,
```

1. Copiar la función `Get-MSBuildCmd` en un archivo de script.

```
function Get-MSBuildCmd
{
        process
{

             $path =  Get-ChildItem "HKLM:\SOFTWARE\Microsoft\MSBuild\ToolsVersions\" |
                                   Sort-Object {[double]$_.PSChildName} -Descending |
                                   Select-Object -First 1 |
                                   Get-ItemProperty -Name MSBuildToolsPath |
                                   Select -ExpandProperty MSBuildToolsPath
       
            $path = (Join-Path -Path $path -ChildPath 'msbuild.exe')

        return Get-Item $path
    }
}
```

1. Reemplazar `New-WebDeployPackage` con el siguiente código y reemplace los marcadores de posición en la línea de construcción `$msbuildCmd`. Este código es de Visual Studio de 2015. Si está usando 2013 de Visual Studio, cambie la propiedad de **VisualStudioVersion** debajo para `12.0`.

```
function New-WebDeployPackage
{
    #Write a function to build and package your web application
      
#To build your web application, use MsBuild.exe. For help, see MSBuild Command-Line Reference at: http://go.microsoft.com/fwlink/?LinkId=391339
      
Write-VerboseWithTime 'Build-WebDeployPackage: Start'
      
$msbuildCmd = '"{0}" "{1}" /T:Rebuild;Package /P:VisualStudioVersion=14.0 /p:OutputPath="{2}\MSBuildOutputPath" /flp:logfile=msbuild.log,v=d' -f (Get-MSBuildCmd), $ProjectFile, $scriptDirectory
      
Write-VerboseWithTime ('Build-WebDeployPackage: ' + $msbuildCmd)
      
#Start execution of the build command
$job = Start-Process cmd.exe -ArgumentList('/C "' + $msbuildCmd + '"') -WindowStyle Normal -Wait -PassThru
      
if ($job.ExitCode -ne 0)
{
throw('MsBuild exited with an error. ExitCode:' + $job.ExitCode)
}

#Obtain the project name
$projectName = (Get-Item $ProjectFile).BaseName
      
#Construct the path to web deploy zip package
$DeployPackageDir =  '.\MSBuildOutputPath\_PublishedWebsites\{0}_Package\{0}.zip' -f $projectName
      
      
#Get the full path for the web deploy zip package. This is required for MSDeploy to work
$WebDeployPackage = Resolve-Path –LiteralPath $DeployPackageDir
      
Write-VerboseWithTime 'Build-WebDeployPackage: End'
      
return $WebDeployPackage
}
```

1. Llamar a la `New-WebDeployPackage` función antes de esta línea: `$Config = Read-ConfigFile $Configuration` para las aplicaciones web o `$Config = Read-ConfigFile $Configuration -HasWebDeployPackage:([Bool]$WebDeployPackage)` para máquinas virtuales.

```
if($ProjectFile)
{
$WebDeployPackage = New-WebDeployPackage
}
```

1. Invocar el script personalizado de línea de comandos con pasar el `$Project` argumento, como se muestra en la línea de comandos de ejemplo siguientes.

```
.\Publish-WebApplicationVM.ps1 -Configuration .\Configurations\WebApplication5-VM-dev.json `
-ProjectFile ..\WebApplication5\WebApplication5.csproj `
-VMPassword @{Name="VMUser";Password="Test.123"} `
-AllowUntrusted `
-Verbose
```

Para automatizar las pruebas de la aplicación, agregue el código para `Test-WebApplication`. No olvide quite los comentarios de las líneas de **Publicar WebApplication.ps1** donde se denominan estas funciones. Si no proporciona una implementación, puede crear manualmente su proyecto con Visual Studio y, a continuación, ejecute el script de publicar para publicar en Azure.

## <a name="publishing-function-summary"></a>Resumen de la función de publicación

Para obtener ayuda para funciones que puede utilizar en el símbolo del sistema de Windows PowerShell, use el comando `Get-Help function-name`. La Ayuda incluye ejemplos y ayuda de parámetros. También es el mismo texto de ayuda en los archivos de origen de secuencia de comandos, **AzureWebAppPublishModule.psm1** y **WebApplication.ps1 publicar**. La secuencia de comandos y ayuda está localizada en el idioma de Visual Studio.

**AzureWebAppPublishModule**

|Nombre de función|Descripción|
|---|---|
|AzureSQLDatabase agregar|Crea una nueva base de datos de SQL Azure.|
|AzureSQLDatabases agregar|Crea bases de datos de SQL Azure de los valores en el archivo de configuración JSON que Visual Studio genera.|
|AzureVM agregar|Crea una máquina virtual Azure y devuelve la dirección URL de la máquina virtual de implementada. La función configura los requisitos previos y, a continuación, llama a la función de **Nuevo AzureVM** (módulo Azure) para crear una nueva máquina virtual.|
|AzureVMEndpoints agregar|Agrega nuevos extremos de entrada a una máquina virtual y devuelve la máquina virtual con el nuevo extremo.|
|AzureVMStorage agregar|Crea una nueva cuenta de almacenamiento de Azure en la suscripción actual. El nombre de la cuenta comienza con "devtest" seguido de una cadena alfanumérica única. La función devuelve el nombre de la nueva cuenta de almacenamiento. Debe especificar una ubicación o un grupo de afinidad para la nueva cuenta de almacenamiento.|
|AzureWebsite agregar|Crea un sitio Web con el nombre y ubicación especificados. Esta función llama a la función **AzureWebsite de nuevo** en el módulo Azure. Si la suscripción ya no incluye un sitio Web con el nombre especificado, esta función crea el sitio Web y devuelve un objeto de sitio Web. En caso contrario, devuelve `$null`.|
|Suscripción de copia de seguridad|Guarda la suscripción actual de Azure en la `$Script:originalSubscription` variable en el ámbito de la secuencia de comandos. Esta función guarda la suscripción de Azure actual (como obtenido por `Get-AzureSubscription -Current`) y su cuenta de almacenamiento y la suscripción que se ha cambiado por esta secuencia (almacenado en la variable `$UserSpecifiedSubscription`) y su cuenta de almacenamiento, en el ámbito de la secuencia de comandos. Guardando los valores, puede usar una función, como por ejemplo `Restore-Subscription`, para restaurar la cuenta de almacenamiento y de suscripción actual original actual si ha cambiado el estado actual.|
|Buscar AzureVM|Obtiene la máquina virtual de Azure especificada.|
|Formato DevTestMessageWithTime|Marca la fecha y hora a un mensaje. Esta función está diseñada para los mensajes que se escriben en las secuencias de Error y detallado.|
|Get-AzureSQLDatabaseConnectionString|Reúne una cadena de conexión para conectarse a una base de datos de SQL Azure.|
|Get-AzureVMStorage|Devuelve el nombre de la primera cuenta de almacenamiento con el modelo de nombre "devtest*" (no distingue mayúsculas de minúsculas) en la ubicación especificada o grupo afinidad. Si el "devtest*" cuenta de almacenamiento no coincide con la ubicación o el grupo afinidad, la función pasa por alto. Debe especificar una ubicación o un grupo de afinidad.|
|Get-MSDeployCmd|Devuelve un comando para ejecutar la herramienta MsDeploy.exe.|
|Nueva AzureVMEnvironment|Busca o crea una máquina virtual en la suscripción que coincida con los valores en el archivo de configuración de JSON.|
|WebPackage publicar|Usos MsDeploy.exe y un sitio web publican un paquete. Archivo ZIP para implementar recursos a un sitio Web. Esta función no genera ningún resultado. Si se produce un error en la llamada a MSDeploy.exe, la función produce una excepción. Para obtener resultados más detallados, utilice la **-detallado** opción.|
|WebPackageToVM publicar|Comprueba los valores de parámetro y, a continuación, llama a la función de **Publicar WebPackage** .|
|Lectura ConfigFile|Valide el archivo de configuración de JSON y devuelve una tabla hash de valores seleccionados.|
|Restaurar la suscripción|Restablecer la suscripción actual a la suscripción original.|
|AzureModule de prueba|Devuelve `$true` si la versión del módulo Azure instalado es 0.7.4 o posterior. Devuelve `$false` si el módulo no está instalado o una versión anterior. Esta función no tiene parámetros.|
|AzureModuleVersion de prueba|Devuelve `$true` si la versión del módulo Azure es 0.7.4 o posterior. Devuelve `$false` si el módulo no está instalado o una versión anterior. Esta función no tiene parámetros.|
|HttpsUrl de prueba|Convierte la URL de entrada a un objeto System.Uri. Devuelve `$True` si la dirección URL es absoluta y su esquema es https. Devuelve `$false` si la dirección URL es relativa, su esquema no HTTPS o no se puede convertir la cadena de entrada a una dirección URL.|
|Miembro de prueba|Devuelve `$true` si una propiedad o método es un miembro del objeto. En caso contrario, devuelve `$false`.|
|ErrorWithTime de escritura|Escribe un mensaje de error con el prefijo con la hora actual. Esta función llama a la función de **Formato DevTestMessageWithTime** para anteponga el tiempo antes de escribir el mensaje en la secuencia de Error.|
|HostWithTime de escritura|Escribe un mensaje en el programa de host (**Host de escritura**) con el prefijo con la hora actual. El efecto de escribir el programa host varía. Mayoría de los programas que hospedan Windows PowerShell escribe estos mensajes de salida estándar.|
|VerboseWithTime de escritura|Escribe un mensaje detallado el prefijo con la hora actual. Dado que llama **Detallado de escritura**, el mensaje muestra solo cuando se ejecuta la secuencia de comandos con el parámetro **detallado** o cuando se establece la preferencia de **VerbosePreference** para **continuar**.|

**Aplicación de Web publicar**

|Nombre de función|Descripción|
|---|---|
|Nueva AzureWebApplicationEnvironment|Crea recursos Azure, como un sitio Web o una máquina virtual.|
|Nueva WebDeployPackage|Esta función no está implementada. Puede agregar comandos en esta función para generar el proyecto.|
|AzureWebApplication publicar|Publica una aplicación web para Azure.|
|Aplicación de Web publicar|Crea e implementa Web Apps, máquinas virtuales, bases de datos SQL y cuentas de almacenamiento para un proyecto de Visual Studio web.|
|Aplicación Web de prueba|Esta función no está implementada. Puede agregar comandos en esta función para probar la aplicación.|

## <a name="next-steps"></a>Pasos siguientes

Más información acerca de los scripts de PowerShell lea [Scripting con Windows PowerShell](https://technet.microsoft.com/library/bb978526.aspx) y ver otras secuencias de comandos de PowerShell de Azure en el [Centro de secuencia de comandos](https://azure.microsoft.com/documentation/scripts/).
