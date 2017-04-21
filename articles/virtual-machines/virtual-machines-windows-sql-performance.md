<properties
    pageTitle="Prácticas recomendadas de rendimiento para SQL Server | Microsoft Azure"
    description="Proporciona prácticas recomendadas para optimizar el rendimiento de SQL Server en máquinas virtuales de Microsoft Azure."
    services="virtual-machines-windows"
    documentationCenter="na"
    authors="rothja"
    manager="jhubbard"
    editor=""
    tags="azure-service-management" />

<tags
    ms.service="virtual-machines-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="vm-windows-sql-server"
    ms.workload="infrastructure-services"
    ms.date="09/07/2016"
    ms.author="jroth" />

# <a name="performance-best-practices-for-sql-server-in-azure-virtual-machines"></a>Prácticas recomendadas de rendimiento para SQL Server en Azure máquinas virtuales de Windows

## <a name="overview"></a>Información general

Este tema proporciona mejores prácticas para optimizar el rendimiento de SQL Server en el equipo Virtual de Microsoft Azure. Mientras se ejecuta SQL Server en Azure máquinas virtuales de Windows, le recomendamos que siga utilizando la misma opciones que se aplican a SQL Server en el entorno de servidor local de ajuste del rendimiento de base de datos. Sin embargo, el rendimiento de una base de datos relacional de una nube pública depende de muchos factores como el tamaño de una máquina virtual y la configuración de los discos de datos.

Al crear imágenes de SQL Server, [considere la posibilidad de hacer el aprovisionamiento de sus máquinas virtuales en el portal de Azure](virtual-machines-windows-portal-sql-server-provision.md). Las máquinas virtuales de SQL Server configurado en el Portal con el Administrador de recursos implemente todas estas recomendaciones, incluida la configuración de almacenamiento.

En este artículo se centra en obtener el *mejor* rendimiento para SQL Server en máquinas virtuales de Azure. Si la carga de trabajo consume menos, no es posible que necesite cada optimización que se muestran a continuación. Tenga en cuenta las necesidades de rendimiento y los patrones de carga de trabajo al evaluar estas recomendaciones.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

## <a name="quick-check-list"></a>Lista de comprobación rápida

A continuación se muestra una lista de comprobación rápida para obtener un rendimiento óptimo de SQL Server en Azure máquinas virtuales de Windows:

|Área|Optimizaciones|
|---|---|
|[Tamaño de memoria virtual](#vm-size-guidance)|[DS3](virtual-machines-windows-sizes.md#standard-tier-ds-series) o superior para SQL Enterprise edition.<br/><br/>[DS2](virtual-machines-windows-sizes.md#standard-tier-ds-series) o superior para las ediciones de SQL estándar y Web.|
|[Almacenamiento de información](#storage-guidance)|Usar [el almacenamiento Premium](../storage/storage-premium-storage.md). Solo se recomienda almacenamiento estándar para desarrollo o prueba.<br/><br/>Mantener la [cuenta de almacenamiento](../storage/storage-create-storage-account.md) y la máquina virtual de SQL Server en la misma región.<br/><br/>Deshabilitar Azure [almacenamiento geo redundantes](../storage/storage-redundancy.md) (geo replicación) en la cuenta de almacenamiento.|
|[Discos](#disks-guidance)|Usar un mínimo de 2 [discos de P30](../storage/storage-premium-storage.md#scalability-and-performance-targets-when-using-premium-storage) (1 para los archivos de registro; 1 para los archivos de datos y TempDB).<br/><br/>Evitar el uso de sistema operativo o discos temporales para el almacenamiento de la base de datos o registro.<br/><br/>Activar la lectura de almacenamiento en caché en los discos que hospeda los archivos de datos y TempDB.<br/><br/>No habilite el almacenamiento en caché en discos que aloja el archivo de registro.<br/><br/>Importante: Detener el servicio SQL Server al cambiar la configuración de caché para un disco de Azure VM.<br/><br/>Bandas de varios discos de datos de Azure para obtener un rendimiento mejorado IO.<br/><br/>Formato con documentado tamaños de la asignación.|
|[I/OS](#io-guidance)|Habilitar la compresión de la página de base de datos.<br/><br/>Habilitar la inicialización instantánea de archivos para archivos de datos.<br/><br/>Limitar o deshabilitar crecimiento automático en la base de datos.<br/><br/>Deshabilitar la reducción automática de la base de datos.<br/><br/>Mover todas las bases de datos a los discos de datos, incluyendo bases de datos del sistema.<br/><br/>Mover directorios de archivos de registro y seguimiento de los errores de SQL Server a discos de datos.<br/><br/>Configurar ubicaciones de archivo de copia de seguridad y base de datos predeterminado.<br/><br/>Habilitar páginas bloqueadas.<br/><br/>Aplicar correcciones de rendimiento de SQL Server.|
|[Características específicas](#feature-specific-guidance)|Haga una copia de seguridad directamente al almacenamiento de blobs.|

Para obtener más información sobre *cómo* y *por qué* realizar estas optimizaciones, revise los detalles y las instrucciones proporcionadas en las secciones siguientes.

## <a name="vm-size-guidance"></a>Guía de tamaño VM

Para las aplicaciones confidenciales de rendimiento, se recomienda que use los siguientes [tamaños de máquinas virtuales de Windows](virtual-machines-windows-sizes.md):

- **SQL Server Enterprise Edition**: DS3 o superior

- **SQL Server Standard y ediciones de Web**: DS2 o superior


## <a name="storage-guidance"></a>Guía de almacenamiento

Compatibilidad con máquinas virtuales de serie DS (junto con DSv2 series y GS-) [Almacenamiento Premium](../storage/storage-premium-storage.md). Almacenamiento de Premium se recomienda para todas las cargas de trabajo de producción.

> [AZURE.WARNING] Almacenamiento estándar tiene distintos latencia y ancho de banda y solo se recomienda para cargas de trabajo de desarrollo o prueba. Cargas de trabajo de producción deben utilizar almacenamiento Premium.

Además, recomendamos que cree su cuenta de almacenamiento de Azure en el mismo centro de datos como las máquinas virtuales de SQL Server para reducir los retrasos en la transferencia. Al crear una cuenta de almacenamiento, deshabilitar la replicación de geo como no se garantiza el orden de escritura coherente en varios discos. Considere la posibilidad de configurar una tecnología de recuperación de desastres de SQL Server entre dos Azure centros de datos. Para obtener más información, vea [alta disponibilidad y recuperación para SQL Server en Azure máquinas virtuales de Windows](virtual-machines-windows-sql-high-availability-dr.md).

## <a name="disks-guidance"></a>Guía de discos

Existen tres tipos de disco principal en una máquina virtual de Azure:

- **Disco de sistema operativo**: cuando se crea una máquina Virtual de Azure, la plataforma conectará al menos un disco (marcado como la unidad **C** ) a la máquina virtual para el disco de sistema operativo. Este disco es un disco duro virtual almacenado como un blob de página en el almacenamiento.
- **Disco temporal**: máquinas virtuales de Azure contienen otro disco llamado el disco temporal (marcado como la **d.**: unidad). Se trata de un disco en el nodo que se pueden usar para el espacio de borrador.
- **Discos de datos**: también puede adjuntar discos adicionales a su máquina virtual como discos de datos y, a continuación, estos se almacenarán en almacenamiento como blobs de página.

Las secciones siguientes describen recomendaciones para utilizar estos discos diferentes.

### <a name="operating-system-disk"></a>Disco del sistema operativo

Un disco de sistema operativo tiene un disco duro virtual que puede arrancar y montaje como una versión en ejecución de un sistema operativo y está marcado como unidad **C** .

Almacenamiento en caché de directiva en el disco de sistema operativo predeterminado es de **Lectura y escritura**. Para aplicaciones confidencial de rendimiento, se recomienda que use discos de datos en lugar del disco de sistema operativo. Consulte la sección sobre discos de datos a continuación.

### <a name="temporary-disk"></a>Disco temporal

La unidad de almacenamiento temporal, marcada como la **d.**: unidad, no se conserva en el almacenamiento de blobs de Windows Azure. No almacene los archivos de base de datos de usuario o archivos de registro de transacciones de usuario en el **d.**: unidad.

Para serie D, Dv2 serie y máquinas virtuales de serie G, la unidad temporal en estas máquinas virtuales está basado en SSD. Si la carga de trabajo hace un uso TempDB (por ejemplo, para objetos temporales o combinaciones complejas), almacenar TempDB en la unidad **D** podría provocar un mayor rendimiento de TempDB y menor latencia de TempDB.

Para máquinas virtuales que admiten el almacenamiento de Premium (serie DS, DSv2 serie y GS serie), se recomienda almacenar TempDB o extensiones de agrupación de búfer en un disco que admite el almacenamiento de Premium con la caché de lectura habilitado. Hay una excepción a esta recomendación; Si el uso de TempDB es escritura intensiva, puede lograr un mayor rendimiento almacenando TempDB en la unidad **D** local, que también está basado en SSD en estos tamaños de equipo.

### <a name="data-disks"></a>Discos de datos

- **Usar discos de datos para los archivos de datos y de registro**: mínimo, utilice 2 Premium almacenamiento [P30 discos](../storage/storage-premium-storage.md#scalability-and-performance-targets-when-using-premium-storage) donde un disco contiene los archivos de registro y la otra contiene los archivos de datos y TempDB.

- **Bandas de disco**: para obtener un rendimiento más, puede agregar discos de datos adicionales y utilizar discos con bandas. Para determinar el número de discos de datos, debe analizar el número de IOPS disponible para los discos de datos y de registro. Para esa información, vea las tablas en IOPS por tamaño del disco y el [tamaño de memoria virtual](virtual-machines-windows-sizes.md) en el siguiente artículo: [Utilizar almacenamiento Premium para discos](../storage/storage-premium-storage.md). Utilice la siguiente estas instrucciones:

    - Para Windows 8/Windows Server 2012 o posterior, use [Espacios de almacenamiento](https://technet.microsoft.com/library/hh831739.aspx). Establecer tamaño de las bandas a 64 KB para cargas de trabajo OLTP y 256 KB para cargas de trabajo de almacenamiento de datos para evitar el impacto en el rendimiento debido a la alineación de partición. Además, establecer el número de columnas = número de discos físicos. Para configurar un espacio de almacenamiento con más de 8 discos debe usar PowerShell (no UI administrador del servidor) para establecer explícitamente el número de columnas para que coincida con el número de discos. Para obtener más información sobre cómo configurar [Espacios de almacenamiento](https://technet.microsoft.com/library/hh831739.aspx), vea [Cmdlets de espacios de almacenamiento en Windows PowerShell](https://technet.microsoft.com/library/jj851254.aspx)

    - Para Windows 2008 R2 o una versión anterior, puede usar discos dinámicos (volúmenes OS seccionados) y el tamaño de las bandas siempre es 64 KB. Tenga en cuenta que esta opción es obsoleto a partir de Windows 8, Windows Server 2012. Para obtener más información, vea la declaración de soporte técnico en el [servicio de disco Virtual está pasando a la API de administración de almacenamiento de Windows](https://msdn.microsoft.com/library/windows/desktop/hh848071.aspx).

    - Si la carga de trabajo no está conectado intensiva y no es necesario IOPs dedicados, puede configurar un solo grupo de almacenamiento. En caso contrario, crear dos grupos de almacenamiento, uno para los archivos de registro y otro grupo de almacenamiento para los archivos de datos y TempDB. Determinar el número de discos asociados a cada grupo de almacenamiento en función de sus expectativas de carga. Tenga en cuenta que distintos tamaños VM permiten a diferentes números de discos de datos adjuntos. Para obtener más información, vea [tamaños para máquinas virtuales](virtual-machines-windows-sizes.md).

    - Si no usa almacenamiento Premium (escenarios de desarrollo y pruebas), la recomendación es agregar el número máximo de discos de datos admitidos por el [tamaño de memoria virtual](virtual-machines-windows-sizes.md) y utilizar los discos con bandas.

- **Directiva de caché**: discos de datos para el almacenamiento de Premium, habilitar la caché de lectura en los discos de datos que hospeda los archivos de datos y TempDB solo. Si no usa almacenamiento Premium, habilite el almacenamiento en caché en los discos de datos. Para obtener instrucciones sobre cómo configurar la caché de disco, vea los siguientes temas: [Conjunto AzureOSDisk](https://msdn.microsoft.com/library/azure/jj152847) y [Establecer AzureDataDisk](https://msdn.microsoft.com/library/azure/jj152851.aspx).

    >[AZURE.WARNING] Detener el servicio SQL Server al cambiar la configuración de caché de discos de Azure VM para evitar la posibilidad de daños en la base de datos.

- **Tamaño de unidad de asignación de NTFS**: al dar formato a los datos de un disco, se recomienda que use un tamaño de unidad de asignación de 64 KB para archivos de registro y datos, así como TempDB.

- **Mejores prácticas de administración de disco**: al quitar un disco de datos o cambiar su tipo de caché, detenga el servicio SQL Server durante el cambio. Cuando se cambia la configuración de almacenamiento en caché en el disco de sistema operativo, Azure deja la máquina virtual, cambia el tipo de caché y reinicia la máquina virtual. Cuando se cambia la configuración de caché de un disco de datos, no se ha detenido la máquina virtual, pero el disco de datos está desconectado de la máquina virtual durante el cambio y, a continuación, volver a adjuntar.

    >[AZURE.WARNING] Error al detener el servicio SQL Server durante estas operaciones puede provocar daños de la base de datos.

## <a name="io-guidance"></a>Guía de i/OS

- Obtener los mejores resultados con almacenamiento Premium se obtienen cuando paralelo su aplicación y solicitudes. Almacenamiento de Premium está diseñado para escenarios donde la profundidad de cola IO es mayor que 1, por lo que verá poco o ningún mejoras de rendimiento de un único subproceso solicitudes de serie (incluso si están almacenamiento intensivo). Por ejemplo, esto podría afectar a los resultados de pruebas de un único subproceso de herramientas de análisis de rendimiento, como SQLIO.

- Considere la posibilidad de usar [compresión de página de base de datos](https://msdn.microsoft.com/library/cc280449.aspx) puede ayudar a mejorar el rendimiento de las cargas de trabajo intensivo de i/OS. Sin embargo, la compresión de datos puede aumentar el consumo de CPU en el servidor de base de datos.

- Puede habilitar la inicialización instantánea de archivos reducir el tiempo necesario para la asignación de archivo inicial. Para aprovechar las ventajas de inicialización instantánea de archivos, conceda a la cuenta de servicio de SQL Server (MSSQLSERVER) con SE_MANAGE_VOLUME_NAME y agregar a la directiva de seguridad de **Realizar tareas de mantenimiento de volumen** . Si está utilizando una imagen de la plataforma de SQL Server para Azure, la cuenta de servicio predeterminada (NT Service\MSSQLSERVER) no se agrega a la directiva de seguridad de **Realizar tareas de mantenimiento de volumen** . En otras palabras, inicialización instantánea de archivos no está habilitada en una imagen de la plataforma de servidor de SQL Azure. Después de agregar la cuenta de servicio de SQL Server a la directiva de seguridad de **Realizar tareas de mantenimiento de volumen** , reinicie el servicio SQL Server. Puede haber consideraciones de seguridad para el uso de esta característica. Para obtener más información, vea [Inicialización del archivo de base de datos](https://msdn.microsoft.com/library/ms175935.aspx).

- **crecimiento automático** se considera simplemente contingencia para el crecimiento inesperado. ¿Administrar el crecimiento de datos y de registro en un día a día con el crecimiento automático. Si se usa el crecimiento automático, previamente agrandar el archivo con el cambio de tamaño.

- Asegúrese de que la **reducción automática** está deshabilitada para evitar la sobrecarga innecesaria que puede afectar el rendimiento.

- Mover todas las bases de datos a los discos de datos, incluyendo bases de datos del sistema. Para obtener más información, vea [Mover las bases de datos](https://msdn.microsoft.com/library/ms345408.aspx).

- Mover directorios de archivos de registro y seguimiento de los errores de SQL Server a discos de datos. Esto puede realizarse en el Administrador de configuración de SQL Server haciendo clic en la instancia de SQL Server y seleccione Propiedades. La configuración del archivo de registro y seguimiento de error se puede cambiar en la ficha **Parámetros de inicio** . Directorio de descarga se especifica en la ficha **Opciones avanzadas** . La siguiente captura de pantalla muestra dónde debe buscar el parámetro de inicio de registro de errores.

    ![Captura de pantalla de registro de errores SQL](./media/virtual-machines-windows-sql-performance/sql_server_error_log_location.png)

- Configurar ubicaciones de archivo de copia de seguridad y base de datos predeterminado. Utilice las recomendaciones de este tema y realice los cambios en la ventana de propiedades del servidor. Para obtener instrucciones, vea [Ver o cambiar las ubicaciones predeterminadas para los datos y archivos de registro (SQL Server Management Studio)](https://msdn.microsoft.com/library/dd206993.aspx). La siguiente captura de pantalla muestra dónde se puede realizar estos cambios.

    ![Archivos de registro de datos de SQL y copia de seguridad](./media/virtual-machines-windows-sql-performance/sql_server_default_data_log_backup_locations.png)

- Habilitar páginas bloqueadas reducir IO y las actividades de paginación. Para obtener más información, vea [Habilitar las páginas de bloqueo en la opción de memoria (Windows)](https://msdn.microsoft.com/library/ms190730.aspx).

- Si está ejecutando SQL Server 2012, instale Service Pack 1 acumulado actualización 10. Esta actualización incluye la solución de bajo rendimiento en i/OS al ejecutar select en la instrucción de la tabla temporal en SQL Server 2012. Para obtener más información, consulte este [artículo de knowledge base](http://support.microsoft.com/kb/2958012).

- Considere la posibilidad de comprimir los archivos de datos cuando se transfieren o fuera de Azure.

## <a name="feature-specific-guidance"></a>Guía de características específica

Algunas implementaciones pueden lograr los beneficios de rendimiento adicionales con técnicas más avanzadas de configuración. La siguiente lista destaca algunas características de SQL Server que le pueden ayudar a mejorar el rendimiento:

- **Copia de seguridad de almacenamiento de Azure**: al realizar copias de seguridad para SQL Server que se ejecuta en máquinas virtuales de Windows Azure, puede usar [Copias de seguridad de SQL Server a la dirección URL](https://msdn.microsoft.com/library/dn435916.aspx). Esta característica está disponible a partir de SQL Server 2012 SP1 CU2 y recomendado para las copias de los discos de datos adjuntos. Cuando se copia de seguridad y restauración de almacenamiento de Azure, siga las recomendaciones proporcionadas en [Copia de seguridad de SQL Server a la dirección URL de los procedimientos recomendados y solución de problemas y restauración de copias de seguridad se almacenan en el almacenamiento de Azure](https://msdn.microsoft.com/library/jj919149.aspx). También puede automatizar estas copias de seguridad mediante [Copia de seguridad automática para SQL Server en Azure máquinas virtuales de Windows](virtual-machines-windows-classic-sql-automated-backup.md).

    Antes de SQL Server 2012, puede usar la [Copia de seguridad de SQL Server a la herramienta de Azure](https://www.microsoft.com/download/details.aspx?id=40740). Esta herramienta puede ayudar a mejorar el rendimiento de copia de seguridad con varios destinos de copia de seguridad de las bandas.

- **Archivos de datos de SQL Server en Azure**: esta característica nueva, [Archivos de datos de SQL Server en Azure](https://msdn.microsoft.com/library/dn385720.aspx), está disponible a partir de SQL Server 2014. Ejecuta SQL Server con archivos de datos de Azure muestra las características de rendimiento comparable como mediante los discos de datos de Azure.

## <a name="next-steps"></a>Pasos siguientes

Si está interesado en exploración de SQL Server y almacenamiento Premium en más profundidad, vea el artículo [Usar el almacenamiento de Premium con SQL Server en máquinas virtuales de Windows Azure](virtual-machines-windows-classic-sql-server-premium-storage.md).

Prácticas recomendadas de seguridad, vea [Consideraciones de seguridad de SQL Server en Azure máquinas virtuales de Windows](virtual-machines-windows-sql-security.md).

Revise otros temas de máquina Virtual de SQL Server en [SQL Server en Introducción a máquinas virtuales de Windows Azure](virtual-machines-windows-sql-server-iaas-overview.md).
