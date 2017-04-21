<properties
    pageTitle="Administrar clústeres Hadoop en HDInsight con .NET SDK | Microsoft Azure"
    description="Obtenga información sobre cómo realizar tareas administrativas para los clústeres Hadoop HDInsight con HDInsight .NET SDK."
    services="hdinsight"
    editor="cgronlun"
    manager="jhubbard"
    tags="azure-portal"
    authors="mumian"
    documentationCenter=""/>

<tags
    ms.service="hdinsight"
    ms.workload="big-data"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/02/2016"
    ms.author="jgao"/>

# <a name="manage-hadoop-clusters-in-hdinsight-by-using-net-sdk"></a>Administrar grupos de Hadoop de HDInsight mediante .NET SDK

[AZURE.INCLUDE [selector](../../includes/hdinsight-portal-management-selector.md)]

Obtenga información sobre cómo administrar clústeres HDInsight con [HDInsight.NET SDK](https://msdn.microsoft.com/library/mt271028.aspx).


**Requisitos previos**

Antes de comenzar este artículo, debe tener el siguiente:

- **Suscripción de un Azure**. Vea la [versión de prueba gratuita de Azure obtener](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).


##<a name="connect-to-azure-hdinsight"></a>Conectarse a HDInsight de Azure

Necesitará los paquetes de Nuget siguientes:

    Install-Package Microsoft.Rest.ClientRuntime.Azure.Authentication -Pre
    Install-Package Microsoft.Azure.Management.ResourceManager -Pre
    Install-Package Microsoft.Azure.Management.HDInsight

El ejemplo siguiente muestra cómo conectarse a Azure para poder administrar clústeres HDInsight en su suscripción de Azure.

    using System;
    using Microsoft.Azure;
    using Microsoft.Azure.Management.HDInsight;
    using Microsoft.Azure.Management.HDInsight.Models;
    using Microsoft.Azure.Management.ResourceManager;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    using Microsoft.Rest;
    using Microsoft.Rest.Azure.Authentication;

    namespace HDInsightManagement
    {
        class Program
        {
            private static HDInsightManagementClient _hdiManagementClient;
            // Replace with your AAD tenant ID if necessary
            private const string TenantId = UserTokenProvider.CommonTenantId; 
            private const string SubscriptionId = "<Your Azure Subscription ID>";
            // This is the GUID for the PowerShell client. Used for interactive logins in this example.
            private const string ClientId = "1950a258-227b-4e31-a9cf-717495945fc2";

            static void Main(string[] args)
            {
                // Authenticate and get a token
                var authToken = Authenticate(TenantId, ClientId, SubscriptionId);
                // Flag subscription for HDInsight, if it isn't already.
                EnableHDInsight(authToken);
                // Get an HDInsight management client
                _hdiManagementClient = new HDInsightManagementClient(authToken);

                // insert code here

                System.Console.WriteLine("Press ENTER to continue");
                System.Console.ReadLine();
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
        }
    }

Se verá un mensaje al ejecutar este programa.  Si no desea ver el símbolo del sistema, consulte [aplicaciones de .NET HDInsight de autenticación no interactiva de crear](hdinsight-create-non-interactive-authentication-dotnet-applications.md).

##<a name="create-clusters"></a>Crear clústeres

Consulte [basados en Linux crear clústeres en HDInsight mediante el SDK de .NET](hdinsight-hadoop-create-linux-clusters-dotnet-sdk.md)

##<a name="list-clusters"></a>Lista clústeres

Fragmento de código siguiente enumera clústeres y algunas propiedades:

    var results = _hdiManagementClient.Clusters.List();
    foreach (var name in results.Clusters) {
        Console.WriteLine("Cluster Name: " + name.Name);
        Console.WriteLine("\t Cluster type: " + name.Properties.ClusterDefinition.ClusterType);
        Console.WriteLine("\t Cluster location: " + name.Location);
        Console.WriteLine("\t Cluster version: " + name.Properties.ClusterVersion);
    }

##<a name="delete-clusters"></a>Eliminar clústeres

Use el fragmento de código siguiente para eliminar un clúster de forma sincrónica o asincrónica: 

    _hdiManagementClient.Clusters.Delete("<Resource Group Name>", "<Cluster Name>");
    _hdiManagementClient.Clusters.DeleteAsync("<Resource Group Name>", "<Cluster Name>");
            
##<a name="scale-clusters"></a>Clústeres de escala
La característica de ajuste de escala de clúster le permite cambiar el número de nodos de trabajo utilizados por un clúster que se ejecuta en Azure HDInsight sin tener que volver a crear el clúster.

>[AZURE.NOTE] Solo clústeres con HDInsight versión 3.1.3 o superior son compatibles. Si no está seguro de la versión de su clúster, puede comprobar la página de propiedades.  Consulte [clústeres de lista y mostrar](hdinsight-administer-use-portal-linux.md#list-and-show-clusters).

El impacto de cambiar el número de nodos de datos para cada tipo de clúster compatible con HDInsight:

- Hadoop

    Sin problemas, puede aumentar el número de nodos de trabajo en un clúster de Hadoop que se está ejecutando sin afectar a los trabajos pendientes o se está ejecutando. También se pueden enviar trabajos de nuevos mientras la operación está en curso. Errores en una operación de escalado se administran correctamente para que el clúster siempre se quede en un estado funcional.

    Cuando se cambia el tamaño de un clúster de Hadoop hacia abajo al reducir el número de nodos de datos, algunos de los servicios en el clúster se reinician. Esto hace todas ellas y trabajos pendientes errores al final de la operación de rotación. Sin embargo, puede volver a enviar las tareas una vez completada la operación.

- HBase

    Sin problemas, puede agregar o quitar nodos en el clúster HBase mientras se está ejecutando. Servidores regionales están equilibrados automáticamente en unos minutos de completar la operación de rotación. Sin embargo, puede ajustar manualmente los servidores regionales, inicie sesión en la headnode de clúster y ejecute los comandos siguientes desde una ventana de símbolo del sistema:

        >pushd %HBASE_HOME%\bin
        >hbase shell
        >balancer

- Tormenta

    Sin problemas, puede agregar o quitar nodos de datos en el clúster de tormenta mientras se está ejecutando. Pero después de la finalización correcta de la operación de escalado, debe equilibrar la topología.

    Volver a equilibrar puede realizarse de dos maneras:

    * Interfaz de usuario de web de tormenta
    * Herramienta de línea de comandos de interfaz

    Consulte la [documentación de tormenta Apache](http://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html) para obtener más detalles.

    La interfaz de usuario de web tormenta está disponible en el clúster HDInsight:

    ![equilibrar de escala de tormenta hdinsight](./media/hdinsight-administer-use-management-portal/hdinsight.portal.scale.cluster.storm.rebalance.png)

    Mostramos un ejemplo de cómo usar el comando CLI a equilibrar la topología de tormenta:

        ## Reconfigure the topology "mytopology" to use 5 worker processes,
        ## the spout "blue-spout" to use 3 executors, and
        ## the bolt "yellow-bolt" to use 10 executors

        $ storm rebalance mytopology -n 5 -e blue-spout=3 -e yellow-bolt=10

Fragmento de código siguiente muestra cómo cambiar el tamaño de un clúster de forma sincrónica o asincrónica:

    _hdiManagementClient.Clusters.Resize("<Resource Group Name>", "<Cluster Name>", <New Size>);   
    _hdiManagementClient.Clusters.ResizeAsync("<Resource Group Name>", "<Cluster Name>", <New Size>);   
    

##<a name="grantrevoke-access"></a>Conceder o denegar el acceso a

HDInsight clústeres tienen los siguientes servicios de web HTTP (todos estos servicios tienen extremos REST):

- ODBC
- JDBC
- Ambari
- Oozie
- Templeton


De forma predeterminada, estos servicios se le ha concedido acceso. Puede revocar o conceder el acceso. Para revocar:

    var httpParams = new HttpSettingsParameters
    {
        HttpUserEnabled = false,
        HttpUsername = "admin",
        HttpPassword = "*******",
    };
    _hdiManagementClient.Clusters.ConfigureHttpSettings("<Resource Group Name>, <Cluster Name>, httpParams);

Para conceder:

    var httpParams = new HttpSettingsParameters
    {
        HttpUserEnabled = enable,
        HttpUsername = "admin",
        HttpPassword = "*******",
    };
    _hdiManagementClient.Clusters.ConfigureHttpSettings("<Resource Group Name>, <Cluster Name>, httpParams);


>[AZURE.NOTE] Conceder y revocar el acceso, se restablece el clúster nombre de usuario y contraseña.

También puede hacerse a través del Portal. Consulte [Administrar HDInsight mediante el Portal de Azure][hdinsight-admin-portal].

##<a name="update-http-user-credentials"></a>Actualizar credenciales de usuario HTTP

Es el mismo procedimiento que [conceder o revocar HTTP access](#grant/revoke-access). Si el clúster se le ha concedido el acceso HTTP, debe revocar primero.  Y, a continuación, conceda acceso con credenciales de usuario HTTP nuevas.


##<a name="find-the-default-storage-account"></a>Buscar la cuenta de almacenamiento predeterminada

Fragmento de código siguiente muestra cómo obtener el nombre de la cuenta de almacenamiento predeterminada y la clave de cuenta de almacenamiento predeterminada para un clúster.

    var results = _hdiManagementClient.Clusters.GetClusterConfigurations(<Resource Group Name>, <Cluster Name>, "core-site");
    foreach (var key in results.Configuration.Keys)
    {
        Console.WriteLine(String.Format("{0} => {1}", key, results.Configuration[key]));
    }


##<a name="submit-jobs"></a>Enviar trabajos

**Enviar trabajos MapReduce**

Vea [ejemplos de ejecutar Hadoop MapReduce en HDInsight](hdinsight-hadoop-run-samples-linux.md).

**Enviar trabajos de sección** 

Consulte [Ejecutar subárbol consultas con .NET SDK](hdinsight-hadoop-use-hive-dotnet-sdk.md).

**Enviar trabajos de cerdo**

Vea [trabajos de ejecutar cerdo con .NET SDK](hdinsight-hadoop-use-pig-dotnet-sdk.md).

**Enviar trabajos Sqoop**

Consulte [usar Sqoop con HDInsight](hdinsight-hadoop-use-sqoop-dotnet-sdk.md).

**Enviar trabajos Oozie**

Consulte [Usar Oozie con Hadoop para definir y ejecutar un flujo de trabajo en HDInsight](hdinsight-use-oozie-linux-mac.md).

##<a name="upload-data-to-azure-blob-storage"></a>Cargar datos con el almacenamiento de blobs de Windows Azure
Vea [cargar datos a HDInsight][hdinsight-upload-data].


## <a name="see-also"></a>Vea también
* [Documentación de referencia de HDInsight .NET SDK](https://msdn.microsoft.com/library/mt271028.aspx)
* [Administrar HDInsight mediante el Portal de Azure][hdinsight-admin-portal]
* [Administrar HDInsight mediante una interfaz de línea de comandos][hdinsight-admin-cli]
* [Crear clústeres HDInsight][hdinsight-provision]
* [Cargar datos a HDInsight][hdinsight-upload-data]
* [Introducción a HDInsight de Azure][hdinsight-get-started]


[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[hdinsight-get-started]: hdinsight-hadoop-linux-tutorial-get-started.md
[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-provision-custom-options]: hdinsight-provision-clusters.md#configuration
[hdinsight-submit-jobs]: hdinsight-submit-hadoop-jobs-programmatically.md

[hdinsight-admin-cli]: hdinsight-administer-use-command-line.md
[hdinsight-admin-portal]: hdinsight-administer-use-portal-linux.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md
[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-mapreduce]: hdinsight-use-mapreduce.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-flight]: hdinsight-analyze-flight-delay-data.md


