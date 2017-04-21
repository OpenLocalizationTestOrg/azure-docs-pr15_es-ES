<properties
    pageTitle="Usar subárbol interactivo en HDInsight | Microsoft Azure"
    description="Aprenda a utilizar la sección interactiva (subárbol en LLAP) en HDInsight."
    keywords=""
    services="hdinsight"
    documentationCenter=""
    tags="azure-portal"
    authors="mumian" 
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="hdinsight"
    ms.workload="big-data"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/27/2016"
    ms.author="jgao"/>


# <a name="use-interactive-hive-in-hdinsight-preview"></a>Usar subárbol interactivo en HDInsight (vista previa)

Interactivo subárbol (también conocido como [Long directo y proceso]( https://cwiki.apache.org/confluence/display/Hive/LLAP)) es un nuevo [tipo de clúster]( hdinsight-hadoop-provision-linux-clusters.md#cluster-types)de HDInsight.  Sección interactiva permite en la memoria caché que hace que las consultas de la sección mucho más interactivo y más rápido. Esta nueva característica hace HDInsight de todo el mundo más eficaz, flexible y Abrir solución grande de datos en la nube con en memoria se almacena en caché (con la sección y motor) y avanzadas análisis a través de la integración con servicios de R. 

El clúster subárbol interactivo es diferente de clúster Hadoop. Solo contiene el servicio de sección. 

> [AZURE.NOTE] MapReduce, cerdo, Sqoop, Oozie y otros servicios se quitarán de este tipo de clúster pronto.
El servicio de sección en el clúster subárbol interactiva solo es accesible a través de la vista de sección Ambari, Beeline y subárbol ODBC. No será accesible a través de la consola, Templeton, CLI de Azure y Azure PowerShell subárbol. 


 


## <a name="create-an-interactive-hive-cluster"></a>Crear un clúster de subárbol interactivos

Interactivo clúster subárbol solo es compatible con clústeres basados en Linux. Para obtener información sobre la creación de clústeres HDInsight, consulte [clústeres basados en Linux crear Hadoop en HDInsight](hdinsight-hadoop-provision-linux-clusters.md).


## <a name="execute-hive-from-interactive-hive"></a>Ejecutar subárbol subárbol interactivos

Existen diferentes opciones de cómo puede ejecutar consultas de la sección:

- Ejecutar subárbol usando la vista de la sección de Ambari

    Para obtener más información sobre el uso de la vista de sección, consulte [usar la vista subárbol con Hadoop en HDInsight]( hdinsight-hadoop-use-hive-ambari-view.md).

- Ejecutar subárbol con Beeline

    Para obtener más información sobre el uso de Beeline en HDInsight, consulte [Usar subárbol con Hadoop en HDInsight con Beeline](hdinsight-hadoop-use-hive-beeline.md).

    Usar Beeline desde el headnode o un nodo del borde vacío.  Se recomienda utilizar Beeline desde un nodo del borde vacío.  Para obtener información sobre cómo crear un clúster de HDInsight con un edgenode vacío, vea [nodos de borde vacío de uso en HDInsight](hdinsight-apps-use-edge-node.md).

- Ejecutar subárbol mediante subárbol ODBC

    Para obtener más información sobre el uso de la sección ODBC, vea [Conectar Excel a Hadoop con el controlador ODBC subárbol de Microsoft](hdinsight-connect-excel-hive-odbc-driver.md).

**Para buscar la cadena de conexión JDBC:**

1.  Inicie sesión Ambari utilizando la siguiente URL: https://<ClusterName>. AzureHDInsight.net.
2.  Menú de la izquierda, haga clic en **sección** .
3.  Haga clic en el icono de resaltado para copiar la dirección URL:

    ![HDInsight Hadoop subárbol interactiva LLAP JDBC](./media/hdinsight-hadoop-use-interactive-hive/hdinsight-hadoop-use-interactive-hive-jdbc.png)

## <a name="see-also"></a>Vea también
-   [Hadoop basados en Linux crear clústeres en HDInsight](hdinsight-hadoop-provision-linux-clusters.md): Obtenga información sobre cómo crear clústeres subárbol interactivos en HDInsight.
-   [Usar subárbol con Hadoop en HDInsight con Beeline](hdinsight-hadoop-use-hive-beeline.md): Obtenga información sobre cómo usar Beeline para enviar las consultas de la sección.
-   [Conectar Excel a Hadoop con el controlador ODBC subárbol de Microsoft](hdinsight-connect-excel-hive-odbc-driver.md): Obtenga información sobre cómo conectar Excel a la sección.
