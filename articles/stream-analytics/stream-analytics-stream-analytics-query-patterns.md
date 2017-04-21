<properties
    pageTitle="Ejemplos de patrones comunes de uso de análisis de secuencia de consulta | Microsoft Azure"
    description="Patrones de consulta de análisis de secuencia de Azure comunes "
    keywords="ejemplos de consultas"
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
    ms.workload="big-data"
    ms.date="09/26/2016"
    ms.author="jeffstok"/>


# <a name="query-examples-for-common-stream-analytics-usage-patterns"></a>Ejemplos de patrones habituales de uso de análisis de secuencia de consulta

## <a name="introduction"></a>Introducción

Las consultas de análisis de secuencia de Azure se expresan en un lenguaje de consulta SQL similares, que se describe en la Guía de [Referencia del lenguaje de consulta de análisis de secuencia](https://msdn.microsoft.com/library/azure/dn834998.aspx) .  Este artículo describen las soluciones de varios patrones de consulta comunes basados en escenarios del mundo real.  Es un trabajo en curso y seguirá se actualizará con nuevos patrones de forma periódica.

## <a name="query-example-data-type-conversions"></a>Ejemplo de consulta: conversión de tipos de datos
**Descripción**: definir los tipos de las propiedades de la secuencia de entrada.
Por ejemplo, el grosor de coches pronto estará disponible en la secuencia de entrada como cadenas y debe convertirse a INT para realizar la suma hacia arriba.

**Entrada**:

| Asegúrese de | Hora | Peso |
| --- | --- | --- |
| Honda | 2015-01-01T00:00:01.0000000Z | "1000" |
| Honda | 2015-01-01T00:00:02.0000000Z | "2000" |

**Resultado**:

| Asegúrese de | Peso |
| --- | --- |
| Honda | 3000 |

**Solución**:

    SELECT
        Make,
        SUM(CAST(Weight AS BIGINT)) AS Weight
    FROM
        Input TIMESTAMP BY Time
    GROUP BY
        Make,
        TumblingWindow(second, 10)

**Explicación**: usar una instrucción de conversión en el campo peso para especificar su tipo (vea la lista de tipos de datos compatibles [aquí](https://msdn.microsoft.com/library/azure/dn835065.aspx)).


## <a name="query-example-using-likenot-like-to-do-pattern-matching"></a>Ejemplo de consulta: usando similares y no le gusta como patrón coincidentes
**Descripción**: Compruebe que el valor de un campo en el evento coincide con una determinada trama por ejemplo, devueltas planchas de licencia que empiezan con A y terminan con 9

**Entrada**:

| Asegúrese de | LicensePlate | Hora |
| --- | --- | --- |
| Honda | ABC 123 | 2015-01-01T00:00:01.0000000Z |
| Toyota | AAA 999 | 2015-01-01T00:00:02.0000000Z |
| Nissan | 369 ABC | 2015-01-01T00:00:03.0000000Z |

**Resultado**:

| Asegúrese de | LicensePlate | Hora |
| --- | --- | --- |
| Toyota | AAA 999 | 2015-01-01T00:00:02.0000000Z |
| Nissan | 369 ABC | 2015-01-01T00:00:03.0000000Z |

**Solución**:

    SELECT
        *
    FROM
        Input TIMESTAMP BY Time
    WHERE
        LicensePlate LIKE 'A%9'

**Explicación**: utilice la instrucción LIKE para comprobar que el valor del campo LicensePlate comienza con A continuación tiene cualquier cadena de cero o más caracteres, y termina con 9. 

## <a name="query-example-specify-logic-for-different-casesvalues-case-statements"></a>Ejemplo de consulta: especifique lógica para diferentes casos o valores (resúmenes de mayúsculas y MINÚSCULAS)
**Descripción**: proporcione cálculo diferente para un campo en función de algunos criterios.
Por ejemplo, proporcione una descripción de cadena para automóviles cuántos pasan de la misma marca con un caso especial para 1.

**Entrada**:

| Asegúrese de | Hora |
| --- | --- |
| Honda | 2015-01-01T00:00:01.0000000Z |
| Toyota | 2015-01-01T00:00:02.0000000Z |
| Toyota | 2015-01-01T00:00:03.0000000Z |

**Resultado**:

| CarsPassed | Hora |
| --- | --- | --- |
| 1 Honda | 2015-01-01T00:00:10.0000000Z |
| 2 Toyotas | 2015-01-01T00:00:10.0000000Z |

**Solución**:

    SELECT
        CASE
            WHEN COUNT(*) = 1 THEN CONCAT('1 ', Make)
            ELSE CONCAT(CAST(COUNT(*) AS NVARCHAR(MAX)), ' ', Make, 's')
        END AS CarsPassed,
        System.TimeStamp AS Time
    FROM
        Input TIMESTAMP BY Time
    GROUP BY
        Make,
        TumblingWindow(second, 10)

**Explicación**: cláusula el caso nos permite proporcionar un cálculo diferentes según algunos criterios (en nuestro caso, el recuento de coches en la ventana Agregar).

## <a name="query-example-send-data-to-multiple-outputs"></a>Ejemplo de consulta: enviar datos a varios resultados
**Descripción**: enviar datos a varios destinos desde un único trabajo.
Por ejemplo, analizar datos para una alerta de umbral y archivar todos los eventos de almacenamiento de blobs

**Entrada**:

| Asegúrese de | Hora |
| --- | --- |
| Honda | 2015-01-01T00:00:01.0000000Z |
| Honda | 2015-01-01T00:00:02.0000000Z |
| Toyota | 2015-01-01T00:00:01.0000000Z |
| Toyota | 2015-01-01T00:00:02.0000000Z |
| Toyota | 2015-01-01T00:00:03.0000000Z |

**Output1**:

| Asegúrese de | Hora |
| --- | --- |
| Honda | 2015-01-01T00:00:01.0000000Z |
| Honda | 2015-01-01T00:00:02.0000000Z |
| Toyota | 2015-01-01T00:00:01.0000000Z |
| Toyota | 2015-01-01T00:00:02.0000000Z |
| Toyota | 2015-01-01T00:00:03.0000000Z |

**Output2**:

| Asegúrese de | Hora | Recuento |
| --- | --- | --- |
| Toyota | 2015-01-01T00:00:10.0000000Z | 3 |

**Solución**:

    SELECT
        *
    INTO
        ArchiveOutput
    FROM
        Input TIMESTAMP BY Time

    SELECT
        Make,
        System.TimeStamp AS Time,
        COUNT(*) AS [Count]
    INTO
        AlertOutput
    FROM
        Input TIMESTAMP BY Time
    GROUP BY
        Make,
        TumblingWindow(second, 10)
    HAVING
        [Count] >= 3

**Explicación**: en la cláusula indica el análisis de secuencia cuál de los resultados para escribir los datos de esta declaración.
La primera consulta es un paso a través de los datos que hemos recibido a un resultado que se denomina ArchiveOutput.
La segunda consulta hace algunas agregación simple y filtrado y envía los resultados a un sistema de alertas descendente.
*Nota*: también puede volver a los resultados de las CTE (es decir, con instrucciones) en varios resúmenes de salida: Esto tiene la ventaja de abrir lectores con el origen de entrada.
Por ejemplo, 

    WITH AllRedCars AS (
        SELECT
            *
        FROM
            Input TIMESTAMP BY Time
        WHERE
            Color = 'red'
    )
    SELECT * INTO HondaOutput FROM AllRedCars WHERE Make = 'Honda'
    SELECT * INTO ToyotaOutput FROM AllRedCars WHERE Make = 'Toyota'

## <a name="query-example-counting-unique-values"></a>Ejemplo de consulta: contar valores únicos
**Descripción**: contar el número de valores de campo únicos que aparecen en la secuencia dentro de una ventana de tiempo.
¿Por ejemplo, cuántos realizar únicos de coches pasa a través de la cabina de pago en una ventana del segundo 2?

**Entrada**:

| Asegúrese de | Hora |
| --- | --- |
| Honda | 2015-01-01T00:00:01.0000000Z |
| Honda | 2015-01-01T00:00:02.0000000Z |
| Toyota | 2015-01-01T00:00:01.0000000Z |
| Toyota | 2015-01-01T00:00:02.0000000Z |
| Toyota | 2015-01-01T00:00:03.0000000Z |

**Resultado:**

| Recuento | Hora |
| --- | --- |
| 2 | 2015-01-01T00:00:02.000Z |
| 1 | 2015-01-01T00:00:04.000Z |

**Solución:**

    WITH Makes AS (
        SELECT
            Make,
            COUNT(*) AS CountMake
        FROM
            Input TIMESTAMP BY Time
        GROUP BY
              Make,
              TumblingWindow(second, 2)
    )
    SELECT
        COUNT(*) AS Count,
        System.TimeStamp AS Time
    FROM
        Makes
    GROUP BY
        TumblingWindow(second, 1)


**Explicación:** Para ello una agregación inicial para obtener hace único con su recuento por encima de la ventana.
A continuación, realizamos una agregación de cuántos hace que nos got – dado únicas valores en una ventana llegar la misma marca de tiempo y la segunda ventana de agregación debe ser mínima no agrega 2 windows desde el primer paso.

## <a name="query-example-determine-if-a-value-has-changed"></a>Ejemplo de consulta: determinar si un valor ha cambiado#
**¿Descripción**: busque un valor anterior para determinar si es diferente del valor actual por ejemplo, el automóvil anterior de la oficina de pago la misma marca como el automóvil actual?

**Entrada**:

| Asegúrese de | Hora |
| --- | --- |
| Honda | 2015-01-01T00:00:01.0000000Z |
| Toyota | 2015-01-01T00:00:02.0000000Z |

**Resultado**:

| Asegúrese de | Hora |
| --- | --- |
| Toyota | 2015-01-01T00:00:02.0000000Z |

**Solución**:

    SELECT
        Make,
        Time
    FROM
        Input TIMESTAMP BY Time
    WHERE
        LAG(Make, 1) OVER (LIMIT DURATION(minute, 1)) <> Make

**Explicación**: usar POSPOSICIÓN para inspeccionar en la entrada transmita un evento nuevo y obtener el valor de creación. A continuación, comparar la marca en el evento actual y el evento de salida si son diferentes.

## <a name="query-example-find-first-event-in-a-window"></a>Ejemplo de consulta: buscar primer evento en una ventana
**¿Descripción**: buscar primer coche en el intervalo de cada 10 minutos?

**Entrada**:

| LicensePlate | Asegúrese de | Hora |
| --- | --- | --- |
| DXE 5291 | Honda | 2015-07-27T00:00:05.0000000Z |
| YZK 5704 | Ford | 2015-07-27T00:02:17.0000000Z |
| QUITAR 8282 | Honda | 2015-07-27T00:05:01.0000000Z |
| YHN 6970 | Toyota | 2015-07-27T00:06:00.0000000Z |
| VFE 1616 | Toyota | 2015-07-27T00:09:31.0000000Z |
| QYF 9358 | Honda | 2015-07-27T00:12:02.0000000Z |
| MDR 6128 | BMW | 2015-07-27T00:13:45.0000000Z |

**Resultado**:

| LicensePlate | Asegúrese de | Hora |
| --- | --- | --- |
| DXE 5291 | Honda | 2015-07-27T00:00:05.0000000Z |
| QYF 9358 | Honda | 2015-07-27T00:12:02.0000000Z |

**Solución**:

    SELECT 
        LicensePlate,
        Make,
        Time
    FROM 
        Input TIMESTAMP BY Time
    WHERE 
        IsFirst(minute, 10) = 1

Ahora vamos a hacer cambiar el problema y buscar primer coche de determinado en el intervalo de cada 10 minutos.

| LicensePlate | Asegúrese de | Hora |
| --- | --- | --- |
| DXE 5291 | Honda | 2015-07-27T00:00:05.0000000Z |
| YZK 5704 | Ford | 2015-07-27T00:02:17.0000000Z |
| YHN 6970 | Toyota | 2015-07-27T00:06:00.0000000Z |
| QYF 9358 | Honda | 2015-07-27T00:12:02.0000000Z |
| MDR 6128 | BMW | 2015-07-27T00:13:45.0000000Z |

**Solución**:

    SELECT 
        LicensePlate,
        Make,
        Time
    FROM 
        Input TIMESTAMP BY Time
    WHERE 
        IsFirst(minute, 10) OVER (PARTITION BY Make) = 1

## <a name="query-example-find-last-event-in-a-window"></a>Ejemplo de consulta: buscar último evento en una ventana
**Descripción**: coche última de buscar en el intervalo de cada 10 minutos.

**Entrada**:

| LicensePlate | Asegúrese de | Hora |
| --- | --- | --- |
| DXE 5291 | Honda | 2015-07-27T00:00:05.0000000Z |
| YZK 5704 | Ford | 2015-07-27T00:02:17.0000000Z |
| QUITAR 8282 | Honda | 2015-07-27T00:05:01.0000000Z |
| YHN 6970 | Toyota | 2015-07-27T00:06:00.0000000Z |
| VFE 1616 | Toyota | 2015-07-27T00:09:31.0000000Z |
| QYF 9358 | Honda | 2015-07-27T00:12:02.0000000Z |
| MDR 6128 | BMW | 2015-07-27T00:13:45.0000000Z |

**Resultado**:

| LicensePlate | Asegúrese de | Hora |
| --- | --- | --- |
| VFE 1616 | Toyota | 2015-07-27T00:09:31.0000000Z |
| MDR 6128 | BMW | 2015-07-27T00:13:45.0000000Z |

**Solución**:

    WITH LastInWindow AS
    (
        SELECT 
            MAX(Time) AS LastEventTime
        FROM 
            Input TIMESTAMP BY Time
        GROUP BY 
            TumblingWindow(minute, 10)
    )
    SELECT 
        Input.LicensePlate,
        Input.Make,
        Input.Time
    FROM
        Input TIMESTAMP BY Time 
        INNER JOIN LastInWindow
        ON DATEDIFF(minute, Input, LastInWindow) BETWEEN 0 AND 10
        AND Input.Time = LastInWindow.LastEventTime

**Explicación**: hay dos pasos en la consulta, primero busca más reciente de windows 10 minutos. El segundo paso combina los resultados de la primera consulta con secuencia original para buscar los eventos que coinciden con las marcas de tiempo último en cada ventana. 

## <a name="query-example-detect-the-absence-of-events"></a>Ejemplo de consulta: detectar la ausencia de eventos
**Descripción**: comprobar que una secuencia no tiene ningún valor que coincida con determinados criterios.
¿Por ejemplo, 2 automóviles consecutivas desde la misma marca han introducido la carretera pago dentro de 90 segundos?

**Entrada**:

| Asegúrese de | LicensePlate | Hora |
| --- | --- | --- |
| Honda | ABC 123 | 2015-01-01T00:00:01.0000000Z |
| Honda | AAA 999 | 2015-01-01T00:00:02.0000000Z |
| Toyota | DEFINICIÓN 987 | 2015-01-01T00:00:03.0000000Z |
| Honda | 345 GHI | 2015-01-01T00:00:04.0000000Z |

**Resultado**:

| Asegúrese de | Hora | CurrentCarLicensePlate | FirstCarLicensePlate | FirstCarTime |
| --- | --- | --- | --- | --- |
| Honda | 2015-01-01T00:00:02.0000000Z | AAA 999 | ABC 123 | 2015-01-01T00:00:01.0000000Z |

**Solución**:

    SELECT
        Make,
        Time,
        LicensePlate AS CurrentCarLicensePlate,
        LAG(LicensePlate, 1) OVER (LIMIT DURATION(second, 90)) AS FirstCarLicensePlate,
        LAG(Time, 1) OVER (LIMIT DURATION(second, 90)) AS FirstCarTime
    FROM
        Input TIMESTAMP BY Time
    WHERE
        LAG(Make, 1) OVER (LIMIT DURATION(second, 90)) = Make

**Explicación**: usar POSPOSICIÓN para inspeccionar en la entrada transmita un evento nuevo y obtener el valor de creación. A continuación, comparar la marca en el evento actual y el evento de salida si son los mismos y utilizar POSPOSICIÓN para obtener información sobre el automóvil anterior.

## <a name="query-example-detect-duration-between-events"></a>Ejemplo de consulta: detectar la duración entre eventos
**Descripción**: buscar la duración de un evento determinado. Por ejemplo, dada una secuencia de clics web determinar el tiempo invertido en una función.

**Entrada**:  
  
| Usuario | Característica | Evento | Hora |
| --- | --- | --- | --- |
| user@location.com | RightMenu | Inicio | 2015-01-01T00:00:01.0000000Z |
| user@location.com | RightMenu | Final | 2015-01-01T00:00:08.0000000Z |
  
**Resultado**:  
  
| Usuario | Característica | Duración |
| --- | --- | --- |
| user@location.com | RightMenu | 7 |
  

**Solución**

````
    SELECT
        [user], feature, DATEDIFF(second, LAST(Time) OVER (PARTITION BY [user], feature LIMIT DURATION(hour, 1) WHEN Event = 'start'), Time) as duration
    FROM input TIMESTAMP BY Time
    WHERE
        Event = 'end'
````

**Explicación**: función última Use para recuperar el último valor de hora al tipo de evento fue 'Inicio'. Observe que la última función utiliza partición por [usuario] para indicar que se calculará por usuario único resultado.  La consulta tiene un umbral máximo de 1 hora para la diferencia de tiempo entre eventos de 'Inicio' y 'Stop', pero se puede configurar como sea necesario (límite DURATION(hour, 1).

## <a name="query-example-detect-duration-of-a-condition"></a>Ejemplo de consulta: detectar la duración de una condición
**Descripción**: averiguar cuánto se ha producido una condición.
Por ejemplo, suponga que un error que ha generado todos los coches cuyo peso incorrecto (por encima de 20 000 libras): queremos calcular la duración del error.

**Entrada**:

| Asegúrese de | Hora | Peso |
| --- | --- | --- |
| Honda | 2015-01-01T00:00:01.0000000Z | 2000 |
| Toyota | 2015-01-01T00:00:02.0000000Z | 25000 |
| Honda | 2015-01-01T00:00:03.0000000Z | 26000 |
| Toyota | 2015-01-01T00:00:04.0000000Z | 25000 |
| Honda | 2015-01-01T00:00:05.0000000Z | 26000 |
| Toyota | 2015-01-01T00:00:06.0000000Z | 25000 |
| Honda | 2015-01-01T00:00:07.0000000Z | 26000 |
| Toyota | 2015-01-01T00:00:08.0000000Z | 2000 |

**Resultado**:

| StartFault | EndFault |
| --- | --- |
| 2015-01-01T00:00:02.000Z | 2015-01-01T00:00:07.000Z |

**Solución**:

````
    WITH SelectPreviousEvent AS
    (
    SELECT
    *,
        LAG([time]) OVER (LIMIT DURATION(hour, 24)) as previousTime,
        LAG([weight]) OVER (LIMIT DURATION(hour, 24)) as previousWeight
    FROM input TIMESTAMP BY [time]
    )

    SELECT 
        LAG(time) OVER (LIMIT DURATION(hour, 24) WHEN previousWeight < 20000 ) [StartFault],
        previousTime [EndFault]
    FROM SelectPreviousEvent
    WHERE
        [weight] < 20000
        AND previousWeight > 20000
````

**Explicación**: usar POSPOSICIÓN para ver la secuencia de entrada de 24 horas y busque instancias donde StartFault y StopFault abarca peso < 20000.

## <a name="query-example-fill-missing-values"></a>Ejemplo de consulta: rellenar los valores que faltan
**Descripción**: para la secuencia de eventos que les faltan valores, crear una secuencia de eventos con regularidad.
Por ejemplo, Generar evento cada 5 segundos que informa del punto de datos ha visto más recientemente.

**Entrada**:

| t | valor |
|--------------------------|-------|
| "2014-01-01T06:01:00" | 1 |
| "2014-01-01T06:01:05" | 2 |
| "2014-01-01T06:01:10" | 3 |
| "2014-01-01T06:01:15" | 4 |
| "2014-01-01T06:01:30" | 5 |
| "2014-01-01T06:01:35" | 6 |

**Resultado (10 primeros filas)**:

| windowend | lastevent.t | lastevent.Value |
|--------------------------|--------------------------|--------|
| 2014-01-01T14:01:00.000Z | 2014-01-01T14:01:00.000Z | 1 |
| 2014-01-01T14:01:05.000Z | 2014-01-01T14:01:05.000Z | 2 |
| 2014-01-01T14:01:10.000Z | 2014-01-01T14:01:10.000Z | 3 |
| 2014-01-01T14:01:15.000Z | 2014-01-01T14:01:15.000Z | 4 |
| 2014-01-01T14:01:20.000Z | 2014-01-01T14:01:15.000Z | 4 |
| 2014-01-01T14:01:25.000Z | 2014-01-01T14:01:15.000Z | 4 |
| 2014-01-01T14:01:30.000Z | 2014-01-01T14:01:30.000Z | 5 |
| 2014-01-01T14:01:35.000Z | 2014-01-01T14:01:35.000Z | 6 |
| 2014-01-01T14:01:40.000Z | 2014-01-01T14:01:35.000Z | 6 |
| 2014-01-01T14:01:45.000Z | 2014-01-01T14:01:35.000Z | 6 |

    
**Solución**:

    SELECT
        System.Timestamp AS windowEnd,
        TopOne() OVER (ORDER BY t DESC) AS lastEvent
    FROM
        input TIMESTAMP BY t
    GROUP BY HOPPINGWINDOW(second, 300, 5)


**Explicación**: esta consulta genera eventos cada segundo 5 y mostrará el último evento que se ha recibido antes. [Salto de ventana] Duración de la (https://msdn.microsoft.com/library/dn835041.aspx "Ventana de salto: análisis de secuencia de Azure") determina cuánto atrás se verá la consulta para encontrar el evento más reciente (300 segundos en este ejemplo).


## <a name="get-help"></a>Obtener ayuda
Para obtener más ayuda, pruebe nuestro [foro de análisis de secuencia de Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Pasos siguientes

- [Introducción a análisis de secuencia de Azure](stream-analytics-introduction.md)
- [Empezar a usar el análisis de secuencia de Azure](stream-analytics-get-started.md)
- [Escala trabajos de análisis de secuencia de Azure](stream-analytics-scale-jobs.md)
- [Referencia del lenguaje de consulta de análisis de secuencia de Azure](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Referencia de la API de REST de administración de secuencia de Azure análisis](https://msdn.microsoft.com/library/azure/dn835031.aspx)
 
