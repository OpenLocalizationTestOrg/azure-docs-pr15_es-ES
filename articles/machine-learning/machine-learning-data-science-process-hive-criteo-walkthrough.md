<properties
    pageTitle="El proceso de ciencias de datos de grupo de acción: usar HDInsight Hadoop clústeres en el conjunto de datos de 1 TB Criteo | Microsoft Azure"
    description="Mediante el proceso de ciencias de datos de grupo para un escenario de llevar a cabo empleando un clúster de HDInsight Hadoop Compile e implemente un modelo con un conjunto de datos esté disponible públicamente (1 TB) grande"
    services="machine-learning,hdinsight"
    documentationCenter=""
    authors="bradsev"
    manager="jhubbard"
    editor="cgronlun" />

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/13/2016"
    ms.author="bradsev" />

# <a name="the-team-data-science-process-in-action---using-azure-hdinsight-hadoop-clusters-on-a-1-tb-dataset"></a>El proceso de ciencias de datos de grupo de acción: usar Azure HDInsight Hadoop clústeres en un conjunto de datos de 1 TB

En este tutorial, se muestran mediante el proceso de ciencias de datos de grupo en un escenario de llevar a cabo con un [clúster de Azure HDInsight Hadoop](https://azure.microsoft.com/services/hdinsight/) para almacenar, explorar, ingeniería de la característica y datos de ejemplo de uno de los conjuntos de datos de [Criteo](http://labs.criteo.com/downloads/download-terabyte-click-logs/) disponible públicamente de abajo. Usamos Azure el aprendizaje para crear un modelo de clasificación binario en estos datos. También le mostraremos cómo publicar uno de estos modelos como un servicio Web.

También es posible usar un bloc de notas de IPython para llevar a cabo las tareas que se presentan en este tutorial. Los usuarios que le gustaría probar este enfoque deberían consultar el tema del [tutorial Criteo mediante una conexión ODBC subárbol](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/iPythonNotebooks/machine-Learning-data-science-process-hive-walkthrough-criteo.ipynb) .


## <a name="dataset"></a>Descripción del conjunto de datos Criteo

La Criteo datos están un conjunto de datos de la predicción de haga clic en es de aproximadamente 370GB de gzip comprimido TSV archivos (~1.3TB sin comprimir), que incluyen más de 4,3 millones de registros. Se obtiene de 24 días de haga clic en datos disponibles [Criteo](http://labs.criteo.com/downloads/download-terabyte-click-logs/). Para la comodidad de científicos de datos, nos hemos descomprimir disponibles para experimentar con datos.

Cada registro de este conjunto de datos contiene 40 columnas:

- la primera columna es una columna de la etiqueta que indica si un usuario hace clic en un **Agregar** (valor 1) o no haga clic en uno (valor 0)
- a continuación 13 columnas son numéricas, y
- última 26 son columnas por categorías

Las columnas se anonymized y utilizar una serie de nombres enumerados: "Col1" (para la columna etiqueta) a ' Col40 "(para la última columna por categorías).            

Este es un extracto de las columnas primero 20 de dos observaciones (filas) de este conjunto de datos:

    Col1    Col2    Col3    Col4    Col5    Col6    Col7    Col8    Col9    Col10   Col11   Col12   Col13   Col14   Col15           Col16           Col17           Col18           Col19       Col20

    0       40      42      2       54      3       0       0       2       16      0       1       4448    4       1acfe1ee        1b2ff61f        2e8b2631        6faef306        c6fc10d3    6fcd6dcb           
    0               24              27      5               0       2       1               3       10064           9a8cb066        7a06385f        417e6103        2170fc56        acf676aa    6fcd6dcb                      

Hay valores que faltan en las columnas numéricas y por categorías en este conjunto de datos. Describimos un método sencillo para controlar los valores que faltan. Obtener más información de los datos se explora al almacenarlos en tablas de la sección.

**Definición:** *Clickthrough tasa (CTR):* Este es el porcentaje de clics en los datos. En este conjunto de datos de Criteo la CTR es aproximadamente 3.3% o 0.033.

## <a name="mltasks"></a>Ejemplos de tareas de predicción
En este tutorial se tratan dos problemas de predicción de ejemplo:

1. **Clasificación binario**: predicciones si un usuario hace clic en Agregar:
    - Clase 0: No hay ningún clic
    - Clase 1: haga clic en

2. **Regresión**: prevé la probabilidad de un clic de ad de algunas características del usuario.


## <a name="setup"></a>Establecer un HDInsight Hadoop clúster de ciencias de datos

**Nota:** Suele ser un **Administrador** de tareas.

Configurar el entorno de ciencias de datos de Azure para crear soluciones de análisis predictiva con HDInsight clústeres en tres pasos:

1. [Crear una cuenta de almacenamiento](../storage/storage-create-storage-account.md): esta cuenta de almacenamiento se usa para almacenar datos en el almacenamiento de blobs de Windows Azure. Aquí se almacenan los datos que se usan en clústeres HDInsight.

2. [Personalizar Azure HDInsight Hadoop clústeres para ciencias de datos](machine-learning-data-science-customize-hadoop-cluster.md): este paso, crea un clúster de Azure HDInsight Hadoop con 64 bits Anaconda Python 2.7 instalado en todos los nodos. Hay dos pasos importantes (descritas en este tema) para completar al personalizar el clúster HDInsight.

    * Debe establecer el vínculo de la cuenta de almacenamiento que creó en el paso 1 con el clúster de HDInsight cuando se crea. Esta cuenta de almacenamiento se usa para obtener acceso a los datos que se pueden procesar dentro del clúster.

    * Debe habilitar el acceso remoto al nodo principal del clúster después de crearlo. Recuerde las credenciales de acceso remoto que especifique aquí (diferentes a las especificadas para el clúster en su creación): debe realizar los procedimientos siguientes.

3. [Crear un área de trabajo de Azure m](machine-learning-create-workspace.md): aprendizaje del equipo de Azure esta área de trabajo se usa para crear modelos de aprendizaje de equipo después de una exploración de datos iniciales y hacia abajo de muestreo en el clúster HDInsight.

## <a name="getdata"></a>Obtener y consumir datos desde un origen público

El conjunto de datos de [Criteo](http://labs.criteo.com/downloads/download-terabyte-click-logs/) se puede acceder haciendo clic en el vínculo, acepte los términos de uso y proporcionar un nombre. A continuación se muestra una instantánea de este aspecto:

![Acepte los términos de Criteo](./media/machine-learning-data-science-process-hive-criteo-walkthrough/hLxfI2E.png)

Haga clic en **Continuar para descargar** para obtener más información sobre el conjunto de datos y su disponibilidad.

Los datos residen en una ubicación de [almacenamiento de blobs de Azure](../storage/storage-dotnet-how-to-use-blobs.md) pública: wasb://criteo@azuremlsampleexperiments.blob.core.windows.net/raw/. "wasb" hace referencia a la ubicación de almacenamiento de blobs de Windows Azure. 

1. Los datos de este almacenamiento de blobs público constan de tres subcarpetas de datos descomprimidos.

    1. La subcarpeta *sin formato o contar/* contiene los primeros 21 días de datos, desde el día\_00 al día\_20
    2. La subcarpeta *sin formato/tren/* consta de un solo día de datos, día\_21
    3. La subcarpeta *sin formato o probar/* consta de dos días de datos, día\_22 y día\_23

2. Para aquellos que quiere empezar con los datos sin formato gzip, también están disponibles en la carpeta principal *sin formato /* como day_NN.gz, donde NN va de 00 a 23.

Un enfoque alternativo para tener acceso, explorar y modelo de datos que no requiere las descargas locales se explican más adelante en este tutorial al crear tablas de la sección.

## <a name="login"></a>Inicie sesión en el clúster de headnode

Para iniciar sesión la headnode del clúster, utilice el [portal de Azure](https://ms.portal.azure.com) para localizar el clúster. Haga clic en el icono de elefante de HDInsight a la izquierda y, a continuación, haga doble clic en el nombre del clúster. Vaya a la pestaña **configuración** , haga doble clic en el icono Conectar en la parte inferior de la página y escriba sus credenciales de acceso remoto cuando se le solicite. Se abrirá la headnode del clúster.

Aquí es el aspecto de un registro primera típico en headnode de clúster:

![Inicie sesión en el clúster](./media/machine-learning-data-science-process-hive-criteo-walkthrough/Yys9Vvm.png)


En la parte izquierda, vemos "Hadoop línea de comandos", que es nuestro potente para la exploración de datos. También vemos dos direcciones URL útiles: "Hadoop hilo estado" y "Nodo de nombre de Hadoop". La dirección URL del hilo estado muestra el progreso de la tarea y el nombre de la URL de nodo ofrece detalles sobre la configuración del clúster.

Ahora está configurados y listo para comenzar la primera parte del tutorial: exploración de datos usando subárbol y preparación de datos de Azure el aprendizaje.

## <a name="hive-db-tables"></a>Crear tablas y base de datos de sección

Para crear tablas de subárbol para dataset Criteo, abra la ***línea de comandos de Hadoop*** en el escritorio del nodo principal y escriba el directorio de subárbol escribiendo el comando

    cd %hive_home%\bin

>[AZURE.NOTE] Ejecutar todos los comandos de la sección de este tutorial desde la ubicación de la sección o indicador del directorio. Esto se ocupa de cualquier problema de ruta de acceso automáticamente. Se utilizan los términos "Aviso de directorio subárbol", "sección Papelera / indicador del directorio" y "línea de comandos de Hadoop" indistintamente.

>[AZURE.NOTE]  Para ejecutar cualquier consulta subárbol, siempre uno puede usar los comandos siguientes:

        cd %hive_home%\bin
        hive

Una vez que aparezca la respuesta de la sección con una "sección >"iniciar sesión, simplemente cortar y pegar la consulta para ejecutarlo.

El código siguiente crea una base de datos "criteo" y, a continuación, genera 4 tablas:


* una *tabla para generar recuentos* creada día días\_00 al día\_20,
* una *tabla que desea usar como el conjunto de datos de tren* creada día\_21, y
* dos *tablas para usan como los conjuntos de datos de prueba* integrada en día\_22 y día\_23 respectivamente.

Nuestro conjunto de datos de prueba se divide en dos tablas diferentes porque uno de los días es un día festivo y queremos determinar si el modelo puede detectar diferencias entre un día festivo y no de vacaciones de la tasa de clickthrough.

La secuencia de comandos [ejemplo & #95; subárbol & #95; crear & #95; criteo & #95; #95; & base de datos y & #95;tables.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_create_criteo_database_and_tables.hql) se muestra aquí para mayor comodidad:

    CREATE DATABASE IF NOT EXISTS criteo;
    DROP TABLE IF EXISTS criteo.criteo_count;
    CREATE TABLE criteo.criteo_count (
    col1 string,col2 double,col3 double,col4 double,col5 double,col6 double,col7 double,col8 double,col9 double,col10 double,col11 double,col12 double,col13 double,col14 double,col15 string,col16 string,col17 string,col18 string,col19 string,col20 string,col21 string,col22 string,col23 string,col24 string,col25 string,col26 string,col27 string,col28 string,col29 string,col30 string,col31 string,col32 string,col33 string,col34 string,col35 string,col36 string,col37 string,col38 string,col39 string,col40 string)
    ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
    LINES TERMINATED BY '\n'
    STORED AS TEXTFILE LOCATION 'wasb://criteo@azuremlsampleexperiments.blob.core.windows.net/raw/count';

    DROP TABLE IF EXISTS criteo.criteo_train;
    CREATE TABLE criteo.criteo_train (
    col1 string,col2 double,col3 double,col4 double,col5 double,col6 double,col7 double,col8 double,col9 double,col10 double,col11 double,col12 double,col13 double,col14 double,col15 string,col16 string,col17 string,col18 string,col19 string,col20 string,col21 string,col22 string,col23 string,col24 string,col25 string,col26 string,col27 string,col28 string,col29 string,col30 string,col31 string,col32 string,col33 string,col34 string,col35 string,col36 string,col37 string,col38 string,col39 string,col40 string)
    ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
    LINES TERMINATED BY '\n'
    STORED AS TEXTFILE LOCATION 'wasb://criteo@azuremlsampleexperiments.blob.core.windows.net/raw/train';

    DROP TABLE IF EXISTS criteo.criteo_test_day_22;
    CREATE TABLE criteo.criteo_test_day_22 (
    col1 string,col2 double,col3 double,col4 double,col5 double,col6 double,col7 double,col8 double,col9 double,col10 double,col11 double,col12 double,col13 double,col14 double,col15 string,col16 string,col17 string,col18 string,col19 string,col20 string,col21 string,col22 string,col23 string,col24 string,col25 string,col26 string,col27 string,col28 string,col29 string,col30 string,col31 string,col32 string,col33 string,col34 string,col35 string,col36 string,col37 string,col38 string,col39 string,col40 string)
    ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
    LINES TERMINATED BY '\n'
    STORED AS TEXTFILE LOCATION 'wasb://criteo@azuremlsampleexperiments.blob.core.windows.net/raw/test/day_22';

    DROP TABLE IF EXISTS criteo.criteo_test_day_23;
    CREATE TABLE criteo.criteo_test_day_23 (
    col1 string,col2 double,col3 double,col4 double,col5 double,col6 double,col7 double,col8 double,col9 double,col10 double,col11 double,col12 double,col13 double,col14 double,col15 string,col16 string,col17 string,col18 string,col19 string,col20 string,col21 string,col22 string,col23 string,col24 string,col25 string,col26 string,col27 string,col28 string,col29 string,col30 string,col31 string,col32 string,col33 string,col34 string,col35 string,col36 string,col37 string,col38 string,col39 string,col40 string)
    ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
    LINES TERMINATED BY '\n'
    STORED AS TEXTFILE LOCATION 'wasb://criteo@azuremlsampleexperiments.blob.core.windows.net/raw/test/day_23';

Se tenga en cuenta que todas estas tablas externas tal como se tiene que apuntar a ubicaciones de almacenamiento de blobs de Windows Azure (wasb).

**Hay dos formas de ejecutar la consulta de cualquier sección que se mencione ahora.**

1. **Uso de la línea de comandos de respuesta de subárbol**: el primero es emitir un comando "sección" y copiar y pegar una consulta en la línea de comandos de respuesta de sección. Para ello, realice:

        cd %hive_home%\bin
        hive

    Ahora en la línea de comandos de respuesta, cortar y pegar la consulta ejecuta.

2. **Guardar consultas a un archivo y ejecutar el comando**: es el segundo guardar las consultas a un archivo de .hql ([ejemplo & #95; subárbol & #95; crear & #95; criteo & #95; #95; & base de datos y & #95;tables.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_create_criteo_database_and_tables.hql)) y, a continuación, ejecute el siguiente comando para ejecutar la consulta:

        hive -f C:\temp\sample_hive_create_criteo_database_and_tables.hql


### <a name="confirm-database-and-table-creation"></a>Confirme la creación de base de datos y tabla

A continuación, hemos confirmar la creación de la base de datos con el siguiente comando de la Papelera subárbol / indicador del directorio:

        hive -e "show databases;"

Esto le da:

        criteo
        default
        Time taken: 1.25 seconds, Fetched: 2 row(s)

Esto confirma la creación de la nueva base de datos, "criteo".

Para ver las tablas que se creó, nos simplemente emitir el comando aquí desde la ubicación de la sección o indicador del directorio:

        hive -e "show tables in criteo;"

A continuación, se mostrará el resultado siguiente:

        criteo_count
        criteo_test_day_22
        criteo_test_day_23
        criteo_train
        Time taken: 1.437 seconds, Fetched: 4 row(s)

##<a name="exploration"></a>Exploración de datos en la sección

Ahora está listos para realizar alguna exploración de datos básico de sección. En primer lugar contando el número de ejemplos en el tren y probar las tablas de datos.

### <a name="number-of-train-examples"></a>Número de ejemplos de tren

Aquí se muestra el contenido de [ejemplo & #95; subárbol & #95; count & #95; tren & #95; tabla & #95;examples.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_count_train_table_examples.hql) :

        SELECT COUNT(*) FROM criteo.criteo_train;

Da como resultado:

        192215183
        Time taken: 264.154 seconds, Fetched: 1 row(s)

Como alternativa, una posible emitir el comando siguiente desde la ubicación de la sección o indicador del directorio:

        hive -f C:\temp\sample_hive_count_criteo_train_table_examples.hql

### <a name="number-of-test-examples-in-the-two-test-datasets"></a>Número de ejemplos de prueba de los dos conjuntos de datos de prueba

Ahora, nos contar el número de ejemplos en los dos conjuntos de datos de prueba. El contenido de [ejemplo & #95 subárbol & #95 count & #95; criteo & #95; prueba & #95; día & #95; 22 & #95; tabla & #95;examples.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_count_criteo_test_day_22_table_examples.hql) aquí:

        SELECT COUNT(*) FROM criteo.criteo_test_day_22;

Da como resultado:

        189747893
        Time taken: 267.968 seconds, Fetched: 1 row(s)

Como de costumbre, podemos llamarlo también la secuencia de comandos de la Papelera subárbol / directory preguntar a emitir el comando:

        hive -f C:\temp\sample_hive_count_criteo_test_day_22_table_examples.hql

Por último, se examine el número de ejemplos de prueba en el conjunto de datos de prueba basados en día\_23.

El comando para realizar esta acción es similar a la que se acaba de mostrar (consulte [muestra & #95; subárbol & #95; count & #95; criteo & #95; prueba & #95; día & #95; 23 & #95;examples.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_count_criteo_test_day_23_examples.hql)):

        SELECT COUNT(*) FROM criteo.criteo_test_day_23;

Esto le da:

        178274637
        Time taken: 253.089 seconds, Fetched: 1 row(s)

### <a name="label-distribution-in-the-train-dataset"></a>Distribución de etiqueta en el conjunto de datos de tren

La distribución de etiqueta en el conjunto de datos de tren es de interés. Para ver, se muestra el contenido de [ejemplo & #95; subárbol & 95 #; criteo & #95; etiqueta & #95; distribución & #95; tren & #95;table.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_label_distribution_train_table.hql):

        SELECT Col1, COUNT(*) AS CT FROM criteo.criteo_train GROUP BY Col1;

Esto da como resultado de la distribución de etiqueta:

        1       6292903
        0       185922280
        Time taken: 459.435 seconds, Fetched: 2 row(s)

Tenga en cuenta que el porcentaje de etiquetas positivos 3.3% (coherente con el conjunto de datos original).

### <a name="histogram-distributions-of-some-numeric-variables-in-the-train-dataset"></a>Distribuciones de histograma de algunas variables numéricos en el conjunto de datos de tren

Podemos utilizar nativo de la sección "histograma\_numérico" función para averiguar qué aspecto tiene la distribución de las variables numéricas. Este es el contenido de [ejemplo & #95; subárbol & #95; criteo & #95; histograma & #95;numeric.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_histogram_numeric.hql):

        SELECT CAST(hist.x as int) as bin_center, CAST(hist.y as bigint) as bin_height FROM
            (SELECT
            histogram_numeric(col2, 20) as col2_hist
            FROM
            criteo.criteo_train
            ) a
            LATERAL VIEW explode(col2_hist) exploded_table as hist;

Esto es el siguiente:

        26      155878415
        2606    92753
        6755    22086
        11202   6922
        14432   4163
        17815   2488
        21072   1901
        24113   1283
        27429   1225
        30818   906
        34512   723
        38026   387
        41007   290
        43417   312
        45797   571
        49819   428
        53505   328
        56853   527
        61004   160
        65510   3446
        Time taken: 317.851 seconds, Fetched: 20 row(s)

La vista - LATERAL de explosión combinación en subárbol sirve para generar un resultado similar a SQL en lugar de la lista habitual. Tenga en cuenta que esta tabla, la primera columna corresponde al centro de la Papelera y la segunda a la frecuencia de la Papelera.

### <a name="approximate-percentiles-of-some-numeric-variables-in-the-train-dataset"></a>Percentiles aproximados de algunas variables numéricos en el conjunto de datos de tren

Además de interés con variables numéricas es el cálculo de percentiles aproximadas. Sección de nativa "percentil\_aproximadamente" hace esto por nosotros. El contenido de [ejemplo & #95; subárbol & #95; criteo & #95; aproximada & #95;percentiles.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_approximate_percentiles.hql) son:

        SELECT MIN(Col2) AS Col2_min, PERCENTILE_APPROX(Col2, 0.1) AS Col2_01, PERCENTILE_APPROX(Col2, 0.3) AS Col2_03, PERCENTILE_APPROX(Col2, 0.5) AS Col2_median, PERCENTILE_APPROX(Col2, 0.8) AS Col2_08, MAX(Col2) AS Col2_max FROM criteo.criteo_train;

Da como resultado:

        1.0     2.1418600917169246      2.1418600917169246    6.21887086390288 27.53454893115633       65535.0
        Time taken: 564.953 seconds, Fetched: 1 row(s)

Nos Comente que la distribución de percentiles está estrechamente relacionado con la distribución de histograma de cualquier variable numérica normalmente.        

### <a name="find-number-of-unique-values-for-some-categorical-columns-in-the-train-dataset"></a>Buscar el número de valores únicos de algunas columnas por categorías en el conjunto de datos de tren

Continuar la exploración de datos, ahora encontramos, para algunas columnas por categorías, el número de valores únicos que realice. Para ello, se muestra el contenido de [ejemplo & #95; subárbol & #95; criteo & #95; únicos & #95; valores & #95;categoricals.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_unique_values_categoricals.hql):

        SELECT COUNT(DISTINCT(Col15)) AS num_uniques FROM criteo.criteo_train;

Da como resultado:

        19011825
        Time taken: 448.116 seconds, Fetched: 1 row(s)

Se tenga en cuenta que Col15 19 millones de valores únicos. Usar técnicas sencilla como "caliente una codificación" codificar variables por categorías alta multidimensionales es factible. En particular, se explican y muestra una técnica eficaz, sólida denominada [Aprendizaje con la cuenta](http://blogs.technet.com/b/machinelearning/archive/2015/02/17/big-learning-made-easy-with-counts.aspx) para abordar este problema de forma eficaz.

Nos terminar esta subsección consultando el número de valores únicos para algunas categorías también otras columnas. El contenido de [ejemplo & #95; subárbol & #95; criteo & #95; únicos & #95; valores & #95; múltiplo & #95;categoricals.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_unique_values_multiple_categoricals.hql) son:

        SELECT COUNT(DISTINCT(Col16)), COUNT(DISTINCT(Col17)),
        COUNT(DISTINCT(Col18), COUNT(DISTINCT(Col19), COUNT(DISTINCT(Col20))
        FROM criteo.criteo_train;

Da como resultado:

        30935   15200   7349    20067   3
        Time taken: 1933.883 seconds, Fetched: 1 row(s)

Nuevo vemos que, excepto Col20, todas las demás columnas tienen muchos valores únicos.

### <a name="co-occurence-counts-of-pairs-of-categorical-variables-in-the-train-dataset"></a>Repetición de la cuenta de pares de variables por categorías en el conjunto de datos de tren

Los recuentos de la aparición de pares de variables por categorías también es de interés. Esto se puede determinar mediante el código de [ejemplo & #95; subárbol & #95; criteo & #95; pareja & #95; por categorías & #95;counts.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_paired_categorical_counts.hql):

        SELECT Col15, Col16, COUNT(*) AS paired_count FROM criteo.criteo_train GROUP BY Col15, Col16 ORDER BY paired_count DESC LIMIT 15;

Estamos inverso los recuentos de pedidos por su aparición y buscar en la parte superior de 15 en este caso. Esto nos ofrece:

        ad98e872        cea68cd3        8964458
        ad98e872        3dbb483e        8444762
        ad98e872        43ced263        3082503
        ad98e872        420acc05        2694489
        ad98e872        ac4c5591        2559535
        ad98e872        fb1e95da        2227216
        ad98e872        8af1edc8        1794955
        ad98e872        e56937ee        1643550
        ad98e872        d1fade1c        1348719
        ad98e872        977b4431        1115528
        e5f3fd8d        a15d1051        959252
        ad98e872        dd86c04a        872975
        349b3fec        a52ef97d        821062
        e5f3fd8d        a0aaffa6        792250
        265366bf        6f5c7c41        782142
        Time taken: 560.22 seconds, Fetched: 15 row(s)

## <a name="downsample"></a>Los conjuntos de datos de ejemplo de abajo para el aprendizaje del equipo de Azure

Tener explorado los conjuntos de datos y muestra cómo podemos hacer a este tipo de exploración para las variables (incluyendo combinaciones), que ahora abajo muestra los conjuntos de datos para que podemos crear modelos en Azure el aprendizaje. Recuerde que es el problema que nos centraremos en: dado un conjunto de atributos de ejemplo (valores de la característica de Col2 - Col40), nos predecir si Col1 es 0 (no hay ningún clic) o 1 (haga clic).

Para nuestra tren de ejemplo y probar los conjuntos de datos 1% del tamaño original, usamos una función RAND nativa de sección. La siguiente secuencia de comandos [muestra & #95; subárbol & #95; criteo & #95; reducir tamaño & #95; tren & #95;dataset.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_downsample_train_dataset.hql) hace esto para el conjunto de datos del tren:

        CREATE TABLE criteo.criteo_train_downsample_1perc (
        col1 string,col2 double,col3 double,col4 double,col5 double,col6 double,col7 double,col8 double,col9 double,col10 double,col11 double,col12 double,col13 double,col14 double,col15 string,col16 string,col17 string,col18 string,col19 string,col20 string,col21 string,col22 string,col23 string,col24 string,col25 string,col26 string,col27 string,col28 string,col29 string,col30 string,col31 string,col32 string,col33 string,col34 string,col35 string,col36 string,col37 string,col38 string,col39 string,col40 string)
        ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
        LINES TERMINATED BY '\n'
        STORED AS TEXTFILE;

        ---Now downsample and store in this table

        INSERT OVERWRITE TABLE criteo.criteo_train_downsample_1perc SELECT * FROM criteo.criteo_train WHERE RAND() <= 0.01;

Da como resultado:

        Time taken: 12.22 seconds
        Time taken: 298.98 seconds

La secuencia de comandos [muestra & #95; subárbol & #95; criteo & #95; reducir tamaño & #95; prueba & #95; día & #95; 22 & #95;dataset.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_downsample_test_day_22_dataset.hql) lo hace para los datos de prueba, día\_22:

        --- Now for test data (day_22)

        CREATE TABLE criteo.criteo_test_day_22_downsample_1perc (
        col1 string,col2 double,col3 double,col4 double,col5 double,col6 double,col7 double,col8 double,col9 double,col10 double,col11 double,col12 double,col13 double,col14 double,col15 string,col16 string,col17 string,col18 string,col19 string,col20 string,col21 string,col22 string,col23 string,col24 string,col25 string,col26 string,col27 string,col28 string,col29 string,col30 string,col31 string,col32 string,col33 string,col34 string,col35 string,col36 string,col37 string,col38 string,col39 string,col40 string)
        ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
        LINES TERMINATED BY '\n'
        STORED AS TEXTFILE;

        INSERT OVERWRITE TABLE criteo.criteo_test_day_22_downsample_1perc SELECT * FROM criteo.criteo_test_day_22 WHERE RAND() <= 0.01;

Da como resultado:

        Time taken: 1.22 seconds
        Time taken: 317.66 seconds


Por último, la secuencia de comandos [muestra & #95; subárbol & #95; criteo & #95; reducir tamaño & #95; prueba & #95; día & #95; 23 & #95;dataset.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_downsample_test_day_23_dataset.hql) lo hace para los datos de prueba, día\_23:

        --- Finally test data day_23
        CREATE TABLE criteo.criteo_test_day_23_downsample_1perc (
        col1 string,col2 double,col3 double,col4 double,col5 double,col6 double,col7 double,col8 double,col9 double,col10 double,col11 double,col12 double,col13 double,col14 double,col15 string,col16 string,col17 string,col18 string,col19 string,col20 string,col21 string,col22 string,col23 string,col24 string,col25 string,col26 string,col27 string,col28 string,col29 string,col30 string,col31 string,col32 string,col33 string,col34 string,col35 string,col36 string,col37 string,col38 string,col39 string,col40 srical feature; tring)
        ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
        LINES TERMINATED BY '\n'
        STORED AS TEXTFILE;

        INSERT OVERWRITE TABLE criteo.criteo_test_day_23_downsample_1perc SELECT * FROM criteo.criteo_test_day_23 WHERE RAND() <= 0.01;

Da como resultado:

        Time taken: 1.86 seconds
        Time taken: 300.02 seconds

Con esto, estamos listos para usar nuestro tren muestra abajo y conjuntos de datos para crear modelos en Azure el aprendizaje de prueba.

Hay un componente importante final antes de seguir adelante a Azure máquina aprendizaje, que se encuentra problemas de la tabla de recuento. En la siguiente sección subsitios, trataremos esto en detalle.

##<a name="count"></a>Una breve explicación de la tabla de recuento

Como hemos visto, varias variables por categorías tienen una dimensión muy alta. En nuestro tutorial, presentamos una técnica eficaz llamada [Aprendizaje con la cuenta](http://blogs.technet.com/b/machinelearning/archive/2015/02/17/big-learning-made-easy-with-counts.aspx) para codificar estas variables de manera eficiente sólida. Para obtener más información sobre esta técnica está en el vínculo proporcionado.

**Nota:** En este tutorial, se centra en el uso recuento tablas para generar representaciones compacta de alta dimensionales características por categorías. Esto no es la única manera de codificar características por categorías; Para obtener más información sobre otras técnicas, pueden comprobar los usuarios interesados [uno caliente-codificación](http://en.wikipedia.org/wiki/One-hot) y [función hash](http://en.wikipedia.org/wiki/Feature_hashing).

Para crear tablas de recuento en los datos de recuento, usamos los datos en la carpeta sin formato o recuento. En la sección de modelado, le mostraremos los usuarios cómo crear estas tablas contar para características por categorías desde cero, o bien usar una tabla de recuento predefinida para sus exploraciones. En lo que se indica a continuación, cuando nos referimos a "recuento predefinida tablas", nos referimos con las tablas de recuento que proporcionamos. En la siguiente sección, se proporcionan instrucciones detalladas sobre cómo obtener acceso a estas tablas.

## <a name="aml"></a>Cree un modelo de aprendizaje del equipo de Azure

Nuestro modelo de proceso de Azure el aprendizaje de creación sigue estos pasos:

1. [Obtener los datos de tablas de subárbol en aprendizaje de máquina de Azure](#step1)
2. [Crear el ensayo: limpiar los datos, elija un estudiante y función con tablas de recuento](#step2)
3. [Entrenar el modelo](#step3)
4. [Puntuación del modelo de datos de prueba](#step4)
5. [Evaluar el modelo](#step5)
6. [Publicar el modelo como un servicio web para su consumo](#step6)

Ahora se está listos para crear modelos en studio aprendizaje del equipo de Azure. Nuestros datos muestras abajo se guardan como tablas de sección en el clúster. Usamos el módulo de aprendizaje de máquina Azure **Importar datos** a leer estos datos. Las credenciales para acceder a la cuenta de almacenamiento de este clúster se proporcionan en lo que sigue.

### <a name="step1"></a>Paso 1: Obtener datos de tablas de la sección en el equipo de Azure aprendizaje uso del módulo de importar datos y seleccione un ensayo de aprendizaje del equipo

Comience seleccionando un **+ nuevo** -> **ensayo** -> **Ensayo en blanco**. A continuación, en el cuadro de **búsqueda** en la parte superior izquierda, busque "Importar datos". Arrastrar y colocar el módulo de **Importar datos** en el lienzo de ensayo (la parte central de la pantalla) para usar el módulo de acceso a datos.

Este es el aspecto de los **Datos de importación** al obtener datos de la tabla subárbol:

![Importar datos obtiene datos](./media/machine-learning-data-science-process-hive-criteo-walkthrough/i3zRaoj.png)

Para el módulo de **Importar datos** , los valores de los parámetros que se proporcionan en el gráfico son sólo algunos ejemplos de la ordenación de los valores que debe proporcionar. Aquí tiene algunas instrucciones generales sobre cómo rellenar el parámetro establecido para el módulo de **Importar datos** .

1. Elija "Sección consulta" para el **Origen de datos**
2. En el cuadro de **sección consulta de base de datos** , un simple SELECT * FROM < su\_base de datos\_name.your\_tabla\_nombre >-es suficiente.
3. **URI del servidor Hcatalog**: si el clúster es "abc", esto es simplemente: https://abc.azurehdinsight.net
4. **Nombre de cuenta de usuario de Hadoop**: el nombre del usuario seleccionado en el momento de la puesta en servicio el clúster. (No el nombre de usuario de acceso remoto!)
5. **Contraseña de cuenta de usuario de Hadoop**: la contraseña para el nombre de usuario seleccionado en el momento de la puesta en servicio el clúster. (No en la contraseña de acceso remoto!)
6. **Ubicación de datos de salida**: elija "Azure"
7. **Nombre de la cuenta de almacenamiento de Azure**: la cuenta de almacenamiento asociada con el clúster
8. **Clave de cuenta de almacenamiento de Azure**: la clave de la cuenta de almacenamiento asociado con el clúster.
9. **Nombre del contenedor de Azure**: si el nombre de clúster es "abc", esto suele ser simplemente "abc,".


Una vez que los **Datos de importación** termine de obtención de datos (verá la marca de verificación verde en el módulo), guardar estos datos como un conjunto de datos (con un nombre de su elección). Este aspecto:

![Importar datos de almacenamiento de datos](./media/machine-learning-data-science-process-hive-criteo-walkthrough/oxM73Np.png)

Haga clic en el puerto de salida del módulo de **Importar datos** . Aparecerá una opción **Guardar como conjunto de datos** y una opción de **visualizar** . La opción **Ver** , si hace clic en, muestra 100 filas de los datos, junto con un panel derecho es útil para algunas estadísticas de resumen. Para guardar los datos, seleccione **Guardar como conjunto de datos** y siga las instrucciones.

Para seleccionar el conjunto de datos guardada para su uso en un ensayo de aprendizaje, busque los conjuntos de datos mediante el cuadro de **búsqueda** que se muestra en la siguiente ilustración. A continuación, simplemente escriba el nombre que le asignó el conjunto de datos parcialmente para tener acceso a él y arrastre el conjunto de datos hasta el panel principal. Coloca en el panel principal, selecciona para su uso en el modelado de aprendizaje del equipo.

![](./media/machine-learning-data-science-process-hive-criteo-walkthrough/cl5tpGw.png)

>[AZURE.NOTE] Hacer esto para el tren y los conjuntos de datos de prueba. Además, recuerde usar el nombre de la base de datos y los nombres de tabla que se proporcionó para ello. Los valores utilizados en la ilustración son únicamente para ilustración purposes.* *

### <a name="step2"></a>Paso 2: Crear un ensayo simple en Azure el aprendizaje predecir clics / sin clics

Nuestra ensayo Azure m es similar a esta:

![](./media/machine-learning-data-science-process-hive-criteo-walkthrough/xRpVfrY.png)

Ahora, examine los componentes clave de este ensayo. Como recordatorio, debemos arrastre nuestra tren guardado y pruebe primero los conjuntos de datos en nuestro lienzo de ensayo.

#### <a name="clean-missing-data"></a>Limpiar los datos que faltan

El módulo de **Limpiar datos falta** hace su nombre sugiere: limpia datos que faltan de maneras que pueden ser especificado por el usuario. Buscar en este módulo, vemos que esto:

![Limpias datos que faltan](./media/machine-learning-data-science-process-hive-criteo-walkthrough/0ycXod6.png)

En este caso, elegimos reemplazar todos los valores que faltan con 0. Hay otras opciones, que pueden verse consultando las listas desplegables en el módulo.

#### <a name="feature-engineering-on-the-data"></a>Característica de ingeniería en los datos

Puede haber millones de valores únicos para algunas características por categorías de grandes conjuntos de datos. Con métodos sencilla como caliente una codificación para representar dichas funciones por categorías alta multidimensionales es factible por completo. En este tutorial, se muestra cómo usar características de recuento mediante módulos de aprendizaje del equipo de Azure integrados para generar compacta representaciones de estas variables por categorías alta dimensionales. El resultado final es un pequeño modelo, tiempos de formación y mediciones de rendimiento que son bastante comparable a usar otras técnicas.

##### <a name="building-counting-transforms"></a>Recuento de las transformaciones de creación

Para compilar características de contar, usamos el módulo **Generar contando transformar** que están disponible en aprendizaje de máquina de Azure. El módulo es similar a esta:


![](./media/machine-learning-data-science-process-hive-criteo-walkthrough/e0eqKtZ.png)
![](./media/machine-learning-data-science-process-hive-criteo-walkthrough/OdDN0vw.png)


**Nota importante** : en el cuadro **Contar columnas** , se escriba aquellas columnas que se desean realizar recuentos en. Normalmente, estos son (como se mencionó) alta dimensionales columnas por categorías. Al principio, hemos mencionado que el conjunto de datos de Criteo tiene 26 columnas por categorías: de Col15 a Col40. En este caso, contamos en todos ellos y asigne a sus índices (entre 15 y 40 separados por comas, como se muestra).

Para utilizar el módulo en el modo de MapReduce (adecuado para grandes conjuntos de datos), se necesita acceso a un clúster de HDInsight Hadoop (usado para obtener información detallada característica puedan reutilizarse para ello también) y sus credenciales. Las figuras anterior muestran qué el rellenado en valores aspecto (reemplace los valores proporcionados para ilustración con los que son relevantes para su propio caso de uso).

![](./media/machine-learning-data-science-process-hive-criteo-walkthrough/05IqySf.png)

En la figura anterior, le mostraremos cómo especificar la ubicación de blobs de entrada. Esta ubicación tiene los datos reservados para crear tablas de recuento.


Cuando finalice este módulo, podemos guardar la transformación para más tarde haciendo clic en el módulo y seleccionando la opción **Guardar como transformación** :

![](./media/machine-learning-data-science-process-hive-criteo-walkthrough/IcVgvHR.png)

En nuestra arquitectura de ensayo mostrado arriba, el conjunto de datos "ytransform2" se corresponde con mayor precisión en una transformación recuento guardado. Para el resto de este ensayo, se supone que el lector utiliza un módulo **Generar contando transformar** en algunos datos para generar la cuenta y, a continuación, puede usar esos recuentos a generar características de recuento en el tren y probar los conjuntos de datos.

##### <a name="choosing-what-count-features-to-include-as-part-of-the-train-and-test-datasets"></a>Elegir qué recuento de características para incluir como parte de los conjuntos de datos de prueba y tren

Una vez que tenemos un recuento transformar listo, el usuario puede elegir qué características para incluir en su tren y probar conjuntos de datos mediante el módulo de **Modificar parámetros de tabla de recuento** . Le mostraremos solo este módulo aquí está completa, pero los intereses de simplificar no realmente usarlo en nuestra prueba.

![](./media/machine-learning-data-science-process-hive-criteo-walkthrough/PfCHkVg.png)

En este caso, como puede verse, nos hemos elegido usar solo las probabilidades registro y omitir el reverso desactivar columna. También podemos establecer parámetros como el umbral de Papelera basura, cuántos ejemplos ficticio anteriores para agregar alisado y si va a usar cualquier ruido Laplacian o no. Todas estas son características avanzadas y es a tener en cuenta que los valores predeterminados son un buen punto de partida para los usuarios que está familiarizado con este tipo de generación de la característica.

##### <a name="data-transformation-before-generating-the-count-features"></a>Transformación de datos antes de generar las características de recuento

Ahora que centrarse en un punto importante sobre transformar nuestro tren y contrastar los datos antes de generar realmente características de recuento. Tenga en cuenta que existen dos módulos de **Ejecutar Script de R** usados antes de que aplique la transformación de contar a los datos.

![](./media/machine-learning-data-science-process-hive-criteo-walkthrough/aF59wbc.png)

Aquí es el primer script R:

![](./media/machine-learning-data-science-process-hive-criteo-walkthrough/3hkIoMx.png)


En esta secuencia de comandos R, se cambia el nombre nuestras columnas a nombres "Col1" a "Col40". Esto es porque la transformación recuento espera nombres de este formato.

En el segundo script R, nos equilibrar la distribución entre clases positivas y negativas (clases 1 y 0, respectivamente) por la clase negativa de resolución. La secuencia de comandos de R aquí muestra cómo hacerlo:

![](./media/machine-learning-data-science-process-hive-criteo-walkthrough/91wvcwN.png)

En esta secuencia de comandos de R simple, usamos "OC\_neg\_relación" para establecer la cantidad de equilibrio entre lo positivo y las clases negativas. Esto es importante hacer puesto que mejorar descuadre clase normalmente tiene ventajas de rendimiento para los problemas de clasificación ¿dónde está la distribución de clase habían sesgada (Recuerde que en este caso, tenemos 3.3% positivo clases y 96,7% negativo).

##### <a name="applying-the-count-transformation-on-our-data"></a>Aplicar la transformación recuento en nuestros datos

Por último, podemos utilizar el módulo de **Aplicar transformación** para aplicar las transformaciones de recuento en nuestra tren y probar los conjuntos de datos. Este módulo toma la transformación recuento guardado como una entrada y los conjuntos de datos de prueba o tren como la otra entrada y devuelve los datos con las características de recuento. Se muestra aquí:

![](./media/machine-learning-data-science-process-hive-criteo-walkthrough/xnQvsYf.png)

##### <a name="an-excerpt-of-what-the-count-features-look-like"></a>Un extracto de las características de recuento de qué aspecto

Es instructivo para ver el aspecto de las características de recuento en nuestro caso. Aquí mostramos un extracto de esto:

![](./media/machine-learning-data-science-process-hive-criteo-walkthrough/FO1nNfw.png)

En este extracto mostramos que para las columnas que se enumeran en obtener los recuentos y registrar probabilidades además de cualquier backoffs relevante.

Estamos listos para crear un modelo de aprendizaje del equipo de Azure con estos conjuntos de datos transformados. En la siguiente sección, le mostraremos cómo se puede hacer esto.

#### <a name="azure-machine-learning-model-building"></a>Creación de modelo de aprendizaje de máquina Azure

##### <a name="choice-of-learner"></a>Elección de estudiante

En primer lugar, debemos elegir un estudiante. Vamos a usar un árbol de decisión de dos clase aumenta como nuestro estudiante. Estas son las opciones predeterminadas para este estudiante:

![](./media/machine-learning-data-science-process-hive-criteo-walkthrough/bH3ST2z.png)

Para nuestra ensayo, vamos a elegir los valores predeterminados. Se observe que los valores predeterminados son normalmente significativos y una buena forma de obtener previsiones rápidos en el rendimiento. Puede mejorar el rendimiento mediante el barrido parámetros si elige una vez que tenga una línea base.

#### <a name="train-the-model"></a>Entrenar el modelo

Para obtener formación, simplemente se invocar un módulo de **Modelo de tren** . Las dos entradas que son al estudiante de árbol de decisión de clase de dos aumenta y dataset tren. Se muestra aquí:

![](./media/machine-learning-data-science-process-hive-criteo-walkthrough/2bZDZTy.png)


#### <a name="score-the-model"></a>Puntuación del modelo

Una vez que tenemos un modelo de formación, estamos listos para la puntuación en el conjunto de datos de prueba y para evaluar su rendimiento. Para hacerlo con el módulo de **Modelo de puntuación** que se muestra en la siguiente ilustración, junto con un módulo de **Evaluar modelo** :

![](./media/machine-learning-data-science-process-hive-criteo-walkthrough/fydcv6u.png)


### <a name="step5"></a>Paso 5: Evaluar el modelo

Por último, vamos a analizar el rendimiento del modelo. Normalmente, dos de los problemas de clasificación (binario) de clase, una buena medida es la AUC. Para visualizar esto, nos conectar el módulo de **Modelo de calificación** a un módulo **Evaluar modelo** para esto. Haga clic en **Ver** en el módulo de **Evaluar modelo** da como resultado un gráfico como el siguiente:

![Evaluar el modelo de BDT módulo](./media/machine-learning-data-science-process-hive-criteo-walkthrough/0Tl0cdg.png)

En binario (o clase dos) problemas de clasificación, una buena medida de precisión de predicción es el área en curva (AUC). En lo que sigue, le mostraremos nuestros resultados con este modelo en nuestro conjunto de datos de prueba. Para obtener esto, haga clic en el puerto de salida del módulo de **Evaluar modelo** y, a continuación, **visualizar**.

![Visualizar el módulo de evaluar modelo](./media/machine-learning-data-science-process-hive-criteo-walkthrough/IRfc7fH.png)

### <a name="step6"></a>Paso 6: Publicar el modelo como un servicio Web
La capacidad de publicar un modelo de aprendizaje del equipo de Azure como servicios web con un mínimo de problemas es una característica valiosa para hacer que esté disponible. Una vez que haya terminado, cualquier persona puede realizar llamadas al servicio web con datos de entrada que necesitan predicciones para y el servicio web utiliza el modelo para devolver las predicciones.

Para ello, nos guardar nuestro modelo capacitado como un objeto de modelo capacitado. Esto se hace haciendo clic en el módulo de **Modelo de tren** y mediante la opción **Guardar como modelo capacitado** .

A continuación, es necesario crear la entrada y salida puertos nuestro servicio web:

* un puerto de entrada toma los datos en la misma forma que los datos que necesitamos predicciones para
* un puerto de salida devuelve las etiquetas de puntuación y las probabilidades asociadas.

#### <a name="select-a-few-rows-of-data-for-the-input-port"></a>Seleccionar algunas filas de datos para el puerto de entrada

Es conveniente usar un módulo de **Aplicar transformación de SQL** para seleccionar simplemente 10 filas a servir de los datos de puerto de entrada. Seleccione estas filas de datos para el puerto de entrada con la consulta SQL que se muestra aquí:

![Datos de puerto de entrada](./media/machine-learning-data-science-process-hive-criteo-walkthrough/XqVtSxu.png)

#### <a name="web-service"></a>Servicio Web
Ahora estamos listos ejecutar un ensayo pequeño que se pueden usar para publicar el servicio web.

#### <a name="generate-input-data-for-webservice"></a>Generar datos de entrada de webservice

Como un paso de cero, puesto que la tabla de recuento es grande, estamos tomar algunas líneas de datos de prueba y generar datos de salida del mismo con las características de recuento. Esto puede servir como el formato de datos de entrada para nuestro webservice. Se muestra aquí:

![Crear datos de entrada de BDT](./media/machine-learning-data-science-process-hive-criteo-walkthrough/OEJMmst.png)

>[AZURE.NOTE] Para el formato de datos de entrada, usamos ahora el resultado del módulo **Featurizer de recuento** . Una vez que finalice este ensayo, guardar los resultados del módulo de **Recuento Featurizer** como un conjunto de datos. Este conjunto de datos se utiliza para los datos de entrada en el servicio Web.

#### <a name="scoring-experiment-for-publishing-webservice"></a>La puntuación de ensayo para la publicación webservice

En primer lugar, le mostraremos este aspecto. La estructura esencial es un módulo de **Modelo de puntuación** que acepte nuestro objeto modelo capacitado y algunas líneas de datos de entrada que hemos generado en los pasos anteriores, utilizando el módulo **Featurizer de recuento** . Usamos "Seleccionar columnas de conjunto de datos" para proyectar las etiquetas Scored y las probabilidades de puntuación.

![Seleccione las columnas del conjunto de datos](./media/machine-learning-data-science-process-hive-criteo-walkthrough/kRHrIbe.png)

Observe cómo el módulo de **Seleccionar columnas de conjunto de datos** puede utilizarse para 'filtrando' datos de un conjunto de datos. Le mostraremos el contenido aquí:

![Filtrado de seleccionar columnas en el módulo de conjunto de datos](./media/machine-learning-data-science-process-hive-criteo-walkthrough/oVUJC9K.png)

Para obtener la entrada azul y puertos de salida, simplemente haga clic en **Preparar webservice** en la parte inferior derecha. Ejecutar este ensayo también nos permite publicar el servicio web: haga clic en el icono de **Servicio WEB de publicación** en la parte inferior derecha, se muestra aquí:

![Publicar el servicio Web](./media/machine-learning-data-science-process-hive-criteo-walkthrough/WO0nens.png)


Una vez que se publica el servicio Web, se hacen redirige a una página que se ve así:

![](./media/machine-learning-data-science-process-hive-criteo-walkthrough/YKzxAA5.png)

Vemos dos vínculos para servicios Web en el lado izquierdo:

* El servicio de **Solicitud y respuesta** (o RR) está pensados para que solo predicciones y usamos en este talleres.
* El servicio de **Ejecución por lotes** (BES) se usa para predicciones lote y requiere el usan de los datos de entrada para realizar predicciones residen en el almacenamiento de blobs de Windows Azure.

Haga clic en el vínculo **Que solicitud/respuesta** nos lleva a una página que nos predefinidos previamente código en C#, python y R. Este código se puede usar cómodamente para realizar llamadas al servicio Web. Tenga en cuenta que debe usarse para la autenticación de la clave de la API de esta página.

Es conveniente copiar este código python sobre a una nueva celda en el Bloc de notas de IPython.

Aquí mostramos un segmento de código python con la clave de API correcta.

![Código Python](./media/machine-learning-data-science-process-hive-criteo-walkthrough/f8N4L4g.png)


Observe que se reemplaza la clave de la API de forma predeterminada con la clave de API de nuestros servicios Web. Haga clic en **Ejecutar** en esta celda en un bloc de notas de IPython da como resultado de la respuesta siguiente:

![Respuesta de IPython](./media/machine-learning-data-science-process-hive-criteo-walkthrough/KSxmia2.png)

Vemos que para los dos ejemplos de prueba que se más frecuentes acerca de (en el marco JSON de la secuencia de comandos de python), obtenemos respuestas en el formulario "Scored etiquetas, las probabilidades de Scored". Tenga en cuenta que en este caso, elegimos los valores predeterminados que el código preestablecido proporciona (0 para todas las columnas numéricas y la cadena "valor" para todas las columnas por categorías).

Esto concluye nuestro tutorial to-end que muestra cómo controlar el conjunto de datos a gran escala mediante el aprendizaje Azure. Empezar con un terabyte de datos, la construcción de un modelo de predicción y había implementado como un servicio web en la nube.
