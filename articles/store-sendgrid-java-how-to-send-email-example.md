<properties 
    pageTitle="Store-sendgrid-Java-How-to-Send-email-example" 
    description="Cómo enviar correo electrónico con SendGrid de Java en una implementación de Azure" 
    services="" 
    documentationCenter="java" 
    authors="thinkingserious" 
    manager="sendgrid" 
    editor="mollybos"/>

<tags 
    ms.service="multiple" 
    ms.workload="na" 
    ms.tgt_pltfrm="na" 
    ms.devlang="Java" 
    ms.topic="article" 
    ms.date="10/30/2014" 
    ms.author="vibhork;dominic.may@sendgrid.com;elmer.thomas@sendgrid.com"/>

# <a name="how-to-send-email-using-sendgrid-from-java-in-an-azure-deployment"></a>Cómo enviar correo electrónico con SendGrid de Java en una implementación de Azure

En el ejemplo siguiente se muestra cómo puede usar SendGrid para enviar correos electrónicos desde una página web hospedada en Azure. La aplicación resultante solicitará al usuario para los valores de correo electrónico, como se muestra en la siguiente captura de pantalla.

![Formulario de correo electrónico][emailform]

El correo electrónico resultante tendrá un aspecto similar a la pantalla siguiente.

![Mensaje de correo electrónico][emailsent]

Debe hacer lo siguiente para usar el código de este tema:

1. Obtener la JARs javax.mail, por ejemplo de <http://www.oracle.com/technetwork/java/javamail/index.html>.
2. Agregar la JARs a la ruta de acceso de compilación de Java.
3. Si está utilizando Eclipse para crear esta aplicación Java, puede incluir las bibliotecas de SendGrid en el archivo de implementación de la aplicación (guerra) mediante la función de agregado de implementación de Eclipse. Si no está utilizando Eclipse para crear esta aplicación Java, asegúrese de las bibliotecas se incluyen dentro de la misma función Azure que su aplicación de Java y se agregan a la ruta de acceso de clase de la aplicación.


También debe tener su propio nombre de usuario de SendGrid y la contraseña, para poder enviar el correo electrónico. Para empezar con SendGrid, consulte [cómo enviar correo usando SendGrid de Java](store-sendgrid-java-how-to-send-email.md).

Además, se recomienda estar familiarizado con la información de [creación de una aplicación Hola a todos de Azure en Eclipse](http://msdn.microsoft.com/library/windowsazure/hh690944)o con otras técnicas para hospedar aplicaciones Java en Azure si no usa Eclipse.

## <a name="create-a-web-form-for-sending-email"></a>Crear un formulario web para enviar correo electrónico

El código siguiente muestra cómo crear un formulario web para recuperar datos de usuario para el envío de correo electrónico. Para el propósito de este contenido, el archivo JSP se denomina **emailform.jsp**.

    <%@ page language="java" contentType="text/html; charset=ISO-8859-1"
        pageEncoding="ISO-8859-1" %>
    <!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
    <html>
    <head>
    <meta http-equiv="Content-Type" content="text/html; charset=ISO-8859-1">
    <title>Email form</title>
    </head>
    <body>
     <p>Fill in all fields and click <b>Send this email</b>.</p>
     <br/>
      <form action="sendemail.jsp" method="post">
       <table>
         <tr>
           <td>To:</td>
           <td><input type="text" size=50 name="emailTo">
           </td>
         </tr>
         <tr>
           <td>From:</td>
           <td><input type="text" size=50 name="emailFrom">
           </td>
         </tr>
         <tr>
           <td>Subject:</td>
           <td><input type="text" size=100 name="emailSubject" value="My email subject">
           </td>
         </tr>
         <tr>
           <td>Text:</td>
           <td><input type="text" size=400 name="emailText" value="Hello,<p>This is my message.</p>Thank you." />
           </td>
         </tr>
         <tr>
           <td>SendGrid user name:</td>
           <td><input type="text" name="sendGridUser">
           </td>
         </tr>
         <tr>
           <td>SendGrid password:</td>
           <td><input type="password" name="sendGridPassword">
           </td>
         </tr>
         <tr>
           <td colspan=2><input type="submit" value="Send this email">
           </td>
         </tr>
       </table>
     </form>
     <br/>
    </body>
    </html>

## <a name="create-the-code-to-send-the-email"></a>Crear el código para enviar el correo electrónico

El código siguiente, que se llama cuando se complete el formulario en emailform.jsp, crea el mensaje de correo electrónico y lo envía. Para el propósito de este contenido, el archivo JSP se denomina **sendemail.jsp**.

    <%@ page language="java" contentType="text/html; charset=ISO-8859-1"
        pageEncoding="ISO-8859-1" import="javax.activation.*, javax.mail.*, javax.mail.internet.*, java.util.Date, java.util.Properties" %>
    <!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
    <html>
    <head>
    <meta http-equiv="Content-Type" content="text/html; charset=ISO-8859-1">
    <title>Email processing happens here</title>
    </head>
    <body>
        <b>This is my send mail page.</b><p/>
     <%
     
     final String sendGridUser = request.getParameter("sendGridUser");
     final String sendGridPassword = request.getParameter("sendGridPassword");
     
     class SMTPAuthenticator extends Authenticator
     {
       public PasswordAuthentication getPasswordAuthentication()
       {
            String username = sendGridUser;
            String password = sendGridPassword;
          
            return new PasswordAuthentication(username, password);   
       }
     }
     try
     {
         
         // The SendGrid SMTP server.
         String SMTP_HOST_NAME = "smtp.sendgrid.net";
    
         Properties properties;
        
         properties = new Properties();
         
         // Specify SMTP values.
         properties.put("mail.transport.protocol", "smtp");
         properties.put("mail.smtp.host", SMTP_HOST_NAME);
         properties.put("mail.smtp.port", 587);
         properties.put("mail.smtp.auth", "true");
         
         // Display the email fields entered by the user. 
         out.println("Value entered for email Subject: " + request.getParameter("emailSubject") + "<br/>");        
         out.println("Value entered for email      To: " + request.getParameter("emailTo") + "<br/>");
         out.println("Value entered for email    From: " + request.getParameter("emailFrom") + "<br/>");
         out.println("Value entered for email    Text: " + "<br/>" + request.getParameter("emailText") + "<br/>");
    
         // Create the authenticator object.
         Authenticator authenticator = new SMTPAuthenticator();
         
         // Create the mail session object.
         Session mailSession;
         mailSession = Session.getDefaultInstance(properties, authenticator);
         
         // Display debug information to stdout, useful when using the
         // compute emulator during development.
         mailSession.setDebug(true);
    
         // Create the message and message part objects.
         MimeMessage message;
         Multipart multipart;
         MimeBodyPart messagePart; 
         
         message = new MimeMessage(mailSession);
         
         multipart = new MimeMultipart("alternative");
         messagePart = new MimeBodyPart();
         messagePart.setContent(request.getParameter("emailText"), "text/html");
         multipart.addBodyPart(messagePart);            
    
         // Specify the email To, From, Subject and Content. 
         message.setFrom(new InternetAddress(request.getParameter("emailFrom")));
         message.addRecipient(Message.RecipientType.TO, new InternetAddress(request.getParameter("emailTo")));
         message.setSubject(request.getParameter("emailSubject")); 
         message.setContent(multipart);
         
         // Uncomment the following if you want to add a footer.
         // message.addHeader("X-SMTPAPI", "{\"filters\": {\"footer\": {\"settings\": {\"enable\":1,\"text/html\": \"<html>This is my <b>email footer</b>.</html>\"}}}}");
    
         // Uncomment the following if you want to enable click tracking.
         // message.addHeader("X-SMTPAPI", "{\"filters\": {\"clicktrack\": {\"settings\": {\"enable\":1}}}}");
         
         Transport transport;
         transport = mailSession.getTransport();
         // Connect the transport object.
         transport.connect();
         // Send the message.
         transport.sendMessage(message,  message.getRecipients(Message.RecipientType.TO));
         // Close the connection.
         transport.close();
     
        out.println("<p>Email processing completed.</p>");
         
     }
     catch (Exception e)
     {
         out.println("<p>Exception encountered: " + 
                            e.getMessage()     +
                            "</p>");   
     }
    %>
    
    </body>
    </html>

Además de enviar el correo electrónico, emailform.jsp proporciona un resultado para el usuario; un ejemplo es la siguiente captura de pantalla:

![Enviar resultado de correo][emailresult]

## <a name="next-steps"></a>Pasos siguientes

Implementar la aplicación en el emulador de cálculo dentro de un explorador ejecutar emailform.jsp especificar valores en el formulario, haga clic en **enviar este mensaje**y, a continuación, ver resultados en sendemail.jsp.

Este código se proporcionó para mostrar cómo usar SendGrid en Java en Azure. Antes de implementar a Azure en producción, desea agregar más control de errores u otras características. Por ejemplo: 

* Puede utilizar blobs de Azure almacenamiento o base de datos SQL para almacenar direcciones de correo electrónico y mensajes de correo electrónico, en lugar de usar un formulario web. Para obtener información sobre el uso de blobs de Azure almacenamiento de Java, vea [cómo usar el servicio de almacenamiento de blobs de Java](https://azure.microsoft.com/develop/java/how-to-guides/blob-storage/). Para obtener información sobre el uso de la base de datos de SQL en Java, consulte [Uso de base de datos de SQL en Java](https://azure.microsoft.com/develop/java/how-to-guides/using-sql-azure-in-java/).
* Podría usar `RoleEnvironment.getConfigurationSettings` para recuperar el nombre de usuario SendGrid y la contraseña de los valores de configuración de la implementación, en lugar de usar el formulario web para recuperar esos valores. Para obtener información sobre la `RoleEnvironment` de clase, consulte la documentación de paquete de la ejecución del servicio de Azure en <http://dl.windowsazure.com/javadoc>y [el uso de la biblioteca de tiempo de ejecución del servicio de Azure en JSP](http://msdn.microsoft.com/library/windowsazure/hh690948) .
* Para obtener más información sobre el uso de SendGrid en Java, vea [cómo enviar correo electrónico con SendGrid de Java](store-sendgrid-java-how-to-send-email.md).

[emailform]: ./media/store-sendgrid-java-how-to-send-email-example/SendGridJavaEmailform.jpg
[emailsent]: ./media/store-sendgrid-java-how-to-send-email-example/SendGridJavaEmailSent.jpg
[emailresult]: ./media/store-sendgrid-java-how-to-send-email-example/SendGridJavaResult.jpg
