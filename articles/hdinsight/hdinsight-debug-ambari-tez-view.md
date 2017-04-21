<properties
pageTitle="Usar la vista de Tez Ambari con HDInsight | Azure"
description="Obtenga información sobre cómo usar la vista de Ambari Tez depurar Tez trabajos en HDInsight."
services="hdinsight"
documentationCenter=""
authors="Blackmist"
manager="jhubbard"
editor="cgronlun"/>

<tags
ms.service="hdinsight"
ms.devlang="na"
ms.topic="article"
ms.tgt_pltfrm="na"
ms.workload="big-data"
ms.date="10/04/2016"
ms.author="larryfr"/>

# <a name="use-ambari-views-to-debug-tez-jobs-on-hdinsight"></a>Usar vistas de Ambari depurar Tez trabajos en HDInsight

La interfaz de usuario de Web Ambari para HDInsight contiene una vista de Tez que puede usarse para comprender y depurar trabajos que utilizan Tez como el motor de ejecución. La vista Tez le permite visualizar el trabajo como un gráfico de elementos conectados, explorar en profundidad cada elemento y recuperar las estadísticas y la información de registro.

> [AZURE.NOTE] La información de este documento es específica para clústeres HDInsight basados en Linux. Para obtener información sobre la depuración de trabajos de Tez con HDInsight de Windows, vea [usar la interfaz de usuario de Tez para depurar Tez trabajos en HDInsight de Windows](hdinsight-debug-tez-ui.md).

## <a name="prerequisites"></a>Requisitos previos

* Un clúster de HDInsight basados en Linux. Para conocer los pasos sobre cómo crear un nuevo clúster, vea [Introducción al uso de HDInsight de Linux](hdinsight-hadoop-linux-tutorial-get-started.md).

* Un explorador web moderna compatible con HTML5.

## <a name="understanding-tez"></a>Descripción Tez

Tez es un marco extensible para el procesamiento de datos en Hadoop que proporciona mayor velocidad de procesamiento de MapReduce tradicional. Para clústeres basados en Linux HDInsight, es el motor predeterminado para la sección.

Cuando el trabajo se envía a Tez, crea un dirigen acíclicos Graph (DAG incluidos) que describe el orden de ejecución de las acciones necesarios para el trabajo. Acciones individuales se denominan vértices y ejecutan una parte del trabajo total. La ejecución del trabajo descrito por un vértice se denomina una tarea y puede distribuirse entre varios nodos en el clúster.

### <a name="understanding-the-tez-view"></a>Descripción de la vista de Tez

La vista de Tez proporciona información sobre los procesos que se están ejecutando, o que tienen ejecutó previamente con Tez. Permite ver la DAG incluidos generado por Tez, ¿cómo se distribuye entre clústeres, contadores como memoria utilizada por tareas y vértices e información de error. Puede ofrecer información útil en las situaciones siguientes:

* Reduce las tareas y procesos, ver el progreso del mapa de la supervisión de ejecución larga.

* Analizar datos históricos para los procesos de éxito o error obtener información sobre cómo puede mejorar el procesamiento o la causa del error.

## <a name="generate-a-dag"></a>Generar un DAG incluidos

La vista Tez sólo contendrá datos si una tarea que usa el motor de Tez se está ejecutando o se ha ejecutado en el pasado. Consultas sencillas de subárbol normalmente se pueden resolver sin usar Tez, sin embargo más complejas consultas que hagan filtrado, la agrupación, orden, combinaciones, etc. normalmente requieren Tez.

Realice los pasos siguientes para ejecutar una consulta de sección que se ejecutará mediante Tez.

1. En un explorador web, vaya a https://CLUSTERNAME.azurehdinsight.net, donde __NOMBREDECLÚSTER__ es el nombre de su clúster HDInsight.

2. En el menú en la parte superior de la página, seleccione el icono de __vistas__ . Esto es similar a una serie de cuadrados. En el menú desplegable que aparece, seleccione la __vista de sección__. 

    ![Seleccionar la vista de sección](./media/hdinsight-debug-ambari-tez-view/selecthive.png)

3. Cuando la sección Ver cargas, pegue lo siguiente en el Editor de consultas y, a continuación, haga clic en __Ejecutar__.

        select market, state, country from hivesampletable where deviceplatform='Android' group by market, country, state;
    
    Una vez completada la tarea, debería ver el resultado mostrado en la sección de __Resultados de proceso de la consulta__ . Los resultados deben ser similares al siguiente
    
        market  state       country
        en-GB   Hessen      Germany
        en-GB   Kingston    Jamaica
        
4. Seleccione la ficha __registro__ . Se muestra información similar a la siguiente:
    
        INFO : Session is already open
        INFO :

        INFO : Status: Running (Executing on YARN cluster with App id application_1454546500517_0063)

    Guardar el valor del __identificador de aplicación__ , que se utilizará en la siguiente sección.

## <a name="use-the-tez-view"></a>Usar la vista de Tez

1. En el menú en la parte superior de la página, seleccione el icono de __vistas__ . En el menú desplegable que aparece, seleccione __vista Tez__.

    ![Seleccionar la vista de Tez](./media/hdinsight-debug-ambari-tez-view/selecttez.png)

2. Cuando se cargue la vista Tez, verá una lista de DAGs que se están ejecutando actualmente o que han sido ha encontrado en el clúster. La vista predeterminada incluye la Dag Name, Id, remitente, estado, hora de inicio, hora de finalización, duración, identificador de la aplicación y cola. Pueden agregar más columnas utilizando el icono de engranaje de la derecha de la página.

    ![Todos los DAGS](./media/hdinsight-debug-ambari-tez-view/alldags.png)

3. Si tiene sólo una entrada, será la consulta que ha encontrado en la sección anterior. Si tiene múltiples entradas, puede buscar, escriba el identificador de aplicación en el campo __Identificador de la aplicación__ y, a continuación, presione ENTRAR.

4. Seleccione el __Nombre de DAG incluidos__. Esta opción mostrará información sobre la DAG incluidos, así como la opción para descargar un archivo zip de archivos JSON que contienen información sobre la DAG incluidos.

    ![Detalles de DAG incluidos](./media/hdinsight-debug-ambari-tez-view/dagdetails.png)

5. Encima de los __Detalles de DAG incluidos__ hay varios vínculos que se pueden usar para mostrar información sobre la DAG incluidos.

    * __DAG incluidos contadores__ muestra información de contadores para este DAG incluidos.
    
    * __Vista gráfica__ muestra una representación gráfica de este DAG incluidos.
    
    * __Todos los vértices__ se muestra una lista de los vértices en este DAG incluidos.
    
    * __Todas las tareas__ muestra una lista de las tareas para todos los vértices en este DAG incluidos.
    
    * __Todos los TaskAttempts__ muestra información sobre los intentos para ejecutar las tareas para este DAG incluidos.
    
    > [AZURE.NOTE] Si se desplaza la visualización de la columna de vértices, tareas y TaskAttempts, observe que hay vínculos para ver __contadores__ y __Ver o descargar registros__ para cada fila.

    Si se ha producido un error con el trabajo, los detalles de DAG incluidos mostrará un estado de error, junto con vínculos a información sobre la tarea con error. Información de diagnóstico se mostrarán debajo de los detalles de DAG incluidos.
    
    ![Una pantalla de detalles de DAG incluidos que detalla un error](./media/hdinsight-debug-ambari-tez-view/faileddag.png)

7. Seleccione la __vista gráfica__. Muestra una representación gráfica de la DAG incluidos. Puede colocar el mouse sobre cada vértice en la vista para mostrar información acerca de él.

    ![Vista gráfica](./media/hdinsight-debug-ambari-tez-view/dagdiagram.png)

8. Al hacer clic en un vértice, cargará el __Vértice detalles__ para ese elemento. Haga clic en el vértice __1 de mapa__ para mostrar los detalles de este artículo.

    ![Detalles de vértices](./media/hdinsight-debug-ambari-tez-view/vertexdetails.png)

9. Observe que ahora tienen vínculos en la parte superior de la página que están relacionados con vértices y las tareas.

    > [AZURE.NOTE] También puede llegar a esta página volviendo a __DAG incluidos detalles__, seleccionando __Vértice detalles__y seleccionando el vértice __1 de mapa__ .

    * __Vértice contadores__ muestra información de contador para este vértice.
    
    * __Tareas__ muestra las tareas para este vértice.
    
    * __Tarea intenta__ muestra información acerca de los intentos para ejecutar las tareas para este vértice.
    
    * __Orígenes y receptores de__ orígenes de datos de muestra y receptores para este vértice.

    > [AZURE.NOTE] Como con el menú anterior, puede desplazarse por la presentación de la columna para las tareas, intentos de tarea y orígenes & Sinks__ mostrar los vínculos a más información para cada elemento.

10. Seleccione __las tareas__y, a continuación, seleccione el elemento denominado __00_000000__. Esta opción mostrará __Detalles de la tarea__ para esta tarea. Desde esta pantalla, puede ver __Tareas contadores__ y __Los intentos de tarea__.

    ![Detalles de la tarea](./media/hdinsight-debug-ambari-tez-view/taskdetails.png)

## <a name="next-steps"></a>Pasos siguientes

Ahora que ha aprendido a usar la vista de Tez, más información sobre el [Uso de subárbol en HDInsight](hdinsight-use-hive.md).

Para obtener más información técnica sobre Tez, consulte la [página de Tez en Hortonworks](http://hortonworks.com/hadoop/tez/).

Para obtener más información sobre el uso de Ambari con HDInsight, consulte [clústeres de HDInsight administrar mediante la interfaz de usuario de Web Ambari](hdinsight-hadoop-manage-ambari.md)
