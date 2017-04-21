<properties 
    pageTitle="Cómo usar Twilio para voz y SMS (Ruby) | Microsoft Azure" 
    description="Obtenga información sobre cómo realizar una llamada telefónica y enviar un mensaje SMS con el servicio de la API Twilio en Azure. Ejemplos de código escritos en Ruby." 
    services="" 
    documentationCenter="ruby" 
    authors="devinrader" 
    manager="twilio" 
    editor=""/>

<tags 
    ms.service="multiple" 
    ms.workload="na" 
    ms.tgt_pltfrm="na" 
    ms.devlang="ruby" 
    ms.topic="article" 
    ms.date="11/25/2014" 
    ms.author="MicrosoftHelp@twilio.com"/>





# <a name="how-to-use-twilio-for-voice-and-sms-capabilities-in-ruby"></a>Cómo usar Twilio para la voz y las capacidades SMS de rubí
Esta guía muestra cómo realizar tareas comunes de programación con el servicio de la API Twilio en Azure. Los escenarios cubiertos incluyen realizar una llamada de teléfono y enviar un mensaje de servicio de mensajes cortos (SMS). Para obtener más información sobre Twilio y el uso de voz y SMS en las aplicaciones, consulte la sección [Pasos siguientes](#NextSteps) .

## <a id="WhatIs"></a>¿Qué es Twilio?
Twilio es una API de servicio web de telefonía que le permite utilizar sus habilidades y lenguajes de web existentes para crear aplicaciones de SMS y voz. Twilio es un servicio de terceros (no una característica de Azure y no es un producto de Microsoft).

**Voz Twilio** permite a las aplicaciones realizar y recibir llamadas telefónicas. **Twilio SMS** permite a las aplicaciones realizar y recibir mensajes SMS. **Cliente de Twilio** permite a las aplicaciones habilitar la comunicación de voz con conexiones a Internet existentes, incluidas las conexiones móviles.

## <a id="Pricing"></a>Twilio precios y ofertas especiales
Obtener información sobre precios Twilio está disponible en [Precios Twilio] [twilio_pricing]. Los clientes de Azure reciban una [oferta especial][special_offer]: un crédito libre de 1000 textos o 1000 minutos de entrada. Para suscribirse a esta oferta u obtener más información, visite [http://ahoy.twilio.com/azure][special_offer].  

## <a id="Concepts"></a>Conceptos
La API de Twilio es una API de REST que proporciona la voz y la funcionalidad de SMS de aplicaciones. Bibliotecas de cliente están disponibles en varios idiomas; Para obtener una lista, vea [Bibliotecas de API de Twilio] [twilio_libraries].

### <a id="TwiML"></a>TwiML
TwiML es un conjunto de instrucciones basado en XML que informan a Twilio de cómo procesar una llamada o SMS.

Como ejemplo, el siguiente TwiML ¿convertir el texto **Hola a todos** en voz.

    <?xml version="1.0" encoding="UTF-8" ?>
    <Response>
       <Say>Hello World</Say>
    </Response>

Todos los documentos de TwiML `<Response>` como su elemento raíz. Desde allí, use Twilio verbos para definir el comportamiento de la aplicación.

### <a id="Verbs"></a>Verbos de TwiML
Verbos de Twilio son etiquetas XML que indican Twilio qué **hacer**. Por ejemplo, el ** &lt;diga&gt; ** verbo indica que Twilio audible entregar un mensaje en una llamada. 

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

Para obtener más información sobre Twilio verbos, sus atributos y TwiML, consulte [TwiML] [twiml]. Para obtener información adicional acerca de la API Twilio, vea [API de Twilio] [twilio_api].

## <a id="CreateAccount"></a>Crear una cuenta de Twilio
Cuando esté listo para obtener una cuenta de Twilio, suscribirse al [Intentar Twilio] [try_twilio]. Puede empezar con una cuenta gratuita y actualizar su cuenta más adelante.

Cuando se suscribe a una cuenta de Twilio, recibirá un número de teléfono gratuito para la aplicación. También recibirá una cuenta SID y un símbolo de autenticación. Ambos será necesario para realizar llamadas de la API de Twilio. Para evitar el acceso no autorizado a su cuenta, mantener el token de autenticación segura. Su cuenta SID y auth token están visibles en la [página de la cuenta de Twilio][twilio_account], en los campos etiquetados **SID de la cuenta** y **El TOKEN de autenticación**, respectivamente.

### <a id="VerifyPhoneNumbers"></a>Comprobar los números de teléfono
Además del número le proporciona Twilio, también puede comprobar los números de ese control (es decir, su número de teléfono móvil o particular) para su uso en las aplicaciones. 

Para obtener información sobre cómo comprobar un número de teléfono, vea [Administrar números] [verify_phone].

## <a id="create_app"></a>Crear una aplicación de ideogramas y transcripción fonética
Una aplicación ideogramas y transcripción fonética que usa el servicio de Twilio y se ejecuta en Azure no es diferente de cualquier otra aplicación ideogramas y transcripción fonética que usa el servicio de Twilio. Mientras Twilio servicios son RESTful y se pueden llamar desde Ruby de varias formas, en este artículo se centrará en cómo usar servicios de Twilio con [biblioteca auxiliar de Twilio para Ruby][twilio_ruby].

En primer lugar, [Configurar una nueva máquina virtual Linux de Azure] [ azure_vm_setup] para que actúe como host para la nueva aplicación web Ruby. Omitir los pasos que implica la creación de una aplicación de guías, acaban de configurar la máquina virtual. Asegúrese de que crear un extremo con un puerto externo de 80 y un puerto interno de 5000.

En los ejemplos siguientes, se utilizará [Sinatra][sinatra], un marco de web muy simple para Ruby. Pero, por supuesto, puede usar la biblioteca de auxiliar Twilio de rubí con cualquier otro marco web incluyen Ruby en guías.

SSH en su nueva máquina virtual y cree un directorio para la nueva aplicación. Dentro de ese directorio, cree un archivo llamado Gemfile y copie el código siguiente en él:

    source 'https://rubygems.org'
    gem 'sinatra'
    gem 'thin'

En la línea de comandos ejecutar `bundle install`. Esto instalará las dependencias anteriores. A continuación, cree un archivo llamado `web.rb`. Se trata de donde reside el código para la aplicación web. Pegue el código siguiente en él:

    require 'sinatra'

    get '/' do
        "Hello Monkey!"
    end

En este momento debería poder el ejecutar el comando `ruby web.rb -p 5000`. Esto se control seguridad un servidor web pequeño en el puerto 5000. Debería poder ir a esta aplicación en el explorador, visite la dirección URL configuración de la máquina virtual de Azure. Una vez que se puede llegar a la aplicación web en el explorador, está listo para comenzar a crear una aplicación de Twilio.

## <a id="configure_app"></a>Configurar la aplicación para usar Twilio
Puede configurar la aplicación web para usar la biblioteca Twilio actualizando su `Gemfile` para incluir esta línea:

    gem 'twilio-ruby'

En la línea de comandos, ejecute `bundle install`. Ahora abra `web.rb` e incluyendo esta línea en la parte superior:

    require 'twilio-ruby'

Está ahora listo para usar la biblioteca de auxiliar Twilio para Ruby en la aplicación web.

## <a id="howto_make_call"></a>Cómo: realizar una llamada de salida
La siguiente muestra cómo realizar una llamada. Conceptos clave incluyen el uso de la biblioteca de auxiliar Twilio para Ruby para realizar llamadas de la API de REST y representación TwiML. Sustituir valores para los números de teléfono **desde** y **hasta** y asegúrese de que compruebe el número **de** teléfono para su cuenta de Twilio antes de ejecutar el código.

Agregue esta función a `web.md`:

    # Set your account ID and authentication token.
    sid = "your_twilio_account_sid";
    token = "your_twilio_authentication_token";

    # The number of the phone initiating the the call.
    # This should either be a Twilio number or a number that you've verified
    from = "NNNNNNNNNNN";

    # The number of the phone receiving call.
    to = "NNNNNNNNNNN";

    # Use the Twilio-provided site for the TwiML response.
    url = "http://yourdomain.cloudapp.net/voice_url";
      
    get '/make_call' do
      # Create the call client.
      client = Twilio::REST::Client.new(sid, token);
      
      # Make the call
      client.account.calls.create(to: to, from: from, url: url)
    end

    post '/voice_url' do
      "<Response>
         <Say>Hello Monkey!</Say>
       </Response>"
    end
    
Si abrir arriba `http://yourdomain.cloudapp.net/make_call` en un explorador, que activará la llamada a la API Twilio para realizar la llamada. Los dos primeros parámetros en `client.account.calls.create` es bastante fácil de entender: es el número de la llamada `from` y es el número de la llamada `to`. 

El tercer parámetro (`url`) es la dirección URL que solicita Twilio para obtener instrucciones sobre qué hacer una vez que se conecta la llamada. En este caso, configurar una dirección URL (`http://yourdomain.cloudapp.net`) que devuelve un documento de TwiML simple y usa la `<Say>` verbo hacer algún texto a voz y decir "Hola mono" a la persona que recibe la llamada.

## <a id="howto_recieve_sms"></a>Cómo: recibir un mensaje de SMS
En el ejemplo anterior se inicia **una llamada de teléfono** . Esta vez, vamos a usar el número de teléfono Twilio nos proporcionó durante la suscripción para procesar un mensaje SMS **entrante** .

En primer lugar, inicie sesión en el [panel de Twilio][twilio_account]. Haga clic en "Números" en la barra de navegación superior y, a continuación, haga clic en el número de Twilio que ha proporcionado. Verá dos direcciones URL que puede configurar. URL de la solicitud de una dirección URL de la solicitud de voz y SMS. Estos son las direcciones URL que Twilio llama siempre que se realiza una llamada de teléfono o un SMS se envía a su número. Las direcciones URL también se conocen como "enlaces web".

Vamos a procesar los mensajes entrantes de SMS, así que vamos a actualizar la dirección URL para `http://yourdomain.cloudapp.net/sms_url`. Continuar y haga clic en Guardar cambios en la parte inferior de la página. Ahora, se copia en `web.rb` vamos a programar nuestra aplicación controlar esto:

    post '/sms_url' do
      "<Response>
         <Message>Hey, thanks for the ping! Twilio and Azure rock!</Message>
       </Response>"
    end

Después de realizar el cambio, asegúrese de que volver a iniciar la aplicación web. Ahora, saque el teléfono y enviar un SMS a su número de Twilio. Mayor brevedad debería obtener una respuesta SMS que dice "Hola, gracias por el ping! Roca Twilio y Azure! ".

## <a id="additional_services"></a>Cómo: usar servicios de Twilio adicionales
Además de los ejemplos que se muestra aquí, Twilio ofrece API basadas en web que puede usar para aprovechar la funcionalidad de Twilio adicional desde la aplicación de Azure. Para obtener más detalles, consulte la [documentación de la API de Twilio] [twilio_api_documentation].

### <a id="NextSteps"></a>Pasos siguientes
Ahora que ha aprendido los conceptos básicos del servicio Twilio, siga estos vínculos para obtener más información:

* [Directrices de seguridad de Twilio] [twilio_security_guidelines]
* [Código de ejemplo y procedimientos de Twilio] [twilio_howtos]
* [Tutoriales de Twilio][twilio_quickstarts] 
* [Twilio en GitHub] [twilio_on_github]
* [Póngase en contacto con soporte técnico de Twilio] [twilio_support]

[twilio_ruby]: https://www.twilio.com/docs/ruby/install





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
[sinatra]: http://www.sinatrarb.com/
[azure_vm_setup]: http://www.windowsazure.com/develop/ruby/tutorials/web-app-with-linux-vm/
