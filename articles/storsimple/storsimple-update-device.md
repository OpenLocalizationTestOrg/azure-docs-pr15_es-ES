<properties
   pageTitle="Actualizar el dispositivo StorSimple | Microsoft Azure"
   description="Se explica cómo usar la característica de actualización de StorSimple instalar revisiones y actualizaciones de modo normal y mantenimiento."
   services="storsimple"
   documentationCenter="NA"
   authors="SharS"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="06/28/2016"
   ms.author="v-sharos" />

# <a name="update-your-storsimple-8000-series-device"></a>Actualizar el dispositivo de la serie 8000 StorSimple

## <a name="overview"></a>Información general

Las características de actualizaciones StorSimple permiten realizar fácilmente su dispositivo StorSimple actualizada. Según el tipo de actualización, puede aplicar actualizaciones al dispositivo a través del portal clásico Azure o a través de la interfaz de Windows PowerShell. Este tutorial describe los tipos de actualización y cómo instalar cada uno de ellos.

Puede aplicar dos tipos de actualizaciones de dispositivos: 

- Normal (o en modo Normal) actualizaciones
- Actualizaciones de modo de mantenimiento

Puede instalar actualizaciones regulares a través del portal clásica Azure o Windows PowerShell; Sin embargo, se debe usar Windows PowerShell para instalar las actualizaciones de modo de mantenimiento. 

Cada tipo de actualización se describe a continuación.

### <a name="regular-updates"></a>Actualizaciones de forma regular

Las actualizaciones son actualizaciones sin interrupciones que se pueden instalar cuando el dispositivo está en modo Normal. Estas actualizaciones se aplican a través del sitio Web de Microsoft Update a cada controlador de dispositivo. 

> [AZURE.IMPORTANT] Puede producirse un error de controlador durante el proceso de actualización. Sin embargo, esto no afectará a la disponibilidad del sistema o una operación.

- Para obtener más información sobre cómo instalar actualizaciones regulares a través del portal clásica Azure, consulte [instalar actualizaciones normales a través de la portal(#install-regular-updates-via-the-azure-classic-portal) clásica Azure.

- También puede instalar actualizaciones periódicas a través de Windows PowerShell para StorSimple. Para obtener más información, consulte [instalar actualizaciones periódicas a través de Windows PowerShell para StorSimple](#install-regular-updates-via-windows-powershell-for-storsimple).

### <a name="maintenance-mode-updates"></a>Actualizaciones de modo de mantenimiento

Actualizaciones de modo de mantenimiento son interrupciones como las actualizaciones de firmware de disco. Estas actualizaciones requieren el dispositivo que se pondrán en modo de mantenimiento. Para obtener información detallada, consulte [paso 2: modo de mantenimiento escriba](#step2). No puede usar el portal de clásico Azure para instalar las actualizaciones de modo de mantenimiento. En su lugar, debe usar Windows PowerShell para StorSimple. 

Para obtener más información sobre cómo instalar las actualizaciones de modo de mantenimiento, consulte [las actualizaciones de modo de mantenimiento de instalar a través de Windows PowerShell para StorSimple](#install-maintenance-mode-updates-via-windows-powershell-for-storsimple).

> [AZURE.IMPORTANT] Actualizaciones de modo de mantenimiento deben aplicarse por separado para cada controlador. 

## <a name="install-regular-updates-via-the-azure-classic-portal"></a>Instalar actualizaciones regulares a través del portal de clásico de Azure

Puede usar el portal de clásico Azure para aplicar actualizaciones a su dispositivo StorSimple.

[AZURE.INCLUDE [storsimple-install-updates-manually](../../includes/storsimple-install-updates-manually.md)]

## <a name="install-regular-updates-via-windows-powershell-for-storsimple"></a>Instalar actualizaciones de normales a través de Windows PowerShell para StorSimple

Como alternativa, puede usar Windows PowerShell para StorSimple para aplicar actualizaciones de normal (modo Normal).

> [AZURE.IMPORTANT] Aunque puede instalar actualizaciones regulares con Windows PowerShell para StorSimple, le recomendamos que instale actualizaciones periódicamente a través del portal clásica Azure. A partir de 1 de actualización, se realizará comprobaciones previas antes de instalar actualizaciones desde el portal. Estas comprobaciones previas se preferencia sobre errores y asegúrese de fluida. 

[AZURE.INCLUDE [storsimple-install-regular-updates-powershell](../../includes/storsimple-install-regular-updates-powershell.md)]

## <a name="install-maintenance-mode-updates-via-windows-powershell-for-storsimple"></a>Instalar las actualizaciones de modo de mantenimiento a través de Windows PowerShell para StorSimple

Usar Windows PowerShell para StorSimple para aplicar actualizaciones de modo de mantenimiento en el dispositivo StorSimple. Todas las solicitudes de i/OS están en pausa en este modo. También se detienen servicios como memoria de acceso aleatorio de volátiles (NVRAM) o el servicio de agrupación. Ambos controladores se reinician al introducir o salir de este modo. Al salir de este modo, todos los servicios continuará y deben ser correcto. (Puede tardar unos minutos).

Si necesita aplicar actualizaciones de modo de mantenimiento, recibirá una alerta a través del portal clásica Azure que tiene actualizaciones que deben instalarse. Esta alerta incluye instrucciones para usar Windows PowerShell para StorSimple para instalar las actualizaciones. Después de actualizar el dispositivo, utilice el mismo procedimiento para cambiar el dispositivo al modo normal. Para obtener instrucciones detalladas, consulte [paso 4: modo de mantenimiento de salida](#step4).

> [AZURE.IMPORTANT] 
> 
> - Antes de entrar en modo de mantenimiento, compruebe que los controladores de dispositivos son correcto comprobando el **Estado de Hardware** en la página de **Mantenimiento** en el portal de clásico de Azure. Si el controlador no es correcto, póngase en contacto con Microsoft Support para los pasos siguientes. Para obtener más información, vaya a soporte técnico de Microsoft del contacto. 
> - Cuando esté en modo de mantenimiento, debe aplicar primero la actualización en un controlador y, a continuación, en el otro controlador.

### <a name="step-1-connect-to-the-serial-console-a-namestep1"></a>Paso 1: Conectar con la consola de serie<a name="step1">

En primer lugar, use una aplicación como PuTTY para tener acceso a la consola de serie. El siguiente procedimiento explica cómo usar PuTTY para conectarse a la consola de serie.

[AZURE.INCLUDE [storsimple-use-putty](../../includes/storsimple-use-putty.md)]

### <a name="step-2-enter-maintenance-mode-a-namestep2"></a>Paso 2: Especificar el modo de mantenimiento<a name="step2">

Después de conectar con la consola, determine si hay actualizaciones para instalar y especificar el modo de mantenimiento para instalarlas.

[AZURE.INCLUDE [storsimple-enter-maintenance-mode](../../includes/storsimple-enter-maintenance-mode.md)]

### <a name="step-3-install-your-updates-a-namestep3"></a>Paso 3: Instalar las actualizaciones<a name="step3">

A continuación, instale las actualizaciones.

[AZURE.INCLUDE [storsimple-install-maintenance-mode-updates](../../includes/storsimple-install-maintenance-mode-updates.md)]
 
### <a name="step-4-exit-maintenance-mode-a-namestep4"></a>Paso 4: Modo de mantenimiento de salida<a name="step4">

Por último, salir del modo de mantenimiento.

[AZURE.INCLUDE [storsimple-exit-maintenance-mode](../../includes/storsimple-exit-maintenance-mode.md)]

## <a name="install-hotfixes-via-windows-powershell-for-storsimple"></a>Instalar las revisiones a través de Windows PowerShell para StorSimple

A diferencia de actualizaciones de Microsoft Azure StorSimple, se instalan las revisiones de una carpeta compartida. Al igual que con las actualizaciones, hay dos tipos de revisiones: 

- Revisiones regulares 
- Revisiones de modo de mantenimiento  

Los siguientes procedimientos explican cómo usar Windows PowerShell para StorSimple instalar revisiones de modo de mantenimiento y normal.

[AZURE.INCLUDE [storsimple-install-regular-hotfixes](../../includes/storsimple-install-regular-hotfixes.md)]

[AZURE.INCLUDE [storsimple-install-maintenance-mode-hotfixes](../../includes/storsimple-install-maintenance-mode-hotfixes.md)]

## <a name="what-happens-to-updates-if-you-perform-a-factory-reset-of-the-device"></a>¿Qué ocurre actualizaciones si realiza un restablecimiento de fábrica del dispositivo?

Si se restablece un dispositivo a la configuración de fábrica, todas las actualizaciones se pierden. Después de que el dispositivo de restablecimiento de fábrica está registrado y configurado, debe instalar manualmente actualizaciones a través del portal clásica Azure o Windows PowerShell para StorSimple. Para obtener más información acerca de la fábrica restablecer, consulte [Restablecer el dispositivo a la configuración predeterminada de fábrica](storsimple-manage-device-controller.md#reset-the-device-to-factory-default-settings).

## <a name="next-steps"></a>Pasos siguientes

- Más información sobre cómo [usar Windows PowerShell para StorSimple administrar el dispositivo de StorSimple](storsimple-windows-powershell-administration.md).
- Más información sobre cómo [usar el servicio de administrador de StorSimple para administrar el dispositivo de StorSimple](storsimple-manager-service-administration.md).
