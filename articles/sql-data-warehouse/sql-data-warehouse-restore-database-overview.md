<properties
   pageTitle="Restauración de almacén de datos SQL | Microsoft Azure"
   description="Descripción general de las opciones de restauración de la base de datos para recuperar una base de datos en el almacén de datos de SQL Azure."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="Lakshmi1812"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="09/29/2016"
   ms.author="lakshmir;barbkess;sonyama"/>


# <a name="sql-data-warehouse-restore"></a>Restauración de almacén de datos SQL

> [AZURE.SELECTOR]
- [Información general][]
- [Portal][]
- [PowerShell][]
- [REST][]

Almacén de datos SQL ofrece restaura local y geográfica como parte de su desastre del almacén de datos capacidades de recuperación. Utilice las copias de seguridad del almacén de datos para restaurar el almacén de datos en un punto de restauración en el área principal, o realizar copias de seguridad geo redundantes restaurar a una región geográfica distinta. En este artículo se explica los detalles de la restauración de un almacén de datos.

## <a name="what-is-a-data-warehouse-restore"></a>¿Qué es una restauración de almacén de datos?

Una restauración de almacén de datos es un nuevo almacén de datos que se crea una copia de seguridad de una existente o un almacén de datos eliminados. El almacén de datos restaurados vuelve a crea el almacén de datos de copia de seguridad en un momento determinado. Dado que el almacén de datos de SQL es un sistema distribuido, se crea una restauración de almacén de datos de muchos archivos de copia de seguridad que se almacenan en Azure BLOB. 

Restaurar base de datos es una parte esencial de cualquier estrategia de recuperación de desastres y continuidad empresarial porque vuelve a crear los datos después de daños accidentales o eliminación.

Para obtener más información, consulte:

-  [Realizar copias de seguridad del almacén de datos SQL](sql-data-warehouse-backups.md)
-  [Información general de continuidad empresarial](../sql-database/sql-database-business-continuity.md)

## <a name="data-warehouse-restore-points"></a>Puntos de restauración del almacén de datos

Una ventaja de usar el almacenamiento de Azure Premium almacén de datos SQL usa instantáneas de blobs de Windows Azure almacenamiento para el almacén de datos principal de copia de seguridad. Cada instantánea tiene un punto de restauración que representa la hora de iniciar la instantánea. Para restaurar un almacén de datos, elija un punto de restauración y ejecutar un comando de restauración.  

Almacén de datos SQL siempre se restaura la copia de seguridad a un nuevo almacén de datos. Puede mantener el almacén de datos restaurados y actual o eliminar uno de ellos. Si desea reemplazar el almacén de datos actual con el almacén de datos restaurado, puede cambiar el nombre.

Si necesita restaurar un almacén de datos en pausa o eliminados, puede [crear una incidencia de soporte técnico](sql-data-warehouse-get-started-create-support-ticket.md). 

<!-- 
### Can I restore a deleted data warehouse?

Yes, you can restore the last available restore point.

Yes, for the next seven calendar days. When you delete a data warehouse, SQL Data Warehouse actually keeps the data warehouse and its snapshots for seven days just in case you need the data. After seven days, you won't be able to restore to any of the restore points. -->

## <a name="geo-redundant-restore"></a>Restaurar geo redundantes

Si está utilizando el almacenamiento geo redundantes, puede restaurar el almacén de datos en el [Centro de datos pareja](../best-practices-availability-paired-regions.md) en una región geográfica distinta. El almacén de datos se restaura desde la última copia de seguridad diaria. 

## <a name="restore-timeline"></a>Restaurar la escala de tiempo

Puede restaurar una base de datos en cualquier punto de restauración disponible dentro de los siete últimos días. Instantáneas iniciar cada horas de cuatro a ocho y están disponibles para los siete días. Cuando una instantánea es más de siete días, que expira y su punto de restauración ya no está disponible.

## <a name="restore-costs"></a>Restaurar los costos

El gasto de almacenamiento para el almacén de datos restaurados se cobra a la tasa de almacenamiento de Azure Premium. 

Si se detiene un almacén de datos restaurado, se cargan de almacenamiento a la tasa de almacenamiento de Azure Premium. La ventaja de pausar es que no se carga para los recursos informáticos de DWU.

Para obtener más información acerca de precios de almacén de datos de SQL, vea [Precios de almacén de datos de SQL](https://azure.microsoft.com/pricing/details/sql-data-warehouse/).

## <a name="uses-for-restore"></a>Usos de restauración

El uso principal de la restauración de almacén de datos es recuperar datos después de pérdida de datos accidental o daños.

También puede usar la restauración de almacén de datos para conservar una copia de seguridad durante más de siete días. Una vez que se restaura la copia de seguridad, que tiene el almacén de datos en línea y puede pausar indefinidamente para ahorrar costos de cálculo. La base de datos en pausa incurre en gastos de almacenamiento a la tasa de almacenamiento de Azure Premium. 

## <a name="related-topics"></a>Temas relacionados

### <a name="scenarios"></a>Escenarios

- Para ver una introducción de continuidad empresarial, vea [información general de continuidad empresarial](../sql-database/sql-database-business-continuity.md)


<!-- ### Tasks -->

Para realizar una restauración de almacén de datos, restaurar con:

- Portal de Azure, vea [restaurar un almacén de datos con el portal de Azure](sql-data-warehouse-restore-database-portal.md)
- Cmdlets de PowerShell, vea [restaurar un almacén de datos mediante cmdlets de PowerShell](sql-data-warehouse-restore-database-powershell.md)
- Coloque la API, vea [restaurar un almacén de datos mediante las API de REST](sql-data-warehouse-restore-database-rest-api.md)

<!-- ### Tutorials -->

<!--Image references-->

<!--Article references-->
[Azure SQL Database business continuity overview]: ../sql-database/sql-database-business-continuity.md
[Información general]: ./sql-data-warehouse-restore-database-overview.md
[Portal]: ./sql-data-warehouse-restore-database-portal.md
[PowerShell]: ./sql-data-warehouse-restore-database-powershell.md
[REST]: ./sql-data-warehouse-restore-database-rest-api.md

<!--MSDN references-->


<!--Other Web references-->
