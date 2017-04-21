<properties
   pageTitle="Sugerencias para usar Hadoop en HDInsight de Linux | Microsoft Azure"
   description="Obtener sugerencias de implementación para usar clústeres basados en Linux HDInsight (Hadoop) en un entorno de Linux familiar en la nube de Azure."
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
   ms.date="09/13/2016"
   ms.author="larryfr"/>

# <a name="information-about-using-hdinsight-on-linux"></a>Información sobre cómo usar HDInsight en Linux

Clústeres de HDInsight de Azure basados en Linux proporcionan Hadoop en un entorno de Linux familiar, en la nube de Azure. Para la mayoría de los elementos, debería funcionar exactamente como cualquier otra instalación Hadoop en Linux. Este documento indica las diferencias específicas que debe tener en cuenta.

##<a name="prerequisites"></a>Requisitos previos

Muchos de los pasos de este documento usan las siguientes utilidades, que deben estar instalada en el sistema.

* [doblez](https://curl.haxx.se/) - utilizado para comunicarse con los servicios basados en web
* [jq](https://stedolan.github.io/jq/) - usado para analizar JSON documentos
* [Azure CLI](../xplat-cli-install.md) - usada para administrar remotamente servicios de Azure

    [AZURE.INCLUDE [use-latest-version](../../includes/hdinsight-use-latest-powershell-and-cli.md)] 

## <a name="domain-names"></a>Nombres de dominio

Es el nombre de dominio completo (FQDN) para usar cuando se conecta al clúster desde internet ** &lt;nombreDeClúster >. azurehdinsight.net** o (si solo SSH) ** &lt;nombreDeClúster-ssh >. azurehdinsight.net**.

Internamente, cada nodo del clúster tiene un nombre que se asigna durante la configuración de clúster. Para buscar los nombres de clúster, puede visite la página de __Hosts__ en la interfaz de usuario de Web Ambari o use las siguientes acciones para devolver una lista de hosts de la API de REST de Ambari:

    curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/hosts" | jq '.items[].Hosts.host_name'

Reemplazar la __contraseña__ con la contraseña de la cuenta de administrador y __NOMBREDECLÚSTER__ con el nombre del clúster. Devolverá un documento JSON que contiene una lista de los hosts en el clúster y luego jq saca el `host_name` valor del elemento para cada host del clúster.

Si necesita buscar el nombre del nodo de un servicio específico, puede crear una consulta Ambari para que el componente. Por ejemplo, para buscar los hosts para el nodo de nombre HDFS, use la siguiente.

    curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/HDFS/components/NAMENODE" | jq '.host_components[].HostRoles.host_name'

Devuelve un documento JSON que describe el servicio y, a continuación, jq saca solo la `host_name` valor para los hosts.

## <a name="remote-access-to-services"></a>Acceso remoto a servicios

* **Ambari (web)** - https://&lt;nombreDeClúster >. azurehdinsight.net

    Autenticar mediante el usuario de administrador de clúster y la contraseña y, a continuación, inicie sesión en Ambari. Esto también usa el usuario administrador y la contraseña.

    La autenticación es texto simple: usar siempre HTTPS para ayudar a asegurar que la conexión es segura.

    > [AZURE.IMPORTANT] Mientras Ambari para el clúster está accesible directamente a través de Internet, algunas funciones se basa en obtener acceso a los nodos por el nombre de dominio interno utilizado por el clúster. Dado que este es un nombre de dominio interno, y no pública, recibirá errores de "servidor no encontrado" al intentar obtener acceso a algunas características a través de Internet.
    >
    > Para usar la funcionalidad completa de la interfaz de usuario de web Ambari, use un túnel SSH para el tráfico de web proxy al nodo de clúster principal. Consulte [Usar SSH túnel para tener acceso a la interfaz de usuario de web Ambari, ResourceManager, JobHistory, NameNode, Oozie y otra de la interfaz de usuario de web](hdinsight-linux-ambari-ssh-tunnel.md)

* **Ambari (REST)** - https://&lt;nombreDeClúster >.azurehdinsight.net/ambari

    > [AZURE.NOTE] Autenticar mediante el usuario de administrador de clúster y la contraseña.
    >
    > La autenticación es texto simple: usar siempre HTTPS para ayudar a asegurar que la conexión es segura.

* **WebHCat (Templeton)** - https://&lt;nombreDeClúster >.azurehdinsight.net/templeton

    > [AZURE.NOTE] Autenticar mediante el usuario de administrador de clúster y la contraseña.
    >
    > La autenticación es texto simple: usar siempre HTTPS para ayudar a asegurar que la conexión es segura.

* **SSH** - &lt;nombreDeClúster >-ssh.azurehdinsight.net en el puerto 22 o 23. Puerto 22 se usa para conectarse a la headnode principal, mientras que 23 se usa para conectarse a la secundaria. Para obtener más información sobre los nodos cabezales, vea [disponibilidad y confiabilidad de clústeres Hadoop HDInsight](hdinsight-high-availability-linux.md).

    > [AZURE.NOTE] Solo puede acceder a los nodos de clúster cabezal a través de SSH desde un equipo cliente. Una vez conectado, a continuación, puede acceder a los nodos de trabajo mediante SSH desde un headnode.

## <a name="file-locations"></a>Ubicaciones de archivos

Archivos de Hadoop pueden encontrarse en los nodos de clúster en `/usr/hdp`. Este directorio contiene los siguientes subdirectorios:

* __2.2.4.9-1__: este directorio con el nombre de la versión de la plataforma de datos de Hortonworks utilizan HDInsight, por lo que el número en el clúster puede ser diferente a la que se muestra aquí.
* __actual__: este directorio contiene vínculos a directorios bajo el directorio __2.2.4.9-1__ y existe para que no es necesario que escriba un número de versión (que puede cambiar,) cada vez que desea tener acceso a un archivo.

Datos de ejemplo y archivos JAR pueden encontrarse en el almacenamiento de blobs de Windows Azure o de sistema de archivos distribuido Hadoop (HDFS) en ' / ejemplo ' o ' wasbs: / / / ejemplo '.

## <a name="hdfs-azure-blob-storage-and-storage-best-practices"></a>HDFS, almacenamiento de blobs de Windows Azure y prácticas recomendadas de almacenamiento

En la mayoría de las distribuciones de Hadoop, HDFS se copia local de almacenamiento en los equipos del clúster. Aunque esto es eficaz, puede ser costosa para una solución basada en nube donde le cobrarán por horas o minutos para recursos de cálculo.

HDInsight usa almacenamiento de blobs de Windows Azure como el almacén de forma predeterminada, que proporciona las siguientes ventajas:

* Almacenamiento a largo plazo económico

* Accesibilidad de servicios externos como sitios Web, utilidades de carga y descarga de archivos, varios SDK de idioma y exploradores web

Puesto que es el almacén predeterminado para HDInsight, normalmente no tiene que hacer nada para usarlo. Por ejemplo, el siguiente comando mostrará archivos en la carpeta **/example/data** , que se almacena en el almacenamiento de blobs de Windows Azure:

    hdfs dfs -ls /example/data

Algunos comandos pueden requerir que especificar que está usando el almacenamiento de blobs. En estos casos, puede prefijo el comando con **wasb: / /**, o **wasbs: / /**.

HDInsight también le permite asociar varias cuentas de almacenamiento de blobs de Windows a un clúster. Para obtener acceso a datos en una cuenta de almacenamiento de blobs no predeterminada, puede usar el formato de **wasbs: / /&lt;container-name>@&lt;nombre de la cuenta >.blob.core.windows.net/**. Por ejemplo, la lista siguiente incluye el contenido del directorio **/example/data** para el contenedor especificado y la cuenta de almacenamiento de blobs:

    hdfs dfs -ls wasbs://mycontainer@mystorage.blob.core.windows.net/example/data

### <a name="what-blob-storage-is-the-cluster-using"></a>¿Es el almacenamiento de blobs con el clúster?

Durante la creación de clúster, que seleccionó para usar una cuenta existente de almacenamiento de Azure y contenedor, o cree uno nuevo. A continuación, probablemente olvidó acerca de él. Puede encontrar la cuenta de almacenamiento predeterminada y el contenedor mediante el uso de la API de REST de Ambari.

1. Utilice el comando siguiente para recuperar información de configuración de HDFS mediante enrollar y filtrarlos mediante [jq](https://stedolan.github.io/jq/):

        curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/configurations/service_config_versions?service_name=HDFS&service_config_version=1" | jq '.items[].configurations[].properties["fs.defaultFS"] | select(. != null)'
    
    > [AZURE.NOTE] Con esto devolverá la primera configuración aplicada al servidor (`service_config_version=1`,) que contendrá esta información. Si va a recuperar un valor que se ha modificado después de la creación de clúster, tendrá las versiones de configuración de la lista y recuperar la última existente.

    Devolverá un valor similar al siguiente, donde __se trata el contenedor predeterminado__ y __nombre de la cuenta__ es el nombre de la cuenta de almacenamiento de Azure:

        wasbs://CONTAINER@ACCOUNTNAME.blob.core.windows.net

1. Obtener el grupo de recursos para la cuenta de almacenamiento, use la [CLI de Azure](../xplat-cli-install.md). En el siguiente comando, reemplace el __nombre de la cuenta__ con el nombre de cuenta de almacenamiento recuperado de Ambari:

        azure storage account list --json | jq '.[] | select(.name=="ACCOUNTNAME").resourceGroup'
    
    Devolverá el nombre del grupo de recursos para la cuenta.
    
    > [AZURE.NOTE] Si no se devuelve nada de este comando, debe cambiar Azure CLI al modo de administrador de recursos de Azure y vuelva a ejecutar el comando. Para cambiar a modo de administrador de recursos de Azure, use el siguiente comando.
    >
    > `azure config mode arm`
    
2. Obtener la clave para la cuenta de almacenamiento. Reemplace el __nombre de grupo__ con el grupo de recursos desde el paso anterior. Reemplazar el __nombre de la cuenta__ con el nombre de la cuenta de almacenamiento:

        azure storage account keys list -g GROUPNAME ACCOUNTNAME --json | jq '.[0].value'

    Devolverá la clave principal de la cuenta.

También puede encontrar la información de almacenamiento con el Portal de Azure:

1. En el [Portal de Azure](https://portal.azure.com/), seleccione el clúster HDInsight.

2. En la sección __Essentials__ , seleccione __todas las opciones__.

3. En __configuración__, seleccione __Claves de almacenamiento de Azure__.

4. __Teclas de almacenamiento de Azure__, seleccione una de las cuentas de almacenamiento. Esta opción mostrará información acerca de la cuenta de almacenamiento.

5. Seleccione el icono de llave. Esta opción mostrará claves para esta cuenta de almacenamiento.

### <a name="how-do-i-access-blob-storage"></a>¿Cómo puedo obtener acceso al almacenamiento de blobs?

Aparte de mediante el comando Hadoop desde el clúster, hay varias maneras para tener acceso a BLOB:

* [Azure CLI para Mac, Linux y Windows](../xplat-cli-install.md): comandos de la interfaz de línea de comandos para trabajar con Azure. Después de instalar, use la `azure storage` comando para obtener ayuda sobre el uso de almacenamiento, o `azure blob` comandos específicos de blobs de.

* [blobxfer.py](https://github.com/Azure/azure-batch-samples/tree/master/Python/Storage): un python de secuencias de comandos para trabajar con BLOB en el almacenamiento de Azure.

* Una gran variedad de SDK:

    * [Java](https://github.com/Azure/azure-sdk-for-java)

    * [Node.js](https://github.com/Azure/azure-sdk-for-node)

    * [PHP](https://github.com/Azure/azure-sdk-for-php)

    * [Python](https://github.com/Azure/azure-sdk-for-python)

    * [Ruby](https://github.com/Azure/azure-sdk-for-ruby)

    * [.NET](https://github.com/Azure/azure-sdk-for-net)

* [API de REST de almacenamiento](https://msdn.microsoft.com/library/azure/dd135733.aspx)

##<a name="scaling"></a>El clúster de ajuste de escala

La característica de ajuste de escala de clúster le permite cambiar el número de nodos de datos utilizados por un clúster que se ejecuta en Azure HDInsight sin tener que eliminar y volver a crear el clúster.

Puede realizar operaciones de escala al resto de las tareas o procesos que se están ejecutando en un clúster.

Los tipos de clúster diferentes se ven afectados por escalado como sigue:

* __Hadoop__: cuando reduzca el número de nodos en un clúster, algunos de los servicios en el clúster se reinician. Errores al final de la operación de escalado Esto puede provocar trabajos marcha o pendiente. Puede volver a enviar las tareas una vez completada la operación.

* __HBase__: servidores regionales están equilibrados automáticamente en unos minutos después de completar la operación de rotación. Para equilibrar manualmente servidores regionales, realice los siguientes pasos:

    1. Conectarse al clúster HDInsight mediante SSH. Para obtener más información sobre cómo utilizar SSH con HDInsight, consulte uno de los documentos siguientes:

        * [Utilizar SSH con HDInsight de Linux, Unix y Mac OS X](hdinsight-hadoop-linux-use-ssh-unix.md)

        * [Utilizar SSH con HDInsight de Windows.](hdinsight-hadoop-linux-use-ssh-windows.md)

    1. Utilice la siguiente para iniciar la consola de HBase:

            hbase shell

    2. Una vez que haya cargado el shell HBase, use las siguientes acciones para equilibrar manualmente los servidores regionales:

            balancer

* __Tormenta__: debe equilibrar cualquier ejecución topologías tormenta después de realizar una operación de escala. Esto permite la topología de realice los ajustes de configuración paralelismo según el nuevo número de nodos en el clúster. Para equilibrar topologías ejecución, utilice una de las siguientes opciones:

    * __SSH__: conectar con el servidor y use el comando siguiente para equilibrar una topología de:

            storm rebalance TOPOLOGYNAME

        También puede especificar los parámetros para reemplazar las sugerencias de paralelismo originalmente proporcionadas por la topología. Por ejemplo, `storm rebalance mytopology -n 5 -e blue-spout=3 -e yellow-bolt=10` a configurar la topología de 5 procesos de trabajo, elementos de ejecución de 3 para el componente azul pitorro y 10 elementos de ejecución del componente de rayo amarillo.

    * __Interfaz de usuario de tormenta__: realice los pasos siguientes para equilibrar una topología mediante la interfaz de usuario de tormenta.

        1. Abra __https://CLUSTERNAME.azurehdinsight.net/stormui__ en el explorador web, donde NOMBREDECLÚSTER es el nombre de su clúster de tormenta. Si se le solicita, escriba el nombre del administrador (admin) de clúster HDInsight y la contraseña especificada al crear el clúster.

        3. Seleccione la topología que desea equilibrar y luego seleccione el botón __equilibrar__ . Especificar el retraso antes de que se realiza la operación de equilibrar.

Para obtener información específica sobre escala el clúster HDInsight, consulte:

* [Administrar clústeres de Hadoop en HDInsight a través del Portal de Azure](hdinsight-administer-use-portal-linux.md#scaling)

* [Administrar clústeres de Hadoop en HDinsight con PowerShell de Azure](hdinsight-administer-use-command-line.md#scaling)

## <a name="how-do-i-install-hue-or-other-hadoop-component"></a>¿Cómo puedo instalar matiz (u otro componente Hadoop)?

HDInsight es un servicio administrado, lo que significa que los nodos en un clúster se pueden destruidos y reaprovisionarla automáticamente por Azure si se detecta un problema. Por este motivo, no se recomienda instalar manualmente cosas directamente en los nodos de clúster. En su lugar, use [Acciones de secuencia de comandos de HDInsight](hdinsight-hadoop-customize-cluster.md) cuando necesita instalar las siguientes:

* Un servicio o sitio web como motor o matiz.
* Componente que requiere cambios de configuración en varios nodos en el clúster. Por ejemplo, una variable de entorno necesarios, creación de un directorio de registro, o la creación de un archivo de configuración.

Secuencia de comandos acciones fiesta de secuencias de comandos que ha encontrado durante el aprovisionamiento de clúster y pueden utilizarse para instalar y configurar componentes adicionales en el clúster. Se proporcionan secuencias de comandos de ejemplo para la instalación de los siguientes componentes:

* [Tono](hdinsight-hadoop-hue-linux.md)
* [Giraph](hdinsight-hadoop-giraph-install-linux.md)
* [Solr](hdinsight-hadoop-solr-install-linux.md)

Para obtener información sobre el desarrollo de sus propias acciones de secuencia de comandos, vea [desarrollo de acción de secuencia de comandos con HDInsight](hdinsight-hadoop-script-actions-linux.md).

###<a name="jar-files"></a>Archivos JAR

Algunas tecnologías de Hadoop se proporcionan en archivos jar autocontenido que contienen funciones que se usan como parte de un trabajo MapReduce o desde dentro de cerdo o subárbol. Mientras se pueden instalar con acciones de secuencia de comandos, a menudo no requiere ninguna configuración y solo se puede cargados en el clúster después de aprovisionamiento y usar directamente. Si desea que el componente supera imágenes del clúster makle, puede almacenar el archivo jar en WASB.

Por ejemplo, si desea usar la versión más reciente de [DataFu](http://datafu.incubator.apache.org/), puede descargar un jar que contiene el proyecto y cárguelo en el clúster HDInsight. A continuación, siga la documentación de DataFu sobre el uso de cerdo o subárbol.

> [AZURE.IMPORTANT] Algunos componentes que son archivos de jar independiente se proporcionan con HDInsight, pero no están en la ruta de acceso. Si está buscando un componente específico, puede usar el seguimiento para buscar en el clúster:
>
> ```find / -name *componentname*.jar 2>/dev/null```
>
> Devolverá la ruta de acceso de los archivos jar coincidentes.

Si el clúster ya proporciona una versión de un componente como un archivo de jar independiente, pero que desea usar una versión diferente, puede cargar una nueva versión del componente en el clúster y pruebe a usar en los trabajos.

> [AZURE.WARNING] Componentes proporcionados con el clúster HDInsight son totalmente compatibles y Microsoft Support le ayudará a aislar y resolver los problemas relacionados con estos componentes.
>
> Componentes personalizados reciban soporte comercialmente razonable para ayudar a solucionar el problema. Esto puede ocasionar resolver el problema o preguntar si desea integrarse canales disponibles para las tecnologías de código abierto donde se encuentra la amplia experiencia para que la tecnología. Por ejemplo, hay muchos sitios de comunidad que se pueden usar como: [foro de MSDN para HDInsight](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=hdinsight), [http://stackoverflow.com](http://stackoverflow.com). También proyectos Apache tienen sitios de proyecto en [http://apache.org](http://apache.org), por ejemplo: [Hadoop](http://hadoop.apache.org/), [motor](http://spark.apache.org/).

## <a name="next-steps"></a>Pasos siguientes

* [Migrar desde HDInsight de Windows a basados en Linux](hdinsight-migrate-from-windows-to-linux.md)
* [Usar subárbol con HDInsight](hdinsight-use-hive.md)
* [Usar cerdo con HDInsight](hdinsight-use-pig.md)
* [Usar MapReduce trabajos con HDInsight](hdinsight-use-mapreduce.md)
