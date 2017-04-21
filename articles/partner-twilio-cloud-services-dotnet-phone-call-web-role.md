<properties 
    pageTitle="Cómo realizar una llamada telefónica desde Twilio (.NET) | Microsoft Azure" 
    description="Obtenga información sobre cómo realizar una llamada telefónica y enviar un mensaje SMS con el servicio de la API Twilio en Azure. Ejemplos de código escritas en .NET." 
    services="" 
    documentationCenter=".net" 
    authors="devinrader" 
    manager="timlt" 
    editor=""/>

<tags 
    ms.service="cloud-services" 
    ms.workload="tbd" 
    ms.tgt_pltfrm="na" 
    ms.devlang="dotnet" 
    ms.topic="article" 
    ms.date="05/04/2016" 
    ms.author="microsofthelp@twilio.com"/>




# <a name="how-to-make-a-phone-call-using-twilio-in-a-web-role-on-azure"></a>Cómo hacer una llamada de teléfono con Twilio en una función web en Azure

Esta guía muestra cómo usar Twilio para realizar una llamada desde una página web hospedada en Azure. La aplicación resultante pide al usuario para los valores de la llamada de teléfono, como se muestra en la siguiente captura de pantalla.

![Formulario de Azure llamada mediante Twilio y ASP.NET][twilio_dotnet_basic_form]

## <a name="twilio-prereqs"></a>Requisitos previos

Debe hacer lo siguiente para usar el código de este tema:

1. Adquirir una cuenta de Twilio y la autenticación de token. Para empezar con Twilio, regístrese en [https://www.twilio.com/try-twilio][try_twilio]. Puede evaluar precios en [http://www.twilio.com/pricing][twilio_pricing]. Obtenga información acerca de la API de Twilio [http://www.twilio.com/voice/api][twilio_api].
2. Agregar la biblioteca de .NET Twilio a su rol de web. Vea "para agregar las bibliotecas Twilio a su proyecto de rol web," más adelante en este tema.

Debe estar familiarizado con la creación de una función web básica en Azure.

## <a name="howtocreateform"></a>Cómo: crear un formulario web para realizar una llamada

<a id="use_nuget"></a>Para agregar las bibliotecas Twilio a su proyecto de rol de web:

1.  Abra la solución en Visual Studio.
2.  Haga clic en **referencias**.
3.  Haga clic en **Administrar paquetes de NuGet**.
4.  Haga clic en **línea**.
5.  En el cuadro de búsqueda en línea, escriba *twilio*.
6.  Haga clic en **instalar** el paquete Twilio.

El código siguiente muestra cómo crear un formulario web para recuperar datos de usuario para realizar una llamada. En este ejemplo, se crea una función de web ASP.NET denominada **TwilioCloud** .

    <%@ Page Title="Home Page" Language="C#" MasterPageFile="~/Site.master"
        AutoEventWireup="true" CodeBehind="Default.aspx.cs"
        Inherits="WebRole1._Default" %>

    <asp:Content ID="HeaderContent" runat="server" ContentPlaceHolderID="HeadContent">
    </asp:Content>
    <asp:Content ID="BodyContent" runat="server" ContentPlaceHolderID="MainContent">
        <div>
            <asp:BulletedList ID="varDisplay" runat="server" BulletStyle="NotSet">
            </asp:BulletedList>
        </div>
        <div>
            <p>Fill in all fields and click <b>Make this call</b>.</p>
            <div>
                To:<br /><asp:TextBox ID="toNumber" runat="server" /><br /><br />
                Message:<br /><asp:TextBox ID="message" runat="server" /><br /><br />
                <asp:Button ID="callpage" runat="server" Text="Make this call"
                    onclick="callpage_Click" />
            </div>
        </div>
    </asp:Content>

## <a id="howtocreatecode"></a>Cómo: crear el código para realizar la llamada
El código siguiente, que se llama cuando el usuario complete el formulario, crea el mensaje de llamada y genera la llamada. En este ejemplo, el código se ejecuta en el controlador de eventos onclick del botón en el formulario. (Utilice su cuenta de Twilio y la autenticación de token en lugar de los valores de marcador de posición asignados a **accountSID** y **authToken** en el siguiente código).

    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Web;
    using System.Web.UI;
    using System.Web.UI.WebControls;
    using Twilio;

    namespace WebRole1
    {
        public partial class _Default : System.Web.UI.Page
        {
            protected void Page_Load(object sender, EventArgs e)
            {

            }

            protected void callpage_Click(object sender, EventArgs e)
            {
                // Call porcessing happens here.

                // Use your account SID and authentication token instead of
                // the placeholders shown here.
                string accountSID = "ACNNNNNNNNNNNNNNNNNNNNNNNNNNNNNN";
                string authToken =  "NNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNN";

                // Instantiate an instance of the Twilio client.
                TwilioRestClient client;
                client = new TwilioRestClient(accountSID, authToken);

                // Retrieve the account, used later to retrieve the
                Twilio.Account account = client.GetAccount();
                string APIversuion = client.ApiVersion;
                string TwilioBaseURL = client.BaseUrl;

                this.varDisplay.Items.Clear();
                if (this.toNumber.Text == "" || this.message.Text == "")
                {
                    this.varDisplay.Items.Add(
                            "You must enter a phone number and a message.");
                }
                else
                {
                    // Retrieve the values entered by the user.
                    string to = this.toNumber.Text;
                    string myMessage = this.message.Text;

                    // Create a URL using the Twilio message and the user-entered
                    // text. You must replace spaces in the user's text with '%20'
                    // to make the text suitable for a URL.
                    String Url = "http://twimlets.com/message?Message%5B0%5D="
                            + myMessage.Replace(" ", "%20");

                    // Display the endpoint, API version, and the URL for the message.
                    this.varDisplay.Items.Add("Using Twilio endpoint "
                        + TwilioBaseURL);
                    this.varDisplay.Items.Add("Twilioclient API Version is "
                        + APIversuion);
                    this.varDisplay.Items.Add("The URL is " + Url);

                    // Instantiate the call options that are passed
                    // to the outbound call.
                    CallOptions options = new CallOptions();

                    // Set the call From, To, and URL values.                    
                    options.From = "+14155992671";
                    options.To = to;
                    options.Url = Url;

                    // Place the call.
                    var call = client.InitiateOutboundCall(options);
                    this.varDisplay.Items.Add("Call status: " + call.Status);
                }
            }
        }
    }

Se realiza la llamada y se muestran el extremo Twilio, la versión de la API y el estado de la llamada. La siguiente captura de pantalla muestra el resultado de una ejecución de ejemplo.

![Respuesta de llamada Azure mediante Twilio y ASP.NET][twilio_dotnet_basic_form_output]

Encontrará más información sobre TwiML en [http://www.twilio.com/docs/api/twiml][twiml]. Más información sobre &lt;diga&gt; y otros verbos Twilio se pueden encontrar en [http://www.twilio.com/docs/api/twiml/say][twilio_say].

## <a id="nextsteps"></a>Pasos siguientes
Este código se proporcionó para mostrar la funcionalidad básica con Twilio en un rol web ASP.NET en Azure. Antes de implementar a Azure en producción, desea agregar más control de errores u otras características. Por ejemplo:

* En lugar de usar un formulario web, podría usar el almacenamiento de blobs de Windows Azure o una instancia de base de datos de SQL Azure para almacenar números de teléfono y llame a texto. Para obtener información sobre el uso de blobs de Azure, consulte [cómo usar el servicio de almacenamiento de blobs de Windows Azure en .NET][howto_blob_storage_dotnet]. Para obtener información sobre el uso de la base de datos de SQL, vea [cómo usar la base de datos de Azure SQL en aplicaciones .NET][howto_sql_azure_dotnet].
* Podría usar RoleEnvironment.getConfigurationSettings para recuperar el ID de cuenta de Twilio y token de autenticación de los valores de configuración de la implementación, en lugar de codificar los valores en el formulario. Para obtener información acerca de la clase RoleEnvironment, vea [Namespace Microsoft.WindowsAzure.ServiceRuntime][azure_runtime_ref_dotnet].
* Lea las instrucciones de seguridad Twilio [https://www.twilio.com/docs/security][twilio_docs_security].
* Para obtener más información sobre Twilio en [https://www.twilio.com/docs][twilio_docs].

##<a name="seealso"></a>Vea también
* [Cómo usar Twilio para la voz y las capacidades SMS de Azure](twilio-dotnet-how-to-use-for-voice-sms.md)

[twilio_pricing]: http://www.twilio.com/pricing
[try_twilio]: http://www.twilio.com/try-twilio
[twilio_api]: http://www.twilio.com/voice/api
[verify_phone]: https://www.twilio.com/user/account/phone-numbers/verified#

[twilio_dotnet_basic_form]: ./media/partner-twilio-cloud-services-dotnet-phone-call-web-role/WA_twilio_dotnet_basic_form.png
[twilio_dotnet_basic_form_output]: ./media/partner-twilio-cloud-services-dotnet-phone-call-web-role/WA_twilio_dotnet_basic_form_output.png

[twiml]: http://www.twilio.com/docs/api/twiml



[howto_twilio_voice_sms_dotnet]: /develop/net/how-to-guides/twilio/

[howto_blob_storage_dotnet]: https://www.windowsazure.com/develop/net/how-to-guides/blob-storage/

[howto_sql_azure_dotnet]: https://www.windowsazure.com/develop/net/how-to-guides/sql-database/


[twilio_docs_security]: http://www.twilio.com/docs/security
[twilio_docs]: http://www.twilio.com/docs
[twilio_say]: http://www.twilio.com/docs/api/twiml/say


[azure_runtime_ref_dotnet]: http://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.serviceruntime.aspx
