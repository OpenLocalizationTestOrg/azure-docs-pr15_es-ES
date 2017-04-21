<properties
    pageTitle="Azure Monitor ajuste automático medidas comunes. | Microsoft Azure"
    description="Obtenga información sobre las métricas que son usadas para el ajuste automático los servicios en la nube, máquinas virtuales y aplicaciones Web."
    authors="kamathashwin"
    manager="carolz"
    editor=""
    services="monitoring-and-diagnostics"
    documentationCenter="monitoring-and-diagnostics"/>

<tags
    ms.service="monitoring-and-diagnostics"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/02/2016"
    ms.author="ashwink"/>

# <a name="azure-monitor-autoscaling-common-metrics"></a>Medidas comunes de Azure Monitor ajuste automático

Ajuste automático de Monitor Azure le permite ampliar el número de instancias en ejecución hacia arriba o hacia abajo, basado en los datos de telemetría (métrica). Este documento describe métricas comunes que desee usar. En el Portal de Azure para servicios en la nube y granjas de servidores, puede elegir la métrica del recurso para ajustar a. Sin embargo, también puede elegir cualquier métrica de un recurso diferente para ajustar a.

Estos son los detalles sobre cómo buscar y las medidas que desea ajustar a la lista. Para ajustar la escala de conjuntos de escala de máquina Virtual también se aplica lo siguiente.

## <a name="compute-metrics"></a>Calcular métricas
De forma predeterminada, Azure VM v2 se suministra con la extensión de diagnósticos configurado y tienen las siguientes métricas activadas.

- [Métricas del invitado para v2 VM de Windows](#compute-metrics-for-windows-vm-v2-as-a-guest-os)
- [Métricas del invitado para Linux VM v2](#compute-metrics-for-linux-vm-v2-as-a-guest-os)

Puede usar el `Get MetricDefinitions` PoSH/API/CLI para ver las métricas disponibles para el recurso VMSS. 

Si está usando conjuntos de escala VM y no ve una métrica determinada aparece, es probable que *deshabilitado* en su extensión de diagnósticos.

Si no se está una métrica determinada muestra o se transfieren en la frecuencia que desee, puede actualizar la configuración de diagnósticos.

Si se cumple cualquiera de los casos anterior, a continuación, revise [Usar PowerShell para habilitar diagnóstico de Azure en una máquina virtual ejecuta Windows](../virtual-machines/virtual-machines-windows-ps-extensions-diagnostics.md) acerca de PowerShell para configurar y actualizar la extensión de Azure VM diagnósticos para habilitar la métrica. Este artículo también incluye un archivo de configuración de diagnósticos de ejemplo.

### <a name="compute-metrics-for-windows-vm-v2-as-a-guest-os"></a>Calcular métricas para Windows VM v2 como invitado OS

Cuando se crea una nueva máquina virtual (v2) en Azure, diagnósticos está habilitado mediante la extensión de diagnósticos.

Puede generar una lista de las mediciones mediante el comando siguiente en PowerShell.


```
Get-AzureRmMetricDefinition -ResourceId <resource_id> | Format-Table -Property Name,Unit
```

Puede crear una alerta para las siguientes métricas.

|Nombre de métrica|   Unidad|
|---|---|
|\Processor(_Total)\% tiempo de procesador    |Porcentaje|
|\Processor(_Total)\% de tiempo con privilegios   |Porcentaje|
|\Processor(_Total)\% tiempo de usuario |Porcentaje|
|Frecuencia de \Processor información (_Total) \Processor |Recuento|
|\System\Processes| Recuento|
|Recuento de \Thread \Process (_Total)| Recuento|
|Recuento de \Handle \Process (_Total)  |Recuento|
|\MEMORY\% Bytes ejecutados en uso   |Porcentaje|
|\Memory\Available bytes|   Bytes|
|\Memory\Committed bytes    |Bytes|
|Límite de \Memory\Commit|  Bytes|
|\Memory\Pool paginar Bytes|  Bytes|
|\Memory\Pool Bytes sin paginar|   Bytes|
|\PhysicalDisk(_Total)\% tiempo de disco| Porcentaje|
|\PhysicalDisk(_Total)\% tiempo de lectura de disco|    Porcentaje|
|\PhysicalDisk(_Total)\% tiempo de escritura en disco|   Porcentaje|
|\PhysicalDisk (_Total) \Disk de s.   |CountPerSecond|
|\PhysicalDisk (_Total) \Disk/seg   |CountPerSecond|
|\PhysicalDisk (_Total) \Disk/seg.  |CountPerSecond|
|\PhysicalDisk (_Total) \Disk bytes por segundo   |BytesPerSecond|
|\Disk \PhysicalDisk (_Total) Bytes s.| BytesPerSecond|
|\PhysicalDisk (_Total) \Disk Bytes de escritura/s |BytesPerSecond|
|\Avg \PhysicalDisk (_Total). Longitud de la cola de disco|  Recuento|
|\Avg \PhysicalDisk (_Total). Longitud de cola de lectura de disco| Recuento|
|\Avg \PhysicalDisk (_Total). Longitud de cola de escritura de disco |Recuento|
|\LogicalDisk(_Total)\% espacio libre| Porcentaje|
|\LogicalDisk (_Total) \Free megabytes|   Recuento|



### <a name="compute-metrics-for-linux-vm-v2-as-a-guest-os"></a>Calcular métricas para Linux VM v2 como invitado OS

Cuando se crea una nueva máquina virtual (v2) en Azure, diagnósticos está habilitado de forma predeterminada con la extensión de diagnósticos.

Puede generar una lista de las mediciones mediante el comando siguiente en PowerShell.

```
Get-AzureRmMetricDefinition -ResourceId <resource_id> | Format-Table -Property Name,Unit
```

 Puede crear una alerta para las siguientes métricas.

|Nombre de métrica                            |Unidad|
|---|---|
|\Memory\AvailableMemory                |Bytes|
|\Memory\PercentAvailableMemory         |Porcentaje|
|\Memory\UsedMemory                     |Bytes|
|\Memory\PercentUsedMemory              |Porcentaje|
|\Memory\PercentUsedByCache             |Porcentaje|
|\Memory\PagesPerSec                    |CountPerSecond|
|\Memory\PagesReadPerSec                |CountPerSecond|
|\Memory\PagesWrittenPerSec             |CountPerSecond|
|\Memory\AvailableSwap                  |Bytes|
|\Memory\PercentAvailableSwap           |Porcentaje|
|\Memory\UsedSwap                       |Bytes|
|\Memory\PercentUsedSwap                |Porcentaje|
|\Processor\PercentIdleTime             |Porcentaje|
|\Processor\PercentUserTime             |Porcentaje|
|\Processor\PercentNiceTime             |Porcentaje|
|\Processor\PercentPrivilegedTime       |Porcentaje|
|\Processor\PercentInterruptTime        |Porcentaje|
|\Processor\PercentDPCTime              |Porcentaje|
|\Processor\PercentProcessorTime        |Porcentaje|
|\Processor\PercentIOWaitTime           |Porcentaje|
|\PhysicalDisk\BytesPerSecond           |BytesPerSecond|
|\PhysicalDisk\ReadBytesPerSecond       |BytesPerSecond|
|\PhysicalDisk\WriteBytesPerSecond      |BytesPerSecond|
|\PhysicalDisk\TransfersPerSecond       |CountPerSecond|
|\PhysicalDisk\ReadsPerSecond           |CountPerSecond|
|\PhysicalDisk\WritesPerSecond          |CountPerSecond|
|\PhysicalDisk\AverageReadTime          |Segundos|
|\PhysicalDisk\AverageWriteTime         |Segundos|
|\PhysicalDisk\AverageTransferTime      |Segundos|
|\PhysicalDisk\AverageDiskQueueLength   |Recuento|
|\NetworkInterface\BytesTransmitted     |Bytes|
|\NetworkInterface\BytesReceived        |Bytes|
|\NetworkInterface\PacketsTransmitted   |Recuento|
|\NetworkInterface\PacketsReceived      |Recuento|
|\NetworkInterface\BytesTotal           |Bytes|
|\NetworkInterface\TotalRxErrors        |Recuento|
|\NetworkInterface\TotalTxErrors        |Recuento|
|\NetworkInterface\TotalCollisions      |Recuento|




## <a name="commonly-used-web-server-farm-metrics"></a>Métrica de Web (granja de servidores) utilizado con frecuencia

También puede realizar Autoescala basado en mediciones de servidor web comunes como la longitud de cola de Http. Su nombre métrica es **HttpQueueLength**.  En la sección siguiente muestra métricas de granja (aplicaciones Web) de servidor disponible.

### <a name="web-apps-metrics"></a>Métrica de aplicaciones Web

Puede generar una lista de las mediciones de aplicaciones Web mediante el comando siguiente en PowerShell.

```
Get-AzureRmMetricDefinition -ResourceId <resource_id> | Format-Table -Property Name,Unit
```

Alerta sobre o la escala por estas métricas.

|Nombre de métrica        |Unidad|
|---                |---|
|CpuPercentage      |Porcentaje|
|MemoryPercentage   |Porcentaje|
|DiskQueueLength    |Recuento|
|HttpQueueLength    |Recuento|
|BytesReceived      |Bytes|
|BytesSent          |Bytes|


## <a name="commonly-used-storage-metrics"></a>Métrica de almacenamiento utilizado con frecuencia
Puede escalar por la longitud de la cola de almacenamiento, que es el número de mensajes en la cola de almacenamiento. Longitud de la cola de almacenamiento es una métrica especial y el umbral aplicado será el número de mensajes por cada instancia. Esto significa que si hay dos instancias y si el umbral se establece en 100, se escala cuando el número total de los mensajes en la cola es 200. Por ejemplo, 100 mensajes por cada instancia.

Puede configurar esta está en el Portal de Azure en el módulo de **configuración** . Para los conjuntos de escala VM, puede actualizar la configuración de ajustar automáticamente en la plantilla ARM para usar *metricName* como *ApproximateMessageCount* y pase el identificador de la cola de almacenamiento como *metricResourceUri*.

Por ejemplo, con una cuenta de almacenamiento clásica la metricTrigger configuración Autoescala incluir:

```
"metricName": "ApproximateMessageCount",
 "metricNamespace": "",
 "metricResourceUri": "/subscriptions/s1/resourceGroups/rg1/providers/Microsoft.ClassicStorage/storageAccounts/mystorage/services/queue/queues/mystoragequeue"
 ```

Para una cuenta de almacenamiento (no clásico), incluir la metricTrigger:

```
"metricName": "ApproximateMessageCount",
"metricNamespace": "",
"metricResourceUri": "/subscriptions/s1/resourceGroups/rg1/providers/Microsoft.Storage/storageAccounts/mystorage/services/queue/queues/mystoragequeue"
```

## <a name="commonly-used-service-bus-metrics"></a>Métricas utilizados con frecuencia de Bus de servicio

Puede escalar por longitud de cola de Bus de servicio, que es el número de mensajes en la cola de Bus de servicio. Longitud de cola de Bus de servicio es una métrica especial y el umbral especificado aplicado será el número de mensajes por cada instancia. Esto significa que si hay dos instancias y si el umbral se establece en 100, se escala cuando el número total de los mensajes en la cola es 200. Por ejemplo, 100 mensajes por cada instancia.

Para los conjuntos de escala VM, puede actualizar la configuración de ajustar automáticamente en la plantilla ARM para usar *metricName* como *ApproximateMessageCount* y pase el identificador de la cola de almacenamiento como *metricResourceUri*.

```
"metricName": "MessageCount",
 "metricNamespace": "",
"metricResourceUri": "/subscriptions/s1/resourceGroups/rg1/providers/Microsoft.ServiceBus/namespaces/mySB/queues/myqueue"
```

>[AZURE.NOTE] Para Bus de servicio, el concepto de grupo de recursos no existe, pero el Administrador de recursos de Azure crea un grupo de recursos predeterminado por región. El grupo de recursos es normalmente en el formato 'Default - ServiceBus-[Región]'. Por ejemplo, 'Predeterminado-ServiceBus-EastUS', 'Predeterminado-ServiceBus-WestUS', 'Predeterminado-ServiceBus-AustraliaEast' etcetera.
