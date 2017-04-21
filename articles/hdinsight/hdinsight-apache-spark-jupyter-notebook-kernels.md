<properties 
    pageTitle="Disponible con blocs de notas de Jupyter en HDInsight Spark kernels clústeres en Linux | Microsoft Azure" 
    description="Obtenga información sobre los kernels de Bloc de notas de Jupyter adicionales disponibles con el clúster de motor en HDInsight Linux." 
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


# <a name="kernels-available-for-jupyter-notebooks-with-apache-spark-clusters-on-hdinsight-linux"></a>Kernels disponibles para los blocs de notas de Jupyter con motor Apache clústeres en HDInsight Linux

Clúster de motor de Apache en HDInsight (Linux) incluye blocs de notas de Jupyter que puede usar para probar las aplicaciones. Un núcleo es un programa que se ejecuta e interpreta el código. HDInsight Spark clústeres proporcionan dos kernels que puede usar con el Bloc de notas de Jupyter. Estos son:

1. **PySpark** (para aplicaciones escritas en Python)
2. **Motor** (para aplicaciones escritas en Scala)

En este artículo, aprenderá cómo usar estos kernels y cuáles son las ventajas que obtiene de usarlos.

**Requisitos previos:**

Debe tener el siguiente:

- Una suscripción de Azure. Vea la [versión de prueba gratuita de Azure obtener](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
- Un clúster de motor de Apache en HDInsight Linux. Para obtener instrucciones, vea [crear Apache motor clústeres en HDInsight de Azure](hdinsight-apache-spark-jupyter-spark-sql.md).

## <a name="how-do-i-use-the-kernels"></a>¿Cómo se puede usar los kernels? 

1. Desde el [Portal de Azure](https://portal.azure.com/), desde el startboard, haga clic en el mosaico de clúster motor (si lo anclados a la startboard). También puede ir a su clúster en **Examinar todos** > **HDInsight clústeres**.   

2. Desde el módulo de clúster motor, haga clic en **Panel de diseño**y, a continuación, haga clic en **Bloc de notas de Jupyter**. Si se le solicita, escriba las credenciales de administrador para el clúster.

    > [AZURE.NOTE] También puede contactar el Bloc de notas de Jupyter para el clúster abriendo la siguiente URL en el explorador. Reemplazar __NOMBREDECLÚSTER__ con el nombre del clúster:
    >
    > `https://CLUSTERNAME.azurehdinsight.net/jupyter`

2. Crear un nuevo bloc de notas con el nuevo kernel. Haga clic en **nuevo**y, a continuación, haga clic en **Pyspark** o **motor**. Debe usar el kernel de motor para aplicaciones Scala y PySpark núcleo para las aplicaciones de Python.

    ![Crear un nuevo bloc de notas de Jupyter] (./media/hdinsight-apache-spark-jupyter-notebook-kernels/jupyter-kernels.png "Crear un nuevo bloc de notas de Jupyter") 

3. Esto debe abrir un bloc de notas con el núcleo seleccionado.

## <a name="why-should-i-use-the-pyspark-or-spark-kernels"></a>¿Por qué debo usar los kernels PySpark o motor?

Estas son algunas ventajas de usar la nuevos kernels.

1. **Preestablecidos contextos**. Con la **PySpark** o kernels **motor** que se proporcionan con blocs de notas Jupyter, no es necesario establecer el motor o subárbol contextos explícitamente antes de empezar a trabajar con la aplicación que se desarrolla; Estos son disponibles de forma predeterminada. Estos contextos son:

    * **sc** - para el contexto de motor
    * **sqlContext** - para el contexto de la sección


    Por lo tanto, no tiene que ejecutar instrucciones como las siguientes acciones para establecer los contextos:

        ###################################################
        # YOU DO NOT NEED TO RUN THIS WITH THE NEW KERNELS
        ###################################################
        sc = SparkContext('yarn-client')
        sqlContext = HiveContext(sc)

    En su lugar, puede usar los contextos preestablecidos directamente en la aplicación.
    
2. **Magics de celda**. El núcleo PySpark proporciona algunos predefinido "magics", que son comandos especiales que se pueden llamar con `%%` (por ejemplo, `%%MAGIC` <args>). El comando mágico debe ser la primera palabra en una celda de código y permitir varias líneas de contenido. La palabra mágica debe ser la primera palabra en la celda. Agregar cualquier cosa antes de la magia, incluso comentarios, se producirá un error.   Para obtener más información sobre magics, consulte [aquí](http://ipython.readthedocs.org/en/stable/interactive/magics.html).

    La siguiente tabla enumera los diferentes magics disponibles a través de los kernels.

  	| Mágico     | Ejemplo                         | Descripción  |
  	|-----------|---------------------------------|--------------|
  	| Ayuda      | `%%help`                            | Genera una tabla de todos los magics disponibles de ejemplo y una descripción |
  	| información      | `%%info`                          | Información de la sesión de resultados para el extremo Livio actual |
  	| configurar | `%%configure -f`<br>`{"executorMemory": "1000M"`,<br>`"executorCores": 4`} | Configura los parámetros para la creación de una sesión. El indicador de fuerza (-f) es obligatorio si ya se ha creado una sesión y se eliminan y volver a crear la sesión. Mire [/sessions de publicación de Livio cuerpo de la solicitud](https://github.com/cloudera/livy#request-body) para obtener una lista de parámetros válidos. Parámetros deben pasar como una cadena JSON y deben estar en la siguiente línea tras la magia, tal como se muestra en la columna de ejemplo. |
  	| SQL       |  `%%sql -o <variable name>`<br> `SHOW TABLES`    | Ejecuta una consulta de la sección con la sqlContext. Si el `-o` se pasan parámetros, el resultado de la consulta se conserva en el %% contexto Python local como una dataframe [Pandas](http://pandas.pydata.org/) .   |
  	| local     |     `%%local`<br>`a=1`              | Todo el código de las siguientes líneas se ejecutará localmente. Código debe ser código Python válido. |
  	| registros      | `%%logs`                        | Envía los registros de la sesión de Livio actual.  |
  	| eliminar    | `%%delete -f -s <session number>` | Elimina una sesión específica del extremo Livio actual. Tenga en cuenta que no se puede eliminar la sesión iniciada para el propio núcleo. |
  	| Liberador de espacio   | `%%cleanup -f`                    | Elimina todas las sesiones del extremo Livio actual, incluida la sesión de este bloc de notas. El indicador de fuerza -f es obligatorio.  |

    >[AZURE.NOTE] Además de magics agregados el núcleo PySpark, también puede usar el [magics de IPython integrados](https://ipython.org/ipython-doc/3/interactive/magics.html#cell-magics), incluidos `%%sh`. Puede usar el `%%sh` mágico ejecuten secuencias de comandos y el bloque de código en el clúster de headnode. 

3. **Visualización automática**. El núcleo **Pyspark** automáticamente visualiza los resultados de las consultas de la sección y SQL. Tiene la opción para elegir entre diferentes tipos de visualizaciones, incluida la tabla, circulares, líneas, área, barra.

## <a name="parameters-supported-with-the-sql-magic"></a>Parámetros compatibles con la %% mágico de sql

La %% sql mágico es compatible con distintos parámetros que puede usar para controlar el tipo de resultado que aparece al ejecutar consultas. La tabla siguiente muestra el resultado.

| Parámetro     | Ejemplo                         | Descripción  |
|-----------|---------------------------------|--------------|
| -o      | `-o <VARIABLE NAME>`                          | Utilice este parámetro para conservar el resultado de la consulta, en la %% contexto Python local, como un dataframe [Pandas](http://pandas.pydata.org/) . El nombre de la variable dataframe es el nombre de variable que especifique. |
| -q      | `-q`                          | Use esta opción para desactivar visualizaciones de la celda. Si no desea visualizar automáticamente el contenido de una celda y solo desea capturar como un dataframe, a continuación, usar `-q -o <VARIABLE>`. Si desea desactivar visualizaciones sin capturar los resultados (por ejemplo, para ejecutar una consulta SQL con efectos secundarios, como un `CREATE TABLE` instrucción), simplemente usar `-q` sin especificar un `-o` argumento. |
| -m       |  `-m <METHOD>`    | Donde **método** es **tomar** o **muestra** (el predeterminado es **tomar**). Si el método es **tomar**, el núcleo selecciona elementos de la parte superior del conjunto de datos de resultado especificado por MAXROWS (se describe más adelante en esta tabla). Si el método es **ejemplo**, el núcleo se aleatoriamente elementos de acuerdo con el conjunto de datos de ejemplo `-r` parámetro, se describe a continuación en esta tabla.   |
| -r     |     `-r <FRACTION>`            | Aquí **FRACCIÓN** es un número de punto flotante entre 0,0 y 1,0. Si es el método de ejemplo para la consulta SQL `sample`, a continuación, el núcleo ejemplos aleatoriamente la fracción especificada de los elementos del resultado por usted; Por ejemplo, si ejecuta una consulta SQL con los argumentos `-m sample -r 0.01`, a continuación, se tomarse aleatoriamente 1% de las filas de resultados. |
| -n      | `-n <MAXROWS>`                        | **MAXROWS** es un valor entero. El núcleo limita el número de filas de salida a **MAXROWS**. Si **MAXROWS** es un número negativo como **-1**, el número de filas del conjunto de resultados no estará limitado. |

**Ejemplo:**

    %%sql -q -m sample -r 0.1 -n 500 -o query2 
    SELECT * FROM hivesampletable

La instrucción anterior hace lo siguiente:

* Selecciona todos los registros de **hivesampletable**.
* Porque usamos - q, que desactiva la visualización automática.
* Dado que usamos `-m sample -r 0.1 -n 500` ejemplos 10% de las filas de la hivesampletable y limita el tamaño del resultado establecido en 500 filas aleatoriamente los.
* Por último, porque hemos usado `-o query2` también guarda los resultados en un dataframe denominado **consulta2**.
    

## <a name="considerations-while-using-the-new-kernels"></a>Consideraciones al usar los nuevos kernels

Independientemente núcleo usar (PySpark o motor), dejando consumen los blocs de notas que se ejecutan los recursos de clúster.  Con estas kernel porque los contextos están predefinidos, simplemente saliendo los blocs de notas no eliminar el contexto y por tanto la los recursos de clúster seguirá estando en uso. Una buena práctica con el kernel PySpark y motor sería usar la opción **Cerrar y detener** desde el menú de **archivo** del Bloc de notas. Esto elimina el contexto y, a continuación, salga del Bloc de notas.    


## <a name="show-me-some-examples"></a>Mostrar algunos ejemplos

Al abrir un bloc de notas Jupyter, verá dos carpetas disponibles en el nivel raíz.

* La carpeta de **PySpark** tiene blocs de notas de ejemplo que utilizan el nuevo kernel **Python** .
* La carpeta **Scala** tiene blocs de notas de ejemplo que usan el nuevo kernel de **motor** .

Puede abrir el Bloc de notas de **00 - [leer primera] motor mágico núcleo características** de la carpeta **PySpark** o **motor** para obtener información sobre la magics diferentes disponibles. También puede usar los otros muestra blocs de notas disponibles en las dos carpetas para obtener información sobre cómo conseguir escenarios diferentes con los blocs de notas Jupyter HDInsight Spark clústeres.

## <a name="where-are-the-notebooks-stored"></a>¿Dónde se almacenan los blocs de notas?

Jupyter blocs de notas se guardan en la cuenta de almacenamiento asociada con el clúster en la carpeta **/HdiNotebooks** .  Blocs de notas, archivos de texto y las carpetas que se crean desde dentro de Jupyter serán accesibles desde WASB.  Por ejemplo, si utiliza Jupyter para crear una carpeta **MiCarpeta** y un bloc de notas **myfolder/mynotebook.ipynb**, puede tener acceso al Bloc de notas `wasbs:///HdiNotebooks/myfolder/mynotebook.ipynb`.  Lo contrario también es cierto, es decir, si carga un bloc de notas directamente a su cuenta de almacenamiento en `/HdiNotebooks/mynotebook1.ipynb`, el Bloc de notas estarán visible desde Jupyter también.  Blocs de notas permanecerá en la cuenta de almacenamiento, incluso después de que el clúster se elimina.

La manera en que se guardan los blocs de notas en la cuenta de almacenamiento es compatible con HDFS. Por lo tanto, si se SSH en el clúster que puede usar del archivo de comandos de administración como la siguiente:

    hdfs dfs -ls /HdiNotebooks                            # List everything at the root directory – everything in this directory is visible to Jupyter from the home page
    hdfs dfs –copyToLocal /HdiNotebooks                 # Download the contents of the HdiNotebooks folder
    hdfs dfs –copyFromLocal example.ipynb /HdiNotebooks   # Upload a notebook example.ipynb to the root folder so it’s visible from Jupyter


En caso de que hay problemas de acceso a la cuenta de almacenamiento para el clúster, también se guardan los blocs de notas en la headnode `/var/lib/jupyter`.

## <a name="supported-browser"></a>Explorador compatible
Los blocs de notas Jupyter ejecutando contra HDInsight Spark clústeres sólo se admiten en Google Chrome.

## <a name="feedback"></a>Comentarios

Los nuevos kernels están en la evolución de la fase y se adultos a lo largo del tiempo. También puede significar que pueden cambiar las API como estos kernels adultos. Agradecemos los comentarios que tiene con el uso de estos kernels nuevos. Esto será muy útil en la versión final de estos kernels de forma. Puede dejar sus comentarios en la sección **comentarios** en la parte inferior de este artículo.


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

* [Usar paquetes externos con blocs de notas Jupyter](hdinsight-apache-spark-jupyter-notebook-use-external-packages.md)

* [Instalar Jupyter en su equipo y conectarse a un clúster de HDInsight Spark](hdinsight-apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Administrar recursos

* [Administrar recursos para el clúster de motor de Apache en HDInsight de Azure](hdinsight-apache-spark-resource-manager.md)

* [Realizar el seguimiento y depuración trabajos que se ejecutan en un clúster de motor de Apache en HDInsight](hdinsight-apache-spark-job-debugging.md)
