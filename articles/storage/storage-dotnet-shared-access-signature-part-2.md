<properties
    pageTitle="Crear y usar un SA con el almacenamiento de blobs | Microsoft Azure"
    description="Este tutorial muestra cómo crear firmas de acceso compartido para su uso con el almacenamiento de blobs y cómo consumir a ellos desde las aplicaciones de cliente."
    services="storage"
    documentationCenter=""
    authors="tamram"
    manager="carmonm"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="10/18/2016"
    ms.author="tamram"/>


# <a name="shared-access-signatures-part-2-create-and-use-a-sas-with-blob-storage"></a>Compartido firmas de Access, parte 2: Crear y usar un SA con el almacenamiento de blobs

## <a name="overview"></a>Información general

[Parte 1](storage-dotnet-shared-access-signature-part-1.md) de este tutorial explorado había compartido firmas de access (SA) y explicación de los procedimientos recomendados para usarlos. Parte 2 muestra cómo generar y, a continuación, usar firmas de acceso compartido con el almacenamiento de blobs. Los ejemplos se escriben en C# y usar la biblioteca de cliente de almacenamiento de Azure para .NET. Los escenarios cubiertos incluyen estos aspectos del trabajo con firmas acceso compartido:

- Generar una firma de acceso compartido en un contenedor
- Generar una firma de acceso compartido en un blob
- Crear una directiva de acceso almacenadas para administrar las firmas de los recursos del contenedor
- Probar las firmas de acceso compartido desde una aplicación de cliente

## <a name="about-this-tutorial"></a>Acerca de este Tutorial

En este tutorial, nos centraremos en cómo crear firmas de acceso compartido para contenedores y BLOB mediante la creación de dos aplicaciones de consola. La primera aplicación de consola genera firmas de acceso compartido en un contenedor y en un blob. Esta aplicación tiene conocimiento de las teclas de la cuenta de almacenamiento. Tiene acceso a la segunda aplicación de consola que actúe como una aplicación de cliente, contenedor y recursos de blob mediante las firmas de acceso compartido creadas con la aplicación de la primera. Esta aplicación utiliza las firmas de acceso compartido solo para autenticar su acceso al contenedor y recursos de manchas: no tiene conocimiento de las teclas de la cuenta.

## <a name="part-1-create-a-console-application-to-generate-shared-access-signatures"></a>Parte 1: Crear una aplicación de consola para generar firmas de acceso compartido

En primer lugar, asegúrese de que dispone de la biblioteca de cliente de almacenamiento de Azure para .NET instalado. Puede instalar el [paquete NuGet](http://nuget.org/packages/WindowsAzure.Storage/ "paquete NuGet") que contiene los ensamblados más actualizados para la biblioteca de cliente. Este es el método recomendado para asegurarse de que tiene las revisiones más recientes. También puede descargar la biblioteca de cliente como parte de la versión más reciente de [Azure SDK para .NET](https://azure.microsoft.com/downloads/).

En Visual Studio, cree una nueva aplicación de consola de Windows y asígnele el nombre **GenerateSharedAccessSignatures**. Agregar referencias a **Microsoft.WindowsAzure.Configuration.dll** y **Microsoft.WindowsAzure.Storage.dll**, con cualquiera de los siguientes enfoques:

-   Si desea instalar el paquete de NuGet, instalar el [Cliente de NuGet](https://docs.nuget.org/consume/installing-nuget). En Visual Studio, seleccione **proyecto | Administrar paquetes de NuGet**, búsqueda en línea para el **Almacenamiento de Azure**y siga las instrucciones de instalación.
-   Como alternativa, busque los ensamblados en la instalación del SDK de Azure y agregue referencias a ellos.

En la parte superior del archivo Program.cs, agregue las siguientes instrucciones **using** :

    using System.IO;    
    using Microsoft.WindowsAzure;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Blob;

Editar el archivo app.config para que contenga una configuración con una cadena de conexión que apunte a su cuenta de almacenamiento. El archivo app.config debe ser similar a este:

    <configuration>
      <startup>
        <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.5" />
      </startup>
      <appSettings>
        <add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=myaccount;AccountKey=mykey"/>
      </appSettings>
    </configuration>

### <a name="generate-a-shared-access-signature-uri-for-a-container"></a>Generar una firma URI de acceso compartido para un contenedor

Para empezar con, agregaremos un método para generar una firma de acceso compartido en un nuevo contenedor. En este caso la firma no está asociada a una directiva de acceso almacenados, por lo que lleva en el URI de la información que indica su fecha de caducidad y los permisos que concede.

En primer lugar, agregue código al método **Main()** para autenticar el acceso a su cuenta de almacenamiento y crear un nuevo contenedor:

    static void Main(string[] args)
    {
        //Parse the connection string and return a reference to the storage account.
        CloudStorageAccount storageAccount = CloudStorageAccount.Parse(CloudConfigurationManager.GetSetting("StorageConnectionString"));

        //Create the blob client object.
        CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

        //Get a reference to a container to use for the sample code, and create it if it does not exist.
        CloudBlobContainer container = blobClient.GetContainerReference("sascontainer");
        container.CreateIfNotExists();

        //Insert calls to the methods created below here...

        //Require user input before closing the console window.
        Console.ReadLine();
    }

A continuación, agregue un nuevo método que genera la firma de acceso compartido para el contenedor y devuelve la URI de firma:

    static string GetContainerSasUri(CloudBlobContainer container)
    {
        //Set the expiry time and permissions for the container.
        //In this case no start time is specified, so the shared access signature becomes valid immediately.
        SharedAccessBlobPolicy sasConstraints = new SharedAccessBlobPolicy();
        sasConstraints.SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24);
        sasConstraints.Permissions = SharedAccessBlobPermissions.Write | SharedAccessBlobPermissions.List;

        //Generate the shared access signature on the container, setting the constraints directly on the signature.
        string sasContainerToken = container.GetSharedAccessSignature(sasConstraints);

        //Return the URI string for the container, including the SAS token.
        return container.Uri + sasContainerToken;
    }

Agregue las siguientes líneas en la parte inferior del método **Main()** , antes de la llamada a **Console.ReadLine ()**para llamar a **GetContainerSasUri()** y escriba la firma URI en la ventana de la consola:

    //Generate a SAS URI for the container, without a stored access policy.
    Console.WriteLine("Container SAS URI: " + GetContainerSasUri(container));
    Console.WriteLine();

Compilar y ejecutar para la firma de acceso compartido URI para el nuevo contenedor de salida. La URI será similar al URI siguiente:       

    https://storageaccount.blob.core.windows.net/sascontainer?sv=2012-02-12&se=2013-04-13T00%3A12%3A08Z&sr=c&sp=wl&sig=t%2BbzU9%2B7ry4okULN9S0wst%2F8MCUhTjrHyV9rDNLSe8g%3D

Una vez que ha ejecutado el código, la firma de acceso compartido que creó en el contenedor será válida para un plazo de 24 horas. La firma concede un permiso de cliente para blobs de lista en el contenedor y escribir un nuevo blob al contenedor.

### <a name="generate-a-shared-access-signature-uri-for-a-blob"></a>Generar una firma URI de acceso compartido para un Blob

A continuación, pasaremos a crear un código similar para crear un nuevo blob dentro del contenedor y generar una firma de acceso compartido para el mismo. Esta firma acceso compartido no está asociada a una directiva de acceso almacenados, por lo que incluye la hora de inicio, fecha de caducidad y la información de permisos en el URI.

Agregar un nuevo método que crea un nuevo blob y escribir texto, a continuación, genera una firma de acceso compartido y devuelve la URI de firma:

    static string GetBlobSasUri(CloudBlobContainer container)
    {
        //Get a reference to a blob within the container.
        CloudBlockBlob blob = container.GetBlockBlobReference("sasblob.txt");

        //Upload text to the blob. If the blob does not yet exist, it will be created.
        //If the blob does exist, its existing content will be overwritten.
        string blobContent = "This blob will be accessible to clients via a Shared Access Signature.";
        MemoryStream ms = new MemoryStream(Encoding.UTF8.GetBytes(blobContent));
        ms.Position = 0;
        using (ms)
        {
            blob.UploadFromStream(ms);
        }

        //Set the expiry time and permissions for the blob.
        //In this case the start time is specified as a few minutes in the past, to mitigate clock skew.
        //The shared access signature will be valid immediately.
        SharedAccessBlobPolicy sasConstraints = new SharedAccessBlobPolicy();
        sasConstraints.SharedAccessStartTime = DateTime.UtcNow.AddMinutes(-5);
        sasConstraints.SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24);
        sasConstraints.Permissions = SharedAccessBlobPermissions.Read | SharedAccessBlobPermissions.Write;

        //Generate the shared access signature on the blob, setting the constraints directly on the signature.
        string sasBlobToken = blob.GetSharedAccessSignature(sasConstraints);

        //Return the URI string for the container, including the SAS token.
        return blob.Uri + sasBlobToken;
    }

En la parte inferior del método **Main()** , agregue las siguientes líneas para llamar **GetBlobSasUri()**, antes de la llamada a **Console.ReadLine ()**y escriba la firma de acceso compartido URI a la ventana de consola:    

    //Generate a SAS URI for a blob within the container, without a stored access policy.
    Console.WriteLine("Blob SAS URI: " + GetBlobSasUri(container));
    Console.WriteLine();


Compilar y ejecutar para la firma de acceso compartido URI para el nuevo blob de salida. La URI será similar al URI siguiente:

    https://storageaccount.blob.core.windows.net/sascontainer/sasblob.txt?sv=2012-02-12&st=2013-04-12T23%3A37%3A08Z&se=2013-04-13T00%3A12%3A08Z&sr=b&sp=rw&sig=dF2064yHtc8RusQLvkQFPItYdeOz3zR8zHsDMBi4S30%3D

### <a name="create-a-stored-access-policy-on-the-container"></a>Crear una directiva de acceso almacenadas en el contenedor

Ahora vamos a crear una directiva de acceso almacenadas en el contenedor, que se define las restricciones de las firmas de acceso compartido que están asociadas a él.

En los ejemplos anteriores, hemos especificado la hora de inicio (implícita o explícitamente), la fecha de caducidad y los permisos en la firma de acceso compartido URI propiamente dicho. En los ejemplos siguientes, se especifica en la directiva de acceso almacenado y no en la firma de acceso compartido. Si lo hace, nos permite cambiar estas restricciones sin volver a emitir la firma de acceso compartido.

Es posible tener una o varias de las restricciones en la firma de acceso compartido y el resto de la directiva de acceso almacenadas. Sin embargo, puede sólo especificar la hora de inicio, fecha de caducidad y permisos en un solo lugar u otro; Por ejemplo, no puede especificar los permisos en la firma de acceso compartido y también especificarlos en la directiva de acceso almacenadas.

Tenga en cuenta que al agregar una directiva de acceso a un contenedor, debe obtener los permisos existentes del contenedor, agregar la nueva directiva de acceso y a continuación, establezca los permisos del contenedor.

Agregar un nuevo método que crea una nueva directiva de acceso almacenadas en un contenedor y devuelve el nombre de la directiva:

    static void CreateSharedAccessPolicy(CloudBlobClient blobClient, CloudBlobContainer container,
        string policyName)
    {
        //Create a new shared access policy and define its constraints.
        SharedAccessBlobPolicy sharedPolicy = new SharedAccessBlobPolicy()
        {
            SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24),
            Permissions = SharedAccessBlobPermissions.Write | SharedAccessBlobPermissions.List | SharedAccessBlobPermissions.Read
        };

        //Get the container's existing permissions.
        BlobContainerPermissions permissions = container.GetPermissions();

        //Add the new policy to the container's permissions, and set the container's permissions.
        permissions.SharedAccessPolicies.Add(policyName, sharedPolicy);
        container.SetPermissions(permissions);
    }

En la parte inferior del método **Main()** , antes de la llamada a **Console.ReadLine ()**, agregue las siguientes líneas desactivar primero las directivas de acceso existente y, a continuación, llame al método **CreateSharedAccessPolicy()** :    

    //Clear any existing access policies on container.
    BlobContainerPermissions perms = container.GetPermissions();
    perms.SharedAccessPolicies.Clear();
    container.SetPermissions(perms);

    //Create a new access policy on the container, which may be optionally used to provide constraints for
    //shared access signatures on the container and the blob.
    string sharedAccessPolicyName = "tutorialpolicy";
    CreateSharedAccessPolicy(blobClient, container, sharedAccessPolicyName);

Tenga en cuenta que cuando desactive las directivas de acceso en un contenedor, primero deberá obtener los permisos existentes del contenedor, a continuación, desactive los permisos, después establecer los permisos de nuevo.

### <a name="generate-a-shared-access-signature-uri-on-the-container-that-uses-an-access-policy"></a>Generar una firma de acceso compartido URI en el contenedor que usa una directiva de acceso

A continuación, vamos a crear otra firma acceso compartido en el contenedor que hemos creado anteriormente, pero esta vez que se deberá asociar la firma con la directiva de acceso que se creó en el ejemplo anterior.

Agregar un nuevo método para generar la firma de acceso compartido a otro en el contenedor:

    static string GetContainerSasUriWithPolicy(CloudBlobContainer container, string policyName)
    {
        //Generate the shared access signature on the container. In this case, all of the constraints for the
        //shared access signature are specified on the stored access policy.
        string sasContainerToken = container.GetSharedAccessSignature(null, policyName);

        //Return the URI string for the container, including the SAS token.
        return container.Uri + sasContainerToken;
    }

En la parte inferior del método **Main()** , antes de la llamada a **Console.ReadLine ()**, agregue las siguientes líneas para llamar al método **GetContainerSasUriWithPolicy** :

    //Generate a SAS URI for the container, using a stored access policy to set constraints on the SAS.
    Console.WriteLine("Container SAS URI using stored access policy: " + GetContainerSasUriWithPolicy(container, sharedAccessPolicyName));
    Console.WriteLine();

### <a name="generate-a-shared-access-signature-uri-on-the-blob-that-uses-an-access-policy"></a>Generar una firma de acceso compartido URI en el Blob que usa una directiva de acceso

Por último, agregaremos un método similar para crear otro blob y generar una firma de acceso compartido que está asociada a una directiva de acceso.

Agregar un nuevo método para crear un blob y generar una firma de acceso compartido:

    static string GetBlobSasUriWithPolicy(CloudBlobContainer container, string policyName)
    {
        //Get a reference to a blob within the container.
        CloudBlockBlob blob = container.GetBlockBlobReference("sasblobpolicy.txt");

        //Upload text to the blob. If the blob does not yet exist, it will be created.
        //If the blob does exist, its existing content will be overwritten.
        string blobContent = "This blob will be accessible to clients via a shared access signature. " +
        "A stored access policy defines the constraints for the signature.";
        MemoryStream ms = new MemoryStream(Encoding.UTF8.GetBytes(blobContent));
        ms.Position = 0;
        using (ms)
        {
            blob.UploadFromStream(ms);
        }

        //Generate the shared access signature on the blob.
        string sasBlobToken = blob.GetSharedAccessSignature(null, policyName);

        //Return the URI string for the container, including the SAS token.
        return blob.Uri + sasBlobToken;
    }

En la parte inferior del método **Main()** , antes de la llamada a **Console.ReadLine ()**, agregue las siguientes líneas para llamar al método **GetBlobSasUriWithPolicy** :    

    //Generate a SAS URI for a blob within the container, using a stored access policy to set constraints on the SAS.
    Console.WriteLine("Blob SAS URI using stored access policy: " + GetBlobSasUriWithPolicy(container, sharedAccessPolicyName));
    Console.WriteLine();

El método **Main()** debe tener el siguiente aspecto en su totalidad. Ejecútelo para escribir la firma de acceso compartido URI a la ventana de consola y, a continuación, copiarlos y pegarlos en un archivo de texto para su uso en la segunda parte de este tutorial.

    static void Main(string[] args)
    {
        //Parse the connection string and return a reference to the storage account.
        CloudStorageAccount storageAccount = CloudStorageAccount.Parse(CloudConfigurationManager.GetSetting("StorageConnectionString"));

        //Create the blob client object.
        CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

        //Get a reference to a container to use for the sample code, and create it if it does not exist.
        CloudBlobContainer container = blobClient.GetContainerReference("sascontainer");
        container.CreateIfNotExists();

        //Generate a SAS URI for the container, without a stored access policy.
        Console.WriteLine("Container SAS URI: " + GetContainerSasUri(container));
        Console.WriteLine();

        //Generate a SAS URI for a blob within the container, without a stored access policy.
        Console.WriteLine("Blob SAS URI: " + GetBlobSasUri(container));
        Console.WriteLine();

        //Clear any existing access policies on container.
        BlobContainerPermissions perms = container.GetPermissions();
        perms.SharedAccessPolicies.Clear();
        container.SetPermissions(perms);

        //Create a new access policy on the container, which may be optionally used to provide constraints for
        //shared access signatures on the container and the blob.
        string sharedAccessPolicyName = "tutorialpolicy";
        CreateSharedAccessPolicy(blobClient, container, sharedAccessPolicyName);

        //Generate a SAS URI for the container, using a stored access policy to set constraints on the SAS.
        Console.WriteLine("Container SAS URI using stored access policy: " + GetContainerSasUriWithPolicy(container, sharedAccessPolicyName));
        Console.WriteLine();

        //Generate a SAS URI for a blob within the container, using a stored access policy to set constraints on the SAS.
        Console.WriteLine("Blob SAS URI using stored access policy: " + GetBlobSasUriWithPolicy(container, sharedAccessPolicyName));
        Console.WriteLine();

        Console.ReadLine();
    }

Cuando se ejecuta la aplicación de consola GenerateSharedAccessSignatures, verá un resultado similar al siguiente en la ventana de la consola. Estas son las firmas de acceso compartido que deberá usar en la parte 2 del tutorial.

![SA-consola-salida-1][sas-console-output-1]

## <a name="part-2-create-a-console-application-to-test-the-shared-access-signatures"></a>Parte 2: Crear una aplicación de consola para probar las firmas de acceso compartido

Para probar las firmas de acceso compartido creadas en los ejemplos anteriores, crearemos una segunda aplicación de consola que utiliza las firmas para realizar operaciones en el contenedor y en un blob.

> [AZURE.NOTE] Si han pasado más de 24 horas dado que completar la primera parte del tutorial, las firmas que genera ya no será válidas. En este caso, debe ejecutar el código en la primera aplicación de consola para generar firmas de un nuevo acceso compartido para su uso en la segunda parte del curso.

En Visual Studio, cree una nueva aplicación de consola de Windows y asígnele el nombre **ConsumeSharedAccessSignatures**. Agregue referencias a **Microsoft.WindowsAzure.Configuration.dll** y **Microsoft.WindowsAzure.Storage.dll**, como hizo anteriormente.

En la parte superior del archivo Program.cs, agregue las siguientes instrucciones **using** :

    using System.IO;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Blob;

En el cuerpo del método **Main()** , agregue las siguientes constantes y actualizar sus valores a las firmas de acceso compartido que generó en la parte 1 del tutorial.

    static void Main(string[] args)
    {
        string containerSAS = "<your container SAS>";
        string blobSAS = "<your blob SAS>";
        string containerSASWithAccessPolicy = "<your container SAS with access policy>";
        string blobSASWithAccessPolicy = "<your blob SAS with access policy>";
    }

### <a name="add-a-method-to-try-container-operations-using-a-shared-access-signature"></a>Agregar un método para probar las operaciones de contenedor con una firma de acceso compartido

A continuación, agregaremos un método que comprueba algunas operaciones de contenedor representante con una firma de acceso compartido en el contenedor. Observe que la firma de acceso compartido se utiliza para devolver una referencia al contenedor, autenticar el acceso al contenedor basado en la firma únicamente.

Agregar el siguiente método a Program.cs:

    static void UseContainerSAS(string sas)
    {
        //Try performing container operations with the SAS provided.

        //Return a reference to the container using the SAS URI.
        CloudBlobContainer container = new CloudBlobContainer(new Uri(sas));

        //Create a list to store blob URIs returned by a listing operation on the container.
        List<ICloudBlob> blobList = new List<ICloudBlob>();

        //Write operation: write a new blob to the container.
        try
        {
            CloudBlockBlob blob = container.GetBlockBlobReference("blobCreatedViaSAS.txt");
            string blobContent = "This blob was created with a shared access signature granting write permissions to the container. ";
            MemoryStream msWrite = new MemoryStream(Encoding.UTF8.GetBytes(blobContent));
            msWrite.Position = 0;
            using (msWrite)
            {
                blob.UploadFromStream(msWrite);
            }
            Console.WriteLine("Write operation succeeded for SAS " + sas);
            Console.WriteLine();
        }
        catch (StorageException e)
        {
            Console.WriteLine("Write operation failed for SAS " + sas);
            Console.WriteLine("Additional error information: " + e.Message);
            Console.WriteLine();
        }

        //List operation: List the blobs in the container.
        try
        {
            foreach (ICloudBlob blob in container.ListBlobs())
            {
                blobList.Add(blob);
            }
            Console.WriteLine("List operation succeeded for SAS " + sas);
            Console.WriteLine();
        }
        catch (StorageException e)
        {
            Console.WriteLine("List operation failed for SAS " + sas);
            Console.WriteLine("Additional error information: " + e.Message);
            Console.WriteLine();
        }

        //Read operation: Get a reference to one of the blobs in the container and read it.
        try
        {
            CloudBlockBlob blob = container.GetBlockBlobReference(blobList[0].Name);
            MemoryStream msRead = new MemoryStream();
            msRead.Position = 0;
            using (msRead)
            {
                blob.DownloadToStream(msRead);
                Console.WriteLine(msRead.Length);
            }
            Console.WriteLine("Read operation succeeded for SAS " + sas);
            Console.WriteLine();
        }
        catch (StorageException e)
        {
            Console.WriteLine("Read operation failed for SAS " + sas);
            Console.WriteLine("Additional error information: " + e.Message);
            Console.WriteLine();
        }
        Console.WriteLine();

        //Delete operation: Delete a blob in the container.
        try
        {
            CloudBlockBlob blob = container.GetBlockBlobReference(blobList[0].Name);
            blob.Delete();
            Console.WriteLine("Delete operation succeeded for SAS " + sas);
            Console.WriteLine();
        }
        catch (StorageException e)
        {
            Console.WriteLine("Delete operation failed for SAS " + sas);
            Console.WriteLine("Additional error information: " + e.Message);
            Console.WriteLine();
        }        
    }


Actualizar el método **Main()** para llamar **UseContainerSAS()** con los de las firmas de acceso compartido que creó en el contenedor:

    static void Main(string[] args)
    {
        string containerSAS = "<your container SAS>";
        string blobSAS = "<your blob SAS>";
        string containerSASWithAccessPolicy = "<your container SAS with access policy>";
        string blobSASWithAccessPolicy = "<your blob SAS with access policy>";

        //Call the test methods with the shared access signatures created on the container, with and without the access policy.
        UseContainerSAS(containerSAS);
        UseContainerSAS(containerSASWithAccessPolicy);

        Console.ReadLine();
    }


### <a name="add-a-method-to-try-blob-operations-using-a-shared-access-signature"></a>Agregar un método para probar las operaciones de Blob con una firma de acceso compartido

Por último, agregaremos un método que comprueba algunas operaciones de blob representante con una firma de acceso compartido en el blob. En este caso, usamos el constructor **CloudBlockBlob(String)**, pasando en la firma de acceso compartido, para devolver una referencia al objeto binario. Ninguna otra autenticación es necesario; se basa en la firma únicamente.

Agregar el siguiente método a Program.cs:

    static void UseBlobSAS(string sas)
    {
        //Try performing blob operations using the SAS provided.

        //Return a reference to the blob using the SAS URI.
        CloudBlockBlob blob = new CloudBlockBlob(new Uri(sas));

        //Write operation: Write a new blob to the container.
        try
        {
            string blobContent = "This blob was created with a shared access signature granting write permissions to the blob. ";
            MemoryStream msWrite = new MemoryStream(Encoding.UTF8.GetBytes(blobContent));
            msWrite.Position = 0;
            using (msWrite)
            {
                blob.UploadFromStream(msWrite);
            }
            Console.WriteLine("Write operation succeeded for SAS " + sas);
            Console.WriteLine();
        }
        catch (StorageException e)
        {
            Console.WriteLine("Write operation failed for SAS " + sas);
            Console.WriteLine("Additional error information: " + e.Message);
            Console.WriteLine();
        }

        //Read operation: Read the contents of the blob.
        try
        {
            MemoryStream msRead = new MemoryStream();
            using (msRead)
            {
                blob.DownloadToStream(msRead);
                msRead.Position = 0;
                using (StreamReader reader = new StreamReader(msRead, true))
                {
                    string line;
                    while ((line = reader.ReadLine()) != null)
                    {
                        Console.WriteLine(line);
                    }
                }
            }
            Console.WriteLine("Read operation succeeded for SAS " + sas);
            Console.WriteLine();
        }
        catch (StorageException e)
        {
            Console.WriteLine("Read operation failed for SAS " + sas);
            Console.WriteLine("Additional error information: " + e.Message);
            Console.WriteLine();
        }

        //Delete operation: Delete the blob.
        try
        {
            blob.Delete();
            Console.WriteLine("Delete operation succeeded for SAS " + sas);
            Console.WriteLine();
        }
        catch (StorageException e)
        {
            Console.WriteLine("Delete operation failed for SAS " + sas);
            Console.WriteLine("Additional error information: " + e.Message);
            Console.WriteLine();
        }        
    }


Actualizar el método **Main()** para llamar **UseBlobSAS()** con los de las firmas de acceso compartido que creó en el blob:

    static void Main(string[] args)
    {
        string containerSAS = "<your container SAS>";
        string blobSAS = "<your blob SAS>";
        string containerSASWithAccessPolicy = "<your container SAS with access policy>";
        string blobSASWithAccessPolicy = "<your blob SAS with access policy>";

        //Call the test methods with the shared access signatures created on the container, with and without the access policy.
        UseContainerSAS(containerSAS);
        UseContainerSAS(containerSASWithAccessPolicy);

        //Call the test methods with the shared access signatures created on the blob, with and without the access policy.
        UseBlobSAS(blobSAS);
        UseBlobSAS(blobSASWithAccessPolicy);

        Console.ReadLine();
    }

Ejecute la aplicación de consola y observe la salida para ver las operaciones que se permiten para que las firmas. El resultado en la ventana de consola tendrá un aspecto similar al siguiente:

![SA consola salida 2][sas-console-output-2]

## <a name="next-steps"></a>Pasos siguientes

[Acceso compartido firmas, parte 1: Información sobre el modelo de SA](storage-dotnet-shared-access-signature-part-1.md)

[Administrar el acceso de lectura anónimo contenedores y BLOB](storage-manage-access-to-resources.md)

[Delegar acceso con una firma de acceso compartido (API de REST)](http://msdn.microsoft.com/library/azure/ee395415.aspx)

[Introducción a tabla y cola SA](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/12/introducing-table-sas-shared-access-signature-queue-sas-and-update-to-blob-sas.aspx)

[sas-console-output-1]: ./media/storage-dotnet-shared-access-signature-part-2/sas-console-output-1.PNG
[sas-console-output-2]: ./media/storage-dotnet-shared-access-signature-part-2/sas-console-output-2.PNG
