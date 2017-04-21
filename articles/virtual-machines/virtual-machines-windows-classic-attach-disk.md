<properties
    pageTitle="Adjuntar un disco a una máquina virtual | Microsoft Azure"
    description="Adjuntar un disco de datos a una máquina virtual de Windows creada con el modelo de implementación clásica e inicialización."
    services="virtual-machines-windows, storage"
    documentationCenter=""
    authors="cynthn"
    manager="timlt"
    editor="tysonn"
    tags="azure-service-management"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="06/27/2016"
    ms.author="cynthn"/>

# <a name="attach-a-data-disk-to-a-windows-virtual-machine-created-with-the-classic-deployment-model"></a>Adjuntar un disco de datos a una máquina virtual de Windows creada con el modelo de implementación clásica

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Si desea usar el nuevo portal, vea [cómo adjuntar un disco de datos a una máquina virtual de Windows en el portal de Azure](virtual-machines-windows-attach-disk-portal.md).

Si necesita un disco de datos adicionales, puede adjuntar un disco vacío o un disco existente con los datos a una máquina virtual. En ambos casos, los discos son archivos .vhd que residen en una cuenta de almacenamiento de Azure. En el caso de un disco nuevo, después de conectar el disco, también deberá iniciar, para que esté listo para su uso por una máquina virtual de Windows.

Para obtener más detalles acerca de los discos, consulte [acerca de los discos y VHD para máquinas virtuales](virtual-machines-windows-about-disks-vhds.md).


[AZURE.INCLUDE [howto-attach-disk-windows-linux](../../includes/howto-attach-disk-windows-linux.md)]

## <a name="initialize-the-disk"></a>Inicializar el disco

1. Conectarse a la máquina virtual. Para obtener instrucciones, vea [cómo iniciar sesión en una máquina virtual ejecuta Windows Server][logon].

2. Después de iniciar sesión en la máquina virtual, abra el **Administrador del servidor**. En el panel izquierdo, seleccione el **archivo y servicios de almacenamiento**.

    ![Abrir el Administrador de servidor](./media/virtual-machines-windows-classic-attach-disk/fileandstorageservices.png)

3. Expandir el menú y seleccione **discos**.

4. La sección de **discos** muestra los discos. En la mayoría de los casos, tendrá disco 0, disco 1 y disco 2. Disco 0 es el sistema operativo, disco 1 es el temporal y disco 2 es el disco de datos que acaba de conectar a la máquina virtual. El disco de datos de nuevo la lista de la partición como **desconocido**. Haga clic en el disco y seleccione **inicializar**.

5.  Se le notificará que se eliminará todos los datos cuando el disco está inicializado. Haga clic en **Sí** para confirmar la advertencia e inicializar el disco. Cuando haya finalizado, se mostrará la partición como **GPT**. Haga clic en el disco de nuevo y seleccione **Nuevo volumen**.

6.  Complete el Asistente con los valores predeterminados. Cuando el asistente haya finalizado, la sección **volúmenes** muestra el volumen nuevo. El disco está ahora en línea y preparada almacenar los datos.

    ![Volumen inicializado correctamente](./media/virtual-machines-windows-classic-attach-disk/newvolumecreated.png)

> [AZURE.NOTE] El tamaño de la máquina virtual determina cuántos discos puede adjuntar a él. Para obtener más información, vea [tamaños para máquinas virtuales](virtual-machines-linux-sizes.md).

## <a name="additional-resources"></a>Recursos adicionales

[Cómo desvincular un disco de una máquina virtual de Windows](virtual-machines-windows-classic-detach-disk.md)

[Acerca de discos y VHD para máquinas virtuales](virtual-machines-linux-about-disks-vhds.md)

[logon]: virtual-machines-windows-classic-connect-logon.md
