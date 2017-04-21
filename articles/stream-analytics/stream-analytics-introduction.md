<properties 
    pageTitle="Introducción a análisis de secuencia | Microsoft Azure" 
    description="Obtenga información sobre el análisis de secuencia de un servicio administrado que le ayuda a analizar datos de transmisión de Internet de cosas (IoT) en tiempo real." 
    keywords="análisis como un servicio administrado servicios, procesamiento de secuencia, transmisión por secuencias análisis, ¿qué es el análisis de secuencia"
    services="stream-analytics" 
    documentationCenter="" 
    authors="jeffstokes72" 
    manager="jhubbard" 
    editor="cgronlun"/>

<tags 
    ms.service="stream-analytics" 
    ms.devlang="na" 
    ms.topic="get-started-article" 
    ms.tgt_pltfrm="na" 
    ms.workload="data-services" 
    ms.date="09/26/2016" 
    ms.author="jeffstok"/>


# <a name="what-is-stream-analytics"></a>¿Qué es el análisis de secuencia?

Análisis de secuencia de Azure es un motor de procesamiento de eventos en tiempo real totalmente administrado y rentable que ayuda a desbloquear perspectivas detallados de datos. Análisis de secuencia facilita la configurar cálculos de análisis en tiempo real en datos transmisión de dispositivos, sensores, sitios web, medios sociales, aplicaciones, sistemas de infraestructura y más.

Con unos pocos clics en el portal de Azure, puede crear una tarea de análisis de secuencia que especifica el origen de entrada de los datos de transmisión por secuencias, el receptor de salida de los resultados de su trabajo y una transformación de datos se expresa en un lenguaje similar a SQL. Puede supervisar y ajustar la escala y la velocidad de su trabajo en el portal de Azure escalar desde unos cuantos kilobytes hasta un gigabyte o más de eventos procesados por segundo.

Análisis de secuencia aprovecha años de trabajo de Microsoft Research de desarrollo ajustan transmisión motores de procesamiento de plazos, así como integraciones de idioma para especificaciones intuitivas de tales.

## <a name="what-can-i-use-stream-analytics-for"></a>¿Qué puedo usar el análisis de secuencia de?
Grandes cantidades de datos fluyen a alta velocidad durante la conexión hoy. Las organizaciones que pueden procesar y actuar en estos datos en tiempo real streaming considerablemente pueden mejorar la eficacia y diferenciarse en el mercado. Escenarios de análisis de transmisiones en tiempo real se encuentra en todos los sectores: análisis de bolsa personalizada y en tiempo real y alertas ofrecidas servicios financieros empresas; detección de fraudes en tiempo real; Servicios de protección de datos y de identidad; recopilación confiable y análisis de datos generados por interruptores y sensores incrustan en objetos físicos (Internet de cosas o IoT); análisis de secuencia de clics Web; y aplicaciones de administración (CRM) de relación de cliente emitir alertas cuando está degradado la experiencia del cliente dentro de un período de tiempo. Empresas que están buscando la manera más flexible, confiable y rentable realizar dicha análisis de datos de secuencia de eventos en tiempo real para tener éxito en el mundo empresarial moderna competitivo.

## <a name="key-capabilities-and-benefits"></a>Ventajas y capacidades clave
-   **Facilidad de uso:** Análisis de secuencia es compatible con un modelo de consultas sencillas y descriptiva para describir las transformaciones. Para optimizar para facilitar su uso, análisis de secuencia usa una variante T SQL y elimina la necesidad de los clientes a atender las complejidades técnicas de sistemas de procesamiento de secuencia. Mediante el [análisis de secuencia de lenguaje de consulta](https://msdn.microsoft.com/library/azure/dn834998.aspx) en el editor de consultas en el explorador, obtendrá IntelliSense Autocompletar para ayudarle a puede de forma rápida y fácilmente implementar consultas de serie de tiempo, incluyendo combinaciones basado en temporal, agregados con ventanas, filtros temporales y otras operaciones comunes como combinaciones, agregados, proyecciones y filtros. Además, la prueba con un archivo de datos de ejemplo de consultas en el navegador permite desarrollo iterativo rápido.  

-   **Escalabilidad:** Análisis de secuencia es capaz de administración de rendimiento de evento alto de 1GB por segundo. Integración con [Hubs de evento de Azure](https://azure.microsoft.com/services/event-hubs/) y [Azure IoT Hubs](https://azure.microsoft.com/services/iot-hub/) permitir que recopile millones de eventos por segundo procedente de los dispositivos conectados, clickstreams, la solución y archivos de registro, entre otros. Para lograr esto, análisis de secuencia aprovecha la capacidad de partición de Hubs de evento, que puede producir 1 MB/s por partición. Los usuarios pueden dividir el cálculo en una serie de pasos lógicos dentro de la definición de consulta, cada una con la capacidad de ser más particiones para aumentar la escalabilidad.  

-   **Confiabilidad, capacidad de repetición y recuperación rápida:** Un servicio administrado en la nube, análisis de secuencia ayuda a evitar la pérdida de datos y proporciona continuidad del negocio en caso de errores mediante capacidades de recuperación integradas. Con la capacidad de mantener internamente el estado, el servicio proporciona resultados reproducibles asegurarse de que es posible archivar eventos y volver a aplicar procesamiento en el futuro, siempre obtener los mismos resultados. Esto permite a los clientes ir hacia atrás e investigar cálculos al realizar análisis de causa de origen, análisis de hipótesis, etcetera.  

-   **Económico:** Como un servicio de nube análisis de secuencia está optimizado para proporcionar a los usuarios un costo muy bajo para en marcha y mantener soluciones de análisis en tiempo real. El servicio se creó para que pagar mientras avanza basándose en el uso de la unidad de transmisión y la cantidad de datos procesados por el sistema. Uso se deriva según el volumen de eventos procesados y la cantidad de energía de cálculo que se aprovisione dentro del clúster para controlar los trabajos de análisis de secuencia respectivos.  

-   **Hacen referencia a datos:** Análisis de secuencia proporciona a los usuarios la capacidad de especificar y usar los datos de referencia. Esto puede deberse a históricos datos o simplemente no transmisión que cambia con menos frecuencia con el tiempo. El sistema simplifica el uso de datos de referencia se traten como cualquier otra secuencia de evento entrantes a unirse con otras secuencias de evento ingeridos en tiempo real para realizar las transformaciones.  

-   **Funciones definidas por el usuario:** Análisis de la secuencia tiene integración con Azure el aprendizaje para definir llamadas de función en el servicio de aprendizaje de equipo como parte de una consulta de análisis de la secuencia. Esto amplía las capacidades de análisis de secuencia para aprovechar las soluciones existentes de aprendizaje del equipo de Azure. Para obtener más información sobre esto, consulte el [tutorial de integración de aprendizaje del equipo](stream-analytics-machine-learning-integration-tutorial.md).

-   **Conectividad:** Análisis de secuencia se conecta directamente a Hubs de evento de Azure y Azure IoT Hubs de recopilación de secuencia y el servicio de blobs de Windows Azure para recopilar datos históricos. Resultados pueden escribirse de análisis de secuencia de Azure almacenamiento de Blobs o tablas, base de datos de SQL Azure, Azure datos lago Stores, DocumentDB, Hubs de evento, temas de Bus de servicio de Azure o colas y Power BI, donde se puede, a continuación, visualizarse, aún más procesado por los flujos de trabajo, utilizado en el análisis de lote a través de [HDInsight de Azure](https://azure.microsoft.com/services/hdinsight/) o volver a procesar como una serie de eventos. Cuando se utiliza el evento Hubs es posible redactar varios análisis de secuencia junto con los orígenes de datos y los motores de procesamiento sin perder la naturaleza transmisiones de los cálculos.  

## <a name="get-help"></a>Obtener ayuda
Para obtener más ayuda, pruebe nuestro [foro de análisis de secuencia de Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Pasos siguientes
Ya conoce a análisis de secuencia, un servicio de transmisión de análisis de datos de Internet de lo administrado. Para obtener más información sobre este servicio, consulte:

- [Empezar a usar el análisis de secuencia de Azure](stream-analytics-get-started.md)
- [Escala trabajos de análisis de secuencia de Azure](stream-analytics-scale-jobs.md)
- [Referencia del lenguaje de consulta de análisis de secuencia de Azure](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Referencia de la API de REST de administración de secuencia de Azure análisis](https://msdn.microsoft.com/library/azure/dn835031.aspx)

