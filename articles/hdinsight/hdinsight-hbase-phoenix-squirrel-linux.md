<properties 
   pageTitle="Usar Apache Caracas y ardilla en HDInsight | Microsoft Azure" 
   description="Obtenga información sobre cómo usar Caracas Apache en HDInsight y cómo instalar y configurar ardilla en su estación de trabajo para conectarse a un clúster de HBase en HDInsight." 
   services="hdinsight" 
   documentationCenter="" 
   authors="mumian" 
   manager="jhubbard" 
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="09/02/2016"
   ms.author="jgao"/>

# <a name="use-apache-phoenix-with-linux-based-hbase-clusters-in-hdinsight"></a>Usar Apache Caracas con clústeres basados en Linux HBase en HDInsight  

Obtenga información sobre cómo usar [Caracas Apache](http://phoenix.apache.org/) en HDInsight y cómo usar SQLLine. Para obtener más información acerca de Caracas, vea [Caracas en 15 minutos o menos](http://phoenix.apache.org/Phoenix-in-15-minutes-or-less.html). Para la gramática Caracas, vea [Caracas gramática](http://phoenix.apache.org/language/index.html).

>[AZURE.NOTE] Para obtener información de versión de Caracas en HDInsight, consulte [Novedades en las versiones de clúster Hadoop proporcionadas por HDInsight?] [hdinsight-versions].

##<a name="use-sqlline"></a>Usar SQLLine
[SQLLine](http://sqlline.sourceforge.net/) es una herramienta de línea de comandos para ejecutar SQL. 

###<a name="prerequisites"></a>Requisitos previos
Antes de poder usar SQLLine, debe tener el siguiente:

- **Clúster A HBase en HDInsight**. Para obtener información sobre la disposición HBase de clúster, vea [Introducción a Apache HBase en HDInsight][hdinsight-hbase-get-started].
- **Conectar con el clúster HBase mediante el protocolo de escritorio remoto**. Para instrucciones, consulte [Administrar Hadoop clústeres en HDInsight a través del Portal de clásico de Azure][hdinsight-manage-portal].


Cuando se conecte a un clúster de HBase, debe conectarse a uno de los Zookeepers. Cada clúster HDInsight tiene 3 Zookeepers. 

**Para averiguar el nombre de host de Zookeeper**

1. Para abrir Ambari, vaya a **https://<ClusterName>. azurehdinsight.net**.
2. Escriba el nombre de usuario HTTP (clúster) y la contraseña para iniciar sesión.
3. Menú de la izquierda, haga clic en **ZooKeeper** . Deberá ver 3 **ZooKeeper Server** enumerados.
4. Haga clic en el **Servidor de ZooKeeper** que aparece. En el panel de resumen, busque el **nombre de host**. Es similar a *zk1 jdolehb.3lnng4rcvp5uzokyktxs4a5dhd.bx.internal.cloudapp.net*.

**Usar SQLLine**

1. Conectarse al clúster mediante SSH. Para obtener instrucciones, vea [Usar SSH con basados en Linux Hadoop en HDInsight de Linux, Unix o OS X](hdinsight-hadoop-linux-use-ssh-unix.md) o [Usar SSH con basados en Linux Hadoop en HDInsight de Windows](hdinsight-hadoop-linux-use-ssh-windows.md) según el sistema operativo del equipo de cliente.

2. Desde SSH, ejecute los comandos siguientes para ejecutar SQLLine:

        cd /usr/hdp/2.2.9.1-7/phoenix/bin
        ./sqlline.py <ClusterName>:2181:/hbase-unsecure

2. Ejecute los comandos siguientes para crear una tabla de HBase e insertar algunos datos:

        CREATE TABLE Company (COMPANY_ID INTEGER PRIMARY KEY, NAME VARCHAR(225));
    
        !tables
        
        UPSERT INTO Company VALUES(1, 'Microsoft');
        
        SELECT * FROM Company;
        
        !quit

Para obtener más información, vea [SQLLine manual](http://sqlline.sourceforge.net/#manual) y la [Gramática de Caracas](http://phoenix.apache.org/language/index.html).


 
##<a name="next-steps"></a>Pasos siguientes
En este artículo, ha aprendido a usar Caracas Apache en HDInsight.  Para obtener más información, vea

- [Información general de HDInsight HBase][hdinsight-hbase-overview]: HBase es una base de datos de NoSQL de código abierto, Apache, integrada en Hadoop que proporciona acceso aleatorio y fuerte coherencia para grandes cantidades de datos estructurados y semistructured.
- [Aprovisionar HBase clústeres en una red Virtual Azure][hdinsight-hbase-provision-vnet]: con la integración de red virtual, se pueden implementar HBase clústeres en la misma red virtual que sus aplicaciones para que las aplicaciones puedan comunicarse con HBase directamente.
- [Configurar HBase replicación en HDInsight](hdinsight-hbase-geo-replication.md): Obtenga información sobre cómo configurar la replicación de HBase entre dos centros de datos de Azure. 
- [Analizar la opinión de Twitter con HBase en HDInsight][hbase-twitter-sentiment]: Obtenga información sobre cómo hacer en tiempo real de [análisis de opinión](http://en.wikipedia.org/wiki/Sentiment_analysis) de datos grandes mediante HBase en un clúster de Hadoop en HDInsight.

[azure-portal]: https://portal.azure.com
[vnet-point-to-site-connectivity]: https://msdn.microsoft.com/library/azure/09926218-92ab-4f43-aa99-83ab4d355555#BKMK_VNETPT

[hdinsight-versions]: hdinsight-component-versioning.md
[hdinsight-hbase-get-started]: hdinsight-hbase-tutorial-get-started.md
[hdinsight-manage-portal]: hdinsight-administer-use-management-portal.md#connect-to-hdinsight-clusters-by-using-rdp
[hdinsight-hbase-provision-vnet]: hdinsight-hbase-provision-vnet.md
[hdinsight-hbase-overview]: hdinsight-hbase-overview.md
[hbase-twitter-sentiment]: hdinsight-hbase-analyze-twitter-sentiment.md

[hdinsight-hbase-phoenix-sqlline]: ./media/hdinsight-hbase-phoenix-squirrel/hdinsight-hbase-phoenix-sqlline.png
[img-certificate]: ./media/hdinsight-hbase-phoenix-squirrel/hdinsight-hbase-vpn-certificate.png
[img-vnet-diagram]: ./media/hdinsight-hbase-phoenix-squirrel/hdinsight-hbase-vnet-point-to-site.png
[img-squirrel-driver]: ./media/hdinsight-hbase-phoenix-squirrel/hdinsight-hbase-squirrel-driver.png
[img-squirrel-alias]: ./media/hdinsight-hbase-phoenix-squirrel/hdinsight-hbase-squirrel-alias.png
[img-squirrel]: ./media/hdinsight-hbase-phoenix-squirrel/hdinsight-hbase-squirrel.png
[img-squirrel-sql]: ./media/hdinsight-hbase-phoenix-squirrel/hdinsight-hbase-squirrel-sql.png


 
