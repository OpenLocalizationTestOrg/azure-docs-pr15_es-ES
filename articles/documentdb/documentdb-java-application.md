<properties
    pageTitle="Tutorial de desarrollo de aplicaciones de Java con DocumentDB | Microsoft Azure"
    description="Este tutorial de aplicación web de Java muestra cómo usar el servicio de DocumentDB de Azure para almacenar y datos de access desde una aplicación de Java hospedado en sitios Web de Azure."
    keywords="Desarrollo de aplicaciones, tutorial de base de datos, aplicación java, tutorial de aplicación web de java, documentdb, azure, Microsoft azure"
    services="documentdb"
    documentationCenter="java"
    authors="dennyglee"
    manager="jhubbard"
    editor="mimig"/>

<tags
    ms.service="documentdb"
    ms.devlang="java"
    ms.topic="hero-article"
    ms.tgt_pltfrm="NA"
    ms.workload="data-services"
    ms.date="08/24/2016"
    ms.author="denlee"/>

# <a name="build-a-java-web-application-using-documentdb"></a>Crear una aplicación web de Java con DocumentDB

> [AZURE.SELECTOR]
- [.NET](documentdb-dotnet-application.md)
- [Node.js](documentdb-nodejs-application.md)
- [Java](documentdb-java-application.md)
- [Python](documentdb-python-application.md)

Este tutorial de aplicación web de Java muestra cómo usar el servicio de [Microsoft Azure DocumentDB](https://portal.azure.com/#gallery/Microsoft.DocumentDB) para almacenar y datos de access desde una aplicación de Java hospedado en sitios Web de Azure. En este tema, aprenderá:

- Cómo crear una aplicación de JSP básica en Eclipse.
- Cómo trabajar con el servicio de Azure DocumentDB mediante el [SDK de Java DocumentDB](https://github.com/Azure/azure-documentdb-java).

Este tutorial de aplicación de Java muestra cómo crear una aplicación de administración de tareas basada en web que le permite crear, recuperar y marcar las tareas como completado, tal como se muestra en la siguiente imagen. Cada una de las tareas en la lista de tareas pendientes se almacenan como documentos JSON en DocumentDB de Azure.

![Mi aplicación Java de lista ToDo](./media/documentdb-java-application/image1.png)

> [AZURE.TIP] Este tutorial de desarrollo de la aplicación, se supone que tiene experiencia previa con Java. Si está familiarizado con Java o las [Herramientas como requisito previos](#Prerequisites), se recomienda descargando el proyecto completo [todo](https://github.com/Azure-Samples/documentdb-java-todo-app) de GitHub y la creación de siguiendo [las instrucciones al final de este artículo](#GetProject). Una vez que ha creado, puede revisar el artículo para obtener información sobre el código en el contexto del proyecto.  

##<a id="Prerequisites"></a>Requisitos previos para este tutorial de aplicación web de Java
Antes de comenzar este tutorial de desarrollo de aplicaciones, debe tener el siguiente:

- Una cuenta de Azure active. Si no tiene una cuenta, puede crear una cuenta de prueba gratuita de dos minutos. Para obtener información detallada, vea [Prueba gratuita de Azure](https://azure.microsoft.com/pricing/free-trial/).
- [Kit de desarrollo de Java (JDK) + 7](http://www.oracle.com/technetwork/java/javase/downloads/index.html).
- [Eclipse IDE para desarrolladores de Java EE.](http://www.eclipse.org/downloads/packages/eclipse-ide-java-ee-developers/lunasr1)
- [Un sitio Web de Azure con un entorno de tiempo de ejecución Java (por ejemplo, Tomcat o embarcadero) habilitado.](../app-service-web/web-sites-java-get-started.md)

Si va a instalar estas herramientas por primera vez, coreservlets.com proporciona un ejemplo paso a paso el proceso de instalación en la sección de inicio rápido de sus [Tutorial: instalar TomCat7 y su uso con Eclipse](http://www.coreservlets.com/Apache-Tomcat-Tutorial/tomcat-7-with-eclipse.html) artículo.

##<a id="CreateDB"></a>Paso 1: Crear una cuenta de base de datos DocumentDB

Empecemos creando una cuenta de DocumentDB. Si ya tiene una cuenta, vaya a [paso 2: crear la aplicación de Java JSP](#CreateJSP).

[AZURE.INCLUDE [documentdb-create-dbaccount](../../includes/documentdb-create-dbaccount.md)]

[AZURE.INCLUDE [documentdb-keys](../../includes/documentdb-keys.md)]

##<a id="CreateJSP"></a>Paso 2: Crear la aplicación de Java JSP

Para crear la aplicación JSP:

1. En primer lugar, comenzaremos mediante la creación de un proyecto de Java. Iniciar Eclipse, a continuación, haga clic en **archivo**, haga clic en **nuevo**y, a continuación, haga clic en **Proyecto de Web dinámica**. Si no ve el **Proyecto de Web dinámica** enumerados como proyecto disponible, haga lo siguiente: haga clic en **archivo**, haga clic en **nuevo**, haga clic en **proyecto**..., expanda **Web**, haga clic en **Proyecto de Web dinámica**y haga clic en **siguiente**.

    ![Desarrollo de aplicaciones de Java JSP](./media/documentdb-java-application/image10.png)

2. Escriba un nombre de proyecto en el cuadro **nombre del proyecto** y en el menú desplegable de **Tiempo de ejecución de destino** , opcionalmente, seleccione un valor (por ejemplo, Apache Tomcat v7.0) y, a continuación, haga clic en **Finalizar**. Seleccionar un tiempo de ejecución de destino le permite ejecutar el proyecto localmente a través de Eclipse.
3. En Eclipse, en la vista del explorador de proyectos, expanda el proyecto. Haga clic con el botón **ContenidoWeb**, haga clic en **nuevo**y, a continuación, haga clic en **Archivo JSP**.
4. En el cuadro de diálogo **Nuevo archivo JSP** , asigne un nombre del archivo **index.jsp**. Mantener la carpeta principal como **ContenidoWeb**, tal como se muestra en la siguiente ilustración y, a continuación, haga clic en **siguiente**.

    ![Hacer que un nuevo archivo JSP - aplicación Web de Java Tutorial](./media/documentdb-java-application/image11.png)

5. En el cuadro de diálogo **Seleccionar plantilla de JSP** , con el fin de este tutorial, seleccione **Nuevo archivo JSP (html)**y, a continuación, haga clic en **Finalizar**.

6. Cuando se abre el archivo index.jsp en Eclipse, agregar texto para mostrar **Hola a todos!** dentro de la existente <body> elemento. La actualización <body> contenido debe tener un aspecto similar al siguiente código:

        <body>
            <% out.println("Hello World!"); %>
        </body>

8. Guarde el archivo index.jsp.
9. Si establece un tiempo de ejecución de destino en el paso 2, puede hacer clic en **proyecto** y, a continuación, en **Ejecutar** para ejecutar la aplicación JSP localmente:

    ![Hola a todos: Tutorial de aplicación de Java](./media/documentdb-java-application/image12.png)

##<a id="InstallSDK"></a>Paso 3: Instalar el SDK de DocumentDB Java ##

La manera más sencilla de extraer en el SDK de Java DocumentDB y sus dependencias es a través de [Experto en Apache](http://maven.apache.org/).

Para ello, debe convertir el proyecto en un proyecto de experto complete los pasos siguientes:

1. Haga clic en el proyecto en el Explorador de proyectos, haga clic en **Configurar**, haga clic en **convertir a experto en Project**.
2. En la ventana **Crear nuevo POM** , acepte los valores predeterminados y haga clic en **Finalizar**.
3. En el **Explorador de proyectos**, abra el archivo pom.xml.
4. En la ficha **dependencias** , en el panel **dependencias** , haga clic en **Agregar**.
4. En la ventana de **Dependencia seleccione** , haga lo siguiente:
 - En el cuadro **ID** , escriba com.microsoft.azure.
 - En el cuadro **Muestra Id** escriba documentdb de azure.
 - En el cuadro **versión** escriba 1.5.1.

    ![Instalar la aplicación de DocumentDB Java SDK](./media/documentdb-java-application/image13.png)

    O agregue la dependencia XML para ID y ArtifactId directamente a la pom.xml a través de un editor de texto:

        <dependency>
            <groupId>com.microsoft.azure</groupId>
            <artifactId>azure-documentdb</artifactId>
            <version>1.5.1</version>
        </dependency>

5. Haga clic en **Aceptar** y experto instalará el SDK de Java DocumentDB.
6. Guarde el archivo pom.xml.

##<a id="UseService"></a>Paso 4: Utilizar el servicio de DocumentDB en una aplicación de Java

1. En primer lugar, vamos a definir el objeto TodoItem:

        @Data
        @Builder
        public class TodoItem {
            private String category;
            private boolean complete;
            private String id;
            private String name;
        }

    En este proyecto, usamos [Lombok de proyecto](http://projectlombok.org/) para generar el constructor, captadores, establecedores y un generador. Como alternativa, puede escribir este código manualmente o tiene el IDE genere.

2. Para llamar al servicio de DocumentDB, debe crear una instancia de un nuevo **DocumentClient**. En general, es mejor volver a utilizar el **DocumentClient** - en lugar de crear a un nuevo cliente para cada solicitud posterior. El cliente nos podemos reutilizar ajustando el cliente en un **DocumentClientFactory**. Esta es también donde se necesita pegar el valor de clave principal y de URI que ha guardado en el Portapapeles en el [paso 1](#CreateDB). Reemplazar [su\_extremo\_aquí] con el URI y reemplazar [su\_clave\_aquí] con la clave principal.

        private static final String HOST = "[YOUR_ENDPOINT_HERE]";
        private static final String MASTER_KEY = "[YOUR_KEY_HERE]";

        private static DocumentClient documentClient;

        public static DocumentClient getDocumentClient() {
            if (documentClient == null) {
                documentClient = new DocumentClient(HOST, MASTER_KEY,
                        ConnectionPolicy.GetDefault(), ConsistencyLevel.Session);
            }

            return documentClient;
        }

3. Ahora vamos a crear un objeto de acceso a datos (DAO) para resumir conservar nuestros elementos ToDo para DocumentDB.

    Para guardar elementos ToDo en una colección, necesita saber qué base de datos y colección conservar para el cliente (al que hace referencia propia vínculos). En general, es mejor en la memoria caché de la base de datos y colección cuando sea posible para evitar adicionales de ida y vuelta a la base de datos.

    El código siguiente muestra cómo recuperar nuestra base de datos y la colección, si existe, o cree uno nuevo si no existe:

        public class DocDbDao implements TodoDao {
            // The name of our database.
            private static final String DATABASE_ID = "TodoDB";

            // The name of our collection.
            private static final String COLLECTION_ID = "TodoCollection";

            // The DocumentDB Client
            private static DocumentClient documentClient = DocumentClientFactory
                    .getDocumentClient();

            // Cache for the database object, so we don't have to query for it to
            // retrieve self links.
            private static Database databaseCache;

            // Cache for the collection object, so we don't have to query for it to
            // retrieve self links.
            private static DocumentCollection collectionCache;

            private Database getTodoDatabase() {
                if (databaseCache == null) {
                    // Get the database if it exists
                    List<Database> databaseList = documentClient
                            .queryDatabases(
                                    "SELECT * FROM root r WHERE r.id='" + DATABASE_ID
                                            + "'", null).getQueryIterable().toList();

                    if (databaseList.size() > 0) {
                        // Cache the database object so we won't have to query for it
                        // later to retrieve the selfLink.
                        databaseCache = databaseList.get(0);
                    } else {
                        // Create the database if it doesn't exist.
                        try {
                            Database databaseDefinition = new Database();
                            databaseDefinition.setId(DATABASE_ID);

                            databaseCache = documentClient.createDatabase(
                                    databaseDefinition, null).getResource();
                        } catch (DocumentClientException e) {
                            // TODO: Something has gone terribly wrong - the app wasn't
                            // able to query or create the collection.
                            // Verify your connection, endpoint, and key.
                            e.printStackTrace();
                        }
                    }
                }

                return databaseCache;
            }

            private DocumentCollection getTodoCollection() {
                if (collectionCache == null) {
                    // Get the collection if it exists.
                    List<DocumentCollection> collectionList = documentClient
                            .queryCollections(
                                    getTodoDatabase().getSelfLink(),
                                    "SELECT * FROM root r WHERE r.id='" + COLLECTION_ID
                                            + "'", null).getQueryIterable().toList();

                    if (collectionList.size() > 0) {
                        // Cache the collection object so we won't have to query for it
                        // later to retrieve the selfLink.
                        collectionCache = collectionList.get(0);
                    } else {
                        // Create the collection if it doesn't exist.
                        try {
                            DocumentCollection collectionDefinition = new DocumentCollection();
                            collectionDefinition.setId(COLLECTION_ID);

                            collectionCache = documentClient.createCollection(
                                    getTodoDatabase().getSelfLink(),
                                    collectionDefinition, null).getResource();
                        } catch (DocumentClientException e) {
                            // TODO: Something has gone terribly wrong - the app wasn't
                            // able to query or create the collection.
                            // Verify your connection, endpoint, and key.
                            e.printStackTrace();
                        }
                    }
                }

                return collectionCache;
            }
        }

4. El siguiente paso es escribir código para conservar la TodoItems en la colección. En este ejemplo, se utilizará [Gson](https://code.google.com/p/google-gson/) para serializar y deserializar TodoItem sencilla antiguo objetos Java (POJOs) a los documentos JSON. [Jackson](http://jackson.codehaus.org/) o su propio serializador personalizado también son excelentes alternativas para serializar POJOs.

        // We'll use Gson for POJO <=> JSON serialization for this example.
        private static Gson gson = new Gson();

        @Override
        public TodoItem createTodoItem(TodoItem todoItem) {
            // Serialize the TodoItem as a JSON Document.
            Document todoItemDocument = new Document(gson.toJson(todoItem));

            // Annotate the document as a TodoItem for retrieval (so that we can
            // store multiple entity types in the collection).
            todoItemDocument.set("entityType", "todoItem");

            try {
                // Persist the document using the DocumentClient.
                todoItemDocument = documentClient.createDocument(
                        getTodoCollection().getSelfLink(), todoItemDocument, null,
                        false).getResource();
            } catch (DocumentClientException e) {
                e.printStackTrace();
                return null;
            }

            return gson.fromJson(todoItemDocument.toString(), TodoItem.class);
        }



5. Como DocumentDB bases de datos y las colecciones, documentos también se hace referencia a propia vínculos. La siguiente función auxiliar nos permite recuperar documentos por otro atributo (por ejemplo, "id") en lugar de vincular automáticamente:

        private Document getDocumentById(String id) {
            // Retrieve the document using the DocumentClient.
            List<Document> documentList = documentClient
                    .queryDocuments(getTodoCollection().getSelfLink(),
                            "SELECT * FROM root r WHERE r.id='" + id + "'", null)
                    .getQueryIterable().toList();

            if (documentList.size() > 0) {
                return documentList.get(0);
            } else {
                return null;
            }
        }

6. Podemos utilizar el método auxiliar en el paso 5 para recuperar un documento TodoItem JSON por id y, a continuación, deserializarla a un POJO:

        @Override
        public TodoItem readTodoItem(String id) {
            // Retrieve the document by id using our helper method.
            Document todoItemDocument = getDocumentById(id);

            if (todoItemDocument != null) {
                // De-serialize the document in to a TodoItem.
                return gson.fromJson(todoItemDocument.toString(), TodoItem.class);
            } else {
                return null;
            }
        }

7. También podemos utilizar la DocumentClient para obtener una lista de TodoItems con DocumentDB SQL o colección:

        @Override
        public List<TodoItem> readTodoItems() {
            List<TodoItem> todoItems = new ArrayList<TodoItem>();

            // Retrieve the TodoItem documents
            List<Document> documentList = documentClient
                    .queryDocuments(getTodoCollection().getSelfLink(),
                            "SELECT * FROM root r WHERE r.entityType = 'todoItem'",
                            null).getQueryIterable().toList();

            // De-serialize the documents in to TodoItems.
            for (Document todoItemDocument : documentList) {
                todoItems.add(gson.fromJson(todoItemDocument.toString(),
                        TodoItem.class));
            }

            return todoItems;
        }

8. Hay muchas formas para actualizar un documento con la DocumentClient. En nuestra aplicación de lista Todo, queremos alternar si un TodoItem ha finalizado. Para ello puede actualizar el atributo "completado" dentro del documento:

        @Override
        public TodoItem updateTodoItem(String id, boolean isComplete) {
            // Retrieve the document from the database
            Document todoItemDocument = getDocumentById(id);

            // You can update the document as a JSON document directly.
            // For more complex operations - you could de-serialize the document in
            // to a POJO, update the POJO, and then re-serialize the POJO back in to
            // a document.
            todoItemDocument.set("complete", isComplete);

            try {
                // Persist/replace the updated document.
                todoItemDocument = documentClient.replaceDocument(todoItemDocument,
                        null).getResource();
            } catch (DocumentClientException e) {
                e.printStackTrace();
                return null;
            }

            return gson.fromJson(todoItemDocument.toString(), TodoItem.class);
        }

9. Por último, queremos la capacidad de eliminar un TodoItem de nuestra lista. Para ello, podemos utilizar el método auxiliar que escribimos anteriormente para recuperar vincular automáticamente y, a continuación, indicar al cliente para eliminarla:

        @Override
        public boolean deleteTodoItem(String id) {
            // DocumentDB refers to documents by self link rather than id.

            // Query for the document to retrieve the self link.
            Document todoItemDocument = getDocumentById(id);

            try {
                // Delete the document by self link.
                documentClient.deleteDocument(todoItemDocument.getSelfLink(), null);
            } catch (DocumentClientException e) {
                e.printStackTrace();
                return false;
            }

            return true;
        }


##<a id="Wire"></a>Paso 5: Cableado el resto del proyecto de desarrollo de aplicaciones de Java junto

Ahora que hemos terminado la diversión bits - todos los que izquierda consiste en crear un usuario rápido de la interfaz y conecte el cable hasta nuestra DAO.

1. En primer lugar, vamos a empezar con la creación de un controlador para llamar a nuestro DAO:

        public class TodoItemController {
            public static TodoItemController getInstance() {
                if (todoItemController == null) {
                    todoItemController = new TodoItemController(TodoDaoFactory.getDao());
                }
                return todoItemController;
            }

            private static TodoItemController todoItemController;

            private final TodoDao todoDao;

            TodoItemController(TodoDao todoDao) {
                this.todoDao = todoDao;
            }

            public TodoItem createTodoItem(@NonNull String name,
                    @NonNull String category, boolean isComplete) {
                TodoItem todoItem = TodoItem.builder().name(name).category(category)
                        .complete(isComplete).build();
                return todoDao.createTodoItem(todoItem);
            }

            public boolean deleteTodoItem(@NonNull String id) {
                return todoDao.deleteTodoItem(id);
            }

            public TodoItem getTodoItemById(@NonNull String id) {
                return todoDao.readTodoItem(id);
            }

            public List<TodoItem> getTodoItems() {
                return todoDao.readTodoItems();
            }

            public TodoItem updateTodoItem(@NonNull String id, boolean isComplete) {
                return todoDao.updateTodoItem(id, isComplete);
            }
        }

    En una aplicación más compleja, el controlador puede alojar la lógica empresarial complicada sobre DAO.

2. A continuación, se creará un servlet para enrutar las solicitudes HTTP en el controlador de:

        public class TodoServlet extends HttpServlet {
            // API Keys
            public static final String API_METHOD = "method";

            // API Methods
            public static final String CREATE_TODO_ITEM = "createTodoItem";
            public static final String GET_TODO_ITEMS = "getTodoItems";
            public static final String UPDATE_TODO_ITEM = "updateTodoItem";

            // API Parameters
            public static final String TODO_ITEM_ID = "todoItemId";
            public static final String TODO_ITEM_NAME = "todoItemName";
            public static final String TODO_ITEM_CATEGORY = "todoItemCategory";
            public static final String TODO_ITEM_COMPLETE = "todoItemComplete";

            public static final String MESSAGE_ERROR_INVALID_METHOD = "{'error': 'Invalid method'}";

            private static final long serialVersionUID = 1L;
            private static final Gson gson = new Gson();

            @Override
            protected void doGet(HttpServletRequest request,
                    HttpServletResponse response) throws ServletException, IOException {

                String apiResponse = MESSAGE_ERROR_INVALID_METHOD;

                TodoItemController todoItemController = TodoItemController
                        .getInstance();

                String id = request.getParameter(TODO_ITEM_ID);
                String name = request.getParameter(TODO_ITEM_NAME);
                String category = request.getParameter(TODO_ITEM_CATEGORY);
                boolean isComplete = StringUtils.equalsIgnoreCase("true",
                        request.getParameter(TODO_ITEM_COMPLETE)) ? true : false;

                switch (request.getParameter(API_METHOD)) {
                case CREATE_TODO_ITEM:
                    apiResponse = gson.toJson(todoItemController.createTodoItem(name,
                            category, isComplete));
                    break;
                case GET_TODO_ITEMS:
                    apiResponse = gson.toJson(todoItemController.getTodoItems());
                    break;
                case UPDATE_TODO_ITEM:
                    apiResponse = gson.toJson(todoItemController.updateTodoItem(id,
                            isComplete));
                    break;
                default:
                    break;
                }

                response.getWriter().println(apiResponse);
            }

            @Override
            protected void doPost(HttpServletRequest request,
                    HttpServletResponse response) throws ServletException, IOException {
                doGet(request, response);
            }
        }

3. Es necesario una interfaz de usuario Web para mostrar al usuario. Vamos a volver a escribir el index.jsp que se creó con una versión anterior:

        <html>
        <head>
          <meta http-equiv="Content-Type" content="text/html; charset=ISO-8859-1">
          <meta http-equiv="X-UA-Compatible" content="IE=edge;" />
          <title>Azure DocumentDB Java Sample</title>

          <!-- Bootstrap -->
          <link href="//ajax.aspnetcdn.com/ajax/bootstrap/3.2.0/css/bootstrap.min.css" rel="stylesheet">

          <style>
            /* Add padding to body for fixed nav bar */
            body {
              padding-top: 50px;
            }
          </style>
        </head>
        <body>
          <!-- Nav Bar -->
          <div class="navbar navbar-inverse navbar-fixed-top" role="navigation">
            <div class="container">
              <div class="navbar-header">
                <a class="navbar-brand" href="#">My Tasks</a>
              </div>
            </div>
          </div>

          <!-- Body -->
          <div class="container">
            <h1>My ToDo List</h1>

            <hr/>

            <!-- The ToDo List -->
            <div class = "todoList">
              <table class="table table-bordered table-striped" id="todoItems">
                <thead>
                  <tr>
                    <th>Name</th>
                    <th>Category</th>
                    <th>Complete</th>
                  </tr>
                </thead>
                <tbody>
                </tbody>
              </table>

              <!-- Update Button -->
              <div class="todoUpdatePanel">
                <form class="form-horizontal" role="form">
                  <button type="button" class="btn btn-primary">Update Tasks</button>
                </form>
              </div>

            </div>

            <hr/>

            <!-- Item Input Form -->
            <div class="todoForm">
              <form class="form-horizontal" role="form">
                <div class="form-group">
                  <label for="inputItemName" class="col-sm-2">Task Name</label>
                  <div class="col-sm-10">
                    <input type="text" class="form-control" id="inputItemName" placeholder="Enter name">
                  </div>
                </div>

                <div class="form-group">
                  <label for="inputItemCategory" class="col-sm-2">Task Category</label>
                  <div class="col-sm-10">
                    <input type="text" class="form-control" id="inputItemCategory" placeholder="Enter category">
                  </div>
                </div>

                <button type="button" class="btn btn-primary">Add Task</button>
              </form>
            </div>

          </div>

          <!-- Placed at the end of the document so the pages load faster -->
          <script src="//ajax.aspnetcdn.com/ajax/jQuery/jquery-2.1.1.min.js"></script>
          <script src="//ajax.aspnetcdn.com/ajax/bootstrap/3.2.0/bootstrap.min.js"></script>
          <script src="assets/todo.js"></script>
        </body>
        </html>

4. Y, finalmente, escriba algunos Javascript del lado cliente para unir la interfaz de usuario de web y el servidor:

        var todoApp = {
          /*
           * API methods to call Java backend.
           */
          apiEndpoint: "api",

          createTodoItem: function(name, category, isComplete) {
            $.post(todoApp.apiEndpoint, {
                "method": "createTodoItem",
                "todoItemName": name,
                "todoItemCategory": category,
                "todoItemComplete": isComplete
              },
              function(data) {
                var todoItem = data;
                todoApp.addTodoItemToTable(todoItem.id, todoItem.name, todoItem.category, todoItem.complete);
              },
              "json");
          },

          getTodoItems: function() {
            $.post(todoApp.apiEndpoint, {
                "method": "getTodoItems"
              },
              function(data) {
                var todoItemArr = data;
                $.each(todoItemArr, function(index, value) {
                  todoApp.addTodoItemToTable(value.id, value.name, value.category, value.complete);
                });
              },
              "json");
          },

          updateTodoItem: function(id, isComplete) {
            $.post(todoApp.apiEndpoint, {
                "method": "updateTodoItem",
                "todoItemId": id,
                "todoItemComplete": isComplete
              },
              function(data) {},
              "json");
          },

          /*
           * UI Methods
           */
          addTodoItemToTable: function(id, name, category, isComplete) {
            var rowColor = isComplete ? "active" : "warning";

            todoApp.ui_table().append($("<tr>")
              .append($("<td>").text(name))
              .append($("<td>").text(category))
              .append($("<td>")
                .append($("<input>")
                  .attr("type", "checkbox")
                  .attr("id", id)
                  .attr("checked", isComplete)
                  .attr("class", "isComplete")
                ))
              .addClass(rowColor)
            );
          },

          /*
           * UI Bindings
           */
          bindCreateButton: function() {
            todoApp.ui_createButton().click(function() {
              todoApp.createTodoItem(todoApp.ui_createNameInput().val(), todoApp.ui_createCategoryInput().val(), false);
              todoApp.ui_createNameInput().val("");
              todoApp.ui_createCategoryInput().val("");
            });
          },

          bindUpdateButton: function() {
            todoApp.ui_updateButton().click(function() {
              // Disable button temporarily.
              var myButton = $(this);
              var originalText = myButton.text();
              $(this).text("Updating...");
              $(this).prop("disabled", true);

              // Call api to update todo items.
              $.each(todoApp.ui_updateId(), function(index, value) {
                todoApp.updateTodoItem(value.name, value.value);
                $(value).remove();
              });

              // Re-enable button.
              setTimeout(function() {
                myButton.prop("disabled", false);
                myButton.text(originalText);
              }, 500);
            });
          },

          bindUpdateCheckboxes: function() {
            todoApp.ui_table().on("click", ".isComplete", function(event) {
              var checkboxElement = $(event.currentTarget);
              var rowElement = $(event.currentTarget).parents('tr');
              var id = checkboxElement.attr('id');
              var isComplete = checkboxElement.is(':checked');

              // Toggle table row color
              if (isComplete) {
                rowElement.addClass("active");
                rowElement.removeClass("warning");
              } else {
                rowElement.removeClass("active");
                rowElement.addClass("warning");
              }

              // Update hidden inputs for update panel.
              todoApp.ui_updateForm().children("input[name='" + id + "']").remove();

              todoApp.ui_updateForm().append($("<input>")
                .attr("type", "hidden")
                .attr("class", "updateComplete")
                .attr("name", id)
                .attr("value", isComplete));

            });
          },

          /*
           * UI Elements
           */
          ui_createNameInput: function() {
            return $(".todoForm #inputItemName");
          },

          ui_createCategoryInput: function() {
            return $(".todoForm #inputItemCategory");
          },

          ui_createButton: function() {
            return $(".todoForm button");
          },

          ui_table: function() {
            return $(".todoList table tbody");
          },

          ui_updateButton: function() {
            return $(".todoUpdatePanel button");
          },

          ui_updateForm: function() {
            return $(".todoUpdatePanel form");
          },

          ui_updateId: function() {
            return $(".todoUpdatePanel .updateComplete");
          },

          /*
           * Install the TodoApp
           */
          install: function() {
            todoApp.bindCreateButton();
            todoApp.bindUpdateButton();
            todoApp.bindUpdateCheckboxes();

            todoApp.getTodoItems();
          }
        };

        $(document).ready(function() {
          todoApp.install();
        });

5. ¡Impresionantes! Ahora todo lo que queda es probar la aplicación. Ejecute la aplicación localmente y agregue algunos elementos Todo rellenando el nombre del elemento y la categoría y haciendo clic en **Agregar tarea**.

6. Una vez que aparezca el elemento, puede actualizar si ha finalizado alternar la casilla de verificación y haciendo clic en **Actualizar tareas**.

##<a id="Deploy"></a>Paso 6: Implementar la aplicación de Java a sitios Web de Azure

Sitios Web Azure hace que la implementación de aplicaciones de Java tan sencillo como exportar la aplicación como un archivo de guerra y bien enviarlo a través de control de código fuente (por ejemplo, GIT) o FTP.

1. Para exportar la aplicación como una guerra, haga clic en el proyecto en el **Explorador de proyectos**, haga clic en **Exportar**y, a continuación, haga clic en **Archivo de guerra**.
2. En la ventana **Guerra exportar** , haga lo siguiente:
 - En el cuadro proyecto de Web, escriba azure documentdb java muestras.
 - En el cuadro de destino, elija un destino para guardar el archivo de guerra.
 - Haga clic en **Finalizar**.

3. Ahora que ya tiene un archivo de guerra en mano, simplemente puede cargar al directorio de **aplicaciones Web** de su sitio Web de Azure. Para obtener instrucciones acerca de cómo cargar el archivo, vea [Agregar una aplicación a su sitio Web de Java en Azure](../app-service-web/web-sites-java-add-app.md).

    Una vez cargado el archivo guerra en el directorio de aplicaciones Web, el entorno de tiempo de ejecución detecta que ha agregado y lo cargará automáticamente.
4. Para ver el producto terminado, vaya a http://YOUR\_sitio\_NAME.azurewebsites.net/azure-documentdb-java-sample/ y empezar a agregar sus tareas!

##<a id="GetProject"></a>Obtener el proyecto de GitHub

Todos los ejemplos de este tutorial se incluyen en el proyecto de [todo](https://github.com/Azure-Samples/documentdb-java-todo-app) en GitHub. Para importar el proyecto todo en Eclipse, asegúrese de que el software y los recursos enumerados en la sección [requisitos previos](#Prerequisites) , a continuación, haga lo siguiente:

1. Instalar [Project Lombok](http://projectlombok.org/). Lombok se usa para generar constructores, captadores, establecedores del proyecto. Una vez haya descargado el archivo lombok.jar, haga doble clic en él para instalarlo o instalar desde la línea de comandos.
2. Si Eclipse está abierto, ciérrelo y reinícielo para cargar Lombok.
3. Eclipse, en el menú **archivo** , haga clic en **Importar**.
4. En la ventana **Importar** , haga clic en **Git**, haga clic en **proyectos de Git**y, a continuación, haga clic en **siguiente**.
5. En la pantalla **Seleccione repositorio de origen** , haga clic en **Clonar URI**.
6. En la pantalla de **Origen Git repositorio** , en el cuadro **URI** , escriba https://github.com/Azure-Samples/documentdb-java-todo-app.git y, a continuación, haga clic en **siguiente**.
7. En la pantalla de **Selección de bifurcación** , asegúrese de que **principal** está seleccionada y, a continuación, haga clic en **siguiente**.
8. En la pantalla de **Destino Local** , haga clic en **Examinar** para seleccionar una carpeta en el repositorio se pueden copiar y, a continuación, haga clic en **siguiente**.
9. En la pantalla **Seleccione Asistente para importar proyectos** , asegúrese de que está seleccionada la opción **importar proyectos existentes** y, a continuación, haga clic en **siguiente**.
10. En la pantalla **Importar proyectos** , anule la selección del proyecto **DocumentDB** y, a continuación, haga clic en **Finalizar**. El proyecto DocumentDB contiene el SDK de Java DocumentDB, que vamos a agregar como dependencia en su lugar.
11. En el **Explorador de proyectos**, vaya a azure-documentdb-java-sample\src\com.microsoft.azure.documentdb.sample.dao\DocumentClientFactory.java y reemplace los valores HOST y MASTER_KEY con el URI y la clave principal de la cuenta DocumentDB y, a continuación, guarde el archivo. Para obtener más información, vea [paso 1. Crear una cuenta de base de datos de DocumentDB](#CreateDB).
12. En el **Explorador de proyectos**, haga clic con el botón secundario del mouse en la **azure documentdb java muestras**, haga clic en **Generar la ruta de acceso**y, a continuación, haga clic en **Configurar ruta de compilación**.
13. En la pantalla de la **Ruta de acceso de compilación de Java** , en el panel derecho, seleccione la ficha **bibliotecas** y, a continuación, haga clic en **Agregar JAR externos**. Navegue hasta la ubicación del archivo lombok.jar, haga clic en **Abrir**y, a continuación, haga clic en **Aceptar**.
14. Usar paso 12 volver a abrir la ventana **Propiedades** y haga clic en **Tiempo de ejecución de destino**en el panel izquierdo.
15. En la pantalla de **Tiempos de ejecución de destino** , haga clic en **nuevo**, seleccione **v7.0 Apache Tomcat**y, a continuación, haga clic en **Aceptar**.
16. Use paso 12 volver a abrir la ventana **Propiedades** y, a continuación, en el panel izquierdo, haga clic en **Proyecto facetas**.
17. En la pantalla de **Los aspectos del proyecto** , seleccione **Módulo Web dinámico** y **Java**y, a continuación, haga clic en **Aceptar**.
18. En la pestaña **servidores** , en la parte inferior de la pantalla, haga **Tomcat v7.0 servidor en host local** y, a continuación, haga clic en **Agregar y quitar**.
19. En la ventana **Agregar y quitar** , mover **azure documentdb java muestras** al cuadro **configurado** y, a continuación, haga clic en **Finalizar**.
20. En la ficha **servidor** , contextual **Tomcat v7.0 servidor en host local**y, a continuación, haga clic en **reiniciar**.
21. En un explorador, vaya a http://localhost: 8080/azure-documentdb-java-sample / y comenzar a agregar a la lista de tareas. Tenga en cuenta que si cambia los valores de puerto predeterminado, cambie 8080 al valor seleccionado.
22. Para implementar el proyecto en un sitio web de Azure, consulte [paso 6. Implementar la aplicación a los sitios Web de Azure](#Deploy).

[1]: media/documentdb-java-application/keys.png
