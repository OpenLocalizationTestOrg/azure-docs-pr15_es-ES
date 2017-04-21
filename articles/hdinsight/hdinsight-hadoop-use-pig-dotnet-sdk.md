<properties
   pageTitle="Usar Hadoop cerdo con .NET en HDInsight | Microsoft Azure"
   description="Obtenga información sobre cómo usar el SDK de .NET para Hadoop enviar trabajos de cerdo a Hadoop en HDInsight."
   services="hdinsight"
   documentationCenter=".net"
   authors="Blackmist"
   manager="jhubbard"
   editor="cgronlun"
   tags="azure-portal"/>

<tags
   ms.service="hdinsight"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="10/17/2016"
   ms.author="larryfr"/>

#<a name="run-pig-jobs-using-the-net-sdk-for-hadoop-in-hdinsight"></a>Ejecutar trabajos de cerdo con .NET SDK para Hadoop en HDInsight

[AZURE.INCLUDE [pig-selector](../../includes/hdinsight-selector-use-pig.md)]

Este documento proporciona un ejemplo de uso de .NET SDK para Hadoop enviar trabajos de cerdo a un Hadoop en clúster HDInsight.

HDInsight .NET SDK proporciona bibliotecas de cliente .NET que hace que sea más fácil trabajar con clústeres HDInsight de .NET. Cerdo le permite crear operaciones MapReduce modelado una serie de transformaciones de datos. Aprenderá cómo usar una aplicación de C# básica para enviar un trabajo cerdo a un clúster de HDInsight.

## <a name="prerequisites"></a>Requisitos previos

Para completar los pasos de este artículo, necesita lo siguiente.

* Un clúster de Azure HDInsight (Hadoop en HDInsight) (Windows o Linux).
* Visual Studio 2012 o 2013 o 2015.

## <a name="create-the-application"></a>Creación de la aplicación

HDInsight .NET SDK proporciona bibliotecas de cliente .NET, lo que hace que sea más fácil trabajar con clústeres HDInsight de .NET. 


1. Abrir Visual Studio 2012 o 2013
2. En el menú **archivo** , seleccione **nuevo** y, a continuación, seleccione el **proyecto**.
3. El nuevo proyecto, escriba o seleccione los valores siguientes.

    <table>
    <tr>
    <th>(Propiedad)</th>
    <th>Valor</th>
    </tr>
    <tr>
    <th>Categoría</th>
    <th>Plantillas de Visual C# / Windows</th>
    </tr>
    <tr>
    <th>Plantilla</th>
    <th>Aplicación de consola</th>
    </tr>
    <tr>
    <th>Nombre</th>
    <th>SubmitPigJob</th>
    </tr>
    </table>
4. Haga clic en **Aceptar** para crear el proyecto.
5. Desde el menú **Herramientas** , seleccione **Administrador de paquetes de biblioteca** o el **Administrador de paquetes de Nuget**y, a continuación, seleccione la **Consola del Administrador de paquetes**.
6. Ejecute el comando siguiente en la consola para instalar los paquetes de .NET SDK.

        Install-Package Microsoft.Azure.Management.HDInsight.Job

7. Desde el Explorador de soluciones, haga doble clic en **Program.cs** para abrirlo. Reemplace el código existente con lo siguiente.

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

                    SubmitPigJob();

                    System.Console.WriteLine("Press ENTER to continue ...");
                    System.Console.ReadLine();
                }

                private static void SubmitPigJob()
                {
                    var parameters = new PigJobSubmissionParameters
                    {
                        Query = @"LOGS = LOAD 'wasbs:///example/data/sample.log';
                                    LEVELS = foreach LOGS generate REGEX_EXTRACT($0, '(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)', 1)  as LOGLEVEL;
                                    FILTEREDLEVELS = FILTER LEVELS by LOGLEVEL is not null;
                                    GROUPEDLEVELS = GROUP FILTEREDLEVELS by LOGLEVEL;
                                    FREQUENCIES = foreach GROUPEDLEVELS generate group as LOGLEVEL, COUNT(FILTEREDLEVELS.LOGLEVEL) as COUNT;
                                    RESULT = order FREQUENCIES by COUNT desc;
                                    DUMP RESULT;"
                    };

                    System.Console.WriteLine("Submitting the Pig job to the cluster...");
                    var response = _hdiJobManagementClient.JobManagement.SubmitPigJob(parameters);
                    System.Console.WriteLine("Validating that the response is as expected...");
                    System.Console.WriteLine("Response status code is " + response.StatusCode);
                    System.Console.WriteLine("Validating the response object...");
                    System.Console.WriteLine("JobId is " + response.JobSubmissionJsonResponse.Id);
                }
            }
        }


7. Presione **F5** para iniciar la aplicación.
8. Presione **ENTRAR** para salir de la aplicación.

## <a name="summary"></a>Resumen

Como puede ver, el SDK de .NET para Hadoop le permite crear aplicaciones .NET que envíen trabajos de cerdo a un clúster de HDInsight y supervisar el estado del trabajo.

## <a name="next-steps"></a>Pasos siguientes

Para obtener información general sobre cerdo en HDInsight.

* [Usar cerdo con Hadoop en HDInsight](hdinsight-use-pig.md)

Para obtener información sobre otras formas de puede trabajar con Hadoop en HDInsight.

* [Usar subárbol con Hadoop en HDInsight](hdinsight-use-hive.md)

* [Usar MapReduce con Hadoop en HDInsight](hdinsight-use-mapreduce.md) [vista previa portal]: https://portal.azure.com/
