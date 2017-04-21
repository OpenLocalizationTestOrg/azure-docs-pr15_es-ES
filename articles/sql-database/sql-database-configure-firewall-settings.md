<properties
    pageTitle="Configurar una regla de firewall de nivel de servidor de base de datos SQL | Microsoft Azure"
    description="Obtenga información sobre cómo configurar el firewall para las direcciones IP que acceso el servidor de SQL Azure."
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
    ms.author="rickbyh;carlrab"/>


# <a name="configure-an-azure-sql-database-server-level-firewall-rule-using-the-azure-portal"></a>Configurar una regla de firewall de nivel de servidor de base de datos de SQL Azure con el Portal de Azure


> [AZURE.SELECTOR]
- [Información general](sql-database-firewall-configure.md)
- [Portal de Azure](sql-database-configure-firewall-settings.md)
- [TSQL](sql-database-configure-firewall-settings-tsql.md)
- [PowerShell](sql-database-configure-firewall-settings-powershell.md)
- [API DE REST](sql-database-configure-firewall-settings-rest.md)

Servidor SQL Azure usa las reglas de firewall para permitir las conexiones a los servidores y bases de datos. Puede definir configuraciones de firewall de nivel de base de datos y servidor para el patrón o una base de datos de usuario en el servidor lógico del servidor de SQL Azure selectivamente permitir acceso a la base de datos. Este tema describen las reglas de firewall de nivel de servidor.

> [AZURE.IMPORTANT] Para permitir que las aplicaciones de Azure conectarse al servidor de SQL Azure, deben estar habilitadas conexiones de Azure. Para comprender cómo el firewall reglas de trabajo, vea [cómo configurar un firewall de servidor de SQL Azure \- Introducción](sql-database-firewall-configure.md). Si va a realizar conexiones dentro del límite de nube de Azure, debe abrir algunos puertos TCP adicionales. Para obtener más información, consulte la **V12 de SQL base de datos: fuera vs dentro de** sección de [puertos más allá de 1433 para ADO.NET 4.5 y V12 de base de datos de SQL](sql-database-develop-direct-route-ports-adonet-v12.md)

**Recomendación:** Usar reglas de firewall de nivel de servidor para los administradores y cuando tiene muchas bases de datos que tienen los mismos requisitos de acceso y no desea perder tiempo configurar de forma individual cada base de datos. Microsoft recomienda usar las reglas de firewall de nivel de base de datos siempre que sea posible, para mejorar la seguridad y para hacer que la base de datos sea más portátil.

[AZURE.INCLUDE [Create SQL Database database](../../includes/sql-database-create-new-server-firewall-portal.md)]

## <a name="manage-existing-server-level-firewall-rules-through-the-azure-portal"></a>Administrar las reglas de firewall de nivel de servidor existentes a través del portal de Azure

Repita los pasos para administrar las reglas de firewall de nivel de servidor.

- Para agregar el equipo actual, haga clic en Agregar IP del cliente.
- Para agregar direcciones IP adicionales, escriba el nombre de la regla, la dirección IP inicial y la dirección IP final.
- Para modificar una regla existente, haga clic en cualquiera de los campos de la regla y modificar.
- Para eliminar una regla existente, desplace el puntero sobre la regla hasta que aparezca la X del final de la fila. Haga clic en X para quitar la regla.

Haga clic en **Guardar** para guardar los cambios.

## <a name="next-steps"></a>Pasos siguientes

Para saber cómo una artículo sobre cómo usar Transact-SQL para crear reglas de firewall de nivel de servidor y de nivel de base de datos, vea [reglas de firewall de nivel de base de datos y servidor de configurar la base de datos de SQL de Azure mediante T SQL](sql-database-configure-firewall-settings-tsql.md). 

Para artículos sobre la creación de reglas de firewall de nivel de servidor con otros métodos, consulte: 

- [Configurar las reglas de firewall de nivel de servidor de base de datos de SQL Azure con PowerShell](sql-database-configure-firewall-settings-powershell.md)
- [Configurar las reglas de firewall de nivel de servidor de base de datos de SQL Azure con la API de REST](sql-database-configure-firewall-settings-rest.md)

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

 
