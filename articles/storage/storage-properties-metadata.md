<properties
    pageTitle="Establecer y recuperar propiedades y metadatos de los objetos de almacenamiento de Azure | Microsoft Azure"
    description="Almacenar metadatos personalizados en los objetos de almacenamiento de Azure y establecer y recuperar propiedades del sistema."
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

# <a name="set-and-retrieve-properties-and-metadata"></a>Establecer y recuperar propiedades y metadatos #

## <a name="overview"></a>Información general

Objetos de propiedades del sistema de soporte técnico de almacenamiento de Azure y metadatos definidas por el usuario, además de los datos que contienen:

*   **Propiedades del sistema.** Propiedades del sistema existen en cada recurso de almacenamiento. Algunos de ellos pueden leer o establecer, mientras que otros son de solo lectura. En segundo plano, algunas propiedades del sistema corresponden a determinados encabezados HTTP estándar. La biblioteca de cliente de almacenamiento de Azure mantiene por usted.  

*   **Metadatos definidos por el usuario.** Metadatos definidos por el usuario son los metadatos que se especifican en un recurso determinado, en el formulario de un par de valor de nombre. Uso de metadatos para almacenar valores adicionales con un recurso de almacenamiento; Estos valores solo tienen fines su propio y no afectan a cómo se comporta el recurso.  

Recuperar los valores de propiedad y metadatos para un recurso de almacenamiento es un proceso de dos pasos. Para poder leer estos valores, debe obtenerlas explícitamente llamando al método **FetchAttributes** .

> [AZURE.IMPORTANT] Valores de propiedad y metadatos para un recurso de almacenamiento no se rellenan a menos que llame a uno de los métodos de **FetchAttributes** . 

## <a name="setting-and-retrieving-properties"></a>Establecer y recuperar propiedades

Para recuperar los valores de propiedad, llame al método **FetchAttributes** en el blob o contenedor para rellenar las propiedades y leer los valores.

Para establecer propiedades en un objeto, especifique el valor de propiedad y luego llamar al método **SetProperties** .

En el ejemplo siguiente se crea un contenedor y escribe algunos de sus valores de propiedad en una ventana de consola:

    //Parse the connection string for the storage account.
    const string ConnectionString = "DefaultEndpointsProtocol=https;AccountName=account-name;AccountKey=account-key";
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(ConnectionString);
    
    //Create the service client object for credentialed access to the Blob service.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Retrieve a reference to a container. 
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

    // Create the container if it does not already exist.
    container.CreateIfNotExists();

    // Fetch container properties and write out their values.
    container.FetchAttributes();
    Console.WriteLine("Properties for container {0}", container.StorageUri.PrimaryUri.ToString());
    Console.WriteLine("LastModifiedUTC: {0}", container.Properties.LastModified.ToString());
    Console.WriteLine("ETag: {0}", container.Properties.ETag);
    Console.WriteLine();

## <a name="setting-and-retrieving-metadata"></a>Establecer y recuperar metadatos

Puede especificar los metadatos como uno o más pares de valor de nombre de un recurso blob o contenedor. Para establecer los metadatos, agregar pares de valor de nombre a la colección de **metadatos** en el recurso y luego llamar al método **SetMetadata** para guardar los valores en el servicio.

> [AZURE.NOTE] El nombre de los metadatos debe cumplir las convenciones de nomenclatura para los identificadores de C#.
 
En el ejemplo siguiente se establece metadatos en un contenedor. Se establece un valor de uso del método **Add** de la colección. El otro valor se establece mediante la sintaxis de valor de clave implícita. Ambas son válidas.

    public static void AddContainerMetadata(CloudBlobContainer container)
    {
        //Add some metadata to the container.
        container.Metadata.Add("docType", "textDocuments");
        container.Metadata["category"] = "guidance";

        //Set the container's metadata.
        container.SetMetadata();
    }

Para recuperar metadatos, llame al método **FetchAttributes** en el blob o contenedor para rellenar la colección de **metadatos** , a continuación, leer los valores, como se muestra en el ejemplo siguiente.

    public static void ListContainerMetadata(CloudBlobContainer container)
    {
        //Fetch container attributes in order to populate the container's properties and metadata.
        container.FetchAttributes();

        //Enumerate the container's metadata.
        Console.WriteLine("Container metadata:");
        foreach (var metadataItem in container.Metadata)
        {
            Console.WriteLine("\tKey: {0}", metadataItem.Key);
            Console.WriteLine("\tValue: {0}", metadataItem.Value);
        }
    }

## <a name="see-also"></a>Vea también  

- [Biblioteca de cliente de almacenamiento de Azure para referencia .NET](http://msdn.microsoft.com/library/azure/wa_storage_30_reference_home.aspx)
- [Biblioteca de cliente de almacenamiento de Azure para el paquete de .NET](https://www.nuget.org/packages/WindowsAzure.Storage/) 
