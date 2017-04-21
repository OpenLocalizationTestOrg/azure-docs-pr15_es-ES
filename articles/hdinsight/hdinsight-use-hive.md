<properties
    pageTitle="Obtenga información sobre cómo usar HiveQL y ¿qué es la sección | Microsoft Azure"
    description="Obtenga información sobre Apache subárbol y cómo usarla con Hadoop en HDInsight. Elija cómo desea ejecutar el trabajo de sección y usar HiveQL para analizar un archivo de ejemplo Apache log4j."
    keywords="hiveql, ¿qué es la sección"
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
    ms.date="09/19/2016"
    ms.author="larryfr"/>

# <a name="use-hive-and-hiveql-with-hadoop-in-hdinsight-to-analyze-a-sample-apache-log4j-file"></a>Usar la sección y HiveQL con Hadoop en HDInsight para analizar un archivo de ejemplo Apache log4j

[AZURE.INCLUDE [hive-selector](../../includes/hdinsight-selector-use-hive.md)]


En este tutorial, podrá obtener información sobre cómo usar Apache subárbol en Hadoop en HDInsight y elija cómo realizar su trabajo de la sección. También aprenderá HiveQL y cómo analizar un archivo de ejemplo Apache log4j.

##<a id="why"></a>¿Qué es subárbol y por qué usa?
[Sección de Apache](http://hive.apache.org/) es un sistema de almacén de datos para Hadoop, que permite resumen de datos, consultas y análisis de datos mediante HiveQL (un lenguaje de consulta similar a SQL). Sección puede utilizarse para explorar los datos de forma interactiva o para crear trabajos de procesamiento de lote reutilizable.

Sección le permite a la estructura de proyecto en datos estructurados en gran medida. Después de definir la estructura, puede usar subárbol para consultar los datos sin conocimiento de Java o MapReduce. **HiveQL** (el lenguaje de consulta de subárbol) le permite escribir consultas con las instrucciones que son similares a T SQL.

Sección sabe cómo trabajar con datos estructurados y semiestructurados, como archivos de texto donde los campos están delimitados por caracteres específicos. Sección también admite personalizado **serializador/deserializadores (SerDe)** para datos complejos o irregular estructurados. Para obtener más información, vea [cómo usar un SerDe JSON personalizado con HDInsight](http://blogs.msdn.com/b/bigdatasupport/archive/2014/06/18/how-to-use-a-custom-json-serde-with-microsoft-azure-hdinsight.aspx).

## <a name="user-defined-functions-udf"></a>Funciones definidas por el usuario (UDF)

También puede ampliarse subárbol a través de **funciones definidas por el usuario (UDF)**. Un archivo UDF le permite implementar la funcionalidad o lógica que no es fácil modelado en HiveQL. Para obtener un ejemplo del uso de UDF con subárbol, consulte lo siguiente:

* [Función con sección definida por el uso de un usuario de Java](hdinsight-hadoop-hive-java-udf.md)

* [Usar Python con la sección y cerdo en HDInsight](hdinsight-python.md)

* [Usar C# con la sección y cerdo en HDInsight](hdinsight-hadoop-hive-pig-udf-dotnet-csharp.md)

* [Cómo agregar una UDF subárbol personalizado a HDInsight](http://blogs.msdn.com/b/bigdatasupport/archive/2014/01/14/how-to-add-custom-hive-udfs-to-hdinsight.aspx)

* [Ejemplo de subárbol UDF personalizado para convertir los formatos de fecha y hora a marca de tiempo de sección](https://github.com/Azure-Samples/hdinsight-java-hive-udf)

## <a name="hive-internal-tables-vs-external-tables"></a>Sección tablas externas de las tablas internas vs

Hay algunas cosas que debe saber acerca de las tablas de subárbol internos y externos:

- El comando **Crear tabla** crea una tabla interna. El archivo de datos debe estar ubicado en el contenedor predeterminado.
- El comando **Crear tabla** mueve el archivo de datos a /hive/almacén/<TableName> carpeta.
- El comando **Crear tabla externa** crea una tabla externa. El archivo de datos puede encontrarse fuera del contenedor de forma predeterminada.
- El comando **Crear tablas externas** no mueva el archivo de datos.
- El comando **Crear tablas externas** no permite todas las carpetas en la ubicación. Esta es la razón por qué el tutorial hace una copia del archivo sample.log.

Para obtener más información, consulte [HDInsight: subárbol internos y externos introducción de tablas][cindygross-hive-tables].


##<a id="data"></a>Acerca de los datos de ejemplo, un archivo de log4j Apache

Este ejemplo usa un archivo de ejemplo *log4j* , que se almacena en **/example/data/sample.log** en el contenedor de almacenamiento de blobs de Windows. Cada registro dentro del archivo consta de una línea de campos que contiene un `[LOG LEVEL]` campo para mostrar el tipo y la gravedad, por ejemplo:

    2012-02-03 20:26:41 SampleClass3 [ERROR] verbose detail for id 1527353937

En el ejemplo anterior, el nivel de registro es el ERROR.

> [AZURE.NOTE] Puede también generar un archivo de log4j mediante la herramienta de registro de [Apache Log4j](http://en.wikipedia.org/wiki/Log4j) y, a continuación, cargar ese archivo en el contenedor de blob. Para obtener instrucciones, vea [Cargar datos a HDInsight](hdinsight-upload-data.md) . Para obtener más información sobre cómo se usa el almacenamiento de blobs de Windows Azure con HDInsight, consulte [Usar almacenamiento de blobs de Azure con HDInsight](hdinsight-hadoop-use-blob-storage.md).

Los datos de ejemplo se almacenan en el almacenamiento de blobs de Windows Azure, que usa HDInsight como el sistema de archivos de forma predeterminada. HDInsight puede tener acceso a los archivos almacenados en BLOB mediante el prefijo **wasb** . Por ejemplo, para obtener acceso al archivo sample.log, debería usar la siguiente sintaxis:

    wasbs:///example/data/sample.log

Dado que el almacenamiento de blobs de Windows Azure es el almacenamiento predeterminado para HDInsight, también puede acceder al archivo utilizando **/example/data/sample.log** desde HiveQL.

> [AZURE.NOTE] La sintaxis de la **wasbs: / / /**, se utiliza para acceder a los archivos almacenados en el contenedor de almacenamiento predeterminada para el clúster de HDInsight. Si especifica cuentas de almacenamiento adicional cuando aprovisionado el clúster y desea obtener acceso a los archivos almacenados en estas cuentas, puede acceder a los datos especificando el contenedor almacenamiento y el nombre de dirección de la cuenta, por ejemplo, **wasbs://mycontainer@mystorage.blob.core.windows.net/example/data/sample.log**.

##<a id="job"></a>Trabajo de ejemplo: proyectar columnas en datos delimitado

Las siguientes afirmaciones HiveQL proyección columnas en datos delimitado que se almacenan en el **wasbs: / / o/datos de ejemplo** directorio:

    set hive.execution.engine=tez;
    DROP TABLE log4jLogs;
    CREATE EXTERNAL TABLE log4jLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string)
    ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
    STORED AS TEXTFILE LOCATION 'wasbs:///example/data/';
    SELECT t4 AS sev, COUNT(*) AS count FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log' GROUP BY t4;

En el ejemplo anterior, las instrucciones de HiveQL realizan las siguientes acciones:

* __establecer hive.execution.engine=tez;__: establece el motor de ejecución usar Tez. Usar Tez en lugar de MapReduce puede proporcionar un aumento en el rendimiento de la consulta. Para obtener más información sobre Tez, vea la sección [Uso Apache Tez para mejorar el rendimiento](#usetez) .

    > [AZURE.NOTE] Esta declaración solo es necesario cuando se utiliza un clúster basado en Windows HDInsight; Tez es el motor de ejecución de forma predeterminada para HDInsight de Linux.

* **Quitar tabla**: elimina la tabla y el archivo de datos si la tabla ya existe.
* **Crear una tabla externa**: crea una nueva tabla **externos** en la sección. Tablas externas sólo almacenan la definición de tabla en la sección; los datos se quedan en la ubicación original y en el formato original.
* **Formato de fila**: indica subárbol cómo se aplica el formato de los datos. En este caso, los campos de cada registro separados por un espacio.
* **Ubicación de archivo de texto como almacenados**: indica subárbol dónde están los datos almacenados (el directorio de ejemplo de datos) y que se almacena como texto. Los datos pueden estar en un archivo o distribuidos en varios archivos en el directorio.
* **Seleccionar**: selecciona un recuento de todas las filas donde la columna **t4** contiene el valor **[ERROR]**. Esto debe devolver un valor de **3** porque hay tres filas que contienen este valor.
* **INPUT__FILE__NAME como '%.log'** - indica subárbol que solo se deben devolver datos de archivos que terminen en. log. Esto restringe la búsqueda al archivo sample.log que contiene los datos y evita que devolver datos de ejemplo de otro archivos de datos que no coincidan con el esquema definido.

> [AZURE.NOTE] Tablas externas se recomienda cuando esperan los datos subyacentes que se actualicen por un origen externo, como un proceso de carga automática de datos, o por otra operación MapReduce y desea siempre las consultas de la sección para usar los datos más recientes.
>
> Anulación de una tabla externa hace **no** eliminar los datos, solo se elimina la definición de tabla.

Después de crear la tabla externa, las siguientes instrucciones se usan para crear una tabla **interna** .

    set hive.execution.engine=tez;
    CREATE TABLE IF NOT EXISTS errorLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string)
    STORED AS ORC;
    INSERT OVERWRITE TABLE errorLogs
    SELECT t1, t2, t3, t4, t5, t6, t7 FROM log4jLogs WHERE t4 = '[ERROR]';

Estas instrucciones realizan las siguientes acciones:

* **Crear tabla si no existe**: crea una tabla, si aún no existe. Ya no se usa la palabra clave **externa** , esta es una tabla interna, que se almacena en el almacén de datos de la sección y se administra completamente por sección.
* **Almacenados como ORC**: almacena los datos en formato de fila de optimizado en columnas (ORC). Se trata de un formato altamente optimizado y eficaz para almacenar datos de la sección.
* SOBRESCRIBIR **Insertar... Seleccione**: selecciona las filas de la tabla de **log4jLogs** que contiene **[ERROR]**y, a continuación, inserta los datos en la tabla **errorLogs** .

> [AZURE.NOTE] A diferencia de tablas externas, anulación de una tabla interna también elimina los datos subyacentes.

##<a id="usetez"></a>Usar Apache Tez para mejorar el rendimiento

[Tez Apache](http://tez.apache.org) es un marco que permite a las aplicaciones de datos intensivo, como subárbol para ejecutar mucho más eficaz a escala. En la versión más reciente de HDInsight, subárbol admite la ejecución de Tez. Tez está habilitada para clústeres basados en Linux HDInsight de forma predeterminada.

> [AZURE.NOTE] Tez actualmente está desactivada de forma predeterminada para clústeres HDInsight basado en Windows y debe estar habilitado. Para aprovechar las ventajas de Tez, se debe establecer el siguiente valor de una consulta de sección:
>
> ```set hive.execution.engine=tez;```
>
>Puede enviar de forma por consulta colocando al principio de la consulta. También puede establecer esta opción para estar encendido en un clúster de forma predeterminada, establezca el valor de configuración al crear el clúster. Puede encontrar más detalles en [El aprovisionamiento de HDInsight clústeres](hdinsight-provision-clusters.md).

La [sección en documentos de diseño de Tez](https://cwiki.apache.org/confluence/display/Hive/Hive+on+Tez) contienen un número de detalles acerca de las opciones de implementación y configuraciones de optimización.

Ayuda depurar trabajos ejecutó con Tez, HDInsight proporciona interfaces de usuario que le permitan ver los detalles de los trabajos de Tez web siguiente:

* [Usar la interfaz de usuario de Tez en HDInsight de Windows](hdinsight-debug-tez-ui.md)

* [Usar la vista de Ambari Tez en HDInsight de Linux](hdinsight-debug-ambari-tez-view.md)

##<a id="run"></a>Elija cómo realizar el trabajo HiveQL

HDInsight puede ejecutar trabajos HiveQL con una amplia variedad de métodos. Utilice la siguiente tabla para decidir qué método es adecuado para usted y luego siga el vínculo para obtener un tutorial.

| **Use esta opción** si desea...                                                     | .. .an shell **interactivo** | ... proceso **por lotes** | .. .with este **sistema operativo del clúster** | .. .de este **sistema operativo del cliente** |
|:--------------------------------------------------------------------------------|:---------------------------:|:-----------------------:|:------------------------------------------|:-----------------------------------------|
| [Vista de sección](hdinsight-hadoop-use-hive-ambari-view.md) | ✔ | ✔ | Linux | Cualquier (en función de explorador) |
| [Comando BEELINE (desde una sesión SSH)](hdinsight-hadoop-use-hive-beeline.md)                                         |              ✔              |            ✔            | Linux                                     | Linux, Unix, Mac OS X o Windows        |
| [Comando de la sección (de una sesión SSH)](hdinsight-hadoop-use-hive-ssh.md)                                         |              ✔              |            ✔            | Linux                                     | Linux, Unix, Mac OS X o Windows        |
| [Doblez](hdinsight-hadoop-use-hive-curl.md)                                       |           &nbsp;            |            ✔            | Linux o Windows                          | Linux, Unix, Mac OS X o Windows        |
| [Consola de consulta](hdinsight-hadoop-use-hive-query-console.md)                     |           &nbsp;            |            ✔            | Windows                                   | Cualquier (en función de explorador)                            |
| [HDInsight tools para Visual Studio](hdinsight-hadoop-use-hive-visual-studio.md) |           &nbsp;            |            ✔            | Linux o Windows                          | Windows                                  |
| [Windows PowerShell](hdinsight-hadoop-use-hive-powershell.md)                   |           &nbsp;            |            ✔            | Linux o Windows                          | Windows                                  |
| [Escritorio remoto](hdinsight-hadoop-use-hive-remote-desktop.md)                   |              ✔              |            ✔            | Windows                                   | Windows                                  |

## <a name="running-hive-jobs-on-azure-hdinsight-using-on-premises-sql-server-integration-services"></a>Ejecución de trabajos de subárbol en Azure HDInsight con local SQL Server Integration Services

También puede usar SQL Server Integration Services (SSIS) para ejecutar un trabajo de la sección. El Feature Pack de Windows Azure para SSIS proporciona los siguientes componentes que funcionan con sección trabajos en HDInsight.


- [Tareas de la sección de HDInsight de Azure][hivetask]
- [Administrador de conexiones de suscripción de Azure][connectionmanager]


Más información sobre el Feature Pack de Azure para SSIS [aquí][ssispack].


##<a id="nextsteps"></a>Pasos siguientes

Ahora que ha aprendido subárbol es y cómo usarla con Hadoop en HDInsight, use los siguientes vínculos para explorar otras formas de trabajar con HDInsight de Azure.


- [Cargar datos a HDInsight][hdinsight-upload-data]
- [Usar cerdo con HDInsight][hdinsight-use-pig]
- [Usar Sqoop con HDInsight](hdinsight-use-sqoop.md)
- [Usar Oozie con HDInsight](hdinsight-use-oozie.md)
- [Usar MapReduce trabajos con HDInsight][hdinsight-use-mapreduce]

[check]: ./media/hdinsight-use-hive/hdi.checkmark.png

[hdinsight-sdk-documentation]: http://msdnstage.redmond.corp.microsoft.com/library/dn479185.aspx

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[apache-tez]: http://tez.apache.org
[apache-hive]: http://hive.apache.org/
[apache-log4j]: http://en.wikipedia.org/wiki/Log4j
[hive-on-tez-wiki]: https://cwiki.apache.org/confluence/display/Hive/Hive+on+Tez
[import-to-excel]: http://azure.microsoft.com/documentation/articles/hdinsight-connect-excel-power-query/
[hivetask]: http://msdn.microsoft.com/library/mt146771(v=sql.120).aspx
[connectionmanager]: http://msdn.microsoft.com/library/mt146773(v=sql.120).aspx
[ssispack]: http://msdn.microsoft.com/library/mt146770(v=sql.120).aspx

[hdinsight-use-pig]: hdinsight-use-pig.md
[hdinsight-use-oozie]: hdinsight-use-oozie.md
[hdinsight-analyze-flight-data]: hdinsight-analyze-flight-delay-data.md
[hdinsight-use-mapreduce]: hdinsight-use-mapreduce.md


[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md

[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-submit-jobs]: hdinsight-submit-hadoop-jobs-programmatically.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-get-started]: hdinsight-get-started.md

[Powershell-install-configure]: ../powershell-install-configure.md
[powershell-here-strings]: http://technet.microsoft.com/library/ee692792.aspx

[image-hdi-hive-powershell]: ./media/hdinsight-use-hive/HDI.HIVE.PowerShell.png
[img-hdi-hive-powershell-output]: ./media/hdinsight-use-hive/HDI.Hive.PowerShell.Output.png
[image-hdi-hive-architecture]: ./media/hdinsight-use-hive/HDI.Hive.Architecture.png


[cindygross-hive-tables]: http://blogs.msdn.com/b/cindygross/archive/2013/02/06/hdinsight-hive-internal-and-external-tables-intro.aspx
