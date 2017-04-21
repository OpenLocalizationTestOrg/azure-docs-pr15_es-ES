<properties
    pageTitle="Conectarse a la base de datos SQL con Ruby | Microsoft Azure"
    description="Dar un ejemplo de código ideogramas y transcripción fonética que se puede ejecutar para conectarse a la base de datos de SQL Azure."
    services="sql-database"
    documentationCenter=""
    authors="ajlam"
    manager="jhubbard"
    editor=""/>


<tags
    ms.service="sql-database"
    ms.workload="drivers"
    ms.tgt_pltfrm="na"
    ms.devlang="ruby"
    ms.topic="article"
    ms.date="10/03/2016"
    ms.author="andrela"/>


# <a name="connect-to-sql-database-by-using-ruby"></a>Conectarse a la base de datos SQL con Ruby 

[AZURE.INCLUDE [sql-database-develop-includes-selector-language-platform-depth](../../includes/sql-database-develop-includes-selector-language-platform-depth.md)] 

Este tema muestra cómo conectarse y consultar una base de datos de SQL Azure con Ruby. Puede ejecutar este ejemplo de plataformas Windows, Ubuntu Linux o Mac.

## <a name="step-1-configure-development-environment"></a>Paso 1: Configurar el entorno de desarrollo

[Requisitos previos para usar el controlador de Ruby TinyTDS para SQL Server](https://msdn.microsoft.com/library/mt711041.aspx)

## <a name="step-2-create-a-sql-database"></a>Paso 2: Crear una base de datos SQL

Consulte la [página de introducción](sql-database-get-started.md) para obtener información sobre cómo crear una base de datos de ejemplo.  Es importante que siga la guía para crear una **plantilla de base de datos de AdventureWorks**. Los ejemplos que se muestra a continuación solo funcionan con el **esquema de AdventureWorks**.

## <a name="step-3-get-connection-details"></a>Paso 3: Obtener detalles de conexión

[AZURE.INCLUDE [sql-database-include-connection-string-details-20-portalshots](../../includes/sql-database-include-connection-string-details-20-portalshots.md)]

## <a name="step-4-run-sample-code"></a>Paso 4: Ejecutar el código de ejemplo

[Prueba de concepto conectar a SQL con Ruby](http://msdn.microsoft.com/library/mt715797.aspx)

## <a name="next-steps"></a>Pasos siguientes

* Revise la [información general sobre el desarrollo de base de datos SQL](sql-database-develop-overview.md)
* Obtener más información sobre el [Controlador de Ruby de Microsoft para SQL Server](https://msdn.microsoft.com/library/mt691981.aspx)

## <a name="additional-resources"></a>Recursos adicionales 

* [Patrones de diseño para las aplicaciones de múltiples arrendatarios SaaS con base de datos SQL Azure](sql-database-design-patterns-multi-tenancy-saas-applications.md)
* Explore las [capacidades de base de datos de SQL](https://azure.microsoft.com/services/sql-database/)
