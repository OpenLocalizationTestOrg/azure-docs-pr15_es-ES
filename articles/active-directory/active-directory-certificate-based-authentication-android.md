<properties 
    pageTitle="Empezar con la autenticación de certificado basado en Android | Microsoft Azure" 
    description="Obtenga información sobre cómo configurar la autenticación de certificado basado en soluciones con dispositivos Android" 
    services="active-directory" 
    authors="markusvi"  
    documentationCenter="na" 
    manager="femila"/>
<tags 
    ms.service="active-directory" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="identity" 
    ms.date="10/10/2016" 
    ms.author="markvi" />



# <a name="get-started-with-certificate-based-authentication-on-android---public-preview"></a>Empezar con la autenticación de certificado basado en Android - Public Preview  

> [AZURE.SELECTOR]
- [iOS](active-directory-certificate-based-authentication-ios.md)
- [Android](active-directory-certificate-based-authentication-android.md)


En este tema se muestra cómo configurar y utilizar autenticación basada en certificados (CBA) en un dispositivo Android para usuarios de inquilinos en planes de Office 365 Enterprise, empresas y educación. 

CBA permite autenticar Azure Active Directory con un certificado de cliente en un dispositivo Android o iOS al conectar la cuenta de Exchange online para: 

- Aplicaciones móviles de Office, como Microsoft Outlook y Microsoft Word   
- Clientes de Exchange ActiveSync (EAS) 

Configuración de esta función, elimina la necesidad de introducir una combinación de nombre de usuario y contraseña en determinadas correo y aplicaciones de Microsoft Office en su dispositivo móvil. 
 

## <a name="supported-scenarios-and-requirements"></a>Requisitos y escenarios admitidos  



### <a name="general-requirements"></a>Requisitos generales 


Para todos los escenarios de este tema, se requieren las siguientes tareas:  

- Acceso a authority(s) de certificado para emitir certificados de cliente.  

- Authority(s) de certificados debe estar configurado en Azure Active Directory. Encontrará instrucciones detalladas acerca de cómo completar la configuración en la sección de [Introducción](#getting-started) .  

- La entidad de certificación raíz y las entidades de certificación intermedias deben estar configuradas en Azure Active Directory.  

- Cada entidad emisora de certificados debe tener una lista de revocación de certificados (CRL) que se puede hacer referencia a través de un orientados a dirección URL de Internet.  

- Debe ser emitido el certificado de cliente para la autenticación de cliente.  


- Para clientes de Exchange ActiveSync, el certificado de cliente debe tener la dirección de correo electrónico enrutables del usuario en Exchange online en el nombre de la entidad de seguridad o el valor de nombre RFC822 del campo nombre alternativo de asunto. Azure Active Directory asigna el valor RFC822 para el atributo de dirección del Proxy en el directorio.  



### <a name="office-mobile-applications-support"></a>Compatibilidad con aplicaciones móviles de Office 

| Aplicaciones                      | Soporte técnico      |
| ---                       | ---          |
| Word o Excel o PowerPoint | ![Comprobar][1]  |
| OneNote                   | Próximamente  |
| OneDrive                  | ![Comprobar][1]  |
| Outlook                   | ![Comprobar][1]  |
| Yammer                    | ![Comprobar][1]  |
| Skype empresarial        | ![Comprobar][1]  |


### <a name="requirements"></a>Requisitos  

La versión de sistema operativo del dispositivo debe ser 5.0 Android (círculo) y superiores. 

Un servidor de federación debe estar configurado.  


Para que Azure Active Directory revocar un certificado de cliente, el token ADFS debe tener las notificaciones siguientes:  

  - `http://schemas.microsoft.com/ws/2008/06/identity/claims/<serialnumber>`  
(El número de serie del certificado de cliente) 

  - `http://schemas.microsoft.com/2012/12/certificatecontext/field/<issuer>`  
(La cadena del emisor de certificado de cliente) 

Azure Active Directory agrega estas notificaciones para el token de actualización si están disponibles en el token ADFS (o cualquier otro token SAML). Cuando es necesario validar el token de actualización, esta información se usa para comprobar la revocación. 

Como práctica recomendada, debe actualizar las páginas de error ADFS con instrucciones sobre cómo obtener un certificado de usuario. 

Para obtener más detalles, consulte [personalización de las páginas AD FS inicio de sesión](https://technet.microsoft.com/library/dn280950.aspx).  



### <a name="exchange-activesync-clients-support"></a>Compatibilidad con clientes de Exchange ActiveSync 


Se admiten algunas aplicaciones de Exchange ActiveSync en Android 5.0 (círculo) o posterior. Para determinar si la aplicación de correo electrónico admite esta característica, póngase en contacto con el desarrollador de la aplicación. 



## <a name="getting-started"></a>Introducción 


Para empezar, debe configurar las entidades emisoras de certificados de Azure Active Directory. Para cada entidad emisora de certificados, cargar la siguiente: 

- La parte pública del certificado, en formato *.cer* 

- Internet opuestas direcciones URL que se encuentran a las listas de revocación de certificados (CRL)
 

A continuación, encontrará el esquema de una entidad emisora de certificados: 

    class TrustedCAsForPasswordlessAuth 
    { 
       CertificateAuthorityInformation[] certificateAuthorities;    
    } 

    class CertificateAuthorityInformation 

    { 
        CertAuthorityType authorityType; 
        X509Certificate trustedCertificate; 
        string crlDistributionPoint; 
        string deltaCrlDistributionPoint; 
        string trustedIssuer; 
        string trustedIssuerSKI; 
    }                

    enum CertAuthorityType 
    { 
        RootAuthority = 0, 
        IntermediateAuthority = 1 
    } 


Para cargar la información, puede usar el módulo Azure AD a través de Windows PowerShell.  
A continuación se muestran ejemplos para agregar, quitar o modificar una entidad emisora de certificados. 



### <a name="configuring-your-azure-ad-tenant-for-certificate-based-authentication"></a>Configurar al inquilino de Azure AD para el certificado de autenticación basada en 

1. Inicie Windows PowerShell con privilegios de administrador. 

2. Instale el módulo Azure AD. Debe instalar la versión [1.1.143.0](http://www.powershellgallery.com/packages/AzureADPreview/1.1.143.0) o superior.  

        Install-Module -Name AzureADPreview –RequiredVersion 1.1.143.0 

3. Conectarse a su inquilino de destino: 

        Connect-AzureAD 

### <a name="adding-a-new-certificate-authority"></a>Agregar una nueva entidad emisora de certificados

1. Establecer varias propiedades de la entidad emisora de certificados y agregarlo a Azure Active Directory: 

        $cert=Get-Content -Encoding byte "[LOCATION OF THE CER FILE]" 
        $new_ca=New-Object -TypeName Microsoft.Open.AzureAD.Model.CertificateAuthorityInformation 
        $new_ca.AuthorityType=0 
        $new_ca.TrustedCertificate=$cert 
        New-AzureADTrustedCertificateAuthority -CertificateAuthorityInformation $new_ca 

5. Obtener la entidades emisoras de certificados: 

        Get-AzureADTrustedCertificateAuthority 


### <a name="retrieving-the-list-certificate-authorities"></a>Recuperar entidades emisoras de certificados de lista

Recuperar las entidades emisoras de certificados actualmente almacenadas en Azure Active Directory para el inquilino: 

        Get-AzureADTrustedCertificateAuthority 


### <a name="removing-a-certificate-authority"></a>Quitar una entidad emisora de certificados

1.  Recuperar las entidades emisoras de certificados: 

        $c=Get-AzureADTrustedCertificateAuthority 


2. Quitar el certificado de la entidad emisora de certificados: 

        Remove-AzureADTrustedCertificateAuthority -CertificateAuthorityInformation $c[2] 


### <a name="modfiying-a-certificate-authority"></a>Modfiying una entidad emisora de certificados 

1.  Recuperar las entidades emisoras de certificados: 

        $c=Get-AzureADTrustedCertificateAuthority 


2. Modificar las propiedades de la entidad emisora de certificados: 

        $c[0].AuthorityType=1 

3. Configurar la **entidad emisora de certificados**: 

        Set-AzureADTrustedCertificateAuthority -CertificateAuthorityInformation $c[0] 




## <a name="testing-office-mobile-applications"></a>Probar aplicaciones móviles de Office  

Para probar la autenticación de certificado en la aplicación de Office mobile: 

1.  En el dispositivo de prueba, instale una aplicación móvil de Office (por ejemplo, OneDrive) de Google Play Store.

2.  Compruebe que ha configurado el certificado de usuario para el dispositivo de prueba. 

3.  Inicie la aplicación. 

4.  Escriba su nombre de usuario y, a continuación, seleccione el certificado de usuario que desea usar. 

Debe iniciar sesión correctamente en. 





## <a name="testing-exchange-activesync-client-applications"></a>Probar las aplicaciones de cliente de Exchange ActiveSync

Para obtener acceso a Exchange ActiveSync a través de la autenticación basada en certificados, un perfil EAS que contiene el certificado de cliente debe estar disponible para la aplicación. El perfil EAS debe contener la siguiente información:

- El certificado que se usará para la autenticación de usuario 

- El extremo EAS debe ser outlook.office365.com (como esta característica es compatible actualmente solo en el entorno de Exchange online inquilino múltiple)

Puede configurar un perfil EAS y coloca en el dispositivo mediante el uso de un MDM como Intune o colocando manualmente el certificado en el perfil EAS en el dispositivo.  


### <a name="testing-eas-client-applications-on-android"></a>Probar las aplicaciones cliente EAS en Android 

Para comprobar la autenticación de certificado con una aplicación en Android 5.0 (círculo) o posterior, realice los pasos siguientes:  

1. Configurar un perfil EAS en la aplicación que cumple los requisitos anteriores.  


2. Una vez que el perfil está configurado correctamente, abra la aplicación y compruebe que está sincronizando el correo. 




## <a name="revocation"></a>Revocación

Para revocar un certificado de cliente, Azure Active Directory recupera la lista de revocación de certificados (CRL) de las direcciones URL de carga como parte de la información de la entidad emisora de certificados y lo guarda. Publicar la última marca de tiempo (propiedad de**Fecha de vigencia** ) en la lista CRL se utiliza para garantizar la CRL sigue siendo válida. Periódicamente se hace referencia a la CRL para revocar el acceso a los certificados que forman parte de la lista.

Si una instantánea más revocación es necesaria (por ejemplo, si un usuario pierde un dispositivo), se puede invalidar el token de autorización del usuario. Para invalidar el token de autorización, establezca el campo **StsRefreshTokenValidFrom** para este usuario particular con Windows PowerShell. Debe actualizar el campo **StsRefreshTokenValidFrom** para cada usuario que desea revocar el acceso para.
 
Para asegurarse de que la revocación persiste, debe establecer la **Fecha de vigencia** de la CRL en una fecha después de que el valor establecido por **StsRefreshTokenValidFrom** y garantizar el certificado en cuestión se encuentra en la CRL.
 
Los pasos siguientes describen el proceso de actualización e invalidación el token de autorización estableciendo el campo **StsRefreshTokenValidFrom** . 

1. Conectar con credenciales de administrador del servicio de MSOL: 

        $msolcred = get-credential 
        connect-msolservice -credential $msolcred 

1.  Recuperar el valor actual de StsRefreshTokensValidFrom para un usuario: 

        $user = Get-MsolUser -UserPrincipalName test@yourdomain.com` 
        $user.StsRefreshTokensValidFrom 


1.  Configurar un nuevo valor StsRefreshTokensValidFrom para el igual de usuario a la marca de tiempo actual: 

        Set-MsolUser -UserPrincipalName test@yourdomain.com -StsRefreshTokensValidFrom ("03/05/2016")


La fecha establecida debe ser en el futuro. Si la fecha no está en el futuro, no se establece la propiedad **StsRefreshTokensValidFrom** . Si la fecha está en el futuro, **StsRefreshTokensValidFrom** se establece en la hora actual (no la fecha indicada por el comando conjunto MsolUser). 


<!--Image references-->
[1]: ./media/active-directory-certificate-based-authentication-android/ic195031.png