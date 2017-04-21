<properties
   pageTitle="Analizar los datos de sensor con Apache tormenta y HBase | Microsoft Azure"
   description="Obtenga información sobre cómo conectarse a Apache tormenta con una red virtual. Usar tormenta con HBase para procesar sensor datos desde un concentrador de evento y visualizar con D3.js."
   services="hdinsight"
   documentationCenter=""
   authors="Blackmist"
   manager="jhubbard"
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="java"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="09/20/2016"
   ms.author="larryfr"/>

# <a name="analyze-sensor-data-with-apache-storm-event-hub-and-hbase-in-hdinsight-hadoop"></a>Analizar los datos de sensor con Apache tormenta, concentrador de evento y HBase en HDInsight (Hadoop) 

Aprenda a usar Apache tormenta en HDInsight a los datos de sensor de proceso de Azure evento concentrador, almacenar en Apache HBase en HDInsight y visualizar usando D3.js ejecuta como una aplicación Web de Azure.

La plantilla de administrador de recursos de Azure utilizada en este documento muestra cómo crear varios recursos de Azure en un grupo de recursos. Más concretamente, crea una red Virtual de Azure, dos clústeres HDInsight (tormenta y HBase) y una aplicación Web de Azure. Una implementación de node.js de un panel web en tiempo real se implementa automáticamente a la aplicación web.

> [AZURE.NOTE] La información de este documento y el ejemplo proporcionado, se ha probado con basados en Linux HDInsight 3.3 y 3.4 versiones de clúster.

## <a name="prerequisites"></a>Requisitos previos

* Una suscripción de Azure. Vea la [versión de prueba gratuita de Azure obtener](http://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

    > [AZURE.IMPORTANT] No es necesario un clúster HDInsight existente; los pasos de este documento creará los recursos siguientes:
    >
    > * Una red Virtual de Azure
    > * Una tormenta de clúster HDInsight (basados en Linux 2 nodos de trabajo)
    > * Un HBase en clúster HDInsight (basados en Linux 2 nodos de trabajo)
    > * Una aplicación Web de Azure que hospeda el panel web

* [Node.js](http://nodejs.org/): se usa para obtener una vista previa del panel web localmente en su entorno de desarrollo.

* [Java y el 1.7 JDK](http://www.oracle.com/technetwork/java/javase/downloads/index.html): utilizado para desarrollar la topología de tormenta.

* [Maven](http://maven.apache.org/what-is-maven.html): se usa para generar y compilar el proyecto.

* [GIT](http://git-scm.com/): utilizado para descargar el proyecto de GitHub.

* Un cliente __SSH__ : utilice para conectarse a los clústeres HDInsight basados en Linux. Para obtener más información sobre cómo utilizar SSH con HDInsight, consulte los siguientes documentos.

    * [Utilizar SSH con HDInsight desde un cliente de Windows](hdinsight-hadoop-linux-use-ssh-windows.md)

    * [Utilizar SSH con HDInsight desde un cliente Linux, Unix o Mac](hdinsight-hadoop-linux-use-ssh-unix.md)

    > [AZURE.NOTE] También debe tener acceso a la `scp` comando, que se utiliza para copiar archivos entre su entorno de desarrollo local y el clúster de HDInsight con SSH.

## <a name="architecture"></a>Arquitectura

![diagrama de arquitectura](./media/hdinsight-storm-sensor-data-analysis/devicesarchitecture.png)

Este ejemplo consta de los siguientes componentes:

* **Azure evento Hubs**: contiene los datos que se recopilan de sensores. En este ejemplo, una aplicación es siempre genera los datos.

* **Tormenta en HDInsight**: proporciona procesamiento en tiempo real de datos del concentrador de evento.

* **HBase en HDInsight**: proporciona un almacén datos persistente para datos después de que se ha procesado tormenta.

* **Servicio de red Virtual de Azure**: permite comunicaciones seguras entre la tormenta en HDInsight y HBase en HDInsight clústeres.

    > [AZURE.NOTE] Una red virtual es necesaria para poder usar la API de cliente Java HBase, ya no se expone a través de la puerta de enlace público para clústeres HBase. Instalar clústeres HBase y tormenta en la misma red virtual permite el clúster de tormenta (o cualquier otro sistema de la red virtual,) acceder directamente HBase con la API de cliente.

* **Sitio Web de paneles**: un panel de ejemplo que los gráficos de datos en tiempo real.

    * El sitio Web se implementa en Node.js, por lo que se puede ejecutar en cualquier sistema operativo de cliente para realizar pruebas o se puede implementar a sitios Web de Azure.

    * [Socket.IO](http://socket.io/) se usa para la comunicación en tiempo real entre la topología de tormenta y el sitio Web.

        > [AZURE.NOTE] Se trata de un detalle de implementación. Puede usar cualquier marco de comunicaciones, como WebSockets sin formato o SignalR.

    * [D3.js](http://d3js.org/) se usa para representar los datos que se envían al sitio Web de gráficos.

> [AZURE.IMPORTANT] Dos clústeres son necesarios, como no hay ningún método para crear un clúster de HDInsight de tormenta y HBase.

La topología lee los datos de evento concentrador mediante la clase [org.apache.storm.eventhubs.spout.EventHubSpout](http://storm.apache.org/releases/0.10.1/javadocs/org/apache/storm/eventhubs/spout/class-use/EventHubSpout.html) y escribe datos en HBase mediante la clase [org.apache.storm.hbase.bolt.HBaseBolt](https://storm.apache.org/javadoc/apidocs/org/apache/storm/hbase/bolt/class-use/HBaseBolt.html) . Comunicación con el sitio Web se realiza mediante [client.java socket.io](https://github.com/nkzawa/socket.io-client.java).

El siguiente es un diagrama de la topología.

![diagrama de topología](./media/hdinsight-storm-sensor-data-analysis/sensoranalysis.png)

> [AZURE.NOTE] Esta es una vista muy simplificada de la topología. En tiempo de ejecución, se crea una instancia de cada componente para cada partición para el concentrador de eventos que se está leyendo. Estas instancias se distribuyen entre los nodos del clúster y datos se enrutan entre ellas como sigue:
>
> * Datos de pitorro al analizador tiene equilibrada de carga.
> * Datos desde el analizador al panel y HBase se agrupan por ID de dispositivo, para que los mensajes desde el mismo dispositivo fluyan siempre al mismo componente.

### <a name="topology-components"></a>Componentes de topología

* **EventHub pitorro**: pitorro es proporcionados como parte de la versión de Apache tormenta 0.10.0 o superior.

    > [AZURE.NOTE] Pitorro Hubs de evento usado en este ejemplo requiere una tormenta de la versión de clúster HDInsight 3.3 o 3.4. Para obtener información sobre cómo usar el evento Hubs con una versión anterior de tormenta en HDInsight, consulte [eventos de proceso de Azure evento Hubs con tormenta en HDInsight](hdinsight-storm-develop-java-event-hub-topology.md).

* **ParserBolt.java**: los datos que emiten pitorro están JSON sin formato y, a continuación, se emite en ocasiones más de un evento en un momento. Este rayo muestra cómo leer los datos emitidos por pitorro y emitir a un nuevo flujo de tupla que contiene varios campos.

* **DashboardBolt.java**: Esto muestra cómo usar la biblioteca de cliente Socket.io para Java para enviar los datos en tiempo real en el panel de la web.

Este ejemplo usa el marco de trabajo de [flujo](https://storm.apache.org/releases/0.10.0/flux.html) , por lo que se encuentra la definición de topología en archivos YAML. Hay dos:

* __sin hbase.yaml__ - utilizar este archivo al probar la topología en su entorno de desarrollo. No utilice HBase componentes, ya que no puede acceder a la API de Java HBase desde fuera de la red virtual de que reside el clúster.

* __con hbase.yaml__ - utilizar este archivo al implementar la topología en el clúster de tormenta. Utiliza componentes HBase ya que se ejecuta en la misma red virtual como el clúster HBase.

## <a name="prepare-your-environment"></a>Preparar el entorno

Antes de utilizar este ejemplo, debe crear un concentrador de evento de Azure, que lee la topología de tormenta.

### <a name="configure-event-hub"></a>Configurar concentrador de evento

Hub de evento es el origen de datos en este ejemplo. Use los siguientes pasos para crear un nuevo evento de concentrador.

1. Desde el [portal de Azure](https://portal.azure.com), seleccione **+ nuevo** -> __Internet de cosas__ -> __Hubs de evento__.

2. En el módulo __Crear Namespace__ , realice las siguientes tareas:

    1. Escriba un __nombre__ para el espacio de nombres.
    2. Seleccione un nivel de precios. __Básica__ es suficiente para este ejemplo.
    3. Seleccione la __suscripción__ de Azure usar.
    4. Seleccione un grupo de recursos existente o cree uno nuevo.
    5. Seleccione la __ubicación__ para el concentrador de eventos.
    6. Seleccione __Anclar al panel__y, a continuación, haga clic en __crear__.

3. Cuando se complete el proceso de creación, se muestra el módulo de Hubs de evento para el espacio de nombres. Desde aquí, seleccione __+ Agregar evento concentrador__. En el módulo __Crear concentrador de evento__ , escriba un nombre de __sensordata__ y haga clic en __crear__. Deje el resto de los campos en los valores predeterminados.

4. En el módulo de Hubs de evento para el espacio de nombres, seleccione __Hubs de evento__. Seleccione la entrada de __sensordata__ .

5. En el módulo de la sensordata concentrador de evento, seleccione __directivas de acceso compartido__. Utilice el vínculo __+ Agregar__ para agregar las siguientes directivas:


  	| Nombre de la directiva | Reclamaciones |
  	| ----- | ----- |
  	| dispositivos | Enviar |
  	| tormenta | Escuchar |

5. Seleccione las dos directivas y anote el valor de __Clave principal__ . Necesitará el valor de ambas directivas en futuras pasos.

## <a name="download-and-configure-the-project"></a>Descargar y configurar el proyecto

Use las siguientes acciones para descargar el proyecto de GitHub.

    git clone https://github.com/Blackmist/hdinsight-eventhub-example

Cuando finalice el comando, tendrá la estructura de directorio siguiente:

    hdinsight-eventhub-example/
        TemperatureMonitor/ - this contains the topology
            resources/
                log4j2.xml - set logging to minimal
                no-hbase.yaml - topology definition for local testing
                with-hbase.yaml - topology definition that uses HBase in a virutal network
            src/ - the Java bolts
            dev.properties - contains configuration values for your environment
        dashboard/nodejs/ - this is the node.js web dashboard
        SendEvents/ - utilities to send fake sensor data

> [AZURE.NOTE] Este documento no empiece a todos los detalles del código incluido en este ejemplo; Sin embargo, el código totalmente es comentarios.

Abra el archivo **hdinsight-eventhub-example/TemperatureMonitor/dev.properties** y agregue la información de evento concentrador para las siguientes líneas:

    eventhub.read.policy.name: storm
    eventhub.read.policy.key: KeyForTheStormPolicy
    eventhub.namespace: YourNamespace
    eventhub.name: sensordata

> [AZURE.NOTE] En este ejemplo se supone que utiliza __tormenta__ como el nombre de la directiva que tiene una solicitud de __escuchar__ y que su centro de evento se denomina __sensordata__.

 Guarde el archivo después de agregar esta información.

## <a name="compile-and-test-locally"></a>Compilar y probar localmente

Antes de la prueba, debe iniciar el panel para ver el resultado de la topología y generar datos que se almacenarán en el Hub de evento.

> [AZURE.IMPORTANT] El componente HBase de esta topología no está activo cuando probar localmente, como la API de Java para el clúster de HBase no puede tener acceso desde fuera de la red Virtual de Azure que contiene los clústeres.

### <a name="start-the-web-application"></a>Inicie la aplicación web

1. Abra un nuevo símbolo o terminal y cambie los directorios a **hdinsight-eventhub/panel de ejemplo**, use el comando siguiente para instalar las dependencias necesarias para la aplicación web:

        npm install

2. Utilice el comando siguiente para iniciar la aplicación web:

        node server.js

    Verá un mensaje similar al siguiente:

        Server listening at port 3000

2. Abra un navegador web y escriba **http://localhost:3000 /** como la dirección. Verá una página similar al siguiente:

    ![panel Web](./media/hdinsight-storm-sensor-data-analysis/emptydashboard.png)

    Deje este símbolo o terminal abierto. Después de probar, use Ctrl-C para detener el servidor web.

### <a name="start-generating-data"></a>Empezar a generar datos

> [AZURE.NOTE] Los pasos de esta sección utiliza Node.js para que se pueden usar en cualquier plataforma. Para obtener otros ejemplos de idioma, vea el directorio **SendEvents** .

1. Abra un nuevo símbolo, shell o terminal y cambie los directorios a **hdinsight-eventhub-ejemplo/SendEvents/nodejs**, use el comando siguiente para instalar las dependencias necesarias para la aplicación:

        npm install

2. Abra el archivo **app.js** en un editor de texto y agregue la información de evento concentrador obtenido anteriormente:

        // ServiceBus Namespace
        var namespace = 'YourNamespace';
        // Event Hub Name
        var hubname ='sensordata';
        // Shared access Policy name and key (from Event Hub configuration)
        var my_key_name = 'devices';
        var my_key = 'YourKey';
    
    > [AZURE.NOTE] En este ejemplo se supone que ha utilizado __sensordata__ como el nombre de su concentrador de evento y __dispositivos__ como el nombre de la directiva que tiene una solicitud de __Enviar__ .

2. Use el comando siguiente para insertar nuevas entradas en el Hub de evento:

        node app.js

    Debería ver varias líneas de salida que contienen los datos enviados a concentrador de evento. Estos tendrá un aspecto similares al siguiente:

        {"TimeStamp":"2015-02-10T14:43.05.00320Z","DeviceId":"0","Temperature":7}
        {"TimeStamp":"2015-02-10T14:43.05.00320Z","DeviceId":"1","Temperature":39}
        {"TimeStamp":"2015-02-10T14:43.05.00320Z","DeviceId":"2","Temperature":86}
        {"TimeStamp":"2015-02-10T14:43.05.00320Z","DeviceId":"3","Temperature":29}
        {"TimeStamp":"2015-02-10T14:43.05.00320Z","DeviceId":"4","Temperature":30}
        {"TimeStamp":"2015-02-10T14:43.05.00320Z","DeviceId":"5","Temperature":5}
        {"TimeStamp":"2015-02-10T14:43.05.00320Z","DeviceId":"6","Temperature":24}
        {"TimeStamp":"2015-02-10T14:43.05.00320Z","DeviceId":"7","Temperature":40}
        {"TimeStamp":"2015-02-10T14:43.05.00320Z","DeviceId":"8","Temperature":43}
        {"TimeStamp":"2015-02-10T14:43.05.00320Z","DeviceId":"9","Temperature":84}

### <a name="start-the-topology"></a>Iniciar la topología

2. Abra un nuevo símbolo, shell o directorios terminal y cambiar a __Hdinsight-eventhub-ejemplo/TemperatureMonitor__y, a continuación, utilice el comando siguiente para iniciar la topología:

        mvn compile exec:java -Dexec.args="--local -R /no-hbase.yaml --filter dev.properties"
    
    Si está utilizando PowerShell, use en su lugar lo siguiente:

        mvn compile exec:java "-Dexec.args=--local -R /no-hbase.yaml --filter dev.properties"

    > [AZURE.NOTE] Si está en un sistema Unix/Linux/OS X y tiene [instalado tormenta en su entorno de desarrollo](http://storm.apache.org/releases/0.10.0/Setting-up-development-environment.html), puede usar los comandos siguientes en su lugar:
    >
    > `mvn compile package`
    > `storm jar target/WordCount-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux --local -R /no-hbase.yaml`

    Se inicia la topología definida en el archivo __sin hbase.yaml__ en modo local. Los valores incluidos en el archivo __dev.properties__ proporcionan la información de conexión para Hubs de evento. Una vez iniciado, la topología lee las entradas de concentrador de evento y enviarlos a panel ejecutándose en su equipo local. Debe ver líneas aparecen en el panel de la web, de forma similar al siguiente:

    ![panel con datos](./media/hdinsight-storm-sensor-data-analysis/datadashboard.png)

3. Mientras se está ejecutando el panel, use la `node app.js` comando de los pasos anteriores para enviar los nuevos datos a Hubs de evento. Porque los valores de temperatura aleatoriamente generados, debe actualizar el gráfico para mostrar grandes cambios de temperatura.

    > [AZURE.NOTE] Debe estar en el directorio __Hdinsight-eventhub-ejemplo/SendEvents/Nodejs__ cuando se usa el `node app.js` comando.

3. Después de comprobar que esto funciona, detenga la topología con CTRL+c. Puede utilizar Ctrl + C para detener el servidor web local también.

## <a name="create-a-storm-and-hbase-cluster"></a>Crear un clúster de tormenta y HBase

Para poder ejecutar la topología en HDInsight y habilitar el rayo HBase, debe crear un nuevo tormenta y HBase clúster. Los pasos descritos en esta sección use un [Administrador de recursos de Azure plantilla](../resource-group-template-deploy.md) para crear un nuevo clúster de red Virtual de Azure y una tormenta y HBase en la red virtual. La plantilla también crea una aplicación Web de Azure e implementa una copia del panel en él.

> [AZURE.NOTE] Se utiliza una red virtual para que la topología que se ejecutan en el clúster de tormenta puede comunicarse directamente con el clúster de HBase con la API de Java HBase.

La plantilla de administrador de recursos utilizada en este documento se encuentra en un contenedor de blob público en __https://hditutorialdata.blob.core.windows.net/armtemplates/create-linux-based-hbase-storm-cluster-in-vnet.json__.

1. Haga clic en el botón siguiente para iniciar sesión en Azure y abra la plantilla de administrador de recursos en el portal de Azure.

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Farmtemplates%2Fcreate-linux-based-hbase-storm-cluster-in-vnet.json" target="_blank"><img src="https://acom.azurecomcdn.net/80C57D/cdn/mediahandler/docarticles/dpsmedia-prod/azure.microsoft.com/en-us/documentation/articles/hdinsight-hbase-tutorial-get-started-linux/20160201111850/deploy-to-azure.png" alt="Deploy to Azure"></a>

2. Desde el módulo de **parámetros** , escriba lo siguiente:

    ![Parámetros de HDInsight](./media/hdinsight-storm-sensor-data-analysis/parameters.png)
    
    * **BASECLUSTERNAME**: este valor se utilizará como nombre de la base de la tormenta y clústeres HBase. Por ejemplo, si escribir __hdi__ creará un clúster de tormenta denominado __tormenta hdi__ y un clúster de HBase denominado __hbase hdi__.
    * __CLUSTERLOGINUSERNAME__: el nombre de usuario de administración para los clústeres tormenta y HBase.
    * __CLUSTERLOGINPASSWORD__: la contraseña de usuario de administración de los clústeres tormenta y HBase.
    * __SSHUSERNAME__: SSH el usuario para crear clústeres de tormenta y HBase.
    * __SSHPASSWORD__: la contraseña para el usuario SSH para los clústeres tormenta y HBase.
    * __Ubicación__: la región que se creará en los clústeres.
    
    Haga clic en __Aceptar__ para guardar los parámetros.
    
3. Use la sección de __grupo de recursos__ para crear un nuevo grupo de recursos o seleccione uno existente.

4. En el menú desplegable de __ubicación de grupo de recursos__ , seleccione la misma ubicación como que ha seleccionado para el parámetro de __ubicación__ .

5. Seleccione las __condiciones legales__y haga clic en __crear__.

6. Por último, active __Anclar al panel__ y haga clic en __crear__. Tardará aproximadamente 20 minutos para crear los clústeres.

Una vez que se han creado los recursos, se le redirigirá a un módulo del grupo de recursos que contiene los clústeres y paneles de web.

![Módulo de grupo de recursos para vnet y clústeres](./media/hdinsight-storm-sensor-data-analysis/groupblade.png)

> [AZURE.IMPORTANT] Observe que los nombres de los clústeres HDInsight __Nombre de base de tormenta__ y __Nombre de base de hbase__, donde nombre base es el nombre proporcionado a la plantilla. Que usa estos nombres en pasos posteriores al conectarse a los clústeres. Observe también que el nombre del sitio de escritorio __Panel nombre base__. Utilícelo más adelante cuando se ve el panel.

## <a name="configure-the-dashboard-bolt"></a>Configurar el rayo de paneles

Para enviar datos al panel implementado como una aplicación web, debe modificar la línea siguiente en el archivo __dev.properties__ :

    dashboard.uri: http://localhost:3000

Cambiar `http://localhost:3000` a `http://BASENAME-dashboard.azurewebsites.net` y guarde el archivo. Reemplazar __nombre base__ con el nombre de base que se proporcionan en el paso anterior. También puede usar el grupo de recursos que creó anteriormente para seleccionar el escritorio y ver la dirección URL.

## <a name="create-the-hbase-table"></a>Crear la tabla HBase

Para almacenar datos en HBase, se debe crear una tabla. Generalmente desea crear previamente recursos que necesita escribir en tormenta, como intentar crear recursos desde dentro de una topología de tormenta pueden dar lugar a varias copias distribuidas del código intenta crear el mismo recurso. Cree los recursos fuera de la topología y simplemente usar tormenta de lectura/escritura y análisis.

1. Usar SSH para conectarse al clúster HBase con SSH usuario y contraseña para la plantilla durante la creación de clúster. Por ejemplo, si va a conectarse utilizando la `ssh` comando, debería usar la siguiente sintaxis:

        ssh USERNAME@hbase-BASENAME-ssh.azurehdinsight.net
    
    En este comando, reemplace el __nombre de usuario__ con el nombre de usuario SSH proporcionado al crear el clúster y __nombre base__ con el nombre de base que ha proporcionado. Cuando se le pida, escriba la contraseña para el usuario SSH.

2. Desde la sesión SSH, inicie la consola de HBase.

        hbase shell
    
    Una vez que haya cargado el shell, verá un `hbase(main):001:0>` símbolo del sistema.

3. Desde la consola de HBase, escriba el comando siguiente para crear una tabla para almacenar los datos de sensor:

        create 'SensorData', 'cf'

4. Compruebe que la tabla se ha creado mediante el comando siguiente:

        scan 'SensorData'
        
    Devuelve información similar al siguiente ejemplo, que indica que hay 0 filas en la tabla.
    
        ROW                   COLUMN+CELL                                       0 row(s) in 0.1900 seconds

5. Escriba lo siguiente para salir de la consola de HBase:

        exit

## <a name="configure-the-hbase-bolt"></a>Configurar el rayo HBase

Para escribir en HBase desde el clúster de tormenta, debe proporcionar el rayo HBase los detalles de la configuración de su clúster HBase. La manera más sencilla de hacerlo es descargar el __hbase site.xml__ desde el clúster e incluir en el proyecto. También debe quitar los comentarios varias dependencias en el archivo __pom.xml__ , que cargar el componente de tormenta hbase y dependencias necesarias.

> [AZURE.IMPORTANT] También debe descargar el archivo de tormenta hbase.jar proporcionado en su tormenta en clúster de clúster 3.3 o 3.4 HDInsight; Esta versión se compila para trabajar con HBase 1.1.x, que se usa para HBase en HDInsight 3.3 y 3.4 clústeres. Si utiliza un componente de tormenta hbase desde cualquier lugar, se puede compilar con una versión anterior de HBase.

### <a name="download-the-hbase-sitexml"></a>Descargar el site.xml hbase

Desde el símbolo del sistema, use SCP para descargar el archivo de __hbase site.xml__ desde el clúster. En el ejemplo siguiente, reemplace el __nombre de usuario__ por el usuario SSH proporcionado al crear el clúster y __nombre base__ con el nombre de base proporcionado anteriormente. Cuando se le pida, escriba la contraseña para el usuario SSH. Reemplazar el `/path/to/TemperatureMonitor/resources/hbase-site.xml` con la ruta de acceso a este archivo en el proyecto TemperatureMonitor.

    scp USERNAME@hbase-BASENAME-ssh.azurehdinsight.net:/etc/hbase/conf/hbase-site.xml /path/to/TemperatureMonitor/resources/hbase-site.xml

Esto descargará el __site.xml hbase__ a la ruta especificada.

### <a name="download-and-install-the-storm-hbase-component"></a>Descargue e instale el componente de tormenta hbase

1. Desde el símbolo del sistema, use SCP para descargar el archivo de __tormenta hbase.jar__ desde el clúster de tormenta. En el ejemplo siguiente, reemplace el __nombre de usuario__ por el usuario SSH proporcionado al crear el clúster y __nombre base__ con el nombre de base proporcionado anteriormente. Cuando se le pida, escriba la contraseña para el usuario SSH.

        scp USERNAME@storm-BASENAME-ssh.azurehdinsight.net:/usr/hdp/current/storm-client/contrib/storm-hbase/storm-hbase*.jar .

    Esto descargará un archivo llamado `storm-hbase-####.jar`, donde ### es el número de versión de tormenta para este clúster. Tome nota de este número, como se utiliza más adelante.

2. Use el comando siguiente para instalar este componente en el repositorio de experto local en su entorno de desarrollo. Esto permite Maven encontrar el paquete durante la compilación del proyecto. Reemplazar __####__ con el número de versión incluido en el nombre de archivo.

        mvn install:install-file -Dfile=storm-hbase-####.jar -DgroupId=org.apache.storm -DartifactId=storm-hbase -Dversion=#### -Dpackaging=jar
    
    Si está utilizando PowerShell, use el siguiente comando:

        mvn install:install-file "-Dfile=storm-hbase-####.jar" "-DgroupId=org.apache.storm" "-DartifactId=storm-hbase" "-Dversion=####" "-Dpackaging=jar"

### <a name="enable-the-storm-hbase-component-in-the-project"></a>Habilitar el componente de tormenta hbase en el proyecto

1. Abra el archivo __TemperatureMonitor/pom.xml__ y elimine las siguientes líneas:

        <!-- uncomment this section to enable the hbase-bolt
        end comment for hbase-bolt section -->
    
    > [AZURE.IMPORTANT] Eliminar sólo estas dos líneas; No elimine ninguna de las líneas entre ellas.
    
    Esto permite varios componentes que son necesarios cuando se comunica con HBase mediante el rayo hbase.

2. Busque las siguientes líneas y, a continuación, reemplace __####__ con el número de versión del archivo tormenta hbase que ha descargado anteriormente.

        <dependency>
            <groupId>org.apache.storm</groupId>
            <artifactId>storm-hbase</artifactId>
            <version>####</version>
        </dependency>

    > [AZURE.IMPORTANT] El número de versión debe coincidir con la versión utilizada al instalar el componente en el repositorio de experto en local, como experto usa esta información para cargar el componente al generar el proyecto.

2. Guarde el archivo __pom.xml__ .

## <a name="build-package-and-deploy-the-solution-to-hdinsight"></a>Crear, empaquetar e implementar la solución a HDInsight

En su entorno de desarrollo, realice los pasos siguientes para implementar la topología de tormenta el clúster de tormenta.

1. Desde el directorio __TemperatureMonitor__ , use el comando siguiente para realizar una nueva generación y crear un paquete JAR de su proyecto:

        mvn clean compile package

    Se creará un archivo llamado **TemperatureMonitor-1.0-SNAPSHOT.jar** en el directorio de **destino** del proyecto.

2. Usar scp para cargar el archivo __TemperatureMonitor-1.0-SNAPSHOT.jar__ en el clúster de tormenta. En el ejemplo siguiente, reemplace el __nombre de usuario__ por el usuario SSH proporcionado al crear el clúster y __nombre base__ con el nombre de base proporcionado anteriormente. Cuando se le pida, escriba la contraseña para el usuario SSH.

        scp target\TemperatureMonitor-1.0-SNAPSHOT.jar USERNAME@storm-BASENAME-ssh.azurehdinsight.net:TemperatureMonitor-1.0-SNAPSHOT.jar
    
    > [AZURE.NOTE] Puede tardar varios minutos para cargar el archivo, como será el tamaño de varios megabytes.

    Use scp para cargar el archivo __dev.properties__ , ya que contiene la información que se usa para conectarse a Hubs de evento y el panel.

        scp dev.properties USERNAME@storm-BASENAME-ssh.azurehdinsight.net:dev.properties

3. Una vez que se hayan cargado los archivos, conectarse al clúster mediante SSH.

        ssh USERNAME@storm-BASENAME-ssh.azurehdinsight.net

4. Desde la sesión SSH, utilice el comando siguiente para iniciar la topología.

        storm jar TemperatureMonitor-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux --remote -R /with-hbase.yaml --filter dev.properties
    
    Se inicia la topología mediante la definición de topología en el archivo __con hbase.yaml__ y los valores de configuración en el archivo __dev.properties__ .

3. Una vez comenzada la topología, abra un navegador el sitio Web que ha publicado en Azure, a continuación, use la `node app.js` comando para enviar datos a concentrador de evento. Verá el panel web actualizar para mostrar la información.

    ![panel](./media/hdinsight-storm-sensor-data-analysis/datadashboard.png)

## <a name="view-hbase-data"></a>Ver datos HBase

Una vez que haya enviado datos al uso de topología `node app.js`, realice los pasos siguientes para conectarse a HBase y compruebe que se ha escrito los datos a la tabla que creó anteriormente.

1. Utilizar SSH para conectar con el clúster de HBase.

        ssh USERNAME@hbase-BASENAME-ssh.azurehdinsight.net

2. Desde la sesión SSH, inicie la consola de HBase.

        hbase shell
    
    Una vez que haya cargado el shell, verá un `hbase(main):001:0>` símbolo del sistema.

2. Ver las filas de la tabla:

        scan 'SensorData'
        
    Debe devolver información similar a la siguiente que indica que hay 0 filas en la tabla.
    
        hbase(main):002:0> scan 'SensorData'
        ROW                             COLUMN+CELL
        \x00\x00\x00\x00               column=cf:temperature, timestamp=1467290788277, value=\x00\x00\x00\x04
        \x00\x00\x00\x00               column=cf:timestamp, timestamp=1467290788277, value=2015-02-10T14:43.05.00320Z
        \x00\x00\x00\x01               column=cf:temperature, timestamp=1467290788348, value=\x00\x00\x00M
        \x00\x00\x00\x01               column=cf:timestamp, timestamp=1467290788348, value=2015-02-10T14:43.05.00320Z
        \x00\x00\x00\x02               column=cf:temperature, timestamp=1467290788268, value=\x00\x00\x00R
        \x00\x00\x00\x02               column=cf:timestamp, timestamp=1467290788268, value=2015-02-10T14:43.05.00320Z
        \x00\x00\x00\x03               column=cf:temperature, timestamp=1467290788269, value=\x00\x00\x00#
        \x00\x00\x00\x03               column=cf:timestamp, timestamp=1467290788269, value=2015-02-10T14:43.05.00320Z
        \x00\x00\x00\x04               column=cf:temperature, timestamp=1467290788356, value=\x00\x00\x00>
        \x00\x00\x00\x04               column=cf:timestamp, timestamp=1467290788356, value=2015-02-10T14:43.05.00320Z
        \x00\x00\x00\x05               column=cf:temperature, timestamp=1467290788326, value=\x00\x00\x00\x0D
        \x00\x00\x00\x05               column=cf:timestamp, timestamp=1467290788326, value=2015-02-10T14:43.05.00320Z
        \x00\x00\x00\x06               column=cf:temperature, timestamp=1467290788253, value=\x00\x00\x009
        \x00\x00\x00\x06               column=cf:timestamp, timestamp=1467290788253, value=2015-02-10T14:43.05.00320Z
        \x00\x00\x00\x07               column=cf:temperature, timestamp=1467290788229, value=\x00\x00\x00\x12
        \x00\x00\x00\x07               column=cf:timestamp, timestamp=1467290788229, value=2015-02-10T14:43.05.00320Z
        \x00\x00\x00\x08               column=cf:temperature, timestamp=1467290788336, value=\x00\x00\x00\x16
        \x00\x00\x00\x08               column=cf:timestamp, timestamp=1467290788336, value=2015-02-10T14:43.05.00320Z
        \x00\x00\x00\x09               column=cf:temperature, timestamp=1467290788246, value=\x00\x00\x001
        \x00\x00\x00\x09               column=cf:timestamp, timestamp=1467290788246, value=2015-02-10T14:43.05.00320Z
        10 row(s) in 0.1800 seconds

    > [AZURE.NOTE] Esta operación de análisis sólo devolverá un máximo de 10 filas de la tabla.

## <a name="delete-your-clusters"></a>Eliminar los clústeres

[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

Para clústeres, el almacenamiento y la aplicación web a la vez, eliminar el grupo de recursos que los contiene.

## <a name="next-steps"></a>Pasos siguientes

Ahora ha aprendido a usar tormenta para leer los datos de evento Hubs, almacenar en HBase y visualizar la información en un panel externo mediante Socket.io y D3.js.

* Para obtener más ejemplos de topologías de tormenta con HDInsight, consulte:

    * [Topologías de ejemplo para tormenta en HDInsight](hdinsight-storm-example-topology.md)

* Para obtener más información acerca de tormenta Apache, consulte el sitio de [Tormenta Apache](https://storm.incubator.apache.org/) .

* Para obtener más información sobre HBase HDInsight, consulte la [HBase con información general de HDInsight](hdinsight-hbase-overview.md).

* Para obtener más información sobre Socket.io, consulte el sitio [socket.io](http://socket.io/) .

* Para obtener más información sobre D3.js, consulte [D3.js - datos controlados por documentos](http://d3js.org/).

* Para obtener información sobre la creación de topologías en Java, vea [desarrollar Java topologías de tormenta Apache en HDInsight](hdinsight-storm-develop-java-topology.md).

* Obtenga información sobre la creación de topologías en .NET [C# desarrollar topologías de tormenta Apache en HDInsight con Visual Studio](hdinsight-storm-develop-csharp-visual-studio-topology.md).

[azure-portal]: https://portal.azure.com
