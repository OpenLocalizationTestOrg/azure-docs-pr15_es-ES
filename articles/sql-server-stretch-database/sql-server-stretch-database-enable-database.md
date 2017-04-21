<properties
    pageTitle="Habilitar estiramiento de base de datos para una base de datos | Microsoft Azure"
    description="Obtenga información sobre cómo configurar una base de datos para la base de datos de estirar."
    services="sql-server-stretch-database"
    documentationCenter=""
    authors="douglaslMS"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="sql-server-stretch-database"
    ms.workload="data-management"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/05/2016"
    ms.author="douglasl"/>

# <a name="enable-stretch-database-for-a-database"></a>Habilitar estiramiento de base de datos para una base de datos

Para configurar una base de datos existente para expandir base de datos, seleccione tareas **| Estirar | Habilitar** para una base de datos de SQL Server Management Studio para abrir el Asistente de **Habilitar la base de datos para expandir** . También puede usar Transact\-SQL para habilitar estiramiento de base de datos para una base de datos.

Si selecciona tareas **| Estirar | Habilitar** para una tabla individual y aún no ha habilitado la base de datos para expandir base de datos, el asistente configura la base de datos para expandir base de datos y le permite seleccionar tablas como parte del proceso. Siga los pasos de este tema en lugar de los pasos de [Habilitar estiramiento de base de datos de una tabla](sql-server-stretch-database-enable-database.md).

Habilitar la base de datos de estirar en una base de datos o una tabla requiere db\_permisos de propietario. Habilitar estiramiento de base de datos en una base de datos requiere también permisos de base de datos de CONTROL.

 >   [AZURE.NOTE] Más adelante, si deshabilita la base de datos de estirar, recuerde que la deshabilitación de la base de datos de estirar para una tabla o una base de datos no elimina el objeto remoto. Si desea eliminar la tabla remota o la base de datos remota, tiene que ocupa a través del portal de administración de Azure. Los objetos remotos seguirán contraer costos Azure hasta que se eliminen manualmente.

## <a name="before-you-get-started"></a>Antes de empezar

-   Antes de configurar una base de datos para expandir, se recomienda que ejecute el Asistente de la base de datos de estirar para identificar las bases de datos y tablas que son elegibles para estirar. El Asistente de la base de datos de estirar también identifica los problemas de bloqueo. Para obtener más información, consulte [Identificar bases de datos y las tablas de base de datos de estirar](sql-server-stretch-database-identify-databases.md).

-   Revise [las limitaciones para expandir la base de datos](sql-server-stretch-database-limitations.md).

-   Base de datos extendido migra datos a Azure. Por lo tanto, debe tener una cuenta de Azure y una suscripción para facturación. Para obtener una cuenta de Azure, [haga clic aquí](http://azure.microsoft.com/pricing/free-trial/).

-   Tiene la información de conexión e inicio de sesión necesarias para crear un nuevo servidor de Azure o para seleccionar un servidor de Azure existente.

## <a name="EnableTSQLServer"></a>Requisito previo: Habilitar estiramiento de base de datos en el servidor
Para poder habilitar estiramiento de base de datos en una base de datos o una tabla, debe habilitar en el servidor local. Esta operación requiere permisos de administrador del sistema o serveradmin.

-   Si tiene los permisos administrativos necesarios, el Asistente para **Habilitar la base de datos para expandir** configura el servidor para estirar.

-   Si no tiene los permisos necesarios, un administrador debe habilitar la opción manualmente ejecutando **sp\_configurar** antes de ejecutar el asistente o un administrador debe ejecutar el asistente.

Para habilitar manualmente estiramiento de base de datos en el servidor, ejecute **sp\_configurar** y activa la opción de **archivo de datos remota** . En el ejemplo siguiente se habilita la opción de **almacenamiento de datos remotos** estableciendo su valor en 1.

```
EXEC sp_configure 'remote data archive' , '1';
GO

RECONFIGURE;
GO
```
Para obtener más información, vea [configurar los datos remotos archivar la opción de configuración de servidor](https://msdn.microsoft.com/library/mt143175.aspx) y [sp_configure (Transact-SQL)](https://msdn.microsoft.com/library/ms188787.aspx).

## <a name="Wizard"></a>Utilice el Asistente para habilitar estiramiento de base de datos en una base de datos
Para obtener información acerca de la base de datos de habilitar para estirar asistente, incluida la información que tenga que escribir y las opciones que se deben realizar, vea [Introducción al ejecutar la base de datos habilitar estirar Asistente para](sql-server-stretch-database-wizard.md).

## <a name="EnableTSQLDatabase"></a>Usar Transact\-SQL para habilitar estiramiento de base de datos en una base de datos
Para poder habilitar estiramiento de base de datos en tablas individuales, debe habilitar la base de datos.

Habilitar la base de datos de estirar en una base de datos o una tabla requiere db\_permisos de propietario. Habilitar estiramiento de base de datos en una base de datos requiere también permisos de base de datos de CONTROL.

1.  Antes de comenzar, elija un servidor de Azure existente para los datos que migra de base de datos de estirar o crear un nuevo servidor de Azure.

2.  En el servidor de Azure, cree una regla de firewall con el intervalo de direcciones IP de SQL Server que permite a SQL Server comunicarse con el servidor remoto.

    Puede encontrar fácilmente los valores que necesite y cree la regla de firewall para intentar conectarse al servidor de Azure desde el Explorador de objetos en SQL Server Management Studio (SSMS). SSMS le ayuda a crear la regla al abrir el cuadro de diálogo siguiente que ya incluye los valores requeridos de dirección IP.

    ![Crear una regla de firewall en SSMS][FirewallRule]

3.  Para configurar una base de datos de SQL Server para expandir base de datos, la base de datos debe tener una clave principal de base de datos. La clave principal de base de datos protege las credenciales de base de datos de estirar para conectarse a la base de datos remota. Aquí tenemos un ejemplo que crea una nueva clave principal de base de datos.

    ```tsql
    USE <database>;
    GO

    CREATE MASTER KEY ENCRYPTION BY PASSWORD ='<password>';
    GO
    ```

    Para obtener más información acerca de la clave principal de base de datos, vea [Crear clave principal (Transact-SQL)](https://msdn.microsoft.com/library/ms174382.aspx) y [crear una clave principal de base de datos](https://msdn.microsoft.com/library/aa337551.aspx).

4.  Cuando configura una base de datos para expandir base de datos, debe proporcionar una credencial para expandir base de datos para la comunicación entre el local de SQL Server y el servidor remoto de Azure. Tiene dos opciones.

    -   Puede proporcionar una credencial de administrador.

        -   Si habilita estiramiento de base de datos, ejecute al asistente, puede crear la credencial en ese momento.

        -   Si va a habilitar la base de datos de estirar ejecutando **Modificar base de datos**, debe crear la credencial manualmente antes de ejecutar **ALTER DATABASE** para habilitar la base de datos de estirar.

        Aquí tenemos un ejemplo que crea una nueva credencial.

        ```tsql
        CREATE DATABASE SCOPED CREDENTIAL <db_scoped_credential_name>
            WITH IDENTITY = '<identity>' , SECRET = '<secret>';
        GO
        ```

        Para obtener más información acerca de la credencial, vea [Crear base de datos de ámbito de la CREDENCIAL (Transact-SQL)](https://msdn.microsoft.com/library/mt270260.aspx). Crear la credencial requiere permisos ALTER ANY CREDENTIAL.

    -   Puede usar una cuenta de servicio federado para SQL Server para comunicarse con el servidor remoto de Azure cuando se cumplen todas las condiciones siguientes.

        -   La cuenta de servicio en el que se ejecuta la instancia de SQL Server es una cuenta de dominio.

        -   La cuenta de dominio pertenece a un dominio federado cuyo Active Directory con Azure Active Directory.

        -   El servidor remoto de Azure está configurado para admitir autenticación de Azure Active Directory.

        -   La cuenta de servicio en el que se ejecuta la instancia de SQL Server debe estar configurada como una cuenta dbmanager o sysadmin en el servidor remoto de Azure.

5.  Para configurar una base de datos para expandir base de datos, ejecute el comando Modificar base de datos.

    1.  Para el argumento de servidor, proporcione el nombre de un servidor de Azure existente, incluida la `.database.windows.net` parte del nombre de \- por ejemplo, `MyStretchDatabaseServer.database.windows.net`.

    2.  Proporcionar una credencial de administrador existente con el argumento de CREDENCIAL o especificar FEDERADO\_SERVICE\_cuenta = ON. En el ejemplo siguiente se proporciona una credencial existente.

    ```tsql
    ALTER DATABASE <database name>
        SET REMOTE_DATA_ARCHIVE = ON
            (
                SERVER = '<server_name>',
                CREDENTIAL = <db_scoped_credential_name>
            ) ;
    GO
    ```

## <a name="next-steps"></a>Pasos siguientes
-   [Habilitar estiramiento de base de datos de una tabla](sql-server-stretch-database-enable-table.md) para habilitar tablas adicionales.

-   [Base de datos de monitor estirar](sql-server-stretch-database-monitor.md) el estado de la migración de datos.

-   [Pausar y reanudar la base de datos de estirar](sql-server-stretch-database-pause.md)

-   [Administrar y solucionar problemas de base de datos de estirar](sql-server-stretch-database-manage.md)

-   [Copia de seguridad habilitados para expandir las bases de datos](sql-server-stretch-database-backup.md)

## <a name="see-also"></a>Vea también

[Identificar las bases de datos y tablas de base de datos de estirar](sql-server-stretch-database-identify-databases.md)

[Opciones de conjunto de base de datos de ALTER (Transact-SQL)](https://msdn.microsoft.com/library/bb522682.aspx)

[FirewallRule]: ./media/sql-server-stretch-database-enable-database/firewall.png
