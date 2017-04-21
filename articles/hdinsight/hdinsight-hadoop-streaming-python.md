<properties
   pageTitle="Desarrollar Python MapReduce trabajos con HDInsight | Microsoft Azure"
   description="Obtenga información sobre cómo crear y ejecutar trabajos de Python MapReduce en clústeres HDInsight basados en Linux."
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

#<a name="develop-python-streaming-programs-for-hdinsight"></a>Desarrollar Python transmisión programas para HDInsight

Hadoop ofrece una API de transmisión MapReduce que le permite escribir mapa y reducir funciones en idiomas que no sean Java. En este artículo, aprenderá cómo usar Python para realizar operaciones de MapReduce.

> [AZURE.NOTE] Mientras el código Python en este documento puede utilizarse con un clúster de HDInsight basado en Windows, los pasos de este documento son específicos para clústeres basados en Linux.

En este artículo se basa en información y ejemplos publicados por Michael Noll al [escribir un programa de MapReduce Hadoop en Python](http://www.michael-noll.com/tutorials/writing-an-hadoop-mapreduce-program-in-python/).

##<a name="prerequisites"></a>Requisitos previos

Para completar los pasos de este artículo, necesita lo siguiente:

* Un Hadoop basados en Linux en clúster HDInsight

* Un editor de texto

    > [AZURE.IMPORTANT] El editor de texto debe utilizar LF como el final de la línea. Si utiliza CRLF, esto provocará errores cuando se utiliza el trabajo MapReduce clústeres HDInsight basados en Linux. Si no está seguro, use el paso opcional en la sección [MapReduce ejecutar](#run-mapreduce) para convertir cualquier CRLF en LF.

* Para clientes de Windows, PuTTY y PSCP. Estas utilidades están disponibles en la <a href="http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html" target="_blank">Página de descarga de PuTTY</a>.

##<a name="word-count"></a>Recuento de palabras

En este ejemplo, implementará un recuento de palabras básico mediante un asignador y reductor. El asignador separa oraciones en palabras individuales, y el reductor agrega las palabras y cuenta generar el resultado.

El siguiente diagrama muestra lo que sucede durante el mapa y reducir las fases.

![ilustración de mapa de reducir](./media/hdinsight-hadoop-streaming-python/HDI.WordCountDiagram.png)

##<a name="why-python"></a>¿Por qué Python?

Python es un lenguaje de programación general de alto nivel que permite a los conceptos de express en menos líneas de código de muchos otros idiomas. Recientemente ha estuvo populares con científicos de datos como idioma para la creación de prototipos porque su naturaleza interpretar, escribir dinámico y elegante sintaxis sea adecuado para el desarrollo rápido de aplicaciones.

Python está instalado en todos los clústeres HDInsight.

##<a name="streaming-mapreduce"></a>MapReduce Streaming

Hadoop le permite especificar un archivo que contiene el mapa y reducir la lógica que se usa en una tarea. Los requisitos específicos del mapa y reducir lógica son:

* **Entrada**: el mapa y reducir componentes deben leerá los datos de entrada desde origen.

* **Resultado**: el mapa y reducir componentes deben escribir los datos de salida a STDOUT.

* **Formato de datos**: los datos consumida y fabrican deben ser un par de clave y valor, separado por un carácter de tabulación.

Python puede manejar fácilmente estos requisitos mediante el módulo de **sistema** a leerá desde origen y **de impresión** para imprimir en STDOUT. La tarea restante simplemente está dando formato a los datos con una pestaña (`\t`) carácter entre la clave y el valor.

##<a name="create-the-mapper-and-reducer"></a>Crear el asignador y reductor

El asignador y reductor son archivos de texto, en este caso **mapper.py** y **reducer.py** (para dejar claro que recursos humanos). Puede crear estos mediante el editor de su elección.

###<a name="mapperpy"></a>Mapper.py

Crear un nuevo archivo denominado **mapper.py** y usar el siguiente código como el contenido:

    #!/usr/bin/env python

    # Use the sys module
    import sys

    # 'file' in this case is STDIN
    def read_input(file):
        # Split each line into words
        for line in file:
            yield line.split()

    def main(separator='\t'):
        # Read the data using read_input
        data = read_input(sys.stdin)
        # Process each words returned from read_input
        for words in data:
            # Process each word
            for word in words:
                # Write to STDOUT
                print '%s%s%d' % (word, separator, 1)

    if __name__ == "__main__":
        main()

Dedique un momento a leer el código de manera que pueda comprender qué hace.

###<a name="reducerpy"></a>REDUCER.py

Crear un nuevo archivo denominado **reducer.py** y usar el siguiente código como el contenido:

    #!/usr/bin/env python

    # import modules
    from itertools import groupby
    from operator import itemgetter
    import sys

    # 'file' in this case is STDIN
    def read_mapper_output(file, separator='\t'):
        # Go through each line
        for line in file:
            # Strip out the separator character
            yield line.rstrip().split(separator, 1)

    def main(separator='\t'):
        # Read the data using read_mapper_output
        data = read_mapper_output(sys.stdin, separator=separator)
        # Group words and counts into 'group'
        #   Since MapReduce is a distributed process, each word
        #   may have multiple counts. 'group' will have all counts
        #   which can be retrieved using the word as the key.
        for current_word, group in groupby(data, itemgetter(0)):
            try:
                # For each word, pull the count(s) for the word
                #   from 'group' and create a total count
                total_count = sum(int(count) for current_word, count in group)
                # Write to stdout
                print "%s%s%d" % (current_word, separator, total_count)
            except ValueError:
                # Count was not a number, so do nothing
                pass

    if __name__ == "__main__":
        main()

##<a name="upload-the-files"></a>Cargue los archivos

**Mapper.py** y **reducer.py** deben estar en el nodo principal del clúster antes de poder ejecutar ellos. La manera más sencilla de cargarlos es usar **scp** (**pscp** si está usando a un cliente de Windows).

Desde el cliente, en el mismo directorio que **mapper.py** y **reducer.py**, utilice el siguiente comando. Reemplace el **nombre de usuario** con un usuario SSH y **nombreDeClúster** con el nombre del clúster.

    scp mapper.py reducer.py username@clustername-ssh.azurehdinsight.net:

Esto copiará los archivos del sistema local en el nodo principal.

> [AZURE.NOTE] Si utiliza una contraseña para proteger su cuenta SSH, se le pedirá la contraseña. Si utiliza una clave SSH, tendrá que usar la `-i` parámetro y la ruta de acceso a la clave privada, por ejemplo, `scp -i /path/to/private/key mapper.py reducer.py username@clustername-ssh.azurehdinsight.net:`.

##<a name="run-mapreduce"></a>Ejecutar MapReduce

1. Conectarse al clúster mediante SSH:

        ssh username@clustername-ssh.azurehdinsight.net

    > [AZURE.NOTE] Si utiliza una contraseña para proteger su cuenta SSH, se le pedirá la contraseña. Si utiliza una clave SSH, tendrá que usar la `-i` parámetro y la ruta de acceso a la clave privada, por ejemplo, `ssh -i /path/to/private/key username@clustername-ssh.azurehdinsight.net`.

2. (Opcional) Si utiliza un editor de texto que usa CRLF como la línea final al crear los archivos mapper.py y reducer.py, o no sabe lo que el editor de fin de línea utiliza, utilice los siguientes comandos para convertir las apariciones de CRLF en mapper.py y reducer.py a LF.

        perl -pi -e 's/\r\n/\n/g' mappery.py
        perl -pi -e 's/\r\n/\n/g' reducer.py

2. Use el comando siguiente para iniciar el trabajo MapReduce.

        yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-streaming.jar -files mapper.py,reducer.py -mapper mapper.py -reducer reducer.py -input wasbs:///example/data/gutenberg/davinci.txt -output wasbs:///example/wordcountout

    Este comando tiene los siguientes elementos:

    * **hadoop streaming.jar**: utilizar al realizar operaciones de MapReduce streaming. Interfaces de Hadoop con el código de MapReduce externo que proporcione.

    * **-archivos**: Hadoop indica que los archivos especificados son necesarias para este trabajo MapReduce y, a continuación, se deberían copiar a todos los nodos de trabajo.

    * **-asignador**: indica qué archivo para utilizarlo como el asignador de Hadoop.

    * **-reductor**: indica qué archivo para utilizarlo como el reductor de Hadoop.

    * **-entrada**: el archivo de entrada que nos debemos contar palabras desde.

    * **-salida**: el directorio que se escriben en el resultado.

        > [AZURE.NOTE] Este directorio se creará el trabajo.

Debe ver un conjunto de instrucciones de la **información** como el trabajo se inicia y por último, vea la operación de **mapa** y **reducir** que se muestran como porcentajes.

    15/02/05 19:01:04 INFO mapreduce.Job:  map 0% reduce 0%
    15/02/05 19:01:16 INFO mapreduce.Job:  map 100% reduce 0%
    15/02/05 19:01:27 INFO mapreduce.Job:  map 100% reduce 100%

Cuando se complete, recibirá información de estado sobre el trabajo.

##<a name="view-the-output"></a>Ver el resultado

Una vez completada la tarea, utilice el comando siguiente para ver el resultado:

    hdfs dfs -text /example/wordcountout/part-00000

Esto debe mostrar una lista de palabras y cuántas veces la palabra se ha producido. A continuación se muestra un ejemplo de los datos de salida:

    wrenching       1
    wretched        6
    wriggling       1
    wrinkled,       1
    wrinkles        2
    wrinkling       2

##<a name="next-steps"></a>Pasos siguientes

Ahora que ha aprendido a usar streaming trabajos MapRedcue con HDInsight, use los siguientes vínculos para explorar otras formas de trabajar con HDInsight de Azure.

* [Usar subárbol con HDInsight](hdinsight-use-hive.md)
* [Usar cerdo con HDInsight](hdinsight-use-pig.md)
* [Usar MapReduce trabajos con HDInsight](hdinsight-use-mapreduce.md)
