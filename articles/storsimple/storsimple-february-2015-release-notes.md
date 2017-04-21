<properties 
   pageTitle="Notas de la versión 0,3 de actualización de 8000 StorSimple | Microsoft Azure"
   description="Describe las nuevas características y correcciones, problemas abiertos y alternativas disponibles de febrero de 2015 versión de Microsoft Azure StorSimple (actualización de 0,3)."
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
   ms.date="04/18/2016"
   ms.author="v-sharos" />

# <a name="storsimple-8000-series-update-03-release-notes---february-2015"></a>Actualización de la serie 8000 StorSimple 0,3 notas - de febrero de 2015

## <a name="overview"></a>Información general

Las notas siguientes identifican los problemas de abrir críticos para la actualización de la serie StorSimple 8000 0,3 publicado en febrero de 2015. También contienen una lista de las actualizaciones de software y firmware StorSimple incluidas en esta versión. Esta es la tercera versión después de la StorSimple 8000 Series versión disponible de forma general en julio de 2014.
  
Esta actualización no cambia la versión del software de dispositivo de la actualización de enero. Continúa versión 6.3.9600.17312. Para confirmar que la actualización se ha instalado activando la fecha de **Última actualización** . Si la fecha es 2/10/2015 o posterior, la actualización se ha instalado correctamente.  

Le recomendamos que buscar y aplicar las actualizaciones disponibles inmediatamente después de instalar el dispositivo de StorSimple. También puede activar actualizaciones automáticas para descargar e instalar actualizaciones de alta prioridad de Microsoft tan pronto como se publiquen. Para obtener más información, vea [actualizar su dispositivo StorSimple](storsimple-update-device.md).  

Revise la información contenida en las notas antes de implementar la actualización de la solución StorSimple.  

>[AZURE.IMPORTANT]   
>
> - Usar el servicio StorSimple administrador y no de Windows PowerShell para StorSimple para instalar la actualización de febrero.   
> - Se tarda aproximadamente una hora para instalar esta actualización. Sin embargo, si va a instalar las actualizaciones acumulativas, el proceso puede tardar unas 3 horas para completar.  
> - La versión de febrero de StorSimple no contiene las actualizaciones al dispositivo virtual StorSimple. Puede aplicar las actualizaciones de Windows disponibles para el dispositivo virtual, incluidas las revisiones de seguridad recientes, pero no podrá ver un cambio en la versión para el dispositivo virtual.  

Asegúrese de que se cumplen los siguientes requisitos previos antes de actualizar el dispositivo StorSimple.  

- Asegúrese de que se están ejecutando dos controladores de dispositivo antes de buscar actualizaciones. Si no se está ejecutando cualquier controlador, se producirá un error en el análisis. Para comprobar que los controladores están en buen estado, vaya a **Estado de Hardware** en la página **Mantenimiento** . Si hay componentes que **necesita atención**, póngase en contacto con Microsoft Support antes de continuar.
- Asegúrese de que fija direcciones IP de controlador de 0 y 1 del controlador son enrutable y puede conectarse a Internet como se usan para atender las actualizaciones en el dispositivo. Puede usar el [cmdlet Probar conexión](https://technet.microsoft.com/library/hh849808.aspx) para hacer ping a una dirección conocida fuera de la red, como outlook.com, para comprobar que el controlador tiene conectividad con la red externa.
- Asegúrese de que los puertos 80 y 443 están disponibles en el dispositivo StorSimple para las comunicaciones salientes. Para obtener más información, consulte los [requisitos de su dispositivo StorSimple de red](storsimple-system-requirements.md#networking-requirements-for-your-storsimple-device).
- Si la versión del software de dispositivo es anterior a 6.3.9600.17312 (actualización de octubre de 2014), deshabilitar los puertos de datos 2 y 3 de datos, si está habilitado, antes de iniciar la actualización. Salir de puertos habilitados al aplicar la actualización de los datos 2 o 3 de datos puede provocar que el controlador de dispositivo ir al modo de recuperación. Tenga en cuenta que al deshabilitar las interfaces de red, todos los volúmenes asociados se desconectarán y las i/OS se pueda llegar durante la duración de la actualización.  
  
## <a name="whats-new-in-the-february-release"></a>Novedades de la versión de febrero

Esta actualización contiene una solución fábrica Restablecer problemas que ocurrieron en dispositivos que habían se han actualizado desde la GA suelte a la versión de octubre de 2014. Para obtener más información, vea [problemas corrigen en esta versión](#issues-fixed-in-the-february-release).   

Esta actualización no contiene nuevas características o funcionalidad.  

## <a name="issues-fixed-in-the-february-release"></a>Problemas corregidos en la versión de febrero

La siguiente tabla describe el problema que se ha corregido en esta actualización.  
 
| No. | Característica | Problema | Se aplica a dispositivo físico | Se aplica al dispositivo virtual |
|-----|---------|-------|---------------------------------|-------------------------------|
| 1 | Restablecimiento de fábrica | Intente realizar una fábrica restablecer en un dispositivo originalmente con la versión de GA (versión 6.3.9600.17215) instalada pero no se ha actualizado a la octubre versión (6.3.9600.17312). El generador de restablece correctamente y el dispositivo deja de ser estable. | Sí | No |


## <a name="known-issues-in-the-february-release"></a>Problemas conocidos de la versión de febrero

La tabla siguiente proporciona un resumen de los problemas conocidos en esta versión.
 
| No. | Característica | Problema | Comentarios o solución | Se aplica a dispositivo físico  | Se aplica al dispositivo virtual |
|-----|---------|-------|----------------------------|-----------------------------|--------------------------|
| 1 | Restablecimiento de fábrica | En algunos casos, cuando realiza un restablecimiento de fábrica, el dispositivo StorSimple pueden bloquearse y mostrar este mensaje: **Restablecer a fábrica está en curso (fase 8)**. Esto ocurre si presiona CTRL + C mientras el cmdlet está en curso. | Presione CTRL + C después de iniciar un restablecimiento de fábrica. Si ya están en este estado, póngase en contacto con Microsoft Support para los pasos siguientes. | Sí | No |
| 2 | Quórum de disco | En algunas ocasiones, si se desconecta la mayoría de los discos en la caja EBOD de un 8600device de ningún quórum de disco, a continuación, el bloque de almacenamiento estará sin conexión. Permanecerá sin conexión incluso si los discos se vuelven a conectar. | Debe reiniciar el dispositivo. Si el problema continúa, póngase en contacto con Microsoft Support para los pasos siguientes. | Sí | No |
| 3 | Errores de nube instantánea | En algunas ocasiones, una instantánea de nube puede fallar con el error **se alcanzó el límite máximo copia de seguridad**. Esto ocurre si supera 255 clones en línea en el mismo dispositivo, desde el mismo volumen original que se ha eliminado. |  | Sí | Sí |
| 4 | ID de controlador incorrecto | Cuando se realiza un reemplazo de controlador, controlador 0 puede mostrarse como controlador 1. Durante el reemplazo de controlador, cuando se cargue la imagen desde el nodo del mismo nivel, el identificador de controlador puede mostrarse inicialmente como el punto de Id. En algunas ocasiones, este comportamiento también pueden aparecer después de reiniciar el sistema. | No se requiere ninguna acción del usuario. Se ocupará esta situación una vez completada la sustitución de controlador. | Sí | No |
| 5 | Dispositivo supervisión gráficos | En el servicio de administrador de StorSimple, los gráficos de supervisión de dispositivo no funcionan cuando básica o la autenticación NTLM está habilitada en la configuración del servidor proxy para el dispositivo. | Modificar la configuración de proxy web para el dispositivo registrado con el servicio de administrador de StorSimple para que la autenticación se establece en ninguno. Para ello, ejecute el cmdlet Set-HcsWebProxy StorSimple Windows PowerShell. | Sí | Sí |
| 6 | Cuentas de almacenamiento | Usar el servicio de almacenamiento para eliminar la cuenta de almacenamiento es un escenario no compatible. Esto llevará a una situación en la que no se puede recuperar datos de usuario. |  | Sí | Sí |
| 7 | Migración tras error de dispositivo | No se admite varias migraciones tras error de un contenedor de volumen desde el mismo dispositivo de origen a dispositivos de destino diferente.  Migración tras error de un único dispositivo inactivo en varios dispositivos hará que los contenedores de volumen en el primer error en dispositivo de perder la propiedad de los datos. Después de la este tipo de migración, estos contenedores de volumen aparecerán o tienen un comportamiento diferente cuando se ve en el portal de clásico de Azure. |   | Sí | No |
| 8 | Instalación | Durante StorSimple adaptador de instalación de SharePoint, debe proporcionar una dirección IP de dispositivo en orden finalizar correctamente la instalación. |  | Sí | No |
| 9 | Proxy Web | Si la configuración de proxy web tiene HTTPS como el protocolo especificado, se verá afectada el servicio de dispositivos de comunicación y se desconecta el dispositivo. También se generará paquetes de asistencia en el proceso, que consume recursos significativos en su dispositivo. | Asegúrese de que la dirección URL de proxy de web tiene HTTP como el protocolo especificado. Más información sobre cómo [Configurar proxy de web de su dispositivo](storsimple-configure-web-proxy.md). | Sí | No |
| 10 | Proxy Web | Si configura y habilitar a proxy web en un dispositivo registrado, deberá reiniciar el controlador activo en el dispositivo. |  | Sí | No |
| 11 | Alta carga de trabajo de i/OS y latencia alta nube | Cuando el dispositivo StorSimple encuentra una combinación de latencia de nube muy alta (orden de segundos) y carga de trabajo de i/OS alta, los volúmenes de dispositivo vaya a estado degradado y las i/OS pueden producir un error "el dispositivo no está listo". | Debe reiniciar los controladores de dispositivo o realizar una migración tras error de dispositivo para recuperar de esta situación manualmente. | Sí | No |

## <a name="physical-device-updates-in-the-february-release"></a>Actualizaciones de dispositivo físico en el febrero versión

Esta actualización soluciona el problema de restablecimiento de fábrica que ocurrieron en dispositivos que habían se han actualizado desde la GA suelte a la versión de octubre de 2014. No contiene otras actualizaciones para el dispositivo StorSimple.  

## <a name="serial-attached-scsi-sas-controller-and-firmware-updates-in-the-february-release"></a>Controlador de SCSI (SA) conectadas en serie y actualizaciones de firmware en la de febrero de lanzamiento

Esta versión no contiene las actualizaciones para el firmware o el controlador de SCSI (SA) conectadas en serie. Actualizar el controlador estaba en la versión de 2014 de octubre.  

## <a name="virtual-device-updates-in-the-february-release"></a>Actualizaciones de dispositivo virtual en febrero de la versión

Esta versión no contiene las actualizaciones para el dispositivo virtual. Aplicar esta actualización no cambiará la versión del software de un dispositivo virtual.
 
