<properties
    pageTitle="Configurar el registro automático de dispositivos de dominio de Windows con Azure Active Directory | Microsoft Azure"
    description="Configurar los dispositivos de Windows dominio para registrar automáticamente con Azure Active Directory."
    services="active-directory"
    documentationCenter=""
    authors="markusvi"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/24/2016"
    ms.author="markvi"/>



# <a name="set-up-automatic-registration-of-windows-domain-joined-devices-with-azure-active-directory"></a>Configurar el registro automático de Windows dominio dispositivos con Azure Active Directory

Para usar [acceso condicional basado en dispositivo de Azure Active Directory](active-directory-conditional-access.md), los equipos de dominio de Windows deben estar registrados con Azure Active Directory (AD Azure). En este artículo, obtenga información sobre lo que debe hacer para configurar el registro de dispositivos de dominio de Windows con Azure AD de su organización.

Uso de acceso condicional en Azure AD proporciona estas ventajas:

-   Experiencia mejorada inicio de sesión único (SSO) en las aplicaciones de Azure AD a través de las cuentas de trabajo o escuela
-   Enterprise movilidad de configuración en todos los dispositivos
-   Acceso a la tienda Windows para la empresa
-   Autenticación más segura y sesión cómodo con Windows Hola

> [AZURE.NOTE] La actualización de noviembre de Windows 10 ofrece algunas de las experiencias de usuario mejorada en Azure AD, pero la actualización de Windows 10 aniversario admite totalmente acceso condicional basado en el dispositivo. Para obtener más información sobre el acceso condicional, consulte [acceso condicional basado en dispositivo de Azure Active Directory](active-directory-conditional-access.md). Para obtener más información acerca de los dispositivos de Windows 10 en el lugar de trabajo y cómo un usuario registra un dispositivo de Windows 10 con Azure AD, consulte [Windows 10 para la empresa: utilizar dispositivos para el trabajo](active-directory-azureadjoin-windows10-devices-overview.md).

Puede registrar algunas versiones anteriores de Windows, incluyendo estas versiones:

-   Windows 8.1
-   Windows 7

Si está utilizando un equipo con Windows Server como un equipo de escritorio, puede registrar estas plataformas:

- Windows Server 2016
- Windows Server 2012 R2
- Windows Server 2012
- Windows Server 2008 R2


## <a name="prerequisites"></a>Requisitos previos

El requisito principal para el registro automático de los dispositivos unido al dominio usando Azure AD es tener una versión actualizada de Azure Active Directory Connect (Azure AD Connect).

Dependiendo de la implementación de Azure AD Connect y, si utiliza una instalación express o personalizada o una actualización en contexto, los siguientes requisitos previos se hayan configurado automáticamente:

-   **Punto de conexión de servicio de Active Directory local**. Para la detección de la información del inquilino de Azure AD equipos que registrarse en Azure AD.

-   **Reglas de transformación de emisión de los servicios de federación de active Directory (AD FS)**. Para la autenticación de equipo en el registro (se aplica a configuraciones federadas).

Si algunos dispositivos en las organizaciones no son Windows 10 unido al dominio, asegúrese de que realizar las siguientes tareas:

- Establecer una directiva de Azure AD para que los usuarios pueden registrar dispositivos
- Configurar la autenticación de Windows integrada (IWA) como una alternativa válida para la autenticación multifactor de AD FS



## <a name="set-a-service-connection-point-for-discovery-of-the-azure-ad-tenant"></a>Establecer un punto de conexión de servicio para detección del inquilino de Azure AD

Un objeto de punto de conexión de servicio debe existir partición de contexto del bosque del dominio donde se unen los equipos de nomenclatura de configuración. El punto de conexión de servicio contiene información de detección sobre el inquilino de Azure AD que el registro de equipos. En una configuración de Active Directory de varios bosque, el punto de conexión de servicio debe existir en todos los bosques que tienen equipos unidos a un dominio.

Establecer el punto de conexión de servicio en estas ubicaciones en Active Directory:

    CN=62a0ff2e-97b9-4513-943f-0d221bd30080,CN=Device Registration Configuration,CN=Services,[Configuration Naming Context]

> [AZURE.NOTE] Para un bosque con el Active Directory nombre de dominio *ejemplo.com*, la configuración del contexto de nomenclatura es CN = Configuración, DC = ejemplo, DC = com.

Puede comprobar si los valores de detección de objeto del inquilino Azure AD con el siguiente script de Windows PowerShell. (Con el contexto de nomenclatura de configuración, reemplace el contexto de nomenclatura de configuración en el ejemplo).

    $scp = New-Object System.DirectoryServices.DirectoryEntry;

    $scp.Path = "LDAP://CN=62a0ff2e-97b9-4513-943f-0d221bd30080,CN=Device Registration Configuration,CN=Services,CN=Configuration,DC=example,DC=com";

    $scp.Keywords;

La `$scp.Keywords` resultado muestra la información del inquilino de Azure AD:

    azureADName:microsoft.com

    azureADId:72f988bf-86f1-41af-91ab-2d7cd011db47

Si no existe el punto de conexión, crear ejecutando el siguiente script de PowerShell en el servidor de Azure AD Connect:

    Import-Module -Name "C:\Program Files\Microsoft Azure Active Directory Connect\AdPrep\AdSyncPrep.psm1";

    $aadAdminCred = Get-Credential;

    Initialize-ADSyncDomainJoinedComputerSync –AdConnectorAccount [connector account name] -AzureADCredentials $aadAdminCred;


> [AZURE.NOTE]Cuando se ejecuta `$aadAdminCred = Get-Credential`, use el formato *user@example.com* el nombre de usuario en el cuadro de diálogo **Get-Credential** .  
> Cuando se ejecuta el cmdlet de inicialización ADSyncDomainJoinedComputerSync, reemplace [*nombre de la cuenta de conector*] con la cuenta de dominio que se usa en la cuenta del conector de Active Directory.  
> El cmdlet usa el módulo de Active Directory PowerShell, que se basa en servicios Web de Active Directory en un controlador de dominio. Servicios Web de Active Directory es compatible con controladores de dominio en Windows Server 2008 R2 y versiones posteriores. Controladores de dominio en Windows Server 2008 o versiones anteriores, use la API System.DirectoryServices mediante PowerShell para crear el punto de conexión de servicio y, a continuación, asignar los valores de palabras clave.

## <a name="create-ad-fs-rules-for-instant-device-registration-in-federated-organizations"></a>Crear reglas de AD FS para el registro del dispositivo de instantánea en las organizaciones federadas

En un Azure federado confían de dispositivos de configuración de AD, AD FS (o en el servidor de federación local) para autenticar a Azure AD. A continuación, registre con Azure Active Directory dispositivo de registro (servicio Azure AD dispositivo registro).

> [AZURE.NOTE] En una configuración no federado, se sincronizan los hash de contraseña de usuario a Azure AD y equipos unidos a un dominio de Windows 10 y Windows Server 2016 autentican en el servicio de registro de Azure AD dispositivo. Un usuario se autentica mediante una credencial que el usuario escribe en sus cuentas de equipo local, y que al Azure AD a través de Azure AD Connect. Para no Windows 10 y equipos de Windows Server 2016 en una configuración no federado, tiene opciones de configuración de una entidad de certificación basada en el dispositivo de su organización. Para obtener más información, vea los paquetes de Windows Installer de descargar sección para equipos que no sean de Windows 10 en este artículo.

Para equipos de Windows 10 y Windows Server 2016, Azure AD Connect asocia el objeto de dispositivo en Azure AD con el objeto de cuenta de equipo local. Las siguientes afirmaciones deben existir durante la autenticación de servicio de registro de Azure AD dispositivo completar el registro y crear el objeto del dispositivo:

- http://schemas.Microsoft.com/ws/2012/01/AccountType contiene el valor de DJ, que identifica el autenticador principal como un equipo unido al dominio.
- http://schemas.Microsoft.com/Identity/Claims/onpremobjectguid contiene el valor del atributo **GUID de objeto** de la cuenta de equipo local.
- http://schemas.Microsoft.com/ws/2008/06/Identity/Claims/primarysid contiene el identificador del equipo principal de seguridad (SID) que corresponde al valor del atributo de **objectSid** de la cuenta de equipo local.
- http://schemas.Microsoft.com/ws/2008/06/Identity/Claims/issuerid contiene el valor de Azure AD para confiar en el token emitido de AD FS o desde el local seguridad Token servicio (STS). Esto es importante en una configuración de Active Directory de varios bosque. En esta configuración, los equipos pueden unirse a un bosque distinto al que se conecta a Azure AD en AD FS o STS local. De AD FS, use el valor http://< el*nombre de dominio*>/adfs/services/confianza /, donde <*nombre de dominio*> es el nombre de dominio validada en Azure AD.

Para crear estas reglas de forma manual en AD FS, use el siguiente script de PowerShell en una sesión que está conectada al servidor. Reemplazar la primera línea con el nombre de dominio de su organización validada en Azure AD.

> [AZURE.NOTE] Si no usa AD FS para el servidor de federación local, siga las instrucciones de su proveedor para crear las reglas que emitir estas notificaciones.

    $validatedDomain = "example.com"      # Replace example.com with your organization's validated domain name in Azure AD

    $rule1 = '@RuleName = "Issue object GUID"

        c1:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", Value =~ "515$", Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"] &&

        c2:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname", Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"]

        => issue(store = "Active Directory", types = ("http://schemas.microsoft.com/identity/claims/onpremobjectguid"), query = ";objectguid;{0}", param = c2.Value);'

    $rule2 = '@RuleName = "Issue account type for domain-joined computers"

      c:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", Value =~ "515$", Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"]

      => issue(Type = "http://schemas.microsoft.com/ws/2012/01/accounttype", Value = "DJ");'

> [AZURE.NOTE] Use sólo las tres primeras reglas si su entorno local es un único bosque. Si los equipos están en un bosque diferente de la sincronización de Azure AD o si usa nombres alternativos para los de la configuración de sincronización, también debe incluir las reglas restantes.

    $rule3 = '@RuleName = "Pass through primary SID"

      c1:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", Value =~ "515$", Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"] &&

      c2:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/primarysid", Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"]

      => issue(claim = c2);'

    $rule4 = '@RuleName = "Issue AccountType with the value User when it’s not a computer account"

      NOT EXISTS([Type == "http://schemas.microsoft.com/ws/2012/01/accounttype", Value == "DJ"])

      => add(Type = "http://schemas.microsoft.com/ws/2012/01/accounttype", Value = "User");'

    $rule5 = '@RuleName = "Capture UPN when AccountType is User and issue the IssuerID"

      c1:[Type == "http://schemas.xmlsoap.org/claims/UPN"] &&

      c2:[Type == "http://schemas.microsoft.com/ws/2012/01/accounttype", Value == "User"]

      => issue(Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid", Value = regexreplace(c1.Value, ".+@(?<domain>.+)", "http://${domain}/adfs/services/trust/"));'

    $rule6 = '@RuleName = "Update issuer for DJ computer auth"

      c1:[Type == "http://schemas.microsoft.com/ws/2012/01/accounttype", Value == "DJ"]

      => issue(Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid", Value = "http://'+$validatedDomain+'/adfs/services/trust/");'

    $existingRules = (Get-ADFSRelyingPartyTrust -Identifier urn:federation:MicrosoftOnline).IssuanceTransformRules

    $updatedRules = $existingRules + $rule1 + $rule2 + $rule3 + $rule4 + $rule5 + $rule6

    $crSet = New-ADFSClaimRuleSet -ClaimRule $updatedRules

    Set-AdfsRelyingPartyTrust -TargetIdentifier urn:federation:MicrosoftOnline -IssuanceTransformRules $crSet.ClaimRulesString

> [AZURE.NOTE] Equipos unidos a un dominio de Windows 10 y Windows Server 2016 autentican mediante IWA a un extremo WS-Trust active hospedado por AD FS. Asegúrese de que el extremo está activo. Si está utilizando al Proxy de la aplicación Web, asegúrese de que se ha publicado este extremo a través del proxy. El extremo es adfs, servicios, confianza, 13/windowstransport. Para comprobar si está activo, en la consola de administración de AD FS vaya al **servicio** > **extremos**. Si no usa AD FS para el servidor de federación local, siga las instrucciones de su proveedor para asegurarse de que el extremo correspondiente está activo.



## <a name="set-up-ad-fs-for-authentication-of-device-registration"></a>Configurar AD FS para la autenticación de registro de dispositivo

Asegúrese de que IWA se establece como una alternativa válida para la autenticación multifactor para el registro de dispositivo en AD FS. Para ello, debe tener una emisión transformar regla que pasa a través del método de autenticación.

1. En la consola de administración de AD FS, vaya a **AD FS** > **Relaciones de confianza** > **Confiar confía de terceros**.

2. Haga clic en el objeto de confianza de fiesta confianza de plataforma de identidad de Microsoft Office 365 y, a continuación, seleccione **Editar reglas de notificación**.

3.  En la pestaña **Reglas de transformación de emisión** , seleccione **Agregar regla**.

4.  En la lista de plantilla de **regla de notificación** , seleccione **Enviar notificaciones usando una regla personalizada**.

5.  Seleccione **siguiente**.

6.  En el cuadro **nombre de la regla de notificación** , escriba **Auth método de notificación de regla**.

7.  En el cuadro de la **regla de notificación** , escriba este comando:

    `c:[Type == "http://schemas.microsoft.com/claims/authnmethodsreferences"]
    => issue(claim = c);`.

8. En el servidor de federación, escriba este comando de PowerShell:

    `Set-AdfsRelyingPartyTrust -TargetName <RPObjectName> -AllowedAuthenticationClassReferences wiaormultiauthn`

<*RPObjectName*> es el nombre de objeto de fiesta confianza para el objeto de confianza Azure AD fiesta de confianza. Este objeto normalmente se denomina *Plataforma de identidad de Microsoft Office 365*.



## <a name="deployment-and-rollout"></a>De implementación

Cuando los equipos de dominio cumplen los requisitos previos, está listos para registrar con Azure AD.

Los equipos de dominio Windows Update de aniversario de 10 y Windows Server 2016 automáticamente registrar con Azure AD la próxima vez que se reinicia el dispositivo o cuando un usuario inicia sesión en Windows. Nuevos equipos que se unen al dominio registrar con Azure AD al reinicia el dispositivo después de la operación de unirse al dominio.

> [AZURE.NOTE] Equipos de dominio de Windows 10 registrar automáticamente con Azure AD solo si se establece el objeto de directiva de grupo de distribución.

Puede usar un objeto de directiva de grupo para controlar la ejecución del registro automático de Windows 10 y equipos de dominio de Windows Server 2016. Para dar a conocer el registro automático de los equipos de dominio no Windows 10, puede implementar un paquete de Windows Installer en equipos que seleccione.

> [AZURE.NOTE] La directiva de grupo para control de implementación también se activa el registro de equipos unidos a un dominio de Windows 8.1. Puede usar la directiva para registrar equipos unidos a un dominio de Windows 8.1. O bien, si tiene una mezcla de versiones de Windows, incluidas las versiones de Windows 7 o Windows Server, puede registrar todos los equipos de Windows Server 2016 y no Windows 10, con un paquete de Windows Installer.

### <a name="create-a-group-policy-object-to-control-the-rollout-of-automatic-registration"></a>Crear un objeto de directiva de grupo para controlar la ejecución del registro automático

Para controlar la ejecución del registro automático de los equipos de dominio con Azure AD, puede implementar la directiva de grupo **registrar dominio une equipos como dispositivos** en los equipos que desea registrar. Por ejemplo, puede implementar la directiva a una unidad organizativa o a un grupo de seguridad.

Para establecer la directiva, siga estos pasos:

1. Abra el administrador del servidor y, a continuación, vaya a **Herramientas** > **Administración de directivas de grupo**.

2. Vaya al nodo de dominio que corresponde al dominio en el que desee activar el registro automático de los equipos de Windows 10 o Windows Server 2016.

3. Haga clic en **Los objetos de directiva de grupo**y, a continuación, seleccione **nuevo**.

4. Escriba un nombre para el objeto de directiva de grupo. Por ejemplo, en el *Registro automático para Azure AD*. Seleccione **Aceptar**.

4. Haga clic en el nuevo objeto de directiva de grupo y, a continuación, seleccione **Editar**.

5. Vaya a **Configuración del equipo** > **directivas** > **plantillas administrativas** > **componentes de Windows** > **registro de dispositivo**. Haga clic con el botón **registro de dominio unido a equipos como dispositivos**y, a continuación, seleccione **Editar**.

    > [AZURE.NOTE] Esta plantilla de directiva de grupo se ha cambiado de versiones anteriores de la consola de administración de directivas de grupo. Si está utilizando una versión anterior de la consola, vaya a **Configuración del equipo** > **directivas** > **Plantillas administrativas** > **Componentes de Windows** > **Combinación de área de trabajo** > **automáticamente trabajo unirse a los equipos cliente**.

6. Seleccione **habilitado**y, a continuación, seleccione **Aplicar**.

7. Seleccione **Aceptar**.

8. Vincular el objeto de directiva de grupo a una ubicación de su elección. Por ejemplo, puede vincular a una unidad organizativa específica. También puede vincularlo a un grupo de seguridad específicos de equipos que se registra automáticamente con Azure AD. Para establecer esta directiva para todos los equipos de Windows 10 y Windows Server 2016 Unidos a un dominio de su organización, vincular el objeto de directiva de grupo para el dominio.

### <a name="download-windows-installer-packages-for-non-windows-10-computers"></a>Descargue los paquetes de Windows Installer para equipos que no sean de Windows 10  

Para registrar unido al dominio que ejecutan Windows 8.1, Windows 7, Windows Server 2012 R2, Windows Server 2012 o Windows Server 2008 R2, puede descargar e instalar estos archivos de paquete (.msi) de Windows Installer:

- [x64](http://download.microsoft.com/download/C/A/7/CA79FAE2-8C18-4A8C-A4C0-5854E449ADB8/Workplace_x64.msi)
- [x86](http://download.microsoft.com/download/C/A/7/CA79FAE2-8C18-4A8C-A4C0-5854E449ADB8/Workplace_x86.msi)

Implementar el paquete mediante un sistema de distribución de software como el Administrador de configuración de System Center. El paquete es compatible con las opciones de instalación silencioso estándar con el parámetro *silencioso* . Administrador de configuración de System Center 2016 ofrece ventajas adicionales de versiones anteriores, como la capacidad de realizar un seguimiento de los registros completados. Para obtener más información, consulte [Centro de sistema 2016](https://www.microsoft.com/en-us/cloud-platform/system-center).

El programa de instalación, crea una tarea programada en el sistema que se ejecuta en el contexto del usuario. La tarea se activa cuando el usuario inicia sesión en Windows. La tarea silenciosamente registra el dispositivo con Azure AD con las credenciales de usuario después de autenticar a través de IWA. Para ver la tarea programada, vaya a **Microsoft** > **Unirse al lugar de trabajo**y, a continuación, vaya a la biblioteca del programador de tareas.



## <a name="next-steps"></a>Pasos siguientes

- [Acceso condicional de Azure Active Directory](active-directory-conditional-access.md)
