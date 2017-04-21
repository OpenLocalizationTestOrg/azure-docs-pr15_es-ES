<properties
   pageTitle="Instalar el dispositivo 8600 StorSimple | Microsoft Azure"
   description="Describe cómo descomprimir, montaje de bastidores y cable de su dispositivo StorSimple 8600 antes de implementar y configurar el software."
   services="storsimple"
   documentationCenter="NA"
   authors="alkohli"
   manager="carmonm"
   editor="" />
<tags
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="10/24/2016"
   ms.author="alkohli" />

# <a name="unpack-rack-mount-and-cable-your-storsimple-8600-device"></a>Descomprimir, montaje en bastidor y su dispositivo StorSimple 8600 de cable

## <a name="overview"></a>Información general
Su 8600 de Microsoft Azure StorSimple es un dispositivo de caracteres enmarcados dual y consta de un principal y enmarcado EBOD. En este tutorial se explica cómo descomprimir, montaje en bastidor y hardware de dispositivo de cable 8600 de StorSimple antes de configuran el software de StorSimple.

## <a name="unpack-your-storsimple-8600-device"></a>Descomprimir el dispositivo StorSimple 8600

Los pasos siguientes proporcionan instrucciones claras y detalladas sobre cómo descomprimir el dispositivo de almacenamiento de StorSimple 8600. Este dispositivo se suministra en dos cuadros, uno para el alojamiento principal y otro para el alojamiento EBOD. A continuación, se colocan estos dos cuadros en un solo cuadro.

### <a name="prepare-to-unpack-your-device"></a>Prepararse para descomprimir el dispositivo

Antes de descomprimir el dispositivo, revise la información siguiente.


![Icono de advertencia](./media/storsimple-safety/IC740879.png)![icono de la exhaustiva](./media/storsimple-8600-hardware-installation/HCS_HeavyWeight_Icon.png) **Advertencia!**

1. Asegúrese de que tiene dos personas disponibles para administrar el grosor del dispositivo si está controlando manualmente. Un alojamiento completamente configurado puede valorar hasta 32 kg (70 libras).
1. Coloque el cuadro en una superficie plana y nivel.

A continuación, complete los pasos siguientes para descomprimir el dispositivo.

#### <a name="to-unpack-your-device"></a>Para descomprimir el dispositivo

1. Inspeccionar el cuadro y la espuma embalaje para crushes, cortes, daños de agua o cualquier otro daño obvio. Si el cuadro o embalaje gravemente está dañado, no se abren el cuadro. Póngase [en contacto con soporte técnico de Microsoft](storsimple-contact-microsoft-support.md) para ayudar a determinar si el dispositivo está en buen estado.

2. Abrir el cuadro externo y tome las dos casillas correspondientes a principal y documentos adjuntos EBOD. Ahora puede descomprimir los principales y los documentos adjuntos EBOD. La figura siguiente muestra la vista sin empaquetar de uno de los documentos adjuntos.

    ![Descomprimir el dispositivo de almacenamiento](./media/storsimple-8600-hardware-installation/HCSUnpackyour4Udevice.png)

    **Vista sin empaquetar de su dispositivo de almacenamiento**

     Etiqueta | Descripción
     ----- | -------------
     1     | Cuadro de embalaje
     2     | Cables de SA (en la Bandeja de cables y accesorios)
     3     | Espuma de la parte inferior
     4     | Dispositivo
     5     | Espuma superior
     6     | Cuadro accesorio

3. Tras desempaquetar los dos cuadros, asegúrese de que tiene:

  - 1 caracteres enmarcados principal (los caracteres enmarcados principal y caracteres enmarcados EBOD están en dos cuadros independientes)
  - Caracteres enmarcados EBOD 1
  - 4 cables de alimentación, 2 en cada cuadro
  - 2 cables SA (para conectar los caracteres enmarcados principal con caracteres enmarcados EBOD)
  - cable Ethernet cruzado de 1
  - 2 cables de consola serie
  - convertidor de USB a serie 1 serie
  - QSFP 4-al-SFP + adaptadores para su uso con interfaces de red GbE 10
  - Kit de montaje (4 guías de lado con montaje, 2 para los caracteres enmarcados principal y caracteres enmarcados EBOD), de bastidores 2 1 en cada cuadro
  - Obtener la documentación de introducción

    Si no se ha recibido cualquiera de los elementos enumerados anteriormente, [póngase en contacto con soporte técnico de Microsoft](storsimple-contact-microsoft-support.md).  

El siguiente paso es el dispositivo de montaje en bastidor.

## <a name="rack-mount-your-storsimple-8600-device"></a>El dispositivo StorSimple 8600 de montaje en bastidor

Siga los pasos siguientes para instalar el dispositivo de almacenamiento de StorSimple 8600 en un bastidor de 19 pulgadas estándar con parte delantera y traseras publicaciones. Este dispositivo se suministra con dos inclusiones: un alojamiento principal y enmarcado EBOD. Ambas necesitan bastidor-instalarse.

La instalación consta de varios pasos, cada uno de los cuales se explica en los siguientes procedimientos.

> [AZURE.IMPORTANT]
Dispositivos de StorSimple deben ser montaje de bastidor para que funcione correctamente.



### <a name="site-preparation"></a>Preparación del sitio

Los documentos adjuntos deben estar instalados en un bastidor de 19 pulgadas estándar cuya parte delantera y traseras publicaciones. Use el procedimiento siguiente para preparar la instalación de bastidor.

#### <a name="to-prepare-the-site-for-rack-installation"></a>Para preparar el sitio para la instalación de bastidor

1. Asegúrese de que los principales y los documentos adjuntos EBOD son definitiva de almacenamiento seguro en una superficie de trabajo fijo, estable y nivel (o similar).

2. Compruebe que el sitio donde desea configurar CA estándar desde una fuente independiente o un unidad de distribución de alimentación (PDU) bastidor con una fuente de alimentación (puede).

3. Asegúrese de que dicha franja de uno 4U (2 X 2U) está disponible en el bastidor en el que desea montaje los documentos adjuntos.

![Icono de advertencia](./media/storsimple-safety/IC740879.png)![icono de la exhaustiva](./media/storsimple-8600-hardware-installation/HCS_HeavyWeight_Icon.png) **Advertencia!**

 Asegúrese de que tiene dos personas disponibles para administrar el peso si controla la configuración de dispositivo manualmente. Un alojamiento completamente configurado puede valorar hasta 32 kg (70 libras).

### <a name="rack-prerequisites"></a>Requisitos previos de bastidor

Los documentos adjuntos están diseñados para la instalación en un bastidor de 19 pulgadas estándar archivador con:

- Profundidad mínima de 27.84 pulgadas de bastidor post para publicación
- Peso máximo de 32 kg para el dispositivo
- Presión máxima de 5 Pascal (medidor de agua de 0,5 mm)

### <a name="rack-mounting-rail-kit"></a>Kit de guías de montaje en bastidor

Se proporcionará un conjunto de guías de montaje para su uso con el contenedor de bastidor de 19 pulgadas. Para controlar el grosor máximo de caracteres enmarcados ha probado las guías. Estas guías también le permitirá la instalación de varios documentos adjuntos sin pérdida de espacio en el bastidor. Instale el alojamiento EBOD en primer lugar.

#### <a name="to-install-the-ebod-enclosure-on-the-rails"></a>Para instalar el alojamiento EBOD en las guías

2. Realizar este paso solo si guías internas no están instaladas en su dispositivo. Normalmente, las guías internas se instalan en fábrica. Si no se instalan guías, a continuación, instalar las diapositivas rail izquierda y derecha rail a los lados del bastidor caracteres enmarcados. Asocian con seis tornillos métricas en cada lado. Para ayudarle con la orientación, se marcan las diapositivas rail **LH – frente** y **hr: frontal**y final adherida hacia atrás de los caracteres enmarcados tiene un extremo cónico.

    ![Adjuntar diapositivas rail a chasis caracteres enmarcados](./media/storsimple-8600-hardware-installation/HCSAttachingRailSlidestoEnclosureChassis.png)

    **Asociar diapositivas rail a los lados de los caracteres enmarcados**

    Etiqueta | Descripción
    ----- | -----------
    1     | M 3 x 4 tornillos de cabeza de botón
    2     | Diapositivas de chasis

3. Adjuntar los conjuntos de columna derecha e izquierda rail a los miembros vertical armario de bastidor. Se marcan los corchetes **LH** **hr**y **este lado hacia arriba** para guiarle durante la orientación correcta.

4. Busque las clavijas rail en la parte delantera y trasera del ensamblado rail. Ampliar la rail para caber entre las publicaciones de bastidor e insertar las clavijas en la parte delantera y trasera bastidor post taladros de miembro vertical. Asegúrese de que el ensamblado rail es nivel.

5. Proteger al ensamblado rail al bastidor miembros verticales con dos de los tornillos métricos proporcionados. Use un paso en la parte frontal y otro en la parte posterior.

6. Repita estos pasos para el otro conjunto de rail.

     ![Adjuntar diapositivas rail a armario de bastidor](./media/storsimple-8600-hardware-installation/HCSAttachingRailSlidestoRackCabinet.png)

    **Asociar ensamblados rail al bastidor**

     Etiqueta | Descripción
     ----- | -----------
     1     | Junta roscada de cierre
     2     | Junta roscada de publicación de bastidor front de taladro de cuadrado
     3     | PIN de ubicación rail frente a la izquierda
     4     | Junta roscada de cierre
     5     | PIN de ubicación rail trasera izquierda

### <a name="mounting-the-ebod-enclosure-in-the-rack"></a>Montaje el alojamiento EBOD en bastidor

Usar guías de bastidor que acaba de instalar, siga estos pasos para la inclusión EBOD en bastidor de montaje.

#### <a name="to-mount-the-ebod-enclosure"></a>Montaje de los caracteres enmarcados EBOD

1. Con un asistente, levantar el alojamiento y alinear con guías de bastidor.

2. Inserte con cuidado el alojamiento en las guías y, a continuación, enviarlo por completo en el bastidor archivador.

    ![Insertar un dispositivo en el bastidor](./media/storsimple-8600-hardware-installation/HCSInsertingDeviceintheRack.png)

    **Montaje el alojamiento en bastidor**

3. Quitar los extremos de la pestaña de la izquierda y derecha frontal mediante la extracción de los extremos gratuitos. Los extremos de la pestaña simplemente acoplar en las pestañas.

4. Proteger el alojamiento en bastidor instalando una junta roscada de estrella cabeza proporcionado a través de cada pestaña, izquierdo y derecho.

4. Instale los extremos de la pestaña presionando ellos en posición y el ajuste de ellos en su lugar.

     ![Instalar mayúsculas de pestaña](./media/storsimple-8600-hardware-installation/HCSInstallingFlangeCaps.png)

    **Instalar los extremos de pestaña**

     Etiqueta | Descripción
     ----- | -----------
     1     | Junta roscada de fijación de caracteres enmarcados


### <a name="mounting-the-primary-enclosure-in-the-rack"></a>Montaje de los caracteres enmarcados principal en el bastidor

Cuando haya terminado de montaje el alojamiento EBOD, debe montaje los caracteres enmarcados principal siguiendo los mismos pasos.

> [AZURE.NOTE]
>
> - Es posible tener algunos casillas vacías en el bastidor entre los caracteres enmarcados principal y los caracteres enmarcados EBOD.
> - Usar el cable de SA proporcionado 2m para conectar los caracteres enmarcados principal al entorno EBOD.
> - No hay ninguna restricción en la posición relativa de la unidad a la unidad EBOD. Por lo tanto, puede colocar el alojamiento principal en la franja superior y el alojamiento EBOD a continuación, o viceversa.

El siguiente paso es el dispositivo para power, la red y el acceso de serie de cable.

## <a name="cable-your-storsimple-8600-device"></a>Cable de su dispositivo StorSimple 8600

Los siguientes procedimientos explican cómo el dispositivo de StorSimple 8600 para power, la red y las conexiones serie de cable.

### <a name="prerequisites"></a>Requisitos previos

Antes de comenzar el dispositivo de cable, necesitará:

- Los caracteres enmarcados principal y el alojamiento EBOD, completamente desempaquetó
- 4 cables de alimentación (2 cada para principal y los caracteres enmarcados EBOD) que venía con el dispositivo
- 2 cables de SA suministrados con el dispositivo para conectar los caracteres enmarcados EBOD al entorno principal
- Acceso a 2 unidades de distribución de alimentación (PDU) (recomendado)
- Cables de red
- Proporciona los cables de serie
- Convertidor de USB de serie con el controlador apropiado instalado en su equipo (si es necesario)
- Proporciona QSFP 4-al-SFP + adaptadores para su uso con interfaces de red GbE 10
- [Hardware compatible para las interfaces de red GbE 10 en el dispositivo StorSimple](storsimple-supported-hardware-for-10-gbe-network-interfaces.md)

### <a name="sas-and-power-cabling"></a>Asociaciones de seguridad y cableado de energía

El dispositivo tiene un alojamiento principal y enmarcado EBOD. Para ello, las unidades de conectar para power y conectividad SCSI conectado en serie (SA).

Al configurar este dispositivo por primera vez, realice los pasos para SA cableado en primer lugar y, a continuación, complete los pasos de power cableado.

[AZURE.INCLUDE [storsimple-cable-8600-for-SAS](../../includes/storsimple-sas-cable-8600.md)]

[AZURE.INCLUDE [storsimple-cable-8600-for-power](../../includes/storsimple-cable-8600-for-power.md)]

### <a name="network-cabling"></a>Cables de red

El dispositivo está en una configuración de reserva activa: en cualquier momento, un módulo de controlador está activo y procesamiento de todas las operaciones de red y del disco mientras el módulo del controlador está en suspensión. Si se produce un error del controlador, el controlador de reserva se activa inmediatamente y continúa todas las operaciones de red y disco.

Para admitir este controlador redundantes de migración tras error, debe cable su red de dispositivo, como se muestra en los siguientes pasos.

#### <a name="to-cable-for-network-connection"></a>Cable de conexión de red

1. El dispositivo tiene seis interfaces de red en cada controlador: cuatro 1 GB/s y dos 10 GB/s Ethernet puertos. Consulte la siguiente ilustración para identificar los puertos de datos en el plano posterior del dispositivo.

     ![Plano del dispositivo 8600](./media/storsimple-8600-hardware-installation/HCSBackplaneof2UDevicewithPortsLabeled.jpg)

    **Volver del dispositivo que muestra los puertos de datos**

     Etiqueta   | Descripción
     ------- | -----------
     0,1,4,5 |  Interfaces de red GbE 1
     2 3     | Interfaces de red GbE 10
     6       | Puertos serie



1. Vea el siguiente diagrama de cableado de red. (Se muestra la configuración de red mínima por líneas azules sólidas. Para alta disponibilidad y rendimiento, configuración adicional necesaria se muestra con líneas de puntos).

![Cable de su dispositivo 4U de red](./media/storsimple-8600-hardware-installation/HCSCableYour4UDeviceforNetwork.png)

**Cableado de su dispositivo**

Etiqueta | Descripción
----- | -----------
RESPUESTAS    | LAN con acceso a Internet
B    | Controlador de 0
C    | PCM 0
D.    | Controlador de 1
E    | PCM 1
F    | Controlador de EBOD 0
G    | Controlador de EBOD 1
H, PUEDO  | Hosts (por ejemplo, servidores de archivo)
0-5  | Interfaces de red
6    | Caracteres enmarcados principal
7    | Caracteres enmarcados EBOD

Cuando el dispositivo de cableado, se requiere la configuración mínima:


- Al menos dos interfaces de red conectadas en cada controlador con uno para el acceso a la nube y otro para iSCSI. 0 puerto automáticamente está habilitado y configurado a través de la consola del dispositivo de serie de datos. Aparte de datos 0, otro puerto de datos también debe configurarse a través del portal clásico Azure. En este caso, conectar datos 0 puerto para la LAN principal (red con acceso a Internet). Los demás puertos de datos se pueden conectar al segmento de SAN/iSCSI LAN (VLAN) de la red, dependiendo de la función.

- Interfaces idénticos en cada controlador conectado a la misma red para garantizar la disponibilidad si se produce un error del controlador. Por ejemplo, si elige conectar datos 0 y 3 de datos de uno de los controladores, debe conectar los datos correspondientes 0 y datos 3 en el otro controlador.

Tenga en cuenta para alta disponibilidad y rendimiento:


- Cuando sea posible, configure un par de interfaz de red para el acceso a la nube (1 GbE) y otro par para iSCSI (se recomienda 10 GbE) en cada controlador.

- Cuando sea posible, conecte interfaces de red de cada controlador a dos modificadores diferentes para garantizar la disponibilidad de un error de cambiar. La ilustración muestra dos 10 GbE interfaces de red, datos 2 y 3 de datos de cada controlador conectado a dos modificadores diferentes. Para obtener más información, consulte las **interfaces de red** en los [requisitos de alta disponibilidad para su dispositivo StorSimple](storsimple-system-requirements.md#high-availability-requirements-for-storsimple).

>[AZURE.NOTE] Si usa transceptores SFP + con las interfaces de red GbE 10, use la QSFP proporcionado-SFP + adaptadores. Para obtener más información, vaya a [hardware compatibles para las interfaces de red GbE 10 en el dispositivo StorSimple](storsimple-supported-hardware-for-10-gbe-network-interfaces.md).

### <a name="serial-port-cabling"></a>Puerto serie cableado

Realice los pasos siguientes para el puerto serie de cable.

#### <a name="to-cable-for-serial-connection"></a>Cable de conexión serie

1. El dispositivo tiene un puerto serie en cada controlador que se identifica por un icono de llave inglesa. Para localizar los puertos serie, consulte la ilustración que muestra los datos de puertos en el reverso de su dispositivo.

2. Identifique el controlador activo en el plano del dispositivo. Un LED azul intermitente indica que el controlador está activo.

3. Use el cable serie suministrado (si es necesario, el convertidor de serie USB de su equipo portátil) y conectar su equipo (con emulación de terminal al dispositivo) o consola al puerto serie del controlador activo.

4. Instalar a los controladores de serie USB (incluidos con el dispositivo) en su equipo.

5. Configurar la conexión serie como sigue:
   - 115.200 baudios
   - 8 bits de datos
   - 1 bit de parada
   - Sin paridad
   - Establecer el control de flujo en **ninguna**

6. Comprobar que la conexión está funcionando, presione ENTRAR en la consola. Debería aparecer un menú de consola serie.

> [AZURE.NOTE] **Administración invisible:** Cuando el dispositivo esté instalado en un centro de datos remota o en un salón del equipo con acceso limitado, asegúrese de que las conexiones serie a dos controladores siempre están conectadas a un conmutador de consola serie o un equipo similar. Esto permite control remoto de fuera de banda y las operaciones de soporte en caso de interrupción de la red o errores inesperados.

Se ha completado el dispositivo para power, acceso a la red y conexión serie de cableado. El siguiente paso es configurar el software en el dispositivo.

## <a name="next-steps"></a>Pasos siguientes

Ya está listo para [implementar y configurar el dispositivo de StorSimple local](storsimple-deployment-walkthrough-u2.md).
