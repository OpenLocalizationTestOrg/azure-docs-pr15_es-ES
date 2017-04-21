<properties
   pageTitle="Migrar bases de datos existentes a escalado | Microsoft Azure"
   description="Convertir bases de datos sharded para usar herramientas de elásticos de la base de datos creando una fragmentar Administrador de mapa"
   services="sql-database"
   documentationCenter=""
   authors="ddove"
   manager="jhubbard"
   editor=""/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-management"
   ms.date="10/24/2016"
   ms.author="ddove"/>

# <a name="migrate-existing-databases-to-scale-out"></a>Migrar bases de datos existentes a escalado

Administrar fácilmente las bases de escala horizontal sharded datos existentes con herramientas de base de datos de base de datos de SQL Azure (por ejemplo, la [biblioteca de cliente de base de datos elástica](sql-database-elastic-database-client-library.md)). En primer lugar debe convertir un conjunto de bases de datos para usar el [administrador se asignan partes](sql-database-elastic-scale-shard-map-management.md)existentes. 

## <a name="overview"></a>Información general
Para migrar una base de datos sharded: 

1. Preparar la [base de datos del Administrador de mapa de partes](sql-database-elastic-scale-shard-map-management.md).
2. Crear el mapa de partes.
3. Preparar la shards individuales.  
2. Agregar asignaciones a la equivalencia de partes.

Estas técnicas se pueden implementar mediante la [biblioteca de cliente de .NET Framework](http://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/)o scripts de PowerShell encontrados en [DB de SQL Azure - secuencias de comandos de herramientas de elásticos de la base de datos](https://gallery.technet.microsoft.com/scriptcenter/Azure-SQL-DB-Elastic-731883db). Los ejemplos siguientes usan las secuencias de comandos de PowerShell.

Para obtener más información sobre la ShardMapManager, vea [asignar la administración de partes](sql-database-elastic-scale-shard-map-management.md). Para obtener información general de las herramientas de base de datos elástico, vea [Introducción a las características elásticos de la base de datos](sql-database-elastic-scale-introduction.md).

## <a name="prepare-the-shard-map-manager-database"></a>Preparar la base de datos del Administrador de mapa de partes

El Administrador de mapa de fragmentar es una base de datos especial que contiene los datos para administrar bases de datos de escala horizontal. Puede usar una base de datos existente o crear una nueva base de datos. Tenga en cuenta que actúa como administrador de mapa fragmentar una base de datos no debe ser la misma base de datos como un fragmentar. Tenga en cuenta que la secuencia de comandos de PowerShell no crea la base de datos para usted. 

## <a name="step-1-create-a-shard-map-manager"></a>Paso 1: crear un fragmentar manager de mapa

    # Create a shard map manager. 
    New-ShardMapManager -UserName '<user_name>' 
    -Password '<password>' 
    -SqlServerName '<server_name>' 
    -SqlDatabaseName '<smm_db_name>' 
    #<server_name> and <smm_db_name> are the server name and database name 
    # for the new or existing database that should be used for storing 
    # tenant-database mapping information.

### <a name="to-retrieve-the-shard-map-manager"></a>Para recuperar el Administrador de mapa de partes

Tras la creación, puede recuperar el Administrador de mapa de partes con este cmdlet. Este paso es necesario cada vez necesite utilizar el objeto ShardMapManager.

    # Try to get a reference to the Shard Map Manager  
    $ShardMapManager = Get-ShardMapManager -UserName '<user_name>' 
    -Password '<password>' 
    -SqlServerName '<server_name>' 
    -SqlDatabaseName '<smm_db_name>' 

  
## <a name="step-2-create-the-shard-map"></a>Paso 2: crear el mapa de partes

Debe seleccionar el tipo de asignación de fragmentar para crear. La opción depende de la arquitectura de base de datos: 

1. Solo inquilino por base de datos (para los términos, consulte el [Glosario](sql-database-elastic-scale-glossary.md)). 
2. Varios inquilinos por base de datos (dos tipos):
    3. Asignación de lista
    4. Asignación de rango
 

Para un modelo de un solo inquilino, cree un mapa de partes de **asignación de la lista** . El modelo solo inquilino asigna una base de datos por cada inquilino. Esto es un modelo eficaz para desarrolladores de SaaS como simplifica la administración.

![Asignación de lista][1]

El modelo de múltiples arrendatario asigna a varios inquilinos a una base de datos (y puede distribuir grupos de inquilinos de múltiples bases de datos). Use este modelo cuando se esperaba cada inquilino tengan necesidades de datos pequeños. En este modelo, nos asignar un intervalo de inquilinos para una base de datos mediante la **asignación de rango**. 
 

![Asignación de rango][2]

O bien, puede implementar un modelo de base de datos de varios inquilinos mediante una *asignación de la lista* para asignar a varios inquilinos a una base de datos. Por ejemplo, DB1 se usa para almacenar información sobre el identificador del inquilino 1 y 5 y DB2 almacena los datos de inquilinos 7 y 10 del inquilino. 

![Varios inquilinos en DB único][3] 

**En función de su elección, elija una de estas opciones:**

### <a name="option-1-create-a-shard-map-for-a-list-mapping"></a>Opción 1: crear un mapa de partes de una asignación de lista
Crear un mapa de fragmentar mediante el objeto ShardMapManager. 

    # $ShardMapManager is the shard map manager object. 
    $ShardMap = New-ListShardMap -KeyType $([int]) 
    -ListShardMapName 'ListShardMap' 
    -ShardMapManager $ShardMapManager 
 
 
### <a name="option-2-create-a-shard-map-for-a-range-mapping"></a>Opción 2: crear un mapa de partes de una asignación de rango

Tenga en cuenta que para utilizar este patrón de asignación, el identificador del inquilino valores debe ser rangos continuos y es aceptable tener discontinuidad en los rangos omitiendo simplemente el rango al crear las bases de datos.

    # $ShardMapManager is the shard map manager object 
    # 'RangeShardMap' is the unique identifier for the range shard map.  
    $ShardMap = New-RangeShardMap 
    -KeyType $([int]) 
    -RangeShardMapName 'RangeShardMap' 
    -ShardMapManager $ShardMapManager 

### <a name="option-3-list-mappings-on-a-single-database"></a>Opción 3: Lista de las asignaciones en una base de datos
Configurar este patrón también requiere la creación de un mapa de la lista, tal como se muestra en el paso 2, opción 1.

## <a name="step-3-prepare-individual-shards"></a>Paso 3: Preparar shards individuales

Agregar cada fragmentar (base de datos) al administrador de mapa fragmentar. Esto prepara las bases de datos individuales para almacenar la información de la asignación. Ejecutar este método en cada fragmentar.
     
    Add-Shard 
    -ShardMap $ShardMap 
    -SqlServerName '<shard_server_name>' 
    -SqlDatabaseName '<shard_database_name>'
    # The $ShardMap is the shard map created in step 2.
 

## <a name="step-4-add-mappings"></a>Paso 4: Agregar asignaciones

La adición de asignaciones depende de la clase de mapa de partes que creó. Si ha creado un mapa de la lista, agregue las asignaciones de la lista. Si ha creado un mapa de rango, agregue las asignaciones de rango.

### <a name="option-1-map-the-data-for-a-list-mapping"></a>Opción 1: asignar los datos de una asignación de lista

Asignar los datos agregando una asignación de la lista por cada inquilino.  

    # Create the mappings and associate it with the new shards 
    Add-ListMapping 
    -KeyType $([int]) 
    -ListPoint '<tenant_id>' 
    -ListShardMap $ShardMap 
    -SqlServerName '<shard_server_name>' 
    -SqlDatabaseName '<shard_database_name>' 

### <a name="option-2-map-the-data-for-a-range-mapping"></a>Opción 2: asignar los datos de una asignación de rango

Agregar el intervalo de asignaciones para todo el inquilino id rango: asociaciones de base de datos:

    # Create the mappings and associate it with the new shards 
    Add-RangeMapping 
    -KeyType $([int]) 
    -RangeHigh '5' 
    -RangeLow '1' 
    -RangeShardMap $ShardMap 
    -SqlServerName '<shard_server_name>' 
    -SqlDatabaseName '<shard_database_name>' 


### <a name="step-4-option-3-map-the-data-for-multiple-tenants-on-a-single-database"></a>La opción 3 del paso 4: asignar los datos de varios inquilinos en una base de datos

Para cada inquilino, ejecute el ListMapping agregar (opción 1, anteriormente). 


## <a name="checking-the-mappings"></a>Comprobación de las asignaciones

Puede consultar información sobre la shards existentes y las asignaciones asociadas con los comandos siguientes:  

    # List the shards and mappings 
    Get-Shards -ShardMap $ShardMap 
    Get-Mappings -ShardMap $ShardMap 

## <a name="summary"></a>Resumen

Una vez haya completado la instalación, puede empezar a usar la biblioteca de cliente de base de datos elástico. También puede usar [enrutamiento según los datos](sql-database-elastic-scale-data-dependent-routing.md) y la [consulta de varias partes](sql-database-elastic-scale-multishard-querying.md).

## <a name="next-steps"></a>Pasos siguientes


Obtener las secuencias de comandos de PowerShell de [sripts de herramientas de base de datos de Azure SQL DB elástica](https://gallery.technet.microsoft.com/scriptcenter/Azure-SQL-DB-Elastic-731883db).

Las herramientas también están en GitHub: [Herramientas Azure, elástica-db](https://github.com/Azure/elastic-db-tools).

Use la herramienta de combinación de división para mover datos a o desde un modelo de múltiples arrendatario a un modelo de inquilinos único. Consulte la [herramienta de división de combinación](sql-database-elastic-scale-get-started.md).

## <a name="additional-resources"></a>Recursos adicionales

Para obtener información sobre patrones de arquitectura de datos comunes de aplicaciones de base de datos de varios inquilinos software como servicio (SaaS), vea [Modelos de diseño para aplicaciones de SaaS inquilino múltiples con la base de datos de SQL Azure](sql-database-design-patterns-multi-tenancy-saas-applications.md).

## <a name="questions-and-feature-requests"></a>Preguntas y solicitudes de características

Si tiene preguntas, póngase contactar con nosotros en el [foro de la base de datos SQL](http://social.msdn.microsoft.com/forums/azure/home?forum=ssdsgetstarted) y para las solicitudes de la característica de, agréguelas al [foro de comentarios de la base de datos de SQL](https://feedback.azure.com/forums/217321-sql-database/).

<!--Image references-->
[1]: ./media/sql-database-elastic-convert-to-use-elastic-tools/listmapping.png
[2]: ./media/sql-database-elastic-convert-to-use-elastic-tools/rangemapping.png
[3]: ./media/sql-database-elastic-convert-to-use-elastic-tools/multipleonsingledb.png
 
