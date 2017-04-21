<properties 
   pageTitle="Notas de la versión de matriz Virtual de StorSimple | Microsoft Azure"
   description="Describe problemas abiertos y resoluciones de la matriz Virtual StorSimple."
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
   ms.date="05/13/2016"
   ms.author="alkohli" />

# <a name="storsimple-virtual-array-release-notes"></a>Notas de la versión de matriz Virtual de StorSimple

## <a name="overview"></a>Información general

Las notas siguientes identifican los problemas de abrir críticos para la versión de marzo de 2016 disponibilidad general (GA) de la StorSimple de Azure Microsoft Virtual matriz (también conocido como el dispositivo virtual de StorSimple local o el dispositivo virtual de StorSimple). Esta versión corresponde a la versión 10.0.10271.0.

Las notas se actualizan continuamente y como se detectan problemas importantes que requieren una solución, que se hayan agregado. Antes de implementar el dispositivo virtual de StorSimple, detenidamente la información contenida en las notas. 

La tabla siguiente proporciona un resumen de los problemas conocidos en esta versión.


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
