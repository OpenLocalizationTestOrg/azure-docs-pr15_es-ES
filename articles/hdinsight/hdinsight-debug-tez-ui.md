<properties
pageTitle="Usar la interfaz de usuario de Tez con HDInsight de Windows | Azure"
description="Obtenga información sobre cómo usar la UI Tez depurar Tez trabajos en HDInsight de HDInsight basado en Windows."
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

# <a name="use-the-tez-ui-to-debug-tez-jobs-on-windows-based-hdinsight"></a>Usar la UI Tez depurar Tez trabajos en HDInsight de Windows

La UI Tez es una página web que puede usarse para comprender y depurar trabajos que utilizan Tez como el motor de ejecución en clústeres HDInsight basado en Windows. La UI Tez le permite visualizar el trabajo como un gráfico de elementos conectados, explorar en profundidad cada elemento y recuperar las estadísticas y la información de registro.

> [AZURE.NOTE] La información de este documento es específica para clústeres HDInsight basado en Windows. Para obtener información sobre cómo ver y depurar Tez en HDInsight de Linux, vea [Usar Ambari vistas depurar Tez trabajos en HDInsight](hdinsight-debug-ambari-tez-view.md).

## <a name="prerequisites"></a>Requisitos previos

* Un clúster basado en Windows HDInsight. Para conocer los pasos sobre cómo crear un nuevo clúster, vea [Introducción al uso de HDInsight de Windows](hdinsight-hadoop-tutorial-get-started-windows.md).

    > [AZURE.IMPORTANT] La UI Tez solo está disponible en clústeres basados en Windows HDInsight creados después del 8 de febrero de 2016.

* Un cliente de escritorio remoto basado en Windows.

## <a name="understanding-tez"></a>Descripción Tez

Tez es un marco extensible para el procesamiento de datos en Hadoop que proporciona mayor velocidad de procesamiento de MapReduce tradicional. Para clústeres HDInsight basado en Windows, es un motor opcional que se puede activar para subárbol mediante el comando siguiente como parte de la consulta de la sección:

    set hive.execution.engine=tez;

Cuando el trabajo se envía a Tez, crea un dirigen acíclicos Graph (DAG incluidos) que describe el orden de ejecución de las acciones necesarios para el trabajo. Acciones individuales se denominan vértices y ejecutan una parte del trabajo total. La ejecución del trabajo descrito por un vértice se denomina una tarea y puede distribuirse entre varios nodos en el clúster.

### <a name="understanding-the-tez-ui"></a>Descripción de la interfaz de usuario de Tez

La UI Tez es que una página web proporciona información sobre los procesos que se están ejecutando, o que tienen ejecutó previamente con Tez. Permite ver la DAG incluidos generado por Tez, ¿cómo se distribuye entre clústeres, contadores como memoria utilizada por tareas y vértices e información de error. Puede ofrecer información útil en las situaciones siguientes:

* Reduce las tareas y procesos, ver el progreso del mapa de la supervisión de ejecución larga.

* Analizar datos históricos para los procesos de éxito o error obtener información sobre cómo puede mejorar el procesamiento o la causa del error.

## <a name="generate-a-dag"></a>Generar un DAG incluidos

La UI Tez sólo contendrá datos si una tarea que usa el motor de Tez se está ejecutando o se ha ejecutado en el pasado. Consultas sencillas de subárbol normalmente se pueden resolver sin usar Tez, sin embargo más complejas consultas que hagan filtrado, la agrupación, orden, combinaciones, etc. normalmente requieren Tez.

Realice los pasos siguientes para ejecutar una consulta de sección que se ejecutará mediante Tez.

1. En un explorador web, vaya a https://CLUSTERNAME.azurehdinsight.net, donde __NOMBREDECLÚSTER__ es el nombre de su clúster HDInsight.

2. En el menú en la parte superior de la página, seleccione el __Editor de sección__. Esta opción mostrará una página con la siguiente consulta de ejemplo.

        Select * from hivesampletable

    Borrar la consulta de ejemplo y reemplácelo con el siguiente.

        set hive.execution.engine=tez;
        select market, state, country from hivesampletable where deviceplatform='Android' group by market, country, state;

3. Seleccione el botón __Enviar__ . La sección de la __Sesión de trabajo__ en la parte inferior de la página mostrará el estado de la consulta. Una vez que el estado cambia a __completada__, seleccione el vínculo __Ver detalles__ para ver los resultados. La __Salida de trabajo__ debe ser similar al siguiente:
        
        en-GB   Hessen      Germany
        en-GB   Kingston    Jamaica
        en-GB   Nairobi Area    Kenya

## <a name="use-the-tez-ui"></a>Usar la interfaz de usuario de Tez

> [AZURE.NOTE] La UI Tez solo está disponible desde el escritorio los nodos de clúster central, lo que debe usar Escritorio remoto para conectarse a los nodos de cabeza.

1. Desde el [portal de Azure](https://portal.azure.com), seleccione el clúster HDInsight. En la parte superior del módulo HDInsight, seleccione el icono de __Escritorio remoto__ . Esta opción mostrará el módulo de escritorio remoto

    ![Icono de escritorio remoto](./media/hdinsight-debug-tez-ui/remotedesktopicon.png)

2. En el módulo de escritorio remoto, seleccione __Conectar__ para conectar con el nodo de cabeza. Cuando se le solicite, utilice el nombre de usuario de escritorio remoto de clúster y la contraseña para autenticar la conexión.

    ![Icono de conexión a Escritorio remoto](./media/hdinsight-debug-tez-ui/remotedesktopconnect.png)

    > [AZURE.NOTE] Si no ha habilitado la conectividad de escritorio remoto, proporcione un nombre de usuario, la contraseña y la fecha de expiración, seleccione __Activar__ para habilitar Escritorio remoto. Una vez que se ha habilitado, realice los pasos anteriores para conectarse.

3. Una vez conectado, abra Internet Explorer en el escritorio remoto, seleccione el icono de engranaje en la esquina superior derecha del explorador y, a continuación, seleccione __Configuración de la vista de compatibilidad__.

4. Desde la parte inferior de la __Configuración de la vista de compatibilidad__, desactive la casilla de verificación de __sitios de la intranet de presentación en la vista de compatibilidad__ y __listas de compatibilidad de Microsoft de uso__y, a continuación, seleccione __Cerrar__.

5. En Internet Explorer, vaya a tezui/http://headnodehost:8188 / #/. Esta opción mostrará la UI Tez

    ![Interfaz de usuario de Tez](./media/hdinsight-debug-tez-ui/tezui.png)

    Cuando se cargue la UI Tez, verá una lista de DAGs que se están ejecutando actualmente o que han sido ha encontrado en el clúster. La vista predeterminada incluye la Dag Name, Id, remitente, estado, hora de inicio, hora de finalización, duración, identificador de la aplicación y cola. Pueden agregar más columnas utilizando el icono de engranaje de la derecha de la página.

    Si tiene sólo una entrada, será la consulta que ha encontrado en la sección anterior. Si tiene múltiples entradas, puede buscar por criterios de búsqueda en los campos sobre la DAGs y luego presione __ENTRAR__.

4. Seleccione el __Nombre de DAG incluidos__ para la entrada de DAG incluidos más reciente. Esta opción mostrará información sobre la DAG incluidos, así como la opción para descargar un archivo zip de archivos JSON que contienen información sobre la DAG incluidos.

    ![Detalles de DAG incluidos](./media/hdinsight-debug-tez-ui/dagdetails.png)

5. Encima de los __Detalles de DAG incluidos__ hay varios vínculos que se pueden usar para mostrar información sobre la DAG incluidos.

    * __DAG incluidos contadores__ muestra información de contadores para este DAG incluidos.
    
    * __Vista gráfica__ muestra una representación gráfica de este DAG incluidos.
    
    * __Todos los vértices__ se muestra una lista de los vértices en este DAG incluidos.
    
    * __Todas las tareas__ muestra una lista de las tareas para todos los vértices en este DAG incluidos.
    
    * __Todos los TaskAttempts__ muestra información sobre los intentos para ejecutar las tareas para este DAG incluidos.
    
    > [AZURE.NOTE] Si se desplaza la visualización de la columna de vértices, tareas y TaskAttempts, observe que hay vínculos para ver __contadores__ y __Ver o descargar registros__ para cada fila.

    Si se ha producido un error con el trabajo, los detalles de DAG incluidos mostrará un estado de error, junto con vínculos a información sobre la tarea con error. Información de diagnóstico se mostrarán debajo de los detalles de DAG incluidos.

7. Seleccione la __vista gráfica__. Muestra una representación gráfica de la DAG incluidos. Puede colocar el mouse sobre cada vértice en la vista para mostrar información acerca de él.

    ![Vista gráfica](./media/hdinsight-debug-tez-ui/dagdiagram.png)

8. Al hacer clic en un vértice, cargará el __Vértice detalles__ para ese elemento. Haga clic en el vértice __1 de mapa__ para mostrar los detalles de este artículo. Seleccione __Confirmar__ para confirmar la navegación.

    ![Detalles de vértices](./media/hdinsight-debug-tez-ui/vertexdetails.png)

9. Observe que ahora tienen vínculos en la parte superior de la página que están relacionados con vértices y las tareas.

    > [AZURE.NOTE] También puede llegar a esta página volviendo a __DAG incluidos detalles__, seleccionando __Vértice detalles__y seleccionando el vértice __1 de mapa__ .

    * __Vértice contadores__ muestra información de contador para este vértice.
    
    * __Tareas__ muestra las tareas para este vértice.
    
    * __Tarea intenta__ muestra información acerca de los intentos para ejecutar las tareas para este vértice.
    
    * __Orígenes y receptores de__ orígenes de datos de muestra y receptores para este vértice.

    > [AZURE.NOTE] Como con el menú anterior, puede desplazarse por la presentación de la columna para las tareas, intentos de tarea y orígenes & Sinks__ mostrar los vínculos a más información para cada elemento.

10. Seleccione __las tareas__y, a continuación, seleccione el elemento denominado __00_000000__. Esta opción mostrará __Detalles de la tarea__ para esta tarea. Desde esta pantalla, puede ver __Tareas contadores__ y __Los intentos de tarea__.

    ![Detalles de la tarea](./media/hdinsight-debug-tez-ui/taskdetails.png)

## <a name="next-steps"></a>Pasos siguientes

Ahora que ha aprendido a usar la vista de Tez, más información sobre el [Uso de subárbol en HDInsight](hdinsight-use-hive.md).

Para obtener más información técnica sobre Tez, consulte la [página de Tez en Hortonworks](http://hortonworks.com/hadoop/tez/).
