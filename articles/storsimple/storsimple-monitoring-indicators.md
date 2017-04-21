<properties 
    pageTitle="Supervisar los indicadores de StorSimple | Microsoft Azure" 
    description="Describe los diodos (LED) emisor de luz y audibles alarmas para controlar el estado del dispositivo StorSimple."
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
    ms.date="08/18/2016"
    ms.author="alkohli" />

# <a name="use-storsimple-monitoring-indicators-to-manage-your-device"></a>Usar StorSimple supervisar los indicadores para administrar el dispositivo   

## <a name="overview"></a>Información general

El dispositivo StorSimple incluye diodos (LED) emisor de luz y alarmas que puede usar para supervisar los módulos y el estado general del dispositivo StorSimple. Los indicadores de seguimiento que encontrará en los componentes de hardware de caracteres enmarcados principal del dispositivo y el alojamiento EBOD. Los indicadores de seguimiento pueden ser LED o alarmas sonoras.

Existen tres Estados LED que se utiliza para indicar el estado de un módulo: verde, intermitente verde a rojo ámbar o ámbar rojo.  

- LED verde representan un estado de funcionamiento correcto.  
- Intermitente verde para representar de LED ámbar rojo la presencia de condiciones no críticas que requiera intervención del usuario.  
- LED ámbar rojo indica que hay un error crítico presente en el módulo.  

El resto de este artículo describe los diversos LED indicador supervisión, su ubicación en el dispositivo StorSimple, el estado del dispositivo basado en los Estados de LED y cualquier alarmas sonoras asociadas.

## <a name="front-panel-indicator-leds"></a>Indicador de panel frontal LED

El panel frontal, también conocido como el *panel de acciones* o el *panel de operaciones*, muestra el estado de todos los módulos agregado del sistema. El panel frontal es idéntico en el StorSimple principal y los caracteres enmarcados EBOD y se muestra a continuación.  

   ![Panel frontal de dispositivo][1]
 
El panel frontal contiene los siguientes indicadores:  

1. Botón de silencio
2. Indicador de alimentación LED (rojo/verde-ámbar)
3. Indicador de error de módulo LED (en rojo y ámbar/OFF)
4. Indicador de error lógico LED (en rojo y ámbar/OFF
5. Pantalla de Id.  

La principal diferencia entre el panel frontal LED del dispositivo y las de los caracteres enmarcados EBOD es el **Número de identificación de unidad de sistema** se muestra en la pantalla LED. El identificador de unidad predeterminado mostrado en el dispositivo es **00**, mientras que el ID de unidad predeterminado mostrado en el alojamiento EBOD es **01**. Esto le permite distinguir rápidamente entre el dispositivo y el alojamiento EBOD cuando el dispositivo está activado. Si el dispositivo está desactivado, utilice la información de [activar un nuevo dispositivo](storsimple-turn-device-on-or-off.md#turn-on-a-new-device) para diferenciar el dispositivo de los caracteres enmarcados EBOD.  

## <a name="front-panel-led-status"></a>Estado de LED del panel frontal  

Utilice la siguiente tabla para identificar el estado indicado por el LED en el panel frontal del dispositivo o el alojamiento EBOD.  

|Alimentación del sistema | Errores de módulo | Error de lógica | Alarma | Estado|
|-------------|---------------|-----------------|-------|-------|
|Ámbar rojo | DESACTIVAR     | DESACTIVAR | N/A. | CA perdido, trabajan en power de copia de seguridad o CA en y el controlador se quitaron los módulos.|
|Verde | ACTIVADO | ACTIVADO | N/A. | Estado de prueba de alimentación del panel de operaciones en (5s)|
|Verde | DESACTIVAR | DESACTIVAR | N/A. | Power en todas las funciones de buenas|
|Verde | ACTIVADO |N/A. | Error de PCM LED, indicadores LED de error de ventilador | Cualquier error de PCM, errores de ventilador, sobre o debajo de temperatura|
| Verde | ACTIVADO | N/A. | Módulo de i/OS LED  | Los errores del módulo de controlador|
| Verde | ACTIVADO | N/A. | N/A. | Errores de lógica de caracteres enmarcados|
| Verde | Flash | N/A. | Estado del módulo LED en el módulo de controlador. Error de PCM LED, indicadores LED de error de ventilador | Tipo de módulo de controlador desconocido instalado, error de bus I2C, error de configuración de controlador módulo productos esenciales datos (VPD) |

## <a name="power-cooling-module-pcm-indicator-leds"></a>Alimentación refrigeración indicadores LED de módulo (PCM)   

Indicador de módulo (PCM) refrigeración LED Power puede encontrarse en el reverso de los caracteres enmarcados principal o caracteres enmarcados EBOD en cada módulo PCM. En este tema se explica cómo usar los siguientes indicadores para supervisar el estado de su dispositivo StorSimple.  

- PCM LED para el alojamiento principal
- PCM LED para el alojamiento EBOD

## <a name="pcm-leds-for-the-primary-enclosure"></a>PCM LED para el alojamiento principal  

El dispositivo de StorSimple tiene un módulo PCM 764W con una batería adicional. La ilustración siguiente muestra el panel LED del dispositivo.  

   ![LED PCM en los caracteres enmarcados principal][2]

Leyenda de LED:

1. Error de alimentación AC
2. Error de ventilador
3. Error de la batería
4. PCM ACEPTAR
5. Error de DC
6. Buena batería  

El estado de la PCM se indica en el panel LED. El panel de PCM LED del dispositivo tiene seis LED. Cuatro de estos indicadores muestran el estado de la fuente de alimentación y el ventilador. Los dos LED restantes indican el estado del módulo de batería de copia de seguridad en el PCM. Puede usar las tablas siguientes para determinar el estado de la PCM.  

### <a name="pcm-indicator-leds-for-power-supply-and-fan"></a>Indicador PCM LED de alimentación y ventilador
| Estado | PCM Aceptar (verde) | Error de aire Acondicionado (ámbar) | Error de ventilador (ámbar) | Error de DC (ámbar) |
|--------|----------------|-----------------------|------------------|----------------------|
| Sin alimentación de CA (para caracteres enmarcados) | DESACTIVAR | DESACTIVAR | DESACTIVAR | DESACTIVAR|
| Sin alimentación de CA (solo esta PCM) | DESACTIVAR | ACTIVADO | DESACTIVAR | ACTIVADO |
| Aire Acondicionado presentar PCM ON - Aceptar     | ACTIVADO | DESACTIVAR | DESACTIVAR | DESACTIVAR |
| Error PCM (ventilador éxito) | DESACTIVAR | DESACTIVAR | ACTIVADO | N/A. |
| Errores PCM (a través de amperios y sobre voltaje sobre actual) | DESACTIVAR | ACTIVADO | ACTIVADO | ACTIVADO |
| PCM (ventilador tolerancia) | ACTIVADO | DESACTIVAR | DESACTIVAR | ACTIVADO |
| Modo de suspensión | Intermitente | DESACTIVAR | DESACTIVAR | DESACTIVAR |
| Descarga de firmware PCM | DESACTIVAR | Intermitente | Intermitente | Intermitente |

### <a name="pcm-indicator-leds-for-the-backup-battery"></a>Indicador PCM LED de la batería de reserva  

| Estado | Buena batería (verde) | Error de batería (ámbar) |
|--------|----------------------|-----------------------|
| Batería no están presente | DESACTIVAR | DESACTIVAR |
| Batería presentar y cargada | ACTIVADO | DESACTIVAR |
| Descarga de mantenimiento o carga de la batería | Intermitente | DESACTIVAR |
| Error de "flexible" batería (recuperar) | DESACTIVAR | Intermitente |
| Error de "duro" batería (no recuperable) | DESACTIVAR | ACTIVADO |
| Batería desactivarse | Intermitente | DESACTIVAR |

## <a name="pcm-leds-for-the-ebod-enclosure"></a>PCM LED para el alojamiento EBOD  

Los caracteres enmarcados EBOD tienen un PCM 580 w y sin batería adicional. El panel PCM para el alojamiento EBOD tiene indicador LED únicamente para las fuentes de alimentación y el ventilador. La ilustración siguiente muestra estos indicadores.

   ![LED PCM en los caracteres enmarcados EBOD][3] 
 
Puede usar la siguiente tabla para determinar el estado de la PCM.  

| Estado | PCM Aceptar (verde) | Error de aire Acondicionado (ámbar) | Error de ventilador (ámbar) | Error de DC (ámbar) |
|--------|---------------|------------------------|------------------|----------------------|
| Sin alimentación de CA (para caracteres enmarcados) | DESACTIVAR | DESACTIVAR | DESACTIVAR | DESACTIVAR |
| Sin alimentación de CA (solo esta PCM) | DESACTIVAR | ACTIVADO | DESACTIVAR | ACTIVADO |
| Aire Acondicionado presentar PCM ON-Aceptar | ACTIVADO | DESACTIVAR | DESACTIVAR | DESACTIVAR |
| Error PCM (ventilador éxito) | DESACTIVAR | DESACTIVAR | ACTIVADO | X |
| Errores PCM (a través de amperios y sobre voltaje sobre actual | DESACTIVAR | ACTIVADO | ACTIVADO | ACTIVADO |
| PCM (ventilador tolerancia) | ACTIVADO | DESACTIVAR | DESACTIVAR | ACTIVADO |
| Modelo de reserva | Intermitente | DESACTIVAR | DESACTIVAR | DESACTIVAR |
| Descarga de firmware PCM | DESACTIVAR | Intermitente | Intermitente | Intermitente |

## <a name="controller-module-indicator-leds"></a>Indicadores LED del módulo de controlador  

El dispositivo StorSimple contiene indicadores para el controlador principal y los módulos del controlador EBOD.   

### <a name="monitoring-leds-for-the-primary-controller"></a>Supervisión LED al controlador principal
La siguiente ilustración le ayuda a identificar los LED del controlador principal. (Todos los componentes se muestran para ayudar a orientación).  

   ![Supervisión LED - controlador principal][4]
 
Utilice la siguiente tabla para determinar si el módulo de controlador funciona correctamente.  

### <a name="controller-indicator-leds"></a>Indicador LED del controlador  

| LED | Descripción                                                                            
|---- | ----------- |
| Id. de LED (azul) | Indica que se que se identifica el módulo. Si el LED azul está intermitente en un controlador de ejecución, el controlador es el activo y el otro es el controlador de espera. Para obtener más información, consulte [identificar el controlador activo en el dispositivo](storsimple-controller-replacement.md#identify-the-active-controller-on-your-device). |
| Error de LED (ámbar) | Indica un error en el controlador.        
| Aceptar LED (verde) | Verde estable indica que el controlador es correcto. Intermitente verde indica un error de configuración de VPD controlador. |
| Indicadores (verde) de la actividad de SA | Verde estable indica una conexión con ninguna actividad actual. Intermitente verde indica que la conexión tiene actividad en curso. |
| LED de estado de Ethernet | Lado derecho indica la actividad de la red o el vínculo: (verde estable) vínculo activo, (intermitente verde) de actividad de red. Lado izquierdo indica la velocidad de red: (amarillo) 1000 Mb/s, 100 Mb/s (verde) y (OFF) 10 Mb/s. Dependiendo del modelo de componente, esta luz podría parpadea incluso si no está habilitada la interfaz de red. |
| Indicadores de entrada | Indica el progreso de inicio cuando se activa el controlador de. Si el dispositivo de StorSimple no se inicia, este LED le ayudará a Microsoft Support identificar el punto en el proceso de inicio del error. |

>[AZURE.IMPORTANT] 
Si está encendido el LED de error, hay un problema con el módulo de controlador que se pueden resolver al reiniciar el controlador. Póngase en contacto con Microsoft Support si reiniciar el controlador no se soluciona este problema.  


### <a name="monitoring-leds-for-the-ebod-ebod-enclosure"></a>Supervisión LED para el EBOD (alojamiento EBOD)  

Cada uno de los controladores de Gb/s SAS EBOD 6 tiene indicadores que indican su estado tal como se muestra en la siguiente ilustración.  

  ![Supervisión LED - caracteres enmarcados EBOD][5]

Utilice la siguiente tabla para determinar si el módulo de controlador EBOD está funcionando normalmente.  

### <a name="ebod-controller-module-indicator-leds"></a>Indicadores LED del módulo de controlador EBOD  

|Estado | Módulo de i/OS Aceptar (verde) | Errores de módulo de i/OS (ámbar) | Actividad de puerto de host (verde) |
|-------|----------------------|-------------------------------|----------------------------|
| Módulo del controlador de Aceptar | ACTIVADO | DESACTIVAR | - |
| Errores del módulo de controlador | DESACTIVAR | ACTIVADO | - |
| Sin conexión de puerto de host externo | - | - | DESACTIVAR |
| Conexión de puerto de host externo: sin actividad | - | - | ACTIVADO |
| Conexión de puerto de host externo - actividad | - | - | Intermitente |
| Error de metadatos del módulo de controlador | Intermitente | - | - |

## <a name="disk-drive-indicator-leds-for-the-primary-enclosure-and-ebod-enclosure"></a>Indicadores LED de la unidad de disco para los caracteres enmarcados principal y caracteres enmarcados EBOD

El dispositivo StorSimple tiene unidades de disco ubicadas en el alojamiento principal y los caracteres enmarcados EBOD. Cada unidad de disco contiene supervisión indicador LED, como se describe en esta sección. 

Para las unidades de disco, el estado de unidad viene indicado por un verde montaje de LED y un LED ámbar rojo en la parte frontal de cada módulo de unidad carrier. La ilustración siguiente muestra estos indicadores.

  ![Indicadores de unidad de disco][6]
 
Utilice la siguiente tabla para determinar el estado de cada unidad de disco, lo que afecta a su vez el panel general frontal LED estado.  

### <a name="disk-drive-indicator-leds-for-the-ebod-enclosure"></a>Indicadores LED de la unidad de disco para el alojamiento EBOD  

| Estado | LED de actividad de Aceptar (verde) | Error de LED (rojo ámbar) | LED del panel de operaciones de asociados |
|-------|--------------------------|----------------------|-------------------------|
| Sin unidad instalado | DESACTIVAR | DESACTIVAR | Ninguno |
| Unidad instalado y en funcionamiento | Intermitente o desactivar con actividad | X | Ninguno |
| Conjunto de identidad de dispositivos de servicios de alojamiento SCSI (sus) | ACTIVADO | Parpadeando 1 segundo en 1/segundo desactivado | Ninguno |
| Conjunto de bits de errores de sus dispositivos | ACTIVADO | ACTIVADO | Error lógico (rojo) |
| Error de circuito de control de alimentación | DESACTIVAR | ACTIVADO | Módulo de falla (rojo) |

## <a name="audible-alarms"></a>Alarmas sonoras  

Un dispositivo StorSimple contiene alarmas sonoras asociadas con los caracteres enmarcados principal y los caracteres enmarcados EBOD. Una alarma audible se encuentra en el panel frontal (también conocido como en el panel de operaciones) de ambos documentos adjuntos. La alarma audible indica cuando existe una condición de error. Las condiciones siguientes activarán la alarma:  

- Error de ventilador o error
- Voltaje fuera del rango
- Sobre o en la condición de temperatura
- Saturación térmica
- Errores del sistema
- Error de lógica
- Fuente de alimentación
- Eliminación de un módulo alimentación/refrigeración (PCM)  

La siguiente tabla describe los distintos Estados de alarma.  

### <a name="alarm-states"></a>Estados de alarma  

| Estado de alarma | Acción | Acción con el botón Desactivar presionado |
|------------|---------|---------------------------------|
| S0 | Modo normal: silencioso | Bip dos veces |
| S1 | Modo de errores: 1 segundo en 1/segundo desactivado | Transición a S2 o S3 (vea las notas) |
| S2 | Recuerde a modo: sonido intermitente | Ninguno |
| S3 | Modo silenciado: silencioso | Ninguno |
| S4 | Modo de errores críticos: alarma continuo | No disponible: silencio no está activo |

> [AZURE.NOTE] 

>  - En estado de alarma S1, si presiona silencio antes de 2 minutos, cambia automáticamente el estado al S2 o S3.  
>  - Estados de alarma S1 a S4 volver a S0 después de borra la condición de error.  
>  - Puede especificar el estado de errores críticos S4 desde cualquier otro estado.  

Puede desactivar la alarma audible al pulsar el botón Desactivar en el panel de operaciones. Silenciar automática si se producirá después de dos minutos desactivar cambiar manualmente no funcionan. Cuando se desactiva la alarma, seguirá sonido con sonidos intermitentes cortos para indicar que todavía existe un problema. La alarma será silenciosa cuando se borren todos los problemas.  

La siguiente tabla describe las diversas condiciones de alarma.  

### <a name="alarm-conditions"></a>Condiciones de alarma  

| Estado | Gravedad | Alarma | Operaciones LED del panel |
|--------|---------|--------|----------------|
| Alerta PCM: pérdida de corriente desde una única PCM | Error: sin pérdida de redundancia | S1 | Errores de módulo|
|Alerta PCM: pérdida de corriente desde una única PCM | Error: pérdida de redundancia | S1 | Errores de módulo |
| Error PCM ventilador | Error: pérdida de redundancia | S1 | Errores de módulo |
| SBB módulo detectado PCM errores | Errores | S1 | Errores de módulo |
| Quita PCM | Error de configuración | Ninguno | Errores de módulo |
| Error de configuración de caracteres enmarcados | Error: crítica | S1 | Errores de módulo |
| Alerta de temperatura de advertencia bajo | Advertencia | S1 | Errores de módulo |
| Alerta de temperatura de advertencia alto | Advertencia | S1 | Errores de módulo |
| Sobre alarma de temperatura | Error: crítica | S1 | Errores de módulo |
| Error de bus I2C | Error: pérdida de redundancia | S1 | Errores de módulo |
| Error de comunicación (I2C) del panel de operaciones | Error: crítica     | S1 | Errores de módulo |
| Error de controlador | Error: crítica | S1 | Errores de módulo |
| Errores de módulo de interfaz SBB | Error: crítica | S1 | Errores de módulo |
| Errores de módulo de interfaz SBB: sin módulos funcionamiento restante | Error: crítica | S4 | Errores de módulo |
| Módulo de interfaz SBB quitado | Advertencia | Ninguno | Errores de módulo |
| Error de control de alimentación de unidad | Advertencia: sin pérdida de alimentación de unidad | S1 | Errores de módulo |
| Error de control de alimentación de unidad | Error: crítico; pérdida de alimentación de unidad | S1 | Errores de módulo |
| Unidad quitada | Advertencia | Ninguno | Errores de módulo |
| Alimentación suficiente disponible | Advertencia | Ninguno | Errores de módulo |

## <a name="next-steps"></a>Pasos siguientes

Más información sobre el [estado y componentes de hardware de StorSimple](storsimple-monitor-hardware-status.md).

[1]: ./media/storsimple-monitoring-indicators/storsimple-monitoring-indicators-IMAGE01.png
[2]: ./media/storsimple-monitoring-indicators/storsimple-monitoring-indicators-IMAGE02.png
[3]: ./media/storsimple-monitoring-indicators/storsimple-monitoring-indicators-IMAGE03.png
[4]: ./media/storsimple-monitoring-indicators/storsimple-monitoring-indicators-IMAGE04.png
[5]: ./media/storsimple-monitoring-indicators/storsimple-monitoring-indicators-IMAGE05.png
[6]: ./media/storsimple-monitoring-indicators/storsimple-monitoring-indicators-IMAGE06.png

 
