<properties
    pageTitle="Analizar los datos de Twitter con Apache subárbol en HDInsight | Microsoft Azure"
    description="Obtenga información sobre cómo utilizar Python para almacenar Tweets que contengan determinadas palabras clave, a continuación, usar la sección y Hadoop en HDInsight para transformar los datos sin formato de TWitter en una tabla subárbol que permiten búsqueda."
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
    ms.date="09/27/2016"
    ms.author="larryfr"/>

# <a name="analyze-twitter-data-using-hive-in-hdinsight"></a>Analizar datos de Twitter mediante subárbol en HDInsight

En este documento, obtendrá tweets usando un Twitter API de transmisión y, a continuación, usar Apache subárbol en un clúster basado en Linux HDInsight al proceso el JSON con formato de datos. El resultado será una lista de usuarios de Twitter que ha enviado la mayoría tweets que contenía una palabra.

> [AZURE.NOTE] Aunque se pueden utilizar piezas individuales de este documento con clústeres HDInsight basado en Windows (Python por ejemplo), varios pasos se basan en mediante un clúster de HDInsight basados en Linux. Para pasos específicos para un clúster basado en Windows, vea [Twitter analizar datos usando subárbol en HDInsight](hdinsight-analyze-twitter-data.md).

###<a name="prerequisites"></a>Requisitos previos

Antes de comenzar este tutorial, debe tener el siguiente:

- Un __clúster de HDInsight de Azure basados en Linux__. Para obtener información sobre la creación de un clúster, vea [Introducción a HDInsight de Linux](hdinsight-hadoop-linux-tutorial-get-started.md) para conocer los pasos sobre cómo crear un clúster.

- Un __cliente SSH__. Para obtener más información sobre cómo utilizar SSH con HDInsight de Linux, consulte los siguientes artículos:

    * [Utilizar SSH con basados en Linux Hadoop en HDInsight de Linux, Unix o OS X](hdinsight-hadoop-linux-use-ssh-unix.md)

    * [Utilizar SSH con basados en Linux Hadoop en HDInsight de Windows.](hdinsight-hadoop-linux-use-ssh-windows.md)

- __Python__ y [puntos](https://pypi.python.org/pypi/pip)

##<a name="get-a-twitter-feed"></a>Obtener una fuente de Twitter

Twitter le permite recuperar los [datos de cada tweet](https://dev.twitter.com/docs/platform-objects/tweets) como un documento de notación de objetos JavaScript (JSON) a través de la API de REST. [OAuth](http://oauth.net) es necesario para que la API de autenticación. También debe crear una _Aplicación de Twitter_ que contiene la configuración que usa para obtener acceso a la API.

###<a name="create-a-twitter-application"></a>Crear una aplicación de Twitter

1. Desde un explorador web, inicie sesión en [https://apps.twitter.com/](https://apps.twitter.com/). Si no tiene una cuenta de Twitter, haga clic en el vínculo **registrarse ahora** .
2. Haga clic en **Crear nueva aplicación**.
3. Escriba el **nombre**, **Descripción**, **sitio Web**. Puede hacer que una dirección URL para el campo **sitio Web** . La siguiente tabla muestra algunos valores de ejemplo para usar:

  	| Campo | Valor |
  	|:----- |:----- |
  	| Nombre  | MyHDInsightApp |
  	| Descripción | MyHDInsightApp |
  	| Sitio Web | http://www.myhdinsightapp.com |
    
4. Active **Sí, acepto**y, a continuación, haga clic en **crear una aplicación de Twitter**.
5. Haga clic en la pestaña **permisos** . El permiso predeterminado es de **solo lectura**. Esto es suficiente para este tutorial.
6. Haga clic en la pestaña de **claves y Tokens de acceso** .
7. Haga clic en **Crear mi token de acceso**.
8. En la esquina superior derecha de la página, haga clic en **Prueba OAuth** .
9. Anote la **clave comercial**, **secreto comercial**, **token de acceso**y **secreto de token de acceso**. Necesitará los valores más adelante.

>[AZURE.NOTE] Al usar el comando curl en Windows, use comillas dobles en lugar de comillas simples para los valores de la opción.

###<a name="download-tweets"></a>Descargar tweets

El siguiente código de Python descargar 10.000 tweets de Twitter y guardarlos en un archivo llamado __tweets.txt__.

> [AZURE.NOTE] Los siguientes pasos se realizan en el clúster HDInsight, puesto que ya está instalado Python.

1. Conectarse al clúster HDInsight mediante SSH:

        ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net
        
    Si utiliza una contraseña para proteger su cuenta de usuario SSH, se le pedirá que lo especifique. Si utiliza una clave pública, tendrá que usar la `-i` parámetro para especificar la clave privada correspondiente. Por ejemplo, `ssh -i ~/.ssh/id_rsa USERNAME@CLUSTERNAME-ssh.azurehdinsight.net`.
        
    Para obtener más información sobre cómo utilizar SSH con HDInsight de Linux, consulte los siguientes artículos:
    
    * [Utilizar SSH con basados en Linux Hadoop en HDInsight de Linux, Unix o OS X](hdinsight-hadoop-linux-use-ssh-unix.md)

    * [Utilizar SSH con basados en Linux Hadoop en HDInsight de Windows.](hdinsight-hadoop-linux-use-ssh-windows)
    
2. De forma predeterminada, la utilidad de __puntos__ no está instalada en el nodo principal de HDInsight. Use los siguientes para instalar y, a continuación, actualice esta utilidad:

        sudo apt-get install python-pip
        sudo pip install --upgrade pip

3. El código para descargar tweets se basa en [Tweepy](http://www.tweepy.org/) y [Progressbar](https://pypi.python.org/pypi/progressbar/2.2). Para instalar los archivos, use el siguiente comando:

        sudo apt-get install python-dev libffi-dev libssl-dev
        sudo apt-get remove python-openssl
        sudo pip install tweepy progressbar pyOpenSSL requests[security]
        
    > [AZURE.NOTE] Los bits acerca de cómo quitar openssl de python, instalar python desarrollo, desarrollo de libffi, desarrollo de libssl, pyOpenSSL y solicitudes [seguridad] es evitar una advertencia InsecurePlatform al conectarse a Twitter a través de SSL de Python.
    >
    > Tweepy v3.2.0 se usa para evitar [un error](https://github.com/tweepy/tweepy/issues/576) que puede producirse cuando se procesan tweets.

4. Use el comando siguiente para crear un nuevo archivo denominado __gettweets.py__:

        nano gettweets.py

5. Utilice la siguiente como el contenido del archivo __gettweets.py__ . Reemplazar la información de marcador de posición para __consumidor\_secreto__, __consumidor\_clave__, __access /\_token__, y __access\_token\_secreto__ con la información de la aplicación de Twitter.

        #!/usr/bin/python

        from tweepy import Stream, OAuthHandler
        from tweepy.streaming import StreamListener
        from progressbar import ProgressBar, Percentage, Bar
        import json
        import sys

        #Twitter app information
        consumer_secret='Your consumer secret'
        consumer_key='Your consumer key'
        access_token='Your access token'
        access_token_secret='Your access token secret'

        #The number of tweets we want to get
        max_tweets=10000

        #Create the listener class that will receive and save tweets
        class listener(StreamListener):
            #On init, set the counter to zero and create a progress bar
            def __init__(self, api=None):
                self.num_tweets = 0
                self.pbar = ProgressBar(widgets=[Percentage(), Bar()], maxval=max_tweets).start()

            #When data is received, do this
            def on_data(self, data):
                #Append the tweet to the 'tweets.txt' file
                with open('tweets.txt', 'a') as tweet_file:
                    tweet_file.write(data)
                    #Increment the number of tweets
                    self.num_tweets += 1
                    #Check to see if we have hit max_tweets and exit if so
                    if self.num_tweets >= max_tweets:
                        self.pbar.finish()
                        sys.exit(0)
                    else:
                        #increment the progress bar
                        self.pbar.update(self.num_tweets)
                return True

            #Handle any errors that may occur
            def on_error(self, status):
                print status

        #Get the OAuth token
        auth = OAuthHandler(consumer_key, consumer_secret)
        auth.set_access_token(access_token, access_token_secret)
        #Use the listener class for stream processing
        twitterStream = Stream(auth, listener())
        #Filter for these topics
        twitterStream.filter(track=["azure","cloud","hdinsight"])

6. Utilice __Ctrl + X__, a continuación, __Y__ para guardar el archivo.

7. Use el comando siguiente para ejecutar el archivo y descargar tweets:

        python gettweets.py

    Un indicador de progreso debe aparecer y contar hasta 100% como la tweets se descargan y se guardan en el archivo.

    > [AZURE.NOTE] Si se tarda mucho tiempo para la barra de progreso avanzar, debe cambiar el filtro para realizar el seguimiento de tendencias temas; Cuando hay una gran cantidad de tweets sobre el tema que va a filtrar, puede ir muy rápidamente la 10000 tweets es necesarios.

###<a name="upload-the-data"></a>Cargar los datos

Para cargar los datos a WASB (el sistema de archivos distribuido utilizado por HDInsight), use los comandos siguientes:

    hdfs dfs -mkdir -p /tutorials/twitter/data
    hdfs dfs -put tweets.txt /tutorials/twitter/data/tweets.txt

Aquí almacenan los datos en una ubicación que pueden tener acceso todos los nodos en el clúster.

##<a name="run-the-hiveql-job"></a>Ejecutar el trabajo HiveQL


1. Use el comando siguiente para crear un archivo que contiene instrucciones HiveQL:

        nano twitter.hql
    
    Utilice la siguiente como el contenido del archivo:

        set hive.exec.dynamic.partition = true;
        set hive.exec.dynamic.partition.mode = nonstrict;
        -- Drop table, if it exists
        DROP TABLE tweets_raw;
        -- Create it, pointing toward the tweets logged from Twitter
        CREATE EXTERNAL TABLE tweets_raw (
            json_response STRING
        )
        STORED AS TEXTFILE LOCATION '/tutorials/twitter/data';
        -- Drop and recreate the destination table
        DROP TABLE tweets;
        CREATE TABLE tweets
        (
            id BIGINT,
            created_at STRING,
            created_at_date STRING,
            created_at_year STRING,
            created_at_month STRING,
            created_at_day STRING,
            created_at_time STRING,
            in_reply_to_user_id_str STRING,
            text STRING,
            contributors STRING,
            retweeted STRING,
            truncated STRING,
            coordinates STRING,
            source STRING,
            retweet_count INT,
            url STRING,
            hashtags array<STRING>,
            user_mentions array<STRING>,
            first_hashtag STRING,
            first_user_mention STRING,
            screen_name STRING,
            name STRING,
            followers_count INT,
            listed_count INT,
            friends_count INT,
            lang STRING,
            user_location STRING,
            time_zone STRING,
            profile_image_url STRING,
            json_response STRING
        );
        -- Select tweets from the imported data, parse the JSON,
        -- and insert into the tweets table
        FROM tweets_raw
        INSERT OVERWRITE TABLE tweets
        SELECT
            cast(get_json_object(json_response, '$.id_str') as BIGINT),
            get_json_object(json_response, '$.created_at'),
            concat(substr (get_json_object(json_response, '$.created_at'),1,10),' ',
            substr (get_json_object(json_response, '$.created_at'),27,4)),
            substr (get_json_object(json_response, '$.created_at'),27,4),
            case substr (get_json_object(json_response, '$.created_at'),5,3)
                when "Jan" then "01"
                when "Feb" then "02"
                when "Mar" then "03"
                when "Apr" then "04"
                when "May" then "05"
                when "Jun" then "06"
                when "Jul" then "07"
                when "Aug" then "08"
                when "Sep" then "09"
                when "Oct" then "10"
                when "Nov" then "11"
                when "Dec" then "12" end,
            substr (get_json_object(json_response, '$.created_at'),9,2),
            substr (get_json_object(json_response, '$.created_at'),12,8),
            get_json_object(json_response, '$.in_reply_to_user_id_str'),
            get_json_object(json_response, '$.text'),
            get_json_object(json_response, '$.contributors'),
            get_json_object(json_response, '$.retweeted'),
            get_json_object(json_response, '$.truncated'),
            get_json_object(json_response, '$.coordinates'),
            get_json_object(json_response, '$.source'),
            cast (get_json_object(json_response, '$.retweet_count') as INT),
            get_json_object(json_response, '$.entities.display_url'),
            array(
                trim(lower(get_json_object(json_response, '$.entities.hashtags[0].text'))),
                trim(lower(get_json_object(json_response, '$.entities.hashtags[1].text'))),
                trim(lower(get_json_object(json_response, '$.entities.hashtags[2].text'))),
                trim(lower(get_json_object(json_response, '$.entities.hashtags[3].text'))),
                trim(lower(get_json_object(json_response, '$.entities.hashtags[4].text')))),
            array(
                trim(lower(get_json_object(json_response, '$.entities.user_mentions[0].screen_name'))),
                trim(lower(get_json_object(json_response, '$.entities.user_mentions[1].screen_name'))),
                trim(lower(get_json_object(json_response, '$.entities.user_mentions[2].screen_name'))),
                trim(lower(get_json_object(json_response, '$.entities.user_mentions[3].screen_name'))),
                trim(lower(get_json_object(json_response, '$.entities.user_mentions[4].screen_name')))),
            trim(lower(get_json_object(json_response, '$.entities.hashtags[0].text'))),
            trim(lower(get_json_object(json_response, '$.entities.user_mentions[0].screen_name'))),
            get_json_object(json_response, '$.user.screen_name'),
            get_json_object(json_response, '$.user.name'),
            cast (get_json_object(json_response, '$.user.followers_count') as INT),
            cast (get_json_object(json_response, '$.user.listed_count') as INT),
            cast (get_json_object(json_response, '$.user.friends_count') as INT),
            get_json_object(json_response, '$.user.lang'),
            get_json_object(json_response, '$.user.location'),
            get_json_object(json_response, '$.user.time_zone'),
            get_json_object(json_response, '$.user.profile_image_url'),
            json_response
        WHERE (length(json_response) > 500);
        
        
3. Presione __Ctrl + X__y luego presione __Y__ para guardar el archivo.

4. Utilice el comando siguiente para ejecutar la HiveQL contenida en el archivo:

        beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http' -n admin -i twitter.hql
        
    Esto cargar el shell de sección, ejecute el HiveQL en el archivo __twitter.hql__ y finalmente devuelven una `jdbc:hive2//localhost:10001/>` símbolo del sistema.
    
5. Desde el indicador beeline, utilice los siguientes para comprobar que puede seleccionar datos de la tabla de __tweets__ creada por la HiveQL en el archivo __twitter.hql__ :
        
        SELECT name, screen_name, count(1) as cc
            FROM tweets
            WHERE text like "%Azure%"
            GROUP BY name,screen_name
            ORDER BY cc DESC LIMIT 10;

    Devolverá un máximo de 10 tweets que contienen la palabra __Azure__ en el texto del mensaje.

##<a name="next-steps"></a>Pasos siguientes

En este tutorial hemos visto cómo transformar un conjunto de datos JSON no estructurado en una tabla subárbol estructurada de consulta, explorar y analizar datos de Twitter mediante HDInsight de Azure. Para obtener más información, consulte:

- [Introducción a HDInsight](hdinsight-hadoop-linux-tutorial-get-started.md)
- [Analizar datos de vuelo retraso usando HDInsight](hdinsight-analyze-flight-delay-data-linux.md)

[curl]: http://curl.haxx.se
[curl-download]: http://curl.haxx.se/download.html

[apache-hive-tutorial]: https://cwiki.apache.org/confluence/display/Hive/Tutorial

[twitter-streaming-api]: https://dev.twitter.com/docs/streaming-apis
[twitter-statuses-filter]: https://dev.twitter.com/docs/api/1.1/post/statuses/filter
