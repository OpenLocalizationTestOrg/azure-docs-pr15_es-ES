<properties 
    pageTitle="Casos de uso común DocumentDB | Microsoft Azure" 
    description="Obtenga más información sobre la parte superior de cinco casos de uso para DocumentDB: contenido, registro de eventos, los datos del catálogo, datos de preferencias de usuario y cosas de Internet (IoT) generados por el usuario." 
    services="documentdb" 
    authors="h0n" 
    manager="jhubbard" 
    editor="monicar" 
    documentationCenter=""/>

<tags 
    ms.service="documentdb" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/28/2016" 
    ms.author="hawong"/>

# <a name="common-documentdb-use-cases"></a>Casos de uso de DocumentDB comunes
En este artículo se proporciona una descripción general de varios casos de uso común para DocumentDB.  Las recomendaciones de este artículo servir como punto de partida al desarrollar su aplicación con DocumentDB.   

Después de leer este artículo, podrá responder a las preguntas siguientes: 
 
- ¿Cuáles son los casos de uso común para DocumentDB?
- ¿Cuáles son las ventajas de usar DocumentDB como un usuario generados por el almacén de contenido?
- ¿Cuáles son las ventajas de usar DocumentDB como datos de catálogo almacena?
- ¿Cuáles son las ventajas de usar DocumentDB como un registro de eventos almacena?
- ¿Cuáles son las ventajas de usar DocumentDB como datos de preferencias de usuario almacena?
- ¿Cuáles son las ventajas de usar DocumentDB como un almacén de datos para sistemas de Internet de cosas (IoT)?

## <a name="common-use-cases-for-documentdb"></a>Casos de uso común para DocumentDB
DocumentDB Azure es una base de datos de NoSQL generales que se usa en una amplia gama de aplicaciones y casos de uso. Es una buena opción para las aplicaciones que necesita tiempos de respuesta de orden de milisegundos baja y necesita escalar rápidamente. Los siguientes son algunos atributos de DocumentDB que hacen que sea adecuada para aplicaciones de alto rendimiento.

- DocumentDB particiones los datos de disponibilidad y escalabilidad de forma nativa.
- DocumentDB tiene almacenamiento SSD copia con tiempos de respuesta de baja latencia orden de milisegundos.
- De DocumentDB niveles de coherencia como final, sesión y antigüedad limitada permite para bajo costo a rendimiento relación. 
- DocumentDB tiene un modelo de datos fáciles precio flexible que metros almacenamiento y el rendimiento de forma independiente.
- Modelo de rendimiento reservadas de DocumentDB le permite pensar en términos de número de lee y escribe en lugar de memoria/CPU/IOPs del hardware subyacente.
- Diseño le permite del DocumentDB que escalar hasta volúmenes de solicitud masiva en el orden de millones de solicitudes por día.

Estos atributos son particularmente útiles cuando aparezca en la web, móvil, juegos y aplicaciones de IoT que necesitan tiempos de respuesta mínimos y controlar grandes cantidades de lectura y escritura. 

## <a name="user-generated-content"></a>Contenido generados por el usuario
Un caso de uso común para DocumentDB es almacenar y contenido (UGC) generados por el usuario de consulta para la web y aplicaciones móviles, medios sociales especialmente.  Algunos ejemplos de UGC son las sesiones de chat, tweets, entradas de blog, clasificaciones y comentarios.  A menudo, la UGC en las aplicaciones de medios sociales es una mezcla de texto de forma libre, propiedades, etiquetas y las relaciones que no están limitadas por estructura rígida.   

Contenido como chats, comentarios y entradas pueden almacenarse en DocumentDB sin necesidad de transformaciones o un objeto complejo a capas de asignación relacional.  Propiedades de datos se pueden agregar o modificar fácilmente para que coincidan con los requisitos según los desarrolladores iteración el código de la aplicación, por tanto, promover el desarrollo rápido.  

Aplicaciones que se integran con varias redes sociales deben responder a cambiar los esquemas de estas redes.  Como los datos se indizan automáticamente de forma predeterminada en DocumentDB, datos están listos para consultar en cualquier momento.  Por lo tanto, estas aplicaciones tienen la flexibilidad para recuperar proyecciones según sus necesidades respectivas.       

Muchas de las aplicaciones sociales ejecutan a escala global y pueden provocar patrones de uso imprevistos.  Flexibilidad en el almacén de datos de ajuste de escala es fundamental como escala de la capa de aplicación para que coincida con la demanda de uso.  Se puede escalar agregando particiones de datos adicionales de una cuenta de DocumentDB.  Además, también puede crear cuentas de DocumentDB adicionales a través de varias áreas. Disponibilidad del área de servicio de DocumentDB, vea [Áreas de Azure](https://azure.microsoft.com/regions/#services).   

## <a name="catalog-data"></a>Catálogo de datos
Escenarios de uso de datos de catálogo implican almacenar y consultar un conjunto de atributos de entidades como personas, lugares y productos.  Algunos ejemplos de catálogo de datos son cuentas de usuario, catálogos de productos, registros de dispositivo de IoT y la lista de sistemas de materiales.  Atributos de datos pueden variar y pueden cambiar a lo largo del tiempo para satisfacer requisitos de la aplicación.  

Considere un ejemplo de un catálogo de productos de un proveedor de componentes para vehículos. Cada elemento puede tener sus propios atributos además de los atributos comunes que comparten todos los elementos.  Además, los atributos de una parte específica pueden cambiar del año siguiente cuando se libera un nuevo modelo.  Como un almacén de documentos JSON, DocumentDB admite los esquemas flexibles y permite representar datos con propiedades anidadas y, por tanto, es adecuado para almacenar los datos del catálogo de productos.       

## <a name="logging-and-time-series-data"></a>Registro de información y datos de la serie de tiempo
Registro de la aplicación con qué frecuencia se emite en grandes volúmenes y pueden basado en la versión de aplicación implementada o los eventos de registro de componente diversos atributos.  Datos del registro no está limitados por relaciones complejas o estructuras rígidas. Cada vez más, los datos del registro se conservan en formato JSON como JSON es fácil y sencillo para el usuario de leer.
   
Normalmente, hay dos casos de uso principales relacionadas con los datos del registro de eventos.  Es el primer caso de uso realizar consultas ad-hoc sobre un subconjunto de datos para solucionar este problema.  Durante la solución de problemas, un subconjunto de datos se primero recupera los registros, normalmente serie de tiempo.  A continuación, se realiza un desglose filtrando el conjunto de datos con los niveles de error o mensajes de error. Esto es donde almacenar los registros de eventos en DocumentDB es una ventaja. Datos de registro almacenados en DocumentDB se indizan automáticamente de forma predeterminada y, por tanto, está listo para consultar en cualquier momento. Además, los datos del registro se pueden conservar en todas las particiones de datos como una serie de tiempo. Los registros antiguos pueden realizarse al almacenamiento fría por la directiva de retención.          

El segundo caso de uso implica largas en ejecución trabajos de análisis de datos sin conexión realizados sobre un gran volumen de datos del registro.  Análisis de disponibilidad del servidor, análisis de errores de aplicación y un análisis de datos son ejemplos de este caso de uso.  Normalmente, se utiliza Hadoop para realizar estos tipos de análisis.  Con el conector de Hadoop para DocumentDB, bases de datos de DocumentDB funcionan como orígenes de datos y receptores para trabajos cerdo, subárbol y/Reduce el mapa. Para obtener detalles sobre el conector de Hadoop para DocumentDB, vea [ejecutar una tarea de Hadoop con DocumentDB y HDInsight](documentdb-run-hadoop-with-hdinsight.md).      

## <a name="gaming"></a>Juegos
El nivel de base de datos es un componente crucial de aplicaciones de juegos. Los juegos modernos realizan procesamiento gráfica en los clientes móviles o consola, pero se basan en la nube para proporcionar contenido personalizadas como estadísticas del juego, integración de medios sociales y tablas de líderes de puntuación. Juegos requieren muy baja latencia para lecturas escribe para proporcionar un atractivos experiencia del juego y el nivel de la base de datos que necesita administrar máximos y mínimos en tasas de solicitud durante lanzamientos juego nuevos y actualizaciones de características.

DocumentDB se utiliza en los juegos de escala masiva como [el recorrido inactivas: tierra de un hombre sin](https://azure.microsoft.com//blog/the-walking-dead-no-mans-land-game-soars-to-1-with-azure-documentdb/) por [Juegos siguiente](http://www.nextgames.com/), y [Halo 5: tutores](https://azure.microsoft.com/blog/how-halo-5-guardians-implemented-social-gameplay-using-azure-documentdb/). En ambos casos de uso, las principales ventajas de DocumentDB son las siguientes:

- DocumentDB permite rendimiento escalar hacia arriba o abajo elástica. Esto permite juegos controlen actualización perfil y las estadísticas de decenas de millones de jugadores simultáneas realizando una sola llamada API.
- DocumentDB admite milisegundos lee y escribe ayudar a evitar los retrasos durante el juego.
- La indización automática de DocumentDB permite filtrar contra varias propiedades diferentes en tiempo real, por ejemplo, busque reproductores por su identificadores, el Reproductor interno o sus GameCenter, Facebook, identificadores de Google o consulta basada en la pertenencia a Reproductor un guild. Esto es posible sin generar indización compleja o infraestructura de sharding.
- Características sociales, incluidos los mensajes de chat en juego, pertenencias a grupos de Reproductor guild, retos completados, tablas de líderes de puntuación y gráficos sociales son más fáciles de implementar con un esquema flexible.
- DocumentDB como un administrado plataforma como-servicio (PaaS) requiere administración configuración mínima trabajar para permitir la iteración rápida y reducir el tiempo en el mercado.


## <a name="user-preferences-data"></a>Datos de preferencias de usuario
En la actualidad, más moderna web y aplicaciones móviles vienen con experiencias y vistas complejas. Estas vistas y experiencias son normalmente dinámicos, cocina a preferencias de usuario o humor y personalización necesidades.  Por lo tanto, las aplicaciones necesitan poder recuperar configuración personalizada de un modo eficaz para representar los elementos de interfaz de usuario y experiencias rápidamente. 

JSON es un formato eficaz para representar datos de diseño de la interfaz de usuario tal y como no solo es ligera, pero también puede interpretar fácilmente JavaScript.  DocumentDB ofrece niveles de coherencia ajustables que permiten lecturas rápidas con baja latencia escribe. Por lo tanto, almacenar los datos de diseño de interfaz de usuario incluidos configuración personalizada como documentos JSON en DocumentDB es una forma eficaz de obtener estos datos a través de la conexión.

## <a name="iot-and-device-sensor-data"></a>Datos de sensor IoT y dispositivo
Casos de uso de IoT comúnmente compartan algunos patrones en cómo recopilar, procesar y almacenar datos.  En primer lugar, estos sistemas permiten la entrada de datos que puede recopilar ráfagas de datos de sensores de dispositivo de varios idiomas.  A continuación, estos sistemas procesan y analizar datos streaming para obtener perspectivas en tiempo real. Y última pero no menos importante, más si no todos los datos se colocarán finalmente en un almacén de datos para análisis ad hoc de consultas y sin conexión.    

Microsoft Azure ofrece servicios enriquecidos que pueden aprovecharse para IoT casos de uso.  Servicios de Azure IoT son un conjunto de servicios incluidos Azure evento Hubs, DocumentDB de Azure, análisis de secuencia de Azure, concentrador de notificación de Azure, el aprendizaje Azure, HDInsight de Azure y PowerBI. 

Ráfagas de datos pueden ser ingeridos por Hubs de evento de Azure que ofrece recopilación de datos de alto rendimiento con una baja latencia. Datos ingeridos que deben procesarse para una perspectiva en tiempo real se pueden dirigidas a análisis de secuencia de Azure para análisis en tiempo real. Datos se pueden cargar en DocumentDB para realizar consultas ad hoc. Una vez que los datos se cargan en DocumentDB, están listos consultar los datos.  Los datos de DocumentDB pueden utilizarse como datos de referencia como parte del análisis en tiempo real. Además, datos pueden ser refinados y procesados mediante una conexión de datos de DocumentDB a HDInsight para trabajos cerdo, sección o/Reduce el mapa.  A continuación, se cargan datos refinados volver a DocumentDB para los informes.   

Para una solución IoT de ejemplo con DocumentDB, EventHubs y tormenta, vea el [repositorio de ejemplos de tormenta hdinsight de GitHub](https://github.com/hdinsight/hdinsight-storm-examples/).

Para obtener más información sobre las ofertas de Azure para IoT, vea [crear la Internet de las cosas](http://www.microsoft.com/en-us/server-cloud/internet-of-things.aspx).

## <a name="next-steps"></a>Pasos siguientes
 
Para empezar con DocumentDB, puede crear una [cuenta](https://azure.microsoft.com/pricing/free-trial/) y siga la [ruta de aprendizaje](https://azure.microsoft.com/documentation/learning-paths/documentdb/) para obtener información sobre DocumentDB y encontrar la información que necesita. 

O bien, si desea obtener más información sobre los clientes que utilicen DocumentDB, están disponibles los siguientes casos de clientes:

- [Juegos siguientes](https://azure.microsoft.com//blog/the-walking-dead-no-mans-land-game-soars-to-1-with-azure-documentdb/). Los problemas de entrega Paseo: un hombre de tierra juego se eleva a #1 compatible con DocumentDB de Azure.
- [Halo](https://azure.microsoft.com/blog/how-halo-5-guardians-implemented-social-gameplay-using-azure-documentdb/). ¿Cómo Halo 5 había implementado juego social con DocumentDB de Azure.
- [Galería de análisis de Cortana](https://azure.microsoft.com/blog/cortana-analytics-gallery-a-scalable-community-site-built-on-azure-documentdb/). Galería de análisis de Cortana: un sitio de la Comunidad scalable integrado en DocumentDB de Azure.
- [Sea sencillo](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=18602). Llevando integración, proporciona una perspectiva Global de las empresas multinacionales en minutos con las tecnologías de nube Flexible.
- [República de noticias](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=18639). Agregar inteligencia a las noticias para proporcionar información con el fin de ciudadanos activados. 
- [Grupos de seguridad internacional](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=18653). Color coherente en todo el mundo, las principales marcas activar a grupos de seguridad. Y grupos de seguridad se convierte en Azure.
- [Telenor](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=18608). Líder global Telenor usa la nube para desplazarse a la velocidad de un inicio. 
- [XOMNI](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=18667). El almacén del futuro se ejecuta en búsqueda rápida y fácil flujo de datos.
