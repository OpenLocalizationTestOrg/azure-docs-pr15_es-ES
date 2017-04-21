<properties
    pageTitle="Crear y cargar datos en tablas de la sección de almacenamiento de blobs | Microsoft Azure"
    description="Crear tablas de sección y cargar los datos en blob de subárbol tablas"
    services="machine-learning,storage"
    documentationCenter=""
    authors="bradsev"
    manager="jhubbard"
    editor="cgronlun" />

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/14/2016"
    ms.author="bradsev" />


#<a name="create-and-load-data-into-hive-tables-from-azure-blob-storage"></a>Crear y cargar datos en tablas de la sección de almacenamiento de blobs de Windows Azure

Este tema presenta genéricas consultas de la sección que crear tablas de sección y carguen datos desde el almacenamiento de blobs de Windows Azure. También se ofrecen algunas instrucciones sobre particiones subárbol tablas y cómo usar la optimizado fila en columnas (ORC) formato para mejorar el rendimiento de la consulta.

Vínculos de este **menú** a temas que describen cómo recopilar datos en entornos de destino donde los datos se pueden almacenar y procesar durante el proceso de ciencias de datos de equipo (TDSP).

[AZURE.INCLUDE [cap-ingest-data-selector](../../includes/cap-ingest-data-selector.md)]


## <a name="prerequisites"></a>Requisitos previos
En este artículo se supone que tiene:

* Crear una cuenta de almacenamiento de Azure. Si necesita instrucciones, consulte [acerca de Azure cuentas de almacenamiento](../storage/storage-create-storage-account.md). 
* Aprovisiona un clúster de Hadoop personalizado con el servicio de HDInsight.  Si necesita instrucciones, vea [Personalizar Azure HDInsight Hadoop clústeres para análisis avanzado](machine-learning-data-science-customize-hadoop-cluster.md).
* Habilitar acceso remoto al clúster, inició sesión y abre la consola de línea de comandos de Hadoop. Si necesita instrucciones, consulte [acceso a la cabeza de Hadoop clúster de nodo](machine-learning-data-science-customize-hadoop-cluster.md#headnode).

## <a name="upload-data-to-azure-blob-storage"></a>Cargar datos con el almacenamiento de blobs de Windows Azure
Si ha creado una máquina virtual Azure siguiendo las instrucciones proporcionadas en [Configurar una máquina virtual Azure para análisis avanzado](machine-learning-data-science-setup-virtual-machine.md), este archivo de comandos debe se ha descargado a la *C:\\usuarios\\\<nombre de usuario\>\\documentos\\secuencias de comandos de datos ciencias* directorio en la máquina virtual. Estas consultas de subárbol sólo requieren que conecte en su propio esquema de datos y la configuración de almacenamiento de blobs de Windows Azure en los campos adecuados para estar preparado para envío.

Suponemos que los datos de tablas de la sección se están en un formato tabular **sin comprimir** y que se ha cargado los datos a los valores predeterminados (o a una adicional) contenedor de la cuenta de almacenamiento utilizada por el clúster de Hadoop.

Si desea práctica en los **Datos de viaje de Nueva York Taxi**, debe:

- archivos de [Datos de viajes de Nueva York Taxi](http://www.andresmh.com/nyctaxitrips) **Descargar** el 24 (12 archivos de viaje y archivos de tarifa 12),
- **descomprima** todos los archivos en archivos .csv y, a continuación
- **cargar** el predeterminado (o el contenedor apropiado) de la cuenta de almacenamiento de Azure que ha sido creada por el procedimiento indicado en el tema de [clústeres personalizar Azure HDInsight Hadoop para el proceso de análisis avanzadas y tecnología](machine-learning-data-science-customize-hadoop-cluster.md) . El proceso para cargar los archivos .csv en el contenedor predeterminado de la cuenta de almacenamiento puede encontrarse en esta [página](machine-learning-data-science-process-hive-walkthrough.md#upload).


## <a name="submit"></a>Cómo enviar las consultas de la sección

Las consultas de la sección se pueden enviar mediante:

1. [Enviar consultas a través de línea de comandos de Hadoop de sección en headnode de clúster Hadoop](#headnode)
2. [Enviar consultas de la sección con el Editor de sección](#hive-editor)
3. [Enviar consultas de la sección con los comandos de PowerShell de Azure](#ps)

Las consultas de la sección de SQL. Si está familiarizado con SQL, le útil la [sección de hoja de referencia sobre los usuarios de SQL](http://hortonworks.com/wp-content/uploads/2013/05/hql_cheat_sheet.pdf) .

Al enviar una consulta de sección, también puede controlar el destino del resultado de las consultas de la sección, ya sea en la pantalla o en un archivo local en el nodo principal o a un blobs de Windows Azure.


###<a name="headnode"></a>1. envíe consultas de sección a través de línea de comandos de Hadoop en headnode de clúster Hadoop

Si la consulta de la sección es compleja, enviarlo directamente en el nodo principal de la Hadoop clúster normalmente lleva a activar más rápido de envío con un Editor de subárbol o Azure PowerShell de secuencias de comandos.

Inicie sesión en el nodo principal del clúster Hadoop, abra la línea de comandos Hadoop en el escritorio del nodo principal y escriba el comando `cd %hive_home%\bin`.

Existen tres maneras de enviar consultas de sección en la línea de comandos de Hadoop:

* directamente
* usar archivos de .hql
* con la consola de comandos de la sección

#### <a name="submit-hive-queries-directly-in-hadoop-command-line"></a>Enviar consultas subárbol directamente en Hadoop línea de comandos.

Puede ejecutar el comando como `hive -e "<your hive query>;` para enviar las consultas sencillas subárbol directamente en Hadoop línea de comandos. Aquí tenemos un ejemplo, donde el cuadro rojo rodee el comando que envía la consulta de la sección y el cuadro verde que describe el resultado de la consulta de la sección.

![Crear área de trabajo](./media/machine-learning-data-science-move-hive-tables/run-hive-queries-1.png)

#### <a name="submit-hive-queries-in-hql-files"></a>Enviar consultas subárbol en archivos de .hql

Cuando la consulta de la sección es más complicada y tiene varias líneas, modificación de consultas en la línea de comandos o la consola de comandos de la sección no es práctico. Una alternativa es utilizar un editor de texto en el nodo principal del clúster Hadoop para guardar las consultas de la sección en un archivo de .hql en un directorio local del nodo principal. A continuación, se puede enviar la consulta de sección en el archivo .hql mediante la `-f` argumento tal como sigue:

    hive -f "<path to the .hql file>"

![Crear área de trabajo](./media/machine-learning-data-science-move-hive-tables/run-hive-queries-3.png)


**Suprimir la impresión de pantalla de estado de progreso de las consultas de la sección**

De forma predeterminada, una vez subárbol consulta enviada en línea de comandos de Hadoop, el progreso de la tarea de mapa o reducir se imprimirá en pantalla. Para suprimir la impresión de pantalla del progreso de tarea/Reduce el mapa, puede usar el argumento `-S` ("S" en mayúsculas) en el comando línea como sigue:

    hive -S -f "<path to the .hql file>"
    hive -S -e "<Hive queries>"

#### <a name="submit-hive-queries-in-hive-command-console"></a>Enviar consultas de la sección en la consola de comandos de la sección.

En primer lugar también puede introducir la consola de comandos de sección, ejecute el comando `hive` en línea de comandos de Hadoop y, a continuación, envíe consultas de sección en la consola de comandos de la sección. Aquí tenemos un ejemplo. En este ejemplo, los dos cuadros rojo resaltan los comandos que se utiliza para introducir la consola de comandos de la sección y a continuación, la consulta de subárbol presentado en la consola de comandos de sección, respectivamente. El cuadro verde resalta el resultado de la consulta de la sección.

![Crear área de trabajo](./media/machine-learning-data-science-move-hive-tables/run-hive-queries-2.png)

Los ejemplos anteriores de salida directamente los resultados de la consulta de sección en la pantalla. También puede escribir el resultado en un archivo local en el nodo principal, o para un blobs de Windows Azure. A continuación, puede utilizar otras herramientas para analizar aún más los resultados de las consultas de la sección.

**Resultados de la consulta de sección en un archivo local de salida.**

Para generar los resultados de la consulta de sección en un directorio local en el nodo principal, tiene que enviar la consulta de sección en la línea de comandos de Hadoop como sigue:

    hive -e "<hive query>" > <local path in the head node>

En el ejemplo siguiente, el resultado de consulta de la sección se escribe en un archivo `hivequeryoutput.txt` en el directorio `C:\apps\temp`.

![Crear área de trabajo](./media/machine-learning-data-science-move-hive-tables/output-hive-results-1.png)

**Resultados de la consulta de sección de salida a un blobs de Windows Azure**

También puede obtener el resultado de consulta de sección en una blobs de Windows Azure, dentro del contenedor predeterminado del clúster Hadoop. La consulta de subárbol para esto es la siguiente:

    insert overwrite directory wasb:///<directory within the default container> <select clause from ...>

En el ejemplo siguiente, el resultado de consulta de la sección se escribe en un directorio de blob `queryoutputdir` dentro del contenedor predeterminado del clúster Hadoop. A continuación, solo debe proporcionar el nombre del directorio, sin el nombre de blobs de Windows. Se produce un error si proporciona los nombres de directorio y blob, como por ejemplo `wasb:///queryoutputdir/queryoutput.txt`.

![Crear área de trabajo](./media/machine-learning-data-science-move-hive-tables/output-hive-results-2.png)

Si abre el contenedor predeterminado del clúster Hadoop con el Explorador de almacenamiento de Azure, puede ver el resultado de la consulta de sección, como se muestra en la siguiente ilustración. Puede aplicar el filtro (resaltado con cuadro rojo) para recuperar solo el blob con letras especificadas en los nombres.

![Crear área de trabajo](./media/machine-learning-data-science-move-hive-tables/output-hive-results-3.png)

###<a name="hive-editor"></a>2. enviar consultas de la sección con el Editor de sección

También puede usar la consola de consulta (subárbol Editor) escribiendo una dirección URL del formulario *https://&#60; Nombre de clúster Hadoop >.azurehdinsight.net/Home/HiveEditor* en un explorador web. Debe estar conectado el consulte esta consola y por lo que debe sus credenciales de clúster de Hadoop.

###<a name="ps"></a>3. enviar consultas de la sección con los comandos de PowerShell de Azure

También puede usar PowerShell para enviar consultas de la sección. Para instrucciones, consulte la [sección enviar trabajos con PowerShell](../hdinsight/hdinsight-submit-hadoop-jobs-programmatically.md#hive-powershell).


## <a name="create-tables"></a>Crear tablas y base de datos de sección

Las consultas de la sección se comparten en el [repositorio de Github](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_create_db_tbls_load_data_generic.hql) y pueden descargarse desde allí.

Esta es la consulta subárbol que crea una tabla subárbol.

    create database if not exists <database name>;
    CREATE EXTERNAL TABLE if not exists <database name>.<table name>
    (
        field1 string,
        field2 int,
        field3 float,
        field4 double,
        ...,
        fieldN string
    )
    ROW FORMAT DELIMITED FIELDS TERMINATED BY '<field separator>' lines terminated by '<line separator>'
    STORED AS TEXTFILE LOCATION '<storage location>' TBLPROPERTIES("skip.header.line.count"="1");

Estas son las descripciones de los campos que necesita para conectar y otras configuraciones:

- **& #60; el nombre de la base de datos >**: el nombre de la base de datos que desea crear. Si desea usar la base de datos de forma predeterminada, se puede omitir la consulta para *crear la base de datos...* .
- **& #60; nombre de tabla >**: el nombre de la tabla que desea crear la base de datos especificada. Si desea usar la base de datos de forma predeterminada, la tabla se puede hacer referencia directamente por *& #60; nombre de tabla >* sin & #60; el nombre de la base de datos >.
- **& #60; separador de campos >**: el separador que delimita los campos en el archivo de datos cargarse en la tabla subárbol.
- **& #60; separador de línea >**: el separador que delimita líneas en el archivo de datos.
- **& #60; ubicación de almacenamiento >**: la ubicación de almacenamiento de Azure para guardar los datos de tablas de la sección. Si no especifica *ubicación & #60; ubicación de almacenamiento >*, la base de datos y las tablas se almacenan en *subárbol/almacén/* directorio en el contenedor predeterminado del clúster subárbol predeterminada. Si desea especificar la ubicación de almacenamiento, la ubicación de almacenamiento debe estar dentro del contenedor predeterminado para la base de datos y las tablas. Esta ubicación tiene que hacer referencia como ubicación en relación con el contenedor predeterminado del clúster en el formato de *' wasb: / / / & #60; directorio 1 > /'* o *' wasb: / / / & #60; directorio 1 > / & #60; directorio 2 > /'*, etcetera. Después de ejecuta la consulta, se crean los directorios relativos dentro del contenedor de forma predeterminada.
- **TBLPROPERTIES("Skip.Header.Line.Count"="1")**: si el archivo de datos tiene una línea de encabezado, debe agregar esta propiedad **al final** de la consulta *Crear tabla* . En caso contrario, la línea de encabezado se carga como un registro a la tabla. Si el archivo de datos no tiene una línea de encabezado, puede omitir esta configuración en la consulta.

## <a name="load-data"></a>Cargar datos en tablas de la sección
Esta es la consulta subárbol que carga los datos en una tabla subárbol.

    LOAD DATA INPATH '<path to blob data>' INTO TABLE <database name>.<table name>;

- **& #60; ruta de acceso a datos blob >**: si el archivo de blobs cargarse en la tabla subárbol está en el contenedor predeterminado del clúster HDInsight Hadoop, la *& #60; ruta de acceso a datos blob >* debe estar en el formato *' wasb: / / / & #60; directorio en este contenedor > / & #60; el nombre de archivo blob >'*. También puede ser el archivo blob en un contenedor adicional del clúster HDInsight Hadoop. En este caso, *& #60; ruta de acceso a datos blob >* debe estar en el formato *' wasb: / / & #60; contenedor name>@&#60;storage nombre de la cuenta >.blob.core.windows.net/ & #60; nombre de archivo blob >'*.

    >[AZURE.NOTE] Los datos de blob cargarse en tabla subárbol tienen que estar en el contenedor adicional de la cuenta de almacenamiento para el clúster de Hadoop o el predeterminado. En caso contrario, la consulta de *Datos de carga* falla queja que no puede tener acceso a los datos.


## <a name="partition-orc"></a>Temas avanzados: Dividir tabla y almacén de datos de subárbol en formato ORC

Si los datos están grandes, la partición de la tabla es útil para las consultas que solo necesitan analizar algunas particiones de la tabla. Por ejemplo, es aconsejable dividir los datos de registro de un sitio web por fechas.

Además de la sección tablas de partición, también es útil almacenar los datos de la sección en el formato de fila de optimizado en columnas (ORC). Para obtener más información sobre cómo aplicar formato ORC, vea <a href="https://cwiki.apache.org/confluence/display/Hive/LanguageManual+ORC#LanguageManualORC-ORCFiles" target="_blank">usar ORC archivos mejora el rendimiento al subárbol es leer, escribir y procesar datos</a>.

### <a name="partitioned-table"></a>Tabla con particiones
Esta es la consulta subárbol que crea una tabla con particiones y carga los datos en él.

    CREATE EXTERNAL TABLE IF NOT EXISTS <database name>.<table name>
    (field1 string,
    ...
    fieldN string
    )
    PARTITIONED BY (<partitionfieldname> vartype) ROW FORMAT DELIMITED FIELDS TERMINATED BY '<field separator>'
         lines terminated by '<line separator>' TBLPROPERTIES("skip.header.line.count"="1");
    LOAD DATA INPATH '<path to the source file>' INTO TABLE <database name>.<partitioned table name>
        PARTITION (<partitionfieldname>=<partitionfieldvalue>);

Al consultar tablas con particiones, se recomienda agregar la condición de partición al **principio** de la `where` cláusula como Esto mejora la eficacia de la búsqueda de forma significativa.

    select
        field1, field2, ..., fieldN
    from <database name>.<partitioned table name>
    where <partitionfieldname>=<partitionfieldvalue> and ...;

### <a name="orc"></a>Almacenar los datos de la sección en formato ORC

No se puede cargar directamente datos desde el almacenamiento de blobs en tablas de la sección que se almacena en el formato ORC. Estos son los pasos que se debe tomar para cargar datos de Azure BLOB a las tablas de la sección almacenadas en formato ORC.

Crear un **Archivo de almacenados como texto** y cargar datos de la tabla externa de almacenamiento de blobs a la tabla.

        CREATE EXTERNAL TABLE IF NOT EXISTS <database name>.<external textfile table name>
        (
            field1 string,
            field2 int,
            ...
            fieldN date
        )
        ROW FORMAT DELIMITED FIELDS TERMINATED BY '<field separator>'
            lines terminated by '<line separator>' STORED AS TEXTFILE
            LOCATION 'wasb:///<directory in Azure blob>' TBLPROPERTIES("skip.header.line.count"="1");

        LOAD DATA INPATH '<path to the source file>' INTO TABLE <database name>.<table name>;

Crear una tabla interna con el mismo esquema que la tabla externa en el paso 1, con el mismo delimitador de campo y a continuación, almacenar los datos de la sección en el formato ORC.

        CREATE TABLE IF NOT EXISTS <database name>.<ORC table name>
        (
            field1 string,
            field2 int,
            ...
            fieldN date
        )
        ROW FORMAT DELIMITED FIELDS TERMINATED BY '<field separator>' STORED AS ORC;

Seleccionar datos de la tabla externa en el paso 1 e insertar en la tabla ORC

        INSERT OVERWRITE TABLE <database name>.<ORC table name>
            SELECT * FROM <database name>.<external textfile table name>;

>[AZURE.NOTE] Si la tabla de archivo de texto *& #60; el nombre de la base de datos >. & #60; el nombre de la tabla de archivo de texto externo >* tiene particiones, en el paso 3, la `SELECT * FROM <database name>.<external textfile table name>` comando selecciona la variable partición como un campo en el conjunto de datos devuelto. Insertar la *& #60; el nombre de la base de datos >. & #60; el nombre de la tabla ORC >* falla desde *& #60; el nombre de la base de datos >. & #60; el nombre de la tabla ORC >* no tiene la variable partición como un campo en el esquema de la tabla. En este caso, debe seleccionar específicamente los campos que se debe insertar en *& #60; el nombre de la base de datos >. & #60; el nombre de la tabla ORC >* como sigue:

        INSERT OVERWRITE TABLE <database name>.<ORC table name> PARTITION (<partition variable>=<partition value>)
           SELECT field1, field2, ..., fieldN
           FROM <database name>.<external textfile table name>
           WHERE <partition variable>=<partition value>;

Es seguro quitar la *& #60; el nombre de la tabla de archivo de texto externo >* cuando usa la siguiente consulta cuando todos los datos se ha insertado en *& #60; el nombre de la base de datos >. & #60; el nombre de la tabla ORC >*:

        DROP TABLE IF EXISTS <database name>.<external textfile table name>;

Después de seguir este procedimiento, debe tener una tabla con los datos en el formato ORC listo para usar.  
