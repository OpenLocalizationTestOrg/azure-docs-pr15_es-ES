<properties
    pageTitle="Reglas de firewall de nivel de servidor de base de datos de SQL Azure con la API de REST | Microsoft Azure"
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


#  <a name="configure-azure-sql-database-server-level-firewall-rules-using-the-rest-api"></a>Configurar las reglas de firewall de nivel de servidor de base de datos de SQL Azure con la API de REST


> [AZURE.SELECTOR]
- [Información general](sql-database-firewall-configure.md)
- [Portal de Azure](sql-database-configure-firewall-settings.md)
- [TSQL](sql-database-configure-firewall-settings-tsql.md)
- [PowerShell](sql-database-configure-firewall-settings-powershell.md)
- [API DE REST](sql-database-configure-firewall-settings-rest.md)


Base de datos de SQL de Microsoft Azure usa las reglas de firewall para permitir las conexiones a los servidores y bases de datos. Puede definir configuraciones de firewall de nivel de base de datos y servidor para el patrón o una base de datos de usuario en el servidor de base de datos de SQL Azure para permitir el acceso a la base de datos de forma selectiva.

> [AZURE.IMPORTANT] Para permitir que las aplicaciones de Azure conectarse al servidor de base de datos, deben estar habilitadas conexiones de Azure. Para obtener más información sobre las reglas de firewall y habilitar las conexiones de Azure, consulte [Firewall de base de datos de SQL Azure](sql-database-firewall-configure.md). Si va a realizar conexiones dentro del límite de nube de Azure, debe abrir algunos puertos TCP adicionales. Para obtener más información, consulte la **V12 de SQL base de datos: fuera vs dentro de** sección de [puertos más allá de 1433 para ADO.NET 4.5 y V12 de base de datos de SQL](sql-database-develop-direct-route-ports-adonet-v12.md)


## <a name="manage-server-level-firewall-rules-through-rest-api"></a>Administrar reglas de firewall de nivel de servidor a través de la API de REST
1. Administrar reglas de firewall a través de la API de REST se debe autenticar. Para obtener más información, consulte [la Guía del desarrollador de autorización con la API del Administrador de recursos de Azure](../resource-manager-api-authentication.md).
2. Las reglas de nivel de servidor pueden crearse actualizada o eliminada con la API de REST

    Para crear o actualizar una regla de firewall de nivel de servidor, ejecute el método SUPERPONER con los siguientes:
 
        https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/firewallRules/{rule-name}?api-version={api-version}
    
    Cuerpo de la solicitud

        {
         "properties": { 
            "startIpAddress": "{start-ip-address}", 
            "endIpAddress": "{end-ip-address}
            }
        } 
 

    Para quitar una regla de firewall de nivel de servidor existente, ejecute el método de eliminación con los siguientes:
     
        https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/firewallRules/{rule-name}?api-version={api-version}


## <a name="manage-firewall-rules-using-the-rest-api"></a>Administrar las reglas de firewall con la API de REST

* [Crear o actualizar la regla de Firewall](https://msdn.microsoft.com/library/azure/mt445501.aspx)
* [Eliminar regla de Firewall](https://msdn.microsoft.com/library/azure/mt445502.aspx)
* [Obtener la regla de Firewall](https://msdn.microsoft.com/library/azure/mt445503.aspx)
* [Lista de todas las reglas de Firewall](https://msdn.microsoft.com/library/azure/mt604478.aspx)
 
## <a name="next-steps"></a>Pasos siguientes

Para saber cómo una artículo sobre cómo usar Transact-SQL para crear reglas de firewall de nivel de servidor y de nivel de base de datos, vea [reglas de firewall de nivel de base de datos y servidor de configurar la base de datos de SQL de Azure mediante T SQL](sql-database-configure-firewall-settings-tsql.md). 

Para artículos sobre la creación de reglas de firewall de nivel de servidor con otros métodos, consulte: 

- [Configurar las reglas de firewall de nivel de servidor de base de datos de SQL Azure con el portal de Azure](sql-database-configure-firewall-settings.md)
- [Configurar las reglas de firewall de nivel de servidor de base de datos de SQL Azure con PowerShell](sql-database-configure-firewall-settings-powershell.md)

Para obtener un tutorial acerca de cómo crear una base de datos, vea [crear una base de datos SQL en minutos con el portal de Azure](sql-database-get-started.md).
Para obtener ayuda para la conexión a una base de datos de SQL Azure de Abrir origen o aplicaciones de terceros, vea [ejemplos de código para la base de datos SQL de inicio de rápido de cliente](https://msdn.microsoft.com/library/azure/ee336282.aspx).
Para comprender cómo navegar a bases de datos, consulte [Administrar la seguridad de access e inicio de sesión de base de datos](https://msdn.microsoft.com/library/azure/ee336235.aspx).


## <a name="additional-resources"></a>Recursos adicionales

- [Proteger la base de datos](sql-database-security.md)
- [Centro de seguridad de base de datos SQL Azure y motor de base de datos SQL Server](https://msdn.microsoft.com/library/bb510589)

<!--Image references-->
[1]: ./media/sql-database-configure-firewall-settings/AzurePortalBrowseForFirewall.png
[2]: ./media/sql-database-configure-firewall-settings/AzurePortalFirewallSettings.png
<!--anchors-->

 
