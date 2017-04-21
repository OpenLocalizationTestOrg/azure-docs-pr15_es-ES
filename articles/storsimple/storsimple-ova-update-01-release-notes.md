<properties 
   pageTitle="Notas de la versión de actualizaciones de matriz Virtual StorSimple | Microsoft Azure"
   description="Describe problemas importantes de abrir y resoluciones de la matriz Virtual StorSimple ejecutar Update 0,2 y 0,1."
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
   ms.workload="NA"
   ms.date="06/16/2016"
   ms.author="alkohli" />

# <a name="storsimple-virtual-array-update-02-and-01-release-notes"></a>Notas de la versión de StorSimple Virtual matriz actualización 0,2 y 0,1

## <a name="overview"></a>Información general

Las notas siguientes identifican los problemas de abrir importantes y los problemas resueltos matriz Virtual de Microsoft Azure StorSimple actualizaciones. (Matriz Virtual de Microsoft Azure StorSimple es también conocida como el dispositivo virtual de StorSimple local o el dispositivo virtual de StorSimple). 

Las notas se actualizan continuamente y como se detectan problemas importantes que requieren una solución, que se hayan agregado. Antes de implementar el dispositivo virtual de StorSimple, detenidamente la información contenida en las notas.

Actualización de 0,2 corresponde a la versión de software **10.0.10280.0**; Actualización de 0,1 es versión **10.0.10279.0**. Las siguientes secciones muestran los cambios para cada actualización. 

> [AZURE.NOTE] Actualizaciones interrupciones y reiniciarán el dispositivo. Si i/OS en curso, el dispositivo producirá el tiempo de inactividad.

## <a name="issues-fixed-in-the-update-02"></a>Problemas corregidos en la actualización de 0,2
Actualización de 0,2 incluye todos los cambios de actualización de 0,1 Además de la solución que se describe en la tabla siguiente:

Característica                              | Problema                                                                                                                                                                                                                                                                                                                           |
--------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
Actualizaciones                                 | En la última versión, las actualizaciones no se han detecta automáticamente en el portal de clásico Azure, por lo que había que usar la interfaz de usuario de Web local para instalar las actualizaciones. Este problema se corrige en esta versión. Después de instalar la actualización de 0,2, puede instalar actualizaciones futuras con el portal de clásico de Azure.                       

## <a name="whats-new-in-the-update-01"></a>Novedades de la actualización de 0,1

Actualización de 0,1 contiene las siguientes mejoras y correcciones de errores. 

- **Resistencia mejorada para interrupciones de nube**: esta versión tiene varias correcciones de errores alrededor de recuperación de copia de seguridad, restaurar y niveles en caso de una interrupción de conectividad de la nube. 

- **Restaurar el mejor rendimiento**: esta versión tiene que haber reducido significativamente la hora de finalización de los trabajos de restauración correcciones de errores.

- **Optimización de Reclamación de espacio automático**: cuando se eliminan datos en volúmenes thin aprovisionados, necesitan reclamar los bloques de almacenamiento sin usar. Esta versión ha mejorado el proceso de recuperación de espacio de la nube es el resultado en el espacio no usado que se encuentre disponible más rápido en comparación con las versiones anteriores.

- **Nuevas imágenes de disco virtual**: nuevo disco duro virtual, VHDX y VMDK ahora están disponibles a través del portal clásico Azure. Puede descargar estas imágenes para aprovisionar nuevos dispositivos de actualización 0,1.

- **Mejorar la precisión del estado de los trabajos en el portal**: en la versión anterior del software, no era granular trabajo informes de estado en el portal. Este problema está resuelto en esta versión.

- **Experiencia de unirse a un dominio**: correcciones de errores relacionados con la unión de dominio y cambiar el nombre del dispositivo.


## <a name="issues-fixed-in-the-update-01"></a>Problemas corregidos en la actualización de 0,1

La tabla siguiente proporciona un resumen de los problemas corregidos en esta versión.

| No.  | Característica                              | Problema                                                                                                                                                                                                                                                                                                                           |
|------|--------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 1    | VMDK                                 | En algunas versiones de VMware, se ha visto el disco OS como disperso causando alertas e interrumpir las operaciones normales. Esto se ha corregido en esta versión.                                                                                                                                                                                    |
| 2    | servidor iSCSI                         | En la última versión, el usuario se debe especificar una puerta de enlace para cada interfaz de red habilitado del dispositivo virtual StorSimple. Este comportamiento ha cambiado en esta versión para que el usuario tiene que configurar al menos una puerta de enlace para todas las interfaces de red habilitado.                                                                              |
| 3    | Paquete de compatibilidad                      | En la versión anterior del software, recopilación del paquete de compatibilidad con error cuando los tamaños de paquete eran más de 1 GB. Este problema se corrige en esta versión.                                                                                                                                                                               |
| 4    | Acceso a la nube                         |  En la última versión, si la matriz Virtual StorSimple no tiene conectividad de red y se reinicia, la interfaz de usuario local tendría problemas de conectividad. Este problema se corrige en esta versión.                                                                                                                            |
| 5    | Gráficos de supervisión                    | En la versión anterior, después de un error de dispositivo, los gráficos de utilización de la capacidad de nube muestran valores incorrectos en el portal de clásico de Azure. Esto queda fijado en la versión actual.                                                                                                                          |



## <a name="known-issues-in-the-update-01"></a>Problemas conocidos de la actualización de 0,1

En la tabla siguiente proporciona un resumen de los problemas conocidos para la matriz Virtual StorSimple e incluye los problemas que se indica la versión de las versiones anteriores. **La versión de problemas especificada en esta versión se marcan con un asterisco. Casi todos los problemas de esta lista se transfieren desde la versión GA de matriz Virtual de StorSimple.**


| No. | Característica | Problema | Solución alternativa y comentarios |
|-----|--------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **1.** | Actualizaciones | Los dispositivos virtuales creados en la versión preliminar no se puede actualizar a una versión compatible de disponibilidad General. | Estos dispositivos virtuales deben error sobre la versión de disponibilidad General mediante un flujo de trabajo de desastres recuperación (DR). |
| **2.** | Preparación de datos de un disco | Una vez que haya proporcionado un disco de datos de un determinado tamaño especificado y crea el dispositivo virtual StorSimple correspondiente, no debe expandir o reducir el disco de datos. Intentar hacerlo, se producirá una pérdida de todos los datos de los niveles locales del dispositivo. |   |
| **3.** | Directiva de grupo | Cuando un dispositivo está unido al dominio, aplicar una directiva de grupo puede afectar el funcionamiento del dispositivo. | Asegúrese de que la matriz virtual está en su propia unidad organizativa (OU) de Active Directory y no objetos de directiva de grupo (GPO) se aplican a él.|
| **4.** | Interfaz de usuario de web local. | Si están habilitadas las características de seguridad mejorada en Internet Explorer (IE ESC), es posible que algunas páginas de la interfaz de usuario de web local como mantenimiento o solución de problemas no funcionen correctamente. Botones de estas páginas también pueden no funcionar. | Desactivar las características de seguridad mejorada en Internet Explorer.|
| **5.** | Interfaz de usuario de web local. | En una máquina virtual Hyper-V, las interfaces de red en la web en la interfaz de usuario se muestran como 10 GB/s interfaces. | Este comportamiento es un reflejo de Hyper-V. Hyper-V siempre muestra 10 GB/s para adaptadores de red virtual. |
| **6.** | En niveles volúmenes o recursos compartidos | Bloqueo de aplicaciones que funcionan con la StorSimple volúmenes en niveles no se admite el intervalo de bytes. Si se habilita el bloqueo de intervalo de bytes, interconexión StorSimple no funcionará. | Medidas recomendadas incluyen: <br></br>Desactivar el bloqueo en la lógica de la aplicación de intervalo de bytes.<br></br>Elija esta opción para colocar los datos de esta aplicación en volúmenes localmente anclados en lugar de volúmenes en niveles.<br></br>*Advertencia*: si usa localmente anclados volúmenes y se habilita el bloqueo de intervalo de bytes, tenga en cuenta que el volumen localmente anclado puede ser en línea, incluso antes de que finalice la restauración. En estos casos, si una restauración está en curso, a continuación, espere la restauración completar. |
| **7.** | Recursos compartidos en niveles | Trabajar con archivos de gran tamaño podría provocar lenta nivel fuera. | Cuando trabaje con archivos grandes, se recomienda que el archivo de mayor tamaño es menor que 3% del tamaño de compartir. |
| **8.** | Utilizar la capacidad de recursos compartidos | Es posible que vea Compartir consumo en ausencia de todos los datos en el recurso compartido. Esto es porque la capacidad usada para recursos compartidos incluye metadatos. |   |
| **9.** | Recuperación | Solo puede realizar la recuperación de un servidor de archivos al mismo dominio que el dispositivo de origen. Recuperación a un dispositivo de destino en otro dominio no es compatible con esta versión. | Se implementará en una versión posterior. |
| **10.** | Azure PowerShell | No se puede administrar los dispositivos virtuales de StorSimple a través de PowerShell de Azure en esta versión. | Toda la administración de los dispositivos virtuales debe realizarse a través del portal clásico Azure y la interfaz de usuario de web local. |
| **11.** | Cambiar la contraseña | La consola de dispositivo de matriz virtual solo acepta la entrada en el formato de teclado en-US. |   |
| **12.** | CHAP | No se puede quitar las credenciales CHAP una vez creadas. Además, si se modifican las credenciales CHAP, debe desconectar los volúmenes y ponerlos en línea para que el cambio surta efecto. | Se resolverá en una versión posterior. |
| **13.** | servidor iSCSI  | La 'utilizar almacenamiento' que aparece para un volumen iSCSI puede ser diferentes en el servicio Administrador de StorSimple y el host de iSCSI. | El host de iSCSI tiene la vista del sistema de archivos.<br></br>El dispositivo ve los bloques asignados cuando el volumen estaba en el tamaño máximo.|
| **14.** | Servidor de archivo *  | Si un archivo en una carpeta tiene una secuencia de datos alternativa (anuncios) asociada, los anuncios no se realizan copias de seguridad o se restaura a través de recuperación, clonar y recuperación de nivel de elemento.| |


## <a name="next-step"></a>Siguiente paso

[Instalar actualizaciones](storsimple-ova-install-update-01.md) en su matriz Virtual de StorSimple.
