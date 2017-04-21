<properties
   pageTitle="Cómo etiquetar una máquina virtual Linux | Microsoft Azure"
   description="Obtenga información sobre el etiquetado de un equipo virtual de Linux creado en Azure utiliza el modelo de implementación de administrador de recursos."
   services="virtual-machines-linux"
   documentationCenter=""
   authors="mmccrory"
   manager="timlt"
   editor="tysonn"
   tags="azure-resource-manager"/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure-services"
   ms.date="07/05/2016"
   ms.author="memccror"/>

# <a name="how-to-tag-a-linux-virtual-machine-in-azure"></a>Cómo etiquetar una máquina virtual Linux en Azure

Este artículo describe diferentes formas para etiquetar un equipo virtual Linux en Azure a través del modelo de implementación de administrador de recursos. Las etiquetas son pares de clave y valor definidos por el usuario que se pueden colocar directamente en un recurso o un grupo de recursos. Azure actualmente es compatible con un máximo de 15 etiquetas por recurso y grupo de recursos. Las etiquetas se pueden colocadas en un recurso en el momento de creación o agregadas a un recurso existente. Tenga en cuenta recursos creados mediante el modelo de implementación de administrador de recursos solo son compatibles con las etiquetas.

[AZURE.INCLUDE [virtual-machines-common-tag](../../includes/virtual-machines-common-tag.md)]

## <a name="tagging-with-azure-cli"></a>Etiquetado con CLI Azure

Para empezar, [instalar y configurar la CLI de Azure](../xplat-cli-azure-resource-manager.md) y asegúrese de que está en modo de administrador de recursos (`azure config mode arm`).

Puede ver todas las propiedades para una determinada Máquina Virtual, incluidas las etiquetas, con este comando:

        azure vm show -g MyResourceGroup -n MyTestVM

Para agregar una nueva etiqueta VM mediante la CLI de Azure, puede usar el `azure vm set` comando junto con el parámetro de etiqueta **t-**:

        azure vm set -g MyResourceGroup -n MyTestVM –t myNewTagName1=myNewTagValue1;myNewTagName2=myNewTagValue2

Para quitar todas las etiquetas, puede usar el parámetro **– T** en el `azure vm set` comando.

        azure vm set – g MyResourceGroup –n MyTestVM -T


Ahora que hemos aplicado etiquetas a nuestros recursos CLI Azure y el Portal, echemos un vistazo a la información de uso para ver las etiquetas en el portal de facturación.

[AZURE.INCLUDE [virtual-machines-common-tag-usage](../../includes/virtual-machines-common-tag-usage.md)]

## <a name="next-steps"></a>Pasos siguientes

* Para obtener más información sobre el etiquetado de los recursos de Azure, consulte [Información general del Administrador de recursos de Azure][] y [Utilizar etiquetas para organizar los recursos de Azure][].
* Para ver cómo las etiquetas pueden ayudarle a administrar el uso de recursos de Azure, vea [comprender su factura de Azure][] y [obtener recomendaciones sobre el consumo de recursos de Microsoft Azure][].





[Azure CLI environment]: ./xplat-cli-azure-resource-manager.md
[Información general de administrador de recursos de Azure]: ../azure-resource-manager/resource-group-overview.md
[Usar etiquetas para organizar los recursos de Azure]: ../resource-group-using-tags.md
[Descripción de la factura de Azure]: ../billing/billing-understand-your-bill.md
[Obtener información sobre el consumo de recursos de Microsoft Azure]: ../billing-usage-rate-card-overview.md
