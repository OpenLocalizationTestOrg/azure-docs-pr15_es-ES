<properties
    pageTitle="Cómo usar el almacenamiento de archivos de C++ | Microsoft Azure"
    description="Almacenar los datos de archivo en la nube con el almacenamiento de archivos de Azure."
    services="storage"
    documentationCenter=".net"
    authors="seguler"
    manager="jahogg"
    editor="tysonn" />

<tags ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/10/2016"
    ms.author="seguler" />

# <a name="how-to-use-file-storage-from-c"></a>Cómo usar el almacenamiento de archivos de C++

[AZURE.INCLUDE [storage-selector-file-include](../../includes/storage-selector-file-include.md)]
<br/>
[AZURE.INCLUDE [storage-try-azure-tools-files](../../includes/storage-try-azure-tools-files.md)]
[AZURE.INCLUDE [storage-file-overview-include](../../includes/storage-file-overview-include.md)]

## <a name="about-this-tutorial"></a>Acerca de este tutorial

En este tutorial, aprenderá cómo llevar a cabo operaciones básicas en el servicio de almacenamiento de archivos de Microsoft Azure. A través de ejemplos escritos en C++, aprenderá a crear recursos compartidos y directorios, cargar, de lista y eliminar archivos. Si es nuevo en el servicio de almacenamiento de archivos de Microsoft Azure, atravesando los conceptos en las secciones siguientes será muy útil entender los ejemplos.

[AZURE.INCLUDE [storage-file-concepts-include](../../includes/storage-file-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## <a name="create-a-c-application"></a>Crear una aplicación de C++

Para generar los ejemplos, debe instalar la biblioteca de cliente de almacenamiento de Azure 2.4.0 para C++. También debe crear una cuenta de almacenamiento de Azure.

Para instalar al cliente de almacenamiento de Azure 2.4.0 para C++, puede usar uno de los métodos siguientes:

-   **Linux:** Siga las instrucciones proporcionadas en la página de la [Biblioteca de cliente de almacenamiento de Azure para C++ Léame](https://github.com/Azure/azure-storage-cpp/blob/master/README.md) .

-   **Windows:** En Visual Studio, haga clic en **herramientas &gt; Administrador de paquetes de NuGet &gt; consola del Administrador de paquete**. Escriba el comando siguiente en la [consola del Administrador de paquetes de NuGet](http://docs.nuget.org/docs/start-here/using-the-package-manager-console) y presione **ENTRAR**.

    Paquete de instalación wastorage

## <a name="set-up-your-application-to-use-file-storage"></a>Configurar la aplicación para utilizar el almacenamiento de archivos

Agregue que las siguientes instrucciones a la parte superior del archivo C++ donde desea usar el almacenamiento de Azure API para tener acceso a archivos include:

    #include "was/storage_account.h"
    #include "was/file.h"

## <a name="set-up-an-azure-storage-connection-string"></a>Configurar una cadena de conexión de almacenamiento de Azure

Para usar el almacenamiento de archivos, debe conectarse a su cuenta de almacenamiento de Azure. Sería el primer paso configurar una cadena de conexión que se usará para conectarse a su cuenta de almacenamiento. Vamos a definir una variable estática para hacerlo.

    // Define the connection-string with your values.
    const utility::string_t 
    storage_connection_string(U("DefaultEndpointsProtocol=https;AccountName=your_storage_account;AccountKey=your_storage_account_key"));

## <a name="connecting-to-an-azure-storage-account"></a>Conectarse a una cuenta de almacenamiento de Azure

Puede usar la clase **cloud_storage_account** para representar la información de su cuenta de almacenamiento. Para recuperar información de su cuenta de almacenamiento de la cadena de conexión de almacenamiento, puede usar el método **analizar** .

    // Retrieve storage account from connection string. 
    azure::storage::cloud_storage_account storage_account = 
      azure::storage::cloud_storage_account::parse(storage_connection_string);

## <a name="how-to-create-a-share"></a>Cómo: crear un recurso compartido

Todos los archivos y directorios de almacenamiento de archivos residen en un contenedor de un **recurso compartido**. Su cuenta de almacenamiento puede tener tantas acciones como permite la capacidad de su cuenta. Para obtener acceso a un recurso compartido y su contenido, debe usar a un cliente de almacenamiento de archivos.

    // Create the file storage client.
    azure::storage::cloud_file_client file_client = 
      storage_account.create_cloud_file_client();

Usa al cliente de almacenamiento de archivos, puede obtener una referencia a un recurso compartido.

    // Get a reference to the file share
    azure::storage::cloud_file_share share = 
      file_client.get_share_reference(_XPLATSTR("my-sample-share"));

Para crear el recurso compartido, utilice el método **create_if_not_exists** del objeto **cloud_file_share** .

    if (share.create_if_not_exists()) { 
        std::wcout << U("New share created") << std::endl;  
    }

En este momento, **Compartir** contiene una referencia a un recurso compartido denominado **Compartir mi de ejemplo**.

## <a name="how-to-upload-a-file"></a>Cómo: cargar un archivo

Al menos, una cuota de almacenamiento de Azure archivo contiene un directorio raíz donde pueden residen los archivos. En esta sección, aprenderá cómo cargar un archivo de almacenamiento local en el directorio raíz de un recurso compartido.

Es el primer paso para cargar un archivo obtener una referencia al directorio donde debe residen. Para ello, llamando al método **get_root_directory_reference** del objeto de compartir.

    //Get a reference to the root directory for the share.
    azure::storage::cloud_file_directory root_dir = share.get_root_directory_reference();

Ahora que ya tiene una referencia al directorio raíz del recurso compartido, puede cargar un archivo en él. En este ejemplo se carga desde un archivo de texto y de una secuencia.

    // Upload a file from a stream.
    concurrency::streams::istream input_stream = 
      concurrency::streams::file_stream<uint8_t>::open_istream(_XPLATSTR("DataFile.txt")).get();

    azure::storage::cloud_file file1 = 
      root_dir.get_file_reference(_XPLATSTR("my-sample-file-1"));
    file1.upload_from_stream(input_stream);

    // Upload some files from text.
    azure::storage::cloud_file file2 = 
      root_dir.get_file_reference(_XPLATSTR("my-sample-file-2"));
    file2.upload_text(_XPLATSTR("more text"));

    // Upload a file from a file.
    azure::storage::cloud_file file4 = 
      root_dir.get_file_reference(_XPLATSTR("my-sample-file-3"));
    file4.upload_from_file(_XPLATSTR("DataFile.txt"));  

## <a name="how-to-create-a-directory"></a>Cómo: crear un directorio

También puede organizar el almacenamiento colocando archivos de subdirectorios en lugar de tener todos ellos en el directorio raíz. El servicio de almacenamiento de archivos de Azure le permite crear tantos directorios que le permitirá a su cuenta. El código siguiente creará un directorio denominado **Mi directorio de ejemplo** en el directorio de raíz, así como un subdirectorio denominado **mi subdirectorio de ejemplo**.
    
    // Retrieve a reference to a directory
    azure::storage::cloud_file_directory directory = share.get_directory_reference(_XPLATSTR("my-sample-directory"));
    
    // Return value is true if the share did not exist and was successfully created.
    directory.create_if_not_exists();
    
    // Create a subdirectory.
    azure::storage::cloud_file_directory subdirectory = 
      directory.get_subdirectory_reference(_XPLATSTR("my-sample-subdirectory"));
    subdirectory.create_if_not_exists();

## <a name="how-to-list-files-and-directories-in-a-share"></a>Cómo: lista de archivos y directorios de un recurso compartido

Obtener una lista de archivos y directorios dentro de un recurso compartido se realiza fácilmente mediante una llamada **list_files_and_directories** en una referencia de **cloud_file_directory** . Para obtener acceso amplio conjunto de métodos y propiedades para un devuelto **list_file_and_directory_item**, debe llamar al método **list_file_and_directory_item.as_file** para obtener un objeto **cloud_file** o el método **list_file_and_directory_item.as_directory** para obtener un objeto **cloud_file_directory** .

El código siguiente muestra cómo recuperar y salida URI de cada elemento en el directorio raíz del recurso compartido.

    //Get a reference to the root directory for the share.
    azure::storage::cloud_file_directory root_dir = 
      share.get_root_directory_reference();
    
    // Output URI of each item.
    azure::storage::list_file_and_diretory_result_iterator end_of_results;
    
    for (auto it = directory.list_files_and_directories(); it != end_of_results; ++it)
    {
        if(it->is_directory())
        {
            ucout << "Directory: " << it->as_directory().uri().primary_uri().to_string() << std::endl;
        }
        else if (it->is_file())
        {
            ucout << "File: " << it->as_file().uri().primary_uri().to_string() << std::endl;
        }       
    }
    

## <a name="how-to-download-a-file"></a>Cómo: descargar un archivo

Para descargar archivos, primero recuperar una referencia de archivo y, a continuación, llame al método **download_to_stream** para transferir el contenido del archivo a un objeto de secuencia que, a continuación, puede conservar en un archivo local. Como alternativa, puede usar el método **download_to_file** para descargar el contenido de un archivo a un archivo local. Puede usar el método **download_text** para descargar el contenido de un archivo como una cadena de texto.

El ejemplo siguiente utiliza los métodos **download_to_stream** y **download_text** para demostrar a descargar los archivos que se crearon en las secciones anteriores.

    // Download as text
    azure::storage::cloud_file text_file = 
      root_dir.get_file_reference(_XPLATSTR("my-sample-file-2"));
    utility::string_t text = text_file.download_text();
    ucout << "File Text: " << text << std::endl;
    
    // Download as a stream.
    azure::storage::cloud_file stream_file = 
      root_dir.get_file_reference(_XPLATSTR("my-sample-file-3"));
    
    concurrency::streams::container_buffer<std::vector<uint8_t>> buffer;
    concurrency::streams::ostream output_stream(buffer);
    stream_file.download_to_stream(output_stream);
    std::ofstream outfile("DownloadFile.txt", std::ofstream::binary);
    std::vector<unsigned char>& data = buffer.collection();
    outfile.write((char *)&data[0], buffer.size());
    outfile.close();

## <a name="how-to-delete-a-file"></a>Cómo: eliminar un archivo

Otra operación de almacenamiento de archivo común es la eliminación de archivos. El siguiente código elimina un archivo con el nombre de mi-muestra-archivo-3 almacenado en el directorio raíz.

    // Get a reference to the root directory for the share. 
    azure::storage::cloud_file_share share = 
      file_client.get_share_reference(_XPLATSTR("my-sample-share"));
    
    azure::storage::cloud_file_directory root_dir = 
      share.get_root_directory_reference();
    
    azure::storage::cloud_file file = 
      root_dir.get_file_reference(_XPLATSTR("my-sample-file-3"));
    
    file.delete_file_if_exists();

## <a name="how-to-delete-a-directory"></a>Cómo: eliminar un directorio

Eliminar un directorio es una tarea sencilla, aunque se debe tener en cuenta que no se puede eliminar un directorio que contenga archivos o en otros directorios.
    
    // Get a reference to the share.
    azure::storage::cloud_file_share share = 
      file_client.get_share_reference(_XPLATSTR("my-sample-share"));
    
    // Get a reference to the directory.
    azure::storage::cloud_file_directory directory = 
      share.get_directory_reference(_XPLATSTR("my-sample-directory"));
    
    // Get a reference to the subdirectory you want to delete.
    azure::storage::cloud_file_directory sub_directory =
      directory.get_subdirectory_reference(_XPLATSTR("my-sample-subdirectory"));
    
    // Delete the subdirectory and the sample directory.
    sub_directory.delete_directory_if_exists();
    
    directory.delete_directory_if_exists();

## <a name="how-to-delete-a-share"></a>Cómo: eliminar un recurso compartido

Eliminar un recurso compartido se realiza llamando al método de **delete_if_exists** en un objeto cloud_file_share. Aquí es el código de ejemplo que lo hace.
    
    // Get a reference to the share.
    azure::storage::cloud_file_share share = 
      file_client.get_share_reference(_XPLATSTR("my-sample-share"));
    
    // delete the share if exists
    share.delete_share_if_exists();

## <a name="set-the-maximum-size-for-a-file-share"></a>Establecer el tamaño máximo de un archivo compartido

Puede establecer la cuota (o el tamaño máximo) para un archivo compartido, en gigabytes. También puede comprobar para ver la cantidad de datos actualmente se almacena en el recurso compartido.

Al establecer la cuota de un recurso compartido, puede limitar el tamaño total de los archivos almacenados en el recurso compartido. Si el tamaño total de archivos en el recurso compartido supera la cuota establecida en el recurso compartido, a continuación, los clientes podrán aumentar el tamaño de los archivos existentes o crear nuevos archivos, a menos que los archivos están vacíos.

El ejemplo siguiente muestra cómo el uso actual para un recurso compartido y cómo configurar la cuota para el recurso compartido.

    // Parse the connection string for the storage account.
    azure::storage::cloud_storage_account storage_account = 
      azure::storage::cloud_storage_account::parse(storage_connection_string);
    
    // Create the file client.
    azure::storage::cloud_file_client file_client = 
      storage_account.create_cloud_file_client();
    
    // Get a reference to the share.
    azure::storage::cloud_file_share share = 
      file_client.get_share_reference(_XPLATSTR("my-sample-share"));
    if (share.exists())
    {
        std::cout << "Current share usage: " << share.download_share_usage() << "/" << share.properties().quota();
    
        //This line sets the quota to 2560GB
        share.resize(2560);
    
        std::cout << "Quota increased: " << share.download_share_usage() << "/" << share.properties().quota();
    
    }

## <a name="generate-a-shared-access-signature-for-a-file-or-file-share"></a>Generar una firma de acceso compartido para un archivo o recurso compartido de archivos

Puede generar una firma de acceso compartido (SA) para un recurso compartido de archivos o un archivo individual. También puede crear una directiva de acceso compartido en un recurso compartido de archivos para administrar el acceso compartido firmas. Se recomienda crear una directiva de acceso compartido, ya que proporciona un medio de revocar las SA si debe estar en peligro.

En el ejemplo siguiente se crea una directiva de acceso compartido en un recurso compartido y, a continuación, utilice esa directiva para proporcionar las restricciones de una SA en un archivo en el recurso compartido.

    // Parse the connection string for the storage account.
    azure::storage::cloud_storage_account storage_account = 
      azure::storage::cloud_storage_account::parse(storage_connection_string);
    
    // Create the file client and get a reference to the share
    azure::storage::cloud_file_client file_client = 
      storage_account.create_cloud_file_client();
    
    azure::storage::cloud_file_share share = 
      file_client.get_share_reference(_XPLATSTR("my-sample-share"));
    
    if (share.exists())
    {
        // Create and assign a policy
        utility::string_t policy_name = _XPLATSTR("sampleSharePolicy");

        azure::storage::file_shared_access_policy sharedPolicy = 
          azure::storage::file_shared_access_policy();

        //set permissions to expire in 90 minutes
        sharedPolicy.set_expiry(utility::datetime::utc_now() + 
          utility::datetime::from_minutes(90));

        //give read and write permissions
        sharedPolicy.set_permissions(azure::storage::file_shared_access_policy::permissions::write | azure::storage::file_shared_access_policy::permissions::read);

        //set permissions for the share
        azure::storage::file_share_permissions permissions; 

        //retrieve the current list of shared access policies
        azure::storage::shared_access_policies<azure::storage::file_shared_access_policy> policies;
        
        //add the new shared policy
        policies.insert(std::make_pair(policy_name, sharedPolicy));

        //save the updated policy list
        permissions.set_policies(policies);
        share.upload_permissions(permissions);

        //Retrieve the root directory and file references
        azure::storage::cloud_file_directory root_dir = 
          share.get_root_directory_reference();
        azure::storage::cloud_file file = 
          root_dir.get_file_reference(_XPLATSTR("my-sample-file-1"));

        // Generate a SAS for a file in the share 
        //  and associate this access policy with it.       
        utility::string_t sas_token = file.get_shared_access_signature(sharedPolicy);
        
        // Create a new CloudFile object from the SAS, and write some text to the file.     
        azure::storage::cloud_file file_with_sas(azure::storage::storage_credentials(sas_token).transform_uri(file.uri().primary_uri()));
        utility::string_t text = _XPLATSTR("My sample content");        
        file_with_sas.upload_text(text);        
        
        //Download and print URL with SAS.
        utility::string_t downloaded_text = file_with_sas.download_text();      
        ucout << downloaded_text << std::endl;      
        ucout << azure::storage::storage_credentials(sas_token).transform_uri(file.uri().primary_uri()).to_string() << std::endl;
    
    }

Para obtener más información sobre cómo crear y usar firmas de acceso compartido, consulte [Uso compartido acceso firmas (SA)](storage-dotnet-shared-access-signature-part-1.md).

## <a name="next-steps"></a>Pasos siguientes

Para obtener más información sobre el almacenamiento de Azure, explore estos recursos:

-   [Biblioteca de cliente de almacenamiento para C++](https://github.com/Azure/azure-storage-cpp)

-   [Explorador de almacenamiento de Azure](http://go.microsoft.com/fwlink/?LinkID=822673&clcid=0x409)

-   [Documentación de almacenamiento de Azure](https://azure.microsoft.com/documentation/services/storage/)
