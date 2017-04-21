<properties 
   pageTitle="Reemplazo de componentes de hardware StorSimple | Microsoft Azure"
   description="Describe cómo reemplazar de forma segura la PCMs, batería, módulos de controlador, controladores EBOD, unidades de disco y chasis de un dispositivo StorSimple."
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
   ms.date="10/11/2016"
   ms.author="alkohli" />

# <a name="storsimple-hardware-component-replacement"></a>Reemplazo de componentes de hardware de StorSimple

## <a name="overview"></a>Información general

Los tutoriales de reemplazo de componentes de hardware, describen los componentes de hardware de su dispositivo de la serie de Microsoft Azure StorSimple 8000 y los pasos necesarios para quitar y reemplazarlas. En este artículo se describe los iconos de seguridad, proporciona punteros a los tutoriales detallados y enumera los componentes que puede reemplazar el.

>[AZURE.IMPORTANT] Antes de intentar quitar o reemplazar cualquier componente StorSimple, asegúrese de que revise las [convenciones de iconos de seguridad](#safety-icon-conventions) y otras [precauciones](storsimple-safety.md).
 
### <a name="safety-icon-conventions"></a>Convenciones de iconos de seguridad

La siguiente tabla describe los iconos de seguridad utilizados en estos tutoriales. Preste atención a estos iconos de seguridad al avanzar por los pasos para quitar y reemplazar componentes de dispositivos.

| Icono | Texto | Información adicional |
|:---- |:---- |:-----------|
|![Icono de advertencia](./media/storsimple-hardware-component-replacement/Warning.png)| **¡PELIGRO!** | Indica una situación de peligro que, si no se evita, da de muerte o graves daños. Esta palabra señal se limita a las situaciones más extremas.|
|![Icono de advertencia](./media/storsimple-hardware-component-replacement/Warning.png)| **¡ADVERTENCIA!** | Indica una situación de peligro que, si no se evita, podría provocar graves daños o de muerte.|
|![Icono de precaución](./media/storsimple-hardware-component-replacement/Caution.png)| **¡PRECAUCIÓN!** |Indica una situación de peligro que, si no se evita, podría provocar daños secundarias o moderado.|
|![Icono de aviso](./media/storsimple-hardware-component-replacement/NoticeIcon.png)| **AVISO:** | Indica la información que considera importante, pero no relacionados con el riesgo.|
|![Icono de descarga eléctrica](./media/storsimple-hardware-component-replacement/Electric.png) | **Riesgo de descarga eléctrica** | Indica el alto voltaje.|
|![Icono de gran volumen](./media/storsimple-hardware-component-replacement/Weight.png)| **Peso pesado**| |
|![No hay icono de piezas útiles para el usuario](./media/storsimple-hardware-component-replacement/NoUserServiceableParts.png)| **No contiene piezas útiles para usuario** | No tienen acceso a menos que la formación adecuada.|
|![Icono de instrucciones de lectura](./media/storsimple-hardware-component-replacement/ReadInstructions.png)|**Lea las instrucciones en primer lugar**| |
|![Icono de riesgo de sugerencia](./media/storsimple-hardware-component-replacement/TipHazard.png)|**Sugerencia el riesgo**| |

### <a name="before-you-begin"></a>Antes de empezar

Familiarizarse con la información de seguridad acerca de los iconos del dispositivo y seguridad usados en este tutorial. Para obtener más información, vaya a [instalar y trabajar con su dispositivo StorSimple de forma segura](storsimple-safety.md) . Asegúrese de revisar las [precauciones](storsimple-safety.md#handling-precautions) antes de tratar el dispositivo StorSimple. 

Antes de intentar reemplazar un componente, tenga en cuenta la siguiente información.

![Icono de advertencia](./media/storsimple-hardware-component-replacement/Warning.png) ![icono descargas eléctricas](./media/storsimple-hardware-component-replacement/Electric.png) **Advertencia!** 

- Protéjase correctamente mediante un electrostáticas o paspartú antiestática al tratamiento módulos y componentes del dispositivo StorSimple.

- Toque cualquier circuito. Use las guías y los controladores suministrados durante la gestión de componentes que pueden ha expuesto circuitos.

![Icono de advertencia](./media/storsimple-hardware-component-replacement/Warning.png) ![Observe icono](./media/storsimple-hardware-component-replacement/NoticeIcon.png) **aviso:**

Cuando se reemplaza un módulo, **Nunca dejar un bay vacío en la parte posterior de los caracteres enmarcados**. Obtener un reemplazo o módulo en blanco antes de quitar el elemento de problema.

## <a name="hardware-component-replacement-procedures"></a>Procedimientos de reemplazo de componentes de hardware

El dispositivo de la serie 8000 StorSimple consta de varios módulos de complementos en el primario o los documentos adjuntos EBOD. 8100 tiene un único alojamiento principal, mientras que la 8600 es un dispositivo de caracteres enmarcados dual con un alojamiento principal y enmarcado EBOD.

En las tablas siguientes se resumen los componentes de hardware principal en el dispositivo. Haga clic en el vínculo de la columna de **procedimiento de reemplazo** para ir al tutorial asociado.

|Componentes|# Presentar|¿Módulo del complemento?|Procedimiento de reemplazo
|:---------|:--------|:--------------|:---------------------|
| Chasis|1|No|[Reemplazar el chasis en el dispositivo StorSimple](storsimple-chassis-replacement.md) |
|Controladores principales|2|Sí| [Reemplazar un módulo de controlador en el dispositivo StorSimple](storsimple-controller-replacement.md) |
|764W Power y refrigeración módulos (PCMs)|2|Sí| [Reemplazar una potencia y refrigeración módulo en el dispositivo StorSimple](storsimple-power-cooling-module-replacement.md) |
|Batería de reserva|2|Sí| [Reemplazar el módulo de batería de copia de seguridad en el dispositivo StorSimple](storsimple-battery-replacement.md) |
|Unidades de disco|12|Sí| [Reemplazar una unidad de disco en el dispositivo StorSimple](storsimple-disk-drive-replacement.md) |

**Tabla 1** Componentes de hardware en la caja principal

Los caracteres enmarcados principal y los caracteres enmarcados EBOD difieren en sus módulos de i/OS. Además, la PCMs tienen potencia diferente. El PCMs en la caja principal son 764 W, mientras que los de los caracteres enmarcados EBOD son 580 w el. El PCMs en la caja principal también contienen un módulo de batería de copia de seguridad.

|Componentes|# Presentar|¿Módulo del complemento?| Procedimiento de reemplazo
|:---------|:--------|:--------------|:---------------------|
|Chasis|1|No| [Reemplazar el chasis en el dispositivo StorSimple](storsimple-chassis-replacement.md) |
|Controladores de EBOD|2|Sí| [Reemplazar un controlador EBOD en el dispositivo StorSimple](storsimple-ebod-controller-replacement.md) |
|580 w de energía y refrigeración módulos (PCMs)|2|Sí| [Reemplazar una potencia y refrigeración módulo en el dispositivo StorSimple](storsimple-power-cooling-module-replacement.md) |
|Unidades de disco|12|Sí| [Reemplazar una unidad de disco en el dispositivo StorSimple](storsimple-disk-drive-replacement.md) |

**Tabla 2** Componentes de hardware en la caja EBOD

Los módulos de complementos en el dispositivo se resaltan en el siguiente delantera y traseros diagramas. Puede usar estos diagramas para determinar la ubicación de los distintos módulos de complementos si es necesario un reemplazo. El diagrama frontal muestra las unidades de disco y los diagramas traseros del EBOD local y la presentación de caracteres enmarcados principal los módulos de complementos.

![Frontplane del dispositivo con unidades de disco](./media/storsimple-hardware-component-replacement/IC741028.png)

**Figura 1** Parte frontal del dispositivo

|Etiqueta|Descripción|
|:----|:----------|
|0 - 11|Unidades de disco (total de 12)|

Los caracteres enmarcados principal y los caracteres enmarcados EBOD tienen módulos de unidad carrier. El chasis aloja doce 3,5" unidades de disco organizadas en un formato de 3 por 4.

![Plano de módulos del dispositivo caracteres enmarcados principal](./media/storsimple-hardware-component-replacement/IC740994.png)

**Figura 2** Reverso de la caracteres enmarcados principal

|Etiqueta|Descripción|
|:----|:----------|
|1|PCM 0|
|2|PCM 1|
|3|Controlador de 0|
|4|Controlador de 1|

![Plano de módulos de complemento de dispositivo EBOD caracteres enmarcados](./media/storsimple-hardware-component-replacement/IC769599.png)

**Figura 3** Reverso de la inclusión EBOD

|Etiqueta|Descripción|
|:----|:----------|
|1|PCM 0|
|2|PCM 1|
|3|Controlador de EBOD 0|
|4|Controlador de EBOD 1|

## <a name="field-replaceable-units"></a>Unidades que puede reemplazar el campo

Las unidades que puede reemplazar el campo siguiente (FRU) están disponibles para su dispositivo StorSimple:

- Chasis (incluido el panel de operaciones integrada)

- 764 PCM DE AIRE ACONDICIONADO W

- PCM DE AIRE ACONDICIONADO DE 580 W

- Unidad de disco duro con módulo de unidad de transportista

- Módulo de controlador

- Módulo de controlador EBOD

- Módulo de batería de reserva

- Rail kit de montaje

Póngase [en contacto con soporte técnico de Microsoft](storsimple-contact-microsoft-support.md) para pedir cualquiera de estas unidades de reemplazo.

## <a name="next-steps"></a>Pasos siguientes

Revisar toda la [información de seguridad](storsimple-safety.md) antes de intentar reemplazar un componente de hardware StorSimple.
