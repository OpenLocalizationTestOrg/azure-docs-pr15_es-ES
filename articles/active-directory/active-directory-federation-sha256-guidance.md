<properties
    pageTitle="Algoritmo de tipo hash de firma de cambio de confianza de Office 365 respuesta fiesta | Microsoft Azure"
    description="Esta página proporciona directrices para cambiar el algoritmo SHA de confianza de federación con Office 365"
    keywords="SHA1, SHA256, O365, federación, aadconnect, adfs, ad fs, cambiar sha, confianza de federación, confiar confianza de fiesta"
    services="active-directory"
    documentationCenter=""
    authors="anandyadavmsft"
    manager="samueld"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/01/2016"
    ms.author="anandy"/>

# <a name="change-signature-hash-algorithm-for-office-365-replying-party-trust"></a>Cambiar algoritmo de firma hash para responder confianza de fiesta de Office 365

## <a name="overview"></a>Información general

Azure Active Directory Federation Services (AD FS) firma sus tokens Microsoft Azure Active Directory para asegurarse de que no se puede manipular con. Esta firma puede basarse en SHA1 o SHA256. Azure Active Directory admite ahora tokens firmados con un algoritmo de SHA256 y, a continuación, se recomienda establecer el algoritmo de firma de tokens en SHA256 el más alto nivel de seguridad. En este artículo se describe los pasos necesarios para establecer el algoritmo de firma de tokens a la más segura SHA256 nivel.

## <a name="change-the-token-signing-algorithm"></a>Cambiar el algoritmo de firma de tokens

Después de establecer el algoritmo de firma con uno de los dos procesos siguientes, AD FS firma los tokens para Office 365 confiar confianza fiesta con SHA256. No es necesario realizar los cambios de configuración adicionales, y este cambio no afecta en su capacidad para obtener acceso a Office 365 u otras aplicaciones de Azure AD.

### <a name="ad-fs-management-console"></a>Consola de administración de AD FS

1. Abra la consola de administración de AD FS en el servidor de AD FS principal.
2. Expanda el nodo de AD FS y haga clic en **Confiar confía de terceros**.
3. Haga clic en la confianza de fiesta confianza de Office 365 y Azure y seleccione **Propiedades**.
4. Seleccione la ficha **Opciones avanzadas** y seleccione el algoritmo de hash seguro SHA256.
5. Haga clic en **Aceptar**.

![Algoritmo de firma SHA256--MMC](./media/active-directory-aadconnectfed-sha256guidance/mmc.png)

### <a name="ad-fs-powershell-cmdlets"></a>Cmdlets de AD FS PowerShell

1. En cualquier servidor AD FS, abra el PowerShell con privilegios de administrador.
2. Establecer el algoritmo de hash seguro mediante el cmdlet **Set-AdfsRelyingPartyTrust** .

 <code>Set-AdfsRelyingPartyTrust -TargetName 'Microsoft Office 365 Identity Platform' -SignatureAlgorithm 'http://www.w3.org/2001/04/xmldsig-more#rsa-sha256'</code>

## <a name="also-read"></a>También leer

* [Reparar de confianza de Office 365 con Azure AD Connect](./active-directory-aadconnect-federation-management.md#repairing-the-trust)
