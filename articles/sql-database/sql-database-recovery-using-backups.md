<properties
   pageTitle="Continuidad empresarial de nube: restaurar una base de datos eliminado - base de datos SQL | Microsoft Azure"
   description="Obtenga información sobre la restauración de punto en el tiempo, que le permite restaurar una base de datos de SQL Azure a un punto anterior en el tiempo (hasta 35 días)."
   services="sql-database"
   documentationCenter=""
   authors="stevestein"
   manager="jhubbard"
   editor="monicar"/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="08/01/2016"
   ms.author="sstein"/>

# <a name="recover-an-azure-sql-database-using-automated-database-backups"></a>Recuperar una base de datos de SQL Azure con copias de seguridad de base de datos automática

Base de datos SQL ofrece tres opciones de recuperación de base de datos con [que copias de seguridad de automatizado de base de datos de SQL](sql-database-automated-backups.md). Puede restaurar una base de datos de las copias de seguridad iniciado por el servicio durante el [período de retención](sql-database-service-tiers.md) para:

- Una nueva base de datos en el mismo servidor lógico recuperado a un punto específico en el tiempo dentro de un período de retención. 
- Una base de datos en el mismo servidor lógico recuperado hasta el momento de la eliminación de una base de datos eliminado.
- Una nueva base de datos en cualquier servidor lógico en cualquier región recuperado para las copias de seguridad diarias más recientes en el almacenamiento de blobs replicar geo (RA GRS).

También puede usar la [base de datos SQL había automatizado copias de seguridad](sql-database-automated-backups.md) para crear una [copia de la base de datos](sql-database-copy.md) en cualquier servidor lógico en cualquier área de coherente con la base de datos SQL. Puede utilizar copiar de la base de datos y [Exportar a un MOCHILA](sql-database-export.md) para archivar una copia de una base de datos de almacenamiento a largo plazo más allá de su período de retención coherente, o para transferir una copia de la base de datos a la implementación local o Azure VM instancia de SQL Server.

## <a name="recovery-time"></a>Tiempo de recuperación

El tiempo de recuperación para restaurar una base de datos con copias de seguridad automatizadas de la base de datos se ve afectado por varios factores: 
 - El tamaño de la base de datos
 - El nivel de rendimiento de la base de datos
 - El número de registros de transacciones implicados
 - La cantidad de actividad que debe reproducirse para recuperar al punto de restauración
 - El ancho de banda de red si la restauración es una región diferente 
 - El número de solicitudes simultáneas restaurar que se procesan en la región de destino. 
 
 Para una base de datos muy grande o active la restauración puede tardar unas horas. Si hay interrupción de actividad prolongado en una región, es posible que se gran cantidad de solicitudes de restauración Geo procesadas otras regiones. Si hay una gran cantidad de solicitudes de que esto puede aumentar el tiempo de recuperación de bases de datos de esa región. La mayoría de base de datos se restaura completado dentro de 12 horas.

 No hay ninguna funcionalidad integrada para restaurar de forma masiva. La [base de datos de SQL Azure: recuperación de servidor completa](https://gallery.technet.microsoft.com/Azure-SQL-Database-Full-82941666) secuencia de comandos es un ejemplo de una forma de realizar esta tarea.

> [AZURE.IMPORTANT] Para recuperar las copias de seguridad automatizadas, debe ser miembro del rol de colaborador de SQL Server en la suscripción o ser el propietario de la suscripción. Se puede recuperar con el portal de Azure, PowerShell o la API de REST. No puede usar Transact-SQL. 

## <a name="point-in-time-restore"></a>Restauración de punto en el tiempo

Restaurar un momento le permite restaurar una base de datos existente como una nueva base de datos en un momento anterior en el tiempo en el mismo servidor lógico con la [que base de datos SQL automatizado copias de seguridad](sql-database-automated-backups.md). No se puede sobrescribir la base de datos existente. Puede restaurar a un momento anterior en el tiempo con el [portal de Azure](sql-database-point-in-time-restore-portal.md), [PowerShell](sql-database-point-in-time-restore-powershell.md) o la [API de REST](https://msdn.microsoft.com/library/azure/mt163685.aspx).

> [AZURE.SELECTOR]
- [Restauración de punto en el tiempo: Portal de Azure](sql-database-point-in-time-restore-portal.md)
- [Restaurar un momento: PowerShell](sql-database-point-in-time-restore-powershell.md)

Se puede restaurar la base de datos en cualquier nivel de rendimiento o elástica grupo. Debe asegurarse de que tiene suficiente cuota DTU en el servidor lógico o grupo elástico. Tenga en cuenta que la restauración crea una nueva base de datos y que el nivel de rendimiento y los niveles de servicio de la base de datos puede ser distinto del estado actual de la base de datos activa. Cuando haya finalizado, la base de datos es una normal totalmente accesible en línea base de datos cobra a tasas normales según su nivel de rendimiento y los niveles de servicio. No se produce cargos hasta que se complete la restauración de la base de datos.

Generalmente se restaura una base de datos en un punto de earler fines de recuperación. Al hacerlo, puede considerar la base de datos como un reemplazo para la base de datos original o usar para recuperar datos de y, a continuación, actualizar la base de datos original. 

- ***Reemplazo de la base de datos:*** Si la base de datos está pensado como reemplazo de la base de datos original, debe comprobar el nivel de rendimiento o nivel de servicio son las más apropiada y ajustar el tamaño de la base de datos si es necesario. Puede cambiar el nombre de la base de datos original y asígnele el nombre original con el comando de modificar la base de datos de SQL de T a la base de datos. 
- ***Recuperación de datos:*** Si tiene previsto recuperar datos de la base de datos para recuperar de un error de usuario o la aplicación, debe por separado escribir y ejecutar las secuencias de comandos de recuperación de datos necesita extraer datos de la base de datos a la base de datos original. Aunque la operación de restauración puede tardar mucho tiempo en completarse, la base de datos de restauración será visible en la lista de la base de datos a lo largo. Si elimina la base de datos durante la restauración, cancelará la operación y no se cobrará para la base de datos que no se ha completado la restauración. 

Para obtener información detallada sobre el uso de restauración de punto en el tiempo para recuperar de los errores de usuario y la aplicación, vea restaurar [en un momento](sql-database-recovery-using-backups.md#point-in-time-restore)

## <a name="deleted-database-restore"></a>Restaurar base de datos eliminada

Restaurar base de datos eliminada le permite restaurar una base de datos eliminado a la hora de eliminación de una base de datos eliminado en el mismo servidor lógico con la [que base de datos SQL automatizado copias de seguridad](sql-database-automated-backups.md). 

> [AZURE.IMPORTANT] Si elimina una instancia del servidor de base de datos de SQL Azure, todos sus bases de datos también se eliminan y no se pueden recuperar. No hay ninguna compatibilidad para restaurar un servidor eliminado en este momento.

Puede usar el mismo o a un nuevo nombre de base de datos para la base de datos. Puede usar el [portal de Azure](sql-database-restore-deleted-database-portal.md), [PowerShell](sql-database-restore-deleted-database-powershell.md) o la [resto (createMode = restaurar)](https://msdn.microsoft.com/library/azure/mt163685.aspx). 

> [AZURE.SELECTOR]
- [Eliminar la restauración de la base de datos: portal de Azure](sql-database-restore-deleted-database-portal.md)
- [Eliminar la restauración de la base de datos: PowerShell](sql-database-restore-deleted-database-powershell.md)

## <a name="geo-restore"></a>Restaurar geo

Restaurar geo le permite restaurar una base de datos SQL en cualquier servidor de cualquier región Azure del más reciente replicar geo [automatizado de copia de seguridad diaria](sql-database-automated-backups.md). Restaurar geo utiliza una copia de seguridad geo redundantes como origen y se puede usar para recuperar una base de datos, incluso si la base de datos o el centro de datos está inaccesible debido a un corte. Puede usar el [portal de Azure](sql-database-geo-restore-portal.md), [PowerShell](sql-database-geo-restore-powershell.md), o la [resto (createMode = recuperación)](https://msdn.microsoft.com/library/azure/mt163685.aspx) 

> [AZURE.SELECTOR]
- [Geo-restaurar: Portal de Azure](sql-database-geo-restore-portal.md)
- [Restaurar geo: PowerShell](sql-database-geo-restore-powershell.md)

Restaurar geo es la opción de recuperación de forma predeterminada cuando la base de datos no está disponible debido a un incidente de la región donde se encuentra la base de datos. Si un incidente de gran escala en los resultados de una región de disponibilidad de la aplicación de la base de datos, puede usar Geo restaurar para restaurar una base de datos de la copia de seguridad más reciente en un servidor en cualquier otra región. Todas las copias de seguridad son replicar geo y puede tener un retardo cuando la copia de seguridad se tomadas y replicar geo a la Azure blob en una región diferente. Este retraso puede tener hasta una hora para un desastre puede haber una pérdida de datos de 1 hora, es decir, RPO de hasta 1 hora. A continuación muestra la restauración de la base de datos desde la última copia de seguridad diaria.

![restaurar geo](./media/sql-database-geo-restore/geo-restore-2.png)

Para obtener información detallada sobre el uso de Geo restaurar para recuperar de una interrupción, vea [recuperar de una interrupción](sql-database-disaster-recovery.md)

> [AZURE.IMPORTANT] Mientras Geo restaurar está disponible con todos los niveles de servicio, es más básico de las soluciones de recuperación de desastres disponibles en la base de datos de SQL con el RPO más larga y el tiempo de recuperación de estimación (Insertar). Para bases de datos básicos con tamaño máximo de 2 GB Geo-restaurar proporciona una solución de DR razonable con un insertar de 12 horas. Estándar o Premium bases de datos grandes si significativamente se desean tiempos de recuperación, o para reducir la probabilidad de pérdida de datos puede utilizar la replicación de Geo Active. Replicación de Geo Active ofrece un menor RPO y convertir como sólo requiere que inicie una migración tras error a un secundario continuamente replicada. Para obtener más información, vea [La replicación de Geo Active](sql-database-geo-replication-overview.md).

## <a name="programmatically-performing-recovery-using-automated-backups"></a>Recuperación mediante programación utilizando copias de seguridad automatizadas

Como se indicó anteriormente, en addiition el portal de Azure, recuperación de base de datos puede realizarse mediante programación utilizando PowerShell de Azure y la API de REST. Las tablas siguientes describen el conjunto de comandos disponibles.

### <a name="powershell"></a>PowerShell

|Cmdlet|Descripción|
|------|-----------|
|[Get-AzureRmSqlDatabase](https://msdn.microsoft.com/en-us/library/azure/mt603648.aspx)|Obtiene una o más bases de datos.|
|[Get-AzureRMSqlDeletedDatabaseBackup](https://msdn.microsoft.com/en-us/library/azure/mt693387.aspx)|Obtiene una base de datos eliminado que puede restaurar.|
|[Get-AzureRmSqlDatabaseGeoBackup](https://msdn.microsoft.com/library/azure/mt693388.aspx)|Obtiene una copia de seguridad geo redundantes de una base de datos.|
|[Restaurar AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt693390.aspx)|Restaura una base de datos SQL.|
||||

### <a name="rest-api"></a>API DE REST

|API|Descripción|
|---|-----------|
|[RESTO (createMode = recuperación)](https://msdn.microsoft.com/library/azure/mt163685.aspx)|Restaura una base de datos|
|[Empezar a crear o actualizar el estado de la base de datos](https://msdn.microsoft.com/library/azure/mt643934.aspx)|Devuelve el estado durante una operación de restauración|
||||



## <a name="summary"></a>Resumen

Copias de seguridad automáticas protección las bases de datos de usuario y errores de aplicación, eliminación de base de datos accidental y prolongadas interrupciones. Esta solución de administración de cero de costo cero está disponible con todas las bases de datos SQL. 

## <a name="next-steps"></a>Pasos siguientes

- Para obtener una descripción de la continuidad empresarial y escenarios, vea [información general de continuidad empresarial](sql-database-business-continuity.md)
- Para obtener información sobre las copias de seguridad automatizadas de base de datos SQL Azure, consulte [copias de seguridad de automatizado de base de datos SQL](sql-database-automated-backups.md)
- Para obtener información sobre las opciones de recuperación más rápidas, vea [Replicación de Geo activo](sql-database-geo-replication-overview.md)  
- Para obtener información sobre el uso de copias de seguridad automatizadas de archivado, vea [Copiar de base de datos](sql-database-copy.md)
