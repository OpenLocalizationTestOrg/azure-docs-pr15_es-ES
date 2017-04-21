<properties
pageTitle="Utilizar el almacén de lago de datos de Azure con Apache tormenta en HDInsight de Azure"
description="Obtenga información sobre cómo escribir datos en el almacén de lago de datos de Azure desde una topología de tormenta Apache en HDInsight. Este documento, el ejemplo asociado, se muestra cómo puede utilizarse el componente HdfsBolt para escribir en el almacén de datos de lago."
services="hdinsight"
documentationCenter="na"
authors="Blackmist"
manager="jhubbard"
editor="cgronlun"/>

<tags
ms.service="hdinsight"
ms.devlang="na"
ms.topic="article"
ms.tgt_pltfrm="na"
ms.workload="big-data"
ms.date="09/06/2016"
ms.author="larryfr"/>

#<a name="use-azure-data-lake-store-with-apache-storm-with-hdinsight"></a>Utilizar el almacén de lago de datos de Azure con Apache tormenta con HDInsight

Almacén de lago de datos de Azure es un servicio de almacenamiento de nube compatible HDFS que proporciona alto rendimiento, la disponibilidad, la duración y la confiabilidad de los datos. En este documento, obtendrá información sobre cómo utilizar una topología de tormenta basada en Java para escribir datos en el almacén de lago de datos de Azure mediante el componente [HdfsBolt](http://storm.apache.org/javadoc/apidocs/org/apache/storm/hdfs/bolt/HdfsBolt.html) , que se incluye como parte de tormenta Apache.

> [AZURE.IMPORTANT] La topología de ejemplo utilizada en este documento se basa en componentes que se incluyen con tormenta en clústeres HDInsight y a continuación, pueden requerir la modificación para trabajar con el almacén de lago de datos de Azure cuando se usa con otros clústeres Apache tormenta.

##<a name="prerequisites"></a>Requisitos previos

* [Java JDK 1.7](https://www.oracle.com/technetwork/java/javase/downloads/jdk7-downloads-1880260.html) o superior
* [Experto en 3.x](https://maven.apache.org/download.cgi)
* Una suscripción de Azure
* Una tormenta de la versión de clúster HDInsight 3.2. Para crear una nueva tormenta en clúster HDInsight, realice los pasos en el documento [HDInsight usar con datos lago almacén con Azure](../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md) . Los pasos de este documento le ayudará a crear un nuevo clúster HDInsight y el almacenamiento de lago de datos de Azure.  

    > [AZURE.IMPORTANT] Cuando se crea el clúster HDInsight, debe seleccionar __tormenta__ como el tipo de clúster y __3,2__ como la versión. Puede ser el sistema operativo Windows o Linux.  

###<a name="configure-environment-variables"></a>Configurar variables de entorno

Pueden establecer las siguientes variables de entorno cuando instalar Java y el JDK en su estación de trabajo de desarrollo. Sin embargo, debe comprobar que existen y que contienen los valores correctos para su sistema.

* __JAVA_HOME__ - deben apuntar al directorio donde está instalado el entorno de tiempo de ejecución de Java (JRE). Por ejemplo, en una distribución de Unix o Linux, debería tener un valor similar a `/usr/lib/jvm/java-7-oracle`. En Windows, tendría un valor similar a `c:\Program Files (x86)\Java\jre1.7`.

* __Ruta de acceso__ : debe contener las rutas siguientes:

    * __JAVA\_HOME__ (o la ruta de acceso equivalente)
    
    * __JAVA\_HOME\bin__ (o la ruta de acceso equivalente)
    
    * El directorio donde está instalado Maven

##<a name="topology-implementation"></a>Implementación de topología

El ejemplo utilizado en este documento está escrito en Java y usa los siguientes componentes:

* __TickSpout__: genera los datos de otros componentes de la topología.

* __PartialCount__: cuenta eventos generados por TickSpout.

* __FinalCount__: agregados contar datos de PartialCount.

* __ADLStoreBolt__: escribe datos en el almacén de lago de datos de Azure mediante el componente [HdfsBolt](http://storm.apache.org/javadoc/apidocs/org/apache/storm/hdfs/bolt/HdfsBolt.html) .

El proyecto que contiene esta topología está disponible como descarga desde [https://github.com/Azure-Samples/hdinsight-storm-azure-data-lake-store](https://github.com/Azure-Samples/hdinsight-storm-azure-data-lake-store).

###<a name="understanding-adlstorebolt"></a>Descripción ADLStoreBolt

El ADLStoreBolt es el nombre de la instancia de HdfsBolt en la topología que escribe en lago de datos de Azure. Esto no es una versión especial de HdfsBolt creado por Microsoft; Sin embargo dependen de los valores de configuración de sitio principal, así como los componentes de Hadoop que se incluyen con HDInsight de Azure para la comunicación con datos lago.

Más concretamente, cuando se crea un clúster de HDInsight, puede asociar con un almacén de lago de datos de Azure. Esto escribe entradas en el sitio principal de la tienda de lago de datos seleccionada, que se utilizan por componentes como cliente de hadoop y hadoop hdfs para habilitar la comunicación con el almacén de datos de lago.

> [AZURE.NOTE] Microsoft ha contribuido código para los proyectos de tormenta y Apache Hadoop que permite la comunicación con el almacenamiento de blobs de Windows Azure y de almacén de lago de datos de Azure, pero esta funcionalidad no pueden incluirse en otras distribuciones Hadoop y tormenta de forma predeterminada.

La configuración de HdfsBolt en la topología es la siguiente:

    // 1. Create sync and rotation policies to control when data is synched
    //    (written) to the file system and when to roll over into a new file.
    SyncPolicy syncPolicy = new CountSyncPolicy(1000);
    FileRotationPolicy rotationPolicy = new FileSizeRotationPolicy(0.5f, Units.KB);
    // 2. Set the format. In this case, comma delimited
    RecordFormat recordFormat = new DelimitedRecordFormat().withFieldDelimiter(",");
    // 3. Set the directory name. In this case, '/stormdata/'
    FileNameFormat fileNameFormat = new DefaultFileNameFormat().withPath("/stormdata/");
    // 4. Create the bolt using the previously created settings,
    //    and also tell it the base URL to your Data Lake Store.
    // NOTE! Replace 'MYDATALAKE' below with the name of your data lake store.
    HdfsBolt adlsBolt = new HdfsBolt()
        .withFsUrl("adl://MYDATALAKE.azuredatalakestore.net/")
        .withRecordFormat(recordFormat)
        .withFileNameFormat(fileNameFormat)
        .withRotationPolicy(rotationPolicy)
        .withSyncPolicy(syncPolicy);
    // 4. Give it a name and wire it up to the bolt it accepts data
    //    from. NOTE: The name used here is also used as part of the
    //    file name for the files written to Data Lake Store.
    builder.setBolt("ADLStoreBolt", adlsBolt, 1)
      .globalGrouping("finalcount");
      
Si está familiarizado con el uso de HdfsBolt, observará que se trata de toda la configuración bastante estándar excepto la dirección URL. La dirección URL proporciona la ruta de acceso a la raíz de la tienda de lago de datos de Azure.

Puesto que escribir en el almacén de datos de lago usa HdfsBolt y es un cambio de dirección URL, debería poder tomar cualquier topología existente que escribe en HDFS o WASB con HdfsBolt y cambiar fácilmente para utilizar el almacén de lago de datos de Azure.

##<a name="build-and-package-the-topology"></a>Generar y empaquetar la topología

1. Descargar el proyecto de ejemplo de [https://github.com/Azure-Samples/hdinsight-storm-azure-data-lake-store](https://github.com/Azure-Samples/hdinsight-storm-azure-data-lake-store
) a su entorno de desarrollo.

2. Abrir la `StormToDataLake\src\main\java\com\microsoft\example\StormToDataLakeStore.java` en un editor y busque la línea que contiene `.withFsUrl("adl://MYDATALAKE.azuredatalakestore.net/")`. Cambiar __MYDATALAKE__ el nombre de la tienda de lago Azure datos que usó al crear el servidor HDInsight.

3. Un comando de símbolo del sistema, terminal o sesión de shell, cambie a la raíz del proyecto descargado y ejecute los comandos siguientes para generar y empaquetar la topología.

        mvn compile
        mvn package
    
    Una vez completada la compilación y empaquetado, habrá un nuevo directorio denominado `target`, que contiene un archivo llamado `StormToDataLakeStore-1.0-SNAPSHOT.jar`. Esta página contiene la topología compilada.

##<a name="deploy-and-run-on-linux-based-hdinsight"></a>Implementar y ejecutar en HDInsight de Linux

Si ha creado una tormenta de Linux en clúster HDInsight, use los siguientes pasos para implementar y ejecutar la topología.

1. Utilice el comando siguiente para copiar la topología al clúster HDInsight. Reemplazar __usuario__ con el nombre de usuario SSH que usó para crear el clúster. Reemplazar __NOMBREDECLÚSTER__ con el nombre del clúster.

        scp target\StormToDataLakeStore-1.0-SNAPSHOT.jar USER@CLUSTERNAME-ssh.azurehdinsight.net:StormToDataLakeStore-1.0-SNAPSHOT.jar
    
    Cuando se le pida, escriba la contraseña que utiliza al crear el usuario SSH para el clúster. Si utiliza una clave pública en lugar de una contraseña, tendrá que usar la `-i` parámetro para especificar la ruta de acceso a la clave privada correspondiente.
    
    > [AZURE.NOTE] Si está utilizando un cliente de Windows para el desarrollo, puede que no tenga una `scp` comando. Si es así, puede usar `pscp`, que está disponible en [http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).

2. Una vez completada la carga, use las siguientes acciones para conectarse al clúster HDInsight mediante SSH. Reemplazar __usuario__ con el nombre de usuario SSH que usó para crear el clúster. Reemplazar __NOMBREDECLÚSTER__ con el nombre del clúster.

        ssh USER@CLUSTERNAME-ssh.azurehdinsight.net

    Cuando se le pida, escriba la contraseña que utiliza al crear el usuario SSH para el clúster. Si utiliza una clave pública en lugar de una contraseña, tendrá que usar la `-i` parámetro para especificar la ruta de acceso a la clave privada correspondiente.
    
    > [AZURE.NOTE] Si está utilizando a un cliente de Windows para el desarrollo, siga la información en [conectarse a HDInsight basados en Linux con SSH de Windows](hdinsight-hadoop-linux-use-ssh-windows.md) para obtener información de utilizando al cliente masilla para conectarse al clúster.
    
3. Una vez conectado, utilice la siguiente para iniciar la topología:

        storm jar StormToDataLakeStore-1.0-SNAPSHOT.jar com.microsoft.example.StormToDataLakeStore datalakewriter
    
    Se iniciará la topología con un nombre descriptivo de `datalakewriter`.

##<a name="deploy-and-run-on-windows-based-hdinsight"></a>Implementar y ejecutar en HDInsight de Windows

1. Abra un navegador web y vaya a HTTPS://CLUSTERNAME.azurehdinsight.net, donde __NOMBREDECLÚSTER__ es el nombre de su clúster HDInsight. Cuando se le solicite, proporcione el nombre de usuario de administración (`admin`) y la contraseña que utiliza para esta cuenta cuando se creó el clúster.

2. Desde el panel de tormenta, seleccione __Examinar__ en la lista desplegable __Archivo Jar__ y luego seleccione el archivo StormToDataLakeStore-1.0-SNAPSHOT.jar desde el `target` directorio. Para las demás entradas en el formulario, use los siguientes valores:

    * Nombre de clase: com.microsoft.example.StormToDataLakeStore
    * Parámetros adicionales: datalakewriter
    
    ![imagen del panel de tormenta](./media/hdinsight-storm-write-data-lake-store/submit.png)

3. Seleccione el botón __Enviar__ para cargar e iniciar la topología. El campo de resultado debajo del botón __Enviar__ debería mostrar información similar al siguiente cuando se ha iniciado la topología:

        Process exit code: 0
        Currently running topologies:
        Topology_name        Status     Num_tasks  Num_workers  Uptime_secs
        -------------------------------------------------------------------
        datalakewriter       ACTIVE     68         8            10        

##<a name="view-output-data"></a>Ver los datos de salida

Existen varias formas de ver los datos. En esta sección se utiliza el Portal de Azure y la `hdfs` comando para ver los datos.

> [AZURE.NOTE] Debe permitir las topologías ejecutar varios minutos antes de comprobar los datos de salida, para que los datos se han sincronizado en varios archivos en el almacén de lago de datos de Azure.

* __Desde el [Portal de Azure](https://portal.azure.com)__: en el portal, seleccione el almacén de lago de datos de Azure que utiliza con HDInsight.

    > [AZURE.NOTE] Si no anclar el almacén de lago de datos al panel portal Azure, puede encontrar seleccionando __Examinar__ en la parte inferior de la lista de la izquierda, a continuación, __Almacén de datos de lago__, y por último la tienda.
    
    En los iconos en la parte superior de la tienda de lago de datos, seleccione __Explorador de datos__.
    
    ![icono de exploración de datos](./media/hdinsight-storm-write-data-lake-store/dataexplorer.png)
    
    A continuación, seleccione la carpeta __stormdata__ . Debe aparecer una lista de archivos de texto.
    
    ![archivos de texto](./media/hdinsight-storm-write-data-lake-store/stormoutput.png)
    
    Seleccione uno de los archivos para ver su contenido.

* __Desde el clúster__: si se ha conectado al clúster HDInsight mediante SSH (clúster de Linux) o escritorio remoto (clúster de Windows), puede usar los siguientes para ver los datos. Reemplace __DATALAKE__ con el nombre de la tienda de lago de datos

        hdfs dfs -cat adl://DATALAKE.azuredatalakestore.net/stormdata/*.txt

    Esto concatenar los archivos de texto almacenados en el directorio y mostrar información similar al siguiente:
    
        406000000
        407000000
        408000000
        409000000
        410000000
        411000000
        412000000
        413000000
        414000000
        415000000
        
##<a name="stop-the-topology"></a>Detener la topología

Topologías de tormenta se ejecutarán hasta que se detiene o se elimina el clúster. Para detener la topologías, utilice la siguiente información.

__Para HDInsight basados en Linux__:

Desde una sesión SSH al clúster, utilice el siguiente comando:

    storm kill datalakewriter

__Para HDInsight de Windows__:

1. Desde el panel de tormenta (https://CLUSTERNAME.azurehdinsight.net), seleccione el vínculo de la __Interfaz de usuario de tormenta__ en la parte superior de la página.

2. Una vez que se carga la interfaz de usuario de tormenta, seleccione el vínculo de __datalakewriter__ .

    ![vínculo a datalakewriter](./media/hdinsight-storm-write-data-lake-store/selecttopology.png)

3. En la sección __Topología de acciones__ , seleccione __Eliminar__ y, a continuación, seleccione Aceptar en el cuadro de diálogo que aparece.

    ![acciones de topología](./media/hdinsight-storm-write-data-lake-store/topologyactions.png)

## <a name="delete-your-cluster"></a>Eliminar el clúster

[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

##<a name="next-steps"></a>Pasos siguientes

Ahora que ha aprendido a usar tormenta para escribir en el almacén de lago de datos de Azure, descubra otros [ejemplos de tormenta para HDInsight](hdinsight-storm-example-topology.md).
