<properties
    pageTitle="Depurar y analizar los servicios de Hadoop con volcados del montón | Microsoft Azure"
    description="Recopilar volcados del montón para servicios de Hadoop y colocar dentro de la cuenta de almacenamiento de blobs de Windows Azure de depuración y análisis automáticamente."
    services="hdinsight"
    documentationCenter=""
    tags="azure-portal"
    authors="mumian"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="hdinsight"
    ms.workload="big-data"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/19/2016"
    ms.author="jgao"/>


# <a name="collect-heap-dumps-in-blob-storage-to-debug-and-analyze-hadoop-services"></a>Recopilar montón vuelca en el almacenamiento de blobs para depurar y analizar los servicios de Hadoop

[AZURE.INCLUDE [heapdump-selector](../../includes/hdinsight-selector-heap-dump.md)]

Volcados del montón contienen una instantánea de memoria de la aplicación, incluidos los valores de variables en el momento en que se creó la descarga. Por lo que son muy útiles para diagnosticar los problemas que se producen en tiempo de ejecución. Volcados del montón pueden recopila para servicios de Hadoop automáticamente y se coloca dentro de la cuenta de almacenamiento de blobs de Windows Azure de un usuario en HDInsightHeapDumps /. 

La colección de volcados del montón para varios servicios debe habilitarse para los servicios de clústeres individuales. El valor predeterminado de esta característica es estar desactivada para un clúster. Estos volcados del montón pueden ser grandes, por lo que es aconsejable supervisar la cuenta de almacenamiento de blobs de Windows donde se que se guardan una vez que se ha habilitado la colección.

> [AZURE.NOTE] La información en este artículo se aplica solo a HDInsight de Windows. Para obtener información sobre HDInsight de Linux, vea [Activar montón volcados Hadoop los servicios de HDInsight de Linux](hdinsight-hadoop-collect-debug-heap-dump-linux.md)

## <a name="eligible-services-for-heap-dumps"></a>Servicios elegibles para volcados del montón

Puede activar montón volcados para los siguientes servicios:

*  **hcatalog** - tempelton
*  **sección** - hiveserver2, metastore, derbyserver
*  **mapreduce** - jobhistoryserver
*  **hilo** - resourcemanager, nodemanager, timelineserver
*  **hdfs** - datanode, secondarynamenode, namenode

## <a name="configuration-elements-that-enable-heap-dumps"></a>Elementos de configuración que activar montón volcados

Para activar montón volcados de un servicio, debe configurar los elementos de configuración apropiado en la sección de ese servicio, que se especifica por **service_name**.

    "javaargs.<service_name>.XX:+HeapDumpOnOutOfMemoryError" = "-XX:+HeapDumpOnOutOfMemoryError",
    "javaargs.<service_name>.XX:HeapDumpPath" = "-XX:HeapDumpPath=c:\Dumps\<service_name>_%date:~4,2%%date:~7,2%%date:~10,2%%time:~0,2%%time:~3,2%%time:~6,2%.hprof"

El valor de **service_name** puede representar cualquiera de los servicios enumerados anteriormente: tempelton, hiveserver2, metastore, derbyserver, jobhistoryserver, resourcemanager, nodemanager, timelineserver, datanode, secondarynamenode, o namenode.

## <a name="enable-using-azure-powershell"></a>Habilitar el uso de PowerShell de Azure

Por ejemplo, para activar montón volcados con PowerShell de Azure para jobhistoryserver, que hacer lo siguiente:

[AZURE.INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]

    $MapRedConfigValues = new-object 'Microsoft.WindowsAzure.Management.HDInsight.Cmdlet.DataObjects.AzureHDInsightMapReduceConfiguration'

    $MapRedConfigValues.Configuration = @{ "javaargs.jobhistoryserver.XX:+HeapDumpOnOutOfMemoryError"="-XX:+HeapDumpOnOutOfMemoryError" ; "javaargs.jobhistoryserver.XX:HeapDumpPath" = "-XX:HeapDumpPath=c:\\Dumps\\jobhistoryserver_%date:~4,2%_%date:~7,2%_%date:~10,2%_%time:~0,2%_%time:~3,2%_%time:~6,2%.hprof" }

## <a name="enable-using-net-sdk"></a>Habilitar el uso de .NET SDK

Por ejemplo, para activar montón volcados usando el SDK de .NET HDInsight de Azure para jobhistoryserver, que hacer lo siguiente:

    clusterInfo.MapReduceConfiguration.ConfigurationCollection.Add(new KeyValuePair<string, string>("javaargs.jobhistoryserver.XX:+HeapDumpOnOutOfMemoryError", "-XX:+HeapDumpOnOutOfMemoryError"));

    clusterInfo.MapReduceConfiguration.ConfigurationCollection.Add(new KeyValuePair<string, string>("javaargs.jobhistoryserver.XX:HeapDumpPath", "-XX:HeapDumpPath=c:\\Dumps\\jobhistoryserver_%date:~4,2%_%date:~7,2%_%date:~10,2%_%time:~0,2%_%time:~3,2%_%time:~6,2%.hprof"));
