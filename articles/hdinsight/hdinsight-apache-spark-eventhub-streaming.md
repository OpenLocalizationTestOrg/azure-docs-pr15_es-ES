<properties 
    pageTitle="Usar Azure evento Hubs con motor Apache en HDInsight para procesar datos streaming | Microsoft Azure" 
    description="Instrucciones paso a paso sobre cómo enviar datos transmita a Azure concentrador de evento y, a continuación, reciban esos eventos en motor con una aplicación scala" 
    services="hdinsight" 
    documentationCenter="" 
    authors="nitinme" 
    manager="jhubbard" 
    editor="cgronlun"
    tags="azure-portal"/>

<tags 
    ms.service="hdinsight" 
    ms.workload="big-data" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/30/2016" 
    ms.author="nitinme"/>


# <a name="spark-streaming-process-events-from-azure-event-hubs-with-apache-spark-cluster-on-hdinsight-linux"></a>Motor de flujo de datos: Procesar los eventos de Azure evento Hubs con Apache motor clúster en HDInsight Linux

Transmisión motor extiende la API de motor para generar aplicaciones de procesamiento de secuencia scalable, de alto rendimiento, tolerancia principal. Pueden ser ingeridos datos desde varios orígenes. En este artículo usamos Hubs de evento de Azure para recopilar datos. Evento Hubs es un sistema de recopilación altamente scalable esa entrada can millones de eventos por segundo. 

En este tutorial, aprenderá a crear un concentrador de evento Azure, cómo la recopilación de mensajes a un concentrador de eventos con una aplicación de consola en Java y recuperarlas en paralelo con una aplicación de motor escrita en Scala. Esta aplicación consume los datos que se transmite a través de evento Hubs y redirige a diferentes salidas (Azure almacenamiento de blobs, tabla subárbol y tabla SQL).

> [AZURE.NOTE] Para seguir las instrucciones de este artículo, debe usar ambas versiones del portal de Azure. Para crear un concentrador de evento usará el [portal de Azure clásico](https://manage.windowsazure.com). Para trabajar con el clúster HDInsight Spark, usará el [Portal de Azure](https://portal.azure.com/).  

**Requisitos previos:**

Debe tener el siguiente:

- Una suscripción de Azure. Vea la [versión de prueba gratuita de Azure obtener](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
- Un clúster de motor de Apache. Para obtener instrucciones, vea [crear Apache motor clústeres en HDInsight de Azure](hdinsight-apache-spark-jupyter-spark-sql.md).
- Kit de desarrollo de Java de Oracle. Puede instalarlo desde [aquí](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html).
- Un Java IDE. En este artículo, se utiliza IntelliJ IDEA 15.0.1. Puede instalarlo desde [aquí](https://www.jetbrains.com/idea/download/).
- Controlador de JDBC de Microsoft para SQL Server, v4.1 o una versión posterior. Esto es necesario para escribir los datos del evento en una base de datos de SQL Server. Puede instalarlo desde [aquí](https://msdn.microsoft.com/sqlserver/aa937724.aspx).
- Una base de datos de SQL Azure. Para obtener instrucciones, vea [crear una base de datos SQL en minutos](../sql-database/sql-database-get-started.md).

## <a name="what-does-this-solution-do"></a>¿Qué hace esta solución?

Este es el flujo de la solución streaming:

1. Crear un concentrador de evento de Azure que recibirá una secuencia de eventos.

2. Ejecutar una aplicación independiente local que genera eventos e inserta el concentrador de eventos de Azure. La aplicación de ejemplo que hace esto se publica en [https://github.com/hdinsight/spark-streaming-data-persistence-examples](https://github.com/hdinsight/spark-streaming-data-persistence-examples).

2. Ejecutar una aplicación de transmisión de forma remota en un clúster de motor que lee eventos transmisiones de concentrador de evento de Azure y lo inserta en diferentes ubicaciones (blobs de Windows Azure, tabla subárbol y tabla de base de datos SQL). 

## <a name="create-azure-event-hub"></a>Crear concentrador de evento de Azure

1. En el [Portal de Azure](https://manage.windowsazure.com), seleccione **nuevo** > **Bus de servicio** > **Concentrador de evento** > **Crear personalizado**.

2. En la pantalla **Agregar un nuevo evento de concentrador** , escriba un **Nombre de concentrador de evento**, seleccione la **región** para crear el concentrador y crear un nuevo espacio de nombres o seleccione uno existente. Haga clic en la **flecha** para continuar.

    ![página 1 del asistente] (./media/hdinsight-apache-spark-eventhub-streaming/hdispark.streaming.create.event.hub.png "Crear un concentrador de evento de Azure")

    > [AZURE.NOTE] Debe seleccionar la misma **ubicación** que el clúster Apache motor en HDInsight de reducir los costos y latencia.

3. En la pantalla **Configurar concentrador de evento** , escriba los valores de **recuento de partición** y **Retención de mensajes** y, a continuación, haga clic en la marca de verificación. En este ejemplo, use un recuento de partición de 10 y retención de mensaje de 1. Tenga en cuenta el número de partición porque lo necesitará más adelante en este valor.

    ![página 2 del asistente] (./media/hdinsight-apache-spark-eventhub-streaming/hdispark.streaming.create.event.hub2.png "Especificar partición tamaño y retención días de concentrador de evento")

4. Haga clic en el Hub de evento que ha creado, haga clic en **Configurar**y, a continuación, crear dos directivas de acceso para el concentrador de eventos.

    <table>
    <tr><th>Nombre</th><th>Permisos</th></tr>
    <tr><td>mysendpolicy</td><td>Enviar</td></tr>
    <tr><td>myreceivepolicy</td><td>Escuchar</td></tr>
    </table>

    Después de crear los permisos, seleccione el icono **Guardar** en la parte inferior de la página. Esto crea las directivas de acceso compartido que se usará para enviar (**mysendpolicy**) y escuchar este concentrador de evento (**myreceivepolicy**).

    ![directivas de] (./media/hdinsight-apache-spark-eventhub-streaming/hdispark.streaming.event.hub.policies.png "Directivas de crear concentrador de evento")

    
5. En la misma página, observe las claves de directiva generados para las directivas de dos. Guardar estas teclas porque se usará más adelante.

    ![claves de directiva] (./media/hdinsight-apache-spark-eventhub-streaming/hdispark.streaming.event.hub.policy.keys.png "Guardar las claves de directiva")

6. En la página de **panel** , haga clic en **Información de conexión** de la parte inferior para recuperar y guardar las cadenas de conexión para el concentrador de eventos con las directivas de dos.

    ![claves de directiva] (./media/hdinsight-apache-spark-eventhub-streaming/hdispark.streaming.event.hub.policy.connection.strings.png "Guardar las cadenas de conexión de directiva")

## <a name="use-a-scala-application-to-send-messages-to-event-hub"></a>Usar una aplicación Scala para enviar mensajes a concentrador de evento

En esta sección se utiliza una aplicación independiente de Scala local para enviar una secuencia de eventos a concentrador de evento de Azure que creó en el paso anterior. Esta aplicación está disponible en GitHub en [https://github.com/hdinsight/eventhubs-sample-event-producer](https://github.com/hdinsight/eventhubs-sample-event-producer). Los siguientes pasos se suponen que ya ha bifurcado este repositorio GitHub.

1. Abra la aplicación, **EventhubsSampleEventProducer**, en IntelliJ IDEA.
    
2. Genere el proyecto. En el menú **Generar** , haga clic en **Crear proyecto**. Jar de salida se crea en **\out\artifacts**.

>[AZURE.TIP] También puede usar una opción disponible en IntelliJ IDEA para crear un proyecto directamente desde un repositorio de GitHub. Para comprender cómo usar ese método, use las instrucciones de la siguiente sección para obtener instrucciones. Tenga en cuenta que no sea aplicable para la aplicación Scala que cree en este paso una gran cantidad de pasos que se describen en la siguiente sección. Por ejemplo:

> * No tendrá que actualizar la POM para incluir la versión de motor. Eso es porque no hay ninguna dependencia en el motor para la creación de esta aplicación
> * No tendrá que agregar algunas botes de dependencia a la biblioteca de proyectos. Eso es porque dichos botes no son necesarios para este proyecto.

## <a name="update-the-scala-streaming-application-for-receiving-the-events"></a>Actualizar la aplicación para recibir los eventos de transmisión de Scala

Una aplicación de Scala de ejemplo para recibir el evento y redirigir a diferentes destinos está disponible en [https://github.com/hdinsight/spark-streaming-data-persistence-examples](https://github.com/hdinsight/spark-streaming-data-persistence-examples). Siga los pasos siguientes para actualizar la aplicación y crear jar de salida.

1. Iniciar IntelliJ IDEA y en la pantalla de inicio, seleccione **Desproteger del Control de versiones** y, a continuación, haga clic en **Git**.
        
    ![Obtener orígenes de Git](./media/hdinsight-apache-spark-eventhub-streaming/get-source-from-git.png)

2. En el cuadro de diálogo **Clonar repositorio** , proporcione la dirección URL en el repositorio de Git clonar desde, especifique el directorio clonar a y, a continuación, haga clic en **clonar**.

    ![Clonar desde Git](./media/hdinsight-apache-spark-eventhub-streaming/clone-from-git.png)

    
3. Siga las indicaciones hasta que el proyecto es clonar completamente. Presione **Alt + 1** para abrir la **Vista del proyecto**. Debe ser similar al siguiente.

    ![Vista del proyecto](./media/hdinsight-apache-spark-eventhub-streaming/project-view.png)
    
4. Asegúrese de que el código de la aplicación se compila con Java8. Para asegurarse de esto, haga clic en **archivo**, haga clic en la **Estructura del proyecto**y en la pestaña **proyecto** , asegúrese de que el nivel de lenguaje de proyecto se establece en **8 - lambda, las anotaciones de tipo, por ejemplo**.

    ![Estructura de proyecto](./media/hdinsight-apache-spark-eventhub-streaming/java-8-compiler.png)

5. Abra la **pom.xml** y asegúrese de que la versión de motor es correcta. En <properties> nodo, busque el fragmento de código siguiente y comprobar la versión de motor.

        <scala.version>2.10.4</scala.version>
        <scala.compat.version>2.10.4</scala.compat.version>
        <scala.binary.version>2.10</scala.binary.version>
        <spark.version>1.6.2</spark.version>
    
5. La aplicación requiere dos botes de dependencia:

    * **Jar receptor de EventHub**. Esto es necesario para motor recibir los mensajes de concentrador de evento. Para usar este jar, actualice el **pom.xml** para agregar los siguientes procedimientos en `<dependencies>`.

            <dependency>
              <groupId>com.microsoft.azure</groupId>
              <artifactId>spark-streaming-eventhubs_2.10</artifactId>
              <version>1.6.0</version>
            </dependency> 

    * **Jar de controlador JDBC**. Esto es necesario para escribir los mensajes recibidos de concentrador de evento en una base de datos de SQL Azure. Puede descargar v4.1 o una versión posterior de este archivo jar desde [aquí](https://msdn.microsoft.com/sqlserver/aa937724.aspx). Agregar referencia a este jar en la biblioteca de proyectos. Realice los pasos siguientes:

        1. Desde la ventana de IntelliJ IDEA donde tiene la aplicación abierta, haga clic en **archivo**, haga clic en **Estructura del proyecto**y, a continuación, haga clic en **bibliotecas**. 
        
        2. Haga clic en el icono Agregar (![icono Agregar](./media/hdinsight-apache-spark-eventhub-streaming/add-icon.png)), haga clic en **Java**y, a continuación, desplácese hasta la ubicación donde haya descargado el tarro de controlador JDBC. Siga las indicaciones para agregar el archivo jar a la biblioteca de proyectos.

            ![Agregar las dependencias que faltan] (./media/hdinsight-apache-spark-eventhub-streaming/add-missing-dependency-jars.png "Agregar faltantes botes de dependencia")

        3. Haga clic en **Aplicar**.

6. Crear el archivo de salida jar. Realice los pasos siguientes.
    1. En el cuadro de diálogo de **Estructura del proyecto** , haga clic en **artefactos** y, a continuación, haga clic en el signo más. En el cuadro de diálogo emergente, haga clic en **JAR**y, a continuación, haga clic en **desde módulos con dependencias**.

        ![Crear JAR](./media/hdinsight-apache-spark-eventhub-streaming/create-jar-1.png)

    1. En el cuadro de diálogo **Crear JAR de módulos** , haga clic en los puntos suspensivos (![puntos suspensivos](./media/hdinsight-apache-spark-eventhub-streaming/ellipsis.png)) en la **Clase principal**.

    1. En el cuadro de diálogo **Seleccionar clase de principales** , seleccione cualquiera de las clases disponibles y, a continuación, haga clic en **Aceptar**.

        ![Crear JAR](./media/hdinsight-apache-spark-eventhub-streaming/create-jar-2.png)

    1. En el cuadro de diálogo **Crear JAR de módulos** , asegúrese de que está seleccionada la opción para **extraer el destino JAR** y, a continuación, haga clic en **Aceptar**. Esto crea un sola JAR con todas las dependencias.

        ![Crear JAR](./media/hdinsight-apache-spark-eventhub-streaming/create-jar-3.png)

    1. La ficha **Diseño de salida** enumera todos la jars se incluye como parte del proyecto de experto. Puede seleccionar y eliminar los que la aplicación Scala no tiene ninguna dependencia directa. Para la aplicación que estamos creando aquí, puede quitar todos excepto el último uno (**microsoft-motor-transmisión-ejemplos compilan resultados**). Seleccione la jars para eliminar y, a continuación, haga clic en el icono **Eliminar** (![icono Eliminar](./media/hdinsight-apache-spark-eventhub-streaming/delete-icon.png)).

        ![Crear JAR](./media/hdinsight-apache-spark-eventhub-streaming/delete-output-jars.png)

        Asegúrese de que **se basan en realizar** casilla está activada, lo que garantiza que se ha creado el tarro cada vez que se genera o se actualiza el proyecto. Haga clic en **Aplicar** y, a continuación, en **Aceptar**.

    1. En la pestaña **Diseño de salida** , derecha en la parte inferior del cuadro **Elementos disponibles** , tiene el tarro SQL JDBC que agregó anteriormente a la biblioteca de proyectos. Deberá agregar este valor a la pestaña **Diseño de salida** . Haga clic en el archivo jar y, a continuación, haga clic en **Extraer en la raíz de salida**.

        ![Extraer jar de dependencia](./media/hdinsight-apache-spark-eventhub-streaming/extract-dependency-jar.png)  

        La ficha **Diseño de salida** debe tener el siguiente aspecto.

        ![Ficha resultado final](./media/hdinsight-apache-spark-eventhub-streaming/final-output-tab.png)     

        En el cuadro de diálogo de **Estructura del proyecto** , haga clic en **Aplicar** y, a continuación, haga clic en **Aceptar**. 

    1. Desde la barra de menús, haga clic en **Generar**y, a continuación, haga clic en **Crear proyecto**. También puede hacer clic en **Generar artefactos** para crear el tarro. Jar de salida se crea en **\out\artifacts**.

        ![Crear JAR](./media/hdinsight-apache-spark-eventhub-streaming/output.png)

## <a name="run-the-applications-remotely-on-a-spark-cluster-using-livy"></a>Ejecutar las aplicaciones de forma remota en un clúster de motor mediante Livio

Vamos a utilizar a Livio para ejecutar la aplicación de transmisión de forma remota en un clúster de motor. Para obtener información detallada sobre cómo usar a Livio con clúster HDInsight Spark, consulte [enviar trabajos remotamente a un clúster de motor de Apache en HDInsight de Azure](hdinsight-apache-spark-livy-rest-interface.md). Antes de iniciar la ejecución los trabajos remotos a eventos de flujo con motor allí son un par de cosas que debe hacer:

1. Inicie la aplicación local independiente para generar eventos y enviado a concentrador de evento. Para hacerlo, utilice el siguiente comando:

        java -cp EventhubsSampleEventProducer.jar com.microsoft.eventhubs.client.example.EventhubsClientDriver --eventhubs-namespace "mysbnamespace" --eventhubs-name "myeventhub" --policy-name "mysendpolicy" --policy-key "<policy key>" --message-length 32 --thread-count 32 --message-count -1

2. Copiar el tarro streaming (**microsoft-motor-transmisión-examples.jar**) en el almacenamiento de blobs de Windows Azure asociado con el clúster. Esto hace que el tarro accesible a Livio. Puede usar [**AzCopy**](../storage/storage-use-azcopy.md), una utilidad de la línea de comandos para hacerlo. Hay una gran cantidad de otros clientes que puede usar para cargar los datos. Puede encontrar más información acerca de ellos al [cargar los datos para trabajos de Hadoop en HDInsight](hdinsight-upload-data.md).

3. Instalar DOBLEZ en el equipo donde se ejecuta estas aplicaciones desde. Usamos DOBLEZ invocar los extremos Livio para ejecutar las tareas de forma remota.

### <a name="run-the-applications-to-receive-the-events-into-an-azure-storage-blob-as-text"></a>Ejecutar las aplicaciones reciban los eventos en un almacenamiento Azure Blob como texto

Abra un símbolo del sistema, desplácese hasta el directorio donde ha instalado ENROLLAR y ejecute el comando siguiente (reemplazar nombre de usuario y contraseña y clúster nombre):

    curl -k --user "admin:mypassword1!" -v -H "Content-Type: application/json" -X POST --data @C:\Temp\inputBlob.txt "https://mysparkcluster.azurehdinsight.net/livy/batches"

Los parámetros en el archivo **inputBlob.txt** se definen como sigue:

    { "file":"wasbs:///example/jars/microsoft-spark-streaming-examples.jar", "className":"com.microsoft.spark.streaming.examples.workloads.EventhubsEventCount", "args":["--eventhubs-namespace", "mysbnamespace", "--eventhubs-name", "myeventhub", "--policy-name", "myreceivepolicy", "--policy-key", "<put-your-key-here>", "--consumer-group", "$default", "--partition-count", 10, "--batch-interval-in-seconds", 20, "--checkpoint-directory", "/EventCheckpoint", "--event-count-folder", "/EventCount/EventCount10"], "numExecutors":20, "executorMemory":"1G", "executorCores":1, "driverMemory":"2G" }

Deje que nosotros comprender cuáles son los parámetros del archivo de entrada:

* **archivo** es la ruta de acceso al archivo jar de la aplicación de la cuenta de almacenamiento de Azure asociado con el clúster.
* **nombre de clase** es el nombre de la clase en el tarro.
* **argumentos** es la lista de argumentos necesarios para la clase
* **numExecutors** es el número de núcleos utilizada por motor para ejecutar la aplicación de transmisión. Siempre debe al menos dos veces el número de particiones de concentrador de evento.
* **executorMemory**, **executorCores**, **driverMemory** son parámetros que se utiliza para asignar recursos necesarios a la aplicación de transmisión.

>[AZURE.NOTE] No es necesario crear las carpetas de salida (EventCheckpoint, conteo de eventos/EventCount10) que se usan como parámetros. La aplicación de transmisión creará automáticamente.
    
Cuando se ejecuta el comando, debería ver un resultado similar al siguiente:

    < HTTP/1.1 201 Created
    < Content-Type: application/json; charset=UTF-8
    < Location: /18
    < Server: Microsoft-IIS/8.5
    < X-Powered-By: ARR/2.5
    < X-Powered-By: ASP.NET
    < Date: Tue, 01 Dec 2015 05:39:10 GMT
    < Content-Length: 37
    <
    {"id":1,"state":"starting","log":[]}* Connection #0 to host mysparkcluster.azurehdinsight.net left intact

Anote el ID de lote en la última línea del resultado (en este ejemplo es '1'). Para comprobar que la aplicación se ejecute correctamente, puede buscar en su cuenta de almacenamiento de Azure asociado con el clúster y verá la carpeta **/EventCount/EventCount10** creada allí. Esta carpeta debe contener BLOB que captura al número de eventos que se procesan en el período de tiempo especificado para el parámetro **por lotes intervalo en segundos**.

La aplicación seguirá ejecutándose hasta que la eliminación. Para ello, use el siguiente comando:

    curl -k --user "admin:mypassword1!" -v -X DELETE "https://mysparkcluster.azurehdinsight.net/livy/batches/1"

### <a name="run-the-applications-to-receive-the-events-into-an-azure-storage-blob-as-json"></a>Ejecutar las aplicaciones reciban los eventos en un almacenamiento Azure Blob como JSON

Abra un símbolo del sistema, desplácese hasta el directorio donde ha instalado ENROLLAR y ejecute el comando siguiente (reemplazar nombre de usuario y contraseña y clúster nombre):

    curl -k --user "admin:mypassword1!" -v -H "Content-Type: application/json" -X POST --data @C:\Temp\inputJSON.txt "https://mysparkcluster.azurehdinsight.net/livy/batches"

Los parámetros en el archivo **inputJSON.txt** se definen como sigue:

    { "file":"wasbs:///example/jars/microsoft-spark-streaming-examples.jar", "className":"com.microsoft.spark.streaming.examples.workloads.EventhubsToAzureBlobAsJSON", "args":["--eventhubs-namespace", "mysbnamespace", "--eventhubs-name", "myeventhub", "--policy-name", "myreceivepolicy", "--policy-key", "<put-your-key-here>", "--consumer-group", "$default", "--partition-count", 10, "--batch-interval-in-seconds", 20, "--checkpoint-directory", "/EventCheckpoint", "--event-count-folder", "/EventCount/EventCount10", "--event-store-folder", "/EventStore10"], "numExecutors":20, "executorMemory":"1G", "executorCores":1, "driverMemory":"2G" }

Los parámetros son similares a especificada para la salida de texto, en el paso anterior. De nuevo, no es necesario crear las carpetas de salida (EventCheckpoint, conteo de eventos/EventCount10) que se usan como parámetros. La aplicación de transmisión creará automáticamente.

 Después de ejecutar el comando, puede buscar en su cuenta de almacenamiento de Azure asociado con el clúster y verá la carpeta **/EventStore10** creada allí. Abrir cualquier archivo con **parte** y el prefijo debería ver los eventos que se procesan en un formato JSON.

### <a name="run-the-applications-to-receive-the-events-into-a-hive-table"></a>Ejecutar las aplicaciones reciban los eventos en una tabla subárbol

Para ejecutar la aplicación que transmite eventos a una tabla subárbol necesita algunos componentes adicionales. Estos son:

* datanucleus-api-jdo-3.2.6.jar
* datanucleus-rdbms-3.2.9.jar
* núcleo de datanucleus de 3.2.10.jar
* sección site.xml

Los archivos **.jar** están disponibles en el clúster HDInsight Spark en `/usr/hdp/current/spark-client/lib`. Está disponible en la **sección site.xml** `/usr/hdp/current/spark-client/conf`.



Puede usar [WinScp](http://winscp.net/eng/download.php) para copiar estos archivos desde el clúster en su equipo local. A continuación, puede usar herramientas para copiar estos archivos a través de su cuenta de almacenamiento asociado con el clúster. Para obtener más información sobre cómo cargar archivos a la cuenta de almacenamiento, vea [cargar datos para trabajos de Hadoop en HDInsight](hdinsight-upload-data.md).

Una vez que haya copiado sobre los archivos a su cuenta de almacenamiento de Azure, abra un símbolo del sistema, desplácese hasta el directorio donde ha instalado ENROLLAR y ejecute el comando siguiente (reemplazar nombre de usuario y contraseña y clúster nombre):

    curl -k --user "admin:mypassword1!" -v -H "Content-Type: application/json" -X POST --data @C:\Temp\inputHive.txt "https://mysparkcluster.azurehdinsight.net/livy/batches"

Los parámetros en el archivo **inputHive.txt** se definen como sigue:

    { "file":"wasbs:///example/jars/microsoft-spark-streaming-examples.jar", "className":"com.microsoft.spark.streaming.examples.workloads.EventhubsToHiveTable", "args":["--eventhubs-namespace", "mysbnamespace", "--eventhubs-name", "myeventhub", "--policy-name", "myreceivepolicy", "--policy-key", "<put-your-key-here>", "--consumer-group", "$default", "--partition-count", 10, "--batch-interval-in-seconds", 20, "--checkpoint-directory", "/EventCheckpoint", "--event-count-folder", "/EventCount/EventCount10", "--event-hive-table", "EventHiveTable10" ], "jars":["wasbs:///example/jars/datanucleus-api-jdo-3.2.6.jar", "wasbs:///example/jars/datanucleus-rdbms-3.2.9.jar", "wasbs:///example/jars/datanucleus-core-3.2.10.jar"], "files":["wasbs:///example/jars/hive-site.xml"], "numExecutors":20, "executorMemory":"1G", "executorCores":1, "driverMemory":"2G" }

Los parámetros son similares a especificada para la salida de texto, en los pasos anteriores. De nuevo, no es necesario crear las carpetas de salida (EventCheckpoint, conteo de eventos/EventCount10) o el resultado de la tabla subárbol (EventHiveTable10) que se usan como parámetros. La aplicación de transmisión creará automáticamente. Observe que la opción **botes** y **archivos** incluye rutas de acceso a los archivos .jar y subárbol-site.xml que ha copiado en la cuenta de almacenamiento.

Para comprobar que la tabla subárbol se creó correctamente, puede SSH en el clúster y ejecutar consultas de la sección. Para obtener instrucciones, vea [Usar subárbol con Hadoop en HDInsight con SSH](hdinsight-hadoop-use-hive-ssh.md). Cuando se haya conectado con SSH, puede ejecutar el comando siguiente para comprobar que la tabla subárbol **EventHiveTable10**, se ha creado.

    show tables;

Debe ver un resultado similar al siguiente:

    OK
    eventhivetable10
    hivesampletable

También puede ejecutar una consulta de selección para ver el contenido de la tabla.

    SELECT * FROM eventhivetable10 LIMIT 10;

Debe ver un resultado similar al siguiente:

    ZN90apUSQODDTx7n6Toh6jDbuPngqT4c
    sor2M7xsFwmaRW8W8NDwMneFNMrOVkW1
    o2HcsU735ejSi2bGEcbUSB4btCFmI1lW
    TLuibq4rbj0T9st9eEzIWJwNGtMWYoYS
    HKCpPlWFWAJILwR69MAq863nCWYzDEw6
    Mvx0GQOPYvPR7ezBEpIHYKTKiEhYammQ
    85dRppSBSbZgThLr1s0GMgKqynDUqudr
    5LAWkNqorLj3ZN9a2mfWr9rZqeXKN4pF
    ulf9wSFNjD7BZXCyunozecov9QpEIYmJ
    vWzM3nvOja8DhYcwn0n5eTfOItZ966pa
    Time taken: 4.434 seconds, Fetched: 10 row(s)


### <a name="run-the-applications-to-receive-the-events-into-an-azure-sql-database-table"></a>Ejecutar las aplicaciones reciban los eventos en una tabla de base de datos de SQL Azure

Antes de ejecutar este paso, asegúrese de que tiene una base de datos de SQL Azure creado. Deberá valores para el nombre de la base de datos, el nombre del servidor de base de datos y las credenciales de administrador de la base de datos como parámetros. No es necesario crear la tabla de base de datos a través. La aplicación de transmisión crea por usted.

Abra un símbolo del sistema, desplácese hasta el directorio donde ha instalado ENROLLAR y ejecute el siguiente comando:

    curl -k --user "admin:mypassword1!" -v -H "Content-Type: application/json" -X POST --data @C:\Temp\inputSQL.txt "https://mysparkcluster.azurehdinsight.net/livy/batches"

Los parámetros en el archivo **inputSQL.txt** se definen como sigue:

    { "file":"wasbs:///example/jars/microsoft-spark-streaming-examples.jar", "className":"com.microsoft.spark.streaming.examples.workloads.EventhubsToAzureSQLTable", "args":["--eventhubs-namespace", "mysbnamespace", "--eventhubs-name", "myeventhub", "--policy-name", "myreceivepolicy", "--policy-key", "<put-your-key-here>", "--consumer-group", "$default", "--partition-count", 10, "--batch-interval-in-seconds", 20, "--checkpoint-directory", "/EventCheckpoint", "--event-count-folder", "/EventCount/EventCount10", "--sql-server-fqdn", "<database-server-name>.database.windows.net", "--sql-database-name", "mysparkdatabase", "--database-username", "sparkdbadmin", "--database-password", "<put-password-here>", "--event-sql-table", "EventContent" ], "numExecutors":20, "executorMemory":"1G", "executorCores":1, "driverMemory":"2G" }

Para comprobar que la aplicación se ejecute correctamente, puede conectarse a la base de datos de SQL Azure con SQL Server Management Studio. Para obtener instrucciones sobre cómo hacerlo, vea [conectarse a la base de datos de SQL con SQL Server Management Studio](../sql-database/sql-database-connect-query-ssms.md). Una vez que está conectado a la base de datos, puede ir a la tabla **EventContent** creado por la aplicación de transmisión. Puede ejecutar una consulta rápida para obtener los datos de la tabla. Ejecute la consulta siguiente:

    SELECT * FROM EventCount

Debería ver el resultado similar al siguiente:

    00046b0f-2552-4980-9c3f-8bba5647c8ee
    000b7530-12f9-4081-8e19-90acd26f9c0c
    000bc521-9c1b-4a42-ab08-dc1893b83f3b
    00123a2a-e00d-496a-9104-108920955718
    0017c68f-7a4e-452d-97ad-5cb1fe5ba81b
    001KsmqL2gfu5ZcuQuTqTxQvVyGCqPp9
    001vIZgOStka4DXtud0e3tX7XbfMnZrN
    00220586-3e1a-4d2d-a89b-05c5892e541a
    0029e309-9e54-4e1b-84be-cd04e6fce5ec
    003333cf-874f-4045-9da3-9f98c2b4ea49
    0043c07e-8d73-420a-9af7-1fcb94575356
    004a11a9-0c2c-4bc0-a7d5-2e0ebd947ab9

    
## <a name="seealso"></a>Vea también


* [Información general: Apache motor en HDInsight de Azure](hdinsight-apache-spark-overview.md)

### <a name="scenarios"></a>Escenarios

* [Motor con BI: realizar análisis de datos interactivas con motor en HDInsight con herramientas de inteligencia empresarial](hdinsight-apache-spark-use-bi-tools.md)

* [Motor con Machine aprendizaje: motor de uso en HDInsight para analizar usando HVAC datos de temperatura de creación](hdinsight-apache-spark-ipython-notebook-machine-learning.md)

* [Motor con Machine aprendizaje: motor de uso en HDInsight de predecir los resultados de la inspección de comida](hdinsight-apache-spark-machine-learning-mllib-ipython.md)

* [Análisis de registro de sitio Web con el motor en HDInsight](hdinsight-apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Crear y ejecutar aplicaciones

* [Crear una aplicación independiente de Scala](hdinsight-apache-spark-create-standalone-application.md)

* [Ejecutar trabajos de forma remota en un clúster de motor mediante Livio](hdinsight-apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Herramientas y extensiones

* [Usar el complemento de herramientas de HDInsight para IntelliJ IDEA para crear y enviar el motor Scala aplicaciones](hdinsight-apache-spark-intellij-tool-plugin.md)

* [Usar el complemento de herramientas de HDInsight para IntelliJ IDEA depurar aplicaciones del motor de forma remota](hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)

* [Utilizar blocs de notas de globo dirigible con un clúster de motor en HDInsight](hdinsight-apache-spark-use-zeppelin-notebook.md)

* [Kernels disponibles para el Bloc de notas de Jupyter en clúster de motor de HDInsight](hdinsight-apache-spark-jupyter-notebook-kernels.md)

* [Usar paquetes externos con blocs de notas Jupyter](hdinsight-apache-spark-jupyter-notebook-use-external-packages.md)

* [Instalar Jupyter en su equipo y conectarse a un clúster de HDInsight Spark](hdinsight-apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Administrar recursos

* [Administrar recursos para el clúster de motor de Apache en HDInsight de Azure](hdinsight-apache-spark-resource-manager.md)

* [Realizar el seguimiento y depuración trabajos que se ejecutan en un clúster de motor de Apache en HDInsight](hdinsight-apache-spark-job-debugging.md)


[hdinsight-versions]: hdinsight-component-versioning.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[azure-management-portal]: https://manage.windowsazure.com/
[azure-create-storageaccount]: ../storage-create-storage-account/ 
