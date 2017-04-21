<properties
    pageTitle="Ejecutar consultas de subárbol con HDInsight .NET SDK | Microsoft Azure"
    description="Obtenga información sobre cómo enviar trabajos de Hadoop a HDInsight Hadoop de Azure con HDInsight .NET SDK."
    editor="cgronlun"
    manager="jhubbard"
    services="hdinsight"
    documentationCenter=""
    tags="azure-portal"
    authors="mumian"/>

<tags
    ms.service="hdinsight"
    ms.workload="big-data"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
   ms.date="09/14/2016"
    ms.author="jgao"/>

# <a name="run-hive-queries-using-hdinsight-net-sdk"></a>Ejecutar consultas de subárbol con HDInsight .NET SDK

[AZURE.INCLUDE [hive-selector](../../includes/hdinsight-selector-use-hive.md)]


Obtenga información sobre cómo enviar consultas de subárbol con HDInsight .NET SDK.

> [AZURE.NOTE] Los pasos de este artículo deben realizarse desde un cliente de Windows. Para obtener información sobre cómo usar un Linux, OS X o cliente Unix para trabajar con la sección, use el selector de tabulaciones que se muestra en la parte superior del artículo.

##<a name="prerequisites"></a>Requisitos previos

Antes de comenzar este artículo, debe tener el siguiente:

- **Clúster A Hadoop en HDInsight**. Vea [Introducción al uso basados en Linux Hadoop en HDInsight](hdinsight-use-sqoop.md#create-cluster-and-sql-database).
- **Visual Studio 2012/2013/2015**.

##<a name="submit-hive-queries-using-hdinsight-net-sdk"></a>Enviar consultas de subárbol con HDInsight .NET SDK

HDInsight .NET SDK proporciona bibliotecas de cliente .NET, lo que hace que sea más fácil trabajar con clústeres HDInsight de .NET. 

**Enviar trabajos**

1. Crear una aplicación de consola de C# en Visual Studio.
2. Desde la consola del Administrador de paquetes de Nuget, ejecute el siguiente comando.

        Install-Package Microsoft.Azure.Management.HDInsight.Job

2. Use el siguiente código:

        using System.Collections.Generic;
        using System.IO;
        using System.Text;
        using System.Threading;
        using Microsoft.Azure.Management.HDInsight.Job;
        using Microsoft.Azure.Management.HDInsight.Job.Models;
        using Hyak.Common;

        namespace SubmitHDInsightJobDotNet
        {
            class Program
            {
                private static HDInsightJobManagementClient _hdiJobManagementClient;

                private const string ExistingClusterName = "<Your HDInsight Cluster Name>";
                private const string ExistingClusterUri = ExistingClusterName + ".azurehdinsight.net";
                private const string ExistingClusterUsername = "<Cluster Username>";
                private const string ExistingClusterPassword = "<Cluster User Password>";

                private const string DefaultStorageAccountName = "<Default Storage Account Name>";
                private const string DefaultStorageAccountKey = "<Default Storage Account Key>";
                private const string DefaultStorageContainerName = "<Default Blob Container Name>";

                static void Main(string[] args)
                {
                    System.Console.WriteLine("The application is running ...");

                    var clusterCredentials = new BasicAuthenticationCloudCredentials { Username = ExistingClusterUsername, Password = ExistingClusterPassword };
                    _hdiJobManagementClient = new HDInsightJobManagementClient(ExistingClusterUri, clusterCredentials);

                    SubmitHiveJob();

                    System.Console.WriteLine("Press ENTER to continue ...");
                    System.Console.ReadLine();
                }

                private static void SubmitHiveJob()
                {
                    Dictionary<string, string> defines = new Dictionary<string, string> { { "hive.execution.engine", "ravi" }, { "hive.exec.reducers.max", "1" } };
                    List<string> args = new List<string> { { "argA" }, { "argB" } };
                    var parameters = new HiveJobSubmissionParameters
                    {
                        Query = "SHOW TABLES",
                        Defines = defines,
                        Arguments = args
                    };

                    System.Console.WriteLine("Submitting the Hive job to the cluster...");
                    var jobResponse = _hdiJobManagementClient.JobManagement.SubmitHiveJob(parameters);
                    var jobId = jobResponse.JobSubmissionJsonResponse.Id;
                    System.Console.WriteLine("Response status code is " + jobResponse.StatusCode);
                    System.Console.WriteLine("JobId is " + jobId);

                    System.Console.WriteLine("Waiting for the job completion ...");

                    // Wait for job completion
                    var jobDetail = _hdiJobManagementClient.JobManagement.GetJob(jobId).JobDetail;
                    while (!jobDetail.Status.JobComplete)
                    {
                        Thread.Sleep(1000);
                        jobDetail = _hdiJobManagementClient.JobManagement.GetJob(jobId).JobDetail;
                    }

                    // Get job output
                    var storageAccess = new AzureStorageAccess(DefaultStorageAccountName, DefaultStorageAccountKey,
                        DefaultStorageContainerName);
                    var output = (jobDetail.ExitValue == 0)
                        ? _hdiJobManagementClient.JobManagement.GetJobOutput(jobId, storageAccess) // fetch stdout output in case of success
                        : _hdiJobManagementClient.JobManagement.GetJobErrorLogs(jobId, storageAccess); // fetch stderr output in case of failure

                    System.Console.WriteLine("Job output is: ");

                    using (var reader = new StreamReader(output, Encoding.UTF8))
                    {
                        string value = reader.ReadToEnd();
                        System.Console.WriteLine(value);
                    }
                }
            }
        }

5. Presione **F5** para ejecutar la aplicación.


## <a name="next-steps"></a>Pasos siguientes

En este artículo, ha aprendido diversas maneras para crear un clúster de HDInsight. Para obtener más información, consulte los artículos siguientes:

* [Introducción a HDInsight de Azure][hdinsight-get-started]
* [Crear clústeres de Hadoop en HDInsight][hdinsight-provision]
* [Administrar clústeres de Hadoop en HDInsight a través del Portal de Azure](hdinsight-administer-use-management-portal.md)
* [Referencia de HDInsight .NET SDK](https://msdn.microsoft.com/library/mt271028.aspx)
* [Usar cerdo con HDInsight](hdinsight-use-pig.md)
* [Usar Sqoop con HDInsight](hdinsight-use-sqoop-mac-linux.md)
* [Crear aplicaciones de .NET HDInsight de autenticación no interactivo](hdinsight-create-non-interactive-authentication-dotnet-applications.md)


[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-get-started]: hdinsight-hadoop-linux-tutorial-get-started.md


