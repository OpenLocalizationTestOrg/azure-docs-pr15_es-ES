<properties
   pageTitle="Solucionar problemas de compatibilidad de base de datos de SQL Server antes de la migración a la base de datos SQL | Microsoft Azure"
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

# <a name="use-sql-azure-migration-wizard-to-fix-sql-server-database-compatibility-issues-before-migration-to-azure-sql-database"></a>Usar el Asistente para la migración de SQL Azure para problemas de compatibilidad de base de datos de corregir SQL Server antes de la migración a la base de datos de SQL Azure

> [AZURE.SELECTOR]
- Usar el [Asistente para la migración de Azure SQL](sql-database-cloud-migrate-fix-compatibility-issues.md)
- Usar [SSDT](sql-database-cloud-migrate-fix-compatibility-issues-ssdt.md)
- Usar [SSMS](sql-database-cloud-migrate-fix-compatibility-issues-ssms.md)

En este artículo, aprenderá a detectar y corregir problemas de compatibilidad de base de datos de SQL Server con el Asistente para la migración de SQL Azure antes de la migración a la base de datos de SQL Azure.

## <a name="using-sql-azure-migration-wizard"></a>Usar el Asistente para la migración de SQL Azure

Use la herramienta de [migración de SQL Azure Asistente](http://sqlazuremw.codeplex.com/) CodePlex para generar una secuencia de comandos T SQL de una base de datos de origen no compatibles. Esta secuencia de comandos, a continuación, se transforma por el Asistente para que sea compatible con la base de datos de SQL. A continuación, debe conectarse a la base de datos de Azure SQL para ejecutar la secuencia de comandos. Esta herramienta también analiza los archivos de seguimiento para determinar los problemas de compatibilidad. La secuencia de comandos puede generar con solo esquema o puede incluir datos en formato BCP. Documentación adicional, incluyendo instrucciones paso a paso está disponible en CodePlex en el [Asistente para la migración de SQL Azure](http://sqlazuremw.codeplex.com/).  

 ![Diagrama de migración de SAMW](./media/sql-database-cloud-migrate/02SAMWDiagram.png)

  > [AZURE.NOTE] No todos los esquema incompatible que se encuentran por el asistente puede corregirse sus transformaciones integradas. Incompatible secuencia de comandos que puede solucionarse aparecen como errores, con los comentarios que se insertan en el script generado. Si se detectan errores, use Visual Studio o SQL Server Management Studio paso a paso y corregir cada error que no se puede corregir mediante el Asistente para la migración de SQL Server.

## <a name="next-steps"></a>Pasos siguientes

- [Versión más reciente de SSDT](https://msdn.microsoft.com/library/mt204009.aspx)
- [Versión más reciente de SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx)
- [Migrar una base de datos de SQL Server compatible para la base de datos SQL](sql-database-cloud-migrate.md#migrate-a-compatible-sql-server-database-to-sql-database)

## <a name="additional-resources"></a>Recursos adicionales

- [V12 de base de datos SQL](sql-database-v12-whats-new.md)
- [Transact-SQL parcialmente o no compatibles de funciones](sql-database-transact-sql-information.md)
- [Migrar las bases de datos no es de SQL Server con el Asistente de migración de SQL Server](http://blogs.msdn.com/b/ssma/)
