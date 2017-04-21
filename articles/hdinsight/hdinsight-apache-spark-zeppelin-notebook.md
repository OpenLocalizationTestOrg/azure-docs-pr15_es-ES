<properties 
    pageTitle="Utilizar blocs de notas de globo dirigible con clúster de motor en HDInsight Linux | Microsoft Azure" 
    description="Instrucciones paso a paso acerca de cómo usar blocs de notas de globo dirigible con clústeres motor en HDInsight Linux." 
    services="hdinsight" 
    documentationCenter="" 
    authors="nitinme" 
    manager="jhubbard" 
    editor="cgronlun"/>

<tags 
    ms.service="hdinsight" 
    ms.workload="big-data" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/05/2016" 
    ms.author="nitinme"/>


# <a name="use-zeppelin-notebooks-with-apache-spark-cluster-on-hdinsight-linux"></a>Utilizar blocs de notas de globo dirigible con clúster de motor de Apache en HDInsight Linux

HDInsight Spark clústeres incluyen blocs de notas de Zeppelin que puede usar para ejecutar el motor trabajos. En este artículo, aprenda a usar el Bloc de notas de globo dirigible en un clúster de HDInsight.


**Requisitos previos:**

* Una suscripción de Azure. Vea la [versión de prueba gratuita de Azure obtener](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

* Un clúster de motor de Apache. Para obtener instrucciones, vea [crear Apache motor clústeres en HDInsight de Azure](hdinsight-apache-spark-jupyter-spark-sql.md).

## <a name="launch-a-zeppelin-notebook"></a>Inicie un bloc de notas de globo dirigible

1. Desde el módulo de clúster motor, haga clic en **Panel de clúster**y, a continuación, haga clic en **Bloc de notas de Zeppelin**. Si se le solicita, escriba las credenciales de administrador para el clúster.

    > [AZURE.NOTE] También puede contactar el Bloc de notas de Zeppelin para el clúster abriendo la siguiente URL en el explorador. Reemplazar __NOMBREDECLÚSTER__ con el nombre del clúster:
    >
    > `https://CLUSTERNAME.azurehdinsight.net/zeppelin`

2. Crear un nuevo bloc de notas. Desde el panel de encabezado, haga clic en **Bloc de notas**y, a continuación, haga clic en **Crear nueva nota**.

    ![Crear un nuevo bloc de notas de globo dirigible] (./media/hdinsight-apache-spark-zeppelin-notebook/hdispark.createnewnote.png "Crear un nuevo bloc de notas de globo dirigible")

    Escriba un nombre para el Bloc de notas y, a continuación, haga clic en **Crear nota**.

3. Además, asegúrese de que el encabezado de Bloc de notas muestra un estado conectado. Se está indicado por un punto verde en la esquina superior derecha.

    ![Estado del Bloc de notas de globo dirigible] (./media/hdinsight-apache-spark-zeppelin-notebook/hdispark.newnote.connected.png "Estado del Bloc de notas de globo dirigible")

4. Cargar datos de ejemplo en una tabla temporal. Cuando se crea un clúster de motor en HDInsight, el archivo de datos de ejemplo, **hvac.csv**, se copia en la cuenta de almacenamiento asociado con **\HdiSamples\SensorSampleData\hvac**.

    En el párrafo vacío que se crea de forma predeterminada en el nuevo bloc de notas, pegue el fragmento de código siguiente.

        %livy.spark
        //The above magic instructs Zeppelin to use the Livy Scala interpreter

        // Create an RDD using the default Spark context, sc
        val hvacText = sc.textFile("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")
        
        // Define a schema
        case class Hvac(date: String, time: String, targettemp: Integer, actualtemp: Integer, buildingID: String)
        
        // Map the values in the .csv file to the schema
        val hvac = hvacText.map(s => s.split(",")).filter(s => s(0) != "Date").map(
            s => Hvac(s(0), 
                    s(1),
                    s(2).toInt,
                    s(3).toInt,
                    s(6)
            )
        ).toDF()
        
        // Register as a temporary table called "hvac"
        hvac.registerTempTable("hvac")
        
    Presione **MAYÚS + ENTRAR** o haga clic en el botón **Reproducir** del párrafo ejecutar el fragmento de código. El estado en la esquina derecha del párrafo debe progreso de listo, pendiente, ejecute a terminado. El resultado se muestra en la parte inferior del mismo párrafo. La captura de pantalla tiene el siguiente aspecto:

    ![Crear una tabla temporal de datos sin formato] (./media/hdinsight-apache-spark-zeppelin-notebook/hdispark.note.loaddDataintotable.png "Crear una tabla temporal de datos sin formato")

    También puede proporcionar un título a cada párrafo. Desde la esquina derecha, haga clic en el icono **configuración** y, a continuación, haga clic en **Mostrar título**.

5. Ahora puede ejecutar instrucciones SQL de motor en la tabla **hvac** . Pegue la siguiente consulta en un párrafo nuevo. La consulta recupera el identificador de creación y la diferencia entre el destino y las temperaturas reales de cada edificio en una fecha determinada. Presione **MAYÚS + ENTRAR**.

        %sql
        select buildingID, (targettemp - actualtemp) as temp_diff, date from hvac where date = "6/1/13" 

    La instrucción **sql %** al principio indica el Bloc de notas para usar el intérprete Livio Scala.

    La siguiente captura de pantalla muestra el resultado.

    ![Ejecutar una instrucción SQL de motor usando el Bloc de notas] (./media/hdinsight-apache-spark-zeppelin-notebook/hdispark.note.sparksqlquery1.png "Ejecutar una instrucción SQL de motor usando el Bloc de notas")

     Para cambiar entre las representaciones distintas para el mismo resultado, haga clic en las opciones de presentación (resaltadas en el rectángulo). Haga clic en **configuración** para elegir qué constituye la clave y los valores en los resultados. La captura de pantalla encima utiliza **buildingID** como la clave y el promedio de **temp_diff** como el valor.

    
6. También puede ejecutar instrucciones SQL de motor mediante las variables de la consulta. El fragmento de código siguiente muestra cómo definir una variable, **Temp**, en la consulta con los posibles valores que desea consultar con. Cuando ejecute la consulta por primera vez, una lista desplegable se rellena automáticamente con los valores especificados para la variable.

        %sql
        select buildingID, date, targettemp, (targettemp - actualtemp) as temp_diff from hvac where targettemp > "${Temp = 65,65|75|85}" 

    Pegue este fragmento de código en un nuevo párrafo y presione **MAYÚS + ENTRAR**. La siguiente captura de pantalla muestra el resultado.

    ![Ejecutar una instrucción SQL de motor usando el Bloc de notas] (./media/hdinsight-apache-spark-zeppelin-notebook/hdispark.note.sparksqlquery2.png "Ejecutar una instrucción SQL de motor usando el Bloc de notas")

    Para las consultas posteriores, puede seleccionar un nuevo valor de la lista desplegable y vuelva a ejecutar la consulta. Haga clic en **configuración** para elegir qué constituye la clave y los valores en los resultados. La captura de pantalla anterior usa **buildingID** como la clave, el promedio de **temp_diff** como el valor y **targettemp** como grupo.

7. Reinicie el intérprete Livio para salir de la aplicación. Para ello, abra Configuración intérprete haciendo clic en el inicio de sesión en el nombre de usuario de la esquina superior derecha y, a continuación, haga clic en **intérprete**.

    ![Intérprete de inicio] (./media/hdinsight-apache-spark-zeppelin-notebook/zeppelin-launch-interpreter.png "Sección de salida")

2. Desplácese a configuración de intérprete Livio y, a continuación, haga clic en **reiniciar**.

    ![Reinicie el intepreter Livio] (./media/hdinsight-apache-spark-zeppelin-notebook/hdispark.zeppelin.restart.interpreter.png "Reinicie el intepreter Zeppelin")

## <a name="how-do-i-use-external-packages-with-the-notebook"></a>¿Cómo se puede usar paquetes externos con el Bloc de notas?

Puede configurar el Bloc de notas de globo dirigible en clúster de motor de Apache en HDInsight (Linux) para usar paquetes externos contribuido a la Comunidad que no están incluidos-de-predefinidas en el clúster. Puede buscar en el [repositorio de experto](http://search.maven.org/) para la lista completa de paquetes que están disponibles. También puede obtener una lista de paquetes disponibles de otros orígenes. Por ejemplo, una lista completa de paquetes contribuido Comunidad está disponible en [Los paquetes de motor](http://spark-packages.org/).

En este artículo, se muestra cómo usar el paquete de [motor csv](http://search.maven.org/#artifactdetails%7Ccom.databricks%7Cspark-csv_2.10%7C1.4.0%7Cjar) con el Bloc de notas de Jupyter.

1. Abrir configuración de intérprete. Desde la esquina superior derecha, haga clic en el inicio de sesión en el nombre de usuario y, a continuación, haga clic en **intérprete**.

    ![Intérprete de inicio] (./media/hdinsight-apache-spark-zeppelin-notebook/zeppelin-launch-interpreter.png "Sección de salida")

2. Desplácese a configuración de intérprete Livio y, a continuación, haga clic en **Editar**.

    ![Cambiar la configuración de intérprete] (./media/hdinsight-apache-spark-zeppelin-notebook/zeppelin-use-external-package-1.png "Cambiar la configuración de intérprete")

3. Agregar una nueva clave, llamada **livy.spark.jars.packages** y establezca su valor en el formato `group:id:version`. Por lo tanto, si desea usar el paquete de [motor csv](http://search.maven.org/#artifactdetails%7Ccom.databricks%7Cspark-csv_2.10%7C1.4.0%7Cjar) , debe establecer el valor de la clave de `com.databricks:spark-csv_2.10:1.4.0`.

    ![Cambiar la configuración de intérprete] (./media/hdinsight-apache-spark-zeppelin-notebook/zeppelin-use-external-package-2.png "Cambiar la configuración de intérprete")

    Haga clic en **Guardar** y, a continuación, reinicie el intérprete Livio.

4. **Sugerencia**: si desea saber cómo llegar a que el valor de la clave de arriba, aquí le indicamos cómo.

    una. Busque el paquete en el repositorio de experto. En este tutorial, hemos usado el [motor csv](http://search.maven.org/#artifactdetails%7Ccom.databricks%7Cspark-csv_2.10%7C1.4.0%7Cjar).
    
    b. Desde el repositorio recopilar los valores de **ID**, **ArtifactId**y **versión**.

    ![Paquetes externos de uso con Jupyter Bloc de notas] (./media/hdinsight-apache-spark-zeppelin-notebook/use-external-packages-with-jupyter.png "Paquetes externos de uso con Jupyter Bloc de notas")

    c. Concatenar los tres valores, separados por un punto y coma (**:**).

        com.databricks:spark-csv_2.10:1.4.0

## <a name="where-are-the-zeppelin-notebooks-saved"></a>¿Dónde se guardan los blocs de notas de globo dirigible?

Los blocs de notas de globo dirigible se guardan en el clúster de headnodes. Por lo que si elimina el clúster, también se eliminarán los blocs de notas. Si desea conservar los blocs de notas para su uso posterior en otros clústeres, debe exportar cuando haya terminado de los trabajos en ejecución. Para exportar un bloc de notas, haga clic en el icono **Exportar** como se muestra en la imagen siguiente.

![Descargar el Bloc de notas] (./media/hdinsight-apache-spark-zeppelin-notebook/zeppelin-download-notebook.png "Descargar el Bloc de notas")

Esto guarda el Bloc de notas como archivo JSON en la ubicación de descarga.

## <a name="livy-session-management"></a>Administración de sesiones de Livio

Cuando se ejecuta el primer párrafo de código en el Bloc de notas Zeppelin, se crea una nueva sesión de Livio en el clúster HDInsight Spark. Esta sesión se comparte entre todos los blocs de notas de globo dirigible que cree. Si por algún motivo el Livio sesión es eliminado (reinicio de clúster, etc.), no podrá ejecutar trabajos desde el Bloc de notas de Zeppelin.

En este caso, debe realizar los siguientes pasos antes de iniciar la ejecución de trabajos de un bloc de notas de Zeppelin. 

1. Reinicie el intérprete Livio desde el Bloc de notas de Zeppelin. Para ello, abra Configuración intérprete haciendo clic en el inicio de sesión en el nombre de usuario de la esquina superior derecha y, a continuación, haga clic en **intérprete**.

    ![Intérprete de inicio] (./media/hdinsight-apache-spark-zeppelin-notebook/zeppelin-launch-interpreter.png "Sección de salida")

2. Desplácese a configuración de intérprete Livio y, a continuación, haga clic en **reiniciar**.

    ![Reinicie el intepreter Livio] (./media/hdinsight-apache-spark-zeppelin-notebook/hdispark.zeppelin.restart.interpreter.png "Reinicie el intepreter Zeppelin")

3. Ejecutar una celda de código de un bloc de notas Zeppelin. Esto crea una nueva sesión Livio en el clúster HDInsight.

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







