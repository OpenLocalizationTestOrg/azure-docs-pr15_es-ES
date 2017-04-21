<properties
    pageTitle="Analizar los datos de Twitter con Hadoop en HDInsight | Microsoft Azure"
    description="Aprenda a usar subárbol para analizar datos de Twitter en Hadoop en HDInsight para encontrar la frecuencia de uso de una palabra específica."
    services="hdinsight"
    documentationCenter=""
    authors="mumian"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="hdinsight"
    ms.workload="big-data"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/19/2016"
    ms.author="jgao"/>

# <a name="analyze-twitter-data-using-hive-in-hdinsight"></a>Analizar datos de Twitter mediante subárbol en HDInsight

Sitios sociales son una de las fuerzas motrices principales para la adopción de datos grande. API públicas proporcionadas por sitios como Twitter son un origen de datos para analizar y comprender las tendencias populares útil. En este tutorial, obtendrá tweets usando un Twitter API de transmisión y, a continuación, utilizar Apache subárbol de HDInsight de Azure para obtener una lista de usuarios de Twitter que ha enviado la mayoría tweets que contenía una palabra.

> [AZURE.NOTE] Los pasos de este documento requieren un clúster basado en Windows HDInsight. Para pasos específicos para un clúster de Linux, vea [Twitter analizar datos usando subárbol en HDInsight (Linux)](hdinsight-analyze-twitter-data-linux.md).


##<a name="prerequisites"></a>Requisitos previos

Antes de comenzar este tutorial, debe tener el siguiente:

- **Una estación de trabajo** con Azure PowerShell instalado y configurado. 

    Para ejecutar las secuencias de comandos de Windows PowerShell, debe ejecutar Azure PowerShell como administrador y establecer la directiva de ejecución *RemoteSigned*. Vea [secuencias de comandos de ejecutar Windows PowerShell][powershell-script].

    Antes de ejecutar secuencias de comandos de Windows PowerShell, asegúrese de que está conectado a su suscripción de Azure mediante el siguiente cmdlet:

        Login-AzureRmAccount

    Si tiene varias suscripciones de Azure, use el cmdlet siguiente para establecer la suscripción actual:

        Select-AzureRmSubscription -SubscriptionID <Azure Subscription ID>

    [AZURE.INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]

- **Clúster de un HDInsight de Azure**. Para obtener instrucciones sobre el aprovisionamiento de clúster, vea [Introducción al uso HDInsight] [ hdinsight-get-started] o [agrupaciones de aprovisionamiento HDInsight] [hdinsight-provision]. El nombre de clúster necesitará más adelante en el tutorial.

La siguiente tabla enumera los archivos que se utilizan en este tutorial:

Archivos|Descripción
---|---
/tutorials/Twitter/Data/TWEETS.txt|Los datos de origen para el trabajo de la sección.
/tutorials/Twitter/output|La carpeta de salida para el trabajo de la sección. El nombre de archivo predeterminado subárbol trabajo salida es **000000_0**.
tutorials/Twitter/Twitter.hql|El archivo de comandos HiveQL.
/tutorials/Twitter/JobStatus|El estado del trabajo Hadoop.


##<a name="get-twitter-feed"></a>Twitter Get fuente

En este tutorial, usará la [API de secuencia de Twitter][twitter-streaming-api]. El específico de Twitter transmisión API usará es [Estados o filtrar][twitter-statuses-filter].

>[AZURE.NOTE] Un archivo que contiene 10.000 tweets y el archivo de comandos de la sección (que se describe en la siguiente sección) se han cargado en un contenedor de Blob público. Puede omitir esta sección si desea usar los archivos cargados.

[Datos de TWEETS](https://dev.twitter.com/docs/platform-objects/tweets) se almacenan en el formato de notación de objetos JavaScript (JSON) que contiene una estructura compleja anidada. En lugar de escribir el número de líneas de código mediante un lenguaje de programación convencional, puede transformar esta estructura anidada en una tabla subárbol para que se puede consultar un lenguaje de consulta estructurado (SQL)-como lenguaje denominado HiveQL.

Twitter usa OAuth para proporcionar autorización para obtener acceso a su API. OAuth es un protocolo de autenticación que permite a los usuarios aprobar aplicaciones para actuar en su nombre sin tener que compartir su contraseña. Puede encontrar más información en [oauth.net](http://oauth.net/) o en la [Guía para principiantes OAuth](http://hueniverse.com/oauth/) de excelencia de Hueniverse.

El primer paso para utilizar OAuth es crear una nueva aplicación en el sitio para desarrolladores de Twitter.

**Para crear una aplicación de Twitter**

1. Inicie sesión en [https://apps.twitter.com/](https://apps.twitter.com/). Si no tiene una cuenta de Twitter, haga clic en el vínculo **registrarse ahora** .
2. Haga clic en **Crear nueva aplicación**.
3. Escriba el **nombre**, **Descripción**, **sitio Web**. Puede hacer que una dirección URL para el campo **sitio Web** . La siguiente tabla muestra algunos valores de ejemplo para usar:

    Campo|Valor
    ---|---
    Nombre|MyHDInsightApp
    Descripción|MyHDInsightApp
    Sitio Web|http://www.myhdinsightapp.com

4. Active **Sí, acepto**y, a continuación, haga clic en **crear una aplicación de Twitter**.
5. Haga clic en la pestaña **permisos** . El permiso predeterminado es de **solo lectura**. Esto es suficiente para este tutorial.
6. Haga clic en la pestaña de **claves y Tokens de acceso** .
7. Haga clic en **Crear mi token de acceso**.
8. En la esquina superior derecha de la página, haga clic en **Prueba OAuth** .
9. Anote la **clave comercial**, **secreto comercial**, **token de acceso**y **secreto de token de acceso**. Necesitará los valores más adelante en el tutorial.

En este tutorial, usará Windows PowerShell para realizar el servicio web de llamadas. Para un ejemplo de .NET C#, vea [analizar opinión de Twitter en tiempo real con HBase en HDInsight][hdinsight-hbase-twitter-sentiment]. La popular herramienta para realizar llamadas de servicio web es [*doblez*][curl]. Doblez puede descargarse desde [aquí][curl-download].

>[AZURE.NOTE] Al usar el comando curl en Windows, use comillas dobles en lugar de comillas simples para los valores de la opción.

**Para obtener tweets**

1. Abra el entorno Scripting integrado (ISE) de PowerShell Windows. (En la pantalla de inicio de Windows 8, escriba **PowerShell_ISE** y, a continuación, haga clic en **Windows PowerShell ISE**. Consulte [Inicio de Windows PowerShell en Windows 8 y Windows][powershell-start].)

2. Copie la siguiente secuencia de comandos en el panel de secuencia de comandos:

        #region - variables and constants
        $clusterName = "<HDInsightClusterName>" # Enter the HDInsight cluster name

        # Enter the OAuth information for your Twitter application
        $oauth_consumer_key = "<TwitterAppConsumerKey>";
        $oauth_consumer_secret = "<TwitterAppConsumerSecret>";
        $oauth_token = "<TwitterAppAccessToken>";
        $oauth_token_secret = "<TwitterAppAccessTokenSecret>";

        $destBlobName = "tutorials/twitter/data/tweets.txt" # This script saves the tweets into this blob.

        $trackString = "Azure, Cloud, HDInsight" # This script gets the tweets containing these keywords.
        $track = [System.Uri]::EscapeDataString($trackString);
        $lineMax = 10000  # The script will get this number of tweets. It is about 3 minutes every 100 lines.
        #endregion

        #region - Connect to Azure subscription
        Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green
        Login-AzureRmAccount
        #endregion

        #region - Create a block blob object for writing tweets into Blob storage
        Write-Host "Get the default storage account name and Blob container name using the cluster name ..." -ForegroundColor Green
        $myCluster = Get-AzureRmHDInsightCluster -Name $clusterName
        $resourceGroupName = $myCluster.ResourceGroup
        $storageAccountName = $myCluster.DefaultStorageAccount.Replace(".blob.core.windows.net", "")
        $containerName = $myCluster.DefaultStorageContainer
        Write-Host "`tThe storage account name is $storageAccountName." -ForegroundColor Yellow
        Write-Host "`tThe blob container name is $containerName." -ForegroundColor Yellow

        Write-Host "Define the Azure storage connection string ..." -ForegroundColor Green
        $storageAccountKey = (Get-AzureRmStorageAccountKey -ResourceGroupName $resourceGroupName -Name $storageAccountName)[0].Value
        $storageConnectionString = "DefaultEndpointsProtocol=https;AccountName=$storageAccountName;AccountKey=$storageAccountKey"
        Write-Host "`tThe connection string is $storageConnectionString." -ForegroundColor Yellow

        Write-Host "Create block blob object ..." -ForegroundColor Green
        $storageAccount = [Microsoft.WindowsAzure.Storage.CloudStorageAccount]::Parse($storageConnectionString)
        $storageClient = $storageAccount.CreateCloudBlobClient();
        $storageContainer = $storageClient.GetContainerReference($containerName)
        $destBlob = $storageContainer.GetBlockBlobReference($destBlobName)
        #end region

        # region - Format OAuth strings
        Write-Host "Format oauth strings ..." -ForegroundColor Green
        $oauth_nonce = [System.Convert]::ToBase64String([System.Text.Encoding]::ASCII.GetBytes([System.DateTime]::Now.Ticks.ToString()));
        $ts = [System.DateTime]::UtcNow - [System.DateTime]::ParseExact("01/01/1970", "dd/MM/yyyy", $null)
        $oauth_timestamp = [System.Convert]::ToInt64($ts.TotalSeconds).ToString();

        $signature = "POST&";
        $signature += [System.Uri]::EscapeDataString("https://stream.twitter.com/1.1/statuses/filter.json") + "&";
        $signature += [System.Uri]::EscapeDataString("oauth_consumer_key=" + $oauth_consumer_key + "&");
        $signature += [System.Uri]::EscapeDataString("oauth_nonce=" + $oauth_nonce + "&");
        $signature += [System.Uri]::EscapeDataString("oauth_signature_method=HMAC-SHA1&");
        $signature += [System.Uri]::EscapeDataString("oauth_timestamp=" + $oauth_timestamp + "&");
        $signature += [System.Uri]::EscapeDataString("oauth_token=" + $oauth_token + "&");
        $signature += [System.Uri]::EscapeDataString("oauth_version=1.0&");
        $signature += [System.Uri]::EscapeDataString("track=" + $track);

        $signature_key = [System.Uri]::EscapeDataString($oauth_consumer_secret) + "&" + [System.Uri]::EscapeDataString($oauth_token_secret);

        $hmacsha1 = new-object System.Security.Cryptography.HMACSHA1;
        $hmacsha1.Key = [System.Text.Encoding]::ASCII.GetBytes($signature_key);
        $oauth_signature = [System.Convert]::ToBase64String($hmacsha1.ComputeHash([System.Text.Encoding]::ASCII.GetBytes($signature)));

        $oauth_authorization = 'OAuth ';
        $oauth_authorization += 'oauth_consumer_key="' + [System.Uri]::EscapeDataString($oauth_consumer_key) + '",';
        $oauth_authorization += 'oauth_nonce="' + [System.Uri]::EscapeDataString($oauth_nonce) + '",';
        $oauth_authorization += 'oauth_signature="' + [System.Uri]::EscapeDataString($oauth_signature) + '",';
        $oauth_authorization += 'oauth_signature_method="HMAC-SHA1",'
        $oauth_authorization += 'oauth_timestamp="' + [System.Uri]::EscapeDataString($oauth_timestamp) + '",'
        $oauth_authorization += 'oauth_token="' + [System.Uri]::EscapeDataString($oauth_token) + '",';
        $oauth_authorization += 'oauth_version="1.0"';

        $post_body = [System.Text.Encoding]::ASCII.GetBytes("track=" + $track);
        #endregion

        #region - Read tweets
        Write-Host "Create HTTP web request ..." -ForegroundColor Green
        [System.Net.HttpWebRequest] $request = [System.Net.WebRequest]::Create("https://stream.twitter.com/1.1/statuses/filter.json");
        $request.Method = "POST";
        $request.Headers.Add("Authorization", $oauth_authorization);
        $request.ContentType = "application/x-www-form-urlencoded";
        $body = $request.GetRequestStream();

        $body.write($post_body, 0, $post_body.length);
        $body.flush();
        $body.close();
        $response = $request.GetResponse() ;

        Write-Host "Start stream reading ..." -ForegroundColor Green

        Write-Host "Define a MemoryStream and a StreamWriter for writing ..." -ForegroundColor Green
        $memStream = New-Object System.IO.MemoryStream
        $writeStream = New-Object System.IO.StreamWriter $memStream

        $sReader = New-Object System.IO.StreamReader($response.GetResponseStream())

        $inrec = $sReader.ReadLine()
        $count = 0
        while (($inrec -ne $null) -and ($count -le $lineMax))
        {
            if ($inrec -ne "")
            {
                Write-Host "`n`t $count tweets received." -ForegroundColor Yellow

                $writeStream.WriteLine($inrec)
                $count ++
            }

            $inrec=$sReader.ReadLine()
        }
        #endregion

        #region - Write tweets to Blob storage
        Write-Host "Write to the destination blob ..." -ForegroundColor Green
        $writeStream.Flush()
        $memStream.Seek(0, "Begin")
        $destBlob.UploadFromStream($memStream)

        $sReader.close()
        #endregion

        Write-Host "Completed!" -ForegroundColor Green

3. Establecer la primera cinco a ocho variables de la secuencia de comandos:


    Variable|Descripción
    ---|---
    $clusterName|Este es el nombre del clúster HDInsight donde desea ejecutar la aplicación.
    oauth_consumer_key $|Esta es la de la aplicación de Twitter **clave consumidor** que anotó anteriormente cuando creó la aplicación de Twitter.
    oauth_consumer_secret $|Esta es la aplicación de Twitter **secreto de consumidores** que anotó anteriormente.
    oauth_token $|Esta es la aplicación de Twitter **token de acceso** que anotó anteriormente.
    oauth_token_secret $|Esta es la aplicación de Twitter **secreto de token de acceso** que anotó anteriormente.
    $destBlobName|Este es el nombre de blobs de salida. El valor predeterminado es **tutorials/twitter/data/tweets.txt**. Si cambia el valor predeterminado, debe actualizar las secuencias de comandos de Windows PowerShell en consecuencia.
    $trackString|El servicio web devolverá tweets relacionados con estas palabras clave. El valor predeterminado es **Azure, nube, HDInsight**. Si cambia el valor predeterminado, se actualizará las secuencias de comandos de Windows PowerShell en consecuencia.
    $lineMax|El valor determina cuántos tweets leerá la secuencia de comandos. Tarda aproximadamente tres minutos para leer 100 tweets. Puede establecer un número mayor, pero se tarda más tiempo para descargar.

5. Presione **F5** para ejecutar la secuencia de comandos. Si tiene problemas, como solución alternativa, seleccione todas las líneas y, a continuación, presione **F8**.
6. Deberá ver "Complete!" al final del resultado. Los mensajes de error se mostrará en rojo.

Como un procedimiento de validación, puede comprobar el archivo de salida, **/tutorials/twitter/data/tweets.txt**, en el almacenamiento de blobs de Windows Azure mediante el Explorador de almacenamiento de Azure o PowerShell de Azure. Para un script de Windows PowerShell de ejemplo para enumerar los archivos, vea el [almacenamiento de blobs de uso con HDInsight][hdinsight-storage-powershell].



##<a name="create-hiveql-script"></a>Crear secuencia de comandos de HiveQL

Usando PowerShell de Azure, puede ejecutar varias instrucciones HiveQL uno a la vez o empaquetar la instrucción HiveQL en un archivo de script. En este tutorial, creará una secuencia de comandos de HiveQL. El archivo de comandos debe cargarse en el almacenamiento de blobs de Windows Azure. En la siguiente sección, se ejecutará el archivo de script con PowerShell de Azure.

>[AZURE.NOTE] El archivo de comandos de la sección y un archivo que contiene 10.000 tweets se hayan cargado en un contenedor de Blob público. Puede omitir esta sección si desea usar los archivos cargados.

La secuencia de comandos de HiveQL realizará las siguientes acciones:

1. **Eliminar la tabla tweets_raw** en caso de la tabla ya existe.
2. **Crear la tabla de la sección tweets_raw**. Esta sección temporal estructurada tabla contiene los datos de otra extraer, transformar y cargar procesamiento (ETL). Para obtener información sobre las particiones, vea [subárbol tutorial][apache-hive-tutorial].  
3. **Cargar datos** de la carpeta de origen, /tutorials/twitter/data. El conjunto de datos de gran tweets anidadas formato JSON ahora se ha convertido en una estructura de tabla subárbol temporal.
3. **Eliminar la tabla tweets** en caso de la tabla ya existe.
4. **Crear la tabla tweets**. Antes de que se puede consultar el conjunto de datos de tweets mediante el uso de la sección, debe ejecutar ningún otro proceso ETL. Este proceso ETL define un esquema de la tabla más detallado de los datos que haya almacenado en la tabla "twitter_raw".  
5. **Insertar tabla de sobrescritura**. Esta secuencia de comandos de subárbol compleja se lanzará un conjunto de trabajos MapReduce largos por el clúster de Hadoop. Dependiendo de su conjunto de datos y el tamaño del clúster, esto puede tardar unos 10 minutos.
6. **Insertar sobrescribir el directorio**. Ejecutar una consulta y el conjunto de datos a un archivo de salida. Esta consulta devuelve una lista de usuarios de Twitter que ha enviado la mayoría tweets que contenía la palabra "Azure".

**Para crear una secuencia de comandos de la sección y cárguelo en Azure**

1. Abrir Windows PowerShell ISE.
2. Copie la siguiente secuencia de comandos en el panel de secuencia de comandos:

        #region - variables and constants
        $clusterName = "<Existing HDInsight Cluster Name>" # Enter your HDInsight cluster name
        $subscriptionID = "<Azure Subscription ID>"
        
        $sourceDataPath = "/tutorials/twitter/data"
        $outputPath = "/tutorials/twitter/output"
        $hqlScriptFile = "tutorials/twitter/twitter.hql"
        
        $hqlStatements = @"
        set hive.exec.dynamic.partition = true;
        set hive.exec.dynamic.partition.mode = nonstrict;
        
        DROP TABLE tweets_raw;
        CREATE EXTERNAL TABLE tweets_raw (
            json_response STRING
        )
        STORED AS TEXTFILE LOCATION '$sourceDataPath';
        
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
        
        INSERT OVERWRITE DIRECTORY '$outputPath'
        SELECT name, screen_name, count(1) as cc
            FROM tweets
            WHERE text like "%Azure%"
            GROUP BY name,screen_name
            ORDER BY cc DESC LIMIT 10;
        "@
        #endregion
        
        #region - Connect to Azure subscription
        Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green
        
        Try{
            Get-AzureRmSubscription
        }
        Catch{
            Login-AzureRmAccount
        }
        
        Select-AzureRmSubscription -SubscriptionId $subscriptionID
        
        #endregion
        
        #region - Create a block blob object for writing the Hive script file
        Write-Host "Get the default storage account name and container name based on the cluster name ..." -ForegroundColor Green
        $myCluster = Get-AzureRmHDInsightCluster -ClusterName $clusterName
        $resourceGroupName = $myCluster.ResourceGroup
        $defaultStorageAccountName = $myCluster.DefaultStorageAccount.Replace(".blob.core.windows.net", "")
        $defaultBlobContainerName = $myCluster.DefaultStorageContainer
        Write-Host "`tThe storage account name is $defaultStorageAccountName." -ForegroundColor Yellow
        Write-Host "`tThe blob container name is $defaultBlobContainerName." -ForegroundColor Yellow
        
        Write-Host "Define the connection string ..." -ForegroundColor Green
        $defaultStorageAccountKey = (Get-AzureRmStorageAccountKey -ResourceGroupName $resourceGroupName -Name $defaultStorageAccountName)[0].Value
        $storageConnectionString = "DefaultEndpointsProtocol=https;AccountName=$defaultStorageAccountName;AccountKey=$defaultStorageAccountKey"
        
        Write-Host "Create block blob objects referencing the hql script file" -ForegroundColor Green
        $storageAccount = [Microsoft.WindowsAzure.Storage.CloudStorageAccount]::Parse($storageConnectionString)
        $storageClient = $storageAccount.CreateCloudBlobClient();
        $storageContainer = $storageClient.GetContainerReference($defaultBlobContainerName)
        $hqlScriptBlob = $storageContainer.GetBlockBlobReference($hqlScriptFile)
        
        Write-Host "Define a MemoryStream and a StreamWriter for writing ... " -ForegroundColor Green
        $memStream = New-Object System.IO.MemoryStream
        $writeStream = New-Object System.IO.StreamWriter $memStream
        $writeStream.Writeline($hqlStatements)
        #endregion
        
        #region - Write the Hive script file to Blob storage
        Write-Host "Write to the destination blob ... " -ForegroundColor Green
        $writeStream.Flush()
        $memStream.Seek(0, "Begin")
        $hqlScriptBlob.UploadFromStream($memStream)
        #endregion
        
        Write-Host "Completed!" -ForegroundColor Green

        

4. Establecer las dos primeras variables de la secuencia de comandos:

    Variable|Descripción
    ---|---
    $clusterName|Escriba el nombre del clúster HDInsight donde desea ejecutar la aplicación.
    $subscriptionID|Escriba su identificador de suscripción de Azure.
    $sourceDataPath|La ubicación de almacenamiento de blobs de Windows Azure donde va a leer los datos de las consultas de la sección. No es necesario cambiar esta variable.
    $outputPath|La ubicación de almacenamiento de blobs de Windows Azure donde las consultas de la sección se los resultados. No es necesario cambiar esta variable.
    $hqlScriptFile|La ubicación y el nombre de archivo del archivo de script HiveQL. No es necesario cambiar esta variable.

5. Presione **F5** para ejecutar la secuencia de comandos. Si tiene problemas, como solución alternativa, seleccione todas las líneas y, a continuación, presione **F8**.
6. Deberá ver "Complete!" al final del resultado. Los mensajes de error se mostrará en rojo.

Como un procedimiento de validación, puede comprobar el archivo de salida, **/tutorials/twitter/twitter.hql**, en el almacenamiento de blobs de Windows Azure mediante el Explorador de almacenamiento de Azure o PowerShell de Azure. Para un script de Windows PowerShell de ejemplo para enumerar los archivos, vea el [almacenamiento de blobs de uso con HDInsight][hdinsight-storage-powershell].  


##<a name="process-twitter-data-by-using-hive"></a>Procesar datos de Twitter mediante subárbol

Ha terminado el trabajo de preparación. Ahora, puede invocar la secuencia de comandos de la sección y comprobar los resultados.

### <a name="submit-a-hive-job"></a>Enviar un trabajo de sección

Usar el siguiente script de Windows PowerShell para ejecutar la secuencia de comandos de la sección. Debe establecer la primera variable.

>[AZURE.NOTE] Para usar la tweets y la secuencia de comandos de HiveQL que ha cargado en las últimas dos secciones, establezca $hqlScriptFile en "/ tutorials/twitter/twitter.hql". Para usar los que se hayan cargado a un público blob para usted, establezca $hqlScriptFile en "wasbs://twittertrend@hditutorialdata.blob.core.windows.net/twitter.hql".

    #region variables and constants
    $clusterName = "<Existing Azure HDInsight Cluster Name>"
    $httpUserName = "admin"
    $httpUserPassword = "<HDInsight Cluster HTTP User Password>"
    
    #use one of the following
    $hqlScriptFile = "wasbs://twittertrend@hditutorialdata.blob.core.windows.net/twitter.hql"
    $hqlScriptFile = "/tutorials/twitter/twitter.hql"
    
    $statusFolder = "/tutorials/twitter/jobstatus"
    #endregion
    
    $myCluster = Get-AzureRmHDInsightCluster -ClusterName $clusterName
    $resourceGroupName = $myCluster.ResourceGroup
    $defaultStorageAccountName = $myCluster.DefaultStorageAccount.Replace(".blob.core.windows.net", "")
    $defaultStorageAccountKey = (Get-AzureRmStorageAccountKey -ResourceGroupName $resourceGroupName -Name $defaultStorageAccountName)[0].Value
    
    $defaultBlobContainerName = $myCluster.DefaultStorageContainer
    
    
    #region - Invoke Hive
    Write-Host "Invoke Hive ... " -ForegroundColor Green
    
    # Create the HDInsight cluster
    $pw = ConvertTo-SecureString -String $httpUserPassword -AsPlainText -Force
    $httpCredential = New-Object System.Management.Automation.PSCredential($httpUserName,$pw)
    
    Use-AzureRmHDInsightCluster -ResourceGroupName $resourceGroupName -ClusterName $clusterName -HttpCredential $httpCredential 
    $response = Invoke-AzureRmHDInsightHiveJob -DefaultStorageAccountName $defaultStorageAccountName -DefaultStorageAccountKey $defaultStorageAccountKey -DefaultContainer $defaultBlobContainerName -file $hqlScriptFile -StatusFolder $statusFolder #-OutVariable $outVariable
    
    Write-Host "Display the standard error log ... " -ForegroundColor Green
    $jobID = ($response | Select-String job_ | Select-Object -First 1) -replace ‘\s*$’ -replace ‘.*\s’
    Get-AzureRmHDInsightJobOutput -ClusterName $clusterName -JobId $jobID -DefaultContainer $defaultBlobContainerName -DefaultStorageAccountName $defaultStorageAccountName -DefaultStorageAccountKey $defaultStorageAccountKey -HttpCredential $httpCredential
    #endregion

### <a name="check-the-results"></a>Comprobar los resultados

Utilice la siguiente secuencia de comandos de Windows PowerShell para comprobar el resultado de trabajo de la sección. Debe establecer las dos primeras variables.

    #region variables and constants
    $clusterName = "<Existing Azure HDInsight Cluster Name>"
    
    $blob = "tutorials/twitter/output/000000_0" # The name of the blob to be downloaded.
    #engregion
    
    #region - Create an Azure storage context object
    Write-Host "Get the default storage account name and container name based on the cluster name ..." -ForegroundColor Green
    $myCluster = Get-AzureRmHDInsightCluster -ClusterName $clusterName
    $resourceGroupName = $myCluster.ResourceGroup
    $defaultStorageAccountName = $myCluster.DefaultStorageAccount.Replace(".blob.core.windows.net", "")
    $defaultStorageAccountKey = (Get-AzureRmStorageAccountKey -ResourceGroupName $resourceGroupName -Name $defaultStorageAccountName)[0].Value
    $defaultBlobContainerName = $myCluster.DefaultStorageContainer
    
    Write-Host "`tThe storage account name is $defaultStorageAccountName." -ForegroundColor Yellow
    Write-Host "`tThe blob container name is $defaultBlobContainerName." -ForegroundColor Yellow
    
    Write-Host "Create a context object ... " -ForegroundColor Green
    $storageContext = New-AzureStorageContext -StorageAccountName $defaultStorageAccountName -StorageAccountKey $defaultStorageAccountKey  
    #endregion
    
    #region - Download blob and display blob
    Write-Host "Download the blob ..." -ForegroundColor Green
    cd $HOME
    Get-AzureStorageBlobContent -Container $defaultBlobContainerName -Blob $blob -Context $storageContext -Force
    
    Write-Host "Display the output ..." -ForegroundColor Green
    Write-Host "==================================" -ForegroundColor Green
    cat "./$blob"
    Write-Host "==================================" -ForegroundColor Green
    #end region

> [AZURE.NOTE] La tabla subárbol usa \001 como delimitador de campo. El delimitador no está visible en el resultado.

Después de que los resultados del análisis se han colocado en el almacenamiento de blobs de Windows Azure, puede exportar los datos a un base de datos y SQL server de SQL Azure, exportar los datos a Excel mediante Power Query o conectar la aplicación a los datos mediante el controlador ODBC subárbol. Para obtener más información, vea [Usar Sqoop con HDInsight][hdinsight-use-sqoop], [analizar datos de retrasos de vuelos con HDInsight][hdinsight-analyze-flight-delay-data], [Conectar Excel a HDInsight con Power Query][hdinsight-power-query]y [Conectar Excel a HDInsight con el controlador ODBC subárbol de Microsoft][hdinsight-hive-odbc].

##<a name="next-steps"></a>Pasos siguientes

En este tutorial hemos visto cómo transformar un conjunto de datos JSON no estructurado en una tabla subárbol estructurada de consulta, explorar y analizar datos de Twitter mediante HDInsight de Azure. Para obtener más información, consulte:

- [Introducción a HDInsight][hdinsight-get-started]
- [Analizar la opinión de Twitter en tiempo real con HBase en HDInsight][hdinsight-hbase-twitter-sentiment]
- [Analizar datos de vuelo retraso usando HDInsight][hdinsight-analyze-flight-delay-data]
- [Conectar Excel a HDInsight con Power Query][hdinsight-power-query]
- [Conectar Excel a HDInsight con el controlador ODBC de Microsoft subárbol][hdinsight-hive-odbc]
- [Usar Sqoop con HDInsight][hdinsight-use-sqoop]

[curl]: http://curl.haxx.se
[curl-download]: http://curl.haxx.se/download.html

[apache-hive-tutorial]: https://cwiki.apache.org/confluence/display/Hive/Tutorial

[twitter-streaming-api]: https://dev.twitter.com/docs/streaming-apis
[twitter-statuses-filter]: https://dev.twitter.com/docs/api/1.1/post/statuses/filter

[powershell-start]: http://technet.microsoft.com/library/hh847889.aspx
[powershell-install]: powershell-install-configure.md
[powershell-script]: http://technet.microsoft.com/library/ee176961.aspx


[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-get-started]: hdinsight-hadoop-linux-tutorial-get-started.md
[hdinsight-storage-powershell]: ../hdinsight-hadoop-use-blob-storage.md#powershell
[hdinsight-analyze-flight-delay-data]: hdinsight-analyze-flight-delay-data.md
[hdinsight-storage]: ../hdinsight-hadoop-use-blob-storage.md
[hdinsight-use-sqoop]: hdinsight-use-sqoop.md
[hdinsight-power-query]: hdinsight-connect-excel-power-query.md
[hdinsight-hive-odbc]: hdinsight-connect-excel-hive-odbc-driver.md
[hdinsight-hbase-twitter-sentiment]: hdinsight-hbase-analyze-twitter-sentiment.md
