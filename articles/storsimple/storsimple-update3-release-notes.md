<properties 
   pageTitle="Notas de la versión de StorSimple 8000 Series actualización 3 | Microsoft Azure"
   description="Describe las características nuevas, problemas y soluciones alternativas para StorSimple 8000 Series actualización 3."
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
   ms.date="10/14/2016"
   ms.author="alkohli" />

# <a name="storsimple-8000-series-update-3-release-notes"></a>Notas de la versión de StorSimple 8000 Series actualización 3  

## <a name="overview"></a>Información general

Las notas siguientes describen las nuevas características e identifican los problemas de abrir críticos para StorSimple 8000 Series actualización 3. También contienen una lista de las actualizaciones de software de StorSimple incluidas en esta versión. 

Actualización 3 se puede aplicar a cualquier dispositivo StorSimple ejecutar versión (GA) o actualización de 0,1 2.2 de actualización. La versión de dispositivo asociada a actualización 3 es 6.3.9600.17759.

Revise la información contenida en las notas antes de implementar la actualización de la solución StorSimple.

>[AZURE.IMPORTANT]
> 
> - Actualización 3 tiene software del dispositivo, el controlador LSI y firmware y Storport y Spaceport actualizaciones. Tarda aproximadamente 2 de 1,5 horas para instalar esta actualización. 

> - Para las nuevas versiones, quizás no pueda ver las actualizaciones inmediatamente porque haremos fases de las actualizaciones. Espere unos días y, a continuación, busque actualizaciones nuevamente como estos estará disponible pronto.


## <a name="whats-new-in-update-3"></a>Novedades de actualización 3

Se han realizado las siguientes mejoras claves y correcciones de errores de actualización 3.

 
- **Cambios de Reclamación de espacio automático** : Iniciar actualización 3, los algoritmos de recuperación de espacio que se ejecute en el controlador de reserva del sistema de ejecución más rápida. Para obtener más información sobre los puertos que son necesarios para que funcione con la recuperación de espacio, consulte los [requisitos de red StorSimple](storsimple-system-requirements.md#networking-requirements-for-your-storsimple-device).

- **Mejoras en el rendimiento** : actualización 3 ha mejorado el rendimiento de lectura y escritura en la nube.

- **Mejoras de la migración** : en esta versión, varias correcciones de errores y mejoras se realizaron para la característica de migración desde dispositivos de serie 5000/7000 a los dispositivos de serie 8000. Para obtener más información sobre cómo usar la característica de migración, vaya a la [migración de 5000/7000 serie entre dispositivos 8000 series](https://www.microsoft.com/en-us/download/details.aspx?id=47322). 

- **Supervisión correcciones** : en esta versión, errores relacionados con la supervisión de gráficos, los paneles de servicio y paneles de dispositivo se fija.


## <a name="issues-fixed-in-update-3"></a>Problemas corregidos en actualización 3

Las tablas siguientes proporciona un resumen de los problemas que se corrigen en Update 3.    

| No | Característica                                    | Problema                                                                                                                                                                                                                                                                                        | Se aplica a dispositivo físico | Se aplica al dispositivo virtual |
|----|--------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------------------------|---------------------------|
| 1  | Migración de datos del lado del host                      | En la versión anterior, el dispositivo de nube StorSimple iba sin conexión durante una migración de datos del lado del host. Este problema se corrige en esta versión.                                                | No                        | Sí                        |
| 2  | Volúmenes localmente anclados                     | En la versión anterior, se han producido problemas relacionados con la i/OS errores, los errores de conversión de volumen y datapath para volúmenes anclados localmente. Estos problemas se han causado raíz y fijado en esta versión.                | Sí                        | No                       |
| 3  | Supervisión                                    | Hay varios problemas relacionados con la supervisión, así como los gráficos de dispositivo panel donde se muestra información incorrecta para volúmenes localmente anclados y unidades de generación de informes. Estos problemas se corrigen en esta versión. | Sí                         | No                       |
| 4  | Escribe gran i/OS                          | Cuando usa StorSimple para cargas de trabajo que implican escribe pesado, el usuario podría ejecutar en un error poco frecuente donde se ha niveles el conjunto de trabajo en la nube. Este error se corrige en esta versión. | Sí                        | Sí                       |
| 5  | Copia de seguridad                                     | En ciertas ocasiones en las versiones anteriores de software, cuando el usuario realiza una copia de seguridad de una copia remota, se produzcan errores de nube y la operación ' d error. En esta versión, se corrige el problema y la operación se completa correctamente.             | Sí                        | Sí                       |
| 6  | Directiva de copia de seguridad                                     | En ciertas ocasiones, en las versiones anteriores de software, se ha producido un error relacionado con la eliminación de directiva de copia de seguridad. Este problema se corrige en esta versión.       | Sí                        | Sí                       |



## <a name="known-issues-in-update-3"></a>Problemas conocidos de actualización 3

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
| 22 | Actualizaciones | Al aplicar actualización 3, la página de **Mantenimiento** en el portal de clásica Azure mostrará el siguiente mensaje relacionado con la actualización 2: "StorSimple de la serie 8000 actualización 2 incluye la capacidad de manera proactiva recopilar información de registro desde el dispositivo cuando se detectan posibles problemas". Esto puede resultar confuso ya que indica que el dispositivo se está actualizando a 2 de actualización. Después de que el dispositivo está succeesfully actualizado a actualización 3, desaparecerá este mensaje. | Este comportamiento se solucionará en una versión futura. | Sí | No |


## <a name="controller-and-firmware-updates-in-update-3"></a>Actualizaciones de controlador y firmware en actualización 3

Esta versión tiene LSI actualizaciones de controlador y firmware. Para obtener más información sobre cómo instalar las actualizaciones de firmware y controlador LSI, vea [instalar 3](storsimple-install-update-3.md) en el dispositivo StorSimple.

 
## <a name="virtual-device-updates-in-update-3"></a>Actualizaciones de dispositivo virtual en actualización 3

Esta actualización no se puede aplicar al equipo de nube StorSimple (también conocido como el dispositivo virtual). Nuevos dispositivos virtuales tendrán que debe crearse. 


## <a name="next-step"></a>Siguiente paso

Obtenga información sobre cómo [instalar la actualización 3](storsimple-install-update-3.md) en el dispositivo StorSimple.
