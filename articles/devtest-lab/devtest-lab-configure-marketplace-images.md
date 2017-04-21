<properties
    pageTitle="Configurar las opciones de imagen de Azure Marketplace de prácticas de Azure DevTest | Microsoft Azure"
    description="Configurar las imágenes de Azure Marketplace pueden utilizarse para crear una máquina virtual en Azure DevTest prácticas"
    services="devtest-lab,virtual-machines"
    documentationCenter="na"
    authors="tomarcher"
    manager="douge"
    editor=""/>

<tags
    ms.service="devtest-lab"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/06/2016"
    ms.author="tarcher"/>

# <a name="configure-azure-marketplace-image-settings-in-azure-devtest-labs"></a>Configurar las opciones de imagen de Azure Marketplace de Azure DevTest prácticas

Prácticas de DevTest es compatible con la creación VM basadas en imágenes de Azure Marketplace dependiendo de cómo haya configurado las imágenes de Azure Marketplace para usarse en la práctica. Este artículo le muestra cómo especificar que, si la hay, imágenes de Azure Marketplace se pueden utilizar al crear máquinas virtuales en un laboratorio.

## <a name="select-which-azure-marketplace-images-are-allowed-when-creating-a-vm"></a>Seleccione las imágenes de Azure Marketplace permitidas al crear una máquina virtual

1. Inicie sesión en el [portal de Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Seleccione **Más servicios**y, a continuación, seleccione **DevTest prácticas** de la lista.

1. En la lista de prácticas, seleccione la práctica deseada. 

1. En el módulo de la práctica, seleccione **configuración**.
    
1. En el módulo de **configuración** de la práctica, seleccione **imágenes de Marketplace**

1. Especifique si desea que todas las imágenes de Azure Marketplace completa esté disponible para su uso como base de una nueva máquina virtual. Si selecciona **Sí**, todas las imágenes de Azure Marketplace que cumplen todos los criterios siguientes se permiten en la práctica:

    - La imagen crea una única VM **y**
    - La imagen se utiliza el Administrador de recursos de Azure aprovisionar máquinas virtuales **y**
    - La imagen no requiere la compra de un plan de licencia adicionales
    
    Si desea que no hay imágenes para poder, o desea especificar las imágenes que se pueden utilizar, seleccione **No**.
 
    ![Opción para permitir que se pueden usar como bases imágenes para máquinas virtuales de todas las imágenes del catálogo de soluciones](./media/devtest-lab-configure-marketplace-images/allow-all-marketplace-images.png)
 
1. Si selecciona **No** en el paso anterior, se activa la casilla de **Permitidos imágenes o seleccionar todo** . Puede usar esta opción junto con el cuadro de búsqueda para seleccionar o anular la selección de todos los elementos que se muestran en la lista rápidamente.
También puede seleccionar las imágenes de Azure Marketplace que desea permitir la creación de VM individualmente activando la casilla de verificación correspondiente de la imagen.
Seleccione nada de la lista si no desea permitir que las imágenes de Azure Marketplace para utilizarse en la práctica.

    ![Puede especificar qué imágenes de Azure Marketplace pueden usarse como imágenes bases para máquinas virtuales](./media/devtest-lab-configure-marketplace-images/select-marketplace-images.png)

[AZURE.INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>Pasos siguientes

Una vez que haya configurado cómo imágenes de Azure Marketplace permitidas al crear una máquina virtual, el siguiente paso es [Agregar una máquina virtual para el laboratorio](./devtest-lab-add-vm-with-artifacts.md).