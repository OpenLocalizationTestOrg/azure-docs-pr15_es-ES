<properties
    pageTitle="Usar PowerShell para administrar recursos de Bus de servicio y eventos Hubs | Microsoft Azure"
    description="Usar PowerShell para crear y administrar recursos de Bus de servicio y Hubs de evento"
    services="service-bus,event-hubs"
    documentationCenter=".NET"
    authors="sethmanheim"
    manager="timlt"
    editor=""/>

<tags
    ms.service="service-bus"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="na"
    ms.date="10/04/2016"
    ms.author="sethm"/>

# <a name="use-powershell-to-manage-service-bus-and-event-hubs-resources"></a>Usar PowerShell para administrar recursos de Bus de servicio y Hubs de evento

Microsoft Azure PowerShell es un entorno de secuencias de comandos que puede usar para controlar y automatizar la implementación y administración de servicios de Azure. En este artículo se describe cómo usar PowerShell para aprovisionar y administrar entidades de Bus de servicios como espacios de nombres, las colas y Hubs de evento con consola Azure PowerShell local.

## <a name="prerequisites"></a>Requisitos previos

Antes de empezar, necesita lo siguiente:

- Una suscripción de Azure. Azure es una plataforma de suscripción. Para obtener más información acerca de cómo obtener una suscripción, vea [Opciones de compra][], [miembro ofrece][]o [cuenta gratuita][].

- Un equipo con PowerShell de Azure. Para obtener instrucciones, consulte [instalar y configurar Azure PowerShell][].

- Una descripción general de secuencias de comandos de PowerShell, paquetes de NuGet y .NET Framework.

## <a name="include-a-reference-to-the-net-assembly-for-service-bus"></a>Incluir una referencia al ensamblado .NET para Bus de servicio

Hay un número limitado de cmdlets de PowerShell para administrar Bus de servicio. Para aprovisionar entidades que no se exponen a través de los cmdlets existentes, puede usar al cliente de .NET para Bus de servicio de PowerShell por que hace referencia el [paquete NuGet Bus de servicio].

En primer lugar, asegúrese de que la secuencia de comandos puede localizar el ensamblado **Microsoft.ServiceBus.dll** , que se instala con el paquete de NuGet. Para ser flexible, la secuencia de comandos realiza estos pasos:

1. Determina la ruta de acceso desde el que se invoca.
2. Recorre la ruta de acceso hasta que encuentre una carpeta denominada `packages`. Esta carpeta se crea al instalar paquetes de NuGet.
3. Búsquedas de forma recursiva la `packages` carpeta para un ensamblado denominado **Microsoft.ServiceBus.dll**.
4. Hace referencia al ensamblado para que los tipos están disponibles para su uso posterior.

Le mostramos cómo se implementan estos pasos en una secuencia de comandos de PowerShell:

```powershell

try
{
    # IMPORTANT: Make sure to reference the latest version of Microsoft.ServiceBus.dll
    Write-Output "Adding the [Microsoft.ServiceBus.dll] assembly to the script..."
    $scriptPath = Split-Path (Get-Variable MyInvocation -Scope 0).Value.MyCommand.Path
    $packagesFolder = (Split-Path $scriptPath -Parent) + "\packages"
    $assembly = Get-ChildItem $packagesFolder -Include "Microsoft.ServiceBus.dll" -Recurse
    Add-Type -Path $assembly.FullName

    Write-Output "The [Microsoft.ServiceBus.dll] assembly has been successfully added to the script."
}

catch [System.Exception]
{
    Write-Error("Could not add the Microsoft.ServiceBus.dll assembly to the script. Make sure you build the solution before running the provisioning script.")
}

```

## <a name="provision-a-service-bus-namespace"></a>Aprovisionar a un espacio de nombres de Bus de servicio

Cuando se trabaja con espacios de nombres de Bus de servicio, hay dos cmdlets que se puede usar en lugar de .NET SDK: [Get-AzureSBNamespace][] y [AzureSBNamespace de nuevo][].

Este ejemplo crea unas cuantas variables locales en la secuencia de comandos; `$Namespace` and `$Location`.

- `$Namespace`es el nombre del espacio de nombres de Bus de servicio con el que desea trabajar.
- `$Location`identifica el centro de datos en la que se nos aprovisionar el espacio de nombres.
- `$CurrentNamespace`almacena el espacio de nombres de referencia que se recuperan (o crear).

En una secuencia de comandos real, `$Namespace` y `$Location` se pueden pasar como parámetros.

Este elemento de la secuencia de comandos hace lo siguiente:

1. Intenta recuperar un espacio de nombres de Bus de servicio con el nombre proporcionado.
2. Si se encuentra el espacio de nombres, informes de lo que se ha encontrado.
3. Si no se encuentra el espacio de nombres, crea el espacio de nombres y, a continuación, recupera el espacio de nombres recién creado.

    ``` powershell

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
        New-AzureSBNamespace -Name $Namespace -Location $Location -CreateACSNamespace false -NamespaceType Messaging
        $CurrentNamespace = Get-AzureSBNamespace -Name $Namespace
        Write-Host "The [$Namespace] namespace in the [$Location] region has been successfully created."
    }
    ```
Para aprovisionar otras entidades de Bus de servicio, crear una instancia de la `NamespaceManager` objeto del SDK. Puede usar el cmdlet [Get-AzureSBAuthorizationRule][] para recuperar una regla de autorización que se utiliza para proporcionar una cadena de conexión. En este ejemplo se guarda una referencia a la `NamespaceManager` de instancia en el `$NamespaceManager` variable. La secuencia de comandos más adelante usa `$NamespaceManager` proporcionando otras entidades.

    ``` powershell
    $sbr = Get-AzureSBAuthorizationRule -Namespace $Namespace
    # Create the NamespaceManager object to create the Event Hub
    Write-Output "Creating a NamespaceManager object for the [$Namespace] namespace..."
    $NamespaceManager = [Microsoft.ServiceBus.NamespaceManager]::CreateFromConnectionString($sbr.ConnectionString);
    Write-Output "NamespaceManager object for the [$Namespace] namespace has been successfully created."
    ```

## <a name="provisioning-other-service-bus-entities"></a>Aprovisionamiento de otras entidades de Bus de servicio

Para aprovisionar otras entidades, como colas, temas y Hubs de evento, puede usar la [API de .NET para Bus de servicio][]. Ejemplos más detallados, incluidas otras entidades, se hace referencia al final de este artículo.

### <a name="create-an-event-hub"></a>Crear un concentrador de evento

Este elemento de la secuencia de comandos crea cuatro variables locales más. Estas variables se usan para crear una instancia de un `EventHubDescription` objeto. La secuencia de comandos hace lo siguiente:

1. Uso de la `NamespaceManager` objeto, compruebe si el concentrador de evento identificada por `$Path` existe.
2. Si no existe, cree una `EventHubDescription` y que se va a pasar el `NamespaceManager` clase `CreateEventHubIfNotExists` método.
3. Después de determinar que el concentrador de eventos está disponible, crear un grupo de consumidor mediante `ConsumerGroupDescription` y `NamespaceManager`.

    ``` powershell

    $Path  = "MyEventHub"
    $PartitionCount = 12
    $MessageRetentionInDays = 7
    $UserMetadata = $null
    $ConsumerGroupName = "MyConsumerGroup"

    # Check if the Event Hub already exists
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

### <a name="create-a-queue"></a>Crear una cola

Para crear una cola o un tema, realice una comprobación de espacio de nombres como en la sección anterior.

    if ($NamespaceManager.QueueExists($Path))
    {
        Write-Output "The [$Path] queue already exists in the [$Namespace] namespace."
    }
    else
    {
        Write-Output "Creating the [$Path] queue in the [$Namespace] namespace..."
        $QueueDescription = New-Object -TypeName Microsoft.ServiceBus.Messaging.QueueDescription -ArgumentList $Path
        if ($AutoDeleteOnIdle -ge 5)
        {
            $QueueDescription.AutoDeleteOnIdle = [System.TimeSpan]::FromMinutes($AutoDeleteOnIdle)
        }
        if ($DefaultMessageTimeToLive -gt 0)
        {
            $QueueDescription.DefaultMessageTimeToLive = [System.TimeSpan]::FromMinutes($DefaultMessageTimeToLive)
        }
        if ($DuplicateDetectionHistoryTimeWindow -gt 0)
        {
            $QueueDescription.DuplicateDetectionHistoryTimeWindow = [System.TimeSpan]::FromMinutes($DuplicateDetectionHistoryTimeWindow)
        }
        $QueueDescription.EnableBatchedOperations = $EnableBatchedOperations
        $QueueDescription.EnableDeadLetteringOnMessageExpiration = $EnableDeadLetteringOnMessageExpiration
        $QueueDescription.EnableExpress = $EnableExpress
        $QueueDescription.EnablePartitioning = $EnablePartitioning
        $QueueDescription.ForwardDeadLetteredMessagesTo = $ForwardDeadLetteredMessagesTo
        $QueueDescription.ForwardTo = $ForwardTo
        $QueueDescription.IsAnonymousAccessible = $IsAnonymousAccessible
        if ($LockDuration -gt 0)
        {
            $QueueDescription.LockDuration = [System.TimeSpan]::FromSeconds($LockDuration)
        }
        $QueueDescription.MaxDeliveryCount = $MaxDeliveryCount
        $QueueDescription.MaxSizeInMegabytes = $MaxSizeInMegabytes
        $QueueDescription.RequiresDuplicateDetection = $RequiresDuplicateDetection
        $QueueDescription.RequiresSession = $RequiresSession
        if ($EnablePartitioning)
        {
            $QueueDescription.SupportOrdering = $False
        }
        else
        {
            $QueueDescription.SupportOrdering = $SupportOrdering
        }
        $QueueDescription.UserMetadata = $UserMetadata
        $NamespaceManager.CreateQueue($QueueDescription);
    }

### <a name="create-a-topic"></a>Crear un tema

    if ($NamespaceManager.TopicExists($Path))
    {
        Write-Output "The [$Path] topic already exists in the [$Namespace] namespace."
    }
    else
    {
        Write-Output "Creating the [$Path] topic in the [$Namespace] namespace..."
        $TopicDescription = New-Object -TypeName Microsoft.ServiceBus.Messaging.TopicDescription -ArgumentList $Path
        if ($AutoDeleteOnIdle -ge 5)
        {
            $TopicDescription.AutoDeleteOnIdle = [System.TimeSpan]::FromMinutes($AutoDeleteOnIdle)
        }
        if ($DefaultMessageTimeToLive -gt 0)
        {
            $TopicDescription.DefaultMessageTimeToLive = [System.TimeSpan]::FromMinutes($DefaultMessageTimeToLive)
        }
        if ($DuplicateDetectionHistoryTimeWindow -gt 0)
        {
            $TopicDescription.DuplicateDetectionHistoryTimeWindow = [System.TimeSpan]::FromMinutes($DuplicateDetectionHistoryTimeWindow)
        }
        $TopicDescription.EnableBatchedOperations = $EnableBatchedOperations
        $TopicDescription.EnableExpress = $EnableExpress
        $TopicDescription.EnableFilteringMessagesBeforePublishing = $EnableFilteringMessagesBeforePublishing
        $TopicDescription.EnablePartitioning = $EnablePartitioning
        $TopicDescription.IsAnonymousAccessible = $IsAnonymousAccessible
        $TopicDescription.MaxSizeInMegabytes = $MaxSizeInMegabytes
        $TopicDescription.RequiresDuplicateDetection = $RequiresDuplicateDetection
        if ($EnablePartitioning)
        {
            $TopicDescription.SupportOrdering = $False
        }
        else
        {
            $TopicDescription.SupportOrdering = $SupportOrdering
        }
        $TopicDescription.UserMetadata = $UserMetadata
        $NamespaceManager.CreateTopic($TopicDescription);
    }

## <a name="next-steps"></a>Pasos siguientes

Este artículo proporciona un flujo de trabajo básico para aprovisionar entidades de Bus de servicio con PowerShell. Aunque hay un número limitado de cmdlets de PowerShell para administrar Bus de servicios de mensajería entidades, al hacer referencia al ensamblado Microsoft.ServiceBus.dll, prácticamente cualquier operación que puede realizar con las bibliotecas de cliente .NET que también se puede realizar en una secuencia de comandos de PowerShell.

Hay más ejemplos en estas entradas de blog:

- [Cómo crear colas, temas y suscripciones utilizando una secuencia de comandos de PowerShell de Bus de servicio](http://blogs.msdn.com/b/paolos/archive/2014/12/02/how-to-create-a-service-bus-queues-topics-and-subscriptions-using-a-powershell-script.aspx)
- [Cómo crear un Namespace de Bus de servicio y un concentrador de evento con un script de PowerShell](http://blogs.msdn.com/b/paolos/archive/2014/12/01/how-to-create-a-service-bus-namespace-and-an-event-hub-using-a-powershell-script.aspx)

Algunas listos para usar secuencias de comandos también están disponibles para su descarga:

- [Secuencias de comandos de PowerShell de Bus de servicio](https://code.msdn.microsoft.com/Service-Bus-PowerShell-a46b7059)

<!--Anchors-->

[Opciones de compra]: http://azure.microsoft.com/pricing/purchase-options/
[ofertas de miembro]: http://azure.microsoft.com/pricing/member-offers/
[cuenta gratuita]: http://azure.microsoft.com/pricing/free-trial/
[Paquete NuGet Bus de servicio]: http://www.nuget.org/packages/WindowsAzure.ServiceBus/
[Get-AzureSBNamespace]: https://msdn.microsoft.com/library/azure/dn495122.aspx
[Nueva AzureSBNamespace]: https://msdn.microsoft.com/library/azure/dn495165.aspx
[Get-AzureSBAuthorizationRule]: https://msdn.microsoft.com/library/azure/dn495113.aspx
[API de .NET para Bus de servicio]: https://msdn.microsoft.com/en-us/library/azure/mt419900.aspx
[Instalar y configurar PowerShell de Azure]: ../powershell-install-configure.md
