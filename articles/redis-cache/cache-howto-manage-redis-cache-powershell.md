<properties
    pageTitle="Administrar caché Redis Azure con PowerShell Azure | Microsoft Azure"
    description="Obtenga información sobre cómo realizar tareas administrativas de caché Redis de Azure con PowerShell de Azure."
    services="redis-cache"
    documentationCenter="" 
    authors="steved0x" 
    manager="douge" 
    editor=""/>

<tags
    ms.service="cache" 
    ms.workload="tbd" 
    ms.tgt_pltfrm="cache-redis" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/25/2016" 
    ms.author="sdanie"/>

# <a name="manage-azure-redis-cache-with-azure-powershell"></a>Administrar caché Redis Azure con PowerShell Azure

> [AZURE.SELECTOR]
- [PowerShell](cache-howto-manage-redis-cache-powershell.md)
- [CLI de Azure](cache-manage-cli.md)

Este tema muestra que cómo llevar a cabo tareas comunes como crear, actualiza y ajustar el tamaño de las instancias de Azure Redis caché, cómo volver a generar las teclas de acceso y cómo ver información acerca de la caché. Para obtener una lista completa de los cmdlets de PowerShell de caché Redis Azure, vea [cmdlets de Azure Redis caché](https://msdn.microsoft.com/library/azure/mt634513.aspx).

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)][modelo clásico](../resource-manager-deployment-model.md#classic-deployment-characteristics).

## <a name="prerequisites"></a>Requisitos previos

Si ya ha instalado PowerShell de Azure, debe tener Azure PowerShell versión 1.0.0 o posterior. Puede comprobar la versión de PowerShell de Azure que ha instalado con este comando en el símbolo del sistema de PowerShell de Azure.

    Get-Module azure | format-table version

[AZURE.INCLUDE [powershell-preview](../../includes/powershell-preview-inline-include.md)]

En primer lugar, debe iniciar sesión en Azure con este comando.

    Login-AzureRmAccount

Especifique la dirección de correo electrónico de su cuenta de Azure y su contraseña en el diálogo de inicio de sesión de Microsoft Azure.

A continuación, si tiene varias suscripciones de Azure, debe configurar su suscripción de Azure. Para ver una lista de sus suscripciones actuales, ejecute este comando.

    Get-AzureRmSubscription | sort SubscriptionName | Select SubscriptionName

Para especificar la suscripción, ejecute el siguiente comando. En el ejemplo siguiente, el nombre de suscripción es `ContosoSubscription`.

    Select-AzureRmSubscription -SubscriptionName ContosoSubscription

Para poder usar Windows PowerShell con el Administrador de recursos de Azure, se necesita lo siguiente:

- Windows PowerShell, versión 3.0 o 4.0. Para buscar la versión de Windows PowerShell, escriba:`$PSVersionTable` y compruebe el valor de `PSVersion` es 3.0 o 4.0. Para instalar una versión compatible, consulte [Windows Management Framework 3.0](http://www.microsoft.com/download/details.aspx?id=34595) o [Windows Management Framework 4.0](http://www.microsoft.com/download/details.aspx?id=40855).

Para obtener ayuda detallada para cualquier cmdlet que vea en este tutorial, use el cmdlet Get-Help.

    Get-Help <cmdlet-name> -Detailed

Por ejemplo, para obtener ayuda sobre la `New-AzureRmRedisCache` cmdlet, tipo:

    Get-Help New-AzureRmRedisCache -Detailed

### <a name="how-to-connect-to-azure-government-cloud-or-azure-china-cloud"></a>Cómo conectarse a la nube de la administración pública de Azure o Azure China

De forma predeterminada el Azure entorno es `AzureCloud`, que representa la instancia de nube de Azure global. Para conectarse a una instancia diferente, utilice la `Add-AzureRmAccount` comando con la `-Environment` o -`EnvironmentName` conmutador de línea de comandos con el entorno deseado o el nombre del entorno.

Para ver la lista de entornos disponibles, ejecute el `Get-AzureRmEnvironment` cmdlet.

### <a name="to-connect-to-the-azure-government-cloud"></a>Para conectarse a la nube de la administración pública de Azure

Para conectarse a la nube de la administración pública de Azure, use uno de los siguientes comandos.

    Add-AzureRMAccount -EnvironmentName AzureUSGovernment

o

    Add-AzureRmAccount -Environment (Get-AzureRmEnvironment -Name AzureUSGovernment)

Para crear una caché en la nube de la administración pública de Azure, utilice una de las siguientes ubicaciones.

-   Virginia USGov
-   USGov Iowa

Para obtener más información acerca de la nube de la administración pública de Azure, vea [Administración pública de Microsoft Azure](https://azure.microsoft.com/features/gov/) y la [Guía del desarrollador de administración pública de Microsoft Azure](../azure-government-developer-guide.md).

### <a name="to-connect-to-the-azure-china-cloud"></a>Para conectarse a la nube de China de Azure

Para conectarse a la nube de China de Azure, use uno de los siguientes comandos.

    Add-AzureRMAccount -EnvironmentName AzureChinaCloud

o

    Add-AzureRmAccount -Environment (Get-AzureRmEnvironment -Name AzureChinaCloud)

Para crear una caché en la nube de China de Azure, utilice una de las siguientes ubicaciones.

-   China oriental
-   Norte de China

Para obtener más información acerca de la nube de Azure China, consulte [AzureChinaCloud para Azure a través de 21Vianet en China](http://www.windowsazure.cn/).

### <a name="properties-used-for-azure-redis-cache-powershell"></a>Propiedades que se usan para Azure Redis caché PowerShell

La tabla siguiente contiene las propiedades y las descripciones de los parámetros utilizados con frecuencia al crear y administrar las instancias de Azure Redis caché con PowerShell de Azure.

| Parámetro          | Descripción                                                                                                                                                                                                        | Predeterminado  |
|--------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------|
| Nombre               | Nombre de la memoria caché                                                                                                                                                                                                  |          |
| Ubicación           | Ubicación de la caché                                                                                                                                                                                              |          |
| ResourceGroupName  | Nombre de grupo de recursos en el que se va a crear la caché                                                                                                                                                                   |          |
| Tamaño               | El tamaño de la memoria caché. Los valores válidos son: P1, P2, P3, P4, C0, C1, C2, C3, C4, C5, C6, 250MB, 1GB de memoria, 2,5 GB, 6 GB, 13 GB, 26 GB, 53 GB                                                                     | 1GB DE MEMORIA      |
| ShardCount         | El número de shards para crear al crear una caché premium con clúster habilitado. Los valores válidos son: 1, 2, 3, 4, 5, 6, 7, 8, 9, 10                                                                                                      |          |
| SKU                | Especifica la SKU de la memoria caché. Los valores válidos son: básico, estándar, Premium                                                                                                                                         | Estándar |
| RedisConfiguration | Especifica Redis parámetros de configuración. Para obtener más información acerca de cada opción, consulte la siguiente tabla [RedisConfiguration propiedades](#redisconfiguration-properties) . |          |
| EnableNonSslPort   | Indica si el puerto SSL no está habilitado.                                                                                                                                                                     | Falso    |
| MaxMemoryPolicy    | Este parámetro está desusado: utilice RedisConfiguration en su lugar.                                                                                                                                              |          |
| StaticIP           | Cuando la memoria caché en un VNET de hospedaje, especifica una dirección IP única en la subred de la memoria caché. Si no se proporciona uno ya está seleccionada de la subred.                                                                                                                     |          |
| Subred             | Cuando la memoria caché en un VNET de hospedaje, especifica el nombre de la subred en la que se va a implementar la caché.                                                                                                                  |          |
| VirtualNetwork     | Cuando la memoria caché en un VNET de hospedaje, especifica el identificador de recursos de la VNET en el que se implementa la memoria caché.                                                                                                             |          |
| Tipo de clave            | Especifica la clave de acceso que regenerar al renovar las teclas de acceso. Los valores válidos son: principal y secundario |  |                                                                                                                                                                                                              |          |


### <a name="redisconfiguration-properties"></a>Propiedades de RedisConfiguration

| (Propiedad)                      | Descripción                                                                                                          | Niveles de precios       |
|-------------------------------|----------------------------------------------------------------------------------------------------------------------|---------------------|
| copia de seguridad de RDB habilitada            | Si está habilitada la [Redis persistencia de datos](cache-how-to-premium-persistence.md)                                     | Sólo Premium        |
| RDB-almacenamiento-cadena de conexión | La cadena de conexión en la cuenta de almacenamiento de [Redis persistencia de datos](cache-how-to-premium-persistence.md)       | Sólo Premium        |
| frecuencia de copia de seguridad de RDB          | La frecuencia de copia de seguridad para [Redis persistencia de datos](cache-how-to-premium-persistence.md)                               | Sólo Premium        |
| reservado MaxMemory            | Configura la [memoria reservada](cache-configure.md#maxmemory-policy-and-maxmemory-reserved) para procesos sin caché | Estándar y Premium |
| Directiva de MaxMemory              | Configura la [Directiva de expulsión](cache-configure.md#maxmemory-policy-and-maxmemory-reserved) de la caché           | Todos los niveles de precios   |
| eventos de espacio de claves notificar        | Configura [las notificaciones de espacio de claves](cache-configure.md#keyspace-notifications-advanced-settings)                     | Estándar y Premium |
| hash max ziplist movimientos      | Configura la [optimización de la memoria](http://redis.io/topics/memory-optimization) para tipos de datos agregado pequeña          | Estándar y Premium |
| hash max ziplist valor        | Configura la [optimización de la memoria](http://redis.io/topics/memory-optimization) para tipos de datos agregado pequeña          | Estándar y Premium |
| establecer max intset movimientos        | Configura la [optimización de la memoria](http://redis.io/topics/memory-optimization) para tipos de datos agregado pequeña          | Estándar y Premium |
| zset max ziplist movimientos      | Configura la [optimización de la memoria](http://redis.io/topics/memory-optimization) para tipos de datos agregado pequeña          | Estándar y Premium |
| zset max ziplist valor        | Configura la [optimización de la memoria](http://redis.io/topics/memory-optimization) para tipos de datos agregado pequeña          | Estándar y Premium |
| bases de datos                     | Configura el número de bases de datos. Esta propiedad se puede configurar sólo durante la creación de la memoria caché.                          | Estándar y Premium |

## <a name="to-create-a-redis-cache"></a>Para crear una caché Redis

Las nuevas instancias de Azure Redis caché creadas con el cmdlet [AzureRmRedisCache de nuevo](https://msdn.microsoft.com/library/azure/mt634517.aspx) .

>[AZURE.IMPORTANT] El portal de la primera vez que se crea una caché Redis en una suscripción con el portal de Azure, registra el `Microsoft.Cache` espacio de nombres de la suscripción. Si intenta crear la primera caché Redis en una suscripción con PowerShell, primero debe registrar ese espacio de nombres mediante el comando siguiente; cmdlets de lo contrario, como `New-AzureRmRedisCache` y `Get-AzureRmRedisCache` un error.
>
>`Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.Cache"`

Para ver una lista de parámetros disponibles y sus descripciones de `New-AzureRmRedisCache`, ejecute el siguiente comando.

    PS C:\> Get-Help New-AzureRmRedisCache -detailed
    
    NAME
        New-AzureRmRedisCache
    
    SYNOPSIS
        Creates a new redis cache.
    
    
    SYNTAX
        New-AzureRmRedisCache -Name <String> -ResourceGroupName <String> -Location <String> [-RedisVersion <String>]
        [-Size <String>] [-Sku <String>] [-MaxMemoryPolicy <String>] [-RedisConfiguration <Hashtable>] [-EnableNonSslPort
        <Boolean>] [-ShardCount <Integer>] [-VirtualNetwork <String>] [-Subnet <String>] [-StaticIP <String>]
        [<CommonParameters>]
    
    
    DESCRIPTION
        The New-AzureRmRedisCache cmdlet creates a new redis cache.
    
    
    PARAMETERS
        -Name <String>
            Name of the redis cache to create.
    
        -ResourceGroupName <String>
            Name of resource group in which to create the redis cache.
    
        -Location <String>
            Location in which to create the redis cache.
    
        -RedisVersion <String>
            RedisVersion is deprecated and will be removed in future release.
    
        -Size <String>
            Size of the redis cache. The default value is 1GB or C1. Possible values are P1, P2, P3, P4, C0, C1, C2, C3,
            C4, C5, C6, 250MB, 1GB, 2.5GB, 6GB, 13GB, 26GB, 53GB.
    
        -Sku <String>
            Sku of redis cache. The default value is Standard. Possible values are Basic, Standard and Premium.
    
        -MaxMemoryPolicy <String>
            The 'MaxMemoryPolicy' setting has been deprecated. Please use 'RedisConfiguration' setting to set
            MaxMemoryPolicy. e.g. -RedisConfiguration @{"maxmemory-policy" = "allkeys-lru"}
    
        -RedisConfiguration <Hashtable>
            All Redis Configuration Settings. Few possible keys: rdb-backup-enabled, rdb-storage-connection-string,
            rdb-backup-frequency, maxmemory-reserved, maxmemory-policy, notify-keyspace-events, hash-max-ziplist-entries,
            hash-max-ziplist-value, set-max-intset-entries, zset-max-ziplist-entries, zset-max-ziplist-value, databases.
    
        -EnableNonSslPort <Boolean>
            EnableNonSslPort is used by Azure Redis Cache. If no value is provided, the default value is false and the
            non-SSL port will be disabled. Possible values are true and false.
    
        -ShardCount <Integer>
            The number of shards to create on a Premium Cluster Cache.
    
        -VirtualNetwork <String>
            The exact ARM resource ID of the virtual network to deploy the redis cache in. Example format: /subscriptions/{
            subid}/resourceGroups/{resourceGroupName}/providers/Microsoft.ClassicNetwork/VirtualNetworks/{vnetName}
    
        -Subnet <String>
            Required when deploying a redis cache inside an existing Azure Virtual Network.
    
        -StaticIP <String>
            Required when deploying a redis cache inside an existing Azure Virtual Network.
    
        <CommonParameters>
            This cmdlet supports the common parameters: Verbose, Debug,
            ErrorAction, ErrorVariable, WarningAction, WarningVariable,
            OutBuffer, PipelineVariable, and OutVariable. For more information, see
            about_CommonParameters (http://go.microsoft.com/fwlink/?LinkID=113216).

Para crear una caché con parámetros predeterminados, ejecute el siguiente comando.

    New-AzureRmRedisCache -ResourceGroupName myGroup -Name mycache -Location "North Central US"

`ResourceGroupName`, `Name`, y `Location` son parámetros requeridos, pero el resto son opcional y tienen valores predeterminados. Ejecutar el comando anterior, crea una instancia de caché para Redis Azure SKU estándar con el nombre especificado, la ubicación y el grupo de recursos, que es 1 GB de tamaño con el puerto SSL no deshabilitado.

Para crear una caché premium, especifique el tamaño de P1 (6 GB - 60 GB), P2 (13 GB - 130 GB), P3 (26 GB - 260 GB), o P4 (GB 53-530 GB). Para habilitar clústeres, especifique un recuento de fragmentar utilizando la `ShardCount` parámetro. En el ejemplo siguiente se crea una caché de premium P1 con 3 shards. Una caché de premium P1 es 6 GB de tamaño y dado que se especifica tres shards el tamaño total es de 18 GB (3 x 6 GB).

    New-AzureRmRedisCache -ResourceGroupName myGroup -Name mycache -Location "North Central US" -Sku Premium -Size P1 -ShardCount 3

Para especificar los valores de la `RedisConfiguration` parámetro, encierre los valores dentro de `{}` como clave y valor como pares `@{"maxmemory-policy" = "allkeys-random", "notify-keyspace-events" = "KEA"}`. En el ejemplo siguiente se crea una caché de 1 GB estándar con `allkeys-random` maxmemory notificaciones de directiva y el espacio de claves configuradas con `KEA`. Para obtener más información, vea [notificaciones de espacio de claves (configuración avanzada)](cache-configure.md#keyspace-notifications-advanced-settings) y [Maxmemory directiva y reservado maxmemory](cache-configure.md#maxmemory-policy-and-maxmemory-reserved).

    New-AzureRmRedisCache -ResourceGroupName myGroup -Name mycache -Location "North Central US" -RedisConfiguration @{"maxmemory-policy" = "allkeys-random", "notify-keyspace-events" = "KEA"}

<a name="databases"></a>
## <a name="to-configure-the-databases-setting-during-cache-creation"></a>Para configurar las bases de datos de configuración durante la creación de caché

La `databases` se puede configurar durante la creación de la memoria caché. En el ejemplo siguiente se crea una prima P3 (26 GB) de memoria caché con 48 bases de datos mediante el cmdlet [AzureRmRedisCache de nuevo](https://msdn.microsoft.com/library/azure/mt634517.aspx) .

    New-AzureRmRedisCache -ResourceGroupName myGroup -Name mycache -Location "North Central US" -Sku Premium -Size P3 -RedisConfiguration @{"databases" = "48"}

Para obtener más información sobre la `databases` propiedad, consulte [Configuración del servidor predeterminado Azure Redis caché](cache-configure.md#default-redis-server-configuration). Para obtener más información sobre la creación de una caché mediante el cmdlet [New-AzureRmRedisCache](https://msdn.microsoft.com/library/azure/mt634517.aspx) , vea la sección anterior [para crear una caché Redis](#to-create-a-redis-cache) .

## <a name="to-update-a-redis-cache"></a>Actualizar una caché Redis

Instancias de caché Redis Azure se actualizan mediante el cmdlet [Set-AzureRmRedisCache](https://msdn.microsoft.com/library/azure/mt634518.aspx) .

Para ver una lista de parámetros disponibles y sus descripciones de `Set-AzureRmRedisCache`, ejecute el siguiente comando.

    PS C:\> Get-Help Set-AzureRmRedisCache -detailed
    
    NAME
        Set-AzureRmRedisCache
    
    SYNOPSIS
        Set redis cache updatable parameters.
    
    SYNTAX
        Set-AzureRmRedisCache -Name <String> -ResourceGroupName <String> [-Size <String>] [-Sku <String>]
        [-MaxMemoryPolicy <String>] [-RedisConfiguration <Hashtable>] [-EnableNonSslPort <Boolean>] [-ShardCount
        <Integer>] [<CommonParameters>]
    
    DESCRIPTION
        The Set-AzureRmRedisCache cmdlet sets redis cache parameters.
    
    PARAMETERS
        -Name <String>
            Name of the redis cache to update.
    
        -ResourceGroupName <String>
            Name of the resource group for the cache.
    
        -Size <String>
            Size of the redis cache. The default value is 1GB or C1. Possible values are P1, P2, P3, P4, C0, C1, C2, C3,
            C4, C5, C6, 250MB, 1GB, 2.5GB, 6GB, 13GB, 26GB, 53GB.
    
        -Sku <String>
            Sku of redis cache. The default value is Standard. Possible values are Basic, Standard and Premium.
    
        -MaxMemoryPolicy <String>
            The 'MaxMemoryPolicy' setting has been deprecated. Please use 'RedisConfiguration' setting to set
            MaxMemoryPolicy. e.g. -RedisConfiguration @{"maxmemory-policy" = "allkeys-lru"}
    
        -RedisConfiguration <Hashtable>
            All Redis Configuration Settings. Few possible keys: rdb-backup-enabled, rdb-storage-connection-string,
            rdb-backup-frequency, maxmemory-reserved, maxmemory-policy, notify-keyspace-events, hash-max-ziplist-entries,
            hash-max-ziplist-value, set-max-intset-entries, zset-max-ziplist-entries, zset-max-ziplist-value.
    
        -EnableNonSslPort <Boolean>
            EnableNonSslPort is used by Azure Redis Cache. The default value is null and no change will be made to the
            currently configured value. Possible values are true and false.
    
        -ShardCount <Integer>
            The number of shards to create on a Premium Cluster Cache.
    
        <CommonParameters>
            This cmdlet supports the common parameters: Verbose, Debug,
            ErrorAction, ErrorVariable, WarningAction, WarningVariable,
            OutBuffer, PipelineVariable, and OutVariable. For more information, see
            about_CommonParameters (http://go.microsoft.com/fwlink/?LinkID=113216).

La `Set-AzureRmRedisCache` cmdlet puede usarse para actualizar propiedades como `Size`, `Sku`, `EnableNonSslPort`y el `RedisConfiguration` valores. 

El comando siguiente actualiza la directiva de maxmemory para la caché Redis denominado myCache.

    Set-AzureRmRedisCache -ResourceGroupName "myGroup" -Name "myCache" -RedisConfiguration @{"maxmemory-policy" = "allkeys-random"}

<a name="scale"></a>
## <a name="to-scale-a-redis-cache"></a>Escalar una caché Redis

`Set-AzureRmRedisCache`se puede usar para ajustar el tamaño de caché de Azure Redis instancia cuando el `Size`, `Sku`, o `ShardCount` modificar las propiedades. 

>[AZURE.NOTE]Escalar una caché con PowerShell está sujeto a los mismos límites directrices como escala una caché desde el portal de Azure. Puede escalar a un nivel de precio diferente con las siguientes restricciones.
>
>-  No puede escalar desde un nivel de precios superior a un nivel de precio menor.
>    -    No puede escalar desde una caché **Premium** hacia abajo hasta un **estándar** o una caché **básico** .
>    -    No puede escalar desde una caché **estándar** hacia abajo hasta una caché **básico** .
>-  Puede escalar desde una caché **básicas** para una caché **estándar** , pero no puede cambiar el tamaño al mismo tiempo. Si necesita un tamaño diferente, puede realizar una operación de escalado posterior hasta el tamaño deseado.
>-  No puede escalar desde una caché **básico** directamente a una caché **Premium** . Deben escalar **básicas** **estándar** en una operación de escala y, a continuación, de **estándar** a **Premium** en una operación de escalado subsiguientes.
>-  No puede cambiar la escala de tamaño hacia abajo hasta la **C0 (250 MB)** tamaño.
>
>Para obtener más información, vea [Cómo escala Azure Redis caché](cache-how-to-scale.md).

En el ejemplo siguiente se muestra cómo ampliar una caché denominado `myCache` a una caché de 2,5 GB. Tenga en cuenta que este comando funciona para un Basic o una caché estándar.

    Set-AzureRmRedisCache -ResourceGroupName myGroup -Name myCache -Size 2.5GB

Después de emite este comando, se devuelve el estado de la memoria caché (similar a la llamada `Get-AzureRmRedisCache`). Tenga en cuenta que el `ProvisioningState` es `Scaling`.

    PS C:\> Set-AzureRmRedisCache -Name myCache -ResourceGroupName myGroup -Size 2.5GB
    
    
    Name               : mycache
    Id                 : /subscriptions/12ad12bd-abdc-2231-a2ed-a2b8b246bbad4/resourceGroups/mygroup/providers/Mi
                         crosoft.Cache/Redis/mycache
    Location           : South Central US
    Type               : Microsoft.Cache/Redis
    HostName           : mycache.redis.cache.windows.net
    Port               : 6379
    ProvisioningState  : Scaling
    SslPort            : 6380
    RedisConfiguration : {[maxmemory-policy, volatile-lru], [maxmemory-reserved, 150], [notify-keyspace-events, KEA],
                         [maxmemory-delta, 150]...}
    EnableNonSslPort   : False
    RedisVersion       : 3.0
    Size               : 1GB
    Sku                : Standard
    ResourceGroupName  : mygroup
    PrimaryKey         : ....
    SecondaryKey       : ....
    VirtualNetwork     :
    Subnet             :
    StaticIP           :
    TenantSettings     : {}
    ShardCount         :

Cuando finalice, la operación de escalado la `ProvisioningState` cambia a `Succeeded`. Si necesita realizar una operación de escalado posteriores, como el cambio de básicas estándar y, a continuación, cambiar el tamaño, debe esperar hasta que haya completado la operación anterior o recibe un error similar al siguiente.

    Set-AzureRmRedisCache : Conflict: The resource '...' is not in a stable state, and is currently unable to accept the update request.

## <a name="to-get-information-about-a-redis-cache"></a>Para obtener información acerca de la caché Redis

Puede recuperar información sobre una caché mediante el cmdlet [Get-AzureRmRedisCache](https://msdn.microsoft.com/library/azure/mt634514.aspx) .

Para ver una lista de parámetros disponibles y sus descripciones de `Get-AzureRmRedisCache`, ejecute el siguiente comando.

    PS C:\> Get-Help Get-AzureRmRedisCache -detailed
    
    NAME
        Get-AzureRmRedisCache
    
    SYNOPSIS
        Gets details about a single cache or all caches in the specified resource group or all caches in the current
        subscription.
    
    SYNTAX
        Get-AzureRmRedisCache [-Name <String>] [-ResourceGroupName <String>] [<CommonParameters>]
    
    DESCRIPTION
        The Get-AzureRmRedisCache cmdlet gets the details about a cache or caches depending on input parameters. If both
        ResourceGroupName and Name parameters are provided then Get-AzureRmRedisCache will return details about the
        specific cache name provided.
    
        If only ResourceGroupName is provided than it will return details about all caches in the specified resource group.
    
        If no parameters are given than it will return details about all caches the current subscription.
    
    PARAMETERS
        -Name <String>
            The name of the cache. When this parameter is provided along with ResourceGroupName, Get-AzureRmRedisCache
            returns the details for the cache.
    
        -ResourceGroupName <String>
            The name of the resource group that contains the cache or caches. If ResourceGroupName is provided with Name
            then Get-AzureRmRedisCache returns the details of the cache specified by Name. If only the ResourceGroup
            parameter is provided, then details for all caches in the resource group are returned.
    
        <CommonParameters>
            This cmdlet supports the common parameters: Verbose, Debug,
            ErrorAction, ErrorVariable, WarningAction, WarningVariable,
            OutBuffer, PipelineVariable, and OutVariable. For more information, see
            about_CommonParameters (http://go.microsoft.com/fwlink/?LinkID=113216).

Para obtener información acerca de la caché de todos en la suscripción actual, ejecute `Get-AzureRmRedisCache` sin parámetros.

    Get-AzureRmRedisCache

Para devolver información acerca de la caché de todos en un grupo de recursos específico, ejecute `Get-AzureRmRedisCache` con el `ResourceGroupName` parámetro.

    Get-AzureRmRedisCache -ResourceGroupName myGroup

Para obtener información sobre una caché específica, ejecute `Get-AzureRmRedisCache` con el `Name` parámetro que contiene el nombre de la memoria caché y la `ResourceGroupName` parámetro con el grupo de recursos que contiene dicha caché.

    PS C:\> Get-AzureRmRedisCache -Name myCache -ResourceGroupName myGroup
    
    Name               : mycache
    Id                 : /subscriptions/12ad12bd-abdc-2231-a2ed-a2b8b246bbad4/resourceGroups/myGroup/providers/Mi
                         crosoft.Cache/Redis/mycache
    Location           : South Central US
    Type               : Microsoft.Cache/Redis
    HostName           : mycache.redis.cache.windows.net
    Port               : 6379
    ProvisioningState  : Succeeded
    SslPort            : 6380
    RedisConfiguration : {[maxmemory-policy, volatile-lru], [maxmemory-reserved, 62], [notify-keyspace-events, KEA],
                         [maxclients, 1000]...}
    EnableNonSslPort   : False
    RedisVersion       : 3.0
    Size               : 1GB
    Sku                : Standard
    ResourceGroupName  : myGroup
    VirtualNetwork     :
    Subnet             :
    StaticIP           :
    TenantSettings     : {}
    ShardCount         :

## <a name="to-retrieve-the-access-keys-for-a-redis-cache"></a>Para recuperar las teclas de acceso para una caché Redis

Para recuperar las teclas de acceso de la memoria caché, puede usar el cmdlet [Get-AzureRmRedisCacheKey](https://msdn.microsoft.com/library/azure/mt634516.aspx) .

Para ver una lista de parámetros disponibles y sus descripciones de `Get-AzureRmRedisCacheKey`, ejecute el siguiente comando.

    PS C:\> Get-Help Get-AzureRmRedisCacheKey -detailed
    
    NAME
        Get-AzureRmRedisCacheKey
    
    SYNOPSIS
        Gets the accesskeys for the specified redis cache.
    
    
    SYNTAX
        Get-AzureRmRedisCacheKey -Name <String> -ResourceGroupName <String> [<CommonParameters>]
    
    DESCRIPTION
        The Get-AzureRmRedisCacheKey cmdlet gets the access keys for the specified cache.
    
    PARAMETERS
        -Name <String>
            Name of the redis cache.
    
        -ResourceGroupName <String>
            Name of the resource group for the cache.
    
        <CommonParameters>
            This cmdlet supports the common parameters: Verbose, Debug,
            ErrorAction, ErrorVariable, WarningAction, WarningVariable,
            OutBuffer, PipelineVariable, and OutVariable. For more information, see
            about_CommonParameters (http://go.microsoft.com/fwlink/?LinkID=113216).

Para recuperar las claves de la memoria caché, llame la `Get-AzureRmRedisCacheKey` cmdlet y pase el nombre de la memoria caché el nombre del grupo de recursos que contiene la memoria caché.

    PS C:\> Get-AzureRmRedisCacheKey -Name myCache -ResourceGroupName myGroup
    
    PrimaryKey   : b2wdt43sfetlju4hfbryfnregrd9wgIcc6IA3zAO1lY=
    SecondaryKey : ABhfB757JgjIgt785JgKH9865eifmekfnn649303JKL=

## <a name="to-regenerate-access-keys-for-your-redis-cache"></a>Volver a generar las teclas de acceso de la memoria caché Redis

Para volver a generar las teclas de acceso de la memoria caché, puede usar el cmdlet [AzureRmRedisCacheKey de nuevo](https://msdn.microsoft.com/library/azure/mt634512.aspx) .

Para ver una lista de parámetros disponibles y sus descripciones de `New-AzureRmRedisCacheKey`, ejecute el siguiente comando.

    PS C:\> Get-Help New-AzureRmRedisCacheKey -detailed
    
    NAME
        New-AzureRmRedisCacheKey
    
    SYNOPSIS
        Regenerates the access key of a redis cache.
    
    SYNTAX
        New-AzureRmRedisCacheKey -Name <String> -ResourceGroupName <String> -KeyType <String> [-Force] [<CommonParameters>]
    
    DESCRIPTION
        The New-AzureRmRedisCacheKey cmdlet regenerate the access key of a redis cache.
    
    PARAMETERS
        -Name <String>
            Name of the redis cache.
    
        -ResourceGroupName <String>
            Name of the resource group for the cache.
    
        -KeyType <String>
            Specifies whether to regenerate the primary or secondary access key. Possible values are Primary or Secondary.
    
        -Force
            When the Force parameter is provided, the specified access key is regenerated without any confirmation prompts.
    
        <CommonParameters>
            This cmdlet supports the common parameters: Verbose, Debug,
            ErrorAction, ErrorVariable, WarningAction, WarningVariable,
            OutBuffer, PipelineVariable, and OutVariable. For more information, see
            about_CommonParameters (http://go.microsoft.com/fwlink/?LinkID=113216).
    
Para regenerar la clave principal o secundaria de la memoria caché, llame a la `New-AzureRmRedisCacheKey` cmdlet y pasar el nombre de grupo de recursos y especifique `Primary` o `Secondary` para el `KeyType` parámetro. En el ejemplo siguiente, se regenera la tecla de acceso secundario para una caché.

    PS C:\> New-AzureRmRedisCacheKey -Name myCache -ResourceGroupName myGroup -KeyType Secondary
    
    Confirm
    Are you sure you want to regenerate Secondary key for redis cache 'myCache'?
    [Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): Y
    
    
    PrimaryKey   : b2wdt43sfetlju4hfbryfnregrd9wgIcc6IA3zAO1lY=
    SecondaryKey : c53hj3kh4jhHjPJk8l0jji785JgKH9865eifmekfnn6=

## <a name="to-delete-a-redis-cache"></a>Para eliminar una caché Redis

Para eliminar una caché Redis, use el cmdlet [Quitar AzureRmRedisCache](https://msdn.microsoft.com/library/azure/mt634515.aspx) .

Para ver una lista de parámetros disponibles y sus descripciones de `Remove-AzureRmRedisCache`, ejecute el siguiente comando.

    PS C:\> Get-Help Remove-AzureRmRedisCache -detailed
    
    NAME
        Remove-AzureRmRedisCache
    
    SYNOPSIS
        Remove redis cache if exists.
    
    SYNTAX
        Remove-AzureRmRedisCache -Name <String> -ResourceGroupName <String> [-Force] [-PassThru] [<CommonParameters>
    
    DESCRIPTION
        The Remove-AzureRmRedisCache cmdlet removes a redis cache if it exists.
    
    PARAMETERS
        -Name <String>
            Name of the redis cache to remove.
    
        -ResourceGroupName <String>
            Name of the resource group of the cache to remove.
    
        -Force
            When the Force parameter is provided, the cache is removed without any confirmation prompts.
    
        -PassThru
            By default Remove-AzureRmRedisCache removes the cache and does not return any value. If the PassThru par
            is provided then Remove-AzureRmRedisCache returns a boolean value indicating the success of the operatio
    
        <CommonParameters>
            This cmdlet supports the common parameters: Verbose, Debug,
            ErrorAction, ErrorVariable, WarningAction, WarningVariable,
            OutBuffer, PipelineVariable, and OutVariable. For more information, see
            about_CommonParameters (http://go.microsoft.com/fwlink/?LinkID=113216).

En el ejemplo siguiente, la caché denominado `myCache` se quita.

    PS C:\> Remove-AzureRmRedisCache -Name myCache -ResourceGroupName myGroup
    
    Confirm
    Are you sure you want to remove redis cache 'myCache'?
    [Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): Y


## <a name="to-import-a-redis-cache"></a>Para importar una caché Redis

Puede importar datos en una instancia de Azure Redis caché mediante la `Import-AzureRmRedisCache` cmdlet.

>[AZURE.IMPORTANT] Importar o exportar solo está disponible para la caché de [nivel premium](cache-premium-tier-intro.md) . Para obtener más información sobre cómo importar o exportar, vea [Importar y exportar datos de Azure Redis caché](cache-how-to-import-export-data.md).

Para ver una lista de parámetros disponibles y sus descripciones de `Import-AzureRmRedisCache`, ejecute el siguiente comando.

    PS C:\> Get-Help Import-AzureRmRedisCache -detailed
    
    NAME
        Import-AzureRmRedisCache
    
    SYNOPSIS
        Import data from blobs to Azure Redis Cache.
    
    
    SYNTAX
        Import-AzureRmRedisCache -Name <String> -ResourceGroupName <String> -Files <String[]> [-Format <String>] [-Force]
        [-PassThru] [<CommonParameters>]
    
    
    DESCRIPTION
        The Import-AzureRmRedisCache cmdlet imports data from the specified blobs into Azure Redis Cache.
    
    
    PARAMETERS
        -Name <String>
            The name of the cache.
    
        -ResourceGroupName <String>
            The name of the resource group that contains the cache.
    
        -Files <String[]>
            SAS urls of blobs whose content should be imported into the cache.
    
        -Format <String>
            Format for the blob.  Currently "rdb" is the only supported, with other formats expected in the future.
    
        -Force
            When the Force parameter is provided, import will be performed without any confirmation prompts.
    
        -PassThru
            By default Import-AzureRmRedisCache imports data in cache and does not return any value. If the PassThru
            parameter is provided then Import-AzureRmRedisCache returns a boolean value indicating the success of the
            operation.
    
        <CommonParameters>
            This cmdlet supports the common parameters: Verbose, Debug,
            ErrorAction, ErrorVariable, WarningAction, WarningVariable,
            OutBuffer, PipelineVariable, and OutVariable. For more information, see
            about_CommonParameters (http://go.microsoft.com/fwlink/?LinkID=113216).
    

El siguiente comando importa datos desde el blob especificado por el uri de SA en Azure Redis caché.


    PS C:\>Import-AzureRmRedisCache -ResourceGroupName "resourceGroupName" -Name "cacheName" -Files @("https://mystorageaccount.blob.core.windows.net/mycontainername/blobname?sv=2015-04-05&sr=b&sig=caIwutG2uDa0NZ8mjdNJdgOY8%2F8mhwRuGNdICU%2B0pI4%3D&st=2016-05-27T00%3A00%3A00Z&se=2016-05-28T00%3A00%3A00Z&sp=rwd") -Force

## <a name="to-export-a-redis-cache"></a>Para exportar una caché Redis

Puede exportar datos de una instancia de Azure Redis caché mediante la `Export-AzureRmRedisCache` cmdlet.

>[AZURE.IMPORTANT] Importar o exportar solo está disponible para la caché de [nivel premium](cache-premium-tier-intro.md) . Para obtener más información sobre cómo importar o exportar, vea [Importar y exportar datos de Azure Redis caché](cache-how-to-import-export-data.md).

Para ver una lista de parámetros disponibles y sus descripciones de `Export-AzureRmRedisCache`, ejecute el siguiente comando.

    PS C:\> Get-Help Export-AzureRmRedisCache -detailed
    
    NAME
        Export-AzureRmRedisCache
    
    SYNOPSIS
        Exports data from Azure Redis Cache to a specified container.
    
    
    SYNTAX
        Export-AzureRmRedisCache -Name <String> -ResourceGroupName <String> -Prefix <String> -Container <String> [-Format
        <String>] [-PassThru] [<CommonParameters>]
    
    
    DESCRIPTION
        The Export-AzureRmRedisCache cmdlet exports data from Azure Redis Cache to a specified container.
    
    
    PARAMETERS
        -Name <String>
            The name of the cache.
    
        -ResourceGroupName <String>
            The name of the resource group that contains the cache.
    
        -Prefix <String>
            Prefix to use for blob names.
    
        -Container <String>
            SAS url of container where data should be exported.
    
        -Format <String>
            Format for the blob.  Currently "rdb" is the only supported, with other formats expected in the future.
    
        -PassThru
            By default Export-AzureRmRedisCache does not return any value. If the PassThru parameter is provided
            then Export-AzureRmRedisCache returns a boolean value indicating the success of the operation.
    
        <CommonParameters>
            This cmdlet supports the common parameters: Verbose, Debug,
            ErrorAction, ErrorVariable, WarningAction, WarningVariable,
            OutBuffer, PipelineVariable, and OutVariable. For more information, see
            about_CommonParameters (http://go.microsoft.com/fwlink/?LinkID=113216).


El siguiente comando exporta datos desde una instancia de Azure Redis caché en el contenedor especificado por el uri de SA.


        PS C:\>Export-AzureRmRedisCache -ResourceGroupName "resourceGroupName" -Name "cacheName" -Prefix "blobprefix"
        -Container "https://mystorageaccount.blob.core.windows.net/mycontainer?sv=2015-04-05&sr=c&sig=HezZtBZ3DURmEGDduauE7
        pvETY4kqlPI8JCNa8ATmaw%3D&st=2016-05-27T00%3A00%3A00Z&se=2016-05-28T00%3A00%3A00Z&sp=rwdl"

## <a name="to-reboot-a-redis-cache"></a>Reiniciar una caché Redis

Puede reiniciar la instancia de Azure Redis caché utilizando la `Reset-AzureRmRedisCache` cmdlet.

>[AZURE.IMPORTANT] Reinicie solo está disponible para la caché de [nivel premium](cache-premium-tier-intro.md) . Para obtener más información sobre cómo reiniciar la memoria caché, vea [administración de la memoria caché - reiniciar](cache-administration.md#reboot).

Para ver una lista de parámetros disponibles y sus descripciones de `Reset-AzureRmRedisCache`, ejecute el siguiente comando.

    PS C:\> Get-Help Reset-AzureRmRedisCache -detailed
    
    NAME
        Reset-AzureRmRedisCache
    
    SYNOPSIS
        Reboot specified node(s) of an Azure Redis Cache instance.
    
    
    SYNTAX
        Reset-AzureRmRedisCache -Name <String> -ResourceGroupName <String> -RebootType <String> [-ShardId <Integer>]
        [-Force] [-PassThru] [<CommonParameters>]
    
    
    DESCRIPTION
        The Reset-AzureRmRedisCache cmdlet reboots the specified node(s) of an Azure Redis Cache instance.
    
    
    PARAMETERS
        -Name <String>
            The name of the cache.
    
        -ResourceGroupName <String>
            The name of the resource group that contains the cache.
    
        -RebootType <String>
            Which node to reboot. Possible values are "PrimaryNode", "SecondaryNode", "AllNodes".
    
        -ShardId <Integer>
            Which shard to reboot when rebooting a premium cache with clustering enabled.
    
        -Force
            When the Force parameter is provided, reset will be performed without any confirmation prompts.
    
        -PassThru
            By default Reset-AzureRmRedisCache does not return any value. If the PassThru parameter is provided
            then Reset-AzureRmRedisCache returns a boolean value indicating the success of the operation.
    
        <CommonParameters>
            This cmdlet supports the common parameters: Verbose, Debug,
            ErrorAction, ErrorVariable, WarningAction, WarningVariable,
            OutBuffer, PipelineVariable, and OutVariable. For more information, see
            about_CommonParameters (http://go.microsoft.com/fwlink/?LinkID=113216).
    

El siguiente comando reinicia ambos nodos de la memoria caché especificada.

    
        PS C:\>Reset-AzureRmRedisCache -ResourceGroupName "resourceGroupName" -Name "cacheName" -RebootType "AllNodes"
        -Force
    

## <a name="next-steps"></a>Pasos siguientes

Para obtener más información sobre cómo usar Windows PowerShell con Azure, consulte los siguientes recursos:

- [Documentación de cmdlet de caché Redis Azure en MSDN](https://msdn.microsoft.com/library/azure/mt634513.aspx)
- [Cmdlets de administrador de recursos de Azure](http://go.microsoft.com/fwlink/?LinkID=394765): Aprenda a usar los cmdlets en el módulo AzureResourceManager.
- [Grupos de uso de recursos para administrar los recursos de Azure](../resource-group-template-deploy-portal.md): Obtenga información sobre cómo crear y administrar grupos de recursos en el portal de Azure.
- [Blog de Azure](http://blogs.msdn.com/windowsazure): Obtenga más información sobre las nuevas características de Azure.
- [Blog de Windows PowerShell](http://blogs.msdn.com/powershell): Obtenga más información sobre las nuevas características de Windows PowerShell.
- ["¡Hola, encargados!" Blog](http://blogs.technet.com/b/heyscriptingguy/): obtener reales sugerencias y trucos de la Comunidad de Windows PowerShell.
