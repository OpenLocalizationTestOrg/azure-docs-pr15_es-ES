<properties 
    pageTitle="Cómo usar notificación Hubs con Python" 
    description="Aprenda a usar Hubs de notificación de Azure desde un Python back-end." 
    services="notification-hubs" 
    documentationCenter="" 
    authors="ysxu"
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="notification-hubs" 
    ms.workload="mobile" 
    ms.tgt_pltfrm="python" 
    ms.devlang="php" 
    ms.topic="article" 
    ms.date="06/29/2016" 
    ms.author="yuaxu"/>

# <a name="how-to-use-notification-hubs-from-python"></a>Cómo usar Hubs de notificación de Python
[AZURE.INCLUDE [notification-hubs-backend-how-to-selector](../../includes/notification-hubs-backend-how-to-selector.md)]
        
Puede obtener acceso a todas las características de notificación Hubs desde un fondo Java o PHP/Python o Ruby mediante la interfaz REST de concentrador de notificación como se describe en el tema MSDN [API de REST de Hubs de notificación](http://msdn.microsoft.com/library/dn223264.aspx).

> [AZURE.NOTE] Esto es un ejemplo de implementación de referencia para implementar el envía notificación en Python y no el SDK de Python de concentrador de notificaciones oficialmente compatible.
>
> En este ejemplo se escribe utilizando Python 3.4.

En este tema le mostraremos cómo:

* Crear a un cliente de resto para las características de notificación Hubs en Python.
* Enviar notificaciones mediante la interfaz de Python a las API de REST de concentrador de notificación. 
* Obtener una descarga de la solicitud de HTTP resto y respuesta a fin de depuración educativos. 

Puede seguir el [tutorial de introducción de obtener](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md) para su plataforma móvil de elección, implementación de la parte de back-end en Python.

> [AZURE.NOTE] El ámbito de la muestra solo está limitado para enviar notificaciones y no ninguna administración del registro.

## <a name="client-interface"></a>Interfaz de cliente
La interfaz de cliente principal puede proporcionar los mismos métodos que están disponibles en el [SDK de .NET notificación Hubs](http://msdn.microsoft.com/library/jj933431.aspx). Esto le permitirá traducir directamente todos los tutoriales y ejemplos están disponibles en este sitio y proporcionada por la Comunidad de internet.

Puede encontrar el código disponible en el [resto de Python muestra de contenedor].

Por ejemplo, para crear a un cliente:

    isDebug = True
    hub = NotificationHub("myConnectionString", "myNotificationHubName", isDebug)
    
Para enviar una notificación de Windows:
    
    wns_payload = """<toast><visual><binding template=\"ToastText01\"><text id=\"1\">Hello world!</text></binding></visual></toast>"""
    hub.send_windows_notification(wns_payload)
    
## <a name="implementation"></a>Implementación
Si no ha hecho, siga nuestro [tutorial de introducción de obtener] hasta la última sección donde tiene para implementar el back-end.

Todos los detalles para implementar un contenedor de resto completo se pueden encontrar en [MSDN](http://msdn.microsoft.com/library/dn530746.aspx). En esta sección describiremos la implementación de Python de los pasos principales necesarios para tener acceso a los extremos de notificación Hubs resto y enviar notificaciones

1. Analizar la cadena de conexión
2. Generar el token de autorización
3. Enviar una notificación con la API de REST de HTTP

### <a name="parse-the-connection-string"></a>Analizar la cadena de conexión

Aquí es la clase principal para implementar al cliente, cuyo constructor analiza la cadena de conexión:

    class NotificationHub:
        API_VERSION = "?api-version=2013-10"
        DEBUG_SEND = "&test"
    
        def __init__(self, connection_string=None, hub_name=None, debug=0):
            self.HubName = hub_name
            self.Debug = debug
    
            # Parse connection string
            parts = connection_string.split(';')
            if len(parts) != 3:
                raise Exception("Invalid ConnectionString.")
    
            for part in parts:
                if part.startswith('Endpoint'):
                    self.Endpoint = 'https' + part[11:]
                if part.startswith('SharedAccessKeyName'):
                    self.SasKeyName = part[20:]
                if part.startswith('SharedAccessKey'):
                    self.SasKeyValue = part[16:]


### <a name="create-security-token"></a>Crear el token de seguridad
Los detalles de la creación de token de seguridad están disponibles [aquí](http://msdn.microsoft.com/library/dn495627.aspx).
Tienen los siguientes métodos para agregarse a la clase **NotificationHub** para crear el token basado en el URI de la solicitud actual y las credenciales extraídas de la cadena de conexión.

    @staticmethod
    def get_expiry():
        # By default returns an expiration of 5 minutes (=300 seconds) from now
        return int(round(time.time() + 300))

    @staticmethod
    def encode_base64(data):
        return base64.b64encode(data)

    def sign_string(self, to_sign):
        key = self.SasKeyValue.encode('utf-8')
        to_sign = to_sign.encode('utf-8')
        signed_hmac_sha256 = hmac.HMAC(key, to_sign, hashlib.sha256)
        digest = signed_hmac_sha256.digest()
        encoded_digest = self.encode_base64(digest)
        return encoded_digest

    def generate_sas_token(self):
        target_uri = self.Endpoint + self.HubName
        my_uri = urllib.parse.quote(target_uri, '').lower()
        expiry = str(self.get_expiry())
        to_sign = my_uri + '\n' + expiry
        signature = urllib.parse.quote(self.sign_string(to_sign))
        auth_format = 'SharedAccessSignature sig={0}&se={1}&skn={2}&sr={3}'
        sas_token = auth_format.format(signature, expiry, self.SasKeyName, my_uri)
        return sas_token

### <a name="send-a-notification-using-http-rest-api"></a>Enviar una notificación con la API de REST de HTTP
Usar en primer lugar, permiten definir una clase que representa una notificación.

    class Notification:
        def __init__(self, notification_format=None, payload=None, debug=0):
            valid_formats = ['template', 'apple', 'gcm', 'windows', 'windowsphone', "adm", "baidu"]
            if not any(x in notification_format for x in valid_formats):
                raise Exception(
                    "Invalid Notification format. " +
                    "Must be one of the following - 'template', 'apple', 'gcm', 'windows', 'windowsphone', 'adm', 'baidu'")
    
            self.format = notification_format
            self.payload = payload
    
            # array with keynames for headers
            # Note: Some headers are mandatory: Windows: X-WNS-Type, WindowsPhone: X-NotificationType
            # Note: For Apple you can set Expiry with header: ServiceBusNotification-ApnsExpiry
            # in W3C DTF, YYYY-MM-DDThh:mmTZD (for example, 1997-07-16T19:20+01:00).
            self.headers = None

Esta clase es un contenedor para el cuerpo de una notificación nativo o un conjunto de propiedades en el caso de una notificación de plantilla, un conjunto de encabezados que contiene formato (plataforma nativa o plantilla) y propiedades específicas de la plataforma (como la propiedad de caducidad de Apple y encabezados WNS).

Consulte la [documentación de la API de REST de Hubs de notificación](http://msdn.microsoft.com/library/dn495827.aspx) y formatos de plataformas son de la notificación específica para todas las opciones disponibles.

Ahora con esta clase, podemos escribir el enviar métodos de notificación dentro de la clase **NotificationHub** .

    def make_http_request(self, url, payload, headers):
        parsed_url = urllib.parse.urlparse(url)
        connection = http.client.HTTPSConnection(parsed_url.hostname, parsed_url.port)

        if self.Debug > 0:
            connection.set_debuglevel(self.Debug)
            # adding this querystring parameter gets detailed information about the PNS send notification outcome
            url += self.DEBUG_SEND
            print("--- REQUEST ---")
            print("URI: " + url)
            print("Headers: " + json.dumps(headers, sort_keys=True, indent=4, separators=(' ', ': ')))
            print("--- END REQUEST ---\n")

        connection.request('POST', url, payload, headers)
        response = connection.getresponse()

        if self.Debug > 0:
            # print out detailed response information for debugging purpose
            print("\n\n--- RESPONSE ---")
            print(str(response.status) + " " + response.reason)
            print(response.msg)
            print(response.read())
            print("--- END RESPONSE ---")

        elif response.status != 201:
            # Successful outcome of send message is HTTP 201 - Created
            raise Exception(
                "Error sending notification. Received HTTP code " + str(response.status) + " " + response.reason)

        connection.close()

    def send_notification(self, notification, tag_or_tag_expression=None):
        url = self.Endpoint + self.HubName + '/messages' + self.API_VERSION

        json_platforms = ['template', 'apple', 'gcm', 'adm', 'baidu']

        if any(x in notification.format for x in json_platforms):
            content_type = "application/json"
            payload_to_send = json.dumps(notification.payload)
        else:
            content_type = "application/xml"
            payload_to_send = notification.payload

        headers = {
            'Content-type': content_type,
            'Authorization': self.generate_sas_token(),
            'ServiceBusNotification-Format': notification.format
        }

        if isinstance(tag_or_tag_expression, set):
            tag_list = ' || '.join(tag_or_tag_expression)
        else:
            tag_list = tag_or_tag_expression

        # add the tags/tag expressions to the headers collection
        if tag_list != "":
            headers.update({'ServiceBusNotification-Tags': tag_list})

        # add any custom headers to the headers collection that the user may have added
        if notification.headers is not None:
            headers.update(notification.headers)

        self.make_http_request(url, payload_to_send, headers)

    def send_apple_notification(self, payload, tags=""):
        nh = Notification("apple", payload)
        self.send_notification(nh, tags)

    def send_gcm_notification(self, payload, tags=""):
        nh = Notification("gcm", payload)
        self.send_notification(nh, tags)

    def send_adm_notification(self, payload, tags=""):
        nh = Notification("adm", payload)
        self.send_notification(nh, tags)

    def send_baidu_notification(self, payload, tags=""):
        nh = Notification("baidu", payload)
        self.send_notification(nh, tags)

    def send_mpns_notification(self, payload, tags=""):
        nh = Notification("windowsphone", payload)

        if "<wp:Toast>" in payload:
            nh.headers = {'X-WindowsPhone-Target': 'toast', 'X-NotificationClass': '2'}
        elif "<wp:Tile>" in payload:
            nh.headers = {'X-WindowsPhone-Target': 'tile', 'X-NotificationClass': '1'}

        self.send_notification(nh, tags)

    def send_windows_notification(self, payload, tags=""):
        nh = Notification("windows", payload)

        if "<toast>" in payload:
            nh.headers = {'X-WNS-Type': 'wns/toast'}
        elif "<tile>" in payload:
            nh.headers = {'X-WNS-Type': 'wns/tile'}
        elif "<badge>" in payload:
            nh.headers = {'X-WNS-Type': 'wns/badge'}

        self.send_notification(nh, tags)

    def send_template_notification(self, properties, tags=""):
        nh = Notification("template", properties)
        self.send_notification(nh, tags)

Los métodos anteriores envían una solicitud HTTP POST al extremo /messages de su centro de notificación, con el cuerpo correcto y los encabezados para enviar la notificación.

### <a name="using-debug-property-to-enable-detailed-logging"></a>Mediante la propiedad de depuración para habilitar el registro detallado
Habilitar la propiedad de depuración durante la inicialización del concentrador de notificación escribirá información de registro de información detallada sobre la solicitud HTTP y descarga de respuesta, así como mensaje de notificación detallado Enviar resultado. Hemos agregado recientemente esta propiedad denominada [notificación Hubs TestSend propiedad](http://msdn.microsoft.com/library/microsoft.servicebus.notifications.notificationhubclient.enabletestsend.aspx) que devuelve información detallada sobre el resultado de enviar notificación. Al usarlo - inicializar el uso de las siguientes acciones:

    hub = NotificationHub("myConnectionString", "myNotificationHubName", isDebug)

La solicitud de notificación concentrador enviar dirección URL de HTTP obtiene anexa con una cadena de consulta "prueba" como resultado. 

##<a name="complete-tutorial"></a>Completar el tutorial
Ahora puede completar el tutorial de introducción mediante el envío de la notificación de un Python back-end.

Inicializar al cliente de notificación Hubs (sustituir el nombre de concentrador y la cadena de conexión siguiendo las instrucciones en el [tutorial de introducción de Get]):

    hub = NotificationHub("myConnectionString", "myNotificationHubName")

A continuación, agregue el código de envío dependiendo de la plataforma móvil de destino. En este ejemplo también agrega los métodos de nivel superior para habilitar las notificaciones envío en función de la plataforma, por ejemplo, send_windows_notification para windows; send_apple_notification (para apple) etcetera. 

### <a name="windows-store-and-windows-phone-81-non-silverlight"></a>Tienda de Windows y Windows Phone 8.1 (no de Silverlight)

    wns_payload = """<toast><visual><binding template=\"ToastText01\"><text id=\"1\">Test</text></binding></visual></toast>"""
    hub.send_windows_notification(wns_payload)

### <a name="windows-phone-80-and-81-silverlight"></a>Windows Phone 8.0 y 8.1 Silverlight

    hub.send_mpns_notification(toast)

### <a name="ios"></a>iOS

    alert_payload = {
        'data':
            {
                'msg': 'Hello!'
            }
    }
    hub.send_apple_notification(alert_payload)

### <a name="android"></a>Android
    gcm_payload = {
        'data':
            {
                'msg': 'Hello!'
            }
    }
    hub.send_gcm_notification(gcm_payload)

### <a name="kindle-fire"></a>Kindle Fire
    adm_payload = {
        'data':
            {
                'msg': 'Hello!'
            }
    }
    hub.send_adm_notification(adm_payload)

### <a name="baidu"></a>Baidu
    baidu_payload = {
        'data':
            {
                'msg': 'Hello!'
            }
    }
    hub.send_baidu_notification(baidu_payload)

Ejecutar el código Python debe producir una notificación que aparecen en el dispositivo de destino.

## <a name="examples"></a>Ejemplos:

### <a name="enabling-debug-property"></a>Habilitar depuración (propiedad)
Cuando se habilita el indicador de depuración durante la inicialización del NotificationHub verá detallada solicitud HTTP y la descarga de respuesta, así como NotificationOutcome parecido al siguiente donde pueden comprender qué son los encabezados HTTP se pasan en la convocatoria y respuesta HTTP que ha recibido del concentrador de notificación de:    ![][1]

Verá detallada resultado concentrador de notificación p. ej. 

- Cuando el mensaje se envía correctamente al servicio de notificaciones de inserción. 
    
        <Outcome>The Notification was successfully sent to the Push Notification System</Outcome>

- Si no hay ningún destino encontrado para ninguna notificación de inserción, a continuación, es probable que va a aparecer el siguiente en la respuesta (que indica que no había ningún registro encontrado para enviar la notificación probablemente porque los registros tenían algunas etiquetas que no coincidentes)

        '<NotificationOutcome xmlns="http://schemas.microsoft.com/netservices/2010/10/servicebus/connect" xmlns:i="http://www.w3.org/2001/XMLSchema-instance"><Success>0</Success><Failure>0</Failure><Results i:nil="true"/></NotificationOutcome>'

### <a name="broadcast-toast-notification-to-windows"></a>Difusión de notificación de Windows 

Observe los encabezados que se envían a cuando envíe una notificación de difusión al cliente de Windows. 

    hub.send_windows_notification(wns_payload)

![][2]

### <a name="send-notification-specifying-a-tag-or-tag-expression"></a>Enviar una notificación que especifica una etiqueta (o expresión de etiqueta)

Observe el encabezado de HTTP de etiquetas que se agrega a la solicitud HTTP (en el ejemplo siguiente, nos estamos enviando la notificación solo a los registros con carga 'deportes')

    hub.send_windows_notification(wns_payload, "sports")

![][3]

### <a name="send-notification-specifying-multiple-tags"></a>Enviar notificación especificar varias etiquetas

Observe cómo se cambia el encabezado etiquetas HTTP cuando se envían varias etiquetas. 
    
    tags = {'sports', 'politics'}
    hub.send_windows_notification(wns_payload, tags)

![][4]

### <a name="templated-notification"></a>Notificación de plantilla

Observe que los cambios de formato HTTP encabezado y el cuerpo de la carga se envía como parte del cuerpo de la solicitud HTTP:

**Cliente - plantilla registrado**

        var template =
                        @"<toast><visual><binding template=""ToastText01""><text id=""1"">$(greeting_en)</text></binding></visual></toast>";
            
**Servidor - enviar la carga**
        
        template_payload = {'greeting_en': 'Hello', 'greeting_fr': 'Salut'}
        hub.send_template_notification(template_payload)

![][5]


## <a name="next-steps"></a>Pasos siguientes
En este tema mostramos cómo crear a un cliente de Python resto simple para Hubs de notificación. Desde aquí puede:

* Descargue el completo [ejemplo de contenedor del resto de Python], que contiene el código anterior.
* Seguir aprendiendo sobre Hubs notificación etiquetado característica en el [tutorial de noticias de hora]
* Continuar aprendiendo sobre la característica de plantillas de Hubs de notificación en el [tutorial de localización de noticias]

<!-- URLs -->
[Ejemplo de contenedor del resto de Python]: https://github.com/Azure/azure-notificationhubs-samples/tree/master/notificationhubs-rest-python
[Tutorial de introducción de Get]: http://azure.microsoft.com/documentation/articles/notification-hubs-windows-store-dotnet-get-started/
[Romper el tutorial de noticias]: http://azure.microsoft.com/documentation/articles/notification-hubs-windows-store-dotnet-send-breaking-news/
[Localizar el tutorial de noticias]: http://azure.microsoft.com/documentation/articles/notification-hubs-windows-store-dotnet-send-localized-breaking-news/

<!-- Images. -->
[1]: ./media/notification-hubs-python-backend-how-to/DetailedLoggingInfo.png
[2]: ./media/notification-hubs-python-backend-how-to/BroadcastScenario.png
[3]: ./media/notification-hubs-python-backend-how-to/SendWithOneTag.png
[4]: ./media/notification-hubs-python-backend-how-to/SendWithMultipleTags.png
[5]: ./media/notification-hubs-python-backend-how-to/TemplatedNotification.png
 
