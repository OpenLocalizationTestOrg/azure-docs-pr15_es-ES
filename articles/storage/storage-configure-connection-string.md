<properties 
    pageTitle="Configurar una cadena de conexión con el almacenamiento de Azure | Microsoft Azure"
    description="Configurar una cadena de conexión a una cuenta de almacenamiento de Azure. Una cadena de conexión incluye la información necesaria para autenticar el acceso a una cuenta de almacenamiento de la aplicación en tiempo de ejecución."
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

# <a name="configure-azure-storage-connection-strings"></a>Configurar cadenas de conexión de almacenamiento de Azure

## <a name="overview"></a>Información general

Una cadena de conexión incluye la información de autenticación necesaria para obtener acceso a datos en una cuenta de almacenamiento de Azure desde la aplicación en tiempo de ejecución. Puede configurar una cadena de conexión:

- Conectar con el emulador de almacenamiento de Azure.
- Obtener acceso a una cuenta de almacenamiento en Azure.
- Obtener acceso a los recursos especificados en Azure a través de una firma de acceso compartido (SA).

[AZURE.INCLUDE [storage-account-key-note-include](../../includes/storage-account-key-note-include.md)]

## <a name="storing-your-connection-string"></a>Almacenar la cadena de conexión

La aplicación necesitará tener acceso a la cadena de conexión en tiempo de ejecución para autenticar las solicitudes de almacenamiento de Azure. Tiene varias opciones diferentes para almacenar la cadena de conexión:

- Para una aplicación que se ejecuta en el escritorio o en un dispositivo, puede almacenar la cadena de conexión en un `app.config `archivo o una `web.config` archivo. Agregue la cadena de conexión a la sección **AppSettings** .
- Para una aplicación que se ejecuta en un servicio de nube de Azure, puede almacenar la cadena de conexión en el [archivo de esquema (.cscfg) de configuración de servicio de Azure](https://msdn.microsoft.com/library/ee758710.aspx). Agregue la cadena de conexión a la sección **ConfigurationSettings** del archivo de configuración de servicio.
- También puede usar la cadena de conexión directamente en el código. Sin embargo, para la mayoría de los escenarios, recomendamos que almacene la cadena de configuración en un archivo de configuración.

Almacenar la cadena de conexión en un archivo de configuración facilita la actualización de la cadena de conexión para cambiar entre el emulador de almacenamiento y una cuenta de almacenamiento de Azure en la nube. Solo necesita modificar la cadena de conexión para que apunten a su entorno de destino.

Puede usar la clase de [Administrador de configuración de Microsoft Azure](https://www.nuget.org/packages/Microsoft.WindowsAzure.ConfigurationManager/) para tener acceso a la cadena de conexión en tiempo de ejecución independientemente de dónde se ejecuta la aplicación.

## <a name="create-a-connection-string-to-the-storage-emulator"></a>Crear una cadena de conexión al emulador de almacenamiento

[AZURE.INCLUDE [storage-emulator-connection-string-include](../../includes/storage-emulator-connection-string-include.md)]

Consulte [usar el emulador de almacenamiento de Azure para desarrollo y pruebas](storage-use-emulator.md) para obtener más información sobre el emulador de almacenamiento.

## <a name="create-a-connection-string-to-an-azure-storage-account"></a>Crear una cadena de conexión a una cuenta de almacenamiento de Azure

Para crear una cadena de conexión a su cuenta de almacenamiento de Azure, use el siguiente formato de la cadena de conexión. Indicar si desea conectarse a la cuenta de almacenamiento a través de HTTPS (recomendado) o HTTP, reemplace `myAccountName` con el nombre de su cuenta de almacenamiento y reemplazar `myAccountKey` con la clave de acceso de cuenta:

    DefaultEndpointsProtocol=[http|https];AccountName=myAccountName;AccountKey=myAccountKey

Por ejemplo, la cadena de conexión tendrá un aspecto similar a la cadena de conexión de ejemplo siguientes:

    DefaultEndpointsProtocol=https;AccountName=storagesample;AccountKey=<account-key>

> [AZURE.NOTE] Almacenamiento de Azure es compatible con HTTP y HTTPS en una cadena de conexión; Sin embargo, mediante HTTPS se recomienda.

## <a name="create-a-connection-string-using-a-shared-access-signature"></a>Crear una cadena de conexión con una firma de acceso compartido

[AZURE.INCLUDE [storage-use-sas-in-connection-string-include](../../includes/storage-use-sas-in-connection-string-include.md)]

## <a name="creating-a-connection-string-to-an-explicit-storage-endpoint"></a>Creación de una cadena de conexión a un extremo de almacenamiento explícitas

Puede especificar explícitamente los extremos del servicio en la cadena de conexión en lugar de usar los extremos de forma predeterminada. Para crear una cadena de conexión que especifica un extremo explícito, especifique el extremo de servicio completo para cada servicio, incluida la especificación de protocolo (HTTPS (recomendado) o HTTP), en el siguiente formato:

    DefaultEndpointsProtocol=[http|https];
    BlobEndpoint=myBlobEndpoint;
    QueueEndpoint=myQueueEndpoint;
    TableEndpoint=myTableEndpoint;
    FileEndpoint=myFileEndpoint;
    AccountName=myAccountName;
    AccountKey=myAccountKey

Un escenario que se desea especificar un extremo explícito es si ha asignado el extremo de almacenamiento de blobs de Windows a un dominio personalizado. En ese caso, puede especificar el extremo personalizado para el almacenamiento de blobs de Windows en la cadena de conexión y, opcionalmente, especifique los extremos de predeterminado para el otro servicio si utiliza la aplicación.

Estos son algunos ejemplos de cadenas de conexión válida que especifica un extremo explícito para el servicio de blobs de Windows:

    # Blob endpoint only
    DefaultEndpointsProtocol=https;
    BlobEndpoint=www.mydomain.com;
    AccountName=storagesample;
    AccountKey=account-key

    # All service endpoints
    DefaultEndpointsProtocol=https;
    BlobEndpoint=www.mydomain.com;
    FileEndpoint=myaccount.file.core.windows.net;
    QueueEndpoint=myaccount.queue.core.windows.net;
    TableEndpoint=myaccount;
    AccountName=storagesample;
    AccountKey=account-key

Emplea el valor de punto final que aparece en la cadena de conexión para crear la solicitud URI del servicio de blobs de Windows y, a continuación, determina la forma de cualquier URI que se devuelven a su código.

Tenga en cuenta que si opta por omitir un extremo de servicio de la cadena de conexión, a continuación, no podrá usar esa cadena de conexión para tener acceso a datos en ese servicio desde el código.

### <a name="creating-a-connection-string-with-an-endpoint-suffix"></a>Creación de una cadena de conexión con un sufijo extremo

Para crear una cadena de conexión de servicio de almacenamiento en regiones o instancias con sufijos extremo diferente, como China Azure o gobernanza de Azure, use el siguiente formato de la cadena de conexión. Indicar si desea conectarse a la cuenta de almacenamiento a través de HTTP o HTTPS, reemplazar `myAccountName` con el nombre de su cuenta de almacenamiento, reemplazar `myAccountKey` con la tecla de acceso de la cuenta y reemplazar `mySuffix` con el sufijo URI:


    DefaultEndpointsProtocol=[http|https];
    AccountName=myAccountName;
    AccountKey=myAccountKey;
    EndpointSuffix=mySuffix;


Por ejemplo, la cadena de conexión debe ser similar a la siguiente cadena de conexión:

    DefaultEndpointsProtocol=https;
    AccountName=storagesample;
    AccountKey=<account-key>;
    EndpointSuffix=core.chinacloudapi.cn;

## <a name="parsing-a-connection-string"></a>Análisis de una cadena de conexión

[AZURE.INCLUDE [storage-cloud-configuration-manager-include](../../includes/storage-cloud-configuration-manager-include.md)]


## <a name="next-steps"></a>Pasos siguientes

- [Almacenamiento de Azure usarlo para desarrollo y pruebas](storage-use-emulator.md)
- [Exploradores de almacenamiento de Azure](storage-explorers.md)
- [Usar firmas de acceso compartido (SA)](storage-dotnet-shared-access-signature-part-1.md)
