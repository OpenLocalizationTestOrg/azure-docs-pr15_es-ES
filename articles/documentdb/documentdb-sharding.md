<properties 
    pageTitle="Cómo implementar una partición de lado cliente con los SDK | Microsoft Azure" 
    description="Obtenga información sobre cómo usar el SDK de Azure DocumentDB a las solicitudes de datos y la ruta de partición (fragmentar) a través de varias colecciones" 
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
    ms.date="10/27/2016" 
    ms.author="arramac"/>

# <a name="how-to-partition-data-using-client-side-support-in-documentdb"></a>Cómo dividir datos mediante la compatibilidad del cliente de DocumentDB

Es compatible con Azure DocumentDB [automáticas partición de colecciones](documentdb-partition-data.md). Sin embargo, hay casos de uso que conviene tiene fino proporciona más control sobre el comportamiento de partición. Para reducir el código reutilizable necesario para las tareas de partición, hemos agregado en .NET, Node.js y SDK de Java que facilita la creación de aplicaciones que se ha escalado a través de varias colecciones de funcionalidad.

En este artículo, deberá echamos un vistazo a las clases e interfaces en el SDK de .NET y cómo puede usarlos para desarrollar aplicaciones divididas. Otro SDK como Java, Node.js y Python admiten similar métodos e interfaces para crear particiones del cliente.

## <a name="client-side-partitioning-with-the-documentdb-sdk"></a>Partición del cliente con el SDK de DocumentDB

Antes de que se profundizar en particiones, vamos a resumir algunos conceptos básicos de DocumentDB que se relacionan con particiones. Cada cuenta de la base de datos de Azure DocumentDB consta de un conjunto de bases de datos, cada una con varias colecciones, cada una de las cuales puede contener procedimientos almacenados, desencadenadores, UDF, documentos y datos adjuntos relacionados. Colecciones pueden ser única partición o divididas a sí mismos y tiene las siguientes propiedades:

- Colecciones ofrecen aislamiento de rendimiento. Por lo tanto, no hay mejora el rendimiento en documentos similares en la misma colección de ordenación. Por ejemplo, para los datos de la serie de tiempo, podría desea colocar los datos del último mes, que es con frecuencia consultar, dentro de una colección con un mayor rendimiento aprovisiona mientras que los datos más antiguos se colocan dentro de las colecciones con bajo rendimiento aprovisiona.
- Transacciones ACID, es decir, procedimientos almacenados y desencadenadores no pueden abarcar una colección. Las transacciones tienen el ámbito de un valor de clave única partición dentro de una colección.
- Colecciones imponen un esquema, por lo que se pueden usar para documentos JSON del mismo tipo o tipos diferentes.

A partir de la versión [1.5. x de los SDK de DocumentDB de Azure](documentdb-sdk-dotnet.md), puede realizar operaciones de documento directamente en una base de datos. La [DocumentClient](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.aspx) utiliza internamente el PartitionResolver que haya especificado para la base de datos a las solicitudes de ruta a la colección correspondiente.

>[AZURE.NOTE] [Servidor partición](documentdb-partition-data.md) introducida en el resto de 2015 API-12-16 y SDK 1.6.0+ deprecates el enfoque de resolución del cliente partición de casos de uso simple. Sin embargo, se particiones del cliente es más flexible y le permite controlar aislamiento de rendimiento entre claves de partición, controlar el grado de paralelismo al leer los resultados de varias particiones y usar particiones enfoques frente hash rango/espacial.

Por ejemplo, en. NET, cada clase PartitionResolver es una implementación concreta de una interfaz de [IPartitionResolver](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.ipartitionresolver.aspx) que tiene tres métodos - [GetPartitionKey](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.ipartitionresolver.getpartitionkey.aspx), [ResolveForCreate](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.ipartitionresolver.resolveforcreate.aspx) y [ResolveForRead](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.ipartitionresolver.resolveforread.aspx). Las consultas LINQ y ReadFeed iteradores efectuar la ResolveForRead internamente iteración en todas las colecciones que coincidan con la clave de partición para la solicitud. Del mismo modo, cree el método ResolveForCreate para enrutar crea a la derecha partición de uso de las operaciones. No existen cambios necesarios para reemplazar, eliminar y leer ya que utilizan los documentos, que ya contienen la referencia a la colección correspondiente.

El SDK también incluye dos clases compatibles con las dos técnicas de particiones canónicas las búsquedas de hash e intervalo a través de un [HashPartitionResolver](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.partitioning.hashpartitionresolver.aspx) y un [RangePartitionResolver](https://msdn.microsoft.com/library/azure/mt126047.aspx). Puede usar estas clases para agregar fácilmente lógica de partición a la aplicación.  

## <a name="add-partitioning-logic-and-register-the-partitionresolver"></a>Agregar lógica de partición y registrar la PartitionResolver 

Esto es un fragmento de código que muestra cómo crear un [HashPartitionResolver](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.partitioning.hashpartitionresolver.aspx) y registrar con la DocumentClient para una base de datos.

```cs
// Create some collections to partition data.
DocumentCollection collection1 = await client.CreateDocumentCollectionAsync(...);
DocumentCollection collection2 = await client.CreateDocumentCollectionAsync(...);

// Initialize a HashPartitionResolver using the "UserId" property and the two collection self-links.
HashPartitionResolver hashResolver = new HashPartitionResolver(
    u => ((UserProfile)u).UserId, 
    new string[] { collection1.SelfLink, collection2.SelfLink });

// Register the PartitionResolver with the database.
this.client.PartitionResolvers[database.SelfLink] = hashResolver;

```

## <a name="create-documents-in-a-partition"></a>Crear documentos en una partición  

Una vez que el PartitionResolver está registrado, puede realizar crea y consultas directamente en la base de datos, como se muestra a continuación. En este ejemplo, el SDK utiliza la PartitionResolver para extraer el identificador de usuario, hash y, a continuación, usar ese valor para enrutar la operación de creación a la colección correcta.

```cs
Document johnDocument = await this.client.CreateDocumentAsync(
    database.SelfLink, new UserProfile("J1", "@John", Region.UnitedStatesEast));
Document ryanDocument = await this.client.CreateDocumentAsync(
    database.SelfLink, new UserProfile("U4", "@Ryan", Region.AsiaPacific, UserStatus.AppearAway));
```

## <a name="create-queries-against-partitions"></a>Crear consultas en particiones  

Puede crear una consulta con el método de [CreateDocumentQuery](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.linq.documentqueryable.createdocumentquery.aspx) pasando en la base de datos y una clave de partición. La consulta devuelve un único conjunto de resultados de todas las colecciones de la base de datos que se asignan a la clave de partición.  

```cs
// Query for John's document by ID - uses PartitionResolver to restrict the query to the partitions 
// containing @John. Again the query uses the database self link, and relies on the hash resolver 
// to route the appropriate collection.
var query = this.client.CreateDocumentQuery<UserProfile>(
    database.SelfLink, null, partitionResolver.GetPartitionKey(johnProfile))
    .Where(u => u.UserName == "@John");
johnProfile = query.AsEnumerable().FirstOrDefault();
```

## <a name="create-queries-against-all-collections-in-the-database"></a>Crear consultas en todas las colecciones de la base de datos 

También puede consultar todas las colecciones de la base de datos y enumerar los resultados como se muestra a continuación, omitiendo el argumento de la clave de partición.

```cs
// Query for all "Available" users. Here since there is no partition key, the query is serially executed 
// across each partition/collection and returns a single result-set. 
query = this.client.CreateDocumentQuery<UserProfile>(database.SelfLink)
    .Where(u => u.Status == UserStatus.Available);
foreach (UserProfile activeUser in query)
{
    Console.WriteLine(activeUser);
}
```

## <a name="hash-partition-resolver"></a>Resolución de la partición de hash
Con particiones hash, las particiones se asignan según el valor de una función de hash, lo que le permite distribuir uniformemente solicitudes y datos a través de un número de particiones. Este enfoque se usa generalmente para partición datos fabrican o consumido desde una gran cantidad de clientes distintas y son útil para almacenar los perfiles de usuario, los elementos del catálogo y los datos de telemetría IoT ("Internet de cosas"). Compatibilidad de partición del servidor de DocumentDB dentro de una colección también utiliza particiones hash.

**Partición de hash:**
![diagrama que ilustra cómo particiones hash distribuye uniformemente las solicitudes en todas las particiones](media/documentdb-sharding/partition-hash.png)

Un esquema de particiones en las colecciones de *N* de hash simple sería llevar cualquier documento, calcular *hash(d) mod N* para determinar qué colección ha colocado en. Pero un problema con esta técnica simple es que no funciona bien cuando agregar nuevas colecciones o quitar colecciones como esto requeriría casi todos los datos al obtener reshuffled. [Coherente hash] (http://citeseerx.ist.psu.edu/viewdoc/summary?doi=10.1.1.23.3738) es un algoritmo conocido que resuelve implementar un esquema de hash que minimiza la cantidad de movimiento de datos necesario durante agregando o quitando colecciones.

La clase [HashPartitionResolver](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.partitioning.hashpartitionresolver.aspx) implementa lógica para generar un timbre de hash coherente sobre la función de hash especificada en la interfaz de [IHashGenerator](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.partitioning.ihashgenerator.aspx) . De forma predeterminada, la HashPartitionResolver usa una función de hash MD5, pero puede intercambiar este comando con su propia implementación de hash. El HashPartitionResolver crea internamente 16 hash o "nodos virtuales" dentro del anillo hash para cada colección para lograr una distribución más uniforme de documentos a través de las colecciones, pero puede variar este número al comercio desactivar asimetría de datos con la cantidad de cálculo del lado cliente.

**Hash coherente con HashPartitionResolver:**
![diagrama que ilustra cómo HashPartitionResolver crea un timbre de hash](media/documentdb-sharding/HashPartitionResolver.JPG)

## <a name="range-partition-resolver"></a>Resolución de la partición de rango

En intervalo de partición, particiones se asignan en función de si es la clave de partición dentro de un rango. Se usa generalmente para particiones con propiedades de marca de tiempo (por ejemplo, eventTime entre el 1 de abril de 2015 y 14 de abril de 2015). La clase [RangePartitionResolver](https://msdn.microsoft.com/library/azure/mt126047.aspx) le ayuda a mantener una asignación entre un rango\<T\> y colección vincular automáticamente. 

[Rango\<T\> ](https://msdn.microsoft.com/library/azure/mt126048.aspx) es una clase simple que administra los intervalos de los tipos que implementan IComparable\<T\> y IEquatable\<T\> como cadenas o números. Para lee y crea, puede pasar en cualquier intervalo arbitrario y la resolución identifica todas las colecciones de candidatos identificando los intervalos de las particiones que forman intersección con el intervalo solicitado. Esta funcionalidad puede ser útil al realizar consultas de rango en los datos de la serie de tiempo.

**Partición de intervalo:**  

![ Diagrama que ilustra cómo rango partición distribuye uniformemente solicita en todas las particiones](media/documentdb-sharding/partition-range.png)  

Un caso especial de las particiones del rango es cuando el intervalo es simplemente un único discreto valor, a veces denominado "partición de búsqueda". Se usa generalmente para particiones por región (por ejemplo, la partición para Escandinavia contiene Noruega, Dinamarca y Suecia) o partición inquilinos en una aplicación de varios inquilino.

## <a name="samples"></a>Ejemplos 

Eche un vistazo a [Github de ejemplos de particiones DocumentDB proyecto](https://github.com/Azure/azure-documentdb-dotnet/tree/287acafef76ad223577759b0170c8f08adb45755/samples/code-samples/Partitioning) que contiene fragmentos de código acerca de cómo usar estos PartitionResolvers y extienden implementar sus propio resoluciones para ajustar los casos de uso específicos, como la siguiente: 

* Cómo especificar una expresión lambda arbitrario para GetPartitionKey y usarlo para implementar claves compuestas de particiones o para dividir los distintos tipos de objetos diferente.
* Cómo crear un simple [LookupPartitionResolver](https://github.com/Azure/azure-documentdb-dotnet/blob/287acafef76ad223577759b0170c8f08adb45755/samples/code-samples/Partitioning/Partitioners/LookupPartitionResolver.cs) que utiliza una tabla de búsqueda manual para realizar particiones. Este patrón se usa generalmente para particiones según los valores discretos como región, identificador del inquilino o aplicación de nombre.
* Cómo crear un [ManagedPartitionResolver](https://github.com/Azure/azure-documentdb-dotnet/blob/287acafef76ad223577759b0170c8f08adb45755/samples/code-samples/Partitioning/Partitioners/ManagedHashPartitionResolver.cs) que se crea automáticamente en función de una plantilla que define un esquema de nombres, IndexingPolicy y procedimientos almacenados que necesitan registrar contra nuevas colecciones de colecciones.
* Cómo crear una combinación de menor [SpilloverPartitionResolver](https://github.com/Azure/azure-documentdb-dotnet/blob/287acafef76ad223577759b0170c8f08adb45755/samples/code-samples/Partitioning/Partitioners/SpilloverPartitionResolver.cs) que simplemente crea nuevas colecciones como colecciones anteriores rellenar hacia arriba.
* Cómo serializar y deserializar el estado de PartitionResolver como JSON, por lo que puede compartir entre procesos y a través de cierres. Puede conservar estos en archivos de configuración, o incluso en una colección de DocumentDB.
* Una clase [DocumentClientHashPartitioningManager](https://github.com/Azure/azure-documentdb-dotnet/blob/287acafef76ad223577759b0170c8f08adb45755/samples/code-samples/Partitioning/Util/DocumentClientHashPartitioningManager.cs) para agregar y quitar las particiones a una base de datos dividida dinámicamente en función de hash coherentes. Internamente utiliza un [TransitionHashPartitionResolver](https://github.com/Azure/azure-documentdb-dotnet/blob/287acafef76ad223577759b0170c8f08adb45755/samples/code-samples/Partitioning/Partitioners/TransitionHashPartitionResolver.cs) a distribuir lee y escribe durante la migración mediante uno de los cuatro modos - leer el esquema de partición antiguo (ReadCurrent), una nueva (ReadNext), combinar los resultados de ambos (ReadBoth) o que no esté disponible durante la migración (ninguno).

Los ejemplos son Abrir origen y le recomendamos que envíe solicitudes de extracción con aportaciones que benefician a otros desarrolladores de DocumentDB. Consulte las [instrucciones de contribución](https://github.com/Azure/azure-documentdb-net/blob/master/Contributing.md) para obtener instrucciones sobre cómo colaborar.  

>[AZURE.NOTE] Colección crea son tasa limitado por DocumentDB, por lo que algunos de los métodos de ejemplo que se muestra aquí pueden tardar unos minutos en completarse.

##<a name="faq"></a>Preguntas más frecuentes
**¿DocumentDB admite la partición de servidor?**

Sí, DocumentDB admite [particiones de servidor](documentdb-partition-data.md). DocumentDB también admite particiones del cliente a través de resoluciones de cliente partición para usos más avanzados.

**¿Cuándo debo usar servidor frente a particiones del cliente?**
Para la mayoría de casos de uso, se recomienda el uso de servidor particiones puesto que controla las tareas administrativas de particiones de datos y las solicitudes de enrutamiento. Sin embargo, si necesita partición de rango o tiene un caso de uso especializado para aislamiento de rendimiento entre los distintos valores de claves de partición, a continuación, particiones del cliente pueden ser el mejor método.

**¿Cómo agregar o quitar una colección a mi esquema de particiones?**

Eche un vistazo a la implementación de DocumentClientHashPartitioningManager en el proyecto de ejemplos para obtener un ejemplo de cómo puede implementar reparticionamiento.

**¿Cómo se mantiene o compartir mi configuración particiones con otros clientes?**

Puede serializar el estado de particionador como JSON y almacenar archivos de configuración o incluso dentro de las colecciones de DocumentDB. Eche un vistazo en el método RunSerializeDeserializeSample en el proyecto de ejemplos para obtener un ejemplo.

**¿Cómo se puede encadenar varias técnicas partición?**

Puede encadenar PartitionResolvers implementando su propios IPartitionResolver que usa internamente uno o más resoluciones existentes. Eche un vistazo a TransitionHashPartitionResolver en el proyecto de ejemplos para obtener un ejemplo.

##<a name="references"></a>Referencias
* [Servidor particiones en DocumentDB](documentdb-partition-data.md)
* [Colecciones de DocumentDB y niveles de rendimiento](documentdb-performance-levels.md)
* [Ejemplos de código de particiones en Github](https://github.com/Azure/azure-documentdb-dotnet/tree/287acafef76ad223577759b0170c8f08adb45755/samples/code-samples/Partitioning)
* [Documentación de DocumentDB .NET SDK en MSDN](https://msdn.microsoft.com/library/azure/dn948556.aspx)
* [Ejemplos de .NET DocumentDB](https://github.com/Azure/azure-documentdb-net)
* [Límites de DocumentDB](documentdb-limits.md)
* [DocumentDB Blog en sugerencias de rendimiento](https://azure.microsoft.com/blog/2015/01/20/performance-tips-for-azure-documentdb-part-1-2/)
 
