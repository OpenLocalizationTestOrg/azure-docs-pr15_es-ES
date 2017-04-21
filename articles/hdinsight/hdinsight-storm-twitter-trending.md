<properties
   pageTitle="Temas de tendencias con Apache tormenta en HDInsight de Twitter | Microsoft Azure"
   description="Obtenga información sobre cómo usar Trident para crear una topología de tormenta Apache que determina los temas de tendencias en Twitter en función de hashtags."
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
   ms.date="09/27/2016"
   ms.author="larryfr"/>

#<a name="determine-twitter-trending-topics-with-apache-storm-on-hdinsight"></a>Determinar temas de tendencias de Twitter con Apache tormenta en HDInsight

Obtenga información sobre cómo usar Trident para crear una topología de tormenta que determina tendencias temas (etiquetas hash) en Twitter.

Trident es una abstracción de alto nivel que proporciona herramientas como combinaciones, agregaciones, agrupación, funciones y filtros. Además, Trident añade a simples de procesamiento con estado e incremental. En este ejemplo se muestra cómo puede crear una topología con un pitorro personalizado, función y varias funciones integradas proporcionadas por Trident.

> [AZURE.NOTE] En este ejemplo intensamente se basa en el ejemplo de [Tormenta Trident](https://github.com/jalonsoramos/trident-storm) por Juan Alonso.

##<a name="requirements"></a>Requisitos

* <a href="http://www.oracle.com/technetwork/java/javase/downloads/index.html" target="_blank">Java y el JDK 1.7</a>

* <a href="http://maven.apache.org/what-is-maven.html" target="_blank">Experto</a>

* <a href="http://git-scm.com/" target="_blank">GIT</a>

* Una cuenta de Twitter programador

##<a name="download-the-project"></a>Descargar el proyecto

Use el siguiente código clonar localmente el proyecto.

    git clone https://github.com/Blackmist/TwitterTrending

##<a name="topology"></a>Topología

La topología de este ejemplo es la siguiente:

![topología](./media/hdinsight-storm-twitter-trending/trident.png)

> [AZURE.NOTE] Esta es una vista simplificada de la topología. Varias instancias de los componentes se distribuye entre los nodos en el clúster.

El código de Trident que implementa la topología es la siguiente:

    topology.newStream("spout", spout)
        .each(new Fields("tweet"), new HashtagExtractor(), new Fields("hashtag"))
        .groupBy(new Fields("hashtag"))
        .persistentAggregate(new MemoryMapState.Factory(), new Count(), new Fields("count"))
        .newValuesStream()
        .applyAssembly(new FirstN(10, "count"))
        .each(new Fields("hashtag", "count"), new Debug());

Este código hace lo siguiente:

1. Crea un nuevo flujo de pitorro. Pitorro recupera tweets de Twitter y los filtros de palabras clave específicas (amor, música y café en este ejemplo).

2. HashtagExtractor, una función personalizada, se usa para extraer las etiquetas de hash de cada tweet. Estos son enviados a la secuencia.

3. La secuencia es agrupada por etiqueta de hash y pasa a un agregador. Este agregador crea un recuento de cuántas veces se ha producido cada etiqueta hash. Estos datos se conservan en memoria. Por último, se emite un nuevo flujo que contiene la etiqueta de hash y el recuento.

4. Dado que solo estamos interesados en las etiquetas más populares de hash para un lote determinado de tweets, el ensamblado **FirstN** se aplica para devolver solo los 10 valores superiores, basados en el campo de recuento.

> [AZURE.NOTE] Que no sea la pitorro y HashtagExtractor, usamos la funcionalidad de Trident integrada.
>
> Para obtener información acerca de las operaciones integradas, consulte <a href="https://storm.apache.org/apidocs/storm/trident/operation/builtin/package-summary.html" target="_blank">storm.trident.operation.builtin de paquete</a>.
>
> Para implementaciones Trident estado que no sea MemoryMapState, consulte lo siguiente:
>
> * <a href="https://github.com/fhussonnois/storm-trident-elasticsearch" target="_blank">Búsqueda de tormenta Trident elástica</a>
>
> * <a href="https://github.com/kstyrc/trident-redis" target="_blank">redis Trident</a>

###<a name="the-spout"></a>Pitorro

Pitorro, **TwitterSpout**, usa <a href="http://twitter4j.org/en/" target="_blank">Twitter4j</a> para recuperar tweets de Twitter. Se crea un filtro (amor, música y café en este ejemplo) y la tweets entrantes (status) que coincidan con el filtro se almacenan en una cola de bloqueo vinculada. (Para obtener más información, consulte la <a href="http://docs.oracle.com/javase/7/docs/api/java/util/concurrent/LinkedBlockingQueue.html" target="_blank">Clase LinkedBlockingQueue</a>). Por último, elementos se saca de la cola y emitir a la topología.

###<a name="the-hashtagextractor"></a>La HashtagExtractor

Para extraer las etiquetas de hash, <a href="http://twitter4j.org/javadoc/twitter4j/EntitySupport.html#getHashtagEntities--" target="_blank">getHashtagEntities</a> se usa para recuperar todas las etiquetas de hash que figuran en la tweet. A continuación, estos se emiten a la secuencia.

##<a name="enable-twitter"></a>Habilitar Twitter

Siga los pasos siguientes para registrar una nueva aplicación de Twitter y obtener la información de token consumidor y acceso necesitada para leer de Twitter:

1. Vaya a <a href="https://apps.twitter.com" target="_blank">Aplicaciones de Twitter</a> y haga clic en el botón **Crear nueva aplicación** . Al rellenar el formulario, deje vacío el campo **Dirección URL de devolución de llamada** .

2. Cuando se crea la aplicación, haga clic en la pestaña de **claves y Tokens de acceso** .

3. Copie la información de **Clave de consumidor** y **Secreto de consumidores** .

4. En la parte inferior de la página, seleccione **Crear mi token de acceso** si no existen ninguna tokens. Cuando se han creado los tokens, copie la información de **Token de acceso** y **Secreto de Token de acceso** .

5. En el proyecto **TwitterSpoutTopology** que previamente de clonar, abra el archivo **resources/twitter4j.properties** , agregue la información recopilada en los pasos anteriores y, a continuación, guarde el archivo.

##<a name="build-the-topology"></a>Crear la topología

Use el código siguiente para generar el proyecto:

        cd [directoryname]
        mvn compile

##<a name="test-the-topology"></a>Probar la topología

Use el comando siguiente para probar la topología de forma local:

    mvn compile exec:java -Dstorm.topology=com.microsoft.example.TwitterTrendingTopology

Cuando se inicie la topología, debería ver información de depuración que contiene el hash etiquetas y cuenta emitido por la topología. El resultado debería ser similar al siguiente:

    DEBUG: [Quicktellervalentine, 7]
    DEBUG: [GRAMMYs, 7]
    DEBUG: [AskSam, 7]
    DEBUG: [poppunk, 1]
    DEBUG: [rock, 1]
    DEBUG: [punkrock, 1]
    DEBUG: [band, 1]
    DEBUG: [punk, 1]
    DEBUG: [indonesiapunkrock, 1]

##<a name="next-steps"></a>Pasos siguientes

Ahora que ha probado localmente la topología, descubra cómo implementar la topología: [implementar y administrar topologías tormenta Apache en HDInsight](hdinsight-storm-deploy-monitor-topology.md).

También puede estar interesado en los siguientes temas de tormenta:

* [Desarrollar topologías Java para tormenta en HDInsight con Maven](hdinsight-storm-develop-java-topology.md)

* [Desarrollar C# topologías para tormenta en HDInsight con Visual Studio](hdinsight-storm-develop-csharp-visual-studio-topology.md)

Para consultar más ejemplos de tormenta para HDinsight:

* [Topologías de ejemplo para tormenta en HDInsight](hdinsight-storm-example-topology.md)
