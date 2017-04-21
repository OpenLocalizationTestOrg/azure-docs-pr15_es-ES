<properties
    pageTitle="Hacer copia de seguridad y restauración de SQL Server | Microsoft Azure"
    description="Describe las consideraciones de copia de seguridad y restauración de las bases de datos de SQL Server que se ejecuta en Azure máquinas virtuales de Windows."
    services="virtual-machines-windows"
    documentationCenter="na"
    authors="rothja"
    manager="jhubbard"
    editor=""
    tags="azure-resource-management" />

<tags
    ms.service="virtual-machines-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="vm-windows-sql-server"
    ms.workload="infrastructure-services"
    ms.date="08/19/2016"
    ms.author="jroth" />

# <a name="backup-and-restore-for-sql-server-in-azure-virtual-machines"></a>Copia de seguridad y restauración de SQL Server en máquinas virtuales de Windows Azure

## <a name="overview"></a>Información general

Copia de datos en bases de datos de SQL Server es una parte importante de la estrategia de protección contra la pérdida de datos debido a errores de aplicación o de usuario. Esto ocurre igualmente para SQL Server que se ejecuta en Azure máquinas virtuales de Windows (VM).

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

Para SQL Server en ejecución en máquinas virtuales de Azure, puede usar copia de seguridad original y restaurar técnicas con discos conectados para el destino de los archivos de copia de seguridad. Sin embargo, hay un límite para el número de discos que puede adjuntar a una máquina virtual Azure, en función del [tamaño de la máquina virtual](virtual-machines-linux-sizes.md). También es la sobrecarga de administración de discos para tener en cuenta.

A partir de SQL Server 2014, puede realizar copias de seguridad y restaurar al almacenamiento de blobs de Microsoft Azure. SQL Server 2016 también proporciona mejoras para esta opción. Además, los archivos de base de datos almacenados en el almacenamiento de blobs de Azure de Microsoft SQL Server 2016 proporciona una opción de casi instantánea copias de seguridad y restauración rápida mediante instantáneas de Azure. Este artículo proporciona una descripción general de estas opciones y se puede encontrar información adicional en [copia de seguridad de SQL Server y restaurar con el servicio de almacenamiento de blobs de Microsoft Azure](https://msdn.microsoft.com/library/jj919148.aspx).

>[AZURE.NOTE] Para obtener una descripción de las opciones para una copia de seguridad muy grandes bases de datos, consulte [Estrategias de copia de seguridad de base de datos multiterabyte SQL Server para Azure máquinas virtuales de Windows](http://blogs.msdn.com/b/igorpag/archive/2015/07/28/multi-terabyte-sql-server-database-backup-strategies-for-azure-virtual-machines.aspx).

Las siguientes secciones incluyen información específica sobre las distintas versiones de SQL Server compatibles con una máquina virtual Azure.

## <a name="sql-server-virtual-machines"></a>Máquinas virtuales SQL Server

Cuando la instancia de SQL Server se ejecuta en una máquina Virtual de Azure, los archivos de base de datos ya residen en discos de datos en Azure. Estos discos live en el almacenamiento de blobs de Windows Azure. Los motivos de copia de la base de datos y los enfoques Eche un vistazo cambiar ligeramente. Tenga en cuenta lo siguiente. 

- Ya no necesita realizar copias de seguridad de base de datos para proporcionar protección contra errores de hardware o de medios porque Microsoft Azure proporciona esta protección como parte del servicio de Microsoft Azure.

- Necesita realizar copias de seguridad de base de datos para proporcionar protección contra errores de usuario o para fines archivados, motivos normativos o fines administrativos.

- Puede almacenar el archivo de copia de seguridad directamente en Azure. Para obtener más información, vea las secciones siguientes que proporcionan pautas para las distintas versiones de SQL Server.

## <a name="sql-server-2016"></a>SQL Server 2016

Microsoft SQL Server 2016 es compatible con características de [copia de seguridad y restauración con Azure BLOB](https://msdn.microsoft.com/library/jj919148.aspx) que se encuentran en SQL Server 2014. Pero también incluye las siguientes mejoras:

| Mejora de 2016               | Detalles                          |
|---------------------|-------------------------------|
| **Bandas**              | Cuando una copia de seguridad para el almacenamiento de blobs de Windows Azure, SQL Server 2016 admite las copias de varios BLOB para habilitar la copia de seguridad de bases de datos grandes, hasta un máximo de 12,8 TB.      |
| **Copia de seguridad instantánea**                | Mediante el uso de instantáneas de Azure, copia de seguridad instantánea de archivo de SQL Server proporciona casi instantáneas copias de seguridad y restaura rápido para archivos de base de datos almacenados con el servicio de almacenamiento de blobs de Windows Azure. Esta capacidad le permite simplificar la copia de seguridad y restaurar las directivas. Instantánea del archivo de copia de seguridad también admite punto en restaurar de tiempo. Para obtener más información, consulte [Copias de seguridad de instantánea para los archivos de base de datos de Azure](https://msdn.microsoft.com/library/mt169363%28v=sql.130%29.aspx).   |
| **Administrar la programación de copia de seguridad**            | Copia de seguridad SQL Server administra a Azure admite ahora programaciones personalizadas. Para obtener más información, vea [Copia de seguridad SQL Server administra a Microsoft Azure](https://msdn.microsoft.com/library/dn449496.aspx).   |

Para obtener un tutorial de las capacidades de SQL Server 2016 cuando se usa el almacenamiento de blobs de Windows Azure, consulte [Tutorial: usar el servicio de almacenamiento de blobs de Microsoft Azure con bases de datos de SQL Server 2016](https://msdn.microsoft.com/library/dn466438.aspx).

## <a name="sql-server-2014"></a>SQL Server de 2014

SQL Server 2014 incluye las siguientes mejoras:

1. **Copia de seguridad y restauración en Azure**:

 - *Copia de seguridad de SQL Server a la dirección URL* ahora admite en SQL Server Management Studio. La opción para realizar una copia de seguridad de Azure ahora está disponible cuando se utiliza la tarea de copia de seguridad o restaurar o el Asistente para planes de mantenimiento en SQL Server Management Studio. Para obtener más información, vea [Copia de seguridad de SQL Server a la dirección URL](https://msdn.microsoft.com/library/jj919148%28v=sql.120%29.aspx).
 - *SQL Server administra una copia de seguridad en Azure* tiene una nueva funcionalidad que permite la administración de copia de seguridad automática. Esto es especialmente útil para automatizar la administración de copia de seguridad para instancias de SQL Server 2014 que se ejecutan en un equipo de Azure. Para obtener más información, vea [Copia de seguridad SQL Server administra a Microsoft Azure](https://msdn.microsoft.com/library/dn449496%28v=sql.120%29.aspx).
 - *Copia de seguridad automática* proporciona automatización adicionales para habilitar automáticamente la *Copia de seguridad SQL Server administra a Azure* en todas las bases de datos nuevos y existentes para una VM de SQL Server en Azure. Para obtener más información, consulte [Copia de seguridad automática para SQL Server en Azure máquinas virtuales de Windows](virtual-machines-windows-sql-automated-backup.md).
 - Para obtener información general de todas las opciones de copia de seguridad de SQL Server 2014 a Azure, consulte [copia de seguridad de SQL Server y restaurar con el servicio de almacenamiento de blobs de Microsoft Azure](https://msdn.microsoft.com/library/jj919148%28v=sql.120%29.aspx).

1. **Cifrado**: SQL Server 2014 admite el cifrado de datos al crear una copia de seguridad. Admite varios algoritmos de cifrado y el uso osf un certificado o clave asimétrico. Para obtener más información, vea [Cifrado de copia de seguridad](https://msdn.microsoft.com/library/dn449489%28v=sql.120%29.aspx).

## <a name="sql-server-2012"></a>SQL Server 2012

Para obtener información detallada sobre la copia de seguridad de SQL Server y restauración de SQL Server 2012, vea [copia de seguridad y restauración de servidor de bases de datos SQL (SQL Server 2012)](https://msdn.microsoft.com/library/ms187048%28v=sql.110%29.aspx).

A partir de SQL Server 2012 SP1 actualización acumulativa 2, puede realizar copias de seguridad y restauración del servicio de almacenamiento de blobs de Windows Azure. Esta mejora puede usarse para hacer la copia de seguridad de las bases de datos de SQL Server en un servidor SQL que se ejecuta en una máquina Virtual de Azure o una instancia local. Para obtener más información, consulte [copia de seguridad de SQL Server y restaurar con el servicio de almacenamiento de blobs de Windows Azure](https://msdn.microsoft.com/library/jj919148%28v=sql.110%29.aspx).

Algunas de las ventajas de usar el servicio de almacenamiento de blobs de Windows Azure incluyen la capacidad para omitir el límite de 16 disco de discos conectados, facilidad de administración, la disponibilidad del archivo de copia de seguridad a otra instancia de la instancia de SQL Server que se ejecuta en una máquina virtual Azure o una instancia local con fines de recuperación de desastres o migración directa. Para obtener una lista completa de los beneficios para usar un servicio de almacenamiento de blobs de Windows Azure copias de seguridad de SQL Server, consulte la sección *ventajas* de [copia de seguridad de SQL Server y restaurar con el servicio de almacenamiento de blobs de Windows Azure](https://msdn.microsoft.com/library/jj919148%28v=sql.110%29.aspx).

Mejores prácticas recomendadas e información de solución de problemas, consulte [copia de seguridad y restaurar los procedimientos recomendados (servicio de almacenamiento de blobs de Windows Azure)](https://msdn.microsoft.com/library/jj919149%28v=sql.110%29.aspx).

## <a name="sql-server-2008"></a>SQL Server 2008

Para copia de seguridad de SQL Server y restaurar en SQL Server 2008 R2, vea [realizar copias de seguridad y restauración de bases de datos de SQL Server (SQL Server 2008 R2)](https://msdn.microsoft.com/library/ms187048%28v=sql.105%29.aspx).

Para copia de seguridad de SQL Server y restaurar en SQL Server 2008, vea [realizar copias de seguridad y restauración de bases de datos de SQL Server (SQL Server 2008)](https://msdn.microsoft.com/library/ms187048%28v=sql.100%29.aspx).

## <a name="next-steps"></a>Pasos siguientes

Si planea la implementación de SQL Server en una máquina virtual de Azure, puede encontrar instrucciones de aprovisionamiento en el siguiente tutorial: [hacer el aprovisionamiento de un equipo Virtual con SQL Server en Azure con el Administrador de recursos de Azure](virtual-machines-windows-portal-sql-server-provision.md).

Aunque copia de seguridad y restauración pueden utilizarse para migrar los datos, hay más fácil potencialmente rutas de migración de datos a SQL Server en una máquina virtual de Azure. Para obtener una descripción completa de las opciones de migración y recomendaciones, vea [migrar una base de datos de SQL Server en una máquina virtual de Azure](virtual-machines-windows-migrate-sql.md).

Revise otros [recursos para ejecutar SQL Server en Azure máquinas virtuales de Windows](virtual-machines-windows-sql-server-iaas-overview.md).
