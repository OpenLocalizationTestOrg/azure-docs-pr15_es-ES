<properties
    pageTitle="Replicación de base de datos global DocumentDB | Microsoft Azure"
    description="Obtenga información sobre cómo administrar la replicación de la cuenta DocumentDB a través del portal de Azure global."
    services="documentdb"
    keywords="base de datos global, replicación"
    documentationCenter=""
    authors="mimig1"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="documentdb"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/17/2016"
    ms.author="mimig"/>

# <a name="how-to-perform-documentdb-global-database-replication-using-the-azure-portal"></a>Cómo llevar a cabo la replicación de base de datos global DocumentDB con el portal de Azure

Aprenda a usar el portal de Azure replicar datos en varias áreas para la disponibilidad de datos de Azure DocumentDB global.

Para obtener información acerca de la base de datos global cómo funciona la replicación en DocumentDB, vea [distribuir datos globalmente con DocumentDB](documentdb-distribute-data-globally.md). Para obtener información acerca de cómo realizar replicación global de la base de datos mediante programación, vea [desarrollar con cuentas de región múltiples DocumentDB](documentdb-developing-with-multiple-regions.md).

> [AZURE.NOTE] Distribución global de bases de datos de DocumentDB está disponible de forma general y automáticamente habilitado para las cuentas de DocumentDB recién creadas. Estamos trabajando para habilitar la distribución global en todas las cuentas existentes, pero mientras tanto, si desea que la distribución global habilitado en su cuenta, por favor, [póngase en contacto con soporte técnico](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) y se deberá habilitarlo para ahora.

## <a id="addregion"></a>Agregar regiones global de la base de datos

DocumentDB está disponible en la mayoría de [las regiones Azure] [azureregions]. Después de seleccionar el nivel de coherencia predeterminada para su cuenta de base de datos, puede asociar una o más regiones (dependiendo de su elección necesidades de distribución globales y de nivel de coherencia de forma predeterminada).

1. En el [portal de Azure](https://portal.azure.com/), en la Jumpbar, haga clic en **Cuentas de DocumentDB**.
2. En el módulo **DocumentDB cuenta** , seleccione la cuenta de base de datos para modificar.
3. En el módulo de cuenta, haga clic en **duplicar datos de forma global** en el menú.
4. En el módulo **replicar datos globalmente** , seleccione las regiones para agregar o quitar y, a continuación, haga clic en **Guardar**. Hay un costo para agregar regiones, consulte el artículo [distribuir datos globalmente con DocumentDB](documentdb-distribute-data-globally.md) para obtener más información o la [página de precios](https://azure.microsoft.com/pricing/details/documentdb/) .

    ![Haga clic en las áreas de la asignación que desea agregar o quitar][1]

### <a name="selecting-global-database-regions"></a>Selección de las zonas de la base de datos global

Al configurar dos o más regiones, se recomienda que las regiones están seleccionadas basado en los pares de región se describe en el [empresa continuidad y recuperación ante desastres (BCDR): regiones de Azure emparejadas]  [ bcdr] artículo.

Específicamente, al configurar para varias regiones, asegúrese de seleccionar el mismo número de regiones (+/-1 de par o impar) de cada una de las columnas de la región pareja. Por ejemplo, si desea implementar en cuatro áreas de Estados Unidos, seleccione dos regiones de Estados Unidos de la columna izquierda y dos desde la derecha. Por lo tanto, los siguientes sería un conjunto apropiado: oeste de Estados Unidos, Estados Unidos oriental, Norte Central de EE y sur Central de EE.

Esta guía es importante seguir cuando solo dos regiones estén configuradas para escenarios de recuperación de desastres. Para más de dos regiones, seguir esta guía es una buena práctica, pero no críticas como algunas de las regiones seleccionadas siga este emparejamiento.

<!---
## <a id="selectwriteregion"></a>Select the write region

While all regions associated with your DocumentDB database account can serve reads (both, single item as well as multi-item paginated reads) and queries, only one region can actively receive the write (insert, upsert, replace, delete) requests. To set the active write region, do the following  


1. In the **DocumentDB Account** blade, select the database account to modify.
2. In the account blade, if the **All Settings** blade is not already opened, click **All Settings**.
3. In the **All Settings** blade, click **Write Region Priority**.
    ![Change the write region under DocumentDB Account > Settings > Add/Remove Regions][2]
4. Click and drag regions to order the list of regions. The first region in the list of regions is the active write region.
    ![Change the write region by reordering the region list under DocumentDB Account > Settings > Change Write Regions][3]
-->

## <a id="next"></a>Pasos siguientes

Obtenga información sobre cómo administrar la coherencia de su cuenta de forma global replicada lea [niveles de coherencia en DocumentDB](documentdb-consistency-levels.md).

Para obtener información acerca de la base de datos global cómo funciona la replicación en DocumentDB, vea [distribuir datos globalmente con DocumentDB](documentdb-distribute-data-globally.md). Para obtener información acerca de cómo replicar datos en varias áreas de mediante programación, vea [desarrollar con cuentas de región múltiples DocumentDB](documentdb-developing-with-multiple-regions.md).

<!--Image references-->
[1]: ./media/documentdb-portal-global-replication/documentdb-add-region.png
[2]: ./media/documentdb-portal-global-replication/documentdb_change_write_region-1.png
[3]: ./media/documentdb-portal-global-replication/documentdb_change_write_region-2.png

<!--Reference style links - using these makes the source content way more readable than using inline links-->
[bcdr]: https://azure.microsoft.com/documentation/articles/best-practices-availability-paired-regions/
[consistency]: https://azure.microsoft.com/documentation/articles/documentdb-consistency-levels/
[azureregions]: https://azure.microsoft.com/en-us/regions/#services
[offers]: https://azure.microsoft.com/en-us/pricing/details/documentdb/
