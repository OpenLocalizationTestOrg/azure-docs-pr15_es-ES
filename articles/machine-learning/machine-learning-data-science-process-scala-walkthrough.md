<properties
    pageTitle="Ciencia datos utilizando Scala y motor en Azure | Microsoft Azure"
    description="Cómo utilizar Scala para las tareas de aprendizaje de máquina supervisados con el motor scalable MLlib motor m paquetes y en un clúster de motor de HDInsight de Azure."  
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
    ms.date="08/01/2016"
    ms.author="bradsev;deguhath"/>


# <a name="data-science-using-scala-and-spark-on-azure"></a>Ciencia datos utilizando Scala y motor de Azure

En este artículo se muestra cómo usar Scala para las tareas de aprendizaje de máquina supervisados con el motor scalable MLlib motor m paquetes y en un clúster de motor de HDInsight de Azure. Le guiará a través de las tareas que constituyen el [proceso de ciencias de datos](http://aka.ms/datascienceprocess): recopilación de datos y obtener información detallada, visualización, ingeniería de la característica, modelado y consumo de modelo. Los modelos en el artículo incluyen regresión lineal y logística, aleatorios bosques y árboles aumenta de degradado (GBTs), además de dos tareas comunes de aprendizaje de máquina supervisadas:

- Problema de regresión: predicción de la cantidad de sugerencia ($) para un viaje de taxi
- Clasificación binario: predicción de sugerencia o ninguna sugerencia (1/0) para un viaje de taxi

El proceso de modelado requiere formación y evaluación de un conjunto de datos de prueba y mediciones de precisión relevantes. En este artículo, aprenderá cómo almacenar estos modelos de almacenamiento de blobs de Windows Azure y cómo puntuación y evaluar su rendimiento predictiva. Este artículo también incluye los temas más avanzados de cómo optimizar modelos mediante validación cruzada y hyper parámetro barrido. Los datos que se utiliza están un ejemplo del 2013 Nueva York taxi viaje y tarifa conjunto de datos disponible en GitHub.

[Scala](http://www.scala-lang.org/), un lenguaje basado en la máquina virtual de Java, se integra conceptos del lenguaje orientado a objetos y funcionales. Es un lenguaje scalable que es adecuado para el procesamiento distribuido en la nube y a continuación, se ejecuta en Azure motor clústeres.

[Motor](http://spark.apache.org/) es un marco de procesamiento en paralelo de código abierto que admite procesamiento en memoria para mejorar el rendimiento de aplicaciones de análisis de datos grande. El motor de procesamiento de motor integrado para velocidad, facilidad de uso y análisis sofisticados. Capacidades de cálculo distribuidas en memoria del motor que sea una buena opción para algoritmos iterativos en cálculos de aprendizaje y gráfico de equipo. El paquete de [spark.ml](http://spark.apache.org/docs/latest/ml-guide.html) proporciona un conjunto uniforme de alto nivel API creados a partir de datos marcos que pueden ayudarle a crean y ajustar práctico máquina canalizaciones de aprendizaje. [MLlib](http://spark.apache.org/mllib/) es la biblioteca de aprendizaje de máquina scalable del motor, que ofrece capacidades de modelado a este entorno distribuido.

[Motor de HDInsight](../hdinsight/hdinsight-apache-spark-overview.md) es la oferta hospedado Azure de motor de código abierto. También se incluye compatibilidad para Jupyter Scala blocs de notas en el clúster de motor y se puede ejecutar consultas interactivas de SQL de motor para transformar, filtrar y visualizar datos almacenados en el almacenamiento de blobs de Windows Azure. Los fragmentos de código Scala en este artículo que proporcionan las soluciones y mostrar la trazados relevantes para visualizar los datos que se ejecutan en blocs de notas Jupyter instalados en los clústeres motor. Los pasos de modelado en estos temas tienen código que muestra cómo entrenar, evaluar, guardar y consumir cada tipo de modelo.

Los pasos de configuración y el código de este artículo son para Azure HDInsight 3.4 motor 1,6. Sin embargo, el código de este artículo y en el [Bloc de notas de Scala Jupyter](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/Scala/Exploration%20Modeling%20and%20Scoring%20using%20Scala.ipynb) son genérico y debería funcionar en cualquier clúster de motor. Los pasos de configuración y administración de clúster pueden ser ligeramente diferentes de lo que se muestra en este artículo si no usa HDInsight Spark.

> [AZURE.NOTE] Para un tema que muestra cómo utilizar Python en lugar de Scala para completar tareas de un proceso de ciencias datos-to-end, vea [Ciencias de datos mediante el motor en HDInsight de Azure](machine-learning-data-science-spark-overview.md).


## <a name="prerequisites"></a>Requisitos previos

-   Debe tener una suscripción de Azure. Si ya tiene una, [Obtenga una versión de prueba gratuita de Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

-   Necesita un clúster de Azure HDInsight 3.4 motor 1,6 para completar los siguientes procedimientos. Para crear un clúster, consulte las instrucciones de [Introducción: crear el motor de Apache en Azure HDInsight](../hdinsight/hdinsight-apache-spark-jupyter-spark-sql.md). Establecer el tipo de clúster y la versión en el menú **Seleccionar tipo de clúster** .

![Configuración del tipo de clúster HDInsight](./media/machine-learning-data-science-process-scala-walkthrough/spark-cluster-on-portal.png)


>[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]


Para obtener una descripción de los datos de viaje de Nueva York taxi e instrucciones sobre cómo ejecutar el código de un bloc de notas de Jupyter en el clúster de motor, vea las secciones correspondientes en la [Introducción de datos ciencia con motor en HDInsight de Azure](machine-learning-data-science-spark-overview.md).  


## <a name="execute-scala-code-from-a-jupyter-notebook-on-the-spark-cluster"></a>Ejecutar el código de Scala de un bloc de notas de Jupyter en el clúster de motor

Puede iniciar un bloc de notas de Jupyter desde el portal de Azure. Encontrar el clúster de motor en el escritorio y, a continuación, haga clic en él para introducir la página de administración para el clúster. A continuación, haga clic en **Paneles de clúster**y, a continuación, haga clic en **Bloc de notas de Jupyter** para abrir el Bloc de notas asociado con el clúster de motor.

![Panel de clúster y blocs de notas de Jupyter](./media/machine-learning-data-science-process-scala-walkthrough/spark-jupyter-on-portal.png)

También puede obtener acceso a blocs de notas de Jupyter en https://&lt;nombreDeClúster&gt;.azurehdinsight.net/jupyter. Reemplazar *nombreDeClúster* con el nombre del clúster. Necesita la contraseña de su cuenta de administrador tener acceso a los blocs de notas de Jupyter.

![Vaya a los blocs de notas Jupyter usando el nombre de clúster](./media/machine-learning-data-science-process-scala-walkthrough/spark-jupyter-notebook.png)

Seleccione **Scala** para ver un directorio que tiene algunos ejemplos de los blocs de notas preestablecidas que utilizan la API PySpark. El modelado de exploración y puntuación con Bloc de notas de Scala.ipynb que contiene los ejemplos de código para este conjunto de temas de motor está disponible en [GitHub](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/Spark/Scala).


Puede cargar el Bloc de notas directamente desde GitHub en el servidor Jupyter Bloc de notas en el clúster de motor. En la página de inicio Jupyter, haga clic en el botón **cargar** . En el Explorador de archivos, pegue la dirección URL (contenido) de GitHub del Bloc de notas Scala y, a continuación, haga clic en **Abrir**. El Bloc de notas Scala está disponible en la siguiente URL:

[Exploration-Modeling-and-Scoring-using-Scala.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/Scala/Exploration-Modeling-and-Scoring-using-Scala.ipynb)

## <a name="setup-preset-spark-and-hive-contexts-spark-magics-and-spark-libraries"></a>Configuración: Motor de programación y subárbol contextos, magics motor y bibliotecas de motor

### <a name="preset-spark-and-hive-contexts"></a>Contextos motor y subárbol preestablecidos

    # SET THE START TIME
    import java.util.Calendar
    val beginningTime = Calendar.getInstance().getTime()


Los kernels motor que se proporcionan con blocs de notas Jupyter tienen preestablecidas contextos. No es necesario establecer explícitamente el motor o desarrollan subárbol contextos antes de empezar a trabajar con la aplicación. Los contextos preestablecidos son:

- `sc`para SparkContext
- `sqlContext`para HiveContext


### <a name="spark-magics"></a>Motor magics

El núcleo motor proporciona algunos predefinido "magics", que son comandos especiales que se pueden llamar con `%%`. Dos de estos comandos se usan en los siguientes ejemplos de código.

- `%%local`Especifica que se ejecutará el código de las siguientes líneas localmente. El código debe ser código Scala válido.
- `%%sql -o <variable name>`ejecuta una consulta de subárbol con `sqlContext`. Si el `-o` parámetro se pasa, el resultado de la consulta se conserva en el `%%local` Scala contexto como una trama de datos de motor.

Para obtener más información sobre los kernels de blocs de notas de Jupyter y sus predefinido "magics" que llame con `%%` (por ejemplo, `%%local`), vea [Kernels disponibles para los blocs de notas de Jupyter con HDInsight motor Linux clústeres en HDInsight](../hdinsight/hdinsight-apache-spark-jupyter-notebook-kernels.md).


### <a name="import-libraries"></a>Bibliotecas de importación

Importar el motor, MLlib y otras bibliotecas que necesitará mediante el siguiente código.

    # IMPORT SPARK AND JAVA LIBRARIES
    import org.apache.spark.sql.SQLContext
    import org.apache.spark.sql.functions._
    import java.text.SimpleDateFormat
    import java.util.Calendar
    import sqlContext.implicits._
    import org.apache.spark.sql.Row

    # IMPORT SPARK SQL FUNCTIONS
    import org.apache.spark.sql.types.{StructType, StructField, StringType, IntegerType, FloatType, DoubleType}
    import org.apache.spark.sql.functions.rand

    # IMPORT SPARK ML FUNCTIONS
    import org.apache.spark.ml.Pipeline
    import org.apache.spark.ml.feature.{StringIndexer, VectorAssembler, OneHotEncoder, VectorIndexer, Binarizer}
    import org.apache.spark.ml.tuning.{ParamGridBuilder, TrainValidationSplit, CrossValidator}
    import org.apache.spark.ml.regression.{LinearRegression, LinearRegressionModel, RandomForestRegressor, RandomForestRegressionModel, GBTRegressor, GBTRegressionModel}
    import org.apache.spark.ml.classification.{LogisticRegression, LogisticRegressionModel, RandomForestClassifier, RandomForestClassificationModel, GBTClassifier, GBTClassificationModel}
    import org.apache.spark.ml.evaluation.{BinaryClassificationEvaluator, RegressionEvaluator, MulticlassClassificationEvaluator}

    # IMPORT SPARK MLLIB FUNCTIONS
    import org.apache.spark.mllib.linalg.{Vector, Vectors}
    import org.apache.spark.mllib.util.MLUtils
    import org.apache.spark.mllib.classification.{LogisticRegressionWithLBFGS, LogisticRegressionModel}
    import org.apache.spark.mllib.regression.{LabeledPoint, LinearRegressionWithSGD, LinearRegressionModel}
    import org.apache.spark.mllib.tree.{GradientBoostedTrees, RandomForest}
    import org.apache.spark.mllib.tree.configuration.BoostingStrategy
    import org.apache.spark.mllib.tree.model.{GradientBoostedTreesModel, RandomForestModel, Predict}
    import org.apache.spark.mllib.evaluation.{BinaryClassificationMetrics, MulticlassMetrics, RegressionMetrics}

    # SPECIFY SQLCONTEXT
    val sqlContext = new SQLContext(sc)


## <a name="data-ingestion"></a>Recopilación de datos

El primer paso del proceso de ciencias de datos es recopilar los datos que desea analizar. Introducir los datos de orígenes externos o sistemas donde reside en su entorno de exploración y modelado de datos. En este artículo, los datos que recopile están un ejemplo de 0,1% combinados del archivo de viaje y tarifa taxi (almacenado como un archivo .tsv). El entorno de exploración y modelado de datos es motor. Esta sección contiene el código para completar la serie de tareas siguiente:

1. Definir rutas de directorios para el almacenamiento de datos y modelo.
2. Leer en el conjunto de datos de entrada (almacenado como un archivo .tsv).
3. Definir un esquema para los datos y limpiar los datos.
4. Crear un marco de datos limpios y almacenarla en memoria caché.
5. Registrar los datos como una tabla temporal en SQLContext.
6. La tabla de consulta e importar los resultados en un marco de datos.


### <a name="set-directory-paths-for-storage-locations-in-azure-blob-storage"></a>Definir rutas de directorios para ubicaciones de almacenamiento en el almacenamiento de blobs de Windows Azure

Motor puede leer y escribir en el almacenamiento de blobs de Windows Azure. Puede utilizar el motor para procesar cualquiera de los datos existentes y volver a almacenar los resultados en el almacenamiento de blobs de Windows.

Para guardar modelos o archivos de almacenamiento de blobs, debe especificar correctamente la ruta de acceso. Referencia del contenedor predeterminado conectado al clúster motor utilizando una ruta de acceso que comienza con `wasb:///`. Hacer referencia a otras ubicaciones mediante `wasb://`.

El ejemplo siguiente especifica la ubicación de los datos de entrada para que puedan leer y la ruta de acceso al almacenamiento de blobs de que está conectado al clúster motor donde se guardará el modelo.

    # SET PATHS TO DATA AND MODEL FILE LOCATIONS
    # INGEST DATA AND SPECIFY HEADERS FOR COLUMNS
    val taxi_train_file = sc.textFile("wasb://mllibwalkthroughs@cdspsparksamples.blob.core.windows.net/Data/NYCTaxi/JoinedTaxiTripFare.Point1Pct.Train.tsv")
    val header = taxi_train_file.first;

    # SET THE MODEL STORAGE DIRECTORY PATH
    # NOTE THAT THE FINAL BACKSLASH IN THE PATH IS REQUIRED.
    val modelDir = "wasb:///user/remoteuser/NYCTaxi/Models/";


### <a name="import-data-create-an-rdd-and-define-a-data-frame-according-to-the-schema"></a>Importar datos, crear un diseño dirigido por responsabilidades y definir una trama de datos según el esquema

    # RECORD THE START TIME
    val starttime = Calendar.getInstance().getTime()

    # DEFINE THE SCHEMA BASED ON THE HEADER OF THE FILE
    val sqlContext = new SQLContext(sc)
    val taxi_schema = StructType(
        Array(
            StructField("medallion", StringType, true),
            StructField("hack_license", StringType, true),
            StructField("vendor_id", StringType, true),
            StructField("rate_code", DoubleType, true),
            StructField("store_and_fwd_flag", StringType, true),
            StructField("pickup_datetime", StringType, true),
            StructField("dropoff_datetime", StringType, true),
            StructField("pickup_hour", DoubleType, true),
            StructField("pickup_week", DoubleType, true),
            StructField("weekday", DoubleType, true),
            StructField("passenger_count", DoubleType, true),
            StructField("trip_time_in_secs", DoubleType, true),
            StructField("trip_distance", DoubleType, true),
            StructField("pickup_longitude", DoubleType, true),
            StructField("pickup_latitude", DoubleType, true),
            StructField("dropoff_longitude", DoubleType, true),
            StructField("dropoff_latitude", DoubleType, true),
            StructField("direct_distance", StringType, true),
            StructField("payment_type", StringType, true),
            StructField("fare_amount", DoubleType, true),
            StructField("surcharge", DoubleType, true),
            StructField("mta_tax", DoubleType, true),
            StructField("tip_amount", DoubleType, true),
            StructField("tolls_amount", DoubleType, true),
            StructField("total_amount", DoubleType, true),
            StructField("tipped", DoubleType, true),
            StructField("tip_class", DoubleType, true)
            )
        )

    # CAST VARIABLES ACCORDING TO THE SCHEMA
    val taxi_temp = (taxi_train_file.map(_.split("\t"))
                            .filter((r) => r(0) != "medallion")
                            .map(p => Row(p(0), p(1), p(2),
                                p(3).toDouble, p(4), p(5), p(6), p(7).toDouble, p(8).toDouble, p(9).toDouble, p(10).toDouble,
                                p(11).toDouble, p(12).toDouble, p(13).toDouble, p(14).toDouble, p(15).toDouble, p(16).toDouble,
                                p(17), p(18), p(19).toDouble, p(20).toDouble, p(21).toDouble, p(22).toDouble,
                                p(23).toDouble, p(24).toDouble, p(25).toDouble, p(26).toDouble)))


    # CREATE AN INITIAL DATA FRAME AND DROP COLUMNS, AND THEN CREATE A CLEANED DATA FRAME BY FILTERING FOR UNWANTED VALUES OR OUTLIERS
    val taxi_train_df = sqlContext.createDataFrame(taxi_temp, taxi_schema)

    val taxi_df_train_cleaned = (taxi_train_df.drop(taxi_train_df.col("medallion"))
            .drop(taxi_train_df.col("hack_license")).drop(taxi_train_df.col("store_and_fwd_flag"))
            .drop(taxi_train_df.col("pickup_datetime")).drop(taxi_train_df.col("dropoff_datetime"))
            .drop(taxi_train_df.col("pickup_longitude")).drop(taxi_train_df.col("pickup_latitude"))
            .drop(taxi_train_df.col("dropoff_longitude")).drop(taxi_train_df.col("dropoff_latitude"))
            .drop(taxi_train_df.col("surcharge")).drop(taxi_train_df.col("mta_tax"))
            .drop(taxi_train_df.col("direct_distance")).drop(taxi_train_df.col("tolls_amount"))
            .drop(taxi_train_df.col("total_amount")).drop(taxi_train_df.col("tip_class"))
            .filter("passenger_count > 0 and passenger_count < 8 AND payment_type in ('CSH', 'CRD') AND tip_amount >= 0 AND tip_amount < 30 AND fare_amount >= 1 AND fare_amount < 150 AND trip_distance > 0 AND trip_distance < 100 AND trip_time_in_secs > 30 AND trip_time_in_secs < 7200"));

    # CACHE AND MATERIALIZE THE CLEANED DATA FRAME IN MEMORY
    taxi_df_train_cleaned.cache()
    taxi_df_train_cleaned.count()

    # REGISTER THE DATA FRAME AS A TEMPORARY TABLE IN SQLCONTEXT
    taxi_df_train_cleaned.registerTempTable("taxi_train")

    # GET THE TIME TO RUN THE CELL
    val endtime = Calendar.getInstance().getTime()
    val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
    println("Time taken to run the above cell: " + elapsedtime + " seconds.");


**Resultado:**

Tiempo de ejecución de la celda: 8 segundos.


### <a name="query-the-table-and-import-results-in-a-data-frame"></a>La tabla de consulta e importar resultados en un marco de datos

A continuación, consulte la tabla para tarifa, pasajeros y datos de sugerencia; filtrar los datos dañados y remotas; e imprimir varias filas.

    # QUERY THE DATA
    val sqlStatement = """
        SELECT fare_amount, passenger_count, tip_amount, tipped
        FROM taxi_train
        WHERE passenger_count > 0 AND passenger_count < 7
        AND fare_amount > 0 AND fare_amount < 200
        AND payment_type in ('CSH', 'CRD')
        AND tip_amount > 0 AND tip_amount < 25
    """
    val sqlResultsDF = sqlContext.sql(sqlStatement)

    # SHOW ONLY THE TOP THREE ROWS
    sqlResultsDF.show(3)

**Resultado:**

fare_amount|passenger_count|tip_amount|superpuesto
-----------|---------------|----------|------
       13.5|            1.0|       2,9|   1.0
       16,0|            2.0|       3.4|   1.0
       10.5|            2.0|       1.0|   1.0


## <a name="data-exploration-and-visualization"></a>Visualización y la exploración de datos

Después de introducir los datos en motor, es el siguiente paso del proceso de ciencias de datos obtener un conocimiento más profundo de los datos a través de exploración y visualización. En esta sección, examina los datos de taxi mediante consultas SQL. A continuación, importar los resultados en un intervalo de datos para trazar las variables de destino y las características posibles para inspección visual mediante la característica de visualización automática de Jupyter.

### <a name="use-local-and-sql-magic-to-plot-data"></a>Usar local y mágico SQL para trazar datos

De forma predeterminada, el resultado de cualquier fragmento de código que se ejecute desde un bloc de notas de Jupyter está disponible en el contexto de la sesión en la que se almacena en los nodos de trabajo. Si desea guardar un viaje a los nodos de trabajo para cada cálculo y si todos los datos que necesita para el cálculo está disponible localmente en el nodo del servidor Jupyter (que es el nodo principal), puede usar el `%%local` mágico para ejecutar el fragmento de código en el servidor Jupyter.

- **Magia SQL** (`%%sql`). El núcleo HDInsight Spark admite consultas de HiveQL de fácil incorporada en SQLContext. La (`-o VARIABLE_NAME`) argumento conserva el resultado de la consulta SQL como una trama de datos de Pandas en el servidor Jupyter. Esto significa que esté disponible en modo local.
- `%%local`**mágico**. La `%%local` mágico ejecuta el código localmente en el servidor de Jupyter, que es el nodo principal del clúster HDInsight. Normalmente, se utiliza `%%local` mágico junto con la `%%sql` mágico con la `-o` parámetro. La `-o` parámetro desea conservar el resultado de la consulta SQL localmente y, a continuación `%%local` mágico activaría el siguiente conjunto de fragmento de código para ejecutar localmente en el resultado de las consultas SQL que se almacena localmente.

### <a name="query-the-data-by-using-sql"></a>Consultar los datos mediante SQL
Esta consulta recupera los viajes taxi por importe de tarifa, recuento de pasajeros e importe de sugerencia.

    # RUN THE SQL QUERY
    %%sql -q -o sqlResults
    SELECT fare_amount, passenger_count, tip_amount, tipped FROM taxi_train WHERE passenger_count > 0 AND passenger_count < 7 AND fare_amount > 0 AND fare_amount < 200 AND payment_type in ('CSH', 'CRD') AND tip_amount > 0 AND tip_amount < 25

En el código siguiente, la `%%local` mágico crea un marco de datos locales, sqlResults. Puede usar sqlResults para trazar mediante matplotlib.

> [AZURE.TIP] Mágico local se usa varias veces en este artículo. Si el conjunto de datos es grande, por favor de ejemplo para crear una trama de datos que puede caber en la memoria local.

### <a name="plot-the-data"></a>Trazar los datos

Puede trazar usando código Python después de la trama de datos en un contexto local como una trama de datos de Pandas.

    # RUN THE CODE LOCALLY ON THE JUPYTER SERVER
    %%local

    # USE THE JUPYTER AUTO-PLOTTING FEATURE TO CREATE INTERACTIVE FIGURES.
    # CLICK THE TYPE OF PLOT TO GENERATE (LINE, AREA, BAR, ETC.)
    sqlResults


 El núcleo motor automáticamente visualiza los resultados de las consultas SQL (HiveQL) después de ejecutar el código. Puede elegir entre varios tipos de visualizaciones:
 
- Tabla
- Gráfico circular
- Línea
- Área
- Barra

Este es el código para trazar los datos:

    # RUN THE CODE LOCALLY ON THE JUPYTER SERVER AND IMPORT LIBRARIES
    %%local
    import matplotlib.pyplot as plt
    %matplotlib inline

    # PLOT TIP BY PAYMENT TYPE AND PASSENGER COUNT
    ax1 = sqlResults[['tip_amount']].plot(kind='hist', bins=25, facecolor='lightblue')
    ax1.set_title('Tip amount distribution')
    ax1.set_xlabel('Tip Amount ($)')
    ax1.set_ylabel('Counts')
    plt.suptitle('')
    plt.show()

    # PLOT TIP BY PASSENGER COUNT
    ax2 = sqlResults.boxplot(column=['tip_amount'], by=['passenger_count'])
    ax2.set_title('Tip amount by Passenger count')
    ax2.set_xlabel('Passenger count')
    ax2.set_ylabel('Tip Amount ($)')
    plt.suptitle('')
    plt.show()

    # PLOT TIP AMOUNT BY FARE AMOUNT; SCALE POINTS BY PASSENGER COUNT
    ax = sqlResults.plot(kind='scatter', x= 'fare_amount', y = 'tip_amount', c='blue', alpha = 0.10, s=5*(sqlResults.passenger_count))
    ax.set_title('Tip amount by Fare amount')
    ax.set_xlabel('Fare Amount ($)')
    ax.set_ylabel('Tip Amount ($)')
    plt.axis([-2, 80, -2, 20])
    plt.show()


**Resultado:**

![Sugerencia cantidad histograma](./media/machine-learning-data-science-process-scala-walkthrough/plot-tip-amount-histogram.png)

![Sugerencia cantidad por número de pasajeros](./media/machine-learning-data-science-process-scala-walkthrough/plot-tip-amount-by-passenger-count.png)

![Cantidad por la cantidad de tarifa de ideas](./media/machine-learning-data-science-process-scala-walkthrough/plot-tip-amount-by-fare-amount.png)


## <a name="create-features-and-transform-features-and-then-prep-data-for-input-into-modeling-functions"></a>Crear características y transformar características y, a continuación, preparar datos de entrada en funciones de modelado

Para las funciones de modelado basadas en el árbol de motor m y MLlib, debe preparar el destino y características mediante una variedad de técnicas, como binning, indización, caliente una codificación y vectorización. Estos son los procedimientos para seguir en esta sección:

1. Crear una nueva característica **binning** horas en depósitos de tiempo de tráfico.
2. Aplicar **la indización y caliente una codificación** características por categoría.
3. **Muestra y dividir el conjunto de datos** en fracciones de aprendizaje y prueba.
4. **Características y especificar variable de formación**y crear indizados o caliente uno codificado de formación y pruebas de punto de entrada etiquetada flexibles distribuyan conjuntos de datos (RDDs) o tramas de datos.
5. Automáticamente **clasificar y vectorize características y destinos** para usar como entradas para modelos de aprendizaje del equipo.


### <a name="create-a-new-feature-by-binning-hours-into-traffic-time-buckets"></a>Crear una nueva característica binning horas en depósitos de tiempo de tráfico

Este código muestra cómo crear una nueva característica binning horas en depósitos de tiempo de tráfico y cómo el marco de datos resultantes en la memoria en caché. Donde marcos RDDs y los datos se usan varias veces, almacenamiento en caché de clientes potenciales para los tiempos de ejecución mejorados. Por consiguiente, deberá caché RDDs y marcos de datos en varias fases de los procedimientos siguientes.

    # CREATE FOUR BUCKETS FOR TRAFFIC TIMES
    val sqlStatement = """
        SELECT *,
        CASE
         WHEN (pickup_hour <= 6 OR pickup_hour >= 20) THEN "Night"
         WHEN (pickup_hour >= 7 AND pickup_hour <= 10) THEN "AMRush"
         WHEN (pickup_hour >= 11 AND pickup_hour <= 15) THEN "Afternoon"
         WHEN (pickup_hour >= 16 AND pickup_hour <= 19) THEN "PMRush"
        END as TrafficTimeBins
        FROM taxi_train
    """
    val taxi_df_train_with_newFeatures = sqlContext.sql(sqlStatement)

    # CACHE THE DATA FRAME IN MEMORY AND MATERIALIZE THE DATA FRAME IN MEMORY
    taxi_df_train_with_newFeatures.cache()
    taxi_df_train_with_newFeatures.count()


### <a name="indexing-and-one-hot-encoding-of-categorical-features"></a>Indización y caliente una codificación de características por categoría

El modelado y predecir las funciones de MLlib requieren características con los datos de entrada por categorías indexado o codificado antes de usarla. Esta sección muestra cómo indizar o codificar características por categorías para la entrada en las funciones de modelado.

Debe indizar o codificar los modelos de diferentes formas, dependiendo del modelo. Por ejemplo, los modelos de regresión lineal y logística requieren caliente una codificación. Por ejemplo, se puede expandir una característica con tres categorías en tres columnas de la característica. Cada columna contiene 0 o 1, dependiendo de la categoría de una observación. MLlib proporciona la función [OneHotEncoder](http://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.OneHotEncoder.html#sklearn.preprocessing.OneHotEncoder) caliente una codificación. Este codificador asigna una columna de índices de etiqueta a una columna de vectores binarios con al menos un solo uno valor. Con esta codificación, se pueden aplicar algoritmos espera características con valores numéricas, como regresión logística, las características de categorías.

A continuación, transformar sólo cuatro variables para mostrar los ejemplos, que son cadenas de caracteres. También puede indizar otras variables representados por valores numéricos, como variables por categorías, como días de la semana.

Para la indización, use `StringIndexer()`y para caliente una codificación, use `OneHotEncoder()` funciones de MLlib. Este es el código de índice y codificar características por categorías:


    # CREATE INDEXES AND ONE-HOT ENCODED VECTORS FOR SEVERAL CATEGORICAL FEATURES

    # RECORD THE START TIME
    val starttime = Calendar.getInstance().getTime()

    # INDEX AND ENCODE VENDOR_ID
    val stringIndexer = new StringIndexer().setInputCol("vendor_id").setOutputCol("vendorIndex").fit(taxi_df_train_with_newFeatures)
    val indexed = stringIndexer.transform(taxi_df_train_with_newFeatures)
    val encoder = new OneHotEncoder().setInputCol("vendorIndex").setOutputCol("vendorVec")
    val encoded1 = encoder.transform(indexed)

    # INDEX AND ENCODE RATE_CODE
    val stringIndexer = new StringIndexer().setInputCol("rate_code").setOutputCol("rateIndex").fit(encoded1)
    val indexed = stringIndexer.transform(encoded1)
    val encoder = new OneHotEncoder().setInputCol("rateIndex").setOutputCol("rateVec")
    val encoded2 = encoder.transform(indexed)

    # INDEX AND ENCODE PAYMENT_TYPE
    val stringIndexer = new StringIndexer().setInputCol("payment_type").setOutputCol("paymentIndex").fit(encoded2)
    val indexed = stringIndexer.transform(encoded2)
    val encoder = new OneHotEncoder().setInputCol("paymentIndex").setOutputCol("paymentVec")
    val encoded3 = encoder.transform(indexed)

    # INDEX AND TRAFFIC TIME BINS
    val stringIndexer = new StringIndexer().setInputCol("TrafficTimeBins").setOutputCol("TrafficTimeBinsIndex").fit(encoded3)
    val indexed = stringIndexer.transform(encoded3)
    val encoder = new OneHotEncoder().setInputCol("TrafficTimeBinsIndex").setOutputCol("TrafficTimeBinsVec")
    val encodedFinal = encoder.transform(indexed)

    # GET THE TIME TO RUN THE CELL
    val endtime = Calendar.getInstance().getTime()
    val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
    println("Time taken to run the above cell: " + elapsedtime + " seconds.");


**Resultado:**

Tiempo de ejecución de la celda: 4 segundos.



### <a name="sample-and-split-the-data-set-into-training-and-test-fractions"></a>Muestra y dividir el conjunto de datos en fracciones de prueba y formación

Este código crea un sondeo de los datos (25%, en este ejemplo). Aunque muestreo no es necesario para este ejemplo por el tamaño del conjunto de datos, el artículo le muestra cómo puede muestrear para que sepa cómo usar su propio problemas cuando sea necesario. Cuando las muestras están grandes, se ahorrará tiempo significativo mientras entrenar modelos. A continuación, dividir los datos de ejemplo en un elemento de formación (75%, en este ejemplo) y un elemento de pruebas (25%, en este ejemplo) para usar en clasificación y modelado de regresión.

Agregar un número aleatorio (entre 0 y 1) para cada fila (en una columna "aleatorio") que se pueden usar para seleccionar pliegue validación cruzada durante el aprendizaje.


    # RECORD THE START TIME
    val starttime = Calendar.getInstance().getTime()

    # SPECIFY SAMPLING AND SPLITTING FRACTIONS
    val samplingFraction = 0.25;
    val trainingFraction = 0.75;
    val testingFraction = (1-trainingFraction);
    val seed = 1234;
    val encodedFinalSampledTmp = encodedFinal.sample(withReplacement = false, fraction = samplingFraction, seed = seed)
    val sampledDFcount = encodedFinalSampledTmp.count().toInt

    val generateRandomDouble = udf(() => {
        scala.util.Random.nextDouble
    })

    # ADD A RANDOM NUMBER FOR CROSS-VALIDATION
    val encodedFinalSampled = encodedFinalSampledTmp.withColumn("rand", generateRandomDouble());

    # SPLIT THE SAMPLED DATA FRAME INTO TRAIN AND TEST, WITH A RANDOM COLUMN ADDED FOR DOING CROSS-VALIDATION (SHOWN LATER)
    # INCLUDE A RANDOM COLUMN FOR CREATING CROSS-VALIDATION FOLDS
    val splits = encodedFinalSampled.randomSplit(Array(trainingFraction, testingFraction), seed = seed)
    val trainData = splits(0)
    val testData = splits(1)

    # GET THE TIME TO RUN THE CELL
    val endtime = Calendar.getInstance().getTime()
    val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
    println("Time taken to run the above cell: " + elapsedtime + " seconds.");


**Resultado:**

Tiempo de ejecución de la celda: 2 segundos.


### <a name="specify-training-variable-and-features-and-then-create-indexed-or-one-hot-encoded-training-and-testing-input-labeled-point-rdds-or-data-frames"></a>Especificar características y variable de formación y crear indizados o caliente uno codificado de aprendizaje y pruebas etiquetada marcos punto RDDs o datos de entrada

Esta sección contiene código que muestra cómo indizar datos de texto por categoría como un tipo de datos de punto etiquetada y codificar para usarla para entrenar y probar regresión logística de MLlib y otros modelos de clasificación. Objetos de punto etiquetada son RDDs que tienen formato de una forma que sea necesaria como entrada de datos por la mayoría de los algoritmos MLlib de aprendizaje. Un [punto de la etiqueta](https://spark.apache.org/docs/latest/mllib-data-types.html#labeled-point) es un vector local, denso o disperso, asociado con una respuesta de etiqueta.

En este código, especifique la variable de destino (dependientes) y las características para formar modelos. A continuación, cree indizados o caliente uno codificado de aprendizaje y pruebas etiquetada marcos punto RDDs o datos de entrada.


    # RECORD THE START TIME
    val starttime = Calendar.getInstance().getTime()

    # MAP NAMES OF FEATURES AND TARGETS FOR CLASSIFICATION AND REGRESSION PROBLEMS
    val featuresIndOneHot = List("paymentVec", "vendorVec", "rateVec", "TrafficTimeBinsVec", "pickup_hour", "weekday", "passenger_count", "trip_time_in_secs", "trip_distance", "fare_amount").map(encodedFinalSampled.columns.indexOf(_))
    val featuresIndIndex = List("paymentIndex", "vendorIndex", "rateIndex", "TrafficTimeBinsIndex", "pickup_hour", "weekday", "passenger_count", "trip_time_in_secs", "trip_distance", "fare_amount").map(encodedFinalSampled.columns.indexOf(_))

    # SPECIFY THE TARGET FOR CLASSIFICATION ('tipped') AND REGRESSION ('tip_amount') PROBLEMS
    val targetIndBinary = List("tipped").map(encodedFinalSampled.columns.indexOf(_))
    val targetIndRegression = List("tip_amount").map(encodedFinalSampled.columns.indexOf(_))

    # CREATE INDEXED LABELED POINT RDD OBJECTS
    val indexedTRAINbinary = trainData.rdd.map(r => LabeledPoint(r.getDouble(targetIndBinary(0).toInt), Vectors.dense(featuresIndIndex.map(r.getDouble(_)).toArray)))
    val indexedTESTbinary = testData.rdd.map(r => LabeledPoint(r.getDouble(targetIndBinary(0).toInt), Vectors.dense(featuresIndIndex.map(r.getDouble(_)).toArray)))
    val indexedTRAINreg = trainData.rdd.map(r => LabeledPoint(r.getDouble(targetIndRegression(0).toInt), Vectors.dense(featuresIndIndex.map(r.getDouble(_)).toArray)))
    val indexedTESTreg = testData.rdd.map(r => LabeledPoint(r.getDouble(targetIndRegression(0).toInt), Vectors.dense(featuresIndIndex.map(r.getDouble(_)).toArray)))

    # CREATE INDEXED DATA FRAMES THAT YOU CAN USE TO TRAIN BY USING SPARK ML FUNCTIONS
    val indexedTRAINbinaryDF = indexedTRAINbinary.toDF()
    val indexedTESTbinaryDF = indexedTESTbinary.toDF()
    val indexedTRAINregDF = indexedTRAINreg.toDF()
    val indexedTESTregDF = indexedTESTreg.toDF()

    # CREATE ONE-HOT ENCODED (VECTORIZED) DATA FRAMES THAT YOU CAN USE TO TRAIN BY USING SPARK ML FUNCTIONS
    val assemblerOneHot = new VectorAssembler().setInputCols(Array("paymentVec", "vendorVec", "rateVec", "TrafficTimeBinsVec", "pickup_hour", "weekday", "passenger_count", "trip_time_in_secs", "trip_distance", "fare_amount")).setOutputCol("features")
    val OneHotTRAIN = assemblerOneHot.transform(trainData)
    val OneHotTEST = assemblerOneHot.transform(testData)

    # GET THE TIME TO RUN THE CELL
    val endtime = Calendar.getInstance().getTime()
    val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
    println("Time taken to run the above cell: " + elapsedtime + " seconds.");


**Resultado:**

Tiempo de ejecución de la celda: 4 segundos.


### <a name="automatically-categorize-and-vectorize-features-and-targets-to-use-as-inputs-for-machine-learning-models"></a>Clasificar automáticamente y vectorize características y destinos para usar como entradas para modelos de aprendizaje

Use motor m para clasificar el destino y características que se utilizarán en las funciones de modelado de árbol. El código realiza dos tareas:

-   Crea un destino binario de clasificación asignando un valor de 0 o 1 para cada punto de datos entre 0 y 1 utilizando un valor de umbral de 0,5.
- Clasifica automáticamente características. Si el número de valores numéricos distintos para cualquier característica es menor que 32, se clasifica dicha característica.

Este es el código para estos dos tareas.

    # CATEGORIZE FEATURES AND BINARIZE THE TARGET FOR THE BINARY CLASSIFICATION PROBLEM

    # TRAIN DATA
    val indexer = new VectorIndexer().setInputCol("features").setOutputCol("featuresCat").setMaxCategories(32)
    val indexerModel = indexer.fit(indexedTRAINbinaryDF)
    val indexedTrainwithCatFeat = indexerModel.transform(indexedTRAINbinaryDF)
    val binarizer: Binarizer = new Binarizer().setInputCol("label").setOutputCol("labelBin").setThreshold(0.5)
    val indexedTRAINwithCatFeatBinTarget = binarizer.transform(indexedTrainwithCatFeat)

    # TEST DATA
    val indexerModel = indexer.fit(indexedTESTbinaryDF)
    val indexedTrainwithCatFeat = indexerModel.transform(indexedTESTbinaryDF)
    val binarizer: Binarizer = new Binarizer().setInputCol("label").setOutputCol("labelBin").setThreshold(0.5)
    val indexedTESTwithCatFeatBinTarget = binarizer.transform(indexedTrainwithCatFeat)

    # CATEGORIZE FEATURES FOR THE REGRESSION PROBLEM
    # CREATE PROPERLY INDEXED AND CATEGORIZED DATA FRAMES FOR TREE-BASED MODELS

    # TRAIN DATA
    val indexer = new VectorIndexer().setInputCol("features").setOutputCol("featuresCat").setMaxCategories(32)
    val indexerModel = indexer.fit(indexedTRAINregDF)
    val indexedTRAINwithCatFeat = indexerModel.transform(indexedTRAINregDF)

    # TEST DATA
    val indexerModel = indexer.fit(indexedTESTbinaryDF)
    val indexedTESTwithCatFeat = indexerModel.transform(indexedTESTregDF)



## <a name="binary-classification-model-predict-whether-a-tip-should-be-paid"></a>Modelo de clasificación binario: predecir si se debe pagar un Consejo

En esta sección, crear tres tipos de modelos de clasificación binario predecir si no se debe pagar un Consejo:

- Un **modelo de regresión logística** usando la m motor `LogisticRegression()` función
- Un **modelo de clasificación de bosque aleatorio** utilizando el m motor `RandomForestClassifier()` función
- Un **modelo de clasificación de árbol Aumentar degradado** utilizando la MLlib `GradientBoostedTrees()` función

### <a name="create-a-logistic-regression-model"></a>Crear un modelo de regresión logística

A continuación, cree un modelo de regresión logística usando la m motor `LogisticRegression()` función. Crear el modelo de creación de código en una serie de pasos:

1. Establecen el **tren el modelo de** datos con un parámetro.
2. **Evaluar el modelo** en un conjunto de datos de prueba con métricas.
3. **Guarde el modelo** de almacenamiento de blobs de consumo futuro.
4. **Puntuación del modelo** con los datos de prueba.
5. **Los resultados de trazado** con receptor operativo curvas característica (rc).

Este es el código de estos procedimientos:

    # CREATE A LOGISTIC REGRESSION MODEL
    val lr = new LogisticRegression().setLabelCol("tipped").setFeaturesCol("features").setMaxIter(10).setRegParam(0.3).setElasticNetParam(0.8)
    val lrModel = lr.fit(OneHotTRAIN)

    # PREDICT ON THE TEST DATA SET
    val predictions = lrModel.transform(OneHotTEST)

    # SELECT `BinaryClassificationEvaluator()` TO COMPUTE THE TEST ERROR
    val evaluator = new BinaryClassificationEvaluator().setLabelCol("tipped").setRawPredictionCol("probability").setMetricName("areaUnderROC")
    val ROC = evaluator.evaluate(predictions)
    println("ROC on test data = " + ROC)

    # SAVE THE MODEL
    val datestamp = Calendar.getInstance().getTime().toString.replaceAll(" ", ".").replaceAll(":", "_");
    val modelName = "LogisticRegression__"
    val filename = modelDir.concat(modelName).concat(datestamp)
    lrModel.save(filename);

Cargar, puntuación y guardar los resultados.

    # RECORD THE START TIME
    val starttime = Calendar.getInstance().getTime()

    # LOAD THE SAVED MODEL AND SCORE THE TEST DATA SET
    val savedModel = org.apache.spark.ml.classification.LogisticRegressionModel.load(filename)
    println(s"Coefficients: ${savedModel.coefficients} Intercept: ${savedModel.intercept}")

    # SCORE THE MODEL ON THE TEST DATA
    val predictions = savedModel.transform(OneHotTEST).select("tipped","probability","rawPrediction")
    predictions.registerTempTable("testResults")

    # SELECT `BinaryClassificationEvaluator()` TO COMPUTE THE TEST ERROR
    val evaluator = new BinaryClassificationEvaluator().setLabelCol("tipped").setRawPredictionCol("probability").setMetricName("areaUnderROC")
    val ROC = evaluator.evaluate(predictions)

    # GET THE TIME TO RUN THE CELL
    val endtime = Calendar.getInstance().getTime()
    val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
    println("Time taken to run the above cell: " + elapsedtime + " seconds.");

    # PRINT THE ROC RESULTS
    println("ROC on test data = " + ROC)


**Resultado:**

ROC en datos de prueba = 0.9827381497557599


Utilizar Python en marcos de datos Pandas locales para trazar la curva ROC.


    # QUERY THE RESULTS
    %%sql -q -o sqlResults
    SELECT tipped, probability from testResults


    # RUN THE CODE LOCALLY ON THE JUPYTER SERVER AND IMPORT LIBRARIES
    %%local
    %matplotlib inline
    from sklearn.metrics import roc_curve,auc

    sqlResults['probFloat'] = sqlResults.apply(lambda row: row['probability'].values()[0][1], axis=1)
    predictions_pddf = sqlResults[["tipped","probFloat"]]

    # PREDICT THE ROC CURVE
    # predictions_pddf = sqlResults.rename(columns={'_1': 'probability', 'tipped': 'label'})
    prob = predictions_pddf["probFloat"]
    fpr, tpr, thresholds = roc_curve(predictions_pddf['tipped'], prob, pos_label=1);
    roc_auc = auc(fpr, tpr)

    # PLOT THE ROC CURVE
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


**Resultado:**

![Sugerencia o sin curva de sugerencia ROC](./media/machine-learning-data-science-process-scala-walkthrough/plot-roc-curve-tip-or-not.png)


### <a name="create-a-random-forest-classification-model"></a>Crear un modelo de clasificación de bosque aleatorios

A continuación, crear un modelo de clasificación de bosque aleatorio mediante la m motor `RandomForestClassifier()` de función y, a continuación, evaluar el modelo de datos de prueba.


    # RECORD THE START TIME
    val starttime = Calendar.getInstance().getTime()

    # CREATE THE RANDOM FOREST CLASSIFIER MODEL
    val rf = new RandomForestClassifier().setLabelCol("labelBin").setFeaturesCol("featuresCat").setNumTrees(10).setSeed(1234)

    # FIT THE MODEL
    val rfModel = rf.fit(indexedTRAINwithCatFeatBinTarget)
    val predictions = rfModel.transform(indexedTESTwithCatFeatBinTarget)

    # EVALUATE THE MODEL
    val evaluator = new MulticlassClassificationEvaluator().setLabelCol("label").setPredictionCol("prediction").setMetricName("f1")
    val Test_f1Score = evaluator.evaluate(predictions)
    println("F1 score on test data: " + Test_f1Score);

    # GET THE TIME TO RUN THE CELL
    val endtime = Calendar.getInstance().getTime()
    val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
    println("Time taken to run the above cell: " + elapsedtime + " seconds.");

    # CALCULATE BINARY CLASSIFICATION EVALUATION METRICS
    val evaluator = new BinaryClassificationEvaluator().setLabelCol("label").setRawPredictionCol("probability").setMetricName("areaUnderROC")
    val ROC = evaluator.evaluate(predictions)
    println("ROC on test data = " + ROC)


**Resultado:**

ROC en datos de prueba = 0.9847103571552683


### <a name="create-a-gbt-classification-model"></a>Crear un modelo de clasificación GBT

A continuación, cree un modelo de clasificación GBT mediante el uso de MLlib `GradientBoostedTrees()` de función y, a continuación, evaluar el modelo de datos de prueba.


    # TRAIN A GBT CLASSIFICATION MODEL BY USING MLLIB AND A LABELED POINT

    # RECORD THE START TIME
    val starttime = Calendar.getInstance().getTime()

    # DEFINE THE GBT CLASSIFICATION MODEL
    val boostingStrategy = BoostingStrategy.defaultParams("Classification")
    boostingStrategy.numIterations = 20
    boostingStrategy.treeStrategy.numClasses = 2
    boostingStrategy.treeStrategy.maxDepth = 5
    boostingStrategy.treeStrategy.categoricalFeaturesInfo = Map[Int, Int]((0,2),(1,2),(2,6),(3,4))

    # TRAIN THE MODEL
    val gbtModel = GradientBoostedTrees.train(indexedTRAINbinary, boostingStrategy)

    # SAVE THE MODEL IN BLOB STORAGE
    val datestamp = Calendar.getInstance().getTime().toString.replaceAll(" ", ".").replaceAll(":", "_");
    val modelName = "GBT_Classification__"
    val filename = modelDir.concat(modelName).concat(datestamp)
    gbtModel.save(sc, filename);

    # EVALUATE THE MODEL ON TEST INSTANCES AND THE COMPUTE TEST ERROR
    val labelAndPreds = indexedTESTbinary.map { point =>
      val prediction = gbtModel.predict(point.features)
      (point.label, prediction)
    }
    val testErr = labelAndPreds.filter(r => r._1 != r._2).count.toDouble / indexedTRAINbinary.count()
    //println("Learned classification GBT model:\n" + gbtModel.toDebugString)
    println("Test Error = " + testErr)

    # USE BINARY AND MULTICLASS METRICS TO EVALUATE THE MODEL ON THE TEST DATA
    val metrics = new MulticlassMetrics(labelAndPreds)
    println(s"Precision: ${metrics.precision}")
    println(s"Recall: ${metrics.recall}")
    println(s"F1 Score: ${metrics.fMeasure}")

    val metrics = new BinaryClassificationMetrics(labelAndPreds)
    println(s"Area under PR curve: ${metrics.areaUnderPR}")
    println(s"Area under ROC curve: ${metrics.areaUnderROC}")

    # GET THE TIME TO RUN THE CELL
    val endtime = Calendar.getInstance().getTime()
    val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
    println("Time taken to run the above cell: " + elapsedtime + " seconds.");

    # PRINT THE ROC METRIC
    println(s"Area under ROC curve: ${metrics.areaUnderROC}")


**Resultado:**

Área de curva ROC: 0.9846895479241554


## <a name="regression-model-predict-tip-amount"></a>Modelo de regresión: predecir la cantidad de sugerencia

En esta sección, crear dos tipos de modelos de regresión predecir la cantidad de sugerencia:

- Un **regularice modelo de regresión lineal** utilizando el m motor `LinearRegression()` función. Deberá guardar el modelo y evaluar el modelo de datos de prueba.
- Un **modelo de regresión de árbol Aumentar degradado** utilizando el m motor `GBTRegressor()` función.


### <a name="create-a-regularized-linear-regression-model"></a>Crear un modelo de regresión lineal regularice

    # RECORD THE START TIME
    val starttime = Calendar.getInstance().getTime()

    # CREATE A REGULARIZED LINEAR REGRESSION MODEL BY USING THE SPARK ML FUNCTION AND DATA FRAMES
    val lr = new LinearRegression().setLabelCol("tip_amount").setFeaturesCol("features").setMaxIter(10).setRegParam(0.3).setElasticNetParam(0.8)

    # FIT THE MODEL BY USING DATA FRAMES
    val lrModel = lr.fit(OneHotTRAIN)
    println(s"Coefficients: ${lrModel.coefficients} Intercept: ${lrModel.intercept}")

    # SUMMARIZE THE MODEL OVER THE TRAINING SET AND PRINT METRICS
    val trainingSummary = lrModel.summary
    println(s"numIterations: ${trainingSummary.totalIterations}")
    println(s"objectiveHistory: ${trainingSummary.objectiveHistory.toList}")
    trainingSummary.residuals.show()
    println(s"RMSE: ${trainingSummary.rootMeanSquaredError}")
    println(s"r2: ${trainingSummary.r2}")

    # SAVE THE MODEL IN AZURE BLOB STORAGE
    val datestamp = Calendar.getInstance().getTime().toString.replaceAll(" ", ".").replaceAll(":", "_");
    val modelName = "LinearRegression__"
    val filename = modelDir.concat(modelName).concat(datestamp)
    lrModel.save(filename);

    # PRINT THE COEFFICIENTS
    println(s"Coefficients: ${lrModel.coefficients} Intercept: ${lrModel.intercept}")

    # SCORE THE MODEL ON TEST DATA
    val predictions = lrModel.transform(OneHotTEST)

    # EVALUATE THE MODEL ON TEST DATA
    val evaluator = new RegressionEvaluator().setLabelCol("tip_amount").setPredictionCol("prediction").setMetricName("r2")
    val r2 = evaluator.evaluate(predictions)
    println("R-sqr on test data = " + r2)

    # GET THE TIME TO RUN THE CELL
    val endtime = Calendar.getInstance().getTime()
    val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
    println("Time taken to run the above cell: " + elapsedtime + " seconds.");


**Resultado:**

Tiempo de ejecución de la celda: 13 segundos.


    # LOAD A SAVED LINEAR REGRESSION MODEL FROM BLOB STORAGE AND SCORE A TEST DATA SET

    # RECORD THE START TIME
    val starttime = Calendar.getInstance().getTime()

    # LOAD A SAVED LINEAR REGRESSION MODEL FROM AZURE BLOB STORAGE
    val savedModel = org.apache.spark.ml.regression.LinearRegressionModel.load(filename)
    println(s"Coefficients: ${savedModel.coefficients} Intercept: ${savedModel.intercept}")

    # SCORE THE MODEL ON TEST DATA
    val predictions = savedModel.transform(OneHotTEST).select("tip_amount","prediction")
    predictions.registerTempTable("testResults")

    # EVALUATE THE MODEL ON TEST DATA
    val evaluator = new RegressionEvaluator().setLabelCol("tip_amount").setPredictionCol("prediction").setMetricName("r2")
    val r2 = evaluator.evaluate(predictions)
    println("R-sqr on test data = " + r2)

    # GET THE TIME TO RUN THE CELL
    val endtime = Calendar.getInstance().getTime()
    val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
    println("Time taken to run the above cell: " + elapsedtime + " seconds.");

    # PRINT THE RESULTS
    println("R-sqr on test data = " + r2)


**Resultado:**

R sqr en datos de prueba = 0.5960320470835743


A continuación, los resultados de la consulta como una trama de datos y usar AutoVizWidget y matplotlib para visualizarlo.


    # RUN A SQL QUERY
    %%sql -q -o sqlResults
    select * from testResults

    # RUN THE CODE LOCALLY ON THE JUPYTER SERVER
    %%local

    # USE THE JUPYTER AUTO-PLOTTING FEATURE TO CREATE INTERACTIVE FIGURES
    # CLICK THE TYPE OF PLOT TO GENERATE (LINE, AREA, BAR, AND SO ON)
    sqlResults

El código crea un marco de datos locales desde el resultado de la consulta y traza los datos. La `%%local` mágico crea una trama de datos local, `sqlResults`, que puede usar para trazar con matplotlib.

>[AZURE.NOTE] Este mágico motor se utiliza varias veces en este artículo. Si la cantidad de datos es grande, debe ejemplo para crear una trama de datos que puede caber en la memoria local.

Crear trazados mediante Python matplotlib.

    # RUN THE CODE LOCALLY ON THE JUPYTER SERVER AND IMPORT LIBRARIES
    %%local
    sqlResults
    %matplotlib inline
    import numpy as np

    # PLOT THE RESULTS
    ax = sqlResults.plot(kind='scatter', figsize = (6,6), x='tip_amount', y='prediction', color='blue', alpha = 0.25, label='Actual vs. predicted');
    fit = np.polyfit(sqlResults['tip_amount'], sqlResults['prediction'], deg=1)
    ax.set_title('Actual vs. Predicted Tip Amounts ($)')
    ax.set_xlabel("Actual")
    ax.set_ylabel("Predicted")
    #ax.plot(sqlResults['tip_amount'], fit[0] * sqlResults['prediction'] + fit[1], color='magenta')
    plt.axis([-1, 15, -1, 8])
    plt.show(ax)

**Resultado:**

![Sugerencia cantidad: real frente previsto](./media/machine-learning-data-science-process-scala-walkthrough/plot-actual-vs-predicted-tip-amount.png)


### <a name="create-a-gbt-regression-model"></a>Crear un modelo de regresión GBT

Crear un modelo de regresión GBT mediante la m motor `GBTRegressor()` de función y, a continuación, evaluar el modelo de datos de prueba.

[Árboles de aumentar degradado](http://spark.apache.org/docs/latest/ml-classification-regression.html#gradient-boosted-trees-gbts) (GBTs) son conjuntos de árboles de decisión. GBTs entrenar árboles de decisión repetidamente para minimizar una función de pérdida. Puede usar GBTs para regresión y clasificación. Puede controlar características por categorías, no requieren la característica de ajuste de escala y puede capturar nonlinearities e interacciones de la característica. También puede usarlos en una configuración de clasificación de multiclass.


    # RECORD THE START TIME
    val starttime = Calendar.getInstance().getTime()

    # TRAIN A GBT REGRESSION MODEL
    val gbt = new GBTRegressor().setLabelCol("label").setFeaturesCol("featuresCat").setMaxIter(10)
    val gbtModel = gbt.fit(indexedTRAINwithCatFeat)

    # MAKE PREDICTIONS
    val predictions = gbtModel.transform(indexedTESTwithCatFeat)

    # COMPUTE TEST SET R2
    val evaluator = new RegressionEvaluator().setLabelCol("label").setPredictionCol("prediction").setMetricName("r2")
    val Test_R2 = evaluator.evaluate(predictions)


    # GET THE TIME TO RUN THE CELL
    val endtime = Calendar.getInstance().getTime()
    val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
    println("Time taken to run the above cell: " + elapsedtime + " seconds.");

    # PRINT THE RESULTS
    println("Test R-sqr is: " + Test_R2);


**Resultado:**

Prueba R-sqr es: 0.7655383534596654



## <a name="advanced-modeling-utilities-for-optimization"></a>Utilidades de modelado avanzadas para la optimización

En esta sección, use utilidades de aprendizaje de máquina que los desarrolladores se usa con frecuencia para optimizar el modelo. Más concretamente, se pueden optimizar modelos de aprendizaje de máquina tres maneras distintas mediante barrido parámetro y validación cruzada:

-   Dividir los datos en conjuntos de validación y el tren, optimizar el modelo mediante barrido hyper parámetro en un conjunto de formación y evaluar en un conjunto de validación (regresión lineal)
-   Optimizar el modelo mediante validación cruzada y hyper parámetro barrido mediante CrossValidator función del motor m (binario clasificación)
-   Optimizar el modelo mediante código personalizado de validación cruzada y barrido parámetro usar cualquier equipo aprendizaje conjunto de función y parámetro (regresión lineal)


**Validación cruzada** es una técnica que se evalúa bien generalizará un modelo de formación en un conjunto de datos conocido para predecir las características de los conjuntos de datos en el que se ha no se ha formado. La idea general de esta técnica es que está capacitado un modelo de datos conocidos en un conjunto de datos y, a continuación, en que se comprueba la precisión de sus predicciones con un conjunto de datos independiente. Una implementación común es dividir un conjunto de datos en el *k*-pliegue para permitir y, a continuación, entrenar el modelo en turnos en todos excepto uno de lo pliegue.

**Optimización de Hyper-parámetro** es el problema de elegir un conjunto de hyper-parámetros para un algoritmo de aprendizaje, normalmente con el objetivo de optimizar una medida de rendimiento del algoritmo en un conjunto de datos independiente. Hyper parámetro es un valor que debe especificar fuera del procedimiento de aprendizaje de modelo. Suposiciones acerca de los valores de parámetro hyper pueden afectar a la flexibilidad y la precisión del modelo. Árboles de decisión tienen hyper-parámetros, por ejemplo, como la profundidad que desee y el número de hojas en el árbol. Debe establecer un término de disminución misclassification para un equipo de soporte técnico vector (SVM).

Una manera de realizar optimización hyper parámetro es usar una búsqueda de la cuadrícula, también denominada un **barrido del parámetro**. En una búsqueda de la cuadrícula, se realiza una búsqueda exhaustiva a través de los valores de un subconjunto del parámetro hyper espacio para un algoritmo de aprendizaje especificado. Validación cruzada puede proporcionar una métrica de rendimiento para ordenar los resultados óptimo producidos por el algoritmo de búsqueda de la cuadrícula. Si utiliza validación cruzada hyper parámetro barrido, puede ayudar a problemas de límite como overfitting un modelo de datos de entrenamiento. De esta forma, el modelo conserva la capacidad de aplicar al conjunto de datos desde el que se extraen los datos de entrenamiento general.

### <a name="optimize-a-linear-regression-model-with-hyper-parameter-sweeping"></a>Optimizar un modelo de regresión lineal con hyper parámetro barrido

A continuación, dividir datos en conjuntos de validación y el tren, usar hyper-parámetro barrido en un conjunto de aprendizaje para optimizar el modelo y evaluar en un conjunto de validación (regresión lineal).

    # RECORD THE START TIME
    val starttime = Calendar.getInstance().getTime()

    # RENAME `tip_amount` AS A LABEL
    val OneHotTRAINLabeled = OneHotTRAIN.select("tip_amount","features").withColumnRenamed(existingName="tip_amount",newName="label")
    val OneHotTESTLabeled = OneHotTEST.select("tip_amount","features").withColumnRenamed(existingName="tip_amount",newName="label")
    OneHotTRAINLabeled.cache()
    OneHotTESTLabeled.cache()

    # DEFINE THE ESTIMATOR FUNCTION: `THE LinearRegression()` FUNCTION
    val lr = new LinearRegression().setLabelCol("label").setFeaturesCol("features").setMaxIter(10)

    # DEFINE THE PARAMETER GRID
    val paramGrid = new ParamGridBuilder().addGrid(lr.regParam, Array(0.1, 0.01, 0.001)).addGrid(lr.fitIntercept).addGrid(lr.elasticNetParam, Array(0.1, 0.5, 0.9)).build()

    # DEFINE THE PIPELINE WITH A TRAIN/TEST VALIDATION SPLIT (75% IN THE TRAINING SET), AND THEN THE SPECIFY ESTIMATOR, EVALUATOR, AND PARAMETER GRID
    val trainPct = 0.75
    val trainValidationSplit = new TrainValidationSplit().setEstimator(lr).setEvaluator(new RegressionEvaluator).setEstimatorParamMaps(paramGrid).setTrainRatio(trainPct)

    # RUN THE TRAIN VALIDATION SPLIT AND CHOOSE THE BEST SET OF PARAMETERS
    val model = trainValidationSplit.fit(OneHotTRAINLabeled)

    # MAKE PREDICTIONS ON THE TEST DATA BY USING THE MODEL WITH THE COMBINATION OF PARAMETERS THAT PERFORMS THE BEST
    val testResults = model.transform(OneHotTESTLabeled).select("label", "prediction")

    # COMPUTE TEST SET R2
    val evaluator = new RegressionEvaluator().setLabelCol("label").setPredictionCol("prediction").setMetricName("r2")
    val Test_R2 = evaluator.evaluate(testResults)

    # GET THE TIME TO RUN THE CELL
    val endtime = Calendar.getInstance().getTime()
    val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
    println("Time taken to run the above cell: " + elapsedtime + " seconds.");

    println("Test R-sqr is: " + Test_R2);


**Resultado:**

Prueba R-sqr es: 0.6226484708501209


### <a name="optimize-the-binary-classification-model-by-using-cross-validation-and-hyper-parameter-sweeping"></a>Optimizar el modelo de clasificación binario mediante validación cruzada y hyper parámetro barrido

Esta sección muestra cómo optimizar un modelo de clasificación binario mediante validación cruzada y hyper parámetro barrido. Utiliza el motor de m `CrossValidator` función.

    # RECORD THE START TIME
    val starttime = Calendar.getInstance().getTime()

    # CREATE DATA FRAMES WITH PROPERLY LABELED COLUMNS TO USE WITH THE TRAIN AND TEST SPLIT
    val indexedTRAINwithCatFeatBinTargetRF = indexedTRAINwithCatFeatBinTarget.select("labelBin","featuresCat").withColumnRenamed(existingName="labelBin",newName="label").withColumnRenamed(existingName="featuresCat",newName="features")
    val indexedTESTwithCatFeatBinTargetRF = indexedTESTwithCatFeatBinTarget.select("labelBin","featuresCat").withColumnRenamed(existingName="labelBin",newName="label").withColumnRenamed(existingName="featuresCat",newName="features")
    indexedTRAINwithCatFeatBinTargetRF.cache()
    indexedTESTwithCatFeatBinTargetRF.cache()

    # DEFINE THE ESTIMATOR FUNCTION
    val rf = new RandomForestClassifier().setLabelCol("label").setFeaturesCol("features").setImpurity("gini").setSeed(1234).setFeatureSubsetStrategy("auto").setMaxBins(32)

    # DEFINE THE PARAMETER GRID
    val paramGrid = new ParamGridBuilder().addGrid(rf.maxDepth, Array(4,8)).addGrid(rf.numTrees, Array(5,10)).addGrid(rf.minInstancesPerNode, Array(100,300)).build()

    # SPECIFY THE NUMBER OF FOLDS
    val numFolds = 3

    # DEFINE THE TRAIN/TEST VALIDATION SPLIT (75% IN THE TRAINING SET)
    val CrossValidator = new CrossValidator().setEstimator(rf).setEvaluator(new BinaryClassificationEvaluator).setEstimatorParamMaps(paramGrid).setNumFolds(numFolds)

    # RUN THE TRAIN VALIDATION SPLIT AND CHOOSE THE BEST SET OF PARAMETERS
    val model = CrossValidator.fit(indexedTRAINwithCatFeatBinTargetRF)

    # MAKE PREDICTIONS ON THE TEST DATA BY USING THE MODEL WITH THE COMBINATION OF PARAMETERS THAT PERFORMS THE BEST
    val testResults = model.transform(indexedTESTwithCatFeatBinTargetRF).select("label", "prediction")

    # COMPUTE THE TEST F1 SCORE
    val evaluator = new MulticlassClassificationEvaluator().setLabelCol("label").setPredictionCol("prediction").setMetricName("f1")
    val Test_f1Score = evaluator.evaluate(testResults)

    # GET THE TIME TO RUN THE CELL
    val endtime = Calendar.getInstance().getTime()
    val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
    println("Time taken to run the above cell: " + elapsedtime + " seconds.");


**Resultado:**

Tiempo de ejecución de la celda: 33 segundos.


### <a name="optimize-the-linear-regression-model-by-using-custom-cross-validation-and-parameter-sweeping-code"></a>Optimizar el modelo de regresión lineal mediante código personalizado de validación cruzada y barrido de parámetro

A continuación, optimizar el modelo mediante código personalizado e identificar los parámetros de modelo mejores con el criterio de precisión más alto. A continuación, cree el modelo final, evaluar el modelo de datos de prueba y guarde el modelo de almacenamiento de blobs. Por último, cargar el modelo, puntuación datos de prueba y evaluar precisión.

    # RECORD THE START TIME
    val starttime = Calendar.getInstance().getTime()

    # DEFINE THE PARAMETER GRID AND THE NUMBER OF FOLDS
    val paramGrid = new ParamGridBuilder().addGrid(rf.maxDepth, Array(5,10)).addGrid(rf.numTrees, Array(10,25,50)).build()

    val nFolds = 3
    val numModels = paramGrid.size
    val numParamsinGrid = 2

    # SPECIFY THE NUMBER OF CATEGORIES FOR CATEGORICAL VARIABLES
    val categoricalFeaturesInfo = Map[Int, Int]((0,2),(1,2),(2,6),(3,4))

    var maxDepth = -1
    var numTrees = -1
    var param = ""
    var paramval = -1
    var validateLB = -1.0
    var validateUB = -1.0
    val h = 1.0 / nFolds;
    val RMSE  = Array.fill(numModels)(0.0)

    # CREATE K-FOLDS
    val splits = MLUtils.kFold(indexedTRAINbinary, numFolds = nFolds, seed=1234)


    # LOOP THROUGH K-FOLDS AND THE PARAMETER GRID TO GET AND IDENTIFY THE BEST PARAMETER SET BY LEVEL OF ACCURACY
    for (i <- 0 to (nFolds-1)) {
        validateLB = i * h
        validateUB = (i + 1) * h
        val validationCV = trainData.filter($"rand" >= validateLB  && $"rand" < validateUB)
        val trainCV = trainData.filter($"rand" < validateLB  || $"rand" >= validateUB)
        val validationLabPt = validationCV.rdd.map(r => LabeledPoint(r.getDouble(targetIndRegression(0).toInt), Vectors.dense(featuresIndIndex.map(r.getDouble(_)).toArray)));
        val trainCVLabPt = trainCV.rdd.map(r => LabeledPoint(r.getDouble(targetIndRegression(0).toInt), Vectors.dense(featuresIndIndex.map(r.getDouble(_)).toArray)));
        validationLabPt.cache()
        trainCVLabPt.cache()

        for (nParamSets <- 0 to (numModels-1)) {
            for (nParams <- 0 to (numParamsinGrid-1)) {
                param = paramGrid(nParamSets).toSeq(nParams).param.toString.split("__")(1)
                paramval = paramGrid(nParamSets).toSeq(nParams).value.toString.toInt
                if (param == "maxDepth") {maxDepth = paramval}
                if (param == "numTrees") {numTrees = paramval}
            }
            val rfModel = RandomForest.trainRegressor(trainCVLabPt, categoricalFeaturesInfo=categoricalFeaturesInfo,
                                                      numTrees=numTrees, maxDepth=maxDepth,
                                                      featureSubsetStrategy="auto",impurity="variance", maxBins=32)
            val labelAndPreds = validationLabPt.map { point =>
                                                     val prediction = rfModel.predict(point.features)
                                                     ( prediction, point.label )
                                                    }
            val validMetrics = new RegressionMetrics(labelAndPreds)
            val rmse = validMetrics.rootMeanSquaredError
            RMSE(nParamSets) += rmse
        }
        validationLabPt.unpersist();
        trainCVLabPt.unpersist();
    }
    val minRMSEindex = RMSE.indexOf(RMSE.min)

    # GET THE BEST PARAMETERS FROM A CROSS-VALIDATION AND PARAMETER SWEEP
    var best_maxDepth = -1
    var best_numTrees = -1
    for (nParams <- 0 to (numParamsinGrid-1)) {
        param = paramGrid(minRMSEindex).toSeq(nParams).param.toString.split("__")(1)
        paramval = paramGrid(minRMSEindex).toSeq(nParams).value.toString.toInt
        if (param == "maxDepth") {best_maxDepth = paramval}
        if (param == "numTrees") {best_numTrees = paramval}
    }

    # CREATE THE BEST MODEL WITH THE BEST PARAMETERS AND A FULL TRAINING DATA SET
    val best_rfModel = RandomForest.trainRegressor(indexedTRAINreg, categoricalFeaturesInfo=categoricalFeaturesInfo,
                                                      numTrees=best_numTrees, maxDepth=best_maxDepth,
                                                      featureSubsetStrategy="auto",impurity="variance", maxBins=32)

    # SAVE THE BEST RANDOM FOREST MODEL IN BLOB STORAGE
    val datestamp = Calendar.getInstance().getTime().toString.replaceAll(" ", ".").replaceAll(":", "_");
    val modelName = "BestCV_RF_Regression__"
    val filename = modelDir.concat(modelName).concat(datestamp)
    best_rfModel.save(sc, filename);

    # PREDICT ON THE TRAINING SET WITH THE BEST MODEL AND THEN EVALUATE
    val labelAndPreds = indexedTESTreg.map { point =>
                                            val prediction = best_rfModel.predict(point.features)
                                            ( prediction, point.label )
                                           }

    val test_rmse = new RegressionMetrics(labelAndPreds).rootMeanSquaredError
    val test_rsqr = new RegressionMetrics(labelAndPreds).r2

    # GET THE TIME TO RUN THE CELL
    val endtime = Calendar.getInstance().getTime()
    val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
    println("Time taken to run the above cell: " + elapsedtime + " seconds.");


    # LOAD THE MODEL
    val savedRFModel = RandomForestModel.load(sc, filename)

    val labelAndPreds = indexedTESTreg.map { point =>
                                            val prediction = savedRFModel.predict(point.features)
                                            ( prediction, point.label )
                                           }
    # TEST THE MODEL
    val test_rmse = new RegressionMetrics(labelAndPreds).rootMeanSquaredError
    val test_rsqr = new RegressionMetrics(labelAndPreds).r2


**Resultado:**

Tiempo de ejecución de la celda: 61 segundos.

## <a name="consume-spark-built-machine-learning-models-automatically-with-scala"></a>Consumir modelos de aprendizaje de máquina integrado motor automáticamente con Scala

Para obtener información general de temas que le guíe durante las tareas que forman el proceso de ciencias datos en Azure, consulte [El proceso de ciencias de datos de grupo](http://aka.ms/datascienceprocess).

[Tutoriales de proceso de ciencias de datos de equipo](data-science-process-walkthroughs.md) , se describen otros tutoriales de llevar a cabo que muestran los pasos del proceso de ciencias de datos de grupo para escenarios específicos. Los tutoriales también muestran cómo combinar servicios y herramientas de la nube y locales en un flujo de trabajo o la canalización de crear una aplicación inteligente.

[Modelos de aprendizaje de máquina creado por el motor de puntuación](machine-learning-data-science-spark-model-consumption.md) se muestra cómo usar Scala código para cargar automáticamente y puntuación nuevos conjuntos de datos con los modelos de aprendizaje integrado en motor y guardado en el almacenamiento de blobs de Windows Azure. Puede seguir las instrucciones que aparecen y simplemente reemplace el código de Python por Scala en este artículo para su consumo automatizado.
