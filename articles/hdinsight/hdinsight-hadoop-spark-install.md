<properties
    pageTitle="Usar la acción de secuencia de comandos para instalar el motor en clúster de Hadoop | Microsoft Azure"
    description="Obtenga información sobre cómo personalizar un clúster HDInsight con motor con la acción de secuencia de comandos."
    services="hdinsight"
    documentationCenter=""
    authors="nitinme"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="hdinsight"
    ms.workload="big-data"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="02/05/2016"
    ms.author="nitinme"/>

# <a name="install-and-use-spark-on-hdinsight-hadoop-clusters-using-script-action"></a>Instalar y usar el motor en clústeres HDInsight Hadoop con la acción de secuencia de comandos

> [AZURE.IMPORTANT] Ya no se utiliza en este artículo. HDInsight proporciona ahora motor como un tipo de clúster de primera clase para clústeres basados en Windows, lo que significa que ahora puede directamente crear un clúster de motor sin modificar un clúster de Hadoop con la acción de secuencia de comandos. Utilizando el tipo de clúster de motor, obtendrá un clúster de versión 3.2 HDInsight con motor versión 1.3.1.  Para instalar diferentes versiones de motor, puede usar la acción de secuencia de comandos. HDInsight proporciona una secuencia de comandos de acción de secuencia de comandos de ejemplo.

Obtenga información sobre cómo instalar el motor en Windows según HDInsight con la acción de secuencia de comandos, y cómo ejecutar consultas de motor en HDInsight clústeres.


**Artículos relacionados**

- [Crear Hadoop clústeres en HDInsight](hdinsight-provision-clusters.md): información general sobre la creación de clústeres HDInsight.

- [Introducción a motor Apache en HDInsight](hdinsight-apache-spark-jupyter-spark-sql.md): crear un clúster HDInsight Spark.

- [Personalizar clúster HDInsight con la acción de secuencia de comandos][hdinsight-cluster-customize]: información general sobre la personalización de clústeres HDInsight con la acción de secuencia de comandos.

- [Acción de secuencia de comandos con el desarrollo de secuencias de comandos para HDInsight](hdinsight-hadoop-script-actions.md).

## <a name="what-is-spark"></a>¿Qué es el motor?

<a href="http://spark.apache.org/docs/latest/index.html" target="_blank">Motor de Apache</a> es un paralelo Abrir origen procesamiento framework que admita el procesamiento en memoria para mejorar el rendimiento de aplicaciones de análisis de datos grande. Capacidades de cálculo en la memoria del motor que sea una buena opción para algoritmos iterativos en cálculos de aprendizaje y gráfico de equipo.

Motor también puede utilizarse para realizar el procesamiento de datos convencional basada en disco. Motor mejora el marco MapReduce tradicional evitando la escritura en disco en las fases intermedias. Además, el motor es compatible con el almacenamiento de blobs de Windows Azure y de sistema de archivos distribuido Hadoop (HDFS) para procesar fácilmente los datos existentes a través del motor.

Este tema proporciona instrucciones sobre cómo personalizar un clúster HDInsight para instalar el motor.

## <a name="install-spark-using-the-azure-portal"></a>Instalar el motor con el Portal de Azure

Una secuencia de comandos de ejemplo para instalar el motor en un clúster de HDInsight está disponible desde un blob de solo lectura almacenamiento de Azure en [https://hdiconfigactions.blob.core.windows.net/sparkconfigactionv03/spark-installer-v03.ps1](https://hdiconfigactions.blob.core.windows.net/sparkconfigactionv03/spark-installer-v03.ps1). Puede instalar esta secuencia de comandos motor 1.2.0 o motor 1.0.2 según la versión de clúster HDInsight que crear.

- Si utiliza la secuencia de comandos al crear un clúster de **3,2 HDInsight** , instala **motor 1.2.0**.
- Si utiliza la secuencia de comandos al crear un clúster de **HDInsight 3.1** , instala **motor 1.0.2**.

Puede modificar esta secuencia de comandos o crear su propio script para instalar otras versiones de motor.

> [AZURE.NOTE] La secuencia de comandos sólo funciona con HDInsight 3.1 y 3.2 clústeres. Para obtener más información sobre versiones de clúster HDInsight, consulte [versiones de clúster HDInsight](hdinsight-component-versioning.md).

1. Empezar a crear un clúster mediante la opción **Crear personalizado** , como se describe en [Hadoop crear clústeres en HDInsight](hdinsight-provision-clusters.md#portal). Seleccione la versión de clúster dependiendo de lo siguiente:

    - Si desea instalar el **motor 1.2.0**, cree un clúster de 3,2 HDInsight.
    - Si desea instalar el **motor 1.0.2**, cree un clúster de HDInsight 3.1.


2. En la página de **Acciones de secuencia de comandos** del asistente, haga clic en **Agregar acción de secuencia de comandos** para proporcionar información detallada sobre la acción de secuencia de comandos, tal como se muestra a continuación:

    ![Acción de secuencia de comandos de uso para personalizar un clúster] (./media/hdinsight-hadoop-spark-install/HDI.CustomProvision.Page6.png "Acción de secuencia de comandos de uso para personalizar un clúster")

    <table border='1'>
        <tr><th>(Propiedad)</th><th>Valor</th></tr>
        <tr><td>Nombre</td>
            <td>Especifique un nombre para la acción de secuencia de comandos. Por ejemplo, <b>Instalar el motor</b>.</td></tr>
        <tr><td>URI de secuencia de comandos</td>
            <td>Especificar el identificador uniforme de recursos (URI) a la secuencia de comandos que se invoca para personalizar el clúster. Por ejemplo, <i>https://hdiconfigactions.blob.core.windows.net/sparkconfigactionv03/spark-installer-v03.ps1</i></td></tr>
        <tr><td>Tipo de nodo</td>
            <td>Especifique los nodos en que se ejecuta la secuencia de comandos de personalización. Puede elegir <b>todos los nodos</b>, <b>solo los nodos de cabeza</b>o <b>solo los nodos de trabajo</b>.
        <tr><td>Parámetros</td>
            <td>Especificar los parámetros, si es necesario la secuencia de comandos. La secuencia de comandos para instalar el motor no requiere los parámetros de modo que puede dejarlo en blanco.</td></tr>
    </table>

    Puede agregar más de una acción de secuencia de comandos para instalar varios componentes en el clúster. Después de haber agregado las secuencias de comandos, haga clic en la marca de verificación para empezar a crear el clúster.

También puede usar la secuencia de comandos para instalar el motor en HDInsight con Azure PowerShell o el SDK de .NET HDInsight. Más adelante en este tema se proporcionan instrucciones para estos procedimientos.

## <a name="use-spark-in-hdinsight"></a>Motor de uso en HDInsight
Motor proporciona las API de Scala, Python y Java. También puede usar el shell de motor interactivo para ejecutar consultas de motor. Esta sección proporciona instrucciones sobre cómo usar los métodos diferentes para trabajar con motor:

- [Usar el shell de motor para ejecutar consultas interactivas](#sparkshell)
- [Usar el shell de motor para ejecutar consultas SQL de motor](#sparksql)
- [Usar un programa de Scala independiente](#standalone)

###<a name="sparkshell"></a>Usar el shell de motor para ejecutar consultas interactivas
Realice los pasos siguientes para ejecutar consultas de motor desde un shell interactivo de motor. En esta sección, se ejecuta una consulta de motor en un archivo de datos de ejemplo (/ example/data/gutenberg/davinci.txt) que está disponible en clústeres HDInsight de forma predeterminada.

1. Desde el portal de Azure, habilite Escritorio remoto para el clúster creado con motor instalado y remoto, a continuación, en el clúster. Para obtener instrucciones, vea [conectarse a clústeres HDInsight con RDP](hdinsight-administer-use-management-portal.md#rdp).

2. En la sesión de protocolo de escritorio remoto (RDP), en el escritorio, abra la línea de comandos de Hadoop (desde un acceso directo de escritorio) y vaya a la ubicación donde está instalado el motor; Por ejemplo, **C:\apps\dist\spark-1.2.0**.


3. Ejecute el comando siguiente para iniciar la consola de motor:

         .\bin\spark-shell --master yarn

    Cuando finalice el comando, obtendrá una Scala del mensaje:

         scala>

5. En el símbolo de Scala, escriba la consulta de motor que se muestra a continuación. Esta consulta cuenta la repetición de cada palabra en el archivo davinci.txt que está disponible en la/ejemplo / / gutenberg o ubicación de datos en el almacenamiento de blobs de Windows Azure asociado con el clúster.

        val file = sc.textFile("/example/data/gutenberg/davinci.txt")
        val counts = file.flatMap(line => line.split(" ")).map(word => (word, 1)).reduceByKey(_ + _)
        counts.toArray().foreach(println)

6. El resultado debe ser similar a lo siguiente:

    ![Resultado de la ejecución Scala shell interactivo en un clúster de HDInsight](./media/hdinsight-hadoop-spark-install/hdi-scala-interactive.png)


7. Escriba: q para salir de Scala.

        :q

###<a name="sparksql"></a>Usar el shell de motor para ejecutar consultas SQL de motor

Motor SQL le permite usar motor para ejecutar consultas relacionales expresadas en lenguaje de consulta estructurado (SQL), HiveQL o Scala. En esta sección, observamos con motor para ejecutar una consulta de sección en una tabla de la sección de ejemplo. La tabla subárbol utilizada en esta sección (llamada **hivesampletable**) está disponible de forma predeterminada al crear un clúster.

>[AZURE.NOTE] En el ejemplo siguiente se creó con **despierten 1.2.0**, que se instala si ejecuta la acción de secuencia de comandos al crear el clúster de 3,2 HDInsight.

1. Desde el portal de Azure, habilite Escritorio remoto para el clúster creado con motor instalado y remoto, a continuación, en el clúster. Para obtener instrucciones, vea [conectarse a clústeres HDInsight con RDP](hdinsight-administer-use-management-portal.md#rdp).

2. En la sesión RDP, desde el escritorio, abra la línea de comandos de Hadoop (desde un acceso directo de escritorio) y vaya a la ubicación donde está instalado el motor; Por ejemplo, **C:\apps\dist\spark-1.2.0**.


3. Ejecute el comando siguiente para iniciar la consola de motor:

         .\bin\spark-shell --master yarn

    Cuando finalice el comando, obtendrá una Scala del mensaje:

         scala>

4. En el símbolo de Scala, establezca el contexto de la sección. Esto es necesario para trabajar con las consultas de la sección con motor.

        val hiveContext = new org.apache.spark.sql.hive.HiveContext(sc)

    Tenga en cuenta que **sc** es el contexto de motor de predeterminado al iniciar la consola de motor.

5. Ejecutar una consulta de la sección mediante el contexto de la sección e imprimir el resultado de la consola. La consulta recupera datos en dispositivos de una marca y limita el número de registros recuperados a 20.

        hiveContext.sql("""SELECT * FROM hivesampletable WHERE devicemake LIKE "HTC%" LIMIT 20""").collect().foreach(println)

6. Debe ver un resultado similar al siguiente:

    ![Resultado de la ejecución de SQL de motor en un clúster de HDInsight](./media/hdinsight-hadoop-spark-install/hdi-spark-sql.png)

7. Escriba: q para salir de Scala.

        :q

### <a name="standalone"></a>Usar un programa de Scala independiente

En esta sección, escribimos una aplicación de Scala que cuenta el número de líneas que contienen las letras 'a' y 'b' en un archivo de datos de ejemplo (/ example/data/gutenberg/davinci.txt) que está disponible en clústeres HDInsight de forma predeterminada. Para escribir y usar un programa de Scala independiente con un diseño personalizado con la instalación del motor, debe realizar los siguientes pasos:

- Escribir un programa Scala
- Generar el programa Scala para obtener el archivo .jar
- Ejecutar el trabajo en el clúster

#### <a name="write-a-scala-program"></a>Escribir un programa Scala
En esta sección, escribe un programa de Scala que cuenta el número de líneas que contienen 'a' y 'b' en el archivo de datos de ejemplo.

1. Abra un editor de texto y pegue el código siguiente:


        /* SimpleApp.scala */
        import org.apache.spark.SparkContext
        import org.apache.spark.SparkContext._
        import org.apache.spark.SparkConf

        object SimpleApp {
          def main(args: Array[String]) {
            val logFile = "/example/data/gutenberg/davinci.txt"         //Location of the sample data file on Azure Blob storage
            val conf = new SparkConf().setAppName("SimpleApplication")
            val sc = new SparkContext(conf)
            val logData = sc.textFile(logFile, 2).cache()
            val numAs = logData.filter(line => line.contains("a")).count()
            val numBs = logData.filter(line => line.contains("b")).count()
            println("Lines with a: %s, Lines with b: %s".format(numAs, numBs))
          }
        }

2. Guarde el archivo con el nombre **SimpleApp.scala**.

#### <a name="build-the-scala-program"></a>Generar el programa Scala
En esta sección, use la <a href="http://www.scala-sbt.org/0.13/docs/index.html" target="_blank">Herramienta de compilación Simple</a> (o sbt) para generar el programa Scala. SBT requiere Java 1,6 o posterior, así que asegúrese de que tiene la versión correcta de Java instalada antes de continuar con esta sección.

1. Instale sbt de http://www.scala-sbt.org/0.13/tutorial/Installing-sbt-on-Windows.html.
2. Cree una carpeta denominada **SimpleScalaApp**y dentro de esta carpeta, cree un archivo llamado **simple.sbt**. Se trata de un archivo de configuración que contiene información sobre la versión de Scala, dependencias de biblioteca, etcetera. Pegue lo siguiente en el archivo simple.sbt y guárdelo:


        name := "SimpleApp"

        version := "1.0"

        scalaVersion := "2.10.4"

        libraryDependencies += "org.apache.spark" %% "spark-core" % "1.2.0"



    >[AZURE.NOTE] Asegúrese de que mantener las líneas en blanco en el archivo.


3. En la carpeta **SimpleScalaApp** , cree una estructura de directorio **\src\main\scala** y pegue el programa Scala (**SimpleApp.scala**) que creó anteriormente en la carpeta \src\main\scala.
4. Abra un símbolo del sistema, desplácese hasta el directorio SimpleScalaApp y escriba el siguiente comando:


        sbt package


    Una vez que se compila la aplicación, verá un archivo de **simpleapp_2.10 1.0.jar** creado en el directorio **\target\scala-2.10** en la carpeta raíz del SimpleScalaApp.


#### <a name="run-the-job-on-the-cluster"></a>Ejecutar el trabajo en el clúster
En esta sección, remoto en el clúster que tiene el motor instaló y, a continuación, copie la carpeta de destino del proyecto SimpleScalaApp. A continuación, use el comando **Enviar motor** a enviar el trabajo en el clúster.

1. Remoto en el clúster que tiene instalado el motor. Desde el equipo donde escribió y genera el programa SimpleApp.scala, copie la carpeta **SimpleScalaApp\target** y péguelo en una ubicación en el clúster.
2. En la sesión RDP, desde el escritorio, abra la línea de comandos de Hadoop y navegue hasta la ubicación donde se pega la carpeta de **destino** .
3. Escriba el comando siguiente para ejecutar el programa de SimpleApp.scala:


        C:\apps\dist\spark-1.2.0\bin\spark-submit --class "SimpleApp" --master local target/scala-2.10/simpleapp_2.10-1.0.jar

4. Cuando finalice el programa, el resultado se muestra en la consola.


        Lines with a: 21374, Lines with b: 11430

## <a name="install-spark-using-azure-powershell"></a>Instalar el motor con PowerShell de Azure

En esta sección, usamos el cmdlet **<a href = "http://msdn.microsoft.com/library/dn858088.aspx" target="_blank">AzureHDInsightScriptAction agregar</a>** invocar secuencias de comandos mediante acción de secuencia de comandos para personalizar un clúster. Antes de continuar, asegúrese de que ha instalado y configurado Azure PowerShell. Para obtener información sobre cómo configurar una estación de trabajo para ejecutar los cmdlets de PowerShell de Azure para HDInsight, consulte [instalar y configurar Azure PowerShell](../powershell-install-configure.md).

Realice los pasos siguientes:

1. Abra una ventana de PowerShell de Azure y declarar las variables siguientes:

        # Provide values for these variables
        $subscriptionName = "<SubscriptionName>"        # Name of the Azure subscription
        $clusterName = "<HDInsightClusterName>"         # HDInsight cluster name
        $storageAccountName = "<StorageAccountName>"    # Azure Storage account that hosts the default container
        $storageAccountKey = "<StorageAccountKey>"      # Key for the Storage account
        $containerName = $clusterName
        $location = "<MicrosoftDataCenter>"             # Location of the HDInsight cluster. It must be in the same data center as the Storage account.
        $clusterNodes = <ClusterSizeInNumbers>          # Number of nodes in the HDInsight cluster
        $version = "<HDInsightClusterVersion>"          # For example, "3.2"

2. Especificar los valores de configuración como nodos en el clúster y el almacenamiento predeterminado que se usará.

        # Specify the configuration options
        Select-AzureSubscription $subscriptionName
        $config = New-AzureHDInsightClusterConfig -ClusterSizeInNodes $clusterNodes
        $config.DefaultStorageAccount.StorageAccountName="$storageAccountName.blob.core.windows.net"
        $config.DefaultStorageAccount.StorageAccountKey=$storageAccountKey
        $config.DefaultStorageAccount.StorageContainerName=$containerName

3. Usar el cmdlet **AzureHDInsightScriptAction agregar** para agregar una acción de secuencia de comandos a la configuración de clúster. Más adelante, cuando se crea el clúster, obtiene ejecuta la acción de secuencia de comandos.

        # Add a script action to the cluster configuration
        $config = Add-AzureHDInsightScriptAction -Config $config -Name "Install Spark" -ClusterRoleCollection HeadNode -Uri https://hdiconfigactions.blob.core.windows.net/sparkconfigactionv03/spark-installer-v03.ps1

    **Agregar AzureHDInsightScriptAction** cmdlet acepta los siguientes parámetros:

    <table style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse;">
    <tr>
    <th style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; width:90px; padding-left:5px; padding-right:5px;">Parámetro</th>
    <th style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; width:550px; padding-left:5px; padding-right:5px;">Definición</th></tr>
    <tr>
    <td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Config</td>
    <td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px; padding-right:5px;">El objeto de configuración de qué script se agrega información de la acción.</td></tr>
    <tr>
    <td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Nombre</td>
    <td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Nombre de la acción de secuencia de comandos.</td></tr>
    <tr>
    <td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">ClusterRoleCollection</td>
    <td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Especifica los nodos en que se ejecuta la secuencia de comandos de personalización. Los valores válidos son HeadNode (para instalar en el nodo principal) o DataNode (para instalar en todos los nodos de datos). Puede usar los valores de una o ambas.</td></tr>
    <tr>
    <td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">URI</td>
    <td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Especifica el URI a la secuencia de comandos que se ejecuta.</td></tr>
    <tr>
    <td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Parámetros</td>
    <td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Parámetros requeridos por la secuencia de comandos. La secuencia de comandos usado en este tema no requiere los parámetros y, por tanto, no puede ver este parámetro en el fragmento de código anterior.
    </td></tr>
    </table>

4. Por último, empiece a crear un clúster personalizado con motor instalado.  

        # Start creating a cluster with Spark installed
        New-AzureHDInsightCluster -Config $config -Name $clusterName -Location $location -Version $version

Cuando se le pida, escriba las credenciales para el clúster. Puede tardar varios minutos antes de crear el clúster.

## <a name="install-spark-using-powershell"></a>Instalar el motor con PowerShell

Consulte [clústeres personalizar HDInsight con la acción de secuencia de comandos](hdinsight-hadoop-customize-cluster.md#call_scripts_using_powershell).

## <a name="install-spark-using-net-sdk"></a>Instalar el motor con .NET SDK

Consulte [clústeres personalizar HDInsight con la acción de secuencia de comandos](hdinsight-hadoop-customize-cluster.md#call_scripts_using_azure_powershell).


## <a name="see-also"></a>Vea también

- [Crear Hadoop clústeres en HDInsight](hdinsight-provision-clusters.md): crear clústeres HDInsight.
- [Introducción a motor Apache en HDInsight](hdinsight-apache-spark-jupyter-spark-sql.md): Introducción a motor en HDInsight.
- [Personalizar clúster HDInsight con la acción de secuencia de comandos][hdinsight-cluster-customize]: personalizar clústeres HDInsight con la acción de secuencia de comandos.
- [Acción de secuencia de comandos con el desarrollo de secuencias de comandos para HDInsight](hdinsight-hadoop-script-actions.md): desarrollar secuencias de comandos de acción de secuencia de comandos.
- [Instalar R en clústeres HDInsight] [ hdinsight-install-r] proporciona instrucciones sobre cómo usar la personalización de clúster para instalar y usar R en clústeres HDInsight Hadoop. R es un idioma de código abierto y el entorno para las estadísticas. Proporciona cientos de funciones estadísticas integradas y su propio lenguaje de programación que combina aspectos de la programación funcional y orientado a objetos. También proporciona capacidades gráficas extensas.
- [Instalar Giraph en clústeres HDInsight](hdinsight-hadoop-giraph-install.md). Use la personalización de clúster para instalar Giraph en clústeres HDInsight Hadoop. Giraph permite realizar graph procesar mediante Hadoop y se pueden usar con HDInsight de Azure.
- [Instalar Solr en clústeres HDInsight](hdinsight-hadoop-solr-install.md). Use la personalización de clúster para instalar Solr en clústeres HDInsight Hadoop. Solr le permite realizar operaciones de búsqueda eficaz de los datos almacenados.

[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-install-r]: hdinsight-hadoop-r-scripts.md
[hdinsight-cluster-customize]: hdinsight-hadoop-customize-cluster.md
[powershell-install-configure]: powershell-install-configure.md
