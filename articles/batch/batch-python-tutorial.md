<properties
    pageTitle="Tutorial: empezar a trabajar con el cliente de Azure lote Python | Microsoft Azure"
    description="Obtenga información sobre los conceptos básicos del lote de Azure y cómo desarrollar el servicio por lotes con un escenario simple"
    services="batch"
    documentationCenter="python"
    authors="mmacy"
    manager="timlt"
    editor=""/>

<tags
    ms.service="batch"
    ms.devlang="python"
    ms.topic="hero-article"
    ms.tgt_pltfrm="na"
    ms.workload="big-compute"
    ms.date="09/27/2016"
    ms.author="marsma"/>

# <a name="get-started-with-the-azure-batch-python-client"></a>Empezar a trabajar con el cliente de Azure lote Python

> [AZURE.SELECTOR]
- [.NET](batch-dotnet-get-started.md)
- [Python](batch-python-tutorial.md)

Aprenda los conceptos básicos del [Lote de Azure] [ azure_batch] y el [Lote de Python] [ py_azure_sdk] cliente como veremos una pequeña aplicación lote escrita en Python. Observamos cómo dos uso de secuencias de comandos de muestra el servicio de lote para procesar una carga de trabajo paralelo en máquinas virtuales de Linux en la nube y cómo interactúan con [El almacenamiento de Azure](./../storage/storage-introduction.md) para ensayo de archivo y la recuperación. Podrá obtener un flujo de trabajo de aplicación lote comunes y obtener un conocimiento de base de los componentes principales del lote como trabajos, tareas, grupos y nodos de cálculo.

![Flujo de trabajo de solución de lote (basic)][11]<br/>

## <a name="prerequisites"></a>Requisitos previos

En este artículo se supone que tiene un conocimiento práctico de Python y estar familiarizado con Linux. También se supone que es capaz de satisfacer los requisitos de creación de cuenta se especifican debajo de Azure y los servicios de almacenamiento y por lotes.

### <a name="accounts"></a>Cuentas

- **Cuenta de Azure**: si todavía no tiene una suscripción de Azure, [crear una cuenta gratuita de Azure][azure_free_account].
- **Cuenta de proceso por lotes**: una vez que tenga una suscripción de Azure, [Cree una cuenta de Azure lote](batch-account-create-portal.md).
- **Cuenta de almacenamiento**: consulte [crear una cuenta de almacenamiento](../storage/storage-create-storage-account.md#create-a-storage-account) en [Azure sobre cuentas de almacenamiento](../storage/storage-create-storage-account.md).

### <a name="code-sample"></a>Código de ejemplo

El [ejemplo de código] de Python tutorial[ github_article_samples] uno de los muchos ejemplos de código del lote se encuentra en los [ejemplos del lote de azure] [ github_samples] repositorio de GitHub. Puede descargar todos los ejemplos haciendo clic en **clonar o descarga > descargar ZIP** en la página de inicio de repositorio o haciendo clic en el [azure-lote-ejemplos-master.zip] [ github_samples_zip] vínculo de descarga directa. Una vez que haya extraído el contenido del archivo ZIP, las dos secuencias de comandos para este tutorial se encuentran en la `article_samples` directorio:

`/azure-batch-samples/Python/Batch/article_samples/python_tutorial_client.py`<br/>
`/azure-batch-samples/Python/Batch/article_samples/python_tutorial_task.py`

### <a name="python-environment"></a>Entorno de Python

Para ejecutar la secuencia de comandos de ejemplo *python_tutorial_client.py* en su estación de trabajo, necesita un **intérprete de Python** compatible con la versión **2.7** o **3.3 +**. Se ha probado la secuencia de comandos en Windows y Linux.

### <a name="cryptography-dependencies"></a>dependencias de cifrado

Debe instalar las dependencias para el [cifrado] [ crypto] biblioteca, necesario para que la `azure-batch` y `azure-storage` paquetes de Python. Realice una de las siguientes operaciones correspondiente a su plataforma o hacer referencia a la [instalación de cifrado] [ crypto_install] detalles para obtener más información:

* Ubuntu

    `apt-get update && apt-get install -y build-essential libssl-dev libffi-dev libpython-dev python-dev`

* CentOS

    `yum update && yum install -y gcc openssl-dev libffi-devel python-devel`

* SLES/OpenSUSE

    `zypper ref && zypper -n in libopenssl-dev libffi48-devel python-devel`

* Windows

    `pip install cryptography`

>[AZURE.NOTE] Si instala Python 3.3 + en Linux, use los equivalentes de python3 para las dependencias de Python. Por ejemplo, en Ubuntu:`apt-get update && apt-get install -y build-essential libssl-dev libffi-dev libpython3-dev python3-dev`

### <a name="azure-packages"></a>Paquetes de Azure

A continuación, instale los paquetes de **Azure lote** y **El almacenamiento de Azure** Python. Puede hacerlo con **puntos** y la *requirements.txt* encontrarse aquí:

`/azure-batch-samples/Python/Batch/requirements.txt`

Problema al siguiente comando de **puntos** para instalar los paquetes de lote y almacenamiento:

`pip install -r requirements.txt`

O bien, puede instalar el [lote de azure] [ pypi_batch] y [almacenamiento de azure] [ pypi_storage] Python paquetes manualmente:

`pip install azure-batch`<br/>
`pip install azure-storage`

> [AZURE.TIP] Puede que necesite prefijo sus comandos con `sudo` si está utilizando una cuenta sin privilegios. Por ejemplo, `sudo pip install -r requirements.txt`. Para obtener más información sobre cómo instalar paquetes de Python, vea [Instalar paquetes] [ pypi_install] en readthedocs.io.

## <a name="batch-python-tutorial-code-sample"></a>Ejemplo de código del tutorial Python por lotes

El ejemplo de código del tutorial de Python lote consta de dos secuencias de comandos de Python y algunos archivos de datos.

- **python_tutorial_client.py**: interactúa con los servicios de almacenamiento y lote para ejecutar una carga de trabajo paralelo en nodos de cálculo (máquinas virtuales). La secuencia de comandos de *python_tutorial_client.py* se ejecuta en su estación de trabajo.

- **python_tutorial_task.py**: la secuencia de comandos que se ejecuta en nodos de cálculo en Azure para realizar el trabajo real. En el ejemplo *python_tutorial_task.py* analiza el texto en un archivo descargado desde el almacenamiento de Azure (el archivo de entrada). A continuación, genera un archivo de texto (el archivo de salida) que contiene una lista de las tres palabras principales que aparecen en el archivo de entrada. Después de crear el archivo de salida, *python_tutorial_task.py* carga el archivo en el almacenamiento de Azure. Esto hace que esté disponible para su descarga para el script de cliente que se ejecuta en su estación de trabajo. La secuencia de comandos de *python_tutorial_task.py* se ejecuta en paralelo en varios nodos de cálculo en el servicio de lote.

- **./data/taskdata\*.txt**: estos archivos de tres texto proporcionan la entrada para las tareas que se ejecutan en los nodos de cálculo.

El siguiente diagrama muestra las operaciones principales que se realizan las secuencias de comandos de cliente y tareas. Este flujo de trabajo básico es típico de muchas soluciones de cálculo que se crean con lote. Mientras no muestra todas las características disponibles en el servicio de proceso por lotes, casi todas las situaciones lote incluyen partes de este flujo de trabajo.

![Flujo de trabajo de ejemplo de proceso por lotes][8]<br/>

[**Paso 1.**](#step-1-create-storage-containers) Crear **contenedores** de almacenamiento de blobs de Windows Azure.<br/>
[**Paso 2.**](#step-2-upload-task-script-and-data-files) Cargar archivos de secuencia de comandos y entrada de tarea a contenedores.<br/>
[**Paso 3.**](#step-3-create-batch-pool) Crear un lote del **grupo**.<br/>
  &nbsp;&nbsp;&nbsp;&nbsp;**3a.** El grupo **se ha StartTask** descarga la secuencia de comandos de tarea (python_tutorial_task.py) en los nodos como unirse a la agrupación.<br/>
[**Paso 4.**](#step-4-create-batch-job) Crear un lote del **trabajo**.<br/>
[**Paso 5.**](#step-5-add-tasks-to-job) Agregar **tareas** a la tarea.<br/>
  &nbsp;&nbsp;&nbsp;&nbsp;**5a.** Las tareas se programan para ejecutar en nodos.<br/>
    &nbsp;&nbsp;&nbsp;&nbsp;**5 b.** Cada tarea descarga sus datos de entrada desde el almacenamiento de Azure y luego comienza la ejecución.<br/>
[**Paso 6.**](#step-6-monitor-tasks) Supervisar las tareas.<br/>
  &nbsp;&nbsp;&nbsp;&nbsp;**6a.** Como tareas están completadas, cargan sus datos de salida para el almacenamiento de Azure.<br/>
[**Paso 7.**](#step-7-download-task-output) Descargar los resultados de la tarea de almacenamiento.

Como mencionada no cada lote solución realiza estos pasos exactos y puede incluir muchos más, pero este muestra procesos comunes que se encuentra en una solución por lotes.

## <a name="prepare-client-script"></a>Preparar el script de cliente

Antes de ejecutar el ejemplo, agregue sus credenciales de cuenta lote y almacenamiento a *python_tutorial_client.py*. Si aún no lo ha hecho, abra el archivo en su editor favorito y actualizar las siguientes líneas con sus credenciales.

```python
# Update the Batch and Storage account credential strings below with the values
# unique to your accounts. These are used when constructing connection strings
# for the Batch and Storage client objects.

# Batch account credentials
batch_account_name = "";
batch_account_key  = "";
batch_account_url  = "";

# Storage account credentials
storage_account_name = "";
storage_account_key  = "";
```

Puede encontrar el lote y el almacenamiento de credenciales de su cuenta en el módulo de cuenta de cada servicio en el [portal de Azure][azure_portal]:

![Credenciales en el portal de lotes][9]
![credenciales de almacenamiento en el portal][10]<br/>

En las siguientes secciones, se analizar los pasos que se utiliza las secuencias de comandos para procesar una carga de trabajo en el servicio de lote. Le recomendamos que consulte periódicamente las secuencias de comandos en el editor mientras que trabaja con el resto de este artículo.

Vaya a la siguiente línea en **python_tutorial_client.py** para comenzar con el paso 1:

```python
if __name__ == '__main__':
```

## <a name="step-1-create-storage-containers"></a>Paso 1: Crear contenedores de almacenamiento

![Crear contenedores de almacenamiento de Azure][1]
<br/>

Proceso por lotes incluye compatibilidad integrada para interactuar con el almacenamiento de Azure. Contenedores en su cuenta de almacenamiento le proporcionará los archivos necesarios por las tareas que se ejecutan en su cuenta por lotes. Los contenedores también proporcionan un lugar para almacenar los datos de salida que producen las tareas. Lo primero que hace la secuencia de comandos de *python_tutorial_client.py* es crear tres contenedores de [Almacenamiento de blobs de Windows Azure](../storage/storage-introduction.md#blob-storage):

- **aplicación**: este contenedor guardará la secuencia de comandos de Python ejecutar las tareas, *python_tutorial_task.py*.
- **entrada**: tareas descargarán los archivos de datos para procesar desde el contenedor de *entrada* .
- **resultado**: tareas completar el procesamiento de archivo de entrada, cargará los resultados al contenedor de *salida* .

Para interactuar con una cuenta de almacenamiento y crear contenedores, usamos el [almacenamiento de azure] [ pypi_storage] paquete para crear un [BlockBlobService] [ py_blockblobservice] objeto: "cliente blob". A continuación, creamos tres contenedores en la cuenta de almacenamiento con el cliente de blobs de Windows.

```python
 # Create the blob client, for use in obtaining references to
 # blob storage containers and uploading files to containers.
 blob_client = azureblob.BlockBlobService(
     account_name=_STORAGE_ACCOUNT_NAME,
     account_key=_STORAGE_ACCOUNT_KEY)

 # Use the blob client to create the containers in Azure Storage if they
 # don't yet exist.
 app_container_name = 'application'
 input_container_name = 'input'
 output_container_name = 'output'
 blob_client.create_container(app_container_name, fail_on_exist=False)
 blob_client.create_container(input_container_name, fail_on_exist=False)
 blob_client.create_container(output_container_name, fail_on_exist=False)
```

Una vez que se han creado los contenedores, la aplicación puede cargar los archivos que se usará en las tareas.

> [AZURE.TIP] [Cómo usar el almacenamiento de blobs de Windows Azure de Python](../storage/storage-python-how-to-use-blob-storage.md) ofrece una buena introducción de trabajar con BLOB y contenedores de almacenamiento de Azure. Al empezar a trabajar con el lote debe ser cerca de la parte superior de la lista de lectura.

## <a name="step-2-upload-task-script-and-data-files"></a>Paso 2: Cargar archivos de datos y la secuencia de comandos de tarea

![Cargar archivos de entrada (datos) y aplicación de la tarea en contenedores][2]
<br/>

En la operación de carga de archivo, *python_tutorial_client.py* define primero colecciones de rutas de acceso de archivo de **entrada** y de **aplicación** que ya existen en el equipo local. A continuación, cargará estos archivos a los contenedores que creó en el paso anterior.

```python
 # Paths to the task script. This script will be executed by the tasks that
 # run on the compute nodes.
 application_file_paths = [os.path.realpath('python_tutorial_task.py')]

 # The collection of data files that are to be processed by the tasks.
 input_file_paths = [os.path.realpath('./data/taskdata1.txt'),
                     os.path.realpath('./data/taskdata2.txt'),
                     os.path.realpath('./data/taskdata3.txt')]

 # Upload the application script to Azure Storage. This is the script that
 # will process the data files, and is executed by each of the tasks on the
 # compute nodes.
 application_files = [
     upload_file_to_container(blob_client, app_container_name, file_path)
     for file_path in application_file_paths]

 # Upload the data files. This is the data that will be processed by each of
 # the tasks executed on the compute nodes in the pool.
 input_files = [
     upload_file_to_container(blob_client, input_container_name, file_path)
     for file_path in input_file_paths]
```

Mediante la comprensión de la lista, la `upload_file_to_container` función se llama para cada archivo en las colecciones y dos [ResourceFile] [ py_resource_file] colecciones se rellenan. La `upload_file_to_container` función aparece debajo de:

```
def upload_file_to_container(block_blob_client, container_name, file_path):
    """
    Uploads a local file to an Azure Blob storage container.

    :param block_blob_client: A blob service client.
    :type block_blob_client: `azure.storage.blob.BlockBlobService`
    :param str container_name: The name of the Azure Blob storage container.
    :param str file_path: The local path to the file.
    :rtype: `azure.batch.models.ResourceFile`
    :return: A ResourceFile initialized with a SAS URL appropriate for Batch
    tasks.
    """
    blob_name = os.path.basename(file_path)

    print('Uploading file {} to container [{}]...'.format(file_path,
                                                          container_name))

    block_blob_client.create_blob_from_path(container_name,
                                            blob_name,
                                            file_path)

    sas_token = block_blob_client.generate_blob_shared_access_signature(
        container_name,
        blob_name,
        permission=azureblob.BlobPermissions.READ,
        expiry=datetime.datetime.utcnow() + datetime.timedelta(hours=2))

    sas_url = block_blob_client.make_blob_url(container_name,
                                              blob_name,
                                              sas_token=sas_token)

    return batchmodels.ResourceFile(file_path=blob_name,
                                    blob_source=sas_url)
```

### <a name="resourcefiles"></a>ResourceFiles

Un [ResourceFile] [ py_resource_file] proporciona tareas en el lote con la dirección URL a un archivo de almacenamiento de Azure que se descarga en un nodo de cálculo antes de que se ejecute esa tarea. La [ResourceFile][py_resource_file]. propiedad de **blob_source** especifica la dirección URL completa del archivo, ya que existe en el almacenamiento de Azure. La dirección URL también puede incluir una firma de acceso compartido (SA) que proporciona acceso seguro para el archivo. La mayoría de tipos de tareas en lote incluyan una propiedad *ResourceFiles* , incluidos:

- [CloudTask][py_task]
- [Se ha StartTask][py_starttask]
- [JobPreparationTask][py_jobpreptask]
- [JobReleaseTask][py_jobreltask]

En este ejemplo no utiliza los tipos de tareas JobPreparationTask o JobReleaseTask, pero puede obtener más información acerca de ellos en [tareas de preparación y finalización de trabajo de ejecución en lotes de Azure nodos de cálculo](batch-job-prep-release.md).

### <a name="shared-access-signature-sas"></a>Firma de acceso compartido (SA)

Acceso compartido firmas son cadenas que proporcionan acceso seguro a contenedores y BLOB en el almacenamiento de Azure. La secuencia de comandos de *python_tutorial_client.py* usa ambos blob y contenedor compartido firmas de acceso y muestra cómo obtener estas cadenas de firma acceso compartido desde el servicio de almacenamiento.

- **BLOB compartido firmas de acceso**: usos del grupo se ha StartTask blob acceso compartido firmas cuando descargue los archivos de datos de secuencia de comandos y entrada de tarea de almacenamiento (vea el [paso 3](#step-3-create-batch-pool) ). La `upload_file_to_container` función en *python_tutorial_client.py* contiene el código que obtiene la firma de acceso compartido de cada blob. Para ello, llamando [BlockBlobService.make_blob_url] [ py_make_blob_url] en el módulo de almacenamiento.

- **Firma de contenedor acceso compartido**: como cada tarea finalice su trabajo en el nodo de cálculo, carga su archivo de salida para el contenedor de *salida* en el almacenamiento de Azure. Para ello, *python_tutorial_task.py* utiliza una firma de acceso compartido de contenedor que proporciona acceso de escritura en el contenedor. La `get_container_sas_token` función en *python_tutorial_client.py* Obtiene la firma de acceso compartido del contenedor, que se pasa como un argumento de línea de comandos para las tareas. Paso 5 #, [Agregar tareas a una tarea](#step-5-add-tasks-to-job), describe el uso del contenedor de SA.

> [AZURE.TIP] Consulte la serie de dos partes en firmas de acceso compartido, [parte 1: información sobre el modelo de SA](../storage/storage-dotnet-shared-access-signature-part-1.md) y [parte 2: crear y usar un SA con el servicio de Blob](../storage/storage-dotnet-shared-access-signature-part-2.md), para obtener más información sobre cómo proporcionar acceso seguro a los datos de su cuenta de almacenamiento.

## <a name="step-3-create-batch-pool"></a>Paso 3: Crear grupo por lotes

![Crear un grupo por lotes][3]
<br/>

Un **grupo** de lote es una colección de nodos de cálculo (máquinas virtuales) en los que el lote ejecuta las tareas de la tarea.

Después de que cargue los archivos de datos y la secuencia de comandos de la tarea a la cuenta de almacenamiento, *python_tutorial_client.py* inicia su interacción con el servicio de lote mediante el módulo de lote Python. Para hacerlo, un [BatchServiceClient] [ py_batchserviceclient] se crea:

```python
 # Create a Batch service client. We'll now be interacting with the Batch
 # service in addition to Storage.
 credentials = batchauth.SharedKeyCredentials(_BATCH_ACCOUNT_NAME,
                                              _BATCH_ACCOUNT_KEY)

 batch_client = batch.BatchServiceClient(
     credentials,
     base_url=_BATCH_ACCOUNT_URL)
```

A continuación, se crea un grupo de nodos de cálculo en la cuenta de proceso por lotes con una llamada a `create_pool`.

```python
def create_pool(batch_service_client, pool_id,
                resource_files, publisher, offer, sku):
    """
    Creates a pool of compute nodes with the specified OS settings.

    :param batch_service_client: A Batch service client.
    :type batch_service_client: `azure.batch.BatchServiceClient`
    :param str pool_id: An ID for the new pool.
    :param list resource_files: A collection of resource files for the pool's
    start task.
    :param str publisher: Marketplace image publisher
    :param str offer: Marketplace image offer
    :param str sku: Marketplace image sku
    """
    print('Creating pool [{}]...'.format(pool_id))

    # Create a new pool of Linux compute nodes using an Azure Virtual Machines
    # Marketplace image. For more information about creating pools of Linux
    # nodes, see:
    # https://azure.microsoft.com/documentation/articles/batch-linux-nodes/

    # Specify the commands for the pool's start task. The start task is run
    # on each node as it joins the pool, and when it's rebooted or re-imaged.
    # We use the start task to prep the node for running our task script.
    task_commands = [
        # Copy the python_tutorial_task.py script to the "shared" directory
        # that all tasks that run on the node have access to.
        'cp -r $AZ_BATCH_TASK_WORKING_DIR/* $AZ_BATCH_NODE_SHARED_DIR',
        # Install pip and the dependencies for cryptography
        'apt-get update',
        'apt-get -y install python-pip',
        'apt-get -y install build-essential libssl-dev libffi-dev python-dev',
        # Install the azure-storage module so that the task script can access
        # Azure Blob storage
        'pip install azure-storage']

    # Get the node agent SKU and image reference for the virtual machine
    # configuration.
    # For more information about the virtual machine configuration, see:
    # https://azure.microsoft.com/documentation/articles/batch-linux-nodes/
    sku_to_use, image_ref_to_use = \
        common.helpers.select_latest_verified_vm_image_with_node_agent_sku(
            batch_service_client, publisher, offer, sku)

    new_pool = batch.models.PoolAddParameter(
        id=pool_id,
        virtual_machine_configuration=batchmodels.VirtualMachineConfiguration(
            image_reference=image_ref_to_use,
            node_agent_sku_id=sku_to_use),
        vm_size=_POOL_VM_SIZE,
        target_dedicated=_POOL_NODE_COUNT,
        start_task=batch.models.StartTask(
            command_line=
            common.helpers.wrap_commands_in_shell('linux', task_commands),
            run_elevated=True,
            wait_for_success=True,
            resource_files=resource_files),
        )

    try:
        batch_service_client.pool.add(new_pool)
    except batchmodels.batch_error.BatchErrorException as err:
        print_batch_exception(err)
        raise
```

Al crear un grupo, defina un [PoolAddParameter] [ py_pooladdparam] que especifica varias propiedades para el grupo:

- **Identificador** del grupo (*id* - obligatorio)<p/>Como ocurre con la mayoría de entidades de proceso por lotes, su nuevo grupo debe tener un identificador único dentro de su cuenta de lote. El código hace referencia a este grupo con su identificador y es cómo identificar el grupo en el [portal]de Azure[azure_portal].

- **Número de nodos de cálculo** (*target_dedicated* - obligatorio)<p/>Esta propiedad especifica cuántas VM se deberían implementar en el grupo. Es importante que tenga en cuenta que todas las cuentas de proceso por lotes de predeterminada **cuota** que limita el número de **núcleos** (y por tanto, nodos de cálculo) en una cuenta de proceso por lotes. Puede encontrar las cuotas predeterminadas e instrucciones sobre cómo [aumentar una cuota](batch-quota-limit.md#increase-a-quota) (por ejemplo, el número máximo de núcleos en su cuenta por lotes) en [las cuotas y límites para el servicio del lote de Azure](batch-quota-limit.md). Si siente que le pregunta "¿por qué no mi grupo de llegar a más de X nodos?" Esta cuota core puede ser la causa.

- **Sistema operativo** de nodos (*virtual_machine_configuration* **o** *cloud_service_configuration* - obligatorio)<p/>En *python_tutorial_client.py*, se crea un grupo de nodos de Linux mediante un [VirtualMachineConfiguration][py_vm_config]. La `select_latest_verified_vm_image_with_node_agent_sku` de función en `common.helpers` simplifica el trabajo con [Máquinas virtuales de Azure Marketplace] [ vm_marketplace] imágenes. Vea [Linux aprovisionar nodos de cálculo en grupos de Azure lote](batch-linux-nodes.md) para obtener más información sobre cómo usar imágenes del catálogo de soluciones.

- **Tamaño de los nodos de cálculo** (*vm_size* - obligatorio)<p/>Puesto que estamos especificando nodos Linux para nuestro [VirtualMachineConfiguration][py_vm_config], se especifica un tamaño de memoria virtual (`STANDARD_A1` en este ejemplo) de [tamaños de máquinas virtuales de Azure](../virtual-machines/virtual-machines-linux-sizes.md). De nuevo, vea [aprovisionar Linux nodos de cálculo en grupos de Azure lote](batch-linux-nodes.md) para obtener más información.

- **Iniciar tarea** (*start_task* - no es necesario)<p/>Junto con la encima de propiedades de nodo físico, también puede especificar un [se ha StartTask] [ py_starttask] para el grupo (no es necesario). El se ha StartTask se ejecuta en cada nodo como nodo une el grupo y, a continuación, cada vez que se reinicia un nodo. El se ha StartTask es especialmente útil para preparar los nodos de cálculo para la ejecución de tareas, como instalar las aplicaciones que se ejecutan las tareas.<p/>En este ejemplo de aplicación, la se ha StartTask copia los archivos que descarga de almacenamiento (que se especifican con **resource_files** propiedad el se de ha StartTask) la se ha StartTask *directorio de trabajo* en el directorio *compartido* que pueden tener acceso todas las tareas que se ejecuta en el nodo. Básicamente, Esto copiará `python_tutorial_task.py` al directorio compartido en cada nodo mientras el nodo une a la agrupación, para que las tareas que se ejecutan en el nodo puede obtener acceso a él.

Quizás observe la llamada a la `wrap_commands_in_shell` función auxiliar. Esta función toma una colección de comandos independientes y crea una línea de comandos adecuado para la propiedad de línea de comandos de la tarea.

También notables del fragmento de código anterior es el uso de dos variables de entorno en la propiedad de **command_line** de la se ha StartTask: `AZ_BATCH_TASK_WORKING_DIR` y `AZ_BATCH_NODE_SHARED_DIR`. Cada nodo de cálculo dentro de un grupo de lote se configura automáticamente con varias variables de entorno específicas de lote. Todos los procesos que se ejecutan una tarea tiene acceso a estas variables de entorno.

> [AZURE.TIP] Para obtener más información acerca de las variables de entorno que están disponibles en los nodos de cálculo en un grupo por lotes, así como información sobre los directorios de trabajo de tarea, consulte **configuración de entorno para las tareas** y **directorios y archivos** en la [información general de las características del lote de Azure](batch-api-basics.md).

## <a name="step-4-create-batch-job"></a>Paso 4: Crear trabajo por lotes

![Crear tarea por lotes][4]<br/>

**Trabajo** por lotes es un conjunto de tareas y está asociado con un grupo de nodos de cálculo. Las tareas de un trabajo se ejecutan en los nodos de cálculo del grupo asociado.

Puede usar un trabajo no solo para organizar y seguimiento de tareas en las cargas de trabajo relacionadas, sino también para imponer ciertas restricciones, como el tiempo de ejecución máximo para el trabajo (y por extensión, sus tareas) y la prioridad en relación con otros trabajos en la cuenta de proceso de trabajo. En este ejemplo, sin embargo, el trabajo sólo está asociado con el grupo que se creó en el paso 3 de #. No se configuran propiedades adicionales.

Todas las tareas por lotes están asociadas a un grupo específico. Esta asociación indica qué nodos ejecutan las tareas del proyecto. Especificar el grupo mediante la [PoolInformation] [ py_poolinfo] propiedad, como se muestra en el fragmento de código siguiente.

```python
def create_job(batch_service_client, job_id, pool_id):
    """
    Creates a job with the specified ID, associated with the specified pool.

    :param batch_service_client: A Batch service client.
    :type batch_service_client: `azure.batch.BatchServiceClient`
    :param str job_id: The ID for the job.
    :param str pool_id: The ID for the pool.
    """
    print('Creating job [{}]...'.format(job_id))

    job = batch.models.JobAddParameter(
        job_id,
        batch.models.PoolInformation(pool_id=pool_id))

    try:
        batch_service_client.job.add(job)
    except batchmodels.batch_error.BatchErrorException as err:
        print_batch_exception(err)
        raise
```

Ahora que se ha creado un trabajo, las tareas se agregan a realizar el trabajo.

## <a name="step-5-add-tasks-to-job"></a>Paso 5: Agregar tareas a la tarea

![Agregar tareas a la tarea][5]<br/>
*(1) tareas se agregan a la tarea, (2) las tareas se programan para que se ejecute en los nodos y (3) las tareas descargar los archivos de datos para procesar*

Lote las **tareas** son las unidades de trabajo que se ejecutan en los nodos de cálculo. Una tarea tiene una línea de comandos y ejecuta los scripts o ejecutables que especifique en la línea de comandos.

Para llevar a cabo trabajo, las tareas deben agregarse a una tarea. Cada [CloudTask] [ py_task] está configurado con una propiedad de la línea de comandos y [ResourceFiles] [ py_resource_file] (como las se ha StartTask del grupo) que la tarea se descarga al nodo antes de la línea de comandos se ejecuta automáticamente. En el ejemplo, cada tarea procesa un solo archivo. Por lo tanto, su colección de ResourceFiles contiene un solo elemento.

```python
def add_tasks(batch_service_client, job_id, input_files,
              output_container_name, output_container_sas_token):
    """
    Adds a task for each input file in the collection to the specified job.

    :param batch_service_client: A Batch service client.
    :type batch_service_client: `azure.batch.BatchServiceClient`
    :param str job_id: The ID of the job to which to add the tasks.
    :param list input_files: A collection of input files. One task will be
     created for each input file.
    :param output_container_name: The ID of an Azure Blob storage container to
    which the tasks will upload their results.
    :param output_container_sas_token: A SAS token granting write access to
    the specified Azure Blob storage container.
    """

    print('Adding {} tasks to job [{}]...'.format(len(input_files), job_id))

    tasks = list()

    for input_file in input_files:

        command = ['python $AZ_BATCH_NODE_SHARED_DIR/python_tutorial_task.py '
                   '--filepath {} --numwords {} --storageaccount {} '
                   '--storagecontainer {} --sastoken "{}"'.format(
                    input_file.file_path,
                    '3',
                    _STORAGE_ACCOUNT_NAME,
                    output_container_name,
                    output_container_sas_token)]

        tasks.append(batch.models.TaskAddParameter(
                'topNtask{}'.format(input_files.index(input_file)),
                wrap_commands_in_shell('linux', command),
                resource_files=[input_file]
                )
        )

    batch_service_client.task.add_collection(job_id, tasks)
```

> [AZURE.IMPORTANT] Cuando tengan acceso a las variables de entorno como `$AZ_BATCH_NODE_SHARED_DIR` o ejecutar una aplicación no se encuentra en el nodo `PATH`, líneas de comandos de la tarea debe invocar el shell explícitamente, como con `/bin/sh -c MyTaskApplication $MY_ENV_VAR`. Este requisito es innecesario si las tareas ejecutan una aplicación en el nodo `PATH` y hacen referencia a las variables de entorno.

Dentro de la `for` bucle del fragmento de código anterior, puede ver que la línea de comandos de la tarea se construye con cinco argumentos de línea de comandos que se pasan a *python_tutorial_task.py*:

1. **ruta del archivo**: esta es la ruta de acceso local al archivo tal y como existe en el nodo. Cuando la ResourceFile objeto en `upload_file_to_container` se creó en el paso 2 anterior, se utiliza el nombre de archivo para esta propiedad (la `file_path` parámetro del constructor ResourceFile). Esto indica que el archivo puede encontrarse en el mismo directorio en el nodo que *python_tutorial_task.py*.

2. **numWords**: las palabras *N* superiores deben escribirse en el archivo de salida.

3. **storageaccount**: el nombre de la cuenta de almacenamiento que posee el contenedor a la que debe cargarse el resultado de la tarea.

4. **storagecontainer**: el nombre del contenedor de almacenamiento a la que se deben cargar los archivos de salida.

5. **sastoken**: la firma de acceso compartido (SA) que proporciona acceso de escritura en el contenedor de **salida** en el almacenamiento de Azure. La secuencia de comandos de *python_tutorial_task.py* usa esta firma acceso compartido cuando crea su referencia BlockBlobService. Esto proporciona acceso de escritura al contenedor sin necesidad de una tecla de acceso para la cuenta de almacenamiento.

```python
# NOTE: Taken from python_tutorial_task.py

# Create the blob client using the container's SAS token.
# This allows us to create a client that provides write
# access only to the container.
blob_client = azureblob.BlockBlobService(account_name=args.storageaccount,
                                         sas_token=args.sastoken)
```

## <a name="step-6-monitor-tasks"></a>Paso 6: Tareas de Monitor

![Supervisar tareas][6]<br/>
*La secuencia de comandos (1) supervisa las tareas de estado de finalización y, a continuación, (2) las tareas cargar los datos de resultado en el almacenamiento de Azure*

Cuando las tareas se agregan a una tarea, automáticamente están en la cola y programadas para que se ejecute en los nodos de cálculo en el grupo asociado con el trabajo. Según la configuración que especifique, por lotes controladores de cola de todas las tareas, programación, Reintentar y otras tareas de administración de tareas para usted.

Existen varios métodos para supervisar la ejecución de la tarea. La `wait_for_tasks_to_complete` función en *python_tutorial_client.py* proporciona un ejemplo sencillo de supervisión de tareas para un estado determinado, en este caso, el [completado] [ py_taskstate] estado.

```python
def wait_for_tasks_to_complete(batch_service_client, job_id, timeout):
    """
    Returns when all tasks in the specified job reach the Completed state.

    :param batch_service_client: A Batch service client.
    :type batch_service_client: `azure.batch.BatchServiceClient`
    :param str job_id: The id of the job whose tasks should be to monitored.
    :param timedelta timeout: The duration to wait for task completion. If all
    tasks in the specified job do not reach Completed state within this time
    period, an exception will be raised.
    """
    timeout_expiration = datetime.datetime.now() + timeout

    print("Monitoring all tasks for 'Completed' state, timeout in {}..."
          .format(timeout), end='')

    while datetime.datetime.now() < timeout_expiration:
        print('.', end='')
        sys.stdout.flush()
        tasks = batch_service_client.task.list(job_id)

        incomplete_tasks = [task for task in tasks if
                            task.state != batchmodels.TaskState.completed]
        if not incomplete_tasks:
            print()
            return True
        else:
            time.sleep(1)

    print()
    raise RuntimeError("ERROR: Tasks did not reach 'Completed' state within "
                       "timeout period of " + str(timeout))
```

## <a name="step-7-download-task-output"></a>Paso 7: Descargar el resultado de la tarea

![Descargar los resultados de la tarea de almacenamiento][7]<br/>

Ahora que se haya completado el trabajo, el resultado de las tareas puede descargarse desde el almacenamiento de Azure. Esto se hace con una llamada a `download_blobs_from_container` en *python_tutorial_client.py*:

```python
def download_blobs_from_container(block_blob_client,
                                  container_name, directory_path):
    """
    Downloads all blobs from the specified Azure Blob storage container.

    :param block_blob_client: A blob service client.
    :type block_blob_client: `azure.storage.blob.BlockBlobService`
    :param container_name: The Azure Blob storage container from which to
     download files.
    :param directory_path: The local directory to which to download the files.
    """
    print('Downloading all files from container [{}]...'.format(
        container_name))

    container_blobs = block_blob_client.list_blobs(container_name)

    for blob in container_blobs.items:
        destination_file_path = os.path.join(directory_path, blob.name)

        block_blob_client.get_blob_to_path(container_name,
                                           blob.name,
                                           destination_file_path)

        print('  Downloaded blob [{}] from container [{}] to {}'.format(
            blob.name,
            container_name,
            destination_file_path))

    print('  Download complete!')
```

> [AZURE.NOTE] La llamada a `download_blobs_from_container` en *python_tutorial_client.py* especifica que se deben descargar los archivos al directorio de inicio. No dude en modificar esta ubicación de salida.

## <a name="step-8-delete-containers"></a>Paso 8: Contenedores de eliminar

Dado que se cargan de datos que residen en el almacenamiento de Azure, siempre es una buena idea para quitar cualquier BLOB que ya no es necesarias para los procesos. En *python_tutorial_client.py*, esto se hace con tres llamadas a [BlockBlobService.delete_container][py_delete_container]:

```
# Clean up storage resources
print('Deleting containers...')
blob_client.delete_container(app_container_name)
blob_client.delete_container(input_container_name)
blob_client.delete_container(output_container_name)
```

## <a name="step-9-delete-the-job-and-the-pool"></a>Paso 9: Eliminar el trabajo y la agrupación

En el paso final, deberá eliminar la tarea y el grupo que se crearon la secuencia de comandos de *python_tutorial_client.py* . Aunque no se cargan para trabajos y tareas, *son* de nodos de cálculo. Por lo tanto, se recomienda asignar nodos sólo según sea necesario. Eliminar grupos sin usar puede ser parte del proceso de mantenimiento.

De BatchServiceClient [JobOperations] [ py_job] y [PoolOperations] [ py_pool] tienen métodos de eliminación correspondientes, que se denominan si Confirmar eliminación:

```python
# Clean up Batch resources (if the user so chooses).
if query_yes_no('Delete job?') == 'yes':
    batch_client.job.delete(_JOB_ID)

if query_yes_no('Delete pool?') == 'yes':
    batch_client.pool.delete(_POOL_ID)
```

> [AZURE.IMPORTANT] Tenga en cuenta que se cargará para recursos de cálculo--eliminar grupos sin usar minimizará costo. Además, tenga en cuenta que la eliminación de un grupo elimina todos los nodos de cálculo dentro de ese grupo, y que todos los datos en los nodos será no recuperables después de elimina el grupo.

## <a name="run-the-sample-script"></a>Ejecutar la secuencia de comandos

Cuando se ejecuta la secuencia de comandos de *python_tutorial_client.py* desde el tutorial [muestra código][github_article_samples], el resultado de la consola es similar a la siguiente. Hay una pausa en `Monitoring all tasks for 'Completed' state, timeout in 0:20:00...` mientras se crean los nodos de cálculo del grupo, iniciado, y se ejecutan los comandos de la tarea de inicio del grupo. Usar el [portal de Azure] [ azure_portal] para supervisar la agrupación, calcular nodos, trabajo y tareas durante y después de la ejecución. Usar el [portal de Azure] [ azure_portal] o el [Explorador de Microsoft Azure almacenamiento] [ storage_explorer] para ver los recursos de almacenamiento (contenedores y BLOB) creados por la aplicación.

>[AZURE.TIP] Ejecutar la secuencia de comandos de *python_tutorial_client.py* desde el `azure-batch-samples/Python/Batch/article_samples` directorio. Utiliza una ruta de acceso relativa para el `common.helpers` importación del módulo, por lo que es posible que vea `ImportError: No module named 'common'` si no se ejecuten el la secuencia de comandos de este directorio.

Tiempo de ejecución típica es **aproximadamente 5-7 minutos** cuando se ejecuta el ejemplo en su configuración predeterminada.

```
Sample start: 2016-05-20 22:47:10

Uploading file /home/user/py_tutorial/python_tutorial_task.py to container [application]...
Uploading file /home/user/py_tutorial/data/taskdata1.txt to container [input]...
Uploading file /home/user/py_tutorial/data/taskdata2.txt to container [input]...
Uploading file /home/user/py_tutorial/data/taskdata3.txt to container [input]...
Creating pool [PythonTutorialPool]...
Creating job [PythonTutorialJob]...
Adding 3 tasks to job [PythonTutorialJob]...
Monitoring all tasks for 'Completed' state, timeout in 0:20:00..........................................................................
  Success! All tasks reached the 'Completed' state within the specified timeout period.
Downloading all files from container [output]...
  Downloaded blob [taskdata1_OUTPUT.txt] from container [output] to /home/user/taskdata1_OUTPUT.txt
  Downloaded blob [taskdata2_OUTPUT.txt] from container [output] to /home/user/taskdata2_OUTPUT.txt
  Downloaded blob [taskdata3_OUTPUT.txt] from container [output] to /home/user/taskdata3_OUTPUT.txt
  Download complete!
Deleting containers...

Sample end: 2016-05-20 22:53:12
Elapsed time: 0:06:02

Delete job? [Y/n]
Delete pool? [Y/n]

Press ENTER to exit...
```

## <a name="next-steps"></a>Pasos siguientes

No dude en realizar cambios en *python_tutorial_client.py* y *python_tutorial_task.py* a experimentar con escenarios de cálculo diferentes. Por ejemplo, intente agregar un retraso de ejecución a *python_tutorial_task.py* para simular las tareas de ejecución larga y supervisión en el portal. Intente agregar más tareas o ajustar el número de nodos de cálculo. Agregar lógica para buscar y permitir el uso de un grupo existente para acelerar el tiempo de ejecución.

Ahora que ya está familiarizado con el flujo de trabajo básica de una solución por lotes, es el momento para profundizar las características adicionales del servicio de lote.

- Revise el artículo de [características de información general del lote de Azure](batch-api-basics.md) , se recomienda si es nuevo en el servicio.
- Iniciar en los otros artículos de desarrollo de lote en **desarrollo en profundidad** en la [ruta de aprendizaje de lote][batch_learning_path].
- Desproteger una implementación diferente de procesamiento de la carga de trabajo de "palabras top N" con el lote de la [TopNWords] [ github_topnwords] muestra.

[azure_batch]: https://azure.microsoft.com/services/batch/
[azure_free_account]: https://azure.microsoft.com/free/
[azure_portal]: https://portal.azure.com
[batch_learning_path]: https://azure.microsoft.com/documentation/learning-paths/batch/
[blog_linux]: http://blogs.technet.com/b/windowshpc/archive/2016/03/30/introducing-linux-support-on-azure-batch.aspx
[crypto]: https://cryptography.io/en/latest/
[crypto_install]: https://cryptography.io/en/latest/installation/
[github_samples]: https://github.com/Azure/azure-batch-samples
[github_samples_zip]: https://github.com/Azure/azure-batch-samples/archive/master.zip
[github_topnwords]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/TopNWords
[github_article_samples]: https://github.com/Azure/azure-batch-samples/tree/master/Python/Batch/article_samples

[nuget_packagemgr]: https://visualstudiogallery.msdn.microsoft.com/27077b70-9dad-4c64-adcf-c7cf6bc9970c
[nuget_restore]: https://docs.nuget.org/consume/package-restore/msbuild-integrated#enabling-package-restore-during-build

[py_account_ops]: http://azure-sdk-for-python.readthedocs.org/en/latest/ref/azure.batch.operations.html#azure.batch.operations.AccountOperations
[py_azure_sdk]: https://pypi.python.org/pypi/azure
[py_batch_docs]: http://azure-sdk-for-python.readthedocs.org/en/latest/ref/azure.batch.html
[py_batch_package]: https://pypi.python.org/pypi/azure-batch
[py_batchserviceclient]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.html#azure.batch.BatchServiceClient
[py_blockblobservice]: http://azure.github.io/azure-storage-python/ref/azure.storage.blob.blockblobservice.html#azure.storage.blob.blockblobservice.BlockBlobService
[py_cloudtask]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.models.html#azure.batch.models.CloudTask
[py_computenodeuser]: http://azure-sdk-for-python.readthedocs.org/en/latest/ref/azure.batch.models.html#azure.batch.models.ComputeNodeUser
[py_cs_config]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.models.html#azure.batch.models.CloudServiceConfiguration
[py_delete_container]: http://azure.github.io/azure-storage-python/ref/azure.storage.blob.baseblobservice.html#azure.storage.blob.baseblobservice.BaseBlobService.delete_container
[py_gen_blob_sas]: http://azure.github.io/azure-storage-python/ref/azure.storage.blob.baseblobservice.html#azure.storage.blob.baseblobservice.BaseBlobService.generate_blob_shared_access_signature
[py_gen_container_sas]: http://azure.github.io/azure-storage-python/ref/azure.storage.blob.baseblobservice.html#azure.storage.blob.baseblobservice.BaseBlobService.generate_container_shared_access_signature
[py_image_ref]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.models.html#azure.batch.models.ImageReference
[py_imagereference]: http://azure-sdk-for-python.readthedocs.org/en/latest/ref/azure.batch.models.html#azure.batch.models.ImageReference
[py_job]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.operations.html#azure.batch.operations.JobOperations
[py_jobpreptask]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.models.html#azure.batch.models.JobPreparationTask
[py_jobreltask]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.models.html#azure.batch.models.JobReleaseTask
[py_list_skus]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.operations.html#azure.batch.operations.AccountOperations.list_node_agent_skus
[py_make_blob_url]: http://azure.github.io/azure-storage-python/ref/azure.storage.blob.baseblobservice.html#azure.storage.blob.baseblobservice.BaseBlobService.make_blob_url
[py_pool]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.operations.html#azure.batch.operations.PoolOperations
[py_pooladdparam]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.models.html#azure.batch.models.PoolAddParameter
[py_poolinfo]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.models.html#azure.batch.models.PoolInformation
[py_resource_file]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.models.html#azure.batch.models.ResourceFile
[py_samples_github]: https://github.com/Azure/azure-batch-samples/tree/master/Python/Batch/
[py_starttask]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.models.html#azure.batch.models.StartTask
[py_starttask]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.models.html#azure.batch.models.StartTask
[py_task]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.models.html#azure.batch.models.CloudTask
[py_taskstate]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.models.html#azure.batch.models.TaskState
[py_vm_config]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.models.html#azure.batch.models.VirtualMachineConfiguration
[pypi_batch]: https://pypi.python.org/pypi/azure-batch
[pypi_storage]: https://pypi.python.org/pypi/azure-storage

[pypi_install]: http://python-packaging-user-guide.readthedocs.io/en/latest/installing/
[storage_explorer]: http://storageexplorer.com/
[visual_studio]: https://www.visualstudio.com/products/vs-2015-product-editions
[vm_marketplace]: https://azure.microsoft.com/marketplace/virtual-machines/

[1]: ./media/batch-python-tutorial/batch_workflow_01_sm.png "Crear contenedores de almacenamiento de Azure"
[2]: ./media/batch-python-tutorial/batch_workflow_02_sm.png "Cargar archivos de entrada (datos) y aplicación de la tarea en contenedores"
[3]: ./media/batch-python-tutorial/batch_workflow_03_sm.png "Crear grupo por lotes"
[4]: ./media/batch-python-tutorial/batch_workflow_04_sm.png "Crear tarea por lotes"
[5]: ./media/batch-python-tutorial/batch_workflow_05_sm.png "Agregar tareas a la tarea"
[6]: ./media/batch-python-tutorial/batch_workflow_06_sm.png "Supervisar tareas"
[7]: ./media/batch-python-tutorial/batch_workflow_07_sm.png "Descargar los resultados de la tarea de almacenamiento"
[8]: ./media/batch-python-tutorial/batch_workflow_sm.png "Flujo de trabajo de solución de lote (diagrama completo)"
[9]: ./media/batch-python-tutorial/credentials_batch_sm.png "Credenciales de proceso por lotes de Portal"
[10]: ./media/batch-python-tutorial/credentials_storage_sm.png "Credenciales de almacenamiento de Portal"
[11]: ./media/batch-python-tutorial/batch_workflow_minimal_sm.png "Flujo de trabajo de solución de lote (diagrama mínima)"
