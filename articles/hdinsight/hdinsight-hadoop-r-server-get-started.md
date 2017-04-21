<properties
   pageTitle="Introducción a servidor R HDInsight (vista preliminar) | Azure"
   description="Aprenda a crear un motor de Apache en clúster HDInsight (Hadoop) que incluya R Server (vista preliminar) y, a continuación, envíe una secuencia de comandos de R en el clúster."
   services="HDInsight"
   documentationCenter=""
   authors="jeffstokes72"
   manager="jhubbard"
   editor="cgronlun"
/>

<tags
   ms.service="HDInsight"
   ms.devlang="R"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="data-services"
   ms.date="08/19/2016"
   ms.author="jeffstok"
/>

# <a name="get-started-using-r-server-on-hdinsight-preview"></a>Empezar a usar R Server en HDInsight (vista previa)

El nivel de premium que se ofrece para HDInsight incluye R Server como parte de su clúster HDInsight (vista preliminar). Esto permite que las secuencias de comandos de R usar MapReduce y motor para ejecutar cálculos distribuidos. En este documento, aprenderá a crear un nuevo servidor R en HDInsight, a continuación, ejecute una secuencia de comandos de R que se muestra con motor para los cálculos de R distribuidos.

![Diagrama de flujo de trabajo para este documento](./media/hdinsight-getting-started-with-r/rgettingstarted.png)

## <a name="prerequisites"></a>Requisitos previos

* __Suscripción de un Azure__: antes de comenzar este tutorial, debe tener una suscripción de Azure. Para obtener más información, vea [obtener Azure la versión de prueba gratuita](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/) .

* __Cliente A Secure Shell (SSH)__: SSH un cliente se usa para conectarse al clúster HDInsight de forma remota y ejecutar comandos directamente en el clúster. Los sistemas Linux, Unix y OS X proporcionan un cliente SSH a través de la `ssh` comando. Para sistemas de Windows, se recomienda [PuTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).

    * __Teclas SSH (opcionales)__: puede proteger la cuenta SSH utilizada para conectar con el clúster mediante una contraseña o una clave pública. Usar una contraseña es más fácil y le permite empezar a trabajar sin necesidad de crear un par de claves públicas y privadas; Sin embargo, es más seguro con una clave.
    
        Los pasos de este documento se supone que está utilizando una contraseña. Para obtener información sobre cómo crear y usar las teclas SSH con HDInsight, consulte los siguientes documentos:
        
        * [Utilizar SSH con HDInsight de clientes Linux, Unix o OS X](hdinsight-hadoop-linux-use-ssh-unix.md)
        
        * [Utilizar SSH con HDInsight de clientes de Windows](hdinsight-hadoop-linux-use-ssh-windows.md)

### <a name="access-control-requirements"></a>Requisitos de control de acceso

[AZURE.INCLUDE [access-control](../../includes/hdinsight-access-control-requirements.md)]

## <a name="create-the-cluster"></a>Crear el clúster

> [AZURE.NOTE] Los pasos de este documento crean un servidor R en HDInsight con información de configuración básica. Para otras opciones de configuración de clúster (por ejemplo, agregar cuentas de almacenamiento adicional, utilizando una red Virtual de Azure o crear un metastore para sección), consulte [HDInsight basados en Linux crear clústeres](hdinsight-hadoop-provision-linux-clusters.md).

1. Inicie sesión en el [portal de Azure](https://portal.azure.com).

2. Seleccione __nuevo__, __datos + análisis__y, a continuación, __HDInsight__.

    ![Imagen de la creación de un nuevo clúster](./media/hdinsight-getting-started-with-r/newcluster.png)

3. Escriba un nombre para el clúster en el campo __Nombre de clúster__ . Si tiene varias suscripciones de Azure, use la entrada de __suscripción__ para seleccionar el que desee usar.

    ![Selecciones de nombre y la suscripción de clúster](./media/hdinsight-getting-started-with-r/clustername.png)

4. Seleccionar __tipo de clúster__. En el módulo de __Tipo de clúster__ , seleccione las opciones siguientes:

    * __Tipo de clúster__: servidor R motor
    
    * __Nivel de clúster__: Premium

    Deje el resto de opciones con los valores predeterminados y luego use el botón __Seleccionar__ para guardar el tipo de clúster.
    
    ![Captura de pantalla del módulo de tipo de clúster](./media/hdinsight-getting-started-with-r/clustertypeconfig.png)
    
    > [AZURE.NOTE] También puede agregar servidor R a otros tipos de clúster HDInsight (como Hadoop o HBase,) seleccionando el tipo de clúster y, a continuación, seleccione __Premium__.

5. Seleccione el **Grupo de recursos** para ver una lista de grupos de recursos existentes y, a continuación, seleccione una para crear el clúster de. O bien, puede seleccionar **Crear nuevo** y, a continuación, escriba el nombre del nuevo grupo de recursos. Aparecerá una marca de verificación verde para indicar que está disponible el nuevo nombre de grupo.

    > [AZURE.NOTE] Esta entrada predeterminada uno de sus grupos de recursos existentes, si alguno está disponible.
    
    Use el botón __Seleccionar__ para guardar el grupo de recursos.

6. Seleccione **las credenciales**y luego escriba un **Nombre de usuario de inicio de sesión de clúster** y una **Contraseña de inicio de sesión de clúster**.

    Escriba un __nombre de usuario SSH__.  SSH se usa para conectarse al clúster mediante un cliente de __Shell seguro (SSH)__ de forma remota. Puede especificar el usuario SSH en este cuadro de diálogo o una vez creado el clúster (ficha de configuración para el clúster). R servidor está configurado para pasar un __nombre de usuario SSH__ de "remoteuser".  Si usa otro nombre de usuario, tendrá que realizar un paso adicional después de crear el clúster.
    
    ![Módulo de credenciales](./media/hdinsight-getting-started-with-r/clustercredentials.png)

    __Tipo de autenticación SSH__: seleccione __contraseña__ como el tipo de autenticación, a menos que prefiera usar de una clave pública.  Si desea tener acceso al servidor R en el clúster a través de un cliente remoto, por ejemplo, RTV, RStudio u otro escritorio IDE tendrá un par de claves públicas y privadas.   

    Para crear y usar una clave pública y privada seleccione 'Clave pública' de par y realice lo siguiente.  Estas instrucciones se suponen que tiene Cygwin con ssh keygen o equivalente instalado.

    -    Generar un par de claves públicas y privadas desde el símbolo del sistema en su ordenador portátil:
      
            SSH keygen - t rsa -b 2048 – f < nombre de la clave privada >
      
    -    Esta opción creará un archivo de clave privada y un archivo de clave pública en el nombre < nombre de la clave privada > .pub, p. ej.  davec y davec.pub.  A continuación, especifique el archivo de clave pública (*. pub) al asignar credenciales de clúster HDI:
      
        ![Módulo de credenciales](./media/hdinsight-getting-started-with-r/publickeyfile.png)  
      
    -    Cambiar permisos en el archivo de clave privada en su ordenador portátil
      
            chmod 600 < nombre de la clave privada >
      
    -    Usar el archivo de clave privada con SSH para inicio de sesión remota, por ejemplo
      
            SSH – i < nombre de la clave privada >remoteuser@<hostname public ip>
      
      o como parte de la definición del contexto cálculo Hadoop Spark para servidor de R en el cliente (como un cliente de Hadoop en la sección [crear un contexto calcular para motor](https://msdn.microsoft.com/microsoft-r/scaler-spark-getting-started#creating-a-compute-context-for-spark) de [RevoScaleR Hadoop motor Guía de introducción](https://msdn.microsoft.com/microsoft-r/scaler-spark-getting-started)en pantalla, consulte Using Microsoft R Server).

7. Seleccione el **Origen de datos** para seleccionar un origen de datos para el clúster. Seleccione una cuenta existente de almacenamiento seleccionando __Seleccione cuenta de almacenamiento__ y, a continuación, seleccione la cuenta, o cree una nueva cuenta con el vínculo de __nuevo__ en la sección __Seleccione la cuenta de almacenamiento__ .

    Si selecciona __nuevo__, debe escribir un nombre para la nueva cuenta de almacenamiento. Si se acepta el nombre, aparecerá una marca de verificación verde.

    El nombre del clúster de forma predeterminada el __Contenedor predeterminado__ . Deje que el valor.
    
    Seleccione la __ubicación__ para seleccionar la región para crear la cuenta de almacenamiento en.
    
    > [AZURE.IMPORTANT] Seleccionar la ubicación de origen de datos predeterminado, también se establece la ubicación del clúster HDInsight. El origen de datos predeterminada y clúster debe estar ubicado en la misma región.

    Use el botón **Seleccionar** para guardar la configuración de origen de datos.
    
    ![Módulo de origen de datos](./media/hdinsight-getting-started-with-r/datastore.png)

8. Seleccione **Niveles de precios de nodo** para mostrar información sobre los nodos que se crearán para este clúster. A menos que sepa que tendrá un clúster de mayor tamaño, deje el número de nodos de trabajo con el valor predeterminado de `4`. El costo estimado del clúster se mostrarán en el módulo.

    > [AZURE.NOTE] Si es necesario, puede volver a cambiar el tamaño del clúster más adelante a través del Portal (clúster -> Configuración -> escala clúster) para aumentar o reducir el número de nodos de trabajo.  Esto puede ser útil para ralentí hacia abajo el clúster cuando no esté en uso, o para agregar la capacidad para satisfacer las necesidades de las tareas más grandes.

    Se incluyen algunos factores que debe tener en cuenta al cambiar el tamaño del clúster, los nodos de datos y el nodo del borde:  
   
    - El rendimiento de análisis de servidor R distribuidos en motor es proporcional al número de nodos de trabajo cuando los datos están grandes.  
    - El rendimiento de análisis de servidor R es lineal en el tamaño de los datos que se está analizadas. Por ejemplo:  
        - Datos pequeña para pequeña, el rendimiento será mejor cuando se analizan en un contexto de cálculo local en el nodo del borde.  Para obtener más información sobre los casos en los que el equipo local y contextos de cálculo motor funcionan mejor vea Opciones de contexto de cálculo para R Server en HDInsight.<br>
        - Aunque puede iniciar sesión en el nodo del borde y ejecutar la secuencia de comandos de R allí, a continuación, todas las funciones de recepción de utilizar otros equipos ejecutarán <strong>localmente</strong> en el nodo del borde para la memoria y el número de núcleos del nodo borde deben ajustarse según corresponda. Lo mismo se aplica si usa servidor R en HDI como un contexto de cálculo remoto desde su equipo portátil.
    
    ![Módulo de niveles de precios de nodo](./media/hdinsight-getting-started-with-r/pricingtier.png)

    Use el botón **Seleccionar** para guardar el nodo Configuración de precios.
    
9. En el módulo **Nuevo HDInsight clúster** , asegúrese de que el **Pin para Startboard** está seleccionada y haga clic en **crear**. Esto creará el clúster y agregar un mosaico correspondiente a la Startboard de su Portal de Azure. El icono indicará que está creando el clúster y cambiará para mostrar el icono de HDInsight una vez completada la creación.

  	| Al crear | Creación de completada |
  	| ------------------ | --------------------- |
  	| ![Creando indicador en startboard](./media/hdinsight-getting-started-with-r/provisioning.png) | ![Crear el mosaico de clúster](./media/hdinsight-getting-started-with-r/provisioned.png) |

    > [AZURE.NOTE] Esta acción tardará algún tiempo para que el clúster creado, normalmente unos 15 minutos. Use el mosaico de la Startboard o la entrada de **notificaciones** de la izquierda de la página para comprobar el proceso de creación.

## <a name="connect-to-the-r-server-edge-node"></a>Conectar con el nodo de R servidor perimetral

Conectar con el nodo del borde R servidor del clúster HDInsight mediante SSH:

    ssh USERNAME@r-server.CLUSTERNAME-ssh.azurehdinsight.net
    
> [AZURE.NOTE] También puede encontrar el `R-Server.CLUSTERNAME-ssh.azurehdinsight.net` dirección en el portal de Azure seleccionando su clúster, a continuación, __Configuración de todas las__ __aplicaciones__y __RServer__. Esta opción mostrará la información del extremo SSH para el nodo del borde.
>
> ![Imagen del SSH extremo para el nodo del borde](./media/hdinsight-getting-started-with-r/sshendpoint.png)
    
Si utiliza una contraseña para proteger su cuenta de usuario SSH, se le pedirá que lo especifique. Si utiliza una clave pública, tendrá que usar la `-i` parámetro para especificar la clave privada correspondiente. Por ejemplo, `ssh -i ~/.ssh/id_rsa USERNAME@R-Server.CLUSTERNAME-ssh.azurehdinsight.net`.
    
Para obtener más información sobre cómo utilizar SSH con HDInsight de Linux, consulte los siguientes artículos:

* [Utilizar SSH con basados en Linux Hadoop en HDInsight de Linux, Unix o OS X](hdinsight-hadoop-linux-use-ssh-unix.md)

* [Utilizar SSH con basados en Linux Hadoop en HDInsight de Windows.](hdinsight-hadoop-linux-use-ssh-windows.md)

Una vez conectado, llegarán en un mensaje similar al siguiente.

    username@ed00-myrser:~$

## <a name="use-the-r-console"></a>Utilice la consola de R

1. Desde la sesión SSH, utilice el comando siguiente para iniciar la consola de R.

        R
    
    Verá el resultado similar al siguiente.
    
        R version 3.2.2 (2015-08-14) -- "Fire Safety"
        Copyright (C) 2015 The R Foundation for Statistical Computing
        Platform: x86_64-pc-linux-gnu (64-bit)

        R is free software and comes with ABSOLUTELY NO WARRANTY.
        You are welcome to redistribute it under certain conditions.
        Type 'license()' or 'licence()' for distribution details.

        Natural language support but running in an English locale

        R is a collaborative project with many contributors.
        Type 'contributors()' for more information and
        'citation()' on how to cite R or R packages in publications.

        Type 'demo()' for some demos, 'help()' for on-line help, or
        'help.start()' for an HTML browser interface to help.
        Type 'q()' to quit R.

        Microsoft R Server version 8.0: an enhanced distribution of R
        Microsoft packages Copyright (C) 2016 Microsoft Corporation

        Type 'readme()' for release notes.

        >

2. Desde el `>` símbolo del sistema, introduzca el código de R. Servidor de R incluye paquetes que permiten interactuar con Hadoop y ejecutar cálculos distribuidos fácilmente. Por ejemplo, utilice el comando siguiente para ver la raíz del sistema de archivo predeterminado para el clúster de HDInsight.

        rxHadoopListFiles("/")
    
    También puede usar el direccionamiento de estilo WASB.
    
        rxHadoopListFiles("wasbs:///")

## <a name="using-r-server-on-hdi-from-a-remote-instance-of-microsoft-r-server-or-microsoft-r-client"></a>Usando R Server HDI desde una instancia remota de Microsoft R servidor o cliente de Microsoft R

Por la sección anterior sobre el uso de pares de claves públicas y privadas para tener acceso al clúster, es posible configurar access para el contexto de cálculo HDI Hadoop motor de una instancia remota de Microsoft R Server o Microsoft R cliente que se ejecuta en un escritorio o portátil (consulte Using Microsoft R Server como un cliente de Hadoop en la sección [crear un contexto calcular para motor](https://msdn.microsoft.com/microsoft-r/scaler-spark-getting-started#creating-a-compute-context-for-spark) de [RevoScaleR Hadoop motor Guía de introducción](https://msdn.microsoft.com/microsoft-r/scaler-spark-getting-started)en pantalla).  Para ello, debe especificar las siguientes opciones al definir el RxSpark calculan contexto en su ordenador portátil: hdfsShareDir, shareDir, sshUsername, sshHostname, sshSwitches y sshProfileScript. Por ejemplo:

    
    myNameNode <- "default"
    myPort <- 0 
 
    mySshHostname  <- 'rkrrehdi1-ssh.azurehdinsight.net'  # HDI secure shell hostname
    mySshUsername  <- 'remoteuser'# HDI SSH username
    mySshSwitches  <- '-i /cygdrive/c/Data/R/davec'   # HDI SSH private key
 
    myhdfsShareDir <- paste("/user/RevoShare", mySshUsername, sep="/")
    myShareDir <- paste("/var/RevoShare" , mySshUsername, sep="/")
 
    mySparkCluster <- RxSpark(
      hdfsShareDir = myhdfsShareDir,
      shareDir     = myShareDir,
      sshUsername  = mySshUsername,
      sshHostname  = mySshHostname,
      sshSwitches  = mySshSwitches,
      sshProfileScript = '/etc/profile',
      nameNode     = myNameNode,
      port         = myPort,
      consoleOutput= TRUE
    )

    
 
## <a name="use-a-compute-context"></a>Usar un contexto de cálculo

Un cálculo contexto le permite controlar si se realizará ningún cálculo localmente en el nodo del borde o si se distribuirá a través de los nodos en el clúster HDInsight.
        
1. Desde la consola R, use las siguientes acciones para cargar los datos de ejemplo en el almacenamiento predeterminado para HDInsight.

        # Set the HDFS (WASB) location of example data
        bigDataDirRoot <- "/example/data"
        # create a local folder for storaging data temporarily
        source <- "/tmp/AirOnTimeCSV2012"
        dir.create(source)
        # Download data to the tmp folder
        remoteDir <- "http://packages.revolutionanalytics.com/datasets/AirOnTimeCSV2012"
        download.file(file.path(remoteDir, "airOT201201.csv"), file.path(source, "airOT201201.csv"))
        download.file(file.path(remoteDir, "airOT201202.csv"), file.path(source, "airOT201202.csv"))
        download.file(file.path(remoteDir, "airOT201203.csv"), file.path(source, "airOT201203.csv"))
        download.file(file.path(remoteDir, "airOT201204.csv"), file.path(source, "airOT201204.csv"))
        download.file(file.path(remoteDir, "airOT201205.csv"), file.path(source, "airOT201205.csv"))
        download.file(file.path(remoteDir, "airOT201206.csv"), file.path(source, "airOT201206.csv"))
        download.file(file.path(remoteDir, "airOT201207.csv"), file.path(source, "airOT201207.csv"))
        download.file(file.path(remoteDir, "airOT201208.csv"), file.path(source, "airOT201208.csv"))
        download.file(file.path(remoteDir, "airOT201209.csv"), file.path(source, "airOT201209.csv"))
        download.file(file.path(remoteDir, "airOT201210.csv"), file.path(source, "airOT201210.csv"))
        download.file(file.path(remoteDir, "airOT201211.csv"), file.path(source, "airOT201211.csv"))
        download.file(file.path(remoteDir, "airOT201212.csv"), file.path(source, "airOT201212.csv"))
        # Set directory in bigDataDirRoot to load the data into
        inputDir <- file.path(bigDataDirRoot,"AirOnTimeCSV2012") 
        # Make the directory
        rxHadoopMakeDir(inputDir)
        # Copy the data from source to input
        rxHadoopCopyFromLocal(source, bigDataDirRoot)

2. A continuación, vamos a crear alguna información de datos y definir dos orígenes de datos para que podemos trabajar con los datos.

        # Define the HDFS (WASB) file system
        hdfsFS <- RxHdfsFileSystem()
        # Create info list for the airline data
        airlineColInfo <- list(
            DAY_OF_WEEK = list(type = "factor"),
            ORIGIN = list(type = "factor"),
            DEST = list(type = "factor"),
            DEP_TIME = list(type = "integer"),
            ARR_DEL15 = list(type = "logical"))

        # get all the column names
        varNames <- names(airlineColInfo)

        # Define the text data source in hdfs
        airOnTimeData <- RxTextData(inputDir, colInfo = airlineColInfo, varsToKeep = varNames, fileSystem = hdfsFS)
        # Define the text data source in local system
        airOnTimeDataLocal <- RxTextData(source, colInfo = airlineColInfo, varsToKeep = varNames)

        # formula to use
        formula = "ARR_DEL15 ~ ORIGIN + DAY_OF_WEEK + DEP_TIME + DEST"

3. Vamos a ejecutar una regresión logística sobre los datos con el equipo local calcular contexto.

        # Set a local compute context
        rxSetComputeContext("local")
        # Run a logistic regression
        system.time(
            modelLocal <- rxLogit(formula, data = airOnTimeDataLocal)
        )
        # Display a summary 
        summary(modelLocal)

    Debería ver el resultado que termina con líneas similares al siguiente.

        Data: airOnTimeDataLocal (RxTextData Data Source)
        File name: /tmp/AirOnTimeCSV2012
        Dependent variable(s): ARR_DEL15
        Total independent variables: 634 (Including number dropped: 3)
        Number of valid observations: 6005381
        Number of missing observations: 91381
        -2*LogLikelihood: 5143814.1504 (Residual deviance on 6004750 degrees of freedom)

        Coefficients:
                        Estimate Std. Error z value Pr(>|z|)
        (Intercept)   -3.370e+00  1.051e+00  -3.208  0.00134 **
        ORIGIN=JFK     4.549e-01  7.915e-01   0.575  0.56548
        ORIGIN=LAX     5.265e-01  7.915e-01   0.665  0.50590
        ......
        DEST=SHD       5.975e-01  9.371e-01   0.638  0.52377
        DEST=TTN       4.563e-01  9.520e-01   0.479  0.63172
        DEST=LAR      -1.270e+00  7.575e-01  -1.676  0.09364 .
        DEST=BPT         Dropped    Dropped Dropped  Dropped
        ---
        Signif. codes:  0 ‘***’ 0.001 ‘**’ 0.01 ‘*’ 0.05 ‘.’ 0.1 ‘ ’ 1

        Condition number of final variance-covariance matrix: 11904202
        Number of iterations: 7

4. A continuación, vamos a ejecutar el misma regresión logística utilizando el contexto de motor. El contexto de motor distribuirá el procesamiento por todos los nodos de trabajo en el clúster HDInsight.

        # Define the Spark compute context 
        mySparkCluster <- RxSpark()
        # Set the compute context 
        rxSetComputeContext(mySparkCluster)
        # Run a logistic regression 
        system.time(  
            modelSpark <- rxLogit(formula, data = airOnTimeData)
        )
        # Display a summary
        summary(modelSpark)

    > [AZURE.NOTE] También puede usar MapReduce distribuir cálculo entre nodos de clúster. Para obtener más información sobre el contexto de cálculo, vea [calcular las opciones de contexto para servidor R HDInsight premium](hdinsight-hadoop-r-server-compute-contexts.md).

## <a name="distribute-r-code-to-multiple-nodes"></a>Distribuir varios nodos de código R

Con el servidor de R fácilmente puede tomar código R existente y ejecutar en varios nodos en el clúster mediante `rxExec`. Esto es útil cuando se realiza un barrido de parámetro o simulaciones. El siguiente es un ejemplo de cómo usar `rxExec`.

    rxExec( function() {Sys.info()["nodename"]}, timesToRun = 4 )
    
Si sigue usando el contexto de motor o MapReduce, devolverá el valor nodename para los nodos de trabajo que el código (`Sys.info()["nodename"]`) se ha encontrado. Por ejemplo, en un clúster de cuatro nodos, puede recibir un resultado similar al siguiente.

    $rxElem1
        nodename
    "wn3-myrser"

    $rxElem2
        nodename
    "wn0-myrser"

    $rxElem3
        nodename
    "wn3-myrser"

    $rxElem4
        nodename
    "wn3-myrser"

## <a name="install-r-packages"></a>Instalar paquetes de R

Si desea instalar paquetes de R adicionales en el nodo del borde, puede usar `install.packages()` directamente desde dentro de la R de consola cuando conectado al nodo de borde mediante SSH. Sin embargo, si necesita instalar paquetes de R en los nodos de trabajo del clúster, debe usar una acción de secuencia de comandos.

Acciones de script son secuencias de comandos de fiesta que se usan para realizar cambios de configuración en el clúster HDInsight o instalar software adicional. En este caso, R adicional de instalar paquetes. Para instalar paquetes adicionales con una acción de secuencia de comandos, realice los siguientes pasos.

> [AZURE.IMPORTANT] Usar acciones de secuencia de comandos para instalar paquetes de R adicionales sólo puede utilizarse una vez creado el clúster. No se debe utilizar durante la creación de clúster, como la secuencia de comandos se basa en el servidor de R completamente instalado y configurado.

1. Desde el [portal de Azure](https://portal.azure.com), seleccione su servidor R en clúster HDInsight.

2. En el módulo de clúster, seleccione __Toda la configuración__y, a continuación, __Acciones de Script__. En el módulo de __Secuencia de comandos de acciones__ , seleccione __Nueva enviar__ para enviar una nueva acción de secuencia de comandos.

    ![Imagen del módulo de acciones de script](./media/hdinsight-getting-started-with-r/newscriptaction.png)

3. Desde el módulo de __acción de secuencia de comandos de envío__ , proporcione la siguiente información.

  - __Nombre__: un nombre descriptivo para identificar esta secuencia de comandos
  - __Bash URI de secuencia de comandos__:`http://mrsactionscripts.blob.core.windows.net/rpackages-v01/InstallRPackages.sh`
  - __Cabeza__: Esto debe estar __desactivada__
  - __Trabajo__: debe ser __activada__
  - __Zookeeper__: este debe estar __desactivada__
  - __Parámetros__: paquetes de R de la instalación. Por ejemplo,`bitops stringr arules`
  - __Persistentes este script..__: debe ser __activada__  

    > [AZURE.NOTE] 1. de forma predeterminada, todos los paquetes de R se instalan desde una instantánea del repositorio de Microsoft MRAN coherente con la versión del servidor de R que se ha instalado.  Si desea instalar las nuevas versiones de paquetes, a continuación, hay algunos riesgos de incompatibilidad, pero es posible especificando `useCRAN` como el primer elemento del paquete de lista, p. ej.  `useCRAN bitops, stringr, arules`.  
    > 2. Algunos paquetes R necesitarán otras bibliotecas de sistema de Linux. Para su comodidad, nos hemos preinstalada las dependencias necesarias para los paquetes de R más populares 100 superiores. Sin embargo, si los paquetes de R que instalar requieren bibliotecas más allá de estos, debe descargar el script base que se utiliza aquí y agregue los pasos para instalar las bibliotecas del sistema. A continuación, debe cargar el script modificado en un contenedor de blob público en el almacenamiento de Azure y use la secuencia de comandos modificada para instalar los paquetes.
    > Para obtener más información sobre el desarrollo de acciones de secuencia de comandos, vea [desarrollo de secuencia de comandos de acción](hdinsight-hadoop-script-actions-linux.md).  

    ![Agregar una acción de secuencia de comandos](./media/hdinsight-getting-started-with-r/scriptaction.png)

4. Seleccione __crear__ para ejecutar la secuencia de comandos. Una vez completada la secuencia de comandos, los paquetes de R estarán disponibles en todos los nodos de trabajo.
    
## <a name="next-steps"></a>Pasos siguientes

Ahora que sabe cómo crear un nuevo clúster de HDInsight que incluye R Server y los conceptos básicos del uso de la consola de R desde una sesión SSH, use las siguientes acciones para descubrir otras maneras de trabajar con el servidor de R en HDInsight.

- [Agregar servidor de RStudio a HDInsight premium](hdinsight-hadoop-r-server-install-r-studio.md)

- [Calcular las opciones de contexto de servidor R en HDInsight premium](hdinsight-hadoop-r-server-compute-contexts.md)

- [Azure opciones de almacenamiento para servidor R en HDInsight premium](hdinsight-hadoop-r-server-storage.md)

### <a name="azure-resource-manager-templates"></a>Plantillas de administrador de recursos de Azure

Si está interesado en automatizar la creación del servidor R HDInsight con el Administrador de recursos de Azure plantillas, vea las siguientes plantillas de ejemplo.

* [Crear un servidor R en un clúster de HDInsight mediante una clave pública de SSH](http://go.microsoft.com/fwlink/p/?LinkID=780809)
* [Crear un servidor R en un clúster de HDInsight mediante una contraseña SSH](http://go.microsoft.com/fwlink/p/?LinkID=780810)

Ambas plantillas de crear un nuevo clúster de HDInsight y una cuenta de almacenamiento asociado y pueden usarse de Azure CLI, Azure PowerShell o el Portal de Azure.

Para obtener información general sobre el uso de plantillas de administrador de recursos de Azure, consulte [Hadoop basados en Linux crear clústeres en HDInsight con plantillas de administrador de recursos de Azure](hdinsight-hadoop-create-linux-clusters-arm-templates.md).
