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


# <a name="build-machine-learning-applications-to-run-on-apache-spark-clusters-on-hdinsight-linux"></a>Crear aplicaciones de aprendizaje de máquina para que se ejecute en clústeres Apache motor en HDInsight Linux

Obtenga información sobre cómo crear una aplicación mediante un clúster de Apache motor de HDInsight de aprendizaje de máquina. Este artículo le muestra cómo usar el Bloc de notas de Jupyter disponible con el clúster para crear y probar la aplicación. La aplicación usa los datos de ejemplo HVAC.csv que está disponibles en todos los clústeres de forma predeterminada.

**Requisitos previos:**

Debe tener el siguiente:

- Una suscripción de Azure. Vea la [versión de prueba gratuita de Azure obtener](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
- Un clúster de motor de Apache en HDInsight Linux. Para obtener instrucciones, vea [crear Apache motor clústeres en HDInsight de Azure](hdinsight-apache-spark-jupyter-spark-sql.md). 

##<a name="data"></a>Mostrar los datos

Antes de empezar a crear la aplicación, deje que nosotros comprender la estructura de los datos y el tipo de análisis que haremos en los datos. 

En este artículo, se utiliza el archivo de datos de ejemplo **HVAC.csv** que está disponible en la cuenta de Azure almacenamiento asociado con el clúster de HDInsight. Dentro de la cuenta de almacenamiento, el archivo está en **\HdiSamples\HdiSamples\SensorSampleData\hvac**. Descargar y abrir el archivo CSV para obtener una instantánea de los datos.  

![Instantánea de datos HVAC] (./media/hdinsight-apache-spark-ipython-notebook-machine-learning/hdispark.ml.show.data.png "Instantánea de los datos HVAC")

Los datos muestran la temperatura de destino y la temperatura real de un edificio con sistemas HVAC instalados. Supongamos que la columna del **sistema** representa el identificador del sistema y la columna **SystemAge** representa el número de años que ha sido el sistema HVAC en lugar de la creación.

Usamos estos datos para predecir si un edificio serán temperaturas o colder basados en la temperatura de destino, dada un identificador de sistema y la edad de sistema.

##<a name="app"></a>Escribir una aplicación de aprendizaje de equipo mediante motor MLlib

En esta aplicación usamos una canalización motor m para realizar una clasificación de documento. En la canalización se divide palabras en el documento, convertir las palabras en un vector característica numérica y finalmente generar un modelo de predicción con vectores característica y etiquetas. Realice los pasos siguientes para crear la aplicación.

1. Desde el [Portal de Azure](https://portal.azure.com/), desde el startboard, haga clic en el mosaico de clúster motor (si lo anclados a la startboard). También puede ir a su clúster en **Examinar todos** > **HDInsight clústeres**.   

2. Desde el módulo de clúster motor, haga clic en **Panel de diseño**y, a continuación, haga clic en **Bloc de notas de Jupyter**. Si se le solicita, escriba las credenciales de administrador para el clúster.

    > [AZURE.NOTE] También puede contactar el Bloc de notas de Jupyter para el clúster abriendo la siguiente URL en el explorador. Reemplazar __NOMBREDECLÚSTER__ con el nombre del clúster:
    >
    > `https://CLUSTERNAME.azurehdinsight.net/jupyter`

2. Crear un nuevo bloc de notas. Haga clic en **nuevo**y, a continuación, haga clic en **PySpark**.

    ![Crear un nuevo bloc de notas de Jupyter] (./media/hdinsight-apache-spark-ipython-notebook-machine-learning/hdispark.note.jupyter.createnotebook.png "Crear un nuevo bloc de notas de Jupyter")

3. Un bloc de notas se crea y se abre con el nombre Untitled.pynb. Haga clic en el nombre del Bloc de notas en la parte superior y escriba un nombre descriptivo.

    ![Proporcionar un nombre para el Bloc de notas] (./media/hdinsight-apache-spark-ipython-notebook-machine-learning/hdispark.note.jupyter.notebook.name.png "Proporcionar un nombre para el Bloc de notas")

3. Ya ha creado un bloc de notas con el núcleo PySpark, no es necesario crear cualquier contextos explícitamente. Los contextos motor y subárbol se crearán automáticamente para usted cuando se ejecuta la primera celda de código. Puede empezar importando los tipos necesarios para este escenario. Pegue el fragmento de código siguiente en una celda vacía y, a continuación, presione **MAYÚS + ENTRAR**. 

        from pyspark.ml import Pipeline
        from pyspark.ml.classification import LogisticRegression
        from pyspark.ml.feature import HashingTF, Tokenizer
        from pyspark.sql import Row
        
        import os
        import sys
        from pyspark.sql.types import *
        
        from pyspark.mllib.classification import LogisticRegressionWithSGD
        from pyspark.mllib.regression import LabeledPoint
        from numpy import array
        
        
     
4. Ahora debe cargar los datos (hvac.csv), analizarla y usarlo para entrenar el modelo. Para ello, defina una función que comprueba si la temperatura real del edificio es mayor que la temperatura de destino. Si la temperatura real es mayor, la creación está caliente, indicado por el valor **1.0**. Si la temperatura real es menor, la creación es fría, indicado por el valor **0.0**. 

    Pegue el fragmento de código siguiente en una celda vacía y presione **MAYÚS + ENTRAR**.

        
        # List the structure of data for better understanding. Becuase the data will be
        # loaded as an array, this structure makes it easy to understand what each element
        # in the array corresponds to

        # 0 Date
        # 1 Time
        # 2 TargetTemp
        # 3 ActualTemp
        # 4 System
        # 5 SystemAge
        # 6 BuildingID

        LabeledDocument = Row("BuildingID", "SystemInfo", "label")

        # Define a function that parses the raw CSV file and returns an object of type LabeledDocument
        
        def parseDocument(line):
            values = [str(x) for x in line.split(',')]
            if (values[3] > values[2]):
                hot = 1.0
            else:
                hot = 0.0        
    
            textValue = str(values[4]) + " " + str(values[5])
    
            return LabeledDocument((values[6]), textValue, hot)

        # Load the raw HVAC.csv file, parse it using the function
        data = sc.textFile("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")

        documents = data.filter(lambda s: "Date" not in s).map(parseDocument)
        training = documents.toDF()


5. Configurar el proceso de aprendizaje de máquina motor que consta de tres fases: analizador, hashingTF y lr. Para obtener más información acerca de qué es una canalización y cómo funciona, consulte <a href="http://spark.apache.org/docs/latest/ml-guide.html#how-it-works" target="_blank">máquina motor canalización de aprendizaje</a>.

    Pegue el fragmento de código siguiente en una celda vacía y presione **MAYÚS + ENTRAR**.

        tokenizer = Tokenizer(inputCol="SystemInfo", outputCol="words")
        hashingTF = HashingTF(inputCol=tokenizer.getOutputCol(), outputCol="features")
        lr = LogisticRegression(maxIter=10, regParam=0.01)
        pipeline = Pipeline(stages=[tokenizer, hashingTF, lr])

6. Ajustar la canalización de documento de aprendizaje. Pegue el fragmento de código siguiente en una celda vacía y presione **MAYÚS + ENTRAR**.

        model = pipeline.fit(training)

7. Compruebe el documento de aprendizaje para el control en el progreso de la aplicación. Pegue el fragmento de código siguiente en una celda vacía y presione **MAYÚS + ENTRAR**.

        training.show()

    Esto debería darle el resultado similar al siguiente:

        +----------+----------+-----+
        |BuildingID|SystemInfo|label|
        +----------+----------+-----+
        |         4|     13 20|  0.0|
        |        17|      3 20|  0.0|
        |        18|     17 20|  1.0|
        |        15|      2 23|  0.0|
        |         3|      16 9|  1.0|
        |         4|     13 28|  0.0|
        |         2|     12 24|  0.0|
        |        16|     20 26|  1.0|
        |         9|      16 9|  1.0|
        |        12|       6 5|  0.0|
        |        15|     10 17|  1.0|
        |         7|      2 11|  0.0|
        |        15|      14 2|  1.0|
        |         6|       3 2|  0.0|
        |        20|     19 22|  0.0|
        |         8|     19 11|  0.0|
        |         6|      15 7|  0.0|
        |        13|      12 5|  0.0|
        |         4|      8 22|  0.0|
        |         7|      17 5|  0.0|
        +----------+----------+-----+


    Vuelva atrás y compruebe el resultado en el archivo CSV sin formato. Por ejemplo, la primera fila del archivo CSV con estos datos:

    ![Instantánea de datos HVAC] (./media/hdinsight-apache-spark-ipython-notebook-machine-learning/hdispark.ml.show.data.first.row.png "Instantánea de los datos HVAC")

    Observe cómo la temperatura real es menor que la temperatura de destino que sugiriendo que edificio esté frío. Por lo tanto, en el resultado de formación, el valor de la **etiqueta** en la primera fila es **0.0**, lo que significa que la creación no está activa.

8.  Preparar un conjunto de datos para ejecutar el modelo capacitado contra. Para ello, podría pasar en un identificador de sistema y la edad de sistema (indicado como **SystemInfo** en el resultado de formación) y el modelo ¿predecir si la creación con ese Id. del sistema y la edad de sistema sería temperaturas (indicado por 1.0) o refrigerador (indicado por 0.0).

    Pegue el fragmento de código siguiente en una celda vacía y presione **MAYÚS + ENTRAR**.
        
        # SystemInfo here is a combination of system ID followed by system age
        Document = Row("id", "SystemInfo")
        test = sc.parallelize([(1L, "20 25"),
                      (2L, "4 15"),
                      (3L, "16 9"),
                      (4L, "9 22"),
                      (5L, "17 10"),
                      (6L, "7 22")]) \
            .map(lambda x: Document(*x)).toDF() 

9. Por último, realizar predicciones en los datos de prueba. Pegue el fragmento de código siguiente en una celda vacía y presione **MAYÚS + ENTRAR**.

        # Make predictions on test documents and print columns of interest
        prediction = model.transform(test)
        selected = prediction.select("SystemInfo", "prediction", "probability")
        for row in selected.collect():
            print row

10. Debe ver un resultado similar al siguiente:

        Row(SystemInfo=u'20 25', prediction=1.0, probability=DenseVector([0.4999, 0.5001]))
        Row(SystemInfo=u'4 15', prediction=0.0, probability=DenseVector([0.5016, 0.4984]))
        Row(SystemInfo=u'16 9', prediction=1.0, probability=DenseVector([0.4785, 0.5215]))
        Row(SystemInfo=u'9 22', prediction=1.0, probability=DenseVector([0.4549, 0.5451]))
        Row(SystemInfo=u'17 10', prediction=1.0, probability=DenseVector([0.4925, 0.5075]))
        Row(SystemInfo=u'7 22', prediction=0.0, probability=DenseVector([0.5015, 0.4985]))

    Desde la primera fila de la predicción, puede ver que para un sistema HVAC con identificador de 20 y la edad de sistema de 25 años, edificio estará activo (**predicción = 1.0**). El primer valor de DenseVector (0.49999) corresponde a la predicción 0,0 y el segundo valor (0.5001) corresponde a la predicción 1.0. En el resultado, aunque el segundo valor solo es un poco más, el modelo de muestra **predicción = 1.0**.

11. Cuando haya terminado de ejecutar la aplicación, debe cerrar el Bloc de notas para liberar los recursos. Para ello, en el menú **archivo** en el Bloc de notas, haga clic en **Cerrar y detener**. Este se cerrará y cierre el Bloc de notas.
           

##<a name="anaconda"></a>Usar Anaconda scikit-obtener biblioteca para el aprendizaje

Motor de Apache clústeres en HDInsight incluyen bibliotecas Anaconda. Esto incluye también la **scikit-obtener** biblioteca para el aprendizaje. La biblioteca también incluye varios conjuntos de datos que puede usar para generar aplicaciones de ejemplo directamente desde un bloc de notas de Jupyter. Para obtener ejemplos sobre el uso de la scikit-obtener la biblioteca, consulte [http://scikit-learn.org/stable/auto_examples/index.html](http://scikit-learn.org/stable/auto_examples/index.html).

##<a name="seealso"></a>Vea también

* [Información general: Apache motor en HDInsight de Azure](hdinsight-apache-spark-overview.md)

### <a name="scenarios"></a>Escenarios

* [Motor con BI: realizar análisis de datos interactivas con motor en HDInsight con herramientas de inteligencia empresarial](hdinsight-apache-spark-use-bi-tools.md)

* [Motor con Machine aprendizaje: motor de uso en HDInsight de predecir los resultados de la inspección de comida](hdinsight-apache-spark-machine-learning-mllib-ipython.md)

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


[hdinsight-versions]: hdinsight-component-versioning.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md

[hdinsight-weblogs-sample]: hdinsight-hive-analyze-website-log.md
[hdinsight-sensor-data-sample]: hdinsight-hive-analyze-sensor-data.md

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[azure-management-portal]: https://manage.windowsazure.com/
[azure-create-storageaccount]: storage-create-storage-account.md
