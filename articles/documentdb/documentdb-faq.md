<properties 
    pageTitle="Preguntas de base de datos de DocumentDB - preguntas más frecuentes | Microsoft Azure" 
    description="Obtenga respuestas a preguntas más frecuentes sobre DocumentDB de Azure en un servicio de base de datos de documento NoSQL para JSON. Responder a preguntas de la base de datos sobre capacidad, niveles de rendimiento y ajuste de escala." 
    keywords="Preguntas de la base de datos, preguntas más frecuentes, documentdb, azure, Microsoft azure"
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
    ms.topic="article" 
    ms.date="10/03/2016" 
    ms.author="mimig"/>


#<a name="frequently-asked-questions-about-documentdb"></a>Preguntas más frecuentes sobre DocumentDB

## <a name="database-questions-about-microsoft-azure-documentdb-fundamentals"></a>Preguntas de la base de datos sobre los conceptos básicos de Microsoft Azure DocumentDB

### <a name="what-is-microsoft-azure-documentdb"></a>¿Qué es Microsoft Azure DocumentDB? 
Microsoft Azure DocumentDB es un increíbles rápida, escala del mundo NoSQL documento base de datos como-servicio que ofrece consultas avanzadas sobre datos libres de esquema, ayuda a proporcionar configurable y fiable el rendimiento y permite el desarrollo rápido, todo mediante una plataforma administrada copia por la potencia y alcance de Microsoft Azure. DocumentDB es la solución adecuada para juegos portátiles, de web, y aplicaciones de IoT cuando el rendimiento predecible, disponibilidad alta, baja latencia y un modelo de datos sin esquema son claves requisitos. DocumentDB ofrece flexibilidad de esquema y enriquecido indización a través de un modelo de datos JSON nativo e incluye compatibilidad con transacciones de varios documento con JavaScript integrado.  
  
Para más preguntas de la base de datos, respuestas e instrucciones sobre cómo implementar y utilizar este servicio, consulte la [página de la documentación de DocumentDB](https://azure.microsoft.com/documentation/services/documentdb/).

### <a name="what-kind-of-database-is-documentdb"></a>¿Qué tipo de base de datos es DocumentDB?
DocumentDB es una NoSQL documento orientados a base de datos que almacena datos en formato JSON.  DocumentDB admite estructuras anidadas automáticamente contained datos que se pueden consultar a través de un DocumentDB enriquecido [gramática de consultas SQL](documentdb-sql-query.md). DocumentDB ofrece transacciones de procesamiento del lado servidor JavaScript a través de [procedimientos almacenados, desencadenadores y las funciones definidas por el usuario](documentdb-programming.md)de alto rendimiento. La base de datos también es compatible con los niveles de coherencia ajustables programador con asociados [niveles de rendimiento](documentdb-performance-levels.md).
 
### <a name="do-documentdb-databases-have-tables-like-a-relational-database-rdbms"></a>¿DocumentDB bases de datos tienen tablas como una base de datos relacional (RDBMS)?
No, DocumentDB almacena los datos en las colecciones de documentos JSON.  Para obtener información sobre recursos DocumentDB, vea [conceptos y modelo de recursos de DocumentDB](documentdb-resources.md). Para obtener más información acerca de cómo soluciones NoSQL como DocumentDB difieren relacionales soluciones, vea [NoSQL vs SQL](documentdb-nosql-vs-sql.md).

### <a name="do-documentdb-databases-support-schema-free-data"></a>¿DocumentDB bases de datos compatibles con datos sin esquema?
Sí, DocumentDB permite que las aplicaciones almacenar los documentos JSON arbitrarios sin sugerencias o la definición de esquema. Datos están inmediatamente disponibles para la consulta mediante la interfaz de consulta SQL DocumentDB.   

### <a name="does-documentdb-support-acid-transactions"></a>¿DocumentDB admite transacciones ACID?
Sí, DocumentDB admite transacciones entre documentos expresadas como desencadenadores y procedimientos almacenado de JavaScript. Ámbito de una sola partición dentro de cada colección y ejecuta con semántica ACID como todas las transacciones o nada aislado de otras solicitudes de usuario y el código de ejecución de forma simultánea.  Si se producen excepciones a través de la ejecución del lado servidor del código de la aplicación de JavaScript, se deshace toda la transacción. Para obtener más información sobre las transacciones, consulte [las transacciones del sistema de base de datos](documentdb-programming.md#database-program-transactions).

### <a name="what-are-the-typical-use-cases-for-documentdb"></a>¿Cuáles son los casos de uso típico de DocumentDB?  
DocumentDB es una buena opción para la nueva web, móvil, para juegos y aplicaciones IoT donde escala automático, performance predecible, rápido orden milisegundos tiempos de respuesta y la capacidad de consultar datos libres de esquema es importante. DocumentDB permite el desarrollo rápido y la iteración continua de modelos de datos de aplicación de servicio de soporte. Aplicaciones que administración contenido generados por el usuario y los datos son [los casos de uso común para DocumentDB](documentdb-use-cases.md).  

### <a name="how-does-documentdb-offer-predictable-performance"></a>¿Cómo DocumentDB ofrece performance predecible?
Una [unidad de solicitud](documentdb-request-units.md) es la medida de rendimiento en DocumentDB. 1 RU se corresponde con el rendimiento de la forma de obtener un documento de 1KB. Cada operación en DocumentDB, incluidos lee, escribe, consultas SQL y ejecuciones del procedimiento almacenado tiene un valor de RU determinista basado en el rendimiento necesario para completar la operación. En lugar de pensar CPU, IO y memoria y cómo cada afectan el rendimiento de la aplicación, puede considerar en términos de una medida RU.

Puede reservar cada colección DocumentDB con el rendimiento de preparación en términos de RUs de rendimiento por segundo. Para las aplicaciones de cualquier escala, puede evaluar solicitudes individuales para medir sus valores RU y aprovisionar colecciones controlen la suma total de unidades de solicitud en todas las solicitudes. También puede ampliar o ajustar el rendimiento de la colección como las necesidades de su evolucionar de aplicación. Para obtener más información acerca de las unidades de la solicitud y para obtener ayuda para determinar la colección de necesita, lea [Administrar rendimiento y la capacidad](documentdb-manage.md) y pruebe la [Calculadora de rendimiento](https://www.documentdb.com/capacityplanner). 

### <a name="is-documentdb-hipaa-compliant"></a>¿Es compatible con DocumentDB HIPAA?
Sí, DocumentDB es compatible con HIPAA. HIPAA establece los requisitos de uso, divulgación y protección de la información de estado identificable individualmente. Para obtener más información, consulte el [Centro de confianza de Microsoft](https://www.microsoft.com/en-us/TrustCenter/Compliance/HIPAA).

### <a name="what-are-the-storage-limits-of-documentdb"></a>¿Cuáles son los límites de almacenamiento de DocumentDB? 
No hay ningún límite teórico a la cantidad total de datos que puede almacenar una colección de DocumentDB. Si desea almacenar más de 250 GB de datos de una sola colección, por favor, [póngase en contacto con soporte técnico](documentdb-increase-limits.md) para tener mayor la cuota de la cuenta. 

### <a name="what-are-the-throughput-limits-of-documentdb"></a>¿Cuáles son los límites de rendimiento de DocumentDB? 
No hay ningún límite teórico a la cantidad total de rendimiento que admite una colección de DocumentDB, si la carga de trabajo puede distribuirse aproximadamente uniformemente entre un número suficientemente grande de claves de partición. Si desea supere solicitud 250.000 unidades por segundo por colección o cuenta, por favor, [póngase en contacto con soporte técnico](documentdb-increase-limits.md) para tener mayor la cuota de la cuenta. 

### <a name="how-much-does-microsoft-azure-documentdb-cost"></a>¿Cuánto cuesta DocumentDB de Microsoft Azure?
Consulte la página de [Detalles de precios DocumentDB](https://azure.microsoft.com/pricing/details/documentdb/) para obtener más detalles. Cargos de uso DocumentDB determinan el número de colecciones de uso, el número de horas que estaban conectadas, las colecciones de almacenamiento y el rendimiento de preparación para cada colección. 

### <a name="is-there-a-free-account-available"></a>¿Hay una cuenta gratuita disponible?
Si es nuevo en Azure, puede registrarse para una [cuenta gratuita de Azure](https://azure.microsoft.com/free/), lo cual proporciona 30 días y 200 $ para probar los servicios de Azure. O bien, si tiene una suscripción de Visual Studio, son elegibles para [150 dólares en créditos de Azure gratuitas al mes](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) usar en cualquier servicio de Azure.  

### <a name="how-can-i-get-additional-help-with-documentdb"></a>¿Cómo puedo obtener ayuda adicional con DocumentDB?
Si necesita ayuda, vuelva a contactar con nosotros en [Desbordamiento de pila](http://stackoverflow.com/questions/tagged/azure-documentdb), los [Foros para desarrolladores de MSDN Azure DocumentDB](https://social.msdn.microsoft.com/forums/azure/home?forum=AzureDocumentDB), o programar una [Charla 1:1 con el equipo de ingeniería DocumentDB](http://www.askdocdb.com/). Para mantenerse actualizado sobre las últimas noticias de DocumentDB y características, siga en [Twitter](https://twitter.com/DocumentDB).

## <a name="set-up-microsoft-azure-documentdb"></a>Configurar Microsoft Azure DocumentDB

### <a name="how-do-i-sign-up-for-microsoft-azure-documentdb"></a>¿Cómo puedo realizar una suscripción de Microsoft Azure DocumentDB?
Microsoft Azure DocumentDB está disponible en el [Portal de Azure][azure-portal].  En primer lugar debe registrarse para una suscripción de Microsoft Azure.  Una vez que se suscribe a una suscripción de Microsoft Azure, puede agregar una cuenta de DocumentDB a su suscripción de Azure. Para obtener instrucciones sobre cómo agregar una cuenta de DocumentDB, vea [crear una cuenta de base de datos de DocumentDB](documentdb-create-account.md).   

### <a name="what-is-a-master-key"></a>¿Qué es una clave principal?
Una clave principal es un token de seguridad para tener acceso a todos los recursos para una cuenta. Personas que trabajan con la clave tienen lectura y escritura acceso a los recursos de todos los de la cuenta de base de datos. Tenga cuidado al distribuir claves principales. La clave principal de patrón y patrón secundario están disponibles en el módulo de **teclas **del [Portal de Azure][azure-portal]. Para obtener más información acerca de las teclas, vea [Ver, copiar y las teclas de acceso regenerar](documentdb-manage-account.md#keys).

### <a name="how-do-i-create-a-database"></a>¿Cómo puedo crear una base de datos?
Puede crear bases de datos con el [Portal de Azure]() tal como se describe en [crear una base de datos DocumentDB](documentdb-create-database.md), uno de los [SDK DocumentDB](documentdb-sdk-dotnet.md)o a través de la [API de REST](https://msdn.microsoft.com/library/azure/dn781481.aspx).  

### <a name="what-is-a-collection"></a>¿Qué es una colección de?
Una colección es un contenedor de documentos JSON y la lógica de la aplicación de JavaScript asociada. Una colección es una entidad facturable, en el que se determina el [costo](documentdb-performance-levels.md) por el rendimiento y storaged. Colecciones pueden abarcar uno o más particiones o servidores y pueden escalar para manejar volúmenes prácticamente ilimitados de almacenamiento o el rendimiento.

Colecciones también son las entidades de facturación para DocumentDB. Cada colección se cobra por hora basada en el rendimiento de la preparación y el espacio de almacenamiento. Para obtener más información, consulte [DocumentDB de precios](https://azure.microsoft.com/pricing/details/documentdb/).  

### <a name="how-do-i-set-up-users-and-permissions"></a>Configuración de usuarios y permisos
Puede crear usuarios y permisos mediante uno de los [SDK DocumentDB](documentdb-sdk-dotnet.md) o mediante las [API de REST](https://msdn.microsoft.com/library/azure/dn781481.aspx).   

## <a name="database-questions-about-developing-against-microsoft-azure-documentdb"></a>Preguntas de la base de datos sobre el desarrollo con Microsoft Azure DocumentDB

### <a name="how-to-do-i-start-developing-against-documentdb"></a>¿Cómo para hacer el inicio del desarrollo contra DocumentDB?
[SDK](documentdb-sdk-dotnet.md) están disponibles para .NET, Python, Node.js, JavaScript y Java.  También pueden aprovechar las [API de REST HTTP](https://msdn.microsoft.com/library/azure/dn781481.aspx) para interactuar con los recursos de DocumentDB desde una gran variedad de idiomas y plataformas. 

Ejemplos de DocumentDB [.NET](documentdb-dotnet-samples.md), [Java](https://github.com/Azure/azure-documentdb-java), [Node.js](documentdb-nodejs-samples.md)y [Python](documentdb-python-samples.md) SDK están disponibles en GitHub.

### <a name="does-documentdb-support-sql"></a>¿DocumentDB admite SQL?
Lenguaje de consulta SQL DocumentDB es un subconjunto mejorado de la funcionalidad de consulta compatible con SQL. Lenguaje de consulta SQL DocumentDB proporciona a enriquecido jerárquico y operadores relacionales y capacidad de ampliación a través de usuario en función de JavaScript definen funciones (UDF). Gramática JSON permite modelar documentos JSON como árboles con etiquetas como los nodos de árbol, que utiliza las técnicas de indización automáticas de DocumentDB así como el lenguaje de consulta SQL de DocumentDB.  Para obtener más información sobre cómo usar la gramática SQL, consulte la [Consulta DocumentDB] [ query] artículo.

### <a name="what-are-the-data-types-supported-by-documentdb"></a>¿Cuáles son los tipos de datos admitidos por DocumentDB?
Los tipos de datos simples compatibles con DocumentDB son los mismos que JSON. JSON tiene un sistema de tipo simple que consta de cadenas, los números (IEEE754 de precisión doble) y booleanos - verdaderos, falso y valores nulos.  Tipos de datos más complejos como DateTime, Guid, Int64 y geometría se pueden representar en JSON y DocumentDB mediante la creación de objetos anidados usando el operador {} y las matrices con el operador []. 

### <a name="how-does-documentdb-provide-concurrency"></a>¿Cómo DocumentDB proporciona simultaneidad?
DocumentDB es compatible con el control de simultaneidad optimista (OCC) a través de HTTP entidad etiquetas o etags. Todos los recursos DocumentDB tiene un valor etag y etag se establece en el servidor cada vez que se actualice un documento. El encabezado etag y el valor actual se incluyen en todos los mensajes de respuesta. ETags puede usarse con el encabezado de coincidencia If para permitir que el servidor decidir si debe actualizarse un recurso. El valor de coincidencia If es el valor de etag revisarse contra. Si el valor de etag coincide con el valor de etag de servidor, se actualizará el recurso. Si el valor etag ya no está actualizado, el servidor rechaza la operación con un "HTTP 412 Error de condición previa" código de respuesta. El cliente tendrá que recuperar el recurso para obtener el valor actual de etag para el recurso de nuevo. Además, puede usarse etags con encabezado If lo ninguna coincidencia para determinar si se necesita la opción capturar volver a un recurso. 

Para usar simultaneidad optimista en .NET, use la clase [AccessCondition](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.accesscondition.aspx) . Un ejemplo de .NET, consulte [Program.cs](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/code-samples/DocumentManagement/Program.cs) en la muestra DocumentManagement en github.

### <a name="how-do-i-perform-transactions-in-documentdb"></a>¿Cómo se puede realizar transacciones en DocumentDB?
DocumentDB admite transacciones integrados de idioma mediante procedimientos almacenado de JavaScript y desencadenadores. Todas las operaciones de base de datos dentro de los scripts se ejecutan en el ámbito de la colección si es una colección única partición, o documentos con el mismo valor de clave de partición dentro de una colección, si se divide la colección de aislamiento de instantánea. Una instantánea de las versiones del documento (ETags) es tomada en el inicio de la transacción y confirmada solo si se realiza correctamente la secuencia de comandos. Si el código JavaScript, produce un error, se deshace la transacción. Para obtener más detalles, consulte [programación de servidor DocumentDB](documentdb-programming.md) .

### <a name="how-can-i-bulk-insert-documents-into-documentdb"></a>¿Cómo puedo masiva insertar documentos en DocumentDB? 
Hay tres formas de insertar documentos de forma masiva en DocumentDB:

- La herramienta de migración datos, como se describe en [Importar datos a DocumentDB](documentdb-import-data.md).
- Explorador de documento en el Portal de Azure, como se describe en [masa agregar documentos con el Explorador de documento](documentdb-view-json-document-explorer.md#BulkAdd).
- Procedimientos almacenados, como se describe en el [servidor de DocumentDB de programación](documentdb-programming.md).

### <a name="does-documentdb-support-resource-link-caching"></a>¿DocumentDB admite la caché de vínculo de recursos?
Sí, porque DocumentDB es un servicio REST, vínculos a recursos son inmutables y pueden almacenarse en caché. Clientes de DocumentDB pueden especificar un encabezado "If-None-coincidir" para lecturas frente a cualquier recurso como documento o colección y actualizar copias de sus locales solo cuando se cambia la versión del servidor. 

### <a name="is-a-local-instance-of-documentdb-available"></a>¿Hay una instancia local de DocumentDB?
En este momento una instancia local de DocumentDB no está disponible. Puede realizar un seguimiento del estado de un emulador local y voto para él en el [foro de comentarios](https://feedback.azure.com/forums/263030-documentdb/suggestions/6328798-standalone-local-instance).


[azure-portal]: https://portal.azure.com
[query]: documentdb-sql-query.md
 
