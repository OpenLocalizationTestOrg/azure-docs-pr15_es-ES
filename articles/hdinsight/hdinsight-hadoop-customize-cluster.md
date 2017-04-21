<properties
    pageTitle="Personalizar clústeres HDInsight usar acciones de script | Microsoft Azure"
    description="Obtenga información sobre cómo personalizar clústeres HDInsight con la acción de secuencia de comandos."
    services="hdinsight"
    documentationCenter=""
    authors="nitinme"
    manager="jhubbard"
    editor="cgronlun"
    tags="azure-portal"/>

<tags
    ms.service="hdinsight"
    ms.workload="big-data"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/05/2016"
    ms.author="nitinme"/>

# <a name="customize-windows-based-hdinsight-clusters-using-script-action"></a>Personalizar clústeres HDInsight basado en Windows con la acción de secuencia de comandos

**Acción de secuencia de comandos** puede utilizarse para invocar [scripts personalizados](hdinsight-hadoop-script-actions.md) durante el proceso de creación de clúster para instalar software adicional en un clúster.

La información en este artículo es específica para clústeres HDInsight basado en Windows. Para clústeres basados en Linux, consulte [clústeres basados en Linux personalizar HDInsight con la acción de secuencia de comandos](hdinsight-hadoop-customize-cluster-linux.md). 

HDInsight clústeres pueden personalizarse en una gran variedad de otras formas, por ejemplo, incluidas cuentas adicionales de almacenamiento de Azure, cambiar la Hadoop (core site.xml, subárbol site.xml, etc.) de los archivos de configuración o agregar compartido bibliotecas (por ejemplo, subárbol, Oozie) a las ubicaciones comunes en el clúster. Estas personalizaciones pueden realizarse a través de PowerShell de Azure, el SDK de .NET HDInsight de Azure o el Portal de Azure. Para obtener más información, consulte [crear Hadoop clústeres en HDInsight][hdinsight-provision-cluster].

[AZURE.INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell-cli-and-dotnet-sdk.md)]

## <a name="script-action-in-the-cluster-creation-process"></a>Acción de secuencia de comandos en el proceso de creación de clúster

Solo se utiliza la acción de secuencia de comandos mientras está un clústeres se está creando. El siguiente diagrama se muestra cuando se ejecuta la acción de secuencia de comandos durante el proceso de creación:

![Personalización de clúster HDInsight y fases durante la creación de clúster][img-hdi-cluster-states]

Cuando se está ejecutando el script, el clúster entra en la fase de **ClusterCustomization** . En este momento, la secuencia de comandos se ejecuta en la cuenta de administrador del sistema, en paralelo en todos los nodos especificados en el clúster y proporciona privilegios de administrador completa en los nodos.

> [AZURE.NOTE] Debido a que tiene privilegios de administrador en los nodos de clúster durante la fase de **ClusterCustomization** , puede utilizar la secuencia de comandos para realizar operaciones como detener e iniciar servicios, incluidos los servicios de Hadoop. Por lo tanto, como parte de la secuencia de comandos, debe asegurarse de que los servicios de Ambari y otros servicios de Hadoop son marcha antes de que finalice el script. Estos servicios son necesarios para determinar correctamente el estado y el estado del clúster mientras se crea. Si cambia cualquier configuración en el clúster que afecta a los servicios siguientes, debe utilizar las funciones que se proporcionan. Para obtener más información sobre las funciones auxiliares, vea [secuencias de comandos de acción de secuencia de comandos de desarrollar para HDInsight][hdinsight-write-script].

El resultado y los registros de errores de la secuencia de comandos se almacenan en la cuenta de almacenamiento predeterminada especificada para el clúster. Los registros se almacenan en una tabla con el nombre **u < \cluster-name-fragment >< \time-stamp > setuplog**. Estos son los registros de agregado de la secuencia de comandos que se ejecute en todos los nodos (nodo principal y los nodos de trabajo) en el clúster.

Cada clúster puede aceptar varias acciones de secuencia de comandos que se invocan en el orden en que se especifican. Una secuencia de comandos que puede ejecutar en el nodo principal, los nodos de trabajo o ambos.

HDInsight proporciona varias secuencias de comandos para instalar los siguientes componentes en clústeres HDInsight:

Nombre | Secuencia de comandos
----- | -----
**Instalar motor** | https://hdiconfigactions.BLOB.Core.Windows.NET/sparkconfigactionv03/Spark-Installer-v03.ps1. Vea [instalar y usar despierten en clústeres HDInsight][hdinsight-install-spark].
**Instalar R** | https://hdiconfigactions.BLOB.Core.Windows.NET/rconfigactionv02/r-Installer-v02.ps1. Consulte [instalar y usar R en clústeres HDInsight][hdinsight-install-r].
**Instalar Solr** | https://hdiconfigactions.BLOB.Core.Windows.NET/solrconfigactionv01/solr-Installer-V01.ps1. Vea [instalar y usar clústeres Solr en HDInsight](hdinsight-hadoop-solr-install.md).
- **Instalar Giraph** | https://hdiconfigactions.BLOB.Core.Windows.NET/giraphconfigactionv01/giraph-Installer-V01.ps1. Vea [instalar y usar clústeres Giraph en HDInsight](hdinsight-hadoop-giraph-install.md).
| **Cargar previamente bibliotecas de sección** | https://hdiconfigactions.BLOB.Core.Windows.NET/setupcustomhivelibsv01/Setup-customhivelibs-V01.ps1. Consulte [Agregar sección bibliotecas en clústeres HDInsight](hdinsight-hadoop-add-hive-libraries.md) |


## <a name="call-scripts-using-the-azure-portal"></a>Secuencias de comandos de la llamada con el Portal de Azure

**Desde el Portal de Azure**

1. Empezar a crear un clúster como se describe en [Hadoop crear clústeres en HDInsight](hdinsight-provision-clusters.md#portal).
2. En configuración opcional, para el módulo de **Secuencia de comandos de acciones** , haga clic en **Agregar acción de secuencia de comandos** para proporcionar información detallada sobre la acción de secuencia de comandos, tal como se muestra a continuación:

    ![Acción de secuencia de comandos de uso para personalizar un clúster] (./media/hdinsight-hadoop-customize-cluster/HDI.CreateCluster.8.png "Acción de secuencia de comandos de uso para personalizar un clúster")

    <table border='1'>
        <tr><th>(Propiedad)</th><th>Valor</th></tr>
        <tr><td>Nombre</td>
            <td>Especifique un nombre para la acción de secuencia de comandos.</td></tr>
        <tr><td>URI de secuencia de comandos</td>
            <td>Especifique el URI a la secuencia de comandos que se invoca para personalizar el clúster. s</td></tr>
        <tr><td>Cabeza/trabajo</td>
            <td>Especifique los nodos (**cabeza** o **trabajo**) en el que se ejecute la secuencia de comandos de personalización. </b>.
        <tr><td>Parámetros</td>
            <td>Especificar los parámetros, si es necesario la secuencia de comandos.</td></tr>
    </table>

    Presione ENTRAR para agregar más de una acción de secuencia de comandos para instalar varios componentes en el clúster.

3. Haga clic en **Seleccionar** para guardar la configuración de acción de secuencia de comandos y continuar con la creación de un clúster.

## <a name="call-scripts-using-azure-powershell"></a>Secuencias de comandos de la llamada con PowerShell de Azure

Esta secuencia de comandos de PowerShell siguiente muestra cómo instalar el motor en clúster en función de HDInsight de Windows.  

    # Provide values for these variables
    $subscriptionID = "<Azure Suscription ID>" # After "Login-AzureRmAccount", use "Get-AzureRmSubscription" to list IDs.

    $nameToken = "<Enter A Name Token>"  # The token is use to create Azure service names.
    $namePrefix = $nameToken.ToLower() + (Get-Date -Format "MMdd")
    
    $resourceGroupName = $namePrefix + "rg"
    $location = "EAST US 2" # used for creating resource group, storage account, and HDInsight cluster.
    
    $hdinsightClusterName = $namePrefix + "spark"
    $httpUserName = "admin"
    $httpPassword = "<Enter a Password>"
    
    $defaultStorageAccountName = "$namePrefix" + "store"
    $defaultBlobContainerName = $hdinsightClusterName
    
    #############################################################
    # Connect to Azure
    #############################################################
    
    Try{
        Get-AzureRmSubscription
    }
    Catch{
        Login-AzureRmAccount
    }
    Select-AzureRmSubscription -SubscriptionId $subscriptionID
    
    #############################################################
    # Prepare the dependent components
    #############################################################
    
    # Create resource group
    New-AzureRmResourceGroup -Name $resourceGroupName -Location $location
    
    # Create storage account
    New-AzureRmStorageAccount `
        -ResourceGroupName $resourceGroupName `
        -Name $defaultStorageAccountName `
        -Location $location `
        -Type Standard_GRS
    $defaultStorageAccountKey = (Get-AzureRmStorageAccountKey `
                                    -ResourceGroupName $resourceGroupName `
                                    -Name $defaultStorageAccountName)[0].Value
    $defaultStorageAccountContext = New-AzureStorageContext `
                                    -StorageAccountName $defaultStorageAccountName `
                                    -StorageAccountKey $storageAccountKey  
    New-AzureStorageContainer `
        -Name $defaultBlobContainerName `
        -Context $defaultStorageAccountContext
    
    #############################################################
    # Create cluster with ApacheSpark
    #############################################################
    
    # Specify the configuration options
    $config = New-AzureRmHDInsightClusterConfig `
                -DefaultStorageAccountName "$defaultStorageAccountName.blob.core.windows.net" `
                -DefaultStorageAccountKey $defaultStorageAccountKey 
                
    
    # Add a script action to the cluster configuration
    $config = Add-AzureRmHDInsightScriptAction `
                -Config $config `
                -Name "Install Spark" `
                -NodeType HeadNode `
                -Uri https://hdiconfigactions.blob.core.windows.net/sparkconfigactionv03/spark-installer-v03.ps1 `
    
    # Start creating a cluster with Spark installed
    New-AzureRmHDInsightCluster `
            -ResourceGroupName $resourceGroupName `
            -ClusterName $hdinsightClusterName `
            -Location $location `
            -ClusterSizeInNodes 2 `
            -ClusterType Hadoop `
            -OSType Windows `
            -DefaultStorageContainer $defaultBlobContainerName `
            -Config $config


Para instalar otro software, debe reemplazar el archivo de comandos en la secuencia de comandos:


Cuando se le pida, escriba las credenciales para el clúster. Puede tardar varios minutos antes de crear el clúster.

## <a name="call-scripts-using-net-sdk"></a>Secuencias de comandos de llamada mediante el SDK de .NET 

En el ejemplo siguiente se muestra cómo instalar el motor en clúster en función de HDInsight de Windows. Para instalar otro software, debe reemplazar el archivo de script en el código.

**Para crear un clúster de HDInsight con motor** 

1. Crear una aplicación de consola de C# en Visual Studio.
2. Desde la consola del Administrador de paquetes de Nuget, ejecute el siguiente comando.

        Install-Package Microsoft.Rest.ClientRuntime.Azure.Authentication -Pre
        Install-Package Microsoft.Azure.Management.ResourceManager -Pre
        Install-Package Microsoft.Azure.Management.HDInsight

2. Utilice las siguientes instrucciones using en el archivo Program.cs:

        using System;
        using System.Security;
        using Microsoft.Azure;
        using Microsoft.Azure.Management.HDInsight;
        using Microsoft.Azure.Management.HDInsight.Models;
        using Microsoft.Azure.Management.ResourceManager;
        using Microsoft.IdentityModel.Clients.ActiveDirectory;
        using Microsoft.Rest;
        using Microsoft.Rest.Azure.Authentication;

3. Coloque el código en la clase con los siguientes elementos:

        private static HDInsightManagementClient _hdiManagementClient;

        // Replace with your AAD tenant ID if necessary
        private const string TenantId = UserTokenProvider.CommonTenantId; 
        private const string SubscriptionId = "<Your Azure Subscription ID>";
        // This is the GUID for the PowerShell client. Used for interactive logins in this example.
        private const string ClientId = "1950a258-227b-4e31-a9cf-717495945fc2";
        private const string ResourceGroupName = "<ExistingAzureResourceGroupName>";
        private const string NewClusterName = "<NewAzureHDInsightClusterName>";
        private const int NewClusterNumWorkerNodes = 2;
        private const string NewClusterLocation = "East US";
        private const string NewClusterVersion = "3.2";
        private const string ExistingStorageName = "<ExistingAzureStorageAccountName>";
        private const string ExistingStorageKey = "<ExistingAzureStorageAccountKey>";
        private const string ExistingContainer = "<ExistingAzureBlobStorageContainer>";
        private const string NewClusterType = "Hadoop";
        private const OSType NewClusterOSType = OSType.Windows;
        private const string NewClusterUsername = "<HttpUserName>";
        private const string NewClusterPassword = "<HttpUserPassword>";

        static void Main(string[] args)
        {
            System.Console.WriteLine("Running");

            // Authenticate and get a token
            var authToken = Authenticate(TenantId, ClientId, SubscriptionId);
            // Flag subscription for HDInsight, if it isn't already.
            EnableHDInsight(authToken);
            // Get an HDInsight management client
            _hdiManagementClient = new HDInsightManagementClient(authToken);

            CreateCluster();
        }

        private static void CreateCluster()
        {
            var parameters = new ClusterCreateParameters
            {
                ClusterSizeInNodes = NewClusterNumWorkerNodes,
                Location = NewClusterLocation,
                ClusterType = NewClusterType,
                OSType = NewClusterOSType,
                Version = NewClusterVersion,

                DefaultStorageAccountName = ExistingStorageName,
                DefaultStorageAccountKey = ExistingStorageKey,
                DefaultStorageContainer = ExistingContainer,

                UserName = NewClusterUsername,
                Password = NewClusterPassword,
            };

            ScriptAction sparkScriptAction = new ScriptAction("Install Spark",
                new Uri("https://hdiconfigactions.blob.core.windows.net/sparkconfigactionv03/spark-installer-v03.ps1"), "");

            parameters.ScriptActions.Add(ClusterNodeType.HeadNode, new System.Collections.Generic.List<ScriptAction> { sparkScriptAction });
            parameters.ScriptActions.Add(ClusterNodeType.WorkerNode, new System.Collections.Generic.List<ScriptAction> { sparkScriptAction });

            _hdiManagementClient.Clusters.Create(ResourceGroupName, NewClusterName, parameters);
        }

        /// <summary>
        /// Authenticate to an Azure subscription and retrieve an authentication token
        /// </summary>
        /// <param name="TenantId">The AAD tenant ID</param>
        /// <param name="ClientId">The AAD client ID</param>
        /// <param name="SubscriptionId">The Azure subscription ID</param>
        /// <returns></returns>
        static TokenCloudCredentials Authenticate(string TenantId, string ClientId, string SubscriptionId)
        {
            var authContext = new AuthenticationContext("https://login.microsoftonline.com/" + TenantId);
            var tokenAuthResult = authContext.AcquireToken("https://management.core.windows.net/", 
                ClientId, 
                new Uri("urn:ietf:wg:oauth:2.0:oob"), 
                PromptBehavior.Always, 
                UserIdentifier.AnyUser);
            return new TokenCloudCredentials(SubscriptionId, tokenAuthResult.AccessToken);
        }
        /// <summary>
        /// Marks your subscription as one that can use HDInsight, if it has not already been marked as such.
        /// </summary>
        /// <remarks>This is essentially a one-time action; if you have already done something with HDInsight
        /// on your subscription, then this isn't needed at all and will do nothing.</remarks>
        /// <param name="authToken">An authentication token for your Azure subscription</param>
        static void EnableHDInsight(TokenCloudCredentials authToken)
        {
            // Create a client for the Resource manager and set the subscription ID
            var resourceManagementClient = new ResourceManagementClient(new TokenCredentials(authToken.Token));
            resourceManagementClient.SubscriptionId = SubscriptionId;
            // Register the HDInsight provider
            var rpResult = resourceManagementClient.Providers.Register("Microsoft.HDInsight");
        }


4. Presione **F5** para ejecutar la aplicación.


## <a name="support-for-open-source-software-used-on-hdinsight-clusters"></a>Compatibilidad con software de código abierto utilizado en clústeres HDInsight
El servicio de Microsoft Azure HDInsight es una plataforma flexible que le permite crear aplicaciones de datos grande en la nube mediante un ecosistema de tecnologías de código abierto formado por Hadoop. Microsoft Azure proporciona un nivel de compatibilidad general para las tecnologías de código abierto, como se describe en la sección **Ámbito de soporte técnico** del <a href="http://azure.microsoft.com/support/faq/" target="_blank">sitio Web de P+F de soporte técnico de Azure</a>. El servicio de HDInsight proporciona un nivel adicional de soporte técnico para algunos de los componentes, como se describe a continuación.

Existen dos tipos de componentes de código abierto que están disponibles en el servicio de HDInsight:

- **Componentes integrados** - estos componentes preinstalados en clústeres HDInsight y proporcionan la funcionalidad básica del clúster. Por ejemplo, hilo ResourceManager, el lenguaje de consulta de la sección (HiveQL) y la biblioteca de Mahout pertenecen a esta categoría. Una lista completa de los componentes de clúster está disponible en [Novedades en las versiones de clúster Hadoop proporcionadas por HDInsight?](hdinsight-component-versioning.md) </a>.
- **Componentes personalizados** :, como un usuario del clúster, puede instalar o usar en su carga de trabajo de cualquier componente disponible en la Comunidad o creado por el usuario.

Componentes integrados son totalmente compatibles y Microsoft Support le ayudará a aislar y resolver los problemas relacionados con estos componentes.

> [AZURE.WARNING] Componentes suministrados con el clúster HDInsight son totalmente compatibles y Microsoft Support le ayudará a aislar y resolver los problemas relacionados con estos componentes.
>
> Componentes personalizados reciban soporte comercialmente razonable para ayudar a solucionar el problema. Esto puede ocasionar resolver el problema o preguntar si desea integrarse canales disponibles para las tecnologías de código abierto donde se encuentra la amplia experiencia para que la tecnología. Por ejemplo, hay muchos sitios de comunidad que se pueden usar como: [foro de MSDN para HDInsight](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=hdinsight), [http://stackoverflow.com](http://stackoverflow.com). También proyectos Apache tienen sitios de proyecto en [http://apache.org](http://apache.org), por ejemplo: [Hadoop](http://hadoop.apache.org/), [motor](http://spark.apache.org/).

El servicio de HDInsight ofrece varias formas de usar componentes personalizados. Independientemente de cómo un componente utilizado o instalado en el clúster, se aplica el mismo nivel de compatibilidad. A continuación se muestra una lista de las formas más comunes que se pueden utilizar componentes personalizados en clústeres HDInsight:

1. Envío de trabajos - Hadoop u otros tipos de tareas que se ejecutan o utilizan componentes personalizados se puede enviar al clúster.
2. Personalización de clúster - durante la creación del clúster, puede especificar opciones adicionales y componentes personalizados que se instalarán en los nodos de clúster.
3. Ejemplos: para componentes personalizados populares, Microsoft y otros usuarios pueden proporcionar ejemplos de cómo se pueden utilizar estos componentes en los clústeres HDInsight. Estos ejemplos se proporcionan sin soporte técnico.

## <a name="develop-script-action-scripts"></a>Desarrollar secuencias de comandos de acción de secuencia de comandos

Vea [secuencias de comandos de acción de secuencia de comandos de desarrollar para HDInsight][hdinsight-write-script].


## <a name="see-also"></a>Vea también

- [Crear clústeres de Hadoop en HDInsight] [ hdinsight-provision-cluster] proporciona instrucciones sobre cómo crear un clúster de HDInsight con otras opciones personalizadas.
- [Desarrollar secuencias de comandos de acción de secuencia de comandos para HDInsight][hdinsight-write-script]
- [Instalar y usar el motor en clústeres HDInsight][hdinsight-install-spark]
- [Instalar y usar R en clústeres HDInsight][hdinsight-install-r]
- [Instalar y usar clústeres Solr en HDInsight](hdinsight-hadoop-solr-install.md).
- [Instalar y usar clústeres Giraph en HDInsight](hdinsight-hadoop-giraph-install.md).

[hdinsight-install-spark]: hdinsight-hadoop-spark-install.md
[hdinsight-install-r]: hdinsight-hadoop-r-scripts.md
[hdinsight-write-script]: hdinsight-hadoop-script-actions.md
[hdinsight-provision-cluster]: hdinsight-provision-clusters.md
[powershell-install-configure]: powershell-install-configure.md


[img-hdi-cluster-states]: ./media/hdinsight-hadoop-customize-cluster/HDI-Cluster-state.png "Fases durante la creación de clúster"
