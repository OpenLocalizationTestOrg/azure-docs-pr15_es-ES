<properties 
    pageTitle="Utilizar bibliotecas personalizadas con un clúster de HDInsight Spark para analizar los registros de sitio Web | Microsoft Azure" 
    description="Usar bibliotecas personalizadas con un clúster HDInsight Spark para analizar los registros de sitio Web" 
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

# <a name="analyze-website-logs-using-a-custom-library-with-apache-spark-cluster-on-hdinsight-linux"></a>Analizar los registros de sitio Web con una biblioteca personalizada con clúster de motor de Apache en HDInsight Linux

Este bloc de notas muestra cómo analizar los datos de registro con una biblioteca personalizada con motor en HDInsight. La biblioteca personalizada que usamos es una biblioteca de Python denominada **iislogparser.py**.

> [AZURE.TIP] Este tutorial también está disponible como un bloc de notas de Jupyter en un clúster de motor (Linux) que cree en HDInsight. La experiencia de Bloc de notas le permite ejecutar los fragmentos de código de Python desde el Bloc de notas. Para realizar el tutorial desde dentro de un bloc de notas, cree un clúster de motor, inicie un bloc de notas de Jupyter (`https://CLUSTERNAME.azurehdinsight.net/jupyter`), y luego ejecute el Bloc de notas **analizar registros con motor mediante un library.ipynb personalizado** en la carpeta **PySpark** .

**Requisitos previos:**

Debe tener el siguiente:

- Una suscripción de Azure. Vea la [versión de prueba gratuita de Azure obtener](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
- Un clúster de motor de Apache en HDInsight Linux. Para obtener instrucciones, vea [crear Apache motor clústeres en HDInsight de Azure](hdinsight-apache-spark-jupyter-spark-sql.md).

## <a name="save-raw-data-as-an-rdd"></a>Guardar datos sin formato como un diseño dirigido por responsabilidades

En esta sección, usamos el Bloc de notas de [Jupyter](https://jupyter.org) asociada a un clúster de motor de Apache en HDInsight para ejecutar trabajos que se procesan los datos de ejemplo sin formato y guárdelo como una tabla subárbol. Los datos de ejemplo están un archivo .csv (hvac.csv) disponible en todos los clústeres de forma predeterminada.

Una vez que sus datos se guardan como una tabla subárbol, en la siguiente sección conectaremos a la tabla subárbol con herramientas de BI como Power BI y una plantilla.

1. Desde el [Portal de Azure](https://portal.azure.com/), desde el startboard, haga clic en el mosaico de clúster motor (si lo anclados a la startboard). También puede ir a su clúster en **Examinar todos** > **HDInsight clústeres**.   

2. Desde el módulo de clúster motor, haga clic en **Panel de diseño**y, a continuación, haga clic en **Bloc de notas de Jupyter**. Si se le solicita, escriba las credenciales de administrador para el clúster.

    > [AZURE.NOTE] También puede contactar el Bloc de notas de Jupyter para el clúster abriendo la siguiente URL en el explorador. Reemplazar __NOMBREDECLÚSTER__ con el nombre del clúster:
    >
    > `https://CLUSTERNAME.azurehdinsight.net/jupyter`

2. Crear un nuevo bloc de notas. Haga clic en **nuevo**y, a continuación, haga clic en **PySpark**.

    ![Crear un nuevo bloc de notas de Jupyter] (./media/hdinsight-apache-spark-custom-library-website-log-analysis/hdispark.note.jupyter.createnotebook.png "Crear un nuevo bloc de notas de Jupyter")

3. Un bloc de notas se crea y se abre con el nombre Untitled.pynb. Haga clic en el nombre del Bloc de notas en la parte superior y escriba un nombre descriptivo.

    ![Proporcionar un nombre para el Bloc de notas] (./media/hdinsight-apache-spark-custom-library-website-log-analysis/hdispark.note.jupyter.notebook.name.png "Proporcionar un nombre para el Bloc de notas")

4. Ya ha creado un bloc de notas con el núcleo PySpark, no es necesario crear cualquier contextos explícitamente. Los contextos motor y subárbol se crearán automáticamente para usted cuando se ejecuta la primera celda de código. Puede empezar importando los tipos necesarios para este escenario. Pegue el fragmento de código siguiente en una celda vacía y, a continuación, presione **MAYÚS + ENTRAR**.


        from pyspark.sql import Row
        from pyspark.sql.types import *


5. Crear un diseño dirigido por responsabilidades usando los datos de registro de ejemplo ya están disponibles en el clúster. Puede obtener acceso a los datos de la cuenta de almacenamiento predeterminada asociado con el clúster en **\HdiSamples\HdiSamples\WebsiteLogSampleData\SampleLog\909f2b.log**.


        logs = sc.textFile('wasbs:///HdiSamples/HdiSamples/WebsiteLogSampleData/SampleLog/909f2b.log')


6. Recuperar un registro de muestra configurar para comprobar que el paso anterior que se completó correctamente.

        logs.take(5)

    Debe ver un resultado similar al siguiente:

        # -----------------
        # THIS IS AN OUTPUT
        # -----------------

        [u'#Software: Microsoft Internet Information Services 8.0',
         u'#Fields: date time s-sitename cs-method cs-uri-stem cs-uri-query s-port cs-username c-ip cs(User-Agent) cs(Cookie) cs(Referer) cs-host sc-status sc-substatus sc-win32-status sc-bytes cs-bytes time-taken',
         u'2014-01-01 02:01:09 SAMPLEWEBSITE GET /blogposts/mvc4/step2.png X-ARR-LOG-ID=2ec4b8ad-3cf0-4442-93ab-837317ece6a1 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 53175 871 46',
         u'2014-01-01 02:01:09 SAMPLEWEBSITE GET /blogposts/mvc4/step3.png X-ARR-LOG-ID=9eace870-2f49-4efd-b204-0d170da46b4a 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 51237 871 32',
         u'2014-01-01 02:01:09 SAMPLEWEBSITE GET /blogposts/mvc4/step4.png X-ARR-LOG-ID=4bea5b3d-8ac9-46c9-9b8c-ec3e9500cbea 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 72177 871 47']

## <a name="analyze-log-data-using-a-custom-python-library"></a>Analizar datos de registro con una biblioteca de Python personalizada

7. En el resultado anterior, las primeras líneas de par incluyan la información de encabezado y cada línea restante coincide con el esquema descrito en dicho encabezado. Analizar estos registros podría ser complicado. Por lo tanto, usamos una biblioteca de Python personalizada (**iislogparser.py**) que ayuda a analizar estos registros mucho más fácil. De forma predeterminada, esta biblioteca se incluye con el clúster de motor en HDInsight en **/HdiSamples/HdiSamples/WebsiteLogSampleData/iislogparser.py**.

    Sin embargo, esta biblioteca no está en la `PYTHONPATH` para que no podemos utilizarlo mediante una instrucción de importación como `import iislogparser`. Para usar esta biblioteca, nos debemos distribuir a todos los nodos de trabajo. Ejecutar el fragmento de código siguiente.


        sc.addPyFile('wasbs:///HdiSamples/HdiSamples/WebsiteLogSampleData/iislogparser.py')


9. `iislogparser`Proporciona una función `parse_log_line` que devuelve `None` si una línea de registro es una fila de encabezado y devuelve una instancia de la `LogLine` si encuentra una línea de registro de la clase. Usar el `LogLine` clase extraer solo las líneas de registro de la RDD:

        def parse_line(l):
            import iislogparser
            return iislogparser.parse_log_line(l)
        logLines = logs.map(parse_line).filter(lambda p: p is not None).cache()


10. Recuperar un par de líneas extraídos de registro para comprobar que el paso que se ha completado correctamente.

        logLines.take(2)

    El resultado debería ser similar al siguiente:

        # -----------------
        # THIS IS AN OUTPUT
        # -----------------

        [2014-01-01 02:01:09 SAMPLEWEBSITE GET /blogposts/mvc4/step2.png X-ARR-LOG-ID=2ec4b8ad-3cf0-4442-93ab-837317ece6a1 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 53175 871 46,
        2014-01-01 02:01:09 SAMPLEWEBSITE GET /blogposts/mvc4/step3.png X-ARR-LOG-ID=9eace870-2f49-4efd-b204-0d170da46b4a 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 51237 871 32]


11. La `LogLine` clase, a su vez, tiene algunos métodos útiles, como `is_error()`, que indica si una entrada de registro tiene un código de error. Use esta opción para calcular el número de errores en las líneas de registro extraídos e inicie todos los errores en un archivo diferente.

        errors = logLines.filter(lambda p: p.is_error())
        numLines = logLines.count()
        numErrors = errors.count()
        print 'There are', numErrors, 'errors and', numLines, 'log entries'
        errors.map(lambda p: str(p)).saveAsTextFile('wasbs:///HdiSamples/HdiSamples/WebsiteLogSampleData/SampleLog/909f2b-2.log')

    Debe ver un resultado similar al siguiente:

        # -----------------
        # THIS IS AN OUTPUT
        # -----------------

        There are 30 errors and 646 log entries

12. También puede usar **Matplotlib** para crear una visualización de los datos. Por ejemplo, si desea aislar a la causa de solicitudes que se ejecutan durante mucho tiempo, que desee buscar los archivos que ocupan más tiempo para servir en promedio.
El fragmento de código siguiente recupera los recursos de 25 superiores que realizó la mayor parte del tiempo para atender una solicitud.

        def avgTimeTakenByKey(rdd):
            return rdd.combineByKey(lambda line: (line.time_taken, 1),
                                    lambda x, line: (x[0] + line.time_taken, x[1] + 1),
                                    lambda x, y: (x[0] + y[0], x[1] + y[1]))\
                      .map(lambda x: (x[0], float(x[1][0]) / float(x[1][1])))
            
        avgTimeTakenByKey(logLines.map(lambda p: (p.cs_uri_stem, p))).top(25, lambda x: x[1])

    Debe ver un resultado similar al siguiente:

        # -----------------
        # THIS IS AN OUTPUT
        # -----------------

        [(u'/blogposts/mvc4/step13.png', 197.5),
         (u'/blogposts/mvc2/step10.jpg', 179.5),
         (u'/blogposts/extractusercontrol/step5.png', 170.0),
         (u'/blogposts/mvc4/step8.png', 159.0),
         (u'/blogposts/mvcrouting/step22.jpg', 155.0),
         (u'/blogposts/mvcrouting/step3.jpg', 152.0),
         (u'/blogposts/linqsproc1/step16.jpg', 138.75),
         (u'/blogposts/linqsproc1/step26.jpg', 137.33333333333334),
         (u'/blogposts/vs2008javascript/step10.jpg', 127.0),
         (u'/blogposts/nested/step2.jpg', 126.0),
         (u'/blogposts/adminpack/step1.png', 124.0),
         (u'/BlogPosts/datalistpaging/step2.png', 118.0),
         (u'/blogposts/mvc4/step35.png', 117.0),
         (u'/blogposts/mvcrouting/step2.jpg', 116.5),
         (u'/blogposts/aboutme/basketball.jpg', 109.0),
         (u'/blogposts/anonymoustypes/step11.jpg', 109.0),
         (u'/blogposts/mvc4/step12.png', 106.0),
         (u'/blogposts/linq8/step0.jpg', 105.5),
         (u'/blogposts/mvc2/step18.jpg', 104.0),
         (u'/blogposts/mvc2/step11.jpg', 104.0),
         (u'/blogposts/mvcrouting/step1.jpg', 104.0),
         (u'/blogposts/extractusercontrol/step1.png', 103.0),
         (u'/blogposts/sqlvideos/sqlvideos.jpg', 102.0),
         (u'/blogposts/mvcrouting/step21.jpg', 101.0),
         (u'/blogposts/mvc4/step1.png', 98.0)]


13. También puede presentar esta información en el formulario de trazado. Como primer paso para crear un gráfico, deje que nosotros crea una tabla temporal **AverageTime**por primera vez. La tabla agrupa los registros por tiempo para ver si hay cualquier picos de latencia inusual en cualquier momento determinado.

        avgTimeTakenByMinute = avgTimeTakenByKey(logLines.map(lambda p: (p.datetime.minute, p))).sortByKey()
        schema = StructType([StructField('Minutes', IntegerType(), True),
                             StructField('Time', FloatType(), True)])
                             
        avgTimeTakenByMinuteDF = sqlContext.createDataFrame(avgTimeTakenByMinute, schema)
        avgTimeTakenByMinuteDF.registerTempTable('AverageTime')

14. A continuación, puede ejecutar la siguiente consulta SQL para obtener todos los registros de la tabla **AverageTime** .

        %%sql -o averagetime
        SELECT * FROM AverageTime

    La `%%sql` mágico seguido `-o averagetime` garantiza que los resultados de la consulta se conservan localmente en el servidor de Jupyter (normalmente la headnode del clúster). El resultado se conserva como un dataframe [Pandas](http://pandas.pydata.org/) con el nombre especificado **averagetime**.

    Debe ver un resultado similar al siguiente:

    ![Resultado de la consulta SQL] (./media/hdinsight-apache-spark-custom-library-website-log-analysis/sql.output.png "Resultado de la consulta SQL")

    Para obtener más información sobre la `%%sql` mágico, así como otras magics disponibles con el núcleo PySpark, vea [Kernels disponibles en los blocs de notas de Jupyter con HDInsight de motor clústeres](hdinsight-apache-spark-jupyter-notebook-kernels.md#why-should-i-use-the-new-kernels).

15. Ahora puede usar Matplotlib, una biblioteca que usa para generar la visualización de datos, para crear un gráfico. Dado que debe crearse el trazado desde la dataframe persistente localmente **averagetime** , el fragmento de código debe comenzar con la `%%local` mágico. Esto garantiza que el código se ejecuta localmente en el servidor Jupyter.

        %%local
        %matplotlib inline
        import matplotlib.pyplot as plt
        
        plt.plot(averagetime['Minutes'], averagetime['Time'], marker='o', linestyle='--')
        plt.xlabel('Time (min)')
        plt.ylabel('Average time taken for request (ms)')

    Debe ver un resultado similar al siguiente:

    ![Resultado de Matplotlib] (./media/hdinsight-apache-spark-custom-library-website-log-analysis/hdi-apache-spark-web-log-analysis-plot.png "Resultado de Matplotlib")

16. Cuando haya terminado de ejecutar la aplicación, debe cerrar el Bloc de notas para liberar los recursos. Para ello, en el menú **archivo** en el Bloc de notas, haga clic en **Cerrar y detener**. Este se cerrará y cierre el Bloc de notas.
    

## <a name="seealso"></a>Vea también


* [Información general: Apache motor en HDInsight de Azure](hdinsight-apache-spark-overview.md)

### <a name="scenarios"></a>Escenarios

* [Motor con BI: realizar análisis de datos interactivas con motor en HDInsight con herramientas de inteligencia empresarial](hdinsight-apache-spark-use-bi-tools.md)

* [Motor con Machine aprendizaje: motor de uso en HDInsight para analizar usando HVAC datos de temperatura de creación](hdinsight-apache-spark-ipython-notebook-machine-learning.md)

* [Motor con Machine aprendizaje: motor de uso en HDInsight de predecir los resultados de la inspección de comida](hdinsight-apache-spark-machine-learning-mllib-ipython.md)

* [Motor Streaming: Motor de uso en HDInsight para generar aplicaciones streaming en tiempo real](hdinsight-apache-spark-eventhub-streaming.md)

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
