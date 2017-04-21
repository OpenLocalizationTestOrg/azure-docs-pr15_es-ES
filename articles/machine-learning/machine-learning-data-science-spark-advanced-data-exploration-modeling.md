<properties
    pageTitle="Exploración de datos y modelado de motor avanzadas | Microsoft Azure"
    description="Usar HDInsight Spark para realizar la exploración de datos y entrenar modelos de clasificación y regresión binarios con optimización de validación cruzada y hyperparameter."
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
    ms.date="10/07/2016"
    ms.author="deguhath;bradsev;gokuma" />

# <a name="advanced-data-exploration-and-modeling-with-spark"></a>Exploración avanzada de datos y modelado de motor 

[AZURE.INCLUDE [machine-learning-spark-modeling](../../includes/machine-learning-spark-modeling.md)]

Este tutorial usa HDInsight Spark hacer la exploración de datos y entrenar clasificación binario y los modelos de regresión con validación cruzada y optimización de hyperparameter de una muestra de la ciudad de Nueva York taxi viaje y resultados son el conjunto de datos de 2013. Le guiará por los pasos del [Proceso de ciencias de datos](http://aka.ms/datascienceprocess), llevar a cabo con un HDInsight Spark de clúster para el procesamiento y blobs de Azure para almacenar los datos y los modelos. El proceso explora y visualiza los datos que se ha insertado de un Blob de almacenamiento de Azure y, a continuación, prepara los datos para crear modelos de predicción. Se ha usado Python para la solución de código y mostrar la trazados relevantes. Estos modelos son generación utilizando el Kit de herramientas de motor MLlib hacer regresión y clasificación binario tareas de modelado. 

- La tarea de **clasificación binario** es predecir si no se paga una sugerencia para el viaje. 
- La tarea de **regresión** es predecir la cantidad de la sugerencia en función de otras características de sugerencia. 

Los pasos de modelado también contienen código que muestra cómo entrenar, evaluar y a continuación, guarde cada tipo de modelo. El tema trata sobre algunos de los mismos conceptos básicos como el tema [exploración de datos y modelado de motor](machine-learning-data-science-spark-data-exploration-modeling.md) . Pero más "avanzadas" que también utiliza validación cruzada junto con hyperparameter barrido para entrenar modelos de clasificación y regresión óptima precisos. 

**Validación cruzada (VC)** es una técnica que se evalúa bien un modelo de formación en un conjunto de datos conocido generaliza para predecir las características de los conjuntos de datos en el que se ha no se ha formado. La idea general de esta técnica es que está capacitado un modelo de datos conocidos en un conjunto de datos y, a continuación, en que se comprueba la precisión de sus predicciones con un conjunto de datos independiente. Una implementación común utilizada aquí es dividir un conjunto de datos en pliegue K y, a continuación, entrenar el modelo en turnos en todos excepto uno de lo pliegue. 

**Optimización de Hyperparameter** es el problema de elegir un conjunto de hyperparameters para un algoritmo de aprendizaje, normalmente con el objetivo de optimizar una medida de rendimiento del algoritmo en un conjunto de datos independiente. **Hyperparameters** son valores que se deben especificar fuera el procedimiento de aprendizaje de modelo. Suposiciones sobre estos valores pueden afectar a la flexibilidad y la precisión de los modelos. Árboles de decisión tienen hyperparameters, por ejemplo, como la profundidad que desee y el número de hojas en el árbol. Compatibilidad con vectores máquinas (SVMs) requieren el establecimiento de un término de disminución misclassification. 

Una manera de realizar optimización hyperparameter utilizada aquí es una búsqueda de la cuadrícula o un **barrido del parámetro**. Consiste en realizar una búsqueda exhaustiva a través de los valores de un subconjunto del espacio hyperparameter especificado para un algoritmo de aprendizaje. Validación cruzada puede proporcionar una métrica de rendimiento para ordenar los resultados óptimo producidos por el algoritmo de búsqueda de la cuadrícula. VC utilizado con hyperparameter barrido ayuda a limitar problemas como overfitting un modelo de datos de aprendizaje para que el modelo conserva la capacidad de aplicar al conjunto de datos desde el que se extraen los datos de entrenamiento general.

Los modelos que usamos incluyen regresión lineal y logística, aleatorios bosques y árboles aumentados degradados:

- [Regresión lineal con SGD](https://spark.apache.org/docs/latest/api/python/pyspark.mllib.html#pyspark.mllib.regression.LinearRegressionWithSGD) es un modelo de regresión lineal que utiliza un método de descenso degradado estocástico (SGD) y para la optimización y la característica escalar para predecir los importes de sugerencia de pago. 
- Regresión [regresión logística con LBFGS](https://spark.apache.org/docs/latest/api/python/pyspark.mllib.html#pyspark.mllib.classification.LogisticRegressionWithLBFGS) o "logit", es un modelo de regresión pueden utilizarse para que la variable dependiente es por categorías para realizar la clasificación de datos. LBFGS es un algoritmo de optimización de cuasi-Hernández que aproxima el algoritmo de Broyden – Fletcher – Goldfarb-Shanno (BFGS) con una cantidad limitada de memoria del equipo y que se utiliza ampliamente en aprendizaje de equipo.
- [Aleatorio bosques](http://spark.apache.org/docs/latest/mllib-ensembles.html#Random-Forests) son conjuntos de árboles de decisión.  Combinan muchos árboles de decisión para reducir el riesgo de desbordamiento. Aleatorios bosques se usan para regresión y clasificación y pueden controlar características por categorías y pueden ampliarse a la configuración de clasificación multiclass. Que no requieren escalado de características y se pueden capturar los errores de alineación e interacciones de características. Aleatorios bosques son uno de la máquina más éxito modelos de clasificación y regresión de aprendizaje.
- [Degradado aumenta árboles](http://spark.apache.org/docs/latest/ml-classification-regression.html#gradient-boosted-trees-gbts) (GBTs) son conjuntos de árboles de decisión. GBTs entrenar árboles de decisión repetidamente para minimizar una función de pérdida. GBTs se usan para regresión y clasificación puede controlar características por categorías no requieren la característica de ajuste de escala y, puede capturar los errores de alineación e interacciones de características. También se puede utilizar en una configuración de clasificación de multiclass.

Ejemplos de uso VC y Hyperparameter de modelado barrido se muestran para el problema de clasificación binario. Ejemplos más simples (sin el parámetro limpia) se presentan en el tema principal para las tareas de regresión. Pero en el apéndice también se presenta validación mediante net elástico de regresión lineal y VC con el parámetro barrido de regresión del bosque aleatorio. La **red elástico** es un método de regresión regularice para ajustar los modelos de regresión lineal que lineal combina la métrica de nivel 1 y nivel 2 como sanciones de los métodos de [Lazo](https://en.wikipedia.org/wiki/Lasso%20%28statistics%29) y [borde saliente](https://en.wikipedia.org/wiki/Tikhonov_regularization) .   



>[AZURE.NOTE] Aunque el Kit de herramientas de motor MLlib está diseñado para ejecutarse en grandes conjuntos de datos, se utiliza aquí un ejemplo relativamente pequeño (con 170K filas, aproximadamente 0,1% del conjunto de datos de Nueva York original de ~ 30 Mb) para mayor comodidad. El ejercicio aquí se ejecuta eficazmente (en unos 10 minutos) en un clúster de HDInsight con 2 nodos de trabajo. El mismo código, con pequeñas modificaciones, puede usarse para procesar más grandes conjuntos de datos, con las modificaciones apropiadas para el almacenamiento en caché de datos en la memoria y cambiar el tamaño de clúster.


## <a name="prerequisites"></a>Requisitos previos

Necesita una cuenta de Azure y un clúster de necesita una 1,6 de motor 3.4 HDInsight HDInsight motor que para completar este tutorial. Vea la [Información general de ciencias datos mediante el motor en Azure HDInsight](machine-learning-data-science-spark-overview.md) para obtener instrucciones sobre cómo cumplir estos requisitos. Este tema también contiene una descripción de los datos de Nueva York 2013 Taxi utilizados aquí e instrucciones sobre cómo ejecutar el código de un bloc de notas de Jupyter en el clúster de motor. El Bloc de notas **pySpark-machine-learning-data-science-spark-advanced-data-exploration-modeling.ipynb** que contiene los ejemplos de código de este tema está disponible en [Github](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/Spark/pySpark).


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
    modelDir = "wasb:///user/remoteuser/NYCTaxi/Models/";

    # PRINT START TIME
    import datetime
    datetime.datetime.now()

**SALIDA**

DateTime.DateTime (2016, 4, 18, 17, 36, 27, 832799)


### <a name="import-libraries"></a>Bibliotecas de importación

Importar bibliotecas necesarias con el siguiente código:

    # LOAD PYSPARK LIBRARIES
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


## <a name="data-ingestion-from-public-blob"></a>Recopilación de datos de blob público: 

El primer paso en el proceso de ciencias datos es recopilar los datos de análisis de orígenes donde reside en su entorno de modelado y la exploración de datos. Este entorno es el motor en este tutorial. Esta sección contiene el código para completar una serie de tareas:

- Recopile la muestra datos modelar
- leer en el conjunto de datos de entrada (almacenado como un archivo .tsv)
- aplicar formato y limpiar los datos
- crear y almacenar en caché objetos (RDDs o tramas de datos) en la memoria
- registrarla como una tabla temp en el contexto de SQL.

Este es el código de recopilación de datos.


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
    
    # CACHE & MATERIALIZE DATA-FRAME IN MEMORY. GOING THROUGH AND COUNTING NUMBER OF ROWS MATERIALIZES THE DATA-FRAME IN MEMORY
    taxi_df_train_cleaned.cache()
    taxi_df_train_cleaned.count()
    
    # REGISTER DATA-FRAME AS A TEMP-TABLE IN SQL-CONTEXT
    taxi_df_train_cleaned.registerTempTable("taxi_train")
    
    # PRINT HOW MUCH TIME IT TOOK TO RUN THE CELL
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 


**SALIDA**

Tiempo necesario para ejecutar encima de celda: 276.62 segundos


## <a name="data-exploration--visualization"></a>Visualización y exploración de datos 

Una vez que los datos se ha conectado a motor, es el siguiente paso del proceso de ciencias de datos obtener un conocimiento más profundo de los datos a través de exploración y visualización. En esta sección, nos examinar los datos de taxi mediante consultas SQL y trazar las variables de destino y las características posibles para inspección visual. Más concretamente, nos trazar la frecuencia de recuentos de pasajeros en taxi viajes, la frecuencia de importes de sugerencia y cómo sugerencias varían según el tipo y la cantidad de pago.

### <a name="plot-a-histogram-of-passenger-count-frequencies-in-the-sample-of-taxi-trips"></a>Trazar un histograma de frecuencias de recuento de pasajeros en la muestra de viajes de taxi

Este código y fragmentos posteriores use mágico SQL para consultar la muestra y mágico local para trazar los datos.

- **Mágico SQL (`%%sql`)** El núcleo HDInsight PySpark admite consultas de HiveQL de fácil incorporada en el sqlContext. La (-o nombre_variable) argumento conserva el resultado de la consulta SQL como un DataFrame Pandas en el servidor Jupyter. Esto significa que está disponible en modo local.
- La ** `%%local` mágico** se usa para ejecutar el código localmente en el servidor Jupyter, que es el headnode del clúster HDInsight. Normalmente, se utiliza `%%local` mágico junto con la `%%sql` mágico con parámetro -o. El parámetro -o desea conservar el resultado de la consulta SQL localmente y, a continuación, %% mágico local activaría el siguiente conjunto de fragmento de código para ejecutar localmente en el resultado de las consultas SQL que se almacena localmente

El resultado se visualiza automáticamente después de ejecutar el código.

Esta consulta recupera los viajes por el número de pasajeros. 

    # PLOT FREQUENCY OF PASSENGER COUNTS IN TAXI TRIPS

    # SQL QUERY
    %%sql -q -o sqlResults
    SELECT passenger_count, COUNT(*) as trip_counts FROM taxi_train WHERE passenger_count > 0 and passenger_count < 7 GROUP BY passenger_count


Este código crea una trama de datos local de resultado de la consulta y traza los datos. La `%%local` mágico crea una trama de datos local, `sqlResults`, que se puede utilizar para representar el gráfico con matplotlib. 

>[AZURE.NOTE] Este mágico PySpark se utiliza varias veces en este tutorial. Si la cantidad de datos es grande, debe ejemplo para crear una trama de datos que puede caber en la memoria local.


    # RUN THE CODE LOCALLY ON THE JUPYTER SERVER
    %%local
    
    # USE THE JUPYTER AUTO-PLOTTING FEATURE TO CREATE INTERACTIVE FIGURES. 
    # CLICK ON THE TYPE OF PLOT TO BE GENERATED (E.G. LINE, AREA, BAR ETC.)
    sqlResults

Este es el código para trazar la viajes por recuentos de pasajeros

    # RUN THE CODE LOCALLY ON THE JUPYTER SERVER AND IMPORT LIBRARIES
    %%local
    import matplotlib.pyplot as plt
    %matplotlib inline
    
    # PLOT PASSENGER NUMBER VS TRIP COUNTS
    x_labels = sqlResults['passenger_count'].values
    fig = sqlResults[['trip_counts']].plot(kind='bar', facecolor='lightblue')
    fig.set_xticklabels(x_labels)
    fig.set_title('Counts of trips by passenger count')
    fig.set_xlabel('Passenger count in trips')
    fig.set_ylabel('Trip counts')
    plt.show()

**SALIDA**

![Frecuencia de viajes por el número de pasajeros](./media/machine-learning-data-science-spark-advanced-data-exploration-modeling/frequency-of-trips-by-passenger-count.png)

Puede seleccionar entre diferentes tipos de visualizaciones (tabla, circulares, líneas, área o barra) mediante los botones de menú de **tipo** en el Bloc de notas. Aquí se muestra el trazado de barra.


### <a name="plot-a-histogram-of-tip-amounts-and-how-tip-amount-varies-by-passenger-count-and-fare-amounts"></a>Trazar un histograma de sugerencia cantidades y cómo sugerencia varía por cantidades de contar y tarifa pasajeros.

Usar una consulta SQL para datos de ejemplo...
    
    # SQL SQUERY
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

    # RUN THE CODE LOCALLY ON THE JUPYTER SERVER AND IMPORT LIBRARIES
    %%local
    %matplotlib inline
    
    # TIP BY PAYMENT TYPE AND PASSENGER COUNT
    ax1 = resultsPDDF[['tip_amount']].plot(kind='hist', bins=25, facecolor='lightblue')
    ax1.set_title('Tip amount distribution')
    ax1.set_xlabel('Tip Amount ($)')
    ax1.set_ylabel('Counts')
    plt.suptitle('')
    plt.show()
    
    # TIP BY PASSENGER COUNT
    ax2 = resultsPDDF.boxplot(column=['tip_amount'], by=['passenger_count'])
    ax2.set_title('Tip amount ($) by Passenger count')
    ax2.set_xlabel('Passenger count')
    ax2.set_ylabel('Tip Amount ($)')
    plt.suptitle('')
    plt.show()
    
    # TIP AMOUNT BY FARE AMOUNT, POINTS ARE SCALED BY PASSENGER COUNT
    ax = resultsPDDF.plot(kind='scatter', x= 'fare_amount', y = 'tip_amount', c='blue', alpha = 0.10, s=5*(resultsPDDF.passenger_count))
    ax.set_title('Tip amount by Fare amount ($)')
    ax.set_xlabel('Fare Amount')
    ax.set_ylabel('Tip Amount')
    plt.axis([-2, 120, -2, 30])
    plt.show()
    

**RESULTADO:** 

![Distribución de la cantidad de sugerencia](./media/machine-learning-data-science-spark-advanced-data-exploration-modeling/tip-amount-distribution.png)

![Sugerencia cantidad por número de pasajeros](./media/machine-learning-data-science-spark-advanced-data-exploration-modeling/tip-amount-by-passenger-count.png)

![Sugerencia para cantidad, billete cantidad](./media/machine-learning-data-science-spark-advanced-data-exploration-modeling/tip-amount-by-fare-amount.png)


## <a name="feature-engineering-transformation-and-data-preparation-for-modeling"></a>Preparación de ingeniería, transformación y datos para un modelado de características

Esta sección describe y proporciona el código de los procedimientos que se utilizan para preparar los datos para su uso en el modelado de m. Muestra cómo realizar las siguientes tareas:

- Crear una nueva característica binning horas en depósitos de tiempo de tráfico
- Indizar y en marcha codificar características por categoría
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

**SALIDA**

126050


### <a name="index-and-one-hot-encode-categorical-features"></a>Indizar y caliente uno codificar características por categoría

Esta sección muestra cómo indizar o codificar características por categorías para la entrada en las funciones de modelado. El modelado y predecir las funciones de MLlib requieren características con los datos de entrada por categorías indexado o codificado antes de usarla. 

Dependiendo del modelo, debe indizar o codificar ellos de maneras diferentes. Por ejemplo, modelos Logistic y regresión lineal requieren caliente una codificación, donde, por ejemplo, se puede expandir una característica con tres categorías en tres columnas de característica, con cada contiene 0 o 1, dependiendo de la categoría de una observación. MLlib proporciona la función [OneHotEncoder](http://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.OneHotEncoder.html#sklearn.preprocessing.OneHotEncoder) para realizar caliente una codificación. Este codificador asigna una columna de índices de etiqueta a una columna de vectores binarios, con al menos un solo uno valor. Esta codificación permite algoritmos espera características de valores numéricas, como regresión logística, que se aplique a las características por categoría.

Este es el código de índice y codificar características por categorías:


    # RECORD START TIME
    timestart = datetime.datetime.now()
    
    # LOAD PYSPARK LIBRARIES
    from pyspark.ml.feature import OneHotEncoder, StringIndexer, VectorAssembler, OneHotEncoder, VectorIndexer
    
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


**SALIDA**

Tiempo necesario para ejecutar encima de celda: 3,14 segundos


### <a name="create-labeled-point-objects-for-input-into-ml-functions"></a>Crear objetos etiquetada de punto de entrada en funciones de m

Esta sección contiene código que muestra cómo indizar datos de texto por categoría como un tipo de datos de punto etiquetada y codificar para que se puede utilizar para entrenar y probar regresión logística de MLlib y otros modelos de clasificación. Objetos de punto etiquetada son resistentes distribuido conjuntos de datos (RDD) con formato de manera que la mayoría de los algoritmos m en MLlib necesita como datos de entrada. Un [punto de la etiqueta](https://spark.apache.org/docs/latest/mllib-data-types.html#labeled-point) es un vector local, denso o disperso, asociado con una respuesta de etiqueta.

Este es el código de índice y codificar las características de texto para clasificación binario.

    # FUNCTIONS FOR BINARY CLASSIFICATION

    # LOAD LIBRARIES
    from pyspark.mllib.regression import LabeledPoint
    from numpy import array

    # INDEXING CATEGORICAL TEXT FEATURES FOR INPUT INTO TREE-BASED MODELS
    def parseRowIndexingBinary(line):
        features = np.array([line.paymentIndex, line.vendorIndex, line.rateIndex, line.pickup_hour, line.weekday,
                             line.passenger_count, line.trip_time_in_secs, line.trip_distance, line.fare_amount])
        labPt = LabeledPoint(line.tipped, features)
        return  labPt
    
    # ONE-HOT ENCODING OF CATEGORICAL TEXT FEATURES FOR INPUT INTO LOGISTIC RERESSION MODELS
    def parseRowOneHotBinary(line):
        features = np.concatenate((np.array([line.pickup_hour, line.weekday, line.passenger_count,
                                            line.trip_time_in_secs, line.trip_distance, line.fare_amount]), 
                                   line.vendorVec.toArray(), line.rateVec.toArray(), line.paymentVec.toArray()), axis=0)
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
    
    # SPECIFY SAMPLING AND SPLITTING FRACTIONS
    from pyspark.sql.functions import rand
    
    samplingFraction = 0.25;
    trainingFraction = 0.75; testingFraction = (1-trainingFraction);
    seed = 1234;
    encodedFinalSampled = encodedFinal.sample(False, samplingFraction, seed=seed)
    
    # SPLIT SAMPLED DATA-FRAME INTO TRAIN/TEST, WITH A RANDOM COLUMN ADDED FOR DOING CV (SHOWN LATER)
    # INCLUDE RAND COLUMN FOR CREATING CROSS-VALIDATION FOLDS
    dfTmpRand = encodedFinalSampled.select("*", rand(0).alias("rand"));
    trainData, testData = dfTmpRand.randomSplit([trainingFraction, testingFraction], seed=seed);
    
    # CACHE TRAIN AND TEST DATA
    trainData.cache()
    testData.cache()
    
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

**SALIDA**

Tiempo necesario para ejecutar encima de celda: 0,31 segundos


### <a name="feature-scaling"></a>Característica de ajuste de escala

Característica de ajuste de escala, también se conoce como normalización de datos, garantiza que funciones con valores muy dispersos son no dada excesivo valorar en función de objetivos. El código de escalado de características utiliza el [StandardScaler](https://spark.apache.org/docs/latest/api/python/pyspark.mllib.html#pyspark.mllib.feature.StandardScaler) para ajustar el tamaño de las características de variación de unidad. Se proporciona por MLlib para su uso en con estocástico degradado descenso (SGD), un algoritmo popular para una amplia gama de otro equipo modelos como retrocesos regularice o soporte técnico vector máquinas (SVM) de aprendizaje de formación de regresión lineal.   

>[AZURE.TIP] Hemos encontrado el algoritmo de LinearRegressionWithSGD ser confidencial escalado de características.   

Este es el código a escala variables para usarla con el algoritmo SGD regularized lineal.

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

**SALIDA**

Tiempo necesario para ejecutar encima de celda: 11.67 segundos


### <a name="cache-objects-in-memory"></a>Objetos de caché en memoria

Almacenando el marco de entrada de datos utilizan para la clasificación, regresión objetos y, en escalan características se puede reducir el tiempo de aprendizaje y la comprobación de algoritmos m.

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

**SALIDA** 

Tiempo necesario para ejecutar encima de celda: 0,13 segundos


## <a name="predict-whether-or-not-a-tip-is-paid-with-binary-classification-models"></a>Predecir si no se paga una sugerencia con los modelos de clasificación binario

Esta sección muestra cómo usar tres modelos para la tarea de clasificación binario de predecir si no se paga una sugerencia para un viaje taxi. Los modelos presentarlos son:

- Regresión logística 
- Aleatorio bosque
- Árboles de aumentar degradados

Cada modelo de creación de la sección de código se divide en los pasos: 

1. **Formación de modelo** de datos con un conjunto de parámetros
2. **Evaluación de modelo** en un conjunto de datos de prueba con métricas
3. **Guardar modelo** blobs de consumo futuro

Le mostraremos cómo llevar a cabo (VC) de validación cruzada con el parámetro de barrido de dos maneras:

1. Usando código personalizado **genérico** que se puede aplicar a cualquier algoritmo de MLlib y a cualquier parámetro se establece en un algoritmo. 
1. Uso de la **función de la canalización de CrossValidator de pySpark**. Tenga en cuenta que aunque es útil, según nuestra experiencia CrossValidator tiene algunas limitaciones de motor 1.5.0: 

    - Modelos de canalización no pueden ser guardado y conserva de consumo futuro.
    - No se pueden usar para cada parámetro en un modelo.
    - No se pueden usar para cada algoritmo de MLlib.


### <a name="generic-cross-validation-and-hyperparameter-sweeping-used-with-the-logistic-regression-algorithm-for-binary-classification"></a>Genérico cruzada validación y barrido hyperparameter utilizados con el algoritmo de regresión logística de clasificación binario

El código de esta sección muestra cómo entrenar, evaluar y guardar un modelo de regresión logística con [LBFGS](https://en.wikipedia.org/wiki/Broyden%E2%80%93Fletcher%E2%80%93Goldfarb%E2%80%93Shanno_algorithm) que prevé si no se paga una sugerencia para un viaje de la ciudad de Nueva York taxi viaje y tarifa conjunto de datos. El modelo está capacitado con cruzada validación (VC) y barrido hyperparameter implementado con código personalizado que se puede aplicar a cualquiera de los algoritmos de aprendizaje en MLlib.   

>[AZURE.NOTE] La ejecución de este código personalizado de VC puede tardar varios minutos.

**Entrenar el modelo de regresión logística con VC y barrido hyperparameter**

    # LOGISTIC REGRESSION CLASSIFICATION WITH CV AND HYPERPARAMETER SWEEPING

    # GET ACCURACY FOR HYPERPARAMETERS BASED ON CROSS-VALIDATION IN TRAINING DATA-SET

    # RECORD START TIME
    timestart = datetime.datetime.now()
    
    # LOAD LIBRARIES
    from pyspark.mllib.classification import LogisticRegressionWithLBFGS 
    from pyspark.mllib.evaluation import BinaryClassificationMetrics
    
    # CREATE PARAMETER GRID FOR LOGISTIC REGRESSION PARAMETER SWEEP
    from sklearn.grid_search import ParameterGrid
    grid = [{'regParam': [0.01, 0.1], 'iterations': [5, 10], 'regType': ["l1", "l2"], 'tolerance': [1e-3, 1e-4]}]
    paramGrid = list(ParameterGrid(grid))
    numModels = len(paramGrid)
    
    # SET NUM FOLDS AND NUM PARAMETER SETS TO SWEEP ON
    nFolds = 3;
    h = 1.0 / nFolds;
    metricSum = np.zeros(numModels);
    
    # BEGIN CV WITH PARAMETER SWEEP
    for i in range(nFolds):
        # Create training and x-validation sets
        validateLB = i * h
        validateUB = (i + 1) * h
        condition = (trainData["rand"] >= validateLB) & (trainData["rand"] < validateUB)
        validation = trainData.filter(condition)
        # Create LabeledPoints from data-frames
        if i > 0:
            trainCVLabPt.unpersist()
            validationLabPt.unpersist()
        trainCV = trainData.filter(~condition)
        trainCVLabPt = trainCV.map(parseRowOneHotBinary)
        trainCVLabPt.cache()
        validationLabPt = validation.map(parseRowOneHotBinary)
        validationLabPt.cache()
        # For parameter sets compute metrics from x-validation
        for j in range(numModels):
            regt = paramGrid[j]['regType']
            regp = paramGrid[j]['regParam']
            iters = paramGrid[j]['iterations']
            tol = paramGrid[j]['tolerance']
            # Train logistic regression model with hypermarameter set
            model = LogisticRegressionWithLBFGS.train(trainCVLabPt, regType=regt, iterations=iters,  
                                                      regParam=regp, tolerance = tol, intercept=True)
            predictionAndLabels = validationLabPt.map(lambda lp: (float(model.predict(lp.features)), lp.label))
            # Use ROC-AUC as accuracy metrics
            validMetrics = BinaryClassificationMetrics(predictionAndLabels)
            metric = validMetrics.areaUnderROC
            metricSum[j] += metric
    
    avgAcc = metricSum / nFolds;
    bestParam = paramGrid[np.argmax(avgAcc)];
    
    # UNPERSIST OBJECTS
    trainCVLabPt.unpersist()
    validationLabPt.unpersist()
        
    # TRAIN ON FULL TRAIING SET USING BEST PARAMETERS FROM CV/PARAMETER SWEEP
    logitBest = LogisticRegressionWithLBFGS.train(oneHotTRAINbinary, regType=bestParam['regType'], 
                                                  iterations=bestParam['iterations'], 
                                                  regParam=bestParam['regParam'], tolerance = bestParam['tolerance'], 
                                                  intercept=True)
    
    
    # PRINT COEFFICIENTS AND INTERCEPT OF THE MODEL
    # NOTE: There are 20 coefficient terms for the 10 features, 
    #       and the different categories for features: vendorVec (2), rateVec, paymentVec (6), TrafficTimeBinsVec (4)
    print("Coefficients: " + str(logitBest.weights))
    print("Intercept: " + str(logitBest.intercept))
    
    # PRINT ELAPSED TIME    
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 


**SALIDA**

Coeficientes: [0.0082065285375,-0.0223675576104,-0.0183812028036, - 3.48124578069e-05,-0.00247646947233,-0.00165897881503, 0.0675394837328,-0.111823113101,-0.324609912762,-0.204549780032,-1.36499216354, 0.591088507921,-0.664263411392,-1.00439726852, 3.46567827545,-3.51025855172,-0.0471341112232,-0.043521833294, 0.000243375810385, 0.054518719222]

Intersección:-0.0111216486893

Tiempo necesario para ejecutar encima de celda: 14.43 segundos


**Evaluar el modelo de clasificación binario con métricas estándares**

El código de esta sección muestra cómo evaluar un modelo de regresión logística frente a una prueba de conjuntos de datos, incluidos un trazado de la curva ROC.


    # RECORD START TIME
    timestart = datetime.datetime.now()

    #IMPORT LIBRARIES
    from sklearn.metrics import roc_curve,auc
    from pyspark.mllib.evaluation import BinaryClassificationMetrics
    from pyspark.mllib.evaluation import MulticlassMetrics
    
    # PREDICT ON TEST DATA WITH BEST/FINAL MODEL
    predictionAndLabels = oneHotTESTbinary.map(lambda lp: (float(logitBest.predict(lp.features)), lp.label))
    
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
    
    # OUTPUT PROBABILITIES AND REGISTER TEMP TABLE
    logitBest.clearThreshold(); # This clears threshold for classification (0.5) and outputs probabilities
    predictionAndLabelsDF = predictionAndLabels.toDF()
    predictionAndLabelsDF.registerTempTable("tmp_results");

    # PRINT ELAPSED TIME    
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 


**SALIDA**

Área de PR = 0.985336538462

Área de ROC = 0.983383274312

Estadísticas de resumen

Precisión = 0.984174341679

Recuperar = 0.984174341679

F1 Puntuación = 0.984174341679

Tiempo necesario para ejecutar encima de celda: 2,67 segundos


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
    
    #PREDICTIONS
    predictions_pddf = sqlResults.rename(columns={'_1': 'probability', '_2': 'label'})
    prob = predictions_pddf["probability"] 
    fpr, tpr, thresholds = roc_curve(predictions_pddf['label'], prob, pos_label=1);
    roc_auc = auc(fpr, tpr)
    
    # PLOT ROC CURVES
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
    

**SALIDA**

![Curva de regresión logística ROC para enfoque genérico](./media/machine-learning-data-science-spark-advanced-data-exploration-modeling/logistic-regression-roc-curve.png)


**Conservar el modelo en un blob de consumo futuro**

El código de esta sección muestra cómo guardar el modelo de regresión logística para consumo.

    # RECORD START TIME
    timestart = datetime.datetime.now()
    
    # LOAD PYSPARK LIBRARIES
    from pyspark.mllib.classification import LogisticRegressionModel
    
    # PERSIST MODEL
    datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
    logisticregressionfilename = "LogisticRegressionWithLBFGS_" + datestamp;
    dirfilename = modelDir + logisticregressionfilename;
    
    logitBest.save(sc, dirfilename);
    
    # PRINT ELAPSED TIME
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds";


**SALIDA**

Tiempo necesario para ejecutar encima de celda: 34.57 segundos


### <a name="use-mllibs-crossvalidator-pipeline-function-with-logistic-regression-elastic-regression-model"></a>Usar función de canalización de CrossValidator de MLlib con el modelo de regresión logística (regresión elástica)

El código de esta sección muestra cómo entrenar, evaluar y guardar un modelo de regresión logística con [LBFGS](https://en.wikipedia.org/wiki/Broyden%E2%80%93Fletcher%E2%80%93Goldfarb%E2%80%93Shanno_algorithm) que prevé si no se paga una sugerencia para un viaje de la ciudad de Nueva York taxi viaje y tarifa conjunto de datos. El modelo está capacitado con cruzada validación (VC) y barrido hyperparameter ha implementado con la función de canalización MLlib CrossValidator para VC barrido de parámetro.   


>[AZURE.NOTE] La ejecución de este código MLlib VC puede tardar varios minutos.


    # RECORD START TIME
    timestart = datetime.datetime.now()
    
    # LOAD PYSPARK LIBRARIES
    from pyspark.ml.classification import LogisticRegression
    from pyspark.ml import Pipeline
    from pyspark.ml.evaluation import BinaryClassificationEvaluator
    from pyspark.ml.tuning import CrossValidator, ParamGridBuilder
    from sklearn.metrics import roc_curve,auc
    
    # DEFINE ALGORITHM / MODEL
    lr = LogisticRegression()
    
    # DEFINE GRID PARAMETERS
    paramGrid = ParamGridBuilder().addGrid(lr.regParam, (0.01, 0.1))\
                                  .addGrid(lr.maxIter, (5, 10))\
                                  .addGrid(lr.tol, (1e-4, 1e-5))\
                                  .addGrid(lr.elasticNetParam, (0.25,0.75))\
                                  .build()
    
    # DEFINE CV WITH PARAMETER SWEEP
    cv = CrossValidator(estimator= lr,
                        estimatorParamMaps=paramGrid,
                        evaluator=BinaryClassificationEvaluator(),
                        numFolds=3)
    
    # CONVERT TO DATA-FRAME: THIS DOES NOT RUN ON RDDs
    trainDataFrame = sqlContext.createDataFrame(oneHotTRAINbinary, ["features", "label"])
    
    # TRAIN WITH CROSS-VALIDATION
    cv_model = cv.fit(trainDataFrame)
    

    ## PREDICT AND EVALUATE ON TEST DATA-SET

    # USE TEST DATASET FOR PREDICTION
    testDataFrame = sqlContext.createDataFrame(oneHotTESTbinary, ["features", "label"])
    test_predictions = cv_model.transform(testDataFrame)
    
    # PRINT ELAPSED TIME
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds";

**SALIDA**

Tiempo necesario para ejecutar encima de celda: 107.98 segundos


**Trazar la curva ROC.**

El *predictionAndLabelsDF* está registrado como una tabla, *tmp_results*, en la celda anterior. *tmp_results* puede utilizarse para hacer consultas y resultados de salida en el marco de datos sqlResults para representar el gráfico. Este es el código.


    # QUERY RESULTS
    %%sql -q -o sqlResults
    SELECT label, prediction, probability from tmp_results

Este es el código para trazar la curva ROC.

    # RUN THE CODE LOCALLY ON THE JUPYTER SERVER AND IMPORT LIBRARIES 
    %%local
    from sklearn.metrics import roc_curve,auc
    
    # ROC CURVE
    prob = [x["values"][1] for x in sqlResults["probability"]]
    fpr, tpr, thresholds = roc_curve(sqlResults['label'], prob, pos_label=1);
    roc_auc = auc(fpr, tpr)
    
    #PLOT
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


**SALIDA**

![Curva de regresión logística ROC con CrossValidator de MLlib](./media/machine-learning-data-science-spark-advanced-data-exploration-modeling/mllib-crossvalidator-roc-curve.png)



### <a name="random-forest-classification"></a>Clasificación de bosque aleatorios

El código de esta sección muestra cómo entrenar, evaluar y guardar una regresión del bosque aleatorio que prevé si no se paga una sugerencia para un viaje de la ciudad de Nueva York taxi viaje y tarifa conjunto de datos.


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
    ## UN-COMMENT IF YOU WANT TO PRING TREES
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


**SALIDA**

Área de ROC = 0.985336538462

Tiempo necesario para ejecutar encima de celda: 26.72 segundos


### <a name="gradient-boosting-trees-classification"></a>Clasificación de árboles Aumentar degradado

El código de esta sección muestra cómo entrenar, evaluar y guardar un modelo de árboles de aumentar degradado predicciones si no se paga una sugerencia para un viaje de ida y vuelta el taxi Nueva York y resultados son el conjunto de datos.


    # RECORD START TIME
    timestart = datetime.datetime.now()
    
    # LOAD PYSPARK LIBRARIES
    from pyspark.mllib.tree import GradientBoostedTrees, GradientBoostedTreesModel
    
    # SPECIFY NUMBER OF CATEGORIES FOR CATEGORICAL FEATURES. FEATURE #0 HAS 2 CATEGORIES, FEATURE #2 HAS 2 CATEGORIES, AND SO ON
    categoricalFeaturesInfo={0:2, 1:2, 2:6, 3:4}
    
    gbtModel = GradientBoostedTrees.trainClassifier(indexedTRAINbinary, categoricalFeaturesInfo=categoricalFeaturesInfo,
                                                    numIterations=10)
    ## UNCOMMENT IF YOU WANT TO PRINT TREE DETAILS
    #print('Learned classification GBT model:')
    #print(bgtModel.toDebugString())
    
    # PREDICT ON TEST DATA AND EVALUATE
    predictions = gbtModel.predict(indexedTESTbinary.map(lambda x: x.features))
    predictionAndLabels = indexedTESTbinary.map(lambda lp: lp.label).zip(predictions)
    
    # Area under ROC curve
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

**SALIDA**

Área de ROC = 0.985336538462

Tiempo necesario para ejecutar encima de celda: 28.13 segundos


## <a name="predict-tip-amount-with-regression-models-not-using-cv"></a>Predecir la cantidad de sugerencia con los modelos de regresión (sin utilizar VC)

Esta sección muestra cómo tres modelos de uso de la tarea de regresión de predecir la cantidad de la sugerencia de pago para un viaje de taxi en función de otras características de sugerencia. Los modelos presentarlos son:

- Regularice regresión lineal
- Aleatorio bosque
- Árboles de aumentar degradados

Estos modelos se ha descrito en la introducción. Cada modelo de creación de la sección de código se divide en los pasos: 

1. **Formación de modelo** de datos con un conjunto de parámetros
2. **Evaluación de modelo** en un conjunto de datos de prueba con métricas
3. **Guardar modelo** blobs de consumo futuro   


>Nota de AZURE: Validación cruzada no se utiliza con los modelos de tres regresión en esta sección, ya que esto se muestra en detalle para los modelos de regresión logística. En el apéndice de este tema, se proporciona un ejemplo que muestra cómo usar VC con elástico neto de regresión lineal.


>Nota AZURE: En nuestra experiencia, puede haber problemas con la convergencia de modelos LinearRegressionWithSGD y parámetros necesitan ser cambiado/optimizados cuidadosamente para obtener un modelo válido. Ajuste de escala de variables significativamente ayuda con convergencia. Elástico regresión net, que se muestra en el apéndice de este tema, también puede usarse en lugar de LinearRegressionWithSGD.


### <a name="linear-regression-with-sgd"></a>Regresión lineal con SGD

El código de esta sección muestra cómo usar las características de escala para formar una regresión lineal que usa descenso degradado estocástico (SGD) para la optimización y cómo puntuación, evaluar y a continuación, guarde el modelo de almacenamiento de blobs de Windows Azure (WASB).

>[AZURE.TIP] En nuestra experiencia, puede haber problemas con la convergencia de modelos LinearRegressionWithSGD y parámetros necesitan ser cambiado/optimizados cuidadosamente para obtener un modelo válido. Ajuste de escala de variables significativamente ayuda con convergencia.


    # LINEAR REGRESSION WITH SGD 

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
    
    print("RMSE = %s" % testMetrics.rootMeanSquaredError)
    print("R-sqr = %s" % testMetrics.r2)
    
    # SAVE MODEL IN BLOB
    datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
    linearregressionfilename = "LinearRegressionWithSGD_" + datestamp;
    dirfilename = modelDir + linearregressionfilename;
    
    linearModel.save(sc, dirfilename)
    
    # PRINT ELAPSED TIME
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 

**SALIDA**

Coeficientes: [0.0141707753435,-0.0252930927087,-0.0231442517137, 0.247070902996, 0.312544147152, 0.360296120645, 0.0122079566092,-0.00456498588241,-0.0898228505177, 0.0714046248793, 0.102171263868, 0.100022455632,-0.00289545676449,-0.00791124681938, 0.54396316518,-0.536293513569, 0.0119076553369,-0.0173039244582, 0.0119632796147, 0.00146764882502]

Interceptar: 0.854507624459

MÉTRICA ECM = 1.23485131376

Sqr R = 0.597963951127

Tiempo necesario para ejecutar encima de celda: 38.62 segundos


### <a name="random-forest-regression"></a>Regresión del bosque aleatoria

El código de esta sección muestra cómo entrenar, evaluar y guardar un modelo de bosque aleatorio que prevé la cantidad de sugerencia para los datos de viaje de taxi de Nueva York.   

>[AZURE.NOTE] En el apéndice se proporciona validación cruzada con el parámetro barrido usando código personalizado.


    #PREDICT TIP AMOUNTS USING RANDOM FOREST

    # RECORD START TIME
    timestart= datetime.datetime.now()
    
    # LOAD PYSPARK LIBRARIES
    from pyspark.mllib.tree import RandomForest, RandomForestModel
    from pyspark.mllib.util import MLUtils
    from pyspark.mllib.evaluation import RegressionMetrics
    
    
    # TRAIN MODEL
    categoricalFeaturesInfo={0:2, 1:2, 2:6, 3:4}
    rfModel = RandomForest.trainRegressor(indexedTRAINreg, categoricalFeaturesInfo=categoricalFeaturesInfo,
                                        numTrees=25, featureSubsetStrategy="auto",
                                        impurity='variance', maxDepth=10, maxBins=32)
    # UN-COMMENT IF YOU WANT TO PRING TREES
    #print('Learned classification forest model:')
    #print(rfModel.toDebugString())
    
    # PREDICT AND EVALUATE ON TEST DATA-SET
    predictions = rfModel.predict(indexedTESTreg.map(lambda x: x.features))
    predictionAndLabels = oneHotTESTreg.map(lambda lp: lp.label).zip(predictions)
    
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

**SALIDA**

MÉTRICA ECM = 0.931981967875

Sqr R = 0.733445485802

Tiempo necesario para ejecutar encima de celda: 25.98 segundos


### <a name="gradient-boosting-trees-regression"></a>Degradado regresión árboles aumentar

El código de esta sección muestra cómo entrenar, evaluar y guardar un modelo de árboles de aumentar degradado predicciones cantidad de sugerencia para los datos de viaje de taxi de Nueva York.

**Entrenar y evaluar**

    #PREDICT TIP AMOUNTS USING GRADIENT BOOSTING TREES

    # RECORD START TIME
    timestart= datetime.datetime.now()
    
    # LOAD PYSPARK LIBRARIES
    from pyspark.mllib.tree import GradientBoostedTrees, GradientBoostedTreesModel
    from pyspark.mllib.util import MLUtils
    
    # TRAIN MODEL
    categoricalFeaturesInfo={0:2, 1:2, 2:6, 3:4}
    gbtModel = GradientBoostedTrees.trainRegressor(indexedTRAINreg, categoricalFeaturesInfo=categoricalFeaturesInfo, 
                                                    numIterations=10, maxBins=32, maxDepth = 4, learningRate=0.1)
    
    # EVALUATE A TEST DATA-SET
    predictions = gbtModel.predict(indexedTESTreg.map(lambda x: x.features))
    predictionAndLabels = indexedTESTreg.map(lambda lp: lp.label).zip(predictions)
    
    testMetrics = RegressionMetrics(predictionAndLabels)
    print("RMSE = %s" % testMetrics.rootMeanSquaredError)
    print("R-sqr = %s" % testMetrics.r2)
    
    # PLOT SCATTER-PLOT BETWEEN ACTUAL AND PREDICTED TIP VALUES
    test_predictions= sqlContext.createDataFrame(predictionAndLabels)
    test_predictions_pddf = test_predictions.toPandas()
    
    # SAVE MODEL IN BLOB
    datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
    btregressionfilename = "GradientBoostingTreeRegression_" + datestamp;
    dirfilename = modelDir + btregressionfilename;
    gbtModel.save(sc, dirfilename)
    
    # PRINT ELAPSED TIME
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 


**SALIDA**

MÉTRICA ECM = 0.928172197114

Sqr R = 0.732680354389

Tiempo necesario para ejecutar encima de celda: 20,9 segundos


**Trazado**
    
*tmp_results* está registrado como una tabla subárbol en la celda anterior. Resultados de la tabla son resultado en el marco de datos *sqlResults* para representar el gráfico. Este es el código

    # PLOT SCATTER-PLOT BETWEEN ACTUAL AND PREDICTED TIP VALUES

    # SELECT RESULTS
    %%sql -q -o sqlResults
    SELECT * from tmp_results


Este es el código para trazar los datos con el servidor Jupyter.

    # RUN THE CODE LOCALLY ON THE JUPYTER SERVER AND IMPORT LIBRARIES
    %%local
    import numpy as np
    
    # PLOT
    ax = sqlResults.plot(kind='scatter', figsize = (6,6), x='_1', y='_2', color='blue', alpha = 0.25, label='Actual vs. predicted');
    fit = np.polyfit(sqlResults['_1'], sqlResults['_2'], deg=1)
    ax.set_title('Actual vs. Predicted Tip Amounts ($)')
    ax.set_xlabel("Actual")
    ax.set_ylabel("Predicted")
    ax.plot(sqlResults['_1'], fit[0] * sqlResults['_1'] + fit[1], color='magenta')
    plt.axis([-1, 15, -1, 15])
    plt.show(ax)

![Vs-pronosticados-sugerencia-importes reales](./media/machine-learning-data-science-spark-advanced-data-exploration-modeling/actual-vs-predicted-tips.png)


## <a name="appendix-additional-regression-tasks-using-cross-validation-with-parameter-sweeps"></a>Apéndice: Tareas de regresión adicionales mediante validación cruzada con recorridos de parámetro

Este apéndice contiene código que muestra cómo hacer VC con net elástico de regresión lineal y cómo hacer VC con limpiar parámetro usando código personalizado de regresión del bosque aleatorio.


### <a name="cross-validation-using-elastic-net-for-linear-regression"></a>Combinación cruzada validación mediante elástica neto de regresión lineal.

El código de esta sección muestra cómo cruzada validación mediante net elástico de regresión lineal y cómo evaluar el modelo de los datos de prueba.

    ###  CV USING ELASTIC NET FOR LINEAR REGRESSION

    # RECORD START TIME
    timestart = datetime.datetime.now()
    
    # LOAD PYSPARK LIBRARIES
    from pyspark.ml.regression import LinearRegression
    from pyspark.ml import Pipeline
    from pyspark.ml.evaluation import RegressionEvaluator
    from pyspark.ml.tuning import CrossValidator, ParamGridBuilder
    
    # DEFINE ALGORITHM/MODEL
    lr = LinearRegression()
    
    # DEFINE GRID PARAMETERS
    paramGrid = ParamGridBuilder().addGrid(lr.regParam, (0.01, 0.1))\
                                  .addGrid(lr.maxIter, (5, 10))\
                                  .addGrid(lr.tol, (1e-4, 1e-5))\
                                  .addGrid(lr.elasticNetParam, (0.25,0.75))\
                                  .build() 
    
    # DEFINE PIPELINE 
    # SIMPLY THE MODEL HERE, WITHOUT TRANSFORMATIONS
    pipeline = Pipeline(stages=[lr])
    
    # DEFINE CV WITH PARAMETER SWEEP
    cv = CrossValidator(estimator= lr,
                        estimatorParamMaps=paramGrid,
                        evaluator=RegressionEvaluator(),
                        numFolds=3)
    
    # CONVERT TO DATA FRAME, AS CROSSVALIDATOR WON'T RUN ON RDDS
    trainDataFrame = sqlContext.createDataFrame(oneHotTRAINreg, ["features", "label"])
    
    # TRAIN WITH CROSS-VALIDATION
    cv_model = cv.fit(trainDataFrame)
    

    # EVALUATE MODEL ON TEST SET
    testDataFrame = sqlContext.createDataFrame(oneHotTESTreg, ["features", "label"])
    
    # MAKE PREDICTIONS ON TEST DOCUMENTS
    # cvModel uses the best model found (lrModel).
    predictionAndLabels = cv_model.transform(testDataFrame)
    
    # CONVERT TO DF AND SAVE REGISER DF AS TABLE
    predictionAndLabels.registerTempTable("tmp_results");
    
    # PRINT ELAPSED TIME
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 


**SALIDA**

Tiempo necesario para ejecutar encima de celda: 161.21 segundos

**Evaluar con métrica SQR R**

*tmp_results* está registrado como una tabla subárbol en la celda anterior. Resultados de la tabla son resultado en el marco de datos *sqlResults* para representar el gráfico. Este es el código

    # SELECT RESULTS
    %%sql -q -o sqlResults
    SELECT label,prediction from tmp_results


Este es el código para calcular R sqr.

    # RUN THE CODE LOCALLY ON THE JUPYTER SERVER AND IMPORT LIBRARIES
    %%local
    from scipy import stats
    
    #R-SQR TEST METRIC
    corstats = stats.linregress(sqlResults['label'],sqlResults['prediction'])
    r2 = (corstats[2]*corstats[2])
    print("R-sqr = %s" % r2)


**SALIDA**

Sqr R = 0.619184907088


### <a name="cross-validation-with-parameter-sweep-using-custom-code-for-random-forest-regression"></a>Combinación cruzada validación con limpiar parámetro usando código personalizado de regresión del bosque aleatorios

El código de esta sección muestra cómo cruzada validación con limpiar parámetro usando código personalizado de regresión del bosque aleatorio y evaluar el modelo de los datos de prueba.


    # RECORD START TIME
    timestart= datetime.datetime.now()

    # LOAD PYSPARK LIBRARIES
    # GET ACCURARY FOR HYPERPARAMETERS BASED ON CROSS-VALIDATION IN TRAINING DATA-SET
    from pyspark.mllib.tree import RandomForest, RandomForestModel
    from pyspark.mllib.util import MLUtils
    from pyspark.mllib.evaluation import RegressionMetrics
    from sklearn.grid_search import ParameterGrid
    
    ## CREATE PARAMETER GRID
    grid = [{'maxDepth': [5,10], 'numTrees': [25,50]}]
    paramGrid = list(ParameterGrid(grid))
    
    ## SPECIFY LEVELS OF CATEGORICAL VARIBLES
    categoricalFeaturesInfo={0:2, 1:2, 2:6, 3:4}
    
    # SPECIFY NUMFOLDS AND ARRAY TO HOLD METRICS
    nFolds = 3;
    numModels = len(paramGrid)
    h = 1.0 / nFolds;
    metricSum = np.zeros(numModels);
    
    for i in range(nFolds):
        # Create training and x-validation sets
        validateLB = i * h
        validateUB = (i + 1) * h
        condition = (trainData["rand"] >= validateLB) & (trainData["rand"] < validateUB)
        validation = trainData.filter(condition)
        # Create labeled points from data-frames
        if i > 0:
            trainCVLabPt.unpersist()
            validationLabPt.unpersist()
        trainCV = trainData.filter(~condition)
        trainCVLabPt = trainCV.map(parseRowIndexingRegression)
        trainCVLabPt.cache()
        validationLabPt = validation.map(parseRowIndexingRegression)
        validationLabPt.cache()
        # For parameter sets compute metrics from x-validation
        for j in range(numModels):
            maxD = paramGrid[j]['maxDepth']
            numT = paramGrid[j]['numTrees']
            # Train logistic regression model with hypermarameter set
            rfModel = RandomForest.trainRegressor(trainCVLabPt, categoricalFeaturesInfo=categoricalFeaturesInfo,
                                        numTrees=numT, featureSubsetStrategy="auto",
                                        impurity='variance', maxDepth=maxD, maxBins=32)
            predictions = rfModel.predict(validationLabPt.map(lambda x: x.features))
            predictionAndLabels = validationLabPt.map(lambda lp: lp.label).zip(predictions)
            # Use ROC-AUC as accuracy metrics
            validMetrics = RegressionMetrics(predictionAndLabels)
            metric = validMetrics.rootMeanSquaredError
            metricSum[j] += metric
    
    avgAcc = metricSum/nFolds;
    bestParam = paramGrid[np.argmin(avgAcc)];
    
    # UNPERSIST OBJECTS
    trainCVLabPt.unpersist()
    validationLabPt.unpersist()
            
    ## TRAIN FINAL MODL WIHT BEST PARAMETERS
    rfModel = RandomForest.trainRegressor(indexedTRAINreg, categoricalFeaturesInfo=categoricalFeaturesInfo,
                                        numTrees=bestParam['numTrees'], featureSubsetStrategy="auto",
                                        impurity='variance', maxDepth=bestParam['maxDepth'], maxBins=32)

    # EVALUATE MODEL ON TEST DATA
    predictions = rfModel.predict(indexedTESTreg.map(lambda x: x.features))
    predictionAndLabels = indexedTESTreg.map(lambda lp: lp.label).zip(predictions)
    
    #PRINT TEST METRICS
    testMetrics = RegressionMetrics(predictionAndLabels)
    print("RMSE = %s" % testMetrics.rootMeanSquaredError)
    print("R-sqr = %s" % testMetrics.r2)
    
    # PRINT ELAPSED TIME
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 


**SALIDA**

MÉTRICA ECM = 0.906972198262

Sqr R = 0.740751197012

Tiempo necesario para ejecutar encima de celda: 69.17 segundos


### <a name="clean-up-objects-from-memory-and-print-model-locations"></a>Limpiar los objetos de la memoria y las ubicaciones de impresión del modelo

Usar `unpersist()` para eliminar los objetos en la memoria caché.

    # UNPERSIST OBJECTS CACHED IN MEMORY

    # REMOVE ORIGINAL DFs
    taxi_df_train_cleaned.unpersist()
    taxi_df_train_with_newFeatures.unpersist()
    trainData.unpersist()
    trainData.unpersist()
    
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


**SALIDA**

PythonRDD [122] en RDD en PythonRDD.scala: 43


**Ruta de la copia impresa a archivos de modelo para utilizarse en el Bloc de notas de consumo.** Para consumir y la puntuación de un conjunto de datos independiente, debe copiar y pegar estos nombres de archivo en el "Bloc de notas de consumo".


    # PRINT MODEL FILE LOCATIONS FOR CONSUMPTION
    print "logisticRegFileLoc = modelDir + \"" + logisticregressionfilename + "\"";
    print "linearRegFileLoc = modelDir + \"" + linearregressionfilename + "\"";
    print "randomForestClassificationFileLoc = modelDir + \"" + rfclassificationfilename + "\"";
    print "randomForestRegFileLoc = modelDir + \"" + rfregressionfilename + "\"";
    print "BoostedTreeClassificationFileLoc = modelDir + \"" + btclassificationfilename + "\"";
    print "BoostedTreeRegressionFileLoc = modelDir + \"" + btregressionfilename + "\"";


**SALIDA**

logisticRegFileLoc = modelDir + "LogisticRegressionWithLBFGS_2016-05-0316_47_30.096528"

linearRegFileLoc = modelDir + "LinearRegressionWithSGD_2016-05-0316_51_28.433670"

randomForestClassificationFileLoc = modelDir + "RandomForestClassification_2016-05-0316_50_17.454440"

randomForestRegFileLoc = modelDir + "RandomForestRegression_2016-05-0316_51_57.331730"

BoostedTreeClassificationFileLoc = modelDir + "GradientBoostingTreeClassification_2016-05-0316_50_40.138809"

BoostedTreeRegressionFileLoc = modelDir + "GradientBoostingTreeRegression_2016-05-0316_52_18.827237"

## <a name="whats-next"></a>¿Qué es el siguiente?

Ahora que ha creado modelos de clasificación y de regresión con el MlLib motor, ya está listo para aprender a la puntuación y evaluar estos modelos.

**Consumo de modelo:** Para obtener información sobre cómo puntuación y evaluar los modelos de clasificación y regresión creados en este tema, consulte [puntuación y evaluar modelos de aprendizaje integrado motor máquina](machine-learning-data-science-spark-model-consumption.md).
