
<properties
   pageTitle="Guía de Azure | patrones y prácticas | Microsoft Azure"
   description="Procedimientos recomendados y orientación para Azure"
   services=""
   documentationCenter="na"
   authors="bennage"
   manager="marksou"
   editor=""
   tags=""/>

<tags
   ms.service="guidance"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/17/2016"
   ms.author="christb"/>

# <a name="azure-guidance"></a>Guía de Azure

[AZURE.INCLUDE [pnp-header](../../includes/guidance-pnp-header-include.md)]

El equipo de patrones y prácticas de Microsoft es parte del equipo de cliente de Azure consultivo. Nuestro objetivo es ayudar a los desarrolladores, arquitectos y profesionales de TI sea correcta en la plataforma de Microsoft Azure. Nos desarrollar orientación que muestra los procedimientos recomendados para crear soluciones de nube en Azure.

## <a name="checklists"></a>Listas de comprobación

Estas listas son una referencia rápida para revisar los aspectos fundamentales de disponibilidad y escalabilidad. 

- [Lista de comprobación de disponibilidad][AvailabilityChecklist] 

    Un resumen de los procedimientos recomendados para garantizar la disponibilidad.

- [Lista de comprobación de escalabilidad][ScalabilityChecklist]

    Un resumen de los procedimientos recomendados para diseñar e implementar servicios scalable y tratamiento de administración de datos.

## <a name="best-practices-articles"></a>Artículos de prácticas recomendadas

Estos artículos proporcionan una descripción detallada de los conceptos importantes asociados normalmente a la nube informática. 

- [Diseño de la API][APIDesign] 

    Explicación de los problemas de diseño para tener en cuenta al diseñar un sitio web API.

- [Implementación de la API][APIImplementation] 

    Un conjunto de prácticas recomendadas para implementar y publicar un sitio web API.

- [Guía de seguridad de la API](https://github.com/mspnp/azure-guidance/blob/master/API-security.md) 

    Explicación de los problemas de autenticación y la autorización (por ejemplo, tipos de token, protocolos de autorización, flujos de autorización y eliminación de amenazas).

- [Guía de ajuste automático][AutoscalingGuidance] 

    Un resumen de las consideraciones de escala soluciones sin necesidad de intervención manual.

- [Guía de trabajos de fondo][BackgroundJobsGuidance] 

    Descripción de las opciones disponibles y los procedimientos recomendados para implementar las tareas que deben realizarse en segundo plano, independientemente de cualquier plano u operaciones interactivas.

- [Guía de red de entrega (CDN) contenido][CDNGuidance] 

    Instrucciones generales y práctica recomendada para usar la CDN para reducir la carga en las aplicaciones y maximizar la disponibilidad y rendimiento.

- [Guía de almacenamiento en caché][CachingGuidance] 

    Resumen de cómo usar el almacenamiento en caché para mejorar el rendimiento y escalabilidad de un sistema.

- [Guía de particiones de datos][DataPartitioningGuidance]

    Reducir los conflictos de estrategias que puede usar para dividir los datos para mejorar la escalabilidad y optimizar el rendimiento.

- [Guía de supervisión y diagnóstico][MonitoringandDiagnosticsGuidance] 

    Consejos sobre cómo los usuarios utilizan el sistema de seguimiento hacer un seguimiento del uso de recursos y, en general, supervisar el mantenimiento y el rendimiento del sistema.

- [Convenciones de nomenclatura recomendadas][naming-conventions] 

    Se recomienda convenciones de nomenclatura para los recursos de Azure.

- [Vuelva a intentar directrices generales][RetryGeneralGuidance] 

    Explicación de los conceptos generales para el tratamiento de errores transitorias.

- [Vuelva a intentar obtener instrucciones específicas del servicio][RetryServiceSpecificGuidance]

    Resumen de características de intentos de muchos de los servicios de Azure, incluida información para ayudarle a usar, adaptar o ampliar el mecanismo de reintento para ese servicio.

## <a name="scenario-guides"></a>Guías de escenario

- [Ejecuta Elasticsearch en Azure][elasticsearch] 
    
    Elasticsearch es un motor de búsqueda muy scalable de código abierto y base de datos. Es adecuado para situaciones que requieren descubrimiento de la información contenida en grandes conjuntos de datos y análisis rápido. Esta guía se centra en algunos aspectos clave para tener en cuenta al diseñar un clúster de Elasticsearch.

- [Administración de identidades para aplicaciones multiempresa][identity-multitenant] 
    
    Varias empresas es una arquitectura donde varios inquilinos de compartan una aplicación, pero están aislados entre sí. Esta guía le muestra cómo administro las identidades de usuario en una aplicación de multiempresa con [Azure Active Directory] [ AzureAD] para controlar el inicio de sesión y la autenticación.
    
- [Desarrollo de soluciones de datos grande](https://msdn.microsoft.com/library/dn749874.aspx)

    Esta guía analiza el uso de HDInsight en escenarios como exploración iterativo, como un almacén de datos para procesos ETL y la integración en los sistemas de BI existentes. También incluye orientación sobre la descripción de los conceptos de datos grandes, planear y diseñar las soluciones datos grande y la implementación de estas soluciones.
    
## <a name="patterns"></a>Tramas

- [Patrones de diseño de la nube: Guía de arquitectura de la normativa para las aplicaciones de nube](https://msdn.microsoft.com/library/dn568099.aspx)

    Patrones de diseño de la nube es una biblioteca de temas relacionados orientación y patrones de diseño. Quiere transmitir el beneficio de la aplicación de patrones que muestra cómo cada fragmento adapta arquitecturas de aplicación de nube.
    
- [Optimizar el rendimiento para aplicaciones de nube](https://github.com/mspnp/performance-optimization)

    Esta guía es una exploración de patrones comunes que impiden aplicaciones de escala de carga. Incluye ejemplos que muestran ocho patrones y un [manual de análisis de rendimiento](https://github.com/mspnp/performance-optimization/blob/master/Performance-Analysis-Primer.md) y una guía para [evaluar el rendimiento de métricas clave](https://github.com/mspnp/performance-optimization/blob/master/Assessing-System-Performance-Against-KPI.md).

## <a name="reference-architectures"></a>Arquitecturas de referencia

Nuestras arquitecturas de referencia están organizadas por escenario.
Cada arquitectura individual ofrece prácticas recomendadas y los pasos indicados y un componente ejecutable que incluye las recomendaciones.

La biblioteca actual de arquitecturas de referencia está disponible en [http://aka.ms/architecture](http://aka.ms/architecture).

## <a name="resiliency-guidance"></a>Orientación de la resistencia

Estos temas describen cómo diseñar aplicaciones que se adaptan a error en un entorno de nube distribuido.   

- [Información general sobre resistencia][ResiliencyOvervew]

     Cómo crear aplicaciones de la plataforma Windows Azure que pueden recuperar de errores y siga funcionando. Describe un enfoque estructurado para lograr resistencia, desde el diseño hasta la implementación, implementación y operaciones.

- [Lista de comprobación de la resistencia][resiliency-checklist]

    Una lista de comprobación de recomendaciones que le ayudará a planear una gran variedad de modos de error podría ocurrir.

- [Análisis de modo de errores][resiliency-fma] 

    Análisis de modo de error (FMA) es un proceso para la creación de la resistencia en un sistema, identificando puntos posibles errores. Como punto de partida para el proceso FMA, este artículo contiene un catálogo de posibles modos de error y sus soluciones. 

<!-- links -->

[AzureAD]: https://azure.microsoft.com/documentation/services/active-directory/

[PerformanceOptimization]: https://github.com/mspnp/performance-optimization

[APIDesign]: ../best-practices-api-design.md
[APIImplementation]: ../best-practices-api-implementation.md
[AutoscalingGuidance]: ../best-practices-auto-scaling.md
[BackgroundJobsGuidance]: ../best-practices-background-jobs.md
[CDNGuidance]: ../best-practices-cdn.md
[CachingGuidance]: ../best-practices-caching.md
[DataPartitioningGuidance]: ../best-practices-data-partitioning.md
[MonitoringandDiagnosticsGuidance]: ../best-practices-monitoring.md
[RetryGeneralGuidance]: ../best-practices-retry-general.md
[RetryServiceSpecificGuidance]: ../best-practices-retry-service-specific.md
[RetryPolicies]: Retry-Policies.md
[ScalabilityChecklist]: ../best-practices-scalability-checklist.md
[AvailabilityChecklist]: ../best-practices-availability-checklist.md
[naming-conventions]: guidance-naming-conventions.md

<!-- guidance projects -->
[elasticsearch]: guidance-elasticsearch.md
[identity-multitenant]: guidance-multitenant-identity.md

<!-- reference architectures -->
[ref-arch-single-vm-windows]: guidance-compute-single-vm.md
[ref-arch-single-vm-linux]: guidance-compute-single-vm-linux.md
[ref-arch-multi-vm]: guidance-compute-multi-vm.md
[ref-arch-3-tier]: guidance-compute-3-tier-vm.md
[ref-arch-n-tier-windows]: guidance-compute-n-tier-vm.md
[ref-arch-n-tier-linux]: guidance-compute-n-tier-vm-linux.md
[ref-arch-multi-dc-windows]: guidance-compute-multiple-datacenters.md
[ref-arch-multi-dc-linux]: guidance-compute-multiple-datacenters-linux.md

<!-- resiliency -->
[resiliency-fma]: guidance-resiliency-failure-mode-analysis.md
[resiliency-checklist]: guidance-resiliency-checklist.md
[ResiliencyOvervew]: guidance-resiliency-overview.md

