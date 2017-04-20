<properties 
    pageTitle="Análisis - la herramienta de búsqueda eficaces de aplicación perspectivas | Microsoft Azure" 
    description="Descripción general de análisis de la herramienta de búsqueda de diagnóstico eficaz de perspectivas de aplicación. " 
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
    ms.date="07/26/2016" 
    ms.author="awills"/>


# <a name="analytics-in-application-insights"></a>Análisis de la información de la aplicación


[El análisis](app-insights-analytics.md) es la característica de búsqueda eficaz de [Perspectivas de aplicación](app-insights-overview.md). Estas páginas describen la lanquage de consulta de análisis. 

* **[Ver el vídeo de introducción](https://applicationanalytics-media.azureedge.net/home_page_video.mp4)**.
* **[Versión de prueba de análisis en nuestros datos simuladas](https://analytics.applicationinsights.io/demo)** si su aplicación no enviar datos de aplicación impresiones todavía.

## <a name="queries-in-analytics"></a>Consultas de análisis
 
Una consulta es una tabla de *origen* seguida de una serie de *operadores* separados por `|`. 

Por ejemplo, vamos a descubrir qué hora del día los ciudadanos de Hyderabad pruebe nuestra aplicación web. Y mientras se está allí, vamos a ver qué códigos de resultados se devuelven a sus solicitudes HTTP. 

```AIQL

    requests      // Table of events that log HTTP requests.
  	| where timestamp > ago(7d) and client_City == "Hyderabad"
  	| summarize clients = dcount(client_IP) 
      by tod_UTC=bin(timestamp % 1d, 1h), resultCode
  	| extend local_hour = (tod_UTC + 5h + 30min) % 24h + datetime("2001-01-01") 
```

Contamos con direcciones IP de cliente distinto, agrupar por la hora del día de los últimos 7 días. 

Vamos a mostrar los resultados con la presentación de gráfico de barras, elegir apilar los resultados de los códigos de respuesta diferente:

![Gráfico de barras, x y ejes y, a continuación, elija segmentación](./media/app-insights-analytics/020.png)

Parece nuestra aplicación es más popular en la hora del almuerzo y jardín tiempo en Hyderabad. (Y debemos investigue dichos 500 códigos).


También hay operaciones estadísticas eficaces:

![](./media/app-insights-analytics/025.png)


El idioma tiene muchas características interesantes:

* [Filtrar](app-insights-analytics-reference.md#where-operator) la telemetría aplicación bruto por todos los campos, incluidos sus propiedades personalizadas y métricas.
* [Unirse a](app-insights-analytics-reference.md#join-operator) varias tablas, las solicitudes de correlación con vistas de página, llamadas de dependencia, excepciones y registro realiza un seguimiento.
* Estadística eficaces [agregaciones](app-insights-analytics-reference.md#aggregations).
* Tan eficaz como SQL, pero mucho más fácil para las consultas complejas: en lugar de instrucciones de anidamiento, canalización los datos de una operación primaria a la siguiente.
* Visualizaciones inmediatos y eficaces.







## <a name="connect-to-your-application-insights-data"></a>Conectar a los datos de aplicación perspectivas


Abra el análisis desde el [módulo de información general](app-insights-dashboards.md) de la aplicación en perspectivas de aplicación: 

![Abra portal.azure.com, abra el recurso de información de la aplicación y haga clic en análisis.](./media/app-insights-analytics/001.png)


## <a name="limits"></a>Límites

En la actualidad, resultados de la consulta se limitan a solo durante una semana de los últimos datos.



[AZURE.INCLUDE [app-insights-analytics-footer](../../includes/app-insights-analytics-footer.md)]


## <a name="next-steps"></a>Pasos siguientes


* Le recomendamos que inicie el [recorrido de idioma](app-insights-analytics-tour.md).