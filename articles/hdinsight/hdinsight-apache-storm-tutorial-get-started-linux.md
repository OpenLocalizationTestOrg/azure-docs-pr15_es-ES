<properties
    pageTitle="Tutorial de tormenta Apache: Introducción a tormenta basados en Linux en HDInsight | Microsoft Azure"
    description="Introducción a análisis de datos grande con Apache tormenta y los ejemplos de tormenta Starter en HDInsight de Linux. Obtenga información sobre cómo usar tormenta para procesar datos en tiempo real."
    keywords="tormenta Apache, tutorial de tormenta apache, análisis de datos grande, starter tormenta"
    services="hdinsight"
    documentationCenter=""
    authors="Blackmist"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="java"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="10/12/2016"
   ms.author="larryfr"/>


# <a name="apache-storm-tutorial-get-started-with-the-storm-starter-samples-for-big-data-analytics-on-hdinsight"></a>Tutorial de tormenta Apache: empezar a trabajar con los ejemplos de tormenta Starter para análisis de datos grande en HDInsight

Tormenta Apache es un sistema de cálculo scalable, tolerancia a errores, distribuido y en tiempo real para el procesamiento de secuencias de datos. Con tormenta en HDInsight de Azure, puede crear un clúster de tormenta basada en nube que realiza el análisis de datos grande en tiempo real.

> [AZURE.NOTE] Los pasos de este artículo, crean un clúster de HDInsight basados en Linux. Para que conocer los pasos crear una tormenta de Windows en clúster HDInsight, consulte [tutorial tormenta Apache: Introducción a la muestra de tormenta Starter con el análisis de datos en HDInsight](hdinsight-apache-storm-tutorial-get-started.md)

## <a name="prerequisites"></a>Requisitos previos

[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

Debe tener las siguientes acciones para finalizar este tutorial Apache tormenta correctamente:

- **Suscripción de un Azure**. Vea la [versión de prueba gratuita de Azure obtener](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

- **Estar familiarizado con SSH y SCP**. Para obtener más información sobre cómo utilizar SSH y SCP con HDInsight, consulte lo siguiente:

    - **Clientes Linux, Unix o OS X**: vea [Usar SSH con basados en Linux Hadoop en HDInsight de Linux, OS X o Unix](hdinsight-hadoop-linux-use-ssh-unix.md)

    - **Los clientes de Windows**: vea [Usar SSH con basados en Linux Hadoop en HDInsight de Windows.](hdinsight-hadoop-linux-use-ssh-windows.md)

### <a name="access-control-requirements"></a>Requisitos de control de acceso

[AZURE.INCLUDE [access-control](../../includes/hdinsight-access-control-requirements.md)]

## <a name="create-a-storm-cluster"></a>Crear un clúster de tormenta

En esta sección, cree un clúster de versión 3.2 HDInsight (tormenta versión 0.9.3) mediante una plantilla de administrador de recursos de Azure. Para obtener información acerca de las versiones de HDInsight y los SLA, consulte [control de versiones de componente HDInsight](hdinsight-component-versioning.md). Para otros métodos de creación de clúster, consulte [crear HDInsight clústeres](hdinsight-hadoop-provision-linux-clusters.md).

1. Haga clic en la imagen siguiente para abrir la plantilla en el portal de Azure.         

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Farmtemplates%2Fcreate-linux-based-storm-cluster-in-hdinsight.json" target="_blank"><img src="https://acom.azurecomcdn.net/80C57D/cdn/mediahandler/docarticles/dpsmedia-prod/azure.microsoft.com/en-us/documentation/articles/hdinsight-hbase-tutorial-get-started-linux/20160201111850/deploy-to-azure.png" alt="Deploy to Azure"></a>
    
    La plantilla se encuentra en un contenedor de blob público *https://hditutorialdata.blob.core.windows.net/armtemplates/create-linux-based-storm-cluster-in-hdinsight.json*. 
   
2. Desde el módulo de parámetros, escriba lo siguiente:

    - **NombreDeClúster**: escriba un nombre para el clúster de Hadoop que se creará.
    - **Contraseña y nombre de inicio de sesión de clúster**: el nombre de inicio de sesión predeterminado es Admin.
    - **SSH nombre de usuario y contraseña**.
    
    Escriba estos valores.  Los necesitará más adelante en el tutorial.

    > [AZURE.NOTE] SSH se utiliza para acceder de forma remota el clúster HDInsight con una línea de comandos. El nombre de usuario y la contraseña que usa aquí se usa para conectarse al clúster a través de SSH. Además, el nombre de usuario SSH debe ser único, como crea una cuenta de usuario en todos los nodos de clúster de HDInsight. Los siguientes son algunos de los nombres de cuenta reservados para su uso por los servicios en el clúster y no se pueden usar como el nombre de usuario SSH:
    >
    > raíz, hdiuser, tormenta, hbase, ubuntu, zookeeper, hdfs, hilo, mapred, hbase, subárbol, oozie, falcon, sqoop, administración, tez, hcat, hdinsight zookeeper.

    > Para obtener más información sobre cómo utilizar SSH con HDInsight, consulte uno de los siguientes artículos:

    > * [Utilizar SSH con basados en Linux Hadoop en HDInsight de Linux, Unix o OS X](hdinsight-hadoop-linux-use-ssh-unix.md)
    > * [Utilizar SSH con basados en Linux Hadoop en HDInsight de Windows.](hdinsight-hadoop-linux-use-ssh-windows.md)

    
3. Haga clic en **Aceptar** para guardar los parámetros.

4 desde el módulo de **implementación personalizada** , haga clic en cuadro de lista desplegable de **grupo de recursos** y, a continuación, haga clic en **nuevo** para crear un nuevo grupo de recursos. El grupo de recursos es un contenedor que agrupa el clúster, la cuenta de almacenamiento dependientes y otros recursos vinculados.

5. Haga clic en **condiciones legales**y, a continuación, haga clic en **crear**.

6. Haga clic en **crear**. Verá un nuevo mosaico titulado implementación Submitting para su implementación de la plantilla. Tarda aproximadamente 20 minutos para crear el clúster y la base de datos SQL.


##<a name="run-a-storm-starter-sample-on-hdinsight"></a>Ejecutar un ejemplo de tormenta Starter en HDInsight

Los ejemplos de [tormenta starter](https://github.com/apache/storm/tree/master/examples/storm-starter) se incluyen en el clúster HDInsight. En los pasos siguientes, se ejecutará en el ejemplo de WordCount.

1. Conectarse al clúster HDInsight mediante SSH:

        ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net
        
    Si utiliza una contraseña para proteger su cuenta de usuario SSH, se le pedirá que lo especifique. Si utiliza una clave pública, tendrá que usar la `-i` parámetro para especificar la clave privada correspondiente. Por ejemplo, `ssh -i ~/.ssh/id_rsa USERNAME@CLUSTERNAME-ssh.azurehdinsight.net`.
        
    Para obtener más información sobre cómo utilizar SSH con HDInsight de Linux, consulte los siguientes artículos:
    
    * [Utilizar SSH con basados en Linux Hadoop en HDInsight de Linux, Unix o OS X](hdinsight-hadoop-linux-use-ssh-unix.md)

    * [Utilizar SSH con basados en Linux Hadoop en HDInsight de Windows.](hdinsight-hadoop-linux-use-ssh-windows)

2. Utilice el comando siguiente para iniciar una topología de ejemplo:

        storm jar /usr/hdp/current/storm-client/contrib/storm-starter/storm-starter-topologies-*.jar storm.starter.WordCountTopology wordcount
        
    > [AZURE.NOTE] La `*` parte del nombre de archivo se utiliza para que coincida con el número de versión, que cambia a medida que se actualiza HDInsight.

    Se iniciará la topología de ejemplo WordCount en el clúster, con un nombre descriptivo de 'wordcount'. Aleatoriamente generará oraciones y contar la repetición de cada palabra de la oración.

    > [AZURE.NOTE] Cuando envíe la topología de clúster, primero debe copiar el archivo jar contiene el clúster antes de utilizar el `storm` comando. Esto se puede conseguir usando la `scp` comando desde el cliente donde reside el archivo. Por ejemplo,`scp FILENAME.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:FILENAME.jar`
    >
    > En el ejemplo de WordCount y otros ejemplos de starter tormenta, ya están incluidas en el clúster en `/usr/hdp/current/storm-client/contrib/storm-starter/`.

##<a name="monitor-the-topology"></a>Supervisar la topología

La interfaz de usuario de tormenta ofrece una interfaz web para trabajar con topologías en ejecución y se incluye en el clúster HDInsight.

Siga los pasos siguientes para supervisar la topología utilizando la interfaz de usuario de tormenta:

1. Abra un explorador web a https://CLUSTERNAME.azurehdinsight.net/stormui, donde __NOMBREDECLÚSTER__ es el nombre del clúster. Se abrirá la interfaz de usuario de tormenta.

    > [AZURE.NOTE] Si se le solicita que proporcione un nombre de usuario y contraseña, escriba el Administrador de clústeres (Administrador) y contraseña que utilizó al crear el clúster.

2. En **la topología de resumen**, seleccione la entrada de **wordcount** en la columna **nombre** . Esta opción mostrará más información acerca de la topología.

    ![Panel de tormenta con información de la topología de tormenta Starter WordCount.](./media/hdinsight-apache-storm-tutorial-get-started-linux/topology-summary.png)

    Esta página proporciona la información siguiente:

    * **Estadísticas de topología** : información básica sobre el rendimiento de topología, organizadas por las ventanas de tiempo.

        > [AZURE.NOTE] Selección de una ventana de tiempo específico cambia la ventana de tiempo para la información que aparece en otras secciones de la página.

    * **Spouts** - información básica sobre spouts, incluido el último error devuelto por cada pitorro.

    * **Pernos** - información básica sobre pernos.

    * **Configuración de la topología** : información detallada sobre la configuración de la topología.

    Esta página también proporciona acciones que pueden realizar en la topología:

    * **Activar** - procesamiento de currículos de topología desactivada.

    * **Desactivar** - pausa una topología de ejecución.

    * **Equilibrar** - ajusta el paralelismo de la topología. Debe equilibrar topologías ejecución después de haber cambiado el número de nodos en el clúster. Esto permite la topología para ajustar paralelismo a compensa la del número mayor o menor de nodos en el clúster. Para obtener más información, vea [comprender el paralelismo de una topología de tormenta](http://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html).

    * **Eliminar** - termina una topología de tormenta tras el tiempo de espera.

3. Desde esta página, seleccione una entrada de la sección **Spouts** o **pernos** . Esta opción mostrará información sobre el componente seleccionado.

    ![Panel de tormenta con información sobre componentes seleccionados.](./media/hdinsight-apache-storm-tutorial-get-started-linux/component-summary.png)

    Esta página muestra la siguiente información:

    * **Estadísticas de pitorro/rayo** - información básica sobre el rendimiento de componente, organizadas por las ventanas de tiempo.

        > [AZURE.NOTE] Selección de una ventana de tiempo específico cambia la ventana de tiempo para la información que aparece en otras secciones de la página.

    * **Estadísticas de entrada** (solo pernos) - información sobre componentes que producen datos consumidos por el rayo.

    * **Estadísticas de resultados** : información sobre datos emitidos por este rayo.

    * **Elementos de ejecución** - información sobre las instancias de este componente.

    * **Errores** - errores producidos por este componente.

4. Al ver los detalles de un pitorro o los pernos, seleccione una entrada de la columna de **puerto** en la sección **elementos de ejecución** ver detalles de una instancia específica del componente.

        2015-01-27 14:18:02 b.s.d.task [INFO] Emitting: split default ["with"]
        2015-01-27 14:18:02 b.s.d.task [INFO] Emitting: split default ["nature"]
        2015-01-27 14:18:02 b.s.d.executor [INFO] Processing received message source: split:21, stream: default, id: {}, [snow]
        2015-01-27 14:18:02 b.s.d.task [INFO] Emitting: count default [snow, 747293]
        2015-01-27 14:18:02 b.s.d.executor [INFO] Processing received message source: split:21, stream: default, id: {}, [white]
        2015-01-27 14:18:02 b.s.d.task [INFO] Emitting: count default [white, 747293]
        2015-01-27 14:18:02 b.s.d.executor [INFO] Processing received message source: split:21, stream: default, id: {}, [seven]
        2015-01-27 14:18:02 b.s.d.task [INFO] Emitting: count default [seven, 1493957]

    Puede ver que la palabra de **siete** se ha producido 1493957 horas de este tipo de datos. Que es cuántas veces se ha encontrado desde que se inició esta topología.

##<a name="stop-the-topology"></a>Detener la topología

Vuelva a la página de **Resumen de la topología** de la topología de recuento de palabras y, a continuación, seleccione el botón **Eliminar** de la sección **acciones de topología** . Cuando se le pida, escriba 10 para los segundos de espera antes de detener la topología. Después del período de tiempo de espera, la topología dejará de aparecer al visitar la sección de la **Interfaz de usuario de tormenta** del panel.

##<a name="delete-the-cluster"></a>Eliminar el clúster

[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

##<a id="next"></a>Pasos siguientes

En este tutorial Apache tormenta, se utiliza el Starter tormenta para aprender a crear una tormenta en clúster HDInsight y usar el panel de tormenta implementar, supervisar y administrar topologías de tormenta. A continuación, obtenga información sobre cómo [topologías basada en Java desarrollar con experto](hdinsight-storm-develop-java-topology.md).

Si ya está familiarizado con desarrollo topologías basada en Java y desea implementar una topología existente a HDInsight, consulte [implementar y administrar topologías tormenta Apache en HDInsight](hdinsight-storm-deploy-monitor-topology-linux.md).

Si es un desarrollador de .NET, puede crear C# o híbrido C# / topologías Java mediante Visual Studio. Para obtener más información, vea [desarrollar C# topologías para tormenta Apache en HDInsight con Hadoop tools para Visual Studio](hdinsight-storm-develop-csharp-visual-studio-topology.md).

Por ejemplo topologías que se pueden usar con tormenta en HDInsight, consulte los siguientes ejemplos:

    * [Topologías de ejemplo para tormenta en HDInsight](hdinsight-storm-example-topology.md)

[apachestorm]: https://storm.incubator.apache.org
[stormdocs]: http://storm.incubator.apache.org/documentation/Documentation.html
[stormstarter]: https://github.com/apache/storm/tree/master/examples/storm-starter
[stormjavadocs]: https://storm.incubator.apache.org/apidocs/
[azureportal]: https://manage.windowsazure.com/
[hdinsight-provision]: hdinsight-provision-clusters.md
[preview-portal]: https://portal.azure.com/
