<properties 
    pageTitle="Notas de la versión 0.1 de actualización de 8000 StorSimple | Microsoft Azure"
    description="Describe las nuevas características y correcciones, problemas abiertos y alternativas disponibles para octubre de 2014 versión de Microsoft Azure StorSimple (actualización de 0,1)."
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
    ms.date="09/21/2016"
    ms.author="alkohli" />

# <a name="storsimple-8000-series-update-01-release-notes--october-2014"></a>Notas de la versión 0.1 de StorSimple 8000 Series actualización: octubre de 2014  

## <a name="overview"></a>Información general

Las notas siguientes identifican los problemas de abrir críticos para la actualización de la serie StorSimple 8000 0,1 publicado en octubre de 2014. También contienen una lista de las actualizaciones de software y firmware StorSimple incluidas en esta versión. Esta es la primera versión después de la StorSimple 8000 Series versión comenzó a estar disponible de forma general en julio de 2014 y corresponde a la versión 6.3.9600.17312.  

Le recomendamos que buscar y aplicar las actualizaciones disponibles inmediatamente después de instalar el dispositivo. También puede activar actualizaciones automáticas para descargar e instalar actualizaciones de alta prioridad de Microsoft tan pronto como se publiquen. Para obtener más información, consulte cómo [actualizar su dispositivo StorSimple](storsimple-update-device.md).  

Revise la información contenida en las notas antes de implementar las actualizaciones de la solución StorSimple.  

>[AZURE.IMPORTANT]
> 
-   Use el servicio StorSimple administrador y no de Windows PowerShell para StorSimple para instalar las actualizaciones de octubre.  
-   Las actualizaciones normalmente tardar unas 3 horas en completarse.  
-   La versión de octubre de StorSimple no contiene las actualizaciones al dispositivo virtual StorSimple. Puede aplicar cualquier disponibles las actualizaciones de Windows, incluyendo las revisiones de seguridad recientes, pero no podrá ver un cambio en la versión para el dispositivo virtual.  

Asegúrese de que se cumplen los siguientes requisitos previos antes de actualizar el dispositivo StorSimple.  

- Asegúrese de que se están ejecutando dos controladores de dispositivo antes de buscar actualizaciones. Si no se está ejecutando cualquier controlador, se producirá un error en el análisis. Para comprobar que los controladores están en buen estado, vaya a **Estado de Hardware** en la página **Mantenimiento** . Si hay componentes que **necesita atención**, póngase en contacto con Microsoft Support antes de continuar.  
- Asegúrese de que direcciones IP fijo para ambos controlador 0 y 1 de controlador son enrutables y pueden conectarse a Internet como se usan para atender las actualizaciones en el dispositivo. Puede usar el [cmdlet Probar conexión](https://technet.microsoft.com/library/hh849808.aspx) para hacer ping a una dirección conocida fuera de la red, como outlook.com, para comprobar que el controlador tiene conectividad con la red externa.  
- Asegúrese de que los puertos de salida necesarios están disponibles en el dispositivo StorSimple para las comunicaciones salientes. Para obtener más información, consulte los [requisitos de su dispositivo StorSimple de red](storsimple-system-requirements.md#networking-requirements-for-your-storsimple-device).  
- Si la versión del software de dispositivo es anterior a 6.3.9600.17312 (actualización de octubre de 2014), deshabilitar los puertos de datos 2 y 3 de datos, si está habilitado, antes de iniciar la actualización. Si deja el 2 de datos o datos 3 puertos habilitados para la aplicación de la actualización, puede hacer que el controlador de dispositivo ir al modo de recuperación. Tenga en cuenta que al deshabilitar las interfaces de red, todos los volúmenes asociados se desconectarán y las i/OS se pueda llegar durante la duración de la actualización.  

## <a name="whats-new-in-the-october-release"></a>Novedades de la versión de octubre

Esta actualización incluye las siguientes mejoras:

- Ahora puede usar el servicio de administrador de StorSimple interfaz de usuario para administrar los controladores de dispositivo. La administración de las acciones incluyen reiniciar, cierre, o activar un controlador. Para obtener más información, vaya a [Administrar StorSimple los controladores de dispositivos](storsimple-manage-device-controller.md).  
- Puede programar la asignación de ancho de banda WAN conforme a las combinaciones de día de la semana y la hora del día. Esto le permite hacer un mejor uso del ancho de banda WAN durante las horas. Plantillas de ancho de banda diferentes tienen permiso para contenedores de volumen diferente. Para obtener más información, vaya a [administrar las plantillas de ancho de banda StorSimple](storsimple-manage-bandwidth-templates.md).  
- Puede configurar notificaciones de correo electrónico de manera proactiva notificar a los administradores y otros usuarios de problemas que pueden resultar próximas o existentes. Para obtener más información, vaya a [configuración de alerta](storsimple-manage-alerts.md#configure-alert-settings).  

## <a name="issues-fixed-in-the-october-release"></a>Problemas corregidos en la versión de octubre


La tabla siguiente proporciona un resumen de los problemas corregidos en esta actualización.  

| No. | Característica | Problema | Se aplica a dispositivo físico | Se aplica al dispositivo virtual |
|-----|---------|-------|---------------------------------|--------------------------------|
| 1 | Interfaces de red | En la versión anterior, la interfaces de red datos 2 y 3 de datos se han cambiado en el software. Esto se ha corregido en esta actualización. Por favor, desactive la configuración y deshabilitar estas interfaces de red antes de instalar la actualización. Después de instalar la actualización, tendrá que volver a configurar estas interfaces. | Sí | No |
| 2 | Paquete de compatibilidad | En la versión anterior, si ejecutó el cmdlet de Windows PowerShell **HcsSupportPackage exportar** para recuperar los registros de controlador de administración de placa base (BMC), error en la operación con la siguiente advertencia: "la operación se realizó correctamente en este controlador, pero falló en el controlador de punto debido a los siguientes errores. Compruebe si el punto es correcto y si el nodo actual puede conectarse al interlocutor." Ahora se corrige este problema. | Sí | No |
| 3 | Migración tras error de dispositivo | En la versión anterior, se ha producido una oportunidad de incoherencia de datos si el error en un trabajo de **copia de seguridad de descubrir** durante un error del dispositivo. Ahora se corrige este problema. | Sí | No |
| 4 | Migración tras error de dispositivo | En la versión anterior, después de la migración de dispositivo, las copias de seguridad estaban visibles pero el contenedor de volumen asociados no presente en el dispositivo de destino. Ahora se corrige este problema. | Sí | No |
| 5 | Migración tras error de dispositivo | En la versión anterior, se ha producido un error en la enumeración de las copias de seguridad de la nube durante la operación de restauración de registro que podría la incoherencia de datos si hay problemas de conectividad de la nube. | Sí | No |
| 6 | Actualización del firmware | En la versión anterior, el trabajo de actualización de firmware de dispositivo error y muestra un error que indica que los cmdlets no eran reconocibles y que la actualización no como resultado. A continuación, fue el controlador en modo de recuperación. Ahora se corrige este problema. | Sí | No |
| 7 | Instalación | Se ha resuelto errores causados por el dispositivo no está una imagen correctamente durante la instalación. | Sí | No |
| 8 | Restablecimiento de fábrica | Ahora puede omitir opcionalmente la comprobación de firmware de restablecimiento de fábrica. Se trata de un cambio de la versión anterior. | Sí | No |
| 9 | Restablecimiento de fábrica | En la versión anterior, cuando se ejecuta un cmdlet de restablecimiento de fábrica, comprobación de la versión de firmware se realizaron solo para algunos componentes de hardware. Comprobaciones firmware adicionales realizadas después de reiniciar por primera vez en el proceso, que podría provocar el restablecimiento no se realice correctamente. Esta corrección garantiza que todas las comprobaciones de firmware se realizan cuando el generador de restablece cmdlet se ejecuta y antes de que el sistema primera reiniciar. | Sí | No |
| 10 | Rotación de clave de cuenta de almacenamiento | El cmdlet **Invocar HcsmServiceDataEncryptionKeyChange** usado para girar las teclas de la cuenta de almacenamiento ahora le pide al usuario que escriba la clave de cifrado de datos de servicio. Se trata de un cambio de la versión anterior en el que se pasó la clave de cifrado de datos de servicio como un parámetro en línea. | Sí | No |
| 11 | Recuperación dentro de 24 horas | Durante la recuperación, el Liberador de espacio en el dispositivo de origen no hubo recuperación más limpia, que provoca un error. Esto se ha corregido en esta versión. | Sí | No |

## <a name="known-issues-in-the-october-release"></a>Problemas conocidos de la versión de octubre

La tabla siguiente proporciona un resumen de los problemas conocidos en esta versión.

| No. | Característica | Problema | Comentarios o solución | Se aplica a dispositivo físico | Se aplica al dispositivo virtual |
|-----|---------|-------|----------------------------|----------------------------|---------------------------|
| 1 | Restablecimiento de fábrica | En algunos casos, cuando realiza un restablecimiento de fábrica, el dispositivo StorSimple pueden bloquearse y mostrar este mensaje: **Restablecer a fábrica está en curso (fase 8)**. Esto ocurre si presiona CTRL + C mientras el cmdlet está en curso. | Presione CTRL + C después de iniciar un restablecimiento de fábrica. Si ya están en este estado, póngase en contacto con Microsoft Support para los pasos siguientes. | Sí | No |
| 2 | Restablecimiento de fábrica | Hacer un dispositivo StorSimple que se actualiza de GA a octubre de 2014 de restablecimiento de fábrica no suelte. | Esta operación sólo funcionará si se instala una revisión. Póngase en contacto con Microsoft Support para obtener esta revisión necesaria. | Sí | No | 
| 3 | Quórum de disco | En algunas ocasiones, si se desconecta la mayoría de los discos en la caja EBOD de un dispositivo 8600 de ningún quórum de disco, a continuación, el bloque de almacenamiento estará sin conexión. Permanecerá sin conexión incluso si los discos se vuelven a conectar. | Debe reiniciar el dispositivo. Si el problema continúa, póngase en contacto con Microsoft Support para los pasos siguientes. | Sí | No |
| 4 | Errores de nube instantánea | En algunas ocasiones, una instantánea de nube puede fallar con el error **se alcanzó el límite máximo copia de seguridad**. Esto ocurre si supera 255 clones en línea en el mismo dispositivo, desde el mismo volumen original que se ha eliminado. | | Sí | Sí |
| 5 | ID de controlador incorrecto | Cuando se realiza un reemplazo de controlador, controlador 0 puede mostrarse como controlador 1. Durante el reemplazo de controlador, cuando se cargue la imagen desde el nodo del mismo nivel, el identificador de controlador puede mostrarse inicialmente como el punto de Id. En algunas ocasiones, este comportamiento también pueden aparecer después de reiniciar el sistema. |No se requiere ninguna acción del usuario. Se ocupará esta situación una vez completada la sustitución de controlador. | Sí | No |
| 6 | Dispositivo supervisión gráficos | En el servicio de administrador de StorSimple, los gráficos de supervisión de dispositivo no funcionan cuando básica o la autenticación NTLM está habilitada en la configuración del servidor proxy para el dispositivo. | Modificar la configuración de proxy web para el dispositivo registrado con el servicio de administrador de StorSimple para que la autenticación se establece en ninguno. Para ello, ejecute el cmdlet Set-HcsWebProxy StorSimple Windows PowerShell. | Sí | Sí |
| 7 | Cuentas de almacenamiento | Usar el servicio de almacenamiento para eliminar la cuenta de almacenamiento es un escenario no compatible. Esto llevará a una situación en la que no se puede recuperar datos de usuario. | | Sí | Sí |
| 8 | Migración tras error de dispositivo | No se admite varias migraciones tras error de un contenedor de volumen desde el mismo dispositivo de origen a dispositivos de destino diferente. | Migración tras error de un único dispositivo inactivo en varios dispositivos hará que los contenedores de volumen en el primer error en dispositivo de perder la propiedad de los datos. Después de la este tipo de migración, estos contenedores de volumen aparecerán o tienen un comportamiento diferente cuando se ve en el portal de clásico de Azure. | Sí | No |
| 9 | Instalación | Durante StorSimple adaptador de instalación de SharePoint, debe proporcionar una dirección IP de dispositivo en orden finalizar correctamente la instalación.    | | Sí | No |
| 10 | Proxy Web | Si la configuración de proxy web tiene HTTPS como el protocolo especificado, se verá afectada el servicio de dispositivos de comunicación y se desconecta el dispositivo. También se generará paquetes de asistencia en el proceso, que consume recursos significativos en su dispositivo. | Asegúrese de que la dirección URL de proxy de web tiene HTTP como el protocolo especificado. Más información sobre cómo [Configurar proxy de web de su dispositivo](storsimple-configure-web-proxy.md). | Sí | No |
| 11 | Proxy Web | Si configura y habilitar a proxy web en un dispositivo registrado, deberá reiniciar el controlador activo en el dispositivo. | | Sí | No |
| 12 | Alta carga de trabajo de i/OS y latencia alta nube | Cuando el dispositivo StorSimple encuentra una combinación de latencia de nube muy alta (orden de segundos) y carga de trabajo de i/OS alta, los volúmenes de dispositivo vaya a estado degradado y las i/OS pueden producir un error "el dispositivo no está listo". | Debe reiniciar los controladores de dispositivo o realizar una migración tras error de dispositivo para recuperar de esta situación manualmente. | Sí | No |

## <a name="physical-device-updates-in-the-october-release"></a>Dispositivo físico actualizaciones de octubre a la versión

Cuando estas actualizaciones se aplican a un dispositivo físico, la versión del software cambiará a 6.3.9600.17312. A menos que se indique lo contrario, estas notas se aplican a todos los modelos del dispositivo StorSimple. Para obtener más información acerca de estas actualizaciones, consulte [software de dispositivo físico de octubre de 2014 actualización para Microsoft Azure StorSimple dispositivo](http://support.microsoft.com/kb/2986997).  

## <a name="serial-attached-scsi-sas-controller-and-firmware-updates-in-the-october-release"></a>Suelte el controlador de SCSI (SA) conectadas en serie y actualizaciones de firmware en el octubre

Esta versión actualiza el controlador y el firmware en el controlador de SA del dispositivo físico. Para obtener más información sobre el controlador de SA actualizar, consulte [Actualizar para SA LSI controladores de dispositivo de Microsoft Azure StorSimple de octubre de 2014](http://support.microsoft.com/kb/2987020).   

Esta versión también aplica una actualización de firmware acumulativa que soluciona problemas de confiabilidad de los componentes de hardware del dispositivo. Para obtener más información acerca de la actualización de firmware, vea [firmware de octubre de 2014 actualización para Microsoft Azure StorSimple dispositivo](http://support.microsoft.com/kb/2987015).  

## <a name="virtual-device-updates-in-the-october-release"></a>Dispositivo virtual actualizaciones de octubre a la versión

Esta versión no contiene las actualizaciones para el dispositivo virtual. Aplicar esta actualización no cambiará la versión del software de un dispositivo virtual.
 
