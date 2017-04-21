<properties
    pageTitle="Crear características para los datos en un clúster de Hadoop el uso de consultas de la sección | Microsoft Azure"
    description="Ejemplos de consultas de la sección que generar características de los datos almacenados en un clúster de Azure HDInsight Hadoop."
    services="machine-learning"
    documentationCenter=""
    authors="bradsev"
    manager="jhubbard"
    editor="cgronlun"  />

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/19/2016"
    ms.author="hangzh;bradsev" />


#<a name="create-features-for-data-in-an-hadoop-cluster-using-hive-queries"></a>Crear características para los datos en un clúster de Hadoop el uso de consultas de la sección

Este documento muestra cómo crear características para los datos almacenados en un clúster de Azure HDInsight Hadoop el uso de consultas de la sección. Estas consultas de la sección utilizan incrustado subárbol usuario funciones definidas por (UDF), las secuencias de comandos que se proporcionan.

Las operaciones necesarias para crear características pueden ser memoria intensiva. El rendimiento de las consultas de la sección resulta más importante en estos casos y se puede mejorar ajustando determinados parámetros. El ajuste de estos parámetros se describe en la sección final.

Ejemplos de las consultas que se presentan son específicos de los [Datos de viaje de Nueva York Taxi](http://chriswhong.com/open-data/foil_nyc_taxi/) escenarios también se proporcionan en el [repositorio de Github](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/DataScienceScripts). Estas consultas ya dispone de esquema de datos especificado y están listos para enviar a ejecutar. En la sección final, también se tratan los parámetros que pueden ajustar los usuarios para que se puede mejorar el rendimiento de las consultas de la sección.

[AZURE.INCLUDE [cap-create-features-data-selector](../../includes/cap-create-features-selector.md)]Vínculos de este **menú** a temas que describen cómo crear características para los datos en distintos entornos. Esta tarea es un paso en el [Proceso de ciencias de datos de equipo (TDSP)](https://azure.microsoft.com/documentation/learning-paths/cortana-analytics-process/).


## <a name="prerequisites"></a>Requisitos previos
En este artículo se supone que tiene:

* Crear una cuenta de almacenamiento de Azure. Si necesita instrucciones, vea [crear una cuenta de almacenamiento de Azure](../storage/storage-create-storage-account.md#create-a-storage-account)
* Aprovisiona un clúster de Hadoop personalizado con el servicio de HDInsight.  Si necesita instrucciones, vea [Personalizar Azure HDInsight Hadoop clústeres para análisis avanzado](machine-learning-data-science-customize-hadoop-cluster.md).
* Se ha cargado los datos a la tabla subárbol en Azure HDInsight Hadoop clústeres. Si no es así, siga [crear y cargar datos en tablas de subárbol](machine-learning-data-science-move-hive-tables.md) para cargar datos en tablas de subárbol en primer lugar.
* Habilitar el acceso remoto al clúster. Si necesita instrucciones, consulte [acceso a la cabeza de Hadoop clúster de nodo](machine-learning-data-science-customize-hadoop-cluster.md#headnode).


##<a name="hive-featureengineering"></a>Generación de característica

En esta sección se describen varios ejemplos de las maneras en que se pueden generar características mediante consultas de la sección. Una vez haya generado características adicionales, puede agregar como columnas a la tabla existente o crear una tabla con las características adicionales y la clave principal, que puede combinarse con la tabla original. Estos son los ejemplos que se presentan:

1. [Frecuencia en función de generación de característica](#hive-frequencyfeature)
2. [Riesgos de Variables por categoría de clasificación binario](#hive-riskfeature)
3. [Extraer las características de Datetime Field](#hive-datefeatures)
4. [Extraer las características de un campo de texto](#hive-textfeatures)
5. [Calcular la distancia entre las coordenadas GPS](#hive-gpsdistance)

###<a name="hive-frequencyfeature"></a>Frecuencia en función de generación de característica

A menudo es útil calcular las frecuencias de los niveles de una variable por categorías o la frecuencia de determinadas combinaciones de niveles de varias categorías variables. Los usuarios pueden usar la siguiente secuencia de comandos para calcular estas frecuencias:

        select
            a.<column_name1>, a.<column_name2>, a.sub_count/sum(a.sub_count) over () as frequency
        from
        (
            select
                <column_name1>,<column_name2>, count(*) as sub_count
            from <databasename>.<tablename> group by <column_name1>, <column_name2>
        )a
        order by frequency desc;


###<a name="hive-riskfeature"></a>Riesgos de Variables por categoría de clasificación binario

En la clasificación binario, necesitamos convertir variables de por categorías no numéricos en características numéricas cuando los modelos que se usa solo tienen características numéricas. Esto se hace reemplazando cada nivel no es numérico, con un riesgo numérico. En esta sección, le mostraremos algunas consultas subárbol genéricos que calculan los valores de riesgo (registro probabilidades) de una variable por categorías.


        set smooth_param1=1;
        set smooth_param2=20;
        select
            <column_name1>,<column_name2>,
            ln((sum_target+${hiveconf:smooth_param1})/(record_count-sum_target+${hiveconf:smooth_param2}-${hiveconf:smooth_param1})) as risk
        from
            (
            select
                <column_nam1>, <column_name2>, sum(binary_target) as sum_target, sum(1) as record_count
            from
                (
                select
                    <column_name1>, <column_name2>, if(target_column>0,1,0) as binary_target
                from <databasename>.<tablename>
                )a
            group by <column_name1>, <column_name2>
            )b

En este ejemplo, las variables `smooth_param1` y `smooth_param2` configurados para redondear los valores de riesgo calculados a partir de los datos. Riesgos tienen un rango comprendido entre -Inf y Inf. Riesgos > 0 indica que la probabilidad de que el destino es igual a 1 es mayor que 0,5.

Tras el riesgo se calcula tabla, los usuarios pueden asignar valores de riesgo a una tabla mediante la combinación con la tabla de riesgos. La consulta de unión de la sección se proporciona en la sección anterior.

###<a name="hive-datefeatures"></a>Extraer las características de Datetime Fields

Sección incluye un conjunto de UDF para el procesamiento de campos de fecha y hora. En la sección, es el formato de fecha y hora predeterminado ' dd-MM-AAAA 00:00:00 ' ('1970-01-01 12:21:32 ' por ejemplo). En esta sección, le mostraremos algunos ejemplos que extraer el día del mes, mes a partir de un campo de fecha y hora y otros ejemplos en los que convertir una cadena de fecha y hora en un formato distinto el formato predeterminado para una cadena de fecha y hora en el formato predeterminado.

        select day(<datetime field>), month(<datetime field>)
        from <databasename>.<tablename>;

Consulta de esta sección, supone que la *& #60; campo datetime >* está en el formato de fecha y hora predeterminado.

Si no es un campo de fecha y hora en el formato predeterminado, debe convertir el campo de fecha y hora en marca de tiempo de Unix en primer lugar y, a continuación, convertir la marca de tiempo de Unix en una cadena de fecha y hora que se encuentra en el formato predeterminado. Cuando la fecha y hora de forma predeterminada en formato, los usuarios pueden aplicar el valor de datetime incrustado UDF extraer características.

        select from_unixtime(unix_timestamp(<datetime field>,'<pattern of the datetime field>'))
        from <databasename>.<tablename>;

En esta consulta, si la *& #60; campo datetime >* tenga el diseño como *26/03/2015 12:04:39*, la *' & #60; trama del campo de fecha y hora >'* debería ser `'MM/dd/yyyy HH:mm:ss'`. Para probarlo, los usuarios pueden ejecutar

        select from_unixtime(unix_timestamp('05/15/2015 09:32:10','MM/dd/yyyy HH:mm:ss'))
        from hivesampletable limit 1;

El *hivesampletable* en esta consulta viene preinstalado en todos los clústeres de Azure HDInsight Hadoop de forma predeterminada cuando se aprovisiona los clústeres.


###<a name="hive-textfeatures"></a>Extraer las características de los campos de texto

Cuando la tabla subárbol tiene un campo de texto que contiene una cadena de palabras que están delimitados por espacios, la siguiente consulta extrae la longitud de la cadena y el número de palabras en la cadena.

        select length(<text field>) as str_len, size(split(<text field>,' ')) as word_num
        from <databasename>.<tablename>;

###<a name="hive-gpsdistance"></a>Calcular la distancia entre conjuntos de coordenadas GPS

La consulta presentada en esta sección se puede aplicar directamente a los datos de viaje de Taxi de Nueva York. La finalidad de esta consulta es mostrar cómo aplicar un funciones matemáticas incrustado en la sección generar características.

Los campos que se usan en esta consulta son las coordenadas GPS de recogida y caída ubicaciones, denominados *recogida\_latitud*, *recogida\_latitud*, *caída\_latitud*, y *caída\_latitud*. Las consultas que calcular la distancia entre las coordenadas recogida y caída directa son:

        set R=3959;
        set pi=radians(180);
        select pickup_longitude, pickup_latitude, dropoff_longitude, dropoff_latitude,
            ${hiveconf:R}*2*2*atan((1-sqrt(1-pow(sin((dropoff_latitude-pickup_latitude)
            *${hiveconf:pi}/180/2),2)-cos(pickup_latitude*${hiveconf:pi}/180)
            *cos(dropoff_latitude*${hiveconf:pi}/180)*pow(sin((dropoff_longitude-pickup_longitude)*${hiveconf:pi}/180/2),2)))
            /sqrt(pow(sin((dropoff_latitude-pickup_latitude)*${hiveconf:pi}/180/2),2)
            +cos(pickup_latitude*${hiveconf:pi}/180)*cos(dropoff_latitude*${hiveconf:pi}/180)*
            pow(sin((dropoff_longitude-pickup_longitude)*${hiveconf:pi}/180/2),2))) as direct_distance
        from nyctaxi.trip
        where pickup_longitude between -90 and 0
        and pickup_latitude between 30 and 90
        and dropoff_longitude between -90 and 0
        and dropoff_latitude between 30 and 90
        limit 10;

Las ecuaciones matemáticas que calcular la distancia entre dos coordenadas GPS pueden encontrarse en el sitio de <a href="http://www.movable-type.co.uk/scripts/latlong.html" target="_blank">Secuencias de comandos de tipo de móvil</a> , editada por Peter Lapisu. En su Javascript, la función `toRad()` solo *lat_or_lon*pi/180*, que convierte grados en radianes. Aquí, *lat_or_lon * es la latitud o. Dado que subárbol no proporciona la función `atan2`, pero proporciona la función `atan`, el `atan2` implementa función `atan` función en la consulta de sección anterior mediante la definición establecida en <a href="http://en.wikipedia.org/wiki/Atan2" target="_blank">Wikipedia</a>.

![Crear área de trabajo](./media/machine-learning-data-science-create-features-hive/atan2new.png)

Una lista completa de la sección UDF incrustadas pueden encontrarse en la sección de **Funciones integradas** en la <a href="https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-MathematicalFunctions" target="_blank">sección Apache wiki</a>).  

## <a name="tuning"></a>Temas avanzados: ajustar parámetros de subárbol para mejorar la velocidad de consulta

La configuración de parámetro predeterminada de clúster subárbol no sea adecuada para las consultas de la sección y los datos que se procesan las consultas. En esta sección se describen algunos parámetros que pueden ajustar los usuarios que mejorar el rendimiento de las consultas de la sección. Los usuarios tendrán que agregar el parámetro optimizar consultas del procesamiento de datos de las consultas.

1. **Espacio de montón de Java**: las consultas que impliquen unirse a grandes conjuntos de datos o el procesamiento de registros largo, **se está quedando sin espacio de montón** es uno de los errores comunes. Esto puede ajustarse mediante la configuración de parámetros *mapreduce.map.java.opts* y *mapreduce.task.io.sort.mb* a los valores que desee. Aquí tenemos un ejemplo:

        set mapreduce.map.java.opts=-Xmx4096m;
        set mapreduce.task.io.sort.mb=-Xmx1024m;


    Este parámetro asigna 4GB de memoria al espacio de montón de Java y también realiza ordenación más eficaz asignando más memoria para él. Es una buena idea jugar con estas asignaciones si hay errores relacionados con el espacio de montón a cualquier trabajo.

2. **DFS Bloquear tamaño** : este parámetro establece la unidad más pequeña de datos que almacena el sistema de archivos. Por ejemplo, si el tamaño del bloque DFS es de 128MB, a continuación, los datos de tamaño menor y hasta 128MB se almacena en un único bloque, mientras que los datos que es mayores que 128MB asignado bloques adicionales. Elegir un tamaño de bloque muy pequeño hace grandes gastos en Hadoop debido a que tiene el nodo name procesar más solicitudes de encontrar el bloque relevante relacionados con el archivo. Una configuración recomendada cuando ocupen con gigabytes (o mayor) están de datos:

        set dfs.block.size=128m;

3. **Optimizar unirse a la operación en subárbol** : mientras las operaciones de combinación en el marco del mapa o reducir normalmente tienen lugar en la fase de reducir, en ocasiones, se consigue ganancias enormes programando combinaciones en la fase de mapa (también denominada "mapjoins"). Para dirigir subárbol para realizar esta acción siempre que sea posible, podemos establecer:

        set hive.auto.convert.join=true;

4. **Especifica el número de Mapeados al subárbol** : Hadoop mientras permite al usuario establecer el número de reductores, suele ser el número de Mapeados no pueden configurarse por el usuario. Tienen una mano que permite que cierto grado de control en este número es elegir las variables Hadoop, *mapred.min.split.size* y *mapred.max.split.size* como el tamaño de cada tarea de asignación:

        num_maps = max(mapred.min.split.size, min(mapred.max.split.size, dfs.block.size))

    Normalmente, el valor predeterminado de *mapred.min.split.size* es 0, de *mapred.max.split.size* es **Long.MAX** y de *dfs.block.size* es 64 MB. Como podemos ver, dado el tamaño de datos, ajuste estos parámetros por "configuración" ellos nos permite ajustar el número de Mapeados utilizado.

5. A continuación se indican algunas otras más **Opciones avanzadas** para optimizar el rendimiento de la sección. Estos permiten establecer la memoria asignada para asignar y reducir las tareas y pueden ser útiles para ajustar el rendimiento. Tenga en cuenta que el *mapreduce.reduce.memory.mb* no puede ser mayor que el tamaño de la memoria física de cada nodo de trabajo en el clúster Hadoop.

        set mapreduce.map.memory.mb = 2048;
        set mapreduce.reduce.memory.mb=6144;
        set mapreduce.reduce.java.opts=-Xmx8192m;
        set mapred.reduce.tasks=128;
        set mapred.tasktracker.reduce.tasks.maximum=128;
