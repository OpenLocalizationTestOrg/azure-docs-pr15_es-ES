<properties
    pageTitle="Conectar dispositivos unido al dominio con Azure AD para experiencias de Windows 10 | Microsoft Azure"
    description="Explica cómo los administradores pueden configurar directivas de grupo para habilitar los dispositivos estén dominio unido a la red de la empresa."
    services="active-directory"
    documentationCenter=""
    authors="femila"
    manager="swadhwa"
    editor=""
    tags="azure-classic-portal"/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/27/2016"
    ms.author="femila"/>

# <a name="connect-domain-joined-devices-to-azure-ad-for-windows-10-experiences"></a>Conectar dispositivos unido al dominio con Azure AD para experiencias de Windows 10

Unirse a un dominio es que las organizaciones de manera tradicional conectar dispositivos para el trabajo de los últimos años 15 y mucho más. Ha habilitado los usuarios que inicie sesión sus dispositivos a través de su trabajo de Active Directory (Active Directory) de Windows Server cuentas profesionales o educativas y permitido TI para administrar completamente estos dispositivos. Las organizaciones normalmente se basan en imágenes métodos para dispositivos de aprovisionamiento a los usuarios y suele usar el Administrador de configuración de System Center (SCCM) o directiva de grupo para administrarlos.

Unirse a un dominio en Windows 10 proporcionará las siguientes ventajas después de conectar dispositivos a Azure Active Directory (AD Azure):

- Inicio de sesión único (SSO) a recursos de Azure AD desde cualquier lugar
- Acceso a la tienda Windows de empresa con cuentas de trabajo o escuela (ninguna cuenta Microsoft requerida)
- Compatible con Enterprise movilidad de configuración de usuario en dispositivos con cuentas de trabajo o escuela (ninguna cuenta Microsoft requerida)
- Autenticación segura y un inicio de sesión adecuado para las cuentas de trabajo o escuela con Microsoft Passport y Hola de Windows
- Capacidad para restringir el acceso únicamente a los dispositivos que cumplen con la configuración de directiva de grupo de organización de dispositivo

## <a name="prerequisites"></a>Requisitos previos

Unirse a un dominio sigue siendo útil. Sin embargo, para obtener las ventajas de Azure AD de SSO, movilidad de configuración con el trabajo o escuela cuentas y acceso a la tienda de Windows con cuentas de trabajo o escuela, tendrá las siguientes acciones:

- Suscripción de Azure AD
- Azure AD Connect para extender el directorio local a Azure AD
- Directiva que está establecido para conectar dispositivos unido al dominio con Azure AD
- Generación de Windows 10 (compilación 10551 o posterior) para dispositivos

Para habilitar Microsoft Passport para el trabajo y Windows Hola, también necesitará lo siguiente:

- Infraestructura de claves públicas (PKI) de emisión de certificados de usuario.
- Administrador de configuración de System Center versión 1509 para Technical Preview. Para obtener más información, vea [Microsoft sistema Centro de configuración de administrador de vista previa técnica](https://technet.microsoft.com/library/dn965439.aspx#BKMK_TP3Update) y el [Blog del equipo de administrador de configuración de System Center](http://blogs.technet.com/b/configmgrteam/archive/2015/09/23/now-available-update-for-system-center-config-manager-tp3.aspx). Esto es necesario para implementar certificados de usuario en función de claves de Microsoft Passport.

Como alternativa para el requisito de implementación de PKI, puede hacer lo siguiente:

- Hay algunos controladores de dominio con servicios de dominio de Windows Server 2016 Active Directory.

Para habilitar el acceso condicional, puede crear la configuración de la directiva de grupo que permite el acceso a los dispositivos de dominio con ninguna de las implementaciones. Para administrar el control de acceso basado en cumplimiento del dispositivo, necesita lo siguiente:

- Administrador de configuración de System Center versión 1509 para Technical Preview para escenarios de Passport

## <a name="deployment-instructions"></a>Instrucciones de implementación



### <a name="step-1-deploy-azure-active-directory-connect"></a>Paso 1: Implementar Azure Active Directory conectar

Azure AD Connect le permitirá proporcionando equipos locales como objetos de dispositivo en la nube. Para implementar Azure AD Connect, consulte "Instalar Azure AD Connect" en el artículo [integrar las identidades locales con Azure Active Directory](active-directory-aadconnect.md#install-azure-ad-connect).

 - Si ha seguido una [instalación personalizada para Azure AD Connect](./connect/active-directory-aadconnect-get-started-custom.md) (no la instalación Express), a continuación, siga el procedimiento **crear una conexión de servicio punto en local de Active Directory**, más adelante en este paso.
 - Si tiene una configuración federada con Azure AD antes de instalar Azure AD Connect (por ejemplo, si ha implementado servicios de federación de Active Directory (AD FS) antes de), a continuación, siga el procedimiento de **configurar AD FS reclamación reglas** , más adelante en este paso.

#### <a name="create-a-service-connection-point-in-on-premises-active-directory"></a>Crear un punto de conexión de servicio en local de Active Directory

Dominio dispositivos usará el punto de conexión de servicio para descubrir información del inquilino de Azure AD en el momento de registro automático con el servicio de registro de dispositivo de Azure.

En el servidor de Azure AD Connect, ejecute los siguientes comandos de PowerShell:

    Import-Module -Name "C:\Program Files\Microsoft Azure Active Directory Connect\AdPrep\AdSyncPrep.psm1";

    $aadAdminCred = Get-Credential;

    Initialize-ADSyncDomainJoinedComputerSync –AdConnectorAccount [connector account name] -AzureADCredentials $aadAdminCred;


Cuando se ejecuta el cmdlet $aadAdminCred = Get-Credential, use el formato *user@example.com* el nombre de usuario de la credencial que se introduce cuando aparece la ventana emergente de Get-Credential.

Cuando ejecute el cmdlet de inicialización ADSyncDomainJoinedComputerSync, reemplace [*nombre de la cuenta de conector*] con la cuenta de dominio que se usa como la cuenta del conector de Active Directory.

#### <a name="configure-ad-fs-claim-rules"></a>Configurar reglas de notificación de AD FS
Configuración de las reglas de la reclamación de AD FS permite registro instantáneo de un equipo con el servicio de registro de dispositivo Azure al permitir a los equipos autenticar mediante Kerberos o NTLM a través de AD FS. Sin este paso, equipos obtendrán a Azure AD de manera retrasada (sujeto a veces de sincronización de Azure AD Connect).

>[AZURE.NOTE]
Si no tiene AD FS como la federación servidor local, siga las instrucciones de su proveedor para crear las reglas de notificación.

En el servidor de AD FS (o en una sesión conectado al servidor AD FS), ejecute los siguientes comandos de PowerShell:

      <#----------------------------------------------------------------------
     |   Modify the Azure AD Relying Party to include the claims needed
     |   for DomainJoin++. The rules include:
     |   -ObjectGuid
     |   -AccountType
     |   -ObjectSid
     +---------------------------------------------------------------------#>

    $existingRules = (Get-ADFSRelyingPartyTrust -Identifier urn:federation:MicrosoftOnline).IssuanceTransformRules

    $rule1 = '@RuleName = "Issue object GUID"
          c1:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", Value =~ "515$", Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"] &&
          c2:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname", Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"]
          => issue(store = "Active Directory", types = ("http://schemas.microsoft.com/identity/claims/onpremobjectguid"), query = ";objectguid;{0}", param = c2.Value);'

    $rule2 = '@RuleName = "Issue account type for domain joined computers"
          c:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", Value =~ "515$", Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"]
          => issue(Type = "http://schemas.microsoft.com/ws/2012/01/accounttype", Value = "DJ");'

    $rule3 = '@RuleName = "Pass through primary SID"
          c1:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", Value =~ "515$", Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"] &&
          c2:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/primarysid", Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"]
          => issue(claim = c2);'

    $updatedRules = $existingRules + $rule1 + $rule2 + $rule3

    $crSet = New-ADFSClaimRuleSet -ClaimRule $updatedRules

    Set-AdfsRelyingPartyTrust -TargetIdentifier urn:federation:MicrosoftOnline -IssuanceTransformRules $crSet.ClaimRulesString

>[AZURE.NOTE]
Equipos de Windows 10 se autenticar mediante la autenticación integrada de Windows a un extremo WS-Trust active hospedado por AD FS. Asegúrese de que este extremo está habilitado. Si está utilizando la autenticación de Proxy de Web, asegurarse de que se ha publicado este extremo a través del proxy. Puede hacerlo activando adfs/services/confianza/13/windowstransport. Debe mostrar como habilitado en la consola de administración de AD FS en **servicio** > **extremos**.


### <a name="step-2-configure-automatic-device-registration-via-group-policy-in-active-directory"></a>Paso 2: Configurar el registro de dispositivo automática a través de la directiva de grupo de Active Directory

Puede usar la directiva de grupo de Active Directory para configurar los dispositivos de dominio de Windows 10 para registrar automáticamente con Azure AD.

> [AZURE.NOTE]
> Para ver instrucciones más recientes acerca de cómo configurar el registro de dispositivo automático, [cómo configurar el registro automático del dominio de Windows unido dispositivos con Azure Active Directory](active-directory-conditional-access-automatic-device-registration-setup.md).
>
> Esta plantilla de directiva de grupo se ha cambiado en Windows 10. Si está ejecutando la herramienta de directiva de grupo desde un equipo Windows 10, la directiva aparecerá como: <br>
> **Registrar equipos de dominio compartido como dispositivos**<br>
> La directiva se encuentra en la siguiente ubicación:<br>
> ***Registro de componentes o dispositivo de equipo Configuración de directivas o administrativa plantillas y Windows***


## <a name="additional-information"></a>Información adicional
* [Windows 10 para la empresa: formas de usar dispositivos para el trabajo](active-directory-azureadjoin-windows10-devices-overview.md)
* [Amplía las capacidades de nube para Windows 10 dispositivos a través de Azure Active Directory unirse](active-directory-azureadjoin-user-upgrade.md)
* [Obtenga información sobre escenarios de uso para unirse a AD de Azure](active-directory-azureadjoin-deployment-aadjoindirect.md)
* [Conectar dispositivos unido al dominio con Azure AD para experiencias de Windows 10](active-directory-azureadjoin-devices-group-policy.md)
* [Configurar combinación de Azure AD](active-directory-azureadjoin-setup.md)
