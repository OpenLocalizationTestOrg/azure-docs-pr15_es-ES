<properties
    pageTitle="Inicie sesión en una máquina virtual de Azure clásico | Microsoft Azure"
    description="Utilice el portal Azure clásico para iniciar sesión en una máquina virtual de Windows creada con el modelo clásico de implementación."
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
    ms.date="07/28/2016"
    ms.author="cynthn"/>


# <a name="log-on-to-a-windows-virtual-machine-using-the-azure-classic-portal"></a>Iniciar sesión en una máquina virtual de Windows mediante el portal clásico de Azure

En el portal de Azure clásico, se utiliza el botón **Conectar** para iniciar una sesión de escritorio remoto e iniciar sesión en una máquina virtual de Windows.

¿Desea conectarse a una VM Linux? Consulte [cómo iniciar sesión en una máquina virtual con Linux](virtual-machines-linux-mac-create-ssh-keys.md).

Aprenda cómo [realizar estos pasos con el nuevo portal de Azure](virtual-machines-windows-connect-logon.md).

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)] 

## <a name="video-walkthrough"></a>Tutorial de vídeo

Aquí es un tutorial de vídeo sobre los pasos de este tutorial. También cubre los extremos y los puertos públicos y privados utilizados para conectarse a una máquina virtual de Windows en Azure.

[AZURE.VIDEO logging-on-to-vm-running-windows-server-on-azure]


## <a name="connect-to-the-virtual-machine"></a>Conéctese a la máquina virtual

1. Iniciar sesión en el portal Azure clásico.

2. Haga clic en **máquinas virtuales**y, a continuación, seleccione la máquina virtual.

3. En la barra de comandos en la parte inferior de la página, haga clic en **Conectar**.

    ![Inicie sesión en la máquina virtual](./media/virtual-machines-windows-classic-connect-logon/connectwindows.png)
    
> [AZURE.TIP] Si el botón **Conectar** no está disponible, consulte las sugerencias al final de este artículo.

## <a name="log-on-to-the-virtual-machine"></a>Inicie sesión en la máquina virtual

[AZURE.INCLUDE [virtual-machines-log-on-win-server](../../includes/virtual-machines-log-on-win-server.md)]

## <a name="next-steps"></a>Próximos pasos

-   Si el botón **Conectar** está inactivo o tiene otros problemas con la conexión a Escritorio remoto, pruebe a restablecer la configuración. En el panel de la máquina virtual, en **Resumen rápido**, haga clic en **Restablecer configuración remota**.
-   En problemas con su contraseña, intente restablecerlo. En el panel de la máquina virtual, en **Resumen rápido**, haga clic en **Restablecer contraseña**.

Si estos consejos no funcionan o no son lo que necesita, consulte [conexiones de solucionar problemas de escritorio remoto a un equipo de Virtual de basado en Windows Azure](virtual-machines-windows-troubleshoot-rdp-connection.md). Este artículo le guiará a través de diagnosticar y resolver problemas comunes.


