<properties 
    pageTitle="Usar el Administrador de recursos para asignar recursos a clúster Apache motor en HDInsight | Microsoft Azure" 
    description="Obtenga información sobre cómo usar el Administrador de recursos para clústeres de motor en HDInsight para mejorar el rendimiento." 
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


# <a name="manage-resources-for-the-apache-spark-cluster-on-hdinsight-linux"></a>Administrar recursos para el clúster de motor de Apache en HDInsight Linux

En este artículo aprenderá cómo tener acceso a las interfaces como Ambari UI, interfaz de usuario del hilo y el servidor de historial motor asociado con el clúster de motor. También aprenderá cómo ajustar la configuración del clúster para un rendimiento óptimo.

**Requisitos previos:**

Debe tener el siguiente:

- Una suscripción de Azure. Vea la [versión de prueba gratuita de Azure obtener](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
- Un clúster de motor de Apache en HDInsight Linux. Para obtener instrucciones, vea [crear Apache motor clústeres en HDInsight de Azure](hdinsight-apache-spark-jupyter-spark-sql.md).

## <a name="how-do-i-launch-the-ambari-web-ui"></a>¿Cómo se puede iniciar la interfaz de usuario de Web Ambari?

1. Desde el [Portal de Azure](https://portal.azure.com/), desde el startboard, haga clic en el mosaico de clúster motor (si lo anclados a la startboard). También puede ir a su clúster en **Examinar todos** > **HDInsight clústeres**. 
 
2. En el módulo de clúster motor, haga clic en **panel**. Cuando se le pida, escriba las credenciales de administrador para el clúster de motor.

    ![Iniciar Ambari] (./media/hdinsight-apache-spark-resource-manager/hdispark.cluster.launch.dashboard.png "Inicie el Administrador de recursos")

3. Esto debería iniciar la interfaz de usuario de Web Ambari, tal como se muestra a continuación.

    ![Interfaz de usuario de Web Ambari] (./media/hdinsight-apache-spark-resource-manager/ambari-web-ui.png "Interfaz de usuario de Web Ambari")   

## <a name="how-do-i-launch-the-spark-history-server"></a>¿Cómo se puede iniciar el servidor de historial de motor?

1. Desde el [Portal de Azure](https://portal.azure.com/), desde el startboard, haga clic en el mosaico de clúster motor (si lo anclados a la startboard).

2. Desde el módulo de clúster, **Vínculos rápidos**, haga clic en **Panel de clúster**. En el módulo de **Clúster paneles** , haga clic en **Servidor de historial de motor**.

    ![Servidor de historial de motor] (./media/hdinsight-apache-spark-resource-manager/launch-history-server.png "Servidor de historial de motor")

    Cuando se le pida, escriba las credenciales de administrador para el clúster de motor.


## <a name="how-do-i-launch-the-yarn-ui"></a>¿Cómo se puede iniciar la interfaz de usuario del hilo?

Puede usar la interfaz de usuario del hilo para supervisar las aplicaciones que se están ejecutando en el clúster de motor. 

1. Desde el módulo de clúster, haga clic en **Panel de diseño**y, a continuación, haga clic en **hilo**.

    ![Iniciar la interfaz de usuario de hilo](./media/hdinsight-apache-spark-resource-manager/launch-yarn-ui.png)

    >[AZURE.TIP] Como alternativa, también puede iniciar la interfaz de usuario del hilo de la UI Ambari. Para iniciar la UI Ambari, desde el módulo de clúster, haga clic en **Panel de clúster**y, a continuación, haga clic en **Panel de clúster HDInsight**. Desde la UI Ambari, haga clic en **hilo**, haga clic en **Vínculos rápidos**y, a continuación, haga clic en el Administrador de recursos activa y, a continuación, haga clic en **La interfaz de usuario de ResourceManager**.

## <a name="what-is-the-optimum-cluster-configuration-to-run-spark-applications"></a>¿Qué es la configuración de clúster óptima para ejecutar aplicaciones de motor?

Los tres parámetros de clave que se pueden usar para la configuración de motor según los requisitos de la aplicación son `spark.executor.instances`, `spark.executor.cores`, y `spark.executor.memory`. Un elemento de ejecución es un proceso iniciado para una aplicación de motor. Se ejecuta en el nodo de trabajo y es responsable de llevar a cabo las tareas de la aplicación. El número predeterminado de los elementos de ejecución y el tamaño de elemento de ejecución de cada clúster se calcula en función del número de nodos de trabajo y el tamaño de nodo de trabajo. Se almacenan en `spark-defaults.conf` en los nodos de clúster central. 

Los parámetros de configuración de tres se pueden configurar en el nivel de clúster (para todas las aplicaciones que se ejecutan en el clúster) o se pueden especificar para cada aplicación también.

### <a name="change-the-parameters-using-ambari-ui"></a>Cambiar los parámetros mediante Ambari UI

1. Desde la UI Ambari haga clic en **motor**, haga clic en **configuración**y, a continuación, expanda **valores predeterminados personalizados motor**.

    ![Establecer parámetros con Ambari](./media/hdinsight-apache-spark-resource-manager/set-parameters-using-ambari.png)

2. Los valores predeterminados son buenos tener 4 motor aplicaciones ejecutarán simultáneamente en el clúster. Puede cambios estos valores que aparecen en la interfaz de usuario, como se muestra a continuación.

    ![Establecer parámetros con Ambari](./media/hdinsight-apache-spark-resource-manager/set-executor-parameters.png)

3. Haga clic en **Guardar** para guardar los cambios de configuración. En la parte superior de la página, se le pedirá que reinicie todos los servicios afectados. Haga clic en **reiniciar**.

    ![Reiniciar servicios](./media/hdinsight-apache-spark-resource-manager/restart-services.png)


### <a name="change-the-parameters-for-an-application-running-in-jupyter-notebook"></a>Cambiar los parámetros para una aplicación que se ejecuta en el Bloc de notas de Jupyter

Para las aplicaciones que se ejecutan en el Bloc de notas de Jupyter, puede utilizar el `%%configure` mágico para realizar los cambios de configuración. Lo ideal es que, debe realizar estos cambios al principio de la aplicación antes de ejecutar la primera celda de código. Esto garantiza que la configuración se aplica a la sesión de Livio, cuando se crea. Si desea cambiar la configuración posteriormente en la aplicación, debe utilizar el `-f` parámetro. Sin embargo, al hacerlo se perderán todos los progresos en la aplicación.

El fragmento de código siguiente muestra cómo cambiar la configuración de una aplicación que se ejecuta en Jupyter.

    %%configure 
    {"executorMemory": "3072M", "executorCores": 4, “numExecutors”:10}

Parámetros de configuración se deben pasar como una cadena JSON y deben estar en la siguiente línea tras la magia, tal como se muestra en la columna de ejemplo. 

### <a name="change-the-parameters-for-an-application-submitted-using-spark-submit"></a>Enviar de motor de los parámetros para una aplicación que se envía mediante el cambio

Siguiente comando es un ejemplo de cómo cambiar los parámetros de configuración para una aplicación de lote que se envía mediante `spark-submit`.

    spark-submit --class <the application class to execute> --executor-memory 3072M --executor-cores 4 –-num-executors 10 <location of application jar file> <application parameters>

### <a name="change-the-parameters-for-an-application-submitted-using-curl"></a>Cambiar los parámetros para una aplicación que se ha enviado con rizo

Siguiente comando es un ejemplo de cómo cambiar los parámetros de configuración para una aplicación de lote que se envía con doblez.

    curl -k -v -H 'Content-Type: application/json' -X POST -d '{"file":"<location of application jar file>", "className":"<the application class to execute>", "args":[<application parameters>], "numExecutors":10, "executorMemory":"2G", "executorCores":5' localhost:8998/batches

### <a name="how-do-i-change-these-parameters-on-a-spark-thrift-server"></a>¿Cómo puedo cambiar estos parámetros en un servidor de Thrift motor?

Motor Thrift Server proporciona acceso JDBC/ODBC a un clúster de motor y se usa para las consultas de SQL de motor de servicio. Herramientas como etcetera una plantilla de Power BI. Usar protocolo ODBC para comunicarse con el servidor de Thrift motor para ejecutar consultas de SQL de motor como una aplicación de motor. Cuando se crea un clúster de motor, se inician dos instancias del servidor de Thrift motor, uno en cada nodo principal. Cada servidor de Thrift motor está visible como una aplicación de motor en la interfaz de usuario del hilo. 

Motor Thrift Server utiliza la asignación de un elemento de ejecución dinámica de motor y por tanto la `spark.executor.instances` no se utiliza. En su lugar, usa el motor Thrift Server `spark.dynamicAllocation.minExecutors` y `spark.dynamicAllocation.maxExecutors` para especificar el número de elemento de ejecución. Los parámetros de configuración `spark.executor.cores` y `spark.executor.memory` se usa para modificar el tamaño del elemento de ejecución. Puede cambiar estos parámetros como se muestra a continuación.

* Expanda la categoría **avanzada motor-thrift-sparkconf** para actualizar los parámetros `spark.dynamicAllocation.minExecutors`, `spark.dynamicAllocation.maxExecutors`, y `spark.executor.memory`.

    ![Configurar el servidor de thrift motor](./media/hdinsight-apache-spark-resource-manager/spark-thrift-server-1.png) 

* Expanda la categoría **personalizada motor-thrift-sparkconf** para actualizar el parámetro `spark.executor.cores`.

    ![Configurar el servidor de thrift motor](./media/hdinsight-apache-spark-resource-manager/spark-thrift-server-2.png)

### <a name="how-do-i-change-the-driver-memory-of-the-spark-thrift-server"></a>¿Cómo puedo cambiar la memoria controlador del servidor de Thrift motor?

Memoria del controlador de motor Thrift Server está configurado para 25% del tamaño de RAM nodo principal, siempre que el tamaño total de memoria RAM del nodo principal es mayor que 14GB. Puede usar la UI Ambari para cambiar la configuración de memoria del controlador, como se muestra a continuación.

* Desde la UI Ambari haga clic en el **motor**, haga clic en **configuración**, expanda **avanzado motor ent**y, a continuación, proporcionar el valor de **spark_thrift_cmd_opts**.

    ![Configurar el servidor de thrift motor de RAM](./media/hdinsight-apache-spark-resource-manager/spark-thrift-server-ram.png)

## <a name="i-do-not-use-bi-with-spark-cluster-how-do-i-take-the-resources-back"></a>No puedo usar BI con clúster de motor. ¿Cómo obtengo los recursos volver?

Dado que usamos asignación dinámica de motor, los únicos recursos que consumen server thrift son los recursos para los patrones de las dos aplicaciones. Para recuperar estos recursos debe detener los servicios de Thrift Server que se ejecutan en el clúster.

1. Desde la UI Ambari, en el panel izquierdo, haga clic en el **motor**.

2. En la siguiente página, haga clic en **Servidores de Thrift motor**.

    ![Reinicie el servidor thrift](./media/hdinsight-apache-spark-resource-manager/restart-thrift-server-1.png)

3. Debería ver los dos headnodes en el que se está ejecutando el servidor de Thrift motor. Haga clic en uno de los headnodes.

    ![Reinicie el servidor thrift](./media/hdinsight-apache-spark-resource-manager/restart-thrift-server-2.png)

4. La siguiente página enumera todos los servicios que se ejecutan en ese headnode. En la lista haga clic en el botón desplegable junto al motor Thrift servidor y, a continuación, haga clic en **Detener**.

    ![Reinicie el servidor thrift](./media/hdinsight-apache-spark-resource-manager/restart-thrift-server-3.png)

5. Repita estos pasos en el otro headnode.


## <a name="my-jupyter-notebooks-are-not-running-as-expected-how-can-i-restart-the-service"></a>Mis blocs de notas de Jupyter no se ejecutan según lo esperado. ¿Cómo puedo reiniciar el servicio?

1. Inicie la interfaz de usuario de Web Ambari como se indicó anteriormente. Desde el panel de navegación izquierdo, haga clic en **Jupyter**, haga clic en **Acciones de servicio**y, a continuación, haga clic en **Reiniciar todo**. Se iniciará el servicio Jupyter en todo el headnodes.

    ![Reinicie Jupyter] (./media/hdinsight-apache-spark-resource-manager/restart-jupyter.png "Reinicie Jupyter")

    


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

* [Realizar el seguimiento y depuración trabajos que se ejecutan en un clúster de motor de Apache en HDInsight](hdinsight-apache-spark-job-debugging.md)



[hdinsight-versions]: hdinsight-component-versioning.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md


[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[azure-management-portal]: https://manage.windowsazure.com/
[azure-create-storageaccount]: storage-create-storage-account.md 
