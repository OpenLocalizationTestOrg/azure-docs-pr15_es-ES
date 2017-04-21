<properties 
   pageTitle="Reemplazar un PCM en el dispositivo StorSimple | Microsoft Azure"
   description="Se explica cómo quitar y reemplazar la potencia y refrigeración módulo (PCM) en el dispositivo StorSimple"
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
   ms.date="08/18/2016"
   ms.author="alkohli" />

# <a name="replace-a-power-and-cooling-module-on-your-storsimple-device"></a>Reemplazar una potencia y refrigeración módulo en el dispositivo StorSimple

## <a name="overview"></a>Información general

La potencia y refrigeración módulo (PCM) en el dispositivo de Microsoft Azure StorSimple consta de una fuente de alimentación y ventiladores controlados por la principal y los documentos adjuntos EBOD. Hay solo un modelo de PCM que está certificada para cada caracteres enmarcados. Los caracteres enmarcados principal está certificada para una 764 PCM W y los caracteres enmarcados EBOD está certificada para una 580 PCM W. Aunque la PCMs para los caracteres enmarcados principal y los caracteres enmarcados EBOD son diferentes, el procedimiento de reemplazo es idéntico.

En este tutorial se explica cómo:

- Quitar un PCM
- Instalar un reemplazo PCM

>[AZURE.IMPORTANT] Antes de quitar y reemplazar un PCM, revise la información de seguridad de [reemplazo de componentes de hardware de StorSimple](storsimple-hardware-component-replacement.md).

## <a name="before-you-replace-a-pcm"></a>Antes de reemplazar un PCM

Tenga en cuenta los siguientes problemas importantes antes de reemplazar el PCM:

- Si se produce un error en la fuente de alimentación de la PCM, deje el módulo defectuoso instalado, pero quite el cable de alimentación. El ventilador continuará recibiendo alimentación de los caracteres enmarcados y seguir proporcionando refrigeración adecuado. Si se produce un error en el ventilador, el PCM debe reemplazarse inmediatamente.

- Antes de quitar el PCM, desconecte la alimentación desde el PCM desactivando el conmutador principal (si está presente) o eliminando física el cable de alimentación. Esto proporciona una advertencia a su sistema que un apagado power es inminente.

- Asegúrese de que el otro PCM es funcional para el funcionamiento de sistema continuo antes de reemplazar el PCM defectuoso. Un PCM defectuoso debe sustituirse por un PCM totalmente operativa tan pronto como sea posible.

- Reemplazo de módulo PCM tarda sólo algunos minutos en completarse, pero debe llevar a cabo 10 minutos después de quitar el error PCM para evitar el sobrecalentamiento.

- Tenga en cuenta que los módulos de reemplazo 764 PCM W desde la fábrica no contienen el módulo de batería de copia de seguridad. Debe quitar la batería de su PCM defectuoso y, a continuación, inserte el módulo de reemplazo antes de realizar la sustitución. Para obtener más información, consulte cómo [quitar e insertar un módulo de batería de copia de seguridad](storsimple-battery-replacement.md).


## <a name="remove-a-pcm"></a>Quitar un PCM

Siga estas instrucciones cuando esté listo para quitar una potencia y refrigeración módulo (PCM) desde el dispositivo de Microsoft Azure StorSimple.

>[AZURE.NOTE] Antes de quitar el PCM, compruebe que tiene un reemplazo correcta (764 W para el alojamiento principal) o 580 W para el alojamiento EBOD.

#### <a name="to-remove-a-pcm"></a>Para quitar un PCM

1. En el portal de Azure clásico, haga clic en **dispositivos** > **Mantenimiento** > **Estado de Hardware**. Comprobar el estado de los componentes PCM en **Componentes compartidos** para identificar qué ha fallado PCM:

     - Si ha fallado una fuente de alimentación de PCM 0, el estado de **Alimentación en PCM 0** será rojo.

     - Si ha fallado una fuente de alimentación de PCM 1, el estado de **Alimentación en PCM 1** será rojo.

     - Si ha fallado el ventilador en PCM 1, el estado de **refrigeración 0 para PCM 0** o **1 refrigeración para PCM 0** será rojo.

2. Busque el error PCM en el reverso de los caracteres enmarcados principal. Si está ejecutando un modelo 8600, identificar los caracteres enmarcados principal consultando el número de identificación de unidad de sistema se muestra en la pantalla LED del panel frontal. Id. de unidad se muestran en los caracteres enmarcados principal predeterminado es **00**, mientras que Id. de unidad se muestran en los caracteres enmarcados EBOD predeterminado es **01**. El diagrama y la tabla siguiente explican el panel frontal de la pantalla LED.

    ![Id. del sistema en el panel frontal de operaciones](./media/storsimple-power-cooling-module-replacement/IC740991.png)

     **Figura 1** Panel frontal del dispositivo  

  	|Etiqueta|Descripción|
  	|:---|:-----------|
  	|1|Botón de silencio|
  	|2|Alimentación del sistema|
  	|3|Errores de módulo|
  	|4|Error de lógica|
  	|5|Pantalla de Id.|

3. El indicador de supervisión LED en el fondo de los caracteres enmarcados principal también puede usarse para identificar el PCM defectuoso. Vea la siguiente tabla para comprender cómo usar los LED para localizar el PCM defectuoso y diagrama. Por ejemplo, si está encendido el LED correspondiente a la **Ventilador un error** , el ventilador dejó. Del mismo modo, si está encendido el LED corresponde al **Éxito de aire Acondicionado** , ha fallado la fuente de alimentación. 

    ![Plano de indicadores de supervisión de PCM LED de dispositivo](./media/storsimple-power-cooling-module-replacement/IC740992.png)

     **Figura 2** Reverso PCM con indicadores LED

  	|Etiqueta|Descripción|
  	|:---|:-----------|
  	|1|Error de alimentación AC|
  	|2|Error de ventilador|
  	|3|Error de la batería|
  	|4|PCM ACEPTAR|
  	|5|Error de power DC|
  	|6|Batería correcto|

4. Consulte el siguiente diagrama de la parte posterior del dispositivo StorSimple para localizar el módulo PCM error. PCM 0 se encuentra a la izquierda y PCM 1 en la derecha. La siguiente tabla explica los módulos.

     ![Plano de módulos del dispositivo caracteres enmarcados principal](./media/storsimple-power-cooling-module-replacement/IC740994.png)

     **Figura 3** Reverso de dispositivo con módulos de complementos 

  	|Etiqueta|Descripción|
  	|:---|:-----------|
  	|1|PCM 0|
  	|2|PCM 1|
  	|3|Controlador de 0|
  	|4|Controlador de 1|

5. Desactivar el PCM defectuoso y desconecte el cable de alimentación. Ahora puede quitar el PCM.

6. Entender el cierre y el lado del controlador PCM entre el pulgar y hágala girar y comprimir juntos para abrir el controlador.

    ![Controlador PCM de apertura](./media/storsimple-power-cooling-module-replacement/IC740995.png)

    **Figura 4** Abrir el controlador PCM

7. Agarre el controlador y quite el PCM.

    ![Quitar dispositivo PCM](./media/storsimple-power-cooling-module-replacement/IC740996.png)

    **Figura 5** Quitar el PCM

## <a name="install-a-replacement-pcm"></a>Instalar un reemplazo PCM

Siga estas instrucciones para instalar un PCM en el dispositivo StorSimple. Asegúrese de que ha insertado el módulo de batería de copia de seguridad antes de instalar la sustitución PCM (se aplica a 764 sólo PCMs W). Para obtener más información, consulte cómo [quitar e insertar un módulo de batería de copia de seguridad](storsimple-battery-replacement.md).

#### <a name="to-install-a-pcm"></a>Para instalar un PCM

1. Compruebe que tiene el PCM de reemplazo correcto para el archivo adjunto. Los caracteres enmarcados principal necesita un 764 PCM W y los caracteres enmarcados EBOD necesita un PCM W 580. No debe intentar usar el PCM W 580 en la caja principal o el PCM W 764 en la caja EBOD. La imagen siguiente muestra dónde puede identificar esta información en la etiqueta adherida al PCM.

    ![Etiqueta de PCM de dispositivo](./media/storsimple-power-cooling-module-replacement/IC740973.png)

    **Figura 6** Etiqueta PCM

2. Comprobar si hay daños en los caracteres enmarcados, preste especial atención a los conectores. 
                                        
    >[AZURE.NOTE] **No instale el módulo si cualquier clavija conector doblada.**

3. Con el controlador PCM en la posición de apertura, deslice el módulo en el entorno.

    ![Instalar el dispositivo PCM](./media/storsimple-power-cooling-module-replacement/IC740975.png)

    **Figura 7** Instalar el PCM

4. Cierre manualmente el controlador PCM. Oirá un clic se activa el controlador de cierre. 
                                        
    >[AZURE.NOTE] Para asegurarse de que han activado las clavijas, puede tug suavemente el controlador sin soltar el cierre. Si el PCM desliza, implica que se ha cerrado el cierre antes de que los conectores atención.

5. Conecte los cables de alimentación a la fuente de alimentación y al PCM.

6. Proteger la fatiga balas seguro. 

7. Activar el PCM.

8. Compruebe que la sustitución se realizó correctamente: en el portal Azure clásico de su servicio de StorSimple Manager, vaya a **dispositivos** > **Mantenimiento** > **Estado de Hardware**. En **Componentes compartidos**, el estado de la PCM debería aparecer en verde. 
                                        
    >[AZURE.NOTE] Puede tardar unos minutos para la sustitución PCM en iniciarse completamente.

## <a name="next-steps"></a>Pasos siguientes

Más información sobre [reemplazo de componentes de hardware de StorSimple](storsimple-hardware-component-replacement.md).
