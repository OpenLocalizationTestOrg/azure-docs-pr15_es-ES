<properties
    pageTitle="Contadores de rendimiento para el administrador del mapa de partes"
    description="ShardMapManager clase y datos dependientes enrutamiento contadores de rendimiento"
    services="sql-database"
    documentationCenter=""
    manager="jhubbard"
    authors="SilviaDoomra"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.workload="sql-database"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="05/23/2016"
    ms.author="SilviaDoomra"/>

# <a name="performance-counters-for-shard-map-manager"></a>Contadores de rendimiento para el administrador del mapa de partes

Puede capturar el rendimiento de un [Administrador de mapa fragmentar](sql-database-elastic-scale-shard-map-management.md), especialmente cuando se utiliza el [enrutamiento según los datos](sql-database-elastic-scale-data-dependent-routing.md). Contadores se crean con métodos de la clase Microsoft.Azure.SqlDatabase.ElasticScale.Client.  

Contadores se utilizan para realizar un seguimiento del rendimiento de operaciones de [enrutamiento según los datos](sql-database-elastic-scale-data-dependent-routing.md) . Estos contadores están disponibles en el Monitor de rendimiento, en la categoría "Elástico base de datos: fragmentar administración".

**Para la versión más reciente:** Vaya a [Microsoft.Azure.SqlDatabase.ElasticScale.Client](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/). Vea también [actualizar una aplicación para usar la biblioteca de cliente elásticos de la base de datos más reciente](sql-database-elastic-scale-upgrade-client-library.md).

## <a name="prerequisites"></a>Requisitos previos

* Para crear la categoría de rendimiento y contadores, el usuario debe ser un elemento del grupo de **administradores** locales en el equipo que aloja la aplicación.  

* Para crear una instancia de contador de rendimiento y actualizar los contadores, el usuario debe ser miembro del grupo **administradores** o **Usuarios del Monitor** . 

## <a name="create-performance-category-and-counters"></a>Crear contadores y categorías de rendimiento 

Para crear los contadores, llame al método CreatePeformanceCategoryAndCounters de la [clase ShardMapManagmentFactory](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.aspx). Sólo un administrador puede ejecutar el método: 

    ShardMapManagerFactory.CreatePerformanceCategoryAndCounters()  

También puede usar [esta](https://gallery.technet.microsoft.com/scriptcenter/Elastic-DB-Tools-for-Azure-17e3d283) secuencia de comandos de PowerShell para ejecutar el método. El método crea los siguientes contadores de rendimiento:  

* **Asignaciones de intercambio en caché**: número de asignaciones en caché del mapa de partes.
*  **DDR operaciones/seg**: tasa de operaciones de enrutamiento dependientes de datos del mapa de partes. Este contador se actualiza cuando una llamada a [OpenConnectionForKey()](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmap.openconnectionforkey.aspx) resultados en una conexión correcta a las partes de destino. 
*  **Asignación de caché de búsqueda visitas/seg**: velocidad de las operaciones de búsqueda de caché correcta para las asignaciones en el mapa de partes. 
*  **Asignación de errores de caché de búsqueda por segundo**: velocidad de las operaciones de búsqueda de caché con error para las asignaciones en el mapa de partes.
*  **Asignaciones de agregan o actualizan en caché/seg**: velocidad a se agregan las asignaciones o actualizados en caché del mapa de partes. 
*  **Quitan las asignaciones de caché/seg**: tasa a la que se quitan las asignaciones de caché del mapa de partes. 

Contadores de rendimiento se crean para cada mapa fragmentar almacenados en caché por el proceso.  


## <a name="notes"></a>Notas
Los siguientes eventos desencadenan la creación de contadores de rendimiento:  

* Inicialización de la [ShardMapManager](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.aspx) con [carga inmediata](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerloadpolicy.aspx), si la ShardMapManager contiene cualquier mapas fragmentar. Se incluyen los [GetSqlShardMapManager](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.getsqlshardmapmanager.aspx?f=255&MSPPError=-2147217396#M:Microsoft.Azure.SqlDatabase.ElasticScale.ShardManagement.ShardMapManagerFactory.GetSqlShardMapManager%28System.String,Microsoft.Azure.SqlDatabase.ElasticScale.ShardManagement.ShardMapManagerLoadPolicy%29) y los métodos de [TryGetSqlShardMapManager](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.trygetsqlshardmapmanager.aspx) .
* Búsqueda correcta de un mapa de fragmentar (con [GetShardMap()](https://msdn.microsoft.com/library/azure/dn824215.aspx), [GetListShardMap()](https://msdn.microsoft.com/library/azure/dn824212.aspx) o [GetRangeShardMap()](https://msdn.microsoft.com/library/azure/dn824173.aspx)). 

* Creación correcta de mapa de fragmentar mediante CreateShardMap().

Los contadores de rendimiento se actualizarán todas las operaciones de caché realizadas en el mapa de partes y las asignaciones. Eliminación con éxito del mapa fragmentar mediante DeleteShardMap () reults eliminación de la instancia de contadores de rendimiento.  

## <a name="best-practices"></a>Prácticas recomendadas

* Creación de la categoría de rendimiento y contadores debe realizarse solo una vez antes de la creación del objeto ShardMapManager. Cada ejecución del comando CreatePerformanceCategoryAndCounters() borra los contadores anterior (perder notificados por todas las instancias de datos) y crea nuevos.  

* Instancias de contador de rendimiento creados por el proceso. Eliminación de las instancias de contadores de rendimiento se producirá cualquier bloqueo de la aplicación o eliminación de un mapa de partes de la memoria caché.  

### <a name="see-also"></a>Vea también

[Elástico información general de las características de base de datos](sql-database-elastic-scale-introduction.md)  

[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Anchors-->
<!--Image references-->

