<properties 
   pageTitle="¿Qué es StorSimple? | Microsoft Azure" 
   description="Describe niveles StorSimple, el dispositivo, dispositivo virtual, servicios y administración de almacenamiento y presenta términos clave usados en StorSimple." 
   services="storsimple" 
   documentationCenter="NA" 
   authors="SharS" 
   manager="carmonm" 
   editor=""/>

<tags
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD" 
   ms.date="10/05/2016"
   ms.author="v-sharos@microsoft.com"/>

# <a name="storsimple-8000-series-a-hybrid-cloud-storage-solution"></a>Serie 8000 StorSimple: una solución de almacenamiento de nube híbrida

## <a name="overview"></a>Información general

Bienvenido a Microsoft Azure StorSimple, almacenamiento integrado administra las tareas de almacenamiento de información entre dispositivos locales y el almacenamiento de nube de Microsoft Azure. StorSimple es una solución de red (SAN) de área de almacenamiento eficaz, rentable y fácil de administrar que elimina muchos de los problemas y los gastos asociados con enterprise almacenamiento y protección de datos. Usa el dispositivo de la serie 8000 StorSimple propietario, se integra con servicios en la nube y proporciona un conjunto de herramientas de administración para una vista transparente de todo el almacenamiento de empresa, incluido el almacenamiento de la nube. (La información de implementación de StorSimple publicada en el sitio Web de Microsoft Azure se aplica a solo los dispositivos de serie 8000 StorSimple. Si está utilizando un dispositivo de la serie StorSimple 5000/7000, vaya a [Ayuda StorSimple](http://onlinehelp.storsimple.com/).)

StorSimple usa [niveles de almacenamiento](#automatic-storage-tiering) para administrar los datos almacenados en diversos medios de almacenamiento. El conjunto de trabajo actual es almacenan localmente en unidades de estado sólido (SSD), los datos que se usan con menos frecuencia se almacenan en unidades de disco duro (disco duro) y datos archivados se insertan en la nube. Además, StorSimple utiliza desduplicación y compresión para reducir la cantidad de almacenamiento que consume los datos. Para obtener más información, vaya a [desduplicación y compresión](#deduplication-and-compression). Para obtener definiciones de otros términos y conceptos clave utilizados en la documentación de la serie 8000 StorSimple, vaya a [StorSimple terminología](#storsimple-terminology) al final de este artículo.

Con la actualización StorSimple 2, puede identificar volúmenes como *localmente anclados* para garantizar que los datos principales permanezcan locales en el dispositivo y no de nivel en la nube. Esto le permite ejecutar cargas de trabajo que dependen de latencia de la nube, como SQL y máquina virtual cargas de trabajo, en volúmenes anclados localmente, sin dejar de usar la nube para copia de seguridad. Para obtener más información acerca de los volúmenes anclados localmente, consulte [usar el servicio de administrador de StorSimple para administrar volúmenes](storsimple-manage-volumes-u2.md). 

Actualización 2 también le permite crear dispositivos virtuales StorSimple aprovechan las ventajas de la baja latencia y alto rendimiento que proporciona almacenamiento de Azure premium. Para obtener más información acerca de los dispositivos de StorSimple premium virtuales, consulte [implementar y administrar un dispositivo virtual StorSimple en Azure](storsimple-virtual-device-u2.md). Para obtener más información sobre el almacenamiento de Azure premium, vaya a [almacenamiento Premium: almacenamiento de alto rendimiento para cargas de trabajo de máquina Virtual de Azure](../storage/storage-premium-storage.md).

Además de la administración de almacenamiento, características de protección de datos de StorSimple le permiten crear a petición programado copias de seguridad y, a continuación, almacenarlos localmente o en la nube. Se realizan copias de seguridad en el formulario de instantáneas incrementales, lo que significa que pueden crearse y restaurar rápidamente. Instantáneas de nube pueden ser muy importantes en escenarios de recuperación de desastres porque reemplazar sistemas de almacenamiento secundaria (como copia de seguridad de la cinta) y le permiten restaurar los datos a su centro de datos o a sitios alternativos si es necesario.

![icono de vídeo](./media/storsimple-overview/video_icon.png) Vea el vídeo para obtener una introducción rápida a Microsoft Azure StorSimple.

> [AZURE.VIDEO storsimple-hybrid-cloud-storage-solution]

## <a name="why-use-storsimple"></a>¿Por qué usar StorSimple?

En la tabla siguiente se describe algunas de las principales ventajas que ofrece Microsoft Azure StorSimple.

| Característica | Beneficio |
|---------|---------|
|Integración transparente | Microsoft Azure StorSimple usa el protocolo iSCSI invisible vincular las instalaciones de almacenamiento de datos. Esto garantiza que los datos almacenados en la nube, en el centro de datos, o en servidores remotos parece almacenarse en una única ubicación.|
|Costos de almacenamiento reducida|Microsoft Azure StorSimple asigna suficiente local o almacenamiento de la nube para satisfacer las necesidades actuales y amplía el almacenamiento de nube solo cuando sea necesario. Más reduce requisitos de almacenamiento y gastos eliminando versiones redundantes de los mismos datos (desduplicación) y mediante la compresión.|
|Administración del almacenamiento simplificada|Microsoft Azure StorSimple proporciona el sistema de herramientas de administración que puede usar para configurar y administrar los datos almacenados en local, en un servidor remoto y en la nube. Además, puede administrar la copia de seguridad y restaurar funciones desde un complemento de consola de administración de Microsoft (MMC). StorSimple ofrece una interfaz opcional independiente que puede utilizar para extender servicios de protección de datos y administración StorSimple al contenido almacenado en servidores de SharePoint. |
|Recuperación mejorada y cumplimiento|StorSimple de Microsoft Azure no requiere tiempo de recuperación ampliada. En su lugar, restaura datos según sea necesario. Esto significa que pueden continuar las operaciones normales con interrupción mínima. Además, puede configurar directivas para especificar las programaciones de copia de seguridad y retención de datos.
|Movilidad de datos|Pueden tener acceso a datos cargados en servicios de nube de Microsoft Azure de otros sitios con fines de recuperación y migración. Además, puede usar StorSimple para configurar los dispositivos virtuales StorSimple en máquinas virtuales (VM) que se ejecutan en Microsoft Azure. Las máquinas virtuales, a continuación, pueden usar dispositivos virtuales para tener acceso a los datos almacenados con fines de pruebas o recuperación.|
|Compatibilidad con otros proveedores de servicios de nube |La serie 8000 StorSimple con el software de actualización 1 o posterior admite Amazon S3 con RR, HP y OpenStack cloud services, así como Microsoft Azure. (Se necesita una cuenta de Microsoft Azure almacenamiento para fines de administración de dispositivos.) Para obtener más información, vaya a [Novedades de actualización 1.2](storsimple-update1-release-notes.md#whats-new-in-update-12).|
|Continuidad empresarial | Actualización 1 o posterior proporciona una nueva característica de migración que permite a los usuarios de la serie de 5000-7000 migrar sus datos a un dispositivo de la serie 8000 StorSimple StorSimple.|
|Disponibilidad en el Portal de administración pública de Azure | Actualización de StorSimple 1 o posterior está disponible en el Portal de administración pública de Azure. Para obtener más información, vea [implementar el dispositivo de StorSimple local en el Portal de administración pública](storsimple-deployment-walkthrough-gov.md).|
|Disponibilidad y protección de datos | La serie 8000 StorSimple con actualización 1 o posterior es compatible con la zona redundantes almacenamiento (ZRS), además de almacenamiento de almacenamiento redundantes localmente (LRS) y Geo redundantes (GRS). Consulte [este artículo sobre las opciones de redundancia de almacenamiento de Azure](https://azure.microsoft.com/documentation/articles/storage-redundancy/) para obtener información detallada ZRS.|
|Soporte técnico para aplicaciones críticas | Con la actualización StorSimple 2, puede identificar volúmenes anclados localmente. Esta función garantiza que los datos necesarios para que las aplicaciones críticas no es niveles en la nube. Volúmenes localmente anclados no están sujetos a problemas de conectividad o de latencia de la nube. Para obtener más información acerca de los volúmenes anclados localmente, consulte [usar el servicio de administrador de StorSimple para administrar volúmenes](storsimple-manage-volumes-u2.md).|
|Baja latencia y alto rendimiento | StorSimple actualización 2 le permite crear dispositivos virtuales que sacar partido de alto rendimiento, características de latencia baja de almacenamiento de Azure premium. Para obtener más información acerca de los dispositivos de StorSimple premium virtuales, consulte [implementar y administrar un dispositivo virtual StorSimple en Azure](storsimple-virtual-device-u2.md).|

![icono de vídeo](./media/storsimple-overview/video_icon.png) vea [este vídeo](https://www.youtube.com/watch?v=4MhJT5xrvQw&feature=youtu.be) para obtener información general sobre las características de serie 8000 StorSimple y ventajas.

## <a name="storsimple-components"></a>Componentes de StorSimple

La solución de Microsoft Azure StorSimple incluye los siguientes componentes:

- **Dispositivo de Microsoft Azure StorSimple** – local híbrido almacenamiento matriz que contiene SSDs y unidades de disco duro, junto con controladores redundantes y las capacidades de migración tras error automática. Los controladores de administran almacenamiento interconexión, colocando actualmente datos utilizados (o activos) en almacenamiento local (en los servidores de dispositivo o local), al mover menos datos utilizados con frecuencia en la nube.
- **Dispositivo virtual de StorSimple** , también conocido como el dispositivo Virtual StorSimple, esta es una versión del software del dispositivo StorSimple que aplica la arquitectura y la mayoría de las funciones del dispositivo de almacenamiento híbrida físico. El dispositivo virtual StorSimple se ejecuta en un único nodo en una máquina virtual Azure. Dispositivos virtuales Premium, que aprovechar las ventajas de almacenamiento de Azure premium, están disponibles en la actualización 2 y posterior.
- **Servicio de administrador de StorSimple** : extensión del portal clásica Azure que le permite administrar un StorSimple dispositivo o StorSimple virtual desde una interfaz web única. Puede usar el servicio Administrador de StorSimple para crear y administrar servicios, ver y administrar dispositivos, ver alertas, administrar volúmenes y ver y administrar las directivas de copia de seguridad y el catálogo de copia de seguridad.
- **Windows PowerShell para StorSimple** : una interfaz de línea de comandos que puede usar para administrar el dispositivo de StorSimple. Windows PowerShell para StorSimple tiene características que le permiten registrar su dispositivo StorSimple, configure la interfaz de red en el dispositivo, instalar a ciertos tipos de actualizaciones, obtener acceso a la sesión de soporte técnico para solucionar el dispositivo y a continuación, cambiar el estado del dispositivo. Puede obtener acceso a Windows PowerShell para StorSimple mediante una conexión a la consola serie o mediante Windows PowerShell remoto.
- **Cmdlets de azure PowerShell StorSimple** : un conjunto de cmdlets de Windows PowerShell que permiten automatizar tareas de nivel de servicio y migración desde la línea de comandos. Para obtener más información acerca de los cmdlets de PowerShell de Azure para StorSimple, vaya a la [referencia de los cmdlets](https://msdn.microsoft.com/library/dn920427.aspx).
- **Administrador de instantáneas StorSimple** : un complemento MMC que usa el servicio de copia de instantáneas de volumen de Windows y grupos de volumen para generar copias de seguridad coherentes para la aplicación. Además, puede usar el Administrador de instantáneas StorSimple crear clonar y programaciones de copia de seguridad o restaurar volúmenes. 
- **Adaptador StorSimple para SharePoint** : una herramienta de forma transparente extiende StorSimple de Microsoft Azure almacenamiento y protección de datos en granjas de servidores de SharePoint, mientras realiza almacenamiento StorSimple visible y se puede administrar desde el portal de Administración Central de SharePoint.

El diagrama siguiente proporciona una visión general de los componentes y la arquitectura de Microsoft Azure StorSimple.

![Arquitectura de StorSimple](./media/storsimple-overview/overview-big-picture.png)

Las secciones siguientes describen cada uno de estos componentes con más detalle y explican cómo la solución organiza los datos, asigna el almacenamiento y facilita la administración de almacenamiento y protección de datos. La última sección proporciona definiciones de algunos de los términos importantes y conceptos relacionados con los componentes de StorSimple y su administración.

## <a name="storsimple-device"></a>Dispositivo StorSimple

El dispositivo de Microsoft Azure StorSimple es una matriz de almacenamiento de híbrida local que proporciona almacenamiento principal y iSCSI acceso a datos almacenados en él. Administra la comunicación con el almacenamiento de la nube y ayuda a garantizar la seguridad y confidencialidad de todos los datos que se almacena en la solución Microsoft Azure StorSimple.

El dispositivo StorSimple incluye SSDs y unidades de disco duro de unidades de disco duro, así como soporte para clúster y automática de migración tras error. Contiene un procesador compartido, almacenamiento compartido y dos controladores reflejados. Cada controlador ofrece lo siguiente:

- Conexión a un equipo host
- Hasta seis puertos de red para conectarse a la red de área local (LAN)
- Supervisión de hardware
- Memoria de acceso aleatorio de no volátiles (NVRAM), que conserva la información incluso si se interrumpe power
- Clústeres actualizar para administrar las actualizaciones de software en servidores en un clúster para que las actualizaciones tengan mínimas o ningún efecto en la disponibilidad del servicio
- Clúster de servicio, que funcione como un clúster de back-end, proporcionar alta disponibilidad y reducir al mínimo los efectos negativos que pueden producirse si una unidad de disco duro o SSD falla o está desconectado

Sólo un controlador está activo en cualquier punto de tiempo. Si se produce un error en el controlador de activo, el segundo controlador activo automáticamente. 

Para obtener más información, vaya a [estado y componentes de hardware de StorSimple](storsimple-monitor-hardware-status.md).

## <a name="storsimple-virtual-device"></a>Dispositivo virtual de StorSimple

Puede usar StorSimple para crear un dispositivo virtual que aplica la arquitectura y las capacidades del dispositivo de almacenamiento híbrida físico. El dispositivo virtual StorSimple (también conocido como el dispositivo Virtual de StorSimple) se ejecuta en un único nodo en una máquina virtual Azure. (Solo se puede crear un dispositivo virtual en una máquina virtual Azure. No puede crear uno en un servidor local o en un dispositivo de StorSimple.) 

El dispositivo virtual tiene las siguientes características:

- Se comporta como un dispositivo físico y ofrece una interfaz iSCSI a máquinas virtuales en la nube. 
- Puede crear una cantidad ilimitada de dispositivos virtuales en la nube y activarlos y desactivar según sea necesario. 
- Puede ayudar a simular entornos locales de recuperación, el desarrollo y escenarios de prueba y puede ayudarle con la recuperación de nivel de elemento de las copias de seguridad. 

Con la actualización 2 y versiones posteriores, el dispositivo virtual StorSimple está disponible en dos modelos: el dispositivo 8010 (anteriormente conocido como el modelo 1100) y el 8020. El dispositivo de 8010 tiene una capacidad máxima de 30 TB. El dispositivo 8020, que aprovecha el almacenamiento de Azure premium, tiene una capacidad máxima de 64 TB. (En los niveles locales, almacenamiento Azure premium almacena datos en SSDs considerando almacenamiento estándar almacena los datos en unidades de disco duro.) Tenga en cuenta que debe tener una cuenta de almacenamiento de Azure premium usar almacenamiento premium. Para obtener más información sobre el almacenamiento de premium, vaya a [almacenamiento Premium: almacenamiento de alto rendimiento para cargas de trabajo de máquina Virtual de Azure](../storage/storage-premium-storage.md).

Para obtener más información sobre el dispositivo virtual StorSimple, vaya a [implementar y administrar un dispositivo virtual StorSimple en Azure](storsimple-virtual-device-u2.md).

## <a name="storsimple-manager-service"></a>Servicio de administrador de StorSimple

Microsoft Azure StorSimple proporciona una interfaz de usuario de basada en web (el servicio de administrador de StorSimple) que permite administrar el centro de datos y almacenamiento de nube de forma centralizada. Puede usar el servicio Administrador de StorSimple para realizar las siguientes tareas:

- Configurar opciones del sistema para dispositivos StorSimple.
- Configurar y administrar la configuración de seguridad para dispositivos StorSimple.
- Configurar las propiedades y las credenciales de la nube.
- Configurar y administrar volúmenes en un servidor.
- Configurar grupos de volumen.
- Realizar copias de seguridad y restaurar los datos.
- Supervisar el rendimiento.
- Revisar la configuración del sistema e identificar posibles problemas.

Puede usar el servicio Administrador de StorSimple para realizar todas las tareas de administración, excepto aquellos que requieren el sistema de tiempo, como la configuración inicial y la instalación de las actualizaciones.

Para obtener más información, vaya a [usar el servicio de administrador de StorSimple para administrar el dispositivo de StorSimple](storsimple-manager-service-administration.md).

## <a name="windows-powershell-for-storsimple"></a>Windows PowerShell para StorSimple

Windows PowerShell para StorSimple ofrece una interfaz de línea de comandos que puede usar para crear y administrar el servicio de Microsoft Azure StorSimple y configurar y supervisar los dispositivos de StorSimple. Es una interfaz de línea de comandos basado en Windows PowerShell, que incluye los cmdlets dedicados para administrar el dispositivo StorSimple. Windows PowerShell para StorSimple tiene características que le permiten:

- Registrar un dispositivo.
- Configurar la interfaz de red en un dispositivo.
- Instalar a ciertos tipos de actualizaciones.
- Obtener acceso a la sesión de soporte técnico para solucionar el dispositivo.
- Cambiar el estado del dispositivo.

Puede tener acceso a Windows PowerShell para StorSimple desde la consola de serie (en un equipo host conectado directamente al dispositivo) o de forma remota mediante el uso de Windows PowerShell remoto. Tenga en cuenta que solo se puede realizar algunos Windows PowerShell para tareas de StorSimple, como el registro inicial del dispositivo, en la consola de serie. 

Para obtener más información, vaya a [Usar Windows PowerShell para StorSimple administrar el dispositivo](storsimple-windows-powershell-administration.md).

## <a name="azure-powershell-storsimple-cmdlets"></a>Cmdlets de PowerShell StorSimple Azure

Los cmdlets de Azure PowerShell StorSimple son un conjunto de cmdlets de Windows PowerShell que permiten automatizar tareas de nivel de servicio y migración desde la línea de comandos. Para obtener más información acerca de los cmdlets de PowerShell de Azure para StorSimple, vaya a la [referencia de los cmdlets](https://msdn.microsoft.com/library/dn920427.aspx).

## <a name="storsimple-snapshot-manager"></a>Administrador de instantáneas StorSimple

Administrador de instantáneas StorSimple es un complemento de consola de administración de Microsoft (MMC) que puede usar para crear copias de seguridad en el momento de local y datos de la nube. El complemento se ejecuta en un host de Windows Server. Puede utilizar el Administrador de instantánea StorSimple para:

- Configurar, realizar copias de seguridad y eliminar volúmenes.
- Configurar grupos de volumen para asegurarse de que la copia de seguridad de datos es coherente para la aplicación.
- Administrar directivas de copia de seguridad para que los datos de copia de seguridad en una programación predeterminada y almacenados en una ubicación designada (localmente o en la nube).
- Restaurar volúmenes y archivos individuales.

Realizar copias de seguridad se capturan como instantáneas que grabar sólo los cambios desde que se realizó la última instantánea y requieran mucho menos espacio de almacenamiento que las copias de seguridad completas. Puede crear programaciones de copia de seguridad o realizar copias de seguridad inmediatos según sea necesario. Además, puede usar Administrador de instantáneas StorSimple establecer directivas de retención que controlan cuántas copias instantáneas se guardarán. Si posteriormente necesita restaurar los datos de una copia de seguridad, permite StorSimple instantánea Manager se selecciona en el catálogo de local o instantáneas de la nube. 

Si se produce un desastre o si necesita restaurar datos por otro motivo, Administrador de instantáneas StorSimple restaura incrementalmente según sea necesario. Restauración de los datos no requiere apagar todo el sistema mientras restaurar un archivo, sustituir el equipo o mover operaciones a otro sitio.

Para obtener más información, vaya a [¿Qué es el Administrador de instantáneas StorSimple?](storsimple-what-is-snapshot-manager.md)

## <a name="storsimple-adapter-for-sharepoint"></a>Adaptador StorSimple para SharePoint

Microsoft Azure StorSimple incluye el adaptador StorSimple para SharePoint, un componente opcional que se extiende transparente StorSimple almacenamiento datos características y protección en granjas de servidores de SharePoint. El adaptador funciona con un proveedor de servicios de almacenamiento de blobs remoto (EDR) y la característica de SQL Server EDR, lo que le permite mover BLOB a un servidor de copia de seguridad por el sistema de Microsoft Azure StorSimple. Microsoft Azure StorSimple, a continuación, almacena los datos BLOB localmente o en la nube, basado en uso.

El adaptador StorSimple de SharePoint se administra desde el portal de Administración Central de SharePoint. Por consiguiente, administración de SharePoint permanece centralizada y todo el almacenamiento aparece en el conjunto de servidores de SharePoint.

Para obtener más información, vaya a [StorSimple adaptador de SharePoint](storsimple-adapter-for-sharepoint.md). 

## <a name="storage-management-technologies"></a>Tecnologías de administración de almacenamiento
 
Además del dispositivo StorSimple dedicado, dispositivo virtual y otros componentes, Microsoft Azure StorSimple usa las siguientes tecnologías de software para proporcionar acceso rápido a los datos y reducir el consumo de almacenamiento:

- [Niveles de almacenamiento automático](#automatic-storage-tiering) 
- [Aprovisionamiento fina](#thin-provisioning) 
- [Desduplicación y compresión](#deduplication-and-compression) 

### <a name="automatic-storage-tiering"></a>Niveles de almacenamiento automático

Microsoft Azure StorSimple organiza automáticamente los datos en niveles lógicos basados en uso actual, edad y relación con otros datos. Los datos más activos se almacenan localmente, aunque menos los datos activos e inactivos se migran automáticamente a la nube. El siguiente diagrama muestra este enfoque de almacenamiento.
 
![Niveles de almacenamiento de StorSimple](./media/storsimple-overview/hcs-data-services-storsimple-components-tiers.png)

Para habilitar el acceso rápido, StorSimple almacena datos muy activos (datos activos) en SSDs en el dispositivo StorSimple. Almacena los datos que se utilizan en ocasiones (caliente datos) en las unidades de disco duro en el dispositivo o en los servidores en el centro de datos. Mueve datos inactivos, datos de copia de seguridad, y los datos se conservan para archivar o propósitos de cumplimiento en la nube. 

>[AZURE.NOTE] En la actualización 2 o versiones posteriores, puede especificar un volumen localmente anclado, en cuyo caso los datos permanecerán en el dispositivo local y no están en niveles en la nube. 

StorSimple ajusta y vuelve a organizar datos y cambiar las asignaciones de almacenamiento como patrones de uso. Por ejemplo, alguna información podría ser menos activa con el tiempo. A medida que cada vez menos activo, se migra de SSDs a unidades de disco duro y, a continuación, en la nube. Si los mismos datos vuelve a estar activos, se migran al dispositivo de almacenamiento.

El proceso de niveles de almacenamiento se produce como sigue:

1. Un administrador del sistema configura una cuenta de almacenamiento de la nube de Microsoft Azure.
2. El administrador utiliza la consola de serie y el servicio de administrador de StorSimple (que se ejecuta en el portal de clásico Azure) para configurar el dispositivo y servidor de archivo, crear directivas de protección de datos y volúmenes. Equipos locales (como servidores de archivo) usar la interfaz de sistema de equipo pequeños de Internet (iSCSI) para tener acceso al dispositivo StorSimple.
3. Inicialmente, StorSimple almacena los datos en el nivel SSD rápido del dispositivo.
4. El nivel SSD acercarse capacidad StorSimple deduplica comprime los bloques de datos más antiguos y coloca en el nivel de la unidad de disco duro.
5. La capacidad de métodos de nivel de unidad de disco duro, StorSimple cifra los bloques de datos más antiguos y envía de forma segura a la cuenta de almacenamiento de Microsoft Azure a través de HTTPS.
6. Microsoft Azure crea varias réplicas de los datos en su centro de datos y en un centro de datos remota, garantizar que los datos se pueden recuperar si se produce un desastre. 
7. Cuando el servidor de archivos solicita datos almacenados en la nube, StorSimple devuelve sin problemas y guarda una copia en el nivel SSD del dispositivo StorSimple.

### <a name="thin-provisioning"></a>Aprovisionamiento fina

Aprovisionamiento fino es una tecnología de virtualización en la que almacenamiento disponible aparece supere los recursos físicos. En lugar de reservar suficiente espacio de almacenamiento por adelantado, StorSimple utiliza aprovisionamiento fina asignar suficiente espacio para satisfacer requisitos actuales. La naturaleza de almacenamiento de nube elástica facilita este enfoque porque StorSimple puede aumentar o disminuir el almacenamiento de la nube para satisfacer las necesidades cambiantes. 

>[AZURE.NOTE] No aprovisionados thin volúmenes anclados localmente. Almacenamiento asignado a un volumen sólo local está configurado en su totalidad cuando se crea el volumen.

### <a name="deduplication-and-compression"></a>Desduplicación y compresión

Microsoft Azure StorSimple usa desduplicación y compresión de datos para reducir aún más los requisitos de almacenamiento.

La deduplicación reduce la cantidad total de datos almacenados eliminando redundancia en el conjunto de datos almacenado. Cuando se pasa la información, StorSimple pasa por alto los datos sin cambios y captura sólo los cambios. Además, StorSimple reduce la cantidad de datos almacenados mediante la identificación y eliminación de información innecesaria. 

>[AZURE.NOTE] Datos de volúmenes localmente anclados no es desduplicados o comprimir. Sin embargo, las copias de seguridad de volúmenes localmente anclados son desduplicados y comprimidos.

## <a name="storsimple-workload-summary"></a>Carga de trabajo de StorSimple resumen

Un resumen de las cargas de trabajo de StorSimple compatibles se incluyen debajo.

| Escenario                  | Carga de trabajo                | Compatibles |  Restricciones                                  | Versión              |
|---------------------------|-------------------------|-----------|------------------------------------------------|----------------------|
| Colaboración             | Uso compartido de archivos            | Sí       |                                                | Todas las versiones         |
| Colaboración             | Uso compartido de archivos distribuido| Sí       |                                                | Todas las versiones         |
| Colaboración             | SharePoint              | Sí *      |Compatible únicamente con volúmenes localmente anclados      | Actualización 2 y versiones posterior   |
| Archivado                  | Archivado simple de archivos   | Sí       |                                                | Todas las versiones         |
| Virtualización            | Máquinas virtuales de Windows        | Sí *      |Compatible únicamente con volúmenes localmente anclados      | Actualización 2 y versiones posterior   |
| Base de datos                  | SQL                     | Sí *      |Compatible únicamente con volúmenes localmente anclados      | Actualización 2 y versiones posterior   |
| Control de vídeo        | Control de vídeo      | Sí *       |Compatible cuando StorSimple dispositivo está dedicado solo a esta carga de trabajo| Actualización 2 y versiones posterior   |
| Copia de seguridad                    | Copia de seguridad de destino principal | Sí *      |Compatible cuando StorSimple dispositivo está dedicado solo a esta carga de trabajo| Actualización 3 y posterior |
| Copia de seguridad                    | Copia de seguridad de destino secundario | Sí *      |Compatible cuando StorSimple dispositivo está dedicado solo a esta carga de trabajo| Actualización 3 y posterior |

*Sí & #42; -Restricciones y directrices solución deben aplicarse.*

Las cargas de trabajo siguientes no son compatibles con dispositivos de la serie 8000 StorSimple. Si se implementa en StorSimple, estas cargas de trabajo se traducirán en una configuración no compatible.

-  Imágenes médicas
-  Exchange
-  VDI
-  Oracle
-  SAP
-  Datos grandes
-  Distribución de contenido
-  Inicio de SCSI

A continuación se muestra una lista de los componentes de infraestructura de StorSimple compatible. 

| Escenario | Carga de trabajo      | Compatibles |  Restricciones                                 | Versión      |
|----------|---------------|-----------|-----------------------------------------------|--------------|
| General  | Ruta de Express | Sí       |                                               |Todas las versiones |
| General  | DataCore FC   | Sí *       |Compatible con DataCore SANsymphony            | Todas las versiones |
| General  | DFSR          | Sí *      |Compatible únicamente con volúmenes localmente anclados     | Todas las versiones |
| General  | Indización      | Sí *       |Para volúmenes en niveles, se admite la indización de metadatos sólo (sin datos).<br>Para volúmenes anclados localmente, se admite la indización completa.| Todas las versiones |
| General  | Antivirus    | Sí *       |Para volúmenes en niveles, es compatible únicamente análisis al abrir y cerrar.<br> Para volúmenes localmente anclados, análisis completo es compatible.| Todas las versiones |

*Sí & #42; -Restricciones y directrices solución deben aplicarse.*

## <a name="storsimple-terminology"></a>Terminología de StorSimple 

Antes de implementar la solución Microsoft Azure StorSimple, le recomendamos que revise los siguientes términos y definiciones.

### <a name="key-terms-and-definitions"></a>Términos y definiciones clave

| Término (acrónimo o abreviatura) | Descripción |
| ------------------------------ | ---------------- |
| registro de control de acceso (ACR)    | Un registro asociado con un volumen en el dispositivo de Microsoft Azure StorSimple que determina qué hosts se pueden conectar a ella. La determinación se basa en el iSCSI nombre completo (IQN) de los hosts (incluidos en el ACR) que se conexión al dispositivo StorSimple.|
| AES 256                        | Un algoritmo de estándar de cifrado avanzado (AES) de 256 bits para cifrar datos tal y como se mueve hacia y desde la nube. |
| tamaño de la unidad de asignación (Australia)     | Sistemas de archivos de la menor cantidad de espacio en disco que puede asignar a un archivo en Windows. Si un tamaño de archivo no es un múltiplo del tamaño del clúster, se debe utilizar espacio adicional para guardar el archivo (hasta el siguiente múltiplo del tamaño del clúster) de fragmentación del disco duro y pierde espacio. <br>La Australia recomendado para volúmenes de Azure StorSimple es 64 KB porque funciona bien con los algoritmos de desduplicación.|
| niveles de almacenamiento automatizado      | Mover automáticamente los datos menos activos de SSDs en unidades de disco duro y después en un nivel en la nube y, a continuación, habilitar la administración de todo el almacenamiento de una interfaz de usuario central.|
| catálogo de copia de seguridad | Una colección de copias de seguridad, normalmente relacionadas con el tipo de aplicación que se ha usado. Esta colección se muestra en la página Catálogo de copia de seguridad de la interfaz de usuario de servicio de administrador de StorSimple.|
| archivo de copia de seguridad de catálogo             | Un archivo que contiene una lista de instantáneas disponibles actualmente almacenados en la base de datos de copia de seguridad del Administrador de instantánea StorSimple. |
| Directiva de copia de seguridad                   | Selección de volúmenes, el tipo de copia de seguridad y un calendario que le permite crear copias de seguridad en una programación predefinida.|
| objetos binarios grandes (BLOB)    | Un conjunto de datos binarios almacenados como una sola entidad en un sistema de administración de la base de datos. BLOB suelen ser imágenes, audio u otros objetos multimedios, aunque a veces binario código ejecutable se almacena como BLOB.|
| Protocolo de autenticación (CHAP) | Protocolo utilizado para autenticar al punto de una conexión, según el punto compartir un secreto o una contraseña. Puede ser CHAP unidireccional o mutuo. Con CHAP unidireccional, el destino autentica un iniciador. CHAP mutuo requiere que el destino autentica el iniciador y que el iniciador autentica el destino. | 
| clonar                          | Un duplicado de un volumen. |
|Nube como nivel (CaaT)          | Almacenamiento integrado como nivel dentro de la arquitectura de almacenamiento para que todo el almacenamiento parece ser parte de la red de almacenamiento de una de las empresas de la nube.|
| proveedor de servicios de nube (CSP)   | Un proveedor de servicios de la informática en nube.|
| instantánea de la nube                 | Una copia de punto en el tiempo de los datos de volumen que se almacenan en la nube. Una instantánea de la nube es equivalente a una instantánea replicada en un sistema de almacenamiento diferente, fuera del sitio. Instantáneas de nube son particularmente útiles en escenarios de recuperación de desastres.|
| clave de cifrado de almacenamiento de nube   | Una contraseña o una clave que utiliza el dispositivo StorSimple para acceder a los datos cifrados enviados por el dispositivo en la nube.|
| actualizar clústeres         | Administrar las actualizaciones de software en servidores en un clúster para que las actualizaciones tengan mínimas o ningún efecto en la disponibilidad del servicio.|
| DataPath                       | Una colección de unidades funcionales que lleve a cabo operaciones de transformación de datos entre conectada.|
| desactivar                     | Una acción permanente que interrumpe la conexión entre el dispositivo de StorSimple y el servicio de nube asociado. Instantáneas de nube del dispositivo permanecen después de este proceso y se pueden clonar o usadas para recuperación.|
| reflejo de disco                 | Replicación de volúmenes de disco lógico en el disco duro independiente unidades en tiempo real para garantizar la disponibilidad continua.|
| reflejo de disco dinámico         | Replicación de volúmenes de disco lógico en discos dinámicos.|
| discos dinámicos                  | Formato de volumen de disco que utiliza el Administrador de discos lógicos (LDM) para almacenar y administrar datos en varios discos físicos. Discos dinámicos pueden ampliarse para proporcionar más espacio libre.|
| Caracteres enmarcados ampliado de conjunto de discos (EBOD) | Un alojamiento secundario de su dispositivo de Microsoft Azure StorSimple que contiene los discos de disco duro adicional para almacenamiento adicional.|
| Aprovisionamiento de FAT               | Un almacenamiento convencional aprovisionamiento en qué almacenamiento se asigna espacio según previsto necesidades (y suele ser más allá de la necesidad actual). Vea también *aprovisionamiento fina*.|
| unidad de disco duro (disco duro)          | Una unidad que utiliza discos de rotación para almacenar datos.|
| almacenamiento de nube híbrida           | Arquitectura de almacenamiento que usa los recursos locales y fuera del sitio, incluido el almacenamiento de la nube.|
| Interfaz de sistema de equipos pequeños de Internet (iSCSI) | Un estándar de red de almacenamiento basada en IP de protocolo de Internet para vincular equipos de almacenamiento de datos o las instalaciones.|
| iniciador iSCSI                 | Componente de software que permite a un equipo host con Windows para conectarse a una red externa almacenamiento basado en iSCSI.|
| iSCSI nombre completo (IQN)      | Un nombre único que identifica un destino iSCSI o iniciador.|
| destino iSCSI                    | Componente de software que proporciona iSCSI centralizada subsistemas de disco en redes de área de almacenamiento.|
| Live archivado                  | Un enfoque de almacenamiento en los que archivar datos están accesibles todo el tiempo (no se almacena fuera de la oficina en la cinta, por ejemplo). Microsoft Azure StorSimple usa archivado directo.|
|volumen localmente anclada | volumen que reside en el dispositivo y nunca es niveles en la nube. |
| instantánea local                  | Una copia de punto en el tiempo de los datos de volumen que se almacenan en el dispositivo de Microsoft Azure StorSimple.|
| StorSimple de Microsoft Azure      | Una solución eficaz formada por un dispositivo de almacenamiento del centro de datos y software que permite a las organizaciones de TI aprovechar almacenamiento de nube como si fuese almacenamiento del centro de datos. StorSimple simplifica la protección de datos y administración de datos y reducir los costos. La solución consolida principal almacenamiento, archivar, copia de seguridad y recuperación de desastres (DR) mediante la integración perfecta con la nube. Mediante la combinación de administración de los datos de almacenamiento y nube de SAN en una plataforma de clase empresarial, StorSimple dispositivos habilitar velocidad, simplificar y confiabilidad para todas las necesidades de almacenamiento.|
| Alimentación y refrigeración módulo (PCM)  | Componentes de hardware de su dispositivo StorSimple formada por las fuentes de alimentación y ventilador refrigeración, por lo tanto, el nombre de la energía y refrigeración módulo. Los caracteres enmarcados principal del dispositivo tienen dos PCMs 764W mientras que los caracteres enmarcados EBOD tiene dos PCMs 580 w.|
| caracteres enmarcados principal               | Caracteres enmarcados principal del dispositivo StorSimple que contiene los controladores de plataforma de aplicación.|
| objetivo de tiempo de recuperación (RTO)   | La cantidad máxima de tiempo que debe dedicado antes de un proceso empresarial o el sistema se restaura completamente después de un desastre.| 
|serie adjunta SCSI (SA)       | Tipo de unidad de disco duro (disco duro).|
| clave de cifrado de datos de servicio     | Una clave disponibles para cualquier dispositivo StorSimple nueva que se registra con el servicio de administrador de StorSimple. Los datos de configuración que se transfieren entre el servicio Administrador de StorSimple y el dispositivo está cifrados con una clave pública y, a continuación, se pueden descifrar únicamente en el dispositivo con una clave privada. Clave de cifrado de datos de servicio permite que el servicio para obtener esta clave privada para el descifrado.|
| clave de registro de servicio        | Una clave que le ayuda a registrar el dispositivo StorSimple con el servicio de administrador de StorSimple para que aparezca en el portal de Azure clásico para aún más acciones de administración.|
| Interfaz de sistema de equipos pequeños (SCSI) | Un conjunto de estándares de física conectar equipos y pasar datos entre ellas.|
| unidad de estado sólido (SSD)         | Un disco que no contenga partes móviles; Por ejemplo, una unidad flash.|
| cuenta de almacenamiento                 | Un conjunto de credenciales de access vinculada a su cuenta de almacenamiento para un proveedor de servicios de nube determinado.| 
| Adaptador StorSimple para SharePoint| Un componente de Microsoft Azure StorSimple transparente extiende StorSimple almacenamiento y protección de datos en granjas de servidores de SharePoint.|
| Servicio de administrador de StorSimple      | Extensión del portal clásica Azure que le permite administrar los dispositivos virtuales y Azure StorSimple local.|
| Administrador de instantáneas StorSimple     | Un complemento de consola de administración de Microsoft (MMC) para administrar las operaciones de copia de seguridad y restauración en Microsoft Azure StorSimple.|
| realizar copia de seguridad                     | Una característica que permite al usuario realizar una copia de seguridad interactiva de un volumen. Es una forma alternativa de realizar una copia de seguridad manual de un volumen en lugar de realizar una copia de seguridad automática a través de una directiva definida.|
| Aprovisionamiento fina               | Un método para optimizar la eficiencia con la que se utiliza el espacio de almacenamiento disponible en los sistemas de almacenamiento. En aprovisionamiento fina, el almacenamiento se asigna entre varios usuarios en función del espacio mínimo necesario para cada usuario en cualquier momento. Vea también *el aprovisionamiento de fat*.|
| interconexión | Organizar los datos en grupos lógicos basados en uso actual, edad y relación con otros datos. StorSimple organiza automáticamente los datos en niveles.   |
| volumen                          | Áreas de almacenamiento lógico realizadas en el formulario de unidades. StorSimple volúmenes corresponden a los volúmenes montados por el host, incluidos aquellos encontrados mediante el uso de iSCSI y un dispositivo de StorSimple.|
 | contenedor de volumen                | Agrupación de volúmenes y la configuración que se aplica a ellas. Todos los volúmenes en el dispositivo StorSimple se agrupan en contenedores de volumen. Configuración del contenedor de volumen incluye cuentas de almacenamiento, la configuración de cifrado de los datos enviados en la nube con claves de cifrado asociado y ancho de banda consumido para operaciones relacionadas con la nube.|
| grupo de volumen                    | En el Administrador de instantánea StorSimple, un grupo de volumen es una colección de volúmenes configurado para facilitar el proceso de copia de seguridad.|
| Servicio de copia de sombra de volumen (VSS)| Un servicio de sistema operativo Windows Server que facilita la coherencia de la aplicación mediante la comunicación con aplicaciones compatibles con VSS para coordinar la creación de instantáneas incrementales. VSS garantiza que las aplicaciones estén temporalmente inactivas cuando se toman instantáneas.|
| Windows PowerShell para StorSimple | Una interfaz de línea de comandos basado en Windows PowerShell utilizada para operar y administrar el dispositivo de StorSimple. Mientras mantiene algunas de las características básicas de Windows PowerShell, esta interfaz tiene cmdlets dedicado adicionales que están dirigidos a administrar un dispositivo StorSimple.|


## <a name="next-steps"></a>Pasos siguientes

Obtenga información sobre [seguridad de StorSimple](storsimple-security.md).




 

 
