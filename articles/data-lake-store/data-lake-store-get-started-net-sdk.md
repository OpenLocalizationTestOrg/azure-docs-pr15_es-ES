<properties
   pageTitle="Utilizar datos lago almacén .NET SDK para desarrollar aplicaciones | Microsoft Azure"
   description="Utilizar datos lago almacén .NET SDK de Azure para desarrollar aplicaciones"
   services="data-lake-store"
   documentationCenter=""
   authors="nitinme"
   manager="jhubbard"
   editor="cgronlun"/>

<tags
   ms.service="data-lake-store"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="09/27/2016"
   ms.author="nitinme"/>

# <a name="get-started-with-azure-data-lake-store-using-net-sdk"></a>Introducción a Azure datos lago tienda con .NET SDK

> [AZURE.SELECTOR]
- [Portal](data-lake-store-get-started-portal.md)
- [PowerShell](data-lake-store-get-started-powershell.md)
- [.NET SDK](data-lake-store-get-started-net-sdk.md)
- [Java SDK](data-lake-store-get-started-java-sdk.md)
- [API DE REST](data-lake-store-get-started-rest-api.md)
- [CLI de Azure](data-lake-store-get-started-cli.md)
- [Node.js](data-lake-store-manage-use-nodejs.md)

Obtenga información sobre cómo usar el [Almacén de .NET SDK de Azure datos lago](https://msdn.microsoft.com/library/mt581387.aspx) para realizar operaciones básicas, como crear carpetas, cargar y descargar archivos de datos, etcetera. Para obtener más información acerca de lago de datos, vea [Lago almacén de datos de Azure](data-lake-store-overview.md).

## <a name="prerequisites"></a>Requisitos previos

* **2013 de visual Studio o de 2015**. Las siguientes instrucciones use Visual Studio de 2015.

* **Suscripción de un Azure**. Vea la [versión de prueba gratuita de Azure obtener](https://azure.microsoft.com/pricing/free-trial/).

* **Cuenta de la tienda de lago de datos de azure**. Para obtener instrucciones sobre cómo crear una cuenta, consulte [Introducción a la tienda de lago de datos de Azure](data-lake-store-get-started-portal.md)

* **Crear una aplicación de Azure Active Directory**. Utilizar la aplicación de Azure AD para autenticar la aplicación de almacenamiento de lago de datos con Azure AD. Existen diferentes métodos para autenticar con Azure AD, que son la **autenticación de usuario final** o **servicio de servicio**. Para obtener instrucciones y más información acerca de cómo autenticar, vea [autenticar con el almacén de datos de lago con Azure Active Directory](data-lake-store-authenticate-using-active-directory.md).

## <a name="create-a-net-application"></a>Crear una aplicación.

1. Abra Visual Studio y cree una aplicación de consola.

2. En el menú **archivo** , haga clic en **nuevo**y, a continuación, haga clic en **proyecto**.

3. **Nuevo proyecto**, escriba o seleccione los valores siguientes:

  	| (Propiedad) | Valor                       |
  	|----------|-----------------------------|
  	| Categoría | Plantillas de Visual C# / Windows |
  	| Plantilla | Aplicación de consola         |
  	| Nombre     | CreateADLApplication        |

4. Haga clic en **Aceptar** para crear el proyecto.

5. Agregue los paquetes de Nuget a su proyecto.

    1. Haga clic en el nombre del proyecto en el Explorador de soluciones y haga clic en **Administrar paquetes de NuGet**.
    2. En la pestaña **Administrador de paquetes de Nuget** , asegúrese de que el **origen del paquete** se establece en **nuget.org** y está seleccionada la casilla de verificación **incluir versión preliminar** .
    3. Buscar e instalar los paquetes de NuGet siguientes:

        * `Microsoft.Azure.Management.DataLake.Store`-Este tutorial usa v0.12.5 de vista previa.
        * `Microsoft.Azure.Management.DataLake.StoreUploader`-Este tutorial usa v0.10.6 de vista previa.
        * `Microsoft.Rest.ClientRuntime.Azure.Authentication`-Este tutorial usa v2.2.8 de vista previa.

        ![Agregar un origen de Nuget] (./media/data-lake-store-get-started-net-sdk/ADL.Install.Nuget.Package.png "Crear una nueva cuenta de lago de datos de Azure")

    4. Cierre el **Administrador de paquetes de Nuget**.

6. Abra **Program.cs**, elimine el código existente y, a continuación, se incluyen las siguientes instrucciones para agregar las referencias a los espacios de nombres.

        using System;
        using System.Threading;
        
        using Microsoft.Rest.Azure.Authentication;
        using Microsoft.Azure.Management.DataLake.Store;
        using Microsoft.Azure.Management.DataLake.StoreUploader;

7. Declarar las variables tal como se muestra a continuación y proporcionar los valores de nombre de la tienda de lago de datos y el nombre del grupo de recursos que ya existen. Además, asegúrese de que la ruta de acceso local y nombre de archivo que proporcione aquí debe existir en el equipo. Agregar fragmento de código siguiente después de las declaraciones de espacio de nombres.

        namespace SdkSample
        {
            class Program
            {
                private static DataLakeStoreAccountManagementClient _adlsClient;
                private static DataLakeStoreFileSystemManagementClient _adlsFileSystemClient;
                
                private static string _adlsAccountName;
                private static string _resourceGroupName;
                private static string _location;
                private static string _subId;

                
                private static void Main(string[] args)
                {
                    _adlsAccountName = "<DATA-LAKE-STORE-NAME>"; // TODO: Replace this value with the name of your existing Data Lake Store account.
                    _resourceGroupName = "<RESOURCE-GROUP-NAME>"; // TODO: Replace this value with the name of the resource group containing your Data Lake Store account.
                    _location = "East US 2";
                    _subId = "<SUBSCRIPTION-ID>";
                    
                    string localFolderPath = @"C:\local_path\"; // TODO: Make sure this exists and can be overwritten.
                    string localFilePath = localFolderPath + "file.txt"; // TODO: Make sure this exists and can be overwritten.
                    string remoteFolderPath = "/data_lake_path/";
                    string remoteFilePath = remoteFolderPath + "file.txt";
                }
            }
        }

En las secciones restantes de este artículo, puede ver cómo utilizar los métodos de .NET disponibles para realizar operaciones como la autenticación, la carga de archivos, etcetera.

## <a name="authentication"></a>Autenticación

### <a name="if-you-are-using-end-user-authentication-recommended-for-this-tutorial"></a>Si está utilizando la autenticación de usuario final (recomendada para este tutorial)

Use esta opción con una aplicación "Native Client" Azure existente de AD; uno se proporciona a continuación. Para ayudarle a completar este tutorial más rápido, se recomienda que usar este método.

    // User login via interactive popup
    // Use the client ID of an existing AAD "Native Client" application.
    SynchronizationContext.SetSynchronizationContext(new SynchronizationContext());
    var domain = "common"; // Replace this string with the user's Azure Active Directory tenant ID or domain name, if needed.
    var nativeClientApp_clientId = "1950a258-227b-4e31-a9cf-717495945fc2";
    var activeDirectoryClientSettings = ActiveDirectoryClientSettings.UsePromptOnly(nativeClientApp_clientId, new Uri("urn:ietf:wg:oauth:2.0:oob"));
    var creds = UserTokenProvider.LoginWithPromptAsync(domain, activeDirectoryClientSettings).Result;

Par de cosas que debe saber sobre este fragmento de código anterior.

* Para ayudarle a completar el tutorial con mayor rapidez, este fragmento utiliza un anuncio Azure ID de cliente y el dominio que está disponible de forma predeterminada para todas las suscripciones de Azure. Por lo tanto, puede **usar este fragmento de código como-está en la aplicación**.
* Sin embargo, si desea usar su propio identificador de cliente de dominio y aplicación de Azure AD, debe crear una aplicación nativa de Azure AD y, a continuación, use el dominio de Azure AD, ID de cliente y redirigir URI de la aplicación que creó. Para obtener instrucciones, vea [crear una aplicación de directorio activa](../resource-group-create-service-principal-portal.md#create-an-active-directory-application) .

>[AZURE.NOTE] Las instrucciones de los vínculos anteriores son para una aplicación web de Azure AD. Sin embargo, los pasos son exactamente iguales incluso si se elige crear una aplicación de cliente nativo en su lugar. 

### <a name="if-you-are-using-service-to-service-authentication-with-client-secret"></a>Si está utilizando la autenticación de servicio para el servicio con secreto de cliente 

El fragmento de código siguiente puede usarse para autenticar la aplicación de forma no interactiva, con el cliente de secreto / clave para una identidad de aplicación o servicio. Use esta opción con una [aplicación de Azure AD "aplicación Web"](../resource-group-create-service-principal-portal.md)de existente.

    // Service principal / appplication authentication with client secret / key
    // Use the client ID and certificate of an existing AAD "Web App" application.
    SynchronizationContext.SetSynchronizationContext(new SynchronizationContext());
    var domain = "<AAD-directory-domain>";
    var webApp_clientId = "<AAD-application-clientid>";
    var clientSecret = "<AAD-application-client-secret>";
    var clientCredential = new ClientCredential(webApp_clientId, clientSecret);
    var creds = ApplicationTokenProvider.LoginSilentAsync(domain, clientCredential).Result;

### <a name="if-you-are-using-service-to-service-authentication-with-certificate"></a>Si está utilizando la autenticación de servicio para el servicio con certificado

Como tercera opción, el fragmento de código siguiente puede usarse para autenticar la aplicación de forma no interactiva, utilizando el certificado para una identidad de aplicación o servicio. Use esta opción con una [aplicación de Azure AD "aplicación Web"](../resource-group-create-service-principal-portal.md)de existente.

    // Service principal / application authentication with certificate
    // Use the client ID and certificate of an existing AAD "Web App" application.
    SynchronizationContext.SetSynchronizationContext(new SynchronizationContext());
    var domain = "<AAD-directory-domain>";
    var webApp_clientId = "<AAD-application-clientid>";
    System.Security.Cryptography.X509Certificates.X509Certificate2 clientCert = <AAD-application-client-certificate>
    var clientAssertionCertificate = new ClientAssertionCertificate(webApp_clientId, clientCert);
    var creds = ApplicationTokenProvider.LoginSilentWithCertificateAsync(domain, clientAssertionCertificate).Result;

## <a name="create-client-objects"></a>Crear objetos de cliente

El fragmento de código siguiente crea al cliente de cuenta y el sistema de archivos de datos de la tienda lago objetos, que se usan para emitir solicitudes del servicio.

    // Create client objects and set the subscription ID
    _adlsClient = new DataLakeStoreAccountManagementClient(creds);
    _adlsFileSystemClient = new DataLakeStoreFileSystemManagementClient(creds);

    _adlsClient.SubscriptionId = _subId;

## <a name="list-all-data-lake-store-accounts-within-a-subscription"></a>Lista de todas las cuentas de almacén de datos lago dentro de una suscripción

El fragmento de código siguiente enumera todas las cuentas de almacén de datos lago dentro de una suscripción de Azure determinada.

    // List all ADLS accounts within the subscription
    public static List<DataLakeStoreAccount> ListAdlStoreAccounts()
    {
        var response = _adlsClient.Account.List();
        var accounts = new List<DataLakeStoreAccount>(response);
        
        while (response.NextPageLink != null)
        {
            response = _adlsClient.Account.ListNext(response.NextPageLink);
            accounts.AddRange(response);
        }
        
        return accounts;
    }

## <a name="create-a-directory"></a>Crear un directorio

El fragmento de código siguiente se muestra un `CreateDirectory` método que puede usar para crear un directorio dentro de una cuenta de almacén de datos lago.

    // Create a directory
    public static void CreateDirectory(string path)
    {
        _adlsFileSystemClient.FileSystem.Mkdirs(_adlsAccountName, path);
    }

## <a name="upload-a-file"></a>Cargar un archivo

El fragmento de código siguiente se muestra un `UploadFile` método que puede usar para cargar archivos a una cuenta de almacén de datos lago.

    // Upload a file
    public static void UploadFile(string srcFilePath, string destFilePath, bool force = true)
    {
        var parameters = new UploadParameters(srcFilePath, destFilePath, _adlsAccountName, isOverwrite: force);
        var frontend = new DataLakeStoreFrontEndAdapter(_adlsAccountName, _adlsFileSystemClient);
        var uploader = new DataLakeStoreUploader(parameters, frontend);
        uploader.Execute();
    }

`DataLakeStoreUploader`admite recursiva carga y descarga entre una ruta de acceso del archivo local y una ruta de acceso del archivo de almacén de datos lago.    

## <a name="get-file-or-directory-info"></a>Obtener información de archivo o directorio

El fragmento de código siguiente se muestra un `GetItemInfo` método que puede usar para recuperar información acerca de un archivo o directorio disponible en el almacén de datos de lago. 

    // Get file or directory info
    public static FileStatusProperties GetItemInfo(string path)
    {
        return _adlsFileSystemClient.FileSystem.GetFileStatus(_adlsAccountName, path).FileStatus;
    }

## <a name="list-file-or-directories"></a>Archivo de la lista o directorios

El fragmento de código siguiente se muestra un `ListItem` método que puede utilizar para enumerar los archivos y directorios de una cuenta de almacén de datos lago.

    // List files and directories
    public static List<FileStatusProperties> ListItems(string directoryPath)
    {
        return _adlsFileSystemClient.FileSystem.ListFileStatus(_adlsAccountName, directoryPath).FileStatuses.FileStatus.ToList();
    }

## <a name="concatenate-files"></a>Concatenar archivos

El fragmento de código siguiente se muestra un `ConcatenateFiles` método que se utiliza para concatenar archivos. 

    // Concatenate files
    public static void ConcatenateFiles(string[] srcFilePaths, string destFilePath)
    {
        _adlsFileSystemClient.FileSystem.Concat(_adlsAccountName, destFilePath, srcFilePaths);
    }

## <a name="append-to-a-file"></a>Anexar a un archivo

El fragmento de código siguiente se muestra un `AppendToFile` método que utilice anexar datos a un archivo que ya está almacenado en una cuenta de almacén de datos lago.

    // Append to file
    public static void AppendToFile(string path, string content)
    {
        var stream = new MemoryStream(Encoding.UTF8.GetBytes(content));
        
        _adlsFileSystemClient.FileSystem.Append(_adlsAccountName, path, stream);
    }

## <a name="download-a-file"></a>Descargar un archivo

El fragmento de código siguiente se muestra un `DownloadFile` método que utiliza para descargar un archivo desde una cuenta de almacén de datos lago.

    // Download file
    public static void DownloadFile(string srcPath, string destPath)
    {
        var stream = _adlsFileSystemClient.FileSystem.Open(_adlsAccountName, srcPath);
        var fileStream = new FileStream(destPath, FileMode.Create);
        
        stream.CopyTo(fileStream);
        fileStream.Close();
        stream.Close();
    }

## <a name="next-steps"></a>Pasos siguientes

- [Proteger los datos en el almacén de datos de lago](data-lake-store-secure-data.md)
- [Usar el análisis de datos de Azure lago con el almacén de datos de lago](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
- [Usar Azure HDInsight con el almacén de datos de lago](data-lake-store-hdinsight-hadoop-use-portal.md)
- [Referencia de almacén de datos lago .NET SDK](https://msdn.microsoft.com/library/mt581387.aspx)
- [Referencia del resto de almacén de datos lago](https://msdn.microsoft.com/library/mt693424.aspx)
