<properties
    pageTitle="Aplicación de Access Hadoop hilo registros mediante programación | Microsoft Azure"
    description="Aplicación de Access mediante programación registros en un clúster de Hadoop en HDInsight."
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
    ms.date="10/19/2016"
    ms.author="jgao"/>

# <a name="access-yarn-application-logs-on-windows-based-hdinsight"></a>Aplicación de Access hilo inicia sesión HDInsight de Windows

Este tema explica cómo tener acceso a los registros de aplicaciones de hilo (aún otro recurso negociador) que termine en un clúster de Hadoop en HDInsight de Azure

> [AZURE.NOTE] La información en este documento se aplica solo a clústeres HDInsight basado en Windows. Para obtener información sobre cómo acceder a hilo inicia sesión clústeres basados en Linux HDInsight, consulte [aplicación de Access hilo inicia sesión basados en Linux Hadoop en HDInsight](hdinsight-hadoop-access-yarn-app-logs-linux.md)

### <a name="prerequisites"></a>Requisitos previos

- Un clúster basado en Windows HDInsight.  Consulte [Hadoop basado en Windows crear clústeres en HDInsight](hdinsight-provision-clusters.md).


## <a name="yarn-timeline-server"></a>Servidor de escala de tiempo del hilo

El <a href="http://hadoop.apache.org/docs/r2.4.0/hadoop-yarn/hadoop-yarn-site/TimelineServer.html" target="_blank">Servidor de escala de tiempo del hilo</a> proporciona información genérica en aplicaciones completadas, así como información de la aplicación específicas del marco a través de dos interfaces diferentes. Específicamente:

* Almacenamiento y la recuperación de la información de la aplicación genérica en clústeres HDInsight ha habilitado con versión 3.1.1.374 o superior.
* El componente de información de la aplicación específicas del marco del servidor de escala de tiempo no está disponible actualmente en clústeres HDInsight.


Obtener información general sobre aplicaciones incluye a los siguientes tipos de datos:

* El identificador de aplicación, un identificador único de una aplicación
* El usuario que ha iniciado la aplicación
* Obtener información acerca de los intentos realizados completar la aplicación
* Los contenedores de cualquier intento de aplicación

En los clústeres HDInsight, esta información se almacena por el Administrador de recursos de Azure a un almacén de historial en el contenedor predeterminado de la cuenta predeterminada de almacenamiento de Azure. Estos datos en aplicaciones completadas genéricos se pueden recuperar a través de la API de REST:

    GET on https://<cluster-dns-name>.azurehdinsight.net/ws/v1/applicationhistory/apps


## <a name="yarn-applications-and-logs"></a>Registros y aplicaciones de hilo

HILO admite varios modelos de programación (MapReduce ser uno de ellos) disociación de administración de recursos de programación/supervisión de aplicaciones. Esto se realiza mediante un global *ResourceManager* (RM), nodo de trabajo *NodeManagers* (NMs) y por la aplicación de *ApplicationMasters* (AMs). El AM por aplicación buscará recursos (CPU, memoria, disco, red) ejecuta la aplicación con la RM. RM funciona con NMs para conceder a estos recursos, que se haya concedido como *contenedores*. La A.M. es responsable del seguimiento del progreso de los contenedores le asignada el RM. Una aplicación puede requerir muchos contenedores dependiendo de la naturaleza de la aplicación.

Además, cada aplicación puede constar de varios *intentos de aplicación* para finalizar en presencia se bloquea o debido a la pérdida de comunicación entre AM y un RM. Por lo tanto, los contenedores se conceden a un intento específico de una aplicación. En un sentido, un contenedor proporciona el contexto de la unidad básica del trabajo realizado por una aplicación de hilo y todo el trabajo que se realiza en el contexto de un contenedor se realiza en el nodo de trabajo en el que se ha asignado el contenedor. Vea [Conceptos hilo] [ YARN-concepts] para consultarla posteriormente.

Registros de la aplicación (y los registros de contenedor asociado) son críticos en las aplicaciones de Hadoop problemáticas de depuración. HILO proporciona un marco bueno para recopilar, agregar y almacenar registros de la aplicación con la [Agregación de registro] [ log-aggregation] característica. La característica de agregación de registro hace que acceder a registros de la aplicación más determinista, ya que agrega registros a través de todos los contenedores en un nodo de trabajo y almacena como un archivo registro agregado por el nodo de trabajo en el sistema de archivos de forma predeterminada cuando termine de una aplicación. Puede utilizar la aplicación cientos o miles de contenedores, pero siempre se agregarán registros para todos los contenedores que se ejecute en un nodo de trabajo en un solo archivo de un archivo de registro por nodo de trabajo utilizado por la aplicación. Agregación de registro está habilitado de forma predeterminada en HDInsight clústeres (versión 3.0 y anteriores), y pueden encontrar registros agregados en el contenedor predeterminado del clúster en la siguiente ubicación:

    wasbs:///app-logs/<user>/logs/<applicationId>

En cuanto a la ubicación, el *usuario* es el nombre del usuario que ha iniciado la aplicación y *applicationId* es el identificador único de una aplicación asignado por el RM hilo.

Los registros agregados no están directamente legibles, tal como se escriben en un [TFile][T-file], [formato binario] [ binary-format] indizado por contenedor. HILO proporciona herramientas CLI para la descarga de estos registros como texto sin formato para aplicaciones o contenedores de interés. Puede ver estos registros como texto sin formato, ejecute una del siguiente hilo comandos directamente en los nodos de clúster (después de conectarse a ella mediante RDP):

    yarn logs -applicationId <applicationId> -appOwner <user-who-started-the-application>
    yarn logs -applicationId <applicationId> -appOwner <user-who-started-the-application> -containerId <containerId> -nodeAddress <worker-node-address>


## <a name="yarn-resourcemanager-ui"></a>Interfaz de usuario de hilo ResourceManager

La interfaz de usuario de ResourceManager hilo se ejecuta en el clúster de headnode y se puede acceder a través del panel de portal Azure: 

1. Inicie sesión en el [portal de Azure](https://portal.azure.com/). 
2. En el menú de la izquierda, haga clic en **Examinar**, haga clic en **HDInsight clústeres**, haga clic en un clúster de Windows que desea tener acceso a los registros de aplicaciones del hilo.
3. En el menú superior, haga clic en **panel**. Verá una página abierta en un explorador nuevo ficha llamado **HDInsight consola de consulta**.
4. Desde la **Consola de HDInsight de consulta**, haga clic en **Interfaz de usuario del hilo**.




[YARN-timeline-server]:http://hadoop.apache.org/docs/r2.4.0/hadoop-yarn/hadoop-yarn-site/TimelineServer.html
[log-aggregation]:http://hortonworks.com/blog/simplifying-user-logs-management-and-access-in-yarn/
[T-file]:https://issues.apache.org/jira/secure/attachment/12396286/TFile%20Specification%2020081217.pdf
[binary-format]:https://issues.apache.org/jira/browse/HADOOP-3315
[YARN-concepts]:http://hortonworks.com/blog/apache-hadoop-yarn-concepts-and-applications/
