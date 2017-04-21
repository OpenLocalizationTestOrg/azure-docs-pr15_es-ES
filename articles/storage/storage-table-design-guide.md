<properties
    pageTitle="Guía de diseño de tabla de almacenamiento de Azure | Microsoft Azure"
    description="Diseño Scalable y tablas de rendimiento en el almacenamiento de tablas de Azure"
    services="storage"
    documentationCenter="na"
    authors="jasonnewyork" 
    manager="tadb"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="storage"
    ms.date="09/22/2016"
    ms.author="jahogg"/>

# <a name="azure-storage-table-design-guide-designing-scalable-and-performant-tables"></a>Guía de diseño de la tabla de almacenamiento de Azure: Diseñar Scalable y tablas de rendimiento

## <a name="overview"></a>Información general

Diseño de scalable y tablas de rendimiento que debe tener en cuenta varios factores como el costo, rendimiento y escalabilidad. Si previamente ha diseñado esquemas para bases de datos relacionales, estas consideraciones será familiares pero, si bien hay algunas similitudes entre los modelos relacionales y el modelo de almacenamiento del servicio de tabla de Azure, también hay muchas diferencias importantes. Normalmente, estas diferencias producir muy diferentes diseños que puede parecer intuitivo o incorrecta a alguien que esté familiarizado con bases de datos relacionales, pero que realice ninguna idea si va a diseñar un almacén clave y valor, como el servicio de la tabla de Azure. Muchos de sus diferencias de diseño reflejará el hecho de que el servicio de la tabla está diseñado para admitir aplicaciones de escala de la nube que pueden contener millones de entidades (filas en la terminología de bases de datos relacionales) de datos o los conjuntos de datos que debe admitir volúmenes transacción muy alta: por lo tanto, tiene que pensar cómo almacena los datos de forma diferente y comprender cómo funciona el servicio de la tabla. Un almacén datos bien diseñado puede activar la solución escalar mucho más (y con un coste inferior) de una solución que use una base de datos relacional. Esta guía le ayuda con estos temas.  

## <a name="about-the-azure-table-service"></a>Información sobre el servicio de la tabla de Azure

En esta sección se resalta algunas de las características clave del servicio de tabla que son especialmente relevantes para diseño de rendimiento y escalabilidad. Si está familiarizado con el almacenamiento de Azure y el servicio de la tabla, primero lea [Introducción a Microsoft Azure almacenamiento](storage-introduction.md) y [empezar a trabajar con el almacenamiento de tablas de Azure con .NET](storage-dotnet-how-to-use-tables.md) antes de leer el resto de este artículo. Aunque el enfoque de esta guía está en el servicio de la tabla, se incluyen algunos discusión de los servicios de cola de Azure y Blob y cómo puede usar junto con el servicio de tabla en una solución.  

¿Qué es el servicio de tabla? Como cabría esperar del nombre, el servicio de la tabla utiliza un formato tabular para almacenar datos. En la terminología estándar, cada fila de la tabla representa una entidad y las columnas almacenan las propiedades de la entidad. Cada entidad tiene un par de claves para identificar de forma exclusiva y, a continuación, actualiza una columna de marca de tiempo que usa el servicio de la tabla para realizar el seguimiento cuando la entidad por última vez (Esto ocurre automáticamente y no se puede sobrescribir manualmente la marca de tiempo con un valor arbitrario). El servicio de tabla usa esta marca de hora de última modificación (LMT) para administrar la simultaneidad optimista.  

>[AZURE.NOTE] Las operaciones de API de REST de servicio de tabla también devolverán un valor de **ETag** que se deriva de la marca de hora de última modificación (LMT). En este documento se utilizará los términos de ETag y LMT indistintamente porque hacen referencia a los mismos datos subyacentes.  

En el ejemplo siguiente se muestra un diseño de tabla simple para almacenar entidades de departamento y empleados. Muchos de los ejemplos que se muestra más adelante en esta guía se basan en este diseño simple.  

<table>
<tr>
<th>PartitionKey</th>
<th>RowKey</th>
<th>Marca de tiempo</th>
<th></th>
</tr>
<tr>
<td>De marketing</td>
<td>00001</td>
<td>2014-08-22T00:50:32Z</td>
<td>
<table>
<tr>
<th>Nombre</th>
<th>Apellido</th>
<th>Edad</th>
<th>Correo electrónico</th>
</tr>
<tr>
<td>Felipe</td>
<td>Salón</td>
<td>34</td>
<td>donh@contoso.com</td>
</tr>
</table>
</tr>
<tr>
<td>De marketing</td>
<td>00002</td>
<td>2014-08-22T00:50:34Z</td>
<td>
<table>
<tr>
<th>Nombre</th>
<th>Apellido</th>
<th>Edad</th>
<th>Correo electrónico</th>
</tr>
<tr>
<td>Junio</td>
<td>CaO</td>
<td>47</td>
<td>junc@contoso.com</td>
</tr>
</table>
</tr>
<tr>
<td>De marketing</td>
<td>Departamento</td>
<td>2014-08-22T00:50:30Z</td>
<td>
<table>
<tr>
<th>Nombredepartamento</th>
<th>EmployeeCount</th>
</tr>
<tr>
<td>De marketing</td>
<td>153</td>
</tr>
</table>
</td>
</tr>
<tr>
<td>Ventas</td>
<td>00010</td>
<td>2014-08-22T00:50:44Z</td>
<td>
<table>
<tr>
<th>Nombre</th>
<th>Apellido</th>
<th>Edad</th>
<th>Correo electrónico</th>
</tr>
<tr>
<td>Ana</td>
<td>Kwok</td>
<td>23</td>
<td>kenk@contoso.com</td>
</tr>
</table>
</td>
</tr>
</table>


Hasta ese momento, esto es muy similar a una tabla en una base de datos relacional con las diferencias clave que las columnas obligatorio y la capacidad de almacenar varios tipos de entidad en la misma tabla. Además, cada una de las propiedades definidas por el usuario como el **nombre** o la **edad** tiene un tipo de datos, como entero o cadena, simplemente como una columna en una base de datos relacional. Aunque a diferencia de una base de datos relacional, la naturaleza de menos de esquema del servicio de tabla significa que una propiedad no necesita tener los mismos datos escriba en cada entidad. Para almacenar tipos de datos complejos en una sola propiedad, debe usar un formato de número de serie como JSON o XML. Para obtener más información sobre los tipos de datos de servicio compatible como tabla, intervalos de fechas admitidas, nomenclatura reglas y límites de tamaño, vea [Descripción del modelo de datos de servicio de tabla](http://msdn.microsoft.com/library/azure/dd179338.aspx).

Como verá, es fundamental al diseño de tabla buena su elección de **PartitionKey** y **RowKey** . Cada entidad almacenado en una tabla debe tener una combinación única de **PartitionKey** y **RowKey**. Como con claves en una tabla de base de datos relacionales, los valores **PartitionKey** y **RowKey** están indizados para crear un índice agrupado que permita búsquedas rápidas; Sin embargo, el servicio de tabla crear los índices secundarios para que estas son las propiedades indizadas sólo dos (algunos de los patrones de describen cómo evitar esta limitación aparente presentación con una versión posterior).  

Una tabla está formada por una o más particiones y como verá, muchas de las decisiones de diseño que se alrededor eligiendo una adecuado **PartitionKey** y **RowKey** para optimizar su solución. Una solución podría constar de solo una sola tabla que contiene todas sus entidades organizadas por particiones, pero normalmente tendrá una solución a varias tablas. Tablas ayudarán a organizar sus entidades lógicamente, ayudarle a administrar el acceso a los datos mediante las listas de control de acceso, y puede colocar una tabla completa mediante una operación de almacenamiento único.  

### <a name="table-partitions"></a>Particiones de tabla  
El nombre de la cuenta, el nombre de la tabla y **PartitionKey** juntos identifican la partición dentro del servicio de almacenamiento donde el servicio de tabla almacena la entidad. Además de ser parte del esquema de direcciones para entidades, particiones definen un ámbito para las transacciones (consulte [Las transacciones de grupo de entidad](#entity-group-transactions) ) y la base de cómo se ajusta el servicio de la tabla. Para obtener más información sobre las particiones vea [destinos de rendimiento y escalabilidad de almacenamiento de Azure](storage-scalability-targets.md).  

En el servicio de la tabla, un nodo individual servicios uno o más completar las particiones y las escalas de servicio Equilibrio de carga dinámicamente particiones en todos los nodos. Si un nodo está bajo carga, el servicio de tabla puede *dividir* el rango de particiones atendidas por ese nodo en distintos nodos; Cuando desaparezca el tráfico, el servicio puede *Combinar* los intervalos de partición de nodos silenciosos en un único nodo.  

Para obtener más información acerca de los detalles internos del servicio de tabla y, en particular cómo el servicio administra las particiones, consulte el documento [Microsoft Azure almacenamiento: A altamente disponible almacenamiento servicio de nube con texto en negrita coherencia](http://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx).  

### <a name="entity-group-transactions"></a>Transacciones de grupo de la entidad
En el servicio de la tabla, las transacciones del grupo de entidad (EGTs) son el mecanismo solo integrado para realizar actualizaciones atómicas entre varias entidades. EGTs también se conoce como *transacciones por lotes* en la documentación. EGTs sólo funcionan en las entidades almacenadas en la misma partición (compartir la misma clave de partición de una tabla), lo siempre que necesite un comportamiento de transacciones atómico entre varias entidades que tenga que asegurarse de que dichas entidades están en la misma partición. Suele ser un motivo para mantener varios tipos de entidad en la misma tabla (y partición) y no usa varias tablas para los tipos de entidad diferente. Una sola EGT puede funcionar en un máximo de 100 entidades.  Si envía varios EGTs simultáneas para procesamiento es importante garantizar que esos EGTs no funcionan en las entidades que sean comunes en EGTs como procesamiento de lo contrario puede retrasarse.

EGTs también introducir un equilibrio posible para evaluar en el diseño: uso de particiones más aumentará la escalabilidad de la aplicación porque Azure tiene más posibilidades de equilibrio de carga solicitudes entre nodos, pero esto podría limitar la capacidad de su aplicación para realizar transacciones atómicas y mantener la coherencia segura para sus datos. Además, hay destinos escalabilidad específico en el nivel de una partición que podría limitar el rendimiento de transacciones puede esperar para un único nodo: para obtener más información acerca de los objetivos de escalabilidad para las cuentas de almacenamiento de Azure y el servicio de la tabla, vea [destinos de rendimiento y escalabilidad de almacenamiento de Azure](storage-scalability-targets.md). Las secciones posteriores de esta guía tratan varios diseño estrategias que ayudan a administración compensaciones como esta y explican cómo para elegir la clave de partición según las necesidades específicas de la aplicación cliente.  

### <a name="capacity-considerations"></a>Consideraciones de capacidad
En la tabla siguiente incluye algunos de los valores de clave a tener en cuenta al diseñar una solución de servicio de tabla:  

|Capacidad total de una cuenta de almacenamiento de Azure|500 TB|
|------------------------------------------|------|
|Número de tablas en una cuenta de almacenamiento de Azure | Limitado sólo por la capacidad de la cuenta de almacenamiento |
|Número de particiones en una tabla | Limitado sólo por la capacidad de la cuenta de almacenamiento |
|Número de entidades de una partición | Limitado sólo por la capacidad de la cuenta de almacenamiento|
|Tamaño de una entidad individual | Hasta 1 MB con un máximo de 255 propiedades (incluidos los **PartitionKey**, **RowKey**y **marca de tiempo**) |
|Tamaño de la **PartitionKey** | Cadena hasta 1 KB de tamaño |
| Tamaño de la **RowKey** | Cadena hasta 1 KB de tamaño |
|Tamaño de una transacción de grupo de la entidad | Una transacción puede incluir un máximo de 100 entidades y la carga debe ser inferior a 4 MB de tamaño. Un EGT solo puede actualizar una entidad de una vez. |

Para obtener más información, vea la [Descripción del modelo de datos de servicio de tabla](http://msdn.microsoft.com/library/azure/dd179338.aspx).  

### <a name="cost-considerations"></a>Consideraciones de costo  
Almacenamiento de tablas es relativamente económico, pero debe incluir las estimaciones de costo para el uso de la capacidad y la cantidad de transacciones como parte de la evaluación de cualquier solución que utilice el servicio de la tabla. Sin embargo, en muchos escenarios almacenar datos duplicados o no normalizados para mejorar el rendimiento o la escalabilidad de su solución es un enfoque válido para tomar. Para obtener más información sobre precios, consulte [Precios de almacenamiento de Azure](https://azure.microsoft.com/pricing/details/storage/).  

## <a name="guidelines-for-table-design"></a>Directrices para el diseño de tabla  
Estas listas resumen algunas de las directrices de clave que debe tener en cuenta al diseñar las tablas y, a continuación, en esta guía se abordará todo con más detalle más adelante en. Estas directrices son muy diferentes de las directrices que normalmente seguiría para el diseño de base de datos relacional.  

Diseño de la solución de servicio de la tabla se *Lea* eficaz:

-   ***Diseño para las consultas en aplicaciones de lectura visible.*** Cuando va a diseñar las tablas, piense en las consultas (especialmente la latencia de las minúsculas) que se ejecutará antes de pensar cómo se actualizan las entidades. Esto suele ser el resultado de una solución eficiente y eficaz.  
-   ***Especificar PartitionKey y RowKey en las consultas.*** *Consultas de punto* como estos son las consultas de servicio de tabla más eficaces.  
-   ***Considere almacenar duplicados de entidades.*** Almacenamiento de tablas es económico así que considere almacenar la misma entidad varias veces (con claves diferentes) para habilitar las consultas más eficaces.  
-   ***Considere la posibilidad de desnormalizar los datos.*** Almacenamiento de tablas es económico así que considere desnormalizar los datos. Por ejemplo, almacenar entidades de resumen para que las consultas para agregar datos solo necesitan tener acceso a una sola entidad.  
-   ***Use los valores de clave compuestos.*** Las teclas sólo que tiene son **PartitionKey** y **RowKey**. Por ejemplo, use los valores de clave compuestos para habilitar rutas de acceso clave alternativa a entidades.  
-   ***Usar proyección de consultas.*** Puede reducir la cantidad de datos que se transfiere por la red mediante el uso de consultas que seleccionan solo los campos que necesite.  

Diseño de la solución de servicio de la tabla que *escribir* eficaz:  

-   ***No crear particiones de acceso rápidas.*** Seleccione las teclas que le permite extender las convocatorias a varias particiones en cualquier punto de tiempo.  
-   ***Evite los picos de tráfico.*** Equilibrar el tráfico durante un período de tiempo razonable y evitar picos de tráfico.
-   ***No necesariamente se cree una tabla independiente para cada tipo de entidad.*** Cuando necesite transacciones atómicas a través de los tipos de entidad, puede almacenar estos varios tipos de entidad en la misma partición en la misma tabla.
-   ***Considerar el rendimiento máximo que debe lograr.*** Debe tener en cuenta los objetivos de escalabilidad para el servicio de la tabla y asegurarse de que el diseño no provocará que supere ellos.  

Al leer a esta guía, verá ejemplos en los que todos estos principios poner en práctica.  

## <a name="design-for-querying"></a>Diseño de consulta  
Se pueden leer intensa, intensiva de escritura o una combinación de las dos soluciones de servicio de la tabla. En esta sección se centra en los aspectos a tener en cuenta al diseñar su servicio de tabla para admitir operaciones de lectura de forma eficaz. Normalmente, un diseño que es compatible con operaciones de lectura eficaz también es eficaz para operaciones de escritura. Sin embargo, hay otras consideraciones a tener en cuenta al diseñar para admitir operaciones de escritura, descritas en la siguiente sección, [diseño para modificación de los datos](#design-for-data-modification).

Un buen punto de partida para diseñar su solución de servicio de tabla para que pueda leer datos de forma eficaz es preguntar "¿qué consultas mi aplicación necesitará ejecutar para recuperar los datos que necesita desde el servicio de tabla?"  

>[AZURE.NOTE] Con el servicio de la tabla, es importante obtener el diseño correcto adelantado porque es difícil y caro cambiarlo más adelante. Por ejemplo, en una base de datos relacional es con frecuencia posible para solucionar los problemas de rendimiento agregando índices a una base de datos: no es una opción con el servicio de la tabla.  

En esta sección se centra en los aspectos clave que debe hacer frente al diseñar las tablas para las consultas. Los temas tratados en esta sección se incluyen:

- [¿Cómo afecta a su elección de PartitionKey y RowKey rendimiento de la consulta](#how-your-choice-of-partitionkey-and-rowkey-impacts-query-performance)
- [Elegir un PartitionKey apropiado](#choosing-an-appropriate-partitionkey)
- [Optimización de consultas para el servicio de tabla](#optimizing-queries-for-the-table-service)
- [Ordenar datos en el servicio de tabla](#sorting-data-in-the-table-service)

### <a name="how-your-choice-of-partitionkey-and-rowkey-impacts-query-performance"></a>¿Cómo afecta a su elección de PartitionKey y RowKey rendimiento de la consulta  

Los siguientes ejemplos se suponen que el servicio de la tabla está almacenando entidades de empleados con la estructura siguiente (la mayoría de los ejemplos omite la propiedad de **marca de tiempo** para claridad):  

|*Nombre de columna* |*Tipo de datos*|
|--------------|-----------|
|**PartitionKey** (Nombre de departamento)|Cadena|
|**RowKey** (Id de empleado)|Cadena|
|**Nombre**|Cadena|
|**Apellido**|Cadena|
|**Edad**|Entero|
|**Dirección de correo electrónico**|Cadena|

La [tabla Azure Introducción al servicio](#overview) de la sección anterior, describe algunas de las características clave del servicio de Azure tabla que tengan una influencia directa en diseñar la consulta. Estos dan como resultado las siguientes pautas para diseñar consultas de servicio de tabla. Tenga en cuenta que la sintaxis de filtro utilizada en los ejemplos siguientes del servicio de tabla API de REST, para obtener más información vea [Entidades de consulta](http://msdn.microsoft.com/library/azure/dd179421.aspx).  

-   Una ***Consulta de punto*** es la búsqueda más eficaz usar y se recomienda que se usará para las búsquedas de gran volumen o que requieren menor latencia de búsquedas. Una consulta puede utilizar los índices para localizar una entidad individual con gran eficacia especificando valores de los **PartitionKey** y **RowKey** . Por ejemplo: $filter = (PartitionKey EC 'Sales') y (RowKey EC '2')  
-   Segunda mejor es un ***Rango de consulta*** que usa el **PartitionKey** y filtros en un rango de valores de **RowKey** para devolver más de una entidad. El valor de **PartitionKey** identifica una partición específica y los valores de **RowKey** identifican un subconjunto de las entidades de esa partición. Por ejemplo: $filter = PartitionKey EC 'Ventas y RowKey Electric' y RowKey lt ' t '  
-   Tercera lo mejor es una ***Partición de análisis*** que usa el **PartitionKey** y filtros en otra propiedad no clave y que puede devolver más de una entidad. El valor de **PartitionKey** identifica una partición específica y, a continuación, seleccione los valores de propiedad para un subconjunto de las entidades de esa partición. Por ejemplo: $filter = PartitionKey EC apellido y 'Ventas' EC 'García'  
-   Un ***Análisis de tabla*** no incluye la **PartitionKey** y es muy eficaz porque busca todas las particiones que componen la tabla por separado para cualquier entidades coincidentes. Realizará un recorrido de tabla independientemente de si el filtro usa la **RowKey**. Por ejemplo: $filter = apellido EC 'Perez'  
-   Consultas que devuelven varias entidades devuelven que se ordenen en orden **PartitionKey** y **RowKey** . Para evitar volver a ordenar las entidades en el cliente, elija **RowKey** que define el criterio de ordenación más comunes.  

Tenga en cuenta que utiliza un "**o**" para especificar un filtro basado en **RowKey** valores da como resultado un análisis de partición y no se tratará como una consulta de intervalo. Por lo tanto, debe evitar las consultas que utilizan filtros como por ejemplo: $filter = PartitionKey EC 'Sales' y (RowKey EC '121' o RowKey EC '322')  

Para obtener ejemplos de código de cliente que utilizan la biblioteca de cliente de almacenamiento para ejecutar consultas eficaces, consulte:  

-   [Ejecutar una consulta de punto de uso de la biblioteca de cliente de almacenamiento](#executing-a-point-query-using-the-storage-client-library)
-   [Recuperar varias entidades con LINQ](#retrieving-multiple-entities-using-linq)
-   [Proyección de servidor](#server-side-projection)  

Para obtener ejemplos de código de cliente que puede controlar varios tipos de entidad almacenados en la misma tabla, consulte:  

-   [Trabajar con tipos de entidad heterogéneos](#working-with-heterogeneous-entity-types)  

### <a name="choosing-an-appropriate-partitionkey"></a>Elegir un PartitionKey apropiado  

La opción de **PartitionKey** debería equilibrar la necesidad que permite el uso de EGTs (para garantizar la coherencia) contra el requisito de distribuir las entidades entre varias particiones (para garantizar una solución scalable).  

En un extremo, podría almacenar todos sus entidades en una sola partición, pero esto puede limitar la escalabilidad de su solución e impedir que el servicio de la tabla que permite a las solicitudes de equilibrio de carga. En el otro extremo, puede almacenar una entidad por partición, que sería muy scalable y que permite que el servicio de la tabla a las solicitudes de equilibrio de carga, pero que podría impedir que use transacciones de grupo de la entidad.  

Un ideal **PartitionKey** es uno que le permite usar consultas eficaces y que tiene suficientes particiones para asegurarse de que su solución es scalable. Por lo general, encontrará que las entidades tendrá una propiedad adecuada que distribuye las entidades en particiones suficientes.

>[AZURE.NOTE] Por ejemplo, en un sistema que almacena información acerca de los usuarios o empleados, ID de usuario puede ser una buena PartitionKey. Puede que tenga varias entidades que utilizan un identificador de usuario especificado como la clave de partición. Cada entidad que almacena los datos de un usuario se agrupa en una sola partición y por tanto estas entidades están disponibles a través de transacciones de grupo de la entidad, mientras se está altamente scalable.

Hay otras consideraciones de su elección de **PartitionKey** relativa a cómo se insertar, actualizar y eliminar entidades: vea la sección [Diseño de modificación de datos](#design-for-data-modification) .  

### <a name="optimizing-queries-for-the-table-service"></a>Optimización de consultas para el servicio de tabla  

El servicio de tabla índices automáticamente las entidades con los valores **PartitionKey** y **RowKey** de un índice agrupado único, por lo tanto, son más eficaz de utilizar el motivo por el que apuntan a las consultas. Sin embargo, no hay ningún índices distinto en el índice agrupado en la **PartitionKey** y **RowKey**.

Muchos diseños deben cumplir los requisitos para habilitar la búsqueda de entidades según varios criterios. Por ejemplo, buscar entidades de empleado en función de correo electrónico, id de empleado o apellido. Los patrones siguientes en la sección [Patrones de diseño de tabla](#table-design-patterns) estos tipos de requisito de direcciones y describen las formas de evitar el hecho de que el servicio de la tabla no ofrece índices secundarios:  

-   [Partición dentro de un patrón de índice secundario](#intra-partition-secondary-index-pattern) - almacén de varias copias de cada entidad con diferentes **RowKey** valores (en la misma partición) para habilitar las búsquedas rápida y eficaz y ordenación alternativo pedidos con distintos valores de **RowKey** .  
-   [Dividir entre trama de índice secundario](#inter-partition-secondary-index-pattern) - almacenar varias copias de cada entidad con distintos valores de RowKey de particiones independientes o en tablas separadas para habilitar rápida y búsquedas eficaces y ordenación alternativos con distintos valores de **RowKey** .  
-   [Trama de entidades de índice](#index-entities-pattern) - mantener entidades de índice para habilitar búsquedas eficaces que devuelven listas de entidades.  

### <a name="sorting-data-in-the-table-service"></a>Ordenar datos en el servicio de tabla  

El servicio de tabla devuelve entidades ordenadas en orden ascendente basado en los **PartitionKey** y, a continuación, **RowKey**. Estas teclas son valores de cadena y para asegurarse de que los valores numéricos se ordenan correctamente, debe convertirlas en una longitud fija y les de relleno con ceros. Por ejemplo, si el valor de id de empleado que use como la **RowKey** es un valor entero, debe convertir el id de empleado **123** a **00000123**.  

Muchas aplicaciones tienen requisitos para usar datos ordenados en pedidos diferentes: por ejemplo, ordenación empleados por nombre o unirse a la fecha. Cómo alternar criterios de ordenación para las entidades de dirección de los patrones siguientes en la sección [Patrones de diseño de tabla](#table-design-patterns) :  

-   [Partición dentro de un patrón de índice secundario](#intra-partition-secondary-index-pattern) - almacenar varias copias de cada entidad usando diferentes valores de RowKey (en la misma partición) para habilitar rápida y búsquedas eficaces y ordenación alternativos con distintos valores de RowKey.  
-   [Dividir entre trama de índice secundario](#inter-partition-secondary-index-pattern) - almacén de varias copias de cada entidad con distintos valores de RowKey de separan las particiones en tablas separadas para habilitar las búsquedas rápidas y eficaces y alternar ordenación con distintos valores de RowKey.
-   [Trama de cola de registro](#log-tail-pattern) - entidades de recuperar la *n* recientemente agregadas a una partición utilizando un valor de **RowKey** que ordena en fecha inverso y orden de tiempo.  

## <a name="design-for-data-modification"></a>Diseño de modificación de datos
En esta sección se centra en las consideraciones de diseño para optimizar inserciones, actualizaciones y eliminaciones. En algunos casos, se debe evaluar el equilibrio entre diseños que optimizar para consultas de diseños que optimizar para modificación de los datos igual que en los diseños de bases de datos relacionales (aunque las técnicas para administrar las ventajas y desventajas de diseño son diferentes en una base de datos relacional). La sección [Patrones de diseño de tabla](#table-design-patterns) , describe algunos patrones de diseño detallado para el servicio de la tabla y destaca algunas de estas ventajas y desventajas. En la práctica, encontrará que muchos diseños optimizados para consultar entidades también funcionan bien para modificación de entidades.  

### <a name="optimizing-the-performance-of-insert-update-and-delete-operations"></a>Optimización del rendimiento de inserción, actualizar y eliminar operaciones  

Para actualizar o eliminar una entidad, debe poder identificar con los valores **PartitionKey** y **RowKey** . En este sentido, la elección de **PartitionKey** y **RowKey** para modificar entidades debería seguir criterios similares a su elección compatibilidad con consultas de punto, ya que desee identificar entidades de forma más eficaz. ¿Desea usar un análisis ineficaz de partición o de tabla para localizar una entidad para detectar los valores de **PartitionKey** y **RowKey** que debe actualizar o eliminarla.  

Los patrones siguientes en la sección dirección de [Patrones de diseño de tabla](#table-design-patterns) optimizar el rendimiento o la Insertar, actualizar y eliminar operaciones:  

-   [Eliminar de gran volumen trama](#high-volume-delete-pattern) - habilitar la eliminación de un gran volumen de entidades almacenando todas las entidades para su eliminación simultánea en su propia tabla independiente; eliminar las entidades eliminando la tabla.  
-   [Trama de la serie de datos](#data-series-pattern) - serie de datos completa de almacenamiento en una sola entidad para minimizar el número de solicitudes que realice.  
-   [Trama de entidades amplia](#wide-entities-pattern) - usar varias entidades físicas para almacenar entidades lógicas con más de 252 propiedades.  
-   [Trama de entidades de gran tamaño](#large-entities-pattern) - almacenamiento de blobs de uso para almacenar los valores de propiedad grande.  

### <a name="ensuring-consistency-in-your-stored-entities"></a>Garantizar la coherencia en las entidades almacenadas  

El factor clave que afecta la elección de teclas para optimizar las modificaciones de datos es cómo garantizar la coherencia con transacciones atómicas. Solo puede usar un EGT trabajar con entidades almacenadas en la misma partición.  

Administrar coherencia de dirección de los patrones siguientes en la sección [Patrones de diseño de tabla](#table-design-patterns) :  

-   [Partición dentro de un patrón de índice secundario](#intra-partition-secondary-index-pattern) - almacén de varias copias de cada entidad con diferentes **RowKey** valores (en la misma partición) para habilitar las búsquedas rápida y eficaz y ordenación alternativo pedidos con distintos valores de **RowKey** .  
-   [Dividir entre trama de índice secundario](#inter-partition-secondary-index-pattern) - almacenar varias copias de cada entidad con distintos valores de RowKey de particiones independientes o en tablas separadas para habilitar rápida y búsquedas eficaces y ordenación alternativos con distintos valores de **RowKey** .  
-   [Trama de transacciones finalmente coherente](#eventually-consistent-transactions-pattern) - habilitar comportamiento finalmente coherente a través de los límites de partición o límites de sistema de almacenamiento mediante colas Azure.
-   [Trama de entidades de índice](#index-entities-pattern) - mantener entidades de índice para habilitar búsquedas eficaces que devuelven listas de entidades.  
-   [Trama de desnormalización](#denormalization-pattern) - combinar datos relacionados juntos en una sola entidad para que pueda recuperar todos los datos que necesita con una consulta de punto único.  
-   [Trama de la serie de datos](#data-series-pattern) - serie de datos completa de almacenamiento en una sola entidad para minimizar el número de solicitudes que realice.  

Para obtener información acerca de las transacciones de grupo de entidad, vea la sección [Transacciones de grupo de la entidad](#entity-group-transactions).  

### <a name="ensuring-your-design-for-efficient-modifications-facilitates-efficient-queries"></a>Asegurarse de que el diseño para las modificaciones eficaces facilita consultas eficaces  

En muchos casos, un diseño para los resultados de consultas eficaces en modificaciones eficaces, pero siempre debe evaluar si este es el caso para su situación específica. Algunos de los patrones en la sección [Patrones de diseño de tabla](#table-design-patterns) explícitamente evaluación compensaciones entre consultas y modificación de entidades y siempre deben tener en cuenta el número de cada tipo de operación.  

Los patrones siguientes en la sección [Patrones de diseño de tabla](#table-design-patterns) dirección compensaciones entre diseñar consultas eficaces y diseñar la modificación de datos eficaz:  

-   [Trama de clave compuesta](#compound-key-pattern) - usar compuesto **RowKey** valores para habilitar un cliente buscar datos relacionados con una consulta de punto único.  
-   [Trama de cola de registro](#log-tail-pattern) - entidades de recuperar la *n* recientemente agregadas a una partición utilizando un valor de **RowKey** que ordena en fecha inverso y orden de tiempo.  

## <a name="encrypting-table-data"></a>Cifrado de los datos de tabla    
     
La biblioteca de cliente de almacenamiento de Azure .NET admite el cifrado de propiedades de la entidad de cadena para insertar y reemplazar operaciones. Las cadenas cifradas se almacenan en el servicio como propiedades binarias y se convierten a cadenas después descifrado.    

Para las tablas, además de la directiva de cifrado, los usuarios deben especificar las propiedades de cifrado. Para ello puede especificar un atributo [EncryptProperty] (para entidades POCO que derivan de TableEntity) o una resolución de cifrado en Opciones de la solicitud. Una resolución de cifrado es un delegado que toma una clave de partición, la clave de fila y el nombre de propiedad y devuelve un valor Boolean que indica si se debe cifrar esa propiedad. Durante el cifrado, la biblioteca de cliente usará esta información para decidir si se debe cifrar una propiedad al escribir en la conexión. El delegado también proporciona la posibilidad de lógica alrededor de cómo se cifran propiedades. (Por ejemplo, si los argumentos X, entonces cifrar propiedad A; en caso contrario, cifrar propiedades A y B.) Tenga en cuenta que no es necesario que proporcione esta información al leer o consultar entidades.

Tenga en cuenta que combinar no se admite actualmente. Dado que un subconjunto de propiedades se puede tiene cifrado previamente con una clave diferente, simplemente combinar las nuevas propiedades y actualizar los metadatos da de pérdida de datos. Combinar cualquiera requiere realizar llamadas de servicios adicionales para leer la entidad existente desde el servicio o con una nueva clave por propiedad, que no son adecuadas para mejorar el rendimiento.     

Para obtener información sobre el cifrado de datos de la tabla, vea [depósito de clave de Azure de Microsoft Azure almacenamiento y cifrado de cliente](storage-client-side-encryption.md).  

## <a name="modelling-relationships"></a>Modelado de relaciones  

Creación de modelos de dominio es un paso clave en el diseño de sistemas complejos. Normalmente, se usa el proceso de modelo para identificar entidades y las relaciones entre ellas como una manera de conocer el dominio de la empresa e informar al diseño del sistema. En esta sección se centra en cómo traducir algunos de los tipos de relación comunes que se encuentra en los modelos de dominio a diseños para el servicio de la tabla. El proceso de asignación de un modelo de datos lógico a un físico NoSQL según modelo de datos es muy diferente de la que utiliza al diseñar una base de datos relacional. Diseño de bases de datos relacionales normalmente supone un proceso de normalización de datos optimizado para minimizar la redundancia y una capacidad de consulta descriptiva que resume la implementación de cómo la base de datos de funcionamiento.  

### <a name="one-to-many-relationships"></a>Relaciones uno a varios  

Relaciones uno a varios entre los objetos de dominio de empresa se producen con frecuencia: por ejemplo, un departamento tiene muchos empleados. Existen varias formas de implementar relaciones uno a varios en el servicio de tabla cada con ventajas y desventajas que pueden estar relacionadas con la situación particular.  

Considere el ejemplo de una gran empresa multinacional con miles de departamentos y entidades de empleado en cada departamento tiene muchos empleados y cada empleado como asociado a un departamento concreto. Un enfoque es almacenar independiente departamento y entidades de empleado como estos:  

![][1]

Este ejemplo muestra una relación de uno a varios implícita entre los tipos basándose en el valor de **PartitionKey** . Cada departamento puede tener muchos empleados.  

Este ejemplo muestra también una entidad de departamento y sus entidades de empleado relacionado en la misma partición. Si decide usar diferentes particiones, tablas o cuentas de almacenamiento incluso para los tipos de entidad diferente.  

Un enfoque alternativo es reducir la normalización de los datos y almacenar sólo entidades de empleados con los datos sin normalizar departamento tal como se muestra en el ejemplo siguiente. En esta situación particular, este enfoque no normalizado puede no ser el mejor si tiene un requisito para poder cambiar los detalles de un administrador de departamento porque para ello deberá actualizar a todos los empleados en el departamento.  

![][2]

Para obtener más información, vea la [trama desnormalización](#denormalization-pattern) más adelante en esta guía.  

La siguiente tabla resume las ventajas y desventajas de cada uno de los enfoques descritos anteriormente para almacenar empleado y entidades de departamento que tienen una relación uno a varios. También debe tener en cuenta la frecuencia con que se espera realizar diversas operaciones: puede ser aceptable tiene un diseño que incluye una operación cara si dicha operación sólo ocurre con poca frecuencia.  

<table>
<tr>
<th>Enfoque</th>
<th>Los profesionales de TI</th>
<th>Desventajas</th>
</tr>
<tr>
<td>Tipos de entidad independiente, la misma partición, la misma tabla</td>
<td>
<ul>
<li>Puede actualizar una entidad de departamento con una única operación.</li>
<li>Puede usar un EGT para mantener la coherencia si tiene un requisito para modificar una entidad de departamento siempre que actualizar, insertar o eliminar una entidad de empleado. Por ejemplo, si mantiene un recuento de departamento empleado para cada departamento.</li>
</ul>
</td>
<td>
<ul>
<li>Debe recuperar un empleado y una entidad de departamento para algunas actividades de cliente.</li>
<li>Operaciones de almacenamiento ocurren en la misma partición. En volúmenes de transacciones alta, esto puede traducirse en una zona activa.</li>
<li>No se puede mover a un empleado a un departamento nuevo con un EGT.</li>
</ul>
</td>
</tr>
<tr>
<td>Tipos de entidad independiente, diferentes particiones o tablas o cuentas de almacenamiento</td>
<td>
<ul>
<li>Puede actualizar una entidad de departamento o de la entidad de empleado con una única operación.</li>
<li>En volúmenes de transacciones alta, esto puede ayudar a distribuir la carga entre más particiones.</li>
</ul>
</td>
<td>
<ul>
<li>Debe recuperar un empleado y una entidad de departamento para algunas actividades de cliente.</li>
<li>No puede usar EGTs para mantener la coherencia cuando que actualizar, insertar o eliminar un empleado y actualizar un departamento. Por ejemplo, actualizar un recuento de empleado en una entidad de departamento.</li>
<li>No se puede mover a un empleado a un departamento nuevo con un EGT.</li>
</ul>
</td>
</tr>
<tr>
<td>Reducir la normalización en único tipo de entidad</td>
<td>
<ul>
<li>Puede recuperar toda la información que necesita con una sola solicitud.</li>
</ul>
</td>
<td>
<ul>
<li>Puede ser muy costosa mantener la coherencia si necesita actualizar la información de departamento (¿requieren que actualice a todos los empleados de un departamento).</li>
</ul>
</td>
</tr>
</table>

¿Cómo se elegir entre estas opciones y cuáles de las ventajas y desventajas son más importantes, depende de los escenarios de aplicación específica. Por ejemplo, ¿con qué frecuencia modificar entidades de departamento; las consultas de empleado necesita la información adicional del departamento; ¿Cómo cerrar son los límites de escalabilidad en las particiones o su cuenta de almacenamiento?  

### <a name="one-to-one-relationships"></a>Relaciones uno a uno  

Modelos de dominio pueden incluir relaciones uno a uno entre entidades. Si necesita implementar una relación uno a uno en el servicio de la tabla, también debe elegir cómo vincular las dos entidades relacionadas cuando necesite recuperarlos dos. Este vínculo puede ser implícita, en función de una convención en los valores de clave o explícita almacenando un vínculo en el formato de valores **PartitionKey** y **RowKey** en cada entidad a su entidad relacionada. Para información sobre si debe almacenar las entidades relacionadas en la misma partición, consulte la sección [uno a varios relaciones](#one-to-many-relationships).  

Tenga en cuenta que también hay consideraciones de implementación que podría implementar relaciones uno a uno en el servicio de la tabla:  

-   Administración de entidades de gran tamaño (para obtener más información, consulte [Grandes trama de entidades](#large-entities-pattern)).  
-   Implementación de controles de access (para obtener más información, vea [controlar el acceso con firmas de acceso compartido](#controlling-access-with-shared-access-signatures)).  

### <a name="join-in-the-client"></a>Unirse a en el cliente  

Aunque hay formas de relaciones del modelo en el servicio de la tabla, no debe olvidar que las dos razones principales para utilizar el servicio de la tabla son el rendimiento y escalabilidad. Si encuentra modelado de muchas relaciones que comprometa el rendimiento y escalabilidad de su solución, debe pregúntese si es necesario generar todas las relaciones de datos en el diseño de la tabla. Es posible que pueda simplificar el diseño y mejorar el rendimiento de la solución y escalabilidad si permitir que la aplicación cliente realizar las combinaciones necesarias.  

Por ejemplo, si tiene tablas pequeñas que contienen datos que no cambian a menudo, puede recuperar datos de una vez y caché en el cliente. Esto puede evitar repetida ida y vuelta para recuperar los mismos datos. En los ejemplos que hemos visto en esta guía, es probable que sean pequeños y cambie con poca frecuencia lo que un buen candidato para los datos de la aplicación cliente puede descargar una vez y caché como buscar datos el conjunto de departamentos en una organización pequeña.  

### <a name="inheritance-relationships"></a>Relaciones de herencia  

Si la aplicación cliente usa un conjunto de clases que forman parte de una relación de herencia para representar entidades empresariales, puede conservar fácilmente las entidades en el servicio de la tabla. Por ejemplo, es posible que tenga el siguiente conjunto de clases definidas en la aplicación cliente donde **persona** es una clase.

![][3]

Puede conservar las instancias de las dos clases concretas en el servicio de la tabla con una sola tabla de persona con entidades en ese tienen este aspecto:  

![][4]

Para obtener más información sobre cómo trabajar con varios tipos de entidad en la misma tabla en el código de cliente, vea la sección [trabajar con tipos de entidad heterogéneos](#working-with-heterogeneous-entity-types) más adelante en esta guía. Esto proporciona ejemplos de cómo reconocer el tipo de entidad en el código de cliente.  

## <a name="table-design-patterns"></a>Patrones de diseño de tabla
En las secciones anteriores, ha visto que algo detallado discusiones acerca de cómo optimizar el diseño de la tabla para ambos al recuperar datos de la entidad mediante consultas y para insertar, actualizar y eliminar datos de la entidad. Esta sección describen algunos de los modelos apropiadas para su uso con soluciones de servicio de la tabla. Además, se muestra cómo se pueden prácticamente tratar algunos de los problemas y compensaciones elevados anteriormente en esta guía. En el diagrama siguiente se resume las relaciones entre los distintos patrones:  

![][5]

El mapa de trama encima resalta algunas relaciones entre patrones (azules) y (naranja) que se mencionan en esta guía. Por supuesto, hay muchos otros modelos consideraciones. Por ejemplo, uno de los escenarios clave para el servicio de la tabla es usar la [Materializar trama de vista](https://msdn.microsoft.com/library/azure/dn589782.aspx) del patrón de [Separación de responsabilidad de consulta de comando (CQRS)](https://msdn.microsoft.com/library/azure/jj554200.aspx) .  

### <a name="intra-partition-secondary-index-pattern"></a>Trama de partición dentro de un índice secundario
Almacenar varias copias de cada entidad con distintos valores de **RowKey** (en la misma partición) para habilitar rápidamente y búsquedas eficaces y ordenación alternativo pedidos con distintos valores de **RowKey** . Actualizaciones entre copias pueden mantenerse coherentes con de EGT.  

#### <a name="context-and-problem"></a>Contexto y problema
El servicio de tabla automáticamente índices entidades con los valores **PartitionKey** y **RowKey** . Esto permite una aplicación de cliente recuperar una entidad eficazmente con estos valores. Por ejemplo, con la estructura de tabla que se muestra a continuación, una aplicación de cliente puede usar una consulta de punto para recuperar una entidad de empleado con el nombre de departamento y el identificador del empleado (los valores **PartitionKey** y **RowKey** ). Un cliente también puede recuperar entidades ordenadas por id de empleado dentro de cada departamento.

![][6]

Si desea poder encontrar una entidad de empleado en función del valor de otra propiedad, como la dirección de correo electrónico, debe usar un análisis de partición menos eficiente para encontrar a una coincidencia. Esto es porque el servicio de la tabla no proporciona índices secundarios. Además, no hay ninguna opción para solicitar una lista de empleados ordenados en un orden diferente del orden de **RowKey** .  

#### <a name="solution"></a>Solución
Para evitar la falta de índices secundarios, puede almacenar varias copias de cada entidad con cada copia con un valor distinto de **RowKey** . Si guarda una entidad con las estructuras que se muestra a continuación, puede recuperar eficazmente entidades de empleado en función de correo electrónico dirección o el id. Los valores de prefijo para **RowKey**, "empid_" y "email_" le permiten consultar un empleado o un rango de empleados mediante un intervalo de direcciones de correo electrónico o el ID de empleado.  

![][7]

Los siguientes criterios de filtro de dos (una búsqueda de identificador de empleado y una búsqueda de la dirección de correo electrónico) especifican consultas de punto:  

-   $filter = (PartitionKey EC 'Sales') y (RowKey EC 'empid_000223')  
-   $filter = (PartitionKey EC 'Sales') y (RowKey EC'email_jonesj@contoso.com')  

Si una consulta para un rango de entidades de empleado, puede especificar un rango ordenados en orden de id de empleado o un rango ordenados en orden de la dirección de correo electrónico mediante una consulta para entidades con el prefijo adecuado en la **RowKey**.  

-   Para buscar todos los empleados en el departamento de ventas con un id de empleado en el uso de rango 000100 a 000199: $filter = (PartitionKey EC 'Sales') y (página RowKey 'empid_000100') y (RowKey le 'empid_000199')  
-   Para buscar todos los empleados del departamento de ventas con una dirección de correo electrónico empiecen por la letra "a" uso: $filter = (PartitionKey EC 'Sales') y (página RowKey 'email_a') y (RowKey lt 'email_b')  

 Tenga en cuenta que la sintaxis de filtro utilizada en los ejemplos anteriores es del servicio de tabla API de REST, para obtener más información vea [Entidades de consulta](http://msdn.microsoft.com/library/azure/dd179421.aspx).  

#### <a name="issues-and-considerations"></a>Problemas y consideraciones  

Al decidir cómo implementar este patrón, tenga en cuenta los siguientes puntos:  

-   Almacenamiento de tablas es relativamente económico se use para que la costes generales de almacenamiento de datos duplicados no deben ser más importante. Sin embargo, debe siempre evaluar el costo de un diseño basado en los requisitos de almacenamiento anticipada y solo agregar entidades duplicadas para admitir las consultas que se ejecutará la aplicación de cliente.  
-   Dado que las entidades de índice secundario se almacenan en la misma partición que las entidades originales, debe asegurarse de que no supere los objetivos de escalabilidad de una partición individual.  
-   Para mantener las entidades duplicadas coherentes entre sí utilizando EGTs para actualizar de forma atómica las dos copias de la entidad. Esto significa que debe almacenar todas las copias de una entidad en la misma partición. Para obtener más información, vea la sección [Usar transacciones de grupo de la entidad](#entity-group-transactions).  
-   El valor utilizado para el **RowKey** debe ser único para cada entidad. Considere la posibilidad de usar valores de clave compuestos.  
-   Relleno valores numéricos en **RowKey** (por ejemplo, el identificador de empleado 000223) permite correcta de ordenación y filtrado límites basándose en superior e inferior.  
-   No es necesario duplicar todas las propiedades de la entidad. Por ejemplo, si las consultas de búsqueda las entidades con el correo electrónico de direcciones en el **RowKey** nunca necesita la edad del empleado, estas entidades podrían tener la estructura siguiente:

![][8]

-   Es generalmente mejor almacenar datos duplicados y asegúrese de que puede recuperar todos los datos que necesita con una sola consulta que use una consulta para buscar una entidad y otra para buscar los datos necesarios.  

#### <a name="when-to-use-this-pattern"></a>Cuándo usar este patrón  

Use este patrón cuando la aplicación cliente necesita recuperar entidades mediante una gran variedad de claves diferentes, cuando el cliente necesita recuperar entidades en distintos criterios de ordenación y donde puede identificar cada entidad con una amplia variedad de valores únicos. Sin embargo, debe asegurarse de que no se superen los límites de escalabilidad de partición al realizar búsquedas de entidad con los valores de **RowKey** diferentes.  

#### <a name="related-patterns-and-guidance"></a>Orientación y patrones relacionados  

Los patrones y las instrucciones siguientes también pueden ser importantes al implementar este patrón:  

-   [Trama de entre partición de índice secundario](#inter-partition-secondary-index-pattern)
-   [Trama de clave compuesta](#compound-key-pattern)
-   [Transacciones de grupo de la entidad](#entity-group-transactions)
-   [Trabajar con tipos de entidad heterogéneos](#working-with-heterogeneous-entity-types)

### <a name="inter-partition-secondary-index-pattern"></a>Trama de entre partición de índice secundario
Almacenar varias copias de cada entidad con distintos valores de **RowKey** de particiones independientes o en tablas separadas para habilitar rápidamente y búsquedas eficaces y ordenación alternativo pedidos con distintos valores de **RowKey** .  

#### <a name="context-and-problem"></a>Contexto y problema
El servicio de tabla automáticamente índices entidades con los valores **PartitionKey** y **RowKey** . Esto permite una aplicación de cliente recuperar una entidad eficazmente con estos valores. Por ejemplo, con la estructura de tabla que se muestra a continuación, una aplicación de cliente puede usar una consulta de punto para recuperar una entidad de empleado con el nombre de departamento y el identificador del empleado (los valores **PartitionKey** y **RowKey** ). Un cliente también puede recuperar entidades ordenadas por id de empleado dentro de cada departamento.  

![][9]

Si desea poder encontrar una entidad de empleado en función del valor de otra propiedad, como la dirección de correo electrónico, debe usar un análisis de partición menos eficiente para encontrar a una coincidencia. Esto es porque el servicio de la tabla no proporciona índices secundarios. Además, no hay ninguna opción para solicitar una lista de empleados ordenados en un orden diferente del orden de **RowKey** .  

Espera un gran volumen de transacciones contra estas entidades y desea reducir el riesgo de que el servicio de tabla límite del cliente.  

#### <a name="solution"></a>Solución  
Para evitar la falta de índices secundarios, puede almacenar varias copias de cada entidad con cada copia con diferentes valores **PartitionKey** y **RowKey** . Si guarda una entidad con las estructuras que se muestra a continuación, puede recuperar eficazmente entidades de empleado en función de correo electrónico dirección o el id. Los valores de prefijo para **PartitionKey**, "empid_" y "email_" le permiten identificar qué índice que desea utilizar para una consulta.  

![][10]

Los siguientes criterios de filtro de dos (una búsqueda de identificador de empleado y una búsqueda de la dirección de correo electrónico) especifican consultas de punto:  

-   $filter = (PartitionKey EC ' empid_Sales') y (RowKey EC '000223')
-   $filter = (PartitionKey EC ' email_Sales') y (RowKey EC'jonesj@contoso.com')  

Si una consulta para un rango de entidades de empleado, puede especificar un rango ordenados en orden de id de empleado o un rango ordenados en orden de la dirección de correo electrónico mediante una consulta para entidades con el prefijo adecuado en la **RowKey**.  

-   Para buscar todos los empleados en el departamento de ventas con un id de empleado en el rango **000100** a **000199** ordenados en uso de orden de id de empleado: $filter = (PartitionKey EC ' empid_Sales') y (página RowKey '000100') y (RowKey le '000199')  
-   Para buscar todos los empleados en el departamento de ventas con una dirección de correo electrónico que empieza con "a" ordenado en orden uso de la dirección de correo electrónico: $filter = (PartitionKey EC ' email_Sales') y (página RowKey 'a') y (RowKey lt «b»)  

Tenga en cuenta que la sintaxis de filtro utilizada en los ejemplos anteriores es del servicio de tabla API de REST, para obtener más información vea [Entidades de consulta](http://msdn.microsoft.com/library/azure/dd179421.aspx).  

#### <a name="issues-and-considerations"></a>Problemas y consideraciones  
Al decidir cómo implementar este patrón, tenga en cuenta los siguientes puntos:  

-   Para mantener las entidades duplicadas finalmente coherentes entre sí mediante el [modelo de transacciones finalmente coherentes](#eventually-consistent-transactions-pattern) para mantener las entidades de índice principal y secundario.  
-   Almacenamiento de tablas es relativamente económico se use para que la costes generales de almacenamiento de datos duplicados no deben ser más importante. Sin embargo, debe siempre evaluar el costo de un diseño basado en los requisitos de almacenamiento anticipada y solo agregar entidades duplicadas para admitir las consultas que se ejecutará la aplicación de cliente.  
-   El valor utilizado para el **RowKey** debe ser único para cada entidad. Considere la posibilidad de usar valores de clave compuestos.  
-   Relleno valores numéricos en **RowKey** (por ejemplo, el identificador de empleado 000223) permite correcta de ordenación y filtrado límites basándose en superior e inferior.  
-   No es necesario duplicar todas las propiedades de la entidad. Por ejemplo, si las consultas de búsqueda las entidades con el correo electrónico de direcciones en el **RowKey** nunca necesita la edad del empleado, estas entidades podrían tener la estructura siguiente:

    ![][11]

-   Es generalmente mejor almacenar datos duplicados y asegúrese de que puede recuperar todos los datos que necesita con una sola consulta que use una consulta para buscar una entidad mediante el índice secundario y otra para buscar los datos necesarios en el índice principal.  

#### <a name="when-to-use-this-pattern"></a>Cuándo usar este patrón  
Use este patrón cuando la aplicación cliente necesita recuperar entidades mediante una gran variedad de claves diferentes, cuando el cliente necesita recuperar entidades en distintos criterios de ordenación y donde puede identificar cada entidad con una amplia variedad de valores únicos. Utilice este patrón cuando desea evitar sobrepasan los límites de escalabilidad de partición al realizar búsquedas de entidad con los valores de **RowKey** diferentes.  

#### <a name="related-patterns-and-guidance"></a>Orientación y patrones relacionados
Los patrones y las instrucciones siguientes también pueden ser importantes al implementar este patrón:  

-   [Trama de transacciones finalmente coherente](#eventually-consistent-transactions-pattern)  
-   [Trama de partición dentro de un índice secundario](#intra-partition-secondary-index-pattern)  
-   [Trama de clave compuesta](#compound-key-pattern)  
-   [Transacciones de grupo de la entidad](#entity-group-transactions)  
-   [Trabajar con tipos de entidad heterogéneos](#working-with-heterogeneous-entity-types)  

### <a name="eventually-consistent-transactions-pattern"></a>Trama de transacciones finalmente coherente  

Habilitar un comportamiento finalmente coherente a través de los límites de partición o límites de sistema de almacenamiento mediante colas Azure.  

#### <a name="context-and-problem"></a>Contexto y problema  

EGTs Habilitar transacciones atómicas a través de varias entidades que comparten la misma clave de partición. Por motivos de escalabilidad y rendimiento, puede decidir almacenar entidades que tienen requisitos de coherencia en particiones independientes o en un sistema de almacenamiento independiente: en este caso, no puede usar EGTs para mantener la coherencia. Por ejemplo, es posible que tenga un requisito para mantener la coherencia entre:  

-   Entidades almacenadas en dos particiones diferentes en la misma tabla en tablas diferentes, en cuentas de almacenamiento diferente.  
-   Una entidad almacenada en el servicio de la tabla y un blob almacenados en el servicio de blobs de Windows.  
-   Entidad almacenada en el servicio de la tabla y un archivo en un sistema de archivos.  
-   Un almacén de entidad en el servicio de tabla aún indizado mediante el servicio de búsqueda de Azure.  

#### <a name="solution"></a>Solución  

Mediante colas Azure, puede implementar una solución que proporciona la coherencia entre dos o más particiones o sistemas de almacenamiento.
Este método, suponga que tiene un requisito para poder archivar entidades antiguo empleado. Entidades antiguo empleado se consultan con poca frecuencia y se deben excluir de las actividades relacionadas con los empleados. Para implementar este requisito es almacenar a empleados activos en la tabla **actual** y los antiguos empleados en la tabla de **almacenamiento** . Archivado de un empleado, debe eliminar la entidad de la tabla **actual** y agregar la entidad a la tabla de **almacenamiento** , pero no puede usar un EGT para realizar estos dos operaciones. Para evitar el riesgo de que un error provoca que aparezca una entidad de ambas o ninguna de las dos tablas, la operación de archivado debe ser finalmente coherente. El siguiente diagrama de secuencia, describe los pasos de esta operación. Rutas de acceso de excepción en el siguiente texto se proporciona más detalle.  

![][12]

Un cliente inicia la operación de archivado colocando un mensaje de una cola de Azure, en este ejemplo archivar empleado 456 #. Una función de trabajador explora la cola para mensajes nuevos; Cuando se encuentra uno, lee el mensaje y deja una copia oculta en la cola. La función de trabajo siguiente recupera una copia de la entidad de la tabla **actual** , inserta una copia de la tabla de **archivo** y, a continuación, elimina el original de la tabla **actual** . Por último, si no ha habido errores de los pasos anteriores, la función de trabajo elimina el mensaje oculto de la cola.  

En este ejemplo, el paso 4 inserta al empleado en la tabla de **almacenamiento** . Pueden agregar a los empleados a un blob en el servicio de blobs de Windows o un archivo en un sistema de archivos.  

#### <a name="recovering-from-failures"></a>Recuperación de errores  

Es importante que las operaciones en los pasos **4** y **5** deben ser *idempotentes* en caso de que la función de trabajo necesita volver a iniciar la operación de almacenamiento. Si está utilizando el servicio de la tabla, en el paso **4** debe usar una operación de "Insertar o reemplazar"; en el paso **5** debe usar un "eliminar si existe" operación en la biblioteca de cliente que está utilizando. Si usa otro sistema de almacenamiento, debe utilizar una operación idempotente adecuado.  

Si la función de trabajo no completa paso **6**, a continuación, después de un tiempo de espera el mensaje vuelve a aparecer en la cola lista para la función de trabajo intentar procesar él. La función de trabajo puede comprobar cuántas veces que se ha creado un mensaje en la cola de leer y, si es necesario, marcar es un mensaje de "daños" para investigación enviando a una cola independiente. Para obtener más información sobre cómo leer mensajes de la cola y comprobar el recuento de quitar, consulte [Obtener mensajes](https://msdn.microsoft.com/library/azure/dd179474.aspx).  

Algunos errores de los servicios de cola y tabla son transitorias y la aplicación de cliente debe incluir lógica de reintento adecuado para solucionarlos.  

#### <a name="issues-and-considerations"></a>Problemas y consideraciones
Al decidir cómo implementar este patrón, tenga en cuenta los siguientes puntos:  

-   No se proporciona esta solución de aislamiento de la transacción. Por ejemplo, un cliente puede leer las tablas **actual** y **Archivar** cuando la función de trabajo era entre los pasos **4** y **5**y vea una vista incoherente de los datos. Tenga en cuenta que los datos sean coherentes finalmente.  
-   Asegúrese de que los pasos 4 y 5 son idempotentes para garantizar la coherencia.  
-   Puede cambiar la escala de la solución con varias colas e instancias de la función de trabajo.  

#### <a name="when-to-use-this-pattern"></a>Cuándo usar este patrón  
Utilice este patrón cuando desee garantizar la coherencia entre entidades que existen en diferentes particiones o las tablas. Puede extender este patrón para garantizar la coherencia de las operaciones en el servicio de la tabla y el servicio de blobs de Windows y otros sin Azure almacenamiento orígenes de datos como base de datos o el sistema de archivos.  

#### <a name="related-patterns-and-guidance"></a>Orientación y patrones relacionados  
Los patrones y las instrucciones siguientes también pueden ser importantes al implementar este patrón:  
-   [Transacciones de grupo de la entidad](#entity-group-transactions)  
-   [Combinar o reemplazar](#merge-or-replace)  

>[AZURE.NOTE] Si es importante para la solución de aislamiento de la transacción, considere la posibilidad de volver a diseñar las tablas para que pueda usar EGTs.  

### <a name="index-entities-pattern"></a>Trama de entidades de índice
Mantener entidades de índice para habilitar búsquedas eficaces que devuelven listas de entidades.  

#### <a name="context-and-problem"></a>Contexto y problema  

El servicio de tabla automáticamente índices entidades con los valores **PartitionKey** y **RowKey** . Esto permite una aplicación de cliente recuperar una entidad eficazmente mediante una consulta de punto. Por ejemplo, con la estructura de tabla que se muestra a continuación, una aplicación de cliente puede recuperar eficazmente una entidad de empleado con el nombre de departamento y el identificador del empleado ( **PartitionKey** y **RowKey**).  

![][13]

Si desea recuperar una lista de entidades de empleado en función del valor de otra propiedad no son únicos, como su apellido, debe usar un análisis de partición menos eficaz para buscar coincidencias en lugar de utilizar un índice para buscarla directamente. Esto es porque el servicio de la tabla no proporciona índices secundarios.  

#### <a name="solution"></a>Solución  

Para habilitar la búsqueda por apellido con la estructura de entidad mostrada anteriormente, debe mantener listas de identificadores de empleado. Si desea recuperar las entidades de empleados con un apellido en particular, como Cornejo, debe primero localice la lista de identificadores de empleado para empleados con Jones como su apellido y, a continuación, recuperar esas entidades empleado. Hay tres opciones principales para almacenar las listas de identificadores de empleado:  

-   Usar el almacenamiento de blobs.  
-   Crear entidades de índice en la misma partición que las entidades de empleado.  
-   Crear entidades de índice en una tabla o una partición independiente.  

<u>La opción #1: Almacenamiento de blobs de uso</u>  

Para la primera opción, crea un blob para cada apellido único y, en cada almacenamiento de blobs una lista de los valores de (id de empleado) de **RowKey** y **PartitionKey** (departamento) para los empleados cuyos apellidos. Al agregar o eliminar a un empleado debe asegurarse de que el contenido del blob relevante es finalmente coherente con las entidades de empleado.  

<u>Opción #2:</u> Crear entidades de índice en la misma partición  

Para la segunda opción, use entidades de índice que almacenan los datos siguientes:  

![][14]

La propiedad **EmployeeIDs** contiene una lista de identificadores de empleado para empleados con el apellido almacenado en la **RowKey**.  

Los pasos siguientes describen el proceso que debe seguir al agregar un nuevo empleado si está utilizando la segunda opción. En este ejemplo, estamos agregando a un empleado con identificador 000152 y un apellido Jones del departamento de ventas:  
1.  Recuperar la entidad de índice con un valor de **PartitionKey** "Ventas" y el valor de **RowKey** "Díaz". Guarde el ETag de esta entidad para usar en el paso 2.  
2.  Crear una transacción de grupo de entidad (es decir, una operación por lotes) que inserta la nueva entidad de empleado (**PartitionKey** valor "Ventas" y el valor de **RowKey** "000152") y a continuación, actualiza la entidad de índice (valor**PartitionKey** "Ventas" y el valor de **RowKey** "Díaz") al agregar el nuevo id de empleado a la lista en el campo EmployeeIDs. Para obtener más información sobre las transacciones de grupo de entidad, vea [Transacciones de grupo de la entidad](#entity-group-transactions).  
3.  Si se produce un error en la transacción de grupo de entidad debido a un error de simultaneidad optimista (otra persona modificó solo la entidad de índice), debe empezar en el paso 1 de nuevo.  

Puede usar un enfoque similar a la eliminación de un empleado si está utilizando la segunda opción. Cambiar el apellido de un empleado es algo más compleja porque lo necesitará ejecutar una transacción de grupo de entidad que actualiza tres entidades: la entidad de empleado, la entidad de índice para el apellido antiguo y la entidad de índice para el nuevo nombre. Debe recuperar cada entidad antes de realizar cambios para recuperar los valores de ETag que puede utilizar para realizar las actualizaciones mediante la simultaneidad optimista.  

Los pasos siguientes describen el proceso que debe seguir cuando necesite buscar todos los empleados con un determinado apellido en un departamento si está utilizando la segunda opción. En este ejemplo, se buscan todos los empleados con apellidos Jones del departamento de ventas:  

1.  Recuperar la entidad de índice con un valor de **PartitionKey** "Ventas" y el valor de **RowKey** "Díaz".  
2.  Analizar la lista de identificadores en el campo EmployeeIDs de empleado.  
3.  Si necesita información adicional sobre cada uno de estos empleados (por ejemplo, sus direcciones de correo electrónico), recuperar cada una de las entidades de empleados con el valor de **PartitionKey** "Ventas" y **RowKey** valores de la lista de empleados que obtuvo en el paso 2.  

<u>Opción #3:</u> Crear entidades de índice en una tabla o una partición independiente  

Para la tercera opción, use entidades de índice que almacenan los datos siguientes:  

![][15]

La propiedad **EmployeeIDs** contiene una lista de identificadores de empleado para empleados con el apellido almacenado en la **RowKey**.  

Con la tercera opción, no puede usar EGTs para mantener la coherencia porque las entidades de índice están en una partición independiente de las entidades de empleado. Debe asegurarse de que las entidades de índice son finalmente coherentes con las entidades de empleado.  

#### <a name="issues-and-considerations"></a>Problemas y consideraciones  

Al decidir cómo implementar este patrón, tenga en cuenta los siguientes puntos:  
-   Esta solución requiere al menos dos consultas para recuperar entidades coincidentes: uno para las entidades de índice para obtener la lista de valores de **RowKey** y, a continuación, consultas para recuperar cada entidad en la lista de la consulta.  
-   Dado que una entidad individual tiene un tamaño máximo de 1 MB, opción #2 y la opción #3 en la solución se suponen que la lista de identificadores de empleado para cualquier apellido determinado nunca es mayor que 1 MB. Si la lista de identificadores de empleado es probable que sea mayor de 1 MB, use la opción #1 y almacenar los datos del índice en el almacenamiento de blobs.  
-   Si utiliza la opción #2 (mediante EGTs manejar agregar y eliminar a empleados y cambiar el apellido del empleado) debe evaluar si el volumen de transacciones aproximan a los límites de escalabilidad en una partición determinada. Si este es el caso, debe tener en cuenta una solución finalmente coherente (opción #1 o #3) que utiliza colas para controlar las solicitudes de actualización y le permite almacenar las entidades de índice en una partición independiente de las entidades de empleado.  
-   Opción #2 en esta solución se supone que desea buscar por apellido dentro de un departamento: por ejemplo, en la que desea recuperar una lista de empleados con un apellido Jones del departamento de ventas. Si desea que puedan buscar todos los empleados con un apellido Jones en toda la organización, use la opción #1 u opción #3.
-   Puede implementar una solución basada en cola que ofrece la coherencia (consulte la [trama de transacciones finalmente coherentes](#eventually-consistent-transactions-pattern) para obtener más detalles).  

#### <a name="when-to-use-this-pattern"></a>Cuándo usar este patrón  

Utilice este patrón cuando desee buscar un conjunto de entidades que todos comparten un valor de propiedad comunes, como todos los empleados con el apellido Gómez.  

#### <a name="related-patterns-and-guidance"></a>Orientación y patrones relacionados  

Los patrones y las instrucciones siguientes también pueden ser importantes al implementar este patrón:  
-   [Trama de clave compuesta](#compound-key-pattern)  
-   [Trama de transacciones finalmente coherente](#eventually-consistent-transactions-pattern)  
-   [Transacciones de grupo de la entidad](#entity-group-transactions)  
-   [Trabajar con tipos de entidad heterogéneos](#working-with-heterogeneous-entity-types)  

### <a name="denormalization-pattern"></a>Trama de desnormalización  

Combinar datos relacionados en una sola entidad para que pueda recuperar todos los datos que necesita con una consulta de punto único.  

#### <a name="context-and-problem"></a>Contexto y problema  

En una base de datos relacional, normalmente Normalizar datos para eliminar datos duplicados de consultas que recuperan datos de varias tablas. Si normalizar los datos en tablas Azure, debe realizar varias acciones de ida y desde el cliente en el servidor para recuperar los datos relacionados. Por ejemplo, con la estructura de tabla que se muestra a continuación se necesita dos acciones de ida y vuelta para recuperar los detalles de un departamento: uno para capturar la entidad de departamento que incluye el identificador de un jefe y, a continuación, otra solicitud para capturar los detalles del Administrador de una entidad de empleado.  

![][16]

#### <a name="solution"></a>Solución  

En lugar de almacenar los datos en dos entidades independientes, reducir la normalización de los datos y conservar una copia de los detalles del Administrador de la entidad de departamento. Por ejemplo:  

![][17]

Con entidades de departamento almacenadas con estas propiedades, ahora puede recuperar todos los detalles que necesita acerca de un departamento mediante una consulta de punto.  

#### <a name="issues-and-considerations"></a>Problemas y consideraciones  

Al decidir cómo implementar este patrón, tenga en cuenta los siguientes puntos:  

-   Hay algunos costo sobrecarga asociada al almacenar algunos datos dos veces. La ventaja de rendimiento (es el resultado de menos solicitudes del servicio de almacenamiento) normalmente supera el aumento marginal de los costos de almacenamiento (y este costo parcialmente se desplaza por una reducción del número de transacciones que se requieren para capturar los detalles de un departamento).  
-   Debe mantener la coherencia de las dos entidades que almacenan información acerca de los administradores. Puede controlar el problema de coherencia mediante EGTs actualizar varias entidades en una sola transacción atómica: en este caso, la entidad de departamento y la entidad de empleado para el Administrador de departamento se almacenan en la misma partición.  

#### <a name="when-to-use-this-pattern"></a>Cuándo usar este patrón
Utilice este patrón cuando necesite buscar información relacionada con frecuencia. Este patrón reduce el número de consultas de que cliente debe hacer para recuperar los datos que necesita.  

#### <a name="related-patterns-and-guidance"></a>Orientación y patrones relacionados
Los patrones y las instrucciones siguientes también pueden ser importantes al implementar este patrón:  
-   [Trama de clave compuesta](#compound-key-pattern)  
-   [Transacciones de grupo de la entidad](#entity-group-transactions)  
-   [Trabajar con tipos de entidad heterogéneos](#working-with-heterogeneous-entity-types)

### <a name="compound-key-pattern"></a>Trama de clave compuesta  

Utilizar valores de **RowKey** compuestos para habilitar un cliente buscar datos relacionados con una consulta de punto único.  

#### <a name="context-and-problem"></a>Contexto y problema  

En una base de datos relacional, es bastante natural utilizar combinaciones en las consultas para devolver los elementos de datos del cliente en una sola consulta. Por ejemplo, puede usar el id de empleado para buscar una lista de entidades relacionadas que contienen el rendimiento y revisar los datos de ese empleado.  

Supongamos que está almacenando entidades de empleado en el servicio de la tabla con la estructura siguiente:  

![][18]

También debe almacenar datos históricos relacionados con revisiones y rendimiento para cada año que el empleado ha trabajado para su organización y necesita tener acceso a esta información por año. Una opción es crear otra tabla que almacena las entidades con la estructura siguiente:  

![][19]

Observe que con este método puede decidir duplicar alguna información (como el nombre y apellido) en la nueva entidad para que pueda recuperar los datos con una sola solicitud. Sin embargo, no puede mantener la coherencia seguro porque no puede usar un EGT actualizar las dos entidades de forma atómica.  

#### <a name="solution"></a>Solución
Almacenar un nuevo tipo de entidad en la tabla original con entidades con la estructura siguiente:  

![][20]

Observe cómo la **RowKey** ahora es una clave compuesta formada por el identificador del empleado y el año de los datos de revisión que le permite recuperar el rendimiento del empleado y revisar los datos con una única solicitud para una sola entidad.  

En el ejemplo siguiente se describe cómo se pueden recuperar todos los datos de revisión de un empleado determinado (por ejemplo, empleado 000123 del departamento de ventas):  

$filter = (PartitionKey EC 'Sales') y (página RowKey 'empid_000123') y (RowKey lt 'empid_000124') & $select = RowKey, Administrador de clasificación, del mismo nivel de clasificación, comentarios  

#### <a name="issues-and-considerations"></a>Problemas y consideraciones
Al decidir cómo implementar este patrón, tenga en cuenta los siguientes puntos:  

-   Debe usar un carácter separador adecuado que hace que sea fácil analizar el valor de **RowKey** : por ejemplo, **000123_2012**.  
-   En la misma partición que otras entidades que contienen datos relacionados para el mismo empleado, lo que significa que puede usar EGTs para mantener la coherencia seguro también almacena esta entidad.
-   Debe tener en cuenta la frecuencia con la se consulta los datos para determinar si este patrón es adecuado.  Por ejemplo, si se accede a los datos de revisión con poca frecuencia y con qué frecuencia los datos de empleados principal debe guardarlos entidades como independientes.  

#### <a name="when-to-use-this-pattern"></a>Cuándo usar este patrón  

Utilice este patrón cuando necesite para almacenar una o más entidades relacionadas que consulta con frecuencia.  

#### <a name="related-patterns-and-guidance"></a>Orientación y patrones relacionados  

Los patrones y las instrucciones siguientes también pueden ser importantes al implementar este patrón:  

-   [Transacciones de grupo de la entidad](#entity-group-transactions)  
-   [Trabajar con tipos de entidad heterogéneos](#working-with-heterogeneous-entity-types)  
-   [Trama de transacciones finalmente coherente](#eventually-consistent-transactions-pattern)  

### <a name="log-tail-pattern"></a>Trama de cola de registro  

Recuperar las entidades de *n* recientemente agregadas a una partición utilizando un valor de **RowKey** que ordena en fecha inverso y orden de tiempo.  

#### <a name="context-and-problem"></a>Contexto y problema  

Un requisito común es recuperar las entidades creadas más recientemente, por ejemplo de la más reciente diez gastos reclamaciones enviadas por un empleado. Una operación de consulta **$top** para devolver la primera entidades *n* de un conjunto de soporte técnico de las consultas de tabla: no hay ninguna operación de consulta equivalentes para devolver el últimas entidades n en un conjunto.  

#### <a name="solution"></a>Solución  

Almacenar las entidades con **RowKey** que naturalmente se ordena en orden inverso de fecha y hora usando para que la entrada más reciente siempre es la primera de la tabla.  

Por ejemplo, para poder recuperar las diez notificaciones de gastos más recientes enviadas por un empleado, puede usar un valor de graduación inversa derivado de la fecha y hora actuales. En el ejemplo de código C# siguiente muestra una forma de crear un valor de "invertido marcas" adecuado para **RowKey** que ordena desde el más reciente al más antiguo:  

`string invertedTicks = string.Format("{0:D19}", DateTime.MaxValue.Ticks - DateTime.UtcNow.Ticks);`  

Para volver al valor de hora de fecha con el siguiente código:  

`DateTime dt = new DateTime(DateTime.MaxValue.Ticks - Int64.Parse(invertedTicks));`  

La consulta de tabla es similar a esta:  

`https://myaccount.table.core.windows.net/EmployeeExpense(PartitionKey='empid')?$top=10`  

#### <a name="issues-and-considerations"></a>Problemas y consideraciones  

Al decidir cómo implementar este patrón, tenga en cuenta los siguientes puntos:  

-   Debe rellenar el valor de graduación inversa con ceros a la izquierda para asegurarse de que el valor de cadena que se ordena según lo esperado.  
-   Debe tener en cuenta los destinos escalabilidad en el nivel de una partición. Tenga cuidado de no crear particiones de zona activa.  

#### <a name="when-to-use-this-pattern"></a>Cuándo usar este patrón  

Utilice este patrón cuando necesite obtener acceso a entidades en orden inverso de fecha y hora o cuando necesite obtener acceso a las entidades agregadas más recientemente.  

#### <a name="related-patterns-and-guidance"></a>Orientación y patrones relacionados  

Los patrones y las instrucciones siguientes también pueden ser importantes al implementar este patrón:  

-   [Anteponga / contra el modelo de datos anexados](#prepend-append-anti-pattern)  
-   [Recuperar entidades](#retrieving-entities)  

### <a name="high-volume-delete-pattern"></a>Patrón de eliminación de gran volumen  

Habilitar la eliminación de un gran volumen de entidades almacenando todas las entidades para su eliminación simultánea en su propia tabla independiente; eliminar las entidades eliminando la tabla.  

#### <a name="context-and-problem"></a>Contexto y problema  

Muchas aplicaciones eliminarán datos antiguos que ya no necesita estar disponible para una aplicación de cliente, o que la aplicación se archivado a otro medio de almacenamiento. Normalmente se identifican estos datos en una fecha: por ejemplo, tiene un requisito para eliminar registros de todas las solicitudes de inicio de sesión más de 60 días de antigüedad.  

Un diseño posible es utilizar la fecha y hora de la solicitud de inicio de sesión en la **RowKey**:  

![][21]

Este enfoque evita puntos de conexión de partición porque la aplicación puede insertar y eliminar entidades de inicio de sesión para cada usuario de una partición independiente. Sin embargo, este enfoque puede ser mucho tiempo si tiene una gran cantidad de entidades porque primero tiene que realizar un recorrido de tabla para identificar todas las entidades a eliminar y, a continuación, debe eliminar cada entidad antiguo y costosas. Tenga en cuenta que puede reducir el número de ida y vuelta al servidor es necesario para eliminar las entidades antiguas por lotes varias solicitudes de eliminación en EGTs.  

#### <a name="solution"></a>Solución  

Usar una tabla independiente para cada día de los intentos de inicio de sesión. Puede usar el diseño de entidad anterior para evitar puntos de conexión al que desea insertar entidades y eliminar entidades antiguas ahora es simplemente una pregunta de eliminar todos los días de una tabla (una operación de almacenamiento único) en lugar de buscar y eliminar cientos y miles de entidades de inicio de sesión individuales cada día.  

#### <a name="issues-and-considerations"></a>Problemas y consideraciones  

Al decidir cómo implementar este patrón, tenga en cuenta los siguientes puntos:  

-   ¿Admite el diseño de otras formas de la aplicación usará los datos como la búsqueda de entidades específicas, vinculación con otros datos o generar información agregado?  
-   ¿Evita el diseño de zonas activas cuando se inserten nuevas entidades?  
-   Espere un retraso si desea volver a utilizar el mismo nombre de la tabla después de eliminarlo. Es mejor usar siempre los nombres de tabla única.  
-   Espere limitación al empezar a usar una nueva tabla mientras el servicio de tabla aprende los patrones de acceso y distribuye las particiones en todos los nodos. Debe tener en cuenta la frecuencia con que necesita para crear tablas nuevas.  

#### <a name="when-to-use-this-pattern"></a>Cuándo usar este patrón  

Utilice este patrón cuando tiene un gran volumen de entidades que debe eliminar al mismo tiempo.  

#### <a name="related-patterns-and-guidance"></a>Orientación y patrones relacionados  

Los patrones y las instrucciones siguientes también pueden ser importantes al implementar este patrón:  

-   [Transacciones de grupo de la entidad](#entity-group-transactions)
-   [Modificación de entidades](#modifying-entities)  

### <a name="data-series-pattern"></a>Trama de la serie de datos  

Serie de datos completa de almacenamiento en una sola entidad para minimizar el número de solicitudes que realice.  

#### <a name="context-and-problem"></a>Contexto y problema  

Un escenario común es para que una aplicación almacenar una serie de datos que normalmente necesita recuperar a la vez. Por ejemplo, la aplicación podría grabar cuántos mensajes de mensajería instantánea, cada empleado envía cada hora y, a continuación, utilizar esta información para trazar la cantidad de mensajes cada usuario que se envían a través de las 24 horas anteriores. Puede ser un diseño almacenar 24 entidades de cada empleado:  

![][22]

Con este diseño, puede buscar fácilmente y actualizar la entidad de actualización para cada empleado cuando la aplicación se necesita actualizar el valor de recuento de mensajes. Sin embargo, para recuperar la información para trazar un gráfico de la actividad de las 24 horas anteriores, debe recuperar 24 entidades.  

#### <a name="solution"></a>Solución  

Utilice el siguiente diseño con una propiedad independiente para almacenar el recuento de mensajes para cada hora:  

![][23]

Con este diseño, puede usar una operación de combinación para actualizar el recuento de mensajes de un empleado para una hora específica. Ahora, puede recuperar toda la información que necesita Trace el gráfico con una solicitud para una sola entidad.  

#### <a name="issues-and-considerations"></a>Problemas y consideraciones  

Al decidir cómo implementar este patrón, tenga en cuenta los siguientes puntos:  
-   Si no se ajusta la serie completa de los datos en una única entidad (una entidad puede tener hasta 252 propiedades), use un almacén de datos alternativo como un blob.  
-   Si tiene varios clientes actualizando una entidad de forma simultánea, debe usar **ETag** para implementar simultaneidad optimista. Si tiene muchos clientes, puede experimentar conflictos alta.  

#### <a name="when-to-use-this-pattern"></a>Cuándo usar este patrón  

Utilice este patrón cuando tenga que actualizar y recuperar una serie de datos asociada a una entidad individual.  

#### <a name="related-patterns-and-guidance"></a>Orientación y patrones relacionados  

Los patrones y las instrucciones siguientes también pueden ser importantes al implementar este patrón:  

-   [Trama de entidades de gran tamaño](#large-entities-pattern)  
-   [Combinar o reemplazar](#merge-or-replace)  
-   [Trama de transacciones finalmente coherente](#eventually-consistent-transactions-pattern) (si va a almacenar la serie de datos en un blob)  

### <a name="wide-entities-pattern"></a>Trama de entidades amplia  

Usar varias entidades físicas para almacenar entidades lógicas con más de 252 propiedades.  

#### <a name="context-and-problem"></a>Contexto y problema  

Una entidad individual puede tener no más de 252 propiedades (excepto las propiedades del sistema obligatorio) y no puede almacenar más de 1 MB de datos en total. En una base de datos relacional, normalmente, obtendría redondear los límites en el tamaño de una fila mediante la adición de una nueva tabla y aplicar una relación de 1 a 1 entre ellas.  

#### <a name="solution"></a>Solución  

Mediante el servicio de la tabla, puede almacenar varias entidades para representar un objeto único grandes empresas con más de 252 propiedades. Por ejemplo, si desea almacenar un recuento del número de mensajes Instantáneos enviados por cada empleado para los últimos 365 días, podría usar el siguiente diseño que usa dos entidades con diferentes esquemas:  

![][24]

Si necesita realizar un cambio que requiere actualizar ambas entidades para mantenerlos sincronizados entre sí puede utilizar un EGT. En caso contrario, puede usar una única operación de combinación para actualizar el recuento de mensajes para un día concreto. Para recuperar todos los datos de un empleado que debe recuperar ambas entidades, que puede llevar a cabo con dos solicitudes eficaces que utilizar un **PartitionKey** y un valor de **RowKey** .  

#### <a name="issues-and-considerations"></a>Problemas y consideraciones  

Al decidir cómo implementar este patrón, tenga en cuenta los siguientes puntos:  

-   Recuperar una entidad lógica completa implica al menos dos transacciones de almacenamiento: uno para recuperar cada entidad física.  

#### <a name="when-to-use-this-pattern"></a>Cuándo usar este patrón  

Utilice este patrón cuando necesite para almacenar entidades cuyo tamaño o el número de propiedades superan los límites de una entidad individual en el servicio de la tabla.  

#### <a name="related-patterns-and-guidance"></a>Orientación y patrones relacionados  

Los patrones y las instrucciones siguientes también pueden ser importantes al implementar este patrón:  

-   [Transacciones de grupo de la entidad](#entity-group-transactions)
-   [Combinar o reemplazar](#merge-or-replace)

### <a name="large-entities-pattern"></a>Trama de entidades de gran tamaño  

Usar el almacenamiento de blobs para almacenar los valores de propiedad grandes.  

#### <a name="context-and-problem"></a>Contexto y problema  

Una entidad individual no puede almacenar más de 1 MB de datos en total. Si una o varias de las propiedades de almacenan los valores que provocan el tamaño total de la entidad supere este valor, no puede almacenar la entidad entera en el servicio de la tabla.  

#### <a name="solution"></a>Solución  

Si la entidad supera 1 MB tamaño porque una o varias propiedades contienen una gran cantidad de datos, puede almacenar datos en el servicio de blobs de Windows y, a continuación, almacenar la dirección del blob en una propiedad de la entidad. Por ejemplo, puede almacenar la foto de un empleado en el almacenamiento de blobs y almacenar un vínculo a la foto en la propiedad de la **foto** de la entidad de empleado:  

![][25]

#### <a name="issues-and-considerations"></a>Problemas y consideraciones  

Al decidir cómo implementar este patrón, tenga en cuenta los siguientes puntos:  

-   Para mantener la coherencia entre la entidad en el servicio de la tabla y los datos en el servicio de blobs, utilice el [modelo de transacciones finalmente coherentes](#eventually-consistent-transactions-pattern) para mantener las entidades.
-   Recuperar una entidad completa implica al menos dos transacciones de almacenamiento: uno para recuperar la entidad y otro para recuperar los datos blob.  

#### <a name="when-to-use-this-pattern"></a>Cuándo usar este patrón  

Utilice este patrón cuando necesite para almacenar entidades cuyo tamaño supera los límites de una entidad individual en el servicio de la tabla.  

#### <a name="related-patterns-and-guidance"></a>Orientación y patrones relacionados  

Los patrones y las instrucciones siguientes también pueden ser importantes al implementar este patrón:  

-   [Trama de transacciones finalmente coherente](#eventually-consistent-transactions-pattern)  
-   [Trama de entidades amplia](#wide-entities-pattern)

<a name="prepend-append-anti-pattern"></a>
### <a name="prependappend-anti-pattern"></a>Anteponga de anexar contra patrón  

Aumentar la escalabilidad cuando tiene un gran volumen de inserciones al distribuir las inserciones entre varias particiones.  

#### <a name="context-and-problem"></a>Contexto y problema  

Anteponiendo o anexar entidades a las entidades almacenadas suele ser el resultado de la aplicación agregando nuevas entidades a la primera o la última partición de una secuencia de particiones. En este caso, todas las inserciones en un momento dado produce en la misma partición, crear una zona activa que impide que el servicio de la tabla de equilibrio de carga inserciones en varios nodos y lo que podría provocar la aplicación alcanzar los objetivos de escalabilidad de partición. Por ejemplo, si tiene una aplicación que inicia la red y partición de la hora actual convertirse en una zona activa si el volumen de transacciones alcanza el destino escalabilidad para una partición individual podría provocar acceso a los recursos por los empleados, a continuación, una estructura de entidad tal como se muestra a continuación:  

![][26]

#### <a name="solution"></a>Solución  

La estructura de una entidad alternativa siguiente evita una zona activa en cualquier partición determinada, como los eventos de registros de la aplicación:  

![][27]

Con este ejemplo, observe cómo el **PartitionKey** y **RowKey** son claves compuestas. La **PartitionKey** usa el departamento y los empleados id para distribuir el registro en varias particiones.  

#### <a name="issues-and-considerations"></a>Problemas y consideraciones  

Al decidir cómo implementar este patrón, tenga en cuenta los siguientes puntos:  

-   ¿Es compatible la estructura de clave alternativa que evita la creación de particiones de acceso rápidas en inserciones eficazmente con las consultas de que la aplicación cliente realiza?  
-   ¿El volumen de transacciones previstas significa que es probable que alcanzar los objetivos de escalabilidad de una partición individual y reducirá por el servicio de almacenamiento?  

#### <a name="when-to-use-this-pattern"></a>Cuándo usar este patrón  

Evitar la trama contra prepend/anexar cuando el volumen de transacciones es probable que provocan el límite por el servicio de almacenamiento cuando se tiene acceso a una partición de acceso rápida.  

#### <a name="related-patterns-and-guidance"></a>Orientación y patrones relacionados  

Los patrones y las instrucciones siguientes también pueden ser importantes al implementar este patrón:  

-   [Trama de clave compuesta](#compound-key-pattern)  
-   [Trama de cola de registro](#log-tail-pattern)  
-   [Modificación de entidades](#modifying-entities)  

### <a name="log-data-anti-pattern"></a>Trama contra de datos de registro  

Normalmente, debe utilizar el servicio de blobs de Windows en lugar del servicio de la tabla para almacenar los datos del registro.  

#### <a name="context-and-problem"></a>Contexto y problema  

Caso de uso común de datos del registro están recuperar una selección de entradas de registro de un rango específico de fecha y hora: por ejemplo, en el que desea buscar todos los mensajes de error y críticos que su aplicación registrada entre 15:04 y 15:06 en una fecha específica. ¿Desea usar la fecha y hora del mensaje de registro para determinar la partición guardar entidades de registro: que da como resultado una partición caliente ya en cualquier momento, todas las entidades de registro comparten el mismo valor de **PartitionKey** (consulte la sección [Prepend/anexar contra trama](#prepend-append-anti-pattern)). Por ejemplo, el siguiente esquema de entidad para un mensaje de registro de resultados en una partición de acceso rápida porque la aplicación escribe todos los mensajes de registro en la partición para la fecha actual y la hora:  

![][28]

En este ejemplo, el **RowKey** incluye la fecha y hora del mensaje de registro para asegurarse de que se almacenan los mensajes de registro ordenados en orden de fecha y hora e incluye un identificador de mensaje en caso de varios mensajes de registro compartan la misma fecha y hora.  

Otra consiste en usar un **PartitionKey** que garantiza que la aplicación escribe los mensajes a través de un rango de particiones. Por ejemplo, si el origen del mensaje de registro proporciona una manera para distribuir mensajes en muchas particiones, podría usar el esquema de la entidad siguiente:  

![][29]

Sin embargo, el problema con este esquema es que, para recuperar todos los mensajes de registro para un intervalo de tiempo específico debe buscar todas las particiones en la tabla.

#### <a name="solution"></a>Solución  

La sección anterior resalta el problema de intentar usar el servicio de la tabla para almacenar las entradas del registro y dos sugerido, satisfactoria, diseños. Una solución dirigida a una partición de acceso rápida con el riesgo de rendimiento deficiente escribir mensajes de registro; la otra solución ocasionado rendimiento de la consulta deficiente debido a la necesidad de examinar cada partición de la tabla para recuperar mensajes de registro para un intervalo de tiempo específico. Almacenamiento de blobs ofrece una mejor solución para este tipo de escenario y este es recopila Azure cómo almacena de análisis de almacenamiento los datos del registro.  

Esta sección describe cómo el análisis de almacenamiento almacena los datos de registro en el almacenamiento de blobs como una ilustración de este enfoque para almacenar los datos que normalmente consultar por rango.  

Análisis de almacenamiento almacena los mensajes de registro en un formato delimitado en varios BLOB. El formato delimitado facilita a una aplicación de cliente analizar los datos en el mensaje de registro.  

Análisis de almacenamiento utiliza una convención de nomenclatura para BLOB que le permite buscar el blob (o BLOB) que contienen los mensajes de registro que está buscando. Por ejemplo, un blob llamado "queue/2014/07/31/1800/000001.log" contiene mensajes de registro que se relacionan con el servicio de cola de la hora de inicio a 18:00 31 de julio de 2014. "000001" indica que este es el primer archivo de registro para este período. Análisis de almacenamiento registros también las marcas de tiempo de los mensajes de primer y último registro almacenados en el archivo como parte de los metadatos de blobs. La API para permite de almacenamiento de blobs busque BLOB en un contenedor basándose en un prefijo de nombre: para encontrar todos los BLOB que contienen datos de registro de cola de la hora, empezando en 18:00, puede usar el prefijo "cola/2014/07/31/1800".  

Búferes de análisis de almacenamiento internamente los mensajes de registro y, a continuación, periódicamente actualiza el blob correspondiente o crea uno nuevo con el lote más reciente de las entradas del registro. Esto reduce el número de operaciones de escritura debe realizar al servicio de blobs de Windows.  

Si implementa una solución similar en su propia aplicación, debe considerar cómo administrar el equilibrio entre confiabilidad (escribir cada entrada de registro con el almacenamiento de blobs como sucede) y el costo y escalabilidad (el búfer actualizaciones en su aplicación y escribirlas en el almacenamiento de blobs en lotes).  

#### <a name="issues-and-considerations"></a>Problemas y consideraciones  

Al decidir cómo almacenar los datos del registro, tenga en cuenta los siguientes puntos:  

-   Si crea un diseño de tabla que evita posibles particiones caliente, es posible que no puede acceder eficientemente los datos del registro.  
-   Para procesar datos del registro, un cliente a menudo necesita cargar varios registros.  
-   Aunque los datos de registro a menudo están estructurados, almacenamiento de blobs puede ser una solución mejor.  

### <a name="implementation-considerations"></a>Consideraciones de implementación  

Esta sección describen algunas de las consideraciones a tener en cuenta al implementar los patrones que se describe en las secciones anteriores. La mayoría de esta sección se utilizan ejemplos escritos en C# que utilizan la biblioteca de cliente de almacenamiento (versión 4.3.0 en el momento de escritura).  

### <a name="retrieving-entities"></a>Recuperar entidades  

Como se describe en la sección [Diseño de consulta](#design-for-querying), la consulta más eficaz es un punto. Sin embargo, en algunos casos tendrá que recuperar varias entidades. Esta sección describen algunos métodos comunes para recuperar entidades mediante la biblioteca de cliente de almacenamiento.  

#### <a name="executing-a-point-query-using-the-storage-client-library"></a>Ejecutar una consulta de punto de uso de la biblioteca de cliente de almacenamiento  

Ejecutar una consulta de punto de la manera más sencilla es utilizar la operación de tabla **recuperar** tal como se muestra en el siguiente C# fragmento de código que recupera una entidad con un **PartitionKey** de valor "Ventas" y un **RowKey** del valor "212":  

    TableOperation retrieveOperation =
        TableOperation.Retrieve<EmployeeEntity>("Sales", "212");
    var retrieveResult = employeeTable.Execute(retrieveOperation);
    if (retrieveResult.Result != null)
    {
    EmployeeEntity employee = (EmployeeEntity)retrieveResult.Result;
    ...
    }  

Observe cómo este ejemplo espera la entidad recupera para ser del tipo **EmployeeEntity**.  

#### <a name="retrieving-multiple-entities-using-linq"></a>Recuperar varias entidades con LINQ  

Puede recuperar varias entidades con LINQ con la biblioteca de cliente de almacenamiento y especificar una consulta con una cláusula **where** . Para evitar un recorrido de tabla, siempre debe incluir el valor **PartitionKey** en where cláusula, y si es posible analiza el valor de **RowKey** para evitar la tabla y partición. El servicio de la tabla es compatible con un conjunto limitado de operadores de comparación (mayores que, mayores o iguales que, menor que, menor o iguales, iguales y no es iguales) para usar en where cláusula. El siguiente fragmento de código de C# busca a todos los empleados cuyos apellidos empieza con "B" (suponiendo que el **RowKey** almacena los apellidos) del departamento de ventas (suponiendo que la almacena **PartitionKey** el nombre de departamento):  

    TableQuery<EmployeeEntity> employeeQuery =
            employeeTable.CreateQuery<EmployeeEntity>();
    var query = (from employee in employeeQuery
                where employee.PartitionKey == "Sales" &&
                employee.RowKey.CompareTo("B") >= 0 &&
                employee.RowKey.CompareTo("C") < 0
                select employee).AsTableQuery();
    var employees = query.Execute();  

Observe cómo la consulta especifica un **RowKey** y un **PartitionKey** para lograr un mejor rendimiento.  

El siguiente ejemplo muestra equivalente funcionalidad de código mediante la API fluida (para obtener más información acerca de las API fluent en general, vea [Procedimientos recomendados para diseñar una API fluida](http://visualstudiomagazine.com/articles/2013/12/01/best-practices-for-designing-a-fluent-api.aspx)):  

    TableQuery<EmployeeEntity> employeeQuery = new TableQuery<EmployeeEntity>().Where(
     TableQuery.CombineFilters(
        TableQuery.CombineFilters(
        TableQuery.GenerateFilterCondition(
        "PartitionKey", QueryComparisons.Equal, "Sales"),
      TableOperators.And,
      TableQuery.GenerateFilterCondition(
        "RowKey", QueryComparisons.GreaterThanOrEqual, "B")
    ),
    TableOperators.And,
    TableQuery.GenerateFilterCondition("RowKey", QueryComparisons.LessThan, "C")
     )
    );
    var employees = employeeTable.ExecuteQuery(employeeQuery);  


>[AZURE.NOTE] El ejemplo anida varios métodos **CombineFilters** para incluir las tres condiciones de filtro.  

#### <a name="retrieving-large-numbers-of-entities-from-a-query"></a>Recuperar una gran cantidad de entidades de una consulta  

Una consulta óptima devuelve una entidad individual en función de un valor de **PartitionKey** y un valor de **RowKey** . Sin embargo, en algunos casos puede que tenga un requisito para devolver varias entidades de la misma partición o incluso de muchas particiones.  

Siempre se debe probar el rendimiento de la aplicación en estos casos.  

Una consulta en el servicio de tabla puede devolver un máximo de 1.000 entidades a la vez y puede ejecutar un máximo de cinco segundos. Si el conjunto de resultados contiene más de 1.000 entidades, si la consulta no se completó en cinco segundos, o si la consulta cruza el límite de partición, el servicio de tabla devuelve un token de continuación para habilitar la aplicación cliente solicite el siguiente conjunto de entidades. Para obtener más información acerca de cómo continuación tokens trabajo, vea [el tiempo de espera de consulta y la paginación](http://msdn.microsoft.com/library/azure/dd135718.aspx).  

Si está utilizando la biblioteca de cliente de almacenamiento, puede controlar automáticamente tokens de continuación para usted como devuelve entidades del servicio de la tabla. En el ejemplo del código C# siguiente, utilizando la biblioteca de cliente de almacenamiento automáticamente controla tokens continuación si el servicio de la tabla los devuelve en una respuesta:  

    string filter = TableQuery.GenerateFilterCondition(
        "PartitionKey", QueryComparisons.Equal, "Sales");
    TableQuery<EmployeeEntity> employeeQuery =
        new TableQuery<EmployeeEntity>().Where(filter);

    var employees = employeeTable.ExecuteQuery(employeeQuery);
    foreach (var emp in employees)
    {
        ...
    }  

El siguiente código C# controla explícitamente tokens continuación:  

    string filter = TableQuery.GenerateFilterCondition(
        "PartitionKey", QueryComparisons.Equal, "Sales");
    TableQuery<EmployeeEntity> employeeQuery =
        new TableQuery<EmployeeEntity>().Where(filter);

    TableContinuationToken continuationToken = null;

    do
    {
        var employees = employeeTable.ExecuteQuerySegmented(
            employeeQuery, continuationToken);
    foreach (var emp in employees)
    {
     ...
    }
    continuationToken = employees.ContinuationToken;
    } while (continuationToken != null);  

Utilizando tokens continuación explícitamente, puede controlar cuando la aplicación recupera el próximo segmento de datos. Por ejemplo, si la aplicación cliente permite a los usuarios para desplazarse por las entidades almacenadas en una tabla, un usuario puede decidir no para desplazarse por todas las entidades recuperadas por la consulta para que la aplicación debería usar solo token de continuación para recuperar el próximo segmento cuando el usuario tenía terminado paginación a través de todas las entidades en el segmento actual. Este enfoque tiene varias ventajas:  

-   Le permite limitar la cantidad de datos que desea recuperar del servicio de la tabla y que se mueve a través de la red.  
-   Le permite realizar IO asincrónica en .NET.  
-   Le permite serializar el token de continuación para el almacenamiento persistente para que pueda seguir en caso de bloqueo de la aplicación.  

>[AZURE.NOTE] Token de continuación normalmente devuelve un segmento que contiene entidades de 1.000, aunque puede ser menos. También es el caso si limitar el número de entradas que devuelve una consulta usando **tomar** para devolver la primera entidades n que cumplen los criterios de búsqueda: el servicio de tabla puede devolver un segmento que contenga menos de entidades n junto con un token de continuación para que pueda recuperar las entidades restantes.  

El siguiente código C# muestra cómo modificar el número de entidades devuelto dentro de un segmento:  

    employeeQuery.TakeCount = 50;  

#### <a name="server-side-projection"></a>Proyección de servidor  

Una sola entidad puede tener hasta 255 propiedades y tener hasta 1 MB. Cuando la tabla de consulta y recuperar entidades, que no necesite todas las propiedades y puede evitar transferir datos innecesariamente (para ayudar a reducir la latencia y costo). Puede usar proyección de servidor para transferir sólo las propiedades que necesarias. El ejemplo siguiente es recupera la propiedad de **correo electrónico** (junto con **PartitionKey**, **RowKey**, **marca de tiempo**y **ETag**) de las entidades seleccionadas por la consulta.  

    string filter = TableQuery.GenerateFilterCondition(
        "PartitionKey", QueryComparisons.Equal, "Sales");
    List<string> columns = new List<string>() { "Email" };
    TableQuery<EmployeeEntity> employeeQuery =
        new TableQuery<EmployeeEntity>().Where(filter).Select(columns);

    var entities = employeeTable.ExecuteQuery(employeeQuery);
    foreach (var e in entities)
    {
        Console.WriteLine("RowKey: {0}, EmployeeEmail: {1}", e.RowKey, e.Email);
    }  

Observe cómo el valor de **RowKey** está disponible incluso aunque no se incluyó en la lista de propiedades para recuperar.  

### <a name="modifying-entities"></a>Modificación de entidades  

La biblioteca de cliente de almacenamiento le permite modificar las entidades almacenadas en el servicio de la tabla, insertar, eliminar y actualizar entidades. Puede usar EGTs para lotes varios Insertar, actualizar y eliminar operaciones para reducir el número de ida y vuelta necesarios y mejorar el rendimiento de la solución.  

Observe que las excepciones que se produce cuando la biblioteca de cliente de almacenamiento ejecuta un EGT normalmente incluyen el índice de la entidad que causaron el lote de errores. Esto es útil al depurar código que utiliza EGTs.  

También debe tener en cuenta cómo afecta a la forma en que la aplicación cliente trata las operaciones de simultaneidad y actualizar el diseño.  

#### <a name="managing-concurrency"></a>Administrar la simultaneidad  

De forma predeterminada, el servicio de tabla implementa optimista comprobaciones de simultaneidad del nivel de entidades individuales para **Insertar**, **Combinar**y **Eliminar** operaciones, aunque es posible que un cliente obligar al servicio de la tabla para omitir estas comprobaciones. Para obtener más información sobre cómo administra la simultaneidad del servicio de la tabla, vea [Administración de simultaneidad en Microsoft Azure almacenamiento](storage-concurrency.md).  

#### <a name="merge-or-replace"></a>Combinar o reemplazar  

El método **Replace** de la clase **TableOperation** siempre reemplaza la entidad completa en el servicio de la tabla. Si no incluye una propiedad en la solicitud cuando existe esa propiedad de la entidad almacenada, la solicitud quita dicha propiedad de la entidad almacenada. A menos que desee quitar una propiedad explícitamente de entidad almacenada, debe incluir todas las propiedades de la solicitud.  

Puede usar el método de **combinación** de la clase **TableOperation** para reducir la cantidad de datos que envíe al servicio de tabla desea actualizar una entidad. El método **Merge** reemplaza las propiedades de la entidad almacenada con valores de propiedad de la entidad incluida en la solicitud, pero permanecen intactos las propiedades de la entidad almacenada que no se incluirán en la convocatoria. Esto es útil si tiene entidades de gran tamaño y solo necesita actualizar un pequeño número de propiedades de una solicitud.  

>[AZURE.NOTE] Los métodos **Reemplazar** y **Combinar** un error si no existe la entidad. Como alternativa, puede usar los métodos **InsertOrReplace** y **InsertOrMerge** que crea una nueva entidad si no existe.  

### <a name="working-with-heterogeneous-entity-types"></a>Trabajar con tipos de entidad heterogéneos  

El servicio de tabla es un almacén de tabla de *esquema menor* que significa que una sola tabla puede almacenar entidades de varios tipos de proporcionar gran flexibilidad en el diseño. En el ejemplo siguiente se muestra una tabla de almacenamiento de empleado y entidades de departamento:  

<table>
<tr>
<th>PartitionKey</th>
<th>RowKey</th>
<th>Marca de tiempo</th>
<th></th>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td>
<table>
<tr>
<th>Nombre</th>
<th>Apellido</th>
<th>Edad</th>
<th>Correo electrónico</th>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td></td>
</tr>
</table>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td>
<table>
<tr>
<th>Nombre</th>
<th>Apellido</th>
<th>Edad</th>
<th>Correo electrónico</th>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td></td>
</tr>
</table>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td>
<table>
<tr>
<th>Nombredepartamento</th>
<th>EmployeeCount</th>
</tr>
<tr>
<td></td>
<td></td>
</tr>
</table>
</td>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td>
<table>
<tr>
<th>Nombre</th>
<th>Apellido</th>
<th>Edad</th>
<th>Correo electrónico</th>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td></td>
</tr>
</table>
</td>
</tr>
</table>

Tenga en cuenta que cada entidad debe tener **PartitionKey**, **RowKey**y valores de la **marca de tiempo** , pero puede tener cualquier conjunto de propiedades. Además, no hay nada para indicar el tipo de una entidad a menos que decida almacenar esa información en algún lugar. Existen dos opciones para identificar el tipo de entidad:  

-   Anteponga el tipo de entidad para el **RowKey** (o posiblemente la **PartitionKey**). Por ejemplo, **EMPLOYEE_000123** o **DEPARTMENT_SALES** como valores de **RowKey** .  
-   Use una propiedad independiente para el tipo de entidad de registro tal como se muestra en la tabla siguiente.  

<table>
<tr>
<th>PartitionKey</th>
<th>RowKey</th>
<th>Marca de tiempo</th>
<th></th>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td>
<table>
<tr>
<th>EntityType</th>
<th>Nombre</th>
<th>Apellido</th>
<th>Edad</th>
<th>Correo electrónico</th>
</tr>
<tr>
<td>Empleado</td>
<td></td>
<td></td>
<td></td>
<td></td>
</tr>
</table>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td>
<table>
<tr>
<th>EntityType</th>
<th>Nombre</th>
<th>Apellido</th>
<th>Edad</th>
<th>Correo electrónico</th>
</tr>
<tr>
<td>Empleado</td>
<td></td>
<td></td>
<td></td>
<td></td>
</tr>
</table>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td>
<table>
<tr>
<th>EntityType</th>
<th>Nombredepartamento</th>
<th>EmployeeCount</th>
</tr>
<tr>
<td>Departamento</td>
<td></td>
<td></td>
</tr>
</table>
</td>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td>
<table>
<tr>
<th>EntityType</th>
<th>Nombre</th>
<th>Apellido</th>
<th>Edad</th>
<th>Correo electrónico</th>
</tr>
<tr>
<td>Empleado</td>
<td></td>
<td></td>
<td></td>
<td></td>
</tr>
</table>
</td>
</tr>
</table>

La primera opción, anteponiendo el tipo de entidad a la **RowKey**, es útil si existe la posibilidad de que dos entidades de los diferentes tipos podrían tener el mismo valor de clave. También agrupa entidades del mismo tipo juntos en la partición.  

Las técnicas descritas en esta sección son especialmente relevantes para la discusión [relaciones de herencia](#inheritance-relationships) anteriormente en esta guía en la sección [Modelling relaciones](#modelling-relationships).  

>[AZURE.NOTE] Considere la posibilidad de incluye un número de versión en el valor de tipo de entidad que permiten a aplicaciones cliente evolucionando POCO objetos y trabajar con diferentes versiones.  

El resto de esta sección describe algunas de las características de la biblioteca de cliente de almacenamiento que facilitan el trabajo con varios tipos de entidad en la misma tabla.  

#### <a name="retrieving-heterogeneous-entity-types"></a>Recuperar tipos de entidad heterogéneos  

Si está utilizando la biblioteca de cliente de almacenamiento, tiene tres opciones para trabajar con varios tipos de entidad.  

Si conoce el tipo de la entidad almacenado con un valores específicos de **RowKey** y **PartitionKey** , puede especificar el tipo de entidad al recuperar la entidad, como se muestra en los dos ejemplos anteriores que recuperar entidades de tipo **EmployeeEntity**: [ejecutar una consulta de punto de uso de la biblioteca de cliente de almacenamiento](#executing-a-point-query-using-the-storage-client-library) y [recuperar varias entidades con LINQ](#retrieving-multiple-entities-using-linq).  

La segunda opción es utilizar el tipo de **DynamicTableEntity** (bolsa de propiedades) en lugar de un tipo concreto de entidad POCO (esta opción puede también mejorar el rendimiento porque no es necesario para serializar y deserializar la entidad a tipos de .NET). El siguiente código C# potencialmente recupera varias entidades de los diferentes tipos de la tabla, pero devuelve todas las entidades como instancias de **DynamicTableEntity** . A continuación, se utiliza la propiedad **EntityType** para determinar el tipo de cada entidad:  

    string filter =     TableQuery.CombineFilters(
        TableQuery.GenerateFilterCondition("PartitionKey",
      QueryComparisons.Equal, "Sales"),
        TableOperators.And,
        TableQuery.CombineFilters(
        TableQuery.GenerateFilterCondition("RowKey",
                    QueryComparisons.GreaterThanOrEqual, "B"),
            TableOperators.And,
            TableQuery.GenerateFilterCondition("RowKey",
          QueryComparisons.LessThan, "F")
        )
    );
    TableQuery<DynamicTableEntity> entityQuery =
    new TableQuery<DynamicTableEntity>().Where(filter);
    var employees = employeeTable.ExecuteQuery(entityQuery);

    IEnumerable<DynamicTableEntity> entities = employeeTable.ExecuteQuery(entityQuery);
    foreach (var e in entities)
    {
    EntityProperty entityTypeProperty;
    if (e.Properties.TryGetValue("EntityType", out entityTypeProperty))
    {
        if (entityTypeProperty.StringValue == "Employee")
        {
            // Use entityTypeProperty, RowKey, PartitionKey, Etag, and Timestamp
          }
     }
    }  

Tenga en cuenta que para recuperar otras propiedades debe utilizar el método **TryGetValue** en la propiedad de **Propiedades** de la clase **DynamicTableEntity** .  

Una tercera opción es combinar utilizando el tipo de **DynamicTableEntity** y una instancia de **EntityResolver** . Esto le permite resolver a varios tipos POCO en la misma consulta. En este ejemplo, el delegado **EntityResolver** usa la propiedad **EntityType** para distinguir entre los dos tipos de entidad que devuelve la consulta. El método **resolver** utiliza al delegado de **resolución** para que resuelva instancias de **DynamicTableEntity** instancias de **TableEntity** .  

    EntityResolver<TableEntity> resolver = (pk, rk, ts, props, etag) =>
    {

        TableEntity resolvedEntity = null;
        if (props["EntityType"].StringValue == "Department")
        {
            resolvedEntity = new DepartmentEntity();
        }
        else if (props["EntityType"].StringValue == "Employee")
        {
            resolvedEntity = new EmployeeEntity();
        }
        else throw new ArgumentException("Unrecognized entity", "props");

        resolvedEntity.PartitionKey = pk;
        resolvedEntity.RowKey = rk;
        resolvedEntity.Timestamp = ts;
        resolvedEntity.ETag = etag;
        resolvedEntity.ReadEntity(props, null);
        return resolvedEntity;
    };

    string filter = TableQuery.GenerateFilterCondition(
        "PartitionKey", QueryComparisons.Equal, "Sales");
    TableQuery<DynamicTableEntity> entityQuery =
        new TableQuery<DynamicTableEntity>().Where(filter);

    var entities = employeeTable.ExecuteQuery(entityQuery, resolver);
    foreach (var e in entities)
    {
        if (e is DepartmentEntity)
        {
        ...
        }
        if (e is EmployeeEntity)
        {
        ...
        }
    }  

#### <a name="modifying-heterogeneous-entity-types"></a>Modificar un tipo de entidad heterogéneos  

No es necesario saber el tipo de una entidad para eliminarla y siempre sepa el tipo de una entidad al insertarlo. Sin embargo, puede usar el tipo de **DynamicTableEntity** para actualizar una entidad sin necesidad de conocer su tipo y sin usar una clase de entidad POCO. En el ejemplo de código siguiente recupera una entidad única y comprueba que la propiedad **EmployeeCount** existe antes de actualizarla.  

    TableResult result =
        employeeTable.Execute(TableOperation.Retrieve(partitionKey, rowKey));
    DynamicTableEntity department = (DynamicTableEntity)result.Result;

    EntityProperty countProperty;

    if (!department.Properties.TryGetValue("EmployeeCount", out countProperty))
    {
        throw new
            InvalidOperationException("Invalid entity, EmployeeCount property not found.");
    }
    countProperty.Int32Value += 1;
    employeeTable.Execute(TableOperation.Merge(department));  

### <a name="controlling-access-with-shared-access-signatures"></a>Controlar el acceso con firmas de acceso compartido  

Puede usar tokens de firma de acceso compartido (SA) para habilitar las aplicaciones de cliente modificar (y consultar) entidades de tabla directamente sin necesidad de autenticar directamente con el servicio de la tabla. Normalmente, hay tres ventajas principales de utilizar SA en la aplicación:  

-   No es necesario distribuir la clave de cuenta de almacenamiento para una plataforma segura (por ejemplo, un dispositivo móvil) para permitir que el dispositivo acceder y modificar entidades en el servicio de la tabla.  
-   Puede descargar parte del trabajo que realizan los roles de web y trabajador en la administración de las entidades de dispositivos cliente como los equipos de usuario final y dispositivos móviles.  
-   Puede asignar un restringida y tiempo limitado conjunto de permisos a un cliente (por ejemplo, para permitir el acceso de solo lectura a recursos específicos).  

Para obtener más información sobre el uso de tokens SA con el servicio de la tabla, vea [Usar firmas de acceso compartido (SA)](storage-dotnet-shared-access-signature-part-1.md).  

Sin embargo, todavía debe generar los tokens SA que concesión a una aplicación de cliente a las entidades en el servicio de tabla: debe hacer esto en un entorno que tiene acceso seguro a las claves de la cuenta de almacenamiento. Normalmente, se utiliza una función web o de trabajo para generar los tokens SA y enviarlos a las aplicaciones cliente que necesitan tener acceso a las entidades. Dado que hay una sobrecarga implicados en generar y entregar tokens SA a los clientes, debe tener en cuenta cuál es la mejor para reducir esta sobrecarga, especialmente en escenarios de gran volumen.  

Es posible generar un token de SA que conceda acceso a un subconjunto de las entidades de una tabla. De forma predeterminada, se crea un token de SA para toda una tabla, pero también es posible especificar que el token de SA conceder acceso a un rango de valores de **PartitionKey** o un rango de valores **PartitionKey** y **RowKey** . Puede generar tokens de SA para usuarios individuales de su sistema de forma que del token cada usuario sa sólo les permite el acceso a sus propias entidades en el servicio de la tabla.  

### <a name="asynchronous-and-parallel-operations"></a>Operaciones paralelas y asincrónicas  

Siempre se propagarse las solicitudes entre varias particiones, puede mejorar la capacidad de respuesta de rendimiento y cliente mediante consultas asincrónicas o en paralelas.
Por ejemplo, es posible que tenga dos o más instancias de rol de trabajo obtener acceso a las tablas en paralelo. Podría tener funciones de trabajo individuales responsables de determinada conjuntos de particiones, o simplemente tiene varias instancias trabajo de rol, cada pueden tener acceso a todas las particiones en una tabla.  

Dentro de una instancia de cliente, puede mejorar el rendimiento al ejecutar las operaciones de almacenamiento de forma asincrónica. La biblioteca de cliente de almacenamiento facilita la escritura modificaciones y consultas asíncronas. Por ejemplo, puede empezar con el método sincrónico que recupera todas las entidades de una partición tal como se muestra en el siguiente código de C#:  

    private static void ManyEntitiesQuery(CloudTable employeeTable, string department)
    {
        string filter = TableQuery.GenerateFilterCondition(
            "PartitionKey", QueryComparisons.Equal, department);
        TableQuery<EmployeeEntity> employeeQuery =
            new TableQuery<EmployeeEntity>().Where(filter);

        TableContinuationToken continuationToken = null;

        do
        {
            var employees = employeeTable.ExecuteQuerySegmented(
                employeeQuery, continuationToken);
            foreach (var emp in employees)
        {
        ...
        }
            continuationToken = employees.ContinuationToken;
        } while (continuationToken != null);
    }  

Puede modificar fácilmente este código para que la consulta se ejecuta de forma asincrónica como sigue:  

    private static async Task ManyEntitiesQueryAsync(CloudTable employeeTable, string department)
    {
        string filter = TableQuery.GenerateFilterCondition(
            "PartitionKey", QueryComparisons.Equal, department);
        TableQuery<EmployeeEntity> employeeQuery =
            new TableQuery<EmployeeEntity>().Where(filter);
        TableContinuationToken continuationToken = null;

        do
        {
            var employees = await employeeTable.ExecuteQuerySegmentedAsync(
                employeeQuery, continuationToken);
            foreach (var emp in employees)
            {
             ...
            }
            continuationToken = employees.ContinuationToken;
            } while (continuationToken != null);
    }  

En este ejemplo asincrónica, puede ver los cambios de la versión sincrónico siguientes:  

-   La firma del método ahora incluye el modificador **asincrónica** y devuelve una instancia de la **tarea** .  
-   En lugar de llamar al método **ExecuteSegmented** para recuperar los resultados, el método llama al método **ExecuteSegmentedAsync** y utiliza el modificador **espera** para recuperar resultados de forma asincrónica.  

La aplicación cliente puede llamar a este método varias veces (con distintos valores para el parámetro de **departamento** ), y cada consulta se ejecutará en un subproceso independiente.  

Tenga en cuenta que no hay ninguna versión asincrónica del método **Execute** en la clase **TableQuery** porque la interfaz **IEnumerable** no admite la enumeración asincrónica.  

También puede insertar, actualizar y eliminar las entidades de forma asincrónica. En el ejemplo de C# siguiente se muestra un método sencillo y sincrónico para insertar o reemplazar una entidad de empleado:  

    private static void SimpleEmployeeUpsert(CloudTable employeeTable,
        EmployeeEntity employee)
    {
        TableResult result = employeeTable
            .Execute(TableOperation.InsertOrReplace(employee));
        Console.WriteLine("HTTP Status: {0}", result.HttpStatusCode);
    }  

Puede modificar fácilmente este código para que la actualización se ejecuta de forma asincrónica como sigue:  

    private static async Task SimpleEmployeeUpsertAsync(CloudTable employeeTable,
        EmployeeEntity employee)
    {
        TableResult result = await employeeTable
            .ExecuteAsync(TableOperation.InsertOrReplace(employee));
        Console.WriteLine("HTTP Status: {0}", result.HttpStatusCode);
    }  

En este ejemplo asincrónica, puede ver los cambios de la versión sincrónico siguientes:  

-   La firma del método ahora incluye el modificador **asincrónica** y devuelve una instancia de la **tarea** .  
-   En lugar de llamar al método **Execute** para actualizar la entidad, el método llama al método **ExecuteAsync** y utiliza el modificador **espera** para recuperar resultados de forma asincrónica.  

La aplicación cliente puede llamar a varios métodos asincrónicos como ésta y cada invocación de método se ejecutará en un subproceso independiente.  


### <a name="credits"></a>Créditos
Nos gustaría gracias a los miembros del equipo de Azure en sus contribuciones siguientes: Dominic Betts, Jason Hogg, Jean Ghanem, Jai Haridas, Jeff Irwin, Vamshidhar Kommineni, Shah Vinay y Serdar Ozler así como Tom Hollander de DX de Microsoft. 

Le gustaría gracias el siguiente Microsoft MVP para sus comentarios valiosos durante ciclos de revisión: Igor Papirov y Edward Bakker.



[1]: ./media/storage-table-design-guide/storage-table-design-IMAGE01.png
[2]: ./media/storage-table-design-guide/storage-table-design-IMAGE02.png
[3]: ./media/storage-table-design-guide/storage-table-design-IMAGE03.png
[4]: ./media/storage-table-design-guide/storage-table-design-IMAGE04.png
[5]: ./media/storage-table-design-guide/storage-table-design-IMAGE05.png
[6]: ./media/storage-table-design-guide/storage-table-design-IMAGE06.png
[7]: ./media/storage-table-design-guide/storage-table-design-IMAGE07.png
[8]: ./media/storage-table-design-guide/storage-table-design-IMAGE08.png
[9]: ./media/storage-table-design-guide/storage-table-design-IMAGE09.png
[10]: ./media/storage-table-design-guide/storage-table-design-IMAGE10.png
[11]: ./media/storage-table-design-guide/storage-table-design-IMAGE11.png
[12]: ./media/storage-table-design-guide/storage-table-design-IMAGE12.png
[13]: ./media/storage-table-design-guide/storage-table-design-IMAGE13.png
[14]: ./media/storage-table-design-guide/storage-table-design-IMAGE14.png
[15]: ./media/storage-table-design-guide/storage-table-design-IMAGE15.png
[16]: ./media/storage-table-design-guide/storage-table-design-IMAGE16.png
[17]: ./media/storage-table-design-guide/storage-table-design-IMAGE17.png
[18]: ./media/storage-table-design-guide/storage-table-design-IMAGE18.png
[19]: ./media/storage-table-design-guide/storage-table-design-IMAGE19.png
[20]: ./media/storage-table-design-guide/storage-table-design-IMAGE20.png
[21]: ./media/storage-table-design-guide/storage-table-design-IMAGE21.png
[22]: ./media/storage-table-design-guide/storage-table-design-IMAGE22.png
[23]: ./media/storage-table-design-guide/storage-table-design-IMAGE23.png
[24]: ./media/storage-table-design-guide/storage-table-design-IMAGE24.png
[25]: ./media/storage-table-design-guide/storage-table-design-IMAGE25.png
[26]: ./media/storage-table-design-guide/storage-table-design-IMAGE26.png
[27]: ./media/storage-table-design-guide/storage-table-design-IMAGE27.png
[28]: ./media/storage-table-design-guide/storage-table-design-IMAGE28.png
[29]: ./media/storage-table-design-guide/storage-table-design-IMAGE29.png
 
