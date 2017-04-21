<properties 
    pageTitle="Usar Twilio para voz, VoIP y mensajería SMS en Azure" 
    description="Obtenga información sobre cómo realizar una llamada telefónica y enviar un mensaje SMS con el servicio de la API Twilio en Azure. Ejemplos de código escritos en Node.js." 
    services="" 
    documentationCenter="nodejs" 
    authors="devinrader" 
    manager="wpickett" 
    editor=""/>

<tags 
    ms.service="multiple" 
    ms.workload="na" 
    ms.tgt_pltfrm="na" 
    ms.devlang="nodejs" 
    ms.topic="article" 
    ms.date="11/25/2014" 
    ms.author="wpickett"/>


# <a name="using-twilio-for-voice-voip-and-sms-messaging-in-azure"></a>Usar Twilio para voz, VoIP y mensajería SMS en Azure

Esta guía muestra cómo crear aplicaciones que se comuniquen con Twilio y node.js en Azure.

<a id="whatis"/>
## <a name="what-is-twilio"></a>¿Qué es Twilio?

Twilio es una plataforma de API que facilita a los desarrolladores a realizar y recibir llamadas telefónicas, enviar y recibir mensajes de texto e incrustar llamadas VoIP en aplicaciones móviles basadas en navegador y nativas.  Vamos a repase brevemente cómo funciona esto antes de comenzar.

### <a name="receiving-calls-and-text-messages"></a>Recibir llamadas y mensajes de texto

Twilio permite a los desarrolladores [de compra de números de teléfono programable] [ purchase_phone] que puede usarse para enviar y recibir llamadas y mensajes de texto.  Cuando un número Twilio recibe una llamada entrante o texto, Twilio enviará su aplicación web un HTTP POST o solicitud GET, donde se pregunta para obtener instrucciones sobre cómo tratar la llamada o el texto.  El servidor responde a la solicitud HTTP de Twilio con [TwiML][twiml], un conjunto de etiquetas XML que contienen instrucciones sobre cómo manejar una llamada de texto simple.  Veremos ejemplos de TwiML en un momento.

### <a name="making-calls-and-sending-text-messages"></a>Realizar llamadas y enviar mensajes de texto

Realizando solicitudes HTTP a la API del servicio web Twilio, los desarrolladores pueden enviar mensajes de texto o iniciar llamadas telefónicas salientes.  Para las llamadas salientes, el programador también debe especificar una dirección URL que devuelve las instrucciones TwiML controlar la llamada de salida una vez que está conectado.

### <a name="embedding-voip-capabilities-in-ui-code-javascript-ios-or-android"></a>Incrustar funciones VoIP en código de la interfaz de usuario (JavaScript, iOS o Android)

Twilio proporciona un SDK de cliente que se puede convertir cualquier explorador web de escritorio, aplicación iOS o Android aplicación en un teléfono de VoIP.  En este artículo, nos centraremos en cómo usar VoIP llamar en el explorador.  Además del SDK de JavaScript Twilio que se ejecuta en el explorador, una aplicación de servidor (nuestra aplicación node.js) debe usarse para emitir un token de capacidad de"" en el cliente de JavaScript.  Puede obtener más información sobre el uso de VoIP con node.js [en el blog de desarrollo de Twilio][voipnode].

<a id="signup"/>
## <a name="sign-up-for-twilio-microsoft-discount"></a>Suscríbase a Twilio (descuento de Microsoft)

Antes de usar servicios de Twilio, debe [registrarse para una cuenta]de primera[signup].  Los clientes de Microsoft Azure reciban un descuento especial: [Asegúrese de registrarse aquí][signup]!

<a id="azuresite"/>
## <a name="create-and-deploy-a-nodejs-azure-website"></a>Crear e implementar un sitio Web de Azure de node.js

A continuación, debe crear un sitio Web de node.js ejecuta en Azure.  [La documentación oficial para realizar esta acción se encuentra aquí][azure_new_site].  En un nivel alto, realizará lo siguiente:

* Suscribirse a una cuenta de Azure, si aún no tiene una
* Uso de la consola de administración de Azure para crear un nuevo sitio Web
* Agregar compatibilidad con control de origen (consideraremos que utiliza git)
* Crear un archivo `server.js` con una aplicación web de node.js simple
* Implementar esta aplicación simple en Azure

<a id="twiliomodule"/>
## <a name="configure-the-twilio-module"></a>Configurar el módulo de Twilio

A continuación, se comienza a escribir una aplicación de node.js simple que utiliza la API Twilio.  Antes de empezar, debemos configurar nuestros credenciales de cuenta Twilio.  

### <a name="configuring-twilio-credentials-in-system-environment-variables"></a>Configurar las credenciales de Twilio en Variables de entorno del sistema

Para poder realizar las solicitudes autenticadas de Twilio back-end, debemos nuestro SID de la cuenta y auth token, que funcionan como el nombre de usuario y la contraseña establecida para la cuenta de Twilio. La forma más segura para configurar estas para su uso con el módulo de nodo en Azure es a través de las variables de entorno de sistema, que puede establecer directamente en la consola de administración de Azure.

Seleccione el sitio Web de node.js y haga clic en el vínculo "Configurar".  Si se desplaza un poco, verá un área donde puede establecer las propiedades de configuración de la aplicación.  Escriba sus credenciales de cuenta de Twilio ([que se incluye en el panel de Twilio][twilio_dashboard]) tal como se muestra: asegúrese de asignarle un nombre "TWILIO_ACCOUNT_SID" y "TWILIO_AUTH_TOKEN", respectivamente:

![Consola de administración de Azure][azure-admin-console]

Una vez que haya configurado estas variables, reinicie la aplicación en la consola de Azure.

### <a name="declaring-the-twilio-module-in-packagejson"></a>Declarar el módulo Twilio en package.json

A continuación, es necesario crear una package.json para administrar nuestras dependencias de módulo de nodo mediante [npm].  En el mismo nivel que el archivo "server.js" que creó en el tutorial Azure/node.js, cree un archivo denominado "package.json".  En este archivo, coloque el siguiente:

  {"nombre": "nombre de aplicación", "versión": "0.0.1", "privado": true "secuencias de comandos": {"inicio": "servidor de nodo"}, "dependencias": {"express": "3.1.0", "ejs": "*", "twilio": "*"}}

Esto declara el módulo twilio como una dependencia, así como populares [marco web express] [ express] y el motor de plantillas de EJS.  Bien, ahora, todo está listo, vamos a escribir código.

<a id="makecall"/>
## <a name="make-an-outbound-call"></a>Realizar una llamada de salida

Vamos a crear un formulario simple que se realizar una llamada a un número que elija.  Abra server.js y escriba el código siguiente.  Tenga en cuenta que dice "CHANGE_ME" - poner el nombre de su sitio Web azure allí:

    // Module dependencies
    var express = require('express'), 
      path = require('path'), 
      http = require('http'), 
      twilio = require('twilio');

    // Create Express web application
    var app = express();

    // Express configuration
    app.configure(function(){
      app.set('port', process.env.PORT || 3000);
      app.set('views', __dirname + '/views');
      app.set('view engine', 'ejs');
      app.use(express.favicon());
      app.use(express.logger('dev'));
      app.use(express.bodyParser());
      app.use(express.methodOverride());
      app.use(app.router);
      app.use(express.static(path.join(__dirname, 'public')));
    });
    app.configure('development', function(){
      app.use(express.errorHandler());
    });

    // Render an HTML user interface for the application's home page
    app.get('/', function(request, response) {
      response.render('index');
    });

    // Handle the form POST to place a call
    app.post('/call', function(request, response) {
      var client = twilio();
      client.makeCall({
          // make a call to this number
          to:request.body.number,

          // Change to a Twilio number you bought - see:
          // https://www.twilio.com/user/account/phone-numbers/incoming
          from:'+15558675309',

          // A URL in our app which generates TwiML
          // Change "CHANGE_ME" to your app's name
          url:'https://CHANGE_ME.azurewebsites.net/outbound_call'
      }, function(error, data) {
          // Go back to the home page
          response.redirect('/');
      });
    });

    // Generate TwiML to handle an outbound call
    app.post('/outbound_call', function(request, response) {
      var twiml = new twilio.TwimlResponse();

      // Say a message to the call's receiver 
      twiml.say('hello - thanks for checking out Twilio and Azure', {
          voice:'woman'
      });

      response.set('Content-Type', 'text/xml');
      response.send(twiml.toString());
    });

    // Start server
    http.createServer(app).listen(app.get('port'), function(){
      console.log("Express server listening on port " + app.get('port'));
    });

A continuación, cree un directorio denominado "vistas" - dentro de este directorio, cree un archivo denominado "index.ejs" con el contenido siguiente:

    <!DOCTYPE html>
    <html>
    <head>
      <title>Twilio Test</title>
      <style>
        input { height:20px; width:300px; font-size:18px; margin:5px; padding:5px; }
      </style>
    </head>
    <body>
      <h1>Twilio Test</h1>
      <form action="/call" method="POST">
          <input placeholder="Enter a phone number" name="number"/>
          <br/>
          <input type="submit" value="Call the number above"/>
      </form>
    </body>
    </html>

Ahora, implementar el sitio Web en Azure y abra su casa.  Debería poder introduzca su número de teléfono en el campo de texto y a continuación, recibe una llamada de su número de Twilio!

<a id="sendmessage"/>
## <a name="send-an-sms-message"></a>Enviar un mensaje SMS

Ahora, vamos a configurar una interfaz de usuario y la lógica de control de formulario para enviar un mensaje de texto.  Abrir "server.js" y agregue el código siguiente después de la última llamada a "app.post":

    app.post('/sms', function(request, response) {
      var client = twilio();
      client.sendSms({
          // send a text to this number
          to:request.body.number,

          // A Twilio number you bought - see:
          // https://www.twilio.com/user/account/phone-numbers/incoming
          from:'+15558675309',

          // The body of the text message
          body: request.body.message
          
      }, function(error, data) {
          // Go back to the home page
          response.redirect('/');
      });
    });

En "views/index.ejs", agregue otro formulario debajo de la primera para enviar un mensaje de texto y un número:

    <form action="/sms" method="POST">
      <input placeholder="Enter a phone number" name="number"/>
      <br/>
      <input placeholder="Enter a message to send" name="message"/>
      <br/>
      <input type="submit" value="Send text to the number above"/>
    </form>

Implementar la aplicación en Azure y ahora debería poder enviar formulario y enviar usted mismo (o cualquiera de sus amigos más cercanos) de un mensaje de texto.

<a id="nextsteps"/>
## <a name="next-steps"></a>Pasos siguientes

Ahora, ha aprendido los conceptos básicos del uso de node.js y Twilio para generar aplicaciones que se comunican.  Pero la superficie de lo que puede hacer con Twilio y node.js de estos ejemplos apenas borrador.  Para obtener más información con Twilio node.js, consulte los siguientes recursos:

* [Documentos de módulo oficial][docs]
* [Tutorial de VoIP con aplicaciones de node.js][voipnode]
* [Votr - un voto aplicación con node.js y CouchDB (tres partes) de SMS en tiempo real][votr]
* [Programación de par en el explorador con node.js][pair]

Esperamos que amor piratería node.js y Twilio de Azure.

[purchase_phone]: https://www.twilio.com/user/account/phone-numbers/available/local
[twiml]: https://www.twilio.com/docs/api/twiml
[signup]: http://ahoy.twilio.com/azure
[azure_new_site]: /app-service-web/web-sites-nodejs-develop-deploy-mac.md
[twilio_dashboard]: https://www.twilio.com/user/account
[NPM]: http://npmjs.org
[express]: http://expressjs.com
[voipnode]: http://www.twilio.com/blog/2013/04/introduction-to-twilio-client-with-node-js.html
[docs]: http://twilio.github.io/twilio-node/
[votr]: http://www.twilio.com/blog/2012/09/building-a-real-time-sms-voting-app-part-1-node-js-couchdb.html
[pair]: http://www.twilio.com/blog/2013/06/pair-programming-in-the-browser-with-twilio.html
[azure-admin-console]: ./media/partner-twilio-nodejs-how-to-use-voice-sms/twilio_1.png



