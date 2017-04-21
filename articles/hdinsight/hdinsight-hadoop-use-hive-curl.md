<properties
   pageTitle="Usar Hadoop subárbol con rizo en HDInsight | Microsoft Azure"
   description="Obtenga información sobre cómo enviar remotamente trabajos de cerdo a HDInsight con doblez."
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
   ms.date="09/07/2016"
   ms.author="larryfr"/>

#<a name="run-hive-queries-with-hadoop-in-hdinsight-with-curl"></a>Ejecutar consultas de la sección con Hadoop en HDInsight con rizo

[AZURE.INCLUDE [hive-selector](../../includes/hdinsight-selector-use-hive.md)]

En este documento, aprenderá cómo usar doblez para ejecutar consultas de sección en una Hadoop en clúster HDInsight de Azure.

Doblez se usa para demostrar cómo puede interactuar con HDInsight utilizando solicitudes HTTP sin formato para ejecutar, supervisar y recuperar los resultados de las consultas de la sección. Esto funciona con la API de REST de WebHCat (anteriormente conocida como Templeton) proporcionadas por el clúster HDInsight.

> [AZURE.NOTE] Si ya está familiarizado con servidores basados en Linux Hadoop, pero está familiarizado con HDInsight, consulte [lo que necesita saber sobre Hadoop en HDInsight de Linux](hdinsight-hadoop-linux-information.md).

##<a id="prereq"></a>Requisitos previos

Para completar los pasos de este artículo, necesita lo siguiente:

* Un Hadoop en clúster HDInsight (Linux o basado en Windows)

* [Doblez](http://curl.haxx.se/)

* [jq](http://stedolan.github.io/jq/)

##<a id="curl"></a>Ejecutar consultas subárbol mediante doblez

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

    Principio de la dirección URL, **https://CLUSTERNAME.azurehdinsight.net/templeton/v1**, será el mismo para todas las solicitudes. La ruta de acceso **/status/Status**, indica que la solicitud es para devolver un estado de WebHCat (también conocido como Templeton) para el servidor. También puede solicitar la versión de la sección mediante el comando siguiente:

        curl -u USERNAME:PASSWORD -G https://CLUSTERNAME.azurehdinsight.net/templeton/v1/version/hive

    Debe devolver una respuesta similar a la siguiente:

        {"module":"hive","version":"0.13.0.2.1.6.0-2103"}

2. Para crear una nueva tabla denominada **log4jLogs**, use las siguientes acciones:

        curl -u USERNAME:PASSWORD -d user.name=USERNAME -d execute="set+hive.execution.engine=tez;DROP+TABLE+log4jLogs;CREATE+EXTERNAL+TABLE+log4jLogs(t1+string,t2+string,t3+string,t4+string,t5+string,t6+string,t7+string)+ROW+FORMAT+DELIMITED+FIELDS+TERMINATED+BY+' '+STORED+AS+TEXTFILE+LOCATION+'wasbs:///example/data/';SELECT+t4+AS+sev,COUNT(*)+AS+count+FROM+log4jLogs+WHERE+t4+=+'[ERROR]'+AND+INPUT__FILE__NAME+LIKE+'%25.log'+GROUP+BY+t4;" -d statusdir="wasbs:///example/curl" https://CLUSTERNAME.azurehdinsight.net/templeton/v1/hive

    Los parámetros que se utilizan en este comando son los siguientes:

    * **-d** - desde `-G` no se utiliza la solicitud de valores predeterminados para el método de entrada. `-d`Especifica los valores de datos que se envían a la solicitud.

        * **User.nombre** - el usuario que se está ejecutando el comando.

        * **Ejecutar** - HiveQL las instrucciones que se ejecutan.

        * **statusdir** - directorio que se escriben en el estado de esta tarea.

    Estas instrucciones realizan las siguientes acciones:

    * **Quitar tabla** - elimina la tabla y el archivo de datos, si la tabla ya existe.

    * **Crear tabla externa** - crea una nueva tabla 'externa' en la sección. Tablas externas almacenan la definición de la tabla en la sección. Los datos se quedan en la ubicación original.

        > [AZURE.NOTE] Tablas externas se deben utilizar cuando se esperan los datos subyacentes pueden actualizar mediante un origen externo, como un proceso de carga automática de datos, o por otra operación MapReduce, pero siempre desea subárbol consultas para usar los datos más recientes.
        >
        > Anulación de una tabla externa hace **no** eliminar los datos, la definición de la tabla.

    * **Formato de fila** : la indica subárbol cómo se aplica el formato de los datos. En este caso, los campos de cada registro separados por un espacio.

    * **Almacenados como archivo de texto ubicación** - indica subárbol dónde están los datos almacenados (el directorio de ejemplo de datos) y que TI se almacena como texto.

    * **Seleccione** - selecciona un recuento de todas las filas donde la columna **t4** contiene el valor **[ERROR]**. Como han tres filas que contienen este valor se debe devolver un valor de **3** .

    > [AZURE.NOTE] Observe que los espacios entre HiveQL resúmenes se reemplazan por los `+` carácter cuando se utiliza con rizo. Los valores entre comillas que contienen un espacio, como el delimitador no deben reemplazarse con `+`.

    * **INPUT__FILE__NAME como '% 25.log'** - este limita la búsqueda a solo usar archivos que terminen en. log. Si esto no está presente, subárbol intentará buscar todos los archivos en este directorio y sus subdirectorios, incluidos los archivos que no coincidan con el esquema de columna definido para esta tabla.

    > [AZURE.NOTE] Observe que 25% es la dirección URL codificada formulario %, por lo que es la condición real `like '%.log'`. El % tiene que ser dirección URL codificada, tal como se trata como un carácter especial en las direcciones URL.

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

6. Use las siguientes instrucciones para crear una nueva tabla 'interna' denominada **errorLogs**:

        curl -u USERNAME:PASSWORD -d user.name=USERNAME -d execute="set+hive.execution.engine=tez;CREATE+TABLE+IF+NOT+EXISTS+errorLogs(t1+string,t2+string,t3+string,t4+string,t5+string,t6+string,t7+string)+STORED+AS+ORC;INSERT+OVERWRITE+TABLE+errorLogs+SELECT+t1,t2,t3,t4,t5,t6,t7+FROM+log4jLogs+WHERE+t4+=+'[ERROR]'+AND+INPUT__FILE__NAME+LIKE+'%25.log';SELECT+*+from+errorLogs;" -d statusdir="wasbs:///example/curl" https://CLUSTERNAME.azurehdinsight.net/templeton/v1/hive

    Estas instrucciones realizan las siguientes acciones:

    * **Crear tabla si no existe** - crea una tabla, si aún no existe. Puesto que no se usa la palabra clave **externa** , esta es una tabla interna, que se almacena en el almacén de datos de la sección y se administra completamente por sección.

        > [AZURE.NOTE] A diferencia de tablas externas, la colocación de una tabla interna eliminará también los datos subyacentes.

    * **Almacenados como ORC** - almacena los datos en formato de fila de optimizado en columnas (ORC). Se trata de un formato altamente optimizado y eficaz para almacenar datos de la sección.
    * SOBRESCRIBIR **Insertar... Seleccione** - selecciona las filas de la tabla de **log4jLogs** que contienen **[ERROR]**, a continuación, inserta los datos en la tabla **errorLogs** .
    * **Seleccione** - selecciona todas las filas de la nueva tabla de **errorLogs** .

7. Use el identificador de trabajo devuelve para comprobar el estado del trabajo. Una vez que se realizó correctamente, use CLI de Azure para Mac, Linux y Windows como se describe anteriormente para descargar y ver los resultados. El resultado debe contener tres líneas, todas las cuales contienen **[ERROR]**.


##<a id="summary"></a>Resumen

Como se muestra en este documento, puede usar una solicitud HTTP sin formato para ejecutar, supervisar y ver los resultados de los trabajos de sección en el clúster HDInsight.

Para obtener más información sobre la interfaz REST usada en este artículo, vea la <a href="https://cwiki.apache.org/confluence/display/Hive/WebHCat+Reference" target="_blank">Referencia de WebHCat</a>.

##<a id="nextsteps"></a>Pasos siguientes

Para obtener información general sobre subárbol con HDInsight:

* [Usar subárbol con Hadoop en HDInsight](hdinsight-use-hive.md)

Para obtener información sobre otras maneras de poder trabajar con Hadoop en HDInsight:

* [Usar cerdo con Hadoop en HDInsight](hdinsight-use-pig.md)

* [Usar MapReduce con Hadoop en HDInsight](hdinsight-use-mapreduce.md)

Si está utilizando Tez con sección, consulte la información de depuración de documentos siguientes:

* [Usar la interfaz de usuario de Tez en HDInsight de Windows](hdinsight-debug-tez-ui.md)

* [Usar la vista de Ambari Tez en HDInsight de Linux](hdinsight-debug-ambari-tez-view.md)

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


