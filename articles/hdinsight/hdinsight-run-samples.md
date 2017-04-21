<properties
    pageTitle="Ejecutar los ejemplos de Hadoop en HDInsight | Microsoft Azure"
    description="Introducción al uso del servicio de HDInsight de Azure con los ejemplos. Utilizar secuencias de comandos de PowerShell que se ejecuten programas MapReduce en clústeres de datos."
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
    ms.date="10/21/2016"
    ms.author="jgao"/>

#<a name="run-hadoop-mapreduce-samples-in-windows-based-hdinsight"></a>Ejecutar Hadoop MapReduce ejemplos en HDInsight de Windows

[AZURE.INCLUDE [samples-selector](../../includes/hdinsight-run-samples-selector.md)]

Se proporciona un conjunto de ejemplos que le ayudarán a los trabajos MapReduce ejecución iniciados en clústeres Hadoop con HDInsight de Azure. Estos ejemplos están disponibles en cada uno de los clústeres HDInsight administrada que cree. Ejecutar estos ejemplos para familiarizarse con el uso de cmdlets de PowerShell de Azure para ejecutar trabajos en clústeres Hadoop.

- [**Recuento de palabras**][hdinsight-sample-wordcount]: cuenta repeticiones de word en un archivo de texto.
- [**Recuento de palabras de transmisión de C#**][hdinsight-sample-csharp-streaming]: cuenta repeticiones de word en un archivo de texto mediante la interfaz de transmisión de Hadoop.
- [**Estimador de PI**][hdinsight-sample-pi-estimator]: utiliza una estadística (cuasi-Monte Carlo) método para estimar el valor de pi.
- [**10 GB Graysort**][hdinsight-sample-10gb-graysort]: ejecutar un propósito general GraySort en un archivo de 10 GB mediante HDInsight. Existen tres tareas para ejecutar: Teragen para generar los datos, Terasort para ordenar los datos y a continuación, Teravalidate para confirmar que los datos se han ordenado correctamente.

>[AZURE.NOTE] Encontrará el código fuente en el apéndice. 

Existe documentación mucho más en la web para tecnologías relacionadas con el Hadoop, como programación basada en Java MapReduce y transmisión y documentación sobre los cmdlets que se usan en Windows PowerShell scripting. Para obtener más información acerca de estos recursos, consulte:

- [Desarrollar programas Java MapReduce para Hadoop en HDInsight](hdinsight-develop-deploy-java-mapreduce-linux.md)
- [Enviar trabajos de Hadoop en HDInsight](hdinsight-submit-hadoop-jobs-programmatically.md)
- [Introducción a HDInsight de Azure][hdinsight-introduction]

En la actualidad, muchas personas que elija subárbol y cerdo sobre MapReduce.  Para obtener más información, consulte:

- [Usar subárbol en HDInsight](hdinsight-use-hive.md)
- [Usar cerdo en HDInsight](hdinsight-use-pig.md)
 
**Requisitos previos**:

- **Suscripción de un Azure**. Vea la [versión de prueba gratuita de Azure obtener](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
- **un clúster de HDInsight**. Para obtener instrucciones sobre las diversas maneras en que se pueden crear como clústeres, consulte [crear Hadoop clústeres en HDInsight](hdinsight-provision-clusters.md).
- **Una estación de trabajo con PowerShell de Azure**.

    [AZURE.INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]

## <a name="hdinsight-sample-wordcount"></a>Count - Java de Word 

Para enviar un proyecto MapReduce, cree primero una definición de trabajo MapReduce. En la definición de trabajo, especifique el archivo de jar MapReduce programa y la ubicación del archivo jar, que es **wasbs:///example/jars/hadoop-mapreduce-examples.jar**, el nombre de la clase y los argumentos.  El programa de MapReduce wordcount acepta dos argumentos: el archivo de origen que se usará para contar las palabras y la ubicación de salida.

Encontrará el código fuente en el [Apéndice A](#apendix-a---the-word-count-MapReduce-program-in-java).

Para el procedimiento de desarrollo de un programa de Java MapReduce, consulte - [desarrollar Java MapReduce programas para Hadoop en HDInsight](hdinsight-develop-deploy-java-mapreduce-linux.md)
 
**Para enviar un trabajo de MapReduce recuento de word**

1. Abrir **Windows PowerShell ISE**. Para obtener instrucciones, consulte [instalar y configurar Azure PowerShell][powershell-install-configure].
2. Pegue el siguiente script de PowerShell:

        $subscriptionName = "<Azure Subscription Name>"
        $resourceGroupName = "<Resource Group Name>"
        $clusterName = "<HDInsight cluster name>"             # HDInsight cluster name
        
        Select-AzureRmSubscription -SubscriptionName $subscriptionName
        
        # Define the MapReduce job
        $mrJobDefinition = New-AzureRmHDInsightMapReduceJobDefinition `
                                    -JarFile "wasbs:///example/jars/hadoop-mapreduce-examples.jar" `
                                    -ClassName "wordcount" `
                                    -Arguments "wasbs:///example/data/gutenberg/davinci.txt", "wasbs:///example/data/WordCountOutput1"
        
        # Submit the job and wait for job completion
        $cred = Get-Credential -Message "Enter the HDInsight cluster HTTP user credential:" 
        $mrJob = Start-AzureRmHDInsightJob `
                            -ResourceGroupName $resourceGroupName `
                            -ClusterName $clusterName `
                            -HttpCredential $cred `
                            -JobDefinition $mrJobDefinition 
        
        Wait-AzureRmHDInsightJob `
            -ResourceGroupName $resourceGroupName `
            -ClusterName $clusterName `
            -HttpCredential $cred `
            -JobId $mrJob.JobId 
        
        # Get the job output
        $cluster = Get-AzureRmHDInsightCluster -ResourceGroupName $resourceGroupName -ClusterName $clusterName
        $defaultStorageAccount = $cluster.DefaultStorageAccount -replace '.blob.core.windows.net'
        $defaultStorageAccountKey = (Get-AzureRmStorageAccountKey -ResourceGroupName $resourceGroupName -Name $defaultStorageAccount)[0].Value
        $defaultStorageContainer = $cluster.DefaultStorageContainer
        
        Get-AzureRmHDInsightJobOutput `
            -ResourceGroupName $resourceGroupName `
            -ClusterName $clusterName `
            -HttpCredential $cred `
            -DefaultStorageAccountName $defaultStorageAccount `
            -DefaultStorageAccountKey $defaultStorageAccountKey `
            -DefaultContainer $defaultStorageContainer  `
            -JobId $mrJob.JobId `
            -DisplayOutputType StandardError

        # Download the job output to the workstation
        $storageContext = New-AzureStorageContext -StorageAccountName $defaultStorageAccount -StorageAccountKey $defaultStorageAccountKey 
        Get-AzureStorageBlobContent -Container $defaultStorageContainer -Blob example/data/WordCountOutput/part-r-00000 -Context $storageContext -Force
        
        # Display the output file
        cat ./example/data/WordCountOutput/part-r-00000 | findstr "there"

    El trabajo MapReduce genera un archivo denominado *elemento-r-00000*, que contiene palabras y la cuenta. La secuencia de comandos usa el comando **findstr** para mostrar todas las palabras que contiene *"no existe"*.

3. Establecer las 3 primeras variables y ejecutarla.

## <a name="hdinsight-sample-csharp-streaming"></a>Count - C# transmisión de Word

Hadoop proporciona una API de transmisión MapReduce, que le permite escribir mapa y reducir funciones en idiomas que no sean Java.

> [AZURE.NOTE] Los pasos de este tutorial se aplican solo a los clústeres de HDInsight basado en Windows. Para obtener un ejemplo de transmisión para clústeres basados en Linux HDInsight, consulte [programas de transmisión de desarrollar Python para HDInsight](hdinsight-hadoop-streaming-python.md).

En el ejemplo, el asignador y el reductor son ejecutables que leerá la entrada desde [origen] [ stdin-stdout-stderr] (línea por línea) y emitir el resultado para [stdout][stdin-stdout-stderr]. El programa cuenta todas las palabras en el texto.

Cuando se especifica un archivo ejecutable para **Mapeados**, cada tarea asignador inicia el archivo ejecutable como un proceso separado cuando el asignador está inicializado. Cuando se ejecuta la tarea asignador, convierte su entrada en líneas y las fuentes de las líneas a la [stdin] [ stdin-stdout-stderr] del proceso.

Mientras tanto, el asignador recopila los resultados de línea desde el stdout del proceso. Convierte cada línea en un par de clave y valor, que se recopila como el resultado del asignador. De forma predeterminada, el prefijo de una línea hasta el primer carácter de tabulación es la clave y el resto de la línea (excluido el carácter de tabulación) es el valor. Si no hay ningún carácter de tabulación en la línea, se considera línea toda la clave y el valor es null.

Cuando se especifica un archivo ejecutable para **reductores**, cada tarea reductor inicia el archivo ejecutable como un proceso separado cuando el reductor está inicializado. Mientras se ejecuta la tarea reductor, convierte sus pares de valores de clave entrada en líneas y las fuentes de las líneas a la [stdin] [ stdin-stdout-stderr] del proceso.

Mientras tanto, el reductor recopila los resultados de línea desde el [stdout] [ stdin-stdout-stderr] del proceso. Convierte cada línea en un par de clave y valor, que se recopila como el resultado de la reductor. De forma predeterminada, el prefijo de una línea hasta el primer carácter de tabulación es la clave y el resto de la línea (excluido el carácter de tabulación) es el valor.

Para obtener más información acerca de la interfaz de transmisión de Hadoop, consulte [Hadoop transmisión] [transmisión hadoop].

**Para enviar un flujo de trabajo de recuento de word C#**

- Siga el procedimiento de [recuento de palabras - Java](#word-count-java)y reemplace la definición de trabajo con la siguiente:

        $mrJobDefinition = New-AzureRmHDInsightStreamingMapReduceJobDefinition `
                                -Files "/example/apps/cat.exe","/example/apps/wc.exe" `
                                -Mapper "cat.exe" `
                                -Reducer "wc.exe" `
                                -InputPath "/example/data/gutenberg/davinci.txt" `
                                -OutputPath "/example/data/StreamingOutput/wc.txt"  


    El archivo de salida será:
    
        example/data/StreamingOutput/wc.txt/part-00000      
                                
## <a name="hdinsight-sample-pi-estimator"></a>Estimador de PI.

El Estimador de pi usa una estadística (cuasi-Monte Carlo) método para estimar el valor de pi. Puntos situadas al azar dentro de una unidad cuadrado también estén incluidos en un círculo inscribe dentro de ese cuadrado con una probabilidad igual al área del círculo, pi/4. Desde el valor de 4R, donde R es la relación entre el número de puntos que están dentro del círculo en el número total de puntos que se encuentran en el cuadrado se puede calcular el valor de pi. Cuanto mayor sea la muestra de puntos, que es mejor la estimación.

La secuencia de comandos para este ejemplo envía un trabajo de jar Hadoop y está configurado para ejecutarse con un valor 16 mapas, cada una de las cuales se requiere para calcular puntos de muestra de 10 millones por los valores de parámetro. Estos valores de parámetro se pueden cambiar para mejorar el valor estimado de pi. Para referencia, los 10 primeros decimales de pi son 3.1415926535.

**Para enviar un trabajo Estimador de PI.**

- Siga el procedimiento de [recuento de palabras - Java](#word-count-java)y reemplace la definición de trabajo con la siguiente:

        $mrJobJobDefinition = New-AzureRmHDInsightMapReduceJobDefinition `
                                    -JarFile "wasbs:///example/jars/hadoop-mapreduce-examples.jar" `
                                    -ClassName "pi" `
                                    -Arguments "16", "10000000"

## <a name="hdinsight-sample-10gb-graysort"></a>10 GB Graysort

Este ejemplo utiliza una pequeña 10GB de datos para que se pueda ejecutar relativamente rápidamente. Usa las aplicaciones MapReduce desarrolladas por Owen O'Malley y Arun Murthy que ganaron la anual comparativa de ordenación de terabyte general ("daytona") de 2009 con una tasa de 0.578 TB/min (100 TB en minutos 173). Para obtener más información sobre esta y otras pruebas de referencia de ordenación, consulte el sitio [Sortbenchmark](http://sortbenchmark.org/) .

Este ejemplo usa tres conjuntos de programas de MapReduce:

1. **TeraGen** es un programa de MapReduce que puede usar para generar las filas de datos para ordenar.
2. **TeraSort** ejemplos de los datos de entrada y usa MapReduce para ordenar los datos de forma total. TeraSort es una ordenación estándar de funciones MapReduce, salvo un particionador personalizado que usa una lista ordenada de las teclas de N-1 muestreada que defina el intervalo de clave de cada reducir. En particular, todas las claves tal que muestra [i-1] < = clave < muestra [i] se envían a reducir i. Esto garantiza que los resultados de reducen i es todos los menores de salida de reducir i + 1.
3. **TeraValidate** es un programa de MapReduce que valida que el resultado se ordena de forma global. Crea una asignación por archivo en el directorio de resultados y, a continuación, cada asignación garantiza que cada clave es menor o igual a la anterior. La función de mapa también genera registros de las claves y el apellido de cada archivo y, a continuación, la función reducir garantiza que la primera clave de archivo i es mayor que la última clave de i-1 de archivo. Los problemas aparecen como un resultado de reducir con las teclas que están en el orden correcto.

El formato de entrada y salido, usado por las tres aplicaciones lee y escribe los archivos de texto en el formato correcto. El resultado de reducir tiene replicación establecida en 1, en lugar del predeterminado 3, porque el concurso comparativa no requiere que los datos de salida se pueden replicar en varios nodos.

En el ejemplo, cada una correspondiente a uno de los programas de MapReduce descritos en la introducción requiere tres tareas:

1. Genere los datos para la ordenación, ejecute el trabajo de MapReduce **TeraGen** .
2. Ordenar los datos mediante el trabajo de MapReduce **TeraSort** .
3. Confirme que los datos se han ordenado correctamente ejecutando el trabajo **TeraValidate** MapReduce.

**Para enviar los trabajos**

- Siga el procedimiento de [recuento de palabras - Java](#word-count-java)y usar las definiciones de trabajos siguientes:

    $teragen = nuevo AzureRmHDInsightMapReduceJobDefinition `
                                -JarFile "/example/jars/hadoop-mapreduce-examples.jar" ` 
   - nombre de clase "teragen" '-argumentos "-Dmapred.map.tasks=50", "100000000", "/ ejemplo / / 10 GB ordenar-entrada de datos"
    
    $terasort = nuevo AzureRmHDInsightMapReduceJobDefinition `
                                -JarFile "/example/jars/hadoop-mapreduce-examples.jar" ` 
   - nombre de clase "terasort" '-argumentos "-Dmapred.map.tasks=50", "-Dmapred.reduce.tasks=25", "/ ejemplo / / 10 GB ordenar-entrada de datos", "/ de ejemplo y datos/10 GB-ordenar-salida"
    
    $teravalidate = nuevo AzureRmHDInsightMapReduceJobDefinition `
                                -JarFile "/example/jars/hadoop-mapreduce-examples.jar" ` 
   - nombre de clase "teravalidate" '-argumentos "-Dmapred.map.tasks=50", "-Dmapred.reduce.tasks=25", "/ ejemplo y datos/10 GB-ordenar-salida", "/ ejemplo o datos/10 GB-ordenar-validar"


##<a name="next-steps"></a>Pasos siguientes 

En este artículo y los artículos de cada uno de los ejemplos, ha aprendido a ejecutar los ejemplos incluidos con los clústeres HDInsight con PowerShell de Azure. Para obtener tutoriales sobre el uso de cerdo, subárbol y MapReduce con HDInsight, consulte los temas siguientes:

* [Empezar a usar Hadoop con subárbol en HDInsight para analizar el uso de teléfono móvil][hdinsight-get-started]
* [Usar cerdo con Hadoop en HDInsight][hdinsight-use-pig]
* [Usar subárbol con Hadoop en HDInsight][hdinsight-use-hive]
* [Enviar trabajos de Hadoop en HDInsight] [hdinsight-submit-jobs]
* [Documentación de SDK de HDInsight de Azure][hdinsight-sdk-documentation]
* [Depurar Hadoop en HDInsight: mensajes de Error] [hdinsight-errors]


## <a name="appendix-a---the-word-count-source-code"></a>Apéndice A - el código de origen del recuento de Word

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


## <a name="appendix-b---the-word-count-streaming-source-code"></a>Apéndice B - el recuento de palabras transmisión de código fuente

El programa MapReduce usa la aplicación cat.exe como una interfaz de asignación para transmitir el texto en la consola y la aplicación de wc.exe como la interfaz de reducir para contar el número de palabras que se transmite desde un documento. El asignador y el reductor leen caracteres, línea por línea de la secuencia de entrada estándar (stdin) y escribir en la secuencia de salida estándar (stdout).

    // The source code for the cat.exe (Mapper).

    using System;
    using System.IO;

    namespace cat
    {
        class cat
        {
            static void Main(string[] args)
            {
                if (args.Length > 0)
                {
                    Console.SetIn(new StreamReader(args[0]));
                }

                string line;
                while ((line = Console.ReadLine()) != null)
                {
                    Console.WriteLine(line);
                }
            }
        }
    }



El código de asignador en el archivo cat.cs utiliza [StreamReader] [ streamreader] objeto para leer los caracteres de la secuencia entrante de la consola, que a continuación, escribe la secuencia en la secuencia de salida estándar con la estática [Console.Writeline] [ console-writeline] método.


    // The source code for wc.exe (Reducer) is:

    using System;
    using System.IO;
    using System.Linq;

    namespace wc
    {
        class wc
        {
            static void Main(string[] args)
            {
                string line;
                var count = 0;

                if (args.Length > 0){
                    Console.SetIn(new StreamReader(args[0]));
                }

                while ((line = Console.ReadLine()) != null) {
                    count += line.Count(cr => (cr == ' ' || cr == '\n'));
                }
                Console.WriteLine(count);
            }
        }
    }


El código de reductor en el archivo wc.cs utiliza [StreamReader] [ streamreader] objeto para leer caracteres de la secuencia de entrada estándar que se han resultado por el asignador cat.exe. Mientras lee los caracteres con la [Console.Writeline] [ console-writeline] método, cuenta las palabras contando espacios y caracteres de fin de línea al final de cada palabra. A continuación, escribe el total en la secuencia de salida estándar con la [Console.Writeline] [ console-writeline] método.





## <a name="appendix-c---the-pi-estimator-source-code"></a>Apéndice C - el código fuente de Estimador de PI.

El código de Java que contiene las funciones asignador y reductor Estimador de pi está disponible para inspección a continuación. El programa de asignador genera un número especificado de puntos situada al azar dentro de un cuadrado de unidad y, a continuación, cuenta el número de los puntos que están dentro del círculo. El programa reductor acumula cuenta por la Mapeados de destino y, a continuación, calcula el valor de pi de la fórmula 4R, donde R es la relación entre el número de puntos que cuentan dentro del círculo en el número total de puntos que se encuentran en el cuadrado.

    /**
    * Licensed to the Apache Software Foundation (ASF) under one
    * or more contributor license agreements. See the NOTICE file
    * distributed with this work for additional information
    * regarding copyright ownership. The ASF licenses this file
    * to you under the Apache License, Version 2.0 (the
    * "License"); you may not use this file except in compliance
    * with the License. You may obtain a copy of the License at
    *
    * http://www.apache.org/licenses/LICENSE-2.0
    *
    * Unless required by applicable law or agreed to in writing, software
    * distributed under the License is distributed on an "AS IS" BASIS,
    * WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or   implied.
    * See the License for the specific language governing permissions and
    * limitations under the License.
    */

    package org.apache.hadoop.examples;

    import java.io.IOException;
    import java.math.BigDecimal;
    import java.util.Iterator;

    import org.apache.hadoop.conf.Configured;
    import org.apache.hadoop.fs.FileSystem;
    import org.apache.hadoop.fs.Path;
    import org.apache.hadoop.io.BooleanWritable;
    import org.apache.hadoop.io.LongWritable;
    import org.apache.hadoop.io.SequenceFile;
    import org.apache.hadoop.io.Writable;
    import org.apache.hadoop.io.WritableComparable;
    import org.apache.hadoop.io.SequenceFile.CompressionType;
    import org.apache.hadoop.mapred.FileInputFormat;
    import org.apache.hadoop.mapred.FileOutputFormat;
    import org.apache.hadoop.mapred.JobClient;
    import org.apache.hadoop.mapred.JobConf;
    import org.apache.hadoop.mapred.MapReduceBase;
    import org.apache.hadoop.mapred.Mapper;
    import org.apache.hadoop.mapred.OutputCollector;
    import org.apache.hadoop.mapred.Reducer;
    import org.apache.hadoop.mapred.Reporter;
    import org.apache.hadoop.mapred.SequenceFileInputFormat;
    import org.apache.hadoop.mapred.SequenceFileOutputFormat;
    import org.apache.hadoop.util.Tool;
    import org.apache.hadoop.util.ToolRunner;


    //A Map-reduce program to estimate the value of Pi
    //using quasi-Monte Carlo method.
    //
    //Mapper:
    //Generate points in a unit square
    //and then count points inside/outside of the inscribed circle of the square.
    //
    //Reducer:
    //Accumulate points inside/outside results from the mappers.
    //Let numTotal = numInside + numOutside.
    //The fraction numInside/numTotal is a rational approximation of
    //the value (Area of the circle)/(Area of the square),
    //where the area of the inscribed circle is Pi/4
    //and the area of unit square is 1.
    //Then, Pi is estimated value to be 4(numInside/numTotal).
    //

    public class PiEstimator extends Configured implements Tool {
    //tmp directory for input/output
    static private final Path TMP_DIR = new Path(
    PiEstimator.class.getSimpleName() + "_TMP_3_141592654");

    //2-dimensional Halton sequence {H(i)},
    //where H(i) is a 2-dimensional point and i >= 1 is the index.
    //Halton sequence is used to generate sample points for Pi estimation.
    private static class HaltonSequence {
    // Bases
    static final int[] P = {2, 3};
    //Maximum number of digits allowed
    static final int[] K = {63, 40};

    private long index;
    private double[] x;
    private double[][] q;
    private int[][] d;

    //Initialize to H(startindex),
    //so the sequence begins with H(startindex+1).
    HaltonSequence(long startindex) {
    index = startindex;
    x = new double[K.length];
    q = new double[K.length][];
    d = new int[K.length][];
    for(int i = 0; i < K.length; i++) {
    q[i] = new double[K[i]];
    d[i] = new int[K[i]];
    }

    for(int i = 0; i < K.length; i++) {
    long k = index;
    x[i] = 0;

    for(int j = 0; j < K[i]; j++) {
    q[i][j] = (j == 0? 1.0: q[i][j-1])/P[i];
    d[i][j] = (int)(k % P[i]);
    k = (k - d[i][j])/P[i];
    x[i] += d[i][j] * q[i][j];
    }
    }
    }

    //Compute next point.
    //Assume the current point is H(index).
    //Compute H(index+1).
    //@return a 2-dimensional point with coordinates in [0,1)^2
    double[] nextPoint() {
    index++;
    for(int i = 0; i < K.length; i++) {
    for(int j = 0; j < K[i]; j++) {
    d[i][j]++;
    x[i] += q[i][j];
    if (d[i][j] < P[i]) {
    break;
    }
    d[i][j] = 0;
    x[i] -= (j == 0? 1.0: q[i][j-1]);
    }
    }
    return x;
    }
    }

    //Mapper class for Pi estimation.
    //Generate points in a unit square and then
    //count points inside/outside of the inscribed circle of the square.
    public static class PiMapper extends MapReduceBase
    implements Mapper<LongWritable, LongWritable, BooleanWritable, LongWritable> {

    //Map method.
    //@param offset samples starting from the (offset+1)th sample.
    //@param size the number of samples for this map
    //@param out output {ture->numInside, false->numOutside}
    //@param reporter
    public void map(LongWritable offset,
    LongWritable size,
    OutputCollector<BooleanWritable, LongWritable> out,
    Reporter reporter) throws IOException {

    final HaltonSequence haltonsequence = new HaltonSequence(offset.get());
    long numInside = 0L;
    long numOutside = 0L;

    for(long i = 0; i < size.get(); ) {
    //generate points in a unit square
    final double[] point = haltonsequence.nextPoint();

    //count points inside/outside of the inscribed circle of the square
    final double x = point[0] - 0.5;
    final double y = point[1] - 0.5;
    if (x*x + y*y > 0.25) {
    numOutside++;
    } else {
    numInside++;
    }

    //report status
    i++;
    if (i % 1000 == 0) {
    reporter.setStatus("Generated " + i + " samples.");
    }
    }

    //output map results
    out.collect(new BooleanWritable(true), new LongWritable(numInside));
    out.collect(new BooleanWritable(false), new LongWritable(numOutside));
    }
    }


    //Reducer class for Pi estimation.
    //Accumulate points inside/outside results from the mappers.
    public static class PiReducer extends MapReduceBase
    implements Reducer<BooleanWritable, LongWritable, WritableComparable<?>, Writable> {

    private long numInside = 0;
    private long numOutside = 0;
    private JobConf conf; //configuration for accessing the file system

    //Store job configuration.
    @Override
    public void configure(JobConf job) {
    conf = job;
    }


    // Accumulate number of points inside/outside results from the mappers.
    // @param isInside Is the points inside?
    // @param values An iterator to a list of point counts
    // @param output dummy, not used here.
    // @param reporter

    public void reduce(BooleanWritable isInside,
    Iterator<LongWritable> values,
    OutputCollector<WritableComparable<?>, Writable> output,
    Reporter reporter) throws IOException {
    if (isInside.get()) {
    for(; values.hasNext(); numInside += values.next().get());
    } else {
    for(; values.hasNext(); numOutside += values.next().get());
    }
    }

    //Reduce task done, write output to a file.
    @Override
    public void close() throws IOException {
    //write output to a file
    Path outDir = new Path(TMP_DIR, "out");
    Path outFile = new Path(outDir, "reduce-out");
    FileSystem fileSys = FileSystem.get(conf);
    SequenceFile.Writer writer = SequenceFile.createWriter(fileSys, conf,
    outFile, LongWritable.class, LongWritable.class,
    CompressionType.NONE);
    writer.append(new LongWritable(numInside), new LongWritable(numOutside));
    writer.close();
    }
    }

    //Run a map/reduce job for estimating Pi.
    //@return the estimated value of Pi.
    public static BigDecimal estimate(int numMaps, long numPoints, JobConf jobConf
    )
    throws IOException {
    //setup job conf
    jobConf.setJobName(PiEstimator.class.getSimpleName());

    jobConf.setInputFormat(SequenceFileInputFormat.class);

    jobConf.setOutputKeyClass(BooleanWritable.class);
    jobConf.setOutputValueClass(LongWritable.class);
    jobConf.setOutputFormat(SequenceFileOutputFormat.class);

    jobConf.setMapperClass(PiMapper.class);
    jobConf.setNumMapTasks(numMaps);

    jobConf.setReducerClass(PiReducer.class);
    jobConf.setNumReduceTasks(1);

    // turn off speculative execution, because DFS doesn't handle
    // multiple writers to the same file.
    jobConf.setSpeculativeExecution(false);

    //setup input/output directories
    final Path inDir = new Path(TMP_DIR, "in");
    final Path outDir = new Path(TMP_DIR, "out");
    FileInputFormat.setInputPaths(jobConf, inDir);
    FileOutputFormat.setOutputPath(jobConf, outDir);

    final FileSystem fs = FileSystem.get(jobConf);
    if (fs.exists(TMP_DIR)) {
     throw new IOException("Tmp directory " + fs.makeQualified(TMP_DIR)
     + " already exists. Please remove it first.");
     }
     if (!fs.mkdirs(inDir)) {
     throw new IOException("Cannot create input directory " + inDir);
     }

     //generate an input file for each map task
     try {
     for(int i=0; i < numMaps; ++i) {
     final Path file = new Path(inDir, "part"+i);
     final LongWritable offset = new LongWritable(i * numPoints);
     final LongWritable size = new LongWritable(numPoints);
     final SequenceFile.Writer writer = SequenceFile.createWriter(
     fs, jobConf, file,
     LongWritable.class, LongWritable.class, CompressionType.NONE);
     try {
     writer.append(offset, size);
     } finally {
     writer.close();
     }
     System.out.println("Wrote input for Map #"+i);
     }

     //start a map/reduce job
     System.out.println("Starting Job");
     final long startTime = System.currentTimeMillis();
     JobClient.runJob(jobConf);
     final double duration = (System.currentTimeMillis() - startTime)/1000.0;
     System.out.println("Job Finished in " + duration + " seconds");

     //read outputs
     Path inFile = new Path(outDir, "reduce-out");
     LongWritable numInside = new LongWritable();
     LongWritable numOutside = new LongWritable();
     SequenceFile.Reader reader = new SequenceFile.Reader(fs, inFile, jobConf);
     try {
     reader.next(numInside, numOutside);
     } finally {
     reader.close();
     }

     //compute estimated value
     return BigDecimal.valueOf(4).setScale(20)
     .multiply(BigDecimal.valueOf(numInside.get()))
     .divide(BigDecimal.valueOf(numMaps))
     .divide(BigDecimal.valueOf(numPoints));
     } finally {
     fs.delete(TMP_DIR, true);
     }
     }

    //Parse arguments and then runs a map/reduce job.
    //Print output in standard out.
    //@return a non-zero if there is an error. Otherwise, return 0.
     public int run(String[] args) throws Exception {
     if (args.length != 2) {
     System.err.println("Usage: "+getClass().getName()+" <nMaps> <nSamples>");
     ToolRunner.printGenericCommandUsage(System.err);
     return -1;
     }

     final int nMaps = Integer.parseInt(args[0]);
     final long nSamples = Long.parseLong(args[1]);

     System.out.println("Number of Maps = " + nMaps);
     System.out.println("Samples per Map = " + nSamples);

     final JobConf jobConf = new JobConf(getConf(), getClass());
     System.out.println("Estimated value of Pi is "
     + estimate(nMaps, nSamples, jobConf));
     return 0;
     }

     //main method for running it as a stand alone command.
     public static void main(String[] argv) throws Exception {
     System.exit(ToolRunner.run(null, new PiEstimator(), argv));
     }
     }
     
## <a name="appendix-d---the-10gb-graysort-source-code"></a>Apéndice D - el código fuente de graysort de 10gb

El código para el programa TeraSort MapReduce se presenta para inspección en esta sección.


    /**
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

    package org.apache.hadoop.examples.terasort;

    import java.io.IOException;
    import java.io.PrintStream;
    import java.net.URI;
    import java.util.ArrayList;
    import java.util.List;

    import org.apache.commons.logging.Log;
    import org.apache.commons.logging.LogFactory;
    import org.apache.hadoop.conf.Configured;
    import org.apache.hadoop.filecache.DistributedCache;
    import org.apache.hadoop.fs.FileSystem;
    import org.apache.hadoop.fs.Path;
    import org.apache.hadoop.io.NullWritable;
    import org.apache.hadoop.io.SequenceFile;
    import org.apache.hadoop.io.Text;
    import org.apache.hadoop.mapred.FileOutputFormat;
    import org.apache.hadoop.mapred.JobClient;
    import org.apache.hadoop.mapred.JobConf;
    import org.apache.hadoop.mapred.Partitioner;
    import org.apache.hadoop.util.Tool;
    import org.apache.hadoop.util.ToolRunner;

    /**
     * Generates the sampled split points, launches the job,
     * and waits for it to finish.
     * <p>
     * To run the program:
     * <b>bin/hadoop jar hadoop-examples-*.jar terasort in-dir out-dir</b>
     */

    public class TeraSort extends Configured implements Tool {
      private static final Log LOG = LogFactory.getLog(TeraSort.class);

      /**
       * A partitioner that splits text keys into roughly equal
       * partitions in a global sorted order.
       */

      static class TotalOrderPartitioner implements Partitioner<Text,Text>{
        private TrieNode trie;
        private Text[] splitPoints;

        /**
         * A generic trie node
         */
        static abstract class TrieNode {
          private int level;
          TrieNode(int level) {
            this.level = level;
          }
          abstract int findPartition(Text key);
          abstract void print(PrintStream strm) throws IOException;
          int getLevel() {
            return level;
          }
        }

        /**
         * An inner trie node that contains 256 children based on the next
         * character.
         */
        static class InnerTrieNode extends TrieNode {
          private TrieNode[] child = new TrieNode[256];

          InnerTrieNode(int level) {
            super(level);
          }
          int findPartition(Text key) {
            int level = getLevel();
            if (key.getLength() <= level) {
              return child[0].findPartition(key);
            }
            return child[key.getBytes()[level]].findPartition(key);
          }
          void setChild(int idx, TrieNode child) {
            this.child[idx] = child;
          }
          void print(PrintStream strm) throws IOException {
            for(int ch=0; ch < 255; ++ch) {
              for(int i = 0; i < 2*getLevel(); ++i) {
                strm.print(' ');
              }
              strm.print(ch);
              strm.println(" ->");
              if (child[ch] != null) {
                child[ch].print(strm);
              }
            }
          }
        }

        /**
         * A leaf trie node that does string compares to figure out where the given
         * key belongs between lower..upper.
         */
        static class LeafTrieNode extends TrieNode {
          int lower;
          int upper;
          Text[] splitPoints;
          LeafTrieNode(int level, Text[] splitPoints, int lower, int upper) {
            super(level);
            this.splitPoints = splitPoints;
            this.lower = lower;
            this.upper = upper;
          }
          int findPartition(Text key) {
            for(int i=lower; i<upper; ++i) {
              if (splitPoints[i].compareTo(key) >= 0) {
                return i;
              }
            }
            return upper;
          }
          void print(PrintStream strm) throws IOException {
            for(int i = 0; i < 2*getLevel(); ++i) {
              strm.print(' ');
            }
            strm.print(lower);
            strm.print(", ");
            strm.println(upper);
          }
        }


        /**
         * Read the cut points from the given sequence file.
         * @param fs the file system
         * @param p the path to read
         * @param job the job config
         * @return the strings to split the partitions on
         * @throws IOException
         */
        private static Text[] readPartitions(FileSystem fs, Path p,
                                             JobConf job) throws IOException {
          SequenceFile.Reader reader = new SequenceFile.Reader(fs, p, job);
          List<Text> parts = new ArrayList<Text>();
          Text key = new Text();
          NullWritable value = NullWritable.get();
          while (reader.next(key, value)) {
            parts.add(key);
            key = new Text();
          }
          reader.close();
          return parts.toArray(new Text[parts.size()]);  
        }

        /**
         * Given a sorted set of cut points, build a trie that will find the correct
         * partition quickly.
         * @param splits the list of cut points
         * @param lower the lower bound of partitions 0..numPartitions-1
         * @param upper the upper bound of partitions 0..numPartitions-1
         * @param prefix the prefix that we have already checked against
         * @param maxDepth the maximum depth we will build a trie for
         * @return the trie node that will divide the splits correctly
         */
        private static TrieNode buildTrie(Text[] splits, int lower, int upper,
                                          Text prefix, int maxDepth) {
          int depth = prefix.getLength();
          if (depth >= maxDepth || lower == upper) {
            return new LeafTrieNode(depth, splits, lower, upper);
          }
          InnerTrieNode result = new InnerTrieNode(depth);
          Text trial = new Text(prefix);
          // append an extra byte on to the prefix
          trial.append(new byte[1], 0, 1);
          int currentBound = lower;
          for(int ch = 0; ch < 255; ++ch) {
            trial.getBytes()[depth] = (byte) (ch + 1);
            lower = currentBound;
            while (currentBound < upper) {
              if (splits[currentBound].compareTo(trial) >= 0) {
                break;
              }
              currentBound += 1;
            }
            trial.getBytes()[depth] = (byte) ch;
            result.child[ch] = buildTrie(splits, lower, currentBound, trial,
                                         maxDepth);
          }
          // pick up the rest
          trial.getBytes()[depth] = 127;
          result.child[255] = buildTrie(splits, currentBound, upper, trial,
                                        maxDepth);
          return result;
        }

        public void configure(JobConf job) {
          try {
            FileSystem fs = FileSystem.getLocal(job);
            Path partFile = new Path(TeraInputFormat.PARTITION_FILENAME);
            splitPoints = readPartitions(fs, partFile, job);
            trie = buildTrie(splitPoints, 0, splitPoints.length, new Text(), 2);
          } catch (IOException ie) {
            throw new IllegalArgumentException("can't read paritions file", ie);
          }
        }

        public TotalOrderPartitioner() {
        }

        public int getPartition(Text key, Text value, int numPartitions) {
          return trie.findPartition(key);
        }

      }

      public int run(String[] args) throws Exception {
        LOG.info("starting");
        JobConf job = (JobConf) getConf();
        Path inputDir = new Path(args[0]);
        inputDir = inputDir.makeQualified(inputDir.getFileSystem(job));
        Path partitionFile = new Path(inputDir, TeraInputFormat.PARTITION_FILENAME);
        URI partitionUri = new URI(partitionFile.toString() +
                                   "#" + TeraInputFormat.PARTITION_FILENAME);
        TeraInputFormat.setInputPaths(job, new Path(args[0]));
        FileOutputFormat.setOutputPath(job, new Path(args[1]));
        job.setJobName("TeraSort");
        job.setJarByClass(TeraSort.class);
        job.setOutputKeyClass(Text.class);
        job.setOutputValueClass(Text.class);
        job.setInputFormat(TeraInputFormat.class);
        job.setOutputFormat(TeraOutputFormat.class);
        job.setPartitionerClass(TotalOrderPartitioner.class);
        TeraInputFormat.writePartitionFile(job, partitionFile);
        DistributedCache.addCacheFile(partitionUri, job);
        DistributedCache.createSymlink(job);
        job.setInt("dfs.replication", 1);
        TeraOutputFormat.setFinalSync(job, true);
        JobClient.runJob(job);
        LOG.info("done");
        return 0;
      }

      /**
       * @param args
       */

      public static void main(String[] args) throws Exception {
        int res = ToolRunner.run(new JobConf(), new TeraSort(), args);
        System.exit(res);
      }
    }














 




[hdinsight-errors]: hdinsight-debug-jobs.md

[hdinsight-sdk-documentation]: https://msdn.microsoft.com/library/azure/dn479185.aspx

[hdinsight-submit-jobs]: hdinsight-submit-hadoop-jobs-programmatically.md
[hdinsight-introduction]: hdinsight-hadoop-introduction.md


[powershell-install-configure]: ../powershell-install-configure.md

[hdinsight-get-started]: hdinsight-hadoop-linux-tutorial-get-started.md

[hdinsight-samples]: hdinsight-run-samples.md
[hdinsight-sample-10gb-graysort]: #hdinsight-sample-10gb-graysort
[hdinsight-sample-csharp-streaming]: #hdinsight-sample-csharp-streaming
[hdinsight-sample-pi-estimator]: #hdinsight-sample-pi-estimator
[hdinsight-sample-wordcount]: #hdinsight-sample-wordcount

[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-pig]: hdinsight-use-pig.md

[streamreader]: http://msdn.microsoft.com/library/system.io.streamreader.aspx
[console-writeline]: http://msdn.microsoft.com/library/system.console.writeline
[stdin-stdout-stderr]: https://msdn.microsoft.com/library/3x292kth.aspx
