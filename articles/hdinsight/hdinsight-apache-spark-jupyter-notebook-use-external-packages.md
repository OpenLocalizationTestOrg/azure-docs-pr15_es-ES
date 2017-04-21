<properties 
    pageTitle="Usar paquetes externos con blocs de notas de Jupyter en clústeres Apache motor en HDInsight | Azure"
    description="Instrucciones paso a paso sobre cómo configurar blocs de notas de Jupyter disponibles con HDInsight Spark clústeres usar paquetes de motor externos." 
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
    ms.date="10/28/2016" 
    ms.author="nitinme"/>


# <a name="use-external-packages-with-jupyter-notebooks-in-apache-spark-clusters-on-hdinsight-linux"></a>Usar paquetes externos con blocs de notas de Jupyter en clústeres Apache motor en HDInsight Linux

>[AZURE.NOTE] En este artículo es aplicable al motor 1.5.2 en HDInsight 3.3 y motor 1.6.2 en HDInsight 3.4. 

Obtenga información sobre cómo configurar un bloc de notas de Jupyter en clúster de motor de Apache en HDInsight (Linux) usar externo, paquetes contribuido a la Comunidad que no incluyen del cuadro en el clúster. 

Puede buscar en el [repositorio de experto](http://search.maven.org/) para la lista completa de paquetes que están disponibles. También puede obtener una lista de paquetes disponibles de otros orígenes. Por ejemplo, una lista completa de paquetes contribuido Comunidad está disponible en [Los paquetes de motor](http://spark-packages.org/).

En este artículo, aprenderá cómo usar el paquete de [motor csv](http://search.maven.org/#artifactdetails%7Ccom.databricks%7Cspark-csv_2.10%7C1.4.0%7Cjar) con el Bloc de notas de Jupyter.

##<a name="prerequisites"></a>Requisitos previos

Debe tener el siguiente:

- Una suscripción de Azure. Vea la [versión de prueba gratuita de Azure obtener](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
- Un clúster de motor de Apache en HDInsight Linux. Para obtener instrucciones, vea [crear Apache motor clústeres en HDInsight de Azure](hdinsight-apache-spark-jupyter-spark-sql.md).

## <a name="use-external-packages-with-jupyter-notebooks"></a>Usar paquetes externos con blocs de notas Jupyter 

1. Desde el [Portal de Azure](https://portal.azure.com/), desde el startboard, haga clic en el mosaico de clúster motor (si lo anclados a la startboard). También puede ir a su clúster en **Examinar todos** > **HDInsight clústeres**.   

2. Desde el módulo de clúster motor, haga clic en **Vínculos rápidos**y, en el módulo de **Clúster paneles** , haga clic en **Bloc de notas de Jupyter**. Si se le solicita, escriba las credenciales de administrador para el clúster.

    > [AZURE.NOTE] También puede contactar el Bloc de notas de Jupyter para el clúster abriendo la siguiente URL en el explorador. Reemplazar __NOMBREDECLÚSTER__ con el nombre del clúster:
    >
    > `https://CLUSTERNAME.azurehdinsight.net/jupyter`

2. Crear un nuevo bloc de notas. Haga clic en **nuevo**y, a continuación, haga clic en el **motor**.

    ![Crear un nuevo bloc de notas de Jupyter] (./media/hdinsight-apache-spark-jupyter-notebook-use-external-packages/hdispark.note.jupyter.createnotebook.png "Crear un nuevo bloc de notas de Jupyter")

3. Un bloc de notas se crea y se abre con el nombre Untitled.pynb. Haga clic en el nombre del Bloc de notas en la parte superior y escriba un nombre descriptivo.

    ![Proporcionar un nombre para el Bloc de notas] (./media/hdinsight-apache-spark-jupyter-notebook-use-external-packages/hdispark.note.jupyter.notebook.name.png "Proporcionar un nombre para el Bloc de notas")

4. Usará el `%%configure` mágico para configurar el Bloc de notas para usar un paquete externo. En blocs de notas que usan paquetes externos, asegúrese de que llame a la `%%configure` mágico en la primera celda de código. Esto garantiza que el núcleo está configurado para utilizar el paquete antes de inicia la sesión.

        %%configure
        { "packages":["com.databricks:spark-csv_2.10:1.4.0"] }


    >[AZURE.IMPORTANT] Si olvida configurar el núcleo en la primera celda, puede usar el `%%configure` con el `-f` parámetro, pero que se reinicie la sesión y el progreso de todas las se perderán.

5. En el fragmento de código anterior, `packages` espera una lista de las coordenadas de experto en repositorio Central de experto. En este fragmento de código, `com.databricks:spark-csv_2.10:1.4.0` es las coordenadas experto paquete **motor csv** . Esto es para construir las coordenadas de un paquete.

    una. Busque el paquete en el repositorio de experto. En este tutorial, usamos [motor csv](http://search.maven.org/#artifactdetails%7Ccom.databricks%7Cspark-csv_2.10%7C1.4.0%7Cjar).
    
    b. Desde el repositorio recopilar los valores de **ID**, **ArtifactId**y **versión**.

    ![Paquetes externos de uso con Jupyter Bloc de notas] (./media/hdinsight-apache-spark-jupyter-notebook-use-external-packages/use-external-packages-with-jupyter.png "Paquetes externos de uso con Jupyter Bloc de notas")

    c. Concatenar los tres valores, separados por un punto y coma (**:**).

        com.databricks:spark-csv_2.10:1.4.0

6. Ejecutar la celda con la `%%configure` mágico. Va a configurar la sesión de Livio subyacente para utilizar el paquete proporcionado. En las celdas siguientes en el Bloc de notas, ahora puede usar el paquete, como se muestra a continuación.

        val df = sqlContext.read.format("com.databricks.spark.csv").
        option("header", "true").
        option("inferSchema", "true").
        load("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")

7. A continuación, puede ejecutar los fragmentos de código, como se muestra a continuación, para ver los datos de la dataframe que creó en el paso anterior.

        df.show()

        df.select("Time").count()


## <a name="seealso"></a>Vea también


* [Información general: Apache motor en HDInsight de Azure](hdinsight-apache-spark-overview.md)

### <a name="scenarios"></a>Escenarios

* [Motor con BI: realizar análisis de datos interactivas con motor en HDInsight con herramientas de inteligencia empresarial](hdinsight-apache-spark-use-bi-tools.md)

* [Motor con Machine aprendizaje: motor de uso en HDInsight para analizar usando HVAC datos de temperatura de creación](hdinsight-apache-spark-ipython-notebook-machine-learning.md)

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

* [Instalar Jupyter en su equipo y conectarse a un clúster de HDInsight Spark](hdinsight-apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Administrar recursos

* [Administrar recursos para el clúster de motor de Apache en HDInsight de Azure](hdinsight-apache-spark-resource-manager.md)

* [Realizar el seguimiento y depuración trabajos que se ejecutan en un clúster de motor de Apache en HDInsight](hdinsight-apache-spark-job-debugging.md)
