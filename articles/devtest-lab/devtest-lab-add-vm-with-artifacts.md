<properties
    pageTitle="Agregar una máquina virtual con artefactos a un laboratorio de prácticas de Azure DevTest | Microsoft Azure"
    description="Obtenga información sobre cómo agregar una máquina virtual con artefactos en Azure DevTest prácticas"
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
    ms.date="08/30/2016"
    ms.author="tarcher"/>

# <a name="add-a-vm-with-artifacts-to-a-lab-in-azure-devtest-labs"></a>Agregar una máquina virtual con artefactos a un laboratorio en Azure DevTest prácticas

> [AZURE.VIDEO how-to-create-vms-with-artifacts-in-a-devtest-lab]

Crear una máquina virtual en un laboratorio desde una *base* que es una [imagen personalizada](./devtest-lab-create-template.md), [fórmula](./devtest-lab-manage-formulas.md)o [imagen de catálogo de soluciones](./devtest-lab-configure-marketplace-images.md).

DevTest prácticas *artefactos* le permiten especificar *acciones* que se realizan cuando se crea la máquina virtual. 

Acciones de muestra pueden realizar los procedimientos, como la ejecución de scripts de Windows PowerShell, ejecute los comandos de fiesta e instalar software. 

Muestra *parámetros* le permiten personalizar la muestra para su situación particular.

Este artículo le muestra cómo crear una máquina virtual en la práctica con artefactos.

## <a name="add-a-vm-with-artifacts"></a>Agregar una máquina virtual con artefactos

1. Inicie sesión en el [portal de Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Seleccione **Más servicios**y, a continuación, seleccione **DevTest prácticas** de la lista.

1. En la lista de prácticas, seleccione la práctica en el que desea crear la máquina virtual.  

1. En el módulo de **información general** de la práctica, seleccione **+ Máquina Virtual**.  
    ![Agregar botón VM](./media/devtest-lab-add-vm-with-artifacts/devtestlab-home-blade-add-vm.png)

1. En el módulo de **Elegir una base** , seleccione una base para la máquina virtual.

1. En el módulo de **Máquina Virtual** , escriba un nombre para la nueva máquina virtual en el cuadro de texto **nombre de la máquina Virtual** .

    ![Módulo de máquina virtual de práctica](./media/devtest-lab-add-vm-with-artifacts/devtestlab-lab-vm-blade.png)

1. Escriba un **Nombre de usuario** que se concederá privilegios de administrador en la máquina virtual.  

1. Si desea usar una contraseña almacenada en la *tienda secreta*, seleccione **usar información confidencial desde mi tienda secreta**y especifique un valor de clave que corresponde a su secreto (contraseña). En caso contrario, simplemente escriba una contraseña en el campo de texto, **Escriba un valor de**la etiqueta.
 
1. Seleccione el **tamaño de la máquina Virtual** y seleccione uno de los elementos predefinidos que especifican las muestras de procesador, el tamaño de RAM y el tamaño de la unidad de disco duro de la máquina virtual para crear.

1. Seleccione la **red Virtual** y la red virtual deseada.

1. Seleccione **subred** y subred.

1. Si se establece la directiva de práctica para permitir que las direcciones IP públicas para la subred seleccionada, especifique si desea que la dirección IP sea público, seleccione **Sí** o **No**. En caso contrario, esta opción está deshabilitada y seleccionada como **No**. 

1. Seleccionar **artefactos** y - de la lista de artefactos - seleccionar y configurar los artefactos que desea agregar a la imagen base. 
**Nota:** Si es nuevo en DevTest prácticas o artefactos configuración, vaya a la sección [Agregar un efecto a una máquina virtual existente](#add-an-existing-artifact-to-a-vm) y volver aquí cuando haya terminado.

1. Si desea ver o copie la plantilla de administrador de recursos de Azure, vaya a la sección del [Administrador de recursos de Azure Guardar plantilla](#save-arm-template) y volver aquí cuando haya terminado.

1. Seleccione **crear** para agregar la máquina virtual especificada a la práctica.

1. El módulo de práctica muestra el estado de la creación de la máquina virtual; en primer lugar como **crear**, a continuación, como **Ejecutar** después de la máquina virtual se ha iniciado.

1. Vaya a la sección [Pasos siguientes](#next-steps) . 

## <a name="add-an-existing-artifact-to-a-vm"></a>Agregar un efecto existente a una máquina virtual

Al crear una máquina virtual, puede agregar artefactos existentes. Cada laboratorio incluye artefactos desde el repositorio de muestra de prácticas DevTest público, así como artefactos que ha creado y agregado a su propio repositorio de muestra.
Para descubrir cómo crear artefactos, vea el artículo, [Aprenda a crear sus propio artefactos para su uso con DevTest prácticas](devtest-lab-artifact-author.md).

1. En el módulo de **Máquina Virtual** , seleccione **artefactos**. 

1. En el módulo de **Agregar artefactos** , seleccione la muestra que desee.  

    ![Agregar placa artefactos](./media/devtest-lab-add-vm-with-artifacts/devtestlab-add-artifact-blade.png)

1. Escriba los valores de parámetro necesario y los parámetros opcionales que necesita.  

1. Seleccione **Agregar** para agregar la muestra y volver a la placa **Agregar artefactos** .

1. Continúe agregando artefactos según sea necesario para su máquina virtual.

1. Una vez que ha agregado su artefactos, puede [cambiar el orden en que se ejecutan los artefactos](#change-the-order-in-which-artifacts-are-run). También puede volver a [Ver o modificar un efecto](#view-or-modify-an-artifact).

## <a name="change-the-order-in-which-artifacts-are-run"></a>Cambiar el orden en que se ejecutan artefactos

De forma predeterminada, las acciones de los artefactos se ejecutan en el orden en que se agregan a la máquina virtual. Los pasos siguientes muestran cómo cambiar el orden en que se ejecutan los artefactos.

1. En la parte superior del módulo **Agregar artefactos** , seleccione el vínculo que indica el número de artefactos que se han agregado a la máquina virtual.

    ![Número de artefactos agregado a VM](./media/devtest-lab-add-vm-with-artifacts/devtestlab-add-artifacts-blade-selected-artifacts.png)

1. Para especificar el orden en que se ejecutan los artefactos, arrastre y coloque los artefactos en el orden deseado. **Nota:** Si tiene problemas para arrastrar la muestra, asegúrese de que se arrastra desde el lado izquierdo de la muestra. 

1. Seleccione **Aceptar** cuando haya terminado.  

## <a name="view-or-modify-an-artifact"></a>Ver o modificar un efecto

Los pasos siguientes muestran cómo ver o modificar los parámetros de muestra:

1. En la parte superior del módulo **Agregar artefactos** , seleccione el vínculo que indica el número de artefactos que se han agregado a la máquina virtual.

    ![Número de artefactos agregado a VM](./media/devtest-lab-add-vm-with-artifacts/devtestlab-add-artifacts-blade-selected-artifacts.png)

1. En el módulo **Artefactos seleccionados** , seleccione la muestra que desea ver o editar.  

1. En el módulo de **Muestra de agregar** , realice los cambios necesarios y haga clic en **Aceptar** para cerrar el módulo de **Muestra de agregar** .

1. Seleccione **Aceptar** para cerrar el módulo **Artefactos seleccionada** .

## <a name="save-azure-resource-manager-template"></a>Guardar plantilla de administrador de recursos de Azure

Una plantilla de administrador de recursos de Azure ofrece una manera descriptiva para definir una implementación de repetición. Los siguientes pasos explican cómo guardar la plantilla de administrador de recursos de Azure para la máquina virtual se ha creado.
Una vez guardado, puede usar la plantilla de administrador de recursos de Azure para [implementar nuevas máquinas virtuales con PowerShell de Azure](../azure-resource-manager/resource-group-overview.md#template-deployment).

1. En el módulo de **Máquina Virtual** , seleccione **Plantilla de vista de ARM**.

1. En el **módulo de la plantilla de administrador de recursos de Azure de vista**, seleccione el texto de la plantilla.

1. Copiar el texto seleccionado al Portapapeles.

1. Seleccione **Aceptar** para cerrar el **módulo de la plantilla de vista de administrador de recursos de Azure**.

1. Abra un editor de texto.

1. Pegue el texto de la plantilla desde el Portapapeles.

1. Guarde el archivo para su uso posterior.

[AZURE.INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>Pasos siguientes

- Una vez que se ha creado la máquina virtual, puede conectarse a la máquina virtual seleccionando **Conectar** en el módulo de la máquina virtual.
- Obtenga información sobre cómo [crear artefactos personalizados para su máquina virtual de prácticas de DevTest](devtest-lab-artifact-author.md).
- Explorar la [Galería de plantillas de DevTest prácticas ARM tutorial](https://github.com/Azure/azure-devtestlab/tree/master/ARMTemplates)