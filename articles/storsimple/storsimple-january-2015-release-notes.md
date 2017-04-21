<properties 
   pageTitle="Notas de la versión de 0,2 de actualización de 8000 StorSimple | Microsoft Azure"
   description="Describe las nuevas características y correcciones, problemas abiertos y alternativas disponibles de enero de 2015 versión de Microsoft Azure StorSimple (actualización de 0,2)."
   services="storsimple"
   documentationCenter="NA"
   authors="SharS"
   manager="carmonm"
   editor="" />
 <tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="05/16/2016"
   ms.author="v-sharos" />


# <a name="storsimple-8000-series-update-02-release-notes---january-2015"></a>Actualización de la serie 8000 StorSimple 0,2 notas - enero de 2015

## <a name="overview"></a>Información general

Las notas siguientes identifican los problemas de abrir críticos para la versión de enero de 2015 de Microsoft Azure StorSimple. También contienen una lista de las actualizaciones de software y firmware StorSimple incluidas en esta versión. Esta es la segunda versión después de la StorSimple 8000 Series versión disponible de forma general en julio de 2014.
  
Esta actualización no cambia la versión del software de dispositivo físico de la actualización de octubre. Continúa versión 6.3.9600.17312. La imagen de la imagen del dispositivo virtual ha cambiado en esta versión. Por lo tanto, todos los dispositivos virtuales nuevas creados después de 20/1/2015 mostrará la versión como 6.3.9600.17361.  

Revise la siguiente información contenida en las notas de la actualización de enero de 2015.

> [AZURE.IMPORTANT]  
>
>- Esta actualización no está disponible a través de Windows Update y no se puede instalar como otras actualizaciones. El dispositivo no recibirá esta actualización incluso si ha aplicado las actualizaciones a través del portal clásico Azure. Esta actualización sólo se aplicará a dispositivos virtuales creados después de 20 de enero de 2015. 
> 
>- La versión de enero de StorSimple no contiene las actualizaciones en el dispositivo físico StorSimple. Puede aplicar las actualizaciones de Windows disponibles para el dispositivo virtual, incluidas las revisiones de seguridad recientes, pero no podrá ver un cambio en la versión para el dispositivo físico StorSimple.

## <a name="whats-new-in-the-january-release"></a>Novedades de la versión de enero

Esta actualización contiene una revisión relacionada con los volúmenes trabajar sin conexión en el dispositivo virtual. (Consulte [problemas corrigen en esta versión](#issues-fixed-in-the-january-release)).  

La actualización no contiene nuevas características o funcionalidad.  

## <a name="issues-fixed-in-the-january-release"></a>Problemas corregidos en la versión de enero

La siguiente tabla describe el problema que se ha corregido en esta actualización.

|No.|Característica|Problema|Se aplica a dispositivo físico|Se aplica al dispositivo virtual 
|---|-------|-----|--------------------------|-------------------------
|1|Volúmenes trabajar sin conexión|Cuando se mantiene latencia alta nube varios minutos, los volúmenes de dispositivo virtual StorSimple trabajar sin conexión en los hosts. Esta revisión aumenta el umbral de latencia de la nube, reduciendo las situaciones que podría provocar que los volúmenes de desconectarse en hosts.|No|Sí  

## <a name="known-issues-in-the-january-release"></a>Problemas conocidos de la versión de enero

La tabla siguiente proporciona un resumen de los problemas conocidos en esta versión.
 
|No.|Característica|Problema|Comentarios o solución|Se aplica a dispositivo físico|Se aplica al dispositivo virtual 
|---|-------|-----|-------------------|--------------------------|-------------------------
|1| Restablecimiento de fábrica|  En algunos casos, cuando realiza un restablecimiento de fábrica, el dispositivo StorSimple pueden bloquearse y mostrar este mensaje: **Restablecer a fábrica está en curso (fase 8).** Esto ocurre si presiona CTRL + C mientras el cmdlet está en curso.| Presione CTRL + C después de iniciar un restablecimiento de fábrica. Si ya están en este estado, póngase en contacto con Microsoft Support para los pasos siguientes.|Sí|No|
|2|Quórum de disco| En algunas ocasiones, si se desconecta la mayoría de los discos en la caja EBOD de un dispositivo 8600 de ningún quórum de disco, a continuación, el bloque de almacenamiento estará sin conexión. Permanecerá sin conexión incluso si los discos se vuelven a conectar.|Debe reiniciar el dispositivo. Si el problema continúa, póngase en contacto con Microsoft Support para los pasos siguientes.|Sí |No
|3|Errores de nube instantánea|En algunas ocasiones, una instantánea de nube puede fallar con el error **se alcanzó el límite máximo copia de seguridad**. Esto ocurre si supera 255 clones en línea en el mismo dispositivo, desde el mismo volumen original que se ha eliminado.||Sí|Sí
|4|ID de controlador incorrecto|Cuando se realiza un reemplazo de controlador, controlador 0 puede mostrarse como controlador 1. Durante el reemplazo de controlador, cuando se cargue la imagen desde el nodo del mismo nivel, el identificador de controlador puede mostrarse inicialmente como el punto de Id.  En algunas ocasiones, este comportamiento también pueden aparecer después de reiniciar el sistema.|No se requiere ninguna acción del usuario. Se ocupará esta situación una vez completada la sustitución de controlador.|Sí|No 
|5| Dispositivo supervisión gráficos|En el servicio de administrador de StorSimple, los gráficos de supervisión de dispositivo no funcionan cuando básica o la autenticación NTLM está habilitada en la configuración del servidor proxy para el dispositivo.|Modificar la configuración de proxy web para el dispositivo registrado con el servicio de administrador de StorSimple para que la autenticación se establece en ninguno. Para ello, ejecute el cmdlet Set-HcsWebProxy StorSimple Windows PowerShell.|Sí|Sí
|6| Cuentas de almacenamiento|Usar el servicio de almacenamiento para eliminar la cuenta de almacenamiento es un escenario no compatible. Esto llevará a una situación en la que no se puede recuperar datos de usuario.|| Sí |  Sí
|7|Migración tras error de dispositivo| No se admite varias migraciones tras error de un contenedor de volumen desde el mismo dispositivo de origen a dispositivos de destino diferente.| Migración tras error de un único dispositivo inactivo en varios dispositivos hará que los contenedores de volumen en el primer error en dispositivo de perder la propiedad de los datos. Después de la este tipo de migración, estos contenedores de volumen aparecerán o tienen un comportamiento diferente cuando se ve en el portal de clásico de Azure.|Sí|No
|8| Instalación|Durante StorSimple adaptador de instalación de SharePoint, debe proporcionar una dirección IP de dispositivo en orden finalizar correctamente la instalación.||Sí|No
|9| Proxy Web|Si la configuración de proxy web tiene HTTPS como el protocolo especificado, se verá afectada el servicio de dispositivos de comunicación y se desconecta el dispositivo. También se generará paquetes de asistencia en el proceso, que consume recursos significativos en su dispositivo.|Asegúrese de que la dirección URL de proxy de web tiene HTTP como el protocolo especificado. Vea más información sobre cómo [Configurar proxy de web de su dispositivo](storsimple-configure-web-proxy.md).|Sí |No
|10|Proxy Web|  Si configura y habilitar a proxy web en un dispositivo registrado, deberá reiniciar el controlador activo en el dispositivo.|| Sí |No
|11|Alta carga de trabajo de i/OS y latencia alta nube|Cuando el dispositivo StorSimple encuentra una combinación de latencia de nube muy alta (orden de segundos) y carga de trabajo de i/OS alta, los volúmenes de dispositivo vaya a estado degradado y las i/OS pueden producir un error "el dispositivo no está listo".|Debe reiniciar los controladores de dispositivo o realizar una migración tras error de dispositivo para recuperar de esta situación manualmente.|Sí|No

## <a name="physical-device-updates-in-the-january-release"></a>Actualizaciones de dispositivo físico en enero versión

Esta actualización no contiene otros cambios en el dispositivo StorSimple.

## <a name="serial-attached-scsi-sas-controller-and-firmware-updates-in-the-january-release"></a>Suelte el controlador de SCSI (SA) conectadas en serie y actualizaciones de firmware en enero

Esta versión no contiene las actualizaciones para el firmware o el controlador de SCSI (SA) conectadas en serie. Actualizar el controlador estaba en la versión de 2014 de octubre. 

## <a name="virtual-device-updates-in-the-january-release"></a>Actualizaciones de dispositivo virtual en enero versión

Esta versión contiene una imagen actualizada del dispositivo virtual. Todos los dispositivos virtuales creados después de 20 de enero de 2015 mostrará la versión del software como 6.3.9600.17361.

 
