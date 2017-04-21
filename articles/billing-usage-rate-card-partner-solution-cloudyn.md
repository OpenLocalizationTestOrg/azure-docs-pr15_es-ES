<properties
   pageTitle="Uso de Microsoft Azure y Cloudyn RateCard API habilitar para proporcionar ITFM para clientes | Microsoft Azure"
   description="Proporciona una perspectiva única de facturación de Microsoft Azure asociado Cloudyn, en sus experiencias integrar las API de facturación de Azure en sus productos.  Esto es especialmente útil para los clientes de Azure y Cloudyn que están interesados en usar o probar Cloudyn para servicios de Azure."
   services=""
   documentationCenter=""
   authors="BryanLa"
   manager="mbaldwin"
   editor=""
   tags="billing"/>

<tags
   ms.service="billing"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="billing"
   ms.date="08/16/2016"
   ms.author="mobandyo;bryanla"/>

# <a name="microsoft-azure-usage-and-ratecard-apis-enable-cloudyn-to-provide-itfm-for-customers"></a>Uso de Microsoft Azure y las API de RateCard habilitan Cloudyn proporcionar ITFM para los clientes

Cloudyn, un asociado de desarrollo de Microsoft y un proveedor líder de capacidades de administración de la nube, se ha elegido para una vista previa privada del uso de recursos de Microsoft Azure y APIs RateCard nuevo.  La API de uso proporciona acceso a los datos de consumo de Azure estimado para una suscripción. La API de RateCard proporciona información de precio completa de todos los servicios de Azure, para los clientes no - contrato Enterprise EA. Integrado juntas, estas API proporcionan una base de toda la información de entrada a las herramientas de administración de finanzas TI (ITFM) como los proporcionados por Cloudyn.

## <a name="introduction"></a>Introducción

Llamados "multiplicación" de los datos de la API de uso con los datos de la API RateCard (precio de uso [unidades] [$unit] = uso detallado y costo) crea más granular, precisa y fiable información de facturación disponible para Azure hoy.

![Información general ITFM][1]

Consumo estas API proporciona información clave en los costos, permitiendo Cloudyn para analizar las cuentas de cliente de forma sencilla, mediante programación y realizar diversas tareas ITFM para sus clientes y uso de los clientes.

## <a name="integrating-cloudyn-with-the-ratecard-and-usage-apis"></a>Integrar Cloudyn con las API de uso y RateCard
La API RateCard requiere varios parámetros de entrada, como la información de la región, la moneda y la configuración regional--pero más importante es OfferDurableID, que especifica el tipo de Azure oferta al cliente es mediante (pago por uso, planes de compromiso 6 y 12 meses heredados, MSDN ofertas, MPN ofertas, ofertas promocionales y otros). La OfferDurableID puede encontrarse en el [portal de facturación y el uso de Azure](https://account.windowsazure.com/Subscriptions), debajo de "Ofrecer ID" para la suscripción determinada.

Al registrarse para servicios de [Cloudyn de Azure](https://www.cloudyn.com/microsoft-azure/) , los clientes pueden agregar su código OfferDurableID, que permite Cloudyn extraer su información de precios correspondiente a través de la API RateCard.  Obtener información sobre los distintos tipos de ofertas se puede encontrar una página de [Detalles de la oferta de Microsoft Azure](https://azure.microsoft.com/support/legal/offer-details/) .

![Información general del motor de Cloudyn ITFM][2]

Cloudyn utiliza el uso y la APIs RateCard, además de la API de rendimiento de Azure, para crear niveles adicionales de visualización, análisis, alertas, informes, administración de costos y recomendaciones útiles, ofrecer a los clientes de Azure una herramienta ITFM de nube de empresarial confiable.

## <a name="cloudyn-itfm-use-cases-enabled-by-usage-and-ratecard-api-integration"></a>Habilitar integración RateCard API y el uso de casos de uso de Cloudyn ITFM
Habilita el uso de casos de uso de comunes ITFM Cloudyn y RateCard APIs incluyen:

+ **Análisis de costo** - permite costos se dividen en cualquier dimensión de identificación nativa (proveedor, servicio, cuenta, región etcetera) en la nube. El uso de Azure y APIs RateCard realizar una tarea fácil, proporcionando el desglose más granular de uso y datos por cuenta, que se agrupan y filtrada por Cloudyn y se presenta al usuario, en un formulario de gráfico o tabular de costo.

![Gráfico circular de análisis de costos][3]

+ **Costo asignación 360** - permite Finanzas y los administradores de TI para descubrir el análisis de costos reales, los controladores y tendencias de su implementación de nube. Más permite a los administradores asociar fácilmente los gastos de implementación con las unidades de negocio, departamentos, regiones y más, proporcionando precedentes recomendaciones sobre los costos de la nube y facilitar showbacks y enterprise recargos. El uso de Azure y RateCard APIs son como entrada al motor de asignación de costo del Cloudyn, que complementa las API definiendo métodos y la lógica empresarial de asignación de recursos sin etiquetar o untaggable.

![Gráfico de asignación de costos 360][4]

+ **Tamaño rentable** - proporciona recomendaciones de tamaño de la derecha para máquinas virtuales poco utilizados, reduciendo gastos del cliente en equipos demasiado grandes o demasiado aprovisionados. Para ello examinando máquina virtual CPU y mediciones de memoria RAM (a través de la API de rendimiento), horas de tiempo de ejecución (mediante el uso de la API) y el costo (a través de la API de RateCard). A continuación, Cloudyn proporciona recomendaciones de tamaño de la derecha en función de los recursos de RAM o CPU poco utilizados (rendimiento) y calcula los ahorros estimados multiplica la diferencia de precio (RateCard) entre las máquinas virtuales por el (uso) de tiempo de utilización real de la máquina poco utilizada.

![Tamaño rentable][5]

+ **Recomendaciones de nube trasladar** - proporciona Consejo financiero en nube trasladar. Examina los costos en actual de recursos de la nube que se implementan en proveedores de nube principales de un usuario y se compara con el costo de una implementación equivalente en Azure. A continuación, proporciona detallados, por recursos, financiero basado en trasladar recomendaciones de Azure. Después de evaluar la implementación equivalente obligatorio en Azure (según las preferencias de usuario y métricas del rendimiento), Cloudyn usa la API RateCard para evaluar el costo de la implementación equivalente en Azure.

+ **Informes de rendimiento** - habilitado por el rendimiento de Azure API, estos informes ofrecen una serie de características de uso de CPU y RAM recomendaciones de optimización. A continuación se muestra un ejemplo de informe de utilización de instancia desglose de instancia de la presentación por medio de la CPU.

![Informes de rendimiento][6]

+ **Administrador de la categoría** : una característica eficaz de Cloudyn que recupera orden a recursos de nube ni organizadas. Proporciona a los usuarios la libertad para crear sus propias categorías únicos (etiquetas) para medir eficaces e informes que está en línea con las prácticas de empresa. Además, los usuarios pueden fácilmente regular y clasificar incoherente etiquetado (es decir, errores tipográficos y otras discrepancias) y detectar automáticamente recursos no etiquetados para atribución precisa de los costos.

![Administrador de la categoría][7]

## <a name="video"></a>Vídeo

Aquí es un breve vídeo que muestra cómo un cliente de Azure puede usar Cloudyn para Azure y las API de facturación de Azure, para obtener información de sus datos de Azure consumo.

> [AZURE.VIDEO cloudyn-provides-cloud-itfm-tools-via-microsoft-azure-apis]


## <a name="next-steps"></a>Pasos siguientes

+ Iniciar una prueba gratuita de [Cloudyn para Azure](https://www.cloudyn.com/microsoft-azure/) para ver cómo puede obtener transparencia de costo con análisis y generación de informes personalizados para la implementación de nube de Microsoft Azure.
+ Para obtener información general sobre el uso de recursos de Azure y RateCard APIs, consulte [obtener recomendaciones sobre el consumo de recursos de Microsoft Azure](billing-usage-rate-card-overview.md) .
+ Consulte la [Referencia de la API de REST de Azure facturación](https://msdn.microsoft.com/library/azure/1ea5b323-54bb-423d-916f-190de96c6a3c) para obtener más información sobre ambos API, que forman parte del conjunto de API proporcionadas por el Administrador de recursos de Azure.
+ Si le gustaría meterse en el código de ejemplo, consulte nuestra Microsoft Azure facturación API ejemplos de código en [Los ejemplos de código de Azure](https://azure.microsoft.com/documentation/samples/?term=billing).

## <a name="learn-more"></a>Aprende más
+ Para obtener más información sobre las ofertas de Microsoft Azure Enterprise contrato (EA), visite [Azure licencias para la empresa] (https://azure.microsoft.com/pricing/enterprise-agreement/)
+ Vea el artículo [Información general del Administrador de recursos de Azure](azure-resource-manager/resource-group-overview.md) para obtener más información sobre el Administrador de recursos de Azure.
+ Para obtener más información acerca del conjunto de herramientas necesarias para ayudar a comprender nube dedica, consulte el artículo de Gartner [Guía de mercado para herramientas de administración de finanzas de TI (ITFM)](http://www.gartner.com/technology/reprints.do?id=1-212F7AL&ct=140909&st=sb).

<!--Image references-->
[1]: ./media/billing-usage-rate-card-partner-solution-cloudyn/Cloudyn-ITFM-Overview.png
[2]: ./media/billing-usage-rate-card-partner-solution-cloudyn/Cloudyn-ITFM-Engine-Overview.png
[3]: ./media/billing-usage-rate-card-partner-solution-cloudyn/Cloudyn-Cost-Analysis-Pie-Chart.png
[4]: ./media/billing-usage-rate-card-partner-solution-cloudyn/Cloudyn-Cost-Allocation-360-Chart.png
[5]: ./media/billing-usage-rate-card-partner-solution-cloudyn/Cloudyn-Cost-Effective-Sizing.png
[6]: ./media/billing-usage-rate-card-partner-solution-cloudyn/Cloudyn-Performance-Reports.png
[7]: ./media/billing-usage-rate-card-partner-solution-cloudyn/Cloudyn-Category-Manager.png
