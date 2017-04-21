<properties
   pageTitle="MapReduce con Hadoop en HDInsight | Microsoft Azure"
   description="Obtenga información sobre cómo ejecutar trabajos MapReduce en Hadoop en HDInsight clústeres. Deberá ejecutar una operación de recuento de word básicas implementada como un trabajo MapReduce Java."
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
   ms.date="08/23/2016"
   ms.author="larryfr"/>

# <a name="use-mapreduce-in-hadoop-on-hdinsight"></a>Usar MapReduce en Hadoop en HDInsight

[AZURE.INCLUDE [mapreduce-selector](../../includes/hdinsight-selector-use-mapreduce.md)]

En este artículo, aprenderá cómo ejecutar trabajos MapReduce en Hadoop en HDInsight clústeres. Se ejecuta una operación de contar de básicas de word implementada como un trabajo de Java MapReduce.

##<a id="whatis"></a>¿Qué es MapReduce?

Hadoop MapReduce es un marco de software para escribir trabajos que procesan grandes cantidades de datos. Datos de entrada se dividen en fragmentos independientes, que se procesan en paralelo en todos los nodos en el clúster. Un trabajo MapReduce constar de dos funciones:

* **Asignador**: consume datos de entrada, analiza (normalmente con el filtro y las operaciones de ordenación) y emite tuplas (pares de valor de clave)
* **Reductor**: consume tuplas emitido por el asignador y realiza una operación de resumen que crea un resultado más pequeño, combinado de los datos de asignador

Se muestra un ejemplo de trabajo de word básicas recuento MapReduce en el diagrama siguiente:

![HDI. WordCountDiagram][image-hdi-wordcountdiagram]

El resultado de esta tarea es un recuento del número de veces que se ha producido cada palabra en el texto que se ha analizado.

* El asignador toma cada línea del texto de entrada como una entrada y separa en palabras. Emite un valor de clave de par cada vez que se produce una palabra de la palabra va seguido de un 1. El resultado se ordena antes de enviar a reductor.

* El reductor suma estos recuentos individuales para cada palabra y emite un par de clave y valor único que contiene la palabra seguida de la suma de sus apariciones.

Puede implementar MapReduce en una gran variedad de idiomas. Java es la implementación más comunes y se usa para la demostración de este documento.

### <a name="hadoop-streaming"></a>Hadoop transmisión

Idiomas o marcos que se basan en Java y la máquina Virtual de Java (por ejemplo, Scalding o en cascada,) pueda ejecutar directamente como trabajo MapReduce, similar a una aplicación de Java. Otros, como C# o Python o ejecutables independientes, deben usar la transmisión de Hadoop.

Hadoop transmisión se comunica con el asignador y reductor sobre STDIN y STDOUT - el asignador reductor leer una línea de datos en un momento de STDIN y escribir el resultado en STDOUT. Cada línea de lectura o emitido por el asignador y reductor debe estar en el formato de un par de clave y valor delimitado por un charaacter ficha:

    [key]/t[value]

Para obtener más información, consulte la [Transmisión de Hadoop](http://hadoop.apache.org/docs/r1.2.1/streaming.html).

Para obtener ejemplos de uso de Hadoop streaming con HDInsight, consulte lo siguiente:

* [Desarrollar Python MapReduce trabajos](hdinsight-hadoop-streaming-python.md)

##<a id="data"></a>Acerca de los datos de ejemplo

En este ejemplo, para los datos de ejemplo que usa los blocs de notas de Leonardo Da Vinci, que se proporcionan como un documento de texto en el clúster HDInsight.

Los datos de ejemplo se almacenan en el almacenamiento de blobs de Windows Azure, que HDInsight se usa como el sistema de archivo predeterminado para clústeres Hadoop. HDInsight puede tener acceso a los archivos almacenados en el almacenamiento de blobs de Windows mediante el prefijo **wasb** . Por ejemplo, para obtener acceso al archivo sample.log, debería usar la siguiente sintaxis:

    wasbs:///example/data/gutenberg/davinci.txt

Dado que el almacenamiento de blobs de Windows Azure es el almacenamiento predeterminado para HDInsight, también puede acceder al archivo usando **/example/data/gutenberg/davinci.txt**.

> [AZURE.NOTE] En la sintaxis anterior, **wasbs: / / /** se usa para acceder a los archivos que se almacenan en el contenedor de almacenamiento predeterminada para el clúster de HDInsight. Si especifica cuentas de almacenamiento adicional cuando había aprovisionado el clúster y desea obtener acceso a los archivos almacenados en estas cuentas, puede acceder a los datos especificando la dirección de la cuenta de almacenamiento y el nombre del contenedor. Por ejemplo, **wasbs://mycontainer@mystorage.blob.core.windows.net/example/data/gutenberg/davinci.txt**.

##<a id="job"></a>Sobre el ejemplo MapReduce

El trabajo MapReduce que se usa en este ejemplo se encuentra en **wasbs://example/jars/hadoop-mapreduce-examples.jar**y se proporciona con el clúster de HDInsight. Esta página contiene un ejemplo de recuento de word que se ejecutará contra **davinci.txt**.

> [AZURE.NOTE] En clústeres HDInsight 2.1, la ubicación del archivo es **wasbs:///example/jars/hadoop-examples.jar**.

Para referencia, el siguiente es el código de Java para el trabajo de MapReduce recuento de word:

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

Para que obtener instrucciones escribir su propio trabajo MapReduce, vea [desarrollar Java MapReduce programas para HDInsight](hdinsight-develop-deploy-java-mapreduce-linux.md).

##<a id="run"></a>Ejecutar la MapReduce

HDInsight puede ejecutar trabajos HiveQL mediante una variedad de métodos. Utilice la siguiente tabla para decidir qué método es adecuado para usted y luego siga el vínculo para obtener un tutorial.

| **Use esta opción**...                                                    | **.. .para hacer esto**                                       | .. .with este **sistema operativo del clúster** | .. .de este **sistema operativo del cliente** |
|:-------------------------------------------------------------------|:--------------------------------------------------------|:------------------------------------------|:-----------------------------------------|
| [SSH](hdinsight-hadoop-use-mapreduce-ssh.md)                       | Use el comando Hadoop a través de **SSH**                  | Linux                                     | Linux, Unix, Mac OS X o Windows        |
| [Doblez](hdinsight-hadoop-use-mapreduce-curl.md)                     | Enviar el trabajo de forma remota utilizando el **resto**               | Linux o Windows                          | Linux, Unix, Mac OS X o Windows        |
| [Windows PowerShell](hdinsight-hadoop-use-mapreduce-powershell.md) | Enviar el trabajo de forma remota con **Windows PowerShell** | Linux o Windows                          | Windows                                  |
| [Escritorio remoto](hdinsight-hadoop-use-mapreduce-remote-desktop)    | Use el comando Hadoop a través de **Escritorio remoto**       | Windows                                   | Windows                                  |

##<a id="nextsteps"></a>Pasos siguientes

Aunque MapReduce proporciona eficaces funciones de diagnósticos, puede ser un poco difícil patrón. Existen varios marcos basada en Java que resulte más fácil definir MapReduce aplicaciones, así como tecnologías como cerdo y subárbol, que proporciona una manera más fácil de trabajar con datos en HDInsight. Para obtener más información, consulte los artículos siguientes:

* [Desarrollar programas Java MapReduce para HDInsight](hdinsight-develop-deploy-java-mapreduce-linux.md)

* [Desarrollar Python transmisión MapReduce programas para HDInsight](hdinsight-hadoop-streaming-python.md)

* [Desarrollar operaciones MapReduce trabajos con Apache Hadoop en HDInsight](hdinsight-hadoop-mapreduce-scalding.md)

* [Usar subárbol con HDInsight][hdinsight-use-hive]

* [Usar cerdo con HDInsight][hdinsight-use-pig]

* [Ejecutar los ejemplos de HDInsight][hdinsight-samples]


[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-get-started]: hdinsight-hadoop-linux-tutorial-get-started.md
[hdinsight-develop-mapreduce-jobs]: hdinsight-develop-deploy-java-mapreduce-linux.md
[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-pig]: hdinsight-use-pig.md
[hdinsight-samples]: hdinsight-run-samples.md
[hdinsight-provision]: hdinsight-provision-clusters.md

[powershell-install-configure]: ../powershell-install-configure.md

[image-hdi-wordcountdiagram]: ./media/hdinsight-use-mapreduce/HDI.WordCountDiagram.gif
