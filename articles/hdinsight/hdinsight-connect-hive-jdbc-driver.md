<properties
 pageTitle="Utilizar JDBC a consulta subárbol en HDInsight de Azure"
 description="Obtenga información sobre cómo utilizar JDBC para conectarse a la sección de HDInsight de Azure y ejecutar consultas en los datos almacenados en la nube de forma remota."
 services="hdinsight"
 documentationCenter=""
 authors="Blackmist"
 manager="jhubbard"
 editor="cgronlun"
    tags="azure-portal"/>

<tags
 ms.service="hdinsight"
 ms.devlang="java"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="big-data"
 ms.date="08/23/2016"
 ms.author="larryfr"/>

#<a name="connect-to-hive-on-azure-hdinsight-using-the-hive-jdbc-driver"></a>Conectarse al subárbol de Azure HDInsight con el controlador de JDBC subárbol

[AZURE.INCLUDE [ODBC-JDBC-selector](../../includes/hdinsight-selector-odbc-jdbc.md)]

En este documento, aprenderá cómo usar JDBC desde una aplicación de Java remotamente enviar consultas de la sección a un clúster de HDInsight. Aprenderá cómo conectarse desde el cliente ardilla SQL y cómo conectarse mediante programación de Java.

Para obtener más información sobre la interfaz de JDBC subárbol, consulte [HiveJDBCInterface](https://cwiki.apache.org/confluence/display/Hive/HiveJDBCInterface).

##<a name="prerequisites"></a>Requisitos previos

Para completar los pasos de este artículo, necesita lo siguiente:

* Un Hadoop en clúster HDInsight. Funcionarán clústeres basados en Linux o en Windows.

* [Ardilla SQL](http://squirrel-sql.sourceforge.net/). Ardilla es una aplicación de cliente JDBC.

Para crear y ejecutar la aplicación de Java ejemplo vinculada de este artículo, necesita lo siguiente.

* El [Kit de desarrollador de Java (JDK) versión 7](https://www.oracle.com/technetwork/java/javase/downloads/jdk7-downloads-1880260.html) o superior.

* [Experto en Apache](https://maven.apache.org). Maven es el sistema de proyectos de Java que utiliza el proyecto asociado a este artículo de compilación de un proyecto.

##<a name="connection-string"></a>Cadena de conexión

Conexiones de JDBC a un clúster de HDInsight de Azure se realizan más 443 y, a continuación, el tráfico está protegido con SSL. La puerta de enlace público que los clústeres sentarse detrás redirige el tráfico al puerto que realmente está escuchando HiveServer2. Para que una cadena de conexión típica gustaría lo siguiente:

    jdbc:hive2://CLUSTERNAME.azurehdinsight.net:443/default;ssl=true?hive.server2.transport.mode=http;hive.server2.thrift.http.path=/hive2

Reemplazar __NOMBREDECLÚSTER__ con el nombre de su clúster HDInsight.

##<a name="authentication"></a>Autenticación

Para establecer la conexión, debe utilizar el nombre del Administrador de clúster de HDInsight y la contraseña para autenticar a la puerta de enlace de clúster. Cuando se conecta desde los clientes JDBC como ardilla SQL, debe escribir el nombre de administrador y la contraseña en la configuración de cliente.

Desde una aplicación de Java, debe utilizar el nombre y contraseña para establecer una conexión. Por ejemplo, el siguiente código de Java abre una nueva conexión con la cadena de conexión, el nombre de administrador y la contraseña:

    DriverManager.getConnection(connectionString,clusterAdmin,clusterPassword);

##<a name="connect-with-squirrel-sql-client"></a>Conectar con ardilla SQL client

Ardilla SQL es un cliente JDBC que se pueden usar para ejecutar consultas de la sección de forma remota con el clúster de HDInsight. Los siguientes pasos se suponen que ya ha instalado ardilla SQL y le guiará a través de descarga y configuración de los controladores de sección.

1. Copie los controladores JDBC subárbol de clúster HDInsight.

    * Para __HDInsight basados en Linux__, siga estos pasos para descargar los archivos jar necesarios.

        1. Crear un nuevo directorio donde desea que aparezcan los archivos. Por ejemplo, `mkdir hivedriver`.

        2. Desde un símbolo del sistema, fiesta, PowerShell u otro símbolo, cambie los directorios al nuevo directorio y use los comandos siguientes para copiar los archivos de clúster HDInsight.

                scp USERNAME@CLUSTERNAME:/usr/hdp/current/hive-client/lib/hive-jdbc*standalone.jar .
                scp USERNAME@CLUSTERNAME:/usr/hdp/current/hadoop-client/hadoop-common.jar .
                scp USERNAME@CLUSTERNAME:/usr/hdp/current/hadoop-client/hadoop-auth.jar .

            Reemplace el __nombre de usuario__ con el nombre de cuenta de usuario SSH para el clúster. Reemplazar __NOMBREDECLÚSTER__ con el nombre de clúster HDInsight.

            > [AZURE.NOTE] En entornos de Windows, debe usar la utilidad PSCP en lugar de scp. Puede descargarla desde [http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).

    * Para __HDInsight de Windows__, realice los pasos siguientes para descargar los archivos jar.

        1. Desde el portal de Azure, seleccione el clúster HDInsight y, a continuación, seleccione el icono de __Escritorio remoto__ .

            ![Icono de escritorio remoto](./media/hdinsight-connect-hive-jdbc-driver/remotedesktopicon.png)

        2. En el módulo de escritorio remoto, use el botón __Conectar__ para conectarse al clúster. Si no está habilitado el escritorio remoto, use el formulario para proporcionar un nombre de usuario y contraseña, a continuación, seleccione __Activar__ para habilitar Escritorio remoto para el clúster.

            ![Módulo de escritorio remoto](./media/hdinsight-connect-hive-jdbc-driver/remotedesktopblade.png)

            Después de seleccionar __Conectar__, se descargará un archivo RDP. Utilice este archivo para iniciar al cliente de escritorio remoto. Cuando se le solicite, utilice el nombre de usuario y contraseña que ha escrito para el acceso de escritorio remoto.

        3. Una vez conectado, copie los siguientes archivos de la sesión de escritorio remoto en el equipo local. Colocarlos en un directorio local denominado `hivedriver`.

            * C:\apps\dist\hive-0.14.0.2.2.9.1-7\lib\hive-JDBC-0.14.0.2.2.9.1-7-Standalone.jar
            * C:\apps\dist\hadoop-2.6.0.2.2.9.1-7\share\hadoop\common\hadoop-Common-2.6.0.2.2.9.1-7.jar
            * C:\apps\dist\hadoop-2.6.0.2.2.9.1-7\share\hadoop\common\lib\hadoop-AUTH-2.6.0.2.2.9.1-7.jar

            > [AZURE.NOTE] Los números de versión incluidos en las rutas de acceso y los nombres de archivo pueden ser diferentes para el clúster.

        4. Desconectar la sesión de escritorio remoto cuando haya terminado de copiar los archivos.

3. Inicie la aplicación ardilla SQL. En la parte izquierda de la ventana, seleccione __controladores__.

    ![Ficha controladores en la parte izquierda de la ventana](./media/hdinsight-connect-hive-jdbc-driver/squirreldrivers.png)

4. En los iconos en la parte superior del cuadro de diálogo __controladores__ , seleccione la __+__ icono para crear un nuevo controlador.

    ![Iconos de controladores](./media/hdinsight-connect-hive-jdbc-driver/driversicons.png)

5. En el cuadro de diálogo Agregar controlador, agregue la siguiente información.

    * __Nombre__: subárbol
    * __Dirección URL de ejemplo__: jdbc:hive2://localhost:443/default;ssl=true?hive.server2.transport.mode=http;hive.server2.thrift.http.path=/hive2
    * __Ruta de acceso de clase adicional__: Use el botón Agregar para agregar los archivos jar descargado anteriormente
    * __Nombre de la clase__: org.apache.hive.jdbc.HiveDriver

    ![cuadro de diálogo del controlador agregar](./media/hdinsight-connect-hive-jdbc-driver/adddriver.png)

    Haga clic en __Aceptar__ para guardar la configuración.

6. A la izquierda de la ventana ardilla SQL, seleccione __alias__. A continuación, haga clic en el __+__ icono para crear un nuevo alias de conexión.

    ![Agregar nuevo alias](./media/hdinsight-connect-hive-jdbc-driver/aliases.png)

7. Use los siguientes valores para el cuadro de diálogo __Agregar Alias__ .

    * __Nombre__: subárbol en HDInsight
    * __Controlador__: Use el menú desplegable para seleccionar el controlador de __sección__
    * __Dirección URL__: jdbc:hive2://CLUSTERNAME.azurehdinsight.net:443/default;ssl=true?hive.server2.transport.mode=http;hive.server2.thrift.http.path=/hive2

        Reemplazar __NOMBREDECLÚSTER__ con el nombre de su clúster HDInsight.

    * __Nombre de usuario__: el nombre de cuenta de inicio de sesión de clúster para el clúster de HDInsight. El valor predeterminado es `admin`.
    * __Contraseña__: la contraseña de la cuenta de inicio de sesión de clúster. Se trata de una contraseña proporcionada al crear el clúster de HDInsight.

    ![agregar el cuadro de diálogo de alias](./media/hdinsight-connect-hive-jdbc-driver/addalias.png)

    Use el botón de __prueba__ para comprobar que la conexión funciona. Cuando __conectarse: subárbol en HDInsight__ aparece el cuadro de diálogo, seleccione __Conectar__ a realizar la prueba. Si la prueba se realiza correctamente, verá un cuadro de diálogo __conexión correcta__ .

    Use el botón __Aceptar__ en la parte inferior del cuadro de diálogo __Agregar Alias__ para guardar el alias de conexión.

8. En la lista desplegable de __conectarse a__ la parte superior de ardilla SQL, seleccione la __sección en HDInsight__. Cuando se le solicite, seleccione __Conectar__.

    ![cuadro de diálogo conexión](./media/hdinsight-connect-hive-jdbc-driver/connect.png)

9. Una vez conectado, escriba la siguiente consulta en el cuadro de diálogo de la consulta SQL y, a continuación, seleccione el icono __Ejecutar__ . El área de resultados debe mostrar los resultados de la consulta.

        select * from hivesampletable limit 10;

    ![cuadro de diálogo de consulta de SQL, incluidos los resultados](./media/hdinsight-connect-hive-jdbc-driver/sqlquery.png)

##<a name="connect-from-an-example-java-application"></a>Conectar desde un aplicación de Java de ejemplo

Un ejemplo de uso de un cliente de Java a consulta subárbol en HDInsight está disponible en [https://github.com/Azure-Samples/hdinsight-java-hive-jdbc](https://github.com/Azure-Samples/hdinsight-java-hive-jdbc). Siga las instrucciones en el repositorio para generar y ejecutar el ejemplo.

##<a name="troubleshooting"></a>Solución de problemas

### <a name="unexpected-error-occurred-attempting-to-open-an-sql-connection"></a>Error inesperado al intentar abrir una conexión de SQL.

__Síntomas__: al conectarse a un clúster de HDInsight versión 3.3 o 3.4, puede recibir un error que se ha producido un error inesperado. El seguimiento de la pila de este error, comenzará con las siguientes líneas:

    java.util.concurrent.ExecutionException: java.lang.RuntimeException: java.lang.NoSuchMethodError: org.apache.commons.codec.binary.Base64.<init>(I)V
    at java.util.concurrent.FutureTas...(FutureTask.java:122)
    at java.util.concurrent.FutureTask.get(FutureTask.java:206)

__Causa__: este error se produce por un error de coincidencia en la versión del archivo codec.jar commons usado por ardilla y a la requerida por los componentes de subárbol JDBC descargados desde el clúster HDInsight.

__Resolución__: para corregir este error, realice los siguientes pasos.

1. Descargar el archivo de jar de códec commons desde el clúster HDInsight.

        scp USERNAME@CLUSTERNAME:/usr/hdp/current/hive-client/lib/commons-codec*.jar ./commons-codec.jar

2. Salir ardilla y, a continuación, vaya al directorio donde está instalado ardilla en el sistema. En el directorio ardilla, en la `lib` directorio, reemplazar la codec.jar de commons existente con el se descarga desde el clúster HDInsight.

3. Reinicie ardilla. Ya no se realizará el error al conectarse a la sección de HDInsight.

##<a name="next-steps"></a>Pasos siguientes

Ahora que ha aprendido a usar JDBC para trabajar con la sección, use los siguientes vínculos para explorar otras formas de trabajar con HDInsight de Azure.

* [Cargar datos a HDInsight](hdinsight-upload-data.md)
* [Usar subárbol con HDInsight](hdinsight-use-hive.md)
* [Usar cerdo con HDInsight](hdinsight-use-pig.md)
* [Usar MapReduce trabajos con HDInsight](hdinsight-use-mapreduce.md)
