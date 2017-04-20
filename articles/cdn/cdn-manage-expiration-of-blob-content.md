<properties
 pageTitle="Administrar la caducidad del contenido de blobs de Windows Azure almacenamiento de Azure CDN | Microsoft Azure"
 description="Obtenga información sobre las opciones para controlar time to live para blobs de Azure CDN almacenamiento en caché."
 services="cdn"
 documentationCenter=""
 authors="camsoper"
 manager="erikre"
 editor=""/>
<tags
 ms.service="cdn"
 ms.workload="media"
 ms.tgt_pltfrm="na"
 ms.devlang="multiple"
 ms.topic="article"
 ms.date="09/15/2016"
 ms.author="casoper"/>


# <a name="manage-expiration-of-azure-storage-blob-content-in-azure-cdn"></a>Administrar la caducidad del contenido de blobs de Windows Azure almacenamiento de Azure CDN

> [AZURE.SELECTOR]
- [Servicios de nube o aplicaciones de Azure Web, ASP.NET o IIS](cdn-manage-expiration-of-cloud-service-content.md)
- [Servicio de almacenamiento de blobs de Windows Azure](cdn-manage-expiration-of-blob-content.md)

El [servicio de blobs de Windows](../storage/storage-introduction.md#blob-storage) en [El almacenamiento de Azure](../storage/storage-introduction.md) es uno de los varios orígenes basados en Azure integrados con Azure CDN.  Cualquier contenido accesible públicamente blob puede almacenarse en caché en Azure CDN hasta que transcurre el tiempo de vida (TTL).  El [encabezado de *Control de caché* ](http://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html#sec14.9) en la respuesta HTTP de almacenamiento de Azure determina el valor de TTL.

>[AZURE.TIP] Puede establecer sin TTL en un blob.  En este caso, CDN Azure aplica automáticamente un TTL predeterminado de siete días.
>
>Para obtener más información acerca del funcionamiento de Azure CDN para acelerar el acceso a BLOB y otros archivos, vea la [Información general de CDN de Azure](./cdn-overview.md).
>
>Para obtener más detalles sobre el servicio de blobs de Windows Azure almacenamiento, consulte [Conceptos de servicio de blobs de Windows](https://msdn.microsoft.com/library/dd179376.aspx). 

Este tutorial muestra varias maneras que puede establecer el valor de TTL en un blob en el almacenamiento de Azure.  

## <a name="azure-powershell"></a>Azure PowerShell

[Azure PowerShell](../powershell-install-configure.md) es una de las formas más rápidas y más eficaces para administrar los servicios de Azure.  Usar el `Get-AzureStorageBlob` cmdlet para obtener una referencia al objeto binario, a continuación, establezca la `.ICloudBlob.Properties.CacheControl` propiedad. 

```powershell
# Create a storage context
$context = New-AzureStorageContext -StorageAccountName "<storage account name>" -StorageAccountKey "<storage account key>"

# Get a reference to the blob
$blob = Get-AzureStorageBlob -Context $context -Container "<container name>" -Blob "<blob name>"

# Set the CacheControl property to expire in 1 hour (3600 seconds)
$blob.ICloudBlob.Properties.CacheControl = "public, max-age=3600"

# Send the update to the cloud
$blob.ICloudBlob.SetProperties()
```

>[AZURE.TIP] También puede usar PowerShell para [administrar los perfiles CDN y extremos](./cdn-manage-powershell.md).

## <a name="azure-storage-client-library-for-net"></a>Biblioteca de cliente de almacenamiento de Azure para .NET

Para establecer el período de vida de un blob mediante .NET, use la [Biblioteca de cliente de almacenamiento de Azure para .NET](../storage/storage-dotnet-how-to-use-blobs.md) para establecer la propiedad [CloudBlob.Properties.CacheControl](https://msdn.microsoft.com/library/microsoft.windowsazure.storage.blob.blobproperties.cachecontrol.aspx) .

```csharp
class Program
{
    const string connectionString = "<storage connection string>";
    static void Main()
    {
        // Retrieve storage account information from connection string
        CloudStorageAccount storageAccount = CloudStorageAccount.Parse(connectionString);
        
        // Create a blob client for interacting with the blob service.
        CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
        
        // Create a reference to the container
        CloudBlobContainer container = blobClient.GetContainerReference("<container name>");

        // Create a reference to the blob
        CloudBlob blob = container.GetBlobReference("<blob name>");

        // Set the CacheControl property to expire in 1 hour (3600 seconds)
        blob.Properties.CacheControl = "public, max-age=3600";

        // Update the blob's properties in the cloud
        blob.SetProperties();
    }
}
```

>[AZURE.TIP] Hay muchos más ejemplos de código de .NET en los [Ejemplos de almacenamiento de blobs de Windows Azure para .NET](https://azure.microsoft.com/documentation/samples/storage-blob-dotnet-getting-started/).

## <a name="other-methods"></a>Otros métodos

- [Interfaz de línea de comandos de Azure](../xplat-cli-install.md)

    Al cargar el blob, establezca la propiedad *cacheControl* utilizando la `-p` cambiar.  En este ejemplo se establece el valor TTL a una hora (3600 segundos).

    ```text
    azure storage blob upload -c <connectionstring> -p cacheControl="public, max-age=3600" .\test.txt myContainer test.txt
    ```

- [API de REST de servicios de almacenamiento de Azure](https://msdn.microsoft.com/library/azure/dd179355.aspx)

    Establezca explícitamente la propiedad de *x ms blob caché controles* en una solicitud de [Poner Blob](https://msdn.microsoft.com/en-us/library/azure/dd179451.aspx), [Coloque la lista de bloques](https://msdn.microsoft.com/en-us/library/azure/dd179467.aspx)o [Establecer propiedades de blobs](https://msdn.microsoft.com/library/azure/ee691966.aspx) .

- Herramientas de administración de almacenamiento de terceros

    Algunas herramientas de administración de almacenamiento de Azure de terceros permiten establecer la propiedad *CacheControl* en BLOB. 

## <a name="testing-the-cache-control-header"></a>Probar el encabezado de *Control de caché*

Puede comprobar fácilmente el período de vida de su BLOB.  Con las [Herramientas de desarrollo](https://developer.microsoft.com/microsoft-edge/platform/documentation/f12-devtools-guide/), prueba que el blob es incluido el encabezado de respuesta de *Control de caché su explorador* .  También puede usar una herramienta como **wget**, [Postman](https://www.getpostman.com/)o [Fiddler](http://www.telerik.com/fiddler) para examinar los encabezados de respuesta.

## <a name="next-steps"></a>Pasos siguientes

- [Obtenga información sobre el encabezado de *Control de caché*](http://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html#sec14.9)
- [Obtenga información sobre cómo administrar la caducidad del contenido de servicio de nube de CDN de Azure](./cdn-manage-expiration-of-cloud-service-content.md)

