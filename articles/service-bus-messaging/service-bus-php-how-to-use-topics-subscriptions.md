<properties 
    pageTitle="Cómo usar temas de Bus de servicio con PHP | Microsoft Azure" 
    description="Obtenga información sobre cómo usar temas de Bus de servicio con PHP en Azure." 
    services="service-bus" 
    documentationCenter="php" 
    authors="sethmanheim" 
    manager="timlt" 
    editor=""/>

<tags 
    ms.service="service-bus" 
    ms.workload="na" 
    ms.tgt_pltfrm="na" 
    ms.devlang="PHP" 
    ms.topic="article" 
    ms.date="10/14/2016" 
    ms.author="sethm"/>


# <a name="how-to-use-service-bus-topics-and-subscriptions"></a>Cómo utilizar las suscripciones y temas de Bus de servicio

[AZURE.INCLUDE [service-bus-selector-topics](../../includes/service-bus-selector-topics.md)]

En este artículo se muestra cómo usar las suscripciones y temas de Bus de servicio. Los ejemplos se escriben en PHP y usan el [SDK de Azure para PHP](../php-download-sdk.md). Las situaciones de ejemplo incluyen la **creación de temas y suscripciones**, **crear filtros de suscripción**, **enviar mensajes a un tema**, **recibir mensajes de una suscripción**y **eliminar temas y suscripciones**.

[AZURE.INCLUDE [howto-service-bus-topics](../../includes/howto-service-bus-topics.md)]

## <a name="create-a-php-application"></a>Crear una aplicación PHP

Es el único requisito para crear una aplicación de PHP que tiene acceso al servicio de blobs de Windows Azure referencia a las clases en el [SDK de Azure para PHP](../php-download-sdk.md) desde dentro del código. Puede usar las herramientas de desarrollo para crear la aplicación o el Bloc de notas.

> [AZURE.NOTE] La instalación de PHP también debe tener la [extensión de OpenSSL](http://php.net/openssl) instalado y activado.

En este artículo se describe cómo usar las características de servicio que se pueden llamar dentro de una aplicación PHP localmente o en el código que se ejecuta dentro de una función de Azure web, trabajo o sitio Web.

## <a name="get-the-azure-client-libraries"></a>Obtener al cliente de Azure bibliotecas

[AZURE.INCLUDE [get-client-libraries](../../includes/get-client-libraries.md)]

## <a name="configure-your-application-to-use-service-bus"></a>Configurar la aplicación para usar Bus de servicio

Para usar las API de Bus de servicio:

1. Hacer referencia al archivo cargador automático mediante la [require_once] [ require-once] instrucción.
2. Hacer referencia a las clases que puede usar.

En el ejemplo siguiente se muestra cómo incluir el archivo de cargador automático y hacer referencia a la clase **ServiceBusService** .

> [AZURE.NOTE] En este ejemplo (y otros ejemplos de este artículo) asume que ha instalado las bibliotecas de cliente de PHP para Azure a través de compositor. Si ha instalado las bibliotecas manualmente o como un paquete de PERALES, debe hacer referencia el archivo de cargador automático **WindowsAzure.php** .

```
require_once 'vendor\autoload.php';
use WindowsAzure\Common\ServicesBuilder;
```

En los ejemplos siguientes, el `require_once` instrucción siempre se muestran, pero solo las clases necesarias para ejecutar el ejemplo se hace referencia.

## <a name="set-up-a-service-bus-connection"></a>Configurar una conexión de Bus de servicio

Para crear una instancia de un cliente de Bus de servicio debe tener una cadena de conexión válida en este formato:

```
Endpoint=[yourEndpoint];SharedSecretIssuer=[Default Issuer];SharedSecretValue=[Default Key]
```

Donde `Endpoint` normalmente tiene el formato `https://[yourNamespace].servicebus.windows.net`.

Para crear a un cliente de servicio de Azure debe utilizar la clase **ServicesBuilder** . Puedes:

* Pase la cadena de conexión directamente a él.
* Utilice la **CloudConfigurationManager (CCM)** para comprobar varios orígenes externos de la cadena de conexión:
    * De forma predeterminada incluye compatibilidad para un origen externo - variables de entorno.
    * Puede agregar nuevos orígenes de ampliar la clase **ConnectionStringSource** .

Para los ejemplos descritos a continuación, la cadena de conexión se pasa directamente.

```
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;
    
$connectionString = "Endpoint=[yourEndpoint];SharedSecretIssuer=[Default Issuer];SharedSecretValue=[Default Key]";

$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);
```

## <a name="create-a-topic"></a>Crear un tema

Puede realizar operaciones de administración para temas de Bus de servicio a través de la clase **ServiceBusRestProxy** . Se crea un objeto de **ServiceBusRestProxy** a través del método de fábrica **ServicesBuilder::createServiceBusService** con una cadena de conexión apropiada encapsulado el tokens permisos para administrar.

En el ejemplo siguiente se muestra cómo crear una instancia de un **ServiceBusRestProxy** y llamar a **ServiceBusRestProxy -> createTopic** para crear un tema denominado `mytopic` dentro de un `MySBNamespace` espacio de nombres:

```
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;
use WindowsAzure\Common\ServiceException;
use WindowsAzure\ServiceBus\Models\TopicInfo;
    
// Create Service Bus REST proxy.
$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);
    
try {       
    // Create topic.
    $topicInfo = new TopicInfo("mytopic");
    $serviceBusRestProxy->createTopic($topicInfo);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here: 
    // http://msdn.microsoft.com/library/windowsazure/dd179357
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

> [AZURE.NOTE] Puede usar el `listTopics` método en `ServiceBusRestProxy` objetos para comprobar si ya existe un tema con un nombre especificado dentro de un espacio de nombres de servicio.

## <a name="create-a-subscription"></a>Crear una suscripción

Suscripciones de tema también se crean con el método **ServiceBusRestProxy -> createSubscription** . Las suscripciones se denominan y pueden tener un filtro opcional que restringe el conjunto de mensajes que se pasan a cola virtual de la suscripción.

### <a name="create-a-subscription-with-the-default-matchall-filter"></a>Crear una suscripción con el filtro predeterminado (MatchAll)

El filtro de **MatchAll** es el filtro predeterminado que se usa si no se especifica ningún filtro cuando se crea una nueva suscripción. Cuando se utiliza el filtro **MatchAll** , todos los mensajes publicados en el tema se colocan en la cola virtual de la suscripción. En el ejemplo siguiente se crea una suscripción con nombre 'mysubscription' y usa el filtro de **MatchAll** predeterminado.

```
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;
use WindowsAzure\Common\ServiceException;
use WindowsAzure\ServiceBus\Models\SubscriptionInfo;

// Create Service Bus REST proxy.
$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);
    
try {
    // Create subscription.
    $subscriptionInfo = new SubscriptionInfo("mysubscription");
    $serviceBusRestProxy->createSubscription("mytopic", $subscriptionInfo);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here: 
    // http://msdn.microsoft.com/library/azure/dd179357
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

### <a name="create-subscriptions-with-filters"></a>Crear suscripciones con los filtros

También puede configurar filtros que le permite especificar qué mensajes enviados a un tema deben aparecer dentro de una suscripción de un tema específico. El tipo de filtro admitido por suscripciones más flexible es **SqlFilter**, que implementa un subconjunto de SQL92. Filtros SQL funcionan en las propiedades de los mensajes que se publican en el tema. Para obtener más información sobre SqlFilters, vea [SqlFilter.SqlExpression (propiedad)][sqlfilter].

> [AZURE.NOTE] Cada regla en una suscripción procesa los mensajes entrantes de forma independiente, agregar sus mensajes de resultado a la suscripción. Además, cada nueva suscripción tiene un objeto de **regla** predeterminado con un filtro que agrega todos los mensajes de un tema a la suscripción. Para recibir sólo los mensajes que coincidan con el filtro, debe quitar la regla predeterminada. Puede quitar la regla predeterminada mediante la `ServiceBusRestProxy->deleteRule` método.

En el ejemplo siguiente se crea una suscripción denominada **HighMessages** con **SqlFilter** que solo selecciona los mensajes que tienen una propiedad personalizada del **MessageNumber** superior a 3 (consulte [enviar mensajes a un tema](#send-messages-to-a-topic) para obtener información sobre cómo agregar propiedades personalizadas a mensajes):

```
$subscriptionInfo = new SubscriptionInfo("HighMessages");
$serviceBusRestProxy->createSubscription("mytopic", $subscriptionInfo);

$serviceBusRestProxy->deleteRule("mytopic", "HighMessages", '$Default');

$ruleInfo = new RuleInfo("HighMessagesRule");
$ruleInfo->withSqlFilter("MessageNumber > 3");
$ruleResult = $serviceBusRestProxy->createRule("mytopic", "HighMessages", $ruleInfo);
```

Observe que este código requiere el uso de un espacio de nombres adicional: `WindowsAzure\ServiceBus\Models\SubscriptionInfo`.

Asimismo, en el ejemplo siguiente se crea una suscripción denominada **LowMessages** con **SqlFilter** que solo selecciona mensajes que tienen una propiedad **MessageNumber** o igual a 3:

```
$subscriptionInfo = new SubscriptionInfo("LowMessages");
$serviceBusRestProxy->createSubscription("mytopic", $subscriptionInfo);

$serviceBusRestProxy->deleteRule("mytopic", "LowMessages", '$Default');

$ruleInfo = new RuleInfo("LowMessagesRule");
$ruleInfo->withSqlFilter("MessageNumber <= 3");
$ruleResult = $serviceBusRestProxy->createRule("mytopic", "LowMessages", $ruleInfo);
```

Ahora, cuando se envía un mensaje a la `mytopic` tema, siempre se entrega a receptores suscritos a la `mysubscription` suscripción y selectivamente entregado a receptores suscritos a la `HighMessages` y `LowMessages` suscripciones (según el contenido del mensaje).

## <a name="send-messages-to-a-topic"></a>Enviar mensajes a un tema

Para enviar un mensaje a un tema de Bus de servicio, la aplicación llama al método **ServiceBusRestProxy -> sendTopicMessage** . El código siguiente muestra cómo enviar un mensaje a la `mytopic` tema creado previamente dentro de la `MySBNamespace` espacio de nombres de servicio.

```
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;
use WindowsAzure\Common\ServiceException;
use WindowsAzure\ServiceBus\Models\BrokeredMessage;

// Create Service Bus REST proxy.
$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);
        
try {
    // Create message.
    $message = new BrokeredMessage();
    $message->setBody("my message");
    
    // Send message.
    $serviceBusRestProxy->sendTopicMessage("mytopic", $message);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here: 
    // http://msdn.microsoft.com/library/azure/hh780775
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

Los mensajes enviados a temas de Bus de servicio son instancias de la clase **BrokeredMessage** . Objetos de **BrokeredMessage** tienen un conjunto de propiedades estándares y métodos (por ejemplo, **getLabel**, **getTimeToLive**, **setLabel**y **setTimeToLive**), así como las propiedades que se pueden usar para mantener las propiedades personalizadas de específicos de la aplicación. En el ejemplo siguiente se muestra cómo enviar mensajes de prueba 5 la `mytopic` tema creado previamente. El método **DefinirPropiedad** se usa para agregar una propiedad personalizada (`MessageNumber`) para cada mensaje. Tenga en cuenta que el `MessageNumber` valor de la propiedad varía en cada mensaje (que puede usar este valor para determinar qué suscripciones reciben, como se muestra en la sección [crear una suscripción](#create-a-subscription) ):

```
for($i = 0; $i < 5; $i++){
    // Create message.
    $message = new BrokeredMessage();
    $message->setBody("my message ".$i);
            
    // Set custom property.
    $message->setProperty("MessageNumber", $i);
            
    // Send message.
    $serviceBusRestProxy->sendTopicMessage("mytopic", $message);
}
```

Temas de Bus de servicio compatible con el tamaño máximo de mensaje de 256 KB en el [nivel estándar](service-bus-premium-messaging.md) y 1 MB en el [nivel de Premium](service-bus-premium-messaging.md). El encabezado, que incluye las propiedades de la aplicación estándar y personalizados, puede tener un tamaño máximo de 64 KB. No hay ningún límite del número de mensajes en un tema, pero hay un límite en el tamaño total de los mensajes que llevan a cabo por un tema. Este límite de tamaño de tema es 5 GB. Para obtener más información acerca de las cuotas, vea [las cuotas de Bus de servicio][].

## <a name="receive-messages-from-a-subscription"></a>Recibir mensajes de una suscripción

La mejor manera de recibir mensajes de una suscripción es usar un método **ServiceBusRestProxy -> receiveSubscriptionMessage** . Mensajes recibidos pueden trabajar en dos modos: **ReceiveAndDelete** (predeterminado) y **PeekLock**.

Al usar el modo de **ReceiveAndDelete** recibir es una operación de captura solo; es decir, cuando Bus de servicio recibe una solicitud de lectura para un mensaje en una suscripción, marca el mensaje como consumida y devuelve a la aplicación. Modo de **ReceiveAndDelete** es el modelo más sencillo y funciona mejor para escenarios en los que puede tolerar una aplicación no procesar un mensaje si se produce un error. Para comprender esto, considere la posibilidad de un escenario en el que el consumidor envía la solicitud de recepción y, a continuación, se bloquea antes de procesamiento. Dado que Bus de servicio habrá marcar el mensaje como consumida, cuando la aplicación se reinicia y empieza a utilizar de nuevo, los mensajes habrá perdido el mensaje que se ha utilizado antes el bloqueo.

En el modo de **PeekLock** , recibe un mensaje se convierte en una operación de dos fases, que permite a las aplicaciones de soporte técnico que no pueden tolerar mensajes que falta. Cuando Bus de servicio recibe una solicitud, encuentra el mensaje siguiente consumir, bloquea para evitar que otros consumidores recibirlo y, a continuación, se devuelve a la aplicación. Después de la aplicación termine de procesar el mensaje (o almacena confiable para procesamiento futuro), complete la segunda fase del proceso de recepción pasando el mensaje recibido a **ServiceBusRestProxy -> deleteMessage**. Cuando Bus de servicio detecta el **deleteMessage** de llamadas, se marca el mensaje como consumida y quitar de la cola.

En el ejemplo siguiente se muestra cómo recibir y procesar un mensaje con el modo de **PeekLock** (no en el modo de forma predeterminada). 

```
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;
use WindowsAzure\Common\ServiceException;
use WindowsAzure\ServiceBus\Models\ReceiveMessageOptions;

// Create Service Bus REST proxy.
$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);
        
try {
    // Set receive mode to PeekLock (default is ReceiveAndDelete)
    $options = new ReceiveMessageOptions();
    $options->setPeekLock();
    
    // Get message.
    $message = $serviceBusRestProxy->receiveSubscriptionMessage("mytopic", "mysubscription", $options);

    echo "Body: ".$message->getBody()."<br />";
    echo "MessageID: ".$message->getMessageId()."<br />";
        
    /*---------------------------
        Process message here.
    ----------------------------*/
        
    // Delete message. Not necessary if peek lock is not set.
    echo "Deleting message...<br />";
    $serviceBusRestProxy->deleteMessage($message);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // http://msdn.microsoft.com/library/azure/hh780735
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

## <a name="how-to-handle-application-crashes-and-unreadable-messages"></a>Cómo: controlar la aplicación se bloquea y no se puede leer mensajes

Bus de servicio proporciona funcionalidad para ayudarle a recuperarse correctamente de errores en la aplicación o dificultades para procesar un mensaje. Si una aplicación de receptor no puede procesar el mensaje por algún motivo, puede llamar al método **unlockMessage** en el mensaje recibido (en lugar de en el método **deleteMessage** ). Esto provocará Bus de servicio desbloquear el mensaje en la cola y que esté disponible para recibir nuevamente, por la misma aplicación consumir o por otra aplicación consume.

También es un tiempo de espera asociado a un mensaje bloqueado en la cola y si se produce un error en la aplicación procesar el mensaje antes el tiempo de espera de bloqueo caduca (por ejemplo, si se bloquea la aplicación), se desbloquear el mensaje automáticamente y que esté disponible para recibir nuevo Bus de servicio.

En caso de que la aplicación se bloquea después de procesar el mensaje, pero antes de que se envía la solicitud de **deleteMessage** , a continuación, el mensaje se volverá a entregar a la aplicación cuando se reinicia. A menudo se denomina **Procesamiento de al menos una vez**; es decir, cada mensaje se procesa al menos una vez, pero en algunos casos es posible volverá a entregar el mismo mensaje. Si el escenario no puede tolerar procesamiento duplicado, los desarrolladores de aplicaciones deben agregar lógica adicional a aplicaciones para controlar la entrega de mensajes duplicados. A menudo, esto se logra utilizando el método **getMessageId** del mensaje, que se mantiene constante en todos los intentos de entrega.

## <a name="delete-topics-and-subscriptions"></a>Eliminar temas y suscripciones

Para eliminar un tema o una suscripción, utilice el **ServiceBusRestProxy -> deleteTopic** o los métodos **ServiceBusRestProxy -> deleteSubscripton** , respectivamente. Tenga en cuenta que la eliminación de un tema, también se elimina cualquier suscripciones que están registradas con el tema.

En el ejemplo siguiente se muestra cómo eliminar un tema denominado `mytopic` y sus suscripciones registrados.

```
require_once 'vendor/autoload.php';

use WindowsAzure\ServiceBus\ServiceBusService;
use WindowsAzure\ServiceBus\ServiceBusSettings;
use WindowsAzure\Common\ServiceException;

// Create Service Bus REST proxy.
$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);
    
try {       
    // Delete topic.
    $serviceBusRestProxy->deleteTopic("mytopic");
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here: 
    // http://msdn.microsoft.com/library/azure/dd179357
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

A través del método de **deleteSubscription** , puede eliminar una suscripción de forma independiente:

```
$serviceBusRestProxy->deleteSubscription("mytopic", "mysubscription");
```

## <a name="next-steps"></a>Pasos siguientes

Ahora que ha aprendido los conceptos básicos de colas de Bus de servicio, vea [colas, temas y las suscripciones][] para obtener más información.

[Colas, temas y suscripciones]: service-bus-queues-topics-subscriptions.md
[sqlfilter]: http://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sqlfilter.sqlexpression.aspx
[require-once]: http://php.net/require_once
[Cuotas de Bus de servicio]: service-bus-quotas.md
