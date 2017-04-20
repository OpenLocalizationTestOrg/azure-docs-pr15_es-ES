<properties
    pageTitle="Comprender los gastos del servicio externos Azure | Microsoft Azure"
    description="Obtenga información sobre facturación de servicios externos, que anteriormente se llamaba Marketplace, cargos en Azure."
    services=""
    documentationCenter=""
    authors="adpick"
    manager="felixwu"
    editor=""
    tags="billing"
    />

<tags
    ms.service="billing"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/12/2016"
    ms.author="adpick"/>

# <a name="understand-your-azure-external-service-charges"></a>Comprender los gastos del servicio externos Azure

En este artículo se explica la facturación de servicios externos de Azure. Servicios externos se usan para llamar pedidos de catálogo de soluciones. Servicios externos proporcionados por los proveedores de servicios independientes, pero se integran completamente en el ecosistema de Azure. Obtenga información sobre cómo:

- Identificar servicios externos
- Entender cómo la facturación difiere de otros recursos de Azure
- Ver y realizar un seguimiento de los costos que acumula del uso de servicios externos
- Administrar pedidos de servicio externo y forma de pago por ellos

## <a name="what-are-azure-external-services"></a>¿Qué son los servicios externos Azure?

Servicios externos se usan para llamar a Azure Marketplace. Por lo general, están servicios publicados por terceros disponibles para Azure. Por ejemplo, ClearDB y SendGrid son servicios externos que pueden comprar en Azure, pero no se publican por Microsoft.

### <a name="identify-external-services"></a>Identificar servicios externos

Cuando se aprovisiona un nuevo servicio externo o un recurso, se muestra una advertencia:

![Advertencia de compra de Marketplace](./media/billing-understand-your-azure-marketplace-charges/marketplace-warning.PNG)

>[AZURE.NOTE] Servicios externos se publican por compañías que no son de Microsoft, pero en ocasiones, los productos de Microsoft también se clasifican como servicios externos.

### <a name="external-services-are-billed-separately"></a>Servicios externos se cargarán por separado

Servicios externos se tratan como pedidos individuales dentro de su suscripción de Azure. El período de facturación para cada servicio se establece cuando compre el servicio. No se deben confundir con el período de facturación de la suscripción en la que se ha adquirido. También se ofrecen independientes facturas y su tarjeta de crédito se cargará por separado.

### <a name="each-external-service-has-a-different-billing-model"></a>Cada servicio externo tiene un modelo de facturación diferente

Algunos servicios se cargarán en una forma de pago, mientras que otros utilizan un modelo de acuerdo de pago mensual. Necesita una tarjeta de crédito para Azure servicios externos, no puede comprar servicios externos con pago de factura.

### <a name="you-cant-use-monthly-free-credits-for-external-services"></a>No puede usar créditos gratuitas mensuales para servicios externos

Si está utilizando una suscripción de Azure que incluye [créditos gratuitas](https://azure.microsoft.com/pricing/spending-limits/), no se puede aplicar a las listas de servicio externo. Utilizar una tarjeta de crédito para comprar servicios externos.

## <a name="view-external-service-spending-and-history"></a>Ver los gastos servicio externo e historial

Puede ver una lista de los servicios externos que están en cada suscripción en el [portal de Azure](https://portal.azure.com/): 

1. Inicie sesión en el [portal de Azure](https://portal.azure.com/) y [navegue hasta el módulo de **facturación** ](https://portal.azure.com/?flight=1#blade/Microsoft_Azure_Billing/BillingBlade).

    ![Seleccione facturación en el menú de concentrador](./media/billing-understand-your-azure-marketplace-charges/billing-button.png) 
  
2. En la sección de **costos de suscripción** , seleccione la suscripción que desea ver. 
   
    ![Seleccione una suscripción en el módulo de facturación](./media/billing-understand-your-azure-marketplace-charges/select-sub.png)

3. Haga clic en **servicios externos**.

    ![Haga clic en servicios externos, en el módulo de suscripción](./media/billing-understand-your-azure-marketplace-charges/external-service-blade.png)

4. Debería ver cada uno de los pedidos de servicio externo, el nombre del Editor, compró de nivel de servicio, que ha asignado el recurso y el estado actual del pedido. Seleccione un servicio externo para ver más allá de facturas.

    ![Seleccione un servicio externo](./media/billing-understand-your-azure-marketplace-charges/external-service-blade2.png)

5. Desde aquí, puede ver más allá de los importes de factura, incluido el desglose de impuestos.

    ![Servicios externos de ver el historial de facturación](./media/billing-understand-your-azure-marketplace-charges/billing-overview-blade.png)

## <a name="manage-payment-methods-for-external-service-orders"></a>Administrar los métodos de pago para los pedidos de servicio externo

Actualizar los métodos de pago para los pedidos de servicio externo desde el [Centro de la cuenta](https://account.windowsazure.com/).

> [AZURE.NOTE] Si ha adquirido la suscripción con una cuenta de trabajo o la escuela debe [ponerse en contacto con soporte técnico](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para realizar cambios en el método de pago.

1. Inicie sesión en el [Centro de cuenta](https://account.windowsazure.com/) y [vaya a la pestaña de **marketplace** ](https://account.windowsazure.com/Store)

    ![Seleccione el catálogo de soluciones en el centro de la cuenta](./media/billing-understand-your-azure-marketplace-charges/select-marketplace.png)

2. Seleccione el servicio externo que desea administrar

    ![Seleccione el servicio externo que desea administrar](./media/billing-understand-your-azure-marketplace-charges/select-ext-service.png)

3. En el lado derecho de la página, haga clic en **cambiar el método de pago** . Este vínculo le ofrece un portal diferentes para administrar el método de pago.
    
    ![Resumen del pedido](./media/billing-understand-your-azure-marketplace-charges/change-payment.PNG)

4. Haga clic en **Editar información** y siga las instrucciones para actualizar la información de pago.

    ![Seleccione Editar información](./media/billing-understand-your-azure-marketplace-charges/edit-info.png)
    
## <a name="cancel-an-external-service-order"></a>Cancelar un pedido de servicio externo

Si desea cancelar el pedido de servicio externo, debe eliminar el recurso en el [portal de Azure](https://portal.azure.com).

![Eliminar recurso](./media/billing-understand-your-azure-marketplace-charges/deleteMarketplaceOrder.PNG)

## <a name="need-help-contact-support"></a>¿Necesita ayuda? Póngase en contacto con soporte técnico.

Si aún tiene más preguntas, póngase [en contacto con soporte técnico](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para obtener el problema resuelto rápidamente.
