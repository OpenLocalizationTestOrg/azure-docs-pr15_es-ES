<properties 
    pageTitle="Cómo configurar la autenticación de TLS mutua aplicación Web" 
    description="Obtenga información sobre cómo configurar la aplicación web para usar autenticación de certificado de cliente en TLS." 
    services="app-service" 
    documentationCenter="" 
    authors="naziml" 
    manager="wpickett" 
    editor="jimbe"/>

<tags 
    ms.service="app-service" 
    ms.workload="na" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/08/2016" 
    ms.author="naziml"/>    

# <a name="how-to-configure-tls-mutual-authentication-for-web-app"></a>Cómo configurar la autenticación de TLS mutua aplicación Web

## <a name="overview"></a>Información general ##
Puede restringir el acceso a la aplicación web de Azure habilitando diferentes tipos de autenticación para el mismo. Una forma de hacerlo es autenticar mediante un certificado de cliente cuando la solicitud es sobre SSL/TLS. Este mecanismo se denomina autenticación de TLS mutua o certificado de cliente autenticación y en este artículo explican cómo configurar la aplicación web para usar autenticación de certificado de cliente.

> **Nota:** Si tiene acceso a su sitio a través de HTTP y HTTPS no, no recibirá ningún certificado de cliente. Así que si la aplicación requiere certificados de cliente no debe permitir las solicitudes a la aplicación sobre HTTP.


[AZURE.INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)] 

## <a name="configure-web-app-for-client-certificate-authentication"></a>Configurar la aplicación Web para la autenticación de certificado de cliente ##
Configurar la aplicación web para requerir certificados de cliente, que debe agregar la configuración del sitio de clientCertEnabled para la aplicación web y se establece en true. Esta opción no está disponible actualmente a través de la experiencia de administración en el Portal y la API de REST tendrán que se utilizará para ello.

Puede usar la [herramienta de ARMClient](https://github.com/projectkudu/ARMClient) para que sea más fácil crear la llamada API de REST. Una vez que inicie sesión con la herramienta debe usar el siguiente comando:

    ARMClient PUT subscriptions/{Subscription Id}/resourcegroups/{Resource Group Name}/providers/Microsoft.Web/sites/{Website Name}?api-version=2015-04-01 @enableclientcert.json -verbose
    
Reemplazar todo el contenido de {} con información para la aplicación web y la creación de un archivo llamado enableclientcert.json con el siguiente JSON contenido:

> {"ubicación": "Mi Web App ubicación",   
>   "propiedades de": {  
>     "clientCertEnabled": true}}  

Asegúrese de cambiar el valor de "ubicación" en el lugar donde se encuentra la aplicación web, por ejemplo, Norte Central de EE o etcetera de Estados Unidos oeste.

> **Nota:** Si ejecuta ARMClient de Powershell, debe salir del @ símbolo para el archivo JSON con un paso atrás '.

## <a name="accessing-the-client-certificate-from-your-web-app"></a>El acceso al certificado de cliente desde la aplicación Web ##
Si está utilizando ASP.NET y configurar la aplicación para utilizar la autenticación de certificado de cliente, el certificado estará disponible a través de la propiedad **HttpRequest.ClientCertificate** . Para otros pilas de aplicaciones, el certificado de cliente estará disponible en su aplicación a través de un valor de base 64 codificado en el encabezado de la solicitud "X-ARR-ClientCert". La aplicación puede crear un certificado de este valor y, a continuación, se utiliza para fines de autenticación y la autorización de la aplicación.

## <a name="special-considerations-for-certificate-validation"></a>Consideraciones especiales para la validación de certificado ##
El certificado de cliente que se envía a la aplicación no va a través de cualquier validación por la plataforma de aplicaciones Web de Azure. Validar este certificado es responsabilidad de la aplicación web. Aquí es el código de ASP.NET de ejemplo que valida propiedades de certificado para la autenticación.

    using System;
    using System.Collections.Specialized;
    using System.Security.Cryptography.X509Certificates;
    using System.Web;

    namespace ClientCertificateUsageSample
    {
        public partial class cert : System.Web.UI.Page
        {
            public string certHeader = "";
            public string errorString = "";
            private X509Certificate2 certificate = null;
            public string certThumbprint = "";
            public string certSubject = "";
            public string certIssuer = "";
            public string certSignatureAlg = "";
            public string certIssueDate = "";
            public string certExpiryDate = "";
            public bool isValidCert = false;

            //
            // Read the certificate from the header into an X509Certificate2 object
            // Display properties of the certificate on the page
            //
            protected void Page_Load(object sender, EventArgs e)
            {
                NameValueCollection headers = base.Request.Headers;
                certHeader = headers["X-ARR-ClientCert"];
                if (!String.IsNullOrEmpty(certHeader))
                {
                    try
                    {
                        byte[] clientCertBytes = Convert.FromBase64String(certHeader);
                        certificate = new X509Certificate2(clientCertBytes);
                        certSubject = certificate.Subject;
                        certIssuer = certificate.Issuer;
                        certThumbprint = certificate.Thumbprint;
                        certSignatureAlg = certificate.SignatureAlgorithm.FriendlyName;
                        certIssueDate = certificate.NotBefore.ToShortDateString() + " " + certificate.NotBefore.ToShortTimeString();
                        certExpiryDate = certificate.NotAfter.ToShortDateString() + " " + certificate.NotAfter.ToShortTimeString();
                    }
                    catch (Exception ex)
                    {
                        errorString = ex.ToString();
                    }
                    finally 
                    {
                        isValidCert = IsValidClientCertificate();
                        if (!isValidCert) Response.StatusCode = 403;
                        else Response.StatusCode = 200;
                    }
                }
                else
                {
                    certHeader = "";
                }
            }

            //
            // This is a SAMPLE verification routine. Depending on your application logic and security requirements, 
            // you should modify this method
            //
            private bool IsValidClientCertificate()
            {
                // In this example we will only accept the certificate as a valid certificate if all the conditions below are met:
                // 1. The certificate is not expired and is active for the current time on server.
                // 2. The subject name of the certificate has the common name nildevecc
                // 3. The issuer name of the certificate has the common name nildevecc and organization name Microsoft Corp
                // 4. The thumbprint of the certificate is 30757A2E831977D8BD9C8496E4C99AB26CB9622B
                //
                // This example does NOT test that this certificate is chained to a Trusted Root Authority (or revoked) on the server 
                // and it allows for self signed certificates
                //

                if (certificate == null || !String.IsNullOrEmpty(errorString)) return false;
                
                // 1. Check time validity of certificate
                if (DateTime.Compare(DateTime.Now, certificate.NotBefore) < 0 || DateTime.Compare(DateTime.Now, certificate.NotAfter) > 0) return false;
                
                // 2. Check subject name of certificate
                bool foundSubject = false;
                string[] certSubjectData = certificate.Subject.Split(new char[] { ',' }, StringSplitOptions.RemoveEmptyEntries);
                foreach (string s in certSubjectData)
                {
                    if (String.Compare(s.Trim(), "CN=nildevecc") == 0)
                    {
                        foundSubject = true;
                        break;
                    }
                }
                if (!foundSubject) return false;

                // 3. Check issuer name of certificate
                bool foundIssuerCN = false, foundIssuerO = false;
                string[] certIssuerData = certificate.Issuer.Split(new char[] { ',' }, StringSplitOptions.RemoveEmptyEntries);
                foreach (string s in certIssuerData)
                {
                    if (String.Compare(s.Trim(), "CN=nildevecc") == 0)
                    {
                        foundIssuerCN = true;
                        if (foundIssuerO) break;
                    }

                    if (String.Compare(s.Trim(), "O=Microsoft Corp") == 0)
                    {
                        foundIssuerO = true;
                        if (foundIssuerCN) break;
                    }
                }

                if (!foundIssuerCN || !foundIssuerO) return false;

                // 4. Check thumprint of certificate
                if (String.Compare(certificate.Thumbprint.Trim().ToUpper(), "30757A2E831977D8BD9C8496E4C99AB26CB9622B") != 0) return false;

                // If you also want to test if the certificate chains to a Trusted Root Authority you can uncomment the code below
                //
                //X509Chain certChain = new X509Chain();
                //certChain.Build(certificate);
                //bool isValidCertChain = true;
                //foreach (X509ChainElement chElement in certChain.ChainElements)
                //{
                //    if (!chElement.Certificate.Verify())
                //    {
                //        isValidCertChain = false;
                //        break;
                //    }
                //}
                //if (!isValidCertChain) return false;

                return true;
            }
        }
    }
