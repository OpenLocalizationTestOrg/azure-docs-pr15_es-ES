<properties
    pageTitle="Tutorial: Cifrar y descifrar BLOB en Microsoft Azure almacenamiento mediante depósito de clave de Azure | Microsoft Azure"
    description="Este tutorial describe cómo cifrar y descifrar un blob con cifrado de cliente para Microsoft Azure almacenamiento con depósito de clave de Azure."
    services="storage"
    documentationCenter=""
    authors="robinsh"
    manager="carmonm"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="required"
    ms.date="10/18/2016"
    ms.author="lakasa;robinsh"/>

# <a name="tutorial-encrypt-and-decrypt-blobs-in-microsoft-azure-storage-using-azure-key-vault"></a>Tutorial: Cifrar y descifrar BLOB en Microsoft Azure almacenamiento mediante depósito de clave de Azure

## <a name="introduction"></a>Introducción

Este tutorial explica cómo hacer que el uso de cifrado de almacenamiento de información del cliente con depósito de clave de Azure. Le guiará por cómo cifrar y descifrar un blob en una aplicación de consola con estas tecnologías.

**Tiempo estimado para completar:** 20 minutos

Para obtener información general acerca de la cámara de clave de Azure, consulte [¿Qué es depósito de clave de Azure?](../key-vault/key-vault-whatis.md).

Para obtener información general sobre el cifrado de cliente para el almacenamiento de Azure, vea [depósito de clave de Azure de Microsoft Azure almacenamiento y cifrado de cliente](storage-client-side-encryption.md).


## <a name="prerequisites"></a>Requisitos previos

Para completar este tutorial, debe tener el siguiente:

- Una cuenta de almacenamiento de Azure
- Visual Studio 2013 o posterior
- Azure PowerShell


## <a name="overview-of-client-side-encryption"></a>Información general sobre el cifrado de cliente

Para obtener información general de cifrado de cliente para el almacenamiento de Azure, vea [cifrado de cliente y depósito de clave de Azure de Microsoft Azure almacenamiento](storage-client-side-encryption.md)

Aquí es una breve descripción de cómo funciona el cifrado de cliente:

1. El cliente de almacenamiento de Azure SDK genera una clave de cifrado de contenido (CEK), que es una clave simétrica de un uso.
2. Datos del cliente se cifran con este CEK.
3. La CEK, a continuación, se ajusta (cifrada) con la clave de cifrado de claves (KEK). La KEK se identifica mediante un identificador de clave y puede ser un par de claves asimétrico o una clave simétrica y pueden administrar de forma local o almacenarse en depósito de clave de Azure. El propio cliente almacenamiento nunca tiene acceso a la KEK. Solo se invoca el algoritmo de ajuste de clave proporcionada por la clave de cámara. Los clientes pueden elegir usar proveedores personalizados para clave ajuste o desprotección si así lo desean.
4. A continuación, se cargan los datos cifrados con el servicio de almacenamiento de Azure.


## <a name="set-up-your-azure-key-vault"></a>Configurar su cámara de clave de Azure
Para continuar con este tutorial, debe realizar los pasos siguientes, que se describen en el tutorial [Introducción a Azure depósito de clave](../key-vault/key-vault-get-started.md):

- Crear un depósito clave.
- Agregar una clave o secreto al depósito clave.
- Registrar una aplicación con Azure Active Directory.
- Autorizar la aplicación para utilizar la clave o el secreto.

Tome nota del ClientID y ClientSecret generados cuando se registra una aplicación con Azure Active Directory.

Crear dos claves en depósito de clave. Suponemos para el resto del tutorial que se usan los siguientes nombres: ContosoKeyVault y TestRSAKey1.


## <a name="create-a-console-application-with-packages-and-appsettings"></a>Crear una aplicación de consola con paquetes y AppSettings

En Visual Studio, cree una nueva aplicación de consola.

Agregar paquetes de nuget necesarios en la consola del Administrador de paquetes.

    Install-Package WindowsAzure.Storage

    // This is the latest stable release for ADAL.
    Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -Version 2.16.204221202

    Install-Package Microsoft.Azure.KeyVault
    Install-Package Microsoft.Azure.KeyVault.Extensions


Agregar AppSettings a App.Config.

    <appSettings>
        <add key="accountName" value="myaccount"/>
        <add key="accountKey" value="theaccountkey"/>
        <add key="clientId" value="theclientid"/>
        <add key="clientSecret" value="theclientsecret"/>
        <add key="container" value="stuff"/>
    </appSettings>

Agregue las siguientes `using` instrucciones y asegúrese de agregar una referencia a System.Configuration al proyecto.

    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    using System.Configuration;
    using Microsoft.WindowsAzure.Storage.Auth;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Blob;
    using Microsoft.Azure.KeyVault;
    using System.Threading;     
    using System.IO;


## <a name="add-a-method-to-get-a-token-to-your-console-application"></a>Agregar un método para obtener un símbolo de la aplicación de consola

Clases de depósito de clave que se deben autenticar para tener acceso a su cámara clave utilizan el método siguiente.

    private async static Task<string> GetToken(string authority, string resource, string scope)
    {
        var authContext = new AuthenticationContext(authority);
        ClientCredential clientCred = new ClientCredential(
            ConfigurationManager.AppSettings["clientId"],
            ConfigurationManager.AppSettings["clientSecret"]);
        AuthenticationResult result = await authContext.AcquireTokenAsync(resource, clientCred);

        if (result == null)
            throw new InvalidOperationException("Failed to obtain the JWT token");

        return result.AccessToken;
    }

## <a name="access-storage-and-key-vault-in-your-program"></a>Almacenamiento de acceso y depósito de clave en el programa

En la función Main, agregue el código siguiente.

    // This is standard code to interact with Blob storage.
    StorageCredentials creds = new StorageCredentials(
        ConfigurationManager.AppSettings["accountName"],
        ConfigurationManager.AppSettings["accountKey"]);
    CloudStorageAccount account = new CloudStorageAccount(creds, useHttps: true);
    CloudBlobClient client = account.CreateCloudBlobClient();
    CloudBlobContainer contain = client.GetContainerReference(ConfigurationManager.AppSettings["container"]);
    contain.CreateIfNotExists();

    // The Resolver object is used to interact with Key Vault for Azure Storage.
    // This is where the GetToken method from above is used.
    KeyVaultKeyResolver cloudResolver = new KeyVaultKeyResolver(GetToken);


> [AZURE.NOTE] Modelos de objetos de depósito clave
>
>Es importante comprender que hay en realidad dos clave depósito modelos de objetos que debería considerar: uno se basa en la API de REST (espacio de nombres KeyVault) y la otra es una extensión para el cifrado de cliente.

> El cliente de cámara clave interactúa con la API de REST y comprende JSON Web teclas y la información confidencial de los dos tipos de elementos que se encuentran en depósito de clave.

> Las extensiones de depósito clave son clases que parecen creadas específicamente para el cifrado de cliente en el almacenamiento de Azure. Contienen una interfaz de claves (IKey) y clases basadas en el concepto de una resolución de clave. Existen dos implementaciones de IKey que debe saber: RSAKey y SymmetricKey. Ahora que se producen para que coincida con las cosas que se encuentran en un depósito de clave, pero en este momento son clases independientes (para que la clave y secreto recuperados por el cliente de cámara clave implementar IKey).


## <a name="encrypt-blob-and-upload"></a>Cifrar blob y cargar
Agregue el código siguiente para cifrar un blob y cárguelo en su cuenta de almacenamiento de Azure. El método **ResolveKeyAsync** que se usa devuelve un IKey.


    // Retrieve the key that you created previously.
    // The IKey that is returned here is an RsaKey.
    // Remember that we used the names contosokeyvault and testrsakey1.
    var rsa = cloudResolver.ResolveKeyAsync("https://contosokeyvault.vault.azure.net/keys/TestRSAKey1", CancellationToken.None).GetAwaiter().GetResult();


    // Now you simply use the RSA key to encrypt by setting it in the BlobEncryptionPolicy.
    BlobEncryptionPolicy policy = new BlobEncryptionPolicy(rsa, null);
    BlobRequestOptions options = new BlobRequestOptions() { EncryptionPolicy = policy };

    // Reference a block blob.
    CloudBlockBlob blob = contain.GetBlockBlobReference("MyFile.txt");

    // Upload using the UploadFromStream method.
    using (var stream = System.IO.File.OpenRead(@"C:\data\MyFile.txt"))
        blob.UploadFromStream(stream, stream.Length, null, options, null);


A continuación se muestra una captura de pantalla desde el [Portal de Azure clásico](https://manage.windowsazure.com) para un blob que se han cifrado mediante el cifrado de cliente con una clave almacenada en depósito de clave. La propiedad **ID** es el URI para la clave en depósito de clave que actúa como la KEK. La propiedad **EncryptedKey** contiene la versión cifrada de la CEK.

![Captura de pantalla que muestra los metadatos de blobs de Windows que incluyen los metadatos de cifrado][1]

> [AZURE.NOTE] Si mira el constructor BlobEncryptionPolicy, verá que puede aceptar una clave o una resolución. Tenga en cuenta que ahora no puede utilizar a una resolución de cifrado porque tiene actualmente no admite una clave de forma predeterminada.



## <a name="decrypt-blob-and-download"></a>Descifrar blob y descargar
Descifrado es realmente cuando utilizando las clases de resolución sentido. El identificador de la clave utilizada para el cifrado está asociado con el objeto blob en sus metadatos, lo que no hay ninguna razón para recuperar la clave y recuerde que la asociación entre claves y blob. Debe asegurarse de que la tecla permanece en depósito de clave.   

La clave privada de una clave RSA permanece en depósito de clave, por lo que para que descifrado que se produzca, la clave de cifrado de los metadatos de blobs de Windows que contiene el CEK se envía a depósito de clave para el descifrado.

Agregue lo siguiente para descifrar el blob que acaba de cargar.

    // In this case, we will not pass a key and only pass the resolver because
    // this policy will only be used for downloading / decrypting.
    BlobEncryptionPolicy policy = new BlobEncryptionPolicy(null, cloudResolver);
    BlobRequestOptions options = new BlobRequestOptions() { EncryptionPolicy = policy };

    using (var np = File.Open(@"C:\data\MyFileDecrypted.txt", FileMode.Create))
        blob.DownloadToStream(np, null, options, null);


> [AZURE.NOTE] Hay un par de otros tipos de resoluciones para facilitar la administración de claves, incluidos: AggregateKeyResolver y CachingKeyResolver.


## <a name="use-key-vault-secrets"></a>Use la información confidencial de la cámara de clave
La manera de usar un secreto con cifrado de cliente es a través de la clase SymmetricKey porque un secreto es, esencialmente, una clave simétrica. Sin embargo, como se mencionó anteriormente, un secreto en depósito de clave no se asigna exactamente a un SymmetricKey. Hay algunos aspectos que debe conocer:


- La clave en un SymmetricKey tiene que ser una longitud fija: 128, 192, 256, 384 o 512 bits.
- La clave en un SymmetricKey debería base 64 codificado.
- Un secreto depósito de clave que se utilizará como un SymmetricKey debe tener un tipo de contenido de "application/octet-stream" en depósito de clave.

Aquí tenemos un ejemplo de PowerShell de creación de un secreto en depósito de clave que se pueden utilizar como un SymmetricKey.
Nota: El valor codificado, $key, es solo con fines de demostración. En su propio código desea generar esta clave.

    // Here we are making a 128-bit key so we have 16 characters.
    //  The characters are in the ASCII range of UTF8 so they are
    //  each 1 byte. 16 x 8 = 128.
    $key = "qwertyuiopasdfgh"
    $b = [System.Text.Encoding]::UTF8.GetBytes($key)
    $enc = [System.Convert]::ToBase64String($b)
    $secretvalue = ConvertTo-SecureString $enc -AsPlainText -Force

    // Substitute the VaultName and Name in this command.
    $secret = Set-AzureKeyVaultSecret -VaultName 'ContoseKeyVault' -Name 'TestSecret2' -SecretValue $secretvalue -ContentType "application/octet-stream"

En la aplicación de consola, puede usar la misma llamada como antes para recuperar este secreto como un SymmetricKey.

    SymmetricKey sec = (SymmetricKey) cloudResolver.ResolveKeyAsync(
        "https://contosokeyvault.vault.azure.net/secrets/TestSecret2/",
        CancellationToken.None).GetAwaiter().GetResult();

Eso es todo. Disfrute.

## <a name="next-steps"></a>Pasos siguientes

Para obtener más información sobre cómo usar Microsoft Azure almacenamiento con C#, vea [Biblioteca de cliente de Microsoft Azure almacenamiento para .NET](https://msdn.microsoft.com/library/azure/dn261237.aspx).

Para obtener más información acerca de la API de REST de blobs de Windows, vea [API de REST de servicio de blobs de Windows](https://msdn.microsoft.com/library/azure/dd135733.aspx).

Para la información más reciente sobre Microsoft Azure almacenamiento, visite el [Blog del equipo de Microsoft Azure almacenamiento](http://blogs.msdn.com/b/windowsazurestorage/).


<!--Image references-->
[1]: ./media/storage-encrypt-decrypt-blobs-key-vault/blobmetadata.png
