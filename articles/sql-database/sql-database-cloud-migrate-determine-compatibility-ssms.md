<properties
   pageTitle="Usar SQL Server Management Studio para la compatibilidad de la base de datos de SQL de determinar antes de la migración a la base de datos de SQL Azure | Microsoft Azure"
   description="Microsoft Azure base de datos SQL, migración de base de datos, la compatibilidad de base de datos de SQL, exportar Asistente de aplicación de nivel de datos"
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
   ms.date="08/29/2016"
   ms.author="carlrab"/>

# <a name="use-sql-server-management-studio-to-determine-sql-database-compatibility-before-migration-to-azure-sql-database"></a>Usar SQL Server Management Studio para la compatibilidad de la base de datos de SQL de determinar antes de la migración a la base de datos de SQL Azure

> [AZURE.SELECTOR]
- [SSDT](sql-database-cloud-migrate-fix-compatibility-issues-ssdt.md)
- [SqlPackage](sql-database-cloud-migrate-determine-compatibility-sqlpackage.md)
- [SSMS](sql-database-cloud-migrate-determine-compatibility-ssms.md)
- [Asesor de actualizaciones](http://www.microsoft.com/download/details.aspx?id=48119)
- [SAMW](sql-database-cloud-migrate-fix-compatibility-issues.md)
 
En este artículo que aprenderá a determinar si una base de datos de SQL Server que es compatible para migrar a la base de datos de SQL mediante el Asistente para exportar aplicaciones de nivel de datos de SQL Server Management Studio.

## <a name="using-sql-server-management-studio"></a>Usar SQL Server Management Studio

1. Compruebe que tiene la última versión de SQL Server Management Studio. Las nuevas versiones de Management Studio se actualizan mensualmente permanezca sincronizado con actualizaciones en el portal de Azure.

     > [AZURE.IMPORTANT] Se recomienda usar siempre la versión más reciente de Management Studio permanezca sincronizado con las actualizaciones de Microsoft Azure y base de datos de SQL. [Actualizar SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).

2. Abra Management Studio y conectarse a la base de datos de origen en el Explorador de objetos.
3. Haga clic en la base de datos de origen en el Explorador de objetos, seleccione **tareas**y haga clic en **Exportar aplicación de nivel de datos...**

    ![Exportar una aplicación de nivel de datos en el menú de tareas](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSSMS01.png)

4. En el Asistente para exportación, haga clic en **siguiente**y, a continuación, en la pestaña **configuración** , configure la exportación para guardar el archivo MOCHILA a una ubicación de disco local o en un blobs de Windows Azure. Si no dispone de ningún problema de compatibilidad de base de datos, se guarda un archivo de MOCHILA. Si hay problemas de compatibilidad, se muestran en la consola.

    ![Exportar configuración](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSSMS02.png)

5. Para omitir la exportación de datos, haga clic en la **ficha Opciones avanzadas** y desactive la casilla de verificación **Seleccionar todo** . En este momento, nuestro objetivo es solo comprobar la compatibilidad.

    ![Exportar configuración](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSSMS03.png)

6. Haga clic en **siguiente** y, a continuación, haga clic en **Finalizar**. Problemas de compatibilidad de base de datos, si la hay, aparecen después de que el asistente valida el esquema.

    ![Exportar configuración](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSSMS04.png)

7. Si no aparezcan errores, la base de datos es compatible y esté listo para migrar. Si se producen errores, debe corregirlos. Para ver los errores, haga clic en el **Error** de **esquema de validación**. 
    ![Exportar configuración](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSSMS05.png)

8.  Si el *. Archivo MOCHILA se generó correctamente, a continuación, la base de datos es compatible con la base de datos de SQL, y esté listo para migrar.

## <a name="next-steps"></a>Pasos siguientes

- [Versión más reciente de SSDT](https://msdn.microsoft.com/library/mt204009.aspx)
- [Versión más reciente de SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx)
- [Solucionar problemas de compatibilidad de migración de base de datos](sql-database-cloud-migrate.md#fix-database-migration-compatibility-issues)
- [Migrar una base de datos de SQL Server compatible para la base de datos SQL](sql-database-cloud-migrate.md#migrate-a-compatible-sql-server-database-to-sql-database)

## <a name="additional-resources"></a>Recursos adicionales

- [V12 de base de datos SQL](sql-database-v12-whats-new.md)
- [Transact-SQL parcialmente o no compatibles de funciones](sql-database-transact-sql-information.md)
- [Migrar las bases de datos no es de SQL Server con el Asistente de migración de SQL Server](http://blogs.msdn.com/b/ssma/)
