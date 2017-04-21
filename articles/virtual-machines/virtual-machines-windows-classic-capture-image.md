<properties
    pageTitle="Capturar una imagen de una VM de Windows Azure | Microsoft Azure"
    description="Capturar una imagen de una máquina virtual de Windows Azure creada con el modelo de implementación clásico."
    services="virtual-machines-windows"
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
    ms.date="09/27/2016"
    ms.author="cynthn"/>

#<a name="capture-an-image-of-an-azure-windows-virtual-machine-created-with-the-classic-deployment-model"></a>Capturar una imagen de una máquina virtual de Windows Azure creada con el modelo de implementación clásico.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Para obtener información de modelo de administrador de recursos, vea [crear una copia de máquina virtual de Windows se ejecuta en Azure](virtual-machines-windows-vhd-copy.md).


En este artículo se muestra cómo capturar una máquina virtual Azure con Windows para usarla como una imagen para crear otros equipos virtuales. Esta imagen incluye el disco de sistema operativo y los discos de datos que se han adjuntado a la máquina virtual. No incluye las configuraciones de red, así que deberá configurarlos al crear los otros equipos virtuales que usan la imagen.

Azure almacena la imagen en **Mis imágenes**. Este es el mismo lugar donde se almacenan las imágenes que haya cargado. Para obtener más información acerca de las imágenes, consulte [acerca de las imágenes para máquinas virtuales](virtual-machines-linux-classic-about-images.md).

##<a name="before-you-begin"></a>Antes de empezar##

El procedimiento siguiente asume que ya ha creado una máquina virtual Azure y ha configurado el sistema operativo, incluidos asociar los discos de datos. Si todavía no lo ha hecho todavía, siga estas instrucciones:

- [Crear una máquina virtual de una imagen](virtual-machines-windows-classic-createportal.md)
- [Cómo adjuntar un disco de datos a una máquina virtual](virtual-machines-windows-classic-attach-disk.md)
- Asegúrese de que las funciones de servidor son compatibles con Sysprep. Para obtener más información, vea [Compatibilidad con Sysprep Roles del servidor](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles).

> [AZURE.WARNING] Este proceso elimina la máquina virtual original una vez que se genera. 

Antes de caputuring una imagen de una máquina virtual Azure, se recomienda que se copia de la máquina virtual de destino. Máquinas virtuales de Windows Azure puede ser una copia mediante copia de seguridad de Azure. Para obtener más información, vea [realizar copias de seguridad máquinas virtuales de Windows Azure](../backup/backup-azure-vms.md). Otras soluciones están disponibles desde socios certificados. Para averiguar qué está disponible actualmente, buscar Azure Marketplace.


##<a name="capture-the-virtual-machine"></a>Capturar la máquina virtual

1. En el [portal de clásica Azure](http://manage.windowsazure.com), **conectarse** a la máquina virtual. Para obtener instrucciones, vea [cómo iniciar sesión en una máquina virtual ejecuta Windows Server] [].

2.  Abra una ventana de símbolo del sistema como administrador.

3.  Cambiar el directorio a `%windir%\system32\sysprep`, y, a continuación, ejecute sysprep.exe.

4.  Aparece el cuadro de diálogo de la **Herramienta de preparación del sistema** . Haga lo siguiente:

    - En **Acción de limpieza del sistema**, seleccione **sistema escriba predefinidas de configuración rápida** y asegúrese de que está activada **Generalize** . Para obtener más información sobre el uso de Sysprep, vea [cómo usar Sysprep: Introducción][].

    - En **Opciones de cierre**, seleccione **Cerrar**.

    - Haga clic en **Aceptar**.

    ![Ejecute Sysprep](./media/virtual-machines-windows-classic-capture-image/SysprepGeneral.png)

7.  Sysprep se cierra la máquina virtual, que cambia el estado de la máquina virtual en el portal de clásica Azure a **detenido**.

8.  En el portal de Azure clásico, haga clic en **máquinas virtuales de Windows** y seleccione la máquina virtual que desea capturar.

9.  En la barra de comandos, haga clic en **capturar**.

    ![Capturar máquina virtual](./media/virtual-machines-windows-classic-capture-image/CaptureVM.png)

    Aparece el cuadro de diálogo **capturar la máquina Virtual** .

10. En **Nombre de la imagen**, escriba un nombre para la nueva imagen.

11. Antes de agregar una imagen de Windows Server con el conjunto de imágenes personalizadas, debe ser generalizado, ejecute Sysprep tal como se indica en los pasos anteriores. Haga clic en **he ejecutado Sysprep en la máquina virtual** para indicar que hizo.

12. Haga clic en la marca de verificación para capturar la imagen. La nueva imagen está ahora disponible en **imágenes**.

    ![Captura de la imagen correcta](./media/virtual-machines-windows-classic-capture-image/VMCapturedImageAvailable.png)

##<a name="next-steps"></a>Pasos siguientes

La imagen está lista para usarse para crear máquinas virtuales. Para ello, debe crear una máquina virtual con el elemento de menú **De la Galería** y seleccionar la imagen que acaba de crear. Para obtener instrucciones, vea [crear una máquina virtual de una imagen](virtual-machines-windows-classic-createportal.md).



[Cómo iniciar sesión una máquina virtual ejecuta Windows Server]: virtual-machines-windows-classic-connect-logon.md
[Cómo utilizar Sysprep: Introducción]: http://technet.microsoft.com/library/bb457073.aspx
[Run Sysprep.exe]: ./media/virtual-machines-capture-image-windows-server/SysprepCommand.png
[Enter Sysprep.exe options]: ./media/virtual-machines-windows-classic-capture-image/SysprepGeneral.png
[The virtual machine is stopped]: ./media/virtual-machines-capture-image-windows-server/SysprepStopped.png
[Capture an image of the virtual machine]: ./media/virtual-machines-windows-classic-capture-image/CaptureVM.png
[Enter the image name]: ./media/virtual-machines-capture-image-windows-server/Capture.png
[Image capture successful]: ./media/virtual-machines-capture-image-windows-server/CaptureSuccess.png
[Use the captured image]: ./media/virtual-machines-capture-image-windows-server/MyImagesWindows.png
