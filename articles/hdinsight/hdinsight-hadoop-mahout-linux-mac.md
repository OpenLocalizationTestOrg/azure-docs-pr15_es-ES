<properties
    pageTitle="Generar recomendaciones mediante Mahout y HDInsight de Linux | Microsoft Azure"
    description="Obtenga información sobre cómo usar el equipo Apache Mahout biblioteca de aprendizaje para generar recomendaciones de película con HDInsight basados en Linux (Hadoop)."
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
    ms.date="08/30/2016"
    ms.author="larryfr"/>

#<a name="generate-movie-recommendations-by-using-apache-mahout-with-linux-based-hadoop-in-hdinsight"></a>Generar recomendaciones de película mediante Apache Mahout con Hadoop basados en Linux en HDInsight

[AZURE.INCLUDE [mahout-selector](../../includes/hdinsight-selector-mahout.md)]

Obtenga información sobre cómo usar el equipo [Apache Mahout](http://mahout.apache.org) biblioteca con HDInsight de Azure de aprendizaje para generar recomendaciones de película.

Mahout es una [máquina de aprendizaje] [ ml] biblioteca para Apache Hadoop. Mahout contiene algoritmos para el procesamiento de datos, como el filtrado, clasificación y clústeres. En este artículo, usará un motor de recomendación para generar recomendaciones de película que se basan en películas que han visto sus amigos.

> [AZURE.NOTE] Los pasos de este documento requieren un Hadoop basados en Linux en clúster HDInsight. Para obtener información sobre cómo usar Mahout con un clúster basado en Windows, vea [recomendaciones de película generar usando Mahout Apache con basado en Windows Hadoop en HDInsight](hdinsight-mahout.md)

##<a name="prerequisites"></a>Requisitos previos

* Hadoop basados en Linux en clúster HDInsight. Para obtener información sobre cómo crear uno, vea [Introducción al uso basados en Linux Hadoop en HDInsight][getstarted]

##<a name="mahout-versioning"></a>Control de versiones Mahout

Para obtener más información sobre la versión de Mahout incluido con el clúster de HDInsight, consulte [HDInsight versiones y componentes de Hadoop](hdinsight-component-versioning.md).

> [AZURE.WARNING] Si es posible cargar una versión diferente de Mahout en el clúster HDInsight, solo los componentes suministrados con el clúster HDInsight son totalmente compatibles y Microsoft Support le ayudará a aislar y resolver los problemas relacionados con estos componentes.
>
> Componentes personalizados reciban soporte comercialmente razonable para ayudar a solucionar el problema. Esto puede ocasionar resolver el problema o preguntar si desea integrarse canales disponibles para las tecnologías de código abierto donde se encuentra la amplia experiencia para que la tecnología. Por ejemplo, hay muchos sitios de comunidad que se pueden usar como: [foro de MSDN para HDInsight](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=hdinsight), [http://stackoverflow.com](http://stackoverflow.com). También proyectos Apache tienen sitios de proyecto en [http://apache.org](http://apache.org), por ejemplo: [Hadoop](http://hadoop.apache.org/), [motor](http://spark.apache.org/).

##<a name="recommendations"></a>Recomendaciones de descripción

Una de las funciones que proporciona Mahout es un motor de recomendación. Este motor acepta datos en el formato de `userID`, `itemId`, y `prefValue` (la preferencia de los usuarios del elemento). Mahout, a continuación, puede realizar un análisis de la instancia para determinar: _los usuarios que tienen una preferencia para un elemento tienen también una preferencia para estos otros elementos_. Mahout determina los usuarios con las preferencias de elemento de tipo, que pueden usarse para hacer recomendaciones.

A continuación se muestra un ejemplo muy sencillo que utiliza películas:

* __La aparición__: Juan, Ana y Bob le ha gustado _guerra de estrella_, _La imperio fijado anterior_y _devolver de la Jedi_. Mahout determina que los usuarios que le guste cualquiera de estas películas también como las otras dos.

* __La aparición__: Juan y Ana también le gustó _La amenaza de fantasma_, _ataque de las copias_y _Revenge of la Sith_. Mahout determina que los usuarios que le gustó la anterior tres películas también como estos tres.

* __Recomendación semejanza__: Joe porque le ha gustado las tres primeras películas, Mahout busca en películas que otras personas con preferencias similar le gustó, pero Juan no ha observan (le gustó o calificó). En este caso, Mahout recomienda _La amenaza de fantasma_, _ataque de las copias_y _Revenge of la Sith_.

###<a name="understanding-the-data"></a>Descripción de los datos

Cómodamente, [GroupLens Research] [ movielens] proporciona los datos de clasificación de películas en un formato que es compatible con Mahout. Estos datos están disponibles en el almacenamiento de predeterminado del clúster en `/HdiSamples/HdiSamples/MahoutMovieData`.

Hay dos archivos `moviedb.txt` (información sobre las películas) y `user-ratings.txt`. El archivo ratings.txt de usuario se utiliza durante el análisis, mientras moviedb.txt se utiliza para proporcionar información de texto descriptivo cuando se muestran los resultados del análisis.

Los datos contenidos en ratings.txt de usuario tienen una estructura de `userID`, `movieID`, `userRating`, y `timestamp`, que nos indica cómo altamente cada usuario clasificación una película. Aquí tiene un ejemplo de los datos:


    196 242 3   881250949
    186 302 3   891717742
    22  377 1   878887116
    244 51  2   880606923
    166 346 1   886397596

##<a name="run-the-analysis"></a>Iniciar el análisis

Use el comando siguiente para ejecutar el trabajo de recomendación:

    mahout recommenditembased -s SIMILARITY_COOCCURRENCE -i /HdiSamples/HdiSamples/MahoutMovieData/user-ratings.txt -o /example/data/mahoutout --tempDir /temp/mahouttemp

> [AZURE.NOTE] El trabajo puede tardar varios minutos en completarse y puede ejecutar varias tareas MapReduce.

##<a name="view-the-output"></a>Ver el resultado

1. Una vez completada la tarea, utilice el comando siguiente para ver el resultado generado:

        hdfs dfs -text /example/data/mahoutout/part-r-00000

    El resultado se mostrará como sigue:

        1   [234:5.0,347:5.0,237:5.0,47:5.0,282:5.0,275:5.0,88:5.0,515:5.0,514:5.0,121:5.0]
        2   [282:5.0,210:5.0,237:5.0,234:5.0,347:5.0,121:5.0,258:5.0,515:5.0,462:5.0,79:5.0]
        3   [284:5.0,285:4.828125,508:4.7543354,845:4.75,319:4.705128,124:4.7045455,150:4.6938777,311:4.6769233,248:4.65625,272:4.649266]
        4   [690:5.0,12:5.0,234:5.0,275:5.0,121:5.0,255:5.0,237:5.0,895:5.0,282:5.0,117:5.0]

    La primera columna es el `userID`. Los valores contenidos en ' [' y ']' son `movieId`:`recommendationScore`.

2. Puede usar el resultado, junto con la moviedb.txt, para mostrar más información de usuario descriptivo. En primer lugar, debemos copiar los archivos localmente mediante los siguientes comandos:

        hdfs dfs -get /example/data/mahoutout/part-r-00000 recommendations.txt
        hdfs dfs -get /HdiSamples/HdiSamples/MahoutMovieData/* .

    Esto copiará los datos de salida a un archivo llamado **recommendations.txt** en el directorio actual, junto con los archivos de datos de la película.

3. Use el comando siguiente para crear una nueva secuencia de comandos de Python buscará nombres de película para los datos en el resultado de recomendaciones:

        nano show_recommendations.py

    Cuando se abre el editor, utilice la siguiente como el contenido del archivo:

        #!/usr/bin/env python

        import sys

        if len(sys.argv) != 5:
                print "Arguments: userId userDataFilename movieFilename recommendationFilename"
                sys.exit(1)

        userId, userDataFilename, movieFilename, recommendationFilename = sys.argv[1:]

        print "Reading Movies Descriptions"
        movieFile = open(movieFilename)
        movieById = {}
        for line in movieFile:
                tokens = line.split("|")
                movieById[tokens[0]] = tokens[1:]
        movieFile.close()

        print "Reading Rated Movies"
        userDataFile = open(userDataFilename)
        ratedMovieIds = []
        for line in userDataFile:
                tokens = line.split("\t")
                if tokens[0] == userId:
                        ratedMovieIds.append((tokens[1],tokens[2]))
        userDataFile.close()

        print "Reading Recommendations"
        recommendationFile = open(recommendationFilename)
        recommendations = []
        for line in recommendationFile:
                tokens = line.split("\t")
                if tokens[0] == userId:
                        movieIdAndScores = tokens[1].strip("[]\n").split(",")
                        recommendations = [ movieIdAndScore.split(":") for movieIdAndScore in movieIdAndScores ]
                        break
        recommendationFile.close()

        print "Rated Movies"
        print "------------------------"
        for movieId, rating in ratedMovieIds:
                print "%s, rating=%s" % (movieById[movieId][0], rating)
        print "------------------------"

        print "Recommended Movies"
        print "------------------------"
        for movieId, score in recommendations:
                print "%s, score=%s" % (movieById[movieId][0], score)
        print "------------------------"

    Presione **Ctrl-X**, **Y**y finalmente **ENTRAR** para guardar los datos.

3. Use el comando siguiente para hacer que el archivo ejecutable:

        chmod +x show_recommendations.py

4. Ejecute la secuencia de comandos de Python. La siguiente asume que se encuentra en el directorio donde todos los archivos descargados:

        ./show_recommendations.py 4 user-ratings.txt moviedb.txt recommendations.txt

    Esto se mire las recomendaciones generados por el usuario 4 de ID.

    * El archivo **ratings.txt de usuario** se utiliza para recuperar películas que el usuario ha calificado
    * El archivo **moviedb.txt** se usa para recuperar los nombres de las películas
    * La **recommendations.txt** se utiliza para recuperar las recomendaciones de película para este usuario

    El resultado de este comando será similar al siguiente:

        Reading Movies Descriptions
        Reading Rated Movies
        Reading Recommendations
        Rated Movies
        ------------------------
        Mimic (1997), rating=3
        Ulee's Gold (1997), rating=5
        Incognito (1997), rating=5
        One Flew Over the Cuckoo's Nest (1975), rating=4
        Event Horizon (1997), rating=4
        Client, The (1994), rating=3
        Liar Liar (1997), rating=5
        Scream (1996), rating=4
        Star Wars (1977), rating=5
        Wedding Singer, The (1998), rating=5
        Starship Troopers (1997), rating=4
        Air Force One (1997), rating=5
        Conspiracy Theory (1997), rating=3
        Contact (1997), rating=5
        Indiana Jones and the Last Crusade (1989), rating=3
        Desperate Measures (1998), rating=5
        Seven (Se7en) (1995), rating=4
        Cop Land (1997), rating=5
        Lost Highway (1997), rating=5
        Assignment, The (1997), rating=5
        Blues Brothers 2000 (1998), rating=5
        Spawn (1997), rating=2
        Wonderland (1997), rating=5
        In & Out (1997), rating=5
        ------------------------
        Recommended Movies
        ------------------------
        Seven Years in Tibet (1997), score=5.0
        Indiana Jones and the Last Crusade (1989), score=5.0
        Jaws (1975), score=5.0
        Sense and Sensibility (1995), score=5.0
        Independence Day (ID4) (1996), score=5.0
        My Best Friend's Wedding (1997), score=5.0
        Jerry Maguire (1996), score=5.0
        Scream 2 (1997), score=5.0
        Time to Kill, A (1996), score=5.0
        Rock, The (1996), score=5.0
        ------------------------

##<a name="delete-temporary-data"></a>Eliminar datos temporales

Mahout trabajos no quitan datos temporales que se crean al procesar el trabajo. La `--tempDir` parámetro especificado en el trabajo de ejemplo para aislar los archivos temporales en una ruta específica para su eliminación fácil. Para quitar los archivos temporales, utilice el siguiente comando:

    hdfs dfs -rm -f -r /temp/mahouttemp

> [AZURE.WARNING] Si desea volver a ejecutar el comando, debe eliminar también el directorio de resultados. Use las siguientes acciones para eliminar este directorio:
>
> ```hdfs dfs -rm -f -r /example/data/mahoutout```

## <a name="next-steps"></a>Pasos siguientes

Ahora que ha aprendido a usar a Mahout, descubra otras formas de trabajar con datos en HDInsight:

* [Sección con HDInsight](hdinsight-use-hive.md)
* [Cerdo con HDInsight](hdinsight-use-pig.md)
* [MapReduce con HDInsight](hdinsight-use-mapreduce.md)

[build]: http://mahout.apache.org/developers/buildingmahout.html
[movielens]: http://grouplens.org/datasets/movielens/
[100k]: http://files.grouplens.org/datasets/movielens/ml-100k.zip
[getstarted]: hdinsight-hadoop-linux-tutorial-get-started.md
[upload]: hdinsight-upload-data.md
[ml]: http://en.wikipedia.org/wiki/Machine_learning
[forest]: http://en.wikipedia.org/wiki/Random_forest
[management]: https://manage.windowsazure.com/
[enableremote]: ./media/hdinsight-mahout/enableremote.png
[connect]: ./media/hdinsight-mahout/connect.png
[hadoopcli]: ./media/hdinsight-mahout/hadoopcli.png
[tools]: https://github.com/Blackmist/hdinsight-tools
 
