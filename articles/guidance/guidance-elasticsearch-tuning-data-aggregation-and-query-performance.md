<properties
   pageTitle="Ajuste del rendimiento de agregación y consulta de datos con Elasticsearch en Azure | Microsoft Azure"
   description="Un resumen de las consideraciones al optimizar el rendimiento de búsqueda y consulta para Elasticsearch."
   services=""
   documentationCenter="na"
   authors="dragon119"
   manager="bennage"
   editor=""
   tags=""/>

<tags
   ms.service="guidance"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/22/2016"
   ms.author="masashin"/>
   
# <a name="tuning-data-aggregation-and-query-performance-with-elasticsearch-on-azure"></a>Ajuste del rendimiento de agregación y consulta de datos con Elasticsearch en Azure

[AZURE.INCLUDE [pnp-header](../../includes/guidance-pnp-header-include.md)]

En este artículo forma [parte de una serie](guidance-elasticsearch.md). 

Es una razón principal para usar Elasticsearch para búsquedas a través de los datos. Los usuarios podrán encontrar rápidamente la información que está buscando. Además, el sistema debe permitir a los usuarios formular preguntas de los datos, buscar correlación y vienen a conclusiones que pueden conducir decisiones empresariales. Este proceso es lo que diferencia datos de información.

Este documento resume las opciones que puede tener en cuenta al determinar la mejor manera de optimizar su sistema de búsqueda y consulta de rendimiento.

Todas las recomendaciones de rendimiento dependen en gran medida los escenarios que se aplican a su situación, el volumen de datos que se va a indizar y la tasa a la que las aplicaciones y los usuarios consultan los datos. Debe probarlo detenidamente los resultados de cualquier cambio de configuración o la estructura de indización con sus propios datos y cargas de trabajo para evaluar las ventajas para sus escenarios específicos. Para ello, este documento también describe un número de referencia que se realizaron para un escenario específico implementado con diferentes configuraciones. Puede adaptar el enfoque adoptado para evaluar el rendimiento de sus propios sistemas. Los detalles de estas pruebas se describen en el [Apéndice](#appendix-the-query-and-aggregation-performance-test).

## <a name="index-and-query-performance-considerations"></a>Consideraciones de rendimiento de índice y consulta

Esta sección describen algunos factores comunes que debe tener en cuenta al diseñar índices que necesitan para admitir consultas y la búsqueda rápida.

### <a name="storing-multiple-types-in-an-index"></a>Almacenar varios tipos en un índice

Un índice de Elasticsearch puede contener varios tipos. Puede ser mejor evitar este enfoque y crear un índice independiente para cada tipo. Tenga en cuenta los siguientes puntos:

- Diferentes tipos podrían especificar diferentes analizadores y no siempre es claro qué analyzer Elasticsearch debe usar si se realiza una consulta en el nivel del índice en lugar de en el nivel de tipo. Para obtener más información, vea [Evitar trampas de tipo](https://www.elastic.co/guide/en/elasticsearch/guide/current/mapping.html#_avoiding_type_gotchas) .

- Shards índices que contienen varios tipos de probablemente será mayores de los índices que contienen un único tipo. Más grande a fragmentar, el esfuerzo más requiere Elasticsearch para filtrar los datos al realizar consultas.

- Si hay una incoherencia significativa entre volúmenes de datos para los tipos, información para un tipo puede convertirse en apenas distribuirse muchas shards reducir la eficacia de búsquedas que recuperar datos.

    ![](./media/guidance-elasticsearch/query-performance1.png)

    ***Los efectos de un índice entre los tipos de uso compartido*** 

    En la parte superior del diagrama, documentos de tipo A y B. tipo comparten el mismo índice Hay varios documentos de más de un tipo de tipo que b. busca tipo una obtendrá implican consultar cuatro shards todos. La parte inferior del diagrama muestra el efecto si se crean índices independientes para cada tipo. En este caso, busca el tipo de una se solo requiere tener acceso a dos shards.

- Shards pequeñas pueden ser más distribuidos que shards grandes, facilitará a Elasticsearch distribuir la carga entre los nodos.

- Tipos diferentes pueden tener diferentes períodos de retención. Puede ser difícil archivar datos antiguos que comparte shards con datos activos.


Sin embargo, en determinadas circunstancias compartir un índice entre tipos puede ser eficaz si:

- Búsquedas con regularidad abarcan tipos de contenidos en el mismo índice.

- Los tipos solo tienen un pequeño número de documentos cada. Mantener un conjunto diferente de shards para cada tipo puede convertirse en una carga significativa en este caso.


### <a name="optimizing-index-types"></a>Optimización de los tipos de índice

Un índice Elasticsearch contiene una copia de los documentos JSON originales que se utilizaron para rellenarla. Esta información se almacena en la [* \_origen*](https://www.elastic.co/guide/en/elasticsearch/reference/current/mapping-source-field.html#mapping-source-field) campo de cada elemento indizado. Estos datos no es que permiten búsquedos, pero devuelven por las solicitudes *get* y *búsqueda* de forma predeterminada. Sin embargo, este campo incurre sobrecarga y ocupa almacenamiento, aumentar tamaño shards y aumentar el volumen de i/OS realizado. Puede deshabilitar la * \_origen* campo por tipo:

```http
PUT my_index
{
  "mappings": {
    "my_type": {
      "_source": {
        "enabled": false
      }
    }
  }
}
```
Deshabilitar este campo también quita la capacidad de realizar las siguientes operaciones:

- Actualizar datos en el índice mediante la *actualización de* API.

- Realizar búsquedas que devuelven datos resaltados.

- Indización de un índice de Elasticsearch directamente a otro.

- Cambio de configuración de análisis o asignaciones.

- Consultas de depuración al ver el documento original.


### <a name="reindexing-data"></a>Volver a indizar datos

Finalmente, el número de shards disponibles para un índice determina la capacidad del índice. Puede realizar una estimación inicial (e informado) en cuántos shards será necesarios, pero siempre debe tener en cuenta el documento reindización estrategia adelantado. En muchos casos, al volver a indizar puede ser una tarea, a medida que crecen de datos. No desea asignar una gran cantidad de shards a un índice inicialmente, por motivos de optimización de búsqueda, pero asignar nuevos shards según el volumen de datos se expande. En los demás casos indización podría deben llevarse a cabo de forma más ad-hoc si las estimaciones sobre crecimiento del volumen de datos simplemente demuestran precisas.

> [AZURE.NOTE] Volver a indizar no podría ser necesario para los datos que caduca rápidamente. En este caso, una aplicación puede crear un nuevo índice para cada período de tiempo. Ejemplos de incluyen registros de rendimiento o de auditoría de datos que se podrían almacenar en un índice fresco cada día.

<!-- -->

Indización de forma eficaz consiste en crear un índice de los datos de uno antiguo y, a continuación, eliminar el índice antiguo. Si un índice es grande, este proceso puede tardar tiempo y debe asegurarse de que los datos permanecen aplicándoles durante este período. Por este motivo, debe crear un [alias para cada índice](https://www.elastic.co/guide/en/elasticsearch/reference/current/indices-aliases.html)y las consultas deben recuperar datos a través de estos alias. Al volver a indizar, mantener el alias que señala el índice antiguo y, a continuación, cambie para hacer referencia al nuevo índice una vez completada la indización. Este método también es útil para tener acceso a datos basados en el tiempo que se crea un nuevo índice cada día. Para obtener acceso a la actual datos use un alias que se pone en el nuevo índice que se crean.

### <a name="managing-mappings"></a>Administrar asignaciones

Elasticsearch usa asignaciones para determinar cómo interpretar los datos que se produce en cada campo en un documento. Cada tipo tiene su propia asignación, que define un esquema para ese tipo de forma eficaz. Elasticsearch usa esta información para generar índices invertidos para cada campo en los documentos en un tipo. En cualquier documento, cada campo tiene un tipo de datos (por ejemplo, *cadena*, *fecha*o *largo*) y un valor. Puede especificar las asignaciones de un índice cuando se crea por primera vez el índice o que se puede deducir por Elasticsearch cuando se agreguen nuevos documentos a un tipo. Sin embargo, tenga en cuenta los siguientes puntos:

- Asignaciones generadas dinámicamente pueden provocar errores dependiendo de cómo se interpretan los campos cuando se agregan documentos a un índice. Por ejemplo, documento 1 podría contener un campo que contiene un número y causas Elasticsearch para agregar una asignación que especifica que este campo es un *tiempo*. Si se agrega un documento subsiguientes en qué campo contiene datos numéricos, se producirá un error. En este caso, campo debe probablemente haya se interpreta como una cadena cuando se agregó el primer documento. Especificar esta asignación cuando se crea el índice puede ayudar a evitar estos problemas.

- Diseñe sus documentos para evitar generar asignaciones excesiva como esto puede agregar significativa sobrecarga al realizar búsquedas, consumir una gran cantidad de memoria y causar que las consultas de errores encontrar datos. Adoptar una convención de nomenclatura coherente para los campos en los documentos que comparten el mismo tipo. Por ejemplo, no use nombres de campo como "nombre", "Nombre" y "Apellidos" en diferentes documentos. Usar el mismo nombre de campo en cada documento. Además, no intente usar valores como claves (este es un enfoque común en las bases de datos de la familia de columna, pero puede provocar errores y eficacia con Elasticsearch.) Para obtener más información, vea [Asignación de explosión](https://www.elastic.co/blog/found-crash-elasticsearch#mapping-explosion).

- Use *not_analyzed* para evitar tokenización donde corresponda. Por ejemplo, si un documento contiene un campo de cadena denominado *datos* que contiene el valor "ABC definición" podría intentar realizar una búsqueda de todos los documentos que coinciden con este valor como sigue:

  ```http
  GET /myindex/mydata/_search
  {
    "query" : {
      "filtered" : {
        "filter" : {
          "term" : {
            "data" : "ABC-DEF"
          }
        }
      }
    }
  }
  ```

 Sin embargo, se producirá un error de la búsqueda devolver los resultados esperados debido a la forma en que se acorta la cadena ABC definición cuando está indizado. Se divida eficaz en dos tokens, ABC y definición, por el guión. Esta característica está diseñada para admitir la búsqueda de texto completo, pero si desea que la cadena se interpreta como un solo elemento atómico debe deshabilitar tokenización cuando el documento se agrega al índice. Puede usar una asignación como esta:

  ```http
  PUT /myindex
  {
    "mappings" : {
      "mydata" : {
        "properties" : {
          "data" : {
            "type" : "string",
            "index" : "not_analyzed"
          }
        }
      }
    }
  }
  ```

  Para obtener más información, vea [Buscar valores exactos](https://www.elastic.co/guide/en/elasticsearch/guide/current/_finding_exact_values.html#_term_filter_with_text).


### <a name="using-doc-values"></a>Con valores del documento

Muchas consultas y agregaciones requieren que se ordenan los datos como parte de la operación de búsqueda. Ordenación requiere poder asignar uno o varios términos a una lista de documentos. Para ayudar en este proceso, puede cargar Elasticsearch todos los valores de un campo utilizado como criterio de ordenación en memoria. Esta información se conoce como *fielddata*. El objetivo es que el almacenamiento en caché fielddata en memoria incurre menos i/OS y podría ser más rápido que lea repetidamente los mismos datos desde disco. Sin embargo, si tiene un campo cardinalidad alta, a continuación, almacenar el fielddata en la memoria puede consumir una gran cantidad de espacio en el montón, posiblemente que afectan a la capacidad de realizar otras operaciones simultáneas o incluso agote almacenamiento causando Elasticsearch errores.

Como alternativa, Elasticsearch también es compatible con *los valores de documento*. Un valor de documento es similar a un elemento de fielddata en memoria, excepto en que se almacena en disco y se crean cuando los datos se almacenan en un índice (fielddata se construye dinámicamente cuando se realiza una consulta.) Valores de documento no consumen espacio de montón y así que son útiles para las consultas que ordenar o agregar datos a través de campos que pueden contener un gran número de valores únicos. Además, la presión reducida del montón puede ayudar a las diferencias de rendimiento entre recuperar datos del disco y la lectura de la memoria de desplazamiento. Recolección es probable que se produzca con menos frecuencia y otras operaciones simultáneas que utilizan memoria están poco probable que se haya realizado.

Habilitar o deshabilitar los valores de documento en un según las propiedades de un índice mediante el atributo *doc_values* , tal como se muestra en el ejemplo siguiente:

```http
PUT /myindex
{
  "mappings" : {
    "mydata" : {
      "properties" : {
        "data" : {
          ...
          "doc_values": true
        }
      }
    }
  }
}
```
> [AZURE.NOTE] Valores del documento están habilitados de forma predeterminada con Elasticsearch versión 2.0.0 en adelante.

El impacto exacto de con valores del documento es probable que ser muy específico escenarios de datos y la consulta, así que esté preparado para realizar pruebas de rendimiento para establecer su utilidad. También debe tener en cuenta a que doc valores no funcionan con campos de cadena analizada. Para obtener más información, vea [Los valores de documento](https://www.elastic.co/guide/en/elasticsearch/guide/current/doc-values.html#doc-values).

### <a name="using-replicas-to-reduce-query-contention"></a>Uso de réplicas para reducir el conflicto de consulta

Una estrategia común para mejorar el rendimiento de las consultas es crear réplicas muchos de cada índice. Operaciones de recuperación de datos pueden cumplirse obteniendo los datos de una réplica. Sin embargo, esta estrategia gravemente puede afectar al rendimiento de las operaciones de recopilación de datos, por lo que necesita para utilizar con cuidado en escenarios que implican cargas de trabajo mixtas. Además, esta estrategia es solo de beneficio si réplicas se distribuyen entre los nodos y no competir por los recursos con shards principales que forman parte del mismo índice. Recuerde que es posible aumentar o reducir el número de réplicas para un índice dinámicamente.

### <a name="using-the-shard-request-cache"></a>Uso de la caché de solicitud de partes

Elasticsearch puede almacenar en caché los datos locales solicitados por consultas en cada fragmentar en memoria. Este búsquedas permite recuperar los mismos datos se ejecute más rápidamente, pueden leerse datos de almacenamiento de memoria en lugar de en disco. Caché de datos de este modo, por tanto, pueden mejorar el rendimiento de algunas operaciones de búsqueda, a costa de reducir la memoria disponible para otras tareas que se llevan a cabo simultáneamente. También es el riesgo que obtener datos de la caché no está actualizado. Los datos en la memoria caché solo se invalidan cuando se actualiza la fragmentar y ha cambiado los datos. La frecuencia de las actualizaciones de se rige por el valor de la configuración de *refresh_interval* del índice.

La solicitud de almacenamiento en caché para un índice está deshabilitada de forma predeterminada, pero puede habilitar como sigue:

```http
PUT /myindex/_settings
{
  "index.requests.cache.enable": true
}
```

La caché de solicitud de fragmentar es más adecuada para la información que se mantiene relativamente estático, como datos históricos o registro.

### <a name="using-client-nodes"></a>Uso de nodos de cliente

Todas las consultas se procesan el nodo que recibe la solicitud por primera vez. Este nodo envía más solicitudes a todos los demás nodos que contiene shards para los índices que se consulta y, a continuación, acumula los resultados para devolver la respuesta. Si trata de una consulta de agregación de datos o realizar cálculos complejos, el nodo inicial es responsable de realizar el procesamiento adecuado. Si el sistema tiene que admitir un pequeño número de consultas complejas, considere la posibilidad de crear un grupo de cliente nodos a reducir la carga de los nodos de datos. Por el contrario, si el sistema tiene manejar un gran número de consultas sencillas, enviar, a continuación, estas solicitudes directamente a los nodos de datos y usan un equilibrador de carga para distribuir las solicitudes de manera uniforme.

### <a name="tuning-queries"></a>Optimización de consultas

Los siguientes puntos resumen sugerencias para maximizar el rendimiento de las consultas de Elasticsearch:

- Evitar las consultas que implican comodines siempre que sea posible.

- Si el mismo campo está sujeto a texto exacto y búsquedo coincidentes, considere la posibilidad de almacenar los datos del campo en formularios analizadas y nonanalyzed. Realizar búsquedas de texto completo en el campo analizada y coincidencias exactas con el campo nonanalyzed.

- Solo devolver los datos necesarios. Si tiene documentos grandes, pero una aplicación sólo requiere la información contenida en un subconjunto de los campos, devolver este subconjunto de consultas, en lugar de documentos completos. Esta estrategia puede reducir los requisitos de ancho de banda de red del clúster.

- Siempre que sea posible, use filtros en lugar de las consultas de búsqueda de datos. Un filtro simplemente determina si un documento coincide con un criterio determinado mientras que una consulta calcula también cómo cerrar una coincidencia con un documento (puntuación). Internamente, los valores generados por un filtro se almacenan como un mapa de bits que indica la coincidencia/no coincidencia de cada documento y Elasticsearch pueden almacenarse en caché. Si el mismo criterio de filtro se produce posteriormente, el mapa de bits se puede recuperar de caché y usado para recuperar rápidamente los documentos coincidentes. Para obtener más información, vea [Interno operación de filtrado](https://www.elastic.co/guide/en/elasticsearch/guide/current/_finding_exact_values.html#_internal_filter_operation).

- Usar filtros de *bool* para realizar comparaciones estáticas y solo usa * *o*y, *no* filtros*para filtros dinámicamente calculados, como los que implican scripting o la *geo -\* * filtros.

- Si una consulta combina *bool* filtros con *y*, *o*, o *no* con *geo -* * filtros, coloque el *y*/*o*/*geo-** filtros última para que operan con el más pequeño posible de conjunto de datos.

    Asimismo, use una *post_filter* para ejecutar las operaciones de filtro caro. Última se realizará estos filtros.

- Utilizar agregaciones en lugar de facetas. Si no desea calcular agregados que se analizan o que tienen muchos valores posibles.

    > **Nota**: se han quitado facetas en Elasticsearch versión 2.0.0.

- Use la agregación de *cardinalidad* preferentemente la agregación *value_count* a menos que la aplicación requiere un número exacto de elementos coincidentes. Un recuento exacto puede convertirse en obsoleto rápidamente y muchas aplicaciones solo establece una aproximación razonable.

- Evitar el scripting. Secuencias de comandos en consultas y filtros pueden ser muy costosas y los resultados no se almacenan en caché. Las secuencias de comandos de ejecución larga pueden consumir búsqueda subprocesos indefinidamente, causando las solicitudes subsiguientes para poner en cola. Si la cola llena, aún más solicitudes serán rechazadas.

## <a name="testing-and-analyzing-aggregation-and-search-performance"></a>Probar y analizar el rendimiento de búsqueda y agregación

Esta sección describen los resultados de una serie de pruebas que se realizaron contra diversos clúster y configuraciones de índice. Dos tipos de pruebas se realizaron, como sigue:

- *Consultas y *la *recopilación* de prueba **. Esta prueba se inicia con un índice vacío que se rellena como la prueba seguidos de realizar operaciones de inserción masiva (cada operación agregado documentos 1000). Al mismo tiempo, un número de consultas diseñadas para buscar documentos agregados durante el período de 15 minutos anterior y generar agregaciones se repite a intervalos segunda 5. Esta prueba normalmente se puede ejecutar 24 horas para reproducir los efectos de una carga de trabajo difícil que incluya la recopilación de datos a gran escala con cerca de las consultas en tiempo real.

- **La *consulta solo* probar**. Esta prueba es similar a la *recopilación y consulta* probar excepto en que se omite la parte de recopilación y el índice en cada nodo se rellena automáticamente con 100 millones de documentos. Se realiza un conjunto de consultas modificado; el elemento de tiempo limitar los documentos a los que agregó en los últimos 15 minutos se quitó como ahora estaban estático. Las pruebas se ejecutaban para 90 minutos, hay menos tiempo necesario para establecer un modelo de rendimiento debido a la cantidad fija de datos.

---

Todos los documentos en el índice tenían el mismo esquema. Esta tabla resumen los campos en el esquema:

Nombre                          | Tipo         | Notas |
  ----------------------------- | ------------ | -------------------------------------------------------- |
  Organización                  | Cadena      | La prueba genera 200 organizaciones únicas. |
  Campopersonalizado1 - CustomField5   |Cadena       |Estos son los cinco campos de cadena que se establecen en una cadena vacía.|
  DateTimeRecievedUtc           |Marca de tiempo    |La fecha y hora en que se agregó el documento.|
  Host                          |Cadena       |Este campo se establece en una cadena vacía.|
  HttpMethod                    |Cadena       |Este campo se establece en uno de los valores siguientes: "POST", "Obtener", "Colocar".|
  HttpReferrer                  |Cadena       |Este campo se establece en una cadena vacía.|
  HttpRequest                   |Cadena       |Este campo se rellena con texto aleatorio entre 10 y 200 caracteres de longitud.|
  HttpUserAgent                 |Cadena       |Este campo se establece en una cadena vacía.|
  HttpVersion                   |Cadena       |Este campo se establece en una cadena vacía.|
  NombreDeOrganización              |Cadena       |Este campo se establece en el mismo valor que el campo de la organización.|
  SourceIp                      |IP           |Este campo contiene una dirección IP que indica que el "origen" de los datos. |
   SourceIpAreaCode              |Largo         |Este campo se establece en 0.|
  SourceIpAsnNr                 |Cadena       |Este campo se establece en "AS\#\#\#\#\#".|
  SourceIpBase10                |Largo         |Este campo se establece en 500.|
  SourceIpCountryCode           |Cadena       |Este campo contiene un código de país de 2 caracteres. |
  SourceIpCity                  |Cadena       |Este campo contiene una cadena que identifica una ciudad en un país. |
  SourceIpLatitude              |Doble       |Este campo contiene un valor aleatorio.|
  SourceIpLongitude             |Doble       |Este campo contiene un valor aleatorio.|
  SourceIpMetroCode             |Largo         |Este campo se establece en 0.|
  SourceIpPostalCode            |Cadena       |Este campo se establece en una cadena vacía.|
  SourceLatLong                 |Punto de geo   |Este campo se establece en un punto geográfico aleatorio.|
  PuertoDeOrigen                    |Cadena       |Este campo se rellena con la representación de cadena de un número aleatorio.|
  TargetIp                      |IP           |Esto se rellena con una dirección IP aleatoria del rango 0.0.100.100 a 255.9.100.100.|
  SourcedFrom                   |Cadena       |Este campo se establece en la cadena "MonitoringCollector".|
  TargetPort                    |Cadena       |Este campo se rellena con la representación de cadena de un número aleatorio.|
  Valoración                        |Cadena       |Este campo se rellena con uno de 20 distintos valores de cadena seleccionados al azar.|
  UseHumanReadableDateTimes     |Valor booleano      |Este campo está establecido en falso.|
 
Las consultas siguientes se realizaron como un lote por cada iteración de las pruebas. Los nombres en cursiva se usan para hacer referencia a estas consultas en el resto de este documento. Tenga en cuenta que el criterio de tiempo (documentos agregados en los últimos 15 minutos) se omite de la *consulta solo* pruebas:

- ¿Cuántos documentos con cada valor de *clasificación* se han incluido en los últimos 15 minutos (*recuento por clasificación*)? 

- ¿Se han agregado cuántos documentos en cada intervalo de 5 minutos durante los últimos 15 minutos (*recuento a lo largo del tiempo*)?

- ¿Se han agregado cuántos documentos de cada valor *de clasificación* de los países en los últimos 15 minutos (*visitas por país*)?

- ¿Qué 15 organizaciones producen la mayoría de los documentos con frecuencia en agregado en los últimos 15 minutos (*organizaciones principio 15*)?

- ¿Cuántos diferentes organizaciones se producen en documentos agregados en los últimos 15 minutos (*organizaciones de recuento únicas*)?

- ¿Se han agregado cuántos documentos en los últimos 15 minutos (*recuento Total de visitas*)?

- ¿Cuántos valores *SourceIp* diferentes se producen en documentos agregados en los últimos 15 minutos (*recuento de IP única*)?


La definición del índice y los detalles de las consultas se describen en el [Apéndice](#appendix-the-query-and-aggregation-performance-test).

Las pruebas se han diseñado para comprender los efectos de las variables siguientes:

- **Tipo de disco**. Pruebas se han realizado en un clúster de nodo de 6 de VM D4 mediante almacenamiento estándar (unidades de disco duro) y repetir en un clúster de nodo de 6 de máquinas virtuales de DS4 mediante almacenamiento premium (SSDs).

- **El tamaño del equipo - escalado**. Pruebas se han realizado en un clúster de nodo de 6 que incluya VM DS3 (designado como el clúster *pequeño* ), repetidas en un clúster de máquinas virtuales de DS4 (el clúster *medio* ) y repite nuevamente en un clúster de equipos de DS14 (el clúster *grande* ). La siguiente tabla resume las características clave de cada SKU VM:

 Clúster | SKU DE VM        | Número de núcleos | Número de discos de datos | MEMORIA RAM (GB) |
---------|---------------|-----------------|----------------------|----------|
 Pequeña   | DS3 estándar  | 4               | 8                    | 14       |
 Medio  | DS4 estándar  | 8               | 16                   | 28       |
 Grande   | DS14 estándar | 16              | 32                   | 112      |

- **Tamaño de clúster - escalado**. Pruebas se realizaron en clústeres de máquinas virtuales de DS14 que contiene 1, 3 y 6 nodos.

- **Número de réplicas de índice**. Pruebas se realizaron utilizando índices configurados con réplicas 1 y 2.

- **Valores de documento**. Inicialmente, las pruebas se realizaron con el índice establecer *doc_values* establecida en *true* (el valor predeterminado). Pruebas seleccionadas se han repetido con *doc_values* establecido en *false*.

- **Almacenamiento en caché**. Se realizaron pruebas con la caché de solicitud de fragmentar habilitada en el índice.

- **Número de shards**. Pruebas se repiten con distintos números de shards establecer si las consultas se ejecutaban más eficazmente a través de índices que contengan shards menos, más grandes o más pequeños, más shards.


## <a name="performance-results---disk-type"></a>Resultados de rendimiento - tipo de disco

Rendimiento del disco se evalúa mediante la ejecución de la prueba de *recopilación y la consulta* en el clúster de nodo de 6 de VM D4 (con unidades de disco duro) y, en el clúster de nodo de 6 de máquinas virtuales de DS4 (con SSDs). La configuración de Elasticsearch en ambos clústeres era el mismo. Los datos se extiendan a través de 16 discos en cada nodo y cada nodo tenía 14GB de memoria RAM asignada a la máquina virtual de Java (JVM) ejecutando Elasticsearch, se dejó la memoria restante (también 14GB) para uso del sistema operativo. Cada prueba ejecutó 24 horas. Este período se ha seleccionado para habilitar los efectos de la creciente volumen de datos para convertirse en aparente y permitir que el sistema a regular. La siguiente tabla resume los resultados, resaltar los tiempos de respuesta de las diversas operaciones que consta de la prueba.

 Clúster | Operación o consulta            | Tiempo medio de respuesta (ms) |
---------|----------------------------|----------------------------|
 D4      | Recopilación                  | 978                        |
         | Recuento por clasificación            | 103                        |
         | Recuento de tiempo            | 134                        |
         | Visitas por país            | 199                        |
         | Organizaciones de 15 principales       | 137                        |
         | Organizaciones de recuento únicas | 139                        |
         | Recuento de IP única            | 510                        |
         | Recuento total de visitas           | 89                         |
 DS4     | Recopilación                  | 511                        |
         | Recuento por clasificación            | 187                        |
         | Recuento de tiempo            | 411                        |
         | Visitas por país            | 402                        |
         | Organizaciones de 15 principales       | 307                        |
         | Organizaciones de recuento únicas | 320                        |
         | Recuento de IP única            | 841                        |
         | Recuento total de visitas           | 236                        |

A primera vista, podría parecer que el clúster DS4 realiza consultas menos bueno que el clúster D4, a veces redoble (o peor) el tiempo de respuesta. No se muestra todo el panorama aunque. La siguiente tabla muestra el número de operaciones de recopilación realizadas por cada clúster (Recuerde que cada operación carga los documentos de 1000):

 Clúster | Número de operaciones de recopilación |
---------|---------------------------|
 D4      | 264769                    |
 DS4     | 503157                    |

El clúster DS4 pudo cargar casi el doble datos que el clúster D4 durante la prueba. Por lo tanto, cuando se analizan los tiempos de respuesta para cada operación, también debe tener en cuenta cuántos documentos cada consulta tiene que analizar y devuelve el número de documentos. Estos son cifras dinámicas como el volumen de documentos en el índice está aumentando continuamente. No se puede dividir simplemente 503137 por 264769 (el número de operaciones de recopilación realizadas por cada clúster) y, a continuación, multiplique el resultado por el tiempo medio de respuesta para cada consulta realizado por el clúster D4 para dar a la información comparativa como esto pasa por alto la cantidad de i/OS llevando a cabo simultáneamente la operación de recopilación. En su lugar, debe medir la cantidad de datos que se escriben en físico y leer desde el disco mientras se realiza la prueba. El plan de pruebas JMeter captura esta información para cada nodo. Los resultados resumidos son:

 Clúster | Promedio de bytes escrito o leer por cada operación |
---------|----------------------------------------------|
 D4      | 13471557                                     |
 DS4     | 24643470                                     |

Estos datos muestran que el clúster DS4 pudo respaldar una tasa de i/OS aproximadamente 1,8 veces que del clúster D4. Dado que, aparte de la naturaleza de los discos, todos los demás recursos son los mismos, la diferencia debe ser debido a que utiliza SSDs prefiere unidades de disco duro.

Para ayudar a justificar esta conclusión, los gráficos siguientes muestran cómo las i/OS se realizó con el tiempo por cada clúster:

![](./media/guidance-elasticsearch/query-performance2.png)

<!-- -->

***Actividad de disco para los clústeres D4 y DS4***

El gráfico para el clúster D4 muestra variación significativa, especialmente durante la primera mitad de la prueba. Esto era probablemente limitación para reducir la tasa de i/OS. En la fase inicial de la prueba son puede ejecutar rápidamente porque no hay datos pequeño para analizar las consultas. Los discos de clúster de D4, por tanto, suelen estar funcionando cerca de las operaciones de entrada y salida por segunda capacidad (IOPS), aunque cada operación de i/OS no puede devolver datos reales. El clúster DS4 admite una mayor tasa IOPS y no verá afectado el mismo nivel de limitación, las tasas de i/OS son más normales. Para admitir esta teoría, el siguiente par de gráficos mostrar cómo ha bloqueado la CPU i/OS del disco en el tiempo (los tiempos de espera de disco se muestra en los gráficos son la proporción del tiempo que la CPU en espera para i/OS):

![](./media/guidance-elasticsearch/query-performance3.png)

***Disco de CPU i/OS esperar horas para los clústeres D4 y DS4***

Es importante saber que hay dos razones principales para operaciones de i/OS bloquear la CPU:

- El subsistema de i/OS se puede leer o escribir datos a o desde el disco.

- El subsistema de i/OS se podría acelerado por el entorno de host. Discos Azure implementados mediante unidades de disco duro tienen un rendimiento máximo de 500 IOPS y SSDs tienen un rendimiento máximo de 5000 IOPS.


Para el clúster D4, la cantidad de tiempo en espera para i/OS durante la primera mitad de la correlaciona prueba estrechamente de forma invertida con el gráfico que muestra los tipos de i/OS. Períodos de baja i/OS corresponden a los períodos de tiempo significativo que la CPU dedica bloqueados, que indica que se está limitando i/OS. Como se agregan más datos al clúster la situación cambia y, a continuación, en la segunda mitad de los picos de prueba en i/OS tiempos de espera se corresponden con picos en el rendimiento de i/OS. En este momento, se bloquea la CPU mientras lleva a cabo real i/OS. Nuevo, con el clúster DS4, el tiempo invertido esperando i/OS es incluso mucho más. Cada máximo coincide con un máximo de rendimiento en lugar de un mediante equivalente, esto significa que hay poco o ningún límite que se producen.

Hay un factor a tener en cuenta. Durante la prueba, el clúster D4 genera 10584 errores de recopilación y 21 errores de consulta. La prueba en el clúster DS4 no producidos errores.

## <a name="performance-results---scaling-up"></a>Resultados de rendimiento - escalado

Escalado pruebas se realizan ejecutando pruebas contra clústeres de nodo de 6 de DS3, DS4 y máquinas virtuales de DS14. Estos SKU se han seleccionado porque una VM DS4 proporciona dos veces más núcleos de CPU y la memoria como un DS3 y una máquina DS14 duplica los recursos de la CPU nuevamente proporcionando cuatro veces la cantidad de memoria. La siguiente tabla compara los aspectos clave de cada SKU:

 SKU  | \#Núcleos de CPU | Memoria (GB) | Disco Max IOPS | Ancho de banda de Max (MB/s)|
------|-------------|-------------|---------------|--------------|
 DS3  | 4           | 14          | 12.800| 128 |
 DS4  | 8           | 28          | 25,600| 256 |
 DS14 | 16          | 112         | 50.000| 512 |

La siguiente tabla resume los resultados de las pruebas en la pequeña (DS3), Media (DS4) y grandes clústeres (DS14). Cada VM utiliza SSDs para mantener los datos. Cada prueba ejecutó 24 horas.

La tabla informa del número de intentos de solicitud para cada tipo de consulta (errores no están incluidos). El número de solicitudes intentado para cada tipo de consulta es aproximadamente el mismo durante una ejecución de la prueba. Esto es porque el plan de pruebas JMeter ejecuta una sola repetición de cada consulta (recuento por valoración, recuento de tiempo, visitas por país, principio 15 organizaciones, únicos de las organizaciones de recuento, recuento de IP únicas y recuento Total de visitas) juntos en una sola unidad conocida como una *transacción de prueba* (esta transacción depende de la tarea que realiza la operación de recopilación, que se ejecuta en un subproceso distinto). Cada iteración del plan de prueba realiza una transacción de prueba individual. El número de transacciones de ensayo completado, por tanto, es una medida de la hora de respuesta de la consulta más lenta en cada transacción.

| Clúster      | Operación o consulta            | Número de solicitudes | Tiempo medio de respuesta (ms) |
|--------------|----------------------------|--------------------|----------------------------|
| Pequeño (DS3)  | Recopilación                  | 207284             | 3328                       |
|              | Recuento por clasificación            | 18444              | 268                        |
|              | Recuento de tiempo            | 18444              | 340                        |
|              | Visitas por país            | 18445              | 404                        |
|              | Organizaciones de 15 principales       | 18439              | 323                        |
|              | Organizaciones de recuento únicas | 18437              | 338                        |
|              | Recuento de IP única            | 18442              | 468                        |
|              | Recuento total de visitas           | 18428              | 294   
|||||
| Medio (DS4) | Recopilación                  | 503157             | 511                        |
|              | Recuento por clasificación            | 6958               | 187                        |
|              | Recuento de tiempo            | 6958               | 411                        |
|              | Visitas por país            | 6958               | 402                        |
|              | Organizaciones de 15 principales       | 6958               | 307                        |
|              | Organizaciones de recuento únicas | 6956               | 320                        |
|              | Recuento de IP única            | 6955               | 841                        |
|              | Recuento total de visitas           | 6958               | 236                        |
|||||
| Grande (DS14) | Recopilación                  | 502714             | 511                        |
|              | Recuento por clasificación            | 7041               | 201                        |
|              | Recuento de tiempo            | 7040               | 298                        |
|              | Visitas por país            | 7039               | 363                        |
|              | Organizaciones de 15 principales       | 7038               | 244                        |
|              | Organizaciones de recuento únicas | 7037               | 283                        |
|              | Recuento de IP única            | 7037               | 681                        |
|              | Recuento total de visitas           | 7038               | 200                        |

Mostrar estas cifras que, para esta prueba, el rendimiento del clúster DS4 y DS14 fueron razonablemente similares. También aparecerán los tiempos de respuesta para las operaciones de consulta para el clúster DS3 comparar de manera favorable inicialmente y el número de operaciones de consulta realizadas es extremo superior a los valores para el clúster DS4 y DS14. Sin embargo, una debería tomarse fuerte aviso de la tasa de recopilación y el número de documentos que se buscan consecutivas. En el clúster DS3 se limita mucho más recopilación y al final de la prueba de la base de datos contiene solo aproximadamente un 40% de los documentos que se lee en cada uno de los otros dos clústeres. Esto es podrían ser debido a los recursos de procesamiento, la red y el ancho de banda de disco disponible para una VM DS3 en comparación con un DS4 o DS14 VM. Dado que una VM DS4 tiene dos veces el número de recursos disponible como una VM DS3 y un DS14 tiene dos veces (cuatro veces memoria) los recursos de una VM DS4, aún queda una pregunta: ¿por qué es la diferencia en tasas de recopilación entre los clústeres DS4 y DS14 significativamente inferior a la que se produce entre los clústeres DS3 y DS4? Esto puede deberse a los límites de ancho de banda de Azure VM y el uso de la red. Los gráficos siguientes muestran estos datos para todos los tres clústeres:

![](./media/guidance-elasticsearch/query-performance4.png)

**Utilización de la red para los clústeres DS3, DS4 y DS14 realizar la prueba de recopilación y consulta** 

<!-- -->

Los límites de ancho de banda de red disponible con máquinas virtuales de Azure no se publican y pueden variar, pero el hecho de que la actividad de red parece ha levelled un promedio de 2.75GBps alrededor de ambas la DS4 y DS14 pruebas sugiere que dicho límite se ha alcanzado y se ha convertido en el principal factor de restricción de rendimiento. En el caso del clúster DS3, la actividad de red era mucho más para que el rendimiento inferior es más probable que debido a las restricciones en la disponibilidad de otros recursos.

Para aislar los efectos de las operaciones de recopilación e ilustran cómo el rendimiento de la consulta varía como nodos escalar, un conjunto de pruebas solo consulta se realizó con los mismos nodos. La siguiente tabla resume los resultados obtenidos en cada clúster:

> [AZURE.NOTE] No debe comparar el rendimiento y el número de solicitudes ejecutadas por las consultas de la *consulta solo* prueba con los ejecutar la prueba de *recopilación y la consulta* . Esto es porque se han modificado las consultas y el volumen de documentos relacionados es diferente.

| Clúster      | Operación o consulta            | Número de solicitudes | Calcular el promedio de respuesta Ttme (ms) |
|--------------|----------------------------|--------------------|----------------------------|
| Pequeño (DS3)  | Recuento por clasificación            | 464                | 11758                      |
|              | Recuento de tiempo            | 464                | 14699                      |
|              | Visitas por país            | 463                | 14075                      |
|              | Organizaciones de 15 principales       | 464                | 11856                      |
|              | Organizaciones de recuento únicas | 462                | 12314                      |
|              | Recuento de IP única            | 461                | 19898                      |
|              | Recuento total de visitas           | 462                | 8882  
|||||
| Medio (DS4) | Recuento por clasificación            | 1045               | 4489                       |
|              | Recuento de tiempo            | 1045               | 7292                       |
|              | Visitas por país            | 1053               | 7564                       |
|              | Organizaciones de 15 principales       | 1055               | 5066                       |
|              | Organizaciones de recuento únicas | 1051               | 5231                       |
|              | Recuento de IP única            | 1051               | 9228                       |
|              | Recuento total de visitas           | 1051               | 2180                       |
|||||
| Grande (DS14) | Recuento por clasificación            | 1842               | 1927                       |
|              | Recuento de tiempo            | 1839               | 4483                       |
|              | Visitas por país            | 1838               | 4761                       |
|              | Organizaciones de 15 principales       | 1842               | 2117                       |
|              | Organizaciones de recuento únicas | 1837               | 2393                       |
|              | Recuento de IP única            | 1837               | 7159                       |
|              | Recuento total de visitas           | 1837               | 642                        |

En este momento, las tendencias en los tiempos de respuesta promedio entre los diferentes clústeres es más claro. Utilización de la red está por debajo de la 2.75GBps anteriormente necesarios para que los clústeres DS4 y DS14 (que probablemente saturación la red en las pruebas de recopilación y consulta) y la 1.5GBps para el clúster DS3. De hecho, es más cercana a 200MBps en todos los casos, como se muestra en los gráficos siguientes:

![](./media/guidance-elasticsearch/query-performance5.png)

***Uso de la DS3 de la red DS4 y DS14 clústeres realizar la prueba de consulta*** 

El factor de limitación en los clústeres DS3 y DS4 ahora parece ser el uso de CPU, que es cerca del 100% para el gran parte de la hora. En la DS14 el uso de la CPU de clúster más medias 80%. Esto es todavía alto, pero resalta claramente las ventajas de tener más núcleos de CPU disponibles. La imagen siguiente muestra los patrones de uso de CPU para los clústeres DS3, DS4 y DS14.

![](./media/guidance-elasticsearch/query-performance6.png)

***Uso de la CPU para los clústeres DS3 y DS14 realizar la prueba de consulta*** 

## <a name="performance-results---scaling-out"></a>Resultados de rendimiento - escalado

Para ilustrar cómo el sistema se escala con el número de nodos, pruebas se ejecutan con clústeres DS14 que contiene 1, 3 y 6 nodos. Sólo esta vez, la *consulta solo* se realizó la prueba usando 100 millones de documentos y ejecución para 90 minutos:

| Clúster | Operación o consulta            | Número de solicitudes | Tiempo medio de respuesta (ms) |
|---------|----------------------------|--------------------|----------------------------|
| 1 nodo  | Recuento por clasificación            | 288                | 6216                       |
|         | Recuento de tiempo            | 288                | 28933                      |
|         | Visitas por país            | 288                | 29455                      |
|         | Organizaciones de 15 principales       | 288                | 9058                       |
|         | Organizaciones de recuento únicas | 287                | 19916                      |
|         | Recuento de IP única            | 284                | 54203                      |
|         | Recuento total de visitas           | 287                | 3333                       |
|||||
| 3 nodos | Recuento por clasificación            | 1194               | 3427                       |
|         | Recuento de tiempo            | 1194               | 5381                       |
|         | Visitas por país            | 1191               | 6840                       |
|         | Organizaciones de 15 principales       | 1196               | 3819                       |
|         | Organizaciones de recuento únicas | 1190               | 2938                       |
|         | Recuento de IP única            | 1189               | 12516                      |
|         | Recuento total de visitas           | 1191               | 1272                       |
|||||
| Nodos de 6 | Recuento por clasificación            | 1842               | 1927                       |
|         | Recuento de tiempo            | 1839               | 4483                       |
|         | Visitas por país            | 1838               | 4761                       |
|         | Organizaciones de 15 principales       | 1842               | 2117                       |
|         | Organizaciones de recuento únicas | 1837               | 2393                       |
|         | Recuento de IP única            | 1837               | 7159                       |
|         | Recuento total de visitas           | 1837               | 642                        |

El número de nodos hace una diferencia significativa en el rendimiento de la consulta del clúster, aunque en forma no lineal. El clúster de 3 nodo completa aproximadamente 4 horas el muchas consultas como clúster de nodo único, mientras que el clúster de 6 nodo controla 6 horas como muchos. Para ayudar a explicar este error de linealidad, los gráficos siguientes muestran cómo la CPU se consumen los tres clústeres:

![](./media/guidance-elasticsearch/query-performance7.png)

***Uso de la CPU para el 1, 3 y clústeres de nodo de 6 realizar la prueba de consulta***

Los clústeres de nodo único y 3 son la CPU, mientras aunque la CPU es alta en el clúster de nodo de 6 hay capacidad de procesamiento adicional. En este caso, otros factores están probable que pueden limitar el rendimiento. Esto podría confirmado probando con nodos 9 y 12, que es probable que desea mostrar más capacidad de procesamiento adicional.

Los datos de la tabla anterior también muestran cómo varían los tiempos de respuesta promedio de las consultas. Este es el elemento que está más informativo al probar cómo se ajusta un sistema para determinados tipos de consulta. Algunas búsquedas son claramente mucho más eficientes cuando que ocupan más nodos que otras personas. Esto puede deberse a la relación entre el número de nodos y el número de documentos en el clúster creciente, cada clúster contenía 100 millones de documentos. Al realizar búsquedas que implican agregar datos, Elasticsearch procesará y los datos recuperados como parte del proceso de agregación en memoria en cada nodo de búfer. Si hay más nodos, hay menos datos recuperar, búfer, y procesar en cada nodo.

## <a name="performance-results---number-of-replicas"></a>Resultados de rendimiento - número de réplicas

Las pruebas de *recopilación y la consulta* se ejecutan en un índice con una única réplica. Las pruebas se han repetido en los clústeres de nodo de 6 DS4 y DS14 con un índice configurado con dos réplicas. Todas las pruebas ejecutaban 24 horas. La siguiente tabla muestra los resultados de comparativos de uno y dos réplicas:

| Clúster | Operación o consulta            | Tiempo medio de respuesta (ms) - 1 réplica | Tiempo medio de respuesta (ms) - 2 réplicas | % de la diferencia de tiempo de respuesta |
|---------|----------------------------|----------------------------------------|-----------------------------------------|-------------------------------|
| DS4     | Recopilación                  | 511                                    | 655                                     | + 28%                          |
|         | Recuento por clasificación            | 187                                    | 168                                     | -10%                          |
|         | Recuento de tiempo            | 411                                    | 309                                     | -25%                          |
|         | Visitas por país            | 402                                    | 562                                     | + 40%                          |
|         | Organizaciones de 15 principales       | 307                                    | 366                                     | + 19%                          |
|         | Organizaciones de recuento únicas | 320                                    | 378                                     | + 18%                          |
|         | Recuento de IP única            | 841                                    | 987                                     | + 17%                          |
|         | Recuento total de visitas           | 236                                    | 236                                     | + 0%                           |
||||||
| DS14    | Recopilación                  | 511                                    | 618                                     | + 21%                          |
|         | Recuento por clasificación            | 201                                    | 275                                     | + 37%                          |
|         | Recuento de tiempo            | 298                                    | 466                                     | + 56%                          |
|         | Visitas por país            | 363                                    | 529                                     | + 46%                          |
|         | Organizaciones de 15 principales       | 244                                    | 407                                     | + 67%                          |
|         | Organizaciones de recuento únicas | 283                                    | 403                                     | + 42%                          |
|         | Recuento de IP única            | 681                                    | 823                                     | + 21%                          |
|         | Recuento total de visitas           | 200                                    | 221                                     | + 11%                          |

Disminuir la velocidad de recopilación como el número de réplicas mayor. Esto se debe esperar mientras está escribiendo Elasticsearch más copias de cada documento, generando i/OS de disco adicional.  Los gráficos para el clúster de DS14 por índices Esto refleja con réplicas 1 y 2 que se muestra en la imagen siguiente. En el caso de índice con la 1 réplica, la tasa promedio de i/OS era 16896573 bytes/segundo. Para el índice con 2 réplicas, la tasa promedio de i/OS fue 33986843 bytes/segundo, unos dos veces la cantidad.

![](./media/guidance-elasticsearch/query-performance8.png)

***Tasas de disco i/OS para nodos con 1 y 2 réplicas realizar la prueba de recopilación y consulta***

| Clúster | Consulta                      | Tiempo medio de respuesta (ms) - 1 réplica | Tiempo medio de respuesta (ms) - 2 réplicas |
|---------|----------------------------|----------------------------------------|-----------------------------------------|
| DS4     | Recuento por clasificación            | 4489                                   | 4079                                    |
|         | Recuento de tiempo            | 7292                                   | 6697                                    |
|         | Visitas por país            | 7564                                   | 7173                                    |
|         | Organizaciones de 15 principales       | 5066                                   | 4650                                    |
|         | Organizaciones de recuento únicas | 5231                                   | 4691                                    |
|         | Recuento de IP única            | 9228                                   | 8752                                    |
|         | Recuento total de visitas           | 2180                                   | 1909                                    |
|||||
| DS14    | Recuento por clasificación            | 1927                                   | 2330                                    |
|         | Recuento de tiempo            | 4483                                   | 4381                                    |
|         | Visitas por país            | 4761                                   | 5341                                    |
|         | Organizaciones de 15 principales       | 2117                                   | 2560                                    |
|         | Organizaciones de recuento únicas | 2393                                   | 2546                                    |
|         | Recuento de IP única            | 7159                                   | 7048                                    |
|         | Recuento total de visitas           | 642                                    | 708                                     |

Estos resultados muestran una mejora en el tiempo medio de respuesta para el clúster DS4, pero un aumento para el clúster de DS14. Ayuda para interpretar los resultados, también debe tener en cuenta el número de consultas realizadas por cada prueba:

| Clúster | Consulta                      | Número realizadas - 1 réplica | Número realizadas - 2 réplicas |
|---------|----------------------------|------------------------------|-------------------------------|
| DS4     | Recuento por clasificación            | 1054                         | 1141                          |
|         | Recuento de tiempo            | 1054                         | 1139                          |
|         | Visitas por país            | 1053                         | 1138                          |
|         | Organizaciones de 15 principales       | 1055                         | 1141                          |
|         | Organizaciones de recuento únicas | 1051                         | 1136                          |
|         | Recuento de IP única            | 1051                         | 1135                          |
|         | Recuento total de visitas           | 1051                         | 1136                          |
|||||
| DS14    | Recuento por clasificación            | 1842                         | 1718                          |
|         | Recuento de tiempo            | 1839                         | 1716                          |
|         | Visitas por país            | 1838                         | 1714                          |
|         | Organizaciones de 15 principales       | 1842                         | 1718                          |
|         | Organizaciones de recuento únicas | 1837                         | 1712                          |
|         | Recuento de IP única            | 1837                         | 1712                          |
|         | Recuento total de visitas           | 1837                         | 1712                          |

Estos datos muestran que aumenta el número de consultas realizadas por el clúster de DS4 en línea con la reducción de tiempo medio de respuesta, pero nuevamente al revés true del clúster DS14. Un factor importante es que el uso de CPU de la DS4 clúster en las pruebas de réplica de 1 y 2 réplica se extienden desigual. Algunos nodos mostrado cerca de la utilización del 100%, mientras que otros usuarios la capacidad de procesamiento adicional. La mejora del rendimiento suele deberse a mayor capacidad para distribuir el procesamiento entre los nodos del clúster. La imagen siguiente muestra la variación de CPU procesamiento entre las máquinas virtuales usadas más ligera e intensamente (nodos 4 y 3):

![](./media/guidance-elasticsearch/query-performance9.png)

***Uso de la CPU para los nodos usados más y menos usados en el clúster DS4 realizar la prueba de consulta***

Para el clúster DS14 no era el caso. Uso de CPU en ambos ensayos era inferior en todos los nodos y la disponibilidad de una segunda réplica se convirtió en menos de una de las ventajas y más de una sobrecarga:

![](./media/guidance-elasticsearch/query-performance10.png)

***Uso de la CPU para los nodos usados más y menos usados en el clúster DS14 realizar la prueba de consulta***

Estos resultados muestran la necesidad para evaluar su sistema con cuidado al decidir si usar varias réplicas. Siempre debe tener al menos una réplica de cada índice (a menos que está dispuesto a evitar el riesgo de pérdida de datos si se produce un error en un nodo), pero réplicas adicionales pueden suponer una carga en el sistema para poco beneficio, dependiendo de las cargas de trabajo y los recursos de hardware disponibles para el clúster.

## <a name="performance-results---doc-values"></a>Resultados de rendimiento - valores de documento

Las pruebas de *recopilación y la consulta* se realizaron con valores de documento habilitados, causando Elasticsearch almacenar los datos que se usa para ordenar los campos en el disco. Las pruebas se han repetido con valores de documento deshabilitados, por lo que Elasticsearch fielddata la construcción dinámicamente y en la memoria caché. Todas las pruebas ejecutaban 24 horas. La siguiente tabla compara los tiempos de respuesta de pruebas que se ejecute en clústeres de 6 nodos creados con D4, DS4 y máquinas virtuales de DS14 (el clúster D4 utiliza discos duro normales, mientras que los clústeres DS4 y DS14 utilizan SSDs).

| Clúster | Operación o consulta            | Tiempo medio de respuesta (ms) - valores de documento habilitados | Tiempo medio de respuesta (ms) - valores de documento deshabilitados | % de la diferencia de tiempo de respuesta |
|---------|----------------------------|-------------------------------------------------|--------------------------------------------------|-------------------------------|
| D4      | Recopilación                  | 978                                             | 835                                              | -15%                          |
|         | Recuento por clasificación            | 103                                             | 132                                              | + 28%                          |
|         | Recuento de tiempo            | 134                                             | 189                                              | + 41%                          |
|         | Visitas por país            | 199                                             | 259                                              | + 30%                          |
|         | Organizaciones de 15 principales       | 137                                             | 184                                              | + 34%                          |
|         | Organizaciones de recuento únicas | 139                                             | 197                                              | + 42%                          |
|         | Recuento de IP única            | 510                                             | 604                                              | + 18%                          |
|         | Recuento total de visitas           | 89                                              | 134                                              | + 51%                          |
||||||
| DS4     | Recopilación                  | 511                                             | 581                                              | + 14%                          |
|         | Recuento por clasificación            | 187                                             | 190                                              | + 2%                           |
|         | Recuento de tiempo            | 411                                             | 409                                              | -0,5%                         |
|         | Visitas por país            | 402                                             | 414                                              | + 3%                           |
|         | Organizaciones de 15 principales       | 307                                             | 284                                              | -7%                           |
|         | Organizaciones de recuento únicas | 320                                             | 313                                              | -% de 2                           |
|         | Recuento de IP única            | 841                                             | 955                                              | + 14%                          |
|         | Recuento total de visitas           | 236                                             | 281                                              | + 19%                          |
||||||
| DS14    | Recopilación                  | 511                                             | 571                                              | + 12%                          |
|         | Recuento por clasificación            | 201                                             | 232                                              | + 15%                          |
|         | Recuento de tiempo            | 298                                             | 341                                              | + 14%                          |
|         | Visitas por país            | 363                                             | 457                                              | + 26%                          |
|         | Organizaciones de 15 principales       | 244                                             | 338                                              | + 39%                          |
|         | Organizaciones de recuento únicas | 283                                             | 350                                              | + 24%                          |
|         | Recuento de IP única            | 681                                             | 909                                              | + 33%                          |
|         | Recuento total de visitas           | 200                                             | 245                                              | + 23%                          |

La siguiente tabla compara el número de operaciones de recopilación realizadas por las pruebas:

| Clúster | Número de operaciones de recopilación - valores de documento habilitados | Número de operaciones de recopilación - doc valores deshabilitado | % de la diferencia en \number de operaciones de recopilación |
|---------|----------------------------------------------|-----------------------------------------------|-----------------------------------------|
| D4      | 264769                                       | 408690                                        | + 54%                                    |
| DS4     | 503137                                       | 578237                                        | + 15%                                    |
| DS14    | 502714                                       | 586472                                        | + 17%                                    |

Los tipos de recopilación mejorada ocurrir con valores de documento deshabilitados como menos los datos se escriben en el disco mientras se insertan los documentos. El mejor rendimiento es especialmente evidente con VM D4 con unidades de disco duro para almacenar los datos. En este caso, el tiempo de respuesta para operaciones de recopilación también disminuido por 15% (consulte la primera tabla de esta sección). Esto podría debido a la presión reducida en el disco duro que pudiera ejecutarse a sus límites IOPS en la prueba con documento valores habilitados, consulte la prueba de tipo de disco para obtener más información. En el siguiente gráfico compara el rendimiento de i/OS de las VM D4 con valores de documento habilitados (valores mantenidos en disco) y documento valores a deshabilitado (valores mantenidos en memoria):

![](./media/guidance-elasticsearch/query-performance11.png)

***Para el clúster D4 con valores de documento activa y desactiva la actividad del disco***

En cambio, los valores de recopilación de las máquinas virtuales con SSDs mostrar un pequeño aumento en el número de documentos, pero también un aumento en el tiempo de respuesta de las operaciones de recopilación. Con uno o dos excepciones pequeñas, los tiempos de respuesta de consulta también fueron peores. La SSDs son reduce la probabilidad de que se esté ejecutando a sus límites IOPS con valores de documento habilitados, por lo que los cambios en el rendimiento son más probable es que debido a mayor procesamiento de actividad y la sobrecarga de administrar el montón JVM. Esto es evidente comparando el uso de CPU con valores de documento activa y desactiva. El siguiente gráfico resalta estos datos para el clúster DS4, donde la mayor parte de la utilización de la CPU se mueve de la banda de 30 y 40% con valores de documento habilitados para la banda de 40-50% con valores de documento deshabilitado (el clúster DS14 mostraba una tendencia similar):

![](./media/guidance-elasticsearch/query-performance12.png)

***Uso de CPU para el clúster de DS4 con valores de documento habilitado y deshabilitado***

Para distinguir los efectos de valores de documento en el rendimiento de la consulta de recopilación de datos, los pares de pruebas solo consulta se realizaron para los clústeres DS4 y DS14 con valores de documento activa y desactiva. La siguiente tabla resume los resultados de las pruebas:

| Clúster | Operación o consulta            | Tiempo medio de respuesta (ms) - valores de documento habilitados | Tiempo medio de respuesta (ms) - valores de documento deshabilitados | % de la diferencia de tiempo de respuesta |
|---------|----------------------------|-------------------------------------------------|--------------------------------------------------|-------------------------------|
| DS4     | Recuento por clasificación            | 4489                                            | 3736                                             | -16%                          |
|         | Recuento de tiempo            | 7293                                            | 5459                                             | -25%                          |
|         | Visitas por país            | 7564                                            | 5930                                             | -% de 22                          |
|         | Organizaciones de 15 principales       | 5066                                            | 3874                                             | -14%                          |
|         | Organizaciones de recuento únicas | 5231                                            | 4483                                             | -% de 2                           |
|         | Recuento de IP única            | 9228                                            | 9474                                             | + 3%                           |
|         | Recuento total de visitas           | 2180                                            | 1218                                             | -44%                          |
||||||
| DS14    | Recuento por clasificación            | 1927                                            | 2144                                             | + 11%                          |
|         | Recuento de tiempo            | 4483                                            | 4337                                             | -3%                           |
|         | Visitas por país            | 4761                                            | 4840                                             | + 2%                           |
|         | Organizaciones de 15 principales       | 2117                                            | 2302                                             | + 9%                           |
|         | Organizaciones de recuento únicas | 2393                                            | 2497                                             | + 4%                           |
|         | Recuento de IP única            | 7159                                            | 7639                                             | + 7%                           |
|         | Recuento total de visitas           | 642                                             | 633                                              | -% de 1                           |

Recuerde que, con Elasticsearch 2.0 y posteriores, los valores de documento están habilitados de forma predeterminada. En las pruebas que cubra el clúster DS4, deshabilitar valores del documento aparece para tener un efecto positivo general, mientras que al revés es generalmente true para el clúster de DS14 (los dos casos donde es mejor rendimiento con valores de documento deshabilitados son muy pequeñas).

Para el clúster DS4, uso de CPU en ambos casos fue cerca del 100% para la duración de las pruebas que indica que el clúster ha vinculado a la CPU. Sin embargo, el número de consultas procesada disminuido de 7369 5894 (20%). Recuerde que si los valores del documento están deshabilitados Elasticsearch generará dinámicamente fielddata en memoria y Esto consume energía de la CPU. Esta configuración ha reducido la velocidad de disco i/OS pero mayor esfuerzo en CPU ya en ejecución cerca de sus capacidades máximos, en este caso consultas son más rápidas con valores de documento deshabilitados pero hay menos de ellas.

En las pruebas de DS14 con y sin valores de documento CPU actividad era alta, pero no al 100%. El número de consultas realizadas era ligeramente por encima (aproximadamente 4%) en las pruebas con valores de documento habilitados:

| Clúster | Consulta                      | Número realizadas - valores de documento habilitados | Número que se realizan - doc valores deshabilitado |
|---------|----------------------------|---------------------------------------|----------------------------------------|
| DS4     | Recuento por clasificación            | 1054                                  | 845                                    |
|         | Recuento de tiempo            | 1054                                  | 844                                    |
|         | Visitas por país            | 1053                                  | 842                                    |
|         | Organizaciones de 15 principales       | 1055                                  | 846                                    |
|         | Organizaciones de recuento únicas | 1051                                  | 839                                    |
|         | Recuento de IP única            | 1051                                  | 839                                    |
|         | Recuento total de visitas           | 1051                                  | 839  
|||||                                  |
| DS14    | Recuento por clasificación            | 1772                                  | 1842                                   |
|         | Recuento de tiempo            | 1772                                  | 1839                                   |
|         | Visitas por país            | 1770                                  | 1838                                   |
|         | Organizaciones de 15 principales       | 1773                                  | 1842                                   |
|         | Organizaciones de recuento únicas | 1769 del IETF                                  | 1837                                   |
|         | Recuento de IP única            | 1768                                  | 1837                                   |
|         | Recuento total de visitas           | 1769 del IETF                                  | 1837                                   |

## <a name="performance-results---shard-request-cache"></a>Resultados de rendimiento - caché de solicitud de partes

Para demostrar cómo los datos de índice de almacenamiento en caché en la memoria de cada nodo pueden afectar al rendimiento, la prueba de *consulta y recopilación* se realizó en un DS4 y un clúster de nodo de 6 DS14 con la caché de índice habilitada: vea la sección [uso de la caché de solicitud fragmentar](#using-the-shard-request-cache) para obtener más información. Los resultados se comparan con las generadas por las pruebas anteriores con el mismo índice pero deshabilitado la caché de índice. La siguiente tabla resume los resultados. Tenga en cuenta que los datos se ha reducido para cubrir solo los primeros 90 minutos de la prueba, en este momento la tendencia comparativa era evidente, y continuar la prueba probablemente habría no generados cualquier información adicional:

| Clúster | Operación o consulta            | Tiempo medio de respuesta (ms) - memoria caché de índice deshabilitado | Tiempo medio de respuesta (ms) - memoria caché de índice habilitado | % de la diferencia de tiempo de respuesta |
|---------|----------------------------|---------------------------------------------------|--------------------------------------------------|-------------------------------|
| DS4     | Recopilación                  | 504                                               | 3260                                             | + 547%                         |
|         | Recuento por clasificación            | 218                                               | 273                                              | + 25%                          |
|         | Recuento de tiempo            | 450                                               | 314                                              | -30%                          |
|         | Visitas por país            | 447                                               | 397                                              | -11%                          |
|         | Organizaciones de 15 principales       | 342                                               | 317                                              | -7%                           |
|         | Organizaciones de recuento únicas | 370                                               | 324                                              | -12 %%                         |
|         | Recuento de IP única            | 760                                               | 355                                              | -53%                          |
|         | Recuento total de visitas           | 258                                               | 291                                              | + 12%                          |
||||||
| DS14    | Recopilación                  | 503                                               | 3365                                             | + 569%                         |
|         | Recuento por clasificación            | 234                                               | 262                                              | + 12%                          |
|         | Recuento de tiempo            | 357                                               | 298                                              | -17%                          |
|         | Visitas por país            | 416                                               | 383                                              | -8%                           |
|         | Organizaciones de 15 principales       | 272                                               | 324                                              | -7%                           |
|         | Organizaciones de recuento únicas | 330                                               | 321                                              | -3%                           |
|         | Recuento de IP única            | 674                                               | 352                                              | -48%                          |
|         | Recuento total de visitas           | 227                                               | 292                                              | + 29%                          |

Estos datos muestran dos puntos de interés:

-  Tasas de recopilación de datos parecen ser disminuye considerablemente habilitando el almacenamiento en caché de índice.

-  Almacenamiento en caché de índice no necesariamente mejorar el tiempo de respuesta de todos los tipos de consulta y puede tener efectos negativos en determinadas operaciones de agregado como las realizadas por las consultas de recuento por clasificación y el recuento Total de visitas.
 

Para entender por qué el sistema tiene este comportamiento, debe tener en cuenta el número de consultas que se realizó correctamente en cada caso durante las pruebas. La tabla siguiente resume estos datos:

| Clúster | Operación o consulta            | Recuento de operaciones y consultas - memoria caché de índice deshabilitado | Recuento de operaciones y consultas - memoria caché de índice habilitado |
|---------|----------------------------|-------------------------------------------------|------------------------------------------------|
| DS4     | Recopilación                  | 38611                                           | 13232                                          |
|         | Recuento por clasificación            | 524                                             | 18704                                          |
|         | Recuento de tiempo            | 523                                             | 18703                                          |
|         | Visitas por país            | 522                                             | 18702                                          |
|         | Organizaciones de 15 principales       | 521                                             | 18706                                          |
|         | Organizaciones de recuento únicas | 521                                             | 18700                                          |
|         | Recuento de IP única            | 521                                             | 18699                                          |
|         | Recuento total de visitas           | 521                                             | 18701                                          |
||||                                        |
| DS14    | Recopilación                  | 38769                                           | 12835                                          |
|         | Recuento por clasificación            | 528                                             | 19239                                          |
|         | Recuento de tiempo            | 528                                             | 19239                                          |
|         | Visitas por país            | 528                                             | 19238                                          |
|         | Organizaciones de 15 principales       | 527                                             | 19240                                          |
|         | Organizaciones de recuento únicas | 524                                             | 19234                                          |
|         | Recuento de IP única            | 524                                             | 19234                                          |
|         | Recuento total de visitas           | 527                                             | 19236                                          |

Puede ver que aunque la tasa de recopilación cuando habilitada la caché era aproximadamente 1/3 de cuando se haya deshabilitado el almacenamiento en caché, aumenta el número de consultas realizadas por un factor de 34. Consultas ya no incurre en tanto i/OS del disco y no debe competir por los recursos de disco. Esto se refleja en los gráficos en la figura siguiente que comparan la actividad de i/OS para todos los cuatro casos:

![](./media/guidance-elasticsearch/query-performance13.png)

***Actividad de disco i/OS de la prueba de recopilación y consulta de la caché de índice deshabilitado y habilitado***

La reducción de disco i/OS también significa que la CPU invertido menos tiempo esperando i/OS completar. Esto se resalta en la figura siguiente:

![](./media/guidance-elasticsearch/query-performance14.png)

***CPU tiempo de espera de disco i/OS completar para la prueba de recopilación y consulta de índice caché deshabilitado y habilitado***

La reducción de disco que i/OS había pensado que Elasticsearch se pasan a una proporción mucho mayor de su tiempo atender las consultas de datos mantenidos en memoria. Esto aumenta el uso de CPU, evidente si mira el uso de CPU para todos los casos de cuatro. Los gráficos siguientes muestran cómo el uso de CPU más se ha sufrido está activada la caché:

![](./media/guidance-elasticsearch/query-performance15.png)

***Uso de CPU para integrar y consulta de prueba con índice almacenamiento en caché deshabilitado y habilitado***

El volumen de red i/OS en los dos escenarios para la duración de las pruebas era muy similar. Las pruebas sin almacenamiento en caché mostraron una degradación gradual durante el período de prueba, pero más larga, 24 horas se ejecuta estas pruebas mostrado que esta estadística levelled en aproximadamente 2.75GBps. La imagen siguiente muestra estos datos para los clústeres DS4 (los datos de los clústeres DS14 eran muy similares):

![](./media/guidance-elasticsearch/query-performance16.png)

***Volúmenes de tráfico de red para la prueba de recopilación y consulta de índice caché deshabilitado y habilitado***

Como se describe en la prueba de [escalado](#performance-results-scaling-up) , las restricciones a la red de ancho de banda con máquinas virtuales de Azure no se publican y pueden variar, pero los niveles de actividad de CPU y disco moderados sugiere que el uso de la red puede ser el factor de limitación en este escenario.

Almacenamiento en caché es naturalmente más adecuado para escenarios donde datos no cambian con frecuencia. Para resaltar el impacto de almacenamiento en caché en este escenario, la *consulta solo* se realizaron pruebas con almacenamiento en caché habilitado. A continuación se muestran los resultados (estas pruebas ejecutaban 90 minuto y los índices de prueba contenía 100 millones de documentos):

| Clúster | Consulta                      | Tiempo medio de respuesta (ms) | Número de consultas realizadas |
|---------|----------------------------|----------------------------|-------------------------|
|         |                            | **Caché desactivada**         | **Caché habilitada**       |
| DS4     | Recuento por clasificación            | 4489                       | 210                     |
|         | Recuento de tiempo            | 7292                       | 211                     |
|         | Visitas por país            | 7564                       | 231                     |
|         | Organizaciones de 15 principales       | 5066                       | 211                     |
|         | Organizaciones de recuento únicas | 5231                       | 211                     |
|         | Recuento de IP única            | 9228                       | 218                     |
|         | Recuento total de visitas           | 2180                       | 210                     |
|         |                            |                            |                         |
| DS14    | Recuento por clasificación            | 1927                       | 211                     |
|         | Recuento de tiempo            | 4483                       | 219                     |
|         | Visitas por país            | 4761                       | 236                     |
|         | Organizaciones de 15 principales       | 2117                       | 212                     |
|         | Organizaciones de recuento únicas | 2393                       | 212                     |
|         | Recuento de IP única            | 7159                       | 220                     |
|         | Recuento total de visitas           | 642                        | 211                     |

La varianza en el rendimiento de las pruebas sin almacenamiento en caché es por la diferencia de los recursos disponibles entre el DS4 y máquinas virtuales de DS14. En ambos casos de la prueba en caché recuperar la hora de respuesta promedio significativamente como datos directamente desde la memoria. También es importante recordar los tiempos de respuesta de la caché DS4 y las pruebas de clúster DS14 fueron muy similares a pesar de la diferencia con los resultados sin almacenamiento en caché. También hay muy poca diferencia entre los tiempos de respuesta para cada consulta dentro de cada prueba, todos ellos toman aproximadamente 220ms. Los tipos de disco i/OS y el uso de la CPU para ambos clústeres fueron muy bajos como una vez todos los datos están en memoria poco i/OS o procesamiento es necesario. La velocidad de red i/OS era similar de las pruebas sin almacenar en caché, confirme que ancho de banda de red puede ser un factor de limitación en esta prueba. Los gráficos siguientes presentan esta información para el clúster de DS4. El perfil del clúster DS14 fue muy similar:

![](./media/guidance-elasticsearch/query-performance17.png)

***I/OS de disco, uso de CPU y uso de la red de la prueba solo consulta está activada la caché de índice***

Las cifras de la tabla anterior ilustra que usa la arquitectura de DS14 muestra poco beneficio sobre el uso de la DS4. De hecho, el número de muestras generados por el clúster DS14 era alrededor del 5% por debajo de clúster DS4, pero también puede deberse a las restricciones de red que pueden variar ligeramente a lo largo del tiempo.

## <a name="performance-results---number-of-shards"></a>Resultados de rendimiento - número de shards

La finalidad de esta prueba era determinar si el número de shards creado para un índice tiene cualquier relación con el rendimiento de la consulta de índice.

Ensayos independientes anteriormente mostraron que la configuración de partes de un índice puede afectar a la tasa de recopilación de datos. Las pruebas se realizan para determinar el rendimiento de la consulta seguido una metodología similar, pero se restringe el acceso a un clúster de nodos de 6 que se ejecuta en hardware DS14. Este enfoque ayuda a reducir el número de variables, por lo que deben ser las diferencias en el rendimiento debido al volumen de shards.

La *consulta sólo* prueba se realizó en copias del mismo índice configurado con 7, 13, 23, 37 y 61 shards principales. El índice de contenidos 100 millones de documentos y tenía una única réplica, duplicar el número de shards en el clúster. Cada prueba se ejecutó para 90 minutos. La siguiente tabla resume los resultados. El tiempo medio de respuesta que se muestra el tiempo de respuesta de la transacción de prueba de JMeter que engloba el conjunto completo de consultas realiza cada iteración de la prueba. Vea la nota en la sección [resultados de rendimiento - escalado](#performance-results-scaling-up) para obtener más información:

| Número de shards          | Diseño de fragmentar (shards por nodo, incluidas réplicas) | Número de consultas realizadas | Promedio de tiempo de respuesta (ms) |
|---------------------------|----------------------------------------------------|-----------------------------|------------------------|
| 7 (14 incluidas réplicas) | 3-2-2-2-2-3                                        | 7461                        | 40524                  |
| 13 (26)                   | 5-4-5-4-4-4                                        | 7369                        | 41055                  |
| 23 (46)                   | 7-8-8-7-8-8                                        | 14193                       | 21283                  |
| 37 (74)                   | 13-12-12-13-12-12                                  | 13399                       | 22506                  |
| 61 (122)                  | 20-21-20-20-21-20                                  | 14743                       | 20445                  |

Estos resultados indican que hay una diferencia significativa en el rendimiento entre las partes 13(26) y el clúster de fragmentar 23,(46), casi duplica el rendimiento y tiempos de respuesta a la mitad. Esto suele deberse a la configuración de las máquinas virtuales y las estructuras de Elasticsearch para procesar las solicitudes de búsqueda. Solicitudes de búsqueda en la cola y un subproceso único de búsqueda controla cada solicitud de búsqueda. El número de subprocesos de búsqueda creados por un nodo Elasticsearch es una función del número de procesadores disponibles en el equipo que aloja el nodo. Los resultados se sugieren que con shards solo 4 o 5 en un nodo, recursos de procesamiento no se utilizan. Esto es compatible consultando el uso de CPU mientras se ejecuta esta prueba. La imagen siguiente es una instantánea tomada desde Marvel al realizar la prueba de fragmentar 13(26):

![](./media/guidance-elasticsearch/query-performance18.png)

***Uso de CPU de la prueba solo consulta en el clúster de fragmentar 7(14)***

Compare estas cifras con los de la prueba de fragmentar 23(46):

![](./media/guidance-elasticsearch/query-performance19.png)

***Uso de CPU de la prueba solo consulta en el clúster de fragmentar 23(46)***

En la prueba de fragmentar 23(46), uso de CPU era extremo superior. Cada nodo contiene shards 7 u 8. La arquitectura de DS14 proporciona 16 procesadores y Elasticsearch mejor puede aprovechar este número de núcleos con la shards adicionales. Las cifras de la tabla anterior ilustra que aumenta el número de shards más allá de este punto puede mejorar el rendimiento ligeramente, pero debe desplazar estas cifras frente a la sobrecarga adicional de mantener un gran volumen de shards. Estas pruebas implican que el número óptimo de shards por nodo sea la mitad del número de procesadores principales disponibles en cada nodo. Sin embargo, recuerde que estos resultados se obtuvieron al ejecutar consultas de solo. Si el sistema importa los datos, también debe tener en cuenta cómo sharding puede afectar al rendimiento de las operaciones de recopilación de datos. 

## <a name="summary"></a>Resumen

Elasticsearch ofrece muchas opciones que puede usar para estructurar índices y ajustarlos para admitir operaciones de consulta a gran escala. Este documento resume algunas configuraciones y técnicas que puede usar para el rendimiento de la base de datos con fines de consulta comunes. Sin embargo, debe reconocer que hay un equilibrio entre la optimización de una base de datos para admitir la recuperación rápida en lugar de soporte de recopilación de datos de gran volumen. A veces lo que sirve para consultar puede tener un impacto negativo en las operaciones de inserción y viceversa. En el sistema que se expone a cargas de trabajo mixtas, debe evaluar donde se encuentra el saldo y ajustar los parámetros del sistema en consecuencia.

Además, la aplicación de diferentes configuraciones y técnicas puede variar según el de la estructura de los datos y las limitaciones (o no) del hardware, el sistema se construye en. Muchas de las pruebas que se muestra en este documento muestran cómo la selección de la plataforma de hardware puede afectar al rendimiento y cómo pueden ser algunas estrategias aconsejable en algunos casos, pero perjudicial en otras. El punto importante es comprender las opciones disponibles y, a continuación, realice pruebas rigurosa con sus propios datos para determinar la combinación óptima.

Por último, recuerde que una base de datos de Elasticsearch no es necesariamente un elemento estático. Es probable que crecerá con el tiempo y las estrategias que se utilizan para estructurar los datos que necesite revisar con regularidad. Por ejemplo, puede que sea necesario para escalar, escalar, o volver a indizar los datos con shards adicionales. A medida que aumenta el sistema en tamaño y complejidad, esté preparado para probar continuamente el rendimiento para asegurarse de que aún se cumple cualquier SLA garantizada que los clientes.

## <a name="appendix-the-query-and-aggregation-performance-test"></a>Apéndice: la consulta y agregación de prueba de rendimiento

Este apéndice describe la prueba de rendimiento realizada en el clúster Elasticsearch. Las pruebas se ejecutan mediante JMeter que se ejecuta en un conjunto de máquinas virtuales independiente. Detalles de la configuración del entorno de pruebas se describen en la [creación de un entorno de Elasticsearch en Azure de pruebas de rendimiento](guidance-elasticsearch-creating-performance-testing-environment.md). Para realizar sus propias pruebas, puede crear su propio plan de pruebas JMeter manualmente siguiendo las instrucciones de este apéndice o puede usar las secuencias de comandos de prueba automatizada disponibles por separado. Para obtener más información, vea [ejecutar las pruebas de rendimiento Elasticsearch automatizadas](guidance-elasticsearch-running-automated-performance-tests.md).

La carga de trabajo de consulta de datos realiza el conjunto de consultas que se describe a continuación al realizar una carga a gran escala de documentos al mismo tiempo. El propósito de esta carga de trabajo era simular un entorno de producción donde nuevos constantemente se agrega datos mientras se realizan búsquedas. Las consultas se han estructurado para recuperar solo los datos más recientes de documentos agregados en los últimos 15 minutos.

Cada documento se almacena en un índice único denominado *idx*y tenía escriba *doc*. Puede usar la solicitud HTTP siguiente para crear el índice. La configuración de *number_of_replicas* y *number_of_shards* varía de los valores que se muestra a continuación en muchas de las pruebas. Además, para las pruebas que usan fielddata en lugar de valores de documento, cada propiedad se agrega con el atributo *"doc_values": false*.

**Importante**: el índice se eliminan y vuelve a crear antes de cada ejecución de prueba. 

``` http
PUT /idx
{  
    "settings" : {
        "number_of_replicas": 1,
        "refresh_interval": "30s",
        "number_of_shards": "5",
        "index.translog.durability": "async"    
    },
    "doc": {
        "mappings": {
            "event": {
                "_all": {
                    "enabled": false
                },
                "_timestamp": {
                    "enabled": true,
                    "store": true,
                    "format": "date_time"
                },
                "properties": {
                    "Organization": {
                        "type": "string",
                        "index": "not_analyzed"
                    },
                    "CustomField1": {
                        "type": "string",
                        "index": "not_analyzed"
                    },
                    "CustomField2": {
                        "type": "string",
                        "index": "not_analyzed"
                    },
                    "CustomField3": {
                        "type": "string",
                        "index": "not_analyzed"
                    },
                    "CustomField4": {
                        "type": "string",
                        "index": "not_analyzed"
                    },
                    "CustomField5": {
                        "type": "string",
                        "index": "not_analyzed"
                    },
                    "DateTimeReceivedUtc": {
                        "type": "date",
                        "format": "dateOptionalTime"
                    },
                    "Host": {
                        "type": "string",
                        "index": "not_analyzed"
                    },
                    "HttpMethod": {
                        "type": "string",
                        "index": "not_analyzed"
                    },
                    "HttpReferrer": {
                        "type": "string",
                        "index": "not_analyzed"
                    },
                    "HttpRequest": {
                        "type": "string",
                        "index": "not_analyzed"
                    },
                    "HttpUserAgent": {
                        "type": "string",
                        "index": "not_analyzed"
                    },
                    "HttpVersion": {
                        "type": "string",
                        "index": "not_analyzed"
                    },
                    "OrganizationName": {
                        "type": "string",
                        "index": "not_analyzed"
                    },
                    "SourceIp": {
                        "type": "ip"
                    },
                    "SourceIpAreaCode": {
                        "type": "long"
                    },
                    "SourceIpAsnNr": {
                        "type": "string",
                        "index": "not_analyzed"
                    },
                    "SourceIpBase10": {
                        "type": "long"
                    },
                    "SourceIpCity": {
                        "type": "string",
                        "index": "not_analyzed"
                    },
                    "SourceIpCountryCode": {
                        "type": "string",
                        "index": "not_analyzed"
                    },
                    "SourceIpLatitude": {
                        "type": "double"
                    },
                    "SourceIpLongitude": {
                        "type": "double"
                    },
                    "SourceIpMetroCode": {
                        "type": "long"
                    },
                    "SourceIpPostalCode": {
                        "type": "string",
                        "index": "not_analyzed"
                    },
                    "SourceIpRegion": {
                        "type": "string",
                        "index": "not_analyzed"
                    },
                    "SourceLatLong": {
                        "type": "geo_point",
                        "doc_values": true,
                        "lat_lon": true,
                        "geohash": true
                    },
                    "SourcePort": {
                        "type": "string",
                        "index": "not_analyzed"
                    },
                    "SourcedFrom": {
                        "type": "string",
                        "index": "not_analyzed"
                    },
                    "TargetIp": {
                        "type": "ip"
                    },
                    "TargetPort": {
                        "type": "string",
                        "index": "not_analyzed"
                    },
                    "Rating": {
                        "type": "string",
                        "index": "not_analyzed"
                    },
                    "UseHumanReadableDateTimes": {
                        "type": "boolean"
                    }
                }
            }
        }
    }
}
```

Las consultas siguientes se han realizado por la prueba:
* ¿Cuántos documentos con cada valor de clasificación se han incluido en los últimos 15 minutos?

  ```http
  GET /idx/doc/_search
  {
    "query": {
      "bool": {
        "must": [
          {
            "range": {
              "DateTimeReceivedUtc": {
                "gte": "now-15m",
                "lte": "now"
              }
            }
          }
        ],
        "must_not": [],
        "should": []
      }
    },
    "from": 0,
    "size": 0,
    "aggs": {
      "2": {
        "terms": {
          "field": "Rating",
          "size": 5,
          "order": {
            "_count": "desc"
          }
        }
      }
    }
  }
  ```

* ¿Se han agregado cuántos documentos en cada intervalo de 5 minutos durante los últimos 15 minutos?

  ```http
  GET /idx/doc/_search
  {
    "query": {
      "bool": {
        "must": [
          {
            "range": {
              "DateTimeReceivedUtc": {
                "gte": "now-15m",
                "lte": "now"
              }
            }
          }
        ],
        "must_not": [],
        "should": []
      }
    },
    "from": 0,
    "size": 0,
    "sort": [],
    "aggs": {
      "2": {
        "date_histogram": {
          "field": "DateTimeReceivedUtc",
          "interval": "5m",
          "time_zone": "America/Los_Angeles",
          "min_doc_count": 1,
          "extended_bounds": {
            "min": "now-15m",
            "max": "now"
          }
        }
      }
    }
  }
  ```

* ¿Se han agregado cuántos documentos de cada valor de clasificación de los países en los últimos 15 minutos?

  ```HTTP
  GET /idx/doc/_search
  {
    "query": {
      "filtered": {
        "query": {
          "query_string": {
            "query": "*",
            "analyze_wildcard": true
          }
        },
        "filter": {
          "bool": {
            "must": [
              {
                "query": {
                  "query_string": {
                    "query": "*",
                    "analyze_wildcard": true
                  }
                }
              },
              {
                "range": {
                  "DateTimeReceivedUtc": {
                    "gte": "now-15m",
                    "lte": "now"
                  }
                }
              }
            ],
            "must_not": []
          }
        }
      }
    },
    "size": 0,
    "aggs": {
      "2": {
        "terms": {
          "field": "Rating",
          "size": 5,
          "order": {
            "_count": "desc"
          }
        },
        "aggs": {
          "3": {
            "terms": {
              "field": "SourceIpCountryCode",
              "size": 15,
              "order": {
                "_count": "desc"
              }
            }
          }
        }
      }
    }
  }
  ```

* ¿Qué 15 organizaciones se producen con más frecuencia en documentos agregados en los últimos 15 minutos?

  ```http
  GET /idx/doc/_search
  {
    "query": {
      "filtered": {
        "query": {
          "query_string": {
            "query": "*",
            "analyze_wildcard": true
          }
        },
        "filter": {
          "bool": {
            "must": [
              {
                "query": {
                  "query_string": {
                    "query": "*",
                    "analyze_wildcard": true
                  }
                }
              },
              {
                "range": {
                  "DateTimeReceivedUtc": {
                    "gte": "now-15m",
                    "lte": "now"
                  }
                }
              }
            ],
            "must_not": []
          }
        }
      }
    },
    "size": 0,
    "aggs": {
      "2": {
        "terms": {
          "field": "Organization",
          "size": 15,
          "order": {
            "_count": "desc"
          }
        }
      }
    }
  }
  ```

* ¿Cuántos diferentes organizaciones se producen en documentos agregados en los últimos 15 minutos?

  ```http
  GET /idx/doc/_search
  {
    "query": {
      "filtered": {
        "query": {
          "query_string": {
            "query": "*",
            "analyze_wildcard": true
          }
        },
        "filter": {
          "bool": {
            "must": [
              {
                "query": {
                  "query_string": {
                    "query": "*",
                    "analyze_wildcard": true
                  }
                }
              },
              {
                "range": {
                  "DateTimeReceivedUtc": {
                    "gte": "now-15m",
                    "lte": "now"
                  }
                }
              }
            ],
            "must_not": []
          }
        }
      }
    },
    "size": 0,
    "aggs": {
      "2": {
        "cardinality": {
          "field": "Organization"
        }
      }
    }
  }
  ```

* ¿Se han agregado cuántos documentos en los últimos 15 minutos?

  ```http
  GET /idx/doc/_search
  {
    "query": {
      "filtered": {
        "query": {
          "query_string": {
            "query": "*",
            "analyze_wildcard": true
          }
        },
        "filter": {
          "bool": {
            "must": [
              {
                "query": {
                  "query_string": {
                    "analyze_wildcard": true,
                    "query": "*"
                  }
                }
              },
              {
                "range": {
                  "DateTimeReceivedUtc": {
                    "gte": "now-15m",
                    "lte": "now"
                  }
                }
              }
            ],
            "must_not": []
          }
        }
      }
    },
    "size": 0,
    "aggs": {}
  }
  ```

* ¿Cuántos valores SourceIp diferentes se producen en documentos agregados en los últimos 15 minutos?

  ```http
  GET /idx/doc/_search
  {
    "query": {
      "filtered": {
        "query": {
          "query_string": {
            "query": "*",
            "analyze_wildcard": true
          }
        },
        "filter": {
          "bool": {
            "must": [
              {
                "query": {
                  "query_string": {
                    "query": "*",
                    "analyze_wildcard": true
                  }
                }
              },
              {
                "range": {
                  "DateTimeReceivedUtc": {
                    "gte": "now-15m",
                    "lte": "now"
                  }
                }
              }
            ],
            "must_not": []
          }
        }
      }
    },
    "size": 0,
    "aggs": {
      "2": {
        "cardinality": {
          "field": "SourceIp"
        }
      }
    }
  }
  ```
