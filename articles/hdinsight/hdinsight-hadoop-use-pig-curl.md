<properties
   pageTitle="Usar Hadoop cerdo con rizo en HDInsight | Microsoft Azure"
   description="Aprenda a usar doblez para ejecutar cerdo latino trabajos en un clúster de Hadoop en HDInsight de Azure."
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

#<a name="run-pig-jobs-with-hadoop-on-hdinsight-by-using-curl"></a>Ejecutar trabajos de cerdo con Hadoop en HDInsight mediante doblez

[AZURE.INCLUDE [pig-selector](../../includes/hdinsight-selector-use-pig.md)]

En este documento, aprenderá cómo usar doblez para ejecutar trabajos de cerdo latino en un clúster de HDInsight de Azure. El lenguaje de programación de cerdo latino le permite describir las transformaciones que se aplican a los datos de entrada para generar el resultado deseado.

Doblez se usa para demostrar cómo puede interactuar con HDInsight utilizando solicitudes HTTP sin formato para ejecutar, supervisar y recuperar los resultados de los trabajos de cerdo. Esto funciona con la API de REST de WebHCat (anteriormente conocida como Templeton) proporcionada por el clúster HDInsight.

> [AZURE.NOTE] Si ya está familiarizado con servidores basados en Linux Hadoop, pero está familiarizado con HDInsight, consulte [Sugerencias de HDInsight basados en Linux](hdinsight-hadoop-linux-information.md).

##<a id="prereq"></a>Requisitos previos

Para completar los pasos de este artículo, necesita lo siguiente:

* Un clúster de Azure HDInsight (Hadoop en HDInsight) (basado en Linux o en Windows)

* [Doblez](http://curl.haxx.se/)

* [jq](http://stedolan.github.io/jq/)

##<a id="curl"></a>Ejecute trabajos cerdo con rizo

> [AZURE.NOTE] Con doblez o cualquier otro tipo de comunicación resto WebHCat, debe autenticar las solicitudes al proporcionar el nombre de usuario administrador y la contraseña para el clúster de HDInsight. También debe usar el nombre de clúster como parte de la identificador uniforme de recursos (URI) que se utiliza para enviar las solicitudes en el servidor.
>
> Para los comandos de esta sección, reemplace el **nombre de usuario** con el usuario para autenticar al clúster y reemplace la **contraseña** con la contraseña de la cuenta de usuario. Reemplazar **NOMBREDECLÚSTER** con el nombre del clúster.
>
> La API de REST está protegida mediante la [autenticación de acceso básica](http://en.wikipedia.org/wiki/Basic_access_authentication). Siempre deben realizar solicitudes utilizando HTTP seguro (HTTPS) para ayudar a garantizar que sus credenciales se envían de forma segura en el servidor.

1. Desde una línea de comandos, use el comando siguiente para comprobar que puede conectarse a su clúster HDInsight:

        curl -u USERNAME:PASSWORD -G https://CLUSTERNAME.azurehdinsight.net/templeton/v1/status

    Recibirá una respuesta similar a la siguiente:

        {"status":"ok","version":"v1"}

    Los parámetros que se utilizan en este comando son los siguientes:

    * **-u**: el nombre de usuario y contraseña que utiliza para autenticar la solicitud
    * **-G**: indica que se trata de una solicitud GET

    Principio de la dirección URL, **https://CLUSTERNAME.azurehdinsight.net/templeton/v1**, será el mismo para todas las solicitudes. La ruta de acceso **/status/Status**, indica que la solicitud es para devolver el estado de WebHCat (también conocido como Templeton) para el servidor.

2. Use el siguiente código para enviar un trabajo cerdo latino el clúster:

        curl -u USERNAME:PASSWORD -d user.name=USERNAME -d execute="LOGS=LOAD+'wasbs:///example/data/sample.log';LEVELS=foreach+LOGS+generate+REGEX_EXTRACT($0,'(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)',1)+as+LOGLEVEL;FILTEREDLEVELS=FILTER+LEVELS+by+LOGLEVEL+is+not+null;GROUPEDLEVELS=GROUP+FILTEREDLEVELS+by+LOGLEVEL;FREQUENCIES=foreach+GROUPEDLEVELS+generate+group+as+LOGLEVEL,COUNT(FILTEREDLEVELS.LOGLEVEL)+as+count;RESULT=order+FREQUENCIES+by+COUNT+desc;DUMP+RESULT;" -d statusdir="wasbs:///example/pigcurl" https://CLUSTERNAME.azurehdinsight.net/templeton/v1/pig

    Los parámetros que se utilizan en este comando son los siguientes:

    * **-d**: porque `-G` no se utiliza la solicitud de valores predeterminados para el método de entrada. `-d`Especifica los valores de datos que se envían a la solicitud.

        * **User.nombre**: el usuario que se está ejecutando el comando
        * **Ejecutar**: el latino cerdo instrucciones para ejecutar
        * **statusdir**: el directorio que se escriben en el estado de esta tarea

    > [AZURE.NOTE] Observe que los espacios de cerdo latino instrucciones son reemplazados por la `+` cuando se utiliza con rizo de caracteres.

    Este comando debe devolver un identificador de trabajo que se pueden usar para comprobar el estado de la tarea, por ejemplo:

        {"id":"job_1415651640909_0026"}

3. Para comprobar el estado de la tarea, use el siguiente comando. Reemplazar **ID** con el valor devuelto en el paso anterior. Por ejemplo, si el valor devuelto es `{"id":"job_1415651640909_0026"}`, sería **ID** `job_1415651640909_0026`.

        curl -G -u USERNAME:PASSWORD -d user.name=USERNAME https://CLUSTERNAME.azurehdinsight.net/templeton/v1/jobs/JOBID | jq .status.state

    Si ha finalizado el trabajo, el estado será **correcta**.

    > [AZURE.NOTE] Esta solicitud doblez devuelve un documento de notación de objetos JavaScript (JSON) con información sobre el trabajo y jq se utiliza para recuperar el valor de estado.

##<a id="results"></a>Ver los resultados

Cuando el estado de la tarea ha cambiado a **correcta**, puede recuperar los resultados de la tarea de almacenamiento de blobs de Windows Azure. La `statusdir` parámetro pasado con la consulta contiene la ubicación del archivo de salida. en este caso, **wasbs: / / / ejemplo/pigcurl**. Esta dirección almacena el resultado de la tarea en el directorio de **ejemplo/pigcurl** en el contenedor de almacenamiento predeterminado utilizado por el clúster HDInsight.

Puede mostrar y descargar estos archivos mediante la [CLI de Azure](../xplat-cli-install.md). Por ejemplo, para mostrar los archivos de **ejemplo/pigcurl**, utilice el siguiente comando:

    azure storage blob list <container-name> example/pigcurl

Para descargar un archivo, use la siguiente:

    azure storage blob download <container-name> <blob-name> <destination-file>

> [AZURE.NOTE] Debe especificar el nombre de la cuenta de almacenamiento que contenga el blob mediante la `-a` y `-k` parámetros o configurar la **AZURE\_almacenamiento\_cuenta** y **AZURE\_almacenamiento\_ACCESS\_clave** variables de entorno.

##<a id="summary"></a>Resumen

Como se muestra en este documento, puede usar una solicitud HTTP sin formato para ejecutar, supervisar y ver los resultados de los trabajos de cerdo en el clúster HDInsight.

Para obtener más información acerca de la interfaz REST usada en este artículo, vea la [Referencia de WebHCat](https://cwiki.apache.org/confluence/display/Hive/WebHCat+Reference).

##<a id="nextsteps"></a>Pasos siguientes

Para obtener información general sobre cerdo en HDInsight:

* [Usar cerdo con Hadoop en HDInsight](hdinsight-use-pig.md)

Para obtener información sobre otras formas de puede trabajar con Hadoop en HDInsight:

* [Usar subárbol con Hadoop en HDInsight](hdinsight-use-hive.md)

* [Usar MapReduce con Hadoop en HDInsight](hdinsight-use-mapreduce.md)
