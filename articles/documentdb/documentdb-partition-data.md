<properties 
    pageTitle="Partición y escala en Azure DocumentDB | Microsoft Azure"      
    description="Obtenga información sobre cómo partición funciona en Azure DocumentDB, cómo configurar particiones y dividir las teclas y cómo elegir la clave de partición correcta para su aplicación."         
    services="documentdb" 
    authors="arramac" 
    manager="jhubbard" 
    editor="monicar" 
    documentationCenter=""/> 

<tags 
    ms.service="documentdb" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/20/2016" 
    ms.author="arramac"/> 

# <a name="partitioning-and-scaling-in-azure-documentdb"></a>Partición y escala en DocumentDB de Azure
[Microsoft Azure DocumentDB](https://azure.microsoft.com/services/documentdb/) está diseñado para ayudarle a alcanzar performance predecible rápida y escala perfectamente junto con la aplicación a medida que crece. Este artículo proporciona una descripción general de cómo partición funciona en DocumentDB y describe cómo configurar DocumentDB colecciones para ajustar las aplicaciones de un modo eficaz.

Después de leer este artículo, podrá responder a las preguntas siguientes:   

- ¿Cómo funciona particiones en DocumentDB de Azure?
- Cómo configurar particiones en DocumentDB
- ¿Cuáles son las claves de partición y cómo elegir la clave de partición correcta para mi aplicación?

Para empezar con código, descargue el proyecto de [Ejemplo de controlador de pruebas de rendimiento de DocumentDB](https://github.com/Azure/azure-documentdb-dotnet/tree/a2d61ddb53f8ab2a23d3ce323c77afcf5a608f52/samples/documentdb-benchmark). 

## <a name="partitioning-in-documentdb"></a>Partición en DocumentDB

En DocumentDB, puede almacenar y consultar sin esquemas JSON documentos con los tiempos de respuesta de orden de milisegundos a cualquier escala. DocumentDB proporciona contenedores para almacenar datos denominadas **colecciones**. Colecciones de recursos lógicos y pueden abarcar una partición física o servidores. El número de particiones depende de DocumentDB basándose en el tamaño de almacenamiento y el rendimiento de la preparación de la colección. Todas las particiones DocumentDB tiene una cantidad fija de almacenamiento copia SSD asociada y replicarán para alta disponibilidad. Administración de partición es completamente administrado por DocumentDB de Azure y no tiene que escribir código complejo o administrar las particiones. Colecciones de DocumentDB son **prácticamente ilimitado** en términos de almacenamiento y rendimiento. 

Partición es completamente transparente para la aplicación. DocumentDB admite lecturas rápidas y escribe, consultas SQL y LINQ, lógica transacciones en función de JavaScript, niveles de coherencia y control de acceso específico a través de la API de REST llamadas a un recurso de colección solo. El servicio controla distribuir datos en todas las particiones y enrutar solicitudes de consulta a la derecha partición. 

¿Cómo funciona esto? Cuando se crea una colección de DocumentDB, habrá observado que hay un valor de configuración de **propiedad de clave de partición** que puede especificar. Esta es la propiedad JSON (o la ruta de acceso) dentro de los documentos que se pueden usar DocumentDB para distribuir los datos entre varios servidores o particiones. DocumentDB se hash el valor de clave de partición y usar el resultado hash para determinar la partición en la que se ha almacenado el documento JSON. Todos los documentos con la misma clave de partición se almacenarán en la misma partición. 

Por ejemplo, considere una aplicación que almacena datos sobre los empleados y sus departamentos en DocumentDB. Vamos a elegir `"department"` como la propiedad de clave de partición, para escalar datos por departamento. Todos los documentos de DocumentDB deben contener un obligatorio `"id"` propiedad que debe ser única para todos los documentos con el mismo valor de clave de partición, por ejemplo, `"Marketing`". Todos los documentos almacenados en una colección deben tener una combinación única de clave de partición y id, por ejemplo, `{ "Department": "Marketing", "id": "0001" }`, `{ "Department": "Marketing", "id": "0002" }`, y `{ "Department": "Sales", "id": "0001" }`. En otras palabras, la propiedad compuesta de (partición, Id. de clave) es la clave principal de la colección.

### <a name="partition-keys"></a>Claves de partición
La opción de la clave de partición es una decisión importante que deberá realizar en tiempo de diseño. Debe elegir un nombre de propiedad JSON que tiene una amplia gama de valores y es probable que haya distribuido patrones de acceso. La clave de partición se especifica como una ruta de acceso JSON, por ejemplo, `/department` representa el departamento de propiedad. 

La siguiente tabla muestra ejemplos de definiciones de clave de partición y los valores JSON correspondientes a cada uno.

<table border="0" cellspacing="0" cellpadding="0">
    <tbody>
        <tr>
            <td valign="top"><p><strong>Ruta de acceso de clave de partición</strong></p></td>
            <td valign="top"><p><strong>Descripción</strong></p></td>
        </tr>
        <tr>
            <td valign="top"><p>/Department</p></td>
            <td valign="top"><p>Corresponde al valor JSON de doc.department donde documento es el documento.</p></td>
        </tr>
        <tr>
            <td valign="top"><p>/ propiedades/nombre</p></td>
            <td valign="top"><p>Corresponde al valor JSON de doc.properties.name donde documento es el documento (propiedad anidada).</p></td>
        </tr>
        <tr>
            <td valign="top"><p>/Id</p></td>
            <td valign="top"><p>Corresponde al valor JSON de doc.id (id y partición clave son la misma propiedad).</p></td>
        </tr>
        <tr>
            <td valign="top"><p>/ "nombre de departamento"</p></td>
            <td valign="top"><p>Corresponde al valor JSON de documento ["nombre de departamento"] donde el documento es el documento.</p></td>
        </tr>
    </tbody>
</table>

> [AZURE.NOTE] La sintaxis de la ruta de acceso de clave de partición es similar a la especificación de ruta de acceso para la indización de trayectorias de la directiva con la diferencia de clave que la ruta de acceso corresponde a la propiedad en lugar del valor, es decir, no hay ningún carácter comodín final. ¿Por ejemplo, debe especificar/departamento /? Para ajustar los valores de departamento, pero especificar /department como la definición de clave de partición. La ruta de la clave de partición se indiza de forma implícita y no se puede excluir de indización reemplaza directiva de indización.

Echemos un vistazo a cómo afecta a la opción de clave de partición el rendimiento de la aplicación.

### <a name="partitioning-and-provisioned-throughput"></a>Rendimiento de particiones y aprovisionado
DocumentDB está diseñado para obtener un rendimiento predecible. Cuando se crea una colección, reserve rendimiento en términos de ** [unidades de la solicitud](documentdb-request-units.md) (RU) por segundo**. Cada solicitud se asigna un cargo de unidad de solicitud es proporcional a la cantidad de recursos del sistema como CPU y IO consumido por la operación. Lectura de un documento de 1 kB con coherencia sesión consume 1 unidad de solicitud. Una lectura es 1 RU independientemente del número de elementos almacenados o el número de solicitudes simultáneas que se ejecutan al mismo tiempo. Documentos grandes requieren más unidades de solicitud dependiendo del tamaño. Si conoce el tamaño de las entidades y el número de lecturas que necesita soporte técnico para la aplicación, puede aprovisionar la cantidad exacta de rendimiento necesario para la aplicación de leer las necesidades. 

Cuando DocumentDB almacena los documentos, las distribuye uniformemente entre las particiones basándose en el valor de clave de partición. El rendimiento es también distribuido uniformemente entre las disponibles, es decir, particiones el rendimiento por partición = (rendimiento total por colección) / (número de particiones). 

>[AZURE.NOTE] Para lograr el rendimiento de la colección completo, debe elegir una clave de partición que le permite distribuir uniformemente las solicitudes entre un número de valores de clave de partición distintos.

## <a name="single-partition-and-partitioned-collections"></a>Partición única y colecciones divididas
DocumentDB es compatible con la creación de colecciones única partición y divididas. 

- **Colecciones con particiones** puede abarcar varias particiones y admitir grandes cantidades de almacenamiento y el rendimiento. Debe especificar una clave de partición para la colección.
- **Colecciones de única partición** tienen opciones de precio inferiores y la capacidad de consultar y realizar transacciones en todos los datos de la colección. Tienen los límites de almacenamiento y escalabilidad de una sola partición. No tiene que especificar una clave de partición para estas colecciones. 

![Colecciones divididas en DocumentDB][2] 

Para escenarios que no es necesario grandes volúmenes de almacenamiento o rendimiento, colecciones de una única partición son una buena opción. Tenga en cuenta que las colecciones de única partición la escalabilidad y los límites de almacenamiento de una sola partición, es decir 10 GB de almacenamiento y hasta 10.000 unidades de solicitud por segundo. 

Colecciones divididas pueden admitir una gran cantidad de almacenamiento y el rendimiento. Sin embargo, las ofertas de forma predeterminada se configuran para almacenar hasta 250 GB de almacenamiento y escalar hasta 250.000 unidades de solicitud por segundo. Si necesita más almacenamiento o rendimiento por colección, póngase en contacto con el [Soporte técnico de Azure](documentdb-increase-limits.md) para que estas aumentado para su cuenta.

La siguiente tabla enumera las diferencias de trabajar con una sola partición y colecciones divididas:

<table border="0" cellspacing="0" cellpadding="0">
    <tbody>
        <tr>
            <td valign="top"><p></p></td>
            <td valign="top"><p><strong>Colección de una sola partición</strong></p></td>
            <td valign="top"><p><strong>Colección dividida</strong></p></td>
        </tr>
        <tr>
            <td valign="top"><p>Clave de partición</p></td>
            <td valign="top"><p>Ninguno</p></td>
            <td valign="top"><p>Obligatorio</p></td>
        </tr>
        <tr>
            <td valign="top"><p>Clave principal para el documento</p></td>
            <td valign="top"><p>"Id."</p></td>
            <td valign="top"><p>clave compuesta &lt;clave de partición&gt; y "ID".</p></td>
        </tr>
        <tr>
            <td valign="top"><p>Mínima de almacenamiento</p></td>
            <td valign="top"><p>0 GB</p></td>
            <td valign="top"><p>0 GB</p></td>
        </tr>
        <tr>
            <td valign="top"><p>Almacenamiento máximo</p></td>
            <td valign="top"><p>10 GB</p></td>
            <td valign="top"><p>Ilimitado (250 GB de forma predeterminada)</p></td>
        </tr>
        <tr>
            <td valign="top"><p>Rendimiento mínimo</p></td>
            <td valign="top"><p>unidades de 400 solicitud por segundo</p></td>
            <td valign="top"><p>unidades de 10.000 solicitud por segundo</p></td>
        </tr>
        <tr>
            <td valign="top"><p>Rendimiento máximo</p></td>
            <td valign="top"><p>unidades de 10.000 solicitud por segundo</p></td>
            <td valign="top"><p>Ilimitado (unidades de solicitud 250.000 por segundo de forma predeterminada)</p></td>
        </tr>
        <tr>
            <td valign="top"><p>Versiones API</p></td>
            <td valign="top"><p>Todos los</p></td>
            <td valign="top"><p>API de 2015-12-16 y más reciente</p></td>
        </tr>
    </tbody>
</table>

## <a name="working-with-the-sdks"></a>Trabajar con los SDK

Azure DocumentDB agrega compatibilidad para las particiones automática con la [API de REST versión 2015-12-16](https://msdn.microsoft.com/library/azure/dn781481.aspx). Para crear colecciones divididas, debe descargar versiones SDK 1.6.0 o una versión posterior de una de las plataformas admitidas de SDK (.NET, Node.js, Java, Python). 

### <a name="creating-partitioned-collections"></a>Crear colecciones divididas

En el ejemplo siguiente se muestra un fragmento de .NET para crear una colección para almacenar los datos de telemetría de dispositivo de 20 000 unidades de solicitud de rendimiento por segundo. El SDK establece el valor de OfferThroughput (que a su vez establece la `x-ms-offer-throughput` encabezado de la solicitud en la API de REST). Aquí se establece el `/deviceId` como la clave de partición. La opción de clave de partición se guardará junto con el resto de los metadatos de la colección como el nombre y la directiva de indización.

En este ejemplo, elegimos `deviceId` dado que se sabe que (a), puesto que hay una gran cantidad de dispositivos, escribe puede distribuirse entre particiones uniformemente y lo que nos permite ajustar el tamaño de la base de datos para recopilar grandes volúmenes de datos y (b) muchas de las solicitudes como obtener la última lectura de un dispositivo están orientadas a una sola deviceId y se pueden recuperar de una sola partición.

    DocumentClient client = new DocumentClient(new Uri(endpoint), authKey);
    await client.CreateDatabaseAsync(new Database { Id = "db" });

    // Collection for device telemetry. Here the JSON property deviceId will be used as the partition key to 
    // spread across partitions. Configured for 10K RU/s throughput and an indexing policy that supports 
    // sorting against any number or string property.
    DocumentCollection myCollection = new DocumentCollection();
    myCollection.Id = "coll";
    myCollection.PartitionKey.Paths.Add("/deviceId");

    await client.CreateDocumentCollectionAsync(
        UriFactory.CreateDatabaseUri("db"),
        myCollection,
        new RequestOptions { OfferThroughput = 20000 });
        

> [AZURE.NOTE] Para crear colecciones divididas, debe especificar un valor de rendimiento de 10.000 > solicitud unidades por segundo. Dado que el rendimiento es en múltiplos de 100, este debe estar 10,100 o superior.

Este método realiza una API de REST llamada a DocumentDB y el servicio aprovisionar un número de particiones basada en el rendimiento solicitado. Puede cambiar el rendimiento de una colección de las necesidades de su rendimiento evolucionando. Para obtener más detalles, vea [Niveles de rendimiento](documentdb-performance-levels.md) .

### <a name="reading-and-writing-documents"></a>Leer y escribir documentos

Ahora, vamos a insertar datos en DocumentDB. Aquí tiene una clase de ejemplo que contiene un dispositivo de lectura y a continuación, una llamada a CreateDocumentAsync para insertar un nuevo dispositivo en una colección de lectura.

    public class DeviceReading
    {
        [JsonProperty("id")]
        public string Id;

        [JsonProperty("deviceId")]
        public string DeviceId;

        [JsonConverter(typeof(IsoDateTimeConverter))]
        [JsonProperty("readingTime")]
        public DateTime ReadingTime;

        [JsonProperty("metricType")]
        public string MetricType;

        [JsonProperty("unit")]
        public string Unit;

        [JsonProperty("metricValue")]
        public double MetricValue;
      }

    // Create a document. Here the partition key is extracted as "XMS-0001" based on the collection definition
    await client.CreateDocumentAsync(
        UriFactory.CreateDocumentCollectionUri("db", "coll"),
        new DeviceReading
        {
            Id = "XMS-001-FE24C",
            DeviceId = "XMS-0001",
            MetricType = "Temperature",
            MetricValue = 105.00,
            Unit = "Fahrenheit",
            ReadingTime = DateTime.UtcNow
        });


Vamos a leer el documento mediante su clave de partición y id, actualizar y, a continuación, como paso final, eliminar identificador y clave de partición. Tenga en cuenta que las lecturas incluyen un valor de PartitionKey (correspondiente a la `x-ms-documentdb-partitionkey` encabezado de la solicitud en la API de REST).

    // Read document. Needs the partition key and the ID to be specified
    Document result = await client.ReadDocumentAsync(
      UriFactory.CreateDocumentUri("db", "coll", "XMS-001-FE24C"), 
      new RequestOptions { PartitionKey = new PartitionKey("XMS-0001") });

    DeviceReading reading = (DeviceReading)(dynamic)result;

    // Update the document. Partition key is not required, again extracted from the document
    reading.MetricValue = 104;
    reading.ReadingTime = DateTime.UtcNow;

    await client.ReplaceDocumentAsync(
      UriFactory.CreateDocumentUri("db", "coll", "XMS-001-FE24C"), 
      reading);

    // Delete document. Needs partition key
    await client.DeleteDocumentAsync(
      UriFactory.CreateDocumentUri("db", "coll", "XMS-001-FE24C"), 
      new RequestOptions { PartitionKey = new PartitionKey("XMS-0001") });



### <a name="querying-partitioned-collections"></a>Consultar colecciones divididas

Cuando la consulta de datos en las colecciones divididas DocumentDB enruta automáticamente la consulta a las particiones correspondiente a los valores de clave de partición especificados en el filtro (si hay alguna). Por ejemplo, esta consulta se dirige a solo la partición que contiene la clave de partición "XMS 0001".

    // Query using partition key
    IQueryable<DeviceReading> query = client.CreateDocumentQuery<DeviceReading>(
        UriFactory.CreateDocumentCollectionUri("db", "coll"))
        .Where(m => m.MetricType == "Temperature" && m.DeviceId == "XMS-0001");

La siguiente consulta no tiene un filtro en la clave de partición (DeviceId) y se abanico a todas las particiones donde se ejecuta en el índice de la partición. Nota que tiene que especificar la EnableCrossPartitionQuery (`x-ms-documentdb-query-enablecrosspartition` en la API de REST) que tiene el SDK para ejecutar una consulta en todas las particiones.

    // Query across partition keys
    IQueryable<DeviceReading> crossPartitionQuery = client.CreateDocumentQuery<DeviceReading>(
        UriFactory.CreateDocumentCollectionUri("db", "coll"), 
        new FeedOptions { EnableCrossPartitionQuery = true })
        .Where(m => m.MetricType == "Temperature" && m.MetricValue > 100);

### <a name="parallel-query-execution"></a>Ejecución de consultas en paralelo

El SDK de DocumentDB 1.9.0 y encima de opciones de ejecución de consultas en paralelo de soporte técnico, que le permiten realizar consultas de baja latencia en colecciones divididas, incluso cuando lo necesiten tocar una gran cantidad de particiones. Por ejemplo, la siguiente consulta está configurada para ejecutar en paralelo en todas las particiones.

    // Cross-partition Order By Queries
    IQueryable<DeviceReading> crossPartitionQuery = client.CreateDocumentQuery<DeviceReading>(
        UriFactory.CreateDocumentCollectionUri("db", "coll"), 
        new FeedOptions { EnableCrossPartitionQuery = true, MaxDegreeOfParallelism = 10, MaxBufferedItemCount = 100})
        .Where(m => m.MetricType == "Temperature" && m.MetricValue > 100)
        .OrderBy(m => m.MetricValue);

Puede administrar la ejecución de consultas en paralelo por la optimización de los siguientes parámetros:

- Estableciendo `MaxDegreeOfParallelism`, puede controlar el grado de paralelismo es decir, el número máximo de conexiones de red simultáneas a particiones de la colección. Si se establece en -1, el grado de paralelismo es administrado por el SDK. Si la `MaxDegreeOfParallelism` no está especificada o establecer a 0, que es el valor predeterminado, habrá una conexión de red a las particiones de la colección.
- Estableciendo `MaxBufferedItemCount`, puedan equilibrar la utilización de memoria de lado de latencia y el cliente de consulta. Si omite este parámetro o establézcalo en -1, el número de elementos en el búfer durante la ejecución de consultas en paralelo es administrado por el SDK.

El mismo estado de la colección, una consulta en paralelo devolverá resultados en el mismo orden que en ejecución en serie. Al realizar una consulta de entre partición que incluye la ordenación (ORDER BY o superior), el SDK DocumentDB problemas de la consulta en paralelo en todas las particiones y combina resultados ordenados parcialmente en el lado cliente para producir resultados ordenados globalmente.

### <a name="executing-stored-procedures"></a>Ejecutar procedimientos almacenados

También puede ejecutar transacciones atómicas con respecto a los documentos con el mismo ID de dispositivo, por ejemplo, si desea mantener agregados o el estado más reciente de un dispositivo en un único documento. 

    await client.ExecuteStoredProcedureAsync<DeviceReading>(
        UriFactory.CreateStoredProcedureUri("db", "coll", "SetLatestStateAcrossReadings"),
        new RequestOptions { PartitionKey = new PartitionKey("XMS-001") }, 
        "XMS-001-FE24C");

En la siguiente sección, veremos cómo puede mover a las colecciones divididas de colecciones de única partición.

<a name="migrating-from-single-partition"></a>
### <a name="migrating-from-single-partition-to-partitioned-collections"></a>Migrar desde única partición a las colecciones divididas
Cuando una aplicación con una colección de partición solo necesita un rendimiento más alto (> 10.000 RU/s) o almacenamiento de datos más grande (> 10GB), puede usar la [Herramienta de migración de datos de DocumentDB](http://www.microsoft.com/downloads/details.aspx?FamilyID=cda7703a-2774-4c07-adcc-ad02ddc1a44d) para migrar los datos de la colección única partición a una colección dividida. 

Migrar desde una colección única partición a una colección de divididas

1. Exportar datos de la colección única partición a JSON. Para obtener más información, consulte [Exportar a archivo JSON](documentdb-import-data.md#export-to-json-file) .
2. Importar los datos en una colección con particiones creada con una definición de clave de partición y más de 10.000 unidades de solicitud por segundo rendimiento, tal como se muestra en el ejemplo siguiente. Para obtener más información, consulte [importar a DocumentDB](documentdb-import-data.md#DocumentDBSeqTarget) .

![Migrar los datos a una colección con particiones en DocumentDB][3]  

>[AZURE.TIP] Tiempos de importación, considere la posibilidad de aumentar el número de solicitudes paralelas a 100 o más para aprovechar las ventajas de la mayor rendimiento disponible para las colecciones divididas. 

Ahora que hemos completado los conceptos básicos, echemos un vistazo a algunas consideraciones de diseño importantes al trabajar con claves de partición en DocumentDB.

## <a name="designing-for-partitioning"></a>Diseñar particiones
La opción de la clave de partición es una decisión importante que deberá realizar en tiempo de diseño. Esta sección describen algunas de las compensaciones implicadas en la selección de una clave de partición para la colección.

### <a name="partition-key-as-the-transaction-boundary"></a>Clave de partición como el límite de la transacción
La opción de clave de partición debe equilibrar la necesidad de habilitar el uso de las transacciones de la necesidad de distribuir las entidades entre varias teclas partición para garantizar una solución scalable. En un extremo, puede establecer la misma clave de partición para todos los documentos, pero esto puede limitar la escalabilidad de su solución. En el otro extremo, puede asignar una clave de partición único para cada documento que sería muy scalable pero podría impedir que use transacciones de documento cruzada a través de los procedimientos almacenados y desencadenadores. Una clave de partición ideal es uno que le permite usar consultas eficaces y que tiene suficiente cardinalidad para asegurarse de que su solución es scalable. 

### <a name="avoiding-storage-and-performance-bottlenecks"></a>Evitar el almacenamiento y el rendimiento botella 
También es importante elegir una propiedad que permite distribuir a través de un número de valores distintos. Las solicitudes de la misma clave de partición no pueden superar el rendimiento de una sola partición y reducirá. Así es importante elegir una clave de partición no da como resultado **"zonas activas"** dentro de la aplicación. El tamaño de almacenamiento total de documentos con la misma clave de partición también no puede superar 10 GB de almacenamiento. 

### <a name="examples-of-good-partition-keys"></a>Ejemplos de claves de partición correcto
Estos son algunos ejemplos de cómo seleccionar la clave de partición de la aplicación:

* Si está implementando un back-end de perfil de usuario, el identificador de usuario es una buena opción para la clave de partición.
* Si está almacenando datos IoT estado del dispositivo por ejemplo, un identificador de dispositivo es una buena opción para la clave de partición.
* Si usa DocumentDB para registrar los datos de la serie temporal, el identificador de proceso o el nombre de host es una buena opción para la clave de partición.
* Si tiene una arquitectura de múltiples arrendatario, el identificador del inquilino es una buena opción para la clave de partición.

Tenga en cuenta que en algunos casos de uso (por ejemplo, los perfiles de usuario y IoT descritos anteriormente), la clave de partición puede ser el mismo como id (clave de documento). En otros, como los datos de la serie de tiempo, es posible que tenga una clave de partición es diferente a la id.

### <a name="partitioning-and-loggingtime-series-data"></a>Partición y tiempo de registro-series de datos
Uno de los casos de uso más comunes de DocumentDB es para el registro y telemetría. Es importante elegir una clave de partición correcto, ya que es posible que deba grandes volúmenes de datos de lectura y escritura. La opción dependerán de la lectura y escritura tasas y tipos de consultas que espera para que se ejecute. Aquí encontrará algunas sugerencias sobre cómo elegir una clave de partición correcto.

- Si su caso de uso implica una pequeña tasa de escribe acculumating durante un largo período de tiempo, y necesito consulta por intervalos de las marcas de tiempo y otros filtros, por ejemplo, usando un resumen de la marca de tiempo de fecha como una clave de partición es un buen enfoque. Esto le permite consulta sobre todos los datos para una fecha desde una única partición. 
- Si la carga de trabajo está visible de escritura, que es generalmente más comunes, debe usar una clave de partición que no se basa en la marca de tiempo para que DocumentDB puede distribuir escribe de manera uniforme en un número de particiones. Aquí un nombre de host, ID de proceso, Id. de actividad u otra propiedad con cardinalidad elevada es una buena opción. 
- Un tercer enfoque es un híbrido uno donde tiene varias colecciones, uno para cada día, mes y la clave de partición es una propiedad granular como nombre de host. Esto tiene las ventajas que puede establecer los niveles de rendimiento diferentes en función de la ventana de tiempo, por ejemplo, la colección para el mes actual es aprovisionado con mayor rendimiento dado que sirve de lectura y escritura, mientras que meses anteriores con menor rendimiento puesto que sirven de solo lectura.

### <a name="partitioning-and-multi-tenancy"></a>Soporte para múltiples usuarios y partición
Si está implementando una aplicación de múltiples arrendatario mediante DocumentDB, hay dos modelos principales para implementar usuarios con DocumentDB: una partición clave por inquilino y uno colección por cada inquilino. Estas son las ventajas y desventajas de cada uno:

* Una clave de partición por inquilino: en este modelo, los inquilinos están ubicados en una sola colección. Pero consultas e inserciones para documentos dentro de un inquilino solo se pueden realizar con una sola partición. También puede implementar la lógica de transacciones en todos los documentos dentro de un inquilino. Desde varios inquilinos de compartan una colección, puede ahorrar costos de almacenamiento y el rendimiento de agrupación de recursos para los inquilinos dentro de una sola colección en lugar de aprovisionamiento espacio adicional para cada inquilino. La desventaja es que no tienen aislamiento de rendimiento por cada inquilino. Aumenta el rendimiento y rendimiento aplica a la colección completa de vs destino aumenta para inquilinos.
* Una colección por inquilino: cada inquilino tiene su propia colección. En este modelo, puede reservar rendimiento por cada inquilino. Con nuevo en función de consumo precio modelo de DocumentDB, este modelo es más rentable para las aplicaciones de múltiples usuarios con un pequeño número de inquilinos.

También puede usar un enfoque combinación/niveles que coloca a los inquilinos pequeños y migra a los inquilinos más grandes a su propia colección.

## <a name="next-steps"></a>Pasos siguientes
En este artículo, hemos descrito partición cómo funciona en Azure DocumentDB, cómo crear colecciones divididas y cómo puede seleccionar una clave de partición correcto para la aplicación. 

-   Realizar pruebas de performance con DocumentDB y de escala. Consulte [escala probar con Azure DocumentDB y el rendimiento](documentdb-performance-testing.md) de una muestra.
-   Comenzar a codificar con el [SDK](documentdb-sdk-dotnet.md) o la [API de REST](https://msdn.microsoft.com/library/azure/dn781481.aspx)
-   Obtenga más información sobre [el rendimiento aprovisiona en DocumentDB](documentdb-performance-levels.md)
-   Si desea personalizar la forma en que la aplicación realiza partición, puede conectar su propia implementación de particiones del cliente. Vea [cliente partición de soporte técnico](documentdb-sharding.md).

[1]: ./media/documentdb-partition-data/partitioning.png
[2]: ./media/documentdb-partition-data/single-and-partitioned.png
[3]: ./media/documentdb-partition-data/documentdb-migration-partitioned-collection.png  

 
