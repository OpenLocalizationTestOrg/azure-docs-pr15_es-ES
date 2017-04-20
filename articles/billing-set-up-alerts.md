<properties
    pageTitle="Configurar alertas para las suscripciones de Microsoft Azure de facturación | Microsoft Azure"
    description="Describe cómo puede configurar alertas en su factura Azure para evitar sorpresas facturación."
    services=""
    documentationCenter=""
    authors="vikdesai"
    manager="mbaldwin"
    editor=""
    tags="billing"
    />

<tags
    ms.service="billing"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/18/2016"
    ms.author="vikdesai"/>

# <a name="set-up-billing-alerts-for-your-microsoft-azure-subscriptions"></a>Configurar las alertas de facturación para las suscripciones de Microsoft Azure

¿Le preocupa cuánto van a gastar cada mes para su suscripción de Azure? Si es el Administrador de la cuenta para una suscripción de Azure, puede usar el servicio de alerta de facturación de Azure para crear personalizado facturación alertas que le ayudarán a supervisar y administrar la actividad de facturación para sus cuentas de Azure.

Este servicio es un servicio de vista previa, por lo que lo primero que tiene que hacer es signo hacia arriba. Visite [la página características de la vista previa](https://account.windowsazure.com/PreviewFeatures) en el portal de administración de cuenta de Azure para habilitar esta característica.

## <a name="set-the-alert-threshold-and-email-recipients"></a>Establecer a los destinatarios de correo electrónico y umbral de alerta

Después de recibir la confirmación de correo electrónico que está activado el servicio de facturación para la suscripción, visite [la página de suscripciones](https://account.windowsazure.com/Subscriptions) en el portal de la cuenta. Haga clic en la suscripción que desea supervisar y, a continuación, haga clic en **alertas**.

![][Image1]

A continuación, haga clic en **Agregar aviso** para crear su primer organigrama, puede configurar un total de cinco alertas facturación por suscripción con un umbral diferente y hasta dos destinatarios de correo electrónico para cada alerta.

![][Image2]

Cuando agrega una alerta, asígnele un nombre único, elija un umbral de gasto y elija las direcciones de correo electrónico que se envían las alertas. Al configurar el umbral, puede elegir un **Total de facturación** o un **Crédito monetarios** de la lista **De alerta** . Para un total de facturación, se envía una alerta cuando el gasto de suscripción supera el umbral. Para obtener un crédito monetario, se envía una alerta cuando coloca créditos monetarios por debajo del límite. Créditos monetarios normalmente se aplican a las versiones de prueba gratuitas y suscripciones asociadas con las cuentas MSDN.

![][Image3]

Azure es compatible con cualquier dirección de correo electrónico, pero no compruebe que la dirección de correo electrónico funciona, así que vuelva a comprobar si hay errores ortográficos.

## <a name="check-on-your-alerts"></a>Activar las alertas

Después de configurar las alertas, el centro de cuenta enumera y muestra cuántos más puede configurar. Para cada alerta, verá la fecha y hora que se envió, ya sea una alerta para facturación Total o crédito monetarios y el límite de que configuración. El formato de fecha y hora es 24 horas el tiempo Universal coordinado (UTC) y la fecha es el formato dd-mm-aaaa. Haga clic en el signo más de una alerta en la lista de edición o haga clic en la Papelera para eliminarlo.

[Image1]: ./media/azure-billing-set-up-alerts/billingalert1.png
[Image2]: ./media/azure-billing-set-up-alerts/billingalert2.png
[Image3]: ./media/azure-billing-set-up-alerts/billingalerts3.png
