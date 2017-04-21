 <properties
    pageTitle="Introducción a Hadoop - ¿qué es Hadoop en HDInsight? | Microsoft Azure"
    description="Obtener una introducción a Hadoop, distribuido grande procesamiento de datos y análisis y los componentes del ecosistema Hadoop en la nube en HDInsight."
    keywords="Introducción a hadoop, ¿qué es hadoop, pila de tecnología de hadoop, hadoop ecosistema realizar análisis de datos grande"
    services="hdinsight"
    documentationCenter=""
    authors="cjgronlund"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="10/21/2016"
   ms.author="cgronlun"/>


# <a name="an-introduction-to-the-hadoop-ecosystem-on-azure-hdinsight"></a>Introducción al ecosistema Hadoop en HDInsight de Azure

 Este artículo proporciona una introducción a Hadoop en HDInsight de Azure, su ecosistema y datos grandes. Obtenga información sobre los componentes Hadoop, terminología comunes y escenarios para análisis de datos grande.

## <a name="what-is-hadoop-on-hdinsight"></a>¿Qué es Hadoop en HDInsight?

Hadoop hace referencia a un ecosistema de software de código abierto es un marco para procesamiento distribuido, almacenar y análisis de grandes conjuntos de datos en clústeres de equipos. HDInsight de Azure hace que los componentes de Hadoop de la distribución de **Datos de Hortonworks plataforma (HDP)** esté disponible en la nube e implementa y aprovisiona clústeres administrados con disponibilidad y confiabilidad alta.  

Apache Hadoop fue el proyecto de código abierto original para el procesamiento de datos grande. Siguiente fue el desarrollo de software relacionado y herramientas que se consideran parte de la pila de tecnología de Hadoop, incluido subárbol Apache, Apache HBase, motor de Apache y muchas otras. Para obtener más información, vea [información general sobre el ecosistema Hadoop en HDInsight](#overview) .

## <a name="what-is-big-data"></a>¿Qué es grandes datos?

Datos grandes describen cualquier cuerpo grande de información digital, el texto de una fuente, la información de sensor de equipo industrial, para obtener información acerca del cliente de exploración y compras en un sitio Web de Twitter. Datos grandes pueden ser histórica (es decir, los datos almacenados) o en tiempo real (es decir, transmitir directamente desde el origen). Se están recopilando datos grandes en siempre crecientes volúmenes de forma más velocidades y en una expansión diversos formatos.

Para obtener datos grandes para proporcionar inteligencia o ideas, debe recopilar datos pertinentes y las preguntas adecuadas. También debe asegurarse de que los datos son accesibles, limpiar, analizar y, a continuación, se presentan de forma útil. Es donde pueden ayudar los análisis de datos grande en Hadoop en HDInsight.

## <a name="overview"></a>Información general sobre el ecosistema Hadoop en HDInsight

HDInsight es una distribución de nube de Microsoft Azure de la pila de tecnología de Apache Hadoop rápida expansión para análisis de datos grande. Incluye implementaciones de motor de Apache, HBase, tormenta, cerdo, subárbol, Sqoop, Oozie, Ambari y así sucesivamente. HDInsight también se integra con herramientas de business intelligence (BI) como Power BI, Excel, SQL Server Analysis Services y SQL Server Reporting Services.

### <a name="hadoop-hbase-spark-storm-and-customized-clusters"></a>Hadoop, HBase, motor, tormenta y clústeres personalizados

HDInsight proporciona las configuraciones de clúster de Apache Hadoop, motor, HBase o tormenta. O bien, puede [Personalizar clústeres con acciones de script](hdinsight-hadoop-customize-cluster-linux.md).

* **Hadoop**: proporciona almacenamiento de datos fiable con [HDFS](#hdfs)y un modelo de programación [MapReduce](#mapreduce) para procesar y analizar datos en paralelo.

* **<a target="_blank" href="http://spark.apache.org/">Motor de Apache</a>**: un marco de procesamiento en paralelo que admite procesamiento en memoria para mejorar el rendimiento de aplicaciones de análisis de datos grande, despierten works para SQL, transmisión de datos y aprendizaje del equipo. Consulte [información general: ¿qué es el motor de Apache en HDInsight?](hdinsight-apache-spark-overview.md)

* **<a target="_blank" href="http://hbase.apache.org/">HBase</a>**: una base de datos NoSQL integrada en Hadoop que proporciona acceso aleatorio y fuerte coherencia para grandes cantidades de datos estructurados y semiestructurados - potencialmente miles de millones de filas veces millones de columnas. Vea [información general sobre HBase en HDInsight](hdinsight-hbase-overview.md).

* **<a  target="_blank" href="https://storm.incubator.apache.org/">Tormenta Apache</a>**: un sistema de cálculo distribuido y en tiempo real para procesar grandes secuencias de datos rápidamente. Tormenta se ofrece como un clúster administrado en HDInsight. Vea [analizar datos de sensor en tiempo real con tormenta y Hadoop](hdinsight-storm-sensor-data-analysis.md).

### <a name="example-customization-scripts"></a>Secuencias de personalización de ejemplo

Acciones de script son secuencias de comandos que se ejecutan durante el aprovisionamiento de clúster y se pueden usar para instalar componentes adicionales en el clúster. Para clústeres basados en Linux, estos son secuencias de comandos de fiesta.

El equipo de HDInsight proporciona las secuencias de comandos de ejemplo siguientes:

* [Matiz](hdinsight-hadoop-hue-linux.md): conjunto de aplicaciones web que se usan para interactuar con un clúster. Clústeres de Linux.

* [Giraph](hdinsight-hadoop-giraph-install-linux.md): gráfico de procesamiento de relaciones del modelo entre cosas o personas.

* [R](hdinsight-hadoop-r-scripts-linux.md): un lenguaje de código abierto y el entorno de sistemas estadísticos usados en el aprendizaje de.

* [Solr](hdinsight-hadoop-solr-install-linux.md): una plataforma de búsqueda empresarial que permite la búsqueda de texto de los datos.

Para obtener información sobre el desarrollo de sus propias acciones de secuencia de comandos, vea [desarrollo de acción de secuencia de comandos con HDInsight](hdinsight-hadoop-script-actions-linux.md).


## <a name="what-are-the-hadoop-components-and-utilities"></a>¿Cuáles son los componentes de Hadoop y utilidades?

Los siguientes componentes y utilidades se incluyen en clústeres HDInsight.

* **[Ambari](#ambari)**: aprovisionamiento, administración, supervisión y utilidades de clúster.

* **[Avro](#avro)** (Biblioteca de Microsoft .NET para Avro): serialización de datos para el entorno Microsoft .NET.

* **[Sección & HCatalog](#hive)**: lenguaje de consulta estructurado (SQL)-como consultas y una capa de administración de almacenamiento y de la tabla.

* **[Mahout](#mahout)**: para máquina scalable aprendizaje aplicaciones.

* **[MapReduce](#mapreduce)**: framework heredado para Hadoop distribuido procesamiento y administración de recursos. Vea [hilo](#yarn), la estructura de recursos de última generación.

* **[Oozie](#oozie)**: administración de flujo de trabajo.

* **[Caracas](#phoenix)**: capa de base de datos relacional sobre HBase.

* **[Cerdo](#pig)**: secuencias de comandos más sencillo para MapReduce transformaciones.

* **[Sqoop](#sqoop)**: datos importar y exportar.

* **[Tez](#tez)**: permite a los procesos de intensa de datos ejecutar eficazmente a escala.

* **[Hilo](#yarn)**: parte de la biblioteca de núcleo de Hadoop y la nueva generación de framework software MapReduce.

* **[ZooKeeper](#zookeeper)**: coordinar procesos en los sistemas distribuidos.

> [AZURE.NOTE] Para obtener información acerca de los componentes específicos y la información de versión, vea [componentes de Hadoop, control de versiones y ofertas de servicio en HDInsight][component-versioning]

### <a name="ambari"></a>Ambari

Apache Ambari es para aprovisionar, administrar y supervisar clústeres Hadoop Apache. Incluye una colección de herramientas de operador intuitiva y un conjunto sólido de API que ocultan la complejidad de Hadoop, lo que simplifica la operación de clústeres. HDInsight basados en Linux clústeres proporcionan tanto la Ambari web interfaz de usuario y la API de REST Ambari mientras clústeres basados en Windows proporcionan un subconjunto de la API de REST. Las vistas de Ambari en HDInsight clústeres permiten complementos capacidades de interfaz de usuario.

Vea [clústeres administrar HDInsight con Ambari](hdinsight-hadoop-manage-ambari.md) (sólo Linux), [clústeres Monitor Hadoop en HDInsight con la API Ambari](hdinsight-monitor-use-ambari-api.md)y <a target="_blank" href="https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md">referencia de la API de Ambari Apache</a>.

### <a name="avro"></a>Avro (biblioteca de Microsoft .NET para Avro)

La biblioteca de Microsoft .NET para Avro implementa el formato de intercambio de datos binarios compacta Apache Avro de serialización para el entorno Microsoft .NET. Utiliza <a target="_blank" href="http://www.json.org/">La notación de objetos JavaScript (JSON)</a> para definir un esquema de lenguaje válido que emite interoperabilidad de idioma, pueden leer datos significado serializados en un idioma en el otro. Encontrará información detallada sobre el formato en el < un destino = _ "en blanco" referencia = "http://avro.apache.org/docs/current/spec.html" > Apache Avro especificación</a>.
El formato de archivos de Avro es compatible con el modelo de programación MapReduce distribuido. Los archivos son "splittable", lo que significa que puede buscar cualquier punto de un archivo y empezar a leer desde un bloque en particular. Para averiguar cómo, vea [datos con la biblioteca de Microsoft .NET para Avro](hdinsight-dotnet-avro-serialization.md).


### <a name="hdfs"></a>HDFS

Sistema de archivos distribuido de Hadoop (HDFS) es un sistema de archivos distribuido que con MapReduce e hilo, es el centro del ecosistema Hadoop. HDFS es el sistema de archivo estándar para clústeres de Hadoop en HDInsight.

### <a name="hive"></a>Sección & HCatalog

<a target="_blank" href="http://hive.apache.org/">Sección de Apache</a> está integrado en Hadoop que le permite consultar y administrar grandes conjuntos de datos de almacenamiento distribuido mediante un lenguaje similar a SQL denominado HiveQL software de almacén de datos. Sección como cerdo, es una abstracción sobre MapReduce. Cuando ejecuta, subárbol traduce las consultas en una serie de MapReduce trabajos. Sección es conceptualmente más cerca de un sistema de administración de la base de datos relacional que cerdo y, por tanto, es adecuado para su uso con los datos más estructurados. Para los datos no estructurados, cerdo es la mejor opción. Consulte [usar subárbol con Hadoop en HDInsight](hdinsight-use-hive.md).

<a target="_blank" href="https://cwiki.apache.org/confluence/display/Hive/HCatalog/">Apache HCatalog</a> es una capa de administración de almacenamiento y la tabla para Hadoop que presenta una vista de datos relacional. En HCatalog, puede leer y escribir archivos en cualquier formato para el que se puede escribir una SerDe subárbol (serializador deserializador).

### <a name="mahout"></a>Mahout

<a target="_blank" href="https://mahout.apache.org/">Apache Mahout</a> es una biblioteca scalable de aprendizaje algoritmos que se ejecutan en Hadoop. Aplicaciones de aprendizaje de máquina con principios de estadísticas, enseñan a sistemas para aprender a partir de datos y usar más allá de los resultados para determinar el comportamiento futuro. Vea [Generar película recomendaciones con Mahout en Hadoop](hdinsight-mahout.md).

### <a name="mapreduce"></a>MapReduce
MapReduce es el marco de software heredado para Hadoop para escribir aplicaciones por lotes proceso grandes conjuntos de datos en paralelo. Un trabajo MapReduce divide grandes conjuntos de datos y organiza los datos en pares de valor de clave para el procesamiento.

[Hilo](#yarn) es el marco de aplicación de administrador del recurso de próxima generación Hadoop y se conoce como MapReduce 2.0. Ejecutan trabajos MapReduce en hilo.

Para obtener más información sobre MapReduce, vea <a target="_blank" href="http://wiki.apache.org/hadoop/MapReduce">MapReduce</a> en el Hadoop Wiki.

### <a name="oozie"></a>Oozie
<a target="_blank" href="http://oozie.apache.org/">Apache Oozie</a> es un sistema de junto de flujo de trabajo que administra Hadoop trabajos. Se integra con la pila Hadoop y es compatible con Hadoop trabajos para MapReduce, cerdo, subárbol y Sqoop. También puede usarse para programar trabajos específicos de un sistema, como los programas de Java o secuencias de comandos de shell. Consulte [usar Oozie con Hadoop](hdinsight-use-oozie.md).

### <a name="phoenix"></a>Caracas
<a  target="_blank" href="http://phoenix.apache.org/">Caracas Apache</a> es un nivel de base de datos relacional sobre HBase. Caracas incluyen un controlador JDBC que permite a los usuarios de la consulta y administrar las tablas SQL directamente. Caracas traduce las consultas y otras instrucciones en llamadas NoSQL API nativas - en lugar de usar MapReduce: lo que permite a las aplicaciones más rápidas por encima de NoSQL stores. Consulte [usar Apache Caracas y ardilla con clústeres HBase](hdinsight-hbase-phoenix-squirrel.md).


### <a name="pig"></a>Cerdo
<a  target="_blank" href="http://pig.apache.org/">Cerdo Apache</a> es una plataforma de alto nivel que le permite realizar transformaciones MapReduce complejas en muy grandes conjuntos de datos mediante un lenguaje de secuencias de comandos simple denominado cerdo latino. Cerdo traduce las secuencias de comandos de cerdo latino, por lo que deberá ejecutar dentro de Hadoop. Puede crear funciones definidas por el usuario (UDF) para extender cerdo latino. Consulte [usar cerdo con Hadoop](hdinsight-use-pig.md).

### <a name="sqoop"></a>Sqoop
<a  target="_blank" href="http://sqoop.apache.org/">Apache Sqoop</a> es la herramienta que transferencias masiva datos entre Hadoop y bases de datos relacionales como un SQL u otro almacena datos estructurados, de forma más eficaz. Consulte [usar Sqoop con Hadoop](hdinsight-use-sqoop.md).

### <a name="tez"></a>Tez
<a  target="_blank" href="http://tez.apache.org/">Tez Apache</a> es un marco de aplicación integrado en hilo Hadoop que ejecuta gráficos complejos, acíclicos general del procesamiento de datos. Es una sucesora más flexible y eficaz al marco MapReduce que permite a los procesos de intensa de datos, como subárbol, para que se ejecute más eficazmente a escala. Consulte ["Uso de Apache Tez para mejorar el rendimiento" en uso subárbol y HiveQL](hdinsight-use-hive.md#usetez).

### <a name="yarn"></a>HILO
Apache hilo es la siguiente generación de MapReduce (MapReduce 2.0 o MRv2) y admite escenarios de procesamiento de datos más allá de MapReduce procesamiento por lotes con mayor escalabilidad y procesamiento en tiempo real. HILO proporciona un marco de aplicación distribuida y administración de recursos. Ejecutan trabajos MapReduce en hilo.

Para obtener información sobre hilo, consulte <a target="_blank" href="http://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html">Apache Hadoop NextGen MapReduce (hilo)</a>.


### <a name="zookeeper"></a>ZooKeeper
<a  target="_blank" href="http://zookeeper.apache.org/">Apache ZooKeeper</a> coordenadas procesos en grandes sistemas distribuidos mediante un espacio de nombres jerárquico compartido de registros de datos (znodes). Znodes contienen pequeñas cantidades de información de meta necesarios para coordinar procesos: estado, ubicación, configuración y así sucesivamente.

## <a name="programming-languages-on-hdinsight"></a>Lenguajes de programación en HDInsight

Clústeres de HBase, tormenta y motor de clústeres--Hadoop, HDInsight--admite un número de lenguajes de programación, pero algunos no se instala de forma predeterminada. Para las bibliotecas, módulos o paquetes que no se instala de forma predeterminada, use una acción de secuencia de comandos para instalar el componente. Vea [desarrollo de acción de secuencia de comandos con HDInsight](hdinsight-hadoop-script-actions-linux.md).

### <a name="default-programming-language-support"></a>Predeterminada la compatibilidad con idiomas de programación

De forma predeterminada, HDInsight clústeres de soporte técnico:

* Java

* Python

Idiomas adicionales pueden instalarse mediante acciones de script: [desarrollo de acción de secuencia de comandos con HDInsight](hdinsight-hadoop-script-actions-linux.md).

### <a name="java-virtual-machine-jvm-languages"></a>Idiomas de Java virtual machine (JVM)

Se pueden ejecutar varios idiomas que no sean Java utilizando una máquina virtual de Java (JVM); Sin embargo, puede obligarle a componentes adicionales instalados en el clúster ejecutando algunos de estos idiomas.

Estos lenguajes de JVM son compatibles con clústeres HDInsight:

* Clojure

* Jython (Python para Java)

* Scala

### <a name="hadoop-specific-languages"></a>Idiomas específicos de Hadoop

HDInsight clústeres admiten los siguientes idiomas específicos al ecosistema Hadoop:

* Cerdo latino para trabajos de cerdo

* HiveQL para trabajos de sección y SparkSQL


## <a name="advantage"></a>Ventajas de Hadoop en la nube

Como parte del ecosistema de nube de Azure, Hadoop en HDInsight ofrece una serie de beneficios, entre ellos:

* Aprovisionamiento automático de Hadoop clústeres. HDInsight clústeres son mucho más fáciles crear clústeres de Hadoop para configurar manualmente. Para obtener más información, consulte [clústeres de aprovisionamiento Hadoop en HDInsight](hdinsight-hadoop-provision-linux-clusters.md).

* Componentes de Hadoop de imágenes prediseñadas. Para obtener más información, vea [componentes de Hadoop, control de versiones y ofertas de servicio en HDInsight][component-versioning].

* Alta disponibilidad y confiabilidad de clústeres. Para obtener información detallada, vea [disponibilidad y confiabilidad de clústeres Hadoop HDInsight](hdinsight-high-availability-linux.md) .

* Almacenamiento de datos eficaz y económico con el almacenamiento de blobs de Windows Azure, una opción de Hadoop compatible. Para obtener más información, vea [almacenamiento de blobs de Azure uso con Hadoop en HDInsight](hdinsight-hadoop-use-blob-storage.md) .

* Integración con otros servicios de Azure, incluidas [aplicaciones Web](https://azure.microsoft.com/documentation/services/app-service/web/) y [Base de datos de SQL](https://azure.microsoft.com/documentation/services/sql-database/).

* Más tamaños VM y tipos para ejecutar clústeres HDInsight. Vea [Hadoop componentes, control de versiones y ofertas de servicio en HDInsight] [ component-versioning] para obtener más detalles.

* Clúster de ajuste de escala. Ajuste de escala de clúster le permite cambiar el número de nodos de un clúster de HDInsight ejecución sin tener que eliminar o volver a crearlo.

* Soporte de red virtual. HDInsight clústeres pueden usarse con una red Virtual Azure a soporte técnico de aislamiento de recursos de la nube o escenarios híbrido con vínculos a recursos de la nube con las de su centro de datos.

* Entrada de bajo costo. Iniciar una [versión de prueba gratuita](https://azure.microsoft.com/free/)o consulte [los detalles de precios HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/).

Para obtener más información sobre las ventajas de Hadoop en HDInsight, consulte la [página características de Azure HDInsight][marketing-page].

## <a name="hdinsight-standard-and-hdinsight-premium"></a>HDInsight estándar y Premium HDInsight

HDInsight proporciona ofertas de nube de datos grande en dos categorías, estándar y Premium. HDInsight estándar proporciona un clúster de escala de la empresa que las organizaciones pueden usar para ejecutar sus cargas de trabajo de datos grande. HDInsight Premium en la que se basa y proporciona avanzada analítico y las capacidades de seguridad para un clúster de HDInsight. Para obtener más información, consulte [HDInsight Premium de Azure](hdinsight-component-versioning.md#hdinsight-standard-and-hdinsight-premium)


## <a id="resources"></a>Recursos para obtener más información sobre el análisis de datos grande, Hadoop y HDInsight

Se basan en esta introducción a Hadoop en la nube y el análisis de datos grande con los siguientes recursos.


### <a name="hadoop-documentation-for-hdinsight"></a>Documentación de Hadoop para HDInsight

* [Documentación de HDInsight](https://azure.microsoft.com/documentation/services/hdinsight/): la página de la documentación de Hadoop en HDInsight de Azure con vínculos a artículos, vídeos y más recursos.

* [Introducción a Hadoop en HDInsight](hdinsight-hadoop-linux-tutorial-get-started.md): un tutorial de inicio rápido para clústeres HDInsight Hadoop de aprovisionamiento y ejecución de consultas de la sección de ejemplo.

* [Empezar a trabajar con el motor en HDInsight](hdinsight-apache-spark-jupyter-spark-sql.md): un tutorial de inicio rápido para crear un clúster de motor y ejecutar consultas de SQL de motor interactivas.

* [Usar el servidor R en HDInsight](hdinsight-hadoop-r-server-get-started.md): empezar a usar servidor R en HDInsight Premium.

* [HDInsight de aprovisionamiento clústeres](hdinsight-hadoop-provision-linux-clusters.md): Obtenga información sobre cómo aprovisionar un clúster HDInsight Hadoop a través del portal de Azure, CLI de Azure o PowerShell de Azure.


### <a name="apache-hadoop"></a>Apache Hadoop

* <a target="_blank" href="http://hadoop.apache.org/">Apache Hadoop</a>: más información acerca de la biblioteca de software de Apache Hadoop, un marco que permite el procesamiento de grandes conjuntos de datos distribuido entre clústeres de equipos.

* <a target="_blank" href="http://hadoop.apache.org/docs/r1.0.4/hdfs_design.html">HDFS</a>: más información sobre la arquitectura y el diseño de la Hadoop sistema de archivos distribuido, el sistema de almacenamiento principal usado por aplicaciones de Hadoop.

* <a target="_blank" href="http://hadoop.apache.org/docs/r1.2.1/mapred_tutorial.html">MapReduce Tutorial</a>: más información sobre el marco de programación para programar aplicaciones de Hadoop que procesan rápidamente grandes cantidades de datos en paralelo en grandes clústeres de nodos de cálculo.


### <a name="microsoft-business-intelligence"></a>Inteligencia empresarial de Microsoft

Herramientas de inteligencia de empresa familiar - como Excel, PowerPivot, SQL Server Analysis Services y SQL Server Reporting Services - recuperarán, analizar e informan datos integrado con HDInsight mediante el complemento de Power Query o el controlador ODBC subárbol de Microsoft.

Estas herramientas de BI pueden ayudar a en el análisis de datos grande:

* [Conectar Excel a Hadoop con Power Query](hdinsight-connect-excel-power-query.md): Obtenga información sobre cómo conectar Excel a la cuenta de almacenamiento de Azure que almacena los datos relacionados con el clúster HDInsight usando Microsoft Power Query para Excel. Estación de trabajo de Windows necesario. Funciona con clúster basado en Windows o Linux.

* [Conectar Excel a Hadoop con el controlador ODBC subárbol de Microsoft](hdinsight-connect-excel-hive-odbc-driver.md): Obtenga información sobre cómo importar datos desde HDInsight con el controlador ODBC subárbol de Microsoft. Estación de trabajo de Windows necesario. Funciona con clúster basado en Windows o Linux.

* [Plataforma de nube de Microsoft](http://www.microsoft.com/server-cloud/solutions/business-intelligence/default.aspx): Obtenga información sobre Power BI para Office 365, descargue la versión de prueba de SQL Server y configurar SharePoint Server 2013 y SQL Server BI.

* [SQL Server Analysis Services](http://msdn.microsoft.com/library/hh231701.aspx).

* [SQL Server Reporting Services](http://msdn.microsoft.com/library/ms159106.aspx).




[marketing-page]: https://azure.microsoft.com/services/hdinsight/
[component-versioning]: hdinsight-component-versioning.md
[zookeeper]: http://zookeeper.apache.org/
