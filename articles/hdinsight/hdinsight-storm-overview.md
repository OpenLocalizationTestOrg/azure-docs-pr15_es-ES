<properties
    pageTitle="Introducción a tormenta Apache en HDInsight | Microsoft Azure"
    description="Obtener una introducción a Apache tormenta y obtenga información sobre cómo puede usar tormenta en HDInsight para crear soluciones de análisis de datos en tiempo real en la nube."
    services="hdinsight"
    documentationCenter=""
    authors="Blackmist"
    manager="jhubbard"
    editor="cgronlun"
    tags="azure-portal"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="10/11/2016"
   ms.author="larryfr"/>

#<a name="introduction-to-apache-storm-on-hdinsight-real-time-analytics-for-hadoop"></a>Introducción a tormenta Apache en HDInsight: análisis en tiempo real de Hadoop

Tormenta Apache en HDInsight le permite crear soluciones de análisis en tiempo real, distribuidas en el entorno de Azure mediante [Hadoop Apache](http://hadoop.apache.org).

##<a name="what-is-apache-storm"></a>¿Qué es Apache tormenta?

Tormenta Apache es un sistema de cálculo distribuido, tolerancia a errores, Abrir origen que le permite procesar datos en tiempo real con Hadoop. Soluciones de tormenta también pueden proporcionar garantiza tratamiento de datos, con la capacidad de reproducir los datos que no se ha procesado correctamente la primera vez.

##<a name="why-use-storm-on-hdinsight"></a>¿Por qué usar tormenta en HDInsight?

Tormenta Apache en HDInsight es un clúster administrado integrado en el entorno de Azure. Proporciona las siguientes ventajas clave:

* Funciona como un servicio administrado con un SLA de 99,9% el tiempo

* Usar el idioma de su elección: proporciona soporte técnico para componentes de tormenta escritos en **Java**, **C#**y **Python**

    * Es compatible con una combinación de lenguajes de programación: leer datos con Java, a continuación, procesar mediante C#
    
        > [AZURE.NOTE] C# topologías solo son compatibles con clústeres HDInsight basado en Windows.

    * Usar la interfaz de Java **Trident** para crear topologías de tormenta que admiten "exactamente una vez" procesamiento de mensajes, persistencia "transacciones" almacén de datos y un conjunto de operaciones de análisis de secuencia comunes

* Incluye características integradas de escalado y reducción: ajustar el tamaño de un clúster de HDInsight sin afectar a ejecutando topologías de tormenta

* Integrar con otros servicios de Azure, incluidos concentrador de evento, una red Virtual Azure, base de datos de SQL, almacenamiento de blobs y DocumentDB

    * Combinar las capacidades de varios clústeres HDInsight mediante una red Virtual Azure: crear canalizaciones analíticas que usan clústeres HDInsight, HBase o Hadoop

Para obtener una lista de empresas que utilizan tormenta Apache para sus soluciones de análisis en tiempo real, consulte [Tormenta Apache usando de compañías](https://storm.apache.org/documentation/Powered-By.html).

Para empezar a usar tormenta, vea [Introducción a tormenta en HDInsight][gettingstarted].

###<a name="ease-of-provisioning"></a>Facilidad de aprovisionamiento

Puede aprovisionar una tormenta de nueva en el clúster de HDInsight en minutos. Especifique el nombre del clúster, tamaño, cuenta de administrador y la cuenta de almacenamiento. Azure creará el clúster, incluyendo topologías de ejemplo y un panel de administración de web.

> [AZURE.NOTE] También puede aprovisionar clústeres tormenta con [Azure CLI](../xplat-cli-install.md) o [PowerShell de Azure](../powershell-install-configure.md).

En 15 minutos de envío de la solicitud, tendrá un nuevo clúster de tormenta ejecutando y listo para su primera de canalización de análisis en tiempo real.

###<a name="ease-of-use"></a>Facilidad de uso

__Basados en Linux de tormenta en clústeres HDInsight__, puede conectarse al clúster mediante SSH y usar la `storm` comando para iniciar y administrar topologías. Además, puede utilizar Ambari para supervisar el servicio de tormenta y la interfaz de usuario de tormenta para supervisar y administrar topologías ejecución.

Para obtener más información sobre cómo trabajar con clústeres basados en Linux tormenta, vea [Introducción a tormenta Apache en HDInsight de Linux](hdinsight-apache-storm-tutorial-get-started-linux.md).

__Basado en Windows para tormenta en clústeres HDInsight__, las herramientas de HDInsight para Visual Studio permite crear híbridos C# y C# / topologías Java y enviarlos a su tormenta en clúster HDInsight.  

![Creación de proyectos de tormenta](./media/hdinsight-storm-overview/createproject.png)

HDInsight Tools para Visual Studio también proporciona una interfaz que permite supervisar y administrar topologías tormenta en un clúster.

![Administración de tormenta](./media/hdinsight-storm-overview/stormview.png)

Para obtener un ejemplo de uso de las herramientas de HDInsight para crear una aplicación de tormenta, vea [desarrollar C# tormenta topologías con las herramientas de HDInsight para Visual Studio](hdinsight-storm-develop-csharp-visual-studio-topology.md).

Para obtener más información acerca de las herramientas de HDInsight para Visual Studio, vea [Introducción al uso de las herramientas de HDInsight para Visual Studio](../HDInsight/hdinsight-hadoop-visual-studio-tools-get-started.md).

Cada tormenta en clúster HDInsight también proporciona un panel de tormenta basada en web que le permite enviar, supervisar y administrar topologías de tormenta que se ejecutan en el clúster.

![Panel de tormenta](./media/hdinsight-storm-overview/dashboard.png)

Para obtener más información sobre cómo usar el panel de tormenta, consulte [implementar y administrar topologías tormenta Apache en HDInsight](hdinsight-storm-deploy-monitor-topology.md).

Tormenta en HDInsight también proporciona una fácil integración con Azure evento Hubs mediante el **Evento chorros de concentrador**. La última versión de este componente está disponible en [https://github.com/hdinsight/hdinsight-storm-examples/tree/master/lib/eventhubs](https://github.com/hdinsight/hdinsight-storm-examples/tree/master/lib/eventhubs). Para obtener más información sobre el uso de este componente, consulte los siguientes documentos.

* [Desarrollar una topología de C# que usa Hubs de evento de Azure](hdinsight-storm-develop-csharp-event-hub-topology.md)

* [Desarrollar una topología de Java que usa Hubs de evento de Azure](hdinsight-storm-develop-java-event-hub-topology.md)

###<a name="reliability"></a>Confiabilidad

Apache tormenta siempre garantiza que todos los mensajes entrantes totalmente procesará, incluso cuando el análisis de datos se extiende cientos de nodos.

El **nodo Nimbus** proporciona funciones similares a las Hadoop JobTracker y asigna tareas a otros nodos de clúster a través de **Zookeeper**. Los nodos de zookeeper proporcionan junto para el clúster y facilitan la comunicación entre Nimbus y el proceso de **Supervisor** en los nodos de trabajo. Si un nodo de procesamiento deja de funcionar, se le informará el nodo Nimbus y asigna la tarea y datos asociados a otro nodo.

La configuración predeterminada de tormenta Apache es sólo tienen un nodo Nimbus. Tormenta en HDInsight ejecuta dos nodos de Nimbus. Si se produce un error en el nodo principal, el clúster HDInsight cambiará al nodo secundario mientras se recupera el nodo principal.

![Diagrama de nimbus, zookeeper y supervisor](./media/hdinsight-storm-overview/nimbus.png)

###<a name="scale"></a>Escala

Aunque puede especificar el número de nodos en el clúster durante la creación, es aconsejable aumentar o reducir el clúster para que coincida con la carga de trabajo. Todos los clústeres HDInsight le permiten cambiar el número de nodos en el clúster, incluso durante el procesamiento de datos.

> [AZURE.NOTE] Para aprovechar las ventajas de nuevos nodos agregados a través de ajuste de escala, debe equilibrar topologías iniciadas antes de que se incrementa el tamaño de clúster.

###<a name="support"></a>Soporte técnico

Tormenta en HDInsight viene con el soporte de 24/7 completo en el nivel de empresa. Tormenta en HDInsight también tiene un SLA de 99,9%. Esto significa que se garantiza que el clúster tiene conectividad externa al menos un 99,9% del tiempo.

##<a name="common-use-cases-for-real-time-analytics"></a>Casos de uso común para el análisis en tiempo real

Los siguientes son algunos escenarios comunes que puede usar tormenta Apache en HDInsight. Para obtener información sobre situaciones del mundo real, lea [cómo empresas están usando tormenta](https://storm.apache.org/documentation/Powered-By.html).

* Internet de cosas (IoT)
* Detección de fraudes
* Análisis social
* Extraer, transformar, carga (ETL)
* Supervisión de red
* Búsqueda
* Compromiso de móvil

##<a name="how-is-data-in-hdinsight-storm-processed"></a>¿Cómo se procesan datos de tormenta HDInsight?

Apache tormenta ejecuta **topologías** en lugar de los trabajos de MapReduce que pueden estar familiarizado con HDInsight o Hadoop. Una tormenta de clúster HDInsight contiene dos tipos de nodos: sede nodos que ejecutan nodos **Nimbus** y el trabajo que se ejecutan **Supervisor**.

* **Nimbus**: Similar a la JobTracker en Hadoop, es responsable de distribuir código en todo el clúster, asignar tareas a máquinas virtuales y supervisión de error. HDInsight proporciona dos nodos de Nimbus, lo que no hay ningún punto único de error de tormenta en HDInsight

* **Supervisor**: el supervisor para cada nodo de trabajo es responsable de iniciar y detener **los procesos de trabajo** en el nodo.

* **Proceso de trabajo**: se ejecuta un subconjunto de una **topología**. Una topología de ejecución se distribuye entre varios procesos de trabajo en todo el clúster.

* **Topología**: define un gráfico de cálculo que procesa las **secuencias** de datos. A diferencia de los trabajos de MapReduce topologías ejecutan hasta que se detiene.

* **Secuencia**: un conjunto de **tuplas**independiente. Secuencias producidas por **spouts** y **pernos**y se consumen por **pernos**.

* **Tupla**: una lista de valores dinámicamente escritas con nombre.

* **Chorros**: consume datos de un origen de datos y emite una o más **secuencias**.

    > [AZURE.NOTE] En muchos casos, los datos se leen de una cola, como Kafka, colas de Bus de servicio de Azure o hubs de evento. La cola garantiza que los datos se conservan si hay una interrupción.

* **Rayo**: consume **secuencias**, realiza procesamiento de **tuplas**y puede emitir **secuencias**. Pernos también son responsables de escribir datos en un almacenamiento externo, como una cola, HDInsight, HBase, un blob u otro almacén de datos.

* **Apache Thrift**: un marco de software para el desarrollo de scalable servicio entre lenguajes. Permite crear servicios que funcionan entre C++, Java, Python, PHP, Ruby, Erlang, Perl, Haskell, C#, cacao, JavaScript, Node.js, Smalltalk y otros idiomas.

    * **Nimbus** es un servicio de Thrift y una **topología** es una definición de Thrift, por lo que es posible desarrollar topologías con una amplia variedad de lenguajes de programación.

Para obtener más información acerca de los componentes de tormenta, consulte el [tutorial de tormenta] [ apachetutorial] en apache.org.


##<a name="what-programming-languages-can-i-use"></a>¿Qué lenguajes de programación se debe usar?

La tormenta en clúster HDInsight proporciona compatibilidad con C#, Java y Python.

### <a name="c35"></a>C & #35;

Las herramientas de HDInsight para Visual Studio permite a los desarrolladores de .NET diseñar e implementar una topología en C#. También puede crear topologías híbridas que utilizan los componentes de Java y C#.

Para obtener más información, vea [desarrollar C# topologías de tormenta Apache en HDInsight con Visual Studio](hdinsight-storm-develop-csharp-visual-studio-topology.md).

###<a name="java"></a>Java

Ejemplos de Java mayoría que encuentra será Java sin formato o Trident. Trident es una abstracción de alto nivel que hace que sea más fácil hacer cosas como combinaciones, agregaciones, agrupar y filtrar. Sin embargo, Trident actúa en lotes de tuplas, mientras que una solución de Java sin formato procesa una secuencia de una tupla en un momento.

Para obtener más información acerca de Trident, consulte el [tutorial de Trident](https://storm.apache.org/documentation/Trident-tutorial.html) en apache.org.

Para obtener ejemplos de Java y Trident topologías, vea la [lista de topologías de tormenta de ejemplo](hdinsight-storm-example-topology.md) o los ejemplos de tormenta starter en el clúster HDInsight.

Los ejemplos de tormenta starter se encuentran en el directorio __/usr/hdp/current/storm-client/contrib/storm-starter__ clústeres basados en Linux y el directorio **%storm_home%\contrib\storm-starter** clústeres basados en Windows.

##<a name="what-are-some-common-development-patterns"></a>¿Cuáles son algunas tramas de desarrollo comunes?

###<a name="guaranteed-message-processing"></a>Procesamiento de mensajes garantiza

Tormenta puede proporcionar diferentes niveles de procesamiento de mensajes garantiza. Por ejemplo, una aplicación de tormenta básica puede garantizar el procesamiento de-menos una vez y Trident puede garantizar exactamente-procesamiento de una vez.

Para obtener más información, vea [garantías de procesamiento de datos](https://storm.apache.org/about/guarantees-data-processing.html) de apache.org.

###<a name="ibasicbolt"></a>IBasicBolt

El modelo de una tupla de entrada, como emisor de cero o más tuplas y, a continuación, acking la entrada tupla inmediatamente al final del método execute de lectura es muy comunes y tormenta proporciona la interfaz de [IBasicBolt](https://storm.apache.org/apidocs/backtype/storm/topology/IBasicBolt.html) para automatizar este patrón.

###<a name="joins"></a>Combinaciones

Combinar dos secuencias de datos varían entre las aplicaciones. Por ejemplo, podría unir cada tupla a partir de varias secuencias en una secuencia de nueva, o puede unirse solo lotes de tuplas una ventana concreta. En ambos casos, unirse a puede realizarse mediante [fieldsGrouping](http://javadox.com/org.apache.storm/storm-core/0.9.1-incubating/backtype/storm/topology/InputDeclarer.html#fieldsGrouping%28java.lang.String,%20backtype.storm.tuple.Fields%29), que es una forma de definir cómo se enrutan tuplas a pernos.

En el siguiente ejemplo de Java fieldsGrouping se utiliza para redirigir tuplas que proceden de componentes de "1", "2" y "3" a rayo de **MyJoiner** .

    builder.setBolt("join", new MyJoiner(), parallelism) .fieldsGrouping("1", new Fields("joinfield1", "joinfield2")) .fieldsGrouping("2", new Fields("joinfield1", "joinfield2")) .fieldsGrouping("3", new Fields("joinfield1", "joinfield2"));

###<a name="batching"></a>Procesamiento por lotes

Procesamiento por lotes se puede realizar varias formas. Con una topología de tormenta Java básica, podría usar contador simple al número de lote X de tuplas antes de emisión de ellos o utilizar un mecanismo de intervalo de tiempo interno conocido como "tupla de graduación" emitir un lote de cada X segundos.

Para obtener un ejemplo de uso de tuplas de graduación, vea [analizar datos de sensor con tormenta y HBase en HDInsight](hdinsight-storm-sensor-data-analysis.md).

Si está utilizando Trident, se basa en el procesamiento de lotes de tuplas.

###<a name="caching"></a>Almacenamiento en caché

En la memoria caché a menudo se utiliza como mecanismo para aumentar la velocidad de procesamiento porque mantiene con frecuencia utiliza activos en memoria. Porque una topología se distribuye en varios nodos y varios procesos dentro de cada nodo, considere la posibilidad de usar [fieldsGrouping](http://javadox.com/org.apache.storm/storm-core/0.9.1-incubating/backtype/storm/topology/InputDeclarer.html#fieldsGrouping%28java.lang.String,%20backtype.storm.tuple.Fields%29) para asegurarse de que siempre se enrutan tuplas que contienen los campos que se usan para la búsqueda en caché para el mismo proceso. Esto evita la duplicación de entradas de caché a través de procesos.

###<a name="streaming-top-n"></a>Transmisión top N

Cuando la topología depende calcular un valor de "top N", como las tendencias de 5 superiores en Twitter, debe calcular el valor de top N en paralelo y, a continuación, combinar los resultados de dichos cálculos en un valor global. Esto puede hacerse mediante [fieldsGrouping](http://javadox.com/org.apache.storm/storm-core/0.9.1-incubating/backtype/storm/topology/InputDeclarer.html#fieldsGrouping%28java.lang.String,%20backtype.storm.tuple.Fields%29) a ruta por campo para los pernos paralelo (que divide los datos por valor de campo) y, a continuación, redirigir a un rayo que determina el valor de top N de forma global.

Para obtener un ejemplo de esto, consulte el ejemplo [RollingTopWords](https://github.com/nathanmarz/storm-starter/blob/master/src/jvm/storm/starter/RollingTopWords.java) .

##<a name="what-type-of-logging-does-storm-use"></a>¿Qué tipo de registro Storm uso?

Tormenta usa Log4j Apache para registrar información. De forma predeterminada, se registra una gran cantidad de datos y puede ser difícil ordenar la información. Puede incluir un archivo de configuración de registro como parte de la topología de tormenta al comportamiento del registro de control.

Para una topología de ejemplo que muestra cómo configurar el registro, vea ejemplo de [WordCount basada en Java](hdinsight-storm-develop-java-topology.md) de tormenta en HDInsight.

##<a name="next-steps"></a>Pasos siguientes

Más información acerca de las soluciones de análisis en tiempo real con Apache tormenta en HDInsight:

* [Introducción a tormenta en HDInsight][gettingstarted]

* [Topologías de ejemplo para tormenta en HDInsight](hdinsight-storm-example-topology.md)

[stormtrident]: https://storm.apache.org/documentation/Trident-API-Overview.html
[samoa]: http://yahooeng.tumblr.com/post/65453012905/introducing-samoa-an-open-source-platform-for-mining
[apachetutorial]: https://storm.apache.org/documentation/Tutorial.html
[gettingstarted]: hdinsight-apache-storm-tutorial-get-started-linux.md
