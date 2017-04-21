<properties
    pageTitle="Conectarse a la base de datos SQL con PHP en Windows | Microsoft Azure"
    description="Presenta un ejemplo de un programa PHP que se conecta a la base de datos de SQL Azure desde un cliente de Windows y proporciona vínculos a los componentes de software necesario necesarios el cliente."
    services="sql-database"
    documentationCenter=""
    authors="meet-bhagdev"
    manager="jhubbard"
    editor=""/>


<tags
    ms.service="sql-database"
    ms.workload="drivers"
    ms.tgt_pltfrm="na"
    ms.devlang="php"
    ms.topic="article"
    ms.date="10/03/2016"
    ms.author="meetb"/>


# <a name="connect-to-sql-database-by-using-php-on-windows"></a>Conectarse a la base de datos SQL con PHP en Windows


[AZURE.INCLUDE [sql-database-develop-includes-selector-language-platform-depth](../../includes/sql-database-develop-includes-selector-language-platform-depth.md)] 


Este tema muestra cómo puede conectarse a la base de datos de SQL Azure desde una aplicación de cliente escrita en PHP que se ejecuta en Windows.

## <a name="step-1--configure-development-environment"></a>Paso 1: Configurar el entorno de desarrollo

[Configurar el entorno de desarrollo para el desarrollo de PHP](https://msdn.microsoft.com/library/mt720663.aspx)

## <a name="step-2-create-a-sql-database"></a>Paso 2: Crear una base de datos SQL

Consulte la [página de introducción](sql-database-get-started.md) para obtener información sobre cómo crear una base de datos de ejemplo.  Es importante que siga la guía para crear una **plantilla de base de datos de AdventureWorks**. Los ejemplos que se muestra a continuación solo funcionan con el **esquema de AdventureWorks**.


## <a name="step-3-get-connection-details"></a>Paso 3: Obtener detalles de conexión

[AZURE.INCLUDE [sql-database-include-connection-string-details-20-portalshots](../../includes/sql-database-include-connection-string-details-20-portalshots.md)]


## <a name="step-4-run-sample-code"></a>Paso 4: Ejecutar el código de ejemplo

* [Prueba de concepto conectar a SQL con PHP](https://msdn.microsoft.com/library/mt720665.aspx)
* [Conectar resiliently SQL con PHP](https://msdn.microsoft.com/library/mt720667.aspx)


## <a name="next-steps"></a>Pasos siguientes

* Revise la [información general sobre el desarrollo de base de datos SQL](sql-database-develop-overview.md)
* Obtener más información sobre el [Controlador de PHP de Microsoft para SQL Server](https://msdn.microsoft.com/library/dn865013.aspx)
* Para obtener más información sobre la instalación de PHP y el uso, vea [Obtener acceso a SQL Server las bases de datos con PHP](http://social.technet.microsoft.com/wiki/contents/articles/1258.accessing-sql-server-databases-from-php.aspx).

## <a name="additional-resources"></a>Recursos adicionales 

* [Patrones de diseño para las aplicaciones de múltiples arrendatarios SaaS con base de datos SQL Azure](sql-database-design-patterns-multi-tenancy-saas-applications.md)
* Explore las [capacidades de base de datos de SQL](https://azure.microsoft.com/services/sql-database/)
