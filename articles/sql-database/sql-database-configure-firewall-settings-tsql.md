<properties
    pageTitle="Reglas de firewall de nivel de base de datos y servidor base de datos de SQL Azure con SQL T | Microsoft Azure"
    description="Obtenga información sobre cómo configurar el firewall para las direcciones IP que tienen acceso a las bases de datos de SQL Azure."
    services="sql-database"
    documentationCenter=""
    authors="BYHAM"
    manager="jhubbard"
    editor=""/>


<tags
    ms.service="sql-database"
    ms.workload="data-management"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="article" 
    ms.date="08/30/2016"
    ms.author="rickbyh"/>


# <a name="configure-azure-sql-database-server-level-and-database-level-firewall-rules-using-t-sql"></a>Configurar reglas de firewall de nivel de base de datos y servidor de base de datos de SQL Azure con SQL T


> [AZURE.SELECTOR]
- [Información general](sql-database-firewall-configure.md)
- [Portal de Azure](sql-database-configure-firewall-settings.md)
- [TSQL](sql-database-configure-firewall-settings-tsql.md)
- [PowerShell](sql-database-configure-firewall-settings-powershell.md)
- [API DE REST](sql-database-configure-firewall-settings-rest.md)


Base de datos de SQL de Microsoft Azure usa las reglas de firewall para permitir las conexiones a los servidores y bases de datos. Puede definir configuraciones de firewall de nivel de base de datos y servidor para el patrón o una base de datos de usuario en el servidor de base de datos de SQL Azure para permitir el acceso a la base de datos de forma selectiva.

> [AZURE.IMPORTANT] Para permitir que las aplicaciones de Azure conectarse al servidor de base de datos, deben estar habilitadas conexiones de Azure. Para obtener más información sobre las reglas de firewall y habilitar las conexiones de Azure, consulte [Firewall de base de datos de SQL Azure](sql-database-firewall-configure.md). Si va a realizar conexiones dentro del límite de nube de Azure, debe abrir algunos puertos TCP adicionales. Para obtener más información, consulte la **V12 de SQL base de datos: fuera vs dentro de** sección de [puertos más allá de 1433 para ADO.NET 4.5 y V12 de base de datos de SQL](sql-database-develop-direct-route-ports-adonet-v12.md)


## <a name="server-level-firewall-rules"></a>Reglas de firewall de nivel de servidor

Solo el inicio de sesión principal de nivel de servidor o el Administrador de Azure Active Directory puede crear una regla de firewall de nivel de servidor mediante Transact-SQL.

1. Inicie una ventana de consulta y conectarse a la base de datos maestra virtual con SQL Server Management Studio.
2. Las reglas de firewall de nivel de servidor pueden seleccionadas, creadas, actualizadas o eliminadas desde dentro de la ventana de consulta.
3. Para crear o actualizar las reglas de firewall de nivel de servidor, ejecute el `sp_set_firewall_rule` procedimiento almacenado. En el ejemplo siguiente se habilita un intervalo de direcciones IP en el servidor de Contoso.<br/>Comience por ver qué reglas ya existe.

        SELECT * FROM sys.firewall_rules ORDER BY name;

    A continuación, agregue una regla de firewall.

        EXECUTE sp_set_firewall_rule @name = N'ContosoFirewallRule',
            @start_ip_address = '192.168.1.1', @end_ip_address = '192.168.1.10'

    Para eliminar una regla de firewall de nivel de servidor, ejecute el procedimiento almacenado de sp_delete_firewall_rule. En el ejemplo siguiente se elimina la regla denominada ContosoFirewallRule.
 
        EXECUTE sp_delete_firewall_rule @name = N'ContosoFirewallRule'
 
 Para obtener más información sobre estos procedimientos almacenados, consulte [sp_set_firewall_rule](https://msdn.microsoft.com/library/dn270017.aspx) y [sp_delete_firewall_rule](https://msdn.microsoft.com/library/dn270024.aspx).

## <a name="database-level-firewall-rules"></a>Reglas de firewall de nivel de base de datos

Solo un usuario de la base de datos con el permiso de **CONTROL** en la base de datos (por ejemplo, el propietario de la base de datos) puede crear una regla de firewall de nivel de base de datos.

1. Después de crear un firewall de nivel de servidor para la dirección IP, inicie una ventana de consulta a través del portal estándar o a través de SQL Server Management Studio.
2. Conectarse a la base de datos para el que desea crear una regla de firewall de nivel de base de datos.

    Para crear una nueva o actualizar una regla de firewall de nivel de base de datos existente, ejecute el `sp_set_database_firewall_rule` procedimiento almacenado. En el ejemplo siguiente se crea una nueva regla de firewall denominada ContosoFirewallRule.
 
        EXEC sp_set_database_firewall_rule @name = N'ContosoFirewallRule', 
            @start_ip_address = '192.168.1.11', @end_ip_address = '192.168.1.11'
 
    Para eliminar una regla de firewall de nivel de base de datos existente, ejecute el `sp_delete_database_firewall_rule` procedimiento almacenado. En el ejemplo siguiente se elimina la regla denominada ContosoFirewallRule.
`
   EJECUTIVO sp_delete_database_firewall_rule @name = N'ContosoFirewallRule'

Para obtener más información sobre estos procedimientos almacenados, consulte [sp_set_database_firewall_rule](https://msdn.microsoft.com/library/dn270010.aspx) y [sp_delete_database_firewall_rule](https://msdn.microsoft.com/library/dn270030.aspx).

## <a name="next-steps"></a>Pasos siguientes

Para artículos sobre la creación de reglas de firewall de nivel de servidor con otros métodos, consulte: 

- [Configurar las reglas de firewall de nivel de servidor de base de datos de SQL Azure con el Portal de Azure](sql-database-configure-firewall-settings.md)
- [Configurar las reglas de firewall de nivel de servidor de base de datos de SQL Azure con PowerShell](sql-database-configure-firewall-settings-powershell.md)
- [Configurar las reglas de firewall de nivel de servidor de base de datos de SQL Azure con la API de REST](sql-database-configure-firewall-settings-rest.md)

Para obtener un tutorial acerca de cómo crear una base de datos, vea [crear una base de datos SQL en minutos con el portal de Azure](sql-database-get-started.md).
Para obtener ayuda para la conexión a una base de datos de SQL Azure de Abrir origen o aplicaciones de terceros, vea [ejemplos de código para la base de datos SQL de inicio de rápido de cliente](https://msdn.microsoft.com/library/azure/ee336282.aspx).
Para comprender cómo navegar a bases de datos, consulte [Administrar la seguridad de access e inicio de sesión de base de datos](https://msdn.microsoft.com/library/azure/ee336235.aspx).


## <a name="additional-resources"></a>Recursos adicionales

- [Proteger la base de datos](sql-database-security.md)
- [Centro de seguridad de base de datos SQL Azure y motor de base de datos SQL Server](https://msdn.microsoft.com/library/bb510589)
