<properties
    pageTitle="Ejecutar Hadoop MapReduce ejemplos en HDInsight de Linux | Microsoft Azure"
    description="Introducción al uso MapReduce ejemplos con HDInsight de Linux. Usar SSH para conectarse al clúster y luego use el comando Hadoop para ejecutar trabajos de ejemplo."
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
    ms.date="09/27/2016"
    ms.author="larryfr"/>




#<a name="run-the-hadoop-samples-in-hdinsight"></a>Ejecutar los ejemplos de Hadoop en HDInsight

[AZURE.INCLUDE [samples-selector](../../includes/hdinsight-run-samples-selector.md)]

Clústeres de HDInsight basados en Linux proporcionan un conjunto de muestras MapReduce que se pueden usar para familiarizarse con la ejecución de Hadoop MapReduce trabajos. En este documento, obtenga más información sobre los ejemplos disponibles y repase la ejecución de algunas de ellas.

##<a name="prerequisites"></a>Requisitos previos

- **Suscripción de un Azure**: vea la [versión de prueba gratuita de obtener Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)

- **HDInsight basados en Linux un clúster**: vea [Introducción al uso de Hadoop con subárbol en HDInsight en Linux](hdinsight-hadoop-linux-tutorial-get-started.md)

- **SSH un cliente**: para obtener información sobre cómo utilizar SSH con HDInsight, consulte los artículos siguientes:

    - [Utilizar SSH con basados en Linux Hadoop en HDInsight de Linux, Unix o OS X](hdinsight-hadoop-linux-use-ssh-unix.md)

    - [Utilizar SSH con basados en Linux Hadoop en HDInsight de Windows.](hdinsight-hadoop-linux-use-ssh-windows.md)

## <a name="the-samples"></a>Los ejemplos ##

**Ubicación**: los ejemplos se encuentran en el clúster de HDInsight en **/usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar**

**Contenido**: los siguientes ejemplos se encuentran en este archivo:

- **aggregatewordcount**: programa asignación/reducción que cuenta las palabras en los archivos de entrada un agregado basado en
- **aggregatewordhist**: programa asignación/reducción que calcula el histograma de las palabras en los archivos de entrada un agregado basado en
- **bbP**: un programa de asignación/reducción Bailey-Borwein-Plouffe se usa para calcular los dígitos exactos de PI.
- **dbcount**: un trabajo de ejemplo que cuenta los registros de la vista de página almacenados en un una base de datos
- **distbbp**: un programa o reducir el mapa que usa una fórmula de tipo BBP para calcular bits exactos de PI.
- **grep**: un programa o reducir el mapa que cuenta las coincidencias de regex en la entrada
- **combinación**: un trabajo que una combinación de efectos más ordenada, igualmente particiones conjuntos de datos
- **multifilewc**: un trabajo que cuenta las palabras de varios archivos
- **pentomino**: asignación/reducción puesta programa para buscar soluciones a problemas de pentomino en mosaico
- **PI**: un programa o reducir el mapa que calcula Pi usando un cuasi-Monte método Carlo
- **randomtextwriter**: un programa o reducir el mapa que escribe 10 GB de datos de texto aleatorias por nodo
- **randomwriter**: un programa o reducir el mapa que escribe 10 GB de datos aleatorios por nodo
- **secondarySort**: un ejemplo que define una ordenación secundaria para reducir
- **Ordenar**: un programa o reducir el mapa que ordena los datos escritos por el escritor aleatorio
- **Sudoku**: un solver sudoku
- **teragen**: generar datos para la terasort
- **terasort**: ejecutar el terasort
- **teravalidate**: comprobar resultados de terasort
- **WordCount**: un programa o reducir el mapa que cuenta las palabras en los archivos de entrada
- **wordmean**: un programa o reducir el mapa que cuente la longitud media de las palabras en los archivos de entrada
- **wordmedian**: un programa o reducir el mapa que cuente la longitud media de las palabras en los archivos de entrada
- **wordstandarddeviation**: un programa o reducir el mapa que cuenta la desviación estándar de la longitud de las palabras en los archivos de entrada

**Código fuente**: código fuente de estos ejemplos se incluye en el clúster de HDInsight en **/usr/hdp/2.2.4.9-1/hadoop/src/hadoop-mapreduce-project/hadoop-mapreduce-examples**

> [AZURE.NOTE] La `2.2.4.9-1` en la ruta es la versión de la plataforma de datos de Hortonworks para el clúster HDInsight y puede cambiar mientras se actualiza HDInsight.

## <a name="how-to-run-the-samples"></a>Cómo ejecutar los ejemplos ##

1. Conectar con HDInsight mediante SSH como se describe en los siguientes artículos:

    - [Utilizar SSH con basados en Linux Hadoop en HDInsight de Linux, Unix o OS X](hdinsight-hadoop-linux-use-ssh-unix.md)

    - [Utilizar SSH con basados en Linux Hadoop en HDInsight de Windows.](hdinsight-hadoop-linux-use-ssh-windows.md)

2. Desde el `username@#######:~$` preguntar, use el comando siguiente para obtener una lista de los ejemplos:

        yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar

    Esto genera la lista de ejemplo de la sección anterior de este documento.

3. Use el comando siguiente para obtener ayuda sobre una muestra específica. En este caso, el ejemplo de **wordcount** :

        yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar wordcount

    Debe aparecer el siguiente mensaje:

        Usage: wordcount <in> [<in>...] <out>

    Esto indica que puede proporcionar varios trazados de entrada para los documentos de origen. La ruta de acceso final es donde se almacena el resultado (recuento de palabras en los documentos de origen).

4. Use las siguientes acciones para contar todas las palabras en los blocs de notas de Leonardo Da Vinci, que se proporcionan como datos de ejemplo con el clúster:

        yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar wordcount /example/data/gutenberg/davinci.txt /example/data/davinciwordcount

    Entrada para este trabajo se lee desde **wasbs:///example/data/gutenberg/davinci.txt**.

    Salida para este ejemplo se almacena en **wasbs: / / / ejemplo/datos/davinciwordcount**.

    > [AZURE.NOTE] Como se indica en la ayuda para el ejemplo de wordcount, también puede especificar varios archivos de entrada. Por ejemplo, `hadoop jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar wordcount /example/data/gutenberg/davinci.txt /example/data/gutenberg/ulysses.txt /example/data/twowordcount` desea contar palabras en davinci.txt y ulysses.txt.

5. Una vez completada la tarea, use el comando siguiente para ver el resultado:

        hdfs dfs -cat /example/data/davinciwordcount/*

    Concatena todos los archivos de salida producidos por el trabajo y mostrarlos. Este ejemplo básico hay un solo archivo, pero si hay más este comando ¿iteración todos ellos.

    El resultado es similar a la siguiente:

        zum     1
        zur     1
        zwanzig 1
        zweite  1

    Cada línea representa una palabra y cuántas veces se produjo en los datos de entrada.

## <a name="sudoku"></a>Sudoku

El ejemplo de Sudoku tiene instrucciones uso algo inútil; "Incluir un rompecabezas en la línea de comandos".

[Sudoku](https://en.wikipedia.org/wiki/Sudoku) es un rompecabezas lógica conformado de 3 x nueve 3 cuadrículas. Algunas celdas de la cuadrícula tienen números, mientras otras personas están en blanco y, a continuación, el objetivo es resolver de las celdas en blanco. El vínculo anterior tiene más información sobre el rompecabezas, pero el propósito de este ejemplo es resolver de las celdas en blanco. Así que nuestra entrada debe ser un archivo que se encuentra en el siguiente formato:

- Nueve filas de nueve columnas

- Cada columna puede contener un número o `?` (lo que indica una celda en blanco)

- Las celdas están separadas por un espacio en blanco

Hay una determinada manera para construir rompecabezas Sudoku; no se puede repetir un número en una columna o fila. Hay un ejemplo en el clúster de HDInsight se construye correctamente. Se encuentra en **/usr/hdp/2.2.4.9-1/hadoop/src/hadoop-mapreduce-project/hadoop-mapreduce-examples/src/main/java/org/apache/hadoop/examples/dancing/puzzle1.dta** y contiene lo siguiente:

    8 5 ? 3 9 ? ? ? ?
    ? ? 2 ? ? ? ? ? ?
    ? ? 6 ? 1 ? ? ? 2
    ? ? 4 ? ? 3 ? 5 9
    ? ? 8 9 ? 1 4 ? ?
    3 2 ? 4 ? ? 8 ? ?
    9 ? ? ? 8 ? 5 ? ?
    ? ? ? ? ? ? 2 ? ?
    ? ? ? ? 4 5 ? 7 8

> [AZURE.NOTE] La `2.2.4.9-1` puede cambiar parte de la ruta como las actualizaciones se realizan en el clúster de HDInsight.

Para ejecutar este ejemplo de Sudoku, utilice el siguiente comando:

    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar sudoku /usr/hdp/2.2.9.1-1/hadoop/src/hadoop-mapreduce-project/hadoop-mapreduce-examples/src/main/java/org/apache/hadoop/examples/dancing/puzzle1.dta

Los resultados deben aparecer similares al siguiente:

    8 5 1 3 9 2 6 4 7
    4 3 2 6 7 8 1 9 5
    7 9 6 5 1 4 3 8 2
    6 1 4 8 2 3 7 5 9
    5 7 8 9 6 1 4 2 3
    3 2 9 4 5 7 8 1 6
    9 4 7 2 8 6 5 3 1
    1 8 5 7 3 9 2 6 4
    2 6 3 1 4 5 9 7 8

## <a name="pi-"></a>PI (π)

En el ejemplo de pi utiliza una estadística (cuasi-Monte Carlo) método para estimar el valor de pi. Puntos situadas al azar dentro de una unidad cuadrado también estén incluidos en un círculo inscribe dentro de ese cuadrado con una probabilidad igual al área del círculo, pi/4. Desde el valor de 4R, donde R es la relación entre el número de puntos que están dentro del círculo en el número total de puntos que se encuentran en el cuadrado se puede calcular el valor de pi. Cuanto mayor sea la muestra de puntos, que es mejor la estimación.

El asignador de este ejemplo genera un número de puntos al azar dentro de un cuadrado de unidad y, a continuación, cuenta el número de los puntos que están dentro del círculo.

A continuación, el reductor acumula puntos cuenta por la Mapeados y calcula el valor de pi de la fórmula 4R, donde R es la relación entre el número de puntos que cuentan dentro del círculo en el número total de puntos que se encuentran en el cuadrado.

Use el comando siguiente para ejecutar este ejemplo. Esto usa 16 mapas con 10.000.000 ejemplos para estimar el valor de pi:

    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar pi 16 10000000

El valor devuelto por este debe ser similar a **3.14159155000000000000**. Para las referencias, las posiciones 10 primeros decimales de pi son 3.1415926535.

##<a name="10gb-greysort"></a>10GB Greysort

GraySort es una especie de referencia cuya métrica es la tasa de ordenación (TB por minuto) que se logra al ordenar grandes cantidades de datos, normalmente un 100TB mínimos.

Este ejemplo utiliza una pequeña 10GB de datos para que se pueda ejecutar relativamente rápidamente. Usa las aplicaciones MapReduce desarrolladas por Owen O'Malley y Arun Murthy que ganaron la anual comparativa de ordenación de terabyte general ("daytona") de 2009 con una tasa de 0.578 TB/min (100 TB en minutos 173). Para obtener más información sobre esta y otras pruebas de referencia de ordenación, consulte el sitio [Sortbenchmark](http://sortbenchmark.org/) .

Este ejemplo usa tres conjuntos de programas de MapReduce:

- **TeraGen**: un programa de MapReduce que genera filas de datos para ordenar

- **TeraSort**: ejemplos de los datos de entrada y usa MapReduce para ordenar los datos en un orden total

    TeraSort es una ordenación estándar de funciones MapReduce, salvo un particionador personalizado que usa una lista ordenada de las teclas de N-1 muestreada que defina el intervalo de clave de cada reducir. En particular, todas las claves tal que muestra [i-1] < = clave < muestra [i] se envían a reducir i. Esto garantiza que los resultados de reducen i es todos los menores de salida de reducir i + 1.

- **TeraValidate**: un programa de MapReduce que valida que el resultado se ordena globalmente

    Crea una asignación por archivo en el directorio de resultados y, a continuación, cada asignación garantiza que cada clave es menor o igual a la anterior. La función de mapa también genera registros de las claves y el apellido de cada archivo y, a continuación, la función reducir garantiza que la primera clave de archivo i es mayor que la última clave de i-1 de archivo. Los problemas aparecen como un resultado de reducir con las teclas que están en el orden correcto.

Utilice los siguientes pasos para generar datos, ordenar y, a continuación, valide el resultado:

1. Generar 10GB de datos, que se almacenarán con el almacenamiento de predeterminado del clúster de HDInsight en **wasbs: / / / ejemplo / / 10GB ordenar-entrada de datos**:

        yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar teragen -Dmapred.map.tasks=50 100000000 /example/data/10GB-sort-input

    La `-Dmapred.map.tasks` indica Hadoop cuántas tareas de mapa para este trabajo. Los dos parámetros finales indicar el trabajo para crear 10GB de datos y guardarla en **wasbs: / / / ejemplo / / 10GB ordenar-entrada de datos**.

2. Use el comando siguiente para ordenar los datos:

        yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar terasort -Dmapred.map.tasks=50 -Dmapred.reduce.tasks=25 /example/data/10GB-sort-input /example/data/10GB-sort-output

    La `-Dmapred.reduce.tasks` indica Hadoop cuántas reducen las tareas que se utilizará para el trabajo. Los dos parámetros finales son solo las ubicaciones de entrada y salidas de datos.

3. Use las siguientes acciones para validar los datos generados por el criterio de ordenación:

        yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar teravalidate -Dmapred.map.tasks=50 -Dmapred.reduce.tasks=25 /example/data/10GB-sort-output /example/data/10GB-sort-validate

##<a name="next-steps"></a>Pasos siguientes ##

En este artículo, ha aprendido cómo ejecutar los ejemplos incluidos en los clústeres HDInsight basados en Linux. Para obtener tutoriales sobre el uso de cerdo, subárbol y MapReduce con HDInsight, consulte los temas siguientes:

* [Usar cerdo con Hadoop en HDInsight][hdinsight-use-pig]
* [Usar subárbol con Hadoop en HDInsight][hdinsight-use-hive]
* [Usar MapReduce con Hadoop en HDInsight] [hdinsight-use-mapreduce]



[hdinsight-errors]: hdinsight-debug-jobs.md
[hdinsight-use-mapreduce]: hdinsight-use-mapreduce.md
[hdinsight-sdk-documentation]: https://msdn.microsoft.com/library/azure/dn479185.aspx

[hdinsight-submit-jobs]: hdinsight-submit-hadoop-jobs-programmatically.md
[hdinsight-introduction]: hdinsight-hadoop-introduction.md



[hdinsight-samples]: hdinsight-run-samples.md

[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-pig]: hdinsight-use-pig.md
