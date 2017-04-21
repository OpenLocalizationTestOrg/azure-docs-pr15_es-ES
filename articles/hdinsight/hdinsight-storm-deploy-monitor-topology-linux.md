<properties
   pageTitle="Implementar y administrar topologías tormenta Apache en HDInsight de Linux | Microsoft Azure"
   description="Obtenga información sobre cómo implementar, supervisar y administrar topologías Apache tormenta con el panel de tormenta en HDInsight de Linux. Usar Hadoop tools para Visual Studio."
   services="hdinsight"
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
   ms.date="09/07/2016"
   ms.author="larryfr"/>

# <a name="deploy-and-manage-apache-storm-topologies-on-linux-based-hdinsight"></a>Implementar y administrar topologías tormenta Apache en HDInsight de Linux

En este documento, aprenda los conceptos básicos de administración y supervisión topologías tormenta quedando tormenta basados en Linux en clústeres HDInsight.

> [AZURE.IMPORTANT] Los pasos de este artículo requieren una tormenta basados en Linux en clúster HDInsight. Para obtener información sobre cómo implementar y supervisar topologías en HDInsight de Windows, vea [implementar y administrar topologías tormenta Apache en HDInsight de Windows](hdinsight-storm-deploy-monitor-topology.md)

## <a name="prerequisites"></a>Requisitos previos

* **Tormenta basados en A Linux en clúster HDInsight**: consulte [Introducción a tormenta Apache en HDInsight](hdinsight-apache-storm-tutorial-get-started-linux.md) para conocer los pasos sobre cómo crear un clúster

* **Estar familiarizado con SSH y SCP**: para obtener más información sobre cómo utilizar SSH y SCP con HDInsight, consulte lo siguiente:

    * **Clientes Linux, Unix o OS X**: vea [Usar SSH con basados en Linux Hadoop en HDInsight de Linux, OS X o Unix](hdinsight-hadoop-linux-use-ssh-unix.md)

    * **Los clientes de Windows**: vea [Usar SSH con basados en Linux Hadoop en HDInsight de Windows.](hdinsight-hadoop-linux-use-ssh-windows.md)

* **SCP un cliente**: se proporciona con todos los sistemas Linux, Unix y OS X. Para los clientes de Windows, se recomienda PSCP, que está disponible en la [página de descarga de PuTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).

## <a name="start-a-storm-topology"></a>Iniciar una topología de tormenta

### <a name="using-ssh-and-the-storm-command"></a>Uso de SSH y el comando de tormenta

1. Utilizar SSH para conectar con el clúster de HDInsight. Reemplazar el **nombre de usuario** el nombre de su inicio de sesión SSH. Reemplazar **NOMBREDECLÚSTER** con su nombre de clúster HDInsight:

        ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net

    Para obtener más información sobre el uso de SSH para conectarse a su clúster HDInsight, consulte los siguientes documentos:
    
    * **Clientes Linux, Unix o OS X**: vea [Usar SSH con basados en Linux Hadoop en HDInsight de Linux, OS X o Unix](hdinsight-hadoop-linux-use-ssh-unix.md)
    
    * **Los clientes de Windows**: vea [Usar SSH con basados en Linux Hadoop en HDInsight de Windows.](hdinsight-hadoop-linux-use-ssh-windows.md)

2. Utilice el comando siguiente para iniciar una topología de ejemplo:

        storm jar /usr/hdp/current/storm-client/contrib/storm-starter/storm-starter-topologies-0.9.3.2.2.4.9-1.jar storm.starter.WordCountTopology WordCount

    Se iniciará la topología de ejemplo WordCount en el clúster. Aleatoriamente generará oraciones y contar la repetición de cada palabra de la oración.

    > [AZURE.NOTE] Cuando envíe la topología de clúster, primero debe copiar el archivo jar contiene el clúster antes de utilizar el `storm` comando. Esto se puede conseguir usando la `scp` comando desde el cliente donde reside el archivo. Por ejemplo,`scp FILENAME.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:FILENAME.jar`
    >
    > En el ejemplo de WordCount y otros ejemplos de starter tormenta, ya están incluidas en el clúster en `/usr/hdp/current/storm-client/contrib/storm-starter/`.

### <a name="programmatically"></a>Mediante programación

Mediante programación puede implementar una topología a tormenta en HDInsight comunicarse con el servicio de Nimbus alojado en el clúster. [https://github.com/Azure-Samples/hdinsight-Java-Deploy-Storm-Topology](https://github.com/Azure-Samples/hdinsight-java-deploy-storm-topology) proporciona un ejemplo de aplicación de Java que muestra cómo implementar e iniciar una topología a través del servicio de Nimbus.

## <a name="monitor-and-manage-using-the-storm-command"></a>Supervisar y administrar con el comando de tormenta

La `storm` utilidad le permite trabajar con topologías en ejecución de la línea de comandos. A continuación se muestra una lista de los comandos más utilizados. Usar `storm -h` para obtener una lista completa de comandos.

### <a name="list-topologies"></a>Topologías de lista

Use el comando siguiente para mostrar todas ejecutando topologías:

    storm list
    
Esto devolverá información similar al siguiente:

    Topology_name        Status     Num_tasks  Num_workers  Uptime_secs
    -------------------------------------------------------------------
    WordCount            ACTIVE     29         2            263

### <a name="deactivate-and-reactivate"></a>Desactivar y reactivar

Desactivar una topología pone en pausa hasta que haya eliminado o reactivar. Para desactivar y reactivar, utilice la siguiente:

    storm Deactivate TOPOLOGYNAME
    
    storm Activate TOPOLOGYNAME

### <a name="kill-a-running-topology"></a>Eliminar una topología de ejecución

Topologías de tormenta, una vez iniciadas, seguirá ejecutando hasta que se detiene. Para detener una topología, utilice el siguiente comando:

    storm stop TOPOLOGYNAME

### <a name="rebalance"></a>Equilibrar

Volver a equilibrar una topología permite que el sistema revisar el paralelismo de la topología. Por ejemplo, si cambia el tamaño de clúster para agregar más notas, volver a equilibrar permitirá una topología de ejecución realizar el uso de los nodos nuevos.

> [AZURE.WARNING] Volver a equilibrar primero una topología desactiva la topología, a continuación, vuelve a distribuir los trabajadores uniformemente en el clúster y por último devuelve la topología al estado que tenía antes de volver a equilibrar ocurrido. Así que si estaba activa la topología, estará activo nuevamente. Si se ha desactivado, permanecerá desactivado.

    storm rebalance TOPOLOGYNAME

## <a name="monitor-and-manage-using-the-storm-ui"></a>Supervisar y administrar el uso de la interfaz de usuario de tormenta

La interfaz de usuario de tormenta ofrece una interfaz web para trabajar con topologías en ejecución y se incluye en el clúster HDInsight. Para ver la interfaz de usuario de tormenta, use un explorador web para abrir __https://CLUSTERNAME.azurehdinsight.net/stormui__, donde __NOMBREDECLÚSTER__ es el nombre del clúster.

> [AZURE.NOTE] Si se le solicita que proporcione un nombre de usuario y contraseña, escriba el Administrador de clústeres (Administrador) y contraseña que utilizó al crear el clúster.


### <a name="main-page"></a>Página principal

La página principal de la interfaz de usuario de tormenta proporciona la información siguiente:

* **Resumen de clúster**: información básica sobre el clúster de tormenta.

* **Topología de resumen**: una lista de ejecución topologías. Use los vínculos de esta sección para obtener más información sobre topologías específicas.

* **Supervisor resumen**: información sobre el supervisor tormenta.

* **Configuración de Nimbus**: configuración de Nimbus para el clúster.

### <a name="topology-summary"></a>Resumen de topología

Seleccionar un vínculo en la sección de **Resumen de topología** muestra la siguiente información acerca de la topología:

* **Topología de resumen**: información básica sobre la topología.

* **Acciones de topología**: acciones que puede realizar para la topología.

  * **Activar**: procesamiento de currículos de topología desactivada.

  * **Desactivar**: pausa una topología de ejecución.

  * **Equilibrar**: ajusta el paralelismo de la topología. Debe equilibrar topologías ejecución después de haber cambiado el número de nodos en el clúster. Esto permite la topología para ajustar paralelismo de compensación para el aumentar o reducir el número de nodos en el clúster.

    Para obtener más información, vea <a href="http://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html" target="_blank">comprender el paralelismo de una topología de tormenta</a>.

  * **Eliminar**: finaliza una topología de tormenta después el tiempo de espera.

* **Estadísticas de topología**: estadísticas acerca de la topología. Use los vínculos en la columna de la **ventana** para establecer el intervalo de tiempo para las demás entradas en la página.

* **Spouts**: el spouts usados por la topología. Use los vínculos de esta sección para obtener más información sobre spouts específicas.

* **Pernos**: los pernos de la topología. Use los vínculos de esta sección para obtener más información acerca de pernos específicos.

* **Configuración de la topología**: la configuración de la topología seleccionada.

### <a name="spout-and-bolt-summary"></a>Pitorro y resumen de rayo

Seleccionar un pitorro de las secciones **Spouts** o **pernos** muestra la información siguiente sobre el elemento seleccionado:

* **Resumen de componentes**: información básica sobre el pitorro o los pernos.

* **Estadísticas de pitorro/rayo**: estadísticas acerca del pitorro o rayo. Use los vínculos en la columna de la **ventana** para establecer el intervalo de tiempo para las demás entradas en la página.

* **Estadísticas de entrada** (solo de pernos): información sobre las secuencias de entrada consumido por el rayo.

* **Estadísticas de resultados**: información sobre las secuencias emitido por esto chorros o pernos.

* **Elementos de ejecución**: información sobre las instancias del pitorro o rayo. Seleccione la entrada de **puerto** para un elemento de ejecución específico ver un registro de información de diagnóstico fabrican para esta instancia.

* **Errores**: cualquier información de error para este chorros o pernos.

## <a name="rest-api"></a>API DE REST

La interfaz de usuario de tormenta se basa en la API de REST, para que pueda realizar administración y supervisión funcionalidad mediante la API de REST similares. Puede usar la API de REST para crear herramientas personalizadas para la administración y supervisión topologías tormenta.

Para obtener más información, vea [API de REST de tormenta de interfaz de usuario](http://storm.apache.org/releases/0.9.6/STORM-UI-REST-API.html). La siguiente información es específica del uso de la API de REST con Apache tormenta en HDInsight.

> [AZURE.IMPORTANT] La API de REST de tormenta no está disponible públicamente a través de internet y debe tener acceso a mediante un túnel SSH al nodo principal del clúster de HDInsight. Para obtener información sobre cómo crear y usar un túnel SSH, consulte [Usar SSH túnel para tener acceso a la interfaz de usuario de web Ambari, ResourceManager, JobHistory, NameNode, Oozie y otra de la interfaz de usuario de web](hdinsight-linux-ambari-ssh-tunnel.md).

### <a name="base-uri"></a>URI básico

Está disponible en el nodo principal en el URI base de la API de REST en clústeres basados en Linux HDInsight **api/https://HEADNODEFQDN:8744/v1/**; Sin embargo, el nombre de dominio del nodo principal se genera durante la creación de un clúster y no es estático.

Puede encontrar el nombre de dominio completo (FQDN) para el nodo de cabeza de varias maneras diferentes:

* __Desde un SSH sesión__: Use el comando `headnode -f` de una sesión de SSH al clúster.

* __Desde la Ambari Web__: seleccione __Servicios__ en la parte superior de la página y luego seleccione __tormenta__. En la pestaña __Resumen__ , seleccione __Servidor de interfaz de usuario de tormenta__. El nombre completo del nodo que se está ejecutando la interfaz de usuario de tormenta y API de REST estará en la parte superior de la página.

* __API de REST de Ambari__: Use el comando `curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/STORM/components/STORM_UI_SERVER"` para recuperar información sobre el nodo de la interfaz de usuario de tormenta y API de REST se ejecutan en. Reemplazar la __contraseña__ con la contraseña de administrador para el clúster. Reemplazar __NOMBREDECLÚSTER__ con el nombre de clúster. En la respuesta, la entrada de "host_name" contiene el nombre completo del nodo.

### <a name="authentication"></a>Autenticación

Las solicitudes de la API de REST deben utilizar **autenticación básica**, para usar el nombre del Administrador de clúster de HDInsight y la contraseña.

> [AZURE.NOTE] Porque la autenticación básica se envía con texto no cifrado, debe usar **siempre** HTTPS para proteger las comunicaciones con el clúster.

### <a name="return-values"></a>Devolver valores

Información que se devuelve de la API de REST sólo puede ser utilizable desde dentro del clúster o máquinas virtuales de Windows en la misma red Virtual de Azure como el clúster. Por ejemplo, el nombre de dominio completo (FQDN) devuelto para servidores Zookeeper no será accesible desde Internet.

## <a name="next-steps"></a>Pasos siguientes

Ahora que ha aprendido a implementar y supervisar topologías mediante el panel de tormenta, obtenga información sobre cómo [topologías basada en Java desarrollar con experto](hdinsight-storm-develop-java-topology.md).

Para obtener una lista de más topologías de ejemplo, vea [topologías de ejemplo para tormenta en HDInsight](hdinsight-storm-example-topology.md).
