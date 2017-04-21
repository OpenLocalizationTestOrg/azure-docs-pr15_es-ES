<properties
   pageTitle="Migrar a base de datos de SQL con la replicación de transacciones | Microsoft Azure"
   description="Base de datos de Microsoft Azure SQL, migración de base de datos, importar base de datos, replicación de transacciones"
   services="sql-database"
   documentationCenter=""
   authors="CarlRabeler"
   manager="jhubbard"
   editor=""/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="sqldb-migrate"
   ms.date="08/23/2016"
   ms.author="carlrab"/>

# <a name="migrate-sql-server-database-to-azure-sql-database-using-transactional-replication"></a>Migrar la base de datos de SQL Server para utilizar la replicación de transacciones de base de datos SQL Azure

> [AZURE.SELECTOR]
- [Asistente para la migración de SSMS](sql-database-cloud-migrate-compatible-using-ssms-migration-wizard.md)
- [Exportar a archivo MOCHILA](sql-database-cloud-migrate-compatible-export-bacpac-ssms.md)
- [Importar desde archivo MOCHILA](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md)
- [Replicación de transacciones](sql-database-cloud-migrate-compatible-using-transactional-replication.md)

En este artículo, aprenderá a migrar una base de datos de SQL Server compatible para la base de datos de SQL Azure con el tiempo de inactividad mínimo mediante duplicación de transacciones de SQL Server.

## <a name="understanding-the-transactional-replication-architecture"></a>Descripción de la arquitectura de replicación de transacciones

Cuando no desea quitar la base de datos de SQL Server de producción mientras se produce la migración, puede utilizar la replicación de transacciones de SQL Server como la solución de migración. Para utilizar esta solución, configurar la base de datos de SQL Azure como un suscriptor a la instancia de SQL Server local que desea migrar. El local distribuidor de transacciones replicación sincroniza los datos de la base de datos local se sincronicen (el publicador) mientras nuevas transacciones continuar se producen. 

También puede usar la replicación de transacciones para migrar un subconjunto de la base de datos local. La publicación que replicar a la base de datos de SQL Azure puede limitarse a un subconjunto de las tablas de la base de datos que se replicada. Para cada tabla que se duplica, puede limitar los datos a un subconjunto de las filas o un subconjunto de las columnas.

Con la replicación de transacciones, todos los cambios en los datos o el esquema aparecerán en la base de datos de SQL Azure. Una vez completada la sincronización y esté listo para migrar, cambie la cadena de conexión de sus aplicaciones de dirigir a la base de datos de SQL Azure. Una vez que la replicación de transacciones agota los cambios a la izquierda de la base de datos local y todas las aplicaciones apunten a la base de datos de Azure, puede desinstalar la replicación de transacciones. La base de datos de SQL Azure es ahora el sistema de producción.

 ![Diagrama de SeedCloudTR](./media/sql-database-cloud-migrate/SeedCloudTR.png)

## <a name="transactional-replication-requirements"></a>Requisitos de replicación de transacciones

Replicación de transacciones es una tecnología integrada e integrada con SQL Server desde SQL Server 6.5. Es una tecnología para adultos y comprobada que la mayoría de los administradores sabe con las que tengan experiencia. Con la [2016 de SQL Server](https://www.microsoft.com/en-us/cloud-platform/sql-server), es posible configurar la base de datos de SQL Azure como un [suscriptor de duplicación de transacciones](https://msdn.microsoft.com/library/mt589530.aspx) a la publicación local. La experiencia de obtener configuración de desde Management Studio es el mismo como si configura un suscriptor de duplicación de transacciones en un servidor local. Soporte técnico para este escenario es compatible cuando el publicador y el distribuidor están al menos una de las siguientes versiones de SQL Server:

 - SQL Server 2016 y anteriores 
 - SQL Server 2014 SP1 CU3 y anteriores
 - SQL Server 2014 RTM CU10 y anteriores
 - SQL Server 2012 SP2 CU8 y anteriores
 - SQL Server 2012 SP3 y anteriores


> [AZURE.IMPORTANT] Usar la última versión de SQL Server Management Studio permanezca sincronizado con las actualizaciones de Microsoft Azure y base de datos de SQL. Las versiones anteriores de SQL Server Management Studio no pueden configurar la base de datos de SQL como suscriptor. [Actualizar SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).


## <a name="next-steps"></a>Pasos siguientes

- [Versión más reciente de SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx)
- [Versión más reciente de SSDT](https://msdn.microsoft.com/library/mt204009.aspx)
- [SQL Server 2016](https://www.microsoft.com/en-us/cloud-platform/sql-server)

## <a name="additional-resources"></a>Recursos adicionales

- [Replicación de transacciones](https://msdn.microsoft.com/library/mt589530.aspx)
- [V12 de base de datos SQL](sql-database-v12-whats-new.md)
- [Transact-SQL parcialmente o no compatibles de funciones](sql-database-transact-sql-information.md)
- [Migrar las bases de datos no es de SQL Server con el Asistente de migración de SQL Server](http://blogs.msdn.com/b/ssma/)
