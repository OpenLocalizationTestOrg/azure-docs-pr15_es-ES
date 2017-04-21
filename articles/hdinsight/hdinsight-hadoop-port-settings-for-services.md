<properties
pageTitle="Puertos usados por HDInsight | Azure"
description="Una lista de puertos utilizados por servicios Hadoop en HDInsight."
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
ms.date="10/03/2016"
ms.author="larryfr"/>

# <a name="ports-and-uris-used-by-hdinsight"></a>Puertos y protocolos de URI usado por HDInsight

Este documento proporciona una lista de los puertos utilizados por servicios Hadoop en clústeres HDInsight basados en Linux. También se proporciona información sobre los puertos que usa para conectarse al clúster mediante SSH.

## <a name="public-ports-vs-non-public-ports"></a>Puertos públicos frente a puertos no públicos

Basados en Linux HDInsight clústeres expone solo tres puertos públicamente en internet; 22, 23 y 443. Se usan para el acceso de forma segura al clúster mediante SSH y servicios que se expone a través del protocolo HTTPS seguro.

Internamente, HDInsight se implementa por varios Azure máquinas virtuales de Windows (los nodos en el clúster) funcionando en una red Virtual de Azure. Desde dentro de la red virtual, puede acceder a los puertos que no se expone a través de internet. Por ejemplo, si se conecta a uno de los nodos de cabeza con SSH, desde el nodo principal puede puede directamente acceso a servicios que se ejecutan en los nodos de clúster.

> [AZURE.IMPORTANT] Cuando se crea un clúster HDInsight, si no especifica una red Virtual de Azure como una opción de configuración, se creará uno; Sin embargo, no puede unirse a otros equipos (como otras máquinas virtuales de Azure o el equipo de desarrollo de cliente) a esto crea automáticamente una red virtual. 

Para unirse a equipos adicionales a la red virtual, debe crear una red virtual primero y especificarlo al crear el clúster de HDInsight. Para obtener más información, vea [capacidades de extender HDInsight mediante una red Virtual de Azure](hdinsight-extend-hadoop-virtual-network.md)

## <a name="public-ports"></a>Puertos públicos

Todos los nodos en un clúster de HDInsight se encuentran en una red Virtual de Azure y no se puede acceder directamente desde internet. Una puerta de enlace público proporciona acceso a internet a los puertos siguientes, que son comunes a todos los tipos de clúster HDInsight.

| Servicio | Puerto | Protocolo | Descripción |
| ---- | ---------- | -------- | ----------- | ----------- |
| sshd | 22 | SSH | Se conecta a los clientes a sshd en el headnode principal. Consulte [usar SSH con HDInsight de Linux](hdinsight-hadoop-linux-use-ssh-windows.md) |
| sshd | 22 | SSH | Se conecta a los clientes a sshd en el nodo del borde (sólo HDInsight Premium). Vea [Introducción al uso de servidor R HDInsight](hdinsight-hadoop-r-server-get-started.md) |
| sshd | 23 | SSH | Se conecta a los clientes a sshd en el headnode secundario. Consulte [usar SSH con HDInsight de Linux](hdinsight-hadoop-linux-use-ssh-windows.md) |
| Ambari | 443 | HTTPS | Interfaz de usuario de Ambari web. Consulte [Administrar HDInsight utilizando la interfaz de usuario de Web Ambari](hdinsight-hadoop-manage-ambari.md) |
| Ambari | 443 | HTTPS | API de REST de Ambari. Consulte [Administrar HDInsight con la API de REST Ambari](hdinsight-hadoop-manage-ambari-rest-api.md) |
| WebHCat | 443 | HTTPS | API de REST de HCatalog. Vea [usar subárbol con rizo](hdinsight-hadoop-use-pig-curl.md), [Use cerdo con rizo](hdinsight-hadoop-use-pig-curl.md), [Use MapReduce con rizo](hdinsight-hadoop-use-mapreduce-curl.md) |
| HiveServer2 | 443 | ODBC | Se conecta a la sección con ODBC. Vea [Conectar Excel a HDInsight con el controlador ODBC de Microsoft](hdinsight-connect-excel-hive-odbc-driver.md). |
| HiveServer2 | 443 | JDBC | Se conecta a la sección con JDBC. Vea [conectarse a subárbol en HDInsight con el controlador de JDBC subárbol](hdinsight-connect-hive-jdbc-driver.md) |

Las siguientes están disponibles para determinados tipos de clúster:

| Servicio | Puerto | Protocolo |Tipo de clúster | Descripción |
| ------------ | ---- |  ----------- | --- | ----------- |
| Vídeo STARGATE | 443 | HTTPS | HBase | API de REST de HBase. Vea [Introducción al uso HBase](hdinsight-hbase-tutorial-get-started-linux.md) |
| Livio | 443 | HTTPS |  Motor | API de REST de motor. Vea [trabajos de motor enviar remotamente mediante Livio](hdinsight-apache-spark-livy-rest-interface.md) |
| Tormenta | 443 | HTTPS | Tormenta | Interfaz de usuario de web tormenta. Consulte [implementar y administrar topologías tormenta en HDInsight](hdinsight-storm-deploy-monitor-topology-linux.md)

### <a name="authentication"></a>Autenticación

Se deben autenticar todos los servicios que se exponen públicamente en internet:

| Puerto | Credenciales |
| ---- | ----------- |
| 22 o 23 | Las credenciales de usuario SSH especificadas durante la creación de un clúster |
| 443 | El nombre de inicio de sesión (predeterminado: administrador,) y la contraseña que se estableció durante la creación de clúster |

## <a name="non-public-ports"></a>Puertos no públicos

> [AZURE.NOTE] Algunos servicios solo están disponibles en determinados tipos de clúster. Por ejemplo, HBase solo está disponible en los tipos de clúster HBase.

### <a name="hdfs-ports"></a>Puertos HDFS

| Servicio | Nodos | Puerto | Protocolo | Descripción |
| ------- | ------- | ---- | -------- | ----------- | 
| Interfaz de usuario de web NameNode | Nodos de cabeza | 30070 | HTTPS | Interfaz de usuario para ver el estado actual de la Web |
| Servicio de metadatos de NameNode | nodos cabezales | 8020 | IPC | Metadatos del sistema de archivos 
| DataNode | Todos los nodos de trabajo | 30075 | HTTPS | Interfaz de usuario Web para ver el estado, registros, etcetera. |
| DataNode | Todos los nodos de trabajo | 30010 | &nbsp; | Transferencia de datos |
| DataNode | Todos los nodos de trabajo | 30020 | IPC | Operaciones de metadatos |
| NameNode secundario | Nodos de cabeza | 50090 | HTTP | Control de metadatos NameNode |

### <a name="yarn-ports"></a>Puertos del hilo

| Servicio | Nodos | Puerto | Protocolo | Descripción |
| ------- | ------- | ---- | -------- | ----------- |
| Interfaz de usuario del Administrador de recursos web | Nodos de cabeza | 8088 | HTTP | Interfaz de usuario para el Administrador de recursos Web |
| Interfaz de usuario del Administrador de recursos web | Nodos de cabeza | 8090 | HTTPS | Interfaz de usuario para el Administrador de recursos Web |
| Interfaz de administración de administrador de recursos | nodos cabezales | 8141 | IPC | Para los envíos de la aplicación (subárbol, servidor subárbol, cerdo, etcetera.) |
| Programador de administrador de recursos | nodos cabezales | 8030 | HTTP | Interfaz administrativa |
| Interfaz de administrador de recursos de aplicaciones | nodos cabezales | 8050 | HTTP |Dirección de la interfaz de administrador de aplicaciones |
| NodeManager | Todos los nodos de trabajo | 30050 | &nbsp; | La dirección del Administrador de contenedor |
| Interfaz de usuario de web NodeManager | Todos los nodos de trabajo | 30060 | HTTP | Interfaz de administrador de recursos |
| Dirección de la escala de tiempo | Nodos de cabeza | 10200 | RPC | El servicio RPC del servicio de escala de tiempo. |
| Interfaz de usuario de web de escala de tiempo | Nodos de cabeza | 8181 | HTTP | La interfaz de usuario de web de servicio de escala de tiempo |

### <a name="hive-ports"></a>Puertos de sección

| Servicio | Nodos | Puerto | Protocolo | Descripción |
| ------- | ------- | ---- | -------- | ----------- |
| HiveServer2 | Nodos de cabeza | 10001 | Thrift | Servicio para conectarse mediante programación a la sección (Thrift/JDBC) |
| HiveServer | Nodos de cabeza | 10000 | Thrift | Servicio para conectarse mediante programación a la sección (Thrift/JDBC) |
| Sección Metastore | Nodos de cabeza | 9083 | Thrift | Servicio para conectarse mediante programación a los metadatos de subárbol (Thrift/JDBC) |

### <a name="webhcat-ports"></a>Puertos WebHCat

| Servicio | Nodos | Puerto | Protocolo | Descripción |
| ------- | ------- | ---- | -------- | ----------- |
| Servidor de WebHCat | Nodos de cabeza | 30111 | HTTP | API de Web en la parte superior HCatalog y otros servicios de Hadoop |

### <a name="mapreduce-ports"></a>Puertos MapReduce

| Servicio | Nodos | Puerto | Protocolo | Descripción |
| ------- | ------- | ---- | -------- | ----------- |
| JobHistory | Nodos de cabeza | 19888 | HTTP | Interfaz de usuario de web MapReduce JobHistory |
| JobHistory | Nodos de cabeza | 10020 | &nbsp; | MapReduce JobHistory server |
| ShuffleHandler | &nbsp; | 13562 | &nbsp; | Mapa de transferencias intermedio muestra los resultados en solicitando reductores |

### <a name="oozie"></a>Oozie

| Servicio | Nodos | Puerto | Protocolo | Descripción |
| ------- | ------- | ---- | -------- | ----------- |
| Servidor de Oozie | Nodos de cabeza | 11000 | HTTP | Dirección URL de servicio de Oozie |
| Servidor de Oozie | Nodos de cabeza | 11001 | HTTP | Puerto para la administración de Oozie |

### <a name="ambari-metrics"></a>Métrica de Ambari

| Servicio | Nodos | Puerto | Protocolo | Descripción |
| ------- | ------- | ---- | -------- | ----------- |
| Escala de tiempo (historial de la aplicación) | Nodos de cabeza | 6188 | HTTP | La interfaz de usuario de web de servicio de escala de tiempo |
| Escala de tiempo (historial de la aplicación) | Nodos de cabeza | 30200 | RPC | La interfaz de usuario de web de servicio de escala de tiempo |

### <a name="hbase-ports"></a>Puertos HBase

| Servicio | Nodos | Puerto | Protocolo | Descripción |
| ------- | ------- | ---- | -------- | ----------- |
| HMaster | Nodos de cabeza | 16000. | &nbsp; | &nbsp; |
| Información de HMaster interfaz de usuario de Web | Nodos de cabeza | 16010 | HTTP | El puerto para la interfaz de usuario de web HBase patrón |
| Servidor de región | Todos los nodos de trabajo | 16020 | &nbsp; | &nbsp; |
| &nbsp; | &nbsp; | 2181 | &nbsp; | El puerto que utilizan los clientes para conectarse a ZooKeeper |

### <a name="kafka-ports"></a>Puertos Kafka

| Servicio | Nodos | Puerto | Protocolo | Descripción |
| ------- | ------- | ---- | -------- | ----------- |
| Agente  | Nodos de trabajo | 9092 | [Protocolo Kafka](http://kafka.apache.org/protocol.html) | Usar para la comunicación de cliente |
| &nbsp; | Nodos de zookeeper | 2181 | &nbsp; | El puerto que utilizan los clientes para conectarse a Zookeeper |
