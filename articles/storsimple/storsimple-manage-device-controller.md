<properties
   pageTitle="Administrar los controladores de dispositivo StorSimple | Microsoft Azure"
   description="Obtenga información sobre cómo detener, reiniciar, apagar o restablecer los controladores de dispositivo StorSimple."
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
   ms.date="10/11/2016"
   ms.author="alkohli" />

# <a name="manage-your-storsimple-device-controllers"></a>Administrar los controladores de dispositivo StorSimple

## <a name="overview"></a>Información general

Este tutorial describe las distintas operaciones que se pueden realizar en los controladores de dispositivo StorSimple. Los controladores del dispositivo StorSimple son controladores redundantes (punto) en una configuración de activo y pasivo. En un momento dado, sólo un controlador está activo y está procesando todas las operaciones de red y del disco. El resto de los controladores se encuentra en un modo pasivo. Si se produce un error en el controlador de activo, el controlador de pasivo activo automáticamente.

Este tutorial incluye instrucciones paso a paso para administrar los controladores de dispositivo mediante el:

- **Controladores de** sección de la página de **Mantenimiento** en el servicio Administrador de StorSimple
- Windows PowerShell para StorSimple.

Se recomienda que se encarga de administrar los controladores de dispositivos a través del servicio de administrador de StorSimple. Si solo se puede realizar una acción con Windows PowerShell para StorSimple, el tutorial hace que una nota de la misma.

Después de leer este tutorial, podrá:

- Reiniciar o apagar un controlador de dispositivo StorSimple
- Cerrar un dispositivo StorSimple
- Reinicie el dispositivo de StorSimple valores predeterminados


## <a name="restart-or-shut-down-a-single-controller"></a>Reiniciar o apagar un controlador único

Un controlador reinicio o apagado no es necesario como parte del funcionamiento normal del sistema. Operaciones de cierre para un controlador de dispositivo único son comunes sólo en casos en los que un componente de hardware de dispositivo defectuoso requiere reemplazo. Reinicie el equipo controlador también puede ser necesario en una situación en la que el rendimiento se ve afectado al uso excesivo de la memoria o un controlador que no funciona correctamente. También debe reiniciar un controlador después de reemplazo controlador correctamente, si desea habilitar y probar el dispositivo reemplazado.

Reiniciar un dispositivo no está interrupciones a iniciadores conectados, suponiendo que el controlador pasivo está disponible. Si un controlador pasivo no está disponible o girada desactivada y luego reiniciar el controlador de activo puede provocar la interrupción del servicio y el tiempo de inactividad.

> [AZURE.IMPORTANT]

> - **Un controlador de ejecución no debe física quitarse nunca como esto daría como resultado una pérdida de redundancia y un mayor riesgo de inactividad.**

> - El procedimiento siguiente se aplica solo a dispositivo físico StorSimple. Para obtener información acerca de cómo iniciar, detener y reiniciar el dispositivo virtual, vea [trabajar con el dispositivo virtual](storsimple-virtual-device-u2.md#work-with-the-storsimple-virtual-device).

Puede reiniciar o apagar un controlador de dispositivo solo a través del portal de clásico Azure del servicio de administrador de StorSimple o Windows PowerShell para StorSimple

Para administrar los controladores de dispositivo desde el portal de clásico Azure, realice los pasos siguientes.

#### <a name="to-restart-or-shut-down-a-controller-in-classic-portal"></a>Para reiniciar o apagar un controlador de portal clásica

1. Vaya a **dispositivos > mantenimiento**.

1. Vaya a **Estado del Hardware** y compruebe que el estado de los controladores de ambos en el dispositivo es **correcto**.

    ![Compruebe los controladores de dispositivos de StorSimple están correcto](./media/storsimple-manage-device-controller/IC766017.png)

1. Desde la parte inferior de la página de **Mantenimiento** , haga clic en **Administrar controladores**.

    ![Administrar los controladores de dispositivo StorSimple](./media/storsimple-manage-device-controller/IC766018.png)</br>

    >[AZURE.NOTE] Si no ve **Administrar controladores**, debe instalar las actualizaciones. Para obtener más información, vea [actualizar su dispositivo StorSimple](storsimple-update-device.md).

1. En el cuadro de diálogo **Cambiar la configuración de controlador** , haga lo siguiente:
    1. En la lista desplegable **Seleccionar controlador** , seleccione el dispositivo que desee administrar. Las opciones son controlador 0 y 1 de controlador. Estos controladores también se identifican como activo o pasivo.

        >[AZURE.NOTE] No se puede administrar un controlador si está disponible o girada desactivada y, a continuación, no aparecerá en la lista desplegable.

    2. En la lista desplegable **Seleccionar acción** , elija **reiniciar controlador** o **Apagar controlador**.

        ![Reinicie StorSimple dispositivo pasivo controlador](./media/storsimple-manage-device-controller/IC766020.png)
    3. Haga clic en el icono de verificación ![Icono de comprobación](./media/storsimple-manage-device-controller/IC740895.png).

Esto reiniciar o apagar el controlador. La siguiente tabla resume los detalles de lo que sucede según las selecciones que ha realizado en el cuadro de diálogo **Cambiar la configuración de controlador** .  


|Selección #|Si opta por...|Esto ocurre.|
|---|---|---|
|1.|Reinicie el controlador de pasivo.|Se creará un trabajo para reiniciar el controlador y se le notificará cuando el trabajo se ha creado correctamente. Se iniciará el reinicio del controlador. Puede supervisar el proceso de reinicio accediendo **servicio > paneles > Ver registros de operación** y, a continuación, filtrar por parámetros específicos en el servicio.|
|2.|Reinicie el controlador de activo.|Verá la siguiente advertencia: "si reiniciar el controlador de activo, el dispositivo no se sobre el controlador de pasivo. ¿Desea continuar?" </br>Si decide continuar con esta operación, los siguientes pasos serán idénticos a los que se utilizan para reiniciar el controlador de pasivo (consulte selección 1).|
|3.|Apagar el controlador pasivo.|Verá el siguiente mensaje: "una vez completado la apagado, deberá insertar el botón de encendido en el controlador de activarla. ¿Está seguro de que desea cerrar este controlador? " </br>Si decide continuar con esta operación, los siguientes pasos serán idénticos a los que se utilizan para reiniciar el controlador de pasivo (consulte selección 1).|
|4.|Apagar el controlador activo.|Verá el siguiente mensaje: "una vez completado la apagado, deberá insertar el botón de encendido en el controlador de activarla. ¿Está seguro de que desea cerrar este controlador? " </br>Si decide continuar con esta operación, los siguientes pasos serán idénticos a los que se utilizan para reiniciar el controlador de pasivo (consulte selección 1).|


#### <a name="to-restart-or-shut-down-a-controller-in-windows-powershell-for-storsimple"></a>Para reiniciar o apagar un controlador de Windows PowerShell para StorSimple
Realice los pasos siguientes para apagar o reiniciar un único controlador en el dispositivo StorSimple desde el portal de clásico de Azure.


1. Acceder al dispositivo mediante la consola serie o una sesión de telnet desde un equipo remoto. Conectarse a controlador 0 o 1 controlador siguiendo los pasos de [PuTTY usar para conectarse a la consola de serie de dispositivo](storsimple-deployment-walkthrough.md#use-putty-to-connect-to-the-device-serial-console).

1. En el menú de consola serie, seleccione la opción 1, **inicie sesión con acceso completo**.

1. En el mensaje de titular, tome nota del controlador está conectado a (controlador de 0 o 1 controlador) y si está activo o el controlador pasivo (de reserva).
    - Para cerrar un único controlador, en el símbolo del sistema, escriba:

        `Stop-HcsController`

        Esto se cerrará el controlador que está conectado a. Si detener el controlador de activo, a continuación, se producirá sobre el controlador de pasivo antes de que se cierre.
    - Para reiniciar un controlador, en el símbolo del sistema, escriba:

        `Restart-HcsController`

        Esto reinicie el dispositivo que está conectado a. Si reiniciar el controlador de activo, se conmutar al controlador pasivo antes del reinicio.


## <a name="shut-down-a-storsimple-device"></a>Cerrar un dispositivo StorSimple

Esta sección explica cómo cerrar un ejecución o un dispositivo incorrecto de StorSimple desde un equipo remoto. Un dispositivo está desactivado después de cerrar los controladores de dispositivo. Un dispositivo se cierra cuando el dispositivo se mueve física o sacar de servicio.

> [AZURE.IMPORTANT] Antes de cerrar el dispositivo, compruebe el estado de los componentes del dispositivo. Vaya a **dispositivos > Mantenimiento > estado de Hardware** y compruebe que el estado de LED de todos los componentes es verde. Dispositivo de buen estado tendrá un estado de color verde. Si el dispositivo que se se cerró para reemplazar un componente defectuoso, verá un error (rojo) o un estado de degradado (amarillo) de los componentes correspondientes.

#### <a name="to-shut-down-a-storsimple-device"></a>Para cerrar un dispositivo StorSimple

1. Use el procedimiento [reiniciar o apagar un controlador](#restart-or-shut-down-a-single-controller) para identificar y cerrar el controlador pasivo en el dispositivo. Puede realizar esta operación en el portal de clásico Azure o en Windows PowerShell para StorSimple.
2. Repita el paso anterior para cerrar el controlador de activo.
3. Ahora debe ver el plano posterior del dispositivo. Después de que los controladores de dos se cierran por completo, los LED de estado en los controladores de ambos debe ser parpadeando rojo. Si necesita desactivar por completo el dispositivo en este momento, voltear los interruptores de alimentación y módulos de refrigeración (PCMs) hasta la posición desactivar. Esto debe desactivar el dispositivo.


<!--#### To shut down a StorSimple device in Windows PowerShell for StorSimple

1. Connect to the serial console of the StorSimple device by following the steps in [Use PuTTY to connect to the device serial console](storsimple-deployment-walkthrough.md#use-putty-to-connect-to-the-serial-console).

1. In the serial console menu, verify from the banner message that the controller you are connected to is the passive controller. If you are connected to the active controller, disconnect from this controller and connect to the other controller.

1. In the serial console menu, choose option 1, **log in with full access**.

1. At the prompt, type:

    `Stop-HCSController`

    This should shut down the current controller. To verify whether the shutdown has finished, check the back of the device. The controller status LED should be solid red.

1. Repeat steps 1 through 4 to connect to the active controller and then shut it down.

1. After both the controllers are completely shut down, the status LEDs on both should be blinking red. If you need to turn off the device completely at this time, flip the power switches on both Power and Cooling Modules (PCMs) to the OFF position.-->

## <a name="reset-the-device-to-factory-default-settings"></a>Restablecer el dispositivo a la configuración predeterminada de fábrica

> [AZURE.IMPORTANT] Si necesita restablecer el dispositivo a la configuración predeterminada de fábrica, póngase en contacto con Microsoft Support. El procedimiento descrito a continuación solo se debe usar junto con Microsoft Support.

Este procedimiento describe cómo restablecer el dispositivo de Microsoft Azure StorSimple a valores predeterminados de fábrica con Windows PowerShell para StorSimple.
Restablecer un dispositivo, quita todos los datos y la configuración de todo el clúster de forma predeterminada.

Siga estos pasos para restablecer el dispositivo de Microsoft Azure StorSimple valores predeterminados de fábrica:

### <a name="to-reset-the-device-to-default-settings-in-windows-powershell-for-storsimple"></a>Para restablecer el dispositivo a la configuración predeterminada en Windows PowerShell para StorSimple

1. El dispositivo de acceso a través de su consola serie. Compruebe el mensaje de pancarta para asegurarse de que está conectado al controlador activo.

1. En el menú de consola serie, seleccione la opción 1, **inicie sesión con acceso completo**.

1. Cuando se le solicite, escriba el comando siguiente para restablecer todo el clúster, quitar todos los datos, metadatos y controlador de la configuración:

    `Reset-HcsFactoryDefault`

    Para restablecer en su lugar un único controlador, use el cmdlet de [Restablecer HcsFactoryDefault](http://technet.microsoft.com/library/dn688132.aspx) con la `-scope` parámetro.)

    El sistema reinicia varias veces. Se le notificará cuando el restablecimiento se ha completado correctamente. Dependiendo del modelo de sistema, pueden tardar 45 y 60 minutos para un dispositivo 8100 y 60 y 90 minutos para un 8600 finalizar este proceso.

    > [AZURE.TIP]

    > - Si está usando la actualización 1.2 o anterior utilizar el `–SkipFirmwareVersionCheck` parámetro omitir la comprobación de la versión de firmware (en caso contrario, verá un error de coincidencia de firmware: restablecimiento de fábrica no puede continuar debido a un error de coincidencia en las versiones de firmware. ).

    > - El procedimiento de restablecimiento de fábrica puede fallar para StorSimple los dispositivos que ejecutan actualización 1 o 1.1 en el portal de administración pública y han realizado un reemplazo con éxito uno o dos controladores (con controladores de reemplazo enviados con software de actualización previa 1). Esto ocurre cuando el generador de restablece imagen se valida la presencia de un archivo de SHA1 en el dispositivo que no existe para 1 anteriores a la actualización de software. Si ve que este generador restablece el error, póngase en contacto con Microsoft Support para ayudarle con los siguientes pasos. Este problema no se produce con controladores de reemplazo que se hayan enviado desde fábrica con actualización 1 o posterior del software.


## <a name="questions-and-answers-about-managing-device-controllers"></a>Preguntas y respuestas acerca de cómo administrar los controladores de dispositivo

En esta sección, hemos resumido algunas de las preguntas más frecuentes sobre la administración de los controladores de dispositivo de StorSimple.

**PREGUNTAS.** ¿Qué ocurre si los controladores de ambos en mi dispositivo son correcto y girada en y que reiniciar o apagar el controlador activo?

**A.** Si los controladores de ambos en el dispositivo son girada y en buen estado, se le pedirá confirmación. Puede optar por:

- **Reinicie el controlador de activo** , se le notificará que reiniciar un controlador active hará que el dispositivo conmutar en el controlador de pasivo. Se reinicia el controlador.

- **Cerrar un controlador active** : se le notificará que apagar un controlador active da de inactividad. También necesitará insertar el botón de encendido en el dispositivo para activar el controlador.

**PREGUNTAS.** ¿Qué ocurre si el controlador de pasivo en mi dispositivo no está disponible o girada desactivada y que reiniciar o apagar el controlador activo?

**A.** Si el controlador pasivo en su dispositivo está disponible o girada desactivada y decide:

- **Reinicie el controlador de activo** , se le notificará que continuar la operación se producirá una interrupción temporal del servicio y se le pedirá confirmación.

- **Cerrar un controlador active** : se le notificará que continuar la operación se traducirán en el tiempo de inactividad y que debe presionar el botón de encendido en uno o ambos controladores para activar el dispositivo. Se le pedirá confirmación.

**PREGUNTAS.** ¿Cuando realiza del controlador reinicio o apagado deja de progreso?

**A.** Reiniciar o apagar un controlador puede fallar si:

- Actualización de un dispositivo está en curso.

- Reinicie el equipo controlador ya está en curso.

- Un apagado controlador ya está en curso.

**PREGUNTAS.** ¿Cómo puede averiguar si un controlador se reinicia o apagar?

**A.** Puede comprobar el estado del controlador en la página Mantenimiento. El estado del controlador indica si se reinicia o apagar un controlador. Además, la página de alertas contendrá una alerta informativa si reinicia o cierra el controlador. Las operaciones de reinicio y apagado de controlador también se graban en los registros de operación. Para obtener más información acerca de los registros de operación, vaya a [Ver los registros de operación](storsimple-service-dashboard.md#view-the-operations-logs).

**PREGUNTAS.** ¿Hay algún impacto en la i/OS como resultado de la migración tras error del controlador?

**A.** Las conexiones de TCP entre iniciadores y controlador active se restablecerán como resultado de la migración tras error del controlador, pero se restablece cuando el controlador de pasivo asume la operación. Puede haber una pausa temporal (menor que 30 segundos) en la actividad de i/OS entre iniciadores y el dispositivo durante el curso de esta operación.

**PREGUNTAS.** ¿Cómo volver de mi controlador de servicio después de cerrar y quitar?

**A.** Para devolver un controlador de servicio, debe insertar en el chasis como se describe en [Reemplazar un módulo de controlador en el dispositivo StorSimple](storsimple-controller-replacement.md).

## <a name="next-steps"></a>Pasos siguientes

- Si tiene problemas con los controladores de dispositivo de StorSimple no puede resolver mediante el uso de los procedimientos descritos en este tutorial, [póngase en contacto con soporte técnico de Microsoft](storsimple-contact-microsoft-support.md).

- Para obtener más información sobre cómo usar el servicio Administrador de StorSimple, vaya a [usar el servicio de administrador de StorSimple para administrar el dispositivo de StorSimple](storsimple-manager-service-administration.md).
