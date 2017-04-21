<properties
    pageTitle="Tutorial de Linux: Introducción a Hadoop y subárbol | Microsoft Azure"
    description="Siga este tutorial Linux para empezar a usar Hadoop en HDInsight. Obtenga información sobre cómo aprovisionar Linux clústeres y consultar los datos de la sección."
    services="hdinsight"
    documentationCenter=""
    authors="mumian"
    manager="jhubbard"
    editor="cgronlun"
    tags="azure-portal"/>

<tags
    ms.service="hdinsight"
    ms.devlang="na"
    ms.topic="hero-article"
    ms.tgt_pltfrm="na"
    ms.workload="big-data"
    ms.date="09/14/2016"
    ms.author="jgao"/>

# <a name="hadoop-tutorial-get-started-using-linux-based-hadoop-in-hdinsight"></a>Tutorial de Hadoop: empezar a usar basados en Linux Hadoop en HDInsight

> [AZURE.SELECTOR]
- [Basados en Linux](hdinsight-hadoop-linux-tutorial-get-started.md)
- [Basado en Windows](hdinsight-hadoop-tutorial-get-started-windows.md)

Aprenda cómo crear clústeres basados en Linux [Hadoop](http://hadoop.apache.org/) en HDInsight y ejecutar trabajos de sección en HDInsight. [Apache subárbol](https://hive.apache.org/) es el componente más popular del ecosistema Hadoop. Actualmente HDInsight se suministra con 4 tipos diferentes de clúster: [Hadoop](hdinsight-hadoop-introduction.md), [motor](hdinsight-apache-spark-overview.md), [HBase](hdinsight-hbase-overview.md) y [tormenta](hdinsight-storm-overview.md).  Cada tipo de clúster es compatible con un conjunto diferente de componentes. Todos los tipos de clúster 4 admiten subárbol. Para obtener una lista de componentes compatibles en HDInsight, consulte [Novedades en las versiones de clúster Hadoop proporcionadas por HDInsight?](hdinsight-component-versioning.md)  

[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="prerequisites"></a>Requisitos previos

Antes de comenzar este tutorial, debe tener:

- **Suscripción de Azure**: para crear una cuenta gratuita de prueba de un mes, vaya a [azure.microsoft.com/free](https://azure.microsoft.com/free).

### <a name="access-control-requirements"></a>Requisitos de control de acceso

[AZURE.INCLUDE [access-control](../../includes/hdinsight-access-control-requirements.md)]

## <a name="create-cluster"></a>Crear clúster

La mayoría de los trabajos de Hadoop son trabajos por lotes. Crear un clúster, ejecutar algunas tareas y, a continuación, elimine el clúster. En esta sección, creará un clúster basados en Linux Hadoop en HDInsight con [el Administrador de recursos de Azure plantilla](../resource-group-template-deploy.md). Plantilla de administrador de recursos es totalmente personalizable; es fácil crear recursos Azure como HDInsight. Experiencia de la plantilla de administrador de recursos no es necesario para seguir este tutorial. Para otros métodos de creación de clúster y descripción de las propiedades que se utilizan en este tutorial, vea [crear HDInsight clústeres](hdinsight-hadoop-provision-linux-clusters.md). La plantilla de administrador de recursos utilizada en este tutorial se encuentra en un contenedor de blob público [https://hditutorialdata.blob.core.windows.net/armtemplates/create-linux-based-hadoop-cluster-in-hdinsight.json](https://hditutorialdata.blob.core.windows.net/armtemplates/create-linux-based-hadoop-cluster-in-hdinsight.json). 

1. Haga clic en la imagen siguiente para iniciar sesión Azure y abra la plantilla de administrador de recursos en el Portal de Azure. 

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Farmtemplates%2Fcreate-linux-based-hadoop-cluster-in-hdinsight.json" target="_blank"><img src="https://acom.azurecomcdn.net/80C57D/cdn/mediahandler/docarticles/dpsmedia-prod/azure.microsoft.com/en-us/documentation/articles/hdinsight-hbase-tutorial-get-started-linux/20160201111850/deploy-to-azure.png" alt="Deploy to Azure"></a>

2. Desde el módulo de **parámetros** , escriba lo siguiente:

    ![HDInsight Linux empezar plantilla de administrador de recursos en el portal](./media/hdinsight-hadoop-linux-tutorial-get-started/hdinsight-linux-get-started-arm-template-on-portal.png).

    - **NombreDeClúster**: escriba un nombre para el clúster de Hadoop que se creará.
    - **Contraseña y nombre de inicio de sesión de clúster**: el nombre de inicio de sesión predeterminado es **admin**.
    - **SSH nombre de usuario y contraseña**: el nombre de usuario predeterminado es **sshuser**.  Puede cambiarlo. 
    
    Otros parámetros son opcionales para seguir este tutorial. Puede dejarla como estén. 
    
    Cada clúster tiene una dependencia de cuenta de almacenamiento de blobs de Windows Azure. Normalmente se conoce como la cuenta de almacenamiento predeterminada. HDInsight clúster y su cuenta de almacenamiento predeterminada deben estar conjunta ubicados en la misma región de Azure. Eliminación de clústeres se elimina la cuenta de almacenamiento. En la plantilla, el nombre de la cuenta de almacenamiento predeterminada se define como el nombre de clúster con anexa "store". 
    
3. Haga clic en **Aceptar** para guardar los parámetros.
4. Desde el módulo de **implementación personalizada** , escriba el **nombre del nuevo grupo de recursos** para crear un nuevo grupo de recursos.  El grupo de recursos es un contenedor que agrupa el clúster, la cuenta de almacenamiento dependientes y otros s. La ubicación del grupo de recursos puede ser distinta de la ubicación de clúster.
5. Haga clic en **condiciones legales**y, a continuación, haga clic en **crear**.
6. Compruebe que está seleccionada la casilla de verificación **Anclar al panel** y, a continuación, haga clic en **crear**. Verá un nuevo mosaico titulado **implementación de implementación de plantilla**. Tarda aproximadamente 20 minutos para crear un clúster. 
7.  Una vez creado el clúster, el título de la ventana cambia el nombre de grupo de recursos especificado. Y el portal abre automáticamente dos módulos con el clúster y la configuración de clúster. 

    ![Configuración de clúster cómo empezar HDInsight Linux](./media/hdinsight-hadoop-linux-tutorial-get-started/hdinsight-linux-get-started-cluster-settings.png).

    Existen dos recursos enumerados, el clúster y la cuenta de almacenamiento predeterminada.

##<a name="run-hive-queries"></a>Ejecutar consultas de la sección

[Apache subárbol](hdinsight-use-hive.md) es el componente más popular utilizado en HDInsight. Hay muchas formas de ejecutar trabajos de sección en HDInsight. En este tutorial, usará la vista Ambari sección desde el portal para ejecutar algunas tareas de la sección. Para otros métodos para enviar trabajos de sección, vea [Usar subárbol en HDInsight](hdinsight-use-hive.md).

1. Vaya a **https://&lt;nombreDeClúster >. azurehdinsight.net**, donde &lt;nombreDeClúster > es el clúster que creó en la sección anterior para abrir Ambari.
2. Escriba el nombre de usuario Hadoop y la contraseña que ha especificado en la sección anterior. El nombre de usuario predeterminado es **admin**.
3. Abrir **Vista subárbol** como se muestra en la siguiente captura de pantalla:

    ![Seleccionar vistas Ambari](./media/hdinsight-hadoop-linux-tutorial-get-started/selecthiveview.png).
4. En la sección __Editor de consultas__ de la página, pegue las siguientes afirmaciones HiveQL en la hoja de cálculo:

        SHOW TABLES;

    >[AZURE.NOTE] Sección requiere punto y coma.       
        
5. Haga clic en __Ejecutar__. Una sección de __Resultados de proceso de la consulta__ debe aparecer debajo del Editor de consultas y mostrar información sobre el trabajo. 

    Una vez finalizada la consulta, la sección de __Resultados de proceso de la consulta__ mostrará los resultados de la operación. Se verá una tabla denominada **hivesampletable**. Esta tabla de la sección de ejemplo incluye todos los clústeres HDInsight.

    ![Vistas de sección de HDInsight](./media/hdinsight-hadoop-linux-tutorial-get-started/hiveview.png).

6. Repita los pasos 4 y 5 para ejecutar la consulta siguiente:

        SELECT * FROM hivesampletable;

    > [AZURE.TIP] Tenga en cuenta la lista desplegable de __Guardar resultados__ en la esquina superior izquierda de la sección de __Resultados de proceso de la consulta__ . Puede usar esta a descargar los resultados o guardarlos en almacenamiento HDInsight como un archivo CSV.

7. Haga clic en **historial** para obtener una lista de los trabajos.

Una vez finalizado el trabajo de una sección, puede [exportar los resultados de la base de datos de SQL Azure o base de datos de SQL Server](hdinsight-use-sqoop-mac-linux.md), también puede [visualizar los resultados con Excel](hdinsight-connect-excel-power-query.md). Para obtener más información sobre el uso de la sección de HDInsight, consulte [usar subárbol y HiveQL con Hadoop en HDInsight para analizar un archivo de ejemplo Apache log4j](hdinsight-use-hive.md).

##<a name="clean-up-the-tutorial"></a>Limpiar el tutorial

Después de completar el tutorial, desea eliminar el clúster. Con HDInsight, los datos se almacenan en el almacenamiento de Azure, por lo que puede eliminar un clúster cuando no está en uso. También se cargan un clúster HDInsight, incluso cuando no está en uso. Dado que muchas veces más que los cargos para el almacenamiento de los cargos para el clúster, tiene sentido económico para eliminar clústeres cuando no están en uso. 

>[AZURE.NOTE] Con el [Generador de datos de Azure](hdinsight-hadoop-create-linux-clusters-adf.md), puede crear clústeres HDInsight a petición y configurar una opción de período de vida para eliminar los clústeres automáticamente. 

**Para eliminar el clúster o la cuenta de almacenamiento predeterminada**

1. Inicie sesión en el [portal de Azure](https://portal.azure.com).
2. Desde el panel de portal, haga clic en el mosaico con el nombre del grupo de recursos que utilizó cuando creó el clúster.
3. Haga clic en **Eliminar** en el módulo de recursos para eliminar el grupo de recursos que contiene el clúster y la cuenta de almacenamiento predeterminada; o haga clic en el nombre de clúster en el mosaico de **recursos** y, a continuación, haga clic en **Eliminar** en el módulo de clúster. Nota, eliminar el grupo de recursos elimine la cuenta de almacenamiento. Si desea conservar la cuenta de almacenamiento, elija Eliminar solo el clúster.

## <a name="next-steps"></a>Pasos siguientes

En este tutorial, ha visto cómo crear un clúster de HDInsight basados en Linux mediante una plantilla de administrador de recursos y cómo realizar consultas básicas de sección.

Para obtener más información acerca del análisis de datos con HDInsight, consulte lo siguiente:

- Para obtener más información sobre cómo usar subárbol con HDInsight, incluido cómo realizar consultas de la sección de Visual Studio, vea [Usar subárbol con HDInsight][hdinsight-use-hive].

- Para obtener información sobre cerdo, un lenguaje utilizado para transformar datos, vea [Usar cerdo con HDInsight][hdinsight-use-pig].

- Para obtener información sobre MapReduce, una manera de escribir programas que procesan datos en Hadoop, vea [Usar MapReduce con HDInsight][hdinsight-use-mapreduce].

- Para obtener información sobre cómo usar las herramientas de HDInsight para Visual Studio para analizar datos en HDInsight, consulte [Introducción al uso de herramientas de Hadoop de Visual Studio para HDInsight](hdinsight-hadoop-visual-studio-tools-get-started.md).

Si está listo para empezar a trabajar con sus propios datos y necesita más información sobre cómo HDInsight almacena los datos o cómo incorporar datos a HDInsight, consulte lo siguiente:

- Para obtener información sobre cómo HDInsight usa almacenamiento de blobs de Windows Azure, consulte [almacenamiento de blobs de Azure uso con HDInsight](hdinsight-hadoop-use-blob-storage.md).

- Para obtener información sobre cómo cargar datos a HDInsight, vea [cargar datos a HDInsight][hdinsight-upload-data].

Si desea obtener más información sobre cómo crear o administrar un clúster de HDInsight, consulte lo siguiente:

- Para obtener información acerca de cómo administrar el clúster basados en Linux HDInsight, consulte [clústeres de administrar HDInsight con Ambari](hdinsight-hadoop-manage-ambari.md).

- Para obtener más información sobre las opciones que puede seleccionar al crear un clúster de HDInsight, consulte [Crear HDInsight en Linux mediante opciones personalizadas](hdinsight-hadoop-provision-linux-clusters.md).

- Si está familiarizado con Linux y Hadoop, pero necesita información específica sobre Hadoop en la HDInsight, consulte [trabajar con HDInsight en Linux](hdinsight-hadoop-linux-information.md). Proporciona información como:

    * Direcciones URL de servicios alojados en el clúster, como Ambari y WebHCat
    * La ubicación de archivos Hadoop y ejemplos del sistema de archivos locales
    * Almacenar el uso de Azure almacenamiento (WASB) en lugar de HDFS como los datos predeterminados


[1]: ../HDInsight/hdinsight-hadoop-visual-studio-tools-get-started.md

[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-admin-powershell]: hdinsight-administer-use-powershell.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-use-mapreduce]: hdinsight-use-mapreduce.md
[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-pig]: hdinsight-use-pig.md

[powershell-download]: http://go.microsoft.com/fwlink/p/?linkid=320376&clcid=0x409
[powershell-install-configure]: powershell-install-configure.md
[powershell-open]: powershell-install-configure.md#Install

[img-hdi-dashboard]: ./media/hdinsight-hadoop-tutorial-get-started-windows/HDI.dashboard.png
[img-hdi-dashboard-query-select]: ./media/hdinsight-hadoop-tutorial-get-started-windows/HDI.dashboard.query.select.png
[img-hdi-dashboard-query-select-result]: ./media/hdinsight-hadoop-tutorial-get-started-windows/HDI.dashboard.query.select.result.png
[img-hdi-dashboard-query-select-result-output]: ./media/hdinsight-hadoop-tutorial-get-started-windows/HDI.dashboard.query.select.result.output.png
[img-hdi-dashboard-query-browse-output]: ./media/hdinsight-hadoop-tutorial-get-started-windows/HDI.dashboard.query.browse.output.png
[image-hdi-clusterstatus]: ./media/hdinsight-hadoop-tutorial-get-started-windows/HDI.ClusterStatus.png
[image-hdi-gettingstarted-powerquery-importdata]: ./media/hdinsight-hadoop-tutorial-get-started-windows/HDI.GettingStarted.PowerQuery.ImportData.png
[image-hdi-gettingstarted-powerquery-importdata2]: ./media/hdinsight-hadoop-tutorial-get-started-windows/HDI.GettingStarted.PowerQuery.ImportData2.png
