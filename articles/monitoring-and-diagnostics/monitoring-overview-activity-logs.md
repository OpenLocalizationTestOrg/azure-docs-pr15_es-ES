<properties
    pageTitle="Información general sobre el registro de actividad de Azure | Microsoft Azure"
    description="Obtenga información sobre qué es el registro de actividad de Azure y cómo se puede utilizar para conocer los eventos que se producen dentro de su suscripción de Azure."
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
    ms.date="10/25/2016"
    ms.author="johnkem"/>

# <a name="overview-of-the-azure-activity-log"></a>Información general sobre el registro de actividad de Azure
El **Registro de actividad de Azure** es un registro que proporciona comprensión de las operaciones que se realizaron en los recursos de la suscripción. El registro de actividad se denominaba anteriormente "Registros de auditoría" o "Registros operativos", ya que informa de eventos de plano de control para las suscripciones. Usar el registro de actividad, puede determinar la ' qué, quién y cuándo ' para las operaciones (SUPERPONER, publicar, eliminar) realizadas en los recursos en su suscripción de escritura. También puede conocer el estado de la operación y otras propiedades pertinentes. El registro de actividad no incluye las operaciones de lectura (GET).

El registro de actividad difiere de [Registros de diagnóstico](./monitoring-overview-of-diagnostic-logs.md), que son todos los registros emitidos por un recurso. Estos registros proporcionan datos sobre el funcionamiento de ese recurso, en lugar de las operaciones en ese recurso.

Puede recuperar los eventos desde el registro de actividad con el portal de Azure, CLI, cmdlets de PowerShell y API de REST de Azure Monitor.

Vea este [vídeo de introducción el registro de actividad](https://channel9.msdn.com/Blogs/Seth-Juarez/Logs-John-Kemnetz).  

## <a name="what-you-can-do-with-the-activity-log"></a>Qué puede hacer con el registro de actividad
Estas son algunas de las cosas que puede hacer con el registro de actividad:

- La consulta y ver en el **portal de Azure**.
- La consulta a través de la API de REST, Cmdlet de PowerShell o CLI.
- [Crear una alerta de correo electrónico o webhook desencadenadores de un evento de registro de actividad.](./insights-auditlog-to-webhook-email.md)
- [Guardar para una **Cuenta de almacenamiento** para la inspección archivado o manual](./monitoring-archive-activity-log.md). Puede especificar el tiempo de retención (en días) con **Los perfiles de registro**.
- Analizar en PowerBI con el [**paquete de contenido de PowerBI**](https://powerbi.microsoft.com/en-us/documentation/powerbi-content-pack-azure-audit-logs/).
- [Lo transmita a un **Concentrador de evento** ](./monitoring-stream-activity-logs-event-hubs.md) para recopilación por un servicio de terceros o una solución de análisis personalizada como PowerBI.

## <a name="export-the-activity-log-with-log-profiles"></a>Exportar el registro de actividad con los perfiles de registro
Un **Perfil de registro** controla cómo se exporta el registro de actividad. Con un perfil de registro, puede configurar:

- ¿Dónde debe enviarse el registro de actividad (cuenta de almacenamiento o evento Hubs)
- Deben enviarse las categorías de evento (acción de escritura, eliminar)
- Se deben exportar qué regiones (ubicaciones)
- ¿Cuánto tiempo deben conservarse el registro de actividad de una cuenta de almacenamiento: una retención de cero días significa que siempre se guardan los registros. En caso contrario, el valor puede ser cualquier número de días entre 1 y 2147483647. Si se establecen las directivas de retención pero almacenar los registros en una cuenta de almacenamiento está deshabilitada (por ejemplo, si sólo están seleccionadas las opciones de evento Hubs o OMS), las directivas de retención no tienen ningún efecto.

Esta configuración se puede configurar mediante la opción "Exportar" en el módulo de registro de actividad en el portal. También puede mediante programación [utilizando la API de REST de Azure Monitor](https://msdn.microsoft.com/library/azure/dn931927.aspx), cmdlets de PowerShell o CLI configurado. Una suscripción sólo puede tener un perfil de registro.

### <a name="configure-log-profiles-using-the-azure-portal"></a>Configurar perfiles de registro con el portal de Azure
Puede transmitir el registro de actividad a un concentrador de evento o almacenar en una cuenta de almacenamiento mediante la opción de "Exportar" en el portal de Azure.

1. Desplácese hasta el módulo de **Registro de actividad** con el menú en el lado izquierdo del portal.

    ![Vaya al registro de actividad de portal](./media/monitoring-overview-activity-logs/activity-logs-portal-navigate.png)
2. Haga clic en el botón **Exportar** en la parte superior de la hoja.

    ![Botón Exportar en el portal](./media/monitoring-overview-activity-logs/activity-logs-portal-export.png)
3. En el módulo que aparece, seleccione:  
    - áreas que le gustaría exportar eventos
    - la cuenta de almacenamiento a la que desea guardar los eventos
    - el número de días que desea conservar estos eventos de almacenamiento. Un valor de 0 días conserva indefinidamente los registros.
    - Namespace de Bus de servicio en la que desea que un concentrador de eventos que se creará para la transmisión de estos eventos.

    ![Módulo de registro de actividad de exportación](./media/monitoring-overview-activity-logs/activity-logs-portal-export-blade.png)
4. Haga clic en **Guardar** para guardar la configuración. La configuración se aplica inmediatamente a la suscripción.

### <a name="configure-log-profiles-using-the-azure-powershell-cmdlets"></a>Configurar perfiles de registro con los PowerShell Cmdlets de Azure
#### <a name="get-existing-log-profile"></a>Obtener el perfil de registro existente
```
Get-AzureRmLogProfile
```

#### <a name="add-a-log-profile"></a>Agregar un perfil de registro
```
Add-AzureRmLogProfile -Name my_log_profile -StorageAccountId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/my_storage -serviceBusRuleId /subscriptions/s1/resourceGroups/Default-ServiceBus-EastUS/providers/Microsoft.ServiceBus/namespaces/mytestSB/authorizationrules/RootManageSharedAccessKey -Locations global,westus,eastus -RetentionInDays 90 -Categories Write,Delete,Action
```

| (Propiedad)         | Obligatorio | Descripción   |
|------------------|----------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Nombre             | Sí      | Nombre de su perfil de registro.                                 |
| StorageAccountId | No       | Id. de recurso de la cuenta de almacenamiento a la que se debe guardar el registro de actividad.                         |
| serviceBusRuleId | No       | Identificador de regla de Bus de servicio para el espacio de nombres de Bus de servicio que le gustaría tener hubs de evento creados en. Es una cadena con este formato: `{service bus resource ID}/authorizationrules/{key name}`. |
| Ubicaciones        | Sí      | Valores separados por comas a la lista de regiones que le gustaría recopilar eventos de registro de actividad.              |
| RetentionInDays  | Sí      | Número de días para los eventos que deben conservarse, entre 1 y 2147483647. Un valor de cero almacena los registros de forma indefinida (siempre). |
| Categorías       | No       | Lista de valores separados por comas de categorías de eventos que se deben recopilar. Valores posibles son escribir, eliminar y acción.                                 |

#### <a name="remove-a-log-profile"></a>Quitar un perfil de registro
```
Remove-AzureRmLogProfile -name my_log_profile
```

### <a name="configure-log-profiles-using-the-azure-cross-platform-cli"></a>Configurar perfiles de registro mediante la CLI de varias plataformas de Azure
#### <a name="get-existing-log-profile"></a>Obtener el perfil de registro existente
```
azure insights logprofile list
```
```
azure insights logprofile get --name my_log_profile
```
La `name` propiedad debe ser el nombre de su perfil de registro.

#### <a name="add-a-log-profile"></a>Agregar un perfil de registro
```
azure insights logprofile add --name my_log_profile --storageId /subscriptions/s1/resourceGroups/insights-integration/providers/Microsoft.Storage/storageAccounts/my_storage --serviceBusRuleId /subscriptions/s1/resourceGroups/Default-ServiceBus-EastUS/providers/Microsoft.ServiceBus/namespaces/mytestSB/authorizationrules/RootManageSharedAccessKey --locations global,westus,eastus,northeurope --retentionInDays 90 –categories Write,Delete,Action
```

| (Propiedad)         | Obligatorio | Descripción   |
|------------------|----------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| nombre             | Sí      | Nombre de su perfil de registro.                                 |
| storageId        | No       | Id. de recurso de la cuenta de almacenamiento a la que se debe guardar el registro de actividad.                         |
| serviceBusRuleId | No       | Identificador de regla de Bus de servicio para el espacio de nombres de Bus de servicio que le gustaría tener hubs de evento creados en. Es una cadena con este formato: `{service bus resource ID}/authorizationrules/{key name}`. |
| ubicaciones        | Sí      | Valores separados por comas a la lista de regiones que le gustaría recopilar eventos de registro de actividad.              |
| retentionInDays  | Sí      | Número de días para los eventos que deben conservarse, entre 1 y 2147483647. Un valor de cero almacena los registros de forma indefinida (siempre).     |
| categorías       | No       | Lista de valores separados por comas de categorías de eventos que se deben recopilar. Valores posibles son escribir, eliminar y acción.                                 |

#### <a name="remove-a-log-profile"></a>Quitar un perfil de registro
```
azure insights logprofile delete --name my_log_profile
```

## <a name="event-schema"></a>Esquema de eventos
Cada evento en el registro de actividad tiene un blob JSON similar a este ejemplo:

```
{
  "value": [ {
    "authorization": {
      "action": "microsoft.support/supporttickets/write",
      "role": "Subscription Admin",
      "scope": "/subscriptions/s1/resourceGroups/MSSupportGroup/providers/microsoft.support/supporttickets/115012112305841"
    },
    "caller": "admin@contoso.com",
    "channels": "Operation",
    "claims": {
      "aud": "https://management.core.windows.net/",
      "iss": "https://sts.windows.net/72f988bf-86f1-41af-91ab-2d7cd011db47/",
      "iat": "1421876371",
      "nbf": "1421876371",
      "exp": "1421880271",
      "ver": "1.0",
      "http://schemas.microsoft.com/identity/claims/tenantid": "1e8d8218-c5e7-4578-9acc-9abbd5d23315 ",
      "http://schemas.microsoft.com/claims/authnmethodsreferences": "pwd",
      "http://schemas.microsoft.com/identity/claims/objectidentifier": "2468adf0-8211-44e3-95xq-85137af64708",
      "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn": "admin@contoso.com",
      "puid": "20030000801A118C",
      "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier": "9vckmEGF7zDKk1YzIY8k0t1_EAPaXoeHyPRn6f413zM",
      "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname": "John",
      "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname": "Smith",
      "name": "John Smith",
      "groups": "cacfe77c-e058-4712-83qw-f9b08849fd60,7f71d11d-4c41-4b23-99d2-d32ce7aa621c,31522864-0578-4ea0-9gdc-e66cc564d18c",
      "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name": " admin@contoso.com",
      "appid": "c44b4083-3bq0-49c1-b47d-974e53cbdf3c",
      "appidacr": "2",
      "http://schemas.microsoft.com/identity/claims/scope": "user_impersonation",
      "http://schemas.microsoft.com/claims/authnclassreference": "1"
    },
    "correlationId": "1e121103-0ba6-4300-ac9d-952bb5d0c80f",
    "description": "",
    "eventDataId": "44ade6b4-3813-45e6-ae27-7420a95fa2f8",
    "eventName": {
      "value": "EndRequest",
      "localizedValue": "End request"
    },
    "eventSource": {
      "value": "Microsoft.Resources",
      "localizedValue": "Microsoft Resources"
    },
    "httpRequest": {
      "clientRequestId": "27003b25-91d3-418f-8eb1-29e537dcb249",
      "clientIpAddress": "192.168.35.115",
      "method": "PUT"
    },
    "id": "/subscriptions/s1/resourceGroups/MSSupportGroup/providers/microsoft.support/supporttickets/115012112305841/events/44ade6b4-3813-45e6-ae27-7420a95fa2f8/ticks/635574752669792776",
    "level": "Informational",
    "resourceGroupName": "MSSupportGroup",
    "resourceProviderName": {
      "value": "microsoft.support",
      "localizedValue": "microsoft.support"
    },
    "resourceUri": "/subscriptions/s1/resourceGroups/MSSupportGroup/providers/microsoft.support/supporttickets/115012112305841",
    "operationId": "1e121103-0ba6-4300-ac9d-952bb5d0c80f",
    "operationName": {
      "value": "microsoft.support/supporttickets/write",
      "localizedValue": "microsoft.support/supporttickets/write"
    },
    "properties": {
      "statusCode": "Created"
    },
    "status": {
      "value": "Succeeded",
      "localizedValue": "Succeeded"
    },
    "subStatus": {
      "value": "Created",
      "localizedValue": "Created (HTTP Status Code: 201)"
    },
    "eventTimestamp": "2015-01-21T22:14:26.9792776Z",
    "submissionTimestamp": "2015-01-21T22:14:39.9936304Z",
    "subscriptionId": "s1"
  } ],
"nextLink": "https://management.azure.com/########-####-####-####-############$skiptoken=######"
}
```

| Nombre del elemento         | Descripción             |
|----------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| autorización        | BLOB de propiedades RBAC del evento. Normalmente incluye las propiedades de "acción", "rol" y "ámbito".|
| autor de la llamada               | Dirección de correo electrónico del usuario que ha realizado la operación, notificación de UPN o reclamo SPN de disponibilidad.|
| canales             | Uno de los valores siguientes: "Administrador", "Operación"|
| correlationId        | Normalmente un GUID con el formato de cadena. Los eventos que comparten un correlationId pertenecen a la misma acción ejercicios.   |
| Descripción          | Descripción de texto estático de un evento.                              |
| eventDataId          | Identificador único de un evento.    |
| origen de eventos          | Nombre del servicio de Azure o infraestructura que generó este evento.    |
| httpRequest          | BLOB que describa la solicitud Http. Normalmente incluye la "clientRequestId", "clientIpAddress" y "método" (HTTP. Por ejemplo, poner).                            |
| nivel                | Nivel del evento. Uno de los valores siguientes: "Crítica", "Error", "Advertencia", "Informativo" y "Detallado"  |
| resourceGroupName    | Nombre del grupo de recursos para los recursos afectados.               |
| resourceProviderName | Nombre del proveedor del recurso para el recurso afectado             |
| resourceUri          | Identificador de recursos de los recursos afectados.                               |
| operationId          | GUID compartido los eventos que se corresponden con una única operación.         |
| operationName        | Nombre de la operación.  |
| propiedades           | Conjunto de `<Key, Value>` pares (es decir, un diccionario) que describe los detalles del evento.                                |
| estado               | Cadena que describe el estado de la operación. Algunos valores comunes son: iniciado, en progreso, éxito, error, activo, resuelto.                                |
| Subestado            | Normalmente el código de estado HTTP del resto correspondiente llamar, pero también pueden incluir otras cadenas que describe un subestado, por ejemplo, estos valores comunes: Aceptar (código de estado HTTP: 200), creado (código de estado HTTP: 201), aceptada (código de estado HTTP: 202), No el contenido (código de estado HTTP: 204), solicitud incorrecta (código de estado HTTP: 400), no se encontró (código de estado HTTP: 404), conflicto (código de estado de HTTP : 409), Error interno del servidor (código de estado HTTP: 500), servicio no disponible (código de estado HTTP: 503), el tiempo de espera de puerta de enlace (código de estado HTTP: 504). |
| marca horaria del evento       | Marca de tiempo cuando el evento se genera por el servicio de Azure procesamiento de la solicitud correspondiente al evento.     |
| submissionTimestamp  | Marca de tiempo cuando el evento estuvo disponible para las consultas.             |
| subscriptionId       | Id. de suscripción de Azure  |
| nextLink             | Token de continuación para capturar el siguiente conjunto de resultados cuando se dividen en varias respuestas. Normalmente, es necesario cuando hay más de 200 registros.     |

## <a name="next-steps"></a>Pasos siguientes
- [Obtenga más información sobre el registro de actividad (anteriormente registros de auditoría)](../resource-group-audit.md)
- [El registro de actividad de Azure Hubs de evento de secuencia](./monitoring-stream-activity-logs-event-hubs.md)
