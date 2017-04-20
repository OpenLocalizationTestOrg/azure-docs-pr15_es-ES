<properties
    pageTitle="Sincronización de Azure AD Connect: atributos sincronizados a Azure Active Directory | Microsoft Azure"
    description="Enumera los atributos que se sincronizan con Azure Active Directory."
    services="active-directory"
    documentationCenter=""
    authors="andkjell"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/13/2016"
    ms.author="markvi;andkjell"/>


# <a name="azure-ad-connect-sync-attributes-synchronized-to-azure-active-directory"></a>Sincronización de Azure AD Connect: atributos sincronizados a Azure Active Directory
En este tema se enumeran los atributos que se sincronizan mediante la sincronización de Azure AD Connect.  
Los atributos se agrupan por la relacionados Azure AD aplicación.

## <a name="attributes-to-synchronize"></a>Atributos para sincronizar
Una pregunta habitual es *lo que la lista de atributos mínimos para sincronizar*. El predeterminado y el enfoque recomendado es para mantener los atributos predeterminados para que una GAL completa (lista Global de direcciones) se puede crear en la nube y obtener todas las características de cargas de trabajo de Office 365. En algunos casos, hay algunos atributos que su organización no desea sincronizados con la nube ya que contienen estos atributos confidencial o datos PII (información de identificación personal), como en este ejemplo:  
![atributos incorrectas](./media/active-directory-aadconnectsync-attributes-synchronized/badextensionattribute.png)

En este caso, comience con la lista de atributos en este tema e identificar los atributos que contienen datos PII o no y no se puede sincronizar. A continuación, anule la selección de los atributos durante la instalación mediante la [aplicación de Azure AD y filtrado de atributo](active-directory-aadconnect-get-started-custom.md#azure-ad-app-and-attribute-filtering).

>[AZURE.WARNING] Al anular la selección de atributos, debe tener cuidado y solo anule la selección de los atributos estrictamente no se puede sincronizar. Anular la selección otros atributos puede tener un impacto negativo en características.

## <a name="office-365-proplus"></a>Office 365 ProPlus

| Nombre del atributo| Usuario| Comentario |
| --- | :-: | --- |
| accountEnabled| X| Define si una cuenta está habilitada.|
| CN| X|  |
| displayName| X|  |
| objectSID| X| propiedad mecánica. Identificador de usuario de AD utilizado para mantener sincronizados entre Azure AD y AD.|
| pwdLastSet| X| propiedad mecánica. Usar para saber cuándo invalidar tokens ya emitidos. Sincronización de la contraseña y federación utilizan.|
| sourceAnchor| X| propiedad mecánica. Identificador inmutable para mantener la relación entre agrega y Azure AD.|
| usageLocation| X| propiedad mecánica. El país del usuario. Se usa para la asignación de licencias.|
| userPrincipalName| X| UPN es el identificador de inicio de sesión para el usuario. Más a menudo el mismo como [correo] valor.|

## <a name="exchange-online"></a>Exchange Online

| Nombre del atributo| Usuario| Contacto| Grupo| Comentario |
| --- | :-: | :-: | :-: | --- |
| accountEnabled| X|  |  | Define si una cuenta está habilitada.|
| Asistente| X| X|  |  |
| authOrig| X| X| X|  |
| c| X| X|  |  |
| CN| X|  | X|  |
| Co| X| X|  |  |
| compañía| X| X|  |  |
| Código| X| X|  |  |
| departamento| X| X|  |  |
| Descripción| X| X| X|  |
| displayName| X| X| X|  |
| dLMemRejectPerms| X| X| X|  |
| dLMemSubmitPerms| X| X| X|  |
| extensionAttribute1| X| X| X|  |
| extensionAttribute10| X| X| X|  |
| extensionAttribute11| X| X| X|  |
| extensionAttribute12| X| X| X|  |
| extensionAttribute13| X| X| X|  |
| extensionAttribute14| X| X| X|  |
| extensionAttribute15| X| X| X|  |
| extensionAttribute2| X| X| X|  |
| extensionAttribute3| X| X| X|  |
| extensionAttribute4| X| X| X|  |
| extensionAttribute5| X| X| X|  |
| extensionAttribute6| X| X| X|  |
| extensionAttribute7| X| X| X|  |
| extensionAttribute8| X| X| X|  |
| extensionAttribute9| X| X| X|  |
| facsimiletelephonenumber| X| X|  |  |
| givenName| X| X|  |  |
| homePhone| X| X|  |  |
| información| X| X| X| Este atributo no consumen actualmente para los grupos.|
| Iniciales| X| X|  |  |
| l| X| X|  |  |
| legacyExchangeDN| X| X| X|  |
| mailNickname| X| X| X|  |
| mangedBy|  |  | X|  |
| Administrador| X| X|  |  |
| miembro|  |  | X|  |
| móvil| X| X|  |  |
| msDS HABSeniorityIndex| X| X| X|  |
| msDS PhoneticDisplayName| X| X| X|  |
| msExchArchiveGUID| X|  |  |  |
| msExchArchiveName| X|  |  |  |
| msExchAssistantName| X| X|  |  |
| msExchAuditAdmin| X|  |  |  |
| msExchAuditDelegate| X|  |  |  |
| msExchAuditDelegateAdmin| X|  |  |  |
| msExchAuditOwner| X|  |  |  |
| msExchBlockedSendersHash| X| X|  |  |
| msExchBypassAudit| X|  |  |  |
| msExchCoManagedByLink|  |  | X|  |
| msExchDelegateListLink| X|  |  |  |
| msExchELCExpirySuspensionEnd| X|  |  |  |
| msExchELCExpirySuspensionStart| X|  |  |  |
| msExchELCMailboxFlags| X|  |  |  |
| msExchEnableModeration| X|  | X|  |
| msExchExtensionCustomAttribute1| X| X| X| Este atributo no consumen actualmente Exchange Online. |
| msExchExtensionCustomAttribute2| X| X| X| Este atributo no consumen actualmente Exchange Online. |
| msExchExtensionCustomAttribute3| X| X| X| Este atributo no consumen actualmente Exchange Online. |
| msExchExtensionCustomAttribute4| X| X| X| Este atributo no consumen actualmente Exchange Online. |
| msExchExtensionCustomAttribute5| X| X| X| Este atributo no consumen actualmente Exchange Online. |
| msExchHideFromAddressLists| X| X| X|  |
| msExchImmutableID| X|  |  |  |
| msExchLitigationHoldDate| X| X| X|  |
| msExchLitigationHoldOwner| X| X| X|  |
| msExchMailboxAuditEnable| X|  |  |  |
| msExchMailboxAuditLogAgeLimit| X|  |  |  |
| msExchMailboxGuid| X|  |  |  |
| msExchModeratedByLink| X| X| X|  |
| msExchModerationFlags| X| X| X|  |
| msExchRecipientDisplayType| X| X| X|  |
| msExchRecipientTypeDetails| X| X| X|  |
| msExchRemoteRecipientType| X|  |  |  |
| msExchRequireAuthToSendTo| X| X| X|  |
| msExchResourceCapacity| X|  |  |  |
| msExchResourceDisplay| X|  |  |  |
| msExchResourceMetaData| X|  |  |  |
| msExchResourceSearchProperties| X|  |  |  |
| msExchRetentionComment| X| X| X|  |
| msExchRetentionURL| X| X| X|  |
| msExchSafeRecipientsHash| X| X|  |  |
| msExchSafeSendersHash| X| X|  |  |
| msExchSenderHintTranslations| X| X| X|  |
| msExchTeamMailboxExpiration| X|  |  |  |
| msExchTeamMailboxOwners| X|  |  |  |
| msExchTeamMailboxSharePointUrl| X|  |  |  |
| msExchUserHoldPolicies| X|  |  |  |
| msOrg IsOrganizational|  |  | X|  |
| objectSID| X|  | X| propiedad mecánica. Identificador de usuario de AD utilizado para mantener sincronizados entre Azure AD y AD.|
| oOFReplyToOriginator|  |  | X|  |
| otherFacsimileTelephone| X| X|  |  |
| otherHomePhone| X| X|  |  |
| otherTelephone| X| X|  |  |
| Localizador| X| X|  |  |
| physicalDeliveryOfficeName| X| X|  |  |
| código postal| X| X|  |  |
| proxyAddresses| X| X| X|  |
| publicDelegates| X| X| X|  |
| pwdLastSet| X|  |  | propiedad mecánica. Usar para saber cuándo invalidar tokens ya emitidos. Sincronización de la contraseña y federación utilizan.|
| reportToOriginator|  |  | X|  |
| reportToOwner|  |  | X|  |
| securityEnabled|  |  | X| Derivado de groupType|
| Sn| X| X|  |  |
| sourceAnchor| X| X| X| propiedad mecánica. Identificador inmutable para mantener la relación entre agrega y Azure AD.|
| St| X| X|  |  |
| Dirección| X| X|  |  |
| targetAddress| X| X|  |  |
| telephoneAssistant| X| X|  |  |
| telephoneNumber| X| X|  |  |
| ThumbNailPhoto| X| X|  |  |
| título| X| X|  |  |
| unauthOrig| X| X| X|  |
| usageLocation| X|  |  | propiedad mecánica. El país del usuario. Se usa para la asignación de licencias.|
| userCertificate| X| X|  |  |
| userPrincipalName| X|  |  | UPN es el identificador de inicio de sesión para el usuario. Más a menudo el mismo como [correo] valor.|
| userSMIMECertificates| X| X|  |  |
| wWWHomePage| X| X|  |  |

## <a name="sharepoint-online"></a>SharePoint Online

| Nombre del atributo| Usuario| Contacto| Grupo| Comentario |
| --- | :-: | :-: | :-: | --- |
| accountEnabled| X|  |  | Define si una cuenta está habilitada.|
| authOrig| X| X| X|  |
| c| X| X|  |  |
| CN| X|  | X|  |
| Co| X| X|  |  |
| compañía| X| X|  |  |
| Código| X| X|  |  |
| departamento| X| X|  |  |
| Descripción| X| X| X|  |
| displayName| X| X| X|  |
| dLMemRejectPerms| X| X| X|  |
| dLMemSubmitPerms| X| X| X|  |
| extensionAttribute1| X| X| X|  |
| extensionAttribute10| X| X| X|  |
| extensionAttribute11| X| X| X|  |
| extensionAttribute12| X| X| X|  |
| extensionAttribute13| X| X| X|  |
| extensionAttribute14| X| X| X|  |
| extensionAttribute15| X| X| X|  |
| extensionAttribute2| X| X| X|  |
| extensionAttribute3| X| X| X|  |
| extensionAttribute4| X| X| X|  |
| extensionAttribute5| X| X| X|  |
| extensionAttribute6| X| X| X|  |
| extensionAttribute7| X| X| X|  |
| extensionAttribute8| X| X| X|  |
| extensionAttribute9| X| X| X|  |
| facsimiletelephonenumber| X| X|  |  |
| givenName| X| X|  |  |
| hideDLMembership|  |  | X|  |
| HomePhone| X| X|  |  |
| información| X| X| X|  |
| iniciales| X| X|  |  |
| ipPhone| X| X|  |  |
| l| X| X|  |  |
| correo| X| X| X|  |
| mailNickname| X| X| X|  |
| managedBy|  |  | X|  |
| Administrador| X| X|  |  |
| miembro|  |  | X|  |
| middleName| X| X|  |  |
| móvil| X| X|  |  |
| msExchTeamMailboxExpiration| X|  |  |  |
| msExchTeamMailboxOwners| X|  |  |  |
| msExchTeamMailboxSharePointLinkedBy| X|  |  |  |
| msExchTeamMailboxSharePointUrl| X|  |  |  |
| objectSID| X|  | X| propiedad mecánica. Identificador de usuario de AD utilizado para mantener sincronizados entre Azure AD y AD.|
| oOFReplyToOriginator|  |  | X|  |
| otherFacsimileTelephone| X| X|  |  |
| otherHomePhone| X| X|  |  |
| otherIpPhone| X| X|  |  |
| otherMobile| X| X|  |  |
| otherPager| X| X|  |  |
| otherTelephone| X| X|  |  |
| Localizador| X| X|  |  |
| physicalDeliveryOfficeName| X| X|  |  |
| código postal| X| X|  |  |
| postOfficeBox| X| X|  |  |
| preferredLanguage| X|  |  |  |
| proxyAddresses| X| X| X|  |
| pwdLastSet| X|  |  | propiedad mecánica. Usar para saber cuándo invalidar tokens ya emitidos. Sincronización de la contraseña y federación utilizan.|
| reportToOriginator|  |  | X|  |
| reportToOwner|  |  | X|  |
| securityEnabled|  |  | X| Derivado de groupType|
| Sn| X| X|  |  |
| sourceAnchor| X| X| X| propiedad mecánica. Identificador inmutable para mantener la relación entre agrega y Azure AD.|
| St| X| X|  |  |
| Dirección| X| X|  |  |
| targetAddress| X| X|  |  |
| telephoneAssistant| X| X|  |  |
| telephoneNumber| X| X|  |  |
| ThumbNailPhoto| X| X|  |  |
| título| X| X|  |  |
| unauthOrig| X| X| X|  |
| dirección URL| X| X|  |  |
| usageLocation| X|  |  | propiedad mecánica. El país del usuario. Se usa para la asignación de licencias.|
| userPrincipalName| X|  |  | UPN es el identificador de inicio de sesión para el usuario. Más a menudo el mismo como [correo] valor.|
| wWWHomePage| X| X|  |  |

## <a name="lync-online"></a>Lync Online

| Nombre del atributo| Usuario| Contacto| Grupo| Comentario |
| --- | :-: | :-: | :-: | --- |
| accountEnabled| X|  |  | Define si una cuenta está habilitada.|
| c| X| X|  |  |
| CN| X|  | X|  |
| Co| X| X|  |  |
| compañía| X| X|  |  |
| departamento| X| X|  |  |
| Descripción| X| X| X|  |
| displayName| X| X| X|  |
| facsimiletelephonenumber| X| X| X|  |
| givenName| X| X|  |  |
| HomePhone| X| X|  |  |
| ipPhone| X| X|  |  |
| l| X| X|  |  |
| correo| X| X| X|  |
| mailNickname| X| X| X|  |
| managedBy|  |  | X|  |
| Administrador| X| X|  |  |
| miembro|  |  | X|  |
| móvil| X| X|  |  |
| msExchHideFromAddressLists| X| X| X|  |
| msRTCSIP-ApplicationOptions| X|  |  |  |
| msRTCSIP-DeploymentLocator| X| X|  |  |
| msRTCSIP-línea| X| X|  |  |
| msRTCSIP-OptionFlags| X| X|  |  |
| msRTCSIP-OwnerUrn| X|  |  |  |
| msRTCSIP-PrimaryUserAddress| X| X|  |  |
| msRTCSIP-UserEnabled| X| X|  |  |
| objectSID| X|  | X| propiedad mecánica. Identificador de usuario de AD utilizado para mantener sincronizados entre Azure AD y AD.|
| otherTelephone| X| X|  |  |
| physicalDeliveryOfficeName| X| X|  |  |
| código postal| X| X|  |  |
| preferredLanguage| X|  |  |  |
| proxyAddresses| X| X| X|  |
| pwdLastSet| X|  |  | propiedad mecánica. Usar para saber cuándo invalidar tokens ya emitidos. Sincronización de la contraseña y federación utilizan.|
| securityEnabled|  |  | X| Derivado de groupType|
| Sn| X| X|  |  |
| sourceAnchor| X| X| X| propiedad mecánica. Identificador inmutable para mantener la relación entre agrega y Azure AD.|
| St| X| X|  |  |
| Dirección| X| X|  |  |
| telephoneNumber| X| X|  |  |
| ThumbNailPhoto| X| X|  |  |
| título| X| X|  |  |
| usageLocation| X|  |  | propiedad mecánica. El país del usuario. Se usa para la asignación de licencias.|
| userPrincipalName| X|  |  | UPN es el identificador de inicio de sesión para el usuario. Más a menudo el mismo como [correo] valor.|
| wWWHomePage| X| X|  |  |

## <a name="azure-rms"></a>Azure RMS

| Nombre del atributo| Usuario| Contacto| Grupo| Comentario |
| --- | :-: | :-: | :-: | --- |
| accountEnabled| X|  |  | Define si una cuenta está habilitada.|
| CN| X|  | X| Nombre o alias común. Más a menudo el prefijo del valor [correo].|
| displayName| X| X| X| Una cadena que representa el nombre que a menudo se muestra como el nombre descriptivo (nombre apellidos).|
| correo| X| X| X| dirección de correo electrónico completa.|
| miembro|  |  | X|  |
| objectSID| X|  | X| propiedad mecánica. Identificador de usuario de AD utilizado para mantener sincronizados entre Azure AD y AD.|
| proxyAddresses| X| X| X| propiedad mecánica. Usado por Azure AD. Contiene todas las direcciones de correo electrónico secundarias para el usuario.|
| pwdLastSet| X|  |  | propiedad mecánica. Usar para saber cuándo invalidar tokens ya emitidos.|
| securityEnabled|  |  | X| Derivado de groupType.|
| sourceAnchor| X| X| X| propiedad mecánica. Identificador inmutable para mantener la relación entre agrega y Azure AD.|
| usageLocation| X|  |  | propiedad mecánica. El país del usuario. Se usa para la asignación de licencias.|
| userPrincipalName| X|  |  | Este UPN es el identificador de inicio de sesión para el usuario. Más a menudo el mismo como [correo] valor.|

## <a name="intune"></a>Intune

| Nombre del atributo| Usuario| Contacto| Grupo| Comentario |
| --- | :-: | :-: | :-: | --- |
| accountEnabled| X|  |  | Define si una cuenta está habilitada.|
| c| X| X|  |  |
| CN| X|  | X|  |
| Descripción| X| X| X|  |
| displayName| X| X| X|  |
| correo| X| X| X|  |
| mailNickname| X| X| X|  |
| miembro|  |  | X|  |
| objectSID| X|  | X| propiedad mecánica. Identificador de usuario de AD utilizado para mantener sincronizados entre Azure AD y AD.|
| proxyAddresses| X| X| X|  |
| pwdLastSet| X|  |  | propiedad mecánica. Usar para saber cuándo invalidar tokens ya emitidos. Sincronización de la contraseña y federación utilizan.|
| securityEnabled|  |  | X| Derivado de groupType|
| sourceAnchor| X| X| X| propiedad mecánica. Identificador inmutable para mantener la relación entre agrega y Azure AD.|
| usageLocation| X|  |  | propiedad mecánica. El país del usuario. Se usa para la asignación de licencias.|
| userPrincipalName| X|  |  | UPN es el identificador de inicio de sesión para el usuario. Más a menudo el mismo como [correo] valor.|

## <a name="dynamics-crm"></a>Dynamics CRM

| Nombre del atributo| Usuario| Contacto| Grupo| Comentario |
| --- | :-: | :-: | :-: | --- |
| accountEnabled| X|  |  | Define si una cuenta está habilitada.|
| c| X| X|  |  |
| CN| X|  | X|  |
| Co| X| X|  |  |
| compañía| X| X|  |  |
| Código| X| X|  |  |
| Descripción| X| X| X|  |
| displayName| X| X| X|  |
| facsimiletelephonenumber| X| X|  |  |
| givenName| X| X|  |  |
| l| X| X|  |  |
| managedBy|  |  | X|  |
| Administrador| X| X|  |  |
| miembro|  |  | X|  |
| móvil| X| X|  |  |
| objectSID| X|  | X| propiedad mecánica. Identificador de usuario de AD utilizado para mantener sincronizados entre Azure AD y AD.|
| physicalDeliveryOfficeName| X| X|  |  |
| código postal| X| X|  |  |
| preferredLanguage| X|  |  |  |
| pwdLastSet| X|  |  | propiedad mecánica. Usar para saber cuándo invalidar tokens ya emitidos. Sincronización de la contraseña y federación utilizan.|
| securityEnabled|  |  | X| Derivado de groupType|
| Sn| X| X|  |  |
| sourceAnchor| X| X| X| propiedad mecánica. Identificador inmutable para mantener la relación entre agrega y Azure AD.|
| St| X| X|  |  |
| Dirección| X| X|  |  |
| telephoneNumber| X| X|  |  |
| título| X| X|  |  |
| usageLocation| X|  |  | propiedad mecánica. El país del usuario. Se usa para la asignación de licencias.|
| userPrincipalName| X|  |  | UPN es el identificador de inicio de sesión para el usuario. Más a menudo el mismo como [correo] valor.|

## <a name="3rd-party-applications"></a>3er aplicaciones de terceros
Este grupo es un conjunto de atributos que se usan como los atributos mínimos necesarios para una carga de trabajo genérico o una aplicación. Puede utilizarse para una carga de trabajo no aparece en otra sección o una aplicación no sean de Microsoft. Se utiliza explícitamente para lo siguiente:

- Yammer (el usuario solo se consume)
- [Escenarios de colaboración híbrido para empresas (B2B) entre org ofrecidos por recursos como SharePoint](http://go.microsoft.com/fwlink/?LinkId=747036)

Este grupo es un conjunto de atributos que se pueden usar si no se utiliza el directorio de Azure AD a soporte técnico de Office 365, Dynamics o Intune. Tiene un pequeño conjunto de atributos principales.

| Nombre del atributo| Usuario| Contacto| Grupo| Comentario |
| --- | :-: | :-: | :-: | --- |
| accountEnabled| X|  |  | Define si una cuenta está habilitada.|
| CN| X|  | X|  |
| displayName| X| X| X|  |
| givenName| X| X|  |  |
| correo| X|  | X|  |
| managedBy|  |  | X|  |
| mailNickName| X| X| X|  |
| miembro|  |  | X|  |
| objectSID| X|  |  | propiedad mecánica. Identificador de usuario de AD utilizado para mantener sincronizados entre Azure AD y AD.|
| proxyAddresses| X| X| X|  |
| pwdLastSet| X|  |  | propiedad mecánica. Usar para saber cuándo invalidar tokens ya emitidos. Sincronización de la contraseña y federación utilizan.|
| Sn| X| X|  |  |
| sourceAnchor| X| X| X| propiedad mecánica. Identificador inmutable para mantener la relación entre agrega y Azure AD.|
| usageLocation| X|  |  | propiedad mecánica. El país del usuario. Se usa para la asignación de licencias.|
| userPrincipalName| X|  |  | UPN es el identificador de inicio de sesión para el usuario. Más a menudo el mismo como [correo] valor.|

## <a name="windows-10"></a>Windows 10
Un computer(device) de dominio de Windows 10 sincroniza algunos atributos de Azure AD. Para obtener más información sobre los escenarios, vea [conectar dispositivos de dominio a Azure Active Directory para Windows 10 experiencias](active-directory-azureadjoin-devices-group-policy.md). Sincronizan siempre estos atributos y Windows 10 no aparece como una aplicación que puede anular la selección. Un equipo de dominio de Windows 10 se identifica por tener la userCertificate atributo rellena.

| Nombre del atributo| Dispositivo| Comentario |
| --- | :-: | --- |
| accountEnabled| X| |
| deviceTrustType| X| Valor codificado para equipos unidos a un dominio. |
| displayName | X| |
| MS-DS-CreatorSID | X| También se denomina registeredOwnerReference.|
| GUID de objeto | X| También se denomina deviceID.|
| objectSID | X| También se denomina onPremisesSecurityIdentifier.|
| sistema operativo | X| También se denomina deviceOSType.|
| operatingSystemVersion | X| También se denomina deviceOSVersion.|
| userCertificate | X| |

Estos atributos de **usuario** son además de las otras aplicaciones que ha seleccionado.  

| Nombre del atributo| Usuario| Comentario |
| --- | :-: | --- |
| domainFQDN| X| También se denomina NombreDominioDns. Por ejemplo, contoso.com.|
| domainNetBios| X| También se denomina nombreNetBIOS. Por ejemplo, CONTOSO.|

## <a name="exchange-hybrid-writeback"></a>Reescritura de implementación híbrida de Exchange
Estos atributos se vuelven de Azure AD a escribir local de Active Directory cuando seleccione para habilitar **híbrida de Exchange**. Según la versión de Exchange, se pueden sincronizar menos atributos.

| Nombre del atributo| Usuario| Contacto| Grupo| Comentario |
| --- | :-: | :-: | :-: | --- |
| msDS ExternalDirectoryObjectID| X|  |  | Derivado de cloudAnchor en Azure AD. Este atributo es nuevo en Exchange 2016.|
| msExchArchiveStatus| X|  |  | Archivo en línea: Permite a los clientes archivar correo.|
| msExchBlockedSendersHash| X|  |  | Filtrado: Reescriba los datos de remitentes seguros y bloqueados en línea y local filtrado de clientes.|
| msExchSafeRecipientsHash| X|  |  | Filtrado: Reescriba los datos de remitentes seguros y bloqueados en línea y local filtrado de clientes.|
| msExchSafeSendersHash| X|  |  | Filtrado: Reescriba los datos de remitentes seguros y bloqueados en línea y local filtrado de clientes.|
| msExchUCVoiceMailSettings| X|  |  | Habilitar la mensajería unificada (MU) - correo de voz en línea: Microsoft Lync Server usado integración para indicar a Lync Server local que el usuario tiene correo de voz en servicios en línea.|
| msExchUserHoldPolicies| X|  |  | Litigios: Permite a los servicios de nube para determinar qué usuarios están bajo retención para litigios.|
| proxyAddresses| X| X| X| Solo el x500 se inserta la dirección de Exchange Online.|

## <a name="device-writeback"></a>Reescritura de dispositivo
Objetos de dispositivo se crean en Active Directory. Estos objetos pueden ser dispositivos Unidos a Azure AD o equipos unidos a un dominio de Windows 10.

| Nombre del atributo| Dispositivo| Comentario |
| --- | :-: | --- |
| altSecurityIdentities | X| |
| displayName | X| |
| DN | X| |
| msDS CloudAnchor | X| |
| msDS DeviceID | X| |
| msDS DeviceObjectVersion | X| |
| msDS DeviceOSType | X| |
| msDS DeviceOSVersion | X| |
| msDS DevicePhysicalIDs | X| |
| msDS KeyCredentialLink | X| Sólo con el esquema de Active Directory de Windows Server 2016 |
| msDS IsCompliant | X| |
| msDS IsEnabled | X| |
| msDS IsManaged | X| |
| msDS RegisteredOwner | X| |


## <a name="notes"></a>Notas

- Cuando se usa un identificador alternativo, local atributo userPrincipalName se sincroniza con la onPremisesUserPrincipalName de atributo Azure AD. El atributo de identificador alternativo para el correo de ejemplo, se ha sincronizado con Azure AD atributo userPrincipalName.
- En las listas anteriormente, el tipo de objeto de **usuario** también se aplica a la del tipo de objeto **iNetOrgPerson**.

## <a name="next-steps"></a>Pasos siguientes
Más información sobre la configuración de [sincronización de Azure AD Connect](active-directory-aadconnectsync-whatis.md) .

Más información acerca de cómo [integrar las identidades locales con Azure Active Directory](active-directory-aadconnect.md).
