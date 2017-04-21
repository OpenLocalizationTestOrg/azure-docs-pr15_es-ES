<properties 
    pageTitle="Cómo usar temas de Bus de servicio con Node.js | Microsoft Azure" 
    description="Obtenga información sobre cómo usar temas de Bus de servicios y suscripciones en Azure desde una aplicación de Node.js." 
    services="service-bus" 
    documentationCenter="nodejs" 
    authors="sethmanheim" 
    manager="timlt" 
    editor=""/>

<tags 
    ms.service="service-bus" 
    ms.workload="na" 
    ms.tgt_pltfrm="na" 
    ms.devlang="nodejs" 
    ms.topic="article" 
    ms.date="10/04/2016" 
    ms.author="sethm"/>


# <a name="how-to-use-service-bus-topics-and-subscriptions"></a>Cómo temas de Bus de servicio de uso y suscripciones

[AZURE.INCLUDE [service-bus-selector-topics](../../includes/service-bus-selector-topics.md)]

Esta guía describe cómo usar temas de Bus de servicio y suscripciones de Node.js aplicaciones. Los escenarios cubiertos incluyen **crear temas y suscripciones**, **crear filtros de suscripción**, **enviar mensajes** a un tema, **recibir mensajes de una suscripción**y **eliminar temas y suscripciones**. Para obtener más información sobre temas y suscripciones, vea la sección [pasos siguientes](#next-steps) .

[AZURE.INCLUDE [howto-service-bus-topics](../../includes/howto-service-bus-topics.md)]

## <a name="create-a-nodejs-application"></a>Crear una aplicación de Node.js

Crear una aplicación de Node.js en blanco. Para obtener instrucciones sobre cómo crear una aplicación de Node.js, consulte [crear e implementar una aplicación de Node.js a un sitio Web de Azure], [Servicio de nube de Node.js][] con Windows PowerShell, o el sitio Web con WebMatrix.

## <a name="configure-your-application-to-use-service-bus"></a>Configurar la aplicación para usar Bus de servicio

Para usar Bus de servicio, descargue el paquete de Node.js Azure. Este paquete incluye un conjunto de bibliotecas que comunicarse con los servicios del resto de Bus de servicio.

### <a name="use-node-package-manager-npm-to-obtain-the-package"></a>Utilice el nodo paquete Manager (NPM) para obtener el paquete

1.  Usar una interfaz de línea de comandos como **PowerShell** (Windows), **Terminal** (Mac) o **Bash** (Unix), vaya a la carpeta donde se crea la aplicación de ejemplo.

2.  Escriba **npm instalar azure** en la ventana de comandos, debe aparecer en el resultado siguiente:

    ```
        azure@0.7.5 node_modules\azure
    ├── dateformat@1.0.2-1.2.3
    ├── xmlbuilder@0.4.2
    ├── node-uuid@1.2.0
    ├── mime@1.2.9
    ├── underscore@1.4.4
    ├── validator@1.1.1
    ├── tunnel@0.0.2
    ├── wns@0.5.3
    ├── xml2js@0.2.7 (sax@0.5.2)
    └── request@2.21.0 (json-stringify-safe@4.0.0, forever-agent@0.5.0, aws-sign@0.3.0, tunnel-agent@0.3.0, oauth-sign@0.3.0, qs@0.6.5, cookie-jar@0.3.0, node-uuid@1.4.0, http-signature@0.9.11, form-data@0.0.8, hawk@0.13.1)
    ```

3.  El comando **ls** para comprobar que puede ejecutar manualmente una **nodo\_módulos** se creó la carpeta. Dentro de esa carpeta, busque el paquete de **azure** , que contiene las bibliotecas que necesite obtener acceso a los temas de Bus de servicio.

### <a name="import-the-module"></a>Importar el módulo

Con el Bloc de notas u otro editor de texto, agregue lo siguiente a la parte superior del archivo **server.js** de la aplicación:

```
var azure = require('azure');
```

### <a name="set-up-a-service-bus-connection"></a>Configurar una conexión de Bus de servicio

El módulo Azure lee las variables de entorno AZURE\_SERVICEBUS\_espacio de nombres y AZURE\_SERVICEBUS\_ACCESS\_clave para obtener información necesaria para conectarse a Bus de servicio. Si no se establecen estas variables de entorno, debe especificar la información de cuenta al llamar a **createServiceBusService**.

Para obtener un ejemplo de la configuración de las variables de entorno en un archivo de configuración para un servicio de nube de Azure, vea [Servicio de nube de Node.js con almacenamiento][].

Para obtener un ejemplo de configuración de las variables de entorno en el [portal de clásica Azure][] para un sitio Web de Azure, vea [Aplicación Web de Node.js con almacenamiento][].

## <a name="create-a-topic"></a>Crear un tema

El objeto **ServiceBusService** le permite trabajar con los temas. El código siguiente crea un objeto **ServiceBusService** . Agréguelo cerca de la parte superior del archivo **server.js** , después de la instrucción para importar el módulo azure:

```
var serviceBusService = azure.createServiceBusService();
```

Al llamar a **createTopicIfNotExists** en el objeto **ServiceBusService** , se devolverá el tema especificado (si existe) o se creará un nuevo tema con el nombre especificado. El código siguiente usa **createTopicIfNotExists** para crear o conectar con el tema denominado 'MyTopic':

```
serviceBusService.createTopicIfNotExists('MyTopic',function(error){
    if(!error){
        // Topic was created or exists
        console.log('topic created or exists.');
    }
});
```

**createServiceBusService** también es compatible con más opciones, que le permite invalidar la configuración de tema predeterminada como período de vida o el tamaño máximo de temas. En el ejemplo siguiente se establece el tamaño máximo de temas en 5GB con un tiempo de vida de 1 minuto:

```
var topicOptions = {
        MaxSizeInMegabytes: '5120',
        DefaultMessageTimeToLive: 'PT1M'
    };

serviceBusService.createTopicIfNotExists('MyTopic', topicOptions, function(error){
    if(!error){
        // topic was created or exists
    }
});
```

### <a name="filters"></a>Filtros

Las operaciones opcionales de filtrado se pueden aplicar a operaciones realizadas mediante **ServiceBusService**. Operaciones de filtrado pueden incluir registro, Reintentar automáticamente, etcetera. Filtros son objetos que implementa un método con la firma:

```
function handle (requestOptions, next)
```

Después de realizar preprocesamiento en las opciones de la solicitud, el método llama `next` pasando una devolución de llamada con la firma siguiente:

```
function (returnObject, finalCallback, next)
```

En esta devolución de llamada y después de procesar **returnObject** (la respuesta de la solicitud en el servidor), las necesidades de devolución de llamada: invocan siguiente si existe para continuar procesando otros filtros o simplemente invocar **finalCallback** en caso contrario, para terminar la llamada de servicio.

Dos filtros que implementan la lógica de reintento se incluyen en el SDK de Azure para Node.js, **ExponentialRetryPolicyFilter** y **LinearRetryPolicyFilter**. La siguiente crea un objeto **ServiceBusService** que usa el **ExponentialRetryPolicyFilter**:

    var retryOperations = new azure.ExponentialRetryPolicyFilter();
    var serviceBusService = azure.createServiceBusService().withFilter(retryOperations);

## <a name="create-subscriptions"></a>Crear suscripciones

Suscripciones de tema también se crean con el objeto **ServiceBusService** . Las suscripciones se denominan y pueden tener un filtro opcional que restringe el conjunto de mensajes entregados a cola virtual de la suscripción.

> [AZURE.NOTE] Suscripciones persistentes y seguirán existe hasta alguno o el tema que están asociadas, se eliminan. Si su aplicación contiene lógica para crear una suscripción, debe comprobar primero si la suscripción ya existe a través del método **getSubscription** .

### <a name="create-a-subscription-with-the-default-matchall-filter"></a>Crear una suscripción con el filtro predeterminado (MatchAll)

El filtro de **MatchAll** es el filtro predeterminado que se usa si no se especifica ningún filtro cuando se crea una nueva suscripción. Cuando se utiliza el filtro **MatchAll** , todos los mensajes publicados en el tema se colocan en la cola virtual de la suscripción. En el ejemplo siguiente se crea una suscripción con nombre 'AllMessages' y usa el filtro de **MatchAll** predeterminado.

```
serviceBusService.createSubscription('MyTopic','AllMessages',function(error){
    if(!error){
        // subscription created
    }
});
```

### <a name="create-subscriptions-with-filters"></a>Crear suscripciones con los filtros

También puede crear filtros que permitan para el ámbito de los mensajes enviados a un tema debe mostrarse dentro de una suscripción de un tema específico.

El tipo de filtro admitido por suscripciones más flexible es **SqlFilter**, que implementa un subconjunto de SQL92. Filtros SQL funcionan en las propiedades de los mensajes que se publican en el tema. Para obtener más detalles sobre las expresiones que se pueden usar con un filtro SQL, revise la [SqlFilter.SqlExpression] [ SqlFilter.SqlExpression] sintaxis.

Los filtros se pueden agregar a una suscripción a través del método **createRule** del objeto **ServiceBusService** . Este método le permite agregar nuevos filtros a una suscripción existente.

> [AZURE.NOTE] Porque el filtro predeterminado se aplica automáticamente a todas las suscripciones nuevas, primero debe quitar el filtro predeterminado o el **MatchAll** invalidará cualquier otro filtro que puede especificar. Puede quitar la regla predeterminada a través del método **deleteRule** del objeto **ServiceBusService** .

En el ejemplo siguiente se crea una suscripción denominada `HighMessages` con **SqlFilter** que solo selecciona mensajes que tienen una propiedad personalizada **messagenumber** superior a 3:

```
serviceBusService.createSubscription('MyTopic', 'HighMessages', function (error){
    if(!error){
        // subscription created
        rule.create();
    }
});
var rule={
    deleteDefault: function(){
        serviceBusService.deleteRule('MyTopic',
            'HighMessages', 
            azure.Constants.ServiceBusConstants.DEFAULT_RULE_NAME, 
            rule.handleError);
    },
    create: function(){
        var ruleOptions = {
            sqlExpressionFilter: 'messagenumber > 3'
        };
        rule.deleteDefault();
        serviceBusService.createRule('MyTopic', 
            'HighMessages', 
            'HighMessageFilter', 
            ruleOptions, 
            rule.handleError);
    },
    handleError: function(error){
        if(error){
            console.log(error)
        }
    }
}
```

Asimismo, en el ejemplo siguiente se crea una suscripción denominada `LowMessages` con **SqlFilter** que solo selecciona mensajes que tienen una propiedad **messagenumber** o igual a 3:

```
serviceBusService.createSubscription('MyTopic', 'LowMessages', function (error){
    if(!error){
        // subscription created
        rule.create();
    }
});
var rule={
    deleteDefault: function(){
        serviceBusService.deleteRule('MyTopic',
            'LowMessages', 
            azure.Constants.ServiceBusConstants.DEFAULT_RULE_NAME, 
            rule.handleError);
    },
    create: function(){
        var ruleOptions = {
            sqlExpressionFilter: 'messagenumber <= 3'
        };
        rule.deleteDefault();
        serviceBusService.createRule('MyTopic', 
            'LowMessages', 
            'LowMessageFilter', 
            ruleOptions, 
            rule.handleError);
    },
    handleError: function(error){
        if(error){
            console.log(error)
        }
    }
}
```

Cuando un mensaje ahora se envía a `MyTopic`, siempre se entregarán a receptores suscritos a la `AllMessages` suscripción de tema y selectivamente entregado a receptores suscritos a la `HighMessages` y `LowMessages` suscripciones tema (según el contenido del mensaje).

## <a name="how-to-send-messages-to-a-topic"></a>Cómo enviar mensajes a un tema

Para enviar un mensaje a un tema de Bus de servicio, la aplicación debe utilizar el método **sendTopicMessage** del objeto **ServiceBusService** .
Los mensajes enviados a temas de Bus de servicio están **BrokeredMessage** objetos.
Objetos de **BrokeredMessage** tienen un conjunto de propiedades estándar (como **etiqueta** y **período de vida**), un diccionario que se utiliza para contener propiedades específicas de aplicación personalizada y un cuerpo de datos de cadena. Una aplicación puede establecer el cuerpo del mensaje pasando un valor de cadena a la **sendTopicMessage** y las propiedades necesarias estándares se rellenará con valores predeterminados.

En el ejemplo siguiente se muestra cómo enviar mensajes a 'MyTopic' de cinco prueba. Tenga en cuenta que el valor de propiedad **messagenumber** de cada mensaje varía en la iteración del bucle (Esto determinará qué suscripciones reciban):

```
var message = {
    body: '',
    customProperties: {
        messagenumber: 0
    }
}

for (i = 0;i < 5;i++) {
    message.customProperties.messagenumber=i;
    message.body='This is Message #'+i;
    serviceBusService.sendTopicMessage(topic, message, function(error) {
      if (error) {
        console.log(error);
      }
    });
}
```

Temas de Bus de servicio compatible con el tamaño máximo de mensaje de 256 KB en el [nivel estándar](service-bus-premium-messaging.md) y 1 MB en el [nivel de Premium](service-bus-premium-messaging.md). El encabezado, que incluye las propiedades de la aplicación estándar y personalizados, puede tener un tamaño máximo de 64 KB. No hay ningún límite del número de mensajes en un tema, pero hay un límite en el tamaño total de los mensajes que llevan a cabo por un tema. El tamaño de este tema se define en la hora de creación, con un límite máximo de 5 GB.

## <a name="receive-messages-from-a-subscription"></a>Recibir mensajes de una suscripción

Reciben los mensajes de una suscripción con el método de **receiveSubscriptionMessage** en el objeto **ServiceBusService** . De forma predeterminada, se eliminan los mensajes de la suscripción a medida que se leen; Sin embargo, puede leer (peek) y bloquear el mensaje sin eliminarlo de la suscripción, establezca el parámetro opcional **isPeekLock** en **true**.

El comportamiento predeterminado de leer y eliminar el mensaje como parte de la operación de recepción es el modelo más sencillo y funciona mejor para escenarios en los que puede tolerar una aplicación no procesar un mensaje si se produce un error. Para comprender esto, considere la posibilidad de un escenario en el que el consumidor envía la solicitud de recepción y, a continuación, se bloquea antes de procesamiento. Dado que Bus de servicio habrá marcar el mensaje como consumida, cuando la aplicación se reinicia y empieza a utilizar de nuevo, los mensajes habrá perdido el mensaje que se ha utilizado antes el bloqueo.

Si el parámetro **isPeekLock** se establece en **true**, la recepción se convierte en una operación de dos fases, que permite a las aplicaciones de soporte técnico que no pueden tolerar mensajes que falta. Cuando Bus de servicio recibe una solicitud, encuentra el mensaje siguiente consumir, bloquea para evitar que otros consumidores recibirlo y, a continuación, se devuelve a la aplicación.
Después de la aplicación termine de procesar el mensaje (o almacena confiable para procesamiento futuro), complete la segunda fase del proceso de recepción llamando al método de **deleteMessage** y proporcionar el mensaje que se va a eliminar como parámetro. El método **deleteMessage** marcar el mensaje como consumida y quitar de la suscripción.

En el ejemplo siguiente se muestra cómo los mensajes se pueden recibir y procesar mediante **receiveSubscriptionMessage**. En el ejemplo se recibe por primera vez, elimina un mensaje de la suscripción de 'LowMessages' y, a continuación, recibe un mensaje de la suscripción de 'HighMessages' mediante **isPeekLock** establecida en true. A continuación, elimina el mensaje utilizando **deleteMessage**:

```
serviceBusService.receiveSubscriptionMessage('MyTopic', 'LowMessages', function(error, receivedMessage){
    if(!error){
        // Message received and deleted
        console.log(receivedMessage);
    }
});
serviceBusService.receiveSubscriptionMessage('MyTopic', 'HighMessages', { isPeekLock: true }, function(error, lockedMessage){
    if(!error){
        // Message received and locked
        console.log(lockedMessage);
        serviceBusService.deleteMessage(lockedMessage, function (deleteError){
            if(!deleteError){
                // Message deleted
                console.log('message has been deleted.');
            }
        }
    }
});
```

## <a name="how-to-handle-application-crashes-and-unreadable-messages"></a>Cómo controlar los mensajes no se puede leer y bloqueos de la aplicación

Bus de servicio proporciona funcionalidad para ayudarle a recuperarse correctamente de errores en la aplicación o dificultades para procesar un mensaje. Si una aplicación de receptor no puede procesar el mensaje por algún motivo, puede llamar al método **unlockMessage** en el objeto **ServiceBusService** . Esto provocará Bus de servicio desbloquear el mensaje dentro de la suscripción y que esté disponible para recibir nuevamente, por la misma aplicación consumir o por otra aplicación consume.

También es un tiempo de espera asociado a un mensaje bloqueado dentro de la suscripción, y si se produce un error en la aplicación procesar el mensaje antes el tiempo de espera de bloqueo caduca (por ejemplo, si se bloquea la aplicación), entonces Bus de servicio desbloquea el mensaje automáticamente y hace que esté disponible para volver a recibir.

En caso de que la aplicación se bloquea después de procesar el mensaje, pero antes de que se llama al método **deleteMessage** , a continuación, el mensaje se volverá a entregar a la aplicación cuando se reinicia. A menudo se denomina **Al menos una vez procesamiento**, es decir, cada mensaje se procesará al menos una vez pero en algunos casos es posible volverá a entregar el mismo mensaje. Si el escenario no puede tolerar procesamiento duplicado, los desarrolladores de aplicaciones deben agregar lógica adicional a su aplicación para controlar la entrega de mensajes duplicados. A menudo, esto se logra mediante la propiedad de **Id. de mensaje** de los mensajes, que permanecerán constante entre intentos de entrega.

## <a name="delete-topics-and-subscriptions"></a>Eliminar temas y suscripciones

Temas de suscripciones son persistentes y deben eliminarse explícitamente a través del [portal clásica Azure][] o mediante programación.
En el ejemplo siguiente se muestra cómo eliminar el tema denominado `MyTopic`:

    serviceBusService.deleteTopic('MyTopic', function (error) {
        if (error) {
            console.log(error);
        }
    });

Eliminación de un tema, también se eliminarán las suscripciones que están registradas con el tema. Suscripciones también se pueden eliminar de forma independiente. En el ejemplo siguiente se muestra cómo eliminar una suscripción denominada `HighMessages` desde el `MyTopic` tema:

    serviceBusService.deleteSubscription('MyTopic', 'HighMessages', function (error) {
        if(error) {
            console.log(error);
        }
    });

## <a name="next-steps"></a>Pasos siguientes

Ahora que ha aprendido los conceptos básicos de temas de Bus de servicio, siga estos vínculos para obtener más información.

-   Vea [colas, temas y las suscripciones][].
-   Referencia de la API para [SqlFilter][].
-   Visite el repositorio de [Azure SDK para nodo][] en GitHub.

  [Azure SDK de nodo]: https://github.com/Azure/azure-sdk-for-node
  [Portal de clásico de Azure]: https://manage.windowsazure.com
  [SqlFilter.SqlExpression]: http://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sqlfilter.sqlexpression.aspx
  [Colas, temas y suscripciones]: service-bus-queues-topics-subscriptions.md
  [SqlFilter]: http://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sqlfilter.aspx
  [Servicio de nube de Node.js]: ../cloud-services/cloud-services-nodejs-develop-deploy-app.md
  [Crear e implementar una aplicación de Node.js a un sitio Web de Azure]: ../app-service-web/web-sites-nodejs-develop-deploy-mac.md
  [Servicio de nube de Node.js con almacenamiento]: ../cloud-services/cloud-services-nodejs-develop-deploy-app.md
  [Aplicación Web de Node.js con almacenamiento]: ../cloud-services/storage-nodejs-use-table-storage-cloud-service-app.md
 
