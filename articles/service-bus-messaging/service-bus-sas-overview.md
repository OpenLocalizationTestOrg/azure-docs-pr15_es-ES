<properties
    pageTitle="Compartir información general de las firmas de Access | Microsoft Azure"
    description="¿Qué son compartidos firmas de Access, cómo funcionan y cómo usarlas de nodo, PHP y C#."
    services="service-bus"
    documentationCenter="na"
    authors="djrosanova"
    manager="timlt"
    editor=""/>

<tags
    ms.service="service-bus"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="na"
    ms.date="10/02/2016"
    ms.author="darosa;sethm"/>

# <a name="shared-access-signatures"></a>Firmas de acceso compartido

*Compartir las firmas de Access* (SA) es el mecanismo de seguridad principal para Bus de servicio, incluyendo el evento Hubs, con mensajería (colas y temas) y retransmisión mensajería. Este artículo explica cómo compartido firmas de Access, cómo funcionan y cómo usarlos de forma independiente de la plataforma.

## <a name="overview-of-sas"></a>Información general de SA

Firmas de acceso compartido son un mecanismo de autenticación basado en los valores de hash seguros SHA-256 o URI. SA es un mecanismo muy eficaces que se usa para todos los servicios de Bus de servicio. En uso real, SA tiene dos componentes: una *Directiva de acceso compartido* y una *Firma de acceso compartidos* (a menudo denominadas un *token*).

Puede encontrar información más detallada sobre las firmas de acceso compartido con Bus de servicio en [autenticación de firma de acceso compartido con Bus de servicio](service-bus-shared-access-signature-authentication.md).

## <a name="shared-access-policy"></a>Directiva de acceso compartido

Reglas para comprender sobre SA es que todo comienza con una directiva. Para cada directiva, decide en tres partes de la información: **nombre**, **ámbito**y **permisos**. El **nombre** es igual; un nombre único dentro de ese ámbito. El ámbito es bastante fácil: es el URI del recurso en cuestión. Espacio de nombres de Bus de servicio, el ámbito es el nombre de dominio completo (FQDN), como por ejemplo `https://<yournamespace>.servicebus.windows.net/`.

Los permisos disponibles para una directiva son muy fácil de entender:

  + Enviar
  + Escuchar
  + Administrar

Después de crear la directiva, se asigna una *Clave principal* y una *Clave secundaria*. Estos son claves cifrado seguras. No pierden o perder ellos: siempre estará disponibles en el [portal de Azure][]. Puede utilizar cualquiera de las teclas generadas y puede generar en cualquier momento. Sin embargo, si volver a crear o cambiar la clave principal de la directiva, se invalidarán las firmas de acceso compartido creado a partir de ella.

Cuando se crea un espacio de nombres de Bus de servicio, se crea automáticamente una directiva para todo el espacio de nombres denominado **RootManageSharedAccessKey**y esta directiva tiene todos los permisos. No inicie sesión como **raíz**, por lo que no utilice esta directiva a menos que haya una buena razón. Puede crear directivas adicionales en la ficha **Configurar** para el espacio de nombres en el portal. Es importante que un solo nivel en Bus de servicio de árbol de nota (espacio de nombres, cola, concentrador de evento, etc.) solo puede tener hasta 12 directivas adjuntas.

## <a name="shared-access-signature-token"></a>Firma de acceso compartido (token)

La propia directiva no es el token de acceso para Bus de servicio. Es el objeto del que se genera el token de acceso - utilizando la clave principal o secundario. Se genera el token cuidadosamente diseñando una cadena con el siguiente formato:

```
SharedAccessSignature sig=<signature-string>&se=<expiry>&skn=<keyName>&sr=<URL-encoded-resourceURI>
```

Donde `signature-string` es el valor de hash SHA-256 del ámbito del token (**ámbito** como se describe en la sección anterior) con un CRLF anexada y una fecha de caducidad (en segundos desde el tiempo: `00:00:00 UTC` en 1 de enero de 1970).

El hash tiene un aspecto similar al siguiente código ficticio y devuelve 32 bytes.

```
SHA-256('https://<yournamespace>.servicebus.windows.net/'+'\n'+ 1438205742)
```

Los valores de hash están en la cadena **SharedAccessSignature** para que el destinatario puede calcular el hash con los mismos parámetros para asegurarse de que devuelve el mismo resultado. El URI especifica el ámbito y el nombre de clave identifica la directiva que se utilizará para calcular el hash. Esto es importante desde la perspectiva de seguridad. Si la firma no coincide con la que calcula el destinatario (Bus de servicio), a continuación, acceso denegado. En este momento puede Asegúrese de que el remitente tuviera acceso a la clave y se debe conceder los derechos especificados en la directiva.

## <a name="generating-a-signature-from-a-policy"></a>Generar una firma de una directiva

¿Cómo se realmente hacerlo en el código? Echemos un vistazo a algunas de las siguientes opciones.

### <a name="nodejs"></a>NodeJS

```
function createSharedAccessToken(uri, saName, saKey) { 
    if (!uri || !saName || !saKey) { 
            throw "Missing required parameter"; 
        } 
    var encoded = encodeURIComponent(uri); 
    var now = new Date(); 
    var week = 60*60*24*7;
    var ttl = Math.round(now.getTime() / 1000) + week;
    var signature = encoded + '\n' + ttl; 
    var signatureUTF8 = utf8.encode(signature); 
    var hash = crypto.createHmac('sha256', saKey).update(signatureUTF8).digest('base64'); 
    return 'SharedAccessSignature sr=' + encoded + '&sig=' +  
        encodeURIComponent(hash) + '&se=' + ttl + '&skn=' + saName; 
}
``` 

### <a name="java"></a>Java

```
private static String GetSASToken(String resourceUri, String keyName, String key)
  {
      long epoch = System.currentTimeMillis()/1000L;
      int week = 60*60*24*7;
      String expiry = Long.toString(epoch + week);

      String sasToken = null;
      try {
          String stringToSign = URLEncoder.encode(resourceUri, "UTF-8") + "\n" + expiry;
          String signature = getHMAC256(key, stringToSign);
          sasToken = "SharedAccessSignature sr=" + URLEncoder.encode(resourceUri, "UTF-8") +"&sig=" +
                  URLEncoder.encode(signature, "UTF-8") + "&se=" + expiry + "&skn=" + keyName;
      } catch (UnsupportedEncodingException e) {

          e.printStackTrace();
      }

      return sasToken;
  }


public static String getHMAC256(String key, String input) {
    Mac sha256_HMAC = null;
    String hash = null;
    try {
        sha256_HMAC = Mac.getInstance("HmacSHA256");
        SecretKeySpec secret_key = new SecretKeySpec(key.getBytes(), "HmacSHA256");
        sha256_HMAC.init(secret_key);
        Encoder encoder = Base64.getEncoder();

        hash = new String(encoder.encode(sha256_HMAC.doFinal(input.getBytes("UTF-8"))));

    } catch (InvalidKeyException e) {
        e.printStackTrace();
    } catch (NoSuchAlgorithmException e) {
        e.printStackTrace();
   } catch (IllegalStateException e) {
        e.printStackTrace();
    } catch (UnsupportedEncodingException e) {
        e.printStackTrace();
    }

    return hash;
}
```

### <a name="php"></a>PHP

```
function generateSasToken($uri, $sasKeyName, $sasKeyValue) 
{ 
$targetUri = strtolower(rawurlencode(strtolower($uri))); 
$expires = time();  
$expiresInMins = 60; 
$week = 60*60*24*7;
$expires = $expires + $week; 
$toSign = $targetUri . "\n" . $expires; 
$signature = rawurlencode(base64_encode(hash_hmac('sha256',             
 $toSign, $sasKeyValue, TRUE))); 

$token = "SharedAccessSignature sr=" . $targetUri . "&sig=" . $signature . "&se=" . $expires .      "&skn=" . $sasKeyName; 
return $token; 
}
```
 
### <a name="c35"></a>C & #35;

```
private static string createToken(string resourceUri, string keyName, string key)
{
    TimeSpan sinceEpoch = DateTime.UtcNow - new DateTime(1970, 1, 1);
    var week = 60 * 60 * 24 * 7;
    var expiry = Convert.ToString((int)sinceEpoch.TotalSeconds + week);
    string stringToSign = HttpUtility.UrlEncode(resourceUri) + "\n" + expiry;
    HMACSHA256 hmac = new HMACSHA256(Encoding.UTF8.GetBytes(key));
    var signature = Convert.ToBase64String(hmac.ComputeHash(Encoding.UTF8.GetBytes(stringToSign)));
    var sasToken = String.Format(CultureInfo.InvariantCulture, "SharedAccessSignature sr={0}&sig={1}&se={2}&skn={3}", HttpUtility.UrlEncode(resourceUri), HttpUtility.UrlEncode(signature), expiry, keyName);
    return sasToken;
}
```

## <a name="using-the-shared-access-signature-at-http-level"></a>Mediante la firma de acceso compartido (en el nivel HTTP)
 
Ahora que sabe cómo crear firmas de acceso compartido para todas las entidades de Bus de servicio, está listo para realizar un HTTP POST:

```
POST https://<yournamespace>.servicebus.windows.net/<yourentity>/messages
Content-Type: application/json
Authorization: SharedAccessSignature sr=https%3A%2F%2F<yournamespace>.servicebus.windows.net%2F<yourentity>&sig=<yoursignature from code above>&se=1438205742&skn=KeyName
ContentType: application/atom+xml;type=entry;charset=utf-8
``` 
    
Recuerde que esto funciona para todo el contenido. Puede crear asociaciones de seguridad de cola, tema, suscripción, concentrador de evento o retransmisión. Si utiliza identidad por publisher para Hubs de evento, simplemente anexar `/publishers/< publisherid>`.

Si va a asignar a un remitente o un cliente un token SA, no tienen la clave directamente y no es posible deshacer la hash para obtenerla. Por lo tanto, tiene control sobre lo que pueden obtener acceso a y para el largo. Una importante que debe recordar es que si cambia la clave principal de la directiva, se invalidarán las firmas de acceso compartido creado a partir de ella.

## <a name="using-the-shared-access-signature-at-amqp-level"></a>Mediante la firma de acceso compartido (en el nivel AMQP)

En la sección anterior, ha visto cómo usar el token de SA con una solicitud de HTTP POST para enviar datos al Bus de servicio. Como ya sabe, puede tener acceso a Bus de servicio mediante la avanzada mensaje Queue protocolo (AMQP) que es el protocolo preferido que se utilizará para mejorar el rendimiento, en muchos casos. El uso de token SA con AMQP se describe en el documento [AMQP Claim-Based seguridad versión 1.0](https://www.oasis-open.org/committees/download.php/50506/amqp-cbs-v1%200-wd02%202013-08-12.doc) que está en el borrador de trabajo desde 2013 pero bien son compatibles con Azure hoy.

Antes de empezar a enviar datos a Bus de servicio, el publicador debe enviar el token de SA dentro de un mensaje de AMQP a un nodo AMQP bien definido denominado **$cbs** (puede verla como "especial" cola utilizada por el servicio para adquirir y validar los símbolos de SA). El publicador debe especificar el campo de **origen** en el mensaje AMQP; Este es el nodo en el que el servicio de respuestas en el publicador con el resultado de la validación de token (una trama simple solicitud y respuesta entre publisher y servicio). Se crea este nodo de respuesta "sobre la marcha," hablar sobre "creación dinámica de nodo remoto", como se describe en la especificación de AMQP 1.0. Después de comprobar que el token de SA es válido, el publicador puede ir hacia adelante y empiece a enviar datos al servicio.

Los pasos siguientes muestran cómo enviar el token de SA con protocolo AMQP mediante la biblioteca de [Interacción AMQP.Net](https://github.com/Azure/amqpnetlite) . Esto es útil si no puede usar el SDK oficial de Bus de servicio (por ejemplo, en WinRT, .net Compact Framework, .net Framework Micro y Mono) desarrollar en C\#. Por supuesto, esta biblioteca es útil para ayudar a comprender cómo basada en notificaciones de seguridad funciona en el nivel AMQP, tal y como hemos visto cómo funciona en el nivel HTTP (con una solicitud HTTP POST y el token de SA enviados en el encabezado "Autorización"). Si no necesita dicha profundo conocimiento AMQP, puede usar el SDK de Bus de servicio oficial con .net aplicaciones de marco de trabajo, que lo haga por usted.

### <a name="c35"></a>C & #35;

```
/// <summary>
/// Send claim-based security (CBS) token
/// </summary>
/// <param name="shareAccessSignature">Shared access signature (token) to send</param>
private bool PutCbsToken(Connection connection, string sasToken)
{
    bool result = true;
    Session session = new Session(connection);

    string cbsClientAddress = "cbs-client-reply-to";
    var cbsSender = new SenderLink(session, "cbs-sender", "$cbs");
    var cbsReceiver = new ReceiverLink(session, cbsClientAddress, "$cbs");

    // construct the put-token message
    var request = new Message(sasToken);
    request.Properties = new Properties();
    request.Properties.MessageId = Guid.NewGuid().ToString();
    request.Properties.ReplyTo = cbsClientAddress;
    request.ApplicationProperties = new ApplicationProperties();
    request.ApplicationProperties["operation"] = "put-token";
    request.ApplicationProperties["type"] = "servicebus.windows.net:sastoken";
    request.ApplicationProperties["name"] = Fx.Format("amqp://{0}/{1}", sbNamespace, entity);
    cbsSender.Send(request);

    // receive the response
    var response = cbsReceiver.Receive();
    if (response == null || response.Properties == null || response.ApplicationProperties == null)
    {
        result = false;
    }
    else
    {
        int statusCode = (int)response.ApplicationProperties["status-code"];
        if (statusCode != (int)HttpStatusCode.Accepted && statusCode != (int)HttpStatusCode.OK)
        {
            result = false;
        }
    }

    // the sender/receiver may be kept open for refreshing tokens
    cbsSender.Close();
    cbsReceiver.Close();
    session.Close();

    return result;
}
```

La `PutCbsToken()` método recibe la *conexión* (instancia de clase AMQP conexión proporcionados por la [biblioteca AMQP .NET interacción](https://github.com/Azure/amqpnetlite)) que representa la conexión de TCP al servicio y el parámetro de *sasToken* es el token de SA para enviar. 

> [AZURE.NOTE] Es importante que se crea la conexión con **mecanismo de autenticación SASL externo** (y no el formato predeterminado con el nombre de usuario y contraseña que utilizó cuando no es necesario enviar el token de SA).

A continuación, el editor crea dos vínculos AMQP para enviar el token de SA y recibir la respuesta (el resultado de validación token) del servicio.

El mensaje AMQP contiene un conjunto de propiedades y más información que un simple mensaje. El token de SA es el cuerpo del mensaje (mediante su constructor). La propiedad de **"Origen"** se establece en el nombre de nodo para recibir el resultado de validación en el vínculo de receptor (que puede cambiar su nombre si desea y, a continuación, se creará dinámicamente por el servicio). Las últimas tres propiedades de aplicación personalizada se utilizan en el servicio para indicar qué tipo de operación debe ejecutar. Como se describe en la especificación de borrador CBS, debe ser el **nombre de la operación** ("superponer-token"), el **tipo de token** (en este caso, un "servicebus.windows.net:sastoken") y el **"nombre" de la audiencia** a la que el token aplica (la entidad entera).

Después de enviar el token de SA en el vínculo del remitente, el publicador debe leer la respuesta en el vínculo de receptor. La respuesta es un simple mensaje AMQP con una propiedad de aplicación denominada **"código de estado"** que pueden contener los mismos valores como un código de estado HTTP. 

## <a name="next-steps"></a>Pasos siguientes

Vea la [referencia de la API de REST de Bus de servicio](https://msdn.microsoft.com/library/azure/hh780717.aspx) para obtener más información sobre lo que puede hacer con estos tokens SA.

Para obtener más información acerca de la autenticación de Bus de servicio, vea [Bus de servicios de autenticación y autorización](service-bus-authentication-and-authorization.md). 

Más ejemplos de SA en C# y Java Script están en [esta entrada de blog](http://developers.de/blogs/damir_dobric/archive/2013/10/17/how-to-create-shared-access-signature-for-service-bus.aspx).

[Portal de Azure]: https://portal.azure.com