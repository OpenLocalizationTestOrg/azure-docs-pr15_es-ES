<properties 
    pageTitle="Desencadenadores de aplicación de servicio de aplicación API | Microsoft Azure" 
    description="Cómo implementar desencadenadores en una API App en servicio de la aplicación de Azure" 
    services="logic-apps" 
    documentationCenter=".net" 
    authors="guangyang"
    manager="wpickett" 
    editor="jimbe"/>

<tags 
    ms.service="logic-apps" 
    ms.workload="na" 
    ms.tgt_pltfrm="dotnet" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/25/2016" 
    ms.author="rachelap"/>

# <a name="azure-app-service-api-app-triggers"></a>Desencadenadores de aplicación de Azure API del servicio de aplicación

>[AZURE.NOTE] Esta versión de este artículo se aplica a la versión de esquema de API aplicaciones 2014 12-01-vista previa.


## <a name="overview"></a>Información general

En este artículo se explica cómo implementar desencadenadores de aplicación API y consumir a ellos desde una aplicación de lógica.

Todos los fragmentos de código de este tema se copian desde la [aplicación de la API de FileWatcher código de ejemplo](http://go.microsoft.com/fwlink/?LinkId=534802). 

Tenga en cuenta que tendrá que descargar el paquete de nuget siguientes para el código de este artículo para crear y ejecutar: [http://www.nuget.org/packages/Microsoft.Azure.AppService.ApiApps.Service/](http://www.nuget.org/packages/Microsoft.Azure.AppService.ApiApps.Service/).

## <a name="what-are-api-app-triggers"></a>¿Qué son los desencadenadores de aplicación API?

Es un escenario común para una aplicación de API desencadenar un evento para que los clientes de la aplicación de la API pueden realizar la acción correspondiente en respuesta al evento. El mecanismo de la API de REST según que admita este escenario se denomina un desencadenador de aplicación de la API. 

Por ejemplo, supongamos que el código de cliente es mediante la [aplicación de Twitter API de conector](../app-service-logic/app-service-logic-connector-twitter.md) y el código necesita realizar una acción basada en tweets nuevos que contienen palabras específicas. En este caso, puede configurar un desencadenador de inserción o de sondeo para facilitar esta necesidad.

## <a name="poll-trigger-versus-push-trigger"></a>Desencadenador de sondeo frente a desencadenador de inserción

Actualmente, los dos tipos de desencadenadores son compatibles:

- Desencadenador de sondeo - cliente explora la aplicación de la API de notificación de un evento que ha desencadenado 
- Desencadenador de inserción - cliente se notifica mediante la aplicación de la API cuando se activa un evento 

### <a name="poll-trigger"></a>Desencadenador de sondeo

Un desencadenador de sondeo se implementa como una API de REST normal y espera que a sus clientes (por ejemplo, una aplicación de la lógica) para sondear para recibir notificación. Mientras el cliente puede mantener el estado, el propio desencadenador de sondeo no tiene estado. 

La siguiente información relacionada con los paquetes de convocatoria y respuesta se muestran algunos aspectos clave del contrato de sondeo desencadenador:

- Solicitar
    - Método HTTP: obtener
    - Parámetros
        - triggerState: este parámetro opcional permite a los clientes especificar su estado para que el desencadenador de sondeo correctamente puede decidir si desea devolver notificación o no basadas en el estado especificado.
        - Parámetros específicos de la API
- Respuesta
    - Código de estado **200** - solicitud es válida y hay una notificación de desencadenador. El contenido de la notificación será el cuerpo de la respuesta. Un encabezado "Reintentar después" en la respuesta indica que se deben recuperar datos adicionales de notificación con una llamada de solicitud posterior.
    - Código de estado **202** - solicitud es válida, pero no hay ninguna notificación nueva desde el desencadenador.
    - Código de estado **4xx** - solicitud no es válida. El cliente no debería reintentar la solicitud.
    - Código de estado **5xx** - solicitud ha generado un error interno del servidor o un problema temporal. El cliente debe volver a intentar la solicitud.

Fragmento de código siguiente es un ejemplo de cómo implementar un desencadenador de sondeo.

    // Implement a poll trigger.
    [HttpGet]
    [Route("api/files/poll/TouchedFiles")]
    public HttpResponseMessage TouchedFilesPollTrigger(
        // triggerState is a UTC timestamp
        string triggerState,
        // Additional parameters
        string searchPattern = "*")
    {
        // Check to see whether there is any file touched after the timestamp.
        var lastTriggerTimeUtc = DateTime.Parse(triggerState).ToUniversalTime();
        var touchedFiles = Directory.EnumerateFiles(rootPath, searchPattern, SearchOption.AllDirectories)
            .Select(f => FileInfoWrapper.FromFileInfo(new FileInfo(f)))
            .Where(fi => fi.LastAccessTimeUtc > lastTriggerTimeUtc);

        // If there are files touched after the timestamp, return their information.
        if (touchedFiles != null && touchedFiles.Count() != 0)
        {
            // Extension method provided by the AppService service SDK.
            return this.Request.EventTriggered(new { files = touchedFiles });
        }
        // If there are no files touched after the timestamp, tell the caller to poll again after 1 mintue.
        else
        {
            // Extension method provided by the AppService service SDK.
            return this.Request.EventWaitPoll(new TimeSpan(0, 1, 0));
        }
    }

Para probar este desencadenador de sondeo, siga estos pasos:

1. Implemente la API App con una configuración de autenticación de **público anónimo**.
2. Llamar a la operación **táctil** para el acceso a un archivo. La imagen siguiente muestra un ejemplo de solicitud a través de Postman.
   ![Operación de llamada Touch mediante Postman](./media/app-service-api-dotnet-triggers/calltouchfilefrompostman.PNG)
3. El desencadenador de sondeo de llamadas con el parámetro **triggerState** establecer una marca de tiempo antes de paso 2. La imagen siguiente muestra la solicitud de ejemplo mediante Postman.
   ![Desencadenador de sondeo de la llamada a través de Postman](./media/app-service-api-dotnet-triggers/callpolltriggerfrompostman.PNG)

### <a name="push-trigger"></a>Desencadenador de inserción

Un desencadenador de inserción se implementa como una API de REST normal que inserta notificaciones a los clientes que se han registrado para recibir una notificación cuando se desencadenan sucesos específicos.

La siguiente información relacionada con los paquetes de convocatoria y respuesta ilustrar algunos aspectos clave del contrato de desencadenador de inserción.

- Solicitar
    - Método HTTP: colocar
    - Parámetros
        - Id. de lanzamiento: requerido - cadena opaca (como un GUID) que representa el registro de un desencadenador de inserción.
        - callbackUrl: requerido - dirección URL de la devolución de llamada que invocar cuando se activa el evento. La llamada es una llamada de HTTP POST simple.
        - Parámetros específicos de la API
- Respuesta
    - Estado código **200** - solicitud de registro de cliente correcto.
    - Código de estado **4xx** - solicitud no es válida. El cliente no debería reintentar la solicitud.
    - Código de estado **5xx** - solicitud ha generado un error interno del servidor o un problema temporal. El cliente debe volver a intentar la solicitud.
- Devolución de llamada
    - Método HTTP: publicación
    - Solicitar cuerpo: contenido de la notificación.

Fragmento de código siguiente es un ejemplo de cómo implementar un desencadenador de inserción:

    // Implement a push trigger.
    [HttpPut]
    [Route("api/files/push/TouchedFiles/{triggerId}")]
    public HttpResponseMessage TouchedFilesPushTrigger(
        // triggerId is an opaque string.
        string triggerId,
        // A helper class provided by the AppService service SDK.
        // Here it defines the input of the push trigger is a string and the output to the callback is a FileInfoWrapper object.
        [FromBody]TriggerInput<string, FileInfoWrapper> triggerInput)
    {
        // Register the trigger to some trigger store.
        triggerStore.RegisterTrigger(triggerId, rootPath, triggerInput);

        // Extension method provided by the AppService service SDK indicating the registration is completed.
        return this.Request.PushTriggerRegistered(triggerInput.GetCallback());
    }

    // A simple in-memory trigger store.
    public class InMemoryTriggerStore
    {
        private static InMemoryTriggerStore instance;

        private IDictionary<string, FileSystemWatcher> _store;

        private InMemoryTriggerStore()
        {
            _store = new Dictionary<string, FileSystemWatcher>();
        }

        public static InMemoryTriggerStore Instance
        {
            get
            {
                if (instance == null)
                {
                    instance = new InMemoryTriggerStore();
                }
                return instance;
            }
        }

        // Register a push trigger.
        public void RegisterTrigger(string triggerId, string rootPath,
            TriggerInput<string, FileInfoWrapper> triggerInput)
        {
            // Use FileSystemWatcher to listen to file change event.
            var filter = string.IsNullOrEmpty(triggerInput.inputs) ? "*" : triggerInput.inputs;
            var watcher = new FileSystemWatcher(rootPath, filter);
            watcher.IncludeSubdirectories = true;
            watcher.EnableRaisingEvents = true;
            watcher.NotifyFilter = NotifyFilters.LastAccess;

            // When some file is changed, fire the push trigger.
            watcher.Changed +=
                (sender, e) => watcher_Changed(sender, e,
                    Runtime.FromAppSettings(),
                    triggerInput.GetCallback());

            // Assoicate the FileSystemWatcher object with the triggerId.
            _store[triggerId] = watcher;

        }

        // Fire the assoicated push trigger when some file is changed.
        void watcher_Changed(object sender, FileSystemEventArgs e,
            // AppService runtime object needed to invoke the callback.
            Runtime runtime,
            // The callback to invoke.
            ClientTriggerCallback<FileInfoWrapper> callback)
        {
            // Helper method provided by AppService service SDK to invoke a push trigger callback.
            callback.InvokeAsync(runtime, FileInfoWrapper.FromFileInfo(new FileInfo(e.FullPath)));
        }
    }

Para probar este desencadenador de sondeo, siga estos pasos:

1. Implemente la API App con una configuración de autenticación de **público anónimo**.
2. Vaya a [http://requestb.in/](http://requestb.in/) para crear un RequestBin que servirá como la dirección URL de devolución de llamada.
3. Llame el desencadenador de inserción con un GUID como **Id. de lanzamiento** y la dirección URL de RequestBin como **callbackUrl**.
   ![Desencadenador de inserción de la llamada a través de Postman](./media/app-service-api-dotnet-triggers/callpushtriggerfrompostman.PNG)
4. Llamar a la operación **táctil** para el acceso a un archivo. La imagen siguiente muestra un ejemplo de solicitud a través de Postman.
   ![Operación de llamada Touch mediante Postman](./media/app-service-api-dotnet-triggers/calltouchfilefrompostman.PNG)
5. Compruebe el RequestBin para confirmar que la devolución de llamada de desencadenador de inserción se invoca con el resultado de la propiedad.
   ![Desencadenador de sondeo de la llamada a través de Postman](./media/app-service-api-dotnet-triggers/pushtriggercallbackinrequestbin.PNG)

### <a name="describe-triggers-in-api-definition"></a>Describir desencadenadores de definición de la API

Después de implementar los desencadenadores e implementar la aplicación de la API para Azure, desplácese hasta el módulo de **Definición de la API** en el portal de vista previa de Azure y verá que desencadenadores se reconocen automáticamente la interfaz de usuario, que se basa en la definición de Swagger 2.0 API de la aplicación de la API.

![Módulo de definición de API](./media/app-service-api-dotnet-triggers/apidefinitionblade.PNG)

Si haga clic en el botón **Descargar Swagger** y abra el archivo JSON, verá resultados similares a los siguientes:

    "/api/files/poll/TouchedFiles": {
      "get": {
        "operationId": "Files_TouchedFilesPollTrigger",
        ...
        "x-ms-scheduler-trigger": "poll"
      }
    },
    "/api/files/push/TouchedFiles/{triggerId}": {
      "put": {
        "operationId": "Files_TouchedFilesPushTrigger",
        ...
        "x-ms-scheduler-trigger": "push"
      }
    }

La propiedad de extensión **x ms-del programador desencadenador** es cómo se describen en la definición de la API desencadenadores y se agregan automáticamente a la puerta de enlace de la aplicación API cuando se solicita la definición de la API a través de la puerta de enlace si la solicitud a uno de los siguientes criterios. (También puede agregar esta propiedad manualmente.)

- Desencadenador de sondeo
    - Si el método HTTP es **obtener**.
    - Si la propiedad **operationId** contiene la cadena **desencadenador**.
    - Si la propiedad de **parámetros** incluye un parámetro con una propiedad de **nombre** establecida en **triggerState**.
- Desencadenador de inserción
    - Si el método HTTP es **colocar**.
    - Si la propiedad **operationId** contiene la cadena **desencadenador**.
    - Si la propiedad de **parámetros** incluye un parámetro con una propiedad de **nombre** establecida en **Id. de lanzamiento**.

## <a name="use-api-app-triggers-in-logic-apps"></a>Usar desencadenadores de aplicación API en las aplicaciones de lógica

### <a name="list-and-configure-api-app-triggers-in-the-logic-apps-designer"></a>Mostrar y configurar desencadenadores de aplicación API en el Diseñador de aplicaciones de lógica

Si crea una aplicación lógica en el mismo grupo de recursos que la aplicación de la API, podrá agregar al diseñador lienzo simplemente haciendo clic en ella. Las siguientes imágenes muestran esto:

![Desencadenadores en el Diseñador de la aplicación de lógica](./media/app-service-api-dotnet-triggers/triggersinlogicappdesigner.PNG)

![Configurar desencadenador de sondeo en el Diseñador de la aplicación de lógica](./media/app-service-api-dotnet-triggers/configurepolltriggerinlogicappdesigner.PNG)

![Configurar el desencadenador de inserción en el Diseñador de la aplicación de lógica](./media/app-service-api-dotnet-triggers/configurepushtriggerinlogicappdesigner.PNG)

## <a name="optimize-api-app-triggers-for-logic-apps"></a>Optimizar desencadenadores de aplicación API para las aplicaciones de lógica

Después de agregar desencadenadores a una aplicación de API, hay algunas cosas que puede hacer para mejorar la experiencia al usar la aplicación de la API en una aplicación de lógica.

Por ejemplo, el parámetro **triggerState** para desencadenadores de sondeo se debe establecer en la siguiente expresión en la aplicación de la lógica. Esta expresión debe evaluar la última invocación del desencadenador desde la aplicación de lógica y ese valor devuelto.  

    @coalesce(triggers()?.outputs?.body?['triggerState'], '')

Nota: Para obtener una explicación de las funciones que se usan en la expresión anterior, consulte la documentación del [Lenguaje de definición de flujo de trabajo de lógica de aplicación](https://msdn.microsoft.com/library/azure/dn948512.aspx).

Usuarios de la aplicación de lógica necesarias para proporcionar la expresión por encima del parámetro **triggerState** mientras utiliza el desencadenador. Es posible que tenga este valor preestablecido por el Diseñador de la aplicación de lógica a través de la propiedad de extensión **recomendación-x-ms-programador**.  La propiedad de extensión **x visibilidad de ms** se puede establecer un valor de *interno* para que el parámetro no se muestra en el diseñador.  Muestra en el fragmento de código siguiente.

    "/api/Messages/poll": {
      "get": {
        "operationId": "Messages_NewMessageTrigger",
        "parameters": [
          {
            "name": "triggerState",
            "in": "query",
            "required": true,
            "x-ms-visibility": "internal",
            "x-ms-scheduler-recommendation": "@coalesce(triggers()?.outputs?.body?['triggerState'], '')",
            "type": "string"
          }
        ]
        ...
        "x-ms-scheduler-trigger": "poll"
      }
    }

Desencadenadores de inserción, el parámetro de **Id. de lanzamiento** debe identificar la aplicación lógica. Es recomendable establecer esta propiedad en el nombre del flujo de trabajo mediante la siguiente expresión:

    @workflow().name

Mediante las propiedades de extensión **x-ms-programador-recomendación** y **x-ms-visibilidad** en su definición de la API, la aplicación de la API puede transmitir al diseñador de aplicación lógica para configurar automáticamente esta expresión para el usuario.

        "parameters":[  
          {  
            "name":"triggerId",
            "in":"path",
            "required":true,
            "x-ms-visibility":"internal",
            "x-ms-scheduler-recommendation":"@workflow().name",
            "type":"string"
          },


### <a name="add-extension-properties-in-api-defintion"></a>Agregar propiedades de extensión de definición de la API

Información de metadatos - como la extensión propiedades **x-ms-programador-recomendación** y **visibilidad de ms x** - puede agregarse en la definición de la API de dos maneras: estática o dinámica.

Directamente para metadatos estáticos, puede editar el archivo */metadata/apiDefinition.swagger.json* en su proyecto y agregar las propiedades de forma manual.

Para las aplicaciones API con metadatos dinámico, puede editar el archivo de SwaggerConfig.cs para agregar un filtro de operación que puede agregar estas extensiones.

    GlobalConfiguration.Configuration 
        .EnableSwagger(c =>
            {
                ...
                c.OperationFilter<TriggerStateFilter>();
                ...
            }


A continuación se muestra un ejemplo de cómo se puede implementar esta clase para facilitar el escenario de metadatos dinámico.

    // Add extension properties on the triggerState parameter
    public class TriggerStateFilter : IOperationFilter
    {

        public void Apply(Operation operation, SchemaRegistry schemaRegistry, System.Web.Http.Description.ApiDescription apiDescription)
        {
            if (operation.operationId.IndexOf("Trigger", StringComparison.InvariantCultureIgnoreCase) >= 0)
            {
                // this is a possible trigger
                var triggerStateParam = operation.parameters.FirstOrDefault(x => x.name.Equals("triggerState"));
                if (triggerStateParam != null)
                {
                    if (triggerStateParam.vendorExtensions == null)
                    {
                        triggerStateParam.vendorExtensions = new Dictionary<string, object>();
                    }

                    // add 2 vendor extensions
                    // x-ms-visibility: set to 'internal' to signify this is an internal field
                    // x-ms-scheduler-recommendation: set to a value that logic app can use
                    triggerStateParam.vendorExtensions.Add("x-ms-visibility", "internal");
                    triggerStateParam.vendorExtensions.Add("x-ms-scheduler-recommendation",
                                                           "@coalesce(triggers()?.outputs?.body?['triggerState'], '')");
                }
            }
        }
    }
 
