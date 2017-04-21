<properties
    pageTitle="Instalar y usar Giraph en clústeres Hadoop en HDInsight | Microsoft Azure"
    description="Obtenga información sobre cómo personalizar clúster de HDInsight con Giraph y cómo usar Giraph."
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
    ms.date="02/05/2016"
    ms.author="nitinme"/>

# <a name="install-and-use-giraph-in-hdinsight"></a>Instalar y usar Giraph en HDInsight


Obtenga información sobre cómo personalizar clúster en función de HDInsight de Windows con Giraph con la acción de secuencia de comandos y cómo usar Giraph para procesar gráficos a gran escala. Para obtener información sobre cómo usar Giraph con un clúster de Linux, consulte [Instalar Giraph en HDInsight Hadoop clústeres (Linux)](hdinsight-hadoop-giraph-install-linux.md).
 
Puede instalar Giraph en cualquier tipo de clúster (Hadoop, tormenta, HBase, motor) en HDInsight de Azure mediante *Secuencia de comandos de acción*. Una secuencia de comandos de ejemplo para instalar Giraph en un clúster de HDInsight está disponible desde un blob de solo lectura almacenamiento de Azure en [https://hdiconfigactions.blob.core.windows.net/giraphconfigactionv01/giraph-installer-v01.ps1](https://hdiconfigactions.blob.core.windows.net/giraphconfigactionv01/giraph-installer-v01.ps1). La secuencia de comandos sólo funciona con versión de clúster HDInsight 3.1. Para obtener más información sobre versiones de clúster HDInsight, consulte [versiones de clúster HDInsight](hdinsight-component-versioning.md).

**Artículos relacionados**

- [Instalar Giraph en HDInsight Hadoop clústeres (Linux)](hdinsight-hadoop-giraph-install-linux.md)
- [Crear Hadoop clústeres en HDInsight](hdinsight-provision-clusters.md): información general sobre la creación de clústeres HDInsight.
- [Personalizar clúster HDInsight con la acción de secuencia de comandos][hdinsight-cluster-customize]: información general sobre la personalización de clústeres HDInsight con la acción de secuencia de comandos.
- [Acción de secuencia de comandos con el desarrollo de secuencias de comandos para HDInsight](hdinsight-hadoop-script-actions.md).

## <a name="what-is-giraph"></a>¿Qué es Giraph?

<a href="http://giraph.apache.org/" target="_blank">Apache Giraph</a> permite realizar graph procesar mediante Hadoop y se pueden usar con HDInsight de Azure. Gráficos de modelo de relaciones entre objetos, como las conexiones entre en una red grande como Internet o relaciones entre personas en redes sociales (también se conoce como un gráfico social). Procesamiento de gráfico le permite motivo acerca de las relaciones entre objetos de un gráfico, como por ejemplo:

- Identificar a posibles amigos en función de sus relaciones actuales.
- Identifica la ruta más corta entre dos equipos en una red.
- Calcular la jerarquía de la página de las páginas Web.


## <a name="install-giraph-using-portal"></a>Instalar Giraph con portal

1. Empezar a crear un clúster mediante la opción **Crear personalizado** , como se describe en [Hadoop crear clústeres en HDInsight](hdinsight-provision-clusters.md#portal).
2. En la página de **Acciones de secuencia de comandos** del asistente, haga clic en **Agregar acción de secuencia de comandos** para proporcionar información detallada sobre la acción de secuencia de comandos, tal como se muestra a continuación:

    ![Acción de secuencia de comandos de uso para personalizar un clúster] (./media/hdinsight-hadoop-giraph-install/hdi-script-action-giraph.png "Acción de secuencia de comandos de uso para personalizar un clúster")

    <table border='1'>
        <tr><th>(Propiedad)</th><th>Valor</th></tr>
        <tr><td>Nombre</td>
            <td>Especifique un nombre para la acción de secuencia de comandos. Por ejemplo, <b>Instalar Giraph</b>.</td></tr>
        <tr><td>URI de secuencia de comandos</td>
            <td>Especificar el identificador uniforme de recursos (URI) a la secuencia de comandos que se invoca para personalizar el clúster. Por ejemplo, <i>https://hdiconfigactions.blob.core.windows.net/giraphconfigactionv01/giraph-installer-v01.ps1</i></td></tr>
        <tr><td>Tipo de nodo</td>
            <td>Especifique los nodos en que se ejecuta la secuencia de comandos de personalización. Puede elegir <b>todos los nodos</b>, <b>solo los nodos de cabeza</b>o <b>solo los nodos de trabajo</b>.
        <tr><td>Parámetros</td>
            <td>Especificar los parámetros, si es necesario la secuencia de comandos. La secuencia de comandos para instalar Giraph no requiere parámetros, por lo que puede dejar en blanco.</td></tr>
    </table>

    Puede agregar más de una acción de secuencia de comandos para instalar varios componentes en el clúster. Después de haber agregado las secuencias de comandos, haga clic en la marca de verificación para empezar a crear el clúster.

## <a name="use-giraph"></a>Usar Giraph

Usamos el ejemplo SimpleShortestPathsComputation para demostrar la implementación de <a href = "http://people.apache.org/~edwardyoon/documents/pregel.pdf">Pregel</a> básica para buscar la ruta de acceso más corta entre objetos de un gráfico. Realice los siguientes pasos para cargar los datos de ejemplo y jar de ejemplo, ejecute un trabajo mediante el ejemplo SimpleShortestPathsComputation y, a continuación, ver los resultados.

1. Cargar un archivo de datos de ejemplo con el almacenamiento de blobs de Windows Azure. En su estación de trabajo, cree un nuevo archivo denominado **tiny_graph.txt**. Debe contener las siguientes líneas:

        [0,0,[[1,1],[3,3]]]
        [1,0,[[0,1],[2,2],[3,1]]]
        [2,0,[[1,2],[4,4]]]
        [3,0,[[0,3],[1,1],[4,4]]]
        [4,0,[[3,4],[2,4]]]

    Cargue el archivo tiny_graph.txt al almacenamiento primario para el clúster de HDInsight. Para obtener instrucciones sobre cómo cargar datos, vea [cargar datos para trabajos de Hadoop en HDInsight](hdinsight-upload-data.md).

    Una relación entre objetos en un gráfico dirigido, utilizando el formato, describen estos datos [origen\_id, origen\_valor, [[dest\_id], [borde\_valor],...]]. Cada línea representa una relación entre un **origen\_id** objeto y uno o más **dest\_id** objetos. La **borde\_valor** (o grosor) puede considerarse como la distancia de la conexión entre **source_id** o intensidad y **dest\_id**.

    Dibujado, y usa el valor (o grosor) como la distancia entre objetos, los datos anteriores tendrá este aspecto:

    ![dibujado en forma de círculos con líneas de diversos distancia entre el tiny_graph.txt](./media/hdinsight-hadoop-giraph-install/giraph-graph.png)



4. Ejecute el ejemplo SimpleShortestPathsComputation. Use los siguientes cmdlets de PowerShell de Azure para ejecutar el ejemplo mediante el archivo tiny_graph.txt como entrada. 

    [AZURE.INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]

        $clusterName = "clustername"
        # Giraph examples jar
        $jarFile = "wasbs:///example/jars/giraph-examples.jar"
        # Arguments for this job
        $jobArguments = "org.apache.giraph.examples.SimpleShortestPathsComputation",
                        "-ca", "mapred.job.tracker=headnodehost:9010",
                        "-vif", "org.apache.giraph.io.formats.JsonLongDoubleFloatDoubleVertexInputFormat",
                        "-vip", "wasbs:///example/data/tiny_graph.txt",
                        "-vof", "org.apache.giraph.io.formats.IdWithValueTextOutputFormat",
                        "-op",  "wasbs:///example/output/shortestpaths",
                        "-w", "2"
        # Create the definition
        $jobDefinition = New-AzureHDInsightMapReduceJobDefinition
          -JarFile $jarFile
          -ClassName "org.apache.giraph.GiraphRunner"
          -Arguments $jobArguments

        # Run the job, write output to the Azure PowerShell window
        $job = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $jobDefinition
        Write-Host "Wait for the job to complete ..." -ForegroundColor Green
        Wait-AzureHDInsightJob -Job $job
        Write-Host "STDERR"
        Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $job.JobId -StandardError
        Write-Host "Display the standard output ..." -ForegroundColor Green
        Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $job.JobId -StandardOutput

    En el ejemplo anterior, reemplace **nombreDeClúster** por el nombre de su clúster de HDInsight con Giraph instalado.

5. Ver los resultados. Una vez que haya finalizado el trabajo, los resultados se almacenarán en dos archivos de salida en el __wasbs: / / / ejemplo/out/shotestpaths__ carpeta. Los archivos se denominan __parte-m-00001__ y __elemento-m-00002__. Realice los pasos siguientes para descargar y ver el resultado:

        $subscriptionName = "<SubscriptionName>"       # Azure subscription name
        $storageAccountName = "<StorageAccountName>"   # Azure Storage account name
        $containerName = "<ContainerName>"             # Blob storage container name

        # Select the current subscription
        Select-AzureSubscription $subscriptionName

        # Create the Storage account context object
        $storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }
        $storageContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey

        # Download the job output to the workstation
        Get-AzureStorageBlobContent -Container $containerName -Blob example/output/shortestpaths/part-m-00001 -Context $storageContext -Force
        Get-AzureStorageBlobContent -Container $containerName -Blob example/output/shortestpaths/part-m-00002 -Context $storageContext -Force

    Esto crea la estructura de directorios de __resultados de ejemplo/shortestpaths__ en el directorio actual de la estación de trabajo y descargar los archivos de dos salida a esa ubicación.

    Usar el cmdlet __gato__ para mostrar el contenido de los archivos:

        Cat example/output/shortestpaths/part*

    El resultado debería ser similar al siguiente:


        0   1.0
        4   5.0
        2   2.0
        1   0.0
        3   1.0

    La SimpleShortestPathComputation ejemplo está codificado para empezar con 1 identificador de objeto y buscar la ruta de acceso más corta a otros objetos. Para que el resultado se debe leer como `destination_id distance`, donde la distancia es el valor o grosor de los bordes desplazados entre objeto identificador 1 y el identificador de destino.

    Visualizar esto, puede comprobar los resultados de las rutas de acceso más corta de viaje entre ID 1 y todos los demás objetos. Observe que la ruta de acceso más corta entre ID 1 y 4 ID es 5. Esta es la distancia total entre <span style="color:orange">ID 1 y 3</span>y, a continuación, <span style="color:red">identificador de 3 y 4</span>.

    ![Dibujo de objetos como círculos con rutas de acceso más corta dibujadas entre](./media/hdinsight-hadoop-giraph-install/giraph-graph-out.png)

## <a name="install-giraph-using-aure-powershell"></a>Instalar Giraph con Aure PowerShell

Consulte [clústeres personalizar HDInsight con la acción de secuencia de comandos](hdinsight-hadoop-customize-cluster.md#call_scripts_using_powershell).  El ejemplo muestra cómo instalar el motor con PowerShell de Azure. Debe personalizar la secuencia de comandos para usar [https://hdiconfigactions.blob.core.windows.net/giraphconfigactionv01/giraph-installer-v01.ps1](https://hdiconfigactions.blob.core.windows.net/giraphconfigactionv01/giraph-installer-v01.ps1).

## <a name="install-giraph-using-net-sdk"></a>Instalar Giraph mediante el SDK de .NET

Consulte [clústeres personalizar HDInsight con la acción de secuencia de comandos](hdinsight-hadoop-customize-cluster.md#call_scripts_using_azure_powershell). El ejemplo muestra cómo instalar motor mediante .NET SDK. Debe personalizar la secuencia de comandos para usar [https://hdiconfigactions.blob.core.windows.net/giraphconfigactionv01/giraph-installer-v01.ps1](https://hdiconfigactions.blob.core.windows.net/giraphconfigactionv01/giraph-installer-v01.ps1).


## <a name="see-also"></a>Vea también

- [Instalar Giraph en HDInsight Hadoop clústeres (Linux)](hdinsight-hadoop-giraph-install-linux.md)
- [Crear Hadoop clústeres en HDInsight](hdinsight-provision-clusters.md): información general sobre la creación de clústeres HDInsight.
- [Personalizar clúster HDInsight con la acción de secuencia de comandos][hdinsight-cluster-customize]: información general sobre la personalización de clústeres HDInsight con la acción de secuencia de comandos.
- [Acción de secuencia de comandos con el desarrollo de secuencias de comandos para HDInsight](hdinsight-hadoop-script-actions.md).
- [Instalar y usar el motor en clústeres HDInsight][hdinsight-install-spark]: ejemplo de Script acción acerca de cómo instalar el motor.
- [Instalar R en clústeres HDInsight][hdinsight-install-r]: ejemplo de acción de secuencia de comandos sobre la instalación de R.
- [Instalar Solr en HDInsight clústeres](hdinsight-hadoop-solr-install.md): ejemplo de acción de secuencia de comandos sobre la instalación de Solr.



[tools]: https://github.com/Blackmist/hdinsight-tools
[aps]: http://azure.microsoft.com/documentation/articles/install-configure-powershell/

[powershell-install]: ../powershell-install-configure.md
[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-install-r]: hdinsight-hadoop-r-scripts.md
[hdinsight-install-spark]: hdinsight-hadoop-spark-install.md
[hdinsight-cluster-customize]: hdinsight-hadoop-customize-cluster.md
