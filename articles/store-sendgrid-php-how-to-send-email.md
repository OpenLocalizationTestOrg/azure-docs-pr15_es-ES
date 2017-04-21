<properties 
    pageTitle="Cómo usar el servicio de correo electrónico SendGrid (PHP) | Microsoft Azure" 
    description="Obtenga información sobre cómo enviar correo electrónico con el servicio de correo electrónico SendGrid en Azure. Ejemplos de código escritos en PHP." 
    documentationCenter="php" 
    services="" 
    manager="sendgrid" 
    editor="mollybos" 
    authors="thinkingserious"/>

<tags 
    ms.service="multiple" 
    ms.workload="na" 
    ms.tgt_pltfrm="na" 
    ms.devlang="PHP" 
    ms.topic="article" 
    ms.date="10/30/2014" 
    ms.author="elmer.thomas@sendgrid.com; erika.berkland@sendgrid.com; vibhork; matt.bernier@sendgrid.com"/>
# <a name="how-to-use-the-sendgrid-email-service-from-php"></a>Cómo usar el servicio de correo electrónico de SendGrid de PHP

Esta guía muestra cómo realizar tareas comunes de programación con el servicio de correo electrónico SendGrid en Azure. Los ejemplos están escritos en PHP.
Los escenarios cubiertos incluyen **construcción de correo electrónico**, **Enviar correo electrónico**y **Agregar datos adjuntos**. Para obtener más información sobre SendGrid y envío de correo electrónico, vea la sección [Pasos siguientes](#next-steps) .

## <a name="what-is-the-sendgrid-email-service"></a>¿Qué es el servicio de correo electrónico de SendGrid?

SendGrid es un [servicio de correo electrónico basado en la nube] que proporciona confiable de [entrega de correo electrónico transacciones], escalabilidad y análisis en tiempo real junto con la API flexibles que facilitan la integración personalizada. Escenarios comunes de uso de SendGrid son:

-   Enviar automáticamente confirmaciones a clientes
-   Distribución de administrar listas para el envío de los clientes e-folletos mensuales y ofertas especiales
-   Recopilación de métricas en tiempo real para cosas como bloqueados de correo electrónico y la capacidad de respuesta del cliente
-   Generación de informes para ayudarle a identificar tendencias
-   Consultas de clientes de desvío de llamadas
- Notificaciones de correo electrónico desde la aplicación

Para obtener más información, consulte [https://sendgrid.com][].

## <a name="create-a-sendgrid-account"></a>Crear una cuenta de SendGrid

[AZURE.INCLUDE [sendgrid-sign-up](../includes/sendgrid-sign-up.md)]

## <a name="using-sendgrid-from-your-php-application"></a>Usar SendGrid desde la aplicación PHP

Usa SendGrid en una aplicación de Azure PHP no requiere especial codificación o configuración. Dado que SendGrid es un servicio, puede tener acceso en la misma forma desde una aplicación de nube como sea posible desde una aplicación local.

## <a name="how-to-send-an-email"></a>Cómo: enviar un correo electrónico

Puede enviar correo electrónico mediante SMTP o la API de Web de SendGrid.

### <a name="smtp-api"></a>API DE SMTP

Para enviar correo electrónico con la API de SMTP SendGrid, use *El formulario de Swift*, una biblioteca basada en el componente para enviar correos electrónicos desde aplicaciones PHP. Puede descargar la biblioteca de *Formulario de Swift* desde [http://swiftmailer.org/download][] v5.3.0 (use [Compositor] instalar formulario Swift). Enviar correo electrónico con la biblioteca implica la creación de instancias de la <span class="auto-style2">Swift\_SmtpTransport</span>, <span class="auto-style2">Swift\_formulario</span>, y <span class="auto-style2">Swift\_mensaje</span> clases, establecer las propiedades adecuadas y llamar a la <span class="auto-style2">Swift\_Mailer::send</span> método.

    <?php
     include_once "vendor/autoload.php";
     /*
      * Create the body of the message (a plain-text and an HTML version).
      * $text is your plain-text email
      * $html is your html version of the email
      * If the reciever is able to view html emails then only the html
      * email will be displayed
      */ 
     $text = "Hi!\nHow are you?\n";
     $html = "<html>
           <head></head>
           <body>
               <p>Hi!<br>
                   How are you?<br>
               </p>
           </body>
           </html>";
     // This is your From email address
     $from = array('someone@example.com' => 'Name To Appear');
     // Email recipients
     $to = array(
           'john@contoso.com'=>'Destination 1 Name',
           'anna@contoso.com'=>'Destination 2 Name'
     );
     // Email subject
     $subject = 'Example PHP Email';

     // Login credentials
     $username = 'yoursendgridusername';
     $password = 'yourpassword';
     
     // Setup Swift mailer parameters
     $transport = Swift_SmtpTransport::newInstance('smtp.sendgrid.net', 587);
     $transport->setUsername($username);
     $transport->setPassword($password);
     $swift = Swift_Mailer::newInstance($transport);

     // Create a message (subject)
     $message = new Swift_Message($subject);

     // attach the body of the email
     $message->setFrom($from);
     $message->setBody($html, 'text/html');
     $message->setTo($to);
     $message->addPart($text, 'text/plain');
     
     // send message 
     if ($recipients = $swift->send($message, $failures))
     {
         // This will let us know how many users received this message
         echo 'Message sent out to '.$recipients.' users';
     }
     // something went wrong =(
     else
     {
         echo "Something went wrong - ";
         print_r($failures);
     }

### <a name="web-api"></a>API de Web

Use [doblez de función de PHP][] para enviar correo electrónico con la API de Web SendGrid.

    <?php

     $url = 'https://api.sendgrid.com/';
     $user = 'USERNAME';
     $pass = 'PASSWORD'; 

     $params = array(
          'api_user' => $user,
          'api_key' => $pass,
          'to' => 'john@contoso.com',
          'subject' => 'testing from curl',
          'html' => 'testing body',
          'text' => 'testing body',
          'from' => 'anna@contoso.com',
       );
       
     $request = $url.'api/mail.send.json';
     
     // Generate curl request
     $session = curl_init($request);
     
     // Tell curl to use HTTP POST
     curl_setopt ($session, CURLOPT_POST, true);
     
     // Tell curl that this is the body of the POST
     curl_setopt ($session, CURLOPT_POSTFIELDS, $params);
     
     // Tell curl not to return headers, but do return the response
     curl_setopt($session, CURLOPT_HEADER, false);
     curl_setopt($session, CURLOPT_RETURNTRANSFER, true);
     
     // obtain response
     $response = curl_exec($session);
     curl_close($session);
     
     // print everything out
     print_r($response);

Web API de SendGrid es muy similar a la API de REST, aunque no sea realmente API de REST ya que, en la mayoría de las llamadas, ambos obtener y verbos de entrada se pueden usar indistintamente.

## <a name="how-to-add-an-attachment"></a>Cómo: agregar datos adjuntos

### <a name="smtp-api"></a>API DE SMTP

Enviar datos adjuntos con la API de SMTP implica una línea adicional de código de la secuencia de comandos para enviar un correo electrónico con el formulario de Swift.

    <?php
     include_once "vendor/autoload.php";
     /*
      * Create the body of the message (a plain-text and an HTML version).
      * $text is your plain-text email
      * $html is your html version of the email
      * If the reciever is able to view html emails then only the html
      * email will be displayed
      */
     $text = "Hi!\nHow are you?\n";
      $html = "<html>
          <head></head>
          <body>
             <p>Hi!<br>
                How are you?<br>
             </p>
          </body>
          </html>";

     // This is your From email address
     $from = array('someone@example.com' => 'Name To Appear');
     
     // Email recipients
     $to = array(
          'john@contoso.com'=>'Destination 1 Name',
          'anna@contoso.com'=>'Destination 2 Name'
     );
     // Email subject
     $subject = 'Example PHP Email';
     
     // Login credentials
     $username = 'yoursendgridusername';
     $password = 'yourpassword';
     
     // Setup Swift mailer parameters
     $transport = Swift_SmtpTransport::newInstance('smtp.sendgrid.net', 587);
     $transport->setUsername($username);
     $transport->setPassword($password);
     $swift = Swift_Mailer::newInstance($transport);
     
     // Create a message (subject)
     $message = new Swift_Message($subject);
     
     // attach the body of the email
     $message->setFrom($from);
     $message->setBody($html, 'text/html');
     $message->setTo($to);
     $message->addPart($text, 'text/plain');
     $message->attach(Swift_Attachment::fromPath("path\to\file")->setFileName("file_name"));
     
     // send message 
     if ($recipients = $swift->send($message, $failures))
     {
          // This will let us know how many users received this message
          echo 'Message sent out to '.$recipients.' users';
     }
     // something went wrong =(
     else
     {
          echo "Something went wrong - ";
          print_r($failures);
     }

La línea adicional de código es la siguiente:

     $message->attach(Swift_Attachment::fromPath("path\to\file")->setFileName('file_name'));

Esta línea de código llama al método de adjuntar en la <span class="auto-style2">Swift\_mensaje</span> objeto y utiliza el método estático <span class="auto-style2">fromPath</span> en la <span class="auto-style2">Swift\_datos adjuntos</span> clase para obtener y adjuntar un archivo a un mensaje.

### <a name="web-api"></a>API de Web

Enviar datos adjuntos con la API de Web es muy similar a enviar un correo electrónico con la API de Web. Sin embargo, tenga en cuenta que en el ejemplo siguiente, la matriz de parámetros debe contener este elemento:

    'files['.$fileName.']' => '@'.$filePath.'/'.$fileName

Ejemplo:

    <?php

     $url = 'https://api.sendgrid.com/';
     $user = 'USERNAME';
     $pass = 'PASSWORD';
     
     $fileName = 'myfile';
     $filePath = dirname(__FILE__);

     $params = array(
         'api_user' => $user,
         'api_key' => $pass,
         'to' =>'john@contoso.com',
         'subject' => 'test of file sends',
         'html' => '<p> the HTML </p>',
         'text' => 'the plain text',
         'from' => 'anna@contoso.com',
         'files['.$fileName.']' => '@'.$filePath.'/'.$fileName
     );
     
     print_r($params);
     
     $request = $url.'api/mail.send.json';
     
     // Generate curl request
     $session = curl_init($request);
     
     // Tell curl to use HTTP POST
     curl_setopt ($session, CURLOPT_POST, true);
     
     // Tell curl that this is the body of the POST
     curl_setopt ($session, CURLOPT_POSTFIELDS, $params);
     
     // Tell curl not to return headers, but do return the response
     curl_setopt($session, CURLOPT_HEADER, false);
     curl_setopt($session, CURLOPT_RETURNTRANSFER, true);
     
     // obtain response
     $response = curl_exec($session);
     curl_close($session);
     
     // print everything out
     print_r($response);

## <a name="how-to-use-filters-to-enable-footers-tracking-and-analytics"></a>Cómo: usar filtros para habilitar los pies de página, seguimiento y análisis

SendGrid proporciona la funcionalidad de correo electrónico adicional mediante el uso de 'filtros'. Estos son los valores que se pueden agregar a un mensaje de correo electrónico para habilitar la funcionalidad específica como habilitar, haga clic en seguimiento, Google analytics, seguimiento de suscripción y así sucesivamente.

Los filtros se pueden aplicar a un mensaje mediante la propiedad de filtros. Cada filtro especificado por un valor de tipo hash que contiene la configuración específica del filtro. En el ejemplo siguiente se permite que el filtro de pie de página y especifica un mensaje de texto que se agregará a la parte inferior del mensaje de correo electrónico.
En este ejemplo, usaremos [sendgrid php biblioteca].
Use [Compositor] instalar biblioteca:
    
    php composer.phar require sendgrid/sendgrid 2.1.1

Ejemplo:    

    <?php
     /*
      * This example is used for sendgrid-php V2.1.1 (https://github.com/sendgrid/sendgrid-php/tree/v2.1.1)
      */
     include "vendor/autoload.php";

     $email = new SendGrid\Email();
     // The list of addresses this message will be sent to
     // [This list is used for sending multiple emails using just ONE request to SendGrid]
     $toList = array('john@contoso.com', 'anna@contoso.com');

     // Specify the names of the recipients
     $nameList = array('Name 1', 'Name 2');

     // Used as an example of variable substitution
     $timeList = array('4 PM', '5 PM');

     // Set all of the above variables
     $email->setTos($toList);
     $email->addSubstitution('-name-', $nameList);
     $email->addSubstitution('-time-', $timeList);

     // Specify that this is an initial contact message
     $email->addCategory("initial");

     // You can optionally setup individual filters here, in this example, we have 
     // enabled the footer filter
     $email->addFilter('footer', 'enable', 1);
     $email->addFilter('footer', "text/plain", "Thank you for your business");
     $email->addFilter('footer', "text/html", "Thank you for your business");

     // The subject of your email
     $subject = 'Example SendGrid Email';

     // Where is this message coming from. For example, this message can be from 
     // support@yourcompany.com, info@yourcompany.com
     $from = 'someone@example.com';

     // If you do not specify a sender list above, you can specifiy the user here. If 
     // a sender list IS specified above, this email address becomes irrelevant.
     $to = 'john@contoso.com';

     # Create the body of the message (a plain-text and an HTML version). 
     # text is your plain-text email 
     # html is your html version of the email
     # if the receiver is able to view html emails then only the html
     # email will be displayed

     /*
      * Note the variable substitution here =)
      */
     $text = "
     Hello -name-,
     Thank you for your interest in our products. We have set up an appointment to call you at -time- EST to discuss your needs in more detail.
     Regards,
     Fred";

     $html = "
     <html> 
     <head></head>
     <body>
     <p>Hello -name-,<br>
     Thank you for your interest in our products. We have set up an appointment
     to call you at -time- EST to discuss your needs in more detail.

     Regards,

     Fred<br>
     </p>
     </body>
     </html>";

     // set subject
     $email->setSubject($subject);

     // attach the body of the email
     $email->setFrom($from);
     $email->setHtml($html);
     $email->addTo($to);
     $email->setText($text);

     // Your SendGrid account credentials
     $username = 'sendgridusername@yourdomain.com';
     $password = 'example';

     // Create SendGrid object
     $sendgrid = new SendGrid($username, $password);

     // send message
     $response = $sendgrid->send($email);

     print_r($response);

## <a name="next-steps"></a>Pasos siguientes

Ahora que ha aprendido los conceptos básicos del servicio de correo electrónico de SendGrid, siga estos vínculos para obtener más información.

-   Documentación de SendGrid: <https://sendgrid.com/docs>
-   Biblioteca de PHP SendGrid: <https://github.com/sendgrid/sendgrid-php>
-   SendGrid oferta especial para clientes de Azure: <https://sendgrid.com/windowsazure.html>

Para obtener más información, vea también el [Centro para desarrolladores de PHP](/develop/php/).


  [https://sendgrid.com]: https://sendgrid.com
  [https://sendgrid.com/transactional-email/pricing]: https://sendgrid.com/transactional-email/pricing
  [special offer]: https://www.sendgrid.com/windowsazure.html
  [Packaging and Deploying PHP Applications for Azure]: http://msdn.microsoft.com/library/windowsazure/hh674499(v=VS.103).aspx
  [http://swiftmailer.org/download]: http://swiftmailer.org/download
  [función doblez]: http://php.net/curl
  [servicio de correo electrónico basado en la nube]: https://sendgrid.com/email-solutions
  [entrega de correo electrónico de transacciones]: https://sendgrid.com/transactional-email
  [biblioteca de php sendgrid]: https://github.com/sendgrid/sendgrid-php/tree/v2.1.1
  [Compositor]: https://getcomposer.org/download/
