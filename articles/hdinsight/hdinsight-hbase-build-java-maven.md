<properties
pageTitle="Crear una aplicación de HBase con Maven e implementar a HDInsight de Windows | Microsoft Azure"
description="Obtenga información sobre cómo usar a Apache experto para crear una aplicación basada en Java Apache HBase, a continuación, impleméntelo en un clúster basado en Windows Azure HDInsight."
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
ms.date="10/03/2016"
ms.author="larryfr"/>

#<a name="use-maven-to-build-java-applications-that-use-hbase-with-windows-based-hdinsight-hadoop"></a>Utilizar a Maven para generar aplicaciones de Java que usan HBase con HDInsight basado en Windows (Hadoop)

Aprenda a crear y generar una aplicación de [Apache HBase](http://hbase.apache.org/) en Java con Apache experto. A continuación, use la aplicación con HDInsight de Azure (Hadoop).

[Maven](http://maven.apache.org/) es una herramienta de comprensión que le permite crear software, documentación e informes de proyectos de Java y administración de proyectos de software. En este artículo, aprenderá a utilizar para crear una aplicación de Java básica que crea, consultas y elimina una tabla de HBase en un clúster de HDInsight de Azure.

> [AZURE.NOTE] Los pasos de este documento se supone que está usando un clúster basado en Windows HDInsight. Para obtener información sobre el uso de un clúster basados en Linux HDInsight, consulte [Usar Maven para generar aplicaciones de Java que utilizan HBase con HDInsight de Linux](hdinsight-hbase-build-java-maven-linux.md)

##<a name="requirements"></a>Requisitos

* [Plataforma Java JDK](http://www.oracle.com/technetwork/java/javase/downloads/index.html) 7 o posterior

* [Experto](http://maven.apache.org/)


* [Un clúster basado en Windows HDInsight con HBase](hdinsight-hbase-tutorial-get-started.md#create-hbase-cluster)


    > [AZURE.NOTE] Los pasos de este documento se ha probado con las versiones de clúster de HDInsight 3.2 y 3.3. Los valores predeterminados proporcionados en los ejemplos son para un clúster de 3.3 HDInsight.

##<a name="create-the-project"></a>Crear el proyecto

1. Desde la línea de comandos en su entorno de desarrollo, cambie los directorios a la ubicación donde desea crear el proyecto, por ejemplo, `cd code\hdinsight`.

2. Use el comando __mvn__ , que se instala con Maven, para generar el scaffolding para el proyecto.

        mvn archetype:generate -DgroupId=com.microsoft.examples -DartifactId=hbaseapp -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false

    Este comando crea un directorio en la ubicación actual, con el nombre especificado por el parámetro __artifactID__ (**hbaseapp** en este ejemplo). Este directorio contiene los elementos siguientes:

    * __pom.XML__: el modelo de objeto de proyectos ([POM](http://maven.apache.org/guides/introduction/introduction-to-the-pom.html)) contiene los detalles de configuración e información se usa para generar el proyecto.

    * __src__: el directorio que contiene el directorio __main\java\com\microsoft\examples__ , donde creará la aplicación.

3. Elimine el archivo __src\test\java\com\microsoft\examples\apptest.java__ porque no se usa en este ejemplo.

##<a name="update-the-project-object-model"></a>Actualizar el modelo de objeto de proyectos

1. Editar el archivo __pom.xml__ y agregue el código siguiente dentro de la `<dependencies>` sección:

        <dependency>
          <groupId>org.apache.hbase</groupId>
          <artifactId>hbase-client</artifactId>
          <version>1.1.2</version>
        </dependency>

    Esta sección se explica que el proyecto requiere la versión de __cliente de hbase__ __1.1.2__experto. En tiempo de compilación esta dependencia se descarga desde el repositorio de experto en predeterminado. Puede usar la [Búsqueda de repositorio Central de experto en](http://search.maven.org/#artifactdetails%7Corg.apache.hbase%7Chbase-client%7C0.98.4-hadoop2%7Cjar) para obtener más información sobre esta dependencia.

    > [AZURE.IMPORTANT] El número de versión debe coincidir con la versión de HBase que se incluye con el clúster de HDInsight. Utilice la siguiente tabla para buscar el número de versión correcto.

  	| Versión de clúster HDInsight | Versión del HBase |
  	| ----- | ----- |
  	| 3.2 | 0.98.4-hadoop2 |
  	| 3.3 | 1.1.2 |

    Para obtener más información sobre las versiones de HDInsight y componentes, consulte [¿Qué son los distintos componentes de Hadoop disponibles con HDInsight](hdinsight-component-versioning.md).

2. Si está utilizando un clúster de HDInsight 3.3, también debe agregar las siguientes acciones para la `<dependencies>` sección:

        <dependency>
            <groupId>org.apache.phoenix</groupId>
            <artifactId>phoenix-core</artifactId>
            <version>4.4.0-HBase-1.1</version>
        </dependency>
    
    Esta dependencia cargará los componentes principales Caracas, que utilizan versiones Hbase 1.1.x.

2. Agregue el código siguiente al archivo __pom.xml__ . Esta sección debe estar dentro de la `<project>...</project>` etiquetas en el archivo, por ejemplo, entre `</dependencies>` y `</project>`.

        <build>
          <sourceDirectory>src</sourceDirectory>
          <resources>
              <resource>
                <directory>${basedir}/conf</directory>
                <filtering>false</filtering>
                <includes>
                  <include>hbase-site.xml</include>
                </includes>
              </resource>
            </resources>
          <plugins>
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-compiler-plugin</artifactId>
                <version>3.3</version>
                <configuration>
                    <source>1.7</source>
                    <target>1.7</target>
                </configuration>
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
              </configuration>
              <executions>
                <execution>
                  <phase>package</phase>
                  <goals>
                    <goal>shade</goal>
                  </goals>
                </execution>
              </executions>
            </plugin>
          </plugins>
        </build>

    La `<resources>` sección configura un recurso (__site.xml conf\hbase__) que contiene la información de configuración de HBase.

    > [AZURE.NOTE] También puede establecer los valores de configuración a través del código. Ver los comentarios en el ejemplo __CreateTable__ que sigue para que saber cómo hacerlo.

    Esto `<plugins>` sección configura el [Experto compilador complemento](http://maven.apache.org/plugins/maven-compiler-plugin/) y el [Complemento de experto en sombra](http://maven.apache.org/plugins/maven-shade-plugin/). El compilador complemento se utiliza para compilar la topología. Se usa el complemento de sombra para evitar la duplicación de licencia en el paquete JAR integrado experto. El motivo que se utiliza es que los archivos de licencia duplicadas producen un error en tiempo de ejecución en el clúster HDInsight. Usando experto sombrear-complemento con la `ApacheLicenseResourceTransformer` implementación impide que este error.

    El complemento de sombreado de experto también genera una ejercicios jar (o jar fat) que contiene todas las dependencias necesarios para la aplicación.

3. Guarde el archivo __pom.xml__ .

4. Crear un nuevo directorio denominado __conferencia__ en el directorio __hbaseapp__ . En el directorio de __conferencia__ , cree un archivo llamado __hbase site.xml__. Utilice la siguiente como el contenido del archivo:

        <?xml version="1.0"?>
        <?xml-stylesheet type="text/xsl" href="configuration.xsl"?>
        <!--
        /**
          * Copyright 2010 The Apache Software Foundation
          *
          * Licensed to the Apache Software Foundation (ASF) under one
          * or more contributor license agreements.  See the NOTICE file
          * distributed with this work for additional information
          * regarding copyright ownership.  The ASF licenses this file
          * to you under the Apache License, Version 2.0 (the
          * "License"); you may not use this file except in compliance
          * with the License.  You may obtain a copy of the License at
          *
          *     http://www.apache.org/licenses/LICENSE-2.0
          *
          * Unless required by applicable law or agreed to in writing, software
          * distributed under the License is distributed on an "AS IS" BASIS,
          * WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
          * See the License for the specific language governing permissions and
          * limitations under the License.
          */
        -->
        <configuration>
          <property>
            <name>hbase.cluster.distributed</name>
            <value>true</value>
          </property>
          <property>
            <name>hbase.zookeeper.quorum</name>
            <value>zookeeper0,zookeeper1,zookeeper2</value>
          </property>
          <property>
            <name>hbase.zookeeper.property.clientPort</name>
            <value>2181</value>
          </property>
        </configuration>

    Este archivo se utilizará para cargar la configuración de HBase para un clúster de HDInsight.

    > [AZURE.NOTE] Este es un archivo de hbase-site.xml mínimas y contiene la configuración de hardware mínima para el clúster de HDInsight.

3. Guarde el archivo __hbase site.xml__ .

##<a name="create-the-application"></a>Creación de la aplicación

1. Vaya al directorio __hbaseapp\src\main\java\com\microsoft\examples__ y cambie el nombre del archivo app.java __CreateTable.java__.

2. Abra el archivo __CreateTable.java__ y reemplace el contenido existente con el siguiente código:

        package com.microsoft.examples;
        import java.io.IOException;

        import org.apache.hadoop.conf.Configuration;
        import org.apache.hadoop.hbase.HBaseConfiguration;
        import org.apache.hadoop.hbase.client.HBaseAdmin;
        import org.apache.hadoop.hbase.HTableDescriptor;
        import org.apache.hadoop.hbase.TableName;
        import org.apache.hadoop.hbase.HColumnDescriptor;
        import org.apache.hadoop.hbase.client.HTable;
        import org.apache.hadoop.hbase.client.Put;
        import org.apache.hadoop.hbase.util.Bytes;

        public class CreateTable {
          public static void main(String[] args) throws IOException {
            Configuration config = HBaseConfiguration.create();

            // Example of setting zookeeper values for HDInsight
            // in code instead of an hbase-site.xml file
            //
            // config.set("hbase.zookeeper.quorum",
            //            "zookeepernode0,zookeepernode1,zookeepernode2");
            //config.set("hbase.zookeeper.property.clientPort", "2181");
            //config.set("hbase.cluster.distributed", "true");
            // The following sets the znode root for Linux-based HDInsight
            //config.set("zookeeper.znode.parent","/hbase-unsecure");

            // create an admin object using the config
            HBaseAdmin admin = new HBaseAdmin(config);

            // create the table...
            HTableDescriptor tableDescriptor = new HTableDescriptor(TableName.valueOf("people"));
            // ... with two column families
            tableDescriptor.addFamily(new HColumnDescriptor("name"));
            tableDescriptor.addFamily(new HColumnDescriptor("contactinfo"));
            admin.createTable(tableDescriptor);

            // define some people
            String[][] people = {
                { "1", "Marcel", "Haddad", "marcel@fabrikam.com"},
                { "2", "Franklin", "Holtz", "franklin@contoso.com" },
                { "3", "Dwayne", "McKee", "dwayne@fabrikam.com" },
                { "4", "Rae", "Schroeder", "rae@contoso.com" },
                { "5", "Rosalie", "burton", "rosalie@fabrikam.com"},
                { "6", "Gabriela", "Ingram", "gabriela@contoso.com"} };

            HTable table = new HTable(config, "people");

            // Add each person to the table
            //   Use the `name` column family for the name
            //   Use the `contactinfo` column family for the email
            for (int i = 0; i< people.length; i++) {
              Put person = new Put(Bytes.toBytes(people[i][0]));
              person.add(Bytes.toBytes("name"), Bytes.toBytes("first"), Bytes.toBytes(people[i][1]));
              person.add(Bytes.toBytes("name"), Bytes.toBytes("last"), Bytes.toBytes(people[i][2]));
              person.add(Bytes.toBytes("contactinfo"), Bytes.toBytes("email"), Bytes.toBytes(people[i][3]));
              table.put(person);
            }
            // flush commits and close the table
            table.flushCommits();
            table.close();
          }
        }

    Esta es la clase __CreateTable__ , que creará una tabla denominada __personas__ y rellénela con algunos usuarios predefinidos.

3. Guarde el archivo __CreateTable.java__ .

4. En el directorio __hbaseapp\src\main\java\com\microsoft\examples__ , cree un nuevo archivo denominado __SearchByEmail.java__. Usar el siguiente código como el contenido de este archivo:

        package com.microsoft.examples;
        import java.io.IOException;

        import org.apache.hadoop.conf.Configuration;
        import org.apache.hadoop.hbase.HBaseConfiguration;
        import org.apache.hadoop.hbase.client.HTable;
        import org.apache.hadoop.hbase.client.Scan;
        import org.apache.hadoop.hbase.client.ResultScanner;
        import org.apache.hadoop.hbase.client.Result;
        import org.apache.hadoop.hbase.filter.RegexStringComparator;
        import org.apache.hadoop.hbase.filter.SingleColumnValueFilter;
        import org.apache.hadoop.hbase.filter.CompareFilter.CompareOp;
        import org.apache.hadoop.hbase.util.Bytes;
        import org.apache.hadoop.util.GenericOptionsParser;

        public class SearchByEmail {
          public static void main(String[] args) throws IOException {
            Configuration config = HBaseConfiguration.create();

            // Use GenericOptionsParser to get only the parameters to the class
            // and not all the parameters passed (when using WebHCat for example)
            String[] otherArgs = new GenericOptionsParser(config, args).getRemainingArgs();
            if (otherArgs.length != 1) {
              System.out.println("usage: [regular expression]");
              System.exit(-1);
            }

            // Open the table
            HTable table = new HTable(config, "people");

            // Define the family and qualifiers to be used
            byte[] contactFamily = Bytes.toBytes("contactinfo");
            byte[] emailQualifier = Bytes.toBytes("email");
            byte[] nameFamily = Bytes.toBytes("name");
            byte[] firstNameQualifier = Bytes.toBytes("first");
            byte[] lastNameQualifier = Bytes.toBytes("last");

            // Create a new regex filter
            RegexStringComparator emailFilter = new RegexStringComparator(otherArgs[0]);
            // Attach the regex filter to a filter
            //   for the email column
            SingleColumnValueFilter filter = new SingleColumnValueFilter(
              contactFamily,
              emailQualifier,
              CompareOp.EQUAL,
              emailFilter
            );

            // Create a scan and set the filter
            Scan scan = new Scan();
            scan.setFilter(filter);

            // Get the results
            ResultScanner results = table.getScanner(scan);
            // Iterate over results and print  values
            for (Result result : results ) {
              String id = new String(result.getRow());
              byte[] firstNameObj = result.getValue(nameFamily, firstNameQualifier);
              String firstName = new String(firstNameObj);
              byte[] lastNameObj = result.getValue(nameFamily, lastNameQualifier);
              String lastName = new String(lastNameObj);
              System.out.println(firstName + " " + lastName + " - ID: " + id);
              byte[] emailObj = result.getValue(contactFamily, emailQualifier);
              String email = new String(emailObj);
              System.out.println(firstName + " " + lastName + " - " + email + " - ID: " + id);
            }
            results.close();
            table.close();
          }
        }

    La clase __SearchByEmail__ puede utilizarse para consultar filas por la dirección de correo electrónico. Porque utiliza un filtro de expresión regular, puede proporcionar una cadena o una expresión regular al utilizar la clase.

5. Guarde el archivo __SearchByEmail.java__ .

6. En el directorio __hbaseapp\src\main\hava\com\microsoft\examples__ , cree un nuevo archivo denominado __DeleteTable.java__. Usar el siguiente código como el contenido de este archivo:

        package com.microsoft.examples;
        import java.io.IOException;

        import org.apache.hadoop.conf.Configuration;
        import org.apache.hadoop.hbase.HBaseConfiguration;
        import org.apache.hadoop.hbase.client.HBaseAdmin;

        public class DeleteTable {
          public static void main(String[] args) throws IOException {
            Configuration config = HBaseConfiguration.create();

            // Create an admin object using the config
            HBaseAdmin admin = new HBaseAdmin(config);

            // Disable, and then delete the table
            admin.disableTable("people");
            admin.deleteTable("people");
          }
        }

    Esta clase es para limpiar este ejemplo deshabilitando y eliminar la tabla creada por la clase __CreateTable__ .

7. Guarde el archivo __DeleteTable.java__ .

##<a name="build-and-package-the-application"></a>Generar y empaquetar la aplicación

1. Abra un símbolo del sistema y cambie los directorios al directorio __hbaseapp__ .

2. Use el comando siguiente para generar un archivo JAR que contiene la aplicación:

        mvn clean package

    Esto limpia cualquier artefacto de compilación anterior, descargas dependencias que no se han instalado, a continuación, crea y paquete de la aplicación.

3. Cuando finalice el comando, el directorio __hbaseapp\target__ contiene un archivo llamado __hbaseapp-1.0-SNAPSHOT.jar__.

    > [AZURE.NOTE] El archivo __hbaseapp-1.0-SNAPSHOT.jar__ es un ejercicios jar (a veces denominado un jar fat,) que contiene todas las dependencias necesarias para ejecutar la aplicación.

##<a name="upload-the-jar-file-and-start-a-job"></a>Cargue el archivo JAR e iniciar un trabajo

Hay muchas formas de cargar un archivo en el clúster HDInsight, como se describe en [cargar los datos para trabajos de Hadoop en HDInsight](hdinsight-upload-data.md). Los siguientes pasos usar PowerShell de Azure.

[AZURE.INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]


1. Después de instalar y configurar Azure PowerShell, cree un nuevo archivo denominado __hbase runner.psm1__. Utilice la siguiente como el contenido de este archivo:

        <#
        .SYNOPSIS
        Copies a file to the primary storage of an HDInsight cluster.
        .DESCRIPTION
        Copies a file from a local directory to the blob container for
        the HDInsight cluster.
        .EXAMPLE
        Start-HBaseExample -className "com.microsoft.examples.CreateTable"
        -clusterName "MyHDInsightCluster"
        
        .EXAMPLE
        Start-HBaseExample -className "com.microsoft.examples.SearchByEmail"
        -clusterName "MyHDInsightCluster"
        -emailRegex "contoso.com"
        
        .EXAMPLE
        Start-HBaseExample -className "com.microsoft.examples.SearchByEmail"
        -clusterName "MyHDInsightCluster"
        -emailRegex "^r" -showErr
        #>
        
        function Start-HBaseExample {
        [CmdletBinding(SupportsShouldProcess = $true)]
        param(
        #The class to run
        [Parameter(Mandatory = $true)]
        [String]$className,
        
        #The name of the HDInsight cluster
        [Parameter(Mandatory = $true)]
        [String]$clusterName,
        
        #Only used when using SearchByEmail
        [Parameter(Mandatory = $false)]
        [String]$emailRegex,
        
        #Use if you want to see stderr output
        [Parameter(Mandatory = $false)]
        [Switch]$showErr
        )
        
        Set-StrictMode -Version 3
        
        # Is the Azure module installed?
        FindAzure
        
        # Get the login for the HDInsight cluster
        $creds = Get-Credential
        
        # Get storage information
        $storage = GetStorage -clusterName $clusterName
        
        # The JAR
        $jarFile = "wasbs:///example/jars/hbaseapp-1.0-SNAPSHOT.jar"
        
        # The job definition
        $jobDefinition = New-AzureRmHDInsightMapReduceJobDefinition `
            -JarFile $jarFile `
            -ClassName $className `
            -Arguments $emailRegex
        
        # Get the job output
        $job = Start-AzureRmHDInsightJob `
            -ClusterName $clusterName `
            -JobDefinition $jobDefinition `
            -HttpCredential $creds
        Write-Host "Wait for the job to complete ..." -ForegroundColor Green
        Wait-AzureRmHDInsightJob `
            -ClusterName $clusterName `
            -JobId $job.JobId `
            -HttpCredential $creds
        if($showErr)
        {
        Write-Host "STDERR"
        Get-AzureRmHDInsightJobOutput `
                    -Clustername $clusterName `
                    -JobId $job.JobId `
                    -DefaultContainer $storage.container `
                    -DefaultStorageAccountName $storage.storageAccount `
                    -DefaultStorageAccountKey $storage.storageAccountKey `
                    -HttpCredential $creds `
                    -DisplayOutputType StandardError
        }
        Write-Host "Display the standard output ..." -ForegroundColor Green
        Get-AzureRmHDInsightJobOutput `
                    -Clustername $clusterName `
                    -JobId $job.JobId `
                    -DefaultContainer $storage.container `
                    -DefaultStorageAccountName $storage.storageAccount `
                    -DefaultStorageAccountKey $storage.storageAccountKey `
                    -HttpCredential $creds
        }
        
        <#
        .SYNOPSIS
        Copies a file to the primary storage of an HDInsight cluster.
        .DESCRIPTION
        Copies a file from a local directory to the blob container for
        the HDInsight cluster.
        .EXAMPLE
        Add-HDInsightFile -localPath "C:\temp\data.txt"
        -destinationPath "example/data/data.txt"
        -ClusterName "MyHDInsightCluster"
        .EXAMPLE
        Add-HDInsightFile -localPath "C:\temp\data.txt"
        -destinationPath "example/data/data.txt"
        -ClusterName "MyHDInsightCluster"
        -Container "MyContainer"
        #>
        
        function Add-HDInsightFile {
            [CmdletBinding(SupportsShouldProcess = $true)]
            param(
                #The path to the local file.
                [Parameter(Mandatory = $true)]
                [String]$localPath,
                
                #The destination path and file name, relative to the root of the container.
                [Parameter(Mandatory = $true)]
                [String]$destinationPath,
                
                #The name of the HDInsight cluster
                [Parameter(Mandatory = $true)]
                [String]$clusterName,
                
                #If specified, overwrites existing files without prompting
                [Parameter(Mandatory = $false)]
                [Switch]$force
            )
            
            Set-StrictMode -Version 3
            
            # Is the Azure module installed?
            FindAzure
            
            # Get authentication for the cluster
            $creds=Get-Credential
            
            # Does the local path exist?
            if (-not (Test-Path $localPath))
            {
                throw "Source path '$localPath' does not exist."
            }
            
            # Get the primary storage container
            $storage = GetStorage -clusterName $clusterName
            
            # Upload file to storage, overwriting existing files if -force was used.
            Set-AzureStorageBlobContent -File $localPath `
                -Blob $destinationPath `
                -force:$force `
                -Container $storage.container `
                -Context $storage.context
        }
        
        function FindAzure {
            # Is there an active Azure subscription?
            $sub = Get-AzureRmSubscription -ErrorAction SilentlyContinue
            if(-not($sub))
            {
                throw "No active Azure subscription found! If you have a subscription, use the Login-AzureRmAccount cmdlet to login to your subscription."
            }
        }
        
        function GetStorage {
            param(
                [Parameter(Mandatory = $true)]
                [String]$clusterName
            )
            $hdi = Get-AzureRmHDInsightCluster -ClusterName $clusterName
            # Does the cluster exist?
            if (!$hdi)
            {
                throw "HDInsight cluster '$clusterName' does not exist."
            }
            # Create a return object for context & container
            $return = @{}
            $storageAccounts = @{}
            
            # Get storage information
            $resourceGroup = $hdi.ResourceGroup
            $storageAccountName=$hdi.DefaultStorageAccount.split('.')[0]
            $container=$hdi.DefaultStorageContainer
            $storageAccountKey=(Get-AzureRmStorageAccountKey `
                -Name $storageAccountName `
            -ResourceGroupName $resourceGroup)[0].Value
            # Get the resource group, in case we need that
            $return.resourceGroup = $resourceGroup
            # Get the storage context, as we can't depend
            # on using the default storage context
            $return.context = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey
            # Get the container, so we know where to
            # find/store blobs
            $return.container = $container
            # Return storage accounts to support finding all accounts for
            # a cluster
            $return.storageAccount = $storageAccountName
            $return.storageAccountKey = $storageAccountKey
            
            return $return
        }
        # Only export the verb-phrase things
        export-modulemember *-*

    Este archivo contiene dos módulos:

    * __Agregar HDInsightFile__ - usado para cargar archivos a HDInsight

    * __Inicio HBaseExample__ - utilizada para ejecutar las clases creadas anteriormente

2. Guarde el archivo __hbase runner.psm1__ .

3. Abrir una nueva ventana de PowerShell de Azure, cambie los directorios al directorio __hbaseapp__ y, a continuación, ejecute el siguiente comando.

        PS C:\ Import-Module c:\path\to\hbase-runner.psm1

    Cambiar la ruta de acceso a la ubicación del archivo de __hbase runner.psm1__ que creó anteriormente. Esto registra el módulo para esta sesión de PowerShell de Azure.

2. Usar el comando siguiente para cargar el __hbaseapp-1.0-SNAPSHOT.jar__ en el clúster HDInsight.

        Add-HDInsightFile -localPath target\hbaseapp-1.0-SNAPSHOT.jar -destinationPath example/jars/hbaseapp-1.0-SNAPSHOT.jar -clusterName hdinsightclustername

    Reemplace __hdinsightclustername__ con el nombre de su clúster HDInsight. El comando carga la __hbaseapp-1.0-SNAPSHOT.jar__ en el __ejemplo/jars__ la ubicación en el almacenamiento principal para el clúster de HDInsight.

3. Cuando se carguen los archivos, use el siguiente código para crear una tabla usando el __hbaseapp__:

        Start-HBaseExample -className com.microsoft.examples.CreateTable -clusterName hdinsightclustername

    Reemplace __hdinsightclustername__ con el nombre de su clúster HDInsight.

    Este comando crea una nueva tabla denominada __personas__ en el clúster HDInsight. Este comando no muestra ningún resultado en la ventana de consola.

2. Para buscar las entradas de la tabla, use el siguiente comando:

        Start-HBaseExample -className com.microsoft.examples.SearchByEmail -clusterName hdinsightclustername -emailRegex contoso.com

    Reemplace __hdinsightclustername__ con el nombre de su clúster HDInsight.

    Este comando usa la clase **SearchByEmail** para buscar las filas donde la columna de __correo electrónico__ y de la familia de la columna __contactinformation__ contiene la cadena __contoso.com__. Recibirá los siguientes resultados:

          Franklin Holtz - ID: 2
          Franklin Holtz - franklin@contoso.com - ID: 2
          Rae Schroeder - ID: 4
          Rae Schroeder - rae@contoso.com - ID: 4
          Gabriela Ingram - ID: 6
          Gabriela Ingram - gabriela@contoso.com - ID: 6

    Usar __fabrikam.com__ para la `-emailRegex` valor devuelve los usuarios que tienen __fabrikam.com__ en el campo de correo electrónico. Dado que esta búsqueda se implementa mediante un filtro basado en expresiones regular, también puede escribir expresiones regulares, como por ejemplo __^ r__, las entradas que devuelve donde empieza el correo electrónico con la letra 'r'.

##<a name="delete-the-table"></a>Eliminar la tabla

Cuando haya terminado con el ejemplo, use el siguiente comando de la sesión de Azure PowerShell para eliminar la tabla de __personas__ que se utiliza en este ejemplo:

    Start-HBaseExample -className com.microsoft.examples.DeleteTable -clusterName hdinsightclustername

Reemplace __hdinsightclustername__ con el nombre de su clúster HDInsight.

##<a name="troubleshooting"></a>Solución de problemas

###<a name="no-results-or-unexpected-results-when-using-start-hbaseexample"></a>Sin resultados o resultados inesperados al utilizar HBaseExample de inicio

Usar el `-showErr` parámetro para ver el error estándar (STDERR) que se ha producido mientras se ejecuta el trabajo.
