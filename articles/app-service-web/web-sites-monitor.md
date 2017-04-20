<properties
    pageTitle="Supervisar aplicaciones de servicio de aplicaciones de Azure"
    description="Obtenga información sobre cómo supervisar las aplicaciones de servicio de la aplicación de Azure a través del Portal de Azure."
    services="app-service"
    documentationCenter=""
    authors="btardif"
    manager="wpickett"
    editor="mollybos"/>

<tags
    ms.service="app-service"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/07/2016"
    ms.author="byvinyal"/>

# <a name="how-to-monitor-apps-in-azure-app-service"></a>Cómo: supervisar las aplicaciones de servicio de aplicaciones de Azure

[Aplicación de servicio](http://go.microsoft.com/fwlink/?LinkId=529714) proporciona la funcionalidad de supervisión integrada en el [Portal de Azure](https://portal.azure.com).
Esto incluye la capacidad para revisar **las cuotas** y **métricas** para una aplicación, así como el plan de servicio de aplicaciones, configuración de **alertas** e incluso **escala** automáticamente en función de estas métricas.

[AZURE.INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]

## <a name="understanding-quotas-and-metrics"></a>Métricas y las cuotas de descripción

### <a name="quotas"></a>Cuotas

Aplicaciones que se encuentran en la aplicación de servicio están sujetos a determinados *límites* en los recursos que se pueden usar. Los límites están definidos por el **plan de servicios de aplicación** asociada con la aplicación.

Si la aplicación está alojada en un plan **libre** o **compartido** , los límites de los recursos que se puede usar la aplicación están definidos por **las cuotas**.

Si la aplicación se hospeda en **básica**, plan **estándar** o **Premium** y los límites de los recursos que se pueden usar establece el **tamaño** (pequeño, mediano, grande) y el **recuento de instancias** (1, 2, 3,...) del **plan de servicios de aplicación**.

Son **las cuotas** de **libre** o **compartido** de aplicaciones:

* **CPU(short)**
   * Cantidad de CPU permitida para esta aplicación en un intervalo de 3 minutos. Esta cuota se vuelve a establece cada 3 minutos.
* **CPU(Day)**
   * Importe total de CPU permitida para esta aplicación en un día. Esta cuota se vuelve a establece cada 24 horas en la medianoche UTC.
* **Memoria**
   * Cantidad total de memoria permitida para esta aplicación.
* **Ancho de banda**
   * Importe total de ancho de banda saliente permitido para esta aplicación en un día.
   Esta cuota se vuelve a establece cada 24 horas en la medianoche UTC.
* **Sistema de archivos**
   * Cantidad total de almacenamiento permitido.

La cuota sola aplicable a aplicaciones hospedadas en **básico**, **estándar** y planes **Premium** es de **sistema de archivos**.

Para obtener más información sobre las cuotas específicas, límites y características disponibles para los diferentes SKU de servicio de aplicación puede encontrarse aquí: [Límites de servicio de suscripción de Azure](../azure-subscription-service-limits.md#app-service-limits)

#### <a name="quota-enforcement"></a>Aplicación de cuotas

Si una aplicación en su uso supera la **CPU (nombre corta)**, **CPU (día)**, o cuota de **ancho de banda** , a continuación, la aplicación se detendrá hasta que vuelva a establece la cuota. Durante este período, se producen un **HTTP 403**todas las solicitudes entrantes.
![][http403]

Si se ha superado la cuota de **memoria** de aplicación, la aplicación será volver a empezar.

Si se ha superado la cuota del **sistema de archivos** , cualquier escritura no funcionará, esto incluye la escritura en los registros.

Las cuotas se pueden incrementar o eliminadas de la aplicación por actualizar su plan de servicios de aplicación.

### <a name="metrics"></a>Métricas

**Métricas** proporcionan información acerca de la aplicación o el comportamiento del plan de servicio de aplicación.

Para una **aplicación**, las medidas disponibles son:

* **Tiempo medio de respuesta**
   * El tiempo promedio necesario para que la aplicación atender las solicitudes en milisegundos.
* **Memoria promedio espacio de trabajo**
   * El promedio de memoria en MIB usados por la aplicación.
* **Tiempo de la CPU**
   * La cantidad de CPU en segundos consumida por la aplicación. Para obtener más información sobre esta métrica, consulte: [porcentaje de CPU de vs de tiempo de la CPU](#cpu-time-vs-cpu-percentage)
* **Datos de**
   * La cantidad de ancho de banda entrante consumido por la aplicación en MIB.
* **Datos**
   * La cantidad de ancho de banda saliente consumido por la aplicación en MIB.
* **2xx http**
   * Recuento de solicitudes de un código de estado http > = 200 pero < 300.
* **3xx http**
   * Recuento de solicitudes de un código de estado http > = 300 pero < 400.
* **HTTP 401**
   * Recuento de solicitudes de código de estado HTTP 401.
* **HTTP 403**
   * Recuento de solicitudes de código de estado HTTP 403.
* **HTTP 404**
   * Recuento de solicitudes de código de estado HTTP 404.
* **HTTP 406**
   * Recuento de solicitudes de código de estado HTTP 406.
* **4xx http**
   * Recuento de solicitudes de un código de estado http > = 400 pero < 500.
* **Errores de servidor HTTP**
   * Recuento de solicitudes de un código de estado http > = 500 pero < 600.
* **Trabajo de la memoria**
   * Cantidad actual de memoria utilizada por la aplicación en MIB.
* **Solicitudes**
   * Número total de solicitudes independientemente de su código de estado HTTP resultante.

Para un **plan de servicios de aplicación**, las medidas disponibles son:

>[AZURE.NOTE] Métricas del plan de servicio de aplicación solo están disponibles para los planes **básicos**, **estándar** y **Premium** SKU.

* **Porcentaje de CPU**
   * Media de la CPU usada en todas las instancias del plan.
* **Porcentaje de memoria**
   * La memoria promedio utilizada en todas las instancias del plan.
* **Datos de**
   * Promedio entrante ancho de banda utilizado en todas las instancias del plan.
* **Datos**
   * El promedio de ancho de banda utilizado en todas las instancias del plan de salida.
* **Longitud de la cola de disco**
   * El promedio de tanto lee y escribe solicitudes que están en cola de almacenamiento. Una longitud de cola de disco elevada es una indicación de una aplicación que podría ser ralentice debido a excesivo i/OS.
* **Longitud de la cola de http**
   * El promedio de solicitudes HTTP que contenía para permanecer en la cola antes de que se cumplan. Una longitud de cola de HTTP alta o creciente es un síntoma de un plan de carga intensa.

### <a name="cpu-time-vs-cpu-percentage"></a>Porcentaje de CPU tiempo vs CPU
<!-- To do: Fix Anchor (#CPU-time-vs.-CPU-percentage) -->

Hay 2 métricas que reflejen el uso de CPU. **Tiempo de CPU** y el **porcentaje de CPU**

**Tiempo de CPU** es útil para aplicaciones alojadas en planes **libre** o **compartido** como uno de sus cuotas se define en minutos de CPU usado por la aplicación.

**Porcentaje de CPU** por otro lado es útil para aplicaciones alojadas en planes **básico**, **estándar** y **premium** , puesto que se puedan escalar y esta métrica es una buena indicación de uso general en todas las instancias.

##<a name="metrics-granularity-and-retention-policy"></a>Detalle de métricas y la directiva de retención

Métricas para una aplicación y un plan de servicios de aplicación se registran y agregados el servicio con las directivas de retención y granularidades siguientes:

 * Métrica de detalle **minuto** se conserva **48** horas
 * Métrica de detalle de **horas** se conserva durante **30 días**
 * Métrica de detalle de **día** se conserva para **90 días**

## <a name="monitoring-quotas-and-metrics-in-the-azure-portal"></a>Supervisar las cuotas y mediciones en el Portal de Azure.

Puede revisar el estado de las diferentes **cuotas** y **métricas** que afectan a una aplicación en el [Portal de Azure](https://portal.azure.com).

![][quotas]
**Las cuotas** pueden encontrarse en Configuración >**cuotas**. La UX permite revisar: (1) el nombre de las cuotas, (2) el intervalo de restablecimiento, (3) el límite actual y valor (4) actual.

![][metrics]
**Métricas** pueden acceder directamente desde la hoja de recursos. También puede personalizar el gráfico: (1) **haga clic** en él y seleccione (2) **Modificar gráfico**.
Desde aquí puede cambiar el **intervalo de tiempo**de (3), (4), **tipo de gráfico**y (5) **métricas** que desea mostrar.  

Puede obtener más información sobre estas métricas: [métrica de servicio Monitor](../monitoring-and-diagnostics/insights-how-to-customize-monitoring.md).

## <a name="alerts-and-autoscale"></a>Autoescala y alertas
Métricas para un plan de aplicación o servicio de la aplicación se puede enlazado a alertas, para obtener más información, vea [recibir notificaciones de alerta](../monitoring-and-diagnostics/insights-receive-alert-notifications.md)

Aplicaciones de servicio de aplicación alojadas en basic, estándar o premium aplicación planes de servicio de soporte técnico **Autoescala**. Esto permite configurar reglas que supervisar las métricas del plan de servicio de aplicaciones y pueden aumentar o disminuir el recuento de instancia proporcionar recursos adicionales según sea necesario, o guardar dinero cuando la aplicación está aprovisionar exceso. Puede obtener más información sobre Autoescala aquí: [Cómo escala](../monitoring-and-diagnostics/insights-how-to-scale.md) y aquí [los procedimientos recomendados para el ajuste automático de Monitor de Azure](../monitoring-and-diagnostics/insights-autoscale-best-practices.md)

>[AZURE.NOTE] Si desea empezar a trabajar con el servicio de aplicación de Azure antes de suscribirse para una cuenta de Azure, vaya a [Probar el servicio de aplicaciones](http://go.microsoft.com/fwlink/?LinkId=523751), donde puede crear inmediatamente una aplicación web de starter corta duración en la aplicación de servicio. Sin tarjetas de crédito obligatoria; sin compromisos.

## <a name="whats-changed"></a>¿Qué ha cambiado
* Encontrará una guía para el cambio de sitios Web a la aplicación de servicio: [servicio de aplicación de Azure y su influencia sobre los servicios de Azure existentes](http://go.microsoft.com/fwlink/?LinkId=529714)

[fzilla]:http://go.microsoft.com/fwlink/?LinkId=247914
[vmsizes]:http://go.microsoft.com/fwlink/?LinkID=309169



<!-- Images. -->
[http403]: ./media/web-sites-monitor/http403.png
[quotas]: ./media/web-sites-monitor/quotas.png
[metrics]: ./media/web-sites-monitor/metrics.png
