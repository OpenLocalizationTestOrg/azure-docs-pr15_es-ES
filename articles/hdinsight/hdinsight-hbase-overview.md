<properties
    pageTitle="¿Qué es HBase en HDInsight? | Microsoft Azure"
    description="Una introducción a Apache HBase en HDInsight, una base de datos NoSQL generarse Hadoop. Obtenga más información sobre casos de uso y HBase en comparación con otros clústeres Hadoop."
    keywords="BigTable nosql, ¿qué es hbase"
    services="hdinsight"
    documentationCenter=""
    tags="azure-portal"
    authors="mumian" 
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="hdinsight"
    ms.workload="big-data"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="09/14/2016"
    ms.author="jgao"/>



# <a name="what-is-hbase-in-hdinsight-a-nosql-database-that-provides-bigtable-like-capabilities-for-hadoop"></a>¿Qué es HBase en HDInsight: base de datos A NoSQL que ofrece capacidades de BigTable como para Hadoop

Apache HBase es una base de datos de código abierto, NoSQL que está integrado en Hadoop y modelar después Google BigTable. HBase proporciona acceso aleatorio y fuerte coherencia para grandes cantidades de datos estructurados y semistructured en una base de datos schemaless organizado por familias de columna.

Los datos se almacenan en las filas de una tabla y datos dentro de una fila se agrupan por familia de columna. HBase es una base de datos schemaless en el sentido de que las columnas ni el tipo de datos almacenados en ellas deben definirse antes de usarlas. El código abierto lineal escala para manejar petabytes de datos en miles de nodos. Puede confiar en redundancia de datos, proceso por lotes y otras características que se proporcionan en aplicaciones distribuidas en el ecosistema Hadoop.

## <a name="how-is-hbase-implemented-in-azure-hdinsight"></a>¿Cómo se implementa HBase en HDInsight de Azure?

HDInsight HBase se ofrece como un clúster administrado que está integrado en el entorno de Azure. Los clústeres están configurados para almacenar datos directamente en el almacenamiento de blobs de Windows Azure, que proporciona baja latencia y elasticidad mayor en las opciones de rendimiento y de costo. Esto permite a los clientes a crear sitios Web interactivos que funcionan con grandes conjuntos de datos para generar servicios para almacenan los datos de telemetría y sensor de millones de puntos finales y para analizar datos con Hadoop trabajos. HBase y Hadoop son buenos puntos de partida para proyectos de datos grande en Azure; en particular, pueden habilitar aplicaciones en tiempo real trabajar con grandes conjuntos de datos.

La implementación de HDInsight aprovecha la arquitectura de escalado de HBase para proporcionar sharding automática de tablas, fuerte coherencia para lee y escribe y migración tras error automática. Rendimiento se ha mejorado por en la memoria caché para lecturas y transmisión de escritura de alto rendimiento. Aprovisionamiento de red virtual también está disponible para HDInsight HBase. Para obtener más información, consulte [clústeres de HDInsight de aprovisionamiento de red Virtual de Azure] [hbase-provision-vnet].

## <a name="how-is-data-managed-in-hdinsight-hbase"></a>¿La administración de datos en HDInsight HBase?

Datos pueden administrarse en HBase utilizando la `create`, `get`, `put`, y `scan` comandos en la consola de HBase. Datos se escriben en la base de datos usando `put` y leer mediante `get`. La `scan` comando se utiliza para obtener datos de varias filas en una tabla. También es posible administrar los datos con la API de HBase C# que proporciona una biblioteca de cliente encima de la API de REST de HBase. También puede consultar una base de datos HBase mediante el uso de la sección. Para obtener una introducción a los modelos de programación, vea [Introducción al uso de HBase con Hadoop en HDInsight][hbase-get-started]. Procesadores compañeros también están disponibles, que permitir el procesamiento de datos en los nodos que alojan la base de datos.


## <a name="scenarios-use-cases-for-hbase"></a>Escenarios: Casos de uso para HBase
Se creó el caso de uso canónico para qué BigTable (y por extensión, HBase) fue la búsqueda en web. Motores de búsqueda crear índices que asignan términos a las páginas web que contienen. Sin embargo, hay muchos otros casos de uso HBase es adecuado para: algunos de los cuales se detallado de esta sección.

- Valor de clave de la tienda

    HBase se puede utilizar como un almacén de clave de valor y es adecuado para administrar los sistemas de mensaje. Facebook usa HBase para su sistema de mensajería y es ideal para almacenar y administrar las comunicaciones de Internet. WebTable usa HBase para buscar y administrar las tablas que se extraen de páginas Web.

- Datos de sensor

    HBase es útil para capturar los datos que se recopilan incrementalmente de varios orígenes. Esto incluye análisis social de la serie temporal, mantener actualizados contadores y tendencias de los paneles interactivos y administrar los sistemas de registro de auditoría. Por ejemplo, el operador Bloomberg terminal y el tiempo de serie de base de datos abierta (OpenTSDB), que almacena y proporciona acceso a las mediciones recopilada información sobre el estado de los sistemas de servidor.

- Consulta en tiempo real

    [Caracas](http://phoenix.apache.org/) es un motor de consulta SQL para Apache HBase. Obtener acceso a ellas como un controlador JDBC y permite consultas y administrar HBase tablas mediante SQL.

- HBase como una plataforma

    Aplicaciones pueden ejecutar en la parte superior HBase usando como un almacén de datos. Por ejemplo, Caracas, OpenTSDB, Kiji y Titán. También pueden integrar aplicaciones con HBase. Por ejemplo, subárbol, cerdo, Solr, tormenta, Flume, Impala, motor, ganglios y obtención de detalles.


##<a name="next-steps"></a>Pasos siguientes

- [Empezar a usar HBase con Hadoop en HDInsight][hbase-get-started]
- [Aprovisionar clústeres HDInsight de red Virtual de Azure] [hbase-provision-vnet]
- [Configurar la replicación de HBase de HDInsight](hdinsight-hbase-geo-replication.md)
- [Analizar la opinión de Twitter con HBase en HDInsight][hbase-twitter-sentiment]
- [Utilizar a Maven para generar aplicaciones de Java que usan HBase con HDInsight (Hadoop)][hbase-build-java-maven]

##<a name="see-also"></a>Vea también

- [Apache HBase](https://hbase.apache.org/)
- [BigTable: Un sistema de almacenamiento distribuido para datos estructurados](http://research.google.com/archive/bigtable.html)




[hbase-provision-vnet]: hdinsight-hbase-provision-vnet.md

[hbase-twitter-sentiment]: hdinsight-hbase-analyze-twitter-sentiment.md

[hbase-build-java-maven]: hdinsight-hbase-build-java-maven.md

[hdinsight-use-hive]: hdinsight-use-hive.md

[hdinsight-storage]: ../hdinsight-hadoop-use-blob-storage.md

[hbase-get-started]: http://azure.microsoft.com/documentation/articles/hdinsight-hbase-get-started/

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[azure-management-portal]: https://portal.azure.com/
[azure-create-storageaccount]: ../storage-create-storage-account.md

[apache-hadoop]: http://hadoop.apache.org/
