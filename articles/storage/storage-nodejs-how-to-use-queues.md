<properties
    pageTitle="Cómo usar el almacenamiento de la cola de Node.js | Microsoft Azure"
    description="Obtenga información sobre cómo usar el servicio de cola de Azure para crear y eliminar colas, insertar, obtener y eliminar mensajes. Ejemplos escritos en Node.js."
    services="storage"
    documentationCenter="nodejs"
    authors="robinsh"
    manager="carmonm"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="nodejs"
    ms.topic="article"
    ms.date="08/11/2016"
    ms.author="robinsh"/>


# <a name="how-to-use-queue-storage-from-nodejs"></a>Cómo usar el almacenamiento de la cola de Node.js

[AZURE.INCLUDE [storage-selector-queue-include](../../includes/storage-selector-queue-include.md)]
<br/>
[AZURE.INCLUDE [storage-try-azure-tools-queues](../../includes/storage-try-azure-tools-queues.md)]

## <a name="overview"></a>Información general

Esta guía le muestra cómo llevar a cabo escenarios comunes mediante el servicio de cola de Microsoft Azure. Los ejemplos se escriben mediante la API de Node.js. Los escenarios cubiertos incluyen **Insertar**, **leerlo**, **Introducción**y **Eliminar** mensajes de la cola, así como **crear y eliminar colas**.

[AZURE.INCLUDE [storage-queue-concepts-include](../../includes/storage-queue-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## <a name="create-a-nodejs-application"></a>Crear una aplicación de Node.js

Crear una aplicación de Node.js en blanco. Para crear una aplicación de Node.js obtener instrucciones, vea [crear una aplicación web de Node.js en la aplicación de servicio de Azure], [Compile e implemente una aplicación de Node.js a un servicio de nube de Azure] con Windows PowerShell o [Compile e implemente una aplicación web de Node.js a Azure con Web Matrix].

## <a name="configure-your-application-to-access-storage"></a>Configurar la aplicación para el almacenamiento de acceso

Para usar el almacenamiento de Azure, necesita el SDK de almacenamiento de Azure para Node.js, que incluye un conjunto de bibliotecas de comodidad que comunicarse con los servicios del resto de almacenamiento.

### <a name="use-node-package-manager-npm-to-obtain-the-package"></a>Utilice el nodo paquete Manager (NPM) para obtener el paquete

1.  Usar una interfaz de línea de comandos como **PowerShell** (Windows), **Terminal** (Mac) o **Bash** (Unix), vaya a la carpeta donde se crea la aplicación de ejemplo.

2.  En la ventana de comandos, escriba **npm instalar almacenamiento de azure** . Resultado del comando es similar al siguiente ejemplo.

        azure-storage@0.5.0 node_modules\azure-storage
        +-- extend@1.2.1
        +-- xmlbuilder@0.4.3
        +-- mime@1.2.11
        +-- node-uuid@1.4.3
        +-- validator@3.22.2
        +-- underscore@1.4.4
        +-- readable-stream@1.0.33 (string_decoder@0.10.31, isarray@0.0.1, inherits@2.0.1, core-util-is@1.0.1)
        +-- xml2js@0.2.7 (sax@0.5.2)
        +-- request@2.57.0 (caseless@0.10.0, aws-sign2@0.5.0, forever-agent@0.6.1, stringstream@0.0.4, oauth-sign@0.8.0, tunnel-agent@0.4.1, isstream@0.1.2, json-stringify-safe@5.0.1, bl@0.9.4, combined-stream@1.0.5, qs@3.1.0, mime-types@2.0.14, form-data@0.2.0, http-signature@0.11.0, tough-cookie@2.0.0, hawk@2.3.1, har-validator@1.8.0)

3.  El comando **ls** para comprobar que puede ejecutar manualmente una **nodo\_módulos** se creó la carpeta. Dentro de esa carpeta, encontrará el paquete de **almacenamiento de azure** , que contiene las bibliotecas que debe tener acceso al almacenamiento.

### <a name="import-the-package"></a>Importar el paquete

Con el Bloc de notas u otro editor de texto, agregue lo siguiente a la parte superior del archivo **server.js** de la aplicación donde vaya a usar el almacenamiento:

    var azure = require('azure-storage');

## <a name="setup-an-azure-storage-connection"></a>Configurar una conexión de almacenamiento de Azure

El módulo azure leerá las variables de entorno AZURE\_almacenamiento\_cuenta y AZURE\_almacenamiento\_ACCESS\_clave o AZURE\_almacenamiento\_conexión\_cadena información necesaria para conectarse a su cuenta de almacenamiento de Azure. Si no se establecen estas variables de entorno, debe especificar la información de cuenta al llamar a **createQueueService**.

Para obtener un ejemplo de configuración de las variables de entorno en el [Portal de Azure](https://portal.azure.com) para un sitio Web de Azure, consulte [Node.js web app con el servicio de la tabla de Azure].

## <a name="how-to-create-a-queue"></a>Cómo: Crear una cola

El código siguiente crea un objeto **QueueService** , que le permite trabajar con colas.

    var queueSvc = azure.createQueueService();

Use el método **createQueueIfNotExists** , que devuelve la cola especificada si ya existe o crea una nueva cola con el nombre especificado, si aún no existe.

    queueSvc.createQueueIfNotExists('myqueue', function(error, result, response){
      if(!error){
        // Queue created or exists
      }
    });

Si se crea la cola, `result.created` es true. Si la cola existe, `result.created` es false.

### <a name="filters"></a>Filtros

Las operaciones opcionales de filtrado se pueden aplicar a operaciones realizadas mediante **QueueService**. Operaciones de filtrado pueden incluir registro, Reintentar automáticamente, etcetera. Filtros son objetos que implementa un método con la firma:

    function handle (requestOptions, next)

Después de realizar su preprocesamiento en las opciones de la solicitud, el método necesita llamar "siguiente" pasando una devolución de llamada con la firma siguiente:

    function (returnObject, finalCallback, next)

En esta devolución de llamada y después de procesar lo returnObject (la respuesta de la solicitud en el servidor), la devolución de llamada debe invocar siguiente si existe para continuar procesando otros filtros o simplemente invocarla finalCallback en caso contrario, para terminar la llamada de servicio.

Dos filtros que implementan la lógica de reintento se incluyen en el SDK de Azure para Node.js, **ExponentialRetryPolicyFilter** y **LinearRetryPolicyFilter**. La siguiente crea un objeto **QueueService** que usa el **ExponentialRetryPolicyFilter**:

    var retryOperations = new azure.ExponentialRetryPolicyFilter();
    var queueSvc = azure.createQueueService().withFilter(retryOperations);

## <a name="how-to-insert-a-message-into-a-queue"></a>Cómo: Insertar un mensaje en una cola

Para insertar un mensaje en una cola, use el método de **createMessage** para crear un nuevo mensaje y agregar a la cola.

    queueSvc.createMessage('myqueue', "Hello world!", function(error, result, response){
      if(!error){
        // Message inserted
      }
    });

## <a name="how-to-peek-at-the-next-message"></a>Cómo: Inspeccionar el mensaje siguiente

Puede leer el mensaje de la parte delantera de una cola sin quitarlo de la cola llamando al método **peekMessages** . De forma predeterminada, **peekMessages** inspecciona un único mensaje.

    queueSvc.peekMessages('myqueue', function(error, result, response){
      if(!error){
        // Message text is in messages[0].messageText
      }
    });

La `result` contiene el mensaje.

> [AZURE.NOTE] Usando **peekMessages** cuando no hay que ningún mensaje en la cola no devolverán un error, pero no mensajes se devolverán.

## <a name="how-to-dequeue-the-next-message"></a>Cómo: Quitar el mensaje siguiente de la cola

Procesamiento de un mensaje es un proceso de dos fases:

1. Quitar el mensaje de la cola.

2. Eliminar el mensaje.

Para quitar un mensaje de la cola, use **getMessages**. De esta forma sea invisible en la cola los mensajes para que ningún otro cliente puede procesar. Una vez que la aplicación ha procesado un mensaje, llame a **deleteMessage** para eliminar de la cola. En el ejemplo siguiente se obtiene un mensaje y luego elimina:

    queueSvc.getMessages('myqueue', function(error, result, response){
      if(!error){
        // Message text is in messages[0].messageText
        var message = result[0];
        queueSvc.deleteMessage('myqueue', message.messageId, message.popReceipt, function(error, response){
          if(!error){
            //message deleted
          }
        });
      }
    });

> [AZURE.NOTE] De forma predeterminada, un mensaje sólo se oculta durante 30 segundos, después de que está visible para otros clientes. Puede especificar un valor diferente mediante `options.visibilityTimeout` con **getMessages**.

> [AZURE.NOTE]
> Usando **getMessages** cuando no hay que ningún mensaje en la cola no devolverán un error, pero no mensajes se devolverán.

## <a name="how-to-change-the-contents-of-a-queued-message"></a>Cómo: Cambiar el contenido de un mensaje en cola

Puede cambiar el contenido de un mensaje en lugar de en la cola mediante **updateMessage**. En el ejemplo siguiente se actualiza el texto de un mensaje:

    queueSvc.getMessages('myqueue', function(error, result, response){
      if(!error){
        // Got the message
        var message = result[0];
        queueSvc.updateMessage('myqueue', message.messageId, message.popReceipt, 10, {messageText: 'new text'}, function(error, result, response){
          if(!error){
            // Message updated successfully
          }
        });
      }
    });

## <a name="how-to-additional-options-for-dequeuing-messages"></a>Cómo: Opciones adicionales para cola mensajes

Hay dos formas de personalizar la recuperación de mensajes de una cola:

* `options.numOfMessages`-Recuperar un lote de mensajes (hasta 32.)
* `options.visibilityTimeout`-Establecer un tiempo de espera de más o menos tiempo.

En el ejemplo siguiente se utiliza el método **getMessages** obtener 15 mensajes en una llamada. A continuación, se procesa cada mensaje con un bucle for. También se establece el tiempo de espera de tiempo en cinco minutos para todos los mensajes devueltos por este método.

    queueSvc.getMessages('myqueue', {numOfMessages: 15, visibilityTimeout: 5 * 60}, function(error, result, response){
      if(!error){
        // Messages retreived
        for(var index in result){
          // text is available in result[index].messageText
          var message = result[index];
          queueSvc.deleteMessage(queueName, message.messageId, message.popReceipt, function(error, response){
            if(!error){
              // Message deleted
            }
          });
        }
      }
    });

## <a name="how-to-get-the-queue-length"></a>Cómo: Obtener la longitud de cola

El **getQueueMetadata** devuelve los metadatos de la cola, incluidos el número aproximado de los mensajes en la cola de espera.

    queueSvc.getQueueMetadata('myqueue', function(error, result, response){
      if(!error){
        // Queue length is available in result.approximateMessageCount
      }
    });

## <a name="how-to-list-queues"></a>Cómo: Lista colas

Para recuperar una lista de colas, utilice **listQueuesSegmented**. Para recuperar una lista filtrada por un prefijo concreto, utilice **listQueuesSegmentedWithPrefix**.

    queueSvc.listQueuesSegmented(null, function(error, result, response){
      if(!error){
        // result.entries contains the list of queues
      }
    });

Si no se pueden devolver todas las colas, `result.continuationToken` puede usarse como primer parámetro de **listQueuesSegmented** o el segundo parámetro de **listQueuesSegmentedWithPrefix** para recuperar más resultados.

## <a name="how-to-delete-a-queue"></a>Cómo: Eliminar una cola

Para eliminar una cola y todos los mensajes contenidos en él, llame al método de **deleteQueue** en el objeto de cola.

    queueSvc.deleteQueue(queueName, function(error, response){
      if(!error){
        // Queue has been deleted
      }
    });

Para borrar todos los mensajes de una cola sin eliminarlo, use **clearMessages**.

## <a name="how-to-work-with-shared-access-signatures"></a>Cómo: trabajar con acceso compartido firmas

Firmas de acceso compartido (SA) son una forma segura para proporcionar acceso granular a colas sin proporcionar el nombre de la cuenta de almacenamiento o teclas. Asociaciones de seguridad se usan a menudo para proporcionar acceso limitado a las colas, por ejemplo, lo que permite una aplicación móvil enviar mensajes.

Una aplicación de confianza, como un servicio basado en la nube genera una SA utilizando la **generateSharedAccessSignature** de la **QueueService**y proporciona a una aplicación de confianza o de confianza parcial. Por ejemplo, una aplicación móvil. Las asociaciones de seguridad se generan mediante una directiva, que describe las fechas de inicio y finalización durante el cual las SA es válida, así como el nivel de acceso concedido al titular SA.

En el ejemplo siguiente se genera una nueva directiva de acceso compartido que le permitirá titular SA agregar los mensajes a la cola y expira 100 minutos después de la hora en que se crea.

    var startDate = new Date();
    var expiryDate = new Date(startDate);
    expiryDate.setMinutes(startDate.getMinutes() + 100);
    startDate.setMinutes(startDate.getMinutes() - 100);

    var sharedAccessPolicy = {
      AccessPolicy: {
        Permissions: azure.QueueUtilities.SharedAccessPermissions.ADD,
        Start: startDate,
        Expiry: expiryDate
      }
    };

    var queueSAS = queueSvc.generateSharedAccessSignature('myqueue', sharedAccessPolicy);
    var host = queueSvc.host;

Tenga en cuenta que la información del host se debe proporcionar también, ya que es necesario cuando el titular SA intenta obtener acceso a la cola.

A continuación, la aplicación cliente usa las asociaciones de seguridad con **QueueServiceWithSAS** para realizar operaciones en la cola. En el ejemplo siguiente se conecta a la cola y crea un mensaje.

    var sharedQueueService = azure.createQueueServiceWithSas(host, queueSAS);
    sharedQueueService.createMessage('myqueue', 'Hello world from SAS!', function(error, result, response){
      if(!error){
        //message added
      }
    });

Dado que las asociaciones de seguridad se ha generado con el acceso de agregar, si ha intentado leer, actualizar o eliminar mensajes, se devolverá un error.

### <a name="access-control-lists"></a>Listas de control de acceso

También puede usar una lista de Control de acceso (ACL) para establecer la directiva de acceso para un SA. Esto es útil si desea permitir que varios clientes tener acceso a la cola, pero proporciona directivas de acceso diferente para cada cliente.

Una ACL se implementa mediante una matriz de directivas de acceso, con un identificador asociados con cada directiva. En el ejemplo siguiente se define dos directivas; uno para 'Usuario1' y otro para 'usuario2':

    var sharedAccessPolicy = {
      user1: {
        Permissions: azure.QueueUtilities.SharedAccessPermissions.PROCESS,
        Start: startDate,
        Expiry: expiryDate
      },
      user2: {
        Permissions: azure.QueueUtilities.SharedAccessPermissions.ADD,
        Start: startDate,
        Expiry: expiryDate
      }
    };

En el ejemplo siguiente se obtiene las ACL de **myqueue**y después suma las nuevas directivas mediante **setQueueAcl**. Este enfoque permite:

    var extend = require('extend');
    queueSvc.getQueueAcl('myqueue', function(error, result, response) {
      if(!error){
        var newSignedIdentifiers = extend(true, result.signedIdentifiers, sharedAccessPolicy);
        queueSvc.setQueueAcl('myqueue', newSignedIdentifiers, function(error, result, response){
          if(!error){
            // ACL set
          }
        });
      }
    });

Una vez que se ha establecido las ACL, a continuación, puede crear un SA según el identificador de una directiva. En el ejemplo siguiente se crea una nueva SA para 'usuario2':

    queueSAS = queueSvc.generateSharedAccessSignature('myqueue', { Id: 'user2' });

## <a name="next-steps"></a>Pasos siguientes

Ahora que ha aprendido los conceptos básicos de almacenamiento de cola, siga estos vínculos para obtener información sobre las tareas de almacenamiento más complejas.

-   Visite el [Blog del equipo de almacenamiento de Azure][].
-   Visite el repositorio de [Azure almacenamiento SDK para nodo][] en GitHub.

  [Almacenamiento de Azure SDK de nodo]: https://github.com/Azure/azure-storage-node
  [using the REST API]: http://msdn.microsoft.com/library/azure/hh264518.aspx
  [Azure Portal]: https://portal.azure.com
  [Crear una aplicación web de Node.js en la aplicación de servicio de Azure]: ../app-service-web/web-sites-nodejs-develop-deploy-mac.md
  [Node.js Cloud Service with Storage]: ../cloud-services/storage-nodejs-use-table-storage-cloud-service-app.md
  [Node.js web app con el servicio de la tabla de Azure]: ../app-service-web/storage-nodejs-use-table-storage-web-site.md


  [Queue1]: ./media/storage-nodejs-how-to-use-queues/queue1.png
  [plus-new]: ./media/storage-nodejs-how-to-use-queues/plus-new.png
  [quick-create-storage]: ./media/storage-nodejs-how-to-use-queues/quick-storage.png



  [Compile e implemente una aplicación de Node.js a un servicio de nube de Azure]: ../cloud-services/cloud-services-nodejs-develop-deploy-app.md
  [Blog del equipo de almacenamiento de Azure]: http://blogs.msdn.com/b/windowsazurestorage/
  [Compile e implemente una aplicación web de Node.js Azure con Web Matrix]: ../app-service-web/web-sites-nodejs-use-webmatrix.md
