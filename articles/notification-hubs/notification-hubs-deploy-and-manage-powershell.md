<properties 
    pageTitle="Implementar y administrar notificación Hubs con PowerShell" 
    description="Cómo crear y administrar con PowerShell para la automatización de Hubs de notificación" 
    services="notification-hubs" 
    documentationCenter="" 
    authors="ysxu" 
    manager="erikre" 
    editor="" />

<tags 
    ms.service="notification-hubs" 
    ms.workload="mobile" 
    ms.tgt_pltfrm="powershell" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="06/29/2016" 
    ms.author="yuaxu"/>

# <a name="deploy-and-manage-notification-hubs-using-powershell"></a>Implementar y administrar notificación Hubs con PowerShell

##<a name="overview"></a>Información general

En este artículo se muestra cómo usar crear y administrar Azure notificación Hubs con PowerShell. En este tema se muestran las siguientes tareas comunes de automatización.

+ Crear un concentrador de notificación
+ Establecer credenciales

Si también necesita crear un nuevo espacio de nombres de bus de servicio para su hubs de notificación, vea [Administrar Bus de servicio con PowerShell](../service-bus-messaging/service-bus-powershell-how-to-provision.md).

Administrar notificaciones Hubs no es compatible directamente con los cmdlets que se incluyen con PowerShell de Azure. El mejor método de PowerShell es hacer referencia al ensamblado Microsoft.Azure.NotificationHubs.dll. El ensamblado se distribuye con el [paquete de Microsoft Azure notificación Hubs NuGet](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/).


## <a name="prerequisites"></a>Requisitos previos

Antes de comenzar este artículo, debe tener el siguiente:

- Una suscripción de Azure. Azure es una plataforma de suscripción. Para obtener más información acerca de cómo obtener una suscripción, vea [Opciones de compra], [Miembro ofrece]o [Versión de prueba gratuita].

- Un equipo con PowerShell de Azure. Para obtener instrucciones, consulte [instalar y configurar Azure PowerShell].

- Una descripción general de secuencias de comandos de PowerShell, paquetes de NuGet y .NET Framework.


## <a name="including-a-reference-to-the-net-assembly-for-service-bus"></a>Incluir una referencia al ensamblado .NET para Bus de servicio

Administrar Hubs de notificación de Azure aún no está incluido con los cmdlets de PowerShell en Azure PowerShell. Para aprovisionar hubs de notificación, puede usar al cliente de .NET proporcionado en el [paquete de Microsoft Azure notificación Hubs NuGet](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/).

En primer lugar, asegúrese de que la secuencia de comandos puede localizar el ensamblado **Microsoft.Azure.NotificationHubs.dll** , que se instala como un paquete de NuGet en un proyecto de Visual Studio. Para ser flexible, la secuencia de comandos realiza estos pasos:

1. Determina la ruta de acceso a la que se invoca.
2. Recorre la ruta de acceso hasta que encuentre una carpeta denominada `packages`. Esta carpeta se crea al instalar paquetes de NuGet para proyectos de Visual Studio.
3. Búsquedas de forma recursiva la `packages` carpeta para un ensamblado denominado **Microsoft.Azure.NotificationHubs.dll**.
4. Hace referencia al ensamblado para que los tipos están disponibles para su uso posterior.

Le mostramos cómo se implementan estos pasos en una secuencia de comandos de PowerShell:

``` powershell

try
{
    # WARNING: Make sure to reference the latest version of Microsoft.Azure.NotificationHubs.dll
    Write-Output "Adding the [Microsoft.Azure.NotificationHubs.dll] assembly to the script..."
    $scriptPath = Split-Path (Get-Variable MyInvocation -Scope 0).Value.MyCommand.Path
    $packagesFolder = (Split-Path $scriptPath -Parent) + "\packages"
    $assembly = Get-ChildItem $packagesFolder -Include "Microsoft.Azure.NotificationHubs.dll" -Recurse
    Add-Type -Path $assembly.FullName

    Write-Output "The [Microsoft.Azure.NotificationHubs.dll] assembly has been successfully added to the script."
}

catch [System.Exception]
{
    Write-Error("Could not add the Microsoft.Azure.NotificationHubs.dll assembly to the script. Make sure you build the solution before running the provisioning script.")
}
```

## <a name="create-the-namespacemanager-class"></a>Crear la clase NamespaceManager

Para aprovisionar Hubs de notificación, crear una instancia de la clase [NamespaceManager](https://msdn.microsoft.com/library/azure/microsoft.azure.notificationhubs.namespacemanager.aspx) desde el SDK. 

Puede usar el cmdlet [Get-AzureSBAuthorizationRule] incluido con Azure PowerShell para recuperar una regla de autorización que se utiliza para proporcionar una cadena de conexión. Se deberá almacenar una referencia a la `NamespaceManager` de instancia en el `$NamespaceManager` variable. Usaremos `$NamespaceManager` proporcionando un concentrador de notificación.

``` powershell
$sbr = Get-AzureSBAuthorizationRule -Namespace $Namespace
# Create the NamespaceManager object to create the hub
Write-Output "Creating a NamespaceManager object for the [$Namespace] namespace..."
$NamespaceManager=[Microsoft.Azure.NotificationHubs.NamespaceManager]::CreateFromConnectionString($sbr.ConnectionString);
Write-Output "NamespaceManager object for the [$Namespace] namespace has been successfully created."
```


## <a name="provisioning-a-new-notification-hub"></a>Un nuevo concentrador de notificación de aprovisionamiento 

Para crear un nuevo concentrador de notificación, use la [API de .NET para Hubs de notificación].

En esta parte de la secuencia de comandos configurar cuatro variables locales. 

1. `$Namespace`: Establecer esto en el nombre del espacio de nombres en la desea crear un concentrador de notificación.
2. `$Path`: Establecer esta ruta de acceso en el nombre del nuevo concentrador de notificación.  Por ejemplo, "MyHub".    
3. `$WnsPackageSid`: Establezca el paquete SID para la aplicación de Windows desde el [Centro de desarrollo de Windows](http://go.microsoft.com/fwlink/p/?linkid=266582&clcid=0x409).
4. `$WnsSecretkey`: Establezca la clave para la aplicación de Windows desde el [Centro de desarrollo de Windows](http://go.microsoft.com/fwlink/p/?linkid=266582&clcid=0x409).

Estas variables se usan para conectarse a su espacio de nombres y crear un nuevo concentrador de notificación configurado para controlar las notificaciones de servicios de notificación de Windows (WNS) con credenciales WNS para una aplicación de Windows. Para obtener información acerca de cómo obtener el paquete SID y clave secreta ver, el tutorial de [Introducción con Hubs de notificación](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md) . 

+ El fragmento de secuencia de comandos utiliza el `NamespaceManager` objeto para comprobar si el concentrador de notificación identificada por `$Path` existe.

+ Si no existe, la secuencia de comandos creará un `NotificationHubDescription` con WNS credenciales y que se va a pasar el `NamespaceManager` clase `CreateNotificationHub` método.

``` powershell

$Namespace = "<Enter your namespace>"
$Path  = "<Enter a name for your notification hub>"
$WnsPackageSid = "<your package sid>"
$WnsSecretkey = "<enter your secret key>"

$WnsCredential = New-Object -TypeName Microsoft.Azure.NotificationHubs.WnsCredential -ArgumentList $WnsPackageSid,$WnsSecretkey

# Query the namespace
$CurrentNamespace = Get-AzureSBNamespace -Name $Namespace

# Check if the namespace already exists
if ($CurrentNamespace)
{
    Write-Output "The namespace [$Namespace] in the [$($CurrentNamespace.Region)] region was found."

    # Create the NamespaceManager object used to create a new notification hub
    $sbr = Get-AzureSBAuthorizationRule -Namespace $Namespace
    Write-Output "Creating a NamespaceManager object for the [$Namespace] namespace..."
    $NamespaceManager = [Microsoft.Azure.NotificationHubs.NamespaceManager]::CreateFromConnectionString($sbr.ConnectionString);
    Write-Output "NamespaceManager object for the [$Namespace] namespace has been successfully created."

    # Check to see if the Notification Hub already exists
    if ($NamespaceManager.NotificationHubExists($Path))
    {
        Write-Output "The [$Path] notification hub already exists in the [$Namespace] namespace."  
    }
    else
    {
        Write-Output "Creating the [$Path] notification hub in the [$Namespace] namespace."
        $NHDescription = New-Object -TypeName Microsoft.Azure.NotificationHubs.NotificationHubDescription -ArgumentList $Path;
        $NHDescription.WnsCredential = $WnsCredential;
        $NamespaceManager.CreateNotificationHub($NHDescription);
        Write-Output "The [$Path] notification hub was created in the [$Namespace] namespace."
    }
}
else
{
    Write-Host "The [$Namespace] namespace does not exist."
}
```




## <a name="additional-resources"></a>Recursos adicionales

- [Administrar Bus de servicio con PowerShell](../service-bus-messaging/service-bus-powershell-how-to-provision.md)
- [Cómo crear colas, temas y suscripciones utilizando una secuencia de comandos de PowerShell de Bus de servicio](http://blogs.msdn.com/b/paolos/archive/2014/12/02/how-to-create-a-service-bus-queues-topics-and-subscriptions-using-a-powershell-script.aspx)
- [Cómo crear un Namespace de Bus de servicio y un concentrador de evento con un script de PowerShell](http://blogs.msdn.com/b/paolos/archive/2014/12/01/how-to-create-a-service-bus-namespace-and-an-event-hub-using-a-powershell-script.aspx)

Algunas listos para usar secuencias de comandos también están disponibles para su descarga:
- [Secuencias de comandos de PowerShell de Bus de servicio](https://code.msdn.microsoft.com/windowsazure/Service-Bus-PowerShell-a46b7059)
 

[Opciones de compra]: http://azure.microsoft.com/pricing/purchase-options/
[Ofertas de miembro]: http://azure.microsoft.com/pricing/member-offers/
[Versión de prueba gratuita]: http://azure.microsoft.com/pricing/free-trial/
[Instalar y configurar PowerShell de Azure]: ../powershell-install-configure.md
[API de .NET para Hubs de notificación]: https://msdn.microsoft.com/library/azure/mt414893.aspx
[Get-AzureSBNamespace]: https://msdn.microsoft.com/library/azure/dn495122.aspx
[New-AzureSBNamespace]: https://msdn.microsoft.com/library/azure/dn495165.aspx
[Get-AzureSBAuthorizationRule]: https://msdn.microsoft.com/library/azure/dn495113.aspx
 
