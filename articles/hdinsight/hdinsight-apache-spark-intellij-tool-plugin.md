 <properties
    pageTitle="Crear aplicaciones de motor Scala con herramientas de HDInsight de Azure Kit de herramientas para IntelliJ | Microsoft Azure"
    description="Obtenga información sobre cómo crear una aplicación de motor se ejecute en HDInsight Spark clústeres independiente."
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
    ms.date="09/09/2016"
    ms.author="nitinme"/>


# <a name="use-hdinsight-tools-in-azure-toolkit-for-intellij-to-create-spark-applications-for-hdinsight-spark-linux-cluster"></a>Use herramientas de HDInsight de Azure Kit de herramientas para IntelliJ crear motor aplicaciones para clúster HDInsight motor Linux

Este artículo proporciona instrucciones paso a paso sobre cómo desarrollar aplicaciones motor escritas en Scala y enviar a un HDInsight Spark de clúster con herramientas de HDInsight de Azure Kit de herramientas para IntelliJ.  Puede usar las herramientas de varias maneras diferentes:

* Desarrollar y enviar una solicitud de Scala Spark en un clúster de HDInsight Spark
* Para obtener acceso a los recursos de clúster de motor de HDInsight de Azure
* Desarrollar y ejecutar una aplicación de Scala Spark localmente

También puede seguir un vídeo [aquí](https://mix.office.com/watch/1nqkqjt5xonza) para ayudarle a comenzar.

>[AZURE.IMPORTANT] Esta herramienta puede utilizarse para crear y enviar solicitudes para un clúster de HDInsight Spark en Linux.


##<a name="prerequisites"></a>Requisitos previos

* Una suscripción de Azure. Vea la [versión de prueba gratuita de Azure obtener](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

* Un clúster de motor de Apache en HDInsight Linux. Para obtener instrucciones, vea [crear Apache motor clústeres en HDInsight de Azure](hdinsight-apache-spark-jupyter-spark-sql.md).

* Kit de desarrollo de Java de Oracle. Puede instalarlo desde [aquí](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html).

* IntelliJ IDEA. En este artículo se utiliza la versión 15.0.1. Puede instalarlo desde [aquí](https://www.jetbrains.com/idea/download/).

## <a name="install-hdinsight-tools-in-azure-toolkit-for-intellij"></a>Instalar herramientas de HDInsight de Azure Kit de herramientas para IntelliJ

HDInsight de las herramientas para IntelliJ está disponible como parte del Kit de herramientas de Azure para IntelliJ. Para obtener instrucciones sobre cómo instalar el Kit de herramientas de Azure, vea [instalar el Kit de herramientas de Azure para IntelliJ](../azure-toolkit-for-intellij-installation.md).

## <a name="log-into-your-azure-subscription"></a>Inicie sesión en su suscripción de Azure

1. Inicie el IDE IntelliJ y abra el Explorador de Azure. En el menú **Ver** , en el IDE, haga clic en **Herramienta de Windows** y, a continuación, haga clic en **Explorador de Azure**.

    ![Crear aplicación Scala motor](./media/hdinsight-apache-spark-intellij-tool-plugin/show-azure-explorer.png)

2. Haga clic en el nodo de **Azure** en el **Explorador de Azure**y, a continuación, haga clic en **Administrar suscripciones**.

3. En el cuadro de diálogo **Administrar suscripciones** , haga clic en **iniciar sesión** y escriba sus credenciales de Azure.

    ![Crear aplicación Scala motor](./media/hdinsight-apache-spark-intellij-tool-plugin/view-explorer-2.png)

4. Después de que ha iniciado sesión, el cuadro de diálogo **Administrar suscripciones** muestra todas las suscripciones de Azure asociadas a las credenciales. Haga clic en **Cerrar** en el cuadro de diálogo.

5. En la ficha **Explorador de Azure** , expanda **HDInsight** para ver los clústeres HDInsight Spark en su suscripción.

    ![Crear aplicación Scala motor](./media/hdinsight-apache-spark-intellij-tool-plugin/view-explorer-3.png)

6. Aún más, puede expandir un nodo de nombre de clúster para ver los recursos (por ejemplo, cuentas de almacenamiento) asociados con el clúster.

    ![Crear aplicación Scala motor](./media/hdinsight-apache-spark-intellij-tool-plugin/view-explorer-4.png)

## <a name="run-a-spark-scala-application-on-an-hdinsight-spark-cluster"></a>Ejecutar una aplicación de motor Scala en un clúster de HDInsight Spark

1. Inicie IntelliJ IDEA y cree un nuevo proyecto. En el cuadro de diálogo nuevo proyecto, efectúe las acciones siguientes y, a continuación, haga clic en **siguiente**.

    ![Crear aplicación Scala motor](./media/hdinsight-apache-spark-intellij-tool-plugin/create-hdi-scala-app.png)

    * En el panel izquierdo, seleccione **HDInsight**.
    * En el panel derecho, seleccione **motor en HDInsight (Scala)**.
    * Haga clic en **siguiente**.

2. En la ventana siguiente, proporcione los detalles del proyecto.

    * Proporcione un nombre de proyecto y la ubicación del proyecto.
    * Para el **SDK de Project**, asegúrese de que proporcione una versión de Java mayor que 7.
    * Para **Scala SDK**, haga clic en **crear**, haga clic en **Descargar**y, a continuación, seleccione la versión de Scala usar. **Asegúrese de que no desea usar la versión 2.11.x**. Este ejemplo usa la versión **2.10.6**.

        ![Crear aplicación Scala motor](./media/hdinsight-apache-spark-intellij-tool-plugin/hdi-scala-version.png)

    * Para **Motor SDK**, descargar y usar el SDK desde [aquí](http://go.microsoft.com/fwlink/?LinkID=723585&clcid=0x409). También puede omitir esta y utilizar el [repositorio de motor experto](http://mvnrepository.com/search?q=spark) en su lugar, sin embargo, asegúrese de que tiene el repositorio de experto en derecha instalado para desarrollar aplicaciones motor. (Por ejemplo, debe asegurarse de que tiene el elemento de transmisión de motor instalado si está utilizando la transmisión motor; También, asegúrese de que está utilizando el repositorio marcado como Scala 2.10: no utilice el repositorio marcado como Scala 2.11.)

        ![Crear aplicación Scala motor](./media/hdinsight-apache-spark-intellij-tool-plugin/hdi-scala-project-details.png)

    * Haga clic en **Finalizar**.

3. El proyecto de motor creará automáticamente un efecto. Para ver la muestra, siga estos pasos.

    1. En el menú **archivo** , haga clic en **Estructura del proyecto**.
    2. En el cuadro de diálogo de **Estructura del proyecto** , haga clic en **artefactos** para ver la muestra de forma predeterminada que se crea.

        ![Crear JAR](./media/hdinsight-apache-spark-intellij-tool-plugin/default-artifact.png)

    También puede crear su propio muestra bly al hacer clic en el **+** icono, resaltado en la imagen anterior.

4. En el cuadro de diálogo de **Estructura del proyecto** , haga clic en **proyecto**. Si se establece el **SDK de Project** a 1,8, asegúrese de que el **nivel de lenguaje de proyecto** se establece en **7 - rombos, etcetera de múltiples captura, ARM,**.

    ![Establecer el nivel de lenguaje de proyecto](./media/hdinsight-apache-spark-intellij-tool-plugin/set-project-language-level.png)

5. Agregar el código fuente de la aplicación.

    1. Desde el **Explorador de proyectos**, contextual **src**, apunte a **nuevo**y, a continuación, haga clic en **clase Scala**.

        ![Agregar código fuente](./media/hdinsight-apache-spark-intellij-tool-plugin/hdi-spark-scala-code.png)

    2. En el cuadro de diálogo **Crear nueva clase Scala** , proporcione un nombre para seleccionar **tipo** de **objeto**y, a continuación, haga clic en **Aceptar**.

        ![Agregar código fuente](./media/hdinsight-apache-spark-intellij-tool-plugin/hdi-spark-scala-code-object.png)

    3. En el archivo **MyClusterApp.scala** , pegue el código siguiente. Este código lee los datos de la HVAC.csv (disponible en todos los clústeres HDInsight Spark), recupera las filas que solo tienen un dígito de la columna séptimo en la CSV y escribe el resultado en **/HVACOut** en el contenedor de almacenamiento predeterminada para el clúster.


            import org.apache.spark.SparkConf
            import org.apache.spark.SparkContext

            object MyClusterApp{
              def main (arg: Array[String]): Unit = {
                val conf = new SparkConf().setAppName("MyClusterApp")
                val sc = new SparkContext(conf)

                val rdd = sc.textFile("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")

                //find the rows which have only one digit in the 7th column in the CSV
                val rdd1 =  rdd.filter(s => s.split(",")(6).length() == 1)

                rdd1.saveAsTextFile("wasbs:///HVACOut")
              }

            }

5. Ejecute la aplicación en un clúster de HDInsight Spark.

    1. Desde el **Explorador de proyectos**, haga clic en el nombre del proyecto y, a continuación, seleccione **Enviar motor aplicación a HDInsight**.

        ![Enviar solicitud de motor](./media/hdinsight-apache-spark-intellij-tool-plugin/hdi-submit-spark-app-1.png)

    2. Se le pedirá que introduzca sus credenciales de suscripción de Azure. En el cuadro de diálogo de **Envío de motor** proporcionarán los valores siguientes.

        * Para **clústeres motor (sólo Linux)**, seleccione el clúster de HDInsight Spark en el que desea ejecutar la aplicación.

        * Se necesita seleccione muestra del proyecto IntelliJ, o seleccione una opción de disco duro.

        * En el cuadro de texto **nombre de la clase principal** , haga clic en los puntos suspensivos (![puntos suspensivos](./media/hdinsight-apache-spark-intellij-tool-plugin/ellipsis.png) ), seleccione la clase principal en el código fuente de la aplicación y, a continuación, haga clic en **Aceptar**.

            ![Enviar solicitud de motor](./media/hdinsight-apache-spark-intellij-tool-plugin/hdi-submit-spark-app-3.png)

        * Porque el código de la aplicación en este ejemplo no requiere ningún argumento de línea de comandos o referencia JARs o archivos, puede dejar que los demás cuadros de texto vacío.

        * Después de proporcionar todas las entradas, el cuadro de diálogo debería ser similar al siguiente.

            ![Enviar solicitud de motor](./media/hdinsight-apache-spark-intellij-tool-plugin/hdi-submit-spark-app-2.png)

        * Haga clic en **Enviar**.

    3. Debe empezar la ficha **Presentación de motor** en la parte inferior de la ventana que muestra el progreso. También puede dejar la aplicación haciendo clic en el botón rojo en la ventana de "Envío de motor".

        ![Resultado de la aplicación de motor](./media/hdinsight-apache-spark-intellij-tool-plugin/hdi-spark-app-result.png)

    En la siguiente sección, obtenga información sobre cómo obtener acceso a la tarea con las herramientas de HDInsight de Azure Kit de herramientas para IntelliJ de salida.


## <a name="access-and-manage-hdinsight-spark-clusters-using-the-hdinsight-tools-in-azure-toolkit-for-intellij"></a>Acceder y administrar clústeres HDInsight Spark con las herramientas de HDInsight de Azure Kit de herramientas para IntelliJ

Puede realizar una amplia variedad de operaciones con las herramientas de HDInsight que forman parte del Kit de herramientas de Azure para IntelliJ.

### <a name="access-the-job-view-directly-from-the-hdinsight-tools"></a>Obtener acceso a la vista de trabajo directamente desde las herramientas de HDInsight

1. Desde el **Explorador de Azure**, expanda **HDInsight**, expanda el nombre de clúster motor y, a continuación, haga clic en **tareas**.

2. En el panel derecho, la ficha de **Vista de trabajo de motor** muestra todas las aplicaciones que se ejecutaran en el clúster. Haga clic en el nombre de la aplicación que desea ver más detalles.

    ![Vista de trabajo de Access](./media/hdinsight-apache-spark-intellij-tool-plugin/view-job-logs.png)

3. Los cuadros para el **Mensaje de Error**, **Salida de trabajo**, **Registros de trabajo Livio**y **Motor los registros del controlador** se rellenan en función de la aplicación que seleccione.

4. También puede abrir la **Interfaz de usuario de motor historial** y la **Interfaz de usuario del hilo** (en el nivel de aplicación) haciendo clic en los botones correspondientes en la parte superior de la pantalla.

### <a name="access-the-spark-history-server"></a>Obtener acceso al servidor de historial de motor

1. Desde el **Explorador de Azure**, expanda **HDInsight**, haga clic en el nombre de clúster motor y, a continuación, seleccione **Abrir UI de historial de motor**. Cuando se le pida, escriba las credenciales de administrador para el clúster. Debe haber especificado estos elementos mientras el clúster de aprovisionamiento.

2. En el panel de motor historial Server, puede buscar la aplicación simplemente terminado ejecutando mediante el nombre de la aplicación. En el código anterior, se establece el nombre de aplicación mediante `val conf = new SparkConf().setAppName("MyClusterApp")`. Por lo tanto, el nombre de la aplicación de motor era **MyClusterApp**.

### <a name="launch-the-ambari-portal"></a>Inicio del portal Ambari

Desde el **Explorador de Azure**, expanda **HDInsight**, haga clic en el nombre de clúster motor y, a continuación, seleccione **Abrir Portal de administración de clúster (Ambari)**. Cuando se le pida, escriba las credenciales de administrador para el clúster. Debe haber especificado estos elementos mientras el clúster de aprovisionamiento.

### <a name="manage-azure-subscriptions"></a>Administrar suscripciones de Azure

De forma predeterminada, las herramientas HDInsight enumera los clústeres motor de todas las suscripciones de Azure. Si es necesario, puede especificar las suscripciones para la que desea tener acceso al clúster. Desde el **Explorador de Azure**, haga clic en el nodo de raíz de **Azure** y, a continuación, haga clic en **Administrar suscripciones**. En el cuadro de diálogo, desactive las casillas de verificación contra la suscripción que no desea tener acceso a y, a continuación, haga clic en **Cerrar**. También puede hacer clic en **Cerrar sesión** si desea cerrar la sesión de la suscripción de Azure.


## <a name="run-a-spark-scala-application-locally"></a>Ejecutar una aplicación de motor Scala localmente

Puede usar las herramientas de HDInsight en el Kit de herramientas de Azure para IntelliJ para ejecutar aplicaciones de motor Scala localmente en su estación de trabajo. Normalmente, estas aplicaciones no necesitan tener acceso a los recursos de clúster como contenedor de almacenamiento y puede ejecutar y probar localmente.

### <a name="prerequisite"></a>Requisito previo

Mientras se ejecuta la aplicación de motor Scala local en un equipo con Windows, puede que aparezca una excepción como se explica en [2356 motor](https://issues.apache.org/jira/browse/SPARK-2356) que se produce debido a una falta WinUtils.exe en Windows. Para evitar este error, debe [descargar el archivo ejecutable desde aquí](http://public-repo-1.hortonworks.com/hdp-win-alpha/winutils.exe) a una ubicación como **C:\WinUtils\bin**. A continuación, debe agregar una variable de entorno **HADOOP_HOME** y establezca el valor de la variable en **C\WinUtils**.

### <a name="run-a-local-spark-scala-application"></a>Ejecutar una aplicación de motor Scala local  

1. Inicie IntelliJ IDEA y cree un nuevo proyecto. En el cuadro de diálogo nuevo proyecto, efectúe las acciones siguientes y, a continuación, haga clic en **siguiente**.

    ![Crear aplicación Scala motor](./media/hdinsight-apache-spark-intellij-tool-plugin/hdi-spark-app-local-run.png)

    * En el panel izquierdo, seleccione **HDInsight**.
    * En el panel derecho, seleccione **motor de muestra de ejecución de HDInsight Local (Scala)**.
    * Haga clic en **siguiente**.

2. En la ventana siguiente, proporcione los detalles del proyecto.

    * Proporcione un nombre de proyecto y la ubicación del proyecto.
    * Para el **SDK de Project**, asegúrese de que proporcione una versión de Java mayor que 7.
    * Para **Scala SDK**, haga clic en **crear**, haga clic en **Descargar**y, a continuación, seleccione la versión de Scala usar. **Asegúrese de que no desea usar la versión 2.11.x**. Este ejemplo usa la versión **2.10.6**.

        ![Crear aplicación Scala motor](./media/hdinsight-apache-spark-intellij-tool-plugin/hdi-scala-version.png)

    * Para **Motor SDK**, descargar y usar el SDK desde [aquí](http://go.microsoft.com/fwlink/?LinkID=723585&clcid=0x409). También puede omitir esta y utilizar el [repositorio de motor experto](http://mvnrepository.com/search?q=spark) en su lugar, sin embargo, asegúrese de que tiene el repositorio de experto en derecha instalado para desarrollar aplicaciones motor. (Por ejemplo, debe asegurarse de que tiene el elemento de transmisión de motor instalado si está utilizando la transmisión motor; También, asegúrese de que está utilizando el repositorio marcado como Scala 2.10: no utilice el repositorio marcado como Scala 2.11.)

        ![Crear aplicación Scala motor](./media/hdinsight-apache-spark-intellij-tool-plugin/hdi-spark-app-local-create-project.png)

    * Haga clic en **Finalizar**.

3. La plantilla agrega un código de ejemplo (**LogQuery**) en la carpeta de **src** puede ejecutar localmente en el equipo.

    ![Aplicación Scala local](./media/hdinsight-apache-spark-intellij-tool-plugin/local-app.png)

4.  Haga clic con el botón secundario en la aplicación **LogQuery** y, a continuación, haga clic en **"Ejecutar 'LogQuery'"**. Verá un resultado similar a esta en la pestaña **Ejecutar** en la parte inferior.

    ![Resultado de ejecución local de aplicación de motor](./media/hdinsight-apache-spark-intellij-tool-plugin/hdi-spark-app-local-run-result.png)

## <a name="convert-existing-intellij-idea-applications-to-use-the-hdinsight-tools-in-azure-toolkit-for-intellij"></a>Convertir aplicaciones de IDEA IntelliJ existentes para usar las herramientas de HDInsight de Azure Kit de herramientas para IntelliJ

También puede convertir las aplicaciones de motor Scala existentes creadas en IntelliJ IDEA para que sea compatible con las herramientas de HDInsight de Azure Kit de herramientas para IntelliJ. Esto le permitirá utilizar la herramienta para enviar las aplicaciones a un clúster HDInsight Spark. Puede hacerlo siguiendo los pasos siguientes:

1. Para una aplicación de motor Scala existente creado con la IDEA de IntelliJ, abra el archivo de .iml asociado.
2. En el nivel raíz, verá un elemento **módulo** similar a esta:

        <module org.jetbrains.idea.maven.project.MavenProjectsManager.isMavenModule="true" type="JAVA_MODULE" version="4">

3. Editar el elemento para agregar `UniqueKey="HDInsightTool"` para que el elemento **módulo** el siguiente aspecto:

        <module org.jetbrains.idea.maven.project.MavenProjectsManager.isMavenModule="true" type="JAVA_MODULE" version="4" UniqueKey="HDInsightTool">

4. Guardar los cambios. La aplicación ahora debería ser compatible con las herramientas de HDInsight de Azure Kit de herramientas para IntelliJ. Para comprobarlo, haciendo doble clic en el nombre del proyecto en el Explorador de proyectos. El menú emergente ahora debería tener la opción de **Enviar la aplicación de motor a HDInsight**.


## <a name="troubleshooting"></a>Solución de problemas

### <a name="please-use-a-larger-heap-size-error-in-local-run"></a>"Utilice un tamaño de montón" error en ejecución local

En el motor 1,6, si está utilizando una versión de 32 bits Java SDK durante la ejecución local, puede encontrar los siguientes errores:

    Exception in thread "main" java.lang.IllegalArgumentException: System memory 259522560 must be at least 4.718592E8. Please use a larger heap size.
        at org.apache.spark.memory.UnifiedMemoryManager$.getMaxMemory(UnifiedMemoryManager.scala:193)
        at org.apache.spark.memory.UnifiedMemoryManager$.apply(UnifiedMemoryManager.scala:175)
        at org.apache.spark.SparkEnv$.create(SparkEnv.scala:354)
        at org.apache.spark.SparkEnv$.createDriverEnv(SparkEnv.scala:193)
        at org.apache.spark.SparkContext.createSparkEnv(SparkContext.scala:288)
        at org.apache.spark.SparkContext.<init>(SparkContext.scala:457)
        at LogQuery$.main(LogQuery.scala:53)
        at LogQuery.main(LogQuery.scala)
        at sun.reflect.NativeMethodAccessorImpl.invoke0(Native Method)
        at sun.reflect.NativeMethodAccessorImpl.invoke(NativeMethodAccessorImpl.java:57)
        at sun.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:43)
        at java.lang.reflect.Method.invoke(Method.java:606)
        at com.intellij.rt.execution.application.AppMain.main(AppMain.java:144)

Esto es porque el tamaño del montón no es lo suficientemente grande como para motor ejecutar, ya que el motor requiere al menos 471MB (puede obtener más detalles de [motor 12081](https://issues.apache.org/jira/browse/SPARK-12081) si lo desea). Una solución sencilla es usar un SDK Java de 64 bits. También puede cambiar la configuración de JVM en IntelliJ mediante la adición de las siguientes opciones:

    -Xms128m -Xmx512m -XX:MaxPermSize=300m -ea

![Resultado de ejecución local de aplicación de motor](./media/hdinsight-apache-spark-intellij-tool-plugin/change-heap-size.png)

## <a name="feedback--known-issues"></a>Problemas conocidos y de comentarios

Actualmente motor envía directamente no es compatible y estamos trabajando en ese.

Si tiene sugerencias o comentarios, o si tienes problemas al usar esta herramienta, no dude en colocar un correo electrónico en hdivstool en microsoft punto com.

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

* [Usar herramientas de HDInsight de Azure Kit de herramientas para IntelliJ depurar despierten aplicaciones de forma remota](hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)

* [Use herramientas de HDInsight de Azure Kit de herramientas para Eclipse para crear aplicaciones de motor](hdinsight-apache-spark-eclipse-tool-plugin.md)

* [Utilizar blocs de notas de globo dirigible con un clúster de motor en HDInsight](hdinsight-apache-spark-use-zeppelin-notebook.md)

* [Kernels disponibles para el Bloc de notas de Jupyter en clúster de motor de HDInsight](hdinsight-apache-spark-jupyter-notebook-kernels.md)

* [Usar paquetes externos con blocs de notas Jupyter](hdinsight-apache-spark-jupyter-notebook-use-external-packages.md)

* [Instalar Jupyter en su equipo y conectarse a un clúster de HDInsight Spark](hdinsight-apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Administrar recursos

* [Administrar recursos para el clúster de motor de Apache en HDInsight de Azure](hdinsight-apache-spark-resource-manager.md)

* [Realizar el seguimiento y depuración trabajos que se ejecutan en un clúster de motor de Apache en HDInsight](hdinsight-apache-spark-job-debugging.md)
