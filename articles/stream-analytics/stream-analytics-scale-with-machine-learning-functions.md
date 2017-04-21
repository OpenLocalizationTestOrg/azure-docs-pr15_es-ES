<properties
    pageTitle="Ajustar el tamaño de su trabajo de análisis de secuencia con funciones de aprendizaje del equipo de Azure | Microsoft Azure"
    description="Obtenga información sobre cómo ajustar correctamente los trabajos de análisis de secuencia (partición, SU cantidad y más) cuando se usan las funciones de aprendizaje del equipo de Azure."
    keywords=""
    documentationCenter=""
    services="stream-analytics"
    authors="jeffstokes72"
    manager="jhubbard"
    editor="cgronlun"
/>

<tags
    ms.service="stream-analytics"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="data-services"
    ms.date="09/26/2016"
    ms.author="jeffstok"
/>

# <a name="scale-your-stream-analytics-job-with-azure-machine-learning-functions"></a>Ajustar el tamaño de su trabajo de análisis de secuencia con funciones de aprendizaje del equipo de Azure

A menudo es bastante fácil de configurar un análisis de flujo de trabajo y ejecutar algunos datos de ejemplo a través de él. ¿Qué debemos hacer nos tenemos que ejecute el mismo trabajo con mayor volumen de datos? Requiere comprender cómo configurar el trabajo de análisis de secuencia de modo que ajustará la escala. En este documento, nos centraremos en los aspectos especiales de trabajos de análisis de secuencia con funciones de aprendizaje de ajuste de escala. Para obtener información sobre cómo ajustar el tamaño de los trabajos de análisis de secuencia en general vea el artículo [trabajos de escala](stream-analytics-scale-jobs.md).

## <a name="what-is-an-azure-machine-learning-function-in-stream-analytics"></a>¿Qué es una función de aprendizaje del equipo de Azure en el análisis de secuencia?

Una función de aprendizaje de equipo en el análisis de secuencia de puede usarse como una llamada de función normal en el lenguaje de consulta de análisis de la secuencia. Sin embargo, detrás de la escena, las llamadas de función son en realidad las solicitudes de servicio Web de Azure máquina aprendizaje. Servicios web de aprendizaje de equipo de soporte técnico "lotes" varias filas, que se denomina lote mínima, en la misma API llamada al servicio web, para mejorar el rendimiento general. Consulte los artículos siguientes para obtener más detalles; [Funciones de aprendizaje del equipo de Azure en el análisis de secuencia](https://blogs.technet.microsoft.com/machinelearning/2015/12/10/azure-ml-now-available-as-a-function-in-azure-stream-analytics/) y [Servicios Web de Azure máquina aprendizaje](machine-learning/machine-learning-consume-web-services.md#request-response-service-rrs).

## <a name="configure-a-stream-analytics-job-with-machine-learning-functions"></a>Configurar un trabajo de análisis de secuencia con funciones de aprendizaje de equipo

Al configurar una función de aprendizaje para el trabajo de análisis de secuencia, hay dos parámetros para tener en cuenta el tamaño del lote de las llamadas de función de aprendizaje de equipo y las unidades de transmisión (SUs) se aprovisione para el trabajo de análisis de secuencia. Para determinar los valores apropiados para estos, primero una decisión entre latencia y rendimiento, es decir, la latencia de la tarea de análisis de secuencia y el rendimiento de cada SU. SUs siempre pueden agregarse a una tarea para aumentar el rendimiento de una consulta de análisis de secuencia bien dividida, aunque SUs adicionales aumenta el costo de la ejecución de la tarea.

Por lo tanto, es importante determinar la *tolerancia* de latencia en una tarea de análisis de la secuencia. Latencia adicional de la ejecución de solicitudes de servicio de aprendizaje del equipo de Azure naturalmente aumentará con el tamaño del lote, que se compuesto la latencia de la tarea de análisis de la secuencia. Por otro lado, aumentar el tamaño del lote permite que el trabajo de análisis de secuencia procesar *más eventos con la *mismo número * de solicitudes de servicio web de aprendizaje del equipo. A menudo el aumento de latencia de servicio web de aprendizaje del equipo es inferior lineal al aumento del tamaño del lote por lo que es importante tener en cuenta el tamaño del lote más rentables para un servicio web de aprendizaje en cualquier situación determinada. El tamaño del lote predeterminado para las solicitudes de servicio web es 1000 y puede modificarse mediante la [API de REST de análisis de secuencia](https://msdn.microsoft.com/library/mt653706.aspx "API de REST de análisis de secuencia") o el [cliente de PowerShell para análisis de secuencia de](stream-analytics-monitor-and-manage-jobs-use-powershell.md "cliente de PowerShell para análisis de secuencia").

Una vez que se ha determinado un tamaño de lote, se puede determinar la cantidad de unidades de transmisión (SUs), en función del número de eventos que necesita la función proceso por segundo. Para obtener más información sobre las unidades de transmisión, consulte el artículo de la [escala de análisis de secuencia de trabajos](stream-analytics-scale-jobs.md#configuring-streaming-units).

En general, hay 20 conexiones simultáneas al servicio web de aprendizaje para todas 6 SUs, salvo que los trabajos SU 1 y 3 trabajos SU obtendrán 20 conexiones simultáneas también.  Por ejemplo, si la tasa de datos de entrada es 200.000 eventos por segundo y el tamaño del lote se queda en el valor predeterminado de 1000 la latencia de servicio web resultante con el lote de 1000 eventos mínima es de 200 milisegundos. Esto significa que todas las conexiones pueden hacer 5 solicitudes al servicio web de aprendizaje en un segundo. Con 20 conexiones, el trabajo de análisis de secuencia puede procesar 20.000 eventos en 200ms y, por tanto, 100000 en un segundo. Por lo tanto para procesar 200.000 eventos por segundo, el trabajo de análisis de secuencia necesita 40 conexiones simultáneas, que se incluye de 12 SUs. El diagrama siguiente muestra las solicitudes de la tarea de análisis de secuencia al extremo de servicio web de aprendizaje: todos SUs 6 tiene 20 conexiones simultáneas al servicio web de aprendizaje en max.

![Análisis de secuencia de escala con ejemplo de trabajo de máquina aprendizaje funciones 2] (./media/stream-analytics-scale-with-ml-functions/stream-analytics-scale-with-ml-functions-00.png "Análisis de secuencia de escala con ejemplo de trabajo de máquina aprendizaje funciones 2")

En general, ***B*** por tamaño del lote, ***L*** para la latencia de servicio web en el tamaño del lote B en milisegundos, el rendimiento de una tarea de análisis de secuencia con SUs ***N*** es:

![Ajustar el análisis de flujo con la fórmula de las funciones de aprendizaje] (./media/stream-analytics-scale-with-ml-functions/stream-analytics-scale-with-ml-functions-02.png "Ajustar el análisis de flujo con la fórmula de las funciones de aprendizaje")

Una consideración adicional puede ser el 'máximo de llamadas simultáneas' en el lado de servicio web de aprendizaje del equipo, es recomendable para establecer el valor máximo (200 actualmente).

Para obtener más información sobre este Revisar configuración el [artículo de la escala de los servicios Web de aprendizaje de equipo](../machine-learning/machine-learning-scaling-webservice.md).

## <a name="example--sentiment-analysis"></a>Ejemplo: análisis de opinión

En el ejemplo siguiente se incluye una tarea de análisis de secuencia con el análisis de opinión función aprendizaje del equipo, como se describe en el [tutorial de integración de secuencia de análisis de aprendizaje](stream-analytics-machine-learning-integration-tutorial.md).

La consulta es un simple totalmente divididas seguido de la función de **opinión** , tal como se muestra a continuación:

    WITH subquery AS (
        SELECT text, sentiment(text) as result from input
    )

    Select text, result.[Score]
    Into output
    From subquery

Considere el siguiente escenario; con un rendimiento de 10.000 tweets por segundo debe crearse una tarea de flujo análisis para realizar análisis de opinión de la tweets (eventos). ¿Con 1 SU, podría este trabajo de análisis de secuencia que pueda manejar el tráfico? Con el tamaño del lote predeterminado de 1000 el trabajo debería poder mantenerse a la entrada. Aún más la función de aprendizaje agregada debe generar más de un segundo de latencia, que es el general latencia predeterminado del servicio web de aprendizaje del equipo de análisis de opinión (con un tamaño de lote predeterminado de 1000). Latencia de llevar a cabo o **General** del trabajo de análisis de secuencia sería normalmente unos pocos segundos. Tener un aspecto más detallado en este trabajo de análisis de secuencia, *especialmente* la máquina llamadas de función de aprendizaje. Tiene el tamaño del lote como 1000, un rendimiento de 10.000 eventos tardará unos 10 solicitudes de servicio web. Incluso con 1 SU, hay suficiente conexiones simultáneas para acomodar el tráfico de entrada.

Pero ¿qué ocurre si aumenta la tasa de evento de entrada por x 100 y ahora debe procesar 1.000.000 tweets por segundo el trabajo de análisis de secuencia? Existen dos opciones:

1.  Aumentar el tamaño del lote, o
2.  Partición de la secuencia de entrada para procesar los eventos en paralelo

Con la primera opción, se aumentará la **latencia** de trabajo.

Con la segunda opción, SUs más necesarias para aprovisionar y, por tanto, generar más simultáneas solicitudes de servicio web de aprendizaje del equipo. Esto significa que el trabajo **costo** aumentará.


Se supone que la latencia del servicio web de aprendizaje del equipo de análisis de opinión de es de 200 milisegundos para lotes de 1000 eventos o por debajo, 250ms para lotes de eventos de 5.000, 300 ms para lotes de 10.000 eventos o 500 ms para lotes de eventos de 25.000.

1. Con la primera opción, (**no** el aprovisionamiento de SUs más), se podría aumentar el tamaño del lote a **25.000**. A su vez, esto le permitirá el trabajo procesar 1.000.000 eventos con 20 conexiones simultáneas al servicio web de aprendizaje del equipo (con una latencia de 500 ms por llamada). Por lo tanto la latencia adicional de la tarea de análisis de secuencia debido a las solicitudes de la función de opinión frente a las solicitudes de servicio web de aprendizaje de máquina se aumentar desde **200ms** a **500 ms**. Sin embargo, tenga en cuenta que por lotes tamaño **no puede** ser mayor indefinidamente como los servicios web de aprendizaje de equipo requiere el tamaño de carga de una solicitud de 4 MB o más pequeña tiempo de espera de solicitudes de servicio web 100 segundos de la operación.
2. Con la segunda opción, el tamaño del lote se queda en 1000, con latencia de servicio web 200ms, cada 20 conexiones simultáneas al servicio web podrán procesar 1000 eventos de *20* 5 = 100,000 por segundo. Por tanto para procesar 1.000.000 eventos por segundo, el trabajo necesario 60 SUs. En comparación con la primera opción, el trabajo de análisis de secuencia sería más web lote las solicitudes de servicio de generar un costo mayor.

A continuación, encontrará una tabla para el rendimiento de la tarea de análisis de secuencia para SUs diferentes y tamaños de lote (en número de eventos por segundo).

| tamaño del lote (latencia de m)  | 500 (200ms) | 1.000 (200ms) | 5.000 (250ms) | 10.000 (300 ms) | 25.000 (500 ms) |
|--------|-------------------------|---------------|---------------|----------------|----------------|
| **1 SU** | 2500 | 5.000 | 20.000 | 30.000 | 50.000 |
| **3 SUs** | 2500 | 5.000 | 20.000 | 30.000 | 50.000 |
| **6 SUs** | 2500 | 5.000 | 20.000 | 30.000 | 50.000 |
| **12 SUs** | 5.000 | 10.000 | 40.000 | 60.000 | 100,000 |
| **18 SUs** | 7.500 | 15.000 | 60.000 | 90.000 | 150.000 |
| **24 SUs** | 10.000 | 20.000 | 80.000 | 120.000 | 200.000 |
| **…** | … | … | … | … | … |
| **60 SUs** | 25.000 | 50.000 | 200.000 | 300.000 | 500.000 |

Por ahora, ya que debería tener a comprender mejor cómo funcionan las funciones de aprendizaje del equipo de análisis de la secuencia. Es probable que también comprender que los trabajos de análisis de secuencia "extraer" datos de orígenes de datos y cada "extracción" devuelve un lote de eventos para el trabajo de análisis de secuencia procesar. ¿Cómo este impacto del modelo de extracción el aprendizaje de máquina web solicitudes de servicio?

Normalmente, el tamaño del lote que se establece para las funciones de aprendizaje de máquina no exactamente estará dividir por el número de eventos devueltos por cada trabajo de análisis de secuencia "extracción". Cuando esto ocurre que llamará al servicio web de aprendizaje del equipo con lotes "parciales". Esto se hace para no sobrecarga trabajo adicional latencia en eventos de uso combinados de extracción para extracción.

## <a name="new-function-related-monitoring-metrics"></a>Nueva métrica supervisión relacionados con la función

En el área Monitor de un trabajo de análisis de secuencia, se han agregado tres métricas adicionales relacionados con la función. Son las solicitudes de la función, eventos de la función y las solicitudes de función de error, como se muestra en el siguiente gráfico.

![Ajustar el análisis de flujo con funciones métricas de aprendizaje] (./media/stream-analytics-scale-with-ml-functions/stream-analytics-scale-with-ml-functions-01.png "Ajustar el análisis de flujo con funciones métricas de aprendizaje")

The se define como sigue:

**Función solicitudes**: el número de solicitudes de la función.

**Función eventos**: el número de eventos en las solicitudes de función.

**Las solicitudes de función de error**: el número de solicitudes de la función error.

## <a name="key-takeaways"></a>Puntos clave  

Para resumir los puntos principales, para ajustar el tamaño de una tarea de análisis de secuencia con funciones de aprendizaje de equipo, deben tener en cuenta los siguientes elementos:

1.  El tipo de evento de entrada
2.  La latencia permitida para el trabajo de análisis de secuencia de ejecución (y, por tanto, el tamaño del lote de las solicitudes de servicio web de aprendizaje de equipo)
3.  La preparación SUs análisis de secuencia y el número de solicitudes de servicio web de aprendizaje del equipo (los relacionados con la función costes adicionales)

Una consulta de análisis de secuencia totalmente dividida se utilizó como ejemplo. Si se necesita una consulta más compleja el [foro de análisis de secuencia de Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics) es un buen recurso para obtener ayuda adicional desde el equipo de análisis de la secuencia.

## <a name="next-steps"></a>Pasos siguientes

Para obtener más información sobre el análisis de secuencia, consulte:

- [Empezar a usar el análisis de secuencia de Azure](stream-analytics-get-started.md)
- [Escala trabajos de análisis de secuencia de Azure](stream-analytics-scale-jobs.md)
- [Referencia del lenguaje de consulta de análisis de secuencia de Azure](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Referencia de la API de REST de administración de secuencia de Azure análisis](https://msdn.microsoft.com/library/azure/dn835031.aspx)
