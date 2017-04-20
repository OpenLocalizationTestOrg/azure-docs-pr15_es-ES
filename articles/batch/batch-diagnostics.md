<properties
   pageTitle="Registro de diagnóstico de Azure lote | Microsoft Azure"
   description="Grabar y analizar los eventos de registro de diagnóstico para recursos de la cuenta de Azure lote como grupos y las tareas."
   services="batch"
   documentationCenter=""
   authors="mmacy"
   manager="timlt"
   editor=""/>

<tags
   ms.service="batch"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="multiple"
   ms.workload="big-compute"
   ms.date="10/12/2016"
   ms.author="marsma"/>

# <a name="azure-batch-diagnostic-logging"></a>Registro de diagnóstico de Azure por lotes

Como ocurre con muchos de los servicios de Azure, el servicio de lote emite los eventos de registro para determinados recursos durante la duración del recurso. Puede habilitar registros de diagnóstico de Azure lote a eventos de registro de recursos, como los grupos y las tareas y, a continuación, utilice los registros de diagnóstico evaluación y seguimiento. Eventos como grupo crean, eliminar grupo, comienzo de la tarea, tarea completa y otros se incluyen en los registros de diagnóstico de lote.

>[AZURE.NOTE] Este artículo describen los eventos de registro para propios recursos de cuenta por lotes, no del trabajo y datos de salida de la tarea. Para obtener más información sobre cómo almacenar los datos de salida de los trabajos y tareas, vea [los resultados de trabajo y tareas de conservar el lote de Azure](batch-task-output.md).

## <a name="prerequisites"></a>Requisitos previos

* [Cuenta de Azure por lotes](batch-account-create-portal.md)

* [Cuenta de almacenamiento de Azure](../storage/storage-create-storage-account.md#create-a-storage-account)

  Para conservar los registros de diagnóstico por lotes, debe crear una cuenta de almacenamiento de Azure donde va a almacenar los registros de Azure. Especificar esta cuenta de almacenamiento al [Habilitar el registro de diagnóstico](#enable-diagnostic-logging) para su cuenta de lote. Especificar cuando se habilita la colección de registro de la cuenta de almacenamiento no es igual a una cuenta de almacenamiento vinculado mencionada en los artículos de [paquetes de aplicaciones](batch-application-packages.md) y [persistencia de salida de tareas](batch-task-output.md) .

  >[AZURE.WARNING] Son **cargará** para los datos almacenados en su cuenta de almacenamiento de Azure. Esto incluye los registros de diagnóstico analizados en este artículo. Tenga esto en cuenta al diseñar su [Directiva de retención de registro](../monitoring-and-diagnostics/monitoring-archive-diagnostic-logs.md).

## <a name="enable-diagnostic-logging"></a>Habilitar el registro de diagnóstico

Registro de diagnóstico no está habilitado para su cuenta de proceso por lotes de forma predeterminada. Debe habilitar el registro de diagnóstico para cada cuenta de lote que desea supervisar explícitamente:

[Cómo habilitar el conjunto de registros de diagnóstico](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md#how-to-enable-collection-of-diagnostic-logs)

Se recomienda que lea el artículo de [Visión general de los registros de diagnóstico Azure](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md) completo para comprender no sólo cómo habilitar el registro, pero las categorías de registro admitidos por los diversos servicios de Azure. Por ejemplo, el lote de Azure es compatible actualmente con una categoría de registro: **Registros del servicio**.

## <a name="service-logs"></a>Registros del servicio

Registros de servicio de Azure lotes contienen eventos emitidos por el servicio de Azure lote durante la duración de un recurso por lotes como un grupo o una tarea. Cada evento emitido por lotes se almacena en la cuenta de almacenamiento especificada en formato JSON. Por ejemplo, éste es el cuerpo de una muestra, **grupo crear evento**:

```json
{
    "poolId": "myPool1",
    "displayName": "Production Pool",
    "vmSize": "Small",
    "cloudServiceConfiguration": {
        "osFamily": "4",
        "targetOsVersion": "*"
    },
    "networkConfiguration": {
        "subnetId": " "
    },
    "resizeTimeout": "300000",
    "targetDedicated": 2,
    "maxTasksPerNode": 1,
    "vmFillType": "Spread",
    "enableAutoscale": false,
    "enableInterNodeCommunication": false,
    "isAutoPool": false
}
```

Cada cuerpo de evento reside en un archivo de .json en la cuenta de almacenamiento de Azure especificada. Si desea tener acceso directo a los registros, puede revisar el [esquema de registros de diagnóstico en la cuenta de almacenamiento](../monitoring-and-diagnostics/monitoring-archive-diagnostic-logs.md#schema-of-diagnostic-logs-in-the-storage-account).

## <a name="service-log-events"></a>Eventos de registro del servicio

El servicio de lote actualmente emite los siguientes eventos de registro del servicio. Esta lista no esté completa, como eventos adicionales que se han agregado en este artículo se actualizó por última vez.

| **Eventos de registro del servicio** |
| ------------------ |
| [Crear grupo][pool_create] |
| [Inicio de eliminación de grupo][pool_delete_start] |
| [Eliminar grupo completa][pool_delete_complete] |
| [Inicio de cambio de tamaño de grupo][pool_resize_start] |
| [Grupo tamaño completo][pool_resize_complete] |
| [Comienzo de la tarea][task_start] |
| [Tarea completa][task_complete] |
| [Error de tarea][task_fail] |

## <a name="next-steps"></a>Pasos siguientes

Además de almacenar los eventos de registro de diagnóstico en una cuenta de almacenamiento de Azure, también puede transmitir eventos de registro del servicio de lote a un [Concentrador de evento de Azure](../event-hubs/event-hubs-what-is-event-hubs.md)y enviarlos a [Azure el análisis de registro](../log-analytics/log-analytics-overview.md).

* [Transmita Azure registros de diagnóstico para Hubs de evento](../monitoring-and-diagnostics/monitoring-stream-diagnostic-logs-to-event-hubs.md)

  Secuencia de eventos de diagnóstico lote al servicio de entrada de datos de gran escalabilidad, Hubs de evento. Evento Hubs pueden recopilar millones de eventos por segundo, lo cual puede, a continuación, transformar y almacenar con cualquier proveedor de análisis en tiempo real.

* [Analizar los registros de diagnóstico Azure mediante el análisis de registro](../log-analytics/log-analytics-azure-storage-json.md)

  Enviar los registros de diagnóstico para el análisis de registro donde puede analizarlos en el portal de administración de operaciones Suite (OMS) o exportarlos para el análisis en Power BI o Excel.

[pool_create]: https://msdn.microsoft.com/library/azure/mt743615.aspx
[pool_delete_start]: https://msdn.microsoft.com/library/azure/mt743610.aspx
[pool_delete_complete]: https://msdn.microsoft.com/library/azure/mt743618.aspx
[pool_resize_start]: https://msdn.microsoft.com/library/azure/mt743609.aspx
[pool_resize_complete]: https://msdn.microsoft.com/library/azure/mt743608.aspx
[task_start]: https://msdn.microsoft.com/library/azure/mt743616.aspx
[task_complete]: https://msdn.microsoft.com/library/azure/mt743612.aspx
[task_fail]: https://msdn.microsoft.com/library/azure/mt743607.aspx
