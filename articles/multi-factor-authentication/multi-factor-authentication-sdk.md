<properties 
    pageTitle="Integrar las identidades locales con Azure Active Directory."
    description="Se trata de Azure AD Connect que describe qué es y por qué debería usarlo."
    services="multi-factor-authentication"
    documentationCenter=""
    authors="kgremban"
    manager="femila"
    editor="curtand"/>

<tags
    ms.service="multi-factor-authentication"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/04/2016"
    ms.author="kgremban"/>

# <a name="building-multi-factor-authentication-into-custom-apps-sdk"></a>Autenticación multifactor de creación en aplicaciones personalizadas (SDK)

> [AZURE.IMPORTANT]  Si desea descargar el SDK, debe crear un proveedor de autenticación multifactor de Azure aunque tenga AMF Azure, AAD Premium o licencias EMS.  Si crea un proveedor de autenticación multifactor de Azure para ello y ya tiene licencias, es necesaria para crear el proveedor de servicios con el modelo **Por usuario habilitado** y vincular el proveedor de servicios de directorio que contiene las licencias de Azure AMF, Azure AD Premium o EMS.  Esto se asegurará de que no se cargarán a menos que tenga más usuarios únicos mediante el SDK de que el número de licencias que posee.

El Kit de desarrollo de Software (SDK) de Azure multifactor autenticación le permite crear llamada de teléfono y texto comprobación de mensaje directamente en los procesos de inicio de sesión o transacción de aplicaciones en su inquilino de Azure AD.

El SDK de autenticación multifactor está disponible para C#, Visual Basic (. NET), Java, Perl, PHP y Ruby. El SDK proporciona un contenedor fino alrededor de la autenticación multifactor. Todo lo que necesita escribir el código, incluidos archivos de código fuente de comentarios, archivos de ejemplo y un archivo Léame detallado incluye. Cada SDK también incluye un certificado y una clave privada para cifrar las transacciones exclusiva para su proveedor de autenticación multifactor. Siempre y cuando tenga un proveedor, puede descargar el SDK en tantos formatos e idiomas como necesite.

La estructura de la API en el SDK de autenticación multifactor es muy sencilla. Realizar una sola función llamar a una API con los parámetros de la opción de varios factores, como el modo de verificación y datos de usuario, como el número de teléfono o el número PIN para validar. Las API de traducen la llamada de función en solicitudes de servicios web al servicio de autenticación multifactor de Azure en la nube. Todas las llamadas deben incluir una referencia a la que se incluye en cada SDK el certificado privado.

Dado que las API no tiene acceso a los usuarios registrados en Azure Active Directory, debe proporcionar información de usuario, como números de teléfono y códigos PIN, en un archivo o una base de datos. Además, las API no proporciona características de administración de inscripción o usuario, por lo que necesita para crear estos procesos en la aplicación.






## <a name="download-the-azure-multi-factor-authentication-sdk"></a>Descargar la autenticación multifactor Azure SDK

Descargar el SDK de Azure multifactor requiere un [Proveedor de autenticación multifactor de Azure](multi-factor-authentication-get-started-auth-provider.md).  Esto requiere una suscripción completa Azure, incluso si pertenecen licencias AMF Azure, Azure AD Premium o serie de movilidad para empresas.  Para descargar el SDK, debe ir al Portal de administración de varios factores al administrar el proveedor de servicios de autenticación multifactor directamente, o haciendo clic en el vínculo **"Vaya al portal"** en la página de configuración del servicio MFA.


### <a name="to-download-the-azure-multi-factor-authentication-sdk-from-the-azure-portal"></a>Para descargar el SDK de autenticación multifactor de Azure desde el portal de Azure


1. Inicie sesión en el Portal de Azure como administrador.
2. En la parte izquierda, seleccione Active Directory.
3. En la página de Active Directory, en la parte superior haga clic en **Proveedores de autenticación multifactor**
4. En la parte inferior, haga clic en **Administrar**
5. Se abrirá una nueva página.  En la parte izquierda, en la parte inferior, haga clic en SDK.
<center>![Descargar](./media/multi-factor-authentication-sdk/download.png)</center>
6. Seleccione el idioma que desee y haga clic en uno de los vínculos de descarga.
7. Guarde la descarga.



### <a name="to-download-the-azure-multi-factor-authentication-sdk-via-the-service-settings"></a>Descargar el SDK de autenticación multifactor de Azure a través de la configuración del servicio


1. Inicie sesión en el Portal de Azure como administrador.
2. En la parte izquierda, seleccione Active Directory.
3. Haga doble clic en la instancia de Azure AD.
4. En la parte superior, haga clic en **Configurar**
5. En la autenticación multifactor, seleccione **Administrar la configuración de servicio**
![descargar](./media/multi-factor-authentication-sdk/download2.png)
6. En la página de configuración de servicios en la parte inferior de la pantalla, haga clic en **Ir al portal**.
![Descargar](./media/multi-factor-authentication-sdk/download3a.png)
7. Se abrirá una nueva página.  En la parte izquierda, en la parte inferior, haga clic en SDK.
8. Seleccione el idioma que desee y haga clic en uno de los vínculos de descarga.
9. Guarde la descarga.

## <a name="contents-of-the-azure-multi-factor-authentication-sdk"></a>Contenido de la autenticación multifactor Azure SDK
En el SDK encontrará los siguientes elementos:

- **Archivo Léame**. Se explica cómo usar las API de autenticación multifactor en una aplicación nueva o existente.
- **Los archivos de origen** para la autenticación multifactor
- **Certificado de cliente** que utiliza para comunicarse con el servicio de autenticación multifactor
- **Clave privada** para el certificado
- **Llamar a los resultados.** Una lista de códigos de resultado de la llamada. Para abrir este archivo, use una aplicación con formato de texto, como WordPad. Use los códigos de resultado de la llamada para probar y solucionar problemas de la implementación de la autenticación multifactor en la aplicación. No son los códigos de estado de autenticación.
- **Ejemplos.** Código de ejemplo para una implementación de trabajo básica de autenticación multifactor.


>[AZURE.WARNING]El certificado de cliente es un certificado privado único generado especialmente para usted. No compartir ni perder este archivo. Es la clave para garantizar la seguridad de las comunicaciones con el servicio de autenticación multifactor.

## <a name="code-sample-standard-mode-phone-verification"></a>Ejemplo de código: Modo estándar verificación por teléfono

Este ejemplo de código muestra cómo usar las API en el SDK de autenticación multifactor de Azure para agregar comprobación de llamada de voz de modo estándar a la aplicación. Modo estándar es una llamada de teléfono que el usuario responde al presionar la tecla #.

Este ejemplo usa C# .NET 2.0 multifactor autenticación SDK en una aplicación de ASP.NET básica con lógica de servidor de C#, pero el proceso es muy similar para las implementaciones simples en otros idiomas. Porque el SDK incluye archivos de origen, no ejecutable, puede generar los archivos y consultarlos o incluirlos directamente en la aplicación.

>[AZURE.NOTE]Al implementar la autenticación multifactor, use los factores adicionales como secundaria o tercer comprobación para completar el método de autenticación principal. Estos métodos no están diseñados para usarse como métodos de autenticación principal.

### <a name="code-sample-overview"></a>Muestra información general sobre código
Este código de ejemplo para una aplicación de demostración de web muy simple utiliza una llamada de teléfono con una respuesta de clave # para completar la autenticación del usuario. Este factor de llamada de teléfono se denomina en autenticación multifactor modo estándar.

El código de cliente no incluye todos los elementos específicos de autenticación multifactor. Dado que son independientes de la autenticación principal los factores de autenticación adicionales, puede agregarlas sin cambiar la interfaz de inicio de sesión existente. Las API en el SDK de varios factores permiten personalizar la experiencia del usuario, pero no tendrá que cambiar nada en absoluto.

El código de servidor agrega la autenticación de modo estándar en el paso 2. Crea un objeto PfAuthParams con los parámetros necesarios para la verificación del modo estándar: nombre de usuario, número de teléfono y el modo y la ruta de acceso al certificado de cliente (CertFilePath), que es necesario en cada llamada. Para ver una demostración de todos los parámetros de PfAuthParams, consulte el archivo de ejemplo en el SDK.

A continuación, el código pasa el objeto PfAuthParams a la función pf_authenticate(). El valor devuelto indica el éxito o error de la autenticación. Los parámetros de salida, callStatus y el identificador del error, contienen información de resultado de llamada adicionales. Los códigos de resultado de la llamada se documentan en el archivo de resultados de la llamada en el SDK.

Esta implementación mínima se puede escribir en un solo unas pocas líneas. Sin embargo, en el código de producción, debe incluir los errores más complejos, código de base de datos adicionales y una experiencia de usuario mejorada.

### <a name="web-client-code"></a>Código de cliente de Web

A continuación se muestra código de cliente de una página de demostración de web.


    <%@ Page Language="C#" AutoEventWireup="true" CodeFile="Default.aspx.cs" Inherits="_Default" %>

    <!DOCTYPE html>

    <html xmlns="http://www.w3.org/1999/xhtml">
    <head runat="server">
    <title>Multi-Factor Authentication Demo</title>
    </head>
    <body>
    <h1>Azure Multi-Factor Authentication Demo</h1>
    <form id="form1" runat="server">

    <div style="width:auto; float:left">
    Username:&nbsp;<br />
    Password:&nbsp;<br />
    </div>

    <div">
    <asp:TextBox id="username" runat="server" width="100px"/><br />
    <asp:Textbox id="password" runat="server" width="100px" TextMode="password" /><br />
    </div>

    <asp:Button id="btnSubmit" runat="server" Text="Log in" onClick="btnSubmit_Click"/>

    <p><asp:Label ID="lblResult" runat="server"></asp:Label></p>

    </form>
    </body>
    </html>


### <a name="server-side-code"></a>Código de servidor

En el siguiente código de servidor, autenticación multifactor está configurada y ejecutar en el paso 2. Modo estándar (MODE_STANDARD) es una llamada de teléfono a los que el usuario responde al presionar la tecla #.

    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Web;
    using System.Web.UI;
    using System.Web.UI.WebControls;

    public partial class _Default : System.Web.UI.Page
    {
        protected void Page_Load(object sender, EventArgs e)
        {
        }

        protected void btnSubmit_Click(object sender, EventArgs e)
        {
            // Step 1: Validate the username and password
            if (username.Text != "Contoso" || password.Text != "password")
            {
                lblResult.ForeColor = System.Drawing.Color.Red;
                lblResult.Text = "Username or password incorrect.";
            }
            else
            {
                // Step 2: Perform multi-factor authentication

                // Add call details from the user database.
                PfAuthParams pfAuthParams = new PfAuthParams();
                pfAuthParams.Username = username.Text;
                pfAuthParams.Phone = "9134884271";
                pfAuthParams.Mode = pf_auth.MODE_STANDARD;

                // Specify a client certificate
                // NOTE: This file contains the private key for the client
                // certificate. It must be stored with appropriate file
                // permissions.
                pfAuthParams.CertFilePath = "c:\\cert_key.p12";

                // Perform phone-based authentication
                int callStatus;
                int errorId;

                if(pf_auth.pf_authenticate(pfAuthParams, out callStatus, out errorId))
                {
                    lblResult.ForeColor = System.Drawing.Color.Green;
                    lblResult.Text = "Multi-Factor Authentication succeeded.";
                }
                else
                {
                    lblResult.ForeColor = System.Drawing.Color.Red;
                    lblResult.Text = " Multi-Factor Authentication failed.";
                }
            }

        }
    }
