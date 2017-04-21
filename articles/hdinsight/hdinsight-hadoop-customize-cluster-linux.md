<properties
    pageTitle="Personalizar clústeres HDInsight usar acciones de script | Microsoft Azure"
    description="Obtenga información sobre cómo agregar componentes personalizados a clústeres basados en Linux HDInsight usar acciones de Script. Acciones de script son intensiva de secuencias de comandos en los nodos del clúster y puede utilizarse para personalizar la configuración del clúster o agregar servicios adicionales y utilidades como matiz, Solr o R."
    services="hdinsight"
    documentationCenter=""
    authors="Blackmist"
    manager="jhubbard"
    editor="cgronlun"
    tags="azure-portal"/>

<tags
    ms.service="hdinsight"
    ms.workload="big-data"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/06/2016"
    ms.author="larryfr"/>

# <a name="customize-linux-based-hdinsight-clusters-using-script-action"></a>Personalizar clústeres basados en Linux HDInsight con la acción de secuencia de comandos

HDInsight proporciona una opción de configuración denominada **Acción de secuencia de comandos** que llama a los scripts personalizados que personalizar el clúster. Estas secuencias de comandos pueden utilizarse durante la creación de clúster o en un clúster ya en ejecución y se utilizan para instalar componentes adicionales o cambiar la configuración.

> [AZURE.NOTE] La capacidad de usar acciones de script en un clúster de ejecución solo está disponible para clústeres HDInsight basados en Linux. Para obtener información sobre cómo utilizar las acciones de script con clústeres basados en Windows, consulte [clústeres de personalizar HDInsight con la acción de secuencia de comandos (Windows)](hdinsight-hadoop-customize-cluster.md).

Acciones de secuencia de comandos también pueden publicarse a Azure Marketplace como una aplicación de HDInsight. Algunos de los ejemplos de este documento muestran cómo puede instalar una aplicación de HDInsight con los comandos de acción de secuencia de comandos de PowerShell y .NET SDK. Para obtener más información acerca de las aplicaciones de HDInsight, consulte [HDInsight publicar aplicaciones en Azure Marketplace](hdinsight-apps-publish-applications.md). 

## <a name="understanding-script-actions"></a>Acciones de Script de descripción

Una acción de secuencia de comandos es simplemente una fiesta ejecutaban secuencias de comandos que proporcionar una dirección URL y parámetros para, y, a continuación, en los nodos de clúster HDInsight. Las siguientes son características de acciones de script.

* Deben estar almacenados en un URI que sea accesible desde el clúster HDInsight. Los siguientes son posibles ubicaciones de almacenamiento:

    * Una cuenta de almacenamiento de blobs de Windows que sea el almacenamiento adicional o principal de la cuenta para el clúster de HDInsight. Puesto que HDInsight ha concedido acceso a ambos tipos de cuentas de almacenamiento durante la creación de un clúster, estos proporcionan una manera de usar una acción de secuencia de comandos no públicos.
    
    * URI legible públicamente como un blobs de Windows Azure, GitHub, OneDrive, Dropbox, etcetera.
    
    Para obtener ejemplos de URI de secuencias de comandos almacenados en el contenedor de blob (públicamente legible), consulte la sección de [secuencias de comandos de acción de secuencia de comandos de ejemplo](#example-script-action-scripts) .

* Se puede __ejecutar en sólo algunos tipos de nodo__, por ejemplo cabezales nodos o trabajo.

    > [AZURE.NOTE] Cuando se utiliza con HDInsight Premium, puede especificar que se debe usar la secuencia de comandos en el nodo del borde.

* Puede __conservar__ o __ad hoc__.

    Los scripts de __Persisted__ son secuencias de comandos que se aplican a los nodos de trabajo y se ha encontrado automáticamente en nuevos nodos creados al cambiar la escala de un clúster.

    Una secuencia de comandos almacenado también puede aplicar cambios a otro tipo de nodo, como un nodo principal, pero desde una perspectiva de funcionalidad la única razón para conservar una secuencia de comandos es para aplicarse a nuevos nodos de trabajo que creó al escala un clúster.

    > [AZURE.IMPORTANT] Acciones de secuencias de comandos persistentes deben tener un nombre único.

    No se conservan las secuencias de comandos __ad hoc__ ; Sin embargo, posteriormente puede promover una secuencia de comandos ad hoc de una secuencia de comandos persistente o disminuir el nivel de una secuencia de comandos almacenado en una secuencia de comandos ad hoc.

    > [AZURE.IMPORTANT] Acciones de script usadas durante la creación de un clúster se conservan automáticamente.
    >
    > Conservan las secuencias de comandos que no son de éxito, incluso si específicamente indica que se deben.

* Puede aceptar __parámetros__ que se usan la secuencia de comandos durante la ejecución.

* Se ha encontrado con __privilegios de nivel de raíz__ en los nodos de clúster.

* Puede usarse a través del __portal de Azure__, __Azure PowerShell__, __CLI de Azure__o __HDInsight.NET SDK__

    [AZURE.INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell-cli-and-dotnet-sdk.md)]

Para ayudar a comprender qué secuencias de comandos se han aplicado a un clúster y determinar que el identificador de secuencias de comandos de promoción o degradación, el clúster mantiene un historial de todas las secuencias de comandos que se han ejecutado.

> [AZURE.IMPORTANT] No hay ninguna forma automática para deshacer los cambios realizados por una acción de secuencia de comandos. Si necesita revertir los efectos de una secuencia de comandos, debe conocer los cambios realizados y manualmente invertir (o proporcionar una acción de secuencia de comandos que invierte ellos.)

### <a name="script-action-in-the-cluster-creation-process"></a>Acción de secuencia de comandos en el proceso de creación de clúster

Acciones de script usadas durante la creación de un clúster son ligeramente diferentes de secuencia de comandos de ejecución de acciones en un clúster existente:

* La secuencia de comandos es __almacena automáticamente__.

* Un __error__ en la secuencia de comandos para hacer que el proceso de creación de clúster no se realice correctamente.

El siguiente diagrama se muestra cuando se ejecuta la acción de secuencia de comandos durante el proceso de creación:

![Personalización de clúster HDInsight y fases durante la creación de clúster][img-hdi-cluster-states]

Se ejecutó la secuencia de comandos mientras se configura HDInsight. En este momento, la secuencia de comandos se ejecuta en paralelo en todos los nodos especificados en el clúster y se ha encontrado con privilegios raíz en los nodos.

> [AZURE.NOTE] Dado que la secuencia de comandos se ejecuta con privilegios de nivel raíz en los nodos de clúster, puede realizar operaciones como detener e iniciar servicios, incluidos los servicios de Hadoop. Si deja de servicios, debe asegurarse de que el servicio de Ambari y otros servicios de Hadoop son marcha antes de que finalice el script. Estos servicios son necesarios para determinar correctamente el estado y el estado del clúster mientras se está creando.

Durante la creación del clúster, puede especificar varias acciones de secuencia de comandos que se invocan en el orden en que se han especificado.

> [AZURE.IMPORTANT] Acciones de secuencia de comandos deben completarse dentro de 60 minutos o aparecerán tiempo de espera. Durante el aprovisionamiento de clúster, se ejecutó la secuencia de comandos simultáneamente con otros procesos de instalación y configuración. Competencia por recursos como el ancho de banda de red o el tiempo de CPU podría provocar tardar mucho más que tiene en su entorno de desarrollo de la secuencia de comandos.
>
> Para reducir el tiempo necesario para ejecutar la secuencia de comandos, evite tareas como descarga y compilar las aplicaciones de origen. En su lugar, la compilación previa de la aplicación y almacenar el archivo binario de almacenamiento de blobs de Windows Azure para que puede descargarse rápidamente al clúster.

###<a name="script-action-on-a-running-cluster"></a>Acción de secuencia de comandos en un clúster de ejecución

A diferencia de acciones que se usa durante la creación del clúster, un error en una secuencia de comandos ha encontrado en un clúster de ejecución de secuencia de comandos no provoca automáticamente el clúster cambiar a un estado de error. Finalizar una secuencia de comandos, el clúster debe volver a un estado "ejecutar".

> [AZURE.IMPORTANT] Esto significa que el clúster de ejecución es resistencia total a secuencias de comandos que hacer cosas incorrectas. Por ejemplo, una secuencia de comandos podría eliminar archivos necesarios para el clúster, cambiar configuración para que los servicios error, etcetera.
>
> Acciones de secuencias de comandos se ejecutan con privilegios de raíz, por lo que debe asegurarse de que comprendan lo que hace una secuencia de comandos antes de aplicar al clúster.

Al aplicar un script a un clúster, el estado de clúster cambiará a __ejecutando__ en __aceptado__, a continuación, __configuración de HDInsight__y finalmente volver a __Ejecutar__ las secuencias de comandos correcta. El estado de la secuencia de comandos se registra en el historial de acción de secuencia de comandos y puede usar para determinar si el script se realizó correctamente o no. Por ejemplo, el `Get-AzureRmHDInsightScriptActionHistory` se puede usar el cmdlet de PowerShell para ver el estado de una secuencia de comandos. Devuelve información similar al siguiente:

    ScriptExecutionId : 635918532516474303
    StartTime         : 2/23/2016 7:40:55 PM
    EndTime           : 2/23/2016 7:41:05 PM
    Status            : Succeeded

> [AZURE.NOTE] Si ha cambiado la contraseña de usuario (admin) clúster una vez creado el clúster, esto puede ocasionar acciones ejecutaban contra este clúster errores de script. Si tiene las acciones de script almacenado ese nodos de trabajo de destino, estos pueden fallar al agregar nodos al clúster a través de cambiar el tamaño de las operaciones.

## <a name="example-script-action-scripts"></a>Secuencias de comandos de acción de secuencia de comandos de ejemplo

Las secuencias de comandos de acción de secuencia de comandos pueden utilizarse desde el portal de Azure Azure PowerShell, Azure CLI o el SDK de .NET HDInsight. HDInsight proporciona secuencias de comandos para instalar los siguientes componentes en clústeres HDInsight:

Nombre | Secuencia de comandos
----- | -----
**Agregar una cuenta de almacenamiento de Azure** | https://hdiconfigactions.BLOB.Core.Windows.NET/linuxaddstorageaccountv01/Add-Storage-Account-V01.sh. Vea [aplicar una acción de Script a un clúster de ejecución](#apply-a-script-action-to-a-running-cluster).
**Instalar matiz** | https://hdiconfigactions.BLOB.Core.Windows.NET/linuxhueconfigactionv02/Install-Hue-uber-v02.sh. Vea [instalar y usar clústeres matiz en HDInsight](hdinsight-hadoop-hue-linux.md).
**Instalar R** | https://hdiconfigactions.BLOB.Core.Windows.NET/linuxrconfigactionv01/r-Installer-V01.sh. Consulte [instalar y usar R en clústeres HDInsight](hdinsight-hadoop-r-scripts-linux.md).
**Instalar Solr** | https://hdiconfigactions.BLOB.Core.Windows.NET/linuxsolrconfigactionv01/solr-Installer-V01.sh. Vea [instalar y usar clústeres Solr en HDInsight](hdinsight-hadoop-solr-install-linux.md).
**Instalar Giraph** | https://hdiconfigactions.BLOB.Core.Windows.NET/linuxgiraphconfigactionv01/giraph-Installer-V01.sh. Vea [instalar y usar clústeres Giraph en HDInsight](hdinsight-hadoop-giraph-install-linux.md).
| **Cargar previamente bibliotecas de sección** | https://hdiconfigactions.BLOB.Core.Windows.NET/linuxsetupcustomhivelibsv01/Setup-customhivelibs-V01.sh. Consulte [Agregar sección bibliotecas en clústeres HDInsight](hdinsight-hadoop-add-hive-libraries.md) |

## <a name="use-a-script-action-during-cluster-creation"></a>Usar una acción de secuencia de comandos durante la creación de clúster

Esta sección proporciona ejemplos de las distintas maneras en que puede usar acciones de secuencia de comandos al crear un clúster de HDInsight - desde el portal de Azure, mediante una plantilla de administrador de recursos de Azure, uso de PowerShell CMDlets y mediante el SDK de .NET.

### <a name="use-a-script-action-during-cluster-creation-from-the-azure-portal"></a>Usar una acción de secuencia de comandos durante la creación de clúster desde el portal de Azure

1. Empezar a crear un clúster como se describe en [Hadoop crear clústeres en HDInsight](hdinsight-provision-clusters.md#portal).

2. En __Configuración opcional__, para el módulo de **Secuencia de comandos de acciones** , haga clic en **Agregar acción de secuencia de comandos** para proporcionar información detallada sobre la acción de secuencia de comandos, tal como se muestra a continuación:

    ![Usar la acción de secuencia de comandos para personalizar un clúster](./media/hdinsight-hadoop-customize-cluster-linux/HDI.CreateCluster.8.png)

  	| (Propiedad) | Valor |
  	| -------- | ----- |
  	| Nombre | Especifique un nombre para la acción de secuencia de comandos. |
  	| URI de secuencia de comandos | Especifique el URI a la secuencia de comandos que se invoca para personalizar el clúster. |
  	| Cabeza/trabajo | Especifique los nodos (**cabeza**, **trabajo**o **ZooKeeper**) en el que se ejecute la secuencia de comandos de personalización. |
  	| Parámetros | Especificar los parámetros, si es necesario la secuencia de comandos. |

    Presione ENTRAR para agregar más de una acción de secuencia de comandos para instalar varios componentes en el clúster.

3. Haga clic en **Seleccionar** para guardar la configuración y continuar con la creación de un clúster.

### <a name="use-a-script-action-from-azure-resource-manager-templates"></a>Usar una acción de secuencia de comandos de plantillas de administrador de recursos de Azure

En esta sección, usamos el Administrador de recursos de Azure plantillas para crear un clúster de HDInsight y también usar una acción de secuencia de comandos para instalar componentes personalizados (en este ejemplo, R) en el clúster. Esta sección proporciona una plantilla de ejemplo para crear un clúster con la acción de secuencia de comandos.

> [AZURE.NOTE] Los pasos de esta sección muestran cómo crear un clúster mediante una acción de secuencia de comandos. Para obtener un ejemplo de la creación de un clúster de una plantilla con una aplicación de HDInsight, consulte [instalar aplicaciones personalizadas de HDInsight](hdinsight-apps-install-custom-applications.md).

#### <a name="before-you-begin"></a>Antes de empezar

* Para obtener información acerca de cómo configurar una estación de trabajo para ejecutar los cmdlets de HDInsight Powershell, consulte [instalar y configurar Azure PowerShell](../powershell-install-configure.md).
* Para obtener instrucciones sobre cómo crear plantillas, vea [plantillas de administrador de recursos de Azure de creación](../resource-group-authoring-templates.md).
* Si usted no ha utilizado previamente PowerShell Azure con el Administrador de recursos, vea [con Azure con el Administrador de recursos de Azure](../powershell-azure-resource-manager.md).

#### <a name="create-clusters-using-script-action"></a>Crear clústeres con la acción de secuencia de comandos

1. Copie la siguiente plantilla a una ubicación en el equipo. Esta plantilla instala Giraph en la headnodes, así como los nodos de trabajo en el clúster. También puede comprobar si la plantilla JSON es válida. Pegar el contenido de la plantilla en [JSONLint](http://jsonlint.com/), una herramienta de validación de JSON en línea.

            {
            "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
            "contentVersion": "1.0.0.0",
            "parameters": {
                "clusterLocation": {
                    "type": "string",
                    "defaultValue": "West US",
                    "allowedValues": [ "West US" ]
                },
                "clusterName": {
                    "type": "string"
                },
                "clusterUserName": {
                    "type": "string",
                    "defaultValue": "admin"
                },
                "clusterUserPassword": {
                    "type": "securestring"
                },
                "sshUserName": {
                    "type": "string",
                    "defaultValue": "username"
                },
                "sshPassword": {
                    "type": "securestring"
                },
                "clusterStorageAccountName": {
                    "type": "string"
                },
                "clusterStorageAccountResourceGroup": {
                    "type": "string"
                },
                "clusterStorageType": {
                    "type": "string",
                    "defaultValue": "Standard_LRS",
                    "allowedValues": [
                        "Standard_LRS",
                        "Standard_GRS",
                        "Standard_ZRS"
                    ]
                },
                "clusterStorageAccountContainer": {
                    "type": "string"
                },
                "clusterHeadNodeCount": {
                    "type": "int",
                    "defaultValue": 1
                },
                "clusterWorkerNodeCount": {
                    "type": "int",
                    "defaultValue": 2
                }
            },
            "variables": {
            },
            "resources": [
                {
                    "name": "[parameters('clusterStorageAccountName')]",
                    "type": "Microsoft.Storage/storageAccounts",
                    "location": "[parameters('clusterLocation')]",
                    "apiVersion": "2015-05-01-preview",
                    "dependsOn": [ ],
                    "tags": { },
                    "properties": {
                        "accountType": "[parameters('clusterStorageType')]"
                    }
                },
                {
                    "name": "[parameters('clusterName')]",
                    "type": "Microsoft.HDInsight/clusters",
                    "location": "[parameters('clusterLocation')]",
                    "apiVersion": "2015-03-01-preview",
                    "dependsOn": [
                        "[concat('Microsoft.Storage/storageAccounts/', parameters('clusterStorageAccountName'))]"
                    ],
                    "tags": { },
                    "properties": {
                        "clusterVersion": "3.2",
                        "osType": "Linux",
                        "clusterDefinition": {
                            "kind": "hadoop",
                            "configurations": {
                                "gateway": {
                                    "restAuthCredential.isEnabled": true,
                                    "restAuthCredential.username": "[parameters('clusterUserName')]",
                                    "restAuthCredential.password": "[parameters('clusterUserPassword')]"
                                }
                            }
                        },
                        "storageProfile": {
                            "storageaccounts": [
                                {
                                    "name": "[concat(parameters('clusterStorageAccountName'),'.blob.core.windows.net')]",
                                    "isDefault": true,
                                    "container": "[parameters('clusterStorageAccountContainer')]",
                                    "key": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', parameters('clusterStorageAccountName')), '2015-05-01-preview').key1]"
                                }
                            ]
                        },
                        "computeProfile": {
                            "roles": [
                                {
                                    "name": "headnode",
                                    "targetInstanceCount": "[parameters('clusterHeadNodeCount')]",
                                    "hardwareProfile": {
                                        "vmSize": "Large"
                                    },
                                    "osProfile": {
                                        "linuxOperatingSystemProfile": {
                                            "username": "[parameters('sshUserName')]",
                                            "password": "[parameters('sshPassword')]"
                                        }
                                    },
                                    "scriptActions": [
                                        {
                                            "name": "installGiraph",
                                            "uri": "https://hdiconfigactions.blob.core.windows.net/linuxgiraphconfigactionv01/giraph-installer-v01.sh",
                                            "parameters": ""
                                        }
                                    ]
                                },
                                {
                                    "name": "workernode",
                                    "targetInstanceCount": "[parameters('clusterWorkerNodeCount')]",
                                    "hardwareProfile": {
                                        "vmSize": "Large"
                                    },
                                    "osProfile": {
                                        "linuxOperatingSystemProfile": {
                                            "username": "[parameters('sshUserName')]",
                                            "password": "[parameters('sshPassword')]"
                                        }
                                    },
                                    "scriptActions": [
                                        {
                                            "name": "installR",
                                            "uri": "https://hdiconfigactions.blob.core.windows.net/linuxrconfigactionv01/r-installer-v01.sh",
                                            "parameters": ""
                                        }
                                    ]
                                }
                            ]
                        }
                    }
                }
            ],
            "outputs": {
                "cluster":{
                    "type" : "object",
                    "value" : "[reference(resourceId('Microsoft.HDInsight/clusters',parameters('clusterName')))]"
                }
            }
        }

2. Inicie Azure PowerShell e inicie sesión en su cuenta de Azure. Después de proporcionar sus credenciales, el comando devuelve información acerca de su cuenta.

        Add-AzureRmAccount

        Id                             Type       ...
        --                             ----
        someone@example.com            User       ...

3. Si tiene varias suscripciones, proporcione el identificador de la suscripción que desea usar para su implementación.

        Select-AzureRmSubscription -SubscriptionID <YourSubscriptionId>

    > [AZURE.NOTE] Puede usar `Get-AzureRmSubscription` para obtener una lista de todas las suscripciones asociadas con su cuenta, que incluye el identificador de la suscripción para cada uno.

5. Si no tiene un grupo de recursos existente, cree un nuevo grupo de recursos. Proporcione el nombre del grupo de recursos y la ubicación que necesita para la solución. Se devuelve un resumen del nuevo grupo de recursos.

        New-AzureRmResourceGroup -Name myresourcegroup -Location "West US"

        ResourceGroupName : myresourcegroup
        Location          : westus
        ProvisioningState : Succeeded
        Tags              :
        Permissions       :
                            Actions  NotActions
                            =======  ==========
                            *
        ResourceId        : /subscriptions/######/resourceGroups/ExampleResourceGroup


6. Para crear una nueva implementación para el grupo de recursos, ejecute el comando **AzureRmResourceGroupDeployment de nuevo** y proporcione los parámetros necesarios. Los parámetros incluyen un nombre para la implementación, el nombre de su grupo de recursos y la ruta de acceso o la dirección URL a la plantilla que creó. Si la plantilla requiere parámetros, se deben pasar también los parámetros. En este caso, la acción de secuencia de comandos para instalar R en el clúster no requiere los parámetros.


        New-AzureRmResourceGroupDeployment -Name mydeployment -ResourceGroupName myresourcegroup -TemplateFile <PathOrLinkToTemplate>


    Se le pedirá que proporcione valores para los parámetros que se definen en la plantilla.

7. Cuando se ha implementado el grupo de recursos, verá un resumen de la implementación.

          DeploymentName    : mydeployment
          ResourceGroupName : myresourcegroup
          ProvisioningState : Succeeded
          Timestamp         : 8/17/2015 7:00:27 PM
          Mode              : Incremental
          ...

8. Si se produce un error en la implementación, puede usar los siguientes cmdlets para obtener información sobre los errores.

        Get-AzureRmResourceGroupDeployment -ResourceGroupName myresourcegroup -ProvisioningState Failed

### <a name="use-a-script-action-during-cluster-creation-from-azure-powershell"></a>Usar una acción de secuencia de comandos durante la creación de un clúster de PowerShell de Azure

En esta sección, usamos el cmdlet [AzureRmHDInsightScriptAction agregar](https://msdn.microsoft.com/library/mt603527.aspx) invocar secuencias de comandos mediante acción de secuencia de comandos para personalizar un clúster. Antes de continuar, asegúrese de que ha instalado y configurado Azure PowerShell. Para obtener información acerca de cómo configurar una estación de trabajo para ejecutar los cmdlets de HDInsight PowerShell, consulte [instalar y configurar Azure PowerShell](../powershell-install-configure.md).

Realice los pasos siguientes:

1. Abra la consola de PowerShell de Azure y use las siguientes acciones para iniciar sesión en su suscripción de Azure y declarar algunas variables de PowerShell:

        # LOGIN TO ZURE
        Login-AzureRmAccount

        # PROVIDE VALUES FOR THESE VARIABLES
        $subscriptionId = "<SubscriptionId>"        # ID of the Azure subscription
        $clusterName = "<HDInsightClusterName>"         # HDInsight cluster name
        $storageAccountName = "<StorageAccountName>"    # Azure storage account that hosts the default container
        $storageAccountKey = "<StorageAccountKey>"      # Key for the storage account
        $containerName = $clusterName
        $location = "<MicrosoftDataCenter>"             # Location of the HDInsight cluster. It must be in the same data center as the storage account.
        $clusterNodes = <ClusterSizeInNumbers>          # The number of nodes in the HDInsight cluster.
        $resourceGroupName = "<ResourceGroupName>"      # The resource group that the HDInsight cluster will be created in

2. Especifique los valores de configuración (como nodos en el clúster) y el almacenamiento predeterminado que se usará.

        # SPECIFY THE CONFIGURATION OPTIONS
        Select-AzureRmSubscription -SubscriptionId $subscriptionId
        $config = New-AzureRmHDInsightClusterConfig
        $config.DefaultStorageAccountName="$storageAccountName.blob.core.windows.net"
        $config.DefaultStorageAccountKey=$storageAccountKey

3. Usar el cmdlet de **Agregar AzureRmHDInsightScriptAction** invocar la secuencia de comandos. En el ejemplo siguiente se utiliza una secuencia de comandos que se instala Giraph en el clúster:

        # INVOKE THE SCRIPT USING THE SCRIPT ACTION FOR HEADNODE AND WORKERNODE
        $config = Add-AzureRmHDInsightScriptAction -Config $config -Name "Install Giraph"  -NodeType HeadNode -Uri https://hdiconfigactions.blob.core.windows.net/linuxgiraphconfigactionv01/giraph-installer-v01.sh
        $config = Add-AzureRmHDInsightScriptAction -Config $config -Name "Install Giraph"  -NodeType WorkerNode -Uri https://hdiconfigactions.blob.core.windows.net/linuxgiraphconfigactionv01/giraph-installer-v01.sh

    El cmdlet **Add-AzureRmHDInsightScriptAction** acepta los siguientes parámetros:

  	| Parámetro | Definición |
  	| --------- | ---------- |
  	| Config | Objeto de configuración de qué script se agrega información de la acción. |
  	| Nombre | Nombre de la acción de secuencia de comandos. |
  	| NodeType | Especifica el nodo en que se ejecuta la secuencia de comandos de personalización. Los valores válidos son **HeadNode** (para instalar en el nodo principal), **WorkerNode** (para instalar en todos los nodos de datos), o **ZookeeperNode** (para instalar en el nodo zookeeper). |
  	| Parámetros | Parámetros requeridos por la secuencia de comandos. |
  	| URI | Especifica el URI a la secuencia de comandos que se ejecuta. |

4. Establecer el usuario administrador/HTTPS para el clúster:

        $httpCreds = get-credential

    Cuando se le pida, escriba 'admin' como el nombre y proporcione una contraseña.

5. Establecer las credenciales SSH:

        $sshCreds = get-credential

    Cuando se le pida, escriba el nombre de usuario SSH y la contraseña. Si desea proteger la cuenta SSH con un certificado en lugar de una contraseña, utilice una contraseña en blanco y establezca `$sshPublicKey` el contenido de la clave pública de certificado que desea usar. Por ejemplo:

        $sshPublicKey = Get-Content .\path\to\public.key -Raw

4. Por último, cree el clúster:

        New-AzureRmHDInsightCluster -config $config -clustername $clusterName -DefaultStorageContainer $containerName -Location $location -ResourceGroupName $resourceGroupName -ClusterSizeInNodes $clusterNodes -HttpCredential $httpCreds -SshCredential $sshCreds -OSType Linux

    Si está utilizando una clave pública para proteger la cuenta SSH, también debe especificar `-SshPublicKey $sshPublicKey` como un parámetro.

Puede tardar varios minutos antes de crear el clúster.

### <a name="use-a-script-action-during-cluster-creation-from-the-hdinsight-net-sdk"></a>Usar una acción de secuencia de comandos durante la creación de un clúster de HDInsight .NET SDK

HDInsight .NET SDK proporciona bibliotecas de cliente que hace que sea más fácil trabajar con HDInsight desde una aplicación .NET. Un ejemplo de código, vea [clústeres basados en Linux crear en HDInsight mediante .NET SDK](hdinsight-hadoop-create-linux-clusters-dotnet-sdk.md#use-script-action).

## <a name="apply-a-script-action-to-a-running-cluster"></a>Aplicar una acción de Script a un clúster de ejecución

Esta sección proporciona ejemplos de las distintas maneras en que puede aplicar acciones de script a un clúster de HDInsight ejecución; desde el portal de Azure, usar CMDlets de PowerShell, con la CLI de Azure entre plataformas y mediante .NET SDK. La acción de secuencia de comandos almacenado utilizada en esta sección agrega una cuenta de almacenamiento de Azure existente a un clúster de ejecución. También puede usar otras acciones de secuencia de comandos, vea [secuencias de comandos de acción de secuencia de comandos de ejemplo](#example-script-action-scripts).

### <a name="apply-a-script-action-to-a-running-cluster-from-the-azure-portal"></a>Aplicar una acción de Script a un clúster de ejecución desde el portal de Azure

1. Desde el [portal de Azure](https://portal.azure.com), seleccione el clúster HDInsight.

2. En el módulo de clúster HDInsight, seleccione el icono de __Acciones de Script__ .

    ![Mosaico de acciones de script](./media/hdinsight-hadoop-customize-cluster-linux/scriptactionstile.png)

    > [AZURE.NOTE] Puede seleccionar también __toda la configuración__ y, a continuación, seleccione __Acciones de Script__ desde el módulo de configuración.

4. Desde la parte superior del módulo de acciones de secuencia de comandos, seleccione __Enviar de nuevo__.

    ![Nuevo icono de envío](./media/hdinsight-hadoop-customize-cluster-linux/newscriptaction.png)

5. Desde el módulo de agregar acción de secuencia de comandos, escriba la información siguiente.

    * __Nombre__: el nombre descriptivo para esta acción de secuencia de comandos. En este ejemplo, `Add Storage account`.
    * __URI de SCRIPT__: URI a la secuencia de comandos. En este ejemplo,`https://hdiconfigactions.blob.core.windows.net/linuxaddstorageaccountv01/add-storage-account-v01.sh`
    * __Cabeza__, __trabajo__y __Zookeeper__: comprobar los nodos que debe aplicarse esta secuencia de comandos. En este ejemplo, se comprueban cabeza, trabajo y Zookeeper.
    * __Parámetros__: si la secuencia de comandos acepta parámetros, introducirlos aquí. En este ejemplo, escriba el nombre de la cuenta de almacenamiento y la clave de cuenta de almacenamiento:

        ![hdinsight conserva la cuenta de almacenamiento de secuencias de comandos acción accesorios clústeres en ejecución](./media/hdinsight-hadoop-customize-cluster-linux/hdinsight-persisted-script-action-add-storage-account.png)

        En la captura de pantalla `contosodata` es una cuenta existente de almacenamiento de Azure, la segunda línea es la clave de cuenta de almacenamiento.
    * __PERSISTED__: Active esta entrada si desea conservar la secuencia de comandos para que se aplicará a nuevos nodos de trabajo cuando escalar el clúster.

6. Por último, utilice el botón __crear__ para aplicar la secuencia de comandos al clúster.

### <a name="apply-a-script-action-to-a-running-cluster-from-azure-powershell"></a>Aplicar una acción de Script a un clúster de ejecución de PowerShell de Azure

Antes de continuar, asegúrese de que ha instalado y configurado Azure PowerShell. Para obtener información acerca de cómo configurar una estación de trabajo para ejecutar los cmdlets de HDInsight PowerShell, consulte [instalar y configurar Azure PowerShell](../powershell-install-configure.md).

1. Abra la consola de PowerShell de Azure y use las siguientes acciones para iniciar sesión en su suscripción de Azure y declarar algunas variables de PowerShell:

        # LOGIN TO ZURE
        Login-AzureRmAccount

        # PROVIDE VALUES FOR THESE VARIABLES
        $clusterName = "<HDInsightClusterName>"         # HDInsight cluster name
        $saName = "<ScriptActionName>"                  # Name of the script action
        $saURI = "<URI to the script>"                  # The URI where the script is located
        $nodeTypes = "headnode", "workernode"
        
    > [AZURE.NOTE] Si usa un clúster HDInsight Premium, puede usar un nodetype de `"edgenode"` para ejecutar el script en el nodo del borde.

2. Use el comando siguiente para aplicar la secuencia de comandos en el clúster:

        Submit-AzureRmHDInsightScriptAction -ClusterName $clusterName -Name $saName -Uri $saURI -NodeTypes $nodeTypes -PersistOnSuccess

    Una vez completada la tarea, debe recibir información similar a la siguiente:

        OperationState  : Succeeded
        ErrorMessage    :
        Name            : Giraph
        Uri             : https://hdiconfigactions.blob.core.windows.net/linuxgiraphconfigactionv01/giraph-installer-v01.sh
        Parameters      :
        NodeTypes       : {HeadNode, WorkerNode}

### <a name="apply-a-script-action-to-a-running-cluster-from-the-azure-cli"></a>Aplicar una acción de Script a un clúster de ejecución de la CLI de Azure

Antes de continuar, asegúrese de que ha instalado y configurado CLI Azure. Para obtener más información, consulte [instalar CLI Azure](../xplat-cli-install.md).

    [AZURE.INCLUDE [use-latest-version](../../includes/hdinsight-use-latest-cli.md)] 

1. Abra una sesión de shell, terminal, símbolo u otra línea de comandos para su sistema y use el comando siguiente para cambiar al modo de administrador de recursos de Azure.

        azure config mode arm

2. Use las siguientes acciones para autenticar a su suscripción de Azure.

        azure login

3. Utilice el comando siguiente para aplicar una acción de script a un clúster de ejecución

        azure hdinsight script-action create <clustername> -g <resourcegroupname> -n <scriptname> -u <scriptURI> -t <nodetypes>

    Si omite los parámetros para este comando, se le pedirá para ellos. Si la secuencia de comandos que especifique con `-u` acepta parámetros, puede especificarlos utilizando la `-p` parámetro.

    Válido __nodetypes__ son __headnode__, __workernode__y __zookeeper__. Si la secuencia de comandos debe aplicarse a varios tipos de nodo, especifique los tipos separados por un ';'. Por ejemplo, `-n headnode;workernode`.

    Para conservar la secuencia de comandos, agregue el `--persistOnSuccess`. También puede guardar la secuencia de comandos en una fecha posterior con `azure hdinsight script-action persisted set`.
    
    Una vez completada la tarea, recibirá un resultado similar al siguiente.
    
        info:    Executing command hdinsight script-action create
        + Executing Script Action on HDInsight cluster
        data:    Operation Info
        data:    ---------------
        data:    Operation status:
        data:    Operation ID:  b707b10e-e633-45c0-baa9-8aed3d348c13
        info:    hdinsight script-action create command OK

### <a name="apply-a-script-action-to-a-running-cluster-using-rest-api"></a>Aplicar una acción de Script a un clúster de ejecución con la API de REST

Consulte [Ejecutar acciones de Script en un clúster de ejecución](https://msdn.microsoft.com/library/azure/mt668441.aspx).
### <a name="apply-a-script-action-to-a-running-cluster-from-the-hdinsight-net-sdk"></a>Aplicar una acción de Script a un clúster de ejecución de HDInsight .NET SDK

Para obtener un ejemplo de uso de .NET SDK para aplicar secuencias de comandos en un clúster, consulte [https://github.com/Azure-Samples/hdinsight-dotnet-script-action](https://github.com/Azure-Samples/hdinsight-dotnet-script-action).

## <a name="view-history-promote-and-demote-script-actions"></a>Ver el historial, promover y degradar acciones de Script

### <a name="using-the-azure-portal"></a>Con el portal de Azure

1. Desde el [portal de Azure](https://portal.azure.com), seleccione el clúster HDInsight.

2. En el módulo de clúster HDInsight, seleccione __configuración__.

    ![Icono Configuración](./media/hdinsight-hadoop-customize-cluster-linux/settingsicon.png)

3. En el módulo de configuración, seleccione __Acciones de Script__.

    ![Vínculo de acciones de script](./media/hdinsight-hadoop-customize-cluster-linux/settings.png)

4. Se muestra una lista de las secuencias de comandos persistentes, así como un historial de secuencias de comandos que se aplica al clúster, en el módulo de acciones de Script. En la captura de pantalla siguiente, puede ver que se ha ejecutado la Solr ha sido el script en este clúster, pero que no se han almacenado acciones de script.

    ![Módulo de acciones de script](./media/hdinsight-hadoop-customize-cluster-linux/scriptactionhistory.png)

5. Si selecciona una secuencia de comandos desde el historial, aparecerá la hoja de propiedades para esta secuencia de comandos. Desde la parte superior del módulo, puede volver a ejecutar la secuencia de comandos o promover él.

    ![Hoja de propiedades de acciones de script](./media/hdinsight-hadoop-customize-cluster-linux/scriptactionproperties.png)

6. También puede usar el __...__ a la derecha de las entradas en el módulo de acciones de secuencia de comandos para realizar acciones como volver a ejecutar, conservar o (para persistentes acciones), eliminar.

    ![Acciones de secuencias de comandos... uso](./media/hdinsight-hadoop-customize-cluster-linux/deletepromoted.png)

### <a name="using-azure-powershell"></a>Uso de PowerShell de Azure

| Use la siguiente... | Para... |
| ----- | ----- |
| Get-AzureRmHDInsightPersistedScriptAction | Obtener información sobre las acciones de secuencias de comandos persistentes |
| Get-AzureRmHDInsightScriptActionHistory | Recuperar un historial de acciones de script aplicado al clúster o detalles de una secuencia de comandos específico |
| Establecer AzureRmHDInsightPersistedScriptAction | Promueve una acción de secuencia de comandos ad hoc a una acción de secuencia de comandos almacenado |
| Quitar AzureRmHDInsightPersistedScriptAction | Reduce una acción de secuencia de comandos almacenado a una acción ad hoc |

> [AZURE.IMPORTANT] Usando `Remove-AzureRmHDInsightPersistedScriptAction` no no deshacer las acciones realizadas por una secuencia de comandos, sólo se quita la marca persistente para que la secuencia de comandos no se ha encontrado en nuevos nodos de trabajo agregados al clúster.

La siguiente secuencia de comandos de ejemplo muestra el uso de los cmdlets que promover, disminuir el nivel de una secuencia de comandos.

    # Get a history of scripts
    Get-AzureRmHDInsightScriptActionHistory -ClusterName mycluster

    # From the list, we want to get information on a specific script
    Get-AzureRmHDInsightScriptActionHistory -ClusterName mycluster -ScriptExecutionId 635920937765978529

    # Promote this to a persisted script
    # Note: the script must have a unique name to be promoted
    # if the name is not unique, you will receive an error
    Set-AzureRmHDInsightPersistedScriptAction -ClusterName mycluster -ScriptExecutionId 635920937765978529

    # Demote the script back to ad hoc
    # Note that demotion uses the unique script name instead of
    # execution ID.
    Remove-AzureRmHDInsightPersistedScriptAction -ClusterName mycluster -Name "Install Giraph"

### <a name="using-the-azure-cli"></a>Uso de la CLI de Azure

| Use la siguiente... | Para... |
| ----- | ----- |
| `azure hdinsight script-action persisted list <clustername>` | Recuperar una lista de acciones de secuencias de comandos persistentes |
| `azure hdinsight script-action persisted show <clustername> <scriptname>` | Obtener información sobre una acción de secuencias de comandos persistentes específicos |
| `azure hdinsight script-action history list <clustername>` | Recuperar un historial de acciones de script aplicado al clúster |
| `azure hdinsight script-action history show <clustername> <scriptname>` | Obtener información sobre una acción de secuencia de comandos específica |
| `azure hdinsight script action persisted set <clustername> <scriptexecutionid>` | Promueve una acción de secuencia de comandos ad hoc a una acción de secuencia de comandos almacenado |
| `azure hdinsight script-action persisted delete <clustername> <scriptname>` | Reduce una acción de secuencia de comandos almacenado a una acción ad hoc |

> [AZURE.IMPORTANT] Usando `azure hdinsight script-action persisted delete` no no deshacer las acciones realizadas por una secuencia de comandos, sólo se quita la marca persistente para que la secuencia de comandos no se ha encontrado en nuevos nodos de trabajo agregados al clúster.

### <a name="using-the-hdinsight-net-sdk"></a>Mediante el SDK de .NET HDInsight

Para obtener un ejemplo de uso de .NET SDK para recuperar el historial de secuencia de comandos de un clúster, aumentar o disminuir el nivel de secuencias de comandos, consulte [https://github.com/Azure-Samples/hdinsight-dotnet-script-action](https://github.com/Azure-Samples/hdinsight-dotnet-script-action).

> [AZURE.NOTE] En este ejemplo también se muestra cómo instalar una aplicación de HDInsight mediante el SDK de .NET.

## <a name="troubleshooting"></a>Solución de problemas

Puede usar Ambari web UI para ver información que registra acciones de script. Si se ha utilizado la secuencia de comandos durante la creación de clúster y creación de un clúster error debido a un error en la secuencia de comandos, los registros también están disponibles en la cuenta de almacenamiento predeterminada asociada con el clúster. Esta sección proporciona información sobre cómo recuperar los registros de ambas opciones.

### <a name="using-the-ambari-web-ui"></a>Uso de la interfaz de usuario de Web Ambari

1. En el explorador, vaya a https://CLUSTERNAME.azurehdinsight.net. Reemplazar NOMBREDECLÚSTER con el nombre de su clúster HDInsight.

    Cuando se le pida, escriba el nombre de la cuenta de administrador (admin) y la contraseña para el clúster. Debe volver a escribir las credenciales de administrador en un formulario web.

2. En la barra de la parte superior de la página, seleccione la entrada de __operaciones__ . Esto le mostrará una lista de anteriores y las actuales operaciones realizadas en el clúster a través de Ambari.

    ![Barra de interfaz de usuario de web Ambari con operaciones seleccionado](./media/hdinsight-hadoop-customize-cluster-linux/ambari-nav.png)

3. Buscar las entradas que tienen __ejecutar\_customscriptaction__ en la columna de __operaciones__ . Se crean cuando se ha encontrado las acciones de Script.

    ![Captura de pantalla de las operaciones](./media/hdinsight-hadoop-customize-cluster-linux/ambariscriptaction.png)

    Seleccione esta entrada y, a continuación, ejecutaban resúmenes a través de los vínculos para ver el resultado STDOUT y STDERR generado cuando la secuencia de comandos en el clúster.

### <a name="access-logs-from-the-default-storage-account"></a>Registros de acceso de la cuenta de almacenamiento predeterminada

Si se produce un error en la creación de clúster debido a un error en acción de secuencia de comandos, los registros de la acción de secuencia de comandos aún se pueden acceder directamente desde la cuenta de almacenamiento predeterminada asociada con el clúster.

* Los registros de almacenamiento están disponibles en `\STORAGE_ACOCUNT_NAME\DEFAULT_CONTAINER_NAME\custom-scriptaction-logs\CLUSTER_NAME\DATE`.

    ![Captura de pantalla de las operaciones](./media/hdinsight-hadoop-customize-cluster-linux/script_action_logs_in_storage.png)

    En este caso, los registros están organizados por separado para los nodos de zookeeper, workernode y headnode. Algunos ejemplos son:
    * **Headnode** - `<uniqueidentifier>AmbariDb-hn0-<generated_value>.cloudapp.net`
    * **Nodo de trabajo** - `<uniqueidentifier>AmbariDb-wn0-<generated_value>.cloudapp.net`
    * **Nodo de zookeeper** - `<uniqueidentifier>AmbariDb-zk0-<generated_value>.cloudapp.net`

* Todos los stdout y stderr del host correspondiente se cargará a la cuenta de almacenamiento. Hay una **salida -\*.txt** y **errores -\*.txt** por cada acción de secuencia de comandos. El archivo de salida *.txt contiene información sobre el URI de la secuencia de comandos que obtuvo ejecutar en el host. Por ejemplo

        'Start downloading script locally: ', u'https://hdiconfigactions.blob.core.windows.net/linuxrconfigactionv01/r-installer-v01.sh'

* Es posible crear repetidamente un clúster de acción de secuencia de comandos con el mismo nombre. En este caso, puede distinguir los registros relevantes basándose en el nombre de carpeta de fecha. Por ejemplo, tendrá la estructura de carpetas para un clúster (MiClúster) creado en diferentes fechas:
    * `\STORAGE_ACOCUNT_NAME\DEFAULT_CONTAINER_NAME\custom-scriptaction-logs\mycluster\2015-10-04`
    * `\STORAGE_ACOCUNT_NAME\DEFAULT_CONTAINER_NAME\custom-scriptaction-logs\mycluster\2015-10-05`

* Si crea un clúster de acción de secuencia de comandos con el mismo nombre en el mismo día, puede usar el prefijo único para identificar los archivos de registro relevantes.

* Si crea un clúster al final del día, es posible que los archivos de registro abarcan dos días. En estos casos, verá dos carpetas de otra fecha para el mismo clúster.

* Cargar archivos de registro en el contenedor predeterminado puede tardar hasta 5 minutos, especialmente para clústeres grandes. Por lo tanto, si desea tener acceso a los registros, no debe inmediatamente eliminar el clúster si se produce un error en una acción de secuencia de comandos.


## <a name="support-for-open-source-software-used-on-hdinsight-clusters"></a>Compatibilidad con software de código abierto utilizado en clústeres HDInsight

El servicio de Microsoft Azure HDInsight es una plataforma flexible que le permite crear aplicaciones de datos grande en la nube mediante un ecosistema de tecnologías de código abierto formado por Hadoop. Microsoft Azure proporciona un nivel de compatibilidad general para las tecnologías de código abierto, como se describe en la sección **Ámbito de soporte técnico** del [sitio Web de P+F de soporte técnico de Azure](https://azure.microsoft.com/support/faq/). El servicio de HDInsight proporciona un nivel adicional de soporte técnico para algunos de los componentes, como se describe a continuación.

Existen dos tipos de componentes de código abierto que están disponibles en el servicio de HDInsight:

- **Componentes integrados** - estos componentes preinstalados en clústeres HDInsight y proporcionan la funcionalidad básica del clúster. Por ejemplo, hilo ResourceManager, el lenguaje de consulta de la sección (HiveQL) y la biblioteca de Mahout pertenecen a esta categoría. Una lista completa de los componentes de clúster está disponible en [Novedades en las versiones de clúster Hadoop proporcionadas por HDInsight?](hdinsight-component-versioning.md).

- **Componentes personalizados** :, como un usuario del clúster, puede instalar o usar en su carga de trabajo de cualquier componente disponible en la Comunidad o creado por el usuario.

> [AZURE.WARNING] Componentes proporcionados con el clúster HDInsight son totalmente compatibles y Microsoft Support le ayudará a aislar y resolver los problemas relacionados con estos componentes.
>
> Componentes personalizados reciban soporte comercialmente razonable para ayudar a solucionar el problema. Esto puede ocasionar resolver el problema o preguntar si desea integrarse canales disponibles para las tecnologías de código abierto donde se encuentra la amplia experiencia para que la tecnología. Por ejemplo, hay muchos sitios de comunidad que se pueden usar como: [foro de MSDN para HDInsight](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=hdinsight), [http://stackoverflow.com](http://stackoverflow.com). También proyectos Apache tienen sitios de proyecto en [http://apache.org](http://apache.org), por ejemplo: [Hadoop](http://hadoop.apache.org/).

El servicio de HDInsight ofrece varias formas de usar componentes personalizados. Independientemente de cómo un componente utilizado o instalado en el clúster, se aplica el mismo nivel de compatibilidad. A continuación se muestra una lista de las formas más comunes que se pueden utilizar componentes personalizados en clústeres HDInsight:

1. Envío de trabajos - Hadoop u otros tipos de tareas que se ejecutan o utilizan componentes personalizados se puede enviar al clúster.

2. Personalización de clúster - durante la creación del clúster, puede especificar opciones adicionales y componentes personalizados que se instalarán en los nodos de clúster.

3. Ejemplos: para componentes personalizados populares, Microsoft y otros usuarios pueden proporcionar ejemplos de cómo se pueden utilizar estos componentes en los clústeres HDInsight. Estos ejemplos se proporcionan sin soporte técnico.

##<a name="troubleshooting"></a>Solución de problemas

###<a name="history-doesnt-show-scripts-used-during-cluster-creation"></a>Historial no muestra las secuencias de comandos que se usa durante la creación de un clúster

Si creó el clúster antes de 15 de marzo de 2016, quizás no pueda ver una entrada en el historial de acción de secuencia de comandos de las secuencias de comandos que se usa durante la creación de un clúster. Sin embargo, si cambia el tamaño del clúster después de 15 de marzo de 2016, las secuencias de comandos con durante la creación de clúster aparecerá en historial tal como se aplican a nuevos nodos en el clúster como parte de la operación de cambio de tamaño.

Hay dos excepciones:

* Si se creó el clúster antes del 1 de septiembre de 2015. Esto es cuando se introduce acciones de Script, cualquier clúster creado antes de esta fecha podría no haya utilizado acciones de secuencia de comandos para la creación de clúster.

* Si usa varias acciones de Script durante la creación de un clúster y utiliza el mismo nombre de varias secuencias de comandos o el mismo nombre, URI del mismo, pero distintos parámetros de varias secuencias de comandos. En estos casos, recibirá el siguiente error.

    No hay nuevas acciones de secuencia de comandos se pueden ejecutar en este clúster debido a los nombres de secuencia de comandos en conflicto en secuencias de comandos existentes. Nombres de secuencia de comandos situados en clúster crear deben ser únicas. Secuencias de comandos existentes se ejecute al cambiar de tamaño.

## <a name="next-steps"></a>Pasos siguientes

Vea la siguiente información y ejemplos sobre cómo crear y usar las secuencias de comandos para personalizar un clúster:

- [Desarrollar secuencias de comandos de acción de secuencia de comandos para HDInsight](hdinsight-hadoop-script-actions-linux.md)
- [Instalar y usar Solr en clústeres HDInsight](hdinsight-hadoop-solr-install-linux.md)
- [Instalar y usar Giraph en clústeres HDInsight](hdinsight-hadoop-giraph-install-linux.md)



[img-hdi-cluster-states]: ./media/hdinsight-hadoop-customize-cluster-linux/HDI-Cluster-state.png "Fases durante la creación de clúster"
