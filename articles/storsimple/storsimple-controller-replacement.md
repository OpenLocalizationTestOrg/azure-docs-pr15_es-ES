<properties 
   pageTitle="Reemplazar un controlador de dispositivo StorSimple | Microsoft Azure"
   description="Se explica cómo quitar y reemplazar uno o ambos módulos de controlador en el dispositivo StorSimple."
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

# <a name="replace-a-controller-module-on-your-storsimple-device"></a>Reemplazar un módulo de controlador en el dispositivo StorSimple

## <a name="overview"></a>Información general

En este tutorial se explica cómo quitar y reemplazar uno o ambos módulos de controlador en un dispositivo de StorSimple. También se explica la lógica subyacente para los escenarios de reemplazo de controlador único y doble.

>[AZURE.NOTE] Antes de realizar un reemplazo de controlador, se recomienda actualizar el firmware del controlador siempre a la última versión.
>
>Para evitar daños en el dispositivo StorSimple, no retirar el controlador hasta que el LED se muestran como uno de estos procedimientos:
>
>- Las luces están OFF.
>- LED 3, ![icono de marca de verificación verde](./media/storsimple-controller-replacement/HCS_GreenCheckIcon.png), y ![icono de la cruz roja](./media/storsimple-controller-replacement/HCS_RedCrossIcon.png) están parpadeando, y LED 0 y 7 LED están **activado**.

La siguiente tabla muestra los escenarios de reemplazo de controladores admitidos.

|Mayúsculas y minúsculas|Escenario de sustitución|Procedimiento aplicable|
|:---|:-------------------|:-------------------|
|1|Un controlador se encuentra en un estado de error, el controlador de otro está activa y en buen estado.|[Reemplazo de controlador único](#replace-a-single-controller), que se describe la [lógica de reemplazo de controlador único](#single-controller-replacement-logic), así como los [pasos de reemplazo](#single-controller-replacement-steps).|
|2|Los controladores de ambos con errores y de reemplazo. Existen chasis, discos, caracteres enmarcados and.disk.|[Reemplazo de controlador doble](#replace-both-controllers), que describe la [lógica de reemplazo de controlador doble](#dual-controller-replacement-logic), así como los [pasos de reemplazo](#dual-controller-replacement-steps). |
|3|Se cambian los controladores desde el mismo dispositivo o desde varios dispositivos. Existen chasis, los discos y los documentos adjuntos de disco.|Aparecerá un mensaje de alerta de franja no coinciden.|
|4|Falta un controlador y se produce un error en el otro controlador.|[Reemplazo de controlador doble](#replace-both-controllers), que describe la [lógica de reemplazo de controlador doble](#dual-controller-replacement-logic), así como los [pasos de reemplazo](#dual-controller-replacement-steps).|
|5|Error de uno o dos controladores. El dispositivo no puede acceder a través de la consola serie o Windows PowerShell remoto.|[Contacto de soporte de Microsoft](storsimple-contact-microsoft-support.md) para obtener un procedimiento de reemplazo de controlador manual.|
|6|Los controladores de tienen una versión de compilación diferente, puede deberse a:<ul><li>Controladores de tener una versión diferente de software.</li><li>Controladores de tienen una versión de firmware distinta.</li></ul>|Si las versiones de software de controlador son diferentes, la lógica de sustitución detecta que y actualice la versión del software en el controlador de reemplazo.<br><br>Si las versiones de firmware de controlador son diferentes y la versión de firmware antiguo es **no** puede actualizar automáticamente, un mensaje de alerta aparecerá en el portal de clásico de Azure. Debe buscar actualizaciones e instale las actualizaciones de firmware.</br></br>Si las versiones de firmware de controlador son diferentes y la versión antigua de firmware es actualizable automáticamente, la lógica de reemplazo del controlador detecte y se inicia el controlador, el firmware se actualizará automáticamente.|

Debe quitar un módulo de controlador si produce un error. Pueden fallar uno o ambos de los módulos de controlador, lo que podría provocar un reemplazo de controlador único o reemplazo de controlador doble. Para los procedimientos de reemplazo y la lógica detrás de ellos, consulte lo siguiente:

- [Reemplazar un único controlador](#replace-a-single-controller)
- [Reemplazar ambos controladores](#replace-both-controllers)
- [Quitar un controlador](#remove-a-controller)
- [Insertar un controlador](#insert-a-controller)
- [Identifique el controlador activo en el dispositivo](#identify-the-active-controller-on-your-device)

>[AZURE.IMPORTANT] Antes de quitar y reemplazar un controlador, revise la información de seguridad de [reemplazo de componentes de hardware de StorSimple](storsimple-hardware-component-replacement.md).

## <a name="replace-a-single-controller"></a>Reemplazar un único controlador

Cuando uno de los dos controladores del dispositivo de Microsoft Azure StorSimple ha fallado, funciona mal o falta, debe reemplazar un único controlador. 

### <a name="single-controller-replacement-logic"></a>Lógica de reemplazo de controlador único

En un reemplazo de controlador único, primero debe quitar el controlador de error. (El controlador restante en el dispositivo es el controlador activo). Al insertar el controlador de reemplazo, se producen las siguientes acciones:

1. El controlador de reemplazo comienza a comunicarse con el dispositivo StorSimple inmediatamente.

2. Una instantánea del disco duro virtual (VHD) para el controlador activo se copia en el controlador de reemplazo.

3. La instantánea se modifica para que cuando se inicia el controlador de reemplazo de este disco duro virtual, se reconocerá como un controlador de espera.

4. Cuando haya completado las modificaciones, se iniciará el controlador de reemplazo como el controlador de espera.

5. Cuando se están ejecutando los controladores de ambos, el clúster viene en línea.

### <a name="single-controller-replacement-steps"></a>Pasos de reemplazo de controlador único

Si se produce un error en alguno de los controladores del dispositivo de Microsoft Azure StorSimple, realice los pasos siguientes. (Debe ser el controlador de otro activos y en ejecución. Si ambos controladores un error o un funcionamiento incorrecto, vaya a [los pasos de sustitución de controlador doble](#dual-controller-replacement-steps).)

>[AZURE.NOTE] Este proceso puede demorarse 30: 45 minutos para el controlador de reiniciar y recuperar completamente desde el procedimiento de reemplazo de controlador único. El tiempo total de todo el procedimiento, incluyendo asociar los cables es aproximadamente 2 horas.

#### <a name="to-remove-a-single-failed-controller-module"></a>Para quitar un módulo de controlador error único

1. En el portal de Azure clásico, vaya al servicio de administrador de StorSimple, haga clic en la pestaña **dispositivos** y, a continuación, haga clic en el nombre del dispositivo que desea supervisar.

2. Vaya a **Mantenimiento > estado de Hardware**. El estado de controlador de 0 o 1 controlador debería rojo, que indica un error.

    >[AZURE.NOTE] El controlador de error de reemplazo de controlador único siempre es un controlador de espera.

3. Use figura 1 y en la tabla siguiente para localizar el módulo de controlador errores.  

    ![Plano de módulos del dispositivo caracteres enmarcados principal](./media/storsimple-controller-replacement/IC740994.png)

    **Figura 1** Reverso de dispositivo StorSimple

  	|Etiqueta|Descripción|
  	|:----|:----------|
  	|1|PCM 0|
  	|2|PCM 1|
  	|3|Controlador de 0|
  	|4|Controlador de 1|

4. En el controlador de error, quite todos los cables de red conectada los puertos de datos. Si está utilizando un modelo de 8600, también quitar los cables de SA que conectan el controlador al controlador EBOD.

5. Siga los pasos de [quitar un controlador](#remove-a-controller) para quitar el controlador error. 

6. Instale la sustitución de fábrica en la misma casilla desde la que se ha quitado el controlador de error. Esto activa la lógica de reemplazo de controlador único. Para obtener más información, vea [lógica de reemplazo de controlador único](#single-controller-replacement-logic).

7. Mientras avanza la lógica de reemplazo de controlador único en segundo plano, vuelva a conectar los cables. Tenga cuidado al conectar todos los cables de la misma manera que estuvieran conectados antes de la sustitución.

8. Después de reinicia el controlador, compruebe el **estado del controlador** y el **estado de clúster** en el portal de clásico Azure para comprobar que el controlador es un buen estado y está en modo de espera.

>[AZURE.NOTE] Si va a supervisar el dispositivo a través de la consola de serie, puede ver varios reinicia mientras se está recuperando el controlador desde el procedimiento de reemplazo. Cuando se presenta el menú de consola serie, sabrá que la sustitución está completa. Si no aparece el menú dentro de dos horas de inicio de reemplazo de controlador, ponte [en contacto con soporte técnico de Microsoft](storsimple-contact-microsoft-support.md).

## <a name="replace-both-controllers"></a>Reemplazar ambos controladores

Cuando ambos controladores del dispositivo de Microsoft Azure StorSimple error, se mal o faltan, debe reemplazar ambos controladores. 

### <a name="dual-controller-replacement-logic"></a>Lógica de reemplazo de controlador doble

En un reemplazo de controlador doble quitar ambos controladores de error y, a continuación, insertar sustituciones. Cuando se insertan los controladores de dos reemplazo, se producen las siguientes acciones:

1. El controlador de reemplazo de franja 0 comprueba lo siguiente:
 
   1. ¿Está usando versiones actuales de firmware y software?

   2. ¿Es una parte del clúster?

   3. Es el controlador del mismo nivel que se ejecuta y se agrupadas?
                            
    Si ninguna de estas condiciones son verdadera, el controlador busca la última copia de seguridad diaria (que se encuentra en la **nonDOMstorage** en la unidad S). El controlador de copia la instantánea más reciente del disco duro virtual de la copia de seguridad.

2. El controlador de franja 0 utiliza la instantánea a la imagen propiamente dicho.

3. Mientras tanto, el controlador de franja 1 espera controlador 0 para completar la creación de imágenes e iniciar.

4. Después de que se inicia el controlador de 0, controlador 1 detecta el clúster creado por el controlador de 0, que se activa la lógica de reemplazo de controlador único. Para obtener más información, vea [lógica de reemplazo de controlador único](#single-controller-replacement-logic).

5. Después, ambos controladores se ejecutará y se conectará al clúster.

>[AZURE.IMPORTANT] Después de reemplazo de controlador doble, después de configura el dispositivo StorSimple, es fundamental que se tome un manual de copia de seguridad del dispositivo. Copias de seguridad de configuración de dispositivo diarias no se producen hasta después de que han transcurrido 24 horas. Trabajar con el [Soporte técnico de Microsoft](storsimple-contact-microsoft-support.md) para hacer una copia de seguridad de su dispositivo manuales.

### <a name="dual-controller-replacement-steps"></a>Pasos de reemplazo de controlador doble

Este flujo de trabajo se requiere cuando ambos de los controladores del dispositivo de Microsoft Azure StorSimple han fallado. Esto puede suceder en un centro de datos en el que el sistema de refrigeración deja de funcionar y, como resultado, no los controladores de ambos en un breve período de tiempo. Dependiendo de si el dispositivo de StorSimple está desactivado, y si está utilizando un 8600 o un modelo de 8100, se requiere un conjunto diferente de pasos.

>[AZURE.IMPORTANT] Puede tardar 45 minutos en 1 hora para el controlador de reiniciar y recuperar completamente desde un procedimiento de reemplazo de controlador doble. El tiempo total de todo el procedimiento, incluyendo asociar los cables es aproximadamente 2,5 horas.

#### <a name="to-replace-both-controller-modules"></a>Para reemplazar ambos módulos de controlador

1. Si el dispositivo está desactivado, omita este paso y continúe con el paso siguiente. Si el dispositivo está activado, desactive el dispositivo.
                                        
    1. Si está utilizando un modelo de 8600, desactivar primero el alojamiento principal y, a continuación, desactivar el alojamiento EBOD.

    2. Espere a que el dispositivo se cerró por completo. Los indicadores LED en la parte posterior del dispositivo estará desactivado.

2. Quitar todos los cables de red que están conectados a los puertos de datos. Si está utilizando un modelo de 8600, también quitar los cables de SA que se conectan los caracteres enmarcados principal al entorno EBOD.

3. Quite los controladores del dispositivo StorSimple. Para obtener más información, vea [quitar un controlador](#remove-a-controller).

4. Insertar el sustituto de fábrica de controlador de 0 en primer lugar y, a continuación, Insertar controlador 1. Para obtener más información, vea [Insertar un controlador](#insert-a-controller). Esto activa la lógica de reemplazo de controlador doble. Para obtener más información, vea [lógica de reemplazo de controlador doble](#dual-controller-replacement-logic).

5. Mientras avanza la lógica de reemplazo de controlador en segundo plano, vuelva a conectar los cables. Tenga cuidado al conectar todos los cables de la misma manera que estuvieran conectados antes de la sustitución. Consulte las instrucciones detalladas para el modelo en el Cable de la sección del dispositivo de [instalar el dispositivo 8100 StorSimple](storsimple-8100-hardware-installation.md) o [el dispositivo 8600 StorSimple](storsimple-8600-hardware-installation.md).

6. Activar el dispositivo StorSimple. Si está utilizando un modelo de 8600:

    1. Asegúrese de que está activado el alojamiento EBOD en primer lugar.

    2. Espere a que se está ejecutando el alojamiento EBOD.

    3. Activar los caracteres enmarcados principal.

    4. Después de que el primer controlador se reinicia y se encuentra en buen estado, se ejecuta el sistema.

    >[AZURE.NOTE] Si va a supervisar el dispositivo a través de la consola de serie, puede ver varios reinicia mientras se está recuperando el controlador desde el procedimiento de reemplazo. Cuando aparezca el menú de consola serie, sabrá que la sustitución está completa. Si no aparece el menú dentro de 2,5 horas de inicio de reemplazo de controlador, ponte [en contacto con soporte técnico de Microsoft](storsimple-contact-microsoft-support.md).

## <a name="remove-a-controller"></a>Quitar un controlador

Use el siguiente procedimiento para quitar un módulo de controlador defectuoso desde su dispositivo StorSimple.

>[AZURE.NOTE] Las siguientes ilustraciones están controlador 0. Controlador 1, ¿pueden invertirse estos.

#### <a name="to-remove-a-controller-module"></a>Para quitar un módulo de controlador

1. Entender el cierre de módulo entre el pulgar y hágala girar.

2. Comprimir suavemente el pulgar y hágala girar juntos para liberar el bloqueo de controlador.

    ![Liberar seguro del controlador](./media/storsimple-controller-replacement/IC741047.png)

    **Figura 2** Liberar seguro del controlador

2. Use el cierre como un controlador para el controlador del chasis de diapositivas.

    ![Controlador deslizante chasis](./media/storsimple-controller-replacement/IC741048.png)

    **Figura 3** Deslizar el controlador del chasis

## <a name="insert-a-controller"></a>Insertar un controlador

Use el siguiente procedimiento para instalar un módulo de controlador de fábrica después de quitar un módulo defectuoso desde su dispositivo StorSimple.

#### <a name="to-install-a-controller-module"></a>Para instalar un módulo de controlador

1. Compruebe si hay algún daño a los conectores de interfaz. No instale el módulo si cualquiera de las clavijas de conector están dañado o doblado.

2. Deslice el módulo de controlador en el chasis mientras la lanzamiento de cierre. 

    ![Deslizar el controlador a chasis](./media/storsimple-controller-replacement/IC741053.png)

    **Figura 4** Controlador deslizante en el chasis

3. Comenzar el módulo de controlador insertado, cierre el cierre mientras sigue el módulo de controlador de inserción en el chasis. El cierre se activará guiar el controlador en su lugar.

    ![Cierre de controlador de cierre](./media/storsimple-controller-replacement/IC741054.png)

    **Figura 5** Cierre el cierre de controlador

4. Termine cuando cierre encaje en su lugar. Ahora debería ser el LED **Aceptar** en.  

    >[AZURE.NOTE] Puede tardar hasta 5 minutos para el controlador y el LED activar.

5. Para comprobar que la sustitución es correcta, en el portal de clásico Azure, vaya a **dispositivos** > **Mantenimiento** > **Estado de Hardware**y asegúrese de que existen controlador 0 y controlador 1 (el estado es verde).

## <a name="identify-the-active-controller-on-your-device"></a>Identifique el controlador activo en el dispositivo

Hay muchas situaciones, como reemplazo de registro o controlador de dispositivo de la primera vez, que requieren que localice el controlador activo en un dispositivo de StorSimple. El controlador de active procesa todas las operaciones de disco firmware y redes. Puede utilizar cualquiera de los siguientes métodos para identificar el controlador activo:

- [Usar el portal de clásico Azure para identificar el controlador de activo](#use-the-azure-classic-portal-to-identify-the-active-controller)

- [Usar Windows PowerShell para StorSimple para identificar el controlador de activo](#use-windows-powershell-for-storsimple-to-identify-the-active-controller)

- [Comprueba el dispositivo para identificar el controlador de activo físico](#check-the-physical-device-to-identify-the-active-controller)

A continuación se describe cada uno de estos procedimientos.

### <a name="use-the-azure-classic-portal-to-identify-the-active-controller"></a>Usar el portal de clásico Azure para identificar el controlador de activo

En el portal de clásico Azure, vaya a **dispositivos** > **Mantenimiento**y a continuación, desplácese hasta la sección de **controladores** . Aquí puede comprobar qué controlador está activo.

![Identificar el controlador active en Azure portal clásica](./media/storsimple-controller-replacement/IC752072.png)

**Figura 6** Azure portal clásica que muestra el controlador de activo

### <a name="use-windows-powershell-for-storsimple-to-identify-the-active-controller"></a>Usar Windows PowerShell para StorSimple para identificar el controlador de activo

Cuando el dispositivo tiene acceso a través de la consola de serie, se presenta un mensaje de titular. El mensaje pancarta contiene información de dispositivo básicos como modelo, el nombre, la versión del software instalado y el estado del controlador que se tiene acceso. La imagen siguiente muestra un ejemplo de un mensaje de pancarta:

![Mensaje de pancarta serie](./media/storsimple-controller-replacement/IC741098.png)

**Figura 7** Pancarta de controlador de mensaje que muestra 0 como activa

Puede usar el mensaje pancarta para determinar si el controlador a que está conectado está activo o pasivo.

### <a name="check-the-physical-device-to-identify-the-active-controller"></a>Comprueba el dispositivo para identificar el controlador de activo físico

Para identificar el controlador activo en el dispositivo, busque el LED azul sobre el puerto 5 de datos en el reverso de los caracteres enmarcados principal.

Si este LED parpadea, el controlador está activo y el otro controlador está en modo de espera. Use el siguiente diagrama y tabla como ayuda.

![Plano de caracteres enmarcados principal del dispositivo con dataports](./media/storsimple-controller-replacement/IC741055.png)

**Figura 8** Reverso de caracteres enmarcados principal con LED supervisión y puertos de datos

|Etiqueta|Descripción|
|:----|:----------|
|1-6|DATOS 0-5 puertos de red|
|7|LED azul|


## <a name="next-steps"></a>Pasos siguientes

Más información sobre [reemplazo de componentes de hardware de StorSimple](storsimple-hardware-component-replacement.md).
