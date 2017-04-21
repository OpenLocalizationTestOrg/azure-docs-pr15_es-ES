<properties
   pageTitle="Recuperación de base de datos SQL | Microsoft Azure"
   description="Obtenga información sobre cómo recuperar una base de datos de un error con las capacidades de restauración Geo y Azure base de datos activa Geo replicación de SQL o interrupciones del centro de datos regional."
   services="sql-database"
   documentationCenter=""
   authors="CarlRabeler"
   manager="jhubbard"
   editor="monicar"/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="10/13/2016"
   ms.author="carlrab"/>

# <a name="restore-an-azure-sql-database-or-failover-to-a-secondary"></a>Restaurar una base de datos de SQL Azure o una migración tras error a un secundario

Base de datos de SQL Azure ofrece las siguientes capacidades de recuperación de una interrupción:

- [Replicación de Geo Active](sql-database-geo-replication-overview.md)
- [Restaurar geo](sql-database-recovery-using-backups.md#point-in-time-restore)

Para obtener información sobre escenarios de continuidad empresarial y las características de compatibilidad con estos escenarios, vea [continuidad empresarial](sql-database-business-continuity.md).

### <a name="prepare-for-the-event-of-an-outage"></a>Prepararse para el caso de una interrupción

Para tener éxito con recuperación para otra región de datos con la replicación de Geo Active o copias de seguridad geo redundantes, necesita preparar un servidor en el centro de datos de otra interrupción puede convertirse en el nuevo servidor principal debe la necesidad de surgir, así como también se ha definido pasos documentado y probado para garantizar la recuperación suave. Incluyen estos pasos de preparación:

- Identificar el servidor lógico en otra región para convertirse en el nuevo servidor principal. Con Active Geo replicación, será al menos uno y tal vez cada uno de los servidores secundarios. Para restaurar de Geo, esto suele ser un servidor de la [región pareja](../best-practices-availability-paired-regions.md) para la región en la que se encuentra la base de datos.
- Identificar y definir opcionalmente, las reglas de firewall de nivel de servidor necesarias en los usuarios acceso a la nueva base de datos principal.
- Determinar cómo se van a redirigir a los usuarios al nuevo servidor principal, como cambiar cadenas de conexión o cambiando las entradas DNS.
- Identificar y, opcionalmente, cree, los inicios de sesión que deben estar presente en la base de datos en el nuevo servidor principal y compruebe que estos inicios de sesión tienen los permisos adecuados en la base de datos, si hay alguna. Para obtener más información, vea [seguridad de base de datos SQL después de recuperación](sql-database-geo-replication-security-config.md)
- Identificar las reglas de alertas que se deben actualizar para asignar a la nueva base de datos principal.
- La configuración de auditoría en la base de datos principal de documentos
- Realizar una [recuperación de detalle](sql-database-disaster-recovery-drills.md). Para simular una interrupción para restaurar Geo, puede eliminar o cambiar el nombre de la base de datos de origen para que se produzca el error de conectividad de la aplicación. Para simular una interrupción de Active Geo replicación, puede deshabilitar la aplicación web o una máquina virtual conectado a la base de datos o migración tras error de la base de datos para que se produzcan errores de aplicación connectity.

## <a name="when-to-initiate-recovery"></a>Cuándo iniciar la recuperación

La operación de recuperación afecta a la aplicación. Se requiere cambiar la cadena de conexión de SQL o redirección con DNS y podría provocar la pérdida permanente de datos. Por lo tanto, debe hacerse solo cuando la interrupción es probable que durar más de objetivo de tiempo de recuperación de la aplicación. Cuando se implementa la aplicación en producción debe realizar la supervisión regular de mantenimiento de la aplicación y utilizar los siguientes puntos de datos declarar que está justificada la recuperación:

1.  Error de conectividad permanente del nivel de aplicación de la base de datos.
2.  El portal de Azure muestra una alerta sobre un incidente de la región con gran impacto.
3.  El servidor de base de datos de SQL Azure está marcado como degradado.

Dependiendo de su tolerancia de la aplicación para el tiempo de inactividad y posibles responsabilidad de empresa puede tener en cuenta las siguientes opciones de recuperación.

Utilice la [Base de datos recuperable obtener](https://msdn.microsoft.com/library/dn800985.aspx) (*LastAvailableBackupDate*) para obtener el punto de restauración replicar Geo más reciente.

## <a name="wait-for-service-recovery"></a>Espere a que la recuperación de servicio

El trabajo de equipos de Azure diligentemente para restaurar la disponibilidad del servicio rápidamente como sea posible, pero según la raíz provocar puede tomar horas o días.  Si la aplicación puede tolerar simplemente puede esperar a que se complete la recuperación de tiempo de inactividad considerable. En este caso, se requiere ninguna acción por su parte. Puede ver el estado del servicio actual en el [Panel de estado del servicio de Azure](https://azure.microsoft.com/status/). Después de la recuperación de la región se restauran la disponibilidad de la aplicación.

## <a name="failover-to-geo-replicated-secondary-database"></a>Migración tras error para replicar geo base de datos secundaria

Si el tiempo de inactividad de la aplicación puede dar lugar a responsabilidad de empresa debe estar usando replicar geo bases de datos en la aplicación. Permitirá la aplicación restaurar rápidamente la disponibilidad en una región diferente en caso de una interrupción. Obtenga información sobre cómo [Configurar la replicación de Geo](sql-database-geo-replication-portal.md).

Para restaurar la disponibilidad de las bases de datos que necesita para iniciar la migración tras error a la secundaria replicar geo usando uno de los métodos admitidos.

Utilice una de las siguientes guías para conmutar por error a una base de datos secundaria replicar geo:

- [Migración tras error a un secundario replicar geo con el Portal de Azure](sql-database-geo-replication-portal.md)
- [Migración tras error a un secundario replicar geo con PowerShell](sql-database-geo-replication-powershell.md)
- [Migración tras error a un secundario replicar geo mediante T-SQL](sql-database-geo-replication-transact-sql.md)

## <a name="recover-using-geo-restore"></a>Recuperar utilizando Geo restauración

Si el tiempo de inactividad de la aplicación no da como resultado responsabilidad de empresa puede usar Geo restaurar como un método para recuperar las bases de datos de la aplicación. Crea una copia de la base de datos desde su última copia de seguridad geo redundantes.

Utilice una de las siguientes guías geo restaurar una base de datos en una región nueva:

- [Geo-restaurar una base de datos a una nueva área con el Portal de Azure](sql-database-geo-restore-portal.md)
- [Geo-restaurar una base de datos a una nueva área con PowerShell](sql-database-geo-restore-powershell.md)

## <a name="configure-your-database-after-recovery"></a>Configurar la base de datos después de la recuperación

Si está utilizando replicación geo migración tras error o restaurar geo recuperarse de una interrupción, debe asegurarse de que la conectividad con las bases de datos está correctamente configurada para que se puede reanudar la función de aplicación normal. Se trata de una lista de comprobación de tareas para preparar su producción recuperados de la base de datos.

### <a name="update-connection-strings"></a>Actualizar las cadenas de conexión

Porque la base de datos recuperado residen en un servidor distinto, deberá actualizar la cadena de conexión de la aplicación para que apunten a dicho servidor.

Para obtener más información acerca de cómo cambiar las cadenas de conexión, vea el lenguaje de programación adecuado para la [biblioteca de conexión](sql-database-libraries.md).

### <a name="configure-firewall-rules"></a>Configurar reglas de Firewall

Debe asegurarse de que las reglas de firewall configuradas en servidor y en la base de datos coinciden con los que se han configurado en el servidor principal y la base de datos principal. Para obtener más información, vea [Cómo: configurar configuraciones de Firewall (base de datos de SQL Azure)](sql-database-configure-firewall-settings.md).


### <a name="configure-logins-and-database-users"></a>Configurar inicios de sesión y los usuarios de la base de datos

Debe asegurarse de que todas las conexiones que utiliza la aplicación existen en el servidor que hospeda la base de datos recuperado. Para obtener más información, vea [Configuración de seguridad para la replicación de Geo](sql-database-geo-replication-security-config.md).

>[AZURE.NOTE] Debe configurar y probar las reglas de firewall del servidor e inicios de sesión (y sus permisos) durante un ejercicio de recuperación de desastres. Estos objetos de nivel de servidor y su configuración no esté disponibles durante la interrupción.

### <a name="setup-telemetry-alerts"></a>Configurar alertas de telemetría

Debe asegurarse de que la configuración de regla de alerta existente se actualiza para asignar a la base de datos recuperado y el otro servidor.

Para obtener más información acerca de las reglas de alerta de base de datos, vea [Recibir notificaciones de alerta](../monitoring-and-diagnostics/insights-receive-alert-notifications.md) y [El estado del servicio de control](../monitoring-and-diagnostics/insights-service-health.md).

### <a name="enable-auditing"></a>Habilitar la auditoría

Si se necesita auditoría para tener acceso a la base de datos, debe habilitar la auditoría después de la recuperación de la base de datos. Un buen indicador que no se requiere la auditoría es que las aplicaciones cliente utilizan cadenas de conexión segura en un patrón de *. database.secure.windows.net. Para obtener más información, vea [Introducción a la auditoría de la base de datos SQL](sql-database-auditing-get-started.md).


## <a name="next-steps"></a>Pasos siguientes

- Para obtener información sobre las copias de seguridad automatizadas de base de datos SQL Azure, consulte [copias de seguridad de automatizado de base de datos SQL](sql-database-automated-backups.md)
- Para obtener información sobre escenarios de diseño y la recuperación de continuidad empresarial, vea [escenarios de continuidad](sql-database-business-continuity.md)
- Para obtener información sobre el uso de copias de seguridad automatizadas de recuperación, consulte [restaurar una base de datos de las copias de seguridad iniciado por el servicio](sql-database-recovery-using-backups.md)
