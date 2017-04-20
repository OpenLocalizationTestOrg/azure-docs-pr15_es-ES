<properties 
    pageTitle="Cómo usar el servicio de correo electrónico (. NET) SendGrid | Microsoft Azure" 
    description="Obtenga información sobre cómo enviar correo electrónico con el servicio de correo electrónico SendGrid en Azure. El código de ejemplos escritos en C# y usar la API de .NET." 
    services="app-service\web" 
    documentationCenter=".net" 
    authors="thinkingserious" 
    manager="dwrede" 
    editor=""/>

<tags 
    ms.service="app-service-web" 
    ms.workload="na" 
    ms.tgt_pltfrm="na" 
    ms.devlang="dotnet" 
    ms.topic="article" 
    ms.date="01/14/2016" 
    ms.author="team-pi@sendgrid.com"/>





# <a name="how-to-send-email-using-sendgrid-with-azure"></a>Cómo enviar correo electrónico mediante SendGrid con Azure


## <a name="overview"></a>Información general

Esta guía muestra cómo realizar tareas comunes de programación con el servicio de correo electrónico SendGrid en Azure. Los ejemplos están escritos en C\#
y use la API .NET. Los escenarios cubiertos incluyen **construcción de correo electrónico**, **Enviar correo electrónico**, **Agregar datos adjuntos**y **mediante filtros**. Para obtener más información sobre SendGrid y envío de correo electrónico, vea la sección [pasos siguientes][] .

## <a name="what-is-the-sendgrid-email-service"></a>¿Qué es el servicio de correo electrónico de SendGrid?

SendGrid es un [servicio de correo electrónico basado en la nube] que proporciona confiable de [entrega de correo electrónico transacciones], escalabilidad y análisis en tiempo real junto con la API flexibles que facilitan la integración personalizada. Escenarios comunes de uso de SendGrid son:

-   Envío de confirmaciones automáticamente a los clientes.
-   Distribución de administrar listas para el envío de los clientes e-folletos mensuales y ofertas especiales.
-   Recopilación de métricas en tiempo real para cosas como bloqueados de correo electrónico y la respuesta al cliente.
-   Generación de informes para ayudarle a identificar tendencias.
-   Consultas de clientes de reenvío.
-   Procesamiento de correos electrónicos entrantes.

Para obtener más información, vea [https://sendgrid.com](https://sendgrid.com) o nuestra [biblioteca C#][csharp sendgrid]

## <a name="create-a-sendgrid-account"></a>Crear una cuenta de SendGrid

[AZURE.INCLUDE [sendgrid-sign-up](../../includes/sendgrid-sign-up.md)]

## <a name="reference-the-sendgrid-net-class-library"></a>Referencia de la biblioteca de clases .NET SendGrid

El [paquete SendGrid NuGet](https://www.nuget.org/packages/Sendgrid) es la manera más sencilla de obtener la API SendGrid y configurar la aplicación con todas las dependencias. NuGet es una extensión de Visual Studio incluida con Microsoft Visual Studio 2015 que hace que sea fácil de instalar y actualizar las bibliotecas y herramientas. 

> [AZURE.NOTE] Para instalar NuGet si está ejecutando una versión anterior a 2015 de Visual Studio de Visual Studio, visite [http://www.nuget.org](http://www.nuget.org)y haga clic en el botón **Instalar NuGet** .

Para instalar el paquete de SendGrid NuGet en la aplicación, haga lo siguiente:

1.  Crear un nuevo proyecto.

    ![Crear un nuevo proyecto][create-new-project]

2.  Seleccione una plantilla.

    ![Seleccione una plantilla][select-a-template]

3.  En el **Explorador de soluciones**, haga clic en **referencias**, haga clic en **Administrar paquetes de NuGet**.

4.  Busque **SendGrid** y seleccione el elemento de **SendGrid** en la lista de resultados.

    ![Paquete SendGrid NuGet][SendGrid-NuGet-package]

5.  Haga clic en **instalar** para completar la instalación y, a continuación, cierre este cuadro de diálogo.

Biblioteca de clases de SendGrid .NET se denomina **SendGridMail**. Contiene los espacios de nombres siguientes:

-   **SendGridMail** para crear y trabajar con los elementos de correo electrónico.
-   **SendGridMail.Transport** para enviar correo electrónico mediante el protocolo **SMTP** o el protocolo HTTP 1.1 con **Web/resto**.

Agregue las siguientes declaraciones de espacio de nombres de código a la parte superior de cualquier C\# archivo en el que desea tener acceso mediante programación al servicio de correo electrónico de SendGrid.
**System.Net** y **System.Net.Mail** son espacios de nombres de .NET Framework que se incluyen porque incluyen tipos que se usa normalmente con las APIs SendGrid.

    using System;
    using System.Net;
    using System.Net.Mail;
    using SendGrid;

## <a name="how-to-create-an-email"></a>Cómo: crear un correo electrónico

Utilice el objeto **SendGridMessage** para crear un mensaje de correo electrónico. Una vez creado el objeto de mensaje, puede establecer las propiedades y métodos, incluido el remitente de correo electrónico, el destinatario de correo electrónico y el asunto y el cuerpo del correo electrónico.

En el ejemplo siguiente se muestra cómo crear un objeto de correo electrónico completa:

    // Create the email object first, then add the properties.
    var myMessage = new SendGridMessage();

    // Add the message properties.
    myMessage.From = new MailAddress("john@example.com");

    // Add multiple addresses to the To field.
    List<String> recipients = new List<String>
    {
        @"Jeff Smith <jeff@example.com>",
        @"Anna Lidman <anna@example.com>",
        @"Peter Saddow <peter@example.com>"
    };

    myMessage.AddTo(recipients);

    myMessage.Subject = "Testing the SendGrid Library";

    //Add the HTML and Text bodies
    myMessage.Html = "<p>Hello World!</p>";
    myMessage.Text = "Hello World plain text!";

Para obtener más información sobre todas las propiedades y métodos compatibles con el tipo de **SendGrid** , vea [csharp sendgrid][] en GitHub.

## <a name="how-to-send-an-email"></a>Cómo: enviar un correo electrónico

Después de crear un mensaje de correo electrónico, puede enviarlo con la API de Web proporcionados por SendGrid. Como alternativa, puede usar [. RED creada en biblioteca](https://sendgrid.com/docs/Code_Examples/csharp.html).

Enviar correo electrónico requiere que proporcione las credenciales de cuenta de SendGrid (nombre de usuario y contraseña) o la clave de la API de SendGrid. Clave API es el método preferido. Si necesita más información sobre cómo configurar claves API, visite nuestra [documentación](https://sendgrid.com/docs/Classroom/Send/api_keys.html)

Puede almacenar estas credenciales a través de su Portal de Azure haciendo clic en configurar y agregar los pares de clave y valor en "configuración de la aplicación".

 ![Configuración de la aplicación de Azure][azure_app_settings]

 A continuación, se pueden acceder a ellos como sigue: 
    
    var username = System.Environment.GetEnvironmentVariable("SENDGRID_USERNAME"); 
    var pswd = System.Environment.GetEnvironmentVariable("SENDGRID_PASSWORD");
    var apiKey = System.Environment.GetEnvironmentVariable("SENDGRID_APIKEY");

Con las credenciales:
    
    // Create network credentials to access your SendGrid account
    var username = "your_sendgrid_username";
    var pswd = "your_sendgrid_password";

    var credentials = new NetworkCredential(username, pswd);
    // Create an Web transport for sending email.
    var transportWeb = new Web(credentials);

Clave de la API de uso:

    var apiKey = "your_sendgrid_api_key";  
    // create a Web transport, using API Key
    var transportWeb = new Web(apiKey);


Los siguientes ejemplos muestra cómo enviar un mensaje con la API de Web.

    // Create the email object first, then add the properties.
    SendGridMessage myMessage = new SendGridMessage();
    myMessage.AddTo("anna@example.com");
    myMessage.From = new MailAddress("john@example.com", "John Smith");
    myMessage.Subject = "Testing the SendGrid Library";
    myMessage.Text = "Hello World!";

    // Create credentials, specifying your user name and password.
    var credentials = new NetworkCredential("username", "password");

    // Create an Web transport for sending email.
    var transportWeb = new Web(credentials);

    // Send the email, which returns an awaitable task.
    transportWeb.DeliverAsync(myMessage);

    // If developing a Console Application, use the following
    // transportWeb.DeliverAsync(mail).Wait();

## <a name="how-to-add-an-attachment"></a>Cómo: agregar datos adjuntos

Para agregar archivos adjuntos a un mensaje al llamar al método **AddAttachment** y especificando el nombre y la ruta de acceso del archivo que desea adjuntar.
Puede incluir varios datos adjuntos al llamar a este método una vez para cada archivo que desea adjuntar. En el ejemplo siguiente se muestra cómo agregar datos adjuntos a un mensaje:

    SendGridMessage myMessage = new SendGridMessage();
    myMessage.AddTo("anna@example.com");
    myMessage.From = new MailAddress("john@example.com", "John Smith");
    myMessage.Subject = "Testing the SendGrid Library";
    myMessage.Text = "Hello World!";

    myMessage.AddAttachment(@"C:\file1.txt");
    
También puede agregar datos adjuntos de **secuencia**de los datos. Se puede hacer llamando el mismo método que anteriormente, **AddAttachment**, pero pasando la secuencia de los datos y el nombre de archivo que desee mostrar como en el mensaje. En este caso debe agregar la biblioteca de System.IO.

    SendGridMessage myMessage = new SendGridMessage();
    myMessage.AddTo("anna@example.com");
    myMessage.From = new MailAddress("john@example.com", "John Smith");
    myMessage.Subject = "Testing the SendGrid Library";
    myMessage.Text = "Hello World!";

    using (var attachmentFileStream = new FileStream(@"C:\file.txt", FileMode.Open))
    {
        myMessage.AddAttachment(attachmentFileStream, "My Cool File.txt");
    }


## <a name="how-to-use-apps-to-enable-footers-tracking-and-analytics"></a>Cómo: usar las aplicaciones para habilitar los pies de página, seguimiento y análisis

SendGrid proporciona la funcionalidad de correo electrónico adicional mediante el uso de aplicaciones. Estos son los valores que se pueden agregar a un mensaje de correo electrónico para habilitar la funcionalidad específica por ejemplo, haga clic en seguimiento, Google analytics, suscripción de seguimiento, y así sucesivamente. Para obtener una lista completa de aplicaciones, vea [Configuración de la aplicación][].

Aplicaciones se pueden aplicar a los mensajes de correo electrónico **SendGrid** con métodos implementados como parte de la clase **SendGrid** .

Los siguientes ejemplos muestran el pie de página y haga clic en filtros de seguimiento:

### <a name="footer"></a>Pie de página

    // Create the email object first, then add the properties.
    SendGridMessage myMessage = new SendGridMessage();
    myMessage.AddTo("anna@example.com");
    myMessage.From = new MailAddress("john@example.com", "John Smith");
    myMessage.Subject = "Testing the SendGrid Library";
    myMessage.Text = "Hello World!";

    // Add a footer to the message.
    myMessage.EnableFooter("PLAIN TEXT FOOTER", "<p><em>HTML FOOTER</em></p>");

### <a name="click-tracking"></a>Haga clic en seguimiento

    // Create the email object first, then add the properties.
    SendGridMessage myMessage = new SendGridMessage();
    myMessage.AddTo("anna@example.com");
    myMessage.From = new MailAddress("john@example.com", "John Smith");
    myMessage.Subject = "Testing the SendGrid Library";
    myMessage.Html = "<p><a href=\"http://www.example.com\">Hello World Link!</a></p>";
    myMessage.Text = "Hello World!";
    
    // true indicates that links in plain text portions of the email 
    // should also be overwritten for link tracking purposes. 
    myMessage.EnableClickTracking(true);

## <a name="how-to-use-additional-sendgrid-services"></a>Cómo: usar servicios de SendGrid adicionales

SendGrid ofrece basados en web API y webhooks que puede usar para aprovechar la funcionalidad de SendGrid adicional desde la aplicación de Azure. Para obtener más detalles, consulte la [documentación de la API de SendGrid][].

## <a name="next-steps"></a>Pasos siguientes

Ahora que ha aprendido los conceptos básicos del servicio de correo electrónico de SendGrid, siga estos vínculos para obtener más información.

*   C SendGrid\# repo biblioteca: [csharp sendgrid][]
*   Documentación de la API de SendGrid: <https://sendgrid.com/docs>
*   SendGrid oferta especial para clientes de Azure: [https://sendgrid.com](https://sendgrid.com)

  [Pasos siguientes]: #next-steps
  [What is the SendGrid Email Service?]: #whatis
  [Create a SendGrid Account]: #createaccount
  [Reference the SendGrid .NET Class Library]: #reference
  [How to: Create an Email]: #createemail
  [How to: Send an Email]: #sendemail
  [How to: Add an Attachment]: #addattachment
  [How to: Use Filters to Enable Footers, Tracking, and Analytics]: #usefilters
  [How to: Use Additional SendGrid Services]: #useservices
  
  [special offer]: https://www.sendgrid.com/windowsazure.html
  
  [create-new-project]: ./media/sendgrid-dotnet-how-to-send-email/create_new_project.png
  [select-a-template]: ./media/sendgrid-dotnet-how-to-send-email/select_a_template.png
  [SendGrid-NuGet-package]: ./media/sendgrid-dotnet-how-to-send-email/sendgrid_nuget.png
  [azure_app_settings]: ./media/sendgrid-dotnet-how-to-send-email/app_settings.png
  [sendgrid csharp]: https://github.com/sendgrid/sendgrid-csharp
  [SMTP vs. Web API]: https://sendgrid.com/docs/Integrate/index.html
  [Configuración de la aplicación]: https://sendgrid.com/docs/API_Reference/SMTP_API/apps.html
  [Documentación de la API SendGrid]: https://sendgrid.com/docs
  
  [servicio de correo electrónico basado en la nube]: https://sendgrid.com/email-solutions
  [entrega de correo electrónico de transacciones]: https://sendgrid.com/transactional-email
 
