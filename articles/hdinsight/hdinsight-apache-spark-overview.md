<properties 
    pageTitle="Información general sobre el motor de Apache en HDInsight | Microsoft Azure" 
    description="Introducción al motor de Apache en HDInsight y escenarios en los que se va a utilizar el motor en HDInsight en las aplicaciones." 
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
    ms.date="08/25/2016" 
    ms.author="nitinme"/>

# <a name="overview-apache-spark-on-hdinsight-linux"></a>Información general: Apache motor en HDInsight Linux
 
<a href="http://spark.apache.org/" target="_blank">Motor de Apache</a> es un paralelo Abrir origen procesamiento framework que admita el procesamiento en memoria para mejorar el rendimiento de aplicaciones de análisis de datos grande. Motor de procesamiento de motor integrado para velocidad, facilidad de uso y análisis sofisticados. Capacidades de cálculo en la memoria del motor que sea una buena opción para algoritmos iterativos en cálculos de aprendizaje y gráfico de equipo. Motor también es compatible con el almacenamiento de blobs de Windows Azure (WASB) para procesar fácilmente los datos existentes almacenados en Azure a través de motor.

Cuando se crea un clúster de motor en HDInsight, crear recursos de cálculo Azure con motor instalado y configurado. Sólo tardará unos diez minutos para crear un clúster de motor en HDInsight. Los datos que deben procesarse se almacenan en el almacenamiento de blobs de Windows Azure. Ver el [uso de almacenamiento de blobs de Windows Azure con HDInsight][hdinsight-storage].

![Motor de Apache en HDInsight de Azure] (./media/hdinsight-apache-spark-overview/hdispark.architecture.png  "Motor de Apache en HDInsight de Azure")


**¿Desea empezar a trabajar con motor Apache en HDInsight de Azure?** Consulte [Tutorial: crear un clúster de motor en HDInsight Linux y ejecutar aplicaciones de ejemplo con Jupyter](hdinsight-apache-spark-jupyter-spark-sql.md).

>[AZURE.NOTE] Para obtener una lista de problemas conocidos y limitaciones de la versión actual, consulte [problemas de motor de Apache en Azure HDInsight (Linux)](hdinsight-apache-spark-jupyter-spark-sql.md).


## <a name="why-use-spark-on-azure-hdinsight"></a>¿Por qué usar motor en HDInsight de Azure? 

HDInsight de Azure ofrece un servicio motor totalmente gestionado. Ventajas del uso de motor en HDInsight son:

| Característica                             | Descripción       |
|-------------------------------------|-------------------|
| Facilitar la creación de clústeres            | Puede crear un nuevo clúster de motor en HDInsight en minutos con el Portal de administración de Azure, Azure PowerShell o el SDK de .NET HDInsight. Consulte [Introducción a clúster motor en HDInsight](hdinsight-apache-spark-jupyter-spark-sql.md) |
| Facilidad de uso                     | Motor de HDInsight clústeres incluye los blocs de notas Jupyter preconfiguradas. Puede utilizar estos interactiva tratamiento de datos y visualización. Las direcciones URL para la es https://CLUSTERNAME.azurehdinsight.net/jupyter. Reemplazar __NOMBREDECLÚSTER__ con el nombre de su clúster de motor HDInsight.|
| API REST                       | Motor de HDInsight incluye [Livio](https://github.com/cloudera/hue/tree/master/apps/spark/java#welcome-to-livy-the-rest-spark-server), una API de REST basado en servidor de trabajo de motor para enviar de forma remota y supervisar la ejecución de trabajos. |
| Soporte técnico para lago almacén de datos de Azure | Motor de HDInsight se puede configurar para usar el almacén de lago de datos de Azure como un almacenamiento adicional. Para obtener más información sobre el almacén de lago de datos, vea [Información general de Azure lago almacén de datos](../data-lake-store/data-lake-store-overview.md).
| Integración con servicios de Azure | Motor de HDInsight incluye un conector a Azure evento Hubs. Los clientes pueden crear aplicaciones streaming con los centros de evento, además de [Kafka](http://kafka.apache.org/), que ya está disponible como parte del motor. |
| Compatibilidad con Server R  | Puede configurar un servidor R en clúster HDInsight Spark ejecutar cálculos R distribuidos con las velocidades prometidas con un clúster de motor. Para obtener más información, vea [Introducción al uso de servidor de R en HDInsight](hdinsight-hadoop-r-server-get-started.md).   |
| Integración con IntelliJ IDEA  | Puede usar el HDInsight Plugin para IntelliJ para crear y enviar aplicaciones a un clúster HDInsight Spark. Para obtener más información, vea [Usar complemento de herramientas HDInsight para IntelliJ IDEA crear motor aplicaciones para clúster HDInsight motor Linux](hdinsight-apache-spark-intellij-tool-plugin.md). |
| Consultas simultáneas              | Motor de HDInsight es compatible con consultas simultáneas. Esto permite varias consultas de un usuario o de varias consultas de varios usuarios y aplicaciones para compartir los mismos recursos de clúster. |
| Almacenamiento en caché en SSDs                 | Puede elegir en caché los datos en la memoria o en SSDs adjuntados a los nodos del clúster. Almacenamiento en caché en la memoria proporciona el mejor rendimiento de la consulta, pero puede ser muy costosa; almacenamiento en caché en SSDs proporciona una buena opción para mejorar el rendimiento de la consulta sin necesidad de crear un clúster de un tamaño que es necesario para ajustar todo el conjunto de datos en la memoria.|
| Integración con herramientas de inteligencia empresarial       | Motor de HDInsight proporciona conectores de las herramientas de BI como [Power BI](http://www.powerbi.com/) y [una plantilla](http://www.tableau.com/products/desktop) para el análisis de datos.|
| Bibliotecas de Anaconda previamente cargadas        | Motor clústeres en HDInsight vienen con bibliotecas Anaconda preinstaladas. [Anaconda](http://docs.continuum.io/anaconda/) proporciona cerca de las bibliotecas de 200 para el aprendizaje, análisis de datos, visualización, etcetera.|
| Escalabilidad                     | Aunque puede especificar el número de nodos en el clúster durante la creación, es aconsejable aumentar o reducir el clúster para que coincida con la carga de trabajo. Todos los clústeres HDInsight le permiten cambiar el número de nodos en el clúster. Además, se pueden quitar clústeres motor sin pérdida de datos como todos los datos se almacena en el almacenamiento de blobs de Windows Azure. |
| Asistencia 24/7                    | Motor de HDInsight incluye soporte técnico 24/7 de nivel empresarial y el SLA de 99,9% de tiempo.|



## <a name="what-are-the-use-cases-for-spark-on-hdinsight"></a>¿Cuáles son los casos de uso para el motor en HDInsight?

Motor de Apache en HDInsight permite los siguientes escenarios clave.

### <a name="interactive-data-analysis-and-bi"></a>Análisis de datos interactivos y BI

[Ver un tutorial](hdinsight-apache-spark-use-bi-tools.md)

Motor de Apache en HDInsight almacena los datos en Azure BLOB. Expertos empresariales y decisiones clave puede analizar y generar informes sobre esos datos y usar Microsoft Power BI para generar informes interactivos de los datos analizados. Los analistas pueden iniciar de datos estructurados y semiestructurados en el almacenamiento de Azure, definir un esquema de los datos utilizando los blocs de notas y, a continuación, generar modelos de datos mediante Microsoft Power BI. Motor de HDInsight también es compatible con un número de herramientas de BI de terceros como una plantilla, Qlikview y Lumira de SAP que hace una plataforma ideal para los analistas de datos, expertos empresariales y decisiones clave.

### <a name="iterative-machine-learning"></a>Aprendizaje de máquina iterativo

[Ver un tutorial: predecir creación temperaturas usar datos HVAC](hdinsight-apache-spark-ipython-notebook-machine-learning.md)

[Ver un tutorial: predecir los resultados de la inspección de comida](hdinsight-apache-spark-machine-learning-mllib-ipython.md)

Motor de Apache incluye [MLlib](http://spark.apache.org/mllib/), un equipo biblioteca creado a partir de motor de aprendizaje. Además, el motor en HDInsight también incluye Anaconda, una distribución de Python con una amplia variedad de paquetes para el aprendizaje. Acoplar esto con una compatibilidad integrada para los blocs de notas Jupyter y tiene un entorno de línea para la creación de aplicaciones de aprendizaje del equipo.  

### <a name="streaming-and-real-time-data-analysis"></a>Análisis de datos en tiempo real y Streaming

[Ver un tutorial](hdinsight-apache-spark-eventhub-streaming.md)

Análisis de datos en tiempo real se usan para escenarios comprendido entre reducir el tiempo de comprensión de datos mediante el procesamiento de datos mientras lleva a la creación de un verdadero transmisión solución. Motor de HDInsight ofrece una compatibilidad para crear soluciones de análisis en tiempo real. Mientras el motor ya tiene conectores a recopilar datos desde varios orígenes como Kafka, Flume, Twitter, ZeroMQ o TCP sockets, motor en HDInsight agrega soporte de primera clase para recopilar datos de Azure evento Hubs. Evento Hubs son el servicio de cola utilizado con más frecuencia en Azure. ¿Tiene un soporte para Hubs de evento ha despierte el hace en HDInsight una plataforma ideal para crear la canalización de análisis en tiempo real.

##<a name="next-steps"></a>¿Qué componentes se incluye como parte de un clúster de motor?

Motor de HDInsight incluye los siguientes componentes que están disponibles en los clústeres de forma predeterminada.

- [Motor principal](https://spark.apache.org/docs/1.5.1/). Incluye principales de motor, motor SQL, motor transmisión API, GraphX y MLlib.
- [Anaconda](http://docs.continuum.io/anaconda/)
- [Livio](https://github.com/cloudera/hue/tree/master/apps/spark/java#welcome-to-livy-the-rest-spark-server)
- [Bloc de notas de Jupyter](https://jupyter.org)

Motor de HDInsight también proporciona un [controlador ODBC](http://go.microsoft.com/fwlink/?LinkId=616229) para conectividad a clústeres motor en HDInsight de las herramientas de inteligencia empresarial como Microsoft Power BI y una plantilla.

## <a name="where-do-i-start"></a>¿Dónde empezar?

Empezar con la creación de un clúster de motor en HDInsight Linux. Consulte [Tutorial: crear un clúster de motor en HDInsight Linux y ejecutar aplicaciones de ejemplo con Jupyter](hdinsight-apache-spark-jupyter-spark-sql.md). 

## <a name="next-steps"></a>Pasos siguientes

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

* [Realizar el seguimiento y depuración trabajos que se ejecutan en un clúster de motor de Apache en HDInsight](hdinsight-apache-spark-job-debugging.md)


[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md
