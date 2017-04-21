<properties
   pageTitle="Procesar eventos de evento Hubs con tormenta en HDInsight | Microsoft Azure"
   description="Aprenda a procesar datos de evento Hubs con una topología de tormenta C# creada en Visual Studio utilizando el HDInsight Tools para Visual Studio."
   services="hdinsight,notification hubs"
   documentationCenter=""
   authors="Blackmist"
   manager="jhubbard"
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="10/27/2016"
   ms.author="larryfr"/>

# <a name="process-events-from-azure-event-hubs-with-storm-on-hdinsight-c"></a>Procesar los eventos de Azure evento Hubs con tormenta en HDInsight (C#)

Azure Hubs evento permite procesar grandes cantidades de datos de sitios Web, aplicaciones y dispositivos. Evento Hubs pitorro hace fácil de usar tormenta Apache en HDInsight para analizar datos en tiempo real. También puede escribir datos en Hubs de evento de tormenta mediante el rayo Hubs de evento.

En este tutorial, aprenderá cómo usar las plantillas de Visual Studio instaladas con HDInsight Tools para Visual Studio para crear dos topologías que funcionan con Azure evento Hubs.

* **EventHubWriter**: genera datos aleatoriamente y escribe en Hubs de evento

* **EventHubReader**: lee los datos de evento Hubs y registra los datos en los registros de tormenta

> [AZURE.NOTE] Mientras los pasos de este documento se basan en un entorno de desarrollo de Windows con Visual Studio, el proyecto compilado puede enviarse a clúster Linux o de HDInsight de Windows. Solo clústeres basados en Linux crean después de soporte técnico de 28/10/2016 SCP.NET topologías.
>
> Para usar una topología de C# con un clúster basado en Linux, deberá actualizar el paquete Microsoft.SCP.Net.SDK NuGet utilizado por su proyecto a la versión 0.10.0.6 o superior. La versión del paquete también debe coincidir con la versión principal de tormenta instalado en HDInsight. Por ejemplo, tormenta en HDInsight versiones 3.3 y 3.4 utilizar la versión de tormenta 0.10.x, mientras que HDInsight 3.5 utiliza tormenta 1.0. x.
> 
> C# topologías en clústeres basados en Linux deben utilizar .NET 4.5 y usar Mono para ejecutar en el clúster HDInsight. La mayoría de elementos funcionará, pero debe comprobar el documento [Mono compatibilidad](http://www.mono-project.com/docs/about-mono/compatibility/) para posible incompatibilidad.
>
> Para obtener una versión de Java de este proyecto, que también funcionarán en un clúster basado en Windows o Linux, vea [eventos del proceso de Azure evento Hubs con tormenta en HDInsight (Java)](hdinsight-storm-develop-java-event-hub-topology.md).

## <a name="prerequisites"></a>Requisitos previos

* Una [Tormenta Apache en clúster HDInsight](hdinsight-apache-storm-tutorial-get-started.md)

* Un [concentrador de evento de Azure](../event-hubs/event-hubs-csharp-ephcs-getstarted.md)

* El [SDK de Azure .NET](http://azure.microsoft.com/downloads/)

* Las [Herramientas de HDInsight para Visual Studio](hdinsight-hadoop-visual-studio-tools-get-started.md)

## <a name="completed-project"></a>Proyecto completo

Puede descargar una versión completa del proyecto creado en este tutorial de GitHub: [eventhub híbrida de tormenta](https://github.com/Azure-Samples/hdinsight-dotnet-java-storm-eventhub). Sin embargo, todavía debe proporcionar configuración siguiendo los pasos de este tutorial.

## <a name="event-hubs-spout-and-bolt"></a>Rayo y pitorro Hubs de evento

Los centros de evento chorros y perno son componentes de Java que le permiten trabajar fácilmente con evento Hubs de tormenta Apache. Aunque estos componentes están escritos en Java, las herramientas de HDInsight para Visual Studio permite crear topologías híbridas que mezclar componentes de Java y C#.

Los pitorro y pernos están distribuidos como un único archivo de almacenamiento (.jar) de Java denominado **eventhubs-storm-spout-#.#-jar-with-dependencies.jar**, donde #. # es la versión del archivo.

### <a name="download-the-jar-file"></a>Descargar el archivo .jar

La versión más reciente del archivo jar se incluye en el proyecto [ejemplos de tormenta HDInsight](https://github.com/hdinsight/hdinsight-storm-examples) en la carpeta de **biblioteca/eventhubs** . Para descargar el archivo, use uno de los métodos siguientes.

> [AZURE.NOTE] La pitorro y pernos se han enviado para su inclusión en el proyecto de tormenta Apache. Para obtener más información, vea [TORMENTA 583: inicial de protección para hubs tormenta evento](https://github.com/apache/storm/pull/336/files) en GitHub.

* **Descargar un archivo ZIP**: desde el sitio de [tormenta HDInsight ejemplos](https://github.com/hdinsight/hdinsight-storm-examples) , seleccione **Descargar ZIP** en el panel derecho para descargar un archivo .zip que contiene el proyecto.

    ![botón de descarga zip](./media/hdinsight-storm-develop-csharp-event-hub-topology/download.png)

    Después de descargar el archivo, puede extraer el archivo y el archivo estará en el directorio de la **biblioteca** .

* **Clonar el proyecto**: si tiene [Git](http://git-scm.com/) instalado, use el comando siguiente para clonar el repositorio localmente y luego busque el archivo en el directorio de la **biblioteca** .

        git clone https://github.com/hdinsight/hdinsight-storm-examples

## <a name="configure-event-hubs"></a>Configurar Hubs de evento

Evento Hubs es el origen de datos en este ejemplo. Use la información en la sección __crear un concentrador de eventos__ del documento [Introducción con Hubs de evento](../event-hubs/event-hubs-csharp-ephcs-getstarted.md) .

3. Una vez creado el hub de evento, ver el módulo EventHub en el Portal de Azure y seleccione __compartido acceso directivas__. Utilice la entrada __+ Agregar__ para agregar las siguientes directivas:

  	| Nombre | Permisos |
  	| ----- | ----- |
  	| escritor | Enviar |
  	| lector | Escuchar |

    ![directivas de](./media/hdinsight-storm-develop-csharp-event-hub-topology/sas.png)

5. Seleccione las directivas de __lector__ y __escritor__ . Copie y guarde el valor de __Clave principal__ para ambas directivas, tal como se utilizarán más adelante.

## <a name="configure-the-eventhubwriter"></a>Configurar la EventHubWriter

1. Si no ha instalado la versión más reciente de las herramientas de HDInsight para Visual Studio, vea [Introducción al uso HDInsight Tools para Visual Studio](hdinsight-hadoop-visual-studio-tools-get-started.md).

2. Descargue la solución de [eventhub híbrida de tormenta](https://github.com/Azure-Samples/hdinsight-dotnet-java-storm-eventhub). Abra la solución y tarde un poco para examinar el código para el proyecto __EventHubWriter__ .

4. En el proyecto __EventHubWriter__ , abra el archivo __App.config__ . Use la información del concentrador de evento configurado previamente para rellenar el valor de las siguientes claves:

  	| Clave | Valor |
  	| ----- | ----- |
  	| EventHubPolicyName | escritor (si se usa un nombre diferente para la directiva de permisos _Enviar_ , use en su lugar.) |
  	| EventHubPolicyKey | La clave para la directiva de escritura |
  	| EventHubNamespace | El espacio de nombres que contiene su centro de evento |
  	| EventHubName | El nombre del concentrador de evento |
  	| EventHubPartitionCount | El número de particiones en el Hub de evento |

4. Guarde y cierre el archivo **App.config** .

## <a name="configure-the-eventhubreader"></a>Configurar la EventHubReader

1. Abra el proyecto __EventHubReader__ y tomar unos momoents para examinar el código.

2. Abra __App.config__ para el __EventHubWriter__. Use la información del concentrador de evento configurado previamente para rellenar el valor de las siguientes claves:

  	| Clave | Valor |
  	| ----- | ----- |
  	| EventHubPolicyName | lector (si se usa un nombre diferente para la directiva de permisos _escuchar_ , use en su lugar.) |
  	| EventHubPolicyKey | La clave para la directiva de lectura |
  	| EventHubNamespace | El espacio de nombres que contiene su centro de evento |
  	| EventHubName | El nombre del concentrador de evento |
  	| EventHubPartitionCount | El número de particiones en el Hub de evento |

3. Guarde y cierre el archivo **App.config** .

## <a name="deploy-the-topologies"></a>Implementar las topologías

1. Desde el **Explorador de soluciones**, haga clic en el proyecto **EventHubReader** y seleccione **Enviar a tormenta en HDInsight**.

    ![Enviar a tormenta](./media/hdinsight-storm-develop-csharp-event-hub-topology/submittostorm.png)

2. En la pantalla de la **Topología de envío** , seleccione el **Clúster de tormenta**. Expandir **Configuraciones adicionales**, seleccione **Java rutas de archivo**, seleccione **...** y seleccione el directorio que contiene el archivo **eventhubs-storm-spout-0.9-jar-with-dependencies.jar** que ha descargado anteriormente. Por último, haga clic en **Enviar**.

    ![Imagen del cuadro de diálogo de envío](./media/hdinsight-storm-develop-csharp-event-hub-topology/submit.png)

3. Cuando se ha enviado la topología, aparecerá el **Visor de tormenta topologías** . Seleccione la topología de **EventHubReader** en el panel izquierdo para ver estadísticas de la topología. Actualmente, no debería nada porque no eventos escritos en el evento Hubs todavía.

    ![ejemplo de vista de almacenamiento](./media/hdinsight-storm-develop-csharp-event-hub-topology/topologyviewer.png)

4. Desde el **Explorador de soluciones**, haga clic en el proyecto **EventHubWriter** y seleccione **Enviar a tormenta en HDInsight**.

2. En la pantalla de la **Topología de envío** , seleccione el **Clúster de tormenta**. Expandir **Configuraciones adicionales**, seleccione **Java rutas de archivo**, seleccione **...** y seleccione el directorio que contiene el archivo **eventhubs-storm-spout-0.9-jar-with-dependencies.jar** que ha descargado anteriormente. Por último, haga clic en **Enviar**.

5. Cuando se ha enviado la topología, actualice la lista de topología en el **Visor de tormenta topologías** para comprobar que ambas topologías se ejecutan en el clúster.

6. En el **Visor de tormenta topologías**, seleccione la topología de **EventHubReader** .

4. En la vista de gráfico, haga doble clic en el componente __LogBolt__ . Se abrirá la página de __Resumen de componentes__ para el rayo.

3. En la sección __elementos de ejecución__ , seleccione uno de los vínculos en la columna __puerto__ . Esta opción mostrará información registrada por el componente. La información registrada es similar a la siguiente:

        2016-10-20 13:26:44.186 m.s.s.b.ScpNetBolt [INFO] Processing tuple: source: com.microsoft.eventhubs.spout.EventHubSpout:7, stream: default, id: {5769732396213255808=520853934697489134}, [{"deviceId":3,"deviceValue":1379915540}]
        2016-10-20 13:26:44.234 m.s.s.b.ScpNetBolt [INFO] Processing tuple: source: com.microsoft.eventhubs.spout.EventHubSpout:7, stream: default, id: {7154038361491319965=4543766486572976404}, [{"deviceId":3,"deviceValue":459399321}]
        2016-10-20 13:26:44.335 m.s.s.b.ScpNetBolt [INFO] Processing tuple: source: com.microsoft.eventhubs.spout.EventHubSpout:6, stream: default, id: {513308780877039680=-7571211415704099042}, [{"deviceId":5,"deviceValue":845561159}]
        2016-10-20 13:26:44.445 m.s.s.b.ScpNetBolt [INFO] Processing tuple: source: com.microsoft.eventhubs.spout.EventHubSpout:7, stream: default, id: {-2409895457033895206=5479027861202203517}, [{"deviceId":8,"deviceValue":2105860655}]

## <a name="stop-the-topologies"></a>Detener las topologías

Para detener la topologías, seleccione cada topología en el **Visor de topología de tormenta**y luego haga clic en **Eliminar**.

![imagen de una topología de sacrificio](./media/hdinsight-storm-develop-csharp-event-hub-topology/killtopology.png)

## <a name="delete-your-cluster"></a>Eliminar el clúster

[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="notes"></a>Notas

### <a name="checkpointing"></a>Puntos de control

La EventHubSpout periódicamente puntos de comprobación de su estado en el nodo Zookeeper, que guarda el desplazamiento actual para mensajes lee de la cola. Esto permite que el componente empezar a recibir mensajes en el desplazamiento de guardado en las situaciones siguientes:

* La instancia de componente falla y se reinicia.

* Aumentar o reducir el clúster agregando o quitando los nodos.

* La topología se elimina y reinicia **con el mismo nombre**.

También puede exportar e importar los puntos de control almacenados en WASB (el almacenamiento de Azure utiliza el clúster HDInsight.) Las secuencias de comandos para realizar esta acción se encuentran en la tormenta en clúster HDInsight, en **c:\apps\dist\storm-0.9.3.2.2.1.0-2340\zkdatatool-1.0\bin**.

>[AZURE.NOTE] El número de versión de la ruta puede ser diferente, como la versión de tormenta instalado en el clúster pueden cambiar en el futuro.

Las secuencias de comandos en este directorio son:

* **stormmeta_import.cmd**: importar todos los metadatos de tormenta desde el contenedor de almacenamiento de clúster de forma predeterminada en Zookeeper.

* **stormmeta_export.cmd**: exportar todos los metadatos de tormenta de Zookeeper al contenedor de almacenamiento de clúster predeterminado.

* **stormmeta_delete.cmd**: eliminar todos los metadatos de tormenta de Zookeeper.

Exportar que una importación le permite conservar los datos de control cuando necesite eliminar el clúster, pero desea reanudar el procesamiento de desplazamiento actual en el hub de cuando se coloca un clúster de nuevo en línea.

> [AZURE.NOTE] Puesto que los datos se conservan en el contenedor de almacenamiento de forma predeterminada, el clúster nuevo **debe** usar la misma cuenta de almacenamiento y el contenedor, como el clúster anterior.

## <a name="next-steps"></a>Pasos siguientes

En este documento, ha aprendido a usar el rayo y Java evento Hubs chorros de una topología de C# para trabajar con datos en el Hub de evento de Azure. Para obtener más información sobre cómo crear topologías C#, consulte lo siguiente.

* [Desarrollar C# topologías para tormenta Apache en HDInsight con Visual Studio](hdinsight-storm-develop-csharp-visual-studio-topology.md)

* [Guía de programación de SCP](hdinsight-storm-scp-programming-guide.md)

* [Topologías de ejemplo para tormenta en HDInsight](hdinsight-storm-example-topology.md)
 
