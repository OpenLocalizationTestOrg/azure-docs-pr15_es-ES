<properties 
    pageTitle="Cómo usar notificación Hubs con PHP" 
    description="Aprenda a usar Hubs de notificación de Azure desde un PHP back-end." 
    services="notification-hubs" 
    documentationCenter="" 
    authors="ysxu" 
    manager="dwrede" 
    editor=""/>

<tags 
    ms.service="notification-hubs" 
    ms.workload="mobile" 
    ms.tgt_pltfrm="php" 
    ms.devlang="php" 
    ms.topic="article" 
    ms.date="06/07/2016" 
    ms.author="yuaxu"/>

# <a name="how-to-use-notification-hubs-from-php"></a>Cómo usar Hubs de notificación de PHP
[AZURE.INCLUDE [notification-hubs-backend-how-to-selector](../../includes/notification-hubs-backend-how-to-selector.md)]

Puede obtener acceso a todas las características de notificación Hubs desde un fondo Java, PHP o Ruby mediante la interfaz REST de concentrador de notificación como se describe en el tema MSDN [API de REST de Hubs de notificación](http://msdn.microsoft.com/library/dn223264.aspx).

En este tema le mostraremos cómo:

* Generar a un cliente del resto para las características de notificación Hubs en PHP;
* Siga el [tutorial de introducción de obtener](notification-hubs-ios-apple-push-notification-apns-get-started.md) para su plataforma móvil de elección, implementación de la parte de back-end en PHP.

## <a name="client-interface"></a>Interfaz de cliente
La interfaz de cliente principal puede proporcionar los mismos métodos que están disponibles en el [SDK de .NET notificación Hubs](http://msdn.microsoft.com/library/jj933431.aspx), podrá traducir directamente todos los tutoriales y ejemplos están disponibles en este sitio y ha contribuido la Comunidad de internet.

Puede encontrar el código disponible en el [resto de PHP ejemplo de contenedor].

Por ejemplo, para crear a un cliente:

    $hub = new NotificationHub("connection string", "hubname"); 

Para enviar un iOS notificación nativa:
    
    $notification = new Notification("apple", '{"aps":{"alert": "Hello!"}}');
    $hub->sendNotification($notification, null);

## <a name="implementation"></a>Implementación
Si no ha hecho, siga nuestro [tutorial de introducción de obtener] hasta la última sección donde tiene para implementar el back-end.
Además, si lo desea puede utilizar el código de la [muestra de contenedor del resto de PHP] y vaya directamente a la sección [completado el tutorial](#complete-tutorial) .

Todos los detalles para implementar un contenedor de resto completo se pueden encontrar en [MSDN](http://msdn.microsoft.com/library/dn530746.aspx). En esta sección describiremos la implementación de PHP de los pasos principales necesarios para tener acceso a los extremos del resto de Hubs de notificación:

1. Analizar la cadena de conexión
2. Generar el token de autorización
3. Realizar la llamada HTTP

### <a name="parse-the-connection-string"></a>Analizar la cadena de conexión

Aquí es la clase principal para implementar al cliente, cuyo constructor que analiza la cadena de conexión:

    class NotificationHub {
        const API_VERSION = "?api-version=2013-10";
    
        private $endpoint;
        private $hubPath;
        private $sasKeyName;
        private $sasKeyValue;
    
        function __construct($connectionString, $hubPath) {
            $this->hubPath = $hubPath;
    
            $this->parseConnectionString($connectionString);
        }
    
        private function parseConnectionString($connectionString) {
            $parts = explode(";", $connectionString);
            if (sizeof($parts) != 3) {
                throw new Exception("Error parsing connection string: " . $connectionString);
            }
    
            foreach ($parts as $part) {
                if (strpos($part, "Endpoint") === 0) {
                    $this->endpoint = "https" . substr($part, 11);
                } else if (strpos($part, "SharedAccessKeyName") === 0) {
                    $this->sasKeyName = substr($part, 20);
                } else if (strpos($part, "SharedAccessKey") === 0) {
                    $this->sasKeyValue = substr($part, 16);
                }
            }
        }
    }


### <a name="create-security-token"></a>Crear el token de seguridad
Los detalles de la creación de token de seguridad están disponibles [aquí](http://msdn.microsoft.com/library/dn495627.aspx).
El método siguiente debe agregarse a la clase de **NotificationHub** para crear el token basado en el URI de la solicitud actual y las credenciales extraídas de la cadena de conexión.

    private function generateSasToken($uri) {
        $targetUri = strtolower(rawurlencode(strtolower($uri)));

        $expires = time();
        $expiresInMins = 60;
        $expires = $expires + $expiresInMins * 60;
        $toSign = $targetUri . "\n" . $expires;

        $signature = rawurlencode(base64_encode(hash_hmac('sha256', $toSign, $this->sasKeyValue, TRUE)));

        $token = "SharedAccessSignature sr=" . $targetUri . "&sig="
                    . $signature . "&se=" . $expires . "&skn=" . $this->sasKeyName;

        return $token;
    }

### <a name="send-a-notification"></a>Enviar una notificación
En primer lugar, deje que nosotros definir una clase que representa una notificación.

    class Notification {
        public $format;
        public $payload;
    
        # array with keynames for headers
        # Note: Some headers are mandatory: Windows: X-WNS-Type, WindowsPhone: X-NotificationType
        # Note: For Apple you can set Expiry with header: ServiceBusNotification-ApnsExpiry in W3C DTF, YYYY-MM-DDThh:mmTZD (for example, 1997-07-16T19:20+01:00).
        public $headers;
    
        function __construct($format, $payload) {
            if (!in_array($format, ["template", "apple", "windows", "gcm", "windowsphone"])) {
                throw new Exception('Invalid format: ' . $format);
            }
    
            $this->format = $format;
            $this->payload = $payload;
        }
    }

Esta clase es un contenedor de cuerpo nativo de notificación, o un conjunto de propiedades en el caso de una notificación de la plantilla y un conjunto de encabezados que contiene formato (plataforma nativa o plantilla) y propiedades específicas de la plataforma (como la propiedad de caducidad de Apple y encabezados WNS).

Consulte la [documentación de la API de REST de Hubs de notificación](http://msdn.microsoft.com/library/dn495827.aspx) y formatos de plataformas son de la notificación específica para todas las opciones disponibles.

Contar con esta clase, ahora podemos escribir el enviar métodos de notificación dentro de la clase **NotificationHub** .

    public function sendNotification($notification, $tagsOrTagExpression="") {
        if (is_array($tagsOrTagExpression)) {
            $tagExpression = implode(" || ", $tagsOrTagExpression);
        } else {
            $tagExpression = $tagsOrTagExpression;
        }

        # build uri
        $uri = $this->endpoint . $this->hubPath . "/messages" . NotificationHub::API_VERSION;
        $ch = curl_init($uri);

        if (in_array($notification->format, ["template", "apple", "gcm"])) {
            $contentType = "application/json";
        } else {
            $contentType = "application/xml";
        }

        $token = $this->generateSasToken($uri);

        $headers = [
            'Authorization: '.$token,
            'Content-Type: '.$contentType,
            'ServiceBusNotification-Format: '.$notification->format
        ];

        if ("" !== $tagExpression) {
            $headers[] = 'ServiceBusNotification-Tags: '.$tagExpression;
        }

        # add headers for other platforms
        if (is_array($notification->headers)) {
            $headers = array_merge($headers, $notification->headers);
        }
        
        curl_setopt_array($ch, array(
            CURLOPT_POST => TRUE,
            CURLOPT_RETURNTRANSFER => TRUE,
            CURLOPT_SSL_VERIFYPEER => FALSE,
            CURLOPT_HTTPHEADER => $headers,
            CURLOPT_POSTFIELDS => $notification->payload
        ));

        // Send the request
        $response = curl_exec($ch);

        // Check for errors
        if($response === FALSE){
            throw new Exception(curl_error($ch));
        }

        $info = curl_getinfo($ch);

        if ($info['http_code'] <> 201) {
            throw new Exception('Error sending notificaiton: '. $info['http_code'] . ' msg: ' . $response);
        }
    } 

Los métodos anteriores envían una solicitud HTTP POST al extremo /messages de su centro de notificación, con el cuerpo correcto y los encabezados para enviar la notificación.

##<a name="complete-tutorial"></a>Completar el tutorial
Ahora puede completar el tutorial de introducción mediante el envío de la notificación de un PHP back-end.

Inicializar al cliente de notificación Hubs (sustituir el nombre de concentrador y la cadena de conexión siguiendo las instrucciones en el [tutorial de introducción de Get]):

    $hub = new NotificationHub("connection string", "hubname"); 

A continuación, agregue el código de envío dependiendo de la plataforma móvil de destino.

### <a name="windows-store-and-windows-phone-81-non-silverlight"></a>Tienda de Windows y Windows Phone 8.1 (no de Silverlight)

    $toast = '<toast><visual><binding template="ToastText01"><text id="1">Hello from PHP!</text></binding></visual></toast>';
    $notification = new Notification("windows", $toast);
    $notification->headers[] = 'X-WNS-Type: wns/toast';
    $hub->sendNotification($notification, null);

### <a name="ios"></a>iOS

    $alert = '{"aps":{"alert":"Hello from PHP!"}}';
    $notification = new Notification("apple", $alert);
    $hub->sendNotification($notification, null);

### <a name="android"></a>Android
    $message = '{"data":{"msg":"Hello from PHP!"}}';
    $notification = new Notification("gcm", $message);
    $hub->sendNotification($notification, null);

### <a name="windows-phone-80-and-81-silverlight"></a>Windows Phone 8.0 y 8.1 Silverlight

    $toast = '<?xml version="1.0" encoding="utf-8"?>' .
                '<wp:Notification xmlns:wp="WPNotification">' .
                   '<wp:Toast>' .
                        '<wp:Text1>Hello from PHP!</wp:Text1>' .
                   '</wp:Toast> ' .
                '</wp:Notification>';
    $notification = new Notification("windowsphone", $toast);
    $notification->headers[] = 'X-WindowsPhone-Target : toast';
    $notification->headers[] = 'X-NotificationClass : 2';
    $hub->sendNotification($notification, null);


### <a name="kindle-fire"></a>Kindle Fire
    $message = '{"data":{"msg":"Hello from PHP!"}}';
    $notification = new Notification("adm", $message);
    $hub->sendNotification($notification, null);

Ejecutar el código PHP debe producir ahora una notificación que aparecen en el dispositivo de destino.


## <a name="next-steps"></a>Pasos siguientes
En este tema mostramos cómo crear a un cliente de Java resto simple para Hubs de notificación. Desde aquí puede:

* Descargue el completo [ejemplo de contenedor del resto de PHP], que contiene el código anterior.
* Seguir aprendiendo sobre Hubs notificación etiquetado característica en [tutorial de noticias de hora]
* Obtenga más información sobre la presión notificaciones a usuarios individuales en [tutorial informar a los usuarios]

Para obtener más información, vea también el [Centro para desarrolladores de PHP](/develop/php/).

[Ejemplo de contenedor del resto de PHP]: https://github.com/Azure/azure-notificationhubs-samples/tree/master/notificationhubs-rest-php
[Tutorial de introducción de Get]: http://azure.microsoft.com/documentation/articles/notification-hubs-ios-get-started/
 
