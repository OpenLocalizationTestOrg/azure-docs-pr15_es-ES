<properties 
    pageTitle="Administrar la búsqueda de Azure con secuencias de comandos de Powershell | Microsoft Azure | Servicio de nube hospedado de búsqueda" 
    description="Administrar el servicio de búsqueda de Azure con secuencias de comandos de PowerShell. Crear o actualizar un servicio de búsqueda de Azure y administrar las claves de administración de búsqueda de Azure" 
    services="search" 
    documentationCenter="" 
    authors="seansaleh" 
    manager="mblythe" 
    editor=""
    tags="azure-resource-manager"/>

<tags 
    ms.service="search" 
    ms.devlang="na" 
    ms.workload="search" 
    ms.topic="article" 
    ms.tgt_pltfrm="powershell" 
    ms.date="08/15/2016" 
    ms.author="seasa"/>

# <a name="manage-your-azure-search-service-with-powershell"></a>Administrar el servicio de búsqueda de Azure con PowerShell
> [AZURE.SELECTOR]
- [Portal](search-manage.md)
- [PowerShell](search-manage-powershell.md)
- [API DE REST](search-get-started-management-api.md)

Este tema describe los comandos de PowerShell para realizar muchas de las tareas de administración de servicios de búsqueda de Azure. Se le guiará a través de la creación de un servicio de búsqueda, modificar su escala y administrar sus claves API.
Estos comandos corresponden con las opciones de administración disponibles en la [API de REST de administración de búsqueda de Azure](http://msdn.microsoft.com/library/dn832684.aspx).

## <a name="prerequisites"></a>Requisitos previos
 
- Debe tener Azure PowerShell 1.0 o mayor. Para obtener instrucciones, consulte [instalar y configurar Azure PowerShell](../powershell-install-configure.md).
- Debe haber iniciado a su suscripción de Azure en PowerShell como se describe a continuación.

En primer lugar, debe iniciar sesión en Azure con este comando:

    Login-AzureRmAccount

Especifique la dirección de correo electrónico de su cuenta de Azure y su contraseña en el cuadro de diálogo de inicio de sesión de Microsoft Azure.

Como alternativa, puede [iniciar sesión de forma no interactiva con un servicio principal](../resource-group-authenticate-service-principal.md).

Si tiene varias suscripciones de Azure, debe configurar su suscripción de Azure. Para ver una lista de sus suscripciones actuales, ejecute este comando.

    Get-AzureRmSubscription | sort SubscriptionName | Select SubscriptionName

Para especificar la suscripción, ejecute el siguiente comando. En el ejemplo siguiente, el nombre de suscripción es `ContosoSubscription`.

    Select-AzureRmSubscription -SubscriptionName ContosoSubscription

## <a name="commands-to-help-you-get-started"></a>Comandos para ayudarle a empezar a

    $serviceName = "your-service-name-lowercase-with-dashes"
    $sku = "free" # or "basic" or "standard" for paid services
    $location = "West US"
    # You can get a list of potential locations with
    # (Get-AzureRmResourceProvider -ListAvailable | Where-Object {$_.ProviderNamespace -eq 'Microsoft.Search'}).Locations
    $resourceGroupName = "YourResourceGroup" 
    # If you don't already have this resource group, you can create it with 
    # New-AzureRmResourceGroup -Name $resourceGroupName -Location $location

    # Register the ARM provider idempotently. This must be done once per subscription
    Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.Search" -Force

    # Create a new search service
    # This command will return once the service is fully created
    New-AzureRmResourceGroupDeployment `
        -ResourceGroupName $resourceGroupName `
        -TemplateUri "https://gallery.azure.com/artifact/20151001/Microsoft.Search.1.0.9/DeploymentTemplates/searchServiceDefaultTemplate.json" `
        -NameFromTemplate $serviceName `
        -Sku $sku `
        -Location $location `
        -PartitionCount 1 `
        -ReplicaCount 1
    
    # Get information about your new service and store it in $resource
    $resource = Get-AzureRmResource `
        -ResourceType "Microsoft.Search/searchServices" `
        -ResourceGroupName $resourceGroupName `
        -ResourceName $serviceName `
        -ApiVersion 2015-08-19
    
    # View your resource
    $resource
    
    # Get the primary admin API key
    $primaryKey = (Invoke-AzureRmResourceAction `
        -Action listAdminKeys `
        -ResourceId $resource.ResourceId `
        -ApiVersion 2015-08-19).PrimaryKey

    # Regenerate the secondary admin API Key
    $secondaryKey = (Invoke-AzureRmResourceAction `
        -ResourceType "Microsoft.Search/searchServices/regenerateAdminKey" `
        -ResourceGroupName $resourceGroupName `
        -ResourceName $serviceName `
        -ApiVersion 2015-08-19 `
        -Action secondary).SecondaryKey

    # Create a query key for read only access to your indexes
    $queryKeyDescription = "query-key-created-from-powershell"
    $queryKey = (Invoke-AzureRmResourceAction `
        -ResourceType "Microsoft.Search/searchServices/createQueryKey" `
        -ResourceGroupName $resourceGroupName `
        -ResourceName $serviceName `
        -ApiVersion 2015-08-19 `
        -Action $queryKeyDescription).Key
    
    # View your query key
    $queryKey

    # Delete query key
    Remove-AzureRmResource `
        -ResourceType "Microsoft.Search/searchServices/deleteQueryKey/$($queryKey)" `
        -ResourceGroupName $resourceGroupName `
        -ResourceName $serviceName `
        -ApiVersion 2015-08-19
        
    # Scale your service up
    # Note that this will only work if you made a non "free" service
    # This command will not return until the operation is finished
    # It can take 15 minutes or more to provision the additional resources
    $resource.Properties.ReplicaCount = 2
    $resource | Set-AzureRmResource
    
    # Delete your service
    # Deleting your service will delete all indexes and data in the service
    $resource | Remove-AzureRmResource
    
## <a name="next-steps"></a>Pasos siguientes
    
Ahora que se ha creado su servicio, puede realizar los siguientes pasos: generar un [índice](search-what-is-an-index.md), [un índice de consulta](search-query-overview.md)y por último crear y administrar su propia aplicación de búsqueda que utiliza la búsqueda de Azure.

- [Crear un índice de búsqueda de Azure en el portal de Azure](search-create-index-portal.md)

- [Consultar un índice de búsqueda de Azure con el Explorador de búsqueda en el portal de Azure](search-explorer.md)

- [Configurar un indizador para cargar los datos de otros servicios](search-indexer-overview.md)

- [Cómo usar la búsqueda de Azure en .NET](search-howto-dotnet-sdk.md)

- [Analizar el tráfico de la búsqueda de Azure](search-traffic-analytics.md)
