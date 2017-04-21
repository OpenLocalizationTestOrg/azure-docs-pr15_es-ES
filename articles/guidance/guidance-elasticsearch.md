
<properties
   pageTitle="Elasticsearch instrucciones Azure | Microsoft Azure"
   description="Elasticsearch instrucciones Azure."
   services=""
   documentationCenter="na"
   authors="dragon119"
   manager="bennage"
   editor=""
   tags=""/>

<tags
   ms.service="guidance"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/22/2016"
   ms.author="masashin"/>

# <a name="elasticsearch-on-azure-guidance"></a>Elasticsearch instrucciones de Azure 

[AZURE.INCLUDE [pnp-header](../../includes/guidance-pnp-header-include.md)]

Elasticsearch es un motor de búsqueda muy scalable de código abierto y base de datos. Es útil para las situaciones que requieren descubrimiento de la información contenida en grandes conjuntos de datos y análisis rápido. Esta guía se centra en algunos aspectos clave para tener en cuenta al diseñar un clúster de Elasticsearch, con un enfoque en formas para optimizar y probar su configuración.

> [AZURE.NOTE]Esta guía supone algunas familiaridad básica con Elasticsearch. Para obtener más información, visite el [sitio Web de Elasticsearch](https://www.elastic.co/products/elasticsearch). 

- **[Ejecutar Elasticsearch en Azure][]** proporciona una breve introducción a la estructura general de Elasticsearch y describe cómo implementar un clúster de Elasticsearch con Azure. 
- **[Ajuste del rendimiento de recopilación de datos para Elasticsearch en Azure][]** , se describe la implementación de un clúster de Elasticsearch y se proporciona un análisis detallado de las distintas opciones de configuración para tener en cuenta cuando se esperaba una alta velocidad de recopilación de datos.
- **[Agregación de datos de optimización y rendimiento de la consulta para Elasticsearch en Azure][]** proporciona un análisis detallado de las opciones que deben contemplarse al decidir cómo optimizar su sistema de búsqueda y consulta de rendimiento.
- **[Configuración resistencia y recuperación en Elasticsearch en Azure][]** , se describen algunas características importantes de un clúster de Elasticsearch que pueden ayudar a minimizar las posibilidades de pérdida de datos y tiempos de recuperación de datos extendido.
- **[Crear un entorno de prueba de rendimiento para Elasticsearch en Azure][]** , se describe cómo configurar un entorno de prueba de la recopilación de datos de rendimiento y las cargas de trabajo de la consulta en un clúster de Elasticsearch. 
- **[Implementar un JMeter probar el plan para Elasticsearch][]** resume implementadas mediante planes de pruebas JMeter junto con el código de Java incorporada como una prueba de JUnit para realizar tareas como cargar datos en el clúster Elasticsearch ejecutar pruebas de rendimiento.
- **[Implementar un muestrario JMeter JUnit para probar el rendimiento de Elasticsearch][]** , se describe cómo crear y usar un muestrario de JUnit que se puede generar y cargar datos en un clúster de Elasticsearch. Esto proporciona un enfoque muy flexible para pruebas de carga que puede generar grandes cantidades de datos de prueba sin dependiendo de los archivos de datos externos. 
- **[Ejecutar las pruebas automatizadas de la resistencia Elasticsearch][]** resume cómo ejecutar las pruebas de resistencia utilizadas en esta serie. 
- **[Ejecutar las pruebas de rendimiento Elasticsearch automatizadas][]** resume cómo ejecutar las pruebas de rendimiento que se usan en esta serie.


[Ejecuta Elasticsearch en Azure]: guidance-elasticsearch-running-on-azure.md
[Ajuste del rendimiento de recopilación de datos para Elasticsearch en Azure]: guidance-elasticsearch-tuning-data-ingestion-performance.md
[Crear un entorno de prueba para Elasticsearch en Azure de rendimiento]: guidance-elasticsearch-creating-performance-testing-environment.md
[Implementar un Plan de pruebas de JMeter para Elasticsearch]: guidance-elasticsearch-implementing-jmeter-test-plan.md
[Implementar un muestrario JMeter JUnit para realizar pruebas de rendimiento Elasticsearch]: guidance-elasticsearch-deploying-jmeter-junit-sampler.md
[Ajustar el rendimiento de consulta para Elasticsearch en Azure y agregación de datos]: guidance-elasticsearch-tuning-data-aggregation-and-query-performance.md
[Configuración resistencia y recuperación en Elasticsearch en Azure]: guidance-elasticsearch-configuring-resilience-and-recovery.md
[Las pruebas de resistencia Elasticsearch automatizado]: guidance-elasticsearch-running-automated-resilience-tests.md
[Las pruebas de rendimiento Elasticsearch automatizado]: guidance-elasticsearch-running-automated-performance-tests.md
