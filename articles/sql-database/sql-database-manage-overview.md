<properties
    pageTitle="Información general: herramientas de administración para la base de datos SQL | Microsoft Azure"
    description="Compara las herramientas y opciones de administración de la base de datos de SQL Azure"
    services="sql-database"
    documentationCenter=""
    authors="stevestein"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.workload="data-management"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/24/2016"
    ms.author="sstein"/>

# <a name="overview-management-tools-for-sql-database"></a>Información general: herramientas de administración para base de datos de SQL

Este tema explora y compara las herramientas y opciones de administración de bases de datos de SQL Azure para que pueda elegir la herramienta adecuada para el trabajo, su empresa y. Elegir la herramienta adecuada depende de cuántas bases de datos administrar, la tarea y con qué frecuencia se realiza una tarea.

## <a name="azure-portal"></a>Portal de Azure

El [portal de Azure](https://portal.azure.com) es una aplicación basada en web donde puede crear, actualizar y eliminar bases de datos y servidores lógicos y supervisar la actividad de la base de datos. Esta herramienta es ideal si está comenzando a con Azure, administrar algunas bases de datos, o tiene que hacer algo rápidamente.

Para obtener más información sobre cómo usar el portal, vea [Administrar bases de datos de SQL con el portal de Azure](sql-database-manage-portal.md).

## <a name="sql-server-management-studio-and-sql-server-data-tools-in-visual-studio"></a>SQL Server Management Studio y herramientas de datos SQL Server en Visual Studio

SQL Server Management Studio (SSMS) y SQL Server Data Tools (SSDT) son las herramientas de cliente que se ejecutan en el equipo para administrar y desarrollar su base de datos en la nube. Si es programador familiarizado con Visual Studio u otros entornos de desarrollo integrado (IDE), [Pruebe a usar SSDT en Visual Studio](https://msdn.microsoft.com/library/mt204009.aspx). Muchos administradores de base de datos están familiarizados con SSMS, que se pueden utilizar con bases de datos de SQL Azure. [Descargar la versión más reciente de SSMS](https://msdn.microsoft.com/library/mt238290) y usar siempre la versión más reciente cuando se trabaja con la base de datos de SQL Azure. Para obtener más información sobre cómo administrar las bases de datos de SQL Azure con SSMS, vea [Administrar bases de datos de SQL con SSMS](sql-database-manage-azure-ssms.md).

> [AZURE.IMPORTANT] Usar siempre la última versión de [SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290) y [SQL Server Data Tools](https://msdn.microsoft.com/library/mt204009.aspx) permanezca sincronizado con las actualizaciones de Microsoft Azure y base de datos de SQL.


## <a name="powershell"></a>PowerShell

Puede usar PowerShell para administrar bases de datos y grupos de elásticos de la base de datos y para automatizar las implementaciones de Azure recursos. Microsoft recomienda esta herramienta para administrar un gran número de bases de datos y automatizar la implementación y los cambios de los recursos en un entorno de producción.

Para obtener más información, vea [Administrar bases de datos de SQL con PowerShell](sql-database-manage-powershell.md)

## <a name="elastic-database-tools"></a>Elásticos herramientas de base de datos
Use las herramientas de base de datos elástica para realizar acciones como 

* Ejecutar una secuencia de comandos SQL T con un conjunto de bases de datos mediante un [trabajo elástica](sql-database-elastic-jobs-overview.md)
* Mover bases de datos de varios inquilinos modelo a un modelo de inquilinos solo con la [herramienta de combinación de división](sql-database-elastic-scale-overview-split-and-merge.md)
* Administrar bases de datos en un solo inquilinos o un modelo inquilino múltiples mediante la [biblioteca de cliente de escala elástico](sql-database-elastic-database-client-library.md).
 

## <a name="additional-resources"></a>Recursos adicionales

- [Administrador de recursos de Azure](https://azure.microsoft.com/features/resource-manager/)
- [Automatización de Azure](https://azure.microsoft.com/documentation/services/automation/)
- [Programador de Azure](https://azure.microsoft.com/documentation/services/scheduler/)