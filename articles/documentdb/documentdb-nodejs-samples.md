<properties
    pageTitle="Ejemplos de NoSQL Node.js para DocumentDB | Microsoft Azure"
    description="Buscar ejemplos de NoSQL Node.js en github para las tareas comunes en DocumentDB, incluidas las operaciones de CRUD para documentos JSON en bases de datos NoSQL."
    keywords="ejemplos de Node.js"
    services="documentdb"
    authors="moderakh"
    manager="jhubbard"
    editor="monicar"
    documentationCenter="nodejs"/>

<tags
    ms.service="documentdb"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/03/2016"
    ms.author="moderakh"/>


# <a name="documentdb-nodejs-examples"></a>Ejemplos de DocumentDB Node.js

> [AZURE.SELECTOR]
- [Ejemplos de .NET](documentdb-dotnet-samples.md)
- [Ejemplos de Node.js](documentdb-nodejs-samples.md)
- [Ejemplos de Python](documentdb-python-samples.md)
- [Galería de código de ejemplo de Azure](https://azure.microsoft.com/documentation/samples/?service=documentdb)

Soluciones de ejemplo que lleve a cabo operaciones CRUD y otras operaciones comunes en Azure DocumentDB recursos se incluyen en el repositorio de GitHub [nodejs de documentdb de azure](https://github.com/Azure/azure-documentdb-node/tree/master/samples) . Este artículo proporciona:

- Vínculos a las tareas de cada uno de los archivos de proyecto de ejemplo de Node.js.
- Vínculos a la API relacionada referencia contenido.

**Requisitos previos**

1. Necesita una cuenta de Azure para usar estos ejemplos de Node.js:
    - Puede [Abrir una cuenta de Azure gratis](https://azure.microsoft.com/pricing/free-trial/): obtenga créditos puede usar para probar los servicios de Azure pagados e incluso después de que se utilizan hasta puede mantener la cuenta y usar libre Azure servicios, como sitios Web. Nunca se cargará su tarjeta de crédito a menos que explícitamente cambia la configuración y pídale que se cargará.
   - Puede [activar las ventajas de suscriptor de Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/): Visual Studio su suscripción le proporciona créditos cada mes que puede usar para los servicios de Azure pagados.
2. También debe el [SDK de Node.js](documentdb-sdk-node.md).

    > [AZURE.NOTE] Cada muestra es independiente, configura y limpia después de sí mismo. Por lo tanto, los ejemplos emiten varias llamadas a [DocumentClient.createCollection](http://azure.github.io/azure-documentdb-node/DocumentClient.html#createCollection). Cada vez que esto se realiza la suscripción se cargará para 1 hora de uso por el nivel de rendimiento de la colección se ha creado.

## <a name="database-examples"></a>Ejemplos de base de datos

El archivo [app.js](https://github.com/Azure/azure-documentdb-node/blob/master/samples/DatabaseManagement/app.js) del proyecto [DatabaseManagement](https://github.com/Azure/azure-documentdb-node/tree/master/samples/DatabaseManagement) muestra cómo realizar las siguientes tareas.

Tarea | Referencia de la API
--- | ---
[Crear una base de datos](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.DatabaseManagement/app.js#L121-L131) | [DocumentClient.createDatabase](http://azure.github.io/azure-documentdb-node/DocumentClient.html#createDatabase)
[Una cuenta para una base de datos de la consulta](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.DatabaseManagement/app.js#L146-L171) | [DocumentClient.queryDatabases](http://azure.github.io/azure-documentdb-node/DocumentClient.html#queryDatabases)
[Leer una base de datos por Id.](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.DatabaseManagement/app.js#L89-L99) | [DocumentClient.readDatabase](http://azure.github.io/azure-documentdb-node/DocumentClient.html#readDatabase)
[Bases de datos de lista para una cuenta](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.DatabaseManagement/app.js#L111-L119) | [DocumentClient.readDatabases](http://azure.github.io/azure-documentdb-node/DocumentClient.html#readDatabase)
[Eliminar una base de datos](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.DatabaseManagement/app.js#L133-L144) | [DocumentClient.deleteDatabase](http://azure.github.io/azure-documentdb-node/DocumentClient.html#deleteDatabase)

## <a name="collection-examples"></a>Ejemplos de la colección

El archivo [app.js](https://github.com/Azure/azure-documentdb-node/blob/master/samples/CollectionManagement/app.js) del proyecto [CollectionManagement](https://github.com/Azure/azure-documentdb-node/tree/master/samples/CollectionManagement) muestra cómo realizar las siguientes tareas.

Tarea | Referencia de la API
--- | ---
[Crear una colección](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.CollectionManagement/app.js#L97-L118) | [DocumentClient.createCollection](http://azure.github.io/azure-documentdb-node/DocumentClient.html#createCollection)
[Leer una lista de todas las colecciones de una base de datos](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.CollectionManagement/app.js#L120-L130) | [DocumentClient.readCollections](http://azure.github.io/azure-documentdb-node/DocumentClient.html#readCollections)
[Obtenga una colección de _self](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.CollectionManagement/app.js#L132-L141) | [DocumentClient.readCollection](http://azure.github.io/azure-documentdb-node/DocumentClient.html#readCollection)
[Obtener una colección de Id.](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.CollectionManagement/app.js#L143-L156) | [DocumentClient.readCollection](http://azure.github.io/azure-documentdb-node/DocumentClient.html#readCollection)
[Obtener el nivel de rendimiento de una colección](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.CollectionManagement/app.js#L158-L186) | [DocumentQueryable.queryOffers](http://azure.github.io/azure-documentdb-node/DocumentClient.html#queryOffers)
[Cambiar el nivel de rendimiento de una colección](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.CollectionManagement/app.js#L188-L202) | [DocumentClient.replaceOffer](http://azure.github.io/azure-documentdb-node/DocumentClient.html#replaceOffer)
[Eliminar una colección](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.CollectionManagement/app.js#L204-L215) | [DocumentClient.deleteCollection](http://azure.github.io/azure-documentdb-node/DocumentClient.html#deleteCollection)

## <a name="document-examples"></a>Ejemplos de documento

El archivo [app.js](https://github.com/Azure/azure-documentdb-node/blob/master/samples/DocumentManagement/app.js) del proyecto [DocumentManagement](https://github.com/Azure/azure-documentdb-node/tree/master/samples/DocumentManagement) muestra cómo realizar las siguientes tareas.

Tarea | Referencia de la API
--- | ---
[Crear documentos](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.DocumentManagement/app.js#L153-L177) | [DocumentClient.createDocument](http://azure.github.io/azure-documentdb-node/DocumentClient.html#createDocument)
[Leer el documento de una colección de fuente](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.DocumentManagement/app.js#L179-L189) | [DocumentClient.readDocument](http://azure.github.io/azure-documentdb-node/DocumentClient.html#readDocument)
[Leer un documento por Id.](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.DocumentManagement/app.js#L191-L201) | [DocumentClient.readDocument](http://azure.github.io/azure-documentdb-node/DocumentClient.html#readDocument)
[Leer el documento únicamente si ha cambiado el documento](https://github.com/Azure/azure-documentdb-node/blob/0778eadea7abb2af41e8c22a239dc872c584f421/samples/DocumentManagement/app.js#L79-L107) | [DocumentClient.readDocument](http://azure.github.io/azure-documentdb-node/DocumentClient.html#readDocument)<br/>[RequestOptions.accessCondition](http://azure.github.io/azure-documentdb-node/global.html#RequestOptions)
[Consulta de documentos](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.DocumentManagement/app.js#L82-L110) | [DocumentClient.queryDocuments](http://azure.github.io/azure-documentdb-node/DocumentClient.html#queryDocuments)
[Reemplazar un documento](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.DocumentManagement/app.js#L112-L119) |  [DocumentClient.replaceDocument](http://azure.github.io/azure-documentdb-node/DocumentClient.html#replaceDocument)
[Reemplazar el documento con la comprobación de ETag condicional](https://github.com/Azure/azure-documentdb-node/blob/0778eadea7abb2af41e8c22a239dc872c584f421/samples/DocumentManagement/app.js#L147-L164) |  [DocumentClient.replaceDocument](http://azure.github.io/azure-documentdb-node/DocumentClient.html#replaceDocument)<br/>[RequestOptions.accessCondition](http://azure.github.io/azure-documentdb-node/global.html#RequestOptions)
[Eliminar un documento](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.DocumentManagement/app.js#L122-L133) | [DocumentClient.deleteDocument](http://azure.github.io/azure-documentdb-node/DocumentClient.html#deleteDocument)

## <a name="indexing-examples"></a>Ejemplos de indización

El archivo [app.js](https://github.com/Azure/azure-documentdb-node/blob/master/samples/IndexManagement/app.js) del proyecto [IndexManagement](https://github.com/Azure/azure-documentdb-node/tree/master/samples/IndexManagement) muestra cómo realizar las siguientes tareas.

Tarea | Referencia de la API
--- | ---
Crear una colección con la indización de forma predeterminada | [DocumentClient.createDocument](http://azure.github.io/azure-documentdb-node/DocumentClient.html)
[Manualmente el índice de un documento específico](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.IndexManagement/app.js#L185-L238) | [indexingDirective: 'incluir'](http://azure.github.io/azure-documentdb-node/global.html#indexingDirective)
[Excluir manualmente un documento específico del índice](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.IndexManagement/app.js#L120-L183) | [RequestOptions.indexingDirective](http://azure.github.io/azure-documentdb-node/global.html#RequestOptions)
[Usar la indización diferida de importación masiva o leer colecciones gruesas](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.IndexManagement/app.js#L240-L269) | [IndexingMode.Lazy](http://azure.github.io/azure-documentdb-node/global.html#IndexingMode)
[Incluir rutas específicas de un documento en la indización](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.IndexManagement/app.js#L433-L444) | [IndexingPolicy.IncludedPaths](http://azure.github.io/azure-documentdb-node/global.html#IndexingPolicy)
[Excluir determinadas rutas de indización](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.IndexManagement/app.js#L427-L450) | [ExcludedPath](http://azure.github.io/azure-documentdb-node/global.html#IndexingPolicy)
[Permitir un análisis de una ruta de acceso de cadena durante una operación de rango](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.IndexManagement/app.js#L271-L347)| [ExcludedPath.EnableScanInQuery](http://azure.github.io/azure-documentdb-node/global.html#FeedOptions)
[Crear un índice de rango en una ruta de acceso de cadena](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.IndexManagement/app.js#L349-L425) | [DocumentClient.queryDocument](http://azure.github.io/azure-documentdb-node/DocumentClient.html#queryDocument)
[Crear una colección de indexPolicy de forma predeterminada, a continuación, actualizarla en línea](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.IndexManagement/app.js#L519-L614) | [DocumentClient.createCollection](http://azure.github.io/azure-documentdb-node/DocumentClient.html#createCollection)<br> [DocumentClient.replaceCollection#replaceCollection](http://azure.github.io/azure-documentdb-node/DocumentClient.html)

Para obtener más información acerca de la indización, consulte [DocumentDB directivas de indexación](documentdb-indexing-policies.md).

## <a name="server-side-programming-examples"></a>Ejemplos de programación de servidor

El archivo [app.js](https://github.com/Azure/azure-documentdb-node/blob/master/samples/ServerSideScripts/app.js) del proyecto [ServerSideScripts](https://github.com/Azure/azure-documentdb-node/tree/master/samples/ServerSideScripts) muestra cómo realizar las siguientes tareas.

Tarea | Referencia de la API
--- | ---
[Crear un procedimiento almacenado](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.ServerSideScripts/app.js#L44-L71) | [DocumentClient.createStoredProcedure](http://azure.github.io/azure-documentdb-node/DocumentClient.html#createStoredProcedure)
[Ejecutar un procedimiento almacenado](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.ServerSideScripts/app.js#L73-L90) | [DocumentClient.executeStoredProcedure](http://azure.github.io/azure-documentdb-node/DocumentClient.html#executeStoredProcedure)

Para obtener más información acerca de la programación de servidor, consulte [DocumentDB programación de servidor: procedimientos almacenados, desencadenadores de base de datos y UDF](documentdb-programming.md).

## <a name="partitioning-examples"></a>Ejemplos de partición

El archivo [app.js](https://github.com/Azure/azure-documentdb-node/blob/master/samples/Partitioning/app.js) del proyecto [particiones](https://github.com/Azure/azure-documentdb-node/tree/master/samples/Partitioning) muestra cómo realizar las siguientes tareas.

Tarea | Referencia de la API
--- | ---
[Usar un HashPartitionResolver](https://github.com/Azure/azure-documentdb-node/blob/ce0fc3c4e70b0279091a1e03620a668d93a14fc2/samples/Partitioning/app.js#L53-L103) | [HashPartitionResolver](http://azure.github.io/azure-documentdb-node/HashPartitionResolver.html)

Para obtener más información acerca de particiones de datos en DocumentDB, vea [datos de partición y escala en DocumentDB](documentdb-partition-data.md).
