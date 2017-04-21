<properties 
   pageTitle="Activar o desactivar el dispositivo StorSimple | Microsoft Azure"
   description="Se explica cómo activar un nuevo dispositivo StorSimple, activar un dispositivo que se ha apagado o perdido power y desactivar un dispositivo de ejecución."
   services="storsimple"
   documentationCenter=""
   authors="alkohli"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="08/23/2016"
   ms.author="alkohli" />

# <a name="turn-your-storsimple-device-on-or-off"></a>Activar o desactivar el dispositivo StorSimple 

## <a name="overview"></a>Información general

Cerrar un dispositivo de Microsoft Azure StorSimple no es necesario como parte del funcionamiento normal del sistema. Sin embargo, tendrá que activar un nuevo dispositivo o un dispositivo que tuvieron apagar. En general, es necesario un apagado en casos en los que debe reemplazar hardware con errores, física mover una unidad o un dispositivo fuera de servicio. Este tutorial describe el procedimiento necesario para activar y cerrar el dispositivo StorSimple en escenarios diferentes.

La siguiente tabla enumera varios escenarios para encender y apagar el dispositivo StorSimple y proporciona vínculos a los procedimientos adecuados.

|Escenario|Temas de referencia|
|:-------|:---------------|
|Activar un nuevo dispositivo|[Activar un nuevo dispositivo](#turn-on-a-new-device)<ul><li>[Nuevo dispositivo con caracteres enmarcados principal solo](#new-device-with-primary-enclosure-only)</li><li>[Nuevo dispositivo con caracteres enmarcados EBOD](#new-device-with-ebod-enclosure)</li></ul>|
|Activar un dispositivo después de cierre|[Activar un dispositivo después de cierre](#turn-on-a-device-after-shutdown)<ul><li>[Dispositivo con caracteres enmarcados principal solo](#device-with-primary-enclosure-only)</li><li>[Dispositivo con caracteres enmarcados EBOD](#device-with-ebod-enclosure)</li></ul>|
|Activar un dispositivo después de pérdida de energía|[Activar un dispositivo después de pérdida de energía](#turn-on-a-device-after-a-power-loss)<ul><li>[Dispositivo con caracteres enmarcados principal solo](#8100)</li><li>[Dispositivo con caracteres enmarcados EBOD](#8600)</li></ul>|
|Activar un dispositivo después de los caracteres enmarcados principal y se pierde la conexión de EBOD|[Activar un dispositivo después de principal y se pierde la conexión de caracteres enmarcados EBOD](#turn-on-a-device-after-the-primary-and-ebod-enclosure-connection-is-lost)|
|Cerrar un dispositivo de ejecución|[Desactivar un dispositivo de ejecución](#turn-off-a-running-device)<ul><li>[Dispositivo con caracteres enmarcados principal solo](#8100a)</li><li>[Dispositivo con caracteres enmarcados EBOD](#8600a)</li></ul>|

## <a name="turn-on-a-new-device"></a>Activar un nuevo dispositivo

Los pasos para activar un dispositivo StorSimple por primera vez difieren dependiendo de si el dispositivo es un 8100 o un modelo de 8600. 8100 tiene un único alojamiento principal, mientras que la 8600 es un dispositivo de alojamiento dual con un alojamiento principal y enmarcado EBOD. En las secciones siguientes se tratan los pasos detallados para ambos modelos.

- [Nuevo dispositivo con caracteres enmarcados principal solo](#new-device-with-primary-enclosure-only)

- [Nuevo dispositivo con caracteres enmarcados EBOD](#new-device-with-ebod-enclosure)

### <a name="new-device-with-primary-enclosure-only"></a>Nuevo dispositivo con caracteres enmarcados principal solo

El modelo de StorSimple 8100 es un dispositivo único alojamiento. El dispositivo incluye redundantes Power y módulos de refrigeración (PCMs). Ambos PCMs deben instalarse y conectados a diferentes fuentes de alimentación para garantizar alta disponibilidad.

Realice los pasos siguientes para cable de su dispositivo para power.

[AZURE.INCLUDE [storsimple-cable-8100-for-power](../../includes/storsimple-cable-8100-for-power.md)]

>[AZURE.NOTE]Configuración de dispositivo completo y cableado instrucciones, vaya a [instalar el dispositivo 8100 StorSimple](storsimple-8100-hardware-installation.md). Asegúrese de seguir las instrucciones exactamente.

### <a name="new-device-with-ebod-enclosure"></a>Nuevo dispositivo con caracteres enmarcados EBOD

El modelo de StorSimple 8600 tiene un alojamiento principal y enmarcado EBOD. Para ello, las unidades de conectar para power y conectividad SCSI conectado en serie (SA).

Al configurar este dispositivo por primera vez, realice los pasos para SA cableado en primer lugar y, a continuación, complete los pasos de power cableado.

[AZURE.INCLUDE [storsimple-sas-cable-8600](../../includes/storsimple-sas-cable-8600.md)]

[AZURE.INCLUDE [storsimple-cable-8600-for-power](../../includes/storsimple-cable-8600-for-power.md)]

>[AZURE.NOTE]Configuración de dispositivo completo y cableado instrucciones, vaya a [instalar el dispositivo 8600 StorSimple](storsimple-8600-hardware-installation.md). Asegúrese de seguir las instrucciones exactamente.

## <a name="turn-on-a-device-after-shutdown"></a>Activar un dispositivo después de cierre

Los pasos para activar en un dispositivo StorSimple después de que se ha cerrado son diferentes dependiendo de si el dispositivo es un 8100 o un modelo de 8600. 8100 tiene un único alojamiento principal, mientras que la 8600 es un dispositivo de alojamiento dual con un alojamiento principal y enmarcado EBOD.

- [Dispositivo con caracteres enmarcados principal solo](#device-with-primary-enclosure-only)

- [Dispositivo con caracteres enmarcados EBOD](#device-with-ebod-enclosure)

### <a name="device-with-primary-enclosure-only"></a>Dispositivo con caracteres enmarcados principal solo

Después de un cierre, use el procedimiento siguiente para activar un dispositivo StorSimple con un alojamiento principal y sin alojamiento EBOD.

#### <a name="to-turn-on-a-device-with-a-primary-enclosure-only"></a>Para activar un dispositivo con solo un alojamiento principal

1. Asegúrese de que la potencia cambia en ambos Power y módulos de refrigeración (PCMs) están en la posición desactivar. Si los modificadores no están en la posición desactivado, Voltear a la posición OFF y espere a que la luces para desactivarse.

2. Voltear los interruptores en ambos PCMs a la posición activado para activar el dispositivo. Debe activar el dispositivo.

3. Compruebe lo siguiente para comprobar que el dispositivo está totalmente en:

    1. Los LED Aceptar en ambos módulos PCM son verdes.

    2. Los LED de estado en ambos controladores son verde sólido.

    3. Parpadea el LED azul en uno de los controladores, lo que indica que el controlador está activo.

    Si no se cumplen todas estas condiciones, el dispositivo no es correcto. Ponte [en contacto con soporte técnico de Microsoft](storsimple-contact-microsoft-support.md).

### <a name="device-with-ebod-enclosure"></a>Dispositivo con caracteres enmarcados EBOD

Después de un cierre, use el siguiente procedimiento para activar un dispositivo StorSimple con un alojamiento principal y enmarcado EBOD. Realice cada paso en secuencia tal y como se describe. Si no es así podría provocar pérdidas de datos.

#### <a name="to-turn-on-a-device-with-a-primary-and-an-ebod-enclosure"></a>Para activar un dispositivo con principal y enmarcado EBOD

1. Asegúrese de que los caracteres enmarcados EBOD está conectado al entorno principal. Para obtener más información, consulte [instalar el dispositivo 8600 StorSimple](storsimple-8600-hardware-installation.md).

2. Asegúrese de que la potencia y refrigeración módulos (PCMs) en los documentos adjuntos principales y EBOD están en la posición desactivar. Si los modificadores no están en la posición desactivado, Voltear a la posición OFF y espere a que la luces para desactivarse.

3. Activar los caracteres enmarcados EBOD primera Voltear los interruptores en ambos PCMs a la posición activado. Los LED PCM debe estar en verdes. Un controlador EBOD LED verde unidad indica que los caracteres enmarcados EBOD está activada.

4. Activar los caracteres enmarcados principal Voltear los interruptores en ambos PCMs a la posición activado. Ahora debería en todo el sistema.

5. Compruebe que el LED SA verdes, lo que garantiza que la conexión entre los caracteres enmarcados EBOD y los caracteres enmarcados principal es correcta.

## <a name="turn-on-a-device-after-a-power-loss"></a>Activar un dispositivo después de pérdida de energía

Pueden cerrar un corte de energía o interrupciones un dispositivo StorSimple. El corte de luz puede ocurrir en una de las fuentes de alimentación o ambas fuentes de alimentación. Los pasos de recuperación son diferentes dependiendo de si el dispositivo es un modelo de 8100 o 8600. 8100 tiene un único alojamiento principal, mientras que la 8600 es un dispositivo de alojamiento dual con un alojamiento principal y enmarcado EBOD. Esta sección describe el procedimiento de recuperación para cada escenario.

- [Dispositivo con caracteres enmarcados principal solo](#8100)

- [Dispositivo con caracteres enmarcados EBOD](#8600)

### <a name="device-with-primary-enclosure-only-a-name8100"></a>Dispositivo con caracteres enmarcados principal solo<a name="8100">

El sistema puede continuar su funcionamiento normal si hay pérdida de energía a uno de sus fuentes de alimentación. Sin embargo, para garantizar alta disponibilidad del dispositivo, restaure la potencia la fuente de alimentación tan pronto como sea posible.

Si hay un corte de energía o interrupciones de energía en ambas fuentes de alimentación, el sistema se cerrará de manera ordenada y controlada. Cuando se restaura la potencia, el sistema se activará automáticamente.

### <a name="device-with-ebod-enclosure-a-name8600"></a>Dispositivo con caracteres enmarcados EBOD<a name="8600">

#### <a name="power-loss-on-one-power-supply"></a>Pérdida de energía en un power de suministro

El sistema puede continuar su funcionamiento normal si hay pérdida de energía a uno de sus fuentes de alimentación en los caracteres enmarcados EBOD o contenedor principal. Sin embargo, para asegurarse de alta disponibilidad del dispositivo, restaure power a la fuente de alimentación tan pronto como sea posible.

#### <a name="power-loss-on-both-power-supplies-on-primary-and-ebod-enclosures"></a>Pérdida de alimentación de ambas fuentes de alimentación en principal y documentos adjuntos EBOD

Si hay un corte de energía o interrupciones de energía en ambas fuentes de alimentación, los caracteres enmarcados EBOD se cerrarán inmediatamente y se cerrará el alojamiento principal de una manera controlada y ordenada. Cuando se restaura la alimentación, el equipo se iniciará automáticamente.

Si está desactivada de forma manual la potencia, tome los siguientes pasos para restaurar la alimentación al sistema.

1. Activar los caracteres enmarcados EBOD.

2. Después de la inclusión EBOD está en, active los caracteres enmarcados principal.

### <a name="power-loss-on-both-power-supplies-on-ebod-enclosure"></a>Pérdida de alimentación de ambas fuentes de alimentación en caracteres enmarcados EBOD

Al configurar los cables, debe asegurarse de que el EBOD nunca está conectado solo a una PDU independiente. Si la EBOD y caracteres enmarcados principal no al mismo tiempo, el sistema recuperará.

Si solo los caracteres enmarcados EBOD se produce un error en las fuentes de alimentación, el sistema no se recuperará automáticamente. Realice los pasos siguientes para activar el sistema y restaurarlo a un buen estado:

1. Si está activado el alojamiento principal, desactivar Power y módulos de refrigeración (PCMs).

2. Espere unos minutos apagar el sistema.

3. Activar los caracteres enmarcados EBOD.

4. Después de la inclusión EBOD está en, active los caracteres enmarcados principal.

## <a name="turn-on-a-device-after-the-primary-and-ebod-enclosure-connection-is-lost"></a>Activar un dispositivo después de principal y se pierde la conexión de caracteres enmarcados EBOD

Si la conexión se pierde entre el controlador de reserva y el controlador EBOD correspondiente, el dispositivo continúa funcionando. Si se pierde la conexión entre el controlador activos de sistema y la EBOD correspondiente, debe tener lugar la migración tras error y el dispositivo debe seguir trabajando con normalidad.

Cuando se quitan ambos cables SCSI conectado en serie (SA) o se rompe la conexión entre los caracteres enmarcados EBOD y los caracteres enmarcados principal, el dispositivo deja de funcionar. En este momento, realice los pasos siguientes.

### <a name="to-turn-on-the-device-after-connection-is-lost"></a>Para activar el dispositivo después de que se pierda la conexión

1. Obtener acceso a la parte posterior del dispositivo.

2. Si se interrumpe la conexión por cable SA entre los caracteres enmarcados EBOD y los caracteres enmarcados principal, calle todas las SA LED en los caracteres enmarcados EBOD estará desactivado.

3. Cierre Power y módulos de refrigeración (PCMs) en los caracteres enmarcados EBOD y la principal.

4. Espere hasta que todas las luces en el reverso de ambos documentos adjuntos el desactivar.

5. Vuelva a introducir los cables de SA y asegúrese de que hay una buena conexión entre los caracteres enmarcados EBOD y los caracteres enmarcados principal.

6. Activar los caracteres enmarcados EBOD primera Voltear ambos modificadores PCM a la posición activado.

7. Asegúrese de que los caracteres enmarcados EBOD son en comprobando que el LED verde está activada.

8. Activar los caracteres enmarcados principal.

9. Garantizar que los caracteres enmarcados principal en comprobando que el LED verde controlador está activada.

10. Compruebe que la conexión de caracteres enmarcados EBOD con los caracteres enmarcados principal es buena comprobando que la calle SA LED (cuatro por controlador EBOD) están en todos.

>[AZURE.IMPORTANT] Si los cables de SA están defectuosos o la conexión entre los caracteres enmarcados EBOD y los caracteres enmarcados principal es no válida, al encender el sistema, pasará al modo de recuperación. Ponte [en contacto con soporte técnico de Microsoft](storsimple-contact-microsoft-support.md) si esto sucede.

## <a name="turn-off-a-running-device"></a>Desactivar un dispositivo de ejecución

Un dispositivo StorSimple ejecución posible que tenga que cerrar si se mueve, fuera de servicio, o si tiene un componente defectuoso que debe reemplazarse. Los pasos son diferentes dependiendo de si el dispositivo de StorSimple es un 8100 o un modelo de 8600. 8100 tiene un único alojamiento principal, mientras que la 8600 es un dispositivo de alojamiento dual con un alojamiento principal y enmarcado EBOD. Esta sección describe los pasos para cerrar un dispositivo de ejecución.

- [Dispositivo con caracteres enmarcados principal](#8100a)

- [Dispositivo con caracteres enmarcados EBOD](#8600a)

### <a name="device-with-primary-enclosure-a-name8100a"></a>Dispositivo con caracteres enmarcados principal<a name="8100a"> 

Para cerrar el dispositivo de manera ordenada y controlada, puede hacerlo a través del portal clásico Azure o a través de Windows PowerShell para StorSimple. 

>[AZURE.IMPORTANT] ¿Cerrar un dispositivo ejecución mediante el botón de alimentación en la parte posterior del dispositivo.
>
>Antes de cerrar el dispositivo, asegúrese de que todos los componentes del dispositivo están correcto. En el portal de clásico Azure, vaya a **dispositivos** > **Mantenimiento** > **Estado de Hardware**y compruebe que el estado de todos los componentes es verde. Esto es cierto solo para el estado del sistema. Si el sistema se se cerró para reemplazar un componente defectuoso, verá un error (rojo) o (amarillo) estado del componente correspondiente en el **Estado de Hardware**de degradado.

Después de obtener acceso a Windows PowerShell para StorSimple o el portal Azure clásico, siga los pasos de [Apagar un dispositivo StorSimple](storsimple-manage-device-controller.md#shut-down-a-storsimple-device). 

### <a name="device-with-ebod-enclosure-a-name8600a"></a>Dispositivo con caracteres enmarcados EBOD<a name="8600a">

>[AZURE.IMPORTANT] Antes de cerrar el alojamiento principal y los caracteres enmarcados EBOD, compruebe que todos los componentes de dispositivo correcto. En el portal de clásico Azure, vaya a **dispositivos** > **Mantenimiento** > **Estado de Hardware**y compruebe que todos los componentes están correcto.

#### <a name="to-shut-down-a-running-device-with-ebod-enclosure"></a>Para cerrar un dispositivo con caracteres enmarcados EBOD ejecución

1. Siga todos los pasos indicados en [Apagar un dispositivo StorSimple](storsimple-manage-device-controller.md#shut-down-a-storsimple-device) para el alojamiento principal.

2. Cuando se cierra el alojamiento principal, cierre el EBOD voltear la potencia y refrigeración módulo (PCM) modificadores.

3. Para comprobar que se ha cerrado la EBOD, compruebe que las luces en el reverso de los caracteres enmarcados EBOD están desactivados.

>[AZURE.NOTE] No se deben quitar los cables de SA que se utilizan para conectar los caracteres enmarcados EBOD al entorno principal hasta después de que el sistema se cierra.

## <a name="next-steps"></a>Pasos siguientes

[Contacto de soporte de Microsoft](storsimple-contact-microsoft-support.md) si se producen problemas al encender o apagar un dispositivo StorSimple.

