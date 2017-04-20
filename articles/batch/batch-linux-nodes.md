<properties
    pageTitle="Nodos de Linux en grupos de Azure lote | Microsoft Azure"
    description="Obtenga información sobre cómo procesar las cargas de trabajo de cálculo paralelo en grupos de máquinas virtuales de Linux en lotes de Azure."
    services="batch"
    documentationCenter="python"
    authors="mmacy"
    manager="timlt"
    editor="" />

<tags
    ms.service="batch"
    ms.devlang="multiple"
    ms.topic="article"
    ms.tgt_pltfrm="vm-linux"
    ms.workload="na"
    ms.date="09/08/2016"
    ms.author="marsma" />

# <a name="provision-linux-compute-nodes-in-azure-batch-pools"></a>Aprovisionar Linux nodos de cálculo en grupos de lote de Azure

Puede usar el lote de Azure para ejecutar cargas de trabajo de cálculo paralelo en máquinas virtuales de Windows y Linux. Este artículo describe cómo crear grupos de nodos de cálculo de Linux en el servicio de lote utilizando el [Lote Python] [ py_batch_package] y [Lote .NET] [ api_net] bibliotecas de cliente.

> [AZURE.NOTE] [Paquetes de aplicaciones](batch-application-packages.md) actualmente no son compatibles en los nodos de cálculo Linux.

## <a name="virtual-machine-configuration"></a>Configuración de máquina virtual

Al crear un grupo de nodos de cálculo en lote, tiene dos opciones para seleccionar el tamaño de nodo y el sistema operativo: configuración de servicios de nube y configuración de máquina Virtual.

**Configuración de servicios de nube** proporciona que Windows calculan nodos *solo*. Tamaños de nodo de cálculo disponibles se muestran en [tamaños de servicios en la nube](../cloud-services/cloud-services-sizes-specs.md)y sistemas operativos disponibles se muestran en [las versiones de sistema operativo de invitado de Azure y matriz de compatibilidad SDK](../cloud-services/cloud-services-guestos-update-matrix.md). Al crear un grupo que contiene los nodos de servicios de nube de Azure, debe especificar el tamaño de nodo y su "familia de sistema operativo", que se encuentran en los artículos mencionados anteriormente. Para nodos de cálculo de grupos de Windows, normalmente se usa servicios de nube.

**Configuración de la máquina virtual** proporciona imágenes de Windows y Linux para nodos de cálculo. Tamaños de nodo de cálculo disponibles se muestran en [tamaños de máquinas virtuales de Azure](../virtual-machines/virtual-machines-linux-sizes.md) (Linux) y [tamaños para máquinas virtuales de Azure](../virtual-machines/virtual-machines-windows-sizes.md) (Windows). Al crear un grupo que contiene los nodos de configuración de la máquina Virtual, debe especificar el tamaño de los nodos, la referencia de la imagen de máquina virtual y el agente de nodo lote SKU esté instalado en los nodos.

### <a name="virtual-machine-image-reference"></a>Referencia de la imagen de máquina virtual

El servicio de lote utiliza [establece la escala de la máquina Virtual](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md) para proporcionar nodos de cálculo de Linux. Las imágenes de sistema operativo para estas máquinas virtuales se proporcionan en el [Catálogo de soluciones de Azure][vm_marketplace]. Cuando configura una referencia de la imagen de máquina virtual, especifique las propiedades de una imagen de máquina virtual de catálogo de soluciones. Las propiedades siguientes son necesarias al crear una referencia de la imagen de máquina virtual:

| **Propiedades de la referencia de imagen** | **Ejemplo** |
| ----------------- | ------------------------ |
| Publisher         | Canónica                |
| Oferta             | UbuntuServer             |
| SKU               | 14.04.4-LTS              |
| Versión           | más reciente                   |

> [AZURE.TIP] Puede obtener más información sobre estas propiedades y cómo mostrar imágenes de Marketplace en [navegar y seleccionadas imágenes de máquina virtual Linux en Azure con PowerShell o CLI](../virtual-machines/virtual-machines-linux-cli-ps-findimage.md). Tenga en cuenta que no todas las imágenes de Marketplace son actualmente compatibles con el lote. Para obtener más información, consulte [agente de nodo SKU](#node-agent-sku).

### <a name="node-agent-sku"></a>Agente de nodo SKU

El agente de nodo por lotes es un programa que se ejecuta en cada nodo en el grupo y proporciona la interfaz de comando y control entre el nodo y el servicio de lote. Existen diferentes implementaciones del agente de nodo, conocida como SKU, para distintos sistemas operativos. Básicamente, cuando se crea una configuración de máquina Virtual, primero especifique la referencia de la imagen de máquina virtual y, a continuación, especificar el agente de nodo instalar en la imagen. Normalmente, cada agente de nodo SKU es compatible con varias imágenes de máquina virtual. Estos son algunos ejemplos de agente de nodo SKU:

* batch.Node.Ubuntu 14.04
* batch.Node.centos 7
* batch.Node.Windows amd64

> [AZURE.IMPORTANT] No todas las imágenes de máquina virtual que están disponibles en el catálogo de soluciones son compatibles con los agentes de nodo lote disponibles actualmente. Debe usar el SDK de lote para el agente de nodo disponible SKU y las imágenes de máquina virtual con la que son compatibles de la lista. Ver las [imágenes de la lista de la máquina Virtual](#list-of-virtual-machine-images) más adelante en este artículo para obtener más información.

## <a name="create-a-linux-pool-batch-python"></a>Crear un grupo de Linux: Python por lotes

Fragmento de código siguiente muestra un ejemplo de cómo usar la [Biblioteca de cliente de Microsoft Azure por lotes de Python] [ py_batch_package] para crear un grupo de servidor de Ubuntu nodos de cálculo. Documentación de referencia para el módulo de Python del lote puede encontrarse en [el paquete de azure.batch] [py_batch_docs] en los documentos de lectura.

Este fragmento de código crea una [ImageReference] [ py_imagereference] explícitamente y especifica cada uno de sus propiedades (publisher, oferta, SKU, versión). En el código de producción, sin embargo, se recomienda que use la [list_node_agent_skus] [ py_list_skus] método para determinar y seleccione desde las combinaciones disponibles de SKU agente imagen y nodo en tiempo de ejecución.

```python
# Import the required modules from the
# Azure Batch Client Library for Python
import azure.batch.batch_service_client as batch
import azure.batch.batch_auth as batchauth
import azure.batch.models as batchmodels

# Specify Batch account credentials
account = "<batch-account-name>"
key = "<batch-account-key>"
batch_url = "<batch-account-url>"

# Pool settings
pool_id = "LinuxNodesSamplePoolPython"
vm_size = "STANDARD_A1"
node_count = 1

# Initialize the Batch client
creds = batchauth.SharedKeyCredentials(account, key)
config = batch.BatchServiceClientConfiguration(creds, base_url = batch_url)
client = batch.BatchServiceClient(config)

# Create the unbound pool
new_pool = batchmodels.PoolAddParameter(id = pool_id, vm_size = vm_size)
new_pool.target_dedicated = node_count

# Configure the start task for the pool
start_task = batchmodels.StartTask()
start_task.run_elevated = True
start_task.command_line = "printenv AZ_BATCH_NODE_STARTUP_DIR"
new_pool.start_task = start_task

# Create an ImageReference which specifies the Marketplace
# virtual machine image to install on the nodes.
ir = batchmodels.ImageReference(
    publisher = "Canonical",
    offer = "UbuntuServer",
    sku = "14.04.2-LTS",
    version = "latest")

# Create the VirtualMachineConfiguration, specifying
# the VM image reference and the Batch node agent to
# be installed on the node.
vmc = batchmodels.VirtualMachineConfiguration(
    image_reference = ir,
    node_agent_sku_id = "batch.node.ubuntu 14.04")

# Assign the virtual machine configuration to the pool
new_pool.virtual_machine_configuration = vmc

# Create pool in the Batch service
client.pool.add(new_pool)
```

Como se mencionó anteriormente, se recomienda que en lugar de crear el [ImageReference] [ py_imagereference] explícitamente, usa la [list_node_agent_skus] [ py_list_skus] método para seleccionar dinámicamente en las combinaciones de imagen de nodo actualmente admitidos agente/Marketplace. El siguiente fragmento de Python muestra el uso de este método.

```python
# Get the list of node agents from the Batch service
nodeagents = client.account.list_node_agent_skus()

# Obtain the desired node agent
ubuntu1404agent = next(agent for agent in nodeagents if "ubuntu 14.04" in agent.id)

# Pick the first image reference from the list of verified references
ir = ubuntu1404agent.verified_image_references[0]

# Create the VirtualMachineConfiguration, specifying the VM image
# reference and the Batch node agent to be installed on the node.
vmc = batchmodels.VirtualMachineConfiguration(
    image_reference = ir,
    node_agent_sku_id = ubuntu1404agent.id)
```

## <a name="create-a-linux-pool-batch-net"></a>Crear un grupo de Linux: lote .NET

Fragmento de código siguiente muestra un ejemplo de cómo usar el [Lote .NET] [ nuget_batch_net] biblioteca de cliente para crear un grupo de servidor de Ubuntu nodos de cálculo. Puede encontrar la [documentación de referencia del lote .NET] [ api_net] en MSDN.

Fragmento de código siguiente utiliza la [PoolOperations][net_pool_ops]. [ListNodeAgentSkus] [net_list_skus] método para seleccionar en la lista de actualmente admite combinaciones de SKU de agente de imagen y nodo Marketplace. Esta técnica es conveniente porque la lista de combinaciones admitidas puede cambiar de vez en cuando. Normalmente, se agregan combinaciones admitidas.

```csharp
// Pool settings
const string poolId = "LinuxNodesSamplePoolDotNet";
const string vmSize = "STANDARD_A1";
const int nodeCount = 1;

// Obtain a collection of all available node agent SKUs.
// This allows us to select from a list of supported
// VM image/node agent combinations.
List<NodeAgentSku> nodeAgentSkus =
    batchClient.PoolOperations.ListNodeAgentSkus().ToList();

// Define a delegate specifying properties of the VM image
// that we wish to use.
Func<ImageReference, bool> isUbuntu1404 = imageRef =>
    imageRef.Publisher == "Canonical" &&
    imageRef.Offer == "UbuntuServer" &&
    imageRef.SkuId.Contains("14.04");

// Obtain the first node agent SKU in the collection that matches
// Ubuntu Server 14.04. Note that there are one or more image
// references associated with this node agent SKU.
NodeAgentSku ubuntuAgentSku = nodeAgentSkus.First(sku =>
    sku.VerifiedImageReferences.Any(isUbuntu1404));

// Select an ImageReference from those available for node agent.
ImageReference imageReference =
    ubuntuAgentSku.VerifiedImageReferences.First(isUbuntu1404);

// Create the VirtualMachineConfiguration for use when actually
// creating the pool
VirtualMachineConfiguration virtualMachineConfiguration =
    new VirtualMachineConfiguration(
        imageReference: imageReference,
        nodeAgentSkuId: ubuntuAgentSku.Id);

// Create the unbound pool object using the VirtualMachineConfiguration
// created above
CloudPool pool = batchClient.PoolOperations.CreatePool(
    poolId: poolId,
    virtualMachineSize: vmSize,
    virtualMachineConfiguration: virtualMachineConfiguration,
    targetDedicated: nodeCount);

// Commit the pool to the Batch service
pool.Commit();
```

Aunque el fragmento de código anterior utiliza el [PoolOperations][net_pool_ops]. [ListNodeAgentSkus] [net_list_skus] admite el método de la lista y seleccione desde dinámicamente imagen y nodo combinaciones de SKU de agente (recomendados), también puede configurar un [ImageReference] [ net_imagereference] explícitamente:

```csharp
ImageReference imageReference = new ImageReference(
    publisher: "Canonical",
    offer: "UbuntuServer",
    skuId: "14.04.2-LTS",
    version: "latest");
```

## <a name="list-of-virtual-machine-images"></a>Lista de imágenes de máquina virtual

La siguiente tabla enumeran las imágenes de máquina virtual de Marketplace que son compatibles con los agentes de nodo de lote disponibles cuando este artículo se actualizó por última vez. Es importante que tenga en cuenta que esta lista no es definitiva porque las imágenes y los agentes de nodo pueden agregar o quitar en cualquier momento. Se recomienda que su lote utilizan aplicaciones y servicios siempre [list_node_agent_skus] [ py_list_skus] (Python) y [ListNodeAgentSkus] [ net_list_skus] (lote .NET) para determinar y seleccione desde el SKU disponible actualmente.

> [AZURE.WARNING] La siguiente lista se puede cambiar en cualquier momento. Usar siempre los métodos de **agente de nodo de lista SKU** disponibles en las API de lote de la lista y seleccione de la máquina virtual de compatible y el agente de nodo SKU al ejecutar los procesos.

| **Publisher** | **Oferta** | **Imagen de SKU** | **Versión** | **Agente de nodo ID de SKU** |
| ------- | ------- | ------- | ------- | ------- |
| Canónica | UbuntuServer | 14.04.0-LTS | más reciente | batch.Node.Ubuntu 14.04 |
| Canónica | UbuntuServer | 14.04.1-LTS | más reciente | batch.Node.Ubuntu 14.04 |
| Canónico | UbuntuServer | 14.04.2-LTS | más reciente | batch.Node.Ubuntu 14.04 |
| Canónica | UbuntuServer | 14.04.3-LTS | más reciente | batch.Node.Ubuntu 14.04 |
| Canónica | UbuntuServer | 14.04.4-LTS | más reciente | batch.Node.Ubuntu 14.04 |
| Canónico | UbuntuServer | 14.04.5-LTS | más reciente | batch.Node.Ubuntu 14.04 |
| Canónico | UbuntuServer | 16.04.0-LTS | más reciente | batch.Node.Ubuntu 16.04 |
| Credativ | Debian | 8 | más reciente | batch.Node.Debian 8 |
| OpenLogic | CentOS | 7.0 | más reciente | batch.Node.centos 7 |
| OpenLogic | CentOS | 7.1 | más reciente | batch.Node.centos 7 |
| OpenLogic | CentOS HPC | 7.1 | más reciente | batch.Node.centos 7 |
| OpenLogic | CentOS | 7.2 | más reciente | batch.Node.centos 7 |
| Oracle | Linux de Oracle | 7.0 | más reciente | batch.Node.centos 7 |
| SUSE | openSUSE | 13.2 | más reciente | batch.Node.OpenSuSE 13.2 |
| SUSE | Salto de openSUSE | 42.1 | más reciente | batch.Node.OpenSuSE 42.1 |
| SUSE | SLES HPC | 12 | más reciente | batch.Node.OpenSuSE 42.1 |
| SUSE | SLES | 12 SP1 | más reciente | batch.Node.OpenSuSE 42.1 |
| anuncios de Microsoft | estándar datos ciencias vm | estándar datos ciencias vm | más reciente | batch.Node.Windows amd64 |
| anuncios de Microsoft | Linux datos ciencias vm | linuxdsvm | más reciente | batch.Node.centos 7 |
| Microsoft Windows Server | Windows Server | SP1 DE 2008 R2 | más reciente | batch.Node.Windows amd64 |
| Microsoft Windows Server | Windows Server | Centro de datos de 2012 | más reciente | batch.Node.Windows amd64 |
| Microsoft Windows Server | Windows Server | Centro de datos 2012-R2 | más reciente | batch.Node.Windows amd64 |
| Microsoft Windows Server | Windows Server | Windows Server-técnicos: vista previa | más reciente | batch.Node.Windows amd64 |

## <a name="connect-to-linux-nodes"></a>Conectarse a los nodos de Linux

Durante el desarrollo o solución de problemas, es posible es necesario iniciar sesión en los nodos en el grupo. A diferencia de los nodos de cálculo de Windows, no puede usar el protocolo de escritorio remoto (RDP) para conectarse a los nodos de Linux. En su lugar, el servicio de lote permite el acceso SSH en cada nodo para la conexión remota.

Fragmento de código Python siguiente crea un usuario en cada nodo en un grupo, que es necesario para la conexión remota. A continuación, se imprime la información de conexión de shell seguro (SSH) para cada nodo.

```python
import datetime
import getpass
import azure.batch.batch_service_client as batch
import azure.batch.batch_auth as batchauth
import azure.batch.models as batchmodels

# Specify your own account credentials
batch_account_name = ''
batch_account_key = ''
batch_account_url = ''

# Specify the ID of an existing pool containing Linux nodes
# currently in the 'idle' state
pool_id = ''

# Specify the username and prompt for a password
username = 'linuxuser'
password = getpass.getpass()

# Create a BatchClient
credentials = batchauth.SharedKeyCredentials(
    batch_account_name,
    batch_account_key
)
batch_client = batch.BatchServiceClient(
        credentials,
        base_url=batch_account_url
)

# Create the user that will be added to each node in the pool
user = batchmodels.ComputeNodeUser(username)
user.password = password
user.is_admin = True
user.expiry_time = \
    (datetime.datetime.today() + datetime.timedelta(days=30)).isoformat()

# Get the list of nodes in the pool
nodes = batch_client.compute_node.list(pool_id)

# Add the user to each node in the pool and print
# the connection information for the node
for node in nodes:
    # Add the user to the node
    batch_client.compute_node.add_user(pool_id, node.id, user)

    # Obtain SSH login information for the node
    login = batch_client.compute_node.get_remote_login_settings(pool_id,
                                                                node.id)

    # Print the connection info for the node
    print("{0} | {1} | {2} | {3}".format(node.id,
                                         node.state,
                                         login.remote_login_ip_address,
                                         login.remote_login_port))
```

Esto es el resultado de ejemplo para el código anterior para un grupo que contiene los cuatro nodos de Linux:

```
Password:
tvm-1219235766_1-20160414t192511z | ComputeNodeState.idle | 13.91.7.57 | 50000
tvm-1219235766_2-20160414t192511z | ComputeNodeState.idle | 13.91.7.57 | 50003
tvm-1219235766_3-20160414t192511z | ComputeNodeState.idle | 13.91.7.57 | 50002
tvm-1219235766_4-20160414t192511z | ComputeNodeState.idle | 13.91.7.57 | 50001
```

Tenga en cuenta que, en lugar de una contraseña, puede especificar una clave pública de SSH cuando se crea un usuario en un nodo. En el SDK de Python, esto se hace mediante el parámetro de **ssh_public_key** en [ComputeNodeUser][py_computenodeuser]. En. NET, esto se hace utilizando la [ComputeNodeUser][net_computenodeuser]. [SshPublicKey] [net_ssh_key] propiedad.

## <a name="pricing"></a>Precios

Lote de Azure se basa en la tecnología de servicios de nube de Azure y máquinas virtuales de Azure. El servicio de lote se ofrece sin costo, lo que significa que le cobrarán solo para los recursos de cálculo que consumen las soluciones de lote. Al elegir la **Configuración de servicios de nube**, se cargará en función de los [precios de servicios de nube] [ cloud_services_pricing] estructura. Al elegir la **Configuración de la máquina Virtual**, se cargará en función de los [precios de máquinas virtuales] [ vm_pricing] estructura.

## <a name="next-steps"></a>Pasos siguientes

### <a name="batch-python-tutorial"></a>Tutorial de Python por lotes

Para obtener un tutorial acerca de cómo trabajar con lotes con Python más en profundidad, consulte [empezar a trabajar con el cliente de Azure lote Python](batch-python-tutorial.md). Su [código de ejemplo] de companion[ github_samples_pyclient] incluye una función auxiliar, `get_vm_config_for_distro`, que muestra otra técnica para obtener una configuración de máquina virtual.

### <a name="batch-python-code-samples"></a>Ejemplos de código de Python por lotes

Consulte los otros [ejemplos de código de Python] [ github_samples_py] en los [ejemplos del lote de azure] [ github_samples] repositorio de GitHub de varias secuencias de comandos que muestran cómo llevar a cabo operaciones por lotes comunes como grupo de trabajo y la creación de tareas. El [archivo Léame] [ github_py_readme] que acompaña a la Python ejemplos con información detallada sobre cómo instalar los paquetes necesarios.

### <a name="batch-forum"></a>Foro de proceso por lotes

El [Foro de Azure lote] [ forum] en MSDN es un lugar ideal para tratar el lote y formular preguntas acerca del servicio. Lea publicaciones útil "stickied" y publique sus preguntas que surjan mientras crea soluciones lote.

[api_net]: http://msdn.microsoft.com/library/azure/mt348682.aspx
[api_net_mgmt]: https://msdn.microsoft.com/library/azure/mt463120.aspx
[api_rest]: http://msdn.microsoft.com/library/azure/dn820158.aspx
[cloud_services_pricing]: https://azure.microsoft.com/pricing/details/cloud-services/
[forum]: https://social.msdn.microsoft.com/forums/azure/en-US/home?forum=azurebatch
[github_py_readme]: https://github.com/Azure/azure-batch-samples/blob/master/Python/Batch/README.md
[github_samples]: https://github.com/Azure/azure-batch-samples
[github_samples_py]: https://github.com/Azure/azure-batch-samples/tree/master/Python/Batch
[github_samples_pyclient]: https://github.com/Azure/azure-batch-samples/blob/master/Python/Batch/article_samples/python_tutorial_client.py
[portal]: https://portal.azure.com
[net_cloudpool]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.aspx
[net_computenodeuser]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.computenodeuser.aspx
[net_imagereference]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.imagereference.aspx
[net_list_skus]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.listnodeagentskus.aspx
[net_pool_ops]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.aspx
[net_ssh_key]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.computenodeuser.sshpublickey.aspx
[nuget_batch_net]: https://www.nuget.org/packages/Azure.Batch/
[rest_add_pool]: https://msdn.microsoft.com/library/azure/dn820174.aspx
[py_account_ops]: http://azure-sdk-for-python.readthedocs.org/en/dev/ref/azure.batch.operations.html#azure.batch.operations.AccountOperations
[py_azure_sdk]: https://pypi.python.org/pypi/azure
[py_batch_docs]: http://azure-sdk-for-python.readthedocs.org/en/dev/ref/azure.batch.html
[py_batch_package]: https://pypi.python.org/pypi/azure-batch
[py_computenodeuser]: http://azure-sdk-for-python.readthedocs.org/en/dev/ref/azure.batch.models.html#azure.batch.models.ComputeNodeUser
[py_imagereference]: http://azure-sdk-for-python.readthedocs.org/en/dev/ref/azure.batch.models.html#azure.batch.models.ImageReference
[py_list_skus]: http://azure-sdk-for-python.readthedocs.org/en/dev/ref/azure.batch.operations.html#azure.batch.operations.AccountOperations.list_node_agent_skus
[vm_marketplace]: https://azure.microsoft.com/marketplace/virtual-machines/
[vm_pricing]: https://azure.microsoft.com/pricing/details/virtual-machines/
