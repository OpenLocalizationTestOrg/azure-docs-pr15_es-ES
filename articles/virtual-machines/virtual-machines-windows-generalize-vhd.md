<properties
    pageTitle="Generalizar un disco duro virtual de Windows | Microsoft Azure"
    description="Aprenda a usar Sysprep generalizar una máquina virtual de Windows para usar con el modelo de implementación de administrador de recursos."
    services="virtual-machines-windows"
    documentationCenter=""
    authors="cynthn"
    manager="timlt"
    editor="tysonn"
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/20/2016"
    ms.author="cynthn"/>
    
    
    
    
# <a name="generalize-a-windows-virtual-machine-using-sysprep"></a>Generalizar una máquina virtual de Windows con Sysprep

Esta sección muestra cómo generalizar la máquina virtual de Windows para usar como una imagen. Sysprep quita toda la información de cuenta personal, entre otras cosas y prepara el equipo que se usará como una imagen. Para obtener más información acerca de Sysprep, vea [cómo usar Sysprep: Introducción](http://technet.microsoft.com/library/bb457073.aspx).

Asegúrese de que las funciones de servidor que se ejecuta en el equipo son compatibles con Sysprep. Para obtener más información, vea [Compatibilidad con Sysprep Roles del servidor](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles)

>[AZURE.IMPORTANT] Si se están ejecutando Sysprep antes de cargar su disco duro virtual en Azure por primera vez, asegúrese de que tiene [preparado su máquina virtual](virtual-machines-windows-prepare-for-upload-vhd-image.md) antes de ejecutar Sysprep. 

1. Inicie sesión en la máquina virtual de Windows.

2. Abra la ventana de símbolo del sistema como administrador. Cambie el directorio a **%windir%\system32\sysprep**y luego ejecute `sysprep.exe`.

3. En el cuadro de diálogo **Herramienta de preparación del sistema** , seleccione **sistema escriba predefinidas de configuración rápida**y asegúrese de que está seleccionada la casilla de verificación **Generalize** .

4. En **Opciones de cierre**, seleccione **Cerrar**.

5. Haga clic en **Aceptar**.

    ![Iniciar Sysprep](./media/virtual-machines-windows-upload-image/sysprepgeneral.png)

6. Cuando finalice Sysprep, apaga el equipo virtual. 

## <a name="next-steps"></a>Pasos siguientes

- Si la máquina virtual es local, puede [cargar el disco duro virtual en Azure](virtual-machines-windows-upload-image.md).
- Si la máquina virtual ya está en Azure, puede [crear una imagen de la máquina virtual generalizada](virtual-machines-windows-capture-image.md).