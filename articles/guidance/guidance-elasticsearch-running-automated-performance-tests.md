
<properties
   pageTitle="Ejecutar las pruebas automatizadas de rendimiento Elasticsearch | Microsoft Azure"
   description="Descripción de cómo puede ejecutar las pruebas de rendimiento en su entorno."
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
   
# <a name="running-the-automated-elasticsearch-performance-tests"></a>Ejecutar las pruebas de rendimiento Elasticsearch automatizadas

[AZURE.INCLUDE [pnp-header](../../includes/guidance-pnp-header-include.md)]

En este artículo forma [parte de una serie](guidance-elasticsearch.md). 

Los documentos de [ajuste del rendimiento de recopilación de datos para Elasticsearch en Azure] y [agregación de datos de optimización y rendimiento de la consulta para Elasticsearch en Azure] describen una serie de pruebas de rendimiento que se ejecute en un clúster de Elasticsearch de ejemplo.

Estas pruebas se secuencias de comandos para permitir que se ejecuten de manera automática. Este documento describe cómo puede repetir las pruebas en su entorno.

## <a name="prerequisites"></a>Requisitos previos

Las pruebas automatizadas requieren los elementos siguientes:

-  Un clúster de Elasticsearch.

- Configuración de entorno de JMeter como se describe en el documento [creando un entorno de prueba de rendimiento para Elasticsearch en Azure].

- [Python 3.5.1](https://www.python.org/downloads/release/python-351/) instalado en el patrón de JMeter VM.


## <a name="how-the-tests-work"></a>¿Cómo funcionan las pruebas
Las pruebas se ejecutan mediante JMeter. Servidor maestro JMeter carga un plan de pruebas y los pasa a un conjunto de servidores subordinados de JMeter que ejecutan las pruebas. El servidor maestro JMeter coordenadas JMeter los servidores subordinados y acumula los resultados.

Están disponibles los siguientes planes de prueba:

* [elasticsearchautotestplan3nodes.jmx](https://github.com/mspnp/azure-guidance/blob/master/ingestion-and-query-tests/templates/elasticsearchautotestplan3nodes.jmx). Ejecuta la prueba de recopilación sobre un clúster de nodo 3.

* [elasticsearchautotestplan6nodes.jmx](https://github.com/mspnp/azure-guidance/blob/master/ingestion-and-query-tests/templates/elasticsearchautotestplan6nodes.jmx). Ejecuta la prueba de recopilación sobre un clúster de nodo de 6.

* [elasticsearchautotestplan6qnodes.jmx](https://github.com/mspnp/azure-guidance/blob/master/ingestion-and-query-tests/templates/elasticsearchautotestplan6qnodes.jmx). Ejecuta la prueba de recopilación y consulta sobre un clúster de nodo de 6.

* [elasticsearchautotestplan6nodesqueryonly.jmx](https://github.com/mspnp/azure-guidance/blob/master/ingestion-and-query-tests/templates/elasticsearchautotestplan6nodesqueryonly.jmx). Se ejecuta la prueba de consulta en un clúster de nodo de 6.


Puede utilizar estos planes de pruebas como base para sus propios escenarios si necesita más o menos nodos.

Los planes de pruebas utilizan una muestra de solicitud JUnit para generar y cargar los datos de prueba. El plan de pruebas JMeter crea y ejecuta este muestrario y supervisa cada uno de los nodos de Elasticsearch datos de rendimiento.  

## <a name="building-and-deploying-the-junit-jar-and-dependencies"></a>Crear e implementar la JUnit JAR y dependencias
Antes de ejecutar las pruebas de rendimiento, que debe descargar, compilar e implementar las pruebas JUnit ubicadas en la carpeta de rendimiento/junitcode. Estas pruebas se hace referencia en el plan de pruebas JMeter. Para obtener más información, vea el procedimiento "Importación de un proyecto existente JUnit en Eclipse" en el documento de [implementación de una muestra de JMeter JUnit para probar el rendimiento de Elasticsearch].

Hay dos versiones de las pruebas de JUnit: 

- [Elasticsearch1.73](https://github.com/mspnp/azure-guidance/tree/master/ingestion-and-query-tests/junitcode/elasticsearch1.73). Use este código para realizar pruebas de recopilación. Estas pruebas use 1,73 Elasticsearch.

- [Elasticsearch2](https://github.com/mspnp/azure-guidance/tree/master/ingestion-and-query-tests/junitcode/elasticsearch2). Use este código para realizar pruebas de consulta. Estas pruebas use Elasticsearch 2.1 y versiones posterior.

Copie el archivo de archive (JAR) Java adecuado junto con el resto de las dependencias en los equipos de JMeter. El proceso se describe en la [implementación de una muestra de JMeter JUnit para probar el rendimiento de Elasticsearch][]. 

> **Importante** Después de implementar una prueba JUnit, use JMeter para cargar y configurar los planes de pruebas que hacen referencia a esta prueba JUnit y asegúrese de que el grupo de subproceso BulkInsertLarge hace referencia el archivo JAR correcto, el nombre de la clase JUnit y probar el método de:
> 
> ![](./media/guidance-elasticsearch/performance-tests-image1.png)
> 
> Guardar los planes de prueba actualizados antes de ejecutar las pruebas.

## <a name="creating-the-test-indexes"></a>Crear los índices de prueba
Cada prueba realiza recopilación o consultas en un índice único especifican cuando se ejecuta la prueba. Debe crear el índice usando los esquemas descritos en los anexos a los documentos de [ajuste del rendimiento de recopilación de datos para Elasticsearch en Azure] y [agregación de datos de optimización y rendimiento de la consulta para Elasticsearch en Azure] y configurarlos según su escenario de prueba (valores de documento habilitado o deshabilitado, múltiples réplicas y así sucesivamente). Tenga en cuenta que los planes de prueba se supone que el índice contiene un solo tipo denominado *ctip*.

## <a name="configuring-the-test-script-parameters"></a>Configuración de los parámetros de script de prueba
Copie los siguientes archivos de parámetro de secuencia de comandos de prueba en el equipo del servidor JMeter:

* [run.properties](https://github.com/mspnp/azure-guidance/blob/master/ingestion-and-query-tests/run.properties). Este archivo especifica el número de subprocesos de prueba de JMeter usar, la duración de la prueba (en segundos), la dirección IP de un nodo (o un equilibrador de carga en el clúster de Elasticsearch) y el nombre del clúster:

  ```ini
  nthreads=3
  duration=300
  elasticip=<IP Address or DNS Name Here>
  clustername=<Cluster Name Here>
  ```
  
  Editar el archivo y especifique los valores apropiados para su prueba y clúster.

* [configuración de consulta de win.ini](https://github.com/mspnp/azure-guidance/blob/master/ingestion-and-query-tests/query-config-win.ini) y [nix.ini de configuración de consulta](https://github.com/mspnp/azure-guidance/blob/master/ingestion-and-query-tests/query-config-nix.ini). Estos dos archivos contienen la misma información; el archivo *ganan* es el formato de rutas y nombres de archivo de Windows y, a continuación, el archivo *nix* es el formato de rutas y nombres de archivo de Linux:

  ```ini
  [DEFAULT]
  debug=true #if true shows console logs.

  [RUN]
  pathreports=C:\Users\administrator1\jmeter\test-results\ #path where tests results are saved.
  jmx=C:\Users\administrator1\testplan.jmx #path to the JMeter test plan.
  machines=10.0.0.1,10.0.0.2,10.0.0.3 #IPs of the Elasticsearch data nodes separated by commas.
  reports=aggr,err,tps,waitio,cpu,network,disk,response,view #Name of the reports separated by commas.
  tests=idx1,idx2 #Elasticsearch index(es) name(s) to test, comma delimited if more than one.
  properties=run.properties #Name of the properties file.
  ```

  Editar este archivo para especificar las ubicaciones de los resultados de la prueba, el nombre del plan de prueba JMeter ejecutar, las direcciones IP de los nodos de datos Elasticsearch va a recopilar estadísticas de rendimiento de, los informes que contienen los datos de rendimiento que se generan, y el nombre (o delimitado por comas de nombres) de los índices en prueba, si más de un , pruebas se ejecutarán una después de otra. Si el archivo run.properties se encuentra en una carpeta diferente o directorio, especifique la ruta de acceso completa a este archivo.

## <a name="running-the-tests"></a>Ejecutar las pruebas

* Copie el archivo [test.py de la consulta](https://github.com/mspnp/azure-guidance/blob/master/ingestion-and-query-tests/query-test.py) en el equipo de servidor JMeter, en la misma carpeta que la run.properties y archivos de configuración de consulta de win.ini (nix.ini de configuración de consulta).

* Asegúrese de que jmeter.bat (Windows) o jmeter.sh (Linux) se encuentran en la ruta de acceso ejecutable en su entorno.

* Ejecute la secuencia de comandos de consulta test.py desde la línea de comandos para realizar las pruebas:

  ```cmd
  py query-test.py
  ```

* Cuando haya finalizado la prueba, los resultados se almacenan como archivos (CSV) especificados en el archivo win.ini de configuración de consulta (nix.ini de configuración de consulta) los valores del conjunto de valores separados por comas. Puede usar Excel para analizar y representar gráficamente estos datos.


[Ajuste del rendimiento de recopilación de datos para Elasticsearch en Azure]: guidance-elasticsearch-tuning-data-ingestion-performance.md
[Ajustar el rendimiento de consulta para Elasticsearch en Azure y agregación de datos]: guidance-elasticsearch-tuning-data-aggregation-and-query-performance.md
[Crear un entorno de prueba para Elasticsearch en Azure de rendimiento]: guidance-elasticsearch-creating-performance-testing-environment.md
[Implementar un muestrario JMeter JUnit para realizar pruebas de rendimiento Elasticsearch]: guidance-elasticsearch-deploying-jmeter-junit-sampler.md
