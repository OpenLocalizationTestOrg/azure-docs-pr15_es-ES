<properties
   pageTitle="Usar el shell de sección en HDInsight (Hadoop) | Microsoft Azure"
   description="Aprenda a usar el shell de subárbol con un clúster de HDInsight basados en Linux. Se obtenga información sobre cómo conectar con el clúster de HDInsight mediante SSh y luego usar el Shell de subárbol para ejecutar consultas de forma interactiva."
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
   ms.date="10/04/2016"
   ms.author="larryfr"/>

# <a name="use-hive-with-hadoop-in-hdinsight-with-ssh"></a>Usar subárbol con Hadoop en HDInsight con SSH

[AZURE.INCLUDE [hive-selector](../../includes/hdinsight-selector-use-hive.md)]

En este artículo, aprenderá cómo usar Secure Shell (SSH) para conectarse a un Hadoop en HDInsight de Azure clúster y, a continuación, enviar interactiva subárbol consultas mediante el uso de la sección de línea de comandos interfaz.

> [AZURE.IMPORTANT] Mientras el comando de la sección está disponible en clústeres basados en Linux HDInsight, considere la posibilidad de usar Beeline. BEELINE es un cliente más reciente para trabajar con la sección y se incluye con el clúster de HDInsight. Para obtener más información sobre el uso de esto, vea [Usar subárbol con Hadoop en HDInsight con Beeline](hdinsight-hadoop-use-hive-beeline.md).

##<a id="prereq"></a>Requisitos previos

Para completar los pasos de este artículo, necesita lo siguiente:

* Hadoop basados en Linux en clúster HDInsight.

* Un cliente SSH. Linux, Unix y Mac OS deben proceder con un cliente SSH. Los usuarios de Windows deben descargar a un cliente, como [PuTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).

##<a id="ssh"></a>Conectar con SSH

Conectarse al nombre de dominio completo (FQDN) de su clúster HDInsight mediante el comando SSH. FQDN será el nombre que le asignó el clúster, a continuación, **. azurehdinsight.net**. Por ejemplo, la siguiente ¿conectarse a un clúster denominado **myhdinsight**:

    ssh admin@myhdinsight-ssh.azurehdinsight.net

**Si dispone de una clave de certificado para la autenticación de SSH** cuando creó el clúster de HDInsight, debe especificar la ubicación de la clave privada en su sistema de cliente:

    ssh -i ~/mykey.key admin@myhdinsight-ssh.azurehdinsight.net

**Si dispone de una contraseña para la autenticación de SSH** cuando creó el clúster de HDInsight, debe proporcionar la contraseña cuando se le solicite.

Para obtener más información sobre cómo utilizar SSH con HDInsight, consulte [Usar SSH con basados en Linux Hadoop en HDInsight de Linux, OS X y Unix](hdinsight-hadoop-linux-use-ssh-unix.md).

###<a name="putty-windows-based-clients"></a>PuTTY (clientes basados en Windows)

Windows no proporciona a un cliente SSH integrado. Se recomienda usar **PuTTY**, que puede descargarse desde [http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).

Para obtener más información sobre el uso de PuTTY, vea [Usar SSH con basados en Linux Hadoop en HDInsight de Windows ](hdinsight-hadoop-linux-use-ssh-windows.md).

##<a id="hive"></a>Use el comando de la sección

2. Una vez conectado, iniciar CLI subárbol mediante el siguiente comando:

        hive

3. Utilice la CLI, escriba las siguientes instrucciones para crear una nueva tabla denominada **log4jLogs** usando los datos de ejemplo:

        DROP TABLE log4jLogs;
        CREATE EXTERNAL TABLE log4jLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string)
        ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
        STORED AS TEXTFILE LOCATION 'wasbs:///example/data/';
        SELECT t4 AS sev, COUNT(*) AS count FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log' GROUP BY t4;

    Estas instrucciones realizan las siguientes acciones:

    * **Quitar tabla** - elimina la tabla y el archivo de datos, en caso de que la tabla ya existe.
    * **Crear tabla externa** - crea una nueva tabla 'externa' en la sección. Tablas externas sólo almacenan la definición de tabla en la sección. Los datos se quedan en la ubicación original.
    * **Formato de fila** : la indica subárbol cómo se aplica el formato de los datos. En este caso, los campos de cada registro separados por un espacio.
    * **Almacenados como archivo de texto ubicación** - indica subárbol dónde están los datos almacenados (el directorio de ejemplo de datos) y que TI se almacena como texto.
    * **Seleccione** - selecciona un recuento de todas las filas donde la columna **t4** contiene el valor **[ERROR]**. Como han tres filas que contienen este valor se debe devolver un valor de **3** .
    * **INPUT__FILE__NAME como '%.log'** - indica subárbol que solo se deben devolver datos de archivos que terminen en. log. Esto restringe la búsqueda al archivo sample.log que contiene los datos y evita que devolver datos de ejemplo de otro archivos de datos que no coincidan con el esquema definido.

    > [AZURE.NOTE] Tablas externas se deben utilizar cuando se esperan los datos subyacentes pueden actualizar mediante un origen externo, como un proceso de carga automática de datos, o por otra operación MapReduce, pero siempre desea subárbol consultas para usar los datos más recientes.
    >
    > Anulación de una tabla externa hace **no** eliminar los datos, la definición de la tabla.

4. Use las siguientes instrucciones para crear una nueva tabla 'interna' denominada **errorLogs**:

        CREATE TABLE IF NOT EXISTS errorLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) STORED AS ORC;
        INSERT OVERWRITE TABLE errorLogs SELECT t1, t2, t3, t4, t5, t6, t7 FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log';

    Estas instrucciones realizan las siguientes acciones:

    * **Crear tabla si no existe** - crea una tabla, si aún no existe. Puesto que no se usa la palabra clave **externa** , esta es una tabla interna, que se almacena en el almacén de datos de la sección y se administra completamente por sección.
    * **Almacenados como ORC** - almacena los datos en formato de fila de optimizado en columnas (ORC). Se trata de un formato altamente optimizado y eficaz para almacenar datos de la sección.
    * SOBRESCRIBIR **Insertar... Seleccione** - selecciona las filas de la tabla de **log4jLogs** que contienen **[ERROR]**, a continuación, inserta los datos en la tabla **errorLogs** .

    Para comprobar que se almacenaron solo las filas que contienen **[ERROR]** en la columna t4 a la tabla **errorLogs** , utilice la siguiente instrucción para devolver todas las filas de **errorLogs**:

        SELECT * from errorLogs;

    Tres filas de datos se devuelve, que contiene todos los **[ERROR]** en la columna t4.

    > [AZURE.NOTE] A diferencia de tablas externas, la colocación de una tabla interna eliminará también los datos subyacentes.

##<a id="summary"></a>Resumen

Como puede ver, el comando de la sección proporciona una forma sencilla de forma interactiva ejecutar consultas de la sección en un clúster de HDInsight, supervisar el estado del trabajo y recuperar el resultado.

##<a id="nextsteps"></a>Pasos siguientes

Para obtener información general sobre subárbol en HDInsight:

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

[putty]: http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html

[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-submit-jobs]: hdinsight-submit-hadoop-jobs-programmatically.md
[hdinsight-upload-data]: hdinsight-upload-data.md



[powershell-here-strings]: http://technet.microsoft.com/library/ee692792.aspx


[img-hdi-hive-powershell-output]: ./media/hdinsight-use-hive/HDI.Hive.PowerShell.Output.png

