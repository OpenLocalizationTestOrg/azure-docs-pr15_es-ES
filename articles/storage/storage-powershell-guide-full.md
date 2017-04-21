<properties
    pageTitle="Usar PowerShell Azure con el almacenamiento de Azure | Microsoft Azure"
    description="Aprenda a usar los cmdlets de PowerShell de Azure para el almacenamiento de Azure para crear y administrar cuentas de almacenamiento; trabajar con BLOB, tablas, colas y archivos; configurar el análisis de almacenamiento de la consulta y crear firmas de acceso compartido."
    services="storage"
    documentationCenter="na"
    authors="robinsh"
    manager="carmonm"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/03/2016"
    ms.author="robinsh"/>

# <a name="using-azure-powershell-with-azure-storage"></a>Usar PowerShell Azure con el almacenamiento de Azure

## <a name="overview"></a>Información general

PowerShell Azure es un módulo que proporciona cmdlets para administrar Azure a través de Windows PowerShell. Es un lenguaje de secuencias de comandos diseñadas especialmente para la administración del sistema y shell de línea de comandos basado en tareas. Fácilmente con PowerShell, puede controlar y automatizar la administración de las aplicaciones y servicios de Azure. Por ejemplo, puede usar los cmdlets para realizar las tareas que puede realizar a través del [Portal de Azure](https://portal.azure.com).

En esta guía, veremos cómo usar los [Cmdlets de almacenamiento de Azure](https://msdn.microsoft.com/library/azure/mt269418.aspx) para realizar una gran variedad de tareas de administración y desarrollo con el almacenamiento de Azure.

Esta guía se supone que tiene experiencia anterior con [El almacenamiento de Azure](https://azure.microsoft.com/documentation/services/storage/) y [Windows PowerShell](http://technet.microsoft.com/library/bb978526.aspx). La guía proporciona un número de secuencias de comandos para demostrar el uso de PowerShell con el almacenamiento de Azure. Debe actualizar las variables de secuencia de comandos en función de su configuración antes de ejecutar cada secuencia de comandos.

La primera sección en esta guía proporciona un vistazo rápido a Azure almacenamiento y PowerShell. Para obtener información detallada e instrucciones, inicie desde los [requisitos previos para usar PowerShell Azure con el almacenamiento de Azure](#prerequisites-for-using-azure-powershell-with-azure-storage).


## <a name="getting-started-with-azure-storage-and-powershell-in-5-minutes"></a>Introducción a Azure almacenamiento y PowerShell en 5 minutos

Esta sección muestra cómo tener acceso al almacenamiento de Azure a través de PowerShell en 5 minutos.

**Nuevo en Azure:** Obtener una suscripción de Microsoft Azure y una cuenta de Microsoft asociada con la suscripción. Para obtener información sobre las opciones de compra de Azure, vea [Prueba gratuita](https://azure.microsoft.com/pricing/free-trial/), [Opciones de compra](https://azure.microsoft.com/pricing/purchase-options/)y [Miembro ofrece](https://azure.microsoft.com/pricing/member-offers/) (para los miembros de MSDN, Microsoft Partner Network, BizSpark y otros programas de Microsoft).

Para obtener más información acerca de las suscripciones de Azure, vea [asignación de roles de administrador de Azure Active Directory (AD Azure)](https://msdn.microsoft.com/library/azure/hh531793.aspx) .

**Después de crear una cuenta y la suscripción de Microsoft Azure:**

1.  Descargue e instale [PowerShell de Azure](http://go.microsoft.com/?linkid=9811175&clcid=0x409).
2.  Iniciar Windows PowerShell Scripting entorno integrado (ISE): En el equipo local, vaya al menú **Inicio** . Escriba **Herramientas administrativas** y haga clic en para ejecutarlo. En la ventana **Herramientas administrativas** , haga clic en **Windows PowerShell ISE**, haga clic en **Ejecutar como administrador**.
3.  En **Windows PowerShell ISE**, haga clic en **archivo** > **nuevo** para crear un nuevo archivo de script.
4.  Ahora, le ofrecemos una secuencia de comandos simple que muestra los comandos de PowerShell básicos para tener acceso al almacenamiento de Azure. La secuencia de comandos primero le pedirá su Azure credenciales de cuenta para agregar su Azure cuenta al entorno de PowerShell local. A continuación, la secuencia de comandos establecerá Azure suscripción predeterminado y crear una nueva cuenta de almacenamiento en Azure. A continuación, la secuencia de comandos creará un nuevo contenedor de esta nueva cuenta de almacenamiento y cargar un archivo de imagen existente (blob) en ese contenedor. Después de la secuencia de comandos enumera todos los blobs de ese contenedor, creará un nuevo directorio de destino en el equipo local y descargar el archivo de imagen.
5.  En la siguiente sección de código, seleccione la secuencia de comandos entre los comentarios **#begin** y **#end**. Presione CTRL+C para copiarlo al Portapapeles.

        #begin
        # Update with the name of your subscription.
        $SubscriptionName = "YourSubscriptionName"

        # Give a name to your new storage account. It must be lowercase!
        $StorageAccountName = "yourstorageaccountname"

        # Choose "West US" as an example.
        $Location = "West US"

        # Give a name to your new container.
        $ContainerName = "imagecontainer"

        # Have an image file and a source directory in your local computer.
        $ImageToUpload = "C:\Images\HelloWorld.png"

        # A destination directory in your local computer.
        $DestinationFolder = "C:\DownloadImages"

        # Add your Azure account to the local PowerShell environment.
        Add-AzureAccount

        # Set a default Azure subscription.
        Select-AzureSubscription -SubscriptionName $SubscriptionName –Default

        # Create a new storage account.
        New-AzureStorageAccount –StorageAccountName $StorageAccountName -Location $Location

        # Set a default storage account.
        Set-AzureSubscription -CurrentStorageAccountName $StorageAccountName -SubscriptionName $SubscriptionName

        # Create a new container.
        New-AzureStorageContainer -Name $ContainerName -Permission Off

        # Upload a blob into a container.
        Set-AzureStorageBlobContent -Container $ContainerName -File $ImageToUpload

        # List all blobs in a container.
        Get-AzureStorageBlob -Container $ContainerName

        # Download blobs from the container:
        # Get a reference to a list of all blobs in a container.
        $blobs = Get-AzureStorageBlob -Container $ContainerName

        # Create the destination directory.
        New-Item -Path $DestinationFolder -ItemType Directory -Force  

        # Download blobs into the local destination directory.
        $blobs | Get-AzureStorageBlobContent –Destination $DestinationFolder
        #end

5.  En **Windows PowerShell ISE**, presione CTRL + V para copiar la secuencia de comandos. Haga clic en **archivo** > **Guardar**. En la ventana de diálogo **Guardar como** , escriba el nombre del archivo de script, como "mystoragescript". Haga clic en **Guardar**.

6.  Ahora, deberá actualizar las variables de secuencia de comandos en función de las opciones de configuración. Debe actualizar la variable **$SubscriptionName** con su propia suscripción. Puede mantener las demás variables como se especifica en la secuencia de comandos o actualizarlos como desee.

    - **$SubscriptionName:** Debe actualizar esta variable con su propio nombre de suscripción. Siga uno de las siguientes tres formas para buscar el nombre de su suscripción:

        una. En **Windows PowerShell ISE**, haga clic en **archivo** > **nuevo** para crear un nuevo archivo de script. Copie la siguiente secuencia de comandos para el nuevo archivo de script y haga clic en **Depurar** > **Ejecutar**. La siguiente secuencia de comandos primero le pedirá su Azure credenciales de cuenta para agregar su Azure en el entorno de PowerShell local de la cuenta y, a continuación, mostrar todas las suscripciones que están conectadas a la sesión de PowerShell local. Anote el nombre de la suscripción que desea utilizar mientras sigue este tutorial:

            Add-AzureAccount
                Get-AzureSubscription | Format-Table SubscriptionName, IsDefault, IsCurrent, CurrentStorageAccountName

        b. Para buscar y copiar su nombre de suscripción en el [Portal de Azure](https://portal.azure.com), en el menú de concentrador a la izquierda, haga clic en **suscripciones**. Copiar el nombre de la suscripción que desea usar mientras se ejecuta las secuencias de comandos en esta guía.

        ![Portal de Azure][Image2]

        c. Para buscar y copiar su nombre de suscripción en el [Portal de Azure clásica](https://manage.windowsazure.com/), desplácese hacia abajo y haga clic en **configuración** en el lado izquierdo del portal. Haga clic en **suscripciones** para ver una lista de sus suscripciones. Copiar el nombre de la suscripción que desea usar mientras se ejecuta las secuencias de comandos proporcionadas en esta guía.

        ![Portal de clásico de Azure][Image1]

    - **$StorageAccountName:** Use el nombre especificado en la secuencia de comandos o escriba un nombre nuevo para su cuenta de almacenamiento. **Importante:** El nombre de la cuenta de almacenamiento debe ser único en Azure. ¡Debe ser minúscula, demasiado!

    - **$Location:** Usar determinado "Oeste US" en la secuencia de comandos o elegir otras ubicaciones Azure, como oriental Estados Unidos, Europa del Norte y así sucesivamente.

    - **$ContainerName:** Use el nombre especificado en la secuencia de comandos o escriba un nombre nuevo para el contenedor.

    - **$ImageToUpload:** Escriba una ruta de acceso a una imagen en el equipo local, como por ejemplo: "C:\Images\HelloWorld.png".

    - **$DestinationFolder:** Escriba una ruta de acceso a un directorio local para almacenar los archivos descargados desde el almacenamiento de Azure, como por ejemplo: "C:\DownloadImages".

7.  Después de actualizar las variables de secuencia de comandos en el archivo "mystoragescript.ps1", haga clic en **archivo** > **Guardar**. A continuación, haga clic en **Depurar** > **Ejecutar** o presione **F5** para ejecutar la secuencia de comandos.  

Después de ejecuta la secuencia de comandos, debe tener una carpeta de destino local que incluye el archivo de imagen descargado. La siguiente captura de pantalla muestra un ejemplo de salida:

![Descargar BLOB][Image3]


> [AZURE.NOTE] La sección "Introducción a Azure almacenamiento y PowerShell 5 minutos" proporciona una breve introducción sobre cómo usar PowerShell Azure con el almacenamiento de Azure. Para obtener información detallada e instrucciones, le recomendamos que lea las secciones siguientes.

## <a name="prerequisites-for-using-azure-powershell-with-azure-storage"></a>Requisitos previos para usar PowerShell Azure con el almacenamiento de Azure
Necesita una suscripción de Azure y una cuenta para ejecutar los cmdlets de PowerShell en esta guía, como se describió anteriormente.

PowerShell Azure es un módulo que proporciona cmdlets para administrar Azure a través de Windows PowerShell. Para obtener información sobre cómo instalar y configurar Azure PowerShell, vea [cómo instalar y configurar Azure PowerShell](../powershell-install-configure.md). Se recomienda descargar e instalar o actualizar a la última módulo de PowerShell de Azure antes de utilizar a esta guía.

Puede ejecutar los cmdlets en la consola de Windows PowerShell estándar o el Windows PowerShell Scripting entorno integrado (ISE). Por ejemplo, para abrir **Windows PowerShell ISE**, vaya al menú Inicio, escriba herramientas administrativas y haga clic en para ejecutarlo. En la ventana Herramientas administrativas, haga clic en Windows PowerShell ISE, haga clic en Ejecutar como administrador.

## <a name="how-to-manage-storage-accounts-in-azure"></a>Cómo administrar las cuentas de almacenamiento de Azure

### <a name="how-to-set-a-default-azure-subscription"></a>Cómo configurar una suscripción de Azure predeterminado
Para administrar el almacenamiento de Azure con PowerShell de Azure, debe autenticar su entorno de cliente con Azure a través de autenticación de Azure Active Directory o la autenticación basada en certificados. Para obtener información detallada, vea tutorial de [cómo instalar y configurar Azure PowerShell](../powershell-install-configure.md) . Esta guía utiliza la autenticación de Azure Active Directory.

1.  En Windows PowerShell ISE, escriba el comando siguiente para agregar su Azure en el entorno de PowerShell local de la cuenta:

    `Add-AzureAccount`

2.  En la ventana de "Inicio de sesión en a Microsoft Azure", escriba la dirección de correo electrónico y contraseña asociada a su cuenta. Azure autentica y guarda la información de credenciales y, a continuación, cierra la ventana.

3.  A continuación, ejecute el comando siguiente para ver las cuentas de Azure en su entorno local de PowerShell y compruebe que aparece su cuenta:

    `Get-AzureAccount`

4.  A continuación, ejecute el siguiente cmdlet para ver todas las suscripciones que están conectadas a la sesión de PowerShell local y compruebe que aparece su suscripción:

    `Get-AzureSubscription | Format-Table SubscriptionName, IsDefault, IsCurrent, CurrentStorageAccountName`

5.  Para configurar una suscripción de Azure predeterminado, ejecute el cmdlet de Select AzureSubscription:

        $SubscriptionName = 'Your subscription Name'
        Select-AzureSubscription -SubscriptionName $SubscriptionName –Default

6.  Compruebe el nombre de la suscripción de forma predeterminada, ejecute el cmdlet Get-AzureSubscription:

    `Get-AzureSubscription -Default`

7.  Para ver todos los cmdlets de PowerShell disponibles para el almacenamiento de Azure, ejecute:

    `Get-Command -Module Azure -Noun *Storage*`

### <a name="how-to-create-a-new-azure-storage-account"></a>Cómo crear una nueva cuenta de almacenamiento de Azure
Para usar el almacenamiento de Azure, necesita una cuenta de almacenamiento. Puede crear una nueva cuenta de almacenamiento de Azure después de haber configurado el equipo para conectarse a su suscripción.

1.  Ejecute el cmdlet Get-AzureLocation para buscar todas las ubicaciones del centro de datos disponibles:

    `Get-AzureLocation | Format-Table -Property Name, AvailableServices, StorageAccountTypes`

2.  A continuación, ejecute el cmdlet AzureStorageAccount de nuevo para crear una nueva cuenta de almacenamiento. En el ejemplo siguiente se crea una nueva cuenta de almacenamiento en el centro de datos de "US Oeste".

        $location = "West US"
        $StorageAccountName = "yourstorageaccount"
        New-AzureStorageAccount –StorageAccountName $StorageAccountName -Location $location

> [AZURE.IMPORTANT] El nombre de su cuenta de almacenamiento debe ser único dentro de Azure y debe ser minúscula. Para convenciones de nomenclatura y restricciones, consulte [Acerca de cuentas de almacenamiento de Azure](storage-create-storage-account.md) y [nomenclatura y hace referencia a contenedores, BLOB y metadatos](http://msdn.microsoft.com/library/azure/dd135715.aspx).

### <a name="how-to-set-a-default-azure-storage-account"></a>Cómo configurar una cuenta de Azure almacenamiento predeterminada
Puede tener varias cuentas de almacenamiento en su suscripción. Puede elegir uno de ellos y establecerlo como la cuenta de almacenamiento predeterminada para todos los comandos de almacenamiento en la misma sesión de PowerShell. Esto le permite ejecutar los comandos de PowerShell de Azure almacenamiento sin especificar el contexto de almacenamiento explícitamente.

1.  Para configurar una cuenta de almacenamiento predeterminada para la suscripción, puede ejecutar el cmdlet Set-AzureSubscription.

        $SubscriptionName = "Your subscription name"
        $StorageAccountName = "yourstorageaccount"  
        Set-AzureSubscription -CurrentStorageAccountName $StorageAccountName -SubscriptionName $SubscriptionName

2.  A continuación, ejecute el cmdlet Get-AzureSubscription para comprobar que la cuenta de almacenamiento está asociada a su cuenta de suscripción predeterminada. Este comando devuelve las propiedades de suscripción de la suscripción actual, incluida su cuenta de almacenamiento actual.

        Get-AzureSubscription –Current

### <a name="how-to-list-all-azure-storage-accounts-in-a-subscription"></a>Cómo mostrar todas las cuentas de almacenamiento de Azure en una suscripción
Cada suscripción Azure puede tener hasta 100 cuentas de almacenamiento. Para obtener la información más actualizada sobre los límites, consulte [suscripción de Azure y los límites del servicio, cuotas y las restricciones](../azure-subscription-service-limits.md).

Ejecute el cmdlet siguiente para averiguar el nombre y el estado de las cuentas de almacenamiento en la suscripción actual:

    Get-AzureStorageAccount | Format-Table -Property StorageAccountName, Location, AccountType, StorageAccountStatus

### <a name="how-to-create-an-azure-storage-context"></a>Cómo crear un contexto de almacenamiento de Azure
Contexto de almacenamiento de Azure es un objeto de PowerShell para encapsulados las credenciales de almacenamiento. Mediante el contexto de almacenamiento mientras ejecuta cualquier cmdlet subsiguientes permite autenticar su solicitud sin especificar la cuenta de almacenamiento y su clave de acceso explícitamente. Puede crear un contexto de almacenamiento de muchas formas, como el uso de la clave de nombre y el acceso de la cuenta de almacenamiento, el token de acceso compartido de firma (SA), la cadena de conexión, o anónimos. Para obtener más información, consulte [AzureStorageContext de nuevo](http://msdn.microsoft.com/library/azure/dn806380.aspx).  

Utilice una de las siguientes tres formas para crear un contexto de almacenamiento:

- Ejecute el cmdlet [Get-AzureStorageKey](http://msdn.microsoft.com/library/azure/dn495235.aspx) para averiguar la tecla de acceso de almacenamiento principal para su cuenta de almacenamiento de Azure. A continuación, llame el cmdlet [AzureStorageContext de nuevo](http://msdn.microsoft.com/library/azure/dn806380.aspx) para crear un contexto de almacenamiento:

        $StorageAccountName = "yourstorageaccount"
        $StorageAccountKey = Get-AzureStorageKey -StorageAccountName $StorageAccountName
        $Ctx = New-AzureStorageContext $StorageAccountName -StorageAccountKey $StorageAccountKey.Primary


- Generar un token de firma de acceso compartido para un contenedor de almacenamiento de Azure y usarlo para crear un contexto de almacenamiento:

        $sasToken = New-AzureStorageContainerSASToken -Container abc -Permission rl
        $Ctx = New-AzureStorageContext -StorageAccountName $StorageAccountName -SasToken $sasToken

    Para obtener más información, vea [AzureStorageContainerSASToken de nuevo](http://msdn.microsoft.com/library/azure/dn806416.aspx) y [Utilizar firmas de acceso compartido (SA)](storage-dotnet-shared-access-signature-part-1.md).

- En algunos casos, desea especificar los extremos de servicio cuando se crea un nuevo contexto de almacenamiento. Esto podría ser necesario cuando ha registrado un nombre de dominio personalizado para su cuenta de almacenamiento con el servicio de Blob o que desea usar una firma de acceso compartido para tener acceso a los recursos de almacenamiento. Establecer los extremos del servicio en una cadena de conexión y usarlo para crear un nuevo contexto de almacenamiento tal como se muestra a continuación:

        $ConnectionString = "DefaultEndpointsProtocol=http;BlobEndpoint=<blobEndpoint>;QueueEndpoint=<QueueEndpoint>;TableEndpoint=<TableEndpoint>;AccountName=<AccountName>;AccountKey=<AccountKey>"
        $Ctx = New-AzureStorageContext -ConnectionString $ConnectionString

Para obtener más información sobre cómo configurar una cadena de conexión de almacenamiento, consulte [Configurar cadenas de conexión](storage-configure-connection-string.md).

Ahora que ha configurado el equipo y ha aprendido cómo administrar suscripciones y cuentas de almacenamiento con PowerShell de Azure, vaya a la siguiente sección para obtener información sobre cómo administrar blobs de Azure y blob instantáneas.

### <a name="how-to-retrieve-and-regenerate-azure-storage-keys"></a>Cómo recuperar y generar las claves de almacenamiento de Azure

Una cuenta de almacenamiento de Azure se suministra con dos claves de cuenta. Puede usar el siguiente cmdlet para recuperar las claves.

    Get-AzureStorageKey -StorageAccountName "yourstorageaccount"

Usar el siguiente cmdlet para recuperar una clave específica. Los valores válidos son primarios y secundarios.  

    (Get-AzureStorageKey -StorageAccountName $StorageAccountName).Primary

    (Get-AzureStorageKey -StorageAccountName $StorageAccountName).Secondary

Si desea volver a generar las claves, use el cmdlet siguiente. Valores válidos para el tipo de clave - son "Principal" y "Secundario"

    New-AzureStorageKey -StorageAccountName $StorageAccountName -KeyType “Primary”

    New-AzureStorageKey -StorageAccountName $StorageAccountName -KeyType “Secondary”

## <a name="how-to-manage-azure-blobs"></a>Cómo administrar blobs de Azure
Almacenamiento de blobs de Windows Azure es un servicio para almacenar grandes cantidades de datos no estructurados, como texto o binario, que puede tener acceso desde cualquier lugar del mundo a través de HTTP o HTTPS. Esta sección se supone que ya está familiarizado con los conceptos de servicio de almacenamiento de blobs de Windows Azure. Para obtener información detallada, vea [Introducción a almacenamiento de blobs con .NET](storage-dotnet-how-to-use-blobs.md) y [Conceptos de servicio de blobs de Windows](http://msdn.microsoft.com/library/azure/dd179376.aspx).

### <a name="how-to-create-a-container"></a>Cómo crear un contenedor
Cada blob en Azure almacenamiento debe estar en un contenedor. Puede crear un contenedor privado mediante el cmdlet AzureStorageContainer nuevo:

    $StorageContainerName = "yourcontainername"
    New-AzureStorageContainer -Name $StorageContainerName -Permission Off

> [AZURE.NOTE] Hay tres niveles de acceso de lectura anónimo: **desactivar**, **Blob**y el **contenedor**. Para evitar el acceso anónimo a BLOB, establezca el parámetro permiso en **desactivar**. De forma predeterminada, el nuevo contenedor es privado y se puede acceder sólo por el propietario de la cuenta. Para permitir el acceso de lectura público anónimo blob recursos, pero no a los metadatos de contenedor o a la lista de BLOB en el contenedor, establezca el parámetro de permiso a **Blob**. Para permitir el acceso de lectura público completa a recursos de blobs, metadatos de contenedor y la lista de BLOB en el contenedor, establezca el parámetro de permiso al **contenedor**. Para obtener más información, vea [administrar el acceso anónimo lectura contenedores y BLOB](storage-manage-access-to-resources.md).

### <a name="how-to-upload-a-blob-into-a-container"></a>Cómo cargar un blob en un contenedor
Almacenamiento de blobs de Windows Azure es compatible con BLOB bloque y blobs de página. Para obtener más información, vea [Descripción bloque BLOB, anexar BLobs y Blobs de página](http://msdn.microsoft.com/library/azure/ee691964.aspx).

Para cargar BLOB en un contenedor, puede usar el cmdlet [Set-AzureStorageBlobContent](http://msdn.microsoft.com/library/azure/dn806379.aspx) . De forma predeterminada, este comando cargará los archivos locales a un blob de bloque. Para especificar el tipo de blob, puede usar el parámetro - BlobType.

En el ejemplo siguiente se ejecuta el cmdlet [Get-ChildItem](http://technet.microsoft.com/library/hh849800.aspx) para obtener todos los archivos en la carpeta especificada y, a continuación, pasa al siguiente cmdlet mediante el operador de canalización. El cmdlet [Set-AzureStorageBlobContent](http://msdn.microsoft.com/library/azure/dn806379.aspx) cargará los archivos locales en el contenedor:

    Get-ChildItem –Path C:\Images\* | Set-AzureStorageBlobContent -Container "yourcontainername"

### <a name="how-to-download-blobs-from-a-container"></a>Cómo descargar BLOB desde un contenedor
En el ejemplo siguiente se muestra cómo descargar BLOB desde un contenedor. En el ejemplo primero establece una conexión con el almacenamiento de Azure utilizando el contexto de la cuenta de almacenamiento, que incluye el nombre de la cuenta de almacenamiento y su clave de acceso principal. A continuación, en el ejemplo se recupera una referencia de blobs de Windows mediante el cmdlet [Get-AzureStorageBlob](http://msdn.microsoft.com/library/azure/dn806392.aspx) . A continuación, en el ejemplo se usa el cmdlet [Get-AzureStorageBlobContent](http://msdn.microsoft.com/library/azure/dn806418.aspx) para descargar BLOB en la carpeta de destino local.

    #Define the variables.
    $ContainerName = "yourcontainername"
    $DestinationFolder = "C:\DownloadImages"

    #Define the storage account and context.
    $StorageAccountName = "yourstorageaccount"
    $StorageAccountKey = "Storage key for yourstorageaccount ends with =="
    $Ctx = New-AzureStorageContext -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey

    #List all blobs in a container.
    $blobs = Get-AzureStorageBlob -Container $ContainerName -Context $Ctx

    #Download blobs from a container.
    New-Item -Path $DestinationFolder -ItemType Directory -Force
    $blobs | Get-AzureStorageBlobContent -Destination $DestinationFolder -Context $Ctx

### <a name="how-to-copy-blobs-from-one-storage-container-to-another"></a>Cómo copiar BLOB desde un contenedor de almacenamiento a otro
Puede copiar asincrónica BLOB en todas las regiones y cuentas de almacenamiento. En el ejemplo siguiente se muestra cómo copiar BLOB desde un contenedor de almacenamiento a otra en dos cuentas de almacenamiento diferente. El ejemplo primero establece variables para las cuentas de almacenamiento de origen y de destino y, a continuación, crea un contexto de almacenamiento para cada cuenta. A continuación, el ejemplo copia BLOB del contenedor de origen en el contenedor de destino mediante el cmdlet [AzureStorageBlobCopy de inicio](http://msdn.microsoft.com/library/azure/dn806394.aspx) . El ejemplo se supone que ya existen los contenedores y cuentas de almacenamiento de origen y de destino.

    #Define the source storage account and context.
    $SourceStorageAccountName = "yoursourcestorageaccount"
    $SourceStorageAccountKey = "Storage key for yoursourcestorageaccount"
    $SrcContainerName = "yoursrccontainername"
    $SourceContext = New-AzureStorageContext -StorageAccountName $SourceStorageAccountName -StorageAccountKey $SourceStorageAccountKey

    #Define the destination storage account and context.
    $DestStorageAccountName = "yourdeststorageaccount"
    $DestStorageAccountKey = "Storage key for yourdeststorageaccount"
    $DestContainerName = "destcontainername"
    $DestContext = New-AzureStorageContext -StorageAccountName $DestStorageAccountName -StorageAccountKey $DestStorageAccountKey

    #Get a reference to blobs in the source container.
    $blobs = Get-AzureStorageBlob -Container $SrcContainerName -Context $SourceContext

    #Copy blobs from one container to another.
    $blobs| Start-AzureStorageBlobCopy -DestContainer $DestContainerName -DestContext $DestContext

Tenga en cuenta que este ejemplo se realiza una copia asincrónica. Puede supervisar el estado de cada copia, ejecute el cmdlet [Get-AzureStorageBlobCopyState](http://msdn.microsoft.com/library/azure/dn806406.aspx) .

### <a name="how-to-copy-blobs-from-a-secondary-location"></a>Cómo copiar BLOB desde una ubicación secundaria
Puede copiar BLOB desde la ubicación de una cuenta habilitada para RA GRS secundaria.

    #define secondary storage context using a connection string constructed from secondary endpoints.
    $SrcContext = New-AzureStorageContext -ConnectionString "DefaultEndpointsProtocol=https;AccountName=***;AccountKey=***;BlobEndpoint=http://***-secondary.blob.core.windows.net;FileEndpoint=http://***-secondary.file.core.windows.net;QueueEndpoint=http://***-secondary.queue.core.windows.net; TableEndpoint=http://***-secondary.table.core.windows.net;"
    Start-AzureStorageBlobCopy –Container *** -Blob *** -Context $SrcContext –DestContainer *** -DestBlob *** -DestContext $DestContext

### <a name="how-to-delete-a-blob"></a>Cómo eliminar un blob
Para eliminar un objeto binario, obtener una referencia de blobs y llamamos el cmdlet quitar AzureStorageBlob en él. En el ejemplo siguiente se elimina todos los BLOB en un contenedor determinado. El ejemplo primero establece variables para una cuenta de almacenamiento y, a continuación, crea un contexto de almacenamiento. A continuación, en el ejemplo se recupera una referencia de blobs de Windows mediante el cmdlet [Get-AzureStorageBlob](http://msdn.microsoft.com/library/azure/dn806392.aspx) y se ejecuta el cmdlet [AzureStorageBlob quitar](http://msdn.microsoft.com/library/azure/dn806399.aspx) para quitar BLOB de un contenedor de almacenamiento de Azure.

    #Define the storage account and context.
    $StorageAccountName = "yourstorageaccount"
    $StorageAccountKey = "Storage key for yourstorageaccount ends with =="
    $ContainerName = "containername"
    $Ctx = New-AzureStorageContext -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey

    #Get a reference to all the blobs in the container.
    $blobs = Get-AzureStorageBlob -Container $ContainerName -Context $Ctx

    #Delete blobs in a specified container.
    $blobs| Remove-AzureStorageBlob

## <a name="how-to-manage-azure-blob-snapshots"></a>Cómo administrar instantáneas de blobs de Windows Azure
Azure le permite crear una instantánea de un blob. Una instantánea es una versión de solo lectura de un objeto binario que se toma en un punto en el tiempo. Una vez que se ha creado una instantánea, se puede se leer, copiado, o eliminado, pero no se ha modificado. Instantáneas proporcionan una manera de realizar una copia de un blob tal y como aparece en un momento determinado. Para obtener más información, vea [crear una instantánea de un Blob](http://msdn.microsoft.com/library/azure/hh488361.aspx).

### <a name="how-to-create-a-blob-snapshot"></a>Cómo crear una instantánea de blobs
Para crear una instantánea de un blob, obtener una referencia de blobs de Windows y, a continuación, llame a la `ICloudBlob.CreateSnapshot` método en él. En el ejemplo siguiente se establece primero variables para una cuenta de almacenamiento y, a continuación, crea un contexto de almacenamiento. A continuación, en el ejemplo se recupera una referencia de blobs de Windows mediante el cmdlet [Get-AzureStorageBlob](http://msdn.microsoft.com/library/azure/dn806392.aspx) y ejecuta el método [ICloudBlob.CreateSnapshot](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.blob.icloudblob.aspx) para crear una instantánea.

    #Define the storage account and context.
    $StorageAccountName = "yourstorageaccount"
    $StorageAccountKey = "Storage key for yourstorageaccount ends with =="
    $ContainerName = "yourcontainername"
    $BlobName = "yourblobname"
    $Ctx = New-AzureStorageContext -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey

    #Get a reference to a blob.
    $blob = Get-AzureStorageBlob -Context $Ctx -Container $ContainerName -Blob $BlobName

    #Create a snapshot of the blob.
    $snap = $blob.ICloudBlob.CreateSnapshot()

### <a name="how-to-list-a-blobs-snapshots"></a>Cómo enumerar instantáneas de un blob
Puede crear tantas copias instantáneas como desee para un blob. Lista de las instantáneas asociadas a su blob para realizar un seguimiento de sus instantáneas actuales. En el ejemplo siguiente se usa un blob predefinido y llama el cmdlet [Get-AzureStorageBlob](http://msdn.microsoft.com/library/azure/dn806392.aspx) para obtener una lista de las instantáneas de blob.  

    #Define the blob name.
    $BlobName = "yourblobname"

    #List the snapshots of a blob.
    Get-AzureStorageBlob –Context $Ctx -Prefix $BlobName -Container $ContainerName  | Where-Object  { $_.ICloudBlob.IsSnapshot -and $_.Name -eq $BlobName }

### <a name="how-to-copy-a-snapshot-of-a-blob"></a>Cómo copiar una instantánea de un blob
Puede copiar una instantánea de un blob para restaurar la instantánea. Para obtener información detallada y las restricciones, vea [crear una instantánea de un Blob](http://msdn.microsoft.com/library/azure/hh488361.aspx). En el ejemplo siguiente se establece primero variables para una cuenta de almacenamiento y, a continuación, crea un contexto de almacenamiento. A continuación, en el ejemplo se define las variables de nombre de contenedor y blob. En el ejemplo se recupera una referencia de blobs de Windows mediante el cmdlet [Get-AzureStorageBlob](http://msdn.microsoft.com/library/azure/dn806392.aspx) y ejecuta el método [ICloudBlob.CreateSnapshot](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.blob.icloudblob.aspx) para crear una instantánea. A continuación, en el ejemplo se ejecuta el cmdlet [AzureStorageBlobCopy inicio](http://msdn.microsoft.com/library/azure/dn806394.aspx) para copiar la instantánea de un blob con el objeto ICloudBlob para el blob de origen. No olvide actualizar las variables según su configuración antes de ejecutar el ejemplo. Observe que el ejemplo siguiente se supone que ya existen el origen y contenedores de destino y el blob de origen.

    #Define the storage account and context.
    $StorageAccountName = "yourstorageaccount"
    $StorageAccountKey = "Storage key for yourstorageaccount ends with =="
    $Ctx = New-AzureStorageContext -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey

    #Define the variables.
    $SrcContainerName = "yoursourcecontainername"
    $DestContainerName = "yourdestcontainername"
    $SrcBlobName = "yourblobname"
    $DestBlobName = "CopyBlobName"

    #Get a reference to a blob.
    $blob = Get-AzureStorageBlob -Context $Ctx -Container $SrcContainerName -Blob $SrcBlobName

    #Create a snapshot of a blob.
    $snap = $blob.ICloudBlob.CreateSnapshot()

    #Copy the snapshot to another container.
    Start-AzureStorageBlobCopy –Context $Ctx -ICloudBlob $snap -DestBlob $DestBlobName -DestContainer $DestContainerName

Ahora que ha aprendido a administrar blobs de Azure y blob instantáneas con PowerShell de Azure, vaya a la siguiente sección para obtener información sobre cómo administrar archivos, colas y tablas.

## <a name="how-to-manage-azure-tables-and-table-entities"></a>Cómo administrar las entidades de tabla y tablas de Azure
Servicio de almacenamiento de tablas Azure es un almacén de datos NoSQL, que puede usar para almacenar y grandes conjuntos de datos estructurados y relacionales de la consulta. Los componentes principales del servicio son tablas, entidades y propiedades. Una tabla es una colección de entidades. Una entidad es un conjunto de propiedades. Cada entidad puede tener hasta 252 propiedades, que son todos los pares de valor de nombre. Esta sección se supone que ya está familiarizado con los conceptos de servicio de almacenamiento de tablas de Azure. Para obtener información detallada, vea [Descripción del modelo de datos de servicio de tabla](http://msdn.microsoft.com/library/azure/dd179338.aspx) y [empezar a trabajar con el almacenamiento de tablas de Azure con .NET](storage-dotnet-how-to-use-tables.md).

En las subsecciones siguientes, aprenderá cómo administrar el servicio de almacenamiento de tablas de Azure con PowerShell de Azure. Los escenarios cubiertos incluyen **crear**, **Eliminar**y **recuperar** **tablas**, así como **Agregar**, **consultas**y **Eliminar entidades de tabla**.

### <a name="how-to-create-a-table"></a>Cómo crear una tabla
Cada tabla debe residen en una cuenta de almacenamiento de Azure. En el ejemplo siguiente se muestra cómo crear una tabla en el almacenamiento de Azure. En el ejemplo primero establece una conexión con el almacenamiento de Azure utilizando el contexto de la cuenta de almacenamiento, que incluye el nombre de la cuenta de almacenamiento y su clave de acceso. A continuación, utiliza el cmdlet [AzureStorageTable de nuevo](http://msdn.microsoft.com/library/azure/dn806417.aspx) para crear una tabla en el almacenamiento de Azure.

    #Define the storage account and context.
    $StorageAccountName = "yourstorageaccount"
    $StorageAccountKey = "Storage key for yourstorageaccount ends with =="
    $Ctx = New-AzureStorageContext $StorageAccountName -StorageAccountKey $StorageAccountKey

    #Create a new table.
    $tabName = "yourtablename"
    New-AzureStorageTable –Name $tabName –Context $Ctx

### <a name="how-to-retrieve-a-table"></a>Cómo recuperar una tabla
Puede consultar y recuperar una o todas las tablas de una cuenta de almacenamiento. En el ejemplo siguiente se muestra cómo recuperar una tabla dada mediante el cmdlet [Get-AzureStorageTable](http://msdn.microsoft.com/library/azure/dn806411.aspx) .

    #Retrieve a table.
    $tabName = "yourtablename"
    Get-AzureStorageTable –Name $tabName –Context $Ctx

Si se llama el cmdlet Get-AzureStorageTable sin parámetros, obtiene todas las tablas de almacenamiento para una cuenta de almacenamiento.

### <a name="how-to-delete-a-table"></a>Cómo eliminar una tabla
Puede eliminar una tabla de una cuenta de almacenamiento mediante el cmdlet [Quitar AzureStorageTable](http://msdn.microsoft.com/library/azure/dn806393.aspx) .  

    #Delete a table.
    $tabName = "yourtablename"
    Remove-AzureStorageTable –Name $tabName –Context $Ctx

### <a name="how-to-manage-table-entities"></a>Cómo administrar las entidades de tabla
Actualmente, PowerShell Azure no proporciona cmdlets para administrar las entidades de tabla directamente. Para realizar operaciones en las entidades de tabla, puede usar las clases en la [Biblioteca de cliente de almacenamiento de Azure para .NET](http://msdn.microsoft.com/library/azure/wa_storage_30_reference_home.aspx).

#### <a name="how-to-add-table-entities"></a>Cómo agregar entidades de tabla
Para agregar una entidad a una tabla, cree primero un objeto que define las propiedades de la entidad. Una entidad puede tener hasta 255 propiedades, incluidas las propiedades de sistema 3: **PartitionKey**, **RowKey**y **marca de tiempo**. Usted es responsable de insertar y actualizar los valores de **PartitionKey** y **RowKey**. El servidor administra el valor de **marca de tiempo**, que no se puede modificar. Conjunto de la **PartitionKey** y **RowKey** para identificar cada entidad dentro de una tabla.

-   **PartitionKey**: determina la partición almacenado en la entidad.
-   **RowKey**: identifica la entidad dentro de la partición.

Puede definir hasta 252 propiedades personalizadas de una entidad. Para obtener más información, vea la [Descripción del modelo de datos de servicio de tabla](http://msdn.microsoft.com/library/azure/dd179338.aspx).

En el ejemplo siguiente se muestra cómo agregar entidades a una tabla. En el ejemplo se muestra cómo recuperar la tabla de empleados y agregue varias entidades en él. En primer lugar, Establece una conexión con el almacenamiento de Azure utilizando el contexto de la cuenta de almacenamiento, que incluye el nombre de la cuenta de almacenamiento y su clave de acceso. A continuación, recupera la tabla dada mediante el cmdlet [Get-AzureStorageTable](http://msdn.microsoft.com/library/azure/dn806411.aspx) . Si la tabla no existe, el cmdlet [New-AzureStorageTable](http://msdn.microsoft.com/library/azure/dn806417.aspx) se usa para crear una tabla en el almacenamiento de Azure. A continuación, en el ejemplo se define una función personalizada entidad Agregar para agregar entidades a la tabla especificando partición de la entidad y la clave de la fila. La función Agregar entidad llama el cmdlet [New-Object](http://technet.microsoft.com/library/hh849885.aspx) en la clase [Microsoft.WindowsAzure.Storage.Table.DynamicTableEntity](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.table.dynamictableentity.aspx) para crear un objeto de entidad. Más adelante, en el ejemplo se llama al método [Microsoft.WindowsAzure.Storage.Table.TableOperation.Insert](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.table.tableoperation.insert.aspx) en este objeto de entidad para agregar a una tabla.

    #Function Add-Entity: Adds an employee entity to a table.
    function Add-Entity() {
        [CmdletBinding()]
        param(
           $table,
           [String]$partitionKey,
           [String]$rowKey,
           [String]$name,
           [Int]$id
        )

      $entity = New-Object -TypeName Microsoft.WindowsAzure.Storage.Table.DynamicTableEntity -ArgumentList $partitionKey, $rowKey
      $entity.Properties.Add("Name", $name)
      $entity.Properties.Add("ID", $id)

      $result = $table.CloudTable.Execute([Microsoft.WindowsAzure.Storage.Table.TableOperation]::Insert($entity))
    }

    #Define the storage account and context.
    $StorageAccountName = "yourstorageaccount"
    $StorageAccountKey = Get-AzureStorageKey -StorageAccountName $StorageAccountName
    $Ctx = New-AzureStorageContext $StorageAccountName -StorageAccountKey $StorageAccountKey.Primary
    $TableName = "Employees"

    #Retrieve the table if it already exists.
    $table = Get-AzureStorageTable –Name $TableName -Context $Ctx -ErrorAction Ignore

    #Create a new table if it does not exist.
    if ($table -eq $null)
    {
       $table = New-AzureStorageTable –Name $TableName -Context $Ctx
    }

    #Add multiple entities to a table.
    Add-Entity -Table $table -PartitionKey Partition1 -RowKey Row1 -Name Chris -Id 1
    Add-Entity -Table $table -PartitionKey Partition1 -RowKey Row2 -Name Jessie -Id 2
    Add-Entity -Table $table -PartitionKey Partition2 -RowKey Row1 -Name Christine -Id 3
    Add-Entity -Table $table -PartitionKey Partition2 -RowKey Row2 -Name Steven -Id 4

#### <a name="how-to-query-table-entities"></a>Cómo consultar entidades de tabla
Para consultar una tabla, utilice la clase [Microsoft.WindowsAzure.Storage.Table.TableQuery](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.table.tablequery.aspx) . En el ejemplo siguiente se supone que ya ha ejecutado la secuencia de comandos en cómo agregar sección entidades de esta guía. En el ejemplo primero establece una conexión con el almacenamiento de Azure utilizando el contexto de almacenamiento, que incluye el nombre de la cuenta de almacenamiento y su clave de acceso. A continuación, trata de recuperar la tabla "Empleados" creada previamente con el cmdlet [Get-AzureStorageTable](http://msdn.microsoft.com/library/azure/dn806411.aspx) . Llamar al cmdlet [New-Object](http://technet.microsoft.com/library/hh849885.aspx) en la clase Microsoft.WindowsAzure.Storage.Table.TableQuery crea un nuevo objeto de consulta. El ejemplo busca las entidades que tienen una columna 'ID' cuyo valor es 1 según lo especificado en un filtro de cadena. Para obtener información detallada, vea [entidades y tablas de consulta](http://msdn.microsoft.com/library/azure/dd894031.aspx). Cuando se ejecuta esta consulta, devuelve todas las entidades que cumplen los criterios de filtro.

    #Define the storage account and context.
    $StorageAccountName = "yourstorageaccount"
    $StorageAccountKey = Get-AzureStorageKey -StorageAccountName $StorageAccountName
    $Ctx = New-AzureStorageContext –StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey.Primary
    $TableName = "Employees"

    #Get a reference to a table.
    $table = Get-AzureStorageTable –Name $TableName -Context $Ctx

    #Create a table query.
    $query = New-Object Microsoft.WindowsAzure.Storage.Table.TableQuery

    #Define columns to select.
    $list = New-Object System.Collections.Generic.List[string]
    $list.Add("RowKey")
    $list.Add("ID")
    $list.Add("Name")

    #Set query details.
    $query.FilterString = "ID gt 0"
    $query.SelectColumns = $list
    $query.TakeCount = 20

    #Execute the query.
    $entities = $table.CloudTable.ExecuteQuery($query)

    #Display entity properties with the table format.
    $entities  | Format-Table PartitionKey, RowKey, @{ Label = "Name"; Expression={$_.Properties["Name"].StringValue}}, @{ Label = "ID"; Expression={$_.Properties["ID"].Int32Value}} -AutoSize

#### <a name="how-to-delete-table-entities"></a>Cómo eliminar entidades de tabla
Puede eliminar una entidad mediante sus claves partición y fila. En el ejemplo siguiente se supone que ya ha ejecutado la secuencia de comandos en cómo agregar sección entidades de esta guía. En el ejemplo primero establece una conexión con el almacenamiento de Azure utilizando el contexto de almacenamiento, que incluye el nombre de la cuenta de almacenamiento y su clave de acceso. A continuación, trata de recuperar la tabla "Empleados" creada previamente con el cmdlet [Get-AzureStorageTable](http://msdn.microsoft.com/library/azure/dn806411.aspx) . Si la tabla existe, en el ejemplo se llama al método de [Microsoft.WindowsAzure.Storage.Table.TableOperation.Retrieve](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.table.tableoperation.retrieve.aspx) para recuperar una entidad según sus valores de clave de partición y fila. A continuación, pase la entidad al método [Microsoft.WindowsAzure.Storage.Table.TableOperation.Delete](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.table.tableoperation.delete.aspx) para eliminar.

    #Define the storage account and context.
    $StorageAccountName = "yourstorageaccount"
    $StorageAccountKey = Get-AzureStorageKey -StorageAccountName $StorageAccountName
    $Ctx = New-AzureStorageContext –StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey.Primary

    #Retrieve the table.
    $TableName = "Employees"
    $table = Get-AzureStorageTable -Name $TableName -Context $Ctx -ErrorAction Ignore

    #If the table exists, start deleting its entities.
    if ($table -ne $null) {
       #Together the PartitionKey and RowKey uniquely identify every  
       #entity within a table.
       $tableResult = $table.CloudTable.Execute([Microsoft.WindowsAzure.Storage.Table.TableOperation]::Retrieve("Partition2", "Row1"))
       $entity = $tableResult.Result
    if ($entity -ne $null)
    {
       #Delete the entity.
       $table.CloudTable.Execute([Microsoft.WindowsAzure.Storage.Table.TableOperation]::Delete($entity))
    }
    }

## <a name="how-to-manage-azure-queues-and-queue-messages"></a>Cómo administrar Azure colas y mensajes de cola
Almacenamiento de cola Azure es un servicio para almacenar grandes cantidades de mensajes que pueden tener acceso desde cualquier lugar del mundo a través de llamadas autenticadas mediante HTTP o HTTPS. Esta sección se supone que ya está familiarizado con los conceptos de servicio de almacenamiento de cola de Azure. Para obtener información detallada, vea [Introducción a almacenamiento de Azure cola mediante .NET](storage-dotnet-how-to-use-queues.md).

Esta sección le mostrará cómo administrar el servicio de almacenamiento en cola de Azure con PowerShell de Azure. Los escenarios cubiertos incluyen **Insertar** y **Eliminar** mensajes de la cola, así como **crear**, **Eliminar**y **recuperar colas**.

### <a name="how-to-create-a-queue"></a>Cómo crear una cola
En primer lugar, en el ejemplo siguiente se establece una conexión al almacenamiento de Azure usando el contexto de la cuenta de almacenamiento, que incluye el nombre de la cuenta de almacenamiento y su clave de acceso. A continuación, llamadas cmdlet [AzureStorageQueue de nuevo](http://msdn.microsoft.com/library/azure/dn806382.aspx) para crear una cola denominada 'nombre'.

    #Define the storage account and context.
    $StorageAccountName = "yourstorageaccount"
    $StorageAccountKey = Get-AzureStorageKey -StorageAccountName $StorageAccountName
    $Ctx = New-AzureStorageContext –StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey.Primary
    $QueueName = "queuename"
    $Queue = New-AzureStorageQueue –Name $QueueName -Context $Ctx

Para obtener información sobre convenciones de nomenclatura para el servicio de cola de Azure, vea [colas de nomenclatura y metadatos](http://msdn.microsoft.com/library/azure/dd179349.aspx).

### <a name="how-to-retrieve-a-queue"></a>Cómo recuperar una cola
Puede consultar y recuperar una cola específica o una lista de todas las colas de una cuenta de almacenamiento. En el ejemplo siguiente se muestra cómo recuperar una cola especificada mediante el cmdlet [Get-AzureStorageQueue](http://msdn.microsoft.com/library/azure/dn806377.aspx) .

    #Retrieve a queue.
    $QueueName = "queuename"
    $Queue = Get-AzureStorageQueue –Name $QueueName –Context $Ctx

Si se llama el cmdlet [Get-AzureStorageQueue](http://msdn.microsoft.com/library/azure/dn806377.aspx) sin parámetros, obtiene una lista de todas las colas.

### <a name="how-to-delete-a-queue"></a>Cómo eliminar una cola
Para eliminar una cola y todos los mensajes contenidos en él, llame el cmdlet quitar AzureStorageQueue. En el ejemplo siguiente se muestra cómo eliminar una cola especificada mediante el cmdlet quitar AzureStorageQueue.

    #Delete a queue.
    $QueueName = "yourqueuename"
    Remove-AzureStorageQueue –Name $QueueName –Context $Ctx

#### <a name="how-to-insert-a-message-into-a-queue"></a>Cómo insertar un mensaje en una cola
Para insertar un mensaje en una cola existente, crear una nueva instancia de la clase [Microsoft.WindowsAzure.Storage.Queue.CloudQueueMessage](http://msdn.microsoft.com/library/azure/jj732474.aspx) . A continuación, llame al método [AddMessage](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.queue.cloudqueue.addmessage.aspx) . Puede crear un CloudQueueMessage desde una cadena (en formato UTF-8) o una matriz de bytes.

En el ejemplo siguiente se muestra cómo agregar el mensaje a una cola. En el ejemplo primero establece una conexión con el almacenamiento de Azure utilizando el contexto de la cuenta de almacenamiento, que incluye el nombre de la cuenta de almacenamiento y su clave de acceso. A continuación, recupera la cola especificada mediante el cmdlet [Get-AzureStorageQueue](https://msdn.microsoft.com/library/azure/dn806377.aspx) . Si la cola existe, se utiliza el cmdlet [New-Object](http://technet.microsoft.com/library/hh849885.aspx) para crear una instancia de la clase [Microsoft.WindowsAzure.Storage.Queue.CloudQueueMessage](http://msdn.microsoft.com/library/azure/jj732474.aspx) . Más adelante, en el ejemplo se llama al método [AddMessage](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.queue.cloudqueue.addmessage.aspx) en este objeto de mensaje para agregar a una cola. Aquí es código que recupera una cola e inserta el mensaje 'MessageInfo':

    #Define the storage account and context.
    $StorageAccountName = "yourstorageaccount"
    $StorageAccountKey = Get-AzureStorageKey -StorageAccountName $StorageAccountName
    $Ctx = New-AzureStorageContext –StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey.Primary

    #Retrieve the queue.
    $QueueName = "queuename"
    $Queue = Get-AzureStorageQueue -Name $QueueName -Context $ctx

    #If the queue exists, add a new message.
    if ($Queue -ne $null) {
       # Create a new message using a constructor of the CloudQueueMessage class.
       $QueueMessage = New-Object -TypeName Microsoft.WindowsAzure.Storage.Queue.CloudQueueMessage -ArgumentList MessageInfo

       #Add a new message to the queue.
       $Queue.CloudQueue.AddMessage($QueueMessage)
    }


#### <a name="how-to-de-queue-at-the-next-message"></a>Cómo anule la cola en el mensaje siguiente
El código anule la cola un mensaje de una cola en dos pasos. Cuando llame al método [Microsoft.WindowsAzure.Storage.Queue.CloudQueue.GetMessage](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.queue.cloudqueue.getmessage.aspx) , recibe el siguiente mensaje de una cola. Se convierte en un mensaje devuelto de **GetMessage** invisible a cualquier otro código leer los mensajes de esta cola. Para terminar de quitar el mensaje de la cola, también debe llamar al método de [Microsoft.WindowsAzure.Storage.Queue.CloudQueue.DeleteMessage](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.queue.cloudqueue.deletemessage.aspx) . Este proceso de dos pasos de la eliminación de un mensaje asegura que si se produce un error en el código procesar un mensaje debido a un error de software o hardware, otra instancia de su código puede obtener el mismo mensaje y vuelva a intentarlo. El código llama **DeleteMessage** derecha después de que el mensaje se ha procesado.

    #Define the storage account and context.
    $StorageAccountName = "yourstorageaccount"
    $StorageAccountKey = Get-AzureStorageKey -StorageAccountName $StorageAccountName
    $Ctx = New-AzureStorageContext –StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey.Primary

    #Retrieve the queue.
    $QueueName = "queuename"
    $Queue = Get-AzureStorageQueue -Name $QueueName -Context $ctx

    $InvisibleTimeout = [System.TimeSpan]::FromSeconds(10)

    #Get the message object from the queue.
    $QueueMessage = $Queue.CloudQueue.GetMessage($InvisibleTimeout)
    #Delete the message.
    $Queue.CloudQueue.DeleteMessage($QueueMessage)

## <a name="how-to-manage-azure-file-shares-and-files"></a>Cómo administrar archivos y recursos compartidos de archivos de Azure
Almacenamiento de archivos Azure ofrece almacenamiento compartido de aplicaciones mediante el protocolo SMB estándar. Máquinas virtuales de Microsoft Azure y servicios de nube pueden compartir datos de archivo en componentes de la aplicación a través de recursos compartidos montados y aplicaciones de local pueden tener acceso a los datos del archivo en un recurso compartido a través de la API de almacenamiento de archivos o PowerShell de Azure.

Para obtener más información sobre el almacenamiento de archivos de Azure, consulte [Introducción a almacenamiento de archivos de Azure en Windows](storage-dotnet-how-to-use-files.md) y [API de REST de servicio de archivo](http://msdn.microsoft.com/library/azure/dn167006.aspx).

## <a name="how-to-set-and-query-storage-analytics"></a>Cómo configurar y análisis de almacenamiento de consultas
Puede usar [El análisis de almacenamiento de Azure](storage-analytics.md) para recopilar métricas para sus cuentas de almacenamiento de Azure y datos de registro sobre las solicitudes enviadas a su cuenta de almacenamiento. Puede usar métrica de almacenamiento para supervisar el estado de una cuenta de almacenamiento y el registro de almacenamiento para diagnosticar y solucionar problemas con su cuenta de almacenamiento.
De forma predeterminada, métrica de almacenamiento no está habilitado para los servicios de almacenamiento. Puede habilitar la supervisión de usar el Portal de Azure o Windows PowerShell, o mediante programación utilizando la biblioteca de cliente de almacenamiento. Registro de almacenamiento pasa del servidor y le permite registrar detalles para las solicitudes correctas e incorrectas en la cuenta de almacenamiento. Estos registros le permiten ver detalles de leer, escribir y eliminar operaciones con las tablas, colas y BLOB, así como las razones por las solicitudes de errores.

Para obtener información sobre cómo habilitar y ver datos de métrica de almacenamiento con PowerShell, vea [cómo habilitar métrica de almacenamiento con PowerShell](http://msdn.microsoft.com/library/azure/dn782843.aspx#HowtoenableStorageMetricsusingPowerShell).

Para obtener información sobre cómo habilitar y recuperar los datos de registro de almacenamiento con PowerShell, vea [cómo habilitar el registro de almacenamiento con PowerShell](http://msdn.microsoft.com/library/azure/dn782840.aspx#HowtoenableStorageLoggingusingPowerShell) y [Buscar los datos de registro de registro de almacenamiento](http://msdn.microsoft.com/library/azure/dn782840.aspx#FindingyourStorageLogginglogdata).
Para obtener información detallada sobre el uso de almacenamiento de registro y métrica de almacenamiento para solucionar problemas de almacenamiento, vea [supervisar, diagnosticar y solución de problemas de Microsoft Azure almacenamiento](storage-monitoring-diagnosing-troubleshooting.md).

## <a name="how-to-manage-shared-access-signature-sas-and-stored-access-policy"></a>Cómo administrar la firma de acceso compartido (SA) y almacena la directiva de acceso
Las firmas de acceso compartido son una parte importante del modelo de seguridad de cualquier aplicación de almacenamiento de Azure. Son útiles para proporcionar permisos limitados a su cuenta de almacenamiento a los clientes que no deberían tener la clave de cuenta. De forma predeterminada, solo el propietario de la cuenta de almacenamiento puede acceder a blobs, tablas y colas dentro de esa cuenta. Si el servicio o la aplicación debe hacer estos recursos disponibles para otros clientes sin tener que compartir su clave de acceso, tiene tres opciones:

- Establecer permisos del contenedor para permitir el acceso de lectura anónimo al contenedor y sus blobs. Esto no está permitido para tablas o colas.
- Usar una firma de acceso compartido que concede derechos de acceso a los contenedores, BLOB, colas y tablas restringidos para un intervalo de tiempo específico.
- Utilizar una directiva de acceso almacenadas para obtener un nivel adicional de control sobre las firmas de acceso compartido para un contenedor o su BLOB, de una cola o para una tabla. La directiva de acceso almacenadas le permite cambiar la hora de inicio, fecha de caducidad o permisos de una firma, o revocar después de se haya emitido.

Una firma de acceso compartido puede estar en una de dos formas:

- **Ad hoc SA**: cuando se crea una SA ad hoc, la hora de inicio, fecha de caducidad y permisos para las asociaciones de seguridad se especifican en el URI de SAS. Este tipo de SA puede crearse en un contenedor, blob, tabla o cola y es no revokable.
- **Asociaciones de seguridad con la directiva de acceso almacenadas**: se define una directiva de acceso almacenadas en un contenedor de recursos un contenedor de blob, tabla o cola - y puede usarlo para administrar las restricciones de una o varias firmas de acceso compartido. Cuando se asocia un SA con una directiva de acceso almacenadas, las asociaciones de seguridad hereda las restricciones - la hora de inicio, fecha de caducidad y permisos - definidos para la directiva de acceso almacenadas. Este tipo de SA es revokable.

Para obtener más información, vea [Usar firmas de acceso compartido (SA)](storage-dotnet-shared-access-signature-part-1.md) y [administrar el acceso anónimo lectura contenedores y BLOB](storage-manage-access-to-resources.md).

En las siguientes secciones, aprenderá cómo crear una directiva de acceso almacenado y token de la firma de acceso compartido para tablas de Azure. PowerShell Azure proporciona cmdlets similar para contenedores BLOB, colas y también. Para ejecutar las secuencias de comandos en esta sección, descargue el [Azure PowerShell versión 0.8.14](http://go.microsoft.com/?linkid=9811175&clcid=0x409) o posterior.

### <a name="how-to-create-a-policy-based-shared-access-signature-token"></a>Cómo crear un token de firma de acceso compartido basado en directivas
Usar el cmdlet AzureStorageTableStoredAccessPolicy de nuevo para crear una nueva directiva de acceso almacenadas. A continuación, llame el cmdlet [AzureStorageTableSASToken de nuevo](http://msdn.microsoft.com/library/azure/dn806400.aspx) para crear un nuevo token de acceso compartido basadas en la directiva de firma para una tabla de almacenamiento de Azure.

    $policy = "policy1"
    New-AzureStorageTableStoredAccessPolicy -Name $tableName -Policy $policy -Permission "rd" -StartTime "2015-01-01" -ExpiryTime "2016-01-01" -Context $Ctx
    New-AzureStorageTableSASToken -Name $tableName -Policy $policy -Context $Ctx

### <a name="how-to-create-an-ad-hoc-non-revokable-shared-access-signature-token"></a>Cómo crear un token de firma de acceso compartido ad hoc (no revokable)
Use el cmdlet [AzureStorageTableSASToken de nuevo](http://msdn.microsoft.com/library/azure/dn806400.aspx) para crear un nuevo token de firma de acceso compartido (no revokable) ad hoc para una tabla de almacenamiento de Azure:

    New-AzureStorageTableSASToken -Name $tableName -Permission "rqud" -StartTime "2015-01-01" -ExpiryTime "2015-02-01" -Context $Ctx

### <a name="how-to-create-a-stored-access-policy"></a>Cómo crear una directiva de acceso almacenadas
Use el cmdlet AzureStorageTableStoredAccessPolicy de nuevo para crear una nueva directiva de acceso almacenadas para una tabla de almacenamiento de Azure:

    $policy = "policy1"
    New-AzureStorageTableStoredAccessPolicy -Name $tableName -Policy $policy -Permission "rd" -StartTime "2015-01-01" -ExpiryTime "2016-01-01" -Context $Ctx

### <a name="how-to-update-a-stored-access-policy"></a>Cómo actualizar una directiva de acceso almacenadas
Use el cmdlet Set-AzureStorageTableStoredAccessPolicy para actualizar una directiva de acceso almacenadas existente para una tabla de almacenamiento de Azure:

    Set-AzureStorageTableStoredAccessPolicy -Policy $policy -Table $tableName -Permission "rd" -NoExpiryTime -NoStartTime -Context $Ctx

### <a name="how-to-delete-a-stored-access-policy"></a>Cómo eliminar una directiva de acceso almacenadas
Use el cmdlet AzureStorageTableStoredAccessPolicy quitar para eliminar una directiva de acceso almacenados en una tabla de almacenamiento de Azure:

    Remove-AzureStorageTableStoredAccessPolicy -Policy $policy -Table $tableName -Context $Ctx


## <a name="how-to-use-azure-storage-for-us-government-and-azure-china"></a>Cómo usar el almacenamiento de Azure para gobierno y China de Azure
Un entorno de Azure es una implementación independiente de Microsoft Azure, como [Administración pública de Azure para gobierno de Estados Unidos](https://azure.microsoft.com/features/gov/), [AzureCloud para Azure global](https://portal.azure.com)y [AzureChinaCloud para Azure a través de 21Vianet en China](http://www.windowsazure.cn/). Puede implementar nuevos entornos de Azure para gobierno estadounidense y China de Azure.

Para usar el almacenamiento de Azure con AzureChinaCloud, debe crear un contexto de almacenamiento que está asociado a AzureChinaCloud. Siga estos pasos para empezar:

1.  Ejecute el cmdlet [Get-AzureEnvironment](https://msdn.microsoft.com/library/azure/dn790368.aspx) para ver los entornos de Azure disponibles:

    `Get-AzureEnvironment`

2.  Agregar una cuenta de Azure China a Windows PowerShell:

    `Add-AzureAccount –Environment AzureChinaCloud`

3.  Crear un contexto de almacenamiento para una cuenta de AzureChinaCloud:

        $Ctx = New-AzureStorageContext -StorageAccountName $AccountName -StorageAccountKey $AccountKey> -Environment AzureChinaCloud

Para usar el almacenamiento de Azure con [Gobierno de Azure](https://azure.microsoft.com/features/gov/), debe definir un entorno nuevo y, a continuación, crear un nuevo contexto de almacenamiento con este entorno:

1.  Ejecute el cmdlet [Get-AzureEnvironment](https://msdn.microsoft.com/library/azure/dn790368.aspx) para ver los entornos de Azure disponibles:

    `Get-AzureEnvironment`

2.  Agregar una cuenta de Azure gobierno de Estados Unidos a Windows PowerShell:

    `Add-AzureAccount –Environment AzureUSGovernment`

3.  Crear un contexto de almacenamiento para una cuenta de AzureUSGovernment:

        $Ctx = New-AzureStorageContext -StorageAccountName $AccountName -StorageAccountKey $AccountKey> -Environment AzureUSGovernment

Para obtener más información, consulte:

- [Guía de programador de administración pública de Microsoft Azure](../azure-government-developer-guide.md).
- [Información general de las diferencias al crear una aplicación de servicio de China](https://msdn.microsoft.com/library/azure/dn578439.aspx)

## <a name="next-steps"></a>Pasos siguientes
En esta guía, ha aprendido cómo administrar el almacenamiento de Azure con PowerShell de Azure. Estos son algunos artículos relacionados y recursos para conocer más acerca de ellos.

- [Documentación de almacenamiento de Azure](https://azure.microsoft.com/documentation/services/storage/)
- [Cmdlets de PowerShell de almacenamiento de Azure](http://msdn.microsoft.com/library/azure/dn806401.aspx)
- [Referencia de Windows PowerShell](https://msdn.microsoft.com/library/ms714469.aspx)

[Image1]: ./media/storage-powershell-guide-full/Subscription_currentportal.png
[Image2]: ./media/storage-powershell-guide-full/Subscription_Previewportal.png
[Image3]: ./media/storage-powershell-guide-full/Blobdownload.png


[Getting started with Azure Storage and PowerShell in 5 minutes]: #getstart
[Prerequisites for using Azure PowerShell with Azure Storage]: #pre
[How to manage storage accounts in Azure]: #manageaccount
[How to set a default Azure subscription]: #setdefsub
[How to create a new Azure storage account]: #createaccount
[How to set a default Azure storage account]: #defaultaccount
[How to list all Azure storage accounts in a subscription]: #listaccounts
[How to create an Azure storage context]: #createctx
[How to manage Azure blobs and blob snapshots]: #manageblobs
[How to create a container]: #container
[How to upload a blob into a container]: #uploadblob
[How to download blobs from a container]: #downblob
[How to copy blobs from one storage container to another]: #copyblob
[How to delete a blob]: #deleteblob
[How to manage Azure blob snapshots]: #manageshots
[How to create a blob snapshot]: #createshot
[How to list snapshots of a blob]: #listshot
[How to copy a snapshot of a blob]: #copyshot
[How to manage Azure tables and table entities]: #managetables
[How to create a table]: #createtable
[How to retrieve a table]: #gettable
[How to delete a table]: #remtable
[How to manage table entities]: #mngentity
[How to add table entities]: #addentity
[How to query table entities]: #queryentity
[How to delete table entities]: #deleteentity
[How to manage Azure queues and queue messages]: #managequeues
[How to create a queue]: #createqueue
[How to retrieve a queue]: #getqueue
[How to delete a queue]: #remqueue
[How to manage queue messages]: #mngqueuemsg
[How to insert a message into a queue]: #addqueuemsg
[How to de-queue at the next message]: #dequeuemsg
[How to manage Azure file shares and files]: #files
[How to set and query storage analytics]: #stganalytics
[How to manage Shared Access Signature (SAS) and Stored Access Policy]: #sas
[How to use Azure Storage for U.S. government and Azure China]: #gov
[Next Steps]: #next
