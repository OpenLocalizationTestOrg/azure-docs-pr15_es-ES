<properties
    pageTitle="Sincronización de Azure AD Connect: cómo administrar la cuenta de servicio de Azure AD | Microsoft Azure"
    description="En este tema se explica cómo restaurar la cuenta de servicio de Azure AD."
    services="active-directory"
    keywords="AADSTS70002, AADSTS50054, cómo restablecer la contraseña de la cuenta de servicio de conector de sincronización de Azure AD Connect"
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
    ms.date="09/01/2016"
    ms.author="billmath"/>

# <a name="azure-ad-connect-sync-how-to-manage-the-azure-ad-service-account"></a>Sincronización de Azure AD Connect: cómo administrar la cuenta de servicio de Azure AD
La cuenta de servicio utilizada por el conector de Azure AD se supone que es el servicio gratuito. Si necesita restablecer sus credenciales, este tema es para usted. Por ejemplo, si un administrador Global tiene por error restablece la contraseña de la cuenta de servicio con PowerShell.

## <a name="reset-the-credentials"></a>Restablecer las credenciales
Si la cuenta de servicio definida en el conector de Azure AD no puede ponerse en contacto con Azure AD debido a problemas de autenticación, se puede restablecer la contraseña.

1. Inicie sesión en el servidor de sincronización de Azure AD Connect y empiece a PowerShell.
2. Ejecutar `Add-ADSyncAADServiceAccount`.  
![Addadsyncaadserviceaccount de cmdlet de PowerShell](./media/active-directory-aadconnectsync-howto-azureadaccount/addadsyncaadserviceaccount.png)
3. Proporcionar las credenciales de administrador Global de Azure AD.

Este cmdlet restablece la contraseña de la cuenta de servicio y lo actualiza en Azure AD y en el motor de sincronización.

## <a name="known-issues-these-steps-can-solve"></a>Estos pasos pueden resolver los problemas conocidos
Esta sección es una lista de los errores notificados por los clientes corregidos por un credenciales restablecer en la cuenta de servicio de Azure AD.

-----------
Evento 6900  
El servidor ha encontrado un error inesperado al procesar una notificación de cambio de contraseña:  
AADSTS70002: Error de validación credenciales. AADSTS50054: La contraseña antigua se usa para la autenticación.

----------
Evento 659  
Error al recuperar la configuración de sincronización de directiva de contraseñas. Microsoft.IdentityModel.Clients.ActiveDirectory.AdalServiceException:  
AADSTS70002: Error de validación credenciales. AADSTS50054: La contraseña antigua se usa para la autenticación.

## <a name="next-steps"></a>Pasos siguientes

**Temas de información general**

- [Sincronización de Azure AD Connect: entender y personalizar la sincronización](active-directory-aadconnectsync-whatis.md)
- [Integrar las identidades locales con Azure Active Directory](active-directory-aadconnect.md)
