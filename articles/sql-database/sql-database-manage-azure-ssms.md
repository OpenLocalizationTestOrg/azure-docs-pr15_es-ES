<properties 
    pageTitle="Administrar una base de datos SQL con SSMS | Microsoft Azure" 
    description="Aprenda a usar SQL Server Management Studio para administrar bases de datos y servidores de base de datos de SQL." 
    services="sql-database" 
    documentationCenter=".net" 
    authors="stevestein" 
    manager="jhubbard" 
    editor="tysonn"/>

<tags 
    ms.service="sql-database" 
    ms.workload="data-management" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/29/2016" 
    ms.author="sstein"/>


# <a name="managing-azure-sql-database-using-sql-server-management-studio"></a>Administrar la base de datos de SQL Azure con SQL Server Management Studio 


> [AZURE.SELECTOR]
- [Portal de Azure](sql-database-manage-portal.md)
- [SSMS](sql-database-manage-azure-ssms.md)
- [PowerShell](sql-database-manage-powershell.md)

Puede usar SQL Server Management Studio (SSMS) para administrar bases de datos y servidores de base de datos de SQL Azure. Este tema le guiará a través de las tareas comunes con SSMS. Ya debe tener un servidor y la base de datos de base de datos de SQL Azure antes de empezar. Para obtener más información, vea [crear su primera base de datos de SQL Azure](sql-database-get-started.md) y [Conectar y consulta con SSMS](sql-database-connect-query-ssms.md) .

Se recomienda usar la última versión de SSMS siempre que trabaje con la base de datos de SQL Azure. 

> [AZURE.IMPORTANT] Usar siempre la versión más reciente de SSMS porque mejora continua para trabajar con las últimas actualizaciones de Azure y base de datos de SQL. Para obtener la última versión, consulte [Descargar SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).



## <a name="create-and-manage-azure-sql-databases"></a>Crear y administrar bases de datos de SQL Azure

Mientras está conectado a **la base de datos** , puede crear bases de datos en el servidor, modificar o eliminar bases de datos existentes. Los pasos siguientes describen cómo realizar varias tareas de administración comunes de base de datos mediante Management Studio. Para realizar estas tareas, asegúrese de que está conectado a **la base de datos con el inicio de sesión principal de nivel de servidor que creó al configurar el servidor** .

Para abrir una ventana de consulta en Management Studio, abra la carpeta de bases de datos, expanda la carpeta de **Bases de datos de sistema** , haga clic en **patrón**y, a continuación, haga clic en **Nueva consulta**.

-   Utilice la instrucción **CREATE DATABASE** para crear una base de datos. Para obtener más información, vea [Crear base de datos (SQL)](https://msdn.microsoft.com/library/dn268335.aspx). La instrucción siguiente crea una base de datos denominada **myTestDB** y especifica que es una base de datos de Standard Edition S0 con un tamaño máximo predeterminado de 250 GB.

        CREATE DATABASE myTestDB
        (EDITION='Standard',
         SERVICE_OBJECTIVE='S0');

Haga clic en **Ejecutar** para ejecutar la consulta.

-   Utilice la instrucción **ALTER DATABASE** para modificar una base de datos existente, por ejemplo, si desea cambiar el nombre y la edición de la base de datos. Para obtener más información, vea [Modificar la base de datos (SQL)](https://msdn.microsoft.com/library/ms174269.aspx). La siguiente instrucción modifica la base de datos que creó en el paso anterior para cambiar edition a S1 estándar.

        ALTER DATABASE myTestDB
        MODIFY
        (SERVICE_OBJECTIVE='S1');

-   Utilice **La base de datos de anular** declaración para eliminar una base de datos existente. Para obtener más información, vea [Eliminar base de datos (SQL)](https://msdn.microsoft.com/library/ms178613.aspx). La siguiente instrucción elimina la base de datos de **myTestDB** , pero no colóquelos ahora debido a que usa para crear inicios de sesión en el siguiente paso.

        DROP DATABASE myTestBase;

-   La base de datos tiene la vista de **sys.databases** que puede usar para ver los detalles acerca de todas las bases de datos. Para ver todas las bases de datos existentes, ejecute la instrucción siguiente:

        SELECT * FROM sys.databases;

-   En la base de datos de SQL, la instrucción **USE** no es compatible para cambiar entre las bases de datos. En su lugar, debe establecer una conexión directa a la base de datos de destino.

>[AZURE.NOTE] Muchas de las instrucciones de Transact-SQL que crear o modifiquen una base de datos deben ejecutar dentro de su propio lote y no se pueden agrupar con otras instrucciones Transact-SQL. Para obtener más información, consulte la información específica de la declaración.

## <a name="create-and-manage-logins"></a>Crear y administrar inicios de sesión

**La base de datos** contiene inicios de sesión y qué inicios de sesión tienen permiso para crear bases de datos u otros inicios de sesión. Administrar inicios de sesión mediante una conexión a **la base de datos con el inicio de sesión principal de nivel de servidor que creó al configurar el servidor** . Puede usar las instrucciones de **Crear el inicio de sesión**, **ALTER LOGIN**o **Colocar el inicio de sesión** para ejecutar consultas en la base de datos que administra inicios de sesión a través de todo el servidor. Para obtener más información, vea [Administrar bases de datos e inicios de sesión de base de datos de SQL](http://msdn.microsoft.com/library/azure/ee336235.aspx). 


-   Utilice la instrucción **CREATE LOGIN** para crear un inicio de sesión de nivel de servidor. Para obtener más información, consulte [CREATE LOGIN (base de datos de SQL)](https://msdn.microsoft.com/library/ms189751.aspx). La siguiente instrucción crea un inicio de sesión llamado **login1**. Reemplazar **Contraseña1** con la contraseña de su elección.

        CREATE LOGIN login1 WITH password='password1';

-   Utilice la instrucción **CREATE USER** para conceder permisos de nivel de base de datos. Todos los inicios de sesión deben crearse en **la base de datos** . Para que un inicio de sesión para conectarse a una base de datos diferente, deben otorgar permisos de nivel de base de datos mediante la instrucción **CREATE USER** en dicha base de datos. Para obtener más información, vea [Crear usuario (base de datos de SQL)](https://msdn.microsoft.com/library/ms173463.aspx). 

-   Para conceder permisos de login1 para una base de datos denominado **myTestDB**, realice los pasos siguientes:

 1.  Para actualizar el Explorador de objetos para ver la base de datos **myTestDB** que ha creado, haga clic en el nombre del servidor en el Explorador de objetos y, a continuación, haga clic en **Actualizar**.  

     Si ha cerrado la conexión, puede volver a conectar seleccionando **Conectar Explorador de objetos** en el menú archivo.

 2. Haga clic en base de datos de **myTestDB** y seleccione **Nueva consulta**.

    3.  Ejecute la instrucción siguiente contra la base de datos de myTestDB para crear un usuario de base de datos denominado **login1User** que corresponde a la de inicio de sesión de nivel de servidor **login1**.

            CREATE USER login1User FROM LOGIN login1;

-   Usar el **sp\_addrolemember** procedimiento almacenado para dar a la cuenta de usuario en el nivel de permisos en la base de datos adecuado. Para obtener más información, vea [sp_addrolemember (Transact-SQL)](http://msdn.microsoft.com/library/ms187750.aspx). La siguiente instrucción proporciona **login1User** permisos de solo lectura a la base de datos agregando **login1User** a la **db\_datareader** rol.

        exec sp_addrolemember 'db_datareader', 'login1User';    

-   Utilice la instrucción **ALTER LOGIN** para modificar un inicio de sesión existente, por ejemplo, si desea cambiar la contraseña para el inicio de sesión. Para obtener más información, consulte [ALTER LOGIN (base de datos de SQL)](https://msdn.microsoft.com/library/ms189828.aspx). La instrucción **ALTER LOGIN** se debe ejecutar en **la base de datos** . Cambiar a la ventana de consulta está conectada a dicha base de datos. La siguiente instrucción modifica el inicio de sesión **login1** para restablecer la contraseña. Reemplazar **nuevaContraseña** con la contraseña de su elección y **oldPassword** con la contraseña actual para el inicio de sesión.

        ALTER LOGIN login1
        WITH PASSWORD = 'newPassword'
        OLD_PASSWORD = 'oldPassword';

-   Usar **El inicio de sesión de anular** declaración para eliminar un inicio de sesión existente. Eliminación de un inicio de sesión en el nivel de servidor, también elimina las cuentas de usuario de la base de datos asociada. Para obtener más información, vea [Eliminar base de datos (SQL)](https://msdn.microsoft.com/library/ms178613.aspx). La instrucción **DROP LOGIN** se debe ejecutar en **la base de datos** . La instrucción elimina el inicio de sesión **login1** .

        DROP LOGIN login1;

-   La base de datos tiene la **sys.sql\_inicios de sesión** ver que puede usar para ver los inicios de sesión. Para ver todas las conexiones existentes, ejecute la instrucción siguiente:

        SELECT * FROM sys.sql_logins;

## <a name="monitor-sql-database-using-dynamic-management-views"></a>Supervisar la base de datos de SQL con vistas de administración dinámicas

Base de datos SQL es compatible con varias vistas dinámicas de administración que puede usar para supervisar la base de datos individual. Algunos ejemplos del tipo de datos del monitor que puede recuperar mediante estas vistas son los siguientes. Para obtener información detallada y más ejemplos de uso, consulte [supervisión de base de datos de SQL con vistas de administración dinámicas](https://msdn.microsoft.com/library/azure/ff394114.aspx).

-   Consultar una vista de administración dinámica requiere permisos de **Estado de la base de datos de vista** . Para conceder permiso de **Estado de vista de base de datos** a un usuario específico de la base de datos, conéctese a la base de datos y ejecute la instrucción siguiente contra la base de datos:

        GRANT VIEW DATABASE STATE TO login1User;

-   Calcular el tamaño de base de datos utilizando la **sys.dm\_db\_partición\_estadísticas** vista. La **sys.dm\_db\_partición\_estadísticas** vista devuelve información de página y el número de filas para cada partición de la base de datos, puede usar para calcular el tamaño de la base de datos. La siguiente consulta devuelve el tamaño de la base de datos en megabytes:

        SELECT SUM(reserved_page_count)*8.0/1024
        FROM sys.dm_db_partition_stats;   

-   Usar la **sys.dm\_ejecutivo\_conexiones** y **sys.dm\_ejecutivo\_sesiones** vistas para recuperar información sobre las conexiones de usuario actuales e internas tareas asociadas con la base de datos. La siguiente consulta devuelve información acerca de la conexión actual:

        SELECT
            e.connection_id,
            s.session_id,
            s.login_name,
            s.last_request_end_time,
            s.cpu_time
        FROM
            sys.dm_exec_sessions s
            INNER JOIN sys.dm_exec_connections e
              ON s.session_id = e.session_id;

-   Usar la **sys.dm\_ejecutivo\_consulta\_estadísticas** vista para recuperar las estadísticas de rendimiento agregadas para planes de consulta en la caché. La siguiente consulta devuelve información acerca de las consultas de cinco principales según el tiempo de CPU promedio.

        SELECT TOP 5 query_stats.query_hash AS "Query Hash",
            SUM(query_stats.total_worker_time), SUM(query_stats.execution_count) AS "Avg CPU Time",
            MIN(query_stats.statement_text) AS "Statement Text"
        FROM
            (SELECT QS.*,
            SUBSTRING(ST.text, (QS.statement_start_offset/2) + 1,
            ((CASE statement_end_offset
                WHEN -1 THEN DATALENGTH(ST.text)
                ELSE QS.statement_end_offset END
                    - QS.statement_start_offset)/2) + 1) AS statement_text
             FROM sys.dm_exec_query_stats AS QS
             CROSS APPLY sys.dm_exec_sql_text(QS.sql_handle) as ST) as query_stats
        GROUP BY query_stats.query_hash
        ORDER BY 2 DESC;
 
 
