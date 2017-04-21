<properties
    pageTitle="Cambiar su suscripción de Azure a otra oferta | Microsoft Azure"
    description="Obtenga más información sobre cómo cambiar su suscripción de Azure y cambiar a una oferta de diferentes con el portal de administración de la suscripción"
    services=""
    documentationCenter=""
    authors="genlin"
    manager="mbaldwin"
    editor=""
    tags="billing,top-support-issue"/>

<tags
    ms.service="billing"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/30/2016"
    ms.author="genli"/>

# <a name="switch-your-azure-subscription-to-another-offer"></a>Cambiar su suscripción de Azure a otra oferta

Como un cliente de [pago por uso](https://azure.microsoft.com/offers/ms-azr-0003p/) , es posible que pueda cambiar su suscripción de Azure a otra oferta en el [Centro de la cuenta](https://account.windowsazure.com/Subscriptions). Por ejemplo, puede usar esta característica para aprovechar las ventajas de los [créditos mensuales para los suscriptores de Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/). Si se encuentra en la [Versión de prueba gratuita](https://azure.microsoft.com/free/), obtenga información sobre cómo [actualizar a pago por uso](billing-upgrade-azure-subscription.md).

#### <a name="whats-supported"></a>¿Qué es compatible:

| De                                                              | Para                                                                                      |
|-------------------------------------------------------------------|-----------------------------------------------------------------------------------------|
| [Pago por uso](https://azure.microsoft.com/offers/ms-azr-0003p/) | [Pruebas y desarrollo de pago por uso](https://azure.microsoft.com/offers/ms-azr-0023p/)              |
| [Pago por uso](https://azure.microsoft.com/offers/ms-azr-0003p/) | [Visual Studio Professional](https://azure.microsoft.com/offers/ms-azr-0059p/)          |
| [Pago por uso](https://azure.microsoft.com/offers/ms-azr-0003p/) | [Prueba de Visual Studio Professional](https://azure.microsoft.com/offers/ms-azr-0060p/)     |
| [Pago por uso](https://azure.microsoft.com/offers/ms-azr-0003p/) | [Plataformas MSDN](https://azure.microsoft.com/offers/ms-azr-0062p/)                      |
| [Pago por uso](https://azure.microsoft.com/offers/ms-azr-0003p/) | [Visual Studio Enterprise](https://azure.microsoft.com/offers/ms-azr-0063p/)            |
| [Pago por uso](https://azure.microsoft.com/offers/ms-azr-0003p/) | [Visual Studio Enterprise (Bizspark)](https://azure.microsoft.com/offers/ms-azr-0064p/) |

> [AZURE.NOTE] Para otros ofrecen cambios, [póngase en contacto con soporte técnico](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
    
## <a name="switch-subscription-offer"></a>Oferta de suscripción de conmutador

> [AZURE.VIDEO switch-to-a-different-azure-offer]

1.  Iniciar sesión en el [Centro de la cuenta de Azure](https://account.windowsazure.com/Subscriptions).

2.  Seleccione la suscripción de pago.

3.  Haga clic en **cambiar a otra oferta**. El botón sólo está disponible si se encuentra en el pago por uso y con el primer período de facturación.

    ![Observe el botón de la oferta de conmutador en el lado derecho de la página](./media/billing-how-to-switch-azure-offer/switchbutton.png)
    
4.  **Seleccione la oferta que desee** de la lista de ofertas, que puede cambiar su suscripción. Esta lista varía según la pertenencia a que su cuenta está asociada. Si no está disponible, compruebe la [lista de ofertas disponibles que puede cambiar](#whats-supported) y asegúrese de que tiene la pertenencia a derecha. 

    ![Seleccione una oferta que desee cambiar a](./media/billing-how-to-switch-azure-offer/selectoffer.png)

5.  Dependiendo de la oferta, que está cambiando, es posible que vea una nota sobre el impacto del cambio. Vaya a través de esta lista detenidamente y siga las instrucciones antes de continuar.

    ![Revise las notas](./media/billing-how-to-switch-azure-offer/thingstonote.png)

6.  Puede cambiar el nombre de la suscripción. De forma predeterminada, se establece en el nuevo nombre de la oferta. Haga clic en **Cambiar la oferta** para completar el proceso.

    ![Haga clic en el botón verde](./media/billing-how-to-switch-azure-offer/confirmpage.png)

7.  ¡Éxito! La suscripción ahora se cambia a la nueva oferta.

## <a name="why-cant-i-switch-offers"></a>¿Por qué no puedo cambiar ofertas?

No es posible que vea **cambiar a otra oferta** si:

- No está en el [pago por uso](https://azure.microsoft.com/offers/ms-azr-0003p/). Actualmente solo las suscripciones de pago por uso pueden cambiarlo a otra oferta.

    - Si se encuentra en la [Versión de prueba gratuita](https://azure.microsoft.com/free/), obtenga información sobre cómo [actualizar a pago por uso](billing-upgrade-azure-subscription.md).

    - Para cambiar la oferta de una suscripción diferente, [póngase en contacto con soporte técnico](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

- Está todavía en el primer período de facturación; debe esperar a que el primer período de facturación finalizar antes de poder cambiar ofertas.

Es posible que vea **hay ninguna oferta disponible en su región o país en este momento** si:

- No es apto para los modificadores de la oferta. Compruebe la [lista de ofertas disponibles que puede cambiar](#whats-supported).

## <a name="what-does-switching-azure-offers-do-to-my-service-and-billing"></a>¿Qué significa cambiar hacer ofertas de Azure a mi servicio y facturación?

Estos son los detalles de lo que sucede cuando cambia Azure planes en el centro de la cuenta.

### <a name="access-to-services"></a>Acceso a servicios

No hay ninguna interrupción del servicio para los usuarios asociada con la suscripción. Sin embargo, es posible que la oferta a que cambia tenga restricciones. Por ejemplo, algunas ofertas prohiben el uso de producción, por lo que necesite mover recursos de producción a otra suscripción.

### <a name="billing"></a>Facturación

En el día que cambie, se genera una factura para todos los cargos pendientes. A continuación, la suscripción se factura por términos de precios de la oferta nuevo. Su aniversario de facturación de la suscripción cambia a la fecha de modificación de ofertas. Uso y facturación datos antes de que no se conservará el cambio de la oferta, por lo que recomendamos descargar una copia antes de cambiar.

> [AZURE.NOTE] Debido a las restricciones relacionadas con la facturación, modificadores de oferta no son posibles en el primer ciclo de facturación después de crear una suscripción.

## <a name="can-i-migrate-from-pay-as-you-go-to-cloud-solution-providerhttpspartnermicrosoftcomsolutionscloud-reseller-overview-csp-or-enterprise-agreementhttpsazuremicrosoftcompricingenterprise-agreement-ea"></a>¿Puedo migrar de pago por uso para el [Proveedor de soluciones de nube](https://partner.microsoft.com/Solutions/cloud-reseller-overview) (CSP) o [Contrato Enterprise](https://azure.microsoft.com/pricing/enterprise-agreement/) (EA)?

Actualmente no se admite cambiar oferta CSP o EA en el centro de cuentas. Para mover su suscripción existente en EA, solicite al administrador de inscripción agregar su cuenta en el atributo extendido. A continuación, recibirá un correo electrónico de invitación. Si sigue las instrucciones para aceptar la invitación, las suscripciones se mueven automáticamente en el contrato Enterprise. Para migrar a CSP, consulte [Migración de suscripción de Azure a CSP](https://blogs.technet.microsoft.com/hybridcloudbp/2016/08/26/azure-subscription-migration-to-csp/).

## <a name="next-steps"></a>Pasos siguientes

- Obtenga información sobre cómo [Administrar roles de administrador](billing-add-change-azure-subscription-administrator.md) para su suscripción

- Realizar un seguimiento de su uso descargando [factura y datos de uso](billing-download-azure-invoice-daily-usage-date.md)

## <a name="need-help-contact-support"></a>¿Necesita ayuda? Póngase en contacto con soporte técnico.

Si aún tiene más preguntas, póngase [en contacto con soporte técnico](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para obtener el problema resuelto rápidamente.
