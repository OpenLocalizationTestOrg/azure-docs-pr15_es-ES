<properties
   pageTitle="Usar la sección Hadoop y escritorio remoto en HDInsight | Microsoft Azure"
   description="Obtenga información sobre cómo conectarse a clúster de Hadoop en HDInsight con Escritorio remoto y, a continuación, ejecutar consultas de subárbol mediante la interfaz de línea de comandos de sección."
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
   ms.date="09/06/2016"
   ms.author="larryfr"/>

# <a name="use-hive-with-hadoop-on-hdinsight-with-remote-desktop"></a>Usar subárbol con Hadoop en HDInsight con Escritorio remoto

[AZURE.INCLUDE [hive-selector](../../includes/hdinsight-selector-use-hive.md)]

En este artículo, se obtenga información sobre cómo conectarse a un clúster de HDInsight con Escritorio remoto y, a continuación, ejecutar consultas subárbol mediante la sección de línea de comandos (CLI).

> [AZURE.NOTE] Este documento no proporciona una descripción detallada de lo que siga las instrucciones de HiveQL que se usan en los ejemplos. Para obtener información sobre la HiveQL que se usa en este ejemplo, vea [Usar subárbol con Hadoop en HDInsight](hdinsight-use-hive.md).

##<a id="prereq"></a>Requisitos previos

Para completar los pasos de este artículo, necesita lo siguiente:

* Un clúster basado en Windows HDInsight (Hadoop en HDInsight)

* Un equipo cliente con Windows 10, ventana 8 o Windows 7

##<a id="connect"></a>Conectar con Escritorio remoto

Habilitar Escritorio remoto para el clúster HDInsight y luego conectarse siguiendo las instrucciones en [conectarse a clústeres HDInsight con RDP](hdinsight-administer-use-management-portal.md#rdp).

##<a id="hive"></a>Use el comando de la sección

Cuando se ha conectado a la versión de escritorio para el clúster HDInsight, realice los pasos siguientes para trabajar con la sección:

1. En el escritorio HDInsight, comience a la **línea de comandos de Hadoop**.

2. Escriba el comando siguiente para iniciar la CLI subárbol:

        %hive_home%\bin\hive

    Cuando haya iniciado la CLI, verá el indicador de CLI subárbol: `hive>`.

3. Utilice la CLI, escriba las siguientes instrucciones para crear una nueva tabla denominada **log4jLogs** con datos de ejemplo:

        set hive.execution.engine=tez;
        DROP TABLE log4jLogs;
        CREATE EXTERNAL TABLE log4jLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string)
        ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
        STORED AS TEXTFILE LOCATION 'wasbs:///example/data/';
        SELECT t4 AS sev, COUNT(*) AS count FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log' GROUP BY t4;

    Estas instrucciones realizan las siguientes acciones:

    * **Quitar tabla**: elimina la tabla y el archivo de datos si la tabla ya existe.

    * **Crear una tabla externa**: crea una nueva tabla 'externa' en la sección. Tablas externas almacenan la definición de la tabla en la sección (los datos permanecen en la ubicación original).

        > [AZURE.NOTE] Tablas externas se deben usar cuando se esperan los datos subyacentes que se actualicen por un origen externo (por ejemplo, un proceso de carga automática de datos) o por otra operación MapReduce, pero desea siempre las consultas de la sección para usar los datos más recientes.
        >
        > Anulación de una tabla externa hace **no** eliminar los datos, la definición de la tabla.

    * **Formato de fila**: indica subárbol cómo se aplica el formato de los datos. En este caso, los campos de cada registro separados por un espacio.

    * **Ubicación de archivo de texto como almacenados**: indica subárbol dónde están los datos almacenados (el directorio de ejemplo de datos) y que se almacena como texto.

    * **Seleccionar**: selecciona un recuento de todas las filas donde la columna **t4** contiene el valor **[ERROR]**. Esto debe devolver un valor de **3** porque hay tres filas que contienen este valor.

    * **INPUT__FILE__NAME como '%.log'** - indica subárbol que solo se deben devolver datos de archivos que terminen en. log. Esto restringe la búsqueda al archivo sample.log que contiene los datos y evita que devolver datos de ejemplo de otro archivos de datos que no coincidan con el esquema definido.


4. Use las siguientes instrucciones para crear una nueva tabla 'interna' denominada **errorLogs**:

        CREATE TABLE IF NOT EXISTS errorLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) STORED AS ORC;
        INSERT OVERWRITE TABLE errorLogs SELECT t1, t2, t3, t4, t5, t6, t7 FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log';

    Estas instrucciones realizan las siguientes acciones:

    * **Crear tabla si no existe**: si aún no existe, se crea una tabla. Ya no se usa la palabra clave **externa** , esta es una tabla interna, que se almacena en el almacén de datos de la sección y se administra completamente por sección.

        > [AZURE.NOTE] A diferencia de tablas **externas** , anulación de una tabla interna también elimina los datos subyacentes.

    * **Almacenados como ORC**: almacena los datos en formato de fila optimizado columnas (ORC). Se trata de un formato altamente optimizado y eficaz para almacenar datos de la sección.

    * SOBRESCRIBIR **Insertar... Seleccione**: selecciona las filas de la tabla **log4jLogs** que contienen **[ERROR]**, a continuación, inserta los datos en la tabla **errorLogs** .

    Para comprobar que se almacenaron solo las filas que contienen **[ERROR]** en la columna t4 a la tabla **errorLogs** , utilice la siguiente instrucción para devolver todas las filas de **errorLogs**:

        SELECT * from errorLogs;

    Tres filas de datos se devuelve, que contiene todos los **[ERROR]** en la columna t4.

##<a id="summary"></a>Resumen

Como puede ver, el comando de la sección proporciona una forma sencilla de forma interactiva ejecutar consultas de la sección en un clúster de HDInsight, supervisar el estado del trabajo y recuperar el resultado.

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





[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-submit-jobs]: hdinsight-submit-hadoop-jobs-programmatically.md
[hdinsight-upload-data]: hdinsight-upload-data.md


[Powershell-install-configure]: ../powershell-install-configure.md
[powershell-here-strings]: http://technet.microsoft.com/library/ee692792.aspx

