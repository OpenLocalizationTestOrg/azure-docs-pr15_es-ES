<properties
   pageTitle="Realizar copias de seguridad del almacén de datos SQL | Microsoft Azure"
   description="Obtenga información sobre las copias de seguridad de base de datos integrados de almacén de datos de SQL que le permiten restaurar un almacén de datos de SQL Azure en un punto de restauración o una región geográfica distinta."
   services="sql-data-warehouse"
   documentationCenter=""
   authors="lakshmi1812"
   manager="barbkess"
   editor="monicar"/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="10/06/2016"
   ms.author="lakshmir;barbkess"/>

# <a name="sql-data-warehouse-backups"></a>Realizar copias de seguridad del almacén de datos SQL

Almacén de datos SQL ofrece copias de seguridad locales y geográficas como parte de sus capacidades de copia de seguridad de almacén de datos. Se incluyen instantáneas de blobs de Windows Azure almacenamiento y almacenamiento geo redundantes. Utilice las copias de seguridad del almacén de datos para restaurar el almacén de datos a un punto de restauración en el área principal, o a una región geográfica distinta. En este artículo se explica los detalles de las copias de seguridad en el almacén de datos de SQL.

## <a name="what-is-a-data-warehouse-backup"></a>¿Qué es una copia de seguridad del almacén de datos?

Una copia de seguridad del almacén de datos es los datos que puede usar para restaurar un almacén de datos a una hora específica.  Dado que el almacén de datos de SQL es un sistema distribuido, una copia de seguridad del almacén de datos consta de varios archivos que se almacenan en Azure BLOB. 

Realizar copias de seguridad de base de datos son una parte esencial de cualquier estrategia de recuperación de desastres y continuidad empresarial porque protegen los datos de daños accidentales o eliminación. Para obtener más información, vea [información general de continuidad empresarial](../sql-database/sql-database-business-continuity.md).

## <a name="data-redundancy"></a>Redundancia de datos

Almacén de datos SQL protege los datos al almacenar los datos en localmente redundantes (LRS) el almacenamiento de Azure Premium. Esta característica de almacenamiento de Azure almacena varias copias sincrónicos de los datos en el centro de datos local para garantizar la protección de datos transparente si hay errores localizados. Redundancia de datos garantiza que los datos pueden superar los problemas de infraestructura como errores de disco. Redundancia de datos garantiza la continuidad empresarial con una tolerancia y la infraestructura de alta disponibilidad.

Para obtener más información sobre:

- Almacenamiento de Azure Premium, vea [Introducción al almacenamiento de Azure Premium](../storage/storage-premium-storage.md).
- Almacenamiento redundante local, consulte [la replicación de almacenamiento de Azure](../storage/storage-redundancy.md#locally-redundant-storage).


## <a name="azure-storage-blob-snapshots"></a>Instantáneas de almacenamiento blobs de Windows Azure

Como una ventaja de usar el almacenamiento de Azure Premium, almacén de datos SQL usa instantáneas de blobs de Windows Azure almacenamiento para hacer copia de seguridad del almacén de datos local. Puede restaurar un almacén de datos en un punto de restauración instantánea. Instantáneas iniciar un mínimo de cada ocho horas y están disponibles para los siete días.  

Para obtener más información sobre:

- Instantáneas de blobs de Windows Azure, vea [crear una instantánea de blobs de Windows](../storage/storage-blob-snapshots.md).


## <a name="geo-redundant-backups"></a>Realizar copias de seguridad geo redundantes

Cada 24 horas, almacén de datos SQL almacena el almacén de datos completa en almacenamiento estándar. Se crea el almacén de datos completa para que coincida con la hora de la última instantánea. El almacenamiento estándar pertenece a una cuenta de almacenamiento geo redundantes con acceso de lectura (RA GRS). La característica de almacenamiento de Azure RA-GRS aplica los archivos de copia de seguridad a un [Centro de datos pareja](../best-practices-availability-paired-regions.md). Esta replicación de geo garantiza que puede restaurar el almacén de datos en caso de que no puede acceder a las instantáneas en su región principal. 

Esta característica está activada de forma predeterminada. Si no desea utilizar geo redundantes copias de seguridad, puede cambiar de opinión. 

>[AZURE.NOTE] En el almacenamiento de Azure, la *replicación* de términos se refiere a copiar archivos de una ubicación a otra. *Replicación de base de datos* SQL se refiere a mantener a varias bases de datos secundarios sincronizados con una base de datos principal. 

Para obtener más información sobre:
- Almacenamiento geo redundantes, vea [replicación de almacenamiento de Azure](../storage/storage-redundancy.md).
- Almacenamiento de GRS RA, vea [almacenamiento geo redundantes de acceso de lectura](../storage/storage-redundancy.md#read-access-geo-redundant-storage).

## <a name="data-warehouse-backup-schedule-and-retention-period"></a>Programación de copia de seguridad de almacén de datos y el período de retención

Almacén de datos SQL crea instantáneas en el almacén de datos en línea de cada horas de cuatro a ocho y conserva cada instantánea siete días. Para restaurar la base de datos en línea a uno de los puntos de restauración de los siete últimos días. 

Para ver cuando inicia la última instantánea, ejecutar esta consulta en el almacén de datos de SQL en línea. 

```sql
select top 1 *
from sys.pdw_loader_backup_runs 
order by run_id desc;
```

Si necesita conservar una instantánea durante más de siete días, puede restaurar un punto de restauración a un nuevo almacén de datos. Una vez finalizada la restauración, almacén de datos SQL comienza a crear instantáneas en el almacén de datos nueva. Si no realizar cambios en el almacén de datos nuevo, las instantáneas estar vacías y, por tanto, el costo de instantánea es mínimo. También puede pausar la base de datos para mantener el almacén de datos de SQL de creación de instantáneas.


### <a name="what-happens-to-my-backup-retention-while-my-data-warehouse-is-paused"></a>¿Qué pasa con mi copia de seguridad retención mientras está en pausa mi almacenamiento de datos?

Almacén de datos SQL no crea instantáneas y no caduca instantáneas mientras está en pausa un almacén de datos. Mientras está en pausa el almacén de datos no cambia la edad de instantánea. Retención de instantánea se basa en el número de días que del almacén de datos está en línea, no días del calendario.

Por ejemplo, si una instantánea empieza el 1 de octubre a 4 p.m. y el almacén de datos está en pausa 3 de octubre a 4 p.m., la instantánea es dos días de antigüedad. Siempre que el almacén de datos vuelve a estar conectado la instantánea es dos días de antigüedad. Si el almacén de datos entra en línea 5 de octubre a 4 p.m., la instantánea de dos días de antigüedad y permanece durante más de cinco días.

Cuando conecte el almacén de datos, almacén de datos SQL reanuda nuevas instantáneas y expira instantáneas cuando tengan más de siete días de datos.

### <a name="how-long-is-the-retention-period-for-a-dropped-data-warehouse"></a>¿Cuánto tiempo dura el período de retención para un almacén de datos colocados?
Cuando se quita un almacén de datos, el almacén de datos y las instantáneas se guardan para siete días y, a continuación, se elimina. Puede restaurar el almacén de datos a cualquiera de los puntos de restauración guardados.

> [AZURE.IMPORTANT] Si elimina una instancia de SQL server lógica, todas las bases de datos que pertenecen a la instancia también se eliminan y no se pueden recuperar. No puede restaurar un servidor eliminado.

## <a name="data-warehouse-backup-costs"></a>Costos de copia de seguridad de almacén de datos

El costo total de los siete días de instantáneas de blobs de Windows Azure y el almacén de datos principal se redondea a la más cercano TB. Por ejemplo, si el almacén de datos es 1,5 TB y las instantáneas usar 100 GB, de facturación de 2 TB de datos a una velocidad de almacenamiento de Azure Premium. 

>[AZURE.NOTE] Cada instantánea está vacía inicialmente y crece a medida que realiza cambios en el almacén de datos principal. Todas las instantáneas aumentan tamaño como los cambios del almacén de datos. Por lo tanto, los costos de almacenamiento de instantáneas crecen según la velocidad de cambio.

Si está utilizando almacenamiento geo redundantes, recibirá un cargo de almacenamiento independiente. El almacenamiento geo redundantes se cargarán a la tasa estándar de almacenamiento geográfico redundantes de acceso de lectura (RA GRS).

Para obtener más información acerca de precios de almacén de datos de SQL, vea [Precios de almacén de datos de SQL](https://azure.microsoft.com/pricing/details/sql-data-warehouse/).

## <a name="using-database-backups"></a>Uso de copias de seguridad de base de datos

El uso principal de copias de seguridad del almacén de datos SQL es restaurar el almacén de datos a uno de los puntos de restauración dentro del período de retención.  

- Para restaurar un almacén de datos SQL, vea [restaurar un almacén de datos SQL](sql-data-warehouse-restore-database-overview.md).


## <a name="related-topics"></a>Temas relacionados

### <a name="scenarios"></a>Escenarios

- Para ver una introducción de continuidad empresarial, vea [información general de continuidad empresarial](../sql-database/sql-database-business-continuity.md)


<!-- ### Tasks -->

- Para restaurar un almacén de datos, vea [restaurar un almacén de datos SQL](sql-data-warehouse-restore-database-overview.md)

<!-- ### Tutorials -->

