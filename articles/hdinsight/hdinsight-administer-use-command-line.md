<properties
    pageTitle="Administrar clústeres de Hadoop mediante CLI de Azure | Microsoft Azure"
    description="Cómo utilizar la CLI Azure para administrar clústeres Hadoop en HDIsight"
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
    ms.date="08/10/2016"
    ms.author="jgao"/>

# <a name="manage-hadoop-clusters-in-hdinsight-using-the-azure-cli"></a>Administrar clústeres Hadoop en HDInsight con la CLI de Azure

[AZURE.INCLUDE [selector](../../includes/hdinsight-portal-management-selector.md)]

Obtenga información sobre cómo usar la [interfaz de línea de comandos de Azure](../xplat-cli-install.md) para administrar clústeres Hadoop en HDInsight de Azure. CLI Azure se implementa en Node.js. Puede usar en cualquier plataforma que admite Node.js, incluyendo Windows, Mac y Linux.

En este artículo trata sobre usando solo CLI Azure con HDInsight. Instrucciones generales sobre cómo usar CLI de Azure, consulte [instalar y configurar Azure CLI][azure-command-line-tools].

[AZURE.INCLUDE [use-latest-version](../../includes/hdinsight-use-latest-cli.md)]

##<a name="prerequisites"></a>Requisitos previos

Antes de comenzar este artículo, debe tener el siguiente:

- **Suscripción de un Azure**. Vea la [versión de prueba gratuita de Azure obtener](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
- **Azure CLI** - vea [instalar y configurar la CLI Azure](../xplat-cli-install.md) para obtener información de instalación y configuración.
- **Conectarse a Azure**, la mediante el siguiente comando:

        azure login

    Para obtener más información acerca de cómo autenticar con una cuenta profesional o educativa, vea [conectarse a una suscripción de Azure desde la CLI de Azure](xplat-cli-connect.md).
    
- **Cambiar al modo de administrador de recursos de Azure**, la mediante el siguiente comando:

        azure config mode arm

Para obtener ayuda, utilice el modificador **-h** .  Por ejemplo:

    azure hdinsight cluster create -h
    
##<a name="create-clusters"></a>Crear clústeres

Consulte [basados en Linux crear clústeres en HDInsight con la CLI de Azure](hdinsight-hadoop-create-linux-clusters-azure-cli.md).

##<a name="list-and-show-cluster-details"></a>Lista y mostrar detalles de clúster
Use los comandos siguientes para enumerar y mostrar detalles de clúster:

    azure hdinsight cluster list
    azure hdinsight cluster show <Cluster Name>

![HDI. CLIListCluster][image-cli-clusterlisting]


##<a name="delete-clusters"></a>Eliminar clústeres

Use el comando siguiente para eliminar un clúster:

    azure hdinsight cluster delete <Cluster Name>

También puede eliminar un clúster eliminando el grupo de recursos que contiene el clúster. Tenga en cuenta, se eliminarán todos los recursos en el grupo incluida la cuenta de almacenamiento predeterminada.

    azure group delete <Resource Group Name>

##<a name="scale-clusters"></a>Clústeres de escala

Para cambiar el tamaño de clúster Hadoop:

    azure hdinsight cluster resize [options] <clusterName> <Target Instance Count>


## <a name="enabledisable-http-access-for-a-cluster"></a>Habilitar o deshabilitar el acceso para un clúster de HTTP

    azure hdinsight cluster enable-http-access [options] <Cluster Name> <userName> <password>
    azure hdinsight cluster disable-http-access [options] <Cluster Name>

## <a name="enabledisable-rdp-access-for-a-cluster"></a>Habilitar o deshabilitar el acceso RDP para un clúster

    azure hdinsight cluster enable-rdp-access [options] <Cluster Name> <rdpUserName> <rdpPassword> <rdpExpiryDate>
    azure hdinsight cluster disable-rdp-access [options] <Cluster Name>


##<a name="next-steps"></a>Pasos siguientes
En este artículo, ha aprendido realizar tareas administrativas de clúster HDInsight diferentes. Para obtener más información, consulte los artículos siguientes:

* [Administrar HDInsight mediante el Portal de Azure] [hdinsight-admin-portal]
* [Administrar HDInsight con PowerShell de Azure] [hdinsight-admin-powershell]
* [Introducción a HDInsight de Azure] [hdinsight-get-started]
* [Cómo utilizar la CLI de Azure] [azure-command-line-tools]


[azure-command-line-tools]: ../xplat-cli-install.md
[azure-create-storageaccount]: ../storage-create-storage-account.md
[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/


[hdinsight-admin-portal]: hdinsight-administer-use-management-portal.md
[hdinsight-admin-powershell]: hdinsight-administer-use-powershell.md
[hdinsight-get-started]: hdinsight-hadoop-linux-tutorial-get-started.md

[image-cli-account-download-import]: ./media/hdinsight-administer-use-command-line/HDI.CLIAccountDownloadImport.png
[image-cli-clustercreation]: ./media/hdinsight-administer-use-command-line/HDI.CLIClusterCreation.png
[image-cli-clustercreation-config]: ./media/hdinsight-administer-use-command-line/HDI.CLIClusterCreationConfig.png
[image-cli-clusterlisting]: ./media/hdinsight-administer-use-command-line/HDI.CLIListClusters.png "Lista y mostrar clústeres"
