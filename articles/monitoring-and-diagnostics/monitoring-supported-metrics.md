<properties
    pageTitle="Métrica de Azure Monitor - métricas admitidas por tipo de recurso | Microsoft Azure"
    description="Lista de las mediciones disponibles para cada tipo de recurso con el Monitor de Azure."
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

# <a name="supported-metrics-with-azure-monitor"></a>Métricas compatibles con el Monitor de Azure

Monitor de Azure proporciona varias maneras para interactuar con las mediciones, que incluyen gráficos de ellos en el portal, obtener acceso a ellos a través de la API de REST o consultar ellos usando PowerShell o CLI. A continuación se muestra una lista completa de todas las métricas están disponibles con la canalización de métricas del Monitor de Azure.

> [AZURE.NOTE] Otras métricas pueden estar disponibles en el portal o mediante las API de heredados. Esta lista incluye solo métricas de versión preliminar pública disponibles con la versión preliminar pública de la canalización de métrica de Monitor de Azure consolidada.

## <a name="microsoftbatchbatchaccounts"></a>Microsoft.Batch/batchAccounts

|Métrica|Nombre para mostrar métrica|Unidad|Tipo de agregación|Descripción|
|---|---|---|---|---|
|CoreCount|Recuento de Core|Recuento|Total|Número total de núcleos de la cuenta de proceso por lotes|
|TotalNodeCount|Número de nodos|Recuento|Total|Número total de nodos en la cuenta de proceso por lotes|
|CreatingNodeCount|Número de nodos de creación|Recuento|Total|Número de nodos que se ha creado|
|StartingNodeCount|Número de nodo inicial|Recuento|Total|Número de nodos iniciar|
|WaitingForStartTaskNodeCount|Esperando para el número de nodos de tareas de inicio|Recuento|Total|Número de nodos en espera completar la tarea de inicio|
|StartTaskFailedNodeCount|Número de nodos de error de la tarea de inicio|Recuento|Total|Número de nodos donde ha fallado Iniciar tarea|
|IdleNodeCount|Recuento de nodo inactivo|Recuento|Total|Número de nodos inactivos|
|OfflineNodeCount|Número de nodos sin conexión|Recuento|Total|Número de nodos sin conexión|
|RebootingNodeCount|Reiniciar el número de nodos|Recuento|Total|Número de nodos de reiniciar|
|ReimagingNodeCount|Número de nodos restante|Recuento|Total|Número de nodos restante|
|RunningNodeCount|Número de nodos de ejecución|Recuento|Total|Número de nodos en ejecución|
|LeavingPoolNodeCount|Dejando el número de nodos de grupo|Recuento|Total|Número de nodos salir de la agrupación|
|UnusableNodeCount|Número de nodos inutilizable|Recuento|Total|Número de nodos inutilizables|
|TaskStartEvent|Eventos de inicio de la tarea|Recuento|Total|Número total de tareas que han comenzado|
|TaskCompleteEvent|Eventos de tarea completado|Recuento|Total|Número total de tareas que ha completado|
|TaskFailEvent|Eventos de éxito de tareas|Recuento|Total|Número total de tareas finalizadas en un estado de error|
|PoolCreateEvent|Crear eventos del grupo|Recuento|Total|Número total de grupos que se han creado|
|PoolResizeStartEvent|Eventos de inicio de cambio de tamaño de grupo|Recuento|Total|Número total de cambia de grupo que haya iniciado|
|PoolResizeCompleteEvent|Eventos de grupo de cambio de tamaño completo|Recuento|Total|Número total de cambia de grupo que se haya completado|
|PoolDeleteStartEvent|Eventos de inicio de eliminar grupo|Recuento|Total|Número total de eliminaciones de grupo que se han iniciado|
|PoolDeleteCompleteEvent|Eventos de grupo de eliminación completa|Recuento|Total|Número total de eliminaciones de grupo que se haya completado|

## <a name="microsoftcacheredis"></a>Microsoft.Cache/redis

|Métrica|Nombre para mostrar métrica|Unidad|Tipo de agregación|Descripción|
|---|---|---|---|---|
|connectedclients|Clientes conectados|Recuento|Valor máximo||
|totalcommandsprocessed|Total de operaciones|Recuento|Total||
|cachehits|Visitas a la caché|Recuento|Total||
|cachemisses|Errores de caché|Recuento|Total||
|GetCommands|Obtiene|Recuento|Total||
|setcommands|Conjuntos de|Recuento|Total||
|evictedkeys|Teclas expulsadas|Recuento|Total||
|totalkeys|Total de claves|Recuento|Valor máximo||
|expiredkeys|Teclas expiradas|Recuento|Total||
|usedmemory|Memoria utilizada|Bytes|Valor máximo||
|usedmemoryRss|Memoria utilizada RSS|Bytes|Valor máximo||
|serverLoad|Carga del servidor|Porcentaje|Valor máximo||
|cacheWrite|Memoria caché de escritura|BytesPerSecond|Valor máximo||
|cacheRead|Lectura de caché|BytesPerSecond|Valor máximo||
|percentProcessorTime|CPU|Porcentaje|Valor máximo||
|connectedclients0|Clientes conectados (fragmentar 0)|Recuento|Valor máximo||
|totalcommandsprocessed0|Total de operaciones (fragmentar 0)|Recuento|Total||
|cachehits0|Visitas a la caché (fragmentar 0)|Recuento|Total||
|cachemisses0|Errores de caché (fragmentar 0)|Recuento|Total||
|getcommands0|Obtiene (fragmentar 0)|Recuento|Total||
|setcommands0|Conjuntos (fragmentar 0)|Recuento|Total||
|evictedkeys0|Teclas expulsadas (fragmentar 0)|Recuento|Total||
|totalkeys0|Total de claves (nodo 0)|Recuento|Valor máximo||
|expiredkeys0|Teclas expiradas (fragmentar 0)|Recuento|Total||
|usedmemory0|Memoria utilizada (fragmentar 0)|Bytes|Valor máximo||
|usedmemoryRss0|Memoria utilizada RSS (fragmentar 0)|Bytes|Valor máximo||
|serverLoad0|Carga del servidor (fragmentar 0)|Porcentaje|Valor máximo||
|cacheWrite0|Memoria caché de escritura (fragmentar 0)|BytesPerSecond|Valor máximo||
|cacheRead0|Lectura de caché (fragmentar 0)|BytesPerSecond|Valor máximo||
|percentProcessorTime0|CPU (fragmentar 0)|Porcentaje|Valor máximo||
|connectedclients1|Clientes conectados (fragmentar 1)|Recuento|Valor máximo||
|totalcommandsprocessed1|Total de operaciones (fragmentar 1)|Recuento|Total||
|cachehits1|Visitas a la caché (fragmentar 1)|Recuento|Total||
|cachemisses1|Errores de caché (fragmentar 1)|Recuento|Total||
|getcommands1|Obtiene (fragmentar 1)|Recuento|Total||
|setcommands1|Conjuntos (fragmentar 1)|Recuento|Total||
|evictedkeys1|Teclas expulsadas (fragmentar 1)|Recuento|Total||
|totalkeys1|Total de claves (nodo 1)|Recuento|Valor máximo||
|expiredkeys1|Teclas expiradas (fragmentar 1)|Recuento|Total||
|usedmemory1|Memoria utilizada (fragmentar 1)|Bytes|Valor máximo||
|usedmemoryRss1|Memoria utilizada RSS (fragmentar 1)|Bytes|Valor máximo||
|serverLoad1|Carga del servidor (fragmentar 1)|Porcentaje|Valor máximo||
|cacheWrite1|Memoria caché de escritura (fragmentar 1)|BytesPerSecond|Valor máximo||
|cacheRead1|Lectura de caché (fragmentar 1)|BytesPerSecond|Valor máximo||
|percentProcessorTime1|CPU (fragmentar 1)|Porcentaje|Valor máximo||
|connectedclients2|Clientes conectados (fragmentar 2)|Recuento|Valor máximo||
|totalcommandsprocessed2|Total de operaciones (fragmentar 2)|Recuento|Total||
|cachehits2|Visitas a la caché (fragmentar 2)|Recuento|Total||
|cachemisses2|Errores de caché (fragmentar 2)|Recuento|Total||
|getcommands2|Obtiene (fragmentar 2)|Recuento|Total||
|setcommands2|Conjuntos (fragmentar 2)|Recuento|Total||
|evictedkeys2|Teclas expulsadas (fragmentar 2)|Recuento|Total||
|totalkeys2|Total de claves (nodo 2)|Recuento|Valor máximo||
|expiredkeys2|Teclas expiradas (fragmentar 2)|Recuento|Total||
|usedmemory2|Memoria utilizada (fragmentar 2)|Bytes|Valor máximo||
|usedmemoryRss2|Memoria utilizada RSS (fragmentar 2)|Bytes|Valor máximo||
|serverLoad2|Carga del servidor (fragmentar 2)|Porcentaje|Valor máximo||
|cacheWrite2|Memoria caché de escritura (fragmentar 2)|BytesPerSecond|Valor máximo||
|cacheRead2|Lectura de caché (fragmentar 2)|BytesPerSecond|Valor máximo||
|percentProcessorTime2|CPU (fragmentar 2)|Porcentaje|Valor máximo||
|connectedclients3|Clientes conectados (fragmentar 3)|Recuento|Valor máximo||
|totalcommandsprocessed3|Total de operaciones (fragmentar 3)|Recuento|Total||
|cachehits3|Visitas a la caché (fragmentar 3)|Recuento|Total||
|cachemisses3|Errores de caché (fragmentar 3)|Recuento|Total||
|getcommands3|Obtiene (fragmentar 3)|Recuento|Total||
|setcommands3|Conjuntos (fragmentar 3)|Recuento|Total||
|evictedkeys3|Teclas expulsadas (fragmentar 3)|Recuento|Total||
|totalkeys3|Total de claves (nodo 3)|Recuento|Valor máximo||
|expiredkeys3|Teclas expiradas (fragmentar 3)|Recuento|Total||
|usedmemory3|Memoria utilizada (fragmentar 3)|Bytes|Valor máximo||
|usedmemoryRss3|Memoria utilizada RSS (fragmentar 3)|Bytes|Valor máximo||
|serverLoad3|Carga del servidor (fragmentar 3)|Porcentaje|Valor máximo||
|cacheWrite3|Memoria caché de escritura (fragmentar 3)|BytesPerSecond|Valor máximo||
|cacheRead3|Lectura de caché (fragmentar 3)|BytesPerSecond|Valor máximo||
|percentProcessorTime3|CPU (fragmentar 3)|Porcentaje|Valor máximo||
|connectedclients4|Clientes conectados (fragmentar 4)|Recuento|Valor máximo||
|totalcommandsprocessed4|Total de operaciones (fragmentar 4)|Recuento|Total||
|cachehits4|Visitas a la caché (fragmentar 4)|Recuento|Total||
|cachemisses4|Errores de caché (fragmentar 4)|Recuento|Total||
|getcommands4|Obtiene (fragmentar 4)|Recuento|Total||
|setcommands4|Conjuntos (fragmentar 4)|Recuento|Total||
|evictedkeys4|Teclas expulsadas (fragmentar 4)|Recuento|Total||
|totalkeys4|Total de claves (nodo 4)|Recuento|Valor máximo||
|expiredkeys4|Teclas expiradas (fragmentar 4)|Recuento|Total||
|usedmemory4|Memoria utilizada (fragmentar 4)|Bytes|Valor máximo||
|usedmemoryRss4|Memoria utilizada RSS (fragmentar 4)|Bytes|Valor máximo||
|serverLoad4|Carga del servidor (fragmentar 4)|Porcentaje|Valor máximo||
|cacheWrite4|Memoria caché de escritura (fragmentar 4)|BytesPerSecond|Valor máximo||
|cacheRead4|Lectura de caché (fragmentar 4)|BytesPerSecond|Valor máximo||
|percentProcessorTime4|CPU (fragmentar 4)|Porcentaje|Valor máximo||
|connectedclients5|Clientes conectados (fragmentar 5)|Recuento|Valor máximo||
|totalcommandsprocessed5|Total de operaciones (fragmentar 5)|Recuento|Total||
|cachehits5|Visitas a la caché (fragmentar 5)|Recuento|Total||
|cachemisses5|Errores de caché (fragmentar 5)|Recuento|Total||
|getcommands5|Obtiene (fragmentar 5)|Recuento|Total||
|setcommands5|Conjuntos (fragmentar 5)|Recuento|Total||
|evictedkeys5|Teclas expulsadas (fragmentar 5)|Recuento|Total||
|totalkeys5|Total de claves (nodo 5)|Recuento|Valor máximo||
|expiredkeys5|Teclas expiradas (fragmentar 5)|Recuento|Total||
|usedmemory5|Memoria utilizada (fragmentar 5)|Bytes|Valor máximo||
|usedmemoryRss5|Memoria utilizada RSS (fragmentar 5)|Bytes|Valor máximo||
|serverLoad5|Carga del servidor (fragmentar 5)|Porcentaje|Valor máximo||
|cacheWrite5|Memoria caché de escritura (fragmentar 5)|BytesPerSecond|Valor máximo||
|cacheRead5|Lectura de caché (fragmentar 5)|BytesPerSecond|Valor máximo||
|percentProcessorTime5|CPU (fragmentar 5)|Porcentaje|Valor máximo||
|connectedclients6|Clientes conectados (fragmentar 6)|Recuento|Valor máximo||
|totalcommandsprocessed6|Total de operaciones (fragmentar 6)|Recuento|Total||
|cachehits6|Visitas a la caché (fragmentar 6)|Recuento|Total||
|cachemisses6|Errores de caché (fragmentar 6)|Recuento|Total||
|getcommands6|Obtiene (fragmentar 6)|Recuento|Total||
|setcommands6|Conjuntos (fragmentar 6)|Recuento|Total||
|evictedkeys6|Teclas expulsadas (fragmentar 6)|Recuento|Total||
|totalkeys6|Total de claves (nodo 6)|Recuento|Valor máximo||
|expiredkeys6|Teclas expiradas (fragmentar 6)|Recuento|Total||
|usedmemory6|Memoria utilizada (fragmentar 6)|Bytes|Valor máximo||
|usedmemoryRss6|Memoria utilizada RSS (fragmentar 6)|Bytes|Valor máximo||
|serverLoad6|Carga del servidor (fragmentar 6)|Porcentaje|Valor máximo||
|cacheWrite6|Memoria caché de escritura (fragmentar 6)|BytesPerSecond|Valor máximo||
|cacheRead6|Lectura de caché (fragmentar 6)|BytesPerSecond|Valor máximo||
|percentProcessorTime6|CPU (fragmentar 6)|Porcentaje|Valor máximo||
|connectedclients7|Clientes conectados (fragmentar 7)|Recuento|Valor máximo||
|totalcommandsprocessed7|Total de operaciones (fragmentar 7)|Recuento|Total||
|cachehits7|Visitas a la caché (fragmentar 7)|Recuento|Total||
|cachemisses7|Errores de caché (fragmentar 7)|Recuento|Total||
|getcommands7|Obtiene (fragmentar 7)|Recuento|Total||
|setcommands7|Conjuntos (fragmentar 7)|Recuento|Total||
|evictedkeys7|Teclas expulsadas (fragmentar 7)|Recuento|Total||
|totalkeys7|Total de claves (nodo 7)|Recuento|Valor máximo||
|expiredkeys7|Teclas expiradas (fragmentar 7)|Recuento|Total||
|usedmemory7|Memoria utilizada (fragmentar 7)|Bytes|Valor máximo||
|usedmemoryRss7|Memoria utilizada RSS (fragmentar 7)|Bytes|Valor máximo||
|serverLoad7|Carga del servidor (fragmentar 7)|Porcentaje|Valor máximo||
|cacheWrite7|Memoria caché de escritura (fragmentar 7)|BytesPerSecond|Valor máximo||
|cacheRead7|Lectura de caché (fragmentar 7)|BytesPerSecond|Valor máximo||
|percentProcessorTime7|CPU (fragmentar 7)|Porcentaje|Valor máximo||
|connectedclients8|Clientes conectados (fragmentar 8)|Recuento|Valor máximo||
|totalcommandsprocessed8|Total de operaciones (fragmentar 8)|Recuento|Total||
|cachehits8|Visitas a la caché (fragmentar 8)|Recuento|Total||
|cachemisses8|Errores de caché (fragmentar 8)|Recuento|Total||
|getcommands8|Obtiene (fragmentar 8)|Recuento|Total||
|setcommands8|Conjuntos (fragmentar 8)|Recuento|Total||
|evictedkeys8|Teclas expulsadas (fragmentar 8)|Recuento|Total||
|totalkeys8|Total de claves (nodo 8)|Recuento|Valor máximo||
|expiredkeys8|Teclas expiradas (fragmentar 8)|Recuento|Total||
|usedmemory8|Memoria utilizada (fragmentar 8)|Bytes|Valor máximo||
|usedmemoryRss8|Memoria utilizada RSS (fragmentar 8)|Bytes|Valor máximo||
|serverLoad8|Carga del servidor (fragmentar 8)|Porcentaje|Valor máximo||
|cacheWrite8|Memoria caché de escritura (fragmentar 8)|BytesPerSecond|Valor máximo||
|cacheRead8|Lectura de caché (fragmentar 8)|BytesPerSecond|Valor máximo||
|percentProcessorTime8|CPU (fragmentar 8)|Porcentaje|Valor máximo||
|connectedclients9|Clientes conectados (fragmentar 9)|Recuento|Valor máximo||
|totalcommandsprocessed9|Total de operaciones (fragmentar 9)|Recuento|Total||
|cachehits9|Visitas a la caché (fragmentar 9)|Recuento|Total||
|cachemisses9|Errores de caché (fragmentar 9)|Recuento|Total||
|getcommands9|Obtiene (fragmentar 9)|Recuento|Total||
|setcommands9|Conjuntos (fragmentar 9)|Recuento|Total||
|evictedkeys9|Teclas expulsadas (fragmentar 9)|Recuento|Total||
|totalkeys9|Total de claves (nodo 9)|Recuento|Valor máximo||
|expiredkeys9|Teclas expiradas (fragmentar 9)|Recuento|Total||
|usedmemory9|Memoria utilizada (fragmentar 9)|Bytes|Valor máximo||
|usedmemoryRss9|Memoria utilizada RSS (fragmentar 9)|Bytes|Valor máximo||
|serverLoad9|Carga del servidor (fragmentar 9)|Porcentaje|Valor máximo||
|cacheWrite9|Memoria caché de escritura (fragmentar 9)|BytesPerSecond|Valor máximo||
|cacheRead9|Lectura de caché (fragmentar 9)|BytesPerSecond|Valor máximo||
|percentProcessorTime9|CPU (fragmentar 9)|Porcentaje|Valor máximo||

## <a name="microsoftcognitiveservicesaccounts"></a>Microsoft.CognitiveServices/accounts

|Métrica|Nombre para mostrar métrica|Unidad|Tipo de agregación|Descripción|
|---|---|---|---|---|
|NumberOfCalls|Número total de llamadas a la API|Recuento|Total|Número total de llamadas a la API.|
|NumberOfFailedCalls|Número total de llamadas a la API error|Recuento|Total|Número total de llamadas a la API errores.|

## <a name="microsoftcomputevirtualmachines"></a>Microsoft.Compute/virtualMachines

|Métrica|Nombre para mostrar métrica|Unidad|Tipo de agregación|Descripción|
|---|---|---|---|---|
|Porcentaje de CPU|Porcentaje de CPU|Porcentaje|Promedio|El porcentaje de unidades de cálculo asignado que las máquinas virtuales|
|En la red|En la red|Bytes|Total|El número de bytes recibidos en todas las interfaces de red por las máquinas virtuales (tráfico entrante)|
|Fuera de la red|Fuera de la red|Bytes|Total|El número de bytes en todas las interfaces de red por las máquinas virtuales (tráfico saliente)|
|Bytes de lectura de disco|Bytes de lectura de disco|Bytes|Total|Número total de bytes leídos desde el disco durante el período de supervisión|
|Bytes de escritura de disco|Bytes de escritura de disco|Bytes|Total|Número total de bytes escrito en el disco durante el período de supervisión|
|Disco operaciones de lectura/s|Disco operaciones de lectura/s|CountPerSecond|Promedio|Lectura IOPS|
|Operaciones de escritura de disco/seg.|Operaciones de escritura de disco/seg.|CountPerSecond|Promedio|IOPS de escritura de disco|

## <a name="microsoftcomputevirtualmachinescalesets"></a>Microsoft.Compute/virtualMachineScaleSets

|Métrica|Nombre para mostrar métrica|Unidad|Tipo de agregación|Descripción|
|---|---|---|---|---|
|Porcentaje de CPU|Porcentaje de CPU|Porcentaje|Promedio|El porcentaje de unidades de cálculo asignado que las máquinas virtuales|
|En la red|En la red|Bytes|Total|El número de bytes recibidos en todas las interfaces de red por las máquinas virtuales (tráfico entrante)|
|Fuera de la red|Fuera de la red|Bytes|Total|El número de bytes en todas las interfaces de red por las máquinas virtuales (tráfico saliente)|
|Bytes de lectura de disco|Bytes de lectura de disco|Bytes|Total|Número total de bytes leídos desde el disco durante el período de supervisión|
|Bytes de escritura de disco|Bytes de escritura de disco|Bytes|Total|Número total de bytes escrito en el disco durante el período de supervisión|
|Disco operaciones de lectura/s|Disco operaciones de lectura/s|CountPerSecond|Promedio|Lectura IOPS|
|Operaciones de escritura de disco/seg.|Operaciones de escritura de disco/seg.|CountPerSecond|Promedio|IOPS de escritura de disco|

## <a name="microsoftcomputevirtualmachinescalesetsvirtualmachines"></a>Microsoft.Compute/virtualMachineScaleSets/virtualMachines

|Métrica|Nombre para mostrar métrica|Unidad|Tipo de agregación|Descripción|
|---|---|---|---|---|
|Porcentaje de CPU|Porcentaje de CPU|Porcentaje|Promedio|El porcentaje de unidades de cálculo asignado que las máquinas virtuales|
|En la red|En la red|Bytes|Total|El número de bytes recibidos en todas las interfaces de red por las máquinas virtuales (tráfico entrante)|
|Fuera de la red|Fuera de la red|Bytes|Total|El número de bytes en todas las interfaces de red por las máquinas virtuales (tráfico saliente)|
|Bytes de lectura de disco|Bytes de lectura de disco|Bytes|Total|Número total de bytes leídos desde el disco durante el período de supervisión|
|Bytes de escritura de disco|Bytes de escritura de disco|Bytes|Total|Número total de bytes escrito en el disco durante el período de supervisión|
|Disco operaciones de lectura/s|Disco operaciones de lectura/s|CountPerSecond|Promedio|Lectura IOPS|
|Operaciones de escritura de disco/seg.|Operaciones de escritura de disco/seg.|CountPerSecond|Promedio|IOPS de escritura de disco|

## <a name="microsoftdevicesiothubs"></a>Microsoft.Devices/IotHubs

|Métrica|Nombre para mostrar métrica|Unidad|Tipo de agregación|Descripción|
|---|---|---|---|---|
|d2c.Telemetry.Ingress.allProtocol|Intentos de envío de mensaje de telemetría|Recuento|Total|Número de dispositivo en la nube mensajes de telemetría intentó enviar a su centro IoT|
|d2c.Telemetry.Ingress.Success|Mensajes de telemetría enviados|Recuento|Total|Número de dispositivo a los mensajes de telemetría de nube enviado a su centro IoT|
|c2d.Commands.egress.complete.Success|Comandos completados|Recuento|Total|Número de la nube a los comandos de dispositivo que se completó correctamente el dispositivo|
|c2d.Commands.egress.Abandon.Success|Comandos abandonados|Recuento|Total|Número de nube abandonadas por el dispositivo de comandos de dispositivo|
|c2d.Commands.egress.Reject.Success|Comandos rechazados|Recuento|Total|Número de la nube a los comandos de dispositivo rechazado por el dispositivo|
|devices.totalDevices|Dispositivos total|Recuento|Total|Número de dispositivos registrados para su centro IoT|
|devices.connectedDevices.allProtocol|Dispositivos conectados|Recuento|Total|Número de dispositivos conectados a su centro IoT|

## <a name="microsofteventhubnamespaces"></a>Microsoft.EventHub/namespaces

|Métrica|Nombre para mostrar métrica|Unidad|Tipo de agregación|Descripción|
|---|---|---|---|---|
|INREQS|Solicitudes entrantes|Recuento|Total|Rendimiento evento concentrador de mensaje entrante para un espacio de nombres|
|SUCCREQ|Solicitudes correctas|Recuento|Total|Número total de solicitudes correctamente un espacio de nombres|
|FAILREQ|Solicitudes de errores|Recuento|Total|Total de peticiones erróneas para un espacio de nombres|
|SVRBSY|Errores del servidor ocupado|Recuento|Total|Errores ocupado total del servidor para un espacio de nombres|
|INTERR|Error interno del servidor|Recuento|Total|Errores de servidor interno total para un espacio de nombres|
|MISCERR|Otros errores|Recuento|Total|Total de peticiones erróneas para un espacio de nombres|
|INMSGS|Mensajes entrantes|Recuento|Total|Total de los mensajes entrante para un espacio de nombres|
|OUTMSGS|Mensajes salientes|Recuento|Total|Total de los mensajes para un espacio de nombres salientes|
|EHINMBS|Bytes entrantes por segundo|BytesPerSecond|Total|Rendimiento evento concentrador de mensaje entrante para un espacio de nombres|
|EHOUTMBS|Bytes salientes por segundo|BytesPerSecond|Total|Total de los mensajes para un espacio de nombres salientes|
|EHABL|Archivar los mensajes de trabajo pendiente|Recuento|Total|Archivar mensajes en el trabajo pendiente para un espacio de nombres de concentrador de evento|
|EHAMSGS|Archivar los mensajes|Recuento|Total|Evento concentrador archiva mensajes en un espacio de nombres|
|EHAMBS|Rendimiento del mensaje de archivo|BytesPerSecond|Total|Rendimiento de mensaje archivado de concentrador de evento en un espacio de nombres|

## <a name="microsoftlogicworkflows"></a>Microsoft.Logic/workflows

|Métrica|Nombre para mostrar métrica|Unidad|Tipo de agregación|Descripción|
|---|---|---|---|---|
|RunsStarted|Se ejecuta iniciado|Recuento|Total|Número de flujo de trabajo se ejecuta Introducción.|
|RunsCompleted|Ejecuciones completadas|Recuento|Total|Número de flujo de trabajo se ejecuta completada.|
|RunsSucceeded|Se inicia correctamente|Recuento|Total|Número de flujo de trabajo se ejecuta correcta.|
|RunsFailed|Error de ejecución|Recuento|Total|Número de flujo de trabajo se ejecuta error.|
|RunsCancelled|Se ejecuta cancelado|Recuento|Total|Número de flujo de trabajo se ejecuta cancelada.|
|RunLatency|Ejecutar la latencia|Segundos|Promedio|Se ejecuta la latencia de flujo de trabajo completado.|
|RunSuccessLatency|Ejecutar la latencia de éxito|Segundos|Promedio|Se ejecuta la latencia de flujo de trabajo correcta.|
|RunThrottledEvents|Ejecutar eventos limitados|Recuento|Total|Número de acciones de flujo de trabajo o desencadenador había acelerado eventos.|
|RunFailurePercentage|Ejecutar el porcentaje de error|Porcentaje|Total|Porcentaje de flujo de trabajo se ejecuta error.|
|ActionsStarted|Acciones iniciados |Recuento|Total|Número de acciones de flujo de trabajo iniciado.|
|ActionsCompleted|Acciones completadas |Recuento|Total|Número de acciones de flujo de trabajo completado.|
|ActionsSucceeded|Acciones que se realizó correctamente |Recuento|Total|Número de acciones de flujo de trabajo se realizó correctamente.|
|ActionsFailed|Error de acciones|Recuento|Total|Número de acciones de flujo de trabajo de error.|
|ActionsSkipped|Acciones omitidos |Recuento|Total|Se omitió el número de acciones de flujo de trabajo.|
|ActionLatency|Latencia de acción |Segundos|Promedio|Latencia de acciones de flujo de trabajo completado.|
|ActionSuccessLatency|Latencia de éxito de acción |Segundos|Promedio|Latencia de acciones de flujo de trabajo correcta.|
|ActionThrottledEvents|Acción acelerado eventos|Recuento|Total|Número de acción de flujo de trabajo acelerado eventos...|
|TriggersStarted|Introducción de desencadenadores |Recuento|Total|Número de desencadenadores de flujo de trabajo se inicia.|
|TriggersCompleted|Desencadenadores completados |Recuento|Total|Número de desencadenadores de flujo de trabajo completado.|
|TriggersSucceeded|Desencadenadores se realizó correctamente |Recuento|Total|Número de desencadenadores de flujo de trabajo se realizó correctamente.|
|TriggersFailed|Error de desencadenadores |Recuento|Total|Error en el número de desencadenadores de flujo de trabajo.|
|TriggersSkipped|Desencadenadores omitidos|Recuento|Total|Se omitió el número de desencadenadores de flujo de trabajo.|
|TriggersFired|Desencadenadores activados |Recuento|Total|Número de desencadenadores de flujo de trabajo activa.|
|TriggerLatency|Latencia de desencadenador |Segundos|Promedio|Latencia de desencadenadores de flujo de trabajo completado.|
|TriggerFireLatency|Latencia de desencadenador Fire |Segundos|Promedio|Latencia de desencadenados desencadenadores de flujo de trabajo.|
|TriggerSuccessLatency|Latencia de éxito de desencadenador |Segundos|Promedio|Latencia de desencadenadores de flujo de trabajo se realizó correctamente.|
|TriggerThrottledEvents|Desencadenar eventos limitados|Recuento|Total|Número de desencadenador de flujo de trabajo había acelerado eventos.|
|BillableActionExecutions|Acción facturable ejecuciones|Recuento|Total|Número de ejecuciones de acción de flujo de trabajo obtener facturado.|
|BillableTriggerExecutions|Desencadenador facturable ejecuciones|Recuento|Total|Número de ejecuciones de desencadenador de flujo de trabajo obtener facturado.|
|TotalBillableExecutions|Total de ejecuciones facturable|Recuento|Total|Número de ejecuciones del flujo de trabajo obtener facturado.|

## <a name="microsoftnetworkapplicationgateways"></a>Microsoft.Network/applicationGateways

|Métrica|Nombre para mostrar métrica|Unidad|Tipo de agregación|Descripción|
|---|---|---|---|---|
|Rendimiento|Rendimiento|BytesPerSecond|Promedio||

## <a name="microsoftsearchsearchservices"></a>Microsoft.Search/searchServices

|Métrica|Nombre para mostrar métrica|Unidad|Tipo de agregación|Descripción|
|---|---|---|---|---|
|SearchLatency|Latencia de búsqueda|Segundos|Promedio|Latencia promedio de búsqueda para el servicio de búsqueda|
|SearchQueriesPerSecond|Consultas de búsqueda por segundo|CountPerSecond|Promedio|Consultas de búsqueda por segundo para el servicio de búsqueda|
|ThrottledSearchQueriesPercentage|Porcentaje de consultas de búsqueda limitado|Porcentaje|Promedio|Porcentaje de consultas de búsqueda que se han acelerado para el servicio de búsqueda|

## <a name="microsoftservicebusnamespaces"></a>Microsoft.ServiceBus/namespaces

|Métrica|Nombre para mostrar métrica|Unidad|Tipo de agregación|Descripción|
|---|---|---|---|---|
|CPUXNS|Uso de CPU por espacio de nombres|Porcentaje|Valor máximo|Métrica de uso de CPU del espacio de nombres de servicio bus premium|
|WSXNS|Uso de tamaño de la memoria por espacio de nombres|Porcentaje|Valor máximo|Métrica de uso de memoria de espacio de nombres de servicio bus premium|

## <a name="microsoftsqlserversdatabases"></a>Microsoft.Sql/servers/databases

|Métrica|Nombre para mostrar métrica|Unidad|Tipo de agregación|Descripción|
|---|---|---|---|---|
|cpu_percent|Porcentaje de CPU|Porcentaje|Promedio|Porcentaje de CPU|
|physical_data_read_percent|Porcentaje de IO de datos|Porcentaje|Promedio|Porcentaje de IO de datos|
|log_write_percent|Porcentaje de IO de registro|Porcentaje|Promedio|Porcentaje de IO de registro|
|dtu_consumption_percent|Porcentaje DTU|Porcentaje|Promedio|Porcentaje DTU|
|almacenamiento de información|Tamaño total de la base de datos|Bytes|Valor máximo|Tamaño total de la base de datos|
|connection_successful|Conexiones correctas|Recuento|Total|Conexiones correctas|
|connection_failed|Error conexiones|Recuento|Total|Error conexiones|
|blocked_by_firewall|Bloqueado por el Firewall|Recuento|Total|Bloqueado por el Firewall|
|bloqueo|Bloqueos|Recuento|Total|Bloqueos|
|storage_percent|Porcentaje de tamaño de la base de datos|Porcentaje|Valor máximo|Porcentaje de tamaño de la base de datos|
|xtp_storage_percent|Percent(Preview) de almacenamiento en memoria OLTP|Porcentaje|Promedio|Percent(Preview) de almacenamiento en memoria OLTP|
|workers_percent|Porcentaje de trabajadores|Porcentaje|Promedio|Porcentaje de trabajadores|
|sessions_percent|Porcentaje de sesiones|Porcentaje|Promedio|Porcentaje de sesiones|
|dtu_limit|Límite DTU|Recuento|Promedio|Límite DTU|
|dtu_used|DTU utilizado|Recuento|Promedio|DTU utilizado|
|service_level_objective|Objetivo de nivel de servicio de la base de datos|Recuento|Total|Objetivo de nivel de servicio de la base de datos|
|dwu_limit|límite de dwu|Recuento|Valor máximo|límite de dwu|
|dwu_consumption_percent|Porcentaje DWU|Porcentaje|Promedio|Porcentaje DWU|
|dwu_used|DWU utilizado|Recuento|Promedio|DWU utilizado|

## <a name="microsoftsqlserverselasticpools"></a>Microsoft.Sql/servers/elasticPools

|Métrica|Nombre para mostrar métrica|Unidad|Tipo de agregación|Descripción|
|---|---|---|---|---|
|cpu_percent|Porcentaje de CPU|Porcentaje|Promedio|Porcentaje de CPU|
|physical_data_read_percent|Porcentaje de IO de datos|Porcentaje|Promedio|Porcentaje de IO de datos|
|log_write_percent|Porcentaje de IO de registro|Porcentaje|Promedio|Porcentaje de IO de registro|
|dtu_consumption_percent|Porcentaje DTU|Porcentaje|Promedio|Porcentaje DTU|
|storage_percent|Porcentaje de almacenamiento|Porcentaje|Promedio|Porcentaje de almacenamiento|
|workers_percent|Porcentaje de trabajadores|Porcentaje|Promedio|Porcentaje de trabajadores|
|sessions_percent|Porcentaje de sesiones|Porcentaje|Promedio|Porcentaje de sesiones|
|eDTU_limit|límite de eDTU|Recuento|Promedio|límite de eDTU|
|storage_limit|Límite de almacenamiento|Bytes|Promedio|Límite de almacenamiento|
|eDTU_used|eDTU utilizado|Recuento|Promedio|eDTU utilizado|
|storage_used|Almacenamiento utilizado|Bytes|Promedio|Almacenamiento utilizado|

## <a name="microsoftstreamanalyticsstreamingjobs"></a>Microsoft.StreamAnalytics/streamingjobs

|Métrica|Nombre para mostrar métrica|Unidad|Tipo de agregación|Descripción|
|---|---|---|---|---|
|ResourceUtilization|% De SU uso|Porcentaje|Valor máximo|% De SU uso|
|InputEvents|Eventos de entrada|Recuento|Total|Eventos de entrada|
|InputEventBytes|Bytes de evento de entrada|Bytes|Total|Bytes de evento de entrada|
|LateInputEvents|Eventos de entrada de tiempo|Recuento|Total|Eventos de entrada de tiempo|
|OutputEvents|Eventos de salida|Recuento|Total|Eventos de salida|
|ConversionErrors|Errores de conversión de datos|Recuento|Total|Errores de conversión de datos|
|Errores|Errores de tiempo de ejecución|Recuento|Total|Errores de tiempo de ejecución|
|DroppedOrAdjustedEvents|Eventos en el orden correcto|Recuento|Total|Eventos en el orden correcto|
|AMLCalloutRequests|Solicitudes de función|Recuento|Total|Solicitudes de función|
|AMLCalloutFailedRequests|Solicitudes de la función Error|Recuento|Total|Solicitudes de la función Error|
|AMLCalloutInputEvents|Eventos de función|Recuento|Total|Eventos de función|

## <a name="microsoftwebserverfarms"></a>Microsoft.Web/serverfarms

|Métrica|Nombre para mostrar métrica|Unidad|Tipo de agregación|Descripción|
|---|---|---|---|---|
|CpuPercentage|Porcentaje de CPU|Porcentaje|Promedio|Porcentaje de CPU|
|MemoryPercentage|Porcentaje de memoria|Porcentaje|Promedio|Porcentaje de memoria|
|DiskQueueLength|Longitud de la cola de disco|Recuento|Total|Longitud de la cola de disco|
|HttpQueueLength|Longitud de la cola de http|Recuento|Total|Longitud de la cola de http|
|BytesReceived|Datos de|Bytes|Total|Datos de|
|BytesSent|Datos|Bytes|Total|Datos|

## <a name="microsoftwebsites"></a>Microsoft.Web/sites

|Métrica|Nombre para mostrar métrica|Unidad|Tipo de agregación|Descripción|
|---|---|---|---|---|
|CpuTime|Tiempo de la CPU|Segundos|Total|Tiempo de la CPU|
|Solicitudes|Solicitudes|Recuento|Total|Solicitudes|
|BytesReceived|Datos de|Bytes|Total|Datos de|
|BytesSent|Datos|Bytes|Total|Datos|
|Http2xx|2xx http|Recuento|Total|2xx http|
|Http3xx|3xx http|Recuento|Total|3xx http|
|Http401|HTTP 401|Recuento|Total|HTTP 401|
|Http403|HTTP 403|Recuento|Total|HTTP 403|
|Http404|HTTP 404|Recuento|Total|HTTP 404|
|Http406|HTTP 406|Recuento|Total|HTTP 406|
|Http4xx|4xx http|Recuento|Total|4xx http|
|Http5xx|Errores de servidor HTTP|Recuento|Total|Errores de servidor HTTP|
|MemoryWorkingSet|Trabajo de la memoria|Bytes|Total|Trabajo de la memoria|
|AverageMemoryWorkingSet|Memoria promedio espacio de trabajo|Bytes|Promedio|Memoria promedio espacio de trabajo|
|AverageResponseTime|Tiempo medio de respuesta|Segundos|Promedio|Tiempo medio de respuesta|

## <a name="microsoftwebsitesslots"></a>Microsoft.Web/sites/slots

|Métrica|Nombre para mostrar métrica|Unidad|Tipo de agregación|Descripción|
|---|---|---|---|---|
|CpuTime|Tiempo de la CPU|Segundos|Total|Tiempo de la CPU|
|Solicitudes|Solicitudes|Recuento|Total|Solicitudes|
|BytesReceived|Datos de|Bytes|Total|Datos de|
|BytesSent|Datos|Bytes|Total|Datos|
|Http2xx|2xx http|Recuento|Total|2xx http|
|Http3xx|3xx http|Recuento|Total|3xx http|
|Http401|HTTP 401|Recuento|Total|HTTP 401|
|Http403|HTTP 403|Recuento|Total|HTTP 403|
|Http404|HTTP 404|Recuento|Total|HTTP 404|
|Http406|HTTP 406|Recuento|Total|HTTP 406|
|Http4xx|4xx http|Recuento|Total|4xx http|
|Http5xx|Errores de servidor HTTP|Recuento|Total|Errores de servidor HTTP|
|MemoryWorkingSet|Trabajo de la memoria|Bytes|Total|Trabajo de la memoria|
|AverageMemoryWorkingSet|Memoria promedio espacio de trabajo|Bytes|Promedio|Memoria promedio espacio de trabajo|
|AverageResponseTime|Tiempo medio de respuesta|Segundos|Promedio|Tiempo medio de respuesta|

## <a name="next-steps"></a>Pasos siguientes

- [Obtenga información sobre métricas en Monitor de Azure](./monitoring-overview.md#monitoring-sources)
- [Crear alertas métrica](./insights-receive-alert-notifications.md)
- [Exportar métrica de almacenamiento, concentrador de eventos o el análisis de registro](./monitoring-overview-of-diagnostic-logs.md)
