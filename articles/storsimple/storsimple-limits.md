<properties 
   pageTitle="Límites de sistema StorSimple | Microsoft Azure"
   description="Describe los límites de sistema y tamaños recomendados para los componentes de la serie 8000 StorSimple y conexiones."
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
   ms.date="10/06/2016"
   ms.author="alkohli" />

# <a name="storsimple-system-limits"></a>Límites del sistema StorSimple

## <a name="overview"></a>Información general

StorSimple proporciona almacenamiento scalable y flexible para su centro de datos. Sin embargo, existen algunas limitaciones que deben tener en cuenta al planear, implementar y trabajar con la solución StorSimple. La siguiente tabla describe estos límites y proporciona algunas recomendaciones para que pueda obtener el máximo partido a su solución StorSimple.

| Identificador de límite | Límite | Comentarios |
|----------------- | ------|--------- |
| Número máximo de credenciales de la cuenta de almacenamiento | 64 | |
| Número máximo de contenedores de volumen | 64 | |
| Número máximo de volúmenes | 255 | |
| Número máximo de volúmenes localmente anclados | 32 | |
| Número máximo de programaciones de cada plantilla de ancho de banda | 168 | Una programación para cada hora, todos los días de la semana (24 * 7). |
| Tamaño máximo de un volumen en niveles en dispositivos físicos | 64 TB para 8100 y 8600 | 8100 y 8600 son dispositivos físicos. |
| Tamaño máximo de un volumen en niveles en dispositivos virtuales en Azure | 30 TB para 8010 <br></br> 64 TB para 8020 | 8010 y 8020 son dispositivos virtuales en Azure que utilizan almacenamiento estándar y Premium, respectivamente. |
| Tamaño máximo de un volumen localmente anclado en dispositivos físicos | 8,5 TB para 8100 <br></br> 22,5 TB para 8600 | 8100 y 8600 son dispositivos físicos. |
| Número máximo de conexiones | 512 | |
| Número máximo de conexiones de iniciadores | 512 | |
| Número máximo de registros de control de acceso por dispositivo | 64 | |
| Número máximo de volúmenes por directiva de copia de seguridad | 24 | |
| Número máximo de copias de seguridad que se conservan por programación (en una directiva de copia de seguridad) | 64 | |
| Número máximo de programaciones de directiva de copia de seguridad | 10 | |
| Número máximo de instantáneas de cualquier tipo que se pueden retener por volumen | 256 | Este número incluye instantáneas locales e instantáneas en la nube. |
| Número máximo de instantáneas que pueden aparecer en cualquier dispositivo | 10.000 | |
| Número máximo de volúmenes que se puede procesar en paralelo para copia de seguridad, restaurar o clonar | 16 |<ul><li>Si hay más de 16 volúmenes, se procesan secuencialmente como procesamiento ranuras no estarán disponibles.</li><li>No se produce nuevas copias de seguridad de un duplicado o un volumen en niveles restaurado hasta que finalice la operación. Sin embargo, para un volumen local, las copias de seguridad se admiten después de que el volumen está en línea.</li></ul>|
| Restaurar y clonar recuperar tiempo para volúmenes en niveles | < 2 minutos | <ul><li>El volumen estará disponible dentro de la operación de restauración o clonar, independientemente del tamaño de volumen 2 minutos.</li><li>El rendimiento de volumen inicialmente puede ser menor que normal como la mayoría de los datos y metadatos todavía se encuentra en la nube. El rendimiento puede aumentar como flujos de datos de la nube en el dispositivo StorSimple.</li><li>El tiempo total para descargar metadatos depende del tamaño de volumen asignado. Metadatos automáticamente entra en el dispositivo en el fondo a la tasa de 5 minutos por TB de datos de volumen asignado. Este tipo puede verse afectado por el ancho de banda de Internet en la nube.</li><li>La operación de clonar o restaurar es completa cuando todos los metadatos que se están en el dispositivo.</li><li>No se puede realizar operaciones de copia de seguridad hasta la restauración o está totalmente completa la operación de clonar.|
| Restaurar recuperar tiempo para volúmenes localmente anclados | < 2 minutos | <ul><li>El volumen estará disponible dentro de la operación de restauración, independientemente del tamaño de volumen 2 minutos.</li><li>El rendimiento de volumen inicialmente puede ser menor que normal como la mayoría de los datos y metadatos todavía se encuentra en la nube. El rendimiento puede aumentar como flujos de datos de la nube en el dispositivo StorSimple.</li><li>El tiempo total para descargar metadatos depende del tamaño de volumen asignado. Metadatos automáticamente entra en el dispositivo en el fondo a la tasa de 5 minutos por TB de datos de volumen asignado. Este tipo puede verse afectado por el ancho de banda de Internet en la nube.</li><li>A diferencia de volúmenes en niveles, para volúmenes localmente anclados, los datos de volumen también se descargan localmente en el dispositivo. La operación de restauración es completa cuando todos los datos de volumen ha sido puesta en el dispositivo.</li><li>Las operaciones de restauración pueden ser largos. El tiempo total para completar la restauración dependerá el tamaño de la preparación volumen local, el ancho de banda de Internet y los datos existentes en el dispositivo. Operaciones de copia de seguridad en el volumen localmente anclada permitidas mientras la operación de restauración está en curso.|
|Tasa de procesamiento de instantáneas de nube| 15 minutos/TB | <ul><li>Tiempo mínimo para asegurarse de la nube instantánea listo para cargar por TB de datos de volumen asignado de copia de seguridad. </li><li> Tiempo de instantánea de nube total se calcula agregando esta vez a la hora de carga de instantánea, que se ve afectada por el ancho de banda de Internet a la nube.
| Rendimiento de lectura y escritura de cliente máximo (cuando servida desde el nivel SSD) * | 920/720 MB/s con una única interfaz de red 10 GbE | Hasta 2 x con MPIO y dos interfaces de red. |
| Rendimiento de lectura y escritura de cliente máximo (cuando servida desde el nivel de unidad de disco duro) * | 120/250 MB/s |
| Número máximo de clientes lectura y escritura (cuando servida desde el nivel de nube) el rendimiento* de actualización 3 y posteriores** | 40/60 MB/s para volúmenes de niveles<br><br>60/80 MB/s para niveles volúmenes con la opción archivado seleccionada durante la creación de volumen | Rendimiento de lectura depende de clientes generar y mantener suficiente profundidad de cola de i/OS. <br><br>Velocidad logrado depende de la velocidad de la cuenta de almacenamiento subyacente utilizada. | 

& #42; Rendimiento máximo por tipo de i/OS se mide con 100 por cien lectura y escenarios de escritura del 100%. Rendimiento real puede ser inferior y depende de i/OS mezclar y condiciones de red.

& #42; & #42; Números de rendimiento antes de actualizar 3 pueden ser inferiores.

## <a name="next-steps"></a>Pasos siguientes

Revise los [requisitos del sistema StorSimple](storsimple-system-requirements.md). 
