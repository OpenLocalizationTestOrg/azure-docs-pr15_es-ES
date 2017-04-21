<properties
   pageTitle="Empezar a trabajar con el almacén de lago de datos mediante la interfaz de línea de comandos de varias plataformas | Microsoft Azure"
   description="Usar línea de comandos de Azure entre plataformas para crear una cuenta de almacén de datos lago y realizar operaciones básicas"
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

# <a name="get-started-with-azure-data-lake-store-using-azure-command-line"></a>Introducción a Azure datos lago tienda con línea de comandos de Azure

> [AZURE.SELECTOR]
- [Portal](data-lake-store-get-started-portal.md)
- [PowerShell](data-lake-store-get-started-powershell.md)
- [.NET SDK](data-lake-store-get-started-net-sdk.md)
- [Java SDK](data-lake-store-get-started-java-sdk.md)
- [API DE REST](data-lake-store-get-started-rest-api.md)
- [CLI de Azure](data-lake-store-get-started-cli.md)
- [Node.js](data-lake-store-manage-use-nodejs.md)

Obtenga información sobre cómo usar la interfaz de línea de comandos de Azure para crear una cuenta de la tienda de lago de datos de Azure y realizar operaciones básicas, como crear carpetas, cargar y descargar archivos de datos, eliminar la cuenta, etcetera. Para obtener más información acerca de la tienda de lago de datos, vea [Información general de la tienda de lago datos](data-lake-store-overview.md).

CLI Azure se implementa en Node.js. Puede usar en cualquier plataforma que admite Node.js, incluyendo Windows, Mac y Linux. CLI Azure es Abrir origen. El código fuente administrado en GitHub en <a href= "https://github.com/azure/azure-xplat-cli">https://github.com/azure/azure-xplat-cli</a>. En este artículo trata sobre usando solo CLI Azure con lago almacén de datos. Instrucciones generales sobre cómo usar CLI de Azure, consulte [cómo usar CLI Azure] [azure-command-line-tools].


##<a name="prerequisites"></a>Requisitos previos

Antes de comenzar este artículo, debe tener el siguiente:

- **Suscripción de un Azure**. Vea la [versión de prueba gratuita de Azure obtener](https://azure.microsoft.com/pricing/free-trial/).

- **Azure CLI** - vea [instalar y configurar la CLI Azure](../xplat-cli-install.md) para obtener información de instalación y configuración. Asegúrese de que reinicie el equipo después de instalar CLI.

## <a name="authentication"></a>Autenticación

En este artículo se usa un método de autenticación más sencillo con almacén de lago de datos que se inicia sesión como un usuario para el usuario final. El nivel de acceso a datos lago Store cuenta y sistema de archivos, a continuación, se rige por el nivel de acceso del usuario que ha iniciado sesión. Sin embargo, hay otros enfoques también para autenticar con datos lago almacén, que son la **autenticación de usuario final** o **servicio de servicio**. Para obtener instrucciones y más información acerca de cómo autenticar, vea [autenticar con el almacén de datos de lago con Azure Active Directory](data-lake-store-authenticate-using-active-directory.md).

##<a name="login-to-your-azure-subscription"></a>Inicie sesión en su suscripción de Azure

1. Siga los pasos documentados en [Conectar con una suscripción de Azure desde la interfaz de línea de comandos de Azure (Azure CLI)](../xplat-cli-connect.md) y conectarse a su suscripción utilizando el `azure login` método.

2. Lista de las suscripciones a las que están asociadas a su cuenta mediante la `azure account list` comando.

        info:    Executing command account list
        data:    Name              Id                                    Current
        data:    ----------------  ------------------------------------  -------
        data:    Azure-sub-1       ####################################  true
        data:    Azure-sub-2       ####################################  false

    Desde el resultado anterior, **Azure-sub-1** está habilitada y la otra suscripción es **Azure-sub-2**. 

3. Seleccione la suscripción que desea trabajar en. Si desea trabajar en la suscripción de Azure-sub-2, use la `azure account set` comando.

        azure account set Azure-sub-2


## <a name="create-an-azure-data-lake-store-account"></a>Crear una cuenta de la tienda de lago de datos de Azure

Abra un símbolo del sistema, shell o una sesión de terminal y ejecute los comandos siguientes.

2. Cambie al modo de administrador de recursos de Azure mediante el siguiente comando:

        azure config mode arm


5. Crear un nuevo grupo de recursos. En el siguiente comando, proporcione los valores de parámetro que desee usar.

        azure group create <resourceGroup> <location>

    Si el nombre contiene espacios, coloque entre comillas. Por ejemplo "oriental US 2".

5. Crear la cuenta de almacén de datos lago.

        azure datalake store account create <dataLakeStoreAccountName> <location> <resourceGroup>

## <a name="create-folders-in-your-data-lake-store"></a>Crear carpetas en el almacén de datos de lago

Puede crear carpetas en su cuenta de lago almacén de datos de Azure para administrar y almacenar datos. Use el comando siguiente para crear una carpeta denominada "MiNuevaCarpeta" en la raíz de la tienda de lago de datos.

    azure datalake store filesystem create <dataLakeStoreAccountName> <path> --folder

Por ejemplo:

    azure datalake store filesystem create mynewdatalakestore /mynewfolder --folder

## <a name="upload-data-to-your-data-lake-store"></a>Cargar datos en el almacén de datos de lago

Puede cargar los datos al almacén de datos lago directamente en el nivel raíz o a una carpeta que ha creado dentro de la cuenta. Los fragmentos de código siguientes muestran cómo cargar algunos datos de ejemplo en la carpeta (**MiNuevaCarpeta**) que creó en la sección anterior.

Si está buscando algunos datos de ejemplo cargar, puede obtener la carpeta **Ambulancia datos** del [Repositorio de Azure datos lago Git](https://github.com/MicrosoftBigData/usql/tree/master/Examples/Samples/Data/AmbulanceData). Descargar el archivo y guardarlo en un directorio local en el equipo, como C:\sampledata\.

    azure datalake store filesystem import <dataLakeStoreAccountName> "<source path>" "<destination path>"

Por ejemplo:

    azure datalake store filesystem import mynewdatalakestore "C:\SampleData\AmbulanceData\vehicle1_09142014.csv" "/mynewfolder/vehicle1_09142014.csv"


## <a name="list-files-in-data-lake-store"></a>Lista de archivos en el almacén de datos de lago

Use el comando siguiente para enumerar los archivos en una cuenta de almacén de datos lago.

    azure datalake store filesystem list <dataLakeStoreAccountName> <path>

Por ejemplo:

    azure datalake store filesystem list mynewdatalakestore /mynewfolder

El resultado de este debe ser similar al siguiente:

    info:    Executing command datalake store filesystem list
    data:    accessTime: 1446245025257
    data:    blockSize: 268435456
    data:    group: NotSupportYet
    data:    length: 1589881
    data:    modificationTime: 1446245105763
    data:    owner: NotSupportYet
    data:    pathSuffix: vehicle1_09142014.csv
    data:    permission: 777
    data:    replication: 0
    data:    type: FILE
    data:    ------------------------------------------------------------------------------------
    info:    datalake store filesystem list command OK

## <a name="rename-download-and-delete-data-from-your-data-lake-store"></a>Cambiar el nombre, descargar y eliminar los datos del almacén de datos lago

* **Para cambiar el nombre de un archivo**, utilice el siguiente comando:

        azure datalake store filesystem move <dataLakeStoreAccountName> <path/old_file_name> <path/new_file_name>

    Por ejemplo:

        azure datalake store filesystem move mynewdatalakestore /mynewfolder/vehicle1_09142014.csv /mynewfolder/vehicle1_09142014_copy.csv

* **Para descargar un archivo**, utilice el siguiente comando. Asegúrese de que la ruta de destino que especifique ya existe.

        azure datalake store filesystem export <dataLakeStoreAccountName> <source_path> <destination_path>

    Por ejemplo:

        azure datalake store filesystem export mynewdatalakestore /mynewfolder/vehicle1_09142014_copy.csv "C:\mysampledata\vehicle1_09142014_copy.csv"

* **Para eliminar un archivo**, utilice el siguiente comando:

        azure datalake store filesystem delete <dataLakeStoreAccountName> <path>

    Por ejemplo:

        azure datalake store filesystem delete mynewdatalakestore /mynewfolder/vehicle1_09142014_copy.csv

    Cuando se le solicite, escriba **Y** para eliminar el elemento.

## <a name="view-the-access-control-list-for-a-folder-in-data-lake-store"></a>Ver la lista de control de acceso para una carpeta en el almacén de datos de lago

Use el comando siguiente para ver las ACL en una carpeta de almacén de datos lago. En la versión actual, se pueden establecer ACL únicamente en la raíz de la tienda de lago de datos. Por lo tanto, el parámetro de ruta de acceso más adelante siempre será root (/).

    azure datalake store permissions show <dataLakeStoreName> <path>

Por ejemplo:

    azure datalake store permissions show mynewdatalakestore /


## <a name="delete-your-data-lake-store-account"></a>Eliminar la cuenta de almacén de datos lago

Use el comando siguiente para eliminar una cuenta de almacén de datos lago.

    azure datalake store account delete <dataLakeStoreAccountName>

Por ejemplo:

    azure datalake store account delete mynewdatalakestore

Cuando se le solicite, escriba **Y** para eliminar la cuenta.


## <a name="next-steps"></a>Pasos siguientes

- [Proteger los datos en el almacén de datos de lago](data-lake-store-secure-data.md)
- [Usar el análisis de datos de Azure lago con el almacén de datos de lago](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
- [Usar Azure HDInsight con el almacén de datos de lago](data-lake-store-hdinsight-hadoop-use-portal.md)


[azure-command-line-tools]: ../xplat-cli-install.md
