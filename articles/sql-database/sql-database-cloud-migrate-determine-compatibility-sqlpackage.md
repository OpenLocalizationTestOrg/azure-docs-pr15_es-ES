<properties
   pageTitle="Determinar la compatibilidad de base de datos SQL con SqlPackage.exe | Microsoft Azure"
   description="Microsoft Azure base de datos SQL, migración de base de datos, la compatibilidad de base de datos de SQL, SqlPackage"
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

# <a name="determine-sql-database-compatibility-using-sqlpackageexe"></a>Determinar la compatibilidad de base de datos SQL con SqlPackage.exe

> [AZURE.SELECTOR]
- [SSDT](sql-database-cloud-migrate-fix-compatibility-issues-ssdt.md)
- [SqlPackage](sql-database-cloud-migrate-determine-compatibility-sqlpackage.md)
- [SSMS](sql-database-cloud-migrate-determine-compatibility-ssms.md)
- [Asesor de actualizaciones](http://www.microsoft.com/download/details.aspx?id=48119)
- [SAMW](sql-database-cloud-migrate-fix-compatibility-issues.md)

En este artículo, aprenderá a determinar si una base de datos de SQL Server es compatible para migrar a la base de datos de SQL con la herramienta de línea de comandos [SqlPackage](https://msdn.microsoft.com/library/hh550080.aspx) .

## <a name="using-sqlpackageexe"></a>Usar SqlPackage.exe

1. Abra un símbolo del sistema y cambie un directorio que contenga la versión más reciente de sqlpackage.exe. Esta utilidad se incluye con las últimas versiones de [SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) y [SQL Server Data Tools para Visual Studio](https://msdn.microsoft.com/library/mt204009.aspx), o puede descargar la versión más reciente de [SqlPackage](https://www.microsoft.com/en-us/download/details.aspx?id=53876) directamente desde el centro de descarga de Microsoft.
2. Ejecute el siguiente comando SqlPackage con los argumentos siguientes para su entorno:

    ' sqlpackage.exe /Action:Export /ssn: /sdn < nombre_de_servidor >: < database_name > /tf: /p:TableData < target_file > = < schema_name.table_name >>< output_file > 2 > & 1'

  	| Argumento  | Descripción  |
  	|---|---|
  	| < nombre_de_servidor >  | nombre del servidor de origen  |
  	| < database_name >  | nombre de la base de datos de origen  |
  	| < target_file >  | nombre de archivo y una ubicación para el archivo de MOCHILA  |
  	| < schema_name.table_name >  | las tablas para que los datos son resultado en el archivo de destino  |
  	| < output_file >  | el nombre de archivo y la ubicación del archivo de salida con errores, si los hay  |

    El motivo por el argumento /p:TableName es que sólo vamos a comprobar la compatibilidad de la base de datos para la exportación a V12 de base de datos de SQL Azure en lugar de exportar los datos de todas las tablas. Desgraciadamente, el argumento de exportación para sqlpackage.exe no admite la extracción cero tablas. Debe especificar al menos una tabla, como una sola tabla pequeña. < Output_file > contiene el informe de errores. La "> 2 > & 1" cadena canalizaciones la salida estándar y el error estándar es el resultado de la ejecución del comando al archivo de salida especificada.

    ![Exportar una aplicación de nivel de datos en el menú de tareas](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSQLPackage01.png)

3. Abra el archivo de salida y revisar los errores de compatibilidad. 

    ![Exportar una aplicación de nivel de datos en el menú de tareas](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSQLPackage02.png)

## <a name="next-steps"></a>Pasos siguientes

- [Versión más reciente de SSDT](https://msdn.microsoft.com/library/mt204009.aspx)
[versión más reciente de SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx)
- [Solucionar problemas de compatibilidad de migración de base de datos](sql-database-cloud-migrate.md#fix-database-migration-compatibility-issues)
- [Migrar una base de datos de SQL Server compatible para la base de datos SQL](sql-database-cloud-migrate.md#migrate-a-compatible-sql-server-database-to-sql-database)

## <a name="additional-resources"></a>Recursos adicionales

- [V12 de base de datos SQL](sql-database-v12-whats-new.md)
- [Transact-SQL parcialmente o no compatibles de funciones](sql-database-transact-sql-information.md)
- [Migrar las bases de datos no es de SQL Server con el Asistente de migración de SQL Server](http://blogs.msdn.com/b/ssma/)
