<properties 
   pageTitle="Tabla de límites de análisis de secuencia"
   description="Describe los límites de sistema y tamaños recomendados para los componentes de análisis de flujo y las conexiones."
   services="stream-analytics"
   documentationCenter="NA"
   authors="jeffstokes72"
   manager="paulettm"
   editor="cgronlun" />
<tags 
   ms.service="stream-analytics"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="big-data"
   ms.date="07/25/2016"
   ms.author="jeffstok" />

| Identificador de límite | Límite       | Comentarios |
|----------------- | ------------|--------- |
| Número máximo de unidades de transmisión por suscripción por región | 50 | Póngase en contacto con [Soporte técnico de Microsoft](https://support.microsoft.com/en-us)pueden realizarse una solicitud para aumentar la transmisión unidades para su suscripción más allá de 50. |
| Rendimiento máximo de una unidad de transmisión | 1MB / s * | Rendimiento máximo por SU depende del escenario. Rendimiento real puede ser inferior y depende de complejidad de la consulta y particiones. Encontrará más detalles en el artículo de [trabajos de análisis de secuencia de Azure escala para aumentar el rendimiento](../articles/stream-analytics/stream-analytics-scale-jobs.md) . |
| Número máximo de entradas por trabajo | 60 | Hay un límite máximo de 60 entradas por trabajo de análisis de la secuencia. |
| Número máximo de resultados por trabajo | 60 | Hay un límite máximo de 60 salidas por trabajo de análisis de la secuencia. |
| Número máximo de funciones por trabajo | 60 | Hay un límite máximo de 60 funciones por trabajo de análisis de la secuencia. |
| Número máximo de trabajos por región | 1500 | Cada suscripción puede tener hasta 1500 trabajos por región geográfica. |