<properties
    pageTitle="Exploración de datos y modelado de motor | Microsoft Azure"
    description="Se muestran las capacidades de exploración y modelado de datos del Kit de herramientas de motor MLlib."
    services="machine-learning"
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
    ms.date="10/07/2016"
    ms.author="deguhath;bradsev;gokuma" />

# <a name="data-exploration-and-modeling-with-spark"></a>Exploración de datos y modelado de motor

[AZURE.INCLUDE [machine-learning-spark-modeling](../../includes/machine-learning-spark-modeling.md)]

Este tutorial utiliza HDInsight Spark para realizar la exploración de datos y clasificación binario y tareas de modelado de una muestra de la ciudad de Nueva York de regresión taxi viaje y resultados son el conjunto de datos de 2013.  Le guiará por los pasos del [Proceso de ciencias de datos](http://aka.ms/datascienceprocess), llevar a cabo con un HDInsight Spark de clúster para el procesamiento y blobs de Azure para almacenar los datos y los modelos. El proceso explora y visualiza los datos que se ha insertado de un Blob de almacenamiento de Azure y, a continuación, prepara los datos para crear modelos de predicción. Estos modelos son generación utilizando el Kit de herramientas de motor MLlib hacer regresión y clasificación binario tareas de modelado.

- La tarea de **clasificación binario** es predecir si no se paga una sugerencia para el viaje. 
- La tarea de **regresión** es predecir la cantidad de la sugerencia en función de otras características de sugerencia. 

Los modelos que usamos incluyen regresión lineal y logística, aleatorios bosques y árboles aumentados degradados:

- [Regresión lineal con SGD](https://spark.apache.org/docs/latest/api/python/pyspark.mllib.html#pyspark.mllib.regression.LinearRegressionWithSGD) es un modelo de regresión lineal que utiliza un método de descenso degradado estocástico (SGD) y para la optimización y la característica escalar para predecir los importes de sugerencia de pago. 
- Regresión [regresión logística con LBFGS](https://spark.apache.org/docs/latest/api/python/pyspark.mllib.html#pyspark.mllib.classification.LogisticRegressionWithLBFGS) o "logit", es un modelo de regresión pueden utilizarse para que la variable dependiente es por categorías para realizar la clasificación de datos. LBFGS es un algoritmo de optimización de cuasi-Hernández que aproxima el algoritmo de Broyden – Fletcher – Goldfarb-Shanno (BFGS) con una cantidad limitada de memoria del equipo y que se utiliza ampliamente en aprendizaje de equipo.
- [Aleatorio bosques](http://spark.apache.org/docs/latest/mllib-ensembles.html#Random-Forests) son conjuntos de árboles de decisión.  Combinan muchos árboles de decisión para reducir el riesgo de desbordamiento. Aleatorios bosques se usan para regresión y clasificación y pueden controlar características por categorías y pueden ampliarse a la configuración de clasificación multiclass. Que no requieren escalado de características y se pueden capturar los errores de alineación e interacciones de características. Aleatorios bosques son uno de la máquina más éxito modelos de clasificación y regresión de aprendizaje.
- [Degradado aumenta árboles](http://spark.apache.org/docs/latest/ml-classification-regression.html#gradient-boosted-trees-gbts) (GBTs) son conjuntos de árboles de decisión. GBTs entrenar árboles de decisión repetidamente para minimizar una función de pérdida. GBTs se usan para regresión y clasificación puede controlar características por categorías no requieren la característica de ajuste de escala y, puede capturar los errores de alineación e interacciones de características. También se puede utilizar en una configuración de clasificación de multiclass.

Los pasos de modelado también contienen código que muestra cómo entrenar, evaluar y a continuación, guarde cada tipo de modelo. Se ha usado Python para la solución de código y mostrar la trazados relevantes.   


>[AZURE.NOTE] Aunque el Kit de herramientas de motor MLlib está diseñado para ejecutarse en grandes conjuntos de datos, se utiliza aquí un ejemplo relativamente pequeño (con 170K filas, aproximadamente 0,1% del conjunto de datos de Nueva York original de ~ 30 Mb) para mayor comodidad. El ejercicio aquí se ejecuta eficazmente (en unos 10 minutos) en un clúster de HDInsight con 2 nodos de trabajo. El mismo código, con pequeñas modificaciones, puede usarse para procesar más grandes conjuntos de datos, con las modificaciones apropiadas para el almacenamiento en caché de datos en la memoria y cambiar el tamaño de clúster.

## <a name="prerequisites"></a>Requisitos previos

Necesita una cuenta de Azure y un clúster de necesita una 1,6 de motor 3.4 HDInsight HDInsight motor que para completar este tutorial. Vea la [Información general de ciencias datos mediante el motor en Azure HDInsight](machine-learning-data-science-spark-overview.md) para obtener instrucciones sobre cómo cumplir estos requisitos. Este tema también contiene una descripción de los datos de Nueva York 2013 Taxi utilizados aquí e instrucciones sobre cómo ejecutar el código de un bloc de notas de Jupyter en el clúster de motor. El Bloc de notas **pySpark-machine-learning-data-science-spark-data-exploration-modeling.ipynb** que contiene los ejemplos de código de este tema está disponible en [Github](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/Spark/pySpark). 


[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]


## <a name="setup-storage-locations-libraries-and-the-preset-spark-context"></a>Configuración: el contexto de motor preestablecido, bibliotecas y ubicaciones de almacenamiento

Motor es capaz de leer y escribir en Azure almacenamiento de blobs (también conocido como WASB). Para que cualquiera de los datos existentes almacenados allí pueden procesar con el motor y los resultados almacenados nuevamente en WASB.

Para guardar los archivos o modelos en WASB, la ruta de acceso debe especificarse correctamente. Se puede hacer referencia el contenedor predeterminado conectado al clúster motor con el que comienza con una ruta de acceso: "wasb: / / /". Otras ubicaciones hace referencia a "wasb: / /".


### <a name="set-directory-paths-for-storage-locations-in-wasb"></a>Establecer rutas de directorios para ubicaciones de almacenamiento de WASB

El ejemplo siguiente especifica la ubicación de los datos que se puedan leer y la ruta de acceso para el directorio de almacenamiento de modelo a la que se guarda el resultado de modelo:


    # SET PATHS TO FILE LOCATIONS: DATA AND MODEL STORAGE

    # LOCATION OF TRAINING DATA
    taxi_train_file_loc = "wasb://mllibwalkthroughs@cdspsparksamples.blob.core.windows.net/Data/NYCTaxi/JoinedTaxiTripFare.Point1Pct.Train.tsv";

    # SET THE MODEL STORAGE DIRECTORY PATH 
    # NOTE THAT THE FINAL BACKSLASH IN THE PATH IS NEEDED.
    modelDir = "wasb:///user/remoteuser/NYCTaxi/Models/" 


### <a name="import-libraries"></a>Bibliotecas de importación

Configurar también requiere la importación de bibliotecas necesarias. Establecer el contexto de motor e importar bibliotecas necesarias con el siguiente código:


    # IMPORT LIBRARIES
    import pyspark
    from pyspark import SparkConf
    from pyspark import SparkContext
    from pyspark.sql import SQLContext
    import matplotlib
    import matplotlib.pyplot as plt
    from pyspark.sql import Row
    from pyspark.sql.functions import UserDefinedFunction
    from pyspark.sql.types import *
    import atexit
    from numpy import array
    import numpy as np
    import datetime


### <a name="preset-spark-context-and-pyspark-magics"></a>Contexto de motor preestablecido y PySpark magics

Los kernels PySpark que se proporcionan con blocs de notas Jupyter tienen un contexto preestablecido. Así que no es necesario configurar el motor o desarrollan subárbol contextos explícitamente antes de empezar a trabajar con la aplicación. Estos contextos están disponibles de forma predeterminada. Estos contextos son:

- SC - del motor 
- sqlContext - para sección

El núcleo PySpark proporciona algunos predefinido "magics", que son comandos especiales que se pueden llamar con %%. Hay dos comandos que se utilizan en estos ejemplos de código.

- **%% local** Especifica que el código de las siguientes líneas se ejecutará localmente. Código debe ser código Python válido.
- **%%sql -o <variable name>** Ejecuta una consulta de la sección con la sqlContext. Si se pasa el parámetro -o, el resultado de la consulta se conserva en el %% contexto Python local como una DataFrame Pandas.
 

Para obtener más información sobre los kernels de blocs de notas de Jupyter y la predefinido "magics" que proporcionan, consulte [Kernels disponibles para los blocs de notas de Jupyter con HDInsight motor Linux clústeres en HDInsight](../hdinsight/hdinsight-apache-spark-jupyter-notebook-kernels.md).
 

## <a name="data-ingestion-from-public-blob"></a>Recopilación de datos de blob público

El primer paso en el proceso de ciencias datos es recopilar los datos que se pueden analizar de orígenes donde está reside en su entorno de exploración y modelado de datos. El entorno es motor en este tutorial. Esta sección contiene el código para completar una serie de tareas:

- Recopile la muestra datos modelar
- leer en el conjunto de datos de entrada (almacenado como un archivo .tsv)
- aplicar formato y limpiar los datos
- crear y almacenar en caché objetos (RDDs o tramas de datos) en la memoria
- registrarla como una tabla temp en el contexto de SQL.

Este es el código de recopilación de datos.

    # INGEST DATA

    # RECORD START TIME
    timestart = datetime.datetime.now()

    # IMPORT FILE FROM PUBLIC BLOB
    taxi_train_file = sc.textFile(taxi_train_file_loc)
    
    # GET SCHEMA OF THE FILE FROM HEADER
    schema_string = taxi_train_file.first()
    fields = [StructField(field_name, StringType(), True) for field_name in schema_string.split('\t')]
    fields[7].dataType = IntegerType() #Pickup hour
    fields[8].dataType = IntegerType() # Pickup week
    fields[9].dataType = IntegerType() # Weekday
    fields[10].dataType = IntegerType() # Passenger count
    fields[11].dataType = FloatType() # Trip time in secs
    fields[12].dataType = FloatType() # Trip distance
    fields[19].dataType = FloatType() # Fare amount
    fields[20].dataType = FloatType() # Surcharge
    fields[21].dataType = FloatType() # Mta_tax
    fields[22].dataType = FloatType() # Tip amount
    fields[23].dataType = FloatType() # Tolls amount
    fields[24].dataType = FloatType() # Total amount
    fields[25].dataType = IntegerType() # Tipped or not
    fields[26].dataType = IntegerType() # Tip class
    taxi_schema = StructType(fields)
    
    # PARSE FIELDS AND CONVERT DATA TYPE FOR SOME FIELDS
    taxi_header = taxi_train_file.filter(lambda l: "medallion" in l)
    taxi_temp = taxi_train_file.subtract(taxi_header).map(lambda k: k.split("\t"))\
            .map(lambda p: (p[0],p[1],p[2],p[3],p[4],p[5],p[6],int(p[7]),int(p[8]),int(p[9]),int(p[10]),
                            float(p[11]),float(p[12]),p[13],p[14],p[15],p[16],p[17],p[18],float(p[19]),
                            float(p[20]),float(p[21]),float(p[22]),float(p[23]),float(p[24]),int(p[25]),int(p[26])))
    
    
    # CREATE DATA FRAME
    taxi_train_df = sqlContext.createDataFrame(taxi_temp, taxi_schema)
    
    # CREATE A CLEANED DATA-FRAME BY DROPPING SOME UN-NECESSARY COLUMNS & FILTERING FOR UNDESIRED VALUES OR OUTLIERS
    taxi_df_train_cleaned = taxi_train_df.drop('medallion').drop('hack_license').drop('store_and_fwd_flag').drop('pickup_datetime')\
        .drop('dropoff_datetime').drop('pickup_longitude').drop('pickup_latitude').drop('dropoff_latitude')\
        .drop('dropoff_longitude').drop('tip_class').drop('total_amount').drop('tolls_amount').drop('mta_tax')\
        .drop('direct_distance').drop('surcharge')\
        .filter("passenger_count > 0 and passenger_count < 8 AND payment_type in ('CSH', 'CRD') AND tip_amount >= 0 AND tip_amount < 30 AND fare_amount >= 1 AND fare_amount < 150 AND trip_distance > 0 AND trip_distance < 100 AND trip_time_in_secs > 30 AND trip_time_in_secs < 7200" )

    
    # CACHE DATA-FRAME IN MEMORY & MATERIALIZE DF IN MEMORY
    taxi_df_train_cleaned.cache()
    taxi_df_train_cleaned.count()
    
    # REGISTER DATA-FRAME AS A TEMP-TABLE IN SQL-CONTEXT
    taxi_df_train_cleaned.registerTempTable("taxi_train")
    
    # PRINT HOW MUCH TIME IT TOOK TO RUN THE CELL
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds";

**RESULTADO:**

Tiempo necesario para ejecutar encima de celda: 51.72 segundos


## <a name="data-exploration--visualization"></a>Visualización y exploración de datos

Una vez que los datos se ha conectado a motor, es el siguiente paso del proceso de ciencias de datos obtener un conocimiento más profundo de los datos a través de exploración y visualización. En esta sección, nos examinar los datos de taxi mediante consultas SQL y trazar las variables de destino y las características posibles para inspección visual. Más concretamente, nos trazar la frecuencia de recuentos de pasajeros en taxi viajes, la frecuencia de importes de sugerencia y cómo sugerencias varían según el tipo y la cantidad de pago.

### <a name="plot-a-histogram-of-passenger-count-frequencies-in-the-sample-of-taxi-trips"></a>Trazar un histograma de frecuencias de recuento de pasajeros en la muestra de viajes de taxi

Este código y fragmentos posteriores use mágico SQL para consultar la muestra y mágico local para trazar los datos.

- **Mágico SQL (`%%sql`)** El núcleo HDInsight PySpark admite consultas de HiveQL de fácil incorporada en el sqlContext. La (-o nombre_variable) argumento conserva el resultado de la consulta SQL como un DataFrame Pandas en el servidor Jupyter. Esto significa que está disponible en modo local.
- La ** `%%local` mágico** se usa para ejecutar el código localmente en el servidor Jupyter, que es el headnode del clúster HDInsight. Normalmente, se utiliza `%%local` mágico junto con la `%%sql` mágico con parámetro -o. El parámetro -o desea conservar el resultado de la consulta SQL localmente y, a continuación, %% mágico local activaría el siguiente conjunto de fragmento de código para ejecutar localmente en el resultado de las consultas SQL que se almacena localmente

El resultado se visualiza automáticamente después de ejecutar el código.

Esta consulta recupera los viajes por el número de pasajeros. 

    # PLOT FREQUENCY OF PASSENGER COUNTS IN TAXI TRIPS

    # HIVEQL QUERY AGAINST THE sqlContext
    %%sql -q -o sqlResults
    SELECT passenger_count, COUNT(*) as trip_counts 
    FROM taxi_train 
    WHERE passenger_count > 0 and passenger_count < 7 
    GROUP BY passenger_count 

Este código crea una trama de datos local de resultado de la consulta y traza los datos. La `%%local` mágico crea una trama de datos local, `sqlResults`, que se puede utilizar para representar el gráfico con matplotlib. 

>[AZURE.NOTE] Este mágico PySpark se utiliza varias veces en este tutorial. Si la cantidad de datos es grande, debe ejemplo para crear una trama de datos que puede caber en la memoria local.

    #CREATE LOCAL DATA-FRAME AND USE FOR MATPLOTLIB PLOTTING

    # RUN THE CODE LOCALLY ON THE JUPYTER SERVER
    %%local
    
    # USE THE JUPYTER AUTO-PLOTTING FEATURE TO CREATE INTERACTIVE FIGURES. 
    # CLICK ON THE TYPE OF PLOT TO BE GENERATED (E.G. LINE, AREA, BAR ETC.)
    sqlResults

Este es el código para trazar la viajes por recuentos de pasajeros

    # PLOT PASSENGER NUMBER VS. TRIP COUNTS
    %%local
    import matplotlib.pyplot as plt
    %matplotlib inline
    
    x_labels = sqlResults['passenger_count'].values
    fig = sqlResults[['trip_counts']].plot(kind='bar', facecolor='lightblue')
    fig.set_xticklabels(x_labels)
    fig.set_title('Counts of trips by passenger count')
    fig.set_xlabel('Passenger count in trips')
    fig.set_ylabel('Trip counts')
    plt.show()

**RESULTADO:**

![Frecuencia de ida y vuelta por el número de pasajeros](./media/machine-learning-data-science-spark-data-exploration-modeling/trip-freqency-by-passenger-count.png)

Puede seleccionar entre diferentes tipos de visualizaciones (tabla, circulares, líneas, área o barra) mediante los botones de menú de **tipo** en el Bloc de notas. Aquí se muestra el trazado de barra.
    
### <a name="plot-a-histogram-of-tip-amounts-and-how-tip-amount-varies-by-passenger-count-and-fare-amounts"></a>Trazar un histograma de sugerencia cantidades y cómo sugerencia varía por cantidades de contar y tarifa pasajeros.

Usar una consulta SQL para datos de ejemplo.

    #PLOT HISTOGRAM OF TIP AMOUNTS AND VARIATION BY PASSENGER COUNT AND PAYMENT TYPE
    
    # HIVEQL QUERY AGAINST THE sqlContext
    %%sql -q -o sqlResults
    SELECT fare_amount, passenger_count, tip_amount, tipped 
    FROM taxi_train 
    WHERE passenger_count > 0 
    AND passenger_count < 7 
    AND fare_amount > 0 
    AND fare_amount < 200 
    AND payment_type in ('CSH', 'CRD') 
    AND tip_amount > 0 
    AND tip_amount < 25


Esta celda código usa la consulta SQL para crear tres trazados los datos.

    # RUN THE CODE LOCALLY ON THE JUPYTER SERVER
    %%local
    
    # HISTOGRAM OF TIP AMOUNTS AND PASSENGER COUNT
    ax1 = sqlResults[['tip_amount']].plot(kind='hist', bins=25, facecolor='lightblue')
    ax1.set_title('Tip amount distribution')
    ax1.set_xlabel('Tip Amount ($)')
    ax1.set_ylabel('Counts')
    plt.suptitle('')
    plt.show()
    
    # TIP BY PASSENGER COUNT
    ax2 = sqlResults.boxplot(column=['tip_amount'], by=['passenger_count'])
    ax2.set_title('Tip amount by Passenger count')
    ax2.set_xlabel('Passenger count')
    ax2.set_ylabel('Tip Amount ($)')
    plt.suptitle('')
    plt.show()
    
    # TIP AMOUNT BY FARE AMOUNT, POINTS ARE SCALED BY PASSENGER COUNT
    ax = sqlResults.plot(kind='scatter', x= 'fare_amount', y = 'tip_amount', c='blue', alpha = 0.10, s=5*(sqlResults.passenger_count))
    ax.set_title('Tip amount by Fare amount')
    ax.set_xlabel('Fare Amount ($)')
    ax.set_ylabel('Tip Amount ($)')
    plt.axis([-2, 100, -2, 20])
    plt.show()


**RESULTADO:** 

![Distribución de la cantidad de sugerencia](./media/machine-learning-data-science-spark-data-exploration-modeling/tip-amount-distribution.png)

![Sugerencia cantidad por número de pasajeros](./media/machine-learning-data-science-spark-data-exploration-modeling/tip-amount-by-passenger-count.png)

![Cantidad por la cantidad de tarifa de ideas](./media/machine-learning-data-science-spark-data-exploration-modeling/tip-amount-by-fare-amount.png)


## <a name="feature-engineering-transformation-and-data-preparation-for-modeling"></a>Preparación de ingeniería, transformación y datos para un modelado de características
Esta sección describe y proporciona el código de los procedimientos que se utilizan para preparar los datos para su uso en el modelado de m. Muestra cómo realizar las siguientes tareas:

- Crear una nueva característica binning horas en depósitos de tiempo de tráfico
- Indizar y codificar características por categoría
- Crear objetos etiquetada de punto de entrada en funciones de m
- Crear un sondeo subcarpetas de los datos y dividir en formación y conjuntos de pruebas
- Característica de ajuste de escala
- Objetos de caché en memoria


### <a name="create-a-new-feature-by-binning-hours-into-traffic-time-buckets"></a>Crear una nueva característica binning horas en depósitos de tiempo de tráfico

Este código muestra cómo crear una nueva característica binning horas en depósitos de tiempo de tráfico y, a continuación, cómo el marco de datos resultantes en la memoria en caché. Cuando se utilicen varias veces y son resistentes distribuido los conjuntos de datos (RDDs) y tramas de datos, almacenamiento en caché lleva a los tiempos de ejecución mejorados. Por lo tanto, se almacenará en caché RDDs y tramas de datos en varias fases en el tutorial. 

    # CREATE FOUR BUCKETS FOR TRAFFIC TIMES
    sqlStatement = """
        SELECT *,
        CASE
         WHEN (pickup_hour <= 6 OR pickup_hour >= 20) THEN "Night" 
         WHEN (pickup_hour >= 7 AND pickup_hour <= 10) THEN "AMRush" 
         WHEN (pickup_hour >= 11 AND pickup_hour <= 15) THEN "Afternoon"
         WHEN (pickup_hour >= 16 AND pickup_hour <= 19) THEN "PMRush"
        END as TrafficTimeBins
        FROM taxi_train 
    """
    taxi_df_train_with_newFeatures = sqlContext.sql(sqlStatement)
    
    # CACHE DATA-FRAME IN MEMORY & MATERIALIZE DF IN MEMORY
    # THE .COUNT() GOES THROUGH THE ENTIRE DATA-FRAME,
    # MATERIALIZES IT IN MEMORY, AND GIVES THE COUNT OF ROWS.
    taxi_df_train_with_newFeatures.cache()
    taxi_df_train_with_newFeatures.count()

**RESULTADO:** 

126050

### <a name="index-and-encode-categorical-features-for-input-into-modeling-functions"></a>Indizar y codificar características por categorías para la entrada en funciones de modelado

Esta sección muestra cómo indizar o codificar características por categorías para la entrada en las funciones de modelado. El modelado y predecir las funciones de MLlib requieren características con los datos de entrada por categorías indexado o codificado antes de usarla. Dependiendo del modelo, debe indizar o codificar ellos de maneras diferentes:  

- **Modelado de árbol** requiere categorías para codificar como valores numéricos (por ejemplo, una característica con tres categorías puede codificada con 0, 1, 2). Se presenta en función de [StringIndexer](http://spark.apache.org/docs/latest/ml-features.html#stringindexer) de MLlib. Esta función codifica una columna de cadena de etiquetas a una columna de índices de etiqueta que se ordenan por frecuencias de etiqueta. Aunque indizado con valores numéricos para la entrada y tratamiento de datos, pueden especificar los algoritmos de árbol traten adecuadamente como categorías. 

- Donde **modelos Logistic y regresión lineal** requieren la codificación de caliente uno, por ejemplo, se puede expandir una característica con tres categorías en tres columnas de característica, con cada contiene 0 o 1, dependiendo de la categoría de una observación. MLlib proporciona la función [OneHotEncoder](http://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.OneHotEncoder.html#sklearn.preprocessing.OneHotEncoder) para realizar caliente una codificación. Este codificador asigna una columna de índices de etiqueta a una columna de vectores binarios, con al menos un solo uno valor. Esta codificación permite algoritmos espera características de valores numéricas, como regresión logística, que se aplique a las características por categoría.

Este es el código de índice y codificar características por categorías:


    # INDEX AND ENCODE CATEGORICAL FEATURES

    # RECORD START TIME
    timestart = datetime.datetime.now()

    # LOAD PYSPARK LIBRARIES    
    from pyspark.ml.feature import OneHotEncoder, StringIndexer, VectorAssembler, VectorIndexer
    
    # INDEX AND ENCODE VENDOR_ID
    stringIndexer = StringIndexer(inputCol="vendor_id", outputCol="vendorIndex")
    model = stringIndexer.fit(taxi_df_train_with_newFeatures) # Input data-frame is the cleaned one from above
    indexed = model.transform(taxi_df_train_with_newFeatures)
    encoder = OneHotEncoder(dropLast=False, inputCol="vendorIndex", outputCol="vendorVec")
    encoded1 = encoder.transform(indexed)
    
    # INDEX AND ENCODE RATE_CODE
    stringIndexer = StringIndexer(inputCol="rate_code", outputCol="rateIndex")
    model = stringIndexer.fit(encoded1)
    indexed = model.transform(encoded1)
    encoder = OneHotEncoder(dropLast=False, inputCol="rateIndex", outputCol="rateVec")
    encoded2 = encoder.transform(indexed)
    
    # INDEX AND ENCODE PAYMENT_TYPE
    stringIndexer = StringIndexer(inputCol="payment_type", outputCol="paymentIndex")
    model = stringIndexer.fit(encoded2)
    indexed = model.transform(encoded2)
    encoder = OneHotEncoder(dropLast=False, inputCol="paymentIndex", outputCol="paymentVec")
    encoded3 = encoder.transform(indexed)
    
    # INDEX AND TRAFFIC TIME BINS
    stringIndexer = StringIndexer(inputCol="TrafficTimeBins", outputCol="TrafficTimeBinsIndex")
    model = stringIndexer.fit(encoded3)
    indexed = model.transform(encoded3)
    encoder = OneHotEncoder(dropLast=False, inputCol="TrafficTimeBinsIndex", outputCol="TrafficTimeBinsVec")
    encodedFinal = encoder.transform(indexed)
    
    # PRINT ELAPSED TIME
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 

**RESULTADO:**

Tiempo necesario para ejecutar encima de celda: 1,28 segundos

### <a name="create-labeled-point-objects-for-input-into-ml-functions"></a>Crear objetos etiquetada de punto de entrada en funciones de m

Esta sección contiene código que muestra cómo indizar datos de texto por categoría como un tipo de datos de punto etiquetada y codificar para que se puede utilizar para entrenar y probar regresión logística de MLlib y otros modelos de clasificación. Objetos de punto etiquetada son resistentes distribuido conjuntos de datos (RDD) con formato de manera que la mayoría de los algoritmos m en MLlib necesita como datos de entrada. Un [punto de la etiqueta](https://spark.apache.org/docs/latest/mllib-data-types.html#labeled-point) es un vector local, denso o disperso, asociado con una respuesta de etiqueta.  

Esta sección contiene código que muestra cómo indizar datos de texto por categoría como un tipo de datos [denominado punto](https://spark.apache.org/docs/latest/mllib-data-types.html#labeled-point) y codificar para que se puede utilizar para entrenar y probar regresión logística de MLlib y otros modelos de clasificación. Objetos de punto etiquetada son resistentes distribuido conjuntos de datos (RDD) que consta de una etiqueta (variable de destino y respuesta) y vector característica. Este formato es necesaria como entrada por muchos algoritmos m en MLlib.

Este es el código de índice y codificar las características de texto para clasificación binario.

    # FUNCTIONS FOR BINARY CLASSIFICATION

    # LOAD LIBRARIES
    from pyspark.mllib.regression import LabeledPoint
    from numpy import array

    # INDEXING CATEGORICAL TEXT FEATURES FOR INPUT INTO TREE-BASED MODELS
    def parseRowIndexingBinary(line):
        features = np.array([line.paymentIndex, line.vendorIndex, line.rateIndex, line.TrafficTimeBinsIndex,
                             line.pickup_hour, line.weekday, line.passenger_count, line.trip_time_in_secs, 
                             line.trip_distance, line.fare_amount])
        labPt = LabeledPoint(line.tipped, features)
        return  labPt
    
    # ONE-HOT ENCODING OF CATEGORICAL TEXT FEATURES FOR INPUT INTO LOGISTIC RERESSION MODELS
    def parseRowOneHotBinary(line):
        features = np.concatenate((np.array([line.pickup_hour, line.weekday, line.passenger_count,
                                            line.trip_time_in_secs, line.trip_distance, line.fare_amount]), 
                                            line.vendorVec.toArray(), line.rateVec.toArray(), 
                                            line.paymentVec.toArray(), line.TrafficTimeBinsVec.toArray()), axis=0)
        labPt = LabeledPoint(line.tipped, features)
        return  labPt


Este es el código para codificar y características de texto por categorías para análisis de regresión lineal de índice.

    # FUNCTIONS FOR REGRESSION WITH TIP AMOUNT AS TARGET VARIABLE

    # ONE-HOT ENCODING OF CATEGORICAL TEXT FEATURES FOR INPUT INTO TREE-BASED MODELS
    def parseRowIndexingRegression(line):
        features = np.array([line.paymentIndex, line.vendorIndex, line.rateIndex, line.TrafficTimeBinsIndex, 
                             line.pickup_hour, line.weekday, line.passenger_count, line.trip_time_in_secs, 
                             line.trip_distance, line.fare_amount])

        labPt = LabeledPoint(line.tip_amount, features)
        return  labPt
    
    # INDEXING CATEGORICAL TEXT FEATURES FOR INPUT INTO LINEAR REGRESSION MODELS
    def parseRowOneHotRegression(line):
        features = np.concatenate((np.array([line.pickup_hour, line.weekday, line.passenger_count,
                                            line.trip_time_in_secs, line.trip_distance, line.fare_amount]), 
                                            line.vendorVec.toArray(), line.rateVec.toArray(), 
                                            line.paymentVec.toArray(), line.TrafficTimeBinsVec.toArray()), axis=0)
        labPt = LabeledPoint(line.tip_amount, features)
        return  labPt


### <a name="create-a-random-sub-sampling-of-the-data-and-split-it-into-training-and-testing-sets"></a>Crear un sondeo subcarpetas de los datos y dividir en formación y conjuntos de pruebas

Este código crea un sondeo de los datos (se usa 25%). Aunque no es necesario para este ejemplo por el tamaño del conjunto de datos, mostramos cómo puede muestrear aquí para saber cómo usarla para su propio problema cuando sea necesario. Cuando las muestras están grandes, esto puede ahorrar tiempo significativo modelos de aprendizaje. A continuación se dividen los datos de ejemplo en un elemento de formación (75% aquí) y un elemento de pruebas (25% aquí) para usar en clasificación y modelado de regresión.


    # RECORD START TIME
    timestart = datetime.datetime.now()
    
    # LOAD PYSPARK LIBRARIES
    from pyspark.sql.functions import rand

    # SPECIFY SAMPLING AND SPLITTING FRACTIONS
    samplingFraction = 0.25;
    trainingFraction = 0.75; testingFraction = (1-trainingFraction);
    seed = 1234;
    encodedFinalSampled = encodedFinal.sample(False, samplingFraction, seed=seed)
    
    # SPLIT SAMPLED DATA-FRAME INTO TRAIN/TEST
    # INCLUDE RAND COLUMN FOR CREATING CROSS-VALIDATION FOLDS (FOR USE LATER IN AN ADVANCED TOPIC)
    dfTmpRand = encodedFinalSampled.select("*", rand(0).alias("rand"));
    trainData, testData = dfTmpRand.randomSplit([trainingFraction, testingFraction], seed=seed);
    
    # FOR BINARY CLASSIFICATION TRAINING AND TESTING
    indexedTRAINbinary = trainData.map(parseRowIndexingBinary)
    indexedTESTbinary = testData.map(parseRowIndexingBinary)
    oneHotTRAINbinary = trainData.map(parseRowOneHotBinary)
    oneHotTESTbinary = testData.map(parseRowOneHotBinary)
    
    # FOR REGRESSION TRAINING AND TESTING
    indexedTRAINreg = trainData.map(parseRowIndexingRegression)
    indexedTESTreg = testData.map(parseRowIndexingRegression)
    oneHotTRAINreg = trainData.map(parseRowOneHotRegression)
    oneHotTESTreg = testData.map(parseRowOneHotRegression)
    
    # PRINT ELAPSED TIME
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 

**RESULTADO:**

Tiempo necesario para ejecutar encima de celda: 0,24 segundos


### <a name="feature-scaling"></a>Característica de ajuste de escala

Característica de ajuste de escala, también se conoce como normalización de datos, garantiza que funciones con valores muy dispersos son no dada excesivo valorar en función de objetivos. El código de escalado de características utiliza el [StandardScaler](https://spark.apache.org/docs/latest/api/python/pyspark.mllib.html#pyspark.mllib.feature.StandardScaler) para ajustar el tamaño de las características de variación de unidad. Se proporciona por MLlib para su uso en con estocástico degradado descenso (SGD), un algoritmo popular para una amplia gama de otro equipo modelos como retrocesos regularice o soporte técnico vector máquinas (SVM) de aprendizaje de formación de regresión lineal.

>[AZURE.NOTE] Hemos encontrado el algoritmo de LinearRegressionWithSGD ser confidencial escalado de características.

Este es el código a escala variables para usarla con el algoritmo SGD regularized lineal.

    # FEATURE SCALING

    # RECORD START TIME
    timestart = datetime.datetime.now()

    # LOAD PYSPARK LIBRARIES
    from pyspark.mllib.regression import LabeledPoint
    from pyspark.mllib.linalg import Vectors
    from pyspark.mllib.feature import StandardScaler, StandardScalerModel
    from pyspark.mllib.util import MLUtils
    
    # SCALE VARIABLES FOR REGULARIZED LINEAR SGD ALGORITHM
    label = oneHotTRAINreg.map(lambda x: x.label)
    features = oneHotTRAINreg.map(lambda x: x.features)
    scaler = StandardScaler(withMean=False, withStd=True).fit(features)
    dataTMP = label.zip(scaler.transform(features.map(lambda x: Vectors.dense(x.toArray()))))
    oneHotTRAINregScaled = dataTMP.map(lambda x: LabeledPoint(x[0], x[1]))
    
    label = oneHotTESTreg.map(lambda x: x.label)
    features = oneHotTESTreg.map(lambda x: x.features)
    scaler = StandardScaler(withMean=False, withStd=True).fit(features)
    dataTMP = label.zip(scaler.transform(features.map(lambda x: Vectors.dense(x.toArray()))))
    oneHotTESTregScaled = dataTMP.map(lambda x: LabeledPoint(x[0], x[1]))
    
    # PRINT ELAPSED TIME
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 

**RESULTADO:**

Tiempo necesario para ejecutar encima de celda: 13.17 segundos


### <a name="cache-objects-in-memory"></a>Objetos de caché en memoria

Almacenando el marco de entrada de datos objetos usan para clasificación de regresión y las características de escala, se puede reducir el tiempo de aprendizaje y la comprobación de algoritmos m.

    # RECORD START TIME
    timestart = datetime.datetime.now()
    
    # FOR BINARY CLASSIFICATION TRAINING AND TESTING
    indexedTRAINbinary.cache()
    indexedTESTbinary.cache()
    oneHotTRAINbinary.cache()
    oneHotTESTbinary.cache()
    
    # FOR REGRESSION TRAINING AND TESTING
    indexedTRAINreg.cache()
    indexedTESTreg.cache()
    oneHotTRAINreg.cache()
    oneHotTESTreg.cache()
    
    # SCALED FEATURES
    oneHotTRAINregScaled.cache()
    oneHotTESTregScaled.cache()
    
    # PRINT ELAPSED TIME
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 

**RESULTADO:** 

Tiempo necesario para ejecutar encima de celda: 0,15 segundos


## <a name="predict-whether-or-not-a-tip-is-paid-with-binary-classification-models"></a>Predecir si no se paga una sugerencia con los modelos de clasificación binario

Esta sección muestra cómo usar tres modelos para la tarea de clasificación binario de predecir si no se paga una sugerencia para un viaje taxi. Los modelos presentarlos son:

- Regresión logística regularice 
- Modelo de bosque aleatorios
- Árboles de aumentar degradados

Cada modelo de creación de la sección de código se divide en los pasos: 

1. **Formación de modelo** de datos con un conjunto de parámetros
2. **Evaluación de modelo** en un conjunto de datos de prueba con métricas
3. **Guardar modelo** blobs de consumo futuro

### <a name="classification-using-logistic-regression"></a>Clasificación utilizando regresión logística

El código de esta sección muestra cómo entrenar, evaluar y guardar un modelo de regresión logística con [LBFGS](https://en.wikipedia.org/wiki/Broyden%E2%80%93Fletcher%E2%80%93Goldfarb%E2%80%93Shanno_algorithm) que prevé si no se paga una sugerencia para un viaje de la ciudad de Nueva York taxi viaje y tarifa conjunto de datos.

**Entrenar el modelo de regresión logística con VC y barrido hyperparameter**

    # LOGISTIC REGRESSION CLASSIFICATION WITH CV AND HYPERPARAMETER SWEEPING

    # GET ACCURACY FOR HYPERPARAMETERS BASED ON CROSS-VALIDATION IN TRAINING DATA-SET

    # RECORD START TIME
    timestart = datetime.datetime.now()
    
    # LOAD LIBRARIES
    from pyspark.mllib.classification import LogisticRegressionWithLBFGS 
    from sklearn.metrics import roc_curve,auc
    from pyspark.mllib.evaluation import BinaryClassificationMetrics
    from pyspark.mllib.evaluation import MulticlassMetrics
    
    
    # CREATE MODEL WITH ONE SET OF PARAMETERS
    logitModel = LogisticRegressionWithLBFGS.train(oneHotTRAINbinary, iterations=20, initialWeights=None, 
                                                   regParam=0.01, regType='l2', intercept=True, corrections=10, 
                                                   tolerance=0.0001, validateData=True, numClasses=2)
    
    # PRINT COEFFICIENTS AND INTERCEPT OF THE MODEL
    # NOTE: There are 20 coefficient terms for the 10 features, 
    #       and the different categories for features: vendorVec (2), rateVec, paymentVec (6), TrafficTimeBinsVec (4)
    print("Coefficients: " + str(logitModel.weights))
    print("Intercept: " + str(logitModel.intercept))
    
    # PRINT ELAPSED TIME
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 


**RESULTADO:** 

Coeficientes: [0.0082065285375,-0.0223675576104,-0.0183812028036, - 3.48124578069e-05,-0.00247646947233,-0.00165897881503, 0.0675394837328,-0.111823113101,-0.324609912762,-0.204549780032,-1.36499216354, 0.591088507921,-0.664263411392,-1.00439726852, 3.46567827545,-3.51025855172,-0.0471341112232,-0.043521833294, 0.000243375810385, 0.054518719222]

Intersección:-0.0111216486893

Tiempo necesario para ejecutar encima de celda: 14.43 segundos

**Evaluar el modelo de clasificación binario con métricas estándares**

    #EVALUATE LOGISTIC REGRESSION MODEL WITH LBFGS

    # RECORD START TIME
    timestart = datetime.datetime.now()

    # PREDICT ON TEST DATA WITH MODEL
    predictionAndLabels = oneHotTESTbinary.map(lambda lp: (float(logitModel.predict(lp.features)), lp.label))
    
    # INSTANTIATE METRICS OBJECT
    metrics = BinaryClassificationMetrics(predictionAndLabels)

    # AREA UNDER PRECISION-RECALL CURVE
    print("Area under PR = %s" % metrics.areaUnderPR)

    # AREA UNDER ROC CURVE
    print("Area under ROC = %s" % metrics.areaUnderROC)
    metrics = MulticlassMetrics(predictionAndLabels)

    # OVERALL STATISTICS
    precision = metrics.precision()
    recall = metrics.recall()
    f1Score = metrics.fMeasure()
    print("Summary Stats")
    print("Precision = %s" % precision)
    print("Recall = %s" % recall)
    print("F1 Score = %s" % f1Score)


    ## SAVE MODEL WITH DATE-STAMP
    datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
    logisticregressionfilename = "LogisticRegressionWithLBFGS_" + datestamp;
    dirfilename = modelDir + logisticregressionfilename;
    logitModel.save(sc, dirfilename);
    
    # OUTPUT PROBABILITIES AND REGISTER TEMP TABLE
    logitModel.clearThreshold(); # This clears threshold for classification (0.5) and outputs probabilities
    predictionAndLabelsDF = predictionAndLabels.toDF()
    predictionAndLabelsDF.registerTempTable("tmp_results");
    
    # PRINT ELAPSED TIME
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds";

**RESULTADO:** 

Área de PR = 0.985297691373

Área de ROC = 0.983714670256

Estadísticas de resumen

Precisión = 0.984304060189

Recuperar = 0.984304060189

F1 Puntuación = 0.984304060189

Tiempo necesario para ejecutar encima de celda: 57.61 segundos

**Trazar la curva ROC.**

El *predictionAndLabelsDF* está registrado como una tabla, *tmp_results*, en la celda anterior. *tmp_results* puede utilizarse para hacer consultas y resultados de salida en el marco de datos sqlResults para representar el gráfico. Este es el código.


    # QUERY RESULTS                              
    %%sql -q -o sqlResults
    SELECT * from tmp_results


Este es el código para realizar predicciones y trazar la curva de ROC.

    # MAKE PREDICTIONS AND PLOT ROC-CURVE

    # RUN THE CODE LOCALLY ON THE JUPYTER SERVER AND IMPORT LIBRARIES
    %%local
    %matplotlib inline
    from sklearn.metrics import roc_curve,auc

    # MAKE PREDICTIONS
    predictions_pddf = test_predictions.rename(columns={'_1': 'probability', '_2': 'label'})
    prob = predictions_pddf["probability"] 
    fpr, tpr, thresholds = roc_curve(predictions_pddf['label'], prob, pos_label=1);
    roc_auc = auc(fpr, tpr)

    # PLOT ROC CURVE
    plt.figure(figsize=(5,5))
    plt.plot(fpr, tpr, label='ROC curve (area = %0.2f)' % roc_auc)
    plt.plot([0, 1], [0, 1], 'k--')
    plt.xlim([0.0, 1.0])
    plt.ylim([0.0, 1.05])
    plt.xlabel('False Positive Rate')
    plt.ylabel('True Positive Rate')
    plt.title('ROC Curve')
    plt.legend(loc="lower right")
    plt.show()
    

**RESULTADO:**

![Regresión logística ROC curve.png](./media/machine-learning-data-science-spark-data-exploration-modeling/logistic-regression-roc-curve.png)


### <a name="random-forest-classification"></a>Clasificación de bosque aleatorios

El código de esta sección muestra cómo entrenar, evaluar y guardar un modelo de bosque aleatorio que prevé si no se paga una sugerencia para un viaje de la ciudad de Nueva York taxi viaje y tarifa conjunto de datos.
    
    #PREDICT WHETHER A TIP IS PAID OR NOT USING RANDOM FOREST

    # RECORD START TIME
    timestart = datetime.datetime.now()
    
    # LOAD PYSPARK LIBRARIES
    from pyspark.mllib.tree import RandomForest, RandomForestModel
    from pyspark.mllib.util import MLUtils
    from pyspark.mllib.evaluation import BinaryClassificationMetrics
    from pyspark.mllib.evaluation import MulticlassMetrics
    
    # SPECIFY NUMBER OF CATEGORIES FOR CATEGORICAL FEATURES. FEATURE #0 HAS 2 CATEGORIES, FEATURE #2 HAS 2 CATEGORIES, AND SO ON
    categoricalFeaturesInfo={0:2, 1:2, 2:6, 3:4}
    
    # TRAIN RANDOMFOREST MODEL
    rfModel = RandomForest.trainClassifier(indexedTRAINbinary, numClasses=2, 
                                           categoricalFeaturesInfo=categoricalFeaturesInfo,
                                           numTrees=25, featureSubsetStrategy="auto",
                                           impurity='gini', maxDepth=5, maxBins=32)
    ## UN-COMMENT IF YOU WANT TO PRINT TREES
    #print('Learned classification forest model:')
    #print(rfModel.toDebugString())
    
    # PREDICT ON TEST DATA AND EVALUATE
    predictions = rfModel.predict(indexedTESTbinary.map(lambda x: x.features))
    predictionAndLabels = indexedTESTbinary.map(lambda lp: lp.label).zip(predictions)
    
    # AREA UNDER ROC CURVE
    metrics = BinaryClassificationMetrics(predictionAndLabels)
    print("Area under ROC = %s" % metrics.areaUnderROC)
    
    # PERSIST MODEL IN BLOB
    datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
    rfclassificationfilename = "RandomForestClassification_" + datestamp;
    dirfilename = modelDir + rfclassificationfilename;
    
    rfModel.save(sc, dirfilename);
    
    # PRINT ELAPSED TIME
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 

**RESULTADO:**

Área de ROC = 0.985297691373

Tiempo necesario para ejecutar encima de celda: 31.09 segundos


### <a name="gradient-boosting-trees-classification"></a>Clasificación de árboles Aumentar degradado

El código de esta sección muestra cómo entrenar, evaluar y guardar un modelo de árboles de aumentar degradado predicciones si no se paga una sugerencia para un viaje de ida y vuelta el taxi Nueva York y resultados son el conjunto de datos.

    #PREDICT WHETHER A TIP IS PAID OR NOT USING GRADIENT BOOSTING TREES

    # RECORD START TIME
    timestart = datetime.datetime.now()
    
    # LOAD PYSPARK LIBRARIES
    from pyspark.mllib.tree import GradientBoostedTrees, GradientBoostedTreesModel
    
    # SPECIFY NUMBER OF CATEGORIES FOR CATEGORICAL FEATURES. FEATURE #0 HAS 2 CATEGORIES, FEATURE #2 HAS 2 CATEGORIES, AND SO ON
    categoricalFeaturesInfo={0:2, 1:2, 2:6, 3:4}
    
    gbtModel = GradientBoostedTrees.trainClassifier(indexedTRAINbinary, categoricalFeaturesInfo=categoricalFeaturesInfo, numIterations=5)
    ## UNCOMMENT IF YOU WANT TO PRINT TREE DETAILS
    #print('Learned classification GBT model:')
    #print(bgtModel.toDebugString())
    
    # PREDICT ON TEST DATA AND EVALUATE
    predictions = gbtModel.predict(indexedTESTbinary.map(lambda x: x.features))
    predictionAndLabels = indexedTESTbinary.map(lambda lp: lp.label).zip(predictions)
    
    # AREA UNDER ROC CURVE
    metrics = BinaryClassificationMetrics(predictionAndLabels)
    print("Area under ROC = %s" % metrics.areaUnderROC)
    
    # PERSIST MODEL IN A BLOB
    datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
    btclassificationfilename = "GradientBoostingTreeClassification_" + datestamp;
    dirfilename = modelDir + btclassificationfilename;
    
    gbtModel.save(sc, dirfilename)
    
    # PRINT ELAPSED TIME
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 


**RESULTADO:**

Área de ROC = 0.985297691373

Tiempo necesario para ejecutar encima de celda: 19.76 segundos


## <a name="predict-tip-amounts-for-taxi-trips-with-regression-models"></a>Predecir las cantidades de sugerencia para viajes taxi con los modelos de regresión.

Esta sección muestra cómo tres modelos de uso de la tarea de regresión de predecir la cantidad de la sugerencia de pago para un viaje de taxi en función de otras características de sugerencia. Los modelos presentarlos son:

- Regularice regresión lineal
- Aleatorio bosque
- Árboles de aumentar degradados

Estos modelos se ha descrito en la introducción. Cada modelo de creación de la sección de código se divide en los pasos: 

1. **Formación de modelo** de datos con un conjunto de parámetros
2. **Evaluación de modelo** en un conjunto de datos de prueba con métricas
3. **Guardar modelo** blobs de consumo futuro

### <a name="linear-regression-with-sgd"></a>Regresión lineal con SGD 

El código de esta sección muestra cómo usar las características de escala para formar una regresión lineal que usa descenso degradado estocástico (SGD) para la optimización y cómo puntuación, evaluar y a continuación, guarde el modelo de almacenamiento de blobs de Windows Azure (WASB).

>[AZURE.TIP] En nuestra experiencia, puede haber problemas con la convergencia de modelos LinearRegressionWithSGD y parámetros necesitan ser cambiado/optimizados cuidadosamente para obtener un modelo válido. Ajuste de escala de variables significativamente ayuda con convergencia. 


    #PREDICT TIP AMOUNTS USING LINEAR REGRESSION WITH SGD

    # RECORD START TIME
    timestart = datetime.datetime.now()
    
    # LOAD LIBRARIES
    from pyspark.mllib.regression import LabeledPoint, LinearRegressionWithSGD, LinearRegressionModel
    from pyspark.mllib.evaluation import RegressionMetrics
    from scipy import stats
    
    # USE SCALED FEATURES TO TRAIN MODEL
    linearModel = LinearRegressionWithSGD.train(oneHotTRAINregScaled, iterations=100, step = 0.1, regType='l2', regParam=0.1, intercept = True)

    # PRINT COEFFICIENTS AND INTERCEPT OF THE MODEL
    # NOTE: There are 20 coefficient terms for the 10 features, 
    #       and the different categories for features: vendorVec (2), rateVec, paymentVec (6), TrafficTimeBinsVec (4)
    print("Coefficients: " + str(linearModel.weights))
    print("Intercept: " + str(linearModel.intercept))
    
    # SCORE ON SCALED TEST DATA-SET & EVALUATE
    predictionAndLabels = oneHotTESTregScaled.map(lambda lp: (float(linearModel.predict(lp.features)), lp.label))
    testMetrics = RegressionMetrics(predictionAndLabels)
    
    # PRINT TEST METRICS
    print("RMSE = %s" % testMetrics.rootMeanSquaredError)
    print("R-sqr = %s" % testMetrics.r2)
    
    # SAVE MODEL WITH DATE-STAMP IN THE DEFAULT BLOB FOR THE CLUSTER
    datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
    linearregressionfilename = "LinearRegressionWithSGD_" + datestamp;
    dirfilename = modelDir + linearregressionfilename;
    
    linearModel.save(sc, dirfilename)
    
    # PRINT ELAPSED TIME
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 

**RESULTADO:**

Coeficientes: [0.00457675809917,-0.0226314167349,-0.0191910355236, 0.246793409578, 0.312047890459, 0.359634405999, 0.00928692253981,-0.000987181489428,-0.0888306617845, 0.0569376211553, 0.115519551711, 0.149250164995,-0.00990211159703,-0.00637410344522, 0.545083566179,-0.536756072402, 0.0105762393099,-0.0130117577055, 0.0129304737772,-0.00171065945959]

Interceptar: 0.853872718283

MÉTRICA ECM = 1.24190115863

Sqr R = 0.608017146081

Tiempo necesario para ejecutar encima de celda: 58,42 segundos


### <a name="random-forest-regression"></a>Regresión del bosque aleatoria

El código de esta sección muestra cómo entrenar, evaluar y guardar una regresión del bosque aleatorio que prevé la cantidad de sugerencia para los datos de viaje de Nueva York taxi.


    #PREDICT TIP AMOUNTS USING RANDOM FOREST

    # RECORD START TIME
    timestart= datetime.datetime.now()

    # LOAD PYSPARK LIBRARIES
    from pyspark.mllib.tree import RandomForest, RandomForestModel
    from pyspark.mllib.util import MLUtils
    from pyspark.mllib.evaluation import RegressionMetrics
    
    
    ## TRAIN MODEL
    categoricalFeaturesInfo={0:2, 1:2, 2:6, 3:4}
    rfModel = RandomForest.trainRegressor(indexedTRAINreg, categoricalFeaturesInfo=categoricalFeaturesInfo,
                                        numTrees=25, featureSubsetStrategy="auto",
                                        impurity='variance', maxDepth=10, maxBins=32)
    ## UN-COMMENT IF YOU WANT TO PRING TREES
    #print('Learned classification forest model:')
    #print(rfModel.toDebugString())
    
    ## PREDICT AND EVALUATE ON TEST DATA-SET
    predictions = rfModel.predict(indexedTESTreg.map(lambda x: x.features))
    predictionAndLabels = oneHotTESTreg.map(lambda lp: lp.label).zip(predictions)

    # TEST METRICS
    testMetrics = RegressionMetrics(predictionAndLabels)
    print("RMSE = %s" % testMetrics.rootMeanSquaredError)
    print("R-sqr = %s" % testMetrics.r2)
    
    # SAVE MODEL IN BLOB
    datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
    rfregressionfilename = "RandomForestRegression_" + datestamp;
    dirfilename = modelDir + rfregressionfilename;
    
    rfModel.save(sc, dirfilename);
    
    # PRINT ELAPSED TIME
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 

**RESULTADO:**

MÉTRICA ECM = 0.891209218139

Sqr R = 0.759661334921

Tiempo necesario para ejecutar encima de celda: 49.21 segundos


### <a name="gradient-boosting-trees-regression"></a>Degradado regresión árboles aumentar

El código de esta sección muestra cómo entrenar, evaluar y guardar un modelo de árboles de aumentar degradado predicciones cantidad de sugerencia para los datos de viaje de taxi de Nueva York.

**Entrenar y evaluar**

    #PREDICT TIP AMOUNTS USING GRADIENT BOOSTING TREES

    # RECORD START TIME
    timestart= datetime.datetime.now()
    
    # LOAD PYSPARK LIBRARIES
    from pyspark.mllib.tree import GradientBoostedTrees, GradientBoostedTreesModel
    from pyspark.mllib.util import MLUtils
    
    ## TRAIN MODEL
    categoricalFeaturesInfo={0:2, 1:2, 2:6, 3:4}
    gbtModel = GradientBoostedTrees.trainRegressor(indexedTRAINreg, categoricalFeaturesInfo=categoricalFeaturesInfo, 
                                                    numIterations=10, maxBins=32, maxDepth = 4, learningRate=0.1)
    
    ## EVALUATE A TEST DATA-SET
    predictions = gbtModel.predict(indexedTESTreg.map(lambda x: x.features))
    predictionAndLabels = indexedTESTreg.map(lambda lp: lp.label).zip(predictions)

    # TEST METRICS
    testMetrics = RegressionMetrics(predictionAndLabels)
    print("RMSE = %s" % testMetrics.rootMeanSquaredError)
    print("R-sqr = %s" % testMetrics.r2)

    # SAVE MODEL IN BLOB
    datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
    btregressionfilename = "GradientBoostingTreeRegression_" + datestamp;
    dirfilename = modelDir + btregressionfilename;
    gbtModel.save(sc, dirfilename)
    
    # CONVER RESULTS TO DF AND REGISER TEMP TABLE
    test_predictions = sqlContext.createDataFrame(predictionAndLabels)
    test_predictions.registerTempTable("tmp_results");
    
    # PRINT ELAPSED TIME
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 

**RESULTADO:**

MÉTRICA ECM = 0.908473148639

Sqr R = 0.753835096681

Tiempo necesario para ejecutar encima de celda: 34.52 segundos

**Trazado**

*tmp_results* está registrado como una tabla subárbol en la celda anterior. Resultados de la tabla son resultado en el marco de datos *sqlResults* para representar el gráfico. Este es el código

    # PLOT SCATTER-PLOT BETWEEN ACTUAL AND PREDICTED TIP VALUES

    # SELECT RESULTS
    %%sql -q -o sqlResults
    SELECT * from tmp_results

Este es el código para trazar los datos con el servidor Jupyter.

    # RUN THE CODE LOCALLY ON THE JUPYTER SERVER AND IMPORT LIBRARIES
    %%local
    %matplotlib inline
    import numpy as np

    # PLOT 
    ax = test_predictions_pddf.plot(kind='scatter', figsize = (6,6), x='_1', y='_2', color='blue', alpha = 0.25, label='Actual vs. predicted');
    fit = np.polyfit(test_predictions_pddf['_1'], test_predictions_pddf['_2'], deg=1)
    ax.set_title('Actual vs. Predicted Tip Amounts ($)')
    ax.set_xlabel("Actual")
    ax.set_ylabel("Predicted")
    ax.plot(test_predictions_pddf['_1'], fit[0] * test_predictions_pddf['_1'] + fit[1], color='magenta')
    plt.axis([-1, 20, -1, 20])
    plt.show(ax)
    

**RESULTADO:**

![Vs-pronosticados-sugerencia-importes reales](./media/machine-learning-data-science-spark-data-exploration-modeling/actual-vs-predicted-tips.png)

    
## <a name="clean-up-objects-from-memory"></a>Limpiar los objetos de la memoria

Usar `unpersist()` para eliminar los objetos en la memoria caché.
        
    # REMOVE ORIGINAL DFs
    taxi_df_train_cleaned.unpersist()
    taxi_df_train_with_newFeatures.unpersist()
    
    # FOR BINARY CLASSIFICATION TRAINING AND TESTING
    indexedTRAINbinary.unpersist()
    indexedTESTbinary.unpersist()
    oneHotTRAINbinary.unpersist()
    oneHotTESTbinary.unpersist()
    
    # FOR REGRESSION TRAINING AND TESTING
    indexedTRAINreg.unpersist()
    indexedTESTreg.unpersist()
    oneHotTRAINreg.unpersist()
    oneHotTESTreg.unpersist()
    
    # SCALED FEATURES
    oneHotTRAINregScaled.unpersist()
    oneHotTESTregScaled.unpersist()


## <a name="record-storage-locations-of-the-models-for-consumption-and-scoring"></a>Ubicaciones de almacenamiento de registro de los modelos de consumo y puntuación

Consumir y la puntuación de un conjunto de datos independiente que se describe en el [puntuación y evaluar modelos de aprendizaje integrado motor](machine-learning-data-science-spark-model-consumption.md) tema, debe copiar y pegar estos nombres de archivo que contiene los modelos de guardado aquí creados en el Bloc de notas de consumo Jupyter. Este es el código para imprimir las rutas de acceso a archivos de modelo que necesita allí.

    # MODEL FILE LOCATIONS FOR CONSUMPTION
    print "logisticRegFileLoc = modelDir + \"" + logisticregressionfilename + "\"";
    print "linearRegFileLoc = modelDir + \"" + linearregressionfilename + "\"";
    print "randomForestClassificationFileLoc = modelDir + \"" + rfclassificationfilename + "\"";
    print "randomForestRegFileLoc = modelDir + \"" + rfregressionfilename + "\"";
    print "BoostedTreeClassificationFileLoc = modelDir + \"" + btclassificationfilename + "\"";
    print "BoostedTreeRegressionFileLoc = modelDir + \"" + btregressionfilename + "\"";


**SALIDA**

logisticRegFileLoc = modelDir + "LogisticRegressionWithLBFGS_2016-05-0317_03_23.516568"

linearRegFileLoc = modelDir + "LinearRegressionWithSGD_2016-05-0317_05_21.577773"

randomForestClassificationFileLoc = modelDir + "RandomForestClassification_2016-05-0317_04_11.950206"

randomForestRegFileLoc = modelDir + "RandomForestRegression_2016-05-0317_06_08.723736"

BoostedTreeClassificationFileLoc = modelDir + "GradientBoostingTreeClassification_2016-05-0317_04_36.346583"

BoostedTreeRegressionFileLoc = modelDir + "GradientBoostingTreeRegression_2016-05-0317_06_51.737282"


## <a name="whats-next"></a>¿Qué es el siguiente?

Ahora que ha creado modelos de clasificación y de regresión con el MlLib motor, ya está listo para aprender a la puntuación y evaluar estos modelos. La exploración avanzada de datos y modelado del Bloc de notas profundiza más profundos incluidos validación cruzada, hyper parámetro barrido y del modelo de evaluación. 

**Consumo de modelo:** Para obtener información sobre cómo puntuación y evaluar los modelos de clasificación y regresión creados en este tema, consulte [puntuación y evaluar modelos de aprendizaje integrado motor máquina](machine-learning-data-science-spark-model-consumption.md).

**Validación cruzada y hyperparameter barrido**: vea [la exploración de datos y modelado de motor avanzadas](machine-learning-data-science-spark-advanced-data-exploration-modeling.md) sobre cómo pueden ser modelos capacitado mediante validación cruzada y hyper parámetro barrido



