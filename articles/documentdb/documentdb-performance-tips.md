<properties 
    pageTitle="Sugerencias de rendimiento DocumentDB | Microsoft Azure" 
    description="Obtenga información sobre las opciones de configuración de cliente para mejorar el rendimiento de la base de datos de Azure DocumentDB"
    keywords="cómo mejorar el rendimiento de la base de datos"
    services="documentdb" 
    authors="mimig1" 
    manager="jhubbard" 
    editor="" 
    documentationCenter=""/>

<tags 
    ms.service="documentdb" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/17/2016" 
    ms.author="mimig"/>

# <a name="performance-tips-for-documentdb"></a>Sugerencias de rendimiento para DocumentDB

DocumentDB Azure es rápida y flexible base de datos distribuida que escala sin problemas con la garantía de latencia y el rendimiento. No tiene que realizar cambios de arquitectura importantes o escribir código complejo para ajustar el tamaño de la base de datos con DocumentDB. Escalar hacia arriba y abajo es tan sencillo como realizar una llamada a la API único o el [método SDK llamar](documentdb-performance-levels.md#changing-performance-levels-using-the-net-sdk). Sin embargo, debido a que DocumentDB se tiene acceso a través de llamadas de red son las optimizaciones de cliente que puede hacer para conseguir un rendimiento óptimo.

Por lo que si quiere pedir "¿Cómo puedo mejorar el rendimiento de la base de datos?" Tenga en cuenta las siguientes opciones:

## <a name="networking"></a>Redes
<a id="direct-connection"></a>

1. **Directiva de conexión: usar el modo de conexión directa**
    
    ¿Cómo un cliente se conecta a Azure DocumentDB tiene implicaciones importantes en el rendimiento, especialmente en términos de latencia de cliente observado. Existen dos opciones de configuración de la clave para configurar la directiva de conexión: el *modo* de conexión y el [ *protocolo*de conexión](#connection-protocol)del cliente.  Los dos modos disponibles son:

    1. Modo de puerta de enlace (predeterminado)
    2. Modo directo

    Puesto que DocumentDB es un sistema de almacenamiento distribuido, recursos de DocumentDB como colecciones se dividen en varios equipos y cada partición se duplica para alta disponibilidad. Lógica traducción de dirección física se mantiene en una tabla de enrutamiento, que también está disponible internamente como un recurso.

    En modo de puerta de enlace, los equipos de puerta de enlace DocumentDB realizan esta ruta, lo que permite que el código de cliente simple y compacta. Una aplicación cliente envía solicitudes a los equipos de puerta de enlace DocumentDB que traducen la lógico URI en la convocatoria de la dirección física de nodo de back-end y reenvían la solicitud correctamente.  Por el contrario, en modo directo clientes deben mantener – y actualizar periódicamente: una copia de esta tabla de enrutamiento y conectar directamente a los nodos de DocumentDB back-end.

    Modo de puerta de enlace es compatible en todas las plataformas SDK y es el valor predeterminado configurado.  Si la aplicación se ejecuta en una red corporativa con restricciones de firewall estricto, el modo de puerta de enlace es la mejor opción ya que utiliza el puerto HTTPS estándar y un único extremo. El compromiso de rendimiento, sin embargo, es que el modo de puerta de enlace implica un salto de red adicional cada vez que se lee o se escribe en DocumentDB datos.   Por este motivo, el modo directo ofrece mejor rendimiento debido a menos saltos de red.

2. **Directiva de conexión: Use el protocolo TCP**

    Al aprovechar el modo directo, hay dos opciones de protocolo disponibles:

    - TCP
    - HTTPS

    DocumentDB ofrece una sencilla y abra el modelo de programación REST sobre HTTPS. Además, ofrece un protocolo TCP eficaz, que es también RESTful en su modelo de comunicación y está disponible a través del cliente de .NET SDK. TCP directo y HTTPS utilizar SSL para la autenticación inicial y el tráfico cifrado. Para mejorar el rendimiento, utilice el protocolo TCP cuando sea posible. 

    Cuando se usa TCP en modo de puerta de enlace, el puerto TCP 443 es el puerto DocumentDB y 10250 es el puerto de MongoDB API. Cuando se utiliza TCP en modo directo, además de los puertos de puerta de enlace, tendrá que garantizar el puerto comprendido entre 10000 y 20000 está abierta ya DocumentDB utiliza puertos TCP dinámicos. Si estos puertos no están abiertos y se intenta utilizar TCP, recibirá un error 503 Servicio no disponible. 

    El modo de conectividad se configura durante la construcción de la instancia de DocumentClient con el parámetro ConnectionPolicy. Si utiliza el modo directo, el protocolo también puede establecer el parámetro ConnectionPolicy.

        var serviceEndpoint = new Uri("https://contoso.documents.net");
        var authKey = new "your authKey from Azure Mngt Portal";
        DocumentClient client = new DocumentClient(serviceEndpoint, authKey, 
        new ConnectionPolicy
        {
            ConnectionMode = ConnectionMode.Direct,
            ConnectionProtocol = Protocol.Tcp
        });

    Porque TCP solo se admite en modo directo, si se utiliza el modo de puerta de enlace, el protocolo HTTPS siempre se usa para comunicarse con la puerta de enlace y se omite el valor de protocolo en la ConnectionPolicy.

    ![Ilustración de la directiva de conexión DocumentDB](./media/documentdb-performance-tips/azure-documentdb-connection-policy.png)

3. **Llamar a OpenAsync para evitar la latencia de inicio en la primera solicitud**

    De forma predeterminada, la primera solicitud tendrá una latencia mayor porque tiene que capturar la tabla de enrutamiento de dirección. Para evitar esta latencia de inicio en la primera solicitud, debe llamar a OpenAsync() una vez durante la inicialización como sigue.

        await client.OpenAsync();
<a id="same-region"></a>
4. **Colocar a los clientes en la misma región Azure rendimiento**

    Cuando sea posible, coloque todas las aplicaciones de llamar a DocumentDB en la misma región como la base de datos de DocumentDB. Para ver una comparación aproximada, llamadas a DocumentDB dentro de la misma región completan dentro de ms 1 y 2, pero la latencia entre el oeste y la costa este de Estados Unidos es > 50 ms. Esta latencia es probable que depende para solicitar la ruta tomada por la solicitud cuando pasa el límite del centro de datos de Azure desde el cliente. Asegurarse de que la aplicación de llamada se encuentra en la misma región Azure como extremo DocumentDB aprovisiona se logra la menor latencia posible. Para obtener una lista de regiones disponibles, vea [Áreas de Azure](https://azure.microsoft.com/regions/#services).

    ![Ilustración de la directiva de conexión DocumentDB](./media/documentdb-performance-tips/azure-documentdb-same-region.png)
<a id="increase-threads"></a>
5. **Aumentar el número de subprocesos y tareas**

    Puesto que se realizan las llamadas a DocumentDB en la red, debe variar el grado de paralelismo de las solicitudes de modo que la aplicación cliente dedicará poco tiempo de espera entre solicitudes. Por ejemplo, si está usando. [Tareas paralelas biblioteca](https://msdn.microsoft.com//library/dd460717.aspx), de NET crear en el orden de 100s de tareas leer o escribir en DocumentDB.

## <a name="sdk-usage"></a>Uso SDK

1. **Instalar el SDK más reciente**

    El SDK de DocumentDB constantemente que se han mejorado para proporcionar el mejor rendimiento. Consulte las páginas de [DocumentDB SDK](documentdb-sdk-dotnet.md) para determinar el SDK más reciente y revisar mejoras. 

2. **Usar a un cliente de DocumentDB singleton para la duración de la aplicación**
  
    Tenga en cuenta que cada instancia de DocumentClient es subprocesos y realiza la administración de la conexión eficaz y dirección almacenamiento en caché cuando se trabaja en modo directo. Para permitir la administración de la conexión eficaz y mejor rendimiento por DocumentClient, se recomienda usar una instancia de DocumentClient por cada dominio de aplicación para la duración de la aplicación.
<a id="max-connection"></a>
3. **Aumentar System.Net MaxConnections por host**

    Las solicitudes de DocumentDB realizadas sobre HTTPS/resto de forma predeterminada y están sujetos al límite de conexión predeterminado por nombre de host o la dirección IP. Debe configurar la MaxConnections en un valor superior (100-1000) para que la biblioteca de cliente puede utilizar varias conexiones simultáneas a DocumentDB. En el SDK de .NET 1.8.0 y versiones posteriores, el valor predeterminado de [ServicePointManager.DefaultConnectionLimit](https://msdn.microsoft.com/library/system.net.servicepointmanager.defaultconnectionlimit.aspx) es 50 y para cambiar el valor, puede establecer la [Documents.Client.ConnectionPolicy.MaxConnectionLimit](https://msdn.microsoft.com/en-us/library/azure/microsoft.azure.documents.client.connectionpolicy.maxconnectionlimit.aspx) a un valor más alto.  

4. **Ajustar las consultas en paralelo para las colecciones divididas**

     DocumentDB .NET SDK versión 1.9.0 y por encima de la compatibilidad con las consultas en paralelo, lo que le permite consultar una colección dividida en paralelo (vea los [ejemplos de código](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/code-samples/Queries/Program.cs) relacionados para obtener más información y [trabajar con los SDK](documentdb-partition-data.md#working-with-the-sdks) ). Las consultas en paralelo están diseñadas para mejorar la latencia de consultas y el rendimiento con su equivalente de la serie. Las consultas en paralelo proporcionan dos parámetros que los usuarios pueden ajustar para adaptar sus requisitos, MaxDegreeOfParallelism (a): para controlar el número máximo de particiones que se pueden consultar en paralelo y (b) MaxBufferedItemCount: controlar el número de resultados recopilados previamente. 
    
    (a) ***ajuste MaxDegreeOfParallelism\: *** 
    funciona de la consulta en paralelo consultando varias particiones en paralelo. Sin embargo, los datos de un recopilar dividida individual se obtienen en serie con respecto a la consulta. Por lo tanto, establecer el MaxDegreeOfParallelism en el número de particiones no tiene la posibilidad de máxima de lograr la mayoría consulta de rendimiento, siempre todas las demás condiciones del sistema siguen siendo las mismas. Si no conoce el número de particiones, puede establecer la MaxDegreeOfParallelism a un número elevado y el sistema elija mínimo (número de particiones, entrada proporcionado por el usuario) como la MaxDegreeOfParallelism. 
    
    Es importante que tenga en cuenta que las consultas en paralelo producen las mejores ventajas si los datos se distribuyen uniformemente a todas las particiones con respecto a la consulta. Si la colección dividida se divide la manera que todo o una mayoría de los datos devueltos por una consulta se concentra en unas particiones (una partición en peor caso), a continuación, el rendimiento de la consulta se podría esté congestionado por las particiones. 
    
    (b) ***ajuste MaxBufferedItemCount\: *** 
    paralelo consulta está diseñada para capturar previamente resultados mientras se procesa el lote actual de resultados por el cliente. Ayuda la obtención previa mejora de latencia general de una consulta. MaxBufferedItemCount es el parámetro para limitar la cantidad de resultados recopilados previamente. Establecer MaxBufferedItemCount al número de resultados esperado (o un número mayor) permite la consulta benefician de obtención previa. 
    
    Tenga en cuenta que obtención previa funciona del mismo modo con independencia de la MaxDegreeOfParallelism y hay un único búfer de los datos de todas las particiones.  

5. **Activar catálogo global del servidor**
    
    Puede ayudar a reducir la frecuencia de recolección en algunos casos. En. NET, establezca [gcServer](https://msdn.microsoft.com/library/ms229357.aspx) en true.

6. **Implementar espera a intervalos RetryAfter**
 
    Durante las pruebas de rendimiento, debe aumentar la carga hasta obtener acelerado una pequeña tasa de solicitudes. Si acelerado, la aplicación cliente debe espera Acelerador para el intervalo de reintento especificado por el servidor. Respetar la espera garantiza que pasan cantidad mínima de tiempo esperando entre reintentos. Compatibilidad con la directiva de reintento se incluye en la versión 1.8.0 y encima del DocumentDB [.NET](documentdb-sdk-dotnet.md) y [Java](documentdb-sdk-java.md)y la versión 1.9.0 y encima de la [Node.js](documentdb-sdk-node.md) y [Python](documentdb-sdk-python.md). Para obtener más información, vea [limita el rendimiento reservado de Exceeding](documentdb-request-units.md#exceeding-reserved-throughput-limits) y [RetryAfter](https://msdn.microsoft.com/library/microsoft.azure.documents.documentclientexception.retryafter.aspx).

7. **Cambiar la escala de la carga de trabajo de cliente**

    Si está probando en niveles de alto rendimiento (> 50.000 RU/s), la aplicación cliente puede ser la botella debido a que el equipo de límite fuera de uso de CPU o la red. Si llega a este punto, puede seguir push adicional de la cuenta DocumentDB escalado de las aplicaciones cliente a través de varios servidores.

8. **Documento de caché URI de menor latencia de lectura**

    Documento de caché URI siempre que sea posible para mejorar el rendimiento de lectura.
<a id="tune-page-size"></a>
9. **Ajustar el tamaño de página para las consultas o leer fuentes para mejorar el rendimiento**

    Cuando realiza un bloque de lectura de documentos con lectura fuente funcionalidad (por ejemplo, ReadDocumentFeedAsync) o al emitir una consulta SQL DocumentDB, los resultados se devuelven de forma segmentada si el conjunto de resultados es demasiado grande. De forma predeterminada, los resultados se devuelven en fragmentos de 100 elementos o 1 MB, el límite es de visitas primero. 

    Para reducir el número de red redondear viajes necesarios para recuperar todos los resultados aplicables, puede aumentar el tamaño de página mediante el encabezado de la solicitud de x ms max elemento recuento a hasta 1000. En casos donde se necesita para mostrar solo unos resultados, por ejemplo, si la API de aplicación o de la interfaz de usuario devuelve sólo 10 resultados una hora, también puede reducir el tamaño de página a 10 para reducir el rendimiento consumido para lecturas y consultas.

    También puede establecer el tamaño de página mediante el SDK DocumentDB disponible.  Por ejemplo:
    
        IQueryable<dynamic> authorResults = client.CreateDocumentQuery(documentCollection.SelfLink, "SELECT p.Author FROM Pages p WHERE p.Title = 'About Seattle'", new FeedOptions { MaxItemCount = 1000 });

10. **Aumentar el número de subprocesos y tareas**

    Vea [aumentar el número de subprocesos y tareas](#increase-threads) en la sección de redes.

## <a name="indexing-policy"></a>Directiva de indización

1. **Usar la indización diferida tasas de recopilación de tiempo máximo más rápido**

    DocumentDB permite especificar, en el nivel de colección de una directiva de indexación, que le permite elegir si desea que los documentos en una colección de indizar automáticamente o no.  Además, también puede elegir entre sincrónico (coherente) y actualizaciones de índice (Lazy) asincrónicas. De forma predeterminada, el índice se actualiza sincrónicamente en cada Insertar, reemplazar o eliminar de un documento a la colección. Sincrónicamente modo permite que las consultas respetar el mismo [nivel de coherencia](documentdb-consistency-levels.md) que de las lecturas documento sin demora para el índice "ponerse al día".
    
    Indización diferida puede considerar para escenarios en los que los datos se escriben en ráfagas y desea amortización el trabajo necesario para indizar el contenido durante un período de tiempo más largo. Indización diferida también le permite utilizar de forma eficaz el rendimiento de preparación y atender las solicitudes de escritura en momentos con una latencia mínima. Es importante no obstante, tenga en cuenta que cuando se habilita la indización lenta, resultados de la consulta será finalmente uniforme independientemente del nivel de coherencia configurado para la cuenta de DocumentDB.

    Por lo tanto, coherente indización modo (IndexingPolicy.IndexingMode es coherente) incurre el gasto de unidad de solicitud máxima por escritura mientras Lazy indización modo (IndexingPolicy.IndexingMode es Lazy) y sin indización (IndexingPolicy.Automatic está establecida en falso) tienen un coste cero indización en el momento de escritura.

2. **Excluir rutas de acceso sin usar la indización de escritura más rápido**

    Directiva de indización de DocumentDB también le permite especificar qué rutas de acceso del documento para incluir o excluir de la indización aprovechando rutas de indización (IndexingPolicy.IncludedPaths y IndexingPolicy.ExcludedPaths). El uso de la indización de rutas puede ofrecer rendimiento mejorado de escritura y almacenamiento de índice inferior para escenarios en los que los patrones de consulta se conocen con antelación, como costos indización directamente son relacionados con el número de rutas de acceso únicas indizados.  Por ejemplo, el código siguiente muestra cómo excluir toda una sección de los documentos (también conocido como un subárbol) de indización utilizando la "*" comodín.

        var collection = new DocumentCollection { Id = "excludedPathCollection" };
        collection.IndexingPolicy.IncludedPaths.Add(new IncludedPath { Path = "/*" });
        collection.IndexingPolicy.ExcludedPaths.Add(new ExcludedPath { Path = "/nonIndexedContent/*");
        collection = await client.CreateDocumentCollectionAsync(UriFactory.CreateDatabaseUri("db"), excluded);

    Para obtener más información, vea [DocumentDB directivas de indexación](documentdb-indexing-policies.md).

## <a name="throughput"></a>Rendimiento
<a id="measure-rus"></a>

1. **Medir y el rendimiento de solicitud inferior unidades por segundo uso**

    DocumentDB ofrece un amplio conjunto de operaciones de base de datos, incluidas las consultas relacionales y jerárquicas con UDF, procedimientos almacenados y desencadenadores – todo funcionamiento en los documentos dentro de una colección de base de datos. Los costos asociados con cada una de estas operaciones varían en función de la CPU, IO y memoria necesaria para completar la operación. En lugar de pensar y administración de recursos de hardware, puede considerar que una unidad de solicitud (RU) como una medida de los recursos necesarios para realizar diversas operaciones de base de datos y una solicitud de aplicación de servicio.

    [Solicitar unidades](documentdb-request-units.md) aprovisionados para cada cuenta de la base de datos en función del número de unidades de capacidad que compra. Consumo de unidades de la solicitud se evalúa como una tasa por segundo. Aplicaciones que superen la tasa de unidad de solicitud de preparación de su cuenta es limitada hasta que la tasa cae por debajo del nivel reservado para la cuenta. Si su aplicación requiere un nivel de rendimiento, puede comprar unidades de capacidad adicional.

    La complejidad de una consulta afecta cuántas unidades solicitar se consumen para una operación. El número de predicados, naturaleza de los predicados, número de UDF y el tamaño de todo el conjunto de datos de origen influir en el costo de operaciones de consulta.

    Medir la sobrecarga de cualquier operación (crear, actualizar o eliminar), inspeccionar el encabezado x ms solicitud cargo (o la propiedad RequestCharge equivalente en ResourceResponse<T> o FeedResponse<T> en el SDK de .NET) para medir el número de unidades de solicitud consumido por estas operaciones.

        // Measure the performance (request units) of writes
        ResourceResponse<Document> response = await client.CreateDocumentAsync(collectionSelfLink, myDocument);
        Console.WriteLine("Insert of document consumed {0} request units", response.RequestCharge);
        // Measure the performance (request units) of queries
        IDocumentQuery<dynamic> queryable = client.CreateDocumentQuery(collectionSelfLink, queryString).AsDocumentQuery();
        while (queryable.HasMoreResults)
             {
                  FeedResponse<dynamic> queryResponse = await queryable.ExecuteNextAsync<dynamic>();
                  Console.WriteLine("Query batch consumed {0} request units", queryResponse.RequestCharge);
             }
        
    El gasto de solicitud devuelto en este encabezado es una fracción del rendimiento aprovisiona (es decir, 2000 RUs / segundo). Por ejemplo, si la consulta anterior devuelve 1000 1KB documentos, el coste de la operación será 1000. Por lo tanto, en un segundo, el servidor respeta sólo dos dichas solicitudes antes de limitar las solicitudes subsiguientes. Para obtener más información, vea [unidades de la solicitud](documentdb-request-units.md) y la [Calculadora de unidades de la solicitud](https://www.documentdb.com/capacityplanner).

2. **Controlar la tasa de limitación o solicitud de tasa demasiado grande**

    Cuando un cliente intenta supere el rendimiento reservado para una cuenta, no hay ninguna degradación del rendimiento en el servidor y sin uso de la capacidad de rendimiento más allá del nivel reservado. El servidor IntruShield finalizar la solicitud con RequestRateTooLarge (código de estado HTTP 429) y devolver el encabezado x intentos ms después de ms que indica la cantidad de tiempo en milisegundos, que el usuario debe esperar antes de volviendo a intentar la solicitud.
 
        HTTP Status 429,
        Status Line: RequestRateTooLarge
        x-ms-retry-after-ms :100

    Todo implícitamente el SDK detecta esta respuesta, respeta el encabezado especificado por el servidor después de reintento y vuelva a intentar la solicitud. A menos que su cuenta está accediendo simultáneamente varios clientes, el siguiente reintento se realizará correctamente.

    Si tiene más de un cliente de forma acumulativa operativo constantemente por encima de la tasa de solicitud, el número de reintentos de forma predeterminada establecido actualmente a 9 internamente por el cliente no puede ser suficiente; en este caso, el cliente inicia una DocumentClientException con código de estado 429 a la aplicación. El número de reintentos de forma predeterminada puede cambiarse estableciendo la RetryOptions en la instancia de ConnectionPolicy. De forma predeterminada, se devuelve el DocumentClientException con código de estado 429 después de un tiempo de espera acumulado de 30 segundos si la convocatoria sigue funcionando por encima de la tasa de solicitud. Esto ocurre incluso cuando el número de reintentos actual es menor que el número máximo de reintentos, ya sea el valor predeterminado de 9 o un valor definido por el usuario.

    Mientras el comportamiento de reintento automatizado ayuda a mejorar la resistencia y el uso de la mayoría de las aplicaciones, es posible que incluya enfrentado al realizar pruebas comparativas de rendimiento, especialmente cuando medir la latencia.  La latencia observado cliente tendrá un pico si la prueba alcanza el límite de servidor y hace que el cliente SDK silenciosamente reintentos. Para evitar picos de latencia durante experimentación de rendimiento, medir el cargo devuelto por cada operación y asegúrese de que funcionan las solicitudes de debajo de la tasa de solicitud reservado. Para obtener más información, consulte [solicitar unidades](documentdb-request-units.md).
   
3. **Diseño de documentos más pequeños para aumentar el rendimiento**

    El cargo de solicitud (es decir, el coste de procesamiento de la solicitud) de una determinada operación directamente está asociado con el tamaño del documento. Operaciones en documentos grandes costar más que operaciones para documentos pequeños.

## <a name="consistency-levels"></a>Niveles de coherencia

1. **Utilizar niveles de coherencia más débiles para mejor latencia de lectura**

    Otro factor importante para tener en cuenta al ajustar el rendimiento de aplicaciones de DocumentDB es el nivel de coherencia. La opción de nivel de coherencia tiene implicaciones de rendimiento de leer y escribir. Puede configurar el nivel de coherencia de forma predeterminada en la cuenta de base de datos y el nivel de coherencia elegida, a continuación, se aplica a todas las colecciones (en todas las bases de datos) dentro de la cuenta DocumentDB. En términos de operaciones de escritura, el impacto de cambiar el nivel de coherencia se observa como latencia de solicitud. Como se utilizan los niveles de coherencia más seguro, aumentará la latencia de escritura. Por otro lado, se observa el impacto del nivel de coherencia en las operaciones de lectura en términos de rendimiento. Más débil coherencia niveles permiten mayor lea rendimiento a ser realizadas por el cliente.

    De forma predeterminada todas las lecturas y consultas emitidas para los recursos definidos por el usuario utilizará el nivel de coherencia predeterminada especificado en la cuenta de base de datos. Sin embargo, puede reducir el nivel de coherencia de una solicitud de lectura o consulta específica especificando el encabezado de solicitud de x ms coherencia nivel. Para obtener más información, vea [niveles de coherencia en DocumentDB](documentdb-consistency-levels.md).

## <a name="next-steps"></a>Pasos siguientes

Para una aplicación de ejemplo que se utiliza para evaluar DocumentDB para escenarios de alto rendimiento en algunos equipos de cliente, vea [rendimiento y escala pruebas con DocumentDB de Azure](documentdb-performance-testing.md).

Para obtener más información acerca del diseño de la aplicación de escala y alto performance, consulte también [particiones y escala en DocumentDB de Azure](documentdb-partition-data.md).
