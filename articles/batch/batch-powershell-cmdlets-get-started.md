<properties
   pageTitle="Introducción a PowerShell lote de Azure | Microsoft Azure"
   description="Una rápida introducción a los cmdlets de PowerShell de Azure que puede usar para administrar el servicio de proceso de Azure"
   services="batch"
   documentationCenter=""
   authors="mmacy"
   manager="timlt"
   editor=""/>

<tags
   ms.service="batch"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="powershell"
   ms.workload="big-compute"
   ms.date="10/20/2016"
   ms.author="marsma"/>

# <a name="get-started-with-azure-batch-powershell-cmdlets"></a>Introducción a los cmdlets de PowerShell lote de Azure
Con los cmdlets de PowerShell lote de Azure, puede realizar y script muchas de las tareas que lleva a cabo con las API de proceso por lotes, el portal de Azure y la interfaz de línea de comandos de Azure (CLI). Se trata de una introducción rápida a los cmdlets que se puede usar para administrar las cuentas de lote y trabajar con los recursos de proceso por lotes como grupos, tareas y tareas.

Para obtener una lista completa de los cmdlets del lote y la sintaxis de cmdlet detallada, vea la [referencia de los cmdlets lote de Azure](https://msdn.microsoft.com/library/azure/mt125957.aspx).

En este artículo se basa en cmdlets de PowerShell de Azure versión 3.0.0. Le recomendamos que actualice su PowerShell Azure con frecuencia para aprovechar las mejoras y actualizaciones de servicio.

## <a name="prerequisites"></a>Requisitos previos

Realizar las siguientes operaciones para usar Azure PowerShell para administrar los recursos del lote.

* [Instalar y configurar PowerShell de Azure](../powershell-install-configure.md)

* Ejecute el cmdlet **AzureRmAccount de inicio de sesión** para conectarse a su suscripción (envío de cmdlets de Azure lote en el módulo de administrador de recursos de Azure):

    `Login-AzureRmAccount`

* **Registrar el espacio de nombres del proveedor de lote**. Esta operación solo tiene que realizar **una vez por suscripción**.

    `Register-AzureRMResourceProvider -ProviderNamespace Microsoft.Batch`

## <a name="manage-batch-accounts-and-keys"></a>Administrar cuentas de lote y claves

### <a name="create-a-batch-account"></a>Crear una cuenta de proceso por lotes

**Nuevo AzureRmBatchAccount** crea una cuenta de proceso por lotes en un grupo de recursos especificado. Si todavía no tiene un grupo de recursos, crearla, ejecute el cmdlet [AzureRmResourceGroup de nuevo](https://msdn.microsoft.com/library/azure/mt603739.aspx) . Especifique una de las regiones de Azure en el parámetro de **ubicación** , como "US Central". Por ejemplo:

    New-AzureRmResourceGroup –Name MyBatchResourceGroup –location "Central US"

A continuación, cree una cuenta de lote en el grupo de recursos, especifique un nombre para la cuenta en <*account_name*> y la ubicación y el nombre de su grupo de recursos. Crear la cuenta del lote puede tardar algún tiempo en completarse. Por ejemplo:

    New-AzureRmBatchAccount –AccountName <account_name> –Location "Central US" –ResourceGroupName <res_group_name>

> [AZURE.NOTE] El nombre de cuenta del lote debe ser único en la región de Azure del grupo de recursos, contienen entre 3 y 24 caracteres y usar letras minúsculas y solo los números.

### <a name="get-account-access-keys"></a>Obtener las teclas de acceso de cuenta
**Get-AzureRmBatchAccountKeys** muestra las teclas de acceso asociadas a una cuenta de Azure lote. Por ejemplo, ejecute el siguiente procedimiento para obtener la clave principal y secundaria de la cuenta que creó.

    $Account = Get-AzureRmBatchAccountKeys –AccountName <account_name>

    $Account.PrimaryAccountKey

    $Account.SecondaryAccountKey

### <a name="generate-a-new-access-key"></a>Generar una nueva clave de acceso
**Nuevo AzureRmBatchAccountKey** genera una nueva clave de cuenta primaria o secundaria para una cuenta de Azure lote. Por ejemplo, para generar una nueva clave principal de la cuenta de proceso por lotes, escriba:

    New-AzureRmBatchAccountKey -AccountName <account_name> -KeyType Primary

> [AZURE.NOTE] Para generar una nueva clave secundaria, especifique "Secundario" para el parámetro de **tipo de clave** . Tiene que volver a crear las claves principales y secundarias por separado.

### <a name="delete-a-batch-account"></a>Eliminar una cuenta de proceso por lotes
**Quitar AzureRmBatchAccount** elimina una cuenta de proceso por lotes. Por ejemplo:

    Remove-AzureRmBatchAccount -AccountName <account_name>

Cuando se le solicite, confirme que desea quitar la cuenta. Eliminación de cuenta puede tardar algún tiempo en completarse.

## <a name="create-a-batchaccountcontext-object"></a>Crear un objeto BatchAccountContext

Para autenticar mediante los cmdlets de PowerShell lote al crear y administrar grupos por lotes, tareas, tareas y otros recursos, crear un objeto BatchAccountContext para almacenar el nombre de la cuenta y las claves:

    $context = Get-AzureRmBatchAccountKeys -AccountName <account_name>

Pase el objeto BatchAccountContext cmdlets que usan el parámetro **BatchContext** .

> [AZURE.NOTE] De forma predeterminada, se utiliza la clave principal de la cuenta para la autenticación, pero se puede seleccionar explícitamente la clave para usar cambiando su BatchAccountContext **KeyInUse** propiedad objeto: `$context.KeyInUse = "Secondary"`.

## <a name="create-and-modify-batch-resources"></a>Crear y modificar recursos por lotes
Usar cmdlets como **Nuevo AzureBatchPool**, **AzureBatchJob de nuevo**y **AzureBatchTask de nuevo** para crear recursos en una cuenta de proceso por lotes. Existen complementarios **Get -** y cmdlets **Set -** actualizar las propiedades de los recursos existentes y cmdlets de **Quitar** para quitar los recursos en una cuenta de proceso por lotes.

Cuando se usa muchos de estos cmdlets, además de pasar un objeto BatchContext, debe crear o pasar objetos que contienen la configuración de recursos detallados, tal como se muestra en el ejemplo siguiente. Consulte la ayuda detallada de cada cmdlet para obtener otros ejemplos.

### <a name="create-a-batch-pool"></a>Crear un grupo por lotes

Al crear o actualizar un grupo por lotes, para seleccionar una configuración de servicio de nube o una configuración de máquina virtual para el sistema operativo de los nodos de cálculo (consulte [Introducción a las características lote](batch-api-basics.md#pool)). Su elección determina si se representan los nodos de cálculo con una de las [versiones de sistema operativo de invitado de Azure](../cloud-services/cloud-services-guestos-update-matrix.md#releases) o con una de las imágenes compatibles Linux o máquina virtual de Windows Azure Marketplace.

Cuando se ejecuta **AzureBatchPool de nuevo**, pase la configuración del sistema operativo de un objeto PSCloudServiceConfiguration o PSVirtualMachineConfiguration. Por ejemplo, el siguiente cmdlet crea un nuevo grupo de lote con nodos de proceso pequeña de tamaño en la configuración de servicio de nube, una imagen con la última versión de sistema operativo de la familia 3 (Windows Server 2012). Aquí, el parámetro **CloudServiceConfiguration** especifica la variable *$configuration* como el objeto PSCloudServiceConfiguration. El parámetro **BatchContext** especifica una variable definidas previamente *$context* como el objeto BatchAccountContext.

    $configuration = New-Object -TypeName "Microsoft.Azure.Commands.Batch.Models.PSCloudServiceConfiguration" -ArgumentList @(4,"*")

    New-AzureBatchPool -Id "AutoScalePool" -VirtualMachineSize "Small" -CloudServiceConfiguration $configuration -AutoScaleFormula '$TargetDedicated=4;' -BatchContext $context

El número de nodos de cálculo en el nuevo grupo de destino depende de una fórmula de ajuste automático. En este caso, la fórmula es simplemente **$TargetDedicated = 4**, que indica el número de nodos de cálculo en el grupo es 4 como máximo.

## <a name="query-for-pools-jobs-tasks-and-other-details"></a>Consulta de grupos, tareas, tareas y otros detalles

Usar cmdlets como **Get-AzureBatchPool**, **Get-AzureBatchJob**y **Get-AzureBatchTask** a consulta de entidades creadas en una cuenta de proceso por lotes.

### <a name="query-for-data"></a>Consulta de datos

Por ejemplo, use **Get-AzureBatchPools** para encontrar los grupos. De forma predeterminada este consultas para todos los grupos en su cuenta, suponiendo que ya almacenan el objeto BatchAccountContext en *$context*:

    Get-AzureBatchPool -BatchContext $context

### <a name="use-an-odata-filter"></a>Usar un filtro de OData

Puede proporcionar un filtro de OData mediante el parámetro de **filtro** para buscar solo los objetos que le interesa. Por ejemplo, puede buscar todos los grupos con identificadores comenzando por "myPool":

    $filter = "startswith(id,'myPool')"

    Get-AzureBatchPool -Filter $filter -BatchContext $context

Este método no es tan flexible como con "Where-Object" en una canalización local. Sin embargo, la consulta se envía al servicio de lote directamente para que todos los filtros, ocurre en el servidor, ahorrando ancho de banda de Internet.

### <a name="use-the-id-parameter"></a>Usar el parámetro de Id.

Una alternativa a un filtro de OData es usar el parámetro de **Id** . Para consultar un grupo específico con identificador "myPool":

    Get-AzureBatchPool -Id "myPool" -BatchContext $context

Es compatible con el parámetro **Id** sólo búsqueda de identificador completo, no caracteres comodín o filtros de estilo de OData.

### <a name="use-the-maxcount-parameter"></a>Usar el parámetro MaxCount

De forma predeterminada, cada cmdlet devuelve un máximo de 1000 objetos. Si alcanza el límite, refinar el filtro para traer menos volver objetos o establecidos explícitamente utilizando el parámetro **MaxCount** como máximo. Por ejemplo:

    Get-AzureBatchTask -MaxCount 2500 -BatchContext $context

Para quitar el límite superior, establezca **MaxCount** en 0 o menos.

### <a name="use-the-powershell-pipeline"></a>Usar la canalización de PowerShell

Cmdlets de lote puede aprovechar la canalización de PowerShell para enviar datos entre los cmdlets. Tiene el mismo efecto que especificar un parámetro, pero facilita el trabajo con varias entidades.

Por ejemplo, buscar y visualizar todas las tareas en su cuenta:

    Get-AzureBatchJob -BatchContext $context | Get-AzureBatchTask -BatchContext $context

Reiniciar (reiniciar) cada nodo de cálculo en un grupo:

    Get-AzureBatchComputeNode -PoolId "myPool" -BatchContext $context | Restart-AzureBatchComputeNode -BatchContext $context

## <a name="application-package-management"></a>Administración del paquete de aplicaciones

Paquetes de aplicaciones proporcionan una manera simplificada para implementar aplicaciones en los nodos de cálculo de los grupos. Con los cmdlets de PowerShell lote, puede cargar y administrar paquetes de aplicaciones en la cuenta del lote e implementar versiones de paquete para nodos de cálculo.

**Crear** una aplicación:

    New-AzureRmBatchApplication -AccountName <account_name> -ResourceGroupName <res_group_name> -ApplicationId "MyBatchApplication"

**Agregar** un paquete de aplicación:

    New-AzureRmBatchApplicationPackage -AccountName <account_name> -ResourceGroupName <res_group_name> -ApplicationId "MyBatchApplication" -ApplicationVersion "1.0" -Format zip -FilePath package001.zip

Establezca la **versión predeterminada** de la aplicación:

    Set-AzureRmBatchApplication -AccountName <account_name> -ResourceGroupName <res_group_name> -ApplicationId "MyBatchApplication" -DefaultVersion "1.0"

**Lista de** paquetes de la aplicación

    $application = Get-AzureRmBatchApplication -AccountName <account_name> -ResourceGroupName <res_group_name> -ApplicationId "MyBatchApplication"

    $application.ApplicationPackages

**Eliminar** un paquete de aplicación

    Remove-AzureRmBatchApplicationPackage -AccountName <account_name> -ResourceGroupName <res_group_name> -ApplicationId "MyBatchApplication" -ApplicationVersion "1.0"

**Eliminar** una aplicación

    Remove-AzureRmBatchApplication -AccountName <account_name> -ResourceGroupName <res_group_name> -ApplicationId "MyBatchApplication"

>[AZURE.NOTE] Debe eliminar todas las versiones de paquete de aplicación de la aplicación antes de eliminar la aplicación. Si intenta eliminar una aplicación que tiene actualmente paquetes de aplicaciones, recibirá un error 'Conflicto'.

### <a name="deploy-an-application-package"></a>Implementar un paquete de aplicación

Puede especificar uno o varios paquetes de aplicación para su implementación al crear un grupo. Al especificar un paquete en tiempo de creación de grupo, se implementa a cada nodo como la agrupación de combinaciones de nodo. También se despliegan los paquetes cuando se reinicia un nodo o se rehace su imagen.

Especifique la `-ApplicationPackageReference` opción para crear un grupo para implementar un paquete de aplicación a los nodos del grupo como unirse a la agrupación. En primer lugar, cree un objeto **PSApplicationPackageReference** y configurar con la versión de identificador y el paquete de aplicación que desea implementar en los nodos de cálculo del grupo:

    $appPackageReference = New-Object Microsoft.Azure.Commands.Batch.Models.PSApplicationPackageReference

    $appPackageReference.ApplicationId = "MyBatchApplication"

    $appPackageReference.Version = "1.0"

Ahora crear el grupo y especifique el objeto de referencia de paquete como argumento para la `ApplicationPackageReferences` opción:

    New-AzureBatchPool -Id "PoolWithAppPackage" -VirtualMachineSize "Small" -CloudServiceConfiguration $configuration -BatchContext $context -ApplicationPackageReferences $appPackageReference

Puede encontrar más información sobre los paquetes de aplicación de [implementación de la aplicación con los paquetes de aplicaciones de Azure lote](batch-application-packages.md).

>[AZURE.IMPORTANT] Debe [vincular una cuenta de Azure almacenamiento](#linked-storage-account-autostorage) a su cuenta de lote para usar paquetes de aplicaciones.

### <a name="update-a-pools-application-packages"></a>Actualizar los paquetes de aplicación de la agrupación

Para actualizar las aplicaciones asignadas a un grupo existente, cree primero un objeto PSApplicationPackageReference con las propiedades que desee (Id y paquete de versión de la aplicación):

    $appPackageReference = New-Object Microsoft.Azure.Commands.Batch.Models.PSApplicationPackageReference

    $appPackageReference.ApplicationId = "MyBatchApplication"

    $appPackageReference.Version = "2.0"

A continuación, obtener la agrupación de lote, borrar los paquetes existentes, agregar nuestra nueva referencia de paquete y actualizar el servicio de lote con la nueva configuración de grupo:

    $pool = Get-AzureBatchPool -BatchContext $context -Id "PoolWithAppPackage"

    $pool.ApplicationPackageReferences.Clear()

    $pool.ApplicationPackageReferences.Add($appPackageReference)

    Set-AzureBatchPool -BatchContext $context -Pool $pool

Ahora ha actualizado propiedades del grupo en el servicio de lote. Para implementar el paquete de aplicación nuevos nodos en el grupo de cálculo, sin embargo, debe reiniciar o crear una nueva imagen los nodos. Puede reiniciar todos los nodos en un grupo con este comando:

    Get-AzureBatchComputeNode -PoolId "PoolWithAppPackage" -BatchContext $context | Restart-AzureBatchComputeNode -BatchContext $context

>[AZURE.TIP] Puede implementar varios paquetes de aplicación a los nodos de cálculo en un grupo. Si desea *Agregar* un paquete de aplicación en lugar de reemplazar los paquetes de implementada actualmente, omita el `$pool.ApplicationPackageReferences.Clear()` línea anterior.

## <a name="next-steps"></a>Pasos siguientes

* Para sintaxis cmdlet detallada y ejemplos, vea [referencia de los cmdlets lote de Azure](https://msdn.microsoft.com/library/azure/mt125957.aspx).

* Para obtener más información sobre los paquetes de aplicación en el lote y aplicaciones, vea [implementación de aplicaciones con los paquetes de aplicaciones de Azure lote](batch-application-packages.md).
