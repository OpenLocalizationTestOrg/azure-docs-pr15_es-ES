<properties
    pageTitle="Información general sobre métricas en Microsoft Azure | Microsoft Azure"
    description="Información general sobre métricas y sus usos en Microsoft Azure"
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
    ms.date="09/26/2016"
    ms.author="ashwink"/>

# <a name="overview-of-metrics-in-microsoft-azure"></a>Información general sobre métricas en Microsoft Azure 

En este artículo se describen métricas en Microsoft Azure, sus ventajas y cómo empezar a trabajar con ellos.  

## <a name="what-are-metrics"></a>¿Qué son métricas?

Monitor de Azure le permite consumir telemetría para tener visibilidad en el rendimiento y la salud de las cargas de trabajo en Azure. El tipo de datos de telemetría Azure más importante son las métricas (también denominadas contadores) emitidas por Azure más recursos. Monitor de Azure proporciona varias maneras de configurar y usar estas métricas para supervisar y solucionar problemas.


## <a name="what-can-you-do-with-metrics"></a>¿Qué puede hacer con métricas?

Métricas son una valiosa fuente de telemetría y le permiten hacer lo siguiente:

- **Seguimiento del rendimiento** del recurso (por ejemplo, una máquina virtual, sitio Web o aplicación lógica) por trazar su métricas en un gráfico de portal y anclar el gráfico a un panel.
- **Recibir una notificación de un problema** afectar el rendimiento de los recursos cuando una métrica cruza un determinado umbral.
- **Configurar las medidas automatizadas**, como un recurso de ajuste automático o desencadenar un runbook cuando una métrica cruza un determinado umbral.
- **Realizar análisis avanzados** o informes de tendencias de rendimiento o el uso de los recursos.
- **Archivar** el historial de rendimiento o estado de sus propósitos **de auditoría y cumplimiento** de recursos.

##  <a name="metric-characteristics"></a>Características de métricas
Métricas tienen las características siguientes:

- Todas las métricas tienen **frecuencia de 1 minuto**. Recibe un valor de métrica cada minuto de los recursos que le da cerca de visibilidad en tiempo real en el estado y el estado del recurso.
- Métrica está **disponible - de-predefinidas sin necesidad de participar en** o la configuración de diagnósticos adicionales.
- Puede acceder a **30 días del historial** de cada métrica. Puede buscar rápidamente en las tendencias recientes y mensuales en el rendimiento o estado del recurso.

Puedes:

- Descubrir fácilmente, access y **Ver todas las métricas** a través del portal de Azure cuando seleccione el recurso y trazar en un gráfico. 
- Configurar una **regla de alerta que envía una notificación o realiza una acción automática** de métrica cuando la métrica fuera del umbral que ha establecido. Autoescala es una acción automatizada especial que le permite cambiar la escala de los recursos para satisfacer las solicitudes entrantes o cargar en su sitio web o calcular recursos. Puede configurar una regla de configuración Autoescala escalar fuera/en función de una métrica excedido un umbral.
- Métrica de **almacenamiento** para ya o usarlos para los informes de sin conexión. Puede redirigir la métrica de almacenamiento de blobs al configurar opciones de diagnóstico para el recurso.
- Métrica de **secuencia** a un concentrador de evento, lo que le permite después para el análisis de secuencia de Azure o aplicaciones personalizadas para el análisis de prácticamente en tiempo real. Puede hacer el uso de la configuración de diagnóstico.
- **Distribuir** todas las medidas para el análisis de registro de OMS para desbloquear el análisis de instantáneo, búsqueda y alertas personalizados en los datos de métricas de los recursos.
- **Consume** las mediciones a través de la nueva API de REST de Monitor de Azure.
- Métrica de **consulta** con los Cmdlets de PowerShell o la API de REST entre plataformas.

 ![Enrutamiento de métricas en el Monitor de Azure](./media/monitoring-overview-metrics/MetricsOverview0.png)

## <a name="access-metrics-via-portal"></a>Métrica de Access a través del portal
Esto es un tutorial rápido para crear un gráfico métrico a través del portal de Azure

### <a name="view-metrics-after-creating-a-resource"></a>Vista métricas después de crear un recurso
1. Portal de Azure abierto
2. Crear un nuevo servicio de aplicación - sitio Web.
3. Después de crear un sitio Web, vaya a la hoja de información general del sitio web.
4. Puede ver la nueva métrica como un mosaico 'Supervisión'. Puede editar el mosaico y seleccione más métricas

 ![Métricas en un recurso en el Monitor de Azure](./media/monitoring-overview-metrics/MetricsOverview1.png)    

### <a name="access-all-metrics-in-a-single-place"></a>Obtener acceso a todas las medidas en un único lugar
1. Abra el portal de Azure 
2. Vaya a la pestaña Monitor nuevo y seleccione la opción de métricas debajo de ella 
3. Puede seleccionar la suscripción, grupo de recursos y el nombre del recurso en la lista desplegable. 
4. Ahora puede ver la lista de métricas disponibles. 
5. Seleccione la métrica que le interesa y Piénsalo. 
6. Puede anclar a panel haciendo clic en el pin en la esquina superior derecha.

 ![Obtener acceso a todas las medidas en un único lugar en el Monitor de Azure](./media/monitoring-overview-metrics/MetricsOverview2.png) 


>[AZURE.NOTE] Puede acceder a métricas del nivel de host de máquinas virtuales (según el Administrador de recursos Azure) y conjuntos de escala de VM sin ninguna configuración adicional de diagnóstico. Estas nuevas métricas del nivel de host están disponibles para las instancias de Windows y Linux. Estas métricas no son confundirse con la métrica del nivel de sistema operativo invitado que tiene acceso a cuando se activan los diagnósticos de Azure en su máquinas virtuales o VMSS. Para obtener más información sobre la configuración de diagnósticos de Azure, consulte [¿Qué es Microsoft Azure diagnósticos](../azure-diagnostics.md).

## <a name="access-metrics-via-rest-api"></a>Métrica de acceso a través de la API de REST
Métrica de Azure se puede acceder a través de las API de Monitor de Azure. Hay dos API que le ayudarán a descubrir y tener acceso a métricas. Usar el: 

- [Definiciones de azure Monitor métrica API de REST](https://msdn.microsoft.com/library/mt743621.aspx) para tener acceso a la lista de métricas disponibles para un servicio.
- [API de REST de azure Monitor métricas](https://msdn.microsoft.com/library/mt743622.aspx) para tener acceso a los datos reales métricas

>[AZURE.NOTE] Este artículo tratan las métricas a través de la [nueva API para métricas](https://msdn.microsoft.com/library/dn931930.aspx) para recursos de Azure. La versión de la API para las nuevas definiciones métricas API es 2016-03-01 y la versión para métricas API es 2016-09-01. Pueden tener acceso a las definiciones de métrica heredadas y métricas con la versión de la api de 2014-04-01.

Para obtener un tutorial más detallado con la API de REST de Azure Monitor, vea [Tutorial de API de REST de Azure Monitor](monitoring-rest-api-walkthrough.md).

## <a name="export-options-for-metrics"></a>Opciones de exportación de métricas
Puede ir a la hoja de registros de diagnósticos en la pestaña Monitor y ver las opciones de exportación de métricas. Puede seleccionar métricas (y registros de diagnóstico) para enrutar al almacenamiento de blobs, Hubs de evento o a OMS registro análisis de casos de uso mencionados anteriormente en este artículo. 

 ![Opciones de exportación de métricas en Monitor de Azure](./media/monitoring-overview-metrics/MetricsOverview3.png)   

Se puede configurar a través de plantillas de administrador de recursos, [PowerShell](insights-powershell-samples.md), [CLI](insights-cli-samples.md) o [API de REST](https://msdn.microsoft.com/library/dn931943.aspx). 

## <a name="take-action-on-metrics"></a>Tomar medidas en métricas
Puede recibir notificaciones o tomar acciones automáticas en datos métricas. Puede configurar reglas de alerta o configuración de Autoescala para hacerlo.

### <a name="alert-rules"></a>Reglas de alertas
Puede configurar reglas de alerta de las estadísticas. Estas reglas de alerta pueden comprobar si una métrica ha cruzado un determinado umbral y le notifique por correo electrónico o aplique un webhook que puede usarse para ejecutar cualquier secuencia de comandos personalizada. También puede usar el webhook para configurar 3ª integraciones de productos.

 ![Métricas y las reglas de alertas en el Monitor de Azure](./media/monitoring-overview-metrics/MetricsOverview4.png)

### <a name="autoscale"></a>Autoescala
Algunos recursos Azure admiten varias instancias escalar o para controlar las cargas de trabajo. Autoescala se aplica a los servicios de aplicación (aplicaciones Web), conjuntos de escala de máquina Virtual (VMSS) y servicios de nube clásico. Puede configurar reglas de Autoescala escalar o cuando una determinada métrica que afectan a la carga de trabajo cruza un umbral que especifique. Para obtener más información, vea [información general sobre el ajuste automático](monitoring-overview-autoscale.md).

 ![Autoescala en el Monitor de Azure y mediciones](./media/monitoring-overview-metrics/MetricsOverview5.png)

## <a name="supported-services-and-metrics"></a>Servicios compatibles y mediciones
Monitor de Azure es una nueva infraestructura de métricas. Proporciona soporte técnico para los siguientes servicios de Azure en el portal de Azure y la nueva versión de la API de Monitor de Azure:

- Máquinas virtuales (según el Administrador de recursos Azure)
- Conjuntos de escala VM
- Proceso por lotes
- Espacio de nombres de concentrador de evento 
- Espacio de nombres de Bus de servicio (sólo SKU premium)
- SQL (versión 12)
- Grupo elástico SQL
- Sitios Web
- Conjuntos de servidores Web
- Aplicaciones de lógica
- IoT Hubs
- Redis caché
- Red: Las puertas de enlace de aplicación
- Búsqueda

Puede ver una lista detallada de todos los servicios compatibles y sus métricas en [Monitor de Azure métricas - métricas admitidas por tipo de recurso](monitoring-supported-metrics.md). 


## <a name="next-steps"></a>Pasos siguientes

Consulte los vínculos en este artículo. Además, obtenga información sobre:  

- acerca de [medidas comunes para el ajuste automático](insights-autoscale-common-metrics.md)
- cómo [crear reglas de alertas](insights-alerts-portal.md)




