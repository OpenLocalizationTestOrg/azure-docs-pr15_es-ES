<properties
   pageTitle="Conexión MapReduce y SSH con Hadoop en HDInsight | Microsoft Azure"
   description="Obtenga información sobre cómo utilizar SSH para ejecutar trabajos MapReduce con Hadoop en HDInsight."
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

# <a name="use-mapreduce-with-hadoop-on-hdinsight-with-ssh"></a>Usar MapReduce con Hadoop en HDInsight con SSH

[AZURE.INCLUDE [mapreduce-selector](../../includes/hdinsight-selector-use-mapreduce.md)]

En este artículo, aprenderá cómo usar Secure Shell (SSH) para conectarse a un Hadoop en clúster HDInsight y, a continuación, enviar trabajos de MapReduce mediante los comandos de Hadoop.

> [AZURE.NOTE] Si ya está familiarizado con el uso de servidores basados en Linux Hadoop, pero está acostumbrado a HDInsight, consulte [sugerencias HDInsight basados en Linux](hdinsight-hadoop-linux-information.md).

##<a id="prereq"></a>Requisitos previos

Para completar los pasos de este artículo, necesita lo siguiente:

* Un clúster de HDInsight basados en Linux (Hadoop en HDInsight)

* Un cliente SSH. Sistemas operativos Linux, Unix y Mac debe proceder con un cliente SSH. Los usuarios de Windows deben descargar a un cliente, como [PuTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).

##<a id="ssh"></a>Conectar con SSH

Conectarse al nombre de dominio completo (FQDN) de su clúster HDInsight mediante el comando SSH. FQDN será el nombre que le asignó el clúster, seguido de **. azurehdinsight.net**. Por ejemplo, la siguiente ¿conectarse a un clúster denominado **myhdinsight**:

    ssh admin@myhdinsight-ssh.azurehdinsight.net

**Si dispone de una clave de certificado para la autenticación de SSH** cuando creó el clúster de HDInsight, debe especificar la ubicación de la clave privada en su sistema de cliente, por ejemplo:

    ssh -i ~/mykey.key admin@myhdinsight-ssh.azurehdinsight.net

**Si dispone de una contraseña para la autenticación de SSH** cuando creó el clúster de HDInsight, debe proporcionar la contraseña cuando se le solicite.

Para obtener más información sobre cómo utilizar SSH con HDInsight, consulte [Usar SSH con basados en Linux Hadoop en HDInsight de Linux, OS X y Unix](hdinsight-hadoop-linux-use-ssh-unix.md).

###<a name="putty-windows-clients"></a>PuTTY (clientes de Windows)

Windows no proporciona a un cliente SSH integrado. Se recomienda usar **PuTTY**, que puede descargarse desde [http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).

Para obtener más información sobre el uso de PuTTY, vea [Usar SSH con basados en Linux Hadoop en HDInsight de Windows ](hdinsight-hadoop-linux-use-ssh-windows.md).

##<a id="hadoop"></a>Use los comandos de Hadoop

1. Cuando se haya conectado al clúster HDInsight, utilice el siguiente comando **Hadoop** para iniciar un trabajo MapReduce:

        yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar wordcount wasb:///example/data/gutenberg/davinci.txt wasb:///example/data/WordCountOutput

    Se inicia la clase **wordcount** , que se encuentra en el archivo de **hadoop-mapreduce-examples.jar** . Como entrada, utiliza el documento de **wasbs://example/data/gutenberg/davinci.txt** y salida se almacena en **wasbs: / / / ejemplo/datos/WordCountOutput**.

    > [AZURE.NOTE] Para obtener más información acerca de este trabajo MapReduce y los datos de ejemplo, vea [Usar MapReduce en Hadoop en HDInsight](hdinsight-use-mapreduce.md).

2. El trabajo emite detalles como procesa y, a continuación, devuelve información similar al siguiente cuando finalice el trabajo:

        File Input Format Counters
        Bytes Read=1395666
        File Output Format Counters
        Bytes Written=337623

3. Cuando finalice el trabajo, use el comando siguiente para mostrar los archivos de salida que se almacenan en **wasbs://example/data/WordCountOutput**:

        hdfs dfs -ls wasbs:///example/data/WordCountOutput

    Debe mostrar de dos archivos, **_SUCCESS** y **elemento-r-00000**. El archivo de **elemento-r-00000** contiene la salida de esta tarea.

    > [AZURE.NOTE] Algunos trabajos MapReduce pueden dividir los resultados en varios archivos de **elemento-r-###** . Si es así, utilice el ### sufijo para indicar el orden de los archivos.

4. Para ver el resultado, use el siguiente comando:

        hdfs dfs -cat wasbs:///example/data/WordCountOutput/part-r-00000

    Esto muestra una lista de las palabras que se encuentran en el archivo **wasbs://example/data/gutenberg/davinci.txt** y el número de veces que se ha producido de cada palabra. A continuación se muestra un ejemplo de los datos que se incluirán en el archivo:

        wreathed        3
        wreathing       1
        wreaths         1
        wrecked         3
        wrenching       1
        wretched        6
        wriggling       1

##<a id="summary"></a>Resumen

Como puede ver, los comandos de Hadoop proporcionan una forma sencilla de ejecutar MapReduce trabajos en un clúster de HDInsight y, a continuación, ver los resultados de la tarea.

##<a id="nextsteps"></a>Pasos siguientes

Para obtener información general acerca de los trabajos de MapReduce en HDInsight:

* [Usar MapReduce en HDInsight Hadoop](hdinsight-use-mapreduce.md)

Para obtener información sobre otras formas de puede trabajar con Hadoop en HDInsight:

* [Usar subárbol con Hadoop en HDInsight](hdinsight-use-hive.md)

* [Usar cerdo con Hadoop en HDInsight](hdinsight-use-pig.md)
