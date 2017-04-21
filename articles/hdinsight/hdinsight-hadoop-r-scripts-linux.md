<properties
    pageTitle="Instalar R a HDInsight de Linux | Microsoft Azure"
    description="Obtenga información sobre cómo instalar y usar R personalizar clústeres Hadoop basados en Linux."
    services="hdinsight"
    documentationCenter=""
    authors="Blackmist"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="hdinsight"
    ms.workload="big-data"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/20/2016"
    ms.author="larryfr"/>

# <a name="install-and-use-r-on-hdinsight-hadoop-clusters"></a>Instalar y usar R en clústeres HDInsight Hadoop

Puede instalar R en cualquier tipo de clúster en Hadoop en HDInsight con personalización de clúster de **Secuencia de comandos de acción** . Esto permite científicos de datos y los analistas utilizar R para implementar el marco de programación MapReduce/hilo eficaces procesar grandes cantidades de datos en clústeres Hadoop que se implementan en HDInsight.

> [AZURE.IMPORTANT] El [nivel premium](https://azure.microsoft.com/pricing/details/hdinsight/) que se ofrece para HDInsight incluye R Server como parte de su clúster HDInsight. Esto permite que las secuencias de comandos de R usar MapReduce y motor para ejecutar cálculos distribuidos. Para obtener más información, vea [Introducción al uso de servidor de R en HDInsight](hdinsight-hadoop-r-server-get-started.md). 


## <a name="what-is-r"></a>¿Qué es R?

El <a href="http://www.r-project.org/" target="_blank">Proyecto R sistemas estadísticos de</a> es un idioma de origen abierto y el entorno para las estadísticas. R proporciona cientos de compilación de funciones estadísticas y su propio lenguaje de programación que combina aspectos de la programación funcional y orientado a objetos. También proporciona capacidades gráficas extensas. R es el entorno de programación preferido para profesionales más estadísticos y científicos en una amplia variedad de campos.

Se pueden ejecutar secuencias de comandos de R en clústeres Hadoop en HDInsight que se personalizaran estaban campos con la acción de secuencia de comandos cuando creó para instalar el entorno de R. R es compatible con el almacenamiento de blobs de Windows Azure (WASB) para que los datos que se almacenan allí pueden procesarse mediante R en HDInsight.

## <a name="what-the-script-does"></a>¿Qué significa la secuencia de comandos

La acción de secuencia de comandos utilizada para instalar R en el clúster de HDInsight instala los siguientes paquetes Ubuntu, que proporcionan una instalación R básica:

* [r base](http://packages.ubuntu.com/precise/r-base): paquete Base R GNU
* [desarrollo de base de r](http://packages.ubuntu.com/precise/r-base-dev): paquetes la GNU R

También se instalan los siguientes paquetes de RHadoop, que proporciona integración con MapReduce y HDFS:

* [rmr2](https://github.com/RevolutionAnalytics/rmr2): permite a los desarrolladores R usar Hadoop MapReduce
* [rhdfs](https://github.com/RevolutionAnalytics/rhdfs): permite a los desarrolladores R usar HDFS Hadoop (WASB para HDInsight)

Además, se instalan los paquetes de R siguientes:

| Paquete R | Lo que proporciona |
| --------- | ---------------- |
| [rJava](https://cran.r-project.org/web/packages/rJava/index.html) | Un bajo nivel R a la interfaz de Java. |
| [RCPP](https://cran.r-project.org/web/packages/Rcpp/index.html) | Integración de R y C++. |
| [RJSONIO](https://cran.r-project.org/web/packages/RJSONIO/index.html) | Serializar o deserializar objetos R a JSON |
| [bitops](https://cran.r-project.org/web/packages/bitops/index.html) | Funciones de las operaciones bit a bit de vectores entero. |
| [Resumen](https://cran.r-project.org/web/packages/digest/index.html) | Crear resúmenes de Hash de cifrado de objetos R. |
| [funcional](https://cran.r-project.org/web/packages/functional/index.html) | Curry, redactar y otras funciones de orden superior |
| [reshape2](https://cran.r-project.org/web/packages/reshape2/index.html) | Flexible reestructurar y agregar datos. |
| [stringr](https://cran.r-project.org/web/packages/stringr/index.html) | Contenedores simples y consistentes para operaciones de cadena comunes. |
| [plyr](https://cran.r-project.org/web/packages/plyr/index.html) | Herramientas para dividir, aplicar y combinar datos. |
| [caTools](https://cran.r-project.org/web/packages/caTools/index.html) | Herramientas para mover las estadísticas de la ventana, GIF, base 64, ROC AUC, etcetera. |
| [stringdist](https://cran.r-project.org/web/packages/stringdist/index.html) | Aproximar coincidencia de cadenas y distancia funciones de cadena. |

## <a name="install-r-using-script-actions"></a>Instalar R usar acciones de Script

La acción de secuencia de comandos siguientes se usa para instalar R en un clúster de HDInsight. https://hdiconfigactions.BLOB.Core.Windows.NET/linuxrconfigactionv01/r-Installer-V01.sh
    
Esta sección proporciona instrucciones sobre cómo usar la secuencia de comandos para crear un nuevo clúster con el portal de Azure. 

> [AZURE.NOTE] PowerShell Azure, CLI Azure, el SDK de .NET HDInsight o administrador de recursos de Azure plantillas también sirve para aplicar acciones de script. También puede aplicar acciones de secuencia de comandos para clústeres ya en ejecución. Para obtener más información, vea [Personalizar HDInsight clústeres con acciones de Script](hdinsight-hadoop-customize-cluster-linux.md).

1. Iniciar el aprovisionamiento de un clúster con los pasos en [clústeres basados en Linux aprovisionar HDInsight](hdinsight-hadoop-provision-linux-clusters.md#portal), pero no completa de aprovisionamiento.

2. En el módulo **Configuración opcional** , seleccione **Acciones de secuencias de comandos**y proporcione la siguiente información:

    * __Nombre__: escriba un nombre descriptivo para la acción de secuencia de comandos.
    * __URI de secuencia de comandos__: https://hdiconfigactions.blob.core.windows.net/linuxrconfigactionv01/r-installer-v01.sh
    * __Cabeza__: Active esta opción
    * __Trabajo__: Active esta opción
    * __ZOOKEEPER__: Active esta opción para instalar en el nodo Zookeeper.
    * __Parámetros__: deje este campo en blanco

3. En la parte inferior de las **Acciones de secuencias de comandos**, use el botón **Seleccionar** para guardar la configuración. Por último, utilice el botón **Seleccionar** en la parte inferior del módulo **Opcional de configuración** para guardar la información de configuración opcional.

4. Continúe el clúster de aprovisionamiento como se describe en [clústeres basados en Linux aprovisionar HDInsight](hdinsight-hadoop-provision-linux-clusters.md#portal).

## <a name="run-r-scripts"></a>Ejecutar secuencias de comandos de R

Cuando haya finalizado el clúster de aprovisionamiento, siga los siguientes pasos para utilizar R para realizar una operación de MapReduce en el clúster.

1. Conectarse al clúster HDInsight mediante SSH:

        ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net

    Para obtener más información sobre cómo utilizar SSH con HDInsight, consulte lo siguiente:

    * [Utilizar SSH con basados en Linux Hadoop en HDInsight de Linux, Unix o OS X](hdinsight-hadoop-linux-use-ssh-unix.md)

    * [Utilizar SSH con basados en Linux Hadoop en HDInsight de Windows.](hdinsight-hadoop-linux-use-ssh-windows.md)

2. Desde el `username@hn0-CLUSTERNAME:~$` pregunta, escriba el comando siguiente para iniciar una sesión de R interactiva:

        R

3. Escriba el siguiente programa R. Esto genera los números del 1 al 100 y, a continuación, multiplica por 2.

        library(rmr2)
        ints = to.dfs(1:100)
        calc = mapreduce(input = ints, map = function(k, v) cbind(v, 2*v))

    La primera línea llama rmr2 de biblioteca RHadoop, que se utiliza para operaciones de MapReduce.

    La segunda línea genera valores 1-100, a continuación, almacena en el sistema de archivo de Hadoop mediante `to.dfs`.

    La tercera línea crea un proceso de MapReduce mediante la funcionalidad de rmr2 y comienza el procesamiento. Debería ver varias líneas desplácese anteriores a medida que comienza el procesamiento.

4. A continuación, utilice la siguiente para ver la ruta de acceso temporal que se almacena el resultado MapReduce en:

        print(calc())

    Se trata de algo similar a `/tmp/file5f615d870ad2`. Para ver el resultado real, use la siguiente:

        print(from.dfs(calc))

    El resultado debería tener este aspecto:

        [1,]  1 2
        [2,]  2 4
        .
        .
        .
        [98,]  98 196
        [99,]  99 198
        [100,] 100 200

5. Para salir R, escriba lo siguiente:

        q()


## <a name="next-steps"></a>Pasos siguientes

- [Instalar y usar clústeres matiz en HDInsight](hdinsight-hadoop-hue-linux.md). El matiz es una interfaz de usuario que hace que sea fácil crear, ejecutar y guardar trabajos cerdo y subárbol, así como examinar el almacenamiento predeterminado para su HDInsight clúster de web.

- [Instalar Giraph en clústeres HDInsight](hdinsight-hadoop-giraph-install.md). Use la personalización de clúster para instalar Giraph en clústeres HDInsight Hadoop. Giraph le permite realizar el procesamiento de gráfico con Hadoop y, a continuación, se puede usar con HDInsight de Azure.

- [Instalar Solr en clústeres HDInsight](hdinsight-hadoop-solr-install.md). Use la personalización de clúster para instalar Solr en clústeres HDInsight Hadoop. Solr le permite realizar operaciones de búsqueda eficaces en los datos almacenados.

- [Instalar matiz en clústeres HDInsight](hdinsight-hadoop-hue-linux.md). Use la personalización de clúster para instalar matiz en clústeres HDInsight Hadoop. El matiz es un conjunto de aplicaciones Web que se utilizan para interactuar con un clúster de Hadoop.

[hdinsight-cluster-customize]: hdinsight-hadoop-customize-cluster-linux.md
