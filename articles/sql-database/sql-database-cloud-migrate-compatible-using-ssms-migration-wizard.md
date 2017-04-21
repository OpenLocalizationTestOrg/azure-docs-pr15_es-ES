<properties
   pageTitle="Migrar la base de datos de SQL Server a la base de datos SQL implementar base de datos en el Asistente de base de datos de Microsoft Azure | Microsoft Azure"
   description="Base de datos de Microsoft Azure SQL, migración de base de datos, el Asistente para la base de datos de Microsoft Azure"
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

# <a name="migrate-sql-server-database-to-sql-database-using-deploy-database-to-microsoft-azure-database-wizard"></a>Migrar la base de datos de SQL Server a la base de datos SQL implementar base de datos en el Asistente de base de datos de Microsoft Azure


> [AZURE.SELECTOR]
- [Asistente para la migración de SSMS](sql-database-cloud-migrate-compatible-using-ssms-migration-wizard.md)
- [Exportar a archivo MOCHILA](sql-database-cloud-migrate-compatible-export-bacpac-ssms.md)
- [Importar desde archivo MOCHILA](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md)
- [Replicación de transacciones](sql-database-cloud-migrate-compatible-using-transactional-replication.md)

La base de datos implementar para bases de datos de Microsoft Azure en SQL Server Management Studio migra una [base de datos de SQL Server compatible](sql-database-cloud-migrate.md) directamente en el servidor de base de datos de SQL Azure.

## <a name="use-the-deploy-database-to-microsoft-azure-database-wizard"></a>Usar la base de datos de distribución en el Asistente de la base de datos de Microsoft Azure

> [AZURE.NOTE] Los siguientes pasos se suponen que tiene un [aprovisionar la base de datos de SQL server](https://azure.microsoft.com/documentation/learning-paths/sql-database-training-learn-sql-database/).

1. Compruebe que tiene la última versión de SQL Server Management Studio. Las nuevas versiones de Management Studio se actualizan mensualmente permanezca sincronizado con actualizaciones en el portal de Azure.

    > [AZURE.IMPORTANT] Se recomienda usar siempre la versión más reciente de Management Studio permanezca sincronizado con las actualizaciones de Microsoft Azure y base de datos de SQL. [Actualizar SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).

2. Abra Management Studio y conectarse a la base de datos de SQL Server que deben migrarse en el Explorador de objetos.
3. Haga clic en la base de datos en el Explorador de objetos, seleccione **tareas**y haga clic en **Desplegar bases de datos a Microsoft Azure base de datos SQL...**

    ![Implementar en Azure en el menú de tareas](./media/sql-database-cloud-migrate/MigrateUsingDeploymentWizard01.png)

4.  En el Asistente de implementación, haga clic en **siguiente**y, a continuación, haga clic en **Conectar** para configurar la conexión al servidor de base de datos de SQL.

    ![Implementar en Azure en el menú de tareas](./media/sql-database-cloud-migrate/MigrateUsingDeploymentWizard002.png)

5. En conectar al cuadro de diálogo de servidor, escriba la información de conexión para conectarse al servidor de base de datos de SQL.

    ![Implementar en Azure en el menú de tareas](./media/sql-database-cloud-migrate/MigrateUsingDeploymentWizard00.png)

5.  Proporciona las siguientes acciones para el archivo de [MOCHILA](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4) este asistente crea durante el proceso de migración:

 - El **nuevo nombre de la base de datos** 
 - La **Edición de Microsoft Azure base de datos SQL** ([nivel de servicio](sql-database-service-tiers.md))
 - El **tamaño máximo de la base de datos**
 - El **Objetivo de servicio** (nivel de rendimiento)
 - El **nombre de archivo temporal**  

    ![Exportar configuración](./media/sql-database-cloud-migrate/MigrateUsingDeploymentWizard02.png)

6.  Complete el asistente. Dependiendo del tamaño y la complejidad de la base de datos, implementación puede tardar desde unos minutos a unas horas. Si este asistente detecta problemas de compatibilidad, se muestran errores en la pantalla y no continuar la migración. Para obtener instrucciones sobre cómo solucionar problemas de compatibilidad de base de datos, vaya a [solucionar problemas de compatibilidad de base de datos](sql-database-cloud-migrate-fix-compatibility-issues.md).

7.  Usando el Explorador de objetos, conectarse a la base de datos migrado en su servidor de base de datos de SQL Azure.
8.  Con el portal de Azure, ver la base de datos y sus propiedades.

## <a name="next-steps"></a>Pasos siguientes

- [Versión más reciente de SSDT](https://msdn.microsoft.com/library/mt204009.aspx)
- [Versión más reciente de SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx)

## <a name="additional-resources"></a>Recursos adicionales

- [V12 de base de datos SQL](sql-database-v12-whats-new.md)
- [Transact-SQL parcialmente o no compatibles de funciones](sql-database-transact-sql-information.md)
- [Migrar las bases de datos no es de SQL Server con el Asistente de migración de SQL Server](http://blogs.msdn.com/b/ssma/)
