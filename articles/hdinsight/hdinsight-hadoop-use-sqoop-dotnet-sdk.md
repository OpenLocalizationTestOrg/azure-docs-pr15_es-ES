<properties
    pageTitle="Usar Hadoop Sqoop en HDInsight | Microsoft Azure"
    description="Obtenga información sobre cómo usar HDInsight .NET SDK para ejecutar Sqoop importar y exportar entre un clúster de Hadoop y una base de datos de SQL Azure."
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

#<a name="run-sqoop-jobs-using-net-sdk-for-hadoop-in-hdinsight"></a>Ejecutar trabajos Sqoop con .NET SDK para Hadoop en HDInsight

[AZURE.INCLUDE [sqoop-selector](../../includes/hdinsight-selector-use-sqoop.md)]

Obtenga información sobre cómo usar HDInsight .NET SDK para ejecutar trabajos de Sqoop en HDInsight para importar y exportar entre HDInsight clúster y base de datos de SQL Azure o base de datos de SQL Server.

> [AZURE.NOTE] Los pasos de este artículo se pueden utilizar con cualquier un clúster basado en Windows o Linux de HDInsight; Sin embargo, estos pasos solo funcionará desde un cliente de Windows. Usar el selector de tabulaciones en la parte superior de este artículo para elegir otros métodos.

###<a name="prerequisites"></a>Requisitos previos

Antes de comenzar este tutorial, debe tener el siguiente:

- **Clúster A Hadoop en HDInsight**. Consulte [crear un clúster y base de datos SQL](hdinsight-use-sqoop.md#create-cluster-and-sql-database).

## <a name="run-sqoop-using-net-sdk"></a>Ejecutar Sqoop mediante el SDK de .NET

HDInsight .NET SDK proporciona bibliotecas de cliente .NET, lo que hace que sea más fácil trabajar con clústeres HDInsight de .NET. En esta sección, creará una aplicación de consola de C# para exportar la hivesampletable a la tabla de base de datos SQL que creó anteriormente en esta tutoriales.

**Para enviar un trabajo Sqoop**

1. Crear una aplicación de consola de C# en Visual Studio.
2. Desde la consola del Administrador de paquetes de Visual Studio, ejecute el siguiente comando Nuget para importar el paquete.

        Install-Package Microsoft.Azure.Management.HDInsight.Job
        
3. Use el siguiente código en el archivo Program.cs:

        using System.Collections.Generic;
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
        
                static void Main(string[] args)
                {
                    System.Console.WriteLine("The application is running ...");
        
                    var clusterCredentials = new BasicAuthenticationCloudCredentials { Username = ExistingClusterUsername, Password = ExistingClusterPassword };
                    _hdiJobManagementClient = new HDInsightJobManagementClient(ExistingClusterUri, clusterCredentials);
        
                    SubmitSqoopJob();
        
                    System.Console.WriteLine("Press ENTER to continue ...");
                    System.Console.ReadLine();
                }
        
                private static void SubmitSqoopJob()
                {
                    var sqlDatabaseServerName = "<SQLDatabaseServerName>";
                    var sqlDatabaseLogin = "<SQLDatabaseLogin>";
                    var sqlDatabaseLoginPassword = "<SQLDatabaseLoginPassword>";
                    var sqlDatabaseDatabaseName = "<DatabaseName>";
        
                    var tableName = "<TableName>";
                    var exportDir = "/tutorials/usesqoop/data";
        
                    // Connection string for using Azure SQL Database.
                    // Comment if using SQL Server
                    var connectionString = "jdbc:sqlserver://" + sqlDatabaseServerName + ".database.windows.net;user=" + sqlDatabaseLogin + "@" + sqlDatabaseServerName + ";password=" + sqlDatabaseLoginPassword + ";database=" + sqlDatabaseDatabaseName;
                    // Connection string for using SQL Server.
                    // Uncomment if using SQL Server
                    //var connectionString = "jdbc:sqlserver://" + sqlDatabaseServerName + ";user=" + sqlDatabaseLogin + ";password=" + sqlDatabaseLoginPassword + ";database=" + sqlDatabaseDatabaseName;
        
                    var parameters = new SqoopJobSubmissionParameters
                    {
                        Files = new List<string> { "/user/oozie/share/lib/sqoop/sqljdbc41.jar" }, // This line is required for Linux-based cluster.
                        Command = "export --connect " + connectionString + " --table " + tableName + "_mobile --export-dir " + exportDir + "_mobile --fields-terminated-by \\t -m 1"
                    };
        
                    System.Console.WriteLine("Submitting the Sqoop job to the cluster...");
                    var response = _hdiJobManagementClient.JobManagement.SubmitSqoopJob(parameters);
                    System.Console.WriteLine("Validating that the response is as expected...");
                    System.Console.WriteLine("Response status code is " + response.StatusCode);
                    System.Console.WriteLine("Validating the response object...");
                    System.Console.WriteLine("JobId is " + response.JobSubmissionJsonResponse.Id);
                }
            }
        }
        
4. Presione **F5** para ejecutar el programa. 

##<a name="limitations"></a>Limitaciones

* Exportación de masa - HDInsight de con Linux, el conector de Sqoop utilizado para exportar datos a Microsoft SQL Server o base de datos de SQL Azure no es compatible con las inserciones masivas.

* Procesamiento por lotes - con HDInsight basados en Linux, cuando se usa el `-batch` cambia cuando se realizan inserciones, Sqoop realizará varias inserciones en lugar de por lotes las operaciones de inserción.

##<a name="next-steps"></a>Pasos siguientes

Ahora ha aprendido a usar Sqoop. Para obtener más información, consulte:

- [Usar Oozie con HDInsight](hdinsight-use-oozie.md): Sqoop de usar una acción en un flujo de trabajo Oozie.
- [Analizar datos de retrasos de vuelos con HDInsight](hdinsight-analyze-flight-delay-data.md): usar subárbol para analizar vuelo retrasar datos y use Sqoop para exportar datos a una base de datos de SQL Azure.
- [Cargar datos a HDInsight](hdinsight-upload-data.md): buscar otros métodos para cargar los datos con el almacenamiento de blobs de Windows Azure o HDInsight.


