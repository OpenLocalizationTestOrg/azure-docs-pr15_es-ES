<properties
   pageTitle="Utilizar componentes de Python en una topología de tormenta en HDinsight | Microsoft Azure"
   description="Obtenga información sobre cómo puede usar los componentes de Python con Apache tormenta en HDInsight de Azure. Obtendrá información sobre cómo utilizar Python componentes de ambos un Java según y Clojure según la topología de tormenta."
   services="hdinsight"
   documentationCenter=""
   authors="Blackmist"
   manager="jhubbard"
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="python"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="09/27/2016"
   ms.author="larryfr"/>

#<a name="develop-apache-storm-topologies-using-python-on-hdinsight"></a>Desarrollar topologías Apache tormenta con Python en HDInsight

Apache tormenta admite varios idiomas, incluso le permite combinar componentes de varios idiomas en una topología. En este documento, aprenderá cómo usar componentes de Python en su topologías Java y tormenta de Clojure en HDInsight.

##<a name="prerequisites"></a>Requisitos previos

* Python 2.7 o superior

* Java JDK 1.7 o superior

* [Leiningen](http://leiningen.org/)

##<a name="storm-multi-language-support"></a>Compatibilidad con varios idioma de tormenta

Tormenta se ha diseñado para funcionar con componentes escritos mediante cualquier lenguaje de programación, pero esto requiere que los componentes de comprenden cómo trabajar con la [definición de Thrift de tormenta](https://github.com/apache/storm/blob/master/storm-core/src/storm.thrift). Para Python, se proporciona un módulo como parte del proyecto de tormenta Apache que le permite interactuar fácilmente con tormenta. Puede encontrar este módulo en [https://github.com/apache/storm/blob/master/storm-multilang/python/src/main/resources/resources/storm.py](https://github.com/apache/storm/blob/master/storm-multilang/python/src/main/resources/resources/storm.py).

Puesto que Apache tormenta es un proceso de Java que se ejecuta en la máquina Virtual de Java (JVM), los componentes escritos en otros idiomas se ejecutan como subprocesos. Los bits de tormenta ejecuta en JVM se comunica con estos subprocesos mediante mensajes JSON enviados a través de stdin o stdout. Obtener más detalles acerca de la comunicación entre los componentes se pueden encontrar en la documentación de [protocolo múltiple lang](https://storm.apache.org/documentation/Multilang-protocol.html) .

###<a name="the-storm-module"></a>El módulo de tormenta

El módulo de tormenta (https://github.com/apache/storm/blob/master/storm-multilang/python/src/main/resources/resources/storm.py), proporciona los bits necesarios para crear los componentes de Python que funcionan con tormenta.

Esto proporciona cosas como `storm.emit` emitir tuplas, y `storm.logInfo` para escribir en los registros. ¿Recomendamos que lea sobre este archivo y entender lo que proporciona.

##<a name="challenges"></a>Desafíos

Uso del módulo de __storm.py__ , puede crear spouts Python que consumen datos y pernos que procesan datos, pero la definición de topología de tormenta general que se conecta la comunicación entre los componentes se escribe con Java o Clojure. Además, si utiliza Java, debe crear también los componentes de Java que actúen como una interfaz de los componentes de Python.

Además, como clústeres tormenta ejecutan en modo distribuido, debe asegurarse de que todos los módulos necesarios para que los componentes de Python están disponibles en todos los nodos de trabajo en el clúster. Tormenta no proporciona ninguna forma fácil hacerlo para los recursos de múltiples lang: debe incluir todas las dependencias como parte del archivo jar para la topología o instalar manualmente las dependencias en cada nodo de trabajo en el clúster.

###<a name="java-vs-clojure-topology-definition"></a>Definición de topología de Java frente a Clojure

De los dos métodos de definir una topología, Clojure es la más sencilla y lo más limpio posible directamente componentes de python de referencia en la definición de la topología. Las definiciones de topología basada en Java, también debe definir los componentes de Java que controlan cosas como declarar los campos de las tuplas obtenidos mediante los componentes de Python.

Ambos métodos se describen en este documento, junto con ejemplos de proyectos.

##<a name="python-components-with-a-java-topology"></a>Componentes de Python con una topología de Java

> [AZURE.NOTE] Este ejemplo está disponible en [https://github.com/Azure-Samples/hdinsight-python-storm-wordcount](https://github.com/Azure-Samples/hdinsight-python-storm-wordcount) en el directorio __JavaTopology__ . Se trata de un proyecto en función de experto. Si no está familiarizado con experto, vea [topologías basada en desarrollar Java con Apache tormenta en HDInsight](hdinsight-storm-develop-java-topology.md) para obtener más información sobre la creación de un proyecto de experto en una topología de tormenta.

Una topología de Java que usa Python (o a otros componentes de lenguaje JVM) aparece inicialmente usar componentes de Java; pero si mira en cada uno de los spouts Java/pernos, verá código similar al siguiente:

    public SplitBolt() {
        super("python", "countbolt.py");
    }

Aquí es donde Java invoca Python ejecuta la secuencia de comandos que contiene la lógica de rayo real. Los spouts/pernos Java (para este ejemplo), simplemente declarar los campos de la tupla que será emitida por el componente de Python subyacente.

Los archivos de Python reales se almacenan en el `/multilang/resources` directorio en este ejemplo. La `/multilang` directory se hace referencia en el __pom.xml__:

<resources>
    <resource>
        <!-- Where the Python bits are kept -->
        <directory>${basedir} / multilang</directory>
    </resource>
</resources>

Esto incluye todos los archivos de la `/multilang` carpeta en el tarro que se generará de este proyecto.

> [AZURE.IMPORTANT] Tenga en cuenta que esto solo especifica la `/multilang` directorio y no `/multilang/resources`. Tormenta espera recursos no JVM en un `resources` directorio, por lo que se busca internamente ya. Colocación de componentes en esta carpeta le permite simplemente referencia por su nombre en el código de Java. Por ejemplo, `super("python", "countbolt.py");`. Otra forma de considerar es que ve tormenta la `resources` directorio como la raíz (/) cuando el acceso a los recursos de múltiples lang.
>
> Para este proyecto de ejemplo, el `storm.py` módulo se incluye en el `/multilang/resources` directorio.

###<a name="build-and-run-the-project"></a>Crear y ejecutar el proyecto

Para ejecutar este proyecto localmente, use el siguiente comando de experto para generar y ejecutar en modo local:

    mvn compile exec:java -Dstorm.topology=com.microsoft.example.WordCount

Utilice ctrl + c para terminar el proceso.

Para implementar el proyecto en un clúster de HDInsight ejecuta Apache tormenta, realice los siguientes pasos:

1. Crear un jar ejercicios:

        mvn package

    Esta opción creará un archivo llamado __WordCount--SNAPSHOT.jar 1.0__ en el `/target` directorio para este proyecto.

2. Cargue el archivo jar al clúster Hadoop mediante uno de los métodos siguientes:

    * Para clústeres de HDInsight __basados en Linux__ : usar `scp WordCount-1.0-SNAPSHOT.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:WordCount-1.0-SNAPSHOT.jar` para copiar el archivo jar en el clúster, reemplazar el nombre de usuario con el nombre de usuario SSH y NOMBREDECLÚSTER con el nombre de clúster HDInsight.

        Una vez que ha terminado de cargar el archivo, conecte al clúster mediante SSH e inicie el uso de topología`storm jar WordCount-1.0-SNAPSHOT.jar com.microsoft.example.WordCount wordcount`

    * Para clústeres de HDInsight __basados en Windows__ : conectar con el panel de tormenta yendo a HTTPS://CLUSTERNAME.azurehdinsight.net/ en el explorador. Reemplazar NOMBREDECLÚSTER con su nombre de clúster HDInsight y proporcione el nombre de administrador y la contraseña cuando se le solicite.

        Con el formulario, realice las siguientes acciones:

        * __Archivo JAR__: seleccione __Examinar__y luego seleccione el archivo __WordCount-1.0-SNAPSHOT.jar__
        * __Nombre de la clase__: escriba`com.microsoft.example.WordCount`
        * __Parámetros adicionales__: escriba un nombre descriptivo como `wordcount` para identificar la topología

        Por último, seleccione __Enviar__ para iniciar la topología.

> [AZURE.NOTE] Una vez iniciado, se ejecuta una topología de tormenta hasta que se detiene (sacrificio). Para detener la topología, use uno la `storm kill TOPOLOGYNAME` comando de la línea de comandos (SSH sesión a un clúster de Linux por ejemplo,) o utilizando la interfaz de usuario de tormenta, seleccione la topología y, a continuación, seleccione el botón __Eliminar__ .

##<a name="python-components-with-a-clojure-topology"></a>Componentes de Python con una topología de Clojure

> [AZURE.NOTE] Este ejemplo está disponible en [https://github.com/Azure-Samples/hdinsight-python-storm-wordcount](https://github.com/Azure-Samples/hdinsight-python-storm-wordcount) en el directorio __ClojureTopology__ .

Esta topología se creó usando [Leiningen](http://leiningen.org) para [crear un nuevo proyecto de Clojure](https://github.com/technomancy/leiningen/blob/stable/doc/TUTORIAL.md#creating-a-project). Después de eso, se han realizado las siguientes modificaciones al proyecto scaffolded:

* __Project.CLJ__: agregar dependencias de tormenta y exclusiones para elementos que pueden causar problemas cuando se implementa en el servidor HDInsight.
* __recursos y recursos__: Leiningen crea un valor predeterminado `resources` directorio, sin embargo, los archivos que se almacenan aquí parecen se agregan a la raíz del archivo jar creado a partir de este proyecto y tormenta espera archivos en un subcarpeta directorio denominado `resources`. Por lo que se ha agregado un directorio subcarpetas y los archivos de Python se almacenan en `resources/resources`. En tiempo de ejecución, esto se tratará como el raíz (/) para obtener acceso a los componentes de Python.
* __src/WordCount/Core.CLJ__: este archivo contiene la definición de topología y se hace referencia en el archivo __project.clj__ . Para obtener más información sobre cómo utilizar Clojure para definir una topología de tormenta, consulte [Clojure DSL](https://storm.apache.org/documentation/Clojure-DSL.html).

###<a name="build-and-run-the-project"></a>Crear y ejecutar el proyecto

__Para generar y ejecutar el proyecto de forma local__, utilice el siguiente comando:

    lein clean, run

Para detener la topología, use __Ctrl + C__.

__Para crear un uberjar e implementar a HDInsight__, realice los siguientes pasos:

1. Crear un uberjar que contiene la topología y las dependencias necesarias:

        lein uberjar

    Esta opción creará un nuevo archivo denominado `wordcount-1.0-SNAPSHOT.jar` en la `target\uberjar+uberjar` directorio.
    
2. Use uno de los siguientes métodos para implementar y ejecutar la topología a un clúster de HDInsight:

    * __HDInsight de Linux__
    
        1. Copie el archivo en el nodo principal del clúster HDInsight utilizando `scp`. Por ejemplo:
        
                scp wordcount-1.0-SNAPSHOT.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:wordcount-1.0-SNAPSHOT.jar
                
            Reemplace el nombre de usuario con un usuario SSH para su clúster y NOMBREDECLÚSTER con su nombre de clúster HDInsight.
            
        2. Una vez que el archivo se ha copiado en el clúster, use SSH para conectarse al clúster y enviar el trabajo. Para obtener información sobre cómo utilizar SSH con HDInsight, consulte uno de estos procedimientos:
        
            * [Utilizar SSH con HDInsight basados en Linux de Linux, Unix o OS X](hdinsight-hadoop-linux-use-ssh-unix.md)
            * [Utilizar SSH con basados en Linux HDInsight de Windows.](hdinsight-hadoop-linux-use-ssh-windows.md)
            
        3. Una vez conectado, utilice la siguiente para iniciar la topología:
        
                storm jar wordcount-1.0-SNAPSHOT.jar wordcount.core wordcount
    
    * __HDInsight de Windows__
    
        1. Conectar con el panel de tormenta yendo a HTTPS://CLUSTERNAME.azurehdinsight.net/ en el explorador. Reemplazar NOMBREDECLÚSTER con su nombre de clúster HDInsight y proporcione el nombre de administrador y la contraseña cuando se le solicite.

        2. Con el formulario, realice las siguientes acciones:

            * __Archivo JAR__: seleccione __Examinar__y luego seleccione el archivo __wordcount-1.0-SNAPSHOT.jar__
            * __Nombre de la clase__: escriba`wordcount.core`
            * __Parámetros adicionales__: escriba un nombre descriptivo como `wordcount` para identificar la topología

            Por último, seleccione __Enviar__ para iniciar la topología.

> [AZURE.NOTE] Una vez iniciado, se ejecuta una topología de tormenta hasta que se detiene (sacrificio). Para detener la topología, use uno la `storm kill TOPOLOGYNAME` comando de la línea de comandos (SSH sesión a un clúster de Linux) o utilizando la interfaz de usuario de tormenta, seleccione la topología y, a continuación, seleccione el botón __Eliminar__ .

##<a name="next-steps"></a>Pasos siguientes

En este documento, ha aprendido cómo utilizar componentes de Python desde una topología de tormenta. Consulte los siguientes documentos para otras maneras de utilizar Python con HDInsight:

* [Cómo usar Python para streaming MapReduce trabajos](hdinsight-hadoop-streaming-python.md)
* [Cómo utilizar Python usuario definido funciones (UDF) en cerdo y subárbol](hdinsight-python.md)
