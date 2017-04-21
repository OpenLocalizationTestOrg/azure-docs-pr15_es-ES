<properties
   pageTitle="Usar Hadoop cerdo con Escritorio remoto en HDInsight | Microsoft Azure"
   description="Obtenga información sobre cómo usar el comando cerdo para ejecutar instrucciones latino cerdo de una conexión a Escritorio remoto a un clúster basado en Windows Hadoop en HDInsight."
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

#<a name="run-pig-jobs-from-a-remote-desktop-connection"></a>Ejecutar trabajos de cerdo desde una conexión a Escritorio remoto

[AZURE.INCLUDE [pig-selector](../../includes/hdinsight-selector-use-pig.md)]

Este documento proporciona un tutorial para que usar el comando cerdo ejecutar instrucciones cerdo latino desde una conexión a Escritorio remoto a un clúster de HDInsight basado en Windows. Cerdo latino le permite crear aplicaciones MapReduce mediante la descripción de las transformaciones de datos, en lugar de asignar y reducir las funciones.

En este documento, obtenga información sobre cómo

##<a id="prereq"></a>Requisitos previos

Para completar los pasos de este artículo, necesita lo siguiente.

* Un clúster basado en Windows HDInsight (Hadoop en HDInsight)

* Un equipo cliente con Windows 10, Windows 8 o Windows 7

##<a id="connect"></a>Conectar con Escritorio remoto

Habilitar Escritorio remoto para el clúster HDInsight y luego conectarse siguiendo las instrucciones en [conectarse a clústeres HDInsight con RDP](hdinsight-administer-use-management-portal.md#rdp).

##<a id="pig"></a>Use el comando cerdo

2. Cuando haya una conexión a Escritorio remoto, iniciar la **línea de comandos de Hadoop** mediante el icono en el escritorio.

2. Para iniciar el comando cerdo, utilice la siguiente:

        %pig_home%\bin\pig

    Se presentará con una `grunt>` símbolo del sistema.

3. Escriba la siguiente instrucción:

        LOGS = LOAD 'wasbs:///example/data/sample.log';

    Este comando carga el contenido del archivo sample.log en el archivo de registros. Puede ver el contenido del archivo mediante el comando siguiente:

        DUMP LOGS;

4. Transformar los datos aplicando una expresión regular para extraer el nivel de registro de cada registro:

        LEVELS = foreach LOGS generate REGEX_EXTRACT($0, '(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)', 1)  as LOGLEVEL;

    Puede usar la **descarga** para ver los datos después de la transformación. En este caso, `DUMP LEVELS;`.

5. Siga aplicando transformaciones mediante el uso de las siguientes afirmaciones. Usar `DUMP` para ver el resultado de la transformación después de cada paso.

    <table>
    <tr>
    <th>Instrucción</th><th>Qué hace</th>
    </tr>
    <tr>
    <td>FILTEREDLEVELS = niveles de filtro por LOGLEVEL no es null;</td><td>Quita las filas que contienen un valor nulo para el nivel de registro y almacena los resultados en FILTEREDLEVELS.</td>
    </tr>
    <tr>
    <td>GROUPEDLEVELS = FILTEREDLEVELS grupo por LOGLEVEL;</td><td>Agrupa las filas por nivel de registro y almacena los resultados en GROUPEDLEVELS.</td>
    </tr>
    <tr>
    <td>FRECUENCIAS = foreach GROUPEDLEVELS generar grupo como LOGLEVEL, contar (FILTEREDLEVELS. LOGLEVEL) como contar;</td><td>Crea un nuevo conjunto de datos que contiene cada registro único valor de nivel y cuántas veces se produce. Esto se almacena en FRECUENCIAS</td>
    </tr>
    <tr>
    <td>RESULTADO = orden FRECUENCIAS por recuento desc;</td><td>Los niveles de registro de pedidos por número (descendente) y almacena en resultado</td>
    </tr>
    </table>

6. También puede guardar los resultados de una transformación mediante la `STORE` instrucción. Por ejemplo, el siguiente comando guarda la `RESULT` en el directorio **/example/data/pigout** en el contenedor de almacenamiento predeterminado para el clúster:

        STORE RESULT into 'wasbs:///example/data/pigout'

    > [AZURE.NOTE] Los datos se almacenan en el directorio especificado en archivos con el nombre de **elemento nnnnn**. Si ya existe el directorio, recibirá un mensaje de error.

7. Para salir de la pregunta pesado, escriba la siguiente instrucción.

        QUIT;

###<a name="pig-latin-batch-files"></a>Archivos por lotes de cerdo latino

También puede usar el comando cerdo para ejecutar latino cerdo contenida en un archivo.

3. Después de haber salido la pregunta pesado, abra **el Bloc de notas** y crear un nuevo archivo denominado **pigbatch.pig** en el directorio **% PIG_HOME %** .

4. Escriba o pegue las siguientes líneas en el archivo **pigbatch.pig** y, a continuación, guárdelo:

        LOGS = LOAD 'wasbs:///example/data/sample.log';
        LEVELS = foreach LOGS generate REGEX_EXTRACT($0, '(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)', 1)  as LOGLEVEL;
        FILTEREDLEVELS = FILTER LEVELS by LOGLEVEL is not null;
        GROUPEDLEVELS = GROUP FILTEREDLEVELS by LOGLEVEL;
        FREQUENCIES = foreach GROUPEDLEVELS generate group as LOGLEVEL, COUNT(FILTEREDLEVELS.LOGLEVEL) as COUNT;
        RESULT = order FREQUENCIES by COUNT desc;
        DUMP RESULT;

5. Use las siguientes acciones para ejecutar el archivo **pigbatch.pig** utilizando el comando cerdo.

        pig %PIG_HOME%\pigbatch.pig

    Cuando se complete el proceso, verá el siguiente resultado, que debe ser igual que cuando utiliza `DUMP RESULT;` en los pasos anteriores:

        (TRACE,816)
        (DEBUG,434)
        (INFO,96)
        (WARN,11)
        (ERROR,6)
        (FATAL,2)

##<a id="summary"></a>Resumen

Como puede ver, el comando cerdo permite interactiva ejecutar MapReduce operaciones o trabajos de cerdo latino que se almacenan en un archivo por lotes.

##<a id="nextsteps"></a>Pasos siguientes

Para obtener información general sobre cerdo en HDInsight:

* [Usar cerdo con Hadoop en HDInsight](hdinsight-use-pig.md)

Para obtener información sobre otras formas de puede trabajar con Hadoop en HDInsight:

* [Usar subárbol con Hadoop en HDInsight](hdinsight-use-hive.md)

* [Usar MapReduce con Hadoop en HDInsight](hdinsight-use-mapreduce.md)
