<properties 
    pageTitle="Modelo de diseño DocumentDB: aplicaciones de medios sociales | Microsoft Azure" 
    description="Obtenga información sobre un modelo de diseño para las redes sociales aprovechando la flexibilidad de almacenamiento de DocumentDB y otros servicios de Azure." 
    keywords="aplicaciones de redes sociales"
    services="documentdb" 
    authors="ealsur" 
    manager="jhubbard" 
    editor="" 
    documentationCenter=""/>

<tags 
    ms.service="documentdb" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/27/2016" 
    ms.author="mimig"/>

# <a name="going-social-with-documentdb"></a>Va sociales con DocumentDB

Vida en una sociedad interconectados masivamente significa que, en algún momento de la vida, forman parte de una **red social**. Usamos redes sociales mantenerse en contacto con amigos, compañeros, familiares o a veces compartir nuestra pasión con personas con intereses comunes.

Como ingenieros o los desarrolladores, nos podríamos se ha preguntado ¿cómo estas redes almacenar e interconexión nuestros datos, o puede incluso han encargado para crear o diseñar una nueva red social para un mercado nacido específico ustedes. Esto sucede cuando surge la pregunta: ¿cómo se almacenan todos los datos?

Supongamos que estamos creando una red social nueva y brillante, donde los usuarios pueden exponer artículos con medios relacionados como imágenes, vídeos o música par. Los usuarios pueden comentar publicaciones y proporcionan puntos de clasificación. Habrá una fuente de publicaciones que los usuarios verán y podrá interactuar con los de la página de inicio del sitio Web principal. No parece realmente complejo (al principio), pero para simplificar, vamos a dejar allí (¿podríamos profundizar en fuentes de usuario personalizada afectadas por relaciones, pero supera el objetivo de este artículo).

Por lo tanto, ¿cómo se almacenan esto y dónde?

Muchos de los podrían tener experiencia en bases de datos SQL o tener al menos el concepto de [relacionales modelado de datos](https://en.wikipedia.org/wiki/Relational_model) y es posible que desee empezar a dibujar algo parecido a esto:

![Diagrama que ilustra un modelo relacional relativo](./media/documentdb-social-media-apps/social-media-apps-sql.png) 

Una estructura de datos perfectamente normalizado y bella... que no se escala. 

No me recibe un error que he trabajado con bases de datos SQL toda la vida, que son una excelente, pero como cada plataforma de trama, práctica y software no es perfecto para cada caso.

¿Por qué no SQL la mejor opción en este escenario? Echemos un vistazo a la estructura de un solo elemento para exponer, si quisiera mostrar esa publicación en un sitio Web o una aplicación, que tengo que hacer una consulta con... combinaciones de tabla 8 (!) solamente para mostrar un solo elemento para exponer, ahora, imagen de una secuencia de publicaciones que cargar y aparecen en la pantalla y puede ver dónde voy.

Por supuesto, podríamos, usar una instancia de SQL humongous con capacidad suficiente para resolver miles de consultas con estas combinaciones muchos servir nuestro contenido, pero realmente, ¿por qué se haría cuando existe una solución más sencilla?

## <a name="the-nosql-road"></a>Carretera NoSQL

Hay bases de datos de gráfico especial que se pueden [ejecutar en Azure](http://neo4j.com/developer/guide-cloud-deployment/#_windows_azure) pero no son económicos y requiere servicios de IaaS (infraestructura como servicio, máquinas virtuales principalmente) y el mantenimiento. Voy a buscar en este artículo en una solución de costo menor que funciona mayoría de los escenarios, ejecutar en la base de datos de Azure NoSQL [DocumentDB](https://azure.microsoft.com/services/documentdb/). Con un enfoque [NoSQL](https://en.wikipedia.org/wiki/NoSQL) , almacenar los datos con formato JSON y aplicar [desnormalización](https://en.wikipedia.org/wiki/Denormalization), se puede transformar nuestro publicación previamente complicada en un único [documento](https://en.wikipedia.org/wiki/Document-oriented_database):

    {
        "id":"ew12-res2-234e-544f",
        "title":"post title",
        "date":"2016-01-01",
        "body":"this is an awesome post stored on NoSQL",
        "createdBy":User,
        "images":["http://myfirstimage.png","http://mysecondimage.png"],
        "videos":[
            {"url":"http://myfirstvideo.mp4", "title":"The first video"},
            {"url":"http://mysecondvideo.mp4", "title":"The second video"}
        ],
        "audios":[
            {"url":"http://myfirstaudio.mp3", "title":"The first audio"},
            {"url":"http://mysecondaudio.mp3", "title":"The second audio"}
        ]
    }

Y puede obtenerse con una sola consulta y sin combinaciones. Esto es mucho más sencilla y directa y budget-wise, requiere menos recursos para obtener mejores resultados.

DocumentDB Azure asegura que todas las propiedades están indizadas con la [indización automática](documentdb-indexing.md), que puede ser [personalizada](documentdb-indexing-policies.md). El enfoque libre de esquema nos permite almacenar documentos con diferentes y dinámicas estructuras, quizás mañana que queremos publicaciones que tiene una lista de categorías o hashtags asociados con ellos, DocumentDB controlará los nuevos documentos con los atributos agregados sin ningún trabajo adicional necesario para que nosotros.

Comentarios en una publicación pueden tratará como solo otras publicaciones con una propiedad de elemento primario (Esto simplifica la asignación de objeto). 

    {
        "id":"1234-asd3-54ts-199a",
        "title":"Awesome post!",
        "date":"2016-01-02",
        "createdBy":User2,
        "parent":"ew12-res2-234e-544f"
    }

    {
        "id":"asd2-fee4-23gc-jh67",
        "title":"Ditto!",
        "date":"2016-01-03",
        "createdBy":User3,
        "parent":"ew12-res2-234e-544f"
    }

Y todas las interacciones sociales pueden almacenarse en un objeto independiente como contadores:

    {
        "id":"dfe3-thf5-232s-dse4",
        "post":"ew12-res2-234e-544f",
        "comments":2,
        "likes":10,
        "points":200
    }

Creación de fuentes sólo se trata de la creación de documentos que pueden contener una lista de identificadores de entrada con un orden determinado relevancia:

    [
        {"relevance":9, "post":"ew12-res2-234e-544f"},
        {"relevance":8, "post":"fer7-mnb6-fgh9-2344"},
        {"relevance":7, "post":"w34r-qeg6-ref6-8565"}
    ]

Podríamos tener una secuencia "última" con entradas ordenadas por fecha de creación, una secuencia con esas entradas con "más" le gusta más en las últimas 24 horas, incluso bien podríamos implementar una secuencia personalizada para cada usuario según la lógica como seguidores e intereses y podría ser una lista de publicaciones. Se trata de cómo crear estas listas, pero el rendimiento de lectura permanece sin obstáculos. Una vez adquirido una de estas listas, se emite una sola consulta a DocumentDB mediante el [operador IN](documentdb-sql-query.md#where-clause) para obtener páginas de publicaciones a la vez.

Se puede generar las secuencias de alimentación con [Los servicios de aplicación de Azure](https://azure.microsoft.com/services/app-service/) procesos en segundo plano: [Webjobs](../app-service-web/web-sites-create-web-jobs.md). Una vez que se crea una publicación, pueden activarse procesos en segundo plano con [El almacenamiento de Azure](https://azure.microsoft.com/services/storage/) [colas](../storage/storage-dotnet-how-to-use-queues.md) y Webjobs desencadenado mediante el [SDK de Webjobs de Azure](../app-service-web/websites-dotnet-webjobs-sdk.md), implementar la propagación post dentro de secuencias basado en nuestra propio lógica personalizada. 

Puede procesar puntos y me gusta sobre una publicación de una manera diferida usando esta misma técnica para crear un entorno finalmente coherente.

Seguidores son más complicados. DocumentDB tiene un límite de tamaño de documento de 512Kb, por lo que piensa almacenar seguidores como un documento con la estructura:

    {
        "id":"234d-sd23-rrf2-552d",
        "followersOf": "dse4-qwe2-ert4-aad2",
        "followers":[
            "ewr5-232d-tyrg-iuo2",
            "qejh-2345-sdf1-ytg5",
            //...
            "uie0-4tyg-3456-rwjh"
        ]
    }

Esto podría funcionar para un usuario con unos pocos miles seguidores, pero si algunos famosos unen nuestra clasifica, esta se enfoque finalmente acertar el límite de tamaño del documento.

Para resolver este problema, podemos usar un enfoque mixto. Como parte del documento estadísticas de usuario podemos almacenar el número de seguidores:

    {
        "id":"234d-sd23-rrf2-552d",
        "user": "dse4-qwe2-ert4-aad2",
        "followers":55230,
        "totalPosts":452,
        "totalPoints":11342
    }

Y el gráfico real de seguidores puede almacenarse en tablas de almacenamiento de Azure con una [extensión](https://github.com/richorama/AzureStorageExtensions#azuregraphstore) que permite la recuperación y el almacenamiento de "A-sigue-B" simple. De esta forma nos podemos delegar el proceso de recuperación de la lista de seguidores exacta (al que sea necesario) a las tablas de almacenamiento de Azure, pero para una búsqueda rápida de números, nos seguimos usando DocumentDB.

## <a name="the-ladder-pattern-and-data-duplication"></a>Duplicación de trama y los datos de "Escalera"

Es posible que haya observado en el documento JSON que hace referencia a una publicación, hay varias apariciones de un usuario. Y habría adivinar derecha, que esto significa que la información que representa un usuario, este desnormalización puede estar presente en más de un lugar.

Para permitir consultas más rápidas, nos provocar duplicación de datos. El problema con este efecto secundario es que si alguna acción, cambios en los datos de un usuario, es necesario buscar todas las actividades nunca se produjo y actualizar todos ellos. ¿No muy práctico, a la derecha de sonido?

Bases de datos de gráfico solución en su propia manera, vamos a resolver es identificar los atributos de clave de un usuario que se muestra en la aplicación para cada actividad. Si mostrar una publicación en nuestra aplicación visualmente y mostrar solo el creador nombre y una imagen, ¿por qué almacenar todos los datos del usuario en el atributo "createdBy"? Si para cada comentario le mostraremos la imagen del usuario, no es necesario el resto de su información. Es donde algo que llamo "patrón de escala" entra en reproducir.

Echemos información de usuario como ejemplo:

    {
        "id":"dse4-qwe2-ert4-aad2",
        "name":"John",
        "surname":"Doe",
        "address":"742 Evergreen Terrace",
        "birthday":"1983-05-07",
        "email":"john@doe.com",
        "twitterHandle":"@john",
        "username":"johndoe",
        "password":"some_encrypted_phrase",
        "totalPoints":100,
        "totalPosts":24
    }
    
Consultando esta información, que podamos detectar rápidamente cuál es la información esencial y que no es, por lo que crea una "escalera":

![Diagrama de un modelo de la escalera](./media/documentdb-social-media-apps/social-media-apps-ladder.png)

El paso más pequeño se denomina una UserChunk, la mínima fragmento de información que identifica un usuario y se utiliza para duplicar datos. Reduciendo el tamaño de los datos duplicados a la información "mostraremos", se reduce la posibilidad de actualizaciones masivas.

El segundo paso se denomina el usuario, es todos los datos que se utilizará en la mayoría de las consultas dependientes de rendimiento en DocumentDB más acceder y crítica. Incluye la información representada por una UserChunk.

El mayor es el usuario extendido. Incluye toda la información de usuario crítica junto con otros datos que realmente no requieren que para se puedan leer rápidamente o su uso es final (por ejemplo, el proceso de inicio de sesión). Estos datos se pueden almacenar fuera DocumentDB, en la base de datos de SQL Azure o las tablas de almacenamiento de Azure.

¿Por qué debería que dividir el usuario e incluso almacenar esta información en diferentes lugares? Porque el espacio de almacenamiento en DocumentDB es [no infinito](documentdb-limits.md) de rendimiento de un punto de vista, mayor será el documentos, la costosa las consultas. Mantener documentos fino, con la información adecuada para realizar todas las consultas de rendimiento depende de su red social y almacenar el resto de información adicional para escenarios final como modificaciones de perfil completo, inicios de sesión, incluso minería de datos para el análisis de uso e iniciativas de datos grande. Nos realmente no importa si los datos de recopilación de minería de datos están más lentos porque se está ejecutando en la base de datos de SQL Azure, se ha se refieren a través que nuestros usuarios tengan una experiencia rápida y ligera. Un usuario, que se almacena en DocumentDB, tendría un aspecto similar a este:

    {
        "id":"dse4-qwe2-ert4-aad2",
        "name":"John",
        "surname":"Doe",
        "username":"johndoe"
        "email":"john@doe.com",
        "twitterHandle":"@john"
    }

Y una entrada tendría el siguiente aspecto:

    {
        "id":"1234-asd3-54ts-199a",
        "title":"Awesome post!",
        "date":"2016-01-02",
        "createdBy":{
            "id":"dse4-qwe2-ert4-aad2",
            "username":"johndoe"
        }
    }

Y cuando una edición surge donde uno de los atributos del fragmento se ve afectado, es fácil encontrar los documentos afectados por el uso de consultas que apuntan a los atributos indizados (seleccione * FROM publicaciones p dónde p.createdBy.id == "edited_user_id") y, a continuación, actualizar los fragmentos.

## <a name="the-search-box"></a>El cuadro de búsqueda

Los usuarios generará, Afortunadamente, una gran cantidad de contenido. Y podremos proporciona la capacidad de buscar y encontrar contenido que podría no ser directamente en sus secuencias de contenido, quizás porque no seguimos los creadores o quizás que solo estamos trabajando para encontrar que post antiguo realizamos hace 6 meses.

Por suerte, y porque usamos DocumentDB de Azure, podemos implementar fácilmente un motor de búsqueda con [búsqueda de Azure](https://azure.microsoft.com/services/search/) en un par de minutos y sin escribir una sola línea de código (excepto evidentes, el proceso de búsqueda y la interfaz de usuario).

¿Por qué es tan fácil?

Búsqueda de Azure implementa lo denominamos [indizadores](https://msdn.microsoft.com/library/azure/dn946891.aspx), procesos en segundo plano que enlazar en los repositorios de datos y automáticamente agregar, actualizar o quitar los objetos en los índices. Admiten una [base de datos de SQL Azure indizadores](https://blogs.msdn.microsoft.com/kaevans/2015/03/06/indexing-azure-sql-database-with-azure-search/), [indizadores de Blobs de Azure](../search/search-howto-indexing-azure-blob-storage.md) y por suerte, [indizadores DocumentDB de Azure](../documentdb/documentdb-search-indexer.md). La transición de la información de DocumentDB buscar Azure es sencilla, como ambos almacenar la información en formato JSON, necesitamos [crear nuestro índice](../search/search-create-index-portal.md) y mapa qué atributos de nuestros documentos queremos indexado y eso es todo, en cuestión de minutos (según el tamaño de los datos), todo el contenido de nuestro estará disponible para buscar, la mejor solución de búsqueda como servicio en la infraestructura de la nube. 

Para obtener más información acerca de la búsqueda de Azure, puede visitar la [Guía de Hitchhiker de búsqueda](https://blogs.msdn.microsoft.com/mvpawardprogram/2016/02/02/a-hitchhikers-guide-to-search/).

## <a name="the-underlying-knowledge"></a>El conocimiento subyacente

Tras almacenar este contenido que crece y crece diariamente, es posible que encontremos nosotros pensando: ¿Qué puedo hacer con todos los esta secuencia de la información de Mis usuarios?

La respuesta es sencilla: colocarlo para trabajar y obtener de él.

Pero, ¿qué nos podemos aprender? Algunos ejemplos fáciles incluyen [análisis de opinión](https://en.wikipedia.org/wiki/Sentiment_analysis), recomendaciones contenidas basadas en las preferencias de usuario o incluso una automatizado moderador contenido que garantiza que todo el contenido publicado por nuestra red social que es seguro para la familia.

Ahora que tengo que enlazado, deberá probablemente piensa necesita algunos doctorado en ciencias matemáticas para extraer estos patrones y la información de bases de datos simples y archivos, pero es incorrecta.

[Aprendizaje del equipo de azure](https://azure.microsoft.com/services/machine-learning/), parte del [Conjunto de aplicaciones de inteligencia de Cortana](https://www.microsoft.com/en/server-cloud/cortana-analytics-suite/overview.aspx), es la un servicio de nube totalmente administrado que le permite crear flujos de trabajo mediante algoritmos en una interfaz sencilla de arrastrar y colocar, el código de sus propios algoritmos en [R](https://en.wikipedia.org/wiki/R_(programming_language)) o usar algunas de ya creado y listo para usar las API como: [Análisis de texto](https://gallery.cortanaanalytics.com/MachineLearningAPI/Text-Analytics-2), [Contenido del moderador](https://www.microsoft.com/moderator) o [recomendaciones](https://gallery.cortanaanalytics.com/MachineLearningAPI/Recommendations-2).

Para realizar cualquiera de estos escenarios de aprendizaje, podemos usar [Lago de datos de Azure](https://azure.microsoft.com/services/data-lake-store/) para recopilar la información de orígenes diferentes y utilizar [U SQL](https://azure.microsoft.com/documentation/videos/data-lake-u-sql-query-execution/) para procesar la información y generar un resultado que puede procesar el aprendizaje Azure.

Otra opción disponible es usar [Servicios cognitivas de Microsoft](https://www.microsoft.com/cognitive-services) para analizar el contenido de los usuarios; no sólo podemos comprendemos mejor (a través de análisis que escriben con la [API de análisis de texto](https://www.microsoft.com/cognitive-services/en-us/text-analytics-api)), pero también podemos detectar contenido no deseado y actuar en consecuencia con la [API de visión del equipo](https://www.microsoft.com/cognitive-services/en-us/computer-vision-api). Servicios cognitivos incluyen una gran cantidad de soluciones de cuadro que no requieren ningún tipo de conocimiento de aprendizaje para usar.

## <a name="conclusion"></a>Conclusión

En este artículo se intenta aclarar ciertos en las alternativas de crear redes sociales completamente en Azure con los servicios de bajo costo y proporcionar resultados excelentes al animar el uso de una distribución de datos y solución de varios niveles de almacenamiento denominado "Escala".

![Diagrama de interacción entre los servicios de Azure para las redes sociales](./media/documentdb-social-media-apps/social-media-apps-azure-solution.png)

La realidad es que no hay ningún mágica para este tipo de casos, es la sinergia creada por la combinación de excelentes servicios que nos permiten crear grandes experiencias: la velocidad y libertad de Azure DocumentDB para proporcionar una excelente aplicación social, la inteligencia detrás de una solución de búsqueda de primera clase como la búsqueda de Azure, la flexibilidad de Azure Servicios de aplicación al host no incluso aplicaciones independiente del idioma pero procesos en segundo plano eficaces y el almacenamiento de Azure ampliables y la base de datos de Azure SQL para almacenar grandes cantidades de datos y la potencia analítica de Azure el aprendizaje para crear conocimiento e inteligencia que puedan proporcionar comentarios a nuestro procesos y ayuda nos entregar contenido adecuado a los usuarios de la derecha.

## <a name="next-steps"></a>Pasos siguientes

Más información sobre, lea el artículo de [modelado de datos en DocumentDB](documentdb-modeling-data.md) de modelado de datos. Si está interesado en los demás casos de uso para DocumentDB, consulte [DocumentDB comunes de casos de uso](documentdb-use-cases.md).

O más información sobre DocumentDB siguiendo la [Ruta de aprendizaje de DocumentDB](https://azure.microsoft.com/documentation/learning-paths/documentdb/).
