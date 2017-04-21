<properties
    pageTitle="Ajustar el tamaño de los trabajos de análisis de secuencia para aumentar el rendimiento | Microsoft Azure"
    description="Obtenga información sobre cómo ajustar trabajos de análisis de secuencia a configurar particiones entradas, ajuste la definición de la consulta y establecer la transmisión de unidades de trabajo."
    keywords="datos de transmisión, transmisión de procesamiento de datos, ajustar el análisis"
    services="stream-analytics"
    documentationCenter=""
    authors="jeffstokes72"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="stream-analytics"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="data-services"
    ms.date="09/26/2016"
    ms.author="jeffstok"/>

# <a name="scale-azure-stream-analytics-jobs-to-increase-stream-data-processing-throughput"></a>Ajustar el tamaño de los trabajos de análisis de secuencia de Azure para mejorar el rendimiento del procesamiento de datos de secuencia

Obtenga información sobre cómo ajustar trabajos de análisis y calcular la *transmisión de unidades* para el análisis de secuencia, cómo escalar trabajos de análisis de secuencia configurar particiones entradas, ajuste la definición de consulta de análisis y estableciendo la transmisión de unidades de trabajo. 

## <a name="what-are-the-parts-of-a-stream-analytics-job"></a>¿Cuáles son las partes de una tarea de análisis de secuencia?
Una definición de análisis de flujo de trabajo incluye entradas, una consulta y salida. Las entradas son desde donde el trabajo lee el flujo de datos, la consulta se utiliza para transformar la secuencia de entrada de datos y el resultado es donde el trabajo envía los resultados de la tarea.  

Un trabajo requiere al menos un origen de entrada de transmisión de datos. El origen de entrada de flujo de datos se puede almacenar en un concentrador de evento de Bus de servicio de Azure o en el almacenamiento de blobs de Windows Azure. Para obtener más información, vea [Introducción a análisis de secuencia de Azure](stream-analytics-introduction.md) y [empezar a usar el análisis de secuencia de Azure](stream-analytics-get-started.md).

## <a name="configuring-streaming-units"></a>Configurar transmisiones de unidades
Unidades de transmisión por secuencias (SUs) representan los recursos y potencia necesarios para ejecutar una tarea de análisis de secuencia de Azure. SUs proporcionan una manera para describir el evento relativo basada en una medida combinada de CPU, memoria, la capacidad de procesamiento y leen y escribir tasas. Cada unidad streaming corresponde a aproximadamente 1MB por segundo. 

Elegir SUs cuántas se requieren para un trabajo determinado depende de la configuración de partición para las entradas y la consulta definido para el trabajo. Puede seleccionar hasta su cuota de transmisión de unidades para un trabajo mediante el Portal de clásico de Azure. Cada suscripción Azure predeterminada tiene una cuota de hasta 50 unidades streaming todos los trabajos de análisis de una región específica. Para aumentar la transmisión de unidades para las suscripciones, póngase en contacto con [Soporte técnico de Microsoft](http://support.microsoft.com).

El número de transmisiones unidades que puede utilizar un trabajo depende de la configuración de partición para las entradas y la consulta definido para el trabajo. Observe también que se debe utilizar un valor válido para las unidades de la secuencia. Los valores válidos comienzan en 1, 3, 6 y después hacia arriba en incrementos de 6, como se muestra a continuación.

![Escala de unidades de secuencia de análisis de secuencia de Azure][img.stream.analytics.streaming.units.scale]

En este artículo le mostrará cómo calcular y ajustar la consulta para aumentar el rendimiento para las tareas de análisis.

## <a name="embarrassingly-parallel-job"></a>Trabajo una paralela
El trabajo una paralelo es el escenario más scalable que tenemos en análisis de secuencia de Azure. Una partición de entrada de una instancia de la consulta se conecta a una partición de los resultados. Alcanzar este paralelismo requiere algunas cosas:

1.  Si la lógica de consulta depende de la misma clave procesada en la misma instancia de consulta, a continuación, debe asegurarse de que los eventos para ir a la misma partición de la entrada. En el caso de los centros de evento, esto significa que los datos del evento debe tener **PartitionKey** establecer o puede usar remitentes divididas. Para blobs, esto significa que los eventos se envían a la misma carpeta de partición. Si la lógica de la consulta no requiere la misma clave procese la misma instancia de consulta, puede omitir este requisito. Un ejemplo de esto sería una consulta de proyecto/Seleccione/Filtro simple.  
2.  Una vez que los datos se colocan como debe estar en el lado de entrada, es necesario para asegurarse de que la consulta se divide. Esto requiere usar **Partición por** en todos los pasos. Se permiten varios pasos, pero todos ellos deben dividirse por la misma clave. Otra cosa nota es que, actualmente, la clave de partición debe establecerse a **PartitionId** tiene un trabajo totalmente paralelo.  
3.  Solo evento Hubs y Blob admiten actualmente salida dividida. Para la salida de evento Hubs, debe configurar el campo **PartitionKey** para ser **PartitionId**. Para blobs, no tiene que hacer nada.  
4.  Otra cosa que tenga en cuenta el número de particiones entradas debe coincidir con el número de particiones de salida. Salida BLOB actualmente no admite particiones, pero esto está bien porque heredarán el esquema de particiones de la consulta en sentido ascendente. Ejemplos de valores de partición que permiten un trabajo totalmente paralelo:  
    1.  8 Hubs evento entrada particiones y particiones de salida de 8 Hubs de evento
    2.  8 Hubs de evento de entrada particiones y salida Blob  
    3.  8 particiones de blobs entradas y salida Blob  
    4.  Particiones de entrada de blobs de Windows 8 y 8 Hubs evento particiones de salida  

Estas son algunas situaciones de ejemplo que son una paralelas.

### <a name="simple-query"></a>Consulta simple
Entrada-Hubs de evento con 8 particiones salida: evento concentrador con 8 particiones

**Consulta:**

    SELECT TollBoothId
    FROM Input1 Partition By PartitionId
    WHERE TollBoothId > 100

Esta consulta es un filtro simple y por tanto, no es necesario que preocuparse por la entrada de enviar a evento Hubs de partición. Observe que la consulta tiene **Partición por** de **PartitionId**, por lo que nos satisfacer requisitos #2 de arriba. Para la salida, es necesario configurar la salida de evento Hubs en el trabajo para que el campo **PartitionKey** establecido en **PartitionId**. Una última comprobación, particiones entradas == particiones de salida. Esta topología es una paralela.

### <a name="query-with-grouping-key"></a>Consulta con clave de agrupación
Entrada-Hubs de evento con 8 particiones salida – Blob

**Consulta:**

    SELECT COUNT(*) AS Count, TollBoothId
    FROM Input1 Partition By PartitionId
    GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId

Esta consulta tiene una clave de agrupación y por lo tanto, se necesita la misma clave procese la misma instancia de consulta. Esto significa que necesitamos enviar nuestros eventos a eventos Hubs de forma dividida. ¿Clave que nos importa sobre? **PartitionId** es un concepto de lógica de trabajo, la clave real que nos importa es **TollBoothId**. Esto significa que se debe establecer el **PartitionKey** de los datos del evento de enviar a evento Hubs sea la **TollBoothId** del evento. La consulta tiene **Partición por** de **PartitionId**, por lo que estamos buenas allí. Para la salida, ya que es blobs, no debemos preocuparme acerca de cómo configurar **PartitionKey**. Requisito #4, de nuevo, esto es blobs, para que no tenga que preocuparse por ello. Esta topología es una paralela.

### <a name="multi-step-query-with-grouping-key"></a>Consulta de paso múltiple con clave de agrupación ###
Entrada-concentrador de evento con 8 particiones salida: evento concentrador con 8 particiones

**Consulta:**

    WITH Step1 AS (
    SELECT COUNT(*) AS Count, TollBoothId, PartitionId
    FROM Input1 Partition By PartitionId
    GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId
    )
    
    SELECT SUM(Count) AS Count, TollBoothId
    FROM Step1 Partition By PartitionId
    GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId

Esta consulta tiene una clave de agrupación y por lo tanto, se necesita la misma clave procese la misma instancia de consulta. Podemos utilizar la misma estrategia como la consulta anterior. La consulta tiene varios pasos. ¿Tiene cada paso **Por partición** de **PartitionId**? Sí, por lo que es conveniente. Para la salida, es necesario establecer el **PartitionKey** **ParticiónID** como descrito anteriormente y podemos ver también tiene el mismo número de particiones como entrada. Esta topología es una paralela.


## <a name="example-scenarios-that-are-not-embarrassingly-parallel"></a>Escenarios de ejemplo que no son una paralelas

### <a name="mismatched-partition-count"></a>Recuento de partición no coincidentes ###
Entrada-Hubs de evento con 8 particiones salida: evento concentrador con 32 particiones

No importa cuál la consulta es en este caso porque la entrada partición recuento! = número de partición de salida.

### <a name="not-using-event-hubs-or-blobs-as-output"></a>No usa evento Hubs o BLOB como resultado
Entrada-Hubs de evento con 8 particiones salida: PowerBI

Resultado de PowerBI actualmente no admite particiones.

### <a name="multi-step-query-with-different-partition-by-values"></a>Consulta de paso de múltiples con distintos partición por valores
Entrada-concentrador de evento con 8 particiones salida: evento concentrador con 8 particiones

**Consulta:**

    WITH Step1 AS (
    SELECT COUNT(*) AS Count, TollBoothId, PartitionId
    FROM Input1 Partition By PartitionId
    GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId
    )
    
    SELECT SUM(Count) AS Count, TollBoothId
    FROM Step1 Partition By TollBoothId
    GROUP BY TumblingWindow(minute, 3), TollBoothId
    
Como puede ver, el segundo paso utiliza **TollBoothId** como la clave de partición. Esto no es lo mismo que el primer paso y, por tanto, necesitarán poder hacer un orden aleatorio. 

Estos son algunos ejemplos y counterexamples de trabajos de análisis de secuencia que podrán lograr una topología una paralela y con ella la posibilidad de escala máxima. Para las tareas que no se ajusta a uno de estos perfiles, se realizará futuras actualizaciones que detalla cómo al máximo escalar algunos de los otros escenarios de análisis de secuencia canónicos.

Para realizar ahora uso de las directrices generales siguientes:

## <a name="calculate-the-maximum-streaming-units-of-a-job"></a>Calcular el máximo de unidades de una tarea de flujo
El número total de unidades streaming que puede ser usada por un trabajo de análisis de secuencia depende del número de pasos de la consulta definida por el trabajo y el número de particiones para cada paso.

### <a name="steps-in-a-query"></a>Pasos en una consulta
Una consulta puede tener uno o varios pasos. Cada paso es una subconsulta definida mediante la palabra clave **WITH** . La consulta sólo que está fuera de la palabra clave **WITH** también se cuenta como un paso, por ejemplo, la instrucción **SELECT** en la siguiente consulta:

    WITH Step1 AS (
        SELECT COUNT(*) AS Count, TollBoothId
        FROM Input1 Partition By PartitionId
        GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId
    )

    SELECT SUM(Count) AS Count, TollBoothId
    FROM Step1
    GROUP BY TumblingWindow(minute,3), TollBoothId

La consulta anterior tiene dos pasos.

> [AZURE.NOTE] Esta consulta de ejemplo se explicará más adelante en el artículo.

### <a name="partition-a-step"></a>Dividir un paso

Partición un paso requiere las siguientes condiciones:

- Debe tener particiones el origen de entrada. Para obtener más información, vea la [Guía de programación de Hubs de evento](../event-hubs/event-hubs-programming-guide.md).
- La instrucción **SELECT** de la consulta debe leer desde un origen de entrada con particiones.
- La consulta en el paso debe tener la **Partición por** palabra clave

Cuando se divide una consulta, los eventos de entrada se mostrará partición independiente procesado y se agregan en grupos y se generan eventos de resultados para cada uno de los grupos. Si un agregado combinado es conveniente, debe crear un segundo paso sin particiones al agregado.

### <a name="calculate-the-max-streaming-units-for-a-job"></a>Calcular el límite máximo de unidades para una tarea de flujo

Juntos todos los pasos sin particiones pueden escalar hasta seis unidades transmisión para una tarea de análisis de la secuencia. Para agregar adicionales de transmisión de unidades, un paso debe crearse. Cada partición puede tener seis unidades streaming.

<table border="1">
<tr><th>Consulta de un trabajo</th><th>Max transmisión de unidades para el trabajo</th></td>

<tr><td>
<ul>
<li>La consulta contiene un paso.</li>
<li>El paso no se divide.</li>
</ul>
</td>
<td>6</td></tr>

<tr><td>
<ul>
<li>La secuencia de entrada de datos está dividida por 3.</li>
<li>La consulta contiene un paso.</li>
<li>El paso se divide.</li>
</ul>
</td>
<td>18</td></tr>

<tr><td>
<ul>
<li>La consulta contiene dos pasos.</li>
<li>Ninguno de los pasos se divide.</li>
</ul>
</td>
<td>6</td></tr>



<tr><td>
<ul>
<li>La entrada de la secuencia de datos se divide por 3.</li>
<li>La consulta contiene dos pasos. El paso de entrada se divide y no el segundo paso.</li>
<li>La instrucción SELECT se lee desde la entrada dividida.</li>
</ul>
</td>
<td>24 (18 para conocer los pasos con particiones) + 6 para conocer los pasos sin particiones</td></tr>
</table>

### <a name="examples-of-scale"></a>Ejemplos de escala
La siguiente consulta calcula el número de coches atravesando una estación de pago con tres casetas de cuota dentro de una ventana de tres minutos. Esta consulta puede ampliarse hasta seis unidades streaming.

    SELECT COUNT(*) AS Count, TollBoothId
    FROM Input1
    GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId

Para usar más unidades de transmisiones de la consulta, la secuencia de entrada ambos los datos y la consulta debe tener particiones. Dado que la partición de flujo de datos se establece como 3, la siguiente consulta modificada se puede escalar hasta 18 unidades streaming:

    SELECT COUNT(*) AS Count, TollBoothId
    FROM Input1 Partition By PartitionId
    GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId

Cuando se divide una consulta, los eventos de entrada se procesa y se agregan en grupos de partición independiente. También se generan eventos de salida para cada uno de los grupos. Partición puede provocar algunos resultados inesperados cuando el campo **Agrupar por** no es la clave de partición en la entrada de la secuencia de datos. Por ejemplo, el campo **TollBoothId** en la consulta del ejemplo anterior no es la clave de partición de Entrada1. Pueden se distribuyan de los datos desde el 1 de peaje en varias particiones.

Cada una de las particiones Entrada1 serán procesada por separado por el análisis de secuencia y se creará varios registros del recuento coche pase por para el mismo peaje en la misma ventana de rotación. Si no se puede cambiar la clave de partición de entrada, este problema puede corregirse agregando un paso adicional de partición que no es, por ejemplo:

    WITH Step1 AS (
        SELECT COUNT(*) AS Count, TollBoothId
        FROM Input1 Partition By PartitionId
        GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId
    )

    SELECT SUM(Count) AS Count, TollBoothId
    FROM Step1
    GROUP BY TumblingWindow(minute, 3), TollBoothId

Esta consulta se puede escalar a 24 transmisión unidades.

>[AZURE.NOTE] Si se están uniendo dos secuencias, asegúrese de que las secuencias se dividen por la clave de partición de la columna que siga las combinaciones y tiene el mismo número de particiones en ambas secuencias.


## <a name="configure-stream-analytics-job-partition"></a>Configurar partición de trabajo de análisis de secuencia

**Para ajustar una unidad streaming para una tarea**

1. Inicie sesión en el [portal de administración](https://manage.windowsazure.com).
2. En el panel izquierdo, haga clic en **Análisis de secuencia** .
3. Haga clic en el trabajo de análisis de flujo que desea ajustar.
4. Haga clic en **escala** en la parte superior de la página.

![Escala de unidades de secuencia de análisis de secuencia de Azure][img.stream.analytics.streaming.units.scale]

En el Portal de Azure, se puede tener acceso a configuración de escala en configuración:

![Configuración de trabajo de análisis de secuencia de Portal Azure][img.stream.analytics.preview.portal.settings.scale]

## <a name="monitor-job-performance"></a>Supervisar el rendimiento del trabajo

Con el portal de administración, puede realizar el seguimiento del rendimiento de un trabajo de eventos por segundo:

![Análisis de secuencia de Azure supervisar trabajos][img.stream.analytics.monitor.job]

Calcular el rendimiento esperado de la carga de trabajo de eventos por segundo. Si el rendimiento es menor de lo esperado, ajustar la partición de entrada, ajustar la consulta y agregar unidades streaming adicionales a su trabajo.

## <a name="stream-analytics-throughput-at-scale---raspberry-pi-scenario"></a>Rendimiento de análisis de Stream a escala - escenario frambuesas Pi


Para comprender cómo trabajos de análisis de secuencia escalar en un escenario típico en términos de rendimiento de procesamiento de varias unidades de transmisión, aquí tiene un ensayo que envía los datos de sensor (clientes) a concentrador de evento, procesa y envía alerta o estadísticas como salida a otro concentrador de evento.

El cliente envía los datos de sensor síntesis a Hubs de evento en formato JSON para el análisis de secuencia y la salida de datos también está en formato JSON.  Le mostramos cómo los datos de ejemplo tendría un aspecto similar al:  

    {"devicetime":"2014-12-11T02:24:56.8850110Z","hmdt":42.7,"temp":72.6,"prss":98187.75,"lght":0.38,"dspl":"R-PI Olivier's Office"}

Consulta: "enviar una alerta cuando se desactiva la luz"  

    SELECT AVG(lght),
     “LightOff” as AlertText
    FROM input TIMESTAMP
    BY devicetime
     WHERE
        lght< 0.05 GROUP BY TumblingWindow(second, 1)

Medir el rendimiento: Rendimiento en este contexto es la cantidad de datos de entrada procesados por el análisis de secuencia en una cantidad fija de tiempo (10 minutos). Para obtener el mejor rendimiento de procesamiento de los datos de entrada, entrada de secuencia de los datos y la consulta debe tener particiones. También **COUNT()**se incluye en la consulta para medir procesados cuántos eventos de entrada. Para asegurarse de que el trabajo no está esperando simplemente vienen eventos de entrada, se ha cargado cada partición del concentrador de evento de entrada con datos de entrada suficientes (aproximadamente 300MB).  

A continuación se muestran los resultados con mayor número de unidades de Streaming y recuentos de partición correspondiente en Hubs de evento.  

<table border="1">
<tr><th>Entrada de particiones</th><th>Particiones de salida</th><th>Unidades de transmisión por secuencias</th><th>Rendimiento prolongado
</th></td>

<tr><td>12</td>
<td>12</td>
<td>6</td>
<td>4.06 MB/s</td>
</tr>

<tr><td>12</td>
<td>12</td>
<td>12</td>
<td>8.06 MB/s</td>
</tr>

<tr><td>48</td>
<td>48</td>
<td>48</td>
<td>38.32 MB/s</td>
</tr>

<tr><td>192</td>
<td>192</td>
<td>192</td>
<td>172.67 MB/s</td>
</tr>

<tr><td>480</td>
<td>480</td>
<td>480</td>
<td>454.27 MB/s</td>
</tr>

<tr><td>720</td>
<td>720</td>
<td>720</td>
<td>609.69 MB/s</td>
</tr>
</table>

![IMG.Stream.Analytics.perfgraph][img.stream.analytics.perfgraph]

## <a name="get-help"></a>Obtener ayuda
Para obtener más ayuda, pruebe a nuestro [foro de análisis de secuencia de Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics).


## <a name="next-steps"></a>Pasos siguientes

- [Introducción a análisis de secuencia de Azure](stream-analytics-introduction.md)
- [Empezar a usar el análisis de secuencia de Azure](stream-analytics-get-started.md)
- [Referencia del lenguaje de consulta de análisis de secuencia de Azure](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Referencia de la API de REST de administración de secuencia de Azure análisis](https://msdn.microsoft.com/library/azure/dn835031.aspx)


<!--Image references-->

[img.stream.analytics.monitor.job]: ./media/stream-analytics-scale-jobs/StreamAnalytics.job.monitor.png
[img.stream.analytics.configure.scale]: ./media/stream-analytics-scale-jobs/StreamAnalytics.configure.scale.png
[img.stream.analytics.perfgraph]: ./media/stream-analytics-scale-jobs/perf.png
[img.stream.analytics.streaming.units.scale]: ./media/stream-analytics-scale-jobs/StreamAnalyticsStreamingUnitsExample.jpg
[img.stream.analytics.preview.portal.settings.scale]: ./media/stream-analytics-scale-jobs/StreamAnalyticsPreviewPortalJobSettings.png

<!--Link references-->

[microsoft.support]: http://support.microsoft.com
[azure.management.portal]: http://manage.windowsazure.com
[azure.event.hubs.developer.guide]: http://msdn.microsoft.com/library/azure/dn789972.aspx

[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-get-started.md
[stream.analytics.query.language.reference]: http://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: http://go.microsoft.com/fwlink/?LinkId=517301
 
