<properties
   pageTitle="Solucionar problemas de compatibilidad de base de datos de SQL Server con SQL Server Management Studio antes de la migración a la base de datos SQL | Microsoft Azure"
   description="Microsoft Azure base de datos SQL, migración de base de datos, compatibilidad, Asistente para la migración de SQL Azure"
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
   ms.date="08/24/2016"
   ms.author="carlrab"/>

# <a name="fix-sql-server-database-compatibility-issues-using-sql-server-management-studio-before-migration-to-sql-database"></a>Solucionar problemas de compatibilidad de base de datos de SQL Server con SQL Server Management Studio antes de la migración a la base de datos SQL

> [AZURE.SELECTOR]
- Usar el [Asistente para la migración de Azure SQL](sql-database-cloud-migrate-fix-compatibility-issues.md)
- Usar [SSDT](sql-database-cloud-migrate-fix-compatibility-issues-ssdt.md)
- Usar [SSMS](sql-database-cloud-migrate-fix-compatibility-issues-ssms.md)

Los usuarios avanzados pueden solucionar los problemas de compatibilidad de base de datos de SQL Server con SQL Server Management Studio antes de la migración a la base de datos de SQL Azure.


> [AZURE.IMPORTANT] Se recomienda usar siempre la versión más reciente de Management Studio permanezca sincronizado con las actualizaciones de Microsoft Azure y base de datos de SQL. [Actualizar SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).


## <a name="using-sql-server-management-studio"></a>Usar SQL Server Management Studio

Usar SQL Server Management Studio para corregir problemas de compatibilidad con varios comandos de Transact-SQL, como **Modificar base de datos**. Este método es principalmente para usuarios avanzados que se siente cómodos trabajando Transact-SQL en una base de datos activa. En caso contrario, se recomienda que use SSDT. 



## <a name="next-steps"></a>Pasos siguientes

- [Versión más reciente de SSDT](https://msdn.microsoft.com/library/mt204009.aspx)
- [Versión más reciente de SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx)
- [Migrar una base de datos de SQL Server compatible para la base de datos SQL](sql-database-cloud-migrate.md#migrate-a-compatible-sql-server-database-to-sql-database)

## <a name="additional-resources"></a>Recursos adicionales

- [V12 de base de datos SQL](sql-database-v12-whats-new.md)
- [Transact-SQL parcialmente o no compatibles de funciones](sql-database-transact-sql-information.md)
- [Migrar las bases de datos no es de SQL Server con el Asistente de migración de SQL Server](http://blogs.msdn.com/b/ssma/)
