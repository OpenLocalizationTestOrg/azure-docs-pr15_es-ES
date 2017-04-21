<properties
   pageTitle="Empezar a trabajar con el almacén de datos de lago | Azure"
   description="Usar PowerShell de Azure para crear una cuenta de almacén de datos lago y realizar operaciones básicas"
   services="data-lake-store"
   documentationCenter=""
   authors="nitinme"
   manager="jhubbard"
   editor="cgronlun"/>

<tags
   ms.service="data-lake-store"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="10/04/2016"
   ms.author="nitinme"/>

# <a name="get-started-with-azure-data-lake-store-using-azure-powershell"></a>Introducción a Azure datos lago tienda con PowerShell de Azure

> [AZURE.SELECTOR]
- [Portal](data-lake-store-get-started-portal.md)
- [PowerShell](data-lake-store-get-started-powershell.md)
- [.NET SDK](data-lake-store-get-started-net-sdk.md)
- [Java SDK](data-lake-store-get-started-java-sdk.md)
- [API DE REST](data-lake-store-get-started-rest-api.md)
- [CLI de Azure](data-lake-store-get-started-cli.md)
- [Node.js](data-lake-store-manage-use-nodejs.md)

Obtenga información sobre cómo usar PowerShell Azure para crear una cuenta de la tienda de lago de datos de Azure y realizar operaciones básicas, como crear carpetas, cargar y descargar archivos de datos, eliminar la cuenta, etcetera. Para obtener más información acerca de la tienda de lago de datos, vea [Información general de la tienda de lago datos](data-lake-store-overview.md).

## <a name="prerequisites"></a>Requisitos previos

Antes de comenzar este tutorial, debe tener el siguiente:

* **Suscripción de un Azure**. Vea la [versión de prueba gratuita de Azure obtener](https://azure.microsoft.com/pricing/free-trial/).

* **Azure PowerShell 1.0 o mayor**. Vea [cómo instalar y configurar Azure PowerShell](../powershell-install-configure.md).

## <a name="authentication"></a>Autenticación

En este artículo, se usa un enfoque más sencillo de autenticación con almacén de lago de datos donde se le pide que introduzca sus credenciales de cuenta de Azure. El nivel de acceso a datos lago Store cuenta y sistema de archivos, a continuación, se rige por el nivel de acceso del usuario que ha iniciado sesión. Sin embargo, hay otros enfoques también para autenticar con datos lago almacén, que son la **autenticación de usuario final** o **servicio de servicio**. Para obtener instrucciones y más información acerca de cómo autenticar, vea [autenticar con el almacén de datos de lago con Azure Active Directory](data-lake-store-authenticate-using-active-directory.md).

## <a name="create-an-azure-data-lake-store-account"></a>Crear una cuenta de la tienda de lago de datos de Azure

1. Desde el escritorio, abra una nueva ventana de Windows PowerShell y escriba el fragmento de código siguiente para iniciar sesión en su cuenta de Azure, establezca la suscripción y registrar el proveedor de almacén de datos lago. Cuando se solicita que inicie sesión, asegúrese de que inicie sesión como uno de lo admininistrators o propietario de la suscripción:

        # Log in to your Azure account
        Login-AzureRmAccount

        # List all the subscriptions associated to your account
        Get-AzureRmSubscription

        # Select a subscription
        Set-AzureRmContext -SubscriptionId <subscription ID>

        # Register for Azure Data Lake Store
        Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.DataLakeStore"


2. Una cuenta de Azure datos lago almacén está asociada con un grupo de recursos de Azure. Empiece por crear un grupo de recursos de Azure.

        $resourceGroupName = "<your new resource group name>"
        New-AzureRmResourceGroup -Name $resourceGroupName -Location "East US 2"

    ![Crear un grupo de recursos de Azure] (./media/data-lake-store-get-started-powershell/ADL.PS.CreateResourceGroup.png "Crear un grupo de recursos de Azure")

2. Cree una cuenta de la tienda de lago de datos de Azure. El nombre que especifique solo debe contener letras minúsculas y números.

        $dataLakeStoreName = "<your new Data Lake Store name>"
        New-AzureRmDataLakeStoreAccount -ResourceGroupName $resourceGroupName -Name $dataLakeStoreName -Location "East US 2"

    ![Crear una cuenta de lago almacén de datos de Azure] (./media/data-lake-store-get-started-powershell/ADL.PS.CreateADLAcc.png "Crear una cuenta de lago almacén de datos de Azure")

3. Compruebe que la cuenta se ha creado correctamente.

        Test-AzureRmDataLakeStoreAccount -Name $dataLakeStoreName

    El resultado de esta debe ser **Verdadero**.

## <a name="create-directory-structures-in-your-azure-data-lake-store"></a>Crear estructuras de directorios en la tienda de lago de datos de Azure

Puede crear directorios en su cuenta de lago almacén de datos de Azure para administrar y almacenar datos.

1. Especifique un directorio raíz.

        $myrootdir = "/"

2. Crear un nuevo directorio denominado **mynewdirectory** en la raíz especificada.

        New-AzureRmDataLakeStoreItem -Folder -AccountName $dataLakeStoreName -Path $myrootdir/mynewdirectory

3. Compruebe que se ha creado correctamente el nuevo directorio.

        Get-AzureRmDataLakeStoreChildItem -AccountName $dataLakeStoreName -Path $myrootdir

    Debe mostrar un resultado similar al siguiente:

    ![Compruebe el directorio] (./media/data-lake-store-get-started-powershell/ADL.PS.Verify.Dir.Creation.png "Compruebe el directorio")


## <a name="upload-data-to-your-azure-data-lake-store"></a>Cargar datos en el almacén de lago de datos de Azure

Puede cargar los datos al almacén de datos lago directamente en el nivel raíz, o en un directorio que ha creado dentro de la cuenta. Los fragmentos de código siguientes muestran cómo cargar algunos datos de ejemplo en el directorio (**mynewdirectory**) que creó en la sección anterior.

Si está buscando algunos datos de ejemplo cargar, puede obtener la carpeta **Ambulancia datos** del [Repositorio de Azure datos lago Git](https://github.com/MicrosoftBigData/usql/tree/master/Examples/Samples/Data/AmbulanceData). Descargar el archivo y guardarlo en un directorio local en el equipo, como C:\sampledata\.

    Import-AzureRmDataLakeStoreItem -AccountName $dataLakeStoreName -Path "C:\sampledata\vehicle1_09142014.csv" -Destination $myrootdir\mynewdirectory\vehicle1_09142014.csv


## <a name="rename-download-and-delete-data-from-your-data-lake-store"></a>Cambiar el nombre, descargar y eliminar los datos del almacén de datos lago

Para cambiar el nombre de un archivo, use el siguiente comando:

    Move-AzureRmDataLakeStoreItem -AccountName $dataLakeStoreName -Path $myrootdir\mynewdirectory\vehicle1_09142014.csv -Destination $myrootdir\mynewdirectory\vehicle1_09142014_Copy.csv

Para descargar un archivo, use el siguiente comando:

    Export-AzureRmDataLakeStoreItem -AccountName $dataLakeStoreName -Path $myrootdir\mynewdirectory\vehicle1_09142014_Copy.csv -Destination "C:\sampledata\vehicle1_09142014_Copy.csv"

Para eliminar un archivo, use el siguiente comando:

    Remove-AzureRmDataLakeStoreItem -AccountName $dataLakeStoreName -Paths $myrootdir\mynewdirectory\vehicle1_09142014_Copy.csv

Cuando se le solicite, escriba **Y** para eliminar el elemento. Si tiene más de un archivo que desea eliminar, puede proporcionar todas las rutas de acceso separadas por punto y coma.

    Remove-AzureRmDataLakeStoreItem -AccountName $dataLakeStoreName -Paths $myrootdir\mynewdirectory\vehicle1_09142014.csv, $myrootdir\mynewdirectoryvehicle1_09142014_Copy.csv

## <a name="delete-your-azure-data-lake-store-account"></a>Eliminar la cuenta de la tienda de lago de datos de Azure

Use el comando siguiente para eliminar la cuenta de almacén de datos lago.

    Remove-AzureRmDataLakeStoreAccount -Name $dataLakeStoreName

Cuando se le solicite, escriba **Y** para eliminar la cuenta.


## <a name="next-steps"></a>Pasos siguientes

- [Proteger los datos en el almacén de datos de lago](data-lake-store-secure-data.md)
- [Usar el análisis de datos de Azure lago con el almacén de datos de lago](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
- [Usar Azure HDInsight con el almacén de datos de lago](data-lake-store-hdinsight-hadoop-use-portal.md)
