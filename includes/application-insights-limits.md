Existen algunos límites del número de eventos por aplicación y métricas (es decir, por clave instrumentación). 

Límites dependen del [nivel de precios](https://azure.microsoft.com/pricing/details/application-insights/) que elija.

**Recursos** | **Límite predeterminado** | **Límite máximo**
-------- | ------------- | -------------
Puntos de datos de la sesión<sup>1, 2</sup> por mes. | ilimitado | 
Puntos de datos total por mes para la solicitud, eventos, dependencia, seguimiento, excepción y vista de página | 5 millones | 50 millones de<sup>3</sup>
Tasa de datos de [registro y seguimiento](../articles/application-insights/app-insights-search-diagnostic-logs.md) | 200 dp/s | 500 dp/s
Tasa de datos de [excepción](../articles/application-insights/app-insights-asp-net-exceptions.md) | dp 50/s | dp 50/s
Tasa de total de los datos de la solicitud, eventos, dependencia de telemetría de vista de página | 200 dp/s | 500 dp/s
Retención de datos sin formato para [la búsqueda](../articles/application-insights/app-insights-diagnostic-search.md) y [análisis](../articles/application-insights/app-insights-analytics.md) | 7 días
Retención de datos agregados para [explorer métrica](../articles/application-insights/app-insights-metrics-explorer.md) | 90 días
Recuento de nombre de [propiedad](../articles/application-insights/app-insights-api-custom-events-metrics.md#properties) | 100 |
Longitud del nombre de propiedad | 150 | 
Longitud del valor de propiedad | 8192 | 
Longitud del mensaje de excepción y de seguimiento | 10000 |
Recuento de nombre de [métrica](../articles/application-insights/app-insights-api-custom-events-metrics.md#properties) | 100 |
Longitud del nombre métrica |  150 | 
[Pruebas de disponibilidad](../articles/application-insights/app-insights-monitor-web-app-availability.md) | 10 | 

<sup>1</sup> un punto de datos es un valor de métrica individual o un evento, con propiedades adjuntas y medidas.

<sup>2</sup> una sesión de punto de datos inicia al comienzo o al final de una sesión y registra la identidad de usuario.

<sup>3</sup> se puede comprar capacidad adicional más allá de 50 millones.
 
[Acerca de cuotas en aplicación perspectivas y precios](../articles/application-insights/app-insights-pricing.md)
