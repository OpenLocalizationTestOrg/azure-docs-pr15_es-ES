<properties
   pageTitle="Implementar y administrar topologías tormenta Apache en HDInsight | Microsoft Azure"
   description="Obtenga información sobre cómo implementar, supervisar y administrar topologías Apache tormenta con el panel de tormenta en HDInsight. Usar Hadoop tools para Visual Studio."
   services="hdinsight"
   documentationCenter=""
   authors="Blackmist"
   manager="jhubbard"
   editor="cgronlun"
    tags="azure-portal"/>

<tags
   ms.service="hdinsight"
   ms.devlang="java"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="10/11/2016"
   ms.author="larryfr"/>

#<a name="deploy-and-manage-apache-storm-topologies-on-windows-based-hdinsight"></a>Implementar y administrar topologías tormenta Apache en HDInsight de Windows

El panel de tormenta le permite implementar fácilmente y ejecutar topologías Apache tormenta al clúster HDInsight mediante el explorador web. También puede usar el panel para supervisar y administrar topologías ejecución. Si utiliza Visual Studio, las herramientas de HDInsight para Visual Studio proporcionan características similares en Visual Studio.

El panel de tormenta y las características de tormenta en las herramientas de HDInsight dependen de la API de REST de tormenta, que se puede utilizar para crear su propia supervisión y soluciones de administración.

> [AZURE.IMPORTANT] Los pasos de este documento requieren una tormenta basado en Windows en clúster HDInsight. Para obtener información sobre el uso de un clúster de Linux, consulte [implementar y administrar topologías tormenta Apache en HDInsight de Linux](hdinsight-storm-deploy-monitor-topology-linux.md)

##<a name="prerequisites"></a>Requisitos previos

* **Tormenta Apache en HDInsight** - consulte <a href="../hdinsight-storm-getting-started/" target="_blank">Introducción a tormenta Apache en HDInsight</a> para conocer los pasos sobre cómo crear un clúster

* Para el **Panel de tormenta**: un explorador web moderna compatible con HTML5

* Para **Visual Studio** - Azure SDK 2.5.1 o posterior y las herramientas de HDInsight para Visual Studio. Vea <a href="../hdinsight-hadoop-visual-studio-tools-get-started/" target="_blank">Introducción al uso HDInsight Tools para Visual Studio</a> para instalar y configurar las herramientas de HDInsight para Visual Studio.

    Una de las siguientes versiones de Visual Studio:

    * Visual Studio 2012 con <a href="http://www.microsoft.com/download/details.aspx?id=39305" target="_blank">4 de actualización</a>

    * 2013 de Visual Studio con la <a href="http://go.microsoft.com/fwlink/?LinkId=517284" target="_blank">Comunidad Visual Studio 2013</a> o <a href="http://www.microsoft.com/download/details.aspx?id=44921" target="_blank">Actualizar 4</a>

    * <a href="http://visualstudio.com/downloads/visual-studio-2015-ctp-vs" target="_blank">CTP6 Visual Studio de 2015</a>

    > [AZURE.NOTE] Las herramientas de HDInsight para Visual Studio solo admite actualmente tormenta en la versión de clúster HDInsight 3.2.

##<a name="storm-dashboard"></a>Panel de tormenta

El panel de tormenta es una página web disponibles en el clúster de tormenta. La dirección URL es **https://&lt;nombreDeClúster >.azurehdinsight.net/**, donde **nombreDeClúster** es el nombre de su tormenta en clúster HDInsight.

Desde la parte superior del panel de tormenta, seleccione **Enviar topología**. Siga las instrucciones de la página para ejecutar una topología de ejemplo, o para cargar y ejecutar una topología que ha creado.

![la página de la topología de enviar][storm-dashboard-submit]

###<a name="storm-ui"></a>Interfaz de usuario de tormenta

Desde el panel de tormenta, seleccione el vínculo de la **Interfaz de usuario de tormenta** . Esta opción mostrará información acerca del clúster, además de cualquier ejecución topologías.

![la interfaz de usuario de tormenta][storm-dashboard-ui]

> [AZURE.NOTE] En algunas versiones de Internet Explorer, es posible que descubra que no actualizará la interfaz de usuario de tormenta después de que ha visitado en primer lugar. Por ejemplo, no puede mostrar las topologías nuevas que ha enviado o puede mostrar una topología como activa cuando se desactivado anteriormente. Microsoft tiene constancia de este problema y está trabajando en una solución.

####<a name="main-page"></a>Página principal

La página principal de la interfaz de usuario de tormenta proporciona la información siguiente:

* **Resumen de clúster**: información básica sobre el clúster de tormenta.

* **Topología de resumen**: una lista de ejecución topologías. Use los vínculos de esta sección para obtener más información sobre topologías específicas.

* **Supervisor resumen**: información sobre el supervisor tormenta.

* **Configuración de Nimbus**: configuración de Nimbus para el clúster.

####<a name="topology-summary"></a>Resumen de topología

Seleccionar un vínculo en la sección de **Resumen de topología** muestra la siguiente información acerca de la topología:

* **Topología de resumen**: información básica sobre la topología.

* **Acciones de topología**: acciones que puede realizar para la topología.

    * **Activar**: procesamiento de currículos de topología desactivada.

    * **Desactivar**: pausa una topología de ejecución.

    * **Equilibrar**: ajusta el paralelismo de la topología. Debe equilibrar topologías ejecución después de haber cambiado el número de nodos en el clúster. Esto permite la topología para ajustar paralelismo de compensación para el aumentar o reducir el número de nodos en el clúster.

        Para obtener más información, vea <a href="http://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html" target="_blank">comprender el paralelismo de una topología de tormenta</a>.

    * **Eliminar**: finaliza una topología de tormenta después el tiempo de espera.

* **Estadísticas de topología**: estadísticas acerca de la topología. Use los vínculos en la columna de la **ventana** para establecer el intervalo de tiempo para las demás entradas en la página.

* **Spouts**: el spouts usados por la topología. Use los vínculos de esta sección para obtener más información sobre spouts específicas.

* **Pernos**: los pernos de la topología. Use los vínculos de esta sección para obtener más información acerca de pernos específicos.

* **Configuración de la topología**: la configuración de la topología seleccionada.

####<a name="spout-and-bolt-summary"></a>Pitorro y resumen de rayo

Seleccionar un pitorro de las secciones **Spouts** o **pernos** muestra la información siguiente sobre el elemento seleccionado:

* **Resumen de componentes**: información básica sobre el pitorro o los pernos.

* **Estadísticas de pitorro/rayo**: estadísticas acerca del pitorro o rayo. Use los vínculos en la columna de la **ventana** para establecer el intervalo de tiempo para las demás entradas en la página.

* **Estadísticas de entrada** (solo de pernos): información sobre las secuencias de entrada consumido por el rayo.

* **Estadísticas de resultados**: información sobre las secuencias emitido por esto chorros o pernos.

* **Elementos de ejecución**: información sobre las instancias del pitorro o rayo. Seleccione la entrada de **puerto** para un elemento de ejecución específico ver un registro de información de diagnóstico fabrican para esta instancia.

* **Errores**: cualquier información de error para este chorros o pernos.

##<a name="hdinsight-tools-for-visual-studio"></a>HDInsight Tools para Visual Studio

Las herramientas de HDInsight puede utilizarse para enviar topologías C# o híbrido en el clúster de tormenta. Los pasos siguientes use una aplicación de ejemplo. Para obtener información acerca de cómo crear sus propio topologías mediante las herramientas de HDInsight, consulte [desarrollar C# topologías con las herramientas de HDInsight para Visual Studio](hdinsight-storm-develop-csharp-visual-studio-topology.md).

Use los siguientes pasos para implementar una muestra a su tormenta en clúster de HDInsight, a continuación, ver y administrar la topología.

1. Si no ha instalado la versión más reciente de las herramientas de HDInsight para Visual Studio, vea <a href="../hdinsight-hadoop-visual-studio-tools-get-started/" target="_blank">Introducción al uso HDInsight Tools para Visual Studio</a>.

2. Abra Visual Studio, seleccione **archivo** > **nuevo** > **proyecto**.

3. En el cuadro de diálogo **Nuevo proyecto** , expanda **instalados** > **plantillas**y, a continuación, seleccione **HDInsight**. En la lista de plantillas, seleccione **Tormenta de ejemplo**. En la parte inferior del cuadro de diálogo, escriba un nombre para la aplicación.

    ![imagen](./media/hdinsight-storm-deploy-monitor-topology/sample.png)

1. En el **Explorador de soluciones**, haga clic en el proyecto y haga **clic en Enviar a tormenta en HDInsight**.

    > [AZURE.NOTE] Si se le solicita, escriba las credenciales de inicio de sesión para su suscripción de Azure. Si tiene más de una suscripción, inicie sesión en la tabla que contiene la tormenta en clúster HDInsight.

2. Seleccione su tormenta en clúster de HDInsight de la lista desplegable de **Tormenta clúster** y, a continuación, haga **clic en enviar**. Puede supervisar si la presentación es correcta mediante la ventana de **resultados** .

3. Cuando la topología se ha enviado correctamente, debe aparecer la **Tormenta topologías** para el clúster. Seleccione la topología de la lista para ver información sobre la topología de ejecución.

    ![monitor de Visual studio](./media/hdinsight-storm-deploy-monitor-topology/vsmonitor.png)

    > [AZURE.NOTE] También puede ver **Tormenta topologías** del **Explorador** de servidores, expanda **Azure** > **HDInsight**y, a continuación, haga clic con el botón una tormenta en clúster HDInsight y seleccionando **Vista tormenta topologías**.

    Seleccione la forma de la spouts o esenciales para ver información sobre estos componentes. Se abrirá una ventana nueva para cada elemento seleccionado.
    
    > [AZURE.NOTE] El nombre de la topología es el nombre de la topología de clase (en este caso, `HelloWord`,) con una marca de tiempo que se anexa.

4. Desde la vista de **Resumen de la topología** , seleccione **Eliminar** para detener la topología.

    > [AZURE.NOTE] Topologías de tormenta seguir ejecutando hasta que se detienen o se elimina el clúster.

##<a name="rest-api"></a>API DE REST

La interfaz de usuario de tormenta se basa en la API de REST, para que pueda realizar administración y supervisión funcionalidad mediante la API de REST similares. Puede usar la API de REST para crear herramientas personalizadas para la administración y supervisión topologías tormenta.

Para obtener más información, vea [API de REST de tormenta de interfaz de usuario](https://github.com/apache/storm/blob/0.9.3-branch/STORM-UI-REST-API.md). La siguiente información es específica del uso de la API de REST con Apache tormenta en HDInsight.

###<a name="base-uri"></a>URI básico

Es el URI base de la API de REST en clústeres HDInsight **https://&lt;nombreDeClúster >.azurehdinsight.net/stormui/api/v1/**, donde **nombreDeClúster** es el nombre de su tormenta en clúster HDInsight.

###<a name="authentication"></a>Autenticación

Las solicitudes de la API de REST deben utilizar **autenticación básica**, para usar el nombre del Administrador de clúster de HDInsight y la contraseña.

> [AZURE.NOTE] Porque la autenticación básica se envía con texto no cifrado, debe usar **siempre** HTTPS para proteger las comunicaciones con el clúster.

###<a name="return-values"></a>Devolver valores

Información que se devuelve de la API de REST sólo puede ser utilizable desde dentro del clúster o máquinas virtuales de Windows en la misma red Virtual de Azure como el clúster. Por ejemplo, el nombre de dominio completo (FQDN) devuelto para servidores Zookeeper no será accesible desde Internet.

##<a name="next-steps"></a>Pasos siguientes

Ahora que ha aprendido a implementar y supervisar topologías mediante el panel de tormenta, obtenga información sobre cómo:

* [Desarrollar C# topologías con las herramientas de HDInsight para Visual Studio](hdinsight-storm-develop-csharp-visual-studio-topology.md)

* [Desarrollar topologías basada en Java con experto](hdinsight-storm-develop-java-topology.md)

Para obtener una lista de más topologías de ejemplo, vea [topologías de ejemplo para tormenta en HDInsight](hdinsight-storm-example-topology.md).

[hdinsight-dashboard]: ./media/hdinsight-storm-deploy-monitor-topology/dashboard-link.png
[storm-dashboard-submit]: ./media/hdinsight-storm-deploy-monitor-topology/submit.png
[storm-dashboard-ui]: ./media/hdinsight-storm-deploy-monitor-topology/storm-ui-summary.png
