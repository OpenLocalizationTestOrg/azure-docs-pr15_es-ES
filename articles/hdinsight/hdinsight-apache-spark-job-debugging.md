<properties 
    pageTitle="Realizar el seguimiento y depuración trabajos que se ejecutan en Apache motor clúster en HDInsight | Microsoft Azure" 
    description="Usar hilo UI, interfaz de usuario de motor y servidor motor historial para realizar un seguimiento y depurar trabajos se ejecutan en un clúster de motor en HDInsight de Azure" 
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
    ms.date="08/25/2016" 
    ms.author="nitinme"/>

# <a name="track-and-debug-jobs-running-on-apache-spark-cluster-in-hdinsight-linux"></a>Realizar el seguimiento y depuración trabajos que se ejecutan en clúster de motor de Apache en HDInsight Linux

En este artículo aprenderá a realizar un seguimiento y trabajos de motor mediante la interfaz de usuario del hilo, interfaz de usuario de motor y el servidor de historial de motor de depuración. En este artículo, comenzaremos un trabajo de motor mediante un bloc de notas disponible con el clúster de motor **aprendizaje de máquina: análisis predictivo en los datos de la inspección de comida con MLLib**. Puede usar los siguientes pasos para realizar un seguimiento de una aplicación que ha enviado con cualquier otro método, por ejemplo, el **motor de envío**.

##<a name="prerequisites"></a>Requisitos previos

Debe tener el siguiente:

- Una suscripción de Azure. Vea la [versión de prueba gratuita de Azure obtener](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
- Un clúster de motor de Apache en HDInsight Linux. Para obtener instrucciones, vea [crear Apache motor clústeres en HDInsight de Azure](hdinsight-apache-spark-jupyter-spark-sql.md).
- Deberían haber comenzado ejecutando el Bloc de notas, **[aprendizaje de máquina: análisis predictivo en los datos de la inspección de comida con MLLib](hdinsight-apache-spark-machine-learning-mllib-ipython.md)**. Para obtener instrucciones sobre cómo ejecutar este bloc de notas, siga el vínculo.  

## <a name="track-an-application-in-the-yarn-ui"></a>Realizar un seguimiento de una aplicación en la interfaz de usuario de hilo

1. Inicie la interfaz de usuario del hilo. Desde el módulo de clúster, haga clic en **Panel de diseño**y, a continuación, haga clic en **hilo**.

    ![Iniciar la interfaz de usuario de hilo](./media/hdinsight-apache-spark-job-debugging/launch-yarn-ui.png)

    >[AZURE.TIP] Como alternativa, también puede iniciar la interfaz de usuario del hilo de la UI Ambari. Para iniciar la UI Ambari, desde el módulo de clúster, haga clic en **Panel de clúster**y, a continuación, haga clic en **Panel de clúster HDInsight**. Desde la UI Ambari, haga clic en **hilo**, haga clic en **Vínculos rápidos**y, a continuación, haga clic en el Administrador de recursos activa y, a continuación, haga clic en **La interfaz de usuario de ResourceManager**.  

3. Dado que se inició el trabajo de motor con blocs de notas Jupyter, la aplicación tiene el nombre **remotesparkmagics** (este es el nombre de todas las aplicaciones que se inician de los blocs de notas). Haga clic en el identificador de la aplicación con el nombre de la aplicación para obtener más información sobre el trabajo. Esto inicia la vista de la aplicación.

    ![Id. de aplicación de motor de búsqueda](./media/hdinsight-apache-spark-job-debugging/find-application-id.png)

    Para esas aplicaciones que se ejecutan en los blocs de notas Jupyter, el estado es siempre **EJECUTANDO** hasta que salga del Bloc de notas.

4. Desde la vista de la aplicación, puede profundizar más para calcular los contenedores asociada con la aplicación y los registros (stdout y stderr). También puede iniciar la interfaz de usuario de motor haciendo clic en el vínculo correspondiente a la **Dirección URL de seguimiento**, tal como se muestra a continuación. 

    ![Descargar los registros de contenedor](./media/hdinsight-apache-spark-job-debugging/download-container-logs.png)

## <a name="track-an-application-in-the-spark-ui"></a>Realizar un seguimiento de una aplicación en la interfaz de usuario de motor

En la interfaz de usuario del motor usted puede desglosar los trabajos de motor generados por la aplicación que ha empezado anteriormente.

1. Para iniciar la interfaz de usuario del motor desde la vista de la aplicación, haga clic en el vínculo con la **Dirección URL de seguimiento**, como se muestra en la captura de pantalla anteriormente. Puede ver todos los trabajos de motor que inician la aplicación que se ejecuta en el Bloc de notas de Jupyter.

    ![Ver los trabajos de motor](./media/hdinsight-apache-spark-job-debugging/view-spark-jobs.png)

2. Haga clic en la ficha **elementos de ejecución** para ver la información de procesamiento y almacenamiento de cada elemento de ejecución. También puede recuperar la pila de llamadas haciendo clic en el vínculo de **Descarga de subproceso** .

    ![Ver elementos de ejecución de motor](./media/hdinsight-apache-spark-job-debugging/view-spark-executors.png)
 
3. Haga clic en la ficha **fases** para ver las fases asociadas a la aplicación.

    ![Etapas del motor de vista](./media/hdinsight-apache-spark-job-debugging/view-spark-stages.png)

    Cada fase puede tener varias tareas que puede ver las estadísticas de ejecución, como se muestra a continuación.

    ![Etapas del motor de vista](./media/hdinsight-apache-spark-job-debugging/view-spark-stages-details.png) 

4. Desde la página de detalles de la fase, puede iniciar la visualización DAG incluidos. Vínculo expandir la **Visualización de DAG incluidos** en la parte superior de la página, como se muestra a continuación.

    ![Ver la visualización de fases DAG incluidos motor](./media/hdinsight-apache-spark-job-debugging/view-spark-stages-dag-visualization.png)

    DAG incluidos o un gráfico de Aclyic directo representa las diferentes fases de la aplicación. Cada cuadro azul en el gráfico representa una operación de motor invocada desde la aplicación.

5. Desde la página de detalles de la fase, también puede iniciar la vista de escala de tiempo de aplicación. Vínculo expandir la **Escala de tiempo del evento** en la parte superior de la página, como se muestra a continuación.

    ![Ver motor etapas evento escala de tiempo](./media/hdinsight-apache-spark-job-debugging/view-spark-stages-event-timeline.png)

    Esto muestra los eventos de motor en forma de una escala de tiempo. La vista escala de tiempo está disponible en tres niveles en trabajos, dentro de una tarea y dentro de una fase. La imagen anterior captura la vista escala de tiempo para una determinada fase.

    >[AZURE.TIP] Si selecciona la casilla de verificación **Habilitar zoom** , puede desplazarse a través de la vista escala de tiempo izquierda y derecha.

6. Otras pestañas de la interfaz de usuario de motor proporcionan información útil acerca de la instancia del motor.

    * Ficha de almacenamiento: si su aplicación crea una RDDs, puede encontrar información sobre los de la ficha de almacenamiento.
    * Ficha entorno - esta pestaña proporciona una gran cantidad de información útil acerca de la instancia de motor como la 
        * Versión scala
        * Directorio de registro de eventos asociado con el clúster
        * Número de núcleos del elemento de ejecución de la aplicación
        * Etcetera.

## <a name="find-information-about-completed-jobs-using-the-spark-history-server"></a>Buscar información sobre los trabajos completados con el servidor de historial de motor

Una vez que se completa un trabajo, la información sobre el trabajo se conserva en el servidor de historial de motor.

1. Para iniciar el servidor de historial motor, en el módulo de clúster, haga clic en **Panel de diseño**y, a continuación, haga clic en **Servidor de historial de motor**.

    ![Iniciar el servidor de historial de motor](./media/hdinsight-apache-spark-job-debugging/launch-spark-history-server.png)

    >[AZURE.TIP] Como alternativa, también puede iniciar la interfaz de usuario del servidor del historial de motor de la UI Ambari. Para iniciar la UI Ambari, desde el módulo de clúster, haga clic en **Panel de clúster**y, a continuación, haga clic en **Panel de clúster HDInsight**. Desde la UI Ambari, haga clic en el **motor**, haga clic en **Vínculos rápidos**y, a continuación, haga clic en **Interfaz de usuario de motor historial Server**.

2. Verá todas las aplicaciones completadas enumeradas. Haga clic en un identificador de la aplicación para profundizar en una aplicación para obtener más información.

    ![Iniciar el servidor de historial de motor](./media/hdinsight-apache-spark-job-debugging/view-completed-applications.png)
    

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

* [Usar paquetes externos con blocs de notas Jupyter](hdinsight-apache-spark-jupyter-notebook-use-external-packages.md)

* [Instalar Jupyter en su equipo y conectarse a un clúster de HDInsight Spark](hdinsight-apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Administrar recursos

* [Administrar recursos para el clúster de motor de Apache en HDInsight de Azure](hdinsight-apache-spark-resource-manager.md)
