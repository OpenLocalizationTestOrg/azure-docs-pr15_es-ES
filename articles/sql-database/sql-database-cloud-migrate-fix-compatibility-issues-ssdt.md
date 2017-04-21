<properties
   pageTitle="Solucionar problemas de compatibilidad de base de datos de SQL Server antes de la migración a la base de datos SQL | Microsoft Azure"
   description="Microsoft Azure base de datos SQL, migración de base de datos, compatibilidad, el Asistente para migración de SQL Azure, SSDT"
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

# <a name="migrate-a-sql-server-database-to-azure-sql-database-using-sql-server-data-tools-for-visual-studio"></a>Migrar una base de datos SQL Server a la base de datos SQL Azure con SQL Server Data Tools para Visual Studio 

> [AZURE.SELECTOR]
- [SSDT](sql-database-cloud-migrate-fix-compatibility-issues-ssdt.md)
- [SqlPackage](sql-database-cloud-migrate-determine-compatibility-sqlpackage.md)
- [SSMS](sql-database-cloud-migrate-determine-compatibility-ssms.md)
- [Asesor de actualizaciones](http://www.microsoft.com/download/details.aspx?id=48119)
- [SAMW](sql-database-cloud-migrate-fix-compatibility-issues.md)

En este artículo, aprenderá a detectar y corregir problemas de compatibilidad de base de datos de SQL Server con SQL Server Data Tools para Visual Studio antes de la migración a la base de datos de SQL Azure.

## <a name="using-sql-server-data-tools-for-visual-studio"></a>Usar SQL Server Data Tools para Visual Studio

Usar SQL Server Data Tools para Visual Studio ("SSDT") para importar el esquema de la base de datos en un proyecto de base de datos de Visual Studio para el análisis. Para analizar, especifique la plataforma de destino para el proyecto como V12 de base de datos de SQL y, a continuación, cree el proyecto. Si la compilación es correcta, la base de datos es compatible. Si se produce un error en la compilación, puede resolver los errores de SSDT (o una de las otras herramientas descritas en este tema). Una vez que el proyecto se compila correctamente, puede publicar como una copia de la base de datos de origen. A continuación, puede usar la característica de comparación de datos en SSDT para copiar los datos de la base de datos de origen a la base de datos compatible V12 de SQL Azure. A continuación, puede migrar esta base de datos actualizado. Para usar esta opción, descargue la [versión más reciente de SSDT](https://msdn.microsoft.com/library/mt204009.aspx).

  ![Diagrama de migración de VSSSDT](./media/sql-database-cloud-migrate/03VSSSDTDiagram.png)

  > [AZURE.NOTE] Si se requiere una migración de sólo esquema, el esquema se puede publicar directamente desde Visual Studio directamente a la base de datos de SQL Azure. Use este método cuando el esquema de la base de datos requiere más cambios que se puede controlar el Asistente para la migración por sí solo.

## <a name="detecting-compatibility-issues-using-sql-server-data-tools-for-visual-studio"></a>Detectar problemas de compatibilidad con SQL Server Data Tools para Visual Studio
   
1.  Abra el **Explorador de objetos SQL Server** en Visual Studio. Usar **Agregar SQL Server** para conectarse a la instancia de SQL Server que contiene la base de datos que se migran. Busque la base de datos en el Explorador de objetos, haga clic en la base de datos y seleccione **Crear nuevo proyecto...**     
    
    ![Nuevo proyecto](./media/sql-database-migrate-visualstudio-ssdt/02MigrateSSDT.png)    
   
2.  Configurar las opciones de importación para **Importar solo objetos ámbito de la aplicación**. Desactive las opciones para importar las siguientes acciones: hace referencia a inicios de sesión, permisos y configuración de la base de datos.    

    ![texto alternativo](./media/sql-database-migrate-visualstudio-ssdt/03MigrateSSDT.png)    

3.  Haga clic en **Iniciar** para importar la base de datos y crear el proyecto que contiene un archivo de script de SQL de T para cada objeto de la base de datos. Los archivos de comandos están anidados en carpetas dentro del proyecto.    

    ![texto alternativo](./media/sql-database-migrate-visualstudio-ssdt/04MigrateSSDT.png)    

4.  En el Explorador de soluciones de Visual Studio, haga clic en el proyecto de base de datos y seleccione Propiedades. En la página **Configuración del proyecto** , configure la plataforma de destino a V12 de base de datos de SQL de Microsoft Azure.    
    
    ![texto alternativo](./media/sql-database-migrate-visualstudio-ssdt/05MigrateSSDT.png)    
    
5.  Haga clic en el proyecto y seleccione **Generar** para generar el proyecto.    
    
    ![texto alternativo](./media/sql-database-migrate-visualstudio-ssdt/06MigrateSSDT.png)    
    
6.  La **Lista de errores** muestra cada incompatibilidad. En este caso, el nombre de usuario NT AUTHORITY\NETWORK SERVICE es incompatible. Puesto que no es compatible, puede comentario o quitarlo (y las consecuencias de quitar este inicio de sesión y la función de la solución de base de datos de direcciones).     
    
    ![texto alternativo](./media/sql-database-migrate-visualstudio-ssdt/07MigrateSSDT.png)    
    
## <a name="fixing-compatibility-issues-using-sql-server-data-tools-for-visual-studio"></a>Solución de problemas de compatibilidad con SQL Server Data Tools para Visual Studio

1.  Haga doble clic en la primera secuencia de comandos para abrir la secuencia de comandos en una ventana de consulta y un comentario a la secuencia de comandos y, a continuación, ejecute la secuencia de comandos.     
    ![texto alternativo](./media/sql-database-migrate-visualstudio-ssdt/08MigrateSSDT.png)

2.  Repita este proceso para cada secuencia de comandos que contiene incompatibilidad hasta que no queden ningún error.    
    ![texto alternativo](./media/sql-database-migrate-visualstudio-ssdt/09MigrateSSDT.png)
    
3.  Cuando la base de datos está libre de errores, haga clic en el proyecto y seleccione **Publicar**. Se genera una copia de la base de datos de origen y se publica (se recomienda que use una copia, al menos inicialmente).     
 - Antes de publicar, dependiendo de la versión de SQL Server de origen (anteriores a SQL Server 2014), tendrá que restablecer la plataforma de destino del proyecto para habilitar la implementación.     
 - Si va a migrar una base de datos de SQL Server anterior, no presentan las características en el proyecto que no son compatibles con el origen de SQL Server hasta migrar la base de datos a una versión más reciente de SQL Server.     

        ![alt text](./media/sql-database-migrate-visualstudio-ssdt/10MigrateSSDT.png)    
    
        ![alt text](./media/sql-database-migrate-visualstudio-ssdt/11MigrateSSDT.png)    
        
4.  En el Explorador de objetos de SQL Server, haga clic en la base de datos de origen y haga clic en **Comparación de datos**. Comparar el proyecto a la base de datos original le ayuda a comprender qué cambios realizados por el asistente. Seleccione la versión de V12 de SQL Azure de la base de datos y, a continuación, haga clic en **Finalizar**.    
    
    ![texto alternativo](./media/sql-database-migrate-visualstudio-ssdt/12MigrateSSDT.png)    
    
    ![texto alternativo](./media/sql-database-migrate-visualstudio-ssdt/13MigrateSSDT.png)    

5.  Revisar las diferencias detectadas y, a continuación, haga clic en **Actualizar destino** para migrar datos de la base de datos de origen a la base de datos de V12 de SQL Azure.     
    
    ![texto alternativo](./media/sql-database-migrate-visualstudio-ssdt/14MigrateSSDT.png)    
    
6.  Elija un método de implementación. Consulte [migrar una base de datos de SQL Server compatible con base de datos de SQL.](sql-database-cloud-migrate.md)  

## <a name="next-steps"></a>Pasos siguientes

- [Versión más reciente de SSDT](https://msdn.microsoft.com/library/mt204009.aspx)
- [Versión más reciente de SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx)

## <a name="additional-resources"></a>Recursos adicionales

- [V12 de base de datos SQL](sql-database-v12-whats-new.md)
- [Transact-SQL parcialmente o no compatibles de funciones](sql-database-transact-sql-information.md)
- [Migrar las bases de datos no es de SQL Server con el Asistente de migración de SQL Server](http://blogs.msdn.com/b/ssma/)
