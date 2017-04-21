<properties
   pageTitle="Usar Hadoop subárbol en la consola de consulta en HDInsight | Microsoft Azure"
   description="Obtenga información sobre cómo usar la consola de consulta basada en web para ejecutar consultas de la sección en un clúster de HDInsight Hadoop desde el explorador."
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
   ms.date="09/20/2016"
   ms.author="larryfr"/>

# <a name="run-hive-queries-using-the-query-console"></a>Ejecutar consultas de subárbol con la consola de consulta

[AZURE.INCLUDE [hive-selector](../../includes/hdinsight-selector-use-hive.md)]

En este artículo, aprenderá cómo usar la consola de consulta HDInsight para ejecutar consultas de la sección en un clúster de HDInsight Hadoop desde el explorador.

> [AZURE.IMPORTANT] La consola de consulta HDInsight solo está disponible en clústeres HDInsight basado en Windows. Si está utilizando un clúster basados en Linux HDInsight, consulte [consultas ejecutar subárbol usando la vista de sección](hdinsight-hadoop-use-hive-ambari-view.md).


##<a id="prereq"></a>Requisitos previos

Para completar los pasos de este artículo, necesita lo siguiente.

* Un clúster basado en Windows HDInsight Hadoop

* Un explorador web moderna

##<a id="run"></a>Ejecutar consultas de subárbol con la consola de consulta

1. Abra un navegador web y vaya a __https://CLUSTERNAME.azurehdinsight.net__, donde __NOMBREDECLÚSTER__ es el nombre de su clúster HDInsight. Si se le solicita, escriba el nombre de usuario y contraseña que utilizó cuando creó el clúster.


2. En los vínculos en la parte superior de la página, seleccione **Editor de sección**. Muestra un formulario que se pueden usar para especificar las instrucciones de HiveQL que desea ejecutar en el clúster HDInsight.

    ![el editor de sección](./media/hdinsight-hadoop-use-hive-query-console/queryconsole.png)

    Reemplazar el texto `Select * from hivesampletable` con las siguientes afirmaciones HiveQL:

        set hive.execution.engine=tez;
        DROP TABLE log4jLogs;
        CREATE EXTERNAL TABLE log4jLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string)
        ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
        STORED AS TEXTFILE LOCATION 'wasbs:///example/data/';
        SELECT t4 AS sev, COUNT(*) AS count FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log' GROUP BY t4;

    Estas instrucciones realizan las siguientes acciones:

    * **Quitar tabla**: elimina la tabla y el archivo de datos si la tabla ya existe.
    * **Crear una tabla externa**: crea una nueva tabla 'externa' en la sección. Tablas externas almacenan la definición de la tabla en la sección; los datos se quedan en la ubicación original.

    > [AZURE.NOTE] Tablas externas se deben usar cuando se esperan los datos subyacentes que se actualicen por un origen externo (por ejemplo, un proceso de carga automática de datos) o por otra operación MapReduce, pero desea siempre las consultas de la sección para usar los datos más recientes.
    >
    > Anulación de una tabla externa hace **no** eliminar los datos, la definición de la tabla.

    * **Formato de fila**: indica subárbol cómo se aplica el formato de los datos. En este caso, los campos de cada registro separados por un espacio.
    * **Ubicación de archivo de texto como almacenados**: indica subárbol dónde están los datos almacenados (el directorio de ejemplo de datos) y que se almacena como texto
    * **Seleccionar**: seleccione un recuento de todas las filas donde la columna **t4** contienen el valor **[ERROR]**. Esto debe devolver un valor de **3** porque hay tres filas que contienen este valor.
    * **INPUT__FILE__NAME como '%.log'** - indica subárbol que solo se deben devolver datos de archivos que terminen en. log. Esto restringe la búsqueda al archivo sample.log que contiene los datos y evita que devolver datos de ejemplo de otro archivos de datos que no coincidan con el esquema definido.

2. Haga clic en **Enviar**. La **Sesión de trabajo** en la parte inferior de la página debe mostrar detalles del trabajo.

3. Cuando se cambia el campo **estado** en **completado**, seleccione **Ver detalles** de la tarea. En la página Detalles, la **Salida de trabajo** contiene `[ERROR]   3`. Puede usar el botón **Descargar** en este campo para descargar un archivo que contiene el resultado de la tarea.


##<a id="summary"></a>Resumen

Como puede ver, la consola de consulta proporciona una forma sencilla de ejecutar consultas de la sección en un clúster de HDInsight, supervisar el estado del trabajo y recuperar el resultado.

Para más información sobre cómo usar subárbol consola de consulta para ejecutar trabajos de sección, seleccione **Introducción** en la parte superior de la consola de consulta y luego use los ejemplos que se proporcionan. Cada muestra guiará durante el proceso de utilizar subárbol para analizar datos, incluidos explicaciones acerca de las instrucciones de HiveQL utilizadas en el ejemplo.

##<a id="nextsteps"></a>Pasos siguientes

Para obtener información general sobre subárbol en HDInsight:

* [Usar subárbol con Hadoop en HDInsight](hdinsight-use-hive.md)

Para obtener información sobre otras formas de puede trabajar con Hadoop en HDInsight:

* [Usar cerdo con Hadoop en HDInsight](hdinsight-use-pig.md)

* [Usar MapReduce con Hadoop en HDInsight](hdinsight-use-mapreduce.md)

Si está utilizando Tez con sección, consulte la información de depuración de documentos siguientes:

* [Usar la interfaz de usuario de Tez en HDInsight de Windows](hdinsight-debug-tez-ui.md)

* [Usar la vista de Ambari Tez en HDInsight de Linux](hdinsight-debug-ambari-tez-view.md)

[1]: ../HDInsight/hdinsight-hadoop-visual-studio-tools-get-started.md

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



[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md

[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-submit-jobs]: hdinsight-submit-hadoop-jobs-programmatically.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-get-started]: hdinsight-hadoop-linux-tutorial-get-started.md

[Powershell-install-configure]: powershell-install-configure.md
[powershell-here-strings]: http://technet.microsoft.com/library/ee692792.aspx


[img-hdi-hive-powershell-output]: ./media/hdinsight-use-hive/HDI.Hive.PowerShell.Output.png
