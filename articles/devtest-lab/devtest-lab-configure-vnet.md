<properties
    pageTitle="Configurar una red virtual en Azure DevTest prácticas | Microsoft Azure"
    description="Obtenga información sobre cómo configurar una red virtual existente y subred y usarlos en una máquina virtual con Azure DevTest prácticas"
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

# <a name="configure-a-virtual-network-in-azure-devtest-labs"></a>Configurar una red virtual en Azure DevTest prácticas

Como se explica en el artículo, [Agregar una máquina virtual con artefactos a un laboratorio](devtest-lab-add-vm-with-artifacts.md), cuando se crea una máquina virtual en un laboratorio, puede especificar una red virtual configurada. Un escenario para hacer esto es si necesita tener acceso a los recursos de la red corporativa desde sus máquinas virtuales con la red virtual que se ha configurado con ExpressRoute o VPN de sitio a otro. Las secciones siguientes muestran cómo agregar su red virtual existente en la configuración de red Virtual del laboratorio para que esté disponible para elegir al crear máquinas virtuales.

## <a name="configure-a-virtual-network-for-a-lab-using-the-azure-portal"></a>Configurar una red virtual para una práctica con el portal de Azure
Los siguientes pasos mostrarle agregando una red virtual existente (y subred) a un laboratorio de forma que se puede usar al crear una máquina virtual en el mismo laboratorio. 

1. Inicie sesión en el [portal de Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Seleccione **Más servicios**y, a continuación, seleccione **DevTest prácticas** de la lista.

1. En la lista de prácticas, seleccione la práctica deseada. 

1. En el módulo de la práctica, seleccione **configuración**.

1. En el módulo de **configuración** de la práctica, seleccione **Virtual redes**.

1. En el módulo de **redes virtuales** , verá una lista de redes virtuales configurada para la práctica actual, así como la red virtual predeterminada que se crea para su laboratorio. 

1. Seleccione **+ Agregar**.

    ![Agregar una red virtual existente a la práctica](./media/devtest-lab-configure-vnet/lab-settings-vnet-add.png)
    
1. En el módulo de **red Virtual** , seleccione **[seleccionar una red virtual]**.

    ![Seleccione una red virtual existente](./media/devtest-lab-configure-vnet/lab-settings-vnets-vnet1.png)
    
1. En el módulo de **Elegir una red virtual** , seleccione la red virtual deseada. El módulo muestra todas las redes virtuales que están en la misma región en la suscripción de la práctica.  

1. Después de seleccionar una red virtual, se devuelven a módulo de **red Virtual** y se habilitan varios campos.  

    ![Seleccione una red virtual existente](./media/devtest-lab-configure-vnet/lab-settings-vnets-vnet2.png)

1. Especifique una descripción para su red virtual o combinación de práctica.

1. Para permitir una subred que se utilizará en práctica creación VM, seleccione **Utilizar en la creación de máquina VIRTUAL**.

1. Para permitir que las direcciones IP públicas en una subred, seleccione **Permitir IP pública**.

1. En el campo **Máximo máquinas virtuales por usuario** , especifique las VM máximas por usuario para cada subred. Si desea un número de máquinas virtuales sin restricciones, deje este campo en blanco.

1. Seleccione **Guardar**.

1. Ahora que se configura la red virtual, se puede seleccionar al crear una máquina virtual. Para ver cómo crear una máquina virtual y especifique una red virtual, consulte el artículo, [Agregar una máquina virtual con artefactos a un laboratorio](devtest-lab-add-vm-with-artifacts.md). 

[AZURE.INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>Pasos siguientes

Una vez que haya agregado la red virtual deseada en la práctica, el siguiente paso es [Agregar una máquina virtual para el laboratorio](devtest-lab-add-vm-with-artifacts.md).