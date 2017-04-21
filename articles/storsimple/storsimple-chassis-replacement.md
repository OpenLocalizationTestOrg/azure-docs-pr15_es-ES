<properties 
   pageTitle="Reemplazar el chasis en un dispositivo StorSimple | Microsoft Azure"
   description="Describe cómo quitar y reemplazar el chasis para su inclusión principal de StorSimple o caracteres enmarcados EBOD."
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

# <a name="replace-the-chassis-on-your-storsimple-device"></a>Reemplazar el chasis en el dispositivo StorSimple

## <a name="overview"></a>Información general

En este tutorial se explica cómo quitar y reemplazar un chasis en un dispositivo de la serie 8000 StorSimple. El modelo de StorSimple 8100 es un dispositivo único alojamiento (un bastidor), mientras que el 8600 es un dispositivo de caracteres enmarcados dual (dos bastidor). Para un modelo de 8600 hay potencialmente dos chasis que podrían producir un error en el dispositivo: el chasis para el chasis para el alojamiento EBOD o contenedor principal.

En cualquier caso, el chasis de reemplazo que se incluye Microsoft está vacío. Alimentación y módulos de refrigeración (PCMs), módulos de controlador, unidades de disco de estado sólido (SSD), unidades de disco duro (disco duro) o módulos EBOD no se incluirán.

>[AZURE.IMPORTANT] Antes de quitar y reemplazar el chasis, revise la información de seguridad de [reemplazo de componentes de hardware de StorSimple](storsimple-hardware-component-replacement.md).

## <a name="remove-the-chassis"></a>Quitar el chasis

Siga estos pasos para quitar el chasis en su dispositivo StorSimple.

#### <a name="to-remove-a-chassis"></a>Para quitar un chasis

1. Asegúrese de que el dispositivo StorSimple se cierra o desconectarán del origen de energía.

2. Quitar la red y los cables de SA, si procede.

3. Quitar la unidad de bastidor.

4. Quitar cada una de las unidades y observe las ranuras desde la que se quitan. Para obtener más información, vea [quitar la unidad de disco](storsimple-disk-drive-replacement.md#remove-the-disk-drive).

5. En la caja EBOD (si es el chasis que no se pudo), quite los módulos del controlador EBOD. Para obtener más información, vea [quitar un controlador EBOD](storsimple-ebod-controller-replacement.md#remove-an-ebod-controller). 

    En la caja principal (si es el chasis que no se pudo), quite los controladores y observe las ranuras desde la que se quitan. Para obtener más información, vea [quitar un controlador](storsimple-controller-replacement.md#remove-a-controller).

## <a name="install-the-chassis"></a>Instalar el chasis

Realice los pasos siguientes para instalar el chasis en su dispositivo StorSimple.

#### <a name="to-install-a-chassis"></a>Para instalar un chasis

1. Montaje del chasis en bastidor. Para obtener más información, vea [el dispositivo StorSimple 8100 de montaje en bastidor](storsimple-8100-hardware-installation.md#rack-mount-your-storsimple-8100-device) o [su dispositivo StorSimple 8600 de montaje en bastidor](storsimple-8600-hardware-installation.md#rack-mount-your-storsimple-8600-device).

2. Después de que el chasis está montaje en bastidor, instale los módulos de controlador en la misma posición en la que se ha instalado previamente en.

3. Instale las unidades en las posiciones e intervalos que estaban previamente instalados en el mismo.

    >[AZURE.NOTE] Le recomendamos que instale la SSDs en las casillas en primer lugar y, a continuación, instalar el disco duro.

2. Con el dispositivo en el bastidor y los componentes instalados, conectar el dispositivo a los orígenes de alimentación adecuada y activar el dispositivo. Para obtener información detallada, consulte [Cable de su dispositivo StorSimple 8100](storsimple-8100-hardware-installation.md#cable-your-storsimple-8100-device) o [su dispositivo StorSimple 8600](storsimple-8600-hardware-installation.md#cable-your-storsimple-8600-device).

## <a name="next-steps"></a>Pasos siguientes

Más información sobre [reemplazo de componentes de hardware de StorSimple](storsimple-hardware-component-replacement.md).

