<properties
    pageTitle="Las cuotas de servicio y los límites por lotes | Microsoft Azure"
    description="Obtenga más información sobre las restricciones, los límites y las cuotas de Azure lote predeterminado y, a continuación, cómo solicitar cuota aumenta"
    services="batch"
    documentationCenter=""
    authors="mmacy"
    manager="timlt"
    editor=""/>

<tags
    ms.service="batch"
    ms.workload="big-compute"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/10/2016"
    ms.author="marsma"/>

# <a name="quotas-and-limits-for-the-azure-batch-service"></a>Las cuotas y límites para el servicio del lote de Azure

Como con otros servicios de Azure, hay límites determinados recursos asociados con el servicio de lote. Muchos de estos límites son cuotas predeterminadas aplicadas por Azure en el nivel de cuenta o de suscripción. Este artículo describen los valores predeterminados y cómo puede solicitar cuota aumenta.

Si planea ejecutar cargas de trabajo de producción por lotes, debe aumentar una o varias de las cuotas por encima de la predeterminada. Si desea elevar una cuota, puede abrir un online [solicitud de soporte al cliente](#increase-a-quota) sin cargo.

>[AZURE.NOTE] Una cuota es un límite de crédito, no se garantiza la capacidad. Si tiene necesidades de capacidad a gran escala, póngase en contacto con el soporte técnico de Azure.

## <a name="subscription-quotas"></a>Cuotas de suscripción
**Recursos**|**Límite predeterminado**|**Límite máximo**
---|---|---
Cuentas de lote por región por suscripción | 1 | 50

## <a name="batch-account-quotas"></a>Cuotas de cuenta por lotes
[AZURE.INCLUDE [azure-batch-limits](../../includes/azure-batch-limits.md)]

## <a name="other-limits"></a>Otros límites
**Recursos**|**Límite máximo**
---|---
[Tareas simultáneas](batch-parallel-node-tasks.md) por nodo de cálculo | número de x 4 de núcleos de nodo
[Aplicaciones](batch-application-packages.md) de cada cuenta por lotes        | 20
Paquetes de aplicación por aplicación  | 40
Tamaño del paquete de aplicación (cada uno)       | Aproximadamente 195GB<sup>1</sup>

Límite de almacenamiento de azure de <sup>1</sup> para el tamaño máximo de bloque blob

## <a name="view-batch-quotas"></a>Ver las cuotas de proceso por lotes

Ver las cuotas de cuenta por lotes en el [portal de Azure][portal].

1. Seleccione **cuentas de lote** en el portal y luego seleccione la cuenta de proceso por lotes que le interesa.

2. Seleccione **Propiedades** en el módulo de menú de la cuenta de proceso por lotes

3. La hoja de propiedades muestra las **cuotas** aplicado actualmente a la cuenta de proceso por lotes

    ![Cuotas de cuenta por lotes][account_quotas]

## <a name="increase-a-quota"></a>Aumentar cuotas

Siga los pasos siguientes para solicitar una cuota aumentan con el [portal de Azure][portal].

1. Seleccione el icono de **ayuda + soporte técnico** en su panel portal o el signo de interrogación (?****) en la esquina superior derecha del portal.

2. Seleccione **nueva solicitud de soporte técnico** > **conceptos básicos**.

3. En el módulo de **conceptos básicos** :

    una. **Tipo de problema** > **cuota**

    b. Seleccione la suscripción.

    c. **Tipo de cuota** > **por lotes**

    d. **Plan de asistencia** > **soporte de cuota - incluido**

    Haga clic en **siguiente**.

4. En el módulo del **problema** :

    una. Seleccione una **gravedad** según el [impacto en el negocio][support_sev].

    b. En **Detalles**, especifique cada cuota que desea cambiar el nombre de cuenta del lote y el límite de nuevo.

    Haga clic en **siguiente**.

5. En el módulo de la **información de contacto** :

    una. Seleccione un **método de contacto preferido**.

    b. Compruebe y escriba los detalles de contacto necesarios.

    Haga clic en **crear** para enviar la solicitud de soporte técnico.

Una vez que ha enviado su solicitud de soporte técnico, soporte Azure se comunicará con usted. Tenga en cuenta que completar la solicitud puede tardar hasta 2 días laborables.

## <a name="related-topics"></a>Temas relacionados

* [Crear una cuenta de Azure lote con el portal de Azure](batch-account-create-portal.md)

* [Introducción a las características lote Azure](batch-api-basics.md)

* [Suscripción de Azure y los límites del servicio, cuotas y restricciones](../azure-subscription-service-limits.md)

[portal]: https://portal.azure.com
[portal_classic_increase]: https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/
[support_sev]: http://aka.ms/supportseverity

[account_quotas]: ./media/batch-quota-limit/accountquota_portal.PNG
