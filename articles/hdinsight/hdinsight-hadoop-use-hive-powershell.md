<properties
   pageTitle="Usar Hadoop subárbol con PowerShell en HDInsight | Microsoft Azure"
   description="Usar PowerShell para ejecutar consultas de sección en Hadoop en HDInsight."
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
   ms.date="09/07/2016"
   ms.author="larryfr"/>

#<a name="run-hive-queries-using-powershell"></a>Ejecutar consultas de subárbol con PowerShell

[AZURE.INCLUDE [hive-selector](../../includes/hdinsight-selector-use-hive.md)]

Este documento proporciona un ejemplo de uso de PowerShell de Azure en el modo de grupo de recursos de Azure para ejecutar consultas de sección en una Hadoop en clúster de HDInsight.

> [AZURE.NOTE] Este documento no proporciona una descripción detallada de lo que siga las instrucciones de HiveQL que se usan en los ejemplos. Para obtener información sobre la HiveQL que se usa en este ejemplo, vea [Usar subárbol con Hadoop en HDInsight](hdinsight-use-hive.md).


**Requisitos previos**

Para completar los pasos de este artículo, necesita lo siguiente.

- **Un clúster de Azure HDInsight (Hadoop en HDInsight) (basado en Windows o en Linux)**
- **Una estación de trabajo con PowerShell de Azure**.

    [AZURE.INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]

##<a name="run-hive-queries-using-azure-powershell"></a>Ejecutar consultas de subárbol con PowerShell de Azure

Azure PowerShell ofrece *cmdlets* que le permiten ejecutar consultas subárbol en HDInsight de forma remota. Internamente, esto se logra mediante llamadas del resto a [WebHCat](https://cwiki.apache.org/confluence/display/Hive/WebHCat) (anteriormente llamado Templeton) ejecutan en el clúster HDInsight.

Al ejecutar consultas de la sección en un clúster de HDInsight remoto, se usan los siguientes cmdlets:

* **Agregar AzureRmAccount**: autentica Azure PowerShell a su suscripción de Azure

* **Nuevo AzureRmHDInsightHiveJobDefinition**: crea una nueva *definición de trabajo* mediante las instrucciones de HiveQL especificadas

* **Inicio AzureRmHDInsightJob**: la definición de trabajo se envía a HDInsight, el trabajo se inicia y devuelve un objeto de *trabajo* que se pueden usar para comprobar el estado del trabajo

* **Espera AzureRmHDInsightJob**: utiliza el objeto de trabajo para comprobar el estado del trabajo. Esperará hasta que finalice el trabajo o se superó el tiempo de espera.

* **Get-AzureRmHDInsightJobOutput**: usado para recuperar el resultado de la tarea

* **Invocar AzureRmHDInsightHiveJob**: utilizada para ejecutar instrucciones HiveQL. Esto bloqueará la consulta se completa, a continuación, devuelve los resultados

* **Usar AzureRmHDInsightCluster**: establece el clúster actual para usar el comando **Invocar AzureRmHDInsightHiveJob**

Los pasos siguientes muestran cómo usar estos cmdlets para ejecutar un trabajo en el clúster HDInsight:

1. Usar un editor, guarde el código siguiente como **hivejob.ps1**. Debe reemplazarse **NOMBREDECLÚSTER** con el nombre de su clúster HDInsight.

        #Specify the values
        $clusterName = "CLUSTERNAME"
        $creds=Get-Credential

        # Login to your Azure subscription
        # Is there an active Azure subscription?
        $sub = Get-AzureRmSubscription -ErrorAction SilentlyContinue
        if(-not($sub))
        {
            Add-AzureRmAccount
        }

        #HiveQL
        #Note: set hive.execution.engine=tez; is not required for
        #      Linux-based HDInsight
        $queryString = "set hive.execution.engine=tez;" +
                    "DROP TABLE log4jLogs;" +
                    "CREATE EXTERNAL TABLE log4jLogs(t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) ROW FORMAT DELIMITED FIELDS TERMINATED BY ' ' STORED AS TEXTFILE LOCATION 'wasbs:///example/data/';" +
                    "SELECT * FROM log4jLogs WHERE t4 = '[ERROR]';"

        #Create an HDInsight Hive job definition
        $hiveJobDefinition = New-AzureRmHDInsightHiveJobDefinition -Query $queryString 

        #Submit the job to the cluster
        Write-Host "Start the Hive job..." -ForegroundColor Green

        $hiveJob = Start-AzureRmHDInsightJob -ClusterName $clusterName -JobDefinition $hiveJobDefinition -ClusterCredential $creds

        #Wait for the Hive job to complete
        Write-Host "Wait for the job to complete..." -ForegroundColor Green
        Wait-AzureRmHDInsightJob -ClusterName $clusterName -JobId $hiveJob.JobId -ClusterCredential $creds

        #Get the cluster info so we can get the resource group, storage, etc.
        $clusterInfo = Get-AzureRmHDInsightCluster -ClusterName $clusterName
        $resourceGroup = $clusterInfo.ResourceGroup
        $storageAccountName=$clusterInfo.DefaultStorageAccount.split('.')[0]
        $container=$clusterInfo.DefaultStorageContainer
        $storageAccountKey=(Get-AzureRmStorageAccountKey `
            -Name $storageAccountName `
        -ResourceGroupName $resourceGroup)[0].Value
        # Print the output
        Write-Host "Display the standard output..." -ForegroundColor Green
        Get-AzureRmHDInsightJobOutput `
            -Clustername $clusterName `
            -JobId $hiveJob.JobId `
            -DefaultContainer $container `
            -DefaultStorageAccountName $storageAccountName `
            -DefaultStorageAccountKey $storageAccountKey `
            -HttpCredential $creds
            
2. Abra un nuevo símbolo de **PowerShell de Azure** . Cambie los directorios a la ubicación del archivo **hivejob.ps1** y luego use el comando siguiente para ejecutar la secuencia de comandos:

        .\hivejob.ps1

    Cuando se ejecuta la secuencia de comandos, le pedirá que lo escriba las credenciales de cuenta de administrador/HTTPS para el clúster. Puede también se le iniciar sesión en su suscripción de Azure.
    
7. Cuando finalice el trabajo, se debe devolver información similar al siguiente:

        Display the standard output...
        2012-02-03      18:35:34        SampleClass0    [ERROR] incorrect       id
        2012-02-03      18:55:54        SampleClass1    [ERROR] incorrect       id
        2012-02-03      19:25:27        SampleClass4    [ERROR] incorrect       id

4. Como se mencionó anteriormente, **Invocar subárbol** puede usarse para ejecutar una consulta y esperar la respuesta. Utilice los comandos siguientes y reemplace **NOMBREDECLÚSTER** con el nombre del clúster:

        Use-AzureRmHDInsightCluster -ClusterName $clusterName -HttpCredential $creds
        #Get the cluster info so we can get the resource group, storage, etc.
        $clusterInfo = Get-AzureRmHDInsightCluster -ClusterName $clusterName
        $resourceGroup = $clusterInfo.ResourceGroup
        $storageAccountName=$clusterInfo.DefaultStorageAccount.split('.')[0]
        $container=$clusterInfo.DefaultStorageContainer
        $storageAccountKey=(Get-AzureRmStorageAccountKey `
            -Name $storageAccountName `
        -ResourceGroupName $resourceGroup)[0].Value
        $queryString = "set hive.execution.engine=tez;" +
                    "DROP TABLE log4jLogs;" +
                    "CREATE EXTERNAL TABLE log4jLogs(t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) ROW FORMAT DELIMITED FIELDS TERMINATED BY ' ' STORED AS TEXTFILE LOCATION 'wasbs:///example/data/';" +
                    "SELECT * FROM log4jLogs WHERE t4 = '[ERROR]';"
        Invoke-AzureRmHDInsightHiveJob `
            -StatusFolder "statusout" `
            -DefaultContainer $container `
            -DefaultStorageAccountName $storageAccountName `
            -DefaultStorageAccountKey $storageAccountKey `
            -Query $queryString

    El resultado tendrá un aspecto similar al siguiente:

        2012-02-03  18:35:34    SampleClass0    [ERROR] incorrect   id
        2012-02-03  18:55:54    SampleClass1    [ERROR] incorrect   id
        2012-02-03  19:25:27    SampleClass4    [ERROR] incorrect   id

    > [AZURE.NOTE] Para las consultas de HiveQL más, puede usar el cmdlet de PowerShell de Azure **Aquí cadenas** o los archivos de secuencia de comandos de HiveQL. El fragmento de código siguiente muestra cómo usar el cmdlet **Invocar subárbol** para ejecutar un archivo de script de HiveQL. El archivo de comandos de HiveQL debe cargarse en wasbs: / /.
    >
    > `Invoke-AzureRmHDInsightHiveJob -File "wasbs://<ContainerName>@<StorageAccountName>/<Path>/query.hql"`
    >
    > Para obtener más información sobre **Las cadenas de aquí**, vea <a href="http://technet.microsoft.com/library/ee692792.aspx" target="_blank">Usar Windows PowerShell aquí cadenas</a>.

##<a name="troubleshooting"></a>Solución de problemas

Si no se devuelve información cuando finalice el trabajo, puede ha ocurrido un error durante el proceso. Para ver información de error para esta tarea, agregue lo siguiente al final del archivo **hivejob.ps1** , guárdelo y ejecute de nuevo.

    # Print the output of the Hive job.
    Get-AzureRmHDInsightJobOutput `
            -Clustername $clusterName `
            -JobId $job.JobId `
            -DefaultContainer $container `
            -DefaultStorageAccountName $storageAccountName `
            -DefaultStorageAccountKey $storageAccountKey `
            -HttpCredential $creds `
            -DisplayOutputType StandardError

Devuelve la información que se escribe en STDERR en el servidor cuando ejecutó el trabajo y, a continuación, puede ayudar a determinar por qué no funciona el trabajo.

##<a name="summary"></a>Resumen

Como puede ver, Azure PowerShell proporciona una forma sencilla de ejecutar consultas de la sección en un clúster de HDInsight, supervisar el estado del trabajo y recuperar el resultado.

##<a name="next-steps"></a>Pasos siguientes

Para obtener información general sobre subárbol en HDInsight:

* [Usar subárbol con Hadoop en HDInsight](hdinsight-use-hive.md)

Para obtener información sobre otras formas de puede trabajar con Hadoop en HDInsight:

* [Usar cerdo con Hadoop en HDInsight](hdinsight-use-pig.md)

* [Usar MapReduce con Hadoop en HDInsight](hdinsight-use-mapreduce.md)
