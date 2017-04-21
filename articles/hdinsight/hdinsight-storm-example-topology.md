<properties
 pageTitle="Topologías de tormenta Apache de ejemplo en HDInsight | Microsoft Azure"
 description="Una lista de topologías de tormenta de ejemplo creado y probado con Apache tormenta en HDInsight incluidos topologías básicas de C# y Java y trabajar con Hubs de evento."
 services="hdinsight"
 documentationCenter=""
 authors="Blackmist"
 manager="jhubbard"
 editor="cgronlun"
    tags="azure-portal"/>

<tags
 ms.service="hdinsight"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="big-data"
 ms.date="08/23/2016"
 ms.author="larryfr"/>

# <a name="example-storm-toplogies-and-components-for-apache-storm-on-hdinsight"></a>Ejemplo tormenta toplogies y componentes de tormenta Apache en HDInsight

A continuación se muestra una lista de ejemplos crean y mantienen por Microsoft para su uso con Apache tormenta en HDInsight. Estos ejemplos abarcan una variedad de temas, desde la creación básica C# y topologías Java para trabajar con los servicios de Azure como evento Hubs, DocumentDB, Power BI, base de datos de SQL, HBase en HDInsight y el almacenamiento de Azure. Algunos ejemplos también muestra cómo trabajar con tecnologías de Azure no o incluso no son de Microsoft, como SignalR y Socket.IO

| Descripción                                                                                             | Se muestra                                         | Idioma/Framework         |
|:--------------------------------------------------------------------------------------------------------|:-----------------------------------------------------|:---------------------------|
| [Escribir en el almacén de lago de datos de Azure desde Apache tormenta](hdinsight-storm-write-data-lake-store.md) | Escribir en el almacén de lago de datos de Azure | Java |
| [Origen de evento concentrador chorros y rayo](https://github.com/apache/storm/tree/master/external/storm-eventhubs) | Origen de para el evento concentrador pitorro y rayo | Java |
| [Desarrollar topologías basada en Java para tormenta Apache en HDInsight][5797064f]                                 | Experto                                                | Java                       |
| [Desarrollar C# topologías para tormenta Apache en HDInsight con Visual Studio][16fce2d1]                     | HDInsight Tools para Visual Studio                    | C#, Java                   |
| [Crear varias secuencias de datos en una topología de tormenta C#][ec5a4064]                                         | Varias secuencias                                     | C#                         |
| [Determinar temas de tendencias de Twitter con tormenta en HDInsight][3c86c7c8]                                   | Trident                                              | Java, Trident              |
| [Procesar los eventos de Azure evento Hubs con tormenta en HDInsight (C#)][844d1d81]                                | Evento Hubs                                           | C# y Java                |
| [Procesar los eventos de Azure evento Hubs con tormenta en HDInsight (Java)](hdinsight-storm-develop-java-event-hub-topology.md) | Evento Hubs | Java |
| [Usar Power Bi para visualizar datos de una topología de tormenta][94d15238]                              | Power BI                                             | C#                         |
| [Analizar los datos de sensor con tormenta y HBase en HDInsight][ab894747]                                     | Evento Hubs, HBase, Socket.IO, panel Web          | C#, Java, JavaScript, HTML |
| [Procesar datos de sensor de vehículo de Hubs de evento con tormenta en HDInsight][246ee964]                        | Evento Hubs, DocumentDb, almacenamiento Azure Blob (WASB)    | C#, Java                   |
| [Extracción, transformación y carga (ETL) de Azure evento Hubs a HBase, con tormenta en HDInsight][b4b68194] | Evento Hubs, HBase                                    | C#                         |
| [Proyecto de topología de C# tormenta de plantilla para trabajar con los servicios de tormenta en HDInsight de Azure][ce0c02a2]  | Evento Hubs, DocumentDb, SQL base de datos, HBase, SignalR | C#, Java                   |
| [Referencia de escalabilidad para la lectura de Azure evento Hubs con tormenta en HDInsight][d6c540e3]           | Flujo de mensajes, eventos Hubs, base de datos SQL         | C#, Java                   |
| [Relacionar con tormenta y HBase en HDInsight de eventos](hdinsight-storm-correlation-topology.md) | HBase | C# |
| [Utilizar Python con tormenta en HDInsight](hdinsight-storm-develop-python-topology.md) | Componentes de Python con Java y Clojure según topologías de tormenta | Python |

## <a name="next-steps"></a>Pasos siguientes

* [Introducción a tormenta Apache en HDInsight][2b8c3488]

* [Aprenda a implementar y administrar topologías de tormenta con tormenta en HDInsight][6eb0d3b8]

  [2b8c3488]: hdinsight-apache-storm-tutorial-get-started-linux.md "Obtenga información sobre cómo crear una tormenta en clúster HDInsight y use el panel de tormenta para implementar topologías de ejemplo."
  [6eb0d3b8]: hdinsight-storm-deploy-monitor-topology.md "Aprenda a implementar y administrar topologías mediante el panel de tormenta basados en web y la interfaz de usuario de tormenta o las herramientas de HDInsight para Visual Studio."
  [16fce2d1]: hdinsight-storm-develop-csharp-visual-studio-topology.md "Aprenda a crear topologías C# tormenta con las herramientas de HDInsight para Visual Studio."
  [5797064f]: hdinsight-storm-develop-java-topology.md "Aprenda a crear topologías de tormenta en Java, con Maven, mediante la creación de una topología de wordcount básica."
  [94d15238]: hdinsight-storm-power-bi-topology.md "Muestra cómo escribir datos en Power BI desde una topología de C#, a continuación, crear un gráfico y el panel de los datos."
  [ec5a4064]: https://github.com/Blackmist/csharp-storm-example "Se muestra una topología de tormenta básica que realiza una wordcount, implementado en C#. También se muestra cómo crear varias secuencias de datos dentro de una topología de C#."
  [844d1d81]: hdinsight-storm-develop-csharp-event-hub-topology.md "Aprenda a leer y escribir datos de Azure evento Hubs con tormenta en HDInsight."
  [ab894747]: hdinsight-storm-sensor-data-analysis.md "Aprenda a usar tormenta Apache en HDInsight para procesar datos de sensor de Azure evento Hubs, visualizar mediante D3.js y (opcionalmente) se almacenan en HBase."
  [3c86c7c8]: hdinsight-storm-twitter-trending.md "Obtenga información sobre cómo usar Trident para crear una topología de tormenta que determina tendencias temas (según hashtags,) en Twitter."
  [246ee964]: hdinsight-storm-iot-eventhub-documentdb.md "Obtenga información sobre cómo utilizar una topología de tormenta para leer mensajes de Azure evento Hubs, leer documentos de Azure DocumentDB para hacer referencia a datos y guardar datos con el almacenamiento de Azure."
  [d6c540e3]: https://github.com/hdinsight/hdinsight-storm-examples/blob/master/EventCountExample "Varias topologías para mostrar el rendimiento al leer Hubs de evento de Azure y almacenamiento de base de datos de SQL con Apache tormenta en HDInsight."
  [b4b68194]: https://github.com/hdinsight/hdinsight-storm-examples/blob/master/RealTimeETLExample "Aprenda a leer los datos de Azure evento Hubs, agregado y transformar los datos y, a continuación, almacenar a HBase en HDInsight."
  [ce0c02a2]: https://github.com/hdinsight/hdinsight-storm-examples/tree/master/templates/HDInsightStormExamples "Este proyecto contiene plantillas para spouts, pernos y topologías para interactuar con diversos servicios de Azure como evento Hubs, DocumentDB y base de datos de SQL."
 
