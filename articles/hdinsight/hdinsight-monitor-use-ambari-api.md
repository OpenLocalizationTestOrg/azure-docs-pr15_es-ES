<properties
    pageTitle="Supervisar clústeres Hadoop en HDInsight con la API Ambari | Microsoft Azure"
    description="Use las Apache Ambari APIs para crear, administrar y supervisar clústeres Hadoop. Herramientas de operador intuitiva y las API de ocultan la complejidad de Hadoop."
    services="hdinsight"
    documentationCenter=""
    tags="azure-portal"
    authors="mumian"
    editor="cgronlun"
    manager="jhubbard"/>

<tags
    ms.service="hdinsight"
    ms.workload="big-data"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/10/2016"
    ms.author="jgao"/>

# <a name="monitor-hadoop-clusters-in-hdinsight-using-the-ambari-api"></a>Supervisar clústeres Hadoop en HDInsight con la API de Ambari

Obtenga información sobre cómo supervisar clústeres HDInsight mediante APIs Ambari.

> [AZURE.NOTE] La información en este artículo es principalmente para clústeres HDInsight basado en Windows, que proporciona una versión de solo lectura de la API de REST de Ambari. Para clústeres basados en Linux, consulte [clústeres de Hadoop administrar mediante Ambari](hdinsight-hadoop-manage-ambari.md).

## <a name="what-is-ambari"></a>¿Qué es Ambari?

[Apache Ambari] [ ambari-home] se usa para aprovisionar, administrar y supervisar clústeres Hadoop Apache. Incluye una colección de herramientas de operador intuitiva y un conjunto sólido de API que ocultan la complejidad de Hadoop, lo que simplifica la operación de clústeres. Para obtener más información acerca de las API, vea [Referencia de la API de Ambari][ambari-api-reference]. 

HDInsight actualmente es compatible con la característica de supervisión Ambari. Ambari API 1.0 es compatible con clústeres de versión 3.0 y 2.1 HDInsight. En este artículo trata sobre acceso a APIs Ambari en clústeres de versión 3.1 y 2.1 HDInsight. La diferencia clave entre los dos es que algunos de los componentes han cambiado con la introducción de nuevas capacidades (por ejemplo, el servidor de historial de trabajo). 

**Requisitos previos**

Antes de comenzar este tutorial, debe tener el siguiente:

- **Una estación de trabajo con PowerShell de Azure**.

    [AZURE.INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]

- (Opcional) [doblez] [curl]. Para instalarlo, consulte [doblez versiones y descargas][curl-download].

    >[AZURE.NOTE] Cuándo usar el comando cURL en Windows, use comillas dobles en lugar de comillas simples para los valores de la opción.

- **Clúster de un HDInsight de Azure**. Para obtener instrucciones sobre la creación de clúster, vea [Introducción al uso HDInsight] [ hdinsight-get-started] o [agrupaciones de aprovisionamiento HDInsight][hdinsight-provision]. Necesitará los siguientes datos llegue el tutorial:

    Clúster (propiedad)|Nombre de variable de PowerShell Azure|Valor|Descripción
    ---|---|---|---
    Nombre de clúster HDInsight|$clusterName||El nombre de su clúster de HDInsight.
    Nombre de usuario de clúster|$clusterUsername||Nombre de usuario de clúster especificados cuando se creó el clúster.
    Contraseña de clúster|$clusterPassword||Contraseña de usuario de clúster.

    >[AZURE.NOTE] Para rellenar los valores de la tabla. Será útil para seguir este tutorial.

## <a name="jump-start"></a>Saltar inicio

Hay varias maneras de utilizar Ambari para supervisar clústeres HDInsight.

**Usar PowerShell Azure**

El siguiente es un script de PowerShell de Azure para obtener la información de seguimiento del trabajo MapReduce *en un clúster de HDInsight 3.1.*  La diferencia clave es que nos extraer estos detalles desde el servicio de hilo (en lugar de MapReduce).

    $clusterName = "<HDInsightClusterName>"
    $clusterUsername = "<HDInsightClusterUsername>"
    $clusterPassword = "<HDInsightClusterPassword>"

    $ambariUri = "https://$clusterName.azurehdinsight.net:443/ambari"
    $uriJobTracker = "$ambariUri/api/v1/clusters/$clusterName.azurehdinsight.net/services/yarn/components/resourcemanager"

    $passwd = ConvertTo-SecureString $clusterPassword -AsPlainText -Force
    $creds = New-Object System.Management.Automation.PSCredential ($clusterUsername, $passwd)

    $response = Invoke-RestMethod -Method Get -Uri $uriJobTracker -Credential $creds -OutVariable $OozieServerStatus

    $response.metrics.'yarn.queueMetrics'

La siguiente es una secuencia de comandos de PowerShell de Azure para obtener el MapReduce trabajo rastreador información *en un clúster de HDInsight 2.1*.

    $clusterName = "<HDInsightClusterName>"
    $clusterUsername = "<HDInsightClusterUsername>"
    $clusterPassword = "<HDInsightClusterPassword>"

    $ambariUri = "https://$clusterName.azurehdinsight.net:443/ambari"
    $uriJobTracker = "$ambariUri/api/v1/clusters/$clusterName.azurehdinsight.net/services/mapreduce/components/jobtracker"

    $passwd = ConvertTo-SecureString $clusterPassword -AsPlainText -Force
    $creds = New-Object System.Management.Automation.PSCredential ($clusterUsername, $passwd)

    $response = Invoke-RestMethod -Method Get -Uri $uriJobTracker -Credential $creds -OutVariable $OozieServerStatus

    $response.metrics.'mapred.JobTracker'

El resultado es:

![Resultado de Jobtracker][img-jobtracker-output]

**Utilice Doblez**

El siguiente es un ejemplo de obtener la información de clúster usando doblez:

    curl -u <username>:<password> -k https://<ClusterName>.azurehdinsight.net:443/ambari/api/v1/clusters/<ClusterName>.azurehdinsight.net

El resultado es:

    {"href":"https://hdi0211v2.azurehdinsight.net/ambari/api/v1/clusters/hdi0211v2.azurehdinsight.net/",
     "Clusters":{"cluster_name":"hdi0211v2.azurehdinsight.net","version":"2.1.3.0.432823"},
     "services"[
       {"href":"https://hdi0211v2.azurehdinsight.net/ambari/api/v1/clusters/hdi0211v2.azurehdinsight.net/services/hdfs",
        "ServiceInfo":{"cluster_name":"hdi0211v2.azurehdinsight.net","service_name":"hdfs"}},
       {"href":"https://hdi0211v2.azurehdinsight.net/ambari/api/v1/clusters/hdi0211v2.azurehdinsight.net/services/mapreduce",
        "ServiceInfo":{"cluster_name":"hdi0211v2.azurehdinsight.net","service_name":"mapreduce"}}],
     "hosts":[
       {"href":"https://hdi0211v2.azurehdinsight.net/ambari/api/v1/clusters/hdi0211v2.azurehdinsight.net/hosts/headnode0",
        "Hosts":{"cluster_name":"hdi0211v2.azurehdinsight.net",
                 "host_name":"headnode0"}},
       {"href":"https://hdi0211v2.azurehdinsight.net/ambari/api/v1/clusters/hdi0211v2.azurehdinsight.net/hosts/workernode0",
        "Hosts":{"cluster_name":"hdi0211v2.azurehdinsight.net",
                 "host_name":"headnode0.{ClusterDNS}.azurehdinsight.net"}}]}

**Para el 8/10/2014 publicación**:

Cuando se usa el extremo Ambari, "https://{clusterDns}.azurehdinsight.net/ambari/api/v1/clusters/{clusterDns}.azurehdinsight.net/services/{servicename}/components/{componentname}", el campo *host_name* devuelve el nombre de dominio completo (FQDN) del nodo en lugar del nombre de host. Antes de la versión 8/10/2014, este ejemplo devuelve simplemente "**headnode0**". Después del lanzamiento de 8/10/2014, obtendrá el FQDN "**headnode0. { .Azurehdinsight ClusterDNS} .net**", como se muestra en el ejemplo anterior. Este cambio se necesarias para facilitar escenarios donde se pueden implementar varios tipos de clúster (como HBase y Hadoop) en una red virtual (VNET). Esto sucede, por ejemplo, cuando se usa HBase como una plataforma de back-end para Hadoop.

## <a name="ambari-monitoring-apis"></a>Ambari API de supervisión

La siguiente tabla muestra algunos de lo más comunes Ambari llamadas a la API de supervisión. Para obtener más información acerca de la API, vea [Referencia de la API de Ambari][ambari-api-reference].

Llamada de la API de Monitor|URI|Descripción
---|---|---
Obtener clústeres|`/api/v1/clusters`|
Obtener información de clúster.|`/api/v1/clusters/<ClusterName>.azurehdinsight.net`|clústeres, servicios, hosts
Obtener servicios|`/api/v1/clusters/<ClusterName>.azurehdinsight.net/services`|Los servicios incluyen: hdfs, mapreduce
Obtener información de los servicios.|`/api/v1/clusters/<ClusterName>.azurehdinsight.net/services/<ServiceName>`|
Obtener los componentes de servicio|`/api/v1/clusters/<ClusterName>.azurehdinsight.net/services/<ServiceName>/components`|HDFS: namenode, datanode<br/>MapReduce: jobtracker; tasktracker
Obtener información de componente.|`/api/v1/clusters/<ClusterName>.azurehdinsight.net/services/<ServiceName>/components/<ComponentName>`|ServiceComponentInfo componentes de host, métricas
Obtener hosts|`/api/v1/clusters/<ClusterName>.azurehdinsight.net/hosts`|headnode0, workernode0
Obtener información de host.|`/api/v1/clusters/<ClusterName>.azurehdinsight.net/hosts/<HostName>`|
Obtener componentes de host|`/api/v1/clusters/<ClusterName>.azurehdinsight.net/hosts/<HostName>/host_components`|namenode, resourcemanager
Obtener información de componente de host.|`/api/v1/clusters/<ClusterName>.azurehdinsight.net/hosts/<HostName>/host_components/<ComponentName>`|HostRoles, componente, host, métricas
Obtener configuraciones|`/api/v1/clusters/<ClusterName>.azurehdinsight.net/configurations`|Tipos de configuración: sitio principal, sitio hdfs, mapred sitio, sitios de la sección
Obtener información de configuración.|`/api/v1/clusters/<ClusterName>.azurehdinsight.net/configurations?type=<ConfigType>&tag=<VersionName>`|Tipos de configuración: sitio principal, sitio hdfs, mapred sitio, sitios de la sección


##<a name="next-steps"></a>Pasos siguientes

Ahora ha aprendido a usar Ambari llamadas a la API de supervisión. Para obtener más información, consulte:

- [Administrar clústeres HDInsight con el Portal de Azure][hdinsight-admin-portal]
- [Administrar clústeres HDInsight con PowerShell de Azure][hdinsight-admin-powershell]
- [Administrar clústeres HDInsight con interfaz de línea de comandos][hdinsight-admin-cli]
- [Documentación de HDInsight][hdinsight-documentation]
- [Introducción a HDInsight][hdinsight-get-started]



[ambari-home]: http://ambari.apache.org/
[ambari-api-reference]: https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md

[curl]: http://curl.haxx.se
[curl-download]: http://curl.haxx.se/download.html

[microsoft-hadoop-SDK]: http://hadoopsdk.codeplex.com/wikipage?title=Ambari%20Monitoring%20Client

[powershell-install]: powershell-install-configure.md
[powershell-script]: http://technet.microsoft.com/library/ee176949.aspx

[hdinsight-admin-powershell]: hdinsight-administer-use-powershell.md
[hdinsight-admin-portal]: hdinsight-administer-use-management-portal.md
[hdinsight-admin-cli]: hdinsight-administer-use-command-line.md
[hdinsight-documentation]: /documentation/services/hdinsight/
[hdinsight-get-started]: hdinsight-hadoop-linux-tutorial-get-started.md
[hdinsight-provision]: hdinsight-provision-clusters.md

[img-jobtracker-output]: ./media/hdinsight-monitor-use-ambari-api/hdi.ambari.monitor.jobtracker.output.png
