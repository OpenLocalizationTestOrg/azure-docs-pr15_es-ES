<properties
    pageTitle="Administrar y solucionar problemas de base de datos de estirar | Microsoft Azure"
    description="Obtenga información sobre cómo administrar y solucionar problemas de base de datos de estirar."
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
    ms.date="06/27/2016"
    ms.author="douglasl"/>

# <a name="manage-and-troubleshoot-stretch-database"></a>Administrar y solucionar problemas de base de datos de estirar

Para administrar y solucionar problemas de base de datos de ampliación, use las herramientas y los métodos descritos en este tema.

## <a name="manage-local-data"></a>Administrar datos locales

### <a name="LocalInfo"></a>Obtener información acerca de bases de datos locales y tablas habilitadas para la base de datos de estirar
Abra el catálogo vistas **sys.databases** y **sys.tables** para ver información sobre estirar\-habilitado tablas y bases de datos de SQL Server. Para obtener más información, vea [sys.databases (Transact-SQL)](https://msdn.microsoft.com/library/ms178534.aspx) y [sys.tables (Transact-SQL)](https://msdn.microsoft.com/library/ms187406.aspx).

Para ver cuánto espacio un estiramiento\-usa tabla habilitada en SQL Server, ejecute la instrucción siguiente.

 ```tsql
USE <Stretch-enabled database name>;
GO
EXEC sp_spaceused '<Stretch-enabled table name>', 'true', 'LOCAL_ONLY';
GO
 ```
## <a name="manage-data-migration"></a>Administrar la migración de datos

### <a name="check-the-filter-function-applied-to-a-table"></a>Compruebe la función de filtro aplicada a una tabla
Abrir la vista de catálogo **sys.remote\_datos\_archivo\_tablas** y compruebe el valor de la **filtro\_predicado** columna para identificar la función que está utilizando la base de datos de estirar para seleccionar filas para migrar. Si el valor es null, toda la tabla es elegible para migrar. Para obtener más información, vea [sys.remote_data_archive_tables (Transact-SQL)](https://msdn.microsoft.com/library/dn935003.aspx) y [Seleccione las filas para migrar mediante una función de filtro](sql-server-stretch-database-predicate-function.md).

### <a name="Migration"></a>Comprobar el estado de la migración de datos
Seleccione tareas **| Estirar | Monitor** para una base de datos de SQL Server Management Studio para supervisar la migración de datos en el Monitor de la base de datos de estirar. Para obtener más información, vea [Monitor y solucionar problemas de migración de datos (base de datos de estirar)](sql-server-stretch-database-monitor.md).

O bien, abra la vista de administración dinámica **sys.dm\_db\_rda\_migración\_estado** para ver el número de secciones y filas de datos se han migrado.

### <a name="Firewall"></a>Solucionar problemas de migración de datos
Para sugerencias de solución de problemas, consulte [Monitor y solucionar problemas de migración de datos (base de datos de estirar)](sql-server-stretch-database-monitor.md).

## <a name="manage-remote-data"></a>Administrar datos remotos

### <a name="RemoteInfo"></a>Obtener información acerca de las bases de datos remotas y utilizados estiramiento de base de datos de tablas
Abrir las vistas de catálogo **sys.remote\_datos\_archivo\_bases de datos** y **sys.remote\_datos\_archivo\_tablas** para ver información sobre las tablas y las bases de datos remotas en que se almacenan los datos migrados. Para obtener más información, vea [sys.remote_data_archive_databases (Transact-SQL)](https://msdn.microsoft.com/library/dn934995.aspx) y [sys.remote_data_archive_tables (Transact-SQL)](https://msdn.microsoft.com/library/dn935003.aspx).

Para ver cuánto espacio está utilizando una tabla habilitado estirar en Azure, ejecute la instrucción siguiente.

 ```tsql
USE <Stretch-enabled database name>;
GO
EXEC sp_spaceused '<Stretch-enabled table name>', 'true', 'REMOTE_ONLY';
GO
 ```

### <a name="delete-migrated-data"></a>Eliminar datos migrados  
Si desea eliminar los datos que ya se han migrado a Azure, siga los pasos descritos en [sys.sp_rda_reconcile_batch](https://msdn.microsoft.com/library/mt707768.aspx).

## <a name="manage-table-schema"></a>Administrar el esquema de la tabla

### <a name="dont-change-the-schema-of-the-remote-table"></a>No cambia el esquema de la tabla remota
No cambia el esquema de una tabla remota de Azure que está asociada a una tabla de SQL Server configurada para expandir base de datos. En particular, no modifique el nombre o el tipo de datos de una columna. La característica de base de datos de estirar hace distintos supuestos sobre el esquema de la tabla remota en relación con el esquema de la tabla de SQL Server. Si cambia el esquema remoto, base de datos de estirar deja de funcionar para la tabla modificada.

### <a name="reconcile-table-columns"></a>Conciliar columnas de tabla  
Si ha eliminado accidentalmente columnas de la tabla remota, ejecute **sp_rda_reconcile_columns** para agregar columnas a la tabla remota que existen en el estiramiento\-habilitada la tabla de SQL Server, pero no en la tabla remota. Para obtener más información, consulte [sys.sp_rda_reconcile_columns](https://msdn.microsoft.com/library/mt707765.aspx).  

  > [!IMPORTANT] **Sp_rda_reconcile_columns** vuelve a crear columnas que accidentalmente eliminado de la tabla remota, no restaura los datos que anteriormente estaba en las columnas eliminadas.

**sp_rda_reconcile_columns** no eliminar columnas de la tabla remota que existen en la tabla remota pero no en el estiramiento\-habilitada la tabla de SQL Server. Si hay columnas de la tabla de Azure remota que ya no existen en el estiramiento\-habilitado para SQL Server tabla, estas columnas adicionales no impiden base de datos de estirar funcionando normalmente. Opcionalmente, puede quitar las columnas adicionales manualmente.  

## <a name="manage-performance-and-costs"></a>Administrar costos y rendimiento  

### <a name="troubleshoot-query-performance"></a>Solucionar problemas de rendimiento de la consulta
Las consultas que incluyen estirar\-tablas habilitadas deben realizar más despacio de antes de que las tablas se han habilitado para estirar. Si el rendimiento de consulta disminuye significativamente, revise los posibles problemas siguientes.

-   ¿Es el servidor de Azure en una región geográfica distinta de SQL Server? Configurar el servidor de Azure para estar en la misma región geográfica como SQL Server para reducir la latencia de red.

-   Las condiciones de red pueden haberse reducido. Para obtener información sobre problemas recientes o interrupciones, póngase en contacto con el Administrador de red.

### <a name="increase-azure-performance-level-for-resource-intensive-operations-such-as-indexing"></a>Aumentar el nivel de rendimiento de Azure para recursos\-intensivas operaciones, como la indización
Al generar, volver a generar o reorganizar un índice en una tabla grande que está configurada para la base de datos de Estirar y anticipar pesado consultar los datos migrados en Azure durante este período, considere la posibilidad de aumentar el nivel de rendimiento de la base de Azure remoto correspondiente para la duración de la operación. Para obtener más información sobre los niveles de rendimiento y precios, consulte [Precios de base de datos de SQL Server estirar](https://azure.microsoft.com/pricing/details/sql-server-stretch-database/).

### <a name="you-cant-pause-the-sql-server-stretch-database-service-on-azure"></a>No se puede pausar el servicio de base de datos de SQL Server estirar en Azure  
 Asegúrese de seleccionar el rendimiento correspondiente y el nivel de precios. Si aumentar el nivel de rendimiento temporalmente para un recurso\-operación intensiva, restaurar al nivel anterior después de que finalice la operación. Para obtener más información sobre los niveles de rendimiento y precios, consulte [Precios de base de datos de SQL Server estirar](https://azure.microsoft.com/pricing/details/sql-server-stretch-database/).  

## <a name="change-the-scope-of-queries"></a>Cambiar el ámbito de las consultas  
 Consultas en estirar\-tablas habilitadas devuelven datos local y remota de forma predeterminada. Puede cambiar el ámbito de las consultas para todas las consultas de todos los usuarios o solo para una sola consulta por un administrador.  

### <a name="change-the-scope-of-queries-for-all-queries-by-all-users"></a>Cambiar el ámbito de las consultas para todas las consultas por todos los usuarios  
 Para cambiar el ámbito de todas las consultas de todos los usuarios, ejecute el procedimiento almacenado **sys.sp_rda_set_query_mode**. Puede reducir el ámbito para consultar únicamente datos locales, deshabilitar todas las consultas o restaurar la configuración predeterminada. Para obtener más información, consulte [sys.sp_rda_set_query_mode](https://msdn.microsoft.com/library/mt703715.aspx).  

### <a name="queryHints"></a>Cambiar el ámbito de las consultas para una sola consulta por un administrador  
 Para cambiar el ámbito de una sola consulta por un miembro de la función db_owner, agregue el * *con \( REMOTE_DATA_ARCHIVE_OVERRIDE = *valor* \)** sugerencia de consulta a la instrucción SELECT. La sugerencia de consulta REMOTE_DATA_ARCHIVE_OVERRIDE puede tener los valores siguientes.  
 -   **LOCAL_ONLY**. Únicamente datos locales de la consulta.  

 -   **REMOTE_ONLY**. Solo los datos remotos de la consulta.  

 -   **STAGE_ONLY**. Solo los datos de la tabla donde etapas de base de datos de expandir filas elegible para la migración y conserva filas migrados durante el período especificado después de la migración de la consulta. Esta sugerencia de consulta es la única manera de la tabla provisional de la consulta.  

Por ejemplo, la siguiente consulta devuelve resultados locales solo.  

 ```tsql  
 USE <Stretch-enabled database name>;
 GO
 SELECT * FROM <Stretch_enabled table name> WITH (REMOTE_DATA_ARCHIVE_OVERRIDE = LOCAL_ONLY) WHERE ... ;
 GO
```  

## <a name="adminHints"></a>Hacer que las eliminaciones y actualizaciones administrativas  
 Por predeterminado no se puede actualizar o eliminar filas que son elegibles para la migración o filas que ya se han migrado, en un estiramiento\-habilitada la tabla. Cuando haya que solucionar un problema, un miembro de la función db_owner puede ejecutar una operación de actualización o eliminación agregando el * *con \( REMOTE_DATA_ARCHIVE_OVERRIDE = *valor* \)** sugerencia de consulta a la declaración. La sugerencia de consulta REMOTE_DATA_ARCHIVE_OVERRIDE puede tener los valores siguientes.  
 -   **LOCAL_ONLY**. Actualizar o eliminar únicamente datos locales.  

 -   **REMOTE_ONLY**. Actualizar o eliminar datos remotos solo.  

 -   **STAGE_ONLY**. Actualizar o eliminar solo los datos de la tabla donde etapas de base de datos de expandir filas elegible para la migración y conserva filas migrados durante el período especificado después de la migración.  

## <a name="see-also"></a>Vea también

[Base de datos extendido del Monitor](sql-server-stretch-database-monitor.md)

[Copia de seguridad habilitados para expandir las bases de datos](sql-server-stretch-database-backup.md)

[Restaurar bases de datos habilitado estirar](sql-server-stretch-database-restore.md)
