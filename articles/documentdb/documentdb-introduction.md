<properties 
    pageTitle="Introducción a DocumentDB, una base de datos JSON | Microsoft Azure" 
    description="Obtenga más información sobre DocumentDB de Azure, una base de datos JSON NoSQL. Esta base de datos del documento se ha creado para datos grandes, escalabilidad elástica y alta disponibilidad." 
    keywords="base de datos JSON, base de datos de documento"
    services="documentdb" 
    authors="mimig1" 
    manager="jhubbard" 
    editor="monicar" 
    documentationCenter=""/>

<tags 
    ms.service="documentdb" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="get-started-article" 
    ms.date="09/13/2016" 
    ms.author="mimig"/>

# <a name="introduction-to-documentdb-a-nosql-json-database"></a>Introducción a DocumentDB: una base de datos JSON NoSQL

##<a name="what-is-documentdb"></a>¿Qué es DocumentDB?

DocumentDB es un servicio de base de datos de NoSQL administrado totalmente integrado para rendimiento rápido y predecible, alta disponibilidad, escala elástico, distribución global y facilitar el desarrollo. Como una base de datos de la NoSQL libre de esquema, DocumentDB ofrece capacidades de consulta SQL enriquecidas y familiares con latencia baja coherente en datos JSON - garantizar que se sirven 99% de su lecturas en 10 milisegundos y 99% de la escritura se proporcionan en 15 milisegundos. Estas hacen beneficios únicos DocumentDB un buen ajustar para web, móvil, juegos y IoT y muchas otras aplicaciones que necesitan escala transparente y una réplica global.

## <a name="how-can-i-learn-about-documentdb"></a>¿Cómo puedo obtener información acerca de DocumentDB? 

Una forma rápida de obtener información sobre DocumentDB y véalo en acción es seguir estos tres pasos: 

1. Ver los dos minutos [¿Qué es DocumentDB?](https://azure.microsoft.com/documentation/videos/what-is-azure-documentdb/) vídeo, que presenta las ventajas de usar DocumentDB.
2. Ver el minuto tres [DocumentDB crear en Azure](https://azure.microsoft.com/documentation/videos/create-documentdb-on-azure/) vídeo, que resalta cómo empezar a trabajar con DocumentDB mediante el Portal de Azure.
3. Visite la [Animación de la consulta](http://www.documentdb.com/sql/demo), donde puede recorrer las diferentes actividades para obtener información sobre la funcionalidad de consultas enriquecida disponible en DocumentDB. A continuación, vaya sobre a la pestaña de espacio aislado y ejecutar consultas SQL personalizadas y experimentar con DocumentDB.

A continuación, vuelva a este artículo, donde se le profundizar.  

## <a name="what-capabilities-and-key-features-does-documentdb-offer"></a>¿Qué son las funciones y características clave ofrece DocumentDB?  

Azure DocumentDB ofrece las siguientes capacidades claves y ventajas:

-   **Rendimiento elástica scalable y almacenamiento:** Fácilmente escalar hacia arriba o hacia abajo de la base de datos JSON DocumentDB a sus necesidades de la aplicación. Los datos se almacenan en discos de estado sólido (SSD) para baja latencia predecible. DocumentDB es compatible con contenedores para almacenar datos JSON denominadas colecciones que pueden escalar a tamaños de prácticamente ilimitada de almacenamiento y el rendimiento de preparación. Puede elástica escalar DocumentDB con un rendimiento predecible sin problemas a medida que crece la aplicación. 

-   **Replicación de múltiples región:** DocumentDB aplica transparente los datos a todas las regiones que ha asociada a su cuenta de DocumentDB, lo que le permite desarrollar aplicaciones que requieren acceso global a los datos mientras proporciona compensaciones entre coherencia, disponibilidad y rendimiento, todas las garantías correspondientes. DocumentDB proporciona transparente migración tras error regional con las API de varias direcciones IP y la capacidad de escalar elástica rendimiento y almacenamiento en todo el mundo. Obtenga información acerca de en más [distribuir datos globalmente con DocumentDB](documentdb-distribute-data-globally.md).

-   **Consultas Ad hoc con sintaxis SQL familiar:** Almacenar documentos JSON heterogéneos dentro de DocumentDB y estos documentos a través de una sintaxis SQL familiar de la consulta. DocumentDB utiliza un bloqueo muy simultáneo, libre, registro estructurado tecnología de indización indizar automáticamente todo el contenido de documento. Esto permite enriquecidas consultas en tiempo real sin necesidad de especificar sugerencias de esquema, índices secundarios o vistas. Más información en [DocumentDB de consulta](documentdb-sql-query.md). 

-   **Ejecución de JavaScript dentro de la base de datos:** Expresar lógica de la aplicación como procedimientos almacenados, desencadenadores y funciones definidas por el usuario (UDF) con JavaScript estándar. Esto permite la lógica de su aplicación trabajar con datos sin preocuparse por la no coinciden entre la aplicación y el esquema de la base de datos. DocumentDB proporciona la ejecución completa transacciones de lógica de la aplicación de JavaScript directamente dentro del motor de base de datos. La integración de JavaScript permite la ejecución de insertar, reemplazar, eliminar y seleccionadas operaciones desde un programa de JavaScript como una transacción aislada. Más información en la [programación de servidor DocumentDB](documentdb-programming.md).

-   **Niveles de coherencia ajustables:** Seleccione de cuatro niveles de coherencia bien definido para lograr un equilibrio óptimo entre coherencia y rendimiento. Para las consultas y las operaciones de lectura, DocumentDB ofrece cuatro niveles de coherencia distinct: sesión seguro, antigüedad limitada y final. Estos niveles de coherencia granulares, bien definidas permiten realizar sonidos compensaciones entre coherencia, la disponibilidad y la latencia. Más información de [uso de los niveles de coherencia para maximizar la disponibilidad y rendimiento de DocumentDB](documentdb-consistency-levels.md).

-   **Totalmente administrados:** Eliminar la necesidad de administrar los recursos de base de datos y el equipo. Como un servicio de Microsoft Azure completamente administrado, no necesita administrar máquinas virtuales, implementar y configurar el software, administrar escala o tratar con las actualizaciones de nivel de datos complejos. Cada base de datos de copia de seguridad y protegida contra errores regionales automáticamente. Puede agregar una cuenta de DocumentDB fácilmente y aprovisionar capacidad como sea necesario, lo que le permite centrarse en la aplicación en lugar de funcionamiento y administración de la base de datos. 

-   **Abiertos por diseño:** Introducción rápida mediante herramientas y habilidades existentes. Programación de DocumentDB es sencillo, accesible y no es necesario adoptar las nuevas herramientas o siga extensiones personalizadas a JSON o JavaScript. Puede obtener acceso a todas las funciones de base de datos incluidos CRUD, consulta y JavaScript procesar a través de una interfaz REST HTTP simple. DocumentDB adopta formatos existentes, los idiomas y los estándares y ofrecer alto valor capacidades de base de datos sobre ellos.

-   **La indización automática:** De forma predeterminada, DocumentDB [índices automáticamente](documentdb-indexing.md) todos los documentos de la base de datos y no esperan o requieren cualquier esquema o la creación de índices secundarias. ¿No desea indizar todo? No se preocupe, puede [optar por rutas de acceso en los archivos JSON](documentdb-indexing-policies.md) demasiado.

##<a name="data-management"></a>¿Cómo administrar DocumentDB los datos?

Azure DocumentDB administra datos JSON a través de recursos de base de datos bien definido. Estos recursos se duplican para alta disponibilidad y exclusivamente accesible por su URI lógico. DocumentDB ofrece que una sencilla HTTP según modelo de programación REST para todos los recursos. 

La cuenta de la base de datos de DocumentDB es un espacio de nombres único que le proporciona acceso a DocumentDB de Azure. Antes de crear una cuenta de base de datos, debe tener una suscripción de Azure, que le proporciona acceso a una amplia variedad de servicios de Azure. 

Todos los recursos DocumentDB se modelados y se almacenan como documentos JSON. Administrar los recursos como elementos, que son JSON documentos que contienen metadatos y como fuentes que son colecciones de elementos. Conjuntos de elementos dentro de sus respectivos fuentes.

La imagen siguiente muestra las relaciones entre los recursos de DocumentDB:

![La relación jerárquica entre recursos en DocumentDB, una base de datos JSON NoSQL][1] 

Una cuenta de base de datos consta de un conjunto de bases de datos, cada una con varias colecciones, cada una de las cuales puede contener procedimientos almacenados, desencadenadores, UDF, documentos y datos adjuntos relacionados. Una base de datos también tiene asociados los usuarios, cada una con un conjunto de permisos para tener acceso a varias otras colecciones, procedimientos almacenados, desencadenadores, UDF, documentos o datos adjuntos. Mientras las bases de datos, los usuarios, permisos y colecciones son recursos definidos por el sistema con esquemas conocidos, documentos, procedimientos almacenados, desencadenadores, UDF y datos adjuntos contengan arbitrario, definidas por el usuario contenido JSON.  

##<a name="develop"></a>¿Cómo puedo desarrollar aplicaciones con DocumentDB?

Azure DocumentDB expone recursos a través de una API de REST puede llamar a cualquier idioma capaz de realizar las solicitudes HTTP/HTTPS. Además, DocumentDB ofrece bibliotecas de programación para varios idiomas populares. Estas bibliotecas simplifican muchos aspectos del trabajo con Azure DocumentDB controlando detalles como la dirección de almacenamiento en caché, administración de excepciones, reintentos automáticos y así sucesivamente. Bibliotecas ya están disponibles para las plataformas y los idiomas siguientes:  

Descargar | Documentación
--- | ---
[.NET SDK](http://go.microsoft.com/fwlink/?LinkID=402989) | [Biblioteca de .NET](https://msdn.microsoft.com/library/azure/dn948556.aspx)
[Node.js SDK](http://go.microsoft.com/fwlink/?LinkID=402990) | [Biblioteca de Node.js](http://azure.github.io/azure-documentdb-node/)
[Java SDK](http://go.microsoft.com/fwlink/?LinkID=402380) | [Biblioteca de Java](http://azure.github.io/azure-documentdb-java/)
[SDK de JavaScript](http://go.microsoft.com/fwlink/?LinkID=402991) | [Biblioteca de JavaScript](http://azure.github.io/azure-documentdb-js/)
n/a. | [SDK de JavaScript del servidor](http://azure.github.io/azure-documentdb-js-server/)
[Python SDK](https://pypi.python.org/pypi/pydocumentdb) | [Biblioteca de Python](http://azure.github.io/azure-documentdb-python/)

Más allá básico crear, leer, actualizar y eliminar operaciones, DocumentDB proporciona una interfaz de consultas SQL enriquecida para recuperar documentos JSON y del servidor de soporte técnico para la ejecución de transacciones de la lógica de la aplicación de JavaScript. Las interfaces de ejecución de consultas y secuencias de comandos están disponibles a través de todas las bibliotecas de plataforma, así como las API de REST. 

### <a name="sql-query"></a>Consulta SQL
Azure DocumentDB es compatible con consultar documentos usando un lenguaje de SQL que se basa en el sistema de tipos de JavaScript y expresiones con soporte de consultas relacionales, jerárquicas y espaciales. El lenguaje de consulta DocumentDB es una interfaz sencilla pero efectiva a documentos JSON de consulta. El idioma es compatible con un subconjunto de la gramática de ANSI SQL y agrega la integración de objeto JavaScript, matrices, construcción de objetos y una llamada a función. DocumentDB proporciona su modelo de consulta sin ningún esquema explícito o sugerencias de indización del desarrollador.

Funciones definidas por el usuario (UDF) puede registradas con DocumentDB y se hace referencia como parte de una consulta SQL, con lo que se extiende la gramática para admitir lógica de aplicación. Estos UDF se escriben como programas de JavaScript y se ejecutan dentro de la base de datos. 

Para desarrolladores. NET, DocumentDB también ofrece un proveedor de consultas LINQ como parte del [SDK de .NET](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.linq.aspx). 

### <a name="transactions-and-javascript-execution"></a>Las transacciones y ejecución de JavaScript
DocumentDB le permite escribir lógica de la aplicación como con nombre programas escritos en JavaScript. Estos programas registrados para una colección y pueden emitir operaciones de base de datos en los documentos dentro de una colección. Puede registrar JavaScript para la ejecución como un desencadenador, el procedimiento almacenado o la función definida por el usuario. Procedimientos almacenados y desencadenadores pueden crear, leer, actualizar y eliminar documentos que ejecutan funciones definidas por el usuario como parte de la lógica de ejecución de consulta sin acceso de escritura a la colección.

Ejecución de JavaScript dentro de DocumentDB se basa los conceptos compatibles con los sistemas de bases de datos relacionales, con JavaScript como reemplazo moderna de Transact-SQL. Toda la lógica de JavaScript se ejecuta dentro de una transacción ACID ambiente con aislamiento de instantánea. Durante el curso de su ejecución, si el código JavaScript inicia una excepción, se anula toda la transacción.

## <a name="next-steps"></a>Pasos siguientes
¿Ya tiene una cuenta de Azure? A continuación, puede empezar a trabajar con DocumentDB en el [Portal de Azure](https://portal.azure.com/#gallery/Microsoft.DocumentDB) mediante la [creación de una cuenta de base de datos de DocumentDB](documentdb-create-account.md).

¿No tiene una cuenta de Azure? Puedes:

- Suscribirse a una [prueba gratuita de Azure](https://azure.microsoft.com/free/), lo cual proporciona 30 días y 200 $ para probar los servicios de Azure. 
- Si tiene una suscripción a MSDN, tiene derecho a [150 dólares en créditos de Azure gratuitas al mes](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) usar en cualquier servicio de Azure. 

A continuación, cuando esté listo para obtener más información, visite nuestra [ruta de aprendizaje](https://azure.microsoft.com/documentation/learning-paths/documentdb/) para desplazarse por todos los recursos de aprendizaje disponibles. 


[1]: ./media/documentdb-introduction/json-database-resources1.png
 
