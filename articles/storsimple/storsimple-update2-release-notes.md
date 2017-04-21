<properties 
   pageTitle="Notas de la versión 2 de actualización de StorSimple 8000 Series | Microsoft Azure"
   description="Describe las características nuevas, problemas y soluciones alternativas para StorSimple 8000 Series Update 2."
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
   ms.date="05/24/2016"
   ms.author="v-sharos" />

# <a name="storsimple-8000-series-update-2-release-notes"></a>Notas de la versión 2 de actualización de StorSimple 8000 Series  

## <a name="overview"></a>Información general

Las notas siguientes describen las nuevas características e identifican los problemas de abrir críticos para StorSimple 8000 Series actualización 2. También contienen una lista del software StorSimple, controlador y las actualizaciones de firmware de disco incluidas en esta versión. 

Actualización 2 se puede aplicar a cualquier dispositivo StorSimple ejecutando versión (GA) o actualización de 0,1 a través de la actualización 1.2. La versión de dispositivo asociada a la actualización 2 es 6.3.9600.17673.

Revise la información contenida en las notas antes de implementar la actualización de la solución StorSimple.

>[AZURE.IMPORTANT]
> 
- Tarda aproximadamente 7 de 4 horas para instalar esta actualización (incluidas las actualizaciones de Windows). 
- Actualización 2 tiene software, controlador LSI y actualizaciones de firmware SSD.
- Para las nuevas versiones, quizás no pueda ver las actualizaciones inmediatamente porque haremos fases de las actualizaciones. Espere unos días y, a continuación, busque actualizaciones nuevamente como estos estará disponible pronto.


## <a name="whats-new-in-update-2"></a>Novedades de la actualización 2

Actualización 2 presenta las siguientes características nuevas.

- **Localmente anclados volúmenes** : en versiones anteriores de la serie 8000 StorSimple, bloques de datos se niveles en la nube basada en uso. Se ha producido ninguna manera garantiza que los bloques quedaría en local. En la actualización 2, cuando se crea un volumen, puede designar un volumen como localmente anclados y principales de los datos de ese volumen no se en niveles en la nube. Instantáneas de volúmenes localmente anclados se copiarán en la nube para copia de seguridad para que la nube que puede usarse para fines de recuperación de desastres y movilidad de datos. Además, puede cambiar el tipo de volumen (es decir, convertir en niveles volúmenes a volúmenes localmente anclados y volúmenes de convertir localmente anclado a niveles). 

- **Mejoras de dispositivo virtual StorSimple** : anteriormente, la serie 8000 StorSimple sitúa el dispositivo virtual como una solución de desarrollo/prueba o recuperación de desastres. Se ha producido un solo modelo de dispositivo virtual (modelo 1100). Actualización 2 introduce dos modelos de dispositivo virtual: 

     - 8010 (anteriormente denominados la 1100): No cambia; tiene una capacidad de 30 TB y usa Azure almacenamiento estándar.
     - 8020 – tiene una capacidad de 64 TB y usa almacenamiento de Azure Premium para mejorar el rendimiento.

    Hay un disco duro virtual solo para ambos modelos de dispositivo virtual (8010/8020). Primera vez que inicie el dispositivo virtual, detecta los parámetros de plataforma y se aplica a la versión de modelo correcto.

- **Mejoras en la red** : 2 de actualización contiene las siguientes mejoras de red:

     - Varias NIC pueden estar habilitadas para la nube para que la migración tras error puede ocurrir si se produce un error en un NIC.
     - Mejoras de enrutamiento con métricas fijos de la nube habilitado bloques.
     - Reintentar en línea de recursos erróneos antes de un error.
     - Nuevas alertas de errores de servicio.

- **Mejoras en la actualización** : de actualización 1.2 y anterior, la serie 8000 StorSimple se ha actualizado a través de dos canales: Windows Update para Microsoft Update para archivos binarios y firmware, clústeres, iSCSI y así sucesivamente.
    Actualización 2 utiliza Microsoft Update para todos los paquetes de actualización. Se deben dirigir a menos tiempo en revisiones o realizando migraciones tras error. 

- **Actualizaciones de firmware** : las siguientes actualizaciones de firmware se incluyen:
    - LSI: lsi_sas2.sys versión del producto 2.00.72.10
    - Solo SSD (no hay actualizaciones de la unidad de disco duro): XMGG, XGEG, KZ50, F6C2 y VR08

- **Soporte técnico proactiva** : 2 de actualización permite a Microsoft extraer la información de diagnóstico adicional desde el dispositivo. Cuando nuestro equipo de operaciones identifica los dispositivos que están teniendo problemas, estamos mejor preparados para recopilar información desde el dispositivo y diagnóstico de problemas. **Aceptando 2 de actualización, usted nos permite proporcionar este soporte proactivo**.    
 

## <a name="issues-fixed-in-update-2"></a>Problemas corregidos en la actualización 2

Las tablas siguientes proporciona un resumen de los problemas corregidos en actualizaciones 2.    

| No. | Característica | Problema | Se aplica a dispositivo físico | Se aplica al dispositivo virtual |
|-----|---------|-------|--------------------------------|--------------------------------|
| 1 | Interfaces de red | Después de actualizar a la actualización de 1, el servicio Administrador de StorSimple informa que los puertos Data2 y Data3 falló en un controlador. Este problema se ha corregido. | Sí | No |
| 2 | Actualizaciones | Después de actualizar a 1 de actualización, se ha producido alertas de alarma audible en el portal de Azure clásico en varios dispositivos. Este problema se ha corregido. | Sí | No |
| 3 | Autenticación de Openstack | Cuando usa Openstack como el proveedor de servicios de nube, puede recibir un error que la cadena de autenticación de nube es demasiado larga. Esto se ha corregido. | Sí | No |


## <a name="known-issues-in-update-2"></a>Problemas conocidos de la actualización 2

La tabla siguiente proporciona un resumen de los problemas conocidos en esta versión.

| No. | Característica | Problema | Comentarios o solución | Se aplica a dispositivo físico | Se aplica al dispositivo virtual |
|-----|---------|-------|----------------------------|----------------------------|---------------------------|
| 1 | Quórum de disco | En algunas ocasiones, si se desconecta la mayoría de los discos en la caja EBOD de un dispositivo 8600 de ningún quórum de disco, a continuación, el bloque de almacenamiento pasará sin conexión. Permanecerá sin conexión incluso si los discos se vuelven a conectar. | Debe reiniciar el dispositivo. Si el problema continúa, póngase en contacto con Microsoft Support para los pasos siguientes. | Sí | No |
| 2 | ID de controlador incorrecto | Cuando se realiza un reemplazo de controlador, controlador 0 puede mostrarse como controlador 1. Durante el reemplazo de controlador, cuando se cargue la imagen desde el nodo del mismo nivel, el identificador de controlador puede mostrarse inicialmente como el punto de Id. En algunas ocasiones, este comportamiento también pueden aparecer después de reiniciar el sistema. | No se requiere ninguna acción del usuario. Se ocupará esta situación una vez completada la sustitución de controlador. | Sí | No |
| 3 | Cuentas de almacenamiento | Usar el servicio de almacenamiento para eliminar la cuenta de almacenamiento es un escenario no compatible. Esto llevará a una situación en la que no se puede recuperar datos de usuario.|  | Sí | Sí |
| 4 | Migración tras error de dispositivo | No se admite varias migraciones tras error de un contenedor de volumen desde el mismo dispositivo de origen a dispositivos de destino diferente. Migración tras error de un único dispositivo inactivo en varios dispositivos hará que los contenedores de volumen en el primer error en dispositivo de perder la propiedad de los datos. Después de la este tipo de migración, estos contenedores de volumen aparecerán o tienen un comportamiento diferente cuando se ve en el portal de clásico de Azure. | | Sí | No |
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
| 15 | Cmdlets de PowerShell Azure y volúmenes localmente anclados | No puede crear un volumen localmente anclado a través de los cmdlets de PowerShell de Azure. (Se interconectados cualquier volumen que se crea mediante PowerShell Azure.) |Usar siempre el servicio Administrador de StorSimple para configurar volúmenes anclados localmente.| Sí | No |
| 16 |Espacio disponible para volúmenes localmente anclados | Si elimina un volumen localmente anclado, puede que el espacio disponible para los nuevos volúmenes no se pueden actualizar inmediatamente. El servicio de administrador de StorSimple actualiza el espacio local disponible aproximadamente cada hora.| Espere una hora antes de intentar crear el nuevo volumen. | Sí | No |
| 17 | Volúmenes localmente anclados | El trabajo de restauración expone la copia de seguridad de instantánea temporal en el catálogo de copia de seguridad, pero solo para la duración de la tarea de restauración. Además, expone un grupo de disco virtual con el prefijo **tmpCollection** en la página **Directivas de copia de seguridad** , pero solo para la duración de la tarea de restauración. | Esto puede ocurrir si el trabajo de restauración solo localmente ha anclado volúmenes o una mezcla de volúmenes localmente anclados y en niveles. Si el trabajo de restauración incluye solo en niveles volúmenes, no se producirá este comportamiento. Intervención del usuario no es necesaria. | Sí | No |
| 18 | Volúmenes localmente anclados | Si cancela un trabajo de restauración y un controlador de error se produce inmediatamente después, el trabajo de restauración mostrará el **error** en lugar de **cancelado**. Si se produce un error en un trabajo de restauración y se produce un error de controlador inmediatamente después, el trabajo de restauración mostrará **cancelado** en lugar de **error**. | Esto puede ocurrir si el trabajo de restauración solo localmente ha anclado volúmenes o una mezcla de volúmenes localmente anclados y en niveles. Si el trabajo de restauración incluye solo en niveles volúmenes, no se producirá este comportamiento. Intervención del usuario no es necesaria. | Sí | No |
| 19 |Volúmenes localmente anclados | Si se cancela un trabajo de restauración o si se produce un error en una restauración y, a continuación, se produce un error de controlador, un trabajo de restauración adicional aparece en la página de **trabajos** . | Esto puede ocurrir si el trabajo de restauración solo localmente ha anclado volúmenes o una mezcla de volúmenes localmente anclados y en niveles. Si el trabajo de restauración incluye solo en niveles volúmenes, no se producirá este comportamiento. Intervención del usuario no es necesaria. | Sí | No |
| 20 |Volúmenes localmente anclados | Si intenta convertir un volumen en niveles (creado y duplicado con actualización 1.2 o anterior) a un volumen localmente anclado y el dispositivo se está quedando sin espacio o hay una interrupción de la nube, la clone(s) puede estar dañado.| Este problema se produce sólo con volúmenes que estaban software creado y duplicado con anteriores a la actualización 2. Debe ser un escenario poco frecuente.|
| 21 | Conversión de volumen | No se actualizan lo ACRs conectado a un volumen mientras se realiza una conversión de volumen (niveles a anclados localmente o viceversa). Actualizar la ACRs podría causar daños en los datos. | Si es necesario, actualice el ACRs antes de la conversión de volumen y no realizar más actualizaciones ACR mientras la conversión está en curso. |

## <a name="controller-and-firmware-updates-in-update-2"></a>Actualizaciones de controlador y firmware en Update 2

Esta versión actualiza el controlador y el firmware del disco en el dispositivo.
 
- Para obtener más información sobre el firmware LSI actualización, consulte el artículo de Microsoft Knowledge base 3121900. 
- Para obtener más información sobre el firmware disco actualización, consulte el artículo de Microsoft Knowledge base 3121899.
 
## <a name="virtual-device-updates-in-update-2"></a>Actualizaciones de dispositivo virtual en Update 2

Esta actualización no se puede aplicar al dispositivo virtual. Nuevos dispositivos virtuales tendrán que debe crearse. 

## <a name="next-step"></a>Siguiente paso

Obtenga información sobre cómo [instalar la actualización 2](storsimple-install-update-2.md) en el dispositivo StorSimple.
