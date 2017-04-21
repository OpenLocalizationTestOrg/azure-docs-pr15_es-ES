<properties 
    pageTitle="Agregar un fragmentar con herramientas de base de datos elástico | Microsoft Azure" 
    description="Describe cómo usar elástico API de escala para agregar nuevos shards a un fragmentar conjunto." 
    services="sql-database" 
    documentationCenter="" 
    manager="jhubbard" 
    authors="ddove" 
    editor=""/>

<tags 
    ms.service="sql-database" 
    ms.workload="sql-database" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="05/27/2016" 
    ms.author="ddove"/>

# <a name="adding-a-shard-using-elastic-database-tools"></a>Agregar un fragmentar con herramientas de base de datos elásticas

## <a name="to-add-a-shard-for-a-new-range-or-key"></a>Para agregar un partes de un rango nuevo o una clave  

Aplicaciones a menudo necesitan simplemente agregar nuevos shards para controlar los datos que se esperan de nuevas claves o intervalos de claves para un mapa de partes que ya existe. Por ejemplo, una aplicación sharded por inquilino ID quizá deba aprovisionar un nuevo fragmentar para un nuevo inquilino, o mensual sharded de datos que tenga un nuevo fragmentar aprovisionado antes del inicio de nuevo cada mes. 

Si el nuevo rango de valores de clave no es parte de una asignación existente, es muy sencillo agregar el nuevo fragmentar y asociar la clave nueva o el rango a ese fragmentar. 

### <a name="example--adding-a-shard-and-its-range-to-an-existing-shard-map"></a>Ejemplo: agregar un fragmentar y su intervalo a un mapa de fragmentar existente
Este ejemplo usa la [TryGetShard](https://msdn.microsoft.com/library/azure/dn823929.aspx) la [CreateShard](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmap.createshard.aspx), métodos de [CreateRangeMapping](https://msdn.microsoft.com/library/azure/dn807221.aspx#M:Microsoft.Azure.SqlDatabase.ElasticScale.ShardManagement.RangeShardMap`1.CreateRangeMapping(Microsoft.Azure.SqlDatabase.ElasticScale.ShardManagement.RangeMappingCreationInfo{`0})) y crea una instancia de la clase [ShardLocation](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardlocation.shardlocation.aspx#M:Microsoft.Azure.SqlDatabase.ElasticScale.ShardManagement.ShardLocation.) . En el ejemplo siguiente, se han creado una base de datos denominada **sample_shard_2** y todos los objetos de esquema necesarios en su interior para mantener rango [300, 400).  

    // sm is a RangeShardMap object.
    // Add a new shard to hold the range being added. 
    Shard shard2 = null; 

    if (!sm.TryGetShard(new ShardLocation(shardServer, "sample_shard_2"),out shard2)) 
    { 
        shard2 = sm.CreateShard(new ShardLocation(shardServer, "sample_shard_2"));  
    } 

    // Create the mapping and associate it with the new shard 
    sm.CreateRangeMapping(new RangeMappingCreationInfo<long> 
                            (new Range<long>(300, 400), shard2, MappingStatus.Online)); 


Como alternativa, puede usar Powershell para crear un nuevo administrador de mapa de partes. Un ejemplo está disponible [aquí](https://gallery.technet.microsoft.com/scriptcenter/Azure-SQL-DB-Elastic-731883db).
## <a name="to-add-a-shard-for-an-empty-part-of-an-existing-range"></a>Para agregar un partes de una parte vacía de un rango existente  

En algunos casos, tiene ya asignado un rango a un fragmentar y parcialmente rellenado con datos, pero ahora desea datos próximas pueden dirigirse a un fragmentar diferente. Por ejemplo, puede fragmentar por intervalo de día y tiene 50 días ya asignadas a un fragmentar pero día 24, desea datos en un fragmentar diferentes en el futuro. El de la base de datos elástico [herramienta de combinación de división](sql-database-elastic-scale-overview-split-and-merge.md) puede realizar esta operación, pero si el movimiento de datos no es necesario (por ejemplo, de datos para el rango de días [25, 50), es decir, no existe aún día 25 inclusive en modo exclusivo, 50) puede realizar utilizando las API de administración de mapa fragmentar directamente.

### <a name="example-splitting-a-range-and-assigning-the-empty-portion-to-a-newly-added-shard"></a>Ejemplo: dividir un rango y asignar la parte vacía a un fragmentar recién agregado

Se han creado una base de datos denominada "sample_shard_2" y todos los objetos de esquema necesarios dentro de ella.  

 
    // sm is a RangeShardMap object.
    // Add a new shard to hold the range we will move 
    Shard shard2 = null; 

    if (!sm.TryGetShard(new ShardLocation(shardServer, "sample_shard_2"),out shard2)) 
    { 
    
        shard2 = sm.CreateShard(new ShardLocation(shardServer, "sample_shard_2"));  
    } 

    // Split the Range holding Key 25 

    sm.SplitMapping(sm.GetMappingForKey(25), 25); 

    // Map new range holding [25-50) to different shard: 
    // first take existing mapping offline 
    sm.MarkMappingOffline(sm.GetMappingForKey(25)); 
    // now map while offline to a different shard and take online 
    RangeMappingUpdate upd = new RangeMappingUpdate(); 
    upd.Shard = shard2; 
    sm.MarkMappingOnline(sm.UpdateMapping(sm.GetMappingForKey(25), upd)); 

**Importante**: usar esta técnica sólo si está seguro de que el rango de la asignación actualizada está vacía.  Los métodos anteriores no revisar datos para el rango que se mueve, por lo que es mejor incluir comprobaciones en el código.  Si existen filas en el rango que se mueve, la distribución de datos reales no coincidirán el mapa fragmentar actualizado. Use la [herramienta de combinación de división](sql-database-elastic-scale-overview-split-and-merge.md) para realizar la operación en su lugar en estos casos.  


[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]
 
