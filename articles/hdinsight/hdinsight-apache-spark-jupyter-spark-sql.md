<properties
    pageTitle="Crear un clúster de motor en HDInsight Linux y usar SQL del motor de Jupyter para análisis interactivo | Microsoft Azure"
    description="Instrucciones paso a paso acerca de cómo crear rápidamente un motor Apache clúster en HDInsight y luego use SQL del motor de blocs de notas de Jupyter para ejecutar consultas interactivas."
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
    ms.topic="get-started-article"
    ms.date="10/28/2016"
    ms.author="nitinme"/>


# <a name="get-started-create-apache-spark-cluster-on-hdinsight-linux-and-run-interactive-queries-using-spark-sql"></a>Introducción: crear clúster de motor de Apache en HDInsight Linux y ejecutar consultas interactivas con motor SQL

Obtenga información sobre cómo crear un clúster de Apache motor en HDInsight y, a continuación, usar el Bloc de notas de [Jupyter](https://jupyter.org) para ejecutar consultas de SQL de motor interactivas en el clúster de motor.

   ![Empezar a usar el motor de Apache en HDInsight] (./media/hdinsight-apache-spark-jupyter-spark-sql/hdispark.getstartedflow.png  "Empezar a usar el motor de Apache en tutorial HDInsight. Pasos ilustradas: crear una cuenta de almacenamiento. crear un clúster; ejecutar instrucciones SQL de motor")

[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="prerequisites"></a>Requisitos previos

- **Suscripción de un Azure**. Antes de comenzar este tutorial, debe tener una suscripción de Azure. Vea la [versión de prueba gratuita de Azure obtener](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

- **Cliente A Secure Shell (SSH)**: Linux, Unix y OS X sistemas especifica ninguno un SSH cliente a través de la `ssh` comando. Para sistemas de Windows, se recomienda [PuTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).
    
- **Teclas de Shell seguro (SSH) (opcionales)**: puede proteger la cuenta SSH utilizada para conectar con el clúster mediante una contraseña o una clave pública. Utilizando una contraseña le permite iniciar rápidamente y debe usar esta opción si desea crear un clúster rápidamente y ejecutar algunos trabajos de prueba. Con una clave es más segura, aunque requiere configuración adicional. Desea usar este método para crear un clúster de producción. En este artículo, se utiliza el enfoque de contraseña. Para obtener instrucciones sobre cómo crear y usar las teclas SSH con HDInsight, consulte los siguientes artículos:

    -  Desde un equipo Linux - [Uso SSH con HDInsight basados en Linux (Hadoop) de Linux, Unix o OS X](hdinsight-hadoop-linux-use-ssh-unix.md).
    
    -  Desde un equipo Windows - [Uso SSH con HDInsight basados en Linux (Hadoop) de Windows](hdinsight-hadoop-linux-use-ssh-windows.md).

>[AZURE.NOTE] En este artículo se usa una plantilla de administrador de recursos de Azure para crear un clúster de motor que usa [Azure almacenamiento de Blobs como el almacenamiento de clúster](hdinsight-hadoop-use-blob-storage.md). También puede crear un clúster de motor que utiliza el [Almacén de lago de datos de Azure](../data-lake-store/data-lake-store-overview.md) como un almacenamiento adicional, además de Azure almacenamiento de Blobs como el almacenamiento predeterminado. Para obtener instrucciones, consulte [crear un clúster de HDInsight con el almacén de datos de lago](../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md).

### <a name="access-control-requirements"></a>Requisitos de control de acceso

[AZURE.INCLUDE [access-control](../../includes/hdinsight-access-control-requirements.md)]

## <a name="create-spark-cluster"></a>Crear clúster de motor

En esta sección, creará un clúster de versión 3.4 HDInsight (motor versión 1.6.1) mediante una plantilla de administrador de recursos de Azure. Para obtener información acerca de las versiones de HDInsight y los SLA, consulte [control de versiones de componente HDInsight](hdinsight-component-versioning.md). Para otros métodos de creación de clúster, consulte [crear HDInsight clústeres](hdinsight-hadoop-provision-linux-clusters.md).

1. Haga clic en la imagen siguiente para abrir la plantilla en el Portal de Azure.         

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Farmtemplates%2Fcreate-linux-based-spark-cluster-in-hdinsight.json" target="_blank"><img src="https://acom.azurecomcdn.net/80C57D/cdn/mediahandler/docarticles/dpsmedia-prod/azure.microsoft.com/en-us/documentation/articles/hdinsight-hbase-tutorial-get-started-linux/20160201111850/deploy-to-azure.png" alt="Deploy to Azure"></a>
    
    La plantilla se encuentra en un contenedor de blob público *https://hditutorialdata.blob.core.windows.net/armtemplates/create-linux-based-spark-cluster-in-hdinsight.json*. 
   
2. Desde el módulo de parámetros, escriba lo siguiente:

    - **NombreDeClúster**: escriba un nombre para el clúster de Hadoop que se creará.
    - **Contraseña y nombre de inicio de sesión de clúster**: el nombre de inicio de sesión predeterminado es Admin.
    - **SSH nombre de usuario y contraseña**.
    
    Escriba estos valores.  Los necesitará más adelante en el tutorial.

    > [AZURE.NOTE] SSH se utiliza para acceder de forma remota el clúster HDInsight con una línea de comandos. El nombre de usuario y la contraseña que usa aquí se usa para conectarse al clúster a través de SSH. Además, el nombre de usuario SSH debe ser único, como crea una cuenta de usuario en todos los nodos de clúster de HDInsight. Los siguientes son algunos de los nombres de cuenta reservados para su uso por los servicios en el clúster y no se pueden usar como el nombre de usuario SSH:
    >
    > raíz, hdiuser, tormenta, hbase, ubuntu, zookeeper, hdfs, hilo, mapred, hbase, subárbol, oozie, falcon, sqoop, administración, tez, hcat, hdinsight zookeeper.

    > Para obtener más información sobre cómo utilizar SSH con HDInsight, consulte uno de los siguientes artículos:

    > * [Utilizar SSH con basados en Linux Hadoop en HDInsight de Linux, Unix o OS X](hdinsight-hadoop-linux-use-ssh-unix.md)
    > * [Utilizar SSH con basados en Linux Hadoop en HDInsight de Windows.](hdinsight-hadoop-linux-use-ssh-windows.md)

    
3. Haga clic en **Aceptar** para guardar los parámetros.

4 desde el módulo de **implementación personalizada** , haga clic en cuadro de lista desplegable de **grupo de recursos** y, a continuación, haga clic en **nuevo** para crear un nuevo grupo de recursos. El grupo de recursos es un contenedor que agrupa el clúster, la cuenta de almacenamiento dependientes y otros recursos vinculados.

5. Haga clic en **condiciones legales**y, a continuación, haga clic en **crear**.

6. Haga clic en **crear**. Verá un nuevo mosaico titulado implementación Submitting para su implementación de la plantilla. Tarda aproximadamente 20 minutos para crear el clúster y la base de datos SQL.



## <a name="run-spark-sql-queries-using-a-jupyter-notebook"></a>Ejecutar consultas de SQL de motor con un bloc de notas de Jupyter

En esta sección, usa el Bloc de notas de Jupyter para ejecutar consultas de SQL de motor en el clúster de motor. HDInsight Spark clústeres proporcionan dos kernels que puede usar con el Bloc de notas de Jupyter. Estos son:

* **PySpark** (para aplicaciones escritas en Python)
* **Motor** (para aplicaciones escritas en Scala)

En este artículo, usará el núcleo PySpark. En el artículo [Kernels disponibles en los blocs de notas de Jupyter con HDInsight de motor clústeres](hdinsight-apache-spark-jupyter-notebook-kernels.md#why-should-i-use-the-new-kernels) puede leer en detalle acerca de las ventajas de usar el núcleo PySpark. Sin embargo, par de claves ventajas de usar el núcleo PySpark es:

* No es necesario configurar los contextos motor y subárbol. Se establecen automáticamente para usted.
* Puede usar magics de celda, como por ejemplo `%%sql`para ejecutar directamente las consultas SQL o subárbol, sin los fragmentos de código anterior.
* El resultado de las consultas SQL o subárbol automáticamente se visualiza.

### <a name="create-jupyter-notebook-with-pyspark-kernel"></a>Crear bloc de notas de Jupyter con núcleo de PySpark 

1. Desde el [Portal de Azure](https://portal.azure.com/), desde el startboard, haga clic en el mosaico de clúster motor (si lo anclados a la startboard). También puede ir a su clúster en **Examinar todos** > **HDInsight clústeres**.   

2. Desde el módulo de clúster motor, haga clic en **Panel de diseño**y, a continuación, haga clic en **Bloc de notas de Jupyter**. Si se le solicita, escriba las credenciales de administrador para el clúster.

    > [AZURE.NOTE] También puede contactar el Bloc de notas de Jupyter para el clúster abriendo la siguiente URL en el explorador. Reemplazar __NOMBREDECLÚSTER__ con el nombre del clúster:
    >
    > `https://CLUSTERNAME.azurehdinsight.net/jupyter`

2. Crear un nuevo bloc de notas. Haga clic en **nuevo**y, a continuación, haga clic en **PySpark**.

    ![Crear un nuevo bloc de notas de Jupyter] (./media/hdinsight-apache-spark-jupyter-spark-sql/hdispark.note.jupyter.createnotebook.png "Crear un nuevo bloc de notas de Jupyter")

3. Un bloc de notas se crea y se abre con el nombre Untitled.pynb. Haga clic en el nombre del Bloc de notas en la parte superior y escriba un nombre descriptivo.

    ![Proporcionar un nombre para el Bloc de notas] (./media/hdinsight-apache-spark-jupyter-spark-sql/hdispark.note.jupyter.notebook.name.png "Proporcionar un nombre para el Bloc de notas")

4. Ya ha creado un bloc de notas con el núcleo PySpark, no es necesario crear cualquier contextos explícitamente. Los contextos motor y subárbol se crearán automáticamente para usted cuando se ejecuta la primera celda de código. Puede empezar importando los tipos necesarios para este escenario. Para ello, pegue el fragmento de código siguiente en una celda y presione **MAYÚS + ENTRAR**.

        from pyspark.sql.types import *
        
    Cada vez que ejecute un trabajo en Jupyter, el título de ventana de explorador web le mostrará un estado **(disponibilidad)** junto con el título del Bloc de notas. También verá un círculo sólido al lado del texto de **PySpark** en la esquina superior derecha. Una vez completada la tarea, cambiará a un círculo hueco.

     ![Estado de un trabajo de Bloc de notas de Jupyter] (./media/hdinsight-apache-spark-jupyter-spark-sql/hdispark.jupyter.job.status.png "Estado de un trabajo de Bloc de notas de Jupyter")

4. Cargar datos de ejemplo en una tabla temporal. Cuando se crea un clúster de motor en HDInsight, el archivo de datos de ejemplo, **hvac.csv**, se copia en la cuenta de almacenamiento asociado con **\HdiSamples\HdiSamples\SensorSampleData\hvac**.

    En una celda vacía, pegue el siguiente ejemplo de código y presione **MAYÚS + ENTRAR**. Este ejemplo de código registra los datos en una tabla temporal denominada **hvac**.

        # Load the data
        hvacText = sc.textFile("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")
        
        # Create the schema
        hvacSchema = StructType([StructField("date", StringType(), False),StructField("time", StringType(), False),StructField("targettemp", IntegerType(), False),StructField("actualtemp", IntegerType(), False),StructField("buildingID", StringType(), False)])
        
        # Parse the data in hvacText
        hvac = hvacText.map(lambda s: s.split(",")).filter(lambda s: s[0] != "Date").map(lambda s:(str(s[0]), str(s[1]), int(s[2]), int(s[3]), str(s[6]) ))
        
        # Create a data frame
        hvacdf = sqlContext.createDataFrame(hvac,hvacSchema)
        
        # Register the data fram as a table to run queries against
        hvacdf.registerTempTable("hvac")

5. Dado que está utilizando un núcleo PySpark, ahora puede ejecutar directamente una consulta SQL en la tabla temporal **hvac** que acaba de crear mediante el uso de la `%%sql` mágico. Para obtener más información sobre la `%%sql` mágico, así como otras magics disponibles con el núcleo PySpark, vea [Kernels disponibles en los blocs de notas de Jupyter con HDInsight de motor clústeres](hdinsight-apache-spark-jupyter-notebook-kernels.md#why-should-i-use-the-new-kernels).
        
        %%sql
        SELECT buildingID, (targettemp - actualtemp) AS temp_diff, date FROM hvac WHERE date = \"6/1/13\"

5. Una vez que la tarea se completó correctamente, el resultado de la tabla siguiente se muestra de forma predeterminada.

    ![Resultados de la tabla de resultados de consulta] (./media/hdinsight-apache-spark-jupyter-spark-sql/tabular.output.png "Resultados de la tabla de resultados de consulta")

    También puede ver los resultados en otras visualizaciones también. Por ejemplo, un gráfico de área para el mismo resultado tendría un aspecto similar al siguiente.

    ![Gráfico de áreas del resultado de la consulta] (./media/hdinsight-apache-spark-jupyter-spark-sql/area.output.png "Gráfico de áreas del resultado de la consulta")


6. Cuando haya terminado de ejecutar la aplicación, debe cerrar el Bloc de notas para liberar los recursos. Para ello, en el menú **archivo** en el Bloc de notas, haga clic en **Cerrar y detener**. Este se cerrará y cierre el Bloc de notas.

##<a name="delete-the-cluster"></a>Eliminar el clúster

[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]


## <a name="see-also"></a>Vea también


* [Información general: Apache motor en HDInsight de Azure](hdinsight-apache-spark-overview.md)

### <a name="scenarios"></a>Escenarios

* [Motor con BI: realizar análisis de datos interactivas con motor en HDInsight con herramientas de inteligencia empresarial](hdinsight-apache-spark-use-bi-tools.md)

* [Motor con Machine aprendizaje: motor de uso en HDInsight para analizar usando HVAC datos de temperatura de creación](hdinsight-apache-spark-ipython-notebook-machine-learning.md)

* [Motor con Machine aprendizaje: motor de uso en HDInsight de predecir los resultados de la inspección de comida](hdinsight-apache-spark-machine-learning-mllib-ipython.md)

* [Motor de flujo de datos: Motor de uso en HDInsight para la creación de aplicaciones de transmisión en tiempo real](hdinsight-apache-spark-eventhub-streaming.md)

* [Análisis de registro de sitio Web con el motor en HDInsight](hdinsight-apache-spark-custom-library-website-log-analysis.md)

* [Análisis de datos de telemetría aplicación conocimientos con motor en HDInsight](hdinsight-spark-analyze-application-insight-logs.md)

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

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[azure-management-portal]: https://manage.windowsazure.com/
[azure-create-storageaccount]: storage-create-storage-account.md
