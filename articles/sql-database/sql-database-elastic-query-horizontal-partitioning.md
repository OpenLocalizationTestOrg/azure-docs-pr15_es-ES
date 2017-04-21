<properties
    pageTitle="Notificar en bases de datos de escala de salida de la nube | Microsoft Azure"
    description="Cómo configurar elásticas consultas sobre particiones horizontales"    
    services="sql-database"
    documentationCenter=""  
    manager="jhubbard"
    authors="torsteng"/>

<tags
    ms.service="sql-database"
    ms.workload="sql-database"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="05/27/2016"
    ms.author="torsteng" />

# <a name="reporting-across-scaled-out-cloud-databases-preview"></a>Notificar en bases de datos de escala de salida de la nube (vista preliminar)

![Hacer consultas en shards][1]

Bases de datos sharded distribución filas en una escala los datos nivel. El esquema es idéntico en todas las bases de datos participantes, también conocidos como partición horizontal. Usar una consulta de elástica, puede crear informes que abarcan todas las bases de datos en una base de datos sharded.

Para el inicio rápido, vea [informes en bases de datos de escala de salida de la nube](sql-database-elastic-query-getting-started.md).

Para no sharded bases de datos, vea [consulta entre bases de datos de nube con diferentes esquemas](sql-database-elastic-query-vertical-partitioning.md). 

 
## <a name="prerequisites"></a>Requisitos previos

* Crear un mapa de partes con la biblioteca de cliente de base de datos elástico. vea [fragmentar asignar administración](sql-database-elastic-scale-shard-map-management.md). O bien, use la aplicación de ejemplo en [Introducción a las herramientas de base de datos elástico](sql-database-elastic-scale-get-started.md).
* Como alternativa, vea [migrar bases de datos existentes a bases de datos de escala horizontal](sql-database-elastic-convert-to-use-elastic-tools.md).
* El usuario debe tener permiso de modificar cualquier origen de datos externo. Este permiso se incluye con el permiso de modificar la base de datos.
* Se necesitan permisos de modificar cualquier origen de datos externo para hacer referencia al origen de datos subyacente.

## <a name="overview"></a>Información general

Estas instrucciones crean la representación de metadatos de la capa de datos sharded en la base de datos de consulta elástico. 


1. [CREAR CLAVE PRINCIPAL](https://msdn.microsoft.com/library/ms174382.aspx)
2. [CREAR LA BASE DE DATOS DE ÁMBITO DE LA CREDENCIAL](https://msdn.microsoft.com/library/mt270260.aspx)
3. [CREAR ORIGEN DE DATOS EXTERNO](https://msdn.microsoft.com/library/dn935022.aspx)
4. [CREAR TABLA EXTERNA](https://msdn.microsoft.com/library/dn935021.aspx) 

## <a name="11-create-database-scoped-master-key-and-credentials"></a>Ámbito 1.1 Crear base de datos de la clave principal y las credenciales 

La consulta elástico se utiliza la credencial para conectarse a las bases de datos remotas.  

    CREATE MASTER KEY ENCRYPTION BY PASSWORD = 'password';
    CREATE DATABASE SCOPED CREDENTIAL <credential_name>  WITH IDENTITY = '<username>',  
    SECRET = '<password>'
    [;]
 
>[AZURE.NOTE] Asegúrese de que la *"\<nombre de usuario\>"* no incluye ninguna *"@servername"* sufijo. 

## <a name="12-create-external-data-sources"></a>1.2 crear orígenes de datos externos

Sintaxis:

    <External_Data_Source> ::=    
    CREATE EXTERNAL DATA SOURCE <data_source_name> WITH                                            
            (TYPE = SHARD_MAP_MANAGER,
                    LOCATION = '<fully_qualified_server_name>',
            DATABASE_NAME = ‘<shardmap_database_name>',
            CREDENTIAL = <credential_name>, 
            SHARD_MAP_NAME = ‘<shardmapname>’ 
                   ) [;] 

### <a name="example"></a>Ejemplo 

    CREATE EXTERNAL DATA SOURCE MyExtSrc 
    WITH 
    ( 
        TYPE=SHARD_MAP_MANAGER,
        LOCATION='myserver.database.windows.net', 
        DATABASE_NAME='ShardMapDatabase', 
        CREDENTIAL= SMMUser, 
        SHARD_MAP_NAME='ShardMap' 
    );
 
Recuperar la lista de orígenes de datos actual: 

    select * from sys.external_data_sources; 

El origen de datos externo hace referencia a su mapa fragmentar. Una consulta elástica utiliza el origen de datos externo y el mapa de fragmentar subyacente para enumerar las bases de datos que participan en el nivel de datos. Leer el mapa fragmentar y tener acceso a los datos en el shards durante el proceso de una consulta elástico, se usan las mismas credenciales. 

## <a name="13-create-external-tables"></a>1.3 crear tablas externas 
 
Sintaxis:  

    CREATE EXTERNAL TABLE [ database_name . [ schema_name ] . | schema_name. ] table_name  
        ( { <column_definition> } [ ,...n ])     
        { WITH ( <sharded_external_table_options> ) }
    ) [;]  
    
    <sharded_external_table_options> ::= 
      DATA_SOURCE = <External_Data_Source>,       
      [ SCHEMA_NAME = N'nonescaped_schema_name',] 
      [ OBJECT_NAME = N'nonescaped_object_name',] 
      DISTRIBUTION = SHARDED(<sharding_column_name>) | REPLICATED |ROUND_ROBIN

**Ejemplo**

    CREATE EXTERNAL TABLE [dbo].[order_line]( 
         [ol_o_id] int NOT NULL, 
         [ol_d_id] tinyint NOT NULL,
         [ol_w_id] int NOT NULL, 
         [ol_number] tinyint NOT NULL, 
         [ol_i_id] int NOT NULL, 
         [ol_delivery_d] datetime NOT NULL, 
         [ol_amount] smallmoney NOT NULL, 
         [ol_supply_w_id] int NOT NULL, 
         [ol_quantity] smallint NOT NULL, 
         [ol_dist_info] char(24) NOT NULL 
    ) 
    
    WITH 
    ( 
        DATA_SOURCE = MyExtSrc, 
        SCHEMA_NAME = 'orders', 
        OBJECT_NAME = 'order_details', 
        DISTRIBUTION=SHARDED(ol_w_id)
    ); 

Recuperar la lista de tablas externas de la base de datos actual: 

    SELECT * from sys.external_tables; 

Quitar tablas externas:

    DROP EXTERNAL TABLE [ database_name . [ schema_name ] . | schema_name. ] table_name[;]

### <a name="remarks"></a>Notas

Los datos\_cláusula de origen define el origen de datos externos (un mapa fragmentar) que se usa para la tabla externa.  

El esquema\_nombre y objeto\_cláusulas de nombre asignan la definición de tabla externa a una tabla en un esquema diferente. Si se omite, se supone que el esquema del objeto remoto "dbo" y su nombre se supone que es idéntico al nombre de tabla externa que se ha definido. Esto es útil si el nombre de la tabla remota ya existe en la base de datos donde desea crear la tabla externa. Por ejemplo, que desea definir una tabla externa para obtener una vista agregada de vistas de catálogo o DMV en los datos de escalados fuera de nivel. Dado que las vistas de catálogo y DMV ya existe localmente, no puede usar sus nombres para la definición de tabla externa. En su lugar, use un nombre diferente y usar la vista de catálogo o la DMV nombre en el esquema\_nombre o el objeto\_cláusulas de nombre. (Consulte el ejemplo siguiente). 

La cláusula de distribución especifica la distribución de datos utilizada para esta tabla. Procesador de consultas utiliza la información de la cláusula de distribución para generar los planes de consulta más eficaces.  

1. **SHARDED** significa datos está divididos horizontalmente en las bases de datos. La clave de partición para la distribución de datos es el parámetro de **< sharding_column_name >** .
2. **REPLICADA** significa que idénticos copias de la tabla están presentes en cada base de datos. Es su responsabilidad asegurarse de que las réplicas son idénticas en todas las bases de datos.
3. **Redondear\_ROBIN** significa que la tabla se divide horizontalmente mediante un método de distribución de dependientes de la aplicación. 

**Referencia de nivel de datos**: la tabla externa DDL hace referencia a un origen de datos externo. El origen de datos externo especifica un mapa de partes que proporciona la información necesaria para ubicar las bases de datos en el nivel de datos a la tabla externa. 


### <a name="security-considerations"></a>Consideraciones de seguridad 

Los usuarios con acceso a la tabla externa automáticamente tener acceso a las tablas remotas subyacentes en las credenciales proporcionadas en la definición de origen de datos externos. Evite no deseada elevación de privilegios a través de la credencial de origen de datos externo. Use conceder o REVOCAR para una tabla externa como si fuera una tabla normal.  

Una vez que ha definido el origen de datos externo y las tablas externas, ahora puede usar T-SQL completa sobre las tablas externas.

## <a name="example-querying-horizontal-partitioned-databases"></a>Ejemplo: consultar bases de datos divididas horizontales 

La siguiente consulta realiza una combinación de tres vías entre almacenes, pedidos y líneas de pedido y usa varios agregados y un filtro selectivo. Supone partición (1) horizontal (sharding) y (2) que almacenes, pedidos y líneas de pedido son sharded por la columna de Id. de almacén y que la consulta elástica puede ubicar las combinaciones en la shards y procesar la consume parte de la consulta en el shards en paralelo. 

    select  
         w_id as warehouse,
         o_c_id as customer,
         count(*) as cnt_orderline,
         max(ol_quantity) as max_quantity,
         avg(ol_amount) as avg_amount, 
         min(ol_delivery_d) as min_deliv_date
    from warehouse 
    join orders 
    on w_id = o_w_id
    join order_line 
    on o_id = ol_o_id and o_w_id = ol_w_id 
    where w_id > 100 and w_id < 200 
    group by w_id, o_c_id 
 
## <a name="stored-procedure-for-remote-t-sql-execution-spexecuteremote"></a>Procedimiento almacenado para la ejecución de SQL T remota: sp\_execute_remote

Consulta elástico también presenta un procedimiento almacenado que proporciona acceso directo a la shards. El procedimiento almacenado se denomina [sp\_ejecutar \_remoto](https://msdn.microsoft.com/library/mt703714) y puede utilizarse para ejecutar procedimientos almacenados remotos o código T-SQL en las bases de datos remotas. Toma los parámetros siguientes: 

* Nombre de origen de datos (nvarchar): el nombre del origen de datos externos de tipo RDBMS. 
* Consulta (nvarchar): consulta T-SQL que se ejecuta en cada fragmentar. 
* Declaración de parámetro (nvarchar) - opcional: cadena con definiciones de tipo de datos para los parámetros que se utilizan en el parámetro de consulta (como sp_executesql). 
* Lista de valores de parámetro - opcional: lista de valores separados por comas de valores de parámetro (por ejemplo, sp_executesql).

El sp\_ejecutar\_remota utiliza el origen de datos externos en los parámetros de invocación de funciones para ejecutar la instrucción SQL T dada en las bases de datos remotas. Usa las credenciales del origen de datos externo para conectarse a la base de datos del Administrador de shardmap y las bases de datos remotas.  

Ejemplo: 

    EXEC sp_execute_remote
        N'MyExtSrc',
        N'select count(w_id) as foo from warehouse' 

## <a name="connectivity-for-tools"></a>Conectividad de herramientas  

Usar regulares cadenas de conexión de SQL Server para conectar la aplicación, las herramientas de integración de BI y datos a la base de datos con las definiciones de tabla externa. Asegúrese de que se admite SQL Server como origen de datos de la herramienta. A continuación, referencia de la base de datos de consulta elástico como cualquier otra base de datos de SQL Server conectada a la herramienta y el uso de tablas externas de la herramienta o la aplicación como si fueran tablas locales. 

## <a name="best-practices"></a>Prácticas recomendadas 

* Asegúrese de que se ha concedido acceso a la base de datos de extremo elástico consulta a la base de datos de shardmap y todos los shards a través de los servidores de seguridad de base de datos de SQL.  

* Validar o exigir la distribución de datos definida por la tabla externa. Si la distribución de datos real es diferente de la distribución especificada en la definición de una tabla, las consultas pueden producir resultados inesperados. 

* Consulta elástico actualmente no realiza la eliminación de partes al predicados sobre la clave de sharding permitan excluir determinadas shards de procesamiento de forma segura.

* Consulta elástico funciona mejor para las consultas donde se puede realizar la mayor parte del cálculo de la shards. Normalmente recibe el mejor rendimiento de la consulta con predicados de filtro selectivo que puede evaluarse en el shards o combinaciones sobre las teclas de particiones que se pueden realizar de forma en shards todas las particiones alineadas. Otros patrones de consulta para cargar grandes cantidades de datos de la shards al nodo principal y pueden realizar mal

[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-query-horizontal-partitioning/horizontalpartitioning.png
<!--anchors-->
