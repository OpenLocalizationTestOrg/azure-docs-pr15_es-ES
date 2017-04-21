<properties
    pageTitle="Todos los temas de servicio de datos Factory | Microsoft Azure"
    description="Tabla de todos los temas para el servicio de Azure denominada Generador de datos que existen en http://azure.microsoft.com/documentation/articles/, el título y la descripción."
    services="data-factory"
    documentationCenter=""
    authors="spelluru"
    manager="jhubbard"
    editor="MightyPen"/>

<tags
    ms.service="data-factory"
    ms.workload="data-factory"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/05/2016"
    ms.author="spelluru"/>


# <a name="all-topics-for-azure-data-factory-service"></a>Todos los temas para el servicio de generador de datos de Azure

En este tema se enumeran todos los temas que se aplican directamente al servicio de **Generador de datos** de Azure. Puede buscar esta página Web para las palabras clave mediante **CTRL+f**, para buscar los temas de interés actual.




## <a name="new"></a>Nuevo

| &nbsp; | Título | Descripción |
| --: | :-- | :-- |
| 1 | [Mover los datos de Amazon Redshift con el generador de datos de Azure](data-factory-amazon-redshift-connector.md) | Obtenga información sobre cómo mover los datos de Redshift de Amazon con el generador de datos de Azure. |
| 2 | [Mover los datos de Amazon Simple Storage Service con el generador de datos de Azure](data-factory-amazon-simple-storage-service-connector.md) | Obtenga información sobre cómo mover datos de Amazon Simple Storage Service (S3) usando el generador de datos de Azure. |
| 3 | [Datos de Azure Factory - Asistente para copiar](data-factory-azure-copy-wizard.md) | Obtenga información sobre cómo usar al Asistente para la copia de datos fábrica Azure para copiar los datos desde orígenes de datos compatibles receptores. |
| 4 | [Tutorial: Crear su primer generador de datos de Azure con la API de REST generador de datos](data-factory-build-your-first-pipeline-using-rest-api.md) | En este tutorial, se crea una canalización de generador de datos de Azure de ejemplo con la API de REST generador de datos. |
| 5 | [Tutorial: Crear una canalización con la actividad de copia con la API de .NET](data-factory-copy-activity-tutorial-using-dotnet-api.md) | En este tutorial, cree una canalización del generador de datos de Azure con una actividad de copia con la API de .NET. |
| 6 | [Tutorial: Crear una canalización con la actividad de copia con la API de REST](data-factory-copy-activity-tutorial-using-rest-api.md) | En este tutorial, cree una canalización de generador de datos de Azure con una actividad de copia mediante el uso de la API de REST. |
| 7 | [Asistente para la copia de generador de datos](data-factory-copy-wizard.md) | Obtenga información sobre cómo usar al Asistente para la copia del generador de datos para copiar los datos desde orígenes de datos compatibles receptores. |
| 8 | [Data Management Gateway](data-factory-data-management-gateway.md) | Configurar una puerta de enlace de datos para mover datos entre local y la nube. Utilice Data Management Gateway en el generador de datos de Azure para mover los datos. |
| 9 | [Mover los datos de una base de datos local Casandra con el generador de datos de Azure](data-factory-onprem-cassandra-connector.md) | Obtenga información sobre cómo mover los datos de una base de datos local Casandra con el generador de datos de Azure. |
| 10 | [Mover los datos de MongoDB con el generador de datos de Azure](data-factory-on-premises-mongodb-connector.md) | Obtenga información sobre cómo mover los datos de la base de datos de MongoDB mediante el generador de datos de Azure. |
| 11 | [Mover datos de Salesforce mediante el generador de datos de Azure](data-factory-salesforce-connector.md) | Obtenga información sobre cómo mover datos de Salesforce mediante el generador de datos de Azure. |


## <a name="updated-articles-data-factory"></a>Artículos actualizados, el generador de datos

En esta sección se enumera los artículos que se han actualizado recientemente, donde la actualización fue grande o significativa. Para cada artículo actualizado, se muestra un fragmento de código aproximada del texto agregado descuento. Los artículos se actualizaron dentro del intervalo de fechas de **2016-08-22** a **2016-10-05**.

| &nbsp; | Artículo | Texto actualizado, fragmento de código | Se actualiza cuando |
| --: | :-- | :-- | :-- |
| 12 | [Generador de datos de Azure - registro de cambios de la API de .NET](data-factory-api-change-log.md) | En este artículo se proporciona información sobre cambios en SDK de fábrica de datos de Azure en una versión específica. Puede encontrar el paquete NuGet más reciente para aquí fábrica de Azure datos (https://www.nuget.org/packages/Microsoft.Azure.Management.DataFactories) **versión 4.11.0** característica adiciones: / se han agregado los siguientes tipos de servicio vinculadas: - OnPremisesMongoDbLinkedService (https://msdn.microsoft.com/library/mt765129.aspx) - AmazonRedshiftLinkedService (https://msdn.microsoft.com/library/mt765121.aspx) - AwsAccessKeyLinkedService (https://msdn.microsoft.com/library/mt765144.aspx) y se han agregado los siguientes tipos de conjunto de datos: AmazonS3Dataset - MongoDbCollectionDataset (https://msdn.microsoft.com/library/mt765145.aspx) - (https://msdn.microsoft.com/library/mt765112.aspx) o se han agregado los siguientes tipos de origen de copia :-MongoDbSource (https://msdn.microsoft.com/en-US/library/mt765123.aspx) **versión 4.10.0** o se han agregado las siguientes propiedades opcionales para TextFormat:-esquí | 2016-09-22 |
| 13 | [Mover los datos de blobs de Windows Azure con el generador de datos de Azure](data-factory-azure-blob-connector.md) |  / copyBehavior / define el comportamiento de copia cuando el origen es BlobSource o sistema de archivos.  / **PreserveHierarchy:** conserva la jerarquía de archivos en la carpeta de destino. La ruta de acceso del archivo de origen a la carpeta de origen es idéntica a la ruta de acceso del archivo de destino a la carpeta de destino... br /... br /. **FlattenHierarchy:** todos los archivos de la carpeta de origen se encuentran en el primer nivel de la carpeta de destino. Los archivos de destino tienen nombre genera automáticamente. entre /... br /. **MergeFiles: (predeterminado)** combina todos los archivos de la carpeta de origen a un archivo. Si se especifica el nombre de archivo o blobs, el nombre de archivo combinado sería el nombre especificado; en caso contrario, sería nombre de archivo generado automáticamente.  / No / **BlobSource** también es compatible con estas dos propiedades para compatibilidad con versiones anteriores. / **treatEmptyAsNull**: Especifica si se va a tratar null o cadena vacía como valor nulo. / **skipHeaderLineCount** - especifica que se necesita omitir el número de líneas. Es aplicable al conjunto de datos de entrada está usando solo TextFormat. Del mismo modo, **BlobSink** admite th | 2016-09-28 |
| 14 | [Crear predictivas canalizaciones con actividades de aprendizaje del equipo de Azure](data-factory-azure-ml-batch-execution-activity.md) | **Servicio Web requiere varias entradas** Si el servicio web tarda varias entradas, use la propiedad **webServiceInputs** en lugar de usar **webServiceInput**. Conjuntos de datos que hace referencia la **webServiceInputs** también deben incluirse en las **entradas**de actividad. En la prueba de Azure m parámetros globales y puertos de salida y entrada de servicio web tienen nombres predeterminados ("Entrada1", "Entrada2") que puede personalizar. Los nombres que usa para webServiceInputs, webServiceOutputs y configuración de globalParameters deben coincidir exactamente con los nombres de la experimentación. Puede ver la carga de solicitud de ejemplo en la página de Ayuda de ejecución de proceso por lotes el extremo de Azure v verificar la asignación esperada.    {"nombre": "PredictivePipeline", "propiedades": {"Descripción": "usar el modelo de AzureML", "actividades": {"nombre": "MLActivity", "tipo": "AzureMLBatchExecution", "Descripción": "análisis de predicción en lotes de entrada", "entradas": {"nombre": "inputDataset1"}, {"nombre": "inputDatase | 2016-09-13 |
| 15 | [Copiar actividad Guía de rendimiento y optimización](data-factory-copy-activity-performance.md) | 1. **establecer una línea base**. Durante la fase de desarrollo, probar la canalización mediante copia actividad frente a una muestra de representante de datos. Puede usar el generador de datos división modelo (datos-factory-programación-y-execution.md time-series-datasets-and-data-slices) para limitar la cantidad de datos que se trabaja con.  Recopilar tiempo de ejecución y las características de rendimiento mediante la **supervisión y administración de aplicación**. Seleccione **Monitor y administrar** en la página de inicio del generador de datos. En la vista de árbol, elija el **conjunto de datos de salida**. En la lista de **Actividad de Windows** , elija ejecutar la actividad de copia. **Actividad de Windows** muestra la duración de la actividad de copia y el tamaño de los datos que se copian. El rendimiento se muestra en el **Explorador de la ventana de actividad**. Para obtener más información acerca de la aplicación, vea a supervisar y administrar canalizaciones generador de datos de Azure mediante la supervisión y la aplicación de administración (datos-factory-monitor-administrar-app.md).  ! Ejecutar detalles (./media/data-factory-copy-activity-performance/mmapp-activity-run-details.pn de actividad | 2016-09-27 |
| 16 | [Tutorial: Crear una canalización con la actividad de copia con Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) |   Tenga en cuenta los siguientes puntos:- **tipo** de conjunto de datos se establece en **AzureBlob**.     - **linkedServiceName** se establece en **AzureStorageLinkedService**. Este servicio vinculada que creado en el paso 2.     - **folderPath** se establece en el contenedor **adftutorial** . También puede especificar el nombre de un blob dentro de la carpeta mediante la propiedad del **nombre de archivo** . Puesto que no se especifica el nombre del objeto binario, datos de todos los blobs en el contenedor se consideran como una entrada de datos.    -formato **tipo** se establece en **TextFormat** - hay dos campos en el texto archivo ΓÇô **nombre** y **Apellidos** ΓÇô separado por un carácter de coma (**columnDelimiter**): la **disponibilidad** se establece en **cada hora** (**frecuencia** se establece en **horas** y el **intervalo** está establecido en **1**). Por lo tanto, el generador de datos busca datos de entrada cada hora en la carpeta raíz del contenedor de blob (**adftutorial**) especificado.  Si no especifica un **nombre de archivo** para un conjunto de datos de **entrada** , todos los archivos o blobs de la carpeta de entrada (**folderPath**) son consid | 29-09-2016 |
| 17 | [Crear, supervisar y administrar fábricas de datos de Azure con datos fábrica .NET SDK](data-factory-create-data-factories-programmatically.md) | Anote el identificador de aplicación y la contraseña (secreto de cliente) y usarlo en el tutorial. **Suscripción de Azure obtener e inquilino identificadores** Si no tiene la versión más reciente de PowerShell instalado en su equipo, siga las instrucciones en cómo instalar y configurar Azure PowerShell (.. / configure.md de instalación de powershell) artículo para instalarlo. 1. Iniciar PowerShell de Azure y ejecute el siguiente comando 2. Ejecute el comando siguiente y escriba el nombre de usuario y contraseña que utiliza para iniciar sesión en el portal de Azure.         Inicio de sesión AzureRmAccount si que tiene solo una suscripción Azure asociada a esta cuenta, no es necesario llevar a cabo los dos pasos siguientes. 3. Ejecute el comando siguiente para ver todas las suscripciones para esta cuenta.       Get-AzureRmSubscription 4. Ejecute el comando siguiente para seleccionar la suscripción que desea trabajar con. Reemplace **NameOfAzureSubscription** con el nombre de su suscripción de Azure.       Get-AzureRmSubscription - SubscriptionName NameOfAzureSubscription / AzureRmCo conjunto | 2016-09-14 |
| 18 | [Canalizaciones y actividades en el generador de datos de Azure](data-factory-create-pipelines.md) |       "inicio": "2016-07-12T00:00:00Z", "end": "2016-07-13T00:00:00Z"}} tenga en cuenta los siguientes puntos: / en la sección actividades, hay sólo una actividad cuyo **tipo** se establece en **Copiar**. O entrada de la actividad se establece en **InputDataset** y salida de la actividad está establecida en **OutputDataset**. O en la sección **typeProperties** , **BlobSource** se especifica como el tipo de origen y **SqlSink** se especifica como el tipo de receptor. Para obtener un tutorial completo de creación de esta canalización, vea Tutorial: copiar datos de almacenamiento de blobs a la base de datos de SQL (data-factory-copy-data-from-azure-blob-storage-to-sql-database.md). **Canalización de la transformación de ejemplo** En la canalización de ejemplo siguiente, hay una actividad de tipo **HDInsightHive** en la sección de **actividades** . En este ejemplo, la actividad de la sección HDInsight (datos factory-subárbol activity.md) transforma los datos de un almacenamiento de blobs de Windows Azure ejecutando un archivo de script de sección en un clúster de Azure HDInsight Hadoop.  {"nombre": "TransformPipeline", "p | 2016-09-27 |
| 19 | [Transformar datos en el generador de datos de Azure](data-factory-data-transformation-activities.md) | Generador de datos admite las siguientes actividades de transformación de datos que se pueden agregar a tuberías (datos-factory-crear-pipelines.md) individualmente o encadenada con otra actividad. .  AZURE. Nota para obtener un tutorial con instrucciones paso a paso, vea Crear una canalización con el artículo de sección transformación (data-factory-build-your-first-pipeline.md). **Sección de HDInsight de actividad** La actividad de subárbol HDInsight de una canalización de datos fábrica ejecuta consultas subárbol sus propias o clúster de HDInsight basado en Windows y Linux a petición. Consulte el artículo de actividad de la sección (datos factory-subárbol activity.md) para obtener más información sobre esta actividad. **Cerdo HDInsight de actividad** La actividad de cerdo HDInsight de una canalización de datos fábrica ejecuta consultas cerdo sus propias o clúster de a petición HDInsight basado en Windows o Linux. Consulte el artículo de actividad de cerdo (datos factory-cerdo activity.md) para obtener más información sobre esta actividad. **HDInsight de MapReduce actividad** La actividad HDInsight MapReduce en una canalización de datos fábrica ejecuta programas MapReduce en y | 2016-09-26 |
| 20 | [Programación de fábrica de datos y la ejecución](data-factory-scheduling-and-execution.md) | Ejecutar CopyActivity2 solo si se ha ejecutado correctamente el CopyActivity1 y Dataset2 está disponible. Aquí es la canalización de ejemplo JSON: {"nombre": "ChainActivities", "propiedades": {"Descripción": "Ejecutar actividades en secuencia", "actividades": {"tipo": "Copiar", "typeProperties": {"origen": {"tipo": "BlobSource"}, "receptor": {"tipo": "BlobSink", "copyBehavior": "PreserveHierarchy", "writeBatchSize": 0, "writeBatchTimeout": "00: 00:00"}}, "entradas": {"nombre": "Dataset1"}, "envía": {"nombre": "Dataset2"}, "directiva": {"tiempo de espera": "01: 00:00"}, "programador": {"frecuencia": "Hora", "intervalo": 1}, "nombre": "CopyFromBlob1ToBlob2", "Descripción": "Copiar datos de un objeto binario a otro"}, {"tipo": "Copiar", "typeProperties": {"origen": {"tipo": "BlobSource"}, "receptor" : {"tipo": "BlobSink", "writeBatchSize": 0, "writeBatchTimeout": "00: 00:00"}}, "en | 2016-09-28 |





## <a name="tutorials"></a>Tutoriales

| &nbsp; | Título | Descripción |
| --: | :-- | :-- |
| 21 | [Tutorial: Crear su primer canalización para procesar datos mediante clúster Hadoop](data-factory-build-your-first-pipeline.md) | Este tutorial generador de datos de Azure muestra cómo crear y programar un generador de datos que procesa datos usando secuencias de comandos de sección en un clúster de Hadoop. |
| 22 | [Tutorial: Crear su primer generador de datos de Azure con el Administrador de recursos de Azure plantilla](data-factory-build-your-first-pipeline-using-arm.md) | En este tutorial, se crea una canalización de generador de datos de Azure de ejemplo con una plantilla de administrador de recursos de Azure. |
| 23 | [Tutorial: Crear su primer generador de datos de Azure con Azure portal](data-factory-build-your-first-pipeline-using-editor.md) | En este tutorial, se crea una canalización de generador de datos de Azure de ejemplo con el Editor de fábrica de datos en el portal de Azure. |
| 24 | [Tutorial: Crear su primer generador de datos de Azure con PowerShell de Azure](data-factory-build-your-first-pipeline-using-powershell.md) | En este tutorial, se crea una canalización de generador de datos de Azure de ejemplo con PowerShell de Azure. |
| 25 | [Tutorial: Generador de datos crear su primer Azure con Microsoft Visual Studio](data-factory-build-your-first-pipeline-using-vs.md) | En este tutorial, creará una canalización de generador de datos de Azure de ejemplo con Visual Studio. |
| 26 | [Tutorial: Crear una canalización con la actividad de copia con Azure portal](data-factory-copy-activity-tutorial-using-azure-portal.md) | En este tutorial, creará una canalización del generador de datos de Azure con una actividad de copia mediante el Editor de fábrica de datos en el portal de Azure. |
| 27 | [Tutorial: Crear una canalización con la actividad de copia con PowerShell de Azure](data-factory-copy-activity-tutorial-using-powershell.md) | En este tutorial, se crea una canalización del generador de datos de Azure con una actividad de copia con PowerShell de Azure. |
| 28 | [Tutorial: Crear una canalización con la actividad de copia con Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) | En este tutorial, creará una canalización del generador de datos de Azure con una actividad de copia mediante Visual Studio. |
| 29 | [Copiar datos de almacenamiento de blobs de base de datos de SQL mediante el generador de datos](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) | En este tutorial se muestra cómo usar la actividad de copia en una canalización del generador de datos de Azure para copiar los datos de almacenamiento de blobs de base de datos de SQL. |
| 30 | [Tutorial: Crear una canalización con actividad copia utilizando al Asistente para copiar la fábrica de datos](data-factory-copy-data-wizard-tutorial.md) | En este tutorial, crear una canalización de generador de datos de Azure con una actividad de copia mediante el Asistente para copiar compatibles con el generador de datos |



## <a name="data-movement"></a>Movimiento de datos

| &nbsp; | Título | Descripción |
| --: | :-- | :-- |
| 31 | [Mover los datos de blobs de Windows Azure con el generador de datos de Azure](data-factory-azure-blob-connector.md) | Obtenga información sobre cómo copiar datos blob en el generador de datos de Azure. Utilizar nuestro ejemplo: cómo copiar datos a y desde el almacenamiento de blobs de Windows Azure y base de datos de SQL Azure. |
| 32 | [Mover los datos del almacén de lago de datos de Azure con el generador de datos de Azure](data-factory-azure-datalake-connector.md) | Obtenga información sobre cómo mover los datos de almacén de lago de datos de Azure mediante el generador de datos de Azure |
| 33 | [Mover los datos hacia y desde DocumentDB con el generador de datos de Azure](data-factory-azure-documentdb-connector.md) | Obtenga información sobre cómo mover los datos a o de la colección de Azure DocumentDB con el generador de datos de Azure |
| 34 | [Mover los datos de SQL Azure con generador de datos de Azure](data-factory-azure-sql-connector.md) | Obtenga información sobre cómo mover los datos desde base de datos de Azure SQL con el generador de datos de Azure. |
| 35 | [Mover los datos hacia y desde el almacén de datos de SQL Azure con el generador de datos de Azure](data-factory-azure-sql-data-warehouse-connector.md) | Obtenga información sobre cómo mover los datos de almacén de datos de SQL Azure con el generador de datos de Azure |
| 36 | [Mover los datos de tabla de Azure con el generador de datos de Azure](data-factory-azure-table-connector.md) | Obtenga información sobre cómo mover los datos de almacenamiento de tablas de Azure con el generador de datos de Azure. |
| 37 | [Copiar actividad Guía de rendimiento y optimización](data-factory-copy-activity-performance.md) | Obtenga información sobre los factores clave que afectan al rendimiento de movimiento de datos en el generador de datos de Azure cuando se usa la actividad de copia. |
| 38 | [Mover datos mediante el uso de la actividad de copia](data-factory-data-movement-activities.md) | Obtenga más información sobre el movimiento de datos en datos fábrica canalizaciones: migración de datos entre nube almacena y un almacén local y el almacenamiento de una nube. Use la actividad de copia. |
| 39 | [Notas de la versión de Data Management Gateway](data-factory-gateway-release-notes.md) | Notas de la versión de artículo de datos Management Gateway |
| 40 | [Mover los datos de HDFS local con el generador de datos de Azure](data-factory-hdfs-connector.md) | Obtenga información sobre cómo mover los datos de HDFS locales con Azure datos fábrica. |
| 41 | [Supervisar y administrar con nuevo supervisión y aplicación de administración de canalizaciones de generador de datos de Azure](data-factory-monitor-manage-app.md) | Obtenga información sobre cómo utilizar supervisión y aplicación de administración para supervisar y administrar canalizaciones y generadores de datos de Azure. |
| 42 | [Mover datos entre orígenes locales y en la nube con Data Management Gateway](data-factory-move-data-between-onprem-and-cloud.md) | Configurar una puerta de enlace de datos para mover datos entre local y la nube. Utilice Data Management Gateway en el generador de datos de Azure para mover los datos. |
| 43 | [Mover los datos de origen de OData unos con el generador de datos de Azure](data-factory-odata-connector.md) | Obtenga información sobre cómo mover los datos de fuentes de OData con el generador de datos de Azure. |
| 44 | [Mover datos de ODBC datos almacena con el generador de datos de Azure](data-factory-odbc-connector.md) | Obtenga información sobre cómo mover los datos de ODBC datos almacena con el generador de datos de Azure. |
| 45 | [Mover los datos de DB2 con el generador de datos de Azure](data-factory-onprem-db2-connector.md) | Obtenga más información sobre cómo mover los datos de la base de datos de DB2 con el generador de datos de Azure |
| 46 | [Mover datos hacia y desde un sistema de archivos local mediante el generador de datos de Azure](data-factory-onprem-file-system-connector.md) | Obtenga información sobre cómo mover datos hacia y desde un sistema de archivos local mediante el generador de datos de Azure. |
| 47 | [Mover los datos de MySQL con el generador de datos de Azure](data-factory-onprem-mysql-connector.md) | Obtenga información sobre cómo mover los datos de la base de datos MySQL con el generador de datos de Azure. |
| 48 | [Mover los datos de Oracle local con el generador de datos de Azure](data-factory-onprem-oracle-connector.md) | Obtenga información sobre cómo mover usando el generador de datos de Azure en local de datos de Oracle base de datos. |
| 49 | [Mover los datos de PostgreSQL con el generador de datos de Azure](data-factory-onprem-postgresql-connector.md) | Obtenga información sobre cómo mover los datos de la base de datos PostgreSQL con el generador de datos de Azure. |
| 50 | [Mover los datos de Sybase con el generador de datos de Azure](data-factory-onprem-sybase-connector.md) | Obtenga información sobre cómo mover los datos de la base de datos de Sybase mediante el generador de datos de Azure. |
| 51 | [Mover los datos de Teradata con el generador de datos de Azure](data-factory-onprem-teradata-connector.md) | Obtenga más información sobre el conector de Teradata al servicio de generador de datos que le permite mover los datos de la base de datos de Teradata |
| 52 | [Mover los datos a y desde SQL Server local o en IaaS (Azure VM) con el generador de datos de Azure](data-factory-sqlserver-connector.md) | Obtenga información sobre cómo mover datos desde base de datos de SQL Server local o en una máquina virtual de Azure con el generador de datos de Azure. |
| 53 | [Mover los datos de un origen de tabla de Web con el generador de datos de Azure](data-factory-web-table-connector.md) | Obtenga información sobre cómo mover datos locales una tabla en una página Web con el generador de datos de Azure. |



## <a name="data-transformation"></a>Transformación de datos

| &nbsp; | Título | Descripción |
| --: | :-- | :-- |
| 54 | [Crear predictivas canalizaciones con actividades de aprendizaje del equipo de Azure](data-factory-azure-ml-batch-execution-activity.md) | Describe cómo crear crear predictivas canalizaciones con el generador de datos de Azure y aprendizaje del equipo de Azure |
| 55 | [Calcular servicios vinculados](data-factory-compute-linked-services.md) | Obtenga información sobre enviornments de cálculo que puede usar en el generador de datos de Azure canalizaciones al procesar y para transformar los datos. |
| 56 | [Proceso conjuntos de datos a gran escala mediante el generador de datos y por lotes](data-factory-data-processing-using-batch.md) | Describe cómo procesar grandes cantidades de datos en una canalización del generador de datos de Azure mediante la capacidad de procesamiento en paralelo del lote de Azure. |
| 57 | [Transformar datos en el generador de datos de Azure](data-factory-data-transformation-activities.md) | Obtenga información sobre cómo transformar datos o datos de proceso de fábrica de datos de Azure con Hadoop, el aprendizaje o análisis de lago de datos de Azure. |
| 58 | [Actividad de transmisión de Hadoop](data-factory-hadoop-streaming-activity.md) | Obtenga información sobre cómo puede usar la actividad de transmisión de Hadoop un generador de datos de Azure para ejecutar programas de transmisión de Hadoop en un clúster de HDInsight en-petición o su propio. |
| 59 | [Actividad de sección](data-factory-hive-activity.md) | Obtenga información sobre cómo puede usar la actividad de subárbol en un generador de datos de Azure para ejecutar consultas de la sección en un clúster de HDInsight en-petición o su propio. |
| 60 | [Invocar MapReduce programas de la fábrica de datos](data-factory-map-reduce.md) | Obtenga información sobre cómo procesar datos ejecutando programas MapReduce en un clúster de Azure HDInsight desde un generador de datos de Azure. |
| 61 | [Actividad de cerdo](data-factory-pig-activity.md) | Obtenga información sobre cómo puede usar la actividad de cerdo en un generador de datos de Azure ejecuten secuencias de comandos de cerdo en un clúster de HDInsight en-petición o su propio. |
| 62 | [Invocar programas de motor de la fábrica de datos](data-factory-spark.md) | Obtenga información sobre cómo invocar un generador de datos de Azure mediante la actividad MapReduce programas motor. |
| 63 | [Actividad de procedimiento almacenados de SQL Server](data-factory-stored-proc-activity.md) | Obtenga información sobre cómo puede usar la actividad de procedimiento almacenado de SQL Server para invocar un procedimiento almacenado en una base de datos de SQL Azure o el almacén de datos de SQL Azure de una canalización del generador de datos. |
| 64 | [Usar las actividades personalizadas de una canalización de generador de datos de Azure](data-factory-use-custom-activities.md) | Aprenda a crear actividades personalizadas y usarlos en una canalización del generador de datos de Azure. |
| 65 | [Ejecutar secuencia de comandos de U SQL Azure datos lago Analytics de la fábrica de datos de Azure](data-factory-usql-activity.md) | Obtenga información sobre cómo procesar datos ejecutando secuencias de comandos U SQL en el servicio de cálculo de análisis de lago de datos de Azure. |



## <a name="samples"></a>Ejemplos

| &nbsp; | Título | Descripción |
| --: | :-- | :-- |
| 66 | [Datos de Azure Factory - ejemplos](data-factory-samples.md) | Proporciona detalles acerca de los ejemplos que se incluyen con el servicio de generador de datos de Azure. |



## <a name="use-cases"></a>Casos de uso

| &nbsp; | Título | Descripción |
| --: | :-- | :-- |
| 67 | [Casos prácticos de clientes](data-factory-customer-case-studies.md) | Obtenga información sobre cómo algunos de nuestros clientes han usado generador de datos de Azure. |
| 68 | [Utilizar caso - perfiles de cliente](data-factory-customer-profiling-usecase.md) | Obtenga información sobre cómo se utiliza el generador de datos de Azure para crear un flujo de controlados por datos (canalización) para los clientes de juegos de perfil. |
| 69 | [Utilizar caso - recomendaciones de producto](data-factory-product-reco-usecase.md) | Obtenga información sobre un caso de uso que se implementan mediante el generador de datos de Azure junto con otros servicios. |



## <a name="monitor-and-manage"></a>Supervisar y administrar

| &nbsp; | Título | Descripción |
| --: | :-- | :-- |
| 70 | [Supervisar y administrar el generador de datos de Azure canalizaciones](data-factory-monitor-manage-pipelines.md) | Obtenga información sobre cómo usar el Portal de Azure y Azure PowerShell para supervisar y administrar fábricas de datos de Azure y canalizaciones que haya creado. |



## <a name="sdk"></a>SDK

| &nbsp; | Título | Descripción |
| --: | :-- | :-- |
| 71 | [Generador de datos de Azure - registro de cambios de la API de .NET](data-factory-api-change-log.md) | Describe los cambios importantes, las adiciones de características, correcciones de errores etcetera... en una versión específica de .NET API para el generador de datos de Azure. |
| 72 | [Crear, supervisar y administrar fábricas de datos de Azure con datos fábrica .NET SDK](data-factory-create-data-factories-programmatically.md) | Aprenda a crear, supervisar y administrar fábricas de datos de Azure mediante el SDK de fábrica de datos mediante programación. |
| 73 | [Referencia del programador de generador de datos de Azure](data-factory-sdks.md) | Obtenga más información sobre las diferentes maneras de crear, supervisar y administrar fábricas de datos de Azure |



## <a name="miscellaneous"></a>Varios

| &nbsp; | Título | Descripción |
| --: | :-- | :-- |
| 74 | [Generador de datos de Azure - preguntas más frecuentes](data-factory-faq.md) | Preguntas más frecuentes sobre el generador de datos de Azure. |
| 75 | [Datos de Azure Factory - funciones y Variables del sistema](data-factory-functions-variables.md) | Proporciona una lista de funciones del generador de datos de Azure y variables del sistema |
| 76 | [Generador de datos de Azure - reglas de nomenclatura](data-factory-naming-rules.md) | Describe las reglas de nomenclatura para entidades de generador de datos. |
| 77 | [Solucionar problemas del generador de datos](data-factory-troubleshoot.md) | Obtenga información sobre cómo solucionar problemas relacionados con el generador de datos de Azure. |

