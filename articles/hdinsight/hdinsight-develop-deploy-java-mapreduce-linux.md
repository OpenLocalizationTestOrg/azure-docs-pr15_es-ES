<properties
    pageTitle="Desarrollar programas Java MapReduce para HDInsight de Linux | Microsoft Azure"
    description="Obtenga información sobre cómo desarrollar programas Java MapReduce e implementar ellos a HDInsight de Linux."
    services="hdinsight"
    editor="cgronlun"
    manager="jhubbard"
    authors="Blackmist"
    documentationCenter=""
    tags="azure-portal"/>

<tags
    ms.service="hdinsight"
    ms.workload="big-data"
    ms.tgt_pltfrm="na"
    ms.devlang="Java"
    ms.topic="article"
    ms.date="10/11/2016"
    ms.author="larryfr"/>

# <a name="develop-java-mapreduce-programs-for-hadoop-on-hdinsight-linux"></a>Desarrollar programas Java MapReduce para Hadoop en HDInsight Linux

Este documentos le guiará por usar Apache experto para crear una aplicación MapReduce, a continuación, implementar y ejecutar en un Hadoop basados en Linux en clúster HDInsight.

##<a name="prerequisites"></a>Requisitos previos

Antes de comenzar este tutorial, debe tener el siguiente:

- [Java JDK](http://www.oracle.com/technetwork/java/javase/downloads/) 7 o posterior (o un equivalente, como OpenJDK)

- [Experto en Apache](http://maven.apache.org/)

- **Una suscripción de Azure**

- **CLI de Azure**

    [AZURE.INCLUDE [use-latest-version](../../includes/hdinsight-use-latest-cli.md)]

##<a name="configure-environment-variables"></a>Configurar variables de entorno

Las siguientes variables de entorno pueden establecerse cuando se instalación Java y el JDK. Sin embargo, debe comprobar que existen y que contienen los valores correctos para su sistema.

* **JAVA_HOME** - deben apuntar al directorio donde está instalado el entorno de tiempo de ejecución de Java (JRE). Por ejemplo, en un sistema de OS X, Unix o Linux, debería tener un valor similar a `/usr/lib/jvm/java-7-oracle`. En Windows, tendría un valor similar a`c:\Program Files (x86)\Java\jre1.7`

* **Ruta de acceso** : debe contener las rutas siguientes:

    * **JAVA_HOME** (o la ruta de acceso equivalente)

    * **JAVA_HOME\bin** (o la ruta de acceso equivalente)

    * El directorio donde está instalado Maven

##<a name="create-a-new-maven-project"></a>Crear un nuevo proyecto de experto

1. Desde una sesión de terminal, o línea de comandos en su entorno de desarrollo, cambie a la ubicación que desea almacenar este proyecto.

3. Use el comando __mvn__ , que se instala con Maven, para generar el scaffolding para el proyecto.

        mvn archetype:generate -DgroupId=org.apache.hadoop.examples -DartifactId=wordcountjava -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false

    Esto creará un nuevo directorio en el directorio actual, con el nombre especificado por el parámetro __artifactID__ (**wordcountjava** en este ejemplo). Este directorio contendrá los siguientes elementos:

    * __pom.xml__ - el [Modelo de objeto de proyecto (POM)](http://maven.apache.org/guides/introduction/introduction-to-the-pom.html) que contiene los detalles de configuración e información se usa para generar el proyecto.

    * __src__ - el directorio que contiene el directorio de __java/main/org/hadoop/apache/ejemplos__ donde creará la aplicación.

3. Elimine el archivo __src/test/java/org/apache/hadoop/examples/apptest.java__ , ya que no se utiliza en este ejemplo.

##<a name="add-dependencies"></a>Agregar dependencias

1. Editar el archivo __pom.xml__ y agregue las siguientes dentro de la `<dependencies>` sección:

        <dependency>
          <groupId>org.apache.hadoop</groupId>
          <artifactId>hadoop-mapreduce-examples</artifactId>
          <version>2.5.1</version>
          <scope>provided</scope>
        </dependency>
        <dependency>
          <groupId>org.apache.hadoop</groupId>
          <artifactId>hadoop-mapreduce-client-common</artifactId>
          <version>2.5.1</version>
          <scope>provided</scope>
        </dependency>
        <dependency>
          <groupId>org.apache.hadoop</groupId>
          <artifactId>hadoop-common</artifactId>
          <version>2.5.1</version>
          <scope>provided</scope>
        </dependency>

    Indica que el proyecto requiere las bibliotecas de experto (enumerados en &lt;artifactId\>) con una versión específica (incluidos en &lt;versión\>). En tiempo de compilación, se descargarán desde el repositorio de experto en predeterminado. Puede usar la [búsqueda de repositorio de experto](http://search.maven.org/#artifactdetails%7Corg.apache.hadoop%7Chadoop-mapreduce-examples%7C2.5.1%7Cjar) para ver más.

    La `<scope>provided</scope>` indica que el experto que estas dependencias no se deben empaquetar con la aplicación, como se proporcionará el clúster HDInsight en tiempo de ejecución.

2. Agregue lo siguiente al archivo __pom.xml__ . Esto debe estar dentro de la `<project>...</project>` etiquetas en el archivo. Por ejemplo, entre `</dependencies>` y `</project>`.

        <build>
          <plugins>
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
            <plugin>
              <groupId>org.apache.maven.plugins</groupId>
              <artifactId>maven-compiler-plugin</artifactId>
              <configuration>
               <source>1.7</source>
               <target>1.7</target>
              </configuration>
            </plugin>
          </plugins>
        </build>

    El complemento del primer configura [Experto sombrear complemento](http://maven.apache.org/plugins/maven-shade-plugin/), que se usa para generar un uberjar (a veces denominado un fatjar) que contiene dependencias necesarios para la aplicación. También se evita la duplicación de licencias dentro del paquete jar, lo que puede causar problemas en algunos sistemas.

    El complemento del segundo configura el compilador experto, que se utiliza para establecer la versión de Java requerida esta aplicación a la versión utilizada en el clúster HDInsight.

3. Guarde el archivo __pom.xml__ .

##<a name="create-the-mapreduce-application"></a>Crear la aplicación MapReduce

1. Vaya al directorio __wordcountjava/src/main/java/org/apache/hadoop/ejemplos__ y cambie el nombre del archivo __App.java__ __WordCount.java__.

2. Abra el archivo __WordCount.java__ en un editor de texto y reemplace el contenido con lo siguiente:

        package org.apache.hadoop.examples;

        import java.io.IOException;
        import java.util.StringTokenizer;
        import org.apache.hadoop.conf.Configuration;
        import org.apache.hadoop.fs.Path;
        import org.apache.hadoop.io.IntWritable;
        import org.apache.hadoop.io.Text;
        import org.apache.hadoop.mapreduce.Job;
        import org.apache.hadoop.mapreduce.Mapper;
        import org.apache.hadoop.mapreduce.Reducer;
        import org.apache.hadoop.mapreduce.lib.input.FileInputFormat;
        import org.apache.hadoop.mapreduce.lib.output.FileOutputFormat;
        import org.apache.hadoop.util.GenericOptionsParser;

        public class WordCount {

          public static class TokenizerMapper
               extends Mapper<Object, Text, Text, IntWritable>{

            private final static IntWritable one = new IntWritable(1);
            private Text word = new Text();

            public void map(Object key, Text value, Context context
                            ) throws IOException, InterruptedException {
              StringTokenizer itr = new StringTokenizer(value.toString());
              while (itr.hasMoreTokens()) {
                word.set(itr.nextToken());
                context.write(word, one);
              }
            }
          }

          public static class IntSumReducer
               extends Reducer<Text,IntWritable,Text,IntWritable> {
            private IntWritable result = new IntWritable();

            public void reduce(Text key, Iterable<IntWritable> values,
                               Context context
                               ) throws IOException, InterruptedException {
              int sum = 0;
              for (IntWritable val : values) {
                sum += val.get();
              }
              result.set(sum);
              context.write(key, result);
            }
          }

          public static void main(String[] args) throws Exception {
            Configuration conf = new Configuration();
            String[] otherArgs = new GenericOptionsParser(conf, args).getRemainingArgs();
            if (otherArgs.length != 2) {
              System.err.println("Usage: wordcount <in> <out>");
              System.exit(2);
            }
            Job job = new Job(conf, "word count");
            job.setJarByClass(WordCount.class);
            job.setMapperClass(TokenizerMapper.class);
            job.setCombinerClass(IntSumReducer.class);
            job.setReducerClass(IntSumReducer.class);
            job.setOutputKeyClass(Text.class);
            job.setOutputValueClass(IntWritable.class);
            FileInputFormat.addInputPath(job, new Path(otherArgs[0]));
            FileOutputFormat.setOutputPath(job, new Path(otherArgs[1]));
            System.exit(job.waitForCompletion(true) ? 0 : 1);
          }
        }

    Observe el nombre del paquete es **org.apache.hadoop.examples** y el nombre de la clase es **WordCount**. Utilice estos nombres al enviar el trabajo MapReduce.

3. Guarde el archivo.

##<a name="build-the-application"></a>Generar la aplicación

1. Cambie al directorio __wordcountjava__ , si no está ya allí.

2. Use el comando siguiente para generar un archivo JAR que contiene la aplicación:

        mvn clean package

    Esto se limpiar los artefactos de compilación anterior, descargue las dependencias que no ya está instaladas y, a continuación, creación y empaquetar la aplicación.

3. Una vez que finalice el comando, el directorio de __wordcountjava/destino__ contendrá un archivo llamado __wordcountjava-1.0-SNAPSHOT.jar__.

    > [AZURE.NOTE] El archivo __wordcountjava-1.0-SNAPSHOT.jar__ es un uberjar, que contiene no sólo el trabajo WordCount, pero también dependencias que necesita el trabajo en tiempo de ejecución.


##<a id="upload"></a>Cargar el tarro

Usar el comando siguiente para cargar el archivo jar en el headnode HDInsight:

    scp wordcountjava-1.0-SNAPSHOT.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:

    Replace __USERNAME__ with your SSH user name for the cluster. Replace __CLUSTERNAME__ with the HDInsight cluster name.

Esto copiará los archivos del sistema local en el nodo principal.

> [AZURE.NOTE] Si utiliza una contraseña para proteger su cuenta SSH, se le pedirá la contraseña. Si utiliza una clave SSH, tendrá que usar la `-i` parámetro y la ruta de acceso a la clave privada. Por ejemplo, `scp -i /path/to/private/key wordcountjava-1.0-SNAPSHOT.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:`.

##<a name="run"></a>Ejecutar el trabajo MapReduce

1. Conectar con HDInsight mediante SSH como se describe en los siguientes artículos:

    - [Utilizar SSH con basados en Linux Hadoop en HDInsight de Linux, Unix o OS X](hdinsight-hadoop-linux-use-ssh-unix.md)

    - [Utilizar SSH con basados en Linux Hadoop en HDInsight de Windows.](hdinsight-hadoop-linux-use-ssh-windows.md)

2. Desde la sesión SSH, utilice el comando siguiente para ejecutar la aplicación MapReduce:

        yarn jar wordcountjava.jar org.apache.hadoop.examples.WordCount wasbs:///example/data/gutenberg/davinci.txt wasbs:///example/data/wordcountout

    Esto usar la aplicación WordCount MapReduce para contar las palabras en el archivo davinci.txt y almacenar los resultados de la __wasbs: / / / ejemplo/datos/wordcountout__. Tanto el archivo de entrada y salida se almacenan en el almacenamiento predeterminado para el clúster.

3. Una vez completada la tarea, utilice la siguiente para ver los resultados:

        hdfs dfs -cat wasbs:///example/data/wordcountout/*

    Debe aparecer una lista de palabras y cuenta con valores similares al siguiente:

        zeal    1
        zelus   1
        zenith  2

##<a id="nextsteps"></a>Pasos siguientes

En este documento, ha aprendido a desarrollar un trabajo MapReduce Java. Consulte los siguientes documentos para otras formas de trabajar con HDInsight.

- [Usar subárbol con HDInsight][hdinsight-use-hive]
- [Usar cerdo con HDInsight][hdinsight-use-pig]
- [Usar MapReduce con HDInsight](hdinsight-use-mapreduce.md)

Para obtener más información, vea también el [Centro para desarrolladores de Java](https://azure.microsoft.com/develop/java/).

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[hdinsight-use-sqoop]: hdinsight-use-sqoop.md
[hdinsight-ODBC]: hdinsight-connect-excel-hive-ODBC-driver.md
[hdinsight-power-query]: hdinsight-connect-excel-power-query.md

[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-admin-powershell]: hdinsight-administer-use-powershell.md
[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-pig]: hdinsight-use-pig.md
[hdinsight-power-query]: hdinsight-connect-excel-power-query.md

[powershell-PSCredential]: http://social.technet.microsoft.com/wiki/contents/articles/4546.working-with-passwords-secure-strings-and-credentials-in-windows-powershell.aspx

