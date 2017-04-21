<properties
 pageTitle="Procesar datos de sensor vehículo con Apache tormenta en HDInsight | Microsoft Azure"
 description="Aprenda a procesar datos de sensor de vehículo de Hubs de evento con Apache tormenta en HDInsight. Agregar datos de modelo de DocumentDB y almacenar los resultados de almacenamiento."
 services="hdinsight,documentdb,notification-hubs"
 documentationCenter=""
 authors="Blackmist"
 manager="jhubbard"
 editor="cgronlun"/>

<tags
ms.service="hdinsight"
ms.devlang="java"
ms.topic="article"
ms.tgt_pltfrm="na"
ms.workload="big-data"
ms.date="08/23/2016"
ms.author="larryfr"/>

#<a name="process-vehicle-sensor-data-from-azure-event-hubs-using-apache-storm-on-hdinsight"></a>Procesar datos de sensor de vehículo de Azure evento Hubs con Apache tormenta en HDInsight

Aprenda a procesar datos de sensor de vehículo de Azure evento Hubs con Apache tormenta en HDInsight. Este ejemplo lee los datos de sensor de Azure evento Hubs, enriquece los datos mediante una referencia a datos almacenados en Azure DocumentDB y finalmente almacena los datos en el almacenamiento de Azure mediante el sistema de archivo de Hadoop (HDFS).

![HDInsight y el diagrama de arquitectura de Internet de cosas (IoT)](./media/hdinsight-storm-iot-eventhub-documentdb/iot.png)

##<a name="overview"></a>Información general

Agregar sensores a vehículos permite predecir problemas de los equipos en función de las tendencias de datos históricos, así como para realizar mejoras en futuras versiones en función de análisis de uso de la trama. Mientras el procesamiento por lotes de MapReduce tradicional puede utilizarse para este análisis, debe poder rápida y eficaz cargar los datos de todos los vehículos en Hadoop antes de procesamiento de MapReduce se puede producir. Además, puede que desee realizar análisis de las rutas de acceso de error crítico (temperatura motor, frenos, etc.) en tiempo real.

Se generan Azure Hubs de evento para controlar el volumen masivo de datos generados por sensores y tormenta Apache en HDInsight puede utilizarse para cargar y procesar los datos antes de almacenarlos en HDFS (copia por almacenamiento de Azure) para el procesamiento de MapReduce adicional.

##<a name="solution"></a>Solución

Datos de telemetría de temperatura del motor, la temperatura y la velocidad de un vehículo están registrados sensores, a continuación, se envía a evento Hubs junto con una marca de tiempo y el número de identificación de vehículo (bastidor del automóvil). Desde allí, una topología de tormenta ejecutando en una tormenta Apache en clúster HDInsight lee los datos, lo procesa y lo almacena en HDFS.

Durante el proceso, el número de bastidor se usa para recuperar información sobre el modelo de DocumentDB de Azure. Esto se agrega a la secuencia de datos antes de que se almacena.

Los componentes utilizados en la topología de tormenta son:

* **EventHubSpout** - lee los datos de Azure evento Hubs

* **TypeConversionBolt** - convierte la cadena JSON de Hubs de evento en una tupla que contiene los valores de datos individuales de temperatura motor, temperatura, velocidad, bastidor y marca de tiempo

* Busca el modelo de vehículo **DataReferencBolt** - desde DocumentDB con el número de bastidor

* **WasbStoreBolt** - almacena los datos en HDFS (almacenamiento de Azure)

El siguiente es un diagrama de esta solución:

![topología de tormenta](./media/hdinsight-storm-iot-eventhub-documentdb/iottopology.png)

> [AZURE.NOTE] Este es un diagrama simplificado y en la solución, cada componente puede tener varias instancias. Por ejemplo, la varias instancias de cada componente en la topología se distribuyen entre los nodos de la tormenta en clúster HDInsight.

##<a name="implementation"></a>Implementación

Completado, automatizado solución para este escenario está disponible como parte del repositorio de [Ejemplos de tormenta HDInsight](https://github.com/hdinsight/hdinsight-storm-examples) en GitHub. Para utilizar este ejemplo, siga los pasos de la [IoTExample README. MD](https://github.com/hdinsight/hdinsight-storm-examples/blob/master/IotExample/README.md).

## <a name="next-steps"></a>Pasos siguientes

Para obtener más topologías de tormenta de ejemplo, vea [topologías de ejemplo para tormenta en HDInsight](hdinsight-storm-example-topology.md).
