<properties 
    pageTitle="Obtenga información sobre Node.js - DocumentDB Node.js Tutorial | Microsoft Azure" 
    description="¡Obtenga información sobre Node.js! Tutorial explora cómo usar Microsoft Azure DocumentDB para almacenar y tener acceso a datos desde una aplicación web de Node.js Express hospedada en sitios Web de Azure." 
    keywords="Desarrollo de aplicaciones, tutorial de base de datos, obtenga información sobre node.js, tutorial node.js, documentdb, azure, Microsoft azure"
    services="documentdb" 
    documentationCenter="nodejs" 
    authors="syamkmsft" 
    manager="jhubbard" 
    editor="cgronlun"/>

<tags 
    ms.service="documentdb" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="nodejs" 
    ms.topic="hero-article" 
    ms.date="08/25/2016" 
    ms.author="syamk"/>

# <a name="_Toc395783175"></a>Crear una aplicación web de Node.js con DocumentDB

> [AZURE.SELECTOR]
- [.NET](documentdb-dotnet-application.md)
- [Node.js](documentdb-nodejs-application.md)
- [Java](documentdb-java-application.md)
- [Python](documentdb-python-application.md)

Este tutorial Node.js muestra cómo usar el servicio de DocumentDB de Azure para almacenar y datos de access desde una aplicación de Node.js Express hospedado en sitios Web de Azure.

Se recomienda Introducción observando el siguiente vídeo, donde aprenderá cómo aprovisionar una cuenta de base de datos de Azure DocumentDB y almacenar documentos JSON en la aplicación de Node.js. 

> [AZURE.VIDEO azure-demo-getting-started-with-azure-documentdb-on-nodejs-in-linux]

A continuación, volver a este tutorial Node.js, donde puede aprender las respuestas a las preguntas siguientes:

- ¿Cómo funcionan con DocumentDB uso del módulo de npm documentdb?
- ¿Cómo puedo implementar la aplicación web a sitios Web de Azure?

Siguiendo este tutorial de base de datos, va a crear una aplicación simple de administración de tareas basada en web que permite crear, recuperar y finalización de las tareas. Las tareas se guardarán como documentos JSON en DocumentDB de Azure.

![Captura de pantalla de la aplicación de mi lista de tareas pendientes creada en este tutorial Node.js](./media/documentdb-nodejs-application/image1.png)

¿No tiene tiempo para completar el tutorial y solo desea obtener la solución completa? No es un problema, puede obtener la solución de ejemplo completos de [GitHub][].

## <a name="_Toc395783176"></a>Requisitos previos

> [AZURE.TIP] Este tutorial Node.js supone que tiene alguna experiencia anterior con Node.js y sitios Web de Azure.

Antes de seguir las instrucciones de este artículo, debe asegurarse de que tiene los siguientes:

- Una cuenta de Azure active. Si no tiene una cuenta, puede crear una cuenta de prueba gratuita de dos minutos. Para obtener información detallada, vea [Prueba gratuita de Azure](https://azure.microsoft.com/pricing/free-trial/).
- [Node.js][] versión v0.10.29 o superior.
- [Generador de Express](http://www.expressjs.com/starter/generator.html) (puede instalar esto a través de `npm install express-generator -g`)
- [Git][].

## <a name="_Toc395637761"></a>Paso 1: Crear una cuenta de base de datos DocumentDB

Empecemos creando una cuenta de DocumentDB. Si ya tiene una cuenta, vaya a [paso 2: crear una nueva aplicación de Node.js](#_Toc395783178).

[AZURE.INCLUDE [documentdb-create-dbaccount](../../includes/documentdb-create-dbaccount.md)]

[AZURE.INCLUDE [documentdb-keys](../../includes/documentdb-keys.md)]

## <a name="_Toc395783178"></a>Paso 2: Más información sobre cómo crear una nueva aplicación de Node.js

Ahora vamos a aprender a crear un proyecto Node.js Hola mundo básico mediante el marco [Express](http://expressjs.com/) .

1. Abra su terminal favorito.

2. Usar el generador de express para generar una nueva aplicación denominada **todo**.

        express todo

3. Abra el nuevo directorio **todo** e instale dependencias.

        cd todo
        npm install

4. Ejecutar la nueva aplicación.

        npm start

5. Se puede ver la nueva aplicación desplazándose su explorador [http://localhost:3000](http://localhost:3000).

    ![Obtenga información sobre Node.js - captura de pantalla de la aplicación Hola a todos en una ventana del explorador](./media/documentdb-nodejs-application/image12.png)

## <a name="_Toc395783179"></a>Paso 3: Instalar módulos adicionales

El archivo **package.json** es uno de los archivos creados en la raíz del proyecto. Este archivo contiene una lista de módulos adicionales necesarios para la aplicación Node.js. Más adelante, cuando se implementa esta aplicación a los sitios Web un Azure, este archivo se usa para determinar qué módulos deben instalarse en Azure para ser compatible con la aplicación. Es necesario instalar paquetes de más de dos para este tutorial.

1. En terminal, instale el módulo de **asincrónico** a través de npm.

        npm install async --save

1. Instale el módulo **documentdb** a través de npm. Esto es el módulo dónde se realizará la magia de DocumentDB.

        npm install documentdb --save

3. Una comprobación rápida del archivo **package.json** de la aplicación debe mostrar los módulos adicionales. Este archivo le indicará los paquetes que desea descargar e instalar cuando se ejecuta la aplicación de Azure. Debe ser similar el ejemplo siguiente.

    ![Captura de pantalla de la ficha package.json](./media/documentdb-nodejs-application/image17.png)

    Esto indica a nodo (y más tarde Azure) que su aplicación depende de estos módulos adicionales.

## <a name="_Toc395783180"></a>Paso 4: Utilizar el servicio de DocumentDB en una aplicación de nodo

Encarga de todos los la instalación y configuración iniciales, ahora vamos a obtener hacia abajo a ¿por qué estamos aquí y que es escribir código con DocumentDB de Azure.

### <a name="create-the-model"></a>Crear el modelo

1. En el directorio del proyecto, cree un nuevo directorio denominado **modelos**.
2. En el directorio de **modelos** , cree un nuevo archivo denominado **taskDao.js**. Este archivo contiene el modelo para las tareas creadas con la aplicación.
3. En el mismo directorio de **modelos** , cree otro nuevo archivo denominado **docdbUtils.js**. Este archivo contiene código útil, reutilizable, que usamos en nuestra aplicación. 
4. Copie el código siguiente en a **docdbUtils.js**

        var DocumentDBClient = require('documentdb').DocumentClient;
            
        var DocDBUtils = {
            getOrCreateDatabase: function (client, databaseId, callback) {
                var querySpec = {
                    query: 'SELECT * FROM root r WHERE r.id= @id',
                    parameters: [{
                        name: '@id',
                        value: databaseId
                    }]
                };
        
                client.queryDatabases(querySpec).toArray(function (err, results) {
                    if (err) {
                        callback(err);
        
                    } else {
                        if (results.length === 0) {
                            var databaseSpec = {
                                id: databaseId
                            };
        
                            client.createDatabase(databaseSpec, function (err, created) {
                                callback(null, created);
                            });
        
                        } else {
                            callback(null, results[0]);
                        }
                    }
                });
            },
        
            getOrCreateCollection: function (client, databaseLink, collectionId, callback) {
                var querySpec = {
                    query: 'SELECT * FROM root r WHERE r.id=@id',
                    parameters: [{
                        name: '@id',
                        value: collectionId
                    }]
                };             
                
                client.queryCollections(databaseLink, querySpec).toArray(function (err, results) {
                    if (err) {
                        callback(err);
        
                    } else {        
                        if (results.length === 0) {
                            var collectionSpec = {
                                id: collectionId
                            };
                            
                            client.createCollection(databaseLink, collectionSpec, function (err, created) {
                                callback(null, created);
                            });
        
                        } else {
                            callback(null, results[0]);
                        }
                    }
                });
            }
        };
                
        module.exports = DocDBUtils;

    > [AZURE.TIP] createCollection toma un parámetro de requestOptions opcional que se pueden usar para especificar el tipo de la oferta para la colección. Si no se proporciona ningún valor requestOptions.offerType, a continuación, la colección se creará utilizando el tipo de oferta predeterminado.
    >
    > Para obtener más información sobre tipos de ofrecer DocumentDB, consulte [niveles de rendimiento en DocumentDB](documentdb-performance-levels.md) 
        
3. Guarde y cierre el archivo **docdbUtils.js** .

4. Al principio del archivo **taskDao.js** , agregue el código siguiente para hacer referencia a la **DocumentDBClient** y la **docdbUtils.js** que se creó anteriormente:

        var DocumentDBClient = require('documentdb').DocumentClient;
        var docdbUtils = require('./docdbUtils');

4. A continuación, agregará código para definir y exportar el objeto de tarea. Esto es responsable de inicialización nuestro objeto de tarea y la configuración de la base de datos y la colección de documentos que usaremos.

        function TaskDao(documentDBClient, databaseId, collectionId) {
          this.client = documentDBClient;
          this.databaseId = databaseId;
          this.collectionId = collectionId;
        
          this.database = null;
          this.collection = null;
        }
        
        module.exports = TaskDao;

5. A continuación, agregue el código siguiente para definir métodos adicionales en el objeto de tarea, que permiten la interacción con los datos almacenados en DocumentDB.

        TaskDao.prototype = {
            init: function (callback) {
                var self = this;
        
                docdbUtils.getOrCreateDatabase(self.client, self.databaseId, function (err, db) {
                    if (err) {
                        callback(err);
                    } else {
                        self.database = db;
                        docdbUtils.getOrCreateCollection(self.client, self.database._self, self.collectionId, function (err, coll) {
                            if (err) {
                                callback(err);
        
                            } else {
                                self.collection = coll;
                            }
                        });
                    }
                });
            },
        
            find: function (querySpec, callback) {
                var self = this;
        
                self.client.queryDocuments(self.collection._self, querySpec).toArray(function (err, results) {
                    if (err) {
                        callback(err);
        
                    } else {
                        callback(null, results);
                    }
                });
            },
        
            addItem: function (item, callback) {
                var self = this;
        
                item.date = Date.now();
                item.completed = false;
        
                self.client.createDocument(self.collection._self, item, function (err, doc) {
                    if (err) {
                        callback(err);
        
                    } else {
                        callback(null, doc);
                    }
                });
            },
        
            updateItem: function (itemId, callback) {
                var self = this;
        
                self.getItem(itemId, function (err, doc) {
                    if (err) {
                        callback(err);
        
                    } else {
                        doc.completed = true;
        
                        self.client.replaceDocument(doc._self, doc, function (err, replaced) {
                            if (err) {
                                callback(err);
        
                            } else {
                                callback(null, replaced);
                            }
                        });
                    }
                });
            },
        
            getItem: function (itemId, callback) {
                var self = this;
        
                var querySpec = {
                    query: 'SELECT * FROM root r WHERE r.id = @id',
                    parameters: [{
                        name: '@id',
                        value: itemId
                    }]
                };
        
                self.client.queryDocuments(self.collection._self, querySpec).toArray(function (err, results) {
                    if (err) {
                        callback(err);
        
                    } else {
                        callback(null, results[0]);
                    }
                });
            }
        };

6. Guarde y cierre el archivo **taskDao.js** . 

### <a name="create-the-controller"></a>Crear el controlador

1. En el directorio de **rutas** de su proyecto, cree un nuevo archivo denominado **tasklist.js**. 
2. Agregue el código siguiente a **tasklist.js**. Esto carga los módulos DocumentDBClient y asincrónica, que se utilizan en **tasklist.js**. Esto también define la función de la **lista de tareas** , que se pasa una instancia del objeto de **tarea** definido anteriormente:

        var DocumentDBClient = require('documentdb').DocumentClient;
        var async = require('async');
        
        function TaskList(taskDao) {
          this.taskDao = taskDao;
        }
        
        module.exports = TaskList;

3. Continúe agregando el archivo **tasklist.js** mediante la adición de los métodos utilizados para **showTasks, addTask**y **completeTasks**:
        
        TaskList.prototype = {
            showTasks: function (req, res) {
                var self = this;
        
                var querySpec = {
                    query: 'SELECT * FROM root r WHERE r.completed=@completed',
                    parameters: [{
                        name: '@completed',
                        value: false
                    }]
                };
        
                self.taskDao.find(querySpec, function (err, items) {
                    if (err) {
                        throw (err);
                    }
        
                    res.render('index', {
                        title: 'My ToDo List ',
                        tasks: items
                    });
                });
            },
        
            addTask: function (req, res) {
                var self = this;
                var item = req.body;
        
                self.taskDao.addItem(item, function (err) {
                    if (err) {
                        throw (err);
                    }
        
                    res.redirect('/');
                });
            },
        
            completeTask: function (req, res) {
                var self = this;
                var completedTasks = Object.keys(req.body);
        
                async.forEach(completedTasks, function taskIterator(completedTask, callback) {
                    self.taskDao.updateItem(completedTask, function (err) {
                        if (err) {
                            callback(err);
                        } else {
                            callback(null);
                        }
                    });
                }, function goHome(err) {
                    if (err) {
                        throw err;
                    } else {
                        res.redirect('/');
                    }
                });
            }
        };


4. Guarde y cierre el archivo **tasklist.js** .
 
### <a name="add-configjs"></a>Agregar config.js

1. En el directorio del proyecto, cree un nuevo archivo denominado **config.js**.
2. Agregue lo siguiente a **config.js**. Define los valores de configuración y valores necesarios para la aplicación.

        var config = {}
        
        config.host = process.env.HOST || "[the URI value from the DocumentDB Keys blade on http://portal.azure.com]";
        config.authKey = process.env.AUTH_KEY || "[the PRIMARY KEY value from the DocumentDB Keys blade on http://portal.azure.com]";
        config.databaseId = "ToDoList";
        config.collectionId = "Items";
        
        module.exports = config;

3. En el archivo **config.js** , actualice los valores de HOST y AUTH_KEY con los valores que se encuentra en la hoja de claves de su cuenta de DocumentDB en el [Portal de Microsoft Azure](https://portal.azure.com):

4. Guarde y cierre el archivo **config.js** .
 
### <a name="modify-appjs"></a>Modificar app.js

1. En el directorio del proyecto, abra el archivo **app.js** . Este archivo se creó con una versión anterior cuando se creó la aplicación web de Express.
2. Agregue el código siguiente a la parte superior de **app.js**
    
        var DocumentDBClient = require('documentdb').DocumentClient;
        var config = require('./config');
        var TaskList = require('./routes/tasklist');
        var TaskDao = require('./models/taskDao');

3. Este código define el archivo de configuración que se utilizará y continúa con leer los valores de este archivo en algunas variables usará pronto.
4. Reemplazar las siguientes dos líneas en el archivo **app.js** :

        app.use('/', routes);
        app.use('/users', users); 

      con el fragmento de código siguiente:

        var docDbClient = new DocumentDBClient(config.host, {
            masterKey: config.authKey
        });
        var taskDao = new TaskDao(docDbClient, config.databaseId, config.collectionId);
        var taskList = new TaskList(taskDao);
        taskDao.init();
        
        app.get('/', taskList.showTasks.bind(taskList));
        app.post('/addtask', taskList.addTask.bind(taskList));
        app.post('/completetask', taskList.completeTask.bind(taskList));
        app.set('view engine', 'jade');



6. Estas líneas definen una nueva instancia de nuestro objeto **TaskDao** , con una nueva conexión a DocumentDB (con los valores de lectura desde la **config.js**), inicializar el objeto de tarea y, a continuación, enlazan acciones de formulario a los métodos en el controlador de nuestra **lista de tareas** . 

7. Por último, guarde y cierre el archivo **app.js** , casi hemos terminado.
 
## <a name="_Toc395783181"></a>Paso 5: Crear una interfaz de usuario

Ahora nos centraremos a la creación de la interfaz de usuario para que un usuario realmente puede interactuar con la aplicación. La aplicación de Express que hemos creado usa **Jade** como el motor de vista. Para obtener más información sobre Jade, consulte [http://jade-lang.com/](http://jade-lang.com/).

1. El archivo **layout.jade** en el directorio de **vistas** se usa como una plantilla global para otros archivos de **.jade** . En este paso se modificarla para usar el [Inicio de Twitter](https://github.com/twbs/bootstrap), que es un Kit de herramientas que facilita el diseño de un sitio Web de aspecto interesante. 
2. Abrir el archivo **layout.jade** que se encuentra en la carpeta **vistas** y reemplace el contenido con las siguientes acciones:
    
        doctype html
        html
          head
            title= title
            link(rel='stylesheet', href='//ajax.aspnetcdn.com/ajax/bootstrap/3.3.2/css/bootstrap.min.css')
            link(rel='stylesheet', href='/stylesheets/style.css')
          body
            nav.navbar.navbar-inverse.navbar-fixed-top
              div.navbar-header
                a.navbar-brand(href='#') My Tasks
            block content
            script(src='//ajax.aspnetcdn.com/ajax/jQuery/jquery-1.11.2.min.js')
            script(src='//ajax.aspnetcdn.com/ajax/bootstrap/3.3.2/bootstrap.min.js')



    Esto indica el motor **Jade** para representar HTML para nuestra aplicación eficaz y crea un **bloque** denominado **contenido** donde podemos proporcionarle el diseño de las páginas de contenido.
    Guarde y cierre este archivo **layout.jade** .

4. Ahora, abra el archivo de **index.jade** , la vista que utilizará la aplicación y a continuación, reemplace el contenido del archivo con lo siguiente:

        extends layout
        
        block content
          h1 #{title}
          br
        
          form(action="/completetask", method="post")
            table.table.table-striped.table-bordered
              tr
                td Name
                td Category
                td Date
                td Complete
              if (typeof tasks === "undefined")
                tr
                  td
              else
                each task in tasks
                  tr
                    td #{task.name}
                    td #{task.category}
                    - var date  = new Date(task.date);
                    - var day   = date.getDate();
                    - var month = date.getMonth() + 1;
                    - var year  = date.getFullYear();
                    td #{month + "/" + day + "/" + year}
                    td
                      input(type="checkbox", name="#{task.id}", value="#{!task.completed}", checked=task.completed)
            button.btn(type="submit") Update tasks
          hr
          form.well(action="/addtask", method="post")
            label Item Name:
            input(name="name", type="textbox")
            label Item Category:
            input(name="category", type="textbox")
            br
            button.btn(type="submit") Add item

    Este diseño extiende y proporciona contenido para el marcador de posición de **contenido** que hemos visto en el archivo **layout.jade** antes.
    
    En este diseño creamos dos formas HTML. 
    El primer formulario contiene una tabla de nuestros datos y un botón que nos permite actualizar elementos publicando en **/completetask** método de nuestro controlador.
    El segundo formulario contiene dos campos de entrada y un botón que nos permite crear un nuevo elemento publicando en **/addtask** método de nuestro controlador.
    
    Debería todo lo que se necesita para que funcione en nuestra aplicación.

5. Abra el archivo **style.css** en directorio de **public\stylesheets** y reemplace el código con lo siguiente:

        body {
          padding: 50px;
          font: 14px "Lucida Grande", Helvetica, Arial, sans-serif;
        }
        a {
          color: #00B7FF;
        }
        .well label {
          display: block;
        }
        .well input {
          margin-bottom: 5px;
        }
        .btn {
          margin-top: 5px;
          border: outset 1px #C8C8C8;
        }

    Guarde y cierre este archivo **style.css** .

## <a name="_Toc395783181"></a>Paso 6: Ejecute la aplicación localmente

1. Para probar la aplicación en el equipo local, ejecute `npm start` en un terminal para iniciar la aplicación y a continuación, inicie un explorador con una página que es similar a la imagen siguiente:

    ![Captura de pantalla de la aplicación de la lista de MyTodo en una ventana del explorador](./media/documentdb-nodejs-application/image18.png)


2. Utilice los campos proporcionados para el elemento, el nombre del elemento y la categoría para escribir información y, a continuación, haga clic en **Agregar elemento**.

3. Debe actualizar la página para mostrar el elemento recién creado en la lista de tareas pendientes.

    ![Captura de pantalla de la aplicación con un nuevo elemento en la lista de tareas pendientes](./media/documentdb-nodejs-application/image19.png)

4. Para completar una tarea, simplemente marque la casilla de verificación en la columna completa y, a continuación, haga clic en **Actualizar tareas**.

## <a name="_Toc395783182"></a>Paso 7: Implementar el proyecto de desarrollo de aplicaciones para sitios Web de Azure

1. Si todavía no lo ha hecho, habilitar un repositorio git para el sitio Web de Azure. Puede encontrar las instrucciones sobre cómo hacer esto en el tema de la [Implementación Local de Git al servicio de la aplicación de Azure](../app-service-web/app-service-deploy-local-git.md) .

2. Agregar el sitio Web de Azure como un git remoto.

        git remote add azure https://username@your-azure-website.scm.azurewebsites.net:443/your-azure-website.git

3. Implementar presionando en el equipo remoto.

        git push azure master

4. En unos segundos, git se terminar de publicar su aplicación web e inicie un explorador donde puede ver su trabajo práctico que se ejecuta en Azure!

## <a name="_Toc395637775"></a>Pasos siguientes

¡Felicidades! Solo ha creado su primera Node.js Express aplicación Web mediante DocumentDB de Azure y publicado a sitios Web de Azure.

El código fuente de la aplicación de referencia completa puede descargarse desde [GitHub][].

Para obtener más información, consulte el [Centro para desarrolladores de Node.js](https://azure.microsoft.com/develop/nodejs/).

[Node.js]: http://nodejs.org/
[GIT]: http://git-scm.com/
[Github]: https://github.com/Azure-Samples/documentdb-node-todo-app
 
