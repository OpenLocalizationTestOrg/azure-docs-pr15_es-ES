<properties 
   pageTitle="Reemplazar un controlador StorSimple EBOD | Microsoft Azure"
   description="Se explica cómo quitar y reemplazar uno o ambos controladores EBOD en un dispositivo StorSimple 8600."
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

# <a name="replace-an-ebod-controller-on-your-storsimple-device"></a>Reemplazar un controlador EBOD en el dispositivo StorSimple

## <a name="overview"></a>Información general

En este tutorial se explica cómo reemplazar un módulo de controlador EBOD defectuoso en el dispositivo de Microsoft Azure StorSimple. Para reemplazar un módulo de controlador EBOD, necesita:

- Quitar el controlador EBOD defectuoso
- Instalar un nuevo controlador de EBOD

Antes de empezar, tenga en cuenta la siguiente información:

- En blanco módulos EBOD deben insertarse en todas las ranuras sin usar. Los caracteres enmarcados no se enfriar correctamente si se queda abierto un espacio.

- El controlador de EBOD es intercambiables y se pueden quitar o reemplazar. No quite un módulo que ha fallado hasta que tenga un reemplazo. Cuando se inicia el proceso de reemplazo, debe terminar en 10 minutos.

>[AZURE.IMPORTANT] Antes de intentar quitar o reemplazar cualquier componente StorSimple, asegúrese de que revise las [convenciones de iconos de seguridad](storsimple-safety.md#safety-icon-conventions) y otras [precauciones](storsimple-safety.md).

## <a name="remove-an-ebod-controller"></a>Quitar un controlador EBOD

Antes de reemplazar el módulo de controlador EBOD error en el dispositivo StorSimple, asegúrese de que el otro módulo de controlador EBOD está activa y en funcionamiento. El procedimiento y la tabla siguiente explican cómo quitar el módulo de controlador EBOD.

#### <a name="to-remove-an-ebod-module"></a>Para quitar un módulo EBOD

1. Abra el portal de clásico de Azure.

2. Vaya a **dispositivos** > **Mantenimiento** > **Estado de Hardware**y compruebe que el estado de LED del módulo de controlador EBOD active es verde y el LED del módulo de controlador EBOD error es rojo.

3. Busque el módulo de controlador EBOD error al final del dispositivo.

4. Quite los cables que conectan el módulo de controlador EBOD en el controlador antes de tomar el módulo EBOD fuera del sistema.

5. Anote el puerto SA exacto del módulo de controlador EBOD que se ha conectado al controlador. Se le pedirá para restaurar el sistema esta configuración después de reemplazar el módulo EBOD. 

    >[AZURE.NOTE] Normalmente, se trata de un puerto, que está etiquetado como **Host en** el diagrama siguiente.

    ![Controlador de plano posterior de EBOD](./media/storsimple-ebod-controller-replacement/IC741049.png)

     **Figura 1** Reverso de módulo EBOD

  	|Etiqueta|Descripción|
  	|:----|:----------|
  	|1|LED de error|
  	|2|LED de alimentación|
  	|3|Conectores de SA|
  	|4|LED SA|
  	|5|Puertos serie para uso exclusivo en fábrica|
  	|6|Puerto (Host en)|
  	|7|Puerto B (fuera de Host)|
  	|8|Puerto C (solo para uso de fábrica)|

## <a name="install-a-new-ebod-controller"></a>Instalar un nuevo controlador de EBOD

El procedimiento y la tabla siguiente explican cómo instalar un módulo de controlador EBOD en el dispositivo StorSimple.

#### <a name="to-install-an-ebod-controller"></a>Para instalar un controlador EBOD

1. Comprobar el dispositivo EBOD para daños, especialmente en el conector de interfaz. No instale el nuevo controlador EBOD si cualquier clavija doblada.

2. Con cierres en posición abierta, deslice el módulo en el entorno hasta que los cierres integrarse.

    ![Instalar controlador de EBOD](./media/storsimple-ebod-controller-replacement/IC741050.png)

    **Figura 2**  Instalar el módulo de controlador EBOD

3. Cierre el cierre. Oirá un clic se activa el cierre.

    ![Liberar cierre EBOD](./media/storsimple-ebod-controller-replacement/IC741047.png)

    **Figura 3**  Cierre el cierre de módulo EBOD

4. Volver a conectar los cables. Use la configuración exacta que había antes de la sustitución. Vea la siguiente tabla para obtener más información sobre cómo conectar los cables y diagrama.

    ![Cable de su dispositivo de 4U para power](./media/storsimple-ebod-controller-replacement/IC770723.png)

    **Figura 4**. Volver a conectar cables

  	|Etiqueta|Descripción|
  	|:----|:----------|
  	|1|Caracteres enmarcados principal|
  	|2|PCM 0|
  	|3|PCM 1|
  	|4|Controlador de 0|
  	|5|Controlador de 1|
  	|6|Controlador de EBOD 0|
  	|7|Controlador de EBOD 1|
  	|8|Caracteres enmarcados EBOD|
  	|9|Unidades de distribución de energía|

## <a name="next-steps"></a>Pasos siguientes

Más información sobre [reemplazo de componentes de hardware de StorSimple](storsimple-hardware-component-replacement.md).
