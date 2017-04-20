<properties
    pageTitle="Cómo instalar silenciosamente el conector de Proxy de aplicación de AD Azure | Microsoft Azure"
    description="Trata sobre cómo realizar una instalación de conector de Proxy de aplicación de Azure AD para proporcionar acceso remoto seguro a sus aplicaciones locales silenciosa."
    services="active-directory"
    documentationCenter=""
    authors="kgremban"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="06/22/2016"
    ms.author="kgremban"/>

# <a name="how-to-silently-install-the-azure-ad-application-proxy-connector"></a>Cómo instalar silenciosamente el conector de Proxy de Azure AD aplicación

Desea poder enviar una secuencia de comandos de instalación a varios servidores de Windows o a los servidores de Windows que no tengan la interfaz de usuario habilitado. En este tema se explica cómo crear un script de Windows PowerShell que permite la instalación desatendida instalar y registrar el conector de Proxy de Azure AD aplicación.

## <a name="enabling-access"></a>Habilitar el acceso
Proxy de aplicación funciona si instala un servicio de Windows Server fino denominado conector dentro de la red. Para que el conector de Proxy de aplicación para que funcione, debe estar registrado con el directorio de Azure AD con un nombre y una contraseña de administrador global. Normalmente, esto se escribe durante la instalación de conector en un cuadro de diálogo emergente. Como alternativa, puede usar Windows PowerShell para crear un objeto de credencial para introducir su información de registro, o puede crear su propio token y usarlo para introducir su información de registro.

## <a name="step-1--install-the-connector-without-registration"></a>Paso 1: Instalar el conector sin registro


Instale el MSI de conector sin registrar el conector como sigue:


1. Abra un símbolo del sistema.
2. Ejecute el comando siguiente en la que el parámetro/q significa instalación silencioso: la instalación no le pedirá que acepte el contrato de licencia de usuario final.

        AADApplicationProxyConnectorInstaller.exe REGISTERCONNECTOR="false" /q

## <a name="step-2-register-the-connector-with-azure-active-directory"></a>Paso 2: Registrar el conector con Azure Active Directory
Esto se puede conseguir usando uno de los métodos siguientes:


- Registrar el conector mediante un objeto de credenciales de Windows PowerShell
- Registrar el conector utilizando un token creado sin conexión

### <a name="register-the-connector-using-a-windows-powershell-credential-object"></a>Registrar el conector mediante un objeto de credenciales de Windows PowerShell


1. Para crear el objeto de credenciales de Windows PowerShell, ejecute lo siguiente, donde "<username>"y"<password>" debe reemplazarse con el nombre de usuario y contraseña para el directorio:

        $User = "<username>"
        $PlainPassword = '<password>'
        $SecurePassword = $PlainPassword | ConvertTo-SecureString -AsPlainText -Force
        $cred = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $User, $SecurePassword

2. Vaya a **C:\Program Files\Microsoft AAD aplicación Proxy conector** y ejecutar la secuencia de comandos con el objeto de credenciales de PowerShell que creó, donde $cred es el nombre del PowerShell credenciales de objeto que ha creado:

        RegisterConnector.ps1 -modulePath "C:\Program Files\Microsoft AAD App Proxy Connector\Modules\" -moduleName "AppProxyPSModule" -Authenticationmode Credentials -Usercredentials $cred


### <a name="register-the-connector-using-a-token-created-offline"></a>Registrar el conector utilizando un token creado sin conexión

1. Crear un símbolo de sin conexión mediante la clase AuthenticationContext con los valores del fragmento de código:


        using System;
        using System.Diagnostics;
        using Microsoft.IdentityModel.Clients.ActiveDirectory;

        class Program
        {
        #region constants
        /// <summary>
        /// The AAD authentication endpoint uri
        /// </summary>
        static readonly Uri AadAuthenticationEndpoint = new Uri("https://login.windows.net/common/oauth2/token?api-version=1.0");

        /// <summary>
        /// The application ID of the connector in AAD
        /// </summary>
        static readonly string ConnectorAppId = "55747057-9b5d-4bd4-b387-abf52a8bd489";

        /// <summary>
        /// The reply address of the connector application in AAD
        /// </summary>
        static readonly Uri ConnectorRedirectAddress = new Uri("urn:ietf:wg:oauth:2.0:oob");

        /// <summary>
        /// The AppIdUri of the registration service in AAD
        /// </summary>
        static readonly Uri RegistrationServiceAppIdUri = new Uri("https://proxy.cloudwebappproxy.net/registerapp");

        #endregion

        #region private members
        private string token;
        private string tenantID;
        #endregion

        public void GetAuthenticationToken()
        {
            AuthenticationContext authContext = new AuthenticationContext(AadAuthenticationEndpoint.AbsoluteUri);

            AuthenticationResult authResult = authContext.AcquireToken(RegistrationServiceAppIdUri.AbsoluteUri,
                ConnectorAppId,
                ConnectorRedirectAddress,
                PromptBehavior.Always);

            if (authResult == null || string.IsNullOrEmpty(authResult.AccessToken) || string.IsNullOrEmpty(authResult.TenantId))
            {
                Trace.TraceError("Authentication result, token or tenant id returned are null");
                throw new InvalidOperationException("Authentication result, token or tenant id returned are null");
            }

            token = authResult.AccessToken;
            tenantID = authResult.TenantId;
        }





2. Una vez que tenga el token crear SecureString usando el token: <br>
`$SecureToken = $Token | ConvertTo-SecureString -AsPlainText -Force`
3. Ejecute el siguiente comando de Windows PowerShell, donde SecureToken es el nombre del token que creó anteriormente y tenantID es GUID de su inquilino: <br>
`RegisterConnector.ps1 -modulePath "C:\Program Files\Microsoft AAD App Proxy Connector\Modules\" -moduleName "AppProxyPSModule" -Authenticationmode Token -Token $SecureToken -TenantId <tenant GUID>`



## <a name="see-also"></a>Vea también

- [Habilitar a Proxy de aplicación para Azure Active Directory](active-directory-application-proxy-enable.md)
- [Publicar aplicaciones usando su propio nombre de dominio](active-directory-application-proxy-custom-domains.md)
- [Habilitar inicio de sesión único](active-directory-application-proxy-sso-using-kcd.md)
- [Solucionar problemas que tiene con Proxy de aplicación](active-directory-application-proxy-troubleshoot.md)

Para las últimas noticias y actualizaciones, consulte el [blog de Proxy de aplicación](http://blogs.technet.com/b/applicationproxyblog/)
