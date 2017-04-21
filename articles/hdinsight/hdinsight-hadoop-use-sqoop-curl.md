<properties
   pageTitle="Usar Hadoop Sqoop con rizo en HDInsight | Microsoft Azure"
   description="Obtenga información sobre cómo enviar remotamente trabajos de Sqoop a HDInsight con doblez."
   services="hdinsight"
   documentationCenter=""
   authors="mumian"
   manager="jhubbard"
   editor="cgronlun"
    tags="azure-portal"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="10/21/2016"
   ms.author="jgao"/>

#<a name="run-sqoop-jobs-with-hadoop-in-hdinsight-with-curl"></a>Ejecutar trabajos Sqoop con Hadoop en HDInsight con rizo

[AZURE.INCLUDE [sqoop-selector](../../includes/hdinsight-selector-use-sqoop.md)]

Aprenda a usar doblez para ejecutar Sqoop trabajos en un clúster de Hadoop en HDInsight.

Doblez se usa para demostrar cómo puede interactuar con HDInsight utilizando solicitudes HTTP sin formato para ejecutar, supervisar y recuperar los resultados de los trabajos de Sqoop. Esto funciona con la API de REST de WebHCat (anteriormente conocida como Templeton) proporcionadas por el clúster HDInsight.

> [AZURE.NOTE] Si ya está familiarizado con servidores basados en Linux Hadoop, pero está familiarizado con HDInsight, consulte [información sobre el uso de HDInsight en Linux](hdinsight-hadoop-linux-information.md).

##<a name="prerequisites"></a>Requisitos previos

Para completar los pasos de este artículo, necesita lo siguiente:

* Un Hadoop en clúster HDInsight (Linux o basado en Windows)

* [Doblez](http://curl.haxx.se/)

* [jq](http://stedolan.github.io/jq/)

##<a name="submit-sqoop-jobs-by-using-curl"></a>Enviar trabajos de Sqoop mediante doblez

> [AZURE.NOTE] Con doblez o cualquier otro tipo de comunicación resto WebHCat, debe autenticar las solicitudes al proporcionar el nombre de usuario y contraseña para el Administrador de clústeres HDInsight. También debe usar el nombre de clúster como parte del identificador de recursos uniforme (URI) utilizado para enviar las solicitudes en el servidor.
>
> Para los comandos de esta sección, reemplace el **nombre de usuario** con el usuario para autenticar al clúster y reemplace la **contraseña** con la contraseña de la cuenta de usuario. Reemplazar **NOMBREDECLÚSTER** con el nombre del clúster.
>
> La API de REST está protegida mediante [la autenticación básica](http://en.wikipedia.org/wiki/Basic_access_authentication). Siempre deben realizar solicitudes utilizando HTTP seguro (HTTPS) para ayudar a garantizar que sus credenciales se envían de forma segura en el servidor.

1. Desde una línea de comandos, use el comando siguiente para comprobar que puede conectarse a su clúster HDInsight:

        curl -u USERNAME:PASSWORD -G https://CLUSTERNAME.azurehdinsight.net/templeton/v1/status

    Recibirá una respuesta similar a la siguiente:

        {"status":"ok","version":"v1"}

    Los parámetros que se utilizan en este comando son los siguientes:

    * **-u** - el nombre de usuario y contraseña que utiliza para autenticar la solicitud.
    * **-G** - indica que se trata de una solicitud GET.

    Principio de la dirección URL, **https://CLUSTERNAME.azurehdinsight.net/templeton/v1**, será el mismo para todas las solicitudes. La ruta de acceso **/status/Status**, indica que la solicitud es para devolver un estado de WebHCat (también conocido como Templeton) para el servidor. 

2. Utilice las siguientes acciones para enviar un trabajo sqoop:


        curl -u USERNAME:PASSWORD -d user.name=USERNAME -d command="export --connect jdbc:sqlserver://SQLDATABASESERVERNAME.database.windows.net;user=USERNAME@SQLDATABASESERVERNAME;password=PASSWORD;database=SQLDATABASENAME --table log4jlogs --export-dir /tutorials/usesqoop/data --input-fields-terminated-by \0x20 -m 1" -d statusdir="wasbs:///example/curl" https://CLUSTERNAME.azurehdinsight.net/templeton/v1/sqoop

    Los parámetros que se utilizan en este comando son los siguientes:

    * **-d** - desde `-G` no se utiliza la solicitud de valores predeterminados para el método de entrada. `-d`Especifica los valores de datos que se envían a la solicitud.

        * **User.nombre** - el usuario que se está ejecutando el comando.

        * **comando** - Sqoop el comando ejecutar.

        * **statusdir** - directorio que se escriben en el estado de esta tarea.

    Este comando debe devolver un identificador de trabajo que se pueden usar para comprobar el estado del trabajo.

        {"id":"job_1415651640909_0026"}

3. Para comprobar el estado de la tarea, use el siguiente comando. Reemplazar **ID** con el valor devuelto en el paso anterior. Por ejemplo, si el valor devuelto es `{"id":"job_1415651640909_0026"}`, sería **ID** `job_1415651640909_0026`.

        curl -G -u USERNAME:PASSWORD -d user.name=USERNAME https://CLUSTERNAME.azurehdinsight.net/templeton/v1/jobs/JOBID | jq .status.state

    Si ha finalizado el trabajo, el estado será **correcta**.

    > [AZURE.NOTE] Esta solicitud doblez devuelve un documento de notación de objetos JavaScript (JSON) con información sobre el trabajo; jq se usa para recuperar el valor de estado.

4. Una vez que el estado de la tarea ha cambiado a **correcta**, puede recuperar los resultados de la tarea de almacenamiento de blobs de Windows Azure. La `statusdir` parámetro pasado con la consulta contiene la ubicación del archivo de salida. en este caso, **wasbs: / / / ejemplo/doblez**. Esta dirección almacena el resultado de la tarea en el **directorio/doblez de ejemplo** en el contenedor de almacenamiento predeterminado utilizado por el clúster HDInsight.

    Puede mostrar y descargar estos archivos mediante la [CLI de Azure](../xplat-cli-install.md). Por ejemplo, en archivos de la lista de **ejemplo/enrollar**, utilice el siguiente comando:

        azure storage blob list <container-name> example/curl

    Para descargar un archivo, use la siguiente:

        azure storage blob download <container-name> <blob-name> <destination-file>

    > [AZURE.NOTE] Debe especificar el nombre de la cuenta de almacenamiento que contenga el blob mediante la `-a` y `-k` parámetros o configurar la **AZURE\_almacenamiento\_cuenta** y **AZURE\_almacenamiento\_ACCESS\_clave** variables de entorno. Vea < una referencia = "data.md de carga de hdinsight" target = "_blank" para obtener más información.

##<a name="limitations"></a>Limitaciones

* Exportación de masa - HDInsight de con Linux, el conector de Sqoop utilizado para exportar datos a Microsoft SQL Server o base de datos de SQL Azure no es compatible con las inserciones masivas.

* Procesamiento por lotes - con HDInsight basados en Linux, cuando se usa el `-batch` cambia cuando se realizan inserciones, Sqoop realizará varias inserciones en lugar de por lotes las operaciones de inserción.

##<a name="summary"></a>Resumen

Como se muestra en este documento, puede usar una solicitud HTTP sin formato para ejecutar, supervisar y ver los resultados de los trabajos de Sqoop en el clúster HDInsight.

Para obtener más información sobre la interfaz REST usada en este artículo, consulte la <a href="https://sqoop.apache.org/docs/1.99.3/RESTAPI.html" target="_blank">Guía de la API de REST de Sqoop</a>.

##<a name="next-steps"></a>Pasos siguientes

Para obtener información general sobre subárbol con HDInsight:

* [Usar Sqoop con Hadoop en HDInsight](hdinsight-use-sqoop.md)

Para obtener información sobre otras maneras de poder trabajar con Hadoop en HDInsight:

* [Usar subárbol con Hadoop en HDInsight](hdinsight-use-hive.md)

* [Usar cerdo con Hadoop en HDInsight](hdinsight-use-pig.md)

* [Usar MapReduce con Hadoop en HDInsight](hdinsight-use-mapreduce.md)

[hdinsight-sdk-documentation]: http://msdnstage.redmond.corp.microsoft.com/library/dn479185.aspx

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[apache-tez]: http://tez.apache.org
[apache-hive]: http://hive.apache.org/
[apache-log4j]: http://en.wikipedia.org/wiki/Log4j
[hive-on-tez-wiki]: https://cwiki.apache.org/confluence/display/Hive/Hive+on+Tez
[import-to-excel]: http://azure.microsoft.com/documentation/articles/hdinsight-connect-excel-power-query/


[hdinsight-use-oozie]: hdinsight-use-oozie.md
[hdinsight-analyze-flight-data]: hdinsight-analyze-flight-delay-data.md




[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-submit-jobs]: hdinsight-submit-hadoop-jobs-programmatically.md
[hdinsight-upload-data]: hdinsight-upload-data.md

[powershell-here-strings]: http://technet.microsoft.com/library/ee692792.aspx


