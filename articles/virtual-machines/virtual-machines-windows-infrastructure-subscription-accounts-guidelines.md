<properties
    pageTitle="Directrices de cuentas y de suscripción | Microsoft Azure"
    description="Obtenga información sobre las directrices de diseño e implementación claves para cuentas de Azure y suscripciones."
    documentationCenter=""
    services="virtual-machines-windows"
    authors="iainfoulds"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/08/2016"
    ms.author="iainfou"/>

# <a name="subscription-and-accounts-guidelines"></a>Directrices de cuentas y suscripción

[AZURE.INCLUDE [virtual-machines-windows-infrastructure-guidelines-intro](../../includes/virtual-machines-windows-infrastructure-guidelines-intro.md)] 

En este artículo se centra en comprender cómo enfocar la suscripción y administración de cuentas como su entorno y base de usuarios crece.


## <a name="implementation-guidelines-for-subscriptions-and-accounts"></a>Directrices de implementación de suscripciones y cuentas

Decisiones:

- ¿Qué conjunto de suscripciones y cuentas debe hospedar su carga de trabajo de TI o infraestructura?
- ¿Cómo puedo desglosar la jerarquía para ajustar su organización?

Tareas:

- Definir su jerarquía lógica de la organización tal y como administrar desde un nivel de suscripción.
- Para hacer coincidir esta jerarquía lógica, defina las cuentas necesarias y suscripciones en cada cuenta.
- Cree el conjunto de suscripciones y cuentas utilizando la convención de nomenclatura.


## <a name="subscriptions-and-accounts"></a>Las suscripciones y cuentas

Para trabajar con Azure, necesita una o varias suscripciones de Azure. Recursos como máquinas virtuales (VM) o redes virtuales existen en de las suscripciones.

- Los clientes empresariales suelen tienen la inscripción Enterprise, que es el recurso de nivel superior de la jerarquía y está asociado a una o varias cuentas.
- Los consumidores y a los clientes sin la inscripción de empresa, el recurso de nivel superior es la cuenta.
- Las suscripciones están asociadas a las cuentas y puede haber una o más suscripciones por cuenta. Registros de Azure información en el nivel de suscripción de facturación.

Debido al límite de dos niveles de jerarquía en la relación de cuenta o suscripción, es importante alinear la convención de nomenclatura de cuentas y las suscripciones a las necesidades de facturación. Por ejemplo, si una empresa global utiliza Azure, puede elegir tener una cuenta por región y se administran suscripciones en el nivel de región:

![](./media/virtual-machines-common-infrastructure-service-guidelines/sub01.png)

Por ejemplo, puede usar la estructura siguiente:

![](./media/virtual-machines-common-infrastructure-service-guidelines/sub02.png)

Si decide que una región tiene más de una suscripción asociada a un grupo determinado, la convención de nomenclatura debe incorporar una manera para codificar los datos adicionales en la cuenta o el nombre de suscripción. Esta organización permite corporativos datos de facturación para generar los nuevos niveles de jerarquía durante los informes de facturación:

![](./media/virtual-machines-common-infrastructure-service-guidelines/sub03.png)

La organización puede tener un aspecto similar al siguiente:

![](./media/virtual-machines-common-infrastructure-service-guidelines/sub04.png)

Le proporcionamos facturación detallada a través de un archivo descargable para una sola cuenta o para todas las cuentas en un contrato enterprise.


## <a name="next-steps"></a>Pasos siguientes

[AZURE.INCLUDE [virtual-machines-windows-infrastructure-guidelines-next-steps](../../includes/virtual-machines-windows-infrastructure-guidelines-next-steps.md)] 