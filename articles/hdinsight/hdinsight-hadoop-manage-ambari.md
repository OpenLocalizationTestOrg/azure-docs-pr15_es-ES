<properties
   pageTitle="Supervisar y administrar clústeres de HDInsight mediante la interfaz de usuario de Web Apache Ambari | Microsoft Azure"
   description="Obtenga información sobre cómo utilizar Ambari para supervisar y administrar clústeres HDInsight basados en Linux. En este documento, obtendrá información sobre cómo usar la interfaz de usuario de Web Ambari incluido con HDInsight clústeres."
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
   ms.date="09/27/2016"
   ms.author="larryfr"/>

#<a name="manage-hdinsight-clusters-by-using-the-ambari-web-ui"></a>Administrar clústeres HDInsight mediante la interfaz de usuario de Web Ambari

[AZURE.INCLUDE [ambari-selector](../../includes/hdinsight-ambari-selector.md)]

Apache Ambari simplifica la administración y supervisión de un clúster de Hadoop proporcionando un más fáciles de usar la interfaz de usuario web y las API de REST. Ambari se incluye en clústeres basados en Linux HDInsight y se usa para supervisar el clúster y realizar cambios de configuración.

En este documento, obtendrá información sobre cómo usar la interfaz de usuario de Web Ambari con un clúster de HDInsight.

##<a id="whatis"></a>¿Qué es Ambari?

<a href="http://ambari.apache.org" target="_blank">Apache Ambari</a> simplifica la administración de Hadoop proporcionando un web fácil de usar la interfaz de usuario que se pueden usar para aprovisionar, administrar y supervisar los clústeres Hadoop. Los desarrolladores pueden integrar estas funciones en sus aplicaciones usando las <a href="https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md" target="_blank">API de REST de Ambari</a>.

Se proporciona la interfaz de usuario de Web Ambari con clústeres basados en Linux HDInsight de forma predeterminada. 

##<a name="connectivity"></a>Conectividad

La interfaz de usuario de Web Ambari está disponible en el clúster de HDInsight en HTTPS://CLUSTERNAME.azurehdidnsight.net, donde __NOMBREDECLÚSTER__ es el nombre del clúster. 

> [AZURE.IMPORTANT] Conectarse a Ambari en HDInsight requiere HTTPS. También debe autenticar a Ambari con el nombre de la cuenta de administrador (el valor predeterminado es __Administrador__) y la contraseña proporcionada cuando se creó el clúster.

##<a name="ssh-proxy"></a>Proxy SSH

> [AZURE.NOTE] Mientras Ambari para el clúster es accesible directamente a través de Internet, algunos vínculos de la interfaz de usuario de Web Ambari (como el JobTracker) no se exponen en internet. Así que recibirá errores "el servidor no encontrado" al intentar obtener acceso a estas características a menos que use un túnel de Shell seguro (SSH) para el tráfico de web proxy al nodo de clúster principal.

Para obtener información sobre la creación de un túnel SSH para trabajar con Ambari, consulte [Usar SSH túnel para tener acceso a la interfaz de usuario de web Ambari, ResourceManager, JobHistory, NameNode, Oozie y otra de la interfaz de usuario de web](hdinsight-linux-ambari-ssh-tunnel.md).

##<a name="ambari-web-ui"></a>Interfaz de usuario de Web Ambari

Al conectarse a la interfaz de usuario de Web Ambari, le pedirá que lo autenticar a la página. Use el usuario de administración de clúster (predeterminado Administrador) y contraseña que utilizó durante la creación de clúster.

Cuando se abre la página, tenga en cuenta la barra situada en la parte superior. Esta página contiene la siguiente información y controles:

![navegación ambari](./media/hdinsight-hadoop-manage-ambari/ambari-nav.png)

* **Logotipo de Ambari** - abre el panel, que se puede utilizar para supervisar el clúster.

* **Operaciones de clúster nombre #** - muestra el número de operaciones de Ambari en curso. Seleccionar el nombre de clúster u **operaciones #** se mostrará una lista de operaciones en segundo plano.

* **alertas de #** - advertencias o alertas críticas, si existe, para el clúster. Al seleccionar esta se mostrará una lista de las alertas.

* **Panel** - muestra el panel.

* **Servicios** - información y opciones de configuración para los servicios en el clúster.

* **Hosts** : información y opciones de configuración para los nodos en el clúster.

* **Alertas** - un registro de información, advertencias y alertas críticas.

* **Administración** - pila de Software y servicios que están instalados en el clúster, la información de la cuenta de servicio y la seguridad de Kerberos.

* **Botón de administración** - administración Ambari, configuración de usuario y cierre de sesión.

##<a name="monitoring"></a>Supervisión

###<a name="alerts"></a>Alertas

Ambari ofrece muchas alertas, que tendrán uno de los siguientes como el estado:

* **Vale**

* **Advertencia**

* **TAREAS CRÍTICAS**

* **DESCONOCIDO**

Avisos aparte de **Aceptar** hará que la entrada de **alertas de #** en la parte superior de la página para mostrar el número de alertas. Seleccionar esta entrada mostrará las alertas y su estado.

Las alertas se organizan en varios grupos de forma predeterminada, que pueden verse en la página de **alertas** .

![página de alertas](./media/hdinsight-hadoop-manage-ambari/alerts.png)

Puede administrar los grupos con el menú **acciones** y seleccione **Administrar grupos de alerta**. Esto le permite modificar grupos existentes o crear nuevos grupos.

![administrar el cuadro de diálogo Alerta de grupos](./media/hdinsight-hadoop-manage-ambari/manage-alerts.png)

También puede crear alertas en el menú **acciones** . Esto le permite crear desencadenadores que enviar notificaciones por **correo electrónico** o **SNMP** cuando se produzcan combinaciones de alerta o gravedad específica. Por ejemplo, puede enviar una alerta cuando cualquiera de las alertas en el grupo **Hilo predeterminado** se establece como **crítico**.

![Crear un cuadro de diálogo Alerta](./media/hdinsight-hadoop-manage-ambari/create-alert-notification.png)

###<a name="cluster"></a>Clúster

La ficha **métricas** del panel contiene una serie de widgets que hacen que sea fácil de supervisar el estado de su clúster de un vistazo. Widgets varios, como el **Uso de CPU**, proporcionan información adicional al hacer clic en.

![panel con métricas](./media/hdinsight-hadoop-manage-ambari/metrics.png)

La ficha **Heatmaps** muestra métricas como heatmaps coloreado, va de verde a rojo.

![panel con heatmaps](./media/hdinsight-hadoop-manage-ambari/heatmap.png)

Para obtener información más detallada sobre los nodos en el clúster, seleccione **Hosts**y, a continuación, seleccione el nodo específico que le interesa.

![Detalles del host](./media/hdinsight-hadoop-manage-ambari/host-details.png)

###<a name="services"></a>Servicios

La barra lateral de **Servicios** en el panel proporciona información rápida sobre el estado de los servicios que se ejecutan en el clúster. Se usan varios iconos para indicar el estado o las acciones que deben realizarse, como un símbolo de reciclaje amarillo si un servicio debe ser reciclados.

![barra lateral de servicios](./media/hdinsight-hadoop-manage-ambari/service-bar.png)

Seleccionar un servicio mostrará información más detallada sobre el servicio.

![información de resumen de servicio](./media/hdinsight-hadoop-manage-ambari/service-details.png)

####<a name="quick-links"></a>Vínculos rápidos

Algunos servicios mostrarán un vínculo de **Vínculos rápidos** en la parte superior de la página. Esto puede utilizarse para tener acceso específico del servicio web interfaces de usuario, como por ejemplo:

* **Historial de trabajos** : MapReduce historial de trabajos.

* **Administrador de recursos** : hilo ResourceManager UI.

* **NameNode** - distribuido Hadoop IU de NameNode (HDFS) del sistema de archivos.

* **Interfaz de usuario de Web Oozie** - Oozie interfaz de usuario.

Selección de cualquiera de estos vínculos se abrirá una nueva pestaña en el explorador, que muestra la página seleccionada.

> [AZURE.NOTE] Seleccionar un vínculo de **Vínculos rápidos** para cualquier servicio producirá un error "el servidor no encontrado" a menos que está usando un túnel de capa de Sockets seguros (SSL) para el tráfico de proxy web en el clúster. Esto es porque las aplicaciones web que se usa para mostrar esta información no se exponen en internet.
>
> Para obtener información sobre el uso de un túnel SSL con HDInsight, consulte [Usar SSH túnel para tener acceso a la interfaz de usuario de web Ambari, ResourceManager, JobHistory, NameNode, Oozie y otra de la interfaz de usuario de web](hdinsight-linux-ambari-ssh-tunnel.md)

##<a name="management"></a>Administración

###<a name="ambari-users-groups-and-permissions"></a>Permisos, grupos y usuarios de Ambari

Administrar usuarios, grupos y permisos no se utiliza con clústeres HDInsight.

###<a name="hosts"></a>Hosts

La página **Hosts** enumera todos los hosts del clúster. Para administrar hosts, siga estos pasos.

![página hosts](./media/hdinsight-hadoop-manage-ambari/hosts.png)

> [AZURE.NOTE] No se recomienda agregar, para retirar o recommissioning un host con HDInsight clústeres.

1. Seleccione el o los hosts que desea administrar.

2. Utilice el menú **acciones** para seleccionar la acción que desea realizar:

    * **Iniciar todos los componentes** - iniciar todos los componentes en el host.

    * **Detener todos los componentes** : detener todos los componentes en el host.

    * **Reinicie todos los componentes** : detener y comenzar a todos los componentes en el host.

    * **Activar el modo de mantenimiento** - Suprimir alertas para el host. Debe habilitarse si va a realizar acciones que generarán alertas, como reiniciar un servicio que dependen de servicios en ejecución.

    * **Desactivar el modo de mantenimiento** - devuelve el host de alertas normal.

    * **Detener** - puntos DataNode o NodeManagers en el host.

    * **Iniciar** - inicia DataNode o NodeManagers en el host.

    * **Reinicie** - detiene e inicia DataNode o NodeManagers en el host.

    * **Desactivar** - quita un host de clúster.

        > [AZURE.NOTE] No use esta acción en clústeres HDInsight.

    * **Recommission** - agrega un host retirado previamente en el clúster.

        > [AZURE.NOTE] No use esta acción en clústeres HDInsight.

###<a id="service"></a>Servicios

Desde la página de **panel** o **Servicios** , use el botón **acciones de** la parte inferior de la lista de servicios para detener e iniciar todos los servicios.

![acciones de servicio](./media/hdinsight-hadoop-manage-ambari/service-actions.png)

> [AZURE.WARNING] Mientras __Agregar servicio__ se muestran en este menú, no se recomienda agregar servicios al clúster HDInsight. Nuevos servicios deben agregarse mediante una acción de secuencia de comandos durante el aprovisionamiento de clúster. Para obtener más información sobre el uso de las acciones de secuencias de comandos, consulte [clústeres de personalizar HDInsight usar acciones de Script](hdinsight-hadoop-customize-cluster-linux.md).


Mientras el botón **acciones** puede reiniciar todos los servicios, con qué frecuencia desea iniciar, detener o reiniciar un servicio específico. Para realizar acciones en un servicio individual, realice los siguientes pasos:

1. Desde la página de **panel** o **Servicios** , seleccione un servicio.

2. Desde la parte superior de la ficha **Resumen** , use el botón **Acciones de servicio** y seleccione la acción que se ejecuta. Esto reinicie el servicio en todos los nodos.

    ![acción de servicio](./media/hdinsight-hadoop-manage-ambari/individual-service-actions.png)

    > [AZURE.NOTE] Reiniciar algunos servicios mientras se está ejecutando el clúster puede generar alertas. Para evitar esto, puede usar el botón **Acciones de servicio** para habilitar el **modo de mantenimiento** del servicio antes de realizar el reinicio.

3. Una vez que se ha seleccionado una acción, se incrementará la entrada de **op #** en la parte superior de la página para mostrar que se produce una operación en segundo plano. Si ha configurado para mostrar, se muestra la lista de operaciones en segundo plano.

    > [AZURE.NOTE] Si habilita **el modo de mantenimiento** del servicio, no olvide deshabilitar mediante el botón de **Acciones de servicio** finaliza la operación.

Para configurar un servicio, siga estos pasos:

1. Desde la página de **panel** o **Servicios** , seleccione un servicio.

2. Seleccione la ficha de **configuraciones** . Se mostrará la configuración actual. También se muestra una lista de las configuraciones anteriores.

    ![configuraciones](./media/hdinsight-hadoop-manage-ambari/service-configs.png)

3. Use los campos mostrados para modificar la configuración y, a continuación, seleccione **Guardar**. O seleccione una configuración anterior y, a continuación, seleccione **convertir en actual** para volver a la configuración anterior.

##<a name="ambari-views"></a>Vistas Ambari

Ambari vistas permiten a los desarrolladores Enchufe los elementos de interfaz de usuario en la interfaz de usuario de Web Ambari mediante el [Marco de vistas Ambari](https://cwiki.apache.org/confluence/display/AMBARI/Views). HDInsight proporciona las siguientes vistas con tipos de Hadoop de clúster:

* Administrador de cola de hilo: el Administrador de cola proporciona una interfaz de usuario simple para ver y modificar colas hilo.
* Vista de sección: La vista de sección permite ejecutar consultas de la sección directamente desde el explorador web. Puede guardar las consultas, ver los resultados, guardar los resultados en el almacenamiento de clúster o descargar resultados a su sistema local. Para obtener más información sobre el uso de vistas de sección, vea [Usar vistas subárbol con HDInsight](hdinsight-hadoop-use-hive-ambari-view.md).
* Vista de Tez: La vista de Tez permite a comprender mejor y optimizar trabajos al ver información sobre cómo se ejecutan los trabajos de Tez y los recursos que se usan por el trabajo.
