<properties
   pageTitle="Sección consulta con Hadoop tools para Visual Studio | Microsoft Azure"
   description="Aprenda a usar subárbol con Hadoop en HDInsight con herramientas Hadoop de Visual Studio."
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

#<a name="run-hive-queries-using-the-hdinsight-tools-for-visual-studio"></a>Ejecutar consultas de la sección con las herramientas de HDInsight para Visual Studio

[AZURE.INCLUDE [hive-selector](../../includes/hdinsight-selector-use-hive.md)]

En este artículo, aprenderá a enviar las consultas de la sección a un clúster de HDInsight con las herramientas de HDInsight para Visual Studio.

> [AZURE.NOTE] Este documento no proporciona una descripción detallada de lo que siga las instrucciones de HiveQL utilizadas en los ejemplos. Para obtener información sobre la HiveQL que se usa en este ejemplo, vea [Usar subárbol con Hadoop en HDInsight](hdinsight-use-hive.md).

##<a id="prereq"></a>Requisitos previos

Para completar los pasos de este artículo, necesita lo siguiente.

* Un clúster de Azure HDInsight (Hadoop en HDInsight) (Linux o basado en Windows)

* Visual Studio (una de las siguientes versiones):

    Visual Studio 2013 Comunidad/Professional o Premium o Ultimate con [4 de actualización](https://www.microsoft.com/download/details.aspx?id=44921)

    Visual Studio 2015 (Comunidad/Enterprise)

- HDInsight tools para Visual studio. Para obtener información sobre cómo instalar y configurar las herramientas, vea [Introducción al uso de herramientas de Hadoop de Visual Studio para HDInsight](hdinsight-hadoop-visual-studio-tools-get-started.md) .

##<a id="run"></a>Ejecutar consultas de la sección con las herramientas de HDInsight para Visual Studio

1. Abra **Visual Studio** y seleccione **nuevo** > **proyecto** > **HDInsight** > **Aplicación subárbol**. Proporcione un nombre para este proyecto.

2. Abra el archivo **Script.hql** que se crea con este proyecto y pegar en las siguientes afirmaciones HiveQL:

        set hive.execution.engine=tez;
        DROP TABLE log4jLogs;
        CREATE EXTERNAL TABLE log4jLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string)
        ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
        STORED AS TEXTFILE LOCATION 'wasbs:///example/data/';
        SELECT t4 AS sev, COUNT(*) AS count FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log' GROUP BY t4;

    Estas instrucciones realizan las siguientes acciones:

    * **Quitar tabla**: elimina la tabla y el archivo de datos si la tabla ya existe.
    * **Crear una tabla externa**: crea una nueva tabla 'externa' en la sección. Tablas externas sólo almacenan la definición de tabla en la sección (los datos permanecen en la ubicación original).

        > [AZURE.NOTE] Tablas externas se deben usar cuando se esperan los datos subyacentes que se actualicen por un origen externo (por ejemplo, un proceso de carga automática de datos) o por otra operación MapReduce, pero desea siempre las consultas de la sección para usar los datos más recientes.
        >
        > Anulación de una tabla externa hace **no** eliminar los datos, la definición de la tabla.

    * **Formato de fila**: indica subárbol cómo se aplica el formato de los datos. En este caso, los campos de cada registro separados por un espacio.
    * **Ubicación de archivo de texto como almacenados**: indica subárbol dónde están los datos almacenados (el directorio de ejemplo de datos) y que se almacena como texto.
    * **Seleccionar**: seleccione un recuento de todas las filas donde la columna **t4** contienen el valor **[ERROR]**. Esto debe devolver un valor de **3** porque hay tres filas que contienen este valor.
    * **INPUT__FILE__NAME como '%.log'** - indica subárbol que solo se deben devolver datos de archivos que terminen en. log. Esto restringe la búsqueda al archivo sample.log que contiene los datos y evita que devolver datos de ejemplo de otro archivos de datos que no coincidan con el esquema definido.

3. Desde la barra de herramientas, seleccione el **HDInsight clúster** que desea utilizar para esta consulta y, a continuación, seleccione **Enviar a WebHCat** para ejecutar las instrucciones como una tarea de subárbol con WebHCat. También puede enviar el trabajo con el botón __Ejecutar a través de HiveServer2__ si HiveServer2 está disponible en su versión de clúster. **Resumen de tareas de la sección** aparece y muestra información sobre el trabajo en ejecución. Use el vínculo **Actualizar** para actualizar la información del trabajo, hasta que cambie el **Estado del trabajo** **completado**.

4. Utilice el vínculo de **Salida de trabajo** para ver el resultado de esta tarea. Debe mostrar `[ERROR] 3`, que es el valor devuelto por la instrucción SELECT.

5. También puede ejecutar consultas de la sección sin crear un proyecto. Con el **Explorador de servidores**, expanda **Azure** > **HDInsight**, haga clic en el servidor HDInsight y luego seleccione **escribir una consulta de sección**.

6. En el documento **temp.hql** que aparece, agregue las siguientes instrucciones de HiveQL:

        set hive.execution.engine=tez;
        CREATE TABLE IF NOT EXISTS errorLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) STORED AS ORC;
        INSERT OVERWRITE TABLE errorLogs SELECT t1, t2, t3, t4, t5, t6, t7 FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log';

    Estas instrucciones realizan las siguientes acciones:

    * **Crear tabla si no existe**: si aún no existe, se crea una tabla. Ya no se usa la palabra clave **externa** , esta es una tabla interna, que se almacena en el almacén de datos de la sección y se administra completamente por sección.

        > [AZURE.NOTE] A diferencia de tablas **externas** , anulación de una tabla interna también elimina los datos subyacentes.

    * **Almacenados como ORC**: almacena los datos en formato de fila optimizado columnas (ORC). Se trata de un formato altamente optimizado y eficaz para almacenar datos de la sección.
    * SOBRESCRIBIR **Insertar... Seleccione**: selecciona las filas de la tabla **log4jLogs** que contienen **[ERROR]**, a continuación, inserta los datos en la tabla **errorLogs** .

7. En la barra de herramientas, seleccione **Enviar** para ejecutar el trabajo. Use el **Estado de la tarea** para determinar que el trabajo se ha completado correctamente.

8. Para comprobar que el trabajo completado y crea una nueva tabla, use el **Explorador de servidores** y expanda **Azure** > **HDInsight** > clúster HDInsight > **Subárbol bases de datos** > y **predeterminado**. Vea la tabla de **errorLogs** y la tabla **log4jLogs** .

##<a id="summary"></a>Resumen

Como puede ver, la las herramientas de HDInsight para Visual Studio proporcionan una forma sencilla de ejecutar consultas de la sección en un clúster de HDInsight, supervisar el estado del trabajo y recuperar el resultado.

##<a id="nextsteps"></a>Pasos siguientes

Para obtener información general sobre subárbol en HDInsight:

* [Usar subárbol con Hadoop en HDInsight](hdinsight-use-hive.md)

Para obtener información sobre otras formas de puede trabajar con Hadoop en HDInsight:

* [Usar cerdo con Hadoop en HDInsight](hdinsight-use-pig.md)

* [Usar MapReduce con Hadoop en HDInsight](hdinsight-use-mapreduce.md)

Para obtener más información acerca de las herramientas de HDInsight para Visual Studio:

* [Introducción a HDInsight tools para Visual Studio](../HDInsight/hdinsight-hadoop-visual-studio-tools-get-started.md)


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

[powershell-here-strings]: http://technet.microsoft.com/library/ee692792.aspx

[image-hdi-hive-powershell]: ./media/hdinsight-use-hive/HDI.HIVE.PowerShell.png
[img-hdi-hive-powershell-output]: ./media/hdinsight-use-hive/HDI.Hive.PowerShell.Output.png
[image-hdi-hive-architecture]: ./media/hdinsight-use-hive/HDI.Hive.Architecture.png
