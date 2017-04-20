<properties
    pageTitle="Acceso condicional para las aplicaciones publicadas con Proxy de la aplicación de Azure AD"
    description="Trata sobre cómo configurar el acceso condicional para publicar para tener acceso remotamente mediante Proxy de aplicación de Azure AD de aplicaciones."
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

# <a name="working-with-conditional-access"></a>Trabajar con acceso condicional

Puede configurar reglas de acceso para conceder acceso condicional a las aplicaciones publicadas mediante Proxy de la aplicación. Esto le permite:

- Requerir autenticación multifactor por aplicación
- Requerir autenticación multifactor solo cuando los usuarios no están en el trabajo
- Impedir que los usuarios acceso a la aplicación cuando no se encuentran en el trabajo

Estas reglas se pueden aplicar a todos los usuarios y grupos o solo a los usuarios y grupos específicos. De forma predeterminada la regla se aplicará a todos los usuarios que tienen acceso a la aplicación. Sin embargo también se puede restringir la regla a los usuarios que sean miembros de grupos de seguridad especificados.  

Reglas de acceso se evalúan cuando un usuario tiene acceso a una aplicación federada que usa OAuth 2.0, conectar OpenID, SAML o WS-Federation. Además, las reglas de acceso se evalúan con OAuth 2.0 y conectar OpenID cuando se usa un símbolo de actualización para adquirir un token de acceso.

## <a name="conditional-access-prerequisites"></a>Requisitos previos de acceso condicional

- Suscripción a Premium de Azure Active Directory
- Un inquilino de Azure Active Directory federado o administrado
- Los inquilinos federados requieren habilitarse esa autenticación multifactor (AMF)  
    ![Configurar reglas de acceso: requerir autenticación multifactor](./media/active-directory-application-proxy-conditional-access/application-proxy-conditional-access.png)

## <a name="configure-per-application-multi-factor-authentication"></a>Configurar la autenticación multifactor de por aplicación
1. Inicie sesión como administrador en el portal de clásico de Azure.
2. Vaya a Active Directory y seleccione el directorio en el que desea habilitar a Proxy de la aplicación.
3. Haga clic en **aplicaciones** y desplácese hacia abajo hasta la sección **Reglas de acceso** . Sólo aparece la sección reglas de acceso para las aplicaciones publicadas con Proxy de aplicación que usan autenticación federados.
4. Habilite la regla seleccionando **Habilitar reglas de acceso** para **en**.
5. Especifique los usuarios y grupos a la que se aplican las reglas. Use el botón **Agregar grupo** para seleccionar uno o varios de los grupos a los que se aplicará la regla de acceso. Este cuadro de diálogo también puede usarse para quitar los grupos seleccionados.  Cuando se seleccionan las reglas que se aplican a grupos, solo se aplicarán las reglas de acceso para los usuarios que pertenecen a uno de los grupos de seguridad especificado.  

  - Para excluir explícitamente grupos de seguridad de la regla, active **excepto** y especifica uno o más grupos. Los usuarios que sean miembros de un grupo en la lista excepto no deberán realizar autenticación multifactor.  

  - Si un usuario se ha configurado con la característica de autenticación multifactor por usuario, esta configuración tendrá prioridad sobre las reglas de autenticación multifactor de aplicación. Esto significa que un usuario que se ha configurado para cada usuario con autenticación multifactor sea preciso para realizar la autenticación multifactor incluso si la exención de las reglas de autenticación multifactor de la aplicación. Más información sobre la [autenticación multifactor y configuración de cada usuario](../multi-factor-authentication/multi-factor-authentication.md).

6. Seleccione la regla de acceso que desea configurar:
    - **Autenticación multifactor requerir**: usuarios a la que se aplican las reglas de acceso será necesarios para la autenticación multifactor completo antes de acceder a la aplicación a la que se aplica la regla.
    - **Autenticación multifactor requieren cuando no está en trabajo**: los usuarios que intenten tener acceso a la aplicación desde una dirección IP de confianza no deberán realizar autenticación multifactor. En la página de configuración de autenticación multifactor se pueden configurar los intervalos de direcciones IP confiables.
    - **Bloquear el acceso cuando no está en trabajo**: los usuarios que intenten tener acceso a la aplicación desde fuera de su red corporativa no pueda tener acceso a la aplicación.


## <a name="configuring-mfa-for-federation-services"></a>Configurar AMF para servicios de federación
Para federados inquilinos, puede realizarse con autenticación multifactor (AMF) mediante Azure Active Directory o local en el servidor de AD FS. De forma predeterminada, se producirá AMF en cualquier página hospedado en Azure Active Directory. Para configurar AMF local, ejecute Windows PowerShell y utilice la propiedad – SupportsMFA para configurar el módulo Azure AD.

En el ejemplo siguiente se muestra cómo habilitar AMF local mediante el [cmdlet Set-MsolDomainFederationSettings](https://msdn.microsoft.com/library/azure/dn194088.aspx) en el inquilino contoso.com:`Set-MsolDomainFederationSettings -DomainName contoso.com -SupportsMFA $true `

Además de establecer este indicador, la instancia de federados inquilino AD FS debe estar configurada para realizar la autenticación multifactor. Siga las instrucciones para la [implementación de Microsoft Azure autenticación multifactor local](../multi-factor-authentication/multi-factor-authentication-get-started-server.md).


## <a name="see-also"></a>Vea también

- [Trabajar con aplicaciones de notificaciones](active-directory-application-proxy-claims-aware-apps.md)
- [Publicar aplicaciones con Proxy de aplicación](active-directory-application-proxy-publish.md)
- [Habilitar inicio de sesión único](active-directory-application-proxy-sso-using-kcd.md)
- [Publicar aplicaciones usando su propio nombre de dominio](active-directory-application-proxy-custom-domains.md)

Para las últimas noticias y actualizaciones, consulte el [blog de Proxy de aplicación](http://blogs.technet.com/b/applicationproxyblog/)
