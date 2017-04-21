<properties 
    pageTitle="Aplicación Web con el almacenamiento de tablas (Node.js) | Microsoft Azure" 
    description="Tutorial que se basa en la aplicación Web con el tutorial de Express agregando el módulo Azure y servicios de almacenamiento de Azure." 
    services="cloud-services, storage" 
    documentationCenter="nodejs" 
    authors="tamram" 
    manager="carmonm" 
    editor="tysonn"/>

<tags 
    ms.service="storage" 
    ms.workload="storage" 
    ms.tgt_pltfrm="na" 
    ms.devlang="nodejs" 
    ms.topic="article" 
    ms.date="10/18/2016" 
    ms.author="robmcm"/>

# <a name="nodejs-web-application-using-storage"></a>Aplicación Web de Node.js mediante almacenamiento

## <a name="overview"></a>Información general

En este tutorial, para ampliar la aplicación creada en el tutorial de la [Aplicación Web de Node.js con Express] mediante las bibliotecas de cliente de Microsoft Azure para Node.js para trabajar con servicios de administración de datos. Para ampliar la aplicación para crear una aplicación de la lista de tareas basada en web que puede implementar en Azure. La lista de tareas permite a un usuario recuperar las tareas y agregar nuevas tareas para marcar una tarea como completada.

Los elementos de tareas se almacenan en el almacenamiento de Azure. Almacenamiento de Azure proporciona almacenamiento de datos no estructurados tolerancia y altamente disponibles. Almacenamiento de Azure incluye varias estructuras de datos donde puede almacenar y tener acceso a datos y puede aprovechar los servicios de almacenamiento de las API incluidos en el SDK de Azure para Node.js o a través de las API de REST. Para obtener más información, vea [almacenar y tener acceso a datos en Azure].

En este tutorial se supone que se han realizado los tutoriales de[uso de la aplicación Web de Node.js Express] [Aplicación Web de Node.js] y [Node.js con Express].

Aprenderá:

-   Cómo trabajar con el motor de plantillas Jade
-   Cómo trabajar con los servicios de administración de datos de Azure

Captura de pantalla de la aplicación completada está por debajo de:

![La página web completa en internet explorer](./media/storage-nodejs-use-table-storage-cloud-service-app/getting-started-1.png)

## <a name="setting-storage-credentials-in-webconfig"></a>Configurar las credenciales de almacenamiento en Web.Config

Para tener acceso al almacenamiento de Azure, debe pasar las credenciales de almacenamiento. Para hacerlo, utilice configuración de la aplicación de web.config.
Esta configuración se pasará como variables de entorno al nodo, que se lee el SDK de Azure.

> [AZURE.NOTE] Credenciales de almacenamiento sólo se utilizan cuando la aplicación se implementa en Azure. Cuando se ejecuta en el emulador, la aplicación usará el emulador de almacenamiento.

Realice los pasos siguientes para recuperar las credenciales de cuenta de almacenamiento y agregarlos a la configuración de web.config:

1.  Si no está ya abrir, iniciar Azure PowerShell desde el menú **Inicio** , expanda **todos los programas, Azure**, contextual **Azure PowerShell**y, a continuación, seleccione **Ejecutar como administrador**.

2.  Cambie a la carpeta que contiene la aplicación. Por ejemplo, C:\\nodo\\lista de tareas\\WebRole1.

3.  Desde la ventana de Powershell de Azure, escriba el siguiente cmdlet para recuperar la información de cuenta de almacenamiento:

        PS C:\node\tasklist\WebRole1> Get-AzureStorageAccounts

    Recupera la lista de cuentas de almacenamiento y cuenta teclas asociada con el servicio hospedado.

    > [AZURE.NOTE] Como el SDK de Azure crea una cuenta de almacenamiento al implementar un servicio, ya debe existir una cuenta de almacenamiento de implementación de la aplicación en las guías anterior.

4.  Abra el archivo **ServiceDefinition.csdef** que contiene la configuración de entorno que se usa cuando se implementa la aplicación en Azure:

        PS C:\node\tasklist> notepad ServiceDefinition.csdef

5.  Inserte el siguiente bloque en elemento del **entorno** , sustituir {cuenta de almacenamiento} y {tecla de acceso de almacenamiento} con el nombre de cuenta y la clave principal de la cuenta de almacenamiento que desee usar para su implementación:

        <Variable name="AZURE_STORAGE_ACCOUNT" value="{STORAGE ACCOUNT}" />
        <Variable name="AZURE_STORAGE_ACCESS_KEY" value="{STORAGE ACCESS KEY}" />

    ![El contenido del archivo web.cloud.config](./media/storage-nodejs-use-table-storage-cloud-service-app/node37.png)

6.  Guarde el archivo y cierre el Bloc de notas.

### <a name="install-additional-modules"></a>Instalar módulos adicionales

2. Use el comando siguiente para instalar [azure], [uuid nodo], [nconf] y [asincrónico] módulos localmente, así como para guardar una entrada de ellos en el archivo **package.json** :

        PS C:\node\tasklist\WebRole1> npm install azure-storage node-uuid async nconf --save

    El resultado de este comando debería ser similar al siguiente:

        node-uuid@1.4.1 node_modules\node-uuid

        nconf@0.6.9 node_modules\nconf
        ├── ini@1.1.0
        ├── async@0.2.9
        └── optimist@0.6.0 (wordwrap@0.0.2, minimist@0.0.8)

        azure-storage@0.1.0 node_modules\azure-storage
        ├── extend@1.2.1
        ├── xmlbuilder@0.4.3
        ├── mime@1.2.11
        ├── underscore@1.4.4
        ├── validator@3.1.0
        ├── node-uuid@1.4.1
        ├── xml2js@0.2.7 (sax@0.5.2)
        └── request@2.27.0 (json-stringify-safe@5.0.0, tunnel-agent@0.3.0, aws-sign@0.3.0, forever-agent@0.5.2, qs@0.6.6, oauth-sign@0.3.0, cookie-jar@0.3.0, hawk@1.0.0, form-data@0.1.3, http-signature@0.10.0)

##<a name="using-the-table-service-in-a-node-application"></a>Utilizar el servicio de la tabla en una aplicación de nodo

En esta sección para ampliar la aplicación básica creada por el comando **express** agregando un archivo **task.js** que contiene el modelo de tareas. También se modifique el existente **app.js** y crear un nuevo archivo **tasklist.js** que utiliza el modelo.

### <a name="create-the-model"></a>Crear el modelo

1. En el directorio **WebRole1** , cree un nuevo directorio denominado **modelos**.

2. En el directorio de **modelos** , cree un nuevo archivo denominado **task.js**. Este archivo contiene el modelo para las tareas creadas con la aplicación.

3. Al principio del archivo **task.js** , agregue el código siguiente para hacer referencia a las bibliotecas necesarias:

        var azure = require('azure-storage');
        var uuid = require('node-uuid');
        var entityGen = azure.TableUtilities.entityGenerator;

4. A continuación, agregará código para definir y exportar el objeto de tarea. Este objeto es responsable de conectarse a la tabla.

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

5. A continuación, agregue el código siguiente para definir métodos adicionales en el objeto de tarea, que permiten la interacción con los datos almacenados en la tabla:

        Task.prototype = {
          find: function(query, callback) {
            self = this;
            self.storageClient.queryEntities(query, function entitiesQueried(error, result) {
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

### <a name="create-the-controller"></a>Crear el controlador

1. En el directorio **WebRole1/rutas** , cree un nuevo archivo denominado **tasklist.js** y ábralo en un editor de texto.

2. Agregue el código siguiente a **tasklist.js**. Esto carga los módulos azure y asincrónica, que se utilizan en **tasklist.js**. También se define la función de la **lista de tareas** , que se pasa una instancia del objeto de **tarea** definido anteriormente:

        var azure = require('azure-storage');
        var async = require('async');

        module.exports = TaskList;

        function TaskList(task) {
          this.task = task;
        }

2. Continúe agregando el archivo **tasklist.js** mediante la adición de los métodos utilizados para **showTasks**, **addTask**y **completeTasks**:

        TaskList.prototype = {
          showTasks: function(req, res) {
            self = this;
            var query = azure.TableQuery()
              .where('completed eq ?', false);
            self.task.find(query, function itemsFound(error, items) {
              res.render('index',{title: 'My ToDo List ', tasks: items});
            });
          },

          addTask: function(req,res) {
            var self = this      
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

3. Guarde el archivo **tasklist.js** .

### <a name="modify-appjs"></a>Modificar app.js

1. En el directorio **WebRole1** , abra el archivo **app.js** en un editor de texto. 

2. Al principio del archivo, agregue lo siguiente para cargar el módulo azure y definir la clave de nombre y partición de tabla:

        var azure = require('azure-storage');
        var tableName = 'tasks';
        var partitionKey = 'hometasks';

3. En el archivo app.js, desplácese hacia abajo hasta que vea la siguiente línea:

        app.use('/', routes);
        app.use('/users', users);

    Reemplazar las líneas anteriores con el código que se muestra a continuación. Esto iniciar una instancia de <strong>tarea</strong> con una conexión a su cuenta de almacenamiento. Se pasa a la <strong>lista de tareas</strong>, que se utiliza para comunicarse con el servicio de la tabla:

        var TaskList = require('./routes/tasklist');
        var Task = require('./models/task');
        var task = new Task(azure.createTableService(), tableName, partitionKey);
        var taskList = new TaskList(task);

        app.get('/', taskList.showTasks.bind(taskList));
        app.post('/addtask', taskList.addTask.bind(taskList));
        app.post('/completetask', taskList.completeTask.bind(taskList));
    
4. Guarde el archivo **app.js** .

### <a name="modify-the-index-view"></a>Modificar la vista de índice

1. Cambie los directorios al directorio de **vistas** y abra el archivo **index.jade** en un editor de texto.

2. Reemplace el contenido del archivo **index.jade** con el código siguiente. Define la vista para mostrar las tareas existentes, así como un formulario para agregar nuevas tareas y marcar las existentes como completada.

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
              if tasks != []
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

El archivo **layout.jade** en el directorio de **vistas** se usa como una plantilla global para otros archivos de **.jade** . En este paso se modificarla para usar el [Inicio de Twitter](https://github.com/twbs/bootstrap), que es un Kit de herramientas que facilita el diseño de un sitio Web de aspecto interesante.

1. Descargar y extraer los archivos para un [Inicio de Twitter](http://getbootstrap.com/). Copie el archivo **bootstrap.min.css** desde el **arranque\\distribuciones\\css** carpeta a la **público\\hojas de estilo** directorio de la aplicación de la lista de tareas.

2. Desde la carpeta **vistas** , abra la **layout.jade** en el editor de texto y reemplace el contenido con lo siguiente:

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

3. Guarde el archivo **layout.jade** .

### <a name="running-the-application-in-the-emulator"></a>Ejecute la aplicación en el emulador

Use el comando siguiente para iniciar la aplicación en el emulador.

    PS C:\node\tasklist\WebRole1> start-azureemulator -launch

El explorador se abrirá y muestra la página siguiente:

![Un sitio web paginado titulado mi lista de tareas con una tabla que contiene las tareas y los campos para agregar una nueva tarea.](./media/storage-nodejs-use-table-storage-cloud-service-app/node44.png)

Use el formulario para agregar elementos o quitar elementos existentes marcándolos como completada.

## <a name="publishing-the-application-to-azure"></a>Publicar la aplicación en Azure


En la ventana de Windows PowerShell, llame el siguiente cmdlet para implementar el servicio hospedado en Azure.

    PS C:\node\tasklist\WebRole1> Publish-AzureServiceProject -name myuniquename -location datacentername -launch

Reemplace **myuniquename** con un nombre único para esta aplicación. Reemplace **datacentername** con el nombre de un centro de datos de Azure, como **US oeste**.

Una vez completada la implementación, debería ver una respuesta similar a la siguiente:

    PS C:\node\tasklist> publish-azureserviceproject -servicename tasklist -location "West US"
    WARNING: Publishing tasklist to Microsoft Azure. This may take several minutes...
    WARNING: 2:18:42 PM - Preparing runtime deployment for service 'tasklist'
    WARNING: 2:18:42 PM - Verifying storage account 'tasklist'...
    WARNING: 2:18:43 PM - Preparing deployment for tasklist with Subscription ID: 65a1016d-0f67-45d2-b838-b8f373d6d52e...
    WARNING: 2:19:01 PM - Connecting...
    WARNING: 2:19:02 PM - Uploading Package to storage service larrystore...
    WARNING: 2:19:40 PM - Upgrading...
    WARNING: 2:22:48 PM - Created Deployment ID: b7134ab29b1249ff84ada2bd157f296a.
    WARNING: 2:22:48 PM - Initializing...
    WARNING: 2:22:49 PM - Instance WebRole1_IN_0 of role WebRole1 is ready.
    WARNING: 2:22:50 PM - Created Website URL: http://tasklist.cloudapp.net/.

Como antes, porque especificó el **-Inicio** opción, el explorador se abre y se muestra la aplicación que se ejecuta en Azure al finalizar la publicación.

![Una ventana del explorador que muestra la página de mi lista de tareas. La dirección URL indica que la página ahora se hospeda en Azure.](./media/storage-nodejs-use-table-storage-cloud-service-app/getting-started-1.png)

## <a name="stopping-and-deleting-your-application"></a>Detener y eliminar la aplicación

Después de implementar la aplicación, desea deshabilitarlo por lo que puede evitar costos o compilar e implementar otras aplicaciones en el período de prueba gratuita.

Facturas Azure web instancias de la función de tiempo de servidor consumido por hora.
Hora del servidor se consume una vez que se implementa la aplicación, incluso si las instancias no se están ejecutando y están en el estado de detención.

Los pasos siguientes muestran cómo detener y eliminar la aplicación.

1.  En la ventana de Windows PowerShell, detenga la implementación de servicio que creó en la sección anterior con el siguiente cmdlet:

        PS C:\node\tasklist\WebRole1> Stop-AzureService

    Detener el servicio puede tardar varios minutos. Cuando se detiene el servicio, recibirá un mensaje que indica que se ha detenido.

3.  Para eliminar el servicio, llame el siguiente cmdlet:

        PS C:\node\tasklist\WebRole1> Remove-AzureService contosotasklist

    Cuando se le solicite, escriba **Y** para eliminar el servicio.

    Eliminar el servicio puede tardar varios minutos. Después de que el servicio se ha eliminado, recibirá un mensaje que indica que el servicio se ha eliminado.

  [Aplicación Web de Node.js con Express]: http://azure.microsoft.com/develop/nodejs/tutorials/web-app-with-express/
  [Almacenamiento y acceso a datos de Azure]: http://msdn.microsoft.com/library/azure/gg433040.aspx
  [Aplicación Web de Node.js]: http://azure.microsoft.com/develop/nodejs/tutorials/getting-started/
 
 
