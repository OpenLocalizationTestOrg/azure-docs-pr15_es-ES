<properties
   pageTitle="Use MapReduce y PowerShell con Hadoop | Microsoft Azure"
   description="Obtenga información sobre cómo usar PowerShell para ejecutar remotamente MapReduce trabajos con Hadoop en HDInsight."
   services="hdinsight"
   documentationCenter=""
   authors="Blackmist"
   manager="jhubbard"
   editor="cgronlun"
    tags="azure-portal"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="08/29/2016"
   ms.author="larryfr"/>

# <a name="run-mapreduce-jobs-with-hadoop-on-hdinsight-using-powershell"></a>Ejecutar trabajos MapReduce con Hadoop en HDInsight con PowerShell

[AZURE.INCLUDE [mapreduce-selector](../../includes/hdinsight-selector-use-mapreduce.md)]

Este documento proporciona un ejemplo de uso de PowerShell de Azure para ejecutar un trabajo MapReduce en un Hadoop en clúster de HDInsight.

##<a id="prereq"></a>Requisitos previos

Para completar los pasos de este artículo, necesita lo siguiente:

- **Un clúster de Azure HDInsight (Hadoop en HDInsight) (basado en Windows o en Linux)**

- **Una estación de trabajo con PowerShell de Azure**.

    [AZURE.INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]

##<a id="powershell"></a>Ejecutar un trabajo MapReduce con PowerShell de Azure

Azure PowerShell ofrece *cmdlets* que le permiten ejecutar remotamente MapReduce trabajos en HDInsight. Internamente, esto se logra mediante llamadas del resto a [WebHCat](https://cwiki.apache.org/confluence/display/Hive/WebHCat) (anteriormente llamado Templeton) ejecutan en el clúster HDInsight.

Los siguientes cmdlets se usan cuando se ejecuta MapReduce trabajos en un clúster de HDInsight remoto.

* **Inicio de sesión AzureRmAccount**: autentica Azure PowerShell a su suscripción de Azure

* **Nuevo AzureRmHDInsightMapReduceJobDefinition**: crea una nueva *definición de trabajo* con la información de MapReduce especificada

* **Inicio AzureRmHDInsightJob**: la definición de trabajo se envía a HDInsight, el trabajo se inicia y devuelve un objeto de *trabajo* que se pueden usar para comprobar el estado del trabajo

* **Espera AzureRmHDInsightJob**: utiliza el objeto de trabajo para comprobar el estado del trabajo. Espera hasta que finalice el trabajo o se superó el tiempo de espera.

* **Get-AzureRmHDInsightJobOutput**: usado para recuperar el resultado de la tarea

Los pasos siguientes muestran cómo usar estos cmdlets para ejecutar un trabajo en el clúster HDInsight.

1. Usar un editor, guarde el código siguiente como **mapreducejob.ps1**. Debe reemplazarse **NOMBREDECLÚSTER** con el nombre de su clúster HDInsight.

        #Specify the values
        $clusterName = "CLUSTERNAME"
                
        # Login to your Azure subscription
        # Is there an active Azure subscription?
        $sub = Get-AzureRmSubscription -ErrorAction SilentlyContinue
        if(-not($sub))
        {
            Login-AzureRmAccount
        }

        #Get HTTPS/Admin credentials for submitting the job later
        $creds = Get-Credential
        #Get the cluster info so we can get the resource group, storage, etc.
        $clusterInfo = Get-AzureRmHDInsightCluster -ClusterName $clusterName
        $resourceGroup = $clusterInfo.ResourceGroup
        $storageAccountName=$clusterInfo.DefaultStorageAccount.split('.')[0]
        $container=$clusterInfo.DefaultStorageContainer
        $storageAccountKey=(Get-AzureRmStorageAccountKey `
            -Name $storageAccountName `
        -ResourceGroupName $resourceGroup)[0].Value

        #Create a storage content and upload the file
        $context = New-AzureStorageContext `
            -StorageAccountName $storageAccountName `
            -StorageAccountKey $storageAccountKey
            
        #Define the MapReduce job
        #NOTE: If using an HDInsight 2.0 cluster, use hadoop-examples.jar instead.
        # -JarFile = the JAR containing the MapReduce application
        # -ClassName = the class of the application
        # -Arguments = The input file, and the output directory
        $wordCountJobDefinition = New-AzureRmHDInsightMapReduceJobDefinition `
            -JarFile "wasbs:///example/jars/hadoop-mapreduce-examples.jar" `
            -ClassName "wordcount" `
            -Arguments `
                "wasbs:///example/data/gutenberg/davinci.txt", `
                "wasbs:///example/data/WordCountOutput"

        #Submit the job to the cluster
        Write-Host "Start the MapReduce job..." -ForegroundColor Green
        $wordCountJob = Start-AzureRmHDInsightJob `
            -ClusterName $clusterName `
            -JobDefinition $wordCountJobDefinition `
            -HttpCredential $creds

        #Wait for the job to complete
        Write-Host "Wait for the job to complete..." -ForegroundColor Green
        Wait-AzureRmHDInsightJob `
            -ClusterName $clusterName `
            -JobId $wordCountJob.JobId `
            -HttpCredential $creds
        # Download the output
        Get-AzureStorageBlobContent `
            -Blob 'example/data/WordCountOutput/part-r-00000' `
            -Container $container `
            -Destination output.txt `
            -Context $context
        # Print the output
        Get-AzureRmHDInsightJobOutput `
            -Clustername $clusterName `
            -JobId $wordCountJob.JobId `
            -DefaultContainer $container `
            -DefaultStorageAccountName $storageAccountName `
            -DefaultStorageAccountKey $storageAccountKey `
            -HttpCredential $creds
            
2. Abra un nuevo símbolo de **PowerShell de Azure** . Cambie los directorios a la ubicación del archivo **mapreducejob.ps1** y luego use el comando siguiente para ejecutar la secuencia de comandos:

        .\mapreducejob.ps1
    
    Cuando se ejecuta la secuencia de comandos, es podrán que deba autenticar a su suscripción de Azure. Le pedirá que proporcione el nombre de la cuenta de administrador/HTTPS y la contraseña para el clúster de HDInsight.

3. Cuando finalice el trabajo, obtendrá un resultado similar al siguiente:

        Cluster         : CLUSTERNAME
        ExitCode        : 0
        Name            : wordcount
        PercentComplete : map 100% reduce 100%
        Query           :
        State           : Completed
        StatusDirectory : f1ed2028-afe8-402f-a24b-13cc17858097
        SubmissionTime  : 12/5/2014 8:34:09 PM
        JobId           : job_1415949758166_0071

    Este resultado indica que la tarea se completó correctamente.

    > [AZURE.NOTE] Si el **código** es un valor distinto de 0, vea [solución de problemas](#troubleshooting).

    En este ejemplo se guardará también los archivos descargados a un archivo **output.txt** en el directorio que se ejecuta la secuencia de comandos.

###<a name="view-output"></a>Resultados de la vista

Abra el archivo **output.txt** en un editor de texto para ver las palabras y recuentos producidos por el trabajo.

> [AZURE.NOTE] Los archivos de resultados de un trabajo MapReduce son inmutables. Así que si se vuelva a ejecutar este ejemplo, debe cambiar el nombre del archivo de salida.

##<a id="troubleshooting"></a>Solución de problemas

Si no se devuelve información cuando finalice el trabajo, puede ha ocurrido un error durante el proceso. Para ver información de error para esta tarea, agregue el comando siguiente al final del archivo **mapreducejob.ps1** , guárdelo y ejecute de nuevo.

    # Print the output of the WordCount job.
    Write-Host "Display the standard output ..." -ForegroundColor Green
    Get-AzureRmHDInsightJobOutput `
            -Clustername $clusterName `
            -JobId $wordCountJob.JobId `
            -DefaultContainer $container `
            -DefaultStorageAccountName $storageAccountName `
            -DefaultStorageAccountKey $storageAccountKey `
            -HttpCredential $creds `
            -DisplayOutputType StandardError

Devuelve la información que escribió en STDERR en el servidor al ejecutar la tarea y, a continuación, puede ayudar a determinar por qué no funciona el trabajo.

##<a id="summary"></a>Resumen

Como puede ver, Azure PowerShell proporciona una forma sencilla de ejecutar trabajos MapReduce en un clúster de HDInsight, supervisar el estado del trabajo y recuperar el resultado.

##<a id="nextsteps"></a>Pasos siguientes

Para obtener información general acerca de los trabajos de MapReduce en HDInsight:

* [Usar MapReduce en HDInsight Hadoop](hdinsight-use-mapreduce.md)

Para obtener información sobre otras formas de puede trabajar con Hadoop en HDInsight:

* [Usar subárbol con Hadoop en HDInsight](hdinsight-use-hive.md)

* [Usar cerdo con Hadoop en HDInsight](hdinsight-use-pig.md)
