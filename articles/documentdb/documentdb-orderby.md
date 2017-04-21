<properties 
    pageTitle="Ordenar datos DocumentDB con Order By | Microsoft Azure" 
    description="Obtenga información sobre cómo usar ORDER BY en DocumentDB consultas en LINQ y SQL y cómo especificar una directiva de indexación de ORDER BY consultas." 
    services="documentdb" 
    authors="arramac" 
    manager="jhubbard" 
    editor="cgronlun" 
    documentationCenter=""/>

<tags 
    ms.service="documentdb" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/03/2016" 
    ms.author="arramac"/>

# <a name="sorting-documentdb-data-using-order-by"></a>Ordenar datos DocumentDB con Order By
Microsoft Azure DocumentDB admite consultar documentos usando SQL sobre documentos JSON. Resultados de la consulta se pueden ordenar con la cláusula ORDER BY en las instrucciones de la consulta SQL.

Después de leer este artículo, podrá responder a las preguntas siguientes: 

- ¿Cómo consultar con Order By?
- ¿Cómo se puede configurar una directiva de indexación para ordenar por?
- ¿Cuál es la siguiente?

También se proporcionan [ejemplos](#samples) y [preguntas más frecuentes](#faq) .

Para obtener una referencia completa en consultas de SQL, consulte el [tutorial de consulta DocumentDB](documentdb-sql-query.md).

## <a name="how-to-query-with-order-by"></a>Cómo consultar con Order By
Al igual que en ANSI-SQL, ahora se puede incluir una cláusula Order By opcional en las instrucciones SQL al consultar DocumentDB. La cláusula puede incluir un argumento ASC o DESC opcional para especificar el orden en que se deben recuperar resultados. 

### <a name="ordering-using-sql"></a>Orden mediante SQL
Por ejemplo aquí es una consulta para recuperar los libros de 10 mejores en orden descendente de los títulos. 

    SELECT TOP 10 * 
    FROM Books 
    ORDER BY Books.Title DESC

### <a name="ordering-using-sql-with-filtering"></a>Orden mediante SQL con filtrado
Puede ordenar con cualquier propiedad anidada dentro de documentos como Books.ShippingDetails.Weight y, a continuación, puede especificar filtros adicionales en la cláusula WHERE en combinación con Order By, como en este ejemplo:

    SELECT * 
    FROM Books 
    WHERE Books.SalePrice > 4000
    ORDER BY Books.ShippingDetails.Weight

### <a name="ordering-using-the-linq-provider-for-net"></a>Ordenación con el proveedor de LINQ para .NET
Usar la versión 1.2.0 .NET SDK y versiones posteriores, también puede usar la cláusula OrderBy() o OrderByDescending() dentro de las consultas LINQ como en este ejemplo:

    foreach (Book book in client.CreateDocumentQuery<Book>(UriFactory.CreateDocumentCollectionUri("db", "books"))
        .OrderBy(b => b.PublishTimestamp)
        .Take(100))
    {
        // Iterate through books
    }

DocumentDB admite pedidos numéricos, cadena o propiedad booleana por consulta, con los tipos de consulta adicionales próximamente. Para obtener más detalles, consulte [Cuál es la siguiente](#Whats_coming_next) .

## <a name="configure-an-indexing-policy-for-order-by"></a>Configurar una directiva de indexación para ordenar por

Recuerde que DocumentDB es compatible con dos tipos de índices (Hash y rango), pueden establecer propiedades concretas rutas de acceso, tipos de datos (cadenas o números) y los valores de precisión diferente (precisión máxima o un valor de precisión fija). Puesto que DocumentDB utiliza Hash indización como predeterminado, debe crear una nueva colección con una directiva de indexación personalizada con el rango de números, cadenas o ambos, para poder usar Order By. 

>[AZURE.NOTE] Índices de rango de cadena se han introducido en 7 de julio de 2015 con la API de REST versión 2015-06-03. Para crear directivas para ordenar por contra cadenas, debe utilizar SDK versión 1.2.0 de .NET SDK o versión 1.1.0 Python, Node.js o SDK de Java.
>
>Antes de la API de REST versión 2015-06-03, la directiva de indización de colección predeterminada era Hash para cadenas y números. Esto se cambió a Hash cadenas e intervalo de números. 

Para obtener más detalles, consulte [DocumentDB directivas de indexación](documentdb-indexing-policies.md).

### <a name="indexing-for-order-by-against-all-properties"></a>Indización de Order By frente a todas las propiedades
He aquí cómo puede crear una colección con "Todos los intervalo" indización para ordenar por frente a todos los numéricos o propiedades de cadena que aparecen en los documentos JSON dentro de ella. Aquí se invalidar el tipo de índice predeterminado para los valores de cadena en rango y, en la precisión máxima (-1).
                   
    DocumentCollection books = new DocumentCollection();
    books.Id = "books";
    books.IndexingPolicy = new IndexingPolicy(new RangeIndex(DataType.String) { Precision = -1 });
    
    await client.CreateDocumentCollectionAsync(UriFactory.CreateDatabaseUri("db"), books);  

>[AZURE.NOTE] Tenga en cuenta que Order By sólo devolverá resultados de los tipos de datos (cadena y número) que están indizados con un RangeIndex. Por ejemplo, si tiene el valor predeterminado indización directiva que sólo tiene RangeIndex en números, una cláusula Order By con una trayectoria con valores de cadena no devolverá documentos.

### <a name="indexing-for-order-by-for-a-single-property"></a>Para ordenar por la indización para una única propiedad
Aquí es cómo puede crear un conjunto con la indización para Order By con solo la propiedad título, que es una cadena. Existen dos rutas de acceso, uno para la propiedad Título ("título /?") con la indización de intervalo y otro para todas las propiedades con la combinación de indización predeterminada, que es Hash para cadenas y rango de números.                    
    
    booksCollection.IndexingPolicy.IncludedPaths.Add(
        new IncludedPath { 
            Path = "/Title/?", 
            Indexes = new Collection<Index> { 
                new RangeIndex(DataType.String) { Precision = -1 } } 
            });
    
    await client.CreateDocumentCollectionAsync(UriFactory.CreateDatabaseUri("db"), booksCollection);  


## <a name="samples"></a>Ejemplos
Eche un vistazo a este [proyecto de ejemplos de Github](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/code-samples/Queries) que se muestra cómo usar Order By, incluidos crear directivas de indización y paginación con Order By. Los ejemplos son Abrir origen y le recomendamos que envíe solicitudes de extracción con aportaciones que podrían beneficiarse otros desarrolladores DocumentDB. Consulte las [instrucciones de contribución](https://github.com/Azure/azure-documentdb-net/blob/master/Contributing.md) para obtener instrucciones sobre cómo colaborar.  

## <a name="faq"></a>Preguntas más frecuentes

**¿Qué es el consumo de unidad solicitar (RU) previsto de Order By consultas?**

Dado que Order By utiliza el índice de DocumentDB para las búsquedas, el número de unidades de solicitud consumido por Order By consultas será similar a las consultas equivalentes sin Order By. Al igual que cualquier otra operación en DocumentDB, el número de unidades de la solicitud depende de las tamaños y formas de documentos, así como la complejidad de la consulta. 


**¿Qué es la sobrecarga de indización esperado para ordenar por?**

La indización sobrecarga de almacenamiento será proporcional al número de propiedades. En el peor caso, la sobrecarga de índice será 100% de los datos. No hay ninguna diferencia de sobrecarga de rendimiento (unidades solicitar) entre rango/Order By indización y la indización de Hash predeterminado.

**¿Cómo se puede consultar Mis datos existentes en DocumentDB con Order By?**

Para ordenar los resultados de la consulta mediante Order By, debe modificar la directiva de indización de la colección de usar un tipo de índice de intervalo con la propiedad usado para ordenar. Consulte [modificación de la directiva de indización](documentdb-indexing-policies.md#modifying-the-indexing-policy-of-a-collection). 

**¿Cuáles son las limitaciones actuales de Order By?**

Se especifica Order By sólo con respecto a una propiedad, ambos numérico o cadena cuando es el rango de indizado con la precisión máxima (-1).

No se puede realizar lo siguiente:
 
- Order By con propiedades de cadena interna como id, _rid y _self (próximamente).
- Order By con propiedades derivadas el resultado de una combinación de dentro de un documento (próximamente).
- Ordenar por varias propiedades (próximamente).
- Order By con consultas en bases de datos, colecciones, usuarios, permisos o datos adjuntos (próximamente).
- Order By con propiedades calculadas, por ejemplo, el resultado de una expresión o una función UDF/incorporada-in.

Order By no es se admite entre partición consultas cuando se utiliza el Explorador de consulta en el portal de Azure.

## <a name="troubleshooting"></a>Solución de problemas

Si recibe un error que Order By no es compatible, compruebe que está utilizando una versión de [SDK](documentdb-sdk-dotnet.md) que admita Order By. 

## <a name="next-steps"></a>Pasos siguientes

Se bifurcan el [proyecto de ejemplos de Github](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/code-samples/Queries) y empezar a ordenar sus datos. 

## <a name="references"></a>Referencias
* [Referencia de consulta DocumentDB](documentdb-sql-query.md)
* [Referencia de la directiva de indización de DocumentDB](documentdb-indexing-policies.md)
* [Referencia de SQL DocumentDB](https://msdn.microsoft.com/library/azure/dn782250.aspx)
* [Orden de DocumentDB ejemplos](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/code-samples/Queries)
 

