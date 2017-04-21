<properties
    pageTitle="Introducción de consulta de base de datos elástico de base de datos de SQL Azure | Microsoft Azure"
    description="Información general sobre la característica de consulta elásticas"    
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
    ms.date="04/27/2016"
    ms.author="torsteng" />

# <a name="azure-sql-database-elastic-database-query-overview-preview"></a>Introducción de consulta elásticos de la base de datos de Azure base de datos SQL (vista preliminar)

La característica de consulta elásticos de la base de datos (en la vista previa) le permite ejecutar una consulta de Transact-SQL que se extiende por varias bases de datos de base de datos de SQL Azure (SQLDB). Permite realizar consultas de bases de datos cruzadas para tener acceso a tablas remotas y conectar herramientas de Microsoft y de terceros (Excel, PowerBI, tablero, etc.) a la consulta en todos los niveles de datos con varias bases de datos. Esta característica puede escalar consultas a los niveles de datos de gran tamaño en la base de datos SQL y visualizar los resultados en los informes de business intelligence (BI).

## <a name="documentation"></a>Documentación

* [Introducción a las consultas de bases de datos cruzadas](sql-database-elastic-query-getting-started-vertical.md)
* [Crear informes en bases de datos de escala de salida de la nube](sql-database-elastic-query-getting-started.md)
* [Hacer consultas en bases de datos de nube sharded (horizontalmente particionados)](sql-database-elastic-query-horizontal-partitioning.md)
* [Hacer consultas en bases de datos de nube con diferentes esquemas (verticalmente particionados)](sql-database-elastic-query-vertical-partitioning.md)
* [SP\_ejecutar \_remoto](https://msdn.microsoft.com/library/mt703714)


## <a name="why-use-elastic-queries"></a>¿Por qué usar consultas elásticas?

**Base de datos SQL Azure**

Hacer consultas en bases de datos de SQL Azure completamente en T SQL. Esto permite para consultas de solo lectura de bases de datos remotas. Proporciona una opción para los clientes de SQL Server local actuales migrar aplicaciones con nombres de tres y cuatro parcial o servidor vinculado a la base de datos de SQL.

**Disponible en nivel estándar** Consulta elástico es compatible en el nivel de rendimiento estándar además del nivel de rendimiento Premium. Vea la sección limitaciones de vista previa debajo de las limitaciones de rendimiento para niveles inferiores de rendimiento.

**Insertar en bases de datos remotas**

Consultas elásticas ahora pueden insertar parámetros SQL para las bases de datos remotas de ejecución.

**Ejecución de procedimiento almacenado**

Ejecutar llamadas de procedimiento almacenado remoto o funciones remotas mediante [sp\_ejecutar \_remoto](https://msdn.microsoft.com/library/mt703714).

**Flexibilidad**

Tablas externas con elástico consulta ahora hacer referencia a tablas remotas con un esquema diferente o el nombre de la tabla.

## <a name="elastic-database-query-scenarios"></a>Escenarios de consulta de base de datos elásticas

El objetivo es facilitar la consultas escenarios donde varias bases de datos contribuyan filas en un único resultado general. La consulta o bien puede estar compuesta por el usuario o la aplicación directamente o indirectamente a través de herramientas que están conectadas a la base de datos. Esto es especialmente útil al crear informes, con herramientas de integración de BI o datos comercial, o cualquier aplicación que no se puede cambiar. Con una consulta elástica, puede crear una consulta a través de varias bases de datos con la experiencia familiar de conectividad de SQL Server en herramientas como Excel, PowerBI, una plantilla o Cognos.
Una consulta elástica permite acceder fácilmente a toda una colección de bases de datos a través de consultas emitidas por SQL Server Management Studio o Visual Studio y facilita la consulta entre bases de datos de entidad Framework u otros entornos de ORM. Figura 1 muestra un escenario donde una aplicación de nube existente (que usa la [biblioteca de cliente de base de datos elástica](sql-database-elastic-database-client-library.md)) se basa en un nivel de datos de escala horizontal y una consulta elástica se utiliza para los informes de entre bases de datos.

**Figura 1** Consulta de elásticos de la base de datos usada en el nivel de datos de escala horizontal

![Consulta de elásticos de la base de datos usada en el nivel de datos de escala horizontal][1]

Escenarios de cliente de consulta elástico se caracterizan por las siguientes topologías:

* **Vertical partición: consultas de bases de datos cruzadas** (Topología 1): los datos se dividen verticalmente entre un número de bases de datos en un nivel de datos. Normalmente, los diferentes conjuntos de tablas residen en bases de datos diferentes. Esto significa que el esquema es diferente en distintas bases de datos. Por ejemplo, todas las tablas de inventario están en una base de datos mientras todas las tablas de contabilidad se encuentran en una segunda base de datos. Casos de uso común con esta topología requieren uno para hacer consultas en o para compilar informes en tablas de bases de datos.
* **Horizontal particiones – Sharding** (Topología 2): datos se dividen horizontalmente para distribuir filas en una escala los datos nivel. Con este método, el esquema es idéntico en todas las bases de datos de participantes. Este método también se denomina "sharding". Puede llevar a cabo sharding y administradas utilizando (1) la base de datos elástico herramientas bibliotecas o (2) self-sharding. Utiliza una consulta elástica consulta o compilar informes en muchos shards.

> [AZURE.NOTE] Consulta de base de datos elástico funciona mejor para ocasionales escenarios de informes, donde se puede realizar la mayoría de los procesos en el nivel de datos. Para cargas de trabajo informes o escenarios de almacenamiento de datos con consultas más complejas, considere usar el [Almacén de datos de SQL Azure](https://azure.microsoft.com/services/sql-data-warehouse/).


## <a name="elastic-database-query-topologies"></a>Elásticas topologías de consulta de base de datos

### <a name="topology-1-vertical-partitioning--cross-database-queries"></a>Topología 1: Creación de particiones verticales: las consultas entre bases de datos

Para empezar a escribir código, consulte [Introducción a la consulta de base de datos cruzados (partición vertical)](sql-database-elastic-query-getting-started-vertical.md).

Una consulta elástica puede usarse para que los datos que se encuentra en una base de datos SQLDB disponible para otras bases de datos SQLDB. Esto permite que las consultas de una base de datos para hacer referencia a tablas en cualquier otro SQLDB base de datos remota. El primer paso es definir el origen de datos externo para cada base de datos remota. El origen de datos externo se define en la base de datos local desde la que desea obtener acceso a las tablas que se encuentra en la base de datos remota. Cambios no son necesarios en la base de datos remota. Para verticales partición escenarios típicos donde diferentes bases de datos tienen diferentes esquemas, consultas elásticas pueden usarse para implementar los casos de uso común, como el acceso a datos de referencia y bases de datos cruzadas consultar.

**Datos de referencia**: topología 1 se usa para la administración de datos de referencia. En la siguiente ilustración, se mantienen dos tablas (T1 y T2) con datos de referencia en una base de datos dedicado. Usar una consulta de elástica, puede ahora acceso tablas T1 y T2 remotamente desde otras bases de datos, tal como se muestra en la ilustración. Topología de uso 1 si las tablas de referencia son pequeñas o remotas consultas en la tabla de referencia tiene predicados selectivos.

**Figura 2** Vertical partición - utilizando los datos de referencia de consulta a consulta elástico

![Vertical partición - utilizando los datos de referencia de consulta a consulta elástico][3]

**Consultas de bases de datos cruzadas**: elástica consulta habilitar casos de uso que requieren consultas a través de varias bases de datos SQLDB. La figura 3 muestra cuatro bases de datos diferentes: CRM, inventario, recursos humanos y productos. Consultas realizadas en una de las bases de datos también necesitan tener acceso a una o todas las otras bases de datos. Mediante una consulta elástica, puede configurar la base de datos para este caso ejecutando algunas instrucciones DDL simples en cada una de las cuatro bases de datos. Después de esta configuración de una sola vez, el acceso a una tabla remota es tan sencillo como hacer referencia a una tabla local de las consultas SQL T o de las herramientas de inteligencia empresarial. Este enfoque es recomendable si las consultas remotas no devuelven resultados de gran tamaño.

**Figura 3** Vertical partición - usando elástica a consulta a través de varias bases de datos

![Vertical partición - usando elástica a consulta a través de varias bases de datos][4]

### <a name="topology-2-horizontal-partitioning--sharding"></a>Topología de 2: Partición Horizontal: sharding

El uso de consulta elástica para realizar tareas de informes sobre sharded, es decir, horizontalmente particionadas, nivel de datos requiere un [mapa de fragmentar elásticos de la base de datos](sql-database-elastic-scale-shard-map-management.md) para representar las bases de datos de la capa de datos. Normalmente, solo un mapa fragmentar solo se usa en este escenario y una base de datos dedicada con capacidades de consulta elástico sirve como punto de entrada para los informes de consultas. Sólo esta base de datos dedicado necesita obtener acceso a la equivalencia de partes. Figura 4 ilustra esta topología y su configuración con el mapa de base de datos y partes de consulta elástico. Las bases de datos en el nivel de datos pueden ser de cualquier versión de la base de datos de SQL Azure o la edición. Para obtener más información acerca de la biblioteca de cliente de base de datos elásticas y crear mapas fragmentar, vea [fragmentar asignar administración](sql-database-elastic-scale-shard-map-management.md).

**Figura 4** Horizontal partición - con query elástica para informar sobre los niveles de datos sharded

![Horizontal partición - con query elástica para informar sobre los niveles de datos sharded][5]

> [AZURE.NOTE] La base de datos de consulta de base de datos elástico dedicado debe ser una base de datos de SQL DB v12. No hay ninguna restricción en la shards a sí mismos.

Para empezar a escribir código, consulte [Introducción a la consulta de base de datos elástica para particiones horizontales (sharding)](sql-database-elastic-query-getting-started.md).

## <a name="implementing-elastic-database-queries"></a>Implementar consultas de base de datos elásticas

En las siguientes secciones, se describen los pasos para implementar elástico consulta para los escenarios de particiones verticales y horizontales. También hacen referencia a documentación más detallada de los diferentes escenarios de particiones.

### <a name="vertical-partitioning---cross-database-queries"></a>Partición vertical - consultas entre bases de datos

Los siguientes pasos configurar consultas de base de datos elástica para escenarios de partición verticales que requieren acceso a una tabla que se encuentra en bases de datos remotas SQLDB con el mismo esquema:

*    [Crear la clave del maestro](https://msdn.microsoft.com/library/ms174382.aspx) mymasterkey
*    [Crear la CREDENCIAL del ámbito de la base de datos](https://msdn.microsoft.com/library/mt270260.aspx) mycredential
*    [Origen de datos externos de crear y colocar](https://msdn.microsoft.com/library/dn935022.aspx) mydatasource de tipo **RDBMS**
*    [Crear y colocar externos tabla](https://msdn.microsoft.com/library/dn935021.aspx) mytable

Después de ejecutar las instrucciones de DDL, puede tener acceso a la tabla remota "mytable" como si fuera una tabla local. Base de datos de SQL Azure automáticamente abre una conexión a la base de datos remota, procesa su solicitud en la base de datos remota y devuelve los resultados.
Obtener más información sobre los pasos necesarios para el escenario de partición vertical puede encontrarse en [query elástico para las particiones verticales](sql-database-elastic-query-vertical-partitioning.md).  

### <a name="horizontal-partitioning---sharding"></a>Partición horizontal - sharding

Los siguientes pasos configuran consultas de base de datos elástica horizontal escenarios particiones que requieren acceso a un conjunto de tabla que se encuentran en (normalmente) remotos SQLDB bases de datos:

*    [Crear la clave del maestro](https://msdn.microsoft.com/library/ms174382.aspx) mymasterkey
*    [Crear la CREDENCIAL del ámbito de la base de datos](https://msdn.microsoft.com/library/mt270260.aspx) mycredential
*    Crear un [mapa de partes](sql-database-elastic-scale-shard-map-management.md) que representa el nivel de datos mediante la biblioteca de cliente de base de datos elástico.   
*    [Origen de datos externos de crear y colocar](https://msdn.microsoft.com/library/dn935022.aspx) mydatasource de tipo **SHARD_MAP_MANAGER**
*    [Crear y colocar externos tabla](https://msdn.microsoft.com/library/dn935021.aspx) mytable

Después de realizar estos pasos, puede acceder a la tabla dividida "mytable" como si fuera una tabla local. Base de datos de SQL Azure automáticamente abre varias conexiones paralelas a las bases de datos remotas donde las tablas se almacenan física, procesa las solicitudes en las bases de datos remotas y devuelve los resultados.
Obtener más información sobre los pasos necesarios para el escenario de particiones horizontal puede encontrarse en [query elástica para partición horizontal](sql-database-elastic-query-horizontal-partitioning.md).

## <a name="t-sql-querying"></a>Consulta SQL T
Una vez que ha definido los orígenes de datos y las tablas externas, puede usar regulares cadenas de conexión de SQL Server para conectarse a las bases de datos donde se definen las tablas externas. A continuación, puede ejecutar instrucciones SQL T sobre las tablas externas en esa conexión con las limitaciones que se describen a continuación. Puede encontrar más información y ejemplos de consultas SQL T en los temas de documentación para [particiones horizontales](sql-database-elastic-query-horizontal-partitioning.md) y [verticales partición](sql-database-elastic-query-vertical-partitioning.md).

## <a name="connectivity-for-tools"></a>Conectividad de herramientas
Puede usar regulares cadenas de conexión de SQL Server para conectar las aplicaciones y herramientas de integración de BI o datos para bases de datos con tablas externas. Asegúrese de que se admite SQL Server como origen de datos de la herramienta. Una vez conectado, consulte la base de datos de consulta elásticas y las tablas externas en dicha base de datos como lo haría con cualquier otra base de SQL Server que se conecta con la herramienta.

> [AZURE.IMPORTANT] Autenticación con Azure Active Directory con consultas elásticas no es compatible actualmente.

## <a name="cost"></a>Costo

Consulta elástico se incluye en el coste de las bases de datos de la base de datos de SQL Azure. Tenga en cuenta que topologías ¿dónde están las bases de datos remotas en un centro de datos diferente que el extremo de consulta elásticos son compatibles, pero salida de datos de bases de datos remotas le cobrarán regular [tasas de Azure](https://azure.microsoft.com/pricing/details/data-transfers/).

## <a name="preview-limitations"></a>Limitaciones de vista previa
* Ejecutar la primera consulta elástica puede tardar unos minutos en el nivel de rendimiento estándar. Esta vez es necesaria cargar la funcionalidad de consulta elástico; mejora el rendimiento de carga con niveles superiores de rendimiento.
* Secuencias de comandos de orígenes de datos externos o tablas externas de SSMS o SSDT todavía no es compatible.
* Importar o exportar para SQL DB aún no admite orígenes de datos externos y tablas externas. Si necesita usar importar o exportar, coloque estos objetos antes de exportar y, a continuación, volver a crearlos después de importar.
* Consulta de base de datos elástico actualmente solo es compatible con acceso de solo lectura a tablas externas. Sin embargo, puede utilizar la funcionalidad completa de T SQL en la base de datos donde se define la tabla externa. Esto puede ser útil para, por ejemplo, conservar los resultados temporales mediante, por ejemplo, seleccione < column_list > en < local_table > o definir procedimientos almacenados en la base de datos de consulta elásticos que hacen referencia a tablas externas.
* Excepto nvarchar (max), tipos LOB no son compatibles con las definiciones de tabla externa. Como solución alternativa, puede crear una vista en la base de datos remota que convierte el tipo de línea de negocio en nvarchar (max), definen la tabla externa a través de la vista en lugar de la tabla base y conviértala en el tipo de línea de negocio en las consultas.
* Estadísticas de columna en tablas externas no se admiten actualmente. Estadísticas de tablas son compatibles, pero es necesario crear manualmente.

## <a name="feedback"></a>Comentarios
Comparta sus comentarios sobre su experiencia con elásticas consultas con nosotros en Disqus a continuación, los foros de MSDN o en Stackoverflow. Estamos interesados en todos los tipos de comentarios sobre el servicio (defectos, bordes desiguales, espacios de característica).

## <a name="more-information"></a>Más información

Puede encontrar más información sobre las consultas entre bases de datos y escenarios de partición verticales en los documentos siguientes:

* [Información general de particiones vertical y consultas entre bases de datos](sql-database-elastic-query-vertical-partitioning.md)
* Pruebe nuestro tutorial paso a paso para tener un completo ejemplo de trabajo que se ejecuta en minutos: [Introducción a la consulta de base de datos cruzados (partición vertical)](sql-database-elastic-query-getting-started-vertical.md).


Obtener más información sobre los escenarios de particiones y sharding horizontales está disponible aquí:

* [Información general de particiones y sharding horizontal](sql-database-elastic-query-horizontal-partitioning.md)
* Pruebe nuestro tutorial paso a paso para tener un completo ejemplo de trabajo que se ejecuta en minutos: [Introducción a la consulta de base de datos elástica para particiones horizontales (sharding)](sql-database-elastic-query-getting-started.md).


[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-query-overview/overview.png
[2]: ./media/sql-database-elastic-query-overview/topology1.png
[3]: ./media/sql-database-elastic-query-overview/vertpartrrefdata.png
[4]: ./media/sql-database-elastic-query-overview/verticalpartitioning.png
[5]: ./media/sql-database-elastic-query-overview/horizontalpartitioning.png

<!--anchors-->
