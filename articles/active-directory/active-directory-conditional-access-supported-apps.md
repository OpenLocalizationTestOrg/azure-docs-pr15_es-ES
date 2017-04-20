<properties
    pageTitle="Aplicaciones que utilicen las reglas de acceso condicional de Azure Active Directory | Microsoft Azure"
    description="Con control de acceso condicional Azure Active Directory busca condiciones específicas cuando autentica al usuario y para permitir el acceso de la aplicación."
    services="active-directory"
    documentationCenter=""
    authors="markusvi"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="identity"
    ms.date="10/26/2016"
    ms.author="markvi"/>

# <a name="applications-that-use-conditional-access-rules-in-azure-active-directory"></a>Aplicaciones que utilicen las reglas de acceso condicional de Azure Active Directory

Reglas de acceso condicional son compatibles con Azure Active Directory (AD Azure)-aplicaciones conectadas, integrada previamente federado software como un servicio (SaaS) aplicaciones, que usan la contraseña de inicio de sesión único (SSO), aplicaciones de línea de negocio y las aplicaciones que utilizan Proxy de aplicación de Azure AD. Para obtener una lista detallada de las aplicaciones que puede utilizar el acceso condicional, vea [servicios iniciados con acceso condicional](active-directory-conditional-access-technical-reference.md#Services-enabled-with-conditional-access). Acceso condicional funciona tanto con aplicaciones móviles y de escritorio que usan autenticación moderna. En este artículo, trataremos cómo condicional funciona de acceso en las aplicaciones móviles y de escritorio.

Puede usar páginas de inicio de sesión de Azure AD en las aplicaciones que usan autenticación moderna. Con una página de inicio de sesión, se solicita un usuario para la autenticación multifactor. Se muestra un mensaje si se bloquea el acceso del usuario. Autenticación moderna es necesaria para el dispositivo autenticar con Azure AD para que se evalúan las directivas de acceso condicional basado en el dispositivo.

Es importante conocer las aplicaciones que pueden usar las reglas de acceso condicional y los pasos que debe seguir para proteger otros puntos de entrada de la aplicación.

## <a name="applications-that-use-modern-authentication"></a>Aplicaciones que usan autenticación moderna

> [AZURE.NOTE] Si tiene una directiva de acceso condicional en Azure AD que tiene un equivalente en Office 365, configurar las directivas de acceso condicional. Esto se aplica, por ejemplo, a las directivas de acceso condicional para Exchange Online o SharePoint Online.

Admiten acceso condicional de las siguientes aplicaciones para Office 365 y otras aplicaciones de servicio de Azure AD conectados:

| Servicio de destino  | Plataforma  | Aplicación                                                  |
|--------------|-----------------|----------------------------------------------------------------|
|Exchange Online de Office 365 | Windows 10|Aplicación de correo o calendario o personas, Outlook 2016, Outlook 2013 (con autenticación moderna), Skype empresarial (con autenticación moderna)|
|Exchange Online de Office 365| Windows 8.1 y Windows 7 |Outlook 2016, Outlook 2013 (con autenticación moderna), Skype empresarial (con autenticación moderna)|
|Exchange Online de Office 365|iOS, Android|  Aplicación móvil de Outlook|
|Exchange Online de Office 365|Mac OS X| Outlook 2016 para la autenticación multifactor y la ubicación. compatibilidad con directivas basadas en el dispositivo planificado en el futuro, Skype para Business support planeado para el futuro|
|Office 365 SharePoint Online|Windows 10| Aplicaciones de Office 2016, aplicaciones de Office Universal, Office 2013 (con autenticación moderna), OneDrive para soporte de aplicación de empresa (cliente de sincronización de generación siguiente o NGSC) planificado en el futuro, soporte de grupos de Office planificado para el futuro, soporte de aplicación de SharePoint planificado para el futuro|
|Office 365 SharePoint Online|Windows 8.1 y Windows 7|Aplicaciones de Office 2016, Office 2013 (con autenticación moderna), OneDrive para la aplicación de empresa (cliente de sincronización de Groove)|
|Office 365 SharePoint Online|iOS, Android|  Aplicaciones de Office mobile |
|Office 365 SharePoint Online|Mac OS X| Aplicaciones de Office 2016 para la autenticación multifactor y la ubicación. compatibilidad con directivas basadas en el dispositivo planeado para el futuro|
|Yammer de Office 365|Windows 10, iOS y Android | Aplicación de Yammer de Office|
|Dynamics CRM|Windows 10, Windows 8.1, Windows 7, iOS y Android | Aplicación de Dynamics CRM|
|Servicio de PowerBI|Windows 10, Windows 8.1, Windows 7, iOS y Android | Aplicación de PowerBI|
|Servicio de aplicación remota de Azure|Windows 10, Windows 8.1, Windows 7, iOS, Android y Mac OS X |Aplicación remota Azure|
|Cualquier servicio de aplicación de mis aplicaciones|Android y iOS|Cualquier servicio de aplicación de mis aplicaciones |


## <a name="applications-that-do-not-use-modern-authentication"></a>Aplicaciones que no usan autenticación moderna

Actualmente, debe usar otros métodos para bloquear el acceso a aplicaciones que no usan autenticación moderna. No se aplican las reglas de acceso para las aplicaciones que no se usan autenticación moderna por access condicional. Principalmente se trata de una cuenta de acceso de Exchange y SharePoint. La mayoría de las versiones anteriores de aplicaciones utilizan protocolos de control de acceso a versiones anteriores.

### <a name="control-access-in-office-365-sharepoint-online"></a>Controlar el acceso de Office 365 SharePoint Online
Puede deshabilitar protocolos heredados para el acceso a SharePoint mediante el cmdlet Set-SPOTenant. Use este cmdlet para evitar que a los clientes de Office que utilizan protocolos de autenticación no moderno de acceso a los recursos de SharePoint Online.

**Comando de ejemplo**:    `Set-SPOTenant -LegacyAuthProtocolsEnabled $false`

### <a name="control-access-in-office-365-exchange-online"></a>Controlar el acceso de Exchange en línea de Office 365

Exchange ofrece dos categorías principales de protocolos. Revise las siguientes opciones y, a continuación, seleccione la directiva que es la adecuada para su organización.

-   **Exchange ActiveSync**. De forma predeterminada, no se aplican las directivas de acceso condicional para autenticación multifactor y la ubicación de Exchange ActiveSync. Es necesario proteger el acceso a estos servicios mediante la configuración de directiva de Exchange ActiveSync directamente o mediante el bloqueo de Exchange ActiveSync mediante reglas de los servicios de federación de Active Directory (AD FS).
-   **Protocolos heredado**. Puede bloquear protocolos heredados con AD FS. Esto bloquea el acceso a los clientes de Office anteriores, como Office 2013 sin autenticación moderna habilitada y versiones anteriores de Office.


### <a name="use-ad-fs-to-block-legacy-protocol"></a>Utilizar AD FS para bloquear el protocolo heredado

Puede usar las reglas de ejemplo siguientes para bloquear el acceso de protocolo heredado en el nivel de AD FS. Elegir entre dos configuraciones comunes.

#### <a name="option-1-allow-exchange-activesync-and-allow-legacy-apps-but-only-on-the-intranet"></a>Opción 1: Permiten a Exchange ActiveSync y aplicaciones heredadas, pero sólo en la intranet

Al aplicar las siguientes reglas de tres a la confianza de confianza de terceros para la plataforma de identidad de Microsoft Office 365, el tráfico de Exchange ActiveSync y el explorador y el tráfico de autenticación moderna de AD FS, tiene acceso. Aplicaciones heredadas están bloqueadas desde la extranet.

##### <a name="rule-1"></a>Regla 1

    @RuleName = “Allow all intranet traffic”
    c1:[Type == "http://schemas.microsoft.com/ws/2012/01/insidecorporatenetwork", Value == "true"]
    => issue(Type = "http://schemas.microsoft.com/authorization/claims/permit", Value = "true");

##### <a name="rule-2"></a>Regla 2

    @RuleName = “Allow Exchange ActiveSync ”
    c1:[Type == "http://schemas.microsoft.com/2012/01/requestcontext/claims/x-ms-client-application", Value == "Microsoft.Exchange.ActiveSync"]
    => issue(Type = "http://schemas.microsoft.com/authorization/claims/permit", Value = "true");

##### <a name="rule-3"></a>Regla 3

    @RuleName = “Allow extranet browser and browser dialog traffic”
    c1:[Type == " http://schemas.microsoft.com/ws/2012/01/insidecorporatenetwork", Value == "false"] &&
    c2:[Type == "http://schemas.microsoft.com/2012/01/requestcontext/claims/x-ms-endpoint-absolute-path", Value =~ "(/adfs/ls)|(/adfs/oauth2)"]
    => issue(Type = "http://schemas.microsoft.com/authorization/claims/permit", Value = "true");

#### <a name="option-2-allow-exchange-activesync-and-block-legacy-apps"></a>Opción 2: Permitir Exchange ActiveSync y bloquear aplicaciones heredadas

Al aplicar las siguientes reglas de tres a la confianza de confianza de terceros para la plataforma de identidad de Microsoft Office 365, el tráfico de Exchange ActiveSync y el explorador y el tráfico de autenticación moderna de AD FS, tiene acceso. Aplicaciones heredadas se bloquean desde cualquier ubicación.

##### <a name="rule-1"></a>Regla 1

    @RuleName = “Allow all intranet traffic only for browser and modern authentication clients”
    c1:[Type == "http://schemas.microsoft.com/ws/2012/01/insidecorporatenetwork", Value == "true"] &&
    c2:[Type == "http://schemas.microsoft.com/2012/01/requestcontext/claims/x-ms-endpoint-absolute-path", Value =~ "(/adfs/ls)|(/adfs/oauth2)"]
    => issue(Type = "http://schemas.microsoft.com/authorization/claims/permit", Value = "true");


##### <a name="rule-2"></a>Regla 2

    @RuleName = “Allow Exchange ActiveSync”
    c1:[Type == "http://schemas.microsoft.com/2012/01/requestcontext/claims/x-ms-client-application", Value == "Microsoft.Exchange.ActiveSync"]
    => issue(Type = "http://schemas.microsoft.com/authorization/claims/permit", Value = "true");


##### <a name="rule-3"></a>Regla 3

    @RuleName = “Allow extranet browser and browser dialog traffic”
    c1:[Type == " http://schemas.microsoft.com/ws/2012/01/insidecorporatenetwork", Value == "false"] &&
    c2:[Type == "http://schemas.microsoft.com/2012/01/requestcontext/claims/x-ms-endpoint-absolute-path", Value =~ "(/adfs/ls)|(/adfs/oauth2)"]
    => issue(Type = "http://schemas.microsoft.com/authorization/claims/permit", Value = "true");
