<properties
    pageTitle="Generar recomendaciones mediante Mahout y HDInsight de WIndows | Microsoft Azure"
    description="Obtenga información sobre cómo usar el equipo Apache Mahout biblioteca de aprendizaje para generar recomendaciones de película con HDInsight basado en Windows (Hadoop)."
    services="hdinsight"
    documentationCenter=""
    authors="Blackmist"
    manager="jhubbard"
    editor="cgronlun"
    tags="azure-portal"/>

<tags
    ms.service="hdinsight"
    ms.workload="big-data"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/11/2016"
    ms.author="larryfr"/>

#<a name="generate-movie-recommendations-by-using-apache-mahout-with-hadoop-in-hdinsight"></a>Generar recomendaciones de película mediante Apache Mahout con Hadoop en HDInsight

[AZURE.INCLUDE [mahout-selector](../../includes/hdinsight-selector-mahout.md)]

Obtenga información sobre cómo usar el equipo [Apache Mahout](http://mahout.apache.org) biblioteca con HDInsight de Azure de aprendizaje para generar recomendaciones de película.

> [AZURE.NOTE] Los pasos de este documento requieren un cliente de Windows y un clúster basado en Windows HDInsight. Para obtener información sobre cómo usar Mahout de Linux, OS X o cliente Unix, con un clúster basado en Linux HDInsight, consulte [generar recomendaciones de película utilizando Apache Mahout con Hadoop basados en Linux en HDInsight](hdinsight-hadoop-mahout-linux-mac.md)


##<a name="learn"></a>Lo que aprenderá

Mahout es una [máquina de aprendizaje] [ ml] biblioteca para Apache Hadoop. Mahout contiene algoritmos para el procesamiento de datos, como el filtrado, clasificación y clústeres. En este artículo, usará un motor de recomendación para generar recomendaciones de película que se basan en películas que han visto sus amigos. También aprenderá cómo realizar clasificaciones con un bosque de decisión. Esto aprenderá lo siguiente:

* Cómo ejecutar trabajos de Mahout con Windows PowerShell

* Cómo ejecutar trabajos Mahout desde la línea de comandos de Hadoop

* Cómo instalar a Mahout en clústeres HDInsight 3.0 y HDInsight 2.0

    > [AZURE.NOTE] Mahout se proporciona con la versión 3.1 HDInsight de los clústeres. Si está utilizando una versión anterior de HDInsight, consulte [Instalar Mahout](#install) antes de continuar.

##<a name="prerequisites"></a>requisitos previos

- **Clúster basado en Windows una Hadoop en HDInsight**. Para obtener información sobre cómo crear uno, vea [Introducción al uso de Hadoop en HDInsight][getstarted]
- **Una estación de trabajo con PowerShell de Azure**.

    [AZURE.INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]


##<a name="recommendations"></a>Generar recomendaciones con Windows PowerShell

> [AZURE.NOTE] Aunque el trabajo que se utiliza en esta sección funciona con Windows PowerShell, muchas de las clases suministradas con Mahout actualmente no funcionan con Windows PowerShell y deben ejecutarse mediante el uso de la línea de comandos de Hadoop. Para obtener una lista de clases que no funcionan con Windows PowerShell, vea la sección de [solución de problemas](#troubleshooting) .
>
> Para obtener un ejemplo de uso de la línea de comandos Hadoop para ejecutar trabajos Mahout, vea [clasificar datos mediante el uso de la línea de comandos de Hadoop](#classify).

Una de las funciones que proporciona Mahout es un motor de recomendación. Este motor acepta datos en el formato de `userID`, `itemId`, y `prefValue` (la preferencia de los usuarios del elemento). Mahout, a continuación, puede realizar un análisis de la instancia para determinar: _los usuarios que tienen una preferencia para un elemento tienen también una preferencia para estos otros elementos_. Mahout determina los usuarios con las preferencias de elemento de tipo, que pueden usarse para hacer recomendaciones.

A continuación se muestra un ejemplo muy sencillo que utiliza películas:

* __La aparición__: Juan, Ana y Bob le ha gustado _guerra de estrella_, _La imperio fijado anterior_y _devolver de la Jedi_. Mahout determina que los usuarios que le guste cualquiera de estas películas también como las otras dos.

* __La aparición__: Juan y Ana también le gustó _La amenaza de fantasma_, _ataque de las copias_y _Revenge of la Sith_. Mahout determina que los usuarios que le gustó la anterior tres películas también como estos tres.

* __Recomendación semejanza__: Joe porque le ha gustado las tres primeras películas, Mahout busca en películas que otras personas con preferencias similar le gustó, pero Juan no ha observado (le gustó o calificó). En este caso, Mahout recomienda _La amenaza de fantasma_, _ataque de las copias_y _Revenge of la Sith_.

###<a name="understanding-the-data"></a>Descripción de los datos

Cómodamente, [GroupLens Research] [ movielens] proporciona los datos de clasificación de películas en un formato que es compatible con Mahout. Estos datos están disponibles en el almacenamiento de predeterminado del clúster en `/HdiSamples/MahoutMovieData`.

Hay dos archivos `moviedb.txt` (información sobre las películas) y `user-ratings.txt`. El archivo ratings.txt de usuario se utiliza durante el análisis, mientras moviedb.txt se utiliza para proporcionar información de texto descriptivo cuando se muestran los resultados del análisis.

Los datos contenidos en ratings.txt de usuario tienen una estructura de `userID`, `movieID`, `userRating`, y `timestamp`, que nos indica cómo altamente cada usuario clasificación una película. Aquí tiene un ejemplo de los datos:


    196 242 3   881250949
    186 302 3   891717742
    22  377 1   878887116
    244 51  2   880606923
    166 346 1   886397596

###<a name="run-the-job"></a>Ejecutar el trabajo

Use el siguiente script de Windows PowerShell para ejecutar una tarea que usa el motor de recomendación Mahout con los datos de la película:

    # The HDInsight cluster name.
    $clusterName = "the cluster name"
    
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
            
    # NOTE: The version number in the file path
    # may change in future versions of HDInsight.
    $jarFile =  "file:///C:/apps/dist/mahout-0.9.0.2.2.9.1-8/examples/target/mahout-examples-0.9.0.2.2.9.1-8-job.jar"
    #
    # If you are using an earlier version of HDInsight,
    # set $jarFile to the jar file you
    # uploaded.
    # For example,
    # $jarFile = "wasbs:///example/jars/mahout-core-0.9-job.jar"

    # The arguments for this job
    # * input - the path to the data uploaded to HDInsight
    # * output - the path to store output data
    # * tempDir - the directory for temp files
    $jobArguments = "--similarityClassname", "recommenditembased", `
                    "-s", "SIMILARITY_COOCCURRENCE", `
                    "--input", "wasbs:///HdiSamples/MahoutMovieData/user-ratings.txt",
                    "--output", "wasbs:///example/out",
                    "--tempDir", "wasbs:///example/temp"

    # Create the job definition
    $jobDefinition = New-AzureRmHDInsightMapReduceJobDefinition `
      -JarFile $jarFile `
      -ClassName "org.apache.mahout.cf.taste.hadoop.item.RecommenderJob" `
      -Arguments $jobArguments

    # Start the job
    $job = Start-AzureRmHDInsightJob `
        -ClusterName $clusterName `
        -JobDefinition $jobDefinition `
        -HttpCredential $creds

    # Wait on the job to complete
    Write-Host "Wait for the job to complete ..." -ForegroundColor Green
    Wait-AzureRmHDInsightJob `
            -ClusterName $clusterName `
            -JobId $job.JobId `
            -HttpCredential $creds
    # Download the output
    Get-AzureStorageBlobContent `
            -Blob example/out/part-r-00000 `
            -Container $container `
            -Destination output.txt `
            -Context $context
            
    # Write out any error information
    Write-Host "STDERR"
    Get-AzureRmHDInsightJobOutput `
            -Clustername $clusterName `
            -JobId $job.JobId `
            -DefaultContainer $container `
            -DefaultStorageAccountName $storageAccountName `
            -DefaultStorageAccountKey $storageAccountKey `
            -HttpCredential $creds `
            -DisplayOutputType StandardError

> [AZURE.NOTE] Mahout trabajos no quitan datos temporales que se crean al procesar el trabajo. La `--tempDir` parámetro especificado en el trabajo de ejemplo para aislar los archivos temporales en un directorio específico.

El trabajo Mahout no devuelve el resultado para STDOUT. En su lugar, almacena en el directorio de salida especificado como __parte-r-00000__. La secuencia de comandos descarga este archivo a __output.txt__ en el directorio actual de la estación de trabajo.

A continuación se muestra un ejemplo del contenido de este archivo:

    1   [234:5.0,347:5.0,237:5.0,47:5.0,282:5.0,275:5.0,88:5.0,515:5.0,514:5.0,121:5.0]
    2   [282:5.0,210:5.0,237:5.0,234:5.0,347:5.0,121:5.0,258:5.0,515:5.0,462:5.0,79:5.0]
    3   [284:5.0,285:4.828125,508:4.7543354,845:4.75,319:4.705128,124:4.7045455,150:4.6938777,311:4.6769233,248:4.65625,272:4.649266]
    4   [690:5.0,12:5.0,234:5.0,275:5.0,121:5.0,255:5.0,237:5.0,895:5.0,282:5.0,117:5.0]

La primera columna es el `userID`. Los valores contenidos en ' [' y ']' son `movieId`:`recommendationScore`.

###<a name="view-the-output"></a>Ver el resultado

Aunque el resultado generado pueden Aceptar para su uso en una aplicación, no es muy legible. La `moviedb.txt` desde el servidor de puede usarse para solucionar el `movieId` a una película nombre, pero debe descargar junto con el archivo de clasificación del servidor mediante la siguiente secuencia de comandos:

    # The HDInsight cluster name.
    $clusterName = "the cluster name"
    
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
    #Download the files
    Get-AzureStorageBlobContent -blob "HdiSamples/MahoutMovieData/moviedb.txt" `
    -Container $container `
    -Destination moviedb.txt `
    -Context $context
    Get-AzureStorageBlobContent -blob "HdiSamples/MahoutMovieData/user-ratings.txt" `
    -Container $container `
    -Destination user-ratings.txt `
    -Context $context

Una vez que haya descargado los archivos, use el siguiente script de PowerShell para mostrar recomendaciones con nombres de película:

    <#
    .SYNOPSIS
        Displays recommendations for movies.
    .DESCRIPTION
        Displays recommendations generated by Mahout
        with HDInsight example in a human readable format.
    .EXAMPLE
        .\Show-Recommendation -userId 4
            -userDataFile "user-ratings.txt"
            -movieFile "moviedb.txt"
            -recommendationFile "output.txt"
    #>

    [CmdletBinding(SupportsShouldProcess = $true)]
    param(
        #The user ID
        [Parameter(Mandatory = $true)]
        [String]$userId,

        [Parameter(Mandatory = $true)]
        [String]$userDataFile,

        [Parameter(Mandatory = $true)]
        [String]$movieFile,

        [Parameter(Mandatory = $true)]
        [String]$recommendationFile
    )
    # Read movie ID & description into hash table
    Write-Host "Reading movies descriptions" -ForegroundColor Green
    $movieById = @{}
    foreach($line in Get-Content $movieFile)
    {
        $tokens = $line.Split("|")
        $movieById[$tokens[0]] = $tokens[1]
    }
    # Load movies user has already seen (rated)
    # into a hash table
    Write-Host "Reading rated movies" -ForegroundColor Green
    $ratedMovieIds = @{}
    foreach($line in Get-Content $userDataFile)
    {
        $tokens = $line.Split("`t")
        if($tokens[0] -eq $userId)
        {
            # Resolve the ID to the movie name
            $ratedMovieIds[$movieById[$tokens[1]]] = $tokens[2]
        }
    }
    # Read recommendations generated by Mahout
    Write-Host "Reading recommendations" -ForegroundColor Green
    $recommendations = @{}
    foreach($line in get-content $recommendationFile)
    {
        $tokens = $line.Split("`t")
        if($tokens[0] -eq $userId)
        {
            #Trim leading/treailing [] and split at ,
            $movieIdAndScores = $tokens[1].TrimStart("[").TrimEnd("]").Split(",")
            foreach($movieIdAndScore in $movieIdAndScores)
            {
                #Split at : and store title and score in a hash table
                $idAndScore = $movieIdAndScore.Split(":")
                $recommendations[$movieById[$idAndScore[0]]] = $idAndScore[1]
            }
            break
        }
    }

    Write-Host "Rated movies" -ForegroundColor Green
    Write-Host "---------------------------" -ForegroundColor Green
    $ratedFormat = @{Expression={$_.Name};Label="Movie";Width=40}, `
                   @{Expression={$_.Value};Label="Rating"}
    $ratedMovieIds | format-table $ratedFormat
    Write-Host "---------------------------" -ForegroundColor Green

    write-host "Recommended movies" -ForegroundColor Green
    Write-Host "---------------------------" -ForegroundColor Green
    $recommendationFormat = @{Expression={$_.Name};Label="Movie";Width=40}, `
                            @{Expression={$_.Value};Label="Score"}
    $recommendations | format-table $recommendationFormat

El siguiente es un ejemplo de la ejecución de la secuencia de comandos:

    PS C:\> show-recommendation.ps1 -userId 4 -userDataFile .\user-ratings.txt -movieFile .\moviedb.txt -recommendationFile .\output.txt

El resultado debería ser similar al siguiente:

    Reading movies descriptions
    Reading rated movies
    Reading recommendations
    Rated movies
    ---------------------------
    Movie                                    Rating
    -----                                    ------
    Devil's Own, The (1997)                  1
    Alien: Resurrection (1997)               3
    187 (1997)                               2
    (lines ommitted)

    ---------------------------
    Recommended movies
    ---------------------------

    Movie                                    Score
    -----                                    -----
    Good Will Hunting (1997)                 4.6504064
    Swingers (1996)                          4.6862745
    Wings of the Dove, The (1997)            4.6666665
    People vs. Larry Flynt, The (1996)       4.834559
    Everyone Says I Love You (1996)          4.707071
    Secrets & Lies (1996)                    4.818182
    That Thing You Do! (1996)                4.75
    Grosse Pointe Blank (1997)               4.8235292
    Donnie Brasco (1997)                     4.6792455
    Lone Star (1996)                         4.7099237  

##<a name="classify"></a>Clasificar datos mediante el uso de la línea de comandos de Hadoop

Uno de los métodos de clasificación disponibles con Mahout es crear un [bosque aleatorio][forest]. Se trata de un proceso de varios pasos que implica el uso de datos de formación para generar árboles de decisión, que se utilizan para clasificar los datos. Usa la clase __org.apache.mahout.classifier.df.tools.Describe__ proporcionada por Mahout. Actualmente se debe ejecutar mediante el uso de la línea de comandos de Hadoop.

###<a name="load-the-data"></a>Cargar los datos

1. Descargue los siguientes archivos desde [el conjunto de datos NSL KDD](http://nsl.cs.unb.ca/NSL-KDD/).

  * [KDDTrain +. ARFF](http://nsl.cs.unb.ca/NSL-KDD/KDDTrain+.arff): el archivo de formación

  * [KDDTest +. ARFF](http://nsl.cs.unb.ca/NSL-KDD/KDDTest+.arff): los datos de prueba

2. Abra cada archivo y quitar las líneas en la parte superior que comiencen con '@', y, a continuación, guarde los archivos. Si no se eliminan, recibirá mensajes de error cuando usa estos datos con Mahout.

2. Cargue los archivos __a/datos de ejemplo__. Puede hacerlo mediante el siguiente script. Reemplazar __NOMBREDECLÚSTER__ con el nombre del clúster HDInsight. Reemplazar el nombre de archivo con el nombre fo el archivo que va a cargar.

        #Get the cluster info so we can get the resource group, storage, etc.
        $clusterName="CLUSTERNAME"
        $fileToUpload="FILENAME"
        $blobPath="example/data/FILENAME"
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
            
        Set-AzureStorageBlobContent `
            -File $fileToUpload `
            -Blob $blobPath `
            -Container $container `
            -Context $context

###<a name="run-the-job"></a>Ejecutar el trabajo

1. Este trabajo requiere la línea de comandos de Hadoop. Habilitar Escritorio remoto para el clúster HDInsight y luego conectarse siguiendo las instrucciones en [conectarse a clústeres HDInsight con RDP](hdinsight-administer-use-management-portal.md#rdp).

3. Después de conectar, utilice el icono de __línea de comandos de Hadoop__ para abrir la línea de comandos de Hadoop:

    ![cli hadoop][hadoopcli]

3. Use el comando siguiente para generar el descriptor de archivo (__KDDTrain + .info__), que usa a Mahout.

        hadoop jar "c:/apps/dist/mahout-0.9.0.2.2.9.1-8/examples/target/mahout-examples-0.9.0.2.2.9.1-8-job.jar" org.apache.mahout.classifier.df.tools.Describe -p "wasbs:///example/data/KDDTrain+.arff" -f "wasbs:///example/data/KDDTrain+.info" -d N 3 C 2 N C 4 N C 8 N 2 C 19 N L

    La `N 3 C 2 N C 4 N C 8 N 2 C 19 N L` describe los atributos de los datos en el archivo. Por ejemplo, L indica una etiqueta.

4. Crear un bosque de árboles de decisión mediante el siguiente comando:

        hadoop jar c:/apps/dist/mahout-0.9.0.2.2.9.1-8/examples/target/mahout-examples-0.9.0.2.2.9.1-8-job.jar org.apache.mahout.classifier.df.mapreduce.BuildForest -Dmapred.max.split.size=1874231 -d wasbs:///example/data/KDDTrain+.arff -ds wasbs:///example/data/KDDTrain+.info -sl 5 -p -t 100 -o nsl-forest

    El resultado de esta operación se almacena en el directorio de __bosque nsl__ , que se encuentra en el almacenamiento de su clúster de HDInsight en __ wasbs://user/&lt;nombre de usuario > / nsl-bosque/nsl-forest.seq. La &lt;nombre de usuario > es el nombre de usuario que ha usado para la sesión de escritorio remoto. Este archivo no es legible por personas.

5. Probar el bosque clasificar el conjunto de datos __KDDTest + .arff__ . Utilice el siguiente comando:

        hadoop jar c:/apps/dist/mahout-0.9.0.2.2.9.1-8/examples/target/mahout-examples-0.9.0.2.2.9.1-8-job.jar org.apache.mahout.classifier.df.mapreduce.TestForest -i wasbs:///example/data/KDDTest+.arff -ds wasbs:///example/data/KDDTrain+.info -m nsl-forest -a -mr -o wasbs:///example/data/predictions

    Este comando devuelve información de resumen sobre el proceso de clasificación similar al siguiente:

        14/07/02 14:29:28 INFO mapreduce.TestForest:

        =======================================================
        Summary
        -------------------------------------------------------
        Correctly Classified Instances          :      17560       77.8921%
        Incorrectly Classified Instances        :       4984       22.1079%
        Total Classified Instances              :      22544

        =======================================================
        Confusion Matrix
        -------------------------------------------------------
        a       b       <--Classified as
        9437    274      |  9711        a     = normal
        4710    8123     |  12833       b     = anomaly

        =======================================================
        Statistics
        -------------------------------------------------------
        Kappa                                       0.5728
        Accuracy                                   77.8921%
        Reliability                                53.4921%
        Reliability (standard deviation)            0.4933

  Este trabajo también genera un archivo ubicado en __wasbs:///example/data/predictions/KDDTest+.arff.out__. Sin embargo, este archivo no es legible por personas.

> [AZURE.NOTE] Mahout trabajos no sobrescriben archivos. Si desea volver a ejecutar estas tareas, debe eliminar los archivos que se crearon trabajos anteriores.

##<a name="troubleshooting"></a>Solución de problemas

###<a name="install"></a>Instalar Mahout

Mahout se instala en HDInsight 3.1 clústeres y se puede instalar manualmente en clústeres HDInsight 3.0 o 2.1 HDInsight con los pasos siguientes:

1. La versión de Mahout usar depende de la versión de HDInsight de clúster. Puede encontrar la versión de clúster mediante la visualización de las propiedades para el clúster en el portal de Azure.

  * __Para HDInsight 2.1__, puede descargar un archivo de Java Archive (JAR) que contiene [Mahout 0,9](http://repo2.maven.org/maven2/org/apache/mahout/mahout-core/0.9/mahout-core-0.9-job.jar).

  * __Para HDInsight 3.0__, debe [crear Mahout del origen] [ build] y especifique la versión de Hadoop proporcionada por HDInsight. Instalar los requisitos previos enumerados en la página Crear, descargar el código fuente y, a continuación, utilice el comando siguiente para crear los archivos de jar Mahout:

            mvn -Dhadoop2.version=2.2.0 -DskipTests clean package

        After the build completes, you can find the JAR file at __mahout\mrlegacy\target\mahout-mrlegacy-1.0-SNAPSHOT-job.jar__.

        > [AZURE.NOTE] Cuando se publica Mahout 1.0, podrá utilizar los paquetes prediseñados con HDInsight 3.0.

2. Cargue el archivo jar a __jars/tarros de ejemplo__ en el almacenamiento predeterminado para el clúster. Reemplace NOMBREDECLÚSTER en la siguiente secuencia de comandos con el nombre de su clúster HDInsight y nombre de archivo con la ruta de acceso al archivo __mahout-coure-0,9-job.jar__ ...

        #Get the cluster info so we can get the resource group, storage, etc.
        $clusterName = "CLUSTERNAME"
        $fileToUpload = "FILENAME"
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
            
        Set-AzureStorageBlobContent `
            -File $fileToUpload `
            -Blob "example/jars/mahout-core-0.9-job.jar" `
            -Container $container `
            -Context $context

###<a name="cannot-overwrite-files"></a>No se puede sobrescribir archivos

Hacer de trabajos Mahout no limpiar los archivos temporales que se crearon durante el proceso. Además, las tareas no sobrescribirá un archivo de salida existente.

Para evitar errores cuando se ejecutan trabajos Mahout, elimine los archivos temporales y de salida entre ejecuciones o utilizar nombres únicos directorio temporal y de salida.

###<a name="cannot-find-the-jar-file"></a>No se puede encontrar el archivo JAR

3.1 HDInsight clústeres incluyen a Mahout. La ruta de acceso y nombre de archivo incluyen el número de versión de Mahout que esté instalada en el clúster. La secuencia de comandos de ejemplo de Windows PowerShell en este tutorial utiliza una ruta de acceso es válida a partir de noviembre de 2015, pero el número de versión se cambiará en futuras actualizaciones HDInsight. Para determinar la ruta de acceso actual al archivo JAR Mahout para el clúster, utilice el siguiente comando de Windows PowerShell y, a continuación, modifique la secuencia de comandos para hacer referencia a la ruta de acceso del archivo que se devuelve:

    Use-AzureRmHDInsightCluster -ClusterName $clusterName
    #Get the cluster info so we can get the resource group, storage, etc.
        $clusterInfo = Get-AzureRmHDInsightCluster -ClusterName $clusterName
        $resourceGroup = $clusterInfo.ResourceGroup
        $storageAccountName=$clusterInfo.DefaultStorageAccount.split('.')[0]
        $container=$clusterInfo.DefaultStorageContainer
        $storageAccountKey=(Get-AzureRmStorageAccountKey `
            -Name $storageAccountName `
        -ResourceGroupName $resourceGroup)[0].Value
    Invoke-AzureRmHDInsightHiveJob `
            -StatusFolder "wasbs:///example/statusout" `
            -DefaultContainer $container `
            -DefaultStorageAccountName $storageAccountName `
            -DefaultStorageAccountKey $storageAccountKey `
            -Query '!${env:COMSPEC} /c dir /b /s ${env:MAHOUT_HOME}\examples\target\*-job.jar'

###<a name="nopowershell"></a>Clases que no funcionan con Windows PowerShell

Tareas de Mahout que usan las clases siguientes devuelven una gran variedad de mensajes de error si se usan desde Windows PowerShell:

* org.apache.mahout.utils.clustering.ClusterDumper
* org.apache.mahout.utils.SequenceFileDumper
* org.apache.mahout.utils.vectors.lucene.Driver
* org.apache.mahout.utils.vectors.arff.Driver
* org.apache.mahout.text.WikipediaToSequenceFile
* org.apache.mahout.clustering.streaming.tools.ResplitSequenceFiles
* org.apache.mahout.clustering.streaming.tools.ClusterQualitySummarizer
* org.apache.mahout.classifier.sgd.TrainLogistic
* org.apache.mahout.classifier.sgd.RunLogistic
* org.apache.mahout.classifier.sgd.TrainAdaptiveLogistic
* org.apache.mahout.classifier.sgd.ValidateAdaptiveLogistic
* org.apache.mahout.classifier.sgd.RunAdaptiveLogistic
* org.apache.mahout.classifier.sequencelearning.hmm.BaumWelchTrainer
* org.apache.mahout.classifier.sequencelearning.hmm.ViterbiEvaluator
* org.apache.mahout.classifier.sequencelearning.hmm.RandomSequenceGenerator
* org.apache.mahout.classifier.df.tools.Describe

Ejecutar trabajos que utilizan estas clases, conectan al clúster HDInsight y ejecutan los trabajos mediante el uso de la línea de comandos de Hadoop. Ver [datos de clasificar mediante la línea de comandos de Hadoop](#classify) para obtener un ejemplo.

##<a name="next-steps"></a>Pasos siguientes

Ahora que ha aprendido a usar a Mahout, descubra otras formas de trabajar con datos en HDInsight:

* [Sección con HDInsight](hdinsight-use-hive.md)
* [Cerdo con HDInsight](hdinsight-use-pig.md)
* [MapReduce con HDInsight](hdinsight-use-mapreduce.md)

[build]: http://mahout.apache.org/developers/buildingmahout.html
[aps]: ../powershell-install-configure.md
[movielens]: http://grouplens.org/datasets/movielens/
[100k]: http://files.grouplens.org/datasets/movielens/ml-100k.zip
[getstarted]: hdinsight-hadoop-linux-tutorial-get-started.md
[upload]: hdinsight-upload-data.md
[ml]: http://en.wikipedia.org/wiki/Machine_learning
[forest]: http://en.wikipedia.org/wiki/Random_forest
[management]: https://manage.windowsazure.com/
[enableremote]: ./media/hdinsight-mahout/enableremote.png
[connect]: ./media/hdinsight-mahout/connect.png
[hadoopcli]: ./media/hdinsight-mahout/hadoopcli.png
[tools]: https://github.com/Blackmist/hdinsight-tools
 
