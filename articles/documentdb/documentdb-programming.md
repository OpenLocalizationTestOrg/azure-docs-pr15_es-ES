<properties 
    pageTitle="Programación de DocumentDB: procedimientos almacenados, desencadenadores de base de datos y UDF | Microsoft Azure" 
    description="Obtenga información sobre cómo usar DocumentDB para escribir procedimientos almacenados, desencadenadores de base de datos y las funciones definidas por el usuario (UDF) en JavaScript. Obtener sugerencias de programing de base de datos y mucho más." 
    keywords="Desencadenadores de base de datos, procedimiento almacenado, procedimiento almacenado, el programa de base de datos, procedimiento almacenado, documentdb, azure, Microsoft azure"
    services="documentdb" 
    documentationCenter="" 
    authors="aliuy" 
    manager="jhubbard" 
    editor="mimig"/>

<tags 
    ms.service="documentdb" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/14/2016" 
    ms.author="andrl"/>

# <a name="documentdb-server-side-programming-stored-procedures-database-triggers-and-udfs"></a>Programación de servidor DocumentDB: procedimientos almacenados, desencadenadores de base de datos y UDF

Obtenga información sobre cómo integra el idioma de Azure DocumentDB, transacciones ejecución de JavaScript permite a los desarrolladores escribir **procedimientos almacenados**, **desencadenadores** y **definidas por el usuario (UDF) de las funciones** de forma nativa en JavaScript. Esto le permite escribir lógica de aplicación de programa de base de datos que puede enviar y ejecutar directamente en las particiones de almacenamiento de la base de datos 

Se recomienda Introducción observando el siguiente vídeo, donde Andrew Liu proporciona una breve introducción al modelo de programación de base de datos del servidor de DocumentDB. 

> [AZURE.VIDEO azure-demo-a-quick-intro-to-azure-documentdbs-server-side-javascript]

A continuación, vuelva a este artículo, donde puede aprender las respuestas a las preguntas siguientes:  

- ¿Cómo escribir una un procedimiento almacenado, desencadenador o UDF con JavaScript?
- ¿Cómo garantiza el DocumentDB ácido?
- ¿Cómo funcionan las transacciones en DocumentDB?
- ¿Cuáles son previamente desencadenadores y posteriores a la activa y cómo escribir una?
- ¿Cómo registrar y ejecutar un procedimiento almacenado, desencadenador o UDF de manera REST utilizando HTTP
- ¿Qué DocumentDB SDK están disponible para crear y ejecutar procedimientos almacenados, desencadenadores y UDF?

## <a name="introduction-to-stored-procedure-and-udf-programming"></a>Introducción a la programación de UDF y procedimiento almacenado

Este enfoque de *"JavaScript como un día moderna SQL T"* libera los desarrolladores de aplicaciones de las complejidades de errores de coincidencia de tipo sistema y tecnologías de asignación relacional de objetos. También tiene varias ventajas intrínsecos que se puede utilizar para crear aplicaciones completas:  

-   **Lógica de procedimientos:** JavaScript como un alto nivel de programación de idioma, proporciona una interfaz enriquecida y familiar para expresar la lógica empresarial. Puede realizar secuencias complejas de operaciones más cerca a los datos.

-   **Transacciones atómicas:** DocumentDB garantiza que realizar operaciones de base de datos dentro de un único procedimiento almacenado o desencadenador son atómica. Esto permite a una aplicación combinar operaciones relacionadas en un único lote para que todos ellos son correctas o ninguna de ellas se realice correctamente. 

-   **Rendimiento:** Permite que el hecho de que JSON esencia se asigna al sistema de tipos de lenguaje de Javascript y también es la unidad básica de almacenamiento en DocumentDB para un número de optimizaciones como diferida cada vez documentos JSON en el grupo de búferes y hacerle disponibles a petición para ejecutar el código. Hay más beneficios de rendimiento asociados con lógica de negocios de envío a la base de datos:
    -   Procesamiento por lotes, los desarrolladores pueden agrupar operaciones como inserciones y enviarlos de forma masiva. La latencia de tráfico de red de costo y la sobrecarga de almacenamiento para crear transacciones independientes se ha reducido significativamente. 
    -   Compilación antes: DocumentDB precompila procedimientos almacenados, desencadenadores y funciones definidas por el usuario (UDF) para evitar el costo de compilación de JavaScript para cada invocación. La sobrecarga de crear el código de bytes para la lógica de procedimiento se amortiza en un valor mínimo.
    -   Secuencias: necesidad de muchas operaciones efecto secundario ("desencadenador") que posiblemente implica realizar operaciones de almacenamiento secundario de uno o varios. Aparte de atomicidad, esto es más eficaz cuando mueve al servidor. 
-   **Encapsulado:** Procedimientos almacenados pueden usarse para agrupar la lógica empresarial en un solo lugar. Esto tiene dos ventajas:
    -   Agrega un nivel de abstracción sobre los datos sin formato, que permite a los datos arquitectos evolucionando en sus aplicaciones, independientemente de los datos. Esto es muy útil cuando los datos están menor de esquema, debido a las suposiciones frágiles que tal vez deba estar preparado en la aplicación si tienen que tratar directamente con los datos.  
    -   Esta abstracción permite a las empresas proteger sus datos al optimizar el acceso de las secuencias de comandos.  

Se admite la creación y ejecución de desencadenadores de base de datos, el procedimiento almacenado y operadores de consulta personalizada a través de la [API de REST](https://msdn.microsoft.com/library/azure/dn781481.aspx), [DocumentDB Studio](https://github.com/mingaliu/DocumentDBStudio/releases)y [cliente SDK](documentdb-sdk-dotnet.md) en muchas plataformas incluidos .NET, Node.js y JavaScript.

**Que este tutorial usa el [SDK de Node.js con preguntas promesa](http://azure.github.io/azure-documentdb-node-q/) ** para ilustrar la sintaxis y el uso de procedimientos almacenados, desencadenadores y UDF.   

## <a name="stored-procedures"></a>Procedimientos almacenados

### <a name="example-write-a-simple-stored-procedure"></a>Ejemplo: Escribir un procedimiento almacenado simple 
Comencemos con un procedimiento almacenado simple que devuelve una respuesta de "Hola a todos".

    var helloWorldStoredProc = {
        id: "helloWorld",
        body: function () {
            var context = getContext();
            var response = context.getResponse();
    
            response.setBody("Hello, World");
        }
    }


Procedimientos almacenados están registrados por colección y pueden funcionar en cualquier documento y datos adjuntos presentes en esa colección. El fragmento de código siguiente muestra cómo registrar el procedimiento almacenado Hola a todos con un conjunto. 

    // register the stored procedure
    var createdStoredProcedure;
    client.createStoredProcedureAsync('dbs/testdb/colls/testColl', helloWorldStoredProc)
        .then(function (response) {
            createdStoredProcedure = response.resource;
            console.log("Successfully created stored procedure");
        }, function (error) {
            console.log("Error", error);
        });


Una vez que se registra el procedimiento almacenado, podemos ejecutar con respecto a la colección y leer los resultados en el cliente. 

    // execute the stored procedure
    client.executeStoredProcedureAsync('dbs/testdb/colls/testColl/sprocs/helloWorld')
        .then(function (response) {
            console.log(response.result); // "Hello, World"
        }, function (err) {
            console.log("Error", error);
        });


El objeto de contexto proporciona acceso a todas las acciones que pueden realizarse en el almacenamiento de DocumentDB, así como el acceso a los objetos de solicitud y la respuesta. En este caso, hemos usado el objeto de respuesta para establecer el cuerpo de la respuesta que se envió al cliente. Para obtener más detalles, consulte el [servidor de DocumentDB JavaScript documentación SDK](http://azure.github.io/azure-documentdb-js-server/).  

Deje que nosotros expanda en este ejemplo y agregar que más funciones relacionadas al procedimiento almacenado de base de datos. Procedimientos almacenados pueden crear, actualizar, lea, la consulta y eliminar documentos y datos adjuntos dentro de la colección.    

### <a name="example-write-a-stored-procedure-to-create-a-document"></a>Ejemplo: Escribir un procedimiento almacenado para crear un documento 
El fragmento de código siguiente muestra cómo usar el objeto de contexto para interactuar con los recursos de DocumentDB.

    var createDocumentStoredProc = {
        id: "createMyDocument",
        body: function createMyDocument(documentToCreate) {
            var context = getContext();
            var collection = context.getCollection();

            var accepted = collection.createDocument(collection.getSelfLink(),
                  documentToCreate,
                  function (err, documentCreated) {
                      if (err) throw new Error('Error' + err.message);
                      context.getResponse().setBody(documentCreated.id)
                  });
            if (!accepted) return;
        }
    }


Este procedimiento almacenado toma como entrada documentToCreate, el cuerpo de un documento que se creen en la colección actual. Todas estas operaciones son asincrónicas y dependen de devoluciones de llamada de función de JavaScript. La función de devolución de llamada tiene dos parámetros, uno para el objeto de error en caso que se produce un error en la operación y otro para el objeto creado. Dentro de la devolución de llamada, los usuarios pueden controlar la excepción o producir un error. En caso de que no se proporciona una devolución de llamada y se produce un error, el tiempo de ejecución de DocumentDB produce un error.   

En el ejemplo anterior, la devolución de llamada produce un error si el error en la operación. En caso contrario, Establece el identificador del documento creado como el cuerpo de la respuesta al cliente. Le mostramos cómo se ejecuta este procedimiento almacenado con parámetros de entrada.

    // register the stored procedure
    client.createStoredProcedureAsync('dbs/testdb/colls/testColl', createDocumentStoredProc)
        .then(function (response) {
            var createdStoredProcedure = response.resource;
    
            // run stored procedure to create a document
            var docToCreate = {
                id: "DocFromSproc",
                book: "The Hitchhiker’s Guide to the Galaxy",
                author: "Douglas Adams"
            };
    
            return client.executeStoredProcedureAsync('dbs/testdb/colls/testColl/sprocs/createMyDocument',
                  docToCreate);
        }, function (error) {
            console.log("Error", error);
        })
    .then(function (response) {
        console.log(response); // "DocFromSproc"
    }, function (error) {
        console.log("Error", error);
    });

    
Tenga en cuenta que este procedimiento almacenado se puede modificar para tomar una matriz de organismos de documento como entrada y crearlos todos en la misma ejecución de procedimiento almacenado en lugar de varias solicitudes de red al crear de forma individual cada uno de ellos. Esto puede usarse para implementar un importador masa eficaz para DocumentDB (que se describe más adelante en este tutorial).   

El ejemplo descrito muestra cómo usar procedimientos almacenados. Trataremos desencadenadores y funciones definidas por el usuario (UDF) más adelante en el tutorial.

## <a name="database-program-transactions"></a>Transacciones del sistema de base de datos
Transacciones en una base de datos típica pueden definirse como una secuencia de operaciones realizadas como una sola unidad lógica de trabajo. Cada transacción proporciona **ácido garantiza**. ÁCIDO es un acrónimo conocido que corresponde a cuatro propiedades - atomicidad, coherencia, aislamiento y duración.  

En resumen, atomicidad garantiza que todo el trabajo realizado dentro de una transacción se interpreta como una sola unidad donde alguno todos se confirma o ninguno. Coherencia se asegura de que los datos están siempre en buen estado interno en las transacciones. Aislamiento garantiza que no hay dos transacciones interfieran entre sí: por lo general, sistemas más comerciales proporcionan varios niveles de aislamiento que se pueden usar según las necesidades de la aplicación. Duración garantiza que cualquier cambio que se confirma en la base de datos siempre se pueden presentar.   

En DocumentDB, JavaScript está hospedado en el mismo espacio de memoria que la base de datos. Por lo tanto, las solicitudes realizadas dentro de los procedimientos almacenados y desencadenadores ejecutarán en el mismo ámbito de una sesión de base de datos. Esto permite DocumentDB garantizar ácido para todas las operaciones que forman parte de un solo procedimiento almacenado o desencadenador. Tenga en cuenta la siguiente definición de procedimiento almacenado:

    // JavaScript source code
    var exchangeItemsSproc = {
        name: "exchangeItems",
        body: function (playerId1, playerId2) {
            var context = getContext();
            var collection = context.getCollection();
            var response = context.getResponse();
    
            var player1Document, player2Document;
    
            // query for players
            var filterQuery = 'SELECT * FROM Players p where p.id  = "' + playerId1 + '"';
            var accept = collection.queryDocuments(collection.getSelfLink(), filterQuery, {},
                function (err, documents, responseOptions) {
                    if (err) throw new Error("Error" + err.message);
    
                    if (documents.length != 1) throw "Unable to find both names";
                    player1Document = documents[0];
    
                    var filterQuery2 = 'SELECT * FROM Players p where p.id = "' + playerId2 + '"';
                    var accept2 = collection.queryDocuments(collection.getSelfLink(), filterQuery2, {},
                        function (err2, documents2, responseOptions2) {
                            if (err2) throw new Error("Error" + err2.message);
                            if (documents2.length != 1) throw "Unable to find both names";
                            player2Document = documents2[0];
                            swapItems(player1Document, player2Document);
                            return;
                        });
                    if (!accept2) throw "Unable to read player details, abort ";
                });
    
            if (!accept) throw "Unable to read player details, abort ";
    
            // swap the two players’ items
            function swapItems(player1, player2) {
                var player1ItemSave = player1.item;
                player1.item = player2.item;
                player2.item = player1ItemSave;
    
                var accept = collection.replaceDocument(player1._self, player1,
                    function (err, docReplaced) {
                        if (err) throw "Unable to update player 1, abort ";
    
                        var accept2 = collection.replaceDocument(player2._self, player2,
                            function (err2, docReplaced2) {
                                if (err) throw "Unable to update player 2, abort"
                            });
    
                        if (!accept2) throw "Unable to update player 2, abort";
                    });
    
                if (!accept) throw "Unable to update player 1, abort";
            }
        }
    }
    
    // register the stored procedure in Node.js client
    client.createStoredProcedureAsync(collection._self, exchangeItemsSproc)
        .then(function (response) {
            var createdStoredProcedure = response.resource;
        }
    );

Este procedimiento almacenado utiliza transacciones dentro de una aplicación de juegos a elementos de comercio entre dos jugadores en una única operación. El procedimiento almacenado intenta leer dos documentos cada correspondientes a los identificadores de Reproductor pasan como un argumento. Si se encuentran ambos documentos Reproductor, el procedimiento almacenado actualiza los documentos por intercambiar sus elementos. Si se encuentra algún error a lo largo de la forma, produce una excepción de JavaScript que implícitamente anula la transacción.

Si la colección de procedimiento almacenado está registrada contra es una colección única partición, la transacción tiene como ámbito todo el docuemnts dentro de la colección. Si la colección tiene particiones, procedimientos almacenados se ejecutan en el ámbito de la transacción de una clave de una sola partición. Cada almacenado ejecución del procedimiento, a continuación, debe incluir un valor de clave de partición correspondiente al ámbito de la transacción debe ejecutar. Para obtener más detalles, vea [DocumentDB particiones](documentdb-partition-data.md).

### <a name="commit-and-rollback"></a>Confirmar y deshacer
Las transacciones se integran de profundidad y forma nativa en modelo de programación de JavaScript de DocumentDB. Dentro de una función de JavaScript, todas las operaciones se ajustan automáticamente en una sola transacción. Si el código JavaScript completa sin cualquier excepción, se confirman las operaciones de la base de datos. De hecho, las instrucciones "Comenzar transacción" y "Confirmar" en bases de datos relacionales están implícitas en DocumentDB.  
 
Si hay cualquier excepción que se propaga desde el script, tiempo de ejecución de JavaScript de DocumentDB volverá toda la transacción. Tal como se muestra en el ejemplo anterior, iniciar una excepción es realmente el equivalente a una "transacción de deshacer" en DocumentDB.
 
### <a name="data-consistency"></a>Coherencia de los datos
Procedimientos almacenados y desencadenadores siempre se ejecutan en la réplica principal de la colección de DocumentDB. Esto garantiza que lee desde dentro de almacena procedimientos oferta fuerte coherencia. Se pueden ejecutar consultas con funciones definidas por el usuario en el principal o cualquier réplica secundario, pero garantizamos para cumplir con el nivel de coherencia solicitado eligiendo la réplica adecuada.

## <a name="bounded-execution"></a>Ejecución limitada
Se deben completar todas las operaciones de DocumentDB en el servidor especificado duración de tiempo de espera de la solicitud. Esta restricción también se aplica a las funciones de JavaScript (procedimientos almacenados, desencadenadores y funciones definidas por el usuario). Si una operación no se completa con ese límite de tiempo, se deshace la transacción. Funciones de JavaScript deben finalizar dentro del plazo o implementar un modelo de continuación según para lote o reanudar la ejecución.  

Para simplificar el desarrollo de procedimientos almacenados y desencadenadores para controlar los límites de tiempo, todas las funciones en el objeto de la colección (para crear, leer, reemplazar y eliminar documentos y datos adjuntos) devuelven un valor Boolean que indica si se complete la operación. Si este valor es false, es una indicación de que el límite de tiempo está a punto de caducar y que el procedimiento debe conclusión de ejecución.  Se garantiza que las operaciones en cola antes de la primera operación de almacenamiento no aceptado completar si el procedimiento almacenado completa en el tiempo y no en cola más solicitudes.  

Funciones de JavaScript también están limitadas consumo de los recursos. DocumentDB reserva rendimiento por colección basándose en el tamaño de la preparación de una cuenta de base de datos. Rendimiento se expresa como una unidad normalizada de CPU, memoria y consumo IO llamado unidades de solicitud o RUs. Funciones de JavaScript potencialmente pueden usar un gran número de RUs dentro de una hora corta y obtengan tasa limitado si se ha alcanzado el límite de la colección. También se pueden puesto en cuarentena procedimientos almacenados intensivos de recursos para garantizar la disponibilidad de las operaciones de base de datos simple.  

### <a name="example-bulk-importing-data-into-a-database-program"></a>Ejemplo: Importar datos en un programa de base de datos en masa
A continuación se muestra un ejemplo de un procedimiento almacenado que se escribe en importación masiva documentos en una colección. Observe cómo el procedimiento almacenado trata ejecución limitada comprobando el valor booleano devuelve el valor de createDocument y, a continuación, usa el recuento de documentos que se inserta en cada invocación del procedimiento almacenado para realizar un seguimiento y reanudar el progreso por lotes.

    function bulkImport(docs) {
        var collection = getContext().getCollection();
        var collectionLink = collection.getSelfLink();
    
        // The count of imported docs, also used as current doc index.
        var count = 0;
    
        // Validate input.
        if (!docs) throw new Error("The array is undefined or null.");
    
        var docsLength = docs.length;
        if (docsLength == 0) {
            getContext().getResponse().setBody(0);
        }
    
        // Call the create API to create a document.
        tryCreate(docs[count], callback);
    
        // Note that there are 2 exit conditions:
        // 1) The createDocument request was not accepted. 
        //    In this case the callback will not be called, we just call setBody and we are done.
        // 2) The callback was called docs.length times.
        //    In this case all documents were created and we don’t need to call tryCreate anymore. Just call setBody and we are done.
        function tryCreate(doc, callback) {
            var isAccepted = collection.createDocument(collectionLink, doc, callback);
    
            // If the request was accepted, callback will be called.
            // Otherwise report current count back to the client, 
            // which will call the script again with remaining set of docs.
            if (!isAccepted) getContext().getResponse().setBody(count);
        }
    
        // This is called when collection.createDocument is done in order to process the result.
        function callback(err, doc, options) {
            if (err) throw err;
    
            // One more document has been inserted, increment the count.
            count++;
    
            if (count >= docsLength) {
                // If we created all documents, we are done. Just set the response.
                getContext().getResponse().setBody(count);
            } else {
                // Create next document.
                tryCreate(docs[count], callback);
            }
        }
    }

## <a id="trigger"></a>Desencadenadores de base de datos
### <a name="database-pre-triggers"></a>Desencadenadores de base de datos anterior
DocumentDB proporciona desencadenadores que se ejecuta o desencadenados por una operación en un documento. Por ejemplo, puede especificar un desencadenador anterior cuando se crea un documento, este desencadenador anterior se ejecuta antes de que se creó el documento. A continuación se muestra un ejemplo de cómo pueden utilizarse desencadenadores preliminar para validar las propiedades de un documento que se va a crear:

    var validateDocumentContentsTrigger = {
        name: "validateDocumentContents",
        body: function validate() {
            var context = getContext();
            var request = context.getRequest();
    
            // document to be created in the current operation
            var documentToCreate = request.getBody();
    
            // validate properties
            if (!("timestamp" in documentToCreate)) {
                var ts = new Date();
                documentToCreate["my timestamp"] = ts.getTime();
            }
    
            // update the document that will be created
            request.setBody(documentToCreate);
        },
        triggerType: TriggerType.Pre,
        triggerOperation: TriggerOperation.Create
    }


Y el código de registro de cliente de Node.js correspondiente del desencadenador:

    // register pre-trigger
    client.createTriggerAsync(collection.self, validateDocumentContentsTrigger)
        .then(function (response) {
            console.log("Created", response.resource);
            var docToCreate = {
                id: "DocWithTrigger",
                event: "Error",
                source: "Network outage"
            };
    
            // run trigger while creating above document 
            var options = { preTriggerInclude: "validateDocumentContents" };
    
            return client.createDocumentAsync(collection.self,
                  docToCreate, options);
        }, function (error) {
            console.log("Error", error);
        })
    .then(function (response) {
        console.log(response.resource); // document with timestamp property added
    }, function (error) {
        console.log("Error", error);
    });


Desencadenadores preliminar no pueden tener los parámetros de entrada. El objeto de solicitud se puede utilizar para manipular el mensaje de solicitud asociado a la operación. A continuación, se ejecuta el desencadenador previo con la creación de un documento y el cuerpo del mensaje de solicitud contiene el documento que se creen en formato JSON.   

Cuando se registran desencadenadores, los usuarios pueden especificar las operaciones que se pueden ejecutar con. Este desencadenador se creó con TriggerOperation.Create, lo que significa que no se permite el siguiente.

    var options = { preTriggerInclude: "validateDocumentContents" };
    
    client.replaceDocumentAsync(docToReplace.self,
                  newDocBody, options)
    .then(function (response) {
        console.log(response.resource);
    }, function (error) {
        console.log("Error", error);
    });
    
    // Fails, can’t use a create trigger in a replace operation

### <a name="database-post-triggers"></a>Desencadenadores de posteriores a la base de datos
Desencadenadores posteriores a la, como desencadenadores preliminar, están asociados a una operación en un documento y no toman los parámetros de entrada. Ejecutar **una vez** que haya completado la operación, y a tener acceso al mensaje de respuesta que se envía al cliente.   

En el ejemplo siguiente se muestra desencadenadores posteriores a la acción:

    var updateMetadataTrigger = {
        name: "updateMetadata",
        body: function updateMetadata() {
            var context = getContext();
            var collection = context.getCollection();
            var response = context.getResponse();
    
            // document that was created
            var createdDocument = response.getBody();
    
            // query for metadata document
            var filterQuery = 'SELECT * FROM root r WHERE r.id = "_metadata"';
            var accept = collection.queryDocuments(collection.getSelfLink(), filterQuery,
                updateMetadataCallback);
            if(!accept) throw "Unable to update metadata, abort";
     
            function updateMetadataCallback(err, documents, responseOptions) {
                if(err) throw new Error("Error" + err.message);
                         if(documents.length != 1) throw 'Unable to find metadata document';
                         
                         var metadataDocument = documents[0];
                         
                         // update metadata
                         metadataDocument.createdDocuments += 1;
                         metadataDocument.createdNames += " " + createdDocument.id;
                         var accept = collection.replaceDocument(metadataDocument._self,
                               metadataDocument, function(err, docReplaced) {
                                      if(err) throw "Unable to update metadata, abort";
                               });
                         if(!accept) throw "Unable to update metadata, abort";
                         return;                    
            }                                                                                           
        },
        triggerType: TriggerType.Post,
        triggerOperation: TriggerOperation.All
    }


Como se muestra en el ejemplo siguiente, se puede registrar el desencadenador.

    // register post-trigger
    client.createTriggerAsync('dbs/testdb/colls/testColl', updateMetadataTrigger)
        .then(function(createdTrigger) { 
            var docToCreate = { 
                name: "artist_profile_1023",
                artist: "The Band",
                albums: ["Hellujah", "Rotators", "Spinning Top"]
            };
    
            // run trigger while creating above document 
            var options = { postTriggerInclude: "updateMetadata" };
        
            return client.createDocumentAsync(collection.self,
                  docToCreate, options);
        }, function(error) {
            console.log("Error" , error);
        })
    .then(function(response) {
        console.log(response.resource); 
    }, function(error) {
        console.log("Error" , error);
    });


Este desencadenador consulta para el documento de metadatos y actualiza con los detalles sobre el documento recién creado.  

Lo que es importante tener en cuenta es la ejecución de **transacciones** de desencadenadores en DocumentDB. Este desencadenador posterior a la que se ejecuta como parte de la misma transacción que la creación del documento original. Por lo tanto, si se produce una excepción desde el desencadenador posteriores a la (por ejemplo si no se puede actualizar el documento de metadatos), toda la transacción se producirá un error y se puede deshacer. No hay ningún documento se creará y se devolverá una excepción.  

##<a id="udf"></a>Funciones definidas por el usuario
Funciones definidas por el usuario (UDF) se utilizan para extender la gramática del lenguaje de consulta SQL DocumentDB e implementar la lógica empresarial personalizada. Sólo puede llamar desde dentro de las consultas. Que no tienen acceso al objeto de contexto y están diseñados para usarse como JavaScript solo cálculo. Por lo tanto, se pueden ejecutar UDF en réplicas secundarias del servicio DocumentDB.  
 
En el ejemplo siguiente crea un archivo UDF para calcular el impuesto basado en tasas para distintos corchetes de ingresos y usa dentro de una consulta para buscar todas las personas que pagan más de 20.000 $ en los impuestos.

    var taxUdf = {
        name: "tax",
        body: function tax(income) {
    
            if(income == undefined) 
                throw 'no input';
    
            if (income < 1000) 
                return income * 0.1;
            else if (income < 10000) 
                return income * 0.2;
            else
                return income * 0.4;
        }
    }


El archivo UDF puede usarse posteriormente en las consultas como en el ejemplo siguiente:

    // register UDF
    client.createUserDefinedFunctionAsync('dbs/testdb/colls/testColl', taxUdf)
        .then(function(response) { 
            console.log("Created", response.resource);
    
            var query = 'SELECT * FROM TaxPayers t WHERE udf.tax(t.income) > 20000'; 
            return client.queryDocuments('dbs/testdb/colls/testColl',
                   query).toArrayAsync();
        }, function(error) {
            console.log("Error" , error);
        })
    .then(function(response) {
        var documents = response.feed;
        console.log(response.resource); 
    }, function(error) {
        console.log("Error" , error);
    });

## <a name="javascript-language-integrated-query-api"></a>API de JavaScript integrados de idioma consulta
Además de emitir consultas con la gramática SQL de DocumentDB, el SDK de servidor le permite realizar consultas optimizadas mediante una interfaz intuitiva de JavaScript sin ningún conocimiento de SQL. La consulta de JavaScript que API permite crear mediante programación consultas pasando funciones predicadas función encadenada llamadas, con una sintaxis familiar de ECMAScript5 integradas de matriz y las bibliotecas de JavaScript populares como lodash. Las consultas se analizan el tiempo de ejecución de JavaScript para ejecutarse de forma eficiente mediante los índices de DocumentDB.

> [AZURE.NOTE]`__` (subrayado doble) es un alias para `getContext().getCollection()`.
> <br/>
> En otras palabras, puede usar `__` o `getContext().getCollection()` para tener acceso a la API de JavaScript consulta.

Funciones compatibles son:
<ul>
<li>
<b>... Chain(). valor ([devolución de llamada] [, opciones])</b>
<ul>
<li>
Se inicia una llamada encadenada que se debe terminar con value().
</li>
</ul>
</li>
<li>
<b>filtro (predicateFunction [, opciones] [, devolución de llamada])</b>
<ul>
<li>
Filtros de entrada con una función de predicado devuelve true o false para filtrar los documentos de entrada de entrada y salida en el conjunto resultante. Este comportamiento es similar a una cláusula WHERE de SQL.
</li>
</ul>
</li>
<li>
<b>mapa (transformationFunction [, opciones] [, devolución de llamada])</b>
<ul>
<li>
Se aplica una proyección dada una función de transformación que asigna cada elemento de entrada a un objeto de JavaScript o un valor. Este comportamiento es similar a una cláusula SELECT en SQL.
</li>
</ul>
</li>
<li>
<b>Pluck ([propertyName] [, opciones] [, devolución de llamada])</b>
<ul>
<li>
Se trata de un método abreviado para un mapa que extrae el valor de una propiedad de cada elemento de entrada.
</li>
</ul>
</li>
<li>
<b>acoplar ([isShallow] [, opciones] [, devolución de llamada])</b>
<ul>
<li>
Combina y acopla matrices de cada elemento de entrada a una sola matriz. Este comportamiento es similar a SelectMany en LINQ.
</li>
</ul>
</li>
<li>
<b>sortBy ([predicado] [, opciones] [, devolución de llamada])</b>
<ul>
<li>
Crear un nuevo conjunto de documentos al ordenar los documentos en la secuencia de entrada de documentos en orden ascendente utilizando el predicado especificado. Este comportamiento es similar a una cláusula ORDER BY de SQL.
</li>
</ul>
</li>
<li>
<b>sortByDescending ([predicado] [, opciones] [, devolución de llamada])</b>
<ul>
<li>
Crear un nuevo conjunto de documentos al ordenar los documentos en la secuencia de entrada de documento en orden descendente con el predicado especificado. Este comportamiento es similar a una cláusula ORDER BY x DESC en SQL.
</li>
</ul>
</li>
</ul>


Cuando se encuentran dentro de las funciones de predicado o selector, las siguientes construcciones de JavaScript automáticamente obtengan optimizadas para ejecutarse directamente en los índices de DocumentDB:

* Operadores simples: = + - * / % | ^ &amp; == != === !=== &lt; &gt; &lt;= &gt;= || &amp;&amp; &lt;&lt; &gt;&gt; &gt;&gt;&gt;! ~
* Literales, incluido el objeto literal: {}
* var y valor devuelto

Las siguientes construcciones de JavaScript no obtener optimizadas para los índices de DocumentDB:

* Flujo de control (por ejemplo, if, mientras)
* Llamadas de función

Para obtener más información, consulte nuestra [JSDocs del servidor](http://azure.github.io/azure-documentdb-js-server/).

### <a name="example-write-a-stored-procedure-using-the-javascript-query-api"></a>Ejemplo: Escribir un procedimiento almacenado con la API de JavaScript consulta

En el ejemplo de código siguiente es un ejemplo de uso de la API de JavaScript consulta en el contexto de un procedimiento almacenado. El procedimiento almacenado inserta un documento, proporcionado por un parámetro de entrada y actualiza los metadatos de un documento, con el `__.filter()` método con minSize, tamaño máximo y totalSize basado en la propiedad de tamaño del documento de entrada.

    /**
     * Insert actual doc and update metadata doc: minSize, maxSize, totalSize based on doc.size.
     */
    function insertDocumentAndUpdateMetadata(doc) {
      // HTTP error codes sent to our callback funciton by DocDB server.
      var ErrorCode = {
        RETRY_WITH: 449,
      }

      var isAccepted = __.createDocument(__.getSelfLink(), doc, {}, function(err, doc, options) {
        if (err) throw err;

        // Check the doc (ignore docs with invalid/zero size and metaDoc itself) and call updateMetadata.
        if (!doc.isMetadata && doc.size > 0) {
          // Get the meta document. We keep it in the same collection. it's the only doc that has .isMetadata = true.
          var result = __.filter(function(x) {
            return x.isMetadata === true
          }, function(err, feed, options) {
            if (err) throw err;

            // We assume that metadata doc was pre-created and must exist when this script is called.
            if (!feed || !feed.length) throw new Error("Failed to find the metadata document.");

            // The metadata document.
            var metaDoc = feed[0];

            // Update metaDoc.minSize:
            // for 1st document use doc.Size, for all the rest see if it's less than last min.
            if (metaDoc.minSize == 0) metaDoc.minSize = doc.size;
            else metaDoc.minSize = Math.min(metaDoc.minSize, doc.size);

            // Update metaDoc.maxSize.
            metaDoc.maxSize = Math.max(metaDoc.maxSize, doc.size);

            // Update metaDoc.totalSize.
            metaDoc.totalSize += doc.size;

            // Update/replace the metadata document in the store.
            var isAccepted = __.replaceDocument(metaDoc._self, metaDoc, function(err) {
              if (err) throw err;
              // Note: in case concurrent updates causes conflict with ErrorCode.RETRY_WITH, we can't read the meta again 
              //       and update again because due to Snapshot isolation we will read same exact version (we are in same transaction).
              //       We have to take care of that on the client side.
            });
            if (!isAccepted) throw new Error("replaceDocument(metaDoc) returned false.");
          });
          if (!result.isAccepted) throw new Error("filter for metaDoc returned false.");
        }
      });
      if (!isAccepted) throw new Error("createDocument(actual doc) returned false.");
    }

## <a name="sql-to-javascript-cheat-sheet"></a>SQL a la hoja de referencia rápida de Javascript
La siguiente tabla presenta varias consultas SQL y las consultas de JavaScript correspondientes.

Con las consultas SQL, las claves de propiedad del documento (por ejemplo, `doc.id`) distinguen mayúsculas de minúsculas.

<br/>
<table border="1" width="100%">
<colgroup>
<col span="1" style="width: 40%;">
<col span="1" style="width: 40%;">
<col span="1" style="width: 20%;">
</colgroup>
<tbody>
<tr>
<th>SQL</th>
<th>API de JavaScript consulta</th>
<th>Detalles</th>
</tr>
<tr>
<td>
<pre>
Seleccione * de documentos
</pre>
</td>
<td>
<pre>
__.map(Function(doc) {doc devuelto;});
</pre>
</td>
<td>Resultados en todos los documentos (paginados con el token de continuación) como está.</td>
</tr>
<tr>
<td>
<pre>
Seleccione docs.id, docs.message como mensaje, docs.actions de documentos
</pre>
</td>
<td>
<pre>
__.map(Function(doc) {devolver {identificador: doc.id, msj: doc.message, acciones: doc.actions};});
</pre>
</td>
<td>Proyectos id, el mensaje (un alias para msj) y la acción de todos los documentos.</td>
</tr>
<tr>
<td>
<pre>
Seleccionar * desde documentos dónde docs.id="X998_Y998"
</pre>
</td>
<td>
<pre>
__.Filter(Function(doc) {devolver doc.id === "X998_Y998;"});
</pre>
</td>
<td>Las consultas de documentos con el predicado: id = "X998_Y998".</td>
</tr>
<tr>
<td>
<pre>
Seleccionar * desde documentos donde ARRAY_CONTAINS(docs. Etiquetas, 123)
</pre>
</td>
<td>
<pre>
__.Filter(Function(x) {devolver x.Tags & & x.Tags.indexOf(123) > -1;});
</pre>
</td>
<td>Las consultas para los documentos que tengan una propiedad de etiquetas y etiquetas es una matriz que contiene el valor 123.</td>
</tr>
<tr>
<td>
<pre>
Seleccione docs.id, docs.message como documentos de mensaje desde dónde docs.id="X998_Y998"
</pre>
</td>
<td>
<pre>
__.Chain() .filter(function(doc) {devolver doc.id === "X998_Y998;"}) .map(function(doc) {devolver {identificador: doc.id, msj: doc.message};}) .value();
</pre>
</td>
<td>Las consultas de documentos con un predicado id = "X998_Y998" y, a continuación, proyectos el mensaje (alias de mensaje) y el id.</td>
</tr>
<tr>
<td>
<pre>
Documentos de seleccionar valor etiqueta de combinación de etiquetas en documentos. Etiquetas ORDER BY docs._ts
</pre>
</td>
<td>
<pre>
__.Chain() .filter(function(doc) {devuelto doc. Etiquetas & & Array.isArray (doc. Etiquetas); .sortBy(function(doc)}) {return doc._ts;}) .pluck("tags") .flatten() .value()
</pre>
</td>
<td>Filtros para los documentos que tienen una propiedad de matriz, etiquetas y ordena los documentos resultantes por la propiedad del sistema _ts marca de tiempo y, a continuación, proyectos + acopla la matriz de etiquetas.</td>
</tr>
</tbody>
</table>

## <a name="runtime-support"></a>Soporte de tiempo de ejecución
[Servidor de DocumentDB JavaScript SDK](http://azure.github.io/azure-documentdb-js-server/) proporciona compatibilidad para la mayoría de las características de idioma de JavaScript estándar como normalizado por [ECMA 262](http://www.ecma-international.org/publications/standards/Ecma-262.htm).

### <a name="security"></a>Seguridad
JavaScript procedimientos almacenados y desencadenadores de espacio aislados para los efectos de una secuencia de comandos no filtración a otra sin pasar por el aislamiento de la transacción de instantánea en el nivel de base de datos. Los entornos de tiempo de ejecución están agrupados pero limpiar del contexto después de cada ejecución. Por lo tanto, se garantiza que estar seguros de efectos secundarios no deseados entre sí.

### <a name="pre-compilation"></a>Compilación antes de
Procedimientos almacenados, desencadenadores y UDF están implícitamente precompiladas en el formato de código de bytes con el fin de evitar el costo de compilación en el momento de cada invocación de funciones de script. Así se garantiza que las llamadas de procedimientos almacenados son rápidas y tienen una superficie de baja.

## <a name="client-sdk-support"></a>Compatibilidad con clientes de SDK
Además del cliente de [Node.js](documentdb-sdk-node.md) DocumentDB es compatible con [. NET](documentdb-sdk-dotnet.md), [Java](documentdb-sdk-java.md), [JavaScript](http://azure.github.io/azure-documentdb-js/)y [Python SDK](documentdb-sdk-python.md). Procedimientos almacenados, desencadenadores y UDF se pueden crear y ejecutan mediante uno de estos SDK también. En el ejemplo siguiente se muestra cómo crear y ejecutar un procedimiento almacenado con el cliente de .NET. Observe cómo los tipos de .NET se pasa al procedimiento almacenado como JSON y lean.

    var markAntiquesSproc = new StoredProcedure
    {
        Id = "ValidateDocumentAge",
        Body = @"
                function(docToCreate, antiqueYear) {
                    var collection = getContext().getCollection();    
                    var response = getContext().getResponse();    
    
                    if(docToCreate.Year != undefined && docToCreate.Year < antiqueYear){
                        docToCreate.antique = true;
                    }
    
                    collection.createDocument(collection.getSelfLink(), docToCreate, {}, 
                        function(err, docCreated, options) { 
                            if(err) throw new Error('Error while creating document: ' + err.message);                              
                            if(options.maxCollectionSizeInMb == 0) throw 'max collection size not found'; 
                            response.setBody(docCreated);
                    });
            }"
    };
    
    // register stored procedure
    StoredProcedure createdStoredProcedure = await client.CreateStoredProcedureAsync(UriFactory.CreateDocumentCollectionUri("db", "coll"), markAntiquesSproc);
    dynamic document = new Document() { Id = "Borges_112" };
    document.Title = "Aleph";
    document.Year = 1949;
    
    // execute stored procedure
    Document createdDocument = await client.ExecuteStoredProcedureAsync<Document>(UriFactory.CreateStoredProcedureUri("db", "coll", "sproc"), document, 1920);


Este ejemplo muestra cómo usar el [SDK de .NET](https://msdn.microsoft.com/library/azure/dn948556.aspx) para crear un desencadenador anterior y crear un documento con el desencadenador habilitado. 

    Trigger preTrigger = new Trigger()
    {
        Id = "CapitalizeName",
        Body = @"function() {
            var item = getContext().getRequest().getBody();
            item.id = item.id.toUpperCase();
            getContext().getRequest().setBody(item);
        }",
        TriggerOperation = TriggerOperation.Create,
        TriggerType = TriggerType.Pre
    };
    
    Document createdItem = await client.CreateDocumentAsync(UriFactory.CreateDocumentCollectionUri("db", "coll"), new Document { Id = "documentdb" },
        new RequestOptions
        {
            PreTriggerInclude = new List<string> { "CapitalizeName" },
        });


Y, en el ejemplo siguiente se muestra cómo crear una función definida por el usuario (UDF) y usar en una [consulta SQL DocumentDB](documentdb-sql-query.md).

    UserDefinedFunction function = new UserDefinedFunction()
    {
        Id = "LOWER",
        Body = @"function(input) 
        {
            return input.toLowerCase();
        }"
    };
    
    foreach (Book book in client.CreateDocumentQuery(UriFactory.CreateDocumentCollectionUri("db", "coll"),
        "SELECT * FROM Books b WHERE udf.LOWER(b.Title) = 'war and peace'"))
    {
        Console.WriteLine("Read {0} from query", book);
    }

## <a name="rest-api"></a>API DE REST
Todas las operaciones de DocumentDB pueden realizarse de manera REST. Procedimientos almacenados, desencadenadores y funciones definidas por el usuario se pueden registrar en una colección utilizando HTTP POST. A continuación se muestra un ejemplo de cómo registrar un procedimiento almacenado:

    POST https://<url>/sprocs/ HTTP/1.1
    authorization: <<auth>>
    x-ms-date: Thu, 07 Aug 2014 03:43:10 GMT
    
    
    var x = {
      "name": "createAndAddProperty",
      "body": function (docToCreate, addedPropertyName, addedPropertyValue) {
                var collectionManager = getContext().getCollection();
                collectionManager.createDocument(
                    collectionManager.getSelfLink(),
                    docToCreate,
                    function(err, docCreated) {
                      if(err) throw new Error('Error:  ' + err.message);
                      docCreated[addedPropertyName] = addedPropertyValue;
                      getContext().getResponse().setBody(docCreated);
                    });
            }
    }


El procedimiento almacenado está registrado mediante la ejecución de una solicitud de entrada con la URI dbs/bdprueba/colls/testColl/procedimientos almacenados con el texto que contiene el procedimiento almacenado para crear. Desencadenadores y UDF se pueden registrar de forma similar al emitir una publicación contra /desencadenadores y /udfs respectivamente.
Esto almacenado procedimiento pueden, a continuación, ejecutar emitir una solicitud de entrada con el vínculo de recursos:

    POST https://<url>/sprocs/<sproc> HTTP/1.1
    authorization: <<auth>>
    x-ms-date: Thu, 07 Aug 2014 03:43:20 GMT
    
    
    [ { "name": "TestDocument", "book": "Autumn of the Patriarch"}, "Price", 200 ]


A continuación, se pasa la entrada al procedimiento almacenado en el cuerpo de la solicitud. Tenga en cuenta que la entrada se pasa como una matriz JSON de parámetros de entrada. El procedimiento almacenado toma la primera entrada como un documento de un cuerpo de la respuesta. La respuesta que recibimos es la siguiente:

    HTTP/1.1 200 OK
     
    { 
      name: 'TestDocument',
      book: ‘Autumn of the Patriarch’,
      id: ‘V7tQANV3rAkDAAAAAAAAAA==‘,
      ts: 1407830727,
      self: ‘dbs/V7tQAA==/colls/V7tQANV3rAk=/docs/V7tQANV3rAkDAAAAAAAAAA==/’,
      etag: ‘6c006596-0000-0000-0000-53e9cac70000’,
      attachments: ‘attachments/’,
      Price: 200
    }


Desencadenadores, a diferencia de los procedimientos almacenados, no se puede ejecutar directamente. En su lugar se ejecutan como parte de una operación en un documento. Podemos especificar los desencadenadores para ejecutar una solicitud de uso de encabezados HTTP. A continuación se muestra la solicitud para crear un documento.

    POST https://<url>/docs/ HTTP/1.1
    authorization: <<auth>>
    x-ms-date: Thu, 07 Aug 2014 03:43:10 GMT
    x-ms-documentdb-pre-trigger-include: validateDocumentContents 
    x-ms-documentdb-post-trigger-include: bookCreationPostTrigger
    
    
    {
       "name": "newDocument",
       “title”: “The Wizard of Oz”,
       “author”: “Frank Baum”,
       “pages”: 92
    }


Aquí el desencadenador previo para ejecutarse con la solicitud especificado en el encabezado x-ms-documentdb-pre-trigger-include. En consecuencia, cualquier desencadenadores posteriores a la se indican en el encabezado x-ms-documentdb-post-trigger-include. Tenga en cuenta que anteriores y posteriores a la desencadenadores se pueden especificar para una determinada solicitud.

## <a name="sample-code"></a>Código de ejemplo

Puede encontrar más ejemplos de código del servidor (incluidos [Eliminar de forma masiva](https://github.com/Azure/azure-documentdb-js-server/tree/master/samples/stored-procedures/bulkDelete.js)y [Actualizar](https://github.com/Azure/azure-documentdb-js-server/tree/master/samples/stored-procedures/update.js)) en nuestro [repositorio de Github](https://github.com/Azure/azure-documentdb-js-server/tree/master/samples).

¿Desea compartir su impresionante procedimiento almacenado? Por favor, envíenos una solicitud de extracción! 

## <a name="next-steps"></a>Pasos siguientes

Una vez que tenga una o más procedimientos almacenados, desencadenadores y funciones definidas por el usuario creadas, puede cargarlos y verlos en el Portal de Azure con el Explorador de secuencia de comandos. Para obtener más información, vea [ver procedimientos almacenados, desencadenadores y las funciones definidas por el usuario mediante el Explorador de secuencias de comandos de DocumentDB](documentdb-view-scripts.md).

También puede encontrar los siguientes referencias y recursos útiles en la ruta de acceso para obtener más información sobre la programación de servidor DocumentDB:

- [DocumentDB Azure SDK](https://msdn.microsoft.com/library/azure/dn781482.aspx)
- [DocumentDB Studio](https://github.com/mingaliu/DocumentDBStudio/releases)
- [JSON](http://www.json.org/) 
- [JavaScript ECMA 262](http://www.ecma-international.org/publications/standards/Ecma-262.htm)
- [JavaScript: sistema de tipo JSON](http://www.json.org/js.html) 
- [Capacidad de ampliación de base de datos seguras y portátiles](http://dl.acm.org/citation.cfm?id=276339) 
- [Arquitectura de base de datos orientada a servicios](http://dl.acm.org/citation.cfm?id=1066267&coll=Portal&dl=GUIDE) 
- [Hospedaje .NET Runtime en Microsoft SQL server](http://dl.acm.org/citation.cfm?id=1007669)
