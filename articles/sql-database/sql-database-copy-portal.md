<properties
    pageTitle="Copiar una base de datos de SQL Azure con el portal de Azure | Microsoft Azure"
    description="Crear una copia de una base de datos de SQL Azure"
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



# <a name="copy-an-azure-sql-database-using-the-azure-portal"></a>Copiar una base de datos de SQL Azure con el portal de Azure

> [AZURE.SELECTOR]
- [Información general](sql-database-copy.md)
- [Portal de Azure](sql-database-copy-portal.md)
- [PowerShell](sql-database-copy-powershell.md)
- [T-SQL](sql-database-copy-transact-sql.md)

Los pasos siguientes muestran cómo copiar una base de datos SQL con el [portal de Azure](https://portal.azure.com) en el mismo servidor o un servidor diferente.

Para copiar una base de datos SQL, necesitará los elementos siguientes:

- Una suscripción de Azure. Si necesita una suscripción de Azure simplemente haga clic en la **Versión de prueba gratuita** en la parte superior de esta página y, a continuación, vuelva a fin de este artículo.
- Base de datos SQL para copiar. Si no tiene una base de datos SQL, crear una siguiendo los pasos de este artículo: [crear la primera base de datos de SQL Azure](sql-database-get-started.md).


## <a name="copy-your-sql-database"></a>Copie la base de datos SQL

Abra la página de base de datos SQL de la base de datos que desea copiar:

1.  Vaya al [portal de Azure](https://portal.azure.com).
2.  Haga clic en **más servicios** > **bases de datos SQL**y, a continuación, haga clic en la base de datos que desee.
3.  En la página de la base de datos SQL, haga clic en **Copiar**:

    ![Base de datos SQL](./media/sql-database-copy-portal/sql-database-copy.png)

1.  En la página **Copiar** , se proporciona un nombre de base de datos predeterminado. Escriba un nombre diferente si desea (todas las bases de datos en un servidor deben tener nombres únicos).
2.  Seleccione un **servidor de destino**. El servidor de destino es donde se crea la copia de la base de datos. Puede copiar la base de datos en el mismo servidor o en un servidor distinto. Puede crear un servidor o seleccione un servidor existente en la lista. 
3.  Después de seleccionar el **servidor de destino**, el **grupo elásticos de la base de datos**y el **nivel de precios** se habilitarán opciones. Si el servidor tiene un grupo, puede copiar la base de datos en él.
3.  Haga clic en **Aceptar** para iniciar el proceso de copia.

    ![Base de datos SQL](./media/sql-database-copy-portal/copy-page.png)


## <a name="monitor-the-progress-of-the-copy-operation"></a>Supervisar el progreso de la operación de copia

- Después de iniciar la copia, haga clic en la notificación de portal para obtener más detalles.

    ![notificación][3]
 
    ![Monitor][4]


## <a name="verify-the-database-is-live-on-the-server"></a>Comprobar que la base de datos está activo en el servidor

- Haga clic en **más servicios** > **bases de datos SQL** y compruebe la nueva base de datos está **en línea**.


## <a name="resolve-logins"></a>Resolver inicios de sesión

Para resolver los inicios de sesión después de que finalice la operación de copia, vea [resolver inicios de sesión](sql-database-copy-transact-sql.md#resolve-logins-after-the-copy-operation-completes)


## <a name="next-steps"></a>Pasos siguientes

- Para obtener información general de la copia de una base de datos de SQL Azure, vea [Copiar una base de datos de SQL Azure](sql-database-copy.md) .
- Consulte [Copiar una base de datos de SQL Azure con PowerShell](sql-database-copy-powershell.md) para copiar una base de datos con PowerShell.
- Consulte [Copiar una base de datos de SQL Azure con SQL T](sql-database-copy-transact-sql.md) para copiar una base de datos mediante Transact-SQL.
- Consulte [cómo administrar la seguridad de la base de datos de SQL Azure tras la recuperación](sql-database-geo-replication-security-config.md) para obtener información acerca de cómo administrar usuarios y los inicios de sesión al copiar una base de datos a otro servidor lógico.



## <a name="additional-resources"></a>Recursos adicionales

- [Administrar inicios de sesión](sql-database-manage-logins.md)
- [Conectarse a la base de datos de SQL con SQL Server Management Studio y realizar una consulta de SQL de T de ejemplo](sql-database-connect-query-ssms.md)
- [Exportar la base de datos a una MOCHILA](sql-database-export.md)
- [Información general de continuidad empresarial](sql-database-business-continuity.md)
- [Documentación de la base de datos SQL](https://azure.microsoft.com/documentation/services/sql-database/)




<!--Image references-->
[1]: ./media/sql-database-copy-portal/copy.png
[2]: ./media/sql-database-copy-portal/copy-ok.png
[3]: ./media/sql-database-copy-portal/copy-notification.png
[4]: ./media/sql-database-copy-portal/monitor-copy.png

