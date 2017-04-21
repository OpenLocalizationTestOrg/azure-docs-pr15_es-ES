<properties
    pageTitle="Usar Azure depósito clave desde una aplicación Web | Microsoft Azure"
    description="Use este tutorial para aprender a usar Azure clave depósito desde una aplicación web."
    services="key-vault"
    documentationCenter=""
    authors="adhurwit"
    manager="mbaldwin"
    tags="azure-resource-manager"/>

<tags
    ms.service="key-vault"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/05/2016"
    ms.author="adhurwit"/>

# <a name="use-azure-key-vault-from-a-web-application"></a>Usar Azure depósito clave desde una aplicación Web #

## <a name="introduction"></a>Introducción  
Use este tutorial para aprender a usar Azure clave depósito desde una aplicación web en Azure. Le guiará por el proceso de obtener acceso a un secreto de un depósito de Azure clave para que se puede usar en la aplicación web.

**Tiempo estimado para completar:** 15 minutos


Para obtener información general acerca de la cámara de clave de Azure, consulte [¿Qué es depósito de clave de Azure?](key-vault-whatis.md)

## <a name="prerequisites"></a>Requisitos previos

Para completar este tutorial, debe tener el siguiente:

- Un URI para un secreto en un depósito de clave de Azure
- Un identificador de cliente y un secreto de cliente para una aplicación web registran con Azure Active Directory que tiene acceso a su cámara clave
- Una aplicación web. Mostraremos los pasos necesarios para una aplicación de ASP.NET MVC implementado en Azure como una aplicación Web.

> [AZURE.NOTE]  Es fundamental que haya completado los pasos indicados en [Introducción a Azure depósito de clave de](key-vault-get-started.md) este tutorial para que tenga el URI a un secreto y el identificador de cliente y el secreto de cliente para una aplicación web.

La aplicación web que tendrá acceso a la cámara de clave es el que está registrado en Azure Active Directory y se ha concedido acceso a su depósito de clave. Si no es el caso, volver a registrar una aplicación en el tutorial de introducción y repita los pasos que se enumeran.

Este tutorial está diseñado para desarrolladores de web que entender los conceptos básicos de la creación de aplicaciones web en Azure. Para obtener más información sobre las aplicaciones Web de Azure, vea [Introducción a aplicaciones Web](../app-service-web/app-service-web-overview.md).



## <a id="packages"></a>Agregar paquetes de Nuget ##
Hay dos paquetes de la aplicación web necesita tener instalado.

- Active Directory Authentication Library - contiene métodos para interactuar con Azure Active Directory y administrar la identidad de usuario
- Biblioteca de depósito clave Azure - contiene métodos para interactuar con depósito de clave de Azure


Ambos de estos paquetes pueden instalarse mediante la consola del Administrador de paquete mediante el comando paquete de instalación.

    // this is currently the latest stable version of ADAL
    Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -Version 2.16.204221202

    Install-Package Microsoft.Azure.KeyVault


## <a id="webconfig"></a>Modificar Web.Config ##
Hay tres opciones de aplicación que se deben agregar al archivo web.config como sigue.

    <!-- ClientId and ClientSecret refer to the web application registration with Azure Active Directory -->
    <add key="ClientId" value="clientid" />
    <add key="ClientSecret" value="clientsecret" />

    <!-- SecretUri is the URI for the secret in Azure Key Vault -->
    <add key="SecretUri" value="secreturi" />


Si no va a hospedar la aplicación como una aplicación Web de Azure, debe agregar los valores reales de ClientId, secreto cliente y secreto URI a web.config. En caso contrario, deje estos valores ficticios porque se agregará los valores reales en el Portal de Azure para un nivel de seguridad adicional.


## <a id="gettoken"></a>Agregar método para obtener un acceso Token ##
Para utilizar la API del depósito de clave necesita un token de acceso. El cliente de cámara clave controla las llamadas a la API del depósito de clave, pero debe proporcionar con una función que obtiene el token de acceso.  

A continuación se muestra el código para obtener acceso a token de Azure Active Directory. Este código puede ir a cualquier parte de la aplicación. Deseo agregar una clase utilidades o EncryptionHelper.  

    //add these using statements
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    using System.Threading.Tasks;
    using System.Web.Configuration;

    //this is an optional property to hold the secret after it is retrieved
    public static string EncryptSecret { get; set; }

    //the method that will be provided to the KeyVaultClient
    public static async Task<string> GetToken(string authority, string resource, string scope)
    {
        var authContext = new AuthenticationContext(authority);
        ClientCredential clientCred = new ClientCredential(WebConfigurationManager.AppSettings["ClientId"],
                    WebConfigurationManager.AppSettings["ClientSecret"]);
        AuthenticationResult result = await authContext.AcquireTokenAsync(resource, clientCred);

        if (result == null)
            throw new InvalidOperationException("Failed to obtain the JWT token");

        return result.AccessToken;
    }

> [AZURE.NOTE] 
> Un identificador de cliente y el secreto de cliente es la manera más sencilla de autenticar una aplicación de Azure AD. Y uso de la aplicación web permite una separación de responsabilidades y tener más control sobre la administración de claves. Pero se basan en poner el secreto de cliente en las opciones de configuración que para algunos pueden ser tan arriesgadas como poner el secreto que desea proteger en la configuración. Consulte a continuación para obtener información sobre cómo usar un identificador de cliente y un certificado en lugar de identificador de cliente y el secreto de cliente para autenticar la aplicación de Azure AD.



## <a id="appstart"></a>Recuperar el secreto en Iniciar aplicación ##
Ahora se necesita código para llamar a la API del depósito clave y recuperar el secreto. El siguiente código se puede colocar en cualquier lugar mientras se llama antes de que necesite utilizarlo. He coloqué este código en el evento de inicio de la aplicación en Global.asax para que se ejecuta una vez en Inicio y hace que el secreto que esté disponible para la aplicación.

    //add these using statements
    using Microsoft.Azure.KeyVault;
    using System.Web.Configuration;

    // I put my GetToken method in a Utils class. Change for wherever you placed your method.
    var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(Utils.GetToken));

    var sec = kv.GetSecretAsync(WebConfigurationManager.AppSettings["SecretUri"]).Result.Value;

    //I put a variable in a Utils class to hold the secret for general  application use.
    Utils.EncryptSecret = sec;



## <a id="portalsettings"></a>Agregar opciones de configuración de aplicación en el Portal de Azure (opcional) ##
Si tiene una aplicación Web de Azure ahora puede agregar los valores reales para la AppSettings en el Portal de Azure. Al hacerlo, los valores reales no se en web.config, pero protegida a través del Portal donde tiene capacidades de control de acceso independientes. Estos valores se sustituirá para los valores que escribió en el archivo web.config. Asegúrese de que los nombres son los mismos.

![Configuración de la aplicación aparece en el Portal de Azure][1]


## <a name="authenticate-with-a-certificate-instead-of-a-client-secret"></a>Autenticar con un certificado en lugar de un secreto de cliente
Otra forma de autenticar una aplicación de Azure AD es mediante un identificador de cliente y un certificado en lugar de un identificador de cliente y el secreto de cliente. Siguientes son los pasos para usar un certificado en una aplicación Web de Azure:

1. Obtener o crear un certificado
2. Asociar el certificado con una aplicación de Azure AD
3. Agregar código a la aplicación Web para usar el certificado
4. Agregar un certificado a la aplicación Web


**Obtener o crear un certificado** Para nuestros fines ofrecemos un certificado de prueba. Estos son algunos de los comandos que puede usar en un símbolo del sistema de programador para crear un certificado. Cambiar el directorio donde desee que los archivos de certificado que debe crearse.

    makecert -sv mykey.pvk -n "cn=KVWebApp" KVWebApp.cer -b 07/31/2015 -e 07/31/2016 -r
    pvk2pfx -pvk mykey.pvk -spc KVWebApp.cer -pfx KVWebApp.pfx -po test123

Tome nota de la fecha de finalización y la contraseña para la .pfx (en este ejemplo: 31/07/2016 y test123). Necesitará a continuación.

Para obtener más información sobre cómo crear un certificado de prueba, vea [Cómo: crear el certificado de prueba propia](https://msdn.microsoft.com/library/ff699202.aspx)


**Asociar el certificado con una aplicación de Azure AD** Ahora que tiene un certificado, debe asociar a una aplicación de Azure AD. Pero el Portal de administración de Azure no admite esto ahora mismo. En su lugar debe usar Powershell. A continuación se muestran los comandos que necesita para ejecutar:

    $x509 = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2

    PS C:\> $x509.Import("C:\data\KVWebApp.cer")

    PS C:\> $credValue = [System.Convert]::ToBase64String($x509.GetRawCertData())

    PS C:\> $now = [System.DateTime]::Now

    # this is where the end date from the cert above is used
    PS C:\> $yearfromnow = [System.DateTime]::Parse("2016-07-31")

    PS C:\> $adapp = New-AzureRmADApplication -DisplayName "KVWebApp" -HomePage "http://kvwebapp" -IdentifierUris "http://kvwebapp" -KeyValue $credValue -KeyType "AsymmetricX509Cert" -KeyUsage "Verify" -StartDate $now -EndDate $yearfromnow

    PS C:\> $sp = New-AzureRmADServicePrincipal -ApplicationId $adapp.ApplicationId

    PS C:\> Set-AzureRmKeyVaultAccessPolicy -VaultName 'contosokv' -ServicePrincipalName $sp.ServicePrincipalName -PermissionsToSecrets all -ResourceGroupName 'contosorg'

    # get the thumbprint to use in your app settings
    PS C:\>$x509.Thumbprint

Después de ejecutar estos comandos, puede ver la aplicación en Azure AD. Si no ve la aplicación de la primera, busque "Aplicaciones es propietario de mi compañía" en lugar de "Aplicaciones de mi compañía utiliza".

Para obtener más información acerca de los objetos de aplicación de Azure AD y ServicePrincipal, consulte [objetos de la aplicación y Principal del servicio](../active-directory/active-directory-application-objects.md)



**Agregar el código de la aplicación Web para usar el certificado** Ahora agregaremos código a su aplicación Web para acceder el certificado y usarlo para la autenticación.

En primer lugar, hay código para tener acceso a los certificados.

    public static class CertificateHelper
    {
        public static X509Certificate2 FindCertificateByThumbprint(string findValue)
        {
            X509Store store = new X509Store(StoreName.My, StoreLocation.CurrentUser);
            try
            {
                store.Open(OpenFlags.ReadOnly);
                X509Certificate2Collection col = store.Certificates.Find(X509FindType.FindByThumbprint,
                    findValue, false); // Don't validate certs, since the test root isn't installed.
                if (col == null || col.Count == 0)
                    return null;
                return col[0];
            }
            finally
            {
                store.Close();
            }
        }
    }


Tenga en cuenta que el valor de StoreLocation CurrentUser en lugar de LocalMachine. Y que nos estamos proporcionando false al método Find como estamos usando un certificado de prueba.


A continuación es código que utiliza el CertificateHelper y crea un ClientAssertionCertificate que es necesario para la autenticación.

    public static ClientAssertionCertificate AssertionCert { get; set; }

    public static void GetCert()
    {
        var clientAssertionCertPfx = CertificateHelper.FindCertificateByThumbprint(WebConfigurationManager.AppSettings["thumbprint"]);
        AssertionCert = new ClientAssertionCertificate(WebConfigurationManager.AppSettings["clientid"], clientAssertionCertPfx);
    }


Este es el código nuevo para obtener el token de acceso. Esto reemplazará el método GetToken anterior. He dado un nombre diferente para su comodidad.

    public static async Task<string> GetAccessToken(string authority, string resource, string scope)
    {
        var context = new AuthenticationContext(authority, TokenCache.DefaultShared);
        var result = await context.AcquireTokenAsync(resource, AssertionCert);
        return result.AccessToken;
    }

He poner todo este código en la clase de utilidades de mi project Web App para facilitar su uso.

El último cambio de código está en el método Application_Start. En primer lugar, debemos llamar al método GetCert() para cargar el ClientAssertionCertificate. Y, a continuación, se cambia el método de devolución de llamada que proporcionamos al crear un nuevo KeyVaultClient. Tenga en cuenta que esto reemplazará el código de la anterior.

    Utils.GetCert();
    var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(Utils.GetAccessToken));


**Agregar un certificado a su aplicación Web a través del Portal de Azure** Agregar un certificado a su aplicación Web es un proceso de dos pasos simples. En primer lugar, vaya al Portal de Azure y vaya a la aplicación Web. En el módulo de configuración para la aplicación Web, haga clic en la entrada de "dominios personalizados y SSL". En el módulo que se abrirá, podrá cargar el certificado que ha creado anteriormente, KVWebApp.pfx, asegúrese de recordar la contraseña de la pfx.

![Agregar un certificado a una aplicación Web en el Portal de Azure][2]


Lo último que debe hacer es agregar una configuración de aplicación a la aplicación Web que tiene el sitio Web de nombre\_carga\_certificados y un valor de *. Esto se asegurará de que se hayan cargado todos los certificados. Si desea cargar sólo los certificados que ha cargado, a continuación, puede escribir una lista de valores separados por comas de sus huellas digitales.

Para obtener más información sobre cómo agregar un certificado a una aplicación Web, vea [Usar certificados en las aplicaciones de sitios Web de Azure](https://azure.microsoft.com/blog/2014/10/27/using-certificates-in-azure-websites-applications/)


**Agregar un certificado en depósito de clave como un secreto** En lugar de cargar el certificado al servicio Web App directamente, puede guardarlo en depósito de clave como secreto e implementar desde allí. Este es un proceso de dos pasos que se describe en la siguiente entrada de blog, [Implementar certificado Azure de aplicación Web a través de la cámara de clave](https://blogs.msdn.microsoft.com/appserviceteam/2016/05/24/deploying-azure-web-app-certificate-through-key-vault/)



## <a id="next"></a>Pasos siguientes ##


Para programar referencias, vea [Azure clave depósito cliente API de referencia de C#](https://msdn.microsoft.com/library/azure/dn903628.aspx).


<!--Image references-->
[1]: ./media/key-vault-use-from-web-application/PortalAppSettings.png
[2]: ./media/key-vault-use-from-web-application/PortalAddCertificate.png
