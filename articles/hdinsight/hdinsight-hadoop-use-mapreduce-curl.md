<properties
   pageTitle="Usar MapReduce y doblez con Hadoop en HDInsight | Microsoft Azure"
   description="Obtenga información sobre cómo ejecutar remotamente MapReduce trabajos con Hadoop en HDInsight con doblez."
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

#<a name="run-mapreduce-jobs-with-hadoop-on-hdinsight-using-curl"></a>Ejecutar trabajos MapReduce con Hadoop en HDInsight con rizo

[AZURE.INCLUDE [mapreduce-selector](../../includes/hdinsight-selector-use-mapreduce.md)]

En este documento, aprenderá cómo usar doblez ejecuten trabajos MapReduce en un Hadoop en clúster HDInsight.

Doblez se usa para demostrar cómo puede interactuar con HDInsight con solicitudes HTTP sin formato para ejecutar MapReduce trabajos. Esto funciona con la API de REST de WebHCat (anteriormente conocida como Templeton) proporcionadas por el clúster HDInsight.

> [AZURE.NOTE] Si ya está familiarizado con el uso de servidores basados en Linux Hadoop, pero está acostumbrado a HDInsight, consulte [lo que necesita saber sobre basados en Linux Hadoop en HDInsight](hdinsight-hadoop-linux-information.md).

##<a id="prereq"></a>Requisitos previos

Para completar los pasos de este artículo, necesita lo siguiente:

* Un Hadoop en clúster HDInsight (Linux o basado en Windows)

* [Doblez](http://curl.haxx.se/)

* [jq](http://stedolan.github.io/jq/)

##<a id="curl"></a>Ejecutar trabajos MapReduce con rizo

> [AZURE.NOTE] Cuando utiliza doblez o cualquier otro tipo de comunicación resto con WebHCat, debe autenticar las solicitudes al proporcionar el nombre de usuario de administrador de clúster de HDInsight y la contraseña. También debe usar el nombre de clúster como parte de la URI que se usa para enviar las solicitudes en el servidor.
>
> Para los comandos de esta sección, reemplace el **nombre de usuario** por el usuario para autenticar al clúster y la **contraseña** con la contraseña de la cuenta de usuario. Reemplazar **NOMBREDECLÚSTER** con el nombre del clúster.
>
> La API de REST está protegida mediante la [autenticación de acceso básica](http://en.wikipedia.org/wiki/Basic_access_authentication). Siempre deben realizar solicitudes usando HTTPS para garantizar que sus credenciales se envían de forma segura en el servidor.

1. Desde una línea de comandos, utilice el comando siguiente para comprobar que puede conectarse a su clúster HDInsight:

        curl -u USERNAME:PASSWORD -G https://CLUSTERNAME.azurehdinsight.net/templeton/v1/status

    Recibirá una respuesta similar a la siguiente:

        {"status":"ok","version":"v1"}

    Los parámetros que se utilizan en este comando son los siguientes:

    * **-u**: indica el nombre de usuario y contraseña que utiliza para autenticar la solicitud
    * **-G**: indica que se trata de una solicitud GET

    Principio de la dirección URI, **https://CLUSTERNAME.azurehdinsight.net/templeton/v1**, es el mismo para todas las solicitudes.

2. Para enviar un trabajo MapReduce, utilice el siguiente comando:

        curl -u USERNAME:PASSWORD -d user.name=USERNAME -d jar=wasbs:///example/jars/hadoop-mapreduce-examples.jar -d class=wordcount -d arg=wasbs:///example/data/gutenberg/davinci.txt -d arg=wasbs:///example/data/CurlOut https://CLUSTERNAME.azurehdinsight.net/templeton/v1/mapreduce/jar

    Al final de la URI (/ mapreduce/jar) indica WebHCat que esta solicitud iniciará un trabajo MapReduce de una clase en un archivo jar. Los parámetros que se utilizan en este comando son los siguientes:

    * **-d**: `-G` no se utiliza para la solicitud de valores predeterminados para el método de entrada. `-d`Especifica los valores de datos que se envían a la solicitud.

        * **User.nombre**: el usuario que se está ejecutando el comando
        * **JAR**: ejecutó la ubicación del archivo jar que contiene la clase para que sea
        * **clase**: la clase que contiene la lógica MapReduce
        * **argumento**: los argumentos que se pasan a la tarea MapReduce; en este caso, el archivo de texto de entrada y el directorio que se usan para la salida

    Este comando debe devolver un identificador de trabajo que se pueden usar para comprobar el estado del trabajo:

        {"id":"job_1415651640909_0026"}

3. Para comprobar el estado de la tarea, use el siguiente comando. Reemplazar **ID** con el valor devuelto en el paso anterior. Por ejemplo, si el valor devuelto es `{"id":"job_1415651640909_0026"}`, sería el ID `job_1415651640909_0026`.

        curl -G -u USERNAME:PASSWORD -d user.name=USERNAME https://CLUSTERNAME.azurehdinsight.net/templeton/v1/jobs/JOBID | jq .status.state

    Si la tarea está completada, el estado se pueden "correcta".

    > [AZURE.NOTE] Esta solicitud doblez devuelve un documento JSON con información sobre el trabajo; jq se usa para recuperar el valor de estado.

4. Cuando el estado de la tarea ha cambiado a **correcta**, puede recuperar los resultados de la tarea de almacenamiento de blobs de Windows Azure. La `statusdir` parámetro que se pasa con la consulta contiene la ubicación del archivo de salida. en este caso, **wasbs: / / / ejemplo/doblez**. Esta dirección almacena el resultado de la tarea en el **directorio/doblez de ejemplo** en el contenedor de almacenamiento predeterminado utilizado por el clúster HDInsight.

Puede mostrar y descargar estos archivos mediante la [CLI de Azure](../xplat-cli-install.md). Por ejemplo, para los archivos de la lista en el **ejemplo/enrollar**, utilice el siguiente comando:

    azure storage blob list <container-name> example/curl

Para descargar un archivo, use la siguiente:

    azure storage blob download <container-name> <blob-name> <destination-file>

> [AZURE.NOTE] Debe especificar el nombre de la cuenta de almacenamiento que contenga el blob mediante la `-a` y `-k` parámetros o configurar la **AZURE\_almacenamiento\_cuenta** y **AZURE\_almacenamiento\_ACCESS\_clave** variables de entorno. Vea [cómo cargar datos a HDInsight](hdinsight-upload-data.md) para obtener más información.

##<a id="summary"></a>Resumen

Como se muestra en este documento, puede usar para ejecutar, supervisar y ver los resultados de los trabajos de sección en el clúster HDInsight solicitud HTTP sin formato.

Para obtener más información acerca de la interfaz REST usada en este artículo, vea la [Referencia de WebHCat](https://cwiki.apache.org/confluence/display/Hive/WebHCat+Reference).

##<a id="nextsteps"></a>Pasos siguientes

Para obtener información general acerca de los trabajos de MapReduce en HDInsight:

* [Usar MapReduce con Hadoop en HDInsight](hdinsight-use-mapreduce.md)

Para obtener información sobre otras formas de puede trabajar con Hadoop en HDInsight:

* [Usar subárbol con Hadoop en HDInsight](hdinsight-use-hive.md)

* [Usar cerdo con Hadoop en HDInsight](hdinsight-use-pig.md)
