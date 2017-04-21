<properties
    pageTitle="Transmitir el registro de actividad de Azure evento Hubs | Microsoft Azure"
    description="Aprenda a transmitir el registro de actividad de Azure Hubs de evento."
    authors="johnkemnetz"
    manager="rboucher"
    editor=""
    services="monitoring-and-diagnostics"
    documentationCenter="monitoring-and-diagnostics"/>

<tags
    ms.service="monitoring-and-diagnostics"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/03/2016"
    ms.author="johnkem"/>

# <a name="stream-the-azure-activity-log-to-event-hubs"></a>El registro de actividad de Azure Hubs de evento de secuencia
El [**Registro de actividad de Azure**](./monitoring-overview-activity-logs.md) se pueden transmitir casi en tiempo real a cualquier aplicación con la opción "Exportar" incorporada en el portal o habilitando el identificador de regla de Bus de servicio en un perfil de registro a través de los Cmdlets de PowerShell de Azure o CLI de Azure.

## <a name="what-you-can-do-with-the-activity-log-and-event-hubs"></a>Qué puede hacer con el registro de actividades y eventos Hubs
Estas son unos pocos formas que puede usar la capacidad de transmisión para el registro de actividad:

- **Secuencia de sistemas de registro y de telemetría de terceros** – con el tiempo, Hubs evento transmisión se convertirá en el mecanismo para el registro de actividad de canalización en SIEMs de terceros y soluciones de análisis de registro.
- **Crear una plataforma de registro y telemetría personalizado** : si ya tiene una plataforma de telemetría personalizadas o está pensando acerca de cómo crear uno, altamente scalable publicación suscripción naturaleza de evento Hubs le permite recopilar flexible el registro de actividad. [Vea a Guía de Dan Rosanova usar Hubs de evento en una plataforma de telemetría de escala global.](https://azure.microsoft.com/documentation/videos/build-2015-designing-and-sizing-a-global-scale-telemetry-platform-on-azure-event-Hubs/)

## <a name="enable-streaming-of-the-activity-log"></a>Habilitar la transmisión del registro de actividad
Puede habilitar la transmisión del registro de actividad mediante programación o a través del portal. En ambos casos, elija un Namespace de Bus de servicio y una directiva de acceso compartido para ese espacio de nombres y un concentrador de eventos se crea en ese espacio de nombres cuando se produce el primer evento de registro de actividad de nuevo. Si no tiene un Namespace de Bus de servicio, primero debe crear una. Si previamente ha transmitido eventos de registro de actividad a este Namespace de Bus de servicio, se volverá el concentrador de eventos que se ha creado previamente. La directiva de acceso compartido define los permisos que tiene el mecanismo de transmisión. En la actualidad, transmisión a un evento de Hubs requiere permisos **Administrar**, **leer**y **Enviar** . Puede crear o modificar las directivas de acceso de Namespace de Bus de servicio compartido en el portal de clásico en la ficha "Configurar" para su Namespace de Bus de servicio. Para actualizar el perfil de registro de registro de actividad para incluir la transmisión, el usuario que realiza el cambio debe tener el permiso de ListKey en esa regla de autorización de Bus de servicio.

### <a name="via-azure-portal"></a>Mediante el portal de Azure 
1. Desplácese hasta el módulo de **Registro de actividad** con el menú en el lado izquierdo del portal.

    ![Vaya al registro de actividad de portal](./media/monitoring-overview-activity-logs/activity-logs-portal-navigate.png)
2. Haga clic en el botón **Exportar** en la parte superior de la hoja.

    ![Botón Exportar en el portal](./media/monitoring-overview-activity-logs/activity-logs-portal-export.png)
3. En el módulo que aparece, puede seleccionar las regiones que le gustaría el Namespace de Bus de servicio en la que desea que un concentrador de eventos que se creará para la transmisión de estos eventos y eventos de la secuencia.

    ![Módulo de registro de actividad de exportación](./media/monitoring-overview-activity-logs/activity-logs-portal-export-blade.png)
4. Haga clic en **Guardar** para guardar la configuración. La configuración se aplica inmediatamente a la suscripción.


### <a name="via-powershell-cmdlets"></a>A través de los Cmdlets de PowerShell
Si ya existe un perfil de registro, primero debe quitar ese perfil.

1. Usar `Get-AzureRmLogProfile` para identificar si existe un perfil de registro
2. Si es así, utilice `Remove-AzureRmLogProfile` para eliminarlo.
3. Usar `Set-AzureRmLogProfile` para crear un perfil:

```
Add-AzureRmLogProfile -Name my_log_profile -StorageAccountId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/my_storage -serviceBusRuleId /subscriptions/s1/resourceGroups/Default-ServiceBus-EastUS/providers/Microsoft.ServiceBus/namespaces/mytestSB/authorizationrules/RootManageSharedAccessKey -Locations global,westus,eastus -RetentionInDays 90 -Categories Write,Delete,Action
```

El identificador de regla de Bus de servicio es una cadena con este formato: {Id. de recurso de bus de servicio} /authorizationrules/ {nombre de la clave}, por ejemplo 

### <a name="via-azure-cli"></a>Mediante la CLI de Azure
Si ya existe un perfil de registro, primero debe quitar ese perfil.

1. Usar `azure insights logprofile list` para identificar si existe un perfil de registro
2. Si es así, utilice `azure insights logprofile delete` para eliminarlo.
3. Usar `azure insights logprofile add` para crear un perfil:

```
azure insights logprofile add --name my_log_profile --storageId /subscriptions/s1/resourceGroups/insights-integration/providers/Microsoft.Storage/storageAccounts/my_storage --serviceBusRuleId /subscriptions/s1/resourceGroups/Default-ServiceBus-EastUS/providers/Microsoft.ServiceBus/namespaces/mytestSB/authorizationrules/RootManageSharedAccessKey --locations global,westus,eastus,northeurope --retentionInDays 90 –categories Write,Delete,Action
```

El identificador de regla de Bus de servicio es una cadena con este formato: `{service bus resource ID}/authorizationrules/{key name}`.
 
## <a name="how-do-i-consume-the-log-data-from-event-hubs"></a>¿Cómo se puede consumir los datos del registro de eventos Hubs?
[El esquema para el registro de actividad está disponible aquí](./monitoring-overview-activity-logs.md). Cada evento está en una matriz de blobs JSON denominados "registros".

## <a name="next-steps"></a>Pasos siguientes
- [Archivar el registro de actividad a una cuenta de almacenamiento](./monitoring-archive-activity-log.md)
- [Lea la información general sobre el registro de actividad de Azure](./monitoring-overview-activity-logs.md)
- [Configurar una alerta basada en un evento de registro de actividad](./insights-auditlog-to-webhook-email.md)
