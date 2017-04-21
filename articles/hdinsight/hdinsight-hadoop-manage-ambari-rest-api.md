<properties
   pageTitle="Supervisar y administrar clústeres de HDInsight con la API de REST de Apache Ambari | Microsoft Azure"
   description="Obtenga información sobre cómo utilizar Ambari para supervisar y administrar clústeres HDInsight basados en Linux. En este documento, obtendrá información sobre cómo usar la API de REST de Ambari incluido con HDInsight clústeres."
   services="hdinsight"
   documentationCenter=""
   authors="Blackmist"
   manager="jhubbard"
   editor="cgronlun"
    tags="azure-portal"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="09/20/2016"
   ms.author="larryfr"/>

#<a name="manage-hdinsight-clusters-by-using-the-ambari-rest-api"></a>Administrar clústeres HDInsight con la API de REST Ambari

[AZURE.INCLUDE [ambari-selector](../../includes/hdinsight-ambari-selector.md)]

Apache Ambari simplifica la administración y supervisión de un clúster de Hadoop proporcionando un más fáciles de usar la interfaz de usuario web y las API de REST. Ambari se incluye en clústeres basados en Linux HDInsight y se usa para supervisar el clúster y realizar cambios de configuración. En este documento, obtenga información sobre los conceptos básicos de trabajar con la API de REST Ambari al realizar tareas comunes mediante doblez.

##<a name="prerequisites"></a>Requisitos previos

* [doblez](http://curl.haxx.se/): cURL es una utilidad de varias plataformas que se pueden usar para trabajar con la API de REST de la línea de comandos. En este documento, se utiliza para comunicarse con la API de REST de Ambari.
* [jq](https://stedolan.github.io/jq/): jq es una herramienta de línea de comandos de varias plataformas para trabajar con documentos JSON. En este documento, se utiliza para analizar los documentos JSON devueltos de la API de REST de Ambari.
* [Azure CLI](../xplat-cli-install.md): una utilidad de la línea de comandos entre plataformas para trabajar con los servicios de Azure.

    [AZURE.INCLUDE [use-latest-version](../../includes/hdinsight-use-latest-cli.md)] 

##<a id="whatis"></a>¿Qué es Ambari?

[Apache Ambari](http://ambari.apache.org) simplifica la administración de Hadoop proporcionando un web fácil de usar la interfaz de usuario que se pueden usar para aprovisionar, administrar y supervisar los clústeres Hadoop. Los desarrolladores pueden integrar estas funciones en sus aplicaciones usando las [API de REST de Ambari](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md).

Ambari se proporciona con clústeres basados en Linux HDInsight de forma predeterminada.

##<a name="rest-api"></a>API DE REST

El URI base de la API de REST de Ambari en HDInsight es https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME, donde __NOMBREDECLÚSTER__ es el nombre del clúster.

> [AZURE.IMPORTANT] Mientras el nombre de clúster en la parte del nombre (FQDN) de dominio completo del URI (CLUSTERNAME.azurehdinsight.net) distingue mayúsculas de minúsculas, otras apariciones en el URI distinguen mayúsculas de minúsculas. Por ejemplo, si el clúster se denomina miClúster, los siguientes son URI válidos:
>
> `https://mycluster.azurehdinsight.net/api/v1/clusters/MyCluster`
> `https://MyCluster.azurehdinsight.net/api/v1/clusters/MyCluster`
>
> Los siguientes URI devuelve un error porque la segunda aparición del nombre no es mayúsculas y minúsculas.
>
> `https://mycluster.azurehdinsight.net/api/v1/clusters/mycluster`
> `https://MyCluster.azurehdinsight.net/api/v1/clusters/mycluster`

Conectarse a Ambari en HDInsight requiere HTTPS. Para autenticar la conexión, debe utilizar el nombre de la cuenta de administrador (el valor predeterminado es __Administrador__) y la contraseña proporcionada cuando se creó el clúster.

El ejemplo siguiente utiliza doblez para realizar una solicitud GET con la API de REST. Reemplazar la __contraseña__ con la contraseña de administrador para el clúster. Reemplazar __NOMBREDECLÚSTER__ con el nombre del clúster:

    curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME"
    
La respuesta es un documento JSON que comienza con información similar al siguiente:

    {
    "href" : "http://10.0.0.10:8080/api/v1/clusters/CLUSTERNAME",
    "Clusters" : {
        "cluster_id" : 2,
        "cluster_name" : "CLUSTERNAME",
        "health_report" : {
        "Host/stale_config" : 0,
        "Host/maintenance_state" : 0,
        "Host/host_state/HEALTHY" : 7,
        "Host/host_state/UNHEALTHY" : 0,
        "Host/host_state/HEARTBEAT_LOST" : 0,
        "Host/host_state/INIT" : 0,
        "Host/host_status/HEALTHY" : 7,
        "Host/host_status/UNHEALTHY" : 0,
        "Host/host_status/UNKNOWN" : 0,
        "Host/host_status/ALERT" : 0

Puesto que se trata de JSON, es más fácil usar un analizador JSON para trabajar con los datos. Por ejemplo, el ejemplo siguiente utiliza jq para mostrar solo la `health_report` elemento.

    curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME" | jq '.Clusters.health_report'

##<a name="example-get-the-fqdn-of-cluster-nodes"></a>Ejemplo: Obtener el FQDN de nodos de clúster

Cuando trabaje con HDInsight, necesitará saber el nombre de dominio completo (FQDN) de un nodo de clúster. Puede recuperar fácilmente el FQDN de los distintos nodos del clúster con los siguientes:

* __Nodos de cabeza__:`curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/HDFS/components/NAMENODE" | jq '.host_components[].HostRoles.host_name'`
* __Nodos de trabajo__:`curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/HDFS/components/DATANODE" | jq '.host_components[].HostRoles.host_name'`
* __Nodos de zookeeper__:`curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/ZOOKEEPER/components/ZOOKEEPER_SERVER" | jq '.host_components[].HostRoles.host_name'`

Nota de cada uno de estos ejemplos sigue el mismo patrón:

1. Un componente que sabemos de consulta se ejecuta en los nodos.
2. Recuperar el `host_name` elementos, que contienen el FQDN para estos nodos.

La `host_components` elemento del documento devuelto contiene varios elementos. Usando `.host_components[]`, y, a continuación, especificar una ruta de acceso en el elemento se recorra cada elemento y extraer el valor de la ruta de acceso específica. Si solo desea un valor, como la primera entrada de nombre de dominio completo, puede devolver los elementos como una colección y, a continuación, seleccione una entrada específica:

    jq '[.host_components[].HostRoles.host_name][0]'

Devuelve el primer nombre de dominio completo de la colección.

##<a name="example-get-the-default-storage-account-and-container"></a>Ejemplo: Obtener la cuenta de almacenamiento predeterminada y el contenedor

Cuando se crea un clúster de HDInsight, debe utilizar una cuenta de almacenamiento de Azure y un contenedor de blob como el almacenamiento predeterminado para el clúster. Puede usar Ambari para recuperar esta información una vez creado el clúster. Por ejemplo, si desea mediante programación escribir datos directamente en el contenedor.

La siguiente recupera el WASB URI del almacenamiento de clústeres predeterminado:

    curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/configurations/service_config_versions?service_name=HDFS&service_config_version=1" | jq '.items[].configurations[].properties["fs.defaultFS"] | select(. != null)'
    
> [AZURE.NOTE] Devuelve la primera configuración aplicada al servidor (`service_config_version=1`,) que contiene la siguiente información. Si recupera un valor que se ha modificado después de la creación de clúster, tendrá las versiones de configuración de la lista y recuperar la última existente.

Devuelve un valor similar al siguiente ejemplo, donde __se trata el contenedor predeterminado__ y __nombre de la cuenta__ es el nombre de la cuenta de almacenamiento de Azure:

    wasbs://CONTAINER@ACCOUNTNAME.blob.core.windows.net

A continuación, puede usar esta información con la [CLI de Azure](../xplat-cli-install.md) en cargar o descargar datos desde el contenedor.

1. Obtener el grupo de recursos para la cuenta de almacenamiento. Reemplazar el __nombre de la cuenta__ con el nombre de cuenta de almacenamiento recuperado de Ambari:

        azure storage account list --json | jq '.[] | select(.name=="ACCOUNTNAME").resourceGroup'
    
    Devuelve el nombre del grupo de recursos para la cuenta.
    
    > [AZURE.NOTE] Si no se devuelve nada de este comando, debe cambiar Azure CLI al modo de administrador de recursos de Azure y vuelva a ejecutar el comando. Para cambiar a modo de administrador de recursos de Azure, utilice el siguiente comando:
    >
    > `azure config mode arm`
    
2. Obtener la clave para la cuenta de almacenamiento. Reemplace el __nombre de grupo__ con el grupo de recursos desde el paso anterior. Reemplazar el __nombre de la cuenta__ con el nombre de la cuenta de almacenamiento:

        azure storage account keys list -g GROUPNAME ACCOUNTNAME --json | jq '.storageAccountKeys.key1'

    En este ejemplo se devuelve la clave principal de la cuenta.
    
3. Use el comando de carga guardando un archivo en el contenedor:

        azure storage blob upload -a ACCOUNTNAME -k ACCOUNTKEY -f FILEPATH --container __CONTAINER__ -b BLOBPATH
        
    Reemplace el __nombre de la cuenta__ con el nombre de la cuenta de almacenamiento. Reemplace __ACCOUNTKEY__ con la clave recuperada previamente. __Ruta del archivo__ es la ruta de acceso al archivo que desea cargar, mientras que __BLOBPATH__ es la ruta de acceso en el contenedor.

    Por ejemplo, si desea que el archivo aparezca en HDInsight en wasbs://example/data/filename.txt, __BLOBPATH__ sería `example/data/filename.txt`.

##<a name="example-update-ambari-configuration"></a>Ejemplo: Configuración de actualización Ambari

1. Obtener la configuración actual, que almacena los Ambari como "configuración deseada":

        curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME?fields=Clusters/desired_configs"
        
    Este ejemplo devuelve un documento JSON que contiene la configuración actual (identificada por el valor de _etiqueta_ ) de los componentes instalados en el clúster. El ejemplo siguiente es un extracto de los datos devueltos desde un tipo de clúster de motor.
    
        "spark-metrics-properties" : {
            "tag" : "INITIAL",
            "user" : "admin",
            "version" : 1
        },
        "spark-thrift-fairscheduler" : {
            "tag" : "INITIAL",
            "user" : "admin",
            "version" : 1
        },
        "spark-thrift-sparkconf" : {
            "tag" : "INITIAL",
            "user" : "admin",
            "version" : 1
        }

    En esta lista, debe copiar el nombre del componente (por ejemplo, __motor\_thrift\_sparkconf__ y el valor de la __etiqueta__ .
    
2. Recuperar la configuración para el componente y la etiqueta mediante el comando siguiente. Reemplazar __thrift de motor de sparkconf__ e __inicial__ con el componente y la etiqueta que desea recuperar la configuración.

        curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/configurations?type=spark-thrift-sparkconf&tag=INITIAL" | jq --arg newtag $(echo version$(date +%s%N)) '.items[] | del(.href, .version, .Config) | .tag |= $newtag | {"Clusters": {"desired_config": .}}' > newconfig.json
    
    Doblez recupera el documento JSON y luego jq se usa para realizar modificaciones de los datos para crear una plantilla. La plantilla, a continuación, se usa para agregar o modificar los valores de configuración. Específicamente hace lo siguiente:
    
    * Crea un valor único que contiene la cadena "versión" y la fecha, que se almacena en __newtag__.
    * Crea un documento de raíz de la nueva configuración que desee.
    * Obtiene el contenido de la `.items[]` de matriz y agrega en el elemento __desired_config__ .
    * Elimina la __referencia__, __versión__y elementos de __configuración__ , como los elementos siguientes no son necesarios para enviar una nueva configuración.
    * Agrega un nuevo elemento de __etiqueta__ y establece su valor en __versión ###__. La parte numérica se basa en la fecha actual. Cada configuración debe tener una etiqueta única.
    
    Por último, los datos se guardan en el documento __newconfig.json__ . La estructura del documento debería ser similar al siguiente ejemplo:
    
        {
            "Clusters": {
                "desired_config": {
                "tag": "version1459260185774265400",
                "type": "spark-thrift-sparkconf",
                "properties": {
                    ....
                 },
                 "properties_attributes": {
                     ....
                 }
            }
        }

3. Abra los valores del documento y agregar o modificar __newconfig.json__ en el objeto de __Propiedades__ . En el ejemplo siguiente se cambia el valor de __"spark.yarn.am.memory"__ de __"1 g"__ a __"3 g"__ y y se agrega un nuevo elemento para __"spark.kryoserializer.buffer.max"__ con un valor de __"256 m"__.

        "spark.yarn.am.memory": "3g",
        "spark.kyroserializer.buffer.max": "256m",

    Una vez que haya terminado de hacer modificaciones, guarde el archivo.

4. Use el comando siguiente para enviar la configuración actualizada a Ambari.

        cat newconfig.json | curl -u admin:PASSWORD -H "X-Requested-By: ambari" -X PUT -d "@-" "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME"
        
    Este comando envía el contenido del archivo __newconfig.json__ a la solicitud de doblez que envía al clúster como la nueva configuración deseada. La solicitud de doblez devuelve un documento JSON. El elemento __versionTag__ en este documento debe coincidir con la versión que ha enviado y el objeto de __configuraciones__ contendrá los cambios de configuración solicitados.

###<a name="example-restart-a-service-component"></a>Ejemplo: Reiniciar un componente de servicio

En este momento, si mira la interfaz de usuario de web Ambari, el servicio motor indicará que debe reiniciar antes de que la nueva configuración surta efecto. Realice los pasos siguientes para reiniciar el servicio.

1. Para habilitar el modo de mantenimiento del servicio motor, utilice la siguiente:

        echo '{"RequestInfo": {"context": "turning on maintenance mode for SPARK"},"Body": {"ServiceInfo": {"maintenance_state":"ON"}}}' | curl -u admin:PASSWORD -H "X-Requested-By: ambari" -X PUT -d "@-" "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/SPARK"

    Este comando envía un documento JSON en el servidor (incluidos en el `echo` instrucción,) se activa el modo de mantenimiento.
    Puede comprobar que el servicio está ahora en modo de mantenimiento con la solicitud siguiente:
    
        curl -u admin:PASSWORD -H "X-Requested-By: ambari" "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/SPARK" | jq .ServiceInfo.maintenance_state
        
    Devolverá un valor de `"ON"`.

3. A continuación, use las siguientes acciones para desactivar el servicio:

        echo '{"RequestInfo": {"context" :"Stopping the Spark service"}, "Body": {"ServiceInfo": {"state": "INSTALLED"}}}' | curl -u admin:PASSWORD -H "X-Requested-By: ambari" -X PUT -d "@-" "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/SPARK"
        
    Este comando devuelve una respuesta similar a la siguiente.
    
        {
            "href" : "http://10.0.0.18:8080/api/v1/clusters/CLUSTERNAME/requests/29",
            "Requests" : {
                "id" : 29,
                "status" : "Accepted"
            }
        }
    
    La `href` valor devuelto por esta URI está usando la dirección IP interna del nodo. Para usar desde fuera del clúster, reemplace la parte de '10.0.0.18:8080' con el nombre completo del clúster. Por ejemplo, el siguiente comando recupera el estado de la solicitud.
    
        curl -u admin:PASSWORD -H "X-Requested-By: ambari" "https://CLUSTERNAME/api/v1/clusters/CLUSTERNAME/requests/29" | jq .Requests.request_status
    
    Si se devuelve un valor de `"COMPLETED"` , a continuación, ha finalizado la solicitud.

4. Una vez completada la solicitud anterior, utilice la siguiente para iniciar el servicio.

        echo '{"RequestInfo": {"context" :"Restarting the Spark service"}, "Body": {"ServiceInfo": {"state": "STARTED"}}}' | curl -u admin:PASSWORD -H "X-Requested-By: ambari" -X PUT -d "@-" "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/SPARK"

    Una vez que se reinicia el servicio, que es la nueva configuración.

5. Por último, use los siguientes para desactivar el modo de mantenimiento.

        echo '{"RequestInfo": {"context": "turning off maintenance mode for SPARK"},"Body": {"ServiceInfo": {"maintenance_state":"OFF"}}}' | curl -u admin:PASSWORD -H "X-Requested-By: ambari" -X PUT -d "@-" "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/SPARK"

##<a name="next-steps"></a>Pasos siguientes

Para obtener una referencia completa de la API de REST, consulte [Ambari API referencia V1](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md).

> [AZURE.NOTE] Algunas funciones de Ambari está deshabilitada, tal como se administra mediante el servicio de nube HDInsight; Por ejemplo, agregando o quitando hosts del clúster o agregar nuevos servicios.
