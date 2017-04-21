<properties
    pageTitle="Características de alta disponibilidad de basados en Linux HDInsight (Hadoop) | Microsoft Azure"
    description="Obtenga información sobre cómo basados en Linux HDInsight clústeres mejoran la confiabilidad y disponibilidad mediante un nodo de cabeza adicional. Aprenderá cómo afecta esto a servicios de Hadoop como Ambari y subárbol, así como la conectarse individualmente a cada nodo principal con SSH."
    services="hdinsight"
    editor="cgronlun"
    manager="jhubbard"
    authors="Blackmist"
    documentationCenter=""
    tags="azure-portal"/>

<tags
    ms.service="hdinsight"
    ms.workload="big-data"
    ms.tgt_pltfrm="na"
    ms.devlang="multiple"
    ms.topic="article"
    ms.date="09/13/2016"
    ms.author="larryfr"/>

#<a name="availability-and-reliability-of-hadoop-clusters-in-hdinsight"></a>Disponibilidad y confiabilidad de clústeres Hadoop HDInsight

Hadoop logra alta disponibilidad y confiabilidad al distribuir copias redundantes de datos y servicios a través de los nodos en un clúster. Sin embargo distribuciones estándar de Hadoop normalmente tienen un solo nodo central. Cualquier interrupción del nodo principal solo puede hacer que el clúster deje de funcionar.

Para solucionar este problema, clústeres basados en Linux HDInsight de Azure proporcionan dos nodos cabezales para aumentar la disponibilidad y confiabilidad de los servicios de Hadoop y trabajos que se ejecutan.

> [AZURE.NOTE] Los pasos que se utilizan en este documento son específicos para clústeres HDInsight basados en Linux. Si está utilizando un clúster basado en Windows, vea [disponibilidad y confiabilidad de clústeres basado en Windows Hadoop HDInsight](hdinsight-high-availability.md) para obtener información específica de Windows.

##<a name="understanding-the-nodes"></a>Descripción de los nodos

Nodos en un clúster de HDInsight se implementan mediante máquinas virtuales de Azure. En caso de que se produce un error en un nodo, se desconecta y se creará un nuevo nodo para reemplazar el nodo del error. Mientras el nodo está sin conexión, se utilizará otro nodo del mismo tipo hasta que se conecte el nodo nuevo.

> [AZURE.NOTE] Si el nodo es analizar los datos cuando se produce un error, se pierden su progreso en la tarea. Se volverá a enviar el trabajo que estaba trabajando nodo erróneo a otro nodo.

Las secciones siguientes describen los tipos de nodo individual que se usan con HDInsight. No todos los tipos de nodo se usan para un tipo de clúster. Por ejemplo, un tipo de clúster Hadoop no tendrán los nodos Nimbus. Para obtener más información sobre los nodos usadas por tipos de clúster HDInsight, consulte la sección tipos de clúster de [clústeres basados en Linux crear Hadoop HDInsight](hdinsight-hadoop-provision-linux-clusters.md#cluster-types).

###<a name="head-nodes"></a>Nodos de cabeza

Algunas implementaciones de Hadoop tienen un único nodo principal que aloja servicios y componentes que administran el error de nodos de trabajo sin problemas. Pero cualquier interrupciones de servicios principales que se ejecutan en el nodo principal haría que el clúster deje de funcionar.

HDInsight clústeres proporcionan un nodo secundario principal, que permite servicios principales y componentes para continuar para que se ejecute el nodo secundario si se produce un error en el principal.

> [AZURE.IMPORTANT] Ambos nodos cabezales están activos y en ejecución en el clúster al mismo tiempo. Algunos servicios, como HDFS o hilo, sólo están activos en un nodo principal a cualquier dada hora (reserva en el otro nodo central). Otros servicios como HiveServer2 o MetaStore subárbol están activos en ambos nodos de cabeza al mismo tiempo.

Nodos de cabeza (y otros nodos en HDInsight,) tiene un valor numérico como parte del nombre de host del nodo. Por ejemplo, `hn0-CLUSTERNAME` o `hn4-CLUSTERNAME`. 

> [AZURE.IMPORTANT] No se asocia el valor numérico con si un nodo es primaria o secundaria; el valor numérico sólo está presente para proporcionar un nombre único para cada nodo.

###<a name="nimbus-nodes"></a>Nodos de Nimbus

Clústeres de tormenta, los nodos Nimbus proporcionan funcionalidad similar a la Hadoop JobTracker distribuir y supervisión procesamiento a través de nodos de trabajo. HDInsight proporciona 2 nodos de Nimbus para el tipo de clúster de tormenta.

###<a name="zookeeper-nodes"></a>Nodos de zookeeper

Nodos de [zooKeeper](http://zookeeper.apache.org/ ) (ZKs) se usan para la elección de relleno de servicios principales en centrales nodos y para asegurarse de que los servicios, puertas de enlace y los nodos de datos (trabajo) saben qué nodo principal está activo en un servicio de patrón. De forma predeterminada, HDInsight proporciona 3 nodos de ZooKeeper.

###<a name="worker-nodes"></a>Nodos de trabajo

Nodos de trabajo realizan el análisis de datos reales cuando se envía un trabajo al clúster. Si se produce un error en un nodo de trabajo, la tarea que estaba realizando se enviarán a otro nodo de trabajo. De forma predeterminada, HDInsight creará 4 nodos de trabajo; Sin embargo, puede cambiar este número para adaptarla a sus necesidades durante la creación de clúster y después de la creación de clúster.

###<a name="edge-node"></a>Nodo del borde

Un nodo del borde conocidas no participa en análisis de datos en el clúster, pero se usa por desarrolladores o científicos de datos cuando se trabaja con Hadoop. El nodo del borde reside en la misma red Virtual de Azure que los demás nodos del clúster y puede tener acceso directo a todos los demás nodos. Puesto que no participa en análisis de datos para el clúster, puede usar sin ningún problema de poner los recursos fuera de servicios de Hadoop críticos o trabajos de análisis.

Actualmente, R Server HDInsight es el único tipo de clúster que proporciona un nodo del borde de forma predeterminada. Para servidor de R en HDInsight, se utiliza el nodo del borde código de prueba R localmente en el nodo antes de enviarlo al clúster de procesamiento distribuido.

[Crear un clúster basados en Linux HDInsight con matiz en un nodo del borde](https://azure.microsoft.com/documentation/templates/hdinsight-linux-with-hue-on-edge-node/) es un ejemplo de plantilla que se pueden usar para crear un tipo de clúster de Hadoop que tiene un nodo del borde.


## <a name="accessing-the-nodes"></a>Obtener acceso a los nodos

Acceso al clúster a través de internet se proporciona a través de una puerta de enlace público y está limitado a conectarse a los nodos de cabeza y (si se trata de un servidor R en clúster de HDInsight) el nodo del borde. Acceso a los servicios que se ejecutan en los nodos del cabezales no se aplique al tener varios nodos centrales, como la puerta de enlace público enruta las solicitudes al nodo principal que aloja el servicio solicitado. Por ejemplo, si Ambari se hospeda actualmente en el nodo principal secundario, la puerta de enlace redirige las solicitudes entrantes Ambari a ese nodo.

Al tener acceso al clúster mediante SSH, conectar a través de puerto 22 (el valor predeterminado para SSH,) se conectará al nodo principal primario; conectar a través de puerto 23 se conectará al nodo secundario principal. Por ejemplo, `ssh username@mycluster-ssh.azurehdinsight.net` se conectará al nodo principal principal del clúster denominado __miClúster__.

> [AZURE.NOTE] Esto también se aplica a protocolos basados en SSH, como el SSH archivo transferir protocolo (SFTP).

También se puede acceder el nodo del borde proporcionado con el servidor de R en clústeres HDInsight directamente mediante SSH a través de puerto 22. Por ejemplo, `ssh username@RServer.mycluster.ssh.azurehdinsight.net` se conectará el nodo del borde para un servidor R en clúster HDInsight denominado __miClúster__. 

### <a name="internal-fully-qualified-domain-names-fqdn"></a>Nombres de dominio completo interno (FQDN)

Nodos en un clúster de HDInsight tienen una dirección IP y FQDN que solo se puede acceder desde el clúster (por ejemplo, una sesión SSH para el nodo principal o un trabajo se ejecuta en el clúster.) interno Al tener acceso a servicios en el clúster mediante la dirección IP o FQDN interna, debe usar Ambari para comprobar la dirección IP o FQDN usar al obtener acceso al servicio.

Por ejemplo, el servicio de Oozie sólo se puede ejecutar en un nodo principal y el uso del `oozie` comando de una sesión de SSH requiere la dirección URL del servicio. Puede recuperar de Ambari mediante el siguiente comando:

    curl -u admin:PASSWORD "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/configurations?type=oozie-site&tag=TOPOLOGY_RESOLVED" | grep oozie.base.url

Devolverá un valor similar al siguiente, que contiene la dirección URL interna para usar con la `oozie` comando:

    "oozie.base.url": "http://hn0-CLUSTERNAME-randomcharacters.cx.internal.cloudapp.net:11000/oozie"

### <a name="accessing-other-node-types"></a>Obtener acceso a otros tipos de nodo

Puede conectarse a los nodos que no sean accesibles directamente a través de internet usando los métodos siguientes.

* __SSH__: una vez conectado a un nodo principal con SSH, a continuación, puede usar SSH desde el nodo principal para conectarse a otros nodos en el clúster.
* __Túnel SSH__: si necesita tener acceso a un servicio web hospedado en uno de los nodos que no se exponen en internet, debe [usar un túnel SSH](hdinsight-linux-ambari-ssh-tunnel.md).
* __Red Virtual de Azure__: si el clúster HDInsight es parte de una red Virtual de Azure, todos los recursos en la misma red Virtual directamente pueden obtener acceso a todos los nodos en el clúster.

## <a name="how-to-check-on-a-service-status"></a>¿Cómo puedo comprobar en un estado del servicio

La interfaz de usuario de Web Ambari o la API de REST de Ambari puede utilizarse para comprobar el estado de los servicios que se ejecutan en los nodos del cabezales.

###<a name="ambari-web-ui"></a>Interfaz de usuario de Web Ambari

La interfaz de usuario de Web Ambari está visible en https://CLUSTERNAME.azurehdinsight.net. Reemplazar **NOMBREDECLÚSTER** con el nombre del clúster. Si se le solicita, escriba las credenciales de usuario HTTP para el clúster. El nombre de usuario HTTP predeterminado es **admin** y la contraseña es la contraseña que especificó al crear el clúster.

Cuando llegan en la página Ambari, los servicios instalados se mostrarán en la parte izquierda de la página.

![Servicios instalados](./media/hdinsight-high-availability-linux/services.png)

Hay una serie de iconos que pueden aparecer junto a un servicio para indicar el estado. Alertas relacionadas con un servicio pueden verse con el vínculo de **alertas** en la parte superior de la página. Puede seleccionar cada servicio para ver más información en él.

Mientras la página servicio proporciona información sobre el estado y la configuración de cada servicio, no se proporciona información sobre qué nodo principal se está ejecutando el servicio. Para ver esta información, utilice el vínculo de **Hosts** en la parte superior de la página. Esta opción mostrará hosts dentro del clúster, incluidos los nodos de cabeza.

![lista de hosts](./media/hdinsight-high-availability-linux/hosts.png)

Seleccionando el vínculo para uno de los nodos de cabeza mostrará los servicios y componentes se ejecutan en ese nodo.

![Estado del componente](./media/hdinsight-high-availability-linux/nodeservices.png)

###<a name="ambari-rest-api"></a>API de REST de Ambari

La API de REST Ambari está disponible a través de internet y la puerta de enlace público controla las solicitudes de enrutamiento al nodo principal que aloja actualmente la API de REST.

Puede usar el comando siguiente para comprobar el estado de un servicio a través de la API de REST de Ambari:

    curl -u admin:PASSWORD https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/SERVICENAME?fields=ServiceInfo/state

* Reemplazar la **contraseña** con el usuario HTTP (admin), contraseña de la cuenta

* Reemplazar **NOMBREDECLÚSTER** con el nombre del clúster

* Reemplazar **SERVICENAME** con el nombre del servicio para comprobar el estado de

Por ejemplo, para comprobar el estado del servicio **HDFS** en un clúster denominado **miClúster**con una contraseña de **contraseña**, utilizar lo siguiente:

    curl -u admin:password https://mycluster.azurehdinsight.net/api/v1/clusters/mycluster/services/HDFS?fields=ServiceInfo/state

La respuesta será similar al siguiente:

    {
      "href" : "http://hn0-CLUSTERNAME.randomcharacters.cx.internal.cloudapp.net:8080/api/v1/clusters/mycluster/services/HDFS?fields=ServiceInfo/state",
      "ServiceInfo" : {
        "cluster_name" : "mycluster",
        "service_name" : "HDFS",
        "state" : "STARTED"
      }
    }

La dirección URL indica que el servicio se está ejecutando en un nodo principal denominado __NOMBREDECLÚSTER hn0__.

El estado le indica que el servicio se está ejecutando o **iniciado**.

Si no sabe qué servicios están instalados en el clúster, puede usar las siguientes acciones para recuperar una lista:

    curl -u admin:PASSWORD https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services

####<a name="service-components"></a>Componentes de servicio

Servicios pueden contener componentes que desea comprobar el estado de forma individual. Por ejemplo, HDFS contiene el componente NameNode. Para ver información sobre un componente, el comando sería:

    curl -u admin:PASSWORD https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/SERVICE/components/component

Si no sabe qué componentes se proporcionan un servicio, puede usar las siguientes acciones para recuperar una lista:

    curl -u admin:PASSWORD https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/SERVICE/components/component
    
## <a name="how-to-access-log-files-on-the-head-nodes"></a>Cómo obtener acceso a archivos de registro en los nodos del cabezales

###<a name="ssh"></a>SSH

Mientras está conectado a un nodo principal a través de SSH, archivos de registro se encuentran en **/var/log**. Por ejemplo, **/var/log/hadoop-yarn/yarn** contienen registros para hilo.

Cada nodo principal puede tener entradas de registro único, por lo que debe comprobar los registros de ambas.

###<a name="sftp"></a>SFTP

También puede conectar con el nodo principal mediante el SSH File Transfer Protocol o protocolo de transferencia de archivo seguro (SFTP) y descargar los archivos de registro directamente.

De forma similar a un cliente SSH, cuando se conecta al clúster debe proporcionar el nombre de cuenta de usuario SSH y la dirección SSH del clúster. Por ejemplo, `sftp username@mycluster-ssh.azurehdinsight.net`. También debe proporcionar la contraseña de la cuenta cuando se le solicite o proporcionar un mediante clave pública la `-i` parámetro.

Una vez conectado, se le presenta una `sftp>` símbolo del sistema. En este aviso, puede cambiar los directorios, cargar y descargar archivos. Por ejemplo, los comandos siguientes cambie los directorios al directorio **/var/log/hadoop/hdfs** y, a continuación, descargar todos los archivos en el directorio.

    cd /var/log/hadoop/hdfs
    get *

Para obtener una lista de comandos disponibles, escriba `help` en la `sftp>` símbolo del sistema.

> [AZURE.NOTE] También hay interfaces gráficas que le permiten visualizar el sistema de archivos cuando conectado con SFTP. Por ejemplo, [MobaXTerm](http://mobaxterm.mobatek.net/) le permite examinar el sistema de archivos con una interfaz similar al explorador de Windows.


###<a name="ambari"></a>Ambari

> [AZURE.NOTE] Acceso a archivos de registro a través de Ambari requiere un túnel SSH, como los sitios web para los servicios individuales no se exponen públicamente en Internet. Para obtener información sobre el uso de un túnel SSH, consulte [Usar SSH túnel para tener acceso a la interfaz de usuario de web Ambari, ResourceManager, JobHistory, NameNode, Oozie y otra de la interfaz de usuario de web](hdinsight-linux-ambari-ssh-tunnel.md).

Desde la interfaz de usuario de Web Ambari, seleccione el servicio que desea ver los registros (por ejemplo, hilo) y, a continuación, use **Vínculos rápidos** para seleccionar qué nodo principal para ver los registros.

![Utilizar vínculos rápidos para ver los registros](./media/hdinsight-high-availability-linux/viewlogs.png)

## <a name="how-to-configure-the-node-size"></a>Cómo configurar el tamaño de nodo ##

El tamaño de la un nodo sólo pueden seleccionarse durante la creación de clúster. Puede encontrar una lista de los distintos tamaños VM disponibles para HDInsight, incluidos el principal, la memoria y el almacenamiento local para cada uno, en la [página de precios HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/).

Al crear un nuevo clúster, puede especificar el tamaño de los nodos. Los siguientes proporcionan información sobre cómo especificar el tamaño con el [Portal de Azure][preview-portal], [Azure PowerShell][azure-powershell]y la [CLI de Azure][azure-cli]:

* **Portal de Azure**: al crear un nuevo clúster, tiene la opción de establecer el tamaño (nivel de precios) de la cabeza, trabajo y (si se utiliza el tipo de clúster) nodos de ZooKeeper para el clúster:

    ![Imagen del Asistente para la creación de clúster con la selección del tamaño de nodo](./media/hdinsight-high-availability-linux/headnodesize.png)

* **Azure CLI**: cuando se usa el `azure hdinsight cluster create` comando, puede establecer el tamaño de la cabeza, trabajo y nodos de ZooKeeper mediante la `--headNodeSize`, `--workerNodeSize`, y `--zookeeperNodeSize` parámetros.

* **Azure PowerShell**: cuando se usa el `New-AzureRmHDInsightCluster` cmdlet, puede establecer el tamaño de la cabeza, trabajo y nodos de ZooKeeper mediante la `-HeadNodeVMSize`, `-WorkerNodeSize`, y `-ZookeeperNodeSize` parámetros.

##<a name="next-steps"></a>Pasos siguientes

En este documento ha visto cómo HDInsight de Azure proporciona alta disponibilidad para Hadoop. Utilice la siguiente para obtener más información sobre los elementos que se mencionan en este documento.

- [Referencia del resto de Ambari](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md)

- [Instalar y configurar la CLI de Azure](../xplat-cli-install.md)

- [Instalar y configurar PowerShell de Azure](../powershell-install-configure.md)

- [Administrar HDInsight con Ambari](hdinsight-hadoop-manage-ambari.md)

- [Aprovisionar clústeres basados en Linux HDInsight](hdinsight-hadoop-provision-linux-clusters.md)

[preview-portal]: https://portal.azure.com/
[azure-powershell]: ../powershell-install-configure.md
[azure-cli]: ../xplat-cli-install.md
