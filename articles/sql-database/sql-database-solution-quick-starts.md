<properties
   pageTitle="Inicio rápido de solución de base de datos SQL Azure | Microsoft Azure"
   description="Obtenga más información sobre las soluciones de base de datos SQL Azure"
   services="sql-database"
   documentationCenter=""
   authors="CarlRabeler"
   manager="jhubbard"
   editor=""/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="sqldb-quickstart"
   ms.date="09/06/2016"
   ms.author="carlrab"/>

# <a name="explore-azure-sql-database-solution-quick-starts"></a>Explorar el inicio rápido de solución de base de datos SQL Azure

Este artículo contiene información general sobre la inicia rápida de Azure SQL base de datos de solución. Estos tutoriales rápidos se encuentran en el repositorio de muestras GitHub SQL Server y se muestra el uso de la base de datos SQL en una solución completa basada en escenarios del mundo real. Para simples tutoriales paso a paso que muestran el uso de una determinada característica de base de datos de SQL, vea [tutoriales de base de datos de SQL de Azure explorar](sql-database-explore-tutorials.md).

## <a name="try-the-wingtiptickets-demo-and-hands-on-lab"></a>Pruebe la demostración de WingTipTickets y práctica

La demostración de [WingTipTickets de base de datos de SQL Azure](https://github.com/microsoft/wingtiptickets) práctica se muestra una aplicación de ejemplo basadas en búsquedas de Azure que se usa para vender vales de combinación y base de datos de SQL Azure.


## <a name="collect-and-monitor-resource-usage-data-across-multiple-pools"></a>Recopilar y supervisar los datos de uso de recursos entre varios grupos

[Solución de inicio rápido: telemetría grupo elástico con PowerShell](https://github.com/Microsoft/sql-server-samples/tree/master/samples/manage/azure-sql-db-elastic-pools) proporciona una solución para recopilar y supervisar el uso de recursos de base de datos SQL en varios grupos de una suscripción. Cuando tiene un gran número de bases de datos en una suscripción, es complejo para supervisar cada grupo elástico por separado.

Para resolver este problema, puede combinar los cmdlets de PowerShell de base de datos de SQL y consultas de SQL T para recopilar datos de uso de recursos de varios grupos y sus bases de datos. Esto le permite supervisar y analizar el uso de recursos más eficazmente.

Este inicio rápido, se proporciona un conjunto de secuencias de comandos de PowerShell y consultas de SQL T junto con la documentación sobre lo que hace la solución y cómo implementarlo.

## <a name="get-started-with-elastic-database-in-an-saas-scenario"></a>Introducción a elásticos de la base de datos en un escenario de SaaS

 [Solución de inicio rápido: panel personalizado de grupo elástica para SaaS](https://github.com/Microsoft/sql-server-samples/tree/master/samples/manage/azure-sql-db-elastic-pools-custom-dashboard) proporciona una solución para un escenario de Software como un-solución-(SaaS) que aprovecha la característica de base de datos elástico de base de datos de SQL para proporcionar rentable y scalable de base de datos back-end para una aplicación de SaaS.

En esta solución le guiará a través de la implementación de una aplicación web. Esta aplicación web le permite visualizar la carga que se crea en una base de datos elástico por un generador de carga que utiliza un panel personalizado complementaria el portal de Azure.

Este inicio rápido le proporciona un generador de carga y la supervisión Online junto con la documentación sobre lo que hace la aplicación y cómo usarla.

## <a name="create-an-azure-sql-database-by-using-code-first-development-and-the-entity-framework"></a>Crear una base de datos de SQL Azure con desarrollo Code First y el marco de la entidad

El ejemplo y vídeo en [El primero de código para una nueva base de datos](https://msdn.microsoft.com/data/jj193542.aspx) proporciona una introducción al desarrollo de Code First que dirige a una nueva base de datos. Este escenario dirige a una base de datos que no existe, pero que se creará primero el código. Como alternativa, el escenario crea una base de datos vacía al que Code First agrega nuevas tablas.

Código primero le permite definir el modelo mediante el uso de clases C# o Visual Basic. NET. Puede realizar configuración adicional opcional mediante atributos en las clases y propiedades o mediante una API fluida.

## <a name="integrate-elastic-database-tools-into-an-entity-framework-application"></a>Integrar herramientas de base de datos elásticos en una aplicación de marco de la entidad

La [biblioteca de cliente de base de datos elástico con entidad Framework](sql-database-elastic-scale-use-entity-framework-applications-visual-studio.md) muestra los cambios que debe realizar una aplicación de marco de entidad integrar con [Herramientas de base de datos elástico](sql-database-elastic-scale-get-started.md). El foco está en Redactar [fragmentar asignar la administración](sql-database-elastic-scale-shard-map-management.md) y el [enrutamiento según los datos](sql-database-elastic-scale-data-dependent-routing.md) con el código de entidad Framework primer enfoque.

El [Código de primera a una muestra de base de datos nueva para EF](http://msdn.microsoft.com/data/jj193542.aspx) sirve como ejemplo a lo largo de este ejemplo. El código de ejemplo que acompaña a este documento forma parte del conjunto de herramientas elásticos de la base de datos de ejemplos en los ejemplos de código de Visual Studio.

## <a name="integrate-elastic-database-tools-with-row-level-security"></a>Integrar herramientas de base de datos elástico con seguridad a nivel de fila

[Aplicaciones multiempresa con herramientas de base de datos elásticas y seguridad a nivel de fila](sql-database-elastic-tools-multi-tenant-row-level-security.md) muestra los cambios que debe realizar en una aplicación de marco de entidad integrar [Herramientas de base de datos elástico](sql-database-elastic-scale-get-started.md) con [seguridad a nivel de fila](https://msdn.microsoft.com/library/dn765131). Este ejemplo muestra cómo usar estas tecnologías juntos para crear una aplicación con un nivel de datos de gran escalabilidad que admita shards multiempresa.

Hacer esto mediante SqlClient ADO.NET o marco de la entidad. En este ejemplo extiende la [biblioteca de cliente de base de datos elástico con Framework entidad](sql-database-elastic-scale-use-entity-framework-applications-visual-studio.md) agregando compatibilidad para las bases de datos de multiempresa fragmentar.
Crea una aplicación de consola simple para la creación de blogs y publicaciones, con cuatro inquilinos y bases de datos de dos partes multiempresa.

## <a name="create-online-surveys-with-the-tailspin-surveys-application"></a>Crear encuestas en línea con la aplicación Tailspin encuestas

Esta [aplicación de ejemplo de encuestas Tailspin](https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps/blob/master/docs/running-the-app.md) es una aplicación de web multiempresa denominada encuestas, que permite a los usuarios crear encuestas en línea. El ejemplo resuelve algunos problemas claves acerca de cómo administrar identidades de usuario en una aplicación multiempresa, incluida la suscripción, autenticación, autorización y funciones de aplicación.

## <a name="learn-about-the-latest-security-features-of-sql-database-with-the-contoso-clinic-demo-application"></a>Obtenga más información sobre las últimas características de seguridad de base de datos de SQL con la aplicación de demostración de la sesión de Contoso

Esta [aplicación de demostración de la sesión de Contoso](https://github.com/Microsoft/azure-sql-security-sample) presenta las últimas características de seguridad de base de datos de SQL.

## <a name="next-steps"></a>Pasos siguientes

[Explorar los tutoriales de base de datos de SQL Azure](sql-database-explore-tutorials.md)
