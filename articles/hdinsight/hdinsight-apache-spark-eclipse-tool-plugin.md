 <properties
    pageTitle="Crear aplicaciones de motor Scala con herramientas de HDInsight de Azure Kit de herramientas para Eclipse | Microsoft Azure"
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
    ms.date="08/30/2016"
    ms.author="nitinme"/>


# <a name="use-hdinsight-tools-in-azure-toolkit-for-eclipse-to-create-spark-applications-for-hdinsight-spark-linux-cluster"></a>Use herramientas de HDInsight Kit de herramientas de Azure para Eclipse para crear aplicaciones para clúster HDInsight motor Linux de motor

Este artículo proporciona instrucciones paso a paso sobre cómo desarrollar aplicaciones motor escritas en Scala y enviar a un HDInsight Spark de clúster con herramientas de HDInsight de Azure Kit de herramientas para Eclipse. Puede usar las herramientas de varias maneras diferentes:

* Desarrollar y enviar una solicitud de Scala Spark en un clúster de HDInsight Spark
* Para obtener acceso a los recursos de clúster de motor de HDInsight de Azure
* Desarrollar y ejecutar una aplicación de Scala Spark localmente

>[AZURE.IMPORTANT] Esta herramienta puede utilizarse para crear y enviar solicitudes para un clúster de HDInsight Spark en Linux.


##<a name="prerequisites"></a>Requisitos previos

* Una suscripción de Azure. Vea la [versión de prueba gratuita de Azure obtener](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

* Un clúster de motor de Apache en HDInsight Linux. Para obtener instrucciones, vea [crear Apache motor clústeres en HDInsight de Azure](hdinsight-apache-spark-jupyter-spark-sql.md).

* Kit de desarrollo de Java Oracle versión 7 y 8. 
    * **7 de SDK de Java** se utiliza para compilar proyectos de motor como los clústeres HDInsight compatibilidad con Java versión 7. Puede descargar Java SDK 7 desde [aquí](http://www.oracle.com/technetwork/java/javase/downloads/jdk7-downloads-1880260.html).
    * **Java SDK 8** se usa para el tiempo de ejecución de IDE Eclipse. Puede descargarla desde [aquí](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html).

* IDE Eclipse. En este artículo se usa neón Eclipse. Puede instalarlo desde [aquí](https://www.eclipse.org/downloads/).

* Scala IDE para Eclipse. 
    * **Si tiene IDE Eclipse instalado**, puede agregar el complemento Scala IDE, vaya a **Ayuda** -> **Instalar SoftWare nuevo**y agregar [http://download.scala-ide.org/sdk/lithium/e44/scala211/stable/site](http://download.scala-ide.org/sdk/lithium/e44/scala211/stable/site) como origen de para descargar Scala Plugin para Eclipse. 
    * **Si no tiene IDE Eclipse instalado**, puede instalar Scala IDE directamente desde [aquí](http://scala-ide.org/download/sdk.html). Puede descargar el archivo .zip desde este vínculo, extraerlo, vaya a la carpeta **/eclipse** y luego ejecute el archivo **eclipse.exe** desde allí.
    
    >[AZURE.NOTE] Los pasos de este documento se basan en usar IDE Eclipse con complemento Scala instalado.

* Motor SDK. Puede descargarla desde [aquí](http://go.microsoft.com/fwlink/?LinkID=723585&clcid=0x409).

* Instale e (fx) clipse de [https://www.eclipse.org/efxclipse/install.html](https://www.eclipse.org/efxclipse/install.html).


## <a name="install-hdinsight-tools-in-azure-toolkit-for-eclipse"></a>Instalar herramientas HDInsight de Azure Kit de herramientas para Eclipse

HDInsight de las herramientas para Eclipse está disponible como parte del Kit de herramientas de Azure para Eclipse. Para obtener instrucciones sobre cómo instalar el Kit de herramientas de Azure, vea [instalar el Kit de herramientas de Azure para Eclipse](../azure-toolkit-for-eclipse-installation.md).

## <a name="log-into-your-azure-subscription"></a>Inicie sesión en su suscripción de Azure

1. Inicie el IDE Eclipse y abra el Explorador de Azure. En el menú de la **ventana** del IDE, haga clic en **Mostrar vista** y, a continuación, haga clic en **otros**. Desde el cuadro de diálogo que se abre, expanda **Azure**, haga clic en **Explorador de Azure**y, a continuación, haga clic en **Aceptar**.

    ![Crear aplicación Scala motor](./media/hdinsight-apache-spark-eclipse-tool-plugin/view-explorer-1.png)

2. Haga clic en el nodo de **Azure** en el **Explorador de Azure**y, a continuación, haga clic en **Administrar suscripciones**.

3. En el cuadro de diálogo **Administrar suscripciones** , haga clic en **iniciar sesión** y escriba sus credenciales de Azure.

    ![Crear aplicación Scala motor](./media/hdinsight-apache-spark-eclipse-tool-plugin/view-explorer-2.png)

4. Después de que ha iniciado sesión, el cuadro de diálogo **Administrar suscripciones** muestra todas las suscripciones de Azure asociadas a las credenciales. Haga clic en **Cerrar** en el cuadro de diálogo.

5. En la ficha Explorador de Azure, expanda **HDInsight** para ver los clústeres HDInsight Spark en su suscripción.

    ![Crear aplicación Scala motor](./media/hdinsight-apache-spark-eclipse-tool-plugin/view-explorer-3.png)

6. Aún más, puede expandir un nodo de nombre de clúster para ver los recursos (por ejemplo, cuentas de almacenamiento) asociados con el clúster.

    ![Crear aplicación Scala motor](./media/hdinsight-apache-spark-eclipse-tool-plugin/view-explorer-4.png)

## <a name="set-up-a-spark-scala-project-for-an-hdinsight-spark-cluster"></a>Configurar un proyecto de motor Scala para un clúster de HDInsight Spark

1. Desde el espacio de trabajo IDE Eclipse, haga clic en **archivo**, haga clic en **nuevo**y, a continuación, haga clic en **proyecto**. 

2. En el Asistente para **Nuevo proyecto** , expanda **HDInsight**, seleccione el **motor en HDInsight (Scala)**y, a continuación, haga clic en **siguiente**.

    ![Crear aplicación Scala motor](./media/hdinsight-apache-spark-eclipse-tool-plugin/create-hdi-scala-app-2.png)

3. En el cuadro de diálogo **Nuevo proyecto de Scala HDInsight** , escribir o seleccionar valores tal como se muestra en la imagen siguiente y, a continuación, haga clic en **siguiente**.

    ![Crear aplicación Scala motor](./media/hdinsight-apache-spark-eclipse-tool-plugin/create-hdi-scala-app-3.png)

    * Escriba un nombre para el proyecto.
    * En el cuadro **JRE** , asegúrese de **que usar un entorno de ejecución JRE** se establece en **JavaSE 1.7**.
    * Asegúrese de que el motor SDK se establece en la ubicación donde haya descargado el SDK. El vínculo a la ubicación de descarga se incluye en los [requisitos previos](#prerequisites) más adelante en este tema. También puede descargar el SDK desde el vínculo incluido en este cuadro de diálogo, como se muestra en la imagen anterior.    

4. En el cuadro de diálogo siguiente, haga clic en la pestaña de **bibliotecas** y, a continuación, haga doble clic en **Biblioteca de sistema JRE [JavaSE 1.7]**.

    ![Crear aplicación Scala motor](./media/hdinsight-apache-spark-eclipse-tool-plugin/create-hdi-scala-app-4.png)

5. En el cuadro de diálogo **Editar biblioteca** , asegúrese de que **El entorno de ejecución** se establece en **JavaSE-1.7(jdk1.7.0_79)**. Si no está disponible como opción, siga los pasos siguientes.

    1. Seleccione la opción de **JRE alternativo** y vea si **JavaSE-1.7(jdk1.7.0_79)** está disponible.
    2. Si no es así, haga clic en el botón de **Versiones de JRE instalado** .

          ![Crear aplicación Scala motor](./media/hdinsight-apache-spark-eclipse-tool-plugin/create-hdi-scala-app-5.png)

    3. En el cuadro de diálogo **Versiones de JRE instalada** , haga clic en **Agregar**.

          ![Crear aplicación Scala motor](./media/hdinsight-apache-spark-eclipse-tool-plugin/create-hdi-scala-app-6.png)  

    4. En el cuadro de diálogo de **Tipo de JRE** , seleccione **VM estándar**y, a continuación, haga clic en **siguiente**

          ![Crear aplicación Scala motor](./media/hdinsight-apache-spark-eclipse-tool-plugin/create-hdi-scala-app-7.png)  

    5. En el cuadro de diálogo **Definición de JRE** , haga clic en el directorio, a continuación, desplácese hasta la ubicación de instalación de JDK 7 y seleccione la carpeta raíz de **jdk1.7.0_79**.

          ![Crear aplicación Scala motor](./media/hdinsight-apache-spark-eclipse-tool-plugin/create-hdi-scala-app-8.png)  

    6. Haga clic en **Finalizar**. En el cuadro de diálogo **Versiones de JRE instalado** , seleccione JRE recién agregado y, a continuación, haga clic en **Aceptar**.

           ![Create Spark Scala application](./media/hdinsight-apache-spark-eclipse-tool-plugin/create-hdi-scala-app-9.png)

    7. JRE recién agregado debería aparecer en **El entorno de ejecución**. Haga clic en **Finalizar**.

           ![Create Spark Scala application](./media/hdinsight-apache-spark-eclipse-tool-plugin/create-hdi-scala-app-10.png)

6. En la ficha de **bibliotecas** , haga doble clic en el **Contenedor de biblioteca Scala [2.11.8]**. En el cuadro de diálogo **Editar biblioteca** , seleccione **fijo de la biblioteca Scala contenedor: 2.10.6**. 

    ![Crear aplicación Scala motor](./media/hdinsight-apache-spark-eclipse-tool-plugin/create-hdi-scala-app-11.png)

    Haga clic en **Finalizar** hasta que salga del cuadro de diálogo Configuración.

## <a name="create-a-scala-application-for-hdinsight-spark-cluster"></a>Crear una aplicación de Scala para clúster HDInsight Spark

1. En el IDE Eclipse ya está abierto, en el **Explorador de paquetes**, expanda el proyecto que ha creado anteriormente, contextual **src**, apunte a **nuevo**y, a continuación, haga clic en **otros**.

2. En el cuadro de diálogo **Seleccionar a un asistente** , expanda **Scala asistentes**, haga clic en **Objeto Scala**y, a continuación, haga clic en **siguiente**.

    ![Crear aplicación Scala motor](./media/hdinsight-apache-spark-eclipse-tool-plugin/create-scala-proj-1.png)

3. En el cuadro de diálogo **Crear nuevo archivo** , escriba un nombre para el objeto y, a continuación, haga clic en **Finalizar**.

    ![Crear aplicación Scala motor](./media/hdinsight-apache-spark-eclipse-tool-plugin/create-scala-proj-2.png)

4. Pegue el código siguiente en el editor de texto.

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

    1. Desde el **Explorador de paquetes**, haga clic en el nombre del proyecto y, a continuación, seleccione **Enviar motor aplicación a HDInsight**.      

    2. En el cuadro de diálogo de **Envío de motor** proporcionarán los valores siguientes.

        * **Nombre de clúster**, seleccione el clúster de HDInsight Spark en el que desea ejecutar la aplicación.

        * Necesita seleccionar un efecto del proyecto Eclipse, o seleccione una opción de disco duro.

        * En el cuadro de texto **nombre de la clase principal** , escriba el nombre del objeto especificado en el código (consulte la imagen siguiente).

            ![Crear aplicación Scala motor](./media/hdinsight-apache-spark-eclipse-tool-plugin/create-scala-proj-3.png)

        * Porque el código de la aplicación en este ejemplo no requiere ningún argumento de línea de comandos o referencia JARs o archivos, puede dejar que los demás cuadros de texto vacío.

        * Haga clic en **Enviar**.

    3. La ficha **Presentación de motor** debería empezar a mostrar el progreso. Puede detener la aplicación haciendo clic en el botón rojo en la ventana de "Envío de motor". También puede ver los registros de esta aplicación específica ejecutar haciendo clic en el icono del globo terráqueo (indicado por el cuadro azul en la imagen).

        ![Crear aplicación Scala motor](./media/hdinsight-apache-spark-eclipse-tool-plugin/create-scala-proj-4.png)

    En la siguiente sección, obtenga información sobre cómo obtener acceso a la tarea con las herramientas de HDInsight de Azure Kit de herramientas para Eclipse de salida.


## <a name="access-and-manage-hdinsight-spark-clusters-using-the-hdinsight-tools-in-azure-toolkit-for-eclipse"></a>Acceder y administrar clústeres HDInsight Spark con las herramientas de HDInsight de Azure Kit de herramientas para Eclipse

Puede realizar una amplia variedad de operaciones con las herramientas de HDInsight.

### <a name="access-the-storage-container-for-the-cluster"></a>Obtener acceso al contenedor de almacenamiento para el clúster

1. Desde el Explorador de Azure, expanda el nodo de raíz de **HDInsight** para ver una lista de clústeres HDInsight Spark que están disponibles.

3. Expanda el nombre del clúster para ver la cuenta de almacenamiento y el contenedor de almacenamiento predeterminada para el clúster.

    ![Crear aplicación Scala motor](./media/hdinsight-apache-spark-eclipse-tool-plugin/view-explorer-5.png)

4. Haga clic en el nombre del contenedor de almacenamiento asociado con el clúster. En el panel derecho, verá una carpeta denominada **HVACOut**. Haga doble clic para abrir la carpeta y se verá **parte -*** archivos. Abra uno de los archivos para ver el resultado de la aplicación.

### <a name="access-the-spark-history-server"></a>Obtener acceso al servidor de historial de motor

1. Desde el **Explorador de Azure**, haga clic en el nombre de clúster motor y, a continuación, seleccione **Abrir UI de historial de motor**. Cuando se le pida, escriba las credenciales de administrador para el clúster. Debe haber especificado estos elementos mientras el clúster de aprovisionamiento.

2. En el panel de motor historial Server, puede buscar la aplicación simplemente terminado ejecutando mediante el nombre de la aplicación. En el código anterior, se establece el nombre de aplicación mediante `val conf = new SparkConf().setAppName("MyClusterApp")`. Por lo tanto, el nombre de la aplicación de motor era **MyClusterApp**.

### <a name="launch-the-ambari-portal"></a>Inicio del portal Ambari

Desde el **Explorador de Azure**, haga clic en el nombre de clúster motor y, a continuación, seleccione **Abrir Portal de administración de clúster (Ambari)**. Cuando se le pida, escriba las credenciales de administrador para el clúster. Debe haber especificado estos elementos mientras el clúster de aprovisionamiento.

### <a name="manage-azure-subscriptions"></a>Administrar suscripciones de Azure

De forma predeterminada, las herramientas de HDInsight de Azure Kit de herramientas para Eclipse enumera los clústeres motor de todas las suscripciones de Azure. Si es necesario, puede especificar las suscripciones para la que desea tener acceso al clúster. Desde el **Explorador de Azure**, haga clic en el nodo de raíz de **Azure** y, a continuación, haga clic en **Administrar suscripciones**. En el cuadro de diálogo, desactive las casillas de verificación contra la suscripción que no desea tener acceso a y, a continuación, haga clic en **Cerrar**. También puede hacer clic en **Cerrar sesión** si desea cerrar la sesión de la suscripción de Azure.


## <a name="run-a-spark-scala-application-locally"></a>Ejecutar una aplicación de motor Scala localmente

Puede usar las herramientas de HDInsight en Kit de herramientas de Azure para Eclipse para ejecutar aplicaciones de motor Scala localmente en su estación de trabajo. Normalmente, estas aplicaciones no necesitan tener acceso a los recursos de clúster como contenedor de almacenamiento y puede ejecutar y probar localmente.

### <a name="prerequisite"></a>Requisito previo

Mientras se ejecuta la aplicación de motor Scala local en un equipo con Windows, puede que aparezca una excepción como se explica en [2356 motor](https://issues.apache.org/jira/browse/SPARK-2356) que se produce debido a una falta **WinUtils.exe** en el sistema operativo Windows. Para evitar este error, debe [descargar el archivo ejecutable desde aquí](http://public-repo-1.hortonworks.com/hdp-win-alpha/winutils.exe) a una ubicación como **C:\WinUtils\bin**. A continuación, debe agregar una variable de entorno **HADOOP_HOME** y establezca el valor de la variable en **C\WinUtils**.

### <a name="run-a-local-spark-scala-application"></a>Ejecutar una aplicación de motor Scala local  

1. Inicie Eclipse y cree un nuevo proyecto. En el cuadro de diálogo nuevo proyecto, efectúe las acciones siguientes y, a continuación, haga clic en **siguiente**.

    ![Crear aplicación Scala motor](./media/hdinsight-apache-spark-eclipse-tool-plugin/hdi-spark-app-local-run.png)

    * En el panel izquierdo, seleccione **HDInsight**.
    * En el panel derecho, seleccione **motor de muestra de ejecución de HDInsight Local (Scala)**.
    * Haga clic en **siguiente**.

2. Para proporcionar los detalles del proyecto, siga los pasos 3 a 6 tal como se muestra en la sección anterior, [configurar un proyecto de aplicación de motor Scala para un clúster de motor de HDInsight](#set-up-a-spark-scala-application-project-for-an-hdinsight-spark cluster).

3. La plantilla agrega un código de ejemplo (**LogQuery**) en la carpeta de **src** puede ejecutar localmente en el equipo.

    ![Resultado de ejecución local de aplicación de motor](./media/hdinsight-apache-spark-eclipse-tool-plugin/local-app.png)

4.  Haga clic con el botón secundario en la aplicación **LogQuery** , seleccione **Ejecutar como**y, a continuación, haga clic en **aplicación de Scala 1**. Verá un resultado similar a esta en la pestaña de la **consola** en la parte inferior.

    ![Resultado de ejecución local de aplicación de motor](./media/hdinsight-apache-spark-eclipse-tool-plugin/hdi-spark-app-local-run-result.png)

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

* [Use herramientas de HDInsight de Azure Kit de herramientas de IntelliJ para crear y enviar el motor Scala aplicaciones](hdinsight-apache-spark-intellij-tool-plugin.md)

* [Usar herramientas de HDInsight de Azure Kit de herramientas para IntelliJ depurar despierten aplicaciones de forma remota](hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)

* [Utilizar blocs de notas de globo dirigible con un clúster de motor en HDInsight](hdinsight-apache-spark-use-zeppelin-notebook.md)

* [Kernels disponibles para el Bloc de notas de Jupyter en clúster de motor de HDInsight](hdinsight-apache-spark-jupyter-notebook-kernels.md)

* [Usar paquetes externos con blocs de notas Jupyter](hdinsight-apache-spark-jupyter-notebook-use-external-packages.md)

* [Instalar Jupyter en su equipo y conectarse a un clúster de HDInsight Spark](hdinsight-apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Administrar recursos

* [Administrar recursos para el clúster de motor de Apache en HDInsight de Azure](hdinsight-apache-spark-resource-manager.md)

* [Realizar el seguimiento y depuración trabajos que se ejecutan en un clúster de motor de Apache en HDInsight](hdinsight-apache-spark-job-debugging.md)
