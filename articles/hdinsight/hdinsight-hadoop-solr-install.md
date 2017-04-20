<properties
    pageTitle="Usar la acción de secuencia de comandos para instalar Solr en clúster de Hadoop | Microsoft Azure"
    description="Obtenga información sobre cómo personalizar clúster HDInsight con Solr con la acción de secuencia de comandos."
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
    ms.date="02/05/2016"
    ms.author="nitinme"/>

# <a name="install-and-use-solr-on-hdinsight-hadoop-clusters"></a>Instalar y usar Solr en clústeres HDInsight Hadoop

Obtenga información sobre cómo personalizar clúster en función de HDInsight de Windows con Solr con la acción de secuencia de comandos y cómo usar Solr para buscar datos. Para obtener información sobre cómo usar Solr con un clúster de Linux, consulte [instalar y usar Solr en HDinsight Hadoop clústeres (Linux)](hdinsight-hadoop-solr-install-linux.md).
 
Puede instalar Solr en cualquier tipo de clúster (Hadoop, tormenta, HBase, motor) en HDInsight de Azure mediante *Secuencia de comandos de acción*. Una secuencia de comandos de ejemplo para instalar Solr en un clúster de HDInsight está disponible desde un blob de solo lectura almacenamiento de Azure en [https://hdiconfigactions.blob.core.windows.net/solrconfigactionv01/solr-installer-v01.ps1](https://hdiconfigactions.blob.core.windows.net/solrconfigactionv01/solr-installer-v01.ps1). 

La secuencia de comandos sólo funciona con versión de clúster HDInsight 3.1. Para obtener más información sobre versiones de clúster HDInsight, consulte [versiones de clúster HDInsight](hdinsight-component-versioning.md).

La secuencia de comandos usado en este tema crea un clúster de Solr basado en Windows con una configuración específica. Si desea configurar el clúster de Solr con diferentes colecciones, shards, esquemas, réplicas, etc., debe modificar la secuencia de comandos y archivos binarios Solr en consecuencia.

**Artículos relacionados**

- [Instalar y usar Solr en HDinsight Hadoop clústeres (Linux)](hdinsight-hadoop-solr-install-linux.md)
- [Crear Hadoop clústeres en HDInsight](hdinsight-provision-clusters.md): información general sobre la creación de clústeres HDInsight.
- [Personalizar clúster HDInsight con la acción de secuencia de comandos][hdinsight-cluster-customize]: información general sobre la personalización de clústeres HDInsight con la acción de secuencia de comandos.
- [Acción de secuencia de comandos con el desarrollo de secuencias de comandos para HDInsight](hdinsight-hadoop-script-actions.md).


## <a name="what-is-solr"></a>¿Qué es Solr?

<a href="http://lucene.apache.org/solr/features.html" target="_blank">Apache Solr</a> es una plataforma de búsqueda empresarial que permite la búsqueda de texto completo eficaz de los datos. Mientras Hadoop permite almacenar y administrar grandes cantidades de datos, Apache Solr proporciona la capacidad de búsqueda para recuperar rápidamente los datos. 

## <a name="install-solr-using-portal"></a>Instalar Solr con portal

1. Empezar a crear un clúster mediante la opción **Crear personalizado** , como se describe en [Hadoop crear clústeres en HDInsight](hdinsight-provision-clusters.md#portal).
2. En la página de **Acciones de secuencia de comandos** del asistente, haga clic en **Agregar acción de secuencia de comandos** para proporcionar información detallada sobre la acción de secuencia de comandos, tal como se muestra a continuación:

    ![Acción de secuencia de comandos de uso para personalizar un clúster] (./media/hdinsight-hadoop-solr-install/hdi-script-action-solr.png "Acción de secuencia de comandos de uso para personalizar un clúster")

    <table border='1'>
        <tr><th>(Propiedad)</th><th>Valor</th></tr>
        <tr><td>Nombre</td>
            <td>Especifique un nombre para la acción de secuencia de comandos. Por ejemplo, <b>Instalar Solr</b>.</td></tr>
        <tr><td>URI de secuencia de comandos</td>
            <td>Especificar el identificador uniforme de recursos (URI) a la secuencia de comandos que se invoca para personalizar el clúster. Por ejemplo, <i>https://hdiconfigactions.blob.core.windows.net/solrconfigactionv01/solr-installer-v01.ps1</i></td></tr>
        <tr><td>Tipo de nodo</td>
            <td>Especifique los nodos en que se ejecuta la secuencia de comandos de personalización. Puede elegir <b>todos los nodos</b>, <b>solo los nodos de cabeza</b>o <b>solo los nodos de trabajo</b>.
        <tr><td>Parámetros</td>
            <td>Especificar los parámetros, si es necesario la secuencia de comandos. La secuencia de comandos para instalar Solr no requiere parámetros, por lo que puede dejar en blanco.</td></tr>
    </table>

    Puede agregar más de una acción de secuencia de comandos para instalar varios componentes en el clúster. Después de haber agregado las secuencias de comandos, haga clic en la marca de verificación para empezar a crear el clúster.


## <a name="use-solr"></a>Usar Solr

Debe comenzar con la indización Solr con algunos archivos de datos. A continuación, puede usar Solr para ejecutar consultas de búsqueda en los datos indizados. Realice los pasos siguientes para utilizar Solr en un clúster de HDInsight:

1. **Protocolo de escritorio remoto (RDP) de uso remoto en el clúster HDInsight con Solr instalado**. Desde el portal de Azure, habilite Escritorio remoto para el clúster creado con Solr instalado y remoto, a continuación, en el clúster. Para obtener instrucciones, vea [conectarse a clústeres HDInsight con RDP](hdinsight-administer-use-management-portal.md#connect-to-clusters-using-rdp).

2. **Índice Solr al cargar archivos de datos**. Cuando se indiza Solr, coloque los documentos en los que se debe buscar en. Para indizar Solr, use RDP remoto en el clúster, navegue hasta el escritorio, abra la línea de comandos de Hadoop y vaya a **C:\apps\dist\solr-4.7.2\example\exampledocs**. Ejecute el siguiente comando:

        java -jar post.jar solr.xml monitor.xml

    Verá el siguiente resultado en la consola:

        POSTing file solr.xml
        POSTing file monitor.xml
        2 files indexed.
        COMMITting Solr index changes to http://localhost:8983/solr/update..
        Time spent: 0:00:01.624

    La utilidad post.jar indiza Solr con documentos de dos muestras, **solr.xml** y **monitor.xml**. La utilidad post.jar y los documentos de muestra están disponibles con la instalación de Solr.

3. **Usar el panel de Solr Buscar dentro de los documentos indizados**. En la sesión RDP al clúster HDInsight, abra Internet Explorer e inicio en el panel de Solr **solr/http://headnodehost:8983 / #/**. En el panel izquierdo, en la lista desplegable **Selector principales** , seleccione **collection1**y dentro de ella, haga clic en **consulta**. Por ejemplo, para seleccionar y devolver a todos los documentos en Solr, proporciona los siguientes valores:

    * En el cuadro de texto **preguntas** , escriba ** \*:**\*. Esto devolverá todos los documentos que estén indizados en Solr. Si desea buscar una cadena dentro de los documentos específica, puede introducir aquí esa cadena.
    
    * En el cuadro de texto **wt** , seleccione el formato de salida. El valor predeterminado es **json**. Haga clic en **ejecutar la consulta**.

    ![Acción de secuencia de comandos de uso para personalizar un clúster] (./media/hdinsight-hadoop-solr-install/hdi-solr-dashboard-query.png "Ejecutar una consulta en el panel de Solr")
    
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


4. **Recomendado: copia de seguridad los datos indizados Solr al almacenamiento de blobs de Windows Azure asociado con el clúster de HDInsight**. Una buena práctica, debe hacer los datos indizados desde los nodos de clúster de Solr en el almacenamiento de blobs de Windows Azure. Realice los pasos siguientes para ello:

    1. Desde la sesión de RDP, abra Internet Explorer y apunte a la siguiente URL:

            http://localhost:8983/solr/replication?command=backup

        Debería ver una respuesta similar a esta:

            <?xml version="1.0" encoding="UTF-8"?>
            <response>
              <lst name="responseHeader">
                <int name="status">0</int>
                <int name="QTime">9</int>
              </lst>
              <str name="status">OK</str>
            </response>

    2. En la sesión remota, vaya a {SOLR_HOME}\{colección} \data. Para el clúster creado a través de la secuencia de comandos, debe ser **C:\apps\dist\solr-4.7.2\example\solr\collection1\data**. En esta ubicación, verá una carpeta de instantánea creada con un nombre similar a * *instantánea.* marca de tiempo***.

    3. Comprima la carpeta instantánea y cárguelo en el almacenamiento de blobs de Windows Azure. Desde la línea de comandos Hadoop, desplácese a la ubicación de la carpeta instantánea mediante el comando siguiente:

              hadoop fs -CopyFromLocal snapshot._timestamp_.zip /example/data

        Este comando copia la instantánea /example/data/bajo el contenedor dentro de la cuenta de almacenamiento asociado con el clúster predeterminada.

## <a name="install-solr-using-aure-powershell"></a>Instalar Solr con Aure PowerShell

Consulte [clústeres personalizar HDInsight con la acción de secuencia de comandos](hdinsight-hadoop-customize-cluster.md#call_scripts_using_powershell).  El ejemplo muestra cómo instalar el motor con PowerShell de Azure. Debe personalizar la secuencia de comandos para usar [https://hdiconfigactions.blob.core.windows.net/solrconfigactionv01/solr-installer-v01.ps1](https://hdiconfigactions.blob.core.windows.net/solrconfigactionv01/solr-installer-v01.ps1).

## <a name="install-solr-using-net-sdk"></a>Instalar Solr mediante el SDK de .NET

Consulte [clústeres personalizar HDInsight con la acción de secuencia de comandos](hdinsight-hadoop-customize-cluster.md#call_scripts_using_azure_powershell). El ejemplo muestra cómo instalar motor mediante .NET SDK. Debe personalizar la secuencia de comandos para usar [https://hdiconfigactions.blob.core.windows.net/solrconfigactionv01/solr-installer-v01.ps1](https://hdiconfigactions.blob.core.windows.net/solrconfigactionv01/solr-installer-v01.ps1).



## <a name="see-also"></a>Vea también

- [Instalar y usar Solr en HDinsight Hadoop clústeres (Linux)](hdinsight-hadoop-solr-install-linux.md)
- [Crear Hadoop clústeres en HDInsight](hdinsight-provision-clusters.md): información general sobre la creación de clústeres HDInsight.
- [Personalizar clúster HDInsight con la acción de secuencia de comandos][hdinsight-cluster-customize]: información general sobre la personalización de clústeres HDInsight con la acción de secuencia de comandos.
- [Acción de secuencia de comandos con el desarrollo de secuencias de comandos para HDInsight](hdinsight-hadoop-script-actions.md).
- [Instalar y usar el motor en clústeres HDInsight][hdinsight-install-spark]: ejemplo de Script acción acerca de cómo instalar el motor.
- [Instalar R en clústeres HDInsight][hdinsight-install-r]: ejemplo de acción de secuencia de comandos sobre la instalación de R.
- [Instalar Giraph en HDInsight clústeres](hdinsight-hadoop-giraph-install.md): ejemplo de acción de secuencia de comandos sobre la instalación de Giraph.


[powershell-install-configure]: powershell-install-configure.md
[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-install-r]: hdinsight-hadoop-r-scripts.md
[hdinsight-install-spark]: hdinsight-hadoop-spark-install.md
[hdinsight-cluster-customize]: hdinsight-hadoop-customize-cluster.md
