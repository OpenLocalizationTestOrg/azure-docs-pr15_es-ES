<properties
   pageTitle="Con imágenes de cliente de Windows para escenarios de desarrollo/prueba | Microsoft Azure"
   description="Cómo usar los beneficios de suscripción de Visual Studio para implementar Windows 8/7/10 en Azure para escenarios de pruebas y desarrollo"
   services="virtual-machines-windows"
   documentationCenter=""
   authors="iainfoulds"
   manager="timlt"
   editor=""/>

<tags
   ms.service="virtual-machines-windows"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-windows"
   ms.workload="infrastructure-services"
   ms.date="08/31/2016"
   ms.author="iainfou"/>

# <a name="using-windows-client-in-azure-for-devtest-scenarios"></a>Uso del cliente de Windows en Azure para escenarios de pruebas y desarrollo

Puede usar Windows 7, Windows 8 o Windows 10 en Azure para escenarios de desarrollo o probar proporcionado que tiene una suscripción de Visual Studio (anteriormente MSDN) correspondiente. En este artículo se describe los requisitos de elegibilidad para la ejecución de cliente de Windows en Azure y el uso de las imágenes de la Galería de Azure.


## <a name="subscription-eligibility"></a>Idoneidad de suscripción
Los suscriptores de Visual Studio activos (personas que han adquirido una licencia de suscripción de Visual Studio) pueden usar al cliente de Windows para desarrollo y pruebas. Cliente de Windows puede usarse en su propio hardware y Azure máquinas virtuales que se ejecutan en cualquier tipo de suscripción de Azure. Cliente de Windows no se implementa en o utilizado en Azure para uso en producción normal o utilizada por personas que no son suscriptores de Visual Studio activos.

Para su comodidad, hemos realizado ciertas imágenes de Windows 10 disponibles desde la Galería de Azure dentro de [desarrollo/prueba válida ofrece](#eligible-offers). Suscriptores visuales Studio dentro de cualquier tipo de oferta también pueden [Preparar adecuadamente y crear](virtual-machines-windows-prepare-for-upload-vhd-image.md) un 64 bits Windows 7, Windows 8, o imagen de Windows 10 y, a continuación, [cargar en Azure](virtual-machines-windows-upload-image.md). El uso permanece limitado a desarrollo o probar los suscriptores de Visual Studio activo.


## <a name="eligible-offers"></a>Oferta válida
La siguiente tabla detallan la oferta de identificadores que son elegibles implementar Windows 10 a través de la Galería de Azure. Las imágenes de Windows 10 sólo son visibles para las ofertas siguientes. Los suscriptores visuales Studio que necesitan para ejecutar el cliente de Windows en un tipo de oferta diferentes requieren que se [prepare adecuadamente y crear](virtual-machines-windows-prepare-for-upload-vhd-image.md) una 64 bits Windows 7, Windows 8 o Windows 10 imagen y [, a continuación, cargar en Azure](virtual-machines-windows-upload-image.md).

| Nombre de la oferta | Número de oferta | Imágenes de cliente disponibles |
|:-----------|:------------:|:-----------------------:|
| [Pruebas y desarrollo de pago por uso](https://azure.microsoft.com/offers/ms-azr-0023p/)                          | 0023P | Windows 10 |
| [Suscriptores Visual Studio Enterprise (MPN)](https://azure.microsoft.com/offers/ms-azr-0029p/)      | 0029P | Windows 10 |
| [Suscriptores visuales Studio Professional](https://azure.microsoft.com/offers/ms-azr-0059p/)          | 0059P | Windows 10 |
| [Suscriptores visuales Studio Test Professional](https://azure.microsoft.com/offers/ms-azr-0060p/)     | 0060P | Windows 10 |
| [Visual Studio Premium con MSDN (beneficio)](https://azure.microsoft.com/offers/ms-azr-0061p/)       | 0061P | Windows 10 |
| [Suscriptores visuales Studio Enterprise](https://azure.microsoft.com/offers/ms-azr-0063p/)            | 0063P | Windows 10 |
| [Suscriptores visuales Studio Enterprise (BizSpark)](https://azure.microsoft.com/offers/ms-azr-0064p/) | 0064P | Windows 10 |
| [Pruebas y desarrollo empresarial](https://azure.microsoft.com/ofers/ms-azr-0148p/)                              | 0148P | Windows 10 |


## <a name="check-your-azure-subscription"></a>Compruebe su suscripción de Azure
Si no conoce el identificador de la oferta, puede obtener en el portal de Azure o en el portal de la cuenta.

El identificador de oferta de suscripción se indica en el módulo 'Suscripciones' dentro del portal de Azure:

![Detalles del identificador de oferta desde el portal de Azure](./media/virtual-machines-windows-client-images/offer_id_azure_portal.png) 

También puede ver el identificador de oferta en la [ficha 'Suscripciones'](http://account.windowsazure.com/Subscriptions) del portal de la cuenta de Azure:

![Detalles del identificador de oferta desde el portal de la cuenta de Azure](./media/virtual-machines-windows-client-images/offer_id_azure_account_portal.png) 


## <a name="next-steps"></a>Pasos siguientes
Ahora puede implementar sus máquinas virtuales con [PowerShell](virtual-machines-windows-ps-create.md), [plantillas de administrador de recursos](virtual-machines-windows-ps-template.md)o [Visual Studio](../vs-azure-tools-resource-groups-deployment-projects-create-deploy.md).
