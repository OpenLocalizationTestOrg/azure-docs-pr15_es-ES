<properties 
    pageTitle="Cómo realizar una llamada telefónica desde Twilio (Java) | Microsoft Azure" 
    description="Obtenga información sobre cómo realizar una llamada telefónica desde una página web con Twilio en una aplicación de Java en Azure." 
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

# <a name="how-to-make-a-phone-call-using-twilio-in-a-java-application-on-azure"></a>Cómo hacer una llamada de teléfono con Twilio en una aplicación de Java en Azure 

En el ejemplo siguiente se muestra cómo puede usar Twilio para realizar una llamada desde una página web hospedada en Azure. La aplicación resultante solicitará al usuario para los valores de la llamada de teléfono, como se muestra en la siguiente captura de pantalla.

![Formulario de Azure llamada mediante Twilio y Java][twilio_java]

Debe hacer lo siguiente para usar el código de este tema:

1. Adquirir una cuenta de Twilio y la autenticación de token. Para empezar con Twilio, evaluar precios en [http://www.twilio.com/pricing][twilio_pricing]. Puede registrarse en [https://www.twilio.com/try-twilio][try_twilio]. Obtenga información acerca de la API de Twilio [http://www.twilio.com/api][twilio_api].
2. Obtener la Twilio JAR. En [https://github.com/twilio/twilio-java][twilio_java_github], puede descargar los orígenes de GitHub y crear su propios JAR o descargar un JAR predefinido (con o sin dependencias).
El código de este tema se ha escrito usando el TARRO TwilioJava 3.3.8 con dependencias predefinido.
3. Agregar el TARRO a la ruta de acceso de compilación de Java.
4. Si está utilizando Eclipse para crear esta aplicación Java, incluya el JAR Twilio en el archivo de implementación de la aplicación (guerra) mediante la función de agregado de implementación de Eclipse. Si no está utilizando Eclipse para crear esta aplicación Java, asegúrese de la Twilio JAR es incluido en la misma función Azure que su aplicación de Java y se agrega a la ruta de acceso de clase de la aplicación.
5. Asegúrese de que el almacén de claves cacerts contiene el certificado de entidad de certificación segura Equifax con MD5 huella 67:CB:9 D: C0:13:24:8A:82:9B:B2:17:1E:D1:1B:EC:D4 (el número de serie es 35:DE:F4:CF y la huella digital SHA1 D2:32:09:AD:23:D3:14:23:21:74:E4:0 D: 7F:9 D: 62:13:97:86:63:3A). Este es el certificado de entidad emisora (CA) para la [https://api.twilio.com] [ twilio_api_service] servicio, que se llama cuando utilice Twilio APIs. Para obtener información sobre cómo agregar este certificado de entidad emisora de certificados al almacén de autoridad de certificación de su JDK, consulte [Agregar un certificado al almacén de certificados de entidad emisora de certificados de Java][add_ca_cert].

Además, estar familiarizado con la información en [crear una Hola mundo aplicación utilizando el Kit de herramientas de Azure para Eclipse][azure_java_eclipse_hello_world], o con otras técnicas para alojar aplicaciones de Java en Azure si no usa Eclipse, es muy recomendable.

## <a name="create-a-web-form-for-making-a-call"></a>Crear un formulario web para realizar una llamada

El código siguiente muestra cómo crear un formulario web para recuperar datos de usuario para realizar una llamada. Para el propósito de este ejemplo, se ha creado un nuevo proyecto de web dinámicos, denominado **TwilioCloud**, y **callform.jsp** se agregó como un archivo JSP.

    <%@ page language="java" contentType="text/html; charset=ISO-8859-1"
        pageEncoding="ISO-8859-1" %>
    <!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
    <html>
    <head>
    <meta http-equiv="Content-Type" content="text/html; charset=ISO-8859-1">
    <title>Automated call form</title>
    </head>
    <body>
     <p>Fill in all fields and click <b>Make this call</b>.</p>
     <br/>
      <form action="makecall.jsp" method="post">
       <table>
         <tr>
           <td>To:</td>
           <td><input type="text" size=50 name="callTo" value="" />
           </td>
         </tr>
         <tr>
           <td>From:</td>
           <td><input type="text" size=50 name="callFrom" value="" />
           </td>
         </tr>
         <tr>
           <td>Call message:</td>
           <td><input type="text" size=400 name="callText" value="Hello. This is the call text. Good bye." />
           </td>
         </tr>
         <tr>
           <td colspan=2><input type="submit" value="Make this call" />
           </td>
         </tr>
       </table>
     </form>
     <br/>
    </body>
    </html>

## <a name="create-the-code-to-make-the-call"></a>Crear el código para realizar la llamada
El código siguiente, que se llama cuando el usuario completa el formulario que se muestran de forma callform.jsp, crea el mensaje de llamada y genera la llamada. Para el propósito de este ejemplo, el archivo JSP se denomina **makecall.jsp** y se agregó al proyecto **TwilioCloud** . (Utilice su cuenta de Twilio y la autenticación de token en lugar de los valores de marcador de posición asignados a **accountSID** y **authToken** en el siguiente código).

    <%@ page language="java" contentType="text/html; charset=ISO-8859-1"
    import="java.util.*"
    import="com.twilio.*"
    import="com.twilio.sdk.*"
    import="com.twilio.sdk.resource.factory.*"
    import="com.twilio.sdk.resource.instance.*"
    pageEncoding="ISO-8859-1" %>
    <!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
    <html>
    <head>
    <meta http-equiv="Content-Type" content="text/html; charset=ISO-8859-1">
    <title>Call processing happens here</title>
    </head>
    <body>
        <b>This is my make call page.</b><p/>
     <%
    try 
    {
         // Use your account SID and authentication token instead
         // of the placeholders shown here.
         String accountSID = "your_twilio_account";
         String authToken = "your_twilio_authentication_token";
     
         // Instantiate an instance of the Twilio client.     
         TwilioRestClient client;
         client = new TwilioRestClient(accountSID, authToken);

         // Retrieve the account, used later to retrieve the CallFactory.
         Account account = client.getAccount();

         // Display the client endpoint. 
         out.println("<p>Using Twilio endpoint " + client.getEndpoint() + ".</p>");
     
         // Display the API version.
         String APIVERSION = TwilioRestClient.DEFAULT_VERSION;
         out.println("<p>Twilio client API version is " + APIVERSION + ".</p>");
    
         // Retrieve the values entered by the user.
         String callTo = request.getParameter("callTo");  
         // The Outgoing Caller ID, used for the From parameter,
         // must have previously been verified with Twilio.
         String callFrom = request.getParameter("callFrom");
         String userText = request.getParameter("callText");
     
         // Replace spaces in the user's text with '%20', 
         // to make the text suitable for a URL.
         userText = userText.replace(" ", "%20");
     
         // Create a URL using the Twilio message and the user-entered text.
         String Url="http://twimlets.com/message";
         Url = Url + "?Message%5B0%5D=" + userText;
     
         // Display the message URL.
         out.println("<p>");
         out.println("The URL is " + Url);
         out.println("</p>");
    
         // Place the call From, To and URL values into a hash map. 
         HashMap<String, String> params = new HashMap<String, String>();
         params.put("From", callFrom);
         params.put("To", callTo);
         params.put("Url", Url);
     
         CallFactory callFactory = account.getCallFactory();
         Call call = callFactory.create(params);
         out.println("<p>Call status: " + call.getStatus()  + "</p>"); 
    } 
    catch (TwilioRestException e) 
    {
        out.println("<p>TwilioRestException encountered: " + e.getMessage() + "</p>");
        out.println("<p>StackTrace: " + e.getStackTrace().toString() + "</p>");
    }
    catch (Exception e) 
    {
        out.println("<p>Exception encountered: " + e.getMessage() + "");
        out.println("<p>StackTrace: " + e.getStackTrace().toString() + "</p>");
    }
    %>
    </body>
    </html>

Además de la llamada, makecall.jsp muestra el extremo de Twilio, la versión de la API y el estado de la llamada. Un ejemplo es la siguiente captura de pantalla:

![Respuesta de llamada Azure mediante Twilio y Java][twilio_java_response]

## <a name="run-the-application"></a>Ejecute la aplicación
Siguientes son los pasos para ejecutar la aplicación; detalles de estos pasos se pueden encontrar al [crear una Hola mundo aplicación utilizando el Kit de herramientas de Azure para Eclipse][azure_java_eclipse_hello_world].

1. Exportar la guerra TwilioCloud a la carpeta de Azure **approot** . 
2. Modificar **startup.cmd** para descomprima el guerra TwilioCloud.
3. Compile la aplicación para el emulador de cálculo.
4. Iniciar la implementación en el emulador de cálculo.
5. Abra un explorador y ejecutar **http://localhost:8080/TwilioCloud/callform.jsp**.
6. Especificar valores en el formulario, haga clic en **realizar esta llamada**y, a continuación, ver los resultados en makecall.jsp.

Cuando esté listo para implementar Azure, vuelva a compilar para su implementación en la nube, implementar en Azure y ejecutar http://*your_hosted_name*.cloudapp.net/TwilioCloud/callform.jsp en el explorador (sustituir el valor para *your_hosted_name*).

## <a name="next-steps"></a>Pasos siguientes
Este código se proporcionó para mostrar la funcionalidad básica con Twilio en Java en Azure. Antes de implementar a Azure en producción, desea agregar más control de errores u otras características. Por ejemplo:

* En lugar de usar un formulario web, podría usar blobs de Azure almacenamiento o base de datos SQL para almacenar números de teléfono y llame a texto. Para obtener información sobre el uso de blobs de Azure almacenamiento en Java, vea [cómo usar el servicio de almacenamiento de blobs de Java][howto_blob_storage_java]. Para obtener información sobre el uso de la base de datos de SQL en Java, consulte [Uso de base de datos de SQL en Java][howto_sql_azure_java].
* Puede utilizar **RoleEnvironment.getConfigurationSettings** para recuperar el ID de cuenta de Twilio y token de autenticación de valores de configuración de la implementación, en lugar de codificar los valores de makecall.jsp. Para obtener información acerca de la clase **RoleEnvironment** , consulte el [uso de la biblioteca de tiempo de ejecución del servicio de Azure en JSP] [ azure_runtime_jsp] y la documentación de paquete de la ejecución del servicio de Azure en [http://dl.windowsazure.com/javadoc][azure_javadoc].
* El código de makecall.jsp asigna una dirección URL siempre Twilio, [http://twimlets.com/message][twimlet_message_url], a la variable de **dirección Url** . Esta dirección URL proporciona una respuesta de lenguaje de marcado de Twilio (TwiML) que informa Twilio cómo proceder con la llamada. Por ejemplo, puede contener el TwiML que devuelve una ** &lt;diga&gt; ** verbo que da como resultado texto entera al destinatario de la llamada. En lugar de usar la dirección URL Twilio le ha proporcionado, puede crear su propio servicio para responder a la solicitud de Twilio; Para obtener más información, vea [cómo usar Twilio para la voz y las capacidades de SMS en Java][howto_twilio_voice_sms_java]. Encontrará más información sobre TwiML en [http://www.twilio.com/docs/api/twiml][twiml]y obtener más información sobre ** &lt;diga&gt; ** y otros verbos Twilio se pueden encontrar en [http://www.twilio.com/docs/api/twiml/say][twilio_say].
* Lea las instrucciones de seguridad Twilio [https://www.twilio.com/docs/security][twilio_docs_security].

Para obtener información adicional sobre Twilio, consulte [https://www.twilio.com/docs][twilio_docs].

## <a name="see-also"></a>Vea también
* [Cómo usar Twilio para la voz y las capacidades SMS en Java][howto_twilio_voice_sms_java]
* [Agregar un certificado al almacén de certificados de entidad emisora de certificados de Java][add_ca_cert]

[twilio_pricing]: http://www.twilio.com/pricing
[try_twilio]: http://www.twilio.com/try-twilio
[twilio_api]: http://www.twilio.com/api
[verify_phone]: https://www.twilio.com/user/account/phone-numbers/verified#
[twilio_java_github]: http://github.com/twilio/twilio-java
[twimlet_message_url]: http://twimlets.com/message
[twiml]: http://www.twilio.com/docs/api/twiml
[twilio_api_service]: http://api.twilio.com
[add_ca_cert]: java-add-certificate-ca-store.md
[azure_java_eclipse_hello_world]: http://msdn.microsoft.com/library/windowsazure/hh690944.aspx
[howto_twilio_voice_sms_java]: partner-twilio-java-how-to-use-voice-sms.md
[howto_blob_storage_java]: http://www.windowsazure.com/develop/java/how-to-guides/blob-storage/
[howto_sql_azure_java]: http://msdn.microsoft.com/library/windowsazure/hh749029.aspx
[azure_runtime_jsp]: http://msdn.microsoft.com/library/windowsazure/hh690948.aspx
[azure_javadoc]: http://dl.windowsazure.com/javadoc
[twilio_docs_security]: http://www.twilio.com/docs/security
[twilio_docs]: http://www.twilio.com/docs
[twilio_say]: http://www.twilio.com/docs/api/twiml/say
[twilio_java]: ./media/partner-twilio-java-phone-call-example/WA_TwilioJavaCallForm.jpg
[twilio_java_response]: ./media/partner-twilio-java-phone-call-example/WA_TwilioJavaMakeCall.jpg
