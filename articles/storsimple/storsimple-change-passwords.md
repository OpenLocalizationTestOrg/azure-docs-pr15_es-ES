<properties 
   pageTitle="Cambiar las contraseñas de StorSimple | Microsoft Azure" 
   description="Describe cómo usar el servicio Administrador de StorSimple para cambiar la contraseña de administrador StorSimple instantánea administrador y dispositivo." 
   services="storsimple" 
   documentationCenter="NA" 
   authors="alkohli" 
   manager="carmonm" 
   editor=""/>

<tags
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD" 
   ms.date="08/17/2016"
   ms.author="alkohli"/>

# <a name="use-the-storsimple-manager-service-to-change-your-storsimple-passwords"></a>Usar el servicio Administrador de StorSimple para cambiar las contraseñas de StorSimple

## <a name="overview"></a>Información general 

El portal clásico Azure **Configurar** página contiene todos los parámetros de dispositivo que puede cambiar la configuración en un dispositivo StorSimple administrado por un servicio de administrador de StorSimple. En este tutorial se explica cómo puede usar la página de **configuración** para cambiar el administrador del dispositivo o la contraseña de administrador de instantáneas StorSimple.

## <a name="change-the-device-administrator-password"></a>Cambiar la contraseña de administrador de dispositivos

Cuando se utiliza la interfaz de Windows PowerShell para tener acceso al dispositivo StorSimple, debe escribir una contraseña de administrador de dispositivos. Cuando el primer dispositivo StorSimple está registrado con un servicio, la contraseña predeterminada para esta interfaz es *Contraseña1*. Para la seguridad de los datos, debe cambiar esta contraseña al final del proceso de registro. No se puede salir del proceso de registro sin cambiar la contraseña. Para obtener más información, vea [paso 3: configurar y registrar el dispositivo a través de Windows PowerShell para StorSimple](storsimple-deployment-walkthrough-u2.md#step-3-configure-and-register-the-device-through-windows-powershell-for-storsimple).

A continuación, puede cambiarse la contraseña que se estableció en primer lugar mediante la interfaz de Windows PowerShell durante el registro a través del portal clásico Azure. Realice los pasos siguientes para cambiar la contraseña de administrador de dispositivos.

#### <a name="to-change-the-device-administrator-password"></a>Para cambiar la contraseña de administrador de dispositivos

1. En el portal clásico, haga clic en **dispositivos** > **Configurar** para su dispositivo.

2. Desplácese hacia abajo hasta la sección de la **Contraseña de administrador del dispositivo** . Proporcione una contraseña de administrador que contiene de 8 a 15 caracteres. La contraseña debe ser una combinación de 3 o más de los caracteres en mayúsculas, minúsculas, numéricos y especiales.

3. Confirme la contraseña.

4. Haga clic en **Guardar** en la parte inferior de la página.

Ahora debe actualizarse la contraseña de administrador de dispositivos. Puede usar esta contraseña modificada para acceder a la interfaz de Windows PowerShell.

## <a name="change-the-storsimple-snapshot-manager-password"></a>Cambiar la contraseña de administrador de instantáneas StorSimple

Software de administrador de instantáneas StorSimple reside en el host de Windows y permite a los administradores administrar las copias de seguridad de su dispositivo StorSimple en forma de local y la nube instantáneas.

Al configurar un dispositivo en el Administrador de instantáneas de StorSimple, le pedirá que proporcione la dirección IP del dispositivo y la contraseña para autenticar el dispositivo de almacenamiento. Esta contraseña primero se configura mediante la interfaz de Windows PowerShell. Para obtener más información, vea [paso 3: configurar y registrar el dispositivo a través de Windows PowerShell para StorSimple](storsimple-deployment-walkthrough-u2.md#step-3-configure-and-register-the-device-through-windows-powershell-for-storsimple).

A continuación, puede cambiarse la contraseña que se estableció en primer lugar mediante la interfaz de Windows PowerShell durante el registro a través del portal clásico. Realice los pasos siguientes para cambiar la contraseña de administrador de instantáneas StorSimple.

#### <a name="to-change-the-storsimple-snapshot-manager-password"></a>Para cambiar la contraseña de administrador de instantáneas StorSimple

1. En el portal clásico, haga clic en **dispositivos** > **Configurar** para su dispositivo.

2. Desplácese hacia abajo hasta la sección **Administrador de instantáneas StorSimple** . Escriba una contraseña de 14 o 15 caracteres. Asegúrese de que la contraseña contiene una combinación de 3 o más de los caracteres en mayúsculas, minúsculas, numéricos y especiales.

3. Confirme la contraseña.

4. Haga clic en **Guardar** en la parte inferior de la página.

Ahora debe actualizarse la contraseña de administrador de instantáneas StorSimple.
 

## <a name="next-steps"></a>Pasos siguientes

- Más información sobre la [seguridad de StorSimple](storsimple-security.md).

- Más información acerca de cómo [modificar la configuración del dispositivo](storsimple-modify-device-config.md).

- Más información sobre cómo [usar el servicio de administrador de StorSimple para administrar el dispositivo de StorSimple](storsimple-manager-service-administration.md).
