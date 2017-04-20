<properties
   pageTitle="Introducción a Azure lote CLI | Microsoft Azure"
   description="Una rápida introducción a los comandos de proceso por lotes en Azure CLI de administración de recursos de servicio del lote de Azure"
   services="batch"
   documentationCenter=""
   authors="mmacy"
   manager="timlt"
   editor=""/>

<tags
   ms.service="batch"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="multiple"
   ms.workload="big-compute"
   ms.date="09/30/2016"
   ms.author="marsma"/>

# <a name="get-started-with-azure-batch-cli"></a>Introducción a Azure lote CLI

La plataforma cruzada Azure de línea de comandos interfaz Azure le permite administrar las cuentas de lote y los recursos como grupos, tareas y tareas en el shell de comandos de Linux, Mac y Windows. Con la CLI Azure, puede realizar y muchas de las tareas que lleva a cabo con la API de proceso por lotes, portal de Azure y cmdlets de PowerShell lote de scripts.

En este artículo se basa en Azure CLI versión 0.10.5.

## <a name="prerequisites"></a>Requisitos previos

* [Instalar la CLI de Azure](../xplat-cli-install.md)

* [Conectar la CLI Azure a su suscripción de Azure](../xplat-cli-connect.md)

* Cambie al **modo de administrador de recursos**:`azure config mode arm`

>[AZURE.TIP] Le recomendamos que actualice su instalación de Azure CLI con frecuencia para aprovechar las mejoras y actualizaciones de servicio.

## <a name="command-help"></a>Comando de ayuda

Puede mostrar el texto de ayuda para cada comando en Azure CLI agregando `-h` como la única opción después del comando. Por ejemplo:

* Para obtener ayuda sobre la `azure` de comandos, escriba:`azure -h`
* Para obtener una lista de todos los comandos de proceso por lotes en CLI, use:`azure batch -h`
* Para obtener ayuda sobre la creación de una cuenta de proceso por lotes, escriba:`azure batch account create -h`

En caso de duda, utilice la `-h` opción de línea de comandos para obtener ayuda sobre cualquier comando CLI de Azure.

## <a name="create-a-batch-account"></a>Crear una cuenta de proceso por lotes

Uso:

    azure batch account create [options] <name>

Ejemplo:

    azure batch account create --location "West US"  --resource-group "resgroup001" "batchaccount001"

Crea una nueva cuenta de lote con los parámetros especificados. Debe especificar al menos una ubicación, el grupo de recursos y el nombre de la cuenta. Si todavía no tiene un grupo de recursos, cree uno ejecutando `azure group create`y especifique una de las regiones Azure (por ejemplo, "Oeste nos") para la `--location` opción. Por ejemplo:

    azure group create --name "resgroup001" --location "West US"

> [AZURE.NOTE] El nombre de cuenta del lote debe ser único dentro de la región de Azure que se crea la cuenta. Puede contener solo caracteres alfanuméricos en minúsculas y debe ser 3-24 caracteres de longitud. No puede usar caracteres especiales como `-` o `_` en nombres de cuenta de lote.

### <a name="linked-storage-account-autostorage"></a>Cuenta de almacenamiento vinculado (autostorage)

(Opcionalmente), puede vincular una cuenta de almacenamiento de **Propósito General** a su cuenta de lote cuando lo cree. La característica de [paquetes de aplicaciones](batch-application-packages.md) del lote usa almacenamiento de blobs en un cuenta de almacenamiento, de propósito General vinculado al igual que la biblioteca de [.NET de convenciones de archivo por lotes](batch-task-output.md) . Estas características opcionales le ayudarán a implementar las aplicaciones que ejecutarán las tareas por lotes y conservar los datos que generan.

Para vincular una cuenta de Azure almacenamiento existente a una nueva cuenta de lote cuando lo cree, especifique la `--autostorage-account-id` opción. Esta opción requiere el identificador de recurso completo de la cuenta de almacenamiento.

En primer lugar, mostrar detalles de la cuenta de almacenamiento:

    azure storage account show --resource-group "resgroup001" "storageaccount001"

A continuación, use el valor de **dirección Url** para la `--autostorage-account-id` opción. El valor de dirección Url comienza con "/ suscripciones /" y contiene la ruta de ID y recursos de suscripción a la cuenta de almacenamiento:

    azure batch account create --location "West US"  --resource-group "resgroup001" --autostorage-account-id "/subscriptions/8ffffff8-4444-4444-bfbf-8ffffff84444/resourceGroups/resgroup001/providers/Microsoft.Storage/storageAccounts/storageaccount001" "batchaccount001"

## <a name="delete-a-batch-account"></a>Eliminar una cuenta de proceso por lotes

Uso:

    azure batch account delete [options] <name>

Ejemplo:

    azure batch account delete --resource-group "resgroup001" "batchaccount001"

Elimina la cuenta de lote especificada. Cuando se le solicite, confirme que desea quitar la cuenta (eliminación de cuenta puede tardar algún tiempo en completarse).

## <a name="manage-account-access-keys"></a>Administrar las teclas de acceso de cuenta

Necesita una tecla de acceso para [crear y modificar los recursos](#create-and-modify-batch-resources) en su cuenta por lotes.

### <a name="list-access-keys"></a>Teclas de acceso de la lista

Uso:

    azure batch account keys list [options] <name>

Ejemplo:

    azure batch account keys list --resource-group "resgroup001" "batchaccount001"

Enumera las teclas de cuenta para la cuenta de proceso por lotes determinada.

### <a name="generate-a-new-access-key"></a>Generar una nueva clave de acceso

Uso:

    azure batch account keys renew [options] --<primary|secondary> <name>

Ejemplo:

    azure batch account keys renew --resource-group "resgroup001" --primary "batchaccount001"

Regenera la clave de cuenta especificada para la cuenta de proceso por lotes determinada.

## <a name="create-and-modify-batch-resources"></a>Crear y modificar recursos por lotes

Puede utilizar la CLI de Azure para crear, leer, actualizar, eliminar recursos de lote (CRUD) como grupos, calcular nodos, tareas y tareas. Estas operaciones de CRUD requieren su nombre de la cuenta de proceso por lotes, la tecla de acceso y el extremo. También puede especificarlos con la `-a`, `-k`, y `-u` opciones o conjunto de [variables de entorno](#credential-environment-variables) que CLI utiliza automáticamente (si rellena).

### <a name="credential-environment-variables"></a>Variables de entorno de credenciales

Puede establecer `AZURE_BATCH_ACCOUNT`, `AZURE_BATCH_ACCESS_KEY`, y `AZURE_BATCH_ENDPOINT` variables de entorno en lugar de especificar `-a`, `-k`, y `-u` opciones en la línea de comandos para cada comando ejecutar. CLI lote utiliza estas variables (si establece) para que se puede omitir la `-a`, `-k`, y `-u` opciones. El resto de este artículo supone el uso de estas variables de entorno.

>[AZURE.TIP] Lista de las claves con `azure batch account keys list`y mostrar el punto final de la cuenta con `azure batch account show`.

### <a name="json-files"></a>Archivos JSON

Cuando crea recursos de lote como grupos y trabajos, puede especificar un archivo JSON que contiene la nueva configuración de los recursos en lugar de pasar opciones como comandos de sus parámetros. Por ejemplo:

`azure batch pool create my_batch_pool.json`

Aunque puede realizar muchas operaciones de creación de recursos con solo las opciones de línea de comandos, algunas características requieren un archivo con formato JSON que contiene los detalles de recursos. Por ejemplo, debe usar un archivo JSON si desea especificar los archivos de recursos para una tarea de inicio.

Para buscar el JSON necesario para crear un recurso, consulte la [referencia de la API de REST de lote] [ rest_api] documentación en MSDN. Cada tema "Agregar *tipo de recurso"*contiene ejemplo JSON para crear el recurso, que puede utilizar como plantillas para sus archivos JSON. Por ejemplo, puede encontrarse JSON para la creación del grupo en [Agregar un grupo a una cuenta de][rest_add_pool].

>[AZURE.NOTE] Si especifica un archivo JSON cuando se crea un recurso, se pasan por alto el resto de los parámetros que se especifica en la línea de comandos para ese recurso.

## <a name="create-a-pool"></a>Crear un grupo

Uso:

    azure batch pool create [options] [json-file]

Ejemplo (configuración de máquina Virtual):

    azure batch pool create --id "pool001" --target-dedicated 1 --vm-size "STANDARD_A1" --image-publisher "Canonical" --image-offer "UbuntuServer" --image-sku "14.04.2-LTS" --node-agent-id "batch.node.ubuntu 14.04"

Ejemplo (configuración de servicios de nube):

    azure batch pool create --id "pool002" --target-dedicated 1 --vm-size "small" --os-family "4"

Crea un conjunto de nodos de cálculo en el servicio de lote.

Como se mencionó en la [Introducción a las características por lotes](batch-api-basics.md#pool), tiene dos opciones cuando se selecciona un sistema operativo de los nodos en el grupo de servidores: **Configuración de la máquina Virtual** y **Configuración de servicios de nube**. Usar el `--image-*` opciones para crear grupos de configuración de máquina Virtual, y `--os-family` crear grupos de configuración de servicios de nube. No puede especificar ambos `--os-family` y `--image-*` opciones.

Puede especificar grupo [paquetes de aplicaciones](batch-application-packages.md) y la línea de comandos para una [tarea de inicio](batch-api-basics.md#start-task). Sin embargo, para especificar los archivos de recursos de la tarea de inicio, en su lugar debe utilizar un [archivo JSON](#json-files).

Eliminar un grupo con:

    azure batch pool delete [pool-id]

>[AZURE.TIP] Compruebe la [lista de imágenes de máquina virtual](batch-linux-nodes.md#list-of-virtual-machine-images) para los valores apropiados para la `--image-*` opciones.

## <a name="create-a-job"></a>Crear un trabajo

Uso:

    azure batch job create [options] [json-file]

Ejemplo:

    azure batch job create --id "job001" --pool-id "pool001"

Agrega un trabajo en la cuenta de lote y especifica el grupo en el que se ejecutan sus tareas.

Eliminar un trabajo con:

    azure batch job delete [job-id]

## <a name="list-pools-jobs-tasks-and-other-resources"></a>Grupos de la lista, tareas, tareas y otros recursos

Es compatible con cada tipo de recurso por lotes un `list` comando que consulta la cuenta de proceso por lotes y se enumera los recursos de ese tipo. Por ejemplo, puede mostrar los grupos en su cuenta y las tareas de un trabajo:

    azure batch pool list
    azure batch task list --job-id "job001"

### <a name="listing-resources-efficiently"></a>Lista de recursos eficaz

Para consultar más rápido, puede especificar para **Seleccionar**, **filtro**y **expanda** opciones de la cláusula `list` operaciones. Utilice estas opciones para limitar la cantidad de datos devueltos por el servicio de lote. Debido a todos los filtros del servidor, solo los datos que le interesa cruza el cable. Use estas cláusulas para ahorrar ancho de banda (y por tanto tiempo) al realizar operaciones de la lista.

Por ejemplo, devolverá solo los grupos cuyo identificador empiece por "renderTask":

    azure batch task list --job-id "job001" --filter-clause "startswith(id, 'renderTask')"

Lote CLI admite todos los tres cláusulas compatibles con el servicio de lote:

* `--select-clause [select-clause]`Devolver un subconjunto de propiedades para cada entidad
* `--filter-clause [filter-clause]`Devolver sólo entidades que coinciden con la expresión especificada de OData
* `--expand-clause [expand-clause]`Obtener la información de entidad en una sola llamada resto subyacente. La cláusula de expandir solo es compatible con la `stats` propiedad en este momento.

Para obtener detalles sobre los tres cláusulas y realizar consultas de la lista con ellos, vea [consultar el servicio de Azure lote de forma eficaz](batch-efficient-list-queries.md).

## <a name="application-package-management"></a>Administración del paquete de aplicaciones

Paquetes de aplicaciones proporcionan una manera simplificada para implementar aplicaciones en los nodos de cálculo de los grupos. CLI Azure, puede cargar paquetes de aplicaciones, administrar las versiones de paquetes y eliminar paquetes.

Para crear una nueva aplicación y agregar una versión de paquete:

**Crear** una aplicación:

    azure batch application create "resgroup001" "batchaccount001" "MyTaskApplication"

**Agregar** un paquete de aplicación:

    azure batch application package create "resgroup001" "batchaccount001" "MyTaskApplication" "1.10-beta3" package001.zip

**Activar** el paquete:

    azure batch application package activate "resgroup001" "batchaccount001" "MyTaskApplication" "1.10-beta3" zip

Establezca la **versión predeterminada** de la aplicación:

    azure batch application set "resgroup001" "batchaccount001" "MyTaskApplication" --default-version "1.10-beta3"

### <a name="deploy-an-application-package"></a>Implementar un paquete de aplicación

Puede especificar uno o varios paquetes de aplicación para su implementación cuando se crea un nuevo grupo. Al especificar un paquete en tiempo de creación de grupo, se implementa a cada nodo como la agrupación de combinaciones de nodo. También se despliegan los paquetes cuando se reinicia un nodo o se rehace su imagen.

Especifique la `--app-package-ref` opción para crear un grupo para implementar un paquete de aplicación a los nodos del grupo como unirse a la agrupación. La `--app-package-ref` opción acepta una lista delimitada por punto y coma de identificadores de aplicación para implementar los nodos de cálculo.

    azure batch pool create --pool-id "pool001" --target-dedicated 1 --vm-size "small" --os-family "4" --app-package-ref "MyTaskApplication"

Cuando se crea una agrupación con opciones de línea de comandos, actualmente no puede especificar *qué* versión del paquete de aplicación para implementar los nodos de cálculo, por ejemplo "1.10-beta3". Por lo tanto, primero debe especificar la versión predeterminada para la aplicación con `azure batch application set [options] --default-version <version-id>` antes de crear el grupo (consulte la sección anterior). Sin embargo, puede especificar una versión de paquete para el grupo si usa un [archivo JSON](#json-files) en lugar de opciones de línea de comandos al crear el grupo.

Puede encontrar más información sobre los paquetes de aplicación de [implementación de la aplicación con los paquetes de aplicaciones de Azure lote](batch-application-packages.md).

>[AZURE.IMPORTANT] Debe [vincular una cuenta de Azure almacenamiento](#linked-storage-account-autostorage) a su cuenta de lote para usar paquetes de aplicaciones.

### <a name="update-a-pools-application-packages"></a>Actualizar los paquetes de aplicación de la agrupación

Para actualizar las aplicaciones asignadas a un grupo existente, emita el `azure batch pool set` comando con la `--app-package-ref` opción:

    azure batch pool set --pool-id "pool001" --app-package-ref "MyTaskApplication2"

Para implementar el nuevo paquete de aplicación para calcular los nodos ya están en un grupo existente, debe reiniciar o crear una nueva imagen los nodos:

    azure batch node reboot --pool-id "pool001" --node-id "tvm-3105992504_1-20160930t164509z"

>[AZURE.TIP] Puede obtener una lista de los nodos en un grupo, junto con sus identificadores de nodo con `azure batch node list`.

Tenga en cuenta que debe ya ha configurado la aplicación con una versión predeterminada antes de la implementación (`azure batch application set [options] --default-version <version-id>`).

## <a name="troubleshooting-tips"></a>Consejos para solucionar problemas

Esta sección está destinada a proporcionarle recursos que desea utilizar para solucionar problemas de Azure CLI. No necesariamente solucionar todos los problemas, pero puede ser útil limitar la causa y elija para ayudar a los recursos.

* Usar `-h` para obtener el **texto de ayuda** para cualquier comando CLI

* Usar `-v` y `-vv` para mostrar el resultado del comando **detallado** ; `-vv` es "extra" detallado y muestra el resto convocatorias y respuestas real. Estos parámetros son útiles para mostrar la salida completa del error.

* Puede ver el **resultado del comando como JSON** con la `--json` opción. Por ejemplo, `azure batch pool show "pool001" --json` muestra las propiedades de pool001 en formato JSON. Puede copiar y modificar este resultado para usar en un `--json-file` (consulte [archivos JSON](#json-files) más adelante en este artículo).

* El [foro de lote en MSDN] [ batch_forum] es un recurso muy útil y estrechamente supervisado por los miembros del equipo por lotes. Asegúrese de publicar sus preguntas allí si tiene problemas o desea obtener ayuda con una operación específica.

* No cada operación de recursos por lotes es compatible actualmente CLI Azure. Por ejemplo, actualmente no puede especificar un paquete de aplicación *versión* para un grupo, solo el Id. En estos casos, tendrá que proporcionar un `--json-file` el comando en lugar de usar las opciones de línea de comandos. Asegúrese de estar al día con la última versión CLI a recoger mejoras en el futuros.

## <a name="next-steps"></a>Pasos siguientes

*  Vea [implementación de aplicaciones con los paquetes de aplicaciones de Azure lote](batch-application-packages.md) para buscar información sobre cómo usar esta característica para administrar e implementar las aplicaciones que se ejecute en los nodos de cálculo por lotes.

* Vea [consultar el servicio de lote eficazmente](batch-efficient-list-queries.md) con más información sobre reducir el número de elementos y el tipo de información que se devuelve de consultas al lote.

[batch_forum]: https://social.msdn.microsoft.com/forums/azure/en-US/home?forum=azurebatch
[github_readme]: https://github.com/Azure/azure-xplat-cli/blob/dev/README.md
[rest_api]: https://msdn.microsoft.com/library/azure/dn820158.aspx
[rest_add_pool]: https://msdn.microsoft.com/library/azure/dn820174.aspx