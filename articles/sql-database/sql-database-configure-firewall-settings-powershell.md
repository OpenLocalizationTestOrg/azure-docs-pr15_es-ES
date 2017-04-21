<properties
    pageTitle="Configurar las reglas de firewall de nivel de servidor de base de datos de SQL Azure con PowerShell | Microsoft Azure"
    description="Obtenga información sobre cómo configurar el firewall para las direcciones IP que tienen acceso a las bases de datos de SQL Azure."
    services="sql-database"
    documentationCenter=""
    authors="stevestein"
    manager="jhubbard"
    editor=""/>


<tags
    ms.service="sql-database"
    ms.workload="data-management"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="08/09/2016"
    ms.author="sstein"/>


# <a name="configure-azure-sql-database-server-level-firewall-rules-by-using-powershell"></a>Configurar las reglas de firewall de nivel de servidor de base de datos de SQL Azure con PowerShell


> [AZURE.SELECTOR]
- [Información general](sql-database-firewall-configure.md)
- [Portal de Azure](sql-database-configure-firewall-settings.md)
- [TSQL](sql-database-configure-firewall-settings-tsql.md)
- [PowerShell](sql-database-configure-firewall-settings-powershell.md)
- [API DE REST](sql-database-configure-firewall-settings-rest.md)


Base de datos de SQL Azure usa las reglas de firewall para permitir las conexiones a los servidores y bases de datos. Puede definir configuraciones de firewall de nivel de servidor y de nivel de base de datos para la base de datos o una base de datos de usuario en el servidor de base de datos de SQL para permitir el acceso a la base de datos de forma selectiva.

> [AZURE.IMPORTANT] Para permitir que las aplicaciones de Azure conectarse al servidor de base de datos, deben estar habilitadas conexiones de Azure. Para obtener más información sobre las reglas de firewall y habilitar las conexiones de Azure, consulte [Firewall de base de datos de SQL Azure](sql-database-firewall-configure.md). Si va a realizar conexiones dentro del límite de nube de Azure, debe abrir algunos puertos TCP adicionales. Para obtener más información, vea la "V12 de base de datos SQL: fuera vs dentro de" sección de [puertos más allá de 1433 para ADO.NET 4.5 y V12 de base de datos de SQL](sql-database-develop-direct-route-ports-adonet-v12.md).


[AZURE.INCLUDE [Start your PowerShell session](../../includes/sql-database-powershell.md)]

## <a name="create-server-firewall-rules"></a>Crear reglas de firewall de servidor

Firewall de nivel de servidor que se pueden crear reglas, actualizan y eliminan con PowerShell de Azure.

Para crear una nueva regla de firewall de nivel de servidor, ejecute el [New-AzureRmSqlServerFirewallRule] (https://msdn.microsoft.com/library/azure/mt603860(v=azure.300\).aspx) cmdlet. En el ejemplo siguiente se habilita un intervalo de direcciones IP en el servidor de Contoso.

    New-AzureRmSqlServerFirewallRule -ResourceGroupName 'resourcegroup1' -ServerName 'Contoso' -FirewallRuleName "ContosoFirewallRule" -StartIpAddress '192.168.1.1' -EndIpAddress '192.168.1.10'       

Para modificar una regla de firewall de nivel de servidor existente, ejecute el [Set-AzureRmSqlServerFirewallRule] (https://msdn.microsoft.com/library/azure/mt603789(v=azure.300\).aspx) cmdlet. En el ejemplo siguiente se cambia el intervalo de direcciones IP aceptable para la regla denominada ContosoFirewallRule.

    Set-AzureRmSqlServerFirewallRule -ResourceGroupName 'resourcegroup1' –StartIPAddress 192.168.1.4 –EndIPAddress 192.168.1.10 –RuleName 'ContosoFirewallRule' –ServerName 'Contoso'

Para eliminar una regla de firewall de nivel de servidor existente, ejecute el [quitar-AzureRmSqlServerFirewallRule] (https://msdn.microsoft.com/library/azure/mt603588(v=azure.300\).aspx) cmdlet. En el ejemplo siguiente se elimina la regla denominada ContosoFirewallRule.

    Remove-AzureRmSqlServerFirewallRule –RuleName 'ContosoFirewallRule' –ServerName 'Contoso'


## <a name="manage-firewall-rules-by-using-powershell"></a>Administrar las reglas de firewall con PowerShell

También puede usar PowerShell para administrar las reglas de firewall. Para obtener más información, vea los temas siguientes:

* [Nuevo-AzureRmSqlServerFirewallRule] (https://msdn.microsoft.com/library/azure/mt603860(v=azure.300\).aspx)
* [Quitar AzureRmSqlServerFirewallRule] (https://msdn.microsoft.com/library/azure/mt603588(v=azure.300\).aspx)
* [Set-AzureRmSqlServerFirewallRule] (https://msdn.microsoft.com/library/azure/mt603789(v=azure.300\).aspx)
* [Get-AzureRmSqlServerFirewallRule] (https://msdn.microsoft.com/library/azure/mt603586(v=azure.300\).aspx)


## <a name="next-steps"></a>Pasos siguientes

Para obtener información sobre cómo usar Transact-SQL para crear reglas de firewall de nivel de servidor y de nivel de base de datos, vea [Configurar la base de datos de SQL de Azure reglas de firewall de nivel de base de datos y servidor mediante T SQL](sql-database-configure-firewall-settings-tsql.md).

Para obtener información sobre cómo crear reglas de firewall de nivel de servidor con otros métodos, consulte:

- [Configurar las reglas de firewall de nivel de servidor de base de datos de SQL Azure con el portal de Azure](sql-database-configure-firewall-settings.md)
- [Configurar las reglas de firewall de nivel de servidor de base de datos de SQL Azure con la API de REST](sql-database-configure-firewall-settings-rest.md)

Para obtener un tutorial acerca de cómo crear una base de datos, vea [crear una base de datos SQL en minutos con el portal de Azure](sql-database-get-started.md).
Ayuda para conectarse a una base de datos de SQL Azure de Abrir origen o aplicaciones de otros fabricantes, vea [ejemplos de código para la base de datos SQL de inicio de rápido de cliente](https://msdn.microsoft.com/library/azure/ee336282.aspx).
Para comprender cómo navegar a bases de datos, consulte [Administrar la seguridad de access e inicio de sesión de base de datos](https://msdn.microsoft.com/library/azure/ee336235.aspx).


## <a name="additional-resources"></a>Recursos adicionales

- [Proteger la base de datos](sql-database-security.md)
- [Centro de seguridad de base de datos SQL Azure y motor de base de datos SQL Server](https://msdn.microsoft.com/library/bb510589)


<!--Image references-->
[1]: ./media/sql-database-configure-firewall-settings/AzurePortalBrowseForFirewall.png
[2]: ./media/sql-database-configure-firewall-settings/AzurePortalFirewallSettings.png
<!--anchors-->
