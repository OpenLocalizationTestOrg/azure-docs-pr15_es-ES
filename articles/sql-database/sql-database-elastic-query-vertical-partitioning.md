<properties
    pageTitle="Consulta en bases de datos de nube con diferentes esquema | Microsoft Azure"
    description="Cómo configurar las consultas de bases de datos cruzadas sobre particiones verticales"    
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

# <a name="query-across-cloud-databases-with-different-schemas-preview"></a>Hacer consultas en bases de datos de nube con diferentes esquemas (vista preliminar)

![Hacer consultas en tablas de bases de datos diferentes][1]

Dividir verticalmente bases de datos usan diferentes conjuntos de tablas de bases de datos diferentes. Esto significa que el esquema es diferente en distintas bases de datos. Por ejemplo, todas las tablas de inventario están en una base de datos mientras todas las tablas de contabilidad se encuentran en una segunda base de datos. 

## <a name="prerequisites"></a>Requisitos previos

* El usuario debe tener permiso de modificar cualquier origen de datos externo. Este permiso se incluye con el permiso de modificar la base de datos.
* Se necesitan permisos de modificar cualquier origen de datos externo para hacer referencia al origen de datos subyacente.

## <a name="overview"></a>Información general

**Nota**: a diferencia con partición horizontal, estas instrucciones DDL no dependen de definir un nivel de datos con un mapa de fragmentar a través de la biblioteca de cliente de base de datos elástico.

1. [CREAR CLAVE PRINCIPAL](https://msdn.microsoft.com/library/ms174382.aspx)
2. [CREAR LA BASE DE DATOS DE ÁMBITO DE LA CREDENCIAL](https://msdn.microsoft.com/library/mt270260.aspx)
3. [CREAR ORIGEN DE DATOS EXTERNO](https://msdn.microsoft.com/library/dn935022.aspx)
4. [CREAR TABLA EXTERNA](https://msdn.microsoft.com/library/dn935021.aspx) 


## <a name="create-database-scoped-master-key-and-credentials"></a>Crear clave principal de ámbito de la base de datos y credenciales 

La consulta elástico se utiliza la credencial para conectarse a las bases de datos remotas.  

    CREATE MASTER KEY ENCRYPTION BY PASSWORD = 'password';
    CREATE DATABASE SCOPED CREDENTIAL <credential_name>  WITH IDENTITY = '<username>',  
    SECRET = '<password>'
    [;]
 
**Nota**    Asegúrese de que la *<username>* no incluye ninguna *"@servername"* sufijo. 

## <a name="create-external-data-sources"></a>Crear orígenes de datos externos

Sintaxis:

    <External_Data_Source> ::=
    CREATE EXTERNAL DATA SOURCE <data_source_name> WITH 
               (TYPE = RDBMS,
                LOCATION = ’<fully_qualified_server_name>’,
                DATABASE_NAME = ‘<remote_database_name>’,  
                CREDENTIAL = <credential_name> 
                ) [;] 

**Importante**   El parámetro de tipo debe estar establecido en **RDBMS**. 

### <a name="example"></a>Ejemplo 

En el ejemplo siguiente se muestra el uso de la instrucción CREATE para orígenes de datos externos. 

    CREATE EXTERNAL DATA SOURCE RemoteReferenceData 
    WITH 
    ( 
        TYPE=RDBMS, 
        LOCATION='myserver.database.windows.net', 
        DATABASE_NAME='ReferenceData', 
        CREDENTIAL= SqlUser 
    ); 
 
Para recuperar la lista de orígenes de datos actual: 

    select * from sys.external_data_sources; 

### <a name="external-tables"></a>Tablas externas 

Sintaxis:

    CREATE EXTERNAL TABLE [ database_name . [ schema_name ] . | schema_name . ] table_name  
    ( { <column_definition> } [ ,...n ])     
    { WITH ( <rdbms_external_table_options> ) } 
    )[;] 
    
    <rdbms_external_table_options> ::= 
      DATA_SOURCE = <External_Data_Source>, 
      [ SCHEMA_NAME = N'nonescaped_schema_name',] 
      [ OBJECT_NAME = N'nonescaped_object_name',] 

### <a name="example"></a>Ejemplo  

    CREATE EXTERNAL TABLE [dbo].[customer]( 
        [c_id] int NOT NULL, 
        [c_firstname] nvarchar(256) NULL, 
        [c_lastname] nvarchar(256) NOT NULL, 
        [street] nvarchar(256) NOT NULL, 
        [city] nvarchar(256) NOT NULL, 
        [state] nvarchar(20) NULL, 
        [country] nvarchar(50) NOT NULL, 
    ) 
    WITH 
    ( 
           DATA_SOURCE = RemoteReferenceData 
    ); 

En el ejemplo siguiente se muestra cómo recuperar la lista de tablas externas de la base de datos actual: 

    select * from sys.external_tables; 

### <a name="remarks"></a>Notas

Consulta elástico extiende la sintaxis de la tabla externa existente para definir tablas externas que usar orígenes de datos externos de tipo RDBMS. Una definición de tabla externa para las particiones verticales cubre los siguientes aspectos: 

* **Esquema**: la tabla externa DDL define un esquema que pueden usar las consultas. El esquema que se proporcionan en la definición de una tabla externa debe coincidir con el esquema de las tablas de la base de datos remota donde se almacenan los datos reales. 

* **Referencia de la base de datos remota**: la tabla externa DDL hace referencia a un origen de datos externo. El origen de datos externo especifica el nombre del servidor lógico y el nombre de la base de datos de la base de datos remota donde se almacenan los datos reales de la tabla. 

Usar un origen de datos externo tal como se describe en la sección anterior, la sintaxis para crear tablas externas es la siguiente: 

La cláusula DATA_SOURCE define el origen de datos externos (es decir, la base de datos remota en caso de que las particiones verticales) que se usa para la tabla externa.  

Las cláusulas SCHEMA_NAME y nombre de objeto permiten asignar la definición de tabla externa a una tabla en un esquema diferente en la base de datos remota o a una tabla con un nombre diferente, respectivamente. Esto es útil si desea definir una tabla externa a una vista de catálogo o DMV en su base de datos remota o cualquier otra situación donde el nombre de la tabla remota ya existe localmente.  

La siguiente instrucción DDL quita una definición de tabla externa existente desde el catálogo local. No afecta a la base de datos remota. 

    DROP EXTERNAL TABLE [ [ schema_name ] . | schema_name. ] table_name[;]  

**Permisos para crear o colocar externos tabla**: se necesitan permisos de modificar cualquier origen de datos externo para tabla externa DDL que también es necesario que haga referencia al origen de datos subyacente.  

## <a name="security-considerations"></a>Consideraciones de seguridad
Los usuarios con acceso a la tabla externa automáticamente tener acceso a las tablas remotas subyacentes en las credenciales proporcionadas en la definición de origen de datos externos. Debe administrar con cuidado el acceso a la tabla externa para evitar no deseada elevación de privilegios a través de la credencial de origen de datos externo. Permisos de SQL normales pueden usarse para conceder o REVOCAR el acceso a una tabla externa como si fuera una tabla normal.  


## <a name="example-querying-vertically-partitioned-databases"></a>Ejemplo: consultar verticalmente particiones bases de datos 

La siguiente consulta realiza una combinación de tres vías entre las dos tablas locales de pedidos y líneas de pedido y la tabla remota de los clientes. Esto es un ejemplo del caso de uso de datos de referencia para elástico consulta: 

    SELECT      
     c_id as customer,
     c_lastname as customer_name,
     count(*) as cnt_orderline, 
     max(ol_quantity) as max_quantity,
     avg(ol_amount) as avg_amount,
     min(ol_delivery_d) as min_deliv_date
    FROM customer 
    JOIN orders 
    ON c_id = o_c_id
    JOIN  order_line 
    ON o_id = ol_o_id and o_c_id = ol_c_id
    WHERE c_id = 100


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

Puede usar regulares cadenas de conexión de SQL Server para conectar las herramientas de integración de BI y los datos a bases de datos en el servidor de base de datos SQL que tiene elástico consulta habilitado y tablas externas definidas por el. Asegúrese de que se admite SQL Server como origen de datos de la herramienta. Hacer referencia a la base de datos de consulta elásticas y sus tablas externas igual que cualquier otra base de SQL Server para conectarse a con la herramienta. 

## <a name="best-practices"></a>Prácticas recomendadas 
 
* Asegúrese de que la base de datos de extremo de consulta elástico se ha concedido acceso a la base de datos remota habilitando acceso para los servicios de Azure en su configuración de firewall de la base de datos de SQL. También asegúrese de que las credenciales proporcionadas en la definición del origen de datos externos puedan iniciar sesión correctamente en la base de datos remota y tiene los permisos para tener acceso a la tabla remota.  

* Consulta elástico funciona mejor para las consultas donde se puede realizar la mayor parte del cálculo de las bases de datos remotas. Normalmente, obtendrá el mejor rendimiento de la consulta con predicados de filtro selectivo que puede evaluarse en las bases de datos remotas o combinaciones que pueden realizarse por completo en la base de datos remota. Otros patrones de consulta para cargar grandes cantidades de datos de la base de datos remota y pueden realizar mal. 


## <a name="next-steps"></a>Pasos siguientes

Para consultar las bases de datos divididos (también conocido como sharded bases de datos), consulte [consultas en bases de datos de nube sharded (divididas horizontalmente)](sql-database-elastic-query-horizontal-partitioning.md).

[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]


<!--Image references-->
[1]: ./media/sql-database-elastic-query-vertical-partitioning/verticalpartitioning.png


<!--anchors-->
