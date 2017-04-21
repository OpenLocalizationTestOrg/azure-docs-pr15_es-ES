<properties
   pageTitle="Obtener información sobre el consumo de recursos de Microsoft Azure | Microsoft Azure"
   description="Proporciona una introducción conceptual del uso de facturación de Azure y APIs RateCard, que se utilizan para proporcionar información sobre el consumo de recursos de Azure y tendencias."
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

# <a name="gain-insights-into-your-microsoft-azure-resource-consumption"></a>Obtener información sobre el consumo de recursos de Microsoft Azure

Los clientes y socios requieren la capacidad de predecir y administrar los costos de Azure con precisión.  Cuando pasen de un capital a un modelo de Opex, también necesitan la capacidad de hacer showback frente a análisis de cargo al usuario, así como a proporcionar fidelidad de modo de estimación y facturación, especialmente para implementaciones de nube grande.

Las API de tarjeta de tasa y el uso de recursos de Azure analizan en esta dirección al artículo estas necesidades habilitando nueva información en su consumo de recursos de Azure.  

## <a name="introducing-the-azure-resource-usage-and-ratecard-apis"></a>Introducción a las API de RateCard y el uso de recursos de Azure

El uso de recursos de Azure y APIs RateCard se implementan como un proveedor de recursos, como parte de la familia de API expuestas por el Administrador de recursos de Azure.  

### <a name="azure-resource-usage-api-preview"></a>Uso de recursos de Azure API (vista preliminar)
Los clientes y socios pueden usar la API de uso de recursos de Azure para obtener sus datos de consumo estimado de Azure. Las características incluyen:

- **Control de acceso basado en roles de azure** - clientes y socios pueden configurar sus directivas de acceso en el [portal de Azure](https://portal.azure.com) o mediante [cmdlets de PowerShell de Azure](powershell-install-configure.md) para especificar aplicaciones o que los usuarios pueden obtener acceso a los datos de uso de la suscripción. Las personas que llaman deben utilizar tokens de Azure Active Directory estándares para la autenticación. El llamador también debe agregarse a función el lector, propietario o colaborador para obtener acceso a los datos de uso de una determinada suscripción Azure.

- **Cada hora o agregaciones diarias** - las personas que llaman pueden especificar si desea sus datos de uso de Azure en cada horas depósitos o depósitos diarios. El valor predeterminado es diarias.

- **Metadatos de instancia proporcionados (incluye etiquetas de recursos)** : detalles de nivel de instancia como el uri de recurso completo (/subscriptions/ {Id. de suscripción} /..), junto con el recurso se proporcionará etiquetas de información y los recursos del grupo en la respuesta. Esto ayudar a los clientes de manera determinista y asignar mediante programación uso por las etiquetas para casos de uso, como entre de carga.

- **Metadatos de recursos proporcionados** - detalles del recurso como nombre de contador, contador categoría, contador subcategoría, unidad y región también se pasarán en la respuesta, para dar a las personas que llaman una mejor comprensión de lo que se ha consumido. También estamos trabajando para alinear terminología de metadatos de recursos en el portal de Azure, uso de Azure CSV, EA CSV y otras experiencias público, para permitir a los clientes relacionar datos a través de experiencias de facturación.

- **Uso para todos los tipos de oferta** : datos de uso serán accesibles para todos ofrecen tipos como pago por uso, MSDN, compromiso monetario, crédito monetario y EA, entre otros.

### <a name="azure-resource-ratecard-api-preview"></a>Recursos de Azure RateCard API (vista preliminar)
Los clientes y socios pueden usar la API de RateCard de recursos de Azure para obtener la lista de recursos disponibles de Azure, junto con estimado información sobre precios para cada uno. Las características incluyen:

- **Control de acceso basado en roles de azure** - clientes y socios pueden configurar sus directivas de acceso en el [portal de Azure](https://portal.azure.com) o mediante [cmdlets de PowerShell de Azure](powershell-install-configure.md) para especificar aplicaciones o que los usuarios pueden obtener acceso a los datos de RateCard. Las personas que llaman deben utilizar tokens de Azure Active Directory estándares para la autenticación. El llamador también debe agregarse a función el lector, propietario o colaborador para obtener acceso a los datos de uso de una determinada suscripción Azure.

- **Soporte de pago por uso, MSDN, compromiso monetario y monetario crédito ofrece (EA incompatible)** - esta API proporciona la información de tasa de nivel de la oferta de Azure, frente a nivel de la suscripción.  El llamador de esta API debe pasar la información de la oferta para obtener tasas y detalles de los recursos.  Como ofertas EA han personalizado con tasas inscripción, estamos no puede proporcionar las tasas de Asia Oriental en este momento.

## <a name="scenarios"></a>Escenarios

Estas son algunas de las situaciones que se ha hecho posibles con la combinación de las APIs RateCard y el uso:

- **Azure dedica durante el mes** - clientes puedan usar el uso y APIs RateCard en combinación para obtener recomendaciones mejor a su nube dedica durante el mes, analizando los depósitos por hora y día de las estimaciones de uso y cargo.

- **Configure alertas** , clientes y socios puede configurar alertas basado en monetarios o recursos en su consumo de nube obteniendo el consumo estimado y estimación de cargo con las API RateCard y el uso.

- **Factura de predicción** : clientes y socios puedan acceder a su consumo estimado y nube dedica y aplicar algoritmos de aprendizaje de máquina para predecir cuál sería su factura al final del ciclo de facturación.

- **Análisis de costos de consumo preliminar** : puede clientes también el uso de la API RateCard predecir cuánto su factura sería si fueran mover sus cargas de trabajo a Azure, por lo que proporciona deseado números de uso. Si los clientes tienen cargas de trabajo existentes en otros nubes o nubes privadas, también puede asignar su uso con la Azure dedican tasas para obtener una estimación mejor de su Azure estimado. Esto proporciona una vista mejorada de lo que puede obtenerse a través de la [Calculadora de precios de Azure](https://azure.microsoft.com/pricing/calculator/), como (por ejemplo) nuestros partners de facturación proporcionan la posibilidad de tabla dinámica en oferta y comparar y contraste entre tipos diferentes de oferta más allá de pago por uso, incluidos compromiso monetario y crédito monetario. Las API también proporcionan la capacidad de costo cambios estimación por región, habilitar al tipo de análisis de hipótesis necesario para tomar decisiones de implementación, como implementar recursos en distintos controladores de dominio todo el mundo pueden tener un impacto directo en el costo total.

- **Análisis de hipótesis** -

    - Los clientes y socios para determinar si sería más rentable a ejecutar sus cargas de trabajo en otra región o en otra configuración de los recursos de Azure. Recursos Azure los costos pueden diferir según la región de Azure en la que se están ejecutando, y permite a los clientes y socios obtener las optimizaciones de costo.

    - Los clientes y socios también pueden determinar si otro tipo de oferta de Azure da como resultado una tasa mejor en un recurso de Azure.

## <a name="partner-solutions"></a>Soluciones de asociados

[Uso de Microsoft Azure y RateCard API habilitar Cloudyn para proporcionar ITFM para clientes](billing-usage-rate-card-partner-solution-cloudyn.md) describe la experiencia de integración ofrecida por asociado de Azure Billing API [Cloudyn](https://www.cloudyn.com/microsoft-azure/).  Este artículo proporciona cobertura detallada de sus experiencias, incluyendo un breve vídeo que muestra cómo un cliente de Azure puede utilizar Cloudyn y las API de facturación de Azure para impresiones ganancias de sus datos de Azure consumo.

[Cruiser de la nube y la integración de la API de facturación de Microsoft Azure](billing-usage-rate-card-partner-solution-cloudcruiser.md) describe [de nube Cruiser Express para módulo Azure](http://www.cloudcruiser.com/partners/microsoft/) funcionamiento directamente desde el portal WAP, permitiendo a los clientes administrar sin problemas los aspectos operativos y financieros de su nube de Microsoft Azure privada o alojado pública desde una única interfaz de usuario.   

## <a name="next-steps"></a>Pasos siguientes
+ Consulte la [Referencia de la API de REST de Azure facturación](https://msdn.microsoft.com/library/azure/1ea5b323-54bb-423d-916f-190de96c6a3c) para obtener más detalles sobre ambas API, que forman parte del conjunto de API proporcionadas por el Administrador de recursos de Azure.
+ Si le gustaría meterse en el código de ejemplo, consulte nuestra Microsoft Azure facturación API ejemplos de código en [Los ejemplos de código de Azure](https://azure.microsoft.com/documentation/samples/?term=billing).

## <a name="learn-more"></a>Aprende más
+ Vea el artículo [Información general del Administrador de recursos de Azure](azure-resource-manager/resource-group-overview.md) para obtener más información sobre el Administrador de recursos de Azure.
+ Para obtener más información acerca del conjunto de herramientas necesarias para ayudar a comprender nube dedica, consulte el artículo de Gartner [Guía de mercado para herramientas de administración de finanzas de TI (ITFM)](http://www.gartner.com/technology/reprints.do?id=1-212F7AL&ct=140909&st=sb).
