<properties
    pageTitle="Introducción a las funciones, permisos y seguridad con el Monitor de Azure | Microsoft Azure"
    description="Obtenga información sobre cómo usar funciones integradas y los permisos del Monitor de Azure para restringir el acceso a la supervisión de recursos."
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
    ms.date="09/26/2016"
    ms.author="johnkem"/>

# <a name="get-started-with-roles-permissions-and-security-with-azure-monitor"></a>Introducción a las funciones, permisos y seguridad con el Monitor de Azure

Muchos equipos necesitan regular estrictamente el acceso a datos y la configuración de supervisión. Por ejemplo, si tiene los miembros del equipo que trabajan en modo exclusivo de supervisión (ingenieros de soporte técnico devops ingenieros) o si usa un proveedor de servicios administrados, es aconsejable concederles acceso a supervisar sólo datos y restringe su capacidad para crear, modificar o eliminar recursos. Este artículo le muestra cómo aplicar un rol RBAC supervisión integrado a un usuario en Azure o crear su propio rol personalizado para un usuario que tiene permisos de supervisión limitados rápidamente. A continuación, se describe las consideraciones de seguridad para los recursos relacionados con el Monitor de Azure y cómo puede limitar el acceso a los datos que contienen.

## <a name="built-in-monitoring-roles"></a>Funciones de supervisión integradas

Funciones integradas del Monitor Azure están diseñadas para ayudar a limitar el acceso a los recursos en una suscripción mientras sigue permite a los responsables de la supervisión de infraestructura para obtener y configurar los datos que necesitan. Monitor de Azure proporciona dos funciones de cuadro: un lector de supervisión y un colaborador supervisión.

### <a name="monitoring-reader"></a>Lector de supervisión

Personas asignadas a la función Lector supervisión pueden ver todos los datos de supervisión en una suscripción, pero no puede modificar cualquier recurso o editar cualquier configuración relacionados con la supervisión de recursos. Esta función es adecuada para los usuarios de una organización, como ingenieros de soporte técnico u operaciones que necesitan para poder:

- Ver paneles supervisión en el portal y crear sus propios escritorios supervisión privadas.
- Consulta de métricas se ajusta con la [API de REST de Azure Monitor](https://msdn.microsoft.com/library/azure/dn931930.aspx), [cmdlets de PowerShell](insights-powershell-samples.md)o [CLI entre plataformas](insights-cli-samples.md).
- Consultar el registro de actividad con el portal, API de REST de Azure Monitor, cmdlets de PowerShell o CLI entre plataformas.
- Ver la [configuración de diagnóstico](monitoring-overview-of-diagnostic-logs.md#diagnostic-settings) para un recurso.
- Ver el [perfil de registro](monitoring-overview-activity-logs.md#export-the-activity-log-with-log-profiles) para una suscripción.
- Ver la configuración de ajustar automáticamente.
- Actividad de alerta de vista y configuración.
- Acceso a los datos de aplicación perspectivas y ver datos de análisis de AI.
- Buscar datos del área de trabajo de análisis de registro (OMS) incluidos los datos de uso del área de trabajo.
- Ver los grupos de administración de análisis de registro (OMS).
- Recuperar el esquema de búsqueda de análisis de registro (OMS).
- Lista de paquetes de inteligencia de análisis de registro (OMS).
- Recuperar y ejecutar análisis de registro (OMS) búsquedas guardadas.
- Recuperar la configuración de almacenamiento de análisis de registro (OMS).

> [AZURE.NOTE] Esta función no ofrece acceso de lectura a los datos de registro que se han transmitido a un concentrador de evento o almacenados en una cuenta de almacenamiento. [Siga leyendo](#security-considerations-for-monitoring-data) para obtener más información sobre cómo configurar el acceso a estos recursos.

### <a name="monitoring-contributor"></a>Supervisión de colaborador

Personas asignadas el rol de colaborador supervisión pueden ver todos los datos de supervisión en una suscripción y crear o modificar la configuración de supervisión, pero no puede modificar los recursos. Esta función es un superconjunto de la función Lector supervisión y es adecuada para los miembros del grupo supervisión o proveedores de servicios administrados que, además de los permisos anteriores, también deben poder de una organización:

- Publicar paneles supervisión como un escritorio compartido.
- Establecer la [configuración de diagnóstico](monitoring-overview-of-diagnostic-logs.md#diagnostic-settings) para una resource.*
- Establecer el [perfil de registro](monitoring-overview-activity-logs.md#export-the-activity-log-with-log-profiles) para un suscripción.* como
- Establecer alerta actividad y configuración.
- Crear perspectivas de aplicación web pruebas y componentes.
- Área de trabajo de análisis de registro (OMS) lista claves compartidas.
- Habilitar o deshabilitar paquetes de inteligencia de análisis de registro (OMS).
- Crear y eliminar y ejecutar análisis de registro (OMS) búsquedas guardadas.
- Crear y eliminar la configuración de almacenamiento de análisis de registro (OMS).

* usuario también por separado debe tener permiso de ListKeys en el recurso de destino (almacenamiento cuenta o evento concentrador espacio de nombres) para establecer un perfil de registro o configuración de diagnóstico.

> [AZURE.NOTE] Esta función no ofrece acceso de lectura a los datos de registro que se han transmitido a un concentrador de evento o almacenados en una cuenta de almacenamiento. [Siga leyendo](#security-considerations-for-monitoring-data) para obtener más información sobre cómo configurar el acceso a estos recursos.

## <a name="monitoring-permissions-and-custom-rbac-roles"></a>Supervisión de permisos y roles RBAC personalizados
Si las funciones integradas anteriores no satisfacen las necesidades exactas de su equipo, puede [crear una función RBAC personalizada](../active-directory/role-based-access-control-custom-roles.md) con permisos más granulares. A continuación se muestran las operaciones de Azure Monitor RBAC comunes y sus descripciones.

| Operación                                                   | Descripción                                                                                                                                               |
|-------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------|
| Microsoft.Insights/AlertRules/[Read, escribir, eliminar]         | Reglas de alerta de lectura, escritura y eliminación.                                                                                                                            |
| Microsoft.Insights/AlertRules/Incidents/Read                | Lista de incidencias (historial de la regla de alertas que se activa) para las reglas de alertas. Solo se aplica al portal.                                              |
| Microsoft.Insights/AutoscaleSettings/[Read, escribir, eliminar]  | Autoescala de lectura, escritura o eliminar la configuración.                                                                                                                     |
| Microsoft.Insights/DiagnosticSettings/[Read, escribir, eliminar] | Configuración de diagnóstico de lectura, escritura y eliminación.                                                                                                                    |
| Microsoft.Insights/eventtypes/digestevents/Read             | Este permiso es necesario para los usuarios que necesiten acceder a registros de actividad a través del portal.                                                                   |
| Microsoft.Insights/eventtypes/values/Read                   | Lista de eventos de registro de actividad (administración eventos) en una suscripción. Este permiso es aplicable a la programación y portal acceso al registro de actividad. |
| Microsoft.Insights/LogDefinitions/Read                      | Este permiso es necesario para los usuarios que necesiten acceder a registros de actividad a través del portal.                                                                   |
| Microsoft.Insights/MetricDefinitions/Read                   | Lea las definiciones de métrica (lista de tipos de métricas disponibles para un recurso).                                                                                  |
| Microsoft.Insights/Metrics/Read                             | Lea métricas para un recurso.                                                                                                                              |

> [AZURE.NOTE] Acceso a las alertas, la configuración de diagnóstico y métricas para un recurso requiere que el usuario tiene acceso de lectura para el tipo de recurso y el ámbito de ese recurso. ("Escribir") de crear un perfil de configuración o de registro de diagnóstico que archiva a una cuenta de almacenamiento o secuencias de hubs evento requiere que el usuario también tengan permiso de ListKeys en el recurso de destino.

Por ejemplo, usando la tabla anterior, puede crear una función RBAC personalizada para un "lector de registro de actividad" similar a esta:

```powershell
$role = Get-AzureRmRoleDefinition "Reader"
$role.Id = $null
$role.Name = "Activity Log Reader"
$role.Description = "Can view activity logs."
$role.Actions.Clear()
$role.Actions.Add("Microsoft.Insights/eventtypes/*")
$role.AssignableScopes.Clear()
$role.AssignableScopes.Add("/subscriptions/mySubscription")
New-AzureRmRoleDefinition -Role $role 
```

## <a name="security-considerations-for-monitoring-data"></a>Consideraciones de seguridad para supervisar los datos
Supervisar los datos, especialmente los archivos de registro: pueden contener información confidencial, como las direcciones IP o nombres de usuario. Supervisar los datos de Azure tiene tres formas básicas:
1. El registro de actividad que describe todas las acciones de plano de control de su suscripción de Azure.
2. Registros de diagnóstico, que son registros emitidos por un recurso.
3. Métricas, emitidas por los recursos.

Tres de estos tipos de datos pueden almacenados en una cuenta de almacenamiento o se transmite al evento concentrador, que son recursos de Azure generales. Ya que son recursos generales, crear, eliminar y obtener acceso a ellos están una operación de privilegio suele estar reservada para que un administrador. Le recomendamos que use las siguientes prácticas para recursos relacionados con la supervisión para evitar el uso incorrecto:

- Usar una cuenta de almacenamiento dedicado para supervisar los datos. Si necesita separar los datos de supervisión en varias cuentas de almacenamiento, no compartir nunca el uso de una cuenta de almacenamiento entre supervisión y datos sin supervisión, como esto pueden accidentalmente aquellos que solo necesitan tener acceso a la supervisión de datos (ej. un SIEM de terceros) acceso a supervisión sin datos.
- Usar un único dedicado Bus de servicio o evento concentrador espacio de nombres en todas las opciones de diagnóstico por el mismo motivo como se indicó anteriormente.
- Limitar el acceso a cuentas de almacenamiento de información relacionada con la supervisión o hubs evento guardándolas en un grupo de recursos independiente y [utilizar el ámbito](../active-directory/role-based-access-control-what-is.md#basics-of-access-management-in-azure) de las funciones de supervisión para limitar el acceso a ese grupo de recursos solo.
- Nunca se concede el permiso ListKeys para cuentas de almacenamiento o hubs de evento en el ámbito de la suscripción cuando un usuario necesita acceso a los datos de supervisión. En su lugar, dar estos permisos a los usuarios a un grupo de recursos o (si tiene un grupo de recursos de supervisión dedicada) ámbito.

### <a name="limiting-access-to-monitoring-related-storage-accounts"></a>Limitar el acceso a cuentas de almacenamiento relacionada con la supervisión
Cuando un usuario o aplicación necesita obtener acceso a la supervisión de datos en una cuenta de almacenamiento, debe [generar una SA de la cuenta](https://msdn.microsoft.com/library/azure/mt584140.aspx) en la cuenta de almacenamiento que contiene los datos de supervisión con acceso de solo lectura de nivel de servicio de almacenamiento de blobs. En PowerShell, esto puede ser similar:

```powershell
$context = New-AzureStorageContext -ConnectionString "[connection string for your monitoring Storage Account]"
$token = New-AzureStorageAccountSASToken -ResourceType Service -Service Blob -Permission "rl" -Context $context
```

A continuación, puede dar el token a la entidad que necesitan para leer de ese almacenamiento cuenta y se puede de lista y leer todos los blobs en esa cuenta de almacenamiento.

O bien, si necesita controlar este permiso con RBAC, puede conceder a esa entidad el permiso Microsoft.Storage/storageAccounts/listkeys/action en esa cuenta de almacenamiento en particular. Esto es necesario para los usuarios que necesitan poder establecer una configuración de diagnóstico o iniciar perfil archivar en una cuenta de almacenamiento. Por ejemplo, puede crear la siguiente función RBAC personalizada para un usuario o la aplicación que necesita sólo lectura desde una cuenta de almacenamiento:

```powershell
$role = Get-AzureRmRoleDefinition "Reader"
$role.Id = $null
$role.Name = "Monitoring Storage Account Reader"
$role.Description = "Can get the storage account keys for a monitoring storage account."
$role.Actions.Clear()
$role.Actions.Add("Microsoft.Storage/storageAccounts/listkeys/action")
$role.Actions.Add("Microsoft.Storage/storageAccounts/Read")
$role.AssignableScopes.Clear()
$role.AssignableScopes.Add("/subscriptions/mySubscription/resourceGroups/myResourceGroup/providers/Microsoft.Storage/storageAccounts/myMonitoringStorageAccount")
New-AzureRmRoleDefinition -Role $role 
```

> [AZURE.WARNING] El permiso de ListKeys permite al usuario mostrar las teclas de la cuenta de almacenamiento primario y secundario. Estas teclas concesión al usuario todos los permisos de firmados (leer, escribir, crear BLOB, eliminar BLOB, etc.) en todos los firmado servicios (blob, cola, tabla o archivo) en dicha cuenta. Se recomienda usar un SA cuenta descrito anteriormente, cuando sea posible.

### <a name="limiting-access-to-monitoring-related-event-hubs"></a>Limitar el acceso a hubs de eventos relacionados con la supervisión
Se puede seguir un patrón similar con hubs de evento, pero primero tiene que crear una regla de autorización de escuchar dedicada. Si desea conceder acceso a una aplicación que solo debe escuchar hubs de eventos relacionados con la supervisión, haga lo siguiente:

1. Crear una directiva de acceso compartido en los concentradores de evento que se crearon para la transmisión de datos de supervisión con solo escuchar notificaciones. Esto puede realizarse en el portal. Por ejemplo, podría llamarlo "monitoringReadOnly." Si es posible, que desea dar a esta tecla directamente al consumidor y omitir el paso siguiente.
2. Si el consumidor debe podrá obtener la clave ad-hoc, conceda al usuario la acción ListKeys de ese concentrador de evento. Esto también es necesario para los usuarios que necesitan poder establecer una configuración de diagnóstico o de registro de perfil en secuencia de hubs de evento. Por ejemplo, podría crear una regla de RBAC:

   ```powershell
   $role = Get-AzureRmRoleDefinition "Reader"
   $role.Id = $null
   $role.Name = "Monitoring Event Hub Listener"
   $role.Description = "Can get the key to listen to an event hub streaming monitoring data."
   $role.Actions.Clear()
   $role.Actions.Add("Microsoft.ServiceBus/namespaces/authorizationrules/listkeys/action")
   $role.Actions.Add("Microsoft.ServiceBus/namespaces/Read")
   $role.AssignableScopes.Clear()
   $role.AssignableScopes.Add("/subscriptions/mySubscription/resourceGroups/myResourceGroup/providers/Microsoft.ServiceBus/namespaces/mySBNameSpace")
   New-AzureRmRoleDefinition -Role $role 
   ```


## <a name="next-steps"></a>Pasos siguientes
- [Obtenga información sobre RBAC y permisos en el Administrador de recursos](../active-directory/role-based-access-control-what-is.md)
- [Lea la información general de la supervisión de Azure](monitoring-overview.md)
