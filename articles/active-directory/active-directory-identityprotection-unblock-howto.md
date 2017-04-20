<properties
    pageTitle="Azure Active Directory identidad Protection - cómo desbloquear usuarios | Microsoft Azure"
    description="Obtenga información sobre cómo desbloquear usuarios que fueron bloqueados por una directiva de protección de identidad de Azure Active Directory."
    services="active-directory"
    keywords="protección de identidad de Azure directorio activo, desbloquear usuario"
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
    ms.date="09/20/2016"
    ms.author="markvi"/>

#<a name="azure-active-directory-identity-protection---how-to-unblock-users"></a>Azure Active Directory identidad Protection - cómo desbloquear usuarios

Con Azure Active Directory identidad protección, puede configurar directivas para impedir que los usuarios si se cumplen las condiciones configuradas. Normalmente, un usuario bloqueado contactos asistencia para convertirse en Desbloquear. Este tema se explica los pasos que puede realizar para desbloquear un usuario bloqueado.


## <a name="determine-the-reason-for-blocking"></a>Determinar el motivo del bloqueo

Como primer paso para desbloquear un usuario, debe determinar el tipo de directiva que ha bloqueado el usuario porque los pasos siguientes dependen de él. Con Azure Active Directory identidad protección, un usuario puede estar bloqueado por una directiva de inicio de sesión de riesgo o un usuario riesgo. 

Puede obtener el tipo de directiva que se ha bloqueado un usuario desde el encabezado en el cuadro de diálogo que se presenta al usuario durante un intento de inicio de sesión:

|Directiva | Cuadro de diálogo de usuario|
|--- | --- |
|Inicio de sesión de riesgo | ![El inicio de sesión bloqueado](./media/active-directory-identityprotection-unblock-howto/02.png) |
|Riesgo de usuario | ![Cuenta bloqueada](./media/active-directory-identityprotection-unblock-howto/104.png) |


Un usuario que ha sido bloqueado por:

- Una directiva de inicio de sesión de riesgo es también conocida como sesión sospechoso
- Una directiva de riesgo de usuario es también conocido como una cuenta en riesgo

 
## <a name="unblocking-suspicious-sign-ins"></a>Desbloquear inicios de sesión sospechosos

Para desbloquear una sesión sospechoso, tiene las siguientes opciones:

1. **Iniciar sesión desde un dispositivo o ubicación familiar** - una razón habitual para inicios de sesión sospechosos bloqueados son intentos de inicio de sesión de ubicaciones desconocidas o dispositivos. Los usuarios pueden determinar rápidamente si este es el motivo por el bloqueo al intentar iniciar sesión desde un dispositivo o ubicación familiar.


3. **Excluir de la directiva** - si cree que la configuración actual de la directiva de inicio de sesión está causando problemas para usuarios específicos, puede excluir los usuarios de ella. Vea la [Directiva de inicio de sesión de riesgo](active-directory-identityprotection.md#sign-in-risk-policy) para obtener más detalles.
 
4. **Deshabilitar directiva** - si cree que la configuración de directiva está causando problemas para todos los usuarios, puede deshabilitar la directiva. Vea la [Directiva de inicio de sesión de riesgo](active-directory-identityprotection.md#sign-in-risk-policy) para obtener más detalles.


## <a name="unblocking-accounts-at-risk"></a>Desbloquear cuentas en riesgo

Para desbloquear una cuenta en riesgo, tiene las siguientes opciones:

1. **Restablecer la contraseña** - puede restablecer la contraseña del usuario. Vea el [restablecimiento de contraseña segura manual](active-directory-identityprotection.md#manual-secure-password-reset) para obtener más detalles.

2. **Descartar todos los eventos de riesgo** - la directiva de riesgo usuario impide que un usuario si se ha alcanzado el nivel de riesgo usuario configurado para bloquear el acceso. Puede reducir un usuario del nivel de riesgo cerrando manualmente notificadas eventos de riesgo. Para obtener más detalles, consulte [Cerrar eventos de riesgo manualmente](active-directory-identityprotection.md#closing-risk-events-manually).

3. **Excluir de la directiva** - si cree que la configuración actual de la directiva de inicio de sesión está causando problemas para usuarios específicos, puede excluir los usuarios de ella. Consulte [la directiva de riesgo de usuario](active-directory-identityprotection.md#user-risk-policy) para obtener más detalles.
 
4. **Deshabilitar directiva** - si cree que la configuración de directiva está causando problemas para todos los usuarios, puede deshabilitar la directiva. Consulte [la directiva de riesgo de usuario](active-directory-identityprotection.md#user-risk-policy) para obtener más detalles.




## <a name="next-steps"></a>Pasos siguientes

 ¿Desea obtener más información sobre protección de la identidad de Azure AD? Consulte [Protección de identidad de Azure Active Directory](active-directory-identityprotection.md).
 

