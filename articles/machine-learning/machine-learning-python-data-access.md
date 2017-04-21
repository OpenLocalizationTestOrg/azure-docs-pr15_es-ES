<properties 
    pageTitle="Obtener acceso a los conjuntos de datos con una biblioteca de Python de aprendizaje de máquina cliente | Microsoft Azure" 
    description="Instalar y usar la biblioteca de cliente de Python para acceder y administrar de forma segura datos de Azure el aprendizaje de un entorno local de Python." 
    services="machine-learning" 
    documentationCenter="python" 
    authors="bradsev" 
    manager="jhubbard" 
    editor="cgronlun"/>

<tags 
    ms.service="machine-learning" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/12/2016" 
    ms.author="huvalo;bradsev" />


#<a name="access-datasets-with-python-using-the-azure-machine-learning-python-client-library"></a>Conjuntos de datos de Access con Python mediante la biblioteca de cliente de Python de aprendizaje del equipo de Azure 

La vista previa de la biblioteca de Python de aprendizaje de Microsoft Azure máquina cliente puede habilitar el acceso seguro a los conjuntos de datos de Azure el aprendizaje de un entorno local de Python y permite la creación y administración de conjuntos de datos en un área de trabajo.

Este tema proporciona instrucciones sobre cómo:

* instalar la biblioteca de cliente de Python de aprendizaje de equipo 
* acceso y cargar conjuntos de datos, incluidas las instrucciones sobre cómo obtener autorización para tener acceso a los conjuntos de datos de Azure el aprendizaje del entorno local de Python
*  obtener acceso a los conjuntos de datos intermedio de experimentación
*  usar la biblioteca de cliente de Python para enumerar los conjuntos de datos, acceso a los metadatos, leer el contenido de un conjunto de datos, crear nuevos conjuntos de datos y actualizar conjuntos de datos existentes

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

 
##<a name="prerequisites"></a>Requisitos previos

La biblioteca de cliente de Python se ha probado en los siguientes entornos:

 - Windows, Mac y Linux
 - Python 2.7, 3.3 y 3.4

Tiene una dependencia en los paquetes siguientes:

 - solicitudes
 - Python dateutil
 - pandas

Se recomienda utilizar una distribución de Python como [Anaconda](http://continuum.io/downloads#all) o [cubierta](https://store.enthought.com/downloads/), que vienen con Python, IPython y los paquetes de tres enumerado anteriormente instalada. Aunque no es estrictamente necesaria IPython, es un excelente entorno para manipular y visualizar datos de forma interactiva.


###<a name="installation"></a>Cómo instalar la biblioteca de cliente de Python de aprendizaje del equipo de Azure

La biblioteca de cliente de Azure máquina aprendizaje Python también debe estar instalada para completar las tareas descritas en este tema. Está disponible desde el [Índice de paquete de Python](https://pypi.python.org/pypi/azureml). Para instalarlo en su entorno de Python, ejecute el comando siguiente desde su entorno de Python local:

    pip install azureml

Como alternativa, puede descargar e instalar desde los orígenes de [github](https://github.com/Azure/Azure-MachineLearning-ClientLibrary-Python).

    python setup.py install

Si tiene git instalado en su equipo, puede usar puntos para instalar directamente desde el repositorio de git:

    pip install git+https://github.com/Azure/Azure-MachineLearning-ClientLibrary-Python.git


##<a name="datasetAccess"></a>Utilizar fragmentos de código de Studio para tener acceso a los conjuntos de datos

La biblioteca de cliente de Python proporciona puede obtener acceso a los conjuntos de datos existentes de experimentación que se han ejecutado.

Desde la interfaz de web Studio, puede generar fragmentos de código que incluyen toda la información necesaria para descargar y deserializar conjuntos de datos como objetos Pandas DataFrame en el equipo de ubicación.

### <a name="security"></a>Seguridad de acceso a datos

Los fragmentos de código proporcionados por Studio para usar con la biblioteca de cliente de Python incluye el identificador del área de trabajo y la autorización de token. Estas proporcionan acceso completo a su área de trabajo y deben estar protegidas, como una contraseña.

Por motivos de seguridad, la funcionalidad de fragmento de código solo está disponible para los usuarios que tienen su función establecido como **propietario** del área de trabajo. Su rol se muestra en Studio de aprendizaje del equipo de Azure en la página de **usuarios** en **configuración**.

![Seguridad][security]

Si su rol no se establece como **propietario**, puede cualquier solicitud para ser invitado como propietario o pida al propietario del área de trabajo para proporcionarle el fragmento de código.

Para obtener el token de autorización, siga uno de estos procedimientos:



- Solicitar un símbolo de un propietario. Propietarios tienen acceso a sus tokens de autorización de la página de configuración de su área de trabajo en Studio. Seleccione **configuración** en el panel izquierdo y haga clic en **Autorización TOKENS** para ver los símbolos primario y secundarios.  Aunque el principal o los símbolos de autorización secundario pueden usarse en el fragmento de código, se recomienda que los propietarios de compartan solo los tokens autorización secundario.

![](./media/machine-learning-python-data-access/ml-python-access-settings-tokens.png)

- Pida a ser ascendido a rol del propietario.  Para ello, debe primero quite el área de trabajo, a continuación, vuelva a invitar a en él como propietario un propietario actual del área de trabajo.

Una vez han obtenido los desarrolladores el identificador del área de trabajo y la autorización de token, tienen acceso al área de trabajo con el fragmento de código, independientemente de su rol.

Tokens de autorización se administran en la página de **Autorización de TOKENS** en **configuración**. Puede volver a generar ellos, pero este procedimiento le revoca acceso al token anterior.

### <a name="accessingDatasets"></a>Conjuntos de datos de Access desde una aplicación de Python local

1. Studio de aprendizaje de máquina, haga clic en **conjuntos de datos** en la barra de navegación de la izquierda.

2. Seleccione el conjunto de datos que le gustaría tener acceso. Puede seleccionar cualquiera de los conjuntos de datos de la lista **Mis conjuntos de datos** o de la lista de **ejemplos** .

3. En la barra de herramientas de la parte inferior, haga clic en **Generar código de acceso a datos**. Si los datos están en un formato compatible con la biblioteca de cliente de Python, este botón está deshabilitado.

    ![Conjuntos de datos][datasets]

4. Seleccione el fragmento de código de la ventana que aparece y copia al Portapapeles.

    ![Código de acceso][dataset-access-code]

5. Pegue el código en el Bloc de notas de la aplicación de Python local.

    ![Bloc de notas][ipython-dataset]

## <a name="accessingIntermediateDatasets"></a>Acceso a conjuntos de datos intermedio de experimentación de aprendizaje de equipo

Después de ejecuta un ensayo en máquina aprendizaje Studio, es posible tener acceso a los conjuntos de datos intermedio de los nodos de salida de módulos. Conjuntos de datos intermedios son los datos que se ha creado y utilizada para pasos intermedios cuando se ha ejecutado una herramienta de modelo.

Pueden tener acceso a los conjuntos de datos intermedio como el formato de datos es compatible con la biblioteca de cliente de Python.

Se admiten los siguientes formatos (constantes para estos están en la `azureml.DataTypeIds` clase):

 - Texto simple
 - GenericCSV
 - GenericTSV
 - GenericCSVNoHeader
 - GenericTSVNoHeader

Para determinar el formato, mantener el mouse sobre un nodo de salida de módulo. Se muestra junto con el nombre de nodo, en información sobre herramientas.

Algunos de los módulos, como la [división] [ split] módulo de salida a un formato denominado `Dataset`, que no es compatible con la biblioteca de cliente de Python.

![Formato de conjunto de datos][dataset-format]

Debe usar un módulo de conversión, como [convertir a CSV][convert-to-csv], para obtener un resultado en un formato compatible.

![Formato de GenericCSV][csv-format]

Pasos siguientes, muestra un ejemplo que crea un ensayo, se ejecuta y tiene acceso el conjunto de datos intermedio.

1. Crear un nuevo ensayo.

2. Insertar un módulo de **conjunto de datos para adultos censo de ingresos binario clasificación** .

3. Insertar una [división] [ split] módulo y conectarse a su entrada en el resultado del módulo de conjunto de datos.

4. Insertar un [convertir a CSV] [ convert-to-csv] módulo y conecte su entrada a uno de la [división] [ split] módulo salidas.

5. Guardar el ensayo, ejecutarlo y espere a que finalice la ejecución.

6. Haga clic en el nodo de salida en [convertir a CSV] [ convert-to-csv] módulo.

7. Cuando aparezca el menú contextual, seleccione **Generar código de acceso de datos**.

    ![Menú contextual][experiment]

8. Seleccione el fragmento de código y copiarlo en el Portapapeles de la ventana que aparece.

    ![Código de acceso][intermediate-dataset-access-code]

9. Pegue el código en el Bloc de notas.

    ![Bloc de notas][ipython-intermediate-dataset]

10. Puede visualizar los datos mediante matplotlib. Esto se muestra en un histograma para la columna edad:

    ![Histograma][ipython-histogram]


##<a name="clientApis"></a>Usar la biblioteca de cliente de Python de aprendizaje del equipo para obtener acceso, leer, crear y administrar conjuntos de datos

### <a name="workspace"></a>Área de trabajo

El área de trabajo es el punto de entrada para la biblioteca de cliente de Python. Proporcionar la `Workspace` clase con su identificador de área de trabajo y la autorización de token para crear una instancia:

    ws = Workspace(workspace_id='4c29e1adeba2e5a7cbeb0e4f4adfb4df',
                   authorization_token='f4f3ade2c6aefdb1afb043cd8bcf3daf')


### <a name="enumerate-datasets"></a>Enumere los conjuntos de datos

Para enumerar todos los conjuntos de datos en un área de trabajo determinado:

    for ds in ws.datasets:
        print(ds.name)

Para enumerar sólo los conjuntos de datos creados por el usuario:

    for ds in ws.user_datasets:
        print(ds.name)

Para enumerar sólo los conjuntos de datos de ejemplo:

    for ds in ws.example_datasets:
        print(ds.name)

Puede tener acceso a un conjunto de datos por nombre (que distingue mayúsculas de minúsculas):

    ds = ws.datasets['my dataset name']

O bien, puede tener acceso al índice:

    ds = ws.datasets[0]


### <a name="metadata"></a>Metadatos

Conjuntos de datos tienen metadatos, además de contenido. (Intermedios conjuntos de datos son una excepción a esta regla y no tiene los metadatos).

Algunos valores de metadatos se asignan por el usuario en tiempo de creación:

    print(ds.name)
    print(ds.description)
    print(ds.family_id)
    print(ds.data_type_id)

Otros son valores asignados mediante m de Azure:

    print(ds.id)
    print(ds.created_date)
    print(ds.size)

Consulte la `SourceDataset` clase para obtener más información sobre los metadatos disponibles.


### <a name="read-contents"></a>Leer contenido

Los fragmentos de código máquina aprendizaje Studio proporcionados automáticamente descarguen y deserializar el conjunto de datos a un objeto de Pandas DataFrame. Esto se hace con los `to_dataframe` método:

    frame = ds.to_dataframe()

Si prefiere descargar los datos sin formato y realizar ésta usted mismo, es una opción. En este momento, esta es la única opción para formatos como 'ARFF', no se puede deserializar la biblioteca de cliente de Python.

Para leer el contenido como texto:

    text_data = ds.read_as_text()

Para leer el contenido como binarios:

    binary_data = ds.read_as_binary()

También puede abrir una secuencia al contenido:

    with ds.open() as file:
        binary_data_chunk = file.read(1000)


### <a name="create-a-new-dataset"></a>Crear un nuevo conjunto de datos

La biblioteca de cliente de Python le permite cargar conjuntos de datos desde el programa de Python. Estos conjuntos de datos están disponibles para su uso en el área de trabajo.

Si tiene los datos en un DataFrame Pandas, use el siguiente código:

    from azureml import DataTypeIds

    dataset = ws.datasets.add_from_dataframe(
        dataframe=frame,
        data_type_id=DataTypeIds.GenericCSV,
        name='my new dataset',
        description='my description'
    )

Si los datos ya están en la serie, puede usar:

    from azureml import DataTypeIds

    dataset = ws.datasets.add_from_raw_data(
        raw_data=raw_data,
        data_type_id=DataTypeIds.GenericCSV,
        name='my new dataset',
        description='my description'
    )

La biblioteca de cliente de Python es capaz de serializar un DataFrame Pandas a los siguientes formatos (constantes para estos están en la `azureml.DataTypeIds` clase):

 - Texto simple
 - GenericCSV
 - GenericTSV
 - GenericCSVNoHeader
 - GenericTSVNoHeader


### <a name="update-an-existing-dataset"></a>Actualizar un conjunto de datos existente

Si intenta cargar un nuevo conjunto de datos con un nombre que coincida con un conjunto de datos existente, debe obtener un error de conflicto.

Para actualizar un conjunto de datos existente, debe obtener una referencia al conjunto de datos existente:

    dataset = ws.datasets['existing dataset']

    print(dataset.data_type_id) # 'GenericCSV'
    print(dataset.name)         # 'existing dataset'
    print(dataset.description)  # 'data up to jan 2015'

A continuación, utilice `update_from_dataframe` para serializar y reemplace el contenido del conjunto de datos en Azure:

    dataset = ws.datasets['existing dataset']

    dataset.update_from_dataframe(frame2)

    print(dataset.data_type_id) # 'GenericCSV'
    print(dataset.name)         # 'existing dataset'
    print(dataset.description)  # 'data up to jan 2015'

Si desea serializar los datos a otro formato, especifique un valor para el opcional `data_type_id` parámetro.

    from azureml import DataTypeIds

    dataset = ws.datasets['existing dataset']

    dataset.update_from_dataframe(
        dataframe=frame2,
        data_type_id=DataTypeIds.GenericTSV,
    )

    print(dataset.data_type_id) # 'GenericTSV'
    print(dataset.name)         # 'existing dataset'
    print(dataset.description)  # 'data up to jan 2015'

Opcionalmente, puede establecer una nueva descripción especificando un valor para el `description` parámetro.

    dataset = ws.datasets['existing dataset']

    dataset.update_from_dataframe(
        dataframe=frame2,
        description='data up to feb 2015',
    )

    print(dataset.data_type_id) # 'GenericCSV'
    print(dataset.name)         # 'existing dataset'
    print(dataset.description)  # 'data up to feb 2015'

Opcionalmente, puede establecer un nuevo nombre, especifique un valor para el `name` parámetro. En el futuro, deberá recuperar el conjunto de datos con el nuevo nombre. El código siguiente actualiza los datos, el nombre y la descripción.

    dataset = ws.datasets['existing dataset']

    dataset.update_from_dataframe(
        dataframe=frame2,
        name='existing dataset v2',
        description='data up to feb 2015',
    )

    print(dataset.data_type_id)                    # 'GenericCSV'
    print(dataset.name)                            # 'existing dataset v2'
    print(dataset.description)                     # 'data up to feb 2015'

    print(ws.datasets['existing dataset v2'].name) # 'existing dataset v2'
    print(ws.datasets['existing dataset'].name)    # IndexError

El `data_type_id`, `name` y `description` parámetros son opcionales y su valor anterior de forma predeterminada. La `dataframe` parámetro siempre es necesario.

Si los datos ya están en la serie, use `update_from_raw_data` en lugar de `update_from_dataframe`. Si se pasa simplemente `raw_data` en lugar de `dataframe`, funciona de manera similar.



<!-- Images -->
[security]:./media/machine-learning-python-data-access/security.png
[dataset-format]:./media/machine-learning-python-data-access/dataset-format.png
[csv-format]:./media/machine-learning-python-data-access/csv-format.png
[datasets]:./media/machine-learning-python-data-access/datasets.png
[dataset-access-code]:./media/machine-learning-python-data-access/dataset-access-code.png
[ipython-dataset]:./media/machine-learning-python-data-access/ipython-dataset.png
[experiment]:./media/machine-learning-python-data-access/experiment.png
[intermediate-dataset-access-code]:./media/machine-learning-python-data-access/intermediate-dataset-access-code.png
[ipython-intermediate-dataset]:./media/machine-learning-python-data-access/ipython-intermediate-dataset.png
[ipython-histogram]:./media/machine-learning-python-data-access/ipython-histogram.png


<!-- Module References -->
[convert-to-csv]: https://msdn.microsoft.com/library/azure/faa6ba63-383c-4086-ba58-7abf26b85814/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/
 
