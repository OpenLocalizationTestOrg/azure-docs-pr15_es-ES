<properties
    pageTitle="Cómo usar Twilio para voz y SMS (PHP) | Microsoft Azure"
    description="Obtenga información sobre cómo realizar una llamada telefónica y enviar un mensaje SMS con el servicio de la API Twilio en Azure. Ejemplos de código escritos en PHP."
    services=""
    documentationCenter="python"
    authors="devinrader"
    manager="twilio"
    editor=""/>

<tags
    ms.service="multiple"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="python" 
    ms.topic="article"
    ms.date="02/19/2015"
    ms.author="MicrosoftHelp@twilio.com"/>





# <a name="how-to-use-twilio-for-voice-and-sms-capabilities-in-php"></a>Cómo usar Twilio para la voz y las capacidades SMS en PHP
Esta guía muestra cómo realizar tareas comunes de programación con el servicio de la API Twilio en Azure. Los escenarios cubiertos incluyen realizar una llamada de teléfono y enviar un mensaje de servicio de mensajes cortos (SMS). Para obtener más información sobre Twilio y el uso de voz y SMS en las aplicaciones, consulte la sección [Pasos siguientes](#NextSteps) .

## <a id="WhatIs"></a>¿Qué es Twilio?
Twilio está activando el futuro de comunicaciones de empresa, lo que permite a los desarrolladores incrustar voz, VoIP y la mensajería en las aplicaciones. Virtualización de todos los infraestructura necesaria en un entorno basado en la nube, global, exponer a través de la plataforma de comunicaciones API de Twilio. Aplicaciones son sencillos crear y scalable. Disfrute de la flexibilidad con pago a medida que vaya precios y beneficiarse de confiabilidad de la nube.

**Voz Twilio** permite a las aplicaciones realizar y recibir llamadas telefónicas. **Twilio SMS** permite a su aplicación enviar y recibir mensajes de texto. **Cliente de Twilio** le permite hacer llamadas VoIP desde cualquier teléfono, una tableta o un explorador y es compatible con WebRTC.

## <a id="Pricing"></a>Twilio precios y ofertas especiales

Clientes de Azure reciban [oferta especial] $10 de crédito Twilio al actualizar su cuenta de Twilio. Este crédito Twilio se puede aplicar a cualquier uso Twilio (crédito de $10 equivalente al enviar mensajes SMS como 1.000 o recibir hasta 1000 minutos de voz entrantes, dependiendo de la ubicación de destino de mensaje o llamada de teléfono). Canjear este crédito Twilio y empezar a trabajar en: [ahoy.twilio.com/azure].

Twilio es un servicio de pago. No hay ningún costes de configuración y puede cerrar su cuenta en cualquier momento. Puede encontrar más detalles en [Precios Twilio] [twilio_pricing].

## <a id="Concepts"></a>Conceptos
La API de Twilio es una API de REST que proporciona la voz y la funcionalidad de SMS de aplicaciones. Bibliotecas de cliente están disponibles en varios idiomas; Para obtener una lista, vea [Bibliotecas de API de Twilio] [twilio_libraries].

Aspectos clave de la API Twilio son verbos Twilio y lenguaje de marcado de Twilio (TwiML).

### <a id="Verbs"></a>Verbos de Twilio
La API emplea Twilio verbos; Por ejemplo, el ** &lt;diga&gt; ** verbo indica que Twilio audible entregar un mensaje en una llamada.

A continuación se muestra una lista de verbos Twilio. Obtenga más información sobre los demás verbos y capacidades mediante [documentación de lenguaje de marcado de Twilio] [http://www.twilio.com/docs/api/twiml].

* ** &lt;Marcado&gt;**: se conecta la llamada a otro teléfono.
* ** &lt;Recopilar&gt;**: recopila los dígitos numéricos del teclado del teléfono.
* ** &lt;Colgar&gt;**: finaliza una llamada.
* ** &lt;Reproducir&gt;**: se reproduce un archivo de audio.
* ** &lt;Pausa&gt;**: espera silenciosamente durante un número especificado de segundos.
* ** &lt;Registro&gt;**: registra la voz del llamador y devuelve una dirección URL de un archivo que contiene la grabación.
* ** &lt;Redirigir&gt;**: transfiere el control de una llamada de SMS a la TwiML en una dirección URL diferente.
* ** &lt;Rechazar&gt;**: rechaza una llamada entrante a su número de Twilio sin factura
* ** &lt;Diga&gt;**: convierte texto a voz que se realiza una llamada.
* ** &lt;Sms&gt;**: envía un mensaje de SMS.

### <a id="TwiML"></a>TwiML
TwiML es un conjunto de instrucciones basado en XML, en función de los verbos Twilio que informan a Twilio de cómo procesar una llamada o SMS.

Como ejemplo, el siguiente TwiML ¿convertir el texto **Hola a todos** en voz.

    <?xml version="1.0" encoding="UTF-8" ?>
    <Response>
       <Say>Hello World</Say>
    </Response>

Cuando la aplicación llama a la API Twilio, uno de los parámetros de la API es la dirección URL que devuelve la respuesta TwiML. Con fines de desarrollo, puede utilizar direcciones URL siempre Twilio para proporcionar las respuestas de TwiML usadas por las aplicaciones. También puede hospedar su propia URL para generar las respuestas de TwiML y, a continuación, otra opción es utilizar el objeto **TwiMLResponse** .

Para obtener más información sobre Twilio verbos, sus atributos y TwiML, consulte [TwiML] [twiml]. Para obtener información adicional acerca de la API Twilio, vea [API de Twilio] [twilio_api].

## <a id="CreateAccount"></a>Crear una cuenta de Twilio
Cuando esté listo para obtener una cuenta de Twilio, suscribirse al [Intentar Twilio] [try_twilio]. Puede empezar con una cuenta gratuita y actualizar su cuenta más adelante.

Cuando se suscribe a una cuenta de Twilio, recibe un identificador de cuenta y un símbolo de autenticación. Ambos será necesario para realizar llamadas de la API de Twilio. Para evitar el acceso no autorizado a su cuenta, mantener el token de autenticación segura. Su ID de cuenta y la autenticación token están visibles en la [página de la cuenta de Twilio] [twilio_account], en los campos etiquetados **SID de la cuenta** y **El TOKEN de autenticación**, respectivamente.

## <a id="create_app"></a>Crear una aplicación PHP
Una aplicación de PHP que usa el servicio de Twilio y se ejecuta en Azure no es diferente de cualquier otra aplicación de PHP que usa el servicio de Twilio. Mientras Twilio servicios basan en el resto y se pueden llamar desde PHP de varias formas, en este artículo se centrará en cómo usar servicios de Twilio con [Twilio biblioteca para PHP desde GitHub][twilio_php]. Para obtener más información sobre el uso de la biblioteca de Twilio para PHP, consulte [http://readthedocs.org/docs/twilio-php/en/latest/index.html][twilio_lib_docs].

Instrucciones detalladas para crear e implementar una aplicación de Twilio y PHP en Azure están disponibles en [cómo realizar una llamada telefónica usando de Twilio en una aplicación PHP en Azure][howto_phonecall_php].

## <a id="configure_app"></a>Configurar la aplicación para usar bibliotecas de Twilio
Puede configurar la aplicación para usar la biblioteca Twilio para PHP de dos maneras:

1. Descargar la biblioteca Twilio para PHP desde GitHub ([https://github.com/twilio/twilio-php][twilio_php]) y agregue el directorio de **Servicios** a la aplicación.

    -O-

2. Instalar la biblioteca de Twilio para PHP como un paquete de PERALES. Puede instalarse con los siguientes comandos:

        $ pear channel-discover twilio.github.com/pear
        $ pear install twilio/Services_Twilio

Una vez haya instalado la biblioteca Twilio para PHP, a continuación, puede agregar una instrucción **require_once** en la parte superior de los archivos PHP hacer referencia a la biblioteca:

        require_once 'Services/Twilio.php';

Para obtener más información, consulte [https://github.com/twilio/twilio-php/blob/master/README.md][twilio_github_readme].

## <a id="howto_make_call"></a>Cómo: realizar una llamada de salida
La siguiente imagen muestra cómo realizar una llamada mediante la clase **Services_Twilio** . Este código también utiliza un sitio de Twilio le ha proporcionado para devolver la respuesta de lenguaje de marcado de Twilio (TwiML). Sustituir valores para los números de teléfono **desde** y **hasta** y asegúrese de que compruebe el número **de** teléfono para su cuenta de Twilio antes de ejecutar el código.

    // Include the Twilio PHP library.
    require_once 'Services/Twilio.php';

    // Library version.
    $version = "2010-04-01";

    // Set your account ID and authentication token.
    $sid = "your_twilio_account_sid";
    $token = "your_twilio_authentication_token";

    // The number of the phone initiating the the call.
    // (Must be previously validated with Twilio.)
    $from_number = "NNNNNNNNNNN";

    // The number of the phone receiving call.
    $to_number = "NNNNNNNNNNN";

    // Use the Twilio-provided site for the TwiML response.
    $url = "http://twimlets.com/message";

    // The phone message text.
    $message = "Hello world.";

    // Create the call client.
    $client = new Services_Twilio($sid, $token, $version);

    //Make the call.
    try
    {
        $call = $client->account->calls->create(
            $from_number,
            $to_number,
            $url.'?Message='.urlencode($message)
        );
    }
    catch (Exception $e)
    {
        echo 'Error: ' . $e->getMessage();
    }

Como se mencionó, este código utiliza un sitio de Twilio le ha proporcionado para devolver la respuesta TwiML. En su lugar puede utilizar su propio sitio para proporcionar la respuesta TwiML; Para obtener más información, vea [cómo proporcionar las respuestas TwiML desde su propio sitio Web](#howto_provide_twiml_responses).


- **Nota**: para solucionar errores de validación de certificado SSL, consulte [http://readthedocs.org/docs/twilio-php/en/latest/usage/rest.html][ssl_validation]


## <a id="howto_send_sms"></a>Cómo: enviar un mensaje SMS
La siguiente imagen muestra cómo enviar un mensaje SMS mediante la clase **Services_Twilio** . Proporciona el número **de** Twilio para las cuentas de prueba para enviar mensajes SMS. Es necesario comprobar **el número** de la cuenta de Twilio antes de ejecutar el código.

    // Include the Twilio PHP library.
    require_once 'Services/Twilio.php';

    // Library version.
    $version = "2010-04-01";

    // Set your account ID and authentication token.
    $sid = "your_twilio_account_sid";
    $token = "your_twilio_authentication_token";


    $from_number = "NNNNNNNNNNN"; // With trial account, texts can only be sent from your Twilio number.
    $to_number = "NNNNNNNNNNN";
    $message = "Hello world.";

    // Create the call client.
    $client = new Services_Twilio($sid, $token, $version);

    // Send the SMS message.
    try
    {
        $client->account->sms_messages->create($from_number, $to_number, $message);
    }
    catch (Exception $e)
    {
        echo 'Error: ' . $e->getMessage();
    }

## <a id="howto_provide_twiml_responses"></a>Cómo: proporcionar respuestas TwiML desde su propio sitio Web
Cuando la aplicación inicia una llamada a la API Twilio, Twilio enviará su solicitud a una dirección URL que se espera para devolver una respuesta de TwiML. El ejemplo anterior utiliza la dirección URL siempre Twilio [http://twimlets.com/message][twimlet_message_url]. (Aunque TwiML está diseñado para su uso por Twilio, puede ver la TI en el explorador. Por ejemplo, haga clic en [http://twimlets.com/message] [ twimlet_message_url] para ver una vacía `<Response>` elemento; Por ejemplo, haga clic en [http://twimlets.com/message?Message%5B0%5D=Hello%20World] [ twimlet_message_url_hello_world] para ver una `<Response>` elemento que contiene un `<Say>` elemento.)

En lugar de confiar en la dirección URL Twilio le ha proporcionado, puede crear su propio sitio que devuelve respuestas HTTP. Puede crear el sitio en cualquier idioma que devuelve las respuestas XML; en este tema se supone que va a usar PHP para crear la TwiML.

La siguiente página PHP da como resultado una respuesta de TwiML que dice **Hola a todos** en la llamada.

    <?php
        header("content-type: text/xml");
        echo "<?xml version=\"1.0\" encoding=\"UTF-8\"?>\n";
    ?>
    <Response>
        <Say>Hello world.</Say>
    </Response>

Como puede ver en el ejemplo anterior, la respuesta TwiML es simplemente un documento XML. La biblioteca de Twilio para PHP contiene clases que generarán TwiML para usted. El ejemplo siguiente genera la respuesta equivalente, como se indicó anteriormente, pero utiliza la **servicios\_Twilio\_Twiml** clase en la biblioteca de Twilio para PHP:

    require_once('Services/Twilio.php');

    $response = new Services_Twilio_Twiml();
    $response->say("Hello world.");
    print $response;

Para obtener más información sobre TwiML, consulte [https://www.twilio.com/docs/api/twiml][twiml_reference].

Una vez que la página PHP configurada para proporcionar TwiML respuestas, usar la dirección URL de la página PHP como la dirección URL pasa a la `Services_Twilio->account->calls->create` método. Por ejemplo, si tiene una aplicación Web denominada **MyTwiML** se implementa en un Azure hospedado servicio y el nombre de la página PHP es **mytwiml.php**, puede pasar a la dirección URL **Services_Twilio -> cuenta -> llamadas -> crear** tal como se muestra en el ejemplo siguiente:

    require_once 'Services/Twilio.php';

    $sid = "your_twilio_account_sid";
    $token = "your_twilio_authentication_token";
    $from_number = "NNNNNNNNNNN";
    $to_number = "NNNNNNNNNNN";
    $url = "http://<your_hosted_service>.cloudapp.net/MyTwiML/mytwiml.php";

    // The phone message text.
    $message = "Hello world.";

    $client = new Services_Twilio($sid, $token, "2010-04-01");

    try
    {
        $call = $client->account->calls->create(
            $from_number,
            $to_number,
            $url.'?Message='.urlencode($message)
        );
    }
    catch (Exception $e)
    {
        echo 'Error: ' . $e->getMessage();
    }

Para obtener información adicional sobre el uso de Twilio en Azure con PHP, vea [cómo realizar una llamada telefónica usando de Twilio en una aplicación PHP en Azure][howto_phonecall_php].

## <a id="AdditionalServices"></a>Cómo: usar servicios de Twilio adicionales
Además de los ejemplos que se muestra aquí, Twilio ofrece API basadas en web que puede usar para aprovechar la funcionalidad de Twilio adicional desde la aplicación de Azure. Para obtener más detalles, consulte la [documentación de la API de Twilio] [twilio_api_documentation].

## <a id="NextSteps"></a>Pasos siguientes
Ahora que ha aprendido los conceptos básicos del servicio Twilio, siga estos vínculos para obtener más información:

* [Directrices de seguridad de Twilio] [twilio_security_guidelines]
* [Cómo Twilio guías y código de ejemplo] [twilio_howtos]
* [Tutoriales de Twilio][twilio_quickstarts]
* [Twilio en GitHub] [twilio_on_github]
* [Póngase en contacto con soporte técnico de Twilio] [twilio_support]

[twilio_php]: https://github.com/twilio/twilio-php
[twilio_lib_docs]: http://readthedocs.org/docs/twilio-php/en/latest/index.html
[twilio_github_readme]: https://github.com/twilio/twilio-php/blob/master/README.md
[ssl_validation]: http://readthedocs.org/docs/twilio-php/en/latest/usage/rest.html

[howto_phonecall_php]: http://windowsazure.com/documentation/articles/partner-twilio-php-make-phone-call
[twimlet_message_url]: http://twimlets.com/message
[twimlet_message_url_hello_world]: http://twimlets.com/message?Message%5B0%5D=Hello%20World
[twiml_reference]: https://www.twilio.com/docs/api/twiml
[twilio_pricing]: http://www.twilio.com/pricing

[twilio_libraries]: https://www.twilio.com/docs/libraries
[twiml]: http://www.twilio.com/docs/api/twiml
[twilio_api]: http://www.twilio.com/api
[try_twilio]: https://www.twilio.com/try-twilio
[twilio_account]:  https://www.twilio.com/user/account
[verify_phone]: https://www.twilio.com/user/account/phone-numbers/verified#
[twilio_api_documentation]: http://www.twilio.com/api
[twilio_security_guidelines]: http://www.twilio.com/docs/security
[twilio_howtos]: http://www.twilio.com/docs/howto
[twilio_on_github]: https://github.com/twilio
[twilio_support]: http://www.twilio.com/help/contact
[twilio_quickstarts]: http://www.twilio.com/docs/quickstart
