<properties
    pageTitle="Cuándo usar NoSQL vs SQL | Microsoft Azure"
    description="Comparar las ventajas de usar soluciones no relacionales de NoSQL frente a soluciones SQL. Obtenga más información si uno de los servicios de Microsoft Azure NoSQL o SQL Server mejor se adapte a su caso."
    keywords="NoSQL vs sql, cuándo usar NoSQL, sql vs nosql"
    services="documentdb"
    documentationCenter=""
    authors="mimig1"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="documentdb"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="article" 
    ms.date="06/24/2016"
    ms.author="mimig"/>

# <a name="nosql-vs-sql"></a>NoSQL vs SQL

SQL Server y bases de datos relacionales (RDBMS) han sido las bases de datos ir a más de 20 años. Sin embargo, la necesidad de mayor para procesar volúmenes y tipos de datos a una velocidad rápida mayor ha modificado la naturaleza de las necesidades de almacenamiento de datos para los desarrolladores de aplicaciones. Para habilitar este escenario, las bases de datos NoSQL que puedan almacenar datos estructurados y heterogéneos a escala han obtenido en popularidad. 

NoSQL es una categoría de bases de datos diferentes de bases de datos SQL. NoSQL a menudo se usa para hacer referencia a los sistemas de administración de datos que son "No SQL" o un método de administración de datos que incluye "no solo SQL". Hay una serie de tecnologías de la categoría de NoSQL, incluidos bases de datos del documento, valor clave almacena, almacena familia de columna y bases de datos de gráfico, que son populares con juegos, sociales, y IoT aplicaciones.

![Diagrama de introducción NoSQL vs SQL que se muestran los modelos de datos y escenarios comunes](./media/documentdb-nosql-vs-sql/nosql-vs-sql-overview.png)

El objetivo de este artículo es para ayudarle a conocer las diferencias entre NoSQL y SQL y proporciona una introducción a las ofertas NoSQL y SQL de Microsoft.  

## <a name="when-to-use-nosql"></a>¿Cuándo usar NoSQL?

Imaginemos que desea crear un nuevo sitio de contratación social. Los usuarios pueden crear entradas y agregar imágenes, vídeos y música a ellas. Otros usuarios pueden comentar las publicaciones y proporcionan puntos (Me gusta) para clasificar las publicaciones. La página de inicio tendrá una fuente de publicaciones que pueden compartir e interactuar con los usuarios. 

¿Cómo almacenar los datos? Si ya está familiarizado con SQL, puede empezar a dibujar algo parecido a esto:

![Diagrama de NoSQL vs SQL que muestra el modelo de datos relacionales de un sitio de contratación social](./media/documentdb-nosql-vs-sql/nosql-vs-sql-social.png)

Hasta el momento, vamos bien, pero ahora piense en la estructura de un solo elemento para exponer y mostrarlo. Si desea mostrar la entrada y las imágenes asociadas, audio, vídeo, comentarios, puntos e información de usuario en un sitio Web o la aplicación, tendrá que realizar una consulta con ocho combinaciones de tabla para recuperar el contenido. Ahora imagine una secuencia de publicaciones que cargar y aparecen en la pantalla y puede prever fácilmente que se va a requerir miles de consultas y muchas combinaciones para completar la tarea.

Ahora puede utilizar una solución relacional como SQL Server para almacenar los datos - pero hay una opción de NoSQL que simplifica el enfoque de otra opción. Transformar la publicación en un documento JSON como la siguiente y almacenar en DocumentDB, un servicio de base de datos de documento NoSQL Azure, puede aumentar el rendimiento y recuperar la entrada completa con una consulta y sin combinaciones. Es más sencillo, más sencillo y más eficaz como resultado.

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

Además, estos datos pueden dividirse por Id. de publicación para permitir que los datos escalar naturalmente y aprovechar las ventajas de las características de escala de NoSQL. También NoSQL sistemas permiten a los desarrolladores Afloje coherencia y ofrecer aplicaciones altamente disponibles.  Por último, esta solución no requiere a los desarrolladores definir, administrar y mantener esquema en el nivel de datos, lo que permite la iteración rápida.

A continuación, puede crear en esta solución con otros servicios de Azure:

- [Búsqueda de Azure](https://azure.microsoft.com/services/search/) puede usarse a través de la aplicación web para permitir a los usuarios buscar publicaciones.
- [Servicios de aplicación de Azure](https://azure.microsoft.com/services/app-service/) puede usarse para hospedar aplicaciones y procesos en segundo plano.
- [Almacenamiento de blobs de Windows Azure](https://azure.microsoft.com/services/storage/) puede utilizarse para almacenar los perfiles de usuario completo incluyendo las imágenes.
- [Base de datos de SQL Azure](https://azure.microsoft.com/services/sql-database/) se puede usar para almacenar grandes cantidades de datos como la información de inicio de sesión y los datos para el análisis de uso.
- [Aprendizaje del equipo de Azure](https://azure.microsoft.com/services/machine-learning/) puede usarse para generar conocimientos e inteligencia que puede proporcionar comentarios al proceso y ayudar a ofrecer el contenido correcto para cada usuario.

Este sitio compromiso social es solo un escenario en el que una base de datos NoSQL es el modelo de datos adecuados para el trabajo. Si está interesado en leer más sobre este escenario y cómo los datos del modelo para DocumentDB en las aplicaciones de medios sociales, vea [va sociales con DocumentDB](documentdb-social-media-apps.md). 

## <a name="nosql-vs-sql-comparison"></a>Comparación de NoSQL vs SQL

La siguiente tabla compara las diferencias entre NoSQL y SQL principales. 

![Diagrama de NoSQL vs SQL que muestra cuándo usar NoSQL y cuándo usar SQL. Comparación de NoSQL vs SQL](./media/documentdb-nosql-vs-sql/nosql-vs-sql-comparison.png)

Si una base de datos NoSQL mejor se adapte a sus necesidades, continúe con la siguiente sección para obtener más información sobre los servicios de NoSQL disponibles de Azure. En caso contrario, si una base de datos SQL mejor se adapte a sus necesidades, vaya al [¿Cuáles son las ofertas de Microsoft SQL?](#what-are-the-microsoft-sql-offerings)

## <a name="what-are-the-microsoft-azure-nosql-offerings"></a>¿Cuáles son las ofertas de Microsoft Azure NoSQL?

Azure tiene cuatro servicios NoSQL totalmente administrados: 

- [DocumentDB de Azure](https://azure.microsoft.com/services/documentdb/)
- [Almacenamiento de tablas de Azure](https://azure.microsoft.com/services/storage/)
- [Azure HBase como parte de HDInsight](https://azure.microsoft.com/services/hdinsight/)
- [Caché de Azure Redis](https://azure.microsoft.com/services/cache/)

El siguiente gráfico de comparación de mapas fuera qu para cada servicio. ¿Cuál con más fidelidad describe las necesidades de la aplicación? 

![Diagrama que muestra cuándo usar NoSQL ofertas de Microsoft Azure, incluidos DocumentDB, almacenamiento de tablas, HBase como parte de HDInsight, de NoSQL vs SQL y Redis caché](./media/documentdb-nosql-vs-sql/nosql-vs-sql-documentdb-storage-hbase-hdinsight-redis-cache.png)

Si uno o varios de estos servicios podrían satisfacer las necesidades de la aplicación, obtenga más información con los siguientes recursos: 

- [Ruta de aprendizaje de DocumentDB](https://azure.microsoft.com/documentation/learning-paths/documentdb/) y [DocumentDB casos de uso](documentdb-use-cases.md)
- [Introducción a almacenamiento de tablas de Azure](../storage/storage-dotnet-how-to-use-tables.md)
- [¿Qué es HBase en HDInsight](../hdinsight/hdinsight-hbase-overview.md)
- [Redis caché ruta de aprendizaje](https://azure.microsoft.com/documentation/learning-paths/redis-cache/)

Vaya al [paso siguiente](#next-steps) para obtener información de evaluación gratuita.

## <a name="what-are-the-microsoft-sql-offerings"></a>¿Cuáles son las ofertas de Microsoft SQL?

Microsoft tiene cinco ofertas de SQL: 

- [Base de datos SQL Azure](https://azure.microsoft.com/services/sql-database/)
- [SQL Server en máquinas virtuales de Windows Azure](https://azure.microsoft.com/services/virtual-machines/sql-server/)
- [SQL Server](https://www.microsoft.com/server-cloud/products/sql-server-2016/)
- [Almacén de datos SQL Azure (vista preliminar)](https://azure.microsoft.com/services/sql-data-warehouse/)
- [Sistema de plataforma de análisis (equipo local)](https://www.microsoft.com/en-us/server-cloud/products/analytics-platform-system/)

Si está interesado en SQL Server en una máquina Virtual o una base de datos de SQL, lea [Elija una opción de SQL Server de nube: base de datos de SQL Azure (PaaS) o SQL Server en máquinas virtuales de Azure (IaaS)](../sql-database/sql-database-paas-vs-sql-server-iaas.md) para obtener más información sobre las diferencias entre los dos.

Si SQL parece la mejor opción, a continuación, vaya a [SQL Server](https://www.microsoft.com/server-cloud/products/) para obtener más información sobre lo que nuestros productos de Microsoft SQL y servicios tienen que ofrecer.

A continuación, vaya a [pasos siguientes](#next-steps) gratuitamente vínculos de prueba y evaluación.

## <a name="next-steps"></a>Pasos siguientes

Nos invitar a más información sobre nuestros productos SQL y NoSQL intentando realizar de forma gratuita. 

- Para todos los servicios de Azure, puede iniciar sesión en un [mes de una prueba gratuita](https://azure.microsoft.com/pricing/free-trial/) y recibir 200 $ gastar en cualquiera de los servicios de Azure.
    - [DocumentDB de Azure](https://azure.microsoft.com/services/documentdb/)
    - [Azure HBase como parte de HDInsight](https://azure.microsoft.com/services/hdinsight/)
    - [Caché de Azure Redis](https://azure.microsoft.com/services/cache/)
    - [Almacén de datos SQL Azure (vista preliminar)](https://azure.microsoft.com/services/sql-data-warehouse/)
    - [Base de datos SQL Azure](https://azure.microsoft.com/services/sql-database/)
    - [Almacenamiento de tablas de Azure](https://azure.microsoft.com/services/storage/)

- Puede poner en marcha una [versión de evaluación de SQL Server 2016 en una máquina virtual](https://azure.microsoft.com/marketplace/partners/microsoft/sqlserver2016ctp33evaluationwindowsserver2012r2/) o descargar una [versión de evaluación de SQL Server](https://www.microsoft.com/en-us/evalcenter/evaluate-sql-server-2016).
    - [SQL Server](https://www.microsoft.com/server-cloud/products/sql-server-2016/)
    - [SQL Server en máquinas virtuales de Windows Azure](https://azure.microsoft.com/services/virtual-machines/sql-server/)

