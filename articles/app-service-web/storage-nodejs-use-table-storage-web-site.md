<properties
    pageTitle="Node.js web app con el servicio de la tabla de Azure"
    description="Este tutorial le enseña a usar el servicio de Azure tabla para almacenar datos desde una aplicación de Node.js que se hospeda en Azure aplicación de servicio Web Apps."
    tags="azure-portal"
    services="app-service\web, storage"
    documentationCenter="nodejs"
    authors="rmcmurray"
    manager="wpickett"
    editor=""/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="nodejs"
    ms.topic="article"
    ms.date="08/11/2016"
    ms.author="robmcm"/>

# <a name="nodejs-web-app-using-the-azure-table-service"></a>Node.js web app con el servicio de la tabla de Azure

## <a name="overview"></a>Información general

En este tutorial se muestra cómo usar el servicio de la tabla proporcionada por la administración de datos de Azure para almacenar y tener acceso a datos desde una aplicación de [nodo] alojada en aplicaciones Web de [Servicio de aplicaciones de Azure](http://go.microsoft.com/fwlink/?LinkId=529714) . En este tutorial, se supone que tiene alguna experiencia anterior con nodo y [Git].

Aprenderá:

* Cómo usar npm (Administrador de nodos de paquete) para instalar los módulos de nodo

* Cómo trabajar con el servicio de la tabla de Azure

* Cómo utilizar la CLI Azure para crear una aplicación web.

Siguiendo este tutorial, creará un simple basada en web application "lista de tareas pendientes" que permite crear, recuperar y completar las tareas. Las tareas se almacenan en el servicio de la tabla.

Esto es la aplicación completada:

![Una página web que muestra una lista de tareas vacía][node-table-finished]

>[AZURE.NOTE] Si desea empezar a trabajar con el servicio de aplicación de Azure antes de suscribirse para una cuenta de Azure, vaya a [Probar el servicio de aplicaciones](http://go.microsoft.com/fwlink/?LinkId=523751), donde puede crear inmediatamente una aplicación web de starter corta duración en la aplicación de servicio. Sin tarjetas de crédito obligatoria; sin compromisos.

## <a name="prerequisites"></a>Requisitos previos

Antes de seguir las instrucciones de este artículo, asegúrese de que tiene instalado lo siguiente:

* [nodo] versión 0.10.24 o superior

* [GIT]

[AZURE.INCLUDE [create-account-and-websites-note](../../includes/create-account-and-websites-note.md)]

## <a name="create-a-storage-account"></a>Crear una cuenta de almacenamiento

Cree una cuenta de almacenamiento de Azure. La aplicación usará esta cuenta para almacenar los elementos de tareas pendientes.

1.  Inicie sesión en el [Portal de Azure](https://portal.azure.com/).

2. Haga clic en el icono de **nuevo** en la parte inferior izquierda del portal y luego haga clic en **datos + almacenamiento** > **almacenamiento**. Dé un nombre único de la cuenta de almacenamiento y cree un nuevo [grupo de recursos](../azure-resource-manager/resource-group-overview.md) .

    ![Botón nuevo](./media/storage-nodejs-use-table-storage-web-site/configure-storage.png)

    Cuando se ha creado la cuenta de almacenamiento, el botón **notificaciones** flash verde **éxito** y módulos de la cuenta de almacenamiento está abierto para mostrar que pertenece el nuevo grupo de recursos que ha creado.

5. Módulo de la cuenta de almacenamiento, haga clic en **configuración** > **teclas**. Copie la clave de acceso principal en el Portapapeles.

    ![Tecla de acceso][portal-storage-access-keys]


##<a name="install-modules-and-generate-scaffolding"></a>Instalar módulos y generar scaffolding

En esta sección se crea una nueva aplicación de nodo y use npm para agregar paquetes de módulo. Para esta aplicación usará los módulos [Express] y [Azure] . El módulo de Express proporciona un marco de controlador de vista de modelo para nodo, mientras que los módulos Azure proporciona conectividad con el servicio de la tabla.

### <a name="install-express-and-generate-scaffolding"></a>Instalación express y generar scaffolding

1. Desde la línea de comandos, cree un nuevo directorio denominado **lista de tareas** y cambiar a ese directorio.  

2. Escriba el comando siguiente para instalar el módulo de Express.

        npm install express-generator@4.2.0 -g

    Según el sistema operativo, debe colocar 'sudo' antes del comando:

        sudo npm install express-generator@4.2.0 -g

    El resultado es similar al siguiente ejemplo:

        express-generator@4.2.0 /usr/local/lib/node_modules/express-generator
        ├── mkdirp@0.3.5
        └── commander@1.3.2 (keypress@0.1.0)

    > [AZURE.NOTE] La '-g' parámetro instala el módulo globalmente. De este modo, podemos usar **express** para generar scaffolding de aplicación web sin tener que escribir información adicional de la ruta de acceso.

4. Para crear el scaffolding para la aplicación, escriba el comando **express** :

        express

    El resultado de este comando es similar al siguiente ejemplo:

           create : .
           create : ./package.json
           create : ./app.js
           create : ./public
           create : ./public/images
           create : ./routes
           create : ./routes/index.js
           create : ./routes/users.js
           create : ./public/stylesheets
           create : ./public/stylesheets/style.css
           create : ./views
           create : ./views/index.jade
           create : ./views/layout.jade
           create : ./views/error.jade
           create : ./public/javascripts
           create : ./bin
           create : ./bin/www

           install dependencies:
             $ cd . && npm install

           run the app:
             $ DEBUG=my-application ./bin/www

    Ahora tiene varios nuevos directorios y archivos en el directorio de la **lista de tareas** .

### <a name="install-additional-modules"></a>Instalar módulos adicionales

Uno de los archivos que **express** crea es **package.json**. Este archivo contiene una lista de dependencias de módulo. Más adelante, cuando se implementa la aplicación de la aplicación de servicio Web Apps, este archivo determina qué módulos deben instalarse en Azure.

Desde la línea de comandos, escriba el comando siguiente para instalar los módulos descritos en el archivo **package.json** . Debe usar 'sudo'.

    npm install

El resultado de este comando es similar al siguiente ejemplo:

    debug@0.7.4 node_modules\debug

    cookie-parser@1.0.1 node_modules\cookie-parser
    ├── cookie-signature@1.0.3
    └── cookie@0.1.0

    [...]


A continuación, escriba el comando siguiente para instalar los módulos de [azure], [nodo uuid], [nconf] y [asincrónico] :

    npm install azure-storage node-uuid async nconf --save

La **--Guardar** marca agrega entradas para estos módulos al archivo **package.json** .

El resultado de este comando es similar al siguiente ejemplo:

    async@0.9.0 node_modules\async

    node-uuid@1.4.1 node_modules\node-uuid

    nconf@0.6.9 node_modules\nconf
    ├── ini@1.2.1
    ├── async@0.2.9
    └── optimist@0.6.0 (wordwrap@0.0.2, minimist@0.0.10)

    [...]


## <a name="create-the-application"></a>Creación de la aplicación

Ahora, está listos generar la aplicación.

### <a name="create-a-model"></a>Crear un modelo

Un *modelo* es un objeto que representa los datos en la aplicación. Para la aplicación, el modelo solo es un objeto de tarea, que representa un elemento de la lista de tareas pendientes. Tareas tiene los siguientes campos:

- PartitionKey
- RowKey
- nombre (cadena)
- categoría (cadena)
- completadas (Boolean)

**PartitionKey** y **RowKey** se usan en el servicio de la tabla como claves de tabla. Para obtener más información, vea la [Descripción del modelo de datos de servicio de la tabla](https://msdn.microsoft.com/library/azure/dd179338.aspx).


1. En el directorio de la **lista de tareas** , cree un nuevo directorio denominado **modelos**.

2. En el directorio de **modelos** , cree un nuevo archivo denominado **task.js**. Este archivo contiene el modelo para las tareas creadas con la aplicación.

3. Al principio del archivo **task.js** , agregue el código siguiente para hacer referencia a las bibliotecas necesarias:

        var azure = require('azure-storage');
        var uuid = require('node-uuid');
        var entityGen = azure.TableUtilities.entityGenerator;

4. Agregue el código siguiente para definir y exportar el objeto de tarea. Este objeto es responsable de conectarse a la tabla.

        module.exports = Task;

        function Task(storageClient, tableName, partitionKey) {
          this.storageClient = storageClient;
          this.tableName = tableName;
          this.partitionKey = partitionKey;
          this.storageClient.createTableIfNotExists(tableName, function tableCreated(error) {
            if(error) {
              throw error;
            }
          });
        };

5. Agregue el código siguiente para definir métodos adicionales en el objeto de tarea, que permiten la interacción con los datos almacenados en la tabla:

        Task.prototype = {
          find: function(query, callback) {
            self = this;
            self.storageClient.queryEntities(this.tableName, query, null, function entitiesQueried(error, result) {
              if(error) {
                callback(error);
              } else {
                callback(null, result.entries);
              }
            });
          },

          addItem: function(item, callback) {
            self = this;
            // use entityGenerator to set types
            // NOTE: RowKey must be a string type, even though
            // it contains a GUID in this example.
            var itemDescriptor = {
              PartitionKey: entityGen.String(self.partitionKey),
              RowKey: entityGen.String(uuid()),
              name: entityGen.String(item.name),
              category: entityGen.String(item.category),
              completed: entityGen.Boolean(false)
            };
            self.storageClient.insertEntity(self.tableName, itemDescriptor, function entityInserted(error) {
              if(error){  
                callback(error);
              }
              callback(null);
            });
          },

          updateItem: function(rKey, callback) {
            self = this;
            self.storageClient.retrieveEntity(self.tableName, self.partitionKey, rKey, function entityQueried(error, entity) {
              if(error) {
                callback(error);
              }
              entity.completed._ = true;
              self.storageClient.updateEntity(self.tableName, entity, function entityUpdated(error) {
                if(error) {
                  callback(error);
                }
                callback(null);
              });
            });
          }
        }

6. Guarde y cierre el archivo **task.js** .

### <a name="create-a-controller"></a>Crear un controlador

Un *controlador* controla las solicitudes HTTP y representa la respuesta HTML.

1. En el **directorio/rutas de la lista de tareas** , cree un nuevo archivo denominado **tasklist.js** y ábralo en un editor de texto.

2. Agregue el código siguiente a **tasklist.js**. Esto carga los módulos azure y asincrónica, que se utilizan en **tasklist.js**. También se define la función de la **lista de tareas** , que se pasa una instancia del objeto de **tarea** definido anteriormente:

        var azure = require('azure-storage');
        var async = require('async');

        module.exports = TaskList;

3. Defina un objeto de la **lista de tareas** .

        function TaskList(task) {
          this.task = task;
        }


4. En la **lista de tareas**, agregue los métodos siguientes:

        TaskList.prototype = {
          showTasks: function(req, res) {
            self = this;
            var query = new azure.TableQuery()
              .where('completed eq ?', false);
            self.task.find(query, function itemsFound(error, items) {
              res.render('index',{title: 'My ToDo List ', tasks: items});
            });
          },

          addTask: function(req,res) {
            var self = this;
            var item = req.body.item;
            self.task.addItem(item, function itemAdded(error) {
              if(error) {
                throw error;
              }
              res.redirect('/');
            });
          },

          completeTask: function(req,res) {
            var self = this;
            var completedTasks = Object.keys(req.body);
            async.forEach(completedTasks, function taskIterator(completedTask, callback) {
              self.task.updateItem(completedTask, function itemsUpdated(error) {
                if(error){
                  callback(error);
                } else {
                  callback(null);
                }
              });
            }, function goHome(error){
              if(error) {
                throw error;
              } else {
               res.redirect('/');
              }
            });
          }
        }


### <a name="modify-appjs"></a>Modificar app.js

1. Desde el directorio de la **lista de tareas** , abra el archivo **app.js** . Este archivo se creó con una versión anterior, ejecute el comando **express** .

2. Al principio del archivo, agregue lo siguiente para cargar el módulo azure, establezca el nombre de tabla, la clave de partición y establecer las credenciales de almacenamiento que se utiliza en este ejemplo:

        var azure = require('azure-storage');
        var nconf = require('nconf');
        nconf.env()
             .file({ file: 'config.json', search: true });
        var tableName = nconf.get("TABLE_NAME");
        var partitionKey = nconf.get("PARTITION_KEY");
        var accountName = nconf.get("STORAGE_NAME");
        var accountKey = nconf.get("STORAGE_KEY");

    > [AZURE.NOTE] nconf carga los valores de configuración de variables de entorno o el archivo **config.json** , que creará más adelante.

3. En el archivo app.js, desplácese hacia abajo hasta que vea la siguiente línea:

        app.use('/', routes);
        app.use('/users', users);

    Reemplazar las líneas anteriores con el código que se muestra a continuación. Esto iniciar una instancia de <strong>tarea</strong> con una conexión a su cuenta de almacenamiento. Se pasa a la <strong>lista de tareas</strong>, que se utiliza para comunicarse con el servicio de la tabla:

        var TaskList = require('./routes/tasklist');
        var Task = require('./models/task');
        var task = new Task(azure.createTableService(accountName, accountKey), tableName, partitionKey);
        var taskList = new TaskList(task);

        app.get('/', taskList.showTasks.bind(taskList));
        app.post('/addtask', taskList.addTask.bind(taskList));
        app.post('/completetask', taskList.completeTask.bind(taskList));

4. Guarde el archivo **app.js** .

### <a name="modify-the-index-view"></a>Modificar la vista de índice

1. Abra el archivo **tasklist/views/index.jade** en un editor de texto.

2. Reemplazar todo el contenido del archivo con el código siguiente. Define una vista que muestra las tareas existentes e incluye un formulario para agregar nuevas tareas y marcar las existentes como completada.

        extends layout

        block content
          h1= title
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
                    td #{task.name._}
                    td #{task.category._}
                    - var day   = task.Timestamp._.getDate();
                    - var month = task.Timestamp._.getMonth() + 1;
                    - var year  = task.Timestamp._.getFullYear();
                    td #{month + "/" + day + "/" + year}
                    td
                      input(type="checkbox", name="#{task.RowKey._}", value="#{!task.completed._}", checked=task.completed._)
            button.btn(type="submit") Update tasks
          hr
          form.well(action="/addtask", method="post")
            label Item Name:
            input(name="item[name]", type="textbox")
            label Item Category:
            input(name="item[category]", type="textbox")
            br
            button.btn(type="submit") Add item

3. Guarde y cierre el archivo **index.jade** .

### <a name="modify-the-global-layout"></a>Modificar el diseño global

El archivo **layout.jade** en el directorio de **vistas** es una plantilla global y otros archivos de **.jade** . En este paso se modificarla para usar el [Inicio de Twitter](https://github.com/twbs/bootstrap), que es un Kit de herramientas que hace que sea fácil diseñar una aplicación web de aspecto interesante.

Descargar y extraer los archivos para un [Inicio de Twitter](http://getbootstrap.com/). Copie el archivo de **bootstrap.min.css** desde la carpeta Inicio **css** en el directorio **público y hojas de estilo** de la aplicación.

Desde la carpeta **vistas** , abra **layout.jade** y reemplace todo el contenido con lo siguiente:

    doctype html
    html
      head
        title= title
        link(rel='stylesheet', href='/stylesheets/bootstrap.min.css')
        link(rel='stylesheet', href='/stylesheets/style.css')
      body.app
        nav.navbar.navbar-default
          div.navbar-header
          a.navbar-brand(href='/') My Tasks
        block content

### <a name="create-a-config-file"></a>Crear un archivo de configuración

Para ejecutar la aplicación localmente, deberá incluimos credenciales de almacenamiento de Azure en un archivo de configuración. Crear un archivo llamado * *config.json* *con el siguiente JSON:

    {
        "STORAGE_NAME": "<storage account name>",
        "STORAGE_KEY": "<storage access key>",
        "PARTITION_KEY": "mytasks",
        "TABLE_NAME": "tasks"
    }

Reemplazar el **nombre de la cuenta de almacenamiento** con el nombre de la cuenta de almacenamiento que creó anteriormente y reemplace la **tecla de acceso de almacenamiento** con la tecla de acceso principal para su cuenta de almacenamiento. Por ejemplo:

    {
        "STORAGE_NAME": "nodejsappstorage",
        "STORAGE_KEY": "KG0oDd..."
        "PARTITION_KEY": "mytasks",
        "TABLE_NAME": "tasks"
    }

Guardar este archivo *en un directorio nivel superior* que el directorio de la **lista de tareas** , así:

    parent/
      |-- config.json
      |-- tasklist/

La razón para hacer esto es evitar la comprobación del archivo de configuración en el control de la fuente, donde podría ser público. Cuando se implementa la aplicación en Azure, usaremos variables de entorno en lugar de un archivo de configuración.


## <a name="run-the-application-locally"></a>Ejecute la aplicación localmente

Para probar la aplicación en el equipo local, siga estos pasos:

1. Desde la línea de comandos, cambie los directorios al directorio de la **lista de tareas** .

2. Use el comando siguiente para iniciar la aplicación localmente:

        npm start

3. Abra un navegador web y vaya a http://127.0.0.1:3000.

    Aparece una página web similar al siguiente ejemplo.

    ![Una página Web que muestra una lista de tareas vacía][node-table-finished]

4. Para crear un nuevo elemento de tarea, escriba un nombre y una categoría y haga clic en **Agregar elemento**. 

6. Para marcar una tarea como completada, active **completado** y haga clic en **Actualizar tareas**.

    ![Una imagen del nuevo elemento en la lista de tareas][node-table-list-items]

Aunque la aplicación se está ejecutando localmente, almacena los datos en el servicio de la tabla de Azure.

## <a name="deploy-your-application-to-azure"></a>Implementar la aplicación en Azure

Los pasos descritos en esta sección use las herramientas de línea de comandos de Azure para crear una nueva aplicación web en la aplicación de servicio y, a continuación, usar Git para implementar la aplicación. Para realizar estos pasos debe tener una suscripción de Azure.

> [AZURE.NOTE] Estos pasos también se realizan mediante el [Portal de Azure](https://portal.azure.com/). Vea [crear e implementar una aplicación web de Node.js en la aplicación de servicio de Azure].
>
> Si esta es la primera aplicación web que haya creado, debe usar el Portal de Azure para implementar esta aplicación.

Para empezar, instale [Azure CLI] escribiendo el siguiente comando de la línea de comandos:

    npm install azure-cli -g

### <a name="import-publishing-settings"></a>Importar la configuración de publicación

En este paso, se descarga un archivo que contiene información acerca de la suscripción.

1. Escriba el siguiente comando:

        azure account download

    Este comando inicia un explorador y se desplaza a la página de descarga. Si se le solicita, inicie sesión con la cuenta asociada a su suscripción de Azure.

    <!-- ![The download page][download-publishing-settings] -->

    La descarga del archivo comienza automáticamente; Si no es así, puede hacer clic en el vínculo al principio de la página para descargar manualmente el archivo. Guarde el archivo y anote la ruta de acceso de archivo.

2. Escriba el comando siguiente para importar la configuración:

        azure account import <path-to-file>

    Especificar la ruta de acceso y nombre de archivo del archivo de configuración de la publicación que ha descargado en el paso anterior.

3. Una vez haya importado la configuración, elimine el archivo de configuración de publicación. Ya no es necesario y contiene información confidencial en cuanto a su suscripción de Azure.

### <a name="create-an-app-service-web-app"></a>Crear una aplicación de servicio de aplicación web

1. Desde la línea de comandos, cambie los directorios al directorio de la **lista de tareas** .

2. Use el comando siguiente para crear una nueva aplicación web.

        azure site create --git

    Se le pedirá para la ubicación y el nombre de la aplicación web. Proporcione un nombre único y seleccione la misma ubicación geográfica como su cuenta de almacenamiento de Azure.

    La `--git` parámetro crea un repositorio Git en Azure para esta aplicación web. También inicializa un repositorio Git en el directorio actual si ninguna existe y agrega un [Git remoto] denominado 'azure', que se usa para publicar la aplicación en Azure. Por último, crea un archivo **web.config** , que contiene los valores que utiliza Azure para aplicaciones de nodo de host. Si omite el `--git` parámetro pero el directorio contiene un repositorio Git, el comando creará 'azure' remoto.

    Tras completa este comando, verá un resultado similar al siguiente. Observe que la línea que comienza con el **sitio Web creado en** contiene la dirección URL de la aplicación web.

        info:   Executing command site create
        help:   Need a site name
        Name: TableTasklist
        info:   Using location southcentraluswebspace
        info:   Executing `git init`
        info:   Creating default .gitignore file
        info:   Creating a new web site
        info:   Created web site at  tabletasklist.azurewebsites.net
        info:   Initializing repository
        info:   Repository initialized
        info:   Executing `git remote add azure https://username@tabletasklist.azurewebsites.net/TableTasklist.git`
        info:   site create command OK

    > [AZURE.NOTE] Si esta es la primera aplicación web de servicio de aplicación para la suscripción, se le indicará para usar el Portal de Azure para crear la aplicación web. Para obtener más información, vea [crear e implementar una aplicación web de Node.js en la aplicación de servicio de Azure].

### <a name="set-environment-variables"></a>Establecer las variables de entorno

En este paso, agregará variables de entorno a su configuración de aplicación web en Azure.
Desde la línea de comandos, escriba lo siguiente:

    azure site appsetting add
        STORAGE_NAME=<storage account name>;STORAGE_KEY=<storage access key>;PARTITION_KEY=mytasks;TABLE_NAME=tasks


Reemplazar **<storage account name>** con el nombre del almacenamiento de la cuenta que creó anteriormente y reemplace **<storage access key>** con la tecla de acceso principal para su cuenta de almacenamiento. (Use los mismos valores que el archivo config.json que creó anteriormente).

Como alternativa, puede establecer las variables de entorno en el [Portal de Azure](https://portal.azure.com/):

1.  Abra el módulo de la aplicación web haciendo clic en **Examinar** > **Aplicaciones Web** > el nombre de la aplicación web.

1.  Módulo de la aplicación web, haga clic en **Configuración de todos los** > **Configuración de la aplicación**.

    <!-- ![Top Menu](./media/storage-nodejs-use-table-storage-web-site/PollsCommonWebSiteTopMenu.png) -->

1.  Desplácese hacia abajo hasta la sección **configuración de la aplicación** y agregue los pares de clave y valor.

    ![Configuración de la aplicación](./media/storage-nodejs-use-table-storage-web-site/storage-tasks-appsettings.png)

1. Haga clic en **Guardar**.


### <a name="publish-the-application"></a>Publicar la aplicación

Para publicar la aplicación, confirme los archivos de código a Git y, a continuación, insertar en azure o patrón.

1. Establecer las credenciales de la implementación.

        azure site deployment user set <name> <password>

2. Agregar y confirme los archivos de la aplicación.

        git add .
        git commit -m "adding files"

3. Insertar la confirmación a la aplicación de servicio de la aplicación web:

        git push azure master

    Usar **maestra** como la bifurcación de destino. Al final de la implementación, verá una instrucción similar al siguiente ejemplo:

        To https://username@tabletasklist.azurewebsites.net/TableTasklist.git
         * [new branch]      master -> master

4. Una vez completada la operación de inserción, vaya a la dirección URL de la aplicación web previamente devuelta por la `azure create site` comando para ver la aplicación.


## <a name="next-steps"></a>Pasos siguientes

Aunque los pasos de este artículo describen mediante el servicio de la tabla para almacenar información, también puede usar [MongoDB](https://mlab.com/azure/). 

## <a name="additional-resources"></a>Recursos adicionales

[CLI de Azure]

## <a name="whats-changed"></a>¿Qué ha cambiado
* Encontrará una guía para el cambio de sitios Web a la aplicación de servicio: [servicio de aplicación de Azure y su influencia sobre los servicios de Azure existentes](http://go.microsoft.com/fwlink/?LinkId=529714)

<!-- URLs -->

[Compile e implemente una aplicación web de Node.js en la aplicación de servicio de Azure]: web-sites-nodejs-develop-deploy-mac.md
[Azure Developer Center]: /develop/nodejs/

[nodo]: http://nodejs.org
[GIT]: http://git-scm.com
[Express]: http://expressjs.com
[for free]: http://windowsazure.com
[GIT remoto]: http://git-scm.com/docs/git-remote

[CLI de Azure]: ../xplat-cli-install.md

[Azure]: https://github.com/Azure/azure-sdk-for-node
[nodo uuid]: https://www.npmjs.com/package/node-uuid
[nconf]: https://www.npmjs.com/package/nconf
[asincrónica]: https://www.npmjs.com/package/async

[Azure Portal]: https://portal.azure.com

[Create and deploy a Node.js application to an Azure Web Site]: web-sites-nodejs-develop-deploy-mac.md
 
<!-- Image References -->

[node-table-finished]: ./media/storage-nodejs-use-table-storage-web-site/table_todo_empty.png
[node-table-list-items]: ./media/storage-nodejs-use-table-storage-web-site/table_todo_list.png
[download-publishing-settings]: ./media/storage-nodejs-use-table-storage-web-site/azure-account-download-cli.png
[portal-new]: ./media/storage-nodejs-use-table-storage-web-site/plus-new.png
[portal-storage-account]: ./media/storage-nodejs-use-table-storage-web-site/new-storage.png
[portal-quick-create-storage]: ./media/storage-nodejs-use-table-storage-web-site/quick-storage.png
[portal-storage-access-keys]: ./media/storage-nodejs-use-table-storage-web-site/manage-access-keys.png
[go-to-dashboard]: ./media/storage-nodejs-use-table-storage-web-site/go_to_dashboard.png
[web-configure]: ./media/storage-nodejs-use-table-storage-web-site/sql-task-configure.png
[app-settings-save]: ./media/storage-nodejs-use-table-storage-web-site/savebutton.png
[app-settings]: ./media/storage-nodejs-use-table-storage-web-site/storage-tasks-appsettings.png
