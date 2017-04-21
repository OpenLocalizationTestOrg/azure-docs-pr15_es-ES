<properties
    pageTitle="Crear independiente scala aplicaciones que se ejecutan en clústeres HDInsight Spark | Microsoft Azure"
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
    ms.date="10/28/2016"
    ms.author="nitinme"/>


# <a name="create-a-standalone-scala-application-to-run-on-apache-spark-cluster-on-hdinsight-linux"></a>Crear una aplicación Scala para ejecutar en clúster de motor de Apache en HDInsight Linux independiente

Este artículo proporciona instrucciones paso a paso sobre cómo desarrollar aplicaciones de motor independiente escritas en Scala con Maven IntelliJ IDEA. El artículo usa a experto en Apache como el sistema de compilación y se inicia con Arquetipo experto existente Scala proporcionado por IntelliJ IDEA.  De alto nivel, crear una Scala aplicación en IntelliJ IDEA incluirá los siguientes pasos:


* Use Maven como el sistema de compilación.
* Actualizar archivo de modelo de objeto de proyecto (POM) para resolver las dependencias de módulo de motor.
* Crear una aplicación en Scala.
* Generar un archivo jar que se puede enviar a HDInsight Spark clústeres.
* Ejecute la aplicación en un clúster de motor mediante Livio.

>[AZURE.NOTE] HDInsight también proporciona una herramienta de complemento de IDEA IntelliJ para facilitar el proceso de crear y enviar aplicaciones a un clúster de HDInsight Spark en Linux. Para obtener más información, vea [Usar complemento de herramientas HDInsight para IntelliJ IDEA crear y enviar aplicaciones de motor](hdinsight-apache-spark-intellij-tool-plugin.md).


**Requisitos previos**

* Una suscripción de Azure. Vea la [versión de prueba gratuita de Azure obtener](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* Un clúster de motor de Apache en HDInsight Linux. Para obtener instrucciones, vea [crear Apache motor clústeres en HDInsight de Azure](hdinsight-apache-spark-jupyter-spark-sql.md).
* Kit de desarrollo de Java de Oracle. Puede instalarlo desde [aquí](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html).
* Un Java IDE. En este artículo, se utiliza IntelliJ IDEA 15.0.1. Puede instalarlo desde [aquí](https://www.jetbrains.com/idea/download/).


## <a name="install-scala-plugin-for-intellij-idea"></a>Instalar el complemento de Scala para IntelliJ IDEA

Si IntelliJ IDEA instalación no pide habilitar complemento Scala, inicio IntelliJ IDEA y siga los pasos siguientes para instalar el complemento:

1. Iniciar IntelliJ IDEA y desde la pantalla de bienvenida, haga clic en **Configurar** y, a continuación, haga clic en **complementos**.

    ![Habilitar complemento scala](./media/hdinsight-apache-spark-create-standalone-application/enable-scala-plugin.png)

2. En la siguiente pantalla, haga clic en **instalar JetBrains complemento** de la esquina inferior izquierda. En el cuadro de diálogo **Examinar JetBrains complementos** que se abre, busque Scala y, a continuación, haga clic en **instalar**.

    ![Instale el complemento de scala](./media/hdinsight-apache-spark-create-standalone-application/install-scala-plugin.png)

3. Después de que el complemento se instala correctamente, haga clic en el **botón Reiniciar IntelliJ IDEA** para reiniciar el IDE.

## <a name="create-a-standalone-scala-project"></a>Crear un proyecto de Scala independiente

1. Inicie IntelliJ IDEA y cree un nuevo proyecto. En el cuadro de diálogo nuevo proyecto, efectúe las acciones siguientes y, a continuación, haga clic en **siguiente**.

    ![Crear proyecto experto](./media/hdinsight-apache-spark-create-standalone-application/create-maven-project.png)

    * Seleccione **Maven** como el tipo de proyecto.
    * Especifique un **proyecto SDK**. Haga clic en nuevo y vaya al directorio de instalación de Java, normalmente `C:\Program Files\Java\jdk1.8.0_66`.
    * Seleccione la opción **crear desde Arquetipo** .
    * En la lista de archetypes, seleccione **org.scala tools.archetypes:scala Arquetipo simple**. Esto creará la estructura de directorios adecuada y descargar las dependencias predeterminados necesarios para escribir Scala programa.

2. Proporcionar valores relevantes para **ID**, **ArtifactId**y **versión**. Haga clic en **siguiente**.

3. En el cuadro de diálogo siguiente, donde puede especificar el directorio de inicio de experto y otras opciones de configuración de usuario, acepte los valores predeterminados y haga clic en **siguiente**.

4. En el último cuadro de diálogo, especifique un nombre de proyecto y una ubicación y, a continuación, haga clic en **Finalizar**.

5. Elimine el archivo **MySpec.Scala** en **src\test\scala\com\microsoft\spark\example**. No es necesario para la aplicación.

6. Si es necesario, cambie el nombre de los archivos de origen y de prueba de forma predeterminada. En el panel izquierdo de la IDEA de IntelliJ, vaya a **src\main\scala\com.microsoft.spark.example**. Haga clic con el botón **App.scala**, haga clic en **Refactorizar**, haga clic en Cambiar nombre archivo, en el cuadro de diálogo, proporcione el nuevo nombre de la aplicación y, a continuación, haga clic en **Refactorizar**.

    ![Cambiar el nombre de archivos](./media/hdinsight-apache-spark-create-standalone-application/rename-scala-files.png)  

7. En los siguientes pasos, se actualizará la pom.xml para definir las dependencias de la aplicación de motor Scala. Para que esas dependencias descargado y resolver automáticamente, debe configurar a experto en consecuencia.

    ![Configurar a experto para las descargas automáticas](./media/hdinsight-apache-spark-create-standalone-application/configure-maven.png)

    1. En el menú **archivo** , haga clic en **configuración**.
    2. En el cuadro de diálogo de **configuración** , vaya a **crear, ejecución, implementación** > **Generar herramientas** > **Maven** > **Importar**.
    3. Seleccione la opción de **experto en importar proyectos automáticamente**.
    4. Haga clic en **Aplicar**y, a continuación, haga clic en **Aceptar**.


8. Actualizar el archivo de origen Scala para incluir el código de la aplicación. Abrir y reemplace el código de ejemplo existente con el siguiente código y guardar los cambios. Este código lee los datos de la HVAC.csv (disponible en todos los clústeres HDInsight Spark) recupera las filas que solo tienen un dígito en la sexta columna y escribe el resultado en **/HVACOut** en el contenedor de almacenamiento predeterminada para el clúster.

        package com.microsoft.spark.example

        import org.apache.spark.SparkConf
        import org.apache.spark.SparkContext

        /**
          * Test IO to wasb
          */
        object WasbIOTest {
          def main (arg: Array[String]): Unit = {
            val conf = new SparkConf().setAppName("WASBIOTest")
            val sc = new SparkContext(conf)

            val rdd = sc.textFile("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")

            //find the rows which have only one digit in the 7th column in the CSV
            val rdd1 = rdd.filter(s => s.split(",")(6).length() == 1)

            rdd1.saveAsTextFile("wasbs:///HVACout")
          }
        }


9. Actualizar la pom.xml.

    1.  Dentro de `<project>\<properties>` , agregue lo siguiente:

            <scala.version>2.10.4</scala.version>
            <scala.compat.version>2.10.4</scala.compat.version>
            <scala.binary.version>2.10</scala.binary.version>

    2. Dentro de `<project>\<dependencies>` , agregue lo siguiente:

            <dependency>
              <groupId>org.apache.spark</groupId>
              <artifactId>spark-core_${scala.binary.version}</artifactId>
              <version>1.4.1</version>
            </dependency>

    Guardar cambios en pom.xml.

10. Crear el archivo .jar. IDEA IntelliJ permite la creación de JAR como un efecto de un proyecto. Realice los pasos siguientes.

    1. En el menú **archivo** , haga clic en **Estructura del proyecto**.
    2. En el cuadro de diálogo de **Estructura del proyecto** , haga clic en **artefactos** y, a continuación, haga clic en el signo más. En el cuadro de diálogo emergente, haga clic en **JAR**y, a continuación, haga clic en **desde módulos con dependencias**.

        ![Crear JAR](./media/hdinsight-apache-spark-create-standalone-application/create-jar-1.png)

    3. En el cuadro de diálogo **Crear JAR de módulos** , haga clic en los puntos suspensivos (![puntos suspensivos](./media/hdinsight-apache-spark-create-standalone-application/ellipsis.png) ) en la **Clase principal**.

    4. En el cuadro de diálogo **Seleccionar clase de principales** , seleccione la clase que aparece de forma predeterminada y, a continuación, haga clic en **Aceptar**.

        ![Crear JAR](./media/hdinsight-apache-spark-create-standalone-application/create-jar-2.png)

    5. En el cuadro de diálogo **Crear JAR de módulos** , asegúrese de que está seleccionada la opción para **extraer el destino JAR** y, a continuación, haga clic en **Aceptar**. Esto crea un sola JAR con todas las dependencias.

        ![Crear JAR](./media/hdinsight-apache-spark-create-standalone-application/create-jar-3.png)

    6. La ficha Diseño de salida enumera todos la jars se incluye como parte del proyecto de experto. Puede seleccionar y eliminar los que la aplicación Scala no tiene ninguna dependencia directa. Para la aplicación que estamos creando aquí, puede quitar todos excepto el último uno (**SparkSimpleApp compilar resultados**). Seleccione la jars para eliminar y, a continuación, haga clic en el icono **Eliminar** .

        ![Crear JAR](./media/hdinsight-apache-spark-create-standalone-application/delete-output-jars.png)

        Asegúrese de que **se basan en realizar** casilla está activada, lo que garantiza que se ha creado el tarro cada vez que se genera o se actualiza el proyecto. Haga clic en **Aplicar** y, a continuación, en **Aceptar**.

    7. Desde la barra de menús, haga clic en **Generar**y, a continuación, haga clic en **Crear proyecto**. También puede hacer clic en **Generar artefactos** para crear el tarro. Jar de salida se crea en **\out\artifacts**.

        ![Crear JAR](./media/hdinsight-apache-spark-create-standalone-application/output.png)

## <a name="run-the-application-on-the-spark-cluster"></a>Ejecute la aplicación en el clúster de motor

Para ejecutar la aplicación en el clúster, haga lo siguiente:

* **Copiar el tarro de aplicación a los blobs de Windows Azure almacenamiento** asociado con el clúster. Puede usar [**AzCopy**](../storage/storage-use-azcopy.md), una utilidad de la línea de comandos para hacerlo. Hay una gran cantidad de otros clientes, así que puede usar para cargar los datos. Puede encontrar más información acerca de ellos al [cargar los datos para trabajos de Hadoop en HDInsight](hdinsight-upload-data.md).

* **Usar Livio para enviar un trabajo de la aplicación de manera remota** al clúster motor. Motor de clústeres en HDInsight incluye Livio que expone extremos REST para enviar trabajos de motor de forma remota. Para obtener más información, vea [trabajos de motor enviar remotamente mediante Livio con clústeres motor en HDInsight](hdinsight-apache-spark-livy-rest-interface.md).


## <a name="seealso"></a>Vea también


* [Información general: Apache motor en HDInsight de Azure](hdinsight-apache-spark-overview.md)

### <a name="scenarios"></a>Escenarios

* [Motor con BI: realizar análisis de datos interactivas con motor en HDInsight con herramientas de inteligencia empresarial](hdinsight-apache-spark-use-bi-tools.md)

* [Motor con Machine aprendizaje: motor de uso en HDInsight para analizar usando HVAC datos de temperatura de creación](hdinsight-apache-spark-ipython-notebook-machine-learning.md)

* [Motor con Machine aprendizaje: motor de uso en HDInsight de predecir los resultados de la inspección de comida](hdinsight-apache-spark-machine-learning-mllib-ipython.md)

* [Motor de flujo de datos: Motor de uso en HDInsight para la creación de aplicaciones de transmisión en tiempo real](hdinsight-apache-spark-eventhub-streaming.md)

* [Análisis de registro de sitio Web con el motor en HDInsight](hdinsight-apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Crear y ejecutar aplicaciones

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
