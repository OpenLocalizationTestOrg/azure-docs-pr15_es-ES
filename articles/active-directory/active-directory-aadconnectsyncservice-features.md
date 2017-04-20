<properties
    pageTitle="Configuración y características del servicio de sincronización de Azure AD Connect | Microsoft Azure"
    description="Describe las características de servicio del servicio de sincronización de Azure AD Connect."
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
    ms.date="08/22/2016"
    ms.author="andkjell;markvi"/>

# <a name="azure-ad-connect-sync-service-features"></a>Características del servicio de sincronización de Azure AD Connect

La característica de sincronización de Azure AD Connect tiene dos componentes:

- El componente local denominado **sincronización de Azure AD Connect**, también denominado **motor de sincronización**.
- El servicio que reside en Azure AD también conocido como **servicio de sincronización de Azure AD Connect**

En este tema se explica cómo funcionan las siguientes características del **servicio de sincronización de Azure AD Connect** y cómo puede configurarlos con Windows PowerShell.

Estas opciones se configuran en el [Módulo Azure Active Directory para Windows PowerShell](http://aka.ms/aadposh). Descargue e instale de forma independiente de Azure AD Connect. Los cmdlets documentados en este tema se introdujo en la [versión de 2016 marzo (compilación 9031.1)](http://social.technet.microsoft.com/wiki/contents/articles/28552.microsoft-azure-active-directory-powershell-module-version-release-history.aspx#Version_9031_1). Si no tiene los cmdlets documentados en este tema o que no producen el mismo resultado, asegúrese de que ejecutar la versión más reciente.

Para ver la configuración en el directorio de Azure AD, ejecute `Get-MsolDirSyncFeatures`.  
![Resultado de Get-MsolDirSyncFeatures](./media/active-directory-aadconnectsyncservice-features/getmsoldirsyncfeatures.png)

Muchos de estos parámetros sólo se puede cambiar por Azure AD Connect.

Puede configurar la siguiente configuración `Set-MsolDirSyncFeature`:

DirSyncFeature | Comentario
--- | ---
[DuplicateProxyAddressResiliency<br/>DuplicateUPNResiliency](#duplicate-attribute-resiliency) | Permite a un atributo en cuarentena cuando es un duplicado de otro objeto en lugar de todo el objeto con errores durante la exportación.
[EnableSoftMatchOnUpn](#userprincipalname-soft-match) | Permite que los objetos en la combinación userPrincipalName además de la dirección SMTP principal.
[SynchronizeUpnForManagedUsers](#synchronize-userprincipalname-updates) | Permite que el motor de sincronización actualizar el atributo userPrincipalName para administrados o una licencia de usuarios (no federado).

Después de habilitar una característica, no se puede volver a deshabilitar.

>[AZURE.NOTE] De 24 de agosto de 2016 la característica de *la resistencia de atributo duplicado* está habilitada de forma predeterminada para nuevos Azure directorios de AD. Esta característica también se despliegan las en y habilitada en directorios creados antes de esta fecha. Recibirá una notificación de correo electrónico cuando el directorio está a punto de obtener esta característica está habilitada.

La siguiente configuración configurada por Azure AD Connect y no puede modificar `Set-MsolDirSyncFeature`:

DirSyncFeature | Comentario
--- | ---
DeviceWriteback | [Azure AD Connect: Habilitar reescritura de dispositivo](active-directory-aadconnect-feature-device-writeback.md)
DirectoryExtensions | [Sincronización de Azure AD Connect: extensiones de directorio](active-directory-aadconnectsync-feature-directory-extensions.md)
PasswordSync | [Implementar la sincronización de contraseña con la sincronización de Azure AD Connect](active-directory-aadconnectsync-implement-password-synchronization.md)
UnifiedGroupWriteback | [Vista previa: Reescritura de grupo](active-directory-aadconnect-feature-preview.md#group-writeback)
UserWriteback | No es compatible actualmente.

## <a name="duplicate-attribute-resiliency"></a>Resistencia de atributo duplicado
En lugar de por error a disposición objetos con el UPN duplicados / proxyAddresses, el atributo duplicado es "puesto en cuarentena" y se asigna un valor temporal. Cuando se resuelve el conflicto, el UPN temporal se cambia automáticamente el valor correcto. Este comportamiento puede estar habilitada para UPN y proxyAddress por separado. Para obtener más detalles, consulte [sincronización de identidad y la resistencia de atributo duplicado](active-directory-aadconnectsyncservice-duplicate-attribute-resiliency.md).

## <a name="userprincipalname-soft-match"></a>Coincidencia de UserPrincipalName suave
Cuando esta característica está habilitada, coincidir suaves está habilitado para UPN además de la [dirección SMTP principal](https://support.microsoft.com/kb/2641663), que siempre está habilitada. Coincidir suaves se utiliza para que coincida con los usuarios existentes de nube de Azure AD con usuarios locales.

Si debe coincidir con cuentas de AD con cuentas existentes creadas en la nube local y no está usando Exchange Online, esta característica es útil. En este escenario, generalmente no tiene una razón para establecer el atributo SMTP en la nube.

Esta característica de forma predeterminada para recién creada directorios de Azure AD. Puede ver si esta característica está habilitada para ejecutando:  
```
Get-MsolDirSyncFeatures -Feature EnableSoftMatchOnUpn
```

Si esta característica no está habilitada para el directorio de Azure AD, puede habilitar ejecutando:  
```
Set-MsolDirSyncFeature -Feature EnableSoftMatchOnUpn -Enable $true
```

## <a name="synchronize-userprincipalname-updates"></a>Sincronizar actualizaciones userPrincipalName
Tradicionalmente, actualizaciones para el atributo UserPrincipalName usando el servicio de sincronización de local se ha bloqueado, a menos que estas dos condiciones son verdaderas:

- El usuario se administra (no federado).
- El usuario no se ha asignado una licencia.

Para obtener más detalles, vea [los nombres de usuario en Office 365, Azure o Intune no coincide con el UPN local o el identificador de inicio de sesión alternativo](https://support.microsoft.com/kb/2523192).

Esta característica permite el motor de sincronización actualizar userPrincipalName cuando es modificado en local y usar sincronización de la contraseña. Si utiliza federación, esta característica no es compatible.

Esta característica de forma predeterminada para recién creada directorios de Azure AD. Puede ver si esta característica está habilitada para ejecutando:  
```
Get-MsolDirSyncFeatures -Feature SynchronizeUpnForManagedUsers
```

Si esta característica no está habilitada para el directorio de Azure AD, puede habilitar ejecutando:  
```
Set-MsolDirSyncFeature -Feature SynchronizeUpnForManagedUsers -Enable $true
```

Después de habilitar esta característica, los valores de userPrincipalName existentes permanecerán como-es. En el siguiente cambio del atributo userPrincipalName en local, la sincronización de delta normal de los usuarios actualizará el UPN.  

## <a name="see-also"></a>Vea también

- [Sincronización de Azure AD Connect](active-directory-aadconnectsync-whatis.md)
- [Integrar las identidades locales con Azure Active Directory](active-directory-aadconnect.md).
