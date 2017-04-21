<properties 
    pageTitle="Conexión de base de datos SQL Azure a Azure búsqueda mediante indizadores | Microsoft Azure | Indizadores" 
    description="Obtenga información sobre cómo extraer datos de la base de datos de SQL Azure a un índice de búsqueda de Azure con indizadores." 
    services="search" 
    documentationCenter="" 
    authors="chaosrealm" 
    manager="pablocas" 
    editor=""/>

<tags 
    ms.service="search" 
    ms.devlang="rest-api" 
    ms.workload="search" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.date="10/27/2016" 
    ms.author="eugenesh"/>

#<a name="connecting-azure-sql-database-to-azure-search-using-indexers"></a>Conexión de base de datos de SQL Azure a utilizar indizadores de búsqueda de Azure

Servicio de búsqueda de Azure es un servicio de búsqueda de nube hospedado que hace que sea fácil proporcionar una experiencia de búsqueda excelente. Antes de buscar, debe rellenar un índice de búsqueda de Azure con los datos. Si los datos residen en una base de datos de SQL Azure, **indizador de búsqueda de Azure para la base de datos de SQL Azure** nueva (o **indizador de SQL Azure** para abreviar) puede automatizar el proceso de indización. Esto significa que tiene menos código para escribir y menos infraestructura debe preocuparse.

Este artículo explica la forma de utilizar indizadores, pero también se describen las características que solo están disponibles con bases de datos de SQL Azure (por ejemplo, integrado seguimiento de cambios). Búsqueda de Azure también es compatible con otros orígenes de datos, como DocumentDB de Azure, almacenamiento de blobs y almacenamiento de la tabla. Si desea ver orígenes de datos adicionales, la compatibilidad de informar en el [foro de comentarios de búsqueda de Azure](https://feedback.azure.com/forums/263029-azure-search/).

## <a name="indexers-and-data-sources"></a>Indizadores y orígenes de datos

Puede instalar y configurar un indizador de SQL Azure usando: 

- Asistente Importar datos en el [portal de Azure](https://portal.azure.com)
- Búsqueda de Azure [.NET SDK](https://msdn.microsoft.com/library/azure/dn951165.aspx)
- [API de REST](http://go.microsoft.com/fwlink/p/?LinkID=528173) de búsqueda de Azure

En este artículo, usaremos la API de REST para mostrar cómo crear y administrar **orígenes de datos**e **indizadores** . 

Un **origen de datos** especifica los datos que indizar credenciales necesarias para tener acceso a los datos y las directivas que eficazmente identifican los cambios en los datos (filas nuevas, modificados o eliminados). Se define como recurso independiente para que se puede usar por varios indizadores.

Un **indizador** es un recurso que se conecta a orígenes de datos con los índices de búsqueda de destino. Se usa un indizador de las siguientes maneras:
 
- Realizar una copia de los datos para rellenar un índice única.
- Actualizar un índice con los cambios del origen de datos en una programación.
- Ejecutar a petición para actualizar un índice según sea necesario. 

## <a name="when-to-use-azure-sql-indexer"></a>Cuándo usar indizador SQL Azure

Dependiendo de varios factores relacionados con los datos, el uso del indizador de SQL Azure puede o no puede ser adecuado. Si los datos se ajusta a los requisitos siguientes, puede usar indizador de SQL Azure: 

- Todos los datos proceden de una sola tabla o vista
    - Si los datos están distribuidos en varias tablas, puede crear una vista y usar Vista con el indizador. Sin embargo, si usa una vista, no podrá usar detección de cambios integrada de SQL Server. Para obtener más información, consulte [esta sección](#CaptureChangedRows). 
- Los tipos de datos usados en el origen de datos son compatibles con el indizador. La mayoría aunque no todos los tipos SQL son compatibles. Para obtener más información, consulte [asignar tipos de datos en la búsqueda de Azure](http://go.microsoft.com/fwlink/p/?LinkID=528105). 
- No necesita cerca actualizaciones en tiempo real en el índice cuando cambia una fila. 
    - El indizador puede volver a indizar la tabla como máximo cada 5 minutos. Si necesitan se reflejan en el índice en segundos o minutos solo los cambios de datos con frecuencia y los cambios, se recomienda utilizar [API de índice de búsqueda de Azure](https://msdn.microsoft.com/library/azure/dn798930.aspx) directamente. 
- Si tiene un conjunto de datos grande y planea ejecutar el indizador en una programación, su esquema nos permite identificar eficazmente cambiado (y eliminados, si procede) filas. Para obtener más detalles, consulte "Capturar cambiado y eliminar filas" más abajo. 
- El tamaño de los campos indizados en una fila no exceda el tamaño máximo de una búsqueda de Azure indización solicitud, que es 16 MB. 

## <a name="create-and-use-an-azure-sql-indexer"></a>Crear y usar un indizador SQL Azure

Primero, cree el origen de datos: 

    POST https://myservice.search.windows.net/datasources?api-version=2015-02-28 
    Content-Type: application/json
    api-key: admin-key
    
    { 
        "name" : "myazuresqldatasource",
        "type" : "azuresql",
        "credentials" : { "connectionString" : "Server=tcp:<your server>.database.windows.net,1433;Database=<your database>;User ID=<your user name>;Password=<your password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30;" },
        "container" : { "name" : "name of the table or view that you want to index" }
    }


Puede obtener la cadena de conexión desde el [Portal de Azure clásica](https://portal.azure.com); Utilice la `ADO.NET connection string` opción.

A continuación, cree el índice de búsqueda de Azure de destino si aún no tiene una. Puede crear un índice con el [portal de interfaz de usuario](https://portal.azure.com) o la [API de índice de crear](https://msdn.microsoft.com/library/azure/dn798941.aspx). Asegúrese de que el esquema del índice de destino es compatible con el esquema de la tabla de origen: consulte [asignación entre tipos de datos de búsqueda SQL y Azure](#TypeMapping).

Por último, asigne un nombre y la referencia al índice de origen y destino de datos para crear el indizador:

    POST https://myservice.search.windows.net/indexers?api-version=2015-02-28 
    Content-Type: application/json
    api-key: admin-key
    
    { 
        "name" : "myindexer",
        "dataSourceName" : "myazuresqldatasource",
        "targetIndexName" : "target index name"
    }

Un indizador creado de esta forma no tiene una programación. Se ejecuta automáticamente una vez cuando se crea. Puede ejecutarla en cualquier momento mediante una solicitud de **Ejecutar indizador** :

    POST https://myservice.search.windows.net/indexers/myindexer/run?api-version=2015-02-28 
    api-key: admin-key

Puede personalizar varios aspectos de comportamiento de indizador, como el tamaño del lote y se pueden omitir cuántos documentos antes de que se produce un error de ejecución del indizador. Para obtener más información, consulte [Crear API de indizador](https://msdn.microsoft.com/library/azure/dn946899.aspx).
 
Debe permitir que los servicios de Azure para conectarse a la base de datos. Para obtener instrucciones sobre cómo hacerlo, vea [Conectar de Azure](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure) .

Para supervisar el estado del indizador y ejecución historial (número de elementos indizados, errores, etc.), utilice una solicitud de **estado del indizador** : 

    GET https://myservice.search.windows.net/indexers/myindexer/status?api-version=2015-02-28 
    api-key: admin-key

La respuesta debe ser similar al siguiente: 

    {
        "@odata.context":"https://myservice.search.windows.net/$metadata#Microsoft.Azure.Search.V2015_02_28.IndexerExecutionInfo",
        "status":"running",
        "lastResult": {
            "status":"success",
            "errorMessage":null,
            "startTime":"2015-02-21T00:23:24.957Z",
            "endTime":"2015-02-21T00:36:47.752Z",
            "errors":[],
            "itemsProcessed":1599501,
            "itemsFailed":0,
            "initialTrackingState":null,
            "finalTrackingState":null 
        },
        "executionHistory":
        [
            {
                "status":"success",
                "errorMessage":null,
                "startTime":"2015-02-21T00:23:24.957Z",
                "endTime":"2015-02-21T00:36:47.752Z",
                "errors":[],
                "itemsProcessed":1599501,
                "itemsFailed":0,
                "initialTrackingState":null,
                "finalTrackingState":null 
            },
            ... earlier history items 
        ]
    }

Historial de ejecución contiene 50 de ejecuciones recientemente completadas, que se ordenan en orden cronológico inverso (de modo que la última ejecución venga primera en la respuesta). Encontrará información adicional sobre la respuesta en [Obtener estado del indizador](http://go.microsoft.com/fwlink/p/?LinkId=528198)

## <a name="run-indexers-on-a-schedule"></a>Ejecutar indizadores en una programación

También puede organizar el indizador ejecuten periódicamente en una programación. Para ello, agregue la propiedad de **programación** al crear o actualizar el indizador. El ejemplo siguiente muestra una solicitud de SUPERPONER para actualizar el indizador:

    PUT https://myservice.search.windows.net/indexers/myindexer?api-version=2015-02-28 
    Content-Type: application/json
    api-key: admin-key 

    { 
        "dataSourceName" : "myazuresqldatasource",
        "targetIndexName" : "target index name",
        "schedule" : { "interval" : "PT10M", "startTime" : "2015-01-01T00:00:00Z" }
    }

Se requiere el parámetro de **intervalo** . El intervalo se refiere a la hora entre el inicio de dos ejecuciones del indizador consecutivos. El intervalo más pequeño permitido es 5 minutos. la más larga es un día. Debe tener el formato como un valor de "dayTimeDuration" XSD (un subconjunto de un valor de [duración ISO 8601](http://www.w3.org/TR/xmlschema11-2/#dayTimeDuration) restringido). El modelo de esto es: `P(nD)(T(nH)(nM))`. Ejemplos: `PT15M` para cada 15 minutos, `PT2H` para cada 2 horas.

La **hora de inicio** de opcional indica cuándo deben comenzar las ejecuciones programadas. Si se omite, se utiliza la hora UTC actual. En este momento puede ser en el pasado, en el que está programada la primera ejecución de mayúsculas y minúsculas como si se ha ejecutado el indizador continuamente desde la hora de inicio.  

Solo una ejecución de un indizador se puede ejecutar en un momento. Si está ejecutando un indizador cuando está programada su ejecución, la ejecución se retrasa hasta la próxima hora programada.

Veamos un ejemplo que esté más concreto. Supongamos que nos la programación por hora siguiente configurada: 

    "schedule" : { "interval" : "PT1H", "startTime" : "2015-03-01T00:00:00Z" }

Esto es lo que ocurre: 

1. La primera ejecución del indizador se inicia en o alrededor del 1 de marzo de 2015 12:00 a.m. UTC.
1. Supongamos que esta ejecución tarda 20 minutos (o en cualquier momento menos de 1 hora).
1. La segunda ejecución se inicia en o alrededor del 1 de marzo de 2015 1:00 a.m. 
1. Ahora, supongamos que esta ejecución tarda más de una hora, por ejemplo, 70 minutos – para que se complete alrededor 2:10 a.m.
1. Es ahora 2:00 a.m., hora de la tercera ejecución iniciar. Sin embargo, dado que la segunda ejecución de 1 a.m. aún está en ejecución, se omite la ejecución terceros. La tercera ejecución empieza a las 3 a.m.

Puede agregar, cambiar o eliminar una programación para un indizador existente mediante una solicitud de **poner indizador** . 

<a name="CaptureChangedRows">, /a >
## <a name="capturing-new-changed-and-deleted-rows"></a>Captura de nuevo, ha cambiado y filas eliminadas

Si la tabla tiene muchas filas, debe utilizar una directiva de detección de cambio de datos. Detección de cambios permite una recuperación eficaz de las filas nuevas o modificadas sin tener que volver a indizar la tabla completa.

### <a name="sql-integrated-change-tracking-policy"></a>Directiva de seguimiento de cambios integrados de SQL

Si la base de datos SQL admite [el seguimiento de cambios](https://msdn.microsoft.com/library/bb933875.aspx), se recomienda utilizar **SQL integrado cambiar directiva de seguimiento**. Esta es la directiva más eficaz. Además, permite la búsqueda de Azure para identificar las filas eliminadas sin tener que agregar una columna explícitas "eliminar suaves" a la tabla.

Seguimiento de cambios integrado es compatible comenzando con las siguientes versiones de base de datos de SQL Server:
 
- SQL Server 2008 R2 y posteriores, si está usando SQL Server en máquinas virtuales de Azure. 
- Azure SQL base de datos V12, si está utilizando la base de datos de SQL Azure.

Cuando usando directivas de seguimiento de cambios SQL integrado no especifica una directiva de detección de eliminación de datos independiente: esta directiva tiene compatibilidad integrada para identificar las filas eliminadas.

Esta directiva sólo se puede utilizar con tablas; no se pueden utilizar con las vistas. Debe habilitar el seguimiento de cambios para la tabla que se usa para poder usar esta directiva. Para obtener instrucciones, vea [Habilitar y deshabilitar el seguimiento de cambios](https://msdn.microsoft.com/library/bb964713.aspx) . 

Para utilizar esta directiva, crear o actualizar el origen de datos similar a esta:
 
    { 
        "name" : "myazuresqldatasource",
        "type" : "azuresql",
        "credentials" : { "connectionString" : "connection string" },
        "container" : { "name" : "table or view name" }, 
        "dataChangeDetectionPolicy" : {
           "@odata.type" : "#Microsoft.Azure.Search.SqlIntegratedChangeTrackingPolicy" 
      }
    }

<a name="HighWaterMarkPolicy"></a>
### <a name="high-water-mark-change-detection-policy"></a>Directiva de detección de cambio de marca de agua

Mientras se recomienda la directiva de seguimiento de cambios integrado de SQL, puede usarse con tablas, vistas no. Si está utilizando una vista, considere la posibilidad de usar la directiva de marca de agua. Puede usar esta directiva si la tabla o vista contiene una columna que cumple los criterios siguientes:

- Todas las inserciones para especificar un valor para la columna. 
- Todas las actualizaciones a un elemento también cambian el valor de la columna. 
- Aumenta el valor de esta columna con cada cambio.
- Consultas con la siguiente donde y cláusulas ORDER BY se pueden ejecutar eficientemente: `WHERE [High Water Mark Column] > [Current High Water Mark Value] ORDER BY [High Water Mark Column]`.

Por ejemplo, una columna indizada **rowversion** es un candidato ideal para la columna de marca de agua. Para utilizar esta directiva, crear o actualizar el origen de datos similar a esta: 

    { 
        "name" : "myazuresqldatasource",
        "type" : "azuresql",
        "credentials" : { "connectionString" : "connection string" },
        "container" : { "name" : "table or view name" }, 
        "dataChangeDetectionPolicy" : {
           "@odata.type" : "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy",
           "highWaterMarkColumnName" : "[a row version or last_updated column name]" 
      }
    }

> [AZURE.WARNING] Si la tabla de origen no tiene un índice en la columna de marca de agua, consultas utilizadas por el indizador SQL podrán tiempo de espera. En particular, el `ORDER BY [High Water Mark Column]` cláusula requiere un índice para ejecutar eficientemente cuando la tabla contiene el número de filas. 

Si se producen errores de tiempo de espera, puede usar el `queryTimeout` configuración del indizador para establecer el tiempo de espera de la consulta en un valor mayor que el tiempo de espera de 5 minutos de forma predeterminada. Por ejemplo, para establecer el tiempo de espera en 10 minutos, crear o actualizar el indizador con la siguiente configuración: 

    {
      ... other indexer definition properties
     "parameters" : { 
            "configuration" : { "queryTimeout" : "00:10:00" } }
    }

También puede deshabilitar la `ORDER BY [High Water Mark Column]` cláusula. Sin embargo, esto no se recomienda ya que si se interrumpe la ejecución del indizador por un error, el indizador debe volver a procesar todas las filas si se ejecuta más adelante - aunque el indizador ya ha procesado casi todas las filas en el momento en que se interrumpió. Para deshabilitar la `ORDER BY` cláusula, use la `disableOrderByHighWaterMarkColumn` configuración en la definición del indizador:  

    {
     ... other indexer definition properties
     "parameters" : { 
            "configuration" : { "disableOrderByHighWaterMarkColumn" : true } }
    }

### <a name="soft-delete-column-deletion-detection-policy"></a>Directiva de detección de eliminación de columna Eliminar suave

Cuando se eliminan filas de la tabla de origen, que probablemente que desea eliminar las filas del índice de búsqueda. Si utiliza la política de seguimiento de cambios SQL integrado, esto está teniendo en cuenta para usted. Sin embargo, el cambio de marca de agua política de seguimiento no ayudarle con filas eliminadas. ¿Qué hacer? 

Si física se quitarán las filas de la tabla, búsqueda de Azure no tiene forma deducir la presencia de registros que ya no existe.  Sin embargo, puede usar esta técnica "suaves eliminar" lógicamente eliminar filas sin quitarlos de la tabla. Agregar una columna a las filas de tabla o vista y marcar como eliminados con esa columna.

Cuando se usa la técnica de eliminar suaves, puede especificar la suaves eliminar la directiva de manera al crear o actualizar el origen de datos: 

    { 
        …, 
        "dataDeletionDetectionPolicy" : { 
           "@odata.type" : "#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy",
           "softDeleteColumnName" : "[a column name]", 
           "softDeleteMarkerValue" : "[the value that indicates that a row is deleted]" 
        }
    }

El **softDeleteMarkerValue** debe ser una cadena: utilice la representación de cadena de su valor real. Por ejemplo, si tiene una columna de enteros donde filas eliminadas se marcan con el valor 1, use `"1"`. Si tiene una columna BIT donde filas eliminadas se marcan con el valor booleano true, use `"True"`. 

<a name="TypeMapping"></a>
## <a name="mapping-between-sql-data-types-and-azure-search-data-types"></a>Asignación entre tipos de datos de SQL y tipos de datos de búsqueda de Azure

|Tipo de datos SQL | Permite tipos de campo de índice de destino |Notas 
|------|-----|----|
|bit|Edm.Boolean, Edm.String| |
|int, smallint, tinyint |Edm.Int32, Edm.Int64, Edm.String| |
| bigint | Edm.Int64, Edm.String | |
| real, flotante |Edm.Double, Edm.String | |
| smallmoney, numérico decimal de dinero | Edm.String| Búsqueda de Azure no admite la conversión de tipos decimales en Edm.Double porque esto podría perder precisión |
| Char, nchar, varchar, nvarchar | Edm.String<br/>Collection(EDM.String)|Una cadena SQL puede utilizarse para rellenar un campo de Collection(Edm.String) si la cadena representa una matriz JSON de cadenas:`["red", "white", "blue"]` | 
|smalldatetime, la fecha y hora, la fecha, datetime2, datetimeoffset |Edm.DateTimeOffset, Edm.String| |
|uniqueidentifer y su valor | Edm.String | |
|geografía | Edm.GeographyPoint | Se admiten sólo geografía instancias del tipo de punto de 4326 SRID (que es el valor predeterminado) | | 
|rowversion| N/A. |Columnas de versión de fila no pueden almacenarse en el índice de búsqueda, pero se pueden usar para el seguimiento de cambios | |
| hora, intervalo de tiempo, binario, varbinary, imagen, xml, geometría, tipos de CLR | N/A. |No compatible |

## <a name="configuration-settings"></a>Opciones de configuración

Indizador SQL expone diversas opciones de configuración: 

|Configuración |Tipo de datos | Propósito | Valor predeterminado |
|--------|----------|---------|---------------|
| queryTimeout | cadena | Establece el tiempo de espera de ejecución de consultas SQL | 5 minutos ("00: 05:00") |
| disableOrderByHighWaterMarkColumn | BOOL | Hace que la consulta SQL utilizada por la directiva de marca de agua para omitir la cláusula ORDER BY. Vea [la directiva de marca de agua](#HighWaterMarkPolicy) | falso | 

Esta configuración se usa en la `parameters.configuration` objeto en la definición del indizador. Por ejemplo, para establecer el tiempo de espera de la consulta a 10 minutos, crear o actualizar el indizador con la siguiente configuración: 

    {
      ... other indexer definition properties
     "parameters" : { 
            "configuration" : { "queryTimeout" : "00:10:00" } }
    }

## <a name="frequently-asked-questions"></a>Preguntas más frecuentes

**Q:** ¿Puedo usar indizador de SQL Azure con bases de datos SQL de máquinas virtuales de IaaS en Azure?

R: Sí. Sin embargo, debe permitir el servicio de búsqueda para conectarse a la base de datos. Para obtener más información, vea [Configurar una conexión de un indizador de búsqueda de Azure a SQL Server en una máquina virtual de Azure](search-howto-connecting-azure-sql-iaas-to-azure-search-using-indexers.md).


**Q:** ¿Usar indizador de SQL Azure con SQL bases de datos local? 

R: no recomienda ni admite esto, como esta, se requeriría abrir las bases de datos para el tráfico de Internet. 

**Q:** ¿Puedo usar indizador de SQL Azure con bases de datos que no sea de SQL Server en IaaS en Azure? 

R: no se admite en este escenario, porque no hemos probado el indizador con las bases de datos que no sea de SQL Server.  

**Q:** ¿Puedo crear varios indizadores que se ejecuta en una programación? 

R: Sí. Sin embargo, solo indizador se puede ejecutar en un nodo al mismo tiempo. Si necesita varios indizadores ejecutándose a la vez, considere la posibilidad de escalado de su servicio de búsqueda más de una unidad de búsqueda. 

**Q:** ¿Ejecutar un indizador afecta la carga de trabajo de la consulta? 

R: Sí. Indizador se ejecuta en uno de los nodos en el servicio de búsqueda y recursos de ese nodo comparten indización y servir el tráfico de consultas y otras solicitudes de API. Si ejecuta cargas de trabajo intensivo de indexación y consulta y encontrar una alta velocidad de 503 errores o aumentar los tiempos de respuesta, considere la posibilidad de escalado de su servicio de búsqueda.
