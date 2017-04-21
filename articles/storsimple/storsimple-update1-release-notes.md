<properties 
   pageTitle="Notas de la versión 1.2 de actualización de StorSimple 8000 Series | Microsoft Azure"
   description="Describe las características nuevas, problemas y soluciones alternativas para StorSimple 8000 Series actualización 1.2."
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

# <a name="storsimple-8000-series-update-12-release-notes"></a>Notas de la versión 1.2 de actualización de StorSimple 8000 Series  

## <a name="overview"></a>Información general

Las notas siguientes describen las nuevas características e identifican los problemas de abrir críticos para StorSimple 8000 Series actualización 1.2. También contienen una lista de software, controladores y actualizaciones de firmware de disco incluidas en esta versión de StorSimple. 

Actualización 1.2 se puede aplicar a cualquier dispositivo StorSimple ejecutando versión (GA), actualización 0,1, actualización 0,2 o software de actualización 0,3. Actualización 1.2 no está disponible si está ejecutando el dispositivo 1 Update o actualización 1.1. Si el dispositivo se está ejecutando una versión (GA), ponte [en contacto con soporte técnico de Microsoft](storsimple-contact-microsoft-support.md) para ayudarle con la instalación de esta actualización.

La siguiente tabla enumeran las versiones de software de dispositivo correspondiente a las actualizaciones de 1, 1.1 y 1.2.

| Si ejecuta la actualización... | Esta es la versión del software de dispositivo. |
|---------------------|---------------------------------------|
| Actualizar 1.2          | 6.3.9600.17584                        |
| Actualizar 1.1          | 6.3.9600.17521                        |
| Actualizar 1.0          | 6.3.9600.17491                        |

Revise la información contenida en las notas antes de implementar la actualización de la solución StorSimple. Para obtener más información, consulte cómo [instalar la actualización 1.2 en su dispositivo StorSimple](storsimple-install-update-1.md). 

>[AZURE.IMPORTANT]
> 
- Tarda aproximadamente 5-10 horas para instalar esta actualización (incluidas las actualizaciones de Windows). 
- Actualización 1.2 tiene software, controlador LSI y actualizaciones de firmware de disco. Para instalar, siga las instrucciones de [instalar la actualización 1.2 en su dispositivo StorSimple](storsimple-install-update-1.md).
- Para las nuevas versiones, quizás no pueda ver las actualizaciones inmediatamente porque haremos fases de las actualizaciones. Buscar actualizaciones en unos días nuevamente como estos estará disponible próximamente.


## <a name="whats-new-in-update-12"></a>Novedades de la actualización 1.2

Estas características se han publicado en primer lugar con 1 actualización disponible para un conjunto limitado de usuarios. Con la versión 1.2 de actualización, la mayoría de los usuarios StorSimple verá las siguientes características nuevas y mejoras:

- **Migración de 5000-7000 serie a los dispositivos de serie 8000** : esta versión presenta una nueva característica de migración que permite la StorSimple 5000-7000 serie dispositivo a los usuarios migrar sus datos a un dispositivo físico de serie 8000 StorSimple o un dispositivo virtual. La característica de migración tiene dos propuestas de valor de clave:                                                                  

    - **Continuidad empresarial**, por lo que permite la migración de los datos existentes en equipos de la serie 5000-7000 a dispositivos 8000 serie.
    - **Ofertas de característica mejorada de los dispositivos de serie 8000**, como eficaz administración centralizada de varios dispositivos a través del servicio de administrador de StorSimple, mejor clase de hardware y actualiza el firmware, accesorios virtuales, movilidad de datos y características de la estrategia futura.

    Consulte la [Guía de migración](http://www.microsoft.com/download/details.aspx?id=47322) para obtener más información sobre cómo migrar una serie de 5000-7000 StorSimple a un dispositivo de la serie 8000. 

- **Disponibilidad en el Portal de administración pública de Azure** : StorSimple está ahora disponible en el portal de administración pública de Azure. Vea cómo [implementar un dispositivo StorSimple en el Portal de administración pública de Azure](storsimple-deployment-walkthrough-gov.md).

- **Compatibilidad con otros proveedores de servicios de nube** : los otros proveedores de servicios de nube compatibles son Amazon S3, Amazon S3 con RR, HP y OpenStack (beta).

- **Actualización más reciente de las API de almacenamiento** : con esta versión, StorSimple se ha actualizado a la API del servicio de almacenamiento de Azure más reciente. Dispositivos de serie 8000 StorSimple que ejecutan versiones anteriores a la actualización 1 del software (versión, 0,1, 0,2 y 0,3) usan versiones de las API de servicio de almacenamiento de Azure anteriores a 17 de julio de 2009. Como se indica en el [anuncio de supresión de versiones de servicio de almacenamiento](http://blogs.msdn.com/b/windowsazurestorage/archive/2015/10/19/microsoft-azure-storage-service-version-removal-update-extension-to-2016.aspx)de actualizado, por el 1 de agosto de 2016, estas API quedará obsoleta. Es fundamental que aplique StorSimple 8000 Series Update 1 antes del 1 de agosto de 2016. Si no puede hacerlo, StorSimple dispositivos dejará de funcionar correctamente.

- **Soporte técnico para el almacenamiento redundantes de zona (ZRS)** : con la actualización a la última versión de la API de almacenamiento de la serie 8000 StorSimple admitirá almacenamiento redundantes de zona (ZRS) además de almacenamiento redundantes localmente (LRS) y almacenamiento Geo redundantes (GRS). Consulte este [artículo sobre opciones de redundancia de almacenamiento de Azure](../storage/storage-redundancy.md) para obtener información detallada ZRS.

- **Mejora la experiencia de implementación y actualización inicial** : en esta versión, la instalación y procesos de actualización se han mejorado. La instalación mediante el Asistente de configuración se ha mejorado para proporcionar comentarios al usuario si la configuración de red y la configuración del firewall es incorrecto. Ha proporcionado cmdlets adicionales de diagnósticos para ayudarle con la solución de problemas de red del dispositivo. Vea el [artículo de implementación de solución de problemas](storsimple-troubleshoot-deployment.md) para obtener más información acerca de los cmdlets de diagnósticos nuevos usarse para solucionar problemas.

## <a name="issues-fixed-in-update-12"></a>Problemas corregidos en la actualización 1.2

La tabla siguiente proporciona un resumen de los problemas corregidos en actualizaciones 1.2, 1.1 y 1.    


| No. | Característica | Problema | Resueltos en la actualización | Se aplica a dispositivo físico | Se aplica al dispositivo virtual |
|-----|---------|-------|-----------------|---------------------------------|--------------------------------|
| 1 | Windows PowerShell para StorSimple | Cuando un usuario tener acceso remoto el dispositivo StorSimple con Windows PowerShell para StorSimple y, a continuación, inicie al Asistente de configuración, un bloqueo se produjo tan pronto como datos 0 entrada de IP. Ahora está corregido en la actualización 1. | Actualización 1 | Sí | Sí |
| 2 | Restablecimiento de fábrica | En algunos casos, cuando realiza un restablecimiento de fábrica, el dispositivo StorSimple estuvo detiene y aparece este mensaje: **Restablecer a fábrica está en curso (fase 8)**. Esto ha ocurrido si presiona CTRL + C mientras el cmdlet estaba en curso. Ahora está corregido.| Actualización 1 | Sí | No |
| 3 | Restablecimiento de fábrica | Después de restablece un generador de controlador de doble error, que se hayan permiso para continuar con el registro del dispositivo. Esto ha generado una configuración de sistema no admitidas. Actualización de 1, se muestra un mensaje de error y registro se bloquea en un dispositivo que se restablece un generador de error. | Actualización 1 | Sí | No |
| 4 | Restablecimiento de fábrica | En algunos casos, se han generado alertas de error de coincidencia falso positivo. Ya no se generará alertas no coinciden incorrecto en dispositivos que ejecutan 1 de actualización. | Actualización 1 | Sí | No |
| 5 | Restablecimiento de fábrica | Si el restablecimiento de fábrica se interrumpió antes de finalizar, el dispositivo en modo de recuperación y no le permite acceder a Windows PowerShell para StorSimple. Ahora está corregido. | Actualización 1 | Sí | No |
| 6 | Recuperación | Ha corregido un error de recuperación (DR) de desastres donde DR sería un error durante la detección de copias de seguridad en el dispositivo de destino. | Actualización 1 | Sí | Sí |
| 7 | Indicadores de supervisión | En algunos casos, supervisión LED al final del dispositivo no indicó estado correcto. Se ha desactivado el LED azul. DATOS 0 y datos 1 LED parpadean incluso cuando estas interfaces no están configuradas. El problema se ha corregido y supervisión LED ahora indica el estado correcto.  | Actualización 1 | Sí | No |
| 8 | Indicadores de supervisión | En algunos casos, después de aplicar la actualización de 1, la luz azul en el controlador de active desactivada por lo que resulta difícil identificar el controlador de active. Este problema se ha corregido en esta versión de revisión.| Actualizar 1.2 | Sí | No |
| 9 | Interfaces de red | En versiones anteriores, podría desconectarse de un dispositivo StorSimple configurado con una puerta de enlace no enrutables. En esta versión, ha sido la métrica de enrutamiento de datos 0 realizado los valores más bajos; por lo tanto, incluso si otras interfaces de red están habilitados para la nube, todo el tráfico de nube desde el dispositivo se se enrutan a través de datos 0. | Actualización 1 | Sí | Sí | 
| 10 | Realizar copias de seguridad | Una actualización de 1 que se produjo un error después de 24 días de copias de seguridad se ha corregido en la versión 1.1 de actualización de revisión. | Actualizar 1.1 | Sí | Sí |
| 11 | Realizar copias de seguridad | Un error en las versiones anteriores da como resultado un rendimiento deficiente de instantáneas de nube con tasas de cambio baja. Se ha corregido en esta versión de revisión.| Actualizar 1.2 | Sí | Sí |
| 12 | Actualizaciones | Una actualización de 1 que informa de un error de actualización y causado los controladores ir al modo de recuperación, se ha corregido en esta versión de revisión.| Actualizar 1.2 | Sí | Sí |


## <a name="known-issues-in-update-12"></a>Problemas conocidos de actualización 1.2

La tabla siguiente proporciona un resumen de los problemas conocidos en esta versión.

| No. | Característica | Problema | Comentarios o solución | Se aplica a dispositivo físico | Se aplica al dispositivo virtual |
|-----|---------|-------|----------------------------|----------------------------|---------------------------|
| 1 | Quórum de disco | En algunas ocasiones, si se desconecta la mayoría de los discos en la caja EBOD de un dispositivo 8600 de ningún quórum de disco, a continuación, el bloque de almacenamiento estará sin conexión. Permanecerá sin conexión incluso si los discos se vuelven a conectar. | Debe reiniciar el dispositivo. Si el problema continúa, póngase en contacto con Microsoft Support para los pasos siguientes. | Sí | No |
| 2 | ID de controlador incorrecto | Cuando se realiza un reemplazo de controlador, controlador 0 puede mostrarse como controlador 1. Durante el reemplazo de controlador, cuando se cargue la imagen desde el nodo del mismo nivel, el identificador de controlador puede mostrarse inicialmente como el punto de Id. En algunas ocasiones, este comportamiento también pueden aparecer después de reiniciar el sistema. | No se requiere ninguna acción del usuario. Se ocupará esta situación una vez completada la sustitución de controlador. | Sí | No |
| 3 | Cuentas de almacenamiento | Usar el servicio de almacenamiento para eliminar la cuenta de almacenamiento es un escenario no compatible. Esto llevará a una situación en la que no se puede recuperar datos de usuario. | Sí | Sí |
| 4 | Migración tras error de dispositivo | No se admite varias migraciones tras error de un contenedor de volumen desde el mismo dispositivo de origen a dispositivos de destino diferente. Dispositivo la migración tras error de un único dispositivo inactivo en varios dispositivos hará que los contenedores de volumen en el primer error en dispositivo de perder la propiedad de los datos. Después de la este tipo de migración, estos contenedores de volumen aparecerán o tienen un comportamiento diferente cuando se ve en el portal de clásico de Azure. | | Sí | No |
| 5 | Instalación | Durante StorSimple adaptador de instalación de SharePoint, debe proporcionar una dirección IP de dispositivo en orden finalizar correctamente la instalación.    | | Sí | No |
| 6 | Proxy Web | Si la configuración de proxy web tiene HTTPS como el protocolo especificado, se verá afectada el servicio de dispositivos de comunicación y se desconecta el dispositivo. También se generará paquetes de asistencia en el proceso, que consume recursos significativos en su dispositivo. | Asegúrese de que la dirección URL de proxy de web tiene HTTP como el protocolo especificado. Para obtener más información, vaya a [Configurar proxy de web de su dispositivo](storsimple-configure-web-proxy.md). | Sí | No |
| 7 | Proxy Web | Si configura y habilitar a proxy web en un dispositivo registrado, deberá reiniciar el controlador activo en el dispositivo. | | Sí | No |
| 8 | Alta carga de trabajo de i/OS y latencia alta nube | Cuando el dispositivo StorSimple encuentra una combinación de latencia de nube muy alta (orden de segundos) y carga de trabajo de i/OS alta, los volúmenes de dispositivo vaya a estado degradado y las i/OS pueden producir un error "el dispositivo no está listo". | Debe reiniciar los controladores de dispositivo o realizar una migración tras error de dispositivo para recuperar de esta situación manualmente. | Sí | No |
| 9 | Azure PowerShell | Cuándo usar el cmdlet StorSimple **Get AzureStorSimpleStorageAccountCredential & #124; Select-Object - espera primero 1 -** para seleccionar el primer objeto para que pueda crear un nuevo objeto **VolumeContainer** , el cmdlet devuelve todos los objetos. | Ajustar el cmdlet entre paréntesis como sigue: **(Get-Azure-StorSimpleStorageAccountCredential) & #124; Select-Object - espera primero 1 -** | Sí | Sí |
| 10| Migración | Cuando se pasan varios contenedores de volumen para la migración, es preciso solo para el primer contenedor de volumen el ETA para copia de seguridad más reciente. Además, migración paralela se inicia después de que se migran las copias de seguridad de 4 en primer lugar en el primer contenedor de volumen. | Se recomienda migrar un contenedor de volumen a la vez. | Sí | No |
| 11| Migración | Después de la restauración volúmenes no se agregan a la directiva de copia de seguridad o el grupo de disco virtual. | Debe agregar estos volúmenes a una directiva de copia de seguridad para crear copias de seguridad. | Sí | Sí |
| 12| Migración | Una vez completada la migración, el dispositivo de la serie 5000/7000 no debe tener acceso a los contenedores de datos migrados. | Se recomienda eliminar los contenedores de datos migrados después de la migración completa y confirmada. | Sí | No |
| 13| Clonar y DR | Un dispositivo StorSimple ejecutando 1 de actualización no puede clonar o realizar recuperación a un dispositivo anteriores a la actualización de software 1. | Debe actualizar el dispositivo de destino a actualizar 1 para permitir que estas operaciones | Sí | Sí |
| 14 | Migración | Copia de seguridad de configuración para la migración puede fallar en un dispositivo de serie 5000-7000 cuando hay grupos de volumen sin volúmenes asociado. | Eliminar todos los grupos de volumen vacío sin volúmenes asociados y vuelva a intentar la copia de seguridad de la configuración.| Sí | No |

## <a name="physical-device-updates-in-update-12"></a>Actualizaciones de dispositivo físico en actualización 1.2

Si actualiza revisión 1.2 se aplica a un dispositivo físico (con versiones anteriores Update 1), la versión del software cambiará a 6.3.9600.17584.

## <a name="controller-and-firmware-updates-in-update-12"></a>Actualizaciones de controlador y firmware en actualización 1.2

Esta versión actualiza el controlador y el firmware del disco en el dispositivo.
 
- Para obtener más información acerca de la actualización de controlador de SA, vea [actualizar 1 para los controladores de LSI SA en Microsoft Azure StorSimple dispositivo](https://support.microsoft.com/kb/3043005). 

- Para obtener más información sobre el firmware disco actualización, consulte [firmware Update 1 para Microsoft Azure StorSimple dispositivo del disco](https://support.microsoft.com/kb/3063416).
 
## <a name="virtual-device-updates-in-update-12"></a>Actualizaciones de dispositivo virtual en actualización 1.2

Esta actualización no se puede aplicar al dispositivo virtual. Nuevos dispositivos virtuales tendrán que debe crearse. 

## <a name="next-steps"></a>Pasos siguientes

- [Instalar la actualización 1.2 en su dispositivo](storsimple-install-update-1.md).
 
