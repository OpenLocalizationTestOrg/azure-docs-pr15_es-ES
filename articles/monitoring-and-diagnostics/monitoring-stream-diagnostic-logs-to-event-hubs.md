<properties
    pageTitle="Transmita los registros de diagnóstico Azure al evento Hubs | Microsoft Azure"
    description="Aprenda a transmitir registros de diagnóstico de Azure en Hubs de evento."
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
    ms.date="08/08/2016"
    ms.author="johnkem"/>

# <a name="stream-azure-diagnostic-logs-to-event-hubs"></a>Transmita Azure registros de diagnóstico para Hubs de evento

**[Registros de diagnóstico de Azure](monitoring-overview-of-diagnostic-logs.md)** se pueden transmitir casi en tiempo real a cualquier aplicación con la opción "Exportar a evento Hubs" incorporada en el Portal o habilitando el identificador de regla de Bus de servicio en una configuración de diagnóstico a través de los Cmdlets de PowerShell de Azure o CLI de Azure.

## <a name="what-you-can-do-with-diagnostics-logs-and-event-hubs"></a>Qué puede hacer con los registros de diagnósticos y Hubs de evento
Estas son unos pocos formas que puede usar la capacidad de transmisión de registros de diagnóstico:

- **Registros de secuencia a parte 3ª registro y sistemas de telemetría** – con el tiempo, Hubs evento transmisión se convertirá en el mecanismo de los registros de diagnóstico de canalización en SIEMs de terceros y soluciones de análisis de registro.

- **Estado del servicio de vista por la transmisión de datos de "ruta de acceso rápido" a PowerBI** – usando Hubs de evento, el análisis de secuencia y PowerBI, fácilmente puede transformar los datos de diagnóstico en cerca perspectivas en tiempo real en los servicios de Azure. [En este artículo de documentación ofrece una excelente introducción de cómo configurar un evento de Hubs, procesar datos con el análisis de secuencia y usar PowerBI como salida](../stream-analytics/stream-analytics-power-bi-dashboard.md). Le presentamos unos cuantos consejos para obtener una configuración con registros de diagnóstico:
    - Los centros de evento de una categoría de registros de diagnóstico se crea automáticamente cuando Active la opción en el portal o habilitar a través de PowerShell, por lo que desea seleccionar los centros de evento en el espacio de nombres de Bus de servicio con el nombre que comience por "perspectivas-"
    - Esta es una consulta de análisis de secuencia de muestra que puede usar para analizar simplemente todos los datos de registro de una tabla de PowerBI:

```
SELECT
records.ArrayValue.[Properties you want to track]
INTO
[OutputSourceName – the PowerBI source]
FROM
[InputSourceName] AS e
CROSS APPLY GetArrayElements(e.records) AS records
```

- **Crear una plataforma de registro y telemetría personalizado** : si ya tiene una plataforma de telemetría personalizadas o está pensando acerca de cómo crear uno, altamente scalable publicación suscripción naturaleza de evento Hubs le permite flexible recopilar registros de diagnóstico. [Guía de ver Dan Rosanova de uso Hubs de evento en una plataforma de telemetría de escala global](https://azure.microsoft.com/documentation/videos/build-2015-designing-and-sizing-a-global-scale-telemetry-platform-on-azure-event-Hubs/).

## <a name="enable-streaming-of-diagnostic-logs"></a>Habilitar la transmisión de registros de diagnóstico
Puede habilitar la transmisión de registros de diagnóstico mediante programación, a través del portal, o con la [API de REST de Azure Monitor](https://msdn.microsoft.com/library/azure/dn931943.aspx). En ambos casos, puede elegir un Namespace de Bus de servicio y se crea una Hubs de evento en el espacio de nombres para cada categoría de registro que habilitar. Una **Categoría de registro** de diagnóstico es un tipo de registro que puede recopilar un recurso. Puede seleccionar qué categorías de registro que desea recopilar para un recurso específico en el Portal de Azure en el módulo de diagnóstico.

![Categorías de registro en el Portal](./media/monitoring-stream-diagnostic-logs-to-event-hubs/log-categories.png)

> [AZURE.WARNING] Habilitar y transmisión registros de diagnóstico de cálculo recursos (por ejemplo, máquinas virtuales o servicio tela) [requiere un conjunto diferente de pasos](../event-hubs/event-hubs-streaming-azure-diags-data.md).

### <a name="via-powershell-cmdlets"></a>A través de los Cmdlets de PowerShell
Para habilitar la transmisión a través de los [Cmdlets de PowerShell de Azure](insights-powershell-samples.md), puede usar el `Set-AzureRmDiagnosticSetting` cmdlet con estos parámetros:

```
Set-AzureRmDiagnosticSetting -ResourceId [your resource Id] -ServiceBusRuleId [your service bus rule id] -Enabled $true
```

El identificador de regla de Bus de servicio es una cadena con este formato: `{service bus resource ID}/authorizationrules/{key name}`, por ejemplo, `/subscriptions/{subscription ID}/resourceGroups/Default-ServiceBus-WestUS/providers/Microsoft.ServiceBus/namespaces/{service bus namespace}/authorizationrules/RootManageSharedAccessKey`.


### <a name="via-azure-cli"></a>Mediante la CLI de Azure
Para habilitar la transmisión mediante la [CLI de Azure](insights-cli-samples.md), puede usar el `insights diagnostic set` comando similar a esta:

```
azure insights diagnostic set --resourceId <resourceId> --serviceBusRuleId <serviceBusRuleId> --enabled true
```

Utilizar el mismo formato de ID de regla de Bus de servicio, como se explica el Cmdlet de PowerShell.

###<a name="via-azure-portal"></a>Mediante el Portal de Azure
Para habilitar la transmisión a través del Portal de Azure, desplácese a la configuración de diagnósticos de un recurso y seleccione 'Exportar a concentrador de evento'.

![Exportar a Hubs de evento en el Portal](./media/monitoring-stream-diagnostic-logs-to-event-hubs/portal-export.png)

Para configurarlo, seleccione un Namespace de Bus de servicio existente. El espacio de nombres seleccionado será donde se crea (si es su primera registros de diagnóstico transmisiones de tiempo) o se transmite al Hubs de evento (si ya hay recursos que se transmisión esa categoría registro espacio), y la directiva define los permisos que tiene el mecanismo de transmisión. En la actualidad, transmisión a un evento de Hubs requiere permisos Administrar, leer y enviar. Puede crear o modificar las directivas de acceso de Namespace de Bus de servicio compartido en el portal de clásico en la ficha "Configurar" para su Namespace de Bus de servicio. Para actualizar una de estas opciones de diagnóstico, el cliente debe tener el permiso de ListKey en la regla de autorización de Bus de servicio.

##<a name="how-do-i-consume-the-log-data-from-event-hubs"></a>¿Cómo se puede consumir los datos del registro de eventos Hubs?
Esto es datos de resultados de ejemplo de los centros de evento:

```
{
    "records": [
        {
            "time": "2016-07-15T18:00:22.6235064Z",
            "workflowId": "/SUBSCRIPTIONS/DF602C9C-7AA0-407D-A6FB-EB20C8BD1192/RESOURCEGROUPS/JOHNKEMTEST/PROVIDERS/MICROSOFT.LOGIC/WORKFLOWS/JOHNKEMTESTLA",
            "resourceId": "/SUBSCRIPTIONS/DF602C9C-7AA0-407D-A6FB-EB20C8BD1192/RESOURCEGROUPS/JOHNKEMTEST/PROVIDERS/MICROSOFT.LOGIC/WORKFLOWS/JOHNKEMTESTLA/RUNS/08587330013509921957/ACTIONS/SEND_EMAIL",
            "category": "WorkflowRuntime",
            "level": "Error",
            "operationName": "Microsoft.Logic/workflows/workflowActionCompleted",
            "properties": {
                "$schema": "2016-04-01-preview",
                "startTime": "2016-07-15T17:58:55.048482Z",
                "endTime": "2016-07-15T18:00:22.4109204Z",
                "status": "Failed",
                "code": "BadGateway",
                "resource": {
                    "subscriptionId": "df602c9c-7aa0-407d-a6fb-eb20c8bd1192",
                    "resourceGroupName": "JohnKemTest",
                    "workflowId": "243aac67fe904cf195d4a28297803785",
                    "workflowName": "JohnKemTestLA",
                    "runId": "08587330013509921957",
                    "location": "westus",
                    "actionName": "Send_email"
                },
                "correlation": {
                    "actionTrackingId": "29a9862f-969b-4c70-90c4-dfbdc814e413",
                    "clientTrackingId": "08587330013509921958"
                }
            }
        },
        {
            "time": "2016-07-15T18:01:15.7532989Z",
            "workflowId": "/SUBSCRIPTIONS/DF602C9C-7AA0-407D-A6FB-EB20C8BD1192/RESOURCEGROUPS/JOHNKEMTEST/PROVIDERS/MICROSOFT.LOGIC/WORKFLOWS/JOHNKEMTESTLA",
            "resourceId": "/SUBSCRIPTIONS/DF602C9C-7AA0-407D-A6FB-EB20C8BD1192/RESOURCEGROUPS/JOHNKEMTEST/PROVIDERS/MICROSOFT.LOGIC/WORKFLOWS/JOHNKEMTESTLA/RUNS/08587330012106702630/ACTIONS/SEND_EMAIL",
            "category": "WorkflowRuntime",
            "level": "Information",
            "operationName": "Microsoft.Logic/workflows/workflowActionStarted",
            "properties": {
                "$schema": "2016-04-01-preview",
                "startTime": "2016-07-15T18:01:15.5828115Z",
                "status": "Running",
                "resource": {
                    "subscriptionId": "df602c9c-7aa0-407d-a6fb-eb20c8bd1192",
                    "resourceGroupName": "JohnKemTest",
                    "workflowId": "243aac67fe904cf195d4a28297803785",
                    "workflowName": "JohnKemTestLA",
                    "runId": "08587330012106702630",
                    "location": "westus",
                    "actionName": "Send_email"
                },
                "correlation": {
                    "actionTrackingId": "042fb72c-7bd4-439e-89eb-3cf4409d429e",
                    "clientTrackingId": "08587330012106702632"
                }
            }
        }
    ]
}
```

| Nombre del elemento | Descripción                                            |
|--------------|--------------------------------------------------------|
|registros       | Matriz de todos los eventos de registro de esta carga.            |
|hora          | Hora en que se produjo el evento.                      |
|categoría      | Categoría de registro para este evento.                           |
|resourceId    | Id. de recurso del recurso que generó este evento. |
|operationName | Nombre de la operación.                                 |
|nivel         | Opcional. Indica el nivel de registro de eventos.               |
|propiedades    | Propiedades del evento.                               |


Puede ver una lista de todos los proveedores de recursos que admiten la transmisión a concentrador de evento [aquí](monitoring-overview-of-diagnostic-logs.md).

##<a name="next-steps"></a>Pasos siguientes
- [Obtenga más información acerca de los registros de diagnóstico de Azure](monitoring-overview-of-diagnostic-logs.md)
- [Introducción a Hubs de evento](../event-hubs/event-hubs-csharp-ephcs-getstarted.md)
