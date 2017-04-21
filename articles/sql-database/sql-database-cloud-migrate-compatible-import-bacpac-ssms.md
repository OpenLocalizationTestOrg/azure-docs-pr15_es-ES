<properties
   pageTitle="Migrar una base de datos de SQL Server a la base de datos de SQL Azure | Microsoft Azure"
   description="Base de datos de SQL de Microsoft Azure, implementar de base de datos, migración de base de datos, base de datos de importación, exportación de base de datos, Asistente para la migración"
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

# <a name="import-from-bacpac-to-sql-database-using-ssms"></a>Importar desde MOCHILA a la base de datos de SQL con SSMS

> [AZURE.SELECTOR]
- [SSMS](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md)
- [SqlPackage](sql-database-cloud-migrate-compatible-import-bacpac-sqlpackage.md)
- [Portal de Azure](sql-database-import.md)
- [PowerShell](sql-database-import-powershell.md)

Este artículo le muestra cómo importar desde un archivo de [MOCHILA](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4) a la base de datos de SQL mediante el Asistente para exportar aplicaciones de nivel de datos en SQL Server Management Studio.

> [AZURE.NOTE] Los siguientes pasos se suponen que ya se ha suministrado la instancia de SQL Azure lógica y tener a mano de la información de conexión.

1. Compruebe que tiene la última versión de SQL Server Management Studio. Las nuevas versiones de Management Studio se actualizan mensualmente permanezca sincronizado con actualizaciones en el portal de Azure.

     > [AZURE.IMPORTANT] Se recomienda usar siempre la versión más reciente de Management Studio permanezca sincronizado con las actualizaciones de Microsoft Azure y base de datos de SQL. [Actualizar SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).

2. Conectarse al servidor de base de datos de SQL Azure, haga clic en la carpeta de **bases de datos** y haga clic en **importar la aplicación de nivel de datos...**

    ![Importar el elemento de menú de aplicación de nivel de datos](./media/sql-database-cloud-migrate/MigrateUsingBACPAC03.png)

3.  Para crear la base de datos en la base de datos de SQL Azure, importar un archivo de MOCHILA de disco local o seleccione la cuenta de almacenamiento de Azure y el contenedor a la que ha cargado el archivo MOCHILA.

    ![Configuración de importación](./media/sql-database-cloud-migrate/MigrateUsingBACPAC04.png)

     > [AZURE.IMPORTANT] Al importar una MOCHILA de almacenamiento de blobs de Windows Azure, utilice el almacenamiento estándar. No se admite la importación de una MOCHILA de almacenamiento premium.

4.  Proporcione el **nuevo nombre de la base de datos** para la base de datos en la base de datos de SQL Azure, establezca la **Edición de Microsoft Azure base de datos SQL** (nivel de servicio), el **tamaño máximo de la base de datos**y el **Objetivo de servicio** (nivel de rendimiento).

    ![Configuración de la base de datos](./media/sql-database-cloud-migrate/MigrateUsingBACPAC05.png)

5.  Haga clic en **siguiente** y, a continuación, haga clic en **Finalizar** para importar el archivo MOCHILA en una nueva base de datos en el servidor de base de datos de SQL Azure.

6. Usando el Explorador de objetos, conectarse a la base de datos migrado en su servidor de base de datos de SQL Azure.

6.  Con el portal de Azure, ver la base de datos y sus propiedades.

## <a name="next-steps"></a>Pasos siguientes

- [Versión más reciente de SSDT](https://msdn.microsoft.com/library/mt204009.aspx)
- [Versión más reciente de SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx)

## <a name="additional-resources"></a>Recursos adicionales

- [V12 de base de datos SQL](sql-database-v12-whats-new.md)
- [Transact-SQL parcialmente o no compatibles de funciones](sql-database-transact-sql-information.md)
- [Migrar las bases de datos no es de SQL Server con el Asistente de migración de SQL Server](http://blogs.msdn.com/b/ssma/)
