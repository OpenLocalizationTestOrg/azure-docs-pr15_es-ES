<properties
   pageTitle="Importar a la base de datos SQL desde un archivo MOCHILA SqlPackage"
   description="Base de datos de Microsoft Azure SQL, migración de base de datos, importar base de datos, importar archivo MOCHILA, sqlpackage"
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

# <a name="import-to-sql-database-from-a-bacpac-file-using-sqlpackage"></a>Importar a la base de datos SQL desde un archivo MOCHILA SqlPackage

> [AZURE.SELECTOR]
- [SSMS](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md)
- [SqlPackage](sql-database-cloud-migrate-compatible-import-bacpac-sqlpackage.md)
- [Portal de Azure](sql-database-import.md)
- [PowerShell](sql-database-import-powershell.md)

Este artículo le muestra cómo importar a la base de datos SQL de un archivo de [MOCHILA](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4) mediante la utilidad de línea de comandos de [SqlPackage](https://msdn.microsoft.com/library/hh550080.aspx) . Esta utilidad se incluye con las últimas versiones de [SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) y [SQL Server Data Tools para Visual Studio](https://msdn.microsoft.com/library/mt204009.aspx), o puede descargar la versión más reciente de [SqlPackage](https://www.microsoft.com/en-us/download/details.aspx?id=53876) directamente desde el centro de descarga de Microsoft.


> [AZURE.NOTE] Los siguientes pasos se supone ya se ha suministrado un servidor de base de datos de SQL, tiene la información de conexión a mano y comprobar que la base de datos de origen es compatible.

## <a name="import-from-a-bacpac-file-into-azure-sql-database-using-sqlpackage"></a>Importar desde un archivo de MOCHILA con SqlPackage de base de datos SQL Azure

Use los siguientes pasos para usar la utilidad de línea de comandos de [SqlPackage.exe](https://msdn.microsoft.com/library/hh550080.aspx) para importar una base de datos de SQL Server compatible (o base de datos de SQL Azure) desde un archivo de MOCHILA.

> [AZURE.NOTE] Los siguientes pasos se suponen que ya se ha suministrado un servidor de base de datos de SQL Azure y tener a mano de la información de conexión.

1. Abra un símbolo del sistema y cambie un directorio que contiene la utilidad de línea de comandos de sqlpackage.exe: esta herramienta se distribuye con Visual Studio y SQL Server.
2. Ejecute el siguiente comando sqlpackage.exe con los argumentos siguientes para su entorno:

    `sqlpackage.exe /Action:Import /tsn:< server_name > /tdn:< database_name > /tu:< user_name > /tp:< password > /sf:< source_file >`

  	| Argumento  | Descripción  |
  	|---|---|
  	| < nombre_de_servidor >  | nombre del servidor de destino  |
  	| < database_name >  | nombre de la base de datos de destino  |
  	| < nombreDeUsuario >  | el nombre de usuario en el servidor de destino |
  	| < contraseña >  | la contraseña del usuario  |
  	| < source_file >  | el nombre de archivo y la ubicación del archivo de MOCHILA que se importa  |

    ![Exportar una aplicación de nivel de datos en el menú de tareas](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSQLPackage01c.png)

## <a name="next-steps"></a>Pasos siguientes

- [Versión más reciente de SSDT](https://msdn.microsoft.com/library/mt204009.aspx)
- [Versión más reciente de SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx)

## <a name="additional-resources"></a>Recursos adicionales

- [V12 de base de datos SQL](sql-database-v12-whats-new.md)
- [Transact-SQL parcialmente o no compatibles de funciones](sql-database-transact-sql-information.md)
- [Migrar las bases de datos no es de SQL Server con el Asistente de migración de SQL Server](http://blogs.msdn.com/b/ssma/)
