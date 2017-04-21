<properties
pageTitle="Usar una función de Java definidas por el usuario (UDF) con subárbol en HDInsight | Microsoft Azure"
description="Aprenda a crear y usar una función de Java definidas por el usuario (UDF) de sección en HDInsight."
services="hdinsight"
documentationCenter=""
authors="Blackmist"
manager="jhubbard"
editor="cgronlun"/>

<tags
ms.service="hdinsight"
ms.devlang="java"
ms.topic="article"
ms.tgt_pltfrm="na"
ms.workload="big-data"
ms.date="09/27/2016"
ms.author="larryfr"/>

#<a name="use-a-java-udf-with-hive-in-hdinsight"></a>Usar un Java UDF con subárbol en HDInsight

Sección es una buena opción para trabajar con datos en HDInsight, pero a veces necesita más general propósito idioma. Sección le permite crear funciones definidas por el usuario (UDF) con una amplia variedad de lenguajes de programación. En este documento, aprenderá cómo usar una UDF Java de sección.

## <a name="requirements"></a>Requisitos

* Una suscripción de Azure

* Un clúster de HDInsight (Windows o Linux)

    > [AZURE.NOTE] Mayoría de los pasos de este documento funcionará en ambos tipos de clúster; Sin embargo, los pasos que se usa para cargar el archivo compilado UDF en el clúster y ejecútelo son específicos para clústeres basados en Linux. Se proporcionan vínculos a información que se pueden usar con clústeres basados en Windows.

* [Java JDK](http://www.oracle.com/technetwork/java/javase/downloads/) 7 o posterior (o un equivalente, como OpenJDK)

* [Experto en Apache](http://maven.apache.org/)

* Un editor de texto o IDE Java

    > [AZURE.IMPORTANT] Si está utilizando un servidor de HDInsight basados en Linux, pero crear los archivos de Python en un cliente de Windows, debe usar un editor que utiliza LF como un fin de línea. Si no está seguro de si el editor utiliza LF o CRLF, vea la sección de [solución de problemas](#troubleshooting) para conocer los pasos sobre cómo quitar el carácter CR mediante utilidades en el clúster HDInsight.

## <a name="create-an-example-udf"></a>Crear un ejemplo UDF

1. Desde una línea de comandos, utilice la siguiente para crear un nuevo proyecto de experto:

        mvn archetype:generate -DgroupId=com.microsoft.examples -DartifactId=ExampleUDF -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false

    > [AZURE.NOTE] Si está utilizando PowerShell, debe colocar comillas alrededor de los parámetros. Por ejemplo, `mvn archetype:generate "-DgroupId=com.microsoft.examples" "-DartifactId=ExampleUDF" "-DarchetypeArtifactId=maven-archetype-quickstart" "-DinteractiveMode=false"`.

    Se creará un nuevo directorio denominado __exampleudf__, que contiene el proyecto de experto.

2. Una vez creado el proyecto, elimine el directorio de __src/exampleudf/prueba__ creado como parte del proyecto; no se utilizará en este ejemplo.

3. Abra la __exampleudf/pom.xml__y reemplazar la existente `<dependencies>` entrada con las siguientes acciones:

        <dependencies>
            <dependency>
                <groupId>org.apache.hadoop</groupId>
                <artifactId>hadoop-client</artifactId>
                <version>2.7.1</version>
                <scope>provided</scope>
            </dependency>
            <dependency>
                <groupId>org.apache.hive</groupId>
                <artifactId>hive-exec</artifactId>
                <version>1.2.1</version>
                <scope>provided</scope>
            </dependency>
        </dependencies>

    Estas entradas especifican la versión de Hadoop y subárbol incluido con HDInsight 3.3 y 3.4 clústeres. Puede encontrar información en las versiones de Hadoop y subárbol proporcionado con HDInsight del documento de [control de versiones de componente HDInsight](hdinsight-component-versioning.md) .

    Agregar un `<build>` sección antes de la `</project>` línea al final del archivo. Esta sección debe contener lo siguiente:

        <build>
            <plugins>
                <!-- build for Java 1.7, even if you're on a later version -->
                <plugin>
                    <groupId>org.apache.maven.plugins</groupId>
                    <artifactId>maven-compiler-plugin</artifactId>
                    <version>3.3</version>
                    <configuration>
                        <source>1.7</source>
                        <target>1.7</target>
                    </configuration>
                </plugin>
                <!-- build an uber jar -->
                <plugin>
                    <groupId>org.apache.maven.plugins</groupId>
                    <artifactId>maven-shade-plugin</artifactId>
                    <version>2.3</version>
                    <configuration>
                        <!-- Keep us from getting a can't overwrite file error -->
                        <transformers>
                            <transformer
                                    implementation="org.apache.maven.plugins.shade.resource.ApacheLicenseResourceTransformer">
                            </transformer>
                            <transformer implementation="org.apache.maven.plugins.shade.resource.ServicesResourceTransformer">
                            </transformer>
                        </transformers>
                        <!-- Keep us from getting a bad signature error -->
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
                        </execution>
                    </executions>
                </plugin>
            </plugins>
        </build>
    
    Estas entradas definen cómo generar el proyecto. Más concretamente, la versión de Java que utiliza el proyecto y cómo crear un uberjar para su implementación en el clúster.

    Una vez que se han realizado los cambios, guarde el archivo.

4. Cambie el nombre __exampleudf/src/main/java/com/microsoft/examples/App.java__ a __ExampleUDF.java__y, a continuación, abra el archivo en el editor.

5. Reemplace el contenido del archivo __ExampleUDF.java__ con la siguiente, a continuación, guarde el archivo.

        package com.microsoft.examples;

        import org.apache.hadoop.hive.ql.exec.Description;
        import org.apache.hadoop.hive.ql.exec.UDF;
        import org.apache.hadoop.io.*;

        // Description of the UDF
        @Description(
            name="ExampleUDF",
            value="returns a lower case version of the input string.",
            extended="select ExampleUDF(deviceplatform) from hivesampletable limit 10;"
        )
        public class ExampleUDF extends UDF {
            // Accept a string input
            public String evaluate(String input) {
                // If the value is null, return a null
                if(input == null)
                    return null;
                // Lowercase the input string and return it
                return input.toLowerCase();
            }
        }

    Implementa una UDF acepta un valor de cadena y devuelve una versión de la cadena en minúsculas.

## <a name="build-and-install-the-udf"></a>Crear e instalar el archivo UDF

1. Use el comando siguiente para compilar y empaquetar UDF:

        mvn compile package

    Esto genere y empaquetar UDF en __exampleudf/target/ExampleUDF-1.0-SNAPSHOT.jar__.

2. Usar el `scp` comando para copiar el archivo en el clúster HDInsight.

        scp ./target/ExampleUDF-1.0-SNAPSHOT.jar myuser@mycluster-ssh.azurehdinsight

    Reemplazar __miUsuario__ con la cuenta de usuario SSH para el clúster. Reemplazar __miClúster__ con el nombre de clúster. Si utiliza una contraseña para proteger la cuenta SSH, se le pedirá que escriba la contraseña. Si utiliza un certificado, tendrá que usar la `-i` parámetro para especificar el archivo de clave privada.

3. Conectarse al clúster mediante SSH. 

        ssh myuser@mycluster-ssh.azurehdinsight.net

    Para obtener más información sobre cómo utilizar SSH con HDInsight, consulte los siguientes documentos.

    * [Utilizar SSH con basados en Linux Hadoop en HDInsight de Linux, Unix o OS X](hdinsight-hadoop-linux-use-ssh-unix.md)

    * [Utilizar SSH con basados en Linux Hadoop en HDInsight de Windows.](hdinsight-hadoop-linux-use-ssh-windows.md)

4. Desde la sesión SSH, copie el archivo jar al almacenamiento de HDInsight.

        hdfs dfs -put ExampleUDF-1.0-SNAPSHOT.jar /example/jars

## <a name="use-the-udf-from-hive"></a>Usar el archivo UDF de sección

1. Utilice la siguiente para iniciar al cliente de Beeline desde la sesión SSH.

        beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http' -n admin

    Este comando se supone que utiliza el valor predeterminado de __administración__ de la cuenta de inicio de sesión para el clúster.

2. Una vez que llegue a la `jdbc:hive2://localhost:10001/>` pregunta, escriba lo siguiente para agregar el archivo UDF al subárbol y exponer como una función.

        ADD JAR wasbs:///example/jars/ExampleUDF-1.0-SNAPSHOT.jar;
        CREATE TEMPORARY FUNCTION tolower as 'com.microsoft.examples.ExampleUDF';

3. Use la UDF para convertir valores recuperados de una tabla de cadenas de minúsculas.

        SELECT tolower(deviceplatform) FROM hivesampletable LIMIT 10;

    Esto seleccione la plataforma de dispositivo (Android, Windows, iOS, etc.) de la tabla, convertir la cadena a minúsculas y, a continuación, mostrarlas. El resultado tendrá un aspecto similar al siguiente.

        +----------+--+
        |   _c0    |
        +----------+--+
        | android  |
        | android  |
        | android  |
        | android  |
        | android  |
        | android  |
        | android  |
        | android  |
        | android  |
        | android  |
        +----------+--+

## <a name="next-steps"></a>Pasos siguientes

Para que conocer otras maneras de trabajar con la sección, vea [Usar subárbol con HDInsight](hdinsight-use-hive.md).

Para obtener más información acerca de las funciones Hive User-Defined, vea la sección [subárbol operadores y funciones definidas por el usuario](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF) de la wiki de sección en apache.org.
