<properties 
   pageTitle="En el análisis de registro de contadores de rendimiento de Windows y Linux | Microsoft Azure"
   description="Contadores de rendimiento recopilados por el análisis de registro para analizar el rendimiento de agentes de Windows y Linux.  Este artículo describe cómo configurar la colección de contadores de rendimiento para Windows y Linux agentes, detalles de la se almacenan en el repositorio OMS y cómo analizarlos en el portal OMS."
   services="log-analytics"
   documentationCenter=""
   authors="bwren"
   manager="jwhit"
   editor="tysonn" />
<tags 
   ms.service="log-analytics"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/27/2016"
   ms.author="bwren" />

# <a name="windows-and-linux-performance-data-sources-in-log-analytics"></a>Orígenes de datos de rendimiento de Windows y Linux en el análisis de registro 

Contadores de rendimiento en Windows y Linux proporcionan información sobre el rendimiento de componentes de hardware, sistemas operativos y aplicaciones.  Análisis de registro puede recopilar contadores de rendimiento en intervalos frecuentes para el análisis de casi en tiempo Real (NRT) además de informes y agregar datos de rendimiento para el análisis de términos más larga.

![Contadores de rendimiento](media/log-analytics-data-sources-performance-counters/overview.png)

## <a name="configuring-performance-counters"></a>Configurar contadores de rendimiento

Configurar contadores de rendimiento en el [menú datos de configuración de análisis de registro](log-analytics-data-sources.md#configuring-data-sources).

Primera vez que configure Windows o Linux rendimiento contadores para un área de trabajo OMS, tiene la opción de crear rápidamente varios contadores comunes.  Aparecen con una casilla de verificación junto a cada uno.  Asegúrese de que cualquier contador que desea crear inicialmente estén activadas y, a continuación, haga clic en **Agregar contadores de rendimiento seleccionado**.

![Configurar contadores de rendimiento de Windows](media/log-analytics-data-sources-performance-counters/configure-windows.png)

Siga este procedimiento para agregar un nuevo contador de rendimiento de Windows para recopilar.

1. Escriba el nombre del contador en el cuadro de texto en el formato del *objeto (instancia) \counter*.  Cuando empiece a escribir, se presentan con una lista de contadores comunes coincidente.  Puede seleccionar un contador de la lista o escriba uno de sus propios.  También puede devolver todas las instancias de un determinado contador especificando *objeto o contador*. 
2. Haga clic en **+** o presione **ENTRAR** para agregar el contador a la lista.
3. Cuando agrega un contador, usa el valor predeterminado de 10 segundos para el **Intervalo de muestra**.  Puede cambiar a un valor mayor de 1800 segundos (30 minutos) si desea reducir los requisitos de almacenamiento de los datos de rendimiento recopilados.
4. Cuando haya acabado de agregar contadores, haga clic en el botón **Guardar** en la parte superior de la pantalla para guardar la configuración.

![Configurar contadores de rendimiento de Linux](media/log-analytics-data-sources-performance-counters/configure-linux.png)

Siga este procedimiento para agregar un nuevo contador de rendimiento de Linux para recopilar.

1. De forma predeterminada, todos los cambios de configuración se insertan automáticamente a todos los agentes.  Para agentes Linux, se envía un archivo de configuración para el selector de datos de Fluentd.  Si desea modificar este archivo manualmente en cada agente Linux, a continuación, desactive la casilla *Aplicar debajo de configuración de mis equipos Linux*.
2. Escriba el nombre del contador en el cuadro de texto en el formato del *objeto (instancia) \counter*.  Cuando empiece a escribir, se presentan con una lista de contadores comunes coincidente.  Puede seleccionar un contador de la lista o escriba uno de sus propios.  
2. Haga clic en **+** o presione **ENTRAR** para agregar el contador a la lista de los demás contadores para el objeto.
3. Todos los contadores de un objeto utilizan el mismo **Intervalo de muestra**.  El valor predeterminado es de 10 segundos.  Cambia a un valor mayor de 1800 segundos (30 minutos) si desea reducir los requisitos de almacenamiento de los datos de rendimiento recopilados.
4. Cuando haya acabado de agregar contadores, haga clic en el botón **Guardar** en la parte superior de la pantalla para guardar la configuración.

## <a name="data-collection"></a>Recopilación de datos

Análisis de registro recopila todos los contadores de rendimiento especificado en el intervalo de ejemplo especificado en todos los agentes que tienen que instalar contador.  No se agregan los datos y los datos sin formato están disponibles en todas las vistas de búsqueda de registro para la duración especificada por su suscripción de OMS.


## <a name="performance-record-properties"></a>Propiedades del registro de rendimiento

Registros de rendimiento tienen un tipo de **rendimiento** y las propiedades de la tabla siguiente.

| (Propiedad) | Descripción |
|:--|:--|
| Equipo         | Equipo que se ha recopilado el evento. |
| CounterName      | Nombre del contador de rendimiento |
| RutaDeAccesoDeContador      | Ruta de acceso completa del contador en el formulario \\ \\ \<equipo >\\objeto (instancia)\\contador. |
| CounterValue     | Valor numérico del contador.  |
| InstanceName     | Nombre de la instancia de evento.  Vaciar si ninguna instancia. |
| ObjectName       | Nombre del objeto de rendimiento |
| SourceSystem  | Tipo de los datos recopilados desde el agente. <br> Conectar OpsManager: agente de Windows, ambos directa o SCOM <br> Linux: todos los agentes de Linux  <br> AzureStorage: diagnóstico de Azure |
| TimeGenerated       | Fecha y hora que muestrean los datos. |


## <a name="sizing-estimates"></a>Estimaciones de tamaño

 Una estimación aproximada para la colección de un contador determinado en intervalos de 10 segundos es entre 1 MB por día por cada instancia.  Puede calcular los requisitos de almacenamiento de un determinado contador con la siguiente fórmula.

    1 MB x (number of counters) x (number of agents) x (number of instances)

## <a name="log-searches-with-performance-records"></a>Búsquedas de registros con los registros de rendimiento

La tabla siguiente proporciona diferentes ejemplos de búsquedas de registro que recuperar registros de rendimiento.

| Consulta | Descripción |
|:--|:--|
| Tipo = rendimiento | Todos los datos de rendimiento |
| Tipo = rendimiento equipo = "Mi PC" | Todos los datos de rendimiento desde un equipo concreto |
| Tipo = rendimiento CounterName = "Longitud de cola de disco actual" | Todos los datos de rendimiento de un determinado contador |
| Tipo = rendimiento (ObjectName = procesador) CounterName = "% de tiempo de procesador" InstanceName = _Total & #124; medir Avg(Average) como AVGCPU equipo | Uso de CPU promedio en todos los equipos |
| Tipo = rendimiento (CounterName = "tiempo de procesador %") & #124;  medir max(Max) equipo | Uso de CPU máximo en todos los equipos |
| Tipo = rendimiento ObjectName = disco lógico CounterName = equipo "Actual cola de disco" = "NombreDelEquipo" & #124; medir Avg(Average) por InstanceName | Promedio de cola de disco actual a través de todas las instancias de un equipo |
| Tipo = rendimiento CounterName = "DiskTransfers/seg" & #124; medir percentile95(Average) equipo | 95 percentil de transferencias de disco/seg en todos los equipos |
| Tipo = rendimiento CounterName = "% de tiempo de procesador" InstanceName = "_Total" & #124; medir avg(CounterValue) por intervalo de equipo 1 hora | Cada hora promedio de uso de CPU en todos los equipos |
| Tipo = rendimiento equipo = "Mi PC" CounterName = % * InstanceName = _Total & #124; medir percentile70(CounterValue) por intervalo CounterName 1 hora | Percentil 70 por hora de cada contador % porcentual de un equipo en particular |
| Tipo = rendimiento CounterName = "% de tiempo de procesador" InstanceName = "_Total" (equipo = "Mi PC") & #124; medir min(CounterValue), avg(CounterValue), percentile75(CounterValue), max(CounterValue) por intervalo de equipo 1 hora | Cada hora promedio, mínimo, máximo y percentil 75 CPU para un equipo específico |

## <a name="viewing-performance-data"></a>Ver datos de rendimiento

Cuando se ejecuta una búsqueda de registro de datos de rendimiento, se muestra la vista del **registro** de forma predeterminada.  Para ver los datos en formato de gráfico, haga clic en **métricas**.  Para obtener una vista detallada de gráfica, haga clic en el **+** junto a un contador.  

![Vista de medidas contraída](media/log-analytics-data-sources-performance-counters/metricscollapsed.png)

Si ha seleccionado el intervalo de tiempo es 6 horas o menos, el gráfico se actualiza cada pocos segundos.  Los datos se muestran en el lado derecho del gráfico en azul claro.

![Vista de medidas expandida con datos actualizados](media/log-analytics-data-sources-performance-counters/metricsexpanded.png)

Agregar datos de rendimiento en una búsqueda de registros, consulte [visualización de OMS y agregación de métrica a petición](http://blogs.technet.microsoft.com/msoms/2016/02/26/on-demand-metric-aggregation-and-visualization-in-oms/).

## <a name="next-steps"></a>Pasos siguientes

- Obtenga información acerca de las [búsquedas de registro](log-analytics-log-searches.md) analizar los datos recopilados desde orígenes de datos y soluciones.  
- Exportar los datos recopilados a [Power BI](log-analytics-powerbi.md) para análisis y visualizaciones adicionales.