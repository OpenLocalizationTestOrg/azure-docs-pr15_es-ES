<properties
    pageTitle="Cómo realizar una llamada telefónica desde Twilio (PHP) | Microsoft Azure"
    description="Obtenga información sobre cómo realizar una llamada telefónica y enviar un mensaje SMS con el servicio de la API Twilio en Azure. Son ejemplos de aplicación PHP."
    documentationCenter="php"
    services=""
    authors="devinrader"
    manager="twilio"
    editor="mollybos"/>

<tags
    ms.service="multiple"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="PHP"
    ms.topic="article"
    ms.date="11/25/2014"
    ms.author="microsofthelp@twilio.com"/>

# <a name="how-to-make-a-phone-call-using-twilio-in-a-php-application-on-azure"></a>Cómo hacer una llamada de teléfono con Twilio en una aplicación PHP en Azure

En el ejemplo siguiente se muestra cómo puede usar Twilio para hacer una llamada de una página web PHP hospedado en Azure. La aplicación resultante solicitará al usuario para los valores de la llamada de teléfono, como se muestra en la siguiente captura de pantalla.

![Formulario de Azure llamada mediante Twilio y PHP][twilio_php]

Debe hacer lo siguiente para usar el código de este tema:

1. Adquirir una cuenta de Twilio y la autenticación de token. Para empezar con Twilio, evaluar precios en [http://www.twilio.com/pricing][twilio_pricing]. Puede registrarse para una cuenta de prueba en [https://www.twilio.com/try-twilio][try_twilio]. Obtenga información acerca de la API proporcionada por Twilio [http://www.twilio.com/api][twilio_api].
2. Obtener la [biblioteca de Twilio para PHP](https://github.com/twilio/twilio-php) o instalar un paquete de PERALES. Para obtener más información, vea el [archivo Léame](https://github.com/twilio/twilio-php/blob/master/README.md).
3. Instale el SDK de Azure para PHP. Para obtener información general de los SDK e instrucciones sobre cómo instalarlo, consulte [Configurar el SDK de Azure para PHP][setup_php_sdk].

## <a name="create-a-web-form-for-making-a-call"></a>Crear un formulario web para realizar una llamada

El siguiente código HTML muestra cómo crear una página web (**callform.html**) que recupera datos de usuario para realizar una llamada:

    <html>
    <head>
        <title>Automated call form</title>
    </head>
    <body>
    <h1>Automated Call Form</h1>
    <p>Fill in all fields and click <b>Make this call</b>.</p>
    <form action="makecall.php" method="post">
    <table>
        <tr>
            <td>To:</td>
            <td><input type="text" size=50 name="callTo" value=""></td>
        </tr>
        <tr>
            <td>From:</td>
            <td><input type="text" size=50 name="callFrom" value=""></td>
        </tr>
        <tr>
            <td>Call message:</td>
            <td><input type="text" size=100 name="callText" value="Hello. This is the call text. Good bye." /></td>
        </tr>
        <tr>
            <td colspan=2><input type="submit" value="Make this call"></td>
        </tr>
    </table>
    </form>
    <br/>
    </body>
    </html>

## <a name="create-the-code-to-make-the-call"></a>Crear el código para realizar la llamada
El código siguiente muestra cómo crear una página web (**makecall.php**) que se llama cuando el usuario envía el formulario que se muestran de forma **callform.html**. El código siguiente crea el mensaje de llamada y genera la llamada. (Utilice su cuenta de Twilio y la autenticación de token en lugar de los valores de marcador de posición asignados a **$sid** y **$token** en el siguiente código).

    <html>
    <head><title>Making call...</title></head>
    <body>
    <p>Your call is being made.</p>

    <?php
    require_once 'Services/Twilio.php';

    $sid = "your_account_sid";
    $token = "your_authentication_token";

    $from_number = $_POST['callFrom']; // Calls must be made from a registered Twilio number.
    $to_number = $_POST['callTo'];
    $message = $_POST['callText'];

    $client = new Services_Twilio($sid, $token, "2010-04-01");

    $call = $client->account->calls->create(
        $from_number,
        $to_number,
        'http://twimlets.com/message?Message='.urlencode($message)
    );

    echo "Call status: ".$call->status."<br />";
    echo "URI resource: ".$call->uri."<br />";
    ?>
    </body>
    </html>

Además de la llamada, **makecall.php** muestra algunos metadatos de llamada (se muestra en la siguiente captura de pantalla de ejemplo). Para obtener más información acerca de los metadatos de la llamada, consulte [https://www.twilio.com/docs/api/rest/call#instance-properties][twilio_call_properties].

![Respuesta de llamada Azure mediante Twilio y PHP][twilio_php_response]

## <a name="run-the-application"></a>Ejecute la aplicación
El siguiente paso es implementar la aplicación a los sitios Web de Azure. Los artículos siguientes contienen la información para crear un sitio Web e implementar el código con Git, FTP o WebMatrix (aunque no toda la información de cada artículo es relevante):

* [Crear un sitio Web de Azure de MySQL PHP e implementar con Git][website-git]
* [Crear un sitio de Azure Web PHP MySQL e implementar mediante FTP][website-ftp]

## <a name="next-steps"></a>Pasos siguientes
Este código se proporcionó para mostrar la funcionalidad básica con Twilio en PHP en Azure. Antes de implementar a Azure en producción, desea agregar más control de errores u otras características. Por ejemplo:

* En lugar de usar un formulario web, podría usar blobs de Azure almacenamiento o base de datos SQL para almacenar números de teléfono y llame a texto. Para obtener información sobre el uso de blobs de Azure almacenamiento de PHP, vea [Usar el almacenamiento de Azure con aplicaciones de PHP][howto_blob_storage_php]. Para obtener información sobre cómo usar la base de datos SQL en PHP, consulte [Uso de base de datos de SQL con aplicaciones de PHP][howto_sql_azure_php].
* El código de **makecall.php** utiliza la dirección URL siempre Twilio ([http://twimlets.com/message][twimlet_message_url]) para proporcionar una respuesta de lenguaje de marcado de Twilio (TwiML) que informa Twilio cómo proceder con la llamada. Por ejemplo, puede contener el TwiML que devuelve una `<Say>` verbo que da como resultado texto entera al destinatario de la llamada. En lugar de usar la dirección URL Twilio le ha proporcionado, puede crear su propio servicio para responder a la solicitud de Twilio; Para obtener más información, vea [cómo usar Twilio para la voz y las capacidades de SMS en PHP][howto_twilio_voice_sms_php]. Encontrará más información sobre TwiML en [http://www.twilio.com/docs/api/twiml][twiml]y obtener más información sobre `<Say>` y otros verbos Twilio se pueden encontrar en [http://www.twilio.com/docs/api/twiml/say][twilio_say].
* Lea las instrucciones de seguridad Twilio [https://www.twilio.com/docs/security][twilio_docs_security].

Para obtener información adicional sobre Twilio, consulte [https://www.twilio.com/docs][twilio_docs].

## <a name="see-also"></a>Vea también
* [Cómo usar Twilio para la voz y las capacidades SMS en PHP](partner-twilio-php-how-to-use-voice-sms.md)

[twilio_pricing]: http://www.twilio.com/pricing
[try_twilio]: http://www.twilio.com/try-twilio
[twilio_api]: http://www.twilio.com/api
[verify_phone]: https://www.twilio.com/user/account/phone-numbers/verified#
[setup_php_sdk]: http://azurephp.interoperabilitybridges.com/articles/setup-the-windows-azure-sdk-for-php
[twimlet_message_url]: http://twimlets.com/message
[twiml]: http://www.twilio.com/docs/api/twiml
[twilio_api_service]: http://api.twilio.com
[build_php_azure_app]: http://azurephp.interoperabilitybridges.com/articles/build-and-deploy-a-windows-azure-php-application
[howto_twilio_voice_sms_php]: partner-twilio-php-how-to-use-voice-sms.md
[howto_blob_storage_php]: http://azure.microsoft.com/documentation/articles/storage-php-how-to-use-blobs/
[howto_sql_azure_php]: http://azure.microsoft.com/documentation/articles/sql-database-php-how-to-use/
[twilio_call_properties]: https://www.twilio.com/docs/api/rest/call#instance-properties
[twilio_docs_security]: http://www.twilio.com/docs/security
[twilio_docs]: http://www.twilio.com/docs
[twilio_say]: http://www.twilio.com/docs/api/twiml/say
[ssl_validation]: http://readthedocs.org/docs/twilio-php/en/latest/usage/rest.html
[twilio_php]: ./media/partner-twilio-php-make-phone-call/WA_TwilioPHPCallForm.jpg
[twilio_php_response]: ./media/partner-twilio-php-make-phone-call/WA_TwilioPHPMakeCall.jpg
[website-git]: ./web-sites/web-sites-php-mysql-deploy-use-git.md
[website-ftp]: ./web-sites/web-sites-php-mysql-deploy-use-ftp.md
[twilio_php_github]: https://github.com/twilio/twilio-php
