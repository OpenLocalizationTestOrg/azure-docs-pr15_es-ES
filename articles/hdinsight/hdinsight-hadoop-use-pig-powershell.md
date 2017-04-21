<properties
   pageTitle="Usar Hadoop cerdo con PowerShell en HDInsight | Microsoft Azure"
   description="Obtenga información sobre cómo enviar trabajos de cerdo a un clúster de Hadoop en HDInsight con PowerShell de Azure."
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
   ms.date="10/11/2016"
   ms.author="larryfr"/>

#<a name="run-pig-jobs-using-powershell"></a>Ejecutar trabajos de cerdo con PowerShell

[AZURE.INCLUDE [pig-selector](../../includes/hdinsight-selector-use-pig.md)]

Este documento proporciona un ejemplo de uso de PowerShell de Azure enviar trabajos de cerdo a un Hadoop en clúster HDInsight. Cerdo le permite escribir MapReduce trabajos mediante un idioma (latino de cerdo), que modelos transformaciones de datos, en lugar de asignar y reducir las funciones.

> [AZURE.NOTE] Este documento no proporciona una descripción detallada de lo que hacer las instrucciones de cerdo latino utilizadas en los ejemplos. Para obtener información sobre el alfabeto latino cerdo usados en este ejemplo, vea [Usar cerdo con Hadoop en HDInsight](hdinsight-use-pig.md).

##<a id="prereq"></a>Requisitos previos

Para completar los pasos de este artículo, necesita lo siguiente.

- **Suscripción de un Azure**. Vea la [versión de prueba gratuita de Azure obtener](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
- **Una estación de trabajo con PowerShell de Azure**.

    [AZURE.INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]


##<a id="powershell"></a>Ejecutar trabajos de cerdo con PowerShell

Azure PowerShell ofrece *cmdlets* que le permiten ejecutar remotamente trabajos de cerdo en HDInsight. Internamente, esto se logra mediante llamadas del resto a [WebHCat](https://cwiki.apache.org/confluence/display/Hive/WebHCat) (anteriormente llamado Templeton) ejecutan en el clúster HDInsight.

Cuando se ejecutan trabajos de cerdo en un clúster de HDInsight remoto, se usan los siguientes cmdlets:

* **Inicio de sesión AzureRmAccount**: autentica PowerShell Azure a su suscripción de Azure

* **Nuevo AzureRmHDInsightPigJobDefinition**: crea una nueva *definición de trabajo* mediante las instrucciones de cerdo latino especificadas

* **Inicio AzureRmHDInsightJob**: la definición de trabajo se envía a HDInsight, el trabajo se inicia y devuelve un objeto de *trabajo* que se pueden usar para comprobar el estado del trabajo

* **Espera AzureRmHDInsightJob**: utiliza el objeto de trabajo para comprobar el estado del trabajo. Esperará hasta que haya finalizado el trabajo o ha superado el tiempo de espera.

* **Get-AzureRmHDInsightJobOutput**: usado para recuperar el resultado de la tarea

Los pasos siguientes muestran cómo usar estos cmdlets para ejecutar un trabajo en el clúster HDInsight.

1. Usar un editor, guarde el código siguiente como **pigjob.ps1**. Debe reemplazarse **NOMBREDECLÚSTER** con el nombre de su clúster HDInsight.

        #Login to your Azure subscription
        Login-AzureRmAccount
        #Get credentials for the admin/HTTPs account
        $creds = Get-Credential

        #Specify the cluster name
        $clusterName = "CLUSTERNAME"
        
        #Get the cluster info so we can get the resource group, storage, etc.
        $clusterInfo = Get-AzureRmHDInsightCluster -ClusterName $clusterName
        $resourceGroup = $clusterInfo.ResourceGroup
        $storageAccountName = $clusterInfo.DefaultStorageAccount.split('.')[0]
        $container = $clusterInfo.DefaultStorageContainer
        $storageAccountKey = (Get-AzureRmStorageAccountKey `
            -Name $storageAccountName `
        -ResourceGroupName $resourceGroup)[0].Value

        #Store the Pig Latin into $QueryString
        $QueryString =  "LOGS = LOAD 'wasbs:///example/data/sample.log';" +
        "LEVELS = foreach LOGS generate REGEX_EXTRACT(`$0, '(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)', 1)  as LOGLEVEL;" +
        "FILTEREDLEVELS = FILTER LEVELS by LOGLEVEL is not null;" +
        "GROUPEDLEVELS = GROUP FILTEREDLEVELS by LOGLEVEL;" +
        "FREQUENCIES = foreach GROUPEDLEVELS generate group as LOGLEVEL, COUNT(FILTEREDLEVELS.LOGLEVEL) as COUNT;" +
        "RESULT = order FREQUENCIES by COUNT desc;" +
        "DUMP RESULT;"


        #Create a new HDInsight Pig Job definition
        $pigJobDefinition = New-AzureRmHDInsightPigJobDefinition `
            -Query $QueryString `
            -Arguments "-w"

        # Start the Pig job on the HDInsight cluster
        Write-Host "Start the Pig job ..." -ForegroundColor Green
        $pigJob = Start-AzureRmHDInsightJob `
            -ClusterName $clusterName `
            -JobDefinition $pigJobDefinition `
            -HttpCredential $creds

        # Wait for the Pig job to complete
        Write-Host "Wait for the Pig job to complete ..." -ForegroundColor Green
        Wait-AzureRmHDInsightJob `
            -ClusterName $clusterName `
            -JobId $pigJob.JobId `
            -HttpCredential $creds

        # Display the output of the Pig job.
        Write-Host "Display the standard output ..." -ForegroundColor Green
        Get-AzureRmHDInsightJobOutput `
            -ClusterName $clusterName `
            -JobId $pigJob.JobId `
            -DefaultContainer $container `
            -DefaultStorageAccountName $storageAccountName `
            -DefaultStorageAccountKey $storageAccountKey `
            -HttpCredential $creds

2. Abra un nuevo símbolo de Windows PowerShell. Cambie los directorios a la ubicación del archivo **pigjob.ps1** y luego use el comando siguiente para ejecutar la secuencia de comandos:

        .\pigjob.ps1
        
    Se le pedirá primero iniciar sesión en su suscripción de Azure. A continuación, se le pedirá el nombre de la cuenta de administrador/HTTPs y la contraseña para el clúster de HDInsight.

7. Cuando finalice el trabajo, se debe devolver información similar al siguiente:

        Start the Pig job ...
        Wait for the Pig job to complete ...
        Display the standard output ...
        (TRACE,816)
        (DEBUG,434)
        (INFO,96)
        (WARN,11)
        (ERROR,6)
        (FATAL,2)

##<a id="troubleshooting"></a>Solución de problemas

Si no se devuelve información cuando finalice el trabajo, puede ha ocurrido un error durante el proceso. Para ver información de error para esta tarea, agregue el comando siguiente al final del archivo **pigjob.ps1** , guárdelo y ejecute de nuevo.

    # Print the output of the Pig job.
    Write-Host "Display the standard error output ..." -ForegroundColor Green
    Get-AzureRmHDInsightJobOutput `
            -Clustername $clusterName `
            -JobId $pigJob.JobId `
            -DefaultContainer $container `
            -DefaultStorageAccountName $storageAccountName `
            -DefaultStorageAccountKey $storageAccountKey `
            -HttpCredential $creds `
            -DisplayOutputType StandardError

Esto devolverá la información que escribió en STDERR en el servidor cuando ejecutó el trabajo y puede ayudar a determinar por qué no funciona el trabajo.

##<a id="summary"></a>Resumen

Como puede ver, Azure PowerShell proporciona una forma sencilla de ejecutar trabajos de cerdo en un clúster de HDInsight, supervisar el estado del trabajo y recuperar el resultado.

##<a id="nextsteps"></a>Pasos siguientes

Para obtener información general sobre cerdo en HDInsight:

* [Usar cerdo con Hadoop en HDInsight](hdinsight-use-pig.md)

Para obtener información sobre otras formas de puede trabajar con Hadoop en HDInsight:

* [Usar subárbol con Hadoop en HDInsight](hdinsight-use-hive.md)

* [Usar MapReduce con Hadoop en HDInsight](hdinsight-use-mapreduce.md)
