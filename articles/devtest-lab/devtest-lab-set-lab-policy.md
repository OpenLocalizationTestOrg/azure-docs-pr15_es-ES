<properties
    pageTitle="Definir directivas de laboratorio de prácticas de Azure DevTest | Microsoft Azure"
    description="Obtenga información sobre cómo definir directivas de laboratorio como tamaños VM, máximos máquinas virtuales por el usuario y la automatización de cierre."
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
    ms.date="09/12/2016"
    ms.author="tarcher"/>

# <a name="define-lab-policies-in-azure-devtest-labs"></a>Definir directivas de práctica en Azure DevTest prácticas

> [AZURE.VIDEO how-to-set-vm-policies-in-a-devtest-lab]

Prácticas de Azure DevTest permite especificar directivas de clave que le ayudarán a controlar los costos y minimizar residuos en sus prácticas. Estas directivas de práctica incluyen el número máximo de VM creado por usuario y por laboratorio y distintas opciones de apagado automático e inicio automático. 

## <a name="accessing-a-labs-policies-in-azure-devtest-labs"></a>Acceder a directivas del laboratorio en Azure DevTest prácticas

Los pasos siguientes guían configurar directivas para una práctica en Azure DevTest prácticas:

Para ver las directivas para un laboratorio (y cambiar), siga estos pasos:

1. Inicie sesión en el [portal de Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Seleccione **más servicios**y, a continuación, seleccione **DevTest prácticas** de la lista.

1. En la lista de prácticas, seleccione la práctica deseada.   

1. Seleccione **configuración de la directiva**.

1. El módulo de **configuración de directiva** contiene un menú de configuración que puede especificar: 

    ![Módulo de configuración de directiva](./media/devtest-lab-set-lab-policy/policies.png)

    Para obtener más información sobre la configuración de una directiva, selecciónelo en la lista siguiente:

    - [Tamaños de máquina virtual permitidos](#set-allowed-virtual-machine-sizes) : seleccione la lista de tamaños VM permitida en la práctica. Un usuario puede crear máquinas virtuales sólo de esta lista.

    - [Máquinas virtuales por usuario](#set-virtual-machines-per-user) - especificar el número máximo de máquinas virtuales que se pueden crear un usuario. 

    - [Máquinas virtuales por laboratorio](#set-virtual-machines-per-lab) - especificar el número máximo de máquinas virtuales que se pueden crear para una práctica. 

    - [Apagado automático](#set-auto-shutdown) : especifique la hora cuando la práctica actual de máquinas virtuales se cierra automáticamente.

    - [Inicio automático](#set-auto-start) : especifique la hora cuando máquinas virtuales de la práctica actual inicien automáticamente.

## <a name="set-allowed-virtual-machine-sizes"></a>Establezca permitida tamaños de máquina virtual

La directiva para configurar los tamaños VM permitidos ayuda a minimizar laboratorio residuos por lo que le permite especificar los tamaños VM se permiten en la práctica. Si está activada esta directiva, sólo los tamaños de máquina virtual de esta lista pueden usarse para crear máquinas virtuales.

1. En el módulo de **configuración de la directiva** de la práctica, seleccione **tamaños de máquinas virtuales de permitidos**.

    ![Tamaños permitidos máquinas virtuales de Windows](./media/devtest-lab-set-lab-policy/allowed-vm-sizes.png)
 
1. Seleccione **en** habilitar esta directiva y **desactivar** para desactivarlo.

1. Si habilita esta directiva, seleccione uno o varios tamaños VM que pueden crearse en la práctica.

1. Seleccione **Guardar**.

## <a name="set-virtual-machines-per-user"></a>Máquinas virtuales de conjunto por usuario

La directiva de **máquinas virtuales por usuario** le permite especificar el número máximo de máquinas virtuales que se pueden crear un usuario individual. Si un usuario intenta crear una máquina virtual cuando se ha alcanzado el límite de usuarios, un mensaje de error indica que no se puede crear la máquina virtual. 

1. En el módulo de **configuración de la directiva** de la práctica, seleccione **máquinas virtuales por usuario**.

    ![Máquinas virtuales por el usuario](./media/devtest-lab-set-lab-policy/max-vms-per-user.png)

1. Seleccione **en** habilitar esta directiva y **desactivar** para desactivarlo.

1. Si habilita esta directiva, escriba un valor numérico que indica el número máximo de máquinas virtuales que se puede crear un usuario. Si especifica un número que no es válido, la interfaz de usuario muestra el número máximo permitido para este campo.

1. Seleccione **Guardar**.

## <a name="set-virtual-machines-per-lab"></a>Máquinas virtuales de conjunto por práctica

La directiva de **máquinas virtuales por laboratorio** le permite especificar el número máximo de máquinas virtuales que se pueden crear de la práctica actual. Si un usuario intenta crear una máquina virtual cuando se ha alcanzado el límite de la práctica, un mensaje de error indica que no se puede crear la máquina virtual. 

1. En el módulo de **configuración de la directiva** de la práctica, seleccione **máquinas virtuales por laboratorio**.

    ![Máquinas virtuales por práctica](./media/devtest-lab-set-lab-policy/total-vms-allowed.png)

1. Seleccione **en** habilitar esta directiva y **desactivar** para desactivarlo.

1. Si habilita esta directiva, escriba un valor numérico que indica el número máximo de máquinas virtuales que se pueden crear de la práctica actual. Si especifica un número que no es válido, la interfaz de usuario muestra el número máximo permitido para este campo.

1. Seleccione **Guardar**.

## <a name="set-auto-shutdown"></a>Establecer apagado automático

La directiva de apagado automático ayuda a minimizar laboratorio residuos por lo que le permite especificar el tiempo que apagar máquinas virtuales de esta práctica.

1. En el módulo de **configuración de la directiva** de la práctica, seleccione **apagado automático**.

    ![Apagado automático](./media/devtest-lab-set-lab-policy/auto-shutdown.png)

1. Seleccione **en** habilitar esta directiva y **desactivar** para desactivarlo.

1. Si habilita esta directiva, especifique la hora local para cerrar todas las máquinas virtuales de la práctica actual.

1. Seleccione **Guardar**.

1. De forma predeterminada, una vez habilitada, esta directiva se aplica a todas las máquinas virtuales de la práctica actual. Para quitar esta configuración de una máquina virtual específica, abra la hoja de la máquina virtual y cambie su configuración de **apagado automático** 

## <a name="set-auto-start"></a>Establecer inicio automático

La directiva de inicio automático le permite especificar cuándo se deben iniciar las máquinas virtuales de la práctica actual.  

1. En el módulo de **configuración de la directiva** de la práctica, seleccione **Inicio automático**.

    ![Inicio automático](./media/devtest-lab-set-lab-policy/auto-start.png)

1. Seleccione **en** habilitar esta directiva y **desactivar** para desactivarlo.

1. Si habilita esta directiva, especifique al local programada hora de inicio y los días de la semana correspondiente a la vez. 

1. Seleccione **Guardar**.

1. Una vez habilitada, esta directiva no se aplica automáticamente a cualquier máquinas virtuales de la práctica actual. Para aplicar esta configuración a una máquina virtual específica, abra la hoja de la máquina virtual y cambie su configuración de **Inicio automático** 

[AZURE.INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>Pasos siguientes

Una vez que haya definido y aplicar diferentes configuraciones de directiva de máquina virtual para su laboratorio, estas son algunas de las cosas que puede probar a continuación:

- [Administración de costos de configurar](./devtest-lab-configure-cost-management.md) - ilustra cómo utilizar el gráfico de **Tendencias de coste mensual estimado**  
Para ver el mes actual del estimada costo hasta la fecha y el costo previsto de fin de mes.
- [Crear imagen personalizada](./devtest-lab-create-template.md) : cuando se crea una máquina virtual, especifique una base, que puede ser una imagen personalizada o una imagen del catálogo de soluciones. En este artículo muestra cómo crear una imagen personalizada desde un archivo de disco duro virtual.
- [Configurar Marketplace imágenes](./devtest-lab-configure-marketplace-images.md) - Azure DevTest prácticas admite la creación de máquinas virtuales basadas en imágenes de Azure Marketplace. Este artículo muestra cómo especificar que, si la hay, imágenes de Azure Marketplace se pueden utilizar al crear máquinas virtuales en un laboratorio.
- [Crear una máquina virtual en un laboratorio](./devtest-lab-add-vm-with-artifacts.md) - muestra cómo crear una máquina virtual de una imagen de base (ambos personalizada o Marketplace) y cómo trabajar con defectos en la máquina virtual.
