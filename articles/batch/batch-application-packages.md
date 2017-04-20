<properties
    pageTitle="Instalación de la aplicación fácil y administración en lotes de Azure | Microsoft Azure"
    description="Use la característica de paquetes de aplicación del lote de Azure para administrar fácilmente varias aplicaciones y versiones para la instalación en el lote de nodos de cálculo."
    services="batch"
    documentationCenter=".net"
    authors="mmacy"
    manager="timlt"
    editor="" />

<tags
    ms.service="batch"
    ms.devlang="multiple"
    ms.topic="article"
    ms.tgt_pltfrm="vm-windows"
    ms.workload="big-compute"
    ms.date="10/21/2016"
    ms.author="marsma" />

# <a name="application-deployment-with-azure-batch-application-packages"></a>Implementación de la aplicación con los paquetes de aplicaciones de proceso de Azure

La característica de paquetes de aplicación del lote de Azure proporciona fácil administración de aplicaciones de la tarea y su implementación en los nodos de cálculo en el grupo. Con los paquetes de aplicación, puede cargar y administrar varias versiones de las aplicaciones que se ejecuten las tareas, incluidos los archivos auxiliares. Puede luego automáticamente implementar uno o más de estas aplicaciones a los nodos de cálculo en el grupo.

En este artículo, aprenderá cómo cargar y administrar paquetes de aplicaciones en el portal de Azure. A continuación, aprenderá a instalar nodos de un conjunto de cálculo con el [Lote de .NET] [ api_net] biblioteca.

> [AZURE.NOTE] La característica de paquetes de aplicaciones que se describen aquí reemplaza a la característica "Lote aplicaciones" disponible en versiones anteriores del servicio.

## <a name="application-package-requirements"></a>Requisitos del paquete de aplicación

Debe [vincular una cuenta de Azure almacenamiento](#link-a-storage-account) a su cuenta de lote para usar paquetes de aplicaciones.

La característica de paquetes de aplicación analizada en este artículo es compatible *únicamente* con los grupos por lotes que se crearon después de 10 de marzo de 2016. Paquetes de aplicación no se implementará para calcular los nodos en grupos creados antes de esta fecha.

Esta característica se introdujo en [API de REST de lote] [ api_rest] versión 2015-12-01.2.2 y correspondiente [Lote .NET] [ api_net] versión 3.1.0 de la biblioteca. Se recomienda usar siempre la versión más reciente de la API cuando se trabaja con el lote.

> [AZURE.IMPORTANT] Grupos de *CloudServiceConfiguration* solo admiten actualmente, paquetes de aplicaciones. No puede usar paquetes de aplicaciones en grupos creados mediante VirtualMachineConfiguration imágenes. Consulte la sección [configuración de la máquina Virtual](batch-linux-nodes.md#virtual-machine-configuration) de [aprovisionamiento Linux nodos de cálculo en grupos de Azure lote](batch-linux-nodes.md) para obtener más información acerca de las diferentes configuraciones de dos.

## <a name="about-applications-and-application-packages"></a>Acerca de las aplicaciones y paquetes de aplicaciones

En el lote de Azure, una *aplicación* hace referencia a un conjunto de archivos binarios versiones que se pueden descargar automáticamente a los nodos de cálculo en el grupo. Un *paquete de aplicación* hace referencia a un *conjunto determinado* de los datos binarios y representa una determinada *versión* de la aplicación.

![Diagrama de alto nivel de aplicaciones y paquetes de aplicaciones][1]

### <a name="applications"></a>Aplicaciones

Una aplicación en lote contiene uno o más aplicación paquetes y especifica las opciones de configuración de la aplicación. Por ejemplo, una aplicación puede especificar la versión del paquete de aplicación predeterminados instalar en nodos de cálculo y si se pueden actualizar o eliminar sus paquetes.

### <a name="application-packages"></a>Paquetes de aplicaciones

Un paquete de aplicación es un archivo .zip que contiene los archivos binarios y los archivos auxiliares necesarios para la ejecución por sus tareas. Cada paquete de aplicación representa una versión específica de la aplicación.

Puede especificar los paquetes de aplicaciones en el nivel de grupo y tareas. Puede especificar uno o varios de estos paquetes y (opcionalmente) en una versión al crear un grupo o una tarea.

* **Paquetes de aplicaciones de grupo** se implementan en *todos* los nodos en el grupo. Aplicaciones se implementan cuando un nodo une a un grupo, y cuando se reinicia o se rehace su imagen.

    Paquetes de aplicaciones de grupo son las más apropiados cuando todos los nodos en un grupo de ejecutan las tareas de la tarea. Puede especificar uno o varios paquetes de aplicación al crear un grupo, y puede agregar o actualizar paquetes de un grupo existente. Si actualiza los paquetes de aplicaciones de un grupo existente, debe reiniciar sus nodos para instalar el paquete nuevo.

* **Paquetes de aplicaciones de la tarea** se implementan solo a un nodo de cálculo programado para ejecutar una tarea, justo antes de ejecutar la línea de comandos de la tarea. Si el paquete de aplicación especificada y la versión ya está en el nodo, se vuelve a implementar y se usa el paquete existente.

    Paquetes de aplicaciones de la tarea son útiles en entornos de grupo compartido, donde se inician las distintas tareas en un grupo y el grupo no se elimina cuando se complete una tarea. Si el trabajo tiene menos tareas de nodos en el grupo, paquetes de aplicaciones de la tarea pueden minimizar la transferencia de datos desde la aplicación se implementa solo en los nodos que ejecutan las tareas.

    Otras situaciones que pueden beneficiarse de paquetes de aplicaciones de la tarea son tareas que usan una aplicación de gran tamaño, pero para solo un pequeño número de tareas. Por ejemplo, una fase de preprocesamiento o una tarea de combinación de correspondencia, donde la aplicación preprocesamiento o combinación de correspondencia es pesado.

> [AZURE.IMPORTANT] Hay restricciones en el número de aplicaciones y paquetes de aplicaciones dentro de una cuenta de proceso por lotes, así como el tamaño del paquete de aplicación máxima. Vea [las cuotas y límites para el servicio de Azure lote](batch-quota-limit.md) para obtener más información acerca de estos límites.

### <a name="benefits-of-application-packages"></a>Ventajas de paquetes de aplicaciones

Paquetes de aplicación pueden simplificar el código de la solución de lote y reducir la sobrecarga necesaria para administrar las aplicaciones que se ejecutan las tareas.

Tarea de inicio de la agrupación no tiene que especificar una lista larga de archivos de recursos individuales para instalar en los nodos. No tiene varias versiones de sus archivos de aplicación en el almacenamiento de Azure o en los nodos de administrar manualmente. Y no tienes que preocuparte generar [Direcciones URL SA](../storage/storage-dotnet-shared-access-signature-part-1.md) para proporcionar acceso a los archivos en su cuenta de almacenamiento. Lote funciona en segundo plano con el almacenamiento de Azure para almacenar los paquetes de aplicaciones e implementar para nodos de cálculo.

## <a name="upload-and-manage-applications"></a>Cargar y administrar aplicaciones

Puede usar el [portal de Azure] [ portal] o a la biblioteca de [Lote administración .NET](batch-management-dotnet.md) para administrar los paquetes de aplicaciones en la cuenta de proceso por lotes. En las secciones siguientes, se primero vincular una cuenta de almacenamiento y luego analizar agregando aplicaciones y paquetes y administrarlos con el portal.

### <a name="link-a-storage-account"></a>Vincular una cuenta de almacenamiento

Para usar paquetes de aplicaciones, primero debe vincular una cuenta de Azure almacenamiento a su cuenta de lote. Si aún no ha configurado una cuenta de almacenamiento para su cuenta de proceso por lotes, el portal de Azure mostrará una advertencia de la primera vez que haga clic en el mosaico de **aplicaciones** en el módulo de la **cuenta de proceso por lotes** .

> [AZURE.IMPORTANT] Lote actualmente admite *únicamente* el tipo de cuenta de almacenamiento de **Propósito General** como se describe en el paso 5, [crear una cuenta de almacenamiento](../storage/storage-create-storage-account.md#create-a-storage-account), en [Azure sobre cuentas de almacenamiento](../storage/storage-create-storage-account.md). Al vincular una cuenta de Azure almacenamiento a su cuenta de proceso por lotes, vínculo *solo* una cuenta de almacenamiento de **Propósito General** .

![Ninguna advertencia de almacenamiento cuenta configurada en el portal de Azure][9]

El servicio de lote utiliza la cuenta de almacenamiento asociada para el almacenamiento y la recuperación de paquetes de aplicaciones. Después de que se han vinculado las dos cuentas, lote puede implementar automáticamente los paquetes almacenados en la cuenta de almacenamiento vinculada a los nodos de cálculo. Haga clic en **configuración de la cuenta de almacenamiento** en el módulo de **Advertencia** y, a continuación, haga clic en **Cuenta de almacenamiento** en el módulo de **Cuenta de almacenamiento** para vincular una cuenta de almacenamiento a su cuenta de lote.

![Elija el módulo de la cuenta de almacenamiento en el portal de Azure][10]

Le recomendamos que cree un almacenamiento cuenta *específicamente* para su uso con su cuenta de lote y seleccione de aquí. Para obtener más información sobre cómo crear una cuenta de almacenamiento, consulte "Crear una cuenta de almacenamiento" en [Azure sobre cuentas de almacenamiento](../storage/storage-create-storage-account.md). Una vez que haya creado una cuenta de almacenamiento, puede vincularlo a continuación, su cuenta de lote usando el módulo de la **Cuenta de almacenamiento** .

> [AZURE.WARNING] Debido a que por lotes utiliza el almacenamiento de Azure para almacenar los paquetes de aplicaciones, es [cargará normalidad] [ storage_pricing] para los datos de blob de bloque. Asegúrese de tener en cuenta el tamaño y el número de los paquetes de aplicaciones y quitar periódicamente paquetes desusados para minimizar el costo.

### <a name="view-current-applications"></a>Aplicaciones de la vista actuales

Para ver las aplicaciones en su cuenta por lotes, haga clic en el elemento de menú de **aplicaciones** en el menú izquierdo mientras visualiza el módulo de la **cuenta de proceso por lotes** .

![Mosaico de aplicaciones][2]

Se abrirá el módulo de **aplicaciones** :

![Aplicaciones de la lista][3]

El módulo de **aplicaciones** muestra el identificador de cada aplicación en su cuenta y las siguientes propiedades:

* **Paquetes**: el número de versiones asociado con esta aplicación.
* **Versión predeterminada**, la versión que se instalará si no especifica una versión cuando se establece la aplicación de un grupo. Esta opción es opcional.
* **Permitir actualizaciones**, el valor que especifica si actualizaciones del paquete, las eliminaciones y las adiciones permitidos. Si se establece en **No**, las eliminaciones y actualizaciones de paquete están deshabilitadas para la aplicación. Pueden agregarse solo versiones nuevas de paquete de aplicación. El valor predeterminado es **Sí**.

### <a name="view-application-details"></a>Ver detalles de la aplicación

Haga clic en una aplicación en el módulo de **aplicaciones** para abrir el módulo que incluye los detalles de la aplicación.

![Detalles de la aplicación][4]

En el módulo de detalles de la aplicación, puede configurar las siguientes opciones de la aplicación.

* **Permitir actualizaciones**--especificar si se pueden actualizar o eliminar sus paquetes de aplicación. Consulte "Actualizar o eliminar un paquete de aplicación" más adelante en este artículo.
* **Versión predeterminada**: especifique un paquete de aplicación predeterminada para implementar nodos de cálculo.
* **Nombre para mostrar**: especifique un "descriptivo" nombre que el lote de solución puede usar cuando se muestra información sobre la aplicación, como la interfaz de usuario de un servicio que proporciona a los clientes a través de lotes.

### <a name="add-a-new-application"></a>Agregar una nueva aplicación

Para crear una nueva aplicación, agregue un paquete de aplicación y especifique un identificador único nueva aplicación. El primer paquete de aplicación que se agrega con el nuevo ID de aplicación también crea la nueva aplicación.

Haga clic en **Agregar** en el módulo de **aplicaciones** para abrir el módulo de la **nueva aplicación** .

![Nuevo módulo de aplicación en el portal de Azure][5]

El módulo de **nueva aplicación** proporciona los siguientes campos para especificar la configuración de la nueva aplicación y el paquete de aplicación.

**Identificador de la aplicación**

Este campo especifica el identificador de la nueva aplicación, que está sujeta a las reglas de validación de ID de lote de Azure estándares:

* Puede contener cualquier combinación de caracteres alfanuméricos, incluidos los guiones y caracteres de subrayado.
* No puede contener más de 64 caracteres.
* Debe ser único dentro de la cuenta de proceso por lotes.
* Es conservar mayúsculas y minúsculas y mayúsculas y minúsculas en minúsculas.

**Versión**

Especifica la versión del paquete de aplicación que está cargando. Cadenas de versión están sujetos a las siguientes reglas de validación:

* Puede contener cualquier combinación de caracteres alfanuméricos, incluidos los guiones, caracteres de subrayado y puntos.
* No puede contener más de 64 caracteres.
* Debe ser único dentro de la aplicación.
* Mayúsculas o minúsculas conservación y distingue mayúsculas de minúsculas.

**Paquete de aplicación**

Este campo especifica el archivo .zip que contiene los archivos binarios y los archivos auxiliares que son necesarios para ejecutar la aplicación. Haga clic en el cuadro **Seleccionar un archivo** o en el icono de carpeta para buscar y seleccionar un archivo .zip que contiene los archivos de la aplicación.

Después de seleccionar un archivo, haga clic en **Aceptar** para comenzar la carga con el almacenamiento de Azure. Una vez completada la operación de carga, se le notificará y se cerrará el módulo. Dependiendo del tamaño del archivo que va a cargar y la velocidad de la conexión de red, esta operación puede tardar algún tiempo.

> [AZURE.WARNING] Antes de completar la operación de carga, no se cierra el módulo de la **nueva aplicación** . Si lo hace, detendrá el proceso de carga.

### <a name="add-a-new-application-package"></a>Agregar un nuevo paquete de aplicación

Para agregar una nueva versión del paquete de aplicación para una aplicación existente, seleccione una aplicación en el módulo de **aplicaciones** , haga clic en **paquetes**, haga clic en **Agregar** para abrir el módulo de **Agregar paquete** .

![Agregar módulo de paquete de aplicación en el portal de Azure][8]

Como puede ver, los campos coinciden con los de la hoja **nueva aplicación** , pero está deshabilitado el cuadro **id de aplicación** . Al igual que para la nueva aplicación, especificar la **versión** para el paquete de nuevo, busque el archivo .zip de **paquete de aplicación** y luego haga clic en **Aceptar** para cargar el paquete.

### <a name="update-or-delete-an-application-package"></a>Actualizar o eliminar un paquete de aplicación

Para actualizar o eliminar un paquete de aplicación existente, abra el módulo de detalles de la aplicación, haga clic en **paquetes** para abrir el módulo de **paquetes** , haga clic en los **puntos suspensivos** en la fila del paquete de aplicación que desea modificar y seleccione la acción que desee realizar.

![Actualizar o eliminar el paquete en el portal de Azure][7]

**Actualización**

Al hacer clic en **Actualizar**, se muestra el módulo de *paquete de actualización* . Este módulo es similar a la del módulo *nuevo paquete de aplicación* , pero solo el campo de selección de paquete está habilitado, que le permite especificar un nuevo archivo ZIP para cargar.

![Módulo de paquete de actualización en el portal de Azure][11]

**Eliminar**

Al hacer clic en **Eliminar**, se le pedirá que confirme la eliminación de la versión del paquete y lote elimina el paquete de almacenamiento de Azure. Si elimina la versión predeterminada de una aplicación, se quita la configuración de la **versión predeterminada** de la aplicación.

![Eliminar aplicación][12]

## <a name="install-applications-on-compute-nodes"></a>Instalar aplicaciones en los nodos de cálculo

Ahora que ha visto cómo administrar paquetes de aplicaciones en el portal de Azure, veremos cómo implementar para los nodos de cálculo y ejecutarlos con las tareas por lotes.

### <a name="install-pool-application-packages"></a>Instalar paquetes de aplicación de grupo

Para instalar un paquete de aplicación en todos los nodos de cálculo en un grupo, especifique una o más aplicaciones paquete *referencias* para el grupo. Los paquetes de aplicaciones que se especifican para un grupo se instalan en cada nodo de cálculo cuando une a ese nodo del grupo y, cuando se reinicia el nodo o se vuelve.

En .NET por lotes, especifique una o más [CloudPool][net_cloudpool]. [ApplicationPackageReferences] [net_cloudpool_pkgref] al crear un nuevo grupo, o para un grupo existente. La [ApplicationPackageReference] [ net_pkgref] clase especifica un identificador de la aplicación y versión instalar en un grupo de nodos de cálculo.

```csharp
// Create the unbound CloudPool
CloudPool myCloudPool =
    batchClient.PoolOperations.CreatePool(
        poolId: "myPool",
        targetDedicated: "1",
        virtualMachineSize: "small",
        cloudServiceConfiguration: new CloudServiceConfiguration(osFamily: "4"));

// Specify the application and version to install on the compute nodes
myCloudPool.ApplicationPackageReferences = new List<ApplicationPackageReference>
{
    new ApplicationPackageReference {
        ApplicationId = "litware",
        Version = "1.1001.2b" }
};

// Commit the pool so that it's created in the Batch service. As the nodes join
// the pool, the specified application package will be installed on each.
await myCloudPool.CommitAsync();
```

>[AZURE.IMPORTANT] Si se produce un error en una implementación de paquete de aplicación por cualquier motivo, el servicio de lote marca el nodo de [inutilizable][net_nodestate], y no hay ninguna tarea se programará para ejecución en ese nodo. En este caso, debe **reiniciar** el nodo para reiniciar la implementación del paquete. Reiniciar el nodo también le permitirá la programación de tareas de nuevo en el nodo.

### <a name="install-task-application-packages"></a>Instalar paquetes de aplicación de la tarea

De forma similar a un grupo, especifique de paquete de aplicación *referencias* para una tarea. Cuando una tarea está programada para que se ejecute en un nodo, el paquete se descargan y extraído justo antes de línea de comandos de la tarea. Si un paquete especificada y la versión ya está instalado en el nodo, no se descarga el paquete y se usa el paquete existente.

Para instalar un paquete de aplicación de la tarea, configure la tarea [CloudTask][net_cloudtask]. [ApplicationPackageReferences] [net_cloudtask_pkgref] propiedad:

```csharp
CloudTask task =
    new CloudTask(
        "litwaretask001",
        "cmd /c %AZ_BATCH_APP_PACKAGE_LITWARE%\\litware.exe -args -here");

task.ApplicationPackageReferences = new List<ApplicationPackageReference>
{
    new ApplicationPackageReference
    {
        ApplicationId = "litware",
        Version = "1.1001.2b"
    }
};
```

## <a name="execute-the-installed-applications"></a>Ejecutar las aplicaciones instaladas

Los paquetes que ha especificado para un grupo o tarea se descargan y se extraen en un directorio con nombre dentro de la `AZ_BATCH_ROOT_DIR` del nodo. Proceso por lotes también crea una variable de entorno que contiene la ruta del directorio con nombre. Líneas de comandos de la tarea use esta variable de entorno al hacer referencia a la aplicación en el nodo. La variable está en el siguiente formato:

`AZ_BATCH_APP_PACKAGE_APPLICATIONID#version`

`APPLICATIONID`y `version` son valores que se corresponden con la versión de paquete y la aplicación que ha especificado para su implementación. Por ejemplo, si ha especificado que la versión 2.7 de aplicación *mezcladora* debe estar instalado, las líneas de comandos de tarea debería usar esta variable de entorno para tener acceso a sus archivos:

`AZ_BATCH_APP_PACKAGE_BLENDER#2.7`

Si se especifica una versión predeterminada para una aplicación, puede omitir el sufijo de versión. Por ejemplo, si establece "2.7" como la versión predeterminada para aplicación *mezcladora*, pueden hacer referencia a la siguiente variable de entorno las tareas y se ejecutarán versión 2.7:

`AZ_BATCH_APP_PACKAGE_BLENDER`

Fragmento de código siguiente muestra una línea de comandos de tarea de ejemplo que inicia la versión predeterminada de la aplicación de *mezcladora* :

```csharp
string taskId = "blendertask01";
string commandLine =
    @"cmd /c %AZ_BATCH_APP_PACKAGE_BLENDER%\blender.exe -args -here";
CloudTask blenderTask = new CloudTask(taskId, commandLine);
```

> [AZURE.TIP] Ver la [configuración de entorno para las tareas](batch-api-basics.md#environment-settings-for-tasks) de la [Introducción de la característica de lote](batch-api-basics.md) para obtener más información sobre la configuración de entorno de nodo de cálculo.

## <a name="update-a-pools-application-packages"></a>Actualizar los paquetes de aplicación de la agrupación

Si ya ha configurado un grupo existente con un paquete de aplicación, puede especificar un nuevo paquete para el grupo. Si especifica una nueva referencia de paquete para un grupo, las condiciones siguientes:

* Todos los nodos nuevos que unirse a la agrupación y cualquier nodo existente que se reinicia o renovado instalará el paquete recién especificado.
* Calcular los nodos que ya están en el grupo cuando se actualizan las referencias de paquete no instalación automáticamente el nuevo paquete de aplicación. Calcular el nodos se deben reiniciar o renovados para recibir el nuevo paquete.
* Cuando se implementa un nuevo paquete, las variables de entorno creado reflejan las nuevas referencias de paquete de aplicación.

En este ejemplo, el grupo existente tiene la versión 2.7 de la aplicación de *mezcladora* configurada como uno de sus [CloudPool][net_cloudpool]. [ApplicationPackageReferences] [net_cloudpool_pkgref]. Para actualizar los nodos del grupo con versión 2.76b, especifique una nueva [ApplicationPackageReference] [ net_pkgref] con la nueva versión y confirmar el cambio.

```csharp
string newVersion = "2.76b";
CloudPool boundPool = await batchClient.PoolOperations.GetPoolAsync("myPool");
boundPool.ApplicationPackageReferences = new List<ApplicationPackageReference>
{
    new ApplicationPackageReference {
        ApplicationId = "blender",
        Version = newVersion }
};
await boundPool.CommitAsync();
```

Ahora que se ha configurado la nueva versión, cualquier nodo *nuevo* que se une el grupo tendrá versión 2.76b implementado en él. Para instalar 2.76b en los nodos que están *ya* en el grupo, reinicie o crear una nueva imagen ellos. Tenga en cuenta que los nodos reinicio conservará los archivos desde implementaciones de paquete anterior.

## <a name="list-the-applications-in-a-batch-account"></a>Lista de las aplicaciones en una cuenta de proceso por lotes

Puede enumerar las aplicaciones y los paquetes de una cuenta de proceso por lotes mediante la [ApplicationOperations][net_appops]. [ListApplicationSummaries] [net_appops_listappsummaries] método.

```csharp
// List the applications and their application packages in the Batch account.
List<ApplicationSummary> applications = await batchClient.ApplicationOperations.ListApplicationSummaries().ToListAsync();
foreach (ApplicationSummary app in applications)
{
    Console.WriteLine("ID: {0} | Display Name: {1}", app.Id, app.DisplayName);

    foreach (string version in app.Versions)
    {
        Console.WriteLine("  {0}", version);
    }
}
```

## <a name="wrap-up"></a>Conclusión

Con los paquetes de aplicación, puede ayudar a los clientes seleccionar las aplicaciones de su trabajo y especifique la versión exacta usar al procesamiento de tareas con el servicio de lote activado. También puede proporcionar la capacidad de los clientes cargar y realizar un seguimiento de sus propias aplicaciones en el servicio.

## <a name="next-steps"></a>Pasos siguientes

* La [API de REST de lote] [ api_rest] también proporciona compatibilidad para trabajar con los paquetes de aplicaciones. Por ejemplo, consulte la [applicationPackageReferences] [ rest_add_pool_with_packages] elemento en [Agregar un grupo a una cuenta de] [ rest_add_pool] para obtener información sobre cómo especificar paquetes para instalar mediante la API de REST. Vea [aplicaciones] [ rest_applications] para obtener más información acerca de cómo obtener información de la aplicación con la API de REST de lote.

* Obtenga información sobre cómo mediante programación [Administrar cuentas de Azure lote y las cuotas de lote administración .NET](batch-management-dotnet.md). El [Lote de administración .NET] [ api_net_mgmt] biblioteca puede habilitar las características de creación y eliminación de cuenta para la aplicación del lote o servicio.

[api_net]: http://msdn.microsoft.com/library/azure/mt348682.aspx
[api_net_mgmt]: https://msdn.microsoft.com/library/azure/mt463120.aspx
[api_rest]: http://msdn.microsoft.com/library/azure/dn820158.aspx
[batch_mgmt_nuget]: https://www.nuget.org/packages/Microsoft.Azure.Management.Batch/
[github_samples]: https://github.com/Azure/azure-batch-samples
[storage_pricing]: https://azure.microsoft.com/pricing/details/storage/
[net_appops]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.applicationoperations.aspx
[net_appops_listappsummaries]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.applicationoperations.listapplicationsummaries.aspx
[net_cloudpool]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.aspx
[net_cloudpool_pkgref]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.applicationpackagereferences.aspx
[net_cloudtask]: https://msdn.microsoft.com/library/microsoft.azure.batch.cloudtask.aspx
[net_cloudtask_pkgref]: https://msdn.microsoft.com/library/microsoft.azure.batch.cloudtask.applicationpackagereferences.aspx
[net_nodestate]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.computenode.state.aspx
[net_pkgref]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.applicationpackagereference.aspx
[portal]: https://portal.azure.com
[rest_applications]: https://msdn.microsoft.com/library/azure/mt643945.aspx
[rest_add_pool]: https://msdn.microsoft.com/library/azure/dn820174.aspx
[rest_add_pool_with_packages]: https://msdn.microsoft.com/library/azure/dn820174.aspx#bk_apkgreference

[1]: ./media/batch-application-packages/app_pkg_01.png "Diagrama de alto nivel de paquetes de aplicaciones"
[2]: ./media/batch-application-packages/app_pkg_02.png "Mosaico de aplicaciones en el portal de Azure"
[3]: ./media/batch-application-packages/app_pkg_03.png "Módulo de aplicaciones en el portal de Azure"
[4]: ./media/batch-application-packages/app_pkg_04.png "Módulo de detalles de la aplicación en el portal de Azure"
[5]: ./media/batch-application-packages/app_pkg_05.png "Nuevo módulo de aplicación en el portal de Azure"
[7]: ./media/batch-application-packages/app_pkg_07.png "Actualizar o eliminar paquetes desplegable en el portal de Azure"
[8]: ./media/batch-application-packages/app_pkg_08.png "Nuevo módulo de paquete de aplicación en el portal de Azure"
[9]: ./media/batch-application-packages/app_pkg_09.png "Ninguna alerta de cuenta vinculada de almacenamiento"
[10]: ./media/batch-application-packages/app_pkg_10.png "Elija el módulo de la cuenta de almacenamiento en el portal de Azure"
[11]: ./media/batch-application-packages/app_pkg_11.png "Módulo de paquete de actualización en el portal de Azure"
[12]: ./media/batch-application-packages/app_pkg_12.png "Eliminar el cuadro de diálogo de confirmación de paquete en el portal de Azure"
