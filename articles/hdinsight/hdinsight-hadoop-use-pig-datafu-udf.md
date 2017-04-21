<properties
pageTitle="Usar DataFu con cerdo en HDInsight"
description="DataFu es una colección de bibliotecas para usarlos con Hadoop. Obtenga información sobre cómo usar DataFu con cerdo en el clúster HDInsight."
services="hdinsight"
documentationCenter=""
authors="Blackmist"
manager="jhubbard"
editor="cgronlun"/>

<tags
ms.service="hdinsight"
ms.devlang="na"
ms.topic="article"
ms.tgt_pltfrm="na"
ms.workload="big-data"
ms.date="08/23/2016"
ms.author="larryfr"/>

#<a name="use-datafu-with-pig-on-hdinsight"></a>Usar DataFu con cerdo en HDInsight

DataFu es una colección de bibliotecas de código abierto para su uso con Hadoop. En este documento, aprenderá cómo usar DataFu en el clúster HDInsight y cómo usar las funciones definidas por el usuario (UDF) de DataFu con cerdo.

##<a name="prerequisites"></a>Requisitos previos

* Una suscripción de Azure.

* Un clúster de Azure HDInsight (Linux o basados en Windows)

* Una familiaridad básica con el [uso de cerdo en HDInsight](hdinsight-use-pig.md)

##<a name="install-datafu-on-linux-based-hdinsight"></a>Instalar DataFu en HDInsight de Linux

> [AZURE.NOTE] DataFu se instala en clústeres basados en Linux 3.3 y versiones posteriores y en clústeres basados en Windows. No está instalada en clústeres basados en Linux anteriores a 3.3.
>
> Si está utilizando una versión de clúster basado en Linux 3.3 o posterior, o en un clúster basado en Windows, puede omitir esta sección.

DataFu puede descargar e instalar desde el repositorio de experto. Realice los siguientes pasos para agregar DataFu a su clúster de HDInsight:

1. Conectarse a su clúster de HDInsight basados en Linux mediante SSH. Para obtener más información sobre cómo utilizar SSH con HDInsight, consulte uno de los documentos siguientes:

    * [Utilizar SSH con basados en Linux Hadoop en HDInsight de Linux, OS X y Unix](hdinsight-hadoop-linux-use-ssh-unix.md)
    * [Utilizar SSH con basados en Linux Hadoop en HDInsight de Windows.](hdinsight-hadoop-linux-use-ssh-unix.md)
    
2. Utilice el comando siguiente para descargar el archivo de jar DataFu mediante la utilidad wget, o copie y pegue el vínculo en el explorador para comenzar la descarga.

        wget http://central.maven.org/maven2/com/linkedin/datafu/datafu/1.2.0/datafu-1.2.0.jar

3. A continuación, cargar el archivo en almacenamiento predeterminado para el clúster de HDInsight. Esto hace el archivo esté disponible en todos los nodos en el clúster y el archivo permanecerá en almacenamiento incluso si eliminar y volver a crear el clúster.

        hdfs dfs -put datafu-1.2.0.jar /example/jars
    
    > [AZURE.NOTE] En el ejemplo anterior, se almacena el tarro en `wasbs:///example/jars` desde este directorio ya existe en el almacenamiento de clúster. Puede usar cualquier ubicación que desee en el almacenamiento de clúster HDInsight.

##<a name="use-datafu-with-pig"></a>Usar DataFu con cerdo

Los pasos de esta sección se supone que está familiarizado con cerdo en HDInsight y solo se proporcionan las instrucciones de cerdo latino, no los pasos sobre cómo usarlas con el clúster. Para obtener más información sobre cómo usar cerdo con HDInsight, consulte [Usar cerdo con HDInsight](hdinsight-use-pig.md).

> [AZURE.IMPORTANT] Cuando se usa DataFu de cerdo en un clúster de HDInsight basados en Linux, primero debe registrar el archivo jar mediante la siguiente instrucción cerdo latino:
>
> ```register wasbs:///example/jars/datafu-1.2.0.jar```
>
> DataFu está registrado en clústeres HDInsight basado en Windows de forma predeterminada.

Normalmente se definen un alias para las funciones de DataFu. Por ejemplo:

    DEFINE SHA datafu.pig.hash.SHA();
    
Define un alias denominado `SHA` para la función hash de SHA. A continuación, puede usar esto en una secuencia de comandos de cerdo latino para generar un valor hash para los datos de entrada. Por ejemplo, el siguiente reemplaza los nombres de los datos de entrada con un valor de tipo hash:

    raw = LOAD '/data/raw/' USING PigStorage(',') AS  
        (name:chararray, 
        int1:int, 
        int2:int,
        int3:int); 
    mask = FOREACH raw GENERATE SHA(name), int1, int2, int3; 
    DUMP mask;

Si se utiliza con los siguientes datos de entrada:

    Lana Zemljaric,5,9,1
    Qiong Zhong,9,3,6
    Sandor Harsanyi,0,7,3
    Roko Petkovic,2,6,2
    Tibor Rozsa,8,0,0
    Lea Hrastovsek,6,3,6
    Regina Toth,2,1,2
    Eva Makay,8,9,2
    Shi Liao,4,6,0
    Tjasa Zemljaric,0,2,5
    
Genera el resultado siguiente:

    (c1a743b0f34d349cfc2ce00ef98369bdc3dba1565fec92b4159a9cd5de186347,5,9,1)
    (713d030d621ab69aa3737c8ea37a2c7c724a01cd0657a370e103d8cdecac6f99,9,3,6)
    (7a5f5abdd281f68168199319d98a1a662535f988d1443b3a3c497010937bac89,0,7,3)
    (a94818e93807e12079c4b35f8f3c8c8ef8e8acd1954e7f0476bc1a3a86fc96a9,2,6,2)
    (894ead4f48af91df7e088241218a23157bede7c52115272e417e95c046d48902,8,0,0)
    (6f99f163af3448fda672087db306f363e27a98a9e49c1f274a0860e303f8aec4,6,3,6)
    (a03de92a28be3c6a984c7a153fa9ed81c0413f76a9401955b5f7e04a5dd0ab9f,2,1,2)
    (6ceab977c8fb48d9ad0dc413e6bc646cabd89f22e7ab97a6b0133f3d225c6013,8,9,2)
    (fa9c436469096ff1bd297e182831f460501b826272ae97e921f5f6e3f54747e8,4,6,0)
    (bc22db7c238b86c37af79a62c78f61a304b35143f6087eb99c34040325865654,0,2,5)

##<a name="next-steps"></a>Pasos siguientes

Para obtener más información sobre DataFu o cerdo, consulte los siguientes documentos:

* [Guía de cerdo DataFu Apache](http://datafu.incubator.apache.org/docs/datafu/guide.html).

* [Usar cerdo con HDInsight](hdinsight-use-pig.md)
