<properties
   pageTitle="Optimizar las consultas de la sección ejecución más rápida en HDInsight | Microsoft Azure"
   description="Aprenda a optimizar las consultas de subárbol para Hadoop en HDInsight."
   services="hdinsight"
   documentationCenter=""
   authors="rashimg"
   manager="mwinkle"
   editor="cgronlun"
   tags="azure-portal"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="07/28/2015"
   ms.author="rashimg"/>


# <a name="optimize-hive-queries-for-hadoop-in-hdinsight"></a>Optimizar consultas subárbol para Hadoop en HDInsight

De forma predeterminada, Hadoop clústeres no están optimizados para el rendimiento. Este artículo describen algunos de los métodos de optimización del rendimiento subárbol más comunes que puede aplicar a las consultas.

##<a name="scale-out-worker-nodes"></a>Cambiar la escala de nodos de trabajo

Aumentar el número de nodos de trabajo en un clúster puede aprovechar más Mapeados y reductores que se ejecutará en paralelo. Hay dos formas de aumentar escalada en HDInsight:

- En el momento de aprovisionamiento, puede especificar el número de nodos de trabajo con el Portal de Azure, Azure PowerShell o línea de comandos entre plataformas.  Para obtener más información, consulte [clústeres de HDInsight de aprovisionamiento](hdinsight-provision-clusters.md). La siguiente pantalla, mostrar la configuración de nodos de trabajo en el Portal de Azure:

    ![scaleout_1][image-hdi-optimize-hive-scaleout_1]

- En el tiempo de ejecución, también puede escalar un clúster sin volver a crear uno. Esto se muestra a continuación.
![scaleout_1][image-hdi-optimize-hive-scaleout_2]

Para obtener más detalles sobre las distintas máquinas virtuales compatibles con HDInsight, consulte [HDInsight de precios](https://azure.microsoft.com/pricing/details/hdinsight/).

##<a name="enable-tez"></a>Habilitar Tez

[Tez Apache](http://hortonworks.com/hadoop/tez/) es un motor de ejecución alternativo al motor MapReduce:

![tez_1][image-hdi-optimize-hive-tez_1]


Tez es más rápido porque:

- Ejecutar dirigen acíclicos Graph (DAG incluidos) como un único trabajo en el motor MapReduce, la DAG incluidos que se expresan requieren cada conjunto de Mapeados seguidos de un conjunto de reductores. Esto hace varios trabajos MapReduce poner en marcha para cada consulta de sección. Tez no tiene restricción de este tipo y puede procesar DAG incluidos complejos como un trabajo reduciendo sobrecarga de inicio de trabajo.
- **Escribe evita innecesario** Debido a varios trabajos está girados para la misma consulta subárbol en el motor MapReduce, el resultado de cada tarea se escribe en HDFS para datos intermedios. Puesto que Tez minimiza el número de tareas para cada consulta subárbol es posible evitar escritura innecesario.
- **Minimiza retrasos de inicio** Tez mejor es posible minimizar retraso de inicio al reducir el número de Mapeados que necesita para iniciar y mejorar también optimización a lo largo.
- **Reutiliza contenedores** Siempre que Tez posible es volver a usar contenedores para asegurarse de que se reduce la latencia debido a iniciando contenedores.
- **Técnicas de optimización continua** Tradicionalmente optimización se ha hecho durante la fase de compilación. Sin embargo, hay disponible más información acerca de las entradas que permiten mejor optimización en tiempo de ejecución. Tez usa técnicas de optimización continua que le permite optimizar aún más el plan en la fase de tiempo de ejecución.

Para obtener más detalles sobre estos conceptos, haga clic en [aquí](http://hortonworks.com/hadoop/tez/)

Puede realizar cualquier consulta subárbol Tez habilitado anteponiendo la consulta con la configuración siguiente:

    set hive.execution.engine=tez;

Para clústeres HDInsight basado en Windows, Tez debe estar habilitado en el momento de aprovisionamiento. A continuación se muestra un script de PowerShell de Azure de ejemplo para el aprovisionamiento de un clúster de Hadoop Tez habilitado:

[AZURE.INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]

    $clusterName = "[HDInsightClusterName]"
    $location = "[AzureDataCenter]" #i.e. West US
    $dataNodes = 32 # number of worker nodes in the cluster

    $defaultStorageAccountName = "[DefaultStorageAccountName]"
    $defaultStorageContainerName = "[DefaultBlobContainerName]"
    $defaultStorageAccountKey = $defaultStorageAccountKey = Get-AzureStorageKey $defaultStorageAccountName.ToLower() | %{ $_.Primary }

    $hdiUserName = "[HTTPUserName]"
    $hdiPassword = "[HTTPUserPassword]"

    $hdiSecurePassword = ConvertTo-SecureString $hdiPassword -AsPlainText -Force
    $hdiCredential = New-Object System.Management.Automation.PSCredential($hdiUserName, $hdiSecurePassword)

    $hiveConfig = new-object 'Microsoft.WindowsAzure.Management.HDInsight.Cmdlet.DataObjects.AzureHDInsightHiveConfiguration'
    $hiveConfig.Configuration = @{ "hive.execution.engine"="tez" }

    New-AzureHDInsightClusterConfig -ClusterSizeInNodes $dataNodes -HeadNodeVMSize Standard_D14 -DataNodeVMSize Standard_D14 |
    Set-AzureHDInsightDefaultStorage -StorageAccountName "$defaultStorageAccountName.blob.core.windows.net" -StorageAccountKey $defaultStorageAccountKey -StorageContainerName $defaultStorageContainerName |
    Add-AzureHDInsightConfigValues -Hive $hiveConfig |
    New-AzureHDInsightCluster -Name $clusterName -Location $location -Credential $hdiCredential

    
> [AZURE.NOTE] Clústeres de HDInsight basados en Linux tienen Tez habilitado de forma predeterminada.
    

## <a name="hive-partitioning"></a>Sección de partición

Operación de i/OS es importantes de rendimiento para ejecutar consultas de la sección. El rendimiento se puede mejorar si se puede reducir la cantidad de datos que debe leerse. De forma predeterminada, las consultas de la sección analizar tablas subárbol completas. Esta es una buena opción para consultas como tabla analiza, sin embargo, para las consultas que solo se necesitan para digitalizar una cantidad pequeña de datos (por ejemplo, consultas con filtrado), esto crea una sobrecarga innecesaria. Partición de subárbol permite consultas de la sección tener acceso a solo la cantidad necesaria de datos en tablas de la sección.

Partición subárbol implementan reorganizar los datos sin formato en nuevos directorios con cada partición tiene su propio directorio - donde está definida la partición por el usuario. El siguiente diagrama muestra una tabla subárbol de partición por la columna *año*. Se crea un nuevo directorio para cada año.

![partición][image-hdi-optimize-hive-partitioning_1]

Algunas consideraciones de particiones:

- **No incompleta partición** - partición en columnas con solo unos valores pueden provocar muy pocos particiones. Por ejemplo, partición de género se sólo crear dos particiones que debe crearse (macho y hembra), así sólo reducir la latencia en un máximo de mitad.

- **No dividir exceso** - en el otro extremo, creando una partición en una columna con un valor único (por ejemplo, ID de usuario) provocará varias particiones causando una gran cantidad de carga en el clúster de namenode como tendrán manejar el gran cantidad de directorios.

- **Inclinar no datos** - elegir sabiamente su clave de partición para que todas las particiones tienen tamaño par. Un ejemplo es partición en *estado* puede causar el número de registros en California ser casi 30 x de Vermont por la diferencia de la población.

Para crear una tabla de partición, utilice la cláusula *Dividido por* :

    CREATE TABLE lineitem_part
        (L_ORDERKEY INT, L_PARTKEY INT, L_SUPPKEY INT,L_LINENUMBER INT,
         L_QUANTITY DOUBLE, L_EXTENDEDPRICE DOUBLE, L_DISCOUNT DOUBLE,
         L_TAX DOUBLE, L_RETURNFLAG STRING, L_LINESTATUS STRING,
         L_SHIPDATE_PS STRING, L_COMMITDATE STRING, L_RECEIPTDATE        STRING, L_SHIPINSTRUCT STRING, L_SHIPMODE STRING,
         L_COMMENT STRING)
    PARTITIONED BY(L_SHIPDATE STRING)
    ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
    STORED AS TEXTFILE;

Una vez creada la tabla con particiones, puede crear partición estática o dinámica particiones.

- **Partición estática** significa que tiene datos ya sharded en los directorios adecuados y puede solicitar particiones subárbol manualmente según la ubicación del directorio. Esto se muestra en el fragmento de código siguiente.

        INSERT OVERWRITE TABLE lineitem_part
        PARTITION (L_SHIPDATE = ‘5/23/1996 12:00:00 AM’)
        SELECT * FROM lineitem 
        WHERE lineitem.L_SHIPDATE = ‘5/23/1996 12:00:00 AM’

        ALTER TABLE lineitem_part ADD PARTITION (L_SHIPDATE = ‘5/23/1996 12:00:00 AM’))
        LOCATION ‘wasbs://sampledata@ignitedemo.blob.core.windows.net/partitions/5_23_1996/'

- **Partición dinámica** significa que desea subárbol crear particiones automáticamente por usted. Dado que ya hemos creado la tabla de partición de la tabla provisional, todo lo que necesitamos es insertar datos en la tabla con particiones tal como se muestra a continuación:

        SET hive.exec.dynamic.partition = true;
        SET hive.exec.dynamic.partition.mode = nonstrict;
        INSERT INTO TABLE lineitem_part
        PARTITION (L_SHIPDATE)
        SELECT L_ORDERKEY as L_ORDERKEY, L_PARTKEY as L_PARTKEY , 
             L_SUPPKEY as L_SUPPKEY, L_LINENUMBER as L_LINENUMBER,
             L_QUANTITY as L_QUANTITY, L_EXTENDEDPRICE as L_EXTENDEDPRICE,
             L_DISCOUNT as L_DISCOUNT, L_TAX as L_TAX, L_RETURNFLAG as       L_RETURNFLAG, L_LINESTATUS as L_LINESTATUS, L_SHIPDATE as       L_SHIPDATE_PS, L_COMMITDATE as L_COMMITDATE, L_RECEIPTDATE as   L_RECEIPTDATE, L_SHIPINSTRUCT as L_SHIPINSTRUCT, L_SHIPMODE as      L_SHIPMODE, L_COMMENT as L_COMMENT, L_SHIPDATE as L_SHIPDATE FROM lineitem;

Para obtener más detalles, consulte [Dividir tablas](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+DDL#LanguageManualDDL-PartitionedTables).

##<a name="use-the-orcfile-format"></a>Use el formato ORCFile

Sección es compatible con diferentes formatos de archivo. Por ejemplo:

- **Texto**: este es el formato de archivo predeterminado y funciona con la mayoría de los escenarios
- **Avro**: funciona bien para escenarios de interoperabilidad
- **ORC/parqués**: adapta mejor rendimiento

Formato ORC (optimizado fila en columnas) es una manera muy eficaz para almacenar los datos de la sección. Comparado con otros formatos, ORC tiene las siguientes ventajas:

- compatibilidad con tipos complejos como DateTime y tipos complejos estructurados y semiestructurados
- hasta 70% de compresión
- índices cada 10.000 filas que permiten omitir filas
- una caída significativa en tiempo de ejecución

Para habilitar el formato ORC, cree primero una tabla con la cláusula *almacenados como ORC*:

    CREATE TABLE lineitem_orc_part
        (L_ORDERKEY INT, L_PARTKEY INT,L_SUPPKEY INT, L_LINENUMBER INT,
         L_QUANTITY DOUBLE, L_EXTENDEDPRICE DOUBLE, L_DISCOUNT DOUBLE,
         L_TAX DOUBLE, L_RETURNFLAG STRING, L_LINESTATUS STRING,
         L_SHIPDATE_PS STRING, L_COMMITDATE STRING, L_RECEIPTDATE STRING,
         L_SHIPINSTRUCT STRING, L_SHIPMODE STRING, L_COMMENT     STRING)
    PARTITIONED BY(L_SHIPDATE STRING)
    STORED AS ORC;

A continuación, insertar datos a la tabla ORC desde la tabla provisional. Por ejemplo:

    INSERT INTO TABLE lineitem_orc
    SELECT L_ORDERKEY as L_ORDERKEY, 
           L_PARTKEY as L_PARTKEY , 
           L_SUPPKEY as L_SUPPKEY,
           L_LINENUMBER as L_LINENUMBER,
           L_QUANTITY as L_QUANTITY, 
           L_EXTENDEDPRICE as L_EXTENDEDPRICE,
           L_DISCOUNT as L_DISCOUNT,
           L_TAX as L_TAX,
           L_RETURNFLAG as L_RETURNFLAG,
           L_LINESTATUS as L_LINESTATUS,
           L_SHIPDATE as L_SHIPDATE,
           L_COMMITDATE as L_COMMITDATE,
           L_RECEIPTDATE as L_RECEIPTDATE, 
           L_SHIPINSTRUCT as L_SHIPINSTRUCT,
           L_SHIPMODE as L_SHIPMODE,
           L_COMMENT as L_COMMENT
    FROM lineitem;

Puede obtener más información sobre el formato ORC [aquí](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+ORC).

##<a name="vectorization"></a>Vectorización

Vectorización permite subárbol procesar un lote de filas de 1024 juntos en lugar de procesamiento de una fila a la vez. Esto significa que se realizan operaciones simples con mayor rapidez porque necesita menos código interno para poder ejecutar.

Para habilitar vectorización prefijo la consulta de la sección con la siguiente configuración:

    set hive.vectorized.execution.enabled = true;

Para obtener más información, vea [Vectorized ejecución de la consulta](https://cwiki.apache.org/confluence/display/Hive/Vectorized+Query+Execution).


##<a name="other-optimization-methods"></a>Otros métodos de optimización

Hay más métodos de optimización puede tener en cuenta, por ejemplo:

- **Subárbol la creación de depósitos:** una técnica que permite a clúster o segmentar grandes conjuntos de datos para optimizar el rendimiento de la consulta.
- **Optimización de la combinación:** optimización de ejecución de consultas de la sección Planificación para mejorar la eficacia de las combinaciones y reducir la necesidad de sugerencias de usuario. Para obtener más información, consulte [optimización de la combinación](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+JoinOptimization#LanguageManualJoinOptimization-JoinOptimization).
- **aumentar reductores**

##<a id="nextsteps"></a>Pasos siguientes
En este artículo, ha aprendido varios métodos comunes de optimización de consulta de sección. Para obtener más información, consulte los artículos siguientes:

- [Usar Apache subárbol en HDInsight](hdinsight-use-hive.md)
- [Analizar datos de vuelo retraso usando subárbol en HDInsight](hdinsight-analyze-flight-delay-data.md)
- [Analizar datos de Twitter mediante subárbol en HDInsight](hdinsight-analyze-twitter-data.md)
- [Analizar datos de sensor con la consola de consulta subárbol en Hadoop en HDInsight](hdinsight-hive-analyze-sensor-data.md)
- [Usar subárbol con HDInsight para analizar los registros de los sitios Web](hdinsight-hive-analyze-website-log.md)


[image-hdi-optimize-hive-scaleout_1]: ./media/hdinsight-hadoop-optimize-hive-query/scaleout_1.png
[image-hdi-optimize-hive-scaleout_2]: ./media/hdinsight-hadoop-optimize-hive-query/scaleout_2.png
[image-hdi-optimize-hive-tez_1]: ./media/hdinsight-hadoop-optimize-hive-query/tez_1.png
[image-hdi-optimize-hive-partitioning_1]: ./media/hdinsight-hadoop-optimize-hive-query/partitioning_1.png
