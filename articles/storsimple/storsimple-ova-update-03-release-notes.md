<properties 
   pageTitle="Notas de la versión de actualizaciones de matriz Virtual StorSimple | Microsoft Azure"
   description="Describe los problemas de abrir críticos y resoluciones de la matriz Virtual StorSimple ejecutar Update 0,3."
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
   ms.date="09/15/2016"
   ms.author="alkohli" />

# <a name="storsimple-virtual-array-update-03-release-notes"></a>Notas de la versión 0,3 de actualización de matriz Virtual StorSimple

## <a name="overview"></a>Información general

Las notas siguientes identifican los problemas de abrir importantes y los problemas resueltos matriz Virtual de Microsoft Azure StorSimple actualizaciones.

Las notas se actualizan continuamente y como se detectan problemas importantes que requieren una solución, que se hayan agregado. Antes de implementar la matriz Virtual de StorSimple, detenidamente la información contenida en las notas.

Actualización 0,3 corresponde a la versión de software **10.0.10288.0**.

> [AZURE.NOTE] Actualizaciones interrupciones y reinicie el dispositivo. Si i/OS en curso, el dispositivo genera del tiempo de inactividad.


## <a name="whats-new-in-the-update-03"></a>Novedades de la actualización 0,3

Actualización 0,3 es principalmente una compilación de corregir el error. En esta versión, se han tratado varios errores de errores de copia de seguridad de la versión anterior.

## <a name="issues-fixed-in-the-update-03"></a>Problemas corregidos en la actualización 0,3

La tabla siguiente proporciona un resumen de los problemas corregidos en esta versión.

| No.  | Característica                              | Problema                                                                                                                                                                                                                                                                                                                           |
|------|--------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 1    | Realizar copias de seguridad                                |Un problema se ha visto en la versión anterior, donde las copias de seguridad no completar un recurso compartido de archivos. Si se produce este problema, se producirá un error en el trabajo de copia de seguridad y se genera una alerta crítica en el servicio de administrador de StorSimple para notificar al usuario. Este problema no ha afectará a los datos de los recursos compartidos o el acceso a los datos. La causa identificada y fijada en esta versión. <br></br> La revisión no se aplican retroactivamente a las acciones que ya están viendo este problema. Los clientes que experimentan este problema primero deben aplicar actualización 0,3 después póngase en contacto con Microsoft Support para realizar una copia de seguridad completa del sistema para solucionar el problema. En lugar de ponerse en contacto con Microsoft Support, también pueden restaurar los clientes a un nuevo recurso compartido de una copia de seguridad correcto para los recursos compartidos afectados.                                                                                                                                                                                 |
| 2    | iSCSI                         | Un problema se ha visto en la versión anterior, donde los volúmenes desaparecerá al copiar datos a un volumen en la matriz Virtual StorSimple. Este problema se ha corregido en esta versión. <br></br> Las revisiones surten efecto solo recién creado volúmenes. Las revisiones no se aplican retroactivamente a volúmenes que ya están viendo este problema. Se recomienda a los clientes poner los volúmenes afectados en línea a través del portal clásico Azure, realizar una copia de seguridad de estos volúmenes y, a continuación, restaurar estos volúmenes a nuevos volúmenes.                                                               |


## <a name="known-issues-in-the-update-03"></a>Problemas conocidos de la actualización 0,3

En la tabla siguiente proporciona un resumen de los problemas conocidos para la matriz Virtual StorSimple e incluye los problemas que se indica la versión de las versiones anteriores. 


| No. | Característica | Problema | Solución alternativa y comentarios |
|-----|--------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **1.** | Actualizaciones | Los dispositivos virtuales creados en la versión preliminar no se puede actualizar a una versión compatible de disponibilidad General. | Estos dispositivos virtuales deben error sobre la versión de disponibilidad General mediante un flujo de trabajo de desastres recuperación (DR). |
| **2.** | Preparación de datos de un disco | Una vez que haya proporcionado un disco de datos de un determinado tamaño especificado y crea el dispositivo virtual StorSimple correspondiente, no debe expandir o reducir el disco de datos. Se intenta resultados en una pérdida de todos los datos en los niveles locales del dispositivo. |   |
| **3.** | Directiva de grupo | Cuando un dispositivo está unido al dominio, aplicar una directiva de grupo puede afectar el funcionamiento del dispositivo. | Asegúrese de que la matriz virtual está en su propia unidad organizativa (OU) de Active Directory y no objetos de directiva de grupo (GPO) se aplican a él.|
| **4.** | Interfaz de usuario de web local. | Si están habilitadas las características de seguridad mejorada en Internet Explorer (IE ESC), es posible que algunas páginas de la interfaz de usuario de web local como mantenimiento o solución de problemas no funcionen correctamente. Botones de estas páginas también pueden no funcionar. | Desactivar las características de seguridad mejorada en Internet Explorer.|
| **5.** | Interfaz de usuario de web local. | En una máquina virtual Hyper-V, las interfaces de red en la web en la interfaz de usuario se muestran como 10 GB/s interfaces. | Este comportamiento es un reflejo de Hyper-V. Hyper-V siempre muestra 10 GB/s para adaptadores de red virtual. |
| **6.** | En niveles volúmenes o recursos compartidos | Bloqueo de aplicaciones que funcionan con la StorSimple volúmenes en niveles no se admite el intervalo de bytes. Si se habilita el bloqueo de intervalo de bytes, interconexión StorSimple no funciona. | Medidas recomendadas incluyen: <br></br>Desactivar el bloqueo en la lógica de la aplicación de intervalo de bytes.<br></br>Elija esta opción para colocar los datos de esta aplicación en volúmenes localmente anclados en lugar de volúmenes en niveles.<br></br>*Advertencia*: cuando utiliza localmente anclados volúmenes y se habilita el bloqueo de intervalo de bytes, el volumen localmente anclado puede online incluso antes de que finalice la restauración. En estos casos, si una restauración está en curso, a continuación, espere la restauración completar. |
| **7.** | Recursos compartidos en niveles | Trabajar con archivos de gran tamaño podría provocar lenta nivel fuera. | Cuando trabaje con archivos grandes, se recomienda que el archivo de mayor tamaño es menor que 3% del tamaño de compartir. |
| **8.** | Utilizar la capacidad de recursos compartidos | Es posible que vea Compartir consumo cuando no hay ningún dato en el recurso compartido. Esto es porque la capacidad usada para recursos compartidos incluye metadatos. |   |
| **9.** | Recuperación | Solo puede realizar la recuperación de un servidor de archivos al mismo dominio que el dispositivo de origen. Recuperación a un dispositivo de destino en otro dominio no es compatible con esta versión. | Esto se implementa en una versión posterior. |
| **10.** | Azure PowerShell | No se puede administrar los dispositivos virtuales de StorSimple a través de PowerShell de Azure en esta versión. | Toda la administración de los dispositivos virtuales debe realizarse a través del portal clásico Azure y la interfaz de usuario de web local. |
| **11.** | Cambiar la contraseña | La consola de dispositivo de matriz virtual solo acepta la entrada en el formato de teclado en-US. |   |
| **12.** | CHAP | No se puede quitar las credenciales CHAP una vez creadas. Además, si se modifican las credenciales CHAP, debe desconectar los volúmenes y ponerlos en línea para que el cambio surta efecto. | Este problema se trata de una versión posterior. |
| **13.** | servidor iSCSI  | La 'utilizar almacenamiento' que aparece para un volumen iSCSI puede ser diferentes en el servicio Administrador de StorSimple y el host de iSCSI. | El host de iSCSI tiene la vista del sistema de archivos.<br></br>El dispositivo ve los bloques asignados cuando el volumen estaba en el tamaño máximo.|
| **14.** | Servidor de archivos  | Si un archivo en una carpeta tiene una secuencia de datos alternativa (anuncios) asociada, los anuncios no se realizan copias de seguridad o se restaura a través de recuperación, clonar y recuperación de nivel de elemento.| |


## <a name="next-step"></a>Siguiente paso

[Instalar la actualización de 0,3](storsimple-ova-install-update-01.md) en la matriz Virtual de StorSimple.

## <a name="references"></a>Referencias

¿Está buscando una nota de la versión anterior? Vete a: 

- [Notas de la versión de actualización de matriz Virtual StorSimple 0,1 y 0,2](storsimple-ova-update-01-release-notes.md)

- [Notas de la versión de disponibilidad General de la matriz Virtual de StorSimple](storsimple-ova-pp-release-notes.md)
 

