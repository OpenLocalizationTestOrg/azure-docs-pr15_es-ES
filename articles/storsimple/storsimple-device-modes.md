<properties 
   pageTitle="Cambiar el modo de dispositivo StorSimple | Microsoft Azure"
   description="Describe los modos de dispositivo StorSimple y se explica cómo usar Windows PowerShell para StorSimple para cambiar el modo de dispositivo."
   services="storsimple"
   documentationCenter=""
   authors="alkohli"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="06/17/2016"
   ms.author="alkohli" />

# <a name="change-the-device-mode-on-your-storsimple-device"></a>Cambiar el modo de dispositivo en el dispositivo StorSimple

En este artículo se proporciona una breve descripción de los distintos modos en los que puede trabajar con su dispositivo StorSimple. El dispositivo StorSimple puede funcionar en tres modos: normal, el mantenimiento y la recuperación. 

Después de leer este artículo, sabrá:

- ¿Qué los modos de dispositivo de StorSimple son
- Cómo determinar el modo en que el dispositivo StorSimple se encuentra en
- Cómo cambiar de normal a modo de mantenimiento y *viceversa*


Las tareas de administración anteriores sólo pueden realizarse a través de la interfaz de Windows PowerShell de su dispositivo StorSimple.

## <a name="about-storsimple-device-modes"></a>Acerca de los modos de dispositivo StorSimple

El dispositivo StorSimple puede funcionar en modo normal, el mantenimiento o la recuperación. Cada uno de estos modos se describe brevemente a continuación.

### <a name="normal-mode"></a>Modo normal

Esto se define como el modo de funcionamiento normal de un dispositivo StorSimple completamente configurado. De forma predeterminada, el dispositivo debe estar en modo normal.

### <a name="maintenance-mode"></a>Modo de mantenimiento

A veces el dispositivo StorSimple debe situarse en modo de mantenimiento. Este modo permite realizar el mantenimiento en el dispositivo e instalar actualizaciones de interrupciones, como los relacionados con el firmware del disco.

Puede poner el sistema en modo de mantenimiento sólo a través de Windows PowerShell para StorSimple. Todas las solicitudes de i/OS están en pausa en este modo. También se detienen servicios como memoria de acceso aleatorio de volátiles (NVRAM) o el servicio de agrupación. Los controladores de ambos se reinician cuando especifique o salir de este modo. Al salir del modo de mantenimiento, todos los servicios continuará y deben ser correcto. Esto puede tardar unos minutos.

>[AZURE.NOTE]Modo de mantenimiento de **sólo se admite en un dispositivo funcionando correctamente. No se admite en un dispositivo en el que uno o ambos de los controladores no funcionan.**
</br>

### <a name="recovery-mode"></a>Modo de recuperación

Modo de recuperación puede describirse como "Modo seguro de Windows con soporte técnico de red". Modo de recuperación activa el equipo de Microsoft Support y les permite realizar diagnósticos en el sistema. Es el objetivo principal del modo de recuperación recuperar los registros del sistema.

Si el sistema entra en modo de recuperación, póngase en contacto con Microsoft Support para los pasos siguientes. Para obtener más información, vaya a [Soporte técnico de Microsoft del contacto](storsimple-contact-microsoft-support.md).

>[AZURE.NOTE] **No puede colocar el dispositivo en modo de recuperación. Si el dispositivo está en mal estado, el modo de recuperación intenta obtener el dispositivo en un estado en el que el personal de Microsoft Support puede examinar.**

## <a name="determine-storsimple-device-mode"></a>Determinar el modo de dispositivo StorSimple

#### <a name="to-determine-the-current-device-mode"></a>Para determinar el modo de dispositivo actual

1. Inicie sesión en la consola de serie de dispositivo siguiendo los pasos de [PuTTY usar para conectarse a la consola de serie de dispositivo](storsimple-deployment-walkthrough.md#use-putty-to-connect-to-the-device-serial-console).

2. Busque el mensaje de pancarta en el menú de consola serie del dispositivo. Este mensaje explícitamente indica si el dispositivo está en modo de recuperación o mantenimiento. Si el mensaje no contiene información específica relacionada con el modo de sistema, el dispositivo está en modo normal.

## <a name="change-the-storsimple-device-mode"></a>Cambiar el modo de dispositivo StorSimple 

Puede colocar el dispositivo StorSimple en modo de mantenimiento (desde modo normal) para realizar el mantenimiento o instalar actualizaciones del modo de mantenimiento. Realice los siguientes procedimientos para entrar o salir del modo de mantenimiento.

> [AZURE.IMPORTANT] Antes de entrar en modo de mantenimiento, compruebe que los controladores de dispositivos son buen Estados accediendo el **Estado de Hardware** en la página de **Mantenimiento** en el portal de clásico de Azure. Si uno o ambos de los controladores no están correcto, póngase en contacto con Microsoft Support para los pasos siguientes. Para obtener más información, vaya a [Soporte técnico de Microsoft del contacto](storsimple-contact-microsoft-support.md).

#### <a name="to-enter-maintenance-mode"></a>Para especificar el modo de mantenimiento

1. Inicie sesión en la consola de serie de dispositivo siguiendo los pasos de [PuTTY usar para conectarse a la consola de serie de dispositivo](storsimple-deployment-walkthrough.md#use-putty-to-connect-to-the-device-serial-console).

2. En el menú de consola serie, seleccione la opción 1, **inicie sesión con acceso completo**. Cuando se le solicite, proporcione la **contraseña de administrador de dispositivos**. La contraseña predeterminada es: `Password1`.

3. En el símbolo del sistema, escriba 

    `Enter-HcsMaintenanceMode`

4. Verá un mensaje de advertencia que indica que el modo de mantenimiento se interrumpen todas las solicitudes de i/OS y la conexión con el portal Azure clásico de servidor y se le pedirá confirmación. Escriba **Y** para especificar el modo de mantenimiento.

5. Ambos controladores se reiniciarán. Una vez completado el reinicio, aparecerá otro mensaje que indica que el dispositivo está en modo de mantenimiento.


#### <a name="to-exit-maintenance-mode"></a>Para salir del modo de mantenimiento

1. Inicie sesión en la consola de serie de dispositivo. Compruebe desde el mensaje de titular que el dispositivo está en modo de mantenimiento.

2. En el símbolo del sistema, escriba:

    `Exit-HcsMaintenanceMode`

3. Aparecerán un mensaje de advertencia y un mensaje de confirmación. Escriba **Y** para salir del modo de mantenimiento.

4. Ambos controladores se reiniciarán. Una vez completado el reinicio, aparecerá otro mensaje que indica que el dispositivo está en modo normal.


## <a name="next-steps"></a>Pasos siguientes

Obtenga información sobre cómo [aplicar actualizaciones de modo normal y mantenimiento](storsimple-update-device.md) en el dispositivo StorSimple.

