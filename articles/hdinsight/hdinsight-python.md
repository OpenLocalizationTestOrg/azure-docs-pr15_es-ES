<properties
    pageTitle="Utilizar Python con la sección y cerdo en HDInsight | Microsoft Azure"
    description="Obtenga información sobre cómo usar Python usuario definido funciones (UDF) de la sección y cerdo en HDInsight, la pila de tecnología de Hadoop en Azure."
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
    ms.devlang="python"
    ms.topic="article"
    ms.date="09/07/2016" 
    ms.author="larryfr"/>

#<a name="use-python-with-hive-and-pig-in-hdinsight"></a>Utilizar Python con la sección y cerdo en HDInsight

La sección y cerdo son una buena opción para trabajar con datos en HDInsight, pero a veces necesita más general propósito idioma. La sección y cerdo permiten crear funciones definidas por el usuario (UDF) con una amplia variedad de lenguajes de programación. En este artículo, aprenderá cómo usar una UDF Python desde la sección y cerdo.

##<a name="requirements"></a>Requisitos

* Un clúster de HDInsight (Windows o Linux)

* Un editor de texto

    > [AZURE.IMPORTANT] Si está utilizando un servidor de HDInsight basados en Linux, pero crear los archivos de Python en un cliente de Windows, debe usar un editor que utiliza LF como un fin de línea. Si no está seguro de si el editor utiliza LF o CRLF, vea la sección de [solución de problemas](#troubleshooting) para conocer los pasos sobre cómo quitar el carácter CR mediante utilidades en el clúster HDInsight.
    
##<a name="python"></a>Python en HDInsight

Python2.7 se instala de forma predeterminada en HDInsight 3.0 y clústeres posterior. Sección se puede utilizar con esta versión de Python para el procesamiento de secuencia (los datos se pasan entre la sección y Python usando STDOUT/entrada estándar).

HDInsight también incluye Jython, que es una implementación de Python escrita en Java. Cerdo sabe cómo hablar con Jython sin tener que utilizar transmisión, por lo que es preferible al usar cerdo. Sin embargo, también puede utilizar Python normal (C Python), con cerdo también.

##<a name="hivepython"></a>La sección y Python

Se puede utilizar Python como un archivo UDF de sección a través de la HiveQL **transformar** declaración. Por ejemplo, el siguiente HiveQL invoca un script de Python almacenado en el archivo **streaming.py** .

**HDInsight de Linux**

    add file wasbs:///streaming.py;

    SELECT TRANSFORM (clientid, devicemake, devicemodel)
      USING 'python streaming.py' AS
      (clientid string, phoneLable string, phoneHash string)
    FROM hivesampletable
    ORDER BY clientid LIMIT 50;

**HDInsight de Windows**

    add file wasbs:///streaming.py;

    SELECT TRANSFORM (clientid, devicemake, devicemodel)
      USING 'D:\Python27\python.exe streaming.py' AS
      (clientid string, phoneLable string, phoneHash string)
    FROM hivesampletable
    ORDER BY clientid LIMIT 50;

> [AZURE.NOTE] En clústeres HDInsight basado en Windows, la cláusula **USING** debe especificar la ruta completa al python.exe. Esto es siempre `D:\Python27\python.exe`.

Esto es lo que hace este ejemplo:

1. La instrucción **Agregar archivo** al principio del archivo agrega el archivo de **streaming.py** a la caché distribuida, por lo que es accesible para todos los nodos en el clúster.

2. La transformación **seleccione... USAR** instrucción selecciona datos de la **hivesampletable**y pasa clientid, devicemake y devicemodel a la secuencia de comandos de **streaming.py** .

3. La cláusula **AS** , describe los campos devueltos de **streaming.py**

<a name="streamingpy"></a>Éste es el archivo de **streaming.py** que se utiliza en el ejemplo HiveQL.

    #!/usr/bin/env python

    import sys
    import string
    import hashlib

    while True:
      line = sys.stdin.readline()
      if not line:
        break

      line = string.strip(line, "\n ")
      clientid, devicemake, devicemodel = string.split(line, "\t")
      phone_label = devicemake + ' ' + devicemodel
      print "\t".join([clientid, phone_label, hashlib.md5(phone_label).hexdigest()])

Puesto que estamos usando la transmisión, esta secuencia de comandos tiene que hacer lo siguiente:

1. Leerá datos desde origen. Esto se logra utilizando `sys.stdin.readline()` en este ejemplo.

2. El carácter de nueva línea al final se quita mediante `string.strip(line, "\n ")`, ya que queremos solo los datos de texto y no el final del indicador de la línea.

2. Cuando se realiza el procesamiento de secuencia, una sola línea contiene todos los valores con un carácter de tabulación entre cada valor. Así que `string.split(line, "\t")` puede utilizarse para dividir la entrada en cada ficha, devolver solo los campos.

3. Cuando se completa el proceso, el resultado debe escribirse stdout como una sola línea, con una pestaña entre cada campo. Esto se logra utilizando `print "\t".join([clientid, phone_label, hashlib.md5(phone_label).hexdigest()])`.

4. Todo esto ocurre dentro de un `while` bucle, que se repetirá hasta que no `line` leer, momento en que `break` salidas termina el bucle y la secuencia de comandos.

Además, la secuencia de comandos simplemente concatena los valores de entrada para `devicemake` y `devicemodel`y calcula un hash del valor concatenado. Bastante sencilla, pero se describen los conceptos básicos de funcionan de las secuencias de comandos de Python invoca subárbol: bucle, lectura de entrada hasta que no hay nada más, separar cada línea de entrada en las pestañas, proceso, escribir una sola línea de salida delimitado por tabulaciones.

Ver [ejecutando los ejemplos](#running) de cómo ejecutar este ejemplo en el clúster HDInsight.

##<a name="pigpython"></a>Cerdo y Python

Una secuencia de comandos de Python se puede utilizar como un archivo UDF de cerdo a través de la instrucción de **Generar** . Hay dos formas de hacerlo; usar Jython (Python implementado en la máquina Virtual de Java) y C Python (Python normal). 

La diferencia principal entre estos son que Jython se ejecuta en JVM y forma nativa se puede llamar desde cerdo (también se ejecuta en JVM.) C Python es un proceso externo (escrito en C.) Así que los datos de cerdo en JVM se envían a la secuencia de comandos que se ejecutan en un proceso de Python, a continuación, el resultado de la se envía a cerdo.

Para determinar si cerdo utiliza Jython o C Python para ejecutar la secuencia de comandos, use __registrar__ al hacer referencia a la secuencia de comandos de Python de cerdo latino. Esto indica cerdo qué intérprete que utilice y qué alias para crear la secuencia de comandos. Los ejemplos siguientes registrar secuencias de comandos con cerdo como __myfuncs__:

* __Usar Jython__:`register '/path/to/pig_python.py' using jython as myfuncs;`
* __Usar Python C__:`register '/path/to/pig_python.py' using streaming_python as myfuncs;`

> [AZURE.IMPORTANT] Al utilizar Jython, la ruta de acceso al archivo pig_jython puede ser una ruta de acceso local o una WASB: / / ruta de acceso. Sin embargo, cuando se usa C Python, debe hacer referencia un archivo en el sistema de archivos local del nodo que va a usar para enviar el trabajo de cerdo.

Una vez más allá del registro, el latino cerdo en este ejemplo es el mismo para ambos:

    LOGS = LOAD 'wasbs:///example/data/sample.log' as (LINE:chararray);
    LOG = FILTER LOGS by LINE is not null;
    DETAILS = FOREACH LOG GENERATE myfuncs.create_structure(LINE);
    DUMP DETAILS;

Esto es lo que hace este ejemplo:

1. La primera línea cargue el archivo de datos de ejemplo, **sample.log** en **registros**. Dado que este archivo de registro no tiene un esquema coherente, define como un **chararray**de cada registro (en este caso,**línea** ). CharArray es, esencialmente, una cadena.

2. La siguiente línea filtra los valores nulos, almacenar el resultado de la operación en el **registro**.

3. A continuación, itera por los registros de **registro** y **Generar** se utiliza para invocar el método **create_structure** contenido en la secuencia de comandos de Python o Jython cargado como **myfuncs**.  **Línea** se usa para pasar el registro actual a la función.

4. Por último, los resultados se guardan en STDOUT con el comando de **descarga** . Esto es solo mostrar inmediatamente los resultados después de que finalice la operación; en una secuencia de comandos real lo haría normalmente en el **almacén de** los datos en un archivo nuevo.

El archivo de script de Python real también es similar entre Python C y Jython, la única diferencia es que debe importar desde __cerdo\_utilizada__ cuando se usen C Python. Esto es el __cerdo\_python.py__ secuencia de comandos:

<a name="streamingpy"></a>

    # Uncomment the following if using C Python
    #from pig_util import outputSchema

    @outputSchema("log: {(date:chararray, time:chararray, classname:chararray, level:chararray, detail:chararray)}")
    def create_structure(input):
    if (input.startswith('java.lang.Exception')):
        input = input[21:len(input)] + ' - java.lang.Exception'
    date, time, classname, level, detail = input.split(' ', 4)
    return date, time, classname, level, detail

> [AZURE.NOTE] 'pig_util' no es algo que tiene que preocuparse por instalar; está disponible automáticamente a la secuencia de comandos.

¿Recuerde que anteriormente solo definido la entrada de **línea** como un chararray porque no había ningún esquema coherente para la entrada? ¿Qué la secuencia de comandos de Python es transformar los datos en un esquema coherente de salida. Funciona así:

1. La **@outputSchema** instrucción define el formato de los datos que se devolverá al cerdo. En este caso, es un **conjunto de datos**, que es un tipo de datos de cerdo. La bolsa contiene los campos siguientes, que son chararray (cadenas):

    * fecha: la fecha en que se creó la entrada del registro
    * tiempo - el tiempo que se creó la entrada del registro
    * nombre - el nombre de la clase la entrada se creó para
    * nivel - el nivel de registro
    * detalle - detallados detalles de la entrada del registro

2. A continuación, en la **definición create_structure(input)** se define la función que cerdo pasarán elementos de línea para.

3. Los datos de ejemplo, **sample.log**, principalmente se ajustan a la fecha, hora, nombre de clase, nivel y queremos obtener el esquema de detalle. Pero también contiene unas cuantas líneas que empiecen por la cadena '*java.lang.Exception*' que debe modificarse para que coincida con el esquema. La instrucción **if** comprueba los luego retoca los datos de entrada para mover la cadena '*java.lang.Exception*' hasta el final, poner la datos en línea con nuestro esquema de los resultados esperados.

4. A continuación, se usa el comando **dividir** para dividir los datos en los primeros caracteres de espacio en cuatro. El resultado es cinco valores asignados en **fecha**, **hora**, **nombre de clase**, **nivel**y **detalle**.

5. Por último, los valores se devuelven a cerdo.

Cuando los datos se devuelven a cerdo, tendrá un esquema coherente según se define en la **@outputSchema** instrucción.

##<a name="running"></a>Ejecutar los ejemplos

Si está utilizando un clúster de HDInsight basados en Linux, utilice los siguientes pasos **SSH** . Si está usando un clúster HDInsight basado en Windows y un cliente de Windows, siga los pasos de **PowerShell** .

###<a name="ssh"></a>SSH

Para obtener más información sobre el uso de SSH, vea <a href="../hdinsight-hadoop-linux-use-ssh-unix/" target="_blank">Usar SSH con basados en Linux Hadoop en HDInsight de Linux, Unix o OS X</a> o <a href="../hdinsight-hadoop-linux-use-ssh-windows/" target="_blank">Usar SSH con basados en Linux Hadoop en HDInsight de Windows</a>.

1. Con Python ejemplos [streaming.py](#streamingpy) y [pig_python.py](#jythonpy), crear copias locales de los archivos en su equipo de desarrollo.

2. Usar `scp` para copiar los archivos en el clúster HDInsight. Por ejemplo, el siguiente copie los archivos en un clúster denominado **miClúster**.

        scp streaming.py pig_python.py myuser@mycluster-ssh.azurehdinsight.net:

3. Utilizar SSH para conectar con el clúster. Por ejemplo, la siguiente sería conectarse a un clúster denominado **miClúster** como usuario **myuser**.

        ssh myuser@mycluster-ssh.azurehdinsight.net

4. Desde la sesión SSH, agregue los archivos de python cargados previamente en el almacenamiento WASB para el clúster.

        hdfs dfs -put streaming.py /streaming.py
        hdfs dfs -put pig_python.py /pig_python.py

Después de cargar los archivos, realice los pasos siguientes para ejecutar las tareas de la sección y cerdo.

####<a name="hive"></a>Sección

1. Usar el `hive` comando para iniciar la consola de sección. Debería ver una `hive>` Preguntar una vez que ha cargado el shell.

2. Escriba lo siguiente en la `hive>` símbolo del sistema.

        add file wasbs:///streaming.py;
        SELECT TRANSFORM (clientid, devicemake, devicemodel)
          USING 'python streaming.py' AS
          (clientid string, phoneLabel string, phoneHash string)
        FROM hivesampletable
        ORDER BY clientid LIMIT 50;

3. Después de escribir la última línea, debe iniciar el trabajo. Finalmente devuelve resultados similares a los siguientes.

        100041  RIM 9650    d476f3687700442549a83fac4560c51c
        100041  RIM 9650    d476f3687700442549a83fac4560c51c
        100042  Apple iPhone 4.2.x  375ad9a0ddc4351536804f1d5d0ea9b9
        100042  Apple iPhone 4.2.x  375ad9a0ddc4351536804f1d5d0ea9b9
        100042  Apple iPhone 4.2.x  375ad9a0ddc4351536804f1d5d0ea9b9

####<a name="pig"></a>Cerdo

1. Usar el `pig` comando para iniciar la consola. Debería ver una `grunt>` Preguntar una vez que ha cargado el shell.

2. Escriba las siguientes instrucciones en el `grunt>` símbolo del sistema ejecutar el script de Python utilizando el intérprete Jython.

        Register wasbs:///pig_python.py using jython as myfuncs;
        LOGS = LOAD 'wasbs:///example/data/sample.log' as (LINE:chararray);
        LOG = FILTER LOGS by LINE is not null;
        DETAILS = foreach LOG generate myfuncs.create_structure(LINE);
        DUMP DETAILS;

3. Después de escribir la línea siguiente, debe iniciar el trabajo. Finalmente devuelve resultados similares a los siguientes.

        ((2012-02-03,20:11:56,SampleClass5,[TRACE],verbose detail for id 990982084))
        ((2012-02-03,20:11:56,SampleClass7,[TRACE],verbose detail for id 1560323914))
        ((2012-02-03,20:11:56,SampleClass8,[DEBUG],detail for id 2083681507))
        ((2012-02-03,20:11:56,SampleClass3,[TRACE],verbose detail for id 1718828806))
        ((2012-02-03,20:11:56,SampleClass3,[INFO],everything normal for id 530537821))

4. Usar `quit` para salir del shell de pesado y, a continuación, utilice la siguiente para editar el archivo pig_python.py en el sistema de archivos local:

    nano pig_python.py

5. Una vez en el editor, elimine la siguiente línea eliminando la `#` carácter desde el principio de la línea:

        #from pig_util import outputSchema

    Una vez que el cambio se ha realizado, use Ctrl + X para salir del editor. Seleccione Y y, a continuación, escriba para guardar los cambios.

6. Usar el `pig` comando para iniciar la consola de nuevo. Una vez que se encuentre en la `grunt>` preguntar, use las siguientes acciones para ejecutar la secuencia de comandos de Python utilizando el intérprete C Python.

        Register 'pig_python.py' using streaming_python as myfuncs;
        LOGS = LOAD 'wasbs:///example/data/sample.log' as (LINE:chararray);
        LOG = FILTER LOGS by LINE is not null;
        DETAILS = foreach LOG generate myfuncs.create_structure(LINE);
        DUMP DETAILS;

    Una vez completado este trabajo, verá el mismo resultado como cuando ejecutó la secuencia de comandos con Jython previamente.

###<a name="powershell"></a>PowerShell

Estos pasos usar PowerShell de Azure. Si esto no está instalado y configurado en su equipo de desarrollo, vea [cómo instalar y configurar Azure PowerShell](../powershell-install-configure.md) antes de usar los siguientes pasos.

[AZURE.INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]

1. Con Python ejemplos [streaming.py](#streamingpy) y [pig_python.py](#jythonpy), crear copias locales de los archivos en su equipo de desarrollo.

2. Usar el siguiente script de PowerShell para cargar la **streaming.py** y **cerdo\_python.py** archivos en el servidor. Sustituya el nombre de su clúster de HDInsight de Azure y la ruta de acceso a la **streaming.py** y **cerdo\_python.py** archivos en las tres primeras líneas de la secuencia de comandos.

        $clusterName = YourHDIClusterName
        $pathToStreamingFile = "C:\path\to\streaming.py"
        $pathToJythonFile = "C:\path\to\pig_python.py"

        $clusterInfo = Get-AzureRmHDInsightCluster -ClusterName $clusterName
        $resourceGroup = $clusterInfo.ResourceGroup
        $storageAccountName=$clusterInfo.DefaultStorageAccount.split('.')[0]
        $container=$clusterInfo.DefaultStorageContainer
        $storageAccountKey=(Get-AzureRmStorageAccountKey `
            -Name $storageAccountName `
        -ResourceGroupName $resourceGroup)[0].Value

        #Create a storage content and upload the file
        $context = New-AzureStorageContext `
            -StorageAccountName $storageAccountName `
            -StorageAccountKey $storageAccountKey
        
        Set-AzureStorageBlobContent `
            -File $pathToStreamingFile `
            -Blob "streaming.py" `
            -Container $container `
            -Context $context
        
        Set-AzureStorageBlobContent `
            -File $pathToJythonFile `
            -Blob "pig_python.py" `
            -Container $container `
            -Context $context

    Esta secuencia de comandos recupera información para el clúster de HDInsight, a continuación, extrae la cuenta y la clave de la cuenta de almacenamiento predeterminada y cargará los archivos en la raíz del contenedor.

    > [AZURE.NOTE] Otros métodos para cargar las secuencias de comandos pueden encontrarse en el documento de [cargar los datos para trabajos de Hadoop en HDInsight](hdinsight-upload-data.md) .

Después de cargar los archivos, use los siguientes scripts de PowerShell para iniciar las tareas. Cuando finalice el trabajo, el resultado se debe escribir en la consola de PowerShell.

####<a name="hive"></a>Sección

La siguiente secuencia de comandos ejecutará la secuencia de comandos de __streaming.py__ . Antes de ejecutar, le pedirá la información de cuenta de administrador/HTTPs para el clúster de HDInsight.

    # Replace 'YourHDIClusterName' with the name of your cluster
    $clusterName = YourHDIClusterName
    $creds=Get-Credential
    #Get the cluster info so we can get the resource group, storage, etc.
    $clusterInfo = Get-AzureRmHDInsightCluster -ClusterName $clusterName
    $resourceGroup = $clusterInfo.ResourceGroup
    $storageAccountName=$clusterInfo.DefaultStorageAccount.split('.')[0]
    $container=$clusterInfo.DefaultStorageContainer
    $storageAccountKey=(Get-AzureRmStorageAccountKey `
        -Name $storageAccountName `
        -ResourceGroupName $resourceGroup)[0].Value
    #Create a storage content and upload the file
    $context = New-AzureStorageContext `
        -StorageAccountName $storageAccountName `
        -StorageAccountKey $storageAccountKey
    
    # If using a Windows-based HDInsight cluster, change the USING statement to:
    # "USING 'D:\Python27\python.exe streaming.py' AS " +
    $HiveQuery = "add file wasbs:///streaming.py;" +
                 "SELECT TRANSFORM (clientid, devicemake, devicemodel) " +
                   "USING 'python streaming.py' AS " +
                   "(clientid string, phoneLabel string, phoneHash string) " +
                 "FROM hivesampletable " +
                 "ORDER BY clientid LIMIT 50;"

    $jobDefinition = New-AzureRmHDInsightHiveJobDefinition `
        -Query $HiveQuery

    $job = Start-AzureRmHDInsightJob `
        -ClusterName $clusterName `
        -JobDefinition $jobDefinition `
        -HttpCredential $creds
    Write-Host "Wait for the Hive job to complete ..." -ForegroundColor Green
    Wait-AzureRmHDInsightJob `
        -JobId $job.JobId `
        -ClusterName $clusterName `
        -HttpCredential $creds
    # Uncomment the following to see stderr output
    # Get-AzureRmHDInsightJobOutput `
    #   -Clustername $clusterName `
    #   -JobId $job.JobId `
    #   -DefaultContainer $container `
    #   -DefaultStorageAccountName $storageAccountName `
    #   -DefaultStorageAccountKey $storageAccountKey `
    #   -HttpCredential $creds `
    #   -DisplayOutputType StandardError
    Write-Host "Display the standard output ..." -ForegroundColor Green
    Get-AzureRmHDInsightJobOutput `
        -Clustername $clusterName `
        -JobId $job.JobId `
        -DefaultContainer $container `
        -DefaultStorageAccountName $storageAccountName `
        -DefaultStorageAccountKey $storageAccountKey `
        -HttpCredential $creds

La salida para el trabajo **subárbol** debería ser similar al siguiente:

    100041  RIM 9650    d476f3687700442549a83fac4560c51c
    100041  RIM 9650    d476f3687700442549a83fac4560c51c
    100042  Apple iPhone 4.2.x  375ad9a0ddc4351536804f1d5d0ea9b9
    100042  Apple iPhone 4.2.x  375ad9a0ddc4351536804f1d5d0ea9b9
    100042  Apple iPhone 4.2.x  375ad9a0ddc4351536804f1d5d0ea9b9

####<a name="pig-jython"></a>Cerdo (Jython)

La siguiente usará el script __pig_python.py__ , utilizando el intérprete Jython. Antes de ejecutar, le pedirá que la información de administración/HTTPs para el clúster de HDInsight.

> [AZURE.NOTE] Al enviar remotamente un trabajo con PowerShell, no es posible usar C Python como intérprete.

    # Replace 'YourHDIClusterName' with the name of your cluster
    $clusterName = YourHDIClusterName

    $creds = Get-Credential
    #Get the cluster info so we can get the resource group, storage, etc.
    $clusterInfo = Get-AzureRmHDInsightCluster -ClusterName $clusterName
    $resourceGroup = $clusterInfo.ResourceGroup
    $storageAccountName=$clusterInfo.DefaultStorageAccount.split('.')[0]
    $container=$clusterInfo.DefaultStorageContainer
    $storageAccountKey=(Get-AzureRmStorageAccountKey `
        -Name $storageAccountName `
        -ResourceGroupName $resourceGroup)[0].Value
    
    #Create a storage content and upload the file
    $context = New-AzureStorageContext `
        -StorageAccountName $storageAccountName `
        -StorageAccountKey $storageAccountKey
            
    $PigQuery = "Register wasbs:///jython.py using jython as myfuncs;" +
                "LOGS = LOAD 'wasbs:///example/data/sample.log' as (LINE:chararray);" +
                "LOG = FILTER LOGS by LINE is not null;" +
                "DETAILS = foreach LOG generate myfuncs.create_structure(LINE);" +
                "DUMP DETAILS;"

    $jobDefinition = New-AzureRmHDInsightPigJobDefinition -Query $PigQuery

    $job = Start-AzureRmHDInsightJob `
        -ClusterName $clusterName `
        -JobDefinition $jobDefinition `
        -HttpCredential $creds
        
    Write-Host "Wait for the Pig job to complete ..." -ForegroundColor Green
    Wait-AzureRmHDInsightJob `
        -Job $job.JobId `
        -ClusterName $clusterName `
        -HttpCredential $creds
    # Uncomment the following to see stderr output
    # Get-AzureRmHDInsightJobOutput `
        -Clustername $clusterName `
        -JobId $job.JobId `
        -DefaultContainer $container `
        -DefaultStorageAccountName $storageAccountName `
        -DefaultStorageAccountKey $storageAccountKey `
        -HttpCredential $creds `
        -DisplayOutputType StandardError
    Write-Host "Display the standard output ..." -ForegroundColor Green
    Get-AzureRmHDInsightJobOutput `
        -Clustername $clusterName `
        -JobId $job.JobId `
        -DefaultContainer $container `
        -DefaultStorageAccountName $storageAccountName `
        -DefaultStorageAccountKey $storageAccountKey `
        -HttpCredential $creds

El resultado para el trabajo de **cerdo** debería ser similar al siguiente:

    ((2012-02-03,20:11:56,SampleClass5,[TRACE],verbose detail for id 990982084))
    ((2012-02-03,20:11:56,SampleClass7,[TRACE],verbose detail for id 1560323914))
    ((2012-02-03,20:11:56,SampleClass8,[DEBUG],detail for id 2083681507))
    ((2012-02-03,20:11:56,SampleClass3,[TRACE],verbose detail for id 1718828806))
    ((2012-02-03,20:11:56,SampleClass3,[INFO],everything normal for id 530537821))

##<a name="troubleshooting"></a>Solución de problemas

###<a name="errors-when-running-jobs"></a>Errores al ejecutar trabajos

Cuando se ejecuta el trabajo de la sección, puede aparecer un error similar al siguiente:

    Caused by: org.apache.hadoop.hive.ql.metadata.HiveException: [Error 20001]: An error occurred while reading or writing to your custom script. It may have crashed with an error.
    
Este problema puede deberse a fin de línea en el archivo streaming.py. Muchos Windows editores de manera predeterminada al CRLF como el final de línea, pero aplicaciones Linux normalmente espere LF.

Si está utilizando un editor que no se puede crear el fin de línea LF o no está seguro de qué fin de línea se utilizan, use las siguientes instrucciones de PowerShell para quitar los caracteres de CR antes de cargar el archivo a HDInsight:

    $original_file ='c:\path\to\streaming.py'
    $text = [IO.File]::ReadAllText($original_file) -replace "`r`n", "`n"
    [IO.File]::WriteAllText($original_file, $text)

###<a name="powershell-scripts"></a>Secuencias de comandos de PowerShell

Las secuencias de comandos de PowerShell en el ejemplo se usa para ejecutar los ejemplos contienen una línea de comentario que se muestra los resultados de error para el trabajo. Si no ve los resultados esperados para el trabajo, elimine la línea siguiente y vea si la información de error indica un problema.

    # Get-AzureRmHDInsightJobOutput `
            -Clustername $clusterName `
            -JobId $job.JobId `
            -DefaultContainer $container `
            -DefaultStorageAccountName $storageAccountName `
            -DefaultStorageAccountKey $storageAccountKey `
            -HttpCredential $creds `
            -DisplayOutputType StandardError

La información de error (STDERR) y a continuación, el resultado de la tarea (STDOUT), también se registran en el contenedor de blob predeterminado para los clústeres en las siguientes ubicaciones.

Para este trabajo...|Mire estos archivos en el contenedor de blob
---|---
Sección|/ HivePython/stderr<p>/ HivePython/stdout
Cerdo|/ PigPython/stderr<p>/ PigPython/stdout

##<a name="next"></a>Pasos siguientes

Si necesita cargar módulos Python que no se proporcionan de forma predeterminada, vea [cómo implementar un módulo a HDInsight de Azure](http://blogs.msdn.com/b/benjguin/archive/2014/03/03/how-to-deploy-a-python-module-to-windows-azure-hdinsight.aspx) para obtener un ejemplo de cómo hacerlo.

Para que conocer otras maneras de utilizar cerdo, subárbol y para obtener información sobre cómo usar MapReduce, vea la siguiente.

* [Usar subárbol con HDInsight](hdinsight-use-hive.md)

* [Usar cerdo con HDInsight](hdinsight-use-pig.md)

* [Usar MapReduce con HDInsight](hdinsight-use-mapreduce.md)
