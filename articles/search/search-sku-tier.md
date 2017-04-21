<properties
    pageTitle="Elija un SKU o nivel de precios para la búsqueda de Azure | Microsoft Azure"
    description="Búsqueda de Azure pueden aprovisionar en estos SKU: libre, básico y estándar, donde estándar está disponible en diversas configuraciones de recursos y niveles de capacidad."
    services="search"
    documentationCenter=""
    authors="HeidiSteen"
    manager="jhubbard"
    editor=""
    tags="azure-portal"/>

<tags
    ms.service="search"
    ms.devlang="NA"
    ms.workload="search"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.date="10/24/2016"
    ms.author="heidist"/>

# <a name="choose-a-sku-or-pricing-tier-for-azure-search"></a>Elija un SKU o nivel de precios para la búsqueda de Azure

En búsqueda de Azure, un [servicio se aprovisiona](search-create-service-portal.md) en un nivel de precios específico o SKU. Opciones de incluyen **libre**, **básica**o **estándar**, donde **estándar** está disponible en varias configuraciones y capacidades. 

El propósito de este artículo es para que pueda elegir un nivel. Si la capacidad de un nivel resulta para ser demasiado bajo, debe aprovisionar un nuevo servicio en el nivel superior y, a continuación, volver a cargar los índices. No hay ninguna actualización en lugar del mismo servicio desde una SKU a otro. 

> [AZURE.NOTE] Después de elegir un nivel y [aprovisionar un servicio de búsqueda](search-create-service-portal.md), puede aumentar réplica y partición cuenta dentro del servicio. Para obtener instrucciones, vea [niveles de recursos de escala de consulta y las cargas de trabajo de indización](search-capacity-planning.md).

## <a name="how-to-approach-a-pricing-tier-decision"></a>Cómo enfocar una decisión de nivel de precios

En búsqueda de Azure, el nivel determina la capacidad, no la disponibilidad de características. En general, características están disponibles en todos los niveles, incluidas las características de vista previa. La única excepción no es compatible con indizadores en HD S3.

> [AZURE.TIP] Le recomendamos que siempre aprovisionar un servicio **gratuito** (uno por suscripción sin expiración) para que su disponible para proyectos de atenuado. Utilizar el servicio **gratuito** de prueba y evaluación; crear un segundo servicio facturable en el nivel **básico** o **estándar** de producción o cargas de trabajo de prueba más grandes.

Capacidad y costos de ejecuta el servicio de ir de la mano. Información en este artículo puede ayudarle a decidir qué SKU ofrece el equilibrio adecuado, pero para cualquiera de los que sea útil, necesita al menos aproximadas estimaciones de lo siguiente:

- Número y el tamaño de los índices que tiene previsto crear
- Número y el tamaño de los documentos al cargar
- Una idea de volumen de consulta, en términos de consultas por segundo (QPS)

Número y el tamaño son importantes porque se alcance el límite máximo a través de un límite máximo en el número de documentos en un servicio o índices o en recursos (almacenamiento o réplicas) utilizados por el servicio. El límite real de su servicio es lo que se usa primero: recursos u objetos.

Con las estimaciones de mano, los pasos siguientes deben simplificar el proceso:

- **Paso 1** Revise las descripciones de SKU siguientes para obtener información sobre las opciones disponibles.
- **Paso 2** Responda a las preguntas siguientes para llegar a una decisión preliminar.
- **Paso 3** Finalizar su decisión revisando límites de almacenamiento y precios.

## <a name="sku-descriptions"></a>Descripciones de SKU

En la tabla siguiente proporciona descripciones de cada nivel. 

Nivel|Escenarios principales
----|-----------------
**Liberar**|Un servicio compartido, sin cargo, utilizado para evaluación, investigaciones o pequeñas cargas de trabajo. Dado que se comparte con otros suscriptores, varía según quién más está usando el servicio de rendimiento de la consulta y la indización. La capacidad es pequeña (50 MB o 3 índices con el 10.000 documentos cada uno).
**Básico**|Cargas de trabajo de producción pequeña en hardware dedicado. Altamente disponible. La capacidad es hasta 3 réplicas y 1 partición (2 GB).
**S1**|1 estándar admite combinaciones flexibles de particiones (12) y réplicas (12), utilizadas para cargas de trabajo de producción medio en hardware dedicado. Puede asignar particiones y réplicas combinaciones compatibles con un número máximo de unidades de búsqueda facturable 36. En este nivel, las particiones son 25 GB y QPS es aproximadamente 15 consultas por segundo.
**S2**|Estándar 2 ejecuta cargas de trabajo de producción mayores con las mismas unidades de 36 búsqueda S1 pero con réplicas y particiones mayores de tamaño. En este nivel, las particiones son 100 GB y QPS es aproximadamente 60 consultas por segundo.
**S3**|3 estándar se ejecuta cargas de trabajo de producción proporcionalmente mayores de sistemas de extremo superior en configuraciones de hasta 12 particiones o 12 réplicas unidades de búsqueda en 36. En este nivel, las particiones son 200 GB y QPS es más de 60 consultas por segundo. 
**S3 HD**|3 estándar de alta densidad está diseñado para una gran cantidad de índices más pequeños. Puede tener hasta 3 particiones, en 200 GB. QPS es más de 60 consultas por segundo. 

> [AZURE.NOTE] Valores máximos de réplica y partición se cargarán los como unidades de búsqueda (unidad 36 máximo por servicio), que impone un límite inferior eficaz que lo que indica el número máximo de valor nominal. Por ejemplo, para usar el número máximo de 12 réplicas, podría tener como máximo 3 particiones (3 * 12 = 36 unidades). Asimismo, para usar particiones máximas, reduzca réplicas a 3. Para un gráfico en combinaciones permitidos, vea [niveles de escala de recursos de consulta y las cargas de trabajo de indización de búsqueda de Azure](search-capacity-planning.md) .

## <a name="review-limits-per-tier"></a>Revise los límites por nivel

El gráfico siguiente es un subconjunto de los límites de [Los límites de servicio de búsqueda de Azure](search-limits-quotas-capacity.md). Enumera los factores más probable que tienen un mayor impacto una decisión SKU. Puede hacer referencia a este gráfico al revisar las siguientes preguntas.

Recursos|Liberar|Básico|S1|S2|S3 |S3 HD
---|---|---|---|----|---|----
Contrato de nivel de servicio (SLA)|No <sup>1</sup> |Sí |Sí  |Sí |Sí  |Sí 
Límites de índice|3|5|50|200|200|1000 <sup>2</sup>
Límites de documento|10.000 total|1 millón por cada servicio|15 millones por partición |60 millones por partición|120 millones por partición |1 millón por índice
Particiones máximas|N/A. |1 |12  |12 |12|3 <sup>2</sup>
Tamaño de la partición|Total de 50 MB|2 GB por cada servicio|25 GB por partición |100 GB por partición (hasta un máximo de 1,2 TB por cada servicio)|200 GB por partición (hasta un máximo de 2,4 TB por cada servicio)|200 GB (hasta un máximo de 600 GB por servicio)
Réplicas máximas|N/A. |3 |12 |12 |12|12
Consultas por segundo|N/A.|~ 3 por réplica|~ 15 por réplica|~ 60 por réplica|> 60 por réplica|> 60 por réplica

<sup>1</sup> Free y SKU de vista previa no se incluyen en SLA. SLA se aplican cuando una SKU vuelve a estar disponible de forma general.

<sup>2</sup> S3 y S3 HD se copian infraestructura de alta capacidad idénticos pero cada uno alcanza el límite máximo de diferentes formas. S3 se dirige a un número menor de índices muy grande. Por lo tanto, el límite máximo es límite de recursos (2,4 TB para cada servicio). S3 HD se dirige a una gran cantidad de índices muy pequeñas. En los índices de 1.000, S3 HD alcanza sus límites en el formulario de restricciones de índice. Si es un cliente de S3 HD que requiere más de 1.000 índices, póngase en contacto con Microsoft Support para obtener información sobre cómo proceder.

## <a name="eliminate-skus-that-dont-meet-requirements"></a>Eliminar SKU que no cumplan los requisitos 

Las siguientes preguntas pueden ayudarle a llegar a la decisión de SKU correcta para su carga de trabajo.

1. ¿Tiene requisitos del **Contrato de nivel de servicio (SLA)** ? Restringir la decisión SKU estándar básica o sin vista previa.
2. ¿ **Cuántos índices** se requieren? Una de las variables mayores división en una decisión SKU es el número de índices admitidos por cada SKU. Soporte técnico de índice es notablemente diferentes niveles en los niveles de precios inferiores. Requisitos del número de índices podrían ser un determinante principal de decisión SKU.
3. **¿Cuántos documentos** se cargarán en cada índice? El número y el tamaño de los documentos determinará el tamaño final del índice. Suponiendo que puede calcular el tamaño estimado del índice, puede comparar ese número con el tamaño de partición por SKU, extendido por el número de particiones necesarias para almacenar un índice de ese tamaño. 
4. **¿Qué es la carga de consultas esperados**? Una vez que se entienden los requisitos de almacenamiento, tenga en cuenta las cargas de trabajo de la consulta. S2 y ambos SKU S3 ofrecen rendimiento cerca equivalente, pero los requisitos SLA se descartar cualquier SKU de vista previa. 
5. Si está pensando en el nivel de S2 o S3, determinar si necesita [indizadores](search-indexer-overview.md). Indizadores no ya están disponibles para el nivel de alta definición S3. Enfoque alternativo es usar un modelo de inserción para actualizaciones de índice, donde escribir código para insertar un conjunto de datos en un índice de la aplicación.

La mayoría de los clientes pueden regla un SKU específica o en función de sus respuestas a las preguntas anteriores. Si aún no está seguro de qué SKU con, puede publicar preguntas en los foros de MSDN o StackOverflow o póngase en contacto con el soporte técnico de Azure para obtener más información.

## <a name="decision-validation-does-the-sku-offer-sufficient-storage-and-qps"></a>¿Validación de toma de decisiones: el SKU ofrece suficiente espacio de almacenamiento y QPS?

Como último paso, vuelva a la [página de precios](https://azure.microsoft.com/pricing/details/search/) y las [secciones de cada servicio y por índice en límites de servicio](search-limits-quotas-capacity.md) para comprobar las estimaciones de los límites de suscripción y el servicio. 

Si el precio o el almacenamiento requisitos están fuera de los límites, desea refactorizar las cargas de trabajo entre varios servicios más pequeños (por ejemplo). En un nivel más detallado, puede diseñar índices para que sea más pequeño o usar filtros para realizar consultas de forma más eficaz.

> [AZURE.NOTE] Requisitos de almacenamiento pueden ser exceso aumentados si los documentos contienen datos extraños. Lo ideal es que, documentos contienen solo pueden buscar datos o metadatos. Datos binarios es que no se puede buscar y se deben almacenar por separado (quizás en un almacenamiento de tabla o blobs de Windows Azure) con un campo en el índice para mantener una referencia de dirección URL a los datos externos. El tamaño máximo de un documento individual es 16 MB (o menos si son cargar varios documentos en una solicitud de masa). Para obtener más información, consulte [límites de servicio de búsqueda de Azure](search-limits-quotas-capacity.md) .

## <a name="next-step"></a>Siguiente paso

Una vez que sepa cual SKU es la mejor opción, continúe con estos pasos:

- [Crear un servicio de búsqueda en el portal](search-create-service-portal.md)
- [Cambiar la asignación de particiones y réplicas escalar su servicio](search-capacity-planning.md)

