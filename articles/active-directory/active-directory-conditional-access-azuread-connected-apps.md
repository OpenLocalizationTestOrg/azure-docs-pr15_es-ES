<properties
    pageTitle="Azure acceso condicional para las aplicaciones de SaaS | Microsoft Azure"
    description="Acceso condicional en Azure AD le permite configurar reglas de acceso de cada aplicación con autenticación multifactor y la capacidad de bloquear el acceso a los usuarios no se encuentran en una red de confianza. "
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
    ms.date="09/26/2016"
    ms.author="markvi"/>

# <a name="getting-started-with-azure-active-directory-conditional-access"></a>Introducción a Azure Active Directory acceso condicional

Azure Active Directory condicional Access para las aplicaciones [SaaS](https://azure.microsoft.com/overview/what-is-saas/) y Azure AD conectados aplicaciones le permite que configurar acceso condicional basado en grupo, la ubicación y la sensibilidad de la aplicación. 

Con el acceso condicional según su importancia de la aplicación, puede establecer reglas de acceso de la autenticación multifactor (AMF) por aplicación. AMF por aplicación proporciona la capacidad de bloquear el acceso para los usuarios que no están en una red de confianza. Puede aplicar reglas de AMF a todos los usuarios que están asignados a la aplicación, o solo para los usuarios dentro de los grupos de seguridad especificado.  Los usuarios se pueden excluir desde el requisito de AMF si tienen acceso a la aplicación desde una dirección IP que se encuentra dentro de la red de la organización.

Estas funciones estarán disponibles para los clientes que han comprado una licencia de Azure Active Directory Premium.

## <a name="scenario-prerequisites"></a>Requisitos previos de escenario
* Licencia de Azure Active Directory Premium

* Federado o administrado inquilino de Azure Active Directory

* Los inquilinos federados requieren que la autenticación multifactor esté habilitada.

## <a name="configure-per-application-access-rules"></a>Configurar reglas de acceso por aplicación

Esta sección describe cómo configurar reglas de acceso de cada aplicación.

1. Inicie sesión en el portal de clásica Azure con una cuenta que es un administrador global de Azure AD.
2. En el panel izquierdo, seleccione **Active Directory**.
3. En la ficha directorio, seleccione el directorio.
4. Seleccione la ficha **aplicaciones** .
5. Seleccione la aplicación de la regla que desea establecer.
6. Seleccione la ficha **Configurar** .
7. Desplácese hacia abajo hasta la sección reglas de acceso. Seleccione la regla de acceso deseado.
8. Especifique los usuarios que se aplicará la regla.
9. Habilitar la directiva seleccionando **habilitado que estar encendido**.

##<a name="understanding-access-rules"></a>Descripción de las reglas de acceso

Esta sección ofrece una descripción detallada de las reglas de acceso compatibles con Azure condicional acceso a la aplicación.

### <a name="specifying-the-users-the-access-rules-apply-to"></a>Especificar los usuarios se aplican las reglas de acceso a

De forma predeterminada la directiva se aplicará a todos los usuarios que tienen acceso a la aplicación. Sin embargo, también puede restringir la directiva a los usuarios que sean miembros de grupos de seguridad especificados. El botón **Agregar grupo** se usa para seleccionar uno o varios grupos en el cuadro de diálogo de selección de grupo que se aplicará la regla de acceso. Este cuadro de diálogo también puede usarse para quitar los grupos seleccionados. Cuando se seleccionan las reglas que se aplican a grupos, solo se aplicarán las reglas de acceso para los usuarios que pertenecen a uno de los grupos de seguridad especificado.

Grupos de seguridad también pueden ser explícitamente excluir de la directiva seleccionando la opción **excepto** y especifica uno o más grupos. Los usuarios que están miembro de un grupo en la lista **excepto** no estará sujeto a los requisitos de autenticación multifactor, incluso si son miembros de un grupo que se aplica la regla de acceso.
La regla de acceso que se muestra a continuación requieren todos los usuarios en el grupo de administradores para usar la autenticación multifactor al acceder a la aplicación.

![Establecer reglas de acceso condicional con AMF](./media/active-directory-conditional-access-azuread-connected-apps/conditionalaccess-saas-apps.png)

## <a name="conditional-access-rules-with-mfa"></a>Reglas de acceso condicional con AMF
Si un usuario se ha configurado con la característica de autenticación multifactor por usuario, esta configuración en el usuario se combinará con las reglas de autenticación multifactor de la aplicación. Esto significa que un usuario que se ha configurado para cada usuario con autenticación multifactor deberán realizar la autenticación multifactor aunque exención de las reglas de autenticación multifactor de aplicación. Más información sobre la configuración de autenticación y por usuario multifactor.

### <a name="access-rule-options"></a>Opciones de regla de acceso
Se admiten las siguientes opciones:

* **Requerir autenticación multifactor**: los usuarios a la que se aplican las reglas de acceso, será necesario para la autenticación multifactor completo antes de acceder a la aplicación que se aplica la directiva.

* **Requerir autenticación multifactor cuando no está en trabajo**: un usuario que proviene de una dirección IP de confianza no deberán realizar autenticación multifactor. En la página de configuración de autenticación multifactor se pueden configurar los intervalos de direcciones IP confiables.

* **Bloquear el acceso cuando no está en trabajo**: se bloqueará un usuario que no proviene de una dirección IP de confianza. En la página de configuración de autenticación multifactor se pueden configurar los intervalos de direcciones IP confiables.

### <a name="setting-rule-status"></a>Establecer el estado de la regla
Estado de la regla de acceso permite activar o desactivar la las reglas. Cuando las reglas de acceso se desactiva, no se aplica el requisito de autenticación multifactor.

### <a name="access-rule-evaluation"></a>Evaluación de la regla de acceso

Reglas de acceso se evalúan cuando un usuario tiene acceso a una aplicación federada que usa OAuth 2.0, conectar OpenID, SAML o WS-Federation. Además, las reglas de acceso se evalúan cuando la OAuth 2.0 y conectar OpenID utilizan un símbolo de actualización para adquirir un token de acceso. Si se produce un error en la evaluación de directivas cuando se utiliza un símbolo de actualización, se devolverá el error **invalid_grant** , esto indica que el usuario debe volver a autenticar al cliente.

###<a name="configure-federation-services-to-provide-multi-factor-authentication"></a>Configurar los servicios de federación para proporcionar la autenticación multifactor

Para los inquilinos federados, puede realizarse AMF Azure Active Directory o local en el servidor de AD FS.

De forma predeterminada, se producirá AMF en una página hospedada en Azure Active Directory. Para configurar AMF local, debe establecerse la propiedad **– SupportsMFA** **true** en Azure Active Directory, mediante el uso del módulo Azure AD para Windows PowerShell.

En el ejemplo siguiente se muestra cómo habilitar AMF local mediante el [cmdlet Set-MsolDomainFederationSettings](https://msdn.microsoft.com/library/azure/dn194088.aspx) en el inquilino contoso.com:

    Set-MsolDomainFederationSettings -DomainName contoso.com -SupportsMFA $true

Además de establecer este indicador, la instancia de federados inquilino AD FS debe estar configurada para realizar la autenticación multifactor. Siga las instrucciones para [implementar la autenticación multifactor de Azure local](../multi-factor-authentication/multi-factor-authentication-get-started-server.md).

## <a name="related-articles"></a>Artículos relacionados

- [Proteger el acceso a Office 365 y otras aplicaciones conectado a Azure Active Directory](active-directory-conditional-access.md)
- [Índice de artículos de administración de aplicaciones de Azure Active Directory](active-directory-apps-index.md)
