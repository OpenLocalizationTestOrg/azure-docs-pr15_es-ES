<properties
    pageTitle="Azure AD Connect varios dominios"
    description="Este documento describe cómo instalar y configurar varios dominios de nivel superior con O365 y Azure AD."
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

# <a name="multiple-domain-support-for-federating-with-azure-ad"></a>Compatibilidad con múltiples dominios para la federación con Azure AD
La siguiente documentación proporciona instrucciones sobre cómo usar varios dominios de nivel superior y subdominios al federarse con Office 365 o dominios de Azure AD.

## <a name="multiple-top-level-domain-support"></a>Compatibilidad con múltiples dominios de nivel superior
Federar varios, dominios de nivel superior con Azure AD requiere una configuración adicional que no es necesaria cuando federar con un dominio de nivel superior.

Cuando un dominio federado con Azure AD, se establecen varias propiedades en el dominio en Azure.  Uno importante es IssuerUri.  Se trata de un URI que Azure AD sirve para identificar el dominio que está asociado el token.  La URI no es necesario para que resuelva cualquier cosa menos debe ser un URI válido.  De forma predeterminada, Azure AD establece en el valor del identificador de servicio de federación en sus instalaciones AD FS configuración.

>[AZURE.NOTE]El identificador de servicio de federación es un URI que identifica un servicio de federación.  El servicio de federación es una instancia de AD FS que funciona como el servicio de token de seguridad. 

Puede ver IssuerUri mediante el comando PowerShell `Get-MsolDomainFederationSettings - DomainName <your domain>`.

![Get-MsolDomainFederationSettings](./media/active-directory-multiple-domains/MsolDomainFederationSettings.png)

El problema surge cuando desea agregar más de un dominio de nivel superior.  Por ejemplo, supongamos que tiene la federación de instalación entre Azure AD y su entorno local.  Estoy usando bmcontoso.com para este documento.  Ahora que he agregado un dominio de primer nivel, segundo, bmfabrikam.com.

![Dominios](./media/active-directory-multiple-domains/domains.png)

Cuando se intenta convertir nuestro dominio bmfabrikam.com a ser federado, se recibe un error.  Es el motivo, Azure AD tiene una delimitación que no admite la propiedad IssuerUri tenga el mismo valor para más de un dominio.  
  

![Error de federación](./media/active-directory-multiple-domains/error.png)

### <a name="supportmultipledomain-parameter"></a>Parámetro SupportMultipleDomain

Para evitar esto, es necesario agregar un IssuerUri diferente que se puede hacer usando la `-SupportMultipleDomain` parámetro.  Este parámetro se utiliza con los siguientes cmdlets:
    
- `New-MsolFederatedDomain`
- `Convert-MsolDomaintoFederated`
- `Update-MsolFederatedDomain`

Este parámetro asegura de Azure AD configurar la IssuerUri de modo que se basa en el nombre del dominio.  Se trata de único en directorios de Azure AD.  Usar el parámetro permite que el comando PowerShell finalice correctamente.

![Error de federación](./media/active-directory-multiple-domains/convert.png)

Consultar la configuración de nuestro nuevo dominio bmfabrikam.com puede ver lo siguiente:

![Error de federación](./media/active-directory-multiple-domains/settings.png)

Tenga en cuenta que `-SupportMultipleDomain` no cambia los otros extremos que siguen estando configurados para que apunten a nuestro servicio de federación en adfs.bmcontoso.com.

Otra cosa que `-SupportMultipleDomain` qué es que garantiza que el sistema de AD FS incluye el valor de emisor correcto en tokens que emite para Azure AD. Esto hace realizando la parte de dominio de los usuarios UPN y establecer esta opción como el dominio en el IssuerUri, es decir, https://{upn sufijo} / adfs/services/confianza. 

Por lo tanto durante la autenticación a Azure AD o Office 365, el elemento IssuerUri en el símbolo del usuario se utiliza para localizar el dominio en Azure AD.  Si no encuentra una coincidencia que se producirá un error en la autenticación. 

Por ejemplo, si un usuario del UPN es bsimon@bmcontoso.com, el elemento IssuerUri en los problemas de token AD FS se establece en http://bmcontoso.com/adfs/services/trust. Esto se coincide con la configuración de Azure AD y autenticación se realizará correctamente.

A continuación se muestra la regla de notificación personalizado que implementa esta lógica:

    c:[Type == "http://schemas.xmlsoap.org/claims/UPN"] => issue(Type =   "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid", Value = regexreplace(c.Value, ".+@(?<domain>.+)", "http://${domain}/adfs/services/trust/"));


>[AZURE.IMPORTANT]Para utilizar el modificador - SupportMultipleDomain cuando intenta agregar nuevos o convertir ya agregado dominios, debe tener el programa de instalación la confianza federada a soporte técnico de ellos originalmente.  


## <a name="how-to-update-the-trust-between-ad-fs-and-azure-ad"></a>Cómo actualizar la confianza entre AD FS y Azure AD
Si no se han configurado la confianza federada entre AD FS y la instancia de Azure AD, debe volver a crear esta confianza.  Esto es porque, cuando originalmente es el programa de instalación sin la `-SupportMultipleDomain` parámetro, el IssuerUri se ha configurado con el valor predeterminado.  En la captura de pantalla que usted puede ver que el IssuerUri se establece en https://adfs.bmcontoso.com/adfs/services/trust.

Ahora, si ha agregado correctamente un dominio nuevo en el portal de Azure AD y, a continuación, intente convertirlo con `Convert-MsolDomaintoFederated -DomainName <your domain>`, se obtiene el siguiente error.

![Error de federación](./media/active-directory-multiple-domains/trust1.png)

Si intenta agregar la `-SupportMultipleDomain` conmutador recibimos el siguiente error:

![Error de federación](./media/active-directory-multiple-domains/trust2.png)

Simplemente intenta ejecutar `Update-MsolFederatedDomain -DomainName <your domain> -SupportMultipleDomain` en el dominio original también producirá un error.

![Error de federación](./media/active-directory-multiple-domains/trust3.png)

Use los siguientes pasos para agregar un dominio adicional de nivel superior.  Si ya ha agregado un dominio y no ha utilizado el `-SupportMultipleDomain` parámetro de inicio con los pasos para quitar y actualizar su dominio original.  Si no ha agregado un dominio de primer nivel aún puede iniciar con los pasos para agregar un dominio con PowerShell de Azure AD Connect.

Use los siguientes pasos para quitar la confianza de Microsoft Online y actualizar su dominio original.

2.  En el servidor de federación de AD FS abrir **administración de AD FS.** 
2.  A la izquierda, expanda **Relaciones de confianza** y **Confiar confía de fiesta**
3.  A la derecha, elimine la entrada de la **Plataforma de identidad de Microsoft Office 365** .
![Quitar Microsoft en línea](./media/active-directory-multiple-domains/trust4.png)
1.  En un equipo que tiene instalado en él de [Módulo Azure Active Directory para Windows PowerShell](https://msdn.microsoft.com/library/azure/jj151815.aspx) , ejecute lo siguiente: `$cred=Get-Credential`.  
2.  Escriba el nombre de usuario y contraseña de administrador global para el dominio de Azure AD con que son federar
2.  En PowerShell escriba`Connect-MsolService -Credential $cred`
4.  En PowerShell escriba `Update-MSOLFederatedDomain -DomainName <Federated Domain Name> -SupportMultipleDomain`.  Este es el dominio original.  Utilizando los dominios anteriores es:`Update-MsolFederatedDomain -DomainName bmcontoso.com -SupportMultipleDomain`


Realice los siguientes pasos para agregar el dominio de primer nivel nuevo con PowerShell

1.  En un equipo que tiene instalado en él de [Módulo Azure Active Directory para Windows PowerShell](https://msdn.microsoft.com/library/azure/jj151815.aspx) , ejecute lo siguiente: `$cred=Get-Credential`.  
2.  Escriba el nombre de usuario y contraseña de administrador global para el dominio de Azure AD con que son federar
2.  En PowerShell escriba`Connect-MsolService -Credential $cred`
3.  En PowerShell escriba`New-MsolFederatedDomain –SupportMultipleDomain –DomainName`

Utilice los siguientes pasos para agregar el nuevo dominio de nivel superior con Azure AD Connect.

1.  Inicio de Azure AD Connect desde el escritorio o el menú Inicio
2.  Elija "Agregar un dominio adicional de AD Azure" ![agregar una adicional dominio Azure AD](./media/active-directory-multiple-domains/add1.png)
3.  Escriba su Azure AD y las credenciales de Active Directory
4.  Seleccione el segundo dominio que desea configurar para la federación.
![Agregar una adicional dominio Azure AD](./media/active-directory-multiple-domains/add2.png)
5.  Haga clic en instalar


### <a name="verify-the-new-top-level-domain"></a>Compruebe el dominio de primer nivel nuevo
Mediante el comando PowerShell `Get-MsolDomainFederationSettings - DomainName <your domain>`puede ver el IssuerUri actualizado.  La siguiente captura de pantalla muestra la federación de configuración se actualizaron en nuestra http://bmcontoso.com/adfs/services/trust dominio original

![Get-MsolDomainFederationSettings](./media/active-directory-multiple-domains/MsolDomainFederationSettings.png)

Y la IssuerUri en nuestro nuevo dominio se ha establecido en https://bmfabrikam.com/adfs/services/trust

![Get-MsolDomainFederationSettings](./media/active-directory-multiple-domains/settings2.png)


##<a name="support-for-sub-domains"></a>Soporte técnico para subdominios
Cuando agrega un subdominio, debido a los dominios de forma Azure AD administran, heredarán la configuración del elemento primario.  Esto significa que la IssuerUri debe coincidir con los elementos primarios.

Así que Supongamos, por ejemplo por ejemplo que tiene bmcontoso.com y, a continuación, agregue corp.bmcontoso.com.  Esto significa que el IssuerUri para un usuario de corp.bmcontoso.com tendrán que estar **http://bmcontoso.com/adfs/services/trust.**  A pesar de que la regla estándar implementada encima de Azure AD generará un token con un emisor como **http://corp.bmcontoso.com/adfs/services/trust.** que no coinciden con el dominio requerido valor y se producirá un error de autenticación.

### <a name="how-to-enable-support-for-sub-domains"></a>Cómo habilitar la compatibilidad con subdominios
Para solucionar este problema AD FS confianza confianza de terceros para Microsoft Online debe actualizarse.  Para ello, debe configurar una regla de notificación personalizada para que elimina cualquier subdominios de sufijo UPN del usuario al construir el valor emisor personalizado. 

La siguiente notificación haga lo siguiente:

    c:[Type == "http://schemas.xmlsoap.org/claims/UPN"] => issue(Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid", Value = regexreplace(c.Value, "^((.*)([.|@]))?(?<domain>[^.]*[.].*)$", "http://${domain}/adfs/services/trust/"));

Utilice los siguientes pasos para agregar una notificación personalizada para admitir subdominios.

1.  Administración de abrir AD FS
2.  Haga clic con el botón secundario del mouse en la relación de confianza de Microsoft Online RP y elija Editar reclamación reglas
3.  Seleccione la regla de notificación de terceros y reemplace ![Reclamación de edición](./media/active-directory-multiple-domains/sub1.png)
4.  Reemplazar la notificación actual:
    
        c:[Type == "http://schemas.xmlsoap.org/claims/UPN"] => issue(Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid", Value = regexreplace(c.Value, ".+@(?<domain>.+)","http://${domain}/adfs/services/trust/"));
        
    con
    
        `c:[Type == "http://schemas.xmlsoap.org/claims/UPN"] => issue(Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid", Value = regexreplace(c.Value, "^((.*)([.|@]))?(?<domain>[^.]*[.].*)$", "http://${domain}/adfs/services/trust/"));`
    
![Reemplazar Reclamación](./media/active-directory-multiple-domains/sub2.png)
5.  Haga clic en Aceptar.  Haga clic en aplicar.  Haga clic en Aceptar.  Cierre Administración de AD FS.

