<properties
   pageTitle="Implementar un plan de pruebas de JMeter para Elasticsearch | Microsoft Azure"
   description="Cómo ejecutar rendimiento comprueba Elasticsearch con JMeter."
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
   ms.author="masashin" />
   
# <a name="implementing-a-jmeter-test-plan-for-elasticsearch"></a>Implementar un plan de pruebas de JMeter para Elasticsearch

[AZURE.INCLUDE [pnp-header](../../includes/guidance-pnp-header-include.md)]

En este artículo forma [parte de una serie](guidance-elasticsearch.md). 

Las pruebas de rendimiento realizadas con Elasticsearch se implementan mediante planes de pruebas JMeter junto con el código de Java incorporada como una prueba de JUnit para realizar tareas como cargar datos en el clúster. En [ajuste del rendimiento de recopilación de datos para Elasticsearch en Azure][]y [agregación de datos de optimización y rendimiento de la consulta para Elasticsearch en Azure][], se describen los planes de prueba y el código de JUnit.

El propósito de este documento es resumir la experiencia de clave obtenida de construcción y ejecuta estos planes de prueba. La página de [Prácticas recomendadas de JMeter](http://jmeter.apache.org/usermanual/best-practices.html) en el sitio Web de Apache JMeter contiene más generalizado consejos sobre el uso de un modo eficaz JMeter.

## <a name="implementing-a-jmeter-test-plan"></a>Implementar un plan de pruebas JMeter

En la lista siguiente se resume los elementos que debe tener en cuenta al crear un plan de pruebas de JMeter:

- Crear un grupo de subproceso independiente para cada prueba que desee realizar. Una prueba puede contener varios pasos, incluidos los controladores de lógica, temporizadores, anteriores y procesadores posteriores a la, muestras y escucha.

- Evite crear demasiados subprocesos en un grupo de subproceso. Un número excesivo de subprocesos provocará JMeter errores con "Memoria insuficiente" excepciones. Es mejor agregar más servidores subordinados de JMeter los que se ejecute un número menor de subprocesos de intentar ejecutar un gran número de subprocesos en un solo servidor JMeter.

![](./media/guidance-elasticsearch/jmeter-testing1.png)

- Para evaluar el rendimiento del clúster, incorporar el complemento del [Selector de métricas de rendimiento](http://jmeter-plugins.org/wiki/PerfMon/) en el plan de pruebas. Se trata de un agente de escucha de JMeter está disponible como uno de los complementos de JMeter estándares. Guardar los datos de rendimiento en un conjunto de archivos en formato de valores separados por comas (CSV) y proceso de una vez completada la prueba. Esto es más eficaz e impone menos presión en JMeter que intenta procesar los datos según se captura. 

![](./media/guidance-elasticsearch/jmeter-testing2.png)

Puede usar una herramienta como Excel para importar los datos y genera un intervalo de gráficos con fines analíticos.

Considere la posibilidad de capturar toda la información siguiente:

- Uso de CPU de todos los nodos en el clúster Elasticsearch.

- El número de bytes leídos por segundo de disco para todos los nodos.

- Si es posible, el porcentaje de CPU tiempo en espera para i/OS que se realicen en cada nodo. Esto no siempre es posible para máquinas virtuales de Windows, pero puede crear una métrica personalizada (una métrica ejecutivo) que se ejecute el siguiente comando shell invocar *vmstat* en un nodo para Linux:

```Shell
sh:-c:vmstat 1 5 | awk 'BEGIN { line=0;total=0;}{line=line+1;if(line&gt;1){total=total+\$16;}}END{print total/4}'
```

16 de campo en el resultado de *vmstat* contiene el tiempo de CPU dedicado esperando i/OS. Para obtener más información sobre cómo funciona esta declaración, consulte el [comando vmstat](http://linuxcommand.org/man_pages/vmstat8.html).

- El número de bytes enviados y recibidos a través de la red a cada nodo.

- Usar escucha de informe de agregado independiente para grabar el rendimiento y la frecuencia de las operaciones correctas y con errores. Capturar datos de éxito y de error en archivos de diferentes.

![](./media/guidance-elasticsearch/jmeter-testing3.png)

- Mantener cada caso de prueba JMeter tan sencillo como sea posible para que pueda relacionar directamente con acciones específicas de prueba de rendimiento. Casos de prueba que requieren una lógica compleja, considere la posibilidad de encapsular esta lógica en una prueba de JUnit y use el Reproductor de solicitud de JUnit JMeter para ejecutar la prueba.

- Usar el Reproductor de solicitud HTTP para realizar operaciones de HTTP, como GET, POST, SUPERPONER o eliminar. Por ejemplo, puede ejecutar búsquedas de Elasticsearch mediante una consulta de entrada y proporciona los detalles de la consulta en el cuadro *Datos de cuerpo* :

![](./media/guidance-elasticsearch/jmeter-testing4.png)

- Para facilitar la capacidad de repetición y volver a usarlos, agregar parámetros JMeter prueba planes de prueba. A continuación, puede usar secuencias de comandos para automatizar la ejecución de planes de prueba.

## <a name="implementing-a-junit-test"></a>Implementar una prueba JUnit

Puede incorporar código complejo en un plan de pruebas JMeter mediante la creación de una o varias pruebas de JUnit. Puede escribir una prueba de JUnit utilizando un entorno de desarrollo integrado (IDE) Java como Eclipse. [Implementar un muestrario JMeter JUnit para probar el rendimiento de Elasticsearch][] proporciona información sobre cómo configurar un entorno de desarrollo apropiadas.

En la lista siguiente se resume algunas prácticas recomendadas que debe seguir al escribir el código para una prueba de JUnit:

- Usar el constructor de clase de prueba para pasar parámetros de inicialización a la prueba. JMeter puede utilizar un constructor que toma un único argumento de cadena. En el constructor, analizar este argumento en sus elementos individuales, como se muestra en el siguiente ejemplo:

```Java
private String hostName = "";
private String indexName = "";
private String typeName = "";
private int port = 0;
private String clusterName = "";
private int itemsPerBatch = 0;

/\* JUnit test class constructor \*/
public ElasticsearchLoadTest2(String params) {
    /* params is a string containing a set of comma separated values for:
        hostName
        indexName
        typeName
        port
        clustername
        itemsPerBatch
    */

    /* Parse the parameter string into an array of string items */
    String delims = "\[ \]\*,\[ \]\*"; // comma surrounded by zero or more spaces
    String\[\] items = params.split(delims);

    /* Note: Parameter validation code omitted */

    /* Use the parameters to populate variables used by the test */
    hostName = items[0];
    indexName = items[1];
    typeName = items[2];
    port = Integer.parseInt(items[3]);
    clusterName = items[4];
    itemsPerBatch = Integer.parseInt(items[5]);

    if(itemsPerBatch == 0)
        itemsPerBatch = 1000;
}
```

- Evitar operaciones de i/OS u otras operaciones que lleva mucho tiempo en el constructor o clase de prueba de instalación, ya que ejecute cada vez que se ejecuta la prueba JUnit. (La misma prueba JUnit puede ejecutar miles de horas para cada prueba de rendimiento que se ejecutan desde JMeter).

- Puede usar la configuración única para inicialización caro caso de prueba.

- Si la prueba requiere una gran cantidad de parámetros de entrada, almacenar la información de configuración de prueba en un archivo de configuración independiente y pasar a la ubicación del archivo en el constructor.

- Evite codificar rutas de acceso del archivo en el código de prueba de carga. Esto puede provocar errores debido a las diferencias entre los sistemas operativos como Windows y Linux.

- Utilizar aserciones para indicar errores en JUnit probar métodos para que puedan realizar un seguimiento de ellos con JMeter y utilizarlas como una métrica empresarial. Si es posible, pasar información sobre la causa del error, como se muestra en negrita en el ejemplo siguiente:

```Java
@Test
public void bulkInsertTest() throws IOException {
    ...
    BulkResponse bulkResponse = bulkRequest.execute().actionGet();
    assertFalse(
        bulkResponse.buildFailureMessage(), bulkResponse.hasFailures());
        ...
}
```


[Running Elasticsearch on Azure]: guidance-elasticsearch-running-on-azure.md
[Ajuste del rendimiento de recopilación de datos para Elasticsearch en Azure]: guidance-elasticsearch-tuning-data-ingestion-performance.md
[Implementar un muestrario JMeter JUnit para realizar pruebas de rendimiento Elasticsearch]: guidance-elasticsearch-deploying-jmeter-junit-sampler.md
[Ajustar el rendimiento de consulta para Elasticsearch en Azure y agregación de datos]: guidance-elasticsearch-tuning-data-aggregation-and-query-performance.md
