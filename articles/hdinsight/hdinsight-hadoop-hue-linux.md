<properties
    pageTitle="Usar matiz con Hadoop en clústeres HDInsight Linux | Microsoft Azure"
    description="Obtenga información sobre cómo instalar y usar matiz con clústeres Hadoop en HDInsight Linux."
    services="hdinsight"
    documentationCenter=""
    authors="nitinme"
    manager="jhubbard"
    editor="cgronlun"/>

<tags 
    ms.service="hdinsight" 
    ms.workload="big-data" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/13/2016" 
    ms.author="nitinme"/>

# <a name="install-and-use-hue-on-hdinsight-hadoop-clusters"></a>Instalar y usar matiz en clústeres HDInsight Hadoop

Obtenga información sobre cómo instalar matiz en clústeres HDInsight Linux y utilice túnel para enrutar las solicitudes de matiz.

## <a name="what-is-hue"></a>¿Qué es matiz?

El matiz es un conjunto de aplicaciones Web que se utilizan para interactuar con un clúster de Hadoop. Puede usar el tono para examinar el almacenamiento asociado a un clúster de Hadoop (WASB, en el caso de clústeres de HDInsight), ejecute trabajos de sección y secuencias de comandos de cerdo, etcetera. Los siguientes componentes están disponibles con las instalaciones de matiz en un clúster de HDInsight Hadoop.

* Editor de cera subárbol
* Cerdo
* Administrador de Metastore
* Oozie
* FileBrowser (que se comunica con el contenedor WASB predeterminado)
* Explorador de trabajo

> [AZURE.WARNING] Componentes proporcionados con el clúster HDInsight son totalmente compatibles y Microsoft Support le ayudará a aislar y resolver los problemas relacionados con estos componentes.
>
> Componentes personalizados reciban soporte comercialmente razonable para ayudar a solucionar el problema. Esto puede ocasionar resolver el problema o preguntar si desea integrarse canales disponibles para las tecnologías de código abierto donde se encuentra la amplia experiencia para que la tecnología. Por ejemplo, hay muchos sitios de comunidad que se pueden usar como: [foro de MSDN para HDInsight](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=hdinsight), [http://stackoverflow.com](http://stackoverflow.com). También proyectos Apache tienen sitios de proyecto en [http://apache.org](http://apache.org), por ejemplo: [Hadoop](http://hadoop.apache.org/).

## <a name="install-hue-using-script-actions"></a>Instalar matiz usar acciones de Script

La siguiente acción de secuencia de comandos puede usarse para instalar matiz en un clúster de HDInsight basados en Linux.
https://hdiconfigactions.BLOB.Core.Windows.NET/linuxhueconfigactionv02/Install-Hue-uber-v02.sh
    
Esta sección proporciona instrucciones sobre cómo usar la secuencia de comandos al hacer el aprovisionamiento de clúster con el Portal de Azure. 

> [AZURE.NOTE] PowerShell Azure, CLI Azure, el SDK de .NET HDInsight o administrador de recursos de Azure plantillas también sirve para aplicar acciones de script. También puede aplicar acciones de secuencia de comandos para clústeres ya en ejecución. Para obtener más información, vea [Personalizar HDInsight clústeres con acciones de Script](hdinsight-hadoop-customize-cluster-linux.md).

1. Iniciar el aprovisionamiento de un clúster con los pasos en [clústeres HDInsight de aprovisionamiento en Linux](hdinsight-hadoop-provision-linux-clusters.md#portal), pero no se completan aprovisionamiento.

    > [AZURE.NOTE] Para instalar matiz en clústeres HDInsight, el tamaño de headnode recomendado es al menos A4 (8 núcleos, 14 GB de memoria).

2. En el módulo **Configuración opcional** , seleccione **Acciones de secuencias de comandos**y como se muestra a continuación, complete la información:

    ![Proporcionar parámetros de acción de secuencia de comandos de tono] (./media/hdinsight-hadoop-hue-linux/hue_script_action.png "Proporcionar parámetros de acción de secuencia de comandos de tono")

    * __Nombre__: escriba un nombre descriptivo para la acción de secuencia de comandos.
    * __URI de secuencia de comandos__: https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv02/install-hue-uber-v02.sh
    * __Cabeza__: Active esta opción
    * __Trabajo__: deje el campo vacío.
    * __ZOOKEEPER__: deje el campo vacío.
    * __Parámetros__: deje el campo vacío.

3. En la parte inferior de las **Acciones de secuencias de comandos**, use el botón **Seleccionar** para guardar la configuración. Por último, utilice el botón **Seleccionar** en la parte inferior del módulo **Opcional de configuración** para guardar la información de configuración opcional.

4. Continúe el clúster de aprovisionamiento, como se describe en [clústeres HDInsight de aprovisionamiento en Linux](hdinsight-hadoop-provision-linux-clusters.md#portal).

## <a name="use-hue-with-hdinsight-clusters"></a>Usar matiz con clústeres HDInsight

Túnel SSH es la única manera de acceder matiz en el clúster cuando se está ejecutando. Túnel mediante SSH permite que el tráfico ir directamente a la headnode del clúster donde se ejecuta el matiz. Cuando haya finalizado el clúster de aprovisionamiento, realice los siguientes pasos para usar matiz en un clúster de HDInsight Linux.

1. Use la información de [Utilizar SSH túnel para tener acceso a Ambari web UI, ResourceManager, JobHistory, NameNode, Oozie y otra de la interfaz de usuario de web](hdinsight-linux-ambari-ssh-tunnel.md) para crear un túnel SSH desde el sistema de cliente a clúster HDInsight y, a continuación, configure el explorador Web para usar el túnel SSH como un servidor proxy.

2. Una vez que haya creado un túnel SSH y configurado su explorador para tráfico de proxy a través de él, debe buscar el nombre de host del nodo principal principal. Puede hacer esto mediante una conexión con el clúster mediante SSH en puerto 22. Por ejemplo, `ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net` donde __USERNAME__ es su nombre de usuario SSH y __NOMBREDECLÚSTER__ es el nombre del clúster.

    Para obtener más información sobre el uso de SSH, consulte los siguientes documentos:

    * [Utilizar SSH con HDInsight basados en Linux desde un cliente Linux, Unix o Mac OS X](hdinsight-hadoop-linux-use-ssh-unix.md)
    * [Utilizar SSH con HDInsight basados en Linux desde un cliente de Windows](hdinsight-hadoop-linux-use-ssh-windows.md)

3. Una vez conectado, use el comando siguiente para obtener el nombre de dominio completo de la headnode principal:

        hostname -f

    Devolverá un nombre parecido al siguiente:

        hn0-myhdi-nfebtpfdv1nubcidphpap2eq2b.ex.internal.cloudapp.net
    
    Esto es el nombre de host de la headnode principal donde se encuentra el sitio Web de tono.

2. Use el explorador para abrir el portal de matiz en http://HOSTNAME:8888. Reemplace el nombre de host con el nombre obtenido en el paso anterior.

    > [AZURE.NOTE] Cuando inicie sesión por primera vez, le pedirá que cree una cuenta para iniciar sesión en el portal de tono. Las credenciales que especifique aquí se limitarán al portal y no están relacionadas con el administrador o SSH credenciales de usuario que especificó al aprovisionar el clúster.

    ![Inicie sesión en el portal de tono] (./media/hdinsight-hadoop-hue-linux/HDI.Hue.Portal.Login.png "Especificar las credenciales para el portal de tono")

### <a name="run-a-hive-query"></a>Ejecutar una consulta de sección

1. Desde el portal de matiz, haga clic en **Editores de consulta**y, a continuación, haga clic en la **sección** para abrir el editor de sección.

    ![Use la sección] (./media/hdinsight-hadoop-hue-linux/HDI.Hue.Portal.Hive.png "Use la sección")

2. En la ficha **Ayuda** , en **base de datos**, debería ver **hivesampletable**. Se trata de una tabla de ejemplo que se incluye con todos los clústeres Hadoop HDInsight. Escriba una consulta de ejemplo en el panel derecho y ver el resultado en la ficha de **resultados** en el panel debajo, tal como se muestra en la captura de pantalla.

    ![Ejecutar la sección consulta] (./media/hdinsight-hadoop-hue-linux/HDI.Hue.Portal.Hive.Query.png "Ejecutar la sección consulta")

    También puede usar la ficha **gráfico** para ver una representación visual del resultado.

### <a name="browse-the-cluster-storage"></a>Busque el almacenamiento de clúster

1. Desde el portal de matiz, haga clic en **Explorador de archivos** en la esquina superior derecha de la barra de menús.

2. De forma predeterminada se abre el Explorador de archivos en el directorio **/user/myuser** . Haga clic en la barra diagonal justo antes del directorio de usuario en la ruta de acceso para ir a la raíz del contenedor de almacenamiento de Azure asociado con el clúster.

    ![Explorador de archivos de uso] (./media/hdinsight-hadoop-hue-linux/HDI.Hue.Portal.File.Browser.png "Explorador de archivos de uso")

3. Haga clic en un archivo o carpeta para ver las operaciones disponibles. Use el botón **cargar** en la esquina derecha para cargar archivos en el directorio actual. Use el botón **nuevo** para crear nuevos archivos o directorios.

> [AZURE.NOTE] El Explorador de archivos de tono sólo puede mostrar el contenido del contenedor predeterminado asociado con el clúster de HDInsight. Cualquier que haya asociado con el clúster de almacenamiento adicional cuentas o contenedores no será accesibles desde el Explorador de archivos. Sin embargo, los contenedores adicionales asociados con el clúster siempre será accesibles para las tareas de la sección. Por ejemplo, si escribe el comando `dfs -ls wasbs://newcontainer@mystore.blob.core.windows.net` en el editor de sección, puede ver el contenido de los contenedores adicionales. En este comando, **newcontainer** no es el contenedor predeterminado asociado con un clúster.

## <a name="important-considerations"></a>Consideraciones importantes

1. La secuencia de comandos que se usó para instalar matiz instala solo en el headnode principal del clúster.

2. Durante la instalación, se reinician varios servicios de Hadoop (HDFS, hilo, MR2, Oozie) para actualizar la configuración. Cuando finalice el script instalar matiz, tardará algún tiempo para otros servicios de Hadoop para que se inicie. Esto puede afectar a rendimiento del matiz inicialmente. Una vez que se inician todos los servicios, matiz será totalmente funcional.

3.  Tono no comprende trabajos Tez, que es la predeterminada para la sección actual. Si desea usar MapReduce como el motor de ejecución de sección, actualice la secuencia de comandos para usar el comando siguiente en la secuencia de comandos:

        set hive.execution.engine=mr;

4.  Con Linux clústeres, puede tener un escenario donde se están ejecutando los servicios en la headnode principal mientras el Administrador de recursos se podría ejecutar en el secundario. Esta situación puede provocar errores (que se muestra a continuación) cuando se usa el tono para ver detalles de trabajos de ejecución en el clúster. Sin embargo, puede ver los detalles del trabajo cuando haya finalizado el trabajo.

    ![Error de portal de tono] (./media/hdinsight-hadoop-hue-linux/HDI.Hue.Portal.Error.png "Error de portal de tono")

    Esto es debido a un problema conocido. Como solución alternativa, modifique Ambari para que el Administrador de recursos activo también se ejecuta en el principal headnode.

5.  Tono comprende WebHDFS mientras HDInsight clústeres están usando el almacenamiento de Azure `wasbs://`. Por lo tanto, el script personalizado que se utiliza con la acción de secuencia de comandos instala WebWasb, que es un servicio compatible con WebHDFS para comunicarse con WASB. Por lo tanto, aunque el portal de matiz dice HDFS en lugares (por ejemplo, cuando se mueve el mouse sobre el **Explorador de archivos**), debe interpretarse como WASB.


## <a name="next-steps"></a>Pasos siguientes

- [Instalar Giraph en clústeres HDInsight](hdinsight-hadoop-giraph-install-linux.md). Use la personalización de clúster para instalar Giraph en clústeres HDInsight Hadoop. Giraph le permite realizar el procesamiento de gráfico con Hadoop y, a continuación, se puede usar con HDInsight de Azure.

- [Instalar Solr en clústeres HDInsight](hdinsight-hadoop-solr-install-linux.md). Use la personalización de clúster para instalar Solr en clústeres HDInsight Hadoop. Solr le permite realizar operaciones de búsqueda eficaces en los datos almacenados.

- [Instalar R en clústeres HDInsight](hdinsight-hadoop-r-scripts-linux.md). Use la personalización de clúster para instalar R en clústeres HDInsight Hadoop. R es un idioma de código abierto y el entorno para las estadísticas. Proporciona cientos de funciones estadísticas integradas y su propio lenguaje de programación que combina aspectos de la programación funcional y orientado a objetos. También proporciona capacidades gráficas extensas.

[powershell-install-configure]: install-configure-powershell-linux.md
[hdinsight-provision]: hdinsight-provision-clusters-linux.md
[hdinsight-cluster-customize]: hdinsight-hadoop-customize-cluster-linux.md
