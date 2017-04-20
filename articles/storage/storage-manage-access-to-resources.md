<properties
    pageTitle="Administrar el acceso de lectura anónimo contenedores y BLOB | Microsoft Azure"
    description="Obtenga información sobre cómo hacer contenedores y BLOB disponibles para el acceso anónimo y cómo tener acceso a ellos mediante programación."
    services="storage"
    documentationCenter=""
    authors="tamram"
    manager="carmonm"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/18/2016"
    ms.author="tamram"/>

# <a name="manage-anonymous-read-access-to-containers-and-blobs"></a>Administrar el acceso de lectura anónimo contenedores y BLOB

## <a name="overview"></a>Información general

De forma predeterminada, solo el propietario de la cuenta de almacenamiento puede tener acceso a los recursos de almacenamiento en esa cuenta. Para el almacenamiento de blobs, puede establecer permisos de un contenedor para permitir el acceso de lectura anónimo al contenedor y sus blobs, por lo que puede conceder acceso a los recursos sin tener que compartir la clave de cuenta.

Acceso anónimo es la mejor para escenarios donde desea que determinadas BLOB esté siempre disponible para el acceso de lectura anónimo. Control preciso, puede crear una firma de acceso compartido, lo que permite el acceso de delegado restringido mediante permisos diferentes y un intervalo de tiempo especificado. Para obtener más información sobre cómo crear firmas de acceso compartido, consulte [Uso compartido acceso firmas (SA)](storage-dotnet-shared-access-signature-part-1.md).

## <a name="grant-anonymous-users-permissions-to-containers-and-blobs"></a>Conceder permisos a los usuarios anónimos para contenedores y BLOB

De forma predeterminada, un contenedor y cualquier BLOB dentro de ella puede tener acceso a solo el propietario de la cuenta de almacenamiento. Para dar a los usuarios anónimos permisos de lectura en un contenedor y sus blobs, puede establecer los permisos del contenedor para permitir el acceso del público. Los usuarios anónimos pueden leer BLOB dentro de un contenedor accesible públicamente sin autenticar la solicitud.

Los contenedores proporcionan las siguientes opciones para administrar el acceso de contenedor:

- **Público acceso de lectura total:** Contenedor y blob de datos se pueden leer a través de la solicitud anónima. Los clientes pueden enumerar BLOB dentro del contenedor a través de la solicitud anónima, pero no pueden enumerar los contenedores dentro de la cuenta de almacenamiento.

- **Acceso para blobs solo de lectura de público:** Pueden leer los datos de BLOB dentro de este contenedor a través de la solicitud anónima, pero los datos de contenedor no están disponibles. Los clientes no pueden enumerar BLOB dentro del contenedor a través de la solicitud anónima.

- **Acceso de lectura sin público:** Contenedor y blob de datos se pueden leer solo el propietario de la cuenta.

Puede establecer permisos del contenedor de las siguientes maneras:

- Desde el [Portal de Azure](https://portal.azure.com).
- Mediante programación, usando la API de REST o la biblioteca de cliente de almacenamiento.
- Con PowerShell. Para obtener información sobre la configuración de permisos del contenedor de Azure PowerShell, vea [con Azure con el almacenamiento de Azure](storage-powershell-guide-full.md#how-to-manage-azure-blobs).

### <a name="setting-container-permissions-from-the-azure-portal"></a>Establecer permisos del contenedor desde el Portal de Azure

Para establecer permisos del contenedor desde el [Portal de Azure](https://portal.azure.com), siga estos pasos:

1. Vaya a panel para su cuenta de almacenamiento.
2. Seleccione el nombre del contenedor de la lista. Haga clic en el nombre expone el BLOB en el contenedor seleccionado
3. Seleccione **la directiva de acceso** de la barra de herramientas.
4. En el campo **tipo de acceso** , seleccione el nivel de permisos que desee, como se muestra en la siguiente captura de pantalla.

    ![Editar cuadro de diálogo de metadatos de contenedor](./media/storage-manage-access-to-resources/storage-manage-access-to-resources-0.png)

### <a name="setting-container-permissions-programmatically-using-net"></a>Configurar los permisos del contenedor mediante programación utilizando .NET

Para establecer permisos para un contenedor de la biblioteca de cliente .NET, en primer lugar, recuperar los permisos existentes del contenedor llamando al método **GetPermissions** . A continuación, establezca la propiedad **PublicAccess** para el objeto **BlobContainerPermissions** devuelta por el método **GetPermissions** . Por último, llame al método **SetPermissions** con los permisos actualizados.

En el ejemplo siguiente se establece permisos del contenedor en público completa acceso de lectura. Para establecer permisos para público acceso de lectura para blobs solo, establezca la propiedad de **PublicAccess** en **BlobContainerPublicAccessType.Blob**. Para quitar todos los permisos para usuarios anónimos, establezca la propiedad en **BlobContainerPublicAccessType.Off**.

    public static void SetPublicContainerPermissions(CloudBlobContainer container)
    {
        BlobContainerPermissions permissions = container.GetPermissions();
        permissions.PublicAccess = BlobContainerPublicAccessType.Container;
        container.SetPermissions(permissions);
    }

## <a name="access-containers-and-blobs-anonymously"></a>Tienen acceso anónimo contenedores y BLOB

Un cliente que tiene acceso a los contenedores y BLOB anónimamente puede utilizar constructores que no requieren credenciales. Los siguientes ejemplos muestran unos cuantos métodos para hacer referencia a recursos de servicio de blobs anónimamente.

### <a name="create-an-anonymous-client-object"></a>Crear un objeto de cliente anónima

Puede crear un nuevo objeto de cliente de servicio para el acceso anónimo proporcionando el extremo de servicio de blobs de la cuenta. Sin embargo, también debe conocer el nombre de un contenedor de la cuenta que está disponible para el acceso anónimo.

    public static void CreateAnonymousBlobClient()
    {
        // Create the client object using the Blob service endpoint.
        CloudBlobClient blobClient = new CloudBlobClient(new Uri(@"https://storagesample.blob.core.windows.net"));

        // Get a reference to a container that's available for anonymous access.
        CloudBlobContainer container = blobClient.GetContainerReference("sample-container");

        // Read the container's properties. Note this is only possible when the container supports full public read access.
        container.FetchAttributes();
        Console.WriteLine(container.Properties.LastModified);
        Console.WriteLine(container.Properties.ETag);
    }

### <a name="reference-a-container-anonymously"></a>Hacer referencia a un contenedor de forma anónima

Si tiene la dirección URL para un contenedor que está disponible de forma anónima, puede usar referencia el contenedor directamente.

    public static void ListBlobsAnonymously()
    {
        // Get a reference to a container that's available for anonymous access.
        CloudBlobContainer container = new CloudBlobContainer(new Uri(@"https://storagesample.blob.core.windows.net/sample-container"));

        // List blobs in the container.
        foreach (IListBlobItem blobItem in container.ListBlobs())
        {
            Console.WriteLine(blobItem.Uri);
        }
    }


### <a name="reference-a-blob-anonymously"></a>Hacer referencia a un blob de forma anónima

Si tiene la dirección URL para un blob que está disponible para el acceso anónimo, puede hacer referencia el blob directamente con esa dirección URL:

    public static void DownloadBlobAnonymously()
    {
        CloudBlockBlob blob = new CloudBlockBlob(new Uri(@"https://storagesample.blob.core.windows.net/sample-container/logfile.txt"));
        blob.DownloadToFile(@"C:\Temp\logfile.txt", System.IO.FileMode.Create);
    }

## <a name="features-available-to-anonymous-users"></a>Características disponibles para los usuarios anónimos

La siguiente tabla muestra las operaciones que se pueden llamar los usuarios anónimos cuando ACL del contenedor está configurado para permitir el acceso del público.

| Operación de resto                                         | Permiso de acceso de lectura público completa | Permiso con acceso de lectura público para blobs solo |
|--------------------------------------------------------|-----------------------------------------|---------------------------------------------------|
| Contenedores de lista                                        | Solo el propietario                              | Solo el propietario                                        |
| Crear contenedor                                       | Solo el propietario                              | Solo el propietario                                        |
| Obtener las propiedades de contenedor                               | Todos los                                     | Solo el propietario                                        |
| Obtener los metadatos de contenedor                                 | Todos los                                     | Solo el propietario                                        |
| Establecer los metadatos de contenedor                                 | Solo el propietario                              | Solo el propietario                                        |
| Obtener contenedor ACL                                      | Solo el propietario                              | Solo el propietario                                        |
| Establecer ACL del contenedor                                      | Solo el propietario                              | Solo el propietario                                        |
| Eliminar el contenedor                                       | Solo el propietario                              | Solo el propietario                                        |
| Lista BLOB                                             | Todos los                                     | Solo el propietario                                        |
| Poner Blob                                               | Solo el propietario                              | Solo el propietario                                        |
| Obtener Blob                                               | Todos los                                     | Todos los                                               |
| Obtener propiedades de blobs                                    | Todos los                                     | Todos los                                               |
| Establecer propiedades de blobs                                    | Solo el propietario                              | Solo el propietario                                        |
| Obtener los metadatos de blobs                                      | Todos los                                     | Todos los                                               |
| Establecer los metadatos de blobs                                      | Solo el propietario                              | Solo el propietario                                        |
| Colocar el bloque                                              | Solo el propietario                              | Solo el propietario                                        |
| Obtener la lista de bloques (solo bloques confirmados)                 | Todos los                                     | Todos los                                               |
| Obtener la lista de bloques (sólo bloques sin confirmar o todos los bloques) | Solo el propietario                              | Solo el propietario                                        |
| Coloque la lista de bloques                                         | Solo el propietario                              | Solo el propietario                                        |
| Eliminar Blob                                            | Solo el propietario                              | Solo el propietario                                        |
| Copiar Blob                                              | Solo el propietario                              | Solo el propietario                                        |
| Instantánea Blob                                          | Solo el propietario                              | Solo el propietario                                        |
| Concesión Blob                                             | Solo el propietario                              | Solo el propietario                                        |
| Insertar página                                               | Solo el propietario                              | Solo el propietario                                        |
| Obtener rangos de página                                        | Todos los                                     | Todos los                                                  |
| Anexar Blob                                            | Solo el propietario                              | Solo el propietario                                                  |


## <a name="see-also"></a>Vea también

- [Autenticación de los servicios de almacenamiento de Azure](https://msdn.microsoft.com/library/azure/dd179428.aspx)
- [Usar firmas de acceso compartido (SA)](storage-dotnet-shared-access-signature-part-1.md)
- [Delegar acceso con una firma de acceso compartido](https://msdn.microsoft.com/library/azure/ee395415.aspx)
