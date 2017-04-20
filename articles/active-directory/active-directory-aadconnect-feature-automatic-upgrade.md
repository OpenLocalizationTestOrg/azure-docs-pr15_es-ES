<properties
   pageTitle="Azure AD Connect: Actualización automática | Microsoft Azure"
   description="En este tema se describe la característica de actualización automática integrada de sincronización de Azure AD Connect."
   services="active-directory"
   documentationCenter=""
   authors="AndKjell"
   manager="femila"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="08/24/2016"
   ms.author="billmath"/>

# <a name="azure-ad-connect-automatic-upgrade"></a>Azure AD Connect: Actualización automática
Esta característica se introdujo con compilación 1.1.105.0 (publicado de 2016 de febrero).

## <a name="overview"></a>Información general
Asegurarse de que la instalación de Azure AD Connect esté siempre actualizada nunca ha sido más fácil con la característica de **actualización automática** . Esta característica está habilitada de forma predeterminada para las instalaciones de express y actualizaciones de sincronización de directorios. Cuando una nueva versión, la instalación se actualiza automáticamente.

Actualización automática está activada de forma predeterminada, lo siguiente:

- Express instalación de configuración y actualizaciones de sincronización de directorios.
- Uso de SQL Express LocalDB, que es lo que configuración Express siempre usa. Sincronización de directorios con SQL Express también usar LocalDB.
- La cuenta de AD es la cuenta MSOL_ predeterminado creada por la configuración de Express y sincronización de directorios.
- Tienen menos de 100000 objetos en el metaverso.

Puede ver el estado actual de una actualización automática con el cmdlet de PowerShell `Get-ADSyncAutoUpgrade`. Tiene los siguientes estados:

Estado | Comentario
---- | ----
Habilitado | Actualización automática está habilitada.
Suspendido | Establecer el sistema solamente. El sistema ya no es elegible para recibir las actualizaciones automáticas.
Deshabilitado | Actualización automática está deshabilitada.

Puede cambiar entre **activado** y **desactivado** con `Set-ADSyncAutoUpgrade`. Solo el sistema debe establecer el estado **suspendido**.

Actualización automática está utilizando el estado de conexión de Azure AD para la infraestructura de actualización. Actualización automática para que funcione, asegúrese de que ha abierto las direcciones URL de su servidor proxy para el **Estado de conexión de Azure AD** tal como se describe en [las direcciones URL de Office 365 y los intervalos de direcciones IP](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2).

Si está ejecutando el **Administrador de servicios de sincronización** de interfaz de usuario en el servidor, la actualización se suspende hasta que se cierra la interfaz de usuario.

## <a name="troubleshooting"></a>Solución de problemas
Si la instalación de conexión no actualización propio según lo esperado, a continuación, siga estos pasos para descubrir qué podría ser incorrecto.

En primer lugar, no deben esperar la actualización automática a intentar el primer día de que una versión nueva. Hay un aleatoriedad intencionado antes de intenta una actualización para que no se alarme si la instalación no está actualizada inmediatamente.

Si piensa algo no es correcta, a continuación, ejecute primero `Get-ADSyncAutoUpgrade` para garantizar la actualización automática está activada.

A continuación, asegúrese de que ha abierto las direcciones URL necesarias en su servidor proxy o firewall. Actualización automática está utilizando el estado de conexión de Azure AD como se describe en la [información general](#overview). Si usa a un servidor proxy, asegúrese de que estado se ha configurado para usar un [servidor proxy](active-directory-aadconnect-health-agent-install.md#configure-azure-ad-connect-health-agents-to-use-http-proxy). También probar la [conectividad de mantenimiento](active-directory-aadconnect-health-agent-install.md#test-connectivity-to-azure-ad-connect-health-service) con Azure AD.

Con la conectividad de Azure AD comprobado, es hora de buscar en los registros de eventos. Iniciar el Visor de sucesos y busque en el registro de eventos de **aplicación** . Agregar un filtro de registro de eventos para el origen de **Azure AD conectar actualizar** y el rango de id de evento **399 300**.  
![Filtro de registro de eventos para la actualización automática](./media/active-directory-aadconnect-feature-automatic-upgrade/eventlogfilter.png)  

Ahora puede ver los registros de eventos asociado con el estado de actualización automática.  
![Filtro de registro de eventos para la actualización automática](./media/active-directory-aadconnect-feature-automatic-upgrade/eventlogresult.png)  

El código de resultado tiene un prefijo con información general sobre el estado.

Prefijo del código de resultado | Descripción
--- | ---
Éxito | La instalación se actualizó correctamente.
UpgradeAborted | Una condición temporal detiene la actualización. Se volverá a y las expectativas es que funciona correctamente más adelante.
UpgradeNotSupported | El sistema tiene una configuración que está bloqueando el sistema se actualice automáticamente. Se volverá para ver si está cambiando el estado, pero las expectativas es que el sistema se debe actualizar manualmente.

Aquí tiene una lista de los mensajes más comunes que se encuentre. No enumera todos, pero el mensaje de resultado debe ser despejado, con lo que el problema es.

Mensaje de resultado | Descripción
--- | ---
**UpgradeAborted** |
UpgradeAbortedCouldNotSetUpgradeMarker | No se puede escribir en el registro.
UpgradeAbortedInsufficientDatabasePermissions | El grupo de administradores integrados no tiene permisos para la base de datos. Actualizar manualmente a la última versión de Azure AD Connect para solucionar este problema.
UpgradeAbortedInsufficientDiskSpace | No hay suficiente espacio en disco de actualización.
UpgradeAbortedSecurityGroupsNotPresent | No se pudo encontrar y resolver todos los grupos de seguridad que utiliza el motor de sincronización.
UpgradeAbortedServiceCanNotBeStarted | No se pudo iniciar el servicio NT **Microsoft Azure AD Sync** .
UpgradeAbortedServiceCanNotBeStopped | No se pudo detener el servicio NT **Microsoft Azure AD Sync** .
UpgradeAbortedServiceIsNotRunning | No se está ejecutando el servicio NT **Microsoft Azure AD Sync** .
UpgradeAbortedSyncCycleDisabled | La opción SyncCycle en el [Programador](active-directory-aadconnectsync-feature-scheduler.md) se ha deshabilitado.
UpgradeAbortedSyncExeInUse | El [Administrador de servicios de sincronización interfaz de usuario](active-directory-aadconnectsync-service-manager-ui.md) está abierto en el servidor.
UpgradeAbortedSyncOrConfigurationInProgress | Se está ejecutando el Asistente para instalación o se programó una sincronización fuera del programador.
**UpgradeNotSupported** |
UpgradeNotSupportedCustomizedSyncRules | Ha agregado sus propias reglas personalizadas a la configuración.
UpgradeNotSupportedDeviceWritebackEnabled | Ha habilitado la característica de [reescritura de dispositivo](active-directory-aadconnect-feature-device-writeback.md) .
UpgradeNotSupportedGroupWritebackEnabled | Ha habilitado la característica de [reescritura de grupo](active-directory-aadconnect-feature-preview.md#group-writeback) .
UpgradeNotSupportedInvalidPersistedState | La instalación no es una configuración de Express o una actualización de sincronización de directorios.
UpgradeNotSupportedMetaverseSizeExceeeded | Tiene más de 100000 objetos en metaverse.
UpgradeNotSupportedMultiForestSetup | Se está conectando a más de un bosque. Instalación Express sólo se conecta a un bosque.
UpgradeNotSupportedNonLocalDbInstall | No está utilizando una base de datos de SQL Server Express LocalDB.
UpgradeNotSupportedNonMsolAccount | La [cuenta de AD conector](active-directory-aadconnect-accounts-permissions.md#active-directory-account) ya no es la cuenta MSOL_ predeterminado.
UpgradeNotSupportedStagingModeEnabled | El servidor está configurado para estar en [modo de ensayo](active-directory-aadconnectsync-operations.md#staging-mode).
UpgradeNotSupportedUserWritebackEnabled | Ha habilitado la característica de [reescritura de usuario](active-directory-aadconnect-feature-preview.md#user-writeback) .

## <a name="next-steps"></a>Pasos siguientes
Más información acerca de cómo [integrar las identidades locales con Azure Active Directory](active-directory-aadconnect.md).
