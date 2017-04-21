<properties
    pageTitle="Información general sobre los registros de diagnóstico Azure | Microsoft Azure"
    description="Obtenga información sobre qué son los registros de diagnóstico de Azure y cómo puede usarlos para comprender los eventos que se producen dentro de un recurso de Azure."
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
    ms.date="10/12/2016"
    ms.author="johnkem; magoedte"/>

# <a name="overview-of-azure-diagnostic-logs"></a>Información general sobre los registros de diagnóstico de Azure
**Registros de diagnóstico de Azure** son registros emitidos por un recurso que proporcionan datos enriquecidos frecuentes acerca del funcionamiento de ese recurso. El contenido de estos registros varía según el tipo de recurso (por ejemplo, registros de sistema de eventos de Windows son una categoría de registro de diagnóstico para máquinas virtuales y blob, tabla y los registros de cola son categorías de registros de diagnóstico para las cuentas de almacenamiento) y diferir desde el [registro de actividad (anteriormente conocida como registro de auditoría u operativas)](monitoring-overview-activity-logs.md), que proporciona una perspectiva de las operaciones que se realizaron en los recursos de la suscripción. No todos los recursos de soporte técnico del nuevo tipo de registros de diagnóstico se describen aquí. La lista de servicios compatibles siguiente muestra qué tipos de recursos de soporte técnico los nuevos registros de diagnóstico.

![Ubicación lógica de registros de diagnóstico](./media/monitoring-overview-of-diagnostic-logs/logical-placement-chart.png)

## <a name="what-you-can-do-with-diagnostic-logs"></a>Qué puede hacer con los registros de diagnóstico
Estas son algunas de las cosas que puede hacer con los registros de diagnósticos:

- Guardarlos en una **Cuenta de almacenamiento** para la inspección de auditoría o manual. Puede especificar el tiempo de retención (en días) con la **Configuración de diagnóstico**.
- [Les **Hubs de evento** transmita](monitoring-stream-diagnostic-logs-to-event-hubs.md) para recopilación por un servicio de terceros o una solución de análisis personalizada como PowerBI.
- Analizarlos con [El análisis del registro de OMS](../log-analytics/log-analytics-azure-storage-json.md)

## <a name="diagnostic-settings"></a>Configuración de diagnóstico
Registros de diagnóstico para recursos de cálculo no estén configurados con opciones de diagnóstico. **Configuración de diagnóstico** para un control de recurso:

- ¿Dónde se envían registros de diagnóstico (cuenta de almacenamiento, Hubs de evento y el análisis del registro de OMS).
- Se envían las categorías de registro.
- ¿Cuánto tiempo debe conservarse cada categoría de registro en una cuenta de almacenamiento: una retención de cero días significa que siempre se guardan los registros. En caso contrario, este valor puede comprendido entre 1 y 2147483647. Si están configuradas las directivas de retención pero está deshabilitado el almacenamiento de registros en una cuenta de almacenamiento (por ejemplo si sólo están seleccionadas las opciones de evento Hubs o OMS), las directivas de retención no tienen ningún efecto.

Esta configuración se fácilmente mediante el módulo de diagnóstico para un recurso en el portal de Azure, mediante los comandos de PowerShell de Azure y CLI o a través de la [API de REST de Azure Monitor](https://msdn.microsoft.com/library/azure/dn931943.aspx).

> [AZURE.WARNING] Registros de diagnóstico y mediciones de recursos de cálculo (por ejemplo, máquinas virtuales o servicio tela) usan [un mecanismo independiente para la configuración y selección de resultados](../azure-diagnostics.md).

## <a name="how-to-enable-collection-of-diagnostic-logs"></a>Cómo habilitar el conjunto de registros de diagnóstico
Conjunto de registros de diagnóstico puede habilitarse como parte de la creación de un recurso o después de haber creado un recurso a través de módulos del recurso en el Portal. También puede habilitar registros de diagnóstico en cualquier momento con los comandos de PowerShell de Azure o CLI o con la API de REST de Azure Monitor.

> [AZURE.TIP] Estas instrucciones no se pueden aplicar directamente a todos los recursos. Consulte los vínculos de esquema en la parte inferior de esta página para conocer los pasos especiales que se pueden aplicar a determinados tipos de recursos.

[Este artículo le muestra cómo puede usar una plantilla de recursos para habilitar la configuración de diagnóstico al crear un recurso](./monitoring-enable-diagnostic-logs-using-template.md)

### <a name="enable-diagnostic-logs-in-the-portal"></a>Habilitar los registros de diagnóstico en el portal
Puede habilitar registros de diagnóstico en el portal de Azure al crear tipos de recursos de cálculo habilitando la extensión de Windows o Linux Azure diagnósticos:

1.  Vaya a **nuevo** y elija el recurso que le interesa.
2.  Después de la configuración básica y seleccionando un tamaño en el módulo de **configuración** , debajo de **supervisión**, seleccione **habilitado** y elija una cuenta de almacenamiento donde desea almacenar los registros de diagnóstico. Le cobrarán tasas de datos normal para el almacenamiento y las transacciones cuando envíe diagnósticos a una cuenta de almacenamiento.

    ![Habilitar los registros de diagnóstico durante la creación de recursos](./media/monitoring-overview-of-diagnostic-logs/enable-portal-new.png)
3.  Haga clic en **Aceptar** y vuelva a crear el recurso.

Recursos de cálculo no puede habilitar registros de diagnóstico en el portal de Azure después de haber creado un recurso haciendo lo siguiente:

1.  Vaya a la hoja para el recurso y abra el módulo de **diagnóstico** .
2.  Haga clic **en** y seleccione una cuenta de almacenamiento o concentrador de evento.

    ![Habilitar los registros de diagnóstico después de la creación de recursos](./media/monitoring-overview-of-diagnostic-logs/enable-portal-existing.png)
3.  En **registros**, seleccione las **Categorías de registro** que le gustaría para recopilar o secuencia.
4.  Haga clic en **Guardar**.

### <a name="enable-diagnostic-logs-via-powershell"></a>Habilitar los registros de diagnóstico a través de PowerShell
Para habilitar los registros de diagnóstico a través de los PowerShell Cmdlets de Azure, use los comandos siguientes.

Para habilitar el almacenamiento de registros de diagnóstico en una cuenta de almacenamiento, use este comando:

    Set-AzureRmDiagnosticSetting -ResourceId [your resource id] -StorageAccountId [your storage account id] -Enabled $true

El ID de cuenta de almacenamiento es el identificador de recursos para la cuenta de almacenamiento a la que desea enviar los registros.

Para habilitar la transmisión de registros de diagnóstico a un concentrador de evento, use este comando:

    Set-AzureRmDiagnosticSetting -ResourceId [your resource id] -ServiceBusRuleId [your service bus rule id] -Enabled $true

El identificador de regla de Bus de servicio es una cadena con este formato: `{service bus resource ID}/authorizationrules/{key name}`.

Para habilitar el envío de registros de diagnóstico a un área de trabajo de análisis de registro, use este comando:

    Set-AzureRmDiagnosticSetting -ResourceId [your resource id] -WorkspaceId [log analytics workspace id] -Enabled $true

> [AZURE.NOTE] El parámetro WorkspaceId no está disponible en la versión de octubre. Estará disponible en la versión de noviembre.

Puede obtener el identificador del área de trabajo de análisis de registro en el portal de Azure.

Puede combinar estos parámetros para habilitar varias opciones de salida.

### <a name="enable-diagnostic-logs-via-cli"></a>Habilitar los registros de diagnóstico a través de CLI
Para habilitar los registros de diagnóstico a través de la CLI de Azure, use los comandos siguientes:

Para habilitar el almacenamiento de registros de diagnóstico en una cuenta de almacenamiento, use este comando:

    azure insights diagnostic set --resourceId <resourceId> --storageId <storageAccountId> --enabled true

El ID de cuenta de almacenamiento es el identificador de recursos para la cuenta de almacenamiento a la que desea enviar los registros.

Para habilitar la transmisión de registros de diagnóstico a un concentrador de evento, use este comando:

    azure insights diagnostic set --resourceId <resourceId> --serviceBusRuleId <serviceBusRuleId> --enabled true

El identificador de regla de Bus de servicio es una cadena con este formato: `{service bus resource ID}/authorizationrules/{key name}`.

Para habilitar el envío de registros de diagnóstico a un área de trabajo de análisis de registro, use este comando:

    azure insights diagnostic set --resourceId <resourceId> --workspaceId <workspaceId> --enabled true

> [AZURE.NOTE] El parámetro workspaceId no está disponible en la versión de octubre. Estará disponible en la versión de noviembre.

Puede obtener el identificador del área de trabajo de análisis de registro en el portal de Azure.

Puede combinar estos parámetros para habilitar varias opciones de salida.

### <a name="enable-diagnostic-logs-via-rest-api"></a>Habilitar los registros de diagnóstico a través de la API de REST
Para cambiar la configuración de diagnóstico con la API de REST de Azure Monitor, consulte [este documento](https://msdn.microsoft.com/library/azure/dn931931.aspx).

## <a name="manage-diagnostic-settings-in-the-portal"></a>Administrar la configuración de diagnóstico en el portal

Para garantizar que todos los recursos están correctamente configurados con la configuración de diagnóstico, puede navegar hasta el módulo de **supervisión** en el portal y abrir el módulo de **Registros de diagnóstico** .

![Módulo de diagnóstico registros en el portal](./media/monitoring-overview-of-diagnostic-logs/manage-portal-nav.png)

Puede que deba hacer clic en "Más servicios" para encontrar el módulo de supervisión.

En este módulo, puede ver y filtrar todos los recursos que son compatibles con los registros de diagnóstico para ver si tienen diagnósticos habilitados y qué cuenta de almacenamiento, hub de evento o área de trabajo de análisis de registro fluyen esos registros a.

![Resultados de módulo de registros de diagnóstico en el portal](./media/monitoring-overview-of-diagnostic-logs/manage-portal-blade.png)

Al hacer clic en un recurso se muestran todos los registros que se han almacenado en la cuenta de almacenamiento y asigne a la opción para desactivar o modificar la configuración de diagnóstico. Haga clic en el icono de descarga para descargar registros durante un período de tiempo determinado.

![Un recurso de diagnóstico registros placa](./media/monitoring-overview-of-diagnostic-logs/manage-portal-logs.png)

> [AZURE.NOTE] Registros de diagnóstico solo aparecerán en esta vista y estará disponible para su descarga si ha configurado opciones de diagnóstico para guardarlos en una cuenta de almacenamiento.

Haga clic en el vínculo Configuración de **Diagnóstico** mostrará el módulo de configuración de diagnóstico, donde puede habilitar, deshabilitar o modificar la configuración de diagnóstico para el recurso seleccionado.

## <a name="supported-services-and-schema-for-diagnostic-logs"></a>Servicios compatibles y el esquema de registros de diagnóstico
El esquema de registros de diagnóstico varía según la categoría de recursos y de registro. A continuación se muestran los servicios compatibles y su esquema.

| Servicio                       | Esquema y documentos                                                                                                   |
|-------------------------------|-----------------------------------------------------------------------------------------------------------------|
|    Equilibrador de carga de software     |    [Análisis de registro de equilibrador de carga de Azure (vista preliminar)](../load-balancer/load-balancer-monitor-log.md)             |
|    Grupos de seguridad de red    |    [Análisis de registro de grupos de seguridad de red (NSGs)](../virtual-network/virtual-network-nsg-manage-log.md)     |
|    Puertas de enlace de aplicación       |    [Registro de diagnósticos de puerta de enlace de aplicación](../application-gateway/application-gateway-diagnostics.md)     |
|    Depósito clave                  |    [Registro de Azure depósito clave](../key-vault/key-vault-logging.md)                                                 |
|    Búsqueda de Azure               |    [Habilitar y usar el análisis de tráfico de búsqueda](../search/search-traffic-analytics.md)                         |
|    Almacén de datos de lago            |    [Obtener acceso a los registros de diagnóstico para el almacén de lago de datos de Azure](../data-lake-store/data-lake-store-diagnostic-logs.md) |
|    Análisis de datos lago        |    [Obtener acceso a registros de diagnóstico para el análisis de lago de datos de Azure](../data-lake-analytics/data-lake-analytics-diagnostic-logs.md) |
|    Aplicaciones de lógica                 |    Ningún esquema disponible.                                                                                         |
|    Lote de Azure                |    [Registro de diagnóstico de Azure por lotes](../batch/batch-diagnostics.md)                                              |
|    Automatización de Azure           |    [Análisis de registro de automatización de Azure](../automation/automation-manage-send-joblogs-log-analytics.md)          |
|    Hub de evento                  |    Ningún esquema disponible.                                                                                         |
|    Bus de servicio                |    Ningún esquema disponible.                                                                                         |
|    Análisis de secuencia           |    Ningún esquema disponible.                                                                                         |

## <a name="supported-log-categories-per-resource-type"></a>Admite las categorías de registro por tipo de recurso

|Tipo de recurso|Categoría|Nombre para mostrar categorías|
|---|---|---|
|Microsoft.Automation/automationAccounts|JobLogs|Registros de trabajo|
|Microsoft.Automation/automationAccounts|JobStreams|Flujos de trabajo|
|Microsoft.Batch/batchAccounts|ServiceLog|Registros del servicio|
|Microsoft.DataLakeAnalytics/accounts|Auditoría|Registros de auditoría|
|Microsoft.DataLakeAnalytics/accounts|Solicitudes|Solicitud de registros|
|Microsoft.DataLakeStore/accounts|Auditoría|Registros de auditoría|
|Microsoft.DataLakeStore/accounts|Solicitudes|Solicitud de registros|
|Microsoft.EventHub/namespaces|ArchiveLogs|Archivar registros|
|Microsoft.EventHub/namespaces|OperationalLogs|Registros operativos|
|Microsoft.KeyVault/vaults|AuditEvent|Registros de auditoría|
|Microsoft.Logic/workflows|WorkflowRuntime|Eventos de diagnóstico en tiempo de ejecución de flujo de trabajo|
|Microsoft.Network/networksecuritygroups|NetworkSecurityGroupEvent|Eventos de grupo de seguridad de red|
|Microsoft.Network/networksecuritygroups|NetworkSecurityGroupRuleCounter|Contador de regla de grupo de seguridad de red|
|Microsoft.Network/networksecuritygroups|NetworkSecurityGroupFlowEvent|Evento de flujo de regla de grupo de seguridad de red|
|Microsoft.Network/loadBalancers|LoadBalancerAlertEvent|Eventos de alerta de equilibrador de carga|
|Microsoft.Network/loadBalancers|LoadBalancerProbeHealthStatus|Estado de salud de sondeo de equilibrador de carga|
|Microsoft.Network/applicationGateways|ApplicationGatewayAccessLog|Registro de acceso de puerta de enlace de aplicación|
|Microsoft.Network/applicationGateways|ApplicationGatewayPerformanceLog|Registro de rendimiento de puerta de enlace de aplicación|
|Microsoft.Network/applicationGateways|ApplicationGatewayFirewallLog|Registro de Firewall de puerta de enlace de aplicación|
|Microsoft.Search/searchServices|OperationLogs|Registros de operación|
|Microsoft.ServerManagement/nodes|RequestLogs|Solicitud de registros|
|Microsoft.ServiceBus/namespaces|OperationalLogs|Registros operativos|
|Microsoft.StreamAnalytics/streamingjobs|Ejecución|Ejecución|
|Microsoft.StreamAnalytics/streamingjobs|Creación|Creación|

## <a name="next-steps"></a>Pasos siguientes
- [Secuencia de registros de diagnóstico para **Hubs de evento**](monitoring-stream-diagnostic-logs-to-event-hubs.md)
- [Cambiar la configuración de diagnóstico con la API de REST de Monitor de Azure](https://msdn.microsoft.com/library/azure/dn931931.aspx)
- [Analizar los registros de análisis de registro de OMS](../log-analytics/log-analytics-azure-storage-json.md)
