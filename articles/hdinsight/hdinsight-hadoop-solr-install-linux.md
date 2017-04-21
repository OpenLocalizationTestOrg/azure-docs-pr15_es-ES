<properties
    pageTitle="Use la acción de secuencia de comandos para instalar Solr en HDInsight de Linux | Microsoft Azure"
    description="Obtenga información sobre cómo instalar Solr en clústeres basados en Linux HDInsight Hadoop usar acciones de Script."
    services="hdinsight"
    documentationCenter=""
    authors="Blackmist"
    manager="jhubbard"
    editor="cgronlun"
    tags="azure-portal"/>

<tags
    ms.service="hdinsight"
    ms.workload="big-data"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/03/2016"
    ms.author="larryfr"/>

# <a name="install-and-use-solr-on-hdinsight-hadoop-clusters"></a>Instalar y usar Solr en clústeres HDInsight Hadoop

En este tema, obtendrá información sobre cómo instalar Solr en HDInsight de Azure mediante secuencia de comandos de acción. Solr es una plataforma de búsqueda eficaces y proporciona funciones de búsqueda de nivel empresarial en datos administrados por Hadoop. Una vez haya instalado Solr en clúster HDInsight, aprenderá cómo buscar datos mediante Solr.

> [AZURE.NOTE] Los pasos de este documento requieren un clúster HDInsight basados en Linux. Para obtener información sobre cómo usar Solr con un clúster basado en Windows, consulte [instalar y usar Solr en clústeres HDinsight Hadoop (Windows)](hdinsight-hadoop-solr-install.md)

La secuencia de comandos usado en este tema crea un clúster de Solr con una configuración específica. Si desea configurar el clúster de Solr con diferentes colecciones, shards, esquemas, réplicas, etc., debe modificar la secuencia de comandos y archivos binarios Solr en consecuencia.

## <a name="whatis"></a>¿Qué es Solr?

[Apache Solr](http://lucene.apache.org/solr/features.html) es una plataforma de búsqueda empresarial que permite la búsqueda de texto completo eficaz de los datos. Mientras Hadoop permite almacenar y administrar grandes cantidades de datos, Apache Solr proporciona la capacidad de búsqueda para recuperar rápidamente los datos. Este tema proporciona instrucciones sobre cómo personalizar un clúster HDInsight para instalar Solr.

> [AZURE.WARNING] Componentes proporcionados con el clúster HDInsight son totalmente compatibles y Microsoft Support le ayudará a aislar y resolver los problemas relacionados con estos componentes.
>
> Componentes personalizados, como Solr, reciban soporte comercialmente razonable para ayudar a solucionar el problema. Esto puede ocasionar resolver el problema o preguntar si desea integrarse canales disponibles para las tecnologías de código abierto donde se encuentra la amplia experiencia para que la tecnología. Por ejemplo, hay muchos sitios de comunidad que se pueden usar como: [foro de MSDN para HDInsight](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=hdinsight), [http://stackoverflow.com](http://stackoverflow.com). También proyectos Apache tienen sitios de proyecto en [http://apache.org](http://apache.org), por ejemplo: [Hadoop](http://hadoop.apache.org/).

## <a name="what-the-script-does"></a>¿Qué significa la secuencia de comandos

Esta secuencia de comandos realiza los cambios siguientes en el clúster HDInsight:

* Instala Solr en`/usr/hdp/current/solr`
* Crea un nuevo usuario, __solrusr__, que se usa para ejecutar el servicio de Solr
* Establece __solruser__ como propietario del`/usr/hdp/current/solr`
* Agrega una configuración de [advenedizo](http://upstart.ubuntu.com/) que iniciará Solr si se reinicia un nodo de clúster. Solr se inicia automáticamente en los nodos de clúster después de la instalación

## <a name="install"></a>Instalar Solr usar acciones de Script

Una secuencia de comandos de ejemplo para instalar Solr en un clúster de HDInsight está disponible en la siguiente ubicación.

    https://hdiconfigactions.blob.core.windows.net/linuxsolrconfigactionv01/solr-installer-v01.sh

Esta sección proporciona instrucciones sobre cómo utilizar la secuencia de comandos para crear un nuevo clúster mediante el portal de Azure. 

> [AZURE.NOTE] PowerShell Azure, CLI Azure, el SDK de .NET HDInsight o administrador de recursos de Azure plantillas también sirve para aplicar acciones de script. También puede aplicar acciones de secuencia de comandos para clústeres ya en ejecución. Para obtener más información, vea [Personalizar HDInsight clústeres con acciones de Script](hdinsight-hadoop-customize-cluster-linux.md).

1. Iniciar el aprovisionamiento de un clúster con los pasos en [clústeres basados en Linux aprovisionar HDInsight](hdinsight-hadoop-create-linux-clusters-portal.md), pero no completa de aprovisionamiento.

2. En el módulo **Configuración opcional** , seleccione **Acciones de secuencias de comandos**y proporcione la siguiente información:

    * __Nombre__: escriba un nombre descriptivo para la acción de secuencia de comandos.
    * __URI de secuencia de comandos__: https://hdiconfigactions.blob.core.windows.net/linuxsolrconfigactionv01/solr-installer-v01.sh
    * __Cabeza__: Active esta opción
    * __Trabajo__: Active esta opción
    * __ZOOKEEPER__: Active esta opción para instalar en el nodo Zookeeper
    * __Parámetros__: deje este campo en blanco

3. En la parte inferior de las **Acciones de secuencias de comandos**, use el botón **Seleccionar** para guardar la configuración. Por último, utilice el botón **Seleccionar** en la parte inferior del módulo **Opcional de configuración** para guardar la información de configuración opcional.

4. Continúe el clúster de aprovisionamiento como se describe en [clústeres basados en Linux aprovisionar HDInsight](hdinsight-hadoop-create-linux-clusters-portal.md).

## <a name="usesolr"></a>¿Cómo se puede usar Solr en HDInsight?

### <a name="indexing-data"></a>Datos de indexación

Debe comenzar con la indización Solr con algunos archivos de datos. A continuación, puede usar Solr para ejecutar consultas de búsqueda en los datos indizados. Realice los siguientes pasos para agregar algunos datos de ejemplo a Solr y, a continuación, la consulta:

1. Conectarse al clúster HDInsight mediante SSH:

        ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net

    Para obtener más información sobre cómo utilizar SSH con HDInsight, consulte lo siguiente:

    * [Utilizar SSH con basados en Linux Hadoop en HDInsight de Linux, Unix o OS X](hdinsight-hadoop-linux-use-ssh-unix.md)

    * [Utilizar SSH con basados en Linux Hadoop en HDInsight de Windows.](hdinsight-hadoop-linux-use-ssh-windows.md)

    > [AZURE.IMPORTANT] Pasos más adelante en este documento de poner usar de un túnel SSL para conectarse a la interfaz de usuario de web Solr. Para poder seguir estos pasos, debe establecer un túnel SSL y, a continuación, configure el explorador para usarlo.
    >
    > Para obtener más información, consulte [Usar SSH túnel para tener acceso a la interfaz de usuario de web Ambari, ResourceManager, JobHistory, NameNode, Oozie y otra de la interfaz de usuario de web](hdinsight-linux-ambari-ssh-tunnel.md)

2. Use los comandos siguientes tener datos de ejemplo de Solr índice:

        cd /usr/hdp/current/solr/example/exampledocs
        java -jar post.jar solr.xml monitor.xml

    Verá el siguiente resultado en la consola:

        POSTing file solr.xml
        POSTing file monitor.xml
        2 files indexed.
        COMMITting Solr index changes to http://localhost:8983/solr/update..
        Time spent: 0:00:01.624

    La utilidad post.jar indiza Solr con documentos de dos muestras, **solr.xml** y **monitor.xml**. Se almacenarán en __collection1__ dentro de Solr.

3. Utilice la siguiente a la API de REST expuestas por Solr de la consulta:

        curl "http://localhost:8983/solr/collection1/select?q=*%3A*&wt=json&indent=true"

    Se emite una consulta en __collection1__ para todos los documentos que coincidan con __ \*:\* __ (codifica como \*3A %\* en la cadena de consulta) y que se devuelve la respuesta como JSON. La respuesta debe ser similar al siguiente:

            "response": {
                "numFound": 2,
                "start": 0,
                "maxScore": 1,
                "docs": [
                  {
                    "id": "SOLR1000",
                    "name": "Solr, the Enterprise Search Server",
                    "manu": "Apache Software Foundation",
                    "cat": [
                      "software",
                      "search"
                    ],
                    "features": [
                      "Advanced Full-Text Search Capabilities using Lucene",
                      "Optimized for High Volume Web Traffic",
                      "Standards Based Open Interfaces - XML and HTTP",
                      "Comprehensive HTML Administration Interfaces",
                      "Scalability - Efficient Replication to other Solr Search Servers",
                      "Flexible and Adaptable with XML configuration and Schema",
                      "Good unicode support: héllo (hello with an accent over the e)"
                    ],
                    "price": 0,
                    "price_c": "0,USD",
                    "popularity": 10,
                    "inStock": true,
                    "incubationdate_dt": "2006-01-17T00:00:00Z",
                    "_version_": 1486960636996878300
                  },
                  {
                    "id": "3007WFP",
                    "name": "Dell Widescreen UltraSharp 3007WFP",
                    "manu": "Dell, Inc.",
                    "manu_id_s": "dell",
                    "cat": [
                      "electronics and computer1"
                    ],
                    "features": [
                      "30\" TFT active matrix LCD, 2560 x 1600, .25mm dot pitch, 700:1 contrast"
                    ],
                    "includes": "USB cable",
                    "weight": 401.6,
                    "price": 2199,
                    "price_c": "2199,USD",
                    "popularity": 6,
                    "inStock": true,
                    "store": "43.17614,-90.57341",
                    "_version_": 1486960637584081000
                  }
                ]
              }

### <a name="using-the-solr-dashboard"></a>Con el panel de Solr

El panel de Solr es un sitio web de interfaz de usuario que le permite trabajar con Solr a través de su explorador web. El panel de Solr no se expone directamente en Internet desde el clúster HDInsight, pero se debe acceder a ellas mediante un túnel SSH. Para obtener más información sobre el uso de un túnel SSH, consulte [Usar SSH túnel para tener acceso a la interfaz de usuario de web Ambari, ResourceManager, JobHistory, NameNode, Oozie y otra de la interfaz de usuario de web](hdinsight-linux-ambari-ssh-tunnel.md)

Una vez que haya establecido un túnel SSH, realice los pasos siguientes para utilizar el panel de Solr:

1. Determinar el nombre de host para el headnode principal:

    1. Usar SSH para conectarse al clúster en puerto 22. Por ejemplo, `ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net` donde __USERNAME__ es su nombre de usuario SSH y __NOMBREDECLÚSTER__ es el nombre del clúster.

        Para obtener más información sobre el uso de SSH, consulte los siguientes documentos:

        * [Utilizar SSH con HDInsight basados en Linux desde un cliente Linux, Unix o Mac OS X](hdinsight-hadoop-linux-use-ssh-unix.md)

        * [Utilizar SSH con HDInsight basados en Linux desde un cliente de Windows](hdinsight-hadoop-linux-use-ssh-windows.md)
    
    3. Utilice el comando siguiente para obtener el nombre de host completo:

            hostname -f

        Devolverá un nombre parecido al siguiente:

            hn0-myhdi-nfebtpfdv1nubcidphpap2eq2b.ex.internal.cloudapp.net
    
        Este es el nombre de host que debe usarse en los siguientes pasos.
    
1. En el explorador, conéctese a __solr/http://HOSTNAME:8983 / #/__, donde __nombre de host__ es el nombre determinado en los pasos anteriores. 

    Enrutar la solicitud a través del túnel SSH el nodo principal para el clúster de HDInsight. Verá una página similar al siguiente:

    ![Imagen del panel de Solr](./media/hdinsight-hadoop-solr-install-linux/solrdashboard.png)

2. En el panel izquierdo, use el **Selector de Core** desplegable para seleccionar **collection1**. Varias entradas aparecen debajo de __collection1__.

3. En las entradas debajo __collection1__, seleccione __consulta__. Para rellenar la página de búsqueda, use los siguientes valores:

    * En el cuadro de texto **preguntas** , escriba ** \*:**\*. Esto devolverá todos los documentos que estén indizados en Solr. Si desea buscar una cadena dentro de los documentos específica, puede introducir aquí esa cadena.

    * En el cuadro de texto **wt** , seleccione el formato de salida. El valor predeterminado es **json**.

    Por último, seleccione el botón **Ejecutar consulta** en la parte inferior de la y patentes de búsqueda.

    ![Usar la acción de secuencia de comandos para personalizar un clúster](./media/hdinsight-hadoop-solr-install-linux/hdi-solr-dashboard-query.png)

    El resultado devuelve a los dos documentos que se usan para la indización de Solr. El resultado es similar a la siguiente:

            "response": {
                "numFound": 2,
                "start": 0,
                "maxScore": 1,
                "docs": [
                  {
                    "id": "SOLR1000",
                    "name": "Solr, the Enterprise Search Server",
                    "manu": "Apache Software Foundation",
                    "cat": [
                      "software",
                      "search"
                    ],
                    "features": [
                      "Advanced Full-Text Search Capabilities using Lucene",
                      "Optimized for High Volume Web Traffic",
                      "Standards Based Open Interfaces - XML and HTTP",
                      "Comprehensive HTML Administration Interfaces",
                      "Scalability - Efficient Replication to other Solr Search Servers",
                      "Flexible and Adaptable with XML configuration and Schema",
                      "Good unicode support: héllo (hello with an accent over the e)"
                    ],
                    "price": 0,
                    "price_c": "0,USD",
                    "popularity": 10,
                    "inStock": true,
                    "incubationdate_dt": "2006-01-17T00:00:00Z",
                    "_version_": 1486960636996878300
                  },
                  {
                    "id": "3007WFP",
                    "name": "Dell Widescreen UltraSharp 3007WFP",
                    "manu": "Dell, Inc.",
                    "manu_id_s": "dell",
                    "cat": [
                      "electronics and computer1"
                    ],
                    "features": [
                      "30\" TFT active matrix LCD, 2560 x 1600, .25mm dot pitch, 700:1 contrast"
                    ],
                    "includes": "USB cable",
                    "weight": 401.6,
                    "price": 2199,
                    "price_c": "2199,USD",
                    "popularity": 6,
                    "inStock": true,
                    "store": "43.17614,-90.57341",
                    "_version_": 1486960637584081000
                  }
                ]
              }

### <a name="starting-and-stopping-solr"></a>Iniciar y detener Solr

Si necesita iniciar Solar o detener manualmente, use los comandos siguientes:

    sudo stop solr

    sudo start solr

## <a name="backup-indexed-data"></a>Copia de seguridad de datos indizados

Una buena práctica, debe hacer los datos indizados desde los nodos de clúster de Solr en el almacenamiento de blobs de Windows Azure. Realice los pasos siguientes para ello:

1. Conectar con el clúster mediante SSH y luego use el comando siguiente para obtener el nombre de host para el nodo principal:

        hostname -f
        
2. Use las siguientes acciones para crear una instantánea de los datos indizados. Reemplazar __HOSTNAME__ con el nombre devuelto por el comando anterior:

        curl http://HOSTNAME:8983/solr/replication?command=backup

    Debería ver una respuesta similar a esta:

        <?xml version="1.0" encoding="UTF-8"?>
        <response>
          <lst name="responseHeader">
            <int name="status">0</int>
            <int name="QTime">9</int>
          </lst>
          <str name="status">OK</str>
        </response>

2. A continuación, cambie al directorio __/usr/hdp/current/solr/example/solr__. Habrá un subdirectorio aquí para cada colección. Cada directorio de la colección contiene un directorio de __datos__ , que es donde la instantánea de esa colección está ubicado.

    Por ejemplo, si utiliza los pasos anteriores para indizar los documentos de muestra, el directorio de __/usr/hdp/current/solr/example/solr/collection1/data__ ahora debe contener un directorio denominado __instantánea. ###__ donde los números de son la fecha y hora de la instantánea.

3. Crear un archivo comprimido de la carpeta de instantánea con un comando similar al siguiente:

        tar -zcf snapshot.20150806185338855.tgz snapshot.20150806185338855

    Se creará un nuevo archivo denominado __snapshot.20150806185338855.tgz__, que contiene el contenido del directorio __snapshot.20150806185338855__ .

3. A continuación, puede almacenar el archivo al almacenamiento primario del clúster mediante el siguiente comando:

    hadoop fs - copyFromLocal snapshot.20150806185338855.tgz/ejemplo y datos

    > [AZURE.NOTE] Desea crear un directorio dedicado para almacenar instantáneas Solr. Por ejemplo, `hadoop fs -mkdir /solrbackup`.

Para obtener más información sobre cómo trabajar con Solr copia de seguridad y restauración, vea [realizar y restaurar copias de seguridad de SolrCores](https://cwiki.apache.org/confluence/display/solr/Making+and+Restoring+Backups+of+SolrCores).


## <a name="see-also"></a>Vea también

- [Instalar y usar clústeres matiz en HDInsight](hdinsight-hadoop-hue-linux.md). El matiz es una interfaz de usuario que hace que sea fácil crear, ejecutar y guardar trabajos cerdo y subárbol, así como examinar el almacenamiento predeterminado para su HDInsight clúster de web.

- [Instalar R en clústeres HDInsight][hdinsight-install-r]. Use la personalización de clúster para instalar R en clústeres HDInsight Hadoop. R es un idioma de código abierto y el entorno para las estadísticas. Proporciona cientos de funciones estadísticas integradas y su propio lenguaje de programación que combina aspectos de la programación funcional y orientado a objetos. También proporciona capacidades gráficas extensas.

- [Instalar Giraph en clústeres HDInsight](hdinsight-hadoop-giraph-install-linux.md). Use la personalización de clúster para instalar Giraph en clústeres HDInsight Hadoop. Giraph permite realizar graph procesar mediante Hadoop y se pueden usar con HDInsight de Azure.

- [Instalar matiz en clústeres HDInsight](hdinsight-hadoop-hue-linux.md). Use la personalización de clúster para instalar matiz en clústeres HDInsight Hadoop. El matiz es un conjunto de aplicaciones Web que se utilizan para interactuar con un clúster de Hadoop.



[hdinsight-install-r]: hdinsight-hadoop-r-scripts-linux.md
[hdinsight-cluster-customize]: hdinsight-hadoop-customize-cluster-linux.md
