<properties
    pageTitle="Memoria insuficiente (OOM) - configuración de la sección | Microsoft Azure"
    description="Corregir un error de memoria (insuficiente OOM) de una consulta de la sección fuera de en Hadoop en HDInsight. El escenario de cliente es una consulta en muchas tablas de gran tamaño."
    keywords="fuera de la sección Configuración de memoria error, OOM,"
    services="hdinsight"
    documentationCenter=""
    authors="rashimg"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="hdinsight"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="big-data"
    ms.date="09/02/2016"
    ms.author="rashimg;jgao"/>

# <a name="fix-an-out-of-memory-oom-error-with-hive-memory-settings-in-hadoop-in-azure-hdinsight"></a>Corregir un error de memoria insuficiente (OOM) con la sección Configuración de la memoria en Hadoop en HDInsight de Azure

Uno de los problemas comunes nominal de nuestros clientes es genera un error de memoria insuficiente (OOM) cuando usando subárbol. En este artículo se describe un escenario de cliente y la configuración de la sección que se recomienda solucionar el problema.

## <a name="scenario-hive-query-across-large-tables"></a>Escenario: Sección consulta a través de tablas de gran tamaño

Un cliente ejecutó la consulta por debajo de la sección.

    SELECT
        COUNT (T1.COLUMN1) as DisplayColumn1,
        …
        …
        ….
    FROM
        TABLE1 T1,
        TABLE2 T2,
        TABLE3 T3,
        TABLE5 T4,
        TABLE6 T5,
        TABLE7 T6
    where (T1.KEY1 = T2.KEY1….
        …
        …

Algunos matices de esta consulta:

* T1 es un alias de una tabla grande, Tabla1, que tiene una gran cantidad de tipos de columnas de cadena.
* Otras tablas no son grandes, pero tiene una gran cantidad de columnas.
* Todas las tablas están uniendo entre sí, en algunos casos con varias columnas de TABLE1 y otros usuarios.

Cuando el cliente ejecutó la consulta mediante subárbol en MapReduce en un nodo 24 clúster A3, ejecutó la consulta en unos minutos 26. El cliente ha observado los siguientes mensajes de advertencia cuando se ejecuta la consulta con subárbol en MapReduce:

    Warning: Map Join MAPJOIN[428][bigTable=?] in task 'Stage-21:MAPRED' is a cross product
    Warning: Shuffle Join JOIN[8][tables = [t1933775, t1932766]] in Stage 'Stage-4:MAPRED' is a cross product

Porque la consulta termina de ejecutarse en unos minutos 26, el cliente omite estas advertencias y en su lugar a concentrarse en cómo mejorar este más el rendimiento de la consulta.

El cliente había consultado [optimizar subárbol consultas para Hadoop en HDInsight](hdinsight-hadoop-optimize-hive-query.md)y decidido utilizar el motor de ejecución de Tez. Una vez que la misma consulta se ha ejecutado con la opción Tez habilitada la consulta ejecutó durante 15 minutos y, a continuación, produjo el siguiente error:

    Status: Failed
    Vertex failed, vertexName=Map 5, vertexId=vertex_1443634917922_0008_1_05, diagnostics=[Task failed, taskId=task_1443634917922_0008_1_05_000006, diagnostics=[TaskAttempt 0 failed, info=[Error: Failure while running task:java.lang.RuntimeException: java.lang.OutOfMemoryError: Java heap space
        at
    org.apache.hadoop.hive.ql.exec.tez.TezProcessor.initializeAndRunProcessor(TezProcessor.java:172)
        at org.apache.hadoop.hive.ql.exec.tez.TezProcessor.run(TezProcessor.java:138)
        at
    org.apache.tez.runtime.LogicalIOProcessorRuntimeTask.run(LogicalIOProcessorRuntimeTask.java:324)
        at
    org.apache.tez.runtime.task.TezTaskRunner$TaskRunnerCallable$1.run(TezTaskRunner.java:176)
        at
    org.apache.tez.runtime.task.TezTaskRunner$TaskRunnerCallable$1.run(TezTaskRunner.java:168)
        at java.security.AccessController.doPrivileged(Native Method)
        at javax.security.auth.Subject.doAs(Subject.java:415)
        at org.apache.hadoop.security.UserGroupInformation.doAs(UserGroupInformation.java:1628)
        at
    org.apache.tez.runtime.task.TezTaskRunner$TaskRunnerCallable.call(TezTaskRunner.java:168)
        at
    org.apache.tez.runtime.task.TezTaskRunner$TaskRunnerCallable.call(TezTaskRunner.java:163)
        at java.util.concurrent.FutureTask.run(FutureTask.java:262)
        at java.util.concurrent.ThreadPoolExecutor.runWorker(ThreadPoolExecutor.java:1145)
        at java.util.concurrent.ThreadPoolExecutor$Worker.run(ThreadPoolExecutor.java:615)
        at java.lang.Thread.run(Thread.java:745)
    Caused by: java.lang.OutOfMemoryError: Java heap space

El cliente luego decidido usar una mayor VM (es decir, D12) pensando una máquina virtual más grande tendría más espacio del montón. Incluso entonces, el cliente continúa viendo el error. El cliente llegó al equipo de HDInsight para obtener ayuda en la depuración de este problema.

## <a name="debug-the-out-of-memory-oom-error"></a>Depurar el error de memoria insuficiente (OOM)

Nuestra página de soporte técnico y equipos de ingeniería juntos encontrados uno de los problemas que causa el error de memoria insuficiente (OOM) era un [problema que se describe en el JIRA Apache conocido](https://issues.apache.org/jira/browse/HIVE-8306). En la descripción de la JIRA:

    When hive.auto.convert.join.noconditionaltask = true we check noconditionaltask.size and if the sum  of tables sizes in the map join is less than noconditionaltask.size the plan would generate a Map join, the issue with this is that the calculation doesnt take into account the overhead introduced by different HashTable implementation as results if the sum of input sizes is smaller than the noconditionaltask size by a small margin queries will hit OOM.

Hemos confirmado que **hive.auto.convert.join.noconditionaltask** en realidad se estableció en **true** buscando en el archivo de subárbol site.xml:

    <property>
        <name>hive.auto.convert.join.noconditionaltask</name>
        <value>true</value>
        <description>
            Whether Hive enables the optimization about converting common join into mapjoin based on the input file size.
            If this parameter is on, and the sum of size for n-1 of the tables/partitions for a n-way join is smaller than the
            specified size, the join is directly converted to a mapjoin (there is no conditional task).
        </description>
    </property>

En función de la advertencia y la JIRA, nuestra hipótesis era que mapa unirse fue la causa del error de Java montón espacio OOM. Para que se descubrió más profunda en este problema.

Como se explica en la [configuración de memoria del hilo de Hadoop en HDInsight](http://blogs.msdn.com/b/shanyu/archive/2014/07/31/hadoop-yarn-memory-settings-in-hdinsigh.aspx)de entrada de blog, cuando Tez es el motor de ejecución utiliza el espacio de montón utilizado realmente al que pertenece el contenedor Tez. Vea la imagen siguiente que describe la memoria del contenedor Tez.

![Diagrama de memoria del contenedor de Tez: subárbol OOM del error de memoria insuficiente](./media/hdinsight-hadoop-hive-out-of-memory-error-oom/hive-out-of-memory-error-oom-tez-container-memory.png)


Como se sugiere la entrada de blog, la siguiente configuración de dos memoria define la memoria de contenedor para el montón: **hive.tez.container.size** y **hive.tez.java.opts**. De nuestra experiencia, la excepción OOM no significa que el tamaño del contenedor es demasiado pequeño. Significa que el tamaño de montón de Java (hive.tez.java.opts) es muy pequeño. Para que siempre vea OOM, puede intentar aumentar **hive.tez.java.opts**. Si es necesario puede que tenga que aumentar **hive.tez.container.size**. La configuración de **java.opts** debe ser aproximadamente el 80% de **container.size**.

> [AZURE.NOTE]  La opción **hive.tez.java.opts** siempre debe ser menor que **hive.tez.container.size**.

Puesto que una máquina D12 tiene 28GB de memoria, hemos decidido usar el tamaño de un contenedor de 10GB (10240MB) y asignar 80% a java.opts. Esto se ha hecho en la consola de sección mediante la configuración siguiente:

    SET hive.tez.container.size=10240
    SET hive.tez.java.opts=-Xmx8192m

En función de esta configuración, la consulta se ejecutó correctamente en menos de diez minutos.

## <a name="conclusion-oom-errors-and-container-size"></a>Conclusión: Errores OOM y el tamaño del contenedor

Recibe un error de OOM no necesariamente que el tamaño del contenedor es demasiado pequeño. En su lugar, debe configurar la configuración de la memoria para que se incrementa el tamaño del montón y al menos el 80% de tamaño de memoria del contenedor.
