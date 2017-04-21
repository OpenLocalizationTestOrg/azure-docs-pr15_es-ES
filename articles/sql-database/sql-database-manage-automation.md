<properties
    pageTitle="Administrar bases de datos de SQL Azure mediante la automatización de Azure | Microsoft Azure"
    description="Obtenga información acerca de cómo puede utilizar el servicio de automatización de Azure para administrar bases de datos de SQL Azure en escala."
    services="sql-database, automation"
    documentationCenter=""
    authors="jodoglevy"
    manager="jhubbard"
    editor="monicar"/>

<tags
    ms.service="sql-database"
    ms.workload="data-management"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="05/26/2016"
    ms.author="jolevy"/>



#<a name="managing-azure-sql-databases-using-azure-automation"></a>Administrar bases de datos de SQL Azure mediante la automatización de Azure

Esta guía presentan el servicio de automatización de Azure y cómo puede utilizarse para simplificar la administración de las bases de datos de SQL Azure.


## <a name="what-is-azure-automation"></a>¿Qué es la automatización de Azure?

[Automatización de Azure](https://azure.microsoft.com/services/automation/) es un servicio de Azure para simplificar la administración de la nube mediante automatización de procesos. Mediante la automatización de Azure, se pueden automatizar tareas de larga duración, manuales, errores y repiten con frecuencia para aumentar la confiabilidad, eficiencia y tiempo de valor para su organización.

Automatización de Azure proporciona un motor de ejecución de flujo de trabajo altamente confiable y altamente disponibles escala para satisfacer sus necesidades a medida que crece su organización. En automatización de Azure, procesos pueden ser dio manualmente, parte 3 º sistemas o intervalos programados para que las tareas que se producen exactamente cuando sea necesario.

Reducir la sobrecarga operativa y liberar TI / DevOps personal para centrarse en el trabajo que agrega empresarial valor al mover las tareas de administración de la nube se ejecute automáticamente mediante la automatización de Azure.


## <a name="how-can-azure-automation-help-manage-azure-sql-databases"></a>¿Cómo puede automatización de Azure ayudar a administrar bases de datos de SQL Azure?

Base de datos de SQL Azure pueden administrarse en Azure automatización mediante el uso de los [cmdlets de PowerShell de base de datos de SQL Azure](https://msdn.microsoft.com/library/dn546723.aspx) que están disponibles en las [Herramientas de PowerShell de Azure](https://msdn.microsoft.com/library/azure/jj156055.aspx). Automatización de Azure tiene estos cmdlets de PowerShell de base de datos de SQL Azure disponibles fuera del cuadro, para que pueda realizar todas las tareas de administración de base de datos SQL en el servicio. También puede emparejar estos cmdlets en automatización de Azure con los cmdlets de otros servicios de Azure, para automatizar tareas complejas en servicios de Azure y 3er sistemas de terceros.

Automatización de Azure también tiene la capacidad de comunicarse con los servidores de SQL Server directamente, emitir comandos SQL con PowerShell.

La [Galería de automatización de Azure runbook](https://azure.microsoft.com/blog/2014/10/07/introducing-the-azure-automation-runbook-gallery/) contiene una variedad de runbooks de comunidad y grupo de producto para empezar a automatizar la administración de bases de datos de SQL Azure, otros servicios de Azure y 3er sistemas de terceros. Galería runbooks incluyen:

 * [Ejecutar consultas SQL en una base de datos de SQL Server](https://gallery.technet.microsoft.com/scriptcenter/How-to-use-a-SQL-Command-be77f9d2)
 * [Escalar verticalmente (hacia arriba o hacia abajo) una base de datos de SQL Azure en una programación](https://gallery.technet.microsoft.com/scriptcenter/Azure-SQL-Database-e957354f)
 * [Truncar una tabla SQL si su base de datos aproxima a su tamaño máximo](https://gallery.technet.microsoft.com/scriptcenter/Azure-Automation-Your-SQL-30f8736b)
 * [Si están muy fragmentar los índices de tablas en una base de datos de SQL Azure](https://gallery.technet.microsoft.com/scriptcenter/Indexes-tables-in-an-Azure-73a2a8ea)

## <a name="next-steps"></a>Pasos siguientes

Ahora que ha aprendido los conceptos básicos de automatización de Azure y cómo puede utilizarse para administrar bases de datos de SQL Azure, siga estos vínculos para obtener más información acerca de automatización de Azure.

- [Información general sobre automatización de Azure](../automation/automation-intro.md)
- [Mi primera runbook](../automation/automation-first-runbook-graphical.md)
- [Mapa de aprendizaje de automatización de Azure](https://azure.microsoft.com/documentation/learning-paths/automation/)
- [Automatización de Azure: El Agente SQL en la nube](https://azure.microsoft.com/blog/2014/06/26/azure-automation-your-sql-agent-in-the-cloud/) 
 
