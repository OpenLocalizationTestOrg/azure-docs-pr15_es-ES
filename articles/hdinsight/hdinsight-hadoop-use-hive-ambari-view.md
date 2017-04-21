<properties
   pageTitle="Usar vistas de Ambari para trabajar con la sección de HDInsight (Hadoop) | Microsoft Azure"
   description="Obtenga información sobre cómo usar la vista de la sección desde el explorador web para enviar las consultas de la sección. La vista de sección es parte de la interfaz de usuario de Web Ambari proporcionado con el clúster HDInsight basados en Linux."
   services="hdinsight"
   documentationCenter=""
   authors="Blackmist"
   manager="jhubbard"
   editor="cgronlun"
    tags="azure-portal"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="10/28/2016"
   ms.author="larryfr"/>

#<a name="use-the-hive-view-with-hadoop-in-hdinsight"></a>Usar la vista de sección con Hadoop en HDInsight

[AZURE.INCLUDE [hive-selector](../../includes/hdinsight-selector-use-hive.md)]

Ambari es una administración y supervisión utilidad proporcionada con clústeres HDInsight basados en Linux. Una de las características proporcionadas por Ambari es una interfaz de usuario de Web que se pueden usar para ejecutar consultas de la sección. Esta es la __Sección Ver__, parte de las vistas de Ambari proporcionadas por el clúster HDInsight.

> [AZURE.NOTE] Ambari tiene una gran cantidad de capacidades que no se tratan en este documento. Para obtener más información, vea [Administrar HDInsight clústeres utilizando la interfaz de usuario de Web Ambari](hdinsight-hadoop-manage-ambari.md).

##<a name="prerequisites"></a>Requisitos previos

- Un clúster de HDInsight basados en Linux. Para obtener información sobre cómo crear un nuevo clúster, vea [Introducción a HDInsight de Linux](hdinsight-hadoop-linux-tutorial-get-started.md).

##<a name="open-the-hive-view"></a>Abrir la vista de sección

Puede Ambari vistas desde el Portal de Azure; Seleccione el clúster HDInsight y, a continuación, seleccione __Ambari vistas__ en la sección __Vínculos rápidos__ .

![sección de vínculos rápidos](./media/hdinsight-hadoop-use-hive-ambari-view/quicklinks.png)

Puede también ir directamente a Ambari yendo a https://CLUSTERNAME.azurehdinsight.net en un explorador web (donde __NOMBREDECLÚSTER__ es el nombre de su clúster HDInsight) y, a continuación, seleccione el conjunto de los cuadrados en el menú de la página (junto al vínculo de __administración__ y el botón de la izquierda de la página) a las vistas de lista disponible. Seleccione la __vista de sección__.

![Seleccionar vistas ambari](./media/hdinsight-hadoop-use-hive-ambari-view/selecthiveview.png).

> [AZURE.NOTE] Al acceder a Ambari, le pedirá para autenticar al sitio. Escriba el administrador (predeterminado `admin`,) nombre de cuenta y contraseña que utilizó al crear el clúster.

Verá una página similar al siguiente:

![Imagen de la página de la vista de sección que contiene una sección del editor de consultas](./media/hdinsight-hadoop-use-hive-ambari-view/hiveview.png)

##<a name="view-tables"></a>Tablas de vista

En la sección de __Explorador de base de datos__ de la página, seleccione la entrada de __forma predeterminada__ en la pestaña de __bases de datos__ . Esta opción mostrará una lista de tablas en la base de datos de forma predeterminada. En un clúster de HDInsight nuevo, debe existir una única tabla; __hivesampletable__.

![Explorador de base de datos con la base de datos predeterminada expandido](./media/hdinsight-hadoop-use-hive-ambari-view/databaseexplorer.png)

Cuando se agregan tablas nuevas a través de los pasos de este documento, puede usar el icono de actualización en la esquina superior derecha del explorador de base de datos para actualizar la lista de tablas disponibles.

##<a name="hivequery"></a>Editor de consultas

Para ejecutar una consulta de la sección con los datos incluidos en el clúster, realice los siguientes pasos de la vista de sección.

1. En la sección __Editor de consultas__ de la página, pegue las siguientes afirmaciones HiveQL en la hoja de cálculo:

        DROP TABLE log4jLogs;
        CREATE EXTERNAL TABLE log4jLogs(t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string)
        ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
        STORED AS TEXTFILE LOCATION 'wasbs:///example/data/';
        SELECT t4 AS sev, COUNT(*) AS cnt FROM log4jLogs WHERE t4 = '[ERROR]' GROUP BY t4;

    Estas instrucciones realizan las siguientes acciones:

    - **Quitar tabla** - elimina la tabla y el archivo de datos, en caso de que la tabla ya existe.
    - **Crear una tabla externa** : se crea una nueva tabla "externa" en la sección. Tablas externas almacenan la definición de la tabla en la sección; los datos se quedan en la ubicación original.
    - **Formato de fila** : la indica subárbol cómo se aplica el formato de los datos. En este caso, los campos de cada registro separados por un espacio.
    - **Almacenados como archivo de texto ubicación** - indica subárbol dónde están los datos almacenados (el directorio de ejemplo de datos) y que TI se almacena como texto.
    - **Seleccione** - selecciona un recuento de todas las filas donde t4 columna contiene el valor [ERROR].

    >[AZURE.NOTE] Tablas externas se deben usar cuando se esperan los datos subyacentes que se actualicen por un origen externo, como un proceso de carga automática de datos, o por otra operación MapReduce, pero desea siempre las consultas de la sección para usar los datos más recientes. Anulación de una tabla externa hace *no* eliminar los datos, la definición de la tabla.

2. Use el botón __Ejecutar__ en la parte inferior del Editor de consultas para iniciar la consulta. Debe activar naranja y cambiará el texto para __Detener la ejecución__. Una sección de __Resultados de proceso de la consulta__ debe aparecer debajo del Editor de consultas y mostrar información sobre el trabajo.

    > [AZURE.IMPORTANT] Algunos exploradores, no pueden actualizar la información de registro o resultados correctamente. Si se ejecuta un trabajo y parece que ejecute siempre sin actualizar el registro o devolver resultados, pruebe a usar Mozilla FireFox o Google Chrome en su lugar.

3. Una vez finalizada la consulta, la sección de __Resultados de proceso de la consulta__ mostrará los resultados de la operación. El botón __Detener la ejecución__ también cambiará a un botón __Ejecutar__ verde. La ficha __resultados__ debe contener la siguiente información:

        sev       cnt
        [ERROR]   3

    La ficha __registros de__ puede usarse para ver la información de registro creada por el trabajo. Puede utilizarla para solucionar problemas si hay problemas con una consulta.

    > [AZURE.TIP] Cuadro de diálogo de la lista desplegable __Guardar los resultados__ de la nota en la esquina superior izquierda de la sección de __Resultados de proceso de la consulta__ . Puede usar esta a descargar los resultados o guardarlos en almacenamiento HDInsight como un archivo CSV.

3. Seleccione las primeras cuatro líneas de esta consulta y luego seleccione __Ejecutar__. Observe que no hay ningún resultado cuando finalice el trabajo. Esto es porque mediante el botón de __ejecución__ cuando se selecciona la parte de la consulta se ejecutarán sólo los extractos seleccionados. En este caso, la selección no incluya la instrucción final que recupera filas de la tabla. Si selecciona solo dicha línea y utilizar __Execute__, verá los resultados esperados.

3. Use el botón __Nueva hoja de cálculo__ en la parte inferior del __Editor de consultas__ para crear una nueva hoja de cálculo. En la hoja de cálculo nueva, escriba las siguientes afirmaciones HiveQL:

        CREATE TABLE IF NOT EXISTS errorLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) STORED AS ORC;
        INSERT OVERWRITE TABLE errorLogs SELECT t1, t2, t3, t4, t5, t6, t7 FROM log4jLogs WHERE t4 = '[ERROR]';

    Estas instrucciones realizan las siguientes acciones:

    - **Crear tabla si no existe** - crea una tabla, si aún no existe. Puesto que no se usa la palabra clave **externa** , esta es una tabla interna, que se almacena en el almacén de datos de la sección y se administra completamente por sección. A diferencia de tablas externas, la colocación de una tabla interna eliminará también los datos subyacentes.
    - **Almacenados como ORC** - almacena los datos en formato de fila de optimizado en columnas (ORC). Se trata de un formato altamente optimizado y eficaz para almacenar datos de la sección.
    - SOBRESCRIBIR **Insertar... Seleccione** - selecciona las filas de la tabla de **log4jLogs** que contienen [ERROR] y, a continuación, inserta los datos en la tabla **errorLogs** .

    Use el botón __Ejecutar__ para ejecutar esta consulta. La ficha __resultados__ no contendrá ninguna información como esta consulta no devuelve ninguna fila, pero debe mostrar el estado como __correcta__.

###<a name="hive-settings"></a>Configuración de la sección

Seleccione el icono de __configuración__ a la derecha del editor.

![iconos](./media/hdinsight-hadoop-use-hive-ambari-view/settings.png)

Configuración puede utilizarse para cambiar las opciones de sección distintos, como el motor de ejecución de subárbol desde Tez (el predeterminado), para MapReduce.

###<a name="visual-explain"></a>Explique Visual

Seleccione el icono __Visual Explique__ a la derecha del editor.

![iconos](./media/hdinsight-hadoop-use-hive-ambari-view/visualexplainicon.png)

Esta es la vista __Explicar Visual__ de la consulta, que puede resultar útiles para entender el flujo de consultas complejas. Puede ver un equivalente textual de esta vista usando el botón __Explicar__ en el Editor de consultas.

![Visual explicar imagen](./media/hdinsight-hadoop-use-hive-ambari-view/visualexplain.png)

###<a name="tez"></a>Tez

Seleccione el icono de __Tez__ a la derecha del editor.

![iconos](./media/hdinsight-hadoop-use-hive-ambari-view/tez.png)

Esta opción mostrará la dirigen acíclicos Graph (DAG incluidos) utilizado Tez para esta consulta, si está disponible. Si desea ver la DAG incluidos de consultas ha ejecutó en el pasado o depurar el proceso de Tez, usar la [Vista de Tez](hdinsight-debug-ambari-tez-view.md) en su lugar.

###<a name="notifications"></a>Notificaciones

Seleccione el icono de __notificaciones__ a la derecha del editor.

![iconos](./media/hdinsight-hadoop-use-hive-ambari-view/notifications.png)

Las notificaciones son mensajes que se generan al ejecutar consultas. Por ejemplo, recibirá una notificación cuando se envía una consulta, o cuando se produce un error.

##<a name="saved-queries"></a>Consultas guardadas

1. Desde el Editor de consultas, crear una nueva hoja de cálculo y escriba la siguiente consulta:

        SELECT * from errorLogs;

    Ejecutar la consulta para comprobar que funciona. El resultado será como sigue:

        errorlogs.t1    errorlogs.t2    errorlogs.t3    errorlogs.t4    errorlogs.t5    errorlogs.t6    errorlogs.t7
        2012-02-03  18:35:34    SampleClass0    [ERROR]     incorrect   id  
        2012-02-03  18:55:54    SampleClass1    [ERROR]     incorrect   id  
        2012-02-03  19:25:27    SampleClass4    [ERROR]     incorrect   id

2. Use el botón __Guardar como__ en la parte inferior del editor. Nombre de esta consulta __Errorlogs__ y haga clic en __Aceptar__. Tenga en cuenta que el nombre de la hoja de cálculo cambia a __Errorlogs__.

3. Seleccione la pestaña __Consultas guardadas__ en la parte superior de la página de vista de la sección. Tenga en cuenta que __Errorlogs__ ahora aparece como una consulta guardada. Permanecerán en esta lista hasta que lo quite. Seleccionar el nombre se abrirá la consulta en el Editor de consultas.

##<a name="query-history"></a>Historial de consulta

El botón __historial__ en la parte superior de la vista de sección permite a las consultas de la vista tiene ejecutó previamente. Utilizar ahora y seleccione algunas de las consultas que tiene ejecutaban previamente. Cuando se selecciona una consulta, se abre en el Editor de consultas.

##<a name="user-defined-functions-udf"></a>Definidas por el usuario (UDF) de funciones

También puede ampliarse subárbol a través de **funciones definidas por el usuario (UDF)**. Un archivo UDF le permite implementar la funcionalidad o lógica que no es fácil modelado en HiveQL.

Aunque puede agregar un archivo UDF como parte de las instrucciones de HiveQL en la consulta, la ficha UDF en la parte superior de la vista de sección le permite declarar y guardar un conjunto de UDF que se pueden usar con el __Editor de consultas__.

Una vez que haya agregado una UDF a la vista de sección, aparecerá un botón __Insertar UDF__ en la parte inferior del __Editor de consultas__. Al seleccionar esta se mostrará una lista desplegable de la UDF definidos en la vista de sección. Seleccionar un archivo UDF agregará HiveQL instrucciones a la consulta para habilitar el archivo UDF.

Por ejemplo, si ha definido un archivo UDF con las siguientes propiedades:

* Nombre del recurso: myudfs
* Ruta de acceso de recursos: wasbs:///myudfs.jar
* Nombre del archivo UDF: myawesomeudf
* Nombre de la clase UDF: com.myudfs.Awesome

Con el botón __Insertar UDF__ se mostrará una entrada denominada __myudfs__con otra lista desplegable para cada UDF definido para ese recurso. En este caso, __myawesomeudf__. Seleccionar esta entrada se agregue lo siguiente al principio de la consulta:

    add jar wasbs:///myudfs.jar;

    create temporary function myawesomeudf as 'com.myudfs.Awesome';

A continuación, puede usar el archivo UDF en la consulta. Por ejemplo, `SELECT myawesomeudf(name) FROM people;`.

Para obtener más información sobre cómo usar UDF con subárbol en HDInsight, consulte lo siguiente:

* [Usar Python con la sección y cerdo en HDInsight](hdinsight-python.md)

* [Cómo agregar una UDF subárbol personalizado a HDInsight](http://blogs.msdn.com/b/bigdatasupport/archive/2014/01/14/how-to-add-custom-hive-udfs-to-hdinsight.aspx)

##<a id="nextsteps"></a>Pasos siguientes

Para obtener información general sobre subárbol en HDInsight:

* [Usar subárbol con Hadoop en HDInsight](hdinsight-use-hive.md)

Para obtener información sobre otras maneras de poder trabajar con Hadoop en HDInsight:

* [Usar cerdo con Hadoop en HDInsight](hdinsight-use-pig.md)

* [Usar MapReduce con Hadoop en HDInsight](hdinsight-use-mapreduce.md)
