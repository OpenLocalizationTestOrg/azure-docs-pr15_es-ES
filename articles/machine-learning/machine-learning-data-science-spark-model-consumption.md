<properties
    pageTitle="Puntuación modelos de aprendizaje de máquina integrado motor | Microsoft Azure"
    description="Cómo modelos de aprendizaje de puntuación que se han almacenado en el almacenamiento de blobs de Windows Azure (WASB)."
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

# <a name="score-spark-built-machine-learning-models"></a>Modelos de aprendizaje de máquina creado por el motor de puntuación 

[AZURE.INCLUDE [machine-learning-spark-modeling](../../includes/machine-learning-spark-modeling.md)]

Este tema describe cómo cargar modelos de máquina (v) de aprendizaje que ha generado con el motor MLlib y almacenados en el almacenamiento de blobs de Windows Azure (WASB) y cómo les puntuación con conjuntos de datos que también se han almacenado en WASB. Muestra cómo procesar previamente los datos de entrada, transformar características de uso de las funciones de indización y codificación en el Kit de herramientas de MLlib y cómo crear un objeto de datos de punto de etiquetado que se pueden usar como entrada de puntuación con los modelos de m. Los modelos usados para puntuación incluyen regresión lineal, regresión logística, modelos de bosque aleatorio y modelos de árbol de aumentar degradado.


## <a name="prerequisites"></a>Requisitos previos

1. Necesita una cuenta de Azure y un clúster de necesita una 1,6 de motor 3.4 HDInsight HDInsight motor que para completar este tutorial. Vea la [Información general de ciencias datos mediante el motor en Azure HDInsight](machine-learning-data-science-spark-overview.md) para obtener instrucciones sobre cómo cumplir estos requisitos. Este tema también contiene una descripción de los datos de Nueva York 2013 Taxi utilizados aquí e instrucciones sobre cómo ejecutar el código de un bloc de notas de Jupyter en el clúster de motor. El Bloc de notas **pySpark-machine-learning-data-science-spark-model-consumption.ipynb** que contiene los ejemplos de código de este tema está disponible en [Github](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/Spark/pySpark).

2. También debe crear el equipo modelos de ser puntuación aquí al seguir el tema [exploración de datos y modelado de motor](machine-learning-data-science-spark-data-exploration-modeling.md) de aprendizaje.   


[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]
 

## <a name="setup-storage-locations-libraries-and-the-preset-spark-context"></a>Configuración: el contexto de motor preestablecido, bibliotecas y ubicaciones de almacenamiento

Motor es capaz de lectura y escritura a un Blob de almacenamiento de Azure (WASB). Para que cualquiera de los datos existentes almacenados allí pueden procesar con el motor y los resultados almacenados nuevamente en WASB.

Para guardar los archivos o modelos en WASB, la ruta de acceso debe especificarse correctamente. Se puede hacer referencia el contenedor predeterminado conectado al clúster motor con el que comienza con una ruta de acceso: *"wasb / /"*. En el ejemplo de código siguiente especifica la ubicación de los datos que se puedan leer y la ruta de acceso para el directorio de almacenamiento de modelo a la que se guarda el resultado del modelo. 


### <a name="set-directory-paths-for-storage-locations-in-wasb"></a>Establecer rutas de directorios para ubicaciones de almacenamiento de WASB

Los modelos se guardan en: "wasb: / / o/remoteuser/NYCTaxi/modelos de usuario". Si esta ruta de acceso no está configurada correctamente, no se cargan modelos de puntuación.

Se han guardado los resultados puntuados en: "wasb: / / / usuarios/remoteuser/NYCTaxi/ScoredResults". Si la ruta de acceso a la carpeta es incorrecto, los resultados no se guardan en esa carpeta.   


>[AZURE.NOTE] Las ubicaciones de la ruta de acceso de archivo se pueden copiar y pegar en los marcadores de posición en el código de los resultados de la última celda del Bloc de notas **machine-learning-data-science-spark-data-exploration-modeling.ipynb** .   


Este es el código para establecer rutas de directorio: 

    # LOCATION OF DATA TO BE SCORED (TEST DATA)
    taxi_test_file_loc = "wasb://mllibwalkthroughs@cdspsparksamples.blob.core.windows.net/Data/NYCTaxi/JoinedTaxiTripFare.Point1Pct.Test.tsv";
    
    # SET THE MODEL STORAGE DIRECTORY PATH 
    # NOTE THE LAST BACKSLASH IN THIS PATH IS NEEDED
    modelDir = "wasb:///user/remoteuser/NYCTaxi/Models/" 
    
    # SET SCORDED RESULT DIRECTORY PATH
    # NOTE THE LAST BACKSLASH IN THIS PATH IS NEEDED
    scoredResultDir = "wasb:///user/remoteuser/NYCTaxi/ScoredResults/"; 
    
    # FILE LOCATIONS FOR THE MODELS TO BE SCORED
    logisticRegFileLoc = modelDir + "LogisticRegressionWithLBFGS_2016-04-1817_40_35.796789"
    linearRegFileLoc = modelDir + "LinearRegressionWithSGD_2016-04-1817_44_00.993832"
    randomForestClassificationFileLoc = modelDir + "RandomForestClassification_2016-04-1817_42_58.899412"
    randomForestRegFileLoc = modelDir + "RandomForestRegression_2016-04-1817_44_27.204734"
    BoostedTreeClassificationFileLoc = modelDir + "GradientBoostingTreeClassification_2016-04-1817_43_16.354770"
    BoostedTreeRegressionFileLoc = modelDir + "GradientBoostingTreeRegression_2016-04-1817_44_46.206262"

    # RECORD START TIME
    import datetime
    datetime.datetime.now()

**RESULTADO:**

DateTime.DateTime (2016, 4, 25, 23, 56, 19, 229403)


### <a name="import-libraries"></a>Bibliotecas de importación

Establecer el contexto de motor y bibliotecas necesarias con el siguiente código de importación

    #IMPORT LIBRARIES
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

Los kernels PySpark que se proporcionan con blocs de notas Jupyter tienen un contexto preestablecido. Así que no es necesario configurar el motor o desarrollan subárbol contextos explícitamente antes de empezar a trabajar con la aplicación. Estos son disponibles de forma predeterminada. Estos contextos son:

- SC - del motor 
- sqlContext - para sección

El núcleo PySpark proporciona algunos predefinido "magics", que son comandos especiales que se pueden llamar con %%. Hay dos comandos que se utilizan en estos ejemplos de código.

- **%% local** Especifica que el código de las siguientes líneas se ejecuta localmente. Código debe ser código Python válido.
- **%% sql -o<variable name>** 
- Ejecuta una consulta de la sección con la sqlContext. Si se pasa el parámetro -o, el resultado de la consulta se conserva en el %% contexto Python local como una dataframe Pandas.
 

Para obtener más información sobre los kernels de blocs de notas de Jupyter y la predefinido "magics" que proporcionan, consulte [Kernels disponibles para los blocs de notas de Jupyter con HDInsight motor Linux clústeres en HDInsight](../hdinsight/hdinsight-apache-spark-jupyter-notebook-kernels.md).


## <a name="ingest-data-and-create-a-cleaned-data-frame"></a>Recopilar datos y crear una trama de datos limpios

Esta sección contiene el código para una serie de tareas necesarias para recopilar los datos que se cuentan. Leer en una muestra de 0,1% combinados del taxi viaje y tarifa archivo (almacenado como un archivo .tsv), formato de los datos y, a continuación, crea un marco de limpiar datos.

Los archivos de viaje y tarifa taxi se han unido basándose en el procedimiento que se proporcionan en la: [el proceso de ciencias de datos de equipo en acción: usar clústeres HDInsight Hadoop](machine-learning-data-science-process-hive-walkthrough.md) tema.

    # INGEST DATA AND CREATE A CLEANED DATA FRAME

    # RECORD START TIME
    timestart = datetime.datetime.now()
    
    # IMPORT FILE FROM PUBLIC BLOB
    taxi_test_file = sc.textFile(taxi_test_file_loc)
    
    # GET SCHEMA OF THE FILE FROM HEADER
    taxi_header = taxi_test_file.filter(lambda l: "medallion" in l)
    
    # PARSE FIELDS AND CONVERT DATA TYPE FOR SOME FIELDS
    taxi_temp = taxi_test_file.subtract(taxi_header).map(lambda k: k.split("\t"))\
            .map(lambda p: (p[0],p[1],p[2],p[3],p[4],p[5],p[6],int(p[7]),int(p[8]),int(p[9]),int(p[10]),
                            float(p[11]),float(p[12]),p[13],p[14],p[15],p[16],p[17],p[18],float(p[19]),
                            float(p[20]),float(p[21]),float(p[22]),float(p[23]),float(p[24]),int(p[25]),int(p[26])))
        
    # GET SCHEMA OF THE FILE FROM HEADER
    schema_string = taxi_test_file.first()
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
    
    # CREATE DATA FRAME
    taxi_df_test = sqlContext.createDataFrame(taxi_temp, taxi_schema)
    
    # CREATE A CLEANED DATA-FRAME BY DROPPING SOME UN-NECESSARY COLUMNS & FILTERING FOR UNDESIRED VALUES OR OUTLIERS
    taxi_df_test_cleaned = taxi_df_test.drop('medallion').drop('hack_license').drop('store_and_fwd_flag').drop('pickup_datetime')\
        .drop('dropoff_datetime').drop('pickup_longitude').drop('pickup_latitude').drop('dropoff_latitude')\
        .drop('dropoff_longitude').drop('tip_class').drop('total_amount').drop('tolls_amount').drop('mta_tax')\
        .drop('direct_distance').drop('surcharge')\
        .filter("passenger_count > 0 and passenger_count < 8 AND payment_type in ('CSH', 'CRD') AND tip_amount >= 0 AND tip_amount < 30 AND fare_amount >= 1 AND fare_amount < 150 AND trip_distance > 0 AND trip_distance < 100 AND trip_time_in_secs > 30 AND trip_time_in_secs < 7200" )
    
    # CACHE DATA-FRAME IN MEMORY & MATERIALIZE DF IN MEMORY
    taxi_df_test_cleaned.cache()
    taxi_df_test_cleaned.count()
    
    # REGISTER DATA-FRAME AS A TEMP-TABLE IN SQL-CONTEXT
    taxi_df_test_cleaned.registerTempTable("taxi_test")
    
    # PRINT HOW MUCH TIME IT TOOK TO RUN THE CELL
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 

**RESULTADO:**

Tiempo necesario para ejecutar encima de celda: 46.37 segundos


## <a name="prepare-data-for-scoring-in-spark"></a>Preparar los datos para el motor de puntuación 

Esta sección muestra cómo indizar, codificar y escalar características por categorías para prepararlos para su uso en algoritmos MLlib supervisado aprendizaje para la clasificación y regresión.

### <a name="feature-transformation-index-and-encode-categorical-features-for-input-into-models-for-scoring"></a>Transformación de características: indizar y codificar características por categorías para la entrada en modelos de puntuación 

Esta sección muestra cómo indizar datos por categorías con un `StringIndexer` y codificar características con `OneHotEncoder` en los modelos de entrada.

La [StringIndexer](http://spark.apache.org/docs/latest/ml-features.html#stringindexer) codifica una columna de cadena de etiquetas a una columna de índices de etiqueta. Los índices se ordenan por frecuencias de etiqueta. 

La [OneHotEncoder](http://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.OneHotEncoder.html#sklearn.preprocessing.OneHotEncoder) asigna una columna de índices de etiqueta a una columna de vectores binarios, con al menos un solo uno valor. Esta codificación permite algoritmos espera continuas características de valores, como regresión logística, que se aplique a las características por categoría.
    
    #INDEX AND ONE-HOT ENCODE CATEGORICAL FEATURES

    # RECORD START TIME
    timestart = datetime.datetime.now()
    
    # LOAD PYSPARK LIBRARIES
    from pyspark.ml.feature import OneHotEncoder, StringIndexer, VectorAssembler, VectorIndexer
    
    # CREATE FOUR BUCKETS FOR TRAFFIC TIMES
    sqlStatement = """
        SELECT *,
        CASE
         WHEN (pickup_hour <= 6 OR pickup_hour >= 20) THEN "Night" 
         WHEN (pickup_hour >= 7 AND pickup_hour <= 10) THEN "AMRush" 
         WHEN (pickup_hour >= 11 AND pickup_hour <= 15) THEN "Afternoon"
         WHEN (pickup_hour >= 16 AND pickup_hour <= 19) THEN "PMRush"
        END as TrafficTimeBins
        FROM taxi_test 
    """
    taxi_df_test_with_newFeatures = sqlContext.sql(sqlStatement)
    
    # CACHE DATA-FRAME IN MEMORY & MATERIALIZE DF IN MEMORY
    taxi_df_test_with_newFeatures.cache()
    taxi_df_test_with_newFeatures.count()
    
    # INDEX AND ONE-HOT ENCODING
    stringIndexer = StringIndexer(inputCol="vendor_id", outputCol="vendorIndex")
    model = stringIndexer.fit(taxi_df_test_with_newFeatures) # Input data-frame is the cleaned one from above
    indexed = model.transform(taxi_df_test_with_newFeatures)
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
    
    # INDEX AND ENCODE TRAFFIC TIME BINS
    stringIndexer = StringIndexer(inputCol="TrafficTimeBins", outputCol="TrafficTimeBinsIndex")
    model = stringIndexer.fit(encoded3)
    indexed = model.transform(encoded3)
    encoder = OneHotEncoder(dropLast=False, inputCol="TrafficTimeBinsIndex", outputCol="TrafficTimeBinsVec")
    encodedFinal = encoder.transform(indexed)
    
    # PRINT HOW MUCH TIME IT TOOK TO RUN THE CELL
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 

**RESULTADO:**

Tiempo necesario para ejecutar encima de celda: 5.37 segundos


### <a name="create-rdd-objects-with-feature-arrays-for-input-into-models"></a>Crear objetos RDD con matrices de característica para la entrada en modelos

Esta sección contiene código que muestra cómo indizar datos de texto por categoría como un objeto RDD y caliente uno codificar por lo que puede utilizar para entrenar y probar MLlib de regresión logística y modelos de árbol. Los datos indizados se almacenan en objetos de [Conjunto de datos de distribuido flexibles (RDD)](http://spark.apache.org/docs/latest/api/java/org/apache/spark/rdd/RDD.html) . Se trata de la extracción básica en el motor. Un objeto RDD representa una colección inmutable dividida de elementos que pueden funcionar en paralelo con motor.

También contiene código que muestra cómo escalar datos con la `StandardScalar` proporcionado por MLlib para su uso en con estocástico degradado descenso (SGD), un algoritmo popular para una amplia gama de modelos de aprendizaje del equipo de formación de regresión lineal. La [StandardScaler](https://spark.apache.org/docs/latest/api/python/pyspark.mllib.html#pyspark.mllib.feature.StandardScaler) se usa para ajustar el tamaño de las características de variación de unidad. Característica de ajuste de escala, también se conoce como normalización de datos, garantiza que funciones con valores muy dispersos son no dada excesivo valorar en función de objetivos. 


    # CREATE RDD OBJECTS WITH FEATURE ARRAYS FOR INPUT INTO MODELS

    # RECORD START TIME
    timestart = datetime.datetime.now()

    # IMPORT LIBRARIES
    from pyspark.mllib.linalg import Vectors
    from pyspark.mllib.feature import StandardScaler, StandardScalerModel
    from pyspark.mllib.util import MLUtils
    from numpy import array
    
    # INDEXING CATEGORICAL TEXT FEATURES FOR INPUT INTO TREE-BASED MODELS
    def parseRowIndexingBinary(line):
        features = np.array([line.paymentIndex, line.vendorIndex, line.rateIndex, line.TrafficTimeBinsIndex,
                             line.pickup_hour, line.weekday, line.passenger_count, line.trip_time_in_secs, 
                             line.trip_distance, line.fare_amount])
        return  features
    
    # ONE-HOT ENCODING OF CATEGORICAL TEXT FEATURES FOR INPUT INTO LOGISTIC RERESSION MODELS
    def parseRowOneHotBinary(line):
        features = np.concatenate((np.array([line.pickup_hour, line.weekday, line.passenger_count,
                                            line.trip_time_in_secs, line.trip_distance, line.fare_amount]), 
                                            line.vendorVec.toArray(), line.rateVec.toArray(), 
                                            line.paymentVec.toArray(), line.TrafficTimeBinsVec.toArray()), axis=0)
        return  features
    
    # ONE-HOT ENCODING OF CATEGORICAL TEXT FEATURES FOR INPUT INTO TREE-BASED MODELS
    def parseRowIndexingRegression(line):
        features = np.array([line.paymentIndex, line.vendorIndex, line.rateIndex, line.TrafficTimeBinsIndex, 
                             line.pickup_hour, line.weekday, line.passenger_count, line.trip_time_in_secs, 
                             line.trip_distance, line.fare_amount])
        return  features
    
    # INDEXING CATEGORICAL TEXT FEATURES FOR INPUT INTO LINEAR REGRESSION MODELS
    def parseRowOneHotRegression(line):
        features = np.concatenate((np.array([line.pickup_hour, line.weekday, line.passenger_count,
                                            line.trip_time_in_secs, line.trip_distance, line.fare_amount]), 
                                            line.vendorVec.toArray(), line.rateVec.toArray(), 
                                            line.paymentVec.toArray(), line.TrafficTimeBinsVec.toArray()), axis=0)
        return  features

    # FOR BINARY CLASSIFICATION TRAINING AND TESTING
    indexedTESTbinary = encodedFinal.map(parseRowIndexingBinary)
    oneHotTESTbinary = encodedFinal.map(parseRowOneHotBinary)
    
    # FOR REGRESSION CLASSIFICATION TRAINING AND TESTING
    indexedTESTreg = encodedFinal.map(parseRowIndexingRegression)
    oneHotTESTreg = encodedFinal.map(parseRowOneHotRegression)
    
    # SCALING FEATURES FOR LINEARREGRESSIONWITHSGD MODEL
    scaler = StandardScaler(withMean=False, withStd=True).fit(oneHotTESTreg)
    oneHotTESTregScaled = scaler.transform(oneHotTESTreg)
    
    # CACHE RDDS IN MEMORY
    indexedTESTbinary.cache();
    oneHotTESTbinary.cache();
    indexedTESTreg.cache();
    oneHotTESTreg.cache();
    oneHotTESTregScaled.cache();
    
    # PRINT HOW MUCH TIME IT TOOK TO RUN THE CELL
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 

**RESULTADO:**

Tiempo necesario para ejecutar encima de celda: 11.72 segundos


## <a name="score-with-the-logistic-regression-model-and-save-output-to-blob"></a>Puntuación con el modelo de regresión logística y guardar los resultados de objetos binarios

El código de esta sección muestra cómo cargar un modelo de regresión logística que se ha guardado en el almacenamiento de blobs de Windows Azure y utilizar para predecir si no se paga una sugerencia de viaje taxi, puntuación con métricas de clasificación estándar y, a continuación, guardar y trazar los resultados al almacenamiento de blobs de Windows. Los resultados puntuados se almacenan en objetos RDD. 


    # SCORE AND EVALUATE LOGISTIC REGRESSION MODEL

    # RECORD START TIME
    timestart = datetime.datetime.now()
    
    # IMPORT LIBRARIES
    from pyspark.mllib.classification import LogisticRegressionModel
    
    ## LOAD SAVED MODEL
    savedModel = LogisticRegressionModel.load(sc, logisticRegFileLoc)
    predictions = oneHotTESTbinary.map(lambda features: (float(savedModel.predict(features))))
    
    ## SAVE SCORED RESULTS (RDD) TO BLOB
    datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
    logisticregressionfilename = "LogisticRegressionWithLBFGS_" + datestamp + ".txt";
    dirfilename = scoredResultDir + logisticregressionfilename;
    predictions.saveAsTextFile(dirfilename)
    
    
    # PRINT HOW MUCH TIME IT TOOK TO RUN THE CELL
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds";

**RESULTADO:**

Tiempo necesario para ejecutar encima de celda: 19.22 segundos


## <a name="score-a-linear-regression-model"></a>Puntuación un modelo de regresión lineal.

Hemos usado [LinearRegressionWithSGD](https://spark.apache.org/docs/latest/api/python/pyspark.mllib.html#pyspark.mllib.regression.LinearRegressionWithSGD) para formar un modelo de regresión lineal mediante estocástico degradado descenso (SGD) para la optimización de predecir la cantidad de sugerencia de pago. 

El código de esta sección muestra cómo cargar un modelo de regresión lineal desde el almacenamiento de blobs de Windows Azure, puntuación mediante variables de escala y, a continuación, guardar los resultados a los blobs.

    #SCORE LINEAR REGRESSION MODEL

    # RECORD START TIME
    timestart = datetime.datetime.now()
    
    #LOAD LIBRARIES
    from pyspark.mllib.regression import LinearRegressionWithSGD, LinearRegressionModel
    
    # LOAD MODEL AND SCORE USING ** SCALED VARIABLES **
    savedModel = LinearRegressionModel.load(sc, linearRegFileLoc)
    predictions = oneHotTESTregScaled.map(lambda features: (float(savedModel.predict(features))))
    
    # SAVE RESULTS
    datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
    linearregressionfilename = "LinearRegressionWithSGD_" + datestamp;
    dirfilename = scoredResultDir + linearregressionfilename;
    predictions.saveAsTextFile(dirfilename)
    
    # PRINT HOW MUCH TIME IT TOOK TO RUN THE CELL
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 


**RESULTADO:**

Tiempo necesario para ejecutar encima de celda: 16.63 segundos


## <a name="score-classification-and-regression-random-forest-models"></a>Puntuación clasificación y regresión modelos bosque aleatorios

El código de esta sección muestra cómo cargar la clasificación guardada y modelos de regresión aleatorio bosque guardado en el almacenamiento de blobs de Windows Azure, puntuación su rendimiento con clasificador estándar y medidas de regresión y, a continuación, guardar los resultados al almacenamiento de blobs.

[Aleatorio bosques](http://spark.apache.org/docs/latest/mllib-ensembles.html#Random-Forests) son conjuntos de árboles de decisión.  Combinan muchos árboles de decisión para reducir el riesgo de desbordamiento. Aleatorios bosques pueden controlar características por categorías, extender a la configuración de clasificación multiclass, no requieren la característica de ajuste de escala y puede capturar los errores de alineación e interacciones de características. Aleatorios bosques son uno de la máquina más éxito modelos de clasificación y regresión de aprendizaje.

[Spark.mllib](http://spark.apache.org/mllib/) admite bosques aleatorios de regresión, con características de por categorías y continuas y clasificación binaria y multiclass. 

    # SCORE RANDOM FOREST MODELS FOR CLASSIFICATION AND REGRESSION

    # RECORD START TIME
    timestart = datetime.datetime.now()

    #IMPORT MLLIB LIBRARIES 
    from pyspark.mllib.tree import RandomForest, RandomForestModel
    
    
    # CLASSIFICATION: LOAD SAVED MODEL, SCORE AND SAVE RESULTS BACK TO BLOB
    savedModel = RandomForestModel.load(sc, randomForestClassificationFileLoc)
    predictions = savedModel.predict(indexedTESTbinary)
    
    # SAVE RESULTS
    datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
    rfclassificationfilename = "RandomForestClassification_" + datestamp + ".txt";
    dirfilename = scoredResultDir + rfclassificationfilename;
    predictions.saveAsTextFile(dirfilename)
    

    # REGRESSION: LOAD SAVED MODEL, SCORE AND SAVE RESULTS BACK TO BLOB
    savedModel = RandomForestModel.load(sc, randomForestRegFileLoc)
    predictions = savedModel.predict(indexedTESTreg)
    
    # SAVE RESULTS
    datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
    rfregressionfilename = "RandomForestRegression_" + datestamp + ".txt";
    dirfilename = scoredResultDir + rfregressionfilename;
    predictions.saveAsTextFile(dirfilename)

    # PRINT HOW MUCH TIME IT TOOK TO RUN THE CELL
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds";

**RESULTADO:**

Tiempo necesario para ejecutar encima de celda: 31.07 segundos


## <a name="score-classification-and-regression-gradient-boosting-tree-models"></a>Clasificación de regresión degradado incremento árbol modelos y la puntuación

El código de esta sección muestra cómo cargar clasificación y regresión degradado incremento modelos de árbol de almacenamiento de blobs de Windows Azure, puntuación su rendimiento con clasificador estándar y medidas de regresión y, a continuación, guardar los resultados al almacenamiento de blobs. 

**Spark.mllib** admite GBTs de regresión, con características de por categorías y continuas y clasificación binario. 

[Árboles de aumentar degradados](http://spark.apache.org/docs/latest/ml-classification-regression.html#gradient-boosted-trees-gbts) (GBTs) son conjuntos de árboles de decisión. GBTs entrenar árboles de decisión repetidamente para minimizar una función de pérdida. GBTs puede controlar características por categorías, no requieren la característica de ajuste de escala y puede capturar los errores de alineación e interacciones de características. También se puede utilizar en una configuración de clasificación de multiclass.


    # SCORE GRADIENT BOOSTING TREE MODELS FOR CLASSIFICATION AND REGRESSION

    # RECORD START TIME
    timestart = datetime.datetime.now()

    #IMPORT MLLIB LIBRARIES
    from pyspark.mllib.tree import GradientBoostedTrees, GradientBoostedTreesModel
    
    # CLASSIFICATION: LOAD SAVED MODEL, SCORE AND SAVE RESULTS BACK TO BLOB

    #LOAD AND SCORE THE MODEL
    savedModel = GradientBoostedTreesModel.load(sc, BoostedTreeClassificationFileLoc)
    predictions = savedModel.predict(indexedTESTbinary)
    
    # SAVE RESULTS
    datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
    btclassificationfilename = "GradientBoostingTreeClassification_" + datestamp + ".txt";
    dirfilename = scoredResultDir + btclassificationfilename;
    predictions.saveAsTextFile(dirfilename)
    

    # REGRESSION: LOAD SAVED MODEL, SCORE AND SAVE RESULTS BACK TO BLOB

    # LOAD AND SCORE MODEL 
    savedModel = GradientBoostedTreesModel.load(sc, BoostedTreeRegressionFileLoc)
    predictions = savedModel.predict(indexedTESTreg)
    
    # SAVE RESULTS
    datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
    btregressionfilename = "GradientBoostingTreeRegression_" + datestamp + ".txt";
    dirfilename = scoredResultDir + btregressionfilename;
    predictions.saveAsTextFile(dirfilename)


    # PRINT HOW MUCH TIME IT TOOK TO RUN THE CELL
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 
    
**RESULTADO:**

Tiempo necesario para ejecutar encima de celda: 14.6 segundos


## <a name="clean-up-objects-from-memory-and-print-scored-file-locations"></a>Limpiar los objetos de la memoria e imprimir puntuación ubicaciones de archivo

    # UNPERSIST OBJECTS CACHED IN MEMORY
    taxi_df_test_cleaned.unpersist()
    indexedTESTbinary.unpersist();
    oneHotTESTbinary.unpersist();
    indexedTESTreg.unpersist();
    oneHotTESTreg.unpersist();
    oneHotTESTregScaled.unpersist();


    # PRINT OUT PATH TO SCORED OUTPUT FILES
    print "logisticRegFileLoc: " + logisticregressionfilename;
    print "linearRegFileLoc: " + linearregressionfilename;
    print "randomForestClassificationFileLoc: " + rfclassificationfilename;
    print "randomForestRegFileLoc: " + rfregressionfilename;
    print "BoostedTreeClassificationFileLoc: " + btclassificationfilename;
    print "BoostedTreeRegressionFileLoc: " + btregressionfilename;


**RESULTADO:**

logisticRegFileLoc: LogisticRegressionWithLBFGS_2016-05-0317_22_38.953814.txt

linearRegFileLoc: LinearRegressionWithSGD_2016-05-0317_22_58.878949

randomForestClassificationFileLoc: RandomForestClassification_2016-05-0317_23_15.939247.txt

randomForestRegFileLoc: RandomForestRegression_2016-05-0317_23_31.459140.txt

BoostedTreeClassificationFileLoc: GradientBoostingTreeClassification_2016-05-0317_23_49.648334.txt

BoostedTreeRegressionFileLoc: GradientBoostingTreeRegression_2016-05-0317_23_56.860740.txt



## <a name="consume-spark-models-through-a-web-interface"></a>Consumir modelos de motor mediante una interfaz web

Motor proporciona un mecanismo para enviar remotamente trabajos por lotes o interactivas consultas a través de una interfaz REST con un componente llamado a Livio. Livio está habilitado de forma predeterminada en el clúster HDInsight Spark. Para obtener más información sobre Livio vea: [trabajos de motor enviar remotamente mediante Livio](../hdinsight/hdinsight-apache-spark-livy-rest-interface.md). 

Puede usar a Livio remotamente enviar un trabajo por lotes la puntuación de un archivo que se almacena en un blobs de Windows Azure y, a continuación, escribe los resultados en otro blob. Para ello, cargar la secuencia de comandos de Python desde  
[Github](https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/Spark/Python/ConsumeGBNYCReg.py) al objeto binario del clúster motor. Puede usar una herramienta como **Microsoft Azure almacenamiento Explorer** o **AzCopy** para copiar la secuencia de comandos en el blob de clúster. En nuestro caso se habían cargado el script en ***wasb:///example/python/ConsumeGBNYCReg.py***.   


>[AZURE.NOTE] Las teclas de acceso necesita se encuentra en el portal de la cuenta de almacenamiento asociado con el clúster de motor. 


Una vez cargado en esta ubicación, esta secuencia de comandos se ejecuta en el clúster de motor en un contexto distribuido. El modelo de carga y ejecutar predicciones en archivos de entrada basados en el modelo.  

Para invocar esta secuencia de comandos de forma remota realizando una solicitud HTTPS/resto simple en Livio.  Aquí tiene un comando curl para construir la solicitud HTTP para invocar la secuencia de comandos de Python de forma remota. Reemplace CLUSTERLOGIN, CLUSTERPASSWORD, NOMBREDECLÚSTER con los valores apropiados para el clúster de motor.


    # CURL COMMAND TO INVOKE PYTHON SCRIPT WITH HTTP REQUEST

    curl -k --user "CLUSTERLOGIN:CLUSTERPASSWORD" -X POST --data "{\"file\": \"wasb:///example/python/ConsumeGBNYCReg.py\"}" -H "Content-Type: application/json" https://CLUSTERNAME.azurehdinsight.net/livy/batches

Puede usar cualquier idioma en el sistema remoto para llamar al trabajo de motor mediante Livio haciendo una llamada simple de HTTPS con autenticación básica.   


>[AZURE.NOTE] Sería conveniente usar la biblioteca de solicitudes de Python cuando se realiza esta llamada HTTP, pero no está instalado actualmente de forma predeterminada en las funciones de Azure. Bibliotecas HTTP anteriores se usa en su lugar.   


Este es el código de Python para la llamada de HTTP:

    #MAKE AN HTTPS CALL ON LIVY. 

    import os

    # OLDER HTTP LIBRARIES USED HERE INSTEAD OF THE REQUEST LIBRARY AS THEY ARE AVAILBLE BY DEFAULT
    import httplib, urllib, base64
    
    # REPLACE VALUE WITH ONES FOR YOUR SPARK CLUSTER
    host = '<spark cluster name>.azurehdinsight.net:443'
    username='<username>'
    password='<password>'
    
    #AUTHORIZATION
    conn = httplib.HTTPSConnection(host)
    auth = base64.encodestring('%s:%s' % (username, password)).replace('\n', '')
    headers = {'Content-Type': 'application/json', 'Authorization': 'Basic %s' % auth}
    
    # SPECIFY THE PYTHON SCRIPT TO RUN ON THE SPARK CLUSTER
    # IN THE FILE PARAMETER OF THE JSON POST REQUEST BODY
    r=conn.request("POST", '/livy/batches', '{"file": "wasb:///example/python/ConsumeGBNYCReg.py"}', headers )
    response = conn.getresponse().read()
    print(response)
    conn.close()


También puede agregar este código Python a las [Funciones de Azure](https://azure.microsoft.com/documentation/services/functions/) para activar el envío de trabajos de motor que puntuaciones blob basado en diversos eventos como un temporizador, creación o actualización de un blob. 

Si prefiere una experiencia de cliente libre de código, usar las [Aplicaciones de Azure lógica](https://azure.microsoft.com/documentation/services/app-service/logic/) para invocar el lote de motor puntuación define una acción de HTTP en el **Diseñador de lógica de aplicaciones** y estableciendo sus parámetros. 

- Desde el portal de Azure, cree una nueva aplicación lógica seleccionando **+ nuevo** -> **Web + Mobile** -> **Lógica de aplicación**. 
- Para abrir el **Diseñador de lógica de aplicaciones**, escriba el nombre de la aplicación de la lógica y el Plan de servicio de aplicación.
- Seleccione una acción de HTTP y escriba los parámetros que se muestra en la siguiente ilustración:

![](./media/machine-learning-data-science-spark-model-consumption/spark-logica-app-client.png)


## <a name="whats-next"></a>¿Qué es el siguiente? 

**Validación cruzada y hyperparameter barrido**: vea [la exploración de datos y modelado de motor avanzadas](machine-learning-data-science-spark-advanced-data-exploration-modeling.md) sobre cómo pueden ser modelos capacitado mediante validación cruzada y hyper parámetro barrido.
