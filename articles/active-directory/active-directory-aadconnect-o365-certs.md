<properties
    pageTitle="Guía de renovación para los usuarios de Office 365 y Azure Active Directory de certificados | Microsoft Azure"
    description="En este artículo se explica a los usuarios de Office 365 cómo resolver los problemas con los correos electrónicos que notificarán acerca de cómo renovar un certificado."
    services="active-directory"
    documentationCenter=""
    authors="billmath"
    manager="femila"
    editor="curtand"/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/08/2016"
    ms.author="billmath"/>


# <a name="renew-federation-certificates-for-office-365-and-azure-active-directory"></a>Renovar los certificados de federación de Office 365 y Azure Active Directory

##<a name="overview"></a>Información general

Para la federación con éxito entre Azure Active Directory (AD Azure) y los servicios de federación de Active Directory (AD FS), los certificados que utiliza AD FS para firmar el token de seguridad a Azure AD deben coincidir con lo que está configurado en Azure AD. Cualquier incoherencia puede producir confianza roto. Azure AD garantiza que esta información se mantiene sincronizada cuando se implementación AD FS y Proxy de aplicación Web (para el acceso de extranet).

En este artículo se proporciona información adicional para administrar los certificados de firma de tokens y mantener sincronizados con Azure AD en los siguientes casos:

* No va a implementar al Proxy de la aplicación Web y, por tanto, no están disponible en la extranet los metadatos de federación.
* No se usa la configuración predeterminada de AD FS de certificados de firma de tokens.
* Está usando un proveedor de identidades de terceros.

## <a name="default-configuration-of-ad-fs-for-token-signing-certificates"></a>Configuración predeterminada de AD FS de certificados de firma de tokens

La firma de tokens token descifrar certificados son certificados normalmente autofirmados y son adecuadas para un año. De forma predeterminada, AD FS incluye un proceso de renovación automática denominado **AutoCertificateRollover**. Si está utilizando AD FS 2.0 o posterior, Office 365 y Azure AD actualizarán automáticamente el certificado antes de que caduque.

### <a name="renewal-notification-from-the-office-365-portal-or-an-email"></a>Notificación de renovación desde el portal de Office 365 o un correo electrónico

>[AZURE.NOTE] Si ha recibido un correo electrónico o una notificación de portal donde se pregunta para renovar el certificado de Office, vea [Administrar cambia a certificados de firma de tokens](#managecerts) para comprobar si debe realizar ninguna acción. Microsoft tiene constancia de un problema posible que puede dar lugar a las notificaciones de renovación de certificados que se envía, incluso cuando no se requiere ninguna acción.

Azure AD intenta supervisar los metadatos de federación y actualizar el token de certificados de firma indicada por metadatos. 30 días antes de la caducidad de los certificados de firma de tokens Azure AD comprueba si nuevos certificados están disponibles mediante un sondeo de los metadatos de federación.

* Si se puede sondear los metadatos de federación y recuperar los certificados nuevos correctamente, notificación de correo electrónico o una advertencia en el portal de Office 365 no se emite al usuario.
* Si no se puede recuperar al nuevo símbolo de certificados de firma, ya sea porque los metadatos de federación no están accesible o no está habilitada la renovación automática de certificados, Azure AD emite una notificación de correo electrónico y una advertencia en el portal de Office 365.

![Notificación de portal de Office 365](./media/active-directory-aadconnect-o365-certs/notification.png)

>[AZURE.IMPORTANT] Si está utilizando AD FS, para garantizar la continuidad empresarial, compruebe que los servidores tienen las siguientes actualizaciones para que no se producen errores de autenticación para los problemas conocidos. Esta forma reduce la problemas conocidos de servidor proxy AD FS para esta renovación y períodos de renovación futuras:
>
>Server 2012 R2 - [Windows Server rollup de mayo de 2014](http://support.microsoft.com/kb/2955164)
>
>Server 2008 R2 y 2012: [se produce un error de autenticación de proxy en Windows Server 2012 o Windows 2008 R2 SP1](http://support.microsoft.com/kb/3094446)

## Comprobar si es necesario actualizar los certificados<a name="managecerts"></a>

### <a name="step-1-check-the-autocertificaterollover-state"></a>Paso 1: Comprobar el estado de AutoCertificateRollover

En el servidor AD FS, abra PowerShell. Compruebe que el valor de AutoCertificateRollover se establece en True.

    Get-Adfsproperties

![AutoCertificateRollover](./media/active-directory-aadconnect-o365-certs/autocertrollover.png)

[AZURE.NOTE] Si está utilizando AD FS 2.0, ejecutar Microsoft.Adfs.Powershell Add-Pssnapin.

### <a name="step-2-confirm-that-ad-fs-and-azure-ad-are-in-sync"></a>Paso 2: Confirmar que AD FS y Azure AD están sincronizados

En su servidor AD FS, abra el símbolo del sistema de Azure AD PowerShell y conectarse a Azure AD.

>[AZURE.NOTE] Puede descargar Azure AD PowerShell [aquí](https://technet.microsoft.com/library/jj151815.aspx).

    Connect-MsolService

Compruebe los certificados configurados en AD FS y Azure AD propiedades para el dominio especificado de confianza.

    Get-MsolFederationProperty -DomainName <domain.name> | FL Source, TokenSigningCertificate

![Get-MsolFederationProperty](./media/active-directory-aadconnect-o365-certs/certsync.png)

Si coinciden con las huellas digitales en los resultados de ambos, sus certificados están sincronizados con Azure AD.

### <a name="step-3-check-if-your-certificate-is-about-to-expire"></a>Paso 3: Comprobar si el certificado está a punto de caducar

En la salida de Get-MsolFederationProperty o Get-AdfsCertificate, compruebe la fecha en "No después de". Si la fecha es menor que 30 días, debe realizar la acción.

| AutoCertificateRollover | Certificados sincronizados con Azure AD | Metadatos de federación están accesible públicamente | Validez | Acción |
|:-----------------------:|:-----------------------:|:-----------------------:|:-----------------------:|:-----------------------:|
| Sí | Sí | Sí | - | No es necesaria ninguna acción. Vea [token de renovar automáticamente, el certificado de firma](#autorenew). |
| Sí | No  | - | Inferior a 15 días | Renovar inmediatamente. Vea [token de renovar certificado de firma manualmente](#manualrenew). |
| No | - | - | Menor que 30 días. | Renovar inmediatamente. Vea [token de renovar certificado de firma manualmente](#manualrenew). |

\[No importa -]

## Renovar el token de certificado de firma automáticamente (recomendado)<a name="autorenew"></a>

No es necesario realizar los pasos manualmente si se cumplen dos de las siguientes acciones:
- Se ha implementado a Proxy de aplicación Web, que se puede habilitar el acceso a los metadatos de federación de la extranet.
- Uso de la configuración predeterminada de AD FS (AutoCertificateRollover está habilitado).

Compruebe lo siguiente para confirmar que se puede actualizar automáticamente el certificado.

**1. la propiedad de AD FS AutoCertificateRollover debe estar establecida en True.** Esto indica que AD FS generará automáticamente nueva firma de tokens y certificados de token descifrado, antes del antiguo expiren.

**2. los metadatos de federación de AD FS están accesible públicamente.** Compruebe que los metadatos de federación están accesible públicamente, vaya a la siguiente URL desde un equipo en internet (fuera de la red corporativa):


/federationmetadata/2007-06/federationmetadata.xml https:// (your_FS_name)

donde `(your_FS_name) `se sustituye por el nombre de host de servicio de federación utiliza su organización, como fs.contoso.com.  Si puede comprobar ambas de estas opciones correctamente, no tiene que hacer nada más.  

Ejemplo: https://fs.contoso.com/federationmetadata/2007-06/federationmetadata.xml

## Renovar el token de certificado de firma manualmente<a name="manualrenew"></a>

Puede renovar el token manualmente los certificados de firma. Por ejemplo, los siguientes escenarios podrían funcionar mejor para la renovación manual:
* Certificados de firma de tokens son certificados no firmados. La razón más común de esto es que su organización administra certificados de AD FS inscritos de una entidad de certificación organizativa.
* Seguridad de la red no permite los metadatos de federación esté disponible públicamente.

En estos casos, cada vez que actualice el token de firma de certificados, también debe actualizar el dominio de Office 365 mediante el comando de PowerShell, MsolFederatedDomain de actualización.

### <a name="step-1-ensure-that-ad-fs-has-new-token-signing-certificates"></a>Paso 1: Asegúrese de que AD FS tiene nuevos certificados de firma de tokens

**Configuración no predeterminada**

Si está utilizando una configuración de AD FS (donde **AutoCertificateRollover** está establecido en **False**), probablemente usa certificados personalizados (autofirmados). Para obtener más información sobre cómo renovar el token de AD FS certificados de firma, consulte la [Guía para los clientes que no usan AD FS había autofirmado certificados](https://msdn.microsoft.com/library/azure/JJ933264.aspx#BKMK_NotADFSCert).

**Metadatos de federación no están disponible públicamente**

Por otro lado, si **AutoCertificateRollover** se establece en **True**, pero no están accesible públicamente los metadatos de federación, primero asegúrese de que se han generado nuevos certificados de firma de tokens por AD FS. Confirmar que tiene nuevo token certificados de firma realizando los siguientes pasos:

1. Compruebe que ha iniciado sesión en el servidor de AD FS principal.
2. Comprobar los certificados de firma actuales en AD FS, abra una ventana de comandos de PowerShell y ejecute el siguiente comando:

    PS C:\>ADFSCertificate Get-CertificateType firma de tokens

    >[AZURE.NOTE] Si está utilizando AD FS 2.0, debe ejecutar primero Microsoft.Adfs.Powershell Add-Pssnapin.

3. Mire el resultado del comando en los certificados que se muestran. Si AD FS ha generado un nuevo certificado, debería ver dos certificados en los resultados: uno para el que el valor de **IsPrimary** es **True** y la fecha de **NotAfter** está dentro de 5 días y una cuya **IsPrimary** es **False** y **NotAfter** es acerca de un año en el futuro.

4. Si solo ver un certificado y la fecha de **NotAfter** es dentro de 5 días, debe generar un nuevo certificado.

5. Para generar un nuevo certificado, ejecute el siguiente comando en el símbolo del sistema de PowerShell: `PS C:\>Update-ADFSCertificate –CertificateType token-signing`.

6. Volver a ejecutar el comando siguiente para comprobar la actualización: PS C:\>ADFSCertificate Get-CertificateType firma de tokens

Deben aparecer dos certificados ahora, uno de los cuales tiene una fecha de **NotAfter** de aproximadamente un año en el futuro y para que el valor de **IsPrimary** es **False**.

### <a name="step-2-update-the-new-token-signing-certificates-for-the-office-365-trust"></a>Paso 2: Actualizar al nuevo símbolo de firma de certificados de confianza de Office 365

Actualizar Office 365 con el nuevo token certificados que se usará para la confianza de la manera de firma.

1.  Abra el módulo de Microsoft Azure Active Directory para Windows PowerShell.
2.  Ejecutar $cred = Get-Credential. Cuando este cmdlet solicita las credenciales, escriba sus credenciales de cuenta de administrador de servicio de nube.
3.  Ejecutar MsolService conectar – $cred de credenciales. Este cmdlet conecta al servicio de nube. Crear un contexto que se conecta al servicio de nube es necesaria antes de ejecutar cualquiera de los cmdlets adicionales que se instala con la herramienta.
4.  Si se están ejecutando estos comandos en un equipo que no es el servidor de federación principal de AD FS, ejecute Set-MSOLAdfscontext-equipo <AD FS primary server>, donde <AD FS primary server> es el nombre FQDN interno del servidor AD FS principal. Este cmdlet crea un contexto que se conecta a AD FS.
5.  Ejecutar la actualización MSOLFederatedDomain-DomainName <domain>. Este cmdlet actualiza la configuración de AD FS en el servicio de nube y configura la relación de confianza entre los dos.


>[AZURE.NOTE] Si necesita compatibilidad con varios dominios de nivel superior, por ejemplo, contoso.com y fabrikam.com, debe utilizar el modificador **SupportMultipleDomain** con los cmdlets. Para obtener más información, vea [compatibilidad con varios dominios de nivel superior](active-directory-aadconnect-multiple-domains.md).

## Reparar confianza de Azure AD mediante Azure AD Connect<a name="connectrenew"></a>

Si configura su granja de servidores de AD FS y confianza de Azure AD mediante Azure AD Connect, puede usar Azure AD Connect para detectar si necesita realizar ninguna acción para los certificados de firma de tokens. Si necesita renovar los certificados, puede usar Azure AD Connect para hacerlo.

Para obtener más información, consulte [Reparar la confianza](./active-directory-aadconnect-federation-management.md#repairing-the-trust).
