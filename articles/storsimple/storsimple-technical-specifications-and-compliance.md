<properties 
   pageTitle="Especificaciones técnicas de StorSimple | Microsoft Azure"
   description="Describe las especificaciones técnicas y la información de cumplimiento de estándares normativos para los componentes de hardware de StorSimple."
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
   ms.date="08/17/2016"
   ms.author="alkohli" />

# <a name="technical-specifications-and-compliance-for-the-storsimple-device"></a>Especificaciones técnicas y cumplimiento para el dispositivo StorSimple

## <a name="overview"></a>Información general

Los componentes de hardware de su dispositivo de Microsoft Azure StorSimple cumplen las especificaciones técnicas y los estándares normativos descritos en este artículo. Las especificaciones técnicas describen potencia y refrigeración módulos (PCMs), unidades de disco, la capacidad de almacenamiento y documentos adjuntos. La información de cumplimiento cubre cosas como estándares internacionales, seguridad y emisiones y cableado.


## <a name="power-and-cooling-module-specifications"></a>Especificaciones de alimentación y módulo de refrigeración  

El dispositivo de StorSimple tiene dos 100-240V ventilador dual, compatible con SBB módulos de refrigeración de Power (PCMs). Esto proporciona una configuración de alimentación. Si se produce un error en un PCM, el dispositivo continuará funcionando normalmente en el otro PCM hasta que se reemplaza el módulo que ha fallado.  

Los caracteres enmarcados EBOD utiliza un PCM W 580 y caracteres enmarcados principal utiliza un 764 PCM W. Las tablas siguientes enumeran las especificaciones técnicas asociadas con la PCMs.

| Especificación           | 580 W PCM (EBOD)                                    | 764 W PCM (principal)                                |
|------------------------ | --------------------------------------------------- | -------------------------------------------------- |
| Potencia máxima de salida    | 580 W                                               | 764                                                |
| Frecuencia               | 50/60Hz                                            | 50/60Hz                                           |
| Selección de intervalo de voltaje | Automático, que abarca: 90-264 V CA, 47 y 63 Hz               | Automático, que abarca: 90-264 V CA, 47 y 63 Hz               |
| Corriente máxima  | 20 RESPUESTAS DE                                                | 20 RESPUESTAS DE                                               |
| Corrección del factor de energía | > voltaje de entrada nominal 95%                          | > voltaje de entrada nominal 95%                         |
| Armonía               | Cumpla EN61000-3-2                                   | Cumpla EN61000-3-2                                  |
| Salida                  | Voltaje en espera de 5 v @ 2.0 A                          | Voltaje en espera de 5 v @ 2.7 A                         |
|                         | + 5 v @ 42 A                                          | + 5 v @ 40 A                                         |
|                         | + 12 v @ 38 A                                         | + 12 v @ 38 A                                        |
| Caliente acoplable           |  Sí                                                | Sí                                                |
| Modificadores y LED       | Desactivación de aire Acondicionado del conmutador y el indicador de estado cuatro LED     | Desactivación de aire Acondicionado del conmutador y el indicador de estado seis LED     |
| Caracteres enmarcados refrigeración       | Axial ventiladores con control de velocidad de ventilador variable  | Axial ventiladores con control de velocidad de ventilador variable |

 
## <a name="power-consumption-statistics"></a>Estadísticas de consumo de energía  

La tabla siguiente muestra los datos de consumo normal de energía (valores reales pueden variar desde la publicados) para los distintos modelos de dispositivo StorSimple. 
 
 Condiciones | 240 V CA | 240 V CA | 240 V CA | 110 V CA | 110 V CA | 110 V CA 
 ---------- | -------- | -------- | -------- | -------- | -------- | -------- 
 Unidades lentas, ventiladores inactivas | 1,45 A  |0.31 kW | 1057.76 BTU/hora | 3.19 A | 0.34 kW | 1160.13 BTU/hora 
 Obtener acceso a las unidades lentas, ventiladores | 1,54 A | 0,33 kW | 1126.01 BTU/hora | 3.27 A | 0.36 kW | 1228.37 BTU/hora 
 Dos PSUs con tecnología de unidades rápidas, ventiladores inactivas, | 2.14 A | 0,49 kW  | 1671.95 BTU/hora | 4,99 A | 0.54 kW | 1842.56 BTU/hora 
 Ventiladores rápidos, las unidades inactivas, una fuente de alimentación con tecnología uno inactivo | 2.05 A | 0.48 kW | 1637.83 BTU/hora | 4.58 A | 0,50 kW | 1706.07 BTU/hora 
 Obtener acceso a dos PSUs con tecnología de unidades rápidas, ventiladores | 2.26 A | 0,51 kW | 1740.19 BTU/hora | 4,95 A | 0.54 kW | 1842.56 BTU/hora 
 Ventiladores rápidos, unidades de acceso, una fuente de alimentación con tecnología uno inactivo | 2.14 A |0,49 kW | 1671.95 BTU/hora | 4.81 A  | 0,53 kW | 1808.44 BTU/hora 

## <a name="disk-drive-specifications"></a>Especificaciones de la unidad de disco  

El dispositivo StorSimple admite hasta 12 unidades de disco SCSI conectado en serie (SA) de 3,5 formulario factores. Las unidades reales pueden ser una combinación de unidades de estado sólido (SSD) o unidades de disco duro (disco duro), dependiendo de la configuración de productos. Las 12 ranuras de unidad de disco se encuentran en una configuración de 3 por 4 delante de los caracteres enmarcados. Los caracteres enmarcados EBOD permite almacenamiento adicional para otra 12 unidades de disco. Estos son siempre unidades de disco duro.  

## <a name="storage-specifications"></a>Especificaciones de almacenamiento
Los dispositivos StorSimple tienen una mezcla de unidades de disco duro y unidades de estado sólido para la 8100 y 8600. La capacidad total utilizable para 8100 y 8600 son aproximadamente 15 TB y 38 TB respectivamente. La siguiente tabla documenta los detalles de SSD, la unidad de disco duro y la capacidad de nube en el contexto de la capacidad de la solución StorSimple.

| Modelo de dispositivo / capacidad                         | 8100                                                    | 8600                                                    |
|------------------------------------------------|---------------------------------------------------------|---------------------------------------------------------|
| Número de unidades de disco duro (disco duro)              |   8                                                     |  19                                                     |
| Número de unidades de estado sólido (SSD)            |   4                                                     |  5                                                      |
| Solo la capacidad de unidad de disco duro                            |   4 TB                                                  |  4 TB                                                   |
| Solo la capacidad de SSD                            |   400 GB                                                |  800 GB                                                 |
| Capacidad de sustitución                                 |   4 TB                                                  | 4 TB                                                    |
| Capacidad de unidad de disco duro utilizable                            | 14 TB                                                   | 36 TB                                                   |
| Capacidad SSD utilizable                            | 800 GB                                                  | 2 TB                                                    |
| La capacidad total utilizable *                         | ~ 15 TB                                                 | ~ 38 TB                                                 |
| Capacidad máxima solución (incluida la nube)    | 200 GB                                                  | 500 TB                                                  |


<sup> * </sup> -  *La capacidad total utilizable incluye la capacidad disponible para buffers.*, datos y metadatos

## <a name="enclosure-dimensions-and-weight-specifications"></a>Especificaciones de peso y dimensiones de caracteres enmarcados  

Las tablas siguientes enumeran las especificaciones de caracteres enmarcados distintos de dimensiones y grosor.  

### <a name="enclosure-dimensions"></a>Dimensiones de caracteres enmarcados
La siguiente tabla enumeran las dimensiones de los caracteres enmarcados en milímetros y pulgadas.

|Caracteres enmarcados |Milímetros |Pulgadas |
|----------|------------|-------| 
| Alto |87.9 | 3,46 |
| Ancho en la pestaña de montaje | 483 | 19.02 |
| Ancho en el cuerpo de caracteres enmarcados | 443 | 17.44 |
| Profundidad de la pestaña montaje frontal al extremo del cuerpo de caracteres enmarcados | 577 | 22.72 |
| Profundidad del extremo más alejado de caracteres enmarcados desde el panel de acciones | 630.5 | 24.82 |
| Profundidad de la pestaña montaje al extremo más alejado de caracteres enmarcados |   603 | 23.74 |

### <a name="enclosure-weight"></a>Peso de caracteres enmarcados  

Según la configuración, un alojamiento principal completamente cargado puede valorar de 21 a 33 kg y requiere dos personas controlarlo. 
 
| Caracteres enmarcados | Peso |
|-----------|--------| 
| Peso máximo (depende de la configuración) |30 kg: 33 kg |
| Vaciar (no se ajusta unidades) |21 – 23 kg |

## <a name="enclosure-environment-specifications"></a>Especificaciones del entorno de caracteres enmarcados  

Esta sección enumeran las especificaciones relacionadas con el entorno de caracteres enmarcados. La temperatura, humedad, altitud, choque, vibración, orientación, seguridad y compatibilidad electromagnético (EMC) se incluyen en esta categoría.  

### <a name="temperature-and-humidity"></a>Temperatura y humedad

| Caracteres enmarcados        | Intervalo de temperatura ambiente  | Ambiente humedad relativa | Máximo húmeda   |
|------------------|----------------------------|---------------------------|--------------------|
| Funcionamiento      | 5-35° C (41° F - 95° F)    | 20-80% no-condensación- | 28° C (82° F)        |
| No operativos  | -40-70° C (40° F - 158° F) | 5-100% sin condensación  | 29° C (84° F)        |

### <a name="airflow-altitude-shock-vibration-orientation-safety-and-emc"></a>Flujo de aire, altitud, choque, vibración, orientación, seguridad y EMC
 
| Caracteres enmarcados          | Especificaciones operativas                                                |
|--------------------|---------------------------------------------------------------------------| 
| Flujo de aire            | Flujo de aire del sistema es frontal a posterior. Sistema debe utilizarse con una instalación de baja presión, trasera escape. Presión creada por bastidor puertas y obstáculos no debe superar a 5 pascales (medidor de agua de 0,5 mm). |
| Altitud, operativa  | -30 metros 3045 metros (-100 pies a 10.000 pies) con la temperatura máxima de funcionamiento, anule la clasificación por 5 ° C encima 7000 pies. |
| Altitud, no operativa  | -305 metros 12.192 metros (– 1.000 pies a 40.000 pies) |
| Descarga, operativa  | 5g 10 ms ½ seno en |
| Descarga, no operativa  | seno de 30g de 10 ms ½ |
| Vibración, operativa  | g 0.21 RMS 5-500 Hz aleatorio |
| Vibración, no operativa  | g 1.04 RMS 2-200 Hz aleatorio |
| Vibración, reubicación  | seno de 3g de 2-200 Hz |
| Orientación y montaje  | montaje de bastidor 19"(2 unidades EIA) |
| Guías de bastidor  | Para ajustar la profundidad mínima 700 mm (31.50 pulgadas) racks compatible con IEC 297 |
| Seguridad y aprobaciones  |   CE y UL EN 61000-3, IEC 61000-3, UL 61000-3 |
| EMC  | EN55022 (CISPR - A), FCC A |

## <a name="international-standards-compliance"></a>Cumplimiento de normas internacionales
El dispositivo de Microsoft Azure StorSimple cumple con los estándares internacionales siguientes:  

- CE - EN 60950-1  
- Informe CB a IEC 60950-1  
- UL y cUL a UL 60950-1  

## <a name="safety-compliance"></a>Cumplimiento de seguridad  

El dispositivo de Microsoft Azure StorSimple cumple los siguientes indicadores de seguridad:  

- Aprobación de tipo de producto de sistema: UL, cUL, CE  
- Cumplimiento de seguridad: UL 60950, IEC 60950, EN 60950  

## <a name="emc-compliance"></a>Cumplimiento de EMC 

El dispositivo de Microsoft Azure StorSimple cumple las siguientes clasificaciones de EMC.  

### <a name="emissions"></a>Emisiones

El dispositivo es compatible con EMC para niveles de emisiones realizadas y radia.  

- Emisiones realizadas limitan niveles: CFR 47 parte 15B clase A EN55022 Clase A CISPR clase A  
- Radiación limita niveles: CFR 47 parte 15B clase A EN55022 Clase A CISPR clase A   

### <a name="harmonics-and-flicker"></a>Armonía y Flickr  

El dispositivo cumple con EN61000-3-2 y 3.  

### <a name="immunity-limit-levels"></a>Niveles de inmunidad límite  
El dispositivo cumple con EN55024.  

## <a name="ac-power-cord-compliance"></a>Cumplimiento de cable de alimentación AC
  
El conector y el conjunto de cable de alimentación completa deben cumplir las normas apropiadas para el país en el que se está usando el dispositivo y deben tener las aprobaciones de seguridad que son aceptables en ese país. Las tablas siguientes enumeran los estándares de los Estados Unidos y Europa.  

### <a name="ac-power-cords---usa-must-be-nrtl-listed"></a>Cables de alimentación AC - Estados Unidos (debe ser NRTL enumerado)

| Componente       | Especificación                                                     |
| --------------- | ----------------------------------------------------------------- | 
| Tipo de cable       | VP o SVT 18 AWG como mínimo, 3 conductores 2.0 longitud máxima |
| Complemento            | Enchufe de datos adjuntos de tipo de conexión a tierra NEMA 5-15P clasificación 120 V, 10 A; o IEC 320 C14 250 V, 10 A |
| Socket          | IEC 320 C-13, 250 V, 10 A                                         |

### <a name="ac-power-cords---europe"></a>Cables de alimentación AC - Europa

| Componente       | Especificación                                                     |
| --------------- | ----------------------------------------------------------------- | 
| Tipo de cable       | Unificados H05-VVF-3G1.0                                         |
| Socket          | IEC 320 C-13, 250 V, 10 A                                         |

## <a name="supported-network-cables"></a>Cables de red admitidos  

Para las interfaces de red GbE 10, DATA 2 y 3 de datos, consulte la [lista de cables de red compatibles y módulos](storsimple-supported-hardware-for-10-gbe-network-interfaces.md).

## <a name="next-steps"></a>Pasos siguientes

Ya está listo para implementar un dispositivo StorSimple en el centro de datos. Para obtener más información, vea [implementar el dispositivo local](storsimple-deployment-walkthrough-u2.md).  
