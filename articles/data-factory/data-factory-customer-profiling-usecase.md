<properties 
    pageTitle="Utilizar caso - perfiles de cliente" 
    description="Obtenga información sobre cómo se utiliza el generador de datos de Azure para crear un flujo de controlados por datos (canalización) para los clientes de juegos de perfil." 
    services="data-factory" 
    documentationCenter="" 
    authors="sharonlo101" 
    manager="jhubbard" 
    editor="monicar"/>

<tags 
    ms.service="data-factory" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/06/2016" 
    ms.author="shlo"/>

# <a name="use-case---customer-profiling"></a>Utilizar caso - perfiles de cliente

Generador de datos de Azure es uno de los muchos servicios que se utiliza para implementar el conjunto de inteligencia Cortana de aceleradores de soluciones.  Para obtener más información sobre inteligencia Cortana, visite el [Conjunto de aplicaciones de inteligencia de Cortana](http://www.microsoft.com/cortanaanalytics). En este documento, describimos un caso de uso sencillo para ayudarle a empezar a trabajar con información sobre cómo generador de datos de Azure puede resolver los problemas más comunes de análisis.

Todo lo que necesita para obtener acceso y pruebe este caso de uso simple es una [suscripción de Azure](https://azure.microsoft.com/pricing/free-trial/).  Puede implementar un ejemplo que implementa este caso de uso siguiendo los pasos descritos en el artículo [ejemplos](data-factory-samples.md) .

## <a name="scenario"></a>Escenario

Contoso es una empresa de juegos que crea juegos para varias plataformas: juegos consolas, dispositivos portátiles y equipos personales (PC). Como reproductores estos jugar, se produce grandes volúmenes de datos de registro que realiza un seguimiento de los patrones de uso, estilo de juegos y preferencias del usuario.  Cuando se combina con demográfica, regional y datos de productos, Contoso puede llevar a cabo análisis para guiar al usuario sobre cómo mejorar la experiencia de los jugadores y compras de destino para las actualizaciones y del juego. 

Objetivo de Contoso es identificar oportunidades de vender arriba-venta cruzada basadas en el historial de juegos de sus reproductores y agregar características atractivas crecimiento del negocio unidad y proporcionar una mejor experiencia a los clientes. En este caso de uso, usamos una empresa de juegos como un ejemplo de un negocio. La empresa desea optimizar sus juegos en función de comportamiento los jugadores. Estos principios se aplican a cualquier empresa que desee contratar a sus clientes alrededor de sus productos y servicios y mejorar la experiencia de sus clientes.

## <a name="challenges"></a>Desafíos


## <a name="solution-overview"></a>Información general sobre soluciones

En este caso de uso simple se puede utilizar como un ejemplo de cómo puede usar el generador de datos de Azure para recopilar, preparar, transformar, analizar y publicar los datos.

![Flujo de trabajo to-end](./media/data-factory-customer-profiling-usecase/EndToEndWorkflow.png)

Esta ilustración muestra cómo las canalizaciones de datos aparecen en el portal de Azure después de que se hayan implementado.

1.  La **PartitionGameLogsPipeline** lee los eventos de juegos sin formato de almacenamiento de blobs y crea particiones en función de año, mes y día.
2.  La **EnrichGameLogsPipeline** une divididos eventos juego con datos de referencia del código geográfico y enriquece los datos mediante la asignación de direcciones IP a las ubicaciones de geo correspondientes.
3.  La canalización de **AnalyzeMarketingCampaignPipeline** utiliza los datos enriquecidos y procesos con los datos de publicidad para crear el resultado final que contiene la eficacia de la campaña de marketing.

En este ejemplo, se utiliza el generador de datos para coordinar actividades que copian datos de entrada, transformar y proceso de los datos y enviar los datos a una base de datos de SQL Azure finales.  También puede visualizar la red de datos canalizaciones, administrarlas y supervisar su estado de la interfaz de usuario.

## <a name="benefits"></a>Ventajas

Al optimizar su análisis de perfil de usuario y alinear con los objetivos empresariales, empresa de juegos es capaz de recopilar los patrones de uso y analizar rápidamente la eficacia de sus campañas de marketing.




