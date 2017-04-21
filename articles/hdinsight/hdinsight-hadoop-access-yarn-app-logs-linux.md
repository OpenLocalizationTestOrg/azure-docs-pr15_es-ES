<properties
    pageTitle="Aplicación de Access Hadoop hilo inicia sesión basados en Linux HDInsight | Microsoft Azure"
    description="Obtenga información sobre cómo obtener acceso a los registros de la aplicación de hilo en un clúster de basados en Linux HDInsight (Hadoop) mediante la línea de comandos y un explorador web."
    services="hdinsight"
    documentationCenter=""
    tags="azure-portal"
    authors="Blackmist" 
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="hdinsight"
    ms.workload="big-data"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/21/2016"
    ms.author="larryfr"/>

# <a name="access-yarn-application-logs-on-linux-based-hdinsight"></a>Aplicación de Access hilo inicia sesión HDInsight de Linux 

Este documento explica cómo tener acceso a los registros de aplicaciones de hilo (aún otro recurso negociador) que termine en un clúster de Hadoop en HDInsight de Azure.

> [AZURE.NOTE] La información de este documento es específica para clústeres HDInsight basados en Linux. Para obtener información sobre clústeres basados en Windows, vea [aplicación de Access hilo inicia sesión HDInsight de Windows](hdinsight-hadoop-access-yarn-app-logs.md)

## <a name="prerequisites"></a>Requisitos previos

* Un clúster de HDInsight basados en Linux.

* Debe [crear un túnel SSH](hdinsight-linux-ambari-ssh-tunnel.md) antes de que puede tener acceso a la interfaz de usuario de web ResourceManager registros.

## <a name="YARNTimelineServer"></a>Servidor de escala de tiempo del hilo

El [Servidor de escala de tiempo del hilo](http://hadoop.apache.org/docs/r2.4.0/hadoop-yarn/hadoop-yarn-site/TimelineServer.html) proporciona información genérica en aplicaciones completadas, así como información de la aplicación específicas del marco a través de dos interfaces diferentes. Específicamente:

* Almacenamiento y la recuperación de la información de la aplicación genérica en clústeres HDInsight ha habilitado con versión 3.1.1.374 o superior.
* El componente de información de la aplicación específicas del marco del servidor de escala de tiempo no está disponible actualmente en clústeres HDInsight.

Obtener información general sobre aplicaciones incluye a los siguientes tipos de datos:

* El identificador de aplicación, un identificador único de una aplicación
* El usuario que ha iniciado la aplicación
* Obtener información acerca de los intentos realizados completar la aplicación
* Los contenedores de cualquier intento de aplicación

## <a name="YARNAppsAndLogs"></a>Registros y aplicaciones de hilo

HILO admite varios modelos de programación (MapReduce ser uno de ellos) disociación de administración de recursos de programación/supervisión de aplicaciones. Esto se realiza mediante un global *ResourceManager* (RM), nodo de trabajo *NodeManagers* (NMs) y por la aplicación de *ApplicationMasters* (AMs). El AM por aplicación buscará recursos (CPU, memoria, disco, red) ejecuta la aplicación con la RM. RM funciona con NMs para conceder a estos recursos, que se haya concedido como *contenedores*. La A.M. es responsable del seguimiento del progreso de los contenedores le asignada el RM. Una aplicación puede requerir muchos contenedores dependiendo de la naturaleza de la aplicación.

Además, cada aplicación puede constar de varios *intentos de aplicación* para finalizar en presencia se bloquea o debido a la pérdida de comunicación entre AM y un RM. Por lo tanto, los contenedores se conceden a un intento específico de una aplicación. En un sentido, un contenedor proporciona el contexto de la unidad básica del trabajo realizado por una aplicación de hilo y todo el trabajo que se realiza en el contexto de un contenedor se realiza en el nodo de trabajo en el que se ha asignado el contenedor. Vea [Conceptos hilo] [ YARN-concepts] para consultarla posteriormente.

Registros de la aplicación (y los registros de contenedor asociado) son críticos en las aplicaciones de Hadoop problemáticas de depuración. HILO proporciona un marco bueno para recopilar, agregar y almacenar registros de la aplicación con la [Agregación de registro] [ log-aggregation] característica. La característica de agregación de registro hace que acceder a registros de la aplicación más determinista, ya que agrega registros a través de todos los contenedores en un nodo de trabajo y almacena como un archivo registro agregado por el nodo de trabajo en el sistema de archivos de forma predeterminada cuando termine de una aplicación. Puede utilizar la aplicación cientos o miles de contenedores, pero siempre se agregarán registros para todos los contenedores que se ejecute en un nodo de trabajo en un solo archivo de un archivo de registro por nodo de trabajo utilizado por la aplicación. Agregación de registro está habilitado de forma predeterminada en HDInsight clústeres (versión 3.0 y anteriores), y pueden encontrar registros agregados en el contenedor predeterminado del clúster en la siguiente ubicación:

    wasbs:///app-logs/<user>/logs/<applicationId>

En cuanto a la ubicación, el *usuario* es el nombre del usuario que ha iniciado la aplicación y *applicationId* es el identificador único de una aplicación asignado por el RM hilo.

Los registros agregados no están directamente legibles, tal como se escriben en un [TFile][T-file], [formato binario] [ binary-format] indizado por contenedor. Debe usar las herramientas CLI o hilo ResourceManager registros para ver estos registros como texto sin formato para aplicaciones o contenedores de interés. 

##<a name="yarn-cli-tools"></a>Herramientas CLI hilo

Para utilizar las herramientas de hilo CLI, primero debe conectarse al clúster HDInsight mediante SSH. Para obtener información sobre cómo utilizar SSH con HDInsight, use uno de los documentos siguientes:

- [Utilizar SSH con basados en Linux Hadoop en HDInsight de Linux, Unix o OS X](hdinsight-hadoop-linux-use-ssh-unix.md)

- [Utilizar SSH con basados en Linux Hadoop en HDInsight de Windows.](hdinsight-hadoop-linux-use-ssh-windows.md)
    
Para ver estos registros como texto sin formato, ejecute uno de los siguientes comandos:

    yarn logs -applicationId <applicationId> -appOwner <user-who-started-the-application>
    yarn logs -applicationId <applicationId> -appOwner <user-who-started-the-application> -containerId <containerId> -nodeAddress <worker-node-address>
    
Debe especificar el &lt;applicationId >, &lt;usuario que-introducción-la aplicación >, &lt;IdContenedor >, y & dirección de nodo ltworker > información cuando se ejecuta estos comandos.

##<a name="yarn-resourcemanager-ui"></a>Interfaz de usuario de hilo ResourceManager

La interfaz de usuario de ResourceManager hilo se ejecuta en el clúster de headnode y se puede acceder a través de la interfaz de usuario; de Ambari web Sin embargo, primero debe [crear un túnel SSH](hdinsight-linux-ambari-ssh-tunnel.md) antes de poder acceder a la UI ResourceManager.

Una vez que haya creado un túnel SSH, realice los pasos siguientes para ver los registros del hilo:

1. En el explorador web, vaya a https://CLUSTERNAME.azurehdinsight.net. Reemplazar NOMBREDECLÚSTER con el nombre de su clúster HDInsight.

2. En la lista de servicios de la izquierda, seleccione __hilo__.

    ![Servicio de hilo seleccionado](./media/hdinsight-hadoop-access-yarn-app-logs-linux/yarnservice.png)

3. En la lista desplegable __Vínculos rápidos__ , seleccione uno de los nodos de clúster cabezal y seleccione __Registro ResourceManager__.

    ![Linnks rápida de hilo](./media/hdinsight-hadoop-access-yarn-app-logs-linux/yarnquicklinks.png)
    
    Aparecerá una lista de vínculos a los registros del hilo.

[YARN-timeline-server]:http://hadoop.apache.org/docs/r2.4.0/hadoop-yarn/hadoop-yarn-site/TimelineServer.html
[log-aggregation]:http://hortonworks.com/blog/simplifying-user-logs-management-and-access-in-yarn/
[T-file]:https://issues.apache.org/jira/secure/attachment/12396286/TFile%20Specification%2020081217.pdf
[binary-format]:https://issues.apache.org/jira/browse/HADOOP-3315
[YARN-concepts]:http://hortonworks.com/blog/apache-hadoop-yarn-concepts-and-applications/
