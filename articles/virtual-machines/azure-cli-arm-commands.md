<properties
    pageTitle="Los comandos CLI Azure en modo de administrador de recursos | Microsoft Azure"
    description="Comandos de Azure interfaz de línea de comandos (CLI) para administrar los recursos en el modelo de implementación de administrador de recursos"
    services="virtual-machines-linux,virtual-machines-windows,virtual-network,mobile-services,cloud-services"
    documentationCenter=""
    authors="dlepow"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="multiple"
    ms.workload="multiple"
    ms.tgt_pltfrm="command-line-interface"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/05/2016"
    ms.author="danlep"/>

# <a name="azure-cli-commands-in-resource-manager-mode"></a>Comandos CLI Azure en modo de administrador de recursos

Este artículo proporciona la sintaxis y las opciones para los comandos de Azure de línea de comandos interfaz que normalmente se utiliza para crear y administrar recursos de Azure en el modelo de implementación de administrador de recursos de Azure. Obtener acceso a estos comandos ejecuta la CLI en modo de administrador de recursos (arm). No es una referencia completa y su versión CLI puede mostrar comandos ligeramente diferentes o parámetros. Para obtener una descripción general de Azure y grupos de recursos, vea [Información general del Administrador de recursos de Azure](../azure-resource-manager/resource-group-overview.md).  

Para comenzar, primera [instalar CLI Azure](../xplat-cli-install.md) y [conectarse a su suscripción de Azure](../xplat-cli-connect.md) mediante un trabajo o escuela o una identidad de la cuenta de Microsoft.

La sintaxis de comando actual y opciones en la línea de comandos en modo de administrador de recursos, escriba `azure help` o, para mostrar la ayuda para un comando específico, `azure help [command]`. También encontrará ejemplos CLI en la documentación para crear y administrar los servicios de Azure específicos.

Parámetros opcionales se muestran entre corchetes (por ejemplo, `[parameter]`). Todos los demás parámetros son necesarios.

Además de los parámetros opcionales específicos del comando documentados aquí, hay tres parámetros opcionales que se pueden usar para mostrar el resultado detallado como opciones de convocatoria y códigos de estado. La `-v` parámetro proporciona un resultado detallado y la `-vv` parámetro proporciona un resultado detallado incluso más detallado. La `--json` opción genera el resultado en formato json sin formato.

## <a name="setting-the-resource-manager-mode"></a>Establecer el modo de administrador de recursos

Use el comando siguiente para habilitar los comandos de modo de administrador de recursos de Azure CLI.

    azure config mode arm

>[AZURE.NOTE] Modo de administrador de recursos de Azure y administración de servicios de Azure la CLI son mutuamente. Es decir, no se puede administrar recursos creados en un modo de otro modo.


## <a name="azure-account-manage-your-account-information"></a>cuenta de Azure: administrar la información de cuenta
Información de su suscripción de Azure se usa la herramienta para conectarse a su cuenta.

**Lista de las suscripciones importadas**

    account list [options]

**Mostrar detalles acerca de la suscripción**  

    account show [options] [subscriptionNameOrId]

**Establecer la suscripción actual**

    account set [options] <subscriptionNameOrId>

**Quitar un entorno o suscripción o borrar todo de la información de cuenta y el entorno almacenada**  

    account clear [options]

**Comandos para administrar su entorno de cuenta**  

    account env list [options]
    account env show [options] [environment]
    account env add [options] [environment]
    account env set [options] [environment]
    account env delete [options] [environment]

## <a name="azure-ad-commands-to-display-active-directory-objects"></a>Azure ad: comandos para mostrar los objetos de Active Directory

**Comandos para mostrar aplicaciones de active directory**

    ad app create [options]
    ad app delete [options] <object-id>

**Comandos para mostrar los grupos de active directory**

    ad group list [options]
    ad group show [options]

**Comandos para proporcionar una información de grupo o miembro de sub de active directory**

    ad group member list [options] [objectId]

**Comandos para mostrar a principales de servicio de active directory**

    ad sp list [options]
    ad sp show [options]
    ad sp create [options] <application-id>
    ad sp delete [options] <object-id>

**Comandos para mostrar los usuarios de active directory**

    ad user list [options]
    ad user show [options]

## <a name="azure-availset-commands-to-manage-your-availability-sets"></a>Azure availset: comandos para administrar los conjuntos de disponibilidad

**Crea una disponibilidad establecer dentro de un grupo de recursos**

    availset create [options] <resource-group> <name> <location> [tags]

**Enumera los conjuntos de disponibilidad de un grupo de recursos**

    availset list [options] <resource-group>

**Obtiene una disponibilidad establecer dentro de un grupo de recursos**

    availset show [options] <resource-group> <name>

**Elimina la disponibilidad de un conjunto dentro de un grupo de recursos**

    availset delete [options] <resource-group> <name>

## <a name="azure-config-commands-to-manage-your-local-settings"></a>configuración de Azure: comandos para administrar la configuración local

**Configuración de lista CLI de Azure**

    config list [options]

**Eliminar una opción de configuración**

    config delete [options] <name>

**Actualizar una opción de configuración**

    config set <name> <value>

**Establece el modo de trabajo de Azure CLI: `arm` o`asm`**

    config mode [options] <modename>


## <a name="azure-feature-commands-to-manage-account-features"></a>característica de Azure: comandos para administrar las características de la cuenta

**Lista de todas las características disponibles para su suscripción**

    feature list [options]

**Muestra una característica**

    feature show [options] <providerName> <featureName>

**Registra la característica Vista previa de un proveedor de recursos**

    feature register [options] <providerName> <featureName>

## <a name="azure-group-commands-to-manage-your-resource-groups"></a>grupo de Azure: comandos para administrar los grupos de recursos

**Crea un grupo de recursos**

    group create [options] <name> <location>

**Establecer etiquetas a un grupo de recursos**

    group set [options] <name> <tags>

**Elimina un grupo de recursos**

    group delete [options] <name>

**Enumera los grupos de recursos para la suscripción**

    group list [options]

**Muestra un grupo de recursos para la suscripción**

    group show [options] <name>

**Comandos para administrar registros de grupo de recursos**

    group log show [options] [name]

**Comandos para administrar la implementación de un grupo de recursos**

    group deployment create [options] [resource-group] [name]
    group deployment list [options] <resource-group> [state]
    group deployment show [options] <resource-group> [deployment-name]
    group deployment stop [options] <resource-group> [deployment-name]

**Comandos para administrar la plantilla de grupo de recursos local o la Galería**

    group template list [options]
    group template show [options] <name>
    group template download [options] [name] [file]
    group template validate [options] <resource-group>

## <a name="azure-hdinsight-commands-to-manage-your-hdinsight-clusters"></a>hdinsight de Azure: comandos para administrar los clústeres HDInsight

**Comandos para crear o agregar a un archivo de configuración de clúster**

    hdinsight config create [options] <configFilePath> <overwrite>
    hdinsight config add-config-values [options] <configFilePath>
    hdinsight config add-script-action [options] <configFilePath>

Ejemplo: Crear un archivo de configuración que contenga una acción de secuencia de comandos para que se ejecute al crear un clúster.

    hdinsight config create "C:\myFiles\configFile.config"
    hdinsight config add-script-action --configFilePath "C:\myFiles\configFile.config" --nodeType HeadNode --uri <scriptActionURI> --name myScriptAction --parameters "-param value"

**Comando para crear un clúster de un grupo de recursos**

    hdinsight cluster create [options] <clusterName>

Ejemplo: Crear una tormenta en clúster de Linux

    azure hdinsight cluster create -g myarmgroup -l westus -y Linux --clusterType Storm --version 3.2 --defaultStorageAccountName mystorageaccount --defaultStorageAccountKey <defaultStorageAccountKey> --defaultStorageContainer mycontainer --userName admin --password <clusterPassword> --sshUserName sshuser --sshPassword <sshPassword> --workerNodeCount 1 myNewCluster01

    info:    Executing command hdinsight cluster create
    + Submitting the request to create cluster...
    info:    hdinsight cluster create command OK

Ejemplo: Crear un clúster con una acción de secuencia de comandos

    azure hdinsight cluster create -g myarmgroup -l westus -y Linux --clusterType Hadoop --version 3.2 --defaultStorageAccountName mystorageaccount --defaultStorageAccountKey <defaultStorageAccountKey> --defaultStorageContainer mycontainer --userName admin --password <clusterPassword> --sshUserName sshuser --sshPassword <sshPassword> --workerNodeCount 1 –configurationPath "C:\myFiles\configFile.config" myNewCluster01

    info:    Executing command hdinsight cluster create
    + Submitting the request to create cluster...
    info:    hdinsight cluster create command OK

Opciones de parámetro:

    -h, --help                                                 output usage information
    -v, --verbose                                              use verbose output
    -vv                                                        more verbose with debug output
    --json                                                     use json output
    -g --resource-group <resource-group>                       The name of the resource group
    -c, --clusterName <clusterName>                            HDInsight cluster name
    -l, --location <location>                                  Data center location for the cluster
    -y, --osType <osType>                                      HDInsight cluster operating system
    'Windows' or 'Linux'
    --version <version>                                        HDInsight cluster version
    --clusterType <clusterType>                                HDInsight cluster type.
    Hadoop | HBase | Spark | Storm
    --defaultStorageAccountName <storageAccountName>           Storage account url to use for default HDInsight storage
    --defaultStorageAccountKey <storageAccountKey>             Key to the storage account to use for default HDInsight storage
    --defaultStorageContainer <storageContainer>               Container in the storage account to use for HDInsight default storage
    --headNodeSize <headNodeSize>                              (Optional) Head node size for the cluster
    --workerNodeCount <workerNodeCount>                        Number of worker nodes to use for the cluster
    --workerNodeSize <workerNodeSize>                          (Optional) Worker node size for the cluster)
    --zookeeperNodeSize <zookeeperNodeSize>                    (Optional) Zookeeper node size for the cluster
    --userName <userName>                                      Cluster username
    --password <password>                                      Cluster password
    --sshUserName <sshUserName>                                SSH username (only for Linux clusters)
    --sshPassword <sshPassword>                                SSH password (only for Linux clusters)
    --sshPublicKey <sshPublicKey>                              SSH public key (only for Linux clusters)
    --rdpUserName <rdpUserName>                                RDP username (only for Windows clusters)
    --rdpPassword <rdpPassword>                                RDP password (only for Windows clusters)
    --rdpAccessExpiry <rdpAccessExpiry>                        RDP access expiry.
    For example 12/12/2015 (only for Windows clusters)
    --virtualNetworkId <virtualNetworkId>                      (Optional) Virtual network ID for the cluster.
    Value is a GUID for Windows cluster and ARM resource ID for Linux cluster)
    --subnetName <subnetName>                                  (Optional) Subnet for the cluster
    --additionalStorageAccounts <additionalStorageAccounts>    (Optional) Additional storage accounts.
    Can be multiple.
    In the format of 'accountName#accountKey'.
    For example, --additionalStorageAccounts "acc1#key1;acc2#key2"
    --hiveMetastoreServerName <hiveMetastoreServerName>        (Optional) SQL Server name for the external metastore for Hive
    --hiveMetastoreDatabaseName <hiveMetastoreDatabaseName>    (Optional) Database name for the external metastore for Hive
    --hiveMetastoreUserName <hiveMetastoreUserName>            (Optional) Database username for the external metastore for Hive
    --hiveMetastorePassword <hiveMetastorePassword>            (Optional) Database password for the external metastore for Hive
    --oozieMetastoreServerName <oozieMetastoreServerName>      (Optional) SQL Server name for the external metastore for Oozie
    --oozieMetastoreDatabaseName <oozieMetastoreDatabaseName>  (Optional) Database name for the external metastore for Oozie
    --oozieMetastoreUserName <oozieMetastoreUserName>          (Optional) Database username for the external metastore for Oozie
    --oozieMetastorePassword <oozieMetastorePassword>          (Optional) Database password for the external metastore for Oozie
    --configurationPath <configurationPath>                    (Optional) HDInsight cluster configuration file path
    -s, --subscription <id>                                    The subscription id
    --tags <tags>                                              Tags to set to the cluster.
    Can be multiple.
    In the format of 'name=value'.
    Name is required and value is optional.
    For example, --tags tag1=value1;tag2


**Comando para eliminar un clúster**

    hdinsight cluster delete [options] <clusterName>

**Comando Mostrar detalles de clúster**

    hdinsight cluster show [options] <clusterName>

**Comando para ver todos los clústeres (en un grupo de recursos específico, si se proporciona)**

    hdinsight cluster list [options]

**Comando para cambiar el tamaño de un clúster**

    hdinsight cluster resize [options] <clusterName> <targetInstanceCount>

**Comando para habilitar el acceso HTTP para un clúster**

    hdinsight cluster enable-http-access [options] <clusterName> <userName> <password>

**Comando para deshabilitar el acceso para un clúster de HTTP**

    hdinsight cluster disable-http-access [options] <clusterName>

**Comando para habilitar el acceso RDP para un clúster**

    hdinsight cluster enable-rdp-access [options] <clusterName> <rdpUserName> <rdpPassword> <rdpExpiryDate>

**Comando para deshabilitar el acceso para un clúster de HTTP**

    hdinsight cluster disable-rdp-access [options] <clusterName>

## <a name="azure-insights-commands-related-to-monitoring-insights-events-alert-rules-autoscale-settings-metrics"></a>perspectivas Azure: comandos relacionados con la supervisión perspectivas (eventos, las reglas de alertas, configuración Autoescala, métricas)

**Recuperar los registros de la operación de una suscripción, un correlationId, un grupo de recursos, el recurso o el proveedor de recursos**

    insights logs list [options]

## <a name="azure-location-commands-to-get-the-available-locations-for-all-resource-types"></a>ubicación de Azure: comandos para obtener las ubicaciones disponibles para todos los tipos de recursos

**Lista de las ubicaciones disponibles**

    location list [options]

## <a name="azure-network-commands-to-manage-network-resources"></a>red de Azure: comandos para administrar los recursos de red

**Comandos para administrar las redes virtuales**

    network vnet create [options] <resource-group> <name> <location>
Crea una red virtual. En el ejemplo siguiente, creamos una red virtual denominada newvnet para myresourcegroup de grupo de recursos en la región de Estados Unidos oeste.


    azure network vnet create myresourcegroup newvnet "west us"
    info:    Executing command network vnet create
    + Looking up virtual network "newvnet"
    + Creating virtual network "newvnet"
     Loading virtual network state
    data:    Id:                   /subscriptions/###############################/resourceGroups/myresourcegroup/providers/Microsoft.Network/virtualNetworks/newvnet
    data:    Name:                 newvnet
    data:    Type:                 Microsoft.Network/virtualNetworks
    data:    Location:             westus
    data:    Tags:
    data:    Provisioning state:   Succeeded
    data:    Address prefixes:
    data:     10.0.0.0/8
    data:    DNS servers:
    data:    Subnets:
    data:
    info:    network vnet create command OK


Opciones de parámetro:

    -h, --help                                 output usage information
    -v, --verbose                              use verbose output
    --json                                     use json output
    -g, --resource-group <resource-group>      the name of the resource group
    -n, --name <name>                          the name of the virtual network
    -l, --location <location>                  the location
    -a, --address-prefixes <address-prefixes>  the comma separated list of address prefixes for this virtual network
      For example -a 10.0.0.0/24,10.0.1.0/24.
      Default value is 10.0.0.0/8

    -d, --dns-servers <dns-servers>            the comma separated list of DNS servers IP addresses
    -t, --tags <tags>                          the tags set on this virtual network.
      Can be multiple. In the format of "name=value".
      Name is required and value is optional.
      For example, -t tag1=value1;tag2
     -s, --subscription <subscription>          the subscription identifier
<BR>

    network vnet set [options] <resource-group> <name>

Actualiza una configuración de red virtual dentro de un grupo de recursos.

    azure network vnet set myresourcegroup newvnet

    info:    Executing command network vnet set
    + Looking up virtual network "newvnet"
    + Updating virtual network "newvnet"
    + Loading virtual network state
    data:    Id:                   /subscriptions/###############################/resourceGroups/myresourcegroup/providers/Microsoft.Network/virtualNetworks/newvnet
    data:    Name:                 newvnet
    data:    Type:                 Microsoft.Network/virtualNetworks
    data:    Location:             westus
    data:    Tags:
    data:    Provisioning state:   Succeeded
    data:    Address prefixes:
    data:     10.0.0.0/8
    data:    DNS servers:
    data:    Subnets:
    data:
    info:    network vnet set command OK

Opciones de parámetro:

       -h, --help                                 output usage information
       -v, --verbose                              use verbose output
       --json                                     use json output
       -g, --resource-group <resource-group>      the name of the resource group
       -n, --name <name>                          the name of the virtual network
       -a, --address-prefixes <address-prefixes>  the comma separated list of address prefixes for this virtual network.
        For example -a 10.0.0.0/24,10.0.1.0/24.
        This list will be appended to the current list of address prefixes.
        The address prefixes in this list should not overlap between them.
        The address prefixes in this list should not overlap with existing address prefixes in the vnet.

       -d, --dns-servers [dns-servers]            the comma separated list of DNS servers IP addresses.
        This list will be appended to the current list of DNS server IP addresses.

       -t, --tags <tags>                          the tags set on this virtual network.
        Can be multiple. In the format of "name=value".
        Name is required and value is optional. For example, -t tag1=value1;tag2.
        This list will be appended to the current list of tags

       --no-tags                                  remove all existing tags
       -s, --subscription <subscription>          the subscription identifier
<BR>

    network vnet list [options] <resource-group>

El comando muestra todas las redes virtuales en un grupo de recursos.


    C:\>azure network vnet list myresourcegroup

    info:    Executing command network vnet list
    + Listing virtual networks
        data:    ID
       Name      Location  Address prefixes  DNS servers
    data:    -------------------------------------------------------------------
    ------  --------  --------  ----------------  -----------
    data:    /subscriptions/###############################/resourceGroups/
    wvnet   newvnet   westus    10.0.0.0/8
    info:    network vnet list command OK

Opciones de parámetro:


      -h, --help                             output usage information
      -v, --verbose                          use verbose output
      --json                                 use json output
      -g, --resource-group <resource-group>  the name of the resource group
      -s, --subscription <subscription>      the subscription identifier

<BR>

    network vnet show [options] <resource-group> <name>
El comando muestra las propiedades de una red virtual en un grupo de recursos.

    azure network vnet show -g myresourcegroup -n newvnet

    info:    Executing command network vnet show
    + Looking up virtual network "newvnet"
    data:    Id:                   /subscriptions/###############################/resourceGroups/myresourcegroup/providers/Microsoft.Network/virtualNetworks/newvnet
    data:    Name:                 newvnet
    data:    Type:                 Microsoft.Network/virtualNetworks
    data:    Location:             westus
    data:    Tags:
    data:    Provisioning state:   Succeeded
    data:    Address prefixes:
    data:     10.0.0.0/8
    data:    DNS servers:
    data:    Subnets:
    data:
    info:    network vnet show command OK
<BR>

    network vnet delete [options] <resource-group> <name>
El comando quita una red virtual.

    azure network vnet delete myresourcegroup newvnetX

    info:    Executing command network vnet delete
    + Looking up virtual network "newvnetX"
    Delete virtual network newvnetX? [y/n] y
    + Deleting virtual network "newvnetX"
    info:    network vnet delete command OK

Opciones de parámetro:

     -h, --help                             output usage information
     -v, --verbose                          use verbose output
     --json                                 use json output
     -g, --resource-group <resource-group>  the name of the resource group
     -n, --name <name>                      the name of the virtual network
     -q, --quiet                            quiet mode, do not ask for delete confirmation
     -s, --subscription <subscription>      the subscription identifier


**Comandos para administrar virtual subredes de red**

    network vnet subnet create [options] <resource-group> <vnet-name> <name>

Agrega otra subred a una red virtual existente.

    azure network vnet subnet create -g myresourcegroup --vnet-name newvnet -n subnet --address-prefix 10.0.1.0/24

    info:    Executing command network vnet subnet create
    + Looking up the subnet "subnet"
    + Creating subnet "subnet"
    + Looking up the subnet "subnet"
    data:    Id:                        /subscriptions/###############################/resourceGroups/myresourcegroup/providers/Microsoft.Network/virtualNetworks/newvnet/subnets/subnet
    data:    Name:                      subnet
    data:    Type:                      Microsoft.Network/virtualNetworks/subnets
    data:    Provisioning state:        Succeeded
    data:    Address prefix:            10.0.1.0/24
    info:    network vnet subnet create command OK

Opciones de parámetro:

     -h, --help                                                       output usage information
     -v, --verbose                                                    use verbose output
         --json                                                           use json output
     -g, --resource-group <resource-group>                            the name of the resource group
     -e, --vnet-name <vnet-name>                                      the name of the virtual network
     -n, --name <name>                                                the name of the subnet
     -a, --address-prefix <address-prefix>                            the address prefix
     -w, --network-security-group-id <network-security-group-id>      the network security group identifier.
           e.g. /subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.Network/networkSecurityGroups/<nsg-name>
     -o, --network-security-group-name <network-security-group-name>  the network security group name
     -s, --subscription <subscription>                                the subscription identifier

<BR>

    network vnet subnet set [options] <resource-group> <vnet-name> <name>

Establece una subred de una red virtual específico dentro de un grupo de recursos.


    C:\>azure network vnet subnet set -g myresourcegroup --vnet-name newvnet -n subnet1

    info:    Executing command network vnet subnet set
    + Looking up the subnet "subnet1"
    + Setting subnet "subnet1"
    + Looking up the subnet "subnet1"
    data:    Id:                        /subscriptions/###############################/resourceGroups/myresourcegroup/providers/Microsoft.Network/virtualNetworks/newvnet/subnets/subnet1
    data:    Name:                      subnet1
    data:    Type:                      Microsoft.Network/virtualNetworks/subnets
    data:    Provisioning state:        Succeeded
    data:    Address prefix:            10.0.1.0/24
    info:    network vnet subnet set command OK
<BR>

    network vnet subnet list [options] <resource-group> <vnet-name>

Enumera todas las subredes de una red virtual para una red virtual específica dentro de un grupo de recursos.

    azure network vnet subnet set -g myresourcegroup --vnet-name newvnet -n subnet1

    info:    Executing command network vnet subnet set
    + Looking up the subnet "subnet1"
    + Setting subnet "subnet1"
    + Looking up the subnet "subnet1"
    data:    Id:                        /subscriptions/###############################/resourceGroups/myresourcegroup/providers/Microsoft.Network/virtualNetworks/newvnet/subnets/subnet1
    data:    Name:                      subnet1
    data:    Type:                      Microsoft.Network/virtualNetworks/subnets
    data:    Provisioning state:        Succeeded
    data:    Address prefix:            10.0.1.0/24
    info:    network vnet subnet set command OK
<BR>

    network vnet subnet show [options] <resource-group> <vnet-name> <name>
Muestra las propiedades de subred de una red virtual

    azure network vnet subnet show -g myresourcegroup --vnet-name newvnet -n subnet1

    info:    Executing command network vnet subnet show
    + Looking up the subnet "subnet1"
    data:    Id:                        /subscriptions/###############################/resourceGroups/myresourcegroup/providers/Microsoft
    .Network/virtualNetworks/newvnet/subnets/subnet1
    data:    Name:                      subnet1
    data:    Type:                      Microsoft.Network/virtualNetworks/subnets
    data:    Provisioning state:        Succeeded
    data:    Address prefix:            10.0.1.0/24
    info:    network vnet subnet show command OK

Opciones de parámetro:

    -h, --help                             output usage information
    -v, --verbose                          use verbose output
    --json                                 use json output
    -g, --resource-group <resource-group>  the name of the resource group
    -e, --vnet-name <vnet-name>            the name of the virtual network
    -n, --name <name>                      the name of the subnet
    -s, --subscription <subscription>      the subscription identifier
<BR>

    network vnet subnet delete [options] <resource-group> <vnet-name> <subnet-name>
Quita una subred de una red virtual existente.

    azure network vnet subnet delete -g myresourcegroup --vnet-name newvnet -n subnet1

    info:    Executing command network vnet subnet delete
    + Looking up the subnet "subnet1"
    Delete subnet "subnet1"? [y/n] y
    + Deleting subnet "subnet1"
    info:    network vnet subnet delete command OK

Opciones de parámetro:

    -h, --help                             output usage information
    -v, --verbose                          use verbose output
    --json                                 use json output
    -g, --resource-group <resource-group>  the name of the resource group
    -e, --vnet-name <vnet-name>            the name of the virtual network
    -n, --name <name>                      the subnet name
    -s, --subscription <subscription>      the subscription identifier
    -q, --quiet                            quiet mode, do not ask for delete confirmation

**Comandos para administrar equilibradores de carga**

    network lb create [options] <resource-group> <name> <location>
Crea un conjunto de equilibrador de carga.

    azure network lb create -g myresourcegroup -n mylb -l westus

    info:    Executing command network lb create
    + Looking up the load balancer "mylb"
    + Creating load balancer "mylb"
    + Looking up the load balancer "mylb"
    data:    Id:                           /subscriptions/###############################/resourceGroups/myresourcegroup/providers/Microsoft.Network/loadBalancers/mylb
    data:    Name:                         mylb
    data:    Type:                         Microsoft.Network/loadBalancers
    data:    Location:                     westus
    data:    Provisioning state:           Succeeded
    info:    network lb create command OK

Opciones de parámetro:

    -h, --help                             output usage information
    -v, --verbose                          use verbose output
    --json                                 use json output
    -g, --resource-group <resource-group>  the name of the resource group
    -n, --name <name>                      the name of the load balancer
    -l, --location <location>              the location
    -t, --tags <tags>                      the list of tags.
     Can be multiple. In the format of "name=value".
     Name is required and value is optional. For example, -t tag1=value1;tag2
    -s, --subscription <subscription>      the subscription identifier
<BR>

    network lb list [options] <resource-group>
Enumera los recursos del equilibrador de carga dentro de un grupo de recursos.

    azure network lb list myresourcegroup

    info:    Executing command network lb list
    + Getting the load balancers
    data:    Name  Location
    data:    ----  --------
    data:    mylb  westus
    info:    network lb list command OK

Opciones de parámetro:

    -h, --help                             output usage information
    -v, --verbose                          use verbose output
    --json                                 use json output
    -g, --resource-group <resource-group>  the name of the resource group
    -s, --subscription <subscription>      the subscription identifier
<BR>

    network lb show [options] <resource-group> <name>

Muestra carga información equilibrador de un equilibrador de carga específico dentro de un grupo de recursos

    azure network lb show myresourcegroup mylb -v

    info:    Executing command network lb show
    verbose: Looking up the load balancer "mylb"
    data:    Id:                           /subscriptions/###############################/resourceGroups/myresourcegroup/providers/Microsoft.Network/loadBalancers/mylb
    data:    Name:                         mylb
    data:    Type:                         Microsoft.Network/loadBalancers
    data:    Location:                     westus
    data:    Provisioning state:           Succeeded
    info:    network lb show command OK

Opciones de parámetro:

    -h, --help                             output usage information
    -v, --verbose                          use verbose output
    --json                                 use json output
    -g, --resource-group <resource-group>  the name of the resource group
    -n, --name <name>                      the name of the load balancer
    -s, --subscription <subscription>      the subscription identifier

<BR>

    network lb delete [options] <resource-group> <name>

Eliminar recursos del equilibrador de carga.

    azure network lb delete  myresourcegroup mylb

    info:    Executing command network lb delete
    + Looking up the load balancer "mylb"
    Delete load balancer "mylb"? [y/n] y
    + Deleting load balancer "mylb"
    info:    network lb delete command OK

Opciones de parámetro:

    -h, --help                             output usage information
    -v, --verbose                          use verbose output
    --json                                 use json output
    -g, --resource-group <resource-group>  the name of the resource group
    -n, --name <name>                      the name of the load balancer
    -q, --quiet                            quiet mode, do not ask for delete confirmation
    -s, --subscription <subscription>      the subscription identifier

**Comandos para administrar sondeos del equilibrador de carga**

    network lb probe create [options] <resource-group> <lb-name> <name>

Crear la configuración de sondeo de estado en el equilibrador de carga. Tenga en cuenta para ejecutar este comando, el equilibrador de carga requiere un recurso de ip de front-end (el comando proteger "azure front-end-dirección ip de red" para asignar una dirección ip del equilibrador de carga).

    azure network lb probe create -g myresourcegroup --lb-name mylb -n mylbprobe --protocol tcp --port 80 -i 300

    info:    Executing command network lb probe create
    + Looking up the load balancer "mylb"
    + Updating load balancer "mylb"
    info:    network lb probe create command OK

Opciones de parámetro:

    -h, --help                             output usage information
    -v, --verbose                          use verbose output
    --json                                 use json output
    -g, --resource-group <resource-group>  the name of the resource group
    -l, --lb-name <lb-name>                the name of the load balancer
    -n, --name <name>                      the name of the probe
    -p, --protocol <protocol>              the probe protocol
    -o, --port <port>                      the probe port
    -f, --path <path>                      the probe path
    -i, --interval <interval>              the probe interval in seconds
    -c, --count <count>                    the number of probes
    -s, --subscription <subscription>      the subscription identifier

<BR>

    network lb probe set [options] <resource-group> <lb-name> <name>

Actualiza un sondeo de equilibrador de carga existentes con los nuevos valores para el mismo.

    azure network lb probe set -g myresourcegroup -l mylb -n mylbprobe -p mylbprobe1 -p TCP -o 443 -i 300

    info:    Executing command network lb probe set
        + Looking up the load balancer "mylb"
    + Updating load balancer "mylb"
    info:    network lb probe set command OK

Opciones de parámetros

    -h, --help                             output usage information
    -v, --verbose                          use verbose output
    --json                                 use json output
    -g, --resource-group <resource-group>  the name of the resource group
    -l, --lb-name <lb-name>                the name of the load balancer
    -n, --name <name>                      the name of the probe
    -e, --new-probe-name <new-probe-name>  the new name of the probe
    -p, --protocol <protocol>              the new value for probe protocol
    -o, --port <port>                      the new value for probe port
    -f, --path <path>                      the new value for probe path
    -i, --interval <interval>              the new value for probe interval in seconds
    -c, --count <count>                    the new value for number of probes
    -s, --subscription <subscription>      the subscription identifier
<BR>

    network lb probe list [options] <resource-group> <lb-name>

Lista de las propiedades de sondeo de un conjunto de equilibrador de carga.

    C:\>azure network lb probe list -g myresourcegroup -l mylb

    info:    Executing command network lb probe list
    + Looking up the load balancer "mylb"
    data:    Name       Protocol  Port  Path  Interval  Count
    data:    ---------  --------  ----  ----  --------  -----
    data:    mylbprobe  Tcp       443         300       2
    info:    network lb probe list command OK

Opciones de parámetro:

    -h, --help                             output usage information
    -v, --verbose                          use verbose output
    --json                                 use json output
    -g, --resource-group <resource-group>  the name of the resource group
    -l, --lb-name <lb-name>                the name of the load balancer
    -s, --subscription <subscription>      the subscription identifier


    network lb probe delete [options] <resource-group> <lb-name> <name>
Quita el sondeo creado para el equilibrador de carga.

    azure network lb probe delete -g myresourcegroup -l mylb -n mylbprobe

    info:    Executing command network lb probe delete
    + Looking up the load balancer "mylb"
    Delete a probe "mylbprobe?" [y/n] y
    + Updating load balancer "mylb"
    info:    network lb probe delete command OK

**Comandos para administrar las configuraciones de ip de front-end de un equilibrador de carga**

    network lb frontend-ip create [options] <resource-group> <lb-name> <name>
Crea una configuración de IP de front-end a un conjunto existente de equilibrador de carga.

    azure network lb frontend-ip create -g myresourcegroup --lb-name mylb -n myfrontendip -o Dynamic -e subnet -m newvnet

    info:    Executing command network lb frontend-ip create
    + Looking up the load balancer "mylb"
    + Looking up the subnet "subnet"
    + Creating frontend IP configuration "myfrontendip"
    + Looking up the load balancer "mylb"
    data:    Id:                           /subscriptions/###############################/resourceGroups/Myresourcegroup/providers/Microsoft.Network/loadBalancers/mylb
    /frontendIPConfigurations/myfrontendip
    data:    Name:                         myfrontendip
    data:    Type:                         Microsoft.Network/loadBalancers/frontendIPConfigurations
    data:    Provisioning state:           Succeeded
    data:    Private IP allocation method: Dynamic
    data:    Private IP address:           10.0.1.4
    data:    Subnet:                       id=/subscriptions/###############################/resourceGroups/myresourcegroup/providers/Microsoft.Network/virtualNetworks/newvnet/subnets/subnet
    data:    Public IP address:
    data:    Inbound NAT rules
    data:    Outbound NAT rules
    data:    Load balancing rules
    data:
    info:    network lb frontend-ip create command OK

<BR>

    network lb frontend-ip set [options] <resource-group> <lb-name> <name>

Actualiza una configuración de una dirección IP de front-end. El siguiente comando agrega una dirección IP pública denominada mypubip5 a una IP de front-end de equilibrador de carga existente denominada myfrontendip.

    azure network lb frontend-ip set -g myresourcegroup --lb-name mylb -n myfrontendip -i mypubip5

    info:    Executing command network lb frontend-ip set
    + Looking up the load balancer "mylb"
    + Looking up the public ip "mypubip5"
    + Updating load balancer "mylb"
    + Looking up the load balancer "mylb"
    data:    Id:                           /subscriptions/###############################/resourceGroups/myresourcegroup/providers/Microsoft.Network/loadBalancers/mylb/frontendIPConfigurations/myfrontendip
    data:    Name:                         myfrontendip
    data:    Type:                         Microsoft.Network/loadBalancers/frontendIPConfigurations
    data:    Provisioning state:           Succeeded
    data:    Private IP allocation method: Dynamic
    data:    Private IP address:
    data:    Subnet:
    data:    Public IP address:            id=/subscriptions/###############################/resourceGroups/myresourcegroup/providers/Microsoft.Network/publicIPAddresses/mypubip5
    data:    Inbound NAT rules
    data:    Outbound NAT rules
    data:    Load balancing rules
    data:
    info:    network lb frontend-ip set command OK

Opciones de parámetro:

    -h, --help                                                         output usage information
    -v, --verbose                                                      use verbose output
    --json                                                             use json output
    -g, --resource-group <resource-group>                              the name of the resource group
    -l, --lb-name <lb-name>                                            the name of the load balancer
    -n, --name <name>                                                  the name of the frontend ip configuration
    -a, --private-ip-address <private-ip-address>                      the private ip address
    -o, --private-ip-allocation-method <private-ip-allocation-method>  the private ip allocation method [Static, Dynamic]
    -u, --public-ip-id <public-ip-id>                                  the public ip identifier.
    e.g. /subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.Network/publicIPAddresses/<public-ip-name>
    -i, --public-ip-name <public-ip-name>                              the public ip name.
    This public ip must exist in the same resource group as the lb.
    Please use public-ip-id if that is not the case.
    -b, --subnet-id <subnet-id>                                        the subnet id.
    e.g. /subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.Network/VirtualNetworks/<vnet-name>/subnets/<subnet-name>
    -e, --subnet-name <subnet-name>                                    the subnet name
    -m, --vnet-name <vnet-name>                                        the virtual network name.
    This virtual network must exist in the same resource group as the lb.
    Please use subnet-id if that is not the case.
    -s, --subscription <subscription>                                  the subscription identifier

<BR>

    network lb frontend-ip list [options] <resource-group> <lb-name>

Muestra todos los recursos IP de front-end configurados para el equilibrador de carga.

    azure network lb frontend-ip list -g myresourcegroup -l mylb

    info:    Executing command network lb frontend-ip list
    + Looking up the load balancer "mylb"
    data:    Name         Provisioning state  Private IP allocation method  Subnet
    data:    -----------  ------------------  ----------------------------  ------
    data:    myprivateip  Succeeded           Dynamic
    info:    network lb frontend-ip list command OK

Opciones de parámetro:

    -h, --help                             output usage information
    -v, --verbose                          use verbose output
    --json                                 use json output
    -g, --resource-group <resource-group>  the name of the resource group
    -l, --lb-name <lb-name>                the name of the load balancer
    -s, --subscription <subscription>      the subscription identifier
<BR>

    network lb frontend-ip delete [options] <resource-group> <lb-name> <name>
Elimina el objeto IP de front-end asociado para equilibrador de carga

    network lb frontend-ip delete -g myresourcegroup -l mylb -n myfrontendip
    info:    Executing command network lb frontend-ip delete
    + Looking up the load balancer "mylb"
    Delete frontend ip configuration "myfrontendip"? [y/n] y
    + Updating load balancer "mylb"

Opciones de parámetro:

    -h, --help                             output usage information
    -v, --verbose                          use verbose output
    --json                                 use json output
    -g, --resource-group <resource-group>  the name of the resource group
    -l, --lb-name <lb-name>                the name of the load balancer
    -n, --name <name>                      the name of the frontend ip configuration
    -q, --quiet                            quiet mode, do not ask for delete confirmation
    -s, --subscription <subscription>      the subscription identifier

**Comandos para administrar grupos de dirección de back-end de un equilibrador de carga**

    network lb address-pool create [options] <resource-group> <lb-name> <name>

Crear un grupo de dirección de back-end para un equilibrador de carga.

    azure network lb address-pool create -g myresourcegroup --lb-name mylb -n myaddresspool

    info:    Executing command network lb address-pool create
    + Looking up the load balancer "mylb"
    + Updating load balancer "mylb"
    + Looking up the load balancer "mylb"
    data:    Id:                        /subscriptions/###############################/resourceGroups/myresourgroup/providers/Microso.Network/loadBalancers/mylb/backendAddressPools/myaddresspool
    data:    Name:                      myaddresspool
    data:    Type:                      Microsoft.Network/loadBalancers/backendAddressPools
    data:    Provisioning state:        Succeeded
    data:    Backend IP configurations:
    data:    Load balancing rules:
    data:
    info:    network lb address-pool create command OK

Opciones de parámetro:

    -h, --help                             output usage information
    -v, --verbose                          use verbose output
    --json                                 use json output
    -g, --resource-group <resource-group>  the name of the resource group
    -l, --lb-name <lb-name>                the name of the load balancer
    -n, --name <name>                      the name of the backend address pool
    -s, --subscription <subscription>      the subscription identifier

<BR>

    network lb address-pool add [options] <resource-group> <lb-name> <name>

Un intervalo de grupo de direcciones de back-end es cómo un equilibrador de carga sabe qué recursos para enrutar el tráfico de red entrante desde su punto final mediante el Administrador de recursos de Azure. Después de crear y asignar un nombre el intervalo de grupo de direcciones de back-end (consulte comando "azure libras grupo direcciones de red-" de crear), debe agregar los extremos, que ahora se definen por un recurso denominado "interfaces de red".

Para configurar el intervalo de direcciones de back-end, necesita al menos una "interfaz de red" (consulte línea de comandos de "network azure libras nic" para obtener más detalles).

En el siguiente ejemplo, una interfaz de red "nic1" creado previamente utilizó para crear el intervalo de grupo de direcciones de back-end.

    azure network lb address-pool add -g myresourcegroup -l mylb -n mybackendpool -a nic1

    info:    Executing command network lb address-pool add
    + Looking up the load balancer "mylb"
    + Getting network interfaces
    + Updating network interface "nic1"
    + Looking up the load balancer "mylb"
    data:    Id:                        /subscriptions/###############################/resourceGroups/myresourcegroup/providers/Microsoft.Network/loadBalancers/mylb/backendAddressPools/mybackendpool
    data:    Name:                      mybackendpool
    data:    Type:                      Microsoft.Network/loadBalancers/backendAddressPools
    data:    Provisioning state:        Succeeded
    data:    Backend IP configurations:
    data:     id=/subscriptions/###############################/resourceGroups/myresourcegroup/providers/Microsoft.Network/networkInterfaces/nic1/ipConfigurations/NIC-config
    data:    Load balancing rules:
    data:
    info:    network lb address-pool add command OK

Opciones de parámetro:

    -h, --help                             output usage information
    -v, --verbose                          use verbose output
    --json                                 use json output
    -g, --resource-group <resource-group>  the name of the resource group
    -l, --lb-name <lb-name>                the name of the load balancer
    -n, --name <name>                      the name of the backend address pool
    -i, --vm-id <vm-id>                    the virtual machine identifier.
    e.g. "/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.Compute/virtualMachines/<vm-name>,/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.Compute/virtualMachines/<vm-name>"
    -m, --vm-name <vm-name>                the name of the virtual machine
    -d, --nic-id <nic-id>                  the network interface identifier.
    e.g. ""/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.Network/networkInterfaces/<nic-name>"
    -a, --nic-name <nic-name>              the name of the network interface
    -s, --subscription <subscription>      the subscription identifier

<BR>

    network lb address-pool remove [options] <resource-group> <lb-name> <name>

Quita una interfaz de red del intervalo de grupo de direcciones IP de back-end.

    azure network lb address-pool remove -g myresourcegroup -l mylb -n mybackendpool -a nic1

    info:    Executing command network lb address-pool remove
    + Looking up the load balancer "mylb"
    + Getting network interfaces
    + Updating network interface "nic1"
    + Looking up the load balancer "mylb"
    data:    Id:                        /subscriptions/###############################/resourceGroups/myresourcegroup/providers/Microsoft.Network/loadBalancers/mylb/backendAddressPools/mybackendpool
    data:    Name:                      mybackendpool
    data:    Type:                      Microsoft.Network/loadBalancers/backendAddressPools
    data:    Provisioning state:        Succeeded
    data:    Backend IP configurations:
    data:    Load balancing rules:
    data:
    info:    network lb address-pool remove command OK

Opciones de parámetro:

    -h, --help                             output usage information
    -v, --verbose                          use verbose output
    --json                                 use json output
    -g, --resource-group <resource-group>  the name of the resource group
    -l, --lb-name <lb-name>                the name of the load balancer
    -n, --name <name>                      the name of the backend address pool
    -i, --vm-id <vm-id>                    the virtual machine identifier.
    e.g. "/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.Compute/virtualMachines/<vm-name>,/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.Compute/virtualMachines/<vm-name>"
    -m, --vm-name <vm-name>                the name of the virtual machine
    -d, --nic-id <nic-id>                  the network interface identifier.
    e.g. ""/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.Network/networkInterfaces/<nic-name>"
    -a, --nic-name <nic-name>              the name of the network interface
    -s, --subscription <subscription>      the subscription identifier
<BR>

    network lb address-pool list [options] <resource-group> <lb-name>

Intervalo de grupo de direcciones IP de back-end de un grupo de recursos específicos de la lista

    azure network lb address-pool list -g myresourcegroup -l mylb

    info:    Executing command network lb address-pool list
    + Looking up the load balancer "mylb"
    data:    Name           Provisioning state
    data:    -------------  ------------------
    data:    mybackendpool  Succeeded
    info:    network lb address-pool list command OK

Opciones de parámetro:

    -h, --help                             output usage information
    -v, --verbose                          use verbose output
    --json                                 use json output
    -g, --resource-group <resource-group>  the name of the resource group
    -l, --lb-name <lb-name>                the name of the load balancer
    -s, --subscription <subscription>      the subscription identifier

<BR>
grupo de direcciones de red libras eliminar [opciones] < grupo de recursos >< > nombre de libras<name>

Quita el recurso de intervalo de grupo IP de back-end de equilibrador de carga.

    azure network lb address-pool delete -g myresourcegroup -l mylb -n mybackendpool

    info:    Executing command network lb address-pool delete
    + Looking up the load balancer "mylb"
    Delete backend address pool "mybackendpool"? [y/n] y
    + Updating load balancer "mylb"
    info:    network lb address-pool delete command OK

Opciones de parámetro:

    -h, --help                             output usage information
    -v, --verbose                          use verbose output
    --json                                 use json output
    -g, --resource-group <resource-group>  the name of the resource group
    -l, --lb-name <lb-name>                the name of the load balancer
    -n, --name <name>                      the name of the backend address pool
    -q, --quiet                            quiet mode, do not ask for delete confirmation
    -s, --subscription <subscription>      the subscription identifier

**Comandos para administrar reglas de equilibrador de carga**

    network lb rule create [options] <resource-group> <lb-name> <name>
Crear reglas del equilibrador de carga.

Puede crear una regla de equilibrador de carga de la configuración del extremo de front-end para el equilibrador de carga y el intervalo de grupo de direcciones de back-end para recibir el tráfico de red. Configuración también incluye los puertos extremo de IP de front-end y puertos para el intervalo de grupo de direcciones de back-end.

En el ejemplo siguiente se muestra cómo crear una regla de equilibrador de carga, el extremo de front-end escucha para el puerto TCP 80 y cargar Equilibrio tráfico de red que se envía al puerto 8080 para el intervalo de grupo de direcciones de back-end.

    azure network lb rule create -g myresourcegroup -l mylb -n mylbrule -p tcp -f 80 -b 8080 -i 10


    info:    Executing command network lb rule create
    + Looking up the load balancer "mylb"
    + Updating load balancer "mylb"
    + Loading rule state
    data:    Id:                        /subscriptions/###############################/resourceGroups/myresourcegroup/providers/Microsoft.Network/loadBalancers/mylb/loadBalancingRules/mylbrule
    data:    Name:                      mylbrule
    data:    Type:                      Microsoft.Network/loadBalancers/loadBalancingRules
    data:    Provisioning state:        Succeeded
    data:    Frontend IP configuration: /subscriptions/###############################/resourceGroups/myresourcegroup/providers/Microsoft.Network/loadBalancers/mylb/frontendIPConfigurations/myfrontendip
    data:    Backend address pool:      id=/subscriptions/###############################/resourceGroups/myresourcegroup/providers/Microsoft.Network/loadBalancers/mylb/backendAddressPools/mybackendpool
    data:    Protocol:                  Tcp
    data:    Frontend port:             80
    data:    Backend port:              8080
    data:    Enable floating IP:        false
    data:    Idle timeout in minutes:   10
    data:    Probes
    data:
    info:    network lb rule create command OK

<BR>

    network lb rule set [options] <resource-group> <lb-name> <name>

Actualiza una regla de equilibrador de carga existente en un grupo de recurso específico. En el siguiente ejemplo, hemos cambiado el nombre de la regla de mylbrule mynewlbrule.

    azure network lb rule set -g myresourcegroup -l mylb -n mylbrule -r mynewlbrule -p tcp -f 80 -b 8080 -i 10 -t myfrontendip -o mybackendpool

    info:    Executing command network lb rule set
    + Looking up the load balancer "mylb"
    + Updating load balancer "mylb"
    + Loading rule state
    data:    Id:                        /subscriptions/###############################/resourceGroups/yresourcegroup/providers/Microsoft.Network/loadBalancers/mylb/loadBalancingRules/mynewlbrule
    data:    Name:                      mynewlbrule
    data:    Type:                      Microsoft.Network/loadBalancers/loadBalancingRules
    data:    Provisioning state:        Succeeded
    data:    Frontend IP configuration: /subscriptions/###############################/resourceGroups/yresourcegroup/providers/Microsoft.Network/loadBalancers/mylb/frontendIPConfigurations/myfrontendip
    data:    Backend address pool:      id=/subscriptions/###############################/resourceGroups/yresourcegroup/providers/Microsoft.Network/loadBalancers/mylb/backendAddressPools/mybackendpool
    data:    Protocol:                  Tcp
    data:    Frontend port:             80
    data:    Backend port:              8080
    data:    Enable floating IP:        false
    data:    Idle timeout in minutes:   10
    data:    Probes
    data:
    info:    network lb rule set command OK

Opciones de parámetro:

    -h, --help                                         output usage information
    -v, --verbose                                      use verbose output
    --json                                             use json output
    -g, --resource-group <resource-group>              the name of the resource group
    -l, --lb-name <lb-name>                            the name of the load balancer
    -n, --name <name>                                  the name of the rule
    -r, --new-rule-name <new-rule-name>                new rule name
    -p, --protocol <protocol>                          the rule protocol
    -f, --frontend-port <frontend-port>                the frontend port
    -b, --backend-port <backend-port>                  the backend port
    -e, --enable-floating-ip <enable-floating-ip>      enable floating point ip
    -i, --idle-timeout <idle-timeout>                  the idle timeout in minutes
    -a, --probe-name [probe-name]                      the name of the probe defined in the same load balancer
    -t, --frontend-ip-name <frontend-ip-name>          the name of the frontend ip configuration in the same load balancer
    -o, --backend-address-pool <backend-address-pool>  name of the backend address pool defined in the same load balancer
    -s, --subscription <subscription>                  the subscription identifier


    network lb rule list [options] <resource-group> <lb-name>

Enumera todas las carga reglas equilibrador configuradas para un equilibrador de carga de un grupo de recursos específico.

    azure network lb rule list -g myresourcegroup -l mylb

    info:    Executing command network lb rule list
    + Looking up the load balancer "mylb"
    data:    Name         Provisioning state  Protocol  Frontend port  Backend port  Enable floating IP  Idle timeout in minutes  Backend address pool  Probe data

    data:    mynewlbrule  Succeeded           Tcp       80             8080          false               10                       /subscriptions/###############################/resourceGroups/myresourcegroup/providers/Microsoft.Network/loadBalancers/mylb/backendAddressPools/mybackendpool
    info:    network lb rule list command OK

Opciones de parámetro:

    -h, --help                             output usage information
    -v, --verbose                          use verbose output
    --json                                 use json output
    -g, --resource-group <resource-group>  the name of the resource group
    -l, --lb-name <lb-name>                the name of the load balancer
    -s, --subscription <subscription>      the subscription identifier

    network lb rule delete [options] <resource-group> <lb-name> <name>

Elimina una regla de equilibrador de carga.

    azure network lb rule delete -g myresourcegroup -l mylb -n mynewlbrule

    info:    Executing command network lb rule delete
    + Looking up the load balancer "mylb"
    Delete load balancing rule mynewlbrule? [y/n] y
    + Updating load balancer "mylb"
    info:    network lb rule delete command OK

Opciones de parámetro:

    -h, --help                             output usage information
    -v, --verbose                          use verbose output
    --json                                 use json output
    -g, --resource-group <resource-group>  the name of the resource group
    -l, --lb-name <lb-name>                the name of the load balancer
    -n, --name <name>                      the name of the rule
    -q, --quiet                            quiet mode, do not ask for delete confirmation
    -s, --subscription <subscription>      the subscription identifier

**Reglas NAT de entrada de comandos para administrar equilibrador de carga**

    network lb inbound-nat-rule create [options] <resource-group> <lb-name> <name>
Crea una regla NAT entrante para equilibrador de carga.

En el ejemplo siguiente, creamos una regla NAT de IP de front-end (que se ha definido previamente con el comando "ip front-end de red azure") con un puerto de escucha entrante y saliente puerto que el equilibrador de carga se utiliza para enviar el tráfico de red.


    azure network lb inbound-nat-rule create -g myresourcegroup -l mylb -n myinboundnat -p tcp -f 80 -b 8080 -i myfrontendip

    info:    Executing command network lb inbound-nat-rule create
    + Looking up the load balancer "mylb"
    + Updating load balancer "mylb"
    + Looking up the load balancer "mylb"
    data:    Id:                        /subscriptions/###############################/resourceGroups/myresourcegroup/providers/Microsoft.Network/loadBalancers/mylb/inboundNatRules/myinboundnat
    data:    Name:                      myinboundnat
    data:    Type:                      Microsoft.Network/loadBalancers/inboundNatRules
    data:    Provisioning state:        Succeeded
    data:    Frontend IP Configuration: id=/subscriptions/###############################/resourceGroups/myresourcegroup/providers/Microsoft.Network/loadBalancers/mylb/frontendIPConfigurations/myfrontendip
    data:    Backend IP configuration
    data:    Protocol                   Tcp
    data:    Frontend port              80
    data:    Backend port               8080
    data:    Enable floating IP         false
    info:    network lb inbound-nat-rule create command OK

Opciones de parámetro:

    -h, --help                                     output usage information
    -v, --verbose                                  use verbose output
    --json                                         use json output
    -g, --resource-group <resource-group>          the name of the resource group
    -l, --lb-name <lb-name>                        the name of the load balancer
    -n, --name <name>                              the name of the inbound NAT rule
    -p, --protocol <protocol>                      the rule protocol [tcp,udp]
    -f, --frontend-port <frontend-port>            the frontend port [0-65535]
    -b, --backend-port <backend-port>              the backend port [0-65535]
    -e, --enable-floating-ip <enable-floating-ip>  enable floating point ip [true,false]
    -i, --frontend-ip <frontend-ip>                the name of the frontend ip configuration
    -m, --vm-id <vm-id>                            the VM id.
    e.g. /subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.Compute/virtualMachines/<vm-name>
    -a, --vm-name <vm-name>                        the VM name.This VM must exist in the same resource group as the lb.
    Please use vm-id if that is not the case.
    this parameter will be ignored if --vm-id is specified
    -s, --subscription <subscription>              the subscription identifier
<BR>

    network lb inbound-nat-rule set [options] <resource-group> <lb-name> <name>
Actualiza una regla nat entrante existente. En el siguiente ejemplo, hemos cambiado el puerto de escucha entrante de 80 81.

    azure network lb inbound-nat-rule set -g group-1 -l mylb -n myinboundnat -p tcp -f 81 -b 8080 -i myfrontendip

    info:    Executing command network lb inbound-nat-rule set
    + Looking up the load balancer "mylb"
    + Updating load balancer "mylb"
    + Looking up the load balancer "mylb"
    data:    Id:                        /subscriptions/###############################/resourceGroups/group-1/providers/Microsoft.Network/loadBalancers/mylb/inboundNatRules/myinboundnat
    data:    Name:                      myinboundnat
    data:    Type:                      Microsoft.Network/loadBalancers/inboundNatRules
    data:    Provisioning state:        Succeeded
    data:    Frontend IP Configuration: id=/subscriptions/###############################/resourceGroups/group-1/providers/Microsoft.Network/loadBalancers/mylb/frontendIPConfigurations/myfrontendip
    data:    Backend IP configuration
    data:    Protocol                   Tcp
    data:    Frontend port              81
    data:    Backend port               8080
    data:    Enable floating IP         false
    info:    network lb inbound-nat-rule set command OK

Opciones de parámetro:

    -h, --help                                     output usage information
    -v, --verbose                                  use verbose output
    --json                                         use json output
    -g, --resource-group <resource-group>          the name of the resource group
    -l, --lb-name <lb-name>                        the name of the load balancer
    -n, --name <name>                              the name of the inbound NAT rule
    -p, --protocol <protocol>                      the rule protocol [tcp,udp]
    -f, --frontend-port <frontend-port>            the frontend port [0-65535]
    -b, --backend-port <backend-port>              the backend port [0-65535]
    -e, --enable-floating-ip <enable-floating-ip>  enable floating point ip [true,false]
    -i, --frontend-ip <frontend-ip>                the name of the frontend ip configuration
    -m, --vm-id [vm-id]                            the VM id.
    e.g. /subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.Compute/virtualMachines/<vm-name>
    -a, --vm-name <vm-name>                        the VM name.
    This virtual machine must exist in the same resource group as the lb.
    Please use vm-id if that is not the case
    -s, --subscription <subscription>              the subscription identifier
<BR>

    network lb inbound-nat-rule list [options] <resource-group> <lb-name>

Enumera todas las reglas nat entrantes de equilibrador de carga.

    azure network lb inbound-nat-rule list -g myresourcegroup -l mylb

    info:    Executing command network lb inbound-nat-rule list
    + Looking up the load balancer "mylb"
    data:    Name          Provisioning state  Protocol  Frontend port  Backend port  Enable floating IP  Idle timeout in minutes  Backend IP configuration
    data:    ------------  ------------------  --------  -------------  ------------  ------------------  -----------------------  ---
    ---------------------
    data:    myinboundnat  Succeeded           Tcp       81             8080          false               4

    info:    network lb inbound-nat-rule list command OK

Opciones de parámetro:

    -h, --help                             output usage information
    -v, --verbose                          use verbose output
    --json                                 use json output
    -g, --resource-group <resource-group>  the name of the resource group
    -l, --lb-name <lb-name>                the name of the load balancer
    -s, --subscription <subscription>      the subscription identifier
<BR>

    network lb inbound-nat-rule delete [options] <resource-group> <lb-name> <name>

Elimina las reglas NAT para el equilibrador de carga de un grupo de recursos específico.

    azure network lb inbound-nat-rule delete -g myresourcegroup -l mylb -n myinboundnat

    info:    Executing command network lb inbound-nat-rule delete
    + Looking up the load balancer "mylb"
    Delete inbound NAT rule "myinboundnat?" [y/n] y
    + Updating load balancer "mylb"
    info:    network lb inbound-nat-rule delete command OK

Opciones de parámetro:

    -h, --help                             output usage information
    -v, --verbose                          use verbose output
    --json                                 use json output
    -g, --resource-group <resource-group>  the name of the resource group
    -l, --lb-name <lb-name>                the name of the load balancer
    -n, --name <name>                      the name of the inbound NAT rule
    -q, --quiet                            quiet mode, do not ask for delete confirmation
    -s, --subscription <subscription>      the subscription identifier

**Comandos para administrar direcciones ip públicas**

    network public-ip create [options] <resource-group> <name> <location>
Crea un recurso de ip pública. Creará el recurso de ip pública y asociar a un nombre de dominio.

    azure network public-ip create -g myresourcegroup -n mytestpublicip1 -l eastus -d azureclitest -a "Dynamic"
    info:    Executing command network public-ip create
    + Looking up the public ip "mytestpublicip1"
    + Creating public ip address "mytestpublicip1"
    + Looking up the public ip "mytestpublicip1"
    data:    Id:                   /subscriptions/###############################/resourceGroups/myresourcegroup/providers/Microsoft.Network/publicIPAddresses/mytestpublicip1
    data:    Name:                 mytestpublicip1
    data:    Type:                 Microsoft.Network/publicIPAddresses
    data:    Location:             eastus
    data:    Provisioning state:   Succeeded
    data:    Allocation method:    Dynamic
    data:    Idle timeout:         4
    data:    Domain name label:    azureclitest
    data:    FQDN:                 azureclitest.eastus.cloudapp.azure.com
    info:    network public-ip create command OK


Opciones de parámetro:

    -h, --help                                   output usage information
    -v, --verbose                                use verbose output
    --json                                       use json output
    -g, --resource-group <resource-group>        the name of the resource group
    -n, --name <name>                            the name of the public ip
    -l, --location <location>                    the location
    -d, --domain-name-label <domain-name-label>  the domain name label.
    This set DNS to <domain-name-label>.<location>.cloudapp.azure.com
    -a, --allocation-method <allocation-method>  the allocation method [Static][Dynamic]
    -i, --idletimeout <idletimeout>              the idle timeout in minutes
    -f, --reverse-fqdn <reverse-fqdn>            the reverse fqdn
    -t, --tags <tags>                            the list of tags.
    Can be multiple. In the format of "name=value".
    Name is required and value is optional.
    For example, -t tag1=value1;tag2
    -s, --subscription <subscription>            the subscription identifier
<br>

    network public-ip set [options] <resource-group> <name>
Actualiza las propiedades de un recurso de ip pública existente. En el siguiente ejemplo hemos cambiado la dirección IP pública de dinámico a estático.

    azure network public-ip set -g group-1 -n mytestpublicip1 -d azureclitest -a "Static"
    info:    Executing command network public-ip set
    + Looking up the public ip "mytestpublicip1"
    + Updating public ip address "mytestpublicip1"
    + Looking up the public ip "mytestpublicip1"
    data:    Id:                   /subscriptions/###############################/resourceGroups/myresourcegroup/providers/Microsoft.Network/publicIPAddresses/mytestpublicip1
    data:    Name:                 mytestpublicip1
    data:    Type:                 Microsoft.Network/publicIPAddresses
    data:    Location:             eastus
    data:    Provisioning state:   Succeeded
    data:    Allocation method:    Static
    data:    Idle timeout:         4
    data:    IP Address:           (static IP address)
    data:    Domain name label:    azureclitest
    data:    FQDN:                 azureclitest.eastus.cloudapp.azure.com
    info:    network public-ip set command OK

Opciones de parámetro:

    -h, --help                                   output usage information
    -v, --verbose                                use verbose output
    --json                                       use json output
    -g, --resource-group <resource-group>        the name of the resource group
    -n, --name <name>                            the name of the public ip
    -d, --domain-name-label [domain-name-label]  the domain name label.
    This set DNS to <domain-name-label>.<location>.cloudapp.azure.com
    -a, --allocation-method <allocation-method>  the allocation method [Static][Dynamic]
    -i, --idletimeout <idletimeout>              the idle timeout in minutes
    -f, --reverse-fqdn [reverse-fqdn]            the reverse fqdn
    -t, --tags <tags>                            the list of tags.
    Can be multiple. In the format of "name=value".
    Name is required and value is optional.
    For example, -t tag1=value1;tag2
    --no-tags                                    remove all existing tags
    -s, --subscription <subscription>            the subscription identifier

<br>
lista de ip pública de red [opciones] < grupo de recursos > enumera todos los recursos IP públicos dentro de un grupo de recursos.

    azure network public-ip list -g myresourcegroup

    info:    Executing command network public-ip list
    + Getting the public ip addresses
    data:    Name             Location  Allocation  IP Address    Idle timeout  DNS Name
    data:    ---------------  --------  ----------  ------------  ------------  -------------------------------------------
    data:    mypubip5         westus    Dynamic                   4             "domain name".westus.cloudapp.azure.com
    data:    myPublicIP       eastus    Dynamic                   4             "domain name".eastus.cloudapp.azure.com
    data:    mytestpublicip   eastus    Dynamic                   4             "domain name".eastus.cloudapp.azure.com
    data:    mytestpublicip1  eastus   Static (Static IP address) 4             azureclitest.eastus.cloudapp.azure.com

Opciones de parámetro:

    -h, --help                             output usage information
    -v, --verbose                          use verbose output
    --json                                 use json output
    -g, --resource-group <resource-group>  the name of the resource group
    -s, --subscription <subscription>      the subscription identifier
<BR>
dirección ip pública de red mostrar [opciones] < grupo de recursos ><name>

Muestra las propiedades de ip pública para un recurso de ip pública dentro de un grupo de recursos.

    azure network public-ip show -g myresourcegroup -n mytestpublicip

    info:    Executing command network public-ip show
    + Looking up the public ip "mytestpublicip1"
    data:    Id:                   /subscriptions/###############################/resourceGroups/myresourcegroup/providers/Microsoft.Network/publicIPAddresses/mytestpublicip
    data:    Name:                 mytestpublicip
    data:    Type:                 Microsoft.Network/publicIPAddresses
    data:    Location:             eastus
    data:    Provisioning state:   Succeeded
    data:    Allocation method:    Static
    data:    Idle timeout:         4
    data:    IP Address:           (static IP address)
    data:    Domain name label:    azureclitest
    data:    FQDN:                 azureclitest.eastus.cloudapp.azure.com
    info:    network public-ip show command OK

Opciones de parámetro:

    -h, --help                             output usage information
    -v, --verbose                          use verbose output
    --json                                 use json output
    -g, --resource-group <resource-group>  the name of the resource group
    -n, --name <name>                      the name of the public IP
    -s, --subscription <subscription>      the subscription identifier


    network public-ip delete [options] <resource-group> <name>

Elimina los recursos de ip pública.

    azure network public-ip delete -g group-1 -n mypublicipname
    info:    Executing command network public-ip delete
    + Looking up the public ip "mypublicipname"
    Delete public ip address "mypublicipname"? [y/n] y
    + Deleting public ip address "mypublicipname"
    info:    network public-ip delete command OK

Opciones de parámetro:

    -h, --help                             output usage information
    -v, --verbose                          use verbose output
    --json                                 use json output
    -g, --resource-group <resource-group>  the name of the resource group
    -n, --name <name>                      the name of the public IP
    -q, --quiet                            quiet mode, do not ask for delete confirmation
    -s, --subscription <subscription>      the subscription identifier


**Comandos para administrar interfaces de red**

    network nic create [options] <resource-group> <name> <location>
Crea un recurso de interfaz de red (NIC) que puede utilizarse para equilibradores de carga o asocia a una máquina Virtual.

    azure network nic create -g myresourcegroup -l eastus -n testnic1 --subnet-name subnet-1 --subnet-vnet-name myvnet

    info:    Executing command network nic create
    + Looking up the network interface "testnic1"
    + Looking up the subnet "subnet-1"
    + Creating network interface "testnic1"
    + Looking up the network interface "testnic1"
    data:    Id:                     /subscriptions/c4a17ddf-aa84-491c-b6f9-b90d882299f7/resourceGroups/group-1/providers/Microsoft.Network/networkInterfaces/testnic1
    data:    Name:                   testnic1
    data:    Type:                   Microsoft.Network/networkInterfaces
    data:    Location:               eastus
    data:    Provisioning state:     Succeeded
    data:    IP configurations:
    data:       Name:                         NIC-config
    data:       Provisioning state:           Succeeded
    data:       Private IP address:           10.0.0.5
    data:       Private IP Allocation Method: Dynamic
    data:       Subnet:                       /subscriptions/c4a17ddf-aa84-491c-b6f9-b90d882299f7/resourceGroups/group-1/providers/Microsoft.Network/virtualNetworks/myVNET/subnets/Subnet-1

Opciones de parámetro:

    -h, --help                                                       output usage information
    -v, --verbose                                                    use verbose output
    --json                                                           use json output
    -g, --resource-group <resource-group>                            the name of the resource group
    -n, --name <name>                                                the name of the network interface
    -l, --location <location>                                        the location
    -w, --network-security-group-id <network-security-group-id>      the network security group identifier.
    e.g. /subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.Network/networkSecurityGroups/<nsg-name>
    -o, --network-security-group-name <network-security-group-name>  the network security group name.
    This network security group must exist in the same resource group as the nic.
    Please use network-security-group-id if that is not the case.
    -i, --public-ip-id <public-ip-id>                                the public IP identifier.
    e.g. /subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.Network/publicIPAddresses/<public-ip-name>
    -p, --public-ip-name <public-ip-name>                            the public IP name.
    This public ip must exist in the same resource group as the nic.
    Please use public-ip-id if that is not the case.
    -a, --private-ip-address <private-ip-address>                    the private IP address
    -u, --subnet-id <subnet-id>                                      the subnet identifier.
    e.g. /subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.Network/virtualNetworks/<vnet-name>/subnets/<subnet-name>
    --subnet-name <subnet-name>                                  the subnet name
    -m, --subnet-vnet-name <subnet-vnet-name>                        the vnet name under which subnet-name exists
    -t, --tags <tags>                                                the comma seperated list of tags.
    Can be multiple. In the format of "name=value".
    Name is required and value is optional.
    For example, -t tag1=value1;tag2
    -s, --subscription <subscription>                                the subscription identifier
    data:
    info:    network nic create command OK

<BR>

    network nic set [options] <resource-group> <name>

    network nic list [options] <resource-group>
    network nic show [options] <resource-group> <name>
    network nic delete [options] <resource-group> <name>

**Comandos para administrar grupos de seguridad de red**

    network nsg create [options] <resource-group> <name> <location>
    network nsg set [options] <resource-group> <name>
    network nsg list [options] <resource-group>
    network nsg show [options] <resource-group> <name>
    network nsg delete [options] <resource-group> <name>

**Comandos para administrar las reglas del grupo de seguridad de red**

    network nsg rule create [options] <resource-group> <nsg-name> <name>
    network nsg rule set [options] <resource-group> <nsg-name> <name>
    network nsg rule list [options] <resource-group> <nsg-name>
    network nsg rule show [options] <resource-group> <nsg-name> <name>
    network nsg rule delete [options] <resource-group> <nsg-name> <name>

**Comandos para administrar el perfil de administrador de tráfico**

    network traffic-manager profile create [options] <resource-group> <name>
    network traffic-manager profile set [options] <resource-group> <name>
    network traffic-manager profile list [options] <resource-group>
    network traffic-manager profile show [options] <resource-group> <name>
    network traffic-manager profile delete [options] <resource-group> <name>
    network traffic-manager profile is-dns-available [options] <resource-group> <relative-dns-name>

**Comandos para administrar los extremos del Administrador de tráfico**

    network traffic-manager profile endpoint create [options] <resource-group> <profile-name> <name> <endpoint-location>
    network traffic-manager profile endpoint set [options] <resource-group> <profile-name> <name>
    network traffic-manager profile endpoint delete [options] <resource-group> <profile-name> <name>

**Comandos para administrar virtual puertas de enlace de red**

    network gateway list [options] <resource-group>

## <a name="azure-provider-commands-to-manage-resource-provider-registrations"></a>proveedor de Azure: comandos para administrar los registros del proveedor de recursos

**Lista de proveedores registrados actualmente en el Administrador de recursos**

    provider list [options]

**Mostrar detalles sobre el espacio de nombres de proveedor solicitado**

    provider show [options] <namespace>

**Registro de proveedor con la suscripción**

    provider register [options] <namespace>

**Anular el registro de proveedor con la suscripción**

    provider unregister [options] <namespace>

## <a name="azure-resource-commands-to-manage-your-resources"></a>recursos de Azure: comandos para administrar los recursos

**Crea un recurso en un grupo de recursos**

    resource create [options] <resource-group> <name> <resource-type> <location> <api-version>

**Actualiza un recurso en un grupo de recursos sin plantillas ni parámetros**

    resource set [options] <resource-group> <name> <resource-type> <properties> <api-version>

**Enumera los recursos**

    resource list [options] [resource-group]

**Obtiene un recurso dentro de un grupo de recursos o suscripción**

    resource show [options] <resource-group> <name> <resource-type> <api-version>

**Elimina un recurso en un grupo de recursos**

    resource delete [options] <resource-group> <name> <resource-type> <api-version>

## <a name="azure-role-commands-to-manage-your-azure-roles"></a>función Azure: comandos para administrar las funciones de Azure

**Obtener todas las definiciones de función disponibles**

    role list [options]

**Obtener una definición de rol disponibles**

    role show [options] [name]

**Comandos para administrar su asignación de roles**

    role assignment create [options] [objectId] [upn] [mail] [spn] [role] [scope] [resource-group] [resource-type] [resource-name]
    role assignment list [options] [objectId] [upn] [mail] [spn] [role] [scope] [resource-group] [resource-type] [resource-name]
    role assignment delete [options] [objectId] [upn] [mail] [spn] [role] [scope] [resource-group] [resource-type] [resource-name]

## <a name="azure-storage-commands-to-manage-your-storage-objects"></a>almacenamiento de Azure: comandos para administrar los objetos de almacenamiento

**Comandos para administrar las cuentas de almacenamiento**

    storage account list [options]
    storage account show [options] <name>
    storage account create [options] <name>
    storage account set [options] <name>
    storage account delete [options] <name>

**Comandos para administrar las claves de la cuenta de almacenamiento**

    storage account keys list [options] <name>
    storage account keys renew [options] <name>

**Comandos para mostrar la cadena de conexión de almacenamiento**

    storage account connectionstring show [options] <name>

**Comandos para administrar los contenedores de almacenamiento**

    storage container list [options] [prefix]
    storage container show [options] [container]
    storage container create [options] [container]
    storage container delete [options] [container]
    storage container set [options] [container]

**Comandos para administrar compartido tener acceso a las firmas de su contenedor de almacenamiento**

    storage container sas create [options] [container] [permissions] [expiry]

**Las directivas de su contenedor de almacenamiento de acceso de comandos para administrar almacenado**

    storage container policy create [options] [container] [name]
    storage container policy show [options] [container] [name]
    storage container policy list [options] [container]
    storage container policy set [options] [container] [name]
    storage container policy delete [options] [container] [name]

**Comandos para administrar el BLOB de almacenamiento**

    storage blob list [options] [container] [prefix]
    storage blob show [options] [container] [blob]
    storage blob delete [options] [container] [blob]
    storage blob upload [options] [file] [container] [blob]
    storage blob download [options] [container] [blob] [destination]

**Las operaciones de copia de comandos para administrar el blob**

    storage blob copy start [options] [sourceUri] [destContainer]
    storage blob copy show [options] [container] [blob]
    storage blob copy stop [options] [container] [blob] [copyid]

**Comandos para administrar compartido tener acceso a la firma de su blobs de almacenamiento**

    storage blob sas create [options] [container] [blob] [permissions] [expiry]

**Comandos para administrar los recursos compartidos de archivos de almacenamiento**

    storage share create [options] [share]
    storage share show [options] [share]
    storage share delete [options] [share]
    storage share list [options] [prefix]

**Comandos para administrar los archivos de almacenamiento**

    storage file list [options] [share] [path]
    storage file delete [options] [share] [path]
    storage file upload [options] [source] [share] [path]
    storage file download [options] [share] [path] [destination]

**Comandos para administrar el directorio de archivo de almacenamiento**

    storage directory create [options] [share] [path]
    storage directory delete [options] [share] [path]

**Comandos para administrar las colas de almacenamiento**

    storage queue create [options] [queue]
    storage queue list [options] [prefix]
    storage queue show [options] [queue]
    storage queue delete [options] [queue]

**Comandos para administrar compartido tener acceso a las firmas de la cola de almacenamiento**

    storage queue sas create [options] [queue] [permissions] [expiry]

**Las directivas de la cola de almacenamiento de acceso de comandos para administrar almacenado**

    storage queue policy create [options] [queue] [name]
    storage queue policy show [options] [queue] [name]
    storage queue policy list [options] [queue]
    storage queue policy set [options] [queue] [name]
    storage queue policy delete [options] [queue] [name]

**Comandos para administrar las propiedades de registro de almacenamiento**

    storage logging show [options]
    storage logging set [options]

**Comandos para administrar las propiedades de métrica de almacenamiento**

    storage metrics show [options]
    storage metrics set [options]

**Comandos para administrar el almacenamiento de tablas**

    storage table create [options] [table]
    storage table list [options] [prefix]
    storage table show [options] [table]
    storage table delete [options] [table]

**Comandos para administrar compartido tener acceso a las firmas de la tabla de almacenamiento**

    storage table sas create [options] [table] [permissions] [expiry]

**Las directivas de la tabla de almacenamiento de acceso de comandos para administrar almacenado**

    storage table policy create [options] [table] [name]
    storage table policy show [options] [table] [name]
    storage table policy list [options] [table]
    storage table policy set [options] [table] [name]
    storage table policy delete [options] [table] [name]

## <a name="azure-tag-commands-to-manage-your-resource-manager-tag"></a>etiqueta de Azure: comandos para administrar su etiqueta del Administrador de recursos

**Agregue una etiqueta**

    tag create [options] <name> <value>

**Quitar una etiqueta o un etiqueta valor**

    tag delete [options] <name> <value>

**Muestra la información de etiqueta**

    tag list [options]

**Obtener una etiqueta**

    tag show [options] [name]

## <a name="azure-vm-commands-to-manage-your-azure-virtual-machines"></a>Azure vm: comandos para administrar su máquinas virtuales de Azure

**Crear una máquina virtual**

    vm create [options] <resource-group> <name> <location> <os-type>

**Crear una máquina virtual con recursos predeterminados**

    vm quick-create [options] <resource-group> <name> <location> <os-type> <image-urn> <admin-username> <admin-password
    
>[AZURE.TIP]A partir de CLI versión 0.10, puede proporcionar un alias corto como "UbuntuLTS" o "Win2012R2Datacenter" como la `image-urn` para algunas imágenes de Marketplace populares. Ejecutar `azure help vm quick-create` de opciones. Además, empezando con la versión 0.10, `azure vm quick-create` usa almacenamiento premium predeterminada si está disponible en la región seleccionada.

**Las máquinas virtuales dentro de una cuenta de la lista**

    vm list [options]

**Obtener una máquina virtual dentro de un grupo de recursos**

    vm show [options] <resource-group> <name>

**Eliminar una máquina virtual dentro de un grupo de recursos**

    vm delete [options] <resource-group> <name>

**Cerrar una máquina virtual dentro de un grupo de recursos**

    vm stop [options] <resource-group> <name>

**Reiniciar una máquina virtual dentro de un grupo de recursos**

    vm restart [options] <resource-group> <name>

**Iniciar una máquina virtual dentro de un grupo de recursos**

    vm start [options] <resource-group> <name>

**Cerrar una máquina virtual dentro de un grupo de recursos y las versiones de los recursos de cálculo**

    vm deallocate [options] <resource-group> <name>

**Tamaños de la máquina virtual de disponibilidad de lista**

    vm sizes [options]

**Capturar la máquina virtual como imagen de sistema operativo o VM**

    vm capture [options] <resource-group> <name> <vhd-name-prefix>

**Establecer el estado de la máquina virtual en Generalized**

    vm generalize [options] <resource-group> <name>

**Obtener la vista de instancia de la máquina virtual**

    vm get-instance-view [options] <resource-group> <name>

**Habilitar restablecer la configuración de acceso remoto al escritorio o SSH en una máquina Virtual y restablecer la contraseña de la cuenta que tiene el administrador o autoridad de sudo**

    vm reset-access [options] <resource-group> <name>

**Actualizar VM con nuevos datos**

    vm set [options] <resource-group> <name>

**Comandos para administrar los discos de datos de máquina Virtual**

    vm disk attach-new [options] <resource-group> <vm-name> <size-in-gb> [vhd-name]
    vm disk detach [options] <resource-group> <vm-name> <lun>
    vm disk attach [options] <resource-group> <vm-name> [vhd-url]

**Comandos para administrar las extensiones de recursos VM**

    vm extension set [options] <resource-group> <vm-name> <name> <publisher-name> <version>
    vm extension get [options] <resource-group> <vm-name>

**Comandos para administrar la máquina Virtual de Docker**

    vm docker create [options] <resource-group> <name> <location> <os-type>

**Comandos para administrar las imágenes de máquina virtual**

    vm image list-publishers [options] <location>
    vm image list-offers [options] <location> <publisher>
    vm image list-skus [options] <location> <publisher> <offer>
    vm image list [options] <location> <publisher> [offer] [sku]
