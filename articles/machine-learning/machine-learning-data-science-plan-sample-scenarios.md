<properties
    pageTitle="Escenarios para el análisis avanzado procesos y tecnología en aprendizaje de máquina Azure | Microsoft Azure"
    description="Seleccione los escenarios adecuados para hacerlo avanzada predictivo análisis con el proceso de ciencias de datos de grupo."
    services="machine-learning"
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
    ms.date="09/19/2016" 
    ms.author="bradsev" />


# <a name="scenarios-for-advanced-analytics-in-azure-machine-learning"></a>Escenarios para análisis avanzado en aprendizaje de máquina de Azure

Este artículo describe la variedad de orígenes de datos de ejemplo y escenarios de destino que se pueden controlar el [Proceso de ciencias de datos de equipo (TDSP)](data-science-process-overview.md). El TDSP proporciona un enfoque sistemático para equipos colaborar en la creación de aplicaciones inteligentes. Los escenarios que se presentan a continuación muestran opciones disponibles en el flujo de trabajo de procesamiento de datos que dependen de las características de los datos, ubicaciones de origen y repositorios de destino en Azure.

En la última sección se presenta el **árbol de decisión** de selección de los escenarios de ejemplo que es adecuado para sus datos y objetivo.

>[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]


Cada una de las siguientes secciones presenta un escenario de ejemplo. Para cada escenario, una ciencia datos posibles o funciones analíticas avanzadas flujo y compatibilidad de Azure recursos se muestran.

>[AZURE.NOTE]**Para todas las situaciones siguientes, debe:**
><br/>
>* [Crear una cuenta de almacenamiento](../storage/storage-create-storage-account.md)
><br/>
>* [Crear un área de trabajo de aprendizaje del equipo de Azure](machine-learning-create-workspace.md)


## <a name="smalllocal"></a>Escenario \#1: pequeñas y mediana tabular del conjunto de datos en los archivos locales

![Pequeñas y medianos archivos locales][1]

#### <a name="additional-azure-resources-none"></a>Recursos adicionales de Azure: ninguno

1.  Inicie sesión en el [equipo Azure Studio de aprendizaje](https://studio.azureml.net/).

2.  Cargar un conjunto de datos.

3.  Crear un flujo de ensayo de aprendizaje del equipo de Azure comenzando con conjuntos de datos cargados.

## <a name="smalllocalprocess"></a>Escenario \#2: pequeñas y mediana conjunto de datos de los archivos locales que requieren procesamiento

![Pequeñas y medianos archivos locales con procesamiento][2]

#### <a name="additional-azure-resources-azure-virtual-machine-ipython-notebook-server"></a>Recursos adicionales de Azure: máquina Azure (servidor de Bloc de notas de IPython)

1.  Crear una máquina Virtual de Azure ejecutando el Bloc de notas de IPython.

2.  Cargar datos en un contenedor de almacenamiento de Azure.

3.  Procesar previamente y limpiar los datos en el Bloc de notas IPython, acceso a los datos del contenedor de almacenamiento de Azure.

4.  Transformar datos limpiar formato tabular.

5.  Guardar datos transformados en Azure BLOB.

6.  Inicie sesión en el [equipo Azure Studio de aprendizaje](https://studio.azureml.net/).

7.  Leer los datos de Azure BLOB usando los [Datos de importación] [ import-data] módulo.

8. Crear un flujo de ensayo de aprendizaje del equipo de Azure comenzando con conjuntos de datos integrados.

## <a name="largelocal"></a>Escenario \#3: conjunto de datos grande de archivos locales, identificación de Blobs de Azure

![Grandes archivos locales][3]

#### <a name="additional-azure-resources-azure-virtual-machine-ipython-notebook-server"></a>Recursos adicionales de Azure: máquina Azure (servidor de Bloc de notas de IPython)

1.  Crear una máquina Virtual de Azure ejecutando el Bloc de notas de IPython.

2.  Cargar datos en un contenedor de almacenamiento de Azure.

3.  Procesar previamente y limpiar los datos en el Bloc de notas IPython, acceso a los datos de Azure BLOB.

4.  Transformar datos limpiar formato tabular, si es necesario.

5.  Explorar datos y crear características según sea necesario.

6.  Extraer una muestra de datos de pequeñas y medianas.

7.  Guardar los datos muestras de blobs de Azure.

8. Inicie sesión en el [equipo Azure Studio de aprendizaje](https://studio.azureml.net/).

9. Leer los datos de Azure BLOB usando los [Datos de importación] [ import-data] módulo.

10. Crear flujo de ensayo de aprendizaje del equipo de Azure comenzando con conjuntos de datos integrados.


## <a name="smalllocaltodb"></a>Escenario \#4: pequeñas y mediana conjunto de datos de archivos locales, identificación de SQL Server en un equipo de Virtal de Azure

![Pequeñas y medianos archivos locales a la base de datos de SQL en Azure][4]

#### <a name="additional-azure-resources-azure-virtual-machine-sql-server--ipython-notebook-server"></a>Recursos adicionales de Azure: Azure Máquina Virtual (SQL Server o servidor de Bloc de notas de IPython)

1.  Crear una máquina Virtual de Azure ejecuta SQL Server + IPython Bloc de notas.

2.  Cargar datos en un contenedor de almacenamiento de Azure.

3.  Procesar previamente y limpiar los datos en el contenedor de almacenamiento de Azure utilizando el Bloc de notas de IPython.

4.  Transformar datos limpiar formato tabular, si es necesario.

5.  Guardar datos en archivos de VM local (Bloc de notas de IPython se ejecuta en VM, consulte unidades VM unidades locales).

6.  Cargar datos a datos de SQL Server que se ejecuta en una máquina virtual de Azure.

    Opción \#1: usar SQL Server Management Studio.

    - Inicio de sesión a SQL Server VM
    - Ejecutar SQL Server Management Studio.
    - Crear tablas de base de datos y de destino.
    - Use uno de masa importar métodos para cargar los datos de los archivos de VM local.

    Opción \#2: usar IPython Bloc de notas: no se recomienda para los conjuntos de datos más grandes y medianas<!-- -->    
    - Usar cadena de conexión ODBC para tener acceso a SQL Server en máquina virtual.
    - Crear tablas de base de datos y de destino.
    - Use uno de masa importar métodos para cargar los datos de los archivos de VM local.

7.  Explorar datos, crear características según sea necesario. Tenga en cuenta que las características no es necesario materializar en las tablas de base de datos. Solo tenga en cuenta la consulta necesaria para crearlos.

8. Decida en un tamaño de la muestra de datos, si es necesario o deseado.

9. Inicie sesión en el [equipo Azure Studio de aprendizaje](https://studio.azureml.net/).

10. Leer los datos directamente desde el servidor de SQL Server usando los [Datos de importación] [ import-data] módulo. Pegue la consulta es necesaria que extrae campos, crea características y ejemplos de datos si es necesario directamente en los [Datos de importación] [ import-data] consulta.

11. Crear flujo de ensayo de aprendizaje del equipo de Azure comenzando con conjuntos de datos integrados.

## <a name="largelocaltodb"></a>Escenario \#5: grandes conjuntos de datos en un archivo local, SQL Server en Azure VM de destino

![Archivos grandes de locales a la base de datos de SQL en Azure][5]

#### <a name="additional-azure-resources-azure-virtual-machine-sql-server--ipython-notebook-server"></a>Recursos adicionales de Azure: Azure Máquina Virtual (SQL Server o servidor de Bloc de notas de IPython)

1.  Crear una máquina Virtual de Azure ejecuta SQL Server y servidor de Bloc de notas de IPython.

2.  Cargar datos en un contenedor de almacenamiento de Azure.

3.  (Opcional) Procesar previamente y limpiar los datos.

    una.  Procesar previamente y limpiar los datos en el Bloc de notas IPython, acceso a los datos de Azure BLOB.

    b.  Transformar datos limpiar formato tabular, si es necesario.

    c.  Guardar datos en archivos de VM local (Bloc de notas de IPython se ejecuta en VM, consulte unidades VM unidades locales).

4.  Cargar datos a datos de SQL Server que se ejecuta en una máquina virtual de Azure.

    una.  Inicie sesión en la máquina virtual de SQL Server.

    b.  Si los datos no se guardan ya, descargar los archivos de datos del contenedor de almacenamiento de Azure a la carpeta local de máquina virtual.

    c.  Ejecutar SQL Server Management Studio.

    d.  Crear tablas de base de datos y de destino.

    e.  Use uno de masa importar métodos para cargar los datos.

    f.  Si se requieren combinaciones de tabla, cree índices para agilizar las combinaciones.

     > [AZURE.NOTE] Para acelerar la carga de tamaños de datos de gran tamaño, se recomienda que crear tablas con particiones y los datos en paralelo de importación masiva. Para obtener más información, vea [Paralelo importación de datos a las tablas de particiones de SQL](machine-learning-data-science-parallel-load-sql-partitioned-tables.md).

5.  Explorar datos, crear características según sea necesario. Tenga en cuenta que las características no es necesario materializar en las tablas de base de datos. Solo tenga en cuenta la consulta necesaria para crearlos.

6.  Decida en un tamaño de la muestra de datos, si es necesario o deseado.

7.  Inicie sesión en el [equipo Azure Studio de aprendizaje](https://studio.azureml.net/).

8. Leer los datos directamente desde el servidor de SQL Server usando los [Datos de importación] [ import-data] módulo. Pegue la consulta es necesaria que extrae campos, crea características y ejemplos de datos si es necesario directamente en los [Datos de importación] [ import-data] consulta.

9. Flujo de ensayo Azure aprendizaje simple, comenzando con el conjunto de datos cargado

## <a name="largedbtodb"></a>Escenario \#6: grandes conjuntos de datos en un servidor SQL base de datos local, identificación de SQL Server en una máquina Virtual de Azure

![SQL grande DB local a la base de datos de SQL en Azure][6]

#### <a name="additional-azure-resources-azure-virtual-machine-sql-server--ipython-notebook-server"></a>Recursos adicionales de Azure: Azure Máquina Virtual (SQL Server o servidor de Bloc de notas de IPython)

1.  Crear una máquina Virtual de Azure ejecuta SQL Server y servidor de Bloc de notas de IPython.

2.  Utilice uno de los datos exportar métodos para exportar los datos de SQL Server a archivos de descarga.

    > [AZURE.NOTE] Si decide mover todos los datos de la base de datos local, un método alternativo (más rápido) para mover la base de datos completa a la instancia de SQL Server en Azure. Omitir los pasos para exportar los datos, crear base de datos y carga o importar datos a la base de datos de destino y siga el método alternativo.

3.  Cargar archivos de descarga al contenedor de almacenamiento de Azure.

4.  Cargar los datos a una base de datos de SQL Server que se ejecuta en una máquina Virtual de Azure.

    una.  Inicie sesión en la máquina virtual de SQL Server.

    b.  Descargar archivos de datos desde un contenedor de almacenamiento de Azure en la carpeta local de máquina virtual.

    c.  Ejecutar SQL Server Management Studio.

    d.  Crear tablas de base de datos y de destino.

    e.  Use uno de masa importar métodos para cargar los datos.

    f.  Si se requieren combinaciones de tabla, cree índices para agilizar las combinaciones.

    > [AZURE.NOTE] Para acelerar la carga de tamaños de grandes cantidades de datos, crear tablas con particiones y masa importar los datos en paralelo. Para obtener más información, vea [Paralelo importación de datos a las tablas de particiones de SQL](machine-learning-data-science-parallel-load-sql-partitioned-tables.md).

5.  Explorar datos, crear características según sea necesario. Tenga en cuenta que las características no es necesario materializar en las tablas de base de datos. Solo tenga en cuenta la consulta necesaria para crearlos.

6.  Decida en un tamaño de la muestra de datos, si es necesario o deseado.

7.  Inicie sesión en el [equipo Azure Studio de aprendizaje](https://studio.azureml.net/).

8. Leer los datos directamente desde el servidor de SQL Server usando los [Datos de importación] [ import-data] módulo. Pegue la consulta es necesaria que extrae campos, crea características y ejemplos de datos si es necesario directamente en los [Datos de importación] [ import-data] consulta.

9. Aprendizaje de máquina Azure simple experimentar flujo comenzando con el conjunto de datos cargado.

### <a name="alternate-method-to-copy-a-full-database-from-an-on-premises--sql-server-to-azure-sql-database"></a>Método alternativo para copiar una base de datos completa de un servidor local SQL Server a la base de datos de SQL Azure

![Desasociar base de datos local y adjuntar a la base de datos de SQL en Azure][7]

#### <a name="additional-azure-resources-azure-virtual-machine-sql-server--ipython-notebook-server"></a>Recursos adicionales de Azure: Azure Máquina Virtual (SQL Server o servidor de Bloc de notas de IPython)

Para replicar la base de datos SQL Server en la VM de SQL Server, debe copiar una base de datos desde una ubicación/servidor a otro, suponiendo que la base de datos puede desconectar temporalmente. Para ello, en el Explorador de objetos de SQL Server Management Studio, o mediante los comandos de Transact-SQL equivalentes.

1. Desconectar la base de datos en la ubicación de origen. Para obtener más información, vea [desasociar una base de datos](https://technet.microsoft.com/library/ms191491(v=sql.110).aspx).
2. En la ventana del explorador de Windows o el símbolo del sistema de Windows, copie el archivo de base de datos separada o archivos y archivo de registro o archivos en la ubicación de destino en la máquina virtual de servidor de SQL en Azure.
3. Adjuntar los archivos copiados a la instancia de SQL Server de destino. Para obtener más información, consulte [adjuntar una base de datos](https://technet.microsoft.com/library/ms190209(v=sql.110).aspx).

[Mover una base de datos utilizando separar y adjuntar (Transact-SQL)](https://technet.microsoft.com/library/ms187858(v=sql.110).aspx)

## <a name="largedbtohive"></a>Escenario \#7: grandes datos en archivos locales, destino base de datos de subárbol en clústeres HDInsight Hadoop de Azure

![Datos grandes en la sección de destino local][9]

#### <a name="additional-azure-resources-azure-hdinsight-hadoop-cluster-and-azure-virtual-machine-ipython-notebook-server"></a>Recursos adicionales de Azure: Hadoop clúster HDInsight de Azure y Máquina Virtual de Azure (servidor de Bloc de notas de IPython)

1.  Crear una máquina Virtual de Azure ejecuta el Bloc de notas de IPython server.

2.  Crear un clúster de Azure HDInsight Hadoop.

3.  (Opcional) Procesar previamente y limpiar los datos.

    una.  Procesar previamente y limpiar los datos en el Bloc de notas IPython, acceso a los datos de Azure BLOB.

    b.  Transformar datos limpiar formato tabular, si es necesario.

    c.  Guardar datos en archivos de VM local (Bloc de notas de IPython se ejecuta en VM, consulte unidades VM unidades locales).

4.  Cargar datos en el contenedor predeterminado del clúster Hadoop seleccionado en el paso 2.

5.  Cargar datos de la base de datos de sección en el clúster de Azure HDInsight Hadoop.

    una.  Inicie sesión en el nodo principal del clúster Hadoop

    b.  Abra la línea de comandos de Hadoop.

    c.  Escriba el directorio de raíz de la sección comando `cd %hive_home%\bin` en línea de comandos de Hadoop.

    d.  Ejecutar las consultas de la sección para crear tablas y base de datos y cargar los datos de almacenamiento de blobs a las tablas de la sección.

    > [AZURE.NOTE] Si los datos están grandes, los usuarios pueden crear la tabla subárbol con particiones. A continuación, los usuarios pueden utilizar un `for` en Hadoop línea de comandos en el nodo principal para cargar los datos en la tabla subárbol dividida por partición de bucle.

6.  Explorar datos y crear características según sea necesario en línea de comandos de Hadoop. Tenga en cuenta que las características no es necesario materializar en las tablas de base de datos. Solo tenga en cuenta la consulta necesaria para crearlos.

    una.  Inicie sesión en el nodo principal del clúster Hadoop

    b.  Abra la línea de comandos de Hadoop.

    c.  Escriba el directorio de raíz de la sección comando `cd %hive_home%\bin` en línea de comandos de Hadoop.

    d.  Ejecutar las consultas de la sección en línea de comandos de Hadoop en el nodo principal del clúster Hadoop para explorar los datos y crear características según sea necesario.

7.  Si es necesario o lo desea, muestra los datos para que quepa en Azure máquina aprendizaje Studio.

8.  Inicie sesión en el [equipo Azure Studio de aprendizaje](https://studio.azureml.net/).

9. Leer los datos directamente desde la `Hive Queries` usando los [Datos de importación] [ import-data] módulo. Pegue la consulta es necesaria que extrae campos, crea características y ejemplos de datos si es necesario directamente en los [Datos de importación] [ import-data] consulta.

10. Aprendizaje de máquina Azure simple experimentar flujo comenzando con el conjunto de datos cargado.

## <a name="decisiontree"></a>Árbol de decisión de selección de escenario
------------------------

En el diagrama siguiente se resume los escenarios descritos anteriormente, el proceso de análisis avanzadas y opciones de tecnología realizadas que llevan a cada uno de los casos detallados. Tenga en cuenta que pueden tardar procesamiento de datos, obtener información detallada, ingeniería de la característica y muestreo colocar en método/entorno: en el origen, intermedio, o entornos de destino y puede continuar repetidamente según sea necesario. El diagrama solo sirve como una ilustración de algunos de los flujos de posibles y no proporciona una enumeración exhaustiva.

![Escenarios de ejemplo DS proceso tutorial][8]

### <a name="advanced-analytics-in-action-examples"></a>Análisis avanzados en acción ejemplos

Para llevar a cabo los tutoriales de aprendizaje del equipo de Azure que usan el proceso de análisis avanzadas y tecnología con conjuntos de datos públicos, vea:


* [Proceso de ciencias de datos de equipo en acción: uso de SQL Server](machine-learning-data-science-process-sql-walkthrough.md).
* [Proceso de ciencias de datos de equipo en acción: usar clústeres HDInsight Hadoop](machine-learning-data-science-process-hive-walkthrough.md).


[1]: ./media/machine-learning-data-science-plan-sample-scenarios/dsp-plan-small-in-aml.png
[2]: ./media/machine-learning-data-science-plan-sample-scenarios/dsp-plan-local-with-processing.png
[3]: ./media/machine-learning-data-science-plan-sample-scenarios/dsp-plan-local-large.png
[4]: ./media/machine-learning-data-science-plan-sample-scenarios/dsp-plan-local-to-db.png
[5]: ./media/machine-learning-data-science-plan-sample-scenarios/dsp-plan-large-to-db.png
[6]: ./media/machine-learning-data-science-plan-sample-scenarios/dsp-plan-db-to-db.png
[7]: ./media/machine-learning-data-science-plan-sample-scenarios/dsp-plan-attach-db.png
[8]: ./media/machine-learning-data-science-plan-sample-scenarios/dsp-plan-sample-scenarios.png
[9]: ./media/machine-learning-data-science-plan-sample-scenarios/dsp-plan-local-to-hive.png


<!-- Module References -->
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
