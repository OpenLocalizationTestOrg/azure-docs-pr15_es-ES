<properties
    pageTitle="Crear Hadoop, HBase o tormenta clústeres en Linux en HDInsight con varias plataformas Azure CLI | Microsoft Azure"
    description="Aprenda a crear clústeres basados en Linux HDInsight mediante la CLI de Azure entre plataformas, plantillas de administrador de recursos de Azure y la API de REST de Azure. Puede especificar el tipo de clúster (Hadoop, HBase o tormenta) o utilizar secuencias de comandos para instalar componentes personalizados..."
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

#<a name="create-linux-based-clusters-in-hdinsight-using-the-azure-cli"></a>Crear clústeres basados en Linux en HDInsight con la CLI de Azure

[AZURE.INCLUDE [selector](../../includes/hdinsight-selector-create-clusters.md)]

CLI Azure es una herramienta de línea de comandos de varias plataformas que le permite administrar servicios de Azure. Puede usarlo, junto con las plantillas de administración de recursos de Azure, para crear un clúster de HDInsight, junto con las cuentas de almacenamiento asociado y otros servicios.

Plantillas de administración de recursos de Azure son documentos JSON que describen un __grupo de recursos__ y todos los recursos en él (por ejemplo, HDInsight.) Este enfoque basado en la plantilla le permite definir todos los recursos que necesita para HDInsight en una plantilla. También le permite administrar los cambios en el grupo completo a través de __implementaciones__, que aplicar cambios a todo el grupo.

Los pasos de este documento guían durante el proceso de creación de un nuevo clúster HDInsight mediante la CLI de Azure y una plantilla.

> [AZURE.IMPORTANT] Los pasos de este documento utiliza el número predeterminado de nodos de trabajo (4) para un clúster de HDInsight. Si piensa en más de 32 nodos de trabajo (durante la creación de clúster o ajustando el clúster), debe seleccionar un tamaño de nodo principal con al menos 8 núcleos y 14 GB de ram.
>
> Para obtener más información sobre los tamaños de nodo y los costos asociados, consulte [HDInsight de precios](https://azure.microsoft.com/pricing/details/hdinsight/).

##<a name="prerequisites"></a>Requisitos previos

[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

- **Suscripción de un Azure**. Vea la [versión de prueba gratuita de Azure obtener](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
- __CLI azure__. Los pasos de este documento se han probado última con Azure CLI versión 0.10.1.

    [AZURE.INCLUDE [use-latest-version](../../includes/hdinsight-use-latest-cli.md)] 


### <a name="access-control-requirements"></a>Requisitos de control de acceso

[AZURE.INCLUDE [access-control](../../includes/hdinsight-access-control-requirements.md)]

##<a name="log-in-to-your-azure-subscription"></a>Inicie sesión en su suscripción de Azure

Siga los pasos documentados en [Conectar con una suscripción de Azure desde la interfaz de línea de comandos de Azure (Azure CLI)](../xplat-cli-connect.md) y conectarse a su suscripción con el método de __Inicio de sesión__ .

##<a name="create-a-cluster"></a>Crear un clúster

Los pasos siguientes deben realizarse desde un símbolo, shell o sesión de terminal después de instalar y configurar la CLI de Azure.

1. Use el comando siguiente para autenticar a su suscripción de Azure:

        azure login

    Le pide que proporcione su nombre y contraseña. Si tiene varias suscripciones de Azure, use `azure account set <subscriptionname>` para establecer la suscripción que usan los comandos de CLI de Azure.

3. Cambie al modo de administrador de recursos de Azure mediante el siguiente comando:

        azure config mode arm

4. Crear un grupo de recursos. Este grupo de recursos contiene el clúster HDInsight y almacenamiento cuenta asociada.

        azure group create groupname location
        
    * Reemplace el __nombre de grupo__ con un nombre único para el grupo. 
    * Reemplazar __ubicación__ con la región geográfica que desea crear el grupo. 
    
        Para obtener una lista de ubicaciones válidas, use la `azure location list` comando y, a continuación, utilice una de las ubicaciones de la columna __nombre__ .

5. Crear una cuenta de almacenamiento. Esta cuenta de almacenamiento se utilizará como el almacenamiento predeterminado para el clúster de HDInsight.

        azure storage account create -g groupname --sku-name RAGRS -l location --kind Storage storagename
        
     * Reemplace el __nombre de grupo__ con el nombre del grupo que creó en el paso anterior.
     * Reemplazar __ubicación__ con la misma ubicación en el paso anterior. 
     * Reemplazar __storagename__ con un nombre único para la cuenta de almacenamiento.
     
     > [AZURE.NOTE] Para obtener más información sobre los parámetros que se utilizan en este comando, use `azure storage account create -h` para ver la ayuda para este comando.

5. Recuperar la clave utilizada para tener acceso a la cuenta de almacenamiento.

        azure storage account keys list -g groupname storagename
        
    * Reemplace el __nombre de grupo__ con el nombre del grupo de recursos.
    * Reemplazar __storagename__ con el nombre de la cuenta de almacenamiento.
    
    En los datos que se devuelven, guarde el valor de __clave__ para __clave1__.

6. Crear un clúster de HDInsight.

        azure hdinsight cluster create -g groupname -l location -y Linux --clusterType Hadoop --defaultStorageAccountName storagename.blob.core.windows.net --defaultStorageAccountKey storagekey --defaultStorageContainer clustername --workerNodeCount 2 --userName admin --password httppassword --sshUserName sshuser --sshPassword sshuserpassword clustername

    * Reemplace el __nombre de grupo__ con el nombre del grupo de recursos.

    * Reemplace __Hadoop__ con el tipo de clúster que desea crear. Por ejemplo, `Hadoop`, `HBase`, `Storm` o `Spark`.

        > [AZURE.IMPORTANT] HDInsight formar una gran variedad de tipos, que corresponden a la carga de trabajo o la tecnología de clúster está optimizado para clústeres. No hay ningún método para crear un clúster que combina varios tipos, como tormenta y HBase en un clúster. 

    * Reemplazar __ubicación__ con la misma ubicación que se usan en los pasos anteriores.

    * Reemplazar __storagename__ con el nombre de la cuenta de almacenamiento.

    * Reemplace __storagekey__ con la clave que se obtienen en el paso anterior. 

    * Para el `--defaultStorageContainer` parámetro, use el mismo nombre que se usa para el clúster.

    * Reemplazar __Administrador__ y __httppassword__ con el nombre y la contraseña que desee usar cuando tengan acceso al clúster a través de HTTPS.

    * Reemplazar __sshuser__ y __sshuserpassword__ con el nombre de usuario y la contraseña que desea utilizar cuando tengan acceso al clúster mediante SSH

    Puede tardar varios minutos para que finalice el proceso de creación de clúster. Normalmente unos 15.

##<a name="next-steps"></a>Pasos siguientes

Ahora que ha creado correctamente un clúster de HDInsight con la CLI de Azure, use los siguientes para obtener información sobre cómo trabajar con el clúster:

###<a name="hadoop-clusters"></a>Clústeres Hadoop

* [Usar subárbol con HDInsight](hdinsight-use-hive.md)
* [Usar cerdo con HDInsight](hdinsight-use-pig.md)
* [Usar MapReduce con HDInsight](hdinsight-use-mapreduce.md)

###<a name="hbase-clusters"></a>Clústeres HBase

* [Introducción a HBase en HDInsight](hdinsight-hbase-tutorial-get-started-linux.md)
* [Desarrollar aplicaciones Java para HBase en HDInsight](hdinsight-hbase-build-java-maven-linux.md)

###<a name="storm-clusters"></a>Clústeres de tormenta

* [Desarrollar topologías Java para tormenta en HDInsight](hdinsight-storm-develop-java-topology.md)
* [Utilizar componentes de Python en tormenta en HDInsight](hdinsight-storm-develop-python-topology.md)
* [Implementar y supervisar topologías con tormenta en HDInsight](hdinsight-storm-deploy-monitor-topology-linux.md)
