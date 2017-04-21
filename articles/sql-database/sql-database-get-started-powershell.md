<properties
    pageTitle="Nueva configuración de la base de datos SQL con PowerShell | Microsoft Azure"
    description="Obtenga información sobre ahora crear una base de datos SQL con PowerShell. Tareas comunes de instalación de base de datos se pueden administrar a través de los cmdlets de PowerShell."
    keywords="Crear nueva base de datos sql, el programa de instalación de la base de datos"
    services="sql-database"
    documentationCenter=""
    authors="stevestein"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.topic="hero-article"
    ms.tgt_pltfrm="powershell"
    ms.workload="data-management"
    ms.date="08/19/2016"
    ms.author="sstein"/>

# <a name="create-a-sql-database-and-perform-common-database-setup-tasks-with-powershell-cmdlets"></a>Crear una base de datos SQL y realizar tareas comunes de instalación de base de datos con los cmdlets de PowerShell


> [AZURE.SELECTOR]
- [Portal de Azure](sql-database-get-started.md)
- [PowerShell](sql-database-get-started-powershell.md)
- [C#](sql-database-get-started-csharp.md)



Aprenda a crear una base de datos SQL mediante cmdlets de PowerShell. (Para crear bases de datos elásticos, consulte [crear un nuevo grupo de base de datos elástico con PowerShell](sql-database-elastic-pool-create-powershell.md)).


[AZURE.INCLUDE [Start your PowerShell session](../../includes/sql-database-powershell.md)]

## <a name="database-setup-create-a-resource-group-server-and-firewall-rule"></a>Configuración de la base de datos: crear una regla de firewall, el servidor y el grupo de recursos

Una vez que tenga acceso a ejecutar los cmdlets en su suscripción de Azure seleccionada, el siguiente paso es establecer el grupo de recursos que contiene el servidor donde se creará la base de datos. Puede editar el comando siguiente para usar cualquier ubicación válida que elija. Ejecutar **(Get-AzureRmLocation | Objeto de dónde {$_. Proveedores - EC "Microsoft.Sql"}). Ubicación** para obtener una lista de ubicaciones válidas.

Ejecute el comando siguiente para crear un grupo de recursos:

    New-AzureRmResourceGroup -Name "resourcegroupsqlgsps" -Location "westus"


### <a name="create-a-server"></a>Crear un servidor

Bases de datos SQL se crean dentro de los servidores de base de datos de SQL Azure. Ejecutar el [New-AzureRmSqlServer] (https://msdn.microsoft.com/library/azure/mt603715(v=azure.300\).aspx) para crear un servidor. El nombre de su servidor debe ser único en todos los servidores de base de datos de SQL Azure. Si ya se toma el nombre del servidor, recibe un error. Destacar también que este comando puede tardar varios minutos en completarse. Puede editar el comando usar cualquier ubicación válida que elija, pero debe usar la misma ubicación utilizado para el grupo de recursos que se creó en el paso anterior.

    New-AzureRmSqlServer -ResourceGroupName "resourcegroupsqlgsps" -ServerName "server1" -Location "westus" -ServerVersion "12.0"

Cuando ejecuta este comando, se le pedirá su nombre de usuario y contraseña. No escriba sus credenciales de Azure. En su lugar, escriba el nombre de usuario y contraseña para crear como el administrador del servidor. La secuencia de comandos en la parte inferior de este artículo muestra cómo establecer las credenciales del servidor en el código.

Los detalles de servidor aparecen después de que se ha creado con el servidor.

### <a name="configure-a-server-firewall-rule-to-allow-access-to-the-server"></a>Configurar una regla de firewall de servidor para permitir el acceso al servidor

Para obtener acceso al servidor, debe establecer una regla de firewall. Ejecutar el [New-AzureRmSqlServerFirewallRule] (comando https://msdn.microsoft.com/library/azure/mt603860(v=azure.300\).aspx), reemplazar las direcciones IP de inicio y finalización con los valores válidos para el equipo.

    New-AzureRmSqlServerFirewallRule -ResourceGroupName "resourcegroupsqlgsps" -ServerName "server1" -FirewallRuleName "rule1" -StartIpAddress "192.168.0.0" -EndIpAddress "192.168.0.0"

Detalles de la regla de firewall aparecen después de la regla se ha creado correctamente.

Para permitir que otros servicios de Azure tener acceso al servidor, agregar una regla de firewall y establezca la StartIpAddress y la EndIpAddress a 0.0.0.0. Esta regla permite tráfico Azure desde cualquier suscripción Azure para tener acceso al servidor.

Para obtener más información, consulte [Firewall de base de datos de SQL Azure](sql-database-firewall-configure.md).


## <a name="create-a-sql-database"></a>Crear una base de datos SQL

Ahora tiene un grupo de recursos, un servidor y una regla de firewall configurado para que pueda acceder el servidor.

Los siguientes [nuevo AzureRmSqlDatabase] (https://msdn.microsoft.com/library/azure/mt619339(v=azure.300\).aspx) comando crea una base de datos SQL (en blanco) en el nivel de servicio estándar, con un nivel de rendimiento de S1:


    New-AzureRmSqlDatabase -ResourceGroupName "resourcegroupsqlgsps" -ServerName "server1" -DatabaseName "database1" -Edition "Standard" -RequestedServiceObjectiveName "S1"


Los detalles de la base de datos aparecen después de la base de datos se creó correctamente.

## <a name="create-a-sql-database-powershell-script"></a>Crear un script de PowerShell de la base de datos SQL

El siguiente script de PowerShell crea una base de datos SQL y todos sus recursos dependientes. Reemplazar todo `{variables}` con valores específicos para la suscripción y los recursos (quitar el **{}** al establecer los valores).

    # Sign in to Azure and set the subscription to work with
    $SubscriptionId = "{subscription-id}"

    Add-AzureRmAccount
    Set-AzureRmContext -SubscriptionId $SubscriptionId

    # CREATE A RESOURCE GROUP
    $resourceGroupName = "{group-name}"
    $rglocation = "{Azure-region}"
    
    New-AzureRmResourceGroup -Name $resourceGroupName -Location $rglocation
    
    # CREATE A SERVER
    $serverName = "{server-name}"
    $serverVersion = "12.0"
    $serverLocation = "{Azure-region}"
    
    $serverAdmin = "{server-admin}"
    $serverPassword = "{server-password}" 
    $securePassword = ConvertTo-SecureString –String $serverPassword –AsPlainText -Force
    $serverCreds = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $serverAdmin, $securePassword
    
    $sqlDbServer = New-AzureRmSqlServer -ResourceGroupName $resourceGroupName -ServerName $serverName -Location $serverLocation -ServerVersion $serverVersion -SqlAdministratorCredentials $serverCreds
    
    # CREATE A SERVER FIREWALL RULE
    $ip = (Test-Connection -ComputerName $env:COMPUTERNAME -Count 1 -Verbose).IPV4Address.IPAddressToString
    $firewallRuleName = '{rule-name}'
    $firewallStartIp = $ip
    $firewallEndIp = $ip
    
    $fireWallRule = New-AzureRmSqlServerFirewallRule -ResourceGroupName $resourceGroupName -ServerName $serverName -FirewallRuleName $firewallRuleName -StartIpAddress $firewallStartIp -EndIpAddress $firewallEndIp
    
    
    # CREATE A SQL DATABASE
    $databaseName = "{database-name}"
    $databaseEdition = "{Standard}"
    $databaseSlo = "{S0}"
    
    $sqlDatabase = New-AzureRmSqlDatabase -ResourceGroupName $resourceGroupName -ServerName $serverName -DatabaseName $databaseName -Edition $databaseEdition -RequestedServiceObjectiveName $databaseSlo
    
   
    # REMOVE ALL RESOURCES THE SCRIPT JUST CREATED
    #Remove-AzureRmResourceGroup -Name $resourceGroupName






## <a name="next-steps"></a>Pasos siguientes
Después de crear una base de datos SQL y realizar tareas de configuración básica de la base de datos, está listo para lo siguiente:

- [Administrar la base de datos SQL con PowerShell](sql-database-manage-powershell.md)
- [Conectarse a la base de datos de SQL con SQL Server Management Studio y realizar una consulta de SQL de T de ejemplo](sql-database-connect-query-ssms.md)


## <a name="additional-resources"></a>Recursos adicionales

- [Cmdlets de base de datos SQL azure] (https://msdn.microsoft.com/library/azure/mt574084(v=azure.300\).aspx)
- [Base de datos SQL Azure](https://azure.microsoft.com/documentation/services/sql-database/)
