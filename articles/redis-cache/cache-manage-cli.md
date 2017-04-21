<properties 
    pageTitle="Cómo crear y administrar mediante la interfaz de línea de comandos de Azure (Azure CLI) de caché Redis Azure | Microsoft Azure" 
    description="Obtenga información sobre cómo instalar la CLI de Azure en cualquier plataforma, cómo usarlo para conectarse a su cuenta de Azure y cómo crear y administrar una caché Redis desde la CLI de Azure." 
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
    ms.date="09/15/2016" 
    ms.author="sdanie"/>

# <a name="how-to-create-and-manage-azure-redis-cache-using-the-azure-command-line-interface-azure-cli"></a>Cómo crear y administrar la caché de Azure Redis mediante la interfaz de línea de comandos de Azure (Azure CLI)

> [AZURE.SELECTOR]
- [PowerShell](cache-howto-manage-redis-cache-powershell.md)
- [CLI de Azure](cache-manage-cli.md)

CLI Azure es una excelente forma de administrar su infraestructura de Azure desde cualquier plataforma. Este artículo muestra cómo crear y administrar las instancias de Azure Redis caché mediante la CLI de Azure.

## <a name="prerequisites"></a>Requisitos previos

Para crear y administrar instancias de Azure Redis caché mediante CLI de Azure, debe completar los pasos siguientes.

-   Debe tener una cuenta de Azure. Si no tiene una, puede crear una [cuenta gratuita](https://azure.microsoft.com/pricing/free-trial/) en unos minutos.
-   [Instalar la CLI de Azure](../xplat-cli-install.md).
-   Conectar la instalación de CLI de Azure con una cuenta personal de Azure o con un trabajo o Azure cuenta escolar e iniciar sesión desde la CLI de Azure mediante la `azure login` comando. Para conocer las diferencias y elegir, vea [conectarse a una suscripción de Azure desde la interfaz de línea de comandos de Azure (Azure CLI)](../xplat-cli-connect.md).
-   Antes de ejecutar cualquiera de los siguientes comandos, cambiar la CLI de Azure en modo de administrador de recursos, ejecute el `azure config mode arm` comando. Para obtener más información, vea [establecer el modo de administrador de recursos de Azure](../xplat-cli-azure-resource-manager.md#set-the-azure-resource-manager-mode).

## <a name="redis-cache-properties"></a>Redis propiedades de caché

Las siguientes propiedades se usan al crear y actualizar la memoria caché Redis instancias.

| (Propiedad)            | Cambiar                                  | Descripción                                                                                                                                                                                                                                          |
|---------------------|-----------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| nombre                | -n,--nombre                              | Nombre de la memoria caché Redis.                                                                                                                                                                                                                             |
| grupo de recursos      | -g,--grupo de recursos                    | Nombre del grupo de recursos.                                                                                                                                                                                                                          |
| ubicación            | -l,--ubicación                          | Ubicación para crear la caché.                                                                                                                                                                                                                            |
| tamaño                | -z,--tamaño                              | Tamaño de la caché Redis. Valores válidos: [C0, C1, C2, C3, C4, C5, C6, P1, P2, P3, P4]                                                                                                                                                                  |
| SKU                 | -x, sku:                               | Redis SKU. Debe ser uno de: [básico, estándar, Premium]                                                                                                                                                                                             |
| EnableNonSslPort    | -e,--habilitar-no--puerto ssl               | Propiedad EnableNonSslPort de la caché Redis. Agregar este marcador si desea habilitar el puerto SSL no la memoria caché                                                                                                                                    |
| Redis configuración | -c,--configuración redis               | Redis configuración. Escriba una cadena JSON con formato de las claves de configuración y valores aquí. Formato: "{" ":""," ":" "}"                                                                                                                                     |
| Redis configuración | -f,--archivo de configuración redis          | Redis configuración. Escriba la ruta de acceso de un archivo que contiene las claves de configuración y valores aquí. Formato de la entrada del archivo: {"": "","": ""}                                                                                                                |
| Recuento de partes         | -r,--fragmentar recuento                       | Número de Shards para crear en una caché de clúster Premium con clústeres.                                                                                                                                                                               |
| Red virtual     | -v,--red virtual                   | Cuando la memoria caché en un VNET de hospedaje, especifica el identificador de recursos ARM exacto de la red virtual para implementar la caché redis en. Ejemplo de formato: /subscriptions/{subid}/resourceGroups/{resourceGroupName}/Microsoft.ClassicNetwork/VirtualNetworks/vnet1 |
| tipo de clave            | -t,--tipo de clave                          | Tipo de clave para renovar. Valores válidos: [principal y secundario]                                                                                                                                                                                             |
| StaticIP            | -p,--ip estática < ip estática >             | Cuando la memoria caché en un VNET de hospedaje, especifica una dirección IP única en la subred de la memoria caché. Si no se proporciona uno ya está seleccionada de la subred.                                                                                                |
| Subred              | t,--subred<subnet>                    | Cuando la memoria caché en un VNET de hospedaje, especifica el nombre de la subred en la que se va a implementar la caché.                                                                                                                                                    |
| VirtualNetwork      | -v,--virtual-< virtual red > | Cuando la memoria caché en un VNET de hospedaje, especifica el identificador de recursos ARM exacto de la red virtual para implementar la caché redis en. Ejemplo de formato: /subscriptions/{subid}/resourceGroups/{resourceGroupName}/Microsoft.ClassicNetwork/VirtualNetworks/vnet1 |
| Suscripción        | -s,--suscripción                      | Identificador de la suscripción.                                                                                                                                                                                                                         |

## <a name="see-all-redis-cache-commands"></a>Ver todos los comandos de caché Redis

Para ver todos los comandos de caché Redis y sus parámetros, use la `azure rediscache -h` comando.

    C:\>azure rediscache -h
    help:    Commands to manage your Azure Redis Cache(s)
    help:
    help:    Create a Redis Cache
    help:      rediscache create [--name <name> --resource-group <resource-group> --location <location> [options]]
    help:
    help:    Delete an existing Redis Cache
    help:      rediscache delete [--name <name> --resource-group <resource-group> ]
    help:
    help:    List all Redis Caches within your Subscription or Resource Group
    help:      rediscache list [options]
    help:
    help:    Show properties of an existing Redis Cache
    help:      rediscache show [--name <name> --resource-group <resource-group>]
    help:
    help:    Change settings of an existing Redis Cache
    help:      rediscache set [--name <name> --resource-group <resource-group> --redis-configuration <redis-configuration>/--redis-configuration-file <redisConfigurationFile>]
    help:
    help:    Renew the authentication key for an existing Redis Cache
    help:      rediscache renew-key [--name <name> --resource-group <resource-group> ]
    help:
    help:    Lists Primary and Secondary key of an existing Redis Cache
    help:      rediscache list-keys [--name <name> --resource-group <resource-group>]
    help:
    help:    Options:
    help:      -h, --help  output usage information
    help:
    help:    Current Mode: arm (Azure Resource Management)

## <a name="create-a-redis-cache"></a>Crear una caché Redis

Para crear una caché Redis, utilice el siguiente comando:

    azure rediscache create [--name <name> --resource-group <resource-group> --location <location> [options]]

Para obtener más información sobre este comando, ejecute el `azure rediscache create -h` comando.

    C:\>azure rediscache create -h
    help:    Create a Redis Cache
    help:
    help:    Usage: rediscache create [--name <name> --resource-group <resource-group> --location <location> [options]]
    help:
    help:    Options:
    help:      -h, --help                                               output usage information
    help:      -v, --verbose                                            use verbose output
    help:      -vv                                                      more verbose with debug output
    help:      --json                                                   use json output
    help:      -n, --name <name>                                        Name of the Redis Cache.
    help:      -g, --resource-group <resource-group>                    Name of the Resource Group
    help:      -l, --location <location>                                Location to create cache.
    help:      -z, --size <size>                                        Size of the Redis Cache. Valid values: [C0, C1, C2, C3, C4, C5, C6, P1, P2, P3, P4]
    help:      -x, --sku <sku>                                          Redis SKU. Should be one of : [Basic, Standard, Premium]
    help:      -e, --enable-non-ssl-port                                EnableNonSslPort property of the Redis Cache. Add this flag if you want to enable the Non SSL Port for your cache
    help:      -c, --redis-configuration <redis-configuration>          Redis Configuration. Enter a JSON formatted string of configuration keys and values here. Format:"{"<key1>":"<value1>","<key2>":"<value2>"}"
    help:      -f, --redis-configuration-file <redisConfigurationFile>  Redis Configuration. Enter the path of a file containing configuration keys and values here. Format for the file entry: {"<key1>":"<value1>","<key2>":"<value2>"}
    help:      -r, --shard-count <shard-count>                          Number of Shards to create on a Premium Cluster Cache
    help:      -v, --virtual-network <virtual-network>                  The exact ARM resource ID of the virtual network to deploy the redis cache in. Example format: /subscriptions/{subid}/resourceGroups/{resourceGroupName}/Microsoft.ClassicNetwork/VirtualNetworks/vnet1
    help:      -t, --subnet <subnet>                                    Required when deploying a redis cache inside an existing Azure Virtual Network
    help:      -p, --static-ip <static-ip>                              Required when deploying a redis cache inside an existing Azure Virtual Network
    help:      -s, --subscription <id>                                  the subscription identifier
    help:
    help:    Current Mode: arm (Azure Resource Management)

## <a name="delete-an-existing-redis-cache"></a>Eliminar una caché Redis existente

Para eliminar una caché Redis, utilice el siguiente comando:

    azure rediscache delete [--name <name> --resource-group <resource-group> ]

Para obtener más información sobre este comando, ejecute el `azure rediscache delete -h` comando.

    C:\>azure rediscache delete -h
    help:    Delete an existing Redis Cache
    help:
    help:    Usage: rediscache delete [--name <name> --resource-group <resource-group> ]
    help:
    help:    Options:
    help:      -h, --help                             output usage information
    help:      -v, --verbose                          use verbose output
    help:      -vv                                    more verbose with debug output
    help:      --json                                 use json output
    help:      -n, --name <name>                      Name of the Redis Cache.
    help:      -g, --resource-group <resource-group>  Name of the Resource Group under which the cache exists
    help:      -s, --subscription <subscription>      the subscription identifier
    help:
    help:    Current Mode: arm (Azure Resource Management)

## <a name="list-all-redis-caches-within-your-subscription-or-resource-group"></a>Lista de todas las caché Redis dentro de la suscripción o grupo de recursos

Para mostrar todas las caché Redis dentro de la suscripción o grupo de recursos, utilice el siguiente comando:

    azure rediscache list [options]

Para obtener más información sobre este comando, ejecute el `azure rediscache list -h` comando.

    C:\>azure rediscache list -h
    help:    List all Redis Caches within your Subscription or Resource Group
    help:
    help:    Usage: rediscache list [options]
    help:
    help:    Options:
    help:      -h, --help                             output usage information
    help:      -v, --verbose                          use verbose output
    help:      -vv                                    more verbose with debug output
    help:      --json                                 use json output
    help:      -g, --resource-group <resource-group>  Name of the Resource Group
    help:      -s, --subscription <subscription>      the subscription identifier
    help:
    help:    Current Mode: arm (Azure Resource Management)

## <a name="show-properties-of-an-existing-redis-cache"></a>Mostrar propiedades de una caché Redis existente

Para mostrar las propiedades de una caché Redis existente, utilice el siguiente comando:

    azure rediscache show [--name <name> --resource-group <resource-group>]

Para obtener más información sobre este comando, ejecute el `azure rediscache show -h` comando.

    C:\>azure rediscache show -h
    help:    Show properties of an existing Redis Cache
    help:
    help:    Usage: rediscache show [--name <name> --resource-group <resource-group>]
    help:
    help:    Options:
    help:      -h, --help                             output usage information
    help:      -v, --verbose                          use verbose output
    help:      -vv                                    more verbose with debug output
    help:      --json                                 use json output
    help:      -n, --name <name>                      Name of the Redis Cache.
    help:      -g, --resource-group <resource-group>  Name of the Resource Group
    help:      -s, --subscription <subscription>      the subscription identifier
    help:
    help:    Current Mode: arm (Azure Resource Management)

<a name="scale"></a>
## <a name="change-settings-of-an-existing-redis-cache"></a>Cambiar la configuración de una caché Redis existente

Para cambiar la configuración de una caché Redis existente, utilice el siguiente comando:

    azure rediscache set [--name <name> --resource-group <resource-group> --redis-configuration <redis-configuration>/--redis-configuration-file <redisConfigurationFile>]

Para obtener más información sobre este comando, ejecute el `azure rediscache set -h` comando.

    C:\>azure rediscache set -h
    help:    Change settings of an existing Redis Cache
    help:
    help:    Usage: rediscache set [--name <name> --resource-group <resource-group> --redis-configuration <redis-configuration>/--redis-configuration-file <redisConfigurationFile>]
    help:
    help:    Options:
    help:      -h, --help                                               output usage information
    help:      -v, --verbose                                            use verbose output
    help:      -vv                                                      more verbose with debug output
    help:      --json                                                   use json output
    help:      -n, --name <name>                                        Name of the Redis Cache.
    help:      -g, --resource-group <resource-group>                    Name of the Resource Group
    help:      -c, --redis-configuration <redis-configuration>          Redis Configuration. Enter a JSON formatted string of configuration keys and values here.
    help:      -f, --redis-configuration-file <redisConfigurationFile>  Redis Configuration. Enter the path of a file containing configuration keys and values here.
    help:      -s, --subscription <subscription>                        the subscription identifier
    help:
    help:    Current Mode: arm (Azure Resource Management)

## <a name="renew-the-authentication-key-for-an-existing-redis-cache"></a>Renovar la clave de autenticación para una caché Redis existente

Para renovar la clave de autenticación para una caché Redis existente, utilice el siguiente comando:

    azure rediscache renew-key [--name <name> --resource-group <resource-group> --key-type <key-type>]

Especificar `Primary` o `Secondary` para `key-type`.

Para obtener más información sobre este comando, ejecute el `azure rediscache renew-key -h` comando.

    C:\>azure rediscache renew-key -h
    help:    Renew the authentication key for an existing Redis Cache
    help:
    help:    Usage: rediscache renew-key [--name <name> --resource-group <resource-group> ]
    help:
    help:    Options:
    help:      -h, --help                             output usage information
    help:      -v, --verbose                          use verbose output
    help:      -vv                                    more verbose with debug output
    help:      --json                                 use json output
    help:      -n, --name <name>                      Name of the Redis Cache.
    help:      -g, --resource-group <resource-group>  Name of the Resource Group under which cache exists
    help:      -t, --key-type <key-type>              type of key to renew. Valid values are: 'Primary', 'Secondary'.
    help:      -s, --subscription <subscription>      the subscription identifier
    help:
    help:    Current Mode: arm (Azure Resource Management)

## <a name="list-primary-and-secondary-keys-of-an-existing-redis-cache"></a>Teclas de lista principal y secundario de una caché Redis existente

Lista claves principales y secundarias de una caché Redis existente, use el siguiente comando:

    azure rediscache list-keys [--name <name> --resource-group <resource-group>]

Para obtener más información sobre este comando, ejecute el `azure rediscache list-keys -h` comando.

    C:\>azure rediscache list-keys -h
    help:    Lists Primary and Secondary key of an existing Redis Cache
    help:
    help:    Usage: rediscache list-keys [--name <name> --resource-group <resource-group>]
    help:
    help:    Options:
    help:      -h, --help                             output usage information
    help:      -v, --verbose                          use verbose output
    help:      -vv                                    more verbose with debug output
    help:      --json                                 use json output
    help:      -n, --name <name>                      Name of the Redis Cache.
    help:      -g, --resource-group <resource-group>  Name of the Resource Group under which Cache exists
    help:      -s, --subscription <subscription>      the subscription identifier
    help:
    help:    Current Mode: arm (Azure Resource Management)
