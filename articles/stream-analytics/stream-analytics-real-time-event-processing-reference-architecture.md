<properties 
    pageTitle="Procesamiento de procesamiento de eventos de análisis de secuencia de eventos en tiempo real | Microsoft Azure" 
    description="Obtenga información sobre cómo puede interactuar un conjunto de servicios de Azure para habilitar el procesamiento de eventos en tiempo real y análisis." 
    keywords="procesamiento en tiempo real, el proceso de evento de arquitectura de referencia"
    services="stream-analytics,event-hubs,storage,sql-database" 
    documentationCenter="" 
    authors="jeffstokes72" 
    manager="jhubbard" 
    editor=""/>

<tags 
    ms.service="stream-analytics" 
    ms.workload="big-data" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/26/2016" 
    ms.author="jeffstok"/>

# <a name="reference-architecture-real-time-event-processing-with-microsoft-azure-stream-analytics"></a>Arquitectura de referencia: procesamiento de análisis de secuencia de Microsoft Azure de eventos en tiempo real

La arquitectura de referencia de evento en tiempo real de procesamiento de análisis de secuencia de Azure está pensada para proporcionar una guía genérica para implementar una plataforma en tiempo real como una solución de procesamiento de secuencia de servicio (PaaS) con Microsoft Azure.

## <a name="summary"></a>Resumen

Tradicionalmente, las soluciones de análisis se han basado en capacidades como ETL (extraer, transformar, cargar) y el almacenamiento de datos, donde se almacenan los datos antes del análisis. Requisitos del cambio, incluyendo más datos entrantes rápidamente, presión este modelo existente al límite. La capacidad de analizar datos en mover secuencias antes de almacenamiento es una solución y, a continuación, aunque no es una nueva función, el enfoque no se han adoptado ampliamente a través de todos los sectores verticales de la industria. 

Microsoft Azure proporciona un amplio catálogo de tecnologías de análisis que pueden admitir una matriz de solución diferentes escenarios y los requisitos. Seleccionar qué servicios Azure para implementar una solución de llevar a cabo, puede ser un desafío dado la variedad de ofertas. Este artículo está diseñado para describir las capacidades y la interoperabilidad de los distintos servicios de Azure que son compatibles con una solución de transmisión de eventos. También se explican algunos de los casos en que los clientes pueden beneficiarse de este tipo de solución.

## <a name="contents"></a>Contenido

- Resumen ejecutivo
- Introducción a análisis en tiempo real
- Propuesta de valor de datos en tiempo real en Azure
- Escenarios comunes para el análisis en tiempo real
- Arquitectura y componentes
    - Orígenes de datos
    - Capa de integración de datos
    - Capa de análisis en tiempo real
    - Capa de almacenamiento de datos
    - Presentación / consumo de capas
- Conclusión

**Autor:** Centro de información de datos de Charles Feddersen, arquitecto de soluciones de excelencia, Microsoft Corporation

**Publicado:** Enero de 2015

**Revisión:** 1.0

**Descargar:** [Procesamiento de análisis de Microsoft Azure secuencia de eventos en tiempo real](http://download.microsoft.com/download/6/2/3/623924DE-B083-4561-9624-C1AB62B5F82B/real-time-event-processing-with-microsoft-azure-stream-analytics.pdf)


## <a name="get-help"></a>Obtener ayuda
Para obtener más ayuda, pruebe nuestro [foro de análisis de secuencia de Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Pasos siguientes

- [Introducción a análisis de secuencia de Azure](stream-analytics-introduction.md)
- [Empezar a usar el análisis de secuencia de Azure](stream-analytics-get-started.md)
- [Escala trabajos de análisis de secuencia de Azure](stream-analytics-scale-jobs.md)
- [Referencia del lenguaje de consulta de análisis de secuencia de Azure](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Referencia de la API de REST de administración de secuencia de Azure análisis](https://msdn.microsoft.com/library/azure/dn835031.aspx)

 
