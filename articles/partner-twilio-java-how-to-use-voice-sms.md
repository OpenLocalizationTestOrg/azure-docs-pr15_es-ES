<properties 
    pageTitle="Cómo usar Twilio para voz y SMS (Java) | Microsoft Azure" 
    description="Obtenga información sobre cómo realizar una llamada telefónica y enviar un mensaje SMS con el servicio de la API Twilio en Azure. Ejemplos de código escritos en Java." 
    services="" 
    documentationCenter="java" 
    authors="devinrader" 
    manager="twilio" 
    editor="mollybos"/>

<tags 
    ms.service="multiple" 
    ms.workload="na" 
    ms.tgt_pltfrm="na" 
    ms.devlang="Java" 
    ms.topic="article" 
    ms.date="11/25/2014" 
    ms.author="microsofthelp@twilio.com"/>

# <a name="how-to-use-twilio-for-voice-and-sms-capabilities-in-java"></a>Cómo usar Twilio para la voz y las capacidades SMS en Java

Esta guía muestra cómo realizar tareas comunes de programación con el servicio de la API Twilio en Azure. Los escenarios cubiertos incluyen realizar una llamada de teléfono y enviar un mensaje de servicio de mensajes cortos (SMS). Para obtener más información sobre Twilio y el uso de voz y SMS en las aplicaciones, consulte la sección [Pasos siguientes](#NextSteps) .

## <a id="WhatIs"></a>¿Qué es Twilio?
Twilio es una API de servicio web de telefonía que le permite utilizar sus habilidades y lenguajes de web existentes para crear aplicaciones de SMS y voz. Twilio es un servicio de terceros (no una característica de Azure y no es un producto de Microsoft).

**Voz Twilio** permite a las aplicaciones realizar y recibir llamadas telefónicas. **Twilio SMS** permite a las aplicaciones realizar y recibir mensajes SMS. **Cliente de Twilio** permite a las aplicaciones habilitar la comunicación de voz con conexiones a Internet existentes, incluidas las conexiones móviles.

## <a id="Pricing"></a>Twilio precios y ofertas especiales
Obtener información sobre precios Twilio está disponible en [Precios Twilio] [twilio_pricing]. Los clientes de Azure reciban una [oferta especial][special_offer]: un crédito libre de 1000 textos o 1000 minutos de entrada. Para suscribirse a esta oferta u obtener más información, visite [http://ahoy.twilio.com/azure][special_offer].  

## <a id="Concepts"></a>Conceptos
La API de Twilio es una API de REST que proporciona la voz y la funcionalidad de SMS de aplicaciones. Bibliotecas de cliente están disponibles en varios idiomas; Para obtener una lista, vea [Bibliotecas de API de Twilio] [twilio_libraries].

Aspectos clave de la API Twilio son verbos Twilio y lenguaje de marcado de Twilio (TwiML).

### <a id="Verbs"></a>Verbos de Twilio
La API emplea Twilio verbos; Por ejemplo, el ** &lt;diga&gt; ** verbo indica que Twilio audible entregar un mensaje en una llamada. 

A continuación se muestra una lista de verbos Twilio.

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

Cuando se suscribe a una cuenta de Twilio, recibirá un identificador de cuenta y un símbolo de autenticación. Ambos será necesario para realizar llamadas de la API de Twilio. Para evitar el acceso no autorizado a su cuenta, mantener el token de autenticación segura. Su ID de cuenta y la autenticación token están visibles en la [página de la cuenta de Twilio] [twilio_account], en los campos etiquetados **SID de la cuenta** y **El TOKEN de autenticación**, respectivamente.

## <a id="create_app"></a>Crear una aplicación de Java
1. Obtener la Twilio JAR y agregarlo a la ruta de acceso de compilación de Java y su ensamblado de implementación de guerra. En [https://github.com/twilio/twilio-java][twilio_java], puede descargar los orígenes de GitHub y crear su propios JAR o descargar un JAR predefinido (con o sin dependencias).
2. Asegúrese de que el almacén de claves de su JDK **cacerts** contiene el certificado de entidad de certificación segura Equifax con MD5 huella 67:CB:9 D: C0:13:24:8A:82:9B:B2:17:1E:D1:1B:EC:D4 (el número de serie es 35:DE:F4:CF y la huella digital SHA1 D2:32:09:AD:23:D3:14:23:21:74:E4:0 D: 7F:9 D: 62:13:97:86:63:3A). Este es el certificado de entidad emisora (CA) para la [https://api.twilio.com] [ twilio_api_service] servicio, que se llama cuando utilice Twilio APIs. Para obtener información sobre cómo asegurarse de su JDK **cacerts** claves contienen el certificado de entidad emisora de certificados correcto, vea [Agregar un certificado al almacén de certificados de entidad emisora de certificados de Java][add_ca_cert].

Instrucciones detalladas para el uso de la biblioteca de cliente de Twilio de Java están disponibles en [cómo realizar una llamada telefónica usando de Twilio en una aplicación de Java en Azure][howto_phonecall_java].

## <a id="configure_app"></a>Configurar la aplicación para usar bibliotecas de Twilio
En el código, puede agregar instrucciones **import** en la parte superior de los archivos de origen para los paquetes de Twilio o clases que desea usar en la aplicación. 

Para los archivos de origen de Java:

    import com.twilio.*;
    import com.twilio.sdk.*;
    import com.twilio.sdk.resource.factory.*;
    import com.twilio.sdk.resource.instance.*;

Para los archivos de origen de la página JSP (Java Server):

    import="com.twilio.*"
    import="com.twilio.sdk.*"
    import="com.twilio.sdk.resource.factory.*"
    import="com.twilio.sdk.resource.instance.*"
Dependiendo de qué paquetes de Twilio o clases que desea usar, las instrucciones de **Importar** pueden ser diferentes.

## <a id="howto_make_call"></a>Cómo: realizar una llamada de salida
La siguiente imagen muestra cómo realizar una llamada mediante la clase **CallFactory** . Este código también utiliza un sitio de Twilio le ha proporcionado para devolver la respuesta de lenguaje de marcado de Twilio (TwiML). Sustituir valores para los números de teléfono **desde** y **hasta** y asegúrese de que compruebe el número **de** teléfono para su cuenta de Twilio antes de ejecutar el código.

    // Use your account SID and authentication token instead
    // of the placeholders shown here.
    String accountSID = "your_twilio_account";
    String authToken = "your_twilio_authentication_token";

    // Create an instance of the Twilio client.
    TwilioRestClient client;
    client = new TwilioRestClient(accountSID, authToken);

    // Retrieve the account, used later to create an instance of the CallFactory.
    Account account = client.getAccount();

    // Use the Twilio-provided site for the TwiML response.
    String Url="http://twimlets.com/message";
    Url = Url + "?Message%5B0%5D=Hello%20World";

    // Place the call From, To and URL values into a hash map. 
    HashMap<String, String> params = new HashMap<String, String>();
    params.put("From", "NNNNNNNNNN"); // Use your own value for the second parameter.
    params.put("To", "NNNNNNNNNN");   // Use your own value for the second parameter.
    params.put("Url", Url);

    // Create an instance of the CallFactory class.
    CallFactory callFactory = account.getCallFactory();

    // Make the call.
    Call call = callFactory.create(params);

Para obtener más información acerca de los parámetros que se pasa al método **CallFactory.create** , consulte [http://www.twilio.com/docs/api/rest/making-calls][twilio_rest_making_calls].

Como se mencionó, este código utiliza un sitio de Twilio le ha proporcionado para devolver la respuesta TwiML. En su lugar puede utilizar su propio sitio para proporcionar la respuesta TwiML; Para obtener más información, vea [cómo proporcionar TwiML las respuestas en una aplicación de Java en Azure](#howto_provide_twiml_responses).

## <a id="howto_send_sms"></a>Cómo: enviar un mensaje SMS
La siguiente imagen muestra cómo enviar un mensaje SMS mediante la clase **SmsFactory** . **De** número, **4155992671**, se proporciona por Twilio para las cuentas de prueba para enviar mensajes SMS. Es necesario comprobar **el número** de la cuenta de Twilio antes de ejecutar el código.

    // Use your account SID and authentication token instead
    // of the placeholders shown here.
    String accountSID = "your_twilio_account";
    String authToken = "your_twilio_authentication_token";

    // Create an instance of the Twilio client.
    TwilioRestClient client;
    client = new TwilioRestClient(accountSID, authToken);

    // Retrieve the account, used later to create an instance of the SmsFactory.
    Account account = client.getAccount();

    // Send an SMS message.
    MessageFactory messageFactory = account.getMessageFactory();
    
    List<NameValuePair> params = new ArrayList<NameValuePair>();
    params.add(new BasicNameValuePair("To", "+14159352345")); // Replace with a valid phone number for your account.
    params.add(new BasicNameValuePair("From", "+14158141829")); // Replace with a valid phone number for your account.
    params.add(new BasicNameValuePair("Body", "Where's Wallace?"));
    
    Message sms = messageFactory.create(params);
        
Para obtener más información acerca de los parámetros que se pasa al método **SmsFactory.create** , consulte [http://www.twilio.com/docs/api/rest/sending-sms][twilio_rest_sending_sms].

## <a id="howto_provide_twiml_responses"></a>Cómo: proporcionar respuestas TwiML desde su propio sitio Web
Cuando la aplicación inicia una llamada a la API Twilio, por ejemplo a través del método **CallFactory.create** , Twilio enviará su solicitud a una dirección URL que se espera para devolver una respuesta de TwiML. El ejemplo anterior utiliza la dirección URL siempre Twilio [http://twimlets.com/message][twimlet_message_url]. (Aunque TwiML está diseñado para usar los servicios Web, puede ver el TwiML en el explorador. Por ejemplo, haga clic en [http://twimlets.com/message] [ twimlet_message_url] para ver una vacía ** &lt;respuesta&gt; ** elemento; Por ejemplo, haga clic en [http://twimlets.com/message?Message%5B0%5D=Hello%20World] [ twimlet_message_url_hello_world] para ver una ** &lt;respuesta&gt; ** elemento que contiene un ** &lt;diga&gt; ** elemento.)

En lugar de confiar en la dirección URL Twilio le ha proporcionado, puede crear su propio sitio de la dirección URL que devuelve respuestas HTTP. Puede crear el sitio en cualquier idioma que devuelve las respuestas HTTP; en este tema se supone que va a hospedar la dirección URL en una página JSP.

Resultados de la siguiente página JSP en una respuesta de TwiML que dice **Hola a todos** en la llamada.

    <%@ page contentType="text/xml" %>
    <Response> 
        <Say>Hello World</Say>
    </Response>

Resultados de la siguiente página JSP en una respuesta de TwiML que dice algún texto, tiene varias se ponga en pausa y dice información sobre la versión de la API Twilio y el nombre de la función de Azure.


    <%@ page contentType="text/xml" %>
    <Response> 
        <Say>Hello from Azure</Say>
        <Pause></Pause>
        <Say>The Twilio API version is <%= request.getParameter("ApiVersion") %>.</Say>
        <Say>The Azure role name is <%= System.getenv("RoleName") %>.</Say>
        <Pause></Pause>
        <Say>Good bye.</Say>
    </Response>

El parámetro **ApiVersion** está disponible en Twilio voz solicitudes (no SMS). Para ver los parámetros de solicitud disponibles para Twilio voz y las solicitudes SMS, consulte <https://www.twilio.com/docs/api/twiml/twilio_request> y <https://www.twilio.com/docs/api/twiml/sms/twilio_request>, respectivamente. La variable de entorno de **nombre de rol** está disponible como parte de una implementación de Azure. (Si desea agregar variables de entorno personalizado, por lo que podría recoger de **System.getenv**, consulte la sección de variables de entorno en [Varios valores de configuración de rol][misc_role_config_settings].)

Una vez que la página JSP configurada para proporcionar TwiML respuestas, use la dirección URL de la página JSP como la dirección URL que se pasa al método **CallFactory.create** . Por ejemplo, si tiene una aplicación Web denominada MyTwiML implementado en un Azure hospedado servicio y el nombre de la página JSP es mytwiml.jsp, la dirección URL se puede pasar a **CallFactory.create** tal como se muestra en el siguiente:

    // Place the call From, To and URL values into a hash map. 
    HashMap<String, String> params = new HashMap<String, String>();
    params.put("From", "NNNNNNNNNN");
    params.put("To", "NNNNNNNNNN");
    params.put("Url", "http://<your_hosted_service>.cloudapp.net/MyTwiML/mytwiml.jsp");

    CallFactory callFactory = account.getCallFactory();
    Call call = callFactory.create(params);

Otra opción para responder con TwiML es a través de la clase **TwiMLResponse** , que está disponible en el paquete **com.twilio.sdk.verbs** .

Para obtener información sobre el uso de Twilio en Azure con Java, consulte [cómo hacer una llamada de teléfono usando de Twilio en una aplicación de Java en Azure][howto_phonecall_java].

## <a id="AdditionalServices"></a>Cómo: usar servicios de Twilio adicionales
Además de los ejemplos que se muestra aquí, Twilio ofrece API basadas en web que puede usar para aprovechar la funcionalidad de Twilio adicional desde la aplicación de Azure. Para obtener más detalles, consulte la [documentación de la API de Twilio] [twilio_api_documentation].

## <a id="NextSteps"></a>Pasos siguientes
Ahora que ha aprendido los conceptos básicos del servicio Twilio, siga estos vínculos para obtener más información:

* [Directrices de seguridad de Twilio] [twilio_security_guidelines]
* [Código de ejemplo y de Twilio HowTo] [twilio_howtos]
* [Tutoriales de Twilio][twilio_quickstarts] 
* [Twilio en GitHub] [twilio_on_github]
* [Póngase en contacto con soporte técnico de Twilio] [twilio_support]

[twilio_java]: https://github.com/twilio/twilio-java
[twilio_api_service]: https://api.twilio.com
[add_ca_cert]: java-add-certificate-ca-store.md
[howto_phonecall_java]: partner-twilio-java-phone-call-example.md
[misc_role_config_settings]: http://msdn.microsoft.com/library/windowsazure/hh690945.aspx
[twimlet_message_url]: http://twimlets.com/message
[twimlet_message_url_hello_world]: http://twimlets.com/message?Message%5B0%5D=Hello%20World
[twilio_rest_making_calls]: http://www.twilio.com/docs/api/rest/making-calls
[twilio_rest_sending_sms]: http://www.twilio.com/docs/api/rest/sending-sms
[twilio_pricing]: http://www.twilio.com/pricing
[special_offer]: http://ahoy.twilio.com/azure
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
