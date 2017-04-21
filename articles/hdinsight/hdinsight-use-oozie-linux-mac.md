<properties
    pageTitle="Usar flujos de trabajo de Hadoop Oozie en HDInsight de Linux | Microsoft Azure"
    description="Usar Hadoop Oozie en HDInsight de Linux. Obtenga información sobre cómo definir un flujo de trabajo Oozie y enviar un trabajo Oozie."
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
    ms.date="10/11/2016"
    ms.author="larryfr"/>


# <a name="use-oozie-with-hadoop-to-define-and-run-a-workflow-on-linux-based-hdinsight"></a>Use Oozie con Hadoop para definir y ejecutar un flujo de trabajo basados en Linux HDInsight

[AZURE.INCLUDE [oozie-selector](../../includes/hdinsight-oozie-selector.md)]

Obtenga información sobre cómo usar Apache Oozie para definir un flujo de trabajo que usa la sección y Sqoop y, a continuación, ejecutar el flujo de trabajo en un clúster de HDInsight basados en Linux.

Apache Oozie es un sistema de flujo de trabajo o junto a la que administra Hadoop trabajos. Se integra con la pila Hadoop y admite Hadoop trabajos de Apache MapReduce, Apache cerdo, Apache subárbol y Apache Sqoop. También puede usarse para programar trabajos que son específicos de un sistema, como los programas de Java o secuencias de comandos de shell

> [AZURE.NOTE] Otra opción para definir los flujos de trabajo con HDInsight es el generador de datos de Azure. Para obtener más información sobre el generador de datos de Azure, vea [usar cerdo y subárbol con el generador de datos][azure-data-factory-pig-hive].

##<a name="prerequisites"></a>Requisitos previos

Antes de comenzar este tutorial, debe tener el siguiente:

- **Suscripción de un Azure**: vea la [versión de prueba gratuita de obtener Azure](https://azure.microsoft.com/pricing/free-trial/).

- **Azure CLI**: consulte [instalar y configurar la CLI de Azure](../xplat-cli-install.md)
    
    [AZURE.INCLUDE [use-latest-version](../../includes/hdinsight-use-latest-cli.md)]

- **HDInsight un clúster**: consulte [Introducción a HDInsight en Linux](hdinsight-hadoop-linux-tutorial-get-started.md)

- **Base de datos de SQL Azure**: Esto se crearán siguiendo los pasos de este documento

##<a name="example-workflow"></a>Flujo de trabajo de ejemplo

El flujo de trabajo que se implementará siguiendo las instrucciones de este documento contiene dos acciones. Acciones son definiciones de tareas, como la ejecución de sección, Sqoop, MapReduce u otro proceso:

![Diagrama de flujo de trabajo][img-workflow-diagram]

1. Una acción de la sección ejecuta una secuencia de comandos HiveQL para extraer los registros de la **hivesampletable** incluido con HDInsight. Cada fila de datos describe una visita desde un dispositivo móvil específico. El formato de registro aparece similar al siguiente:

        8       18:54:20        en-US   Android Samsung SCH-i500        California     United States    13.9204007      0       0
        23      19:19:44        en-US   Android HTC     Incredible      Pennsylvania   United States    NULL    0       0
        23      19:19:46        en-US   Android HTC     Incredible      Pennsylvania   United States    1.4757422       0       1

    La secuencia de comandos de la sección usado en este documento cuenta el número total de visitas para cada plataforma (por ejemplo, Android o iPhone) y almacena los recuentos a una nueva tabla subárbol.

    Para obtener más información acerca de la sección, vea [Usar subárbol con HDInsight][hdinsight-use-hive].

2.  Una acción Sqoop exporta el contenido de la nueva tabla subárbol a una tabla en una base de datos de SQL Azure. Para obtener más información sobre Sqoop, vea [Usar Hadoop Sqoop con HDInsight][hdinsight-use-sqoop].

> [AZURE.NOTE] Para versiones Oozie compatibles en clústeres HDInsight, consulte [Novedades en las versiones de clúster Hadoop proporcionadas por HDInsight?] [hdinsight-versions].

##<a name="create-the-working-directory"></a>Crear el directorio de trabajo

Oozie espera recursos necesarios para que una tarea para almacenarse en el mismo directorio. Este ejemplo usa **wasbs: / / / tutoriales/useoozie**. Utilice el comando siguiente para crear este directorio y el directorio de datos que contendrá la nueva tabla subárbol creada por este flujo de trabajo:

    hdfs dfs -mkdir -p /tutorials/useoozie/data

> [AZURE.NOTE] La `-p` parámetro causado todos los directorios de la ruta de acceso que debe crearse si aún no existe. Se utilizará el directorio de **datos** que contienen datos usados por la secuencia de comandos de **useooziewf.hql** .

También ejecutar el comando siguiente, lo que garantiza que Oozie puede representar la cuenta de usuario cuando se ejecutan trabajos de sección y Sqoop. Reemplazar el **nombre de usuario** con el nombre de inicio de sesión:

    sudo adduser USERNAME users

Si recibe un error que el usuario ya es miembro de usuarios, puede omitir solo.

##<a name="add-a-database-driver"></a>Agregar un controlador de base de datos

Dado que este flujo de trabajo usa Sqoop para exportar datos a la base de datos de SQL, debe proporcionar una copia del controlador JDBC que se utiliza para comunicarse con la base de datos de SQL. Use el comando siguiente para copiarlo en el directorio de trabajo:

    hdfs dfs -copyFromLocal /usr/share/java/sqljdbc_4.1/enu/sqljdbc*.jar /tutorials/useoozie/

Si el flujo de trabajo usa otros recursos, como un jar que contiene una aplicación MapReduce, necesarias para agregar estos también.

##<a name="define-the-hive-query"></a>Definir la consulta de sección

Use los siguientes pasos para crear una secuencia de comandos de HiveQL que define una consulta que se usará en un flujo de trabajo de Oozie más adelante en este documento.

1. Usar SSH para conectarse a HDInsight basados en Linux clúster:

    * **Clientes Linux, Unix o OS X**: vea [Usar SSH con basados en Linux Hadoop en HDInsight de Linux, OS X o Unix](hdinsight-hadoop-linux-use-ssh-unix.md)

    * **Los clientes de Windows**: vea [Usar SSH con basados en Linux Hadoop en HDInsight de Windows.](hdinsight-hadoop-linux-use-ssh-windows.md)

2. Use el comando siguiente para crear un nuevo archivo:

        nano useooziewf.hql

1. Una vez que se abre el editor de nano, utilice la siguiente como el contenido del archivo:

        DROP TABLE ${hiveTableName};
        CREATE EXTERNAL TABLE ${hiveTableName}(deviceplatform string, count string) ROW FORMAT DELIMITED
        FIELDS TERMINATED BY '\t' STORED AS TEXTFILE LOCATION '${hiveDataFolder}';
        INSERT OVERWRITE TABLE ${hiveTableName} SELECT deviceplatform, COUNT(*) as count FROM hivesampletable GROUP BY deviceplatform;

    Hay dos variables que se usan en la secuencia de comandos:

    - **${hiveTableName}**: contendrá el nombre de la tabla que debe crearse
    - **${hiveDataFolder}**: contendrá la ubicación para almacenar los archivos de datos de la tabla

    El archivo de definición de flujo de trabajo (workflow.xml en este tutorial) pasa estos valores para esta secuencia de comandos de HiveQL en tiempo de ejecución.

2. Presione Ctrl-X para salir del editor. Cuando se le solicite, seleccione **Y** para guardar el archivo y luego use **ENTRAR** para usar el nombre de archivo **useooziewf.hql** .

3. Use los comandos siguientes para copiar **useooziewf.hql** a **wasbs:///tutorials/useoozie/useooziewf.hql**:

        hdfs dfs -copyFromLocal useooziewf.hql /tutorials/useoozie/useooziewf.hql

    Estos comandos almacenan el archivo de **useooziewf.hql** en la cuenta de almacenamiento de Azure asociada a este clúster, que se conservará el archivo, incluso si se elimina el clúster. Esto le permite ahorrar dinero eliminando clústeres cuando no están en uso, conservando los trabajos y flujos de trabajo.

##<a name="define-the-workflow"></a>Definir el flujo de trabajo

Definiciones de Oozie flujos de trabajo se escriben en hPDL (un lenguaje de definición de proceso XML). Siga los pasos siguientes para definir el flujo de trabajo:

1. Use la siguiente instrucción para crear y editar un archivo nuevo:

        nano workflow.xml

1. Una vez que se abre el editor de nano, escriba lo siguiente como el contenido del archivo:

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
                <arg>${hiveDataFolder}</arg>
                <arg>-m</arg>
                <arg>1</arg>
                <arg>--input-fields-terminated-by</arg>
                <arg>"\t"</arg>
                <archive>sqljdbc41.jar</archive>
                </sqoop>
            <ok to="end"/>
            <error to="fail"/>
            </action>
            <kill name="fail">
            <message>Job failed, error message[${wf:errorMessage(wf:lastErrorNode())}] </message>
            </kill>
            <end name="end"/>
        </workflow-app>

    Hay dos acciones definidas en el flujo de trabajo:

    - **RunHiveScript**: esta es la acción iniciar y se ejecuta la secuencia de comandos de subárbol **useooziewf.hql**

    - **RunSqoopExport**: exporta los datos creados a partir de la secuencia de comandos de la sección a la base de datos SQL Sqoop. Esto solo se ejecutará si la acción **RunHiveScript** es correcta.

        > [AZURE.NOTE] Para obtener más información sobre el flujo de trabajo de Oozie y usar acciones de flujo de trabajo, consulte la [documentación de Apache Oozie 4.0] [ apache-oozie-400] (para HDInsight versión 3.0) o la [documentación de Apache Oozie 3.3.2] [ apache-oozie-332] (para HDInsight versión 2.1).

    Tenga en cuenta que el flujo de trabajo tiene varias entradas, como por ejemplo `${jobTracker}`, que se reemplazará por valores que usar en la definición de trabajos más adelante en este documento.

    Tenga en cuenta también el `<archive>sqljdbc4.jar</arcive>` entrada en la sección Sqoop. Esto indica a Oozie para hacer este archivo para Sqoop cuando se ejecuta esta acción.

2. Utilice Ctrl-X, después **s** y **ENTRAR** para guardar el archivo.

3. Use el comando siguiente para copiar el archivo **workflow.xml** en **wasbs:///tutorials/useoozie/workflow.xml**:

        hdfs dfs -copyFromLocal workflow.xml /tutorials/useoozie/workflow.xml

##<a name="create-the-database"></a>Crear la base de datos

Siga los pasos en el documento de [crear una base de datos de SQL](../sql-database/sql-database-get-started.md) para crear una nueva base de datos. Al crear la base de datos, use __oozietest__ como nombre de la base de datos. También anote el nombre utilizado para el servidor de base de datos, como esta será necesaria en la siguiente sección.

###<a name="create-the-table"></a>Crear la tabla

> [AZURE.NOTE] Hay muchas formas de conectarse a la base de datos de SQL para crear una tabla. Los siguientes pasos usar [FreeTDS](http://www.freetds.org/) desde el clúster HDInsight.

3. Use el comando siguiente para instalar FreeTDS en el clúster de HDInsight:

        sudo apt-get --assume-yes install freetds-dev freetds-bin

4. Una vez se ha instalado FreeTDS, use el comando siguiente para conectarse al servidor de base de datos SQL que creó anteriormente:

        TDSVER=8.0 tsql -H <serverName>.database.windows.net -U <sqlLogin> -P <sqlPassword> -p 1433 -D oozietest

    Recibirá un resultado similar al siguiente:

        locale is "en_US.UTF-8"
        locale charset is "UTF-8"
        using default charset "UTF-8"
        Default database being set to oozietest
        1>

5. En la `1>` pregunta, escriba las siguientes líneas:

        CREATE TABLE [dbo].[mobiledata](
        [deviceplatform] [nvarchar](50),
        [count] [bigint])
        GO
        CREATE CLUSTERED INDEX mobiledata_clustered_index on mobiledata(deviceplatform)
        GO

    Cuando el `GO` instrucción se haya especificado, se evaluará las afirmaciones anteriores. Esto creará una nueva tabla denominada **mobiledata** que se escribe Sqoop.

    Para comprobar que se ha creado la tabla, utilice la siguiente:

        SELECT * FROM information_schema.tables
        GO

    Debería ver el resultado similar al siguiente:

        TABLE_CATALOG   TABLE_SCHEMA    TABLE_NAME      TABLE_TYPE
        oozietest       dbo     mobiledata      BASE TABLE

8. Escriba `exit` en la `1>` del mensaje salir de la utilidad de tsql.

##<a name="create-the-job-definition"></a>Crear la definición de trabajo

La definición de trabajo describe dónde encontrar el que, así como otros archivos usados por el flujo de trabajo (por ejemplo, useooziewf.hql.) También se define los valores de propiedades del flujo de trabajo y los archivos asociados.

1. Use el comando siguiente para obtener la dirección WASB completa para almacenamiento predeterminado. Se usará en el archivo de configuración en un momento:

        sed -n '/<name>fs.default/,/<\/value>/p' /etc/hadoop/conf/core-site.xml

    Debe devolver información similar al siguiente:

        <name>fs.defaultFS</name>
        <value>wasbs://mycontainer@mystorageaccount.blob.core.windows.net</value>

    Guardar la **wasbs://mycontainer@mystorageaccount.blob.core.windows.net** valor, que se utilizará en los pasos siguientes.

2. Use el comando siguiente para obtener el FQDN de headnode de clúster. Se usará para la dirección de JobTracker para el clúster. Se usará en el archivo de configuración en un momento:

        hostname -f

    Esto devolverá información similar al siguiente:

        hn0-CLUSTERNAME.randomcharacters.cx.internal.cloudapp.net

    El puerto utilizado para el JobTracker es 8050, por lo que la dirección completa para el JobTracker será **hn0 CLUSTERNAME.randomcharacters.cx.internal.cloudapp.net:8050**.

1. Utilice la siguiente para crear la configuración de definición de trabajo de Oozie:

        nano job.xml

2. Una vez que se abre el editor de nano, utilice la siguiente como el contenido del archivo:

        <?xml version="1.0" encoding="UTF-8"?>
        <configuration>

          <property>
            <name>nameNode</name>
            <value>wasbs://mycontainer@mystorageaccount.blob.core.windows.net</value>
          </property>

          <property>
            <name>jobTracker</name>
            <value>JOBTRACKERADDRESS</value>
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
            <name>hiveScript</name>
            <value>wasbs://mycontainer@mystorageaccount.blob.core.windows.net/tutorials/useoozie/useooziewf.hql</value>
          </property>

          <property>
            <name>hiveTableName</name>
            <value>mobilecount</value>
          </property>

          <property>
            <name>hiveDataFolder</name>
            <value>wasbs://mycontainer@mystorageaccount.blob.core.windows.net/tutorials/useoozie/data</value>
          </property>

          <property>
            <name>sqlDatabaseConnectionString</name>
            <value>"jdbc:sqlserver://serverName.database.windows.net;user=adminLogin;password=adminPassword;database=oozietest"</value>
          </property>

          <property>
            <name>sqlDatabaseTableName</name>
            <value>mobiledata</value>
          </property>

          <property>
            <name>user.name</name>
            <value>YourName</value>
          </property>

          <property>
            <name>oozie.wf.application.path</name>
            <value>wasbs://mycontainer@mystorageaccount.blob.core.windows.net/tutorials/useoozie</value>
          </property>
        </configuration>

    * Reemplazar todas las instancias de **wasbs://mycontainer@mystorageaccount.blob.core.windows.net** con el valor que recibió.

    > [AZURE.WARNING] Debe utilizar la ruta completa de WASB, con la cuenta de almacenamiento y contenedor como parte de la ruta de acceso. Con el formato corto (wasbs: / / /) hará que la acción RunHiveScript no se realice correctamente cuando se inicia el trabajo.

    * Reemplace **JOBTRACKERADDRESS** con la dirección de JobTracker/ResourceManager que recibió.

    * Reemplace **suNombre** con su nombre de inicio de sesión para el clúster de HDInsight.

    * Reemplazar **nombre_de_servidor**, **adminLogin**y **adminPassword** con la información de la base de datos de SQL Azure.

    La mayoría de la información en este archivo se utiliza para rellenar los valores que se usan en los archivos workflow.xml o ooziewf.hql (como ${nameNode}.)

    > [AZURE.NOTE] La entrada de **oozie.wf.application.path** define dónde se encuentra el archivo workflow.xml, que contiene el flujo de trabajo se ha ejecutado este trabajo.

2. Utilice Ctrl-X, después **s** y **ENTRAR** para guardar el archivo.

##<a name="submit-and-manage-the-job"></a>Enviar y administrar el trabajo

Los pasos siguientes use el comando Oozie enviar y administrar los flujos de trabajo de Oozie en el clúster. El comando Oozie es una interfaz descriptiva sobre la [API de REST de Oozie](https://oozie.apache.org/docs/4.1.0/WebServicesAPI.html).

> [AZURE.IMPORTANT] Cuando se usa el comando Oozie, debe utilizar el FQDN para el headnode HDInsight. Este FQDN sólo es accesible desde el clúster, o si el clúster está en una red Virtual de Azure, de otros equipos en la misma red.

1. Utilice la siguiente para obtener la dirección URL del servicio de Oozie:

        sed -n '/<name>oozie.base.url/,/<\/value>/p' /etc/oozie/conf/oozie-site.xml

    Devolverá un valor similar al siguiente:

        <name>oozie.base.url</name>
        <value>http://hn0-CLUSTERNAME.randomcharacters.cx.internal.cloudapp.net:11000/oozie</value>

    La parte de **http://hn0-CLUSTERNAME.randomcharacters.cx.internal.cloudapp.net:11000/oozie** es la dirección URL para usar con el comando Oozie.

2. Utilice la siguiente para crear una variable de entorno para la dirección URL, por lo que no es necesario que escriba cada comando:

        export OOZIE_URL=http://HOSTNAMEt:11000/oozie

    Reemplazar la dirección URL con la que recibió.

3. Use las siguientes acciones para enviar el trabajo:

        oozie job -config job.xml -submit

    Esto carga la información de trabajo de **job.xml** y lo envía a Oozie, pero no ejecuta.

    Una vez completado el comando, debe devolver el identificador de la tarea. Por ejemplo, `0000005-150622124850154-oozie-oozi-W`. Se utilizará para administrar el trabajo.

4. Ver el estado del trabajo con el siguiente comando. Escriba el identificador de trabajo devuelto por el comando anterior:

        oozie job -info <JOBID>

    Devolverá información similar al siguiente.

        Job ID : 0000005-150622124850154-oozie-oozi-W
        ------------------------------------------------------------------------------------------------------------------------------------
        Workflow Name : useooziewf
        App Path      : wasbs:///tutorials/useoozie
        Status        : PREP
        Run           : 0
        User          : USERNAME
        Group         : -
        Created       : 2015-06-22 15:06 GMT
        Started       : -
        Last Modified : 2015-06-22 15:06 GMT
        Ended         : -
        CoordAction ID: -
        ------------------------------------------------------------------------------------------------------------------------------------

    Este trabajo tiene un estado de `PREP`, lo que indica que se ha enviado, pero aún no han iniciado.

4. Utilice la siguiente para iniciar el trabajo:

        oozie job -start JOBID

    Si comprobar el estado después de este comando, se mostrará en un estado de ejecución y se devolverá información acerca de las acciones dentro del trabajo.

5. Una vez que la tarea finaliza correctamente, puede comprobar que los datos se ha generado y exportar a la tabla de base de datos SQL mediante los siguientes comandos:

        TDSVER=8.0 tsql -H <serverName>.database.windows.net -U <adminLogin> -P <adminPassword> -p 1433 -D oozietest

    En la `1>` pregunta, escriba lo siguiente:

        SELECT * FROM mobiledata
        GO

    Recibirá información similar a la siguiente:

        deviceplatform  count
        Android 31591
        iPhone OS       22731
        proprietary development 3
        RIM OS  3464
        Unknown 213
        Windows Phone   1791
        (6 rows affected)

Para obtener más información sobre el comando Oozie, consulte la [Herramienta de línea de comandos de Oozie](https://oozie.apache.org/docs/4.1.0/DG_CommandLineTool.html).

##<a name="oozie-rest-api"></a>API de REST de Oozie

La API de REST Oozie le permiten crear sus propias herramientas que funcionan con Oozie. Los siguientes son HDInsight información específica sobre el uso de la API de REST de Oozie:

* **URI**: puede tener acceso a la API de REST de fuera del clúster en`https://CLUSTERNAME.azurehdinsight.net/oozie`

* **Autenticación**: deben autenticarse a la API con la cuenta de clúster HTTP (admin) y la contraseña. Por ejemplo:

        curl -u admin:PASSWORD https://CLUSTERNAME.azurehdinsight.net/oozie/versions

Para obtener más información sobre el uso de la API de REST Oozie, vea [API de servicios Web Oozie](https://oozie.apache.org/docs/4.1.0/WebServicesAPI.html).

##<a name="oozie-web-ui"></a>Interfaz de usuario de Web Oozie

La interfaz de usuario de Web Oozie proporciona una vista basada en web en el estado de los trabajos de Oozie en el clúster. Permite ver el estado del trabajo, la definición de trabajo, configuración, un gráfico de las acciones en el trabajo y los registros para el trabajo. También puede ver detalles de acciones en una tarea.

Para obtener acceso a la interfaz de usuario de Web Oozie, realice los siguientes pasos:

1. Crear un túnel SSH al clúster HDInsight. Para obtener información acerca de cómo hacerlo, consulte [Usar SSH túnel para tener acceso a la interfaz de usuario de web Ambari, ResourceManager, JobHistory, NameNode, Oozie y otra de la interfaz de usuario de web](hdinsight-linux-ambari-ssh-tunnel.md).

2. Una vez que se ha creado un túnel, abra la interfaz de usuario de Ambari web en el explorador web. El URI para el sitio de Ambari es **https://CLUSTERNAME.azurehdinsight.net**. Reemplazar **NOMBREDECLÚSTER** con el nombre de su clúster HDInsight basados en Linux.

3. En el lado izquierdo de la página, seleccione **Oozie**, a continuación, **Vínculos rápidos**y, finalmente, **Interfaz de usuario de Oozie Web**.

    ![imagen de los menús](./media/hdinsight-use-oozie-linux-mac/ooziewebuisteps.png)

4. La interfaz de usuario de Web Oozie, de forma predeterminada, que muestra los trabajos de flujo de trabajo en ejecución. Para ver todas las tareas de flujo de trabajo, seleccione **Todas las tareas**.

    ![Todos los trabajos de muestra](./media/hdinsight-use-oozie-linux-mac/ooziejobs.png)

5. Seleccione una tarea para ver más información sobre el trabajo.

    ![Información de trabajo](./media/hdinsight-use-oozie-linux-mac/jobinfo.png)

6. En la pestaña información de trabajo, puede ver información sobre el trabajo básica, así como las acciones individuales dentro del trabajo. Con las pestañas en la parte superior puede ver el acceso de definición de trabajo, configuración del trabajo, el registro del trabajo o ver un dirigen acíclicos Graph (DAG incluidos) del trabajo.

    * **Registro de trabajo**: seleccione el botón de **GetLogs** obtener todos los registros de la tarea, o usar el campo **Escriba filtro de búsqueda** para filtrar registros

        ![Registro de trabajo](./media/hdinsight-use-oozie-linux-mac/joblog.png)

    * **JobDAG**: DAG incluidos el es una visión general gráfica de las rutas de acceso de datos tomadas por el flujo de trabajo

        ![Trabajo DAG incluidos](./media/hdinsight-use-oozie-linux-mac/jobdag.png)

7. Seleccionar una de las acciones de la pestaña **Información de trabajo** aparecerá la información de la acción. Por ejemplo, seleccione la acción **RunHiveScript** .

    ![Información de acción](./media/hdinsight-use-oozie-linux-mac/action.png)

8. Puede ver los detalles de la acción, que incluye un vínculo a la **Dirección URL de la consola**, que puede utilizarse para ver información de JobTracker para la tarea.

##<a name="scheduling-jobs"></a>Programación de trabajos

El coordinador le permite especificar un inicio, fin y frecuencia de repetición para trabajos de modo que se pueden programar para algunas veces.

Para definir una programación para el flujo de trabajo, realice los siguientes pasos:

1. Utilice la siguiente para crear un nuevo archivo denominado **coordinator.xml**:

        nano coordinator.xml

    Utilice la siguiente como el contenido del archivo:

        <coordinator-app name="my_coord_app" frequency="${coordFrequency}" start="${coordStart}" end="${coordEnd}" timezone="${coordTimezone}" xmlns="uri:oozie:coordinator:0.4">
          <action>
            <workflow>
              <app-path>${workflowPath}</app-path>
            </workflow>
          </action>
        </coordinator-app>

    Tenga en cuenta que utiliza `${...}` las variables que se reemplazará por los valores de la definición de trabajo. Las variables son:

    * **${coordFrequency}**: tiempo entre instancias del trabajo en ejecución
    * **${coordStart}**: hora de inicio de la tarea
    * **${coordEnd}**: la hora de finalización de tarea
    * **${coordTimezone}**: trabajos de coordinador están en una zona horaria fija sin horario de verano (normalmente representado usando UTC). Esta zona horaria se denomina "Oozie zona de horaria de procesamiento"
    * **${wfPath}**: la ruta de acceso a la workflow.xml

2. Utilice Ctrl-X, después **s** y **ENTRAR** para guardar el archivo.

3. Utilice la siguiente para copiarlo en el directorio de trabajo para este trabajo:

        hadoop fs -copyFromLocal coordinator.xml /tutorials/useoozie/coordinator.xml

4. Utilice la siguiente para modificar el archivo **job.xml** :

        nano job.xml

    Realice los cambios siguientes:

    * Cambiar `<name>oozie.wf.application.path</name>` a `<name>oozie.coord.application.path</name>`. Esto indica a Oozie para ejecutar el archivo de coordinador en lugar del archivo de flujo de trabajo

    * Agregue lo siguiente, que se establece una variable usada en la coordinator.xml para que apunten a la ubicación de la workflow.xml:

            <property>
              <name>workflowPath</name>
              <value>wasbs://mycontainer@mystorageaccount.blob.core.windows.net/tutorials/useoozie</value>
            </property>

        Reemplazar los valores de **mycontainer** y **mystorageaccount** con los valores de otras entradas en el archivo job.xml.

    * Agregue lo siguiente, que definen el inicio, fin y frecuencia para el archivo coordinator.xml:

            <property>
              <name>coordStart</name>
              <value>2015-06-25T12:00Z</value>
            </property>

            <property>
              <name>coordEnd</name>
              <value>2015-06-27T12:00Z</value>
            </property>

            <property>
              <name>coordFrequency</name>
              <value>1440</value>
            </property>

            <property>
              <name>coordTimezone</name>
              <value>UTC</value>
            </property>

        Estos establecer la hora de inicio a 12:00 P.M. 25 de junio de 2015, la hora de finalización del 27 de junio de 2015 y el intervalo para ejecutar este trabajo a diario (la frecuencia es en minutos, hasta 24 horas a 60 minutos = 1440 minutos.) Por último, la zona horaria se establece en UTC.

5. Utilice Ctrl-X, después **s** y **ENTRAR** para guardar el archivo.

6. Para ejecutar el trabajo, use el siguiente comando:

        oozie job -config job.xml -run

    Esto enviar e iniciar el trabajo.

7. Si visite la interfaz de usuario de Web Oozie y seleccione la ficha **Trabajos coordinador** , debe información similar a la siguiente:

    ![ficha de trabajos de coordinador](./media/hdinsight-use-oozie-linux-mac/coordinatorjob.png)

    Tenga en cuenta la entrada **Materialización siguiente** ; Esto es cuando el trabajo se ejecutará siguiente.

8. Similar a la tarea de flujo de trabajo anterior, seleccione la entrada de trabajo en la interfaz de usuario de web mostrará información en el trabajo:

    ![Información de trabajo del Coordinador](./media/hdinsight-use-oozie-linux-mac/coordinatorjobinfo.png)

    Tenga en cuenta que esto solo muestra ejecuciones correcta de la tarea, acciones no individuales dentro del flujo de trabajo programado. Para ver, seleccione una de las entradas de **acción** . Esta opción mostrará información similar a la se recuperen para la tarea de flujo de trabajo anteriores.

    ![Información de acción](./media/hdinsight-use-oozie-linux-mac/coordinatoractionjob.png)

##<a name="troubleshooting"></a>Solución de problemas

Para solucionar problemas con los trabajos de Oozie, la UI Oozie es muy útil, ya que permite ver fácilmente ambos registros Oozie, así como vínculos a registros de JobTracker de tareas MapReduce como las consultas de la sección. En general, debe ser el patrón de solución de problemas:

1. Ver el trabajo en la interfaz de usuario de Oozie Web.

2. Si hay un error o un error para una acción específica, seleccione la acción para ver si el campo **Mensaje de Error** proporciona más información sobre el error.

3. Si está disponible, use la dirección URL de la acción para ver más detalles (como registros de JobTracker), de la acción.

Los siguientes son errores específicos que pueden surgir y cómo resolverlos.

###<a name="ja009-cannot-initialize-cluster"></a>JA009: No puede iniciar el clúster

**Síntomas**: el estado del trabajo cambia a **suspendido**. Detalles de la tarea muestran el estado de RunHiveScript como **START_MANUAL**. Seleccionar la acción mostrará el siguiente mensaje de error:

    JA009: Cannot initialize Cluster. Please check your configuration for map

**Causa**: las direcciones de la WASB utilizadas en el archivo **job.xml** no contienen el contenedor de almacenamiento o el nombre de la cuenta de almacenamiento. El formato de dirección WASB debe ser `wasbs://containername@storageaccountname.blob.core.windows.net`.

**Resolución**: cambie las direcciones WASB usadas el trabajo.

###<a name="ja002-oozie-is-not-allowed-to-impersonate-ltuser"></a>JA002: Oozie no se puede representar &lt;usuario >

**Síntomas**: el estado del trabajo cambia a **suspendido**. Detalles de la tarea muestran el estado de RunHiveScript como **START_MANUAL**. Seleccionar la acción mostrará el siguiente mensaje de error:

    JA002: User: oozie is not allowed to impersonate <USER>

**Causa**: la configuración de permisos actual no permitir Oozie representar la cuenta de usuario especificado.

**Resolución**: Oozie se permite representar a los usuarios del grupo de **usuarios** . Usar el `groups USERNAME` para ver los grupos de la cuenta de usuario es miembro. Si el usuario no es un miembro del grupo de **usuarios** , use el comando siguiente para agregar el usuario al grupo:

    sudo adduser USERNAME users

> [AZURE.NOTE] Puede tardar varios minutos antes de HDInsight reconoce que el usuario se ha agregado al grupo.

###<a name="launcher-error-sqoop"></a>Selector de ERROR (Sqoop)

**Síntomas**: el estado del trabajo se le ha cambiado a **KILLED**. Detalles de la tarea muestran el estado de RunSqoopExport como **ERROR**. Seleccionar la acción mostrará el siguiente mensaje de error:

    Launcher ERROR, reason: Main class [org.apache.oozie.action.hadoop.SqoopMain], exit code [1]

**Causa**: Sqoop es no se puede cargar el controlador de base de datos necesario para tener acceso a la base de datos.

**Resolución**: cuando utiliza Sqoop desde un trabajo Oozie, debe incluir el controlador de base de datos con los otros recursos (como workflow.xml), utilizado por el trabajo.

También debe hacer referencia al archivo que contiene el controlador de base de datos de la `<sqoop>...</sqoop>` sección de la workflow.xml.

Por ejemplo, para el trabajo en este documento, usar los siguientes pasos:

1. Copie el archivo sqljdbc4.1.jar en el directorio /tutorials/useoozie:

         hadoop fs -copyFromLocal /usr/share/java/sqljdbc_4.1/enu/sqljdbc41.jar /tutorials/useoozie/sqljdbc41.jar

2. Modificar la workflow.xml para agregar los siguientes en una nueva línea arriba `</sqoop>`:

        <archive>sqljdbc41.jar</archive>

##<a name="next-steps"></a>Pasos siguientes
En este tutorial, ha aprendido cómo definir un flujo de trabajo Oozie y cómo ejecutar un trabajo Oozie. Para obtener más información sobre cómo trabajar con HDInsight, consulte los artículos siguientes:

- [Usar tiempo basadas en el coordinador Oozie con HDInsight][hdinsight-oozie-coordinator-time]
- [Cargar datos para trabajos de Hadoop en HDInsight][hdinsight-upload-data]
- [Usar Sqoop con Hadoop en HDInsight][hdinsight-use-sqoop]
- [Usar subárbol con Hadoop en HDInsight][hdinsight-use-hive]
- [Usar cerdo con Hadoop en HDInsight][hdinsight-use-pig]
- [Desarrollar programas Java MapReduce para HDInsight][hdinsight-develop-mapreduce]


[hdinsight-cmdlets-download]: http://go.microsoft.com/fwlink/?LinkID=325563



[azure-data-factory-pig-hive]: ../data-factory/data-factory-data-transformation-activities.md
[hdinsight-oozie-coordinator-time]: hdinsight-use-oozie-coordinator-time.md
[hdinsight-versions]:  hdinsight-component-versioning.md
[hdinsight-storage]: hdinsight-use-blob-storage.md
[hdinsight-get-started]: hdinsight-get-started.md


[hdinsight-use-sqoop]: hdinsight-use-sqoop-mac-linux.md
[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-use-mapreduce]: hdinsight-use-mapreduce.md
[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-pig]: hdinsight-use-pig.md
[hdinsight-storage]: hdinsight-use-blob-storage.md
[hdinsight-get-started-emulator]: hdinsight-get-started-emulator.md

[hdinsight-develop-mapreduce]: hdinsight-develop-deploy-java-mapreduce-linux.md

[sqldatabase-create-configue]: sql-database-create-configure.md
[sqldatabase-get-started]: sql-database-get-started.md

[azure-create-storageaccount]: storage-create-storage-account.md

[apache-hadoop]: http://hadoop.apache.org/
[apache-oozie-400]: http://oozie.apache.org/docs/4.0.0/
[apache-oozie-332]: http://oozie.apache.org/docs/3.3.2/

[powershell-download]: http://azure.microsoft.com/downloads/
[powershell-about-profiles]: http://go.microsoft.com/fwlink/?LinkID=113729
[powershell-install-configure]: powershell-install-configure.md
[powershell-start]: http://technet.microsoft.com/library/hh847889.aspx
[powershell-script]: https://technet.microsoft.com/en-us/library/ee176961.aspx

[cindygross-hive-tables]: http://blogs.msdn.com/b/cindygross/archive/2013/02/06/hdinsight-hive-internal-and-external-tables-intro.aspx

[img-workflow-diagram]: ./media/hdinsight-use-oozie/HDI.UseOozie.Workflow.Diagram.png
[img-preparation-output]: ./media/hdinsight-use-oozie/HDI.UseOozie.Preparation.Output1.png
[img-runworkflow-output]: ./media/hdinsight-use-oozie/HDI.UseOozie.RunWF.Output.png

[technetwiki-hive-error]: http://social.technet.microsoft.com/wiki/contents/articles/23047.hdinsight-hive-error-unable-to-rename.aspx
