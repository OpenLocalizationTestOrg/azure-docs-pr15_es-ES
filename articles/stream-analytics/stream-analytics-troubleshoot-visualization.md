<properties
    pageTitle="Visualizar y solución de problemas de trabajos de análisis de secuencia | Microsoft Azure"
    description="Obtenga información sobre cómo visualizar un proceso de trabajo de análisis de secuencia para autoservicio de solución de problemas con la característica de diagrama de diagnósticos."
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


# <a name="visualize-and-troubleshoot-stream-analytics-jobs"></a>Visualizar y solución de problemas de trabajos de análisis de secuencia

En el análisis de secuencia, al igual que con otras tecnologías basadas en la nube, solución de problemas es a veces necesaria para vistazo ¿por qué un trabajo no genera el resultado esperado (o ningún resultado en este caso). Con esto en mente, análisis de secuencia proporciona la capacidad para visualizar un trabajo streaming. También es útil como una herramienta de modelado y tiene el beneficio de lado para los que requieren documentación de su trabajo.

En el panel de visualización son visibles, así como la consulta que se está ejecutando y, a continuación, todos los resultados configurados las entradas. Problemas de conectividad o configuración pueden convertirse en más evidente, y también puede ser útil ver una representación visual de la configuración.

## <a name="using-the-diagnosis-diagram-tool"></a>Usar la herramienta de diagnóstico de diagrama

Para obtener acceso a este visualizador, simplemente haga clic en el botón "Diagrama de diagnóstico" en el módulo "Configuración" de la de la tarea de análisis de la secuencia.

![Stream-Analytics-Troubleshoot-Visualization-Diagnosis-Diagram](./media/stream-analytics-troubleshoot-visualization/stream-analytics-troubleshoot-visualization-diagnosis-diagram1.png)

Cada entrada y salida es el estado de colores diferentes para indicar el actual de componente, tal como se muestra a continuación.

![Stream-Analytics-Troubleshoot-Visualization-Input-Map](./media/stream-analytics-troubleshoot-visualization/stream-analytics-troubleshoot-visualization-input-map.png)

Cuando el usuario desea ver los pasos de consulta intermedio para comprender los patrones de flujo de datos dentro de una tarea, la herramienta de visualización ofrece una vista de la división de la consulta en los pasos de componente y la secuencia de flujo. Al hacer clic en cada paso de consulta mostrará la sección correspondiente en una consulta de edición panel tal como se muestra. 

![Stream-Analytics-Troubleshoot-Visualization-Intermediate-Steps](./media/stream-analytics-troubleshoot-visualization/stream-analytics-troubleshoot-visualization-intermediate-steps.png)




## <a name="next-steps"></a>Pasos siguientes

- [Introducción a análisis de secuencia de Azure](stream-analytics-introduction.md)
- [Empezar a usar el análisis de secuencia de Azure](stream-analytics-get-started.md)
- [Escala trabajos de análisis de secuencia de Azure](stream-analytics-scale-jobs.md)
- [Referencia del lenguaje de consulta de análisis de secuencia de Azure](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Referencia de la API de REST de administración de secuencia de Azure análisis](https://msdn.microsoft.com/library/azure/dn835031.aspx)
