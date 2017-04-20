<properties 
    pageTitle="Establecer alertas en aplicación perspectivas | Microsoft Azure" 
    description="Recibir una notificación sobre tiempos de respuesta, excepciones y otros rendimiento o cambios de uso de la aplicación web." 
    services="application-insights" 
    documentationCenter=""
    authors="alancameronwills" 
    manager="douge"/>

<tags 
    ms.service="application-insights" 
    ms.workload="tbd" 
    ms.tgt_pltfrm="ibiza" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/14/2016" 
    ms.author="awills"/>
 
# <a name="set-alerts-in-application-insights"></a>Establecer alertas en perspectivas de aplicación

[Perspectivas de aplicación de Visual Studio] [ start] puede alertar a los cambios en el rendimiento o uso de medidas en la aplicación web. 

Aplicación perspectivas supervisa su aplicación live en una [amplia variedad de plataformas] [ platforms] para ayudarle a diagnosticar problemas de rendimiento y comprender los patrones de uso.

Existen tres tipos de alertas:

* **Métricas alertas** saber cuando cualquier métrica cruza un valor de umbral algunos período - como tiempos de respuesta, recuentos de excepciones, uso de CPU o vistas de la página. 
* [**Pruebas Web** ] [ availability] saber si su sitio está disponible en internet o responde lentamente. [Más][availability].
* [**Diagnósticos proactivas**](app-insights-proactive-diagnostics.md) se configuran automáticamente para que le notifique sobre patrones de rendimiento inusual.

Se centrará en métricas alertas en este artículo.

## <a name="set-a-metric-alert"></a>Configurar una alerta de métrica

Abra el módulo de reglas de alertas y, a continuación, utilice el botón Agregar. 

![En el módulo de reglas de alertas, elija Agregar alerta. Establecer la aplicación como recurso para medir, proporcione un nombre para la alerta y elija una métrica.](./media/app-insights-alerts/01-set-metric.png)

* Establecer el recurso antes de las otras propiedades. **Elegir el recurso "(componentes)"** si desea establecer alertas en métricas de rendimiento o uso.
* El nombre que asigne a la alerta debe ser único dentro del grupo de recursos (no solo la aplicación).
* Tenga cuidado de tenga en cuenta las unidades en el que se le solicita que introduzca el valor de umbral.
* Si activa la casilla "Propietarios de correo electrónico...", se enviará alertas por correo electrónico a todos los usuarios que tengan acceso a este grupo de recursos. Para expandir este conjunto de personas, agregar a la [suscripción o grupo de recursos](app-insights-resources-roles-access-control.md) (que no sea el recurso).
* Si especifica "Correos electrónicos adicionales", las alertas se enviarán a aquellos individuos o grupos (o no activó la casilla "enviar por correo electrónico propietarios..."). 
* Establecer una [dirección webhook](../monitoring-and-diagnostics/insights-webhooks-alerts.md) si ha configurado una aplicación web que responde a las alertas. Se llamará cuando se activa la alerta (que se activa) y se ha resuelto. (Pero tenga en cuenta que en este momento, no se transfieren parámetros de consulta como propiedades de webhook).
* Puede deshabilitar o habilitar la alerta: vea los botones en la parte superior de la hoja.

*No veo el botón Agregar alerta.* 

- ¿Está utilizando una cuenta de la organización? Puede establecer alertas si tiene propietario o colaborador el acceso a este recurso de aplicación. Eche un vistazo al módulo de Control de acceso. [Obtenga más información sobre el control de acceso][roles].

> [AZURE.NOTE] En el módulo de alertas, verá que ya hay un conjunto de alerta de: [Diagnósticos proactiva](app-insights-proactive-failure-diagnostics.md). Se trata de una alerta automática que supervisa una métrica, solicitud error tasa determinada. A menos que decida deshabilitar la alerta proactiva, no es necesario establecer su propio alerta en la tasa de errores de la solicitud. 

## <a name="see-your-alerts"></a>Ver los avisos

Recibe un correo electrónico cuando un alerta cambia el estado entre inactivas y activas. 

En el módulo de reglas de alertas, se muestra el estado actual de cada alerta.

Hay un resumen de la actividad reciente de las alertas de lista desplegable:

![Desplegable de alertas](./media/app-insights-alerts/010-alert-drop.png)

Es el historial de cambios de estado en el registro de actividad:

![En el módulo de introducción, haga clic en configuración, registros de auditoría](./media/app-insights-alerts/09-alerts.png)



## <a name="how-alerts-work"></a>Funcionan de las alertas

* Una alerta tiene tres estados: "No activado nunca", "Activado" y "Resuelto". Activado significa que la condición especificada era true, cuando se ha evaluado última.

* Se genera una notificación cuando una alerta cambia de estado. (Si la condición de alerta ya estaba true cuando creó la alerta, podría no recibe una notificación hasta que la condición pasa false.)

* Cada notificación genera un correo electrónico si activó la casilla de correos electrónicos o proporcionan las direcciones de correo electrónico. También puede buscar en la lista desplegable de las notificaciones.

* Una alerta se evalúa cada vez que se llega una métrica, pero no en caso contrario.

* La evaluación agrega la métrica durante el período anterior y, a continuación, compara con el umbral para determinar el estado nuevo.

* El período que elija especifica el intervalo en los que se agregan métricas. No afecta a la frecuencia con la que se evalúa la alerta: que depende de la frecuencia de llegada de métricas.

* Si no hay datos llegan para una métrica determinada por algún tiempo, la separación tiene diferentes efectos de evaluación de alerta y en los gráficos en el Explorador de métrica. En el Explorador de métrica, si no se ve ningún dato durante más de intervalo de muestreo del gráfico, el gráfico muestra un valor de 0. Pero una alerta basada en la misma métrica no se volverán a evaluar y estado de la alerta permanece sin modificar. 

    Cuando finalmente llegan datos, el gráfico salta a un valor distinto de cero. Evalúa la alerta basada en los datos disponibles para el período especificado. Si solo está disponible en el período del punto de datos nueva, el agregado se basa solamente en punto de datos.

* Una alerta puede parpadean con frecuencia entre los Estados de alerta y correcto, incluso si establece un período de tiempo. Esto puede suceder si el valor de métrica se desplaza por el umbral. No hay ninguna inclinación en el umbral: la transición a alerta ocurre en el mismo valor que la transición a buen estado.



## <a name="what-are-good-alerts-to-set"></a>¿Qué son las alertas buenas establecer?

Depende de la aplicación. Inicialmente, es mejor no definir métricas demasiados. Dedique algún tiempo mostrando gráficos métricos mientras se ejecuta la aplicación, para hacerse una idea de cómo se comporta normalmente. Esto le permite buscar formas de mejorar el rendimiento. A continuación, configure alertas para indicarle a cuando vaya las mediciones fuera de la zona normal. 

Alertas populares incluyen:

* [Métricas del explorador][client], especialmente explorador **tiempos de carga**, es conveniente para las aplicaciones web. Si la página tiene una gran cantidad de secuencias de comandos, querrá tener en cuenta **las excepciones de explorador**. Para obtener estas métricas y alertas, tiene que configurar la [supervisión de la página web][client].
* **Tiempo de respuesta del servidor** para el lado del servidor de aplicaciones web. Así como configurar alertas, seguir controlando en esta métrica para ver si desproporcionada varía con tasas de solicitud alta: que pueden indicar que la aplicación se está quedando sin recursos. 
* **Excepciones de servidor** - verlos, tiene que realizar algunos [adicionales de instalación](app-insights-asp-net-exceptions.md).

No olvide que [los diagnósticos de tasa de errores proactiva](app-insights-proactive-failure-diagnostics.md) automáticamente supervisar la tasa a la que la aplicación se responde a las solicitudes con los códigos de error. 

## <a name="automation"></a>Automatización

* [Usar PowerShell para automatizar la configuración de alertas](app-insights-powershell-alerts.md)
* [Usar webhooks para automatizar la respuesta a las alertas](../monitoring-and-diagnostics/insights-webhooks-alerts.md)

## <a name="see-also"></a>Vea también

* [Pruebas de web de disponibilidad](app-insights-monitor-web-app-availability.md)
* [Automatizar la configuración de alertas](app-insights-powershell-alerts.md)
* [Diagnósticos proactivas](app-insights-proactive-diagnostics.md) 



<!--Link references-->

[availability]: app-insights-monitor-web-app-availability.md
[client]: app-insights-javascript.md
[platforms]: app-insights-platforms.md
[roles]: app-insights-resources-roles-access-control.md
[start]: app-insights-overview.md

 