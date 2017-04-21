<properties 
    pageTitle="Cómo usar colas de Bus de servicio con PHP | Microsoft Azure" 
    description="Obtenga información sobre cómo usar colas de Bus de servicio en Azure. Ejemplos de código escritos en PHP." 
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
    ms.date="10/04/2016" 
    ms.author="sethm"/>

# <a name="how-to-use-service-bus-queues"></a>Cómo usar colas de Bus de servicio

[AZURE.INCLUDE [service-bus-selector-queues](../../includes/service-bus-selector-queues.md)]

Esta guía le muestra cómo usar colas de Bus de servicio. Los ejemplos se escriben en PHP y usan el [SDK de Azure para PHP](../php-download-sdk.md). Los escenarios cubiertos incluyen **crear colas**, **Enviar y recibir mensajes**y **eliminar colas**.

[AZURE.INCLUDE [howto-service-bus-queues](../../includes/howto-service-bus-queues.md)]

## <a name="create-a-php-application"></a>Crear una aplicación PHP

El único requisito para crear una aplicación de PHP que tiene acceso al servicio de blobs de Windows Azure es la referencia de clases en el [SDK de Azure para PHP](../php-download-sdk.md) desde dentro del código. Puede usar las herramientas de desarrollo para crear la aplicación o el Bloc de notas.

> [AZURE.NOTE] La instalación de PHP también debe tener la [extensión de OpenSSL](http://php.net/openssl) instalado y activado.

En esta guía, que usa las características de servicio que se pueden llamar desde dentro de una aplicación PHP localmente o en el código que se ejecuta dentro de una función de Azure web, trabajo o sitio Web.

## <a name="get-the-azure-client-libraries"></a>Obtener al cliente de Azure bibliotecas

[AZURE.INCLUDE [get-client-libraries](../../includes/get-client-libraries.md)]

## <a name="configure-your-application-to-use-service-bus"></a>Configurar la aplicación para usar Bus de servicio

Para usar la API de cola de Bus de servicio, haga lo siguiente:

1. Hacer referencia al archivo cargador automático mediante la [require_once] [ require_once] instrucción.
2. Hacer referencia a las clases que puede usar.

En el ejemplo siguiente se muestra cómo incluir el archivo de cargador automático y hacer referencia a la clase **ServicesBuilder** .

> [AZURE.NOTE] En este ejemplo (y otros ejemplos de este artículo) asume que ha instalado las bibliotecas de cliente de PHP para Azure a través de compositor. Si ha instalado las bibliotecas manualmente o como un paquete de PERALES, debe hacer referencia el archivo de cargador automático **WindowsAzure.php** .

```
require_once 'vendor/autoload.php';
use WindowsAzure\Common\ServicesBuilder;
```

En los ejemplos siguientes, el `require_once` instrucción siempre se muestran, pero solo las clases necesarias para ejecutar el ejemplo se hace referencia.

## <a name="set-up-a-service-bus-connection"></a>Configurar una conexión de Bus de servicio

Para crear una instancia de un cliente de Bus de servicio, primero debe tener una cadena de conexión válida en este formato:

```
Endpoint=[yourEndpoint];SharedSecretIssuer=[Default Issuer];SharedSecretValue=[Default Key]
```

Donde suele ser **extremo** del formato `[yourNamespace].servicebus.windows.net`.

Para crear a un cliente de servicio de Azure debe utilizar la clase **ServicesBuilder** . Puedes:

* Pase la cadena de conexión directamente a él.
* Utilice la **CloudConfigurationManager (CCM)** para comprobar varios orígenes externos de la cadena de conexión:
    * De forma predeterminada se suministra con soporte para un origen externo - variables de entorno
    * Puede agregar nuevos orígenes de ampliar la clase **ConnectionStringSource**

Para los ejemplos descritos a continuación, se pasará directamente la cadena de conexión.

```
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;

$connectionString = "Endpoint=[yourEndpoint];SharedSecretIssuer=[Default Issuer];SharedSecretValue=[Default Key]";

$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);
```

## <a name="how-to-create-a-queue"></a>Cómo: crear una cola

Puede realizar operaciones de administración de colas de Bus de servicio a través de la clase **ServiceBusRestProxy** . Se crea un objeto de **ServiceBusRestProxy** a través del método de fábrica **ServicesBuilder::createServiceBusService** con una cadena de conexión apropiada encapsulado el tokens permisos para administrar.

En el ejemplo siguiente se muestra cómo crear una instancia de un **ServiceBusRestProxy** y llamar a **ServiceBusRestProxy -> createQueue** para crear una cola denominada `myqueue` dentro de un `MySBNamespace` espacio de nombres de servicio:

```
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;
use WindowsAzure\Common\ServiceException;
use WindowsAzure\ServiceBus\Models\QueueInfo;

// Create Service Bus REST proxy.
$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);
    
try {
    $queueInfo = new QueueInfo("myqueue");
        
    // Create queue.
    $serviceBusRestProxy->createQueue($queueInfo);
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

> [AZURE.NOTE] Puede usar el `listQueues` método en `ServiceBusRestProxy` objetos para comprobar si ya existe una cola con un nombre especificado dentro de un espacio de nombres.

## <a name="how-to-send-messages-to-a-queue"></a>Cómo: enviar mensajes a una cola

Para enviar un mensaje a una cola de Bus de servicio, la aplicación llama al método **ServiceBusRestProxy -> sendQueueMessage** . El código siguiente muestra cómo enviar un mensaje a la `myqueue` cola creado previamente dentro de la `MySBNamespace` espacio de nombres de servicio.

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
    $serviceBusRestProxy->sendQueueMessage("myqueue", $message);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here: 
    // http://msdn.microsoft.com/library/windowsazure/hh780775
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

Mensajes enviadas a (y recibidas desde) colas de Bus de servicio son instancias de la clase **BrokeredMessage** . Objetos de **BrokeredMessage** tienen un conjunto de métodos estándar (como **getLabel**, **getTimeToLive**, **setLabel**y **setTimeToLive**) y las propiedades que se utilizan para mantener las propiedades personalizadas de específicos de la aplicación y un conjunto de datos de aplicación arbitrario.

Colas de Bus de servicio compatible con el tamaño máximo de mensaje de 256 KB en el [nivel estándar](service-bus-premium-messaging.md) y 1 MB en el [nivel de Premium](service-bus-premium-messaging.md). El encabezado, que incluye las propiedades de la aplicación estándar y personalizados, puede tener un tamaño máximo de 64 KB. No hay ningún límite del número de mensajes de una cola, pero hay un límite en el tamaño total de los mensajes que llevan a cabo una cola. Este límite de tamaño de la cola es 5 GB.

## <a name="how-to-receive-messages-from-a-queue"></a>Cómo recibir mensajes de una cola

La mejor manera de recibir mensajes de una cola es usar un método **ServiceBusRestProxy -> receiveQueueMessage** . Se pueden recibir mensajes de dos modos diferentes: **ReceiveAndDelete** (predeterminado) y **PeekLock**.

Al usar el modo de **ReceiveAndDelete** recibir es una operación de captura solo - es decir, cuando Bus de servicio recibe una solicitud de lectura para un mensaje de una cola, marca el mensaje como consumida y devuelve a la aplicación. Modo de **ReceiveAndDelete** es el modelo más sencillo y funciona mejor para escenarios en los que puede tolerar una aplicación no procesar un mensaje si se produce un error. Para comprender esto, considere la posibilidad de un escenario en el que el consumidor envía la solicitud de recepción y, a continuación, se bloquea antes de procesamiento. Dado que Bus de servicio habrá marcar el mensaje como consumida, cuando la aplicación se reinicia y empieza a utilizar de nuevo, los mensajes habrá perdido el mensaje que se ha utilizado antes el bloqueo.

En el modo de **PeekLock** , recibe un mensaje se convierte en una operación de dos fases, que permite a las aplicaciones de soporte técnico que no pueden tolerar mensajes que falta. Cuando Bus de servicio recibe una solicitud, encuentra el mensaje siguiente para consumir, lo bloquea para impedir que otros consumidores recibiendo y, a continuación, se devuelve a la aplicación. Después de la aplicación termine de procesar el mensaje (o almacena confiable para procesamiento futuro), complete la segunda fase del proceso de recepción pasando el mensaje recibido a **ServiceBusRestProxy -> deleteMessage**. Cuando Bus de servicio detecta el **deleteMessage** de llamadas, se marca el mensaje como consumida y quitar de la cola.

En el ejemplo siguiente se muestra cómo un mensaje se pueden recibir y procesa utilizando el modo de **PeekLock** (no en el modo de forma predeterminada).

```
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;
use WindowsAzure\Common\ServiceException;
use WindowsAzure\ServiceBus\Models\ReceiveMessageOptions;

// Create Service Bus REST proxy.
$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);
        
try {
    // Set the receive mode to PeekLock (default is ReceiveAndDelete).
    $options = new ReceiveMessageOptions();
    $options->setPeekLock();
        
    // Receive message.
    $message = $serviceBusRestProxy->receiveQueueMessage("myqueue", $options);
    echo "Body: ".$message->getBody()."<br />";
    echo "MessageID: ".$message->getMessageId()."<br />";
        
    /*---------------------------
        Process message here.
    ----------------------------*/
        
    // Delete message. Not necessary if peek lock is not set.
    echo "Message deleted.<br />";
    $serviceBusRestProxy->deleteMessage($message);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // http://msdn.microsoft.com/library/windowsazure/hh780735
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

## <a name="how-to-handle-application-crashes-and-unreadable-messages"></a>Cómo: controlar la aplicación se bloquea y no se puede leer mensajes

Bus de servicio proporciona funcionalidad para ayudarle a recuperarse correctamente de errores en la aplicación o dificultades para procesar un mensaje. Si una aplicación de receptor no puede procesar el mensaje por algún motivo, puede llamar al método **unlockMessage** en el mensaje recibido (en lugar de en el método **deleteMessage** ). Esto provocará Bus de servicio desbloquear el mensaje en la cola y que esté disponible para recibir nuevamente, por la misma aplicación consumir o por otra aplicación consume.

También es un tiempo de espera asociado a un mensaje bloqueado en la cola y si se produce un error en la aplicación procesar el mensaje antes el tiempo de espera de bloqueo caduca (por ejemplo, si se bloquea la aplicación), se desbloquear el mensaje automáticamente y que esté disponible para recibir nuevo Bus de servicio.

En caso de que la aplicación se bloquea después de procesar el mensaje, pero antes de que se envía la solicitud de **deleteMessage** , a continuación, el mensaje se volverá a entregar a la aplicación cuando se reinicia. A menudo se denomina **Procesamiento de al menos una vez**; es decir, cada mensaje se procesa al menos una vez, pero en algunos casos es posible volverá a entregar el mismo mensaje. Si el escenario no puede tolerar procesamiento duplicado, a continuación, agregar lógica adicional a aplicaciones para controlar la entrega de mensajes duplicados se recomienda. A menudo, esto se logra utilizando el método **getMessageId** del mensaje, que se mantiene constante en todos los intentos de entrega.

## <a name="next-steps"></a>Pasos siguientes

Ahora que ha aprendido los conceptos básicos de colas de Bus de servicio, vea [colas, temas y las suscripciones][] para obtener más información.

Para obtener más información, vea también el [Centro para desarrolladores de PHP](/develop/php/).

[Colas, temas y suscripciones]: service-bus-queues-topics-subscriptions.md
[require_once]: http://php.net/require_once

 
