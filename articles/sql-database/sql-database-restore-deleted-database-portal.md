<properties
    pageTitle="Restaurar una base de datos de SQL Azure eliminado (portal de Azure) | Microsoft Azure"
    description="Restaurar una base de datos de SQL Azure eliminado (portal de Azure)."
    services="sql-database"
    documentationCenter=""
    authors="stevestein"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.date="10/12/2016"
    ms.author="sstein"
    ms.workload="NA"
    ms.topic="article"
    ms.tgt_pltfrm="NA"/>


# <a name="restore-a-deleted-azure-sql-database-using-the-azure-portal"></a>Restaurar una base de datos de SQL Azure eliminado con el Portal de Azure

> [AZURE.SELECTOR]
- [Información general](sql-database-recovery-using-backups.md)
- [**Restaurar eliminados DB: Portal**](sql-database-restore-deleted-database-portal.md)
- [Restaurar eliminados DB: PowerShell](sql-database-restore-deleted-database-powershell.md)

## <a name="select-the-database-to-restore"></a>Seleccione la base de datos para restaurar 

Para restaurar una base de datos eliminado en el portal de Azure:

1.  En el [portal de Azure](https://portal.azure.com), haga clic en **más servicios** > **servidores SQL Server**.
3.  Seleccione el servidor que contiene la base de datos que desea restaurar.
4.  Desplácese hacia abajo hasta la sección **operaciones** de su servidor de hoja y seleccione **las bases de datos eliminados**: ![restaurar una base de datos de SQL Azure](./media/sql-database-restore-deleted-database-portal/restore-deleted-trashbin.png)
5.  Seleccione la base de datos que desea restaurar.
6.  Especifique un nombre de base de datos y haga clic en **Aceptar**:

    ![Restaurar una base de datos de SQL Azure](./media/sql-database-restore-deleted-database-portal/restore-deleted.png)


## <a name="next-steps"></a>Pasos siguientes

- Para obtener una descripción de la continuidad empresarial y escenarios, vea [información general de continuidad empresarial](sql-database-business-continuity.md)
- Para obtener información sobre las copias de seguridad automatizadas de base de datos SQL Azure, consulte [copias de seguridad de automatizado de base de datos SQL](sql-database-automated-backups.md)
- Para obtener información sobre el uso de copias de seguridad automatizadas de recuperación, consulte [restaurar una base de datos de las copias de seguridad iniciado por el servicio](sql-database-recovery-using-backups.md)
- Para obtener información sobre las opciones de recuperación más rápidas, vea [Replicación de Geo activo](sql-database-geo-replication-overview.md)  
- Para obtener información sobre el uso de copias de seguridad automatizadas de archivado, vea [Copiar de base de datos](sql-database-copy.md)
