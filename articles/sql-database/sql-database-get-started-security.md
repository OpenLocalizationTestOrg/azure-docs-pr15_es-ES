<properties
    pageTitle="Tutorial de base de datos SQL: Introducción a la seguridad"
    description="Aprenda a crear cuentas de usuario para acceder y administrar una base de datos."
    keywords=""
    services="sql-database"
    documentationCenter=""
    authors="CarlRabeler"
    manager="jhubbard"
    editor=""/>


<tags
    ms.service="sql-database"
    ms.workload="data-management"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="hero-article"
    ms.date="08/17/2016"
    ms.author="carlrab"/>

# <a name="sql-database-tutorial-create-sql-database-user-accounts-to-access-and-manage-a-database"></a>Tutorial de base de datos SQL: crear cuentas de usuario para acceder y administrar una base de datos de base de datos SQL


> [AZURE.SELECTOR]
- [Tutorial de introducción de Get](sql-database-get-started-security.md)
- [Conceder acceso](sql-database-manage-logins.md)

En este tutorial, aprenderá a utilizar SQL Server Management Studio (SSMS) para:

- Inicie sesión en con un inicio de sesión principal de nivel de servidor de base de datos de SQL.
- Crear una cuenta de usuario de la base de datos de SQL.
- Conceder a un usuario de base de datos SQL [db_owner permisos](https://msdn.microsoft.com/library/ms189121.aspx#Anchor_0).
- Conectarse a una base de datos SQL con una cuenta de usuario que no es una entidad de seguridad de nivel de servidor.

[AZURE.INCLUDE [Login](../../includes/azure-getting-started-portal-login.md)]


[AZURE.INCLUDE [Create SQL Database logical server](../../includes/sql-database-sql-server-management-studio-connect-server-principal.md)]


[AZURE.INCLUDE [Create SQL Database database](../../includes/sql-database-create-new-database-user.md)]


[AZURE.INCLUDE [Create SQL Database database](../../includes/sql-database-grant-database-user-dbo-permissions.md)]


[AZURE.INCLUDE [Create SQL Database database](../../includes/sql-database-sql-server-management-studio-connect-user.md)]


## <a name="next-steps"></a>Pasos siguientes
Ahora que ha completado este tutorial de base de datos SQL y creado una cuenta de usuario y permisos de usuario cuenta dbo, está listo para obtener más información sobre la [seguridad de la base de datos de SQL](sql-database-manage-logins.md).


