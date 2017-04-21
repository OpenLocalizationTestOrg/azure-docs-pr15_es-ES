<properties
    pageTitle="Enviar trabajos MapReduce con HDInsight .NET SDK | Microsoft Azure"
    description="Obtenga información sobre cómo enviar trabajos de MapReduce a Azure HDInsight Hadoop con HDInsight .NET SDK."
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
   ms.date="10/28/2016"
    ms.author="jgao"/>

# <a name="run-mapreduce-jobs-using-hdinsight-net-sdk"></a>Ejecutar trabajos MapReduce con HDInsight .NET SDK

[AZURE.INCLUDE [mapreduce-selector](../../includes/hdinsight-selector-use-mapreduce.md)]

Aprenda a enviar trabajos MapReduce con HDInsight .NET SDK. HDInsight clústeres vienen con un archivo jar con algunos ejemplos de MapReduce. El archivo jar es */example/jars/hadoop-mapreduce-examples.jar*.  Una de las muestras es *wordcount*. Desarrollar una aplicación de consola de C# para enviar un trabajo wordcount.  El trabajo lee el archivo */example/data/gutenberg/davinci.txt* y guarda el resultado en */example/data/davinciwordcount*.  Si desea volver a ejecutar la aplicación, debe limpiar la carpeta de salida.

> [AZURE.NOTE] Los pasos de este artículo deben realizarse desde un cliente de Windows. Para obtener información sobre cómo usar un Linux, OS X o cliente Unix para trabajar con la sección, use el selector de tabulaciones que se muestra en la parte superior del artículo.

##<a name="prerequisites"></a>Requisitos previos

Antes de comenzar este artículo, debe tener el siguiente:

- **Clúster A Hadoop en HDInsight**. Vea [Introducción al uso basados en Linux Hadoop en HDInsight](hdinsight-use-sqoop.md#create-cluster-and-sql-database).
- **Visual Studio 2012/2013/2015**.

##<a name="submit-mapreduce-jobs-using-hdinsight-net-sdk"></a>Enviar trabajos MapReduce con HDInsight .NET SDK

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

                    SubmitMRJob();

                    System.Console.WriteLine("Press ENTER to continue ...");
                    System.Console.ReadLine();
                }

                private static void SubmitHiveJob()
                {
                    List<string> args = new List<string> { { "/example/data/gutenberg/davinci.txt" }, { "/example/data/davinciwordcount" } };

                    var paras = new MapReduceJobSubmissionParameters
                    {
                        //Content = "wordcount  ",
                        JarFile = @"/example/jars/hadoop-mapreduce-examples.jar",
                        JarClass = "wordcount",
                        Arguments = args
                    };

                    System.Console.WriteLine("Submitting the MR job to the cluster...");
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

- Para crear un clúster y enviar un trabajo de sección, vea [Introducción a HDInsight de Azure](hdinsight-hadoop-linux-tutorial-get-started.md).
- Para crear clústeres de HDInsight, consulte [clústeres basados en Linux crear Hadoop en HDInsight](hdinsight-hadoop-provision-linux-clusters.md).
- Para administrar clústeres HDInsight, consulte [Administrar Hadoop clústeres en HDInsight](hdinsight-administer-use-management-portal.md).
- Para aprender a usar el SDK de .NET HDInsight, vea [referencia de HDInsight .NET SDK](https://msdn.microsoft.com/library/mt271028.aspx).
- Para no interactivo autenticarse en Azure, vea [crear aplicaciones .NET HDInsight de autenticación no interactivo](hdinsight-create-non-interactive-authentication-dotnet-applications.md).




