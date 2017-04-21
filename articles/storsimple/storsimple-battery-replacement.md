<properties 
   pageTitle="Reemplazar la batería en un dispositivo StorSimple | Microsoft Azure"
   description="Describe cómo quitar, reemplazar y mantener el módulo de batería de copia de seguridad en el dispositivo StorSimple."
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
   ms.date="08/17/2016"
   ms.author="alkohli" />

# <a name="replace-the-backup-battery-module-on-your-storsimple-device"></a>Reemplazar el módulo de batería de copia de seguridad en el dispositivo StorSimple

## <a name="overview"></a>Información general

Los caracteres enmarcados principal Power y refrigeración módulo (PCM) en el dispositivo de Microsoft Azure StorSimple tiene un paquete de batería adicional. Este módulo proporciona power para que el dispositivo de StorSimple puede guardar los datos si hay pérdida de alimentación de CA al entorno principal. Esta batería se conoce como el *módulo de la batería de copia de seguridad*. El módulo de copia de seguridad batería existe solo para los caracteres enmarcados principal en el dispositivo de StorSimple (el alojamiento EBOD no contiene un módulo de batería de reserva). 

En este tutorial se explica cómo:

- Quitar el módulo de batería de reserva 
- Instalar un nuevo módulo de batería de reserva
- Mantener el módulo de batería de reserva

>[AZURE.IMPORTANT] Antes de quitar y reemplazar un módulo de batería de copia de seguridad, revise la información de seguridad en la [Introducción a la sustitución de componentes de hardware de StorSimple](storsimple-hardware-component-replacement.md).

## <a name="remove-the-backup-battery-module"></a>Quitar el módulo de batería de reserva

El módulo de batería de copia de seguridad de su dispositivo StorSimple es una unidad que puede reemplazar el campo. Antes de que está instalada en el PCM, el módulo de la batería debe almacenarse en su embalaje original. Siga estos pasos para quitar la batería de copia de seguridad.

#### <a name="to-remove-the-backup-battery-module"></a>Para quitar el módulo de batería de reserva

1. En el portal de clásico Azure, vaya a **dispositivos** > **Mantenimiento** > **Estado de Hardware**. En **Componentes compartidos**, observe el estado de la batería.

2. Identificar el PCM en el que ha fallado la batería. Figura 1 muestra la parte posterior del dispositivo StorSimple.

    ![Plano de módulos del dispositivo caracteres enmarcados principal](./media/storsimple-battery-replacement/IC740994.png)

    **Figura 1** Reverso de dispositivo principal que muestra los módulos PCM y controlador

  	|Etiqueta|Descripción|
  	|:----|:----------|
  	|1|PCM 0|
  	|2|PCM 1|
  	|3|Controlador de 0|
  	|4|Controlador de 1|

    Tal como se muestra por número 3 en la figura 2, el indicador de supervisión LED en PCM 0 que corresponde a **Errores de la batería** debe estar iluminado.

    ![Plano de LED de indicador de supervisión de PCM de dispositivo](./media/storsimple-battery-replacement/IC740992.png)

    **Figura 2** Reverso PCM que muestra los indicadores LED de supervisión

  	|Etiqueta|Descripción|
  	|:---|:-----------|
  	|1|Error de alimentación AC|
  	|2|Error de ventilador|
  	|3|Error de la batería|
  	|4|PCM ACEPTAR|
  	|5|Error de power DC|
  	|6|Batería correcto|

3. Para quitar el PCM con una batería de error, siga los pasos de [quitar un PCM](storsimple-power-cooling-module-replacement.md#remove-a-pcm).

4. Con PCM quitado, levantar y Girar hacia arriba el identificador del módulo batería tal como se indica en la siguiente ilustración y extraer hasta quitar la batería.

    ![Quitando batería PCM](./media/storsimple-battery-replacement/IC741019.png)

    **Figura 3** Quitando la batería el PCM

5. Coloque el módulo en la unidad que puede reemplazar el campo embalaje.

6. Devolver la unidad defectuosa a Microsoft para el mantenimiento y tratamiento adecuado.

## <a name="install-a-new-backup-battery-module"></a>Instalar un nuevo módulo de batería de reserva

Realice los pasos siguientes para instalar el módulo de batería de reemplazo en el PCM en la caja del dispositivo StorSimple principal.

#### <a name="to-install-the-battery-module"></a>Para instalar el módulo de batería

1. Coloque el módulo de batería de copia de seguridad en la orientación correcta en el PCM.

2. Presione el identificador del módulo batería completamente para acomodar el conector.

3. Reemplazar el PCM en la caja principal siguiendo las instrucciones de [reemplazar una potencia y refrigeración módulo en el dispositivo StorSimple](storsimple-power-cooling-module-replacement.md).

4. Una vez completada la sustitución, vaya a **dispositivos** > **Mantenimiento** > **Estado de Hardware** en el portal de clásico de Azure. Comprobar el estado de la batería para asegurarse de que la instalación es correcta. Estado verde indica que la batería es correcto.

## <a name="maintain-the-backup-battery-module"></a>Mantener el módulo de batería de reserva

En el dispositivo StorSimple, el módulo de batería de reserva proporciona power al controlador durante un evento de pérdida de energía. Permite que el dispositivo StorSimple guardar los datos críticos antes de apagar de manera controlada. Con dos pilas completamente cargadas en la PCMs, el sistema puede controlar dos eventos de pérdida consecutivos.

En el portal de clásico de Azure, el **Estado de Hardware** en la página **Mantenimiento** indica si la batería está mal o se está acercando el final del ciclo de vida. El estado de la batería viene indicado por **batería en PCM 0** o **batería en PCM 1** en **Componentes compartidos**. Esta página le mostrará un estado de **degradado** para alcanzar el final del ciclo de vida y se ha alcanzado el **error** para final del ciclo de vida. 

>[AZURE.NOTE] La batería puede informar de **errores** cuando simplemente necesita cargará.
 
Si aparece el estado de **degradado** , se recomienda el siguiente curso de acción:

- El sistema puede haber experimentado una pérdida de alimentación recientes o las pilas pueden experimentar mantenimiento periódico. Observe el sistema de 12 horas antes de continuar.

    - Si el estado sigue siendo **degradado** después de 12 horas de conexión continua a CA con los controladores y PCMs ejecutando, la batería debe reemplazarse. Póngase [en contacto con soporte técnico de Microsoft](storsimple-contact-microsoft-support.md) para un módulo de batería de reserva de reemplazo.

    - Si el estado se convierte en Aceptar después de 12 horas, la batería está operativa y sólo es necesario un cargo de mantenimiento.

- Si no ha habido una pérdida de alimentación de CA asociada y el PCM está encendido y conectado a CA, la batería debe reemplazarse. [Contacto de soporte de Microsoft](storsimple-contact-microsoft-support.md) para solicitar un módulo de batería de reserva de reemplazo.

>[AZURE.IMPORTANT] Desechar la batería error según las normas nacionales y regionales. 

## <a name="next-steps"></a>Pasos siguientes

Más información sobre [reemplazo de componentes de hardware de StorSimple](storsimple-hardware-component-replacement.md).
