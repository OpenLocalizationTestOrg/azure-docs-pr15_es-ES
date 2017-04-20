<properties
   pageTitle="Utilizar Hadoop cerdo con SSH en un clúster de HDInsight | Microsoft Azure"
   description="Aprenda cómo conectarse a un clúster Hadoop basados en Linux con SSH y, a continuación, utilice el comando de cerdo para ejecutar de forma interactiva instrucciones de cerdo latino, o como un trabajo por lotes."
   services="hdinsight"
   documentationCenter=""
   authors="Blackmist"
   manager="jhubbard"
   editor="cgronlun"
    tags="azure-portal"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="10/11/2016"
   ms.author="larryfr"/>

#<a name="run-pig-jobs-on-a-linux-based-cluster-with-the-pig-command-ssh"></a>Ejecutar trabajos de cerdo en un clúster basado en Linux con el comando de cerdo (SSH)

[AZURE.INCLUDE [pig-selector](../../includes/hdinsight-selector-use-pig.md)]

En este documento le guiará a través del proceso de conexión con un clúster basado en Linux, HDInsight de Azure mediante Secure Shell (SSH), utilizando el comando de cerdo para ejecutar instrucciones de cerdo latina interactivamente, o como un trabajo por lotes.

El lenguaje de programación de cerdo latina permite describir las transformaciones que se aplican a los datos de entrada para producir los resultados deseados.

> [AZURE.NOTE] Si ya están familiarizados con el uso de servidores basados en Linux Hadoop, pero son nuevos para HDInsight, vea [Sugerencias de HDInsight basados en Linux](hdinsight-hadoop-linux-information.md).

##<a id="prereq"></a>Requisitos previos

Para completar los pasos de este artículo, necesita el siguiente.

* Un clúster basado en Linux, HDInsight (Hadoop en HDInsight).

* Un cliente SSH. Linux, Unix y Mac OS deben ofrecer un cliente SSH. Usuarios de Windows deben descargar a un cliente, como [masilla](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).

##<a id="ssh"></a>Conectar con SSH

Conectar el nombre de dominio completo (FQDN) del clúster HDInsight mediante el comando SSH. El FQDN es el nombre que dio el clúster, a continuación, **. azurehdinsight.net**. Por ejemplo, lo siguiente sería conectarse a un clúster denominado **myhdinsight**.

    ssh admin@myhdinsight-ssh.azurehdinsight.net

**Si proporciona una clave de certificado para la autenticación de SSH** al crear el clúster de HDInsight, debe especificar la ubicación de la clave privada en el sistema cliente.

    ssh admin@myhdinsight-ssh.azurehdinsight.net -i ~/mykey.key

**Si proporciona una contraseña para la autenticación de SSH** al crear el clúster de HDInsight, debe proporcionar la contraseña cuando se le pida.

Para obtener más información sobre cómo utilizar SSH con HDInsight, vea [Utilizar SSH con Hadoop basados en Linux en HDInsight de Linux, OS X y Unix](hdinsight-hadoop-linux-use-ssh-unix.md).

###<a name="putty-windows-based-clients"></a>Masilla (clientes basados en Windows)

Windows no proporciona a un cliente SSH integrado. Se recomienda usar **masilla**, que puede descargarse desde [http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).

Para obtener más información acerca de cómo utilizar masilla, vea [Utilizar SSH con Hadoop basados en Linux en HDInsight de Windows ](hdinsight-hadoop-linux-use-ssh-windows.md).

##<a id="pig"></a>Utilice el comando de cerdo

2. Una vez conectado, inicie el cerdo Command-line interface (CLI) mediante el siguiente comando.

        pig

    Tras unos segundos, debe aparecer un `grunt>` símbolo del sistema.

3. Escriba la siguiente instrucción.

        LOGS = LOAD 'wasbs:///example/data/sample.log';

    Este comando carga el contenido del archivo sample.log en registros. Puede ver el contenido del archivo mediante el siguiente.

        DUMP LOGS;

4. A continuación, transformar los datos mediante la aplicación de una expresión regular para extraer sólo el nivel de registro de cada registro mediante el siguiente.

        LEVELS = foreach LOGS generate REGEX_EXTRACT($0, '(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)', 1)  as LOGLEVEL;

    Puede utilizar la **descarga** para ver los datos después de la transformación. En este caso, utilice `DUMP LEVELS;`.

5. Seguir aplicando transformaciones mediante las instrucciones siguientes. Utilice `DUMP` para ver el resultado de la transformación después de cada paso.

    <table>
    <tr>
    <th>Instrucción</th><th>Lo que hace</th>
    </tr>
    <tr>
    <td>FILTEREDLEVELS = niveles de filtrado en el nivel de registro no es null;</td><td>Quita las filas que contienen un valor nulo para el nivel de registro y almacena los resultados en FILTEREDLEVELS.</td>
    </tr>
    <tr>
    <td>GROUPEDLEVELS = grupo FILTEREDLEVELS por el nivel de registro;</td><td>Agrupa las filas por el nivel de registro y almacena los resultados en GROUPEDLEVELS.</td>
    </tr>
    <tr>
    <td>FRECUENCIAS = foreach GROUPEDLEVELS generar grupo como LOGLEVEL, COUNT (FILTEREDLEVELS. LOGLEVEL) como la cuenta;</td><td>Crea un nuevo conjunto de datos que contiene cada registro único nivel valor y cuántas veces se produce. Esto se almacena en las FRECUENCIAS.</td>
    </tr>
    <tr>
    <td>RESULTADO = orden FRECUENCIAS por recuento desc;</td><td>Ordena los niveles de registro por su número (descendente) y almacena en el resultado.</td>
    </tr>
    </table>

6. También puede guardar los resultados de una transformación mediante el `STORE` instrucción. Por ejemplo, el siguiente guarda el `RESULT` en el directorio **/example/data/pigout** en el contenedor de almacenamiento predeterminado para el clúster.

        STORE RESULT into 'wasbs:///example/data/pigout';

    > [AZURE.NOTE] Los datos se almacenan en el directorio especificado en archivos denominados **parte nnnnn**. Si el directorio ya existe, recibirá un error.

7. Para salir del símbolo del sistema de investigadores, escriba la siguiente instrucción.

        QUIT;

###<a name="pig-latin-batch-files"></a>Archivos por lotes de cerdo latino

También puede utilizar el comando de cerdo ejecutar latino cerdo contenida en un archivo.

3. Después de salir del símbolo del sistema de investigadores, utilice el siguiente comando para canalización STDIN en un archivo denominado **pigbatch.pig**. Este archivo se creará en el directorio principal de la cuenta que ha iniciado el período de sesiones de SSH.

        cat > ~/pigbatch.pig

4. Escriba o pegue las líneas siguientes y, a continuación, utilice Ctrl + D cuando haya terminado.

        LOGS = LOAD 'wasbs:///example/data/sample.log';
        LEVELS = foreach LOGS generate REGEX_EXTRACT($0, '(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)', 1)  as LOGLEVEL;
        FILTEREDLEVELS = FILTER LEVELS by LOGLEVEL is not null;
        GROUPEDLEVELS = GROUP FILTEREDLEVELS by LOGLEVEL;
        FREQUENCIES = foreach GROUPEDLEVELS generate group as LOGLEVEL, COUNT(FILTEREDLEVELS.LOGLEVEL) as COUNT;
        RESULT = order FREQUENCIES by COUNT desc;
        DUMP RESULT;

5. Utilice lo siguiente para ejecutar el archivo **pigbatch.pig** mediante el comando de cerdo.

        pig ~/pigbatch.pig

    Una vez que finalice el proceso, verá el siguiente resultado, que debe ser el mismo que cuando utilizó `DUMP RESULT;` en los pasos anteriores.

        (TRACE,816)
        (DEBUG,434)
        (INFO,96)
        (WARN,11)
        (ERROR,6)
        (FATAL,2)

##<a id="summary"></a>Resumen

Como puede ver, el comando de cerdo permite interactivamente ejecutar operaciones MapReduce mediante cerdo latino, así como ejecutar instrucciones almacenadas en un archivo por lotes.

##<a id="nextsteps"></a>Próximos pasos

Para obtener información general sobre cerdo en HDInsight.

* [Utilice cerdo con Hadoop en HDInsight](hdinsight-use-pig.md)

Puede trabajar con Hadoop en HDInsight para obtener información acerca de otras formas.

* [Utilice la sección con Hadoop en HDInsight](hdinsight-use-hive.md)

* [Utilice MapReduce con Hadoop en HDInsight](hdinsight-use-mapreduce.md)
