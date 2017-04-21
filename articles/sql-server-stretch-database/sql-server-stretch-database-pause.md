<properties
    pageTitle="Pausar y reanudar la migración de datos (base de datos de estirar) | Microsoft Azure"
    description="Obtenga información sobre cómo pausar o reanudar la migración de datos de Azure."
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
    ms.date="06/14/2016"
    ms.author="douglasl"/>

# <a name="pause-and-resume-data-migration-stretch-database"></a>Pausar y reanudar la migración de datos (base de datos de estirar)

Para pausar o reanudar la migración de datos de Azure, seleccione **estirar** para una tabla de SQL Server Management Studio y, a continuación, seleccione **haga una pausa** para detener la migración de datos o **Reanudar** para reanudar la migración de datos. También puede usar Transact\-SQL para pausar o reanudar la migración de datos.

Migración de datos de pausa en tablas individuales cuando desea solucionar problemas en el servidor local o maximizar el ancho de banda de red disponible.

## <a name="pause-data-migration"></a>Migración de datos de pausa

### <a name="use-sql-server-management-studio-to-pause-data-migration"></a>Usar SQL Server Management Studio para pausar la migración de datos

1.  En SQL Server Management Studio, en el Explorador de objetos, seleccione el estiramiento\-habilitado para el que desea pausar la migración de datos de tabla.

2.  Derecha\-seleccione **estirar**, haga clic en y, a continuación, seleccione **Detener**.

### <a name="use-transact-sql-to-pause-data-migration"></a>Usar Transact\-SQL para pausar la migración de datos
Ejecute el siguiente comando.

```tsql
USE <Stretch-enabled database name>;
GO
ALTER TABLE <Stretch-enabled table name>  
    SET ( REMOTE_DATA_ARCHIVE ( MIGRATION_STATE = PAUSED ) ) ;  
GO
```

## <a name="resume-data-migration"></a>Reanudar la migración de datos

### <a name="use-sql-server-management-studio-to-resume-data-migration"></a>Usar SQL Server Management Studio para reanudar la migración de datos

1.  En SQL Server Management Studio, en el Explorador de objetos, seleccione el estiramiento\-habilitado para el que desea reanudar la migración de datos de tabla.

2.  Derecha\-seleccione **estirar**, haga clic en y, a continuación, seleccione **Reanudar**.

### <a name="use-transact-sql-to-resume-data-migration"></a>Usar Transact\-SQL para reanudar la migración de datos
Ejecute el siguiente comando.

```tsql
USE <Stretch-enabled database name>;
GO
ALTER TABLE <Stretch-enabled table name>   
    SET ( REMOTE_DATA_ARCHIVE ( MIGRATION_STATE = OUTBOUND ) ) ;  
 GO
```

## <a name="check-whether-migration-is-active-or-paused"></a>Compruebe si la migración está activa o está en pausa

### <a name="use-sql-server-management-studio-to-check-whether-migration-is-active-or-paused"></a>Usar SQL Server Management Studio para comprobar si la migración está activa o está en pausa
En SQL Server Management Studio, abra el **Monitor de base de datos de estirar** y compruebe el valor de la columna **Estado de la migración** . Para obtener más información, vea [Monitor y solucionar problemas de migración de datos](sql-server-stretch-database-monitor.md).

### <a name="use-transact-sql-to-check-whether-migration-is-active-or-paused"></a>Utilice Transact-SQL para comprobar si la migración está activa o está en pausa
Consultar la vista de catálogo **sys.remote_data_archive_tables** y compruebe el valor de la columna **is_migration_paused** . Para obtener más información, consulte [sys.remote_data_archive_tables](https://msdn.microsoft.com/library/dn935003.aspx).

## <a name="see-also"></a>Vea también

[ALTER TABLE (Transact-SQL)](https://msdn.microsoft.com/library/ms190273.aspx)
[Monitor y solucionar problemas de migración de datos](sql-server-stretch-database-monitor.md)
