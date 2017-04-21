<properties 
    pageTitle="Guía de solución de análisis de telemetría de vehículo | Microsoft Azure" 
    description="Usar las capacidades de inteligencia de Cortana para obtener perspectivas en tiempo real y predictivas en estado de vehículo y conducción hábitos." 
    services="machine-learning" 
    documentationCenter="" 
    authors="bradsev" 
    manager="jhubbard" 
    editor="cgronlun" />

<tags 
    ms.service="machine-learning" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/12/2016" 
    ms.author="bradsev" />


# <a name="vehicle-telemetry-analytics-solution-playbook"></a>Guía de solución de análisis de telemetría de vehículo

Vínculos de este **menú** los capítulos en esta guía. 

[AZURE.INCLUDE [cap-vehicle-telemetry-playbook-selector](../../includes/cap-vehicle-telemetry-playbook-selector.md)]

## <a name="overview"></a>Información general
Equipos superior se han movido fuera de la práctica y están ahora estacionados en nuestra garaje! Estos automóviles punteras contienen un gran número de sensores, que le da la capacidad de realizar un seguimiento y supervisar millones de eventos cada segundo. Esperamos que por 2020, la mayoría de estos coches habrá ha conectado a internet. ¡Imagine puntear en esta gran cantidad de datos para proporcionar mejor en seguridad de clase, la confiabilidad y la experiencia de conducción! Microsoft ha realizado este imaginar una realidad con inteligencia Cortana.

Inteligencia de Cortana de Microsoft es un totalmente administrado big data y el conjunto de funciones analíticas avanzadas que le permiten transformar los datos en acción inteligente. Queremos presentan la plantilla de solución de análisis de telemetría de Cortana inteligencia vehículo. Esta solución muestra cómo concesiones de coches, fabricantes de automóviles y las empresas de seguros pueden usar las capacidades de inteligencia de Cortana para obtener en tiempo real y perspectivas predictivas en estado de vehículo y realizar la transformación. 

La solución se implementa como un [patrón de arquitectura de lambda](https://en.wikipedia.org/wiki/Lambda_architecture) que muestra todo el potencial de la plataforma de inteligencia de Cortana para en tiempo real y el proceso por lotes. Solución: 

- Proporciona un simulador telemáticas de vehículo
- aprovecha Hubs de evento para recopila millones de eventos de telemetría de vehículo simulada en Azure 
- utiliza el análisis de secuencia para obtener perspectivas en tiempo real sobre el mantenimiento de vehículo
-  conserva los datos en un almacenamiento a largo plazo para análisis de lote más rico. 
- aprovecha de aprendizaje para la detección de anomalías en tiempo real y por lotes de procesamiento para obtener recomendaciones predictivas.
- aprovecha HDInsight para transformar datos en escala y generador de datos para controlar orquestación, la programación, la administración de recursos y la supervisión de la canalización de proceso por lotes 
- Proporciona un panel enriquecido de esta solución para datos en tiempo real y visualizaciones de análisis predictiva con Power BI

## <a name="architecture"></a>Arquitectura

![](./media/cortana-analytics-playbook-vehicle-telemetry/fig1-vehicle-telemetry-annalytics-solution-architecture.png)
*Figura 1: arquitectura de soluciones de análisis de telemetría de vehículo*

Esta solución incluye los siguientes **componentes de inteligencia de Cortana** y presenta su integración de principio a fin


- **Evento Hubs** para recopila millones de eventos de telemetría vehículo en Azure.
- **Análisis de secuencia** para obtener perspectivas en tiempo real sobre el mantenimiento de vehículo y continúa esos datos en un almacenamiento a largo plazo para análisis de lote más rico.
- **Aprendizaje** para la detección de anomalías en tiempo real y el proceso por lotes para obtener recomendaciones predictivas.
- **HDInsight** se aprovecha para transformar datos a escala
- **Generador de datos** controla orquestación, programación, administración de recursos y el seguimiento de la canalización de proceso por lotes.
- **Power BI** le ofrece esta solución un panel enriquecido para datos en tiempo real y visualizaciones de análisis predictiva.

Esta solución tiene acceso a diferentes dos **orígenes de datos**: 

- **Simulación de vehículo señales y diagnósticos**: un simulador de telemáticas vehículo emite información de diagnóstico y señales que correspondan al estado de vehículo y el patrón de conducción en un momento determinado en el tiempo. 
- **Catálogo de vehículo**: un conjunto de datos de referencia que contiene un número de bastidor asignación modelo.
