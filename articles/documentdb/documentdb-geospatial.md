<properties 
    pageTitle="Trabajar con datos geoespaciales en Azure DocumentDB | Microsoft Azure" 
    description="Aprenda a crear, índice y consulta espaciales objetos con DocumentDB de Azure." 
    services="documentdb" 
    documentationCenter="" 
    authors="arramac" 
    manager="jhubbard" 
    editor="monicar"/>

<tags 
    ms.service="documentdb" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="data-services" 
    ms.date="10/25/2016" 
    ms.author="arramac"/>
    
# <a name="working-with-geospatial-data-in-azure-documentdb"></a>Trabajar con datos geoespaciales en DocumentDB de Azure

En este artículo es una introducción a la funcionalidad de geoespaciales en [DocumentDB de Azure](https://azure.microsoft.com/services/documentdb/). Después de leer esto, podrá responder a las preguntas siguientes:

- ¿Cómo se puede almacenar datos espaciales en DocumentDB de Azure?
- ¿Cómo puedo consultar datos geoespaciales en DocumentDB de Azure en SQL y LINQ?
- ¿Cómo habilitar o deshabilitar la indización espacial en DocumentDB?

Vea este [proyecto Github](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/code-samples/Geospatial/Program.cs) para obtener ejemplos de código.

## <a name="introduction-to-spatial-data"></a>Introducción a los datos espaciales

Datos espaciales, describen la posición y la forma de objetos en el espacio. En la mayoría de las aplicaciones, estos se corresponden a los objetos de la tierra, es decir, por datos geoespaciales. Datos espaciales se pueden utilizar para representar la ubicación de una persona, un lugar de interés o el borde de una ciudad o un lago. Casos de uso común a menudo implican consultas de proximidad, por ejemplo, "Find café todos cerca de la ubicación actual". 

### <a name="geojson"></a>GeoJSON
DocumentDB admite indización y consulta de datos de punto de geoespaciales que se representan mediante la [especificación de GeoJSON](http://geojson.org/geojson-spec.html). Estructuras de datos de GeoJSON siempre son objetos JSON válidos, por lo que se pueden almacenar y consultan mediante DocumentDB sin necesidad de herramientas especializadas o bibliotecas. El SDK de DocumentDB proporcionan clases auxiliares y métodos que hacen que sea fácil trabajar con datos espaciales. 

### <a name="points-linestrings-and-polygons"></a>Puntos, linestrings y polígonos
Un **punto** denota una sola posición en el espacio. En por datos geoespaciales, un punto representa la ubicación exacta, que puede ser una dirección de una tienda de comestibles, exposición, un automóvil o una ciudad.  Se representa un punto de par con sus coordenadas GeoJSON (y DocumentDB) o longitud y latitud. Aquí tenemos un ejemplo JSON un punto.

**Puntos de DocumentDB**

    {
       "type":"Point",
       "coordinates":[ 31.9, -4.8 ]
    }

>[AZURE.NOTE] La especificación de GeoJSON especifica latitud primero y latitud segundo. Al igual que en otras aplicaciones de la asignación, longitud y latitud son ángulos y representan en función de grados. Los valores de latitud se mide desde el meridiano y son entre -180 y 180.0 grados y latitud valores se miden desde el Ecuador y entre-90.0 y 90.0 grados. 
>
> DocumentDB interpreta las coordenadas representada por el sistema de referencia WGS 84. Consulte a continuación para obtener más detalles acerca de los sistemas de coordenadas de referencia.

Esto puede insertarse en un documento de DocumentDB tal como se muestra en este ejemplo de un perfil de usuario que contiene los datos de ubicación:

**Utilizar perfil con la ubicación almacenada en DocumentDB**

    {
       "id":"documentdb-profile",
       "screen_name":"@DocumentDB",
       "city":"Redmond",
       "topics":[ "NoSQL", "Javascript" ],
       "location":{
          "type":"Point",
          "coordinates":[ 31.9, -4.8 ]
       }
    }

Además de puntos, GeoJSON también admite LineStrings y polígonos. **LineStrings** representan una serie de puntos de dos o más de espacio y los segmentos de línea que se conectan a ellos. En por datos geoespaciales, linestrings normalmente se usan para representar autopistas o ríos. Un **polígono** es un límite de puntos conectados que forma un cerrados LineString. Polígonos normalmente se usan para representar las informaciones clave natural como lagos o países políticas como Estados y ciudades. Aquí es un ejemplo de un polígono en DocumentDB. 

**Polígonos en DocumentDB**

    {
       "type":"Polygon",
       "coordinates":[
           [ 31.8, -5 ],
           [ 31.8, -4.7 ],
           [ 32, -4.7 ],
           [ 32, -5 ],
           [ 31.8, -5 ]
       ]
    }

>[AZURE.NOTE] La especificación de GeoJSON requiere que para polígonos válidos, el último par de coordenadas proporcionado debe ser el mismo que el primero, para crear una forma cerrada.
>
>Deben especificar puntos dentro de un polígono en orden hacia la izquierda. Un polígono especificado en el orden de las agujas del reloj representa la función inversa de la región dentro de ella.

Además de punto, LineString y polígono, GeoJSON también especifica la representación de cómo agrupar varias ubicaciones geoespaciales, así como cómo asociar propiedades arbitrarios ubicación geográfica como **característica**. Dado que estos objetos JSON válido, se pueden todos almacenados y procesan en DocumentDB. Sin embargo DocumentDB solo admite la indización automática de puntos.

### <a name="coordinate-reference-systems"></a>Sistemas de coordenadas de referencia

Dado que la forma de la tierra es irregular, se representa las coordenadas de datos geoespaciales en muchos sistemas de coordenadas de referencia (CRS), cada una con sus propios marcos de referencia y unidades de medida de. Por ejemplo, el "nacional cuadrícula de Bretaña" es un sistema de referencia es muy preciso para el Reino Unido, pero no fuera del mismo. 

El más populares CRS en uso hoy es el sistema geodésico del mundo [WGS 84](http://earth-info.nga.mil/GandG/wgs84/). Dispositivos GPS y muchos servicios de asignación como mapas de Google y las API de mapas de Bing usan WGS 84. DocumentDB admite indización y consulta de datos geoespaciales usando solo la CRS WGS 84. 

## <a name="creating-documents-with-spatial-data"></a>Creación de documentos con datos de espacio
Al crear documentos que contienen valores de GeoJSON, se indiza automáticamente con un índice espacial de acuerdo con la directiva de indización de la colección. Si está trabajando con una DocumentDB SDK en un idioma dinámicamente escrito como Python o Node.js, debe crear GeoJSON válida.

**Crear documentos con datos geoespaciales en Node.js**

    var userProfileDocument = {
       "name":"documentdb",
       "location":{
          "type":"Point",
          "coordinates":[ -122.12, 47.66 ]
       }
    };

    client.createDocument(`dbs/${databaseName}/colls/${collectionName}`, userProfileDocument, (err, created) => {
        // additional code within the callback
    });

Si está trabajando con .NET (o Java) SDK, puede usar las nuevas clases de punto y polígono dentro del espacio de nombres Microsoft.Azure.Documents.Spatial para incrustar información de ubicación dentro de los objetos de la aplicación. Estas clases ayudan a simplificar la serialización y deserialización de datos espaciales en GeoJSON.

**Crear documentos con datos geoespaciales en .NET**

    using Microsoft.Azure.Documents.Spatial;
    
    public class UserProfile
    {
        [JsonProperty("name")]
        public string Name { get; set; }

        [JsonProperty("location")]
        public Point Location { get; set; }
        
        // More properties
    }
    
    await client.CreateDocumentAsync(
        UriFactory.CreateDocumentCollectionUri("db", "profiles"), 
        new UserProfile 
        { 
            Name = "documentdb", 
            Location = new Point (-122.12, 47.66) 
        });

Si no tiene la información de latitud y longitud, pero los nombres de ubicación como ciudad o país o direcciones físicas, puede buscar las coordenadas reales mediante un servicio de codificación geográfica como servicios de REST de mapas de Bing. Obtenga más información sobre la codificación geográfica de mapas de Bing [aquí](https://msdn.microsoft.com/library/ff701713.aspx).

## <a name="querying-spatial-types"></a>Consultar tipos espaciales

Ahora que hemos tomado un vistazo a cómo insertar datos geoespaciales, echemos un vistazo a cómo consultar estos datos utilizando DocumentDB con SQL y LINQ.

### <a name="spatial-sql-built-in-functions"></a>Espaciales funciones integradas de SQL
DocumentDB es compatible con las siguientes funciones integradas de abrir geoespaciales Consortium (OGC) para consultar geoespaciales. Para obtener más detalles sobre el conjunto completo de funciones integradas en el idioma SQL, consulte [DocumentDB de consulta](documentdb-sql-query.md).

<table>
<tr>
  <td><strong>Uso</strong></td>
  <td><strong>Descripción</strong></td>
</tr>
<tr>
  <td>ST_DISTANCE (point_expr, point_expr)</td>
  <td>Devuelve la distancia entre las dos expresiones de punto de GeoJSON.</td>
</tr>
<tr>
  <td>ST_WITHIN (point_expr, polygon_expr)</td>
  <td>Devuelve una expresión booleana que indica si el punto de GeoJSON especificado en el primer argumento es dentro del polígono GeoJSON en el segundo argumento.</td>
</tr>
<tr>
  <td>ST_ISVALID</td>
  <td>Devuelve un valor Boolean que indica si la expresión de punto o polígono GeoJSON especificada es válida.</td>
</tr>
<tr>
  <td>ST_ISVALIDDETAILED</td>
  <td>Devuelve un valor JSON que contiene un valor Boolean valor si la expresión de punto o polígono GeoJSON especificada es válida y no válidos, además el motivo como un valor de cadena.</td>
</tr>
</table>

Funciones espaciales pueden utilizarse para realizar consultas de proximidad en datos espaciales. Por ejemplo, aquí es una consulta que devuelve todos los documentos de familia que están dentro de 30 km de la ubicación especificada utilizando la función integrada de ST_DISTANCE. 

**Consulta**

    SELECT f.id 
    FROM Families f 
    WHERE ST_DISTANCE(f.location, {'type': 'Point', 'coordinates':[31.9, -4.8]}) < 30000

**Resultados**

    [{
      "id": "WakefieldFamily"
    }]

Si incluye la indización espacial en la directiva de indización, a continuación, "consultas distancia" se servirá eficaz a través del índice. Para obtener más detalles sobre la indización espacial, consulte la sección siguiente. Si no tiene un índice espacial para rutas especificadas, aún puede realizar consultas espaciales especificando `x-ms-documentdb-query-enable-scan` encabezado de la solicitud con el valor establecido en "true". En. NET, esto puede hacerse pasando el argumento opcional de **FeedOptions** a las consultas con [EnableScanInQuery](https://msdn.microsoft.com/library/microsoft.azure.documents.client.feedoptions.enablescaninquery.aspx#P:Microsoft.Azure.Documents.Client.FeedOptions.EnableScanInQuery) establecida en true. 

ST_WITHIN puede utilizarse para comprobar si un punto se encuentra dentro de un polígono. Polígonos se usan generalmente para representar los límites como códigos postales, los límites de estado o formaciones naturales. Nuevo si incluye la indización espacial en la directiva de indización, a continuación, "dentro de" consultas se servirá eficaz a través del índice. 

Argumentos de polígono en ST_WITHIN pueden contener solo un único timbre, es decir, los polígonos no deben contener taladros en ellos. 

**Consulta**

    SELECT * 
    FROM Families f 
    WHERE ST_WITHIN(f.location, {
        'type':'Polygon', 
        'coordinates': [[[31.8, -5], [32, -5], [32, -4.7], [31.8, -4.7], [31.8, -5]]]
    })

**Resultados**

    [{
      "id": "WakefieldFamily",
    }]
    
>[AZURE.NOTE] De forma similar a cómo discrepancia funciona de tipos de consulta DocumentDB, si el valor de la ubicación especificada en bien argumento es incorrecto o no válidos, evaluará **definido** y el documento evaluado se omite desde los resultados de la consulta. Si la consulta no devuelve ningún resultado, ejecute ST_ISVALIDDETAILED para depuración, ¿por qué el tipo de spatail no es válido.     

DocumentDB también permite realizar consultas inversas, es decir, puede indizar polígonos o líneas en DocumentDB, una consulta con las áreas que contienen un punto especificado. Este patrón se usa generalmente en logística para identificar, por ejemplo, cuando una unidad móvil entra o sale de un área designada. 

**Consulta**

    SELECT * 
    FROM Areas a 
    WHERE ST_WITHIN({'type': 'Point', 'coordinates':[31.9, -4.8]}, a.location)


**Resultados**

    [{
      "id": "MyDesignatedLocation",
      "location": {
        "type":"Polygon", 
        "coordinates": [[[31.8, -5], [32, -5], [32, -4.7], [31.8, -4.7], [31.8, -5]]]
      }
    }]

ST_ISVALID y ST_ISVALIDDETAILED pueden usarse para comprobar si un objeto espacial es válido. Por ejemplo, la siguiente consulta comprueba la validez de un punto con una fuera del valor de rango latitud (-132.8). ST_ISVALID devuelve un valor de tipo Boolean y ST_ISVALIDDETAILED devuelve el valor booleano y una cadena que contiene el motivo por qué se considera no válido.

**Consulta**

    SELECT ST_ISVALID({ "type": "Point", "coordinates": [31.9, -132.8] })

**Resultados**

    [{
      "$1": false
    }]

Estas funciones también pueden usarse para validar polígonos. Por ejemplo, aquí usamos ST_ISVALIDDETAILED para validar un polígono que no está cerrado. 

**Consulta**

    SELECT ST_ISVALIDDETAILED({ "type": "Polygon", "coordinates": [[ 
        [ 31.8, -5 ], [ 31.8, -4.7 ], [ 32, -4.7 ], [ 32, -5 ] 
        ]]})

**Resultados**

    [{
       "$1": { 
          "valid": false, 
          "reason": "The Polygon input is not valid because the start and end points of the ring number 1 are not the same. Each ring of a polygon must have the same start and end points." 
        }
    }]
    
### <a name="linq-querying-in-the-net-sdk"></a>LINQ consultas en el SDK de .NET

El SDK de .NET DocumentDB también proveedores de código auxiliar métodos `Distance()` y `Within()` para su uso en expresiones. El proveedor LINQ DocumentDB traduce estas llamadas a las llamadas de función integrada SQL equivalentes (ST_DISTANCE y ST_WITHIN respectivamente). 

Aquí es un ejemplo de una consulta LINQ que busca todos los documentos en la colección de DocumentDB cuyo valor "ubicación" es dentro de un radio de 30 kilómetros del punto mediante LINQ.

**Consulta LINQ para distancia**

    foreach (UserProfile user in client.CreateDocumentQuery<UserProfile>(UriFactory.CreateDocumentCollectionUri("db", "profiles"))
        .Where(u => u.ProfileType == "Public" && a.Location.Distance(new Point(32.33, -4.66)) < 30000))
    {
        Console.WriteLine("\t" + user);
    }

Del mismo modo, esta es una consulta para buscar todos los documentos cuya "ubicación" está dentro del cuadro/polígono especificado. 

**Consultar LINQ dentro**

    Polygon rectangularArea = new Polygon(
        new[]
        {
            new LinearRing(new [] {
                new Position(31.8, -5),
                new Position(32, -5),
                new Position(32, -4.7),
                new Position(31.8, -4.7),
                new Position(31.8, -5)
            })
        });

    foreach (UserProfile user in client.CreateDocumentQuery<UserProfile>(UriFactory.CreateDocumentCollectionUri("db", "profiles"))
        .Where(a => a.Location.Within(rectangularArea)))
    {
        Console.WriteLine("\t" + user);
    }


Ahora que hemos tomado un vistazo a cómo consultar documentos usando LINQ y SQL, echemos un vistazo a cómo configurar DocumentDB para indización espacial.

## <a name="indexing"></a>Indización

Como se describe en el documento de [Esquema independiente indización con DocumentDB de Azure](http://www.vldb.org/pvldb/vol8/p1668-shukla.pdf) , hemos diseñado el motor de base de datos de DocumentDB ser realmente independiente del esquema y proporcionar soporte de primera clase para JSON. El motor de base de datos de escritura optimizado de DocumentDB forma nativa comprende representados en el estándar GeoJSON datos espaciales (puntos, polígonos y líneas).

En resumen, la geometría está proyectada de coordenadas geodésico en un plano 2D, a continuación, dividir las celdas mediante una **quadtree**cada vez. Estas celdas se asignan a 1d según la ubicación de la celda dentro de una **curva de rellenar espacio Hilbert**, que conserva la ubicación de destino. Además, cuando se indizan de datos de ubicación, pasa por un proceso conocido como **mosaico**, es decir, todas las celdas que forman intersección una ubicación se identifica y se almacenan como claves en el índice de DocumentDB. En el momento de la consulta, argumentos como puntos y polígonos también se teselados para extraer los rangos de celda relevantes ID y luego usar para recuperar datos del índice.

Si especifica una directiva de indexación que incluya el índice espacial para / * (todas las rutas), a continuación, todos los puntos que se encuentra dentro de la colección están indizados para consultas espaciales eficaces (ST_WITHIN y ST_DISTANCE). Índices espaciales no tiene un valor de precisión y utilizar siempre un valor de precisión de forma predeterminada.

>[AZURE.NOTE] DocumentDB admite la indización automática de puntos, polígonos y LineStrings

El siguiente fragmento JSON muestra una directiva de indexación con habilitado la indización espacial, es decir, cualquier punto de GeoJSON que se encuentra dentro de documentos para consultar espacial de índice. Si va a modificar la directiva de indexación con el Portal de Azure, puede especificar el siguiente JSON para la directiva de indización para habilitar espacial indización en la colección.

**JSON indización de directiva de colección con espacial habilitado para puntos y polígonos**

    {
       "automatic":true,
       "indexingMode":"Consistent",
       "includedPaths":[
          {
             "path":"/*",
             "indexes":[
                {
                   "kind":"Range",
                   "dataType":"String",
                   "precision":-1
                },
                {
                   "kind":"Range",
                   "dataType":"Number",
                   "precision":-1
                },
                {
                   "kind":"Spatial",
                   "dataType":"Point"
                },
                {
                   "kind":"Spatial",
                   "dataType":"Polygon"
                }                
             ]
          }
       ],
       "excludedPaths":[
       ]
    }

Aquí tenemos un fragmento de código .NET que muestra cómo crear una colección de indización espacial activado para todas las rutas que contiene puntos. 

**Crear una colección de indización espacial**

    DocumentCollection spatialData = new DocumentCollection()
    spatialData.IndexingPolicy = new IndexingPolicy(new SpatialIndex(DataType.Point)); //override to turn spatial on by default
    collection = await client.CreateDocumentCollectionAsync(UriFactory.CreateDatabaseUri("db"), spatialData);

Y así es cómo puede modificar una colección existente para aprovechar las ventajas de la indización espacial sobre los puntos que se almacenan en los documentos.

**Modificar una colección existente con la indización espacial**

    Console.WriteLine("Updating collection with spatial indexing enabled in indexing policy...");
    collection.IndexingPolicy = new IndexingPolicy(new SpatialIndex(DataType.Point));
    await client.ReplaceDocumentCollectionAsync(collection);

    Console.WriteLine("Waiting for indexing to complete...");
    long indexTransformationProgress = 0;
    while (indexTransformationProgress < 100)
    {
        ResourceResponse<DocumentCollection> response = await client.ReadDocumentCollectionAsync(UriFactory.CreateDocumentCollectionUri("db", "coll"));
        indexTransformationProgress = response.IndexTransformationProgress;

        await Task.Delay(TimeSpan.FromSeconds(1));
    }

> [AZURE.NOTE] Si la ubicación del valor GeoJSON dentro del documento es incorrecto o no válidos, a continuación, se le no indizar para consultar espacial. Para validar los valores de ubicación con ST_ISVALID y ST_ISVALIDDETAILED.
>
> Si la definición de una colección incluye una clave de partición, no se muestra el progreso de la transformación de indización. 

## <a name="next-steps"></a>Pasos siguientes
Ahora que ha aprender sobre cómo empezar a trabajar con el soporte técnico de geoespaciales en DocumentDB, puede:

- Comenzar a codificar con los [ejemplos de código de .NET geoespaciales en Github](https://github.com/Azure/azure-documentdb-dotnet/blob/fcf23d134fc5019397dcf7ab97d8d6456cd94820/samples/code-samples/Geospatial/Program.cs)
- Obtener práctica con una consulta geoespaciales en la [Animación de la consulta de DocumentDB](http://www.documentdb.com/sql/demo#geospatial)
- Obtenga más información sobre la [Consulta de DocumentDB](documentdb-sql-query.md)
- Más información acerca de [Las directivas de indexación de DocumentDB](documentdb-indexing-policies.md)
