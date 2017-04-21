<properties
    pageTitle="Restaurar bases de datos habilitado estirar | Microsoft Azure"
    description="Obtenga información sobre cómo restaurar estirar\-habilitado para bases de datos."
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
    ms.date="08/01/2016"
    ms.author="douglasl"/>

# <a name="restore-stretch-enabled-databases"></a>Restaurar bases de datos habilitado estirar

Restaurar una copia de seguridad de base de datos cuando sea necesario para recuperar de muchos tipos de errores, los errores y desastres.

Para obtener más información acerca de la copia de seguridad, consulte [habilitado para expandir una copia de seguridad de bases de datos](sql-server-stretch-database-backup.md).

>   [AZURE.NOTE] Copia de seguridad es solo una parte de una completa alta disponibilidad y la solución de continuidad empresarial. Para obtener más información acerca de alta disponibilidad, vea [Soluciones de alta disponibilidad](https://msdn.microsoft.com/library/ms190202.aspx).

## <a name="restore-your-sql-server-data"></a>Restaurar los datos de SQL Server
Para recuperar errores del hardware o daños, restaurar el estiramiento\-habilitado para la base de datos de SQL Server desde una copia de seguridad. Aún puede usar los métodos de restauración de SQL Server que utiliza actualmente. Para obtener más información, vea [Restaurar introducción y recuperación](https://msdn.microsoft.com/library/ms191253.aspx).

Después de restaurar la base de datos de SQL Server, debe ejecutar el procedimiento almacenado **sys.sp_rda_reauthorize_db** para volver a establecer la conexión entre el estiramiento\-habilitado para la base de datos de SQL Server y la base de datos de Azure remota. Para obtener más información, vea [restaurar la conexión entre la base de datos de SQL Server y la base de datos de Azure remota](#restore-the-connection-between-the-sql-server-database-and-the-remote-azure-database).

## <a name="restore-your-remote-azure-data"></a>Restaurar los datos de Azure remotos

### <a name="recover-a-live-azure-database"></a>Recuperar una base de datos de Azure directo
La base de datos de SQL Server estirar del servicio de instantáneas Azure todos los datos actualizados al menos cada 8 horas mediante instantáneas de almacenamiento de Azure. Estas instantáneas se mantienen durante 7 días. Esto le permite restaurar los datos a uno de los puntos de 21 al menos en el tiempo dentro de los últimos 7 días hasta el momento cuando tomó la última instantánea.

Para restaurar una base de datos de Azure live en un momento anterior en el tiempo mediante el portal de Azure, haga lo siguiente.

1. Inicie sesión en el portal de Azure.
2. En el lado izquierdo de la pantalla Seleccione **Examinar** y, a continuación, seleccione **Las bases de datos de SQL**.
3. Vaya a la base de datos y selecciónelo.
4. En la parte superior del módulo de base de datos, haga clic en **Restaurar**.
5. Especifique un nuevo **nombre de la base de datos**, seleccione un **Punto de restauración** y, a continuación, haga clic en **crear**.
6. El proceso de restauración de la base de datos se iniciará y se puede supervisar mediante **notificaciones**.

### <a name="recover-a-deleted-azure-database"></a>Recuperar una base de datos de Azure eliminada
El servicio de base de datos de SQL Server estirar en Azure toma una instantánea de la base de datos antes de una base de datos se interrumpe y conserva durante 7 días. Cuando ocurre esto, ya no conserva instantáneas de la base de datos activa. Esto le permite restaurar una base de datos eliminado al punto cuando se eliminó.

Para restaurar una base de datos de Azure eliminado al punto cuando se eliminó a través del portal de Azure, haga lo siguiente.

1. Inicie sesión en el portal de Azure.
2. En el lado izquierdo de la pantalla Seleccione **Examinar** y, a continuación, seleccione **Servidores de SQL Server**.
3. Vaya al servidor y selecciónelo.
4. Desplácese hacia abajo hasta las operaciones en el módulo de su servidor, haga clic en el icono **Eliminar bases de datos** .
5. Seleccione la base de datos eliminado que desea restaurar.
5. Especifique un nuevo **nombre de la base de datos** y haga clic en **crear**.
6. El proceso de restauración de la base de datos se iniciará y se puede supervisar mediante **notificaciones**.

## <a name="restore-the-connection-between-the-sql-server-database-and-the-remote-azure-database"></a>Restaurar la conexión entre la base de datos de SQL Server y la base de datos de Azure remota

1.  Si va a conectarse a una base de Azure restaurada con un nombre diferente o en una región diferente, ejecute el procedimiento almacenado [sys.sp_rda_deauthorize_db](https://msdn.microsoft.com/library/mt703716.aspx) para desconectarse de la base de datos de Azure anterior.  

2.  Ejecutar el procedimiento almacenado [sys.sp_rda_reauthorize_db](https://msdn.microsoft.com/library/mt131016.aspx) para volver a conectar el estiramiento local\-habilitado para la base de datos de la base de datos de Azure.  

    -   Proporcionar la credencial de ámbito de la base de datos existente como un sysname o un varchar\(128\) valor. \(No utilice varchar\(max\).\) Puede buscar el nombre de la credencial en la vista **sys.database\_ámbito\_credenciales**.  

    -   Especifique si desea hacer una copia de los datos remotos y conectarse a la copia (recomendada).  

    ```tsql  
    USE <Stretch-enabled database name>;
    GO
    EXEC sp_rda_reauthorize_db
        @credential = N'<existing_database_scoped_credential_name>',
        @with_copy = 1 ;  
    GO
    ```  

## <a name="see-also"></a>Vea también

[Administrar y solucionar problemas de base de datos de estirar](sql-server-stretch-database-manage.md)

[Sys.sp_rda_reauthorize_db (Transact-SQL)](https://msdn.microsoft.com/library/mt131016.aspx)

[Realizar copias de seguridad y restauración de bases de datos SQL Server](https://msdn.microsoft.com/library/ms187048.aspx)
