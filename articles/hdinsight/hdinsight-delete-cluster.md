<properties
pageTitle="Cómo eliminar un clúster de HDInsight | Azure"
description="Obtener información sobre las distintas maneras en que puede eliminar un clúster de HDInsight."
services="hdinsight"
documentationCenter=""
authors="Blackmist"
manager="jhubbard"
editor="cgronlun"/>

<tags
ms.service="hdinsight"
ms.devlang="na"
ms.topic="article"
ms.tgt_pltfrm="na"
ms.workload="big-data"
ms.date="10/28/2016"
ms.author="larryfr"/>

#<a name="how-to-delete-an-hdinsight-cluster"></a>Cómo eliminar un clúster de HDInsight

HDInsight clúster de facturación se inicia una vez un clúster se crea y se detiene cuando el clúster se elimina y es Prorrateado por minuto, de modo que siempre debe eliminar el clúster cuando ya no está en uso. En este documento, obtendrá información sobre cómo eliminar un clúster mediante el Portal de Azure, PowerShell de Azure y Azure CLI.

> [AZURE.IMPORTANT] Eliminar un clúster de HDInsight no elimina las cuentas de Azure almacenamiento asociadas con el clúster. Esto le permite conservar y volver a los datos almacenados por el clúster.

##<a name="azure-portal"></a>Portal de Azure

1. Inicie sesión en el [portal de Azure](https://portal.azure.com) y seleccione el clúster HDInsight. Si el clúster HDInsight no está anclado a los paneles, puede buscar para él por nombre mediante el campo de búsqueda (icono de lupa), en el lado derecho de la barra de navegación.

    ![búsqueda del portal](./media/hdinsight-delete-cluster/navbar.png)

2. Una vez que abre el módulo para el clúster, seleccione el icono de __Eliminar__ . Cuando se le solicite, seleccione __Sí__ para eliminar el clúster.

    ![icono Eliminar](./media/hdinsight-delete-cluster/deletecluster.png)

##<a name="azure-powershell"></a>Azure PowerShell

Desde un símbolo del sistema de PowerShell, use el comando siguiente para eliminar el clúster:

    Remove-AzureRmHDInsightCluster -ClusterName CLUSTERNAME

Reemplazar __NOMBREDECLÚSTER__ con el nombre de su clúster HDInsight.

##<a name="azure-cli"></a>CLI de Azure

Desde un símbolo del sistema, use las siguientes acciones para eliminar el clúster:

    azure hdinsight cluster delete CLUSTERNAME
    
Reemplazar __NOMBREDECLÚSTER__ con el nombre de su clúster HDInsight.
