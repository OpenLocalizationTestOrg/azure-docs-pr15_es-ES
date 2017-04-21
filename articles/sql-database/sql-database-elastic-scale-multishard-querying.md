<properties 
    pageTitle="Consulta de varias partes | Microsoft Azure" 
    description="Ejecutar consultas a través de shards mediante la biblioteca de cliente elásticos de la base de datos." 
    services="sql-database" 
    documentationCenter="" 
    manager="jhubbard" 
    authors="torsteng" 
    editor=""/>

<tags 
    ms.service="sql-database" 
    ms.workload="sql-database" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="04/12/2016" 
    ms.author="torsteng"/>

# <a name="multi-shard-querying"></a>Consulta de varias partes

## <a name="overview"></a>Información general

Con las [Herramientas de base de datos elástico](sql-database-elastic-scale-introduction.md), puede crear soluciones sharded de la base de datos. **Consultas de varias partes** se utiliza para tareas como colección o informes de datos que requieren la ejecución de una consulta que se estira a través de varias shards. (Esto es distinto a [enrutamiento según los datos](sql-database-elastic-scale-data-dependent-routing.md), que realiza todo el trabajo en una sola fragmentar.) 

## <a name="overview"></a>Información general

1. Obtener una [**RangeShardMap**](https://msdn.microsoft.com/library/azure/dn807318.aspx) o [**ListShardMap**](https://msdn.microsoft.com/library/azure/dn807370.aspx) con la [**TryGetRangeShardMap**](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.trygetrangeshardmap.aspx), el [**TryGetListShardMap**](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.trygetlistshardmap.aspx)o el método [**GetShardMap**](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.getshardmap.aspx) . Consulte [**crear un ShardMapManager**](sql-database-elastic-scale-shard-map-management.md#constructing-a-shardmapmanager) y [**obtener un RangeShardMap o ListShardMap**](sql-database-elastic-scale-shard-map-management.md#get-a-rangeshardmap-or-listshardmap).
2. Crear un objeto **[MultiShardConnection](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.query.multishardconnection.aspx)** .
2. Crear un **[MultiShardCommand](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.query.multishardcommand.aspx)**. 
3. Establezca la **[propiedad CommandText](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.query.multishardcommand.commandtext.aspx#P:Microsoft.Azure.SqlDatabase.ElasticScale.Query.MultiShardCommand.CommandText)** en un comando SQL T.
3. Ejecute el comando llamando al **[método ExecuteReader](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.query.multishardcommand.executereader.aspx)**.
4. Ver los resultados mediante la **[clase MultiShardDataReader](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.query.multisharddatareader.aspx)**. 

## <a name="example"></a>Ejemplo

El código siguiente muestra el uso de la consulta de varias partes con un determinado **ShardMap** denominado *myShardMap*. 

    using (MultiShardConnection conn = new MultiShardConnection( 
                                        myShardMap.GetShards(), 
                                        myShardConnectionString) 
          ) 
    { 
    using (MultiShardCommand cmd = conn.CreateCommand())
           { 
            cmd.CommandText = "SELECT c1, c2, c3 FROM ShardedTable"; 
            cmd.CommandType = CommandType.Text; 
            cmd.ExecutionOptions = MultiShardExecutionOptions.IncludeShardNameColumn; 
            cmd.ExecutionPolicy = MultiShardExecutionPolicy.PartialResults; 

            using (MultiShardDataReader sdr = cmd.ExecuteReader()) 
                { 
                    while (sdr.Read())
                        { 
                            var c1Field = sdr.GetString(0); 
                            var c2Field = sdr.GetFieldValue<int>(1); 
                            var c3Field = sdr.GetFieldValue<Int64>(2);
                        } 
                } 
           } 
    } 

 
Una diferencia clave es la creación de conexiones de varias partes. Donde **SqlConnection** funciona en una base de datos, la **MultiShardConnection** toma una ***colección de shards*** como entrada. Rellenar la colección de shards de un mapa de partes. A continuación, se ejecuta la consulta en la colección de shards con semántica **UNION ALL** ensamblar un único resultado general. Opcionalmente, el nombre de las partes donde se origina la fila se puede agregar a la salida mediante la propiedad **ExecutionOptions** en el comando. 

Tenga en cuenta la llamada a **myShardMap.GetShards()**. Este método recupera todos los shards desde el mapa de partes y proporciona una forma sencilla de ejecutar una consulta en todas las bases de datos pertinentes. La colección de shards para una consulta de varias partes puede refinar aún más realizando una consulta LINQ sobre la colección devueltos por la llamada a **myShardMap.GetShards()**. En combinación con la directiva de resultados parciales, la capacidad actual en consultas de varias partes se ha diseñado para funcionar bien para decenas hasta cientos de shards.

Una limitación con una consulta de varias partes actualmente es la falta de validación de shards y shardlets que se consulta. Mientras enrutamiento según los datos comprueba que un determinado fragmentar es parte de la asignación de fragmentar en el momento de la consulta, las consultas de varias partes no realizan esta comprobación. Esto puede llevar a las consultas de varias partes ejecuta en bases de datos que se han quitado de la asignación de partes.

## <a name="multi-shard-queries-and-split-merge-operations"></a>Consultas de varias partes y las operaciones de combinación de división

Las consultas de varias partes no compruebe si shardlets en la base de datos consultado participan en operaciones de combinación de división en curso. (Consulte [escala mediante la herramienta de combinación de la división de elásticos de la base de datos](sql-database-elastic-scale-overview-split-and-merge.md)). Esto puede provocar incoherencias en las filas de la misma shardlet mostrar varias bases de datos en la misma consulta de varias partes. Tenga en cuenta estas limitaciones y considere la posibilidad de purga operaciones de combinación de división en curso y los cambios en el mapa de fragmentar al realizar consultas de varias partes.

[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

## <a name="see-also"></a>Vea también
**[System.Data.SqlClient](http://msdn.microsoft.com/library/System.Data.SqlClient.aspx)** clases y métodos.


Administrar shards mediante la [biblioteca de cliente de base de datos elástico](sql-database-elastic-database-client-library.md). Incluye un espacio de nombres llamado [Microsoft.Azure.SqlDatabase.ElasticScale.Query](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.query.aspx) que proporciona la capacidad de varios shards con una sola consulta y el resultado de la consulta. Proporciona una abstracción de consulta a través de una colección de shards. También proporciona directivas de ejecución alternativo, resultados parciales en particular, para solucionar los errores al consultar sobre muchos shards.  

 