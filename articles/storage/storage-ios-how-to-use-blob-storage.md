<properties
    pageTitle="Cómo usar el almacenamiento de blobs de Windows Azure de iOS | Microsoft Azure"
    description="Almacenar datos estructurados en la nube con el almacenamiento de blobs de Windows Azure (almacenamiento de objetos)."
    services="storage"
    documentationCenter="ios"
    authors="micurd"
    manager="jahogg"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="objective-c"
    ms.topic="article"
    ms.date="10/18/2016"
    ms.author="micurd"/>

# <a name="how-to-use-blob-storage-from-ios"></a>Cómo usar el almacenamiento de blobs de iOS

[AZURE.INCLUDE [storage-selector-blob-include](../../includes/storage-selector-blob-include.md)]
<br/>
[AZURE.INCLUDE [storage-try-azure-tools-blobs](../../includes/storage-try-azure-tools-blobs.md)]

## <a name="overview"></a>Información general

En este artículo le mostrará cómo llevar a cabo escenarios comunes mediante el almacenamiento de blobs de Microsoft Azure. Los ejemplos se escriben en el objetivo C y usar la [Biblioteca de cliente de almacenamiento de Azure para iOS](https://github.com/Azure/azure-storage-ios). Los escenarios cubiertos incluyen **Cargando**, **listado**, **Descargar**y **Eliminar** BLOB. Para obtener más información sobre BLOB, vea la sección [Pasos siguientes](#next-steps) . También puede descargar la [aplicación de ejemplo](https://github.com/Azure/azure-storage-ios/tree/master/BlobSample) para ver rápidamente el uso de almacenamiento de Azure en una aplicación de iOS.

[AZURE.INCLUDE [storage-blob-concepts-include](../../includes/storage-blob-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## <a name="import-the-azure-storage-ios-library-into-your-application"></a>Importar la biblioteca de iOS de almacenamiento de Azure en la aplicación

Puede importar la biblioteca de iOS de almacenamiento de Azure en la aplicación utilizando el [CocoaPod de almacenamiento de Azure](https://cocoapods.org/pods/AZSClient) o importando el archivo de **marco** .

## <a name="cocoapod"></a>CocoaPod

1. Si aún no lo ha hecho, [Instale CocoaPods](https://guides.cocoapods.org/using/getting-started.html#toc_3) en el equipo, abra una ventana de terminal y ejecutar el comando siguiente

        sudo gem install cocoapods

2. A continuación, en el directorio del proyecto (el directorio que contiene su `.xcodeproj` archivo), cree un nuevo archivo llamado `Podfile`(sin extensión de archivo). Agregue lo siguiente a `Podfile` y guardar

        pod 'AZSClient'

3. En la ventana de terminal, vaya al directorio del proyecto y ejecute el siguiente comando

        pod install

4. Si su `.xcodeproj` está abierto en Xcode, ciérrelo. En el directorio del proyecto, abra el archivo de proyecto recién creado que tendrá la `.xcworkspace` extensión. Este es el archivo que deberá trabajar desde de ahora en.

## <a name="framework"></a>Marco de trabajo
Para usar la biblioteca de almacenamiento de Azure iOS, primero deberá crear el archivo de marco.

1. En primer lugar, descargar o clonar el [almacenamiento de azure de ios repo](https://github.com/azure/azure-storage-ios).

2. Vaya a *ios de almacenamiento de azure* -> *biblioteca* -> *Biblioteca de cliente de almacenamiento de Azure*y abrir `AZSClient.xcodeproj` en Xcode.

3. En la parte superior izquierda de Xcode, cambiar la combinación de activa de "Biblioteca de cliente de almacenamiento de Azure" a "Framework".

4. Genere el proyecto (⌘ + B). Esta opción creará una `AZSClient.framework` archivo en el escritorio.

A continuación, puede importar el archivo de marco de trabajo en la aplicación haciendo lo siguiente:

1. Crear un nuevo proyecto o abra un proyecto existente en Xcode.

2. Haga clic en el proyecto en el panel de navegación izquierdo y haga clic en la pestaña *General* , en la parte superior del editor del proyecto.

3. En la sección *marcos vinculados y bibliotecas* , haga clic en el botón Agregar (+).

4. Haga clic en *Agregar otro...*. Vaya a y agregar la `AZSClient.framework` archivo que acaba de crear.

5. En la sección *marcos vinculados y bibliotecas* , haga clic en el botón Agregar (+).

6. En la lista de bibliotecas que ha proporcionado, busque `libxml2.2.dylib` y lo agrega a su proyecto.

7. Haga clic en la pestaña *Configuración de generación* en la parte superior del editor del proyecto.

8. En la sección *Rutas de búsqueda* , haga doble clic en *Rutas de acceso de búsqueda de Framework* y agregar la ruta de acceso a su `AZSClient.framework` archivo.

## <a name="import-statement"></a>Declaración de importación
Debe incluir la siguiente instrucción de importación del archivo donde desea invocar la API de almacenamiento de Azure.

    // Include the following import statement to use blob APIs.
    #import <AZSClient/AZSClient.h>

[AZURE.INCLUDE [storage-mobile-authentication-guidance](../../includes/storage-mobile-authentication-guidance.md)]

## <a name="asynchronous-operations"></a>Operaciones asincrónicas
> [AZURE.NOTE] Todos los métodos que lleve a cabo una solicitud con el servicio son operaciones asincrónicas. En los ejemplos de código, encontrará que estos métodos tienen un controlador de finalización. Código dentro del controlador de finalización ejecutará **después de** que completar la solicitud. Código de controlador de finalización ejecutará **mientras** la solicitud se realiza.

## <a name="create-a-container"></a>Crear un contenedor
Cada blob en el almacenamiento de Azure debe estar en un contenedor. En el ejemplo siguiente se muestra cómo crear un contenedor, denominado *newcontainer*, en su cuenta de almacenamiento si aún no existe. Al elegir un nombre para el contenedor, tenga en cuenta las reglas de nomenclatura mencionados arriba.

    -(void)createContainer{
      NSError *accountCreationError;

      // Create a storage account object from a connection string.
      AZSCloudStorageAccount *account = [AZSCloudStorageAccount accountFromConnectionString:@"DefaultEndpointsProtocol=https;AccountName=your_account_name_here;AccountKey=your_account_key_here" error:&accountCreationError];

      if(accountCreationError){
         NSLog(@"Error in creating account.");
      }

      // Create a blob service client object.
      AZSCloudBlobClient *blobClient = [account getBlobClient];

      // Create a local container object.
      AZSCloudBlobContainer *blobContainer = [blobClient containerReferenceFromName:@"newcontainer"];

      // Create container in your Storage account if the container doesn't already exist
      [blobContainer createContainerIfNotExistsWithCompletionHandler:^(NSError *error, BOOL exists) {
         if (error){
             NSLog(@"Error in creating container.");
         }
      }];
    }

Para confirmar que esto funciona en el [Explorador de Microsoft Azure almacenamiento](http://storageexplorer.com) y comprobar que *newcontainer* está en la lista de contenedores para su cuenta de almacenamiento.

## <a name="set-container-permissions"></a>Establecer permisos de contenedor
Permisos del contenedor están configurados para acceso **privado** de forma predeterminada. Sin embargo, los contenedores proporcionan algunas opciones diferentes para el acceso de contenedor:

- **Privado**: contenedor y blob pueden leer datos solo el propietario de la cuenta.

- **Blobs**: datos Blob dentro de este contenedor se pueden leer a través de la solicitud anónima, pero los datos de contenedor no están disponibles. Los clientes no pueden enumerar BLOB dentro del contenedor a través de la solicitud anónima.

- **Contenedor**: pueden leer los datos de contenedor y blob a través de la solicitud anónima. Los clientes pueden enumerar BLOB dentro del contenedor a través de la solicitud anónima, pero no pueden enumerar los contenedores dentro de la cuenta de almacenamiento.

En el ejemplo siguiente se muestra cómo crear un contenedor con permisos de acceso de **contenedor** que le permita el acceso público sólo lectura para todos los usuarios en Internet:

    -(void)createContainerWithPublicAccess{
        NSError *accountCreationError;

        // Create a storage account object from a connection string.
        AZSCloudStorageAccount *account = [AZSCloudStorageAccount accountFromConnectionString:@"DefaultEndpointsProtocol=https;AccountName=your_account_name_here;AccountKey=your_account_key_here" error:&accountCreationError];

        if(accountCreationError){
            NSLog(@"Error in creating account.");
        }

        // Create a blob service client object.
        AZSCloudBlobClient *blobClient = [account getBlobClient];

        // Create a local container object.
        AZSCloudBlobContainer *blobContainer = [blobClient containerReferenceFromName:@"containerpublic"];

        // Create container in your Storage account if the container doesn't already exist
        [blobContainer createContainerIfNotExistsWithAccessType:AZSContainerPublicAccessTypeContainer requestOptions:nil operationContext:nil completionHandler:^(NSError *error, BOOL exists){
            if (error){
                NSLog(@"Error in creating container.");
            }
        }];
    }

## <a name="upload-a-blob-into-a-container"></a>Cargar un blob en un contenedor
Como se mencionó en la sección de [conceptos de servicio de blobs](#blob-service-concepts) , almacenamiento de blobs ofrece tres tipos diferentes de BLOB: bloquear BLOB, anexar BLOB y BLOB de página. En este momento, la biblioteca de almacenamiento de Azure iOS solo es compatible con BLOB de bloque. En la mayoría de los casos, blobs de bloque es el tipo recomendado para usar.

En el ejemplo siguiente se muestra cómo cargar un blob de bloque de un NSString. Si ya existe un blob con el mismo nombre en este contenedor, se sobrescribirá el contenido de este blob.

    -(void)uploadBlobToContainer{
        NSError *accountCreationError;

        // Create a storage account object from a connection string.
        AZSCloudStorageAccount *account = [AZSCloudStorageAccount accountFromConnectionString:@"DefaultEndpointsProtocol=https;AccountName=your_account_name_here;AccountKey=your_account_key_here" error:&accountCreationError];

        if(accountCreationError){
            NSLog(@"Error in creating account.");
        }

        // Create a blob service client object.
        AZSCloudBlobClient *blobClient = [account getBlobClient];

        // Create a local container object.
        AZSCloudBlobContainer *blobContainer = [blobClient containerReferenceFromName:@"containerpublic"];

        [blobContainer createContainerIfNotExistsWithAccessType:AZSContainerPublicAccessTypeContainer requestOptions:nil operationContext:nil completionHandler:^(NSError *error, BOOL exists)
         {
             if (error){
                 NSLog(@"Error in creating container.");
             }
             else{
                 // Create a local blob object
                 AZSCloudBlockBlob *blockBlob = [blobContainer blockBlobReferenceFromName:@"sampleblob"];

                 // Upload blob to Storage
                 [blockBlob uploadFromText:@"This text will be uploaded to Blob Storage." completionHandler:^(NSError *error) {
                     if (error){
                         NSLog(@"Error in creating blob.");
                     }
                 }];
             }
         }];
    }

Para confirmar que esto funciona en el [Explorador de Microsoft Azure almacenamiento](http://storageexplorer.com) y comprobar que el contenedor, *containerpublic*, contiene el blob, *sampleblob*. En este ejemplo, hemos usado un contenedor público, por lo que también puede comprobar que esto ha funcionado yendo a la BLOB URI:

    https://nameofyourstorageaccount.blob.core.windows.net/containerpublic/sampleblob

Además de cargar un blob de bloque de un NSString, existen métodos similares para NSData, NSInputStream o un archivo local.

## <a name="list-the-blobs-in-a-container"></a>El BLOB en un contenedor de lista
En el ejemplo siguiente se muestra cómo mostrar todos los blobs en un contenedor. Al realizar esta operación, tenga en cuenta los siguientes parámetros:     

- **continuationToken** - el token de continuación representa donde debe comenzar la operación de la lista. Si no se proporciona ningún token, obtendrá una lista BLOB desde el principio. Cualquier número de BLOB puede figurar desde cero hasta un máximo de conjunto. Incluso si este método devuelve resultados de cero, si `results.continuationToken` no es nulo, puede haber más BLOB en el servicio que no se han enumerado.
- **prefijo** - puede especificar el prefijo que se utilizará para el listado de blobs de Windows. Se mostrarán sólo BLOB que comienzan con el prefijo.
- **useFlatBlobListing** - como se mencionó en la sección de [nomenclatura y referencia contenedores y BLOB](#naming-and-referencing-containers-and-blobs) , aunque el servicio de Blob es una combinación de plano de almacenamiento, puede crear una jerarquía virtual BLOB con información de la ruta de acceso de los nombres. Sin embargo, no plana listado no es compatible actualmente; estará disponible próximamente. Por ahora, este valor debe ser`YES`
- **blobListingDetails** - puede especificar qué elementos desea incluir al listado BLOB
    - `AZSBlobListingDetailsNone`: Lista solo confirmada BLOB y no devuelven blob metadatos.
    - `AZSBlobListingDetailsSnapshots`: Lista confirmada BLOB e instantáneas de blobs de Windows.
    - `AZSBlobListingDetailsMetadata`: Recuperar blob metadatos para cada blob devuelven en la lista.
    - `AZSBlobListingDetailsUncommittedBlobs`: Lista de BLOB confirmada y sin confirmar.
    - `AZSBlobListingDetailsCopy`: Incluir propiedades de copia en la lista.
    - `AZSBlobListingDetailsAll`: Lista de todos los BLOB confirmada disponible, BLOB sin confirmar e instantáneas y devolver todos los metadatos y copiar el estado de esos BLOB.
- **maxResults** - el número máximo de resultados para devolver para realizar esta operación. Use -1 para no ha establecido un límite.
- **completionHandler** - el bloque de código para ejecutar con los resultados de la operación de la lista.

En este ejemplo, un método auxiliar se utiliza para llamar de forma recursiva la lista blobs método cada vez que se devuelve un token de continuación.

    -(void)listBlobsInContainer{
        NSError *accountCreationError;

        // Create a storage account object from a connection string.
        AZSCloudStorageAccount *account = [AZSCloudStorageAccount accountFromConnectionString:@"DefaultEndpointsProtocol=https;AccountName=your_account_name_here;AccountKey=your_account_key_here" error:&accountCreationError];

        if(accountCreationError){
            NSLog(@"Error in creating account.");
        }

        // Create a blob service client object.
        AZSCloudBlobClient *blobClient = [account getBlobClient];

        // Create a local container object.
        AZSCloudBlobContainer *blobContainer = [blobClient containerReferenceFromName:@"containerpublic"];

        //List all blobs in container
        [self listBlobsInContainerHelper:blobContainer continuationToken:nil prefix:nil blobListingDetails:AZSBlobListingDetailsAll maxResults:-1 completionHandler:^(NSError *error) {
            if (error != nil){
                NSLog(@"Error in creating container.");
            }
        }];
    }

    //List blobs helper method
    -(void)listBlobsInContainerHelper:(AZSCloudBlobContainer *)container continuationToken:(AZSContinuationToken *)continuationToken prefix:(NSString *)prefix blobListingDetails:(AZSBlobListingDetails)blobListingDetails maxResults:(NSUInteger)maxResults completionHandler:(void (^)(NSError *))completionHandler
    {
        [container listBlobsSegmentedWithContinuationToken:continuationToken prefix:prefix useFlatBlobListing:YES blobListingDetails:blobListingDetails maxResults:maxResults completionHandler:^(NSError *error, AZSBlobResultSegment *results) {
            if (error)
            {
                completionHandler(error);
            }
            else
            {
                for (int i = 0; i < results.blobs.count; i++) {
                    NSLog(@"%@",[(AZSCloudBlockBlob *)results.blobs[i] blobName]);
                }
                if (results.continuationToken)
                {
                    [self listBlobsInContainerHelper:container continuationToken:results.continuationToken prefix:prefix blobListingDetails:blobListingDetails maxResults:maxResults completionHandler:completionHandler];
                }
                else
                {
                    completionHandler(nil);
                }
            }
        }];
    }


## <a name="download-a-blob"></a>Descargar un blob

En el ejemplo siguiente se muestra cómo descargar un blob a un objeto NSString.

    -(void)downloadBlobToString{
        NSError *accountCreationError;

        // Create a storage account object from a connection string.
        AZSCloudStorageAccount *account = [AZSCloudStorageAccount accountFromConnectionString:@"DefaultEndpointsProtocol=https;AccountName=your_account_name_here;AccountKey=your_account_key_here" error:&accountCreationError];

        if(accountCreationError){
            NSLog(@"Error in creating account.");
        }

        // Create a blob service client object.
        AZSCloudBlobClient *blobClient = [account getBlobClient];

        // Create a local container object.
        AZSCloudBlobContainer *blobContainer = [blobClient containerReferenceFromName:@"containerpublic"];

        // Create a local blob object
        AZSCloudBlockBlob *blockBlob = [blobContainer blockBlobReferenceFromName:@"sampleblob"];

        // Download blob
        [blockBlob downloadToTextWithCompletionHandler:^(NSError *error, NSString *text) {
            if (error) {
                NSLog(@"Error in downloading blob");
            }
            else{
                NSLog(@"%@",text);
            }
        }];
    }

## <a name="delete-a-blob"></a>Eliminar un blob

En el ejemplo siguiente se muestra cómo eliminar un blob.

    -(void)deleteBlob{
        NSError *accountCreationError;

        // Create a storage account object from a connection string.
        AZSCloudStorageAccount *account = [AZSCloudStorageAccount accountFromConnectionString:@"DefaultEndpointsProtocol=https;AccountName=your_account_name_here;AccountKey=your_account_key_here" error:&accountCreationError];

        if(accountCreationError){
            NSLog(@"Error in creating account.");
        }

        // Create a blob service client object.
        AZSCloudBlobClient *blobClient = [account getBlobClient];

        // Create a local container object.
        AZSCloudBlobContainer *blobContainer = [blobClient containerReferenceFromName:@"containerpublic"];

        // Create a local blob object
        AZSCloudBlockBlob *blockBlob = [blobContainer blockBlobReferenceFromName:@"sampleblob1"];

        // Delete blob
        [blockBlob deleteWithCompletionHandler:^(NSError *error) {
            if (error) {
                NSLog(@"Error in deleting blob.");
            }
        }];
    }

## <a name="delete-a-blob-container"></a>Eliminar un contenedor de blob

En el ejemplo siguiente se muestra cómo eliminar un contenedor.

    -(void)deleteContainer{
      NSError *accountCreationError;

      // Create a storage account object from a connection string.
      AZSCloudStorageAccount *account = [AZSCloudStorageAccount accountFromConnectionString:@"DefaultEndpointsProtocol=https;AccountName=your_account_name_here;AccountKey=your_account_key_here" error:&accountCreationError];

      if(accountCreationError){
         NSLog(@"Error in creating account.");
      }

      // Create a blob service client object.
      AZSCloudBlobClient *blobClient = [account getBlobClient];

      // Create a local container object.
      AZSCloudBlobContainer *blobContainer = [blobClient containerReferenceFromName:@"containerpublic"];

      // Delete container
      [blobContainer deleteContainerIfExistsWithCompletionHandler:^(NSError *error, BOOL success) {
         if(error){
             NSLog(@"Error in deleting container");
         }
      }];
    }

## <a name="next-steps"></a>Pasos siguientes

Ahora que ha aprendido a usar el almacenamiento de blobs de iOS, siga estos vínculos para obtener más información acerca de la biblioteca de iOS y el servicio de almacenamiento.

- [Biblioteca de cliente de almacenamiento de Azure para iOS](https://github.com/azure/azure-storage-ios)
- [Azure almacenamiento iOS documentación de referencia](http://azure.github.io/azure-storage-ios/)
- [API de REST de servicios de almacenamiento de Azure](https://msdn.microsoft.com/library/azure/dd179355.aspx)
- [Blog del equipo de almacenamiento de Azure](http://blogs.msdn.com/b/windowsazurestorage)

Si tiene preguntas sobre esta biblioteca no dude en publicar en nuestro [foro de MSDN Azure](http://social.msdn.microsoft.com/Forums/windowsazure/home?forum=windowsazuredata) o [Desbordamiento de pila](http://stackoverflow.com/questions/tagged/windows-azure-storage+or+windows-azure-storage+or+azure-storage-blobs+or+azure-storage-tables+or+azure-table-storage+or+windows-azure-queues+or+azure-storage-queues+or+azure-storage-emulator+or+azure-storage-files).
Si tiene sugerencias de características para el almacenamiento de Azure, envíe a [Los comentarios de almacenamiento de Azure](https://feedback.azure.com/forums/217298-storage/).
