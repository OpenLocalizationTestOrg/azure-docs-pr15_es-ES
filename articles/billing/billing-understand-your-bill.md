<properties
   pageTitle="Descripción de la factura | Microsoft Azure"
   description="Aprenda a leer y entender el uso y la factura para la suscripción de Azure"
   services=""
   documentationCenter=""
   authors="genlin"
   manager="stevenpo"
   editor=""
   tags="billing"/>

<tags
   ms.service="billing"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/31/2016"
   ms.author="erihur;genli"/>


# <a name="understand-your-bill-for-microsoft-azure"></a>Entender la factura de Microsoft Azure

> [AZURE.NOTE] Si necesita más ayuda en cualquier punto de este artículo, póngase [en contacto con soporte técnico](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para obtener el problema resuelto rápidamente.

Los cargos para suscripciones de Microsoft Azure varían según el plan de tasa. Algunos planes de tasa, por ejemplo, los suscriptores de Visual Studio Enterprise (MPN), incluyen créditos mensuales que puede utilizar cualquier servicio de Azure según sus necesidades.

Tenga en cuenta que el 24 horas de uso de latencia de su período de facturación anterior se puede registrar en el período de facturación actual.

Para obtener más información sobre planes de tasa y consumo, consulte la [página de opciones de compra de Microsoft Azure](https://azure.microsoft.com/pricing/purchase-options/).

<!-- The below links cover a complete list of all Microsoft Azure services.

<!-- - [Service Details list (csv1)](https://azurepricing.blob.core.windows.net/supplemental/MOSPServices_csv1.xlsx)
<!-- - [Service Details list (csv2)](https://azurepricing.blob.core.windows.net/supplemental/MOSPServices_csv2.xlsx)

<!-- *NOTE: The **csv1** link refers to the column header names for csv version 1 and **csv2** link refers to the new column header names for csv version 2.  These files are updated monthly.*-->

### <a name="view-or-download-a-bill-for-microsoft-azure"></a>Ver o descargar una factura para Microsoft Azure:

1. Inicie sesión en el [Centro de la cuenta](https://account.windowsazure.com/subscriptions) con su Account de Microsoft o el identificador de organización.

2. Haga clic en la suscripción que le gustaría ver detalles y uso.

3. Haga clic en **el historial de facturación**

    ![Resumen: el historial -1 de facturación](./media/billing-understand-your-bill/ContentViewaBillforMA1.png)

4. La sección **Historial de facturación** enumera las instrucciones de períodos de facturación anteriores más el período actual no facturado. La instrucción para el período actual es una estimación de sus cargos en el momento en que se ha generado la estimación. Esta información solo se actualiza diariamente y no puede incluir todo el uso de contraídos hasta la fecha. La factura mensual puede diferir de esta estimación.  

    ![Historial de facturación de resumen 2](./media/billing-understand-your-bill/ContentViewaBillforMA2.png)

5. Haga clic en **Ver declaración actual** para ver una estimación de sus cargos en el momento en que se ha generado la estimación. Esta información solo se actualiza diariamente y no puede incluir todo el uso de contraídos hasta la fecha. La factura mensual puede diferir de esta estimación.

    ![Historial de facturación de resumen de 3](./media/billing-understand-your-bill/ContentViewaBillforMA3.png)

    ![Historial de facturación de resumen de 4](./media/billing-understand-your-bill/ContentViewaBillforMA4.png)

6. Haga clic en **Descargar factura** para ver una copia de la factura anterior.

    ![Historial de facturación de resumen de 5](./media/billing-understand-your-bill/ContentViewaBillforMA5.png)


> [AZURE.NOTE] Cargos aparece en facturación instrucciones para los clientes internacionales son cálculo solo como bancos tienen costos diferentes para los tipos de conversión.

Las siguientes son algunas instrucciones de ejemplo para dos ofertas diferentes disponibles en Microsoft Azure.

 Tipo de oferta | Descripción | Descargar |
 :--------- |:-------- | :-------|
Pago por uso | Pago mensual de demora | [Ejemplo de archivo](https://azurepricing.blob.core.windows.net/sampleinvoices/Microsoft_Azure_ccinvoice_Sample.pdf)
Oferta de compromiso | Dedica deducido de su compromiso de prepago | [Ejemplo de archivo](https://azurepricing.blob.core.windows.net/sampleinvoices/Microsoft_Azure_invoice_Sample.pdf)

## <a name="account-information"></a>Información de cuenta

La sección información de cuenta identifica información pertinente respecto a su perfil y uso.

![encabezado](./media/billing-understand-your-bill/Header.png)

| Términos                | Descripción                                                                                         |
|---------------------|-----------------------------------------------------------------------------------------------------|
| Nº factura         | Un identificador único factura para realizar un seguimiento                                                   |
| Ciclo de facturación       | El período de tiempo en el que ha realizado uso                                                       |
| Fecha de factura        | Fecha en la que se ha generado la factura                                                                 |
| Método de pago      | Tipo de pago que se usan en la cuenta (factura o tarjeta de crédito)                                   |
| Facturación             | Dirección de pagos de Microsoft Azure                                                                    |
| Oferta de suscripción  | Tipo de oferta de suscripción se compró (pago por uso, BizSpark Plus, pase de Azure, etcetera) |
| Cuenta de correo electrónico de propietario | La dirección de correo electrónico de la cuenta registrada en la cuenta de Microsoft Azure                      |

## <a name="understand-the-invoice-summary"></a>Comprender el resumen de factura

La sección de **Resumen de la factura** de la factura resume las transacciones desde la última factura y los cargos de uso actuales.

![Resumen de factura](./media/billing-understand-your-bill/InvoiceSummary.png)

El saldo anterior, pagos y saldo pendiente sección de la lista resumen las transacciones desde la última factura.

| Términos                                              | Descripción                                                                              |
|---------------------------------------------------|------------------------------------------------------------------------------------------|
| Saldo anterior                                  | El importe total pendiente de pago de la última factura                                                 |
| Pagos                                          | Total pagos aplicados a la última factura                                                 |
| Saldo pendiente (de ciclo de facturación anterior) | Los ajustes de factura (créditos o saldos) aplicados a su cuenta desde la última factura  |

## <a name="understand-the-current-charges"></a>Entender los cargos actuales
La sección de cargos actuales de la factura contiene detalles acerca de los gastos mensuales. Los vínculos se organizan en las subsecciones siguientes.

| Términos          | Descripción                                                                                                                                                                                                                                                                                                                                                                                                                                            |
|---------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Cargos de uso | Cargos de uso son cargos mensuales totales en una suscripción. Se cargarán retrasen para el uso del mes pasado.                                                                                                                                                                                                                                                                                                                                       |
| Descuentos     | Descuentos servicio aplicados a la factura actual se reflejará en este artículo de línea.                                                                                                                                                                                                                                                                                                                                              |
| Ajustes   | Varios ajustes son créditos varios o cargos pendientes aplicados a la factura actual. Por ejemplo, si tiene la Visual Studio Enterprise con la oferta MSDN, verá un crédito mensual en este artículo de línea. Si cancela la suscripción, verá los cargos de uso mensual que superen el crédito mensual incluido en su oferta desde el principio del período de facturación actual a la fecha de cancelación de su suscripción.|

## <a name="footer-information"></a>Información de pie de página
![pie de página](./media/billing-understand-your-bill/footerinformation.png)

## <a name="understand-the-additional-information"></a>Comprender la información adicional
La página información adicional le da referencias a otros recursos para comprender su factura y vínculos para ver su uso y otra información relevante para la factura.

![información adicional](./media/billing-understand-your-bill/AdditionalInformation.png)

### <a name="detailed-usage"></a>Uso detallado
Un vínculo en la descripción del **Uso detallado** dirige el centro de cuenta donde puede ver su uso detallado para esta suscripción.  Ahora hay dos versiones disponibles para descargar: **.csv versión 1** contiene los campos de uso y la convención de nomenclatura antiguo y la **versión 2 de archivo .csv** contiene nombres descriptivos de cliente para cada una de las categorías más campos adicionales que le ayudarán a comprender qué servicios están usando en Microsoft Azure. Tenga en cuenta que en la versión de archivo .csv 1 que no hay ningún detalle del Administrador de recursos de Azure. Puede encontrar información de administrador de recursos Azure en la versión del archivo .csv 2.

### <a name="additional-information-and-useful-resources"></a>Información adicional y recursos útiles
Esta sección contiene vínculos a simples preguntas sobre tamaños de instancia de cálculo, los gastos de DB de SQL y vínculos útiles que le ayudarán a responder más preguntas.

| Términos                 | Descripción                                                                                                                            |
|----------------------|----------------------------------------------------------------------------------------------------------------------------------------|
| Vendido a              | Esto se rellena automáticamente con la dirección de perfil de la cuenta                                                                           |
| Instrucciones de pago | Esta sección es las instrucciones de pago de dónde enviar cheques, transferencias de alambre o urgente comprobaciones si su método de pago es de factura |

## <a name="understand-detailed-usage-charges"></a>Entender los cargos de uso detallado

Como parte de nuestro compromiso para ayudar a los clientes a administrar fácilmente su uso Azure, hemos mejorado el archivo de uso de descargar informes sobre el uso de servicios de Azure y costos.  El vínculo de descarga contiene dos versiones del archivo de uso:

- **Versión 1** utiliza el formato existente

- **Versión 2** se incluye información adicional y se actualizan los nombres de columna en la sección uso diario.  

Cargos de uso son gastos totales **mensuales** en una suscripción menos cualquier crédito o descuento. Se cargarán retrasen para el uso del mes pasado.  La sección superior del archivo muestra los detalles sobre los servicios que está facturadas para durante el ciclo de facturación del mes anterior.  La tabla anterior muestra los nombres de las columnas para cada uno de los archivos de la versión de archivo .csv.

Versión 1 |  Versión 2  |  Descripción|
:---------------| :---------------- | --------|
Período de facturación | Período de facturación | El período de facturación cuando se consumen los recursos.
Nombre | Categoría de contador | Identifica el servicio de nivel superior para la que pertenece este uso.
Tipo | Medidor de la subcategoría. | Servicio de Azure puede definirse por tipo de esta columna, que puede afectar a la velocidad.
Recursos | Nombre de contador | Identifica la unidad de medida para el recurso consumido.
Región | Región de contador | Identifica la ubicación del centro de datos para determinados servicios que tienen un precio en función de la ubicación del centro de datos.
SKU | SKU | Identifica el identificador único del sistema para cada recurso de Azure.
Unidad | Unidad | Identifica la unidad que se cargará el servicio en. Por ejemplo, GB, horas, 10,000s.
Consumida | Cantidad consumida | Contiene la cantidad del recurso que se ha consumido durante el período de facturación.
Incluido | Cantidad incluida | Contiene la cantidad del recurso que se incluye sin cargo en el período de facturación actual.
Facturable | Cantidad | Si la cantidad consumida supera el importe incluye, esta columna muestra la diferencia. Se cargarán durante este período. Ofertas de pago por uso con ningún importe incluido con la oferta, este total será la misma que la cantidad consumida.
Dentro de compromiso | Dentro de compromiso | Contiene los cargos de recursos que se disminuye el importe de compromiso asociado con su oferta de 6 o 12 meses. Los cargos de recursos están disminuye el importe de compromiso en orden cronológico.
Moneda | Moneda | Identifica la divisa que se reflejan en el período de facturación actual.
Excedente | Excedente | Contiene los cargos de recursos que superen el importe de compromiso asociado con su oferta de 6 o 12 meses.
Tasa de compromiso | Tasa de compromiso | Contiene el tipo de compromiso en función de la cantidad total compromiso asociada con su oferta de 6 o 12 meses.
Tasa | Tasa | Tasa muestra la tasa que se le cobrarán por unidad facturable.
Valor | Valor | Muestra el resultado de multiplicar la columna por la columna tasa facturable. Si la cantidad consumida no supere la cantidad incluye, no habrá cargo de esta columna.

## <a name="analyze-daily-usage-data"></a>Analizar los datos de uso diaria
Dependiendo de su uso, puede ser miles de filas de datos de uso diario. Si desea analizar estos datos, haga clic en **Descargar uso** y elija una versión de archivo variable separados por comas (.csv) para ver los datos de uso diaria para el período de facturación adecuado.  Referencia, puede descargar un archivo .csv de ejemplo para cada versión a continuación.

 Nombre | Descargar |
 :----------:| :-------: |
  Uso detallado .csv versión 1|  [Ejemplo de archivo](https://azurepricing.blob.core.windows.net/supplemental/MOSPServices_csv1.xlsx)
  Uso detallado .csv versión 2 | [Ejemplo de archivo](https://azurepricing.blob.core.windows.net/supplemental/MOSPServices_csv2.xlsx)



![csv2screenshot](./media/billing-understand-your-bill/csv2screenshot.png)



En el archivo .csv, los elementos se dividen para mostrar una lista de la cantidad de cada recurso se ha consumido dentro del período de facturación actual.

![instantánea de CSV](./media/billing-understand-your-bill/csvsnapshotportal.png)

Las columnas siguientes muestran detalles que afectan a las tasas al principio del período de facturación:

Versión 1 |   Versión 2   |  Descripción |
:---------------| :----------------| -----|
Fecha de uso | Fecha de uso | La fecha cuando emitió el recurso.
Nombre | Categoría de contador | Identifica el servicio de nivel superior para la que pertenece este uso.
GUID de recursos | Identificador de contador | El identificador de contador facturado.  Esto se usa como el identificador utilizado para el uso de facturación de precios.
Tipo | Medidor de la subcategoría. | Servicio de Azure puede definirse por tipo de esta columna, que puede afectar a la velocidad.
Recursos | Nombre de contador | Identifica la unidad de medida para el recurso consumido.
Región | Región de contador | Identifica la ubicación del centro de datos para determinados servicios que tienen un precio en función de la ubicación del centro de datos.
Unidad | Unidad | Identifica la unidad que se cargará el servicio en. Por ejemplo, GB, horas, 10,000s.
Consumida | Cantidad consumida | Contiene la cantidad del recurso que se ha consumido para ese día.
Región de Sub | Ubicación de recursos | Identifica el centro de datos donde se está ejecutando el recurso.
Servicio | Servicio consumido | Esta columna se utiliza para realizar un seguimiento el servicio de plataforma de Windows Azure individuales que no se consideran específicamente en la columna nombre. Este servicio columna indica qué específica el uso del servicio pertenece.
N/A. | Grupo de recursos | _**Adición nueva columna.**_ El grupo de recursos en el que se está ejecutando el recurso implementado en. Consulte [información general del Administrador de recursos de Azure](../azure-resource-manager/resource-group-overview.md)
Componente | Id. de instancia | El identificador para el recurso de ejecución. El identificador contiene el nombre que especifique para el recurso cuando se creó.
N/A. | Etiquetas | _**Adición nueva columna.**_ Nuevos tipos de recursos en Azure permiten a los recursos de la etiqueta. Hacer referencia a [Organizar los recursos de Azure con etiquetas](http://azure.microsoft.com/updates/organize-your-azure-resources-with-tags/)
Información adicional | Información adicional | Metadatos adicionales relacionados con el servicio.
Información de servicio 1 | Información de servicio 1 | Esta columna proporciona el nombre del proyecto que pertenece el servicio en la suscripción.
Información de servicio 2 | Información de servicio 2 | Esto es un campo heredado que capture metadatos específicos del servicio opcional.

Además de algunos campos nuevos y el nombre cambia a csv versión 2, hay se pueden normalizados el formato de los datos de la debajo de campos:

- **Id. de instancia**: representa de campo Id. de instancia el identificador para el servicio se aprovisione especificado por el usuario. Actualmente, hay dos formatos en los que se representa el identificador de instancia: es el nombre del recurso o el identificador de recurso completo. Servicios de Microsoft Azure están cambiando para representar el identificador de instancia en un formato estándar de Id. de recurso completo _**(/subscriptions/<subscription id>/resourcegroups/<resourcegroupname>/providers/<providername>/<resourcename>)**_. Como transición al nuevo formato de los servicios se muestra el campo de datos de Id. de instancia cambiar desde el nombre del recurso a Id. El identificador del recurso es el formato que usa la [API del Administrador de recursos de Azure](https://msdn.microsoft.com/library/azure/dn790567.aspx) para identificar recursos en una suscripción.

![InstanceId](./media/billing-understand-your-bill/instanceid.png)

- **Información adicional**: columna de la información adicional en el archivo .csv de uso especifica metadatos específicos del servicio. Por ejemplo, un tipo de imagen de una máquina virtual. Actualmente, un servicio emite específicos del servicio de metadatos en varias columnas: campos información adicional, información de servicio 1 y 2 de la información de servicio. Servicios de Microsoft Azure se estandarización de emisión de metadatos específicos del servicio en la columna información adicional solo.  Vea la debajo de instantánea del formato estándar:

![additionalinfo_csv2](./media/billing-understand-your-bill/AdditionaInfo_csv2.png)

- **Etiquetas**: esta columna contiene el usuario especificado etiquetas de recursos. Las etiquetas pueden utilizarse para agrupar registros de facturación. Por ejemplo, puede usar etiquetas para distribuir los costos por departamento, mediante el servicio. Más información sobre cómo [usar etiquetas para organizar los recursos de Azure](../resource-group-using-tags.md). Servicios compatibles con etiquetas de emisión son:  

    - Máquinas virtuales de Windows

    - Almacenamiento y

    - Servicios de red suministrados con la [API del Administrador de recursos de Azure](https://msdn.microsoft.com/library/azure/dn790567.aspx)

![etiquetas](./media/billing-understand-your-bill/tags.png)


## <a name="next-steps"></a>Pasos siguientes

- [Configurar alertas de facturación](../billing-set-up-alerts.md)

- [Administrar los métodos de pago](../billing-how-to-change-credit-card.md)

- [Entender los cargos de Azure Marketplace](../billing-understand-your-azure-marketplace-charges.md)

- [Preguntas más frecuentes de Azure facturación y suscripción](../billing-subscription-faq.md)

> [AZURE.NOTE] Si aún tiene más preguntas, póngase [en contacto con soporte técnico](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para obtener el problema resuelto rápidamente.

<!--
OLD MSDN Articles
- [What do I do if my Azure subscription become disabled?](https://msdn.microsoft.com/library/azure/dn736049.aspx)
- [Edit payment information for an existing credit card](https://msdn.microsoft.com/library/azure/dn736053.aspx)
- [Add a new credit card to use as a payment method](https://msdn.microsoft.com/library/azure/dn736057.aspx)
- [Change the credit card on your Microsoft Azure account](https://msdn.microsoft.com/library/azure/dn736050.aspx)
- [Manage your payment method](https://msdn.microsoft.com/library/azure/dn736054.aspx)
-->



<!--Image references-->
