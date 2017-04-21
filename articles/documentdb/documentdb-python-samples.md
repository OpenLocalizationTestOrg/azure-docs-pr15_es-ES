<properties 
    pageTitle="Ejemplos de NoSQL Python para DocumentDB | Microsoft Azure" 
    description="Buscar ejemplos de NoSQL Python en github para las tareas comunes en DocumentDB, incluidas las operaciones de CRUD para documentos JSON en bases de datos NoSQL." 
    keywords="ejemplos de Python"
    services="documentdb" 
    authors="moderakh" 
    manager="jhubbard" 
    editor="monicar" 
    documentationCenter="python"/>

<tags 
    ms.service="documentdb" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="04/18/2016" 
    ms.author="moderakh"/>


# <a name="documentdb-python-examples"></a>Ejemplos de DocumentDB Python

> [AZURE.SELECTOR]
- [Ejemplos de .NET](documentdb-dotnet-samples.md)
- [Ejemplos de Node.js](documentdb-nodejs-samples.md)
- [Ejemplos de Python](documentdb-python-samples.md)
- [Galería de código de ejemplo de Azure](https://azure.microsoft.com/documentation/samples/?service=documentdb)

Soluciones de ejemplo que lleve a cabo operaciones CRUD y otras operaciones comunes en Azure DocumentDB recursos se incluyen en el repositorio de GitHub [documentdb de azure de python](https://github.com/Azure/azure-documentdb-python/tree/master/samples) . Este artículo proporciona:

- Vínculos a las tareas de cada uno de los archivos de proyecto de ejemplo de Python. 
- Vínculos a la API relacionada referencia contenido.

**Requisitos previos**

1. Necesita una cuenta de Azure para usar estos ejemplos de Python:
    - Puede [Abrir una cuenta de Azure gratis](https://azure.microsoft.com/pricing/free-trial/): obtenga créditos puede usar para probar los servicios de Azure pagados e incluso después de que se utilizan hasta puede mantener la cuenta y usar libre Azure servicios, como sitios Web. Nunca se cargará su tarjeta de crédito a menos que explícitamente cambia la configuración y pídale que se cargará.
   - Puede [activar las ventajas de suscriptor de Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/): Visual Studio su suscripción le proporciona créditos cada mes que puede usar para los servicios de Azure pagados.
2. También debe el [SDK de Python](documentdb-sdk-python.md). 

    > [AZURE.NOTE] Cada muestra es independiente, configura y limpia después de sí mismo. Por lo tanto, los ejemplos emiten varias llamadas a [document_client. CreateCollection](http://azure.github.io/azure-documentdb-python/api/pydocumentdb.document_client.html). Cada vez que esto se realiza la suscripción se cargará para 1 hora de uso por el nivel de rendimiento de la colección se ha creado. 

## <a name="database-examples"></a>Ejemplos de base de datos

El archivo [Program.py](https://github.com/Azure/azure-documentdb-python/tree/master/samples/DatabaseManagement/Program.py) del proyecto [DatabaseManagement](https://github.com/Azure/azure-documentdb-python/tree/master/samples/DatabaseManagement) muestra cómo realizar las siguientes tareas.

Tarea | Referencia de la API
--- | ---
[Crear una base de datos](https://github.com/Azure/azure-documentdb-python/blob/d78170214467e3ab71ace1a7400f5a7fa5a7b5b0/samples/DatabaseManagement/Program.py#L65-L76) | [document_client. CreateDatabase](http://azure.github.io/azure-documentdb-python/api/pydocumentdb.document_client.html)
[Una cuenta para una base de datos de la consulta](https://github.com/Azure/azure-documentdb-python/blob/d78170214467e3ab71ace1a7400f5a7fa5a7b5b0/samples/DatabaseManagement/Program.py#L49-L62) | [document_client. QueryDatabases](http://azure.github.io/azure-documentdb-python/api/pydocumentdb.document_client.html)
[Leer una base de datos por Id.](https://github.com/Azure/azure-documentdb-python/blob/d78170214467e3ab71ace1a7400f5a7fa5a7b5b0/samples/DatabaseManagement/Program.py#L79-L96) | [document_client. DeBase](http://azure.github.io/azure-documentdb-python/api/pydocumentdb.document_client.html)
[Bases de datos de lista para una cuenta](https://github.com/Azure/azure-documentdb-python/blob/d78170214467e3ab71ace1a7400f5a7fa5a7b5b0/samples/DatabaseManagement/Program.py#L99-L110) | [document_client. ReadDatabases](http://azure.github.io/azure-documentdb-python/api/pydocumentdb.document_client.html)
[Eliminar una base de datos](https://github.com/Azure/azure-documentdb-python/blob/d78170214467e3ab71ace1a7400f5a7fa5a7b5b0/samples/DatabaseManagement/Program.py#L113-L126) | [document_client. DeleteDatabase](http://azure.github.io/azure-documentdb-python/api/pydocumentdb.document_client.html)

## <a name="collection-examples"></a>Ejemplos de la colección 

El archivo [Program.py](https://github.com/Azure/azure-documentdb-python/tree/master/samples/CollectionManagement/Program.py) del proyecto [CollectionManagement](https://github.com/Azure/azure-documentdb-python/tree/master/samples/CollectionManagement) muestra cómo realizar las siguientes tareas.

Tarea | Referencia de la API
--- | ---
[Crear una colección](https://github.com/Azure/azure-documentdb-python/blob/d78170214467e3ab71ace1a7400f5a7fa5a7b5b0/samples/CollectionManagement/Program.py#L84-L135) | [document_client. CreateCollection](http://azure.github.io/azure-documentdb-python/api/pydocumentdb.document_client.html#CreateCollection)
[Leer una lista de todas las colecciones de una base de datos](https://github.com/Azure/azure-documentdb-python/blob/d78170214467e3ab71ace1a7400f5a7fa5a7b5b0/samples/CollectionManagement/Program.py#L198-L225) | [document_client. ListCollections](http://azure.github.io/azure-documentdb-python/api/pydocumentdb.document_client.html#CreateCollection)
[Obtener una colección de Id.](https://github.com/Azure/azure-documentdb-python/blob/d78170214467e3ab71ace1a7400f5a7fa5a7b5b0/samples/CollectionManagement/Program.py#L178-L195) | [document_client. ReadCollection](http://azure.github.io/azure-documentdb-python/api/pydocumentdb.document_client.html#CreateCollection)
[Obtener el nivel de rendimiento de una colección](https://github.com/Azure/azure-documentdb-python/blob/d78170214467e3ab71ace1a7400f5a7fa5a7b5b0/samples/CollectionManagement/Program.py#L139-L161) | [DocumentQueryable.QueryOffers](http://azure.github.io/azure-documentdb-python/api/pydocumentdb.document_client.html#CreateCollection)
[Cambiar el nivel de rendimiento de una colección](https://github.com/Azure/azure-documentdb-python/blob/d78170214467e3ab71ace1a7400f5a7fa5a7b5b0/samples/CollectionManagement/Program.py#L163-L175) | [document_client. ReplaceOffer](http://azure.github.io/azure-documentdb-python/api/pydocumentdb.document_client.html#CreateCollection)
[Eliminar una colección](https://github.com/Azure/azure-documentdb-python/blob/d78170214467e3ab71ace1a7400f5a7fa5a7b5b0/samples/CollectionManagement/Program.py#L212-L225) | [document_client. DeleteCollection](http://azure.github.io/azure-documentdb-python/api/pydocumentdb.document_client.html#CreateCollection)
