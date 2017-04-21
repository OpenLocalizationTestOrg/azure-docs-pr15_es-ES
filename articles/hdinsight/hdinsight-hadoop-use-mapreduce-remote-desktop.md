<properties
   pageTitle="MapReduce y escritorio remoto con Hadoop en HDInsight | Microsoft Azure"
   description="Obtenga información sobre cómo usar Escritorio remoto para conectarse a Hadoop en HDInsight y ejecutar MapReduce trabajos."
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
   ms.date="09/27/2016"
   ms.author="larryfr"/>

# <a name="use-mapreduce-in-hadoop-on-hdinsight-with-remote-desktop"></a>Usar MapReduce en Hadoop en HDInsight con Escritorio remoto

[AZURE.INCLUDE [mapreduce-selector](../../includes/hdinsight-selector-use-mapreduce.md)]

En este artículo, aprenderá a conectarse a un Hadoop en clúster HDInsight con Escritorio remoto y, a continuación, ejecutar MapReduce trabajos mediante el comando Hadoop.

##<a id="prereq"></a>Requisitos previos

Para completar los pasos de este artículo, necesita lo siguiente:

* Un clúster basado en Windows HDInsight (Hadoop en HDInsight)

* Un equipo cliente con Windows 10, Windows 8 o Windows 7

##<a id="connect"></a>Conectar con Escritorio remoto

Habilitar Escritorio remoto para el clúster HDInsight y luego conectarse siguiendo las instrucciones en [conectarse a clústeres HDInsight con RDP](hdinsight-administer-use-management-portal.md#rdp).

##<a id="hadoop"></a>Use el comando Hadoop

Cuando está conectado a la versión de escritorio para el clúster de HDInsight, realice los pasos siguientes para ejecutar un trabajo MapReduce mediante el comando Hadoop:

1. En el escritorio HDInsight, comience a la **línea de comandos de Hadoop**. Se abrirá un nuevo símbolo en la **c:\apps\dist\hadoop-&lt;número de versión >** directorio.

    > [AZURE.NOTE] El número de versión cambia cuando se actualiza Hadoop. La variable de entorno **HADOOP_HOME** puede utilizarse para buscar la ruta de acceso. Por ejemplo, `cd %HADOOP_HOME%` cambios de directorios en el directorio Hadoop, sin necesidad de conocer el número de versión.

2. Para usar el comando **Hadoop** para ejecutar un trabajo de ejemplo MapReduce, utilice el siguiente comando:

        hadoop jar hadoop-mapreduce-examples.jar wordcount wasbs:///example/data/gutenberg/davinci.txt wasbs:///example/data/WordCountOutput

    Se inicia la clase **wordcount** , que se encuentra en el archivo de **hadoop-mapreduce-examples.jar** en el directorio actual. Como entrada, utiliza el documento de **wasbs://example/data/gutenberg/davinci.txt** y salida se almacena en: **wasbs: / / / ejemplo/datos/WordCountOutput**.

    > [AZURE.NOTE] Para obtener más información acerca de este trabajo MapReduce y los datos de ejemplo, vea <a href="hdinsight-use-mapreduce.md">Usar MapReduce en HDInsight Hadoop</a>.

2. El trabajo emite detalles mientras se procesa y devuelve información similar al siguiente cuando se complete la tarea:

        File Input Format Counters
        Bytes Read=1395666
        File Output Format Counters
        Bytes Written=337623

3. Una vez completada la tarea, use el comando siguiente para mostrar los archivos de salida almacenados en **wasbs://example/data/WordCountOutput**:

        hadoop fs -ls wasbs:///example/data/WordCountOutput

    Debe mostrar de dos archivos, **_SUCCESS** y **elemento-r-00000**. El archivo de **elemento-r-00000** contiene la salida de esta tarea.

    > [AZURE.NOTE] Algunos trabajos MapReduce pueden dividir los resultados en varios archivos de **elemento-r-###** . Si es así, utilice el ### sufijo para indicar el orden de los archivos.

4. Para ver el resultado, use el siguiente comando:

        hadoop fs -cat wasbs:///example/data/WordCountOutput/part-r-00000

    Esto muestra una lista de las palabras contenidas en el archivo **wasbs://example/data/gutenberg/davinci.txt** , junto con el número de veces que se ha producido de cada palabra. A continuación se muestra un ejemplo de los datos que se incluirán en el archivo:

        wreathed        3
        wreathing       1
        wreaths         1
        wrecked         3
        wrenching       1
        wretched        6
        wriggling       1

##<a id="summary"></a>Resumen

Como puede ver, el comando de Hadoop proporciona una forma sencilla de ejecutar MapReduce trabajos en un clúster de HDInsight y, a continuación, ver los resultados de la tarea.

##<a id="nextsteps"></a>Pasos siguientes

Para obtener información general acerca de los trabajos de MapReduce en HDInsight:

* [Usar MapReduce en HDInsight Hadoop](hdinsight-use-mapreduce.md)

Para obtener información sobre otras formas de puede trabajar con Hadoop en HDInsight:

* [Usar subárbol con Hadoop en HDInsight](hdinsight-use-hive.md)

* [Usar cerdo con Hadoop en HDInsight](hdinsight-use-pig.md)
