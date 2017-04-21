<properties 
   pageTitle="Notas de la versión de StorSimple 8000 Series actualización 2.2 | Microsoft Azure"
   description="Describe las características nuevas, problemas y soluciones alternativas para StorSimple 8000 Series actualización 2.2."
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
   ms.date="07/18/2016"
   ms.author="alkohli" />

# <a name="storsimple-8000-series-update-22-release-notes"></a>Notas de la versión de StorSimple 8000 Series actualización 2.2  

## <a name="overview"></a>Información general

Las notas siguientes describen las nuevas características e identifican los problemas de abrir críticos para StorSimple 8000 Series actualización 2.2. También contienen una lista de las actualizaciones de software de StorSimple incluidas en esta versión. 

Actualización 2.2 se puede aplicar a cualquier dispositivo StorSimple ejecutar versión (GA) o actualización de 0,1 2.1 de actualización. La versión de dispositivo asociada a 2.2 actualización es 6.3.9600.17708.

Revise la información contenida en las notas antes de implementar la actualización de la solución StorSimple.

>[AZURE.IMPORTANT]
> 
> - Actualización 2.2 tiene solo actualizaciones de software. Tarda aproximadamente 2 de 1,5 horas para instalar esta actualización. 

> - Si se están ejecutando 2.1 de actualización, le recomendamos que aplicar actualización 2.2 tan pronto como sea posible.

> - Para las nuevas versiones, quizás no pueda ver las actualizaciones inmediatamente porque haremos fases de las actualizaciones. Espere unos días y, a continuación, busque actualizaciones nuevamente como estos estará disponible pronto.


## <a name="whats-new-in-update-22"></a>Novedades de 2.2 de actualización

Se han realizado las siguientes mejoras claves en 2.2 de actualización.

 
- **Optimización de Reclamación de espacio automático** : cuando se eliminan datos en volúmenes thin aprovisionados, necesitan reclamar los bloques de almacenamiento sin usar. Esta versión ha mejorado el proceso de recuperación de espacio de la nube es el resultado en el espacio no usado que se encuentre disponible más rápido en comparación con las versiones anteriores.


- **Mejoras de rendimiento de instantánea** : actualización 2.2 ha mejorado el tiempo para procesar un instantáneas en ciertos escenarios donde grandes volúmenes se están usando y hay mínima o sin renovación de datos en la nube. Un escenario que podría beneficiarse de esta mejora sería volúmenes de archivo.


- **Recopilar información desde el paquete de consolidación de soporte** : se han realizado mejoras en la forma en el paquete de compatibilidad se reunió y cargado en esta versión. 


- **Mejoras en la confiabilidad de actualización** : esta versión tiene correcciones de errores que producen una mayor confiabilidad de actualización.

  
 

## <a name="issues-fixed-in-update-22"></a>Problemas corregidos en 2.2 de actualización

Las tablas siguientes se proporciona un resumen de los problemas corregidos en actualizaciones 2.2 y 2.1.    

| No | Característica                                    | Problema                                                                                                                                                                                                                                                                                        | Se aplica a dispositivo físico | Se aplica al dispositivo virtual |
|----|--------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------------------------|---------------------------|
| 1  | Rendimiento de host                      | En la versión anterior, se observan problemas de rendimiento del host durante la creación de un volumen localmente anclada y durante la conversión de un volumen en niveles a un volumen localmente anclada. Estos problemas se corrigen en esta versión de lo que resulta en una mejora el rendimiento de host durante los procedimientos de creación y conversión de volumen.                                                                        | Sí                        | No                        |
| 2  | Volúmenes localmente anclados                     | En algunas ocasiones, el sistema bloquee al crear un volumen localmente anclado. Se ha corregido en esta versión.                                                                                                                                                               | Sí                        | No                        |
| 3  | Interconexión                                    | Se han producido se bloquea cuando los metadatos de los dispositivos de nube StorSimple (8010 y 8020) niveles en la nube. Este problema se corrige en esta versión.                                                                                                                              | No                         | Sí                       |
| 4  | Creación de instantáneas                          | Hubo problemas relacionados con la creación de instantáneas incrementales en escenarios con grandes volúmenes y mínima o sin renovación de datos. Estos problemas se corrigen en esta versión.                                                                                                                 | Sí                        | Sí                       |
| 5  | Autenticación de Openstack                   | Cuando usa Openstack como el proveedor de servicios de nube, el usuario podría ejecutar en un error poco frecuente relacionado con la autenticación donde el analizador JSON ha generado un bloqueo. Este error se corrige en esta versión.                                                                                                                              | Sí                        | No                        |
| 6  | Copia del host                             | En versiones anteriores de software, un error poco frecuente relacionado con los intervalos de ODX se ha visto al copiar los datos desde un volumen a otro volumen. Esto daría como resultado un error de controlador y el sistema potencialmente podría ir al modo de recuperación. Este error se corrige en esta versión. | Sí                        | No       |
| 7  | Instrumentación de administración de Windows (WMI) | En las versiones anteriores de software, hay varias instancias del error de proxy de web con la excepción de "<ManagementException> error de carga del proveedor". Este error se atribuidos a una pérdida de memoria WMI y se ha resuelto.                                                               | Sí                        | No                        |
| 8  | Actualización                                     | En ciertas ocasiones en las versiones anteriores de software, el usuario ha recibido un "CisPowershellHcsscripterror" al intentar digitalizar o instalar actualizaciones. Este problema se corrige en esta versión.                                                                                        | Sí                        | Sí                       |
| 9  | Paquete de compatibilidad                            | En esta versión, ha habido mejoras en la manera en que se recopilan y cargar el paquete de compatibilidad.                                                                                                                                                                                                      | Sí                        | Sí                                    |


## <a name="known-issues-in-update-22"></a>Problemas conocidos de actualización 2.2

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
| 20 |Volúmenes localmente anclados | Si intenta convertir un volumen en niveles (creado y duplicado con actualización 1.2 o anterior) a un volumen localmente anclado y el dispositivo se está quedando sin espacio o hay una interrupción de la nube, la clone(s) puede estar dañado.| Este problema se produce sólo con volúmenes que se creen y duplicado con actualizar preliminar 2.1 software. Debe ser un escenario poco frecuente.|
| 21 | Conversión de volumen | No se actualizan lo ACRs conectado a un volumen mientras se realiza una conversión de volumen (niveles a anclados localmente o viceversa). Actualizar la ACRs podría causar daños en los datos. | Si es necesario, actualice el ACRs antes de la conversión de volumen y no realizar más actualizaciones ACR mientras la conversión está en curso. |

## <a name="controller-and-firmware-updates-in-update-22"></a>Actualizaciones de controlador y firmware en 2.2 de actualización

Esta versión tiene actualizaciones de software. Sin embargo, si está actualizando desde una versión anterior de la actualización 2, debe instalar controlador, Storport, Spaceport y (en algunos casos) las actualizaciones de firmware en el dispositivo de disco.
 
Para obtener más información sobre cómo instalar el controlador, Storport, Spaceport y las actualizaciones de firmware de disco, vea [instalar 2.2 de actualización](storsimple-install-update-21.md) en el dispositivo StorSimple.

 
## <a name="virtual-device-updates-in-update-22"></a>Actualizaciones de dispositivo virtual en 2.2 de actualización

Esta actualización no se puede aplicar al dispositivo virtual. Nuevos dispositivos virtuales tendrán que debe crearse. 

## <a name="next-step"></a>Siguiente paso

Obtenga información sobre cómo [instalar 2.2 de actualización](storsimple-install-update-21.md) en el dispositivo StorSimple.
