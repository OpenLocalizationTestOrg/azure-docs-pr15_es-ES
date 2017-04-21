<properties
    pageTitle="Administrar la base de datos SQL Azure con PowerShell | Microsoft Azure"
    description="Administración de base de datos de SQL Azure con PowerShell."
    services="sql-database"
    documentationCenter=""
    authors="stevestein"
    manager="jhubbard"
    editor="monicar"/>

<tags
    ms.service="sql-database"
    ms.workload="data-management"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/13/2016"
    ms.author="sstein"/>

# <a name="manage-azure-sql-database-with-powershell"></a>Administrar la base de datos SQL Azure con PowerShell


> [AZURE.SELECTOR]
- [Portal de Azure](sql-database-manage-portal.md)
- [Transact-SQL (SSMS)](sql-database-manage-azure-ssms.md)
- [PowerShell](sql-database-manage-powershell.md)

Este tema muestra los cmdlets de PowerShell que se usan para realizar varias tareas de base de datos de SQL Azure. Para obtener una lista completa, vea [Cmdlets de base de datos de SQL Azure](https://msdn.microsoft.com/library/mt574084.aspx).


## <a name="create-a-resource-group"></a>Crear un grupo de recursos

Crear un grupo de recursos para nuestra base de datos SQL y recursos de Azure relacionados con el cmdlet [AzureRmResourceGroup de nuevo](https://msdn.microsoft.com/library/azure/mt759837.aspx) .

```
$resourceGroupName = "resourcegroup1"
$resourceGroupLocation = "northcentralus"
New-AzureRmResourceGroup -Name $resourceGroupName -Location $resourceGroupLocation
```

Para obtener más información, vea [Usar PowerShell de Azure con el Administrador de recursos de Azure](../powershell-azure-resource-manager.md).
Para un script de ejemplo, vea [crear un script de PowerShell de la base de datos SQL](sql-database-get-started-powershell.md#create-a-sql-database-powershell-script).

## <a name="create-a-sql-database-server"></a>Crear un servidor de base de datos SQL

Crear un servidor de base de datos SQL con el cmdlet [AzureRmSqlServer de nuevo](https://msdn.microsoft.com/library/azure/mt603715.aspx) . Reemplazar el *servidor 1* con el nombre de su servidor. Los nombres de servidor deben ser únicos en todos los servidores de base de datos de SQL Azure. Si ya se toma el nombre del servidor, recibe un error. Este comando puede tardar varios minutos en completarse. El grupo de recursos ya debe existir en la suscripción.

```
$resourceGroupName = "resourcegroup1"

$sqlServerName = "server1"
$sqlServerVersion = "12.0"
$sqlServerLocation = "northcentralus"
$serverAdmin = "loginname"
$serverPassword = "password" 
$securePassword = ConvertTo-SecureString –String $serverPassword –AsPlainText -Force
$creds = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $serverAdmin, $securePassword
    

$sqlServer = New-AzureRmSqlServer -ServerName $sqlServerName `
 -SqlAdministratorCredentials $creds -Location $sqlServerLocation `
 -ResourceGroupName $resourceGroupName -ServerVersion $sqlServerVersion
```

Para obtener más información, consulte [¿Qué es la base de datos de SQL](sql-database-technical-overview.md). Para un script de ejemplo, vea [crear un script de PowerShell de la base de datos SQL](sql-database-get-started-powershell.md#create-a-sql-database-powershell-script).


## <a name="create-a-sql-database-server-firewall-rule"></a>Crear una regla de firewall del servidor de base de datos SQL

Crear una regla de firewall para tener acceso al servidor con el cmdlet [AzureRmSqlServerFirewallRule de nuevo](https://msdn.microsoft.com/library/azure/mt603860.aspx) . Ejecute el comando siguiente, reemplazando las direcciones IP de inicio y finalización con valores válidos para su cliente. El grupo de recursos y el servidor ya deben existir en la suscripción.

```
$resourceGroupName = "resourcegroup1"
$sqlServerName = "server1"

$firewallRuleName = "firewallrule1"
$firewallStartIp = "0.0.0.0"
$firewallEndIp = "255.255.255.255"

New-AzureRmSqlServerFirewallRule -ResourceGroupName $resourceGroupName `
 -ServerName $sqlServerName -FirewallRuleName $firewallRuleName `
 -StartIpAddress $firewallStartIp -EndIpAddress $firewallEndIp
```

Para permitir el acceso de otros servicios de Azure en su servidor, crear una regla de firewall y establezca la `-StartIpAddress` y `-EndIpAddress` a **0.0.0.0**. Esta regla de firewall especial permite todo el tráfico de Azure tener acceso al servidor.

Para obtener más información, consulte [Firewall de base de datos de SQL Azure](https://msdn.microsoft.com/library/azure/ee621782.aspx). Para un script de ejemplo, vea [crear un script de PowerShell de la base de datos SQL](sql-database-get-started-powershell.md#create-a-sql-database-powershell-script).


## <a name="create-a-sql-database-blank"></a>Crear una base de datos SQL (en blanco)

Crear una base de datos con el cmdlet [AzureRmSqlDatabase de nuevo](https://msdn.microsoft.com/library/azure/mt619339.aspx) . El grupo de recursos y el servidor ya deben existir en la suscripción. 

```
$resourceGroupName = "resourcegroup1"
$sqlServerName = "server1"

$databaseName = "database1"
$databaseEdition = "Standard"
$databaseServiceLevel = "S0"

$currentDatabase = New-AzureRmSqlDatabase -ResourceGroupName $resourceGroupName `
 -ServerName $sqlServerName -DatabaseName $databaseName `
 -Edition $databaseEdition -RequestedServiceObjectiveName $databaseServiceLevel
```

Para obtener más información, consulte [¿Qué es la base de datos de SQL](sql-database-technical-overview.md). Para un script de ejemplo, vea [crear un script de PowerShell de la base de datos SQL](sql-database-get-started-powershell.md#create-a-sql-database-powershell-script).


## <a name="change-the-performance-level-of-a-sql-database"></a>Cambiar el nivel de rendimiento de una base de datos SQL

Ajustar el tamaño de la base de datos hacia arriba o hacia abajo con el cmdlet [Set-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt619433.aspx) . El grupo de recursos, el servidor y la base de datos ya deben existir en la suscripción. Establecer el `-RequestedServiceObjectiveName` en un solo espacio (por ejemplo, el fragmento de código siguiente) para nivel básico. Establecer a *S0*, *S1*, *P1*, *P6*, etc., como el ejemplo anterior para otros niveles.

```
$resourceGroupName = "resourcegroup1"
$sqlServerName = "server1"

$databaseName = "database1"
$databaseEdition = "Basic"
$databaseServiceLevel = " "

Set-AzureRmSqlDatabase -ResourceGroupName $resourceGroupName `
 -ServerName $sqlServerName -DatabaseName $databaseName `
 -Edition $databaseEdition -RequestedServiceObjectiveName $databaseServiceLevel
```

Para obtener más información, vea [Opciones de base de datos SQL y rendimiento: comprender qué está disponible en cada nivel de servicio](sql-database-service-tiers.md). Para un script de ejemplo, vea [secuencias de comandos de PowerShell de ejemplo para cambiar el nivel de rendimiento y los niveles de servicio de la base de datos SQL](sql-database-scale-up-powershell.md#sample-powershell-script-to-change-the-service-tier-and-performance-level-of-your-sql-database).

## <a name="copy-a-sql-database-to-the-same-server"></a>Copiar una base de datos SQL en el mismo servidor

Copiar una base de datos SQL en el mismo servidor con el cmdlet [AzureRmSqlDatabaseCopy de nuevo](https://msdn.microsoft.com/library/azure/mt603644.aspx) . Establecer el `-CopyServerName` y `-CopyResourceGroupName` a los mismos valores que su grupo de servidores y recursos de la base de datos de origen.

```
$resourceGroupName = "resourcegroup1"
$sqlServerName = "server1"
$databaseName = "database1"

$copyDatabaseName = "database1_copy"
$copyServerName = $sqlServerName
$copyResourceGroupName = $resourceGroupName

New-AzureRmSqlDatabaseCopy -DatabaseName $databaseName `
 -ServerName $sqlServerName -ResourceGroupName $resourceGroupName `
 -CopyDatabaseName $copyDatabaseName -CopyServerName $sqlServerName `
 -CopyResourceGroupName $copyResourceGroupName
```

Para obtener más información, vea [Copiar una base de datos de SQL Azure](sql-database-copy.md). Para un script de ejemplo, vea [Copiar un script de PowerShell de la base de datos SQL](sql-database-copy-powershell.md#example-powershell-script).


## <a name="delete-a-sql-database"></a>Eliminar una base de datos SQL

Eliminar una base de datos SQL con el cmdlet [Quitar AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt619368.aspx) . El grupo de recursos, el servidor y la base de datos ya deben existir en la suscripción.

```
$resourceGroupName = "resourcegroup1"
$sqlServerName = "server1"
$databaseName = "database1"

Remove-AzureRmSqlDatabase -DatabaseName $databaseName `
 -ServerName $sqlServerName -ResourceGroupName $resourceGroupName
```

## <a name="delete-a-sql-database-server"></a>Eliminar un servidor de base de datos SQL

Eliminar un servidor con el cmdlet [Quitar AzureRmSqlServer](https://msdn.microsoft.com/library/azure/mt603488.aspx) .

```
$resourceGroupName = "resourcegroup1"
$sqlServerName = "server1"

Remove-AzureRmSqlServer -ServerName $sqlServerName -ResourceGroupName $resourceGroupName
```

## <a name="create-and-manage-elastic-database-pools-using-powershell"></a>Crear y administrar grupos de elásticos de la base de datos con PowerShell

Para obtener más información sobre cómo crear grupos de base de datos elástico con PowerShell, vea [crear un nuevo grupo de base de datos elástico con PowerShell](sql-database-elastic-pool-create-powershell.md).

Para obtener más información acerca de cómo administrar grupos de elásticos de la base de datos con PowerShell, vea [Monitor y administrar un grupo de base de datos elástico con PowerShell](sql-database-elastic-pool-manage-powershell.md).



## <a name="related-information"></a>Información relacionada

- [Cmdlets de base de datos SQL Azure](https://msdn.microsoft.com/library/azure/mt574084.aspx)
- [Referencia de Cmdlet de Azure](https://msdn.microsoft.com/library/azure/dn708514.aspx)
