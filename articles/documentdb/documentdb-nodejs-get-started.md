<properties
  pageTitle="Tutorial de NoSQL Node.js para DocumentDB | Microsoft Azure"
  description="Tutorial de NoSQL Node.js que crea una aplicación de base de datos y la consola de nodo mediante el SDK de Node.js DocumentDB. DocumentDB es una base de datos NoSQL JSON."
    keywords="tutorial de Node.js, base de datos de nodo"
  services="documentdb"
  documentationCenter="node.js"
  authors="AndrewHoh"
  manager="jhubbard"
  editor="monicar"/>

<tags
  ms.service="documentdb"
  ms.workload="data-services"
  ms.tgt_pltfrm="na"
  ms.devlang="node"
  ms.topic="hero-article"
  ms.date="08/11/2016"
  ms.author="anhoh"/>

# <a name="nosql-nodejs-tutorial-documentdb-nodejs-console-application"></a>Tutorial de NoSQL Node.js: aplicación de consola DocumentDB Node.js  

> [AZURE.SELECTOR]
- [.NET](documentdb-get-started.md)
- [Node.js](documentdb-nodejs-get-started.md)

Bienvenido al tutorial Node.js para el SDK de Node.js DocumentDB de Azure. Después de seguir este tutorial, tendrá una aplicación de consola que crea y consultas DocumentDB recursos, incluida una base de datos de nodo.

Trataremos:

- Crear y conectarse a una cuenta de DocumentDB
- Configuración de la aplicación
- Crear una base de datos de nodo
- Crear una colección
- Crear documentos JSON
- Consultar la colección
- Reemplazo de un documento
- Eliminar un documento
- Eliminar la base de datos de nodo

¿No tiene tiempo? ¡No te preocupes! La solución completa está disponible en [GitHub](https://github.com/Azure-Samples/documentdb-node-getting-started). Para obtener instrucciones rápidas, vea [obtener la solución completa](#GetSolution) .

Una vez haya completado el tutorial Node.js, use los botones de voto en la parte superior e inferior de esta página para enviarnos comentarios. Si le gustaría ponernos en contacto con usted directamente, no dude en incluir su dirección de correo electrónico en sus comentarios.

Ahora vamos a empezar!

## <a name="prerequisites-for-the-nodejs-tutorial"></a>Requisitos previos para el tutorial de Node.js

Asegúrese de que tiene los siguientes:

- Una cuenta de Azure active. Si no tiene una, puede registrarse para una [Prueba gratuita de Azure](https://azure.microsoft.com/pricing/free-trial/).
- [Node.js](https://nodejs.org/) versión v0.10.29 o superior.

## <a name="step-1-create-a-documentdb-account"></a>Paso 1: Crear una cuenta de DocumentDB

Vamos a crear una cuenta de DocumentDB. Si ya tiene una cuenta que desea usar, puede omitir anticipada para [instalar la aplicación Node.js](#SetupNode).

[AZURE.INCLUDE [documentdb-create-dbaccount](../../includes/documentdb-create-dbaccount.md)]

## <a id="SetupNode"></a>Paso 2: Configurar la aplicación de Node.js

1. Abra su terminal favorito.
2. Busque la carpeta o el directorio donde desea guardar la aplicación Node.js.
3. Crear dos archivos de JavaScript vacíos con los siguientes comandos:
  - Windows:
      * ```fsutil file createnew app.js 0```
        * ```fsutil file createnew config.js 0```
  - Linux/OS X:
      * ```touch app.js```
        * ```touch config.js```
4. Instale el módulo documentdb a través de npm. Utilice el siguiente comando:
    * ```npm install documentdb --save```

¡Excelente! Ahora que ha terminado de configurar, vamos a empezar a escribir código.

## <a id="Config"></a>Paso 3: Configurar las configuraciones de la aplicación

Abrir ```config.js``` en el editor de texto.

Entonces, copiar y pegar el fragmento de código siguiente y establecer las propiedades ```config.endpoint``` y ```config.primaryKey``` a su uri del extremo de DocumentDB y la clave principal. Ambas de estas configuraciones pueden encontrarse en el [Portal de Azure](https://portal.azure.com).

![Tutorial de Node.js: captura de pantalla del Portal de Azure, que muestra una cuenta de DocumentDB, con el concentrador activo resaltado, el botón de teclas resaltada en el módulo de cuenta DocumentDB y los valores URI de clave principal y clave secundaria resaltados en el módulo de teclas - base de datos de nodo][keys]

    // ADD THIS PART TO YOUR CODE
    var config = {}

    config.endpoint = "~your DocumentDB endpoint uri here~";
    config.primaryKey = "~your primary key here~";

Copiar y pegar la ```database id```, ```collection id```, y ```JSON documents``` a su ```config``` objeto debajo de donde establecer su ```config.endpoint``` y ```config.authKey``` propiedades. Si ya dispone de datos que desea almacenar en la base de datos, puede usar [la herramienta de migración de datos](documentdb-import-data.md) de DocumentDB en lugar de agregar las definiciones de documento.

    config.endpoint = "~your DocumentDB endpoint uri here~";
    config.primaryKey = "~your primary key here~";

    // ADD THIS PART TO YOUR CODE
    config.database = {
        "id": "FamilyDB"
    };

    config.collection = {
        "id": "FamilyColl"
    };

    config.documents = {
        "Andersen": {
            "id": "Anderson.1",
            "lastName": "Andersen",
            "parents": [{
                "firstName": "Thomas"
            }, {
                    "firstName": "Mary Kay"
                }],
            "children": [{
                "firstName": "Henriette Thaulow",
                "gender": "female",
                "grade": 5,
                "pets": [{
                    "givenName": "Fluffy"
                }]
            }],
            "address": {
                "state": "WA",
                "county": "King",
                "city": "Seattle"
            }
        },
        "Wakefield": {
            "id": "Wakefield.7",
            "parents": [{
                "familyName": "Wakefield",
                "firstName": "Robin"
            }, {
                    "familyName": "Miller",
                    "firstName": "Ben"
                }],
            "children": [{
                "familyName": "Merriam",
                "firstName": "Jesse",
                "gender": "female",
                "grade": 8,
                "pets": [{
                    "givenName": "Goofy"
                }, {
                        "givenName": "Shadow"
                    }]
            }, {
                    "familyName": "Miller",
                    "firstName": "Lisa",
                    "gender": "female",
                    "grade": 1
                }],
            "address": {
                "state": "NY",
                "county": "Manhattan",
                "city": "NY"
            },
            "isRegistered": false
        }
    };


La base de datos, colección y definiciones de documento actúe como su DocumentDB ```database id```, ```collection id```y los datos de documentos.

Por último, exportar su ```config``` objeto, por lo que puede hacer referencia a él dentro de la ```app.js``` archivo.

            },
            "isRegistered": false
        }
    };

    // ADD THIS PART TO YOUR CODE
    module.exports = config;

##<a id="Connect"></a>Paso 4: Conectar con una cuenta de DocumentDB

Abra su vaciar ```app.js``` archivo en el editor de texto. Copie y pegue el código siguiente para importar la ```documentdb``` módulo y su recién creado ```config``` módulo.

    // ADD THIS PART TO YOUR CODE
    "use strict";

    var documentClient = require("documentdb").DocumentClient;
    var config = require("./config");
    var url = require('url');

Copie y pegue el código para usar la versión guardada anteriormente ```config.endpoint``` y ```config.primaryKey``` para crear un nuevo DocumentClient.

    var config = require("./config");
    var url = require('url');

    // ADD THIS PART TO YOUR CODE
    var client = new documentClient(config.endpoint, { "masterKey": config.primaryKey });

Ahora que tiene el código para iniciar el cliente de documentdb, echemos un vistazo a trabajar con recursos DocumentDB.

## <a name="step-5-create-a-node-database"></a>Paso 5: Crear una base de datos de nodo
Copie y pegue el código siguiente para establecer el estado HTTP para no se encontró la dirección url de la base de datos y la dirección url de la colección. Estas direcciones URL son cómo encontrarán el cliente de DocumentDB de la base de datos correcto y colección.

    var client = new documentClient(config.endpoint, { "masterKey": config.primaryKey });

    // ADD THIS PART TO YOUR CODE
    var HttpStatusCodes = { NOTFOUND: 404 };
    var databaseUrl = `dbs/${config.database.id}`;
    var collectionUrl = `${databaseUrl}/colls/${config.collection.id}`;

Puede crearse una [base de datos](documentdb-resources.md#databases) usando la función [createDatabase](https://azure.github.io/azure-documentdb-node/DocumentClient.html) de la clase **DocumentClient** . Una base de datos es el contenedor lógico de dividir entre colecciones de almacenamiento de documentos.

Copiar y pegar la función **getDatabase** para crear la nueva base de datos en el archivo app.js con el ```id``` especificado en el ```config``` objeto. La función comprueba si la base de datos con el mismo ```FamilyRegistry``` identificador no existe. Si existe, se volverá a dicha base de datos en lugar de crear uno nuevo.

    var collectionUrl = `${databaseUrl}/colls/${config.collection.id}`;

    // ADD THIS PART TO YOUR CODE
    function getDatabase() {
        console.log(`Getting database:\n${config.database.id}\n`);

        return new Promise((resolve, reject) => {
            client.readDatabase(databaseUrl, (err, result) => {
                if (err) {
                    if (err.code == HttpStatusCodes.NOTFOUND) {
                        client.createDatabase(config.database, (err, created) => {
                            if (err) reject(err)
                            else resolve(created);
                        });
                    } else {
                        reject(err);
                    }
                } else {
                    resolve(result);
                }
            });
        });
    }

Copie y pegue el código siguiente que define la función **getDatabase** para agregar la aplicación auxiliar función **Salir** que se imprimirá el mensaje de salida y la llamada a función **getDatabase** .

                } else {
                    resolve(result);
                }
            });
        });
    }

    // ADD THIS PART TO YOUR CODE
    function exit(message) {
        console.log(message);
        console.log('Press any key to exit');
        process.stdin.setRawMode(true);
        process.stdin.resume();
        process.stdin.on('data', process.exit.bind(process, 0));
    }

    getDatabase()
    .then(() => { exit(`Completed successfully`); })
    .catch((error) => { exit(`Completed with error ${JSON.stringify(error)}`) });

En el terminal, busque su ```app.js``` y ejecute el comando:```node app.js```

¡Felicidades! Ha creado correctamente una base de datos de DocumentDB.

##<a id="CreateColl"></a>Paso 6: Crear una colección  

> [AZURE.WARNING] **CreateDocumentCollectionAsync** creará una nueva colección, que tiene implicaciones de precios. Para obtener más detalles, visite nuestra [página de precios](https://azure.microsoft.com/pricing/details/documentdb/).

Una [colección](documentdb-resources.md#collections) puede crearse con la función [createCollection](https://azure.github.io/azure-documentdb-node/DocumentClient.html) de la clase **DocumentClient** . Una colección es un contenedor de documentos JSON y lógica de la aplicación de JavaScript asociada.

Copiar y pegar la función **getCollection** debajo de la función **getDatabase** para crear la nueva colección con el ```id``` especificado en el ```config``` objeto. De nuevo, le enviaremos para asegurarse de que un conjunto con el mismo ```FamilyCollection``` identificador no existe. Si existe, se volverá a esa colección en lugar de crear uno nuevo.

                } else {
                    resolve(result);
                }
            });
        });
    }

    // ADD THIS PART TO YOUR CODE
    function getCollection() {
        console.log(`Getting collection:\n${config.collection.id}\n`);

        return new Promise((resolve, reject) => {
            client.readCollection(collectionUrl, (err, result) => {
                if (err) {
                    if (err.code == HttpStatusCodes.NOTFOUND) {
                        client.createCollection(databaseUrl, config.collection, { offerThroughput: 400 }, (err, created) => {
                            if (err) reject(err)
                            else resolve(created);
                        });
                    } else {
                        reject(err);
                    }
                } else {
                    resolve(result);
                }
            });
        });
    }

Copie y pegue el código siguiente de la llamada a **getDatabase** para ejecutar la función **getCollection** .

    getDatabase()

    // ADD THIS PART TO YOUR CODE
    .then(() => getCollection())
    // ENDS HERE

    .then(() => { exit(`Completed successfully`); })
    .catch((error) => { exit(`Completed with error ${JSON.stringify(error)}`) });

En el terminal, busque su ```app.js``` y ejecute el comando:```node app.js```

¡Felicidades! Ha creado correctamente una colección de DocumentDB.

##<a id="CreateDoc"></a>Paso 7: Crear un documento
Puede crearse un [documento](documentdb-resources.md#documents) mediante la función [createDocument](https://azure.github.io/azure-documentdb-node/DocumentClient.html) de la clase **DocumentClient** . Los documentos son contenido JSON (arbitrario) definidas por el usuario. Ahora puede insertar un documento en DocumentDB.

Copiar y pegar la función **getFamilyDocument** debajo de la función **getCollection** para crear los documentos que contienen los datos JSON guardados en el ```config``` objeto. De nuevo, le enviaremos para asegurarse de que ya no existe un documento con el mismo id.

                } else {
                    resolve(result);
                }
            });
        });
    }

    // ADD THIS PART TO YOUR CODE
    function getFamilyDocument(document) {
        let documentUrl = `${collectionUrl}/docs/${document.id}`;
        console.log(`Getting document:\n${document.id}\n`);

        return new Promise((resolve, reject) => {
            client.readDocument(documentUrl, { partitionKey: document.district }, (err, result) => {
                if (err) {
                    if (err.code == HttpStatusCodes.NOTFOUND) {
                        client.createDocument(collectionUrl, document, (err, created) => {
                            if (err) reject(err)
                            else resolve(created);
                        });
                    } else {
                        reject(err);
                    }
                } else {
                    resolve(result);
                }
            });
        });
    };

Copie y pegue el código siguiente de la llamada a **getCollection** para ejecutar la función **getFamilyDocument** .

    getDatabase()
    .then(() => getCollection())

    // ADD THIS PART TO YOUR CODE
    .then(() => getFamilyDocument(config.documents.Andersen))
    .then(() => getFamilyDocument(config.documents.Wakefield))
    // ENDS HERE

    .then(() => { exit(`Completed successfully`); })
    .catch((error) => { exit(`Completed with error ${JSON.stringify(error)}`) });

En el terminal, busque su ```app.js``` y ejecute el comando:```node app.js```

¡Felicidades! Ha creado correctamente un documentos DocumentDB.

![Base de datos de Node.js tutorial nodo - diagrama ilustrar la relación jerárquica entre la cuenta, la base de datos, la colección y los documentos:](./media/documentdb-nodejs-get-started/node-js-tutorial-account-database.png)

##<a id="Query"></a>Paso 8: Consultar DocumentDB recursos

DocumentDB admite [consultas avanzadas](documentdb-sql-query.md) en documentos JSON almacenados en cada colección. El código de ejemplo siguiente muestra una consulta que se puede ejecutar en los documentos de la colección.

Copiar y pegar la función **queryCollection** debajo de la función **getFamilyDocument** . DocumentDB admite consultas de tipo SQL como se muestra a continuación. Para obtener más información sobre cómo crear consultas complejas, consulte la [documentación de la consulta](documentdb-sql-query.md)y la [Animación de la consulta](https://www.documentdb.com/sql/demo) .

                } else {
                    resolve(result);
                }
            });
        });
    }

    // ADD THIS PART TO YOUR CODE
    function queryCollection() {
        console.log(`Querying collection through index:\n${config.collection.id}`);

        return new Promise((resolve, reject) => {
            client.queryDocuments(
                collectionUrl,
                'SELECT VALUE r.children FROM root r WHERE r.lastName = "Andersen"'
            ).toArray((err, results) => {
                if (err) reject(err)
                else {
                    for (var queryResult of results) {
                        let resultString = JSON.stringify(queryResult);
                        console.log(`\tQuery returned ${resultString}`);
                    }
                    console.log();
                    resolve(results);
                }
            });
        });
    };


El siguiente diagrama muestra cómo la sintaxis de la consulta SQL DocumentDB se llama con respecto a la colección creó.

![Base de datos de Node.js tutorial nodo - diagrama que ilustra el ámbito y lo que significa que de la consulta:](./media/documentdb-nodejs-get-started/node-js-tutorial-collection-documents.png)

La palabra clave [FROM](documentdb-sql-query.md#from-clause) es opcional en la consulta porque ya están orientadas al DocumentDB consultas para una sola colección. Por lo tanto, "De familias f" se pueden intercambiar con "R de raíz", o cualquier otra variable nombre elija. DocumentDB se inferir esa familias, raíz o el nombre de variable elegido, hacer referencia a la colección actual de forma predeterminada.

Copie y pegue el código siguiente de la llamada a **getFamilyDocument** para ejecutar la función **queryCollection** .

    .then(() => getFamilyDocument(config.documents.Andersen))
    .then(() => getFamilyDocument(config.documents.Wakefield))

    // ADD THIS PART TO YOUR CODE
    .then(() => queryCollection())
    // ENDS HERE

    .then(() => { exit(`Completed successfully`); })
    .catch((error) => { exit(`Completed with error ${JSON.stringify(error)}`) });

En el terminal, busque su ```app.js``` y ejecute el comando:```node app.js```

¡Felicidades! Correctamente que haya consultado DocumentDB documentos.

##<a id="ReplaceDocument"></a>Paso 9: Reemplazar un documento
DocumentDB es compatible con los documentos JSON reemplazar.

Copiar y pegar la función **replaceDocument** debajo de la función **queryCollection** .

                    }
                    console.log();
                    resolve(result);
                }
            });
        });
    }

    // ADD THIS PART TO YOUR CODE
    function replaceFamilyDocument(document) {
        let documentUrl = `${collectionUrl}/docs/${document.id}`;
        console.log(`Replacing document:\n${document.id}\n`);
        document.children[0].grade = 6;

        return new Promise((resolve, reject) => {
            client.replaceDocument(documentUrl, document, (err, result) => {
                if (err) reject(err);
                else {
                    resolve(result);
                }
            });
        });
    };

Copie y pegue el código siguiente de la llamada a **queryCollection** para ejecutar la función **replaceDocument** . Además, agregue el código para llamar a **queryCollection** para comprobar que el documento ha cambiado correctamente.

    .then(() => getFamilyDocument(config.documents.Andersen))
    .then(() => getFamilyDocument(config.documents.Wakefield))
    .then(() => queryCollection())

    // ADD THIS PART TO YOUR CODE
    .then(() => replaceFamilyDocument(config.documents.Andersen))
    .then(() => queryCollection())
    // ENDS HERE

    .then(() => { exit(`Completed successfully`); })
    .catch((error) => { exit(`Completed with error ${JSON.stringify(error)}`) });

En el terminal, busque su ```app.js``` y ejecute el comando:```node app.js```

¡Felicidades! Un documento de DocumentDB se reemplazaron correctamente.

##<a id="DeleteDocument"></a>Paso 10: Eliminar un documento
DocumentDB admite la eliminación de JSON de documentos.

Copiar y pegar la función **deleteDocument** debajo de la función **replaceDocument** .

                else {
                    resolve(result);
                }
            });
        });
    };

    // ADD THIS PART TO YOUR CODE
    function deleteFamilyDocument(document) {
        let documentUrl = `${collectionUrl}/docs/${document.id}`;
        console.log(`Deleting document:\n${document.id}\n`);

        return new Promise((resolve, reject) => {
            client.deleteDocument(documentUrl, (err, result) => {
                if (err) reject(err);
                else {
                    resolve(result);
                }
            });
        });
    };

Copie y pegue el código siguiente de la llamada a la segunda **queryCollection** para ejecutar la función **deleteDocument** .

    .then(() => queryCollection())
    .then(() => replaceFamilyDocument(config.documents.Andersen))
    .then(() => queryCollection())

    // ADD THIS PART TO YOUR CODE
    .then(() => deleteFamilyDocument(config.documents.Andersen))
    // ENDS HERE

    .then(() => { exit(`Completed successfully`); })
    .catch((error) => { exit(`Completed with error ${JSON.stringify(error)}`) });

En el terminal, busque su ```app.js``` y ejecute el comando:```node app.js```

¡Felicidades! Se ha eliminado correctamente un documento de DocumentDB.

##<a id="DeleteDatabase"></a>Paso 11: Eliminar la base de datos de nodo

Eliminar la base de datos creada se quitarán la base de datos y todos los recursos de los niños (colecciones, documentos, etcetera).

Copie y pegue el siguiente fragmento de código (función **Liberador de espacio**) para quitar la base de datos y todos los recursos de los niños.

                else {
                    resolve(result);
                }
            });
        });
    };

    // ADD THIS PART TO YOUR CODE
    function cleanup() {
        console.log(`Cleaning up by deleting database ${config.database.id}`);

        return new Promise((resolve, reject) => {
            client.deleteDatabase(databaseUrl, (err) => {
                if (err) reject(err)
                else resolve(null);
            });
        });
    }

Copie y pegue el código siguiente de la llamada a **deleteDocument** para ejecutar la función de **limpieza** .

    .then(() => deleteFamilyDocument(config.documents.Andersen))

    // ADD THIS PART TO YOUR CODE
    .then(() => cleanup())
    // ENDS HERE

    .then(() => { exit(`Completed successfully`); })
    .catch((error) => { exit(`Completed with error ${JSON.stringify(error)}`) });

##<a id="Run"></a>Paso 12: Ejecutar la aplicación Node.js todo!

Totalmente, la secuencia para llamar a las funciones debe tener un aspecto similar a este:

    getDatabase()
    .then(() => getCollection())
    .then(() => getFamilyDocument(config.documents.Andersen))
    .then(() => getFamilyDocument(config.documents.Wakefield))
    .then(() => queryCollection())
    .then(() => replaceFamilyDocument(config.documents.Andersen))
    .then(() => queryCollection())
    .then(() => deleteFamilyDocument(config.documents.Andersen))
    .then(() => cleanup())
    .then(() => { exit(`Completed successfully`); })
    .catch((error) => { exit(`Completed with error ${JSON.stringify(error)}`) });

En el terminal, busque su ```app.js``` y ejecute el comando:```node app.js```

Debe ver el resultado de la aplicación de introducción de obtener. El resultado debe coincidir con el texto de ejemplo siguiente.

    Getting database:
    FamilyDB

    Getting collection:
    FamilyColl

    Getting document:
    Anderson.1

    Getting document:
    Wakefield.7

    Querying collection through index:
    FamilyColl
        Query returned [{"firstName":"Henriette Thaulow","gender":"female","grade":5,"pets":[{"givenName":"Fluffy"}]}]

    Replacing document:
    Anderson.1

    Querying collection through index:
    FamilyColl
        Query returned [{"firstName":"Henriette Thaulow","gender":"female","grade":6,"pets":[{"givenName":"Fluffy"}]}]

    Deleting document:
    Anderson.1

    Cleaning up by deleting database FamilyDB
    Completed successfully
    Press any key to exit

¡Felicidades! Ha creado se haya completado el tutorial Node.js y tiene la aplicación de consola DocumentDB primera!

## <a id="GetSolution"></a>Obtener la solución del tutorial Node.js completada
Para generar la solución de GetStarted que contiene todos los ejemplos de este artículo, necesita lo siguiente:

-   [Cuenta de DocumentDB][documentdb-create-account].
-   La solución de [GetStarted](https://github.com/Azure-Samples/documentdb-node-getting-started) disponible en GitHub.

Instale el módulo **documentdb** a través de npm. Utilice el siguiente comando:
* ```npm install documentdb --save```

A continuación, en la ```config.js``` de archivos, actualice los valores config.endpoint y config.authKey como se describe en [paso 3: establecer las configuraciones de la aplicación](#Config).

## <a name="next-steps"></a>Pasos siguientes

-   ¿Quiere una muestra de Node.js más compleja? Consulte [crear una aplicación web de Node.js con DocumentDB](documentdb-nodejs-application.md).
-  Obtenga información sobre cómo [supervisar una cuenta de DocumentDB](documentdb-monitor-accounts.md).
-  Ejecutar consultas en nuestro conjunto de datos de ejemplo en la [Animación de la consulta](https://www.documentdb.com/sql/demo).
-  Más información sobre el modelo de programación en la sección de desarrollo de la [página de la documentación de DocumentDB](https://azure.microsoft.com/documentation/services/documentdb/).

[documentdb-create-account]: documentdb-create-account.md
[documentdb-manage]: documentdb-manage.md

[keys]: media/documentdb-nodejs-get-started/node-js-tutorial-keys.png
