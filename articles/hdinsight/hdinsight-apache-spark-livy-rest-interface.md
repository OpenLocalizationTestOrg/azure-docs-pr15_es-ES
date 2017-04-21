<properties
    pageTitle="Enviar trabajos de motor remota mediante Livio | Microsoft Azure"
    description="Obtenga información sobre cómo usar a Livio con HDInsight clústeres para enviar trabajos de motor de forma remota."
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


# <a name="submit-spark-jobs-remotely-to-an-apache-spark-cluster-on-hdinsight-linux-using-livy"></a>Enviar trabajos de motor remotamente a un clúster de motor de Apache en HDInsight Linux con Livio

Clúster de motor de Apache en HDInsight de Azure incluye a Livio, una interfaz REST para enviar trabajos de forma remota a un clúster de motor. Para obtener documentación detallada, vea [Livio](https://github.com/cloudera/hue/tree/master/apps/spark/java#welcome-to-livy-the-rest-spark-server).

Puede usar a Livio para ejecutar el motor interactiva depósitos o enviar trabajos por lotes para ejecutarse en el motor. En este artículo se habla sobre el uso de Livio enviar trabajos por lotes. La sintaxis siguiente usa doblez para realizar llamadas de resto al extremo Livio.

**Requisitos previos:**

Debe tener el siguiente:

- Una suscripción de Azure. Vea la [versión de prueba gratuita de Azure obtener](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
- Un clúster de motor de Apache en HDInsight Linux. Para obtener instrucciones, vea [crear Apache motor clústeres en HDInsight de Azure](hdinsight-apache-spark-jupyter-spark-sql.md).

## <a name="submit-a-batch-job-the-cluster"></a>Enviar un trabajo por lotes del clúster

Antes de enviar un trabajo por lotes, deberá cargar el tarro de aplicación en el almacenamiento de clúster asociado con el clúster. Puede usar [**AzCopy**](../storage/storage-use-azcopy.md), una utilidad de la línea de comandos para hacerlo. Hay una gran cantidad de otros clientes que puede usar para cargar los datos. Puede encontrar más información acerca de ellos al [cargar los datos para trabajos de Hadoop en HDInsight](hdinsight-upload-data.md).

    curl -k --user "<hdinsight user>:<user password>" -v -H <content-type> -X POST -d '{ "file":"<path to application jar>", "className":"<classname in jar>" }' 'https://<spark_cluster_name>.azurehdinsight.net/livy/batches'

**Ejemplos**:

* Si el archivo jar está en el almacenamiento de clúster (WASB)

        curl -k --user "admin:mypassword1!" -v -H 'Content-Type: application/json' -X POST -d '{ "file":"wasbs://mycontainer@mystorageaccount.blob.core.windows.net/data/SparkSimpleTest.jar", "className":"com.microsoft.spark.test.SimpleFile" }' "https://mysparkcluster.azurehdinsight.net/livy/batches"

* Si la que desea pasar el nombre de archivo jar y el nombre de clase como parte de un archivo de entrada (en este ejemplo, entrada.txt)

        curl -k  --user "admin:mypassword1!" -v -H "Content-Type: application/json" -X POST --data @C:\Temp\input.txt "https://mysparkcluster.azurehdinsight.net/livy/batches"

## <a name="get-information-on-batches-running-on-the-cluster"></a>Obtenga información sobre los lotes que se ejecutan en el clúster

    curl -k --user "<hdinsight user>:<user password>" -v -X GET "https://<spark_cluster_name>.azurehdinsight.net/livy/batches"

**Ejemplos**:

* Si desea recuperar todas las secciones que se ejecutan en el clúster:

        curl -k --user "admin:mypassword1!" -v -X GET "https://mysparkcluster.azurehdinsight.net/livy/batches"

* Si desea recuperar un lote específico con un determinado batchId

        curl -k --user "admin:mypassword1!" -v -X GET "https://mysparkcluster.azurehdinsight.net/livy/batches/{batchId}"


## <a name="delete-a-batch-job"></a>Eliminar un trabajo por lotes

    curl -k --user "<hdinsight user>:<user password>" -v -X DELETE "https://<spark_cluster_name>.azurehdinsight.net/livy/batches/{batchId}"

**Ejemplo**:

    curl -k --user "admin:mypassword1!" -v -X DELETE "https://mysparkcluster.azurehdinsight.net/livy/batches/{batchId}"

## <a name="livy-and-high-availability"></a>Livio y alta disponibilidad

Livio proporciona alta disponibilidad para motor trabajos se ejecutan en el clúster. Estos son algunos ejemplos.

* Si el servicio Livio deja de funcionar después de que ha enviado un trabajo de forma remota a un clúster de motor, la tarea continúa funcionando en segundo plano. Una vez Livio copia de seguridad, restaura el estado del trabajo y lo vuelve a los informes.

* Blocs de notas de Jupyter para HDInsight se ofrecido por Livio en el servidor. Si un bloc de notas está ejecutando un trabajo motor y obtiene reiniciar el servicio de Livio, seguirá el Bloc de notas ejecutar las celdas de código. 

## <a name="show-me-an-example"></a>Mostrar un ejemplo

En esta sección, veremos ejemplos sobre cómo usar a Livio para enviar una solicitud de motor, supervisar el progreso de la aplicación y, a continuación, elimine el trabajo. La aplicación que se utilizan en este ejemplo es el desarrollado en el artículo [crear una aplicación Scala independiente y ejecutar en el clúster de motor de HDInsight](hdinsight-apache-spark-create-standalone-application.md). Los pasos siguientes suponen lo siguiente:

* Ya ha copiado sobre el tarro de aplicación a la cuenta de almacenamiento asociada con el clúster.
* Tiene doblez instalada en el equipo donde está intentando estos pasos.

Realice los pasos siguientes.

1. Deje que nosotros primero compruebe que Livio se ejecuta en el clúster. Podemos hacerlo por obtener una lista de la ejecución de lotes. Si es la primera vez que se ejecuta un trabajo mediante Livio, debe devolver cero.

        curl -k --user "admin:mypassword1!" -v -X GET "https://mysparkcluster.azurehdinsight.net/livy/batches"

    Obtendrá un resultado similar al siguiente:

        < HTTP/1.1 200 OK
        < Content-Type: application/json; charset=UTF-8
        < Server: Microsoft-IIS/8.5
        < X-Powered-By: ARR/2.5
        < X-Powered-By: ASP.NET
        < Date: Fri, 20 Nov 2015 23:47:53 GMT
        < Content-Length: 34
        <
        {"from":0,"total":0,"sessions":[]}* Connection #0 to host mysparkcluster.azurehdinsight.net left intact

    Observe cómo la última línea en el resultado indica **total: 0**, lo que no sugiere lotes de ejecución.

2. Deje que nosotros ahora a enviar un trabajo por lotes. El fragmento de código siguiente utiliza un archivo de entrada (entrada.txt) para pasar el nombre de jar y el nombre de la clase como parámetros. Este es el enfoque recomendado si se están ejecutando estos pasos desde un equipo Windows.

        curl -k --user "admin:mypassword1!" -v -H "Content-Type: application/json" -X POST --data @C:\Temp\input.txt "https://mysparkcluster.azurehdinsight.net/livy/batches"

    Los parámetros en el archivo **entrada.txt** se definen como sigue:

        { "file":"wasbs:///example/jars/SparkSimpleApp.jar", "className":"com.microsoft.spark.example.WasbIOTest" }

    Debe ver un resultado similar al siguiente:

        < HTTP/1.1 201 Created
        < Content-Type: application/json; charset=UTF-8
        < Location: /0
        < Server: Microsoft-IIS/8.5
        < X-Powered-By: ARR/2.5
        < X-Powered-By: ASP.NET
        < Date: Fri, 20 Nov 2015 23:51:30 GMT
        < Content-Length: 36
        <
        {"id":0,"state":"starting","log":[]}* Connection #0 to host mysparkcluster.azurehdinsight.net left intact

    Observe cómo la última línea del resultado indica **Estado: iniciar**. También dice, **Id.: 0**. Este es el Id.

3. Ahora se puede recuperar la el estado de este lote específico con el Id.

        curl -k --user "admin:mypassword1!" -v -X GET "https://mysparkcluster.azurehdinsight.net/livy/batches/0"

    Debe ver un resultado similar al siguiente:

        < HTTP/1.1 200 OK
        < Content-Type: application/json; charset=UTF-8
        < Server: Microsoft-IIS/8.5
        < X-Powered-By: ARR/2.5
        < X-Powered-By: ASP.NET
        < Date: Fri, 20 Nov 2015 23:54:42 GMT
        < Content-Length: 509
        <
        {"id":0,"state":"success","log":["\t diagnostics: N/A","\t ApplicationMaster host: 10.0.0.4","\t ApplicationMaster RPC port: 0","\t queue: default","\t start time: 1448063505350","\t final status: SUCCEEDED","\t tracking URL: http://hn0-myspar.lpel1gnnvxne3gwzqkfq5u5uzh.jx.internal.cloudapp.net:8088/proxy/application_1447984474852_0002/","\t user: root","15/11/20 23:52:47 INFO Utils: Shutdown hook called","15/11/20 23:52:47 INFO Utils: Deleting directory /tmp/spark-b72cd2bf-280b-4c57-8ceb-9e3e69ac7d0c"]}* Connection #0 to host mysparkcluster.azurehdinsight.net left intact

    Ahora, el resultado muestra **Estado: éxito**, que se sugiere que el trabajo se ha completado correctamente.

4. Si lo desea, ahora puede eliminar el lote.

        curl -k --user "admin:mypassword1!" -v -X DELETE "https://mysparkcluster.azurehdinsight.net/livy/batches/0"

    Debe ver un resultado similar al siguiente:

        < HTTP/1.1 200 OK
        < Content-Type: application/json; charset=UTF-8
        < Server: Microsoft-IIS/8.5
        < X-Powered-By: ARR/2.5
        < X-Powered-By: ASP.NET
        < Date: Sat, 21 Nov 2015 18:51:54 GMT
        < Content-Length: 17
        <
        {"msg":"deleted"}* Connection #0 to host mysparkcluster.azurehdinsight.net left intact

    La última línea del resultado muestra que el lote se eliminó correctamente. Si elimina un trabajo mientras se está ejecutando, esencialmente elimina el trabajo. Si elimina un trabajo que se ha completado correctamente o en caso contrario, elimina la información del trabajo por completo.

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
