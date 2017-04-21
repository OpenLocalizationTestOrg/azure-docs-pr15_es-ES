<properties
    pageTitle="Active Geo replicación de base de datos SQL Azure"
    description="Replicación de Geo Active le permite configurar 4 réplicas de la base de datos en cualquiera de los centros de datos de Azure."
    services="sql-database"
    documentationCenter="na"
    authors="stevestein"
    manager="jhubbard"
    editor="monicar" />


<tags
    ms.service="sql-database"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="NA"
    ms.date="09/26/2016"
    ms.author="sstein" />

# <a name="overview-sql-database-active-geo-replication"></a>Información general: Base de datos activa Geo replicación de SQL

Replicación de Geo Active le permite configurar un máximo de cuatro legibles secundarios bases de datos en las ubicaciones de centro de datos de la misma u otra (regiones). Bases de datos secundarias están disponibles para las consultas y para la migración tras error en el caso de una interrupción de centro de datos o el no para conectarse a la base de datos principal.

>[AZURE.NOTE] Replicación de Geo Active (secundarios legibles) ahora está disponible para todas las bases de datos en todos los niveles de servicio. En abril de 2017, se retirará el tipo secundario no se puede leer y bases de datos no se puede leer existentes se actualizarán automáticamente a secundarios legibles.

 Puede configurar replicación Geo activa con el [portal de Azure](sql-database-geo-replication-portal.md), [PowerShell](sql-database-geo-replication-powershell.md), [Transact-SQL](sql-database-geo-replication-transact-sql.md), o la [API de REST: crear o actualizar base de datos](https://msdn.microsoft.com/library/azure/mt163685.aspx).

> [AZURE.SELECTOR]
- [Configurar: Portal de Azure](sql-database-geo-replication-portal.md)
- [Configurar: PowerShell](sql-database-geo-replication-powershell.md)
- [Configurar: T-SQL](sql-database-geo-replication-transact-sql.md)

If para cualquier motivo de la base de datos principal de falla o simplemente debe realizarse sin conexión, puede *migración tras error* a cualquiera de las bases de datos secundarios. Cuando se activa la migración tras error a una de las bases de datos secundarios, todos los otros secundarios se vinculan automáticamente para el nuevo principal.

Puede que la migración tras error a un secundario con el [portal de Azure](sql-database-geo-replication-failover-portal.md), [PowerShell](sql-database-geo-replication-failover-powershell.md), [Transact-SQL](sql-database-geo-replication-failover-transact-sql.md), la [API de REST - planeado de migración tras error](https://msdn.microsoft.com/ibrary/azure/mt575007.aspx)o [API de REST - planificado migración tras error](https://msdn.microsoft.com/library/azure/mt582027.aspx).


> [AZURE.SELECTOR]
- [Migración tras error: Portal de Azure](sql-database-geo-replication-failover-portal.md)
- [Migración tras error: PowerShell](sql-database-geo-replication-failover-powershell.md)
- [Migración tras error: T-SQL](sql-database-geo-replication-failover-transact-sql.md)

Después de la migración, asegúrese de que se han configurado los requisitos de autenticación de su servidor y la base de datos en el nuevo principal. Para obtener más información, vea [seguridad de base de datos SQL después de recuperación](sql-database-geo-replication-security-config.md).


La característica de replicación de Geo Active implementa un mecanismo para proporcionar redundancia de base de datos dentro de la misma región de Microsoft Azure o en diferentes regiones (geo redundancia). Replicación de Geo Active asincrónica aplica confirmada las transacciones de una base de datos en un máximo de cuatro copias de la base de datos en diferentes servidores, con aislamiento de instantánea confirmada (RCSI) para aislamiento de lectura. Cuando se configura la replicación de Geo Active, se crea una base de datos secundario en el servidor especificado. La base de datos original se convierte en la base de datos principal. La base de datos principal asincrónica aplica transacciones confirmadas a cada una de las bases de datos secundarios. Solo las transacciones completas se duplican. Mientras se encuentra en un momento dado, la base de datos secundario podría ser ligeramente detrás de la base de datos principal, se garantiza que los datos del secundario nunca tendrá transacciones parciales. Los datos específicos de RPO pueden encontrarse en la [Información general de continuidad empresarial](sql-database-business-continuity.md).

Una de las principales ventajas de Active Geo replicación es que proporciona una solución de recuperación de desastres de nivel de base de datos con el tiempo de recuperación baja. Cuando se coloca la base de datos secundario en un servidor en una región diferente, agregar máxima resistencia a la aplicación. Entre región redundancia permite que las aplicaciones recuperar de la pérdida permanente de un centro de datos completa o partes de un centro de datos causados por desastres naturales, errores humanos graves o malintencionadas. La figura siguiente muestra que un ejemplo de Active Geo replicación configurado con principal en la región Norte Central de EE y secundario en la región sur Central de EE.

![Relación de replicación geo](./media/sql-database-active-geo-replication/geo-replication-relationship.png)

Otra ventaja clave es que las bases de datos secundarios se pueden leer y pueden utilizarse para descargar las cargas de trabajo de solo lectura como trabajos de informes. Si solo desea usar la base de datos secundaria para equilibrio de carga, puede crear en la misma región como el principal. Crear un secundario de la misma región, no aumentar la resistencia de la aplicación a errores graves.  

Otros escenarios donde se puede utilizar la replicación de Geo Active incluyen:

- **Migración de base de datos**: puede usar la replicación de Geo Active para migrar una base de datos de un servidor a otro en línea con el tiempo de inactividad mínimo.
- **Actualizaciones de las aplicaciones**: puede crear un secundario adicional como un error al copiar atrás durante las actualizaciones de la aplicación.

Para lograr la continuidad empresarial real, agregar redundancia de base de datos entre los centros de datos es solo una parte de la solución. Recuperar una aplicación (servicio)-to-end después de un error grave requiere la recuperación de todos los componentes que constituyen el servicio y los servicios dependientes. Ejemplos de estos componentes incluyen el software de cliente (por ejemplo, un explorador con JavaScript personalizado), servidores front-end, almacenamiento y DNS. Es fundamental que todos los componentes son resistentes a los mismos errores y no estarán disponibles en el objetivo de tiempo de recuperación (RTO) de la aplicación. Por lo tanto, debe identificar todos los servicios dependientes y comprender las garantías y capacidades que proporcionan. A continuación, deberá realizar pasos adecuados para asegurarse de que funciona el servicio durante la migración tras error de los servicios en la que depende. Para obtener más información sobre cómo diseñar las soluciones de recuperación, vea [Diseñar soluciones de nube para desastres recuperación usando Active Geo replicación](sql-database-designing-cloud-solutions-for-disaster-recovery.md).

## <a name="active-geo-replication-capabilities"></a>Capacidades de replicación Geo activas
La característica de replicación de Geo Active proporciona las siguientes capacidades esenciales:

- **Replicación asincrónica automática**: solo puede crear una base de datos secundario mediante la adición de una base de datos existente. La secundaria solo puede crearse en un servidor de base de datos de SQL Azure diferente. Una vez creada, la base de datos secundario se rellena con los datos copiados de la base de datos principal. Este proceso se conoce como inicialización. Después de haber creado y visible base de datos secundaria, las actualizaciones de la base de datos principal se asincrónica replicar la base de datos secundario automáticamente. Replicación asincrónica significa que las transacciones se confirman en la base de datos principal antes de que duplicado de la base de datos secundario. 

- **Varias bases de datos secundarios**: dos o más bases de datos secundarios aumentan redundancia y nivel de protección de la base de datos principal y la aplicación. Si existen varias bases de datos secundarios, la aplicación queda protegida, incluso si se produce un error en una de las bases de datos secundarios. Si hay sólo una base de datos secundaria, y se produce un error, la aplicación se expone a riesgos mayores hasta que se crea una nueva base de datos secundario.

- **Bases de datos secundarios legibles**: una aplicación puede tener acceso a una base de datos secundaria para operaciones de sólo lectura con las identidades de seguridad de la misma u otra utilizadas para acceder a la base de datos principal. Trabajar con las bases de datos secundarios en modo de aislamiento de instantánea para asegurarse de que la replicación de las actualizaciones del equipo principal (reproducción del registro) no se retrasa mediante consultas ejecutadas en el secundario.

>[AZURE.NOTE] Si hay actualizaciones de esquema está recibiendo principal que requieren un bloqueo del esquema en la base de datos secundario, se retrasa la reproducción del registro en la base de datos secundario.

- **La replicación de Active geo de bases de datos de grupo elástico**: replicación de Geo Active puede configurarse para cualquier base de datos en cualquier grupo elásticos de la base de datos. La base de datos secundario puede estar en otro grupo de elásticos de la base de datos. Para bases de datos normales, el secundario puede ser una base de datos elástico agrupación y viceversa siempre y cuando los niveles de servicio son los mismos. 

- **Nivel de rendimiento configurable de la base de datos secundario**: una base de datos secundaria puede crearse con el nivel de rendimiento inferior a la principal. Bases de datos principales y secundarios deben tener el mismo nivel de servicio. Esta opción no se recomienda para las aplicaciones con operaciones de escritura de la base de datos alta porque la posposición de aumento de la replicación aumenta el riesgo de pérdida de datos importantes después de un error. Además, después de la migración rendimiento de la aplicación se ve afectado hasta que se actualice el nuevo principal a un nivel superior de rendimiento. El gráfico de porcentaje de IO de registro en el portal de Azure proporciona una buena manera de estimar el nivel de rendimiento mínimo de secundaria que es necesario para mantener la carga de replicación. Por ejemplo, si la base de datos principal es P6 (1000 DTU) y su registro porcentaje IO es el secundario debe tener al menos un 50% P4 (500 DTU). También puede recuperar los datos de IO de registro con vistas de base de datos de [sys.resource_stats](https://msdn.microsoft.com/library/dn269979.aspx) o [sys.dm_db_resource_stats]( https://msdn.microsoft.com/library/dn800981.aspx) .  Para obtener más información sobre los niveles de rendimiento de la base de datos de SQL, vea [rendimiento y opciones de la base de datos de SQL](sql-database-service-tiers.md). 

- **Y failback controlada por el usuario por**: una base de datos secundaria puede explícitamente cambiar la función principal en cualquier momento por el usuario o la aplicación. Durante una interrupción real la opción "no planeada" debe usarse, lo que promueve una secundaria a ser el principal. Cuando el error principal recupera y vuelve a estar disponible, el sistema automáticamente marca principal recuperado como secundario y actualizarla con el nuevo principal. Debido a la naturaleza asincrónica de replicación, una cantidad pequeña de datos se puede perder durante planificado migraciones tras error si se produce un error en el principal antes de que aplica a los cambios más recientes a la secundaria. Cuando realiza principal con varios secundarios, el sistema automáticamente vuelve a configurar las relaciones de replicación y vínculos los secundarios restantes a recién promovido principal sin necesidad de intervención del usuario. Después de la interrupción que provocó la migración tras error se soluciona, puede ser conveniente para devolver la aplicación a la región principal. Para ello, el comando de migración tras error se debe invocar con la opción "planeada". 

- **Mantener las credenciales y las reglas de firewall en sincronización**: se recomienda usar [reglas de firewall de la base de datos](sql-database-firewall-configure.md) para replicar geo bases de datos por lo tanto, estas reglas se pueden replicar con la base de datos para asegurarse de que todas las bases de datos secundarias tienen las mismas reglas de firewall como el principal. Este enfoque elimina la necesidad de los clientes configurar manualmente y mantener las reglas de firewall en servidores que alojan tanto las bases de datos principales y secundarios. Del mismo modo, utilizando el [contenido los usuarios de la base de datos](sql-database-manage-logins.md) para el acceso a datos garantiza primario y secundarios de bases de datos siempre tienen el mismo usuario credenciales durante un error, no hay interrupciones debido a errores de coincidencia con inicios de sesión y las contraseñas. Con la adición de [Azure Active Directory](../active-directory/active-directory-whatis.md), los clientes pueden administrar el acceso de usuario a bases de datos principales y secundarios y eliminar la necesidad de administrar las credenciales en por completo de las bases de datos.

## <a name="upgrading-or-downgrading-a-primary-database"></a>Actualizar o degradar una base de datos principal
Puede actualizar o degradar una base de datos principal de un nivel de rendimiento diferentes (dentro del mismo nivel de servicio) sin desconectar las bases de datos secundarios. Al actualizar, se recomienda que actualizar la base de datos secundaria en primer lugar y, a continuación, actualice el principal. Cuando cambio, invertir el orden: degradar principal en primer lugar y, a continuación, degradar el secundario. 

La base de datos secundaria debe estar en el mismo nivel de servicio como la principal para migrar la base de datos principal a un nivel de servicio diferente, debe terminar el vínculo de réplica geo y cambiar el nombre de la base de datos secundario o simplemente colóquelo. A continuación, migrar principal para el nuevo nivel de servicio y volver a configurar replicación geo. La nueva secundaria se crearán automáticamente con el mismo nivel de rendimiento como la principal de forma predeterminada.

## <a name="preventing-the-loss-of-critical-data"></a>Evitar la pérdida de datos críticos
Debido a la latencia alta de redes de área extensa, copiar continuo usa un mecanismo de replicación asincrónica. Replicación asincrónica hace inevitable perder datos si se produce un error. Sin embargo, algunas aplicaciones pueden requerir sin pérdida de datos. Para proteger estas actualizaciones críticas, un desarrollador de la aplicación puede llamar al procedimiento de sistema de [sp_wait_for_database_copy_sync](https://msdn.microsoft.com/library/dn467644.aspx) inmediatamente después de confirmar la transacción. Llamar a los bloques de **sp_wait_for_database_copy_sync** el subproceso de llamada hasta la última transacción confirmada ha sido replicar la base de datos secundario. El procedimiento esperará hasta que todas las transacciones en cola reconocidas por la base de datos secundario. **sp_wait_for_database_copy_sync** ámbito es un vínculo de una de las copias continuo. Cualquier usuario con los derechos de conexión para la base de datos principal puede llamar a este procedimiento.

>[AZURE.NOTE] El retraso causado por una llamada a procedimiento **sp_wait_for_database_copy_sync** puede ser importante. El retraso depende del tamaño de la longitud de registro de transacciones en el momento y esta llamada no devolverá hasta que se duplica el registro completo. Evite llamar a este procedimiento a menos que sea completamente necesario.

## <a name="programmatically-managing-active-geo-replication"></a>Administrar la replicación de Geo Active de mediante programación

Como se mencionó anteriormente, la replicación de Geo Active también puede administrarse mediante programación utilizando PowerShell de Azure y la API de REST. Las tablas siguientes describen el conjunto de comandos disponibles.

- **API del Administrador de recursos de Azure y seguridad basada en roles**: replicación de Geo Active incluye un conjunto de [API de administrador de recursos de Azure]( https://msdn.microsoft.com/library/azure/mt163571.aspx) para administración, incluidos los [cmdlets de PowerShell en función de administrador de recursos de Azure](sql-database-geo-replication-powershell.md). Estas API requieren el uso de grupos de recursos y soporte técnico de seguridad basado en roles (RBAC). Para obtener más información sobre cómo implementar el acceso a funciones vea [Control de acceso de Azure Role-Based](../active-directory/role-based-access-control-configure.md).

>[AZURE.NOTE] Muchas características nuevas de Active Geo replicación solo son compatibles con [El Administrador de recursos de Azure](../azure-resource-manager/resource-group-overview.md) basada en [API de REST de SQL Azure](https://msdn.microsoft.com/library/azure/mt163571.aspx) y [cmdlets de PowerShell de base de datos de SQL Azure](https://msdn.microsoft.com/library/azure/mt574084.aspx). La API de REST (clásico)] (https://msdn.microsoft.com/library/azure/dn505719.aspx) y [cmdlets de base de datos de SQL Azure (clásico)](https://msdn.microsoft.com/library/azure/dn546723.aspx) son compatibles para la compatibilidad para que se recomienda el uso de las API de administrador de recursos de Azure. 


### <a name="transact-sql"></a>Transact-SQL

|Comando|Descripción|
|-------|-----------|
|[MODIFICAR la base de datos (base de datos SQL Azure)](https://msdn.microsoft.com/library/mt574871.aspx)|Usar el argumento de agregar servidor secundario de en para crear una base de datos secundaria para una base de datos existente y se inicia la replicación de datos|
|[MODIFICAR la base de datos (base de datos SQL Azure)](https://msdn.microsoft.com/library/mt574871.aspx)|Usar la migración tras error o FORCE_FAILOVER_ALLOW_DATA_LOSS para cambiar de una base de datos secundaria para ser principal para iniciar la migración tras error
|[MODIFICAR la base de datos (base de datos SQL Azure)](https://msdn.microsoft.com/library/mt574871.aspx)|Usar quitar activado servidor secundario para terminar la replicación de datos entre una base de datos de SQL y la base de datos secundario especificado.|
|[Sys.geo_replication_links (base de datos de SQL Azure)](https://msdn.microsoft.com/library/mt575501.aspx)|Devuelve información acerca de todos los vínculos de réplica existentes para cada base de datos en el servidor de base de datos de SQL Azure lógico.|
|[Sys.dm_geo_replication_link_status (base de datos de SQL Azure)](https://msdn.microsoft.com/library/mt575504.aspx)|Obtiene la última vez replicación, retraso en la última replicación y otra información sobre el vínculo de réplica para una base de datos SQL dado.|
|[Sys.dm_operation_status (base de datos de SQL Azure)](https://msdn.microsoft.com/library/dn270022.aspx)|Muestra el estado de todas las operaciones de base de datos, incluido el estado de los vínculos de replicación.|
|[sp_wait_for_database_copy_sync (base de datos de SQL Azure)](https://msdn.microsoft.com/library/dn467644.aspx)|hace que la aplicación que esperar hasta que todas las transacciones confirmadas son replicar y reconocidas por la base de datos secundaria activa.|
||||

### <a name="powershell"></a>PowerShell

|Cmdlet|Descripción|
|------|-----------|
|[Get-AzureRmSqlDatabase](https://msdn.microsoft.com/en-us/library/azure/mt603648.aspx)|Obtiene una o más bases de datos.|
|[Nueva AzureRmSqlDatabaseSecondary](https://msdn.microsoft.com/library/mt603689.aspx)|Crea una base de datos secundaria para una base de datos existente y se inicia la replicación de datos.|
|[Establecer AzureRmSqlDatabaseSecondary](https://msdn.microsoft.com/en-us/library/mt619393.aspx)|Cambia una base de datos secundaria para ser principal para iniciar la migración tras error.|
|[Quitar AzureRmSqlDatabaseSecondary](https://msdn.microsoft.com/en-us/library/mt603457.aspx)|Finaliza la replicación de datos entre una base de datos de SQL y la base de datos secundario especificado.|
|[Get-AzureRmSqlDatabaseReplicationLink](https://msdn.microsoft.com/library/mt619330.aspx)|Obtiene los vínculos de replicación geo entre una base de datos de SQL Azure y un grupo de recursos o SQL Server.|
||||

### <a name="rest-api"></a>API DE REST

|API|Descripción|
|---|-----------|
|[Crear o actualizar base de datos (createMode = restaurar)](https://msdn.microsoft.com/library/azure/mt163685.aspx)|Crea, actualiza o se restaura un principal o una base de datos secundario.|
|[Empezar a crear o actualizar el estado de la base de datos](https://msdn.microsoft.com/library/azure/mt643934.aspx)|Devuelve el estado durante una operación de creación.|
|[Establecer la base de datos secundaria como principal (migración tras error planeada)](https://msdn.microsoft.com/ibrary/azure/mt575007.aspx)|Promover una base de datos secundario en una asociación de replicación Geo para convertirse en la nueva base de datos principal.|
|[Establecer la base de datos secundaria como principal (no planeado migración tras error)](https://msdn.microsoft.com/library/azure/mt582027.aspx)|Para forzar un error en la base de datos secundario y establezca la secundaria como el principal.|
|[Obtener vínculos de replicación](https://msdn.microsoft.com/library/azure/mt600929.aspx)|Obtiene todos los vínculos de replicación de una base de datos SQL dado en una asociación de replicación geo. Recupera la información visible en la vista de catálogo sys.geo_replication_links.|
|[Obtener el vínculo de réplica](https://msdn.microsoft.com/library/azure/mt600778.aspx)|Obtiene un vínculo de replicación específico para una base de datos SQL dado en una asociación de replicación geo. Recupera la información visible en la vista de catálogo sys.geo_replication_links.|
||||



## <a name="next-steps"></a>Pasos siguientes

- Para obtener una descripción de la continuidad empresarial y escenarios, vea [información general de continuidad empresarial](sql-database-business-continuity.md)
- Para obtener información sobre las copias de seguridad automatizadas de base de datos SQL Azure, consulte la [base de datos SQL había automatizado copias de seguridad](sql-database-automated-backups.md).
- Para obtener información sobre el uso de copias de seguridad automatizadas de recuperación, consulte [restaurar una base de datos de las copias de seguridad iniciado por el servicio](sql-database-recovery-using-backups.md).
- Para obtener información sobre el uso de copias de seguridad automatizadas para archivar, consulte [copia de la base de datos](sql-database-copy.md).
- Para obtener información sobre los requisitos de autenticación para un nuevo servidor principal y la base de datos, vea [seguridad de base de datos SQL después de recuperación](sql-database-geo-replication-security-config.md).
