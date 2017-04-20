<properties
   pageTitle="Sincronización de Azure AD Connect: programador | Microsoft Azure"
   description="En este tema se describe la característica de programador incorporado de sincronización de Azure AD Connect."
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
   ms.date="08/04/2016"
   ms.author="billmath"/>

# <a name="azure-ad-connect-sync-scheduler"></a>Sincronización de Azure AD Connect: programador
Este tema describe al programador integrado en sincronización de Azure AD Connect (también conocido como motor de sincronización).

Esta característica se introdujo con compilación 1.1.105.0 (publicado de 2016 de febrero).

## <a name="overview"></a>Información general
Sincronización de Azure AD Connect sincronizará los cambios en el directorio local mediante un programador. Hay dos procesos de programador, uno para la sincronización de la contraseña y otro para la sincronización de objeto o atributo y tareas de mantenimiento. En este tema se tratará el último.

En versiones anteriores era externo al motor de sincronización el programador de objetos y atributos y se ha usado el programador de tareas de Windows o un servicio de Windows independiente para desencadenar el proceso de sincronización. El programador es con la 1.1 versiones está integrada en la sincronización del motor y permitir algún tipo de personalización. La frecuencia de sincronización nueva predeterminado es 30 minutos.

El programador es responsable de dos tareas:

- **Ciclo de sincronización**. El proceso para importar, sincronizar y exportar cambios.
- **Tareas de mantenimiento**. Renovar las claves y certificados para restablecer la contraseña y el servicio de registro de dispositivo (DRS). Purgar antiguas entradas en el registro de operaciones.

El programador propio siempre se está ejecutando, pero puede configurarlo para que solo se ejecuta una o ninguna de estas tareas. Por ejemplo si debe tener su propio proceso de ciclo de sincronización, puede deshabilitar esta tarea en el programador, pero seguir ejecutando las tareas de mantenimiento.

## <a name="scheduler-configuration"></a>Configuración del programador
Para ver la configuración actual, vaya a PowerShell y ejecutar `Get-ADSyncScheduler`. Se mostrará algo parecido a esto:

![GetSyncScheduler](./media/active-directory-aadconnectsync-feature-scheduler/getsynccyclesettings.png)

Si ve **el comando de sincronización o el cmdlet no está disponible** cuando se ejecuta este cmdlet, no se carga el módulo de PowerShell. Esto puede ocurrir si ejecuta Azure AD Connect en un controlador de dominio o en un servidor con mayores niveles de restricción de PowerShell que la configuración predeterminada. Si ve este error, vuelva a ejecutar `Import-Module ADSync` que el cmdlet disponible.

- **AllowedSyncCycleInterval**. Con más frecuencia Azure AD permitirá sincronizaciones que se produzca. No se puede sincronizar con más frecuencia que esto y aún se admiten.
- **CurrentlyEffectiveSyncCycleInterval**. La programación en vigor. Tendrá el mismo valor que CustomizedSyncInterval (si establece) si no es más frecuente AllowedSyncInterval. Si cambia CustomizedSyncCycleInterval, esto surtirá efecto después del siguiente ciclo de sincronización.
- **CustomizedSyncCycleInterval**. Si desea que el programador para ejecutar en cualquier otra frecuencia que el valor predeterminado de 30 minutos, configurará esta configuración. En la imagen anterior del programador se estableció para que se ejecute cada hora en su lugar. Si establece un valor inferior a AllowedSyncInterval, se utiliza el último.
- **NextSyncCyclePolicyType**. Delta o en inicial. Define si la siguiente ejecución debe solo proceso delta cambios, o si la siguiente ejecución debería hacer un completo para importar y sincronizar, que también debería procesar las reglas nuevas o modificadas.
- **NextSyncCycleStartTimeInUTC**. Próxima vez que el programador se iniciará el siguiente ciclo de sincronización.
- **PurgeRunHistoryInterval**. El tiempo que se mantendrán los registros de operación. Se pueden revisar en el Administrador de servicios de sincronización. El valor predeterminado es mantener estos durante 7 días.
- **SyncCycleEnabled**. Indica si el programador ejecuta la importación, sincronizar y procesos de exportación como parte de su funcionamiento.
- **MaintenanceEnabled**. Muestra si está habilitado el proceso de mantenimiento. Actualizará las teclas de certificados y purgar el registro de operaciones.
- **IsStagingModeEnabled**. Muestra si está habilitado el [modo de ensayo](active-directory-aadconnectsync-operations.md#staging-mode) .

Puede cambiar algunas de estas opciones con `Set-ADSyncScheduler`. Se pueden modificar los parámetros siguientes:

- CustomizedSyncCycleInterval
- NextSyncCyclePolicyType
- PurgeRunHistoryInterval
- SyncCycleEnabled
- MaintenanceEnabled

La configuración del programador se almacena en Azure AD. Si tiene un servidor de ensayo, cualquier cambio en el servidor principal también afectará el servidor de ensayo (con la excepción de IsStagingModeEnabled).

### <a name="customizedsynccycleinterval"></a>CustomizedSyncCycleInterval
Sintaxis:`Set-ADSyncScheduler -CustomizedSyncCycleInterval d.HH:mm:ss`  
d - días, HH - horas y mm - minutos, ss - segundos

Ejemplo:`Set-ADSyncScheduler -CustomizedSyncCycleInterval 03:00:00`  
Cambiará el programador para que se ejecute cada tres horas.

Ejemplo:`Set-ADSyncScheduler -CustomizedSyncCycleInterval 1.0:0:0`  
Cambiará el programador para ejecutarse diariamente.

## <a name="start-the-scheduler"></a>Iniciar el programador
De forma predeterminada, el programador ejecutará cada 30 minutos. En algunos casos, es recomendable ejecutar un ciclo de sincronización entre los ciclos programados o que necesita para ejecutar un tipo diferente.

**Ciclo de sincronización de Delta**  
Un ciclo de sincronización de delta incluye los siguientes pasos:

- Importación de delta en todos los conectores
- Sincronización de delta en todos los conectores
- Exportar en todos los conectores

Es posible que haya urgente cambiar que debe sincronizarse inmediatamente lo que necesita para ejecutar manualmente un ciclo. Si debe ejecutar manualmente un ciclo, a continuación, desde PowerShell ejecutar `Start-ADSyncSyncCycle -PolicyType Delta`.

**Ciclo de sincronización completa**  
Si ha realizado uno de los siguientes cambios de configuración, debe ejecutar un ciclo de sincronización completa (también conocido como Inicial):

- Agregar más objetos o los atributos que desea importar desde un directorio de origen
- Realizado cambios en las reglas de sincronización
- Cambiado [filtrado](active-directory-aadconnectsync-configure-filtering.md) por lo que se debe incluir un número diferente de objetos

Si ha realizado uno de estos cambios, debe ejecutar un ciclo de sincronización completa, por lo que el motor de sincronización tiene la oportunidad de consolidar los espacios del conector. Un ciclo de sincronización completa incluye los siguientes pasos:

- Importación completa en todos los conectores
- Sincronización completa de todos los conectores
- Exportar en todos los conectores

Para iniciar un ciclo de sincronización completa, ejecute `Start-ADSyncSyncCycle -PolicyType Initial` desde un símbolo del sistema de PowerShell. Se iniciará un ciclo de sincronización completa.

## <a name="stop-the-scheduler"></a>Detener el programador
Si el programador se está ejecutando un ciclo de sincronización debe detenerlo. Por ejemplo, si se inicia al Asistente de instalación y recibe este error:

![SyncCycleRunningError](./media/active-directory-aadconnectsync-feature-scheduler/synccyclerunningerror.png)

Cuando se ejecuta un ciclo de sincronización, no puede realizar cambios de configuración. Puede esperar hasta que el programador ha finalizado el proceso, pero también puede dejar para poder realizar los cambios inmediatamente. Detener el ciclo actual no es perjudicial y los cambios que aún no lo procesados procesará con ejecutar siguiente.

1. Para empezar, indique el programador para dejar su ciclo actual con el cmdlet de PowerShell `Stop-ADSyncSyncCycle`.
2. Detener al programador no se detendrá el conector actual de la tarea actual. Para forzar el conector para detener, realice las siguientes acciones: ![StopAConnector](./media/active-directory-aadconnectsync-feature-scheduler/stopaconnector.png)
    - Iniciar el **Servicio de sincronización** en el menú Inicio. Vaya a **conectores**, resalte el conector con el estado de **ejecución** y seleccione **dejar** de las acciones.

El programador todavía está activo y se iniciará de nuevo en la siguiente oportunidad.

## <a name="custom-scheduler"></a>Programador personalizado
Los cmdlets documentados en esta sección solo están disponibles en la compilación [1.1.130.0](active-directory-aadconnect-version-history.md#111300) o posterior.

Si el programador integrado no satisface sus necesidades, puede programar los conectores con PowerShell.

### <a name="invoke-adsyncrunprofile"></a>ADSyncRunProfile invocar
Para iniciar un perfil para un conector de esta manera:

```
Invoke-ADSyncRunProfile -ConnectorName "name of connector" -RunProfileName "name of profile"
```

Los nombres que desea usar para [nombres de conector](active-directory-aadconnectsync-service-manager-ui-connectors.md) y [Ejecutar perfil](active-directory-aadconnectsync-service-manager-ui-connectors.md#configure-run-profiles) pueden encontrarse en la [Interfaz de usuario de administrador de servicios de sincronización](active-directory-aadconnectsync-service-manager-ui.md).

![Invocar el perfil de ejecución](./media/active-directory-aadconnectsync-feature-scheduler/invokerunprofile.png)  

La `Invoke-ADSyncRunProfile` cmdlet es sincrónico, es decir, no devolverá control hasta que el conector complete la operación correctamente o con error.

Cuando programe los conectores, se recomienda programar a ellos en el siguiente orden:

1. (Completo/Delta) Importar desde directorios locales, por ejemplo, Active Directory
2. (Completo/Delta) Importación de Azure AD
3. (Completo/Delta) Sincronización de directorios local, por ejemplo, Active Directory
4. (Completo/Delta) Sincronización de Azure AD
5. Exportar a Azure AD
6. Exportar a directorios local, por ejemplo, Active Directory

Si mira el programador integrado, este es el orden en que se ejecutarán los conectores.

### <a name="get-adsyncconnectorrunstatus"></a>Get-ADSyncConnectorRunStatus
También puede supervisar el motor de sincronización para ver si está ocupado o inactivo. Este cmdlet devuelve un resultado vacío si el motor de sincronización está inactivo y no está ejecutando un conector. Si está ejecutando un conector, devolverá el nombre del conector.

```
Get-ADSyncConnectorRunStatus
```

![Estado de la ejecución de conector](./media/active-directory-aadconnectsync-feature-scheduler/getconnectorrunstatus.png)  
En la imagen anterior, la primera línea es un estado donde está inactivo el motor de sincronización. La segunda línea de cuando se ejecuta el conector de Azure AD.

## <a name="scheduler-and-installation-wizard"></a>Asistente para instalación y programador
Si inicia al Asistente para instalación, a continuación, el programador se pueden suspendido temporalmente. Esto es porque se supone que realizará cambios en la configuración y estas no se puede aplicar si actualmente se está ejecutando el motor de sincronización. Por este motivo, deje al Asistente para instalación abierto desde detendrá el motor de sincronización de realizar las acciones de sincronización.

## <a name="next-steps"></a>Pasos siguientes
Más información sobre la configuración de [sincronización de Azure AD Connect](active-directory-aadconnectsync-whatis.md) .

Más información acerca de cómo [integrar las identidades locales con Azure Active Directory](active-directory-aadconnect.md).
