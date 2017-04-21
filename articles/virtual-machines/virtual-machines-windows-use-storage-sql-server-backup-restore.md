<properties
    pageTitle="Cómo usar el almacenamiento de Azure para copia de seguridad de SQL Server y restaurar | Microsoft Azure"
    description="Obtenga información sobre cómo realizar copias de seguridad SQL Server con el almacenamiento de Azure. Explica las ventajas de la copia de seguridad de las bases de datos SQL con el almacenamiento de Azure."
    services="virtual-machines-windows"
    documentationCenter=""
    authors="MikeRayMSFT"
    manager="jhubbard"
    tags="azure-service-management"/>

<tags
    ms.service="virtual-machines-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="vm-windows-sql-server"
    ms.workload="infrastructure-services"
    ms.date="07/22/2016"
    ms.author="mikeray"/>

# <a name="use-azure-storage-for-sql-server-backup-and-restore"></a>Usar el almacenamiento de Azure para restaurar y copia de seguridad SQL Server

## <a name="overview"></a>Información general

A partir de SQL Server 2012 SP1 CU2, ahora puede escribir copias de seguridad de SQL Server directamente al servicio de almacenamiento de blobs de Windows Azure. Puede usar esta funcionalidad para realizar copias de seguridad y restaurar desde el servicio de blobs de Windows Azure con una base de datos de SQL Server local o una base de datos de SQL Server en una máquina virtual Azure. Copia de seguridad en la nube ofrece ventajas de disponibilidad, ilimitada almacenamiento fuera del sitio replicar geo y facilitar la migración de datos a y desde la nube. Puede emitir instrucciones de copia de seguridad o restaurar mediante SMO o Transact-SQL.

SQL Server 2016 presenta nuevas capacidades; Puede utilizar [copia de seguridad de instantánea de archivos](http://msdn.microsoft.com/library/mt169363.aspx) para realizar copias de seguridad casi instantáneas y restaura muy rápido.

En este tema se explica por qué puede optar por usar el almacenamiento de Azure copias de seguridad de SQL y, a continuación, se describen los componentes implicados. Puede usar los recursos que se proporcionan al final de este artículo para obtener acceso a información adicional para empezar a usar este servicio con las copias de seguridad de SQL Server y tutoriales.

## <a name="benefits-of-using-the-azure-blob-service-for-sql-server-backups"></a>Ventajas de usar el servicio de blobs de Windows Azure copias de seguridad de SQL Server

Existen varios desafíos que se enfrentan cuando la copia de seguridad de SQL Server. Estos desafíos incluyen la administración de almacenamiento, los riesgos de errores de almacenamiento, acceso al almacenamiento fuera del sitio y configuración de hardware. Muchos de estos desafíos se tratan usando el servicio de almacenamiento de blobs de Windows Azure copias de seguridad de SQL Server. Tenga en cuenta las siguientes ventajas:

- **Facilidad de uso**: almacenar las copias de seguridad en Azure BLOB puede ser una cómoda, flexible y facilitar el acceso a la opción fuera del sitio. Crear almacenamiento fuera del sitio para las copias de seguridad de SQL Server puede ser tan fácil como modificar sus secuencias de comandos o trabajos existentes para usar la sintaxis de la **Copia de seguridad a la dirección URL** . Almacenamiento fuera del sitio normalmente debería suficientemente desde la ubicación de la base de datos de producción para evitar que un desastre único que puede afectar a las ubicaciones base de datos externa y de producción. Si elige [geo replicar su Azure blobs](../storage/storage-redundancy.md), tiene una capa adicional de protección en caso de desastre que puede afectar a la región toda.
- **Archivo de copia de seguridad**: servicio el almacenamiento de blobs de Windows Azure ofrece una alternativa mejor que la opción de cinta utilizados con frecuencia para almacenar las copias de seguridad. Almacenamiento de cinta podría requerir transporte físico a un servicio fuera del sitio y medidas para proteger los archivos multimedia. Almacenamiento de las copias de seguridad en el almacenamiento de blobs de Windows Azure proporciona una instantánea, altamente disponibles y una resistentes archivado opción.
- **Hardware administradas**: no hay ninguna sobrecarga de administración de hardware con los servicios de Azure. Servicios de Azure administración el hardware y proporcionan replicación geo redundancia y protección contra errores de hardware.
- **Almacenamiento ilimitado**: al activar una copia de seguridad directa a Azure BLOB, tendrá acceso a almacenamiento prácticamente ilimitado. Como alternativa, copia de seguridad en un disco de Azure máquina virtual tiene límites basados en el tamaño del equipo. Hay un límite para el número de discos que puede adjuntar a una máquina virtual Azure copias de seguridad. Este límite es de 16 discos de una instancia de gran tamaño y menos instancias más pequeño.
- **Disponibilidad de copia de seguridad**: copias de seguridad almacenadas en Azure BLOB están disponibles desde cualquier lugar y en cualquier momento y se puede acceder fácilmente para restaura a un servidor local SQL Server u otro servidor de SQL Server ejecutando en un equipo de Azure Virtual, sin necesidad de base de datos adjuntar o desasociar o descargar y adjuntar el disco duro virtual.
- **Costo**: pagar solo para el servicio que se usa. Puede ser rentable como una opción de archivo de copia de seguridad y fuera del sitio. Consulte la [Calculadora de precio de Azure](http://go.microsoft.com/fwlink/?LinkId=277060 "Calculadora de precios")y el [artículo de precios de Azure](http://go.microsoft.com/fwlink/?LinkId=277059 "precios artículo") para obtener más información.
- **Instantáneas de almacenamiento**: cuando se almacenan los archivos de base de datos en un blobs de Windows Azure y que está usando SQL Server 2016, puede usar [copia de seguridad de instantánea de archivos](http://msdn.microsoft.com/library/mt169363.aspx) para realizar copias de seguridad casi instantáneas y restaura muy rápido.

Para obtener más detalles, consulte [copia de seguridad de SQL Server y restaurar con el servicio de almacenamiento de blobs de Windows Azure](http://go.microsoft.com/fwlink/?LinkId=271617).

Las dos secciones siguientes presentan el servicio de almacenamiento de blobs de Windows Azure, incluidos los componentes de SQL Server necesarios. Es importante conocer los componentes y su interacción correctamente utilizar copia de seguridad y restauración del servicio de almacenamiento de blobs de Windows Azure.

## <a name="azure-blob-storage-service-components"></a>Componentes de servicio de almacenamiento de blobs de Windows Azure

Los siguientes componentes de Azure se usan cuando una copia de seguridad del servicio de almacenamiento de blobs de Windows Azure.

| Componente               | Descripción                          |
|---------------------|-------------------------------|
| **Cuenta de almacenamiento** | La cuenta de almacenamiento es el punto de partida para todos los servicios de almacenamiento. Para obtener acceso a un servicio de almacenamiento de blobs de Windows Azure, cree primero una cuenta de almacenamiento de Azure. Para obtener más información acerca del servicio de almacenamiento de blobs de Windows Azure, consulte [cómo usar el servicio de almacenamiento de blobs de Windows Azure](https://azure.microsoft.com/develop/net/how-to-guides/blob-storage/) |
| **Contenedor** | Un contenedor proporciona una agrupación de un conjunto de BLOB y puede almacenar una cantidad ilimitada de BLOB. Para escribir un SQL Server copia de seguridad en un servicio de blobs de Windows Azure, debe tener al menos el contenedor raíz creado. |
| **BLOB** | Un archivo de cualquier tipo y tamaño. BLOB es accesible con el siguiente formato de dirección URL: **https://[storage account].blob.core.windows.net/[container]/[blob]**. Para obtener más información acerca de Blobs de página, vea [Descripción bloque y Blobs de página](http://msdn.microsoft.com/library/azure/ee691964.aspx) |

## <a name="sql-server-components"></a>Componentes de SQL Server

Los siguientes componentes de SQL Server se usan cuando una copia de seguridad del servicio de almacenamiento de blobs de Windows Azure.

| Componente               | Descripción                          |
|---------------------|-------------------------------|
| **DIRECCIÓN URL** | Una dirección URL especifica un identificador uniforme de recursos (URI) a un único archivo de copia de seguridad. La dirección URL se usa para proporcionar la ubicación y el nombre del archivo de copia de seguridad de SQL Server. La dirección URL debe apuntar a un objeto binario real, no solo un contenedor. Si el blob no existe, se crea. Si un blob existente se especifica, se produce un error en la copia de seguridad, a menos que la > opción con el formato especificado. El siguiente es un ejemplo de la dirección URL que especificaría en el comando de copia de seguridad: **http[s]://[storageaccount].blob.core.windows.net/[container]/[FILENAME.bak]**. Se recomienda HTTPS, aunque no es necesario. |
| **Credenciales** | La información que necesita para conectarse y autenticarse al servicio de almacenamiento de blobs de Windows Azure se almacena como una credencial.  En orden para SQL Server escribir copias de seguridad en un blobs de Windows Azure o la restauración de él, debe crear una credencial de SQL Server. Para obtener más información, vea [Credenciales de SQL Server](https://msdn.microsoft.com/library/ms189522.aspx). |

> [AZURE.NOTE] Si decide copiar y cargar un archivo de copia de seguridad en el servicio de almacenamiento de blobs de Windows Azure, debe usar un tipo de blobs de página como la opción de almacenamiento si planea usar este archivo para operaciones de restauración. Restaurar desde un tipo de blobs de bloque se producirá un error.

## <a name="next-steps"></a>Pasos siguientes

1. Crear una cuenta de Azure si aún no tiene una. Si va a evaluar Azure, considere la posibilidad de la [versión de prueba gratuita](https://azure.microsoft.com/free/).

1. A continuación, vaya a través de uno de los siguientes tutoriales que le guiarán a través de la creación de una cuenta de almacenamiento y la restauración.

    - **SQL Server 2014**: [Tutorial: copia de seguridad SQL Server 2014 y restaurar a Microsoft Azure Blob servicio de almacenamiento en](https://msdn.microsoft.com/library/jj720558\(v=sql.120\).aspx).
    - **SQL Server 2016**: [Tutorial: usar el servicio de almacenamiento de blobs de Microsoft Azure con bases de datos de SQL Server 2016](https://msdn.microsoft.com/library/dn466438.aspx)

1. Revise la documentación adicional que comienzan con [copia de seguridad de SQL Server y restaurar con el servicio de almacenamiento de blobs de Microsoft Azure](https://msdn.microsoft.com/library/jj919148.aspx).

Si tiene algún problema, revise el tema de la [Copia de seguridad de SQL Server a la dirección URL de mejores prácticas y solución de problemas](https://msdn.microsoft.com/library/jj919149.aspx).

Para otros SQL Server hacer copia de seguridad y restaurar las opciones, consulte [copia de seguridad y restauración de SQL Server en Azure máquinas virtuales de Windows](../virtual-machines/virtual-machines-windows-sql-backup-recovery.md).
