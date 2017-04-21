<properties
   pageTitle="Información general de la matriz Virtual de StorSimple | Microsoft Azure"
   description="Describe la matriz Virtual StorSimple, una solución de almacenamiento integrada que administra las tareas de almacenamiento entre un dispositivo virtual de local y el almacenamiento de nube de Microsoft Azure."
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
   ms.date="10/06/2016"
   ms.author="alkohli" />

# <a name="introduction-to-the-storsimple-virtual-array"></a>Introducción a la matriz Virtual StorSimple

## <a name="overview"></a>Información general

Bienvenido a la Microsoft Azure StorSimple Virtual matriz, una solución de almacenamiento integrada que administra las tareas de almacenamiento entre un dispositivo virtual local ejecuta en un hipervisor y el almacenamiento de nube de Microsoft Azure. La matriz Virtual (también conocido como el StorSimple local dispositivo virtual) es una solución de servidor de iSCSI que elimina muchos de los problemas y los gastos asociados con protección de datos y almacenamiento de enterprise o el servidor de archivos eficaz, rentable y fácil de administrar. La matriz Virtual es especialmente adecuada para escenarios de remotas y sucursales (ROBO).

Esta información general se centra en la matriz Virtual. 

- Para obtener información general de la serie 8000 StorSimple, vaya a [serie 8000 StorSimple: una solución de nube híbrida](storsimple-overview.md). 

- Para obtener información sobre el dispositivo de la serie StorSimple 5000/7000, vaya a [La Ayuda en pantalla StorSimple](http://onlinehelp.storsimple.com/).

La matriz Virtual es compatible con el protocolo de bloque de mensajes del servidor (SMB) o iSCSI. Se ejecuta en la infraestructura de hipervisor y proporciona interconexión con la nube, nube copia de seguridad, restaurar rápida, recuperación de nivel de elemento y características de recuperación de desastres.

La siguiente tabla resume las características importantes de la matriz Virtual.

| Característica | Matriz virtual |
| ------- | ------------- |
|Requisitos de instalación | Usa la infraestructura de virtualización (Hyper-V o sus)|
| Disponibilidad | Nodo único |
| Capacidad total (incluida la nube) |Hasta 64 TB de capacidad utilizable por dispositivo virtual |
| Capacidad local | 390 GB a 6,4 TB de capacidad utilizable por dispositivo virtual (debe aprovisionar a 8 TB de 500 GB de espacio en disco)|
| Protocolos nativos | iSCSI o SMB |
| Objetivo de tiempo de recuperación (RTO) | iSCSI: menos de 2 minutos, independientemente de su tamaño |
| Objetivo de punto de recuperación (RPO) | Copias de seguridad diarias y a petición |
| Niveles de almacenamiento | Usos de calor asignación para determinar qué datos se deben niveles o alejar |
| Soporte técnico | Compatible con el proveedor de infraestructura de virtualización |
| Rendimiento | Varía dependiendo de la infraestructura subyacente |
| Movilidad de datos | Puede restaurar en el mismo dispositivo o el nivel de elemento recuperación (servidor de archivos) |
| Niveles de almacenamiento | Almacenamiento de hipervisor local y la nube |
| Tamaño de compartir |En niveles: hasta 20 TB. localmente anclados: hasta 2 TB |
| Tamaño de volumen |En niveles: hasta 5 TB; localmente anclados: hasta 500 GB |
| Instantáneas | Bloqueo coherente |
| Recuperación de nivel de elemento | Sí; los usuarios pueden restaurar desde los recursos compartidos |

## <a name="why-use-storsimple"></a>¿Por qué usar StorSimple?

StorSimple se conecta usuarios y servidores de almacenamiento de Azure en minutos, sin modificar de la aplicación.

En la tabla siguiente se describe algunas de las principales ventajas que ofrece la solución de matriz Virtual.

| Característica | Beneficio |
|---------|---------|
| Integración transparente | Es compatible con la matriz Virtual iSCSI o el protocolo SMB. El movimiento de datos entre el nivel local y la nube es integrado y transparente para el usuario.|
| Costos de almacenamiento reducida | Con StorSimple, proporcionando suficiente espacio de almacenamiento local para satisfacer las necesidades actuales de los datos de acceso rápidos más usadas. Como crecen las necesidades de almacenamiento, almacenamiento de información cloud StorSimple niveles fríos datos rentables. Los datos se desduplicados y comprimidos antes de enviar a la nube para reducir aún más los gastos y los requisitos de almacenamiento.|
| Administración del almacenamiento simplificada | StorSimple proporciona administración centralizada en la nube mediante StorSimple Manager para administrar varios dispositivos.| 
| Recuperación mejorada y cumplimiento | StorSimple facilita la recuperación más rápida restaurando los metadatos inmediatamente y restaurar los datos según sea necesario. Esto significa que pueden continuar las operaciones normales con interrupción mínima.|
| Movilidad de datos | Niveles de datos en la nube puede tener acceso desde otros sitios con fines de recuperación y migración. Tenga en cuenta que puede restaurar datos solo en la matriz Virtual original. Sin embargo, use las funciones de recuperación de desastres para restaurar toda la matriz Virtual a otra matriz Virtual.|

## <a name="storsimple-workload-summary"></a>Carga de trabajo de StorSimple resumen

Un resumen de las cargas de trabajo de StorSimple compatibles se incluyen debajo.

| Escenario                | Carga de trabajo              | Compatibles |  Restricciones                                  | Versión              |
|-------------------------|-----------------------|-----------|------------------------------------------------|----------------------|
| Colaboración de ROBO      | Uso compartido de archivos          | Sí       | Vea [los límites máximos del servidor de archivos](storsimple-ova-limits.md). <br>Consulte [requisitos del sistema para las versiones SMB compatibles](storsimple-ova-system-requirements.md).   | Todas las versiones      |


## <a name="workflows"></a>Flujos de trabajo

La matriz Virtual StorSimple es especialmente adecuada para los flujos de trabajo siguientes:

- [Administración de almacenamiento basada en nube](#cloud-based-storage-management)
- [Independiente de la ubicación de copia de seguridad](#location-independent-backup)
- [Recuperación de desastres y protección de datos](#data-protection-and-disaster-recovery)

### <a name="cloud-based-storage-management"></a>Administración de almacenamiento basada en nube

Puede usar el servicio de StorSimple Manager que se ejecuta en el portal de clásico Azure para administrar los datos almacenados en varios dispositivos y en varias ubicaciones. Esto es especialmente útil en escenarios de sucursales distribuidas. Tenga en cuenta que debe crear instancias independientes del servicio de administrador de StorSimple para administrar matrices virtuales y dispositivos de StorSimple físicos. 

![administración de almacenamiento basada en nube](./media/storsimple-ova-overview/cloud-based-storage-management.png)

### <a name="location-independent-backup"></a>Independiente de la ubicación de copia de seguridad

Con la matriz Virtual, instantáneas de nube ofrecen una copia independiente de la ubicación en el momento de un volumen o recurso compartido. Instantáneas de nube habilitadas de forma predeterminada y no se puede deshabilitar. Todos los volúmenes y recursos compartidos son copia de seguridad al mismo tiempo a través de una única directiva de copia de seguridad diaria y puede realizar copias de seguridad de ad hoc adicionales cuando sea necesario.

### <a name="data-protection-and-disaster-recovery"></a>Recuperación de desastres y protección de datos

La matriz Virtual es compatible con los siguientes datos protección y desastres recuperación escenarios:

- **Restaurar volumen o recurso compartido** : usar la restauración como el nuevo flujo de trabajo para recuperar un volumen o recurso compartido. Use este método para recuperar el volumen completo o compartir.
- **Recuperación de nivel de elemento** : recursos compartidos permiten acceso simplificado a las copias de seguridad recientes. Fácilmente puede recuperar un archivo individual de una carpeta de .backup especiales disponible en la nube. Esta función de restauración está condicionada por el usuario e intervención del administrador no es necesaria.
- **Recuperación** : usar la capacidad de migración tras error para recuperar todos los volúmenes o recursos compartidos en una nueva matriz Virtual. Crear la nueva matriz Virtual y registrar con el servicio de administrador de StorSimple y luego conmutar la matriz Virtual original. La nueva matriz Virtual supondrá, a continuación, los recursos de preparación. 

## <a name="virtual-array-components"></a>Componentes de matriz virtual.

La matriz Virtual incluye los siguientes componentes:

- [Matriz virtual](#virtual-array) : un dispositivo de almacenamiento de nube híbrida basado en una máquina virtual que se aprovisione en su entorno virtualizado o hipervisor.  
- [Servicio Administrador de StorSimple](#storsimple-manager-service) : extensión del portal clásica Azure que le permite administrar uno o varios dispositivos StorSimple desde una interfaz web único que puede tener acceso desde distintas ubicaciones geográficas. Puede usar el servicio Administrador de StorSimple para crear y administrar servicios, ver y administrar dispositivos y las alertas y administrar volúmenes, recursos compartidos e instantáneas existentes.
- [Interfaz de usuario de web local](#local-web-user-interface) : una interfaz de usuario basada en web que se utiliza para configurar el dispositivo de modo que puede conectarse a la red local y, a continuación, registrar el dispositivo con el servicio Administrador de StorSimple. 
- [Interfaz de línea de comandos](#command-line-interface) : una interfaz de Windows PowerShell que puede usar para iniciar una sesión de asistencia en la matriz Virtual.
Las secciones siguientes describen cada uno de estos componentes con más detalle y explican cómo la solución organiza los datos, asigna el almacenamiento y facilita la administración de almacenamiento y protección de datos.

### <a name="virtual-array"></a>Matriz virtual

La matriz Virtual es una solución de almacenamiento de nodo único que proporciona almacenamiento principal, administra la comunicación con el almacenamiento de la nube y ayuda a garantizar la seguridad y confidencialidad de todos los datos que se almacena en el dispositivo.

La matriz Virtual está disponible en un modelo que está disponible para su descarga. La matriz de almacenamiento tiene una capacidad máxima de 6,4 TB en el dispositivo (con un requisito de almacenamiento subyacente de 8 TB) y 64 TB incluidos almacenamiento en la nube. 

La matriz Virtual tiene las siguientes características:

- Es rentable. Se utiliza la infraestructura de virtualización existente y se puede implementar en su hipervisor Hyper-V o sus existente.
- Se encuentra en el centro de datos y pueden configurarse como un servidor de iSCSI o un archivo. 
- Se integra con la nube.
- Realizar copias de seguridad se almacenan en la nube, que puede facilitar la recuperación y simplificar la recuperación de nivel de elemento (ILR). 
- Puede aplicar actualizaciones a la matriz Virtual, igual que desea aplicar a un dispositivo físico.

>[AZURE.NOTE] No se puede expandir una matriz Virtual. Por lo tanto, es importante aprovisionar almacenamiento adecuado al crear el dispositivo virtual. 

### <a name="storsimple-manager-service"></a>Servicio de administrador de StorSimple

Microsoft Azure StorSimple proporciona una interfaz de usuario de basada en web (el servicio de administrador de StorSimple) que permite administrar el centro de datos y almacenamiento de nube de forma centralizada. Puede usar el servicio Administrador de StorSimple para realizar las siguientes tareas:

- Administrar varios matrices Virtual de StorSimple desde un único servicio. 
- Configurar y administrar la configuración de seguridad para dispositivos StorSimple. (Cifrado en la nube depende de la API de Microsoft Azure).
- Configurar las propiedades y las credenciales de cuenta de almacenamiento.
- Configurar y administrar volúmenes o recursos compartidos.
- Realizar copias de seguridad y restaurar datos en volúmenes o recursos compartidos.
- Supervisar el rendimiento.
- Revisar la configuración del sistema e identificar posibles problemas.

Usar el servicio de administrador de StorSimple para realizar la administración diaria de la matriz Virtual.

Para obtener más información, vaya a [usar el servicio de administrador de StorSimple para administrar el dispositivo de StorSimple](storsimple-manager-service-administration.md).

### <a name="local-web-user-interface"></a>Interfaz de usuario de web local

La matriz Virtual incluye una interfaz de usuario basada en web que se usa para el registro del dispositivo con el servicio Administrador de StorSimple y configuración de una sola vez. Puede usarlo para apagar y reiniciar la matriz Virtual, ejecutar pruebas de diagnóstico, actualizar el software, cambie la contraseña de administrador de dispositivos, ver los registros de sistema y póngase en contacto con Microsoft Support para abrir una solicitud de servicio. 

Para obtener información sobre el uso de la interfaz de usuario basada en web, vaya a [usar la interfaz de usuario basada en web para administrar la matriz Virtual de StorSimple](storsimple-ova-web-ui-admin.md).

### <a name="command-line-interface"></a>Interfaz de línea de comandos

La interfaz de Windows PowerShell incluye le permite iniciar una sesión de soporte técnico con Microsoft Support para que pueden ayudarle a solucionar problemas y resolver los problemas que surjan en el dispositivo virtual.

## <a name="storage-management-technologies"></a>Tecnologías de administración de almacenamiento

Además de la matriz Virtual y otros componentes, la solución de StorSimple usa las siguientes tecnologías de software para proporcionar acceso rápido a datos importantes, reducir el consumo de almacenamiento y proteger los datos almacenados en la matriz Virtual:

- [Niveles de almacenamiento automático](#automatic-storage-tiering) 
- [Volúmenes y recursos compartidos localmente anclados](#locally-pinned-shares-and-volumes)
- [Desduplicación y la compresión de datos en niveles o una copia en la nube](#deduplication-and-compression-for-data-tiered/backed-up-to-the-cloud) 
- [Copias de seguridad programadas y a petición](#scheduled-and-on-demand-backups)

### <a name="automatic-storage-tiering"></a>Niveles de almacenamiento automático

La matriz Virtual usa un nuevo mecanismo de interconexión para administrar los datos almacenados en la matriz Virtual y la nube. Existen dos niveles: almacenamiento de nube de la matriz Virtual y Azure local. La matriz Virtual StorSimple organiza automáticamente los datos en los niveles basados en un mapa de calor que realiza un seguimiento de uso actual, edad y relaciones con otros datos. Datos más activos (populares) se almacenan localmente, aunque menos los datos activos e inactivos se migran automáticamente a la nube. (Todas las copias de seguridad se almacenan en la nube). StorSimple ajusta y vuelve a organizar datos y cambiar las asignaciones de almacenamiento como patrones de uso. Por ejemplo, alguna información podría ser menos activa con el tiempo. A medida que cada vez menos activo, está en niveles fuera en la nube. Si los mismos datos vuelve a estar activos, está en niveles de a la matriz de almacenamiento.

Datos de un determinado compartir en niveles o volumen se garantiza su propio espacio de nivel local. (aproximadamente 10% del total aprovisiona espacio para ese recurso compartido o volumen). Mientras Esto reduce el almacenamiento disponible en el dispositivo virtual para ese recurso compartido o volumen, garantiza que interconexión para un recurso compartido o volumen no se verán afectada por las necesidades de niveles de otros recursos compartidos o volúmenes. Por lo tanto una carga de trabajo muy ocupado en un recurso compartido o volumen no puede forzar todas las otras cargas de trabajo en la nube. 

![niveles de almacenamiento automático](./media/storsimple-ova-overview/automatic-storage-tiering.png)

>[AZURE.NOTE] Puede especificar un volumen localmente anclado, en cuyo caso los datos permanecerán en la matriz Virtual y nunca es niveles en la nube. Para obtener más información, vaya a [localmente anclados volúmenes y recursos compartidos](#locally-pinned-shares-and-volumes).

### <a name="locally-pinned-shares-and-volumes"></a>Volúmenes y recursos compartidos localmente anclados

Puede crear elementos compartidos y volúmenes anclados localmente. Esta función garantiza que los datos necesarios para que las aplicaciones críticas permanecen en la matriz Virtual y nunca es niveles en la nube. Volúmenes y recursos compartidos localmente anclados tienen las siguientes características: 

- No están sujetos a problemas de conectividad o de latencia de la nube.
- Estas benefician aún de características de recuperación de desastres y copia de seguridad para la nube de StorSimple.

Puede restaurar un recurso compartido de localmente anclado o volumen como niveles o en niveles o anclados volumen localmente. 

Para obtener más información acerca de los volúmenes localmente anclados, vaya a [usar el servicio de administrador de StorSimple para administrar volúmenes](storsimple-manage-volumes-u2.md).

### <a name="deduplication-and-compression-for-data-tiered-or-backed-up-to-the-cloud"></a>Desduplicación y la compresión de datos en niveles o una copia en la nube

StorSimple usa desduplicación y compresión de datos para reducir aún más los requisitos de almacenamiento en la nube. La deduplicación reduce la cantidad total de datos almacenados eliminando redundancia en el conjunto de datos almacenado. Cuando se pasa la información, StorSimple pasa por alto los datos sin cambios y captura sólo los cambios. Además, StorSimple reduce la cantidad de datos almacenados identificando y quitar información duplicada. 

>[AZURE.NOTE] Datos almacenados en la matriz Virtual no es desduplicados o comprimir. Todo desduplicación y compresión se produce justo antes de que los datos se envían a la nube.

### <a name="scheduled-and-on-demand-backups"></a>Copias de seguridad programadas y a petición

Características de protección de datos de StorSimple le permiten crear copias de seguridad a petición. Además, una programación de copia de seguridad predeterminada garantiza que se copia diaria de datos. Se realizan copias de seguridad en el formulario de instantáneas incrementales, que se almacenan en la nube. Instantáneas que grabar sólo los cambios desde la última copia, pueden crearse y restaurar rápidamente. Estas instantáneas pueden ser muy importantes en escenarios de recuperación de desastres porque reemplazar sistemas de almacenamiento secundaria (como copia de seguridad de la cinta) y le permiten restaurar los datos a su centro de datos o a sitios alternativos si es necesario.

## <a name="next-steps"></a>Pasos siguientes

Obtenga información sobre cómo [Preparar el portal de la matriz Virtual](storsimple-ova-deploy1-portal-prep.md).


