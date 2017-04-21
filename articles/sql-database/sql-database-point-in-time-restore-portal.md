<properties
    pageTitle="Restaurar una base de datos de SQL Azure a un punto anterior en el tiempo (portal de Azure) | Microsoft Azure"
    description="Restaurar una base de datos de SQL Azure a un punto anterior en el tiempo."
    services="sql-database"
    documentationCenter=""
    authors="stevestein"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.date="10/18/2016"
    ms.author="sstein"
    ms.workload="NA"
    ms.topic="article"
    ms.tgt_pltfrm="NA"/>


# <a name="restore-an-azure-sql-database-to-a-previous-point-in-time-with-the-azure-portal"></a>Restaurar una base de datos de SQL Azure a un punto anterior en el tiempo con el portal de Azure


> [AZURE.SELECTOR]
- [Información general](sql-database-recovery-using-backups.md)
- [Restaurar un momento: PowerShell](sql-database-point-in-time-restore-powershell.md)

Este artículo le muestra cómo restaurar la base de datos en un momento anterior en el tiempo de [que base de datos SQL automatizado copias de seguridad](sql-database-automated-backups.md) con el portal de Azure.

## <a name="restore-a-sql-database-to-a-previous-point-in-time"></a>Restaurar una base de datos SQL a un punto anterior en el tiempo

Seleccione una base de datos para restaurar en el portal de Azure:

1.  Abra el [portal de Azure](https://portal.azure.com).
2.  En el lado izquierdo de la pantalla, seleccione **más servicios** > **bases de datos SQL**.
3.  Haga clic en la base de datos que desea restaurar.
4.  En la parte superior de la página de la base de datos, seleccione **Restaurar**:

    ![Restaurar una base de datos de SQL Azure](./media/sql-database-point-in-time-restore-portal/restore.png)

5.  En la página **Restaurar** , seleccione la fecha y hora (en UTC) para restaurar la base de datos y, a continuación, haga clic en **Aceptar**:

    ![Restaurar una base de datos de SQL Azure](./media/sql-database-point-in-time-restore-portal/restore-details.png)

## <a name="monitor-the-restore-operation"></a>Supervisar la operación de restauración

1. Después de hacer clic en **Aceptar** en el paso anterior, haga clic en el icono de notificación en la parte superior derecha de la página y haga clic en la notificación de la **base de datos de SQL restaurar** para obtener información detallada.

    ![Restaurar una base de datos de SQL Azure](./media/sql-database-point-in-time-restore-portal/notification-icon.png)

2. Se abrirá la página de la base de datos de SQL restaurar con información sobre el estado de la restauración. Puede hacer clic en el elemento de línea para obtener más detalles:

    ![Restaurar una base de datos de SQL Azure](./media/sql-database-point-in-time-restore-portal/inprogress.png)

 

## <a name="next-steps"></a>Pasos siguientes

- Para obtener una descripción de la continuidad empresarial y escenarios, vea [información general de continuidad empresarial](sql-database-business-continuity.md)
- Para obtener información sobre las copias de seguridad automatizadas de base de datos SQL Azure, consulte [copias de seguridad de automatizado de base de datos SQL](sql-database-automated-backups.md)
- Para obtener información sobre el uso de copias de seguridad automatizadas de recuperación, consulte [restaurar una base de datos de las copias de seguridad iniciado por el servicio](sql-database-recovery-using-backups.md)
- Para obtener información sobre las opciones de recuperación más rápidas, vea [Replicación de Geo activo](sql-database-geo-replication-overview.md)  
- Para obtener información sobre el uso de copias de seguridad automatizadas de archivado, vea [Copiar de base de datos](sql-database-copy.md)
