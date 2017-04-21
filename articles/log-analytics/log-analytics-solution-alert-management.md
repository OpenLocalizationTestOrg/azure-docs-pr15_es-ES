<properties
   pageTitle="Alerta de solución de administración en conjunto de aplicaciones de administración de operaciones (OMS) | Microsoft Azure"
   description="La solución de administración de alertas en el registro de análisis ayuda a analizar todas las alertas en su entorno.  Además de consolidar alertas generadas dentro de OMS, importan alertas de los grupos de administración de System Center Operations Manager (SCOM) conectada a análisis de registro."
   services="log-analytics"
   documentationCenter=""
   authors="bwren"
   manager="jwhit"
   editor="tysonn" />
<tags
   ms.service="operations-management-suite"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/06/2016"
   ms.author="bwren" />

# <a name="alert-management-solution-in-operations-management-suite-oms"></a>Solución de administración de alerta en conjunto de aplicaciones de administración de operaciones (OMS)

![Icono de alerta de administración](media/log-analytics-solution-alert-management/icon.png) La solución de administración de alertas le ayuda a analizar todas las alertas en su entorno.  Además de consolidar alertas generadas dentro de OMS, importan alertas de los grupos de administración de System Center Operations Manager (SCOM) conectada a análisis de registro.  En entornos con varios grupos de administración, la solución de administración de alertas le proporcionará una vista consolidada de alertas para todos los grupos de administración.

## <a name="prerequisites"></a>Requisitos previos

- Para importar alertas de SCOM, esta solución requiere una conexión entre el área de trabajo OMS y un grupo de administración de SCOM mediante el proceso descrito en [Conectar Operations Manager para el análisis de registro](log-analytics-om-agents.md).  


## <a name="configuration"></a>Configuración

Agregar la solución de administración de alertas al área de trabajo OMS mediante el proceso descrito en [Agregar soluciones](log-analytics-add-solutions.md).  No es necesaria realizar ninguna configuración adicional.

## <a name="management-packs"></a>Paquetes de administración

Si su grupo de administración de SCOM está conectado al área de trabajo OMS, los paquetes de administración siguientes se instalará en SCOM cuando agregue esta solución.  No hay ninguna configuración o el mantenimiento de estos paquetes de administración necesarios.  

- Administración de alertas de Asesor de Microsoft System Center (Microsoft.IntelligencePacks.AlertManagement)

Para obtener más información sobre cómo se actualizan los paquetes de administración de la solución, consulte [Conectar Operations Manager para el análisis de registro](log-analytics-om-agents.md).

## <a name="data-collection"></a>Recopilación de datos

### <a name="agents"></a>Agentes

La siguiente tabla describe las fuentes conectadas que son compatibles con esta solución.

| Origen conectada | Soporte técnico | Descripción |
|:--|:--|:--|
| [Agentes de Windows](log-analytics-windows-agents.md) | No | Agentes directos de Windows no genera alertas SCOM. |
| [Agentes Linux](log-analytics-linux-agents.md) | No | Directos agentes Linux no generan alertas SCOM. |
| [Grupo de administración de SCOM](log-analytics-om-agents.md) | Sí | Alertas que se generan en agentes SCOM se entregó al grupo de administración y, a continuación, reenviar análisis de registro.<br><br>No se requiere una conexión directa desde el agente SCOM para el análisis de registro. Datos de alerta se reenvían desde el grupo de administración en el repositorio OMS. |
| [Cuenta de almacenamiento de Azure](log-analytics-azure-storage.md) | No | Alertas SCOM no se almacenan en las cuentas de almacenamiento de Azure. |

### <a name="collection-frequency"></a>Frecuencia de la colección

Alertas generadas dentro de OMS están disponibles para la solución inmediatamente.  Datos de alerta se envían desde el grupo de administración de SCOM para el análisis de registro de cada 3 minutos.  

## <a name="using-the-solution"></a>Con la solución

Al agregar la solución de administración de alertas para el área de trabajo OMS, el mosaico de la **Administración de alertas** se agregará a su panel OMS.  Este mosaico muestra un recuento y una representación gráfica de la cantidad de alertas activas generadas dentro de las últimas 24 horas.  No puede cambiar este intervalo de tiempo.

![Mosaico de administración de alertas](media/log-analytics-solution-alert-management/tile.png)

Haga clic en el mosaico de la **Administración de alertas** para abrir el panel de **Administración de alertas** .  El panel incluye las columnas de la tabla siguiente.  Cada columna enumera las alertas de diez superiores por el número que coincida con los criterios de la columna para el ámbito especificado y el intervalo de tiempo.  Puede ejecutar una búsqueda de registro que proporciona toda la lista, haga clic en **Ver todo** en la parte inferior de la columna o haciendo clic en el encabezado de columna.

| Columna| Descripción |
|:--|:--|
| Alertas críticas | Todas las alertas con una gravedad crítica agrupado por nombre de la alerta.  Haga clic en un nombre de alerta para ejecutar una búsqueda de registro devolver todos los registros de esa alerta. |
| Alertas de advertencia | Todas las alertas con una gravedad de advertencia agrupado por nombre de la alerta.  Haga clic en un nombre de alerta para ejecutar una búsqueda de registro devolver todos los registros de esa alerta. |
| Active SCOM alertas | Todas las alertas SCOM con cualquier estado que no sea *cerrado* agrupados por origen que ha generado la alerta. |
| Todas las alertas activas | Todas las alertas con cualquier gravedad agrupados por nombre de la alerta. Solo incluye las alertas SCOM con cualquier estado que no sea *cerrado*.|

Si se desplaza a la derecha, el panel mostrará varias consultas comunes que puede hacer clic en para realizar una [búsqueda de registros](log-analytics-log-searches.md) de datos de alerta.

![Panel de administración de alertas](media/log-analytics-solution-alert-management/dashboard.png)

## <a name="scope-and-time-range"></a>Intervalo de ámbito y hora

De forma predeterminada, es el ámbito de las alertas analizados en la solución de administración de alertas de todos los grupos de administración conectados generados dentro de los últimos 7 días.  

![Ámbito de administración de alertas](media/log-analytics-solution-alert-management/scope.png)

- Para cambiar los grupos de administración incluidos en el análisis, haga clic en el **ámbito** en la parte superior del panel.  Puede seleccionar **Global** para todos los grupos de administración conectados o **Por el grupo de administración** seleccionar un único grupo de administración.

- Para cambiar el intervalo de tiempo de alertas, seleccione **datos basados en** la parte superior del panel.  Puede seleccionar alertas generadas dentro de los últimos 7 días, 1 día o 6 horas.  O bien, puede seleccionar **personalizada** y especificar un intervalo de fechas personalizado.

## <a name="log-analytics-records"></a>Análisis de registros

La solución de administración de alertas analiza todos los registros con un tipo de **alerta**.  También importar alertas de SCOM y crear un registro correspondiente para cada uno con un tipo de **alerta** y un SourceSystem de **OpsManager**.  Estos registros tienen las propiedades de la tabla siguiente.  

| (Propiedad) | Descripción |
|:--|:--|
| Tipo | *Alertas* |
| SourceSystem | *OpsManager* |
| AlertContext | Detalles del elemento de datos que provocó el aviso a generarse en formato XML. |
| AlertDescription | Descripción detallada de la alerta. |
| AlertId | GUID de la alerta. |
| NombreAlerta | Nombre de la alerta. |
| AlertPriority | Nivel de prioridad de la alerta. |
| AlertSeverity | Nivel de gravedad de la alerta. |
| AlertState | Estado de resolución más reciente de la alerta. |
| LastModifiedBy | Nombre del usuario que modificó por última vez la alerta. |
| ManagementGroupName | Nombre del grupo de administración donde se ha generado la alerta. |
| RepeatCount | Número de veces que se ha generado la misma alerta para el mismo supervisadas objeto desde el que se resuelve. |
| ResolvedBy | Nombre del usuario que resuelve la alerta. En blanco si no se ha resuelta la alerta. |
| SourceDisplayName | Nombre para mostrar del objeto supervisión que generó la alerta. |
| SourceFullName | Nombre completo del objeto supervisión que generó la alerta. |
| ID de ficha del | Id. de vale para la alerta si el entorno de SCOM se integra con un proceso para asignar vales para las alertas.  Vacío de ningún vale ID está asignado. |
| TimeGenerated | Fecha y hora en que se creó la alerta. |
| TimeLastModified | Fecha y hora en que se modificó por última vez la alerta. |
| TimeRaised | Fecha y hora en que se ha generado la alerta. |
| TimeResolved | Fecha y hora en que se ha resuelto la alerta. En blanco si no se ha resuelta la alerta. |

## <a name="sample-log-searches"></a>Búsquedas de registro de ejemplo

La tabla siguiente proporciona búsquedas de registros de ejemplo para los registros de alerta recopilados por esta solución.  

| Consulta | Descripción |
|:--|:--|
| Tipo = alerta SourceSystem = OpsManager AlertSeverity = error TimeRaised > ahora 24 horas | Alertas críticas elevadas durante las últimas 24 horas |
| Tipo = alerta AlertSeverity = advertencia TimeRaised > ahora 24 horas | Alertas de advertencia elevadas durante las últimas 24 horas  |
| Tipo = alerta SourceSystem = OpsManager AlertState! = TimeRaised cerrado > ahora 24 horas & #124; medir count() como recuento por SourceDisplayName | Orígenes de alertas activas elevadas durante las últimas 24 horas |
| Tipo = alerta SourceSystem = OpsManager AlertSeverity = error TimeRaised > ahora 24 horas AlertState! = cerrado | Alertas críticas elevadas durante las últimas 24 horas que aún están activas |
| Tipo = alerta SourceSystem = OpsManager TimeRaised > AlertState ahora 24 horas = cerrado | Alertas elevadas durante las últimas 24 horas que se ha cerrado |
| Tipo = alerta SourceSystem = OpsManager TimeRaised > ahora - 1 día & #124; medir count() como recuento por AlertSeverity | Alertas elevadas durante lo último 1 día agrupado por su gravedad |
| Tipo = alerta SourceSystem = OpsManager TimeRaised > ahora - 1 día & #124; Ordenar RepeatCount desc | Elevado durante lo último 1 día ordenado por su valor de número de repeticiones de alertas |

## <a name="next-steps"></a>Pasos siguientes

- Obtenga información acerca de [las alertas de análisis de registro](log-analytics-alerts.md) para obtener más información acerca de cómo generar alertas de análisis de registro.
