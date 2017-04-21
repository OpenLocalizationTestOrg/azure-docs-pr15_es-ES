<properties 
    pageTitle="Copiar una base de datos de SQL Azure con Transact-SQL | Microsoft Azure" 
    description="Crear copia de una base de datos de SQL Azure con Transact-SQL" 
    services="sql-database"
    documentationCenter=""
    authors="stevestein"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.date="09/19/2016"
    ms.author="sstein"
    ms.workload="data-management"
    ms.topic="article"
    ms.tgt_pltfrm="NA"/>


# <a name="copy-an-azure-sql-database-using-transact-sql"></a>Copiar una base de datos de SQL Azure con Transact-SQL


> [AZURE.SELECTOR]
- [Información general](sql-database-copy.md)
- [Portal de Azure](sql-database-copy-portal.md)
- [PowerShell](sql-database-copy-powershell.md)
- [T-SQL](sql-database-copy-transact-sql.md)


En estos pasos siguientes muestran cómo copiar una base de datos SQL con Transact-SQL en el mismo servidor o en un servidor distinto. La operación de copia de la base de datos utiliza la instrucción [CREATE DATABASE](https://msdn.microsoft.com/library/ms176061.aspx) .

Para completar los pasos de este artículo, se necesita lo siguiente:

- Una suscripción de Azure. Si necesita una suscripción de Azure simplemente haga clic en la **Versión de prueba gratuita** en la parte superior de esta página y, a continuación, vuelva a fin de este artículo.
- Una base de datos SQL Azure. Si no tiene una base de datos SQL, crear una siguiendo los pasos de este artículo: [crear la primera base de datos de SQL Azure](sql-database-get-started.md).
- [SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/ms174173.aspx). Si no tiene SSMS, o si las características descritas en este artículo no están disponibles, [Descargue la versión más reciente](https://msdn.microsoft.com/library/mt238290.aspx).


## <a name="copy-your-sql-database"></a>Copie la base de datos SQL

Inicie sesión en la base de datos con el inicio de sesión principal de nivel de servidor o el inicio de sesión que creó la base de datos que desea copiar. Inicios de sesión que no son la entidad de seguridad de nivel de servidor deben ser miembros de la función dbmanager para copiar bases de datos. Para obtener más información acerca de inicios de sesión y conectarse al servidor, vea [administrar inicios de sesión](sql-database-manage-logins.md).

Iniciar la copia de la base de datos de origen con la instrucción [CREATE DATABASE](https://msdn.microsoft.com/library/ms176061.aspx) . Ejecutar esta instrucción inicia el proceso de copia de la base de datos. Dado que copiar una base de datos es un proceso asincrónico, la instrucción CREATE DATABASE devuelve antes de la base de datos completa copiar.


### <a name="copy-a-sql-database-to-the-same-server"></a>Copiar una base de datos SQL en el mismo servidor

Inicie sesión en la base de datos con el inicio de sesión principal de nivel de servidor o el inicio de sesión que creó la base de datos que desea copiar. Inicios de sesión que no son la entidad de seguridad de nivel de servidor deben ser miembros de la función dbmanager para copiar bases de datos.

Este copias de comando Database1 en una nueva base de datos denominada Database2 en el mismo servidor. Dependiendo del tamaño de la base de datos, la operación de copia puede tardar algún tiempo en completarse.

    -- Execute on the master database.
    -- Start copying.
    CREATE DATABASE Database1_copy AS COPY OF Database1;

### <a name="copy-a-sql-database-to-a-different-server"></a>Copiar una base de datos SQL en un servidor diferente

Inicie sesión en la base de datos del servidor de destino, el servidor de base de datos de SQL Azure donde se crearán la nueva base de datos. Use un inicio de sesión que tiene el mismo nombre y contraseña como la base de datos propietario de la base de datos de origen en el servidor de base de datos de SQL Azure de origen. El inicio de sesión en el servidor de destino también debe ser miembro del rol dbmanager o ser el inicio de sesión principal de nivel de servidor.

Este comando copia Database1 en servidor 1 - en una nueva base de datos denominado Database2 en el servidor 2. Dependiendo del tamaño de la base de datos, la operación de copia puede tardar algún tiempo en completarse.


    -- Execute on the master database of the target server (server2)
    -- Start copying from Server1 to Server2
    CREATE DATABASE Database1_copy AS COPY OF server1.Database1;
    

## <a name="monitor-the-progress-of-the-copy-operation"></a>Supervisar el progreso de la operación de copia

Supervisar el proceso de copia consultando las vistas sys.databases y sys.dm_database_copies. Mientras que la copia está en curso, la columna state_desc de la vista de sys.databases para la nueva base de datos se establece en copiar.


- Si se produce un error en la copia, la columna state_desc de la vista de sys.databases para la nueva base de datos se establece en SOSPECHOSO. En este caso, ejecute la instrucción DROP en la nueva base de datos y vuelva a intentarlo.
- Si la copia se realiza correctamente, la columna state_desc de la vista de sys.databases para la nueva base de datos se establece en línea. En este caso, la copia es completa y la nueva base de datos es una base de datos normal, pueden cambiar independientemente de la base de datos de origen.

> [AZURE.NOTE] -Si decide cancelar la copia mientras está en curso, ejecute la instrucción [DROP DATABASE](https://msdn.microsoft.com/library/ms178613.aspx) en la base de datos nueva. También puede ejecutar la instrucción DROP DATABASE en la base de datos de origen también cancela el proceso de copia.


## <a name="resolve-logins-after-the-copy-operation-completes"></a>Resolver inicios de sesión después de que finalice la operación de copia

Después de la nueva base de datos está en línea en el servidor de destino, use la instrucción [ALTER USER](https://msdn.microsoft.com/library/ms176060.aspx) para volver a asignar los usuarios de la base de datos nueva a inicios de sesión en el servidor de destino. Para resolver usuarios huérfanos, consulte [Solucionar problemas de usuarios huérfanos](https://msdn.microsoft.com/library/ms175475.aspx). Consulte también [cómo administrar la seguridad de base de datos de SQL Azure tras la recuperación](sql-database-geo-replication-security-config.md).

Todos los usuarios de la nueva base de datos mantienen los permisos que tenían en la base de datos de origen. El usuario que ha iniciado la copia de la base de datos se convierte en el propietario de la base de datos de la nueva base de datos y se asigna un nuevo identificador de seguridad (SID). Después de que la copia se realiza correctamente y antes de la reasignación de otros usuarios, solo el inicio de sesión que ha iniciado la copia, la base de datos propietario, puede iniciar sesión en la nueva base de datos.


## <a name="next-steps"></a>Pasos siguientes

- Para obtener información general de la copia de una base de datos de SQL Azure, vea [Copiar una base de datos de SQL Azure](sql-database-copy.md) .
- Consulte [Copiar una base de datos de SQL Azure con el portal de Azure](sql-database-copy-portal.md) para copiar una base de datos con el portal de Azure.
- Consulte [Copiar una base de datos de SQL Azure con PowerShell](sql-database-copy-powershell.md) para copiar una base de datos con PowerShell.
- Consulte [cómo administrar la seguridad de la base de datos de SQL Azure tras la recuperación](sql-database-geo-replication-security-config.md) para obtener información acerca de cómo administrar usuarios y los inicios de sesión al copiar una base de datos a otro servidor lógico.



## <a name="additional-resources"></a>Recursos adicionales

- [Administrar inicios de sesión](sql-database-manage-logins.md)
- [Conectarse a la base de datos de SQL con SQL Server Management Studio y realizar una consulta de SQL de T de ejemplo](sql-database-connect-query-ssms.md)
- [Exportar la base de datos a una MOCHILA](sql-database-export.md)
- [Información general de continuidad empresarial](sql-database-business-continuity.md)
- [Documentación de la base de datos SQL](https://azure.microsoft.com/documentation/services/sql-database/)


