<properties 
    pageTitle="Utilizar el motor de Apache para generar aplicaciones de aprendizaje de máquina en HDInsight | Microsoft Azure" 
    description="Instrucciones paso a paso sobre cómo usar los blocs de notas con Apache motor para generar aplicaciones de aprendizaje de equipo" 
    services="hdinsight" 
    documentationCenter="" 
    authors="nitinme" 
    manager="jhubbard" 
    editor="cgronlun"
    tags="azure-portal"/>

<tags 
    ms.service="hdinsight" 
    ms.workload="big-data" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/05/2016" 
    ms.author="nitinme"/>


# <a name="machine-learning-predictive-analysis-on-food-inspection-data-using-mllib-with-apache-spark-cluster-on-hdinsight-linux"></a>Aprendizaje de máquina: análisis de predicción de inspección de comida datos mediante MLlib con motor Apache clúster en HDInsight Linux

> [AZURE.TIP] Este tutorial también está disponible como un bloc de notas de Jupyter en un clúster de motor (Linux) que cree en HDInsight. La experiencia de Bloc de notas le permite ejecutar los fragmentos de código de Python desde el Bloc de notas. Para realizar el tutorial desde dentro de un bloc de notas, cree un clúster de motor, inicie un bloc de notas de Jupyter (`https://CLUSTERNAME.azurehdinsight.net/jupyter`), y luego ejecute el Bloc de notas **motor máquina aprendizaje - predictivo análisis de datos de la inspección de comida con MLLib.ipynb** en la carpeta de **Python** .


En este artículo se muestra cómo usar **MLLib**, equipo integradas de motor las bibliotecas de aprendizaje para realizar un análisis predictivo simple en un conjunto de datos abierta. MLLib es una biblioteca de motor de núcleo que proporciona varias herramientas que son útiles para tareas de aprendizaje de equipo, incluida la utilidades adecuadas para:

* Clasificación

* Regresión.

* Clústeres

* Modelado de tema

* Descomposición valor singular (SVD) y análisis de componente principal (PCA)

* Hipótesis de prueba y calcular estadísticas de ejemplo

Este artículo presenta un enfoque simple para *clasificación* de regresión logística.

## <a name="what-are-classification-and-logistic-regression"></a>¿Qué clasificación y regresión logística?

*Clasificación*, un equipo muy común aprendizaje tarea, es el proceso de ordenar los datos de entrada en categorías. Es el trabajo de un algoritmo de clasificación que averiguar cómo asignar "etiquetas" a la entrada de datos que proporciona. Por ejemplo, se podría considerar un algoritmo de aprendizaje de máquina que acepta la información de cotizaciones como entrada y las acciones que se divide en dos categorías: existencias que debe vender tanto que se debe conservar.

Regresión logística es el algoritmo que usa para la clasificación. Regresión logística de motor API es útil para *clasificación binario*o la clasificación de datos de entrada en uno de los dos grupos. Para obtener más información sobre retrocesos logísticas, vea [Wikipedia](https://en.wikipedia.org/wiki/Logistic_regression).

En resumen, el proceso de regresión logística genera una *función logística* que pueden utilizarse para predecir la probabilidad de que pertenece un vector de entrada en un grupo o a otro.  

## <a name="what-are-we-trying-to-accomplish-in-this-article"></a>¿Qué estamos intentando para llevar a cabo en este artículo?

Motor que usa para realizar algunos predictivo análisis de datos de inspección de comida (**Food_Inspections1.csv**) que se ha adquirido a través del [portal de datos de la ciudad de Chicago](https://data.cityofchicago.org/). Este conjunto de datos contiene información sobre los controles de comida que se realizaron en Chicago, incluida información sobre cada establecimiento de comida que se inspeccionen, las transgresiones de que se han encontrado (si existe) y los resultados de la inspección. El archivo CSV de datos ya está disponible en la cuenta de almacenamiento asociada con el clúster en **/HdiSamples/HdiSamples/FoodInspectionData/Food_Inspections1.csv**.

En los pasos siguientes, desarrollar un modelo para ver lo que se tarda en pasar o no una inspección de comida. 

## <a name="start-building-a-machine-learning-application-using-spark-mllib"></a>Comenzar a crear una aplicación de aprendizaje de equipo mediante motor MLlib

1. Desde el [Portal de Azure](https://portal.azure.com/), desde el startboard, haga clic en el mosaico de clúster motor (si lo anclados a la startboard). También puede ir a su clúster en **Examinar todos** > **HDInsight clústeres**.   

2. Desde el módulo de clúster motor, haga clic en **Panel de diseño**y, a continuación, haga clic en **Bloc de notas de Jupyter**. Si se le solicita, escriba las credenciales de administrador para el clúster.

    > [AZURE.NOTE] También puede contactar el Bloc de notas de Jupyter para el clúster abriendo la siguiente URL en el explorador. Reemplazar __NOMBREDECLÚSTER__ con el nombre del clúster:
    >
    > `https://CLUSTERNAME.azurehdinsight.net/jupyter`

2. Crear un nuevo bloc de notas. Haga clic en **nuevo**y, a continuación, haga clic en **PySpark**.

    ![Crear un nuevo bloc de notas de Jupyter] (./media/hdinsight-apache-spark-machine-learning-mllib-ipython/hdispark.note.jupyter.createnotebook.png "Crear un nuevo bloc de notas de Jupyter")

3. Un bloc de notas se crea y se abre con el nombre Untitled.pynb. Haga clic en el nombre del Bloc de notas en la parte superior y escriba un nombre descriptivo.

    ![Proporcionar un nombre para el Bloc de notas] (./media/hdinsight-apache-spark-machine-learning-mllib-ipython/hdispark.note.jupyter.notebook.name.png "Proporcionar un nombre para el Bloc de notas")

3. Ya ha creado un bloc de notas con el núcleo PySpark, no es necesario crear cualquier contextos explícitamente. Los contextos motor y subárbol se crearán automáticamente para usted cuando se ejecuta la primera celda de código. Puede comenzar a crear su equipo de aprendizaje aplicación importando los tipos necesarios para este escenario. Para ello, coloque el cursor en la celda y presione **MAYÚS + ENTRAR**.


        from pyspark.ml import Pipeline
        from pyspark.ml.classification import LogisticRegression
        from pyspark.ml.feature import HashingTF, Tokenizer
        from pyspark.sql import Row
        from pyspark.sql.functions import UserDefinedFunction
        from pyspark.sql.types import *

## <a name="construct-an-input-dataframe"></a>Crear una entrada dataframe

Podemos utilizar `sqlContext` para realizar transformaciones de datos estructurados. La primera tarea es cargar los datos de ejemplo ((**Food_Inspections1.csv**)) en un motor SQL *dataframe*. 

1. Debido a los datos sin formato están en un archivo CSV, es necesario utilizar el contexto de motor para separar cada línea del archivo en la memoria como texto estructurado; a continuación, use biblioteca CSV de Python a analizar cada línea por separado. 


        def csvParse(s):
            import csv
            from StringIO import StringIO
            sio = StringIO(s)
            value = csv.reader(sio).next()
            sio.close()
            return value
        
        inspections = sc.textFile('wasbs:///HdiSamples/HdiSamples/FoodInspectionData/Food_Inspections1.csv')\
                        .map(csvParse)


2. Ahora tenemos el archivo CSV como un RDD. Deje que nosotros recuperar una fila de la RDD a comprender el esquema de los datos.


        inspections.take(1)


    Debe ver un resultado similar al siguiente:

        # -----------------
        # THIS IS AN OUTPUT
        # -----------------

        [['413707',
          'LUNA PARK INC',
          'LUNA PARK  DAY CARE',
          '2049789',
          "Children's Services Facility",
          'Risk 1 (High)',
          '3250 W FOSTER AVE ',
          'CHICAGO',
          'IL',
          '60625',
          '09/21/2010',
          'License-Task Force',
          'Fail',
          '24. DISH WASHING FACILITIES: PROPERLY DESIGNED, CONSTRUCTED, MAINTAINED, INSTALLED, LOCATED AND OPERATED - Comments: All dishwashing machines must be of a type that complies with all requirements of the plumbing section of the Municipal Code of Chicago and Rules and Regulation of the Board of Health. OBSEVERD THE 3 COMPARTMENT SINK BACKING UP INTO THE 1ST AND 2ND COMPARTMENT WITH CLEAR WATER AND SLOWLY DRAINING OUT. INST NEED HAVE IT REPAIR. CITATION ISSUED, SERIOUS VIOLATION 7-38-030 H000062369-10 COURT DATE 10-28-10 TIME 1 P.M. ROOM 107 400 W. SURPERIOR. | 36. LIGHTING: REQUIRED MINIMUM FOOT-CANDLES OF LIGHT PROVIDED, FIXTURES SHIELDED - Comments: Shielding to protect against broken glass falling into food shall be provided for all artificial lighting sources in preparation, service, and display facilities. LIGHT SHIELD ARE MISSING UNDER HOOD OF  COOKING EQUIPMENT AND NEED TO REPLACE LIGHT UNDER UNIT. 4 LIGHTS ARE OUT IN THE REAR CHILDREN AREA,IN THE KINDERGARDEN CLASS ROOM. 2 LIGHT ARE OUT EAST REAR, LIGHT FRONT WEST ROOM. NEED TO REPLACE ALL LIGHT THAT ARE NOT WORKING. | 35. WALLS, CEILINGS, ATTACHED EQUIPMENT CONSTRUCTED PER CODE: GOOD REPAIR, SURFACES CLEAN AND DUST-LESS CLEANING METHODS - Comments: The walls and ceilings shall be in good repair and easily cleaned. MISSING CEILING TILES WITH STAINS IN WEST,EAST, IN FRONT AREA WEST, AND BY THE 15MOS AREA. NEED TO BE REPLACED. | 32. FOOD AND NON-FOOD CONTACT SURFACES PROPERLY DESIGNED, CONSTRUCTED AND MAINTAINED - Comments: All food and non-food contact equipment and utensils shall be smooth, easily cleanable, and durable, and shall be in good repair. SPLASH GUARDED ARE NEEDED BY THE EXPOSED HAND SINK IN THE KITCHEN AREA | 34. FLOORS: CONSTRUCTED PER CODE, CLEANED, GOOD REPAIR, COVING INSTALLED, DUST-LESS CLEANING METHODS USED - Comments: The floors shall be constructed per code, be smooth and easily cleaned, and be kept clean and in good repair. INST NEED TO ELEVATE ALL FOOD ITEMS 6INCH OFF THE FLOOR 6 INCH AWAY FORM WALL.  ',
          '41.97583445690982',
          '-87.7107455232781',
          '(41.97583445690982, -87.7107455232781)']]


3. La salida anterior nos da una idea del esquema del archivo de entrada; el archivo incluye el nombre de cada establecimiento, el tipo de establecimiento, la dirección, los datos de los controles y la ubicación, entre otras cosas. Seleccionaremos algunas columnas que será útil para nuestro análisis predictiva y agrupar los resultados como un dataframe, que utilizamos para crear una tabla temporal.


        schema = StructType([
        StructField("id", IntegerType(), False), 
        StructField("name", StringType(), False), 
        StructField("results", StringType(), False), 
        StructField("violations", StringType(), True)])

        df = sqlContext.createDataFrame(inspections.map(lambda l: (int(l[0]), l[1], l[12], l[13])) , schema)
        df.registerTempTable('CountResults')

4. Ahora tenemos una *dataframe*, `df` en el que nos podemos realizar nuestro análisis. También tenemos una tabla temporal llamada **CountResults**. Hemos incluido 4 columnas de interés en el dataframe: **id**, **nombre**, **resultados**y **las transgresiones**. 
    
    Vamos a obtener una pequeña muestra de los datos:

        df.show(5)

    Debe ver un resultado similar al siguiente:

        # -----------------
        # THIS IS AN OUTPUT
        # -----------------

        +------+--------------------+-------+--------------------+
        |    id|                name|results|          violations|
        +------+--------------------+-------+--------------------+
        |413707|       LUNA PARK INC|   Fail|24. DISH WASHING ...|
        |391234|       CAFE SELMARIE|   Fail|2. FACILITIES TO ...|
        |413751|          MANCHU WOK|   Pass|33. FOOD AND NON-...|
        |413708|BENCHMARK HOSPITA...|   Pass|                    |
        |413722|           JJ BURGER|   Pass|                    |
        +------+--------------------+-------+--------------------+

## <a name="understand-the-data"></a>Comprender los datos

1. Vamos a empezar a hacerse una idea de lo que contiene el conjunto de datos. Por ejemplo, ¿cuáles son los distintos valores de la columna de **resultados** ?


        df.select('results').distinct().show()

    
    Debe ver un resultado similar al siguiente:

        # -----------------
        # THIS IS AN OUTPUT
        # -----------------
    
        +--------------------+
        |             results|
        +--------------------+
        |                Fail|
        |Business Not Located|
        |                Pass|
        |  Pass w/ Conditions|
        |     Out of Business|
        +--------------------+
    
2. Una visualización rápida puede ayudar a razón de la distribución de estos resultados. Ya tenemos los datos en una tabla temporal **CountResults**. Puede ejecutar la siguiente consulta SQL en la tabla a comprender mejor cómo se distribuyen los resultados.

        %%sql -o countResultsdf
        SELECT results, COUNT(results) AS cnt FROM CountResults GROUP BY results

    La `%%sql` mágico seguido `-o countResultsdf` garantiza que los resultados de la consulta se conservan localmente en el servidor de Jupyter (normalmente la headnode del clúster). El resultado se conserva como un dataframe [Pandas](http://pandas.pydata.org/) con el nombre especificado **countResultsdf**.
    
    Debe ver un resultado similar al siguiente:
    
    ![Resultado de la consulta SQL] (./media/hdinsight-apache-spark-machine-learning-mllib-ipython/query.output.png "Resultado de la consulta SQL")

    Para obtener más información sobre la `%%sql` mágico, así como otras magics disponibles con el núcleo PySpark, vea [Kernels disponibles en los blocs de notas de Jupyter con HDInsight de motor clústeres](hdinsight-apache-spark-jupyter-notebook-kernels.md#why-should-i-use-the-new-kernels).

3. También puede usar Matplotlib, una biblioteca que usa para generar la visualización de datos, para crear un gráfico. Dado que debe crearse el trazado desde la dataframe persistente localmente **countResultsdf** , el fragmento de código debe comenzar con la `%%local` mágico. Esto garantiza que el código se ejecuta localmente en el servidor Jupyter.

        %%local
        %matplotlib inline
        import matplotlib.pyplot as plt
        
        
        labels = countResultsdf['results']
        sizes = countResultsdf['cnt']
        colors = ['turquoise', 'seagreen', 'mediumslateblue', 'palegreen', 'coral']
        plt.pie(sizes, labels=labels, autopct='%1.1f%%', colors=colors)
        plt.axis('equal')

    Debe ver un resultado similar al siguiente:

    ![Resultado de resultado](./media/hdinsight-apache-spark-machine-learning-mllib-ipython/output_13_1.png)


4. Puede ver que hay 5 distintos resultados que puede tener una inspección:
    
    * No se encuentra el negocio 
    * Un error
    * Pasar
    * PSS con condiciones, y
    * Fuera de la empresa 

    Deje que nosotros desarrollar un modelo que puede adivinar el resultado de una inspección de comida, dado las transgresiones. Puesto que regresión logística es un método de clasificación binario, tiene sentido para agrupar los datos en dos categorías: **un error** y **pasar**. Un "pasar con condiciones" sigue siendo un paso, por lo que cuando se entrenar el modelo, tendremos en cuenta los dos resultados equivalentes. Datos con los resultados ("Empresa no encontrado", "fuera de la empresa") no son útiles para que se quitará de nuestro conjunto de aprendizaje. Debería correcto puesto que estas dos categorías componen un porcentaje muy pequeño de los resultados de todos modos.

5. Deje que nosotros continuar y convertir nuestra dataframe existente (`df`) en una nueva dataframe donde se representa cada inspección como un par de violaciones de etiqueta. En este caso, una etiqueta de `0.0` representa un error, una etiqueta de `1.0` representa un éxito y una etiqueta de `-1.0` representa algunos resultados además de las dos. Se filtrará esos otros resultados al calcular la nueva trama de datos.


        def labelForResults(s):
            if s == 'Fail':
                return 0.0
            elif s == 'Pass w/ Conditions' or s == 'Pass':
                return 1.0
            else:
                return -1.0
        label = UserDefinedFunction(labelForResults, DoubleType())
        labeledData = df.select(label(df.results).alias('label'), df.violations).where('label >= 0')


    Vamos a recuperar una fila de los datos etiquetados para ver su apariencia.


        labeledData.take(1)


    Debe ver un resultado similar al siguiente:
    
        # -----------------
        # THIS IS AN OUTPUT
        # -----------------
    
        [Row(label=0.0, violations=u"41. PREMISES MAINTAINED FREE OF LITTER, UNNECESSARY ARTICLES, CLEANING  EQUIPMENT PROPERLY STORED - Comments: All parts of the food establishment and all parts of the property used in connection with the operation of the establishment shall be kept neat and clean and should not produce any offensive odors.  REMOVE MATTRESS FROM SMALL DUMPSTER. | 35. WALLS, CEILINGS, ATTACHED EQUIPMENT CONSTRUCTED PER CODE: GOOD REPAIR, SURFACES CLEAN AND DUST-LESS CLEANING METHODS - Comments: The walls and ceilings shall be in good repair and easily cleaned.  REPAIR MISALIGNED DOORS AND DOOR NEAR ELEVATOR.  DETAIL CLEAN BLACK MOLD LIKE SUBSTANCE FROM WALLS BY BOTH DISH MACHINES.  REPAIR OR REMOVE BASEBOARD UNDER DISH MACHINE (LEFT REAR KITCHEN). SEAL ALL GAPS.  REPLACE MILK CRATES USED IN WALK IN COOLERS AND STORAGE AREAS WITH PROPER SHELVING AT LEAST 6' OFF THE FLOOR.  | 38. VENTILATION: ROOMS AND EQUIPMENT VENTED AS REQUIRED: PLUMBING: INSTALLED AND MAINTAINED - Comments: The flow of air discharged from kitchen fans shall always be through a duct to a point above the roofline.  REPAIR BROKEN VENTILATION IN MEN'S AND WOMEN'S WASHROOMS NEXT TO DINING AREA. | 32. FOOD AND NON-FOOD CONTACT SURFACES PROPERLY DESIGNED, CONSTRUCTED AND MAINTAINED - Comments: All food and non-food contact equipment and utensils shall be smooth, easily cleanable, and durable, and shall be in good repair.  REPAIR DAMAGED PLUG ON LEFT SIDE OF 2 COMPARTMENT SINK.  REPAIR SELF CLOSER ON BOTTOM LEFT DOOR OF 4 DOOR PREP UNIT NEXT TO OFFICE.")]


## <a name="create-a-logistic-regression-model-from-the-input-dataframe"></a>Crear un modelo de regresión logística desde la entrada dataframe

La tarea final es convertir los datos con etiqueta en un formato que se puede analizar regresión logística. La entrada a un algoritmo de regresión logística debe ser un conjunto de *pares de etiqueta característica vectorial*, donde la "vector característica" es un vector de números que representa el punto de entrada de alguna forma. Por lo tanto, se necesita una manera de convertir la columna "violaciones", que es semiestructurados y contiene una gran cantidad de comentarios en texto sin formato, en una matriz de números reales que es fácil imaginar un equipo. 

Un equipo estándar enfoque para el procesamiento de lenguaje natural de aprendizaje es asignar a cada palabra distinct un "índice" y, a continuación, pase vector al equipo algoritmo de aprendizaje que el valor de cada índice contiene la frecuencia relativa de esa palabra en la cadena de texto. 

MLLib proporciona una forma sencilla de realizar esta operación. En primer lugar, se le "convertir" cada cadena violaciones para obtener las palabras individuales en cada cadena y, a continuación, usaremos un `HashingTF` para convertir cada conjunto de símbolos en un vector de característica que se puede pasar al algoritmo de regresión logística para crear un modelo. Deberá realizar todos estos pasos en secuencia mediante una canalización de"".


    tokenizer = Tokenizer(inputCol="violations", outputCol="words")
    hashingTF = HashingTF(inputCol=tokenizer.getOutputCol(), outputCol="features")
    lr = LogisticRegression(maxIter=10, regParam=0.01)
    pipeline = Pipeline(stages=[tokenizer, hashingTF, lr])
    
    model = pipeline.fit(labeledData)


## <a name="evaluate-the-model-on-a-separate-test-dataset"></a>Evaluar el modelo en un conjunto de datos de prueba independiente

Podemos utilizar el modelo que se creó anteriormente *predecir* los resultados de inspecciones nuevas será, en función de las violaciones que se observan. Habían formado por este modelo en el conjunto de datos **Food_Inspections1.csv**. Usemos un segundo conjunto de datos, **Food_Inspections2.csv**, para *evaluar* la intensidad de este modelo de datos nuevos. En este segundo conjunto de datos (**Food_Inspections2.csv**) debe estar en el contenedor de almacenamiento predeterminado asociado con el clúster.

1. El fragmento de código siguiente crea una nueva dataframe, **predictionsDf** que contiene la predicción generada por el modelo. El fragmento de código también crea una tabla de temporal **que predicciones** en función de la dataframe.


        testData = sc.textFile('wasbs:///HdiSamples/HdiSamples/FoodInspectionData/Food_Inspections2.csv')\
                 .map(csvParse) \
                 .map(lambda l: (int(l[0]), l[1], l[12], l[13]))
        testDf = sqlContext.createDataFrame(testData, schema).where("results = 'Fail' OR results = 'Pass' OR results = 'Pass w/ Conditions'")
        predictionsDf = model.transform(testDf)
        predictionsDf.registerTempTable('Predictions')
        predictionsDf.columns


    Debe ver un resultado similar al siguiente:
    
        # -----------------
        # THIS IS AN OUTPUT
        # -----------------
        
        ['id',
         'name',
         'results',
         'violations',
         'words',
         'features',
         'rawPrediction',
         'probability',
         'prediction']

2. Busque una de las predicciones. Ejecutar este fragmento de código:

        predictionsDf.take(1)

    Verá la predicción de la primera entrada en el conjunto de datos de prueba.

3. La `model.transform()` método aplicar la misma transformación a los datos nuevos con el mismo esquema y llegan a la predicción de cómo clasificar los datos. Podemos hacer algunas estadísticas simples para hacerse una idea de nuestras predicciones ¿qué precisión tienen:


        numSuccesses = predictionsDf.where("""(prediction = 0 AND results = 'Fail') OR 
                                              (prediction = 1 AND (results = 'Pass' OR 
                                                                   results = 'Pass w/ Conditions'))""").count()
        numInspections = predictionsDf.count()
        
        print "There were", numInspections, "inspections and there were", numSuccesses, "successful predictions"
        print "This is a", str((float(numSuccesses) / float(numInspections)) * 100) + "%", "success rate"

    El resultado es similar a la siguiente:
    
        # -----------------
        # THIS IS AN OUTPUT
        # -----------------
    
        There were 9315 inspections and there were 8087 successful predictions
        This is a 86.8169618894% success rate


    Usar regresión logística con motor nos ofrece un modelo de precisión de la relación entre las descripciones de las transgresiones en inglés y, si una compañía dada ¿pasar o no se realice correctamente una inspección de comida. 

## <a name="create-a-visual-representation-of-the-prediction"></a>Crear una representación visual de la predicción

Ahora podemos crear una visualización final para ayudarnos a razón sobre los resultados de esta prueba. 

1. Empezaremos por extraer las predicciones diferentes y los resultados de la tabla temporal **predicciones** creada anteriormente. Las siguientes consultas separan el resultado como *true_positive*, *false_positive*, *true_negative*y *false_negative*. En las consultas a continuación, desactivar la visualización usando `-q` y también guardar los resultados (usando `-o`) como dataframes que, a continuación, se pueden usar con la `%%local` mágico. 

        %%sql -q -o true_positive
        SELECT count(*) AS cnt FROM Predictions WHERE prediction = 0 AND results = 'Fail'

        %%sql -q -o false_positive
        SELECT count(*) AS cnt FROM Predictions WHERE prediction = 0 AND (results = 'Pass' OR results = 'Pass w/ Conditions')

        %%sql -q -o true_negative
        SELECT count(*) AS cnt FROM Predictions WHERE prediction = 1 AND results = 'Fail'

        %%sql -q -o false_negative
        SELECT count(*) AS cnt FROM Predictions WHERE prediction = 1 AND (results = 'Pass' OR results = 'Pass w/ Conditions') 

2. Por último, use el fragmento de código siguiente para generar el trazado mediante **Matplotlib**.

        %%local
        %matplotlib inline
        import matplotlib.pyplot as plt
        
        labels = ['True positive', 'False positive', 'True negative', 'False negative']
        sizes = [true_positive['cnt'], false_positive['cnt'], false_negative['cnt'], true_negative['cnt']]
        colors = ['turquoise', 'seagreen', 'mediumslateblue', 'palegreen', 'coral']
        plt.pie(sizes, labels=labels, autopct='%1.1f%%', colors=colors)
        plt.axis('equal')
    
    Verá el siguiente resultado.
    
    ![Resultados de predicción](./media/hdinsight-apache-spark-machine-learning-mllib-ipython/output_26_1.png)


    En este gráfico, un resultado "positivo" hace referencia a la inspección de comida error, mientras que un resultado negativo hace referencia a una inspección pasada.

## <a name="shut-down-the-notebook"></a>Cierre el Bloc de notas

Cuando haya terminado de ejecutar la aplicación, debe cerrar el Bloc de notas para liberar los recursos. Para ello, en el menú **archivo** en el Bloc de notas, haga clic en **Cerrar y detener**. Este se cerrará y cierre el Bloc de notas.


## <a name="seealso"></a>Vea también


* [Información general: Apache motor en HDInsight de Azure](hdinsight-apache-spark-overview.md)

### <a name="scenarios"></a>Escenarios

* [Motor con BI: realizar análisis de datos interactivas con motor en HDInsight con herramientas de inteligencia empresarial](hdinsight-apache-spark-use-bi-tools.md)

* [Motor con Machine aprendizaje: motor de uso en HDInsight para analizar usando HVAC datos de temperatura de creación](hdinsight-apache-spark-ipython-notebook-machine-learning.md)

* [Motor de flujo de datos: Motor de uso en HDInsight para la creación de aplicaciones de transmisión en tiempo real](hdinsight-apache-spark-eventhub-streaming.md)

* [Análisis de registro de sitio Web con el motor en HDInsight](hdinsight-apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Crear y ejecutar aplicaciones

* [Crear una aplicación independiente de Scala](hdinsight-apache-spark-create-standalone-application.md)

* [Ejecutar trabajos de forma remota en un clúster de motor mediante Livio](hdinsight-apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Herramientas y extensiones

* [Usar el complemento de herramientas de HDInsight para IntelliJ IDEA para crear y enviar el motor Scala aplicaciones](hdinsight-apache-spark-intellij-tool-plugin.md)

* [Usar el complemento de herramientas de HDInsight para IntelliJ IDEA depurar aplicaciones del motor de forma remota](hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)

* [Utilizar blocs de notas de globo dirigible con un clúster de motor en HDInsight](hdinsight-apache-spark-use-zeppelin-notebook.md)

* [Kernels disponibles para el Bloc de notas de Jupyter en clúster de motor de HDInsight](hdinsight-apache-spark-jupyter-notebook-kernels.md)

* [Usar paquetes externos con blocs de notas Jupyter](hdinsight-apache-spark-jupyter-notebook-use-external-packages.md)

* [Instalar Jupyter en su equipo y conectarse a un clúster de HDInsight Spark](hdinsight-apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Administrar recursos

* [Administrar recursos para el clúster de motor de Apache en HDInsight de Azure](hdinsight-apache-spark-resource-manager.md)

* [Realizar el seguimiento y depuración trabajos que se ejecutan en un clúster de motor de Apache en HDInsight](hdinsight-apache-spark-job-debugging.md)
