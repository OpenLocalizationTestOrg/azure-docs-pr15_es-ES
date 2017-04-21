<properties
   pageTitle="Usar Beeline para trabajar con la sección de HDInsight (Hadoop) | Microsoft Azure"
   description="Obtenga información sobre cómo utilizar SSH para conectarse a un clúster de Hadoop en HDInsight y, a continuación, envíe interactiva subárbol consultas usando Beeline. BEELINE es una utilidad para trabajar con HiveServer2 sobre JDBC."
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
   ms.date="10/10/2016"
   ms.author="larryfr"/>

#<a name="use-hive-with-hadoop-in-hdinsight-with-beeline"></a>Usar subárbol con Hadoop en HDInsight con Beeline

[AZURE.INCLUDE [hive-selector](../../includes/hdinsight-selector-use-hive.md)]

En este artículo, aprenderá cómo usar Secure Shell (SSH) para conectarse a un clúster de HDInsight basados en Linux y, a continuación, envíe interactiva subárbol consultas mediante la herramienta de línea de comandos de [Beeline](https://cwiki.apache.org/confluence/display/Hive/HiveServer2+Clients#HiveServer2Clients-Beeline–NewCommandLineShell) .

> [AZURE.NOTE] BEELINE usa JDBC para conectarse a la sección. Para obtener más información sobre cómo utilizar JDBC con sección, vea [conectarse a subárbol en Azure HDInsight con el controlador de JDBC subárbol](hdinsight-connect-hive-jdbc-driver.md).

##<a id="prereq"></a>Requisitos previos

Para completar los pasos de este artículo, necesita lo siguiente:

* Hadoop basados en Linux en clúster HDInsight.

* Un cliente SSH. Linux, Unix y Mac OS deben proceder con un cliente SSH. Los usuarios de Windows deben descargar a un cliente, como [PuTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).

##<a id="ssh"></a>Conectar con SSH

Conectarse al nombre de dominio completo (FQDN) de su clúster HDInsight mediante el comando SSH. FQDN será el nombre que le asignó el clúster, a continuación, **. azurehdinsight.net**. Por ejemplo, la siguiente ¿conectarse a un clúster denominado **myhdinsight**:

    ssh admin@myhdinsight-ssh.azurehdinsight.net

**Si dispone de una clave de certificado para la autenticación de SSH** cuando creó el clúster de HDInsight, debe especificar la ubicación de la clave privada en su sistema de cliente:

    ssh admin@myhdinsight-ssh.azurehdinsight.net -i ~/mykey.key

**Si dispone de una contraseña para la autenticación de SSH** cuando creó el clúster de HDInsight, debe proporcionar la contraseña cuando se le solicite.

Para obtener más información sobre cómo utilizar SSH con HDInsight, consulte [Usar SSH con basados en Linux Hadoop en HDInsight de Linux, OS X y Unix](hdinsight-hadoop-linux-use-ssh-unix.md).

###<a name="putty-windows-based-clients"></a>PuTTY (clientes basados en Windows)

Windows no proporciona a un cliente SSH integrado. Se recomienda usar **PuTTY**, que puede descargarse desde [http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).

Para obtener más información sobre el uso de PuTTY, vea [Usar SSH con basados en Linux Hadoop en HDInsight de Windows ](hdinsight-hadoop-linux-use-ssh-windows.md).

##<a id="beeline"></a>Use el comando Beeline

1. Una vez conectado, utilice las siguientes acciones para iniciar Beeline:

        beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http' -n admin

    Esto se inicie al cliente de Beeline y conectarse a la dirección url JDBC. Aquí, `localhost` se usa como HiveServer2 se ejecuta en ambos cabezales nodos en el clúster y estamos trabajando Beeline directamente en el headnode principal.
    
    Una vez completado el comando, llegarán a un `jdbc:hive2://localhost:10001/>` símbolo del sistema.

3. Normalmente, los comandos de BEELINE comienzan con un `!` de caracteres, por ejemplo `!help` muestra la Ayuda. Sin embargo, el `!` a menudo se pueden omitir. Por ejemplo, `help` también funcionarán.

    Si ve ayuda, observará `!sql`, que se utiliza para ejecutar instrucciones HiveQL. Sin embargo, HiveQL es tan frecuente que puede omitir la anterior `!sql`. Las dos instrucciones siguientes tienen exactamente los mismos resultados; mostrar las tablas están disponibles a través de la sección:
    
        !sql show tables;
        show tables;
    
    En un clúster de nuevo, debe aparecer una única tabla: __hivesampletable__.

4. Usar las siguientes acciones para mostrar el esquema de la hivesampletable:

        describe hivesampletable;
        
    Devolverá la siguiente información:
    
        +-----------------------+------------+----------+--+
        |       col_name        | data_type  | comment  |
        +-----------------------+------------+----------+--+
        | clientid              | string     |          |
        | querytime             | string     |          |
        | market                | string     |          |
        | deviceplatform        | string     |          |
        | devicemake            | string     |          |
        | devicemodel           | string     |          |
        | state                 | string     |          |
        | country               | string     |          |
        | querydwelltime        | double     |          |
        | sessionid             | bigint     |          |
        | sessionpagevieworder  | bigint     |          |
        +-----------------------+------------+----------+--+

    Esto muestra las columnas de la tabla. Mientras se podrían realizar algunas consultas en los datos, en su lugar crear una nueva tabla para demostrar cómo cargar datos en la sección y aplicar un esquema.
    
5. Escriba las siguientes instrucciones para crear una tabla denominada **log4jLogs** con datos de ejemplo proporcionados con el clúster de HDInsight:

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
    * **INPUT__FILE__NAME como '%.log'** - indica subárbol que solo se deben devolver datos de archivos que terminen en. log. Normalmente, solo tendrá datos con el mismo esquema en la misma carpeta al consultar con subárbol, sin embargo, se almacena el archivo de registro de ejemplo con otros formatos de datos.

    > [AZURE.NOTE] Tablas externas se deben utilizar cuando se esperan los datos subyacentes pueden actualizar mediante un origen externo, como un proceso de carga automática de datos, o por otra operación MapReduce, pero siempre desea subárbol consultas para usar los datos más recientes.
    >
    > Anulación de una tabla externa hace **no** eliminar los datos, la definición de la tabla.
    
    El resultado de este comando debe ser similar al siguiente:
    
        INFO  : Tez session hasn't been created yet. Opening session
        INFO  :
        
        INFO  : Status: Running (Executing on YARN cluster with App id application_1443698635933_0001)
        
        INFO  : Map 1: -/-      Reducer 2: 0/1
        INFO  : Map 1: 0/1      Reducer 2: 0/1
        INFO  : Map 1: 0/1      Reducer 2: 0/1
        INFO  : Map 1: 0/1      Reducer 2: 0/1
        INFO  : Map 1: 0/1      Reducer 2: 0/1
        INFO  : Map 1: 0(+1)/1  Reducer 2: 0/1
        INFO  : Map 1: 0(+1)/1  Reducer 2: 0/1
        INFO  : Map 1: 1/1      Reducer 2: 0/1
        INFO  : Map 1: 1/1      Reducer 2: 0(+1)/1
        INFO  : Map 1: 1/1      Reducer 2: 1/1
        +----------+--------+--+
        |   sev    | count  |
        +----------+--------+--+
        | [ERROR]  | 3      |
        +----------+--------+--+
        1 row selected (47.351 seconds)

4. Para salir de Beeline, utilice `!quit`.

##<a id="file"></a>Ejecutar un archivo de HiveQL

BEELINE también puede usarse para ejecutar un archivo que contiene instrucciones HiveQL. Siga los pasos siguientes para crear un archivo, a continuación, ejecutar mediante Beeline.

1. Use el comando siguiente para crear un nuevo archivo denominado __query.hql__:

        nano query.hql
        
2. Una vez que se abre el editor, utilice la siguiente como el contenido del archivo. Esta consulta creará una nueva tabla 'interna' denominada **errorLogs**:

        CREATE TABLE IF NOT EXISTS errorLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) STORED AS ORC;
        INSERT OVERWRITE TABLE errorLogs SELECT t1, t2, t3, t4, t5, t6, t7 FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log';

    Estas instrucciones realizan las siguientes acciones:

    * **Crear tabla si no existe** - crea una tabla, si aún no existe. Puesto que no se usa la palabra clave **externa** , esta es una tabla interna, que se almacena en el almacén de datos de la sección y se administra completamente por sección.
    * **Almacenados como ORC** - almacena los datos en formato de fila de optimizado en columnas (ORC). Se trata de un formato altamente optimizado y eficaz para almacenar datos de la sección.
    * SOBRESCRIBIR **Insertar... Seleccione** - selecciona las filas de la tabla de **log4jLogs** que contienen **[ERROR]**, a continuación, inserta los datos en la tabla **errorLogs** .
    
    > [AZURE.NOTE] A diferencia de tablas externas, la colocación de una tabla interna eliminará también los datos subyacentes.
    
3. Para guardar el archivo, use __Ctrl__+ ___X__y luego escriba __Y__y, por último, __ENTRAR__.

4. Use las siguientes acciones para ejecutar el archivo con Beeline. Reemplazar __HOSTNAME__ con el nombre obtenido anteriormente para el nodo principal y la __contraseña__ con la contraseña de la cuenta de administrador:

        beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http' -n admin -i query.hql

    > [AZURE.NOTE] La `-i` parámetro comienza Beeline, ejecuta las instrucciones en el archivo query.hql y permanece en Beeline en el `jdbc:hive2://localhost:10001/>` símbolo del sistema. También puede ejecutar un archivo mediante la `-f` parámetro, que vuelve a fiesta después de que se ha procesado el archivo.

5. Para comprobar que se creó la tabla **errorLogs** , utilice la siguiente instrucción para devolver todas las filas de **errorLogs**:

        SELECT * from errorLogs;

    Tres filas de datos se devuelve, que contiene todos los **[ERROR]** en la columna t4:
    
        +---------------+---------------+---------------+---------------+---------------+---------------+---------------+--+
        | errorlogs.t1  | errorlogs.t2  | errorlogs.t3  | errorlogs.t4  | errorlogs.t5  | errorlogs.t6  | errorlogs.t7  |
        +---------------+---------------+---------------+---------------+---------------+---------------+---------------+--+
        | 2012-02-03    | 18:35:34      | SampleClass0  | [ERROR]       | incorrect     | id            |               |
        | 2012-02-03    | 18:55:54      | SampleClass1  | [ERROR]       | incorrect     | id            |               |
        | 2012-02-03    | 19:25:27      | SampleClass4  | [ERROR]       | incorrect     | id            |               |
        +---------------+---------------+---------------+---------------+---------------+---------------+---------------+--+
        3 rows selected (1.538 seconds)

## <a name="more-about-beeline-connectivity"></a>Más información sobre la conectividad de Beeline

Uso de los pasos de este documento `localhost` para conectarse a HiveServer2 ejecutar en el clúster de headnode. Aunque también puede usar el nombre de host o el nombre de dominio completo de la headnode aquellos requieren pasos adicionales para el proceso (pasos para buscar el nombre de host o FQDN). Usando `localhost` es suficiente cuando se usa Beeline desde el headnode.

Si tiene un nodo del borde en el clúster con Beeline instalado, debe usar el nombre de host o FQDN de la headnode para conectar.

Si tiene instalado en un cliente fuera de su clúster de Beeline, puede conectarse mediante el siguiente comando. Reemplazar __NOMBREDECLÚSTER__ con el nombre de su clúster HDInsight. Reemplazar la __contraseña__ con la contraseña de la cuenta de administrador (inicio de sesión HTTP).

    beeline -u 'jdbc:hive2://CLUSTERNAME.azurehdinsight.net:443/default;ssl=true?hive.server2.transport.mode=http;hive.server2.thrift.http.path=hive2' -n admin -p PASSWORD

Observe que los parámetros o URI es distinto cuando ejecuta directamente en un headnode o desde un nodo del borde en el clúster. Esto es porque conectarse al clúster de internet utiliza una puerta de enlace público que enruta el tráfico a través del puerto 443. Además, otros servicios se exponen a través de la puerta de enlace público en el puerto 443, por lo que el URI es diferente al conectarse directamente. Cuando se conecta desde internet se debe autenticar la sesión proporcionando la contraseña.

##<a id="summary"></a><a id="nextsteps"></a>Pasos siguientes

Como puede ver, el comando Beeline proporciona una forma sencilla de forma interactiva ejecutar consultas de la sección en un clúster de HDInsight.

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

