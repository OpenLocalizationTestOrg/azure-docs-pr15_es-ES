<properties
    pageTitle="Restaurar una base de datos de SQL Azure desde una copia de seguridad automática (portal de Azure) | Microsoft Azure"
    description="Restaurar una base de datos de SQL Azure desde una copia de seguridad automática (portal de Azure)."
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


# <a name="restore-an-azure-sql-database-from-an-automatic-backup-using-the-azure-portal"></a>Restaurar una base de datos de SQL Azure desde una copia de seguridad automática con el portal de Azure


> [AZURE.SELECTOR]
- [Información general](sql-database-recovery-using-backups.md#geo-restore)
- [Restaurar geo: PowerShell](sql-database-geo-restore-powershell.md)

Este artículo muestra cómo restaurar la base de datos desde una [copia de seguridad automática](sql-database-automated-backups.md) en un servidor nuevo con [Geo restaurar](sql-database-recovery-using-backups/.md#geo-restore) con el portal de Azure.

## <a name="select-a-database-to-restore"></a>Seleccione Restaurar una base de datos

Para restaurar una base de datos en el portal de Azure, siga los pasos siguientes:

1.  Vaya al [portal de Azure](https://portal.azure.com).
2.  En el lado izquierdo de la pantalla, seleccione **+ nuevo** > **bases de datos** > **Base de datos SQL**:

    ![Restaurar una base de datos de SQL Azure](./media/sql-database-geo-restore-portal/new-sql-database.png)

3.  Seleccione **una copia de seguridad** como el origen y, a continuación, seleccione la copia de seguridad que desea restaurar. Especifique un nombre de base de datos, un servidor que desea restaurar la base de datos, y, a continuación, haga clic en **crear**:
  
    ![Restaurar una base de datos de SQL Azure](./media/sql-database-geo-restore-portal/geo-restore.png)

Supervisar el estado de la operación de restauración haciendo clic en el icono de notificación en la esquina superior derecha de la página. 


## <a name="next-steps"></a>Pasos siguientes

- Para obtener una descripción de la continuidad empresarial y escenarios, vea [información general de continuidad empresarial](sql-database-business-continuity.md)
- Para obtener información sobre las copias de seguridad automatizadas de base de datos SQL Azure, consulte [copias de seguridad de automatizado de base de datos SQL](sql-database-automated-backups.md)
- Para obtener información sobre el uso de copias de seguridad automatizadas de recuperación, consulte [restaurar una base de datos de las copias de seguridad iniciado por el servicio](sql-database-recovery-using-backups.md)
- Para obtener información sobre las opciones de recuperación más rápidas, vea [Replicación de Geo activo](sql-database-geo-replication-overview.md)  
- Para obtener información sobre el uso de copias de seguridad automatizadas de archivado, vea [Copiar de base de datos](sql-database-copy.md)
