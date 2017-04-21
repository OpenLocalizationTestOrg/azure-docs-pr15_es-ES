<properties
    pageTitle="Usar los nodos de borde vacío en HDInsight | Microsoft Azure"
    description="Cómo agregar un nodo del borde ampty a clúster de HDInsight que se pueden usar como un cliente y las aplicaciones de HDInsight de host o de prueba."
    services="hdinsight"
    editor="cgronlun"
    manager="jhubbard"
    authors="mumian"
    tags="azure-portal"
    documentationCenter=""/>

<tags
    ms.service="hdinsight"
    ms.workload="big-data"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/14/2016"
    ms.author="jgao"/>

# <a name="use-empty-edge-nodes-in-hdinsight"></a>Usar los nodos de borde vacío en HDInsight

Obtenga información sobre cómo agregar un nodo del borde vacío en un clúster de HDInsight basados en Linux. Un nodo del borde vacío es una máquina virtual de Linux con las herramientas cliente instalado y configurado como en la headnodes, pero sin ejecutar hadoop los servicios. Puede usar el nodo del borde para acceder el clúster, probar las aplicaciones de cliente y las aplicaciones cliente de hospedaje. 

Puede agregar un nodo del borde vacía a un clúster HDInsight existente a un nuevo clúster al crear el clúster. Adición de un nodo del borde vacía se lleve a cabo con el Administrador de recursos de Azure plantilla.  En el ejemplo siguiente se muestra cómo se hace con una plantilla:

    "resources": [
        {
            "name": "[concat(parameters('clusterName'),'/', variables('applicationName'))]",
            "type": "Microsoft.HDInsight/clusters/applications",
            "apiVersion": "[variables('clusterApiVersion')]",
            "properties": {
                "marketPlaceIdentifier": "EmptyNode",
                "computeProfile": {
                    "roles": [{
                        "name": "edgenode",
                        "targetInstanceCount": 1,
                        "hardwareProfile": {
                            "vmSize": "[parameters('edgeNodeSize')]"
                        }
                    }]
                },
                "installScriptActions": [{
                    "name": "[concat('emptynode','-' ,uniquestring(variables('applicationName')))]",
                    "uri": "https://raw.githubusercontent.com/hdinsight/Iaas-Applications/master/EmptyNode/scripts/EmptyNodeSetup.sh",
                    "roles": ["edgenode"]
                }],
                "uninstallScriptActions": [],
                "httpsEndpoints": [],
                "applicationType": "CustomApplication"
            }
        }
    ],

Como se muestra en el ejemplo, si lo desea puede llamar a una [acción de secuencia de comandos](hdinsight-hadoop-customize-cluster-linux.md) para realizar una configuración adicional, como instalar [Apache matiz](hdinsight-hadoop-hue-linux.md) en el nodo del borde.

Después de haber creado un nodo del borde, puede conectar con el nodo de borde mediante SSH y ejecutar las herramientas de cliente para tener acceso al clúster de Hadoop en HDInsight.

## <a name="add-an-edge-node-to-an-existing-cluster"></a>Agregar un nodo del borde a un clúster existente

En esta sección, usa una plantilla de administrador de recursos para agregar un nodo del borde a un clúster de HDInsight existente.  La plantilla de administrador de recursos se puede encontrar en [GitHub](https://github.com/hdinsight/Iaas-Applications/tree/master/EmptyNode). La plantilla de administrador de recursos llama a una secuencia de comandos de acción de secuencia de comandos que se encuentra en https://raw.githubusercontent.com/hdinsight/Iaas-Applications/master/EmptyNode/scripts/EmptyNodeSetup.sh. La secuencia de comandos no realiza ninguna acción.  Esto es demostrar llamada acción de secuencia de comandos de una plantilla de administrador de recursos.

**Para agregar un nodo del borde vacía a un clúster existente**

1. Crear un clúster de HDInsight si todavía no tiene uno.  Vea [Hadoop tutorial: Introducción al uso basados en Linux Hadoop en HDInsight](hdinsight-hadoop-linux-tutorial-get-started.md).
2. Haga clic en la imagen siguiente para iniciar sesión Azure y abra la plantilla de administrador de recursos de Azure en el portal de Azure. 

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fhdinsight%2FIaas-Applications%2Fmaster%2FEmptyNode%2Fazuredeploy.json" target="_blank"><img src="https://acom.azurecomcdn.net/80C57D/cdn/mediahandler/docarticles/dpsmedia-prod/azure.microsoft.com/en-us/documentation/articles/hdinsight-hbase-tutorial-get-started-linux/20160201111850/deploy-to-azure.png" alt="Deploy to Azure"></a>

3. Configurar las siguientes propiedades:

    - NOMBREDECLÚSTER: Escriba el nombre de un clúster de HDInsight existente.
    - EDGENODESIZE: Seleccione uno de los tamaños de máquina virtual.
    - EDGENODEPREFIX: El valor predeterminado es **nuevo**.  Usa el valor predeterminado, el nombre de nodo de borde es **nueva edgenode**.  Puede personalizar el prefijo desde el portal. También puede personalizar el nombre completo de la plantilla.


4. Haga clic en **Aceptar** para guardar los cambios.
5. En el **grupo de recursos**, seleccione un grupo de recursos.
6. Haga clic en **condiciones legales de revisión**y, a continuación, haga clic en **comprar**.
7. Seleccione **Anclar al panel**y, a continuación, haga clic en **crear**.

## <a name="add-an-edge-node-when-creating-a-cluster"></a>Agregar un nodo del borde para crear un clúster

En esta sección, usa una plantilla de administrador de recursos para crear clúster HDInsight con un nodo del borde.  La plantilla de administrador de recursos puede encontrarse en un [contenedor de almacenamiento de blobs de Windows Azure](http://hditutorialdata.blob.core.windows.net/armtemplates/create-linux-based-hadoop-cluster-in-hdinsight-with-edge-node.json)del público. La plantilla de administrador de recursos llama a una secuencia de comandos de acción de secuencia de comandos que se encuentra en https://raw.githubusercontent.com/hdinsight/Iaas-Applications/master/EmptyNode/scripts/EmptyNodeSetup.sh. La secuencia de comandos no realiza ninguna acción.  Esto es demostrar llamada acción de secuencia de comandos de una plantilla de administrador de recursos.

**Para agregar un nodo del borde vacía a un clúster existente**

1. Crear un clúster de HDInsight si todavía no tiene uno.  Vea [Hadoop tutorial: Introducción al uso basados en Linux Hadoop en HDInsight](hdinsight-hadoop-linux-tutorial-get-started.md).
2. Haga clic en la imagen siguiente para iniciar sesión Azure y abra la plantilla de administrador de recursos de Azure en el portal de Azure. 

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Farmtemplates%2Fcreate-linux-based-hadoop-cluster-in-hdinsight-with-edge-node.json" target="_blank"><img src="https://acom.azurecomcdn.net/80C57D/cdn/mediahandler/docarticles/dpsmedia-prod/azure.microsoft.com/en-us/documentation/articles/hdinsight-hbase-tutorial-get-started-linux/20160201111850/deploy-to-azure.png" alt="Deploy to Azure"></a>

3. Configurar las siguientes propiedades:
        
    - NOMBREDECLÚSTER: Escriba un nombre para el nuevo clúster de crear.
    - CLUSTERLOGINUSERNAME: Escriba el nombre de usuario de HTTP Hadoop.  El nombre predeterminado es **admin**.
    - CLUSTERLOGINPASSWORD: Escriba la contraseña de usuario de HTTP Hadoop.
    - SSHUSERNAME: Escriba el nombre de usuario SSH. El nombre predeterminado es **sshuser**.
    - SSHPASSWORD: Escriba la contraseña de usuario SSH.
    - UBICACIÓN: Especifique la ubicación de la cuenta de almacenamiento predeterminada, el nombre del grupo de recursos y el clúster.
    - CLUSTERTYPE: El valor predeterminado es **hadoop**.
    - CLUSTERWORKERNODECOUNT: El valor predeterminado es 2.
    - EDGENODESIZE: Seleccione uno de los tamaños de máquina virtual.
    - EDGENODEPREFIX: El valor predeterminado es **nuevo**.  Usa el valor predeterminado, el nombre de nodo de borde es **nueva edgenode**.  Puede personalizar el prefijo desde el portal. También puede personalizar el nombre completo de la plantilla.

4. Haga clic en **Aceptar** para guardar los cambios.
5. En el **grupo de recursos**, escriba un nuevo nombre de grupo de recursos.
6. Haga clic en **condiciones legales de revisión**y, a continuación, haga clic en **comprar**.
7. Seleccione **Anclar al panel**y, a continuación, haga clic en **crear**. 


## <a name="access-an-edge-node"></a>Obtener acceso a un nodo del borde

El nodo del borde ssh extremo es <EdgeNodeName>. <ClusterName>-ssh.azurehdinsight.net:22.  Por ejemplo, nuevo-edgenode.myedgenode0914-ssh.azurehdinsight.net:22.

El nodo del borde aparece como una aplicación en el portal de Azure.  El portal ofrece la información para acceder al nodo de borde mediante SSH.

**Para comprobar el extremo SSH de nodo de borde**

1. Inicie sesión el [portal de Azure](https://portal.azure.com).
2. Abra el clúster HDInsight con un nodo del borde.
3. Haga clic en **aplicaciones** desde el módulo de clúster. Deberá ver el nodo del borde.  El nombre predeterminado es **edgenode nuevo**.
4. Haga clic en el nodo del borde. Deberá ver el extremo SSH.

**Usar subárbol en el nodo del borde**

5. Usar SSH para conectar con el nodo del borde.  Ver [uso SSH con basados en Linux Hadoop en HDInsight de Linux, Unix o OS X](hdinsight-hadoop-linux-use-ssh-unix.md) o [utilizar SSH con basados en Linux Hadoop en HDInsight de Windows](hdinsight-hadoop-linux-use-ssh-windows.md).
6. Después de que ha conectado al nodo de borde mediante SSH, utilice el comando siguiente para abrir la consola de sección:

        hive
7. Ejecute el comando siguiente para mostrar las tablas de la sección en el clúster:

        show tables;

## <a name="delete-an-edge-node"></a>Eliminar un nodo del borde

Puede eliminar un nodo del borde desde el portal de Azure.

**Para obtener acceso a un nodo del borde**

1. Inicie sesión el [portal de Azure](https://portal.azure.com).
2. Abra el clúster HDInsight con un nodo del borde.
3. Haga clic en **aplicaciones** desde el módulo de clúster. Deberá ver una lista de nodos de borde.  
4. Haga clic en el nodo de borde que desea eliminar y, a continuación, haga clic en **Eliminar**.
5. Haga clic en **Sí** para confirmar.

## <a name="next-steps"></a>Pasos siguientes

En este artículo, ha visto cómo agregar un nodo del borde y cómo obtener acceso al nodo de borde. Para obtener más información, consulte los artículos siguientes:

- [HDInsight de instalar aplicaciones](hdinsight-apps-install-applications.md): Obtenga información sobre cómo instalar una aplicación de HDInsight a los clústeres.
- [Instalar aplicaciones personalizadas de HDInsight](hdinsight-apps-install-custom-applications.md): Obtenga información sobre cómo implementar una aplicación de HDInsight no publicada en HDInsight.
- [HDInsight publicar aplicaciones](hdinsight-apps-publish-applications.md): Obtenga información sobre cómo publicar las aplicaciones HDInsight Azure Marketplace.
- [MSDN: instalar una aplicación de HDInsight](https://msdn.microsoft.com/library/mt706515.aspx): Obtenga información sobre cómo definir aplicaciones HDInsight.
- [Clústeres basados en Linux personalizar HDInsight con la acción de secuencia de comandos](hdinsight-hadoop-customize-cluster-linux.md): Obtenga información sobre cómo usar la acción de secuencia de comandos para instalar las aplicaciones adicionales.
- [Hadoop basados en Linux crear clústeres en HDInsight con plantillas de administrador de recursos](hdinsight-hadoop-create-linux-clusters-arm-templates.md): Aprenda a plantillas de administrador de recursos para crear clústeres HDInsight de llamada.

