<properties
    pageTitle="Introducción a las funciones de la ventana de análisis de secuencia | Microsoft Azure"
    description="Obtenga información sobre las funciones de ventana tres en análisis de secuencia (rotación, salto, al Deslizar)."
    keywords="rotación de ventana, la ventana, salto ventana deslizante"
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


# <a name="introduction-to-stream-analytics-window-functions"></a>Introducción a las funciones de la ventana de análisis de secuencia

En tiempo de real muchos transmisión escenarios, es necesario realizar operaciones solo en los datos contenidos en windows temporales. Compatibilidad para las funciones de ventanas es una característica clave de análisis de secuencia de Azure que se mueve la aguja en productividad del programador de trabajos de procesamiento de secuencia compleja de creación. Análisis de secuencia permite a los desarrolladores usar windows [**rotación**](https://msdn.microsoft.com/library/dn835055.aspx), [**Hopping**](https://msdn.microsoft.com/library/dn835041.aspx) y [**deslizante**](https://msdn.microsoft.com/library/dn835051.aspx) realizar operaciones temporales en la transmisión de datos. Es importante recordar que todas las operaciones de la [ventana de](https://msdn.microsoft.com/library/dn835019.aspx) resultados al **final** de la ventana de resultados. El resultado de la ventana será único evento en función de la función de agregado que se utiliza. El evento tendrá la marca de tiempo del final de la ventana y todas las funciones de la ventana se definen con una longitud fija. Por último es importante que tenga en cuenta que todas las funciones de la ventana deben usarse en una cláusula [**GROUP BY**](https://msdn.microsoft.com/library/dn835023.aspx) .

![Conceptos de ventana de análisis de flujo de funciones](media/stream-analytics-window-functions/stream-analytics-window-functions-conceptual.png)

## <a name="tumbling-window"></a>Ventana de rotación

Rotación ventana funciones se utilizan para segmentar una secuencia de datos en los segmentos de tiempo determinado y realizar una función en ellas, como el ejemplo siguiente. La qu de una ventana de rotación es que repita, no se superponen y un evento no pertenece a más de una ventana de rotación.

![Funciones de la ventana de análisis de secuencia rotación Introducción](media/stream-analytics-window-functions/stream-analytics-window-functions-tumbling-intro.png)

## <a name="hopping-window"></a>Ventana de recursos

Cambios de frecuencia ventana funciones salto hacia delante en el tiempo en un período determinado. Es fácil pensar que windows rotación que pueden superponerse, de manera eventos pueden pertenecer a más de un conjunto de resultados de la ventana Hopping. Para crear una ventana de Hopping igual que un rotación ventana uno simplemente ¿especifique el tamaño de salto para coincidir con el tamaño de la ventana. 

![Funciones de la ventana de análisis de secuencia salto Introducción](media/stream-analytics-window-functions/stream-analytics-window-functions-hopping-intro.png)

## <a name="sliding-window"></a>Ventana deslizante

Funciones de ventana deslizante, a diferencia de windows Hopping o rotación, producen un resultado **solo** cuando se produce un evento. Cada ventana tendrá al menos un evento y la ventana continuamente se mueve hacia delante por un € (épsilon). Como Windows salto, eventos pueden pertenecer a más de una ventana.

![Funciones de la ventana de análisis de secuencia deslizamiento Introducción](media/stream-analytics-window-functions/stream-analytics-window-functions-sliding-intro.png)

## <a name="getting-help-with-window-functions"></a>Obtener ayuda con las funciones de la ventana

Para obtener más ayuda, pruebe nuestro [foro de análisis de secuencia de Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Pasos siguientes

- [Introducción a análisis de secuencia de Azure](stream-analytics-introduction.md)
- [Empezar a usar el análisis de secuencia de Azure](stream-analytics-get-started.md)
- [Escala trabajos de análisis de secuencia de Azure](stream-analytics-scale-jobs.md)
- [Referencia del lenguaje de consulta de análisis de secuencia de Azure](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Referencia de la API de REST de administración de secuencia de Azure análisis](https://msdn.microsoft.com/library/azure/dn835031.aspx)
