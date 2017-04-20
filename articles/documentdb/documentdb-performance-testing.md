<properties 
    pageTitle="DocumentDB pruebas de rendimiento y escala | Microsoft Azure" 
    description="Obtenga información sobre cómo llevar a cabo escala y pruebas de performance con DocumentDB de Azure"
    keywords="pruebas de rendimiento"
    services="documentdb" 
    authors="arramac" 
    manager="jhubbard" 
    editor="" 
    documentationCenter=""/>

<tags 
    ms.service="documentdb" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/27/2016" 
    ms.author="arramac"/>

# <a name="performance-and-scale-testing-with-azure-documentdb"></a>Rendimiento y escala pruebas con DocumentDB de Azure
Pruebas de rendimiento y escala es un paso clave en el desarrollo de aplicaciones. Para muchas aplicaciones, el nivel de la base de datos tiene un impacto significativo en el rendimiento y escalabilidad y, por tanto, es un componente crítico de pruebas de rendimiento. [DocumentDB de Azure](https://azure.microsoft.com/services/documentdb/) está diseñado específicamente para elástico escala y rendimiento predecible y, por tanto, ajustar una gran para aplicaciones que necesitan un nivel de base de datos de alto rendimiento. 

En este artículo es una referencia para desarrolladores de implementación de conjuntos de pruebas de rendimiento para sus cargas de trabajo de DocumentDB o evaluar DocumentDB para escenarios de aplicación de alto rendimiento. Se centra principalmente en pruebas de rendimiento aislado de la base de datos, pero también incluye los procedimientos recomendados para las aplicaciones de producción.

Después de leer este artículo, podrá responder a las preguntas siguientes:   

- ¿Dónde se puede encontrar una aplicación de cliente de .NET de ejemplo para pruebas de rendimiento de DocumentDB de Azure? 
- ¿Cómo se puede lograr niveles de alto rendimiento con Azure DocumentDB de mi aplicación de cliente?

Para empezar con código, descargue el proyecto de [Pruebas de rendimiento de ejemplo DocumentDB](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/documentdb-benchmark). 

> [AZURE.NOTE] Es el objetivo de esta aplicación demostrar los procedimientos recomendados para extraer el mejor rendimiento de DocumentDB con un pequeño número de equipos cliente. Esto no se realizó para mostrar la capacidad máxima del servicio, que puede escalar limitlessly.

Si está buscando las opciones de configuración de cliente mejorar el rendimiento de DocumentDB, vea [sugerencias para el rendimiento DocumentDB](documentdb-performance-tips.md).

## <a name="run-the-performance-testing-application"></a>Ejecute la aplicación de pruebas de rendimiento
La forma más rápida de empezar es compilar y ejecutar el ejemplo .NET siguiente, tal como se describe en los pasos siguientes. También puede revisar el código fuente e implementar configuraciones similares a sus propias aplicaciones cliente.

**Paso 1:** Descargar el proyecto de [Ejemplo de pruebas de rendimiento de DocumentDB](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/documentdb-benchmark)o se bifurcan del repositorio de Github.

**Paso 2:** Modificar la configuración de EndpointUrl, AuthorizationKey, CollectionThroughput y DocumentTemplate (opcional) en App.config.

> [AZURE.NOTE] Antes de hacer el aprovisionamiento de colecciones de alto rendimiento, consulte la [Página de precios](https://azure.microsoft.com/pricing/details/documentdb/) para calcular los costos por colección. Almacenamiento de facturas de DocumentDB y rendimiento independientemente de la forma por hora, por lo que puede ahorrar costos eliminando o reducir el rendimiento de las colecciones de DocumentDB después de probar.

**Paso 3:** Compilar y ejecutar la aplicación de consola desde la línea de comandos. Debe ver un resultado similar al siguiente:

    Summary:
    ---------------------------------------------------------------------
    Endpoint: https://docdb-scale-demo.documents.azure.com:443/
    Collection : db.testdata at 50000 request units per second
    Document Template*: Player.json
    Degree of parallelism*: 500
    ---------------------------------------------------------------------

    DocumentDBBenchmark starting...
    Creating database db
    Creating collection testdata
    Creating metric collection metrics
    Retrying after sleeping for 00:03:34.1720000
    Starting Inserts with 500 tasks
    Inserted 661 docs @ 656 writes/s, 6860 RU/s (18B max monthly 1KB reads)
    Inserted 6505 docs @ 2668 writes/s, 27962 RU/s (72B max monthly 1KB reads)
    Inserted 11756 docs @ 3240 writes/s, 33957 RU/s (88B max monthly 1KB reads)
    Inserted 17076 docs @ 3590 writes/s, 37627 RU/s (98B max monthly 1KB reads)
    Inserted 22106 docs @ 3748 writes/s, 39281 RU/s (102B max monthly 1KB reads)
    Inserted 28430 docs @ 3902 writes/s, 40897 RU/s (106B max monthly 1KB reads)
    Inserted 33492 docs @ 3928 writes/s, 41168 RU/s (107B max monthly 1KB reads)
    Inserted 38392 docs @ 3963 writes/s, 41528 RU/s (108B max monthly 1KB reads)
    Inserted 43371 docs @ 4012 writes/s, 42051 RU/s (109B max monthly 1KB reads)
    Inserted 48477 docs @ 4035 writes/s, 42282 RU/s (110B max monthly 1KB reads)
    Inserted 53845 docs @ 4088 writes/s, 42845 RU/s (111B max monthly 1KB reads)
    Inserted 59267 docs @ 4138 writes/s, 43364 RU/s (112B max monthly 1KB reads)
    Inserted 64703 docs @ 4197 writes/s, 43981 RU/s (114B max monthly 1KB reads)
    Inserted 70428 docs @ 4216 writes/s, 44181 RU/s (115B max monthly 1KB reads)
    Inserted 75868 docs @ 4247 writes/s, 44505 RU/s (115B max monthly 1KB reads)
    Inserted 81571 docs @ 4280 writes/s, 44852 RU/s (116B max monthly 1KB reads)
    Inserted 86271 docs @ 4273 writes/s, 44783 RU/s (116B max monthly 1KB reads)
    Inserted 91993 docs @ 4299 writes/s, 45056 RU/s (117B max monthly 1KB reads)
    Inserted 97469 docs @ 4292 writes/s, 44984 RU/s (117B max monthly 1KB reads)
    Inserted 99736 docs @ 4192 writes/s, 43930 RU/s (114B max monthly 1KB reads)
    Inserted 99997 docs @ 4013 writes/s, 42051 RU/s (109B max monthly 1KB reads)
    Inserted 100000 docs @ 3846 writes/s, 40304 RU/s (104B max monthly 1KB reads)

    Summary:
    ---------------------------------------------------------------------
    Inserted 100000 docs @ 3834 writes/s, 40180 RU/s (104B max monthly 1KB reads)
    ---------------------------------------------------------------------
    DocumentDBBenchmark completed successfully.


**Paso 4 (si es necesario):** El rendimiento notificado (RU/s) de la herramienta debe ser igual o mayor que el rendimiento de la preparación de la colección. Si no es así, aumentar la DegreeOfParallelism en pequeños incrementos puede ayudarle a alcanzar el límite. Si el rendimiento de la aplicación cliente meseta, iniciar varias instancias de la aplicación en los equipos de la mismas u otra le ayudará a alcanzar el límite de preparación en todas las instancias diferentes. Si necesita ayuda con este paso, por favor, escribir un correo electrónico a askdocdb@microsoft.com o rellenar una incidencia de soporte técnico.

Una vez que la aplicación que se ejecuta, puede intentar distintas [directivas de indización](documentdb-indexing-policies.md) y [niveles de coherencia](documentdb-consistency-levels.md) para comprender su impacto en el rendimiento y la latencia. También puede revisar el código fuente e implementar configuraciones similares a sus propios conjuntos de pruebas o aplicaciones de producción.

## <a name="next-steps"></a>Pasos siguientes
En este artículo, hemos visto cómo puede realizar el rendimiento y escala pruebas con DocumentDB con una aplicación de consola de .NET. Consulte los vínculos siguientes para obtener más información sobre cómo trabajar con DocumentDB.

* [Ejemplo de pruebas de rendimiento DocumentDB](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/documentdb-benchmark)
* [Opciones de configuración de cliente para mejorar el rendimiento de DocumentDB](documentdb-performance-tips.md)
* [Servidor particiones en DocumentDB](documentdb-partition-data.md)
* [Colecciones de DocumentDB y niveles de rendimiento](documentdb-performance-levels.md)
* [Documentación de DocumentDB .NET SDK en MSDN](https://msdn.microsoft.com/library/azure/dn948556.aspx)
* [Ejemplos de .NET DocumentDB](https://github.com/Azure/azure-documentdb-net)
* [Blog de DocumentDB en sugerencias de rendimiento](https://azure.microsoft.com/blog/2015/01/20/performance-tips-for-azure-documentdb-part-1-2/)
