<properties
    pageTitle="Administrar las fórmulas en Azure DevTest prácticas para crear máquinas virtuales | Microsoft Azure"
    description="Aprenda a crear, actualizar, eliminar fórmulas de Azure DevTest prácticas y usarlos para crear nuevas máquinas virtuales."
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

# <a name="manage-devtest-labs-formulas-to-create-vms"></a>Administrar las fórmulas de DevTest prácticas para crear máquinas virtuales

Una fórmula en Azure DevTest prácticas es una lista de valores de propiedad predeterminados utilizados para crear una máquina virtual (VM). Al crear una máquina virtual de una fórmula, los valores predeterminados pueden usarse como-está o modificado. Como [imágenes personalizadas](./devtest-lab-create-template.md) y [Marketplace](./devtest-lab-configure-marketplace-images.md), fórmulas proporcionan un mecanismo de aprovisionamiento de VM rápida.  

En este artículo, aprenderá a realizar las siguientes tareas:

- [Crear una fórmula](#create-a-formula)
- [Usar una fórmula para aprovisionar una máquina virtual](#use-a-formula-to-provision-a-vm)
- [Modificar una fórmula](#modify-a-formula)
- [Eliminar una fórmula](#delete-a-formula)

> [AZURE.NOTE] Fórmulas - como [imágenes personalizadas](./devtest-lab-create-template.md) - le permiten crear una imagen de la base de un archivo de disco duro virtual. La imagen de base, a continuación, puede usarse para aprovisionar una nueva máquina virtual. Para ayudarle a decidir cuál es la adecuada para su entorno particular, consulte el artículo de [imágenes personalizadas de comparación y las fórmulas en las prácticas de DevTest](./devtest-lab-comparing-vm-base-image-types.md).

## <a name="create-a-formula"></a>Crear una fórmula
Cualquier usuario con permisos de prácticas DevTest *usuarios* es posible crear máquinas virtuales con una fórmula como base. Hay dos formas de crear fórmulas: 

- Desde una base: utilice cuando desee definir todas las características de la fórmula.
- Desde un laboratorio existente VM - Utilícela crear una fórmula en función de la configuración de una máquina virtual existente.

### <a name="create-a-formula-from-a-base"></a>Crear una fórmula de una base.
Los pasos siguientes guían a través del proceso de creación de una fórmula de una imagen personalizada, imagen de Marketplace u otra fórmula.

1. Inicie sesión en el [portal de Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Seleccione **Más servicios**y, a continuación, seleccione **DevTest prácticas** de la lista.

1. En la lista de prácticas, seleccione la práctica deseada.  

1. En el módulo de la práctica, seleccione **fórmulas (reutilizables bases)**.

    ![Menú de fórmula](./media/devtest-lab-manage-formulas/lab-settings-formulas.png)

1. En el módulo de **fórmulas de la práctica** , seleccione **+ Agregar**.

    ![Agregar una fórmula](./media/devtest-lab-manage-formulas/add-formula.png)

1. En el módulo de **Elegir una base** , seleccione la base (imagen personalizada, imagen de Marketplace o fórmula) desde la que desea crear la fórmula.

    ![Lista de base](./media/devtest-lab-manage-formulas/base-list.png)

1. En el módulo de la **fórmula de crear** , especifique los valores siguientes:

    - **Nombre de la fórmula** : escriba un nombre para la fórmula. Este valor se mostrará en la lista de imágenes de base cuando se crea una máquina virtual. Valide el nombre como se escribe y, a continuación, si no es válido, un mensaje le indicará los requisitos para un nombre válido.
    - **Descripción** : escriba una descripción apropiada para la fórmula. Este valor está disponible desde el menú contextual de la fórmula cuando se crea una máquina virtual.
    - **Nombre de usuario** : escriba un nombre de usuario que se concederá privilegios de administrador.
    - **Contraseña** : escriba - o seleccione de la lista desplegable - un valor que está asociado con el secreto (contraseña) que desea usar para el usuario especificado.  
    - **Imagen** : este campo muestra el nombre de la imagen base seleccionado en el módulo anterior. 
    - **Tamaño de la máquina Virtual** , seleccione uno de los elementos predefinidos que especifique las muestras de procesador, el tamaño de RAM y el tamaño de la unidad de disco duro de la máquina virtual para crear.
    - **Red Virtual** , especifique la red virtual deseada.
    - **Subred** - especificar la subred deseada.
    - **Dirección IP pública** : si se establece la directiva de práctica para permitir las direcciones IP públicas para la subred seleccionada, especifique si desea que la dirección IP sea público, no seleccione **Sí** o ****. En caso contrario, esta opción está deshabilitada y seleccionada como **No**.
    - **Artefactos** - seleccionar y configurar los artefactos que desea agregar a la imagen base. Proteger la cadena de valores no se guardan con la fórmula. Por lo tanto, no se muestran los parámetros de muestra que son cadenas seguras. 

        ![Crear la fórmula](./media/devtest-lab-manage-formulas/create-formula.png)

1. Seleccione **crear** para crear la fórmula.

### <a name="create-a-formula-from-a-vm"></a>Crear una fórmula de una máquina virtual
Los pasos siguientes guían a través del proceso de creación de una fórmula basada en una máquina virtual existente. 

> [AZURE.NOTE] Para crear una fórmula desde una máquina virtual, la máquina virtual debe creada después de 30 de marzo de 2016. 

1. Inicie sesión en el [portal de Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Seleccione **Más servicios**y, a continuación, seleccione **DevTest prácticas** de la lista.

1. En la lista de prácticas, seleccione la práctica deseada.  

1. En el módulo de **información general** de la práctica, seleccione la máquina virtual desde la que desea crear la fórmula.

    ![Máquinas virtuales de prácticas](./media/devtest-lab-manage-formulas/my-vms.png)

1. En el módulo de la máquina virtual, seleccione **crear fórmula (base reutilizable)**.

    ![Crear la fórmula](./media/devtest-lab-manage-formulas/create-formula-menu.png)

1. En el módulo **crear fórmula** , escriba un **nombre** y una **Descripción** para la nueva fórmula.

    ![Crear fórmulas placa](./media/devtest-lab-manage-formulas/create-formula-blade.png)

1. Seleccione **Aceptar** para crear la fórmula.

## <a name="use-a-formula-to-provision-a-vm"></a>Usar una fórmula para aprovisionar una máquina virtual
Una vez que haya creado una fórmula, puede crear una máquina virtual en función de la fórmula. La sección [Agregar una máquina virtual con artefactos](devtest-lab-add-vm-with-artifacts.md#add-a-vm-with-artifacts) le guiará por el proceso.

## <a name="modify-a-formula"></a>Modificar una fórmula
Para modificar una fórmula, siga estos pasos:

1. Inicie sesión en el [portal de Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Seleccione **Más servicios**y, a continuación, seleccione **DevTest prácticas** de la lista.

1. En la lista de prácticas, seleccione la práctica deseada.  

1. En el módulo de la práctica, seleccione **fórmulas (reutilizables bases)**.

    ![Menú de fórmula](./media/devtest-lab-manage-formulas/lab-settings-formulas.png)

1. En el módulo de **fórmulas de la práctica** , seleccione la fórmula que desea modificar.

1. En el módulo **Actualizar fórmula** , realice las modificaciones que desee y seleccione **Actualizar**.

## <a name="delete-a-formula"></a>Eliminar una fórmula 
Para eliminar una fórmula, siga estos pasos:

1. Inicie sesión en el [portal de Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Seleccione **Más servicios**y, a continuación, seleccione **DevTest prácticas** de la lista.

1. En la lista de prácticas, seleccione la práctica deseada.  

1. En el módulo de **configuración** de la práctica, haga clic en **fórmulas**.

    ![Menú de fórmula](./media/devtest-lab-manage-formulas/lab-settings-formulas.png)

1. En el módulo de **fórmulas de la práctica** , seleccione los puntos suspensivos a la derecha de la fórmula que desea eliminar.

    ![Menú de fórmula](./media/devtest-lab-manage-formulas/lab-formulas-blade.png)

1. En el menú contextual de la fórmula, seleccione **Eliminar**.

    ![Menú contextual de fórmula](./media/devtest-lab-manage-formulas/formula-delete-context-menu.png)

1. Seleccione **Sí** en el cuadro de diálogo de confirmación de eliminación.

[AZURE.INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="related-blog-posts"></a>Entradas de blog relacionadas

- [¿Imágenes personalizadas o fórmulas?](https://blogs.msdn.microsoft.com/devtestlab/2016/04/06/custom-images-or-formulas/)

## <a name="next-steps"></a>Pasos siguientes
Una vez haya creado una fórmula para usar al crear una máquina virtual, el siguiente paso es [Agregar una máquina virtual para el laboratorio](./devtest-lab-add-vm-with-artifacts.md).