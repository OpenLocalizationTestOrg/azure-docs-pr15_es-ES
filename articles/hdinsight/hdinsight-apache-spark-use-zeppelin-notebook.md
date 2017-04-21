<properties 
    pageTitle="Instalar los blocs de notas de globo dirigible para clúster de motor de Apache en HDInsight Linux | Microsoft Azure" 
    description="Instrucciones paso a paso sobre cómo instalar y usar blocs de notas de globo dirigible con clústeres motor en HDInsight Linux." 
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
    ms.date="10/28/2016" 
    ms.author="nitinme"/>


# <a name="install-zeppelin-notebooks-for-apache-spark-cluster-on-hdinsight-linux"></a>Instalar los blocs de notas de globo dirigible para clúster de motor de Apache en HDInsight Linux

Obtenga información sobre cómo instalar los blocs de notas de globo dirigible en Apache motor clústeres y cómo usar los blocs de notas de globo dirigible ejecutar motor trabajos.

> [AZURE.IMPORTANT] Blocs de notas de globo dirigible ahora están disponibles con clústeres de motor de forma predeterminada. No es necesario instalarlos explícitamente en un clúster de motor ya. Para obtener más información, vea [usar Zeppelin blocs de notas con Apache motor clúster en HDInsight Linux](hdinsight-apache-spark-zeppelin-notebook.md). 

**Requisitos previos:**

* Antes de comenzar este tutorial, debe tener una suscripción de Azure. Vea la [versión de prueba gratuita de Azure obtener](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* Un clúster de motor de Apache. Para obtener instrucciones, vea [crear Apache motor clústeres en HDInsight de Azure](hdinsight-apache-spark-jupyter-spark-sql.md).
* Un cliente SSH. Para las distribuciones de Linux y Unix o Macintosh OS X, el `ssh` comando se suministra con el sistema operativo. Para Windows, se recomienda [PuTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html)

    > [AZURE.NOTE] Si desea utilizar un cliente SSH distinto de `ssh` o masilla, consulte la documentación de su cliente acerca de cómo establecer un túnel SSH.

* Un explorador web que se puede configurar para utilizar a un proxy SOCKS

* __(opcional)__: un complemento como [FoxyProxy](http://getfoxyproxy.org/,) que puede aplicar las reglas que solo enrutar solicitudes específicas a través del túnel.

    > [AZURE.WARNING] Sin un complemento como FoxyProxy, se pueden dirigir todas las solicitudes realizadas a través del explorador a través del túnel. Esto puede provocar más lento carga de páginas web en el explorador.

## <a name="install-zeppelin-on-a-spark-cluster"></a>Instalar Zeppelin en un clúster de motor

Puede instalar Zeppelin en un clúster de motor con la acción de secuencia de comandos. Acción de secuencia de comandos usa scripts personalizados para instalar componentes en el clúster que no están disponibles de forma predeterminada. Puede usar el script personalizado para instalar Zeppelin desde el Portal de Azure, usando HDInsight .NET SDK o con PowerShell de Azure. Puede utilizar la secuencia de comandos para instalar Zeppelin forme parte de la creación de un clúster o después de configurar y ejecutar el clúster. Vínculos en las secciones siguientes proporcionan las instrucciones sobre cómo hacerlo. 

### <a name="using-the-azure-portal"></a>Con el Portal de Azure

Para que obtener instrucciones sobre cómo usar el Portal de Azure para ejecutar la acción de secuencia de comandos instalar Zeppelin, consulte [clústeres de personalizar HDInsight con la acción de secuencia de comandos](hdinsight-hadoop-customize-cluster-linux.md#use-a-script-action-from-the-azure-portal). Debe realizar un par de cambios en las instrucciones de este artículo.

* Debe usar la secuencia de comandos para instalar Zeppelin. La secuencia de comandos personalizada para instalar Zeppelin en un clúster de motor de HDInsight está disponible en los siguientes vínculos:
    * Para clústeres motor 1.6.0:`https://hdiconfigactions.blob.core.windows.net/linuxincubatorzeppelinv01/install-zeppelin-spark160-v01.sh`
    * Para clústeres motor 1.5.2:`https://hdiconfigactions.blob.core.windows.net/linuxincubatorzeppelinv01/install-zeppelin-spark151-v01.sh`

* Debe ejecutar la acción de secuencia de comandos únicamente en la headnode.

* La secuencia de comandos no necesita todos los parámetros. 

### <a name="using-hdinsight-net-sdk"></a>Usar HDInsight .NET SDK

Para que obtener instrucciones sobre cómo usar HDInsight .NET SDK para ejecutar la acción de secuencia de comandos instalar Zeppelin, consulte [clústeres de personalizar HDInsight con la acción de secuencia de comandos](hdinsight-hadoop-customize-cluster-linux.md#use-a-script-action-from-the-hdinsight-net-sdk). Debe realizar un par de cambios en las instrucciones de este artículo.

* Debe usar la secuencia de comandos para instalar Zeppelin. La secuencia de comandos personalizada para instalar Zeppelin en un clúster de motor de HDInsight está disponible en los siguientes vínculos:
    * Para clústeres motor 1.6.0:`https://hdiconfigactions.blob.core.windows.net/linuxincubatorzeppelinv01/install-zeppelin-spark160-v01.sh`
    * Para clústeres motor 1.5.2:`https://hdiconfigactions.blob.core.windows.net/linuxincubatorzeppelinv01/install-zeppelin-spark151-v01.sh`

* La secuencia de comandos no necesita todos los parámetros. 

* Establezca el tipo de clúster que está creando en el motor.

### <a name="using-azure-powershell"></a>Uso de PowerShell de Azure

Use el siguiente fragmento de PowerShell para crear un clúster de motor en HDInsight Linux con Zeppelin instalado. Según la versión de clúster motor tiene, debe actualizar el fragmento de código de PowerShell para incluir el vínculo a la secuencia de comandos personalizada correspondiente. 

* Para clústeres motor 1.6.0:`https://hdiconfigactions.blob.core.windows.net/linuxincubatorzeppelinv01/install-zeppelin-spark160-v01.sh`
* Para clústeres motor 1.5.2:`https://hdiconfigactions.blob.core.windows.net/linuxincubatorzeppelinv01/install-zeppelin-spark151-v01.sh`

[AZURE.INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]


    Login-AzureRMAccount
    
    # PROVIDE VALUES FOR THE VARIABLES
    $clusterAdminUsername="admin"
    $clusterAdminPassword="<<password>>"
    $clusterSshUsername="adminssh"
    $clusterSshPassword="<<password>>"
    $clusterName="<<clustername>>"
    $clusterContainerName=$clusterName
    $resourceGroupName="<<resourceGroupName>>"
    $location="<<region>>"
    $storage1Name="<<storagename>>"
    $storage1Key="<<storagekey>>"
    $subscriptionId="<<subscriptionId>>"
    
    Select-AzureRmSubscription -SubscriptionId $subscriptionId
    
    $passwordAsSecureString=ConvertTo-SecureString $clusterAdminPassword -AsPlainText -Force
    $clusterCredential=New-Object System.Management.Automation.PSCredential ($clusterAdminUsername, $passwordAsSecureString)
    $passwordAsSecureString=ConvertTo-SecureString $clusterSshPassword -AsPlainText -Force
    $clusterSshCredential=New-Object System.Management.Automation.PSCredential ($clusterSshUsername, $passwordAsSecureString)
    
    $azureHDInsightConfigs= New-AzureRmHDInsightClusterConfig -ClusterType Spark
    $azureHDInsightConfigs.DefaultStorageAccountKey = $storage1Key
    $azureHDInsightConfigs.DefaultStorageAccountName = "$storage1Name.blob.core.windows.net"
    
    Add-AzureRMHDInsightScriptAction -Config $azureHDInsightConfigs -Name "Install Zeppelin" -NodeType HeadNode -Parameters "void" -Uri "https://hdiconfigactions.blob.core.windows.net/linuxincubatorzeppelinv01/install-zeppelin-spark151-v01.sh"
    
    New-AzureRMHDInsightCluster -Config $azureHDInsightConfigs -OSType Linux -HeadNodeSize "Standard_D12" -WorkerNodeSize "Standard_D12" -ClusterSizeInNodes 2 -Location $location -ResourceGroupName $resourceGroupName -ClusterName $clusterName -HttpCredential $clusterCredential -DefaultStorageContainer $clusterContainerName -SshCredential $clusterSshCredential -Version "3.3"
 
## <a name="set-up-ssh-tunneling-to-access-a-zeppelin-notebook"></a>Configurar el SSH túnel para obtener acceso a un bloc de notas de globo dirigible

SSH túneles que usa para tener acceso a los blocs de notas de globo dirigible ejecutar en el clúster de motor en HDInsight Linux. Los pasos siguientes muestran cómo crear un túnel SSH mediante ssh PuTTY (Windows) y línea de comandos (Linux).

### <a name="create-a-tunnel-using-the-ssh-command-linux"></a>Crear un túnel con el comando SSH (Linux)

Use el comando siguiente para crear un SSH túnel utilizando la `ssh` comando. Reemplazar el __nombre de usuario__ con un usuario SSH para el clúster HDInsight y reemplace __NOMBREDECLÚSTER__ con el nombre de su clúster HDInsight

    ssh -C2qTnNf -D 9876 USERNAME@CLUSTERNAME-ssh.azurehdinsight.net

Esto crea una conexión que enruta el tráfico al puerto local 9876 al clúster sobre SSH. Las opciones son:

* **D 9876** - el puerto local que se usará para enrutar el tráfico a través del túnel.

* **C** - comprimir todos los datos, porque el tráfico web principalmente texto.

* **2** - forzar SSH para probar la versión 2 del protocolo.

* **preguntas** - modo silencioso.

* **T** - deshabilitar la asignación de ficticio tty, dado que nos estamos reenvío simplemente un puerto.

* **n** - evitar la lectura de STDIN, dado que nos estamos reenvío simplemente un puerto.

* **N** - no ejecutar un comando remoto, ya que nos estamos reenvío simplemente un puerto.

* **f** - ejecutar en segundo plano.

Si ha configurado el clúster con una clave SSH, debe usar la `-i` parámetro y especifique la ruta de acceso a la clave privada de SSH.

Una vez que finalice el comando, el tráfico enviado al puerto 9876 en el equipo local se enrutan sobre Secure Sockets Layer (SSL) en el clúster sede nodo y parecen provenir allí.

### <a name="create-a-tunnel-using-putty-windows"></a>Crear un túnel con PuTTY (Windows)

Use los siguientes pasos para crear un túnel SSH usando PuTTY.

1. Abra PuTTY y escriba la información de conexión. Si no está familiarizado con PuTTY, vea [Usar SSH con basados en Linux Hadoop en HDInsight de Windows](hdinsight-hadoop-linux-use-ssh-windows.md) para obtener información sobre cómo usar con HDInsight.

2. En la sección **categoría** a la izquierda del cuadro de diálogo, expanda la **conexión**, expanda **SSH**y, a continuación, seleccione **túneles**.

3. En el formulario de **Opciones para controlar el reenvío de puerto SSH** , complete la información siguiente:

    * **Puerto de origen** - el puerto en el cliente que desea reenviar. Por ejemplo, **9876**.

    * **Destino** : SSH la dirección para el clúster HDInsight basados en Linux. Por ejemplo, **miClúster ssh.azurehdinsight.net**.

    * **Dinámico** - proxy SOCKS dinámicas permite enrutamiento.

    ![imagen de túnel opciones](./media/hdinsight-apache-spark-use-zeppelin-notebook/puttytunnel.png)

4. Haga clic en **Agregar** para agregar la configuración y, a continuación, haga clic en **Abrir** para abrir una conexión SSH.

5. Cuando se le solicita, inicie sesión en el servidor. Se establezca una sesión SSH y habilitar el túnel.

### <a name="use-the-tunnel-from-your-browser"></a>Usar el túnel desde el explorador

> [AZURE.NOTE] Los pasos descritos en esta sección utilizan el Explorador de FireFox, como está disponible de forma gratuita para sistemas Linux, Unix, Macintosh OS X y Windows. Otros exploradores modernos como Google Chrome, Microsoft Edge o Apple Safari deberían funcionar Sin embargo, el complemento FoxyProxy que se usa en algunos pasos no esté disponible para todos los exploradores.

1. Configurar el explorador para usar **localhost:9876** como un proxy **SOCKS v5** . Aquí tiene el aspecto de la configuración de Firefox. Si utiliza un puerto diferente 9876, cambie el puerto a la que se ha usado:

    ![imagen de la configuración de Firefox](./media/hdinsight-apache-spark-use-zeppelin-notebook/socks.png)

    > [AZURE.NOTE] Seleccionar **Remoto DNS** resolverá solicitudes de sistema de nombres de dominio (DNS) mediante el clúster HDInsight. Si no está seleccionada, se resolverá DNS localmente.

2. Compruebe que el tráfico se ha enrutado a través del túnel vising un sitio como [http://www.whatismyip.com/](http://www.whatismyip.com/) con la configuración de proxy activa y desactiva en Firefox. Mientras que la configuración está habilitada, será la dirección IP para un equipo en el centro de datos de Microsoft Azure.

### <a name="browser-extensions"></a>Extensiones de explorador

Mientras trabaja en la configuración del explorador para utilizar el túnel, normalmente no desea enrutar todo el tráfico a través del túnel. Coincidencia de modelos para las solicitudes de dirección URL de soporte técnico de extensiones de explorador como [FoxyProxy](http://getfoxyproxy.org/) (FoxyProxy estándar o Plus sólo), de modo que se enviará sólo las solicitudes de direcciones URL específicas sobre el túnel.

Si ha instalado FoxyProxy estándar, realice los siguientes pasos para configurarla para reenviar solo el tráfico de HDInsight sobre el túnel.

1. Abra la extensión FoxyProxy en el explorador. Por ejemplo, en Firefox, seleccione el icono de FoxyProxy situada junto al campo de dirección.

    ![icono de foxyproxy](./media/hdinsight-apache-spark-use-zeppelin-notebook/foxyproxy.png)

2. Seleccione **Agregar nuevo Proxy**, seleccione la ficha **General** y, a continuación, escriba un nombre de servidor proxy de **HDInsightProxy**.

    ![foxyproxy general](./media/hdinsight-apache-spark-use-zeppelin-notebook/foxygeneral.png)

3. Seleccione la ficha **Detalles de Proxy** y rellenar los siguientes campos:

    * **Dirección IP o host** - este es el host local, puesto que estamos usando un túnel SSH en el equipo local.

    * **Puerto** - este es el puerto que utiliza para el túnel SSH.

    * **Proxy SOCKS** : seleccione esta opción para habilitar el explorador utilizar el túnel como un proxy.

    * **SOCKS v5** - Seleccione esta opción para establecer la versión necesaria para el servidor proxy.

    ![proxy foxyproxy](./media/hdinsight-apache-spark-use-zeppelin-notebook/foxyproxyproxy.png)

4. Seleccione la ficha **Modelos de dirección URL** y, a continuación, seleccione **Agregar nueva trama**. Utilice la siguiente para definir el patrón y, a continuación, haga clic en **Aceptar**:

    * **Nombre de patrón** - **zeppelinnotebook** - esto es solo un nombre descriptivo para el modelo.

    * **Modelo de dirección URL** - **\*hn0** * - define un patrón que coincida con el nombre de dominio completo interno del extremo donde se hospedan los blocs de notas de Zeppelin. Debido a los blocs de notas de globo dirigible solo están disponibles en la headnode0 del clúster y suele ser el extremo `http://hn0-<string>.internal.cloudapp.net`, utilizando el modelo * *hn0** garantizará que la solicitud se redirige al extremo Zeppelin.

        ![trama de foxyproxy](./media/hdinsight-apache-spark-use-zeppelin-notebook/foxypattern.png)

4. Haga clic en **Aceptar** para agregar al servidor proxy y cerrar **La configuración de Proxy**.

5. En la parte superior del cuadro de diálogo FoxyProxy, cambiar el **Modo de seleccionar** a **los servidores proxy usar según sus patrones predefinidos y prioridades**y, a continuación, haga clic en **Cerrar**.

    ![Seleccione el modo de foxyproxy](./media/hdinsight-apache-spark-use-zeppelin-notebook/selectmode.png)

Después de seguir estos pasos, solo para las direcciones URL que contienen la cadena __hn0__ enrutamiento de peticiones sobre el túnel SSL. 

## <a name="access-the-zeppelin-notebook"></a>El Bloc de notas de Zeppelin de acceso

Una vez haya SSH túnel el programa de instalación, puede usar los siguientes pasos para obtener acceso a blocs de notas de globo dirigible en el clúster de motor siguiendo los pasos siguientes. En esta sección, verá cómo ejecutar sql % y extractos de subárbol %.

1. Desde el explorador web, abra el extremo siguiente:

        http://hn0-myspar:9995

    * **hn0** indica headnode0
    * **myspar** es el primero de seis letras del nombre del clúster de motor.
    * **9995** es el puerto donde el Bloc de notas de globo dirigible es accesible.

2. Crear un nuevo bloc de notas. Desde el panel de encabezado, haga clic en **Bloc de notas**y, a continuación, haga clic en **Crear nueva nota**.

    ![Crear un nuevo bloc de notas de globo dirigible] (./media/hdinsight-apache-spark-use-zeppelin-notebook/hdispark.createnewnote.png "Crear un nuevo bloc de notas de globo dirigible")

    En la misma página, en el título del **Bloc de notas** , debería ver un bloc de notas con el nombre empieza por **XXXXXXXXX de nota**. Haga clic en nuevo bloc de notas.

3. En la página web para el nuevo bloc de notas, haga clic en el encabezado y cambiar el nombre del Bloc de notas si quiere. Presione ENTRAR para guardar el cambio de nombre. Además, asegúrese de que el encabezado de Bloc de notas muestra el estado de **conectado** en la esquina superior derecha.

    ![Estado del Bloc de notas de globo dirigible] (./media/hdinsight-apache-spark-use-zeppelin-notebook/hdispark.newnote.connected.png "Estado del Bloc de notas de globo dirigible")

### <a name="run-sql-statements"></a>Ejecutar instrucciones SQL

4. Cargar datos de ejemplo en una tabla temporal. Cuando se crea un clúster de motor en HDInsight, el archivo de datos de ejemplo, **hvac.csv**, se copia en la cuenta de almacenamiento asociado con **\HdiSamples\SensorSampleData\hvac**.

    En el párrafo vacío que se crea de forma predeterminada en el nuevo bloc de notas, pegue el fragmento de código siguiente.

        // Create an RDD using the default Spark context, sc
        val hvacText = sc.textFile("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")
        
        // Define a schema
        case class Hvac(date: String, time: String, targettemp: Integer, actualtemp: Integer, buildingID: String)
        
        // Map the values in the .csv file to the schema
        val hvac = hvacText.map(s => s.split(",")).filter(s => s(0) != "Date").map(
            s => Hvac(s(0), 
                    s(1),
                    s(2).toInt,
                    s(3).toInt,
                    s(6)
            )
        ).toDF()
        
        // Register as a temporary table called "hvac"
        hvac.registerTempTable("hvac")
        
    Presione **MAYÚS + ENTRAR** o haga clic en el botón **Reproducir** del párrafo ejecutar el fragmento de código. El estado en la esquina derecha del párrafo debe progreso de listo, pendiente, ejecute a terminado. El resultado se muestra en la parte inferior del mismo párrafo. La captura de pantalla tiene el siguiente aspecto:

    ![Crear una tabla temporal de datos sin formato] (./media/hdinsight-apache-spark-use-zeppelin-notebook/hdispark.note.loaddDataintotable.png "Crear una tabla temporal de datos sin formato")

    También puede proporcionar un título a cada párrafo. Desde la esquina derecha, haga clic en el icono **configuración** y, a continuación, haga clic en **Mostrar título**.

5. Ahora puede ejecutar instrucciones SQL de motor en la tabla **hvac** . Pegue la siguiente consulta en un párrafo nuevo. La consulta recupera el identificador de creación y la diferencia entre el destino y las temperaturas reales de cada edificio en una fecha determinada. Presione **MAYÚS + ENTRAR**.

        %sql
        select buildingID, (targettemp - actualtemp) as temp_diff, date 
        from hvac
        where date = "6/1/13" 

    La instrucción **sql %** al principio indica el Bloc de notas para usar el intérprete de SQL de motor. Puede mirar los intérpretes definidos en la pestaña **intérprete** en el encabezado del Bloc de notas.

    La siguiente captura de pantalla muestra el resultado.

    ![Ejecutar una instrucción SQL de motor usando el Bloc de notas] (./media/hdinsight-apache-spark-use-zeppelin-notebook/hdispark.note.sparksqlquery1.png "Ejecutar una instrucción SQL de motor usando el Bloc de notas")

     Para cambiar entre las representaciones distintas para el mismo resultado, haga clic en las opciones de presentación (resaltadas en el rectángulo). Haga clic en **configuración** para elegir qué constituye la clave y los valores en los resultados. La captura de pantalla encima utiliza **buildingID** como la clave y el promedio de **temp_diff** como el valor.

    
6. También puede ejecutar instrucciones SQL de motor mediante las variables de la consulta. El fragmento de código siguiente muestra cómo definir una variable, **Temp**, en la consulta con los posibles valores que desea consultar con. Cuando ejecute la consulta por primera vez, una lista desplegable se rellena automáticamente con los valores especificados para la variable.

        %sql
        select buildingID, date, targettemp, (targettemp - actualtemp) as temp_diff
        from hvac
        where targettemp > "${Temp = 65,65|75|85}" 

    Pegue este fragmento de código en un nuevo párrafo y presione **MAYÚS + ENTRAR**. La siguiente captura de pantalla muestra el resultado.

    ![Ejecutar una instrucción SQL de motor usando el Bloc de notas] (./media/hdinsight-apache-spark-use-zeppelin-notebook/hdispark.note.sparksqlquery2.png "Ejecutar una instrucción SQL de motor usando el Bloc de notas")

    Para las consultas posteriores, puede seleccionar un nuevo valor de la lista desplegable y vuelva a ejecutar la consulta. Haga clic en **configuración** para elegir qué constituye la clave y los valores en los resultados. La captura de pantalla anterior usa **buildingID** como la clave, el promedio de **temp_diff** como el valor y **targettemp** como grupo.

7. Reinicie el intérprete de SQL de motor para salir de la aplicación. Haga clic en la pestaña **intérprete** en la parte superior y el intérprete de motor, haga clic en **reiniciar**.

    ![Reinicie el intepreter Zeppelin] (./media/hdinsight-apache-spark-use-zeppelin-notebook/hdispark.zeppelin.restart.interpreter.png "Reinicie el intepreter Zeppelin")

### <a name="run-hive-statements"></a>Ejecutar instrucciones de la sección

1. Desde el Bloc de notas Zeppelin, haga clic en el botón de **intérprete** .

    ![Intérprete de actualización subárbol] (./media/hdinsight-apache-spark-use-zeppelin-notebook/zeppelin-update-hive-interpreter-1.png "Intérprete de actualización subárbol")

2. El intérprete de **sección** , haga clic en **Editar**.

    ![Intérprete de actualización subárbol] (./media/hdinsight-apache-spark-use-zeppelin-notebook/zeppelin-update-hive-interpreter-2.png "Intérprete de actualización subárbol")

    Actualice las siguientes propiedades.

    * Establezca **default.password** en la contraseña que ha especificado para el usuario administrador al crear el clúster HDInsight Spark.
    * Establezca **default.url** en `jdbc:hive2://<spark_cluster_name>.azurehdinsight.net:443/default;ssl=true?hive.server2.transport.mode=http;hive.server2.thrift.http.path=/hive2`. Reemplazar ** \<spark_cluster_name >** con el nombre de su clúster de motor.
    * Establecer **default.user** en el nombre del usuario al administrador que especificó al crear el clúster. Por ejemplo, el *Administrador*.

3. Haga clic en **Guardar** y cuando se le solicite reiniciar el intérprete de sección, haga clic en **Aceptar**.

4. Crear un nuevo bloc de notas y ejecute la instrucción siguiente para obtener una lista de todas las tablas de sección en el clúster.

        %hive
        SHOW TABLES

    De forma predeterminada, un clúster de HDInsight tiene una tabla de ejemplo denominada **hivesampletable** , verá el siguiente resultado.

    ![Sección de salida] (./media/hdinsight-apache-spark-use-zeppelin-notebook/zeppelin-update-hive-interpreter-3.png "Sección de salida")

5. Ejecute la instrucción siguiente para obtener una lista de los registros de la tabla.

        %hive
        SELECT * FROM hivesampletable LIMIT 5

    Debe un resultado similar al siguiente.

    ![Sección de salida] (./media/hdinsight-apache-spark-use-zeppelin-notebook/zeppelin-update-hive-interpreter-4.png "Sección de salida")

## <a name="seealso"></a>Vea también


* [Información general: Apache motor en HDInsight de Azure](hdinsight-apache-spark-overview.md)

### <a name="scenarios"></a>Escenarios

* [Motor con BI: realizar análisis de datos interactivas con motor en HDInsight con herramientas de inteligencia empresarial](hdinsight-apache-spark-use-bi-tools.md)

* [Motor con Machine aprendizaje: motor de uso en HDInsight para analizar usando HVAC datos de temperatura de creación](hdinsight-apache-spark-ipython-notebook-machine-learning.md)

* [Motor con Machine aprendizaje: motor de uso en HDInsight de predecir los resultados de la inspección de comida](hdinsight-apache-spark-machine-learning-mllib-ipython.md)

* [Motor de flujo de datos: Motor de uso en HDInsight para la creación de aplicaciones de transmisión en tiempo real](hdinsight-apache-spark-eventhub-streaming.md)

* [Análisis de registro de sitio Web con el motor en HDInsight](hdinsight-apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Crear y ejecutar aplicaciones

* [Crear una aplicación independiente de Scala](hdinsight-apache-spark-create-standalone-application.md)

* [Ejecutar trabajos de forma remota en un clúster de motor mediante Livio](hdinsight-apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Herramientas y extensiones

* [Usar el complemento de herramientas de HDInsight para IntelliJ IDEA para crear y enviar el motor Scala aplicaciones](hdinsight-apache-spark-intellij-tool-plugin.md)

* [Usar el complemento de herramientas de HDInsight para IntelliJ IDEA depurar aplicaciones del motor de forma remota](hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)

* [Kernels disponibles para el Bloc de notas de Jupyter en clúster de motor de HDInsight](hdinsight-apache-spark-jupyter-notebook-kernels.md)

* [Usar paquetes externos con blocs de notas Jupyter](hdinsight-apache-spark-jupyter-notebook-use-external-packages.md)

* [Instalar Jupyter en su equipo y conectarse a un clúster de HDInsight Spark](hdinsight-apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Administrar recursos

* [Administrar recursos para el clúster de motor de Apache en HDInsight de Azure](hdinsight-apache-spark-resource-manager.md)

* [Realizar el seguimiento y depuración trabajos que se ejecutan en un clúster de motor de Apache en HDInsight](hdinsight-apache-spark-job-debugging.md)


[hdinsight-versions]: hdinsight-component-versioning.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[azure-management-portal]: https://manage.windowsazure.com/
[azure-create-storageaccount]: storage-create-storage-account.md 







