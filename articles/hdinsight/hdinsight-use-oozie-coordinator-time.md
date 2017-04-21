<properties
    pageTitle="Usar tiempo basadas en el Coordinador de Hadoop Oozie en HDInsight | Microsoft Azure"
    description="Usar tiempo basadas en el Coordinador de Hadoop Oozie en HDInsight de un servicio de datos grande. Obtenga información sobre cómo definir los coordinadores y flujos de trabajo de Oozie y enviar trabajos."
    services="hdinsight"
    documentationCenter=""
    tags="azure-portal"
    authors="mumian"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="hdinsight"
    ms.workload="big-data"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/25/2016"
    ms.author="jgao"/>


# <a name="use-time-based-oozie-coordinator-with-hadoop-in-hdinsight-to-define-workflows-and-coordinate-jobs"></a>Utilizar tiempo basadas en el Coordinador de Oozie con Hadoop en HDInsight para definir los flujos de trabajo y coordinar trabajos

En este artículo, aprenderá cómo definir los flujos de trabajo y los coordinadores y cómo activar los trabajos de coordinador, según la hora. Resulta útil ir a través de [Uso Oozie con HDInsight] [ hdinsight-use-oozie] antes de leer este artículo. Además de Oozie, también puede programar trabajos mediante el generador de datos de Azure. Para obtener información sobre el generador de datos de Azure, vea [usar cerdo y subárbol con el generador de datos](../data-factory/data-factory-data-transformation-activities.md).

> [AZURE.NOTE] En este artículo, se requiere un clúster basado en Windows HDInsight. Para obtener información sobre cómo usar Oozie, incluidas las tareas de tiempo, en un clúster de Linux, vea [Usar Oozie con Hadoop para definir y ejecutar un flujo de trabajo en HDInsight de Linux](hdinsight-use-oozie-linux-mac.md)

##<a name="what-is-oozie"></a>¿Qué es Oozie

Apache Oozie es un sistema de flujo de trabajo o junto a la que administra Hadoop trabajos. Se integra con la pila Hadoop y admite Hadoop trabajos de Apache MapReduce, Apache cerdo, Apache subárbol y Apache Sqoop. También puede usarse para programar trabajos que son específicos de un sistema, como los programas de Java o secuencias de comandos de shell.

La imagen siguiente muestra el flujo de trabajo que se implementará:

![Diagrama de flujo de trabajo][img-workflow-diagram]

El flujo de trabajo contiene dos acciones:

1. Una acción de la sección ejecuta una secuencia de comandos HiveQL para contar las apariciones de cada tipo de nivel de registro en un archivo de registro log4j. Cada registro log4j consta de una línea de campos que contiene un campo [nivel de registro] para mostrar el tipo y la gravedad, por ejemplo:

        2012-02-03 18:35:34 SampleClass6 [INFO] everything normal for id 577725851
        2012-02-03 18:35:34 SampleClass4 [FATAL] system problem at id 1991281254
        2012-02-03 18:35:34 SampleClass3 [DEBUG] detail for id 1304807656
        ...

    Es similar a la salida de la secuencia de comandos de sección:

        [DEBUG] 434
        [ERROR] 3
        [FATAL] 1
        [INFO]  96
        [TRACE] 816
        [WARN]  4

    Para obtener más información acerca de la sección, vea [Usar subárbol con HDInsight][hdinsight-use-hive].

2.  Una acción Sqoop exporta el resultado de la acción HiveQL a una tabla en una base de datos de SQL Azure. Para obtener más información sobre Sqoop, vea [Usar Sqoop con HDInsight][hdinsight-use-sqoop].

> [AZURE.NOTE] Para versiones Oozie compatibles en clústeres HDInsight, consulte [Novedades en las versiones de clúster proporcionadas por HDInsight?] [hdinsight-versions].


##<a name="prerequisites"></a>Requisitos previos

Antes de comenzar este tutorial, debe tener el siguiente:

- **Una estación de trabajo con PowerShell de Azure**.

    [AZURE.INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]

- **HDInsight un clúster**. Para obtener información sobre la creación de un clúster de HDInsight, consulte [crear HDInsight clústeres][hdinsight-provision], o [empezar con HDInsight][hdinsight-get-started]. Necesitará los siguientes datos llegue el tutorial:

    <table border = "1">
    <tr><th>Clúster (propiedad)</th><th>Nombre de variable de Windows PowerShell</th><th>Valor</th><th>Descripción</th></tr>
    <tr><td>Nombre de clúster HDInsight</td><td>$clusterName</td><td></td><td>El clúster de HDInsight en que se ejecutará este tutorial.</td></tr>
    <tr><td>Nombre de usuario de clúster HDInsight</td><td>$clusterUsername</td><td></td><td>El nombre de usuario de clúster HDInsight. </td></tr>
    <tr><td>Contraseña de usuario de clúster HDInsight </td><td>$clusterPassword</td><td></td><td>La contraseña de usuario de clúster HDInsight.</td></tr>
    <tr><td>Nombre de la cuenta de almacenamiento de Azure</td><td>$storageAccountName</td><td></td><td>Una cuenta de Azure almacenamiento disponible para el clúster de HDInsight. En este tutorial, use la cuenta de almacenamiento predeterminada que especificó durante el proceso de aprovisionamiento de clúster.</td></tr>
    <tr><td>Nombre del contenedor de blobs de Windows Azure</td><td>$containerName</td><td></td><td>En este ejemplo, utilice el contenedor de almacenamiento de blobs de Windows Azure que se usa para el sistema de archivos de clúster de HDInsight predeterminado. De forma predeterminada, tiene el mismo nombre que el clúster HDInsight.</td></tr>
    </table>

- **Base de datos de SQL Azure**. Debe configurar una regla de firewall para el servidor de base de datos de SQL permitir el acceso desde su estación de trabajo. Para obtener instrucciones sobre la creación de SQL Azure de base de datos y configurar el firewall, vea [Introducción al uso de la base de datos de SQL Azure][sqldatabase-get-started]. En este artículo se proporciona un script de Windows PowerShell para crear la tabla de base de datos de SQL Azure que necesita para este tutorial.

    <table border = "1">
    <tr><th>Propiedad de base de datos SQL</th><th>Nombre de variable de Windows PowerShell</th><th>Valor</th><th>Descripción</th></tr>
    <tr><td>Nombre del servidor de base de datos SQL</td><td>$sqlDatabaseServer</td><td></td><td>El servidor de base de datos SQL a la que Sqoop exportar los datos. </td></tr>
    <tr><td>Nombre de inicio de sesión de base de datos SQL</td><td>$sqlDatabaseLogin</td><td></td><td>Nombre de inicio de sesión de base de datos de SQL.</td></tr>
    <tr><td>Contraseña de inicio de sesión de base de datos SQL</td><td>$sqlDatabaseLoginPassword</td><td></td><td>Contraseña de inicio de sesión de base de datos de SQL.</td></tr>
    <tr><td>Nombre de la base de datos SQL</td><td>$sqlDatabaseName</td><td></td><td>La base de datos de SQL Azure al que Sqoop exportar los datos. </td></tr>
    </table>

    > [AZURE.NOTE] De forma predeterminada una base de datos de SQL Azure permite las conexiones de servicios de Azure, como HDInsight de Azure. Si se deshabilita esta configuración de firewall, debe habilitar desde el Portal de Azure. Para obtener instrucciones sobre cómo crear una base de datos de SQL y configuración de las reglas de firewall, vea [crear y configurar la base de datos de SQL][sqldatabase-get-started].


> [AZURE.NOTE] Para rellenar los valores de las tablas. Será útil para seguir este tutorial.


##<a name="define-oozie-workflow-and-the-related-hiveql-script"></a>Definir flujo de trabajo de Oozie y la secuencia de comandos de HiveQL relacionado

Definiciones de Oozie flujos de trabajo se escriben en hPDL (un lenguaje de definición de proceso XML). El nombre de archivo de flujo de trabajo predeterminado es *workflow.xml*.  Se guarde el archivo de flujo de trabajo localmente y luego se implementa en el clúster HDInsight con PowerShell de Azure más adelante en este tutorial.

La acción de la sección del flujo de trabajo llama a un archivo de script de HiveQL. Este archivo de comandos contiene tres HiveQL instrucciones:

1. **Quitar tabla de la instrucción** elimina la tabla subárbol log4j si existe.
2. **Instrucción CREATE TABLE de la** crea una tabla externa de subárbol log4j, que apunta a la ubicación del archivo de registro log4j;
3.  **La ubicación del archivo de registro log4j**. El delimitador de campo es ",". El delimitador de línea predeterminado es "\n". Una tabla de subárbol externa se utiliza para evitar el archivo de datos que se quita de su ubicación original, en caso de que desea ejecutar el flujo de trabajo Oozie varias veces.
3. **Insertar SOBRESCRIBIR la instrucción** cuenta las apariciones de cada tipo de nivel de registro de la tabla de la sección log4j y guarda el resultado en una ubicación de almacenamiento de blobs de Windows Azure.

**Nota**: hay un problema conocido de ruta de acceso de sección. Se ejecutará en este problema al enviar un trabajo Oozie. Pueden encontrar las instrucciones para solucionar el problema en la Wiki de TechNet: [HDInsight subárbol error: no se puede cambiar el nombre de][technetwiki-hive-error].

**Para definir el archivo de comandos HiveQL para llamar al flujo de trabajo**

1. Crear un archivo de texto con el contenido siguiente:

        DROP TABLE ${hiveTableName};
        CREATE EXTERNAL TABLE ${hiveTableName}(t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) ROW FORMAT DELIMITED FIELDS TERMINATED BY ' ' STORED AS TEXTFILE LOCATION '${hiveDataFolder}';
        INSERT OVERWRITE DIRECTORY '${hiveOutputFolder}' SELECT t4 AS sev, COUNT(*) AS cnt FROM ${hiveTableName} WHERE t4 LIKE '[%' GROUP BY t4;

    Hay tres variables utilizadas en la secuencia de comandos:

    - ${hiveTableName}
    - ${hiveDataFolder}
    - ${hiveOutputFolder}

    El archivo de definición de flujo de trabajo (workflow.xml en este tutorial) pasará estos valores para esta secuencia de comandos de HiveQL en tiempo de ejecución.

2. Guarde el archivo como **C:\Tutorials\UseOozie\useooziewf.hql** con codificación ANSI (ASCII). (Utilice el Bloc de notas si el editor de texto no proporciona esta opción). Este archivo de script se implementará en el clúster HDInsight más adelante en el tutorial.



**Para definir un flujo de trabajo**

1. Crear un archivo de texto con el contenido siguiente:

        <workflow-app name="useooziewf" xmlns="uri:oozie:workflow:0.2">
            <start to = "RunHiveScript"/>

            <action name="RunHiveScript">
                <hive xmlns="uri:oozie:hive-action:0.2">
                    <job-tracker>${jobTracker}</job-tracker>
                    <name-node>${nameNode}</name-node>
                    <configuration>
                        <property>
                            <name>mapred.job.queue.name</name>
                            <value>${queueName}</value>
                        </property>
                    </configuration>
                    <script>${hiveScript}</script>
                    <param>hiveTableName=${hiveTableName}</param>
                    <param>hiveDataFolder=${hiveDataFolder}</param>
                    <param>hiveOutputFolder=${hiveOutputFolder}</param>
                </hive>
                <ok to="RunSqoopExport"/>
                <error to="fail"/>
            </action>

            <action name="RunSqoopExport">
                <sqoop xmlns="uri:oozie:sqoop-action:0.2">
                    <job-tracker>${jobTracker}</job-tracker>
                    <name-node>${nameNode}</name-node>
                    <configuration>
                        <property>
                            <name>mapred.compress.map.output</name>
                            <value>true</value>
                        </property>
                    </configuration>
                <arg>export</arg>
                <arg>--connect</arg>
                <arg>${sqlDatabaseConnectionString}</arg>
                <arg>--table</arg>
                <arg>${sqlDatabaseTableName}</arg>
                <arg>--export-dir</arg>
                <arg>${hiveOutputFolder}</arg>
                <arg>-m</arg>
                <arg>1</arg>
                <arg>--input-fields-terminated-by</arg>
                <arg>"\001"</arg>
                </sqoop>
                <ok to="end"/>
                <error to="fail"/>
            </action>

            <kill name="fail">
                <message>Job failed, error message[${wf:errorMessage(wf:lastErrorNode())}] </message>
            </kill>

           <end name="end"/>
        </workflow-app>

    Hay dos acciones definidas en el flujo de trabajo. La acción de inicio es *RunHiveScript*. Si la acción ejecuta *Aceptar*, la siguiente acción es *RunSqoopExport*.

    El RunHiveScript tiene varias variables. Al enviar el trabajo Oozie desde su estación de trabajo con Azure PowerShell pasará los valores.

    <table border = "1">
    <tr><th>Variables de flujo de trabajo</th><th>Descripción</th></tr>
    <tr><td>${jobTracker}</td><td>Especifique la dirección URL de la herramienta de seguimiento del trabajo de Hadoop. Use <strong>jobtrackerhost:9010</strong> en la versión de clúster HDInsight 3.0 y 2.0.</td></tr>
    <tr><td>${nameNode}</td><td>Especifique la dirección URL del nodo de nombre Hadoop. Utilice la wasbs de sistema de archivo predeterminado: / / dirección, por ejemplo, <i>wasbs: / /&lt;nombre del contenedor&gt;@&lt;storageAccountName&gt;. blob.core.windows.net</i>.</td></tr>
    <tr><td>${Nombre}</td><td>Especifica el nombre de cola que el trabajo se enviarán a. Utilizar <strong>predeterminado</strong>.</td></tr>
    </table>


    <table border = "1">
    <tr><th>Sección variable de acción</th><th>Descripción</th></tr>
    <tr><td>${hiveDataFolder}</td><td>Directorio de origen para el comando de la sección Crear tabla.</td></tr>
    <tr><td>${hiveOutputFolder}</td><td>La carpeta de salida de la instrucción de insertar SOBRESCRIBIR.</td></tr>
    <tr><td>${hiveTableName}</td><td>El nombre de la tabla de la sección que hace referencia a los archivos de datos de log4j.</td></tr>
    </table>


    <table border = "1">
    <tr><th>Variable de acción Sqoop</th><th>Descripción</th></tr>
    <tr><td>${sqlDatabaseConnectionString}</td><td>Cadena de conexión de base de datos de SQL.</td></tr>
    <tr><td>${sqlDatabaseTableName}</td><td>La tabla de base de datos de SQL Azure a donde se exportarán los datos.</td></tr>
    <tr><td>${hiveOutputFolder}</td><td>La carpeta de salida de la instrucción subárbol insertar SOBRESCRIBIR. Esta es la misma carpeta para la exportación de Sqoop (exportación dir).</td></tr>
    </table>

    Para obtener más información sobre el flujo de trabajo de Oozie y el uso de las acciones de flujo de trabajo, consulte la [documentación de Apache Oozie 4.0] [ apache-oozie-400] (para la versión de clúster HDInsight 3.0) o la [documentación de Apache Oozie 3.3.2] [ apache-oozie-332] (para la versión de clúster HDInsight 2.1).

2. Guarde el archivo como **C:\Tutorials\UseOozie\workflow.xml** con codificación ANSI (ASCII). (Utilice el Bloc de notas si el editor de texto no proporciona esta opción).

**Para definir el coordinador**

1. Crear un archivo de texto con el contenido siguiente:

        <coordinator-app name="my_coord_app" frequency="${coordFrequency}" start="${coordStart}" end="${coordEnd}" timezone="${coordTimezone}" xmlns="uri:oozie:coordinator:0.4">
           <action>
              <workflow>
                 <app-path>${wfPath}</app-path>
              </workflow>
           </action>
        </coordinator-app>

    Hay cinco variables que se utilizan en el archivo de definición:

  	| Variable          | Descripción |
  	| ------------------|------------ |
  	| ${coordFrequency} | Tiempo de pausa de trabajo. Frecuencia siempre se expresa en minutos. |
  	| ${coordStart}     | Hora de inicio del trabajo. |
  	| ${coordEnd}       | Hora de finalización del trabajo. |
  	| ${coordTimezone}  | Oozie procesa trabajos coordinador en una zona horaria fijo con ningún horario de verano (normalmente representado usando UTC). Esta zona horaria se conoce como la "zona de horaria de procesamiento de Oozie". |
  	| ${wfPath}         | La ruta de acceso para el workflow.xml.  Si el nombre de archivo de flujo de trabajo no es el nombre de archivo predeterminado (workflow.xml), debe especificarlo. |

2. Guarde el archivo como **C:\Tutorials\UseOozie\coordinator.xml** utilizando la codificación de ANSI (ASCII). (Utilice el Bloc de notas si el editor de texto no proporciona esta opción).

##<a name="deploy-the-oozie-project-and-prepare-the-tutorial"></a>Implementar el proyecto Oozie y preparar el tutorial

Se ejecutará una secuencia de comandos de PowerShell de Azure para llevar a cabo lo siguiente:

- Copiar la secuencia de comandos de HiveQL (useoozie.hql) con el almacenamiento de blobs de Windows Azure, wasbs:///tutorials/useoozie/useoozie.hql.
- Copie el archivo workflow.xml en wasbs:///tutorials/useoozie/workflow.xml.
- Copie coordinator.xml en wasbs:///tutorials/useoozie/coordinator.xml.
- Copie el archivo de datos (o example/data/sample.log) a wasbs:///tutorials/useoozie/data/sample.log.
- Crear una tabla de base de datos de SQL Azure para almacenar los datos de exportación de Sqoop. El nombre de tabla es *log4jLogCount*.

**Comprender el almacenamiento de HDInsight**

HDInsight usa almacenamiento de blobs de Windows Azure almacenamiento de datos. wasbs: / / es la implementación de Microsoft del sistema de archivos distribuido Hadoop (HDFS) en el almacenamiento de blobs de Windows Azure. Para obtener más información, vea [almacenamiento de blobs de Azure uso con HDInsight][hdinsight-storage].

Cuando se aprovisiona un clúster de HDInsight, una cuenta de almacenamiento de blobs de Windows Azure y un contenedor desde esa cuenta está designado como el sistema de archivos de forma predeterminada, al igual que en HDFS. Además de esta cuenta de almacenamiento, puede agregar cuentas de almacenamiento adicional de la misma suscripción Azure o de varias suscripciones a Azure durante el proceso de aprovisionamiento. Para obtener instrucciones sobre cómo agregar cuentas de almacenamiento adicional, vea [clústeres aprovisionar HDInsight][hdinsight-provision]. Para simplificar el script de PowerShell de Azure utilizado en este tutorial, todos los archivos se almacenan en el contenedor de sistema de archivo predeterminado que se encuentra en */tutorials/useoozie*. De forma predeterminada, este contenedor tiene el mismo nombre que el nombre de clúster HDInsight.
La sintaxis es:

    wasb[s]://<ContainerName>@<StorageAccountName>.blob.core.windows.net/<path>/<filename>

> [AZURE.NOTE] Solo el *wasb: / /* sintaxis es compatible con la versión de clúster HDInsight 3.0. La versión más antigua *asv: / /* sintaxis es compatible con HDInsight 2.1 y 1,6 clústeres, pero no se admite en clústeres HDInsight 3.0.

> [AZURE.NOTE] La wasb: / / ruta de acceso es virtual. Para obtener más información, vea [almacenamiento de blobs de Azure uso con HDInsight][hdinsight-storage].

Puede tener acceso a un archivo almacenado en el contenedor de sistema de archivo predeterminado desde HDInsight con cualquiera de los siguientes URI (estoy usando workflow.xml como ejemplo):

    wasbs://mycontainer@mystorageaccount.blob.core.windows.net/tutorials/useoozie/workflow.xml
    wasbs:///tutorials/useoozie/workflow.xml
    /tutorials/useoozie/workflow.xml

Si desea tener acceso al archivo directamente desde la cuenta de almacenamiento, es el nombre de blobs para el archivo:

    tutorials/useoozie/workflow.xml

**Comprender tablas de subárbol internos y externos**

Hay algunas cosas que debe saber acerca de las tablas de subárbol internos y externos:

- El comando Crear tabla crea una tabla interna, también conocido como una tabla administrada. El archivo de datos debe estar ubicado en el contenedor predeterminado.
- El comando Crear tabla mueve el archivo de datos a /hive/almacén/<TableName> carpeta en el contenedor predeterminado.
- El comando Crear tabla externa crea una tabla externa. El archivo de datos puede encontrarse fuera del contenedor de forma predeterminada.
- El comando Crear tablas externas no mueva el archivo de datos.
- El comando Crear tablas externas no permite las subcarpetas en la carpeta que se especifica en la cláusula de ubicación. Esta es la razón por qué el tutorial hace una copia del archivo sample.log.

Para obtener más información, consulte [HDInsight: subárbol internos y externos introducción de tablas][cindygross-hive-tables].

**Para preparar el tutorial**

1. Abrir Windows PowerShell ISE (en la pantalla de inicio de Windows 8, escriba **PowerShell_ISE**y, a continuación, haga clic en **Windows PowerShell ISE**. Para obtener más información, vea [Iniciar Windows PowerShell en Windows 8 y Windows][powershell-start]).
2. En el panel inferior, ejecute el comando siguiente para conectarse a su suscripción de Azure:

        Add-AzureAccount

    Se le pedirá que introduzca sus credenciales de cuenta de Azure. Este método para agregar una conexión de suscripción agota el tiempo y, después de 12 horas, tendrá que ejecute el cmdlet de nuevo.

    > [AZURE.NOTE] Si tiene varias suscripciones de Azure y la suscripción predeterminada no es la que desea usar, use el cmdlet <strong>AzureSubscription seleccione</strong> para seleccionar una suscripción.

3. Copie la siguiente secuencia de comandos en el panel de secuencia de comandos y, a continuación, establezca las seis variables:

        # WASB variables
        $storageAccountName = "<StorageAccountName>"
        $containerName = "<BlobStorageContainerName>"

        # SQL database variables
        $sqlDatabaseServer = "<SQLDatabaseServerName>"  
        $sqlDatabaseLogin = "<SQLDatabaseLoginName>"
        $sqlDatabaseLoginPassword = "SQLDatabaseLoginPassword>"
        $sqlDatabaseName = "<SQLDatabaseName>"  
        $sqlDatabaseTableName = "log4jLogsCount"

        # Oozie files for the tutorial
        $hiveQLScript = "C:\Tutorials\UseOozie\useooziewf.hql"
        $workflowDefinition = "C:\Tutorials\UseOozie\workflow.xml"
        $coordDefinition =  "C:\Tutorials\UseOozie\coordinator.xml"

        # WASB folder for storing the Oozie tutorial files.
        $destFolder = "tutorials/useoozie"  # Do NOT use the long path here


    Para obtener más descripciones de las variables, consulte la sección [requisitos previos](#prerequisites) de este tutorial.

3. Anexar los siguientes a la secuencia de comandos en el panel de secuencia de comandos:

        # Create a storage context object
        $storageaccountkey = get-azurestoragekey $storageAccountName | %{$_.Primary}
        $destContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageaccountkey

        function uploadOozieFiles()
        {
            Write-Host "Copy HiveQL script, workflow definition and coordinator definition ..." -ForegroundColor Green
            Set-AzureStorageBlobContent -File $hiveQLScript -Container $containerName -Blob "$destFolder/useooziewf.hql" -Context $destContext
            Set-AzureStorageBlobContent -File $workflowDefinition -Container $containerName -Blob "$destFolder/workflow.xml" -Context $destContext
            Set-AzureStorageBlobContent -File $coordDefinition -Container $containerName -Blob "$destFolder/coordinator.xml" -Context $destContext
        }

        function prepareHiveDataFile()
        {
            Write-Host "Make a copy of the sample.log file ... " -ForegroundColor Green
            Start-CopyAzureStorageBlob -SrcContainer $containerName -SrcBlob "example/data/sample.log" -Context $destContext -DestContainer $containerName -destBlob "$destFolder/data/sample.log" -DestContext $destContext
        }

        function prepareSQLDatabase()
        {
            # SQL query string for creating log4jLogsCount table
            $cmdCreateLog4jCountTable = " CREATE TABLE [dbo].[$sqlDatabaseTableName](
                    [Level] [nvarchar](10) NOT NULL,
                    [Total] float,
                CONSTRAINT [PK_$sqlDatabaseTableName] PRIMARY KEY CLUSTERED
                (
                [Level] ASC
                )
                )"

            #Create the log4jLogsCount table
            Write-Host "Create Log4jLogsCount table ..." -ForegroundColor Green
            $conn = New-Object System.Data.SqlClient.SqlConnection
            $conn.ConnectionString = "Data Source=$sqlDatabaseServer.database.windows.net;Initial Catalog=$sqlDatabaseName;User ID=$sqlDatabaseLogin;Password=$sqlDatabaseLoginPassword;Encrypt=true;Trusted_Connection=false;"
            $conn.open()
            $cmd = New-Object System.Data.SqlClient.SqlCommand
            $cmd.connection = $conn
            $cmd.commandtext = $cmdCreateLog4jCountTable
            $cmd.executenonquery()

            $conn.close()
        }

        # upload workflow.xml, coordinator.xml, and ooziewf.hql
        uploadOozieFiles;

        # make a copy of example/data/sample.log to example/data/log4j/sample.log
        prepareHiveDataFile;

        # create log4jlogsCount table on SQL database
        prepareSQLDatabase;

4. Haga clic en **Ejecutar secuencia de comandos** o presione **F5** para ejecutar la secuencia de comandos. El resultado será similar al:

    ![Resultados de preparación tutorial][img-preparation-output]

##<a name="run-the-oozie-project"></a>Ejecutar el proyecto Oozie

PowerShell Azure actualmente no proporciona los cmdlets para definir Oozie trabajos. Puede usar el cmdlet **Invocar RestMethod** invocar servicios web de Oozie. La API de servicios web Oozie es una API de JSON del resto de HTTP. Para obtener más información acerca de los servicios web de Oozie API, consulte la [documentación Apache Oozie 4.0] [ apache-oozie-400] (para la versión de clúster HDInsight 3.0) o la [documentación de Apache Oozie 3.3.2] [ apache-oozie-332] (para la versión de clúster HDInsight 2.1).

**Para enviar un trabajo Oozie**

1. Abrir Windows PowerShell ISE (en la pantalla de inicio de Windows 8, escriba **PowerShell_ISE**y, a continuación, haga clic en **Windows PowerShell ISE**. Para obtener más información, vea [Iniciar Windows PowerShell en Windows 8 y Windows][powershell-start]).

3. Copie la siguiente secuencia de comandos en el panel de secuencia de comandos y, a continuación, establezca las variables primero 14 (sin embargo, omitir **$storageUri**).

        #HDInsight cluster variables
        $clusterName = "<HDInsightClusterName>"
        $clusterUsername = "<HDInsightClusterUsername>"
        $clusterPassword = "<HDInsightClusterUserPassword>"

        #Azure Blob storage (WASB) variables
        $storageAccountName = "<StorageAccountName>"
        $storageContainerName = "<BlobContainerName>"
        $storageUri="wasbs://$storageContainerName@$storageAccountName.blob.core.windows.net"

        #Azure SQL database variables
        $sqlDatabaseServer = "<SQLDatabaseServerName>"
        $sqlDatabaseLogin = "<SQLDatabaseLoginName>"
        $sqlDatabaseLoginPassword = "<SQLDatabaseloginPassword>"
        $sqlDatabaseName = "<SQLDatabaseName>"  

        #Oozie WF/coordinator variables
        $coordStart = "2014-03-21T13:45Z"
        $coordEnd = "2014-03-21T13:45Z"
        $coordFrequency = "1440"    # in minutes, 24h x 60m = 1440m
        $coordTimezone = "UTC"  #UTC/GMT

        $oozieWFPath="$storageUri/tutorials/useoozie"  # The default name is workflow.xml. And you don't need to specify the file name.
        $waitTimeBetweenOozieJobStatusCheck=10

        #Hive action variables
        $hiveScript = "$storageUri/tutorials/useoozie/useooziewf.hql"
        $hiveTableName = "log4jlogs"
        $hiveDataFolder = "$storageUri/tutorials/useoozie/data"
        $hiveOutputFolder = "$storageUri/tutorials/useoozie/output"

        #Sqoop action variables
        $sqlDatabaseConnectionString = "jdbc:sqlserver://$sqlDatabaseServer.database.windows.net;user=$sqlDatabaseLogin@$sqlDatabaseServer;password=$sqlDatabaseLoginPassword;database=$sqlDatabaseName"
        $sqlDatabaseTableName = "log4jLogsCount"

        $passwd = ConvertTo-SecureString $clusterPassword -AsPlainText -Force
        $creds = New-Object System.Management.Automation.PSCredential ($clusterUsername, $passwd)

    Para obtener más descripciones de las variables, consulte la sección [requisitos previos](#prerequisites) de este tutorial.

    $coordstart y $coordend son el flujo de trabajo de inicio y hora de finalización. Para buscar la hora UTC/GMT, busque "hora utc" en bing.com. El $coordFrequency es la frecuencia en minutos que desea ejecutar el flujo de trabajo.

3. Anexar los siguientes a la secuencia de comandos. Este elemento define la carga Oozie:

        #OoziePayload used for Oozie web service submission
        $OoziePayload =  @"
        <?xml version="1.0" encoding="UTF-8"?>
        <configuration>

           <property>
               <name>nameNode</name>
               <value>$storageUrI</value>
           </property>

           <property>
               <name>jobTracker</name>
               <value>jobtrackerhost:9010</value>
           </property>

           <property>
               <name>queueName</name>
               <value>default</value>
           </property>

           <property>
               <name>oozie.use.system.libpath</name>
               <value>true</value>
           </property>

           <property>
               <name>oozie.coord.application.path</name>
               <value>$oozieWFPath</value>
           </property>

           <property>
               <name>wfPath</name>
               <value>$oozieWFPath</value>
           </property>

           <property>
               <name>coordStart</name>
               <value>$coordStart</value>
           </property>

           <property>
               <name>coordEnd</name>
               <value>$coordEnd</value>
           </property>

           <property>
               <name>coordFrequency</name>
               <value>$coordFrequency</value>
           </property>

           <property>
               <name>coordTimezone</name>
               <value>$coordTimezone</value>
           </property>

           <property>
               <name>hiveScript</name>
               <value>$hiveScript</value>
           </property>

           <property>
               <name>hiveTableName</name>
               <value>$hiveTableName</value>
           </property>

           <property>
               <name>hiveDataFolder</name>
               <value>$hiveDataFolder</value>
           </property>

           <property>
               <name>hiveOutputFolder</name>
               <value>$hiveOutputFolder</value>
           </property>

           <property>
               <name>sqlDatabaseConnectionString</name>
               <value>&quot;$sqlDatabaseConnectionString&quot;</value>
           </property>

           <property>
               <name>sqlDatabaseTableName</name>
               <value>$SQLDatabaseTableName</value>
           </property>

           <property>
               <name>user.name</name>
               <value>admin</value>
           </property>

        </configuration>
        "@

    >[AZURE.NOTE] La diferencia principal en comparación con el archivo de carga de envío de flujo de trabajo es la variable **oozie.coord.application.path**. Cuando envíe una tarea de flujo de trabajo, use **oozie.wf.application.path** en su lugar.

4. Anexar los siguientes a la secuencia de comandos. Este elemento comprueba el estado del servicio web Oozie:

        function checkOozieServerStatus()
        {
            Write-Host "Checking Oozie server status..." -ForegroundColor Green
            $clusterUriStatus = "https://$clusterName.azurehdinsight.net:443/oozie/v2/admin/status"
            $response = Invoke-RestMethod -Method Get -Uri $clusterUriStatus -Credential $creds -OutVariable $OozieServerStatus

            $jsonResponse = ConvertFrom-Json (ConvertTo-Json -InputObject $response)
            $oozieServerSatus = $jsonResponse[0].("systemMode")
            Write-Host "Oozie server status is $oozieServerSatus..."

            if($oozieServerSatus -notmatch "NORMAL")
            {
                Write-Host "Oozie server status is $oozieServerSatus...cannot submit Oozie jobs. Check the server status and re-run the job."
                exit 1
            }
        }

5. Anexar los siguientes a la secuencia de comandos. En este artículo, se creará un trabajo Oozie:

        function createOozieJob()
        {
            # create Oozie job
            Write-Host "Sending the following Payload to the cluster:" -ForegroundColor Green
            Write-Host "`n--------`n$OoziePayload`n--------"
            $clusterUriCreateJob = "https://$clusterName.azurehdinsight.net:443/oozie/v2/jobs"
            $response = Invoke-RestMethod -Method Post -Uri $clusterUriCreateJob -Credential $creds -Body $OoziePayload -ContentType "application/xml" -OutVariable $OozieJobName -debug -Verbose

            $jsonResponse = ConvertFrom-Json (ConvertTo-Json -InputObject $response)
            $oozieJobId = $jsonResponse[0].("id")
            Write-Host "Oozie job id is $oozieJobId..."

            return $oozieJobId
        }

    > [AZURE.NOTE] Cuando envíe una tarea de flujo de trabajo, debe realizar otro servicio web llamar para iniciar el trabajo una vez creado el trabajo. En este caso, se activa el trabajo del coordinador por hora. El trabajo se iniciará automáticamente.

6. Anexar los siguientes a la secuencia de comandos. Este elemento comprueba el estado del trabajo Oozie:

        function checkOozieJobStatus($oozieJobId)
        {
            # get job status
            Write-Host "Sleeping for $waitTimeBetweenOozieJobStatusCheck seconds until the job metadata is populated in the Oozie metastore..." -ForegroundColor Green
            Start-Sleep -Seconds $waitTimeBetweenOozieJobStatusCheck

            Write-Host "Getting job status and waiting for the job to complete..." -ForegroundColor Green
            $clusterUriGetJobStatus = "https://$clusterName.azurehdinsight.net:443/oozie/v2/job/" + $oozieJobId + "?show=info"
            $response = Invoke-RestMethod -Method Get -Uri $clusterUriGetJobStatus -Credential $creds
            $jsonResponse = ConvertFrom-Json (ConvertTo-Json -InputObject $response)
            $JobStatus = $jsonResponse[0].("status")

            while($JobStatus -notmatch "SUCCEEDED|KILLED")
            {
                Write-Host "$(Get-Date -format 'G'): $oozieJobId is in $JobStatus state...waiting $waitTimeBetweenOozieJobStatusCheck seconds for the job to complete..."
                Start-Sleep -Seconds $waitTimeBetweenOozieJobStatusCheck
                $response = Invoke-RestMethod -Method Get -Uri $clusterUriGetJobStatus -Credential $creds
                $jsonResponse = ConvertFrom-Json (ConvertTo-Json -InputObject $response)
                $JobStatus = $jsonResponse[0].("status")
            }

            Write-Host "$(Get-Date -format 'G'): $oozieJobId is in $JobStatus state!"
            if($JobStatus -notmatch "SUCCEEDED")
            {
                Write-Host "Check logs at http://headnode0:9014/cluster for detais."
                exit -1
            }
        }

7. (Opcional) Anexar los siguientes a la secuencia de comandos.

        function listOozieJobs()
        {
            Write-Host "Listing Oozie jobs..." -ForegroundColor Green
            $clusterUriStatus = "https://$clusterName.azurehdinsight.net:443/oozie/v2/jobs"
            $response = Invoke-RestMethod -Method Get -Uri $clusterUriStatus -Credential $creds

            write-host "Job ID                                   App Name        Status      Started                         Ended"
            write-host "----------------------------------------------------------------------------------------------------------------------------------"
            foreach($job in $response.workflows)
            {
                Write-Host $job.id "`t" $job.appName "`t" $job.status "`t" $job.startTime "`t" $job.endTime
            }
        }

        function ShowOozieJobLog($oozieJobId)
        {
            Write-Host "Showing Oozie job info..." -ForegroundColor Green
            $clusterUriStatus = "https://$clusterName.azurehdinsight.net:443/oozie/v2/job/$oozieJobId" + "?show=log"
            $response = Invoke-RestMethod -Method Get -Uri $clusterUriStatus -Credential $creds
            write-host $response
        }

        function killOozieJob($oozieJobId)
        {
            Write-Host "Killing the Oozie job $oozieJobId..." -ForegroundColor Green
            $clusterUriStartJob = "https://$clusterName.azurehdinsight.net:443/oozie/v2/job/" + $oozieJobId + "?action=kill" #Valid values for the 'action' parameter are 'start', 'suspend', 'resume', 'kill', 'dryrun', 'rerun', and 'change'.
            $response = Invoke-RestMethod -Method Put -Uri $clusterUriStartJob -Credential $creds | Format-Table -HideTableHeaders -debug
        }

7. Anexar los siguientes a la secuencia de comandos:

        checkOozieServerStatus
        # listOozieJobs
        $oozieJobId = createOozieJob($oozieJobId)
        checkOozieJobStatus($oozieJobId)
        # ShowOozieJobLog($oozieJobId)
        # killOozieJob($oozieJobId)

    Quitar los signos # si desea ejecutar las funciones adicionales.

7. Si el clúster HDinsight es la versión 2.1, reemplace "https://$clusterName.azurehdinsight.net:443/oozie/v2/" con "https://$clusterName.azurehdinsight.net:443/oozie/v1/". Versión de clúster HDInsight 2.1 no no es compatible con la versión 2 de los servicios web.

7. Haga clic en **Ejecutar secuencia de comandos** o presione **F5** para ejecutar la secuencia de comandos. El resultado será similar al:

    ![Tutorial ejecutar salida de flujo de trabajo][img-runworkflow-output]

8. Conectarse a la base de datos de SQL para ver los datos exportados.

**Para comprobar el registro de errores de trabajo**

Para solucionar un flujo de trabajo, el archivo de registro Oozie puede encontrarse en C:\apps\dist\oozie-3.3.2.1.3.2.0-05\oozie-win-distro\logs\Oozie.log de headnode de clúster. Para obtener información sobre RDP, consulte [clústeres de HDInsight de administración con el portal de Azure][hdinsight-admin-portal].

**Para volver a ejecutar el tutorial**

Para volver a ejecutar el flujo de trabajo, debe realizar las siguientes tareas:

- Elimine el archivo de salida de la secuencia de comandos de sección.
- Eliminar los datos de la tabla log4jLogsCount.

Aquí es una secuencia de comandos de Windows PowerShell de muestra que puede usar:

    $storageAccountName = "<AzureStorageAccountName>"
    $containerName = "<ContainerName>"

    #SQL database variables
    $sqlDatabaseServer = "<SQLDatabaseServerName>"
    $sqlDatabaseLogin = "<SQLDatabaseLoginName>"
    $sqlDatabaseLoginPassword = "<SQLDatabaseLoginPassword>"
    $sqlDatabaseName = "<SQLDatabaseName>"
    $sqlDatabaseTableName = "log4jLogsCount"

    Write-host "Delete the Hive script output file ..." -ForegroundColor Green
    $storageaccountkey = get-azurestoragekey $storageAccountName | %{$_.Primary}
    $destContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageaccountkey
    Remove-AzureStorageBlob -Context $destContext -Blob "tutorials/useoozie/output/000000_0" -Container $containerName

    Write-host "Delete all the records from the log4jLogsCount table ..." -ForegroundColor Green
    $conn = New-Object System.Data.SqlClient.SqlConnection
    $conn.ConnectionString = "Data Source=$sqlDatabaseServer.database.windows.net;Initial Catalog=$sqlDatabaseName;User ID=$sqlDatabaseLogin;Password=$sqlDatabaseLoginPassword;Encrypt=true;Trusted_Connection=false;"
    $conn.open()
    $cmd = New-Object System.Data.SqlClient.SqlCommand
    $cmd.connection = $conn
    $cmd.commandtext = "delete from $sqlDatabaseTableName"
    $cmd.executenonquery()

    $conn.close()


##<a name="next-steps"></a>Pasos siguientes
En este tutorial, ha aprendido cómo definir un flujo de trabajo Oozie y un coordinador Oozie y cómo ejecutar un trabajo de coordinador Oozie con PowerShell de Azure. Para obtener más información, consulte los artículos siguientes:

- [Introducción a HDInsight][hdinsight-get-started]
- [Usar el almacenamiento de blobs de Windows Azure con HDInsight][hdinsight-storage]
- [Administrar HDInsight con PowerShell de Azure][hdinsight-admin-powershell]
- [Cargar datos a HDInsight][hdinsight-upload-data]
- [Usar Sqoop con HDInsight][hdinsight-use-sqoop]
- [Usar subárbol con HDInsight][hdinsight-use-hive]
- [Usar cerdo con HDInsight][hdinsight-use-pig]
- [Desarrollar programas Java MapReduce para HDInsight][hdinsight-develop-java-mapreduce]



[hdinsight-cmdlets-download]: http://go.microsoft.com/fwlink/?LinkID=325563


[hdinsight-versions]:  hdinsight-component-versioning.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md
[hdinsight-get-started]: hdinsight-hadoop-linux-tutorial-get-started.md
[hdinsight-admin-portal]: hdinsight-administer-use-management-portal.md


[hdinsight-use-sqoop]: hdinsight-use-sqoop.md
[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-admin-powershell]: hdinsight-administer-use-powershell.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-pig]: hdinsight-use-pig.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md
[hdinsight-develop-java-mapreduce]: hdinsight-develop-deploy-java-mapreduce-linux.md
[hdinsight-use-oozie]: hdinsight-use-oozie.md

[sqldatabase-get-started]: ../sql-database/sql-database-get-started.md

[azure-management-portal]: https://portal.azure.com/
[azure-create-storageaccount]: ../storage-create-storage-account.md

[apache-hadoop]: http://hadoop.apache.org/
[apache-oozie-400]: http://oozie.apache.org/docs/4.0.0/
[apache-oozie-332]: http://oozie.apache.org/docs/3.3.2/

[powershell-download]: http://azure.microsoft.com/downloads/
[powershell-about-profiles]: http://go.microsoft.com/fwlink/?LinkID=113729
[powershell-install-configure]: ../powershell-install-configure.md
[powershell-start]: http://technet.microsoft.com/library/hh847889.aspx
[powershell-script]: http://technet.microsoft.com/library/ee176949.aspx

[cindygross-hive-tables]: http://blogs.msdn.com/b/cindygross/archive/2013/02/06/hdinsight-hive-internal-and-external-tables-intro.aspx

[img-workflow-diagram]: ./media/hdinsight-use-oozie-coordinator-time/HDI.UseOozie.Workflow.Diagram.png
[img-preparation-output]: ./media/hdinsight-use-oozie-coordinator-time/HDI.UseOozie.Preparation.Output1.png  
[img-runworkflow-output]: ./media/hdinsight-use-oozie-coordinator-time/HDI.UseOozie.RunCoord.Output.png  

[technetwiki-hive-error]: http://social.technet.microsoft.com/wiki/contents/articles/23047.hdinsight-hive-error-unable-to-rename.aspx
