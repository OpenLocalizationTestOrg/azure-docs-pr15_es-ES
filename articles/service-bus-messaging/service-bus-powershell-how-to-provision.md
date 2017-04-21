<properties
    pageTitle="Administrar Bus de servicio con PowerShell | Microsoft Azure"
    description="Administrar Bus de servicio con las secuencias de comandos de PowerShell"
    services="service-bus"
    documentationCenter=".net"
    authors="sethmanheim"
    manager="timlt"
    editor=""/>

<tags
    ms.service="service-bus"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/03/2016"
    ms.author="sethm"/>

# <a name="manage-service-bus-with-powershell"></a>Administrar Bus de servicio con PowerShell

## <a name="overview"></a>Información general

Microsoft Azure PowerShell es un entorno de secuencias de comandos que puede usar para controlar y automatizar la implementación y administración de las cargas de trabajo en Azure. En este artículo se describe cómo usar PowerShell para aprovisionar y administrar entidades de Bus de servicios como espacios de nombres, las colas y Hubs de evento con consola Azure PowerShell local.

## <a name="prerequisites"></a>Requisitos previos

Antes de comenzar este artículo, debe tener los siguientes requisitos previos:

- Una suscripción de Azure. Azure es una plataforma de suscripción. Para obtener más información acerca de cómo obtener una suscripción, vea [Opciones de compra][], [Miembro ofrece][]o [Versión de prueba gratuita][].

- Un equipo con PowerShell de Azure. Para obtener instrucciones, consulte [instalar y configurar Azure PowerShell][].

- Una descripción general de secuencias de comandos de PowerShell, paquetes de NuGet y .NET Framework.

## <a name="including-a-reference-to-the-net-assembly-for-service-bus"></a>Incluir una referencia al ensamblado .NET para Bus de servicio

Hay un número limitado de cmdlets de PowerShell para administrar Bus de servicio. Para aprovisionar entidades que no se exponen a través de los cmdlets existentes, puede usar al cliente de .NET para Bus de servicio en el [paquete NuGet Bus de servicio][].

En primer lugar, asegúrese de que la secuencia de comandos puede localizar el ensamblado **Microsoft.ServiceBus.dll** , que se instala con el paquete de NuGet. Para ser flexible, la secuencia de comandos realiza estos pasos:

1. Determina la ruta de acceso a la que se invoca.
2. Recorre la ruta de acceso hasta que encuentre una carpeta denominada `packages`. Esta carpeta se crea al instalar paquetes de NuGet.
3. Búsquedas de forma recursiva la `packages` carpeta para un ensamblado denominado **Microsoft.ServiceBus.dll**.
4. Hace referencia al ensamblado para que los tipos están disponibles para su uso posterior.

Le mostramos cómo se implementan estos pasos en una secuencia de comandos de PowerShell:

```
try
{
    # WARNING: Make sure to reference the latest version of Microsoft.ServiceBus.dll
    Write-Output "Adding the [Microsoft.ServiceBus.dll] assembly to the script..."
    $scriptPath = Split-Path (Get-Variable MyInvocation -Scope 0).Value.MyCommand.Path
    $packagesFolder = (Split-Path $scriptPath -Parent) + "\packages"
    $assembly = Get-ChildItem $packagesFolder -Include "Microsoft.ServiceBus.dll" -Recurse
    Add-Type -Path $assembly.FullName

    Write-Output "The [Microsoft.ServiceBus.dll] assembly has been successfully added to the script."
}

catch [System.Exception]
{
    Write-Error "Could not add the Microsoft.ServiceBus.dll assembly to the script. Make sure you build the solution before running the provisioning script."
}
```

## <a name="provision-a-service-bus-namespace"></a>Aprovisionar a un espacio de nombres de Bus de servicio

Operaciones de espacio de nombres de Bus de servicio de soporte técnico de dos cmdlets de PowerShell. En lugar de las API de .NET SDK, puede usar [Get-AzureSBNamespace][] y [AzureSBNamespace de nuevo][].

Este ejemplo crea unas cuantas variables locales en la secuencia de comandos; `$Namespace` and `$Location`.

- `$Namespace`es el nombre del espacio de nombres de Bus de servicio con el que desea trabajar.
- `$Location`identifica el centro de datos en el que la secuencia de comandos aprovisiona el espacio de nombres.
- `$CurrentNamespace`almacena el espacio de nombres de referencia que la secuencia de comandos recupera (o crea).

En una secuencia de comandos real, `$Namespace` y `$Location` se pueden pasar como parámetros.

Este elemento de la secuencia de comandos realiza las siguientes tareas:

1. Intenta recuperar un espacio de nombres de Bus de servicio con el nombre proporcionado.
2. Si se encuentra el espacio de nombres, informes de lo que se ha encontrado.
3. Si no se encuentra el espacio de nombres, crea el espacio de nombres y, a continuación, recupera el espacio de nombres recién creado.

    ```
    $Namespace = "MyServiceBusNS"
    $Location = "West US"
    
    # Query to see if the namespace currently exists
    $CurrentNamespace = Get-AzureSBNamespace -Name $Namespace
    
    # Check if the namespace already exists or needs to be created
    if ($CurrentNamespace)
    {
        Write-Output "The namespace [$Namespace] already exists in the [$($CurrentNamespace.Region)] region."
    }
    else
    {
        Write-Host "The [$Namespace] namespace does not exist."
        Write-Output "Creating the [$Namespace] namespace in the [$Location] region..."
        New-AzureSBNamespace -Name $Namespace -Location $Location -CreateACSNamespace $false -NamespaceType Messaging
        $CurrentNamespace = Get-AzureSBNamespace -Name $Namespace
        Write-Host "The [$Namespace] namespace in the [$Location] region has been successfully created."
    }
    ```

Para aprovisionar otras entidades de Bus de servicio, crear una instancia de la clase [NamespaceManager][] desde el SDK.
Puede usar el cmdlet [Get-AzureSBAuthorizationRule][] para recuperar una regla de autorización que se utiliza para proporcionar una cadena de conexión. Se deberá almacenar una referencia a la `NamespaceManager` de instancia en el `$NamespaceManager` variable. Usaremos `$NamespaceManager` más adelante en el script proporcionando otras entidades.

``` powershell
$sbr = Get-AzureSBAuthorizationRule -Namespace $Namespace
# Create the NamespaceManager object to create the event hub
Write-Output "Creating a NamespaceManager object for the [$Namespace] namespace..."
$NamespaceManager = [Microsoft.ServiceBus.NamespaceManager]::CreateFromConnectionString($sbr.ConnectionString);
Write-Output "NamespaceManager object for the [$Namespace] namespace has been successfully created."
```

## <a name="provisioning-other-service-bus-entities"></a>Aprovisionamiento de otras entidades de Bus de servicio

Para poder aprovisionar otras entidades, como colas, temas y Hubs de evento, use la [API de .NET para Bus de servicio][]. En este artículo se centra únicamente en Hubs de evento, pero los pasos para otras entidades son similares. Además, se hace referencia a ejemplos más detallados, incluidas otras entidades, al final de este artículo.

Este elemento de la secuencia de comandos crea cuatro variables locales más. Estas variables se usan para crear una instancia de un `EventHubDescription` objeto. La secuencia de comandos realiza las siguientes tareas:

1. Uso de la `NamespaceManager` objeto, compruebe si el concentrador de evento identificada por `$Path` existe.
2. Si no existe, cree una `EventHubDescription` y que se va a pasar el `NamespaceManager` clase `CreateEventHubIfNotExists` método.
3. Después de determinar que el concentrador de eventos está disponible, crear un grupo de consumidor mediante `ConsumerGroupDescription` y `NamespaceManager`.

    ```
    $Path  = "MyEventHub"
    $PartitionCount = 12
    $MessageRetentionInDays = 7
    $UserMetadata = $null
    $ConsumerGroupName = "MyConsumerGroup"
        
    # Check to see if the Event Hub already exists
    if ($NamespaceManager.EventHubExists($Path))
    {
        Write-Output "The [$Path] event hub already exists in the [$Namespace] namespace."  
    }
    else
    {
        Write-Output "Creating the [$Path] event hub in the [$Namespace] namespace: PartitionCount=[$PartitionCount] MessageRetentionInDays=[$MessageRetentionInDays]..."
        $EventHubDescription = New-Object -TypeName Microsoft.ServiceBus.Messaging.EventHubDescription -ArgumentList $Path
        $EventHubDescription.PartitionCount = $PartitionCount
        $EventHubDescription.MessageRetentionInDays = $MessageRetentionInDays
        $EventHubDescription.UserMetadata = $UserMetadata
        $EventHubDescription.Path = $Path
        $NamespaceManager.CreateEventHubIfNotExists($EventHubDescription);
        Write-Output "The [$Path] event hub in the [$Namespace] namespace has been successfully created."
    }
        
    # Create the consumer group if it doesn't exist
    Write-Output "Creating the consumer group [$ConsumerGroupName] for the [$Path] event hub..."
    $ConsumerGroupDescription = New-Object -TypeName Microsoft.ServiceBus.Messaging.ConsumerGroupDescription -ArgumentList $Path, $ConsumerGroupName
    $ConsumerGroupDescription.UserMetadata = $ConsumerGroupUserMetadata
    $NamespaceManager.CreateConsumerGroupIfNotExists($ConsumerGroupDescription);
    Write-Output "The consumer group [$ConsumerGroupName] for the [$Path] event hub has been successfully created."
    ```

## <a name="migrate-a-namespace-to-another-azure-subscription"></a>Migrar un espacio de nombres a otra suscripción de Azure

La siguiente secuencia de comandos cambia un espacio de nombres de una suscripción de Azure a otra. Para ejecutar esta operación, el espacio de nombres ya debe estar activo y el usuario que ejecuta los comandos de PowerShell debe ser un administrador de suscripciones de origen y de destino.

```
# Create a new resource group in target subscription
Select-AzureRmSubscription -SubscriptionId 'ffffffff-ffff-ffff-ffff-ffffffffffff'
New-AzureRmResourceGroup -Name 'targetRG' -Location 'East US'

# Move namespace from source subscription to target subscription
Select-AzureRmSubscription -SubscriptionId 'aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa'
$res = Find-AzureRmResource -ResourceNameContains mynamespace -ResourceType 'Microsoft.ServiceBus/namespaces'
Move-AzureRmResource -DestinationResourceGroupName 'targetRG' -DestinationSubscriptionId 'ffffffff-ffff-ffff-ffff-ffffffffffff' -ResourceId $res.ResourceId
```

## <a name="next-steps"></a>Pasos siguientes

En este artículo se proporciona un esquema básico para el aprovisionamiento de entidades de Bus de servicio con PowerShell. Todo lo que puede hacer con las bibliotecas de cliente .NET, también puede hacer en una secuencia de comandos de PowerShell.

Hay más ejemplos en estas entradas de blog:

- [Cómo crear colas, temas y suscripciones utilizando una secuencia de comandos de PowerShell de Bus de servicio](http://blogs.msdn.com/b/paolos/archive/2014/12/02/how-to-create-a-service-bus-queues-topics-and-subscriptions-using-a-powershell-script.aspx)
- [Cómo crear un Namespace de Bus de servicio y un concentrador de evento con un script de PowerShell](http://blogs.msdn.com/b/paolos/archive/2014/12/01/how-to-create-a-service-bus-namespace-and-an-event-hub-using-a-powershell-script.aspx)

Algunas listos para usar secuencias de comandos también están disponibles para su descarga:
- [Secuencias de comandos de PowerShell de Bus de servicio](https://code.msdn.microsoft.com/Service-Bus-PowerShell-a46b7059)

<!--Link references-->
[Opciones de compra]: http://azure.microsoft.com/pricing/purchase-options/
[Ofertas de miembro]: http://azure.microsoft.com/pricing/member-offers/
[Versión de prueba gratuita]: http://azure.microsoft.com/pricing/free-trial/
[Instalar y configurar PowerShell de Azure]: ../powershell-install-configure.md
[Paquete NuGet Bus de servicio]: http://www.nuget.org/packages/WindowsAzure.ServiceBus/
[Get-AzureSBNamespace]: https://msdn.microsoft.com/library/azure/dn495122.aspx
[Nueva AzureSBNamespace]: https://msdn.microsoft.com/library/azure/dn495165.aspx
[Get-AzureSBAuthorizationRule]: https://msdn.microsoft.com/library/azure/dn495113.aspx
[API de .NET para Bus de servicio]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.aspx
[NamespaceManager]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx
