<properties 
    pageTitle="Configurar la replicación de Geo Active para base de datos de SQL Azure con PowerShell | Microsoft Azure" 
    description="Configurar la replicación de Geo Active para base de datos de SQL Azure con PowerShell" 
    services="sql-database" 
    documentationCenter="" 
    authors="stevestein" 
    manager="jhubbard" 
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.topic="article"
    ms.tgt_pltfrm="powershell"
   ms.workload="NA"
    ms.date="07/14/2016"
    ms.author="sstein"/>

# <a name="configure-geo-replication-for-azure-sql-database-with-powershell"></a>Configurar la replicación de Geo para base de datos SQL Azure con PowerShell

> [AZURE.SELECTOR]
- [Información general](sql-database-geo-replication-overview.md)
- [Portal de Azure](sql-database-geo-replication-portal.md)
- [PowerShell](sql-database-geo-replication-powershell.md)
- [T-SQL](sql-database-geo-replication-transact-sql.md)

En este artículo se muestra cómo configurar la replicación de Geo Active para la base de datos de SQL con PowerShell.

Para iniciar la migración tras error con PowerShell, vea [iniciar una migración tras error planificada o para la base de datos de Azure SQL con PowerShell](sql-database-geo-replication-failover-powershell.md).

>[AZURE.NOTE] Replicación de Geo Active (secundarios legibles) ahora está disponible para todas las bases de datos en todos los niveles de servicio. En abril de 2017 se retirará el tipo secundario no se puede leer y bases de datos no se puede leer existentes se actualizarán automáticamente a secundarios legibles.



Para configurar la replicación de Geo Active con PowerShell, necesita lo siguiente:

- Una suscripción de Azure. 
- Una base de datos de SQL Azure - la base de datos principal que desea duplicar.
- Azure PowerShell 1.0 o posterior. Puede descargar e instalar los módulos de Azure PowerShell por siguientes [cómo instalar y configurar Azure PowerShell](../powershell-install-configure.md).


## <a name="configure-your-credentials-and-select-your-subscription"></a>Configurar las credenciales y seleccione la suscripción

En primer lugar debe establecer acceso a su cuenta de Azure iniciar PowerShell y, a continuación, ejecute el cmdlet siguiente. En la pantalla de inicio de sesión, escriba el mismo correo electrónico y contraseña que utiliza para iniciar sesión en el portal de Azure.


    Login-AzureRmAccount

Tras iniciar sesión correctamente en verá parte de la información en pantalla que contiene el identificador ha iniciado sesión en con y las suscripciones de Azure que tiene acceso.


### <a name="select-your-azure-subscription"></a>Seleccione la suscripción de Azure

Para seleccionar la suscripción que necesita su suscripción de Id. Puede copiar el identificador de la suscripción de la información que aparece en el paso anterior, o si tiene varias suscripciones y necesita más información puede ejecutar el cmdlet **Get-AzureRmSubscription** y copie la información de la suscripción que desee en el conjunto de resultados. El siguiente cmdlet utiliza el identificador de la suscripción para definir la suscripción actual:

    Select-AzureRmSubscription -SubscriptionId 4cac86b0-1e56-bbbb-aaaa-000000000000

Después de ejecutar correctamente **Seleccione AzureRmSubscription** se vuelve a la PowerShell del mensaje.


## <a name="add-secondary-database"></a>Agregar base de datos secundaria


Los pasos siguientes crean una nueva base de datos secundario en una asociación de replicación Geo.  
  
Para habilitar un secundario debe ser el propietario de la suscripción o copropietario. 

Puede usar el cmdlet **AzureRmSqlDatabaseSecondary de nuevo** para agregar una base de datos secundario en un servidor asociado a una base de datos local en el servidor al que está conectado (base de datos principal). 

Este cmdlet reemplaza **AzureSqlDatabaseCopy de inicio** con el parámetro **– IsContinuous** .  Dará como resultado un objeto de **AzureRmSqlDatabaseSecondary** que puede ser usado por otros cmdlets para identificar claramente un vínculo de replicación específica. Este cmdlet devolverá al crear la base de datos secundario y completamente generada. Dependiendo del tamaño de la base de datos puede tardar entre minutos a horas.

La base de datos replicada en el servidor secundario tendrá el mismo nombre que la base de datos en el servidor principal y, de forma predeterminada, tienen el mismo nivel de servicio. La base de datos secundario puede ser legible o no se puede leer y puede ser una base de datos o una base de datos elástico. Para obtener más información, vea [Niveles de servicio](sql-database-service-tiers.md)y [AzureRMSqlDatabaseSecondary de nuevo](https://msdn.microsoft.com/library/mt603689.aspx) .
Después de crear y visible el secundario, datos comenzará la replicación de la base de datos principal a la nueva base de datos secundario. Los pasos siguientes describen cómo llevar a cabo esta tarea mediante PowerShell para crear secundarios no se puede leer y legibles con una base de datos o una base de datos elástico.

Si ya existe la base de datos de partner (por ejemplo - como resultado de terminar una relación de replicación Geo anterior) se producirá un error en el comando.



### <a name="add-a-non-readable-secondary-single-database"></a>Agregar un secundario no se puede leer (base de datos único)

El comando siguiente crea un secundario no se puede leer de la base de datos "mydb" de servidor "srv2" en el grupo de recursos "rg2":

    $database = Get-AzureRmSqlDatabase –DatabaseName "mydb" -ResourceGroupName "rg1" -ServerName "srv1"
    $secondaryLink = $database | New-AzureRmSqlDatabaseSecondary –PartnerResourceGroupName "rg2" –PartnerServerName "srv2" -AllowConnections "No"



### <a name="add-readable-secondary-single-database"></a>Agregar secundario legible (base de datos único)

El comando siguiente crea un legible secundario de base de datos "mydb" de servidor "srv2" en el grupo de recursos "rg2":

    $database = Get-AzureRmSqlDatabase –DatabaseName "mydb" -ResourceGroupName "rg1" -ServerName "srv1"
    $secondaryLink = $database | New-AzureRmSqlDatabaseSecondary –PartnerResourceGroupName "rg2" –PartnerServerName "srv2" -AllowConnections "All"




### <a name="add-a-non-readable-secondary-elastic-database"></a>Agregar un secundario no se puede leer (base de datos elástica)

El comando siguiente crea un secundario no se puede leer de la base de datos "mydb" en el grupo elásticos de la base de datos denominado "ElasticPool1" de servidor "srv2" en el grupo de recursos "rg2":

    $database = Get-AzureRmSqlDatabase –DatabaseName "mydb" -ResourceGroupName "rg1" -ServerName "srv1"
    $secondaryLink = $database | New-AzureRmSqlDatabaseSecondary –PartnerResourceGroupName "rg2" –PartnerServerName "srv2" –SecondaryElasticPoolName "ElasticPool1" -AllowConnections "No"


### <a name="add-a-readable-secondary-elastic-database"></a>Agregar un secundario legible (base de datos elástica)

El comando siguiente crea un legible secundario de base de datos "mydb" en el grupo elásticos de la base de datos denominado "ElasticPool1" de servidor "srv2" en el grupo de recursos "rg2":

    $database = Get-AzureRmSqlDatabase –DatabaseName "mydb" -ResourceGroupName "rg1" -ServerName "srv1"
    $secondaryLink = $database | New-AzureRmSqlDatabaseSecondary –PartnerResourceGroupName "rg2" –PartnerServerName "srv2" –SecondaryElasticPoolName "ElasticPool1" -AllowConnections "All"





## <a name="remove-secondary-database"></a>Quitar la base de datos secundaria

Usar el cmdlet **AzureRmSqlDatabaseSecondary quitar** permanentemente terminar la asociación de replicación entre una base de datos secundaria y su principal. Después de la finalización de la relación, la base de datos secundaria se convierte en una base de datos de lectura y escritura. Si la conectividad de base de datos secundaria se rompe el comando se realiza correctamente pero el secundario se convertirá en lectura y escritura después de que se restaura la conectividad. Para obtener más información, vea [Quitar AzureRmSqlDatabaseSecondary](https://msdn.microsoft.com/library/mt603457.aspx) y [Niveles de servicio](sql-database-service-tiers.md).

Este cmdlet reemplaza detener AzureSqlDatabaseCopy para la replicación. 

Esta eliminación es equivalente de una terminación forzada que se quita el vínculo de réplica y deja el antiguo secundario como una base de datos independiente que no está completamente replicada antes de la finalización. Todos los datos de vínculo se limpia en el antiguo principal y secundario, primero si o cuando estén disponibles. Este cmdlet devolverá cuando se elimina el vínculo de réplica. 


Para quitar secundario, los usuarios deben tener acceso de escritura a bases de datos principales y secundarios según RBAC. Consulte control de acceso basado en roles para obtener información detallada.

El código siguiente quita el vínculo de replicación de base de datos "mydb" al servidor "srv2" del grupo de recursos "rg2". 

    $database = Get-AzureRmSqlDatabase –DatabaseName "mydb" -ResourceGroupName "rg1" -ServerName "srv1"
    $secondaryLink = $database | Get-AzureRmSqlDatabaseReplicationLink –SecondaryResourceGroup "rg2" –PartnerServerName "srv2"
    $secondaryLink | Remove-AzureRmSqlDatabaseSecondary 


## <a name="monitor-geo-replication-configuration-and-health"></a>Configuración de replicación geo monitor y el estado

Tareas de supervisión incluyen la supervisión de la configuración de replicación geo y supervisar el estado de la replicación de datos.  

[Get-AzureRmSqlDatabaseReplicationLink](https://msdn.microsoft.com/library/mt619330.aspx) puede utilizarse para recuperar la información acerca de los vínculos de replicación reenviar visibles en la vista de catálogo sys.geo_replication_links.

El comando siguiente recupera el estado de los vínculos de replicación entre la base de datos "mydb" principal y secundario en el servidor "srv2" del grupo de recursos "rg2".

    $database = Get-AzureRmSqlDatabase –DatabaseName "mydb" -ResourceGroupName "rg1" -ServerName "srv1"
    $secondaryLink = $database | Get-AzureRmSqlDatabaseReplicationLink –PartnerResourceGroup "rg2” –PartnerServerName "srv2”


## <a name="next-steps"></a>Pasos siguientes

- Para obtener más información sobre Active Geo replicación, vea - [Replicación de Geo Active](sql-database-geo-replication-overview.md)
- Para obtener una descripción de la continuidad empresarial y escenarios, vea [información general de continuidad empresarial](sql-database-business-continuity.md)

