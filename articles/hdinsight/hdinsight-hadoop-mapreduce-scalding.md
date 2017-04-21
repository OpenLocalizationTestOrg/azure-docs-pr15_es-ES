<properties
 pageTitle="Desarrollar operaciones MapReduce trabajos con Maven | Microsoft Azure"
 description="Obtenga información sobre cómo usar a Maven para crear un trabajo de operaciones MapReduce, a continuación, implementar y ejecutar el trabajo en un Hadoop en clúster HDInsight."
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
 ms.date="10/18/2016"
 ms.author="larryfr"/>

# <a name="develop-scalding-mapreduce-jobs-with-apache-hadoop-on-hdinsight"></a>Desarrollar operaciones MapReduce trabajos con Apache Hadoop en HDInsight

Operaciones es una biblioteca de Scala que hace que sea fácil crear Hadoop MapReduce trabajos. Ofrece una sintaxis concisa, así como la estrecha integración con Scala.

En este documento, obtenga información sobre cómo usar a Maven para crear un trabajo de word básicas recuento MapReduce escrito en Scalding. A continuación, aprenderá a implementar y ejecutar este trabajo en un clúster de HDInsight.

## <a name="prerequisites"></a>Requisitos previos

- **Suscripción de un Azure**. Vea la [versión de prueba gratuita de Azure obtener](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* **A Windows o Linux según Hadoop en clúster HDInsight**. Para obtener más información, vea [Hadoop basados en Linux de aprovisionamiento en HDInsight](hdinsight-hadoop-provision-linux-clusters.md) o [Hadoop basado en Windows de aprovisionamiento en HDInsight](hdinsight-provision-clusters.md) .

* **[Experto](http://maven.apache.org/)**

* **[Plataforma Java JDK](http://www.oracle.com/technetwork/java/javase/downloads/index.html) 7 o posterior**

## <a name="create-and-build-the-project"></a>Crear y generar el proyecto

1. Use el comando siguiente para crear un nuevo proyecto de experto en:

        mvn archetype:generate -DgroupId=com.microsoft.example -DartifactId=scaldingwordcount -DarchetypeGroupId=org.scala-tools.archetypes -DarchetypeArtifactId=scala-archetype-simple -DinteractiveMode=false

    Este comando se cree un nuevo directorio denominado **scaldingwordcount**y crear el scaffolding para una aplicación Scala.

2. En el directorio **scaldingwordcount** , abra el archivo **pom.xml** y reemplace el contenido con lo siguiente:

        <project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/maven-v4_0_0.xsd">
            <modelVersion>4.0.0</modelVersion>
            <groupId>com.microsoft.example</groupId>
            <artifactId>scaldingwordcount</artifactId>
            <version>1.0-SNAPSHOT</version>
            <name>${project.artifactId}</name>
            <properties>
            <maven.compiler.source>1.6</maven.compiler.source>
            <maven.compiler.target>1.6</maven.compiler.target>
            <encoding>UTF-8</encoding>
            </properties>
            <repositories>
            <repository>
                <id>conjars</id>
                <url>http://conjars.org/repo</url>
            </repository>
            <repository>
                <id>maven-central</id>
                <url>http://repo1.maven.org/maven2</url>
            </repository>
            </repositories>
            <dependencies>
            <dependency>
                <groupId>com.twitter</groupId>
                <artifactId>scalding-core_2.11</artifactId>
                <version>0.13.1</version>
            </dependency>
            <dependency>
                <groupId>org.apache.hadoop</groupId>
                <artifactId>hadoop-core</artifactId>
                <version>1.2.1</version>
                <scope>provided</scope>
            </dependency>
            </dependencies>
            <build>
            <sourceDirectory>src/main/scala</sourceDirectory>
            <plugins>
                <plugin>
                <groupId>org.scala-tools</groupId>
                <artifactId>maven-scala-plugin</artifactId>
                <version>2.15.2</version>
                <executions>
                    <execution>
                    <id>scala-compile-first</id>
                    <phase>process-resources</phase>
                    <goals>
                        <goal>add-source</goal>
                        <goal>compile</goal>
                    </goals>
                    </execution>
                </executions>
                </plugin>
                <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-shade-plugin</artifactId>
                <version>2.3</version>
                <configuration>
                    <transformers>
                    <transformer implementation="org.apache.maven.plugins.shade.resource.ApacheLicenseResourceTransformer">
                    </transformer>
                    </transformers>
                    <filters>
                    <filter>
                        <artifact>*:*</artifact>
                        <excludes>
                        <exclude>META-INF/*.SF</exclude>
                        <exclude>META-INF/*.DSA</exclude>
                        <exclude>META-INF/*.RSA</exclude>
                        </excludes>
                    </filter>
                    </filters>
                </configuration>
                <executions>
                    <execution>
                    <phase>package</phase>
                    <goals>
                        <goal>shade</goal>
                    </goals>
                    <configuration>
                        <transformers>
                        <transformer implementation="org.apache.maven.plugins.shade.resource.ManifestResourceTransformer">
                            <mainClass>com.twitter.scalding.Tool</mainClass>
                        </transformer>
                        </transformers>
                    </configuration>
                    </execution>
                </executions>
                </plugin>
            </plugins>
            </build>
        </project>

    Este archivo describe el proyecto, las dependencias y complementos. Estas son las entradas importantes:

    * **maven.Compiler.Source** y **maven.compiler.target**: establece la versión de Java para este proyecto

    * **repositorios**: los repositorios que contienen los archivos de dependencia utilizados por este proyecto

    * **operaciones de core_2.11** y **hadoop core**: este proyecto depende de Scalding y Hadoop paquetes principales

    * **complemento de scala experto**: complemento para compilar aplicaciones scala

    * **complemento de sombra maven**: complemento para crear sombreado jars (fat). Este complemento aplica filtros y transformaciones; specificially:

        * **filtros**: los filtros aplicados modificar la información de metadatos que se incluye en el archivo jar. Para evitar excepciones en tiempo de ejecución de inicio de sesión, se excluyen los distintos archivos de firma que se incluya con dependencias.

        * **ejecuciones**: la configuración de ejecución de la fase de paquete especifica la clase **com.twitter.scalding.Tool** como la clase principal para el paquete. Sin esto, necesitará especificar com.twitter.scalding.Tool, así como la clase que contiene la lógica de aplicación, cuando se ejecuta el trabajo con el comando hadoop.

3. Eliminar el directorio **src o probar** , como no creará pruebas con este ejemplo.

4. Abra el archivo **src/main/scala/com/microsoft/example/App.scala** y reemplace el contenido con lo siguiente:

        package com.microsoft.example

        import com.twitter.scalding._

        class WordCount(args : Args) extends Job(args) {
            // 1. Read lines from the specified input location
            // 2. Extract individual words from each line
            // 3. Group words and count them
            // 4. Write output to the specified output location
            TextLine(args("input"))
            .flatMap('line -> 'word) { line : String => tokenize(line) }
            .groupBy('word) { _.size }
            .write(Tsv(args("output")))

            //Tokenizer to split sentance into words
            def tokenize(text : String) : Array[String] = {
            text.toLowerCase.replaceAll("[^a-zA-Z0-9\\s]", "").split("\\s+")
            }
        }

    Implementa un trabajo de recuento básicas de word.

5. Guarde y cierre los archivos.

6. Use el comando siguiente desde el directorio **scaldingwordcount** para generar y empaquetar la aplicación:

        mvn package

    Una vez completado este trabajo, encontrará el paquete que contiene la aplicación WordCount en **target/scaldingwordcount-1.0-SNAPSHOT.jar**.

## <a name="run-the-job-on-a-linux-based-cluster"></a>Ejecutar el trabajo en un clúster de Linux

> [AZURE.NOTE] Los pasos siguientes use SSH y el comando Hadoop. Para otros métodos de MapReduce trabajos en ejecución, vea [Usar MapReduce en Hadoop en HDInsight](hdinsight-use-mapreduce.md).

1. Usar el comando siguiente para cargar el paquete en el clúster HDInsight:

        scp target/scaldingwordcount-1.0-SNAPSHOT.jar username@clustername-ssh.azurehdinsight.net:

    Esto copiará los archivos del sistema local en el nodo principal.

    > [AZURE.NOTE] Si utiliza una contraseña para proteger su cuenta SSH, se le pedirá la contraseña. Si utiliza una clave SSH, tendrá que usar la `-i` parámetro y la ruta de acceso a la clave privada. Por ejemplo,`scp -i /path/to/private/key target/scaldingwordcount-1.0-SNAPSHOT.jar username@clustername-ssh.azurehdinsight.net:.`

2. Use el comando siguiente para conectar con el nodo principal de clúster:

        ssh username@clustername-ssh.azurehdinsight.net

    > [AZURE.NOTE] Si utiliza una contraseña para proteger su cuenta SSH, se le pedirá la contraseña. Si utiliza una clave SSH, tendrá que usar la `-i` parámetro y la ruta de acceso a la clave privada. Por ejemplo,`ssh -i /path/to/private/key username@clustername-ssh.azurehdinsight.net`

3. Una vez conectado al nodo principal, use el comando siguiente para ejecutar el trabajo de recuento de word

        yarn jar scaldingwordcount-1.0-SNAPSHOT.jar com.microsoft.example.WordCount --hdfs --input wasbs:///example/data/gutenberg/davinci.txt --output wasbs:///example/wordcountout

    Esta opción ejecuta la clase WordCount implementado anteriormente. `--hdfs`indica que el trabajo usar HDFS. `--input`Especifica el archivo de texto de entrada, mientras que `--output` especifica la ubicación de salida.

4. Una vez completada la tarea, utilice la siguiente para ver el resultado.

        hdfs dfs -text wasbs:///example/wordcountout/*

    Esta opción mostrará información similar a la siguiente:

        writers 9
        writes  18
        writhed 1
        writing 51
        writings        24
        written 208
        writtenthese    1
        wrong   11
        wrongly 2
        wrongplace      1
        wrote   34
        wrotefootnote   1
        wrought 7

## <a name="run-the-job-on-a-windows-based-cluster"></a>Ejecutar el trabajo en un clúster basado en Windows

Los siguientes pasos usar Windows PowerShell. Para otros métodos de MapReduce trabajos en ejecución, vea [Usar MapReduce en Hadoop en HDInsight](hdinsight-use-mapreduce.md).

[AZURE.INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]

2. Inicie Azure PowerShell e inicie sesión en su cuenta de Azure. Después de proporcionar sus credenciales, el comando devuelve información acerca de su cuenta.

        Add-AzureRMAccount

        Id                             Type       ...
        --                             ----
        someone@example.com            User       ...

3. Si tiene varias suscripciones, proporcione el identificador de la suscripción que desea usar para su implementación.

        Select-AzureRMSubscription -SubscriptionID <YourSubscriptionId>

    > [AZURE.NOTE] Puede usar `Get-AzureRMSubscription` para obtener una lista de todas las suscripciones asociadas con su cuenta, que incluye el identificador de la suscripción para cada uno.

4. Use la siguiente secuencia de comandos para cargar y ejecutar el trabajo WordCount. Reemplazar `CLUSTERNAME` con el nombre de su HDInsight de clúster y asegúrese de que `$fileToUpload` es la ruta al archivo __scaldingwordcount-1.0-SNAPSHOT.jar__ correcta.

        #Cluster name, file to be uploaded, and where to upload it
        $clustername = Read-Host -Prompt "Enter the HDInsight cluster name"
        $fileToUpload = Read-Host -Prompt "Enter the path to the scaldingwordcount-1.0-SNAPSHOT.jar file"
        $blobPath = "example/jars/scaldingwordcount-1.0-SNAPSHOT.jar"

        #Login to your Azure subscription
        Login-AzureRmAccount
        #Get HTTPS/Admin credentials for submitting the job later
        $creds = Get-Credential -Message "Enter the login credentials for the cluster"
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
            
        Set-AzureStorageBlobContent `
            -File $fileToUpload `
            -Blob $blobPath `
            -Container $container `
            -Context $context
            
        #Create a job definition and start the job
        $jobDef=New-AzureRmHDInsightMapReduceJobDefinition `
            -JobName ScaldingWordCount `
            -JarFile wasbs:///example/jars/scaldingwordcount-1.0-SNAPSHOT.jar `
            -ClassName com.microsoft.example.WordCount `
            -arguments "--hdfs", `
                        "--input", `
                        "wasbs:///example/data/gutenberg/davinci.txt", `
                        "--output", `
                        "wasbs:///example/wordcountout"
        $job = Start-AzureRmHDInsightJob `
            -clustername $clusterName `
            -jobdefinition $jobDef `
            -HttpCredential $creds
        Write-Output "Job ID is: $job.JobId"
        Wait-AzureRmHDInsightJob `
            -ClusterName $clusterName `
            -JobId $job.JobId `
            -HttpCredential $creds
        #Download the output of the job
        Get-AzureStorageBlobContent `
            -Blob example/wordcountout/part-00000 `
            -Container $container `
            -Destination output.txt `
            -Context $context
        #Log any errors that occured
        Get-AzureRmHDInsightJobOutput `
            -Clustername $clusterName `
            -JobId $job.JobId `
            -DefaultContainer $container `
            -DefaultStorageAccountName $storageAccountName `
            -DefaultStorageAccountKey $storageAccountKey `
            -HttpCredential $creds `
            -DisplayOutputType StandardError

     Cuando se ejecuta la secuencia de comandos, le pedirá que lo escriba el nombre de usuario administrador y la contraseña para el clúster de HDInsight. Los errores que se producen mientras se está ejecutando el trabajo se registrará en la consola.
     
6. Una vez completada la tarea, el resultado se descargará en el archivo __output.txt__ en el directorio actual. Use el comando siguiente para mostrar los resultados.

        cat output.txt

    El archivo debe contener valores similares al siguiente:

        writers 9
        writes  18
        writhed 1
        writing 51
        writings        24
        written 208
        writtenthese    1
        wrong   11
        wrongly 2
        wrongplace      1
        wrote   34
        wrotefootnote   1
        wrought 7

## <a name="next-steps"></a>Pasos siguientes

Ahora que ha aprendido a usar Scalding para crear MapReduce trabajos de HDInsight, use los siguientes vínculos para explorar otras formas de trabajar con HDInsight de Azure.

* [Usar subárbol con HDInsight](hdinsight-use-hive.md)

* [Usar cerdo con HDInsight](hdinsight-use-pig.md)

* [Usar MapReduce trabajos con HDInsight](hdinsight-use-mapreduce.md)
