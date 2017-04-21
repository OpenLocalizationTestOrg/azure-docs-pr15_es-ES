<properties
    pageTitle="Instalar y usar Giraph en HDInsight basados en Linux (Hadoop) | Microsoft Azure"
    description="Obtenga información sobre cómo instalar Giraph en clústeres basados en Linux HDInsight usar acciones de Script. Acciones de script permiten personalizar el clúster durante la creación, cambiando la configuración de clúster o instalar utilidades y servicios."
    services="hdinsight"
    documentationCenter=""
    authors="Blackmist"
    manager="jhubbard"
    editor="cgronlun"
    tags="azure-portal"/>

<tags
    ms.service="hdinsight"
    ms.workload="big-data"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/17/2016"
    ms.author="larryfr"/>

# <a name="install-giraph-on-hdinsight-hadoop-clusters-and-use-giraph-to-process-large-scale-graphs"></a>Instalar Giraph en HDInsight Hadoop clústeres y utilizar Giraph para procesar gráficos a gran escala

Puede instalar Giraph en cualquier tipo de clúster en Hadoop en HDInsight de Azure mediante **Acción de secuencia de comandos** para personalizar un clúster.

En este tema, obtendrá información sobre cómo instalar Giraph mediante secuencia de comandos de acción. Una vez haya instalado Giraph, aprenderá cómo usar Giraph para las aplicaciones más habituales, que es procesar gráficos a gran escala.

> [AZURE.NOTE] La información en este artículo es específica para clústeres HDInsight basados en Linux. Para obtener información sobre cómo trabajar con clústeres basados en Windows, vea [Instalar Giraph en clústeres HDInsight Hadoop (Windows)](hdinsight-hadoop-giraph-install.md)

## <a name="whatis"></a>¿Qué es Giraph?

[Apache Giraph](http://giraph.apache.org/) permite realizar graph procesar mediante Hadoop y se pueden usar con HDInsight de Azure. Gráficos de modelo de relaciones entre objetos, como las conexiones entre en una red grande como Internet o relaciones entre personas en redes sociales (también se conoce como un gráfico social). Procesamiento de gráfico le permite motivo acerca de las relaciones entre objetos de un gráfico, como por ejemplo:

- Identificar a posibles amigos en función de sus relaciones actuales.
- Identifica la ruta más corta entre dos equipos en una red.
- Calcular la jerarquía de la página de las páginas Web.

> [AZURE.WARNING] Componentes proporcionados con el clúster HDInsight son totalmente compatibles y Microsoft Support le ayudará a aislar y resolver los problemas relacionados con estos componentes.
>
> Componentes personalizados, como Giraph, reciban soporte comercialmente razonable para ayudar a solucionar el problema. Esto puede ocasionar resolver el problema o preguntar si desea integrarse canales disponibles para las tecnologías de código abierto donde se encuentra la amplia experiencia para que la tecnología. Por ejemplo, hay muchos sitios de comunidad que se pueden usar como: [foro de MSDN para HDInsight](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=hdinsight), [http://stackoverflow.com](http://stackoverflow.com). También proyectos Apache tienen sitios de proyecto en [http://apache.org](http://apache.org), por ejemplo: [Hadoop](http://hadoop.apache.org/).

##<a name="what-the-script-does"></a>¿Qué significa la secuencia de comandos

Esta secuencia de comandos realiza las siguientes acciones:

* Instalaciones Giraph a`/usr/hdp/current/giraph`
* Copias del `giraph-examples.jar` archivo de almacenamiento predeterminado (WASB) para el clúster:`/example/jars/giraph-examples.jar`

## <a name="install"></a>Instalar Giraph usar acciones de Script

Una secuencia de comandos de ejemplo para instalar Giraph en un clúster de HDInsight está disponible en la siguiente ubicación.

    https://hdiconfigactions.blob.core.windows.net/linuxgiraphconfigactionv01/giraph-installer-v01.sh

Esta sección proporciona instrucciones sobre cómo utilizar la secuencia de comandos al crear el clúster a través del Portal de Azure. 

> [AZURE.NOTE] PowerShell Azure, CLI Azure, el SDK de .NET HDInsight o administrador de recursos de Azure plantillas también sirve para aplicar acciones de script. También puede aplicar acciones de secuencia de comandos para clústeres ya en ejecución. Para obtener más información, vea [Personalizar HDInsight clústeres con acciones de Script](hdinsight-hadoop-customize-cluster-linux.md).

1. Empezar a crear un clúster con los pasos en [clústeres basados en Linux crear HDInsight](hdinsight-hadoop-create-linux-clusters-portal.md), pero no se completa la creación.

2. En el módulo **Configuración opcional** , seleccione **Acciones de secuencias de comandos**y proporcione la siguiente información:

    * __Nombre__: escriba un nombre descriptivo para la acción de secuencia de comandos.
    * __URI de secuencia de comandos__: https://hdiconfigactions.blob.core.windows.net/linuxgiraphconfigactionv01/giraph-installer-v01.sh
    * __Cabeza__: Active esta opción
    * __Trabajo__: deje este desactivado
    * __ZOOKEEPER__: deje este desactivado
    * __Parámetros__: deje este campo en blanco

3. En la parte inferior de las **Acciones de secuencias de comandos**, use el botón **Seleccionar** para guardar la configuración. Por último, utilice el botón **Seleccionar** en la parte inferior del módulo **Opcional de configuración** para guardar la información de configuración opcional.

4. Continuar con la creación del clúster como se describe en [HDInsight basados en Linux crear clústeres](hdinsight-hadoop-create-linux-clusters-portal.md).

## <a name="usegiraph"></a>¿Cómo se puede usar Giraph en HDInsight?

Una vez que ha terminado de crear el clúster, realice los pasos siguientes para ejecutar el ejemplo SimpleShortestPathsComputation incluido con Giraph. Implementa la implementación de <a href = "http://people.apache.org/~edwardyoon/documents/pregel.pdf">Pregel</a> básica para buscar la ruta de acceso más corta entre objetos de un gráfico.

1. Conectarse al clúster HDInsight mediante SSH:

        ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net

    Para obtener más información sobre cómo utilizar SSH con HDInsight, consulte lo siguiente:

    * [Utilizar SSH con basados en Linux Hadoop en HDInsight de Linux, Unix o OS X](hdinsight-hadoop-linux-use-ssh-unix.md)

    * [Utilizar SSH con basados en Linux Hadoop en HDInsight de Windows.](hdinsight-hadoop-linux-use-ssh-windows.md)

1. Utilice la siguiente para crear un nuevo archivo denominado __tiny_graph.txt__:

        nano tiny_graph.txt

    Utilice la siguiente como el contenido de este archivo:

        [0,0,[[1,1],[3,3]]]
        [1,0,[[0,1],[2,2],[3,1]]]
        [2,0,[[1,2],[4,4]]]
        [3,0,[[0,3],[1,1],[4,4]]]
        [4,0,[[3,4],[2,4]]]

    Una relación entre objetos en un gráfico dirigido, utilizando el formato, describen estos datos [origen\_id, origen\_valor, [[dest\_id], [borde\_valor],...]]. Cada línea representa una relación entre un **origen\_id** objeto y uno o más **dest\_id** objetos. La **borde\_valor** (o grosor) puede considerarse como la distancia de la conexión entre **source_id** o intensidad y **dest\_id**.

    Dibujado, y usa el valor (o grosor) como la distancia entre objetos, los datos anteriores tendrá este aspecto:

    ![dibujado en forma de círculos con líneas de diversos distancia entre el tiny_graph.txt](./media/hdinsight-hadoop-giraph-install-linux/giraph-graph.png)

2. Para guardar el archivo, use __ __Ctrl + X__, luego y finalmente __ENTRAR__ __para aceptar el nombre de archivo.

3. Use las siguientes acciones para almacenar los datos en el almacenamiento principal para el clúster de HDInsight:

        hdfs dfs -put tiny_graph.txt /example/data/tiny_graph.txt

4. Ejecute el ejemplo SimpleShortestPathsComputation mediante el siguiente comando.

         yarn jar /usr/hdp/current/giraph/giraph-examples.jar org.apache.giraph.GiraphRunner org.apache.giraph.examples.SimpleShortestPathsComputation -ca mapred.job.tracker=headnodehost:9010 -vif org.apache.giraph.io.formats.JsonLongDoubleFloatDoubleVertexInputFormat -vip /example/data/tiny_graph.txt -vof org.apache.giraph.io.formats.IdWithValueTextOutputFormat -op /example/output/shortestpaths -w 2

    Los parámetros utilizados con este comando se describen en la tabla siguiente.

  	| Parámetro | Qué hace |
  	| --------- | ------------ |
  	| `jar /usr/hdp/current/giraph/giraph-examples.jar` | El archivo jar que contiene los ejemplos. |
  	| `org.apache.giraph.GiraphRunner` | La clase que se utiliza para iniciar los ejemplos. |
  	| `org.apache.giraph.examples.SimpleShortestPathsCoputation` | En el ejemplo que se ha encontrado. En este caso, calculará la ruta de acceso más corta entre ID 1 y todos los otros identificadores en el gráfico. |
  	| `-ca mapred.job.tracker=headnodehost:9010` | Headnode para el clúster. |
  	| `-vif org.apache.giraph.io.formats.JsonLongDoubleFloatDoubleVertexInputFromat` | El formato de entrada para los datos de entrada. |
  	| `-vip /example/data/tiny_graph.txt` | El archivo de datos de entrada. |
  	| `-vof org.apache.giraph.io.formats.IdWithValueTextOutputFormat` | El formato de salida. En este caso, identificador y el valor como texto sin formato. |
  	| `-op /example/output/shortestpaths` | La ubicación de salida. |
  	| `-w 2` | El número de trabajadores usar. En este caso, 2. |

    Para obtener más información sobre estos y otros parámetros que usa con muestras de Giraph, consulte el [Tutorial de Giraph](http://giraph.apache.org/quick_start.html).

5. Una vez que haya finalizado el trabajo, los resultados se almacenarán en el __wasbs: / / / ejemplo/out/shotestpaths__ directorio. Los archivos creados comenzará con __parte-m -__ y terminar con un número que indica el primero, segundo, etc. de archivos. Para ver el resultado, use las siguientes acciones:

        hdfs dfs -text /example/output/shortestpaths/*

    El resultado debería ser similar al siguiente:

        0   1.0
        4   5.0
        2   2.0
        1   0.0
        3   1.0

    La SimpleShortestPathComputation ejemplo está codificado para empezar con 1 identificador de objeto y buscar la ruta de acceso más corta a otros objetos. Para que el resultado se debe leer como `destination_id distance`, donde la distancia es el valor o grosor de los bordes desplazados entre objeto identificador 1 y el identificador de destino.

    Visualizar esto, puede comprobar los resultados de las rutas de acceso más corta de viaje entre ID 1 y todos los demás objetos. Observe que la ruta de acceso más corta entre ID 1 y 4 ID es 5. Esta es la distancia total entre <span style="color:orange">ID 1 y 3</span>y, a continuación, <span style="color:red">identificador de 3 y 4</span>.

    ![Dibujo de objetos como círculos con rutas de acceso más corta dibujadas entre](./media/hdinsight-hadoop-giraph-install-linux/giraph-graph-out.png)


## <a name="next-steps"></a>Pasos siguientes

- [Instalar y usar clústeres matiz en HDInsight](hdinsight-hadoop-hue-linux.md). El matiz es una interfaz de usuario que hace que sea fácil crear, ejecutar y guardar trabajos cerdo y subárbol, así como examinar el almacenamiento predeterminado para su HDInsight clúster de web.

- [Instalar R en clústeres HDInsight](hdinsight-hadoop-r-scripts-linux.md): instrucciones sobre cómo usar clúster personalización para instalar y usar R en clústeres HDInsight Hadoop. R es un idioma de código abierto y el entorno para las estadísticas. Proporciona cientos de funciones estadísticas integradas y su propio lenguaje de programación que combina aspectos de la programación funcional y orientado a objetos. También proporciona capacidades gráficas extensas.

- [Instalar Solr en clústeres HDInsight](hdinsight-hadoop-solr-install-linux.md). Use la personalización de clúster para instalar Solr en clústeres HDInsight Hadoop. Solr le permite realizar operaciones de búsqueda eficaz de los datos almacenados.
