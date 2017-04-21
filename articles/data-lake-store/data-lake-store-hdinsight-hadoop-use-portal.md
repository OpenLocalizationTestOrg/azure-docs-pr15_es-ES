<properties
   pageTitle="Crear clústeres de HDInsight con almacén de lago de datos de Azure con el portal | Azure"
   description="Usar el Portal de Azure para crear y usar clústeres HDInsight con lago almacén de datos de Azure"
   services="data-lake-store,hdinsight" 
   documentationCenter=""
   authors="nitinme"
   manager="jhubbard"
   editor="cgronlun"/>

<tags
   ms.service="data-lake-store"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="10/21/2016"
   ms.author="nitinme"/>

# <a name="create-an-hdinsight-cluster-with-data-lake-store-using-azure-portal"></a>Crear un clúster de HDInsight con el almacén de datos lago con el Portal de Azure

> [AZURE.SELECTOR]
- [Con Portal](data-lake-store-hdinsight-hadoop-use-portal.md)
- [Uso de PowerShell](data-lake-store-hdinsight-hadoop-use-powershell.md)
- [Con el Administrador de recursos](data-lake-store-hdinsight-hadoop-use-resource-manager-template.md)


Obtenga información sobre cómo usar el Portal de Azure para crear un clúster de HDInsight (Hadoop, HBase, motor o tormenta) con acceso al almacén de lago de datos de Azure. Algunas consideraciones importantes para esta versión:

* **Motor para clústeres (Linux) y clústeres de Hadoop (Windows y Linux)**, el almacén de datos de lago solo sirve como una cuenta de almacenamiento adicional. La cuenta de almacenamiento predeterminada para los clústeres de este tipo seguirán siendo Azure almacenamiento de Blobs (WASB).

* **Tormenta para clústeres (Windows y Linux)**, el almacén de datos de lago puede usarse para escribir los datos de una topología de tormenta. Almacén de datos de lago también puede utilizarse para almacenar los datos de referencia que pueden leer por una topología de tormenta. Para obtener más información, vea [Utilizar el almacén de lago de datos en una topología de tormenta](#use-data-lake-store-in-a-storm-topology).

* **HBase para clústeres (Windows y Linux)**, el almacén de datos de lago puede usarse un almacenamiento predeterminado así como almacenamiento adicional. Para obtener más información, vea [Utilizar el almacén de lago de datos con clústeres HBase](#use-data-lake-store-with-hbase-clusters).

> [AZURE.NOTE] Aspectos importantes que tenga en cuenta. 
> 
> * Opción para crear clústeres HDInsight con acceso al almacén de datos lago sólo está disponible para las versiones de HDInsight 3.2 y 3.4 (para clústeres Hadoop, HBase y tormenta en Windows como Linux). Para clústeres motor en Linux, esta opción solo está disponible en clústeres HDInsight 3.4.
>
> * Como se mencionó anteriormente, el almacén de datos de lago está disponible como almacenamiento predeterminado para algunos tipos de clúster (HBase) y almacenamiento adicional para otros tipos de clúster (Hadoop, motor, tormenta). Usar el almacén de lago de datos como una cuenta de almacenamiento adicional no afecta a rendimiento o la capacidad de lectura y escritura para el almacenamiento de clúster. En un escenario donde se usa el almacén de datos de lago como almacenamiento adicional, archivos relacionados con el clúster (por ejemplo, registros, etc.) se escriben en el almacenamiento predeterminado (Blobs de Azure), mientras que los datos que se desean procesar pueden almacenarse en una cuenta de almacén de datos lago.


## <a name="prerequisites"></a>Requisitos previos

Antes de comenzar este tutorial, debe tener el siguiente:

- **Suscripción de un Azure**. Vea la [versión de prueba gratuita de Azure obtener](https://azure.microsoft.com/pricing/free-trial/).

- **Cuenta de la tienda de lago de datos de azure**. Siga las instrucciones en [Introducción a Azure datos lago tienda con el Portal de Azure](data-lake-store-get-started-portal.md). 

- **Cargar datos de ejemplo a su cuenta de lago almacén de datos de Azure**. Una vez que ha creado la cuenta, realice las siguientes tareas para cargar los datos de ejemplo. Necesitará estos datos más adelante en el tutorial para ejecutar trabajos desde un clúster de HDInsight acceso a los datos del almacén de datos lago.

    * [Crear una carpeta en el almacén de datos de lago](data-lake-store-get-started-portal.md#createfolder).
    * [Cargar un archivo en el almacén de datos de lago](data-lake-store-get-started-portal.md#uploaddata). Si está buscando algunos datos de ejemplo cargar, puede obtener la carpeta **Ambulancia datos** del [Repositorio de Azure datos lago Git](https://github.com/Azure/usql/tree/master/Examples/Samples/Data/AmbulanceData).

- **Principales de servicio de azure Active Directory**. Pasos de este tutorial proporcionan instrucciones sobre cómo crear una entidad de seguridad de servicio en Azure AD. Sin embargo, debe ser un administrador de Azure AD para poder crear una entidad de seguridad del servicio. Si es un administrador de Azure AD, puede omitir este requisito previo y continuar con el tutorial.
    
    **Si es un administrador de Azure AD no**, no podrá realizar los pasos necesarios para crear una entidad de seguridad del servicio. En este caso, el Administrador de Azure AD debe crear una entidad de seguridad de servicio para poder crear un clúster de HDInsight con el almacén de datos de lago. Además, servicio principal debe crearse mediante un certificado, como se describe en [crear un servicio principal con certificado](../resource-group-authenticate-service-principal.md#create-service-principal-with-certificate).

## <a name="do-you-learn-faster-with-videos"></a>¿Aprender más rápido con vídeos?

Vea los siguientes vídeos para comprender cómo aprovisionar clústeres HDInsight con acceso al almacén de datos lago.

* [Crear un clúster de HDInsight con acceso al almacén de datos de lago](https://mix.office.com/watch/l93xri2yhtp2)
* Una vez que el clúster está configurado, [datos de Access en el almacén de datos lago mediante secuencias de comandos de la sección y cerdo](https://mix.office.com/watch/1n9g5w0fiqv1q)

## <a name="create-an-hdinsight-cluster-with-access-to-azure-data-lake-store"></a>Crear un clúster de HDInsight con acceso al almacén de lago de datos de Azure

En esta sección, puede crear un clúster de HDInsight Hadoop que usa el almacén de lago de datos como un almacenamiento adicional. En esta versión, para un clúster de Hadoop, almacén de datos de lago solo sirve como un almacenamiento adicional para el clúster. El almacenamiento predeterminado seguirán siendo el BLOB de almacenamiento de Azure (WASB). Por lo tanto, primero crearemos la cuenta de almacenamiento y los contenedores de almacenamiento necesarios para el clúster.

1. Inicie sesión el nuevo [Portal de Azure](https://portal.azure.com).

2. Siga los pasos de [crear Hadoop clústeres en HDInsight](../hdinsight/hdinsight-provision-clusters.md#create-using-the-preview-portal) para iniciar un clúster HDInsight de aprovisionamiento.

3. En el módulo **Configuración opcional** , haga clic en **Origen de datos**. En el módulo de **Origen de datos** , especifique los detalles de la cuenta de almacenamiento y el contenedor de almacenamiento, especifique la **ubicación** US **oriental 2**y, a continuación, haga clic en **Clúster AAD identidad**.

    ![Agregar servicio principal de clúster HDInsight] (./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.1.png "Agregar servicio principal de clúster HDInsight")

4. En el módulo de **Identidad de AAD de clúster** , puede seleccionar una entidad de seguridad de servicio existente o cree uno nuevo.

    * **Crear a una nueva identidad de servicio**

        * En el módulo de **Identidad de AAD de clúster** , haga clic en **Crear nuevo**, haga clic en **Servicio Principal**y, a continuación, en el módulo de **crear una entidad de seguridad de servicio** , proporcione valores para crear a una nueva identidad de servicio. Como parte de ese, también se crea una aplicación de Azure Active Directory y un certificado. Haga clic en **crear**.

            ![Agregar servicio principal de clúster HDInsight] (./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.2.png "Agregar servicio principal de clúster HDInsight")

        * En el módulo de **Identidad de AAD de clúster** , haga clic en **Administrar el acceso de ADLS**. El panel muestra las cuentas de almacén de datos lago asociadas con la suscripción. Sin embargo, puede establecer los permisos solo para la cuenta que ha creado. Seleccione los permisos de lectura/escritura/ejecución de la cuenta que desea asociar con el clúster de HDInsight y, a continuación, haga clic en **Guardar permisos**.

            ![Agregar servicio principal de clúster HDInsight] (./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.3.png "Agregar servicio principal de clúster HDInsight")

        * En el módulo de **Identidad de AAD de clúster** , haga clic en **Descargar certificado** para descargar el certificado asociado con el servicio principal que creó. Esto es útil si desea usar al mismo principal de servicio en el futuro, al crear más clústeres HDInsight. Haga clic en **Seleccionar**.

            ![Agregar servicio principal de clúster HDInsight] (./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.4.png "Agregar servicio principal de clúster HDInsight")


    * **Elija a una identidad de servicio existente**

        * En el módulo de **Identidad de AAD de clúster** , haga clic en **Usar existente**, haga clic en **Servicio Principal**y, a continuación, en el módulo de **Seleccionar una entidad de seguridad de servicio** de búsqueda para una identidad de servicio existente. Haga clic en un nombre principal de servicio y, a continuación, haga clic en **Seleccionar**.

            ![Agregar servicio principal de clúster HDInsight] (./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.5.png "Agregar servicio principal de clúster HDInsight")

        * En el módulo de **Identidad de AAD de clúster** , cargar el certificado (.pfx) asociado con la identidad de servicio que ha seleccionado y, a continuación, proporcione la contraseña de certificado.

5. Haga clic en **Administrar el acceso de ADLS** y, a continuación, haga clic en **Seleccionar permisos de archivo**. 

    ![Agregar servicio principal de clúster HDInsight] (./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.5.existing.save.png "Agregar servicio principal de clúster HDInsight")

6. En el módulo **seleccione permisos de archivo** , en la lista desplegable **cuenta** , seleccione la cuenta de almacén de lago de datos que desea asociado con el clúster de HDInsight. El módulo enumera los archivos y carpetas disponibles en la cuenta de la tienda de lago de datos seleccionada. 
 
    ![Proporcionar acceso al almacén de datos lago] (./media/data-lake-store-hdinsight-hadoop-use-portal/hdi-adl-permission-1.png "Proporcionar acceso al almacén de datos lago")

    Después de eso, determinar los permisos que se proporcione la los archivos y carpetas seleccionados. Para las carpetas, especifique si los permisos se aplican a la carpeta solo o a la carpeta y todos los elementos secundarios en la carpeta. Puede realizar esta selección seleccionando el valor correspondiente de la lista **Aplicar a** lista desplegable. Para quitar un permiso, haga clic en el icono **Eliminar**

    ![Proporcionar acceso al almacén de datos lago] (./media/data-lake-store-hdinsight-hadoop-use-portal/hdi-adl-permission-2.png "Proporcionar acceso al almacén de datos lago")

    Repita estos pasos para carpetas y archivos asociados de otras cuentas de almacén de datos lago también. Cuando haya terminado de asignar los permisos, haga clic en **Seleccionar** en la parte inferior de la hoja.

7. En el módulo **asignar permisos seleccionados** , revise los permisos que haya proporcionado y, a continuación, haga clic en **Ejecutar** para conceder los permisos.

    ![Proporcionar acceso al almacén de datos lago] (./media/data-lake-store-hdinsight-hadoop-use-portal/hdi-adl-permission-3.png "Proporcionar acceso al almacén de datos lago")

    La columna Estado muestra el progreso. Una vez que todos los permisos asignados correctamente, haga clic en **Listo**. 

6. Haga clic en **Seleccionar** en los módulos **Clúster AAD identidad** y **Origen de datos** y continúe con creaciones de clúster como se describe en [Hadoop crear clústeres en HDInsight](../hdinsight/hdinsight-hadoop-create-linux-clusters-portal.md).

7. Una vez que se aprovisiona el clúster, puede comprobar que la entidad de seguridad de servicio está asociado con el clúster HDInsight. Para ello, desde el módulo de clúster, haga clic en el **Clúster AAD identidad** para ver a la identidad de servicio asociada.

    ![Agregar servicio principal de clúster HDInsight] (./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.6.png "Agregar servicio principal de clúster HDInsight")

## <a name="run-test-jobs-on-the-hdinsight-cluster-to-use-the-azure-data-lake-store"></a>Ejecutar trabajos de prueba en el clúster HDInsight usar el almacén de lago de datos de Azure

Después de configurar un clúster de HDInsight, puede ejecutar trabajos de prueba en el clúster para comprobar que el clúster HDInsight puede tener acceso a datos en el almacén de lago de datos de Azure. Para ello, se ejecutará algunas consultas de la sección que el almacén de lago de datos de destino.

### <a name="for-a-linux-cluster"></a>Para un clúster de Linux

1. Abra el módulo de clúster para el clúster que acaba de aprovisionar y, a continuación, haga clic en **panel**. Se abrirá Ambari para el clúster de Linux. Al acceder a Ambari, le pedirá para autenticar al sitio. Escriba el admin (Administrador de forma predeterminada), nombre de cuenta y contraseña que utilizó al crear el clúster.

    ![Iniciar el panel de diseño] (./media/data-lake-store-hdinsight-hadoop-use-portal/hdiadlcluster1.png "Iniciar el panel de diseño")

    También puede ir directamente a Ambari yendo a https://CLUSTERNAME.azurehdinsight.net en un explorador web (donde **NOMBREDECLÚSTER** es el nombre de su clúster HDInsight).

2. Abrir la vista de sección. Seleccione el conjunto de los cuadrados en el menú de la página (junto al vínculo de **administración** y el botón de la derecha de la página) a las vistas de lista disponible. Seleccione la vista de **sección** .

    ![Seleccionar vistas ambari](./media/data-lake-store-hdinsight-hadoop-use-portal/selecthiveview.png)

3. Verá una página similar al siguiente:

    ![Imagen de la página de la vista de sección que contiene una sección del editor de consultas](./media/data-lake-store-hdinsight-hadoop-use-portal/hiveview.png)

4. En la sección **Editor de consultas** de la página, pegue la siguiente instrucción HiveQL en la hoja de cálculo:

        CREATE EXTERNAL TABLE vehicles (str string) LOCATION 'adl://mydatalakestore.azuredatalakestore.net:443/mynewfolder'

5. Haga clic en el botón **Ejecutar** en la parte inferior del **Editor de consultas** para iniciar la consulta. Una sección de **Resultados de proceso de la consulta** debe aparecen debajo del **Editor de consultas** y mostrar información sobre el trabajo.

6. Una vez finalizada la consulta, la sección de **Resultados de proceso de la consulta** mostrará los resultados de la operación. La ficha **resultados** debe contener la siguiente información:

7. Ejecute la consulta siguiente para comprobar que se creó la tabla.

        SHOW TABLES;

    La ficha **resultados** debe mostrar lo siguiente:

        hivesampletable
        vehicles

    **vehículos** es la tabla que creó anteriormente. **hivesampletable** es una tabla de muestra disponible en todos los clústeres HDInsight de forma predeterminada.

8. También puede ejecutar una consulta para recuperar datos de la tabla de **vehículos** .

        SELECT * FROM vehicles LIMIT 5;

### <a name="for-a-windows-cluster"></a>Para un clúster de Windows

1. Abra el módulo de clúster para el clúster que acaba de aprovisionar y, a continuación, haga clic en **panel**.

    ![Iniciar el panel de diseño] (./media/data-lake-store-hdinsight-hadoop-use-portal/hdiadlcluster1.png "Iniciar el panel de diseño")

    Cuando se le pida, escriba las credenciales de administrador para el clúster.

2. Se abrirá la consola de consulta de Microsoft Azure HDInsight. Haga clic en la **sección Editor**.

    ![Abrir sección editor] (./media/data-lake-store-hdinsight-hadoop-use-portal/hdiadlcluster2.png "Abrir sección editor")

3. En el Editor de sección, escriba la siguiente consulta y, a continuación, haga clic en **Enviar**.

        CREATE EXTERNAL TABLE vehicles (str string) LOCATION 'adl://mydatalakestore.azuredatalakestore.net:443/mynewfolder'

    En la consulta de esta sección, se creará una tabla de datos almacenados en el almacén de datos lago en `adl://mydatalakestore.azuredatalakestore.net:443/mynewfolder`. Esta ubicación tiene un archivo de datos de ejemplo que debe ha cargado anteriormente.

    La tabla de **Sesiones de trabajo** en la parte inferior muestra el estado de la tarea de **ejecución**, como **completado**el cambio de **Inicializando**. También puede hacer clic en **Ver detalles** para obtener más información sobre el trabajo completado.

    ![Crear una tabla] (./media/data-lake-store-hdinsight-hadoop-use-portal/hdiadlcluster3.png "Crear una tabla")

4. Ejecute la consulta siguiente para comprobar que se creó la tabla.

        SHOW TABLES;

    Haga clic en **Ver detalles** correspondiente a esta consulta y los resultados debe mostrar lo siguiente:

        hivesampletable
        vehicles

    **vehículos** es la tabla que creó anteriormente. **hivesampletable** es una tabla de muestra disponible en todos los clústeres HDInsight de forma predeterminada.

5. También puede ejecutar una consulta para recuperar datos de la tabla de **vehículos** .

        SELECT * FROM vehicles LIMIT 5;


## <a name="access-data-lake-store-using-hdfs-commands"></a>Almacén de lago de datos de Access con los comandos HDFS

Una vez que haya configurado el clúster de HDInsight para usar el almacén de datos de lago, puede usar los comandos de shell HDFS para tener acceso a la tienda.

### <a name="for-a-linux-cluster"></a>Para un clúster de Linux

En esta sección se SSH en el clúster y ejecute los comandos HDFS. Windows no proporciona a un cliente SSH integrado. Se recomienda usar **PuTTY**, que puede descargarse desde [http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).

Para obtener más información sobre el uso de PuTTY, vea [Usar SSH con basados en Linux Hadoop en HDInsight de Windows](../hdinsight/hdinsight-hadoop-linux-use-ssh-windows.md).

Una vez conectado, utilice el siguiente comando de sistema de archivos HDFS para enumerar los archivos del almacén de datos lago.

    hdfs dfs -ls adl://<Data Lake Store account name>.azuredatalakestore.net:443/

Esto debe mostrar el archivo que ha cargado anteriormente en el almacén de datos de lago.

    15/09/17 21:41:15 INFO web.CaboWebHdfsFileSystem: Replacing original urlConnectionFactory with org.apache.hadoop.hdfs.web.URLConnectionFactory@21a728d6
    Found 1 items
    -rwxrwxrwx   0 NotSupportYet NotSupportYet     671388 2015-09-16 22:16 adl://mydatalakestore.azuredatalakestore.net:443/mynewfolder

También puede usar el `hdfs dfs -put` comando cargar algunos archivos en el almacén de lago de datos y, a continuación, usar `hdfs dfs -ls` para comprobar si los archivos se han cargado correctamente.


### <a name="for-a-windows-cluster"></a>Para un clúster de Windows

1. Inicie sesión el nuevo [Portal de Azure](https://portal.azure.com).

2. Haga clic en **Examinar**, haga clic en **HDInsight clústeres**y, a continuación, haga clic en el clúster HDInsight que ha creado.

3. En el módulo de clúster, haga clic en **Escritorio remoto**y, a continuación, en el módulo de **Escritorio remoto** , haga clic en **Conectar**.

    ![Remoto en clúster HDI] (./media/data-lake-store-hdinsight-hadoop-use-portal/ADL.HDI.PS.Remote.Desktop.png "Crear un grupo de recursos de Azure")

    Cuando se le pida, escriba las credenciales proporcionadas para el usuario de escritorio remoto.

4. En la sesión remota, iniciar Windows PowerShell y use los comandos de sistema de archivos HDFS para enumerar los archivos en el almacén de lago de datos de Azure.

        hdfs dfs -ls adl://<Data Lake Store account name>.azuredatalakestore.net:443/

    Esto debe mostrar el archivo que ha cargado anteriormente en el almacén de datos de lago.

        15/09/17 21:41:15 INFO web.CaboWebHdfsFileSystem: Replacing original urlConnectionFactory with org.apache.hadoop.hdfs.web.URLConnectionFactory@21a728d6
        Found 1 items
        -rwxrwxrwx   0 NotSupportYet NotSupportYet     671388 2015-09-16 22:16 adl://mydatalakestore.azuredatalakestore.net:443/mynewfolder

    También puede usar el `hdfs dfs -put` comando cargar algunos archivos en el almacén de lago de datos y, a continuación, usar `hdfs dfs -ls` para comprobar si los archivos se han cargado correctamente.

## <a name="use-data-lake-store-with-spark-cluster"></a>Utilizar el almacén de lago de datos con clúster de motor

En esta sección, use Bloc de notas de Jupyter disponible con HDInsight Spark clústeres para ejecutar un trabajo que lea los datos de una cuenta de almacén de datos lago asociado con un clúster de HDInsight Spark, en lugar de la cuenta de blobs de Windows Azure almacenamiento predeterminada.

1. Copie sobre algunos datos de ejemplo de la cuenta de almacenamiento predeterminada (WASB) asociada con el clúster de motor a la cuenta de la tienda de lago de datos de Azure asociado con el clúster. Puede usar la [herramienta ADLCopy](http://aka.ms/downloadadlcopy) para hacerlo. Descargue e instale la herramienta desde el vínculo.

2. Abra un símbolo del sistema y navegue hasta el directorio donde está instalado AdlCopy, normalmente `%HOMEPATH%\Documents\adlcopy`.

3. Ejecute el comando siguiente para copiar un blob específico del contenedor de origen a un almacén de lago de datos:

        AdlCopy /source https://<source_account>.blob.core.windows.net/<source_container>/<blob name> /dest swebhdfs://<dest_adls_account>.azuredatalakestore.net/<dest_folder>/ /sourcekey <storage_account_key_for_storage_container>

    En este tutorial, copie el archivo de datos de ejemplo de **HVAC.csv** en **/HdiSamples/HdiSamples/SensorSampleData/hvac/** a la cuenta de la tienda de lago de datos de Azure. El fragmento de código debe ser similar:

        AdlCopy /Source https://mydatastore.blob.core.windows.net/mysparkcluster/HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv /dest swebhdfs://mydatalakestore.azuredatalakestore.net/hvac/ /sourcekey uJUfvD6cEvhfLoBae2yyQf8t9/BpbWZ4XoYj4kAS5Jf40pZaMNf0q6a8yqTxktwVgRED4vPHeh/50iS9atS5LQ==

    >[AZURE.WARNING] Asegúrese de que los nombres de archivo y la ruta están en el propio.

4. Se le pedirá que escriba las credenciales para la suscripción de Azure en el que tiene su cuenta de almacén de datos lago. Verá un resultado similar al siguiente:

        Initializing Copy.
        Copy Started.
        100% data copied.
        Copy Completed. 1 file copied.

    El archivo de datos (**HVAC.csv**) se copiarán en una carpeta **/hvac** en la cuenta de almacén de datos lago.

4. Desde el [Portal de Azure](https://portal.azure.com/), desde el startboard, haga clic en el mosaico de clúster motor (si lo anclados a la startboard). También puede ir a su clúster en **Examinar todos** > **HDInsight clústeres**.   

2. Desde el módulo de clúster motor, haga clic en **Vínculos rápidos**y, en el módulo de **Clúster paneles** , haga clic en **Bloc de notas de Jupyter**. Si se le solicita, escriba las credenciales de administrador para el clúster.

    > [AZURE.NOTE] También puede contactar el Bloc de notas de Jupyter para el clúster abriendo la siguiente URL en el explorador. Reemplazar __NOMBREDECLÚSTER__ con el nombre del clúster:
    >
    > `https://CLUSTERNAME.azurehdinsight.net/jupyter`

2. Crear un nuevo bloc de notas. Haga clic en **nuevo**y, a continuación, haga clic en **PySpark**.

    ![Crear un nuevo bloc de notas de Jupyter] (./media/data-lake-store-hdinsight-hadoop-use-portal/hdispark.note.jupyter.createnotebook.png "Crear un nuevo bloc de notas de Jupyter")

3. Un bloc de notas se crea y se abre con el nombre **Untitled.pynb**. 

4. Ya ha creado un bloc de notas con el núcleo PySpark, no es necesario crear cualquier contextos explícitamente. Los contextos motor y subárbol se crearán automáticamente para usted cuando se ejecuta la primera celda de código. Puede empezar importando los tipos necesarios para este escenario. Para ello, pegue el fragmento de código siguiente en una celda y presione **MAYÚS + ENTRAR**.

        from pyspark.sql.types import *
        
    Cada vez que ejecute un trabajo en Jupyter, el título de ventana de explorador web le mostrará un estado **(disponibilidad)** junto con el título del Bloc de notas. También verá un círculo sólido al lado del texto de **PySpark** en la esquina superior derecha. Una vez completada la tarea, cambiará a un círculo hueco.

     ![Estado de un trabajo de Bloc de notas de Jupyter] (./media/data-lake-store-hdinsight-hadoop-use-portal/hdispark.jupyter.job.status.png "Estado de un trabajo de Bloc de notas de Jupyter")

4. Cargar datos de ejemplo en una tabla temporal utilizando el archivo **HVAC.csv** que ha copiado en la cuenta de almacén de datos lago. Puede obtener acceso a los datos de la cuenta de la tienda de lago de datos utilizando el modelo de dirección URL siguiente.

        adl://<data_lake_store_name>.azuredatalakestore.net/<path_to_file>

    En una celda vacía, pegue el siguiente ejemplo de código, reemplace **MYDATALAKESTORE** por su nombre de cuenta de almacén de datos lago y presione **MAYÚS + ENTRAR**. Este ejemplo de código registra los datos en una tabla temporal denominada **hvac**.

        # Load the data
        hvacText = sc.textFile("adl://MYDATALAKESTORE.azuredatalakestore.net/hvac/HVAC.csv")
        
        # Create the schema
        hvacSchema = StructType([StructField("date", StringType(), False),StructField("time", StringType(), False),StructField("targettemp", IntegerType(), False),StructField("actualtemp", IntegerType(), False),StructField("buildingID", StringType(), False)])
        
        # Parse the data in hvacText
        hvac = hvacText.map(lambda s: s.split(",")).filter(lambda s: s[0] != "Date").map(lambda s:(str(s[0]), str(s[1]), int(s[2]), int(s[3]), str(s[6]) ))
        
        # Create a data frame
        hvacdf = sqlContext.createDataFrame(hvac,hvacSchema)
        
        # Register the data fram as a table to run queries against
        hvacdf.registerTempTable("hvac")

5. Dado que está utilizando un núcleo PySpark, ahora puede ejecutar directamente una consulta SQL en la tabla temporal **hvac** que acaba de crear mediante el uso de la `%%sql` mágico. Para obtener más información sobre la `%%sql` mágico, así como otras magics disponibles con el núcleo PySpark, vea [Kernels disponibles en los blocs de notas de Jupyter con HDInsight de motor clústeres](hdinsight-apache-spark-jupyter-notebook-kernels.md#why-should-i-use-the-new-kernels).
        
        %%sql
        SELECT buildingID, (targettemp - actualtemp) AS temp_diff, date FROM hvac WHERE date = \"6/1/13\"

5. Una vez que la tarea se completó correctamente, el resultado de la tabla siguiente se muestra de forma predeterminada.

    ![Resultados de la tabla de resultados de consulta] (./media/data-lake-store-hdinsight-hadoop-use-portal/tabular.output.png "Resultados de la tabla de resultados de consulta")

    También puede ver los resultados en otras visualizaciones también. Por ejemplo, un gráfico de área para el mismo resultado tendría un aspecto similar al siguiente.

    ![Gráfico de áreas del resultado de la consulta] (./media/data-lake-store-hdinsight-hadoop-use-portal/area.output.png "Gráfico de áreas del resultado de la consulta")


6. Cuando haya terminado de ejecutar la aplicación, debe cerrar el Bloc de notas para liberar los recursos. Para ello, en el menú **archivo** en el Bloc de notas, haga clic en **Cerrar y detener**. Este se cerrará y cierre el Bloc de notas.

## <a name="use-data-lake-store-in-a-storm-topology"></a>Utilizar el almacén de lago de datos en una topología de tormenta

Puede usar el almacén de lago de datos para escribir los datos de una topología de tormenta. Para obtener instrucciones sobre cómo conseguir esta situación, consulte [Utilizar el almacén de lago de datos de Azure con Apache tormenta con HDInsight](../hdinsight/hdinsight-storm-write-data-lake-store.md).

## <a name="use-data-lake-store-with-hbase-clusters"></a>Utilizar el almacén de lago de datos con clústeres HBase

Con HBase clústeres, puede usar almacén de lago de datos como un almacenamiento predeterminado, así como almacenamiento adicional. Siga este procedimiento:

1.  En el módulo de **Origen de datos** , para **HBase datos de ubicación**, seleccione **Datos lago tienda** .
2.  Seleccione el nombre de la tienda de lago de datos que desea usar, o cree uno nuevo.
3.  Por último, especifique la **Carpeta raíz de HBase** dentro de la tienda de lago de datos. Si la cuenta de almacén de datos lago no tiene una carpeta raíz, cree uno nuevo.

    ![HBase con el almacén de datos de lago] (./media/data-lake-store-hdinsight-hadoop-use-portal/hbase-data-lake-store.png "Crear un grupo de recursos de Azure")

### <a name="considerations-when-using-data-lake-store-as-default-storage-for-hbase-clusters"></a>Consideraciones al usar el almacén de datos de lago como almacenamiento predeterminado para clústeres HBase

* Puede usar la misma cuenta de almacén de datos lago de más de un clúster de HBase. Sin embargo, la **Carpeta raíz de HBase** que proporciona para el clúster (paso n.º 4 en la captura de pantalla anterior) deben ser únicos. **No debe** usar la misma carpeta raíz entre dos clústeres HBase diferentes.
* Incluso si usa cuenta de almacén de datos lago como almacenamiento de forma predeterminada, los archivos de registro de clúster HBase aún se almacenan en el Azure almacenamiento de Blobs (WASB) asociado con el clúster. Esto está resaltada en el cuadro de azul en la captura de pantalla anteriormente.



## <a name="see-also"></a>Vea también

* [PowerShell: Crear un clúster de HDInsight para usar el almacén de datos de lago](data-lake-store-hdinsight-hadoop-use-powershell.md)

[makecert]: https://msdn.microsoft.com/library/windows/desktop/ff548309(v=vs.85).aspx
[pvk2pfx]: https://msdn.microsoft.com/library/windows/desktop/ff550672(v=vs.85).aspx
