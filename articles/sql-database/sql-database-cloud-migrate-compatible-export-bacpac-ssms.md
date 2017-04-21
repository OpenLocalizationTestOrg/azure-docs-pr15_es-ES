
<properties
   pageTitle="Exportar una base de datos de SQL Server a un archivo de MOCHILA con SQL Server Management Studio | Microsoft Azure"
   description="Base de datos de Microsoft Azure SQL, migración de base de datos, exportar a base de datos, Exportar archivo MOCHILA, Asistente para exportar aplicaciones de nivel de datos"
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
   ms.workload="data-management"
   ms.date="08/16/2016"
   ms.author="carlrab"/>

# <a name="export-a-sql-server-database-to-a-bacpac-file-using-sql-server-management-studio"></a>Exportar una base de datos de SQL Server a un archivo de MOCHILA con SQL Server Management Studio

> [AZURE.SELECTOR]
- [SSMS](sql-database-cloud-migrate-compatible-export-bacpac-ssms.md)
- [SqlPackage](sql-database-cloud-migrate-compatible-export-bacpac-sqlpackage.md)

 
Este artículo le muestra cómo exportar una base de datos de SQL Server a un archivo de [MOCHILA](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4) mediante el Asistente para exportar aplicaciones de nivel de datos en SQL Server Management Studio. 

1. Compruebe que tiene la última versión de SQL Server Management Studio. Las nuevas versiones de Management Studio se actualizan mensualmente permanezca sincronizado con actualizaciones en el portal de Azure.

     > [AZURE.IMPORTANT] Se recomienda usar siempre la versión más reciente de Management Studio permanezca sincronizado con las actualizaciones de Microsoft Azure y base de datos de SQL. [Actualizar SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).

2. Abra Management Studio y conectarse a la base de datos de origen en el Explorador de objetos.

    ![Exportar una aplicación de nivel de datos en el menú de tareas](./media/sql-database-cloud-migrate/MigrateUsingBACPAC01.png)

3. Haga clic en la base de datos de origen en el Explorador de objetos, seleccione **tareas**y haga clic en **Exportar aplicación de nivel de datos...**

    ![Exportar una aplicación de nivel de datos en el menú de tareas](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSSMS01.png)

4. En el Asistente para exportación, configure la exportación para guardar el archivo MOCHILA: una ubicación de disco local o en un Azure blob. La MOCHILA exportado siempre incluye el esquema de la base de datos completa y, de forma predeterminada, los datos de todas las tablas. Si desea excluir los datos de algunas o todas las tablas, use la pestaña avanzado. Por ejemplo, puede exportar solo los datos de tablas de referencia en lugar de todas las tablas.

***Importante*** Al exportar una MOCHILA al almacenamiento de blobs de Windows Azure, utilice el almacenamiento estándar. No se admite la importación de una MOCHILA de almacenamiento premium.

    ![Export settings](./media/sql-database-cloud-migrate/MigrateUsingBACPAC02.png)


## <a name="next-steps"></a>Pasos siguientes

- [Versión más reciente de SSDT](https://msdn.microsoft.com/library/mt204009.aspx)
- [Versión más reciente de SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx)
- [Importar una MOCHILA a la base de datos de SQL Azure con SSMS](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md)
- [Importar una MOCHILA a SqlPackage de la base de datos SQL Azure](sql-database-cloud-migrate-compatible-import-bacpac-sqlpackage.md)
- [Importar una MOCHILA portal de Azure de base de datos de SQL Azure](sql-database-import.md)
- [Importar una MOCHILA PowerShell de base de datos SQL Azure](sql-database-import-powershell.md)

## <a name="additional-resources"></a>Recursos adicionales

- [V12 de base de datos SQL](sql-database-v12-whats-new.md)
- [Transact-SQL parcialmente o no compatibles de funciones](sql-database-transact-sql-information.md)
- [Migrar las bases de datos no es de SQL Server con el Asistente de migración de SQL Server](http://blogs.msdn.com/b/ssma/)
