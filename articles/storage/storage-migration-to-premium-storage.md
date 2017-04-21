<properties
    pageTitle="Migrar a almacenamiento de Azure Premium | Microsoft Azure"
    description="Migrar las máquinas virtuales existentes para el almacenamiento de Azure Premium. Almacenamiento de Premium ofrece soporte de disco de alto rendimiento, baja latencia para las cargas de trabajo O-intensa ejecuta en Azure máquinas virtuales de Windows."
    services="storage"
    documentationCenter="na"
    authors="aungoo-msft"
    manager="tadb"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/21/2016"
    ms.author="yuemlu"/>


# <a name="migrating-to-azure-premium-storage"></a>Migrar a almacenamiento de Azure Premium

## <a name="overview"></a>Información general

Almacenamiento de Azure Premium ofrece soporte de disco de alto rendimiento, baja latencia para máquinas virtuales que ejecutan cargas de trabajo O-intensiva. Puede tomar ventaja de la velocidad y el rendimiento de estos discos migrar discos de VM de la aplicación para el almacenamiento de Azure Premium.

La finalidad de esta guía es ayudar a los nuevos usuarios de almacenamiento de Azure Premium mejor a prepararse para realizar una transición suave desde su sistema actual al almacenamiento Premium. La guía trata de tres de los componentes clave de este proceso: 

  - [Plan para la migración a almacenamiento de Premium](#plan-the-migration-to-premium-storage)
  - [Preparar y copiar discos duro virtuales (VHD) al almacenamiento de Premium](#prepare-and-copy-virtual-hard-disks-VHDs-to-premium-storage)
  - [Crear la máquina Virtual de Azure mediante almacenamiento Premium](#create-azure-virtual-machine-using-premium-storage)

Puede migrar máquinas virtuales de otras plataformas para el almacenamiento de Azure Premium o migrar máquinas virtuales de Azure existentes de almacenamiento estándar al almacenamiento Premium. Esta guía enumeran los pasos para ambos dos escenarios. Siga los pasos especificados en la sección correspondiente dependiendo de su situación.

>[AZURE.NOTE] Puede encontrar una introducción a las características y precios de almacenamiento Premium en almacenamiento Premium: [Almacenamiento de alto rendimiento para cargas de trabajo de máquina Virtual de Azure](storage-premium-storage.md). Se recomienda migrar cualquier disco de máquina virtual que requieren alta IOPS para el almacenamiento de Azure Premium para mejorar el rendimiento de la aplicación. Si el disco no requiere IOPS alta, puede limitar los costos mediante el mantenimiento de almacenamiento estándar, que almacena los datos de disco de máquina virtual en unidades de disco duro (disco duro) en lugar de SSDs.

Completar el proceso de migración en su totalidad requieran acciones adicionales antes y después de los pasos proporcionados en esta guía. Por ejemplo, configurar redes virtuales o extremos o realicen cambios en el código en la misma aplicación que puede requerir el tiempo de inactividad en la aplicación. Estas acciones son únicas para cada aplicación y que deben completarse junto con los pasos proporcionados en esta guía para realizar la transición completa al almacenamiento Premium tan transparente como sea posible.


## <a name="plan-the-migration-to-premium-storage"></a>Plan para la migración a almacenamiento de Premium

Esta sección se asegura de que esté listo para seguir los pasos de migración en este artículo y le ayudará a tomar la decisión mejor en tipos de máquina virtual y disco.

### <a name="prerequisites"></a>Requisitos previos
- Necesitará una suscripción de Azure. Si no tiene una, puede crear una suscripción de [prueba gratuita](https://azure.microsoft.com/pricing/free-trial/) de un mes o visite [Precios de Azure](https://azure.microsoft.com/pricing/) para ver más opciones.
- Para ejecutar los cmdlets de PowerShell, necesitará el módulo de PowerShell de Microsoft Azure. Vea [cómo instalar y configurar Azure PowerShell](../powershell-install-configure.md) para las instrucciones de instalación y del punto de instalación.
- Cuando planea usar máquinas virtuales de Azure ejecuta en Storage Premium, debe usar las máquinas virtuales capaces de almacenamiento Premium. Puede usar discos estándar y almacenamiento Premium con máquinas virtuales capaces de almacenamiento Premium. Discos de almacenamiento de Premium estará disponibles con más tipos VM en el futuro. Para obtener más información sobre todos los tipos de disco de Azure VM y tamaños disponibles, vea [tamaños para máquinas virtuales](../virtual-machines/virtual-machines-windows-sizes.md) y [tamaños de los servicios en la nube](../cloud-services/cloud-services-sizes-specs.md).

### <a name="considerations"></a>Consideraciones

Una máquina virtual de Azure admite asociar varios discos de almacenamiento de Premium para que las aplicaciones pueden tener hasta 64 TB de almacenamiento por máquina virtual. Con el almacenamiento de Premium, las aplicaciones pueden lograr 80.000 IOPS (operaciones de entrada y salida por segundo) por VM y 2000 MB por segundo rendimiento del disco por máquina virtual con latencia muy baja para operaciones de lectura. Dispone de opciones en una gran variedad de máquinas virtuales y discos. Esta sección es ayudarle a encontrar una opción que mejor se adapte a su carga de trabajo.

#### <a name="vm-sizes"></a>Tamaños VM
Las especificaciones de tamaño de Azure VM aparecen en [tamaños para máquinas virtuales de Windows](../virtual-machines/virtual-machines-windows-sizes.md). Revise las características de rendimiento de máquinas virtuales que funcionan con almacenamiento Premium y elija el tamaño de memoria virtual más adecuado que mejor se adapte a su carga de trabajo. Asegúrese de que hay suficiente ancho de banda disponible en la máquina virtual para dirigir el tráfico de disco.


#### <a name="disk-sizes"></a>Tamaños de disco
Existen tres tipos de discos que se pueden usar con la máquina virtual y cada una tiene IOPs específicos y rendimiento límites. Tenga en cuenta estos límites al elegir el tipo de disco para su máquina virtual según las necesidades de la aplicación en términos de capacidad, rendimiento y escalabilidad, y la carga máxima.

|Tipo de disco de almacenamiento de Premium|P10|P20|P30|
|:---:|:---:|:---:|:---:|
|Tamaño del disco|128 GB|512 GB|1.024 GB (1 TB)|
|IOPS por disco|500|2300|5000|
|Rendimiento por disco|100 MB por segundo|150 MB por segundo|200 MB por segundo|

Dependiendo de su carga de trabajo, determine si los discos de datos adicionales son necesarios para su máquina virtual. Puede adjuntar varios discos de datos persistentes a su máquina virtual. Si es necesario, puede bandas de los discos para aumentar la capacidad y el rendimiento del volumen. (Consulte ¿Qué es bandas de disco [aquí](storage-premium-storage-performance.md#disk-striping).) Si bandas discos de datos de almacenamiento de Premium con [Espacios de almacenamiento][4], debe configurar con una columna para cada disco que se utiliza. En caso contrario, el rendimiento general del volumen seccionado podrán menor de lo esperado debido a una irregular distribución de tráfico entre los discos. Puede usar la utilidad *mdadm* para lograr los mismos para máquinas virtuales de Linux. Consulte el artículo [Configurar RAID de Software en Linux](../virtual-machines/virtual-machines-linux-configure-raid.md) para obtener información detallada.

#### <a name="storage-account-scalability-targets"></a>Destinos de escalabilidad de cuenta de almacenamiento
Cuentas de almacenamiento Premium tienen los siguientes destinos escalabilidad además de los [objetivos de rendimiento y escalabilidad de almacenamiento de Azure](storage-scalability-targets.md). Si los requisitos de la aplicación superen los objetivos de escalabilidad de una cuenta de almacenamiento única, generar la aplicación para utilizar varias cuentas de almacenamiento y dividir los datos en las cuentas de almacenamiento.

|Capacidad total de cuenta|Ancho de banda total de una cuenta de almacenamiento redundante local|
|:--|:---|
|La capacidad de disco: 35TB<br />Capacidad de instantánea: 10 TB|Hasta 50 gigabits por segundo para entrante + saliente|

Para el más información sobre las especificaciones de almacenamiento Premium, consulte [escalabilidad y objetivos de rendimiento al utilizar almacenamiento Premium](storage-premium-storage.md#scalability-and-performance-targets-when-using-premium-storage).

#### <a name="disk-caching-policy"></a>Directiva de caché de disco
De forma predeterminada, directiva de caché de disco es *De solo lectura* para todos los datos de Premium discos y *Lectura y escritura* para el disco de sistema operativo Premium adjuntan a la máquina virtual. Esta configuración se recomienda para lograr un rendimiento óptimo para IOs de la aplicación. Para los discos de datos visible de escritura o sólo escritura (por ejemplo, archivos de registro de SQL Server), deshabilitar la caché de disco, por lo que puede lograr un mejor rendimiento de la aplicación. La configuración de caché para discos de datos existentes puede actualizarse con el parámetro *- HostCaching* del cmdlet *Set-AzureDataDisk* o [Portal de Azure](https://portal.azure.com) .

#### <a name="location"></a>Ubicación
Seleccione una ubicación donde el almacenamiento de Azure Premium está disponible. Vea [Servicios de Azure por región](https://azure.microsoft.com/regions/#services) para información actualizada sobre ubicaciones disponibles. Máquinas virtuales que se encuentra en la misma región como la cuenta de almacenamiento que almacena los discos de VM dará un rendimiento mucho mejor que si están en áreas independientes.

#### <a name="other-azure-vm-configuration-settings"></a>Otras opciones de configuración de la máquina virtual de Azure
Al crear una máquina virtual de Azure, le pedirá que configurar algunas configuraciones de máquina virtual. Recuerde que algunas opciones de configuración se corrigen para la duración de la máquina virtual, aunque puede modificar o agregar otras personas más adelante. Revise estos ajustes de configuración de Azure VM y asegúrese de que estos están configurados correctamente para que coincidan con los requisitos de carga de trabajo.

### <a name="optimization"></a>Optimización

[El almacenamiento de azure Premium: diseño de alto rendimiento](storage-premium-storage-performance.md) proporciona instrucciones para la creación de aplicaciones de alto rendimiento con el almacenamiento de Azure Premium. Siga las directrices combinadas con rendimiento mejores prácticas aplicables a tecnologías utilizadas por la aplicación.


## <a name="prepare-and-copy-virtual-hard-disks-VHDs-to-premium-storage"></a>Preparar y copiar discos duro virtuales (VHD) al almacenamiento de Premium

La siguiente sección proporciona directrices para preparar VHD desde la VM y copiar VHD en el almacenamiento de Azure.

- [Escenario 1: "Estoy migrar máquinas virtuales de Azure existentes al almacenamiento de Azure Premium".](#scenario1)
- [Escenario 2: "Estoy migrar máquinas virtuales desde otras plataformas para el almacenamiento de Azure Premium".](#scenario2)

### <a name="prerequisites"></a>Requisitos previos

Para preparar el VHD para la migración, deberá:

- Una suscripción de Azure, una cuenta de almacenamiento y un contenedor en dicha cuenta a la que puede copiar su disco duro virtual. Observe que la cuenta de almacenamiento de destino puede ser una cuenta estándar o Premium almacenamiento según sus necesidades.
- Herramienta generalizar el disco duro virtual si tiene previsto crear varias instancias de máquina virtual de ella. Por ejemplo, sysprep para Windows o virt sysprep para Ubuntu.
- Herramienta para cargar el archivo de disco duro virtual a la cuenta de almacenamiento. Consulte [transferir datos con la herramienta de línea de comandos de AzCopy](storage-use-azcopy.md) o utilice un [Explorador de almacenamiento de Azure](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/03/11/windows-azure-storage-explorers-2014.aspx). Esta guía describe copiar su disco duro virtual mediante la herramienta AzCopy.

> [AZURE.NOTE] Si elige la opción de copiar sincrónico con AzCopy, para obtener un rendimiento óptimo, copie su disco duro virtual, ejecute una de estas herramientas desde una máquina virtual de Azure que está en la misma región como la cuenta de almacenamiento de destino. Si está copiando un disco duro virtual desde una máquina virtual de Azure en una región diferente, el rendimiento puede ser menor.
>
> Para copiar una gran cantidad de datos a través de ancho de banda limitado, considere la posibilidad de [usando el servicio de importación o exportación de Azure para transferir datos al almacenamiento de blobs](storage-import-export-service.md); Esto le permite transferir los datos por unidades de disco duro envío a un centro de datos de Azure. Puede usar el servicio de importación o exportación de Azure para copiar datos a una cuenta de almacenamiento estándar solo. Una vez que los datos están en su cuenta de almacenamiento estándar, puede usar la [API de blobs de copia](https://msdn.microsoft.com/library/azure/dd894037.aspx) o AzCopy para transferir los datos a su cuenta de almacenamiento premium.
>
> Tenga en cuenta que Microsoft Azure solo es compatible con archivos de disco duro virtual de tamaño fijo. Archivos VHDX o VHD dinámicos no es compatibles. Si tiene un disco duro virtual dinámico, puede convertir a tamaño fijo mediante el cmdlet de [Disco duro virtual de convertir](http://technet.microsoft.com/library/hh848454.aspx) .

### <a name="scenario1"></a>Escenario 1: "Estoy migrar máquinas virtuales de Azure existentes al almacenamiento de Azure Premium".

Si va a migrar máquinas virtuales de Azure existentes, detener la máquina virtual, preparar VHD por el tipo de disco duro virtual que desee y, a continuación, copie el disco duro virtual con AzCopy o PowerShell.

La máquina virtual debe estar completamente hacia abajo para migrar un estado limpio. Habrá un tiempo de inactividad hasta que se complete la migración.

#### <a name="step-1-prepare-vhds-for-migration"></a>Paso 1. Preparar el VHD para la migración

Si va a migrar máquinas virtuales de Azure existentes al almacenamiento Premium, puede ser su disco duro virtual:

- Una imagen de sistema operativo generalizado
- Un disco de sistema operativo únicos
- Un disco de datos

A continuación, le guiaremos a través de estos 3 escenarios para preparar su disco duro virtual.

##### <a name="use-a-generalized-operating-system-vhd-to-create-multiple-vm-instances"></a>Usar un disco duro virtual de sistema operativo generalizado para crear varias instancias VM

Si va a cargar un disco duro virtual que se usará para crear varias instancias de Azure VM genéricos, primero debe generalize disco duro virtual mediante una utilidad sysprep. Esto se aplica a un disco duro virtual que es local o en la nube. Sysprep quita cualquier información específica del equipo desde el disco duro virtual.

>[AZURE.IMPORTANT] Tomar una instantánea o copia de seguridad de la máquina virtual antes de generalización. Ejecutar sysprep detendrá y desasignar la instancia de máquina virtual. Siga los pasos a continuación para sysprep un sistema operativo Windows, disco duro virtual. Tenga en cuenta que ejecute el comando Sysprep requieren apagar la máquina virtual. Para obtener más información acerca de Sysprep, vea [Información general de Sysprep](http://technet.microsoft.com/library/hh825209.aspx) o [Referencia técnica de Sysprep](http://technet.microsoft.com/library/cc766049.aspx).

1. Abra una ventana de símbolo del sistema como administrador.
2. Escriba el comando siguiente para abrir Sysprep:

        %windir%\system32\sysprep\sysprep.exe

3. En la herramienta de preparación del sistema, seleccione sistema escriba predefinidas de configuración rápida, active la casilla de verificación Generalize, seleccione **apagado**y, a continuación, haga clic en **Aceptar**, como se muestra en la imagen siguiente. Sysprep se generalizar el sistema operativo y cerrar el sistema.

    ![][1]

Para una VM Ubuntu, use sysprep virt para lograr la misma. Consulte [virt sysprep](http://manpages.ubuntu.com/manpages/precise/man1/virt-sysprep.1.html) para obtener más detalles. Vea también algunos del [aprovisionamiento de servidores Linux software](http://www.cyberciti.biz/tips/server-provisioning-software.html) de código abierto para otros sistemas operativos Linux.

##### <a name="use-a-unique-operating-system-vhd-to-create-a-single-vm-instance"></a>Usar un único disco duro virtual de sistema operativo para crear una sola instancia VM

Si tiene una aplicación que se ejecuta en la máquina virtual que requiere los datos específicos del equipo, no generalice el disco duro virtual. Un disco duro virtual no generalizado puede utilizarse para crear una instancia de Azure VM única. Por ejemplo, si tiene el controlador de dominio en el disco duro virtual, ejecute sysprep le será ineficaz como un controlador de dominio. Revise las aplicaciones que se ejecutan en la máquina virtual y el impacto de la ejecución de sysprep en ellas antes de generalización el disco duro virtual.

##### <a name="register-data-disk-vhd"></a>Registrar datos disco disco duro virtual

Si tiene discos de datos en Azure que deben migrarse, asegúrese de que se cierran las máquinas virtuales que utilizan estos discos de datos.

Siga los pasos descritos a continuación para copiar el disco duro virtual para el almacenamiento de Azure Premium y registrar como un disco de preparación de datos.

#### <a name="step-2-create-the-destination-for-your-vhd"></a>Paso 2. Crear el destino de su disco duro virtual

Crear una cuenta de almacenamiento para mantener el VHD. Al planear dónde desea almacenar el VHD, tenga en cuenta los siguientes puntos:

- La cuenta de almacenamiento Premium de destino.
- La ubicación de la cuenta de almacenamiento debe ser igual que el almacenamiento de Premium con capacidades de Azure máquinas virtuales se creará en la fase final. Puede copiar a una nueva cuenta de almacenamiento o el plan para usar la misma cuenta de almacenamiento según sus necesidades.
- Copie y guarde la clave de cuenta de almacenamiento de la cuenta de destino de almacenamiento para la siguiente etapa.

Para los discos de datos, puede elegir mantener algunos discos de datos en una cuenta de almacenamiento estándar (por ejemplo, los discos que tienen almacenamiento refrigerador), pero le recomendamos encarecidamente que mover todos los datos de carga de trabajo de producción utilizar el almacenamiento de premium.

#### <a name="copy-vhd-with-azcopy-or-powershell"></a>Paso 3. Copiar el disco duro virtual con AzCopy o PowerShell

Debe buscar la clave de cuenta de almacenamiento y la ruta de acceso de contenedor para procesar cualquiera de estas dos opciones. Clave de cuenta de almacenamiento y la ruta de acceso de contenedor puede encontrarse en el **Portal de Azure** > **almacenamiento**. La dirección URL del contenedor será como "https://myaccount.blob.core.windows.net/mycontainer/".

##### <a name="option-1-copy-a-vhd-with-azcopy-asynchronous-copy"></a>Opción 1: Copiar un disco duro virtual con AzCopy (copia asincrónica)

Usa AzCopy, puede cargar el disco duro virtual fácilmente en Internet. Dependiendo del tamaño de los VHD, esto puede llevar tiempo. No olvide comprobar los límites de almacenamiento cuenta entrada/salida al usar esta opción. Para obtener más información, vea [destinos de rendimiento y escalabilidad de almacenamiento de Azure](storage-scalability-targets.md) .

1. Descargue e instale AzCopy desde aquí: [versión más reciente de AzCopy](http://aka.ms/downloadazcopy)
2. Abra el PowerShell de Azure y vaya a la carpeta donde está instalado AzCopy.
3. Utilice el comando siguiente para copiar el archivo de disco duro virtual de "Origen" de "Destino".

        AzCopy /Source: <source> /SourceKey: <source-account-key> /Dest: <destination> /DestKey: <dest-account-key> /BlobType:page /Pattern: <file-name>

    Ejemplo:

        AzCopy /Source:https://sourceaccount.blob.core.windows.net/mycontainer1 /SourceKey:key1 /Dest:https://destaccount.blob.core.windows.net/mycontainer2 /DestKey:key2 /Pattern:abc.vhd

    Estas son las descripciones de los parámetros que se usan en el comando AzCopy:

 - * */Source: * &lt;origen&gt;:*** ubicación de la carpeta o la dirección URL de contenedor de almacenamiento que contiene el disco duro virtual.
 - * */SourceKey: * &lt;clave de cuenta de origen&gt;:*** clave de cuenta de almacenamiento de la cuenta de almacenamiento de origen.
 - * */Dest: * &lt;destino&gt;:*** dirección URL del contenedor de almacenamiento para copiar el disco duro virtual a.
 - * */DestKey: * &lt;clave de cuenta dest&gt;:*** clave de cuenta de almacenamiento de la cuenta de almacenamiento de destino.
 - * */Patrón: * &lt;nombre de archivo&gt;:*** especificar el nombre de archivo del disco duro virtual para copiar.

Para obtener información detallada sobre el uso de la herramienta de AzCopy, consulte [transferir datos con la herramienta de línea de comandos de AzCopy](storage-use-azcopy.md).

##### <a name="option-2-copy-a-vhd-with-powershell-synchronized-copy"></a>Opción 2: Copiar un disco duro virtual con PowerShell (copia sincronizada)

También puede copiar el archivo de disco duro virtual mediante el cmdlet de PowerShell AzureStorageBlobCopy de inicio. Utilice el comando siguiente en Azure PowerShell para copiar el disco duro virtual. Reemplazar los valores de <> con los valores correspondientes de la cuenta de almacenamiento de origen y de destino. Para usar este comando, debe tener un contenedor llamado VHD de su cuenta de almacenamiento de destino. Si el contenedor no existe, debe crearla antes de ejecutar el comando.

    $sourceBlobUri = <source-vhd-uri>

    $sourceContext = New-AzureStorageContext  –StorageAccountName <source-account> -StorageAccountKey <source-account-key>

    $destinationContext = New-AzureStorageContext  –StorageAccountName <dest-account> -StorageAccountKey <dest-account-key>

    Start-AzureStorageBlobCopy -srcUri $sourceBlobUri -SrcContext $sourceContext -DestContainer <dest-container> -DestBlob <dest-disk-name> -DestContext $destinationContext

Ejemplo:

    C:\PS> $sourceBlobUri = "https://sourceaccount.blob.core.windows.net/vhds/myvhd.vhd"

    C:\PS> $sourceContext = New-AzureStorageContext  –StorageAccountName "sourceaccount" -StorageAccountKey "J4zUI9T5b8gvHohkiRg"

    C:\PS> $destinationContext = New-AzureStorageContext  –StorageAccountName "destaccount" -StorageAccountKey "XZTmqSGKUYFSh7zB5"

    C:\PS> Start-AzureStorageBlobCopy -srcUri $sourceBlobUri -SrcContext $sourceContext -DestContainer "vhds" -DestBlob "myvhd.vhd" -DestContext $destinationContext

### <a name="scenario2"></a>Escenario 2: "Estoy migrar máquinas virtuales desde otras plataformas para el almacenamiento de Azure Premium".

Si va a migrar disco duro virtual de almacenamiento Cloud que no sean de Azure en Azure, primero debe exportar el disco duro virtual en un directorio local. La ruta de acceso de origen completa del directorio local donde el disco duro virtual se almacena útiles y eligiendo AzCopy cargar con el almacenamiento de Azure.

#### <a name="step-1-export-vhd-to-a-local-directory"></a>Paso 1. Exportar el disco duro virtual a un directorio local

##### <a name="copy-a-vhd-from-aws"></a>Copiar un disco duro virtual de AWS

1. Si está utilizando AWS, exporte la instancia EC2 a un disco duro virtual en un depósito de Amazon S3. Siga los pasos descritos en la documentación de Amazon para exportar Amazon EC2 instancias instalar la herramienta de línea de comandos de interfaz de Amazon EC2 y ejecute el comando Crear instancia exportar tarea para exportar la instancia EC2 a un archivo de disco duro virtual. Asegúrese de usar el **disco duro virtual** para el disco & #95; imagen & #95; Variable de formato al ejecutar el comando **Crear instancia-exportación de tarea** . El archivo exportado del disco duro virtual se guarda en el cubo de Amazon S3 que designar durante ese proceso.

        aws ec2 create-instance-export-task --instance-id ID --target-environment TARGET_ENVIRONMENT \
        --export-to-s3-task DiskImageFormat=DISK_IMAGE_FORMAT,ContainerFormat=ova,S3Bucket=BUCKET,S3Prefix=PREFIX

2. Descargar el archivo de disco duro virtual desde el depósito S3. Seleccione el archivo de disco duro virtual, a continuación, **acciones** > **Descargar**.

    ![][3]

##### <a name="copy-a-vhd-from-other-non-azure-cloud"></a>Copiar un disco duro virtual de otro nube no Azure

Si va a migrar disco duro virtual de almacenamiento Cloud que no sean de Azure en Azure, primero debe exportar el disco duro virtual en un directorio local. Copiar la ruta de acceso de origen completa del directorio local donde está almacenado el disco duro virtual.

##### <a name="copy-a-vhd-from-on-premise"></a>Copiar un disco duro virtual de local

Si va a migrar disco duro virtual desde un entorno local, tendrá la ruta de acceso de origen completa donde está almacenado el disco duro virtual. La ruta de origen podría ser un recurso compartido de ubicación o el archivo de servidor.

#### <a name="step-2-create-the-destination-for-your-vhd"></a>Paso 2. Crear el destino de su disco duro virtual

Crear una cuenta de almacenamiento para mantener el VHD. Al planear dónde desea almacenar el VHD, tenga en cuenta los siguientes puntos:

- La cuenta de almacenamiento de destino podría ser estándar o premium almacenamiento según el requisito de la aplicación.
- La región de la cuenta de almacenamiento debe ser igual que el almacenamiento de Premium con capacidades de Azure máquinas virtuales se creará en la fase final. Puede copiar a una nueva cuenta de almacenamiento o el plan para usar la misma cuenta de almacenamiento según sus necesidades.
- Copie y guarde la clave de cuenta de almacenamiento de la cuenta de destino de almacenamiento para la siguiente etapa.

Le recomendamos encarecidamente que mover todos los datos de carga de trabajo de producción utilizar el almacenamiento de premium.

#### <a name="step-3-upload-the-vhd-to-azure-storage"></a>Paso 3. Cargar el disco duro virtual para el almacenamiento de Azure

Ahora que ya tiene su disco duro virtual en el directorio local, puede usar AzCopy o AzurePowerShell para cargar el archivo .vhd en el almacenamiento de Azure. Ambas opciones se proporcionan a continuación:

##### <a name="option-1-using-azure-powershell-add-azurevhd-to-upload-the-vhd-file"></a>Opción 1: Usar Azure de PowerShell agregar AzureVhd para cargar el archivo .vhd

    Add-AzureVhd [-Destination] <Uri> [-LocalFilePath] <FileInfo>

Un ejemplo <Uri> podría ser **_"https://storagesample.blob.core.windows.net/mycontainer/blob1.vhd"_**. Un ejemplo <FileInfo> podría ser **_"C:\path\to\upload.vhd"_**.

##### <a name="option-2-using-azcopy-to-upload-the-vhd-file"></a>Opción 2: Usar AzCopy para cargar el archivo .vhd

Usa AzCopy, puede cargar el disco duro virtual fácilmente en Internet. Dependiendo del tamaño de los VHD, esto puede llevar tiempo. No olvide comprobar los límites de almacenamiento cuenta entrada/salida al usar esta opción. Para obtener más información, vea [destinos de rendimiento y escalabilidad de almacenamiento de Azure](storage-scalability-targets.md) .

1. Descargue e instale AzCopy desde aquí: [versión más reciente de AzCopy](http://aka.ms/downloadazcopy)
2. Abra el PowerShell de Azure y vaya a la carpeta donde está instalado AzCopy.
3. Utilice el comando siguiente para copiar el archivo de disco duro virtual de "Origen" de "Destino".

        AzCopy /Source: <source> /SourceKey: <source-account-key> /Dest: <destination> /DestKey: <dest-account-key> /BlobType:page /Pattern: <file-name>

    Ejemplo:

        AzCopy /Source:https://sourceaccount.blob.core.windows.net/mycontainer1 /SourceKey:key1 /Dest:https://destaccount.blob.core.windows.net/mycontainer2 /DestKey:key2 /BlobType:page /Pattern:abc.vhd

    Estas son las descripciones de los parámetros que se usan en el comando AzCopy:

 - * */Source: * &lt;origen&gt;:*** ubicación de la carpeta o la dirección URL de contenedor de almacenamiento que contiene el disco duro virtual.
 - * */SourceKey: * &lt;clave de cuenta de origen&gt;:*** clave de cuenta de almacenamiento de la cuenta de almacenamiento de origen.
 - * */Dest: * &lt;destino&gt;:*** dirección URL del contenedor de almacenamiento para copiar el disco duro virtual a.
 - * */DestKey: * &lt;clave de cuenta dest&gt;:*** clave de cuenta de almacenamiento de la cuenta de almacenamiento de destino.
 - **/BlobType: página:** Especifica que el destino es un blob de página.
 - * */Patrón: * &lt;nombre de archivo&gt;:*** especificar el nombre de archivo del disco duro virtual para copiar.

Para obtener información detallada sobre el uso de la herramienta de AzCopy, consulte [transferir datos con la herramienta de línea de comandos de AzCopy](storage-use-azcopy.md).

##### <a name="other-options-for-uploading-a-vhd"></a>Otras opciones para cargar un disco duro virtual

También puede cargar un disco duro virtual a su cuenta de almacenamiento mediante una de las siguientes maneras:

- [Almacenamiento de Azure copiar Blob API](https://msdn.microsoft.com/library/azure/dd894037.aspx)
- [Almacenamiento de Azure Explorer cargar BLOB](https://azurestorageexplorer.codeplex.com/)
- [Referencia de la API de REST de servicio de almacenamiento importar o exportar](https://msdn.microsoft.com/library/dn529096.aspx)

>[AZURE.NOTE] Se recomienda usar el servicio de importación o exportación si estimado cargar tiempo tiene más de 7 días. Puede usar [DataTransferSpeedCalculator](https://github.com/Azure-Samples/storage-dotnet-import-export-job-management/blob/master/DataTransferSpeedCalculator.html) para calcular el tiempo de unidad de tamaño y la transferencia de datos.
>
> Importar o exportar se puede utilizar para copiar a una cuenta de almacenamiento estándar. Debe copiar de almacenamiento estándar a la cuenta de almacenamiento premium mediante una herramienta como AzCopy.


## <a name="create-azure-virtual-machine-using-premium-storage"></a>Crear máquinas virtuales de Azure mediante almacenamiento Premium

Después de que el disco duro virtual está cargado o copiado a la cuenta de almacenamiento deseada, siga las instrucciones de esta sección para registrar el disco duro virtual como una imagen de sistema operativo o un disco de sistema operativo dependiendo de su situación y, a continuación, crear una instancia de máquina virtual de. El disco de datos del disco duro virtual puede estar asociado a la máquina virtual después de crearlo. Se proporciona una secuencia de comandos de migración de ejemplo al final de esta sección. Esta secuencia de comandos simple no coincide con todos los escenarios. Debe actualizar la secuencia de comandos que coincida con su situación específica. Para ver si esta secuencia de comandos se aplica a su situación, consulte a continuación [Una secuencia de comandos de migración](#a-sample-migration-script).

### <a name="checklist"></a>Lista de comprobación

1.  Espere hasta que todos los discos de disco duro virtual copiar está completa.
2.  Asegúrese de que el almacenamiento de Premium está disponible en la región que va a migrar.
3.  Decida la nueva serie de máquina virtual que se va a utilizar. Debe ser un sistema de almacenamiento Premium capaz y el tamaño debe ser según la disponibilidad de la región y según sus necesidades.
4.  Decida el tamaño VM exacto que se usa. Tamaño de la máquina virtual debe ser suficientemente grande para admitir el número de discos de datos que tiene. Por ejemplo Si tiene 4 discos de datos, la máquina virtual debe tener 2 o más núcleos. Además, considere la posibilidad de procesamiento power, memoria y ancho de banda necesita.
5.  Crear una cuenta de almacenamiento Premium en la región de destino. Esta es la cuenta que se usa para la nueva máquina virtual.
6.  Tiene los detalles VM actuales útiles, incluida la lista de discos y BLOB de disco duro virtual correspondiente.

Preparar la aplicación para el tiempo de inactividad. Para realizar una migración limpia, debe detener todo el procesamiento en el sistema actual. Solo, a continuación, puede ir a estado coherente que puede migrar a la nueva plataforma. Duración del tiempo de inactividad dependerá de la cantidad de datos en los discos para migrar.

>[AZURE.NOTE] Si va a crear un administrador de recursos VM Azure desde un disco de disco duro virtual especializadas, consulte [esta plantilla](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-from-specialized-vhd) para implementar VM Administrador de recursos con disco existente.

### <a name="register-your-vhd"></a>Registrar su disco duro virtual

Para crear una máquina virtual desde el disco duro virtual de sistema operativo o adjuntar un disco de datos a una nueva máquina virtual, primero debe registrar ellos. Siga los pasos a continuación según escenario de su disco duro de virtual.

#### <a name="generalized-operating-system-vhd-to-create-multiple-azure-vm-instances"></a>Generalizado de sistema operativo del disco duro virtual para crear varias instancias de Azure VM

Después de carga imagen de sistema operativo generalizado disco duro virtual a la cuenta de almacenamiento, registrarla como una **Imagen de Azure VM** para que pueda crear una o varias instancias de máquina virtual de ella. Use los siguientes cmdlets de PowerShell para registrar su disco duro virtual como una imagen de Azure VM OS. Proporcionar la dirección URL completa del contenedor donde se ha copiado el disco duro virtual a.

    Add-AzureVMImage -ImageName "OSImageName" -MediaLocation "https://storageaccount.blob.core.windows.net/vhdcontainer/osimage.vhd" -OS Windows

Copie y guarde el nombre de esta nueva imagen VM de Azure. En el ejemplo anterior, es *NombreImagenSO*.

#### <a name="unique-operating-system-vhd-to-create-a-single-azure-vm-instance"></a>Único de disco duro virtual de sistema operativo para crear una sola instancia de Azure VM

Después de que el disco duro virtual único de sistema operativo cargado en la cuenta de almacenamiento, registrarla como un **Disco de sistema operativo de Azure** para que pueda crear una instancia de máquina virtual de ella. Utilice estos cmdlets de PowerShell para registrar su disco duro virtual como un disco de sistema operativo de Azure. Proporcionar la dirección URL completa del contenedor donde se ha copiado el disco duro virtual a.

    Add-AzureDisk -DiskName "OSDisk" -MediaLocation "https://storageaccount.blob.core.windows.net/vhdcontainer/osdisk.vhd" -Label "My OS Disk" -OS "Windows"

Copie y guarde el nombre del nuevo Azure OS disco. En el ejemplo anterior, es *OSDisk*.

#### <a name="data-disk-vhd-to-be-attached-to-new-azure-vm-instances"></a>Disco duro virtual se adjuntará a todas las instancias de Azure VM nuevos datos en disco

Después de carga el disco duro virtual de disco de datos a la cuenta de almacenamiento, registrarla como un disco de datos de Azure de modo que puede estar asociado a la nueva serie de DS, DSv2 serie o instancia GS serie Azure VM.

Utilice estos cmdlets de PowerShell para registrar su disco duro virtual como un disco de datos de Azure. Proporcionar la dirección URL completa del contenedor donde se ha copiado el disco duro virtual a.

    Add-AzureDisk -DiskName "DataDisk" -MediaLocation "https://storageaccount.blob.core.windows.net/vhdcontainer/datadisk.vhd" -Label "My Data Disk"

Copie y guarde el nombre de este nuevo disco de datos de Azure. En el ejemplo anterior, es *DataDisk*.

### <a name="create-a-premium-storage-capable-vm"></a>Crear una máquina virtual capaz de almacenamiento Premium

Una vez en la imagen de sistema operativo o disco de sistema operativo están registrados, cree una nueva serie de DS, DSv2 serie o serie GS VM. Va a utilizar la imagen de sistema operativo o el nombre del disco de sistema operativo haya registrado. Seleccione el tipo de máquina virtual en el nivel de almacenamiento de Premium. En el ejemplo siguiente, estamos utilizando el tamaño de la memoria virtual *Standard_DS2* .

>[AZURE.NOTE] Actualizar el tamaño del disco para asegurarse de que coincida con la capacidad y los requisitos de rendimiento y los tamaños de Azure disponible en el disco.

Siga los cmdlets de PowerShell de paso a paso para crear la nueva máquina virtual. En primer lugar, configure los parámetros comunes:

    $serviceName = "yourVM"
    $location = "location-name" (e.g., West US)
    $vmSize ="Standard_DS2"
    $adminUser = "youradmin"
    $adminPassword = "yourpassword"
    $vmName ="yourVM"
    $vmSize = "Standard_DS2"

Primero, cree un servicio de nube en la que va a alojar su nuevas máquinas virtuales.

    New-AzureService -ServiceName $serviceName -Location $location

A continuación, dependiendo de su situación, cree la instancia de Azure VM desde la imagen del sistema operativo o en el disco de sistema operativo que registrado.

#### <a name="generalized-operating-system-vhd-to-create-multiple-azure-vm-instances"></a>Generalizado de sistema operativo del disco duro virtual para crear varias instancias de Azure VM

Crear la uno o más DS serie Azure VM instancias con la **Imagen de sistema operativo de Azure** que registrado. Especifique este nombre de la imagen del sistema operativo en la configuración de la máquina virtual al crear nueva máquina virtual tal como se muestra a continuación.

    $OSImage = Get-AzureVMImage –ImageName "OSImageName"

    $vm = New-AzureVMConfig -Name $vmName –InstanceSize $vmSize -ImageName $OSImage.ImageName

    Add-AzureProvisioningConfig -Windows –AdminUserName $adminUser -Password $adminPassword –VM $vm

    New-AzureVM -ServiceName $serviceName -VM $vm

#### <a name="unique-operating-system-vhd-to-create-a-single-azure-vm-instance"></a>Único de disco duro virtual de sistema operativo para crear una sola instancia de Azure VM

Crear una nueva instancia de Azure VM de serie de DS mediante el **Disco de sistema operativo de Azure** que registrado. Especifique este nombre de disco OS en la configuración de la máquina virtual al crear la nueva máquina virtual tal como se muestra a continuación.

    $OSDisk = Get-AzureDisk –DiskName "OSDisk"

    $vm = New-AzureVMConfig -Name $vmName -InstanceSize $vmSize -DiskName $OSDisk.DiskName

    New-AzureVM -ServiceName $serviceName –VM $vm

Especifique otra información de Azure VM, como un servicio de nube, región, cuenta de almacenamiento, conjunto de disponibilidad y la directiva de almacenamiento en caché. Tenga en cuenta que la instancia de VM deben posicionar con sistemas operativos asociados o discos de datos, así que la cuenta de servicio, región y almacenamiento de nube seleccionados debe estar en la misma ubicación que los VHD subyacentes de esos discos.

### <a name="attach-data-disk"></a>Vincular datos de un disco

Por último, si ha registrado datos disco VHD, adjúntelos a la nueva máquina virtual Azure con capacidades de almacenamiento de Premium.

Usar siguiente cmdlet de PowerShell para adjuntar el disco de datos a la nueva máquina virtual y especificar la directiva de almacenamiento en caché. En el ejemplo siguiente se establece la directiva de caché en *sólo lectura*.

    $vm = Get-AzureVM -ServiceName $serviceName -Name $vmName

    Add-AzureDataDisk -ImportFrom -DiskName "DataDisk" -LUN 0 –HostCaching ReadOnly –VM $vm

    Update-AzureVM  -VM $vm

>[AZURE.NOTE] Puede haber pasos adicionales necesarios para ser compatible con la aplicación que no está estará incluido en esta guía.

### <a name="checking-and-plan-backup"></a>Comprobar y planear la copia de seguridad

Una vez que la nueva máquina virtual se marcha, obtener acceso a él con el mismo id de inicio de sesión y contraseña como la máquina virtual original y compruebe que todo funciona como se espera. La configuración, incluidos los volúmenes seccionados, sería presente en la nueva máquina virtual.

El último paso es planear la copia de seguridad y programación de mantenimiento para la nueva máquina virtual según las necesidades de la aplicación.

### <a name="a-sample-migration-script"></a>Una secuencia de comandos de migración de ejemplo

Si tiene varias máquinas virtuales para migrar, automatización a través de scripts de PowerShell que será útil. A continuación se muestra una secuencia de comandos de ejemplo que automatizan la migración de una máquina virtual. Nota que debajo de secuencia de comandos sólo es un ejemplo y hay algunos supuestos acerca de los discos VM actuales. Debe actualizar la secuencia de comandos que coincida con su situación específica.

Las hipótesis son:

- Creación de máquinas virtuales de Azure clásico.
- Los discos de sistema operativo de origen y los discos de datos de origen están en la misma cuenta de almacenamiento y el mismo contenedor. Si el sistema operativo discos y datos no están en el mismo lugar, puede usar AzCopy o Azure PowerShell para copiar VHD sobre contenedores y cuentas de almacenamiento. Consulte el paso anterior: [Disco duro virtual de copia con AzCopy o PowerShell](#copy-vhd-with-azcopy-or-powershell). Editar esta secuencia de comandos para satisfacer su situación es otra opción, pero se recomienda utilizar AzCopy o PowerShell, ya que es más fácil y rápido.

La secuencia de comandos de automatización se proporciona a continuación. Reemplazar texto con la información y actualizar la secuencia de comandos que coincida con su situación específica.

>[AZURE.NOTE] Uso de la secuencia de comandos existente no conserva la configuración de red de su origen VM. Necesitará re-configuración de la configuración de red en sus máquinas virtuales migrados.

    <#
    .Synopsis
    This script is provided as an EXAMPLE to show how to migrate a VM from a standard storage account to a premium storage account. You can customize it according to your specific requirements.

    .Description
    The script will copy the vhds (page blobs) of the source VM to the new storage account.
    And then it will create a new VM from these copied vhds based on the inputs that you specified for the new VM.
    You can modify the script to satisfy your specific requirement, but please be aware of the items specified
    in the Terms of Use section.

    .Terms of Use
    Copyright © 2015 Microsoft Corporation.  All rights reserved.

    THIS CODE AND ANY ASSOCIATED INFORMATION ARE PROVIDED “AS IS” WITHOUT WARRANTY OF ANY KIND,
    EITHER EXPRESSED OR IMPLIED, INCLUDING BUT NOT LIMITED TO THE IMPLIED WARRANTIES OF MERCHANTABILITY
    AND/OR FITNESS FOR A PARTICULAR PURPOSE. THE ENTIRE RISK OF USE, INABILITY TO USE, OR
    RESULTS FROM THE USE OF THIS CODE REMAINS WITH THE USER.

    .Example (Save this script as Migrate-AzureVM.ps1)

    .\Migrate-AzureVM.ps1 -SourceServiceName CurrentServiceName -SourceVMName CurrentVMName –DestStorageAccount newpremiumstorageaccount -DestServiceName NewServiceName -DestVMName NewDSVMName -DestVMSize "Standard_DS2" –Location “Southeast Asia”

    .Link
    To find more information about how to set up Azure PowerShell, refer to the following links.
    http://azure.microsoft.com/documentation/articles/powershell-install-configure/
    http://azure.microsoft.com/documentation/articles/storage-powershell-guide-full/
    http://azure.microsoft.com/blog/2014/10/22/migrate-azure-virtual-machines-between-storage-accounts/

    #>

    Param(
    # the cloud service name of the VM.
    [Parameter(Mandatory = $true)]
    [string] $SourceServiceName,

    # The VM name to copy.
    [Parameter(Mandatory = $true)]
    [String] $SourceVMName,

    # The destination storage account name.
    [Parameter(Mandatory = $true)]
    [String] $DestStorageAccount,

    # The destination cloud service name
    [Parameter(Mandatory = $true)]
    [String] $DestServiceName,

    # the destination vm name
    [Parameter(Mandatory = $true)]
    [String] $DestVMName,

    # the destination vm size
    [Parameter(Mandatory = $true)]
    [String] $DestVMSize,

    # the location of destination VM.
    [Parameter(Mandatory = $true)]
    [string] $Location,

    # whether or not to copy the os disk, the default is only copy data disks
    [Parameter(Mandatory = $false)]
    [Bool] $DataDiskOnly = $true,

    # how frequently to report the copy status in sceconds
    [Parameter(Mandatory = $false)]
    [Int32] $CopyStatusReportInterval = 15,

    # the name suffix to add to new created disks to avoid conflict with source disk names
    [Parameter(Mandatory = $false)]
    [String]$DiskNameSuffix = "-prem"

    ) #end param

    #######################################################################
    #  Verify Azure PowerShell module and version
    #######################################################################

    #import the Azure PowerShell module
    Write-Host "`n[WORKITEM] - Importing Azure PowerShell module" -ForegroundColor Yellow
    $azureModule = Import-Module Azure -PassThru

    if ($azureModule -ne $null)
    {
        Write-Host "`tSuccess" -ForegroundColor Green
    }
    else
    {
        #show module not found interaction and bail out
        Write-Host "[ERROR] - PowerShell module not found. Exiting." -ForegroundColor Red
        Exit
    }


    #Check the Azure PowerShell module version
    Write-Host "`n[WORKITEM] - Checking Azure PowerShell module verion" -ForegroundColor Yellow
    If ($azureModule.Version -ge (New-Object System.Version -ArgumentList "0.8.14"))
    {
        Write-Host "`tSuccess" -ForegroundColor Green
    }
    Else
    {
        Write-Host "[ERROR] - Azure PowerShell module must be version 0.8.14 or higher. Exiting." -ForegroundColor Red
        Exit
    }

    #Check if there is an azure subscription set up in PowerShell
    Write-Host "`n[WORKITEM] - Checking Azure Subscription" -ForegroundColor Yellow
    $currentSubs = Get-AzureSubscription -Current
    if ($currentSubs -ne $null)
    {
        Write-Host "`tSuccess" -ForegroundColor Green
        Write-Host "`tYour current azure subscription in PowerShell is $($currentSubs.SubscriptionName)." -ForegroundColor Green
    }
    else
    {
        Write-Host "[ERROR] - There is no valid Azure subscription found in PowerShell. Please refer to this article http://azure.microsoft.com/documentation/articles/powershell-install-configure/ to connect an Azure subscription. Exiting." -ForegroundColor Red
        Exit
    }


    #######################################################################
    #  Check if the VM is shut down
    #  Stopping the VM is a required step so that the file system is consistent when you do the copy operation.
    #  Azure does not support live migration at this time..
    #######################################################################

    if (($sourceVM = Get-AzureVM –ServiceName $SourceServiceName –Name $SourceVMName) -eq $null)
    {
        Write-Host "[ERROR] - The source VM doesn't exist in the current subscription. Exiting." -ForegroundColor Red
        Exit
    }

    # check if VM is shut down
    if ( $sourceVM.Status -notmatch "Stopped" )
    {
        Write-Host "[Warning] - Stopping the VM is a required step so that the file system is consistent when you do the copy operation. Azure does not support live migration at this time. If you’d like to create a VM from a generalized image, sys-prep the Virtual Machine before stopping it." -ForegroundColor Yellow
        $ContinueAnswer = Read-Host "`n`tDo you wish to stop $SourceVMName now? Input 'N' if you want to shut down the VM manually and come back later.(Y/N)"
        If ($ContinueAnswer -ne "Y") { Write-Host "`n Exiting." -ForegroundColor Red;Exit }
        $sourceVM | Stop-AzureVM

        # wait until the VM is shut down
        $VMStatus = (Get-AzureVM –ServiceName $SourceServiceName –Name $vmName).Status
        while ($VMStatus -notmatch "Stopped")
        {
            Write-Host "`n[Status] - Waiting VM $vmName to shut down" -ForegroundColor Green
            Sleep -Seconds 5
            $VMStatus = (Get-AzureVM –ServiceName $SourceServiceName –Name $vmName).Status
        }
    }

    # exporting the sourve vm to a configuration file, you can restore the original VM by importing this config file
    # see more information for Import-AzureVM
    $workingDir = (Get-Location).Path
    $vmConfigurationPath = $env:HOMEPATH + "\VM-" + $SourceVMName + ".xml"
    Write-Host "`n[WORKITEM] - Exporting VM configuration to $vmConfigurationPath" -ForegroundColor Yellow
    $exportRe = $sourceVM | Export-AzureVM -Path $vmConfigurationPath


    #######################################################################
    #  Copy the vhds of the source vm
    #  You can choose to copy all disks including os and data disks by specifying the
    #  parameter -DataDiskOnly to be $false. The default is to copy only data disk vhds
    #  and the new VM will boot from the original os disk.
    #######################################################################

    $sourceOSDisk = $sourceVM.VM.OSVirtualHardDisk
    $sourceDataDisks = $sourceVM.VM.DataVirtualHardDisks

    # Get source storage account information, not considering the data disks and os disks are in different accounts
    $sourceStorageAccountName = $sourceOSDisk.MediaLink.Host -split "\." | select -First 1
    $sourceStorageKey = (Get-AzureStorageKey -StorageAccountName $sourceStorageAccountName).Primary
    $sourceContext = New-AzureStorageContext –StorageAccountName $sourceStorageAccountName -StorageAccountKey $sourceStorageKey

    # Create destination context
    $destStorageKey = (Get-AzureStorageKey -StorageAccountName $DestStorageAccount).Primary
    $destContext = New-AzureStorageContext –StorageAccountName $DestStorageAccount -StorageAccountKey $destStorageKey

    # Create a container of vhds if it doesn't exist
    if ((Get-AzureStorageContainer -Context $destContext -Name vhds -ErrorAction SilentlyContinue) -eq $null)
    {
        Write-Host "`n[WORKITEM] - Creating a container vhds in the destination storage account." -ForegroundColor Yellow
        New-AzureStorageContainer -Context $destContext -Name vhds
    }


    $allDisksToCopy = $sourceDataDisks
    # check if need to copy os disk
    $sourceOSVHD = $sourceOSDisk.MediaLink.Segments[2]
    if ($DataDiskOnly)
    {
        # copy data disks only, this option requires deleting the source VM so that dest VM can boot
        # from the same vhd blob.
        $ContinueAnswer = Read-Host "`n`t[Warning] You chose to copy data disks only. Moving VM requires removing the original VM (the disks and backing vhd files will NOT be deleted) so that the new VM can boot from the same vhd. This is an irreversible action. Do you wish to proceed right now? (Y/N)"
        If ($ContinueAnswer -ne "Y") { Write-Host "`n Exiting." -ForegroundColor Red;Exit }
        $destOSVHD = Get-AzureStorageBlob -Blob $sourceOSVHD -Container vhds -Context $sourceContext
        Write-Host "`n[WORKITEM] - Removing the original VM (the vhd files are NOT deleted)." -ForegroundColor Yellow
        Remove-AzureVM -Name $SourceVMName -ServiceName $SourceServiceName

        Write-Host "`n[WORKITEM] - Waiting utill the OS disk is released by source VM. This may take up to several minutes."
        $diskAttachedTo = (Get-AzureDisk -DiskName $sourceOSDisk.DiskName).AttachedTo
        while ($diskAttachedTo -ne $null)
        {
            Start-Sleep -Seconds 10
            $diskAttachedTo = (Get-AzureDisk -DiskName $sourceOSDisk.DiskName).AttachedTo
        }

    }
    else
    {
        # copy the os disk vhd
        Write-Host "`n[WORKITEM] - Starting copying os disk $($disk.DiskName) at $(get-date)." -ForegroundColor Yellow
        $allDisksToCopy += @($sourceOSDisk)
        $targetBlob = Start-AzureStorageBlobCopy -SrcContainer vhds -SrcBlob $sourceOSVHD -DestContainer vhds -DestBlob $sourceOSVHD -Context $sourceContext -DestContext $destContext -Force
        $destOSVHD = $targetBlob
    }


    # Copy all data disk vhds
    # Start all async copy requests in parallel.
    foreach($disk in $sourceDataDisks)
    {
        $blobName = $disk.MediaLink.Segments[2]
        # copy all data disks
        Write-Host "`n[WORKITEM] - Starting copying data disk $($disk.DiskName) at $(get-date)." -ForegroundColor Yellow
        $targetBlob = Start-AzureStorageBlobCopy -SrcContainer vhds -SrcBlob $blobName -DestContainer vhds -DestBlob $blobName -Context $sourceContext -DestContext $destContext -Force
        # update the media link to point to the target blob link
        $disk.MediaLink = $targetBlob.ICloudBlob.Uri.AbsoluteUri
    }

    # Wait until all vhd files are copied.
    $diskComplete = @()
    do
    {
        Write-Host "`n[WORKITEM] - Waiting for all disk copy to complete. Checking status every $CopyStatusReportInterval seconds." -ForegroundColor Yellow
        # check status every 30 seconds
        Sleep -Seconds $CopyStatusReportInterval
        foreach ( $disk in $allDisksToCopy)
        {
            if ($diskComplete -contains $disk)
            {
                Continue
            }
            $blobName = $disk.MediaLink.Segments[2]
            $copyState = Get-AzureStorageBlobCopyState -Blob $blobName -Container vhds -Context $destContext
            if ($copyState.Status -eq "Success")
            {
                Write-Host "`n[Status] - Success for disk copy $($disk.DiskName) at $($copyState.CompletionTime)" -ForegroundColor Green
                $diskComplete += $disk
            }
            else
            {
                if ($copyState.TotalBytes -gt 0)
                {
                    $percent = ($copyState.BytesCopied / $copyState.TotalBytes) * 100
                    Write-Host "`n[Status] - $('{0:N2}' -f $percent)% Complete for disk copy $($disk.DiskName)" -ForegroundColor Green
                }
            }
        }
    }
    while($diskComplete.Count -lt $allDisksToCopy.Count)

    #######################################################################
    #  Create a new vm
    #  the new VM can be created from the copied disks or the original os disk.
    #  You can ddd your own logic here to satisfy your specific requirements of the vm.
    #######################################################################

    # Create a VM from the existing os disk
    if ($DataDiskOnly)
    {
        $vm = New-AzureVMConfig -Name $DestVMName -InstanceSize $DestVMSize -DiskName $sourceOSDisk.DiskName
    }
    else
    {
        $newOSDisk = Add-AzureDisk -OS $sourceOSDisk.OS -DiskName ($sourceOSDisk.DiskName + $DiskNameSuffix) -MediaLocation $destOSVHD.ICloudBlob.Uri.AbsoluteUri
        $vm = New-AzureVMConfig -Name $DestVMName -InstanceSize $DestVMSize -DiskName $newOSDisk.DiskName
    }
    # Attached the copied data disks to the new VM
    foreach ($dataDisk in $sourceDataDisks)
    {
        # add -DiskLabel $dataDisk.DiskLabel if there are labels for disks of the source vm
        $diskLabel = "drive" + $dataDisk.Lun
        $vm | Add-AzureDataDisk -ImportFrom -DiskLabel $diskLabel -LUN $dataDisk.Lun -MediaLocation $dataDisk.MediaLink
    }

    # Edit this if you want to add more custimization to the new VM
    # $vm | Add-AzureEndpoint -Protocol tcp -LocalPort 443 -PublicPort 443 -Name 'HTTPs'
    # $vm | Set-AzureSubnet "PubSubnet","PrivSubnet"

    New-AzureVM -ServiceName $DestServiceName -VMs $vm -Location $Location

#### <a name="optimization"></a>Optimización

Se puede personalizar la configuración actual de la máquina virtual específicamente para que funcione correctamente con discos estándares. Por ejemplo, para aumentar el rendimiento con muchos discos en un volumen seccionado. Por ejemplo, en lugar de utilizar 4 discos Premium almacenamiento por separado, es posible que pueda optimizar el costo haciendo que un único disco. Las optimizaciones como esta necesidad corregirse cada caso y requieren pasos personalizados después de la migración. Además, tenga en cuenta que este proceso puede no funcionar bien para bases de datos y las aplicaciones que dependen del diseño de disco que se define en la configuración.

##### <a name="preparation"></a>Preparación

1.  Completar la migración Simple como se describe en la sección anterior. Las optimizaciones se realizará en la nueva máquina virtual después de la migración.
2.  Definir los nuevos tamaños de disco necesarios para una configuración optimizada.
3.  Determinar la asignación de los discos o volúmenes actuales para las nuevas especificaciones de disco.

##### <a name="execution-steps"></a>Pasos de ejecución

1.  Crear nuevos discos con los tamaños de la derecha en la máquina virtual de almacenamiento de Premium.
2.  Inicie sesión en la máquina virtual y copiar los datos del volumen actual al nuevo disco que asigna a ese volumen. Hacer esto para todos los volúmenes actuales que es necesario asignar a un nuevo disco.
3.  A continuación, cambiar la configuración de la aplicación para cambiar a los nuevos discos y separar los volúmenes antiguos.

Para ajustar la aplicación para mejorar el rendimiento de disco, consulte [Optimizar el rendimiento de la aplicación](storage-premium-storage-performance.md#optimizing-application-performance).

### <a name="application-migrations"></a>Migraciones de aplicaciones

Bases de datos y otras aplicaciones complejas pueden requerir pasos especiales definida por el proveedor de aplicación para la migración. Consulte la documentación correspondiente de la aplicación. Por ejemplo Normalmente se pueden migrar bases de datos a través de la copia de seguridad y restauración.

## <a name="next-steps"></a>Pasos siguientes

Consulte los siguientes recursos para escenarios específicos para migrar máquinas virtuales:

- [Migrar Azure máquinas virtuales entre cuentas de almacenamiento](https://azure.microsoft.com/blog/2014/10/22/migrate-azure-virtual-machines-between-storage-accounts/)
- [Crear y cargar un disco duro virtual de Windows Server en Azure.](../virtual-machines/virtual-machines-windows-classic-createupload-vhd.md)
- [Crear y cargar un disco duro Virtual que contiene el sistema operativo Linux](../virtual-machines/virtual-machines-linux-classic-create-upload-vhd.md)
- [Migración de máquinas virtuales de Amazon AWS a Microsoft Azure](http://channel9.msdn.com/Series/Migrating-Virtual-Machines-from-Amazon-AWS-to-Microsoft-Azure)

Además, consulte los siguientes recursos para obtener más información sobre el almacenamiento de Azure y Azure máquinas virtuales de Windows:

- [Almacenamiento de Azure](https://azure.microsoft.com/documentation/services/storage/)
- [Máquinas virtuales de Windows Azure](https://azure.microsoft.com/documentation/services/virtual-machines/)
- [Almacenamiento de Premium: El almacenamiento de alto rendimiento para cargas de trabajo de Azure Máquina Virtual](storage-premium-storage.md)

[1]:./media/storage-migration-to-premium-storage/migration-to-premium-storage-1.png
[2]:./media/storage-migration-to-premium-storage/migration-to-premium-storage-1.png
[3]:./media/storage-migration-to-premium-storage/migration-to-premium-storage-3.png
[4]: http://technet.microsoft.com/library/hh831739.aspx
